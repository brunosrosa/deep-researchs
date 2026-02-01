# Relatório de Pesquisa Avançada: Implementação de Ciclo de Vida de Desenvolvimento de Software Agêntico para DataEngOS com Google Antigravity e Ag-Kit

## 1. Introdução: A Transição para a Engenharia "Agent-First"

A indústria de desenvolvimento de software encontra-se em um ponto de inflexão crítico, transitando de um modelo assistido por IA — onde ferramentas como o GitHub Copilot atuam como mecanismos de autocompletar sofisticados — para um paradigma "Agent-First". Neste novo cenário, plataformas como o Google Antigravity e frameworks como o Ag-Kit redefinem o Ambiente de Desenvolvimento Integrado (IDE) não mais como um editor de texto passivo, mas como um "Mission Control" ou Centro de Comando. Para o desenvolvimento de um sistema complexo como o "DataEngOS" — uma plataforma projetada para auxiliar engenheiros de dados — essa mudança exige não apenas novas ferramentas, mas uma reestruturação fundamental do Ciclo de Vida de Desenvolvimento de Software (SDLC). O desenvolvedor deixa de ser o executor primário da sintaxe para se tornar o arquiteto e orquestrador de uma força de trabalho digital autônoma.

Este relatório técnico aprofundado aborda as complexidades operacionais, arquitetônicas e estratégicas da implementação de um fluxo de trabalho agêntico. A análise foca especificamente na superação dos desafios de manter uma visão estratégica macro enquanto se delega a execução tática a agentes autônomos, na resolução de nuances de infraestrutura envolvendo o Windows Subsystem for Linux (WSL), e na estruturação de governança via Model Context Protocol (MCP) e Procedimentos Operacionais Padrão (SOPs). O objetivo é fornecer um projeto detalhado para a construção do DataEngOS, garantindo que a autonomia dos agentes resulte em produtividade tangível e código de produção robusto, em vez de débito técnico automatizado.

---

## 2. Infraestrutura e Sistema Operacional: A Estratégia WSL vs. Windows

A escolha do sistema operacional subjacente é a decisão de infraestrutura mais crítica ao configurar um stack agêntico moderno. A dúvida apresentada — _"Qual o ganho de usar WSL? Não pode ser melhor ir para windows mesmo?"_ — reflete uma incerteza comum, mas a análise técnica aponta inequivocamente para o **Windows Subsystem for Linux (WSL 2)** como a fundação superior para este caso de uso específico, apesar da complexidade inicial de configuração.

### 2.1 Análise Comparativa de Desempenho e Compatibilidade

Para o desenvolvimento do DataEngOS, que presumivelmente envolverá a orquestração de contêineres Docker, bancos de dados e pipelines de dados, o ambiente nativo do Windows apresenta limitações estruturais significativas que são mitigadas pelo WSL 2.

|**Característica Técnica**|**Windows Nativo (NTFS/Win32)**|**WSL 2 (EXT4/Linux Kernel)**|**Impacto no Fluxo Agêntico (DataEngOS)**|
|---|---|---|---|
|**Desempenho de I/O de Arquivos**|O sistema de arquivos NTFS possui um overhead significativo para operações de metadados em muitos arquivos pequenos (ex: `node_modules`, `.git`).|O WSL 2 utiliza discos virtuais EXT4, oferecendo desempenho de I/O quase nativo do Linux.|Agentes de IA realizam varreduras intensivas no código ("Deep Research"). No Windows, isso pode causar lentidão severa no IDE; no WSL, a indexação é instantânea.|
|**Integração com Docker**|Requer camadas de tradução ou o uso de backend Hyper-V legado, frequentemente resultando em problemas de mapeamento de volume e rede.|O Docker Desktop utiliza o backend nativo do WSL 2, permitindo execução direta de contêineres sem overhead de VM.|Para o DataEngOS, o agente precisará subir contêineres de teste (Postgres, Kafka). A estabilidade do Docker no WSL é crítica para que o agente possa verificar seu próprio trabalho.|
|**Compatibilidade de Ferramentas (Ag-Kit)**|Scripts e ferramentas de IA (Python/Node) frequentemente falham devido a diferenças em separadores de caminho (`\` vs `/`) e permissões POSIX.|Ambiente totalmente compatível com os padrões POSIX, onde a maioria das ferramentas de IA é desenvolvida e testada.|O Ag-Kit e muitos MCPs são desenvolvidos primariamente para ambientes Unix. Rodar no Windows frequentemente resulta em erros obscuros de `path not found` que confundem os agentes.|
|**Hospedagem de Servidores MCP**|Executar daemons persistentes (como servidores MCP) é mais complexo sem um sistema `init` robusto.|Permite execução simples de processos em background ou via `systemd`.|Agentes dependem de servidores MCP (como o do GitHub ou Docker) estarem sempre ativos e acessíveis via `stdio` ou HTTP.|

### 2.2 Diagnóstico e Resolução de Problemas do Ag-Kit no WSL

Uma das dificuldades centrais relatadas — _"Workflows e Skills não aparecem integrados ao '/' do Antigravity"_ — é um sintoma clássico de uma configuração "Split-Brain" (Cérebro Dividido) entre o cliente do IDE e o servidor de execução.

No modelo de desenvolvimento remoto do VS Code (sobre o qual o Antigravity é construído), a Interface Gráfica (GUI) é renderizada no Windows, mas a lógica, as extensões e os terminais são executados dentro da distribuição Linux (WSL). O problema de "invisibilidade" dos comandos ocorre quando o Ag-Kit é instalado no escopo errado. Se o usuário executa `npm install` ou inicializa o Ag-Kit através de um terminal PowerShell no Windows, os arquivos e configurações são gravados no sistema de arquivos do Windows (`C:\Users\...`). No entanto, a instância do Antigravity conectada ao WSL está procurando por definições de Skills e Workflows dentro do sistema de arquivos do Linux (`/home/user/...`).

Para corrigir a ausência de integração no menu `/`:

1. **Conexão Explicita:** É imperativo garantir que o Antigravity esteja conectado ao WSL. O indicador verde no canto inferior esquerdo deve exibir "WSL: Ubuntu" (ou a distribuição escolhida).
    
2. **Instalação no Lado do Servidor:** A instalação do Ag-Kit deve ser realizada estritamente através do **Terminal Integrado** do Antigravity, garantindo que se está operando dentro do shell `bash` ou `zsh` do Linux. O comando `npx @vudovn/ag-kit init` deve ser executado na raiz do projeto dentro do sistema de arquivos Linux.
    
3. **Localização das Skills:** O Ag-Kit e o Antigravity varrem diretórios específicos para carregar Skills. No modo WSL, ele prioriza `~/.gemini/antigravity/global_skills` (no Linux) ou a pasta `.agent/skills` na raiz do projeto. Instalações feitas no Windows `AppData` são invisíveis para o agente que opera no contexto Linux.
    

A adoção do WSL não é apenas uma preferência, mas um requisito técnico para garantir que as ferramentas de orquestração (Ag-Kit) e execução (Docker/MCP) falem a mesma língua que o sistema operacional, eliminando uma classe inteira de erros de "ambiente" que paralisariam os agentes autônomos.

---

## 3. Governança Estratégica: Visão Macro vs. Execução Micro

O desafio central articulado — _"Como ter uma visão macro... ao mesmo tempo que libero para que agentes distintos ajam"_ — toca no cerne do problema de alinhamento em sistemas multiagentes. Se deixados sem supervisão estruturada, agentes tendem a "alucinar" progresso ou divergir do objetivo arquitetural. A solução reside na dissociação entre o plano de controle (estratégico) e o plano de dados (tático), utilizando artefatos persistentes para sincronização.

### 3.1 A Metodologia "Planning with Files" (Planejamento com Arquivos)

A pesquisa identifica a metodologia "Planning with Files" (popularizada pela ferramenta Manus e implementada como Skill por OthmanAdi) como a solução padrão-ouro para este problema. Ao contrário da memória de curto prazo do agente (Context Window), que é volátil e cara, arquivos Markdown no repositório servem como uma memória de longo prazo persistente e auditável.

**O Protocolo de Três Arquivos:**

Esta metodologia impõe que, antes de qualquer código ser escrito, o agente deve interagir com três arquivos específicos na raiz do projeto:

1. **`task_plan.md` (O Estado Estratégico):** Este é o "mapa" do projeto. Ele contém a visão macro, dividida em Épicos e Fases. É o único lugar onde a "verdade" sobre o status do projeto reside.
    
    - _Regra de Governança:_ O agente é proibido de iniciar uma tarefa sem que ela esteja explicitamente listada e marcada como "Em Progresso" neste arquivo.
        
2. **`findings.md` (A Base de Conhecimento):** Durante a execução, agentes descobrem informações (ex: "A biblioteca X é incompatível com a versão Y"). Em vez de "esquecer" isso ao final da sessão, o agente deve registrar essas descobertas aqui. Isso evita que agentes futuros repitam pesquisas ou cometam os mesmos erros arquiteturais.
    
3. **`progress.md` (O Log Tático):** Um diário de bordo granular onde o agente registra suas tentativas, erros de compilação e micro-decisões. Isso permite que um humano (ou outro agente revisor) entenda _como_ uma solução foi alcançada, facilitando a depuração.
    

**Integração Técnica com Ag-Kit:** A dúvida sobre a compatibilidade desta Skill com o Ag-Kit é pertinente. A análise indica que **sim, vale a pena e é totalmente integrável**.

- **Mecanismo de Instalação:** Como o Ag-Kit estrutura agentes, mas utiliza o motor do Antigravity/Claude para execução, Skills baseadas em arquivos são agnósticas ao framework.
    
- **Procedimento:** Deve-se clonar o repositório `OthmanAdi/planning-with-files` diretamente para dentro da pasta `.agent/skills/planning-with-files` no diretório do projeto (dentro do WSL). O Antigravity detectará automaticamente o arquivo `SKILL.md` e disponibilizará os comandos `/plan` ou a instrução natural para o agente utilizar essa estrutura.
    
- **Prevenção de Conflitos:** Para evitar incompatibilidades, recomenda-se namespace nas pastas de Skills (ex: prefixar pastas com `custom_` ou `ext_`). Não há conflito binário, pois Skills são essencialmente prompts estruturados que o LLM lê sob demanda.
    

### 3.2 Gestão de Backlog Externo: O "Kanban" Comum

Enquanto o `task_plan.md` gerencia a execução imediata (o "Agora"), a visão de longo prazo (o Roadmap) deve residir em uma ferramenta dedicada de Gerenciamento de Projetos. O usuário solicita uma solução "grátis" e "open-source" que mantenha uma visualização comum.

**Recomendação Primária: GitHub Projects (Projects V2)**

Embora o GitHub em si seja uma plataforma proprietária, o recurso "Projects" é gratuito para repositórios públicos (e privados pessoais), está intimamente integrado ao código e, crucialmente, possui suporte robusto via **MCP (Model Context Protocol)**.

- **Integração Visual:** O desenvolvedor humano visualiza um quadro Kanban (To Do, In Progress, Done).
    
- **Integração Agêntica:** Através do servidor MCP `github-projects` (como `@taylor-lindores-reeves/mcp-github-projects` ou `kunwarVivek/mcp-github-project-manager`), o agente no Antigravity ganha a capacidade de "ver" e "manipular" esse quadro.
    
- **Fluxo de Trabalho:**
    
    1. O humano cria um Épico no GitHub Projects: "Implementar Camada de Ingestão de Logs".
        
    2. O agente recebe a instrução: "Verifique o backlog no GitHub Projects, pegue a tarefa de maior prioridade e inicie o planejamento."
        
    3. O agente usa o MCP para ler o card, move-o para "In Progress", cria o `task_plan.md` correspondente e inicia o trabalho.
        
    4. Ao finalizar, o agente move o card para "Review" e adiciona um link para o PR.
        

**Alternativa Open-Source: Linear (Versão Free/Startup)** O Linear é frequentemente citado em contextos de "Vibe Coding" devido à sua API extremamente rápida e amigável a LLMs. Existe um servidor MCP oficial e robusto para o Linear. Embora não seja open-source, sua camada gratuita é generosa para desenvolvedores individuais. A integração via MCP permite que o agente crie tickets, leia especificações e atualize status sem sair do contexto do IDE.

---

## 4. Engenharia de Ciclo de Vida: Do "Porquê" ao "Checklist"

O usuário pergunta: _"Preciso criar primeiro uma documentação do 'Porquê' e o que?"_. A resposta, no contexto de desenvolvimento agêntico, é um enfático **sim**. Em sistemas determinísticos, a documentação explica o código. Em sistemas agênticos, a documentação **gera** o código. Sem um "Porquê" claro, a natureza probabilística dos LLMs leva à deriva (drift) — o agente cria código funcional que não atende ao objetivo de negócio.

### 4.1 Documentação como Prompt Mestre (Charter)

Antes de qualquer código, deve-se criar um arquivo `PROJECT_CHARTER.md` na raiz. Este documento serve como a "Constituição" do projeto DataEngOS.

- **Conteúdo:** Missão do projeto, público-alvo (engenheiros de dados), princípios de design (ex: "preferir configuração explícita sobre mágica", "idempotência é obrigatória").
    
- **Aplicação:** Uma regra global no Ag-Kit deve instruir todos os agentes a lerem este arquivo antes de iniciar qualquer nova sessão de trabalho. Isso garante alinhamento estratégico automático.
    

### 4.2 A Criação de Listas de Tarefas e Checklists

O usuário questiona como criar listas detalhadas que vão desde o teste unitário até a entrega. Isso não deve ser feito manualmente pelo humano, mas sim _orquestrado_ por ele.

**O Processo de Refinamento em Cascata:**

O "Agente da IDE" (geralmente Gemini 3 Pro ou Claude 3.5 Sonnet) tende a ser preguiçoso se não for estimulado. Se você pedir "crie um sistema de usuários", ele criará um script simples. Para obter granularidade (tabelas, hashing, rotas, formulários), utiliza-se uma técnica de **Chain of Thought (Cadeia de Pensamento) Forçada**.

1. **Sessão de Arquitetura (Planning Mode):** Inicie uma sessão com o agente em "Planning Mode" (se disponível no Antigravity) ou instrua explicitamente: _"Atue como um Arquiteto de Software Sênior. Não escreva código. Seu objetivo é decompor a Feature X em um plano de implementação atômico."_
    
2. **Prompt de Decomposição:**
    
    > "Para a feature 'Autenticação de Usuários', gere um `task_plan.md` detalhado. Cada tarefa deve ser atômica (máximo de 1 hora de trabalho). Para cada tarefa, liste:
    > 
    > - Dependências.
    >     
    > - Arquivos a serem criados/modificados.
    >     
    > - Critérios de Teste (Unitários e Integração).
    >     
    > - Verificações de Segurança (ex: Hashing, SQL Injection)."
    >     
    
3. **Enriquecimento via IDE Agentica:** Utilize o agente para preencher os metadados das User Stories.
    
    - _Exemplo:_ "Para cada item no `task_plan.md`, gere uma descrição formal de User Story no formato 'Como [persona], eu quero [ação], para que [benefício]', e adicione critérios de aceitação Gherkin (Given/When/Then)."
        

---

## 5. Seleção de Ferramentas: MCPs para DataEngOS

A observação do usuário de que "MCPs de análise de dados não fazem sentido" é perspicaz. O DataEngOS é uma ferramenta para _fazer_ engenharia de dados, logo, o agente que o constrói precisa de ferramentas de **Engenharia de Infraestrutura**, não de Análise de Dados. O agente precisa ser capaz de manipular o ambiente onde o DataEngOS irá operar.

### 5.1 O Stack MCP Recomendado para Construção (Builder Stack)

Para "aumentar os poderes" do agente na construção desta plataforma, os seguintes servidores MCP são recomendados, pois permitem que o agente valide seu próprio trabalho no mundo real (Grounding):

|**Servidor MCP**|**Função no DataEngOS**|**Justificativa de "Poder"**|**Fonte**|
|---|---|---|---|
|**Docker MCP**|Infraestrutura e Testes|O DataEngOS provavelmente orquestrará contêineres (Airflow, Spark, DBs). O agente precisa listar, iniciar, parar e inspecionar contêineres para verificar se o código que ele escreveu realmente sobe a infraestrutura corretamente.||
|**PostgreSQL / Database MCP**|Validação de Integração|O agente pode conectar-se a um banco de dados de teste (que ele mesmo subiu via Docker) para verificar se as migrações (Alembic/Flyway) foram aplicadas corretamente e se os dados estão sendo persistidos conforme o esquema.||
|**Kubernetes MCP**|Orquestração Avançada|Se o DataEngOS tiver deployment em K8s, o agente precisa de acesso via `kubectl` para verificar o estado dos pods, logs e serviços durante os testes de integração.||
|**FileSystem MCP**|Manipulação de Arquivos|Essencial para permitir que o agente crie estruturas de diretórios complexas, arquivos de configuração (YAML/JSON) e scripts de automação com segurança e permissões controladas.||
|**Git/GitHub MCP**|Controle de Versão|Permite ao agente realizar operações complexas de git, gerenciar branches de features, ler o histórico de commits para entender o contexto de mudanças passadas e interagir com PRs.||
|**Sentry/Observability**|Depuração|Permite que o agente leia logs de erro gerados pela aplicação em tempo real para diagnosticar falhas sem que o humano precise colar stack traces no chat.||

Esta seleção transforma o agente de um "gerador de texto" em um "operador de sistema", capaz de executar o ciclo completo de codificação, deploy em ambiente de teste e validação funcional.

---

## 6. Procedimentos Operacionais Padrão (SOPs)

Para garantir que "nada seja esquecido" e que a qualidade seja mantida, deve-se criar uma biblioteca de SOPs (arquivos Markdown na pasta `.agent/rules/` ou `.cursorrules` dependendo da configuração exata do Ag-Kit).

**SOPs Essenciais para DataEngOS:**

1. **`SOP-001-Test-Driven-Development.md`:**
    
    > "Regra Inviolável: Nenhum código de implementação deve ser escrito sem antes escrever um teste unitário que falhe. O ciclo deve ser Red-Green-Refactor. Para componentes de dados, testes de integração com Docker containers são mandatórios."
    
2. **`SOP-002-Error-Handling.md`:**
    
    > "O DataEngOS deve ser resiliente. Todo código que interage com I/O externo (banco, rede, arquivos) deve ter tratamento de exceção explícito. Erros nunca devem ser silenciados (`pass`). Logs estruturados (JSON) devem ser emitidos com contexto."
    
3. **`SOP-003-Definition-of-Done.md`:**
    
    > "Uma tarefa só está concluída se: 1) O código está implementado. 2) Testes passam. 3) Documentação (Docstrings e README) está atualizada. 4) O arquivo `task_plan.md` foi atualizado. 5) O Linter não reporta erros."
    
4. **`SOP-004-Atomic-Commits.md`:**
    
    > "Commits devem ser atômicos e descritivos. Nunca misture refatoração com novas features no mesmo commit. Use a convenção Conventional Commits."
    

---

## 7. Roteiro de Implementação Integrado (Passo a Passo)

Baseado em todas as análises acima, aqui está o roteiro consolidado para o usuário iniciar o projeto:

### Fase 1: Fundação (Infraestrutura)

1. **Instalar WSL 2:** Garantir ambiente Ubuntu atualizado.
    
2. **Docker no WSL:** Configurar Docker Desktop com backend WSL 2.
    
3. **Antigravity + Remote WSL:** Instalar e conectar ao projeto dentro do Linux.
    
4. **Inicialização Ag-Kit:** Executar `npx @vudovn/ag-kit init` no terminal _bash_ do WSL.
    

### Fase 2: Governança (Cérebro)

1. **Charter e Planejamento:** Criar `PROJECT_CHARTER.md` com a visão do DataEngOS.
    
2. **Instalar Skill de Planejamento:** Clonar `OthmanAdi/planning-with-files` para `.agent/skills/planning-with-files` dentro do WSL.
    
3. **Conectar Backlog:** Configurar o `mcp.json` com o servidor do GitHub Projects para conectar o agente ao quadro Kanban.
    

### Fase 3: Execução (Ciclo Vibe Coding)

1. **Brainstorm:** Usar o agente para gerar o PRD da primeira feature ("Ingestão de Dados").
    
2. **Refinamento:** Pedir ao agente para converter o PRD em tarefas atômicas no `task_plan.md`, seguindo o SOP de granularidade.
    
3. **Codificação:** Instruir o agente a pegar a primeira tarefa, escrever o teste (usando Docker MCP para mocks se necessário), implementar e validar.
    
4. **Feedback:** O humano revisa o `task_plan.md` e o quadro Kanban (atualizado automaticamente pelo agente) para manter a visão macro, intervindo apenas nos PRs ou decisões arquiteturais.
    

---

## 8. Conclusão

A construção do DataEngOS utilizando Google Antigravity e Ag-Kit no WSL não é apenas viável, mas representa o estado da arte no desenvolvimento assistido por IA. A chave para o sucesso não está apenas nas ferramentas, mas na **disciplina de processo**: forçar a documentação prévia, utilizar o sistema de arquivos para persistência de memória ("Planning with Files") e equipar os agentes com MCPs de infraestrutura (Docker/K8s) para que eles possam interagir com a realidade do sistema que estão construindo. Ao seguir este roteiro, o desenvolvedor transcende a função de codificador e assume o papel de diretor técnico de uma equipe sintética eficiente.

### Referências

- Google Antigravity & Agent Manager.
    
- WSL Integration & Performance.
    
- Planning with Files Methodology.
    
- MCP Servers (Docker, DB, GitHub).
    
- Ag-Kit Framework & Installation.