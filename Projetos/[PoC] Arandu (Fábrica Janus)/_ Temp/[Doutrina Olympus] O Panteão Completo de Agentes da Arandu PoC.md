---
sticker: lucide//boxes
---
# **[Doutrina Olympus] O Panteão Completo de Agentes da Arandu PoC**

Versão: 1.0

Guardião: @Janus

## **Introdução**

Este documento serve como o diretório oficial e a listagem completa de todos os agentes de IA concebidos para o ecossistema da **Arandu PoC**. A estrutura segue a **Doutrina Olympus**, que divide a força de trabalho em duas camadas distintas: o núcleo de governança e estratégia (`Olympus.core`) e as guildas de especialistas operacionais (`Olympus.agents`).

Cada agente aqui representa uma capacidade específica, e juntos eles formam a força de trabalho simbiótica que irá aumentar e colaborar com o Maestro.

## **`Olympus.core`: O Gabinete Estratégico e de Co-Evolução**

Estes são os agentes de mais alto nível. Eles não executam tarefas de produção; eles pensam, planejam, governam e garantem a evolução da própria fábrica. Eles residem no repositório central `Olympus.core`.

### **O Gabinete Operacional Estratégico**

|                     |                                |                                                                                                                                                                               |
| ------------------- | ------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Agente**          | **Arquétipo**                  | **Função Principal (em uma frase)**                                                                                                                                           |
| **`@Janus`**        | O Chefe de Gabinete (Facade)   | É o único ponto de contato para o Maestro, responsável por interpretar a intenção, delegar tarefas ao conselho interno e sintetizar os resultados em uma resposta coesa.      |
| **`@Elicitor`**     | O Interrogador Socrático       | Especializado em elicitação de requisitos, sua função é transformar solicitações vagas do Maestro em _briefings_ claros e acionáveis através de um diálogo de questionamento. |
| **`@Modeler`**      | O Arquiteto de Soluções        | Recebe o _briefing_ detalhado e sua função é projetar a solução, decompondo o problema em um plano de ataque e gerando o grafo de execução para o `@Orquestrador`.            |
| **`@Orquestrador`** | O Controlador de Tráfego Aéreo | É o motor de execução que recebe o plano do `@Modeler`, invoca os agentes operacionais da "Forja" na sequência correta e gerencia o estado das tarefas em andamento.          |
| **`@Crítico`**      | O Advogado do Diabo            | Atua como o revisor de qualidade final, analisando os artefatos concluídos em busca de falhas lógicas, desalinhamento com os requisitos e violações da "Constituição".        |

### **O Conselho de Co-Evolução (Os Meta-Agentes)**

|   |   |   |
|---|---|---|
|**Agente**|**Arquétipo**|**Função Principal (em uma frase)**|
|**`@Kairós`**|O Otimizador de Processos|Analisa continuamente os dados de observabilidade do `Argus Sentry` para identificar gargalos e ineficiências, propondo proativamente otimizações para os SOPs da fábrica.|
|**`@Epimeteu`**|O Historiador que Aprende|Realiza análises "post-mortem" automatizadas de projetos concluídos, extraindo lições aprendidas para atualizar o `Codex Prime` e evitar a repetição de erros.|
|**`@Prometeu`**|O Batedor de Inovações|Monitora o horizonte tecnológico em busca de novas ferramentas e técnicas, abrindo RFCs de "Avaliação Tecnológica" para garantir que a fábrica permaneça na vanguarda.|

## **`Olympus.agents`: A Forja das Guildas**

Estes são os agentes operacionais, os especialistas que executam as tarefas concretas. Eles são organizados em "guildas" por domínio de conhecimento e residem no repositório `Olympus.agents`.

### **Guilda de Arquitetura e Governança**

|   |   |
|---|---|
|**Agente**|**Função Principal (em uma frase)**|
|**`@ArquitetoDoCodex`**|Especialista em estruturar, nomear e organizar os documentos do `Codex Prime`, garantindo a aderência aos padrões.|
|**`@ArquitetoTI`**|Projeta a arquitetura de software de alto nível (HLD) e de baixo nível (LLD) para novos sistemas e funcionalidades.|
|**`@AgenteDeSeguranca`**|Executa análises de vulnerabilidade, revisa código sob a ótica de segurança e garante a conformidade com as políticas definidas no `@Cerberus`.|

### **Guilda de Engenharia de Software**

|   |   |
|---|---|
|**Agente**|**Função Principal (em uma frase)**|
|**`@EngenheiroBootstrap`**|O desenvolvedor "full-stack" inicial, capaz de criar os primeiros componentes da fábrica e de novos produtos.|
|**`@DevFastAPI`**|Especialista em Python e FastAPI para o desenvolvimento de APIs de backend robustas e performáticas.|
|**`@DevFlutter`**|Especialista em Dart e Flutter para o desenvolvimento de aplicações multiplataforma (mobile, web, desktop).|
|**`@DevJS`**|Especialista em JavaScript/TypeScript para tarefas de frontend ou desenvolvimento de extensões de navegador.|

### **Guilda de Produto e Estratégia**

|   |   |
|---|---|
|**Agente**|**Função Principal (em uma frase)**|
|**`@Estrategista`**|Analisa o cenário macro, a concorrência e as oportunidades de mercado para informar as decisões de alto nível.|
|**`@GerenteDeProduto` (PM)**|Define a visão e a estratégia do produto, gerencia o _roadmap_ e prioriza funcionalidades para maximizar o valor para o negócio.|
|**`@ProductOwner` (PO)**|Atua no nível tático, traduzindo o _roadmap_ em um _backlog_ detalhado, escrevendo Histórias de Usuário e Critérios de Aceite.|
|**`@UXDesigner`**|Foca na experiência do usuário, projetando jornadas, fluxos de interação e realizando pesquisas para garantir que o produto seja útil e intuitivo.|
|**`@UIDesigner`**|Foca na interface do usuário, criando o design visual, os componentes e o sistema de design para garantir que o produto seja esteticamente agradável e consistente.|

### **Guilda de Dados e Pesquisa**

|   |   |
|---|---|
|**Agente**|**Função Principal (em uma frase)**|
|**`@AgenteDePesquisa`**|Executa "Deep Research" na web e em outras fontes para coletar e sintetizar informações sobre um determinado tópico.|
|**`@AnalistaDeDados`**|Executa análises exploratórias em datasets para extrair insights e validar hipóteses.|
|**`@EngenheiroDeDados`**|Projeta e implementa os pipelines de dados e a infraestrutura do `Synapse Engine`.|

### **Guilda de Operações e Qualidade (QA)**

|   |   |
|---|---|
|**Agente**|**Função Principal (em uma frase)**|
|**`@EngenheiroDevOps`**|Gerencia a infraestrutura de CI/CD, os processos de deploy e a automação da plataforma `@Argos Sentry`.|
|**`@EngenheiroDeQA`**|Especialista em qualidade que projeta e executa planos de teste, gera casos de teste (em Gherkin, por exemplo) e automatiza testes de regressão.|
|**`@RevisorDeQualidade`**|Um agente de "reflection" que executa revisões de código, documentação ou outros artefatos com base em checklists de qualidade.|

### **Guilda de Negócios e Crescimento**

|   |   |
|---|---|
|**Agente**|**Função Principal (em uma frase)**|
|**`@AnalistaFinanceiro`**|Modela a viabilidade financeira dos projetos, estima custos e calcula o ROI.|
|**`@AgenteDeGrowth`**|Especialista em táticas de _growth hacking_ para otimizar a aquisição, ativação e retenção de usuários.|
|**`@AgenteDeMarketingDigital`**|Cria e executa campanhas de marketing, gerencia mídias sociais e produz conteúdo para a estratégia de Go-To-Market.|

### **Guilda de Conteúdo e Suporte**

|   |   |
|---|---|
|**Agente**|**Função Principal (em uma frase)**|
|**`@EscritorTecnico`**|Gera a documentação para humanos (no diretório `/docs`), como guias e tutoriais, a partir de especificações técnicas.|
|**`@AgenteDeConteudoEducacional`**|Cria materiais de aprendizado, como cursos ou vídeos, para os usuários dos produtos da fábrica.|
|**`@AgenteDeSuporte`**|Fornece suporte de primeiro nível aos usuários, respondendo a perguntas frequentes com base na documentação do `Codex`.|

### **Guilda de Assuntos Corporativos**

|   |   |
|---|---|
|**Agente**|**Função Principal (em uma frase)**|
|**`@AgenteLegal`**|Revisa documentos e políticas para garantir a conformidade com leis e regulamentações, como a LGPD.|
|**`@AgenteDeI18nL10n`**|Especialista em Internacionalização (i18n) e Localização (l10n) para adaptar produtos para diferentes mercados e idiomas.|