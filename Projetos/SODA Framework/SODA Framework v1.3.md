# SODA Framework v1.3

## Sistema Operacional de Desenvolvimento Agêntico

**(Spec-Oriented Dockerized Architecture)**

**Versão:** 1.3 (Enterprise Lifecycle & Granular SOPs)

**Status:** Definição Canônica (Production Ready)

**Classificação:** Arquitetura de Engenharia de Software Agêntica

**Target Runtime:** Google Antigravity sobre WSL2

**Otimização:** Neurodivergência (AH/SD + TDAH) — Foco em Granularidade Extrema, Segurança Psicológica, Rastreabilidade Total e Eliminação de Ambiguidade.

## 1. Ontologia e Glossário Proprietário (v1.3)

Para eliminar a entropia comunicativa entre o Arquiteto Humano e a Frota Agêntica, expandimos o léxico para cobrir todo o ciclo de vida do produto, transformando conceitos abstratos em entidades manipuláveis.

### 1.1 Entidades de Estado e Governança

- **Clean Root (Raiz Limpa):** Axioma de design onde a raiz do projeto contém apenas a _Intenção_ (`PROJECT_CHARTER.md`) e o _Produto_ (`src`). Toda complexidade operacional é encapsulada em `.agent`.
- **PID-Context (Project Intent Document):** A "Constituição" do projeto. Define _Non-Goals_ e _Axiomas_. Injetado no System Prompt de todos os agentes para evitar deriva estratégica.
- **Memória Tática (`task_plan.md`):** O buffer de execução. Agora segmentado pelas 8 fases do SODA v1.3, servindo como o quadro Kanban persistente do projeto.
- **Ubiquitous Language (SOP-02):** Um dicionário de termos de domínio que deve ser respeitado em todas as camadas (do DB ao Frontend). Se o termo definido é "Cliente", o uso de "Usuário" no código gera erro de linter.

### 1.2 Artefatos Técnicos Específicos

- **DDR (Data Design Record - SOP-09):** A especificação imutável do esquema de dados. Diferente de uma "migration" (que é código imperativo), o DDR é a _intenção declarativa_ do modelo de dados.
- **Gherkin Specs (SOP-04):** Critérios de aceite escritos em sintaxe `Dado/Quando/Então`. Servem como contrato de verdade binária entre o Gerente de Produto e o Agente de Testes.
- **Threat Model (SOP-10):** Documento vivo de análise de riscos (ex: STRIDE), definindo vetores de ataque e superfícies vulneráveis antes que uma linha de código seja escrita.

### 1.3 Mecanismos de Execução

- **Ralph Loop:** O motor de persistência OODA (Observe-Orient-Decide-Act) que itera sobre erros de compilação ou falhas de teste até o sucesso ou limite de custo.
- **Segmentação Modal:** Isolamento físico de ferramentas via Docker Gateway (ex: Agente de UX não tem acesso de rede às chaves de Banco de Dados de Produção).
- **Spec-Lock:** Mecanismo de governança que impede a geração de código (SOP-11) via _pre-commit hook_ simulado, caso não exista um PRD (SOP-03) e um Gherkin (SOP-04) aprovados.

## 2. Manifesto Arquitetural: A Linha de Montagem Cognitiva

O SODA v1.3 abandona a ideia de "Assistente de Chat" e adota o modelo de **Linha de Montagem Cognitiva Industrial**:

1. **Atomização Radical:** O processo de "Fazer Software" é desconstruído em 22 passos discretos e interdependentes. Isso remove a ansiedade do "por onde começo?" típica do TDAH.
2. **Imutabilidade de Fase:** O output da Fase N é o input _read-only_ da Fase N+1. O Agente de Código (Fase 5) não pode alterar os Requisitos (Fase 2). Se o código exigir mudança de requisito, o processo deve reiniciar (Rollback), garantindo a integridade da verdade.
3. **Auditabilidade por Design:** Cada SOP gera um artefato físico auditável (arquivo `.md`, `.mmd`, `.json`, `.yaml`). Se não está no arquivo, não existe. O conhecimento tácito é proibido.
4. **Neuro-Adaptação:** O sistema externaliza 100% da função executiva de "sequenciamento". O humano foca exclusivamente na _Qualidade da Decisão_ (Review), não na _Gestão da Tarefa_ (Memory).

## 3. Anatomia do Sistema: A Árvore de Diretórios (Adaptada v1.3)

A estrutura de diretórios foi expandida para acomodar a saída específica de cada um dos 22 SOPs, criando um "lugar para cada coisa".

```
/RAIZ_DO_PROJETO
│
├── PROJECT_CHARTER.md          # [Fase 1] A Bússola Imutável.
├── src/                        # [Fase 5] O Código Fonte do Produto.
├── tests/                      # [Fase 6] A Malha de Qualidade.
│
├── docs/                       # Memória Humana & Legal (Output dos SOPs)
│   ├── business/               # SOP-01 (Regras), SOP-02 (Glossário).
│   ├── product/                # SOP-03 (PRD), SOP-04 (Gherkin/Features).
│   ├── design/                 # SOP-05 (Flows), SOP-06 (Copy), SOP-07 (System).
│   ├── architecture/           # SOP-08 (API), SOP-09 (DDR), SOP-10 (Threats).
│   └── operations/             # SOP-20 (Privacy), SOP-22 (Manuals), SOP-21 (RCA).
│
├── .agent/                     # Kernel do SODA
│   ├── AGENTS.md               # Contexto Vivo (Memória de Curto Prazo).
│   ├── task_plan.md            # Checklist Mestre das 8 Fases.
│   ├── .soda/                  # Configurações Internas
│   │   ├── sops_registry/      # Definições YAML dos 22 SOPs (Instruções).
│   │   └── templates/          # Templates agnósticos (Skeletons) para cada SOP.
│   ├── memory/                 # OpenMemory (Graph) & Vetores (Chroma).
│   └── scripts/                # Ralph Loop, Bootstrap, CI/CD Generators.
│
├── .openspec/                  # Especificações Técnicas (Machine-Readable)
│   ├── api/                    # OpenAPI/Swagger Specs (YAML).
│   ├── db/                     # Schemas e Migrations Plans (SQL/Mermaid).
│   └── security/               # Políticas de Acesso (RBAC).
│
└── .env.example                # [Fase 5] Template de variáveis sanitizado (SOP-12).
```

## 4. Catálogo Mestre de Procedimentos (SOP Registry v1.3)

Abaixo, a definição canônica e mecânica dos 22 processos que compõem o SODA. Cada SOP é um "programa" executado por um Agente Especialista com ferramentas específicas.

### 🟢 Fase 1: Fundação de Negócio (A Bússola)

_Objetivo:_ Definir as regras do jogo e a linguagem antes de qualquer abstração técnica.

|   |   |   |   |   |   |
|---|---|---|---|---|---|
|**ID**|**Nome do SOP**|**Descrição & Mecânica Agêntica**|**Input Crítico**|**Output Esperado**|**Agente & Tools**|
|**01**|**Business Rules (BRD)**|Documenta leis imutáveis (ex: "Juros compostos de 1%"). A IA usa como validador lógico supremo para impedir alucinações de negócio.|Entrevista Humana|`docs/business/rules.md`|**Analyst** (Conversation)|
|**02**|**Ubiquitous Language**|Cria o glossário de domínio. Garante consistência semântica (ex: `User` vs `Client`). Este arquivo é injetado no System Prompt de **todos** os agentes subsequentes.|SOP-01|`docs/business/glossary.md`|**Analyst** (Text Proc)|

### 🔵 Fase 2: Definição de Produto (O Planejamento)

_Objetivo:_ Traduzir negócio em requisitos funcionais testáveis.

|   |   |   |   |   |   |
|---|---|---|---|---|---|
|**ID**|**Nome do SOP**|**Descrição & Mecânica Agêntica**|**Input Crítico**|**Output Esperado**|**Agente & Tools**|
|**03**|**Product Requirements (PRD)**|Define contexto, personas e objetivos. É o roteiro mestre. O agente deve cruzar referências com o SOP-01 para garantir aderência.|SOP-01, SOP-02|`docs/product/prd.md`|**Product Mgr** (OpenSpec)|
|**04**|**Acceptance Criteria (Gherkin)**|Traduz o PRD em testes `Dado/Quando/Então`. Conecta Produto a QA. Essencial para o TDD (SOP-14). O agente valida se o Gherkin cobre todos os requisitos do PRD.|SOP-03|`docs/product/specs.feature`|**QA Lead** (Gherkin Parser)|

### 🎨 Fase 3: Experiência e Interface (O Design)

_Objetivo:_ Definir a interação visual e textual antes de codar componentes.

|   |   |   |   |   |   |
|---|---|---|---|---|---|
|**ID**|**Nome do SOP**|**Descrição & Mecânica Agêntica**|**Input Crítico**|**Output Esperado**|**Agente & Tools**|
|**05**|**User Flows**|Mapeia a árvore de decisão e navegação. O agente utiliza a sintaxe **Mermaid.js** para gerar visualizações lógicas renderizáveis no Antigravity.|SOP-03|`docs/design/flows.mmd`|**Designer** (Mermaid)|
|**06**|**UX Writing & Voice**|Define o tom de voz (ex: formal/divertido). Garante consistência em mensagens de erro e labels. O agente reescreve textos técnicos do PRD para linguagem humana.|SOP-02 (Glossário)|`docs/design/voice_guide.md`|**Content UX** (LLM)|
|**07**|**Design System Align**|Valida componentes visuais (cores, espaçamentos) contra bibliotecas padrão (Material/Tailwind) ou tokens da marca. Gera um "Inventário de Componentes".|SOP-05|`docs/design/system.md`|**Frontend Arch** (Search)|

### 🏗️ Fase 4: Arquitetura Técnica (A Engenharia)

_Objetivo:_ Projetar a estrutura invisível e os contratos de dados.

|   |   |   |   |   |   |
|---|---|---|---|---|---|
|**ID**|**Nome do SOP**|**Descrição & Mecânica Agêntica**|**Input Crítico**|**Output Esperado**|**Agente & Tools**|
|**08**|**API Contracts**|Define endpoints, payloads e tipos (OpenAPI/Swagger). Contrato inquebrável entre Front e Back. O agente gera o YAML que servirá de base para _stubs_.|SOP-03, SOP-05|`.openspec/api/swagger.yaml`|**Backend Arch** (Swagger Gen)|
|**09**|**Data Design Record (DDR)**|Modela o ERD (Entidade-Relacionamento), índices e chaves. A verdade do banco de dados. O agente prevê queries pesadas e cria índices antecipadamente.|SOP-01, SOP-03|`.openspec/db/schema.mmd`|**DBA** (Mermaid/SQL)|
|**10**|**Threat Modeling**|Identifica riscos (Framework STRIDE), superfícies de ataque e matriz de permissões (RBAC). Define o que precisa de criptografia no SOP-11.|SOP-08, SOP-09|`.openspec/security/threats.md`|**SecOps** (Audit)|

### 💻 Fase 5: Construção (A Execução)

_Objetivo:_ Materializar a arquitetura em código limpo e seguro.

|   |   |   |   |   |   |
|---|---|---|---|---|---|
|**ID**|**Nome do SOP**|**Descrição & Mecânica Agêntica**|**Input Crítico**|**Output Esperado**|**Agente & Tools**|
|**11**|**Implementation (Clean Code)**|Codificação propriamente dita seguindo SOLID. O **Ralph Loop** itera aqui, escrevendo código que satisfaz o Contrato de API (SOP-08) e DDR (SOP-09).|SOP-08, SOP-09|`src/**`|**Developer** (Ralph/Smolagents)|
|**12**|**Secret Management**|Protocolo de configuração de `.env`. O agente identifica variáveis sensíveis no código e as move para o `.env`, criando um `.env.example` sanitizado.|SOP-10|`.env.example`|**DevOps** (Scanner)|
|**13**|**Auto-Documentation**|Gera JSDoc/Pydoc e atualiza o README com base no código real implementado. O agente lê a assinatura das funções e escreve a docstring.|SOP-11|`src/**` (Comentado)|**Tech Writer** (Parser)|

### 🧪 Fase 6: Qualidade (A Verificação)

_Objetivo:_ Garantir que o construído reflete o planejado e não quebra.

|   |   |   |   |   |   |
|---|---|---|---|---|---|
|**ID**|**Nome do SOP**|**Descrição & Mecânica Agêntica**|**Input Crítico**|**Output Esperado**|**Agente & Tools**|
|**14**|**Test Generation**|Escreve testes unitários e de integração baseados no Gherkin (SOP-04). O agente usa **Playwright** para testes E2E e Jest/Pytest para unitários.|SOP-04, SOP-11|`tests/**`|**QA Automation** (Playwright)|
|**15**|**Static Analysis & Review**|Revisor automático (Linter, Sonar). Busca bugs, code smells e desvios de padrão. O Ralph Loop bloqueia o merge se houver erros aqui.|SOP-11|Relatório de Análise|**Auditor** (Linter)|

### 🚀 Fase 7: Operações (O Lançamento)

_Objetivo:_ Levar o código ao ambiente produtivo com segurança e reversibilidade.

|   |   |   |   |   |   |
|---|---|---|---|---|---|
|**ID**|**Nome do SOP**|**Descrição & Mecânica Agêntica**|**Input Crítico**|**Output Esperado**|**Agente & Tools**|
|**16**|**DB Migrations**|Cria scripts de migração seguros baseados no DDR (SOP-09). O agente verifica se a migração é _non-locking_ e reversível (Down migration).|SOP-09|`migrations/*.sql`|**DBA** (SQL Gen)|
|**17**|**CI/CD Pipelines**|Configura esteiras de automação (GitHub Actions) para build, test e deploy. O agente cria o YAML do workflow.|SOP-14, SOP-15|`.github/workflows/*`|**DevOps** (YAML Gen)|
|**18**|**Observability**|Configura logs estruturados, métricas e health checks para monitoramento pós-deploy. O agente instrumenta o código (SOP-11) com telemetria.|SOP-10|Configs de Monitoramento|**SRE** (Code Mod)|

### 🏁 Fase 8: Entrega e Manutenção (O Ciclo Vivo)

_Objetivo:_ Garantir a longevidade, conformidade e valor para o usuário.

|   |   |   |   |   |   |
|---|---|---|---|---|---|
|**ID**|**Nome do SOP**|**Descrição & Mecânica Agêntica**|**Input Crítico**|**Output Esperado**|**Agente & Tools**|
|**19**|**Internationalization (i18n)**|Extrai strings hardcoded para arquivos de tradução. O agente varre o código UI e substitui texto por chaves de tradução.|SOP-06|`locales/*.json`|**Developer** (AST Parser)|
|**20**|**Compliance (LGPD/GDPR)**|Auditoria de dados pessoais. O agente verifica se todos os campos marcados como PII no DDR (SOP-09) estão criptografados ou anonimizados.|SOP-10, SOP-09|Relatório de Conformidade|**DPO Agent** (Audit)|
|**21**|**Root Cause Analysis (RCA)**|Protocolo para debugging e investigação de falhas em produção. O agente lê logs de erro e sugere correções, retroalimentando o SOP-11.|Logs de Erro|`docs/operations/postmortem.md`|**SRE** (Log Analysis)|
|**22**|**Release Notes & Help**|Traduz mudanças técnicas (Commits) em valor para o usuário (Linguagem natural). O agente lê o histórico Git e resume as features para o usuário final.|SOP-20, Commits|`CHANGELOG.md`|**Product Mkt** (Summarizer)|

## 5. Template Agnóstico de SOP (Machine-Readable)

Este template híbrido (YAML + Markdown) é a "API" que permite ao framework SODA instanciar e executar qualquer um dos 22 procedimentos de forma padronizada.

```
# ------------------------------------------------------------------
# SODA SOP TEMPLATE v1.3
# ------------------------------------------------------------------
sop_meta:
  id: "SOP-{{ID}}"
  name: "{{NOME_DO_PROCEDIMENTO}}"
  phase: "{{FASE_DO_CICLO}}"
  version: "1.3"
  responsible_role: "@{{PERSONA}}" # Ex: @architect, @qa_lead

# CONTRATO DE DEPENDÊNCIAS (O que o agente precisa LER)
inputs:
  mandatory:
    - path: "docs/previous_sop_output.md"
      description: "A saída do SOP anterior é a verdade imutável."
  context:
    - path: "docs/business/glossary.md"
      description: "Para garantir consistência semântica."
    - path: "PROJECT_CHARTER.md"
      description: "Para alinhamento estratégico."

# CONTRATO DE ENTREGA (O que o agente precisa CRIAR)
outputs:
  primary:
    path: "docs/current_sop_output.md"
    format: "markdown" # ou yaml, json, sql, py
    template: "templates/{{TIPO}}_template.md"
  side_effects:
    - "Update task_plan.md status to DONE"
    - "Create GitHub Issue if blockers found"

# CONFIGURAÇÃO DO MOTOR DE EXECUÇÃO (RALPH)
execution_config:
  max_iterations: 15
  tools_allowed: ["tool_A", "tool_B"] # Sandbox de ferramentas
  validation_script: ".agent/scripts/validators/validate_{{ID}}.py"

# CORPO DO PROCEDIMENTO (INSTRUÇÕES PARA O LLM)
instructions: |
  ## 1. Objetivo Primário
  {{DESCRICAO_DO_OBJETIVO}}

  ## 2. Protocolo de Execução (Algorithm)
  1. **Ingestão:** Leia todos os arquivos listados em `inputs`. Se algum arquivo obrigatório estiver faltando, ABORTAR IMEDIATAMENTE e solicitar intervenção humana.
  2. **Análise:** Compare o pedido atual com as restrições do `PROJECT_CHARTER.md`.
  3. **Processamento:**
     - Passo 3.1: {{ACAO_ESPECIFICA_1}}
     - Passo 3.2: {{ACAO_ESPECIFICA_2}}
  4. **Verificação:** Antes de salvar, execute o script de validação.

  ## 3. Guardrails (Restrições Negativas)
  - ⛔ **MUST NOT:** Inventar dados não presentes nos inputs.
  - ⛔ **MUST NOT:** Violar a terminologia do Glossário.
  - ⚠️ **WARNING:** Se encontrar ambiguidade, pergunte ao humano. Não assuma.

  ## 4. Definition of Done (DoD)
  - [ ] O arquivo de saída existe no caminho especificado.
  - [ ] O conteúdo passa no validador sintático.
  - [ ] O `task_plan.md` foi atualizado.
```

## 6. Constituição SODA (`GEMINI.md`) - Cláusulas de Governança v1.3

Atualização das regras globais para suportar o rigor da nova estrutura de 22 SOPs.

```
# SODA CONSTITUTION v1.3 (ADDENDUM)

## 5. PROTOCOLO DE INTEGRIDADE DE DADOS (DATA SOVEREIGNTY)
- **Soberania do DDR (SOP-09):** Nenhuma alteração de banco de dados pode ser feita via código (ORM) sem antes atualizar o Data Design Record e gerar o script de migração (SOP-16). "Code-first" em DB é estritamente proibido; "Design-first" é mandatório.
- **Lei do Glossário (SOP-02):** A terminologia definida no Glossário de Domínio é lei suprema. O uso de sinônimos não autorizados (ex: usar `Customer` quando o definido é `Client`) será rejeitado automaticamente pelo Linter Semântico.
- **Privacidade por Design (SOP-20):** Todo campo de dados deve ser classificado como `Público`, `Interno` ou `PII` (Sensível) no momento da criação no DDR. Campos PII exigem criptografia obrigatória.

## 6. INTEGRIDADE DE FLUXO (FLOW INTEGRITY)
- **Bloqueio de Fase:** É proibido iniciar SOPs da Fase N+1 se os SOPs críticos da Fase N não estiverem marcados como `Done` no `task_plan.md`. O Agente não deve "pular etapas" para ganhar tempo.
- **Rastreabilidade de Testes:** Todo Teste criado (SOP-14) deve conter um link ou referência (comentário) ao Critério de Aceite (SOP-04) que o originou. Testes órfãos não são permitidos.
```

## 7. Guia de Implementação (Bootstrap v1.3)

Para iniciar um projeto com a estrutura completa de 22 SOPs:

1. **Instalação do Kernel v1.3:**

    ```
    git clone git@github.com/seu-user/soda-kernel-v1.3.git .agent
    ```

2. **Inicialização do Registro:**
    
    O script de bootstrap agora cria os placeholders para todos os 22 SOPs e configura o `task_plan.md` com as 8 fases.

    ```
    soda /00-bootstrap --mode enterprise
    ```

    _Resultado: A estrutura de pastas completa (`docs/business`, `docs/product`, etc) é criada e o plano tático é populado._
    
3. **Início da Fase 1:**

    ```
    soda /01-inception
    ```