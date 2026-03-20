---
sticker: lucide//chevron-right-square
---
# Guia Prático e Definitivo de Orquestração Agêntica para o Genesis MC no Antigravity IDE

A engenharia de um Sistema Operacional Agêntico (Sovereign OS) focado em privacidade e execução local-first, designado como Genesis MC (SODA), impõe desafios arquiteturais singulares. A fundação de baixo nível estabelecida por Rust, o motor de interface encapsulado pelo Tauri e a persistência atômica via SQLite criam um ecossistema de extrema eficiência e segurança. Contudo, a introdução de agentes autônomos baseados em Large Language Models (LLMs) neste ambiente exige uma infraestrutura de controle rigorosa. O desenvolvimento orientado a inteligência artificial frequentemente sofre de degradação de contexto ("context rot"), alucinações de código ("vibe coding") e consumo predatório de recursos de hardware. O Google Antigravity IDE emerge como a plataforma primária de "Mission Control" para mitigar estas falhas, orquestrando agentes assíncronos que planejam, executam e verificam tarefas complexas através de uma arquitetura estritamente orientada a especificações.

Este relatório fornece um manual arquitetural e operacional exaustivo para a configuração do ambiente de trabalho do Genesis MC, detalhando os fluxos de documentação, a integração de metodologias determinísticas e a conexão de servidores do Model Context Protocol (MCP) otimizados para operações locais.

## 1. Setup Master do Antigravity IDE para o Genesis MC

A inicialização de um projeto de alta complexidade no Antigravity transcende a mera criação de diretórios; trata-se de programar o "sistema nervoso central" que ditará o comportamento da inteligência artificial sobre o código-fonte. O Antigravity evolui o paradigma do Integrated Development Environment (IDE) tradicional para uma plataforma "Agent-First", separando a interface em dois domínios principais: o Editor View (baseado na fundação do VS Code) para interações síncronas, e o Agent Manager, uma superfície dedicada onde múltiplos agentes operam assincronamente.

### 1.1. Processo Exato de Inicialização do Projeto

A configuração inicial deve garantir o isolamento do ambiente e a aplicação estrita de políticas de segurança, prevenindo que agentes executem ações destrutivas no núcleo em Rust sem supervisão explícita.

O processo de configuração começa com a criação de um diretório limpo e a inicialização do IDE. Durante a configuração do "Agent Mode", a seleção de autonomia deve ser ajustada criticamente. As políticas de execução de terminal no Antigravity são categorizadas em três níveis: "Off" (desativado), "Auto" (o agente decide quando pedir permissão) e "Turbo" (execução autônoma contínua). Para o Genesis MC, a política deve ser estritamente fixada em uma variação controlada, utilizando a configuração de "Request review" combinada com uma "Allow list". Isso garante que comandos de compilação pesados, como `cargo build --release` ou manipulações destrutivas do sistema de arquivos local, exijam a validação humana.

Adicionalmente, a transição do "Fast mode" para o "Planning mode" no painel do agente é imperativa. O modo de planejamento força o orquestrador a analisar o prompt, gerar um plano de implementação detalhado ("Implementation Plan") e uma lista de tarefas ("Task List") antes de emitir qualquer alteração no código. Este comportamento previne a geração de código impulsiva, alinhavando a execução com a visão arquitetural do Sovereign OS.

### 1.2. Taxonomia e Estrutura da Árvore de Diretórios Agêntica

A árvore de diretórios atua como a principal interface de programação comportamental para o ecossistema agêntico. A estrutura do Genesis MC deve isolar o código-fonte compilável do contexto cognitivo dos agentes, garantindo que o LLM não sofra sobrecarga de tokens ao ler arquivos irrelevantes.

A tabela abaixo detalha a estrutura de diretórios ideal e a função prática de cada segmento dentro da arquitetura do Antigravity IDE:

|**Diretório / Arquivo**|**Função Prática na Orquestração Agêntica**|**Impacto no Contexto do LLM**|
|---|---|---|
|`AGENTS.md`|O manifesto global do projeto. Define o propósito do Sovereign OS (Genesis MC), o papel do usuário e a fundação tecnológica macro (Rust/Tauri/SQLite).|Carregado permanentemente no contexto raiz. Deve ser restrito a menos de 200 linhas para evitar exaustão prematura de tokens.|
|`.agents/rules/`|Leis e restrições passivas do sistema. Contém arquivos que definem padrões de segurança de memória em Rust, convenções de commits e formatação arquitetural.|Ativos continuamente. As regras são injetadas no prompt do sistema como restrições imutáveis que o agente não pode violar.|
|`.agents/skills/`|Capacidades modulares e encapsuladas. Diretórios contendo arquivos `SKILL.md` que detalham instruções para tarefas complexas e muito específicas (ex: Integração Tauri IPC).|Carregamento sob demanda ("Progressive Disclosure"). Oculto do contexto ativo até que a semântica da tarefa exija a habilidade.|
|`.agents/workflows/`|Procedimentos ativos e sequenciais. Contém definições de processos disparados pelo usuário (ex: `/test-rust-core`) para automatizar pipelines de auditoria ou deploy.|Injetado apenas quando o comando de gatilho correspondente é acionado no chat do Agent Manager.|
|`docs/specs/`|Repositório de Especificações. Armazena os documentos gerados pelo framework Spec-Driven Development (SDD) e os Architecture Decision Records (ADRs).|Fonte de verdade referenciada ativamente pelos agentes durante a fase de planejamento e verificação de tarefas.|

**A Resolução do Conflito entre `AGENTS.md` e `GEMINI.md`:** No ecossistema do Antigravity IDE, existe uma ambiguidade documentada entre o uso do arquivo `GEMINI.md` e `AGENTS.md`. Historicamente, o IDE priorizava o `GEMINI.md`, o que causava "poluição de configuração" quando desenvolvedores utilizavam simultaneamente o Gemini CLI, resultando em instruções vazando entre as ferramentas e gerando comportamentos inesperados. Atualizações recentes no Antigravity (a partir da versão 1.20.3) introduziram suporte nativo à leitura do arquivo padrão da indústria, o `AGENTS.md`.

Para a construção do Genesis MC, a adoção exclusiva do `AGENTS.md` é a via arquitetural correta. Recomenda-se evitar a inserção de minúcias de framework neste arquivo; detalhes como o tratamento do gerenciador de estado do React ou as nuances do compilador do Rust devem ser migrados para arquivos dedicados dentro da pasta `.agents/rules/` (ex: `rust-safety-rules.md`). O arquivo raiz deve focar exclusivamente na ontologia do projeto e nas diretrizes de comportamento do agente.

### 1.3. A Engenharia de 'Progressive Disclosure' nos Arquivos SKILL.md

A injeção excessiva de documentação técnica no prompt do sistema resulta em uma falha cognitiva conhecida como "Context Saturation" ou saturação de contexto. Quando um LLM é bombardeado com as documentações completas do Tauri, do ecossistema SQLite e da linguagem Rust simultaneamente, ele perde a capacidade de focar na instrução primária, aumentando a latência de inferência e a taxa de alucinação.

A solução implementada pelo Antigravity é o paradigma de "Progressive Disclosure" (Revelação Progressiva). Neste modelo, a arquitetura de habilidades (`.agents/skills/`) atua como uma biblioteca dinâmica. O agente orquestrador possui acesso inicial apenas a um "menu" leve contendo os metadados (nome e descrição curta) de cada habilidade disponível. O conteúdo instrucional massivo e os scripts anexos só são descompactados e injetados no buffer de contexto quando o agente detecta que a intenção da tarefa do usuário corresponde à descrição da habilidade.

A estruturação exata de um arquivo `SKILL.md` para suportar essa mecânica exige um bloco de metadados YAML (Frontmatter) rigidamente formatado, seguido por instruções determinísticas e gatilhos de ativação.

Abaixo é apresentado um template Markdown exato para uma habilidade focada na integração Rust e Tauri IPC, estruturada para maximizar o reconhecimento do agente sem desperdiçar tokens no "Cold Start":

---

## name: tauri-ipc-handler-rust description: Implementa funções seguras de backend em Rust e as expõe para o frontend React via Tauri IPC (Inter-Process Communication). Use ESTA HABILIDADE EXCLUSIVAMENTE quando o usuário solicitar a criação de uma ponte de comunicação entre a interface visual (React/Canvas) e o sistema operacional subjacente (Daemon/SQLite).

# Tauri IPC Handler Skill

## 1. Regras de Definição Atômica

- Toda função invocável do backend deve ser estritamente anotada com o macro `#[tauri::command]`.
- A assinatura da função deve retornar obrigatoriamente um tipo `Result<T, String>` para lidar com falhas de forma graciosa perante o frontend.
- O tratamento de erros de crates externas (ex: `rusqlite` ou `reqwest`) deve ser convertido para String imediatamente utilizando o combinador `.map_err(|e| e.to_string())`.

## 2. Paradigma de Segurança e Isolamento

- É terminantemente proibido o uso de dependências Node.js no frontend para operações de sistema. Toda leitura de arquivo, conexão de rede ou manipulação do SQLite deve ser roteada via IPC para os binários em Rust.
- Valide rigorosamente a desserialização de payloads JSON recebidos como argumentos do frontend, garantindo que as estruturas (`structs`) possuam a anotação `#`.

## 3. Registro no Builder Principal

- Após a criação do comando, modifique o arquivo `src-tauri/src/main.rs`.
- Registre o handler no builder do aplicativo invocando `.invoke_handler(tauri::generate_handler![nome_da_sua_funcao])`.

## 4. Gatilhos de Ativação (Quando aplicar)

- Quando a tarefa mencionar "conectar o frontend ao banco de dados".
- Quando for solicitado "chamar um script local a partir de um botão na UI".

Ao utilizar essa sintaxe e formatação, o Antigravity IDE expõe a seção `description` para a rotina de roteamento do LLM, mantendo as regras pesadas de implementação e segurança dormências no disco rígido até o exato momento em que uma tarefa de IPC é solicitada.

## 2. Integração Prática de Metodologias: BMAD e SDD

A ausência de disciplina metodológica na utilização de agentes codificadores culmina na criação de bases de código fragmentadas, onde os padrões arquiteturais desaparecem entre iterações e a dívida técnica se acumula rapidamente. Para o Genesis MC, um sistema focado em privacidade e operação local restrita, cada linha de Rust e cada consulta SQLite devem obedecer a um plano mestre. Para alcançar este nível de rigor, o ambiente deve integrar simultaneamente a orquestração de papéis do BMAD e o rigor documental do SDD.

### 2.1. O Ecossistema BMAD (Build More Architect Dreams) v6

A metodologia BMAD v6 transforma a interface assíncrona do Antigravity em um estúdio de desenvolvimento corporativo completo, simulando as interações de uma equipe multidisciplinar através de inteligência adaptativa ("Scale-Adaptive Intelligence"). O sistema abandona a premissa de um único assistente genérico e introduz agentes especializados com papéis estritos: Analista, Product Manager (PM), Arquiteto, Scrum Master (SM), Desenvolvedor e QA.

**Instalação e Configuração no Antigravity:** O BMAD v6 foi arquitetado para ser nativo aos ambientes agênticos. A configuração exige a transferência da biblioteca de regras do BMAD para os diretórios estruturais do Antigravity. As definições YAML obsoletas de versões anteriores foram convertidas em habilidades unificadas baseadas em Markdown (arquivos `SKILL.md` como pontos de entrada) dentro do diretório `.agents/skills/` ou `.agents/workflows/`, permitindo que os comandos de gatilho sejam ativados diretamente no chat.

**O Fluxo de Trabalho Exato (Workflow):**

O ciclo de vida da metodologia obriga que as especificações técnicas e as decisões de segurança sejam finalizadas antes da geração de código. O fluxo a seguir ilustra a operação real no chat do IDE para o desenvolvimento de um novo módulo no SODA:

A tabela abaixo sumariza o roteamento de comandos da metodologia BMAD v6 dentro do Antigravity :

|**Comando de Gatilho**|**Agente Invocado**|**Ação e Artefato Produzido**|
|---|---|---|
|`/bmad analyst workflow-status`|Analista|Avalia o estado atual do repositório, identifica gargalos e estrutura o "Project Brief" baseado nos requisitos iniciais do usuário.|
|`/pm plan-project`|Product Manager|Traduz a visão do Analista em Requisitos Funcionais tangíveis, particionando o escopo em Épicos e Histórias de Usuário (User Stories) focadas.|
|`/architect solution-architecture`|Arquiteto|Desenha o projeto técnico "Full-Stack". Gera diagramas de componentes, delineia os limites de confiança (Trust Boundaries) essenciais para a segurança do Tauri, e define modelos de dados para o SQLite.|
|`/bmad sm create-story`|Scrum Master|Isola o contexto. Extrai uma história de usuário específica da fila e prepara a base documental restrita para a intervenção do desenvolvedor, gerando o contexto atômico.|
|`/bmad dev dev-story`|Desenvolvedor|Inicia a escrita real do código em Rust ou a estruturação da UI em React, limitando-se rigorosamente à arquitetura imposta pelo agente Arquiteto.|

O uso sequencial destes comandos bloqueia a inclinação natural do LLM para a improvisação, assegurando que o código compilado reflita um design sistêmico validado e resiliente.

### 2.2. Spec-Driven Development (SDD) no Ambiente Agêntico

Enquanto o BMAD orquestra a divisão de papéis, o Spec-Driven Development (SDD) foca em tratar a especificação (o documento de texto) como o verdadeiro código-fonte do projeto, relegando a implementação (arquivos Rust e TypeScript) a meros artefatos compilados pela IA. Esta metodologia assevera que modelos de linguagem performam substancialmente melhor quando executam tarefas estruturadas baseadas em diretrizes imutáveis, em vez de responderem a prompts abertos.

O framework SDD Pilot, uma evolução altamente refinada e adaptada do GitHub Spec Kit, possui suporte nativo para o Antigravity IDE. Diferente de versões anteriores que dependiam de scripts Bash ou PowerShell, o SDD Pilot utiliza agentes e habilidades nativas de inteligência artificial para delegar tarefas e preservar um contexto enxuto.

**Lidando com o Fluxo SDD (Specify -> Plan -> Tasks -> Implement):** Para acoplar o fluxo SDD no Antigravity, o repositório do projeto deve incorporar os pacotes de lançamento do SDD Pilot. Uma vez integrado, a janela de conversa do Agent Manager aceitará comandos estruturados (`/sddp.*`) que forçam o sistema a construir e consumir as especificações através de "Quality Gates" rigorosos.

A execução ocorre através do seguinte pipeline estrito:

1. **Estabelecimento da Fundação (`/sddp.constitution`):** Este comando aciona a criação ou atualização de um documento governamental, a "Constituição". Este arquivo define os princípios arquiteturais imutáveis do Sovereign OS, tais como padrões de segurança, exigências de manipulação direta de memória no Rust, e obrigações de RAG estritamente local. Todo o código futuro será auditado contra este documento.
2. **A Especificação (`/sddp.specify`):** O agente foca no problema de domínio, definindo _o que_ deve ser construído e por que, sem menção a tecnologias. A IA conduz uma pesquisa online ou no Knowledge Graph interno para embasar o documento de requisitos, identificando casos de uso e cenários de falha.
3. **A Arquitetura (`/sddp.plan`):** Apenas após a validação do passo anterior, o agente mapeia a especificação abstrata para a pilha tecnológica real (Tauri, SQLite, WebAssembly). O plano de implementação arquitetural detalhado é gravado nos artefatos do repositório.
4. **A Divisão Atômica (`/sddp.tasks`):** A estratégia macro é convertida em um grafo direcionado de tarefas menores e acionáveis, estabelecendo as dependências lógicas para evitar bloqueios de compilação durante a implementação do código Rust.
5. **A Execução Blindada (`/sddp.implement`):** O comando final engatilha o código. A habilidade de implementação (o `SKILL.md` associado a este comando) é programada com regras estritas contra alucinação, exigindo que o agente recarregue a "Constituição" e valide cada linha de código modificada contra a arquitetura desenhada, paralisando a operação e solicitando intervenção humana caso o impacto transcenda o raio de ação das tarefas isoladas.

A sinergia entre o papel do Arquiteto do BMAD e os portões de qualidade do SDD Pilot transforma o Antigravity de uma ferramenta reativa para uma linha de montagem de software determinística.

## 3. Ecossistema de Addons e Servidores MCP Avançados

O potencial do Antigravity IDE é exponencialmente ampliado pelo Model Context Protocol (MCP), um barramento de integração que permite aos agentes inspecionarem, analisarem e interagirem com infraestruturas externas de maneira controlada e segura. Para o Genesis MC, que preza pela execução em hardware local (Edge Computing) e persistência eficiente, a seleção e configuração cirúrgica destes servidores é fundamental para o sucesso operacional.

### 3.1. Gerenciamento de Processos Background no Antigravity

A conexão de servidores MCP no Antigravity ocorre de forma visual através do painel MCP Store ou mediante edição direta do manifesto de integração. Para adicionar configurações customizadas, o usuário deve acessar o painel "Manage MCP Servers" no dropdown de opções do agente e editar o arquivo `mcp_config.json`.

Entretanto, é vital estar ciente da patologia de arquitetura inerente ao design atual do IDE: o "Process Explosion" (Bug 129054). O Antigravity invoca instâncias isoladas de cada servidor MCP para cada janela de workspace aberta. Um ambiente com quatro workspaces e múltiplos servidores baseados em Node.js (via `npx`) pode acumular rapidamente dezenas de processos zumbis que drenam silenciosamente Gigabytes de RAM, asfixiando os limitados recursos disponíveis para o LLM rodar localmente. Para mitigar este gargalo térmico e de memória em hardware restrito (como uma GPU com 6GB de VRAM), é absolutamente essencial migrar servidores baseados em Node.js/TypeScript para binários puros e estáticos compilados em Rust ou Go sempre que possível, ou convertê-los em execuções de linha de comando descartáveis utilizando wrappers como o `mcp2cli`.

### 3.2. Configuração Prática de Servidores Essenciais

A orquestração do cérebro do Genesis MC requer ferramentas com finalidades específicas de análise lógica, retenção de memória e segurança de renderização.

A tabela a seguir consolida as integrações de alto valor, o problema que resolvem, e o formato de acoplamento JSON exigido:

|**Ferramenta / MCP**|**Função Principal e Valor para a Arquitetura**|**Exemplo de Configuração mcp_config.json (Snippet)**|
|---|---|---|
|**Sequential Thinking**|Força a IA a aplicar um pipeline estruturado de raciocínio passo a passo (`Problem Definition -> Research -> Analysis -> Synthesis -> Conclusion`). Vital para resolver lógicas profundas de Rust sem saturar a VRAM com LLMs massivos, impedindo saltos precipitados para a conclusão ("vibe coding").|`{"command": "uv", "args": ["run", "--directory", "/path/to/mcp-sequential-thinking", "-m", "mcp_sequential_thinking.server"]}`|
|**Memory Bank / Knowledge Graph**|Elimina a "amnésia" sistêmica inter-sessões ("context rot"). Constrói um grafo semântico do projeto ou armazena os fatos e restrições numa base SQLite local. Permite a recuperação contextual estruturada sem injetar dezenas de arquivos de texto no prompt inicial.|`{"command": "npx", "args": ["-y", "@itseasy21/mcp-knowledge-graph"], "env": {"MEMORY_FILE_PATH": "/path/to/soda_memory.jsonl"}}`|
|**Toon Context**|Servidor utilitário que intercepta arquivos estruturados (JSON) solicitados pela IA, minificando-os no formato _Token-Optimized Object Notation_ (TOON). Esta compressão remove chaves repetitivas em tabelas e logs de banco de dados, poupando de 30% a 60% de tokens da janela de contexto de forma totalmente transparente para o agente.|`{"command": "node", "args": ["/path/to/toon-context-mcp/build/index.js"], "env": {"TOON_THRESHOLD": "0.7", "AUTO_CONVERT": "true"}}`|

**A2UI Protocol (Integração Arquitetural Nativa):** Embora não seja um servidor MCP utilitário de fundo tradicional, a compreensão e aplicação do padrão Agent-to-User Interface (A2UI) são a pedra angular da construção do frontend do Genesis OS. O protocolo A2UI, impulsionado pelo Google e CopilotKit, revolve a vulnerabilidade severa presente na geração dinâmica de interfaces por IA.

Em sistemas web tradicionais, agentes que alteram o front-end injetam HTML e JavaScript diretamente no Document Object Model (DOM), o que equivale à Execução Remota de Código (RCE) e convida ataques de Cross-Site Scripting (XSS). O A2UI elimina esse vetor de ataque sendo rigorosamente "Secure-by-design" e "LLM-Friendly". Em vez de enviar código imperativo, o cérebro em Rust (O Maestro) processa a intenção e envia mensagens JSON puras via fluxo de eventos Server-Sent Events (SSE) para a janela web do Tauri. O conteúdo flui formatado num modelo de "Lista de Adjacência" (`Adjacency List Model`), enviando definições de estado separadas da árvore visual (mensagens `createSurface` ou `updateComponents` da especificação v0.9). A camada do cliente (um frontend estrito em React) recebe esse JSON inofensivo e o mapeia para o seu próprio catálogo de componentes pré-aprovados e blindados (como os providos pela biblioteca Shadcn UI), garantindo renderização nativa de alta performance e mitigando qualquer risco de injeção direta de código executável no ambiente host.

### 3.3. O Ouro Oculto: Ferramentas Recentes e Obscuras

Além do circuito tradicional, a pesquisa indicou componentes cruciais para desenvolvedores orquestrando ecossistemas Rust/SQLite com recursos limitados, categorizando um arcabouço tecnológico avançado:

1. **`llm-proxy`:** Um gateway de roteamento e firewall blindado construído em Rust nativo. Quando o Antigravity ou as instâncias em background precisarem lidar com as chaves de API cruas, o llm-proxy atua interceptando as requisições. O diferencial fundamental é que a interface do cliente e os agentes da interface jamais têm acesso às credenciais secretas, prevenindo o vazamento acidental em logs de chat, resolvendo um vetor crítico de exfiltração de dados.
2. **`cplusplus-mcp` (e adaptadores AST para Rust):** Servidores de análise semântica que transcendem a busca de texto (`grep`) por meio da análise de Abstract Syntax Trees (AST) de código C/C++ (sendo adaptável via linguagens de parsing nativo como o Tree-sitter para Rust). Isso permite à IA navegar em heranças complexas, implementações de `traits` e grafos de chamadas sem a necessidade de ler gigabytes de código irrelevante.
3. **`ZeroClaw` / Arquiteturas Wasmtime:** Uma abordagem radical contra a obesidade de frameworks em Node.js ou Python. O projeto provê a fundação para encapsular ferramentas (as "Mãos" do agente) em binários compilados sob execução de WebAssembly (Wasm). No contexto SODA, a integração desse modelo garante que as automações locais rodem blindadas, desprovidas de permissões para manipular o disco hospedeiro até que uma aprovação humana seja explícita, mantendo latências atômicas ("Cold start" em milissegundos).
4. **`TensorZero`:** Um ecossistema purista em Rust focado em engenharia rigorosa de MLOps. Essencial para rastrear a eficiência dos LLMs, gerenciando a latência de inferência na origem. Utilizado como camada para monitorar as taxas de sucesso e falhas do agente orquestrador sem recorrer a soluções pesadas na nuvem.
5. **GraphBit:** Para a execução previsível e performática de tarefas de fundo, este framework introduz o conceito de escalonamento Lock-Free utilizando a matemática de grafos acíclicos dirigidos (DAGs). O núcleo em Rust processa as ramificações de decisão do orquestrador de forma atômica e previsível, isento do gargalo do Global Interpreter Lock (GIL) encontrado nas abordagens em Python.

## 4. O 'Caminho Feliz' da Documentação (Single Source of Truth)

O fracasso crônico de agentes de inteligência artificial em projetos massivos deve-se invariavelmente à ausência de uma Fonte Única da Verdade (Single Source of Truth - SSOT) legível por máquina. Uma documentação exaustiva e bem estruturada neutraliza alucinações cognitivas.

### 4.1. Requisitos Não-Funcionais (NFRs) Direcionados a IA

Enquanto NFRs clássicos focam na percepção do usuário (escalabilidade em nuvem ou transações por segundo), os NFRs direcionados para agentes que operam localmente (Edge Computing) ditam fronteiras restritivas de hardware físico, determinando os trilhos de segurança ("Guardrails") nos quais o LLM deve operar. O agente não deve gerar soluções que extrapolem os limites matemáticos da máquina hospedeira.

Aspectos cruciais na redação destas restrições:

- **Gestão de VRAM e Cold Start:** Agentes não operam em fluxo constante como APIs na nuvem, mas sim em tráfegos de rajada ("Bursty traffic"). Os modelos locais precisam já estar residentes na VRAM para evitar atrasos de carregamento de discos, e o limite de memória (ex: orçamentos de parâmetros restritos a modelos de ~7B como o Qwen) dita a complexidade do modelo selecionável.
- **Latência de Inferência (TTFT):** O _Time to First Token_ (Tempo até o primeiro token) é o gargalo primário na responsividade. Agentes envolvidos em diálogos e validação no Genesis MC precisam sustentar TTFTs próximos a 400-500 milissegundos. Instruções longas e massivas (que sobrecarregam a fase de pré-processamento, a chamada _Prefill Phase_) corrompem esta métrica de latência severamente, o que reforça o uso obrigatório do Toon Context e de resumos constantes.
- **Telemetria e "Runaway Detection":** Diferentemente do software determinístico, agentes operam em loops lógicos (ex: ReAct). As diretrizes devem impor ferramentas que monitorem contagens de passos interativos para evitar o dispêndio financeiro ou térmico resultante de agentes caçando soluções num loop infinito por falha de formatação JSON.

### 4.2. ADRs (Architecture Decision Records) e Automação

Um Architecture Decision Record (ADR) é um diário compacto que captura uma decisão arquitetural imutável e significativa para o sistema, detalhando seu contexto primário, os debates inerentes e suas consequências. Num ecossistema assistido por inteligência artificial, o ADR tem o poder mágico de educar novos agentes sobre as "cicatrizes de guerra" do projeto, respondendo o _porquê_ de bibliotecas específicas terem sido escolhidas ou rejeitadas.

A confecção de ADRs requer disciplina rigorosa, que é facilmente esquecida no calor do desenvolvimento. Portanto, ferramentas dentro do ecossistema BMAD e Antigravity fornecem pontes de automação para rastrear o código e conceber estes registros. O próprio agente Arquiteto ("Architect") do fluxo de trabalho possui comandos instrucionais desenhados para inspecionar repositórios em busca de decisões tácitas, formatando as descobertas através de templates predefinidos, criando um diário persistente dentro de `docs/adr/` ou alimentando a ontologia do Knowledge Graph.

### 4.3. Templates Markdown Estruturados

O provimento de regras explícitas requer formatação limpa e determinística para as ferramentas analíticas baseadas em vetores e grafos.

**Template: Requisitos Não-Funcionais (NFR) de Orquestração**

# NFR-CORE: Limites Operacionais do Daemon Genesis OS (SODA)

## 1. Parâmetros Físicos e Limiares de Inferência

- **Orçamento de VRAM (Capacidade Cognitiva):** O modelo local principal de inferência dedutiva permanecerá engessado num tamanho de até 8B parâmetros quantizados (ex: Qwen 3.5), alocado em um teto máximo e estrito de `5.0 GB VRAM` para garantir estabilidade contínua sem paginação no disco.
- **Responsividade (TTFT):** O _Time-to-First-Token_ para processamento de intenções ("Intent Routing") utilizando modelos efêmeros (Gateway) jamais excederá o SLA estrito de `300ms`.

## 2. Orquestração e Gestão de Orçamento de Tokens

- **Backpressure Atômico:** Agentes autônomos em tarefas secundárias possuem um limite rígido de 5 passos lógicos por iteração antes da intervenção obrigatória para validação ("Runaway Detection").
- **Tolerância de Janela de Contexto:** Dados de RAG (Retrieval-Augmented Generation) extraídos do SQLite nunca serão injetados crus no buffer de atenção, passando mandatoriamente por compressão JSON proprietária (Toon Context), economizando teto cognitivo para a fase de raciocínio.

## 3. Segurança Atômica e Sandboxing (Zero Trust)

- **Barreiras de Execução:** Nenhuma automação produzida dinamicamente terá acesso às APIs nativas de File System (`std::fs`) do Rust. Toda operação transitória de ferramentas ocorrerá sob WebAssembly Runtime isolado (Wasmtime).

**Template: Architecture Decision Record (ADR)**

# ADR-042: Separação de Interface e Inteligência via A2UI Protocol

## Contexto e Desafio

O ecossistema Genesis MC é orquestrado sob Tauri, onde a segurança entre o backend purista em Rust e o frontend webview em React é crítica. Agentes codificadores que injetam estados interativos diretamente no React são vetores gravíssimos de vulnerabilidade de injeção de código executável (RCE/XSS), ameaçando a premissa de um SO Seguro (Sovereign OS).

## Decisão Arquitetural

Adotar a Especificação A2UI (Agent-to-User Interface) em conformidade com o rascunho v0.9. O agente orquestrador processará as intenções através do raciocínio analítico (Backend) e cuspirá exclusivamente fluxos estruturados JSON na sintaxe `createSurface` ou `updateComponents`.

## Status

APROVADO e Aplicado à Base Principal.

## Consequências e Compromissos

- **Mitigação de Ameaça:** Neutralização profunda de ataques vetoriais baseados em payload de frontend mal-formatado.
- **Eficiência de Rede/Memória:** Redução do consumo de recursos através do descarte de estado efêmero; o cliente React opera como um Renderizador Passivo "Burro" manipulando o modelo de dados Adjacente (Adjacency List).
- **Sobrecarga de Implementação:** Exigirá a confecção e registro metódico de uma "Widget Registry" estrita no Frontend React (baseado no Radix/Shadcn) para acoplar os elementos virtuais ditados pelas mensagens oriundas das _syscalls_ IPC (Tauri).

Ao codificar o SODA de acordo com esses paradigmas estritos no Antigravity IDE, o processo se liberta das limitações do desenvolvimento passivo de IA. Através da implementação de metodologias rigorosas (BMAD/SDD) e da introdução de ecossistemas operacionais avançados em Rust, o desenvolvedor transcende o papel de programador assistido, assumindo o controle total e determinístico sobre o tecido fundacional do Sovereign OS.