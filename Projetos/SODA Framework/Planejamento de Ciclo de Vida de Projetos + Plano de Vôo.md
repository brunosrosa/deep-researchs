# Orquestração de Engenharia de Software na Era Agêntica: Estratégias de Controle, Arquitetura de Processos e Integração de Ecossistemas

## Resumo Executivo

A indústria de desenvolvimento de software atravessa uma mudança de paradigma sísmica, transitando da codificação assistida por inteligência artificial (IA) para a **Engenharia Agêntica**. Neste novo modelo, o Ambiente de Desenvolvimento Integrado (IDE) deixa de ser apenas um editor de texto para se tornar uma plataforma de "Controle de Missão", onde o desenvolvedor atua como arquiteto e orquestrador de uma equipe de agentes autônomos. Este relatório técnico analisa profundamente as metodologias para controlar o ciclo de vida de desenvolvimento de software (SDLC) sob esta nova ótica, detalhando como dividir processos em tarefas granulares, planejar arquiteturas refinadas e conectar o ambiente de desenvolvimento à infraestrutura empresarial.

A análise centra-se na tríade tecnológica composta pelo **Google Antigravity** (a plataforma hospedeira), o **Ag-Kit / Antigravity Kit 2.0** (o framework organizacional de agentes) e o **Model Context Protocol - MCP** (o padrão de conectividade universal). Exploramos como esses componentes mitigam os riscos do "Vibe Coding" — codificação rápida mas superficial — introduzindo rigor, governança e auditabilidade. O documento fornece uma taxonomia completa de prompts para cada fase do ciclo de vida, estratégias de armazenamento de conhecimento via diretórios estruturados (`.agent`, `.gemini`) e um roteiro prático para implementação imediata, visando transformar a IA de uma ferramenta de autocompletar em um parceiro de engenharia confiável.

---

## 1. A Evolução do Paradigma: Do "Vibe Coding" à Engenharia Estruturada

### 1.1 A Crise da Abstração e a Necessidade de Controle

O advento dos Grandes Modelos de Linguagem (LLMs) introduziu o fenômeno do "Vibe Coding", onde a barreira de entrada para a criação de software foi drasticamente reduzida, permitindo que funcionalidades sejam geradas através de linguagem natural. No entanto, essa facilidade trouxe consigo um desafio crítico de engenharia: a perda de controle sobre a arquitetura e o ciclo de vida. Em ambientes tradicionais, o rigor é imposto pelo processo manual de escrita; na codificação generativa, a velocidade de produção de código muitas vezes ultrapassa a capacidade humana de revisão e estruturação, resultando em sistemas frágeis e difíceis de manter.

A demanda por um controle refinado do ciclo de vida — dividindo-o em processos, tarefas e fases — surge como uma resposta necessária para profissionalizar o uso da IA. Não basta gerar código; é preciso orquestrar a geração dentro de um pipeline de engenharia que contemple requisitos, design, implementação, testes e segurança. O Google Antigravity posiciona-se não apenas como um editor, mas como uma plataforma "agent-first" projetada para resolver essa dicotomia, oferecendo modos de operação distintos para planejamento e execução.

### 1.2 A Arquitetura da "Mission Control"

Ao contrário dos assistentes de codificação da geração anterior (como o GitHub Copilot original), que operavam como sugestões em linha, o paradigma do Antigravity introduz a noção de **Superfície de Gerenciamento** (Manager Surface). Esta interface dedicada permite ao desenvolvedor visualizar, instanciar e coordenar múltiplos agentes trabalhando de forma assíncrona.

Neste contexto, o "controle" solicitado pelo usuário é exercido através da definição de **Artefatos**. A IA não deve apenas produzir arquivos de código (`.py`, `.ts`), mas sim documentos intermediários de planejamento, listas de tarefas hierárquicas e planos de implementação detalhados. A transição do desenvolvimento imperativo ("escreva esta função") para o desenvolvimento declarativo ("planeje e implemente este módulo seguindo estas regras") exige uma nova classe de ferramentas e métodos, que detalharemos nos capítulos subsequentes, focando especificamente no framework Ag-Kit e na conectividade via MCP.

---

## 2. O Ecossistema Tecnológico: Antigravity, Ag-Kit e MCP

Para responder à questão fundamental sobre "o que conectar" e "quais kits usar", é imperativo dissecar a arquitetura de três camadas que compõe o estado da arte do desenvolvimento agêntico. A integração destes três componentes cria um sistema onde o planejamento refinado é não apenas possível, mas mandatório.

### 2.1 A Plataforma Hospedeira: Google Antigravity

O Antigravity fornece o runtime e a interface. Ele distingue-se pela capacidade de **Navegação Autônoma** (via subagentes de navegador) e pela gestão de estado do projeto. Ele opera em dois modos principais que são vitais para o controle do ciclo de vida:
- **Planning Mode (Modo de Planejamento):** Neste modo, a execução de código é inibida. O agente foca exclusivamente na análise de requisitos, leitura de contexto e geração de planos textuais. É a fase de "pensar antes de agir".
- **Fast Mode (Modo Rápido):** Focado na execução de tarefas triviais ou já planejadas.

A plataforma também introduz o conceito de **Human-in-the-Loop** (Humano no Circuito) através de mecanismos de feedback em artefatos. O desenvolvedor pode comentar em um plano de implementação gerado pela IA da mesma forma que comentaria em um Google Doc, permitindo um refinamento iterativo antes de qualquer linha de código ser escrita.

### 2.2 O Framework Organizacional: Ag-Kit (Antigravity Kit 2.0)

Se o Antigravity é o sistema operacional, o **Ag-Kit** é o conjunto de aplicações empresariais que estruturam o trabalho. Identificado na pesquisa como a solução definitiva para a pergunta sobre "algum addon, método ou kit", o Ag-Kit 2.0 transforma o IDE genérico em uma organização de desenvolvimento completa.

O Ag-Kit não é apenas um plugin; é uma metodologia codificada que impõe estrutura através de:

1. **Especialização de Agentes:** Em vez de um modelo genérico, o kit fornece 16 personas especializadas (ex: `@security-auditor`, `@frontend-specialist`, `@database-architect`). Cada agente possui um prompt de sistema (system prompt) otimizado e acesso a ferramentas restritas ao seu domínio, evitando alucinações fora de sua área de competência.
2. **Habilidades (Skills):** Módulos de conhecimento dinâmico. Um agente de backend, ao detectar que está trabalhando em um projeto Python, carrega automaticamente a "Skill" de PEP-8 e gestão de dependências via `pip`/`uv`, ignorando conhecimentos irrelevantes de npm ou maven. Isso aumenta a precisão e reduz a janela de contexto necessária.
3. **Workflows (Fluxos de Trabalho):** Processos pré-definidos acionados por comandos slash (ex: `/brainstorm`, `/plan`, `/deploy`). Estes workflows padronizam a entrada e saída de cada fase do ciclo de vida.

### 2.3 A Camada de Conectividade Universal: Model Context Protocol (MCP)

A resposta para "existe algo que posso conectar para planejar bastante refinado?" reside no **Model Context Protocol (MCP)**. Desenvolvido pela Anthropic e adotado como padrão aberto, o MCP resolve o problema do isolamento da IA.

Historicamente, a IA no IDE só "via" o código aberto. Com o MCP, o Antigravity pode conectar-se a:

- **Fontes de Verdade de Dados:** Bancos de dados (PostgreSQL, Snowflake) para validar schemas e dados reais.
- **Orquestradores de Processos:** Ferramentas como Apache Airflow e dbt para monitorar pipelines de dados e transformações.
- **Repositórios de Conhecimento:** Wikis, GitHub Issues, Jira e Slack para entender o contexto de negócio e as prioridades do projeto.

O MCP funciona como uma "porta USB-C para aplicações de IA", permitindo que o desenvolvedor plugue qualquer sistema empresarial ao contexto do agente de planejamento, garantindo que o plano gerado seja compatível com a realidade da infraestrutura da empresa.

---

## 3. Controle do Ciclo de Vida: Decomposição em Processos e Fases

O controle eficaz do ciclo de vida em um ambiente agêntico exige uma abordagem estruturada para a decomposição de problemas. Não se deve solicitar à IA que "construa o sistema", mas sim que execute uma série de processos interconectados, cada um gerando artefatos verificáveis.

### 3.1 Fase 1: Iniciação e Planejamento Arquitetural

O objetivo desta fase é transformar uma intenção abstrata em um plano técnico concreto. O uso do **Planning Mode** do Antigravity é mandatório aqui.

**Processo de Brainstorming e Definição:** Utilizando o workflow `/brainstorm` do Ag-Kit, o desenvolvedor inicia uma sessão divergente. O agente `@product-manager` deve ser invocado para explorar os requisitos. A saída deste processo não é código, mas um documento de requisitos (ex: `PRD.md`) que define o escopo, as funcionalidades principais e os critérios de sucesso. O controle é exercido através da revisão deste artefato; se o PRD estiver incorreto, a fase de codificação nem sequer começa.

**Refinamento Arquitetural:**

Uma vez aprovado o PRD, o workflow `/plan` é acionado com o agente `@architect`. Este agente analisa os requisitos e gera um **Implementation Plan** (Plano de Implementação). Este documento é crucial para o "refinamento" solicitado pelo usuário. Ele deve conter:
- Estrutura de diretórios proposta.
- Diagramas de fluxo de dados (utilizando sintaxe Mermaid).
- Definição de APIs e contratos de dados.
- Análise de impacto em sistemas existentes (via conexão MCP com o repositório ou banco de dados).

### 3.2 Fase 2: Decomposição em Tarefas e Funcionalidades

A granularidade é a chave para o controle. Um plano monolítico é difícil de auditar. A IA deve ser instruída a quebrar o Plano de Implementação em uma **Lista de Tarefas (Task List)** hierárquica.

**A Técnica de Micro-Tarefas:**

O sistema deve gerar um arquivo (ex: `TASKS.md`) onde cada funcionalidade é quebrada em tarefas atômicas que:
1. Possam ser implementadas em uma única sessão de trabalho.
2. Tenham um critério de teste claro (ex: "A função X deve retornar Y quando Z").
3. Possam ser revertidas individualmente em caso de falha.

No Antigravity, estas tarefas aparecem na interface de gerenciamento, permitindo que o desenvolvedor priorize, reordene ou cancele itens antes da execução. O controle "refinado" acontece aqui: o desenvolvedor pode expandir uma tarefa genérica ("Criar login") em sub-tarefas específicas ("Criar tabela de usuários", "Implementar hashing de senha", "Criar rota de API", "Criar formulário React") para garantir que nada seja esquecido.

### 3.3 Fase 3: Execução e Implementação Especializada

Na fase de execução, o controle muda de "o que fazer" para "como fazer". Aqui, a delegação para agentes especialistas do Ag-Kit é fundamental.

**Orquestração de Especialistas:**

Em vez de um único agente tentar escrever SQL e CSS, o sistema deve invocar:
- `@backend-specialist` para a lógica de servidor e banco de dados.
- `@frontend-specialist` para a interface e experiência do usuário.
- `@seo-specialist` para garantir a otimização de busca desde a primeira linha de código.

Cada agente opera dentro de um contexto restrito (Saturação de Contexto Otimizada). O agente de frontend, por exemplo, não precisa carregar o schema completo do banco de dados, apenas a definição da API que o agente de backend produziu. Isso reduz alucinações e erros de integração.

### 3.4 Fase 4: Verificação, Testes e Qualidade

O controle de qualidade não pode ser uma etapa posterior; deve ser contínuo.

**Automação de QA:** O workflow `/test` aciona o agente `@qa-engineer`. Este agente não escreve código de produto, mas sim código de teste. Ele lê a implementação e gera testes unitários e de integração para validar se os requisitos do PRD foram atendidos. Paralelamente, o agente `@security-auditor` deve ser invocado via workflow `/review` para analisar o código em busca de vulnerabilidades (ex: injeção de SQL, exposição de segredos) antes que o código seja considerado pronto para merge.

---

## 4. Integração Prática: Conectando o Mundo Real via MCP

Para "atender todos os processos", o ambiente de desenvolvimento deve transcender os limites do sistema de arquivos local. A utilização de servidores MCP permite conectar o planejamento da IA à realidade operacional da empresa.

### 4.1 Conectando à Camada de Dados (Data Layer)

Em projetos modernos, o código raramente vive isolado de dados. Conectar o Antigravity a bancos de dados como PostgreSQL ou Data Warehouses como Snowflake via MCP permite um nível de planejamento refinado inalcançável por IAs desconectadas.

**Cenário de Uso:**

Ao planejar uma nova funcionalidade que requer armazenamento de dados, o agente conectado via MCP pode:
1. Consultar o esquema atual do banco de dados em produção (Read-Only) para evitar conflitos de nomenclatura.
2. Analisar a distribuição de dados reais para sugerir índices otimizados.
3. Validar se as queries SQL geradas são sintaticamente corretas contra o motor do banco de dados específico.
4. Gerar migrações de esquema que respeitam as constraints existentes.

A configuração técnica envolve a adição de servidores MCP no arquivo de configuração do Antigravity, apontando para as instâncias de banco de dados seguras (preferencialmente ambientes de staging ou dev).

### 4.2 Conectando à Engenharia de Dados (dbt & Airflow)

Para equipes de engenharia de dados, o controle do ciclo de vida envolve pipelines e transformações. O MCP permite trazer o contexto dessas ferramentas para o IDE.
- **dbt (data build tool):** O servidor MCP do dbt permite que o agente compreenda a linhagem (lineage) dos modelos de dados. Se o desenvolvedor pedir para alterar uma coluna em um modelo _upstream_, o agente pode alertar imediatamente sobre quais modelos _downstream_ e dashboards serão quebrados, permitindo um planejamento de refatoração seguro.
- **Apache Airflow:** O servidor MCP do Airflow permite que o agente monitore a execução de DAGs, leia logs de falha e proponha correções de código baseadas nos erros de runtime reais, fechando o ciclo entre desenvolvimento e operação (DevOps).

### 4.3 Conectando à Gestão de Conhecimento e Projetos

Para dividir bem as funcionalidades, a IA precisa entender o "porquê" das tarefas. Conectar servidores MCP de ferramentas como GitHub Issues, Linear ou Jira permite que o agente leia a descrição completa da funcionalidade escrita pelo Product Manager, incluindo comentários e anexos, garantindo que o plano de implementação cubra todos os casos de uso e critérios de aceitação definidos fora do código.

---

## 5. Taxonomia de Prompts e Estrutura de Organização

Uma das solicitações centrais do usuário refere-se a "quais conjuntos de prompts criar" e "onde guardar". A resposta exige uma organização sistemática do conhecimento da IA, dividindo os prompts em categorias funcionais e armazenando-os em estruturas de diretórios padronizadas.

### 5.1 Onde Guardar e Organizar (`.agent` e `.gemini`)

A organização física dos arquivos é a base da governança da IA. Recomenda-se estritamente a seguinte estrutura de diretórios, que deve ser versionada no Git junto com o código do projeto.

#### Diretório Global (`~/.gemini/`)

Localizado na pasta home do usuário, este diretório controla o comportamento da IA em _todos_ os projetos.
- **`GEMINI.md`**: O arquivo de "Constituição". Contém regras invioláveis de segurança, ética e estilo pessoal.
- **`global_workflows/`**: Scripts de automação que o desenvolvedor usa recorrentemente (ex: "Gerar relatório de status diário").

#### Diretório do Projeto (`./.agent/`)

Localizado na raiz de cada projeto, criado pelo comando `ag-kit init`.
- **`agents/`**: Definições das personas (quem faz o trabalho). Arquivos `.yaml` ou `.ts` que definem o nome, modelo base e ferramentas permitidas para cada agente.
- **`rules/`**: Regras específicas do projeto (ex: "Neste projeto usamos Tabs, não Espaços", "Sempre use a biblioteca de componentes `ui-kit-v2`").
- **`skills/`**: Conhecimento técnico específico. Arquivos Markdown ou código que ensinam à IA como usar uma biblioteca interna proprietária ou um padrão de arquitetura específico.
- **`workflows/`**: Os "conjuntos de prompts" operacionais. Arquivos que definem sequências de passos para realizar tarefas complexas.

### 5.2 Conjuntos de Prompts Essenciais (Templates)

Abaixo, detalhamos os conjuntos de prompts que devem ser criados e armazenados na pasta `.agent/workflows/` para cobrir o ciclo de vida completo. Estes prompts devem ser escritos em Markdown claro e estruturado.
#### Conjunto A: Iniciação e Arquitetura (`/workflows/1-architect.md`)

Este conjunto controla a fase de planejamento refinado.

> **Estrutura do Prompt:**
> "Você é o Agente Arquiteto Sênior. Sua meta é transformar requisitos vagos em especificações técnicas.
> 1. **Entrada:** Leia os requisitos do usuário e os arquivos em `docs/architecture/`.
> 2. **Análise:** Identifique lacunas nos requisitos. Se houver ambiguidade, faça perguntas de clarificação (não assuma nada).
> 3. **Processo:**
>     - Analise o impacto no esquema do banco de dados (use a ferramenta MCP `postgres-schema`).
>     - Analise a compatibilidade com a stack tecnológica atual.
> 4. **Saída:** Gere um arquivo `PLAN-.md` contendo: Diagrama de Sequência (Mermaid), Definição de API (OpenAPI spec preliminar) e Lista de Arquivos a Criar/Modificar.
> 5. **Restrição:** Não gere código executável nesta etapa. Apenas documentação."

#### Conjunto B: Implementação Frontend (`/workflows/2-frontend-build.md`)

Focado na criação de interfaces visuais e interação.

> **Estrutura do Prompt:**
> "Você é o Especialista Frontend (React/Vue/Angular).
> 1. **Contexto:** Leia o arquivo `PLAN-.md` gerado pelo Arquiteto e o Design System em `src/components/`.
> 2. **Ação:** Implemente a interface visual.
> 3. **Regras de Ouro:**
>     - Use componentes reutilizáveis sempre que possível.
>     - Garanta acessibilidade (WCAG 2.1 AA).
>     - Implemente tratamento de erros visual (Toasts/Alerts).
> 4. **Verificação:** Crie um arquivo de storybook ou exemplo de uso para revisão visual."

#### Conjunto C: Implementação Backend (`/workflows/3-backend-build.md`)

Focado em lógica de negócios, dados e segurança.

> **Estrutura do Prompt:**
> "Você é o Especialista Backend.
> 1. **Contexto:** Leia o `PLAN-.md` e o esquema atual do banco de dados.
> 2. **Ação:** Implemente a lógica de API e persistência.
> 3. **Segurança:** Utilize Prepared Statements para SQL. Valide todos os inputs com Zod/Pydantic. Nunca exponha stack traces no retorno da API.
> 4. **Testes:** Gere testes unitários para cobrir os casos de sucesso e falha (edge cases)."

#### Conjunto D: Revisão e Refinamento (`/workflows/4-review.md`)

Focado em qualidade e conformidade.

> **Estrutura do Prompt:**
> "Você é o Auditor de Código e Segurança.
> 1. **Entrada:** Analise o diff dos arquivos gerados nas etapas anteriores.
> 2. **Checklist:**
>     - Existem credenciais hardcoded? (FALHA IMEDIATA)
>     - O código segue o guia de estilo do projeto em `.agent/rules/style.md`?
>     - A complexidade ciclomática está aceitável?
> 3. **Saída:** Gere um relatório de revisão. Se houver falhas críticas, gere as correções sugeridas. Se aprovado, sugira a mensagem de commit seguindo o padrão Conventional Commits."

---

## 6. Governança e Segurança: O Arquivo GEMINI.md

O controle não existe sem limites. O arquivo `GEMINI.md` é a ferramenta primária para impor restrições rígidas ao comportamento da IA, garantindo que ela atue dentro de parâmetros seguros e éticos.

Este arquivo deve ser configurado imediatamente e conter regras de "Negação Padrão". 
Exemplos de diretivas críticas a incluir:

|**Categoria**|**Regra (Exemplo de Prompt no GEMINI.md)**|
|---|---|
|**Segurança de Sistema**|"NUNCA execute comandos destrutivos de sistema (ex: `rm -rf`, `format`, `drop table`) sem solicitar confirmação explicita e aguardar a aprovação do usuário."|
|**Privacidade de Dados**|"NUNCA envie conteúdo de arquivos `.env`, chaves privadas SSH ou arquivos contendo a string 'SECRET' para servidores externos ou logs."|
|**Integridade de Código**|"NUNCA remova funcionalidades existentes ou código legado a menos que explicitamente instruído. Em caso de dúvida sobre se um código é usado, pergunte."|
|**Consistência**|"SEMPRE leia o arquivo `README.md` e `architecture.md` antes de propor mudanças estruturais para garantir alinhamento com o design do projeto."|

A presença deste arquivo garante que, independentemente do agente ou workflow utilizado, existem salvaguardas universais que impedem a IA de causar danos irreparáveis ao projeto ou ao ambiente de desenvolvimento.

---

## 7. Roteiro Prático de Implementação (Primeiros Passos)

Para responder à questão "Quais primeiros passos devo realizar logo após aqui?", estruturamos um plano de ação imediato para transformar o ambiente de desenvolvimento.

### Passo 1: Preparação do Ambiente (Dia 1)

1. **Instalação do Antigravity:** Assegure o acesso e instalação da plataforma.
2. **Inicialização do Ag-Kit:** No terminal do seu projeto principal, execute o comando de inicialização:
    ```Bash
    npx @vudovn/ag-kit init
    ```
    Isso criará automaticamente a estrutura de pastas `.agent/`, populando-a com os agentes e workflows padrão. É o ponto de partida para a organização.
3. **Configuração da Governança:** Crie o arquivo `~/.gemini/GEMINI.md` e insira as regras de segurança básicas mencionadas no capítulo anterior.

### Passo 2: Conectividade MCP (Dia 2)

1. **Mapeamento de Ferramentas:** Identifique quais ferramentas externas sua equipe usa (Postgres? GitHub? Jira?).
2. **Instalação de Servidores:** Utilize o gerenciador de pacotes (npm/pip/uv) para instalar os servidores MCP correspondentes. Exemplo para Postgres:
    ```Bash
    npm install -g @modelcontextprotocol/server-postgres
    ```
3. **Configuração:** Edite o arquivo de configuração do Antigravity (geralmente um JSON de settings) para registrar esses servidores, fornecendo as credenciais de acesso (preferencialmente de ambientes de desenvolvimento).

### Passo 3: O Ciclo Piloto (Dia 3)

1. **Selecione uma Funcionalidade Isolada:** Escolha uma tarefa de complexidade média (ex: "Criar um endpoint de API para listar produtos com filtro").
2. **Execute o Workflow de Planejamento:** Use o comando `/plan` ou `/brainstorm` para gerar o artefato de planejamento. **Não pule esta etapa.**
3. **Refine o Plano:** Abra o arquivo markdown gerado, leia, critique e peça ajustes à IA. Apenas aprove quando estiver satisfeito com a arquitetura proposta.
4. **Execute a Criação:** Use o comando `/create` invocando o agente especialista adequado (`@backend-specialist`).
5. **Auditoria:** Use o comando `/review` ou `/test` para validar o resultado antes de fazer o commit.

---

## 8. Dicas de Reforço e Insights Estratégicos

Para concluir, reforçamos pontos críticos que diferenciam o sucesso do fracasso na implementação da engenharia agêntica.

### 8.1 Saturação de Contexto e Foco Cognitivo

Um erro comum é tentar fornecer "todo o contexto" para a IA o tempo todo. Isso leva à degradação da qualidade das respostas (fenômeno "Lost in the Middle").
- **Dica:** Utilize a estrutura de Agentes Especialistas e Skills do Ag-Kit para segmentar o contexto. O agente de Frontend não precisa saber sobre a configuração do Terraform. O agente de Banco de Dados não precisa ver os arquivos CSS. O MCP ajuda a trazer contexto _on-demand_ (apenas o que é necessário para a tarefa atual), mantendo a janela de contexto limpa e eficiente.

### 8.2 A Importância do Feedback Assíncrono

O Antigravity permite que os agentes continuem trabalhando enquanto você faz outra coisa.
- **Dica:** Desenvolva o hábito de "despachar" tarefas. Inicie um workflow de `/test` ou `/review` (que pode demorar alguns minutos para rodar em todo o projeto) e mude o foco para o planejamento da próxima funcionalidade. Trate os agentes como colegas de equipe júnior: delegue, mas verifique o resultado depois. Use os artefatos (relatórios, planos) como pontos de verificação assíncronos.

### 8.3 O Código como Subproduto do Plano

A mudança mental mais importante é valorizar o **Plano de Implementação** acima do código em si.
- **Dica:** Gaste 70% do seu tempo interagindo com a IA na fase de planejamento e arquitetura (Planning Mode). Se o plano for sólido, a geração do código (Fast Mode) será trivial e correta. Se o plano for ruim, o código será um "Vibe Coding" de baixa qualidade. O controle refinado reside na qualidade das instruções e restrições impostas antes da primeira linha de código ser gerada.

### 8.4 Evolução Contínua das Regras

As regras em `.agent/rules` e `.gemini/GEMINI.md` não são estáticas.
- **Dica:** Sempre que a IA cometer um erro (ex: usar uma biblioteca depreciada), não apenas corrija o código. Vá até o arquivo de regras e adicione uma nova diretiva proibindo aquele padrão. Dessa forma, o sistema "aprende" e o erro não se repete. Transforme incidentes em regras de governança permanentes.

A adoção destas práticas transforma o IDE de uma ferramenta passiva em um ecossistema ativo de engenharia, onde o desenvolvedor orquestra processos complexos com precisão, segurança e eficiência, elevando o desenvolvimento de software a um novo patamar de produtividade e qualidade.

---

Aqui o plano de voo imediato, dividido em **Configuração**, **Estrutura** e **Rotina**.

### **Fase 1: Configuração do "Controle de Missão" (Dia 1)**

Antes de codificar qualquer coisa, você precisa preparar o terreno para que o Antigravity e os Agentes saibam quem manda e onde estão pisando.

1. **Instale o Framework Organizacional (Ag-Kit):**
    - No terminal do seu projeto, rode:
        ```Bash
        npx @vudovn/ag-kit init
        ```
    - Isso criará a pasta `.agent/` com toda a estrutura de cérebros (agentes), habilidades (skills) e fluxos (workflows). **Não pule isso.** É a diferença entre um estagiário perdido e uma equipe sênior.
2. **Resolva a "Fronteira" WSL/Windows (Crítico):**
    - **O Symlink:** Faça o comando `agy` funcionar no seu terminal Linux para abrir o IDE corretamente conectado.
        - No terminal WSL: `ln -sf "/mnt/c/Users/<SEU_USER>/AppData/Local/Programs/Antigravity/bin/antigravity" ~/.local/bin/agy`
    - **A Rede:** Se estiver no Windows 11, edite o arquivo `%UserProfile%\.wslconfig` no Windows e adicione:
        ```TOML
        [wsl2]
        networkingMode=mirrored
        ```
        Isso permite que o Agente de Navegador (que roda no Windows) veja os servidores do Airflow/dbt (que rodam no Linux) via `localhost`.
3. **Conecte o Cérebro aos Dados (MCP do dbt):**
    - Localize o arquivo de configuração do MCP no Antigravity (Menu `...` > Manage MCP Servers > View raw config).
    - Adicione a configuração do dbt para que o agente pare de alucinar nomes de tabelas.
    - _Dica:_ Use `uvx` para rodar o servidor dbt sem sujar seu ambiente global Python.

---

### **Fase 2: A "Constituição" do Projeto (Dia 2)**

Você precisa criar os documentos que servirão de "leis" para a IA. Sem isso, cada prompt é uma loteria.

1. **Crie o arquivo `GEMINI.md` (Global):**
    - Local: `~/.gemini/GEMINI.md` (na sua home do usuário).
    - Conteúdo: Regras universais. Ex: "Nunca apague código sem fazer backup", "Sempre use Docstrings no formato Google", "Prefira CTEs a subqueries no SQL".
2. **Crie os Prompts de Fluxo de Trabalho (Seus "SOPs"):**
    - Crie/Edite os arquivos dentro de `.agent/workflows/`. Recomendo criar estes 3 essenciais agora:
    
    **A. O Planejador (`.agent/workflows/1-plan.md`)**
    > **Gatilho:** `/plan`
    > **Objetivo:** Transformar ideia em Spec.
    > **Prompt:** "Atue como Product Manager Sênior. Entreviste-me sobre a nova funcionalidade. Em seguida, crie um arquivo `specs/FUNC-001.md` contendo: 1) User Stories, 2) Critérios de Aceite, 3) Desenho de Dados (Schemas). Não escreva código ainda."
    
    **B. O Arquiteto (`.agent/workflows/2-arch.md`)**
    > **Gatilho:** `/arch`
    > **Objetivo:** Transformar Spec em Tarefas Técnicas.
    > **Prompt:** "Leia o `specs/FUNC-001.md`. Crie um plano de implementação técnica detalhado em `specs/TECH-001.md`. Liste os arquivos que precisam ser criados ou alterados. Quebre o trabalho em uma lista de tarefas no arquivo `TASKS.md`. Use o MCP para verificar se as tabelas existem."
    
    **C. O Construtor (`.agent/workflows/3-build.md`)**
    > **Gatilho:** `/build`
    > **Objetivo:** Executar tarefas atomicamente.
    > **Prompt:** "Leia o `TASKS.md`. Pegue a primeira tarefa não concluída. Implemente-a seguindo as regras do `GEMINI.md`. Após implementar, crie um teste unitário para validar. Marque a tarefa como concluída apenas se o teste passar."

---

### **Fase 3: A Rotina de Execução (Dia 3 em diante)**

Abandone o "vibe coding" solto. Adote este ciclo para qualquer nova feature:
1. **Brainstorm (Divergência):** Use o Agente de Navegador para pesquisar referências. "Pesquise como o Netflix faz Data Mesh e sugira uma adaptação para nosso projeto."
2. **Especifique (Convergência):** Rode `/plan`. Responda as perguntas do agente. **Revise o MD gerado.** Se o documento estiver ruim, o código será lixo. Aprove o documento.
3. **Planeje (Quebra):** Rode `/arch`. O agente vai listar os passos.
4. **Execute (Mão na Massa):** Rode `/build`. Vá tomar um café.
5. **Valide (Auditoria):** Não confie. Peça "Rode os testes dbt" ou use o navegador para ver o Airflow rodando.
### **Resumo dos Arquivos para Criar Agora:**

|**Arquivo**|**Onde?**|**O que faz?**|
|---|---|---|
|`.agent/agents/data_eng.yaml`|Projeto|Define o agente especialista com ferramentas de dbt/SQL.|
|`.agent/workflows/1-plan.md`|Projeto|Seu prompt "milagroso" de planejamento.|
|`~/.gemini/GEMINI.md`|Home (Global)|Suas regras de segurança e estilo inegociáveis.|
|`mcp_config.json`|Config do IDE|Conecta o Antigravity ao seu dbt/Banco de Dados local.|

**Dica Final de Ouro:**

Pare de tentar fazer o agente "codar tudo de uma vez". O segredo para qualquer projeto dar certo é você gastar 80% do tempo refinando os arquivos `.md` de especificação (Specs) e deixar o agente gastar 20% do tempo gerando o código Python/SQL baseado neles. **Spec-Driven Development** é a única maneira de manter a sanidade.