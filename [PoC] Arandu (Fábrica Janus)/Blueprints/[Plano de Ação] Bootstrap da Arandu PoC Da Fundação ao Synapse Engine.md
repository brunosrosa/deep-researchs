---
aliases: []
sticker: lucide//chevrons-right
---
# **[Plano de Ação] Bootstrap da Arandu PoC: Da Fundação ao Synapse Engine**

Versão: 1.0

Guardião: O Maestro

## **Introdução: O Caminho para a Gênese**

Este documento é o guia prático e sequencial para a fase inicial de construção da **Arandu PoC**. Ele detalha as tarefas, os artefatos e as tecnologias necessárias para estabelecer as fundações do ecossistema, culminando em um `Synapse Engine` funcional.

O objetivo desta fase inicial não é construir todo o sistema, mas sim estabelecer os dois pilares mais críticos: o **conhecimento (`Codex Prime`)** e a **memória (`Synapse Engine`)**. Sem uma fonte da verdade robusta e um motor capaz de raciocinar sobre ela, os agentes não têm sobre o que operar.

Este plano é dividido em três fases sequenciais:

- **Fase 0: A Fundação** - Configuração do ambiente e da estrutura.
    
- **Fase 1: A Gênese do Conhecimento** - Construção do `Codex Prime Framework` v1.0.
    
- **Fase 2: O Despertar da Memória** - Implementação do `Synapse Engine` MVP.
    

## **Fase 0: A Fundação (Duração Estimada: 1-2 dias)**

**Objetivo:** Preparar o terreno. Configurar o ambiente de desenvolvimento, criar a estrutura de repositórios e estabelecer as ferramentas de base para o controle de versão e a colaboração.

### **Tarefa 0.1: Configuração do Ambiente de Desenvolvimento**

- **Objetivo:** Preparar o "cockpit" do Maestro.
    
- **Agente(s) Envolvido(s):** O Maestro.
    
- **Artefatos Gerados:** Ambiente de desenvolvimento local configurado.
    
- **Ações:**
    
    1. Instalar e configurar o **`Trae IDE`** como o ambiente de desenvolvimento principal.
        
    2. Instalar e configurar o **`Git`** e um cliente de sua preferência.
        
    3. Configurar as credenciais de acesso para o GitHub.
        

### **Tarefa 0.2: Criação e Estruturação dos Repositórios**

- **Objetivo:** Estabelecer os "lares" para o código e o conhecimento.
    
- **Agente(s) Envolvido(s):** O Maestro.
    
- **Artefatos Gerados:**
    
    - Repositório `codex-prime-framework` no GitHub.
        
    - Repositório `olympus-core` no GitHub.
        
    - Repositório `olympus-agents` no GitHub.
        
    - Repositório `synapse-engine` no GitHub.
        
- **Ações:**
    
    1. Criar os quatro repositórios no GitHub.
        
    2. Para cada repositório, inicializá-lo com um `README.md` básico, um arquivo `.gitignore` apropriado para Python e uma licença (ex: MIT ou Apache 2.0).
        
    3. Clonar os repositórios para o ambiente de desenvolvimento local.
        

## **Fase 1: A Gênese do Conhecimento (Duração Estimada: 3-5 dias)**

**Objetivo:** Forjar o DNA do ecossistema. Limpar, estruturar e formalizar o `Codex Prime Framework` para que ele possa servir como a fonte da verdade para todos os futuros trabalhos.

### **Tarefa 1.1: Limpeza e Separação de Conceitos**

- **Objetivo:** Garantir que o `Codex Prime` seja um framework genérico e reutilizável.
    
- **Agente(s) Envolvido(s):** O Maestro, atuando como `@ArquitetoDoCodex`.
    
- **Artefatos Gerados:** Um repositório `codex-prime-framework` "limpo", sem dados específicos do `Recoloca.AI`.
    
- **Ações:**
    
    1. Revisar todos os arquivos existentes no `Codex Prime`.
        
    2. Mover qualquer conteúdo, exemplo ou dado que seja específico do projeto `Recoloca.AI` para um diretório de rascunho separado (fora do repositório).
        
    3. Garantir que os arquivos restantes sejam templates genéricos e reutilizáveis.
        

### **Tarefa 1.2: Implementação da Estrutura de Pastas e Nomenclatura**

- **Objetivo:** Aplicar a arquitetura de informação e as convenções de nomenclatura decididas.
    
- **Agente(s) Envolvido(s):** O Maestro, atuando como `@ArquitetoDoCodex`.
    
- **Artefatos Gerados:**
    
    - Estrutura de pastas (`/.codex`, `/docs`, `/src`) implementada no repositório `codex-prime-framework`.
        
    - Todos os arquivos renomeados para seguir o padrão `TIPO-ASSUNTO-vVERSAO.ext`.
        
- **Ações:**
    
    1. Criar a estrutura de pastas principal.
        
    2. Escrever um script simples em Python (a ser colocado em `/src/utils/`) ou usar o `Trae IDE` para renomear em lote todos os arquivos existentes para aderir à nova convenção de nomenclatura.
        

### **Tarefa 1.3: Criação dos Documentos Fundamentais (v1.0)**

- **Objetivo:** Escrever a "Constituição" e os documentos meta que governam a própria fábrica.
    
- **Agente(s) Envolvido(s):** O Maestro, atuando como `@ArquitetoDoCodex` e `@RevisorDeQualidade`.
    
- **Artefatos Gerados:**
    
    - `/.codex/CONSTITUICAO-PRINCIPIOS_FUNDAMENTAIS-v1.0.md`
        
    - `/.codex/SOP-CRIACAO_DE_SOP-v1.0.md`
        
    - `/.codex/PERSONA-ARQUITETO_DO_CODEX-v1.0.yaml` (e para os outros agentes do "Time de Gênese").
        
    - `README.md` principal do `codex-prime-framework` totalmente detalhado, explicando a filosofia e como contribuir.
        
- **Ações:**
    
    1. Escrever o conteúdo para cada um desses documentos essenciais, utilizando o formato **Markdown com Frontmatter YAML**.
        
    2. Revisar criticamente cada documento, garantindo clareza e completude.
        
    3. Após a conclusão, fazer o _commit_ e criar a _tag_ `v1.0.0` no repositório `codex-prime-framework`.
        

## **Fase 2: O Despertar da Memória (Duração Estimada: 5-8 dias)**

**Objetivo:** Construir a primeira versão funcional (MVP) do `Synapse Engine`, capaz de ingerir os documentos do `Codex Prime`, construir um grafo de conhecimento e responder a consultas básicas.

### **Tarefa 2.1: Provisionamento da Infraestrutura de Dados**

- **Objetivo:** Configurar os bancos de dados e o armazenamento de objetos que sustentarão o `Synapse Engine`.
    
- **Agente(s) Envolvido(s):** O Maestro, atuando como `@EngenheiroBootstrap`.
    
- **Artefatos Gerados:**
    
    - Containers Docker para `MinIO`, `PostgreSQL` e `Neo4j` em execução.
        
    - Um arquivo `docker-compose.yml` no repositório `synapse-engine` para orquestrar a infraestrutura local.
        
- **Tecnologias:** `Docker`, `Docker Compose`.
    
- **Ações:**
    
    1. Criar um `docker-compose.yml` que defina os serviços para `MinIO`, `PostgreSQL` (com a extensão `pgvector`) e `Neo4j`.
        
    2. Configurar os volumes persistentes para garantir que os dados não sejam perdidos ao reiniciar os containers.
        
    3. Iniciar os serviços e verificar se todos estão acessíveis.
        

### **Tarefa 2.2: Implementação do Pipeline de Ingestão (ETL)**

- **Objetivo:** Construir o pipeline que lê os documentos do `Codex`, extrai conhecimento e popula o grafo.
    
- **Agente(s) Envolvido(s):** O Maestro, atuando como `@EngenheiroBootstrap`.
    
- **Artefatos Gerados:**
    
    - Um conjunto de scripts Python no repositório `synapse-engine` para o pipeline de ETL.
        
- **Tecnologias:** `Python`, `LlamaIndex`, `Unstructured.io`.
    
- **Ações:**
    
    1. **Extração (E):** Escrever um script que monitora o repositório `codex-prime-framework` em busca de mudanças (ou que possa ser acionado manualmente). Este script lê os arquivos `.md` e `.yaml`.
        
    2. **Transformação (T):** Utilizar o `Unstructured.io` para limpar o conteúdo do Markdown. Em seguida, usar o `LlamaIndex` (especificamente o `GraphRAGExtractor` ou similar) para passar o texto limpo por um LLM e extrair as entidades e relações (tripletas).
        
    3. **Carga (L):** Escrever a lógica para se conectar ao `Neo4j` e ao `PostgreSQL`. Usar o `LlamaIndex` (`Neo4jGraphStore`, `PgVectorStore`) para carregar as tripletas extraídas no `Neo4j` e os embeddings dos chunks de texto no `PostgreSQL`.
        

### **Tarefa 2.3: Criação da API de Consulta**

- **Objetivo:** Expor o conhecimento do `Synapse Engine` através de uma API para que os futuros agentes possam consumi-lo.
    
- **Agente(s) Envolvido(s):** O Maestro, atuando como `@EngenheiroBootstrap`.
    
- **Artefatos Gerados:**
    
    - Uma aplicação de API básica no repositório `synapse-engine`.
        
- **Tecnologias:** `Python`, `FastAPI`, `GraphQL (Strawberry)`.
    
- **Ações:**
    
    1. Configurar um projeto `FastAPI`.
        
    2. Implementar um endpoint `GraphQL`.
        
    3. Criar um _query resolver_ básico que recebe uma pergunta em linguagem natural.
        
    4. Este _resolver_ utiliza o `LlamaIndex` para executar uma consulta híbrida (vetorial + grafo) no `Neo4j` e `PostgreSQL`.
        
    5. A consulta recupera o contexto relevante e o passa para um LLM para gerar a resposta final.
        
    6. A API retorna a resposta gerada.
        

### **Tarefa 2.4: Teste de Validação End-to-End**

- **Objetivo:** Verificar se todo o fluxo, desde o documento no `Codex` até a resposta da API, está funcionando.
    
- **Agente(s) Envolvido(s):** O Maestro.
    
- **Artefatos Gerados:** Um conjunto de testes ou um notebook de validação.
    
- **Ações:**
    
    1. Executar o pipeline de ingestão para processar os documentos do `Codex Prime v1.0`.
        
    2. Usar uma ferramenta de cliente de API (como o Postman ou um script Python) para enviar uma consulta à API do `Synapse Engine`. A consulta deve ser uma pergunta cuja resposta esteja contida nos documentos fundamentais (ex: "Quais são os princípios da Constituição?").
        
    3. Validar se a resposta recebida é precisa e contextualizada.
        

Ao final desta fase, a fundação da **Arandu PoC** estará completa. Você terá um `Codex` estruturado e um `Synapse Engine` funcional, criando a plataforma de conhecimento necessária para começar a construir e orquestrar os primeiros agentes inteligentes no `Olympus`.