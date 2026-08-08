# Arquitetura de Inferência em Tempo de Teste e Reparo Cognitivo para Small Language Models na Borda

Small Language Models (SLMs) na faixa de 3B a 4B parâmetros operando sob quantização agressiva (como Q4_K_M ou IQ3_M) enfrentam uma degradação cognitiva acentuada em tarefas de raciocínio lógico complexo e estruturação de agentes. Em cenários de borda com restrição severa de VRAM (como a GPU NVIDIA RTX 2060 Mobile de 6GB) e gargalos de largura de banda na interface PCIe, o transbordo (_spillover_) para a memória do sistema inviabiliza a execução de modelos maiores (7B+).

Para superar esse teto de desempenho sem alterar o _footprint_ estático do modelo, a arquitetura do sistema operacional agêntico deve deslocar a complexidade computacional para a fase de inferência. A aplicação de técnicas avançadas de _Test-Time Compute_ (TTC), reparo sintático nativo em tempo real (_Response Healing_) e engenharia de representação (_Activation Steering_) permite recuperar a capacidade cognitiva perdida durante os processos de quantização e destilação.

## Test-Time Compute e Loops de Reflexão Local em Small Language Models

A evolução da inferência local em motores como `llama.cpp`, `mistral.rs` e `vLLM` incorporou o paradigma de _Test-Time Compute_ (TTC). Essa abordagem redistribui a flutuabilidade computacional do treinamento para a execução, permitindo que modelos menores atinjam taxas de acerto comparáveis a modelos de maior escala por meio da alocação dinâmica de tempo de processamento.

### Escalonamento Paralelo versus Escalonamento Sequencial na Borda

O escalonamento em tempo de teste pode ocorrer por amostragem paralela ou por iteração sequencial. Métodos paralelos, como _Best-of-N_, _Self-Consistency_ e _Majority Voting_, geram múltiplos caminhos de resposta simultâneos e selecionam a melhor convergência. Em arquiteturas móbise e aceleradores NPU/GPU, a amostragem paralela explora unidades de multiplicação de matrizes subutilizadas durante a fase de decodificação. Contudo, em GPUs de borda com VRAM limitada a 6GB, a geração paralela de múltiplas sequências multiplica o consumo da memória do _KV-Cache_, provocando contenção acelerada do espaço alocado e estresse na largura de banda da VRAM.

Em sistemas agênticos locais, a estratégia mais viável é o _Test-Time Compute_ sequencial fundamentado em loops de reflexão, a exemplo do padrão Ralph Loop. Nesse fluxo, o SLM gera uma cadeia de raciocínio inicial que é submetida a um ciclo de validação executiva antes do retorno final ao ambiente.

A dinâmica sequencial inicia-se com a submissão do prompt do agente ao SLM primário alocado na GPU. A resposta preliminar gerada é interceptada antes da entrega e direcionada a um módulo verificador (_Critic_). Se o verificador identificar falhas lógicas ou violações de invariantes, um traço de erro (_error trace_) é sintetizado e injetado de volta ao contexto do SLM primário, acionando um ciclo de auto-correção. Uma vez aprovada pelo _Critic_, a resposta passa pela camada de _Response Healing_ para garantia da integridade sintática antes da emissão final ao sistema.

### Arquiteturas de Self-Correction com Isolamento de Memória

Para operar um modelo avaliador (_Critic Model_) sem violar os limites de VRAM, é necessária uma arquitetura de particionamento estrito de memória:

O modelo primário de 3B a 4B parâmetros (como Qwen3-4B ou Nemotron Nano) permanece fixado na GPU em Q4_K_M ou IQ3_M, consumindo entre 2,2 GB e 3,2 GB de VRAM. O espaço de VRAM remanescente é reservado exclusivamente para o _KV-Cache_ de contexto estendido e para o buffer de ativação do modelo primário.

O modelo crítico ou verificador é desacoplado da VRAM da GPU e transferido para a infraestrutura da CPU host. Essa verificação pode ser estruturada por meio de duas abordagens complementares:

Um verificador heurístico e simbólico construído como um motor determinístico em Rust executa validações de lógica formal, pré-condições, pós-condições e invariantes de sistema sem nenhum consumo de VRAM.

Alternativamente, um SLM crítico ultra-compacto de 0,5B a 1,5B parâmetros formatado em GGUF é mantido na memória RAM do sistema e executado exclusivamente na CPU via instruções vetoriais AVX2 ou AVX-512. O motor `llama.cpp` gerencia essa execução paralela sem competir pelos recursos de computação e memória da GPU.

A comunicação no ciclo de auto-correção evita novas chamadas completas do prompt (_re-prefill_). Quando o _Critic_ detecta um erro lógico, o daemon injeta apenas o traço de erro no contexto do modelo via reutilização de prefixo do _KV-Cache_ (_KV-Cache Prefix Reuse_), preservando os tensores de atenção previamente avaliados e reduzindo drasticamente a latência de re-geração.

## Response Healing: Recuperação Sintática e Lógica Zero-Token

Embora restrições de decodificação (_Constrained Decoding_) via `llguidance` assegurem que a amostragem siga gramáticas EBNF ou esquemas JSON, modelos quantizados de 3B a 4B frequentemente encontram _deadlocks_ sintáticos ou geram estruturas malformadas quando interrompidos ou ao tentar expressar raciocínios complexos em chaves truncadas. O _Response Healing_ (cura de resposta) atua no pipeline de saída antes de acionar um novo ciclo de geração pelo modelo, eliminando o custo de inferência suplementar.

O pipeline de cura sintática opera de forma linear sobre o buffer de streaming da resposta. A saída bruta do modelo passa primeiro por um filtro de remoção de cercas e artefatos de marcação (como blocos Markdown). Em seguida, o texto limpo entra no motor de análise _zero-copy_, que varre a estrutura aplicando correções de pontuação, aspas e delimitadores. Por fim, o motor executa o auto-fechamento de pilhas pendentes e a coerção de literais, produzindo um documento JSON válido e tipado sem disparar chamadas adicionais à GPU.

### Bibliotecas e Crates Nativas em Rust

A integração de motores de reparo em linguagem nativa diretamente no daemon de inferência permite corrigir imperfeições sintáticas com latência sub-milissegundo. As principais soluções _open-source_ em Rust para essa finalidade incluem:

- `jsonrepair` (por latias94): Biblioteca de baixo nível baseada em uma arquitetura de parsing por descendência recursiva _zero-copy_ (`&str` slicing) combinada com um scanner otimizado para saídas de LLMs. É capaz de processar fluxos (_streaming_) contínuos e arquivos NDJSON.
- `llm_json` e `json-repair`: Portes nativos em Rust do algoritmo `json_repair`. São otimizados para interceptar strings corrompidas de LLMs, aplicando correções estruturais baseadas em autômatos finitos.
- `fast_json_repair`: Implementação em Rust focada em alto desempenho, projetada para substituir executáveis lentos e tratar buffers truncados com aceleração de memória.

### Algoritmos e Heurísticas de Recuperação Sintática

Os motores de _Response Healing_ operam por meio de uma sequência de transformações heurísticas aplicadas sobre o buffer de saída:

|**Categoria de Erro**|**Mecanismo Algorítmico de Reparo**|**Impacto no Processamento**|
|---|---|---|
|**Blocos de Código Markdown**|Remoção de marcações de cercas (como ` ```json ... ``` `) e textos introdutórios ou conclusivos anexados fora da estrutura JSON.|Preserva apenas a carga útil legível por parsers rígidos.|
|**Delimitadores e Aspas**|Conversão de aspas simples para duplas, remoção de aspas duplicadas e escape automático de caracteres de controle não escapados.|Garante conformidade com a especificação RFC 8259.|
|**Chaves Desaspadas**|Inserção sintática de aspas duplas em identificadores de chaves literais gerados sem delimitação pelo SLM.|Corrige saídas no formato de dicionários Python ou objetos JavaScript.|
|**Vírgulas Pendentes e Ausentes**|Inserção heurística de vírgulas faltantes entre elementos de _arrays_ ou objetos e remoção de vírgulas no último elemento (_trailing commas_).|Evita falhas críticas de parse em bibliotecas estritas como `serde_json`.|
|**Estruturas Truncadas**|Algoritmo de autocompletude por pilha de delimitadores para fechar colchetes e chaves pendentes ao fim de fluxos interrompidos.|Recupera dados parciais úteis de respostas cortadas por limite de tokens.|
|**Coerção de Tipos e Literais**|Normalização de literais Python ou JS (`True`, `False`, `None`, `undefined`) para padrões JSON (`true`, `false`, `null`) e coerção de escalares.|Mantém a compatibilidade com esquemas de validação estritos.|

A incorporação do _Response Healing_ no daemon de inferência do SODA intercepta a resposta no buffer de comunicação IPC. Se o parser estrito falhar, o buffer é submetido ao `jsonrepair` em Rust. Somente se o reparo sintático não produzir uma estrutura válida de acordo com o esquema esperado é que a chamada é devolvida ao loop de re-geração do modelo.

## Dinâmica de Ativação e Reparo Cognitivo no Estado da Arte

A quantização agressiva afeta a precisão dos pesos internos de um SLM, distorcendo a trajetória das ativações ao longo das camadas do Transformer. Técnicas emergentes de tempo de execução permitem reparar e guiar esse fluxo de ativação sem requerer re-treinamento ou aumentar o consumo de memória estática.

### Vetores de Controle via Engenharia de Representação (RepE)

A Engenharia de Representação (_Representation Engineering_ - RepE) introduz a capacidade de alterar o comportamento do modelo manipulando diretamente os tensores de ativação intermediários nas passagens diretas (_forward passes_). Em motores nativos como `llama.cpp` e `mistral.rs`, essa funcionalidade é suportada por meio do carregamento de vetores de controle em formato GGUF.

O mecanismo funciona interceptando a saída do tensor da camada intermediária ($h_l$) por meio de rotinas registradas na inferência, como a _callback_ `cb_eval` do `llama.cpp`. O vetor de controle adiciona um deslocamento direcional constante ao espaço vetorial oculto:

$$h_l' = h_l + \alpha \cdot v_l$$

Onde $h_l$ representa a ativação original na camada $l$, $v_l$ é o vetor de direção comportamental extraído via Análise de Componentes Principais (PCA) ou _Sparse Autoencoders_ (SAE), e $\alpha$ é o fator de escala dinâmico.

#### Aplicação Prática no Daemon de Inferência

Os vetores de controle atuam como um compensador de destilação. Em SLMs de 3B a 4B parâmetros, vetores de controle específicos auxiliam na supressão de alucinações e recusas indevidas, eliminando desvios de atenção que levam o modelo a perder o contexto da instrução agêntica. Além disso, eles forçam a aderência rígida a raciocínios lógicos, amplificando as direções associadas ao raciocínio passo a passo antes da emissão da resposta.

Nos executáveis `llama-cli` ou `llama-server`, os vetores são aplicados diretamente via linha de comando ou pela API de controle do servidor. A seleção da faixa de camadas (_layer range_) é fundamental: aplicar vetores de controle nas primeiras camadas (0 a 9) costuma corromper a extração de recursos sintáticos básicos, enquanto a aplicação nas camadas intermediárias e superiores (10 a 24 em modelos de 32 camadas) altera o comportamento sem desestruturar a linguagem.

Modelos densos, como o Qwen 4B, suportam escalas $\alpha$ entre `0.15` e `0.6` sem perda de fluência. Arquiteturas Mixture-of-Experts (MoE) mostram-se extremamente sensíveis, exigindo escalas reduzidas entre `0.01` e `0.05` para evitar saídas corrompidas.

### Verificação Especulativa e Multi-Token Prediction (MTP)

Outra frente de aceleração e reparo cognitivo durante o runtime é a decodificação especulativa adaptada para verificação de coerência. Arquiteturas modernas incorporam o conceito de _Multi-Token Prediction_ (MTP). Em vez de prever um único token autoregressivo por passo, o modelo especula múltiplos tokens subsequentes que são validados em um único passo de verificação no modelo principal.

A utilização do MTP ou de modelos rascunho (_Draft Models_) ultraleves acelera a geração paralela de tokens. Isso libera tempo de computação na GPU que pode ser reinvestido diretamente nos loops de reflexão do _Test-Time Compute_, mantendo a latência global dentro de limites aceitáveis para cenários de borda.

## Avaliação Comparativa das Técnicas de Otimização

A tabela a seguir sintetiza o impacto das diferentes técnicas realizáveis em tempo de execução para SLMs operando em hardware com restrição de memória:

|**Técnica ou Padrão**|**Custo de VRAM Suplementar**|**Sobrecarga de CPU e Latência**|**Ganho Estimado em Lógica Complexa**|**Complexidade de Implementação**|
|---|---|---|---|---|
|**Response Healing (Rust Native)**|0 MB (Execução exclusiva na CPU Host)|Menor que 1 ms por requisição.|Recuperação sintática direta em até 99% das falhas de estruturação.|Baixa (Inclusão de crate Rust no daemon de inferência).|
|**Vetores de Controle (RepE GGUF)**|Menor que 1 MB (Apenas tensores de direção de camada).|Menor que 2% de degradação no throughput de tokens.|+10% a +20% em consistência de regras e redução de alucinações.|Média (Requer extração de vetores via SAE ou PCA e seleção de camadas).|
|**Constrained Decoding (`llguidance`)**|~10 MB a 50 MB (Trie de estados da gramática)|Negligenciável na fase de _Decode_, leve aumento no _Prefill_.|Elimina erros de validação sintática estrita.|Média (Requer integração de gramáticas EBNF ou JSON Schema).|
|**Sequential Reflexion Loop (Ralph)**|0 MB suplementar (Reutiliza KV-Cache do modelo primário)|Multiplica a latência por $N$ passos de reflexão ($1.5\times$ a $3\times$).|+20% a +35% na resolução de tarefas lógicas difíceis.|Média/Alta (Exige orquestração de estado no daemon agêntico).|
|**Critic Model Desacoplado (CPU AVX)**|0 MB VRAM (Aloca ~500MB a 1.5GB na RAM do Host).|Execução assíncrona na CPU, latência amortecida pelo pipeline.|+15% a +25% em verificação de regras de negócio complexas.|Alta (Exige isolamento de processos IPC e balanceamento Host/Device).|

## Arquitetura Recomendada e Conclusões para o Sistema Operacional Agêntico

Para superar o limite de precisão do modelo de 3B a 4B parâmetros mantendo a estabilidade operacional na GPU de 6GB de VRAM, recomenda-se a estruturação do pipeline de execução do SODA em quatro etapas sequenciais no daemon de inferência:

Na fase de inicialização e modulação de ativação, o servidor de inferência local (`llama-server`) carrega os vetores de controle em formato GGUF ajustados especificamente para a família do modelo em uso. Um fator de escala reduzido (entre `0.25` e `0.35`) é aplicado às camadas intermediárias (faixa de 10 a 24), estabilizando a trajetória de atenção do modelo e reduzindo desvios cognitivos causados pela quantização.

Na fase de inferência e restrição sintática, o modelo executa na GPU sob controle do `llguidance` para garantir que os tokens amostrados sigam a estrutura do esquema JSON esperado.

Na fase de cura nativa no buffer IPC, a resposta gerada em streaming é interceptada por um módulo em Rust integrado com o crate `jsonrepair`. A limpeza de cercas Markdown, o fechamento automático de colchetes e chaves cortados por limite de contexto e a correção de vírgulas ou aspas ocorrem em tempo real antes do envio do payload ao subsistema do agente. Esse procedimento resolve falhas de formatação sem consumir novos tokens de geração.

Na fase de reflexão sequencial (_Ralph Loop_), se a saída tratada for sintaticamente válida, mas falhar na verificação semântica ou nas regras de negócio (avaliadas por verificadores determinísticos no host ou por um SLM ultraleve executando via CPU AVX-512), o daemon inicia um ciclo de auto-correção. O traço do erro é formatado e anexado ao contexto utilizando a reutilização de prefixo do _KV-Cache_, orientando a re-geração sem incorrer na re-avaliação do prompt completo.

A integração desse pipeline eleva a taxa de acerto de modelos quantizados de 3B a 4B parâmetros a níveis observados em modelos não quantizados de 7B a 8B em tarefas agênticas, mitigando a perda cognitiva da quantização sem violar o orçamento de 6GB de VRAM nem provocar estouros de memória pela interface PCIe.

---

