# SODA CORE MANIFESTO: The Sovereign OODA Agent

> **Contexto:** Este documento serve como "Genoma" do projeto. Ele deve ser lido por Agentes de Pesquisa para entender *quem* somos e *o que* buscamos, garantindo que novas ferramentas (MCPs, Tools) sejam selecionadas não por hype, mas por alinhamento filosófico.

## 1. MOONSHOT: A Linha de Montagem Cognitiva
Não estamos construindo um "Chatbot". Estamos construindo um **Sistema Operacional para Neurodivergentes (2e: Gifted + ADHD)**.
- **O Problema:** O usuário tem "Cérebro de Ferrari, Freio de Bicicleta". Alta capacidade de resolução (High IQ), baixa capacidade de execução linear (Executive Dysfunction).
- **A Solução:** SODA (Sovereign OODA Driven Agent). Um framework que externaliza a Função Executiva. Criando um agente pronto para uso com todos as melhores práticas, ferramentas e capacidades inovativas refinadas em seu diretório `.agent` totalmente transferível para novos workspaces!
- **A Metáfora:** Transformar o desenvolvimento de software de uma "Arte Caótica" ("Vibe Coding") em um "Processo Industrial Determinístico". Transformando o processo de excelência industrial em um processo de `/workflows` lógicos que utilizam todas as melhores práticas, agentes, personas, skills, "tools" e mcps, por meio de um elegante gateway definido pela persona que busca atuar por meio de identificação de necessidade.

## 2. FILOSOFIA & AXIOMAS
1.  **Soberania (Sovereign):**
    - Nada de SaaS obrigatório. O sistema deve rodar offline se necessário.
    - Dados locais. Memória local. Controle total.
2.  **Clean Root Axiom:**
    - A raiz do projeto é sagrada. Apenas `src/` e `PROJECT_CHARTER.md` vivem lá.
    - Toda a sujeira do agente (`logs`, `memory`, `scripts`) vive oculta em `.agent/`.
3.  **Spec-Oriented (Spec-Lock):**
    - Nenhuma linha de código é escrita sem um Spec (Gherkin/Markdown) aprovado antes.
    - Pensar (Planning) e Fazer (Coding) são fases distintas e bloqueantes.
4.  **Hardware Independence:**
    - O sistema roda em uma batata ou em um cluster H100.
    - *Hoje:* Cloud (Gemini) + CPU (Python Agent).
    - *Amanhã:* Local GPU (Ollama) + NPU.

## 3. A MÁQUINA (Estrutura Atual v0.3)
O SODA é composto por órgãos vitais:

### A. O Cérebro (Intelligence Layer)
- **Híbrido:** Gemini 3 (Cloud) para raciocínio complexo + Qwen/Phi4 (Local) para tarefas rápidas/privadas.
- **Implementação:** `LiteLLMModel` agnóstico.

### B. O Motor (Execution Layer)
- **Nome:** Ralph (baseado em `snarktank/ralph`).
- **Tech:** HuggingFace `smolagents` (`CodeAgent`).
- **Natureza:** Um loop Python que:
    1. Lê o Plano (`task_plan.md`).
    2. Escreve código Python para resolver o próximo passo.
    3. Executa, valida e commita.
    4. Limpa a memória RAM (Fresh Context) e reinicia.

### C. A Memória (SUMA - Unified Memory Arch)
- **Hot (Mesa de Trabalho):** `task_plan.md`, `findings.md` (Markdown simples).
- **Warm (Specs):** OpenSpec (Estruturado).
- **Cold (Arquivo Morto):** OpenMemory (GraphDB via Docker/MCP).

### D. Os Sentidos (Tools & MCPs)
- **Olhos:** Tavily (Web Search) + `read_file` (FileSystem).
- **Mãos:** `write_file` (FileSystem) + `run_command` (Terminal).
- **Expansão (Futura):** MCP Server Gateway para conectar ferramentas externas (Slack, Github, Jira, Database).

## 4. O CRITÉRIO DE SELEÇÃO (Para Pesquisa)
Ao pesquisar novas ferramentas para o SODA, o Agente deve perguntar:
1.  **É Soberana?** Posso rodar local/self-hosted?
2.  **É "Clean"?** Ela polui a raiz ou consigo isolá-la em `.agent/`?
3.  **É Determinística?** Ela reduz o caos ou adiciona "magia negra" imprevisível?
4.  **É "Smol"?** Ela é leve e focada (Unix Philosophy) ou é um monólito bloatware?

> **Meta Final:** Um sistema onde eu (Humano) defino o "O Que" (Spec), e o SODA executa o "Como" (Code), permitindo que eu foque na Arquitetura e Refinamento, e não na Sintaxe ou melhores práticas toda hora precisando serem reforçadas! Um sistema que auto-evolui com você.
