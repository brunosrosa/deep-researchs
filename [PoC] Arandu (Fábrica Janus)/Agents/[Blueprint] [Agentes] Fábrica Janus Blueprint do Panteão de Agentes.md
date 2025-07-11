---
aliases: []
sticker: lucide//annoyed
---
# Fábrica Janus: Blueprint do Panteão de Agentes

**Versão 1.0**

## 1. Introdução: O Modelo de Orquestração da Fábrica

Bem-vindo ao blueprint da força de trabalho da "*Fábrica Janus*". Este documento serve como o guia mestre para a definição, configuração e interação de todos os agentes de IA que compõem o ecossistema. O objetivo é criar um sistema coeso onde cada agente possui um propósito claro, ferramentas adequadas e um protocolo de comunicação definido, permitindo a orquestração de tarefas complexas, desde a ideação estratégica até a entrega final de artefatos.

O modelo operacional se baseia em uma hierarquia de **Tiers (Camadas)**, garantindo uma clara separação de preocupações e um fluxo de comando lógico.

### O Fluxo de Comando Hierárquico

A orquestração segue um fluxo descendente de delegação e um fluxo ascendente de entrega de valor:

1. **Maestro (Humano):** Define a intenção estratégica de mais alto nível. Inicia o processo com uma questão, uma hipótese ou um objetivo.
    
2. **Tier 0: Agente Fundacional (`@Janus`):** Atua como o "Chefe de Gabinete". Recebe a intenção do Maestro, desafia, refina e a transforma em uma **Diretriz Estratégica** validada. É o guardião do "porquê".
    
3. **Tier 1: Agentes de Síntese e Tática (`@Briefing`, `@Orquestrador`):**
    
    - `@Briefing`: Recebe a Diretriz Estratégica do `@Janus` e a traduz em um **"Briefing de Missão"** estruturado, rico em contexto e livre de ambiguidades. Ele garante que a intenção não se perca na tradução.
        
    - `@Orquestrador`: Recebe o "Briefing de Missão" e o decompõe em um **Plano de Execução Tático**, criando tarefas, épicos e montando as `Crews` (equipes) de agentes especialistas necessárias. É o mestre do "como" e do "quando".
        
4. **Tiers 2-5: Agentes Especialistas e `Crews`:** Grupos de agentes com habilidades específicas (pesquisa, escrita, design, revisão) que são acionados pelo `@Orquestrador` para executar as tarefas do plano. Eles são os mestres do "fazer".
    

**Diagrama do Fluxo de Interação Principal:**

```
[ Maestro (Intenção) ]
       |
       v
[ @Janus (Estratégia) ] ------------> [ Tier 2: Agentes de Inteligência ]
       |                                      ^ (Pedidos de Pesquisa)
       v                                      |
[ @Briefing (Síntese da Missão) ] <----------+ (Relatórios)
       |
       v
[ @Orquestrador (Plano Tático) ]
       |
       v
[ Crews de Agentes Especialistas (Execução) ]
 (Tier 3: Concepção, Tier 4: Criação, Tier 5: Qualidade)
       |
       v
[ Artefato Final ] -> Entregue ao Maestro/Orquestrador
```

## 2. O Panteão de Agentes da Fábrica Janus

A seguir, a lista completa dos agentes propostos, organizados por Tier e função. Cada um deve ter seu próprio arquivo de perfil detalhado, seguindo o modelo de `@Janus` e `@Orquestrador`.

### **Tier 0: Fundacional**

- **`@Janus`**
    
    - **Nome/Título:** Chefe de Gabinete Estratégico & Guardião dos Portões.
        
    - **Missão Principal:** Garantir a clareza e o alinhamento estratégico de toda e qualquer iniciativa, transformando a intenção do Maestro em diretrizes validadas.
        
    - **Ferramentas Essenciais:** Acesso de leitura a todo o `Codex Prime`, Acesso ao `Synapse Engine` (futuro), `Web Search`, Capacidade de invocar `@DeepResearch`.
        
    - **Interage Com:** Maestro (recebe intenção), `@DeepResearch` (solicita pesquisa), `@Briefing` (entrega diretriz).
        

### **Tier 1: Síntese & Tática**

- **`@Briefing`** (O antigo `@Situação`)
    
    - **Nome/Título:** Sintetizador de Missão e Contexto.
        
    - **Missão Principal:** Receber uma diretriz estratégica de alto nível e convertê-la em um "Briefing de Missão" perfeitamente estruturado, detalhado e acionável para o `@Orquestrador`.
        
    - **Ferramentas Essenciais:** Acesso de leitura aos artefatos referenciados na diretriz, templates de briefing pré-definidos.
        
    - **Interage Com:** `@Janus` (recebe diretriz), `@Orquestrador` (entrega briefing).
        
- **`@Orquestrador`**
    
    - **Nome/Título:** Gerente de Projetos Tático & Product Owner.
        
    - **Missão Principal:** Decompor o "Briefing de Missão" em um plano de trabalho granular, gerenciar o `GitHub Projects` e orquestrar as `Crews` de agentes especialistas.
        
    - **Ferramentas Essenciais:** `GitHub API (Read/Write)` para `Projects` e `Issues`, Acesso de leitura a todo o `Codex Prime`, Capacidade de invocar e gerenciar `Crews`.
        
    - **Interage Com:** `@Briefing` (recebe missão), Agentes Especialistas (delega tarefas), Maestro (reporta progresso).
        

### **Tier 2: Inteligência & Pesquisa**

- **`@DeepResearch`**
    
    - **Nome/Título:** Analista de Pesquisa Profunda e Saturação de Contexto.
        
    - **Missão Principal:** Realizar pesquisas extensivas na web e em bases de dados documentais, seguindo um ciclo de refinamento iterativo até atingir a "saturação" de um tópico, e entregar um relatório abrangente e sintetizado.
        
    - **Ferramentas Essenciais:** `Web Search API` (avançada, com múltiplos motores), `Gemini CLI` (para resumos e análises), Acesso ao `Synapse Engine` (futuro), Ferramentas de scraping (com ética), Acesso a APIs de artigos científicos (ex: arXiv).
        
    - **Interage Com:** `@Janus` (recebe pedidos estratégicos), `@Orquestrador` (recebe pedidos táticos).
        
- **`@AnalistaDeMercado`**
    
    - **Nome/Título:** Especialista em Inteligência Competitiva e Tendências.
        
    - **Missão Principal:** Focar a pesquisa em concorrentes, tendências de mercado, análises SWOT e feedback de usuários, gerando insights para a estratégia de produto.
        
    - **Ferramentas Essenciais:** `Web Search API`, Acesso a plataformas de review (G2, Capterra), APIs de notícias de negócios, Ferramentas de análise de redes sociais.
        
    - **Interage Com:** `@EstrategistaDeProduto`, `@Janus`.
        

### **Tier 3: Concepção & Estratégia de Produto**

- **`@EstrategistaDeProduto`** (ou `@ProductManager`)
    
    - **Nome/Título:** Estrategista de Visão de Produto.
        
    - **Missão Principal:** Definir e defender a visão do produto, analisar a viabilidade de features, escrever Requisitos de Produto (PRDs) e garantir que o desenvolvimento esteja alinhado às necessidades do usuário e do negócio.
        
    - **Ferramentas Essenciais:** Acesso aos relatórios do `@AnalistaDeMercado`, Acesso ao `Codex Prime` (especialmente pilar de Produto), Ferramentas de diagramação (para user flows).
        
    - **Interage Com:** `@Janus` (debate estratégico), `@Orquestrador` (refina HUs), `@ProductDesigner` (colabora na solução).
        
- **`@ProductDesigner`**
    
    - **Nome/Título:** Defensor da Experiência do Usuário e Heurísticas.
        
    - **Missão Principal:** Garantir que todos os artefatos e produtos sigam os princípios de UX, usabilidade e design. Analisa fluxos, propõe melhorias de interface (mesmo em documentos) e cria protótipos conceituais.
        
    - **Ferramentas Essenciais:** Acesso a bibliotecas de heurísticas (Nielsen Norman), Ferramentas de design (conceitual), Acesso aos artefatos em desenvolvimento.
        
    - **Interage Com:** `@EstrategistaDeProduto`, `@EscritorTecnico`, `@RevisorCritico`.
        
- **`@ArquitetoDeSolucoes`**
    
    - **Nome/Título:** Arquiteto de Soluções e Viabilidade Técnica.
        
    - **Missão Principal:** Analisar os requisitos de produto e propor uma arquitetura de alto nível (HLD), avaliando trade-offs, tecnologias e impacto no ecossistema existente. Cria os Architecture Decision Records (ADRs).
        
    - **Ferramentas Essenciais:** Acesso ao `Codex Prime` (pilar de Tecnologia), Ferramentas de diagramação (C4 Model), Acesso aos ADRs existentes.
        
    - **Interage Com:** `@Janus` (debate estratégico), `@Orquestrador` (orienta o plano técnico), `@DevSenior` (detalha o LLD).
        

### **Tier 4: Criação & Engenharia de Documentos**

- **`@EscritorTecnico`**
    
    - **Nome/Título:** Especialista em Documentação Clara e Concisa.
        
    - **Missão Principal:** Criar e refatorar a documentação técnica e de produto, garantindo clareza, consistência e aderência aos templates do `Codex Prime`.
        
    - **Ferramentas Essenciais:** `File System Access (Read/Write)`, `markdownlint` (via MCP), Acesso aos templates do `Codex Prime`.
        
    - **Interage Com:** `@Orquestrador` (recebe tarefas), `@RevisorCritico` (envia para revisão).
        
- **`@ArquitetoDeDados`**
    
    - **Nome/Título:** Modelador de Dados e Esquemas.
        
    - **Missão Principal:** Projetar e documentar esquemas de dados, modelos para bancos de dados (SQL/NoSQL/Graph), e garantir a integridade e consistência dos dados que suportarão as aplicações.
        
    - **Ferramentas Essenciais:** `File System Access (Read/Write)`, Ferramentas de modelagem de dados, Conhecimento profundo de YAML/JSON Schema.
        
    - **Interage Com:** `@ArquitetoDeSolucoes`, `@Orquestrador`.
        

### **Tier 5: Qualidade & Revisão**

- **`@RevisorCritico`**
    
    - **Nome/Título:** Crítico Construtivo e Revisor de Qualidade.
        
    - **Missão Principal:** Atuar como o "advogado do diabo" para qualquer artefato criado. Revisa documentos, estratégias e planos em busca de falácias lógicas, ambiguidades, premissas não testadas e falta de clareza.
        
    - **Ferramentas Essenciais:** Acesso de leitura a todos os artefatos em revisão.
        
    - **Interage Com:** `@Orquestrador` (recebe tarefas de revisão), Agentes Criadores (fornece feedback).
        
- **`@Linter`**
    
    - **Nome/Título:** Guardião da Consistência e Padrões.
        
    - **Missão Principal:** Um agente automatizado que valida a conformidade dos documentos Markdown com as regras do `markdownlint` e a estrutura de metadados YAML Front Matter.
        
    - **Ferramentas Essenciais:** `markdownlint CLI` (via MCP), `YAML Parser/Validator` (via MCP).
        
    - **Interage Com:** `@Orquestrador` (integrado no fluxo de CI/CD via GitHub Actions).
        

## 3. Fluxo de Trabalho em Ação: Criando um ADR

Vamos detalhar como esses agentes colaborariam para criar um novo **Architecture Decision Record (ADR)**.

1. **Gatilho (Maestro -> `@Janus`):** "Janus, precisamos decidir se usaremos Neo4j ou um banco relacional com extensões de grafo para a camada principal do Synapse Engine. Analise as implicações."
    
2. **Pesquisa (Janus -> `@DeepResearch`):** `@Janus` solicita ao `@DeepResearch` um relatório comparativo completo sobre "Neo4j vs. PostgreSQL com pg_graph" para casos de uso de RAG semântico, incluindo benchmarks, estudos de caso e análises de custo/complexidade.
    
3. **Debate Estratégico (`@Janus` convoca `Crew`):** Com o relatório em mãos, `@Janus` convoca uma `Crew` temporária com `@ArquitetoDeSolucoes` e `@ArquitetoDeDados` para debater os prós e contras no contexto específico da Fábrica Janus.
    
4. **Decisão e Diretriz (Maestro -> `@Janus` -> `@Briefing`):** O Maestro, com base na recomendação, decide por Neo4j. `@Janus` formaliza a decisão e envia a diretriz para `@Briefing`: "Criar um briefing para a documentação de um ADR que oficializa o uso de Neo4j como a camada de grafo primária do Synapse Engine, com base no relatório X e na ata de decisão Y."
    
5. **Síntese da Missão (`@Briefing` -> `@Orquestrador`):** `@Briefing` cria um documento estruturado contendo o objetivo, o contexto da decisão, os artefatos de referência e os critérios de aceite para o ADR. Ele envia este "Briefing de Missão" para o `@Orquestrador`.
    
6. **Plano Tático (`@Orquestrador`):** `@Orquestrador` recebe o briefing, cria uma nova tarefa no GitHub Projects ("ADR-003: Adoção do Neo4j"), e monta a `Crew` de execução.
    
7. **Execução (`@Orquestrador` -> `Crew`):**
    
    - **Tarefa 1 (para `@EscritorTecnico`):** "Com base no template de ADR e no briefing da missão, redija a primeira versão do ADR-003."
        
    - **Tarefa 2 (para `@ArquitetoDeSolucoes`):** "Forneça os diagramas de arquitetura C4 necessários para ilustrar a integração do Neo4j."
        
    - **Tarefa 3 (para `@RevisorCritico`):** "Após a conclusão da primeira versão, revise o ADR em busca de clareza, justificativas fracas e impactos não considerados."
        
8. **Revisão e Entrega (`Crew` -> `@Orquestrador`):** Os agentes executam suas tarefas. O `@EscritorTecnico` submete o rascunho, o `@ArquitetoDeSolucoes` adiciona os diagramas, e o `@RevisorCritico` fornece feedback. O ciclo se repete até que o artefato seja aprovado pela `Crew`.
    
9. **Validação Final (`@Orquestrador` -> `@Linter`):** O `@Orquestrador` submete o ADR final ao agente `@Linter` para garantir a conformidade com os padrões.
    
10. **Conclusão da Missão (`@Orquestrador` -> Maestro):** O `@Orquestrador` move a tarefa para "Concluído" no Kanban e notifica o Maestro que o ADR-003 foi criado, validado e versionado no `Codex Prime`.
    

## 4. Próximos Passos

1. **Criação dos Perfis:** Utilizar este blueprint para criar os arquivos `.md` de perfil para cada agente listado, detalhando seus prompts de sistema, limitações e protocolos.
    
2. **Configuração no TRAE IDE:** Configurar cada agente no TRAE IDE, associando seus respectivos arquivos de perfil.
    
3. **Implementação dos MCPs:** Configurar os servidores MCP necessários para fornecer as ferramentas essenciais (acesso a arquivos, `markdownlint`, `GitHub API`).
	
4. **Criar Avatares dos Agentes**: Usar algum gerador de imagens para gerar um "Avatar" para cada um dos agentes. Iterar antes para definir o estilo. 
    
5. **Desenvolvimento do LangGraph:** Para os fluxos mais complexos (como o do `@Orquestrador` gerenciando uma `Crew`), começar a modelar esses grafos de agentes usando LangGraph, definindo os nós (agentes) и as arestas (transições de estado).
    

Este documento é um artefato vivo. À medida que a Fábrica Janus evolui, este panteão de agentes deve ser revisitado, refinado e expandido.