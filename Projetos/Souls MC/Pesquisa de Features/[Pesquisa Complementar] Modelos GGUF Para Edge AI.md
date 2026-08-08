# Arquitetura de Inferência em Borda e Seleção de SLMs GGUF Especializados para Sistemas Operacionais Agênticos em Hardware Restrito

A execução de um Sistema Operacional Agêntico (SODA) em hardware altamente restrito, como a GPU NVIDIA GeForce RTX 2060 Mobile dotada de 6GB de VRAM GDDR6, estabelece limitações físicas severas quanto à pegada de memória e ao orçamento de processamento paralelo. O ecossistema de Small Language Models (SLMs) evoluiu significativamente, demonstrando que o desempenho de inferência na orquestração de agentes não depende estritamente da contagem bruta de parâmetros, mas da eficiência da arquitetura de atenção, da densidade de dados sintéticos de treinamento e da precisão da quantização.

Para garantir a estabilidade contínua no motor de inferência em Rust (`llama-cpp-2` e `mistral.rs`), a alocação de memória virtual da GPU precisa respeitar uma equação rigorosa de orçamento espacial. Em uma GPU com 6GB de VRAM, é indispensável reservar entre 1.0 GB e 1.5 GB exclusivamente para o contexto dinâmico (KV Cache unificado de 16k a 32k tokens) e para os buffers operacionais do CUDA ou Vulkan. Consequentemente, a restrição de hardware impõe um teto físico: nenhum arquivo GGUF pode ultrapassar 5.0 GB no disco, situando a faixa ideal de operação entre 1.0 GB e 4.5 GB.

## 1. A Destilação de Fronteira: Os Filhos dos Gigantes

A destilação de conhecimento a partir de modelos de grande escala permitiu que modelos compactos herdem capacidades avançadas de raciocínio lógico, depuração de código e planejamento agêntico, eliminando o custo computacional associado aos parâmetros originais.

### DeepSeek-R1-Distill-Qwen-1.5B

O `DeepSeek-R1-Distill-Qwen-1.5B-Q8_0.gguf` (com tamanho aproximado de 1.89 GB) e sua alternativa `Q6_K_L` (1.58 GB) representam a integração do raciocínio encadeado (_Chain-of-Thought_ - CoT) do modelo DeepSeek-R1 na base do Qwen 2.5 1.5B. O modelo é uma escolha estratégica para ambientes de borda porque ocupa menos de 2.0 GB de VRAM na quantização Q8_0, permitindo a alocação completa dos pesos na memória da RTX 2060m e atingindo taxas de geração superiores a 120 tokens por segundo. O mecanismo de inferência preserva a integridade das etapas lógicas intermediárias sem estourar a memória durante a fase de _prefill_. Na comunidade de desenvolvedores e em repositórios de benchmarking, este modelo demonstrou superar sistemas densos de 7B de gerações anteriores em tarefas de raciocínio sintático e matemático. A estabilidade da quantização Q8_0 garante que as tags `<think>...</think>` sejam geradas sem corrupção, permitindo que o parser do SODA isole o raciocínio interno da resposta final enviada ao sistema.

### Arch-Function-Chat-3B

O `Arch-Function-Chat-3B-Q5_K_M.gguf`, com peso físico de 2.10 GB, foi desenvolvido especificamente para gateways de comunicação e orquestração agêntica via _Model Context Protocol_ (MCP). A limitação de muitos modelos conversacionais reside no disparo impulsivo de chamadas de função com parâmetros incompletos. O Arch-Function-Chat resolve essa falha ao ser treinado para realizar chamadas funcionais conversacionais: quando uma requisição do usuário apresenta ambiguidade, o modelo inicia um diálogo de alinhamento com o usuário antes de emitir a chamada em formato JSON. Avaliações no _Goose local framework_ indicam que o modelo atinge taxas de precisão na invocação de esquemas funcionais comparáveis a modelos de 14B a 70B, mantendo conformidade estrutural em invocações paralelas.

### Phi-4-mini-instruct

O `Phi-4-mini-instruct-Q4_K_M.gguf`, ocupando 2.45 GB de espaço em disco e com 3.8 bilhões de parâmetros, resulta da destilação de pipelines sintéticos da arquitetura Phi-4. O modelo utiliza dados focados em raciocínio matemático, pseudocódigo e estruturação lógica. Por ser um modelo denso altamente refinado, apresenta baixas taxas de alucinação ao processar _prompts_ restritivos para validação de esquemas JSON e construção de _Abstract Syntax Trees_ (AST) no backend Rust. Registros do _Hugging Face Daily Trending_ e do _r/LocalLLaMA_ confirmam que o Phi-4-mini supera grande parte dos modelos tradicionais de 7B e 8B em tarefas de cumprimento de instruções técnicas.

|**Modelo**|**Parâmetros**|**Quantização**|**Tamanho GGUF**|**VRAM Estimada (Com KV Cache)**|**Função no SODA**|
|---|---|---|---|---|---|
|**DeepSeek-R1-Distill-1.5B**|1.78B|Q8_0|1.89 GB|~2.6 GB|Raciocínio Encadeado (CoT)|
|**Arch-Function-Chat-3B**|3.0B|Q5_K_M|2.10 GB|~3.0 GB|Validação de Parâmetros e MCP|
|**Phi-4-mini-instruct**|3.8B|Q4_K_M|2.45 GB|~3.4 GB|Lógica de AST e Validação JSON|

## 2. Arquiteturas Híbridas e Novas Topologias (Anti-Amnésia)

A degradação contextual em sequências longas (_Lost-in-the-Middle_) compromete o desempenho de Transformers densos tradicionais. Arquiteturas baseadas em _State Space Models_ (SSM), Mamba e atenção linear mitigam esse problema ao substituir a complexidade quadrática do mecanismo de atenção por estados contínuos, preservando a coerência do agente em janelas estendidas.

### Zamba2-2.7B-Instruct-v2

O `Zamba2-2.7B-Instruct-v2-Q4_0.gguf` (2.10 GB) e sua versão `Q6_K` (2.80 GB) utilizam uma topologia híbrida que intercala blocos Mamba-2 com camadas de atenção global compartilhada (_Shared Attention_). Essa estrutura permite manter um estado oculto compacto durante o processamento do histórico de ações. Em comparação com o crescimento quadrático de Transformers convencionais, a alocação de KV Cache do Zamba2 cresce de forma reduzida, evitando a perda de desempenho no meio do contexto. Testes realizados no _r/LocalLLaMA_ confirmam que a variante de 2.7B alcança altas velocidades de geração em GPUs de entrada, mantendo a fidelidade às instruções originais em fluxos agênticos de longa duração.

### Falcon-Mamba-7B-Instruct

O `falcon-mamba-7b-instruct-IQ3_M.gguf`, com peso físico de 3.80 GB, adota uma arquitetura baseada inteiramente em SSM (_Attention-Free State Space Model_). A ausência de matrizes de atenção tradicionais faz com que o consumo de memória do estado interno permaneça praticamente constante, independentemente do volume de tokens na janela de contexto. A quantização `IQ3_M` reduz a pegada em disco para menos de 4.0 GB, permitindo a execução de um modelo de 7 bilhões de parâmetros dentro do orçamento de 6GB da GPU. A integração do modelo no ecossistema `llama.cpp` demonstra imunidade ao efeito _Context Rot_, viabilizando a leitura contínua de documentações e arquivos de log sem perda de contexto.

|**Modelo**|**Topologia de Rede**|**Quantização**|**Tamanho GGUF**|**Comportamento do KV Cache**|**Aplicação Agêntica**|
|---|---|---|---|---|---|
|**Zamba2-2.7B-Instruct-v2**|Híbrida (Mamba-2 + Shared Attention)|Q4_0|2.10 GB|Sub-Quadrático|Agentes de Ciclo Longo|
|**Falcon-Mamba-7B-Instruct**|SSM Puro (Attention-Free)|IQ3_M|3.80 GB|Praticamente Constante|Leitura de Logs e Documentação Extensa|

## 3. O Eixo de Design (Generative UI e Spatial Awareness) vs. Coding Puro

O SODA estabelece uma separação funcional entre o agente encarregado da lógica de backend (Rust e estruturas de dados) e o agente responsável pela interface de usuário. O agente de interface necessita de modelos treinados para compreender a hierarquia do DOM, interpretar mapas espaciais e gerar código declarativo (Svelte 5, Tailwind CSS) sem introduzir inconsistências visuais.

### Microsoft Fara-7B

O `microsoft_Fara-7B-GGUF-IQ3_M.gguf` (3.90 GB), operando em conjunto com o arquivo projetor multimodal `mmproj-f16.gguf`, foi projetado pela Microsoft para automação de navegadores e navegação em interfaces digitais. O modelo foi treinado com capturas de tela, mapas de acessibilidade e estruturas hierárquicas de DOM, o que lhe confere a capacidade de associar elementos visuais a marcações de código. Em fluxos de _Generative UI_, o Fara-7B analisa interfaces existentes e gera alterações estruturadas mantendo a integridade das regras de estilo. O modelo registra altos índices de precisão nos benchmarks _WebArena_ e _webeval_, demonstrando robustez no processamento de aplicações web complexas.

### UI-TARS-1.5-7B

O `UI-TARS-1.5-7B-GGUF-IQ3_M.gguf`, com tamanho físico de 3.85 GB, é especializado em percepção de interfaces gráficas (GUI) e automação de layouts. O modelo converte instruções em linguagem natural diretamente em mapeamentos espaciais, caixas delimitadoras (_bounding boxes_) e código de estilização. Dentro da arquitetura do SODA, o UI-TARS atua na interpretação de mockups e na geração de classes CSS/Tailwind correspondentes. Avaliações no benchmark _OSWorld_ e relatos da comunidade destacam sua precisão no alinhamento de componentes visuais e na identificação da hierarquia de elementos na tela.

|**Modelo**|**Capacidade Multimodal**|**Quantização**|**Tamanho GGUF**|**Entrada Primária**|**Saída Alvo no SODA**|
|---|---|---|---|---|---|
|**Microsoft Fara-7B**|Sim (Visão + Texto)|IQ3_M|3.90 GB|DOM Tree + Screenshots|Componentes HTML/Svelte 5|
|**UI-TARS-1.5-7B**|Sim (Visão + Texto)|IQ3_M|3.85 GB|Coordenadas de Tela + Prompts|Mapeamento Espacial e Tailwind CSS|

## 4. O Tier 0: Micro-Modelos para Roteamento, Triagem e Classificação

Para otimizar o uso da GPU e reduzir a latência, o SODA utiliza uma camada primária de triagem. O Tier 0 consiste em micro-modelos com contagem reduzida de parâmetros ou quantizações ternárias de 1.58-bit que interceptam comandos, determinam a intenção do usuário e acionam o agente especializado adequado.

### BitNet b1.58 2B4T

O `bitnet_b1_58-2B4T-tri.gguf` (680 MB) utiliza uma arquitetura baseada em pesos ternários pertencentes ao conjunto $\{-1, 0, +1\}$. Essa abordagem substitui operações de ponto flutuante (_FP16/FP32_) por adições e subtrações inteiras. Com consumo inferior a 700 MB de VRAM, o modelo alcança taxas de processamento (_prefill_) superiores a 400 tokens por segundo na RTX 2060m, atuando como um roteador de baixa latência. Benchmarks da Microsoft Research indicam que o BitNet b1.58 mantém a precisão de classificação de modelos densos de tamanho equivalente, reduzindo o consumo energético da GPU em até 80%.

### SmolLM2-360M-Instruct

O `SmolLM2-360M-Instruct-Q8_0.gguf`, com 360 milhões de parâmetros e tamanho de 380 MB, foi treinado sobre dados sintéticos voltados para o cumprimento de instruções curtas e tarefas de classificação. Devido às suas dimensões, o modelo pode permanecer residente na VRAM ou na RAM do sistema sem comprometer o orçamento de memória. Com latência de resposta reduzida, funciona como um classificador condicional de alta velocidade para o motor de inferência em Rust.

|**Modelo**|**Arquitetura / Precisão**|**Quantização**|**Tamanho GGUF**|**Latência Média**|**Função Principal no SODA**|
|---|---|---|---|---|---|
|**BitNet b1.58 2B4T**|Ternária (1.58-bit: $\{-1, 0, +1\}$)|Native Tri-Bit|0.68 GB|< 10 ms|Roteamento e Classificação de Intenção|
|**SmolLM2-360M-Instruct**|Transformer Denso Ultra-Compacto|Q8_0|0.38 GB|< 15 ms|Interceptador de Comandos e Parser|

## 5. Matriz Comparativa de Desempenho e Alocação de Memória

A tabela a seguir apresenta a síntese dos modelos selecionados, detalhando o consumo de memória em disco, a estimativa de alocação de VRAM e a viabilidade operacional na GPU NVIDIA GeForce RTX 2060 Mobile sob o motor de inferência em Rust (`llama-cpp-2` / `mistral.rs`).

|**Nome do Modelo**|**Eixo de Aplicação**|**Parâmetros**|**Quantização**|**Tamanho GGUF**|**Consumo Total VRAM (Pesos + Cache KV)**|**Viabilidade na RTX 2060m (6GB)**|
|---|---|---|---|---|---|---|
|**DeepSeek-R1-Distill-Qwen-1.5B**|Destilação / CoT|1.78B|Q8_0|1.89 GB|~2.6 GB|**Ideal** (Alocação Total na VRAM)|
|**Arch-Function-Chat-3B**|Chamada de Ferramentas / MCP|3.0B|Q5_K_M|2.10 GB|~3.0 GB|**Excelente**<br><br>[cite: 11]|
|**Phi-4-mini-instruct**|Lógica / Código / AST|3.8B|Q4_K_M|2.45 GB|~3.4 GB|**Excelente**<br><br>[cite: 10, 12]|
|**Zamba2-2.7B-Instruct-v2**|Híbrido SSM / Anti-Amnésia|2.7B|Q4_0|2.10 GB|~2.8 GB|**Excelente** (Baixo consumo KV)|
|**Falcon-Mamba-7B-Instruct**|SSM Puro / Contexto Longo|7.0B|IQ3_M|3.80 GB|~4.6 GB|**Viável** (KV Cache Constante)|
|**Microsoft Fara-7B**|Generative UI / Spatial DOM|7.0B|IQ3_M|3.90 GB|~5.1 GB|**Viável** (Dentro do limite do buffer)|
|**UI-TARS-1.5-7B**|Spatial Awareness / GUI|7.0B|IQ3_M|3.85 GB|~5.0 GB|**Viável**<br><br>[cite: 22]|
|**BitNet b1.58 2B4T**|Tier 0 / Roteamento Ternário|2.0B|Ternária|0.68 GB|~1.1 GB|**Ideal** (Permanente na VRAM)|
|**SmolLM2-360M-Instruct**|Tier 0 / Triagem Rápida|0.36B|Q8_0|0.38 GB|~0.7 GB|**Ideal** (Residente na RAM/VRAM)|

## 6. Considerações da Arquitetura de Orquestração

A implantação do Sistema Operacional Agêntico no ambiente restrito da RTX 2060m deve seguir uma estratégia de orquestração modular baseada no revezamento dinâmico de papéis.

O micro-modelo `BitNet b1.58 2B4T` deve ser mantido de forma residente na memória, realizando a triagem inicial das requisições em milissegundos sem sobrecarregar a GPU. Confirmada a necessidade de raciocínio encadeado ou chamadas de função estruturadas, o SODA direciona a execução para o `DeepSeek-R1-Distill-1.5B` ou para o `Arch-Function-Chat-3B`. Quando o fluxo exigir o processamento de longos históricos de logs ou documentação extensa, o motor aciona as arquiteturas do `Zamba2-2.7B` ou do `Falcon-Mamba-7B`, que minimizam o crescimento do KV Cache. Por fim, tarefas de construção de interfaces visuais são delegadas ao `Microsoft Fara-7B` ou ao `UI-TARS-1.5-7B`, utilizando seus respectivos projetores multimodais para garantir precisão espacial na geração de código.

Com essa abordagem desacoplada e o uso de quantizações otimizadas (`Q8_0`, `Q4_K_M`, `IQ3_M` e Ternária), o SODA mantém sua eficiência operacional na borda, operando dentro do limite de 5.0 GB de peso físico e assegurando a estabilidade da VRAM.