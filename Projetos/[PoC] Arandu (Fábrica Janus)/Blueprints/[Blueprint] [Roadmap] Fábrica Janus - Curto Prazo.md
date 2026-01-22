---
aliases: []
sticker: lucide//calendar-search
---
# Roadmap da Fábrica Janus: O Curto Prazo

### Fase 1: A Fundação da Governança e da Memória

**Objetivo Estratégico da Fase:** Estabelecer a "Constituição" do nosso ecossistema e construir o "Cérebro Mínimo" (`Synapse Engine MVP`), criando uma base sólida, configurar o ambiente de desenvolvimento e os agentes iniciais para que estejam prontos para a fase de construção. Então governado antes de iniciar a orquestração complexa. 

### **Épico 0: Fundação do Ambiente de Desenvolvimento e Agentes Iniciais**

_O artesão deve primeiro afiar suas ferramentas e preparar sua bancada._

| Tarefa                                                | Descrição                                                                                                                                                                                                   | Entregável Chave                                                                                                | Status     |
| ----------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------- | ---------- |
| **0.1. Configuração do Ambiente TRAE IDE**            | Instalar e configurar as regras (`user_rules.md`, `project_rules.md`) no diretório `.trae` para garantir que o ambiente de desenvolvimento responda à nossa "Constituição".                                 | TRAE IDE configurado e respondendo conforme as regras globais e de projeto.                                     | ⏳ Pendente |
| **0.2. Criação dos Perfis de Agente Iniciais**        | Criar os arquivos `.md` que definem os Perfis e Meta-Prompts para os agentes `@Janus` e `@Orquestrador` dentro do `Codex Prime`, para que possamos começar a usá-los no TRAE IDE.                           | Artefatos `janus_profile.md`, `janus_meta_prompt.md` e os equivalentes para o `@Orquestrador` criados e salvos. | ⏳ Pendente |
| **0.3. Desenvolvimento do MCP para `Synapse Engine`** | Criar um "Model Context Protocol" (MCP) – na prática, uma ferramenta/script simples em Python – que o agente no TRAE IDE possa invocar. Este MCP irá se conectar à API do `Synapse Engine` MVP.             | Um script executável que recebe uma string de busca e retorna o contexto recuperado do `Synapse Engine`.        | ⏳ Pendente |
| **0.4. Integração dos Agentes com Ferramentas**       | Refinar os meta-prompts dos agentes (`@Janus`, etc.) com instruções claras sobre **quando e como** utilizar as ferramentas disponíveis, como o novo MCP do `Synapse Engine`, para enriquecer suas análises. | Meta-prompts atualizados com diretivas de uso de ferramentas.                                                   | ⏳ Pendente |

### **Épico 1: Solidificação do `Codex Prime Framework`**

_A fábrica precisa de seus blueprints mestres antes de construir qualquer máquina._

|   |   |   |   |
|---|---|---|---|
|**Tarefa**|**Descrição**|**Entregável Chave**|**Status**|
|**1.1. Finalizar Artefatos de Governança**|Criar as versões finais e completas dos seguintes templates no repositório do `Codex Prime`: `Regras_do_Jogo.md`, `README.md`, `CHANGELOG.md`, `CONTRIBUTING.md`, e o template de `RFC.md`.|Documentos `.md` finalizados e commitados.|⏳ Pendente|
|**1.2. Padronização de Nomenclatura**|Executar o script para renomear todos os arquivos e pastas do `Codex Prime` para o padrão `MAIUSCULA_COM_UNDERLINE`, garantindo consistência total.|Estrutura de arquivos padronizada.|⏳ Pendente|
|**1.3. Setup dos Repositórios**|Criar os 4 repositórios no GitHub (`Codex-Prime`, `Synapse-Engine`, `Maestro-AI`, `Recoloca-AI`). Aplicar o `.gitignore` padrão em cada um. Instanciar a pasta `/docs` em cada projeto com os templates do `Codex Prime`.|4 repositórios no GitHub, estruturados e prontos.|⏳ Pendente|

### **Épico 2: Construção do `Synapse Engine` MVP**

_O cérebro precisa aprender a lembrar antes de poder raciocinar._

|   |   |   |   |
|---|---|---|---|
|**Tarefa**|**Descrição**|**Entregável Chave**|**Status**|
|**2.1. Setup da Infraestrutura de Dados**|Criar o `docker-compose.yml` para o `Synapse Engine` que inicia um serviço **PostgreSQL com a extensão `pgvector`** e um serviço **Neo4j**.|Ambiente de banco de dados local, funcional e conteinerizado.|⏳ Pendente|
|**2.2. Implementação do Endpoint `/ingest`**|Desenvolver a lógica em FastAPI para um endpoint que recebe um documento de texto (`.md`), o processa com LlamaIndex (chunking, embedding) e o indexa tanto no `pgvector` (para busca semântica) quanto no **Neo4j** (criando nós básicos para o documento e suas seções).|Um endpoint `/ingest` funcional que popula ambos os bancos de dados.|⏳ Pendente|
|**2.3. Implementação do Endpoint `/query` (RAG Vetorial)**|Desenvolver a lógica para um endpoint que recebe uma pergunta em linguagem natural, busca os `chunks` mais relevantes no `pgvector` e os retorna.|Um endpoint `/query` que executa uma busca vetorial simples com sucesso.|⏳ Pendente|
|**2.4. Testes de Integração Básicos**|Criar testes `pytest` que validem o fluxo completo: ingestão de um documento de teste e verificação de que uma query relevante retorna os chunks esperados.|Suíte de testes passando, garantindo a funcionalidade do MVP.|⏳ Pendente|

### **Critérios de Sucesso para o Curto Prazo**

Ao final desta fase, teremos alcançado os seguintes marcos críticos:

- ✅ **Governança Estabelecida:** O `Codex Prime Framework` está completo e instanciado, servindo como a "Constituição" para todos os futuros desenvolvimentos.
    
- ✅ **Memória Funcional:** O `Synapse Engine` é capaz de aprender (ingestão) e lembrar (query vetorial), provando que nossa arquitetura de dados unificada é viável.
    
- ✅ **Ambiente Preparado:** Todos os repositórios estão configurados e prontos para a próxima fase de desenvolvimento, que será o `Maestro.AI`.
    

Este roadmap foca em criar uma base sólida e pragmática, mitigando os maiores riscos técnicos (infraestrutura de dados, governança) antes de avançarmos para a complexidade da orquestração de agentes.