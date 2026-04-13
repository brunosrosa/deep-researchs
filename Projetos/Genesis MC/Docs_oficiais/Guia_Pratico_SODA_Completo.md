---
sticker: lucide//clover
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

A ausência de disciplina metodológica na utilização de agentes codificadores culmina na criação de bases de código fragmentadas, onde os padrões arquiteturais desaparecem entre iterações e a dívida técnica se acumula rapidamente. Para o **Genesis MC**, um sistema focado em privacidade e operação local restrita, cada linha de **Rust** e cada consulta **SQLite** devem obedecer a um _plano mestre_. Para alcançar este nível de rigor, o ambiente deve integrar simultaneamente a orquestração de papéis do **BMAD** e o rigor documental do **SDD**.

### 2.1. O Ecossistema BMAD (Build More Architect Dreams) v6

A metodologia BMAD v6 transforma a interface assíncrona do Antigravity em um estúdio de desenvolvimento corporativo completo, simulando as interações de uma equipe multidisciplinar através de inteligência adaptativa ("Scale-Adaptive Intelligence"). O sistema abandona a premissa de um único assistente genérico e introduz agentes especializados com papéis estritos: Analista, Product Manager (PM), Arquiteto, Scrum Master (SM), Desenvolvedor e QA.

**Instalação e Configuração no Antigravity:** O BMAD v6 foi arquitetado para ser nativo aos ambientes agênticos. A configuração exige a transferência da biblioteca de regras do BMAD para os diretórios estruturais do Antigravity. As definições YAML obsoletas de versões anteriores foram convertidas em habilidades unificadas baseadas em Markdown (arquivos `SKILL.md` como pontos de entrada) dentro do diretório `.agents/skills/` ou `.agents/workflows/`, permitindo que os comandos de gatilho sejam ativados diretamente no chat.

**O Fluxo de Trabalho Exato (Workflow):**

O ciclo de vida da metodologia obriga que as especificações técnicas e as decisões de segurança sejam finalizadas antes da geração de código. O fluxo a seguir ilustra a operação real no chat do IDE para o desenvolvimento de um novo módulo no SODA:

A tabela abaixo sumariza o roteamento de comandos da metodologia BMAD v6 dentro do Antigravity :

|**Comando de Gatilho**|**Agente Invocado**|**Ação e Artefato Produzido**|
|---|---|---|
|`/bmad analyst workflow-status`|_Analista_|Avalia o estado atual do repositório, identifica gargalos e estrutura o "Project Brief" baseado nos requisitos iniciais do usuário.|
|`/pm plan-project`|_Product Manager_|Traduz a visão do Analista em Requisitos Funcionais tangíveis, particionando o escopo em Épicos e Histórias de Usuário (User Stories) focadas.|
|`/architect solution-architecture`|_Arquiteto_|Desenha o projeto técnico "Full-Stack". Gera diagramas de componentes, delineia os limites de confiança (Trust Boundaries) essenciais para a segurança do Tauri, e define modelos de dados para o SQLite.|
|`/bmad sm create-story`|_Scrum Master_|Isola o contexto. Extrai uma história de usuário específica da fila e prepara a base documental restrita para a intervenção do desenvolvedor, gerando o contexto atômico.|
|`/bmad dev dev-story`|_Desenvolvedor_|Inicia a escrita real do código em Rust ou a estruturação da UI em React, limitando-se rigorosamente à arquitetura imposta pelo agente Arquiteto.|

O uso sequencial destes comandos bloqueia a inclinação natural do LLM para a improvisação, assegurando que o código compilado reflita um design sistêmico validado e resiliente.

### 2.2. Spec-Driven Development (SDD) no Ambiente Agêntico

Enquanto o **BMAD** orquestra a divisão de papéis, o _Spec-Driven Development_ (_SDD_) foca em tratar a especificação (o documento de texto) como o verdadeiro código-fonte do projeto, relegando a implementação (arquivos Rust e TypeScript) a meros artefatos compilados pela IA. Esta metodologia assevera que modelos de linguagem performam substancialmente melhor quando executam tarefas estruturadas baseadas em diretrizes imutáveis, em vez de responderem a prompts abertos.

O *framework **SDD Pilot***, uma evolução altamente refinada e adaptada do **_GitHub Spec Kit_**, possui suporte **nativo** para o **Antigravity IDE**. Diferente de versões anteriores que dependiam de scripts Bash ou PowerShell, o SDD Pilot utiliza agentes e habilidades nativas de inteligência artificial para delegar tarefas e preservar um contexto enxuto.

**Lidando com o Fluxo SDD (Specify -> Plan -> Tasks -> Implement):** Para acoplar o fluxo SDD no Antigravity, o repositório do projeto deve incorporar os pacotes de lançamento do SDD Pilot. Uma vez integrado, a janela de conversa do Agent Manager aceitará comandos estruturados (`/sddp.*`) que forçam o sistema a construir e consumir as especificações através de "Quality Gates" rigorosos.

A execução ocorre através do seguinte pipeline estrito:

1. **Estabelecimento da Fundação (`/sddp.constitution`):** Este comando aciona a criação ou atualização de um documento governamental, a "Constituição". Este arquivo define os princípios arquiteturais imutáveis do Sovereign OS, tais como padrões de segurança, exigências de manipulação direta de memória no Rust, e obrigações de RAG estritamente local. Todo o código futuro será auditado contra este documento.
2. **A Especificação (`/sddp.specify`):** O agente foca no problema de domínio, definindo _o que_ deve ser construído e por que, sem menção a tecnologias. A IA conduz uma pesquisa online ou no Knowledge Graph interno para embasar o documento de requisitos, identificando casos de uso e cenários de falha.
3. **A Arquitetura (`/sddp.plan`):** Apenas após a validação do passo anterior, o agente mapeia a especificação abstrata para a pilha tecnológica real (Tauri, SQLite, WebAssembly). O plano de implementação arquitetural detalhado é gravado nos artefatos do repositório.
4. **A Divisão Atômica (`/sddp.tasks`):** A estratégia macro é convertida em um grafo direcionado de tarefas menores e acionáveis, estabelecendo as dependências lógicas para evitar bloqueios de compilação durante a implementação do código Rust.
5. **A Execução Blindada (`/sddp.implement`):** O comando final engatilha o código. A habilidade de implementação (o `SKILL.md` associado a este comando) é programada com regras estritas contra alucinação, exigindo que o agente recarregue a "Constituição" e valide cada linha de código modificada contra a arquitetura desenhada, paralisando a operação e solicitando intervenção humana caso o impacto transcenda o raio de ação das tarefas isoladas.

A sinergia entre o papel do Arquiteto do **BMAD** e os portões de qualidade do **SDD Pilot** transforma o Antigravity de uma ferramenta reativa para uma linha de montagem de software determinística.

## 3. Ecossistema de Addons e Servidores MCP Avançados

O potencial do Antigravity IDE é exponencialmente ampliado pelo Model Context Protocol (MCP), um barramento de integração que permite aos agentes inspecionarem, analisarem e interagirem com infraestruturas externas de maneira controlada e segura. Para o Genesis MC, que preza pela execução em hardware local (_Edge Computing_) e persistência eficiente, a seleção e configuração cirúrgica destes servidores é fundamental para o sucesso operacional.

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

Em sistemas web tradicionais, agentes que alteram o front-end injetam HTML e JavaScript diretamente no Document Object Model (DOM), o que equivale à Execução Remota de Código (RCE) e convida ataques de Cross-Site Scripting (XSS). O A2UI elimina esse vetor de ataque sendo rigorosamente "_Secure-by-design_" e "_LLM-Friendly_". Em vez de enviar código imperativo, o cérebro em Rust (O Maestro) processa a intenção e envia mensagens JSON puras via fluxo de eventos Server-Sent Events (SSE) para a janela web do Tauri. O conteúdo flui formatado num modelo de "Lista de Adjacência" (`Adjacency List Model`), enviando definições de estado separadas da árvore visual (mensagens `createSurface` ou `updateComponents` da especificação v0.9). A camada do cliente (um frontend estrito em React) recebe esse JSON inofensivo e o mapeia para o seu próprio catálogo de componentes pré-aprovados e blindados (como os providos pela biblioteca Shadcn UI), garantindo renderização nativa de alta performance e mitigando qualquer risco de injeção direta de código executável no ambiente host.

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

-----

# Genesis MC: Guia Prático - Parte 2 (O Motor SODA)

Este documento detalha a implementação do backend do **Genesis MC (SODA)** usando **Rust, Tauri e SQLite**. O objetivo deste motor é ser brutalmente eficiente, consumindo o mínimo de RAM e CPU, deixando a VRAM livre para os seus SLMs locais (Phi-4-mini, Qwen, etc).

## 1. O Loop Principal e IPC (Inter-Process Communication)

No Tauri, o Frontend (React) e o Backend (Rust) rodam em processos separados. Para que os agentes "pensem" sem congelar a sua UI (Canvas), você **nunca** deve rodar inferência de IA ou queries pesadas na thread principal do Tauri.

**O Padrão Arquitetural:** `MPSC Channels` (Multi-Producer, Single-Consumer) do `tokio` combinados com `Tauri Events`.

### Estrutura do `src-tauri/src/main.rs`

```rust
// Importações essenciais
use tauri::{Manager, State, Emitter};
use tokio::sync::mpsc;
use serde::{Serialize, Deserialize};

// 1. Definição do Payload de Comunicação
#[derive(Clone, Serialize)]
struct AgentThought {
    agent_id: String,
    content: String,
    is_final: bool,
}

// 2. O Estado Global da Aplicação (Injetado via Tauri State)
struct AppState {
    // Canal para enviar comandos do Frontend para o Loop de IA no Rust
    tx_ai_commands: mpsc::Sender<String>, 
}

// 3. O Comando Tauri (Chamado pelo React)
#[tauri::command]
async fn ask_agent(prompt: String, state: State<'_, AppState>) -> Result<(), String> {
    // Apenas envia a mensagem para o canal, NÃO bloqueia a UI aguardando a IA
    state.tx_ai_commands.send(prompt).await.map_err(|e| e.to_string())?;
    Ok(())
}

fn main() {
    // Criação dos canais assíncronos
    let (tx, mut rx) = mpsc::channel::<String>(32);

    tauri::Builder::default()
        .manage(AppState { tx_ai_commands: tx })
        .setup(|app| {
            let app_handle = app.handle().clone();
            
            // 4. O "Motor SODA" rodando em background
            tauri::async_runtime::spawn(async move {
                while let Some(prompt) = rx.recv().await {
                    println!("Agente recebeu: {}", prompt);
                    
                    // AQUI entra a chamada para o Llama.cpp ou MCP
                    // Simulando o raciocínio em streaming do agente
                    for i in 1..=5 {
                        tokio::time::sleep(std::time::Duration::from_millis(500)).await;
                        
                        // Emitindo eventos em tempo real para o Frontend (React atualizar o Canvas)
                        app_handle.emit("agent-thought", AgentThought {
                            agent_id: "phi-4-mini".to_string(),
                            content: format!("Processando token {}...", i),
                            is_final: i == 5,
                        }).unwrap();
                    }
                }
            });

            Ok(())
        })
        .invoke_handler(tauri::generate_handler![ask_agent])
        .run(tauri::generate_context!())
        .expect("Erro ao rodar Genesis MC");
}
```

**Por que isso importa?** Este padrão garante que o React dispare a pergunta e fique livre para animar a UI. As respostas chegam via _event listeners_ no Frontend (veremos isso na Parte 3).

## 2. Memory Bank (SQLite) - O Hippocampus do Life Coach

Para que o sistema seja verdadeiramente "Sovereign" e aprenda com você (o conceito de _Progressive Disclosure_ e _Co-evolução_), ele precisa de uma memória de longo prazo. Não use Vector Databases gigantescos agora. Use **SQLite + FTS5** (Full Text Search) para velocidade extrema.

Crie uma pasta `src-tauri/db` e utilize a crate `sqlx` (fortemente recomendada para Rust assíncrono).

### O Schema SQL (Estrutura do Grafo de Conhecimento Relacional)

```sql
-- Arquivo: src-tauri/db/schema.sql

-- 1. EPISÓDIOS: A Memória Episódica (O que aconteceu e quando)
CREATE TABLE IF NOT EXISTS episodes (
    id TEXT PRIMARY KEY,           -- UUID
    timestamp DATETIME DEFAULT CURRENT_TIMESTAMP,
    agent_id TEXT NOT NULL,        -- Qual agente estava ativo
    user_prompt TEXT NOT NULL,
    agent_response TEXT NOT NULL,
    summary TEXT,                  -- Resumo gerado por um modelo menor em background
    embedding BLOB                 -- (Futuro) Para busca vetorial local com sqlite-vec
);

-- 2. ENTIDADES: A Memória Semântica (Quem/O que o agente conhece)
CREATE TABLE IF NOT EXISTS entities (
    id TEXT PRIMARY KEY,           -- UUID
    name TEXT NOT NULL UNIQUE,     -- Ex: "Bruno", "RTX 2060m", "Projeto SODA"
    type TEXT NOT NULL,            -- Ex: "Pessoa", "Hardware", "Conceito"
    attributes TEXT,               -- JSON contendo detalhes (Ex: {"VRAM": "6GB", "Arquitetura": "Turing"})
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    last_updated DATETIME DEFAULT CURRENT_TIMESTAMP
);

-- 3. RELACIONAMENTOS: O Grafo de Conhecimento Local
CREATE TABLE IF NOT EXISTS entity_relations (
    source_id TEXT NOT NULL,
    target_id TEXT NOT NULL,
    relation_type TEXT NOT NULL,   -- Ex: "POSSUI", "TRABALHA_EM", "LIMITA"
    weight INTEGER DEFAULT 1,      -- Força da relação
    FOREIGN KEY(source_id) REFERENCES entities(id),
    FOREIGN KEY(target_id) REFERENCES entities(id),
    PRIMARY KEY (source_id, target_id, relation_type)
);

-- Índice para busca rápida de texto completo
CREATE VIRTUAL TABLE IF NOT EXISTS episodes_fts USING fts5(
    summary, user_prompt, agent_response, content='episodes', content_rowid='id'
);
```

**Integração Prática com as NFRs (Non-Functional Requirements):**

A tabela de `entities` é onde o agente armazena suas restrições. Quando você disser _"Crie um modelo 3D"_, o Agente busca `RTX 2060m` nas `entities`, lê o JSON `{"VRAM": "6GB"}`, e o **Toon Context** comprime isso e envia para o Phi-4-mini: _"Aviso do Sistema: Usuário possui apenas 6GB de VRAM. Não proponha soluções pesadas."_

## 3. Spawning MCP Servers (Model Context Protocol) no Rust

Como mapeado na sua planilha, usaremos MCPs vitais como o **Time MCP** (para dar noção cronológica) e o **Sequential Thinking MCP**. O Rust atuará como o cliente (Host) que inicia (spawna) esses servidores em _background_ como processos filhos (`child processes`) e se comunica com eles via `stdio` (Standard Input/Output).

### Lógica de Gerenciamento de Subprocessos MCP (`src-tauri/src/mcp_host.rs`)

```rust
use tokio::process::{Command, Child};
use std::process::Stdio;
use tokio::io::{AsyncWriteExt, AsyncBufReadExt, BufReader};

pub struct McpServer {
    process: Child,
    name: String,
}

impl McpServer {
    // Função para iniciar um MCP (Ex: o Time MCP em Node.js ou um binário Go compilado)
    pub async fn start(name: &str, command: &str, args: &[&str]) -> Result<Self, String> {
        let mut child = Command::new(command)
            .args(args)
            .stdin(Stdio::piped())
            .stdout(Stdio::piped())
            .stderr(Stdio::piped()) // Capturar erros para não poluir o terminal principal
            .spawn()
            .map_err(|e| format!("Falha ao iniciar MCP {}: {}", name, e))?;

        println!("🟢 MCP Server '{}' iniciado com sucesso.", name);

        // Opcional: Monitorar o stderr em background para debugging
        if let Some(stderr) = child.stderr.take() {
            tokio::spawn(async move {
                let mut reader = BufReader::new(stderr);
                let mut line = String::new();
                while let Ok(bytes) = reader.read_line(&mut line).await {
                    if bytes == 0 { break; }
                    eprintln!("[MCP {} Erro]: {}", name, line.trim());
                    line.clear();
                }
            });
        }

        Ok(McpServer {
            process: child,
            name: name.to_string(),
        })
    }

    // Exemplo de como enviar um request JSON-RPC para o MCP
    pub async fn send_request(&mut self, payload: &str) -> Result<(), String> {
        if let Some(stdin) = self.process.stdin.as_mut() {
            let message = format!("{}\n", payload);
            stdin.write_all(message.as_bytes()).await.map_err(|e| e.to_string())?;
            Ok(())
        } else {
            Err("Stdin não disponível para o MCP".to_string())
        }
    }
}
```

### Acoplando no Motor SODA (Main.rs)

No seu `setup` do Tauri, você irá inicializar seus MCPs essenciais:

```rust
// Dentro do tauri::Builder::default().setup(|app| { ... })
tauri::async_runtime::spawn(async move {
    // 1. Iniciar o Time MCP (Garante que o Life Coach saiba o horário exato)
    // Supondo que o Time MCP foi empacotado via npx ou é um script local
    let mut time_mcp = McpServer::start(
        "Time_MCP", 
        "node", 
        &["../.agents/mcps/time-mcp/index.js"]
    ).await.expect("Falha no Time MCP");

    // 2. Iniciar Jcodemunch (Leitor de código ultra-rápido em Go)
    let mut code_mcp = McpServer::start(
        "JcodeMunch", 
        "../bin/jcodemunch", 
        &[]
    ).await.expect("Falha no Jcodemunch");

    // Mantenha as referências vivas ou coloque em um Arc<Mutex<>> no AppState
});
```

## 4. O Fluxo de Trabalho (O que o Motor faz quando você digita)

Quando você digita _"O que eu fiz no projeto SODA ontem?"_ no Canvas:

1. **Frontend:** Chama `ask_agent`.
2. **Event Loop (Rust):** Recebe o prompt.
3. **Time MCP (Rust chama o Node):** Pede a data de hoje. (O Rust injeta silenciosamente: _"Hoje é 14/08/2026"_).
4. **SQLite (Hippocampus):** O Rust faz um query no FTS5 buscando _"projeto SODA"_. Encontra os `episodes` de ontem.
5. **Toon Context (Compressão):** O Rust pega os 10 episódios encontrados, minifica o JSON, remove stop-words e compacta (Poupando VRAM).
6. **Llama.cpp (LLM Local):** O Rust envia o prompt comprimido + contexto para o modelo carregado na VRAM.
7. **Tauri Emitter:** Conforme o modelo cospe os tokens, o Rust dispara `app_handle.emit("agent-thought", ...)`.
8. **Frontend:** Renderiza o texto fluindo no seu Canvas React.

Este é o Motor V8 do seu ambiente agêntico. Zero APIs de nuvem. Totalmente em sua posse.

-----

# Genesis MC: Guia Prático - Parte 3 (O "Palco" Multimodal)

O chat linear morreu. O **SODA (Sovereign OS)** utiliza um Canvas Espacial para interação. Isso reduz drasticamente a carga cognitiva, permitindo que você (o humano) veja a árvore de decisão do agente e expanda apenas as partes que importam.

Este guia foca na implementação com **Xyflow (React Flow)**, a escolha arquitetural ideal para mapear o _Sequential Thinking MCP_ e o raciocínio passo-a-passo dos seus SLMs locais.

## 1. Setup do Ecossistema React + Xyflow no Tauri

Assumindo que o seu frontend (dentro do Tauri) está usando Vite + React + TypeScript.

**Instalação das dependências essenciais:**

```
# Dentro da pasta do seu frontend (ex: src/)
npm install @xyflow/react @tauri-apps/api lucide-react clsx tailwind-merge
```

**Por que Xyflow e não Tldraw aqui?**

Ambos têm nota 10 na sua planilha, mas servem a propósitos distintos no SODA:

- **Xyflow:** Para a Mente do Orquestrador (visualizar nós de raciocínio, chamadas de ferramentas MCP, fluxos de dados). **Usaremos este agora.**
- **Tldraw:** Para o "Quadro Branco" (desenho livre, vibe coding, sketch-to-code). Ficará como uma aba ou modo alternativo.

## 2. A Ponte: Escutando o Motor Rust (`useAgentState.ts`)

Na Parte 2, fizemos o Rust emitir eventos `agent-thought`. Agora precisamos de um Hook React que escute esses eventos e os transforme em Nós (Nodes) e Arestas (Edges) no Canvas em tempo real.

```ts
// src/hooks/useAgentState.ts
import { useState, useEffect, useCallback } from 'react';
import { listen } from '@tauri-apps/api/event';
import { Node, Edge, addEdge, applyNodeChanges, applyEdgeChanges, NodeChange, EdgeChange } from '@xyflow/react';

export type AgentThoughtPayload = {
  id: string;
  agent_id: string;
  content: string;
  is_final: bool;
  tool_called?: string; // Ex: "Time_MCP"
  parent_id?: string;   // Para ligar o nó atual ao anterior
};

export function useAgentState() {
  const [nodes, setNodes] = useState<Node[]>([]);
  const [edges, setEdges] = useState<Edge[]>([]);

  const onNodesChange = useCallback(
    (changes: NodeChange[]) => setNodes((nds) => applyNodeChanges(changes, nds)),
    []
  );
  
  const onEdgesChange = useCallback(
    (changes: EdgeChange[]) => setEdges((eds) => applyEdgeChanges(changes, eds)),
    []
  );

  useEffect(() => {
    // Escuta o evento que vem do backend em Rust
    const unlisten = listen<AgentThoughtPayload>('agent-thought', (event) => {
      const payload = event.payload;

      setNodes((prevNodes) => {
        // Se o nó já existe (ex: streaming de tokens), atualiza o conteúdo
        const existingNodeIndex = prevNodes.findIndex(n => n.id === payload.id);
        
        if (existingNodeIndex !== -1) {
          const updatedNodes = [...prevNodes];
          updatedNodes[existingNodeIndex].data = {
            ...updatedNodes[existingNodeIndex].data,
            content: payload.content,
            isFinal: payload.is_final
          };
          return updatedNodes;
        }

        // Se é um pensamento novo, cria um novo nó (Progressive Disclosure Node)
        const newNode: Node = {
          id: payload.id,
          type: 'progressiveThought', // Nosso nó customizado
          position: { x: prevNodes.length * 50, y: prevNodes.length * 150 }, // Layout automático simples
          data: { 
            agentId: payload.agent_id, 
            content: payload.content, 
            toolCalled: payload.tool_called,
            isFinal: payload.is_final 
          },
        };

        // Cria a aresta (Edge) se houver um parent_id (pensamento sequencial)
        if (payload.parent_id) {
           setEdges((prevEdges) => addEdge({
               id: `e-${payload.parent_id}-${payload.id}`,
               source: payload.parent_id,
               target: payload.id,
               animated: !payload.is_final, // Anima a linha enquanto o agente "pensa"
           }, prevEdges));
        }

        return [...prevNodes, newNode];
      });
    });

    return () => {
      unlisten.then(f => f());
    };
  }, []);

  return { nodes, edges, onNodesChange, onEdgesChange, setNodes };
}
```

## 3. O Nó de Divulgação Progressiva (`ThoughtNode.tsx`)

Este é o componente visual mais importante do SODA. Para um perfil **2e (Dupla Excepcionalidade)**, ver um _wall of text_ com logs de sistema, raciocínio interno e chamadas de API drena a energia.

O `ThoughtNode` implementa o **Progressive Disclosure**:

1. **Estado Base:** Mostra apenas a intenção (ex: _"Consultando Memória..."_ ou _"Pensando..."_).
2. **Estado Expandido (On Click):** Revela o raciocínio bruto (ex: os parâmetros passados para o `Jcodemunch MCP`).

```tsx
// src/components/canvas/ThoughtNode.tsx
import { useState } from 'react';
import { Handle, Position } from '@xyflow/react';
import { BrainCircuit, Wrench, ChevronDown, ChevronUp } from 'lucide-react';

export function ThoughtNode({ data }: { data: any }) {
  const [isExpanded, setIsExpanded] = useState(false);

  // Define as cores baseadas no tipo de ação do agente
  const isTool = !!data.toolCalled;
  const borderColor = isTool ? 'border-orange-500' : 'border-blue-500';
  const bgColor = data.isFinal ? 'bg-zinc-900' : 'bg-zinc-800 animate-pulse';

  return (
    <div className={`w-80 rounded-xl border-2 ${borderColor} ${bgColor} text-gray-100 shadow-2xl font-sans overflow-hidden transition-all duration-300`}>
      {/* Ponto de conexão de entrada (Top) */}
      <Handle type="target" position={Position.Top} className="w-3 h-3 bg-gray-500" />

      {/* Header (Resumo Visual) */}
      <div 
        className="flex items-center justify-between p-3 cursor-pointer hover:bg-zinc-700/50"
        onClick={() => setIsExpanded(!isExpanded)}
      >
        <div className="flex items-center gap-2">
          {isTool ? <Wrench size={18} className="text-orange-400" /> : <BrainCircuit size={18} className="text-blue-400" />}
          <span className="font-bold text-sm tracking-wide">
            {isTool ? `MCP: ${data.toolCalled}` : data.agentId}
          </span>
        </div>
        <button className="text-gray-400 hover:text-white">
          {isExpanded ? <ChevronUp size={16} /> : <ChevronDown size={16} />}
        </button>
      </div>

      {/* Área de Divulgação Progressiva (Conteúdo Expandido) */}
      {isExpanded && (
        <div className="p-3 border-t border-zinc-700 bg-black/40 text-sm max-h-60 overflow-y-auto font-mono text-gray-300">
           {data.content}
        </div>
      )}

      {/* Estado "Fechado" mas mostrando progresso se não finalizou */}
      {!isExpanded && !data.isFinal && (
        <div className="p-3 border-t border-zinc-700 text-xs text-gray-400 italic">
          Processando tokens...
        </div>
      )}

      {/* Ponto de conexão de saída (Bottom) */}
      <Handle type="source" position={Position.Bottom} className="w-3 h-3 bg-gray-500" />
    </div>
  );
}
```

## 4. O Palco Principal (`SodaCanvas.tsx`)

A união do Hook de Estado do Tauri com o Visual do React Flow.

```tsx
// src/components/canvas/SodaCanvas.tsx
import { ReactFlow, Background, Controls, MiniMap } from '@xyflow/react';
import '@xyflow/react/dist/style.css';

import { useAgentState } from '../../hooks/useAgentState';
import { ThoughtNode } from './ThoughtNode';
import { CommandInput } from '../CommandInput'; // Seu input de texto/voz

// Registra os nós customizados do SODA
const nodeTypes = {
  progressiveThought: ThoughtNode,
};

export function SodaCanvas() {
  const { nodes, edges, onNodesChange, onEdgesChange } = useAgentState();

  return (
    <div className="w-screen h-screen bg-[#0f0f11] flex flex-col relative">
      
      {/* O Canvas Infinito */}
      <div className="flex-1">
        <ReactFlow
          nodes={nodes}
          edges={edges}
          onNodesChange={onNodesChange}
          onEdgesChange={onEdgesChange}
          nodeTypes={nodeTypes}
          fitView
          className="soda-theme"
          minZoom={0.1}
        >
          {/* Fundo escuro sutil com pontos para orientar o olhar */}
          <Background color="#333" gap={20} size={1} />
          {/* Controles no canto para navegação espacial */}
          <Controls className="bg-zinc-800 border-zinc-700 fill-white" />
          <MiniMap nodeColor="#444" maskColor="rgba(0,0,0,0.7)" className="bg-zinc-900" />
        </ReactFlow>
      </div>

      {/* A Barra de Comando Soberana (Fica flutuando sobre o Canvas) */}
      <div className="absolute bottom-8 left-1/2 transform -translate-x-1/2 w-[600px] z-50">
         {/* Este componente chama a função invoke('ask_agent', { prompt }) do Tauri 
            ensinada na Parte 2. 
         */}
         <CommandInput />
      </div>

    </div>
  );
}
```

### O Fluxo Real na sua Tela:

1. Você digita na barra flutuante: _"Verifique os erros do meu projeto no diretório X"_.
2. A barra não vira um histórico de chat.
3. No Canvas, surge um nó azul: **[ Phi-4-mini ]**. Fica pulsando.
4. Logo abaixo, surge um nó laranja conectado por uma linha animada: **[ MCP: Jcodemunch ]**.
5. Se você clicar no nó laranja (Divulgação Progressiva), verá o log do MCP lendo o diretório X em Go.
6. A linha animada retorna para o nó azul, que para de pulsar, e exibe a conclusão compilada.

O controle visual e cognitivo volta 100% para você.

-----

# Genesis MC: Guia Prático - Parte 4 (A Batalha da VRAM)

Para rodar o **SODA** em uma RTX 2060m (6GB VRAM), precisamos de uma gestão de memória militar. O Rust será o general que decide qual modelo entra na VRAM, quando ele sai, e o quão espremida a informação deve estar antes de ser processada.

## 1. O Roteador SODA (Decisão de Milissegundos)

O Roteador não é uma IA. Usar um LLM para rotear outro LLM gasta tempo e tokens. O Roteador SODA é uma **heurística rápida em Rust** (Expressões Regulares e Palavras-chave) que decide o destino da tarefa instantaneamente.

### Estrutura do Roteador (`src-tauri/src/router.rs`)

```rust
use regex::Regex;

#[derive(Debug, Clone, PartialEq)]
pub enum AgentPersona {
    LogicCoder, // Vai usar o Phi-4-mini (Foco em código, JSON, tarefas exatas)
    LifeCoach,  // Vai usar o Mistral/Qwen (Foco em criatividade, planejamento, análise)
}

pub struct SodaRouter {
    code_regex: Regex,
}

impl SodaRouter {
    pub fn new() -> Self {
        Self {
            // Regex simples para detectar intenção de código ou sistema
            code_regex: Regex::new(r"(?i)(código|função|script|bug|refatorar|json|rust|python|sql)").unwrap(),
        }
    }

    // Retorna qual modelo deve ser carregado na VRAM
    pub fn route(&self, prompt: &str) -> AgentPersona {
        if self.code_regex.is_match(prompt) {
            AgentPersona::LogicCoder
        } else {
            AgentPersona::LifeCoach
        }
    }
}
```

## 2. Hot-Swapping: Dança das Cadeiras na VRAM

Se o Roteador decidir que precisamos do `Phi-4-mini`, mas o `Mistral` estiver na VRAM, precisamos **matar o processo atual e subir o novo**. O `llama.cpp` (usando o binário `llama-server`) é perfeito para isso via subprocessos no Rust.

**Por que não usar a API de troca do Ollama?** Porque matar o processo do `llama-server` via Rust garante **100% de liberação imediata da VRAM** no nível do Sistema Operacional, sem memory leaks (vazamentos de memória).

### Gerenciador de Modelos (`src-tauri/src/llm_manager.rs`)

```rust
use tokio::process::{Command, Child};
use std::sync::Arc;
use tokio::sync::Mutex;
use crate::router::AgentPersona;

pub struct LlmManager {
    current_process: Arc<Mutex<Option<Child>>>,
    current_persona: Arc<Mutex<Option<AgentPersona>>>,
}

impl LlmManager {
    pub fn new() -> Self {
        Self {
            current_process: Arc::new(Mutex::new(None)),
            current_persona: Arc::new(Mutex::new(None)),
        }
    }

    pub async fn ensure_model_is_loaded(&self, persona: &AgentPersona) -> Result<(), String> {
        let mut current_persona = self.current_persona.lock().await;
        
        // Se o modelo correto já está rodando, não faz nada
        if current_persona.as_ref() == Some(persona) {
            return Ok(());
        }

        println!("⚠️ VRAM Swap Necessário. Trocando para: {:?}", persona);

        let mut process_guard = self.current_process.lock().await;

        // 1. DERRUBA O MODELO ATUAL (Libera os 6GB de VRAM instantaneamente)
        if let Some(mut child) = process_guard.take() {
            println!("🛑 Matando processo Llama.cpp anterior...");
            let _ = child.kill().await; 
        }

        // 2. DEFINE O CAMINHO DO NOVO MODELO (Arquivos GGUF)
        let model_path = match persona {
            AgentPersona::LogicCoder => "./models/phi-4-mini-q4_k_m.gguf",
            AgentPersona::LifeCoach => "./models/mistral-7b-instruct-q4_k_m.gguf",
        };

        // 3. SOBE O NOVO MODELO
        // Usamos -ngl 99 (Offload total para GPU) e -c 4096 (Limite estrito de contexto para caber na RTX 2060m)
        let child = Command::new("./bin/llama-server")
            .args(&["-m", model_path, "-c", "4096", "-ngl", "99", "--port", "8080"])
            .spawn()
            .map_err(|e| format!("Falha ao iniciar llama-server: {}", e))?;

        *process_guard = Some(child);
        *current_persona = Some(persona.clone());

        // Aguarda 2 segundos para o servidor HTTP do Llama.cpp ficar pronto
        tokio::time::sleep(std::time::Duration::from_secs(2)).await;
        println!("✅ Novo modelo carregado e pronto na porta 8080.");

        Ok(())
    }
}
```

## 3. Toon Context: A Compressão de Contexto no Rust

Contexto grande = VRAM esgotada e lentidão. O conceito do **Toon Context** (da sua planilha) é minificar arrays e JSONs antes de enviar para o LLM.

Ao invés de rodar o pacote em Node/TS (que gastaria mais RAM do seu notebook), nós portamos a **essência do algoritmo para o Rust**.

### O Compressor (`src-tauri/src/toon_context.rs`)

Aqui nós cortamos gordura. Removemos quebras de linha inúteis, espaços duplos e chaves JSON gigantes antes de injetar na janela de contexto do LLM.

```rust
use serde_json::Value;

pub struct ToonCompressor;

impl ToonCompressor {
    /// Comprime textos longos e históricos do SQLite
    pub fn compress_text(input: &str) -> String {
        // 1. Remove múltiplos espaços por um único
        let single_spaces = input.split_whitespace().collect::<Vec<&str>>().join(" ");
        
        // 2. Remove "Stop Words" triviais se a heurística for agressiva (Opcional, mas salva tokens)
        // Exemplo: remover "por favor", "obrigado", "com base em"
        
        single_spaces
    }

    /// O Verdadeiro 'Toon Context': Minificação extrema de dados estruturados
    /// Pega resultados dos MCPs (que geralmente cospem JSONs gordos) e espreme.
    pub fn compress_json(json_str: &str) -> String {
        if let Ok(val) = serde_json::from_str::<Value>(json_str) {
            match val {
                Value::Array(arr) => {
                    // Se for um Array de Objetos (ex: lista de episódios do SQLite), 
                    // extraímos apenas os valores críticos, ignorando chaves repetitivas.
                    let mut compressed = String::new();
                    compressed.push_str("DATA:[");
                    
                    for item in arr {
                        if let Value::Object(map) = item {
                            // Pega apenas valores, ignorando as chaves para poupar tokens
                            let values: Vec<String> = map.values()
                                .map(|v| v.to_string().replace("\"", ""))
                                .collect();
                            compressed.push_str(&format!("({}),", values.join("|")));
                        }
                    }
                    compressed.push(']');
                    compressed.replace("),]", ")]") // Limpeza final
                },
                _ => serde_json::to_string(&val).unwrap_or_default() // Fallback: Minificação padrão
            }
        } else {
            // Se não for JSON válido, cai na compressão de texto
            Self::compress_text(json_str)
        }
    }
}
```

### Exemplo Prático de Economia de Tokens (Como o Toon Age)

**Saída original do SQLite/MCP (Consome ~80 Tokens):**

```
[
  { "id": "1", "action": "abriu_vscode", "time": "10:00" },
  { "id": "2", "action": "escreveu_rust", "time": "10:05" }
]
```

**Depois do `ToonCompressor::compress_json` (Consome ~18 Tokens):**

```
DATA:[(1|abriu_vscode|10:00),(2|escreveu_rust|10:05)]
```

O `Phi-4-mini` é inteligente o suficiente para entender a estrutura compactada `(ID|Ação|Hora)`. Você acabou de economizar 75% da janela de contexto para a mesma informação.

## 4. Unindo Tudo (O Fluxo Final na API)

Quando o React pede algo, o Rust faz a orquestração tática:

```
// Dentro do seu comando Tauri
#[tauri::command]
async fn ask_agent_optimized(prompt: String, state: State<'_, AppState>) -> Result<String, String> {
    
    // 1. Roteador decide quem assume o problema
    let persona = state.router.route(&prompt);
    
    // 2. Garante que o modelo certo está na VRAM (Mata o outro se necessário)
    state.llm_manager.ensure_model_is_loaded(&persona).await?;
    
    // 3. Busca histórico/dados no Banco (Simulação)
    let raw_memory_json = "[{\"user\":\"bruno\",\"goal\":\"otimizar vram\"}]"; 
    
    // 4. Toon Context ESMAGA a string antes de mandar pra VRAM
    let compressed_memory = ToonCompressor::compress_json(raw_memory_json);
    
    // 5. Constrói o Prompt final reduzido
    let final_prompt = format!("System: Context:{}\nUser: {}", compressed_memory, prompt);
    
    // 6. Faz a requisição HTTP local pro llama-server (que já está rodando na porta 8080)
    // ... Código de requisição HTTP usando reqwest para http://localhost:8080/completion ...

    Ok("Resposta gerada".into())
}
```

### O Triunfo da Arquitetura:

Com essas 4 peças (Roteador Regex, Subprocessos Kill/Spawn, Toon Context e SQLite), você transformou um notebook de 6GB de VRAM numa **estação de trabalho agêntica soberana**. A troca de contexto leva cerca de 2 segundos (tempo do NVMe carregar o modelo), e o sistema nunca sofre "_Out of Memory_".

-----

# Genesis MC: Guia Prático - Parte 5 (A Fonte da Verdade e SDD)

A premissa aqui é o **Pessimismo da razão, otimismo da vontade**: assuma que o Agente (LLM) vai tentar ser prolixo, esquecer regras de infraestrutura e sugerir bibliotecas pesadas. Para extrair a genialidade da IA (otimismo), precisamos algemá-la com especificações determinísticas (razão).

O seu repositório do SODA deve ter a seguinte estrutura raiz para a mente da IA:

```
/
├── .agents/
│   ├── rules/
│   │   └── global_nfrs.md    # Restrições de hardware e segurança
│   ├── skills/
│   │   └── ler_sqlite.md     # Como o agente deve usar a tool
├── docs/
│   └── adrs/                 # Architecture Decision Records
├── AGENTS.md                 # A Constituição (O LLM lê isso PRIMEIRO)
├── src-tauri/
└── src/
```

## 1. O Fluxo SDD (Spec-Driven Development) no SODA

No Antigravity IDE (ou no seu Canvas SODA), você não pede "Crie um botão que salva no banco". Você força a IA a passar por 4 fases. O Roteador SODA que construímos na Parte 4 lida com isso.

1. **`/specify` (Especificar):** Você escreve o que quer em texto puro. O agente _Life Coach (Mistral)_ refina e gera um arquivo `.md` de requisitos.
2. **`/plan` (Planejar):** O _Life Coach_ lê os `docs/adrs/` para garantir que o plano obedece à arquitetura (ex: "Usar Tauri IPC, não HTTP"). Gera a arquitetura.
3. **`/tasks` (Dividir):** O plano vira um checklist determinístico.
4. **`/implement` (Programar):** O _Logic Coder (Phi-4-mini)_ entra em ação na VRAM, lê a Task 1, e cospe apenas o código. Zero conversa.

## 2. A Constituição: O Template `AGENTS.md`

Este é o arquivo mais importante do projeto. É o prompt de sistema estático que o **Toon Context** deve anexar (de forma comprimida) no início de _todas_ as sessões críticas.

**Arquivo:** `/AGENTS.md`

```markdown
# 🤖 Constituição do Genesis MC (SODA)

**Você é um Agente do SODA (Sovereign OS).** Você opera localmente na máquina do usuário.
Você não é um assistente de chat. Você é um motor de raciocínio executável.

## 1. Regras de Ouro (NFRs - Non-Functional Requirements)
- **VRAM Strict Limit:** O hardware alvo possui uma **RTX 2060m (6GB VRAM)**. NUNCA sugira, importe ou planeje soluções que exijam mais de 4GB de VRAM sustentada (deixando 2GB para o SO).
- **Zero Cloud (Local-First):** É estritamente proibido sugerir APIs externas (OpenAI, AWS, Firebase). Todos os dados moram em SQLite local.
- **Eficiência de Tokens:** Não seja prolixo. Seja direto, brutalmente honesto e conciso. Use bullet points.
- **Comunicação Assíncrona:** A UI no React (Canvas) NUNCA pode ser bloqueada. Tudo no backend Rust deve rodar em `tokio::spawn`.

## 2. Divulgação Progressiva (Progressive Disclosure)
Ao gerar código ou explicar conceitos complexos para o usuário (Bruno, perfil 2e):
1. Dê a resposta direta e o código final primeiro.
2. Coloque explicações teóricas, logs de MCPs ou raciocínios secundários dentro de blocos `<details>` ou em nós secundários do Xyflow.
3. Não presuma que o usuário não sabe. Se desafiado, assuma o papel de "Advogado do Diabo" e aponte os pontos cegos do código.

## 3. Stack Tecnológica Obrigatória
- **Frontend:** React, TypeScript, TailwindCSS, Xyflow (para grafos), Lucide React.
- **Backend:** Rust, Tauri (IPC via Canais MPSC), Tokio.
- **Banco de Dados:** SQLite (via sqlx) com FTS5 habilitado.
```

## 3. As Habilidades: Template de SKILL.md

Para evitar saturar a janela de contexto de 4096 tokens do _Phi-4-mini_, não ensine todas as ferramentas de uma vez. Use o **Progressive Disclosure** na arquitetura. O arquivo `SKILL.md` descreve como o agente deve invocar um servidor MCP (ex: _Jcodemunch_).

**Arquivo:** `/.agents/skills/jcodemunch_mcp.md`

````markdown
# 🛠️ Skill: Leitura de Repositório (Jcodemunch MCP)

**Quando usar:** Quando o usuário pedir para analisar código, refatorar um arquivo grande ou encontrar bugs em diretórios que não estão no contexto atual.

**Como invocar (Output Esperado do LLM):**
Para acionar esta habilidade, você DEVE emitir o seguinte bloco JSON (e nada mais) no seu output. O motor Rust irá interceptar e executar a busca.

```json
{
  "tool": "jcodemunch",
  "action": "search",
  "path": "src-tauri/src/",
  "query": "impl McpServer"
}
````

**Restrições da Skill:**

- Não tente adivinhar a estrutura do código. Se você não tem certeza, invoque a tool e aguarde o motor Rust injetar o resultado no próximo turno.
- Após receber o resultado do Jcodemunch, use o **Toon Context** mentalmente: extraia apenas a assinatura das funções antes de planejar a refatoração.

````markdown

---

## 4. Architecture Decision Records (ADRs)

A IA é treinada na internet inteira, então ela sempre vai sugerir "use Postgres", "use Redis". Os ADRs são a vacina contra essa alucinação. Quando a IA roda o `/plan` no fluxo SDD, ela deve ler os ADRs para saber o que **já foi decidido e não deve ser questionado**.

**Arquivo:** `/docs/adrs/0001-uso-de-sqlite-para-memoria.md`
```markdown
# ADR 0001: Uso Exclusivo de SQLite (FTS5) para Memory Bank

**Status:** Aceito
**Data:** 19 de Março de 2026

## Contexto e Problema
O Genesis MC (SODA) requer um grafo de conhecimento e uma memória episódica para o "Life Coach". LLMs tradicionais usam Vector Databases (Milvus, Pinecone, ChromaDB) ou Grafos Nativos (Neo4j). No entanto, o sistema deve rodar inteiramente de forma local e com recursos limitados de RAM.

## Restrições (Decision Drivers)
- Inicialização da aplicação em menos de 2 segundos.
- Ocupar menos de 50MB de RAM na ociosidade (idle).
- Sem dependência de Docker ou instâncias JVM de fundo.

## Opções Consideradas
1. **ChromaDB / Qdrant:** Descartados. Exigem processos pesados em background e estouram a RAM.
2. **Neo4j:** Descartado. Overhead da JVM massivo.
3. **SQLite com FTS5 (Texto Completo) + sqlite-vec (Futuro):** Escolhido.

## Decisão (A Fonte da Verdade)
Nós escolhemos **SQLite**. Toda persistência de dados do SODA (Memória Semântica, Episódica, Relacional) DEVE ser modelada em tabelas relacionais clássicas dentro do diretório `src-tauri/db/`. 
As buscas de contexto inicial devem utilizar consultas textuais (FTS5) pela sua velocidade extrema em I/O local.

## Consequências
- **Positivo:** O footprint de memória do banco é praticamente zero. É apenas um arquivo em disco rápido.
- **Positivo:** Integração perfeita, segura e assíncrona com Rust via `sqlx`.
- **Negativo:** A criação de embeddings para buscas semânticas puras exigirá um esforço manual extra via extensões (ex: `sqlite-vec`) no futuro, ou roteamento para um modelo SLM de embedding dedicado.
````

## Como Automatizar isso no seu Fluxo de Trabalho Hoje:

1. **Crie a pasta `.agents`** no seu projeto agora.
2. **Cole o `AGENTS.md`** na raiz.
3. **Configure o seu prompt de sistema** no motor **Rust** (Parte 4) para fazer um _file read_ do `AGENTS.md` toda vez que carregar o modelo na VRAM. O LLM sempre acordará sabendo quem ele é e quais são os limites da sua placa de vídeo.

-----
