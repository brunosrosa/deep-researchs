---
sticker: lucide//atom
---
# A Constituição da Fábrica Janus

### Visão, Arquitetura e Blueprint da Prova de Conceito

Versão: 2.0

Data: 30 de Junho de 2025

Guardião: @Janus & O Maestro (Bruno S. Rosa)

## **Parte 1: A Prova de Conceito (O "Porquê")**

Esta Prova de Conceito (PoC) é um empreendimento para validar uma tese central: **é possível que um desenvolvedor solo, aumentado por uma equipe de agentes de IA e uma plataforma de orquestração robusta, possa projetar, construir e lançar produtos de software (SaaS) de alta qualidade em uma velocidade e com uma sofisticação que rivalizam com equipes tradicionais.**

O sucesso desta PoC, denominada **"A Fábrica Janus"**, será medido em três camadas de validação interdependentes:

1. **Validação de Metodologia:** Testar e refinar o fluxo de trabalho de "Desenvolvimento Solo Aumentado por IA". O sucesso é medido pelo **aprendizado contínuo** do Maestro e pela eficiência da colaboração Humano-IA.
    
2. **Validação de Plataforma:** Construir e validar os componentes da "Fábrica Janus". O sucesso é medido pela criação de um `Synapse Engine` e um `Maestro.AI` funcionais, integrados e capazes de executar um ciclo de desenvolvimento básico.
    
3. **Validação de Produto:** Usar a metodologia e a plataforma para construir o **`Recoloca.AI`**. O sucesso é medido pela capacidade de entregar um MVP de alta qualidade que resolva um problema de mercado real.
    

## **Parte 2: Os Pilares da Fábrica (As Visões dos Projetos)**

O ecossistema é composto por quatro projetos distintos, cada um com uma proposta de valor clara:

- ### `Codex Prime Framework`
    
    - **Visão:** O "DNA Organizacional" do nosso ecossistema.
        
    - **Proposta de Valor:** Fornecer um framework de documentação e governança mestre, garantindo que todo novo projeto nasça com uma estrutura estratégica, tática e cultural coesa e de altíssima qualidade. Ele é a fonte da nossa forma de construir.
        
- ### `Synapse Engine`
    
    - **Visão:** A "Plataforma de Memória Cognitiva Temporal".
        
    - **Proposta de Valor:** Servir como o cérebro de conhecimento para todo o ecossistema. Através de uma arquitetura avançada de **GraphRAG**, ele fornecerá contexto preciso, relevante e com entendimento das relações temporais e semânticas, potencializando a capacidade de raciocínio dos agentes.
        
- ### `Maestro.AI`
    
    - **Visão:** O "Sistema Operacional de Governança e Execução".
        
    - **Proposta de Valor:** Ser o cockpit central do Maestro, traduzindo a intenção estratégica humana em fluxos de trabalho executáveis para equipes de agentes de IA. Ele gerencia tarefas, permissões, custos e o ciclo de vida do desenvolvimento, tudo integrado nativamente ao ecossistema GitHub.
        
- ### `Recoloca.AI`
    
    - **Visão:** O "Produto Pioneiro".
        
    - **Proposta de Valor:** Ser o primeiro produto SaaS de alto impacto construído pela "Fábrica Janus". Sua missão é validar todo o ecossistema ao resolver um problema real de mercado – o processo de recolocação profissional no Brasil – de forma inovadora e disruptiva.
        

## **Parte 3: O Mapa do Ecossistema (Componentes e Capacidades)**

Esta tabela representa nosso entendimento mais atual e detalhado, um verdadeiro "estado da nação" de todos os componentes que definem a "Fábrica Janus".

|   |   |   |   |   |
|---|---|---|---|---|
|**Categoria**|**Componente / Definição**|**Opção Primária (Plano A)**|**Opção Secundária (Plano B/C)**|**Status / Justificativa**|
|**Tecnologias**|Linguagem Principal (Backend)|**Python 3.11+**|-|**Definido.** Ecossistema de IA maduro e robusto.|
||Framework da API|**FastAPI**|gRPC (para comunicação interna)|**Definido.** Alta performance e integração com Pydantic.|
||Framework da UI (Cockpit)|**Streamlit** (para MVP)|**React/Next.js** (para Produção)|**Definido.** Streamlit para velocidade de prototipagem, React para uma UI robusta e customizável.|
||Bibliotecas de UI (Produção)|**Aceternity UI** + **Shadcn/ui**|Mantine|**Mapeado.** Combinação que une estética moderna e de alto impacto com um sistema de design sólido.|
||Banco de Dados de Grafos|**Neo4j**|Nebula Graph, TigerGraph|**Mapeado.** Neo4j é uma escolha madura e robusta para implementar a arquitetura GraphRAG.|
||Modelos de LLM Locais|**Phi-3 (Microsoft)**, em formatos **GGUF** ou **EXL2**|Llama 3 8B, Mistral 7B|**Mapeado.** A pesquisa indica o melhor balanço de performance e qualidade para a GPU RTX 2060.|
|**Metodologias**|Modelo de Desenvolvimento|**Desenvolvimento Solo Aumentado por IA ("A Fábrica Janus")**|-|**Definido.** É a filosofia central de todo o empreendimento.|
||Processo de Inovação|**"Duplo Diamante"** (Descobrir -> Definir -> Desenvolver -> Entregar)|Design Sprint|**Definido.** Estrutura o fluxo de trabalho dos agentes, da ideia à entrega.|
||Colaboração de Agentes|**Debate Estruturado (Swarm Intelligence)**|Agentes em sequência|**Definido.** Usar `crews` de agentes para debater tópicos e gerar relatórios consolidados.|
|**Frameworks**|Documentação e Governança|**Codex Prime Framework**|-|**Definido.** Nosso framework proprietário para garantir consistência.|
||Orquestração de Agentes|**LangGraph**|CrewAI, AutoGen|**Definido.** A estrutura de grafo do LangGraph é ideal para os fluxos cíclicos e com estado que planejamos.|
|**Padrões**|Arquitetura de Software|**Microserviços** (`Maestro.AI`, `Synapse Engine`)|Arquitetura Monolítica|**Definido.** Permite desacoplamento e escalabilidade.|
||Interação com Plataforma Externa|**Camada Anti-Corrupção (ACL)** para o VCS|Integração direta com a API do GitHub|**Definido.** Decisão estratégica crucial para mitigar o risco de _vendor lock-in_.|
||Controle de Permissões|**ABAC com Open Policy Agent (OPA)**|RBAC|**Definido.** Permite governança dinâmica e granular das ações dos agentes.|
|**Políticas**|Governança Geral|O documento **`Regras_do_Jogo.md`**|Múltiplos documentos de política|**Definido.** Centraliza a "Constituição" do ecossistema.|
|**Ferramentas**|Plataforma de Versionamento|**GitHub** (Apps, Actions, Projects, Issues)|GitLab|**Definido.** A pesquisa validou o GitHub como a plataforma com o ecossistema mais maduro para o MVP.|
||Plataforma de Kanban|**GitHub Projects** (via API)|OpenProject (alternativa auto-hospedada)|**Definido.** Usar a ferramenta nativa do GitHub simplifica a integração do MVP. A ACL nos permite trocar no futuro.|
||Ambiente de Bootstrap|**TRAE IDE** (Fork do VS Code com Agentes)|-|**Definido.** Nosso ambiente inicial para construir as ferramentas mais avançadas.|
|**Infraestrutura**|Ambiente de Execução|**Híbrido:** Local (GPU RTX 2060) + Cloud (APIs de LLMs)|Apenas Cloud|**Definido.** Oferece um balanço ideal entre custo, velocidade e poder.|
||Orquestração de Infra|**Contêineres Docker** com **Docker Compose** (dev) / **GitHub Actions** (CI/CD)|Kubernetes (para produção futura)|**Definido.** Docker oferece portabilidade e consistência.|
||Gestão de Custos e BI|**Dashboard Dedicado no `Maestro.AI`**|Serviços externos (ex: Langfuse)|**Definido.** É uma funcionalidade central do `Maestro.AI`, que pode se integrar com ferramentas de observabilidade.|

### **Parte 4: O Blueprint da Execução (O Fluxo de Valor)**

Este é o processo de trabalho da "Fábrica Janus", formalizando o modelo "Duplo Diamante" em um fluxo de valor claro e gerenciado por agentes.

1. **FASE 1: Descoberta Estratégica (`@Janus` + Maestro)**
    
    - **Gatilho:** Uma nova hipótese ou problema de alto nível.
        
    - **Atividade:** Sessões de brainstorming e debate para definir o problema a ser resolvido.
        
    - **Saída:** Uma **Proposta de Iniciativa** clara, registrada como uma `Issue` no GitHub com a label `fase: descoberta`.
        
2. **FASE 2: Deliberação e Refinamento (`@Janus` convoca especialistas)**
    
    - **Gatilho:** A Proposta de Iniciativa é aprovada pelo Maestro.
        
    - **Atividade:** O `@Janus` monta uma `crew` de agentes estratégicos (ex: `@PM`, `@Arquiteto`, `@UX_Designer`), que podem usar um `@Deep_Research` para aprofundar o debate.
        
    - **Saída:** Um **Relatório Estratégico Consolidado**, com a solução recomendada, riscos e trade-offs, anexado à `Issue` original.
        
3. **FASE 3: Aprovação Estratégica (Gatekeeper Humano)**
    
    - **Gatilho:** O Relatório Estratégico é apresentado ao Maestro.
        
    - **Atividade:** O Maestro analisa e toma a decisão final, aprovando formalmente na `Issue`.
        
    - **Saída:** Uma **Diretriz Tática Aprovada**.
        
4. **FASE 4: Planejamento Tático (`@Orquestrador`)**
    
    - **Gatilho:** O `@Orquestrador` recebe a Diretriz Tática.
        
    - **Atividade:** Ele quebra o objetivo em Histórias de Usuário, Critérios de Aceite, tarefas técnicas e as organiza no **GitHub Projects**.
        
    - **Saída:** Um **Plano de Execução** detalhado.
        
5. **FASE 5: Execução Operacional (Agentes Especialistas)**
    
    - **Gatilho:** O Plano de Execução é aprovado.
        
    - **Atividade:** Agentes especialistas (`@DevPython`, etc.) pegam as tarefas do Kanban, geram o código/artefato e criam **Pull Requests**.
        
    - **Saída:** **Pull Requests** com código e testes, linkados às `Issues` correspondentes.
        
6. **FASE 6: Validação e Deploy (Agentes de QA + DevOps)**
    
    - **Gatilho:** Um Pull Request é aberto.
        
    - **Atividade:** Agentes de QA rodam testes, code review. Após aprovação humana, agentes de DevOps fazem o merge e o deploy através de um workflow de **GitHub Actions**.
        
    - **Saída:** **Feature em produção**.