---
sticker: lucide//baseline
---
# S.O.D.A. v1.8 - Sistema Operacional de Desenvolvimento Agêntico
**(Spec-Oriented Dockerized Architecture - The "Hardware Agnostic" Enterprise Edition)**

**Versão:** 1.8 (Definitive Master)
**Status:** Gold Standard (Production Ready)
**Target Runtime:** Google Antigravity (WSL2)
**Constraint de Hardware:** **Agnóstica**. Suporta Cloud-First (Default atual) e Local-GPU (Futuro via switch de config).
**Filosofia:** "A Linha de Montagem Cognitiva". Atomização extrema de tarefas para TDAH, orquestrada por inteligência híbrida e execução soberana.

## 1. Manifesto Axiomático (As Leis Imutáveis)

Para operar o SODA, o Agente e o Humano devem obedecer a estas leis da física do projeto. A violação destas leis resulta em entropia imediata.

1. **Clean Root Axiom (Raiz Limpa):** A raiz contém apenas _Intenção_ (`PROJECT_CHARTER.md`), _Configuração de Infra_ (`docker-compose.yml`) e _Produto_ (`src`). O "caos" operacional é encapsulado em `.agent/`.
2. **Spec-Lock Protocol:** Código de produção (Fase 6) é um _efeito colateral_ da especificação. É proibido gerar código sem um Critério de Aceite (Gherkin - Fase 2) e Contrato de API (Fase 4) prévios.
3. **Sovereign Reading (Leitura Soberana):** Agentes são proibidos de ler HTML "sujo" da web. Toda ingestão de conhecimento deve passar por sanitização via **Docfork** (para documentação técnica) ou **Rtfmbro** (para repositórios/manuais), convertendo tudo para Markdown limpo antes de entrar na janela de contexto.
4. **Flow Integrity (Integridade de Fluxo):** O output da Fase N é input _read-only_ da Fase N+1. Um desenvolvedor (Fase 6) nunca altera o PRD (Fase 2) silenciosamente. Se o requisito mudar, executa-se um _Rollback_.
5. **State Persistence (Memória Externa):** O estado nunca reside no chat. O estado reside em `task_plan.md` (Kanban Tático), `findings.md` (Wiki Volátil) e `progress.md` (Log Imutável).

## 2. A "Super-Stack" Convergente v1.8 (Ferramentas & Infraestrutura)

Estratégia de inferência híbrida para maximizar inteligência e minimizar latência/custo, adaptável ao hardware disponível.

### A. Inteligência (Hierarquia de Modelos Configurável)

A decisão de onde "pensar" é controlada pelo `.env` (`SODA_LLM_PROVIDER`).

- **Big Brain (Arquiteto/Revisor):**
    - _Default (Cloud):_ **Gemini 1.5 Pro** (Via Google Antigravity/CLI OAuth). Custo Zero/Incluso.
    - _Alternative (Cloud):_ **Claude 3.5 Sonnet** (Via API Key).
- **Fast Brain (Operário/Sub-Agentes):**
    - _Default (Cloud):_ **Gemini 1.5 Flash** (API).
    - _Local (Hardware Ready):_ **Qwen 2.5 / Phi 4** rodando via Ollama. (Atualmente desativado por restrição térmica, mas suportado arquiteturalmente).

### B. A Memória (Persistência)

- **Hot Memory (Tática):** **Plan-With-Files** (`.agent/memory/hot/`).
- **Cold Memory (Episódica):** **OpenMemory** (GraphDB via Docker).
    - _Função:_ Armazena ADRs (Architecture Decision Records) e preferências do usuário (ex: "Sempre use Snake Case em Python").

### C. Os Sentidos (Ingestão & Busca)

- **Ingestão:** **Docfork** (Primário) e **Rtfmbro** (Fallback). Containers Docker que convertem a Web em Markdown.
- **Armada de Busca Federada:**
    - **Brave Search:** Web Geral (Privacidade e Limpeza).
    - **ArXiv:** Deep Tech/Algoritmos (Papers Acadêmicos).
    - **Bing:** Notícias (Recência/Fallback).
- **Percepção de Código:** **Heuristic-MCP**.
    - _Função:_ Busca semântica e _call-graph_ no código local.

### D. As Mãos (Execução)

- **Motor:** **Ralph Loop v1.8** (Powered by **Smolagents**).
    - _Mecânica:_ O script Python roda na CPU local. Ele invoca o "Big Brain" para desenhar a solução lógica e usa ferramentas de arquivo (`read_file`, `write_file`) para materializar o código.
- **Isolamento:** **Docker MCP Gateway**.
    - _Função:_ Segmenta o acesso à rede (ex: Agente de Code não acessa Twitter).

## 3. Anatomia do Sistema (Hyper-Structured)

Estrutura canônica de referência.

```
/RAIZ_DO_PROJETO
│
├── PROJECT_CHARTER.md          # [Fase 1] A Constituição (PID-Context).
├── src/                        # [Fase 6] O Produto.
├── tests/                      # [Fase 7] Testes E2E e Unitários.
│
├── .agent/                     # [Kernel SODA]
│   ├── config/                 # Configurações do Gateway e Regras.
│   ├── sops_registry/          # [O MANUAL] Os 22 SOPs (Templates injetáveis).
│   │   ├── SOP-01_BusinessRules.md
│   │   ├── SOP-02_UbiquitousLanguage.md
│   │   └── ... (até SOP-22)
│   ├── workflows/              # [GATILHOS] Prompts Mestres (/01...).
│   ├── skills/                 # [FERRAMENTAS]
│   │   ├── ingestion/          # Wrappers Docfork/Rtfmbro.
│   │   ├── search/             # Wrappers para Armada.
│   │   └── coding/             # Scripts Smolagents/Ralph.
│   ├── memory/                 # Hot & Cold storage.
│   └── scripts/                # Core engines (ralph_loop.py).
│
├── .openspec/                  # [Specs Machine-Readable]
│   ├── api/                    # Swagger/OpenAPI.
│   ├── db/                     # Schemas e Migrations.
│   └── features/               # Gherkin (.feature).
│
├── docker-compose.yml          # [Infra] O Gateway na Raiz.
├── pyproject.toml              # [Kernel Deps] Gerenciado pelo uv.
└── docs/                       # [Memória Humana]
    ├── 01_business/
    ├── 02_product/
    └── ...
```

## 4. O Ciclo de Vida SODA: Detalhe Estratégico dos 22 SOPs

Aqui definimos a "Estratégia Cognitiva" de cada fase. Cada SOP é um arquivo Markdown em `.agent/sops_registry/` que serve de System Prompt temporário.

### 🟢 Fase 1: Fundação de Negócio (Grounding)

- **SOP-01 (Business Rules):**
    - _Goal:_ Extrair axiomas matemáticos/legais imutáveis.
    - _Prompt Strategy:_ "Atue como um Analista Sênior hostil. Encontre furos lógicos nas regras de negócio propostas."
    - _Tool:_ Brave Search (Pesquisa de mercado).
- **SOP-02 (Ubiquitous Language):**
    - _Goal:_ Evitar Torre de Babel. Glossário único.
    - _Prompt Strategy:_ "Extraia substantivos e verbos. Defina-os. Proíba sinônimos. Se é 'Cliente', nunca chame de 'Usuário'."

### 🔵 Fase 2: Definição de Produto (Convergence)

- **SOP-03 (PRD - Product Requirements):**
    - _Goal:_ Definir o escopo e, principalmente, o _Não-Escopo_.
    - _Prompt Strategy:_ "Use o método 'Inversão'. Como esse projeto falharia? O que NÃO vamos construir no MVP?"
    - _Tool:_ Docfork (Benchmarking de concorrentes).
- **SOP-04 (Gherkin / Acceptance):**
    - _Goal:_ Contrato de verdade binária executável.
    - _Prompt Strategy:_ "Escreva cenários BDD. Cubra 'Caminho Feliz', 'Erro de Input' e 'Caso de Borda'."

### 🎨 Fase 3: Design & Interface (Visual Thinking)

- **SOP-05 (User Flows):**
    - _Tool:_ Mermaid JS Renderer.
    - _Goal:_ Visualizar complexidade lógica antes de codar.
- **SOP-06 (UX Writing) & SOP-07 (Design System):**
    - _Goal:_ Consistência visual e textual. Validação de componentes.

### 🏗️ Fase 4: Arquitetura Técnica (Blueprinting)

- **SOP-08 (API Contracts):**
    - _Goal:_ Spec-First. Frontend e Backend trabalham em paralelo baseados no contrato.
    - _Output:_ `swagger.yaml` ou `schema.graphql`.
- **SOP-09 (Data Design - DDR):**
    - _Goal:_ Schema imutável.
    - _Prompt Strategy:_ "Desenhe o ERD. Identifique chaves estrangeiras, índices de performance e PII (LGPD)."
- **SOP-10 (Threat Modeling):**
    - _Goal:_ Segurança por Design (Modelagem STRIDE).
    - _Tool:_ ArXiv (Busca de vulnerabilidades recentes).

### ⚔️ Fase 5: Planejamento Tático (Sharding)

- **Comando `/05_Sharding`:**
    - _Agente:_ Scrum Master.
    - _Ação:_ Lê os artefatos das Fases 2 e 4. Quebra em tarefas de <4 horas.
    - _Output:_ Popula `task_plan.md` e cria Issues no GitHub via **GitHub PM MCP**.

### 💻 Fase 6: Construção (The Grind - Ralph Loop)

- **Comando `/06_Code`:**
    - _Agente:_ Developer (Ralph Loop).
    - _Engine:_ **Smolagents (Python/CPU)**.
    - _SOP-11 (Implementation):_ Loop TDD (Ler Spec -> Criar Teste -> Implementar -> Refatorar).
    - _SOP-12 (Secrets):_ Detecção e sanitização de segredos (`.env`).
    - _SOP-13 (Auto-Doc):_ Geração automática de JSDoc/Docstrings.

### 🧪 Fase 7: Qualidade (Verification)

- **Comando `/07_Verify`:**
    - _Agente:_ QA Engineer.
    - _SOP-14 (Test Gen):_ Criação de suíte E2E/Unitária baseada em SOP-04.
    - _SOP-15 (Static Analysis):_ Linter, SonarQube simulado, Review de segurança.

### 🚀 Fase 8: Operações (Release)

- **Comando `/08_Release`:**
    - _Agente:_ DevOps / SRE.
    - _SOP-16 a SOP-22:_ Migrations, CI/CD, i18n, Compliance, RCA, Release Notes.
    - _Goal:_ Entrega profissional, rastreável e reversível.

## 5. Nomenclatura de Workflows (O Trilho Lógico)

Para evitar paralisia executiva ("Qual comando uso agora?"), os workflows são numerados sequencialmente. O usuário apenas segue o número.

- **`/01_Foundation`**: Roda SOP-01 e SOP-02. (Start)
- **`/02_Product`**: Roda SOP-03 e SOP-04. (Define)
- **`/03_Design`**: Roda SOP-05, 06, 07. (Visualize)
- **`/04_Arch`**: Roda SOP-08, 09, 10. (Blueprint)
- **`/05_Sharding`**: Quebra o plano. Cria Issues. (Plan)
- **`/06_Code`**: **O Loop Principal.** Invoca o Ralph para executar SOP-11, 12, 13. (Build)
- **`/07_Verify`**: Validação. Roda SOP-14, 15. (Test)
- **`/08_Release`**: Entrega. Roda SOP-16 a 22. (Ship)

## 6. Configuração de Hardware (v1.8)

O SODA v1.8 introduz o conceito de **Hardware Agnostic Execution**.

1. **Switch de Provedor:** O arquivo `.env` controla onde o "pensamento" ocorre.
    - `SODA_LLM_PROVIDER=gemini` (Default: Cloud via OAuth/CLI - Protege GPU).
    - `SODA_LLM_PROVIDER=ollama` (Local GPU - Ativar somente com hardware saudável).
    - `SODA_LLM_PROVIDER=anthropic` (Cloud via API Key).
2. **Smolagents (O Cérebro Local):**
    - Independente do provedor de LLM, a **lógica de código** (loops, condicional, leitura de arquivo) roda sempre localmente na GPU/CPU via Python. Isso garante velocidade e baixo custo.
3. **Fallback de Leitura:**
    - Se `Docfork` falhar, tenta `Rtfmbro`.
    - Se ambos falharem, pede o conteúdo manual.
    - **Nunca** injeta HTML sujo no contexto.