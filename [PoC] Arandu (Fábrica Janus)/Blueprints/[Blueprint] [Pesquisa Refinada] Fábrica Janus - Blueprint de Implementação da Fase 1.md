---
sticker: lucide//brain
---
# Fábrica Janus: Blueprint de Implementação da Fase 1

**Sumário Executivo:** Este documento apresenta um blueprint técnico detalhado para a implementação da Fase 1 do projeto "Fábrica Janus". O objetivo estratégico desta fase é estabelecer o **Codex Prime Framework**, o pilar fundamental que servirá como o "DNA Organizacional" para todo o desenvolvimento de software futuro. A arquitetura proposta é centrada em uma equipe de agentes de IA especializados, orquestrados pela biblioteca **LangGraph**, que operam dentro de um ecossistema de ferramentas desacoplado, regido pela estratégia **"MCP-First" (Model Context Protocol-First)**. O relatório detalha a definição dos agentes, o design do fluxo de trabalho de orquestração, a seleção e configuração de servidores MCP de código aberto e a integração com o ambiente de desenvolvimento do usuário, o Trae IDE. As escolhas arquitetônicas são deliberadamente projetadas para serem os blocos de construção dos futuros sistemas **Synapse Engine** e **Maestro.AI**, garantindo que a implementação da Fase 1 não seja apenas um objetivo em si, mas um passo estratégico e escalável em direção à visão completa da Fábrica Janus. Este blueprint fornece um caminho claro e acionável para transformar a visão conceitual em um sistema operacional e tangível.

---

## Seção 1: A Fundação Arquitetural da Fábrica Janus

### 1.1. A Visão do Ecossistema Integrado

A concepção da Fábrica Janus repousa sobre uma visão ambiciosa de um ecossistema de desenvolvimento de software aumentado por IA, estruturado em três pilares sinérgicos. A validação e formalização desta visão são o ponto de partida para qualquer implementação técnica.1

- **Codex Prime Framework:** Atuando como o "DNA Organizacional", este framework estabelece a base de governança e documentação mestra. Sua proposta de valor é garantir que cada produto nasça com um alicerce estratégico, tático e cultural coeso e de altíssima qualidade. Ele materializa a estratégia, a arquitetura e as melhores práticas em artefatos versionáveis e auditáveis.
    
- **Synapse Engine:** Concebido como a "Plataforma de Memória Cognitiva Temporal", este sistema transcende os mecanismos tradicionais de Retrieval-Augmented Generation (RAG). Seu propósito é construir e consultar um grafo de conhecimento que compreende as relações semânticas e temporais entre todas as entidades do ecossistema — desde requisitos de produto até decisões de arquitetura e linhas de código. Ele fornecerá o contexto profundo necessário para que os agentes de IA operem com um nível de entendimento quase humano.
    
- **Maestro.AI:** Funcionando como o "Sistema Operacional de Governança e Execução", o Maestro.AI é a camada que traduz a intenção estratégica humana em fluxos de trabalho orquestrados e executados por agentes de IA. Ele gerencia a delegação de tarefas, a aplicação de políticas e a automação de processos complexos, atuando como o sistema nervoso central da fábrica.
    

A interconexão destes pilares revela um ciclo de feedback virtuoso, transformando-os de projetos isolados em um sistema unificado de "Pensamento-para-Produto". O Codex Prime define _o que_ construir (o DNA). O Synapse Engine fornece o _conhecimento_ para construir corretamente (a memória). O Maestro.AI define _como_ é construído (a execução). Os artefatos gerados pelo Maestro.AI populam e refinam o Codex Prime, cujo conteúdo estruturado é a principal fonte de dados para o Synapse Engine, que, por sua vez, alimenta os agentes orquestrados pelo Maestro.AI. Esta arquitetura de ciclo fechado é o princípio orientador fundamental para todas as decisões técnicas subsequentes.

### 1.2. Foco da Fase 1: O Codex Prime Framework

A meta imediata e tangível da Fase 1 é a construção e população do Codex Prime Framework. Esta fase concentra-se em estabelecer a metodologia e as ferramentas para criar a documentação fundamental do projeto.

- **A Filosofia Docs-as-Code:** A escolha do Markdown com metadados em YAML Front Matter é uma decisão estratégica. Trata a documentação não como um subproduto, mas como um cidadão de primeira classe do ciclo de vida do desenvolvimento de software. Esta abordagem permite que a documentação seja versionada no Git, validada programaticamente e manipulada por agentes de IA. O YAML Front Matter estrutura os metadados de cada documento (ex: autor, status, versão, entidades relacionadas), tornando-os semanticamente ricos e prontos para processamento automático.
    
- **O Papel do `markdownlint` como Governança:** A ferramenta `markdownlint` é posicionada não apenas como um linter de estilo, mas como a primeira camada de governança automatizada do ecossistema. Ao definir um conjunto de regras estritas para a formatação do Markdown, ele garante consistência estrutural e estilística em todos os artefatos gerados. Isso impõe o aspecto de "altíssima qualidade" da proposta de valor do Codex Prime desde o início, garantindo que todos os documentos, sejam eles criados por humanos ou por IA, adiram a um padrão único e previsível.
    

### 1.3. Princípios Orientadores para a Seleção de Tecnologia

As decisões tecnológicas para a Fase 1 não são tomadas no vácuo; elas são diretamente informadas e guiadas pela visão de longo prazo do Synapse Engine e do Maestro.AI. Cada escolha é um bloco de construção para a arquitetura final.

- **Estruturas de Dados Prontas para o Synapse:** A metodologia Docs-as-Code, com seu uso rigoroso de Markdown e YAML Front Matter, é o precursor direto do Synapse Engine. A estrutura de arquivos e os metadados YAML são, na prática, uma representação serializada de um grafo de conhecimento. Cada arquivo é um nó, e os metadados (ex: `related_docs: [doc1.md, doc2.md]`) definem as arestas. Esta abordagem garante que, quando o Synapse Engine for desenvolvido, a migração dos dados do sistema de arquivos para o banco de dados de grafos (Neo4j) seja um processo de parsing e ingestão direto, em vez de um complexo projeto de extração de informações de documentos não estruturados. A base de conhecimento inicial, baseada em arquivos locais, é projetada como um caminho de migração de baixo atrito para o grafo.
    
- **Orquestração e Governança Prontas para o Maestro:** A escolha de LangGraph como framework de orquestração é uma consequência direta dos requisitos do Maestro.AI para fluxos de trabalho complexos, cíclicos, stateful e governáveis.2 Da mesma forma, a estratégia "MCP-First" para as ferramentas dos agentes é a camada fundamental para o modelo de execução desacoplado e orientado por políticas do Maestro.AI. Ao adotar LangGraph e MCP agora, estamos, na prática, construindo os componentes do kernel do Maestro.AI. A orquestração não é apenas uma automação de tarefas; é a implementação inicial do sistema operacional de governança e execução.
    

---

## Seção 2: O Motor de Orquestração: Arquitetando com LangGraph

A seleção do framework de orquestração é a decisão arquitetônica mais crítica da Fase 1, pois define a "arquitetura cognitiva" do sistema de agentes.

### 2.1. Por que LangGraph para o Maestro.AI

Uma análise comparativa de frameworks como CrewAI e LangGraph revela uma distinção fundamental em suas filosofias de design. CrewAI se destaca em processos hierárquicos e baseados em papéis, que são em grande parte sequenciais.4 Embora eficaz para tarefas lineares (ex: pesquisador -> escritor -> revisor), falta-lhe a flexibilidade nativa para modelar os fluxos de trabalho iterativos e complexos, como o "Duplo Diamante", que são centrais para a visão do Maestro.AI.

LangGraph, por outro lado, é a escolha superior porque modela os fluxos de trabalho como grafos de estado explícitos, não apenas como hierarquias de agentes.2 Isso oferece suporte de primeira classe para as primitivas essenciais exigidas pelo Maestro.AI:

- **Ciclos:** Permitem que o fluxo de trabalho revisite etapas anteriores, o que é crucial para processos iterativos de revisão e refinamento.8
    
- **Ramificação Condicional:** Permite que o fluxo de trabalho tome caminhos diferentes com base nos resultados de uma etapa, como uma revisão que pode ser "aprovada" ou "necessita de revisão".10
    
- **Execução Paralela (Fan-out/Fan-in):** Permite que várias tarefas sejam executadas simultaneamente e seus resultados sejam consolidados posteriormente, aumentando a eficiência.9
    
- **Persistência de Estado:** Salva automaticamente o estado do grafo a cada passo, permitindo pausar, retomar e até mesmo "viajar no tempo" para depurar ou intervir no processo.2
    

A curva de aprendizado mais acentuada do LangGraph é um investimento justificado pela potência, controle e alinhamento direto com os requisitos fundamentais do Maestro.AI.4

### 2.2. O Padrão Supervisor: Implementando o `@Orquestrador`

A arquitetura de orquestração será baseada no padrão de supervisor (ou roteador), onde um agente central direciona as tarefas para uma equipe de agentes trabalhadores especializados.8 Este padrão transforma o

`@Orquestrador` no kernel programável do sistema de agentes.

- **Definição do Estado:** O estado do grafo (`GraphState`) será definido usando `TypedDict` e `Annotated` para ser mais do que apenas um histórico de mensagens. Ele conterá o estado completo da tarefa em andamento: a solicitação original, a tarefa atual, o agente designado, os artefatos de entrada/saída e o histórico de decisões.8
    
    Python
    
    ```
    from typing import List, Optional, TypedDict
    from langchain_core.messages import BaseMessage
    
    class AgentTask(TypedDict):
        task_description: str
        input_artifacts: List[str]
        output_artifact: Optional[str]
        status: str # ex: "pending", "in_progress", "completed", "failed"
    
    class GraphState(TypedDict):
        messages: List
        original_request: str
        current_task: AgentTask
        assigned_agent: str # ex: "@EstrategistaDeProduto", "@RevisorCritico"
        task_queue: List
        final_artifact_path: Optional[str]
    ```
    
- **Nó Supervisor:** O nó do supervisor (`supervisor_node`) será uma função que invoca um LLM. Sua única responsabilidade é analisar o `GraphState` atual e a solicitação do usuário para determinar qual agente especializado deve atuar a seguir ou se a tarefa geral está concluída. A saída deste nó não é o conteúdo do trabalho, mas uma decisão de roteamento.11
    
- **Arestas Condicionais:** A lógica de roteamento será implementada usando `graph.add_conditional_edges`. A saída do nó supervisor determinará dinamicamente qual nó do agente será executado em seguida. Isso cria um fluxo de trabalho flexível que pode se adaptar à natureza da tarefa, em vez de seguir um caminho pré-definido.8
    

### 2.3. Modelando o Fluxo de Trabalho "Duplo Diamante"

Para exemplificar o poder desta arquitetura, podemos mapear o processo de design do "Duplo Diamante" para um ciclo em LangGraph:

1. **Descobrir (Divergir):** A solicitação inicial (ex: "Criar um documento de visão para o recurso X") é recebida. O `@Orquestrador` a roteia para o `@EstrategistaDeProduto`. Este agente, equipado com ferramentas de pesquisa na web, executa uma ampla pesquisa para coletar dados, insights de mercado e análises de concorrentes.
    
2. **Definir (Convergir):** A saída da fase de descoberta é devolvida ao `@Orquestrador`. Ele então delega a tarefa de síntese ao `@EstrategistaDeProduto` e ao `@ArquitetoDeFramework`. Juntos, eles analisam os dados brutos e os convergem em um documento de definição de problema claro e um conjunto de requisitos de alto nível.
    
3. **Desenvolver (Divergir):** Com o problema claramente definido, o `@Orquestrador` roteia a tarefa para o `@ArquitetoDeFramework` e o `@EscritorTecnico`. Eles exploram múltiplas soluções potenciais, criando rascunhos de artefatos como Documentos de Arquitetura de Alto Nível (HLD), diagramas e especificações técnicas.
    
4. **Entregar (Convergir):** Os rascunhos são passados para o `@RevisorCritico`. A saída deste agente é uma decisão: "aprovado" ou "revisão necessária" com feedback detalhado. A aresta condicional que sai do nó de revisão usará essa decisão para rotear o fluxo: se "aprovado", ele move para um nó de finalização que salva o artefato; se "revisão necessária", ele cria um novo ciclo, roteando de volta para a fase de "Desenvolver" com o feedback como novo input.9 Este ciclo de feedback é um recurso nativo do LangGraph e essencial para a produção de trabalho de alta qualidade.
    

### 2.4. Integração de Humano no Loop (HITL)

Para garantir governança e controle, especialmente em decisões de alto impacto, o Human-in-the-Loop é essencial. A persistência de estado do LangGraph torna isso uma implementação elegante.2

Um nó específico, `waitForHumanApproval`, será adicionado ao grafo. Este nó não executa uma chamada de LLM. Em vez disso, ele:

1. Usa o "checkpointer" do LangGraph para salvar o estado atual completo do grafo.
    
2. Imprime uma mensagem no console (ex: "Revisão humana necessária para o artefato 'HLD_auth_service.md'. Aprovar? [s/n]").
    
3. Pausa a execução do grafo, aguardando uma entrada externa (a resposta do desenvolvedor no terminal do Trae IDE).
    

Este mecanismo permite que o desenvolvedor atue como um "nó" no grafo, inserindo sua aprovação ou edições antes que o processo automatizado continue.

---

## Seção 3: Montando a Equipe de Agentes do Codex Prime

A eficácia do sistema emerge não da inteligência de um único agente, mas da colaboração sinérgica de uma equipe de especialistas com papéis, responsabilidades e ferramentas bem definidos. A especialização e a separação de preocupações são os princípios de design chave.

### 3.1. Definindo Personas e Papéis dos Agentes

Com base na solicitação do usuário e nos princípios de design de sistemas multiagente 11, a equipe inicial de agentes para o Codex Prime é definida da seguinte forma:

|Nome do Agente|Papel|Objetivo Principal|Diretivas Chave do Prompt do Sistema|Ferramentas MCP Primárias|
|---|---|---|---|---|
|`@Orquestrador`|Supervisor / Roteador de Tarefas|Decompor solicitações complexas em tarefas sequenciais e delegar cada tarefa ao agente mais apropriado.|"Você é um gerente de projetos de IA. Analise o estado atual e a próxima tarefa na fila. Sua única saída deve ser o nome do próximo agente a ser invocado (ex: '@EscritorTecnico') ou 'FINISH'."|Nenhuma (apenas lógica de roteamento)|
|`@EstrategistaDeProduto`|Estrategista de Produto|Definir o "Porquê" e o "O quê". Gerar artefatos como Documentos de Requisitos de Produto (PRDs), análises de mercado e histórias de usuário.|"Você é um Gerente de Produto sênior. Seu foco é a visão do produto, o valor para o usuário e a estratégia de mercado. Use a pesquisa na web para validar premissas. Seja claro e orientado por dados."|`web-search`, `filesystem:read_file`|
|`@ArquitetoDeFramework`|Arquiteto de Framework|Projetar a estrutura e os padrões do próprio Codex Prime. Definir esquemas YAML, estruturas de pastas e documentos de princípios de arquitetura.|"Você é um Arquiteto de Software Principal. Sua responsabilidade é a integridade estrutural e a consistência do framework. Defina templates claros, reutilizáveis e bem documentados."|`filesystem:read_file`, `filesystem:list_files`, `filesystem:write_file`|
|`@EscritorTecnico`|Escritor Técnico|Gerar o conteúdo principal dos documentos Markdown com base em inputs estruturados (esboços, requisitos) dos outros agentes.|"Você é um escritor técnico especializado. Sua tarefa é expandir os pontos fornecidos em uma prosa clara, concisa e tecnicamente precisa. Siga estritamente as diretrizes de estilo do projeto."|`filesystem:read_file`, `filesystem:write_file`|
|`@RevisorCritico`|Revisor Crítico / Quality Gate|Identificar falhas, inconsistências, ambiguidades e desvios das melhores práticas nos artefatos gerados.|"Você é um revisor técnico 'Red Team'. Seu objetivo é encontrar falhas. Seja implacável. Verifique a consistência lógica, clareza e aderência aos objetivos. Não elogie. Forneça uma lista de melhorias."|`filesystem:read_file`|
|`@Linter`|Agente de Ferramenta|Executar a validação de formato em um arquivo Markdown e retornar o resultado.|Este é um agente não-LLM. Ele simplesmente encapsula uma chamada de ferramenta.|`command-line:run_command`|

### 3.2. Criando Prompts de Sistema Eficazes

O "código-fonte" de um agente LLM é seu prompt de sistema. Um prompt bem estruturado é essencial para um comportamento previsível e de alta qualidade. Cada prompt para os agentes da Fábrica Janus seguirá um template padronizado, inspirado em práticas de engenharia de prompt 13:

1. **`Role` (Papel):** Uma declaração concisa da persona do agente. Ex: "Você é um Arquiteto de Framework de Software."
    
2. **`Goal` (Objetivo):** A tarefa primária que o agente deve realizar. Ex: "Seu objetivo é criar um template de documento de arquitetura em Markdown com um cabeçalho YAML Front Matter bem definido."
    
3. **`Constraints` (Restrições):** Regras inquebráveis. Ex: "Produza apenas saídas em formato Markdown válido. Não invente ferramentas que não foram fornecidas. Responda apenas com o conteúdo do arquivo, sem conversação extra."
    
4. **`Workflow` (Fluxo de Trabalho):** Um guia passo a passo de como o agente deve pensar. Ex: "1. Analise o documento de requisitos de entrada. 2. Identifique as seções principais necessárias. 3. Esboce a estrutura do documento. 4. Preencha cada seção com conteúdo detalhado."
    
5. **`Tool Definitions` (Definições de Ferramentas):** Uma descrição clara das ferramentas disponíveis e quando usá-las.
    

### 3.3. A Base de Conhecimento Fundacional (Pré-Synapse)

Na Fase 1, antes da implementação do Synapse Engine, o sistema de arquivos local servirá como a base de conhecimento primária. Uma estrutura de diretórios clara e hierárquica é crucial para que os agentes possam navegar e entender seu "mundo".

A estrutura do projeto será:

```
/fabrica-janus

|--.vscode/
| |-- mcp.json       # Configuração dos servidores MCP para o Trae IDE
|--.gemini/
| |-- settings.json  # Configuração do Gemini CLI
|-- codex/
| |-- templates/     # Templates de documentos do Codex Prime
| | |-- prd_template.md
| | |-- hld_template.md
| |-- docs/          # Documentos gerados para um projeto específico
| | |-- strategy/
| | |-- architecture/
|-- src/
| |-- agents/        # Código-fonte dos agentes e da orquestração
|-- docker-compose.yml # Para iniciar os servidores MCP
|-- GEMINI.md          # Contexto e regras para o Gemini CLI
```

O `@Orquestrador` será responsável por fornecer os caminhos de arquivo relevantes como parte do contexto para os agentes trabalhadores. Por exemplo, ao delegar uma tarefa de escrita, ele fornecerá o caminho do template a ser usado e o caminho do arquivo de saída a ser criado. Este modelo, embora simples, estabelece os padrões de interação com dados que serão essenciais para o futuro Synapse Engine. As interações dos agentes com esta estrutura de arquivos gerarão os dados e metadados que, eventualmente, alimentarão o grafo de conhecimento.

---

## Seção 4: O Ecossistema de Ferramentas "MCP-First"

A estratégia "MCP-First" é uma decisão arquitetônica que promove flexibilidade, desacoplamento e resiliência futura. Ela transforma o problema de "integração de ferramentas" em um de "descoberta e orquestração de serviços".

### 4.1. O Model Context Protocol (MCP) como um Habilitador Estratégico

O Model Context Protocol (MCP) é um padrão aberto que padroniza a comunicação entre um agente de IA (cliente) e uma ferramenta externa (servidor).14 Em vez de codificar integrações de API específicas para cada ferramenta dentro do framework do agente, os agentes são projetados para "falar" MCP.

A vantagem estratégica é imensa: os agentes podem descobrir e utilizar qualquer ferramenta exposta por um servidor MCP em seu ambiente sem que seu próprio código precise ser alterado.17 Isso cria um sistema de ferramentas "plug-and-play". Podemos trocar uma implementação de servidor de sistema de arquivos por outra, ou adicionar uma nova ferramenta de análise de código, e os agentes existentes poderão usá-la imediatamente. Essa flexibilidade é um pilar da visão do Maestro.AI de um sistema de execução dinâmico e governável.

A tabela a seguir resume a pilha de servidores MCP recomendada para a Fase 1.

|Função|Servidor Recomendado|Fonte / Link|Funcionalidades Chave|Método de Configuração Primário|
|---|---|---|---|---|
|**Acesso ao Sistema de Arquivos**|`cyanheads/filesystem-mcp-server` (TypeScript)|[github.com/cyanheads/filesystem-mcp-server](https://github.com/cyanheads/filesystem-mcp-server) 18|Leitura, escrita, listagem, exclusão, movimentação de arquivos/diretórios. Validação de caminho segura.|`docker-compose.yml`, `mcp.json`|
|**Integração com GitHub**|`github/github-mcp-server` (Go)|[github.com/github/github-mcp-server](https://github.com/github/github-mcp-server) 19|Gerenciamento de repositórios, issues, pull requests, ações. Autenticação OAuth 2.0.|`mcp.json` (usando a versão remota hospedada)|
|**Execução de Comandos**|`Terminal-Control` (Referência)|[github.com/modelcontextprotocol/servers](https://github.com/modelcontextprotocol/servers) 20|Execução segura de comandos de terminal, navegação de diretório.|`docker-compose.yml`, `mcp.json`|
|**Pesquisa na Web**|Gemini CLI (Integrado) / Tavily (LangChain)|N/A (Integrado ao Agente)|Pesquisa na web em tempo real para coleta de informações.|`GEMINI.md` / Código do Agente|
|**Gerenciamento de Tarefas**|`Task-Master.dev`|[task-master.dev](https://www.task-master.dev/) 21|Decomposição de objetivos de alto nível em tarefas estruturadas com dependências.|`mcp.json`|

### 4.2. Acesso ao Sistema de Arquivos

A capacidade de ler e escrever no sistema de arquivos local é a ferramenta mais crítica para um fluxo de trabalho Docs-as-Code.

- **Análise:** É necessária uma implementação de servidor MCP que seja robusta, segura e que ofereça um conjunto completo de operações de arquivo.
    
- **Recomendação:** O `cyanheads/filesystem-mcp-server` 18 ou o
    
    `mark3labs/mcp-filesystem-server` 22 são excelentes opções de código aberto. A recomendação pende para a versão em TypeScript (
    
    `cyanheads`) devido à sua arquitetura clara e uso de Zod para validação de esquemas, o que aumenta a robustez.
    
- **Configuração:** O servidor será executado como um contêiner Docker, gerenciado via `docker-compose.yml`. No Trae IDE, o arquivo `.vscode/mcp.json` será configurado para se conectar a este servidor local. Uma configuração de segurança crucial será restringir o acesso do servidor apenas ao diretório raiz do projeto, prevenindo ataques de travessia de diretório.15
    

### 4.3. Integração com GitHub

Sendo o Maestro.AI "GitHub-Nativo", o acesso programático à API do GitHub é essencial.

- **Análise:** É necessário gerenciar repositórios, issues e, futuramente, pull requests e projetos.
    
- **Recomendação:** O **servidor MCP oficial do GitHub** é a escolha definitiva.19 Para a Fase 1, a versão remota (hospedada pelo GitHub) é recomendada por sua simplicidade de configuração e manutenção zero.24
    
- **Configuração:** A configuração no `.vscode/mcp.json` utilizará o endpoint remoto e a autenticação via OAuth 2.0, que é mais segura e flexível do que tokens de acesso pessoal (PATs).25 Além disso, o parâmetro
    
    `toolsets` será usado para aplicar o princípio do menor privilégio, habilitando apenas os conjuntos de ferramentas estritamente necessários para a Fase 1 (ex: `repos`, `issues`) e desabilitando outros (ex: `actions`, `code_security`).19
    

### 4.4. Capacidade de Pesquisa Profunda

O `@EstrategistaDeProduto` requer acesso a informações atualizadas da web para realizar suas tarefas.

- **Análise:** A pesquisa precisa ser integrada de forma que possa ser usada tanto interativamente pelo desenvolvedor quanto autonomamente pelos agentes.
    
- **Recomendação:** Uma abordagem dupla é proposta:
    
    1. **Pesquisa Interativa via Gemini CLI:** O desenvolvedor pode usar a ferramenta `web-search` embutida no Gemini CLI para realizar pesquisas iniciais e fornecer os resultados como contexto para o fluxo de trabalho do LangGraph.14 Isso permite uma rápida exploração humana.
        
    2. **Pesquisa Autônoma do Agente:** Para fluxos de trabalho totalmente automatizados, uma ferramenta de pesquisa como a API da Tavily será integrada diretamente na definição do agente `@EstrategistaDeProduto` dentro do LangGraph, como é comum em exemplos da LangChain.11 Isso dá ao agente a capacidade de realizar suas próprias pesquisas dinamicamente conforme necessário.
        

### 4.5. Ferramentas Especializadas: Linting e Gerenciamento de Projetos

- **Linting (`markdownlint`):** Em vez de dar aos agentes acesso total ao shell, uma abordagem mais segura e modular é recomendada. Um **servidor MCP de linha de comando** genérico (como o `Terminal-Control` 20) será configurado. O agente
    
    `@Linter` terá acesso a uma única ferramenta exposta por este servidor: `run_command`. A configuração do agente restringirá esta ferramenta a executar apenas o comando `markdownlint --fix /caminho/para/o/arquivo`. Isso encapsula a funcionalidade de linting em um serviço seguro e bem definido.
    
- **Gerenciamento de Projetos (`Task-Master.dev`):** Esta ferramenta pode atuar como um aprimoramento para o `@Orquestrador`.21 Seu valor reside na capacidade de pegar um objetivo de alto nível e decompô-lo em uma lista estruturada de tarefas com dependências.28 Ele pode ser integrado via sua interface MCP e usado pelo
    
    `@Orquestrador` para pré-processar solicitações complexas do usuário, gerando uma fila de tarefas (`task_queue` no `GraphState`) que então orienta a delegação para os agentes trabalhadores.
    

---

## Seção 5: Aumentando o Cockpit do Desenvolvedor: Integração com o Trae IDE

O ambiente de desenvolvimento integrado (IDE) não é apenas um editor de texto; é o cockpit do desenvolvedor. A integração da Fábrica Janus neste ambiente deve ser fluida e potente, criando uma parceria entre o desenvolvedor humano e o sistema de agentes.

### 5.1. Gemini CLI: O Agente no seu Terminal

O Gemini CLI é posicionado como o agente de "espaço do usuário" — a interface direta e conversacional do desenvolvedor com o ecossistema da Fábrica Janus. Ele atua como a ponte para o orquestrador LangGraph, que opera no "espaço do kernel".

- **Papel:** O Gemini CLI é uma ferramenta versátil para uma ampla gama de tarefas, desde a geração de conteúdo e resolução de problemas até a pesquisa profunda e gerenciamento de tarefas.14 No contexto da Fábrica Janus, ele serve como o ponto de entrada para invocar os fluxos de trabalho mais complexos dos agentes.
    
- **Configuração:** A instalação é direta através do `npm` no terminal integrado do Trae IDE: `npm install -g @google/gemini-cli`.26 A autenticação aproveitará a conta Google One existente do usuário, que fornece um generoso nível gratuito de uso do Gemini Pro (até 1.000 solicitações/dia).30
    

### 5.2. Estabelecendo o Contexto do Projeto com `GEMINI.md`

Uma capacidade poderosa e muitas vezes subutilizada do Gemini CLI é sua habilidade de ler um arquivo `GEMINI.md` na raiz do projeto para obter contexto e instruções persistentes.26 Este arquivo funciona como um "meta-prompt" no nível do projeto.

- **Implementação:** Um arquivo `GEMINI.md` será criado na raiz do repositório da Fábrica Janus. Ele conterá diretivas que governam o comportamento do Gemini CLI dentro daquele projeto específico.
    
- **Exemplo de Conteúdo para `GEMINI.md`:**
    
    # Diretivas do Projeto Fábrica Janus para o Agente Gemini
    
    ## Regras Gerais
    
    - Sempre use mensagens de commit no formato Conventional Commits.
        
    - Ao criar novos artefatos do Codex, sempre comece a partir de um template localizado em `/codex/templates/`.
        
    - A fonte da verdade para as tarefas do projeto é a API do GitHub Projects.
        
    
    ## Ferramentas Preferenciais
    
    - Para operações de arquivo, sempre use as ferramentas do servidor MCP `filesystem`.
        
    - Para interações com o repositório, use as ferramentas do servidor MCP `github`.
        
    - O comando `!fabrica-janus` é o ponto de entrada para o sistema de orquestração de agentes.
        

### 5.3. Conectando o Cockpit à Fábrica

A interação primária do desenvolvedor com o sistema de agentes de back-end será mediada pelo terminal do Trae IDE. Isso cria uma separação de preocupações clara e um fluxo de trabalho robusto.

- **Fluxo de Trabalho:**
    
    1. O desenvolvedor abre o terminal integrado no Trae IDE e inicia a sessão do Gemini CLI com o comando `gemini`. O CLI carrega automaticamente o contexto do `GEMINI.md` e se conecta aos servidores MCP configurados no `.vscode/mcp.json`.
        
    2. O desenvolvedor emite uma instrução de alto nível, prefixada com um alias de shell para invocar o sistema de back-end. Ex: `!fabrica-janus "Crie um Documento de Requisitos de Produto para um novo serviço de autenticação, baseando-se nas metas estratégicas definidas em /codex/docs/strategy/q3_goals.md"`.
        
    3. O alias `!fabrica-janus` é um script de shell simples que executa a aplicação Python do LangGraph, passando a string do prompt como um argumento de linha de comando.
        
    4. A aplicação LangGraph inicia, com o `@Orquestrador` recebendo a tarefa e começando o processo de orquestração dos agentes.
        
    5. Os logs detalhados da execução do grafo (decisões do orquestrador, ações dos agentes, saídas das ferramentas) são transmitidos em tempo real para o terminal do Trae IDE, fornecendo observabilidade completa do processo.
        
    6. Quando um ponto de verificação de Humano no Loop (HITL) é alcançado, o processo LangGraph pausa e imprime uma solicitação de entrada no terminal (ex: "Revisão necessária. Aprovar? [s/n]"). O desenvolvedor responde diretamente na mesma sessão do terminal para permitir que o fluxo de trabalho continue.
        

Este modelo de interação cria uma experiência de "cockpit" poderosa, onde o desenvolvedor comanda e supervisiona a "fábrica" de agentes a partir de seu ambiente familiar, o terminal do IDE. A distinção entre o agente local (Gemini CLI) para tarefas imediatas e o sistema de agentes de back-end (LangGraph) para processos complexos é mantida, com uma interface de linha de comando bem definida conectando os dois mundos.

---

## Seção 6: Blueprint da Fase 1: Uma Proposta de Configuração Unificada

Esta seção final sintetiza todos os componentes em um plano de implementação coeso e acionável, fornecendo a arquitetura, os fluxos de trabalho e os arquivos de configuração necessários para iniciar a Fase 1.

### 6.1. O Diagrama da Arquitetura do Sistema

A arquitetura completa da Fase 1 pode ser visualizada como uma pilha de camadas interconectadas:

- **Camada de Usuário:** O desenvolvedor humano operando dentro do **Trae IDE**.
    
- **Camada de Interface:** O **Terminal Integrado** do Trae IDE, executando o **Gemini CLI**. Esta é a interface de comando e controle.
    
- **Camada de Orquestração:** A aplicação **LangGraph** (executada como um processo Python), que contém o **`@Orquestrador`** e a **Equipe de Agentes** (`@EstrategistaDeProduto`, `@EscritorTecnico`, etc.).
    
- **Camada de Ferramentas (Serviços MCP):** Um conjunto de **Servidores MCP** (idealmente executados como contêineres Docker gerenciados por `docker-compose`), cada um expondo uma capacidade específica: Acesso ao Sistema de Arquivos, Execução de Linha de Comando e, opcionalmente, o servidor GitHub local. O servidor GitHub remoto é acessado via rede.
    
- **Camada de Dados:** O **Sistema de Arquivos Local** do projeto, contendo a estrutura de diretórios `codex/`, que serve como a base de conhecimento inicial (o precursor do Synapse Engine).
    

A seguir, uma matriz de controle de acesso que define a política de segurança do sistema, alinhada ao princípio do menor privilégio.

|Agente|`filesystem:read_file`|`filesystem:write_file`|`filesystem:list_files`|`github:create_issue`|`command-line:run_command`|`web-search`|
|---|---|---|---|---|---|---|
|`@Orquestrador`|||||||
|`@EstrategistaDeProduto`|✓|✓|✓|||✓|
|`@ArquitetoDeFramework`|✓|✓|✓||||
|`@EscritorTecnico`|✓|✓|||||
|`@RevisorCritico`|✓|||✓|||
|`@Linter`|||||✓||

### 6.2. Fluxo de Trabalho de Colaboração Agente-Ferramenta (Exemplo de Ponta a Ponta)

Vamos rastrear uma solicitação completa para ilustrar a interação dinâmica entre os componentes:

**Solicitação do Desenvolvedor:** `!fabrica-janus "Rascunhe um documento de arquitetura para nossa nova camada de cache, referenciando nosso documento de metas de desempenho em /codex/docs/strategy/performance_goals.md."`

1. **Interface (Gemini CLI):** O comando é executado, invocando a aplicação LangGraph com o prompt.
    
2. **Orquestração (`@Orquestrador`):** Recebe a tarefa. Analisa o pedido e determina que a primeira ação é de natureza arquitetônica. Roteia a tarefa para o `@ArquitetoDeFramework`.
    
3. **Agente (`@ArquitetoDeFramework`):** Recebe a tarefa. Para cumpri-la, precisa primeiro ler o documento de metas. Ele formula uma solicitação para usar a ferramenta `filesystem:read_file` com o argumento `/codex/docs/strategy/performance_goals.md`.
    
4. **Ferramenta (Filesystem MCP):** O LangGraph roteia a chamada da ferramenta para o servidor MCP do sistema de arquivos. O servidor lê o arquivo e retorna seu conteúdo.
    
5. **Agente (`@ArquitetoDeFramework`):** Com o contexto das metas de desempenho, ele rascunha o documento de arquitetura da camada de cache em Markdown. Ele então retorna o rascunho como sua saída.
    
6. **Orquestração (`@Orquestrador`):** Recebe o rascunho. O próximo passo lógico no fluxo de trabalho é a revisão. Ele roteia o rascunho para o `@RevisorCritico`.
    
7. **Agente (`@RevisorCritico`):** Analisa o documento de arquitetura. Com seu prompt de "Red Team", ele identifica uma ambiguidade na estratégia de invalidação de cache e sugere uma abordagem mais robusta. Ele retorna sua saída como um conjunto de sugestões de revisão.
    
8. **Orquestração (`@Orquestrador`):** Recebe o feedback da revisão. Como a revisão não foi uma aprovação direta, ele inicia um ciclo de iteração. Ele roteia a tarefa de volta para o `@ArquitetoDeFramework`, incluindo o rascunho original e o feedback do revisor como novo contexto.
    
9. **Agente (`@ArquitetoDeFramework`):** Incorpora o feedback, refina a seção de invalidação de cache e submete a versão final.
    
10. **Orquestração (`@Orquestrador`):** Recebe a versão final. Agora, ele a roteia para o `@Linter` para garantir a conformidade do formato.
    
11. **Agente (`@Linter`):** Invoca a ferramenta `command-line:run_command` com os argumentos `markdownlint --fix /caminho/para/o/rascunho.md`.
    
12. **Ferramenta (Command-Line MCP):** O servidor MCP executa o comando `markdownlint`, que corrige quaisquer problemas de formatação e retorna um código de saída 0.
    
13. **Orquestração (`@Orquestrador`):** Recebe a confirmação de que o arquivo está limpo e formatado. A tarefa está completa. Ele roteia para o nó `FINISH`.
    
14. **Finalização:** A aplicação LangGraph salva o artefato final em `/codex/docs/architecture/caching_layer.md` e o processo termina com sucesso.
    

### 6.3. Arquivos de Configuração Recomendados (Setup Chave na Mão)

Para acelerar a implementação, os seguintes arquivos de configuração são propostos:

- **`docker-compose.yml`:**
    
    YAML
    
    ```
    version: '3.8'
    services:
      filesystem_mcp:
        image: ghcr.io/cyanheads/filesystem-mcp-server:latest
        container_name: mcp_filesystem
        command: ["--mcp-allowed-path", "/app"]
        volumes:
          -.:/app
        ports:
          - "3010:3010" # Se usar transporte HTTP
        environment:
          - MCP_TRANSPORT=stdio # ou http
          - MCP_LOG_LEVEL=info
          - NODE_ENV=production
    
      # Adicionar outros servidores MCP locais aqui, como o de linha de comando
    ```
    
- **`.vscode/mcp.json`:**
    
    JSON
    
    ```
    {
      "mcp": {
        "servers": {
          "filesystem": {
            "command": "docker",
            "args": ["exec", "-i", "mcp_filesystem", "node", "dist/index.js"],
            "env": {
              "MCP_TRANSPORT": "stdio"
            }
          },
          "github": {
            "type": "http",
            "url": "https://api.githubcopilot.com/mcp/",
            "toolsets": ["repos", "issues", "pull_requests"]
          }
        }
      }
    }
    ```
    
- **`.env.template`:**
    
    ```
    # Chaves de API
    GEMINI_API_KEY="sua_chave_api_do_gemini"
    # Autenticação para o servidor GitHub MCP (se usar PAT em vez de OAuth)
    # GITHUB_PERSONAL_ACCESS_TOKEN="seu_token_github"
    ```
    

### 6.4. Próximos Passos e Caminho de Evolução

A conclusão bem-sucedida da Fase 1, marcada pela capacidade da equipe de agentes de gerar e gerenciar autonomamente os artefatos do Codex Prime Framework, estabelece a base para as fases subsequentes.

- **Caminho para o Synapse Engine:** O próximo passo lógico é começar a construir o Synapse Engine. Isso envolverá o desenvolvimento de um processo para:
    
    1. Analisar (parse) programaticamente todos os documentos Markdown gerados na pasta `codex/`.
        
    2. Extrair entidades (ex: nomes de recursos, decisões de arquitetura) e relacionamentos a partir do conteúdo e, crucialmente, do YAML Front Matter.
        
    3. Popular um banco de dados Neo4j com esses nós e arestas, construindo a versão inicial do grafo de conhecimento.
        
- **Caminho para o Maestro.AI:** A evolução para o Maestro.AI completo envolverá:
    
    1. Formalizar a aplicação LangGraph do `@Orquestrador` em um serviço mais robusto e persistente.
        
    2. Integrar a API do GitHub Projects, exposta através do servidor MCP do GitHub, para mover a "fonte da verdade" de listas de tarefas implícitas para itens de trabalho explícitos no GitHub.
        
    3. Desenvolver o motor de políticas desacoplado (ex: Open Policy Agent) para governar as ações dos agentes, implementando o Controle de Acesso Baseado em Atributos (ABAC) definido na matriz de acesso.
        

Ao seguir este blueprint, a Fábrica Janus pode evoluir de um conceito poderoso para uma realidade operacional, construindo sistematicamente cada camada de sua arquitetura integrada e autônoma.