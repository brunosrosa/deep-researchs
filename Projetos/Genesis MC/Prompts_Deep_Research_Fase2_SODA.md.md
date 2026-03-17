# Protocolo de Pesquisa Profunda Fase 2: O "Córtex" do SODA

_Instruções de Uso:_ Copie e cole o conteúdo integral de cada Bloco (incluindo o Novo Contexto Mestre) em uma sessão LIMPA e NOVA de uma ferramenta de Deep Research (Gemini Advanced, ChatGPT Pro, etc).

## 🔍 PROMPT 1: VETOR ZETA (Arquitetura e Orquestração de MCPs)

**[COPIE A PARTIR DAQUI]**

ATUE COMO UM ARQUITETO DE INTEGRAÇÕES E ESPECIALISTA EM AGENTES LLM.

**NOVO CONTEXTO MESTRE (PROJETO GENESIS MC - SODA):**

O SODA é um Sistema Operacional Agêntico local em Rust (i9, 32GB RAM, RTX 2060m 6GB limitados pelo uso de monitor Ultrawide). Para poupar o hardware local de tarefas impossíveis, o sistema operará num modelo **Híbrido**. Para integrar o SODA com o mundo externo, o Model Context Protocol (MCP) e integrações CLI são a espinha dorsal.

**A MISSÃO DE PESQUISA (VETOR ZETA - MCP E FERRAMENTAS):**

O mercado de MCPs explodiu, mas o uso prático é caótico. Preciso de uma análise profunda sobre como o Maestro (roteador em Rust) deve orquestrar ferramentas.

1. **Opções Essenciais vs. Hype:** Mapeie os MCPs essenciais (para filesystem, buscas, git, etc.) e as melhores alternativas open-source gratuitas que substituem MCPs famosos/pagos.
2. **Descoberta "On-The-Go" e Cold Starts:** Como o sistema pode descobrir ou invocar MCPs sob demanda sem sofrer o gargalo do "Cold Start" (tempo de subida do servidor Node/Python do MCP)? Pesquise estratégias de Cache de instâncias (como ferramentas tipo `mcp2cli` atuam).
3. **Prevenção de Context Rot com Ferramentas Massivas:** Se o agente tiver 50 ferramentas à disposição, o prompt de sistema ficará obeso. Como implementar abstrações de contexto tardio (onde o agente só vê a ferramenta e sua sintaxe de uso no momento em que "decide" precisar de uma categoria específica)?

**FORMATO DE SAÍDA EXIGIDO:**

Documento Markdown contendo:

1. **O Padrão Ouro de Uso de MCP:** Como orquestrar 50+ ferramentas sem poluir o LLM.
2. **Análise de Soluções como `mcp2cli`:** O que elas resolvem e se devemos absorver a ideia em Rust.
3. **Lista Curada de MCPs Táticos:** Dividida por utilidade (com opções gratuitas destacadas).
4. **Arquitetura de Warm-Cache:** Como driblar o tempo de inicialização de MCPs lentos.

**[FIM DA CÓPIA]**

## 🔍 PROMPT 2: VETOR ETA (Modelos de Linguagem Recursivos - RLM)

**[COPIE A PARTIR DAQUI]**

ATUE COMO UM PESQUISADOR DE VANGUARDA EM MACHINE LEARNING E ENGENHARIA DE PROMPTS.

**NOVO CONTEXTO MESTRE (PROJETO GENESIS MC - SODA):**

Nosso hardware local é severamente restrito em VRAM (RTX 2060m compartilhando espaço com um monitor Ultrawide, sobrando ~4.5GB úteis). Não podemos rodar modelos gigantes (como 70B) localmente.

**A MISSÃO DE PESQUISA (VETOR ETA - RECURSIVE LANGUAGE MODELS):**

A arquitetura de Modelos de Linguagem Recursivos (RLM) promete fazer modelos pequenos alcançarem benchmarks alienígenas ao permitir que chamem a si mesmos (ou outros SLMs menores) de forma iterativa, processando contextos infinitos e contornando a degradação da memória.

Pesquise o estado da arte dos RLMs (analisando referências como `fullstackwebdev/rlm_repl`, `ysz/recursive-llm`, e papers de Alex Zhang sobre `rlm`).

1. **A Mecânica RLM:** Como exatamente funciona o loop recursivo na prática (diferente de um simples framework ReAct)?
2. **Integração no Edge (SODA):** Como eu injeto um motor RLM no meu daemon em Rust? Quando o sistema decide autonomamente usar um RLM em vez de um disparo _zero-shot_ comum?
3. **Mitigação de Context Rot:** Como a recursão ajuda a lidar com o decaimento de contexto em bases de código legadas gigantescas?

**FORMATO DE SAÍDA EXIGIDO:**

Documento Markdown contendo:

1. **Síntese Teórica:** O que é RLM e por que muda o jogo para hardwares limitados (Edge).
2. **Análise de Repositórios de Vanguarda:** Pontos fortes e lógicas a serem canibalizadas dos projetos listados.
3. **Diagrama Lógico de Decisão:** Um fluxograma textual de "Quando e Como" o Maestro SODA deve invocar o modo RLM automaticamente.

**[FIM DA CÓPIA]**

## 🔍 PROMPT 3: VETOR THETA (Cartografia e Roteamento de LLMs)

**[COPIE A PARTIR DAQUI]**

ATUE COMO UM ARQUITETO DE SOLUÇÕES DE IA HÍBRIDA (FINOPS & MLOPS).

**NOVO CONTEXTO MESTRE (PROJETO GENESIS MC - SODA):**

Nosso hardware local: i9 9th Gen, 32GB RAM, RTX 2060m (4.5GB VRAM úteis devido ao monitor Ultrawide), iGPU Intel.

Estratégia: Orquestração Híbrida. Processamento passivo (background) durante _idle_ e roteamento inteligente (Maestro) para a Nuvem quando a tarefa for complexa demais para a VRAM restrita.

**A MISSÃO DE PESQUISA (VETOR THETA - MAPA DE MODELOS E GATEWAY):**

Preciso de um mapeamento definitivo das opções mais atuais de IA, dividindo-as por capacidade de "encaixe" no meu hardware e função de negócio.

Analise os seguintes modelos e sugira outras opções emergentes fundamentadas:

- **Locais Multimodais/Base:** Qwen 3.5 (4B/9B), Ministral 3 (8B), etc.
- **Locais de Raciocínio/Código:** DeepSeek-R1-Distill-Qwen (7B/14B), Phi-4-mini / reasoning, Rnj-1.
- **Locais para Tool Calling Específico:** FunctionGemma 3 (270M).
- **Locais Multimodais (Voz/Visão):** Whisper-MLX, Nvidia Parakeet, granite-4.8.1b-speech, GLM-OCR (0.9b).
- **Nuvem ("Brain" & Heavy Duty):** Claude 4.6 Opus/Sonnet, Gemini 3.1 Pro, ChatGPT 5.4, Kimi K2.5, MiniMax M2.5.

**FORMATO DE SAÍDA EXIGIDO:**

Documento Markdown contendo:

1. **A Tabela Cartográfica de Modelos:** Para cada modelo, listar: Categoria, Tamanho estimado na VRAM (em quantização Q4_K_M), Forças, Fraquezas e "Quando Usar no SODA".
2. **O Motor de Regras de Negócio (Roteamento):** Crie a lógica heurística (árvore de decisão) que o Maestro SODA usará para decidir dinamicamente se a instrução do usuário roda na iGPU (Gemma), na RTX (Qwen/Phi) guardando para o momento _idle_ do PC, ou se gasta centavos despachando para o Claude/Gemini na Nuvem.

**[FIM DA CÓPIA]**

## 🔍 PROMPT 4: VETOR IOTA (Engenharia Enterprise - NemoClaw e Ecossistema NVIDIA)

**[COPIE A PARTIR DAQUI]**

ATUE COMO UM ENGENHEIRO REVERSO DE SOFTWARE E ARQUITETO DE PLATAFORMAS ENTERPRISE.

**NOVO CONTEXTO MESTRE (PROJETO GENESIS MC - SODA):**

Estou construindo o Genesis MC (Sistema Operacional Agêntico Local em Rust). Uma de nossas fundações teóricas de orquestração derivou do projeto OpenClaw/ZeroClaw.

**A MISSÃO DE PESQUISA (VETOR IOTA - DISSECAÇÃO DO NEMOCLAW):**

A NVIDIA lançou recentemente o **NemoClaw** (aparentemente um "enterprise level" apoiado no OpenClaw), juntamente com o `NeMo-Agent-Toolkit`, `OpenShell`, e `TransformerEngine`.

Preciso de uma análise profunda (Engenharia Reversa Teórica) sobre o que a NVIDIA alterou, melhorou ou adicionou em cima da base do OpenClaw.

1. O que o NemoClaw traz de inovações estruturais em orquestração de agentes e segurança de contexto corporativo (como NeMo Guardrails)?
2. Como o `NeMo-Agent-Toolkit` e o `OpenShell` operam a vinculação de ferramentas no nível do SO de forma segura?
3. O `TransformerEngine` possui alguma abstração que possa ser "canibalizada" para otimizar nossa inferência local na RTX 2060m?

**FORMATO DE SAÍDA EXIGIDO:**

Documento Markdown contendo:

1. **Dissecação do NemoClaw:** Quais foram os _upgrades_ arquiteturais exatos que a NVIDIA fez sobre o OpenClaw original.
2. **Análise de Toolkit e Shell:** Como a NVIDIA gerencia o acesso das IAs ao terminal do host de forma não destrutiva.
3. **Plano de Canibalização:** O que o projeto SODA deve copiar, roubar (metodologicamente) ou se inspirar desses 4 repositórios da NVIDIA para alcançar confiabilidade de nível corporativo na máquina pessoal do usuário.

**[FIM DA CÓPIA]**