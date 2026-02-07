---
aliases:
  - "Antigravity: Ferramentas"
  - Organização e Uso
---
# Arquitetura da Fábrica de Software Autônoma: Estratégias de Integração Profunda para o Google Antigravity e Fluxos de Trabalho Agênticos

## Sumário Executivo

A evolução dos Ambientes de Desenvolvimento Integrado (IDEs) transitou da assistência passiva para a engenharia agêntica ativa. A plataforma Google Antigravity representa o ápice atual dessa mudança, oferecendo um "Controle de Missão" para orquestrar inteligências artificiais autônomas. A proposta tecnológica apresentada — uma amálgama sofisticada contendo **OpenSpec, Ag-Kit, Ralph, ARC Protocol, Smart Coding MCP e OpenMemory** — constitui uma "Super-Pilha" (_Super-Stack_) capaz de elevar o desenvolvimento de software a níveis inéditos de automação e precisão.

No entanto, a complexidade inerente à integração dessas ferramentas díspares apresenta desafios arquiteturais significativos. A sobrecarga de contexto ("Context Window Saturation"), a colisão de definições de ferramentas e a persistência de estado entre sessões agênticas exigem uma estratégia de orquestração rigorosa. A simples instalação simultânea de todos os componentes resultará em degradação cognitiva do modelo de linguagem (LLM).

Este relatório fornece um projeto técnico exaustivo para unificar essas tecnologias em uma "Fábrica de Software Autônoma". A análise estabelece que o sucesso depende de uma **Estratégia de Carregamento Modal** — ativando dinamicamente conjuntos de ferramentas com base na fase de desenvolvimento (Planejamento, Codificação, Verificação) — e da implementação de uma **Matriz de Rastreabilidade** robusta gerenciada pelo GitHub Project Manager MCP. A infraestrutura é ancorada no **OpenMemory** para retenção de conhecimento cognitivo e no **Docker MCP Gateway** para a gestão segura e eficiente de ferramentas, garantindo que a intenção humana, cristalizada no `PROJECT_CHARTER.md`, seja transmutada sistematicamente em software de produção verificável.

---

## 1. O Novo Paradigma do Desenvolvimento Agêntico

### 1.1 A Transição do "Vibe Coding" para a Engenharia Agêntica

O desenvolvimento assistido por IA passou por uma fase inicial caracterizada pelo "Vibe Coding" — a geração rápida de código baseada em intuição e prompts vagos, frequentemente resultando em protótipos funcionais mas arquiteturalmente frágeis. Ferramentas como o Cursor popularizaram essa abordagem. Contudo, para projetos de escala empresarial ou complexidade técnica elevada, essa metodologia é insuficiente. O erro compõe-se exponencialmente à medida que o contexto se perde.

O Google Antigravity introduz o conceito de "Agent-First". Diferente de um autocompletar glorificado, o Antigravity pressupõe que a IA é um ator autônomo capaz de planejar, executar, validar e iterar tarefas de engenharia complexas com intervenção humana mínima. Para capitalizar essa capacidade, não basta apenas "conversar" com a IDE; é necessário fornecer-lhe um ecossistema de ferramentas (o protocolo MCP - Model Context Protocol) e uma estrutura de governança rigorosa.

A "Super-Pilha" proposta pelo usuário visa preencher as lacunas do modelo base:

1. **Memória:** O modelo esquece decisões passadas? (Solução: OpenMemory).
2. **Contexto:** O código é muito grande para ler? (Solução: RLM, Smart Coding, TOON).
3. **Disciplina:** A IA alucina soluções? (Solução: ARC Protocol, OpenSpec).
4. **Persistência:** A IA desiste no meio da tarefa? (Solução: Ralph Loop).

### 1.2 Análise dos Componentes da Super-Pilha

Para compreender a integração, devemos primeiro dissecar a função de cada componente dentro da arquitetura proposta.

| **Componente**         | **Função Primária**             | **Papel na Arquitetura**                                                                                                       |
| ---------------------- | ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| **Google Antigravity** | Host / Kernel                   | A interface central que hospeda o Gerente de Agentes e o Editor de Código.                                                     |
| **Ag-Kit**             | Força de Trabalho               | Fornece _Personas_ especializadas (ex: Especialista em Backend, Auditor de Segurança) e habilidades modulares.                 |
| **OpenSpec**           | Planta Baixa (Blueprint)        | Define _o que_ construir através de uma estrutura de especificações rigorosa baseada em diretórios, eliminando a ambiguidade.  |
| **Ralph**              | Motor de Execução               | Um loop autônomo que persiste o estado em arquivos (`progress.txt`) para superar a amnésia de contexto e iterar até o sucesso. |
| **ARC Protocol**       | Supervisor de Processo          | Impõe disciplina (Analisar → Executar → Confirmar) e atua como uma camada de coordenação para prevenir erros de execução.      |
| **GitHub PM MCP**      | Gerente de Projeto              | Gerencia o quadro de tarefas, gera PRDs (Documentos de Requisitos do Produto) e rastreia requisitos até o código.              |
| **OpenMemory**         | Córtex (Memória de Longo Prazo) | Armazena conhecimento baseado em grafos sobre o projeto, prevenindo a perda de decisões arquiteturais entre sessões.           |
| **Smart Coding MCP**   | Olhos (Busca Semântica)         | Fornece compreensão semântica da base de código existente, permitindo busca por significado e não apenas palavras-chave.       |
| **RLM**                | Leitor (Contexto Infinito)      | Processa documentações massivas ou logs que excedem as janelas de contexto padrão através de decomposição recursiva.           |
| **Plan-With-Files**    | Metodologia de Planejamento     | Uma abordagem para manter o plano de execução em arquivos Markdown vivos, servindo como a "memória de trabalho" do agente.     |
| **MCP TOON**           | Otimizador de Tokens            | Comprime dados estruturados (JSON) em formatos otimizados para consumo por LLMs, economizando custos e contexto.               |
## 1.3 Referências de "Tools" conhecidas

Quero usar no **Antigravity** (e *Agente IDE*) como "**Tools**" *instaláveis* (alguns diretos, outros por MCP, é preciso analisar cada um, opções de instalar, refletir melhor maneira e então fazer não escolher a primeira opção apenas):
	- Smolagents: https://github.com/huggingface/smolagents (Possibilidade uso com LLM local (Ollama) em GPU de RTX 2060m 6gb VRAM) [Modelos Escolhidos: Qwen3:4B; Phi4-mini; Phi4-mini-reasoning; ]
	- OpenSpec: https://github.com/Fission-AI/OpenSpec
	- Plan-With-Files: https://github.com/OthmanAdi/planning-with-files
	- Ag-Kit: https://github.com/vudovn/antigravity-kit
	- Antigravity BMAD Config: https://github.com/salacoste/antigravity-bmad-config
	- RLM (Recursive Language Model): https://github.com/Hmbown/aleph
	- Heuristic-MCP (vai subistituir Smart Coding MCP: https://github.com/omar-haris/smart-coding-mcp)
	- ARC Protocol: https://github.com/AshishOP/arc-protocol
	- Ralph: https://github.com/snarktank/ralph
	- MCP TOON (JSON -> TOON): https://github.com/jellyjamin/TOON-context-mcp-server
	- OpenMemory: https://github.com/CaviraOSS/OpenMemory
	- GitHub Project Manager MCP: https://github.com/kunwarVivek/mcp-github-project-manage
	- DuckDuckGo
	- Exa
	- GitHub (Official)
	- Fetch (Reference)
	- Markitdown
	- Memory (Reference)
	- OSP Marketing Tools
	- Time (Reference)
	- YouTube Transcripts

---

## 2. Viabilidade Técnica e Estratégia de Coexistência

### 2.1 A Questão da Instalação Simultânea

**Dúvida:** _"Posso instalar todos? Posso ter ativos tantas coisas + tantos MCPs?"_

**Resposta Analítica:**

Tecnicamente, o protocolo MCP permite que um cliente (Antigravity) se conecte a um número ilimitado de servidores. No entanto, a resposta prática é **"Sim, mas não ativos simultaneamente no contexto global"**.

Existem duas limitações críticas que impedem a ativação simultânea e indiscriminada de todas as ferramentas listadas (que somariam mais de 50 a 100 ferramentas individuais):

1. **Saturação da Janela de Contexto (Context Window Saturation):** Cada ferramenta conectada injeta sua "definição de esquema" (nome, descrição, parâmetros) no prompt do sistema do LLM.
    - _Cálculo:_ 50 ferramentas × ~200 tokens por definição = ~10.000 tokens de _overhead_ constante por requisição. Isso reduz drasticamente o espaço disponível para o raciocínio do modelo e a manipulação de código, além de aumentar o custo e a latência.
2. **Confusão de Roteamento (Router Confusion):** Quando um LLM tem acesso a múltiplas ferramentas com funções sobrepostas (ex: `smart_search` do Smart Coding, `search_issues` do GitHub, `fetch` do Docker MCP, `exa_search`), a probabilidade de ele escolher a ferramenta errada ou alucinar parâmetros aumenta significativamente.

**Estratégia de Solução: Segmentação Modal via Docker Gateway** A solução recomendada é utilizar o **Docker MCP Toolkit** não apenas como um executor, mas como um **Gateway de Ferramentas**. Deve-se configurar "Perfis" ou carregar ferramentas dinamicamente baseando-se na fase do projeto.

- **Camada Core (Sempre Ativa):** OpenMemory (Memória), Smart Coding (Navegação de Código), FileSystem.
- **Camada Dinâmica (Ativada por Fase):**
    - _Fase de Pesquisa:_ Context7, DuckDuckGo, Exa, YouTube Transcripts.
    - _Fase de Planejamento:_ GitHub Project Manager, RLM (para leitura de docs).
    - _Fase de Execução:_ Ralph, Ag-Kit Skills, MCP TOON.
    - _Fase de Verificação:_ ARC Protocol.

### 2.2 Análise de Incompatibilidades e Conflitos

**Dúvida:** _"Existe alguma incompatibilidade no uso em conjunto?"_

Sim, existem conflitos estruturais e operacionais que devem ser mitigados manualmente.

#### 2.2.1 Conflito Ag-Kit vs. Antigravity BMAD Config

Ambos os projetos tentam ditar a estrutura da pasta oculta `.agent`.

- Ag-Kit : Utiliza a estrutura `.agent/agents`, `.agent/skills`, `.agent/workflows`. É mais completo em termos de _conteúdo_ (36 skills, 20 agentes).
- BMAD Config : Utiliza uma estrutura baseada em `.bmad-core` e gera workflows para `.agent/workflows`.
- **Resolução:** Não é possível rodar `ag-kit init` e depois aplicar o template do BMAD sem sobrescrever configurações.
- **Recomendação:** Utilize o **Ag-Kit** como a fundação estrutural, pois ele oferece a maior biblioteca de habilidades. Se desejar as funcionalidades do BMAD (como os comandos de slash `/pm`, `/architect`), você deve portar manualmente os arquivos de workflow do BMAD para a pasta `.agent/workflows` do Ag-Kit, ajustando os caminhos para as skills do Ag-Kit. Não tente usar os dois instaladores automaticamente.

#### 2.2.2 O Desafio WSL (Windows Subsystem for Linux)

O usuário está no Windows usando WSL. O Antigravity (a aplicação GUI) roda no Windows, mas o código e os terminais residem no WSL (Linux).

- **Problema de Caminhos (Pathing):** Servidores MCP que dependem de indexação de arquivos (como **Smart Coding MCP** e **OpenMemory**) podem falhar se rodarem no Windows tentando acessar arquivos de rede (`\\wsl.localhost\Ubuntu\...`). A latência de I/O de rede tornará a indexação inviável.
- **Solução:** É imperativo que os servidores MCP rodem **dentro** do ambiente Linux (seja via Docker dentro do WSL ou nativamente no WSL via `npm`/`python`).
- **Configuração no Antigravity:** O arquivo de configuração `mcp.json` deve apontar para comandos que executam no contexto do WSL. O uso do Docker MCP Toolkit resolve isso elegantemente, pois os containers rodam no motor Docker (que geralmente está ancorado no WSL2), garantindo acesso rápido ao sistema de arquivos via volumes montados.

#### 2.2.3 ARC Protocol vs. Ralph

Ambos parecem fazer a mesma coisa ("loops autônomos"), o que gera redundância.

- **Ralph:** É um "Executor de Loop Cego". Ele pega uma tarefa e tenta, tenta, tenta até passar nos testes. É focado na _persistência_ do progresso.
- **ARC:** É um "Protocolo de Gerenciamento". Ele define _quem_ faz o que (Pesquisador, Construtor, Auditor).
- **Integração:** Eles não são incompatíveis, mas ortogonais. Use o ARC como o "Chefe" que define a estratégia e o Ralph como o "Operário" que executa a codificação bruta dentro de uma das etapas do ARC. Não tente fazer o Ralph gerenciar o projeto inteiro; deixe isso para o GitHub PM.

---

## 3. A Estratégia de Memória e Contexto

A "memória" é o componente mais crítico para transformar um chatbot em um colega de trabalho. A escolha entre as opções apresentadas define a "inteligência a longo prazo" do projeto.

### 3.1 Escolha da Solução de Memória: OpenMemory vs. MCP-Memory

**Dúvida:** _"Preciso de ajuda para escolher (OpenMemory ou MCP-Memory)."_

**Veredito: OpenMemory.**

A comparação técnica revela uma diferença categórica de capacidade:

- MCP-Memory (ebailey78) : É essencialmente um bloco de notas glorificado. Ele salva informações em arquivos Markdown e usa uma busca de texto simples (Lunr.js). É útil para "lembretes de sessão", mas carece de profundidade estrutural.
- OpenMemory (CaviraOSS) : É uma **Arquitetura Cognitiva**.
    - **Estrutura de Grafos:** Armazena o conhecimento como um grafo de entidades e relacionamentos. Isso permite que a IA entenda que "Módulo de Login" _depende de_ "Serviço de Autenticação".
    - **Conhecimento Temporal:** Rastreia _quando_ uma memória foi criada. Isso é vital para saber se uma decisão arquitetural é atual ou obsoleta.
    - **Integração:** Lista explicitamente "Antigravity" como plataforma suportada e possui ferramentas robustas de MCP (`openmemory_store`, `openmemory_query`).
    - **Caso de Uso:** Utilize o OpenMemory para armazenar decisões arquiteturais ("Por que escolhemos PostgreSQL?"), preferências do usuário e o estado atual do entendimento do sistema.

### 3.2 Otimização de Tokens: TOON e RLM

Para viabilizar financeiramente e tecnicamente o uso de tantos agentes:

- MCP TOON : Deve ser usado como um **Middleware de Ingestão de Dados**. Sempre que o agente precisar ler um arquivo de dados grande (logs, JSON de seed, CSV de exportação) que seja maior que 50KB, a regra deve ser usar o `toon_convert`. A economia de tokens de 30-60% permite que arquivos que antes quebrariam o contexto possam ser analisados.
- RLM (Recursive Language Model) : É a solução para **Leitura Profunda**. Enquanto o `Smart Coding` busca trechos de código ("Onde está a função X?"), o RLM deve ser usado para ler documentações inteiras ou especificações longas ("Leia toda a pasta /docs e me diga se a arquitetura de segurança está conforme"). O RLM decompõe o documento recursivamente, permitindo um "contexto infinito" virtual.

---

## 4. O Centro de Comando: Gerenciamento de Projetos

A escolha da ferramenta de gerenciamento de projetos é fundamental para a orquestração Humano-IA.

### 4.1 GitHub Project Manager vs. Linear

**Dúvida:** _"Qual parece melhor usar? GitHub ou Linear?"_

**Recomendação Forte: GitHub Project Manager MCP.**

Embora o Linear seja uma ferramenta de UI excepcional para humanos, o **GitHub Project Manager MCP (por kunwarVivek)** é superior para fluxos de trabalho _agênticos_ pelas seguintes razões:

1. **Rastreabilidade Nativa (Traceability Matrix):** O GitHub PM MCP possui ferramentas específicas (`create_traceability_matrix`) para ligar Requisito (PRD) → Épico → História → Tarefa → Código. O Linear MCP é focado principalmente em operações CRUD (criar/editar tarefas). Para um agente autônomo, saber _por que_ está escrevendo um código (o requisito original) é crucial para evitar deriva.
2. **Geração de PRD Assistida:** A ferramenta inclui `generate_prd` e `parse_prd`, desenhadas especificamente para transformar ideias vagas em planos estruturados.
3. **Ecossistema Unificado:** Manter o gerenciamento (Issues/Projects) no mesmo lugar do código (Repo) facilita para ferramentas como ARC e Ralph verificarem o status sem cruzar fronteiras de autenticação complexas ou latências de API externas.
4. **Custo e Acesso:** Como mencionado pelo usuário, é mais integrado e reduz a fricção de ter mais uma assinatura SaaS (Linear), além de evitar a necessidade de configurar webhooks complexos para sincronização.

### 4.2 Organização de "Projetos Grandes"

**Dúvida:** _"Como idealizo/organizo Projetos, Pacotes, Histórias para eu acompanhar?"_

A estrutura hierárquica ideal para colaboração Humano-IA deve seguir este fluxo descendente de granularidade:

1. **Nível 0: Project Charter (`PROJECT_CHARTER.md`)**
    - _Dono:_ Humano.
    - _Conteúdo:_ A visão imutável, objetivos de negócio, restrições fundamentais ("Não usar Java").
    - _Função:_ A "Estrela do Norte" que evita que a IA se perca em detalhes irrelevantes.
2. **Nível 1: PRD (Documento de Requisitos do Produto)**
    - _Dono:_ Agente (Persona Product Manager) via GitHub PM MCP.
    - _Local:_ `/docs/product/`.
    - _Ação:_ O agente lê o Charter e gera um PRD detalhado. O humano aprova.
3. **Nível 2: Especificações Técnicas (OpenSpec)**
    - _Dono:_ Agente (Persona Arquiteto).
    - _Local:_ `.openspec/`.
    - _Ação:_ O agente converte o PRD em especificações técnicas rigorosas (arquivos `.md` estruturados). Isso define _como_ será feito.
4. **Nível 3: Plano de Tarefas (`task_plan.md`)**
    - _Dono:_ Metodologia "Plan-With-Files".
    - _Local:_ Raiz ou `/docs/planning/`.
    - _Ação:_ Uma lista de verificação viva. Cada item conecta uma Spec a uma ação de codificação.
5. **Nível 4: Execução (Ralph Loop)**
    - _Dono:_ Ralph.
    - _Ação:_ Pega um item do `task_plan.md` e itera até concluir.

Essa estrutura permite que o humano monitore o **Nível 0 e 1** (Estratégia) e aprove o **Nível 3** (Tática), enquanto a IA executa os **Níveis 2 e 4**.

---

## 5. Estruturação Padronizada do Repositório

**Dúvida:** _"Qual a melhor forma de estruturar de forma padronizada projetos? Como não deixar a raiz suja?"_

Para suportar essa "Super-Pilha" sem caos, propõe-se a seguinte estrutura de diretórios canônica, batizada de **"Hyper-Structured Agentic Scaffold"**:

/my-antigravity-project
│
├── PROJECT_CHARTER.md # [Humano] A visão, escopo e restrições imutáveis. (Raiz é essencial).
├── AGENTS.md # Arquivo de contexto dinâmico para handoff entre agentes (Ag-Kit/Ralph).
├── task_plan.md # O plano de execução vivo e atualizado.
│
├──.agent/ # [Ag-Kit + Antigravity] Configurações da IDE e Agentes.
│ ├── agents/ # Definições de Personas (backend.md, pm.md).
│ ├── skills/ # Habilidades técnicas locais (python-expert, clean-code).
│ ├── workflows/ # Workflows de comando slash (/refactor, /plan).
│ └── rules/ # Regras locais do projeto (.md).
│
├──.openspec/ # Especificações Técnicas.
│ ├── global/ # Specs sistêmicas (Design System, Auth).
│ └── features/ # Specs por funcionalidade (UserLogin, Payments).
│
├──.arc/ # Guardrails e Contratos.
│ ├── CONTRACTS.md # Regras inegociáveis de código (ex: "Sempre usar Type Hints").
│ └── TRACEABILITY.md # Mapa de rastreabilidade (Gerado pelo GitHub PM).
│
├──.github/ # Workflows do GitHub Actions.
│
├── scripts/
│ └── ralph/ # Configuração do loop autônomo.
│ ├── ralph.sh # O script executor.
│ └── prompt.md # O prompt base do loop.
│
├── docs/ #.
│ ├── product/ # PRDs gerados (v1, v2).
│ ├── architecture/ # Decisões (ADRs), Diagramas C4.
│ ├── knowledge/ # Dump do OpenMemory (opcional, para backup).
│ └── sops/ # Procedimentos Operacionais Padrão (SOPs) para a IA ler.
│
├── core/ # [Código Fonte] (Preferível a /src para sistemas modulares).
│ ├── api/
│ └── ui/
│
├── tests/ # Suite de Testes (Crítico para o Ralph saber se terminou).
│
└──.vscode/ # Configurações do Editor.
└── mcp.json # Configuração local dos servidores MCP.

### O Papel do `PROJECT_CHARTER.md`

Este arquivo é o substituto do "Vibe". Ele deve ficar na **Raiz**. Ele é o documento fundacional. Antes do OpenSpec definir _como_ construir, o Charter define _o que_ e _por que_. Ele ancora todas as gerações subsequentes de PRDs. Se a IA se perder, a regra deve ser: "Releia o Charter".

---

## 6. Fluxos de Trabalho Operacionais (Exemplos Práticos)

**Dúvida:** _"Apresente exemplos práticos de como usar (em conjunto)!"_

Aqui detalhamos como orquestrar as ferramentas em quatro fases distintas.

### Fase 1: Incepção (O "Porquê")

- **Objetivo:** Transformar uma ideia em um PRD estruturado.
- **Ferramentas Ativas:** Antigravity Editor, GitHub PM MCP, Fetch (Docker MCP).

**Fluxo:**

1. **Humano:** Cria `PROJECT_CHARTER.md` com a visão: "Criar um Dashboard Financeiro Pessoal que se conecta a APIs bancárias via Open Finance".
2. **Agente (Antigravity):**
    - Lê o Charter.
    - Ativa a tool `fetch` para pesquisar "Open Finance API standards Brazil".
    - Usa a tool `generate_prd` (do GitHub PM) passando o Charter e a pesquisa como contexto.
    - **Resultado:** Cria `docs/product/prd-finance-dashboard.md` com Histórias de Usuário, Requisitos Funcionais e Critérios de Aceite.
3. **Humano:** Revisa e aprova o PRD.
4. **Agente:** Usa `github_create_issues_from_prd` para popular o Quadro do GitHub Project.

### Fase 2: Planejamento e Arquitetura (O "Como")

- **Objetivo:** Criar especificações técnicas e o plano de batalha.
- **Ferramentas Ativas:** OpenSpec, Smart Coding MCP, Plan-With-Files.

**Fluxo:**

1. **Agente (Persona Arquiteto):**
    - Analisa o PRD.
    - Executa `openspec init`.
    - Cria pastas em `.openspec/features/transactions/`.
    - Escreve `spec.md` definindo os schemas JSON, endpoints da API e componentes React necessários.
2. **Verificação de Base Existente (Smart Coding):**
    - Se for um projeto existente, o Agente usa `smart_search` para ver como a autenticação já é feita.
    - Consulta o **OpenMemory**: `openmemory_query("Qual a biblioteca de UI padrão?")`.
3. **Plano de Ataque:**
    - O Agente cria `task_plan.md` na raiz.
    - Lista: `- [ ] Implementar Adapter Open Finance (Ref:.openspec/features/transactions)`.

### Fase 3: Execução (A Construção)

- **Objetivo:** Escrever código e passar nos testes.
- **Ferramentas Ativas:** Ralph, Ag-Kit Skills, MCP TOON, Smart Coding.

**Fluxo (O Loop do Ralph):**

1. **Início:** O Agente (ou Humano) invoca o script do Ralph apontando para o primeiro item do plano.
2. **Iteração:**
    - Ralph lê a tarefa e a Spec associada.
    - **Carregamento de Habilidade:** Identifica que é uma tarefa Python/FastAPI. Carrega a skill `.agent/skills/python-expert` (do Ag-Kit).
    - **Ingestão de Dados:** Precisa ler um arquivo de exemplo `bank_statement.json` de 2MB. Usa `toon_convert` para comprimi-lo.
    - **Codificação:** Escreve o código em `core/adapters/`.
    - **Validação:** Roda `pytest`. Falha.
    - **Correção:** Ralph lê o erro, ajusta o código. Roda `pytest`. Sucesso.

3. **Commit:** Ralph faz o commit `feat: implement open finance adapter` e marca o item como concluído no `task_plan.md`.

### Fase 4: Verificação e Memória (O Fechamento)

- **Objetivo:** Garantir qualidade e registrar aprendizado.
- **Ferramentas Ativas:** ARC Protocol, OpenMemory, GitHub PM.

**Fluxo:**

1. **Auditoria (ARC):** Antes do merge, o Agente executa `/arc-verify`. O sub-agente "Auditor" verifica se o código segue o `CONTRACTS.md` (ex: "Todas as funções públicas têm docstrings?").
2. **Registro de Memória:**
    - O Agente chama `openmemory_store` com: "Decisão Arquitetural: Usamos a lib `x` para parsing devido à performance superior em JSONs grandes".
3. **Atualização de Status:**
    - Usa `github_update_issue` para mover a tarefa para "Done".
    - Atualiza a Matriz de Rastreabilidade.

---

## 7. A Constituição do Sistema: Rules e Configurações

**Dúvida:** _"Como montar um EXCELENTE 'Rules' Base?"_

As regras (`Rules`) e o Prompt do Sistema são a "Constituição" que impede a anarquia de ferramentas. Você deve criar um arquivo `GEMINI.md` global (ou `.agent/rules/rules.md` local) autoritário.

### Exemplo de Arquivo de Regras (`.agent/rules/prime_directive.md`)

# ANTIGRAVITY PRIME DIRECTIVE

Você é um Arquiteto de Software Sênior Autônomo operando no ambiente Antigravity.

Seu objetivo é engenharia de precisão, não "vibe coding".

## 1. PROTOCOLO DE FERRAMENTAS (CRÍTICO)

- **Não adivinhe** caminhos de arquivos. Sempre use `ls` ou `smart_search` primeiro.
- **Não alucine** bibliotecas. Use `d_check_last_version` (Smart Coding MCP) para verificar a existência de pacotes antes de escrever `requirements.txt`.
- **Memória Primeiro:** Antes de responder a qualquer dúvida arquitetural, você DEVE chamar `openmemory_query` para verificar decisões passadas.
- **Arquivos Grandes:** Se precisar ler um arquivo de dados >50 linhas, verifique se é JSON/CSV. Se sim, converta usando `toon_convert` antes de ler.

## 2. EXECUÇÃO DE WORKFLOW

- **Planejamento:** Nunca comece a codificar sem um plano. Verifique `task_plan.md`.
- **Spec-Driven:** Todo código deve referenciar uma spec em `.openspec/`. Se não houver spec, peça ao usuário para gerar uma via OpenSpec.
- **Rastreabilidade:** Ao concluir uma tarefa, você DEVE anexar o Hash do Commit ao item correspondente no `task_plan.md`.

## 3. PADRÕES DE CÓDIGO (Integração Ag-Kit)

- Carregue a skill relevante de `.agent/skills/` baseada no tipo de arquivo (ex: se editando `.py`, leia `.agent/skills/python-expert.md`).
- **Sem Yolo Merges:** Rode a suíte de testes (`npm test` ou `pytest`) antes de declarar uma tarefa pronta.

## 4. COMPATIBILIDADE WSL

- Estamos rodando em ambiente WSL.
- Prefixo de caminho é `/home/user/...` (Linux). Não use `C:\`.
- Comandos de shell devem ser compatíveis com Bash, não PowerShell.

### Configuração de Caminhos de Skills

**Dúvida:** _"Onde as skills são instaladas no Windows/WSL?"_

Para o Antigravity rodando no Windows conectado ao WSL, a confusão de caminhos é comum.

- **Caminho Correto (Projeto):** As skills do projeto devem ficar em `<raiz_do_projeto>/.agent/skills`. Este é o local prioritário e mais seguro, pois viaja com o repositório.
- **Caminho Global (WSL):** Se você quiser skills globais disponíveis para todos os projetos no WSL, o caminho padrão que o Antigravity busca dentro do ambiente remoto (WSL) é `~/.gemini/antigravity/global_skills` (no sistema de arquivos Linux, ou seja, `/home/seu_usuario/.gemini/...`).
- **Recomendação:** Instale as skills do **Ag-Kit** _localmente_ no projeto (`./.agent/skills`).
    - _Razão:_ Projetos diferentes requerem versões diferentes de "Clean Code" (ex: Python vs TypeScript). Manter local garante isolamento e reprodutibilidade. Use o Global apenas para utilitários genéricos de sistema (Git, Bash).


---

## 8. Estratégia de Visibilidade de Ferramentas (Toolbox Pattern)

**Dúvida:** _"Como não perder a visibilidade das tools ocupando menos tokens?"_

A solução é o padrão **"Toolbox" (Caixa de Ferramentas)**. Em vez de carregar todas as 50 ferramentas no prompt do sistema o tempo todo, você instrui o agente a _solicitar_ o carregamento de kits.

**Implementação:**

1. Instale apenas um **"Core Kit"** globalmente (OpenMemory, FileSystem, e uma ferramenta customizada de "Listar Kits").
2. Crie uma Regra no Antigravity:
    > "Você tem acesso a kits de ferramentas especializados. Se precisar gerenciar o projeto, peça para carregar o 'Kit PM'. Se for codificar, peça o 'Kit Dev'. Não tente usar ferramentas de um kit não carregado."
3. **Na prática (Via Docker MCP):** O Docker MCP Gateway permite configurar perfis. Você pode ter um perfil "Planejamento" (GitHub, RLM) e um perfil "Codificação" (Ralph, Smart Coding). O usuário troca o perfil no Docker Desktop, e o agente recebe o novo conjunto de ferramentas na próxima interação, mantendo o contexto limpo.

---

## 9. Conclusão

A arquitetura delineada transforma o Google Antigravity de uma ferramenta de edição em uma **Fábrica de Software**. Ao integrar o **GitHub Project Manager** para governança, o **OpenSpec** para definição, o **OpenMemory** para cognição e o **Ralph** para persistência, você cria um sistema onde a IA não apenas "ajuda", mas "lidera" a execução técnica sob supervisão estratégica humana.

O sucesso desta implementação não reside apenas na instalação das ferramentas, mas na disciplina rígida de seguir o fluxo **Charter → Spec → Plan → Code → Verify**. Sem isso, a "Super-Pilha" torna-se apenas uma fonte ruidosa de complexidade. Com ela, você obtém a verdadeira autonomia de engenharia de software.
# Relatório de Pesquisa Aprofundada: Arquitetura da Fábrica de Software Autônoma

## 1. Introdução: A Era da IDE Agêntica

A indústria de desenvolvimento de software encontra-se em um ponto de inflexão crítico. A transição de ferramentas de "Autocompletar Inteligente" (como o GitHub Copilot original) para "Ambientes Agênticos" (como o Google Antigravity) exige uma reformulação completa das metodologias de trabalho. O usuário propôs uma integração ambiciosa de tecnologias de ponta — incluindo **Ag-Kit, OpenSpec, Ralph, ARC Protocol e OpenMemory** — para criar um fluxo de trabalho onde a Inteligência Artificial não apenas assiste, mas executa, planeja e memoriza.

Este relatório tem como objetivo validar a viabilidade técnica desta "Super-Pilha" (_Super-Stack_), resolver conflitos de compatibilidade e fornecer um manual operacional exaustivo para sua implementação. A análise foca especificamente no ambiente **Google Antigravity** rodando sobre **WSL (Windows Subsystem for Linux)**, abordando a orquestração de mais de 50 ferramentas MCP (Model Context Protocol) sem colapso cognitivo do modelo.

---

## 2. Anatomia da Super-Pilha Tecnológica

Para integrar eficazmente as ferramentas propostas, é necessário compreender não apenas o que elas fazem, mas o papel arquitetural que desempenham em um sistema autônomo.

### 2.1 O Núcleo de Orquestração

- **Google Antigravity:** A plataforma host. Diferente do VS Code, o Antigravity possui um "Gerente de Agentes" nativo que entende o conceito de delegar tarefas complexas e multifacetadas.
- **Ag-Kit (Antigravity Kit):** Atua como o departamento de RH da fábrica. Ele fornece "Personas" (ex: Especialista em Backend, Auditor de Segurança). Ao invés de um prompt genérico, o Ag-Kit carrega instruções específicas de comportamento e competência para cada tarefa.
- **Antigravity BMAD Config:** Uma configuração alternativa focada no método "BMad" (PRD→Architecture→Stories). A análise revela que há uma sobreposição significativa com o Ag-Kit na estrutura de pastas `.agent`, o que exige uma fusão manual cuidadosa (detalhada na Seção 3).

### 2.2 O Sistema de Definição e Planejamento

- **PROJECT_CHARTER.md:** O documento fundacional. Em um mundo onde a IA pode gerar código infinito, o Charter define os limites e o propósito. Ele substitui a "intenção vaga" por diretrizes imutáveis.
- **OpenSpec:** A camada de engenharia civil. Enquanto o PRD diz "o que o usuário quer", o OpenSpec define "o contrato técnico" (JSON Schemas, Assinaturas de API). O uso do OpenSpec é crucial para evitar que a IA invente APIs inconsistentes durante a codificação.
- **Plan-With-Files:** Uma metodologia, não uma ferramenta. A ideia é manter o estado do plano de execução em arquivos Markdown (`task_plan.md`) em vez de confiar na memória volátil do chat. Isso permite que agentes diferentes (ou sessões diferentes) peguem o trabalho de onde o anterior parou.

### 2.3 O Motor de Execução e Disciplina

- **Ralph:** O "Executor Incansável". O Ralph é um script de loop que persiste o contexto em arquivos (`progress.txt`) e itera sobre uma tarefa até que os testes passem. Ele resolve o problema da "preguiça" da IA ou interrupções de contexto.
- **ARC Protocol:** O "Supervisor". O ARC (Analyze, Run, Confirm) impõe um protocolo de segurança. Ele impede que o agente saia escrevendo código sem antes analisar o impacto (`Analyze`) e obriga uma verificação (`Confirm`) antes de considerar a tarefa pronta. A integração ideal coloca o Ralph como a ferramenta de execução dentro da fase "Run" do ARC.

### 2.4 O Sistema Nervoso (Memória e Contexto)

- **OpenMemory:** A escolha superior para memória de longo prazo. Diferente do `mcp-memory` (que é simplista), o OpenMemory usa estruturas de dados mais complexas (grafos) para relacionar conceitos, permitindo que a IA entenda a evolução do projeto ao longo do tempo.
- **RLM (Recursive Language Model):** Uma ferramenta especializada para "Contexto Infinito". Baseada em pesquisa do MIT, ela permite que a IA processe repositórios ou documentações inteiras decompondo-as recursivamente, superando as limitações da janela de contexto padrão.
- **Smart Coding MCP:** O mecanismo de busca semântica. Permite encontrar código por funcionalidade ("Onde está a lógica de auth?") em vez de texto exato, essencial para projetos legados.
- **MCP TOON:** Um compressor de dados. Converte JSONs verbosos em formatos otimizados para tokens, economizando custos e espaço na janela de contexto.

---

## 3. Arquitetura e Estratégia de Integração

### 3.1 Viabilidade de Instalação Massiva

**Questão:** _"Posso instalar todos? Posso ter ativos tantos MCPs?"_

**Resposta:** Você pode _instalar_ todos, mas não deve _ativar_ todos simultaneamente no mesmo contexto de agente.

O protocolo MCP injeta a descrição de cada ferramenta no System Prompt do LLM. Com mais de 50 ferramentas (GitHub: ~15, Linear: ~15, Ag-Kit Skills: ~36, Docker Utils: ~10), você consumiria cerca de 10.000 a 15.000 tokens apenas para _explicar_ ao modelo quais ferramentas ele tem. Isso deixa pouco espaço para o raciocínio e o código em si, além de confundir o modelo (alucinação de ferramentas).

**Solução Arquitetural: O Padrão Gateway com Docker MCP**

Utilize o **Docker MCP Toolkit** como um orquestrador de visibilidade.

1. **Grupo Core (Sempre Ativo):** OpenMemory, Smart Coding, FileSystem.
2. **Grupos Dinâmicos (Ativação via Perfil):**
    - Crie containers ou configurações separadas no Docker MCP para "Fase de Planejamento" (contendo GitHub PM, RLM, Fetch) e "Fase de Construção" (contendo Ralph, TOON, Ag-Kit).
    - Embora o Antigravity ainda não tenha um "trocador de perfil" nativo de um clique, você pode gerenciar isso habilitando/desabilitando servidores no arquivo `mcp.json` ou instruindo o agente a usar ferramentas específicas que estão roteadas via Docker Gateway.

### 3.2 Incompatibilidades e Resoluções

#### Conflito 1: Ag-Kit vs. BMAD Config

Ambos querem ser donos da pasta `.agent`.

- **Diagnóstico:** O instalador do Ag-Kit cria uma estrutura rica de personas. O BMAD cria workflows focados em metodologia de produto.
- **Resolução:** Use o **Ag-Kit** como base (instale-o primeiro). Em seguida, clone o repositório do BMAD em uma pasta temporária e copie _apenas_ os arquivos de workflow (`.agent/workflows/*.md`) e regras (`.cursor/rules` adaptadas para `.agent/rules`) para dentro da estrutura do Ag-Kit. Edite os workflows do BMAD para referenciar as skills do Ag-Kit, que são mais robustas.

#### Conflito 2: Antigravity (Windows) vs. Ferramentas (WSL)

- **Diagnóstico:** O Antigravity roda no Windows. Se você configurar um servidor MCP (como Smart Coding) para rodar no Windows apontando para arquivos no WSL (`\\wsl$\...`), a performance será terrível e indexadores de busca falharão.
- **Resolução Obrigatória:** Todos os servidores MCP que tocam em arquivos devem rodar **dentro do ambiente Linux (WSL)**.
    - No `mcp.json` do Antigravity, use o comando `wsl` para invocar o servidor:

```JSON
"smart-coding": {
  "command": "wsl",
  "args": ["npx", "smart-coding-mcp"]
}
```

- Ou melhor, use o **Docker MCP Toolkit** rodando com o backend Docker no WSL2. Isso garante que as ferramentas tenham acesso nativo ao sistema de arquivos Linux via volumes montados.

---

## 4. O Sistema Nervoso: Memória e Contexto

A gestão de memória é o que diferencia um "Agente Sênior" de um "Estagiário Amnésico".

### 4.1 Comparativo: OpenMemory vs. MCP-Memory

Para um projeto desta complexidade, o **OpenMemory** é a escolha obrigatória.

|**Característica**|**MCP-Memory (ebailey78)**|**OpenMemory (CaviraOSS)**|
|---|---|---|
|**Tipo de Armazenamento**|Arquivos Markdown Flat|Grafo de Conhecimento (Graph DB)|
|**Busca**|Palavra-chave (Lunr.js)|Semântica + Relacional|
|**Temporalidade**|Não rastreia evolução|Rastreia histórico de fatos|
|**Complexidade**|Baixa (Plug & Play)|Alta (Requer servidor backend)|
|**Adequação ao Projeto**|Apenas para notas simples|Ideal para decisões de arquitetura|

**Recomendação de Instalação:** Configure o OpenMemory rodando via Docker Compose no WSL e conecte o Antigravity a ele via MCP. Use-o para armazenar ADRs (Architectural Decision Records) e o estado atual do entendimento do sistema.

### 4.2 Otimização com TOON e RLM

- **Quando usar TOON:** Configure uma regra no Antigravity: _"Sempre que precisar ler um arquivo de dados estruturados (JSON, CSV, XML) maior que 100 linhas, utilize a ferramenta `toon_convert` primeiro."_ Isso permite que o agente ingira grandes datasets de teste sem estourar o limite.
- **Quando usar RLM:** Configure uma regra: _"Para ler documentação de bibliotecas ou grandes bases de conhecimento na pasta /docs, utilize a ferramenta RLM em vez de tentar ler os arquivos diretamente."_ O RLM permite que o agente responda perguntas como "Verifique se nossa implementação de Auth está compatível com a documentação oficial em /docs/auth-spec.pdf".

---
## 5. Centro de Comando: Gerenciamento de Projetos

A escolha da ferramenta de PM define como a intenção humana é traduzida em tarefas de máquina.

### 5.1 GitHub Project Manager vs. Linear

Apesar da excelente UX do Linear, o **GitHub Project Manager MCP** é a escolha técnica superior para este stack.

- **Integração Profunda:** Ele permite criar uma **Matriz de Rastreabilidade** (Traceability Matrix). O agente pode verificar: _"O Requisito R-101 (do PRD) está ligado à Issue #42, que foi resolvida pelo Commit a1b2c3d."_ Essa cadeia de custódia é vital para a auditoria autônoma do ARC Protocol.
- **Ferramentas de IA:** O GitHub PM MCP inclui ferramentas como `generate_prd` (que cria PRDs baseados em input textual) e `analyze_task_complexity`. O Linear MCP é mais passivo.
- **Custo/Benefício:** Elimina a necessidade de sincronização entre duas plataformas (GitHub e Linear).

### 5.2 Organizando o Trabalho: A Hierarquia de "Pacotes"

Para que você (humano) acompanhe tudo sem microgerenciar, estruture o projeto em níveis de abstração:

1. **Nível Estratégico (Humano):**
    - Define o `PROJECT_CHARTER.md`.
    - Aprova o PRD (Gerado pela IA).
    - Monitora o "Milestone" no GitHub Projects.
2. **Nível Tático (IA Arquiteto):**
    - Quebra o PRD em "Specs" (OpenSpec).
    - Cria o `task_plan.md` (lista de verificação de arquivos a serem criados/editados).
3. **Nível Operacional (IA Desenvolvedora - Ralph):**
    - Pega uma tarefa do `task_plan.md`.
    - Codifica.
    - Testa.
    - Faz commit.

---

## 6. Padronização: A Estrutura de Diretórios "Hyper-Structured"

Para evitar a "sujeira" na raiz e garantir que cada ferramenta saiba onde buscar seus dados, adote esta estrutura padrão rigorosa.

/my-antigravity-project
│
├── PROJECT_CHARTER.md # [Humano] O documento raiz. Visão, Escopo, Não-Escopo.
├── AGENTS.md # [Ag-Kit] Memória de curto prazo compartilhada entre sessões.
├── task_plan.md # O plano de execução atual.
│
├──.agent/ # [Configuração Antigravity/Ag-Kit]
│ ├── agents/ # Personas (.md)
│ ├── skills/ # Skills locais (.md)
│ ├── workflows/ # Workflows Slash Command (.md)
│ └── rules/ # Regras de comportamento (.md)
│
├──.openspec/ #
│ ├── global/ # Specs transversais (ex: tratamento de erros)
│ └── features/ # Specs por funcionalidade
│
├──.arc/ #
│ ├── CONTRACTS.md # Regras de Ouro (ex: "Nenhum commit sem teste passando")
│ └── TRACEABILITY.md # Link entre Specs e Arquivos (gerado pelo Agente)
│
├── scripts/
│ └── ralph/ #
│ ├── ralph.sh # Script de execução
│ └── prompt.md # Prompt do loop
│
├── docs/ #
│ ├── product/ # PRDs (v1.md, v2.md)
│ ├── architecture/ # Decisões (ADRs), Diagramas
│ ├── knowledge/ # Backups do OpenMemory
│ └── sops/ # Procedimentos (ex: "deployment.md") para RLM ler
│
├── core/ # [Código Fonte] (Recomendado sobre /src para modularidade)
│ ├── domain/
│ └── infrastructure/
│
├── tests/ #
└──.vscode/ #
└── mcp.json # Configuração dos servidores

**Por que `PROJECT_CHARTER.md` na raiz?**

Ele é a âncora. Se o agente se perder ou alucinar requisitos, a regra de recuperação é sempre: _"Leia o PROJECT_CHARTER.md na raiz"_. Ele precede o OpenSpec porque define o "Porquê", enquanto o OpenSpec define o "Como".

---

## 7. Fluxos de Trabalho Operacionais (Exemplos Práticos)

Como utilizar tudo isso em conjunto? O segredo é a **Faseamento**. Não tente usar tudo ao mesmo tempo.

### Fase 1: Incepção e Definição

_Ferramentas: Fetch (Docker), GitHub PM MCP._

1. **Ação Humana:** Cria `PROJECT_CHARTER.md` com a visão do produto.
2. **Comando:** _"Atue como Product Manager. Leia o Charter, pesquise concorrentes usando `fetch` e gere um PRD completo usando `github_generate_prd`. Salve em `docs/product/`."_
3. **Resultado:** O agente cria um PRD detalhado. O humano revisa e aprova.
4. **Integração:** O agente usa `github_create_issues` para transformar o PRD em tickets no GitHub Projects.

### Fase 2: Arquitetura e Planejamento

_Ferramentas: OpenSpec, Smart Coding, Plan-With-Files._

1. **Comando:** _"Atue como Arquiteto. Analise o PRD e o código existente (usando `smart_search`). Inicialize o OpenSpec e crie as especificações técnicas para as funcionalidades aprovadas."_
2. **Ação:** O agente cria arquivos em `.openspec/features/`.
3. **Comando:** _"Crie um plano de execução detalhado em `task_plan.md` baseando-se nas specs criadas."_
4. **Resultado:** Um arquivo Markdown na raiz com checkboxes, onde cada item aponta para uma spec e um arquivo de código destino.

### Fase 3: Execução Autônoma

_Ferramentas: Ralph, Ag-Kit Skills, TOON._

1. **Comando:** _"Inicie o Ralph Loop para resolver a primeira tarefa pendente em `task_plan.md`."_
2. **O Loop do Ralph:**
    - Lê a tarefa: "Implementar Autenticação JWT".
    - Carrega a Persona "Backend Specialist" e a Skill "Security-Best-Practices" (Ag-Kit).
    - Lê a Spec em `.openspec/features/auth/spec.md`.
    - Escreve o código e os testes.
    - Executa os testes. Se falhar, corrige e repete.
    - Se precisar ler um log de erro gigante, usa `toon_convert`.
    - Passou nos testes? Faz commit e marca o checkbox no `task_plan.md`.

### Fase 4: Verificação e Encerramento

_Ferramentas: ARC Protocol, OpenMemory._

1. **Comando:** _"Execute a verificação ARC para o trabalho recente."_
2. **Ação:** O Agente (Persona Auditor) verifica o `CONTRACTS.md`. "O código tem comentários? Segue a convenção de nomenclatura?".
3. **Memória:** O Agente registra no OpenMemory: _"Implementada Auth JWT. Decidimos usar a lib `PyJWT` versão 2.8."_
4. **Status:** Atualiza a Issue no GitHub para "Closed".

---

## 8. A Constituição do Sistema: Rules e System Prompts

Para garantir que o Antigravity utilize a ferramenta certa na hora certa, você deve configurar regras rígidas.

### O Arquivo `GEMINI.md` (Regras Globais)

Este arquivo deve ser colocado em `~/.gemini/GEMINI.md` (ou equivalente no seu sistema) para governar o comportamento base.

# ANTIGRAVITY PRIME DIRECTIVE

Você é um Engenheiro de Software Autônomo de Elite.

Sua operação é regida pelos seguintes protocolos inegociáveis:

## 1. PROTOCOLO DE USO DE FERRAMENTAS

- **Não adivinhe.** Se precisar saber sobre um arquivo, use `ls` ou `read_file`.
- **Não alucine libs.** Use `d_check_last_version` (Smart Coding) antes de importar pacotes novos.
- **Economia de Tokens:** Para arquivos de dados >50KB, use OBRIGATORIAMENTE `toon_convert`.
- **Memória:** Consulte `openmemory_query` antes de tomar decisões arquiteturais.

## 2. HIERARQUIA DE VERDADE

1. `PROJECT_CHARTER.md` (A Visão)
2. `.openspec/` (A Especificação Técnica)
3. `CONTRACTS.md` (As Regras de Código)

## 3. FLUXO DE TRABALHO

- Nunca inicie código sem um item correspondente no `task_plan.md`.
- Carregue as SKILLS apropriadas de `.agent/skills/` antes de codificar.
- Todo trabalho deve ser verificado via `/arc-verify` antes do commit final.

## 4. AMBIENTE WSL

- Você está operando em um ambiente WSL.
- Use caminhos Linux (`/home/user/...`).
- Comandos de terminal devem ser Bash.

### Instalação de Skills

As skills do Ag-Kit devem ser instaladas preferencialmente **localmente no projeto** em `.agent/skills`.

- _Motivo:_ Projetos diferentes têm regras diferentes (ex: um projeto Python usa PEP8, outro usa Black). Manter skills locais garante que o agente siga as convenções _daquele_ repositório.
- _Caminho Global:_ Se necessário, no WSL, o caminho padrão é `~/.gemini/antigravity/global_skills`.

---

## 9. Conclusão

A integração do **Google Antigravity** com o ecossistema proposto (Ag-Kit, Ralph, OpenMemory, GitHub MCP) cria uma **Fábrica de Software Autônoma** de potência industrial. A chave para o sucesso não é apenas a instalação das ferramentas, mas a **segmentação do fluxo de trabalho**.

Ao adotar a estrutura **Hyper-Structured**, você garante que a IA saiba onde ler (Charter/Specs), onde escrever (Core), onde planejar (Task Plan) e onde lembrar (OpenMemory). O uso do **GitHub Project Manager** fornece a camada de governança necessária para que o humano mantenha o controle estratégico, enquanto **Ralph** e **ARC** garantem a execução tática disciplinada. Este setup coloca você na vanguarda absoluta da engenharia de software assistida por IA em 2026.