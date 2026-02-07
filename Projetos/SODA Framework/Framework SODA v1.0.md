---
sticker: lucide//circle-ellipsis
---
# SODA Framework v1.0 (refinado)

## Sistema Operacional de Desenvolvimento Agêntico

**(Spec-Oriented Dockerized Architecture)**

**Versão:** 1.0 (Release Candidate)

**Status:** Definição Canônica

**Contexto:** Otimizado para Alta Performance Cognitiva (AH/SD) e Suporte Executivo (TDAH) via Google Antigravity & WSL.

## 1. Glossário Técnico & Definições Axiomáticas

Para eliminar a ambiguidade semântica entre Operador Humano e Agentes Sintéticos, estabelecemos este vocabulário controlado:

- **Clean Root (Raiz Limpa):** Princípio de design onde a raiz do projeto contém apenas artefatos de _Intenção Humana_ (`PROJECT_CHARTER.md`) e _Produto Final_ (`src`), encapsulando toda a complexidade operacional em diretórios ocultos (`.agent`).
    
- **PID-Context (Project Intent Document - Context):** O arquivo `PROJECT_CHARTER.md`. É a "Constituição" injetada no System Prompt de todos os agentes. Define as leis imutáveis do projeto.
    
- **PID-Full (Project Intent Document - Full):** O documento socrático completo (`docs/management/PID_FULL.md`), contendo nuances, histórico e detalhes para auditoria humana, mas denso demais para o contexto contínuo da IA.
    
- **Memória Tática (`task_plan.md`):** A "Memória RAM" persistida. Um checklist vivo que serve de ponte entre o planejamento (OpenSpec) e a execução (Ralph), sincronizado unidirecionalmente com o GitHub Projects.
    
- **Segmentação Modal:** O uso do Docker MCP Gateway para isolar fisicamente conjuntos de ferramentas. Um agente em "Modo Build" não possui rota de rede para a internet aberta, prevenindo distrações e exfiltração.
    
- **Ralph Loop:** Um padrão de execução autônoma onde o agente entra em um ciclo de _Tentativa → Erro → Correção → Retentativa_ até que os testes passem ou o _CostGuard_ (limite de segurança) seja acionado.
    
- **Armada de Busca:** Uma federação de ferramentas de pesquisa (Exa, Tavily, DuckDuckGo, ArXiv) orquestrada por uma Meta-Skill (`DeepResearch`) que decide qual fonte usar baseada na densidade da pergunta.
    
- **Toonificação:** Processo de compressão de dados via formato TOON (Token-Oriented Object Notation), reduzindo o consumo de tokens em até 60% para arquivos estruturados grandes.
    
- **Spec-Lock:** Guardrail que impede a geração de código para qualquer funcionalidade que não possua um arquivo de especificação correspondente (`.openspec/features/*.md`).
    

## 2. Princípios Fundamentais (Axiomas e Trade-offs)

### 2.1 As Leis Imutáveis do SODA

1. **Higiene de Contexto é Soberania:** O contexto do LLM é um recurso finito e caro. Agentes nunca devem ter acesso a todo o histórico. O contexto deve ser cirúrgico, injetado _just-in-time_ via RLM ou leitura seletiva.
    
2. **A Verdade Habita em Arquivos:** O chat é efêmero; o arquivo é eterno. Todo estado crítico (progresso, decisão arquitetural, erro conhecido) deve ser persistido em Markdown ou no OpenMemory Graph.
    
3. **Execução Determinística (Anti-Vibe):** Nenhuma linha de código de produção é escrita sem uma Spec (OpenSpec) associada e um Teste (TDD) que falhe antes de passar.
    
4. **Externalização da Função Executiva:** O Framework assume a carga de lembrar "o que vem a seguir", organizar arquivos e gerenciar dependências. O Humano foca exclusivamente na **Estratégia** e **Criatividade**.
    

### 2.2 Análise de Trade-offs

|   |   |   |
|---|---|---|
|**Decisão de Design**|**O que ganhamos? (Prós)**|**O que sacrificamos? (Contras)**|
|**Arquitetura "Clean Root"**|Clareza mental absoluta; portabilidade do kernel `.agent` entre projetos.|Complexidade nos scripts de resolução de caminhos (agentes devem saber navegar para dentro de `.agent/`).|
|**Segmentação Modal (Docker)**|Segurança robusta; impossibilidade de alucinação de ferramentas de fases erradas.|Fricção de tempo de _startup_ (3-5s) ao trocar de fases (desligar container de pesquisa, ligar container de dev).|
|**Ralph Loop (Persistência)**|Autonomia real; o agente resolve problemas difíceis ("grind") sem supervisão.|Risco de consumo de API se os _Guardrails_ falharem; tempo de espera maior para resultados.|
|**Hybrid Smolagents (Local)**|Economia massiva de tokens; privacidade em lógica/dados; precisão matemática.|Dependência de Hardware local (VRAM/RAM) no ambiente WSL; necessidade de setup do Ollama.|
|**Spec-Driven Development**|Eliminação de retrabalho por mal-entendido; documentação sempre atualizada.|Velocidade inicial reduzida. Não é possível fazer "só um scriptzinho rápido" sem criar uma spec.|

## 3. Arquitetura de Diretórios ("Clean Root")

Esta estrutura é mandatória. Scripts de bootstrap devem validar sua integridade.

```
/RAIZ_DO_PROJETO
│
├── PROJECT_CHARTER.md          # [AXIOMA] A Verdade Imutável (PID-Context). O Agente lê isso primeiro.
├── src/                        # O Produto. Código-fonte da aplicação.
├── tests/                      # A Validação. Testes E2E (Playwright) e Unitários.
│
├── docs/                       # O Cérebro Humano (Legível & Auditável)
│   ├── management/             # PID_FULL.md, Matriz de Stakeholders, Orçamento.
│   ├── research/               # Relatórios brutos da Armada (Exa/Aleph), Benchmarks.
│   ├── architecture/           # Diagramas C4 (Mermaid), ADRs (Decision Records).
│   └── manuals/                # Manuais de usuário e Guias de Deploy.
│
├── .agent/                     # O "Kernel" do SODA (Oculto & Portável)
│   ├── AGENTS.md               # Memória Compartilhada (Contexto vivo entre sessões/personas).
│   ├── task_plan.md            # A Memória Tática. Checklist sincronizado unidirecionalmente c/ GitHub.
│   │
│   ├── .soda/                  # Configurações Internas do Framework
│   │   ├── config.yaml         # Config global (LLM providers, timeouts).
│   │   ├── sops_toon/          # Versões comprimidas dos SOPs (Prompt Skills) para injeção.
│   │   └── docker-mcp/         # Configurações do Gateway Docker (Perfis/Redes).
│   │
│   ├── memory/                 # Camada de Persistência de Dados
│   │   ├── soda.db             # SQLite (Metadados de execução, status dos SOPs).
│   │   ├── open_mem/           # OpenMemory Graph (Conhecimento Semântico/Decisões).
│   │   └── vectors/            # ChromaDB (Embeddings de Docs e Código para RAG).
│   │
│   ├── scripts/                # Executáveis Auxiliares (Python/Bash)
│   │   ├── bootstrap.sh        # Setup inicial (Idempotente).
│   │   ├── ralph_loop.py       # Motor de Execução (Lógica de Retentativa).
│   │   ├── smol_tools/         # Scripts locais (Scraping, Math, Validation).
│   │   └── cost_guard.py       # Monitor de consumo de API.
│   │
│   ├── agents/                 # Definições de Personas (.yaml) -> PM, Architect, Dev, SM.
│   ├── skills/                 # Skills (.py/.js) -> Ag-Kit Refatorado + Novas Skills.
│   ├── workflows/              # Prompts Mestres (/00, /01, /fase-check...).
│   └── rules/                  # Regras Dinâmicas (Injetadas por fase).
│
├── .openspec/                  # Especificações Técnicas (Output do Arquiteto)
│   ├── global/                 # Specs transversais (Auth, Error Handling, Logging).
│   └── features/               # Specs por funcionalidade (UserLogin.md, PaymentGateway.md).
│
└── .gemini/                    # (Fora do Git - Na Home do Usuário)
    └── GEMINI.md               # Constituição Global (Segurança, Ética, Chaves, Aliases).
```

## 4. A "Super-Stack" de Ferramentas (Toolchain)

Definição precisa de cada componente, sua função axiomática e interações.

### 4.1 Núcleo de Inteligência & Memória

|   |   |   |   |
|---|---|---|---|
|**Componente**|**Ferramenta Escolhida**|**Função Axiomática**|**Implementação**|
|**Memória Longa**|**OpenMemory**|Armazenar o _Grafo de Decisões_. "Por que escolhemos JWT em vez de OAuth?".|Servidor MCP com backend GraphDB. Consultado via `query_memory`.|
|**Memória Tática**|**Plan-With-Files**|Manter o foco imediato (`task_plan.md`).|Leitura direta de arquivo Markdown. Agente marca `[x]` ao concluir.|
|**Leitura Profunda**|**Aleph (RLM)**|Leitura recursiva de Docs/Repos gigantes ("Contexto Infinito").|Servidor MCP que varre diretórios recursivamente e sumariza nós.|
|**Navegação de Código**|**Heuristic-MCP**|Busca Semântica e Call Graphs ("Quem chama a função Login?").|Servidor MCP que indexa símbolos e referências cruzadas localmente.|
|**Otimização**|**MCP TOON**|Compressão de Tokens para JSON/CSV.|Middleware que converte dados estruturados > 50KB antes da leitura.|

### 4.2 Execução & Lógica

|   |   |   |   |
|---|---|---|---|
|**Componente**|**Ferramenta Escolhida**|**Função Axiomática**|**Implementação**|
|**Persistência**|**Ralph Loop**|"Tenta até passar". Autonomia em tarefas difíceis.|Script Python (`ralph_loop.py`) que invoca o LLM em loop com feedback de erro.|
|**Lógica Local**|**Smolagents**|Processamento determinístico (Math, Scraping, Data Gen).|Agentes Python locais rodando via Ollama (ou Gemini Flash) para tarefas lógicas.|
|**Navegação Web**|**Playwright**|Navegação real (JS/SPA) e Testes E2E.|Servidor MCP com browser headless para ler sites complexos e rodar testes.|
|**Browser Rápido**|**Native Browser**|Leitura rápida de sites estáticos.|Ferramenta nativa do Antigravity/Gemini para consumo de texto simples.|

### 4.3 Armada de Busca (Meta-Skill: `DeepResearch`)

Uma única skill roteia a necessidade do agente para o provedor correto:

- **Fatos Rápidos:** `DuckDuckGo` (Custo Zero).
    
- **Pesquisa Semântica:** `Exa` (Metaphor) - Para entender conceitos ("Melhores libs de grafos").
    
- **Notícias/Recente:** `Tavily` - Para dados em tempo real.
    
- **Acadêmico:** `ArXiv` - Para papers e algoritmos complexos.
    
- **Documentação:** `Docfork` - Transforma sites de doc em Markdown limpo. (Fallback: `Rtfmbro`).
    

### 4.4 Infraestrutura & Gestão

- **Gateway:** `Docker MCP` - Gerencia perfis (`research`, `build`, `audit`) ligando/desligando containers.
    
- **Gestão:** `GitHub PM MCP` - Espelha o `task_plan.md` em Issues do GitHub.
    
- **Spec:** `OpenSpec` - Framework de definição técnica.
    
- **Tempo:** `Time MCP` - Fornece data/hora atual para logs e cronogramas.
    
- **Estratégia:** `OSP Marketing Tools` - Framework para Value Map e Estratégia (Uso na Fase 01).
    

## 5. Fluxo de Trabalho Granular (SODA Lifecycle)

O SODA substitui a improvisação por um pipeline de engenharia de 6 estágios sequenciais + comandos ad-hoc.

### 5.1 Comandos de Fase (Sequenciais)

#### **`/00-bootstrap` (Infra)**

- **Agente:** `SystemOps`.
    
- **Mecânica:**
    
    1. Valida ambiente WSL2, Docker e Drivers NVIDIA (se local).
        
    2. Instala dependências (`uv`, `npm`, `python`).
        
    3. Cria a estrutura "Clean Root" se não existir.
        
    4. Gera chaves SSH e `.env` template.
        
    5. Inicializa o `soda.db` (SQLite).
        
- **Saída:** Ambiente operacional ("Green Field").
    

#### **`/01-inception` (Ideação & Estratégia)**

- **Agente:** `ProductManager` + `Analyst`.
    
- **Inputs:** Entrevista Socrática com o Humano.
    
- **Ferramentas:** Exa (Pesquisa), OSP Marketing (Estratégia), Time.
    
- **SOPs Ativos:** 01 (Brief), 02 (Viabilidade).
    
- **Mecânica:**
    
    1. Brainstorming e definição de Value Map.
        
    2. Geração do **PID-Completo** em `docs/management/`.
        
    3. Destilação para **PID-Context** (`PROJECT_CHARTER.md`) na raiz.
        
- **Saída:** `PROJECT_CHARTER.md` aprovado.
    

#### **`/02-blueprint` (Arquitetura & Spec)**

- **Agente:** `Architect`.
    
- **Inputs:** `PROJECT_CHARTER.md`.
    
- **Ferramentas:** OpenSpec, RLM (Leitura Docs), GitHub PM, Heuristic (se legado).
    
- **SOPs Ativos:** 03 (Arch), 04 (Data), 05 (API).
    
- **Mecânica:**
    
    1. RLM lê documentação oficial das libs escolhidas no Charter.
        
    2. OpenSpec gera arquivos em `.openspec/features/`.
        
    3. Arquiteto cria o `task_plan.md` (Checklist Técnico Granular).
        
    4. GitHub PM espelha o plano em Issues no GitHub.
        
- **Saída:** Plano de Ação validado e Specs técnicas prontas.
    

#### **`/03-setup` (Preparação)**

- **Agente:** `DevOps`.
    
- **Ferramentas:** Docker MCP, FileSystem.
    
- **Mecânica:**
    
    1. Configura Repositório Git.
        
    2. Cria Docker Compose do projeto (DB, Cache).
        
    3. Configura estrutura de Testes (Playwright/Pytest/Jest).
        
    4. Configura CI/CD inicial.
        

#### **`/04-construct` (O Loop de Construção)**

- **Agente:** `Developer` (Ralph + Smolagents).
    
- **Ferramentas:** Ralph Loop, Smart Coding, TOON, Smolagents.
    
- **SOPs Ativos:** 09 (TDD), 10 (Code).
    
- **Mecânica (Ralph Loop):**
    
    1. Lê _uma_ tarefa pendente do `task_plan.md` e sua Spec associada.
        
    2. (TDD) Escreve teste que falha.
        
    3. (Smolagents) Se houver lógica matemática/dados, delega para script local.
        
    4. (LLM) Escreve a implementação.
        
    5. Roda testes. Falhou? Lê erro -> Corrige. (Max 15 iterações).
        
    6. Passou? Commita, marca tarefa no Markdown e comenta na Issue do GitHub.
        
- **Saída:** Código funcional em `/src` coberto por testes.
    

#### **`/05-verify` (Auditoria)**

- **Agente:** `Auditor` (ARC Protocol).
    
- **Ferramentas:** Heuristic-MCP, Linter, Security Scanner.
    
- **SOPs Ativos:** 11 (Review), 14 (Sec), 15 (Perf).
    
- **Mecânica:**
    
    1. Análise Estática (Linting/Security).
        
    2. Verificação Semântica: O código obedece ao `PROJECT_CHARTER.md`?
        
    3. Execução de Testes de Regressão e E2E (Playwright).
        
- **Saída:** Relatório de Qualidade ou Aprovação para Release.
    

#### **`/06-release` (Entrega)**

- **Agente:** `ReleaseManager`.
    
- **Mecânica:**
    
    1. Gera Changelog (Semântico).
        
    2. Merge para branch `main`.
        
    3. Dispara pipeline de Deploy.
        
    4. Atualiza OpenMemory com lições aprendidas (Retrospectiva).
        

### 5.2 Comandos Ad-Hoc (Assíncronos)

- **`/fase-check`:** O Scrum Master analisa o estado atual (`soda.db` e `task_plan.md`) e recomenda a transição de fase ou alerta sobre bloqueios.
    
- **`/research "Tópico"`:** Dispara a Armada de Busca e gera um resumo em `docs/research/`.
    
- **`/refactor "Alvo"`:** Inicia um mini-loop Ralph focado apenas em melhoria de código, sem alterar comportamento (protegido por testes).
    
- **`/fix-hot`:** Permite bypassar o Spec-Lock para correções críticas, exigindo log de justificativa em `docs/management/hotfix_log.md`.
    
- **`/status`:** Gera um relatório de progresso textual comparando `task_plan.md` com as Issues do GitHub.
    

## 6. Implementação Técnica

### 6.1 A Constituição (`GEMINI.md`)

Este arquivo deve residir em `~/.gemini/GEMINI.md` e ser referenciado nas configurações do IDE.

```markdown
# SODA CONSTITUTION (GLOBAL)

## 0. PRIME DIRECTIVE
Você é um sistema agêntico operando sob o framework SODA.
Sua função primária é garantir a integridade do sistema e a aderência à especificação.
A velocidade é secundária à correção.

## 1. HIERARQUIA DA VERDADE
1. `PROJECT_CHARTER.md` (Lei Suprema - Raiz do Projeto)
2. `.openspec/` (Contrato Técnico)
3. `task_plan.md` (Ordem do Dia)

## 2. REGRAS DE OURO (SAFETY)
- NUNCA commite na branch `main` diretamente.
- NUNCA execute loops (Ralph) sem definir um `MAX_ITERATIONS` (Padrão: 15).
- NUNCA leia arquivos > 500KB sem usar `TOON` ou `Heuristic`.
- SEMPRE use `smolagents` para cálculos matemáticos, scraping leve ou geração de dados.
- SEMPRE verifique `AGENTS.md` ao iniciar uma sessão para recuperar o contexto.

## 3. SEGURANÇA & PRIVACIDADE
- Segredos vivem exclusivamente no `.env`. Nunca no código, nunca no chat.
- Qualquer nova dependência deve ser validada via `safety-check` ou `audit`.
- Não envie PII (Personal Identifiable Information) para logs.

## 4. PROTOCOLO DE FERRAMENTAS
- Use `DeepResearch` para dúvidas externas.
- Use `Heuristic` para navegação de código.
- Use `SwitchProfile` se perceber que está no modo errado (ex: tentando codar no modo research).
```

### 6.2 Automação de Perfis (Docker Gateway)

O Agente possui uma Skill nativa `switch_profile` que interage com o Docker:

- **Perfil `research`:** Habilita containers da Armada de Busca, OpenMemory, Time. Desabilita acesso de escrita em `/src`.
    
- **Perfil `blueprint`:** Habilita OpenSpec, RLM, GitHub PM.
    
- **Perfil `construct`:** Habilita Ralph, Smolagents, Smart Coding, Linter. Desabilita Internet Aberta (apenas docs permitidos).
    
- **Perfil `audit`:** Habilita ARC, Heuristic, Playwright. Modo Read-Only em `/src`.
    

## 7. Segurança e Guardrails (Red Team Analysis)

Mitigação de riscos identificados durante a análise de robustez.

|   |   |
|---|---|
|**Vetor de Falha**|**Mitigação do Framework**|
|**Loop Infinito do Ralph** (Consumo de API)|**CostGuard:** Script wrapper que monitora o uso de tokens e encerra o processo após 15 iterações ou limite financeiro ($2.00) atingido.|
|**Alucinação de Requisitos**|**Spec-Lock:** O agente de Dev é proibido (via System Prompt e validação de script) de criar código para uma feature que não tenha um hash de arquivo `.md` correspondente em `.openspec/`.|
|**Destruição de Código**|**Diff-Check:** O Ralph Loop verifica o tamanho do Diff antes do commit. Se `deleted_lines > 50%` do arquivo ou `total_changes > 500 lines`, o loop pausa e exige aprovação humana explícita.|
|**Fadiga de Contexto**|**Context-Wiper:** A cada transição de fase (ex: `/02` para `/03`), o histórico do chat é limpo (reset), mantendo apenas os arquivos de memória (`AGENTS.md`, `task_plan.md`) como contexto.|
|**Injeção de Pacotes Maliciosos**|**Dependency-Gate:** O Agente não pode rodar `pip install` ou `npm install` arbitrariamente. Ele deve adicionar ao manifesto e solicitar aprovação na fase `/05-verify`.|

## 8. Guia de Bootstrap (Início Imediato)

Passos para instanciar um projeto SODA v1.0:

1. **Clone o Ag-Kit Base (SODA Refactored):**
    
    ```
    git clone git@github.com:seu-user/soda-ag-kit.git .agent
    ```
    
2. **Instale Docker MCP:** Certifique-se que o Docker Desktop está rodando no WSL2.
    
3. **Alias de Terminal:**
    
    No `.bashrc`: `alias soda="python3 .agent/scripts/soda_cli.py"`
    
4. **Inicie o Ciclo:**
    
    ```
    mkdir meu-novo-projeto
    cd meu-novo-projeto
    # Copia o kernel do SODA para o projeto atual
    cp -r /path/to/soda-kernel/ .agent
    # Inicia a infraestrutura
    soda /00-bootstrap
    ```