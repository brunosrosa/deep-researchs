
SODA Framework v1.4

## Sistema Operacional de Desenvolvimento Agêntico

**(Spec-Oriented Dockerized Architecture)**

**Versão:** 1.4 (Definitive Master Edition)

**Status:** Gold Standard (Imutável para Produção)

**Classificação:** Arquitetura de Engenharia de Software Cognitiva

**Target Runtime:** Google Antigravity sobre WSL2

**Filosofia:** "A Linha de Montagem Cognitiva" — Otimizado para Neurodivergência (AH/SD + TDAH) através de Atomização Extrema, Memória Externa e Governança Industrial.

## 1. Manifesto e Ontologia (A Base Conceitual)

O SODA v1.4 não é uma ferramenta; é um **Sistema Operacional de Processos**. Ele parte da premissa de que a complexidade do software moderno excede a capacidade de gestão de memória de curto prazo humana e de LLMs. Para mitigar isso, transformamos o desenvolvimento de "arte" em "engenharia determinística".

### 1.1 O Glossário da Verdade

Para eliminar a entropia comunicativa, estes termos são lei:

- **Clean Root (Raiz Limpa):** Axioma de design onde a raiz do projeto contém apenas a _Intenção_ (`PROJECT_CHARTER.md`) e o _Produto_ (`src`). Toda a complexidade operacional, scripts, logs e memórias intermediárias são encapsulados no diretório oculto `.agent/`. O humano vê apenas o que importa.
    
- **PID-Context (`PROJECT_CHARTER.md`):** A "Constituição" do projeto. Um arquivo enxuto contendo os _Non-Goals_, _Stack Tecnológico_ e _Axiomas de Negócio_. É injetado no System Prompt de **todos** os agentes para garantir alinhamento estratégico contínuo.
    
- **PID-Full (`docs/management/PID_FULL.md`):** O documento socrático completo, contendo histórico, análise de stakeholders e nuances emocionais. Consultável sob demanda (RAG), mas não reside na memória ativa.
    
- **Memória Tática (`task_plan.md`):** O Quadro Kanban Persistente. Um arquivo Markdown vivo, dividido pelas 8 Fases do SODA, onde cada SOP é um item de checklist. Sincronizado unidirecionalmente com o GitHub Projects.
    
- **Ralph Loop:** O motor de persistência OODA (Observe-Orient-Decide-Act). Um script que encapsula a execução do agente em um loop de _Tentativa → Erro → Correção → Retentativa_, governado por limites de custo e iterações.
    
- **Segmentação Modal:** O uso do **Docker MCP Gateway** para isolar fisicamente as ferramentas disponíveis para cada agente. O Agente de UX não tem acesso ao Banco de Dados; o Agente de Backend não tem acesso à Internet aberta. Isso previne alucinações e vazamentos.
    
- **Ubiquitous Language (SOP-02):** O dicionário de termos de domínio. Se o negócio define "Cliente", é proíbido usar "Usuário" no código.
    

## 2. Anatomia do Sistema: A Árvore de Diretórios Definitiva

A estrutura de pastas reflete a segregação de responsabilidades do framework. Cada fase tem seu lugar.

```
/RAIZ_DO_PROJETO
│
├── PROJECT_CHARTER.md          # [Fase 1] A Bússola Imutável. (PID-Context)
├── src/                        # [Fase 5] O Código Fonte do Produto.
├── tests/                      # [Fase 6] A Malha de Qualidade (E2E/Unit).
│
├── docs/                       # Memória Humana & Legal (Output dos SOPs)
│   ├── business/               # SOP-01 (Regras), SOP-02 (Glossário).
│   ├── product/                # SOP-03 (PRD), SOP-04 (Gherkin/Features).
│   ├── design/                 # SOP-05 (Flows), SOP-06 (Copy), SOP-07 (System).
│   ├── architecture/           # SOP-08 (API), SOP-09 (DDR), SOP-10 (Threats).
│   └── operations/             # SOP-20 (Privacy), SOP-22 (Manuals), SOP-21 (RCA).
│
├── .agent/                     # Kernel do SODA (O Cérebro da Operação)
│   ├── AGENTS.md               # Contexto Vivo (Passagem de turno entre personas).
│   ├── task_plan.md            # Checklist Mestre das 8 Fases (Sincronizado c/ GitHub).
│   ├── .soda/                  # Configurações Internas
│   │   ├── config.yaml         # Configuração global (LLM, Timeouts).
│   │   ├── sops_registry/      # [CORE] Definições YAML/MD dos 22 SOPs.
│   │   ├── templates/          # Templates agnósticos (Skeletons) para outputs.
│   │   └── docker-mcp/         # Perfis de Container (Research, Build, Audit).
│   ├── memory/                 # Persistência de Dados
│   │   ├── soda.db             # SQLite (Metadados de execução).
│   │   ├── open_mem/           # OpenMemory Graph (Conhecimento Semântico).
│   │   └── vectors/            # ChromaDB (Busca Vetorial em Docs).
│   └── scripts/                # Executáveis
│       ├── bootstrap.sh        # Setup inicial.
│       ├── ralph_loop.py       # Motor de Execução.
│       └── smol_tools/         # Ferramentas locais (Python/Scraping).
│
├── .openspec/                  # Especificações Técnicas (Machine-Readable)
│   ├── api/                    # OpenAPI/Swagger Specs (YAML).
│   ├── db/                     # Schemas e Migrations Plans (SQL/Mermaid).
│   └── security/               # Políticas de Acesso (RBAC).
│
└── .env.example                # [Fase 5] Template de variáveis sanitizado (SOP-12).
```

## 3. A Linha de Montagem: O Catálogo Mestre dos 22 SOPs

O desenvolvimento é dividido em 8 Fases Lógicas. Cada SOP é um pré-requisito para o próximo. **Não há atalhos.**

### 🟢 Fase 1: Fundação de Negócio (A Bússola)

_Objetivo:_ Definir as regras do jogo e a linguagem antes de qualquer abstração técnica.

|   |   |   |   |   |   |
|---|---|---|---|---|---|
|**ID**|**SOP Name**|**Descrição & Mecânica Agêntica**|**Input Crítico**|**Output Esperado**|**Agente & Tools**|
|**01**|**Business Rules (BRD)**|Documenta leis imutáveis (ex: regras de cálculo, restrições). A IA usa como validador lógico supremo.|Entrevista Humana|`docs/business/rules.md`|**Analyst** (Conversation)|
|**02**|**Ubiquitous Language**|Cria o glossário de domínio. Garante consistência semântica. Injetado no prompt de todos os agentes.|SOP-01|`docs/business/glossary.md`|**Analyst** (Text Proc)|

### 🔵 Fase 2: Definição de Produto (O Planejamento)

_Objetivo:_ Traduzir negócio em requisitos funcionais testáveis.

|   |   |   |   |   |   |
|---|---|---|---|---|---|
|**ID**|**SOP Name**|**Descrição & Mecânica Agêntica**|**Input Crítico**|**Output Esperado**|**Agente & Tools**|
|**03**|**Product Reqs (PRD)**|Define contexto, personas e objetivos. O roteiro mestre. Deve cruzar referências com SOP-01.|SOP-01, SOP-02|`docs/product/prd.md`|**Product Mgr** (OpenSpec)|
|**04**|**Acceptance (Gherkin)**|Traduz PRD em `Dado/Quando/Então`. É o contrato de TDD/BDD. Define o sucesso da Fase 5.|SOP-03|`docs/product/specs.feature`|**QA Lead** (Gherkin Parser)|

### 🎨 Fase 3: Experiência e Interface (O Design)

_Objetivo:_ Definir a interação visual e textual antes de codar.

|   |   |   |   |   |   |
|---|---|---|---|---|---|
|**ID**|**SOP Name**|**Descrição & Mecânica Agêntica**|**Input Crítico**|**Output Esperado**|**Agente & Tools**|
|**05**|**User Flows**|Mapeia árvore de decisão. Usa Mermaid.js para visualização lógica. Prevines "becos sem saída".|SOP-03|`docs/design/flows.mmd`|**Designer** (Mermaid)|
|**06**|**UX Writing & Voice**|Define o tom de voz. Garante consistência em mensagens de erro/labels. Humaniza a técnica.|SOP-02|`docs/design/voice_guide.md`|**Content UX** (LLM)|
|**07**|**Design System Align**|Valida componentes visuais contra bibliotecas padrão. Gera inventário de UI.|SOP-05|`docs/design/system.md`|**Frontend Arch** (Search)|

### 🏗️ Fase 4: Arquitetura Técnica (A Engenharia)

_Objetivo:_ Projetar a estrutura invisível e os contratos de dados.

|   |   |   |   |   |   |
|---|---|---|---|---|---|
|**ID**|**SOP Name**|**Descrição & Mecânica Agêntica**|**Input Crítico**|**Output Esperado**|**Agente & Tools**|
|**08**|**API Contracts**|Define endpoints e tipos (OpenAPI). Contrato inquebrável Front/Back. Gera stubs.|SOP-03, SOP-05|`.openspec/api/swagger.yaml`|**Backend Arch** (Swagger Gen)|
|**09**|**Data Design (DDR)**|Modela ERD, índices e chaves. A verdade do DB. Previne "Schema-on-read" acidental.|SOP-01, SOP-03|`.openspec/db/schema.mmd`|**DBA** (Mermaid/SQL)|
|**10**|**Threat Modeling**|Análise de riscos (STRIDE) e permissões (RBAC). Define o que exige criptografia.|SOP-08, SOP-09|`.openspec/security/threats.md`|**SecOps** (Audit)|

### 💻 Fase 5: Construção (A Execução)

_Objetivo:_ Materializar a arquitetura em código limpo e seguro.

|   |   |   |   |   |   |
|---|---|---|---|---|---|
|**ID**|**SOP Name**|**Descrição & Mecânica Agêntica**|**Input Crítico**|**Output Esperado**|**Agente & Tools**|
|**11**|**Implement (Clean Code)**|Codificação via **Ralph Loop**. Consome o Gherkin (SOP-04) como guia de implementação (TDD).|SOP-04, SOP-08|`src/**`|**Developer** (Ralph/Smol)|
|**12**|**Secret Management**|Move variáveis sensíveis para `.env` e cria `.env.example`. Sanitiza o repositório.|SOP-10|`.env.example`|**DevOps** (Scanner)|
|**13**|**Auto-Documentation**|Gera JSDoc/Pydoc e atualiza README baseado no código real.|SOP-11|`src/**` (Comentado)|**Tech Writer** (Parser)|

### 🧪 Fase 6: Qualidade (A Verificação)

_Objetivo:_ Garantir estabilidade e prevenir regressão.

|   |   |   |   |   |   |
|---|---|---|---|---|---|
|**ID**|**SOP Name**|**Descrição & Mecânica Agêntica**|**Input Crítico**|**Output Esperado**|**Agente & Tools**|
|**14**|**Test Generation**|Cria a suite automatizada (Unit/E2E) baseada no Gherkin (SOP-04). Imortaliza o requisito.|SOP-04, SOP-11|`tests/**`|**QA Auto** (Playwright)|
|**15**|**Static Analysis**|Revisor automático (Linter, Sonar). Busca bugs e code smells. Bloqueia se falhar.|SOP-11|Relatório de Análise|**Auditor** (Linter)|

### 🚀 Fase 7: Operações (O Lançamento)

_Objetivo:_ Levar o código à produção com segurança.

|   |   |   |   |   |   |
|---|---|---|---|---|---|
|**ID**|**SOP Name**|**Descrição & Mecânica Agêntica**|**Input Crítico**|**Output Esperado**|**Agente & Tools**|
|**16**|**DB Migrations**|Cria scripts SQL seguros baseados no DDR (SOP-09). Garante integridade de dados.|SOP-09|`migrations/*.sql`|**DBA** (SQL Gen)|
|**17**|**CI/CD Pipelines**|Configura GitHub Actions para build/test/deploy automático.|SOP-14, SOP-15|`.github/workflows/*`|**DevOps** (YAML Gen)|
|**18**|**Observability**|Configura logs, métricas e health checks. Instrumenta o código.|SOP-10|Configs de Monitoramento|**SRE** (Code Mod)|

### 🏁 Fase 8: Entrega e Manutenção (O Ciclo Vivo)

_Objetivo:_ Conformidade e valor para o usuário.

|   |   |   |   |   |   |
|---|---|---|---|---|---|
|**ID**|**SOP Name**|**Descrição & Mecânica Agêntica**|**Input Crítico**|**Output Esperado**|**Agente & Tools**|
|**19**|**I18n**|Extrai strings para arquivos de tradução. Prepara para múltiplos idiomas.|SOP-06|`locales/*.json`|**Developer** (AST Parser)|
|**20**|**Compliance (LGPD)**|Auditoria de dados. Verifica criptografia de PII definidos no SOP-09/10.|SOP-10, SOP-09|Relatório de Conformidade|**DPO Agent** (Audit)|
|**21**|**RCA (Debugging)**|Protocolo para investigar falhas em produção. Retroalimenta SOP-11.|Logs de Erro|`docs/ops/postmortem.md`|**SRE** (Log Analysis)|
|**22**|**Release Notes**|Traduz commits técnicos em linguagem humana para usuários.|SOP-20, Commits|`CHANGELOG.md`|**Product Mkt** (Summarizer)|

## 4. O Sistema Nervoso: Integração das Ferramentas (Super-Stack)

O SODA não apenas lista ferramentas; ele define _onde_ e _como_ elas operam.

|   |   |   |   |
|---|---|---|---|
|**Categoria**|**Ferramenta**|**Integração SODA**|**Função Axiomática**|
|**Memória**|**OpenMemory**|Backend GraphDB|Armazenar o _grafo de decisões_ ("Por que usamos JWT?").|
|**Planejamento**|**Plan-With-Files**|`task_plan.md`|Buffer de memória tática das 8 Fases.|
|**Leitura**|**Aleph (RLM)**|Server MCP|"Ler este repositório inteiro e entender a arquitetura".|
|**Persistência**|**Ralph Loop**|Script Python|Loop de tentativa e erro para codificação (Fase 5).|
|**Lógica**|**Smolagents**|Local Python Env|Execução de scripts locais para matemática/scraping.|
|**Navegação**|**Playwright**|Server MCP|Testes E2E (SOP-14) e leitura de SPAs.|
|**Busca**|**Search Armada**|Meta-Skill|Roteia queries: DuckDuckGo (Fatos), Exa (Conceitos), Tavily (News).|
|**Infra**|**Docker MCP**|Gateway|Segmenta ferramentas por perfil (Research vs Build).|
|**Otimização**|**MCP TOON**|Middleware|Comprime JSON/CSV para economizar tokens.|
|**Spec**|**OpenSpec**|Framework|Padroniza a saída dos SOPs da Fase 4 (Arquitetura).|

## 5. Template Mestre de SOP (Agnóstico e Executável)

Este template é a "API" que permite instanciar os 22 SOPs. Ele é lido tanto por humanos quanto pelo orquestrador de agentes.

```
# ------------------------------------------------------------------
# SODA SOP TEMPLATE v1.4
# ------------------------------------------------------------------
sop_meta:
  id: "SOP-{{ID}}" # Ex: SOP-09
  name: "{{NOME}}" # Ex: Data Design Record
  phase: "{{FASE}}" # Ex: 4 - Arquitetura Técnica
  responsible_role: "@{{PERSONA}}" # Ex: @dba_architect

# CONTRATO DE ENTRADA (DEPENDÊNCIAS RÍGIDAS)
inputs:
  mandatory:
    - path: "docs/previous_sop_output.md"
      validation: "file_exists"
  context:
    - path: "PROJECT_CHARTER.md"
      description: "Axiomas do Projeto"
    - path: "docs/business/glossary.md"
      description: "Terminologia Obrigatória"

# CONTRATO DE SAÍDA (ENTREGÁVEIS)
outputs:
  primary:
    path: "docs/path/to/output.md"
    template: ".agent/.soda/templates/{{TIPO}}.md"
  side_effects:
    - action: "update_task_plan"
      status: "DONE"

# CONFIGURAÇÃO DO RALPH LOOP (MOTOR DE EXECUÇÃO)
ralph_config:
  max_iterations: 15
  temperature_decay: true # Aumenta a criatividade se o erro persistir
  allowed_tools: # Sandbox: O agente só vê o necessário
    - "mermaid_renderer"
    - "sql_validator"
    - "open_memory_query"

# INSTRUÇÕES COGNITIVAS (PROMPT DO SISTEMA)
instructions: |
  ## 1. Objetivo
  {{DESCRICAO_CLARA_DO_OBJETIVO}}

  ## 2. Algoritmo de Execução
  1. **Ingestão:** Carregue os inputs. Se faltar algo, ABORTAR.
  2. **Validação Semântica:** Verifique se os termos usados batem com o Glossário (SOP-02).
  3. **Processamento:**
     - Passo 3.1: {{ACAO_1}}
     - Passo 3.2: {{ACAO_2}}
  4. **Auto-Crítica:** O resultado viola algum Non-Goal do Charter?

  ## 3. Guardrails (O que NÃO fazer)
  - ⛔ NÃO invente dados não presentes nos inputs.
  - ⛔ NÃO altere arquivos de outras fases.
  - ⚠️ Se houver ambiguidade, gere uma pergunta no `task_plan.md` em vez de assumir.

  ## 4. Definition of Done (DoD)
  - [ ] Arquivo gerado no caminho correto.
  - [ ] Sintaxe validada (sem erros de linter).
  - [ ] Check no `task_plan.md`.
```

## 6. A Constituição SODA (`GEMINI.md`)

As Leis Supremas que governam a IA, localizadas em `~/.gemini/GEMINI.md`.

```
# SODA CONSTITUTION v1.4

## 1. HIERARQUIA EPISTEMOLÓGICA (A Verdade)
1. `PROJECT_CHARTER.md`: A Lei Suprema.
2. `docs/business/glossary.md`: A Lei da Linguagem.
3. `.openspec/`: O Contrato Técnico.
4. `task_plan.md`: A Ordem do Dia.

## 2. PROTOCOLO DE INTEGRIDADE (Flow Integrity)
- **Imutabilidade Retroativa:** Um Agente da Fase 5 (Construção) NUNCA pode alterar documentos da Fase 2 (Produto). Se o código exigir mudança de requisito, deve-se solicitar um Rollback para a Fase 2.
- **Soberania do DDR (SOP-09):** Nenhuma alteração de DB é feita via código (ORM) sem antes atualizar o DDR e gerar Migrations (SOP-16).
- **Spec-Lock:** É proibido gerar código (SOP-11) para uma feature que não tenha Gherkin (SOP-04) e API Contract (SOP-08) definidos.

## 3. SEGURANÇA E PRIVACIDADE
- **Secret Zero:** Segredos vivem APENAS no `.env`. O Agente deve usar o SOP-12 para gerenciar isso.
- **Privacy by Design:** Dados PII definidos no SOP-09 devem ser tratados conforme o SOP-20.
- **Loop Guard:** O Ralph Loop deve ter `MAX_ITERS=15`. Se falhar, pare e peça ajuda.

## 4. GESTÃO DE CONTEXTO
- **Start Fresh:** Ao mudar de SOP, limpe o histórico de chat. Carregue apenas os Inputs Obrigatórios definidos no Template do SOP.
- **Use Tools:** Não tente emular um terminal. Use o `Ralph Loop` para executar comandos reais.
```

## 7. Guia de Bootstrap (Como Iniciar Agora)

1. **Instalação do Kernel:**
    
    ```
    git clone git@github.com/seu-user/soda-kernel-v1.4.git .agent
    ```
    
2. **Setup do Projeto:**
    
    ```
    mkdir meu-projeto && cd meu-projeto
    cp -r /path/to/kernel/.agent .
    alias soda="python3 .agent/scripts/soda_cli.py"
    ```
    
3. **Bootstrap Enterprise:**
    
    O comando abaixo cria a estrutura de pastas completa para os 22 SOPs e popula o `task_plan.md`.
    
    ```
    soda /00-bootstrap --full
    ```
    
4. **Início da Jornada (Fase 1):**
    
    ```
    soda /01-inception
    ```