---
sticker: lucide//calendar-search
---
# Roadmap da Fábrica Janus: O Médio Prazo

### Fase 2: A Orquestração e a Primeira Sinfonia

**Objetivo Estratégico da Fase:** Construir o "Cockpit Mínimo" (`Maestro.AI MVP`) e validar o fluxo de automação de ponta a ponta, provando que a "Fábrica Janus" é capaz de traduzir uma intenção estratégica em um artefato de código concreto e versionado.

### **Épico 3: Construção do `Maestro.AI` MVP (O Cockpit Mínimo)**

_O maestro precisa de seu pódio e de sua batuta para reger a orquestra._

|   |   |   |   |
|---|---|---|---|
|**Tarefa**|**Descrição**|**Entregável Chave**|**Status**|
|**3.1. Setup da GitHub App**|Registrar e configurar o `Maestro.AI` como uma GitHub App. Definir as permissões iniciais necessárias (leitura de Issues, escrita de comentários, criação de Pull Requests) e configurar os webhooks para escutar eventos.|Uma GitHub App registrada e instalada nos repositórios, com suas credenciais de autenticação seguramente armazenadas.|⏳ Pendente|
|**3.2. Implementação da API (FastAPI)**|Desenvolver o backend do `Maestro.AI` com um endpoint principal para receber e processar os webhooks do GitHub. A lógica inicial deve ser capaz de identificar o tipo de evento (ex: `issue.labeled`).|Um serviço FastAPI conteinerizado que recebe e loga eventos do GitHub com sucesso.|⏳ Pendente|
|**3.3. Desenvolvimento da UI (Streamlit)**|Criar a primeira versão do "Cockpit" usando Streamlit. O objetivo inicial é ter uma interface que exiba um log em tempo real dos eventos recebidos pela API e o status atual dos agentes (ex: "Ocioso", "Processando Tarefa X").|Um dashboard funcional que exibe o fluxo de eventos e o estado básico do sistema.|⏳ Pendente|
|**3.4. Lógica de Orquestração Básica**|Implementar o primeiro fluxo de trabalho em LangGraph. Este fluxo será simples: ao receber um evento de uma `Issue` com a label `@Maestro: executar`, ele deve identificar a tarefa, logar o início do processo e preparar para acionar um agente executor.|Um grafo em LangGraph que modela o fluxo `Evento Recebido -> Análise -> Delegação Pendente`.|⏳ Pendente|

### **Épico 4: A Primeira Sinfonia (Validação do Fluxo End-to-End)**

_A prova da orquestra está na performance. É hora de tocar a primeira nota._

|   |   |   |   |
|---|---|---|---|
|**Tarefa**|**Descrição**|**Entregável Chave**|**Status**|
|**4.1. Integração `Maestro.AI` -> `Synapse Engine`**|Implementar a lógica no `Maestro.AI` para, ao receber uma tarefa, fazer uma chamada de API para o endpoint `/query` do `Synapse Engine` MVP, passando o conteúdo da `Issue` para obter contexto relevante.|O `Maestro.AI` consegue enriquecer o prompt de um agente com o contexto recuperado do `Synapse Engine`.|⏳ Pendente|
|**4.2. Criação do Fluxo de Geração de Código**|Implementar o fluxo completo: uma `Issue` no repositório do `Recoloca.AI` com o título "Criar o `main.py` inicial" é marcada. O `Maestro.AI` recebe o evento, consulta o `Synapse` para obter o template básico de um arquivo FastAPI, aciona um agente `@DevPython` (localmente, via script) que gera o código, e então usa a API do GitHub para criar um **Pull Request** com o novo arquivo.|Um Pull Request aberto automaticamente no repositório do `Recoloca.AI`, contendo o código gerado pelo agente.|⏳ Pendente|
|**4.3. Documentação do Fluxo Inaugural**|Criar um documento no `Codex Prime` do projeto `Maestro.AI` detalhando, com um diagrama Mermaid, como este primeiro fluxo de automação ponta-a-ponta funciona.|Um documento `.md` que serve como referência e aprendizado para a criação de futuros fluxos.|⏳ Pendente|

### **Critérios de Sucesso para o Médio Prazo**

Ao final desta fase, teremos validado a camada de plataforma da nossa PoC:

- ✅ **Orquestração Funcional:** O `Maestro.AI` MVP está operacional, capaz de receber instruções do mundo real (GitHub) e traduzi-las em ações para agentes.
    
- ✅ **Integração Comprovada:** Os três componentes principais (`Maestro.AI`, `Synapse Engine`, Agentes) conseguem se comunicar e colaborar para completar uma tarefa.
    
- ✅ **Validação da Metodologia:** O primeiro ciclo de desenvolvimento totalmente orquestrado foi concluído com sucesso, provando que a "Fábrica Janus" é capaz de produzir código.
    

Com esta fase concluída, teremos uma plataforma funcional sobre a qual poderemos, com confiança, começar a construir as funcionalidades do `Recoloca.AI` na fase de longo prazo.