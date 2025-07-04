---
sticker: lucide//calendar-search
---
# Roadmap da Fábrica Janus: O Longo Prazo

### Fase 3: A Fábrica em Plena Operação e a Validação do Produto

**Objetivo Estratégico da Fase:** Evoluir os MVPs para sistemas robustos, implementar as funcionalidades avançadas que definem a inteligência do nosso ecossistema e, finalmente, utilizar a fábrica em plena capacidade para construir e validar o `Recoloca.AI` como um produto disruptivo.

### **Épico 5: Evolução do `Synapse Engine` para Memória Cognitiva**

_Transformar o cérebro de uma biblioteca para um pensador._

|   |   |   |   |
|---|---|---|---|
|**Tarefa**|**Descrição**|**Entregável Chave**|**Status**|
|**5.1. Implementação do GraphRAG**|Evoluir a ingestão de dados para não apenas criar vetores, mas também extrair entidades e relações, populando o banco de dados de grafos (Neo4j).|Um pipeline de ingestão que constrói um Grafo de Conhecimento a partir dos documentos do `Codex`.|⏳ Pendente|
|**5.2. Implementação da Busca Híbrida**|Desenvolver a lógica de query para que o `Synapse Engine` possa combinar a busca vetorial com a travessia de grafos, permitindo consultas complexas e multi-hop.|Um endpoint `/query_avancada` que responde a perguntas que exigem o entendimento de relações.|⏳ Pendente|
|**5.3. API de Exposição do Grafo (GraphQL)**|Criar uma API GraphQL que permita ao `Maestro.AI` consultar e explorar a estrutura do Grafo de Conhecimento para fins de visualização e análise.|Uma API GraphQL funcional para o `Synapse Engine`.|⏳ Pendente|

### **Épico 6: Evolução do `Maestro.AI` para Cockpit de Governança Total**

_Dar ao maestro um pódio com visão de 360 graus e controle total sobre a orquestra._

|   |   |   |   |
|---|---|---|---|
|**Tarefa**|**Descrição**|**Entregável Chave**|**Status**|
|**6.1. Desenvolvimento da UI de Produção**|Iniciar a migração da UI do Streamlit para uma stack de produção com **React/Next.js** e as bibliotecas de componentes definidas (**Aceternity UI**, **Shadcn/ui**).|A primeira versão do dashboard do `Maestro.AI` em React, com as funcionalidades básicas do MVP.|⏳ Pendente|
|**6.2. Implementação do Dashboard de Custos e BI**|Desenvolver os painéis que monitoram o consumo de API dos LLMs, o `trust_score` dos agentes e outros KPIs de performance da "Fábrica".|Dashboards funcionais que fornecem uma visão clara da saúde operacional e financeira do ecossistema.|⏳ Pendente|
|**6.3. Integração do Motor de Políticas (ABAC)**|Integrar o `Maestro.AI` com o motor **OPA (Open Policy Agent)**. A lógica de orquestração agora deve consultar o OPA antes de delegar qualquer tarefa a um agente.|Um fluxo de trabalho onde uma ação de um agente é bloqueada ou permitida com base em uma política definida no OPA.|⏳ Pendente|
|**6.4. Visualizador de Grafo de Conhecimento**|Implementar na UI do `Maestro.AI` um componente (usando **React Flow** ou `vis.js`) que consome a API GraphQL do `Synapse Engine` para exibir visualmente o grafo de conhecimento.|Uma tela interativa no cockpit que permite ao Maestro explorar a memória do sistema.|⏳ Pendente|

### **Épico 7: Construção e Validação do `Recoloca.AI` MVP**

_A fábrica está pronta. É hora de construir nosso primeiro carro e provar seu valor na estrada._

|   |   |   |   |
|---|---|---|---|
|**Tarefa**|**Descrição**|**Entregável Chave**|**Status**|
|**7.1. Desenvolvimento do Backend (FastAPI)**|Utilizar o fluxo de trabalho completo da "Fábrica Janus" (`Issue` -> `@Janus` -> `@Orquestrador` -> `@DevPython`) para construir todos os endpoints da API do `Recoloca.AI` (gestão de usuários, Kanban, etc.).|API do `Recoloca.AI` totalmente funcional e testada.|⏳ Pendente|
|**7.2. Desenvolvimento do Frontend (Flutter)**|Utilizar o mesmo fluxo para construir a interface do usuário do `Recoloca.AI` em Flutter, conectando-a à API do backend.|Aplicação web do `Recoloca.AI` funcional e com as principais jornadas do usuário implementadas.|⏳ Pendente|
|**7.3. Integração com o `Synapse Engine`**|Implementar a funcionalidade de "Otimização de CV com IA", onde o `Recoloca.AI` faz uma chamada ao `Synapse Engine` para obter contexto sobre a vaga e, em seguida, a um LLM para gerar sugestões.|A feature principal de IA do produto funcionando de ponta a ponta.|⏳ Pendente|
|**7.4. Lançamento e Coleta de Feedback**|Fazer o deploy da versão MVP do `Recoloca.AI` e iniciar o processo de validação com usuários reais, coletando feedback e métricas de sucesso.|O MVP do `Recoloca.AI` em produção e os primeiros dados de uso sendo coletados.|⏳ Pendente|

### **Critérios de Sucesso para o Longo Prazo (Conclusão da PoC)**

Ao final desta fase, teremos alcançado a visão completa da nossa Prova de Conceito:

- ✅ **Plataforma Completa:** O `Maestro.AI` é um cockpit funcional e o `Synapse Engine` opera como uma memória cognitiva avançada.
    
- ✅ **Metodologia Validada:** O fluxo de trabalho da "Fábrica Janus" foi testado em um projeto real, provando sua eficácia.
    
- ✅ **Produto Entregue:** O MVP do `Recoloca.AI` está no ar, entregando valor a usuários e validando a tese de negócio.
    
- ✅ **Aprendizado Consolidado:** O Maestro possui um profundo conhecimento prático e teórico sobre a construção e orquestração de sistemas de IA complexos.
    

Com a conclusão desta fase, a "Fábrica Janus" estará pronta para o seu próximo desafio: escalar a produção e construir o próximo grande produto.