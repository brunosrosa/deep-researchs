# Dossier Técnico: Arquitetura Bare-Metal SODA - Motor de Inferência Poliglota Assíncrono

A evolução das infraestruturas de inferência para modelos de linguagem de grande porte (LLMs) em meados de 2026 impõe severas restrições técnicas a ambientes bare-metal com recursos físicos delimitados. A execução eficiente de pipelines cognitivos em um nó composto por um processador Intel i9, 32 GB de memória RAM e uma unidade de processamento gráfico discreta NVIDIA RTX 2060m com 6 GB de VRAM exige um projeto de engenharia estruturado no nível da gestão de memória virtual, da orquestração assíncrona de processos e da compilação de código nativo. O principal gargalo mecânico em arquiteturas locais deixou de ser apenas a taxa de transferência dos pesos do modelo para ser dominado pela expansão da memória do cache de Chaves e Valores (_Key-Value Cache_ ou KV Cache) em janelas de contexto longas, além da incompatibilidade de representação nos grafos de execução entre arquiteturas orientadas a Atenção e arquiteturas baseadas em Espaço de Estados (_State Space Models_ ou SSMs).

Com o objetivo de fundamentar a transição do motor de inferência do projeto SODA (_Sovereign Operating Data Architecture_) para um sistema poliglota assíncrono construído em Rust sobre o runtime Tokio, este dossier analisa os mecanismos de compressão de KV Cache de última geração, a convivência segura entre ambientes heterogêneos como `bitnet.cpp` e `llama.cpp`, e a orquestração de modelos exóticos sem contaminação de FFI ou vazamentos de VRAM.

## 1. O Estado da Arte do TurboQuant e Esparsificação Dinâmica de KV Cache

### Mecanismo Algorítmico do TurboQuant e Políticas de Compressão Assimétrica

O algoritmo TurboQuant estabeleceu um marco na compressão de KV Cache ao demonstrar que a ocupação de memória do cache pode ser reduzida em até $6\times$ sem necessidade de ajuste fino (_fine-tuning_) ou retreinamento do modelo. O núcleo matemático do TurboQuant opera por meio de uma pipeline de quantização em dois estágios complementares:

1. **Quantização Polar (PolarQuant):** Aplica uma rotação estruturada aos vetores de ativação por meio da Transformada de Walsh-Hadamard, seguida por uma quantização em um livro de códigos (_codebook_) polar fixo de 3 bits. Essa rotação descorrelaciona as dimensões das chaves e valores, neutralizando a sensibilidade a valores discrepantes (_outliers_) e minimizando o Erro Quadrático Médio (MSE).
2. **Correção Residual por Sinal (QJL):** Utiliza uma projeção de Johnson-Lindenstrauss Quantizada de 1 bit para codificar o resíduo da quantização MSE. Esse procedimento assegura que o produto interno entre as consultas (_Queries_) e as chaves (_Keys_) permaneça não-viesado (_unbiased_), preservando a precisão da distribuição Softmax mesmo em contextos de grande extensão.

Na prática de implantação em bifurcações do `llama.cpp` (como as mantidas no projeto `TheTom/llama-cpp-turboquant` e na biblioteca `turboquant_plus`), a aplicação de esquemas simétricos de quantização sobre Chaves e Valores provou ser subótima. As Chaves apresentam extrema sensibilidade a erros de quantização por determinarem a distribuição probabilística da atenção, ao passo que os Valores toleram compressões substancialmente mais agressivas sem comprometer a fidelidade do texto gerado. O ecossistema padronizou políticas de compressão assimétrica que otimizam a ocupação de VRAM na GPU RTX 2060m, conforme detalhado na tabela a seguir:

|**Configuração de Cache (K / V)**|**Rácio de Compressão**|**Impacto em Perplexidade (PPL)**|**Desempenho de Decode (Tokens/s)**|**Viabilidade em Hardware (RTX 2060m - 6GB)**|
|---|---|---|---|---|
|**FP16 / FP16 (Baseline)**|$1.0\times$|$0.0\%$ (Referência)|$1.00\times$|Inviável para contextos superiores a $8\text{k}$ tokens.|
|**Q8_0 / Turbo4**|$\approx 2.5\times$|Insignificante ($< 0.1\%$)|$0.98\times - 1.02\times$|Indicado para validação inicial de modelos sensíveis.|
|**Q8_0 / Turbo3 (Padrão SODA)**|$\approx 4.0\times$|Negligenciável ($< 0.5\%$)|$0.95\times - 0.99\times$|**Ponto ideal:** Habilita janelas de $32\text{k}$ tokens em modelos de 8B.|
|**Q8_0 / Turbo2 (Boundary V)**|$\approx 5.8\times$|Baixo ($< 1.8\%$) em dense|$0.85\times - 0.92\times$|Exige kernels fundidos; adequado para extensões extremas ($32\text{k}+$).|
|**Turbo2 / Turbo2 (Simétrico)**|$\approx 6.2\times$|Severo ($> 15\%$) em raciocínio|$0.70\times - 0.80\times$|**Inadequado:** Degrada drasticamente o cálculo da Softmax.|

A política denominada _Boundary V_ atua de forma adaptativa protegendo as camadas do modelo identificadas como críticas (como as camadas iniciais, finais e os limites de especialistas em arquiteturas MoE), aplicando a quantização agressiva `turbo2` apenas nas camadas intermediárias.

### Metodologias "Heavy Hitters" e Poda de Cache em Tempo de Execução

Além da quantização de vetores, a esparsificação do KV Cache é estendida pela integração de algoritmos de poda baseados na retenção de tokens de alto impacto (_Heavy Hitters_), representados por estruturas como $H_2O$, PyramidKV e TriAttention. O algoritmo TriAttention executa a pontuação e a poda de tokens diretamente na GPU durante a fase de pré-preenchimento (_prefill_). Em vez de armazenar a totalidade da sequência, a engine preserva um orçamento fixo (_budget_) de tokens contendo a janela recente e as posições de atenção dominante.

Em termos de desempenho mecânico na RTX 2060m, a execução da poda via TriAttention reduz o tempo de avaliação do cache de aproximadamente $5900\text{ ms}$ (quando calculado na CPU) para $4-9\text{ ms}$ por evento de poda acelerado por CUDA, resultando em um ganho de até $4.3\times$ no _throughput_ global de geração de tokens. Por sua vez, o PyramidKV ajusta a alocação de memória em formato piramidal ao longo da profundidade da rede: camadas inferiores retêm janelas de contexto locais menores, enquanto as camadas superiores concentram a atenção global, sustentando pontuações perfeitas em testes de recuperação em contextos extensos mesmo com alocações reduzidas a 128 entradas por camada.

### Desafios de Compilação C-FFI, Instabilidade de ABI e o Ecossistema Rust Nativo

A vinculação dessas implementações avançadas de C/C++ a uma aplicação Rust assíncrona enfrenta barreiras de estabilidade de ABI (_Application Binary Interface_):

- **Incompatibilidade de Headers e Modificação de Tipos:** Projetos como `TheTom/llama-cpp-turboquant` estendem a enumeração de tipos do GGML adicionando identificadores customizados (`GGML_TYPE_TQ1`, `GGML_TYPE_TQ2`) e alteram estruturas internas como `llama_context_params`. _Crates_ Rust padrão (como `llama-cpp-2` ou bindings automáticos via `bindgen`) falham na compilação ou induzem desalinhamento de memória (_undefined behavior_) ao interagir com bibliotecas modificadas.
- **Custo de Desquantização no Kernel de Decode:** O processo de desquantização do TurboQuant exige a conversão das chaves e valores compactados de volta para a precisão dos acumuladores da GPU (FP16 ou BF16) a cada token gerado. Caso os kernels CUDA de desquantização não estejam perfeitamente fundidos com as rotinas de atenção, a latência de decodificação sofre degradação acentuada devido ao gargalo na largura de banda do barramento de memória da RTX 2060m.
- **Estado das Bibliotecas Rust Nativas:** Atualmente, não existe uma _crate_ puramente Rust que ofereça kernels fundidos do TurboQuant sem depender de código C/C++ subjacente ou de invocações do ecossistema GGML. A plataforma `mistral.rs` (desenvolvida sobre o framework `candle`) representa a alternativa mais madura em Rust puro, provendo suporte nativo a PagedAttention, quantização _in situ_ (ISQ) e gerenciamento avançado de memória, embora a integração de livros de código polares ainda dependa de extensões em C++/CUDA.

## 2. O Ecossistema BitNet (1.58-bit) e Estratégias de Integração Daemon

### Incompatibilidade de Linkagem Estática e Colisão de Símbolos C-FFI

A tentativa de compilar e vincular estaticamente o framework `bitnet.cpp` da Microsoft juntamente com a biblioteca `llama.cpp` padrão no mesmo executável Rust resulta em falhas críticas de compilação e execução.

A engine `bitnet.cpp` utiliza uma versão modificada e fixada de submódulos do `llama.cpp` (como `isHuangXin/llama.cpp`), criando divergências estruturais com as ramificações principais do ecossistema. A inclusão estática de ambos os projetos em um único binário Rust provoca três problemas fundamentais:

1. **Colisão de Símbolos Globais em C:** Ambas as bases de código exportam rotinas externas com nomes idênticos, como `ggml_init`, `ggml_cuda_init` e `ggml_graph_compute_with_ctx`. A presença de símbolos duplicados no processo de linkagem impede a resolução determinística de chamadas pela FFI.
2. **Divergência nos Formatos de Tensor:** O `bitnet.cpp` introduziu representações ternárias nativas (`I2_S`, `TL1`) projetadas para otimização em instruções SIMD de CPU. A tentativa de carregar arquivos GGUF contendo esses tensores em instâncias não modificadas do `llama.cpp` resulta no abortamento do processo durante o parse dos metadados.
3. **Bugs de Grafo e Perplexidade:** O `bitnet.cpp` aplica modificações específicas nas funções de ativação das subcamadas FFN (substituindo a função SiLU por ReLU ao quadrado, $relu(x)^2$). Se o grafo for executado por um runtime incorreto sem essa adaptação, o modelo não apresenta falhas de execução imediatas, mas sua perplexidade se deteriora de $17.1$ para $99.8$, invalidando os resultados gerados.

Essas limitações tornam a linkagem estática inviável para ambientes de produção, exigindo uma abordagem baseada em isolamento de processos.

### Arquitetura de Isolamento por IPC e Processamento Zero-Copy em Memória Compartilhada

A arquitetura recomendada para a integração do `bitnet.cpp` no projeto SODA baseia-se na execução da engine como um subprocesso dedicado, gerenciado pelo Daemon Supervisor em Rust. A comunicação entre o processo principal e o trabalhador `bitnet.cpp` é realizada por Inter-Process Communication (IPC) com suporte a Memória Compartilhada (_Shared Memory_ - SHM) via arquivos mapeados em memória (`shm_open`, `mmap`) ou por crates especializadas como `mmap-sync` e `iceoryx2`.

Nessa topologia, o processamento ocorre sem a necessidade de copiar dados entre o espaço de usuário e o espaço de núcleo do sistema operacional. O pipeline é estruturado por meio de três componentes operacionais:

- **Buffers Circulares em Memória Compartilhada:** O Daemon Rust grava as sequências de tokens de entrada em blocos de memória compartilhada e notifica o trabalhador `bitnet.cpp` por meio de sinalização leve via _Unix Domain Sockets_ ou primitivas de sincronização do sistema operacional.
- **Processamento Paralelo em CPU:** O subprocesso `bitnet.cpp` consome as entradas e executa as rotinas matemáticas nos núcleos de alta performance do processador Intel i9, aproveitando instruções AVX-2 e AVX-512. Modelos ternários de 1.58 bits (como o `BitNet b1.58 2B4T`) atingem velocidades de 5 a 7 tokens por segundo em execução puramente baseada em CPU, mantendo a GPU RTX 2060m totalmente livre para outras tarefas.
- **Garantia de Thread Safety no Runtime Tokio:** Estruturas de IPC de baixíssima latência (como as presentes na `iceoryx2`) utilizam referências não contíguas que evitam operações atômicas custosas, tornando inviável a implementação da _trait_ `Send` necessária para a migração de tarefas entre threads do Tokio. Para evitar falhas de compilação resultantes do escalonador por roubo de trabalho (_work-stealing_), a execução das tarefas de IPC deve ser confinada a uma thread fixa do sistema operacional por meio das primitivas `tokio::task::LocalSet` e `tokio::task::spawn_local`.

## 3. Arquiteturas Exóticas: State Space Models (Mamba, Zamba2) e Motores Locais

### Divergência Matemática e Topológica entre SSMs e Transformers

Os modelos baseados em Espaço de Estados (SSMs), abrangendo arquiteturas como Mamba-1, Mamba-2 e modelos híbridos como o Zamba2 (que intercala blocos SSM com camadas de Atenção de Múltipla Consulta - MQA), possuem uma dinâmica de execução distinta dos Transformers tradicionais.

Enquanto os Transformers exigem a manutenção de um KV Cache cuja complexidade espacial cresce quadraticamente $O(N^2)$ em função do comprimento da sequência, os modelos SSM comprimem o histórico de contexto em um estado oculto recorrente de dimensão fixa $h_t \in \mathbb{R}^d$, cuja evolução é regida pelas equações discretizadas:

$$h_t = A h_{t-1} + B x_t$$

$$y_t = C h_t$$

Essa formulação altera os requisitos de hardware do nó bare-metal:

1. **Ocupação de Memória Constante $O(1)$ durante a Inferência:** O volume de memória necessário para sustentar o estado do Mamba não se expande com o aumento da janela de texto. Uma sequência de $100\text{k}$ tokens consome a mesma quantidade de VRAM para o estado oculto que uma sequência de 10 tokens.
2. **Incompatibilidade com Alocadores de Grafo Estático:** O motor do `llama.cpp` foi projetado para construir grafos de execução orientados ao processamento paralelo de matrizes de atenção. A adaptação de operadores recorrentes do Mamba nesses grafos resultou historicamente em problemas de fragmentação de memória e suporte instável.

### Avaliação de Desempenho dos Motores Rust Nativa: `mistral.rs` versus `candle`

Para garantir a execução estável de modelos híbridos exóticos no projeto SODA, avaliou-se as principais engines desenvolvidas no ecossistema Rust:

|**Critério de Avaliação**|**Engine mistral.rs**|**Framework candle (Nativo)**|
|---|---|---|
|**Abstração do Grafo**|Engine completa construída sobre o Candle.|Framework de tensores de baixo nível.|
|**Suporte a SSM / Mamba**|Suporte maduro (Mamba-1, Mamba-2, Zamba2).|Suporte via primitivas manuais de tensores.|
|**Gestão de Cache**|PagedAttention e Quantização ISQ integradas.|Exige implementação manual de alocadores.|
|**Vazamento de VRAM**|Zero VRAM Leak via semântica RAII Rust.|Requer descarte manual e rigoroso de Tensores.|
|**Alternância de Modelos**|Suporte nativo a _hot-swap_ de arquiteturas.|Requer recriação de estruturas de controle.|

A plataforma **`mistral.rs`** consolida-se como a engine recomendada para modelos exóticos no ecossistema Rust. Construída de forma nativa, ela abstrai a complexidade do `candle` e disponibiliza suporte pronto para PagedAttention, quantização _in situ_ (ISQ) e execução otimizada de topologias híbridas como o Zamba2 sem necessidade de dependências externas em C++.

### Prevenção de Vazamentos de VRAM e Gestão Determinística de Recursos

O aparecimento de vazamentos de VRAM durante a descarga e alternância de modelos representa uma falha recorrente em sistemas baseados em FFI para C/C++. Quando um modelo é desalocado via chamadas de API como `llama_free_model()`, o driver de GPU da NVIDIA pode reter o contexto CUDA e os buffers mapeados na memória virtual enquanto a thread de origem permanecer ativa no pool de threads do Tokio.

Em contrapartida, soluções desenvolvidas nativamente em Rust (como o `mistral.rs`) utilizam o padrão RAII (_Resource Acquisition Is Initialization_), no qual a _trait_ `Drop` é invocada automaticamente assim que as estruturas dos tensores saem de escopo, liberando os ponteiros da GPU. Contudo, devido ao comportamento do driver proprietário da NVIDIA, uma parcela residual de memória virtual de contexto ($\sim 300\text{ MB} - 500\text{ MB}$) permanece vinculada ao identificador do processo no sistema operacional até a sua finalização.

Para assegurar **Zero-VRAM Leak** absoluto na infraestrutura SODA, a arquitetura adota a estratégia de **Trabalhadores Descartáveis por Processo**: a desalocação completa dos recursos da GPU é obtida forçando o encerramento imediato do processo trabalhador pelo Supervisor Rust, o que obriga o driver da GPU a recuperar $100\%$ da VRAM e da RAM alocadas para aquele processo.

## 4. Estrutura Arquitetural do Roteador Poliglota SODA

### Topologia do Sistema e Organização dos Motores

A arquitetura do projeto SODA é estruturada sob o padrão de **Supervisor-Worker Mesh**, no qual o núcleo assíncrono em Rust atua exclusivamente como roteador de requisições, gerenciador de estado e orquestrador de memória, delegando o processamento matemático a processos filhos especializados e isolados.

As requisições recebidas pela interface HTTP/gRPC do Daemon Rust (desenvolvida com a _crate_ Axum) são analisadas pelo Roteador de Estado. O Roteador consulta o estado de ocupação do hardware e encaminha a carga de trabalho para um dos três trabalhadores independentes:

- **Trabalhador `llama.cpp` (Subprocesso C++):** Executa modelos densos em formato GGUF otimizados com a suíte TurboQuant e TriAttention, operando com acesso direto à GPU NVIDIA RTX 2060m.
- **Trabalhador `bitnet.cpp` (Subprocesso C++):** Executa modelos ternários de 1.58 bits (como BitNet 2B4T) utilizando instruções SIMD na CPU Intel i9, comunicando-se com o Supervisor via memória compartilhada Zero-Copy.
- **Trabalhador `mistral.rs` (Processo Rust Nativo):** Executa modelos híbridos SSM (Zamba2, Mamba-2) e modelos de visão, utilizando alocação nativa via `candle` e gerenciamento RAII de memória.

### Mapeamento Físico de Recursos e Motores de Inferência

A distribuição do carregamento de modelos e a alocação de hardware para a infraestrutura do projeto SODA são especificadas na tabela a seguir:

|**Categoria de Modelo**|**Motor de Inferência**|**Dispositivo Alocado**|**Formato / Quantização**|**Consumo de VRAM**|**Consumo de RAM**|
|---|---|---|---|---|---|
|**LLM Denso (7B - 8B)**|`llama.cpp` (Fork TheTom)|NVIDIA RTX 2060m|GGUF Q4_K_M + KV Turbo3|$4.8\text{ GB}$|$2.0\text{ GB}$|
|**LLM Ternário (1.58b)**|`bitnet.cpp` (Subprocesso)|CPU Intel i9 (AVX-512)|GGUF I2_S / TL1|$0.0\text{ GB}$|$4.5\text{ GB}$|
|**SSM Híbrido (Zamba2/Mamba)**|`mistral.rs` (Rust Nativo)|NVIDIA RTX 2060m / CPU|Safetensors / ISQ (Q4_0)|$3.2\text{ GB}$|$4.0\text{ GB}$|
|**Visão / Multimodal**|`mistral.rs` (Rust Nativo)|NVIDIA RTX 2060m|FP16 / BF16|$2.5\text{ GB}$|$1.5\text{ GB}$|

### Algoritmo de Alternância Dinâmica de Motores (_Hot-Swapping_)

Para realizar a troca entre diferentes motores de inferência em milissegundos sem arriscar pânicos (_panics_) no runtime Tokio, o Roteador Poliglota executa o seguinte procedimento operacional:

1. **Recepção e Classificação da Requisição:** O servidor Axum intercepta a requisição do usuário e identifica o tipo de arquitetura exigido para a tarefa (modelo denso com TurboQuant, modelo SSM Mamba2 ou modelo ternário BitNet).
2. **Auditoria de Ocupação da GPU:** O Supervisor verifica a disponibilidade atual de VRAM por meio de chamadas à biblioteca `nvml-wrapper`. Se o motor requisitado for diferente do motor que se encontra ativo na GPU, o Supervisor inicia o protocolo de transição de contexto.
3. **Desativação do Processo Ativo:** O Supervisor envia um sinal `SIGTERM` para o processo trabalhador que ocupa a GPU. O processo encerra suas threads e finaliza sua execução. A terminação do processo garante que o sistema operacional e o driver NVIDIA recuperem atomicamente $100\%$ das páginas físicas de VRAM e RAM associadas.
4. **Carregamento Otimizado via Mapeamento de Memória (`mmap` Warm-Up):** Os arquivos de pesos dos modelos (GGUF ou Safetensors) são mantidos pré-carregados no _page cache_ do sistema operacional utilizando mapeamento de memória. Isso permite que o novo trabalhador instanciado via `tokio::process::Command` acesse os pesos a partir da RAM em tempo reduzido ($< 50\text{ ms}$), realizando a transferência dos tensores para a VRAM da RTX 2060m na velocidade limite do barramento PCIe.
5. **Reativação do Canal de Comunicação IPC:** O Supervisor estabelece o canal de comunicação por memória compartilhada com o novo trabalhador, utilizando primitivas `tokio::task::LocalSet` para garantir que o manuseio dos buffers ocorra de forma segura, restaurando o serviço de inferência sem contaminação entre motores.

## 5. Conclusões e Recomendações Técnicas

1. **Adoção do Esquema Assimétrico no TurboQuant:** Padronizar o uso do esquema `Q8_0` para Chaves e `Turbo3` (ou `Turbo2` com proteção _Boundary V_) para Valores através da bifurcação `TheTom/llama-cpp-turboquant`. Essa configuração reduz a ocupação do KV Cache em até $4.6\times$, viabilizando janelas de contexto de $32\text{k}$ tokens em modelos de 8B na GPU RTX 2060m de 6 GB.
2. **Isolamento Completo do `bitnet.cpp` por IPC:** Evitar a compilação estática conjunta do `bitnet.cpp` com o `llama.cpp` para prevenir colisões irrecuperáveis de símbolos globais em C e divergências na execução dos grafos FFN. O `bitnet.cpp` deve rodar como um subprocesso dedicado focado em execução via CPU no Intel i9, comunicando-se com o Supervisor Rust por memória compartilhada Zero-Copy via `iceoryx2` ou `mmap-sync`.
3. **Utilização do `mistral.rs` para Arquiteturas Exóticas:** Utilizar a engine `mistral.rs` para modelos baseados em Espaço de Estados (SSMs/Mamba) e arquiteturas híbridas como Zamba2. A ferramenta elimina as ineficiências de alocação de memória presentes em motores baseados em grafos estáticos de atenção e assegura gerenciamento de memória orientado ao padrão RAII.
4. **Gerenciamento de Recursos Baseado em Ciclo de Vida de Processos:** Empregar a criação e finalização de processos trabalhadores isolados como mecânica principal para a alternância de modelos na GPU, garantindo a liberação completa do contexto CUDA sem ocorrência de vazamentos de VRAM ou falhas de tempo de execução no Tokio.