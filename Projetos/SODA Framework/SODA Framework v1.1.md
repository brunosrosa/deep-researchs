# SODA Framework v1.1

## Sistema Operacional de Desenvolvimento Agêntico

**(Spec-Oriented Dockerized Architecture)**

**Versão:** 1.1 (Definitive Ontology)

**Classificação:** Arquitetura de Sistemas Cognitivos

**Target Runtime:** Google Antigravity sobre WSL2 (Windows Subsystem for Linux)

**Otimização:** Neurodivergência (AH/SD + TDAH) — Foco em Externalização de Memória e Redução de Fricção Executiva.

## 1. Ontologia e Glossário Proprietário

Para garantir a integridade semântica entre o Operador Humano e a Frota Agêntica, estabelecemos este léxico imutável. O entendimento destes termos não é opcional; é estrutural.

### 1.1 Entidades de Estado

- **Clean Root (Raiz Limpa):** Princípio arquitetural onde o diretório raiz do projeto expõe exclusivamente a _Intenção Estratégica_ (`PROJECT_CHARTER.md`) e o _Produto Final_ (`src`), ocultando a complexidade operacional (`.agent`) e de documentação (`docs`). Visa reduzir a carga cognitiva visual do operador.
- **PID-Context (Project Intent Document - Context):** O arquivo `PROJECT_CHARTER.md`. É a "Constituição" injetada no _System Prompt_ de todos os agentes. Define as restrições inegociáveis (Tech Stack, Non-Goals, Axiomas de Negócio).
- **PID-Full (Project Intent Document - Full):** O documento socrático completo (`docs/management/PID_FULL.md`). Contém o histórico, as nuances emocionais do cliente, a análise de stakeholders e detalhes de auditoria. É consultado sob demanda, mas não reside na memória ativa.
- **Memória Tática (`task_plan.md`):** O _Buffer de Execução_. Um arquivo Markdown vivo que traduz as Specs abstratas em unidades de trabalho atômicas. Serve como a "Memória RAM" persistida, sincronizada unidirecionalmente com o GitHub Projects para visualização Kanban.

### 1.2 Mecanismos de Execução

- **Segmentação Modal:** A aplicação estrita do Princípio do Menor Privilégio via Infraestrutura. Utiliza o Docker MCP Gateway para montar/desmontar volumes e redes. Um agente em "Modo Build" está fisicamente impedido de acessar a internet aberta, prevenindo distrações e exfiltração de dados.
- **Ralph Loop:** Um padrão de execução recursiva e persistente. O agente não aborta ao encontrar um erro; ele entra em um ciclo OODA (Observe-Orient-Decide-Act) de _Tentativa → Erro → Análise de Stderr → Correção → Retentativa_, governado por um limite rígido de iterações (`MAX_ITERS`).
- **Spec-Lock:** Um mecanismo de _Guardrail_ lógico. O Agente de Desenvolvimento é proibido, via instrução de sistema e validação de script, de gerar código para qualquer funcionalidade que não possua um arquivo de especificação técnica (`.openspec/features/*.md`) aprovado e com hash verificado.

### 1.3 Ferramentas de Amplificação

- **Armada de Busca:** Uma federação de motores de inferência externa (Exa, Tavily, DuckDuckGo, ArXiv), orquestrada por uma Meta-Skill (`DeepResearch`) que roteia a query baseada na densidade informacional necessária (Fato vs. Conceito vs. Dado Recente).
- **Toonificação:** O processo algorítmico de compressão de entropia. Transforma dados estruturados verbosos (JSON/CSV) e textos longos em formato TOON (Token-Oriented Object Notation), reduzindo o consumo da janela de contexto em 40-60% sem perda semântica significativa.
- **Drift-Check:** Um processo de validação executado na fase de auditoria (`/05-verify`) que compara a AST (Abstract Syntax Tree) do código implementado com as definições da Spec. Se houver divergência (ex: função exportada não documentada na Spec), o build é rejeitado.

## 2. Manifesto Arquitetural: Axiomas e Trade-offs

A arquitetura SODA não busca ser "fácil"; busca ser **resiliente**. Cada decisão de design é uma negociação calculada entre flexibilidade e controle.

### 2.1 Leis Imutáveis (Axiomas)

1. **A Memória é Externa:** A mente humana falha e o contexto do LLM satura. Portanto, a "Verdade" nunca reside na conversa. A Verdade reside exclusivamente em artefatos persistentes (Arquivos `.md` e Grafos OpenMemory). O chat é apenas um meio de transporte temporário para alterações de estado.
2. **Determinismo sobre Velocidade:** O "Vibe Coding" (geração de código baseada em intuição rápida) é proibido para código de produção. A sequência **Spec → Test → Code** é inviolável. A velocidade é sacrificada em prol da auditabilidade e da manutenibilidade a longo prazo.
3. **Segregação de Deveres (SoD):** O Agente que planeja (Architect) nunca é o mesmo que executa (Developer), e este nunca é o mesmo que audita (Auditor). Esta separação previne a "cegueira do criador", onde o agente ignora seus próprios erros lógicos.
4. **Soberania da Infraestrutura:** O ambiente (Docker/WSL) dita as capacidades do Agente, não o contrário. Se o Agente precisa de uma ferramenta nova, ele deve solicitar uma alteração na infraestrutura (via PR no `docker-compose`), não "alucinar" que a ferramenta existe.

### 2.2 Matriz de Trade-offs (Custo da Excelência)

|   |   |   |   |
|---|---|---|---|
|**Decisão Arquitetural**|**Ganho Operacional (Upside)**|**Custo / Sacrifício (Downside)**|**Mecanismo de Mitigação**|
|**Estrutura "Clean Root"**|Redução massiva da carga cognitiva visual; portabilidade total do kernel `.agent`.|Complexidade nos scripts de _path resolution_. Agentes precisam ser instruídos explicitamente sobre onde buscar ferramentas.|Uso de _Environment Variables_ globais (`SODA_ROOT`) injetadas no contexto.|
|**Segmentação Modal (Docker)**|Segurança absoluta (Isolamento de Rede); prevenção de alucinação de ferramentas.|Fricção de latência (3-5s) na troca de fases (Container Cold Start).|Cache agressivo de imagens Docker e perfis pré-aquecidos para fases comuns.|
|**Ralph Loop (Persistência)**|Autonomia real; capacidade de resolver bugs complexos ("Grind") sem intervenção humana.|Risco de consumo excessivo de API/Tokens; tempo de espera ("Black Box") até o resultado.|Implementação do `CostGuard`: disjuntor que dispara por limite de custo ($) ou iterações.|
|**Hybrid Smolagents (Local)**|Economia de tokens; privacidade em lógica/dados; precisão matemática determinística.|Dependência de Hardware local (VRAM/RAM) no ambiente WSL; complexidade de manutenção do Ollama.|Fallback automático para modelos de nuvem baratos (Gemini Flash) se a VRAM local estiver saturada.|
|**Spec-Driven Development**|Eliminação de retrabalho estrutural; documentação viva e sempre sincronizada.|Perda de agilidade inicial. Impossibilidade de "testar rapidinho" sem burocracia.|Criação do comando `/fix-hot` para bypass controlado em emergências, com log obrigatório.|

## 3. Anatomia do Sistema: A Árvore de Diretórios Canônica

A estrutura de pastas não é apenas organizacional; é funcional. Ela atua como um circuito lógico onde dados fluem de _Intenção_ (Raiz) para _Definição_ (.openspec) para _Execução_ (.agent) e finalmente para _Produto_ (src).

```
/RAIZ_DO_PROJETO
│
├── PROJECT_CHARTER.md          # [AXIOMA] A Verdade Imutável (PID-Context). O Agente lê isso antes de qualquer ação.
├── src/                        # O Produto. Código-fonte da aplicação.
├── tests/                      # A Validação. Testes E2E (Playwright) e Unitários.
│
├── docs/                       # O Cérebro Humano (Legível & Auditável)
│   ├── management/             # PID_FULL.md, Matriz de Stakeholders, Orçamento, Logs de Decisão.
│   ├── research/               # Relatórios brutos da Armada (Exa/Aleph), Benchmarks Competitivos.
│   ├── architecture/           # Diagramas C4 (Mermaid), ADRs (Architectural Decision Records).
│   └── manuals/                # Manuais de usuário, Guias de Deploy e Playbooks de Operação.
│
├── .agent/                     # O "Kernel" do SODA (Oculto & Portável)
│   ├── AGENTS.md               # Memória Compartilhada. O "Caderno de Passagem de Turno" entre personas.
│   ├── task_plan.md            # A Memória Tática. Checklist sincronizado unidirecionalmente c/ GitHub.
│   │
│   ├── .soda/                  # Configurações Internas do Framework
│   │   ├── config.yaml         # Config global (LLM providers, timeouts, fallback policies).
│   │   ├── sops_toon/          # Versões comprimidas (TOON) dos SOPs para injeção eficiente no contexto.
│   │   └── docker-mcp/         # Configurações do Gateway Docker (Perfis, Redes, Volumes).
│   │
│   ├── memory/                 # Camada de Persistência de Dados (O Hipocampo)
│   │   ├── soda.db             # SQLite (Metadados de execução, status dos SOPs, métricas de performance).
│   │   ├── open_mem/           # OpenMemory Graph (Conhecimento Semântico, Relações entre Entidades).
│   │   └── vectors/            # ChromaDB (Embeddings de Docs e Código para RAG tático).
│   │
│   ├── scripts/                # Executáveis Auxiliares (Python/Bash)
│   │   ├── bootstrap.sh        # Setup inicial (Idempotente e Auto-Corretivo).
│   │   ├── ralph_loop.py       # Motor de Execução (Lógica de Retentativa e Análise de Erro).
│   │   ├── smol_tools/         # Scripts locais (Scraping, Math, Validation, Data Gen).
│   │   └── cost_guard.py       # Monitor de consumo de API (Disjuntor Financeiro).
│   │
│   ├── agents/                 # Definições de Personas (.yaml) -> PM, Architect, Dev, SM, Auditor.
│   ├── skills/                 # Skills (.py/.js) -> Ag-Kit Refatorado + Novas Skills (Wrappers MCP).
│   ├── workflows/              # Prompts Mestres (/00, /01, /fase-check...). O "Código-Fonte" do Processo.
│   └── rules/                  # Regras Dinâmicas (Injetadas por fase para moldar o comportamento).
│
├── .openspec/                  # Especificações Técnicas (Output do Arquiteto)
│   ├── global/                 # Specs transversais (Auth, Error Handling, Logging, I18n).
│   └── features/               # Specs por funcionalidade (UserLogin.md, PaymentGateway.md).
│
└── .gemini/                    # (Fora do Git - Na Home do Usuário)
    └── GEMINI.md               # Constituição Global (Segurança, Ética, Chaves, Aliases de Sistema).
```

## 4. A Super-Stack (SODA Toolchain): Mecânica Detalhada

Cada ferramenta foi selecionada não por popularidade, mas por sua capacidade de resolver um problema específico de orquestração agêntica.

### 4.1 Núcleo de Inteligência & Memória

|   |   |   |   |
|---|---|---|---|
|**Componente**|**Ferramenta**|**Mecânica de Funcionamento**|**Nuances & Casos de Borda**|
|**Memória Semântica**|**OpenMemory**|Utiliza um Graph Database para armazenar fatos como triplas (Sujeito-Predicado-Objeto). Ex: `(AuthService, depende_de, JWT)`. Permite consultas complexas: "O que quebra se eu mudar o JWT?".|**Nuance:** Não usar para armazenar logs ou blobs. Apenas conhecimento estruturado. Requer reindexação periódica.|
|**Memória Tática**|**Plan-With-Files**|O agente lê o arquivo `task_plan.md`, identifica a próxima tarefa não marcada `[ ]`, executa e marca `[x]`.|**Borda:** Conflitos de merge se múltiplos agentes tentarem escrever simultaneamente (mitigado por bloqueio de arquivo no script Ralph).|
|**Leitura Profunda**|**Aleph (RLM)**|Executa uma travessia recursiva em árvore de diretórios ou URLs. Resume nós folha, agrupa resumos em nós pais, até a raiz.|**Contra-indicação:** Custo proibitivo para repositórios > 100MB. Usar apenas quando a resposta exigir compreensão sistêmica ("Entenda a arquitetura").|
|**Navegação de Código**|**Heuristic-MCP**|Constrói um _Call Graph_ e _Symbol Index_ localmente. Permite ao agente navegar por referências ("Go to Definition") sem ler o arquivo inteiro.|**Nuance:** Requer tempo de indexação inicial (`post-install`).|
|**Otimização**|**MCP TOON**|Middleware que intercepta leituras de arquivos `.json`, `.csv`, `.xml`. Converte para sintaxe TOON (removendo aspas redundantes e chaves) antes de entregar ao LLM.|**Nuance:** Perda de tipagem estrita em alguns casos, mas recuperável pelo contexto do LLM.|

### 4.2 Execução & Lógica

|   |   |   |   |
|---|---|---|---|
|**Componente**|**Ferramenta**|**Mecânica de Funcionamento**|**Nuances & Casos de Borda**|
|**Motor de Persistência**|**Ralph Loop**|Script Python que encapsula a chamada ao LLM. Monitora `stderr`. Se `exit_code != 0`, realimenta o erro no prompt e solicita correção. Mantém contador de iterações.|**Borda:** "Loop da Morte" (o agente tenta a mesma correção inválida 15x). Mitigado por _Temperature Decay_ (aumentar criatividade a cada erro).|
|**Lógica Determinística**|**Smolagents**|Agentes Python locais (CodeAgents). O LLM escreve código Python para resolver o problema, e o Smolagents executa em sandbox local.|**Uso:** Cálculos matemáticos, Web Scraping preciso, Validação de JSON, Geração de Dados de Teste. Não usar para arquitetura.|
|**Navegação Web**|**Playwright**|Servidor MCP que lança um browser headless. Renderiza DOM completo (JS/SPA), executa interações (clique, input) e extrai texto visível.|**Nuance:** Mais lento que `fetch`. Usar para sites dinâmicos ou testes E2E.|
|**Browser Rápido**|**Native Browser**|Ferramenta nativa do Antigravity. Realiza requisições HTTP simples e parsing de HTML estático.|**Uso:** Leitura de documentação estática, blogs, artigos. Rápido e barato.|

### 4.3 Armada de Busca (Meta-Skill: `DeepResearch`)

O Agente não escolhe a ferramenta manualmente; ele invoca a Skill `DeepResearch` com sua dúvida. A Skill (script Python) classifica a intenção e roteia:

1. **Fatos Rápidos / Trivialidades:** Roteia para `DuckDuckGo`. (Custo Zero, Baixa Latência).
2. **Pesquisa Conceitual / Exploratória:** Roteia para `Exa` (Metaphor). Retorna clusters de recursos baseados em significado, não palavras-chave.
3. **Dados em Tempo Real / Notícias:** Roteia para `Tavily`. (Otimizado para conteúdo fresco e menos alucinação).
4. **Profundidade Acadêmica / Algoritmos:** Roteia para `ArXiv`.
5. **Documentação Técnica:** Roteia para `Docfork`. (Converte sites de doc inteiros em Markdown limpo para ingestão RLM).

### 4.4 Infraestrutura & Gestão

- **Gateway:** `Docker MCP` - Atua como um _Switch_ de Rede. Baseado no comando `switch_profile`, ele executa `docker network connect/disconnect` e `docker start/stop` para alterar o ambiente físico do agente.
- **Gestão:** `GitHub PM MCP` - Atua como um espelho. Monitora o arquivo `task_plan.md`. Se uma linha mudar de `[ ]` para `[x]`, ele busca a Issue correspondente via API do GitHub e a fecha.
- **Spec:** `OpenSpec` - Framework de definição. Valida se o arquivo Markdown segue o schema esperado (seções obrigatórias: Contexto, Interfaces, Testes).

## 5. Ciclo de Vida SODA (Lifecycle): O Pipeline de Engenharia

O desenvolvimento deixa de ser um ato artístico e torna-se um processo industrial de 6 estágios sequenciais, complementado por intervenções ad-hoc.

### 5.1 Estágios Sequenciais (The Happy Path)

#### **`/00-bootstrap` (Infraestrutura)**

- **Persona:** `SystemOps` (Rigoroso, Infra-focus).
- **Mecânica Detalhada:**
    
    1. Verifica integridade do ambiente WSL2 e Drivers GPU (para Smolagents local).
    2. Valida instalação do Docker e permissões de Socket.
    3. Instala stack de linguagem (`uv` para Python, `npm` para Node).
    4. Materializa a árvore de diretórios "Clean Root".
    5. Gera chaves SSH para GitHub e cria o `.env` a partir de um template seguro, solicitando preenchimento humano.
    6. Inicializa o `soda.db` (SQLite) com o esquema de rastreamento de SOPs.
        
- **DoD (Definition of Done):** Comando `soda health-check` retorna tudo verde.

#### **`/01-inception` (Ideação & Estratégia)**

- **Persona:** `ProductManager` + `Analyst` (Criativo, Business-focus).
- **Inputs:** Sessão de Entrevista Socrática com o Humano ("O que estamos construindo? Para quem? Por que?").
- **Ferramentas:** Exa (Pesquisa de Mercado), OSP Marketing (Value Map), Time.
- **SOPs Ativos:** 01 (Brief), 02 (Viabilidade).
- **Mecânica Detalhada:**
    
    1. O Agente conduz o brainstorming e preenche o Value Map.
    2. Utiliza `Exa` para buscar concorrentes ou soluções similares.
    3. Compila o **PID-Completo** em `docs/management/PID_FULL.md`.
    4. Executa um processo de destilação (Summarization) para gerar o **PID-Context** (`PROJECT_CHARTER.md`) na raiz, contendo apenas os axiomas técnicos e de negócio.
        
- **DoD:** `PROJECT_CHARTER.md` assinado digitalmente (hash) pelo Humano.

#### **`/02-blueprint` (Arquitetura & Especificação)**

- **Persona:** `Architect` (Técnico, Abstrato).
- **Inputs:** `PROJECT_CHARTER.md`.
- **Ferramentas:** OpenSpec, RLM (Leitura de Docs), GitHub PM, Heuristic (se projeto legado).
- **SOPs Ativos:** 03 (Arch), 04 (Data), 05 (API).
- **Mecânica Detalhada:**
    
    1. O Arquiteto lê o Charter e identifica as tecnologias necessárias.
    2. Invoca o **RLM** para ler a documentação oficial ("latest") dessas tecnologias na web, garantindo que não usará APIs depreciadas.
    3. Utiliza o **OpenSpec** para gerar arquivos de especificação em `.openspec/features/` (ex: `AuthSystem.md`, `DatabaseSchema.md`).
    4. Deriva o `task_plan.md` (Checklist Técnico) a partir das Specs.
    5. Invoca o **GitHub PM** para criar as Issues correspondentes no board.
        
- **DoD:** Todas as funcionalidades do Charter possuem uma Spec correspondente e uma Issue no GitHub.

#### **`/03-setup` (Preparação de Ambiente)**

- **Persona:** `DevOps` (Prático, Automação).
- **Ferramentas:** Docker MCP, FileSystem.
- **Mecânica Detalhada:**
    
    1. Inicializa o Repositório Git (se novo).
    2. Gera o `docker-compose.yml` da aplicação (DB, Cache, Filas) baseado na Spec de Arquitetura.
    3. Configura o framework de testes (Playwright/Pytest/Jest) e os scripts de CI/CD locais.
        
- **DoD:** `docker compose up` sobe o ambiente local sem erros.

#### **`/04-construct` (O Loop de Construção - The Grind)**

- **Persona:** `Developer` (Tenaz, Focado).
- **Ferramentas:** Ralph Loop, Smart Coding, TOON, Smolagents.
- **SOPs Ativos:** 09 (TDD), 10 (Code).
- **Mecânica Detalhada (Ralph Loop):**
    
    1. O Agente bloqueia (lock) o `task_plan.md` e lê a próxima tarefa pendente.
    2. Lê a Spec associada em `.openspec/`.
    3. **(Fase TDD):** Escreve um teste que falha (Red).
    4. **(Fase Lógica):** Se houver necessidade de cálculo ou geração de dados, invoca **Smolagents** localmente.
    5. **(Fase Implementação):** Escreve o código para passar no teste.
    6. **(Fase Verificação):** Executa os testes.
        
        - Se falhar: Lê o `stderr`, analisa o erro, corrige o código. Incrementa contador de iterações.
        - Se atingir 15 iterações: Aborta e pede ajuda humana.
            
    7. **(Fase Conclusão):** Passou? Commita (Conventional Commits), marca `[x]` no Markdown e comenta na Issue do GitHub.
        
- **DoD:** Código funcional em `/src`, coberto por testes, sem linter errors.

#### **`/05-verify` (Auditoria & Qualidade)**

- **Persona:** `Auditor` (Crítico, Detalhista - ARC Protocol).
- **Ferramentas:** Heuristic-MCP, Linter, Security Scanner, Playwright.
- **SOPs Ativos:** 11 (Review), 14 (Sec), 15 (Perf).
- **Mecânica Detalhada:**
    
    1. **Drift-Check:** Analisa se o código implementado contém funções ou rotas não definidas na Spec.
    2. **Análise Estática:** Executa Linters e Scanners de Segurança (busca por segredos, injeção).
    3. **Verificação Semântica:** O código obedece aos "Non-Goals" do `PROJECT_CHARTER.md`?
    4. **Testes de Regressão:** Executa a suíte completa (Unitários + E2E via Playwright).
        
- **DoD:** Relatório de Qualidade verde ou lista de _blockers_ para correção.

#### **`/06-release` (Entrega & Deploy)**

- **Persona:** `ReleaseManager` (Cauteloso, Process-oriented).
- **Mecânica Detalhada:**
    
    1. Gera Changelog Semântico baseado nos commits.
    2. Realiza Merge da branch de feature para `main` (ou `develop`).
    3. Tagueia a versão (Git Tag).
    4. Dispara o pipeline de Deploy (se configurado).
    5. Atualiza o **OpenMemory** com lições aprendidas (Retrospectiva Automática).
        
- **DoD:** Código em produção (ou staging) e documentação atualizada.

### 5.2 Comandos Ad-Hoc (Flexibilidade Controlada)

- **`/fase-check`:** O Agente Scrum Master analisa o estado atual (`soda.db` e `task_plan.md`) e recomenda: "Estamos 100% na Fase 02. As Specs X e Y estão prontas. Recomendo iniciar `/03-setup`."
- **`/research "Tópico"`:** Dispara a Armada de Busca (Exa/Aleph) de forma assíncrona. Gera um relatório em Markdown em `docs/research/` e avisa quando pronto.
- **`/refactor "Alvo"`:** Inicia um mini-loop Ralph focado exclusivamente em melhoria de código (Clean Code), garantindo que o comportamento externo (testes) não mude.
- **`/fix-hot`:** Permite bypassar o **Spec-Lock** para correções críticas de produção (Hotfix). Exige que o usuário forneça uma justificativa que será logada imutavelmente em `docs/management/hotfix_log.md`.
- **`/status`:** Gera um relatório de progresso textual rico, comparando o planejado (`task_plan.md`) com o realizado (GitHub Issues) e identificando gargalos.

## 6. Governança e Constituição (`GEMINI.md`)

Este arquivo é o pilar da integridade do sistema. Ele define a "Personalidade Jurídica" da IA.

Localização: `~/.gemini/GEMINI.md`

```
# SODA CONSTITUTION (GLOBAL)

## 0. PRIME DIRECTIVE
Você é um sistema agêntico operando sob o framework SODA (Spec-Oriented Dockerized Architecture).
Sua função primária é garantir a integridade do sistema, a segurança dos dados e a aderência estrita à especificação.
A velocidade é secundária à correção. A autonomia é secundária à segurança.

## 1. HIERARQUIA DA VERDADE (EPISTEMOLOGIA)
1. `PROJECT_CHARTER.md` (Lei Suprema - Raiz do Projeto). Se houver conflito, este arquivo vence.
2. `.openspec/` (Contrato Técnico). O código deve espelhar a Spec.
3. `task_plan.md` (Ordem do Dia). O que deve ser feito agora.
4. Histórico do Chat (Volátil e não confiável para fatos).

## 2. REGRAS DE OURO (SAFETY RAILS)
- **Commit Guard:** NUNCA commite na branch `main` diretamente. Use Feature Branches.
- **Loop Guard:** NUNCA execute loops (Ralph) sem definir um `MAX_ITERATIONS` (Padrão: 15) e um critério de parada claro.
- **Volume Guard:** NUNCA leia arquivos > 500KB sem usar `TOON` ou `Heuristic-Search`.
- **Logic Guard:** SEMPRE use `smolagents` (local) para cálculos matemáticos, scraping leve ou geração de massa de dados. Não confie na sua própria aritmética.
- **Context Guard:** SEMPRE verifique `AGENTS.md` ao iniciar uma nova sessão para recuperar o contexto da "Passagem de Turno".

## 3. SEGURANÇA & PRIVACIDADE
- **Secret Zero:** Segredos (API Keys, Passwords) vivem exclusivamente no `.env`. Nunca no código, nunca no chat, nunca no git.
- **Audit:** Qualquer nova dependência externa (pip/npm) deve ser validada via `safety-check` ou `audit` antes da instalação.
- **PII:** Não envie PII (Personal Identifiable Information) para logs ou saídas de console.

## 4. PROTOCOLO DE FERRAMENTAS
- Use `DeepResearch` para dúvidas externas. Não alucine fatos.
- Use `Heuristic` para navegação de código. Não leia o repositório inteiro.
- Use `SwitchProfile` imediatamente se perceber que está no modo errado (ex: tentando codar no modo research).
```

## 7. Implementação: Guia de Bootstrap

Para materializar o SODA v1.1 agora:

1. **Clone o Kernel:**
    
    Crie/Forke o repositório base contendo a estrutura `.agent` refatorada (sem o lixo do BMAD original).

    ```
    git clone git@github.com:seu-user/soda-kernel.git .agent
    ```

2. **Setup da Infra:**
    
    Garanta que o Docker Desktop está rodando no WSL2 e que o `docker-mcp-gateway` está configurado.
    
3. **Alias de Terminal:**
    
    Adicione ao seu `.bashrc` ou `.zshrc` para invocar a CLI do SODA facilmente:

    ```
    alias soda="python3 .agent/scripts/soda_cli.py"
    ```

4. **Inicie um Novo Projeto:**

    ```
    mkdir meu-projeto-soda
    cd meu-projeto-soda
    # Copia o kernel para o projeto
    cp -r /path/to/soda-kernel/ .agent
    # Inicia a sequência de bootstrap
    soda /00-bootstrap
    ```