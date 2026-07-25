---
aliases: []
---

# Dossiê Técnico Arena SODA: Avaliação Contínua, Benchmarking Local e Roteamento Dinâmico de SLMs em Hardware Restrito

## Os Índices de Avaliação (O Que Medir)

A construção de um pipeline de benchmarking local e automatizado ("Arena SODA") para avaliar mais de 30 modelos de linguagem de pequena e média escala (SLMs e LLMs com parâmetros entre 1B e 32B em formato GGUF) operando em hardware restrito (NVIDIA RTX 2060m com 6GB de VRAM) exige uma revisão crítica das métricas convencionais. A literatura e a prática de vanguarda de 2025/2026 demonstram que a dependência de avaliadores estocásticos ou de benchmarks estáticos induz a conclusões erróneas devido a contaminação de dados, variação de avaliadores e abstrações desnecessárias.

### Análise Crítica das Premissas e Estado da Arte dos Benchmarks

A seleção de benchmarks para validação de modelos locais deve ser rigorosamente segmentada por capacidade cognitiva e funcional, evitando a ilusão do _overfitting_ promovida por conjuntos de dados estáticos contaminados.

|**Eixo de Avaliação**|**Benchmark Recomendado**|**Métrica Principal**|**Tipo de Avaliação**|**Papel na Arena SODA**|
|---|---|---|---|---|
|**Agêntico & Tool-Use**|BFCL v4 Agentic|AST Accuracy & State Transition Success|Execução Sintática e Estado Determinístico|Avaliar chamadas de funções simples, paralelas e multi-turno com recuperação de erro.|
|**Engenharia de Software**|SWE-bench Verified|Pass@1 (Resolução de Issues GitHub)|Execução de Testes Unitários em Sandbox|Validação real de refatoração e resolução de bugs em código Python.|
|**Raciocínio Científico**|SciCode|Subproblem Pass Rate (Docker Sandbox)|Comparação de Matrizes e Saídas Numéricas|Medir capacidade de raciocínio lógico-matemático acoplado a código científico.|
|**Inteligência Fluida**|ARC-AGI-2|Accuracy (Exact Grid Transformation)|Validação Determinística de Matrizes JSON|Avaliar síntese de programas e indução de regras sem contaminação de treino.|
|**Adesão Estrutural**|JSONSchemaBench|Schema Validity Rate & Token-Mask Accuracy|Validação via Parser JSON e Máscara de Logits|Testar conformidade em relação a esquemas complexos e aninhados.|
|**Resistência a Context Rot**|BABILong|Retrieval & Reasoning Accuracy vs Distance|Extração de Agulha em Contexto Extenso|Medir a degradação de atenção no meio da janela de contexto (_Lost in the Middle_).|

### Validação de Agêntica e Tool-Use: A Refutação do LLM-as-a-Judge

A premissa inicial de incorporar abordagens como JudgeBench ou benchmarks baseados exclusivamente em avaliadores estocásticos (_LLM-as-a-Judge_) é refutada pela literatura recente. Auditorias de validade e reprodutibilidade revelam que julgamentos via LLM introduzem taxa de desalinhamento de até 18,5% devido a deriva de rubrica, alucinação de conclusão e variância corrida-a-corrida. Em cenários como o LiveMCPBench, avaliações repetidas da mesma configuração geram flutuações de até 18,9 pontos percentuais na pontuação final, alterando arbitrariamente a liderança dos modelos.

A alternativa adotada na vanguarda é a avaliação determinística baseada na execução e na validação da Árvore de Sintaxe Abstrata (AST). O Berkeley Function Calling Leaderboard (BFCL v4 Agentic) consolida-se como o padrão executável por verificar rigorosamente se os nomes das funções, tipos de argumentos e estruturas de dados gerados correspondem à especificação da API, além de avaliar fluxos de múltiplos turnos, gerenciamento de memória e recuperação de erros em chamadas encadeadas.

### O Paradoxo da Decodificação Restrita e Adesão a JSON

Em sistemas de produção local, a adesão estrita a JSON e gramáticas BNF costuma ser forçada na camada de inferência via _constrained decoding_ usando motores como XGrammar, LLGuidance, Outlines ou GBNF (`llama.cpp`). Motores modernos como XGrammar e Guidance pré-computam máscaras de tokens válidos e reduzem a latência por token para menos de 40 a 50 microssegundos, permitindo conformidade de 100% no formato da resposta.

Contudo, a comunidade científica documentou o "Paradoxo da Decodificação Restrita": a imposição rígida de máscaras de gramática na amostragem de logits intercepta o processo de raciocínio interno do modelo. Pesquisas do EMNLP demonstram que restrições estritas de formato podem degradar a acurácia de raciocínio em tarefas lógicas e matemáticas em até 27 pontos percentuais. Estudos de engenharia de geração estruturada mostram que certas bibliotecas introduzem erros ao restringir excessivamente ou insuficientemente o espaço de busca de tokens, prejudicando a qualidade semântica do texto gerado.

A avaliação na Arena SODA deve separar a validação de capacidade sintática nativa — medida através do JSONSchemaBench (conjunto de 10.000 esquemas reais da indústria) — da medição do impacto semântico da decodificação guiada, comparando o desempenho dos modelos em geração livre versus geração restrita.

### Cegueira Temporal e Degradação de Contexto (Context Rot)

Modelos locais de menor escala frequentemente apresentam perda substancial de capacidade de recuperação e raciocínio à medida que a janela de contexto se expande, fenômeno conhecido como _Context Rot_ ou degradação do meio de contexto (_Lost in the Middle_). O benchmark BABILong foi projetado especificamente para avaliar essa limitação, inserindo fatos sequenciais necessários para resolução de problemas no meio de documentos ruidosos extremamente longos. A inclusão do BABILong permite quantificar exatamente em qual limiar de tokens (p. ex., 4k, 8k, 12k ou 16k) a retenção atencional do SLM colapsa, fornecendo um parâmetro crítico para a parametrização do orquestrador de inferência.

### Formulação da Métrica de Eficiência Termodinâmica ($E^3$)

Para orientar a decisão de engenharia em ambientes com recursos restritos, a Métrica de Eficiência Termodinâmica de Execução ($E^3$) é formulada para equilibrar rigorosamente o desempenho cognitivo e a pegada física de execução:

$$E^3 = \frac{\mathcal{A}^2}{\text{TTFT} \cdot \text{TPOT} \cdot \left(\frac{\text{VRAM}_{\text{peak}}}{\text{VRAM}_{\text{total}}}\right)}$$

Na equação, $\mathcal{A} \in [0, 1]$ representa a taxa de acurácia normalizada obtida pelo modelo no benchmark alvo; $\text{TTFT}$ (_Time-To-First-Token_) mede a latência da fase de pré-preenchimento (_prefill_) expressa em segundos; $\text{TPOT}$ (_Time-Per-Output-Token_) indica o tempo médio de decodificação por token em segundos; $\text{VRAM}_{\text{peak}}$ é o pico de memória de vídeo alocado durante a inferência (em megabytes); e $\text{VRAM}_{\text{total}}$ é a capacidade utilizável da GPU (6.144 MB para a RTX 2060m). Elevando a acurácia $\mathcal{A}$ ao quadrado, o índice estabelece um compromisso quadrático que penaliza desproporcionalmente modelos ultrarrápidos mas incapazes de produzir saídas corretas, priorizando soluções energeticamente viáveis para implantação _edge_.

## O Trator de Avaliação (Como Medir Localmente?)

A execução automatizada de suítes de testes em lote contra mais de 30 modelos locais exige uma arquitetura de avaliação desmembrada (_decoupled_). A premissa de utilizar frameworks interpretados em Python como DeepEval para testes massivos é inviável no cenário bare-metal devido à alta pegada de memória RAM e overhead de processos subjacentes.

### Avaliação Comparativa de Frameworks de Avaliação Contínua

Para selecionar o motor de testes mais eficiente, analisam-se as ferramentas de código aberto sob os critérios de overhead de sistema, velocidade de execução e facilidade de integração local.

|**Framework**|**Overhead de RAM Hospedeira**|**Mecanismo de Execução**|**Suporte a Endpoints Locais**|**Viabilidade para Arena SODA**|
|---|---|---|---|---|
|**Promptfoo**|Mínimo (~150 MB)|Assíncrono (Node.js/C++)|HTTP REST, stdout/stdin, WebSocket|**Excelente**: Alta taxa de disparo de payloads e baixo consumo de RAM.|
|**DeepEval**|Elevado (~1.2 GB+)|Pytest wrapper em Python|Chamadas HTTP / Wrappers LangChain|**Inadequado**: Consumo excessivo de memória hospedeira e concorrência com o motor de inferência.|
|**LightEval**|Médio (~800 MB)|Subprocessos Python (HuggingFace)|Integration nativa com vLLM, TGI, `llama.cpp`<br><br>[cite: ]|**Recomendado para benchmarks acadêmicos** (ex: SciCode via HuggingFace Datasets).|
|**Rust Native Harness**|Ínfimo (< 30 MB)|Executável binário compilado|FFI nativo, Sockets IPC, HTTP via Tokio|**Ideal para o Orquestrador Central**: Controle absoluto de ciclo de vida e isolamento total.|

Enquanto o Promptfoo sobressai-se para baterias de testes focadas em validação estrutural e assertivas assíncronas por HTTP, o ecossistema para a Arena SODA atinge a máxima eficiência computacional ao implementar um orquestrador central nativo em Rust que gerencia diretamente os processos de inferência e despacha solicitações para servidores locais como o `llama-server` (`llama.cpp`).

### Arquitetura de Isolamento de Recursos e Prevenção de Out-Of-Memory (OOM)

Rodar testes em lote em uma GPU RTX 2060m com 6GB de VRAM impõe restrições severas. A execução contínua sem o devido isolamento leva ao acúmulo de fragmentação na VRAM do driver NVIDIA, resultando em travamentos de kernel CUDA e falhas por OOM. O pipeline de avaliação isola as camadas do sistema através da seguinte estrutura operacional de execução:

1. **Camada de Orquestração (Rust Binary Hospedeiro)**: Funciona como o controlador do ciclo de vida da avaliação. O executável orquestrador em Rust interage com o sistema operativo, monitoriza a utilização da GPU via comandos `NVML` e coordena a alocação de modelos.
2. **Camada de Inferência (`llama-server` isolado por subprocesso)**: O motor de inferência local (`llama.cpp`) é executado como um processo filho individual. Entre cada transição de modelo ou bateria de testes, o orquestrador em Rust encerra o processo com um sinal `SIGTERM`, invocando explicitamente rotinas de desalocação e limpeza de cache antes de inicializar o próximo modelo GGUF.
3. **Mapeamento de Memória (`mmap`) e Offloading Dinâmico**: Ao instanciar modelos GGUF no `llama-server`, o parâmetro de mapeamento de memória deve ser mantido como ativo (`--mmap`), enquanto a contagem de camadas transferidas para a GPU (`--n-gpu-layers` / `-ngl`) é calculada dinamicamente com base no tamanho do modelo, evitando exceder a capacidade física de 6GB da GPU.
4. **Quantização do Cache KV**: Para janelas de contexto estendidas (8k a 16k tokens), o cache de Chave-Valor (KV Cache) em precisão total (FP16) consome volumes proibitivos de VRAM. O motor de inferência deve ser forçado a quantizar o cache KV em 8 bits (`q8_0` ou `q4_0`), garantindo uma economia de até 50% no uso de VRAM de contexto com perda de fidelidade insignificante.
5. **Sandboxing de Execução de Código (Docker Ephemérico versus Firecracker Nativo em Rust)**: Para benchmarks que exigem a execução sintática de código gerado (SWE-bench e SciCode), o código emitido pelo modelo é extraído pelo orquestrador e despachado para contêineres Docker isolados sem acesso à rede (`--net=none`), com limites rígidos de CPU e memória RAM (`--memory=2g`), prevenindo execução de instruções maliciosas ou travamentos do sistema anfitrião.
    
    - **Observação e Dica de Engenharia Bare-Metal (Alternativa Nativa ao Docker via Firecracker)**: Embora o Docker seja a solução tradicional para isolamento, a infraestrutura em Rust obtém ganhos expressivos de performance e eficiência ao substituir o daemon do Docker pelo **Firecracker**. Desenvolvido pela AWS em Rust (~50.000 linhas de código seguro), o Firecracker é um monitor de máquina virtual (_Virtual Machine Monitor_ - VMM) baseado no KVM do Linux que cria MicroVMs ultraleves com isolamento real em nível de hardware.
    - **Vantagens Principais do Firecracker em Ecossistemas Rust**:
        - _Boot de Baixíssima Latência_: O tempo de inicialização (_cold start_) de uma MicroVM Firecracker é de $\le 125\text{ ms}$, com um consumo de memória de apenas $\le 5\text{ MB}$ por instância, comparado aos mais de 100 MB e tempos de resposta mais elevados do _daemon_ do Docker (`dockerd`).
        - _Isolamento de Kernel Real_: Diferente dos contêineres OCI do Docker que compartilham o mesmo kernel do sistema hospedeiro, cada MicroVM possui seu próprio kernel Linux minimalista, eliminando riscos de _container escape_ em execuções de código gerado por LLMs.
        - _Integração Nativa sem Daemons_: O orquestrador em Rust pode gerenciar o ciclo de vida das instâncias via FFI ou crates como `agentic-sandbox` (que possui abstrações nativas como `FirecrackerSandbox` e `GatewaySandbox`), eliminando a necessidade de manter processos de segundo plano pesados no sistema operacional.

## Desidratação de Datasets (A Sobrevivência na RTX 2060m)

Modelos hospedados em placas de vídeo com 6GB de VRAM são incapazes de processar prompts originais de 50k a 200k tokens típicos de conjuntos de dados como o SWE-bench completo sem sofrer degradação catastrófica de velocidade ou falhas por falta de memória. A desidratação de datasets consiste no processo de curadoria, redução estrutural e sumarização sintática de "Golden Datasets" para que estes caibam em janelas de contexto compreendidas entre 8.192 e 16.384 tokens.

### Técnicas de Curadoria e Subconjuntos Disponíveis na Vanguard

A comunidade de IA desenvolveu subsets verificados especificamente projetados para acelerar pesquisas e viabilizar testes em hardware de recursos limitados.

#### Etapas do Pipeline de Desidratação de Datasets na Arena SODA

- **Ingestão dos Datasets Originais**: Importação de conjuntos massivos não-filtrados (SWE-bench Full, SciCode Raw, ARC-AGI-2 Raw, BFCL v4).
- **Processamento e Redução pelo Pipeline SODA**:
    - Análise do grafo de dependências do código via parser AST (Tree-sitter).
    - Poda de contexto automatizada usando busca por relevância sintática (BM25 / Oracle Retrieval).
    - Minificação estrutural (compactação de esquemas JSON e codificação de matrizes).
- **Geração de Datasets Desidratados**: Saída contendo prompts ajustados para a janela de $\le 8k/16k$ tokens, prontos para despacho e execução em ambientes isolados de teste.

#### SWE-bench Verified & SWE-bench Lite

O dataset original do SWE-bench compreende 2.294 tarefas extraídas de repositórios Python complexos, exigindo imagens Docker massivas que originalmente totalizavam até 1,8 TB de armazenamento.

- **SWE-bench Lite**: Seleção de 300 tarefas filtradas para acelerar ciclos de avaliação e mitigar custos computacionais.
- **SWE-bench Verified**: Conjunto de 500 tarefas validadas por especialistas humanos que confirmam a viabilidade da resolução do problema a partir da especificação fornecida.
- **Estratégia de Desidratação de Contexto**: A avaliação de modelos de até 32B no SWE-bench exige a eliminação da injeção de repositórios inteiros no prompt. Utilizando técnicas de _Oracle Retrieval_ ou _BM25 Retrieval_, extrai-se do repositório apenas o arquivo afetado, a classe modificada e as assinaturas de métodos diretamente associados à falha relatada. Esse procedimento reduz a janela de entrada de 100k+ tokens para um limite contido entre 4.096 e 8.192 tokens, preservando a capacidade de teste em SLMs locais.

#### SciCode

O SciCode consiste em 80 problemas científicos complexos desdobrados em 338 subproblemas abrangendo física, matemática, química e ciência dos materiais.

- **Estratégia de Desidratação por Decomposição**: Cada problema do SciCode é estruturado como uma sequência de subproblemas interdependentes. Em vez de alimentar o modelo com o problema completo, a Arena SODA executa a avaliação subproblema por subproblema em modo _step-by-step_. O prompt de cada etapa recebe as informações de contexto necessárias e as saídas das etapas anteriores já validadas, mantendo a janela de tokens comprimida entre 2.048 e 4.096 tokens.

#### ARC-AGI-2

O Abstraction and Reasoning Corpus mede inteligência fluida através de transformações de matrizes de cores.

- **Minificação Sintática**: As matrizes bidimensionais do ARC-AGI-2 são convertidas para uma sintaxe JSON minificada sem formatação de espaços em branco. O consumo de tokens por problema cai para menos de 1.500 tokens, encaixando-se perfeitamente na memória de contexto mais rápida da GPU.

### Matriz de Viabilidade de Quantização e Contexto na RTX 2060m (6GB VRAM)

A alocação de modelos na placa de vídeo NVIDIA RTX 2060m exige a seleção precisa da variante de quantização GGUF para evitar que o processo de inferência faça _fallback_ para a memória RAM do sistema através do barramento PCIe, o que reduz drasticamente a taxa de tokens por segundo.

|**Escala do Modelo**|**Tipo de Quantização GGUF**|**Ocupação de VRAM (Pesos)**|**Limite Recomendado de Contexto (Q8 KV Cache)**|**Throughput Médio Estimado**|**Estado do Offload na GPU**|
|---|---|---|---|---|---|
|**1B a 3B**|Q8_0 / FP16|~1.5 GB - 3.2 GB|16.384 tokens|65 - 110 tok/s|Offload Total (100% Camadas na GPU)|
|**7B a 8B**|Q4_K_M|~4.3 GB|8.192 tokens|35 - 50 tok/s|Offload Total (100% Camadas na GPU)|
|**14B a 16B**|IQ3_M / Q3_K_S|~5.1 GB|4.096 tokens|18 - 28 tok/s|Offload Quase Total (~90% GPU / 10% CPU)|
|**32B**|IQ2_XXS / Q2_K|~5.8 GB|2.048 tokens|3 - 8 tok/s|Offload Parcial (~35% GPU / 65% CPU RAM)|

Modelos na faixa de 32B parâmetros operando sob quantizações severas de 2 bits (como `IQ2_XXS`) perdem precisão semântica e sofrem gargalo severo na transferência de dados pela memória do sistema. No entanto, a sua inclusão na Arena SODA serve como linha de base para testar a capacidade de decisão do orquestrador dinâmico em cenários de alta complexidade e baixíssima velocidade de resposta.

## Mapeamento de Score para Roteamento Dinâmico

A conversão das pontuações brutas obtidas pela Arena SODA em regras de roteamento operacional exige uma formulação matemática que supere abordagens estáticas como tabelas de classificação ELO tradicionais. Sistemas como o ELO ou Bayesian ELO fornecem apenas uma ordenação escalar global, falhando em capturar a viabilidade de um modelo para uma tarefa específica sob restrições instantâneas de tempo e recursos.

### Otimização Baseada em Bandidos Contextuais: O Algoritmo ParetoBandit

A metodologia recomendada na literatura de 2025/2026 para roteamento de modelos de linguagem é a implementação de **Bandidos Contextuais Múltiplos com Ajuste Primal-Dual de Orçamento** (conhecido como _ParetoBandit_). O orquestrador em Rust atua selecionando dinamicamente o melhor modelo (representado como um "braço" $a \in \mathcal{A}$) para cada prompt de entrada $q$, otimizando continuamente a fronteira de Pareto entre qualidade de resposta e custo termodinâmico.

#### 1. Extração do Vetor de Contexto

Para cada solicitação recebida $q$, o orquestrador gera um vetor de características de baixo custo de computação $\mathbf{x}(q) \in \mathbb{R}^d$. Este vetor inclui métricas como a contagem estimada de tokens do prompt, indicadores sintáticos obtidos por parsers leves (detecção de pedidos de código, solicitações estruturadas em JSON ou chamadas de função) e embeddings de baixa dimensão obtidos via modelo de regressão estático na CPU.

#### 2. Equação de Pontuação do Braço (Arm Selection Score)

O orquestrador em Rust calcula a pontuação $s_a(q)$ para cada modelo candidate $a$ combinando a estimativa de qualidade esperada com um limite superior de confiança (LinUCB) penalizado pelo custo de execução:

$$s_a(q) = \hat{\boldsymbol{\theta}}_a^\top \mathbf{x}(q) + \alpha \sqrt{\mathbf{x}(q)^\top \mathbf{A}_a^{-1} \mathbf{x}(q)} - (\lambda_c + \lambda_t) \cdot \tilde{C}_a$$

Onde:

- $\hat{\boldsymbol{\theta}}_a$ é o vetor de parâmetros estimados que mapeia o contexto $\mathbf{x}(q)$ para o desempenho do modelo $a$, aprendido continuamente.
- $\mathbf{A}_a$ representa a matriz de covariância acumulada dos recursos processados pelo modelo $a$.
- $\alpha$ é o coeficiente de exploração que controla o grau de amostragem de modelos menos testados.
- $\tilde{C}_a$ é o custo termodinâmico/latência normalizado do modelo $a$, calculado a partir das métricas coletadas na Arena SODA:

$$\tilde{C}_a = w_1 \cdot \left(\frac{\text{TTFT}_a}{\text{TTFT}_{\max}}\right) + w_2 \cdot \left(\frac{\text{TPOT}_a}{\text{TPOT}_{\max}}\right)$$

- $\lambda_c$ e $\lambda_t$ são multiplicadores de Lagrange dinâmicos ajustados em tempo real para impor tetos de latência e custo por requisição.

#### 3. Adaptação Não-Estatidionária via Esquecimento Geométrico

Devido a atualizações de quantização, alterações de drivers CUDA ou variações nas cargas de trabalho, o desempenho dos modelos pode oscilar. O algoritmo aplica um fator de esquecimento geométrico $\gamma \in (0.99, 0.999)$ sobre as estatísticas acumuladas, garantindo que o roteador priorize evidências de desempenho recentes em detrimento de históricos antigos:

$$\mathbf{A}_a^{(t)} = \gamma \mathbf{A}_a^{(t-1)} + \mathbf{x}(q) \mathbf{x}(q)^\top$$

$$\mathbf{b}_a^{(t)} = \gamma \mathbf{b}_a^{(t-1)} + r(q, a) \mathbf{x}(q)$$

$$\hat{\boldsymbol{\theta}}_a = \left(\mathbf{A}_a^{(t)}\right)^{-1} \mathbf{b}_a^{(t)}$$

Onde $r(q, a) \in [0, 1]$ é o sinal de recompensa (_reward_) obtido após a execução da tarefa. Em tarefas de código ou estruturadas, $r(q, a) = 1.0$ se a saída passar na verificação sintática (AST/Parser) ou nos testes unitários, e $r(q, a) = 0.0$ em caso de falha de execução.

#### 4. Controle em Malha Fechada do Pacer de Orçamento (Budget Pacer)

Para evitar que o sistema ultrapasse os limites de latência aceitáveis para a aplicação, o multiplicador de Lagrange $\lambda_t$ é atualizado a cada transação $t$ por meio de uma regra de otimização primal-dual:

$$\lambda_{t+1} = \max \left( 0, \, \lambda_t + \eta \cdot \left( \frac{\bar{C}_t}{B_{\text{target}}} - 1 \right) \right)$$

Nesta formulação, $\bar{C}_t$ é a média móvel do custo/latência observada nas últimas requisições, $B_{\text{target}}$ é a meta de tempo estipulada pelo operador do sistema e $\eta$ é a taxa de aprendizado do controlador em malha fechada.

### Matriz de Decisão do Roteador para a Arena SODA

A aplicação do algoritmo de roteamento resulta na seleção automatizada de modelos com base nas características detectadas na instrução recebida pelo orquestrador em Rust:

|**Perfil da Instrução Detectada**|**Modelo Recomendado para Invocação**|**Variante GGUF**|**Justificativa do Algoritmo de Roteamento**|
|---|---|---|---|
|**Geração de JSON / API Call Simples**|Qwen-2.5-Coder-3B|Q8_0|Alta capacidade sintática alinhada a um custo $\tilde{C}_a$ negligenciável e baixíssimo TTFT.|
|**Refatoração de Código Complexa / Bug Fix**|DeepSeek-R1-Distill-Qwen-14B|IQ3_M|O ganho em acurácia no SWE-bench compensa a penalidade do fator de latência $\lambda_t \tilde{C}_a$.|
|**Raciocínio Matemático / Raciocínio Científico**|QwQ-32B / Qwen-2.5-32B|IQ2_XXS|Invocado estritamente quando a pontuação $s_a(q)$ de modelos menores é insuficiente para garantir acurácia.|
|**Diálogo Genérico e Conversação**|Llama-3.2-3B|Q8_0|Maximiza o throughput de geração por token sem sobrecarregar a memória de vídeo da GPU.|

## Dossiê Executivo e Recomendações Acionáveis

O estabelecimento da Arena SODA como uma infraestrutura bare-metal de avaliação contínua e roteamento de modelos locais exige a execução sequencial das seguintes etapas operacionais:

1. **Implementação da Infraestrutura do Orquestrador**: Construir o executável controlador em Rust, encarregado de gerenciar o ciclo de vida do `llama-server` (`llama.cpp`), executar chamadas ao subsistema `NVML` para monitoramento de VRAM e isolar a execução de código em MicroVMs Firecracker (ou contêineres Docker efêmeros).
2. **Ingestão e Processamento dos Datasets Desidratados**: Configurar o pipeline para baixar e desidratar as suítes de teste SWE-bench Verified (utilizando curadoria por AST/BM25), SciCode (decomposto por subproblemas), ARC-AGI-2 (JSON minificado) e BFCL v4 Agentic.
3. **Execução Automatizada da Suíte e Cálculo do Índice $E^3$**: Executar as baterias de avaliação nos 30+ modelos locais GGUF, registrando métricas de acurácia, latência de pré-preenchimento ($\text{TTFT}$), tempo por token ($\text{TPOT}$) e pico de VRAM para calcular o Score Termodinâmico $E^3$.
4. **Deploy do Roteador Dinâmico ParetoBandit**: Inicializar os parâmetros do algoritmo de bandidos contextuais em Rust utilizando os resultados coletados pela Arena SODA. Expor um endpoint local unificado que analisa as requisições de entrada e as direciona dinamicamente para o modelo local quantizado que oferece o melhor equilíbrio entre precisão cognitiva e custo computacional.

---

## Planejamento Prático para "Fast Sanity Check"

Para fins de planejamento prático de engenharia, a execução de uma bateria de testes completa para um único modelo de **3B a 6B** em uma RTX 2060m (gerando em média de 50 a 80 tokens por segundo) levará entre **45 minutos e 1 hora e meia**.

Se você optar por uma suíte totalmente exaustiva sem cortes, esse tempo pode subir para **3 a 4 horas por modelo**. Por isso, a técnica de desidratação e amostragem é fundamental para a viabilidade da Arena SODA.

### Quantos testes (prompts) são executados no total?

Uma suíte de avaliação contínua otimizada para execução local não utiliza dezenas de milhares de questões. A estratégia padrão de vanguarda é utilizar um **Golden Subset de 500 a 800 payloads/prompts** criteriosamente selecionados:

|**Categoria do Teste**|**Benchmark**|**Qtd. de Prompts (Subset)**|**Tamanho Médio de Saída**|
|---|---|---|---|
|**Adesão a JSON & Gramática**|JSONSchemaBench|~150 prompts|Curtos (~100-200 tokens)|
|**Tool-Use & Chamada de Função**|BFCL v4 Agentic|~150 prompts|Curtos/Médios (~150-300 tokens)|
|**Inteligência Fluida / Padrões**|ARC-AGI-2 (Minificado)|~100 prompts|Curtos (~100-200 tokens)|
|**Raciocínio e Código Científico**|SciCode (Subproblemas)|~80 subproblemas|Longos (~500-800 tokens de código)|
|**Refatoração & Engenharia**|SWE-bench (Desidratado)|~50 a 100 tarefas|Longos (~500-1000 tokens de código)|
|**Cegueira Temporal / Attention**|BABILong (Context Rot)|~40 prompts (4k a 16k)|Curtos (~50-100 tokens)|
|**TOTAL DA SUÍTE SODA**|—|**~570 a 620 prompts**|—|

### Estimativa de Tempo Discriminada por Tipo de Teste

O tempo total é a soma de três comportamentos de inferência muito diferentes:

#### 1. Testes Estruturais e Agênticos Curtos (JSONSchemaBench, BFCL, ARC-AGI-2)

- **Quantidade:** ~400 prompts.
- **Comportamento:** Prompts médios, respostas muito curtas e diretas.
- **Tempo por prompt:** ~1 segundo para carregar no prefill + ~2 segundos para gerar a resposta = **~3 segundos/prompt**.
- **Tempo Subtotal:** 400 × 3s = **~20 minutos**.

#### 2. Testes Agênticos de Código em Sandbox (SciCode, SWE-bench em MicroVM Firecracker)

- **Quantidade:** ~150 prompts.
- **Comportamento:** O modelo precisa "pensar", gerar o código, o orquestrador em Rust dispara a MicroVM no Firecracker, executa o teste unitário e retorna o resultado para o cálculo da recompensa ($r$).
- **Tempo por prompt:** ~3s de prefill + ~12s de geração + ~0,1s de boot/execução no Firecracker = **~15 a 18 segundos/prompt**.
- **Tempo Subtotal:** 150 × 16s = **~40 minutos**.

#### 3. Testes de Estresse de Contexto Longo (BABILong)

- **Quantidade:** ~40 prompts (com janelas de 4k, 8k, 12k e 16k tokens).
- **Comportamento:** O prompt é gigantesco (prefill pesado na GPU/RAM), mas a resposta é apenas uma frase ou palavra chave ("A agulha no palheiro").
- **Tempo por prompt:** ~15 a 25 segundos apenas processando o prefill do contexto longo na GPU + ~2s de geração = **~20 a 25 segundos/prompt**.
- **Tempo Subtotal:** 40 × 22s = **~15 minutos**.

### Resumo do Tempo Total por Modelo (3B a 6B)

- **Tempo de Inferência Puro:** ~75 minutos.
- **Overhead do Orquestrador Rust:** ~3 a 5 minutos (troca de subprocesso `llama-server`, coleta do NVML, inicialização de memória).
- **TEMPO TOTAL ESTIMADO:** **~1 hora e 20 minutos por modelo**.

### Dica Prática de Planejamento: Arquitetura de Duas Etapas (Tiers)

Para não esperar 40 horas ao testar 30 modelos de uma vez só, a melhor prática de engenharia é dividir o pipeline da Arena SODA em dois "Tiers":

1. **Tier 1 — Fast Sanity Check (Smoke Test de ~8 minutos por modelo):**
    
    - Roda apenas **50 prompts** estratégicos (20 de JSON, 15 de Tool-Use, 15 de código simples).
    - **Objetivo:** Eliminar imediatamente modelos incompetentes ou que quebraram a formatação nativa antes de gastar tempo da GPU. Se o modelo tirar nota baixa aqui, o orquestrador descarta o modelo sem rodar o restante.
        
2. **Tier 2 — Full SODA Benchmark (~1h20m por modelo):**
    
    - Executa a bateria completa de ~600 prompts apenas nos modelos que passaram no Tier 1.
    - Alimenta os dados precisos para a matriz do algoritmo de roteamento dinâmico (_ParetoBandit_) e calcula a Métrica Termodinâmica $E^3$.