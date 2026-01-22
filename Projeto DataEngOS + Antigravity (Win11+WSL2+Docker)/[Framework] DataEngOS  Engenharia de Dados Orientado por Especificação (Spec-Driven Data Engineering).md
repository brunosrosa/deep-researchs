---
aliases: []
---
# DataEngOS: Framework de Engenharia de Dados Orientado por Especificação (Spec-Driven Data Engineering)

Este relatório detalha a arquitetura conceitual e estrutural do **DataEngOS**, um _fork_ filosófico do DesignOS adaptado para o ciclo de vida de dados. O objetivo é transformar o desenvolvimento de dados de um processo artesanal de escrita de scripts (SQL/Python) para um processo de **design estruturado**, onde Agentes de IA orquestram a implementação baseados em especificações rigorosas, governança e contratos de dados.

---

## 1. Filosofia e Conceito Central

Assim como o DesignOS impede que desenvolvedores frontend escrevam CSS aleatório exigindo primeiro um "Design System", o **DataEngOS** impede a criação de "Pipeline Spaghetti" exigindo primeiro uma "Especificação de Produto de Dados".

A premissa central é o **Spec-Driven Development (Desenvolvimento Orientado por Especificação)**. O código (dbt models, DAGs do Airflow, Terraform) é meramente um artefato derivado de uma especificação bem definida (YAML/Markdown) que o agente lê e executa.

### Comparativo de Adaptação: DesignOS vs. DataEngOS

|**Componente DesignOS (Frontend)**|**Conceito Equivalente no DataEngOS**|**Função no Novo Sistema**|
|---|---|---|
|**Product Vision**|**Data Product Canvas**|Define o valor de negócio, consumidores, SLAs (latência) e SLOs (qualidade).|
|**Design System** (Cores, Fontes)|**Governance System**|Define padrões de nomenclatura, tags de PII, arquitetura de camadas (Bronze/Silver/Gold) e regras de linting.|
|**Data Model** (Entidades UI)|**Semantic Layer & Contracts**|Define os Contratos de Dados (ODCS), esquemas de entrada/saída e métricas oficiais.|
|**Section Design** (Telas)|**Pipeline Topology**|Define o fluxo lógico de dados (DAG), dependências e estratégias de particionamento.|
|**Export** (React Components)|**Artifact Generation**|A saída final: Código SQL (dbt), Python (Airflow) e IaC (Terraform).|

---

## 2. Arquitetura de Diretórios (O "Kernel" do Sistema)

A estrutura de pastas não é apenas organizacional; é a **memória de longo prazo** do Agente. Ela deve ser legível por máquina e estritamente padronizada.

DataEngOS-Project/

├──.agent-os/ # Configurações do framework de agentes

│ ├── directives/ # Prompts de sistema ("Você é um Data Architect...")

│ └── workflows/ # Definições de passos (shape-spec, build-pipeline)

│

├── specs/ # CAMADA DE PLANEJAMENTO (A "Verdade")

│ ├── product-canvas/ # O "Porquê"

│ │ ├── vision.md # Objetivos, Stakeholders e KPIs

│ │ └── slas.yaml # Latência: "Real-time" vs "D-1", Frescura: "6h"

│ │

│ ├── governance/ # O "Design System" de Dados

│ │ ├── naming-conventions.json # Ex: { "staging": "stg_", "marts": "fct_" }

│ │ ├── classification.yaml # Regras de PII (Ex: email -> hash_sha256)

│ │ └── stack-standards.md # "Use CTEs, não subqueries aninhadas."

│ │

│ ├── contracts/ # Interfaces Rígidas (Entrada/Saída)

│ │ ├── inputs/ # Schemas esperados das fontes (Raw)

│ │ └── outputs/ # O que entregamos ao BI/App (Marts)

│ │

│ └── pipelines/ # Arquitetura Lógica

│ └── churn-prediction/ # Domínio específico

│ ├── flow.mermaid # Grafo de dependência visual

│ └── logic.md # Narrativa da transformação em linguagem natural

│

├── implementation/ # CAMADA DE RASCUNHO (Workspace do Agente)

│ ├── dbt/ # Modelos em desenvolvimento

│ ├── airflow/ # DAGs em desenvolvimento

│ └── tests/ # Testes de unidade e qualidade

│

└── exports/ # CAMADA DE PRODUÇÃO (Artifacts finais)

├── warehouse/ # Pacote dbt pronto para deploy

└── orchestration/ # DAGs validados e versionados

---

## 3. Detalhamento dos Módulos do DataEngOS

### 3.1. Data Product Canvas (`specs/product-canvas/`)

Substitui a "Visão do Produto". Antes de escrever SQL, o agente deve entrevistar o usuário para preencher este canvas.

- **Arquivo `vision.md`**:
    
    - _Domain_: (ex: Financeiro, Marketing)
        
    - _Consumer Persona_: (ex: CFO, Cientista de Dados)
        
    - _Business Question_: "Qual é a taxa de churn por região?"
        
- **Arquivo `slas.yaml`**:
    
    - Define a frequência de atualização. O agente usará isso para decidir entre um _Materialized View_ (mais rápido, mais caro) ou uma _Table_ com atualização diária.
        

### 3.2. Governance System (`specs/governance/`)

Substitui o "Design System". É a fonte da verdade estética e estrutural do código.

- **Padronização Léxica**: O arquivo `naming-conventions.json` dita que toda tabela fato deve começar com `fct_`. Se o agente gerar `tb_sales`, o linter do DataEngOS rejeita automaticamente.
    
- **Segurança como Código**: `classification.yaml` define que campos marcados como `pii: true` devem ser automaticamente envolvidos em funções de mascaramento (ex: `SHA256(email)`) durante a geração do código dbt na camada _Silver_.
    

### 3.3. Semantic Layer & Contracts (`specs/contracts/`)

Substitui o modelo de dados simples. Utiliza o padrão **ODCS (Open Data Contract Standard)**.

- **Input Contract**: Define o que esperamos receber da API/Banco de Origem. Se o schema mudar, o pipeline quebra conscientemente ("Fail Fast").
    
- **Output Contract**: Define exatamente o que o Dashboard ou Modelo de ML espera receber. O agente usa isso para escrever os testes `dbt` (`not_null`, `unique`, `accepted_values`) automaticamente.
    

---

## 4. O Fluxo de Trabalho do Agente (Workflow)

O DataEngOS opera em ciclos de refinamento, não em "prompt único".

### Fase 1: `shape-spec` (Modelagem)

Humano: "Quero um dashboard de vendas por estado."

Agente (DataEngOS):

1. Consulta `governance/` para saber onde buscar dados de vendas.
    
2. Lê `contracts/inputs/` para ver se os dados existem.
    
3. Gera um arquivo `specs/pipelines/sales-geo/logic.md` descrevendo a estratégia: "Vou fazer join da `stg_orders` com `stg_customers` na chave `customer_id`."
    

### Fase 2: `write-contract` (Contrato)

**Agente:**

1. Escreve o arquivo YAML do contrato de saída (`specs/contracts/outputs/sales_by_geo.yaml`).
    
2. Define os tipos de dados e testes de qualidade esperados.
    
3. Pede aprovação humana: "Este esquema atende sua necessidade?"
    

### Fase 3: `implement-stack` (Codificação)

**Agente:**

1. **Geração dbt:** Cria os arquivos `.sql` na pasta `implementation/dbt/`, aplicando as regras de nomenclatura e estilo do `governance/`.
    
2. **Geração Airflow:** Cria o arquivo Python do DAG, configurando o agendamento baseado no `slas.yaml` (ex: se SLA é 1h, schedule=`0 * * * *`).
    
3. **Auto-Correção:** O agente roda `dbt compile`. Se houver erro SQL, ele lê o erro, ajusta o código e tenta novamente (Loop RLM).
    

---

## 5. Hacks e Otimizações para Agentes de Dados

Para tornar o DataEngOS eficiente no Antigravity:

1. **Padrão RLM (Recursive Language Model):**
    
    - Não alimente o agente com todo o banco de dados. Use um script que permita ao agente executar `get_table_schema(table_name)` sob demanda. Isso simula "memória infinita" sem estourar o contexto.
        
2. **TOON (Token-Oriented Object Notation):**
    
    - Ao passar schemas grandes ou amostras de dados para o agente, converta JSON/CSV para TOON (formato otimizado que remove aspas e vírgulas). Isso economiza até 40% de tokens, permitindo que o agente analise estruturas de dados maiores.
        
3. **Template de "Gatekeeper":**
    
    - Crie um script de validação que roda antes do agente finalizar a tarefa. Ele verifica:
        
        - Existe documentação no YAML?
            
        - Existe owner definido?
            
        - O código compila?
            
    - Se falhar, o agente é forçado a corrigir antes de pedir sua revisão.
        

Este arcabouço transforma a Engenharia de Dados de uma tarefa de "encanamento" para uma tarefa de "arquitetura", onde a IA faz o trabalho pesado de codificação, mas sempre dentro das grades de proteção definidas pelo seu **DataEngOS**.