---
sticker: lucide//atom
---
# **[Estratégia Olympus] A Arquitetura da Força de Trabalho Agêntica**

Versão: 1.0

Guardião: @Janus

## **Introdução: A Doutrina Olympus**

A **Estratégia Olympus** define a estrutura organizacional, a hierarquia de comando e a filosofia operacional da força de trabalho inteligente da **Arandu PoC**. Ela abandona a ideia de uma IA monolítica em favor de um ecossistema de agentes especializados, distribuídos em duas camadas distintas, mas interdependentes: um núcleo de governança e estratégia (`Olympus.core`) e uma força de trabalho operacional e escalável (`Olympus.agents`).

O propósito desta doutrina é criar uma organização de IA que seja ao mesmo tempo **estável e poderosa em seu núcleo**, e **flexível e adaptável em sua execução**. É um modelo projetado para equilibrar a tomada de decisão ponderada com a agilidade operacional, permitindo que a "Fábrica Janus" pense, planeje, execute e evolua com clareza e propósito.

## **Pilar 1: `Olympus.core` (O Gabinete Estratégico)**

**Propósito:** Servir como o **cérebro cognitivo, o centro de governança e o conselho de planejamento** da fábrica. Os agentes do `Olympus.core` não executam tarefas de produção; eles pensam, questionam, planejam, governam e orquestram. Eles residem em um monorepo central, pois suas funções são interdependentes e formam o núcleo do sistema.

### **O Gabinete Operacional**

Este é o grupo de agentes que gerencia o ciclo de vida de uma solicitação, desde a ideia vaga até o plano de execução detalhado.

|   |   |   |
|---|---|---|
|**Agente**|**Arquétipo**|**Função Principal (em uma frase)**|
|**`@Janus`**|**O Chefe de Gabinete (Facade)**|É o único ponto de contato para o Maestro, responsável por interpretar a intenção, delegar tarefas ao conselho interno e sintetizar os resultados em uma resposta coesa.|
|**`@Elicitor`**|**O Interrogador Socrático**|Especializado em elicitação de requisitos, sua função é transformar solicitações vagas do Maestro em _briefings_ claros e acionáveis através de um diálogo de questionamento.|
|**`@Modeler`**|**O Arquiteto de Soluções**|Recebe o _briefing_ detalhado e sua função é projetar a solução, decompondo o problema em um plano de ataque e gerando o grafo de execução para o `@Orquestrador`.|
|**`@Orquestrador`**|**O Controlador de Tráfego Aéreo**|É o motor de execução que recebe o plano do `@Modeler`, invoca os agentes operacionais da "Forja" na sequência correta e gerencia o estado das tarefas em andamento.|
|**`@Crítico`**|**O Advogado do Diabo**|Atua como o revisor de qualidade final, analisando os artefatos concluídos em busca de falhas lógicas, desalinhamento com os requisitos e violações da "Constituição" antes da entrega ao Maestro.|

### **O Conselho de Co-Evolução (Os Meta-Agentes)**

Este é o grupo de agentes cujo único propósito é analisar e melhorar a própria fábrica, garantindo sua evolução a longo prazo.

|   |   |   |
|---|---|---|
|**Agente**|**Arquétipo**|**Função Principal (em uma frase)**|
|**`@Kairós`**|**O Otimizador de Processos**|Analisa continuamente os dados de observabilidade do `Argus Sentry` para identificar gargalos e ineficiências, propondo proativamente otimizações para os SOPs da fábrica.|
|**`@Epimeteu`**|**O Historiador que Aprende**|Realiza análises "post-mortem" automatizadas de projetos concluídos, extraindo lições aprendidas para atualizar o `Codex Prime` e evitar a repetição de erros.|
|**`@Prometeu`**|**O Batedor de Inovações**|Monitora o horizonte tecnológico em busca de novas ferramentas e técnicas, abrindo RFCs de "Avaliação Tecnológica" para garantir que a fábrica permaneça na vanguarda.|

## **Pilar 2: `Olympus.agents` (A Forja das Guildas)**

**Propósito:** Servir como a **força de trabalho operacional e escalável** da fábrica. Os agentes aqui são especialistas em domínios específicos, organizados em "guildas" para promover a coesão e a reutilização de ferramentas. Eles vivem em um monorepo dedicado, mas são funcionalmente independentes e são invocados pelo `@Orquestrador` para executar tarefas concretas.

### **As Guildas e Seus Emissários**

A seguir, uma lista consolidada de todos os agentes operacionais que identificamos até agora, organizados em suas respectivas guildas.

#### **Guilda de Arquitetura de Conhecimento**

- **`@ArquitetoDoCodex`**: Especialista em estruturar, nomear e organizar os documentos do `Codex Prime`, garantindo a aderência aos padrões e à filosofia `Diátaxis`.
    

#### **Guilda de Engenharia de Software**

- **`@EngenheiroBootstrap`**: O desenvolvedor inicial, responsável por transformar especificações em código funcional para os componentes da própria fábrica e para novos produtos.
    
- **`@RevisorDeQualidade`**: Um agente de "reflection" que analisa o código gerado em busca de aderência a padrões de estilo, bugs lógicos e clareza.
    
- **`@ArquitetoTI`**: Um agente sênior que projeta a arquitetura de software de alto nível para novos sistemas, focando em escalabilidade, resiliência e segurança.
    

#### **Guilda de Estratégia e Produto**

- **`@Estrategista`**: Analisa o cenário macro, a concorrência e as oportunidades de mercado para informar as decisões de alto nível sobre "o que construir".
    
- **`@GerenteDeProduto`**: Traduz a visão estratégica em um escopo de produto viável, definindo a proposta de valor, priorizando funcionalidades e gerenciando o _backlog_.
    

#### **Guilda de Análise e Finanças**

- **`@AnalistaFinanceiro`**: Modela a viabilidade financeira dos projetos, estimando custos operacionais (LLMs, infraestrutura) e calculando o potencial de ROI.
    
- **`@AnalistaDeDados`**: Executa análises exploratórias em datasets para extrair insights, validar hipóteses e informar as decisões de produto e de ML.
    

#### **Guilda de Pesquisa e Desenvolvimento**

- **`@AgenteDePesquisa`**: Executa "Deep Research" na web e em outras fontes para coletar e sintetizar informações sobre um determinado tópico, gerando relatórios de pesquisa como artefatos.
    

#### **Guilda de Segurança e Confiança**

- **`@AgenteDeSegurança`**: Um agente especializado que pode ser invocado para escanear código em busca de vulnerabilidades ou analisar a conformidade de um artefato com as políticas de segurança.
    

#### **Guilda de Documentação e Comunicação**

- **`@AgenteDocumentador`**: Especializado em gerar documentação para o usuário final (no diretório `/docs`), como guias e tutoriais, a partir de especificações técnicas.
    

Esta estrutura de guildas é projetada para ser extensível. À medida que a fábrica amadurece, novas guildas (ex: "Guilda de Design de UX", "Guilda de MLOps") e novos agentes podem ser adicionados à "Forja" sem perturbar o núcleo estratégico do `Olympus.core`, garantindo a escalabilidade e a especialização contínua da força de trabalho da **Arandu PoC**.