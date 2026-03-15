# Relatório de Engenharia: Orquestração e Viabilidade de Inferência Agêntica em Hardware Restrito (Edge Computing)

## 1. Análise de Contenção de Hardware e Física de Memória

A concepção e implementação de um Sistema Operacional Agêntico (Genesis MC) operando na borda (Edge Computing) exige uma arquitetura de inferência rigorosamente otimizada, dadas as fronteiras matemáticas impostas pelo hardware subjacente. A topologia física em questão compreende uma Unidade Central de Processamento (CPU) auxiliada por 16 GB de RAM de sistema e uma Unidade de Processamento Gráfico (GPU) dedicada NVIDIA RTX 2060m, equipada com 6 GB de VRAM. Este cenário impõe restrições absolutas na alocação de tensores, exigindo compromissos arquitetônicos de alta precisão entre o nível de quantização, a profundidade do modelo (contagem de parâmetros), a retenção do estado de atenção (Cache de Chaves e Valores - KV Cache) e a largura de banda do barramento PCIe para processos de _offloading_.

A viabilidade de execução de Modelos de Linguagem de Grande Escala (LLMs) limitados a um teto de aproximadamente 8 bilhões de parâmetros depende intrinsecamente da adoção de algoritmos de compressão de peso e quantização agressiva, como GGUF (tipicamente no formato Q4_K_M), AWQ (Activation-aware Weight Quantization) e GPTQ. Em um paradigma de precisão de 16-bits (FP16 ou BF16), cada bilhão de parâmetros consome aproximadamente 2 GB de memória. Consequentemente, um modelo de 8B exigiria cerca de 16 GB apenas para alocar seus pesos estáticos, excedendo em mais do dobro a capacidade da RTX 2060m. Através da quantização para 4-bits, a exigência recai para aproximadamente 0,5 a 0,7 GB por bilhão de parâmetros, comprimindo a pegada estática de um modelo de 8B para a faixa de 4,2 a 4,8 GB.

### 1.1. O Limite Matemático de VRAM e o Overhead Estrito de 20%

A estabilidade de um Sistema Operacional Agêntico como o Genesis MC baseia-se na prevenção absoluta de falhas por falta de memória (Out-Of-Memory - OOM) e na mitigação do encerramento forçado de processos de inferência pelo sistema operacional hospedeiro. Para assegurar esta resiliência, a imposição de uma margem de segurança (overhead) de 20% sobre a VRAM total é mandatória. A equação de disponibilidade de memória na placa gráfica RTX 2060m é definida da seguinte forma empírica:

| **Componente de Memória**        | **Capacidade Alocada (Megabytes)** | **Capacidade Alocada (Gigabytes)** | **Impacto Operacional**                                                            |
| -------------------------------- | ---------------------------------- | ---------------------------------- | ---------------------------------------------------------------------------------- |
| **VRAM Física Total**            | 6.144 MB                           | 6,00 GB                            | Limite rígido do hardware da NVIDIA RTX 2060m.                                     |
| **Overhead do Sistema (20%)**    | ~1.228 MB                          | ~1,20 GB                           | Reservado para _framebuffers_, interface gráfica (GUI), drivers e processos do SO. |
| **VRAM Efetiva para Inferência** | **~4.915 MB**                      | **~4,80 GB**                       | Teto máximo absoluto para a soma dos pesos do modelo, ativações e KV Cache.        |

Qualquer configuração de modelo que, somada ao seu respectivo KV Cache, ultrapasse a marca estrita de 4,8 GB de VRAM forçará um _spillover_ (transbordamento) para a RAM do sistema (16 GB) através do barramento PCIe. A velocidade de transferência da memória de vídeo (GDDR6) é calculada em centenas de gigabytes por segundo, enquanto a transferência via barramento PCIe (especialmente em arquiteturas móveis mais antigas) pode criar um gargalo severo, reduzindo a taxa de geração de tokens (Tokens Per Second - TPS) de forma catastrófica. Portanto, a orquestração do Genesis MC deve ser projetada para manter a inteligência central residente estritamente dentro da fronteira de 4,8 GB, utilizando _layer offloading_ (descarregamento de camadas) apenas para modelos que excedam essa capacidade, suportando a degradação da latência de forma controlada e assíncrona.

### 1.2. O Paradoxo do Contexto Expandido e a Arquitetura de Atenção

Um dos requisitos para a exploração de modelos emergentes é o suporte a janelas de contexto grandes, tipicamente superiores a 32.000 tokens (32k+). No entanto, o dimensionamento da janela de contexto tem um impacto direto e profundo no consumo de VRAM devido à natureza da arquitetura _Transformer_ e à necessidade de armazenar o KV Cache. O KV Cache é a memória de trabalho do modelo, armazenando as representações matemáticas de todos os tokens anteriores para evitar o recálculo a cada novo token gerado.

Em modelos que utilizam a tradicional _Multi-Head Attention_ (MHA), o KV Cache cresce linearmente e de forma agressiva. Por exemplo, em um modelo hipotético de 8B com MHA, uma janela de 32k tokens pode facilmente consumir múltiplos gigabytes de VRAM apenas para o cache, inviabilizando a execução em hardware de 6 GB. Para solucionar este problema em dispositivos de borda, a indústria adotou arquiteturas alternativas:

- **Grouped-Query Attention (GQA):** Utilizada em modelos como Mistral e Qwen, a GQA compartilha as cabeças de chaves e valores (KV) entre múltiplas cabeças de consulta (Query). Se um modelo possui 32 cabeças de consulta e 8 cabeças de KV, a pegada do KV Cache é reduzida em um fator de 4x (ou até 8x em outras configurações), mitigando o crescimento exponencial da memória sem perda significativa de precisão analítica.
- **Multi-Head Latent Attention (MLA):** Introduzida pela série DeepSeek (V2, V3 e R1), a MLA comprime os tensores KV em um espaço vetorial de dimensão muito menor (um vetor latente) e os reconstrói sob demanda. Esta inovação permite uma redução fenomenal do tamanho do KV Cache, chegando a ser 93% menor em comparação com implementações MHA, o que torna contextos massivos de 128k viáveis mesmo em hardware com VRAM altamente restrita.

## 2. Avaliação Exaustiva de Modelos Fundacionais (Sub-8B)

A seleção dos modelos adequados para o Genesis MC baseia-se na interseção entre a alta capacidade de raciocínio lógico (para agentes de codificação e decisão) e a compactação paramétrica. A seguir, apresenta-se uma dissecação técnica dos modelos mais proeminentes e recentes no ecossistema de código aberto (open-weight), limitados ao limiar de aproximadamente 8 bilhões de parâmetros para garantir o cumprimento das métricas de VRAM.

### 2.1. A Família Qwen 3.5 (Alibaba Cloud)

Lançada formalmente no início de 2026, a série Qwen 3.5 da Alibaba Cloud introduziu modificações arquitetônicas significativas, projetadas para consolidar capacidades nativas multimodais e expandir drasticamente a proficiência em codificação e raciocínio lógico. A série adota uma arquitetura híbrida inovadora que funde redes de atenção linear (_Gated Delta Networks_) com mecanismos de atenção tradicional, otimizando tanto o tempo de treinamento quanto a velocidade de inferência na borda.

Dentro do limite estipulado, os modelos de 0.8B, 2B, 4B e 9B são os candidatos para avaliação.

|**Modelo (Série Qwen 3.5)**|**Contagem de Parâmetros**|**Quantização Recomendada**|**VRAM Estática Exigida (Aprox.)**|**Arquitetura de Atenção**|**Janela de Contexto Nativa**|
|---|---|---|---|---|---|
|**Qwen 3.5 0.8B**|0.8B|FP16 / Q4_K_M|~2.97 GB / ~1.51 GB|Híbrida / GQA|262k|
|**Qwen 3.5 4B**|4.3B|FP16 / Q4_K_M|~10.64 GB / ~3.49 GB|Híbrida / GQA|262k|
|**Qwen 3.5 9B**|9.0B|Q4_K_M|~6.49 GB|Híbrida / GQA|262k|

**Análise de Viabilidade:** O modelo **Qwen 3.5 9B** apresenta capacidades formidáveis para agentes autônomos, atingindo pontuações rigorosas em benchmarks como o MMLU-Pro (82.5%) e o LiveCodeBench v6 (82.7%). No entanto, seus requisitos estáticos de memória na quantização Q4 exigem 6,49 GB de VRAM. Este valor excede o limite absoluto do hardware de 6 GB, e oblitera o teto de 4,8 GB estabelecido pelo overhead de 20%. Portanto, é fisicamente impossível manter o Qwen 3.5 9B inteiramente residente na RTX 2060m. A sua utilização no Genesis MC exigiria um particionamento severo (descarregando uma parcela expressiva de suas 32 camadas para a memória RAM do sistema) , resultando em uma degradação de desempenho devido à latência do barramento PCIe.

Por outro lado, o **Qwen 3.5 4B** surge como um candidato viável para tarefas medianas. Exigindo aproximadamente 3,49 GB em Q4_K_M , o modelo deixa cerca de 1,3 GB de espaço livre na VRAM efetiva para alocação do KV Cache. Considerando que a arquitetura utiliza _Grouped-Query Attention_, o crescimento do cache é otimizado, permitindo que a aplicação retenha confortavelmente entre 8.000 e 16.000 tokens de contexto ativo sem disparar eventos de _swap_ ou OOM, tornando-o um executor secundário sólido.

### 2.2. Ecossistema Mistral: A Série Ministral 3 e Mistral Small

A Mistral AI consolidou sua estratégia de mercado em 2026 segmentando seus modelos em frotas fronteiriças (Frontier) e modelos otimizados para a borda (Edge). O modelo Mistral Small 3.1, embora altamente capaz, conta com 24B de parâmetros e é matematicamente incompatível com a RTX 2060m, exigindo mais de 16 GB mesmo sob quantização máxima.

A verdadeira inovação reside na família **Ministral 3**, projetada explicitamente para ambientes de computação e memória restritos. Esta família compreende modelos de 3B, 8B e 14B parâmetros, sendo o **Ministral 3 8B** o foco imperativo desta análise.

- **Especificações Arquitetônicas:** O Ministral 3 8B possui um total de 8.8 bilhões de parâmetros, distribuídos de forma assimétrica: 8.4 bilhões dedicados ao raciocínio lógico (Language Model) e 400 milhões integrados como um codificador de visão nativo (Vision Encoder), o que concede ao sistema agêntico a habilidade inata de analisar fluxos multimodais e interfaces gráficas. O modelo possui 34 camadas de profundidade, uma dimensão oculta de 4096 e emprega GQA com 32 cabeças de consulta e 8 cabeças de KV.
- **Gestão de Contexto e Memória:** O modelo suporta uma vasta janela de contexto de 256.000 tokens através do mecanismo de extensão YaRN (Yet another RoPE extensioN). Na quantização Q4_K_M (aproximadamente 4,5 a 5 GB), o modelo atinge o teto da VRAM disponível. Assim como os modelos de 8B concorrentes, requererá que uma pequena fração das camadas iniciais resida na memória RAM da CPU.
- **Potencial Agêntico:** A Mistral calibrou a versão "Instruct" do Ministral 3 8B com um treinamento rigoroso voltado para aderência ao prompt do sistema e capacidades de roteamento JSON de nível superior (_function calling_). Em avaliações empíricas, este modelo demonstrou superar as variantes de 8B anteriores na execução determinística de ferramentas descritas em arquivos markdown e definições de API, tornando-se um componente essencial para o Genesis MC.

### 2.3. Rnj-1: A Anomalia Arquitetônica em Raciocínio de Código

O modelo **Rnj-1**, desenvolvido pela Essential AI (laboratório co-fundado pelos autores do artigo original do _Transformer_), emergiu no final de 2025 como uma arquitetura densa de 8.3 bilhões de parâmetros, concebida puramente do zero com foco implacável em domínios de Ciência, Tecnologia, Engenharia e Matemática (STEM) e geração de código de alta complexidade.

Apesar do seu tamanho promissor e dos resultados de elite — alcançando 20,8% no brutal benchmark SWE-bench Verified (frequentemente superando modelos 10x maiores) — uma análise morfológica profunda revela anomalias que tornam sua execução em hardware restrito um desafio extremo:

- **Atenção Global (A Causa da Inflação de Memória):** Diferente do Qwen 3.5 e do Ministral 3, o Rnj-1 utiliza **exclusivamente atenção global** (Global Attention) em todas as suas 32 camadas. Ele descarta otimizações de _sliding window_ ou agrupamentos densos vistos no Gemma 3.
- **Parâmetros:** O modelo opera com uma dimensão oculta de 4096, 32 cabeças de atenção e, notavelmente, 8 cabeças de KV (indicando uma forma primária de GQA), mas com uma exigência computacional assíncrona pesada devido ao seu vocabulário estendido de 128k.
- **Impacto de Contexto de 32k:** A extensão do contexto nativo de 8k para 32k foi obtida através de treinamento contínuo com YaRN. No entanto, a ausência de mecanismos locais de restrição de atenção resulta em um consumo de VRAM quase insustentável quando o contexto se expande. A quantização Q4_K_M comprime o modelo base para aproximadamente 4,8 GB , o que preenche os exatos 100% da VRAM efetiva da RTX 2060m. Qualquer geração de código que absorva repositórios através de RAG (Retrieval-Augmented Generation) exigirá offloading maciço do KV Cache e dos tensores de peso para a RAM do sistema.
- **Veredito de Implantação:** O Rnj-1 é poderoso demais para ser ignorado em um Sistema Operacional Agêntico voltado a desenvolvedores. Contudo, devido à sua sede por memória decorrente da atenção global , ele deve ser mantido inativo (descarregado) até que uma tarefa específica de codificação pesada seja acionada, operando em contextos limitados e isolados, aceitando taxas de TPS mais lentas como preço por sua acurácia algorítmica.

### 2.4. DeepSeek-R1-Distill (7B), DeepSeek V3.2 e a Fronteira de Transição para o V4

O paradigma global de inferência local foi permanentemente alterado com o advento das metodologias de destilação de modelos de raciocínio por reforço (RL), capitaneadas pela DeepSeek AI. A série R1 demonstrou que as cadeias de pensamento (Chain-of-Thought - CoT) podem ser emuladas em arquiteturas menores sem comprometer severamente a qualidade cognitiva.

- **DeepSeek-R1-Distill-Qwen-7B:** Este modelo foi destilado a partir dos padrões de raciocínio lógico do modelo principal R1 (671B), utilizando a arquitetura densa do Qwen2.5-Math-7B como fundação física. Compreendendo aproximadamente 8 bilhões de parâmetros no total (dada a expansão do vocabulário e projeções), o modelo representa a convergência entre tamanho gerenciável e cognição avançada.
- **Ocupação de VRAM:** A versão quantizada em Q4_K_M exige cerca de 4,5 a 4,8 GB de VRAM estática. Isto o posiciona perfeitamente na fronteira do teto de 4,8 GB da RTX 2060m. Para manter o desempenho sem transbordamentos lentos para a CPU, a retenção de contexto no DeepSeek-R1-Distill-7B deve ser policiada ativamente pelo SO Agêntico. Testes sugerem que, em hardwares de memória limitada, alocar contextos entre 4.096 e 8.192 tokens é o ponto ideal para evitar a exaustão de VRAM pelo KV Cache, uma vez que o CoT inerente do R1 exige buffers de saída longos.
- **DeepSeek V3.2 e Rumores do V4:** O ecossistema DeepSeek encontra-se numa fase de transição intensa em março de 2026. O modelo V3.2 atual atua primariamente em topologias MoE maiores, otimizado para _Thinking Mode_ e raciocínio multi-passo. No horizonte imediato, encontra-se a potencial arquitetura do DeepSeek V4 (vazado frequentemente sob o código _MODEL1_ no repositório FlashMLA). A literatura vazada aponta para avanços monumentais, incluindo atenção esparsa mHC (Manifold-Constrained Hyper-Connections) e memória condicional _Engram_, que permite contextos nativos superiores a 1 milhão de tokens e estabilidade de treinamento radicalmente barateada. Embora a versão principal do V4 seja esperada para ser um colosso de 1 trilhão de parâmetros , é altamente provável que técnicas semelhantes de destilação produzam versões sub-8B nos meses seguintes ao lançamento oficial. Até lá, o DeepSeek-R1-Distill-Qwen-7B permanece como o ápice irrefutável do raciocínio executável na RTX 2060m.

### 2.5. FunctionGemma e Gemma 3: O Paradigma de Tool Calling Extremo

Um sistema operacional agêntico como o Genesis MC não sobrevive apenas de código ou matemática; a infraestrutura depende de roteadores cognitivos ultrarrápidos, capazes de analisar o _prompt_ do usuário, determinar qual ferramenta do sistema executar e emitir a API correta com precisão impecável (Function Calling ou Tool Calling).

Para resolver este gargalo com latência mínima, o Google apresentou o ecossistema Gemma 3 e, criticamente, o **FunctionGemma 3 270M**.

- **FunctionGemma (270M):** Diferente de modelos generalistas ajustados para diálogos ou geração criativa, o FunctionGemma foi treinado com o propósito exclusivo de traduzir intenções não-estruturadas em saídas determinísticas de API. O modelo utiliza pares de tokens de controle restritos (`<start_function_call>`, `<end_function_call>`, e o delimitador `<escape>`) para assegurar que a saída obedece aos esquemas JSON submetidos sem falhas sintáticas.
- **Vantagem em VRAM e Latência:** A pegada do FunctionGemma é quase invisível. Em quantização dinâmica INT8, os pesos ocupam exíguos 288 MB no armazenamento. Em operação ativa, consome um pico de memória residente (RSS) de aproximadamente 550 MB. Com um Time-To-First-Token (TTFT) inferior a 0,3 segundos, o FunctionGemma pode viver de forma permanente na VRAM da RTX 2060m, consumindo menos de 10% da capacidade total, respondendo instantaneamente a chamadas de sistema (como modificar o volume, criar diretórios ou agendar tarefas).
- **Gemma 3 4B:** Se a complexidade das ferramentas (API Schema) ultrapassar o poder analítico do FunctionGemma (que demonstra precisão de 58% nativa subindo para 85% apenas mediante _fine-tuning_ específico do desenvolvedor) , o **Gemma 3 4B** serve como um plano de contingência (_fallback_). Com 4,3 bilhões de parâmetros, ele exige ~3,5 GB de VRAM em Q4_K_M e é equipado para lidar com uma gama mais ampla de interações multimodais e de roteamento de contexto rico (até 128k tokens suportados através da extensão da frequência base do mecanismo RoPE).

## 3. Exploração Aberta: Contextos Massivos (32k+) em Modelos Emergentes (Sub-8B)

A requisição de exploração aberta focou na identificação de outros modelos recentes, com menos de 8 bilhões de parâmetros, disponíveis nas plataformas HuggingFace ou via Ollama, que acomodam grandes janelas de contexto (32k+) e que poderiam figurar no ecossistema do Genesis MC.

A barreira física de 4,8 GB efetivos de VRAM torna qualquer promessa de contexto massivo uma complexidade de engenharia. A alocação da matriz de atenção exige quantidades maciças de cache. Uma janela de 128.000 tokens em modelos de atenção densa pode requerer entre 20 GB e 30 GB apenas para o KV Cache. O uso em hardware restrito, portanto, recai sobre a utilização inteligente de quantização também no nível do KV Cache.

Modelos emergentes notáveis incluem:

1. **Llama 3.3 8B Instruct (e Variantes Abliteradas):** O Facebook (Meta) silenciosamente disponibilizou a versão 3.3 do modelo de 8B através de APIs e vazamentos na comunidade (frequentemente encontráveis no HuggingFace através de usuários que removeram adaptadores ou aplicaram técnicas de abliteração ortogonal para remover recusas). Este modelo suporta nativamente 128.000 tokens de contexto. Para que isso opere na RTX 2060m, o motor de inferência deve ativar a compressão quantizada de chaves e valores (ex: KV Cache em Q4_0 ou Q8_0 no llama.cpp), reduzindo o consumo do contexto para cerca de 1 GB a cada 16.000 tokens.
2. **IBM Granite 3.3 8B Instruct:** Uma adição valiosa recente com suporte a contextos profundos de 128k. O Granite é consistentemente treinado para aderência de instruções e roteamento corporativo, oferecendo desempenhos equiparáveis à classe de 8B do Llama, mas com licença voltada à transparência de dados.
3. **Phi-4 Mini (3.8B):** A Microsoft avançou a família Phi, criando modelos densos pequenos com janelas de contexto otimizadas. Com apenas 3.8 bilhões de parâmetros, seu peso em VRAM é modesto (abaixo de 3 GB em GGUF), permitindo a alocação do modelo inteiro e um vasto contexto diretamente na placa da série 2060.
4. **OpenCoder 8B e Qwen 2.5 Coder 7B:** Especializados em ingestão de repositórios inteiros de código fonte. Quando um agente Genesis MC precisa analisar 10 arquivos Python em simultâneo, estes modelos garantem que os conectivos lógicos sejam preservados através das técnicas avançadas de RoPE scaling e GQA.

## 4. Análise de Ferramentas de Execução e Orquestração VRAM/RAM

O coração de um Sistema Operacional Agêntico baseia-se na orquestração assíncrona. Os agentes recebem comandos e os delegam a diferentes inteligências em segundo plano. Uma arquitetura de modelo monolítico é insustentável em um dispositivo de 6 GB de VRAM; a solução é alternar dinamicamente e rodar instâncias multimodais concorrentemente. Avaliaremos quatro abordagens principais de inferência para determinar a topologia correta.

### 4.1. vLLM e o Limite do PagedAttention

O **vLLM** é considerado o padrão-ouro em servidores na nuvem (High-Throughput Serving). Sua inovação fundacional, o _PagedAttention_, soluciona um problema crônico da inferência LLM: a fragmentação do KV Cache. Tradicionalmente, os tensores do KV Cache são alocados de forma contígua, resultando num desperdício de memória de até 30%. O _PagedAttention_ divide a memória do cache em blocos de tamanho fixo mapeados virtualmente sob demanda, derrubando o desperdício para cerca de 4%. Em teoria, essa otimização extrema deveria beneficiar hardwares limitados como a RTX 2060m.

Contudo, na prática de implementações periféricas (Edge Computing), o vLLM sofre com falhas graves em relação ao _offloading_ de modelos. Modelos como Rnj-1 ou Ministral 3 exigem particionamento (onde algumas camadas devem residir nos 16 GB de RAM da CPU devido à limitação de 4,8 GB da placa). O vLLM oferece uma flag `--cpu-offload-gb` , porém a sua implementação introduz um gargalo massivo de comunicação no barramento PCIe. Ao contrário de outras ferramentas que executam parte do grafo na CPU e parte na GPU independentemente, o vLLM movimenta ativamente o KV Cache ou os pesos de volta e para frente continuamente. Testes apontam um imposto de desempenho brutal: a transferência do modelo para a CPU com o vLLM resultou em uma queda de latência de 56,87 tokens/segundo (tps) para miseráveis 1,65 tps, constituindo uma degradação de 35x na performance. A conclusão é inexorável: o vLLM só é indicado para cenários onde todo o modelo e o contexto cabem inteiramente na VRAM (por exemplo, instanciando o FunctionGemma 270M), descartando-se o uso do vLLM para _offloading_ pesado.

### 4.2. llama.cpp: A Supremacia do mmap e Offloading Granular

O projeto de código aberto **llama.cpp** emerge como o pilar da computação de borda sustentável. Desenvolvido inteiramente em C/C++, sua maior virtude contra as limitações da RTX 2060m é o uso da formatação GGUF alinhada com a chamada de sistema `mmap()` (Memory-Mapped Files).

- **A Física do mmap:** Ao ler um arquivo GGUF de ~5 GB do SSD NVMe, o sistema operacional hospedeiro não realiza cópias diretas da totalidade dos dados para a RAM. Em vez disso, ele cria ponteiros de alocação virtual. O armazenamento atua funcionalmente como memória, e os dados só trafegam para a memória cache do processador ou VRAM à medida que a matriz de tensores necessita ser calculada para prever o próximo token. Este mecanismo mitiga consideravelmente a exaustão da memória do sistema.
- **Offload de Camadas (n-gpu-layers):** Ao contrário do vLLM, o `llama.cpp` divide matematicamente a carga. Num modelo de 32 camadas como o Qwen 3.5 9B , se apenas 22 couberem na placa gráfica, o processamento ocorre linearmente através do processador central para as primeiras 10 camadas e então transita o tensor processado de forma eficiente para a GPU resolver as 22 finais. A latência é contida e as velocidades se mantêm usáveis (entre 15 e 30 tps) sem colapso do sistema.

O `llama.cpp` atualizou recentemente sua base incorporando o **Modo Roteador (Router Mode)**. Este parâmetro permite ao servidor emular sistemas como o Ollama, suportando a inicialização do motor e o descarregamento dinâmico de modelos na VRAM mediante uma requisição da API sem a necessidade de religar os serviços de fundo. Para a orquestração simultânea, o router permite lidar com o _context switching_ de maneira transparente.

### 4.3. LM Studio e Ollama: A Abstração Contra-Produtiva

Ferramentas como o **LM Studio** e o **Ollama** fornecem uma formidável facilidade de uso, encapsulando os intrincados argumentos de terminal (CLI) atrás de uma Interface Gráfica e API RESTful elegante. O Ollama, construído em Go, automatiza todo o provisionamento de GGUF e o descarregamento para a CPU via _wrappers_.

Entretanto, para um Sistema Operacional Agêntico projetado do zero visando controle extremo de fragmentação e _swapping_ acelerado, ambas as soluções introduzem abstrações contra-produtivas.

- O motor do Ollama é essencialmente um _fork_ de versões levemente modificadas do `llama.cpp`, frequentemente lutando com problemas de compatibilidade e de desempenho bruto devido aos seus _wrappers_ de abstração, levando a quedas de performance relatadas (overhead de 20-30% na velocidade de inferência frente ao uso do `llama.cpp` em modo _bare metal_).
- O Ollama e o LM Studio tentam prever a distribuição ideal de camadas entre VRAM e RAM, uma suposição que frequentemente ignora os picos gerados pelo _context caching_ dos fluxos agênticos complexos, resultando em quedas prematuras de sistema. Além disso, o gerenciamento de múltiplos instanciamentos pesados em paralelo não suporta retenção rígida no nível do daemon. O uso bruto de bibliotecas _low-level_ é aconselhável frente ao comodismo destes ambientes.

### 4.4. Llama-swap e a Mecânica do "Swap" Ultrarrápido

Dada a limitação severa de 6 GB, a ideia de operar dois modelos agênticos densos simultaneamente (por exemplo, Rnj-1 8B codificando e DeepSeek R1 7B validando a lógica) é impossível de manter concomitantemente em espaço estático, mesmo considerando a RAM (16 GB). O sistema será forçado a invocar um, apagar seus estados e então injetar o segundo. O recarregamento tradicional (frio) demoraria entre 15 e 30 segundos, comprometendo a ilusão de tempo real.

A ferramenta **llama-swap** apresenta-se como uma das soluções mais engenhosas da comunidade _edge computing_ para este dilema. Operando como um proxy transparente à frente de servidores como o `llama.cpp`, o `llama-swap` controla ativamente a alocação térmica de _endpoints_.

- **Unified Memory & PCIe Bandwidth:** Integrando o `llama-swap` com o argumento embutido em _flags_ não-documentadas do próprio `llama.cpp` (`GGML_CUDA_ENABLE_UNIFIED_MEMORY=1`), o processo ativa o mecanismo UMA (Unified Memory Architecture) nos sistemas compatíveis. O `llama-swap` consegue forçar os dados residuais da VRAM para a memória principal ou ler blocos gigantescos através da velocidade base do SSD NVMe em curtos espaços de tempo. Utilizando a capacidade seqüencial de um NVMe (normalmente de 3.5 a 7 GB/s), um arquivo GGUF de modelo inteiro pesando ~5 GB consegue ser trocado de estado em mero **2 a 5 segundos**. Este processo assegura retenção de estado anterior, configurando um ambiente ágil.
- **Controle Dinâmico de Grupos:** O `llama-swap` introduz a sintaxe de grupos (Groups). Para o Genesis MC, é possível alocar o modelo primário de roteamento sob a flag `swap: false` e `exclusive: true`, enquanto designamos modelos densos (como os agentes 7B) a grupos temporais que sofrem comutação automática dependendo da chamada REST.

## 5. Proposta de Topologia de Inferência Local para o Genesis MC

A convergência dos dados estáticos limitantes do hardware (Apenas 4,8 GB efetivos de VRAM e 16 GB de RAM do processador) e das propriedades empíricas dos modelos recém-explorados culminam na seguinte topologia estrutural (Sistema de Agente Composto). A arquitetura prescreve a bifurcação dos papéis de roteamento determinístico (instantâneo) e raciocínio assimétrico denso (em lotes paralelos). O cérebro local abandona a abordagem de um único LLM monolítico gigante em favor de especializações eficientes na borda (MoE em software).

### Nível 1: Gateway e Classificação Cognitiva Imediata

**Modelo Assinalado:** FunctionGemma 3 270M (INT8) **Mecanismo de Execução:** vLLM ou Llama.cpp (Residência Absoluta) **Função na Topologia:** Atuar como o nó receptor do SO. Sua latência quase nula (0.3s) e sua carga levíssima (288 MB estáticos / ~550 MB ativos ) o consolidam como o interceptador da inteligência primária. O FunctionGemma processa toda linguagem natural de entrada. Caso as intenções mapeiem a execuções lógicas simples ou de infraestrutura base (chamadas JSON nativas, configurações de arquivo, solicitações ao bash), o roteador finaliza a transação sem disparar processamento subsequente. **Espaço de VRAM Consumido:** ~0.6 GB. A VRAM Livre disponível na placa sobe de volta para 4,2 GB para o uso da segunda camada.

### Nível 2: Executores Especialistas (Heavy Workers)

**Modelos Assinalados:**

- Raciocínio Analítico e Matemático: `DeepSeek-R1-Distill-Qwen-7B` (GGUF Q4_K_M).
- Código/STEM Avançado e FIM (Fill-in-the-middle): `Rnj-1 8B` (GGUF Q4_K_M).
- Rotinas Generalistas Longas e Análise Documental: `Ministral 3 8B` (GGUF Q4_K_M).

**Mecanismo de Execução:** Proxy Llama-swap gerenciando instâncias dinâmicas de Llama.cpp com camada profunda de `mmap`. **Função na Topologia:** O Gateway identifica que o usuário requereu refatoração intensa de uma biblioteca em Python, o que excede as capacidades do modelo de 270M. O servidor REST (gerenciado pelo proxy) emite uma requisição ao modelo `Rnj-1`.

- O modelo especialista é ejetado do repouso latente (SSD) para a VRAM disponível via NVMe, um processo demorando de 2 a 5 segundos.
- O tamanho do arquivo do `Rnj-1 8B` em Q4_K_M é de aproximadamente 4,8 GB. A placa gráfica só dispõe de cerca de 4,2 GB residuais (devido ao FunctionGemma e o overhead do sistema). Automaticamente, o `llama.cpp` fará o _offloading_ de cerca de ~80% a 90% dos tensores de peso dentro das trilhas GPGPU, despachando as poucas últimas camadas ativas, assim como o expansivo KV Cache da janela de contexto, para a memória RAM DDR do sistema, utilizando a margem dos 16 GB totais.
- Uma vez concluída a compilação paralela com uma meta realística de 10 a 25 _tokens por segundo_ (tps), os tensores gerados são descartados e o proxy reverte o estado do modelo profundo para o SSD, esfriando ativamente a VRAM e retornando aos estados basais do Gateway, prevenindo assim que o acúmulo gradativo do uso da API e _Memory Leaks_ fragmentem as fronteiras críticas da placa NVIDIA e travem o sistema subjacente.

Esta orquestração garante a plena funcionalidade do SO Agêntico em restrições marginais: maximiza o tempo até o primeiro token (TTFT) mediante modelos hipercompactos como os roteadores do núcleo, preservando largura de banda profunda do PCIe apenas e exclusivamente quando os raciocinadores pesados em GGUF e compressão agressiva são evocados à operação.