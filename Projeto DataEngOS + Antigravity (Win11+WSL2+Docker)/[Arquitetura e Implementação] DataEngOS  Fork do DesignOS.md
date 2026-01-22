---
aliases: []
---
# Arquitetura e Implementação do DataEngOS: Um Relatório Técnico Sobre Engenharia de Dados Agêntica no Google Antigravity

## 1. Introdução: A Mudança de Paradigma na Engenharia de Dados

A engenharia de dados encontra-se num ponto de inflexão crítico. Tradicionalmente, o desenvolvimento de pipelines de dados (ETL/ELT), a modelagem de data warehouses e a criação de dashboards têm sido processos artesanais, caracterizados por uma elevada carga de código repetitivo ("boilerplate") e uma dependência significativa de conhecimento tribal sobre esquemas e regras de negócio. Enquanto o desenvolvimento de software frontend evoluiu através de sistemas de design rigorosos (como o DesignOS da Builder Methods) que padronizam a criação de interfaces antes de qualquer código ser escrito, a engenharia de dados frequentemente salta a fase de especificação detalhada, mergulhando diretamente na escrita de SQL ou Python.1

Este relatório propõe a criação do **DataEngOS** — um _fork_ conceptual e estrutural do DesignOS, adaptado especificamente para o ciclo de vida dos dados. A premissa central é que a natureza determinística dos pipelines de dados torna este domínio um candidato ainda mais forte para a automação agêntica do que o design de interfaces gráficas. Ao contrário da subjetividade estética de uma UI, os dados obedecem a esquemas rígidos, contratos definidos e topologias de grafos direcionados acíclicos (DAGs).3

Para concretizar esta visão, analisamos a configuração avançada do **Google Antigravity**, um ambiente de desenvolvimento integrado (IDE) "agent-first", como o sistema operativo de execução. Este documento detalha não apenas a adaptação teórica, mas fornece um manual exaustivo para configurar o Antigravity em ambientes Windows 11 complexos (com WSL2 e Docker), integrar o **Model Context Protocol (MCP)** para dar aos agentes "sentidos" sobre a infraestrutura de dados, e aplicar estratégias de eficiência como **Modelos de Linguagem Recursivos (RLM)** para otimizar custos e contexto.5

---

## 2. Deconstrução e Adaptação: Do DesignOS ao DataEngOS

O DesignOS funciona sob um princípio simples: não se escreve código até que o "produto" esteja totalmente especificado através de uma interação guiada com um agente. Para criar o DataEngOS, devemos mapear os primitivos de design visual para primitivos de arquitetura de dados.

### 2.1 Análise Comparativa de Estruturas

A tabela seguinte ilustra o mapeamento direto entre os componentes do DesignOS e a nova arquitetura DataEngOS.

|**Componente DesignOS**|**Função Original (Frontend)**|**Componente DataEngOS**|**Função Adaptada (Dados)**|
|---|---|---|---|
|**Product Vision**|Definir o problema do utilizador e a estética da app.|**Data Product Strategy**|Definir o valor de negócio, consumidores, SLAs e SLOs do produto de dados.7|
|**Data Model**|Modelo ER para a aplicação web.|**Schema & Contracts**|Definição rigorosa de esquemas (Bronze/Silver/Gold), Contratos de Dados e linhagem.9|
|**Design System**|Paleta de cores, tipografia, espaçamento.|**Governance System**|Convenções de nomenclatura, tags de conformidade (GDPR/PII), padrões de modelagem (Kimball/Vault).3|
|**Section Design**|Fluxos de ecrã e wireframes.|**Pipeline Topology**|Arquitetura do DAG no Airflow, dependências de tarefas e estratégias de partição.4|
|**Export**|Componentes React + Tailwind.|**Artifact Generation**|Código dbt (SQL/Jinja), DAGs Python, definições de Dashboards (LookML/JSON).|

### 2.2 Product Planning: A Estratégia do Produto de Dados

No DesignOS, a fase de planeamento interroga o utilizador sobre "quem é o cliente". No DataEngOS, o agente deve ser configurado para interrogar o engenheiro sobre o **Contrato de Dados**. Em vez de produzir um documento de visão genérico, o objetivo desta fase é gerar um ficheiro de especificação estruturado, como um YAML compatível com o **Open Data Product Standard (ODPS)** ou o **Data Product Descriptor Specification (DPDS)**.7

Implicações Práticas:

O agente não deve perguntar "Que cor preferes?", mas sim:

- "Qual é a latência máxima aceitável para este dashboard?"
    
- "Quem são os proprietários (Data Owners) deste domínio?"
    
- "Existem restrições de soberania de dados ou PII que exijam mascaramento na camada Silver?"
    

Esta mudança de contexto transforma o agente de um "codificador" para um "arquiteto de soluções", garantindo que nenhum código é gerado sem que os requisitos não-funcionais (segurança, observabilidade) estejam definidos.

### 2.3 Design System: Governação como Código

O equivalente a um "Sistema de Design" em dados é um "Sistema de Governação". No DesignOS, o sistema garante que todos os botões têm o mesmo raio de curvatura. No DataEngOS, o sistema garante que:

1. Todas as tabelas de staging começam com o prefixo `stg_`.
    
2. Todas as colunas de data/hora terminam em `_at` (timestamp) ou `_date` (data).
    
3. Todas as transformações seguem uma estrutura CTE (Common Table Expression) padronizada para legibilidade.
    

Estratégia de Implementação:

O "prompt de sistema" do DataEngOS deve ser carregado com um guia de estilo dbt (como o da dbt Labs ou GitLab). O agente deve recusar-se a gerar código que viole estas regras, tal como o DesignOS se recusa a gerar CSS fora da paleta de cores definida. Isto é crucial para manter a sanidade em projetos com múltiplos colaboradores na pasta DEV-Projects.10

### 2.4 Section Design: Arquitetura de Pipelines e Dashboards

Nesta fase, o DataEngOS substitui o conceito de "telas" por "camadas de transformação" e "vistas de apresentação".

- **Camada de Ingestão (Bronze):** O agente desenha a estratégia de extração (Incremental vs. Full Load) e define os sensores do Airflow necessários para detetar a chegada de dados.
    
- **Camada de Refinamento (Silver):** O agente planeia a lógica de desduplicação e limpeza. Aqui, o uso de RLM (discutido mais à frente) é vital para analisar amostras de dados reais e sugerir regras de qualidade (ex: `expect_column_values_to_be_unique`).
    
- **Camada de Apresentação (Gold/Dashboard):** O agente define as métricas agregadas. Para dashboards, o DataEngOS deve gerar especificações agnósticas (como código JSON para Superset ou LookML), focando-se nas métricas de negócio (KPIs) definidas na fase de estratégia.12
    

### 2.5 Export: A Fábrica de Código

A fase final do DataEngOS não é apenas "escrever ficheiros". É a orquestração de múltiplos sub-agentes especializados:

1. **Agente dbt:** Especialista em SQL e Jinja. Gera modelos, ficheiros `schema.yml` e testes genéricos.
    
2. **Agente Airflow:** Especialista em Python e APIs de orquestração. Gera o grafo de dependências e configura operadores de retry/alerting.
    
3. **Agente de Documentação:** Gera o `README.md` e preenche as descrições de colunas no dbt, garantindo que o catálogo de dados (ex: OpenMetadata) seja populado automaticamente.13
    

---

## 3. O Ambiente de Execução: Google Antigravity Profundo

O Google Antigravity não é apenas mais um editor; é uma plataforma desenhada para "pair programming" assíncrono com agentes de IA. No entanto, a sua configuração padrão é muitas vezes insuficiente para a complexidade da pilha de dados moderna, que exige interação profunda com sistemas Linux (via WSL2) e contentores Docker.

### 3.1 Arquitetura de Integração Windows 11 / WSL2

Para um desenvolvimento de dados sério no Windows, o WSL2 (Windows Subsystem for Linux) é obrigatório. O Antigravity, sendo um fork do VS Code, possui capacidades de conexão remota, mas a sua instalação inicial frequentemente falha em configurar corretamente a ponte entre o host Windows e a distribuição Linux (Ubuntu/Debian).14

#### 3.1.1 Correção do Launcher e Scripts de Apoio

Um problema documentado na comunidade é que o instalador do Antigravity por vezes omite scripts auxiliares necessários para a extensão "Remote - WSL" funcionar, ou aponta para IDs de extensão incorretos.

**Procedimento de Correção Avançada:**

1. **Localizar o Executável:** Navegue até `%LOCALAPPDATA%\Programs\Antigravity\bin`.
    
2. **Editar o Script `antigravity`:** Abra o ficheiro (sem extensão, mas é um script shell/batch) num editor de texto. Verifique a variável `WSL_EXT_ID`.
    
    - _Incorreto:_ `ms-vscode-remote.remote-wsl` (Aponta para a extensão da Microsoft, que pode não estar autorizada no contexto do Antigravity).
        
    - _Correção:_ Altere para `google.antigravity-remote-wsl` se a extensão nativa da Google estiver presente, ou assegure-se de que os scripts de servidor (`wslCode.sh`, `wslDownload.sh`) estão presentes na pasta `resources/app/extensions/.../scripts`. Se faltarem, copie-os de uma instalação funcional do VS Code.15
        
3. **Symlink no WSL:** Dentro do terminal WSL (Ubuntu), crie um link simbólico para poder chamar o IDE diretamente da pasta do projeto:
    
    Bash
    
    ```
    sudo ln -s /mnt/c/Users/rosas/AppData/Local/Programs/Antigravity/bin/antigravity /usr/local/bin/agy
    ```
    
    Isto permite executar `agy.` dentro da pasta `~/DEV-Projects` no Linux, abrindo o projeto no contexto correto.
    

#### 3.1.2 A Solução de Rede "Mirrored"

Agentes de IA no Antigravity frequentemente precisam de aceder a portas locais para validar o seu trabalho (ex: verificar se o Airflow Webserver está a correr em `localhost:8080`). No modo de rede padrão do WSL2 (NAT), o "localhost" do Windows e do Linux são distintos, o que confunde o agente.

Configuração Obrigatória (.wslconfig):

Crie ou edite o ficheiro C:\Users\rosas\.wslconfig com o seguinte conteúdo:

Ini, TOML

```
[wsl2]
networkingMode=mirrored
dnsTunneling=true
autoProxy=true
firewall=true
```

A diretiva `networkingMode=mirrored` (disponível nas versões recentes do Windows 11) unifica as interfaces de rede. Isto significa que se o agente no Antigravity (Windows) tentar aceder a um serviço Docker (Linux), ele conseguirá fazê-lo via `localhost` sem túneis complexos ou configurações de proxy.14

### 3.2 Integração Profunda com Docker

O DataEngOS depende do Docker para subir bases de dados locais (Postgres), orquestradores (Airflow) e ferramentas de transformação. O agente precisa de mais do que apenas "ver" o código; precisa de controlar a infraestrutura.

**Passos de Configuração:**

1. **Docker Desktop com WSL2 Backend:** Nas definições do Docker Desktop (Windows), garanta que a opção "Use the WSL 2 based engine" está ativa e que a sua distribuição (ex: Ubuntu-22.04) está selecionada em "Resources > WSL Integration".
    
2. **Permissões de Socket:** Dentro do WSL, adicione o seu utilizador ao grupo docker: `sudo usermod -aG docker $USER`. Isto permite que o agente execute comandos `docker` sem `sudo`, evitando que o agente bloqueie à espera de uma password.
    
3. **Montagem de Volumes:** Como os seus projetos estão em `C:\Users\rosas\OneDrive...`, estes são montados em `/mnt/c/...` no WSL. O Docker no WSL2 lida bem com isto, mas o desempenho de I/O pode ser lento para muitas pequenas operações de ficheiros (comum em `node_modules` ou `venv`).
    
    - _Recomendação de Especialista:_ Para projetos ativos do DataEngOS, considere mover o código para o sistema de ficheiros nativo do Linux (`~/projects`) e usar o Git para sincronizar com o repositório remoto, em vez de trabalhar diretamente na montagem do OneDrive (`/mnt/c`). Se o OneDrive for obrigatório, aceite a penalidade de performance mas garanta que o agente está ciente dos caminhos absolutos corretos.12
        

### 3.3 Estratégia de Pastas e Multi-Projetos

O utilizador possui uma pasta `DEV-Projects` com múltiplos repositórios (`.codex`, `olympus-agents`, etc.). O Antigravity funciona melhor com contexto focado, mas o DataEngOS pode precisar de dependências cruzadas (ex: um modelo dbt no projeto A que lê de uma fonte definida no projeto B).

Configuração do Workspace:

Utilize o recurso "Multi-root Workspaces" do motor VS Code subjacente. Crie um ficheiro data-eng-os.code-workspace na raiz:

JSON

```
{
	"folders":,
	"settings": {
		"antigravity.agent.contextMode": "workspace_wide"
	}
}
```

Isto permite que o agente tenha visibilidade sobre todos os projetos relacionados sem precisar de trocar de janela, facilitando a refatorização cruzada e a compreensão da linhagem de dados completa.5

---

## 4. O Sistema Nervoso Agêntico: Model Context Protocol (MCP)

O MCP é a tecnologia que transforma o Antigravity de um chatbot num engenheiro sénior. Ele fornece uma interface padronizada para que os agentes leiam o estado do mundo (bases de dados, logs, contentores) e atuem sobre ele. Para o DataEngOS, propomos uma "Stack MCP" essencial.17

### 4.1 Servidores MCP Essenciais para Engenharia de Dados

A tabela abaixo define a configuração recomendada para o ficheiro de configuração do Antigravity (geralmente localizado em `%APPDATA%\Claude\claude_desktop_config.json` ou local equivalente para o Antigravity se este suportar o padrão de configuração do Claude Desktop, ou via a interface de gestão de extensões do próprio IDE).

|**Categoria**|**Servidor MCP Recomendado**|**Capacidades Críticas para o Agente**|**Justificação**|
|---|---|---|---|
|**Banco de Dados**|`crystaldba/postgres-mcp`|`query`, `explain_plan`, `list_tables`|Permite ao agente validar SQL gerado, verificar se tabelas existem antes de criar modelos dbt e analisar planos de execução para otimização.20|
|**Orquestração**|`astronomer/astro-airflow-mcp`|`list_dags`, `trigger_dag`, `get_task_logs`|Fundamental. Permite ao agente lançar o pipeline que acabou de escrever e, se falhar, ler os logs de erro autonomamente para corrigir o código.21|
|**Transformação**|`dbt-labs/dbt-mcp`|`compile_sql`, `get_lineage`, `run_test`|Dá ao agente acesso ao grafo semântico do dbt. Ele pode ver quais modelos dependem da tabela que está a alterar, prevenindo quebras a jusante.22|
|**Infraestrutura**|`docker-mcp` (Docker Toolkit)|`list_containers`, `inspect_logs`, `restart`|Permite ao agente diagnosticar se o Airflow ou Postgres cairam e reiniciá-los sem intervenção humana.23|
|**Nuvem (Opcional)**|`gcp-mcp` ou `aws-mcp`|`list_buckets`, `query_bigquery`|Se o DataEngOS interagir com a nuvem, estes servidores permitem validar a existência de ficheiros no S3/GCS ou tabelas no BigQuery.24|

### 4.2 Configuração Técnica do MCP no Antigravity

Para configurar estes servidores, deve-se editar o ficheiro de configuração JSON do agente. Assumindo um ambiente WSL com `uv` (um gestor de pacotes Python ultra-rápido) instalado, a configuração seria:

JSON

```
{
  "mcpServers": {
    "postgres-dw": {
      "command": "uvx",
      "args": [
        "postgres-mcp-server",
        "postgresql://user:password@localhost:5432/data_warehouse"
      ]
    },
    "airflow-local": {
      "command": "uvx",
      "args": [
        "astro-airflow-mcp",
        "--airflow-url", "http://localhost:8080",
        "--username", "admin", "--password", "admin"
      ]
    },
    "dbt-core": {
      "command": "uvx",
      "args":
    },
    "filesystem-dev": {
      "command": "npx",
      "args":
    }
  }
}
```

**Nota Crítica:** O servidor `filesystem-dev` é configurado explicitamente para a pasta do utilizador. Isto é uma medida de segurança e eficiência, garantindo que o agente tem permissão explícita para ler/escrever apenas nos diretórios de projeto relevantes, evitando alucinações sobre caminhos de ficheiros inexistentes.25

---

## 5. Potencialização Agêntica: Skills, RLM e Hacks de Eficiência

Para que o DataEngOS funcione de forma fluida, não basta ter as ferramentas (MCP); é preciso ensinar ao agente _como_ pensar.

### 5.1 OpenCode: O Agente CLI para Tarefas Rápidas

O utilizador questionou sobre o **OpenCode**. Esta é uma ferramenta CLI (Linha de Comandos) agêntica que opera no terminal.26

- **Quando usar:** Utilize o OpenCode para tarefas de "trench warfare" (trincheira) rápidas, como refatorizações em massa de ficheiros, ou para interrogar o estado do git (`opencode "resuma as alterações no último commit e gere uma mensagem de PR"`).
    
- **Integração:** Instale o OpenCode no WSL (`npm install -g opencode-ai`). Ele brilha quando precisa de modificar ficheiros sem abrir o IDE pesado, ou quando quer encadear a saída de um comando linux para o LLM. No contexto do DataEngOS, o OpenCode pode servir como um "agente de CI/CD" local que corre testes e corrige falhas antes do commit.
    

### 5.2 Modelos de Linguagem Recursivos (RLM) e Eficiência de Tokens

Projetos de dados legados possuem esquemas massivos (milhares de tabelas). Alimentar todo o esquema num prompt único é impossível ou proibitivamente caro (limites de tokens).

Estratégia RLM (Simulada):

Não existe um botão "Ativar RLM" no Antigravity, mas podemos simular o comportamento através de Prompt Engineering Estrutural:

1. **Exploração Recursiva:** Instrua o agente a _não_ ler todos os ficheiros de uma vez. Em vez disso, dê-lhe uma ferramenta (via script Python ou `ls -R`) para listar a estrutura de pastas.
    
2. **Decomposição:** O agente deve identificar quais subpastas (ex: `models/staging/stripe`) são relevantes para a tarefa e ler _apenas_ esses esquemas.
    
3. **Resumo Iterativo:** Para grandes migrações, peça ao agente para criar um ficheiro `context_memory.md` temporário. O agente analisa um módulo, resume as regras de negócio nesse ficheiro, limpa a sua memória de contexto (nova sessão) e lê o resumo para continuar. Isto simula a "memória infinita" dos RLMs descritos na literatura recente.6
    

### 5.3 O "Hack" TOON (Token-Oriented Object Notation)

Para reduzir o consumo de tokens ao passar dados ou esquemas grandes para o agente, utilize o formato **TOON**.

- **O que é:** Uma alternativa ao JSON que remove chavetas, aspas e vírgulas desnecessárias, usando indentação significativa (semelhante a YAML mas mais compacto).
    
- **Implementação:** Instale um servidor MCP simples (ou script Python) que converta saídas de `dbt docs generate` (que produz um `manifest.json` gigante) para TOON antes de o passar ao agente. Isto pode reduzir o uso de tokens em 30-50%, permitindo que o agente "veja" mais do grafo de linhagem de uma só vez.29
    

---

## 6. Workflow Demonstrativo: DataEngOS em Ação

Para consolidar a configuração, eis um passo-a-passo de como o DataEngOS seria utilizado para criar um novo pipeline na pasta `DEV-Projects`.

1. **Inicialização (Fase DesignOS):**
    
    - Utilizador: "Quero criar um pipeline para dados de vendas do Shopify."
        
    - Agente (DataEngOS): Consulta o MCP `postgres-mcp` para ver se já existem tabelas de vendas. Cria um ficheiro `specs/shopify_sales.yaml` definindo a estrutura Bronze/Silver/Gold e os SLAs.
        
2. **Geração de Código (Fase dbt):**
    
    - Agente: Lê o spec. Usa o MCP `filesystem` para criar `models/staging/shopify/stg_orders.sql`. Aplica as regras de governação (prefixos, macros de limpeza) definidas no prompt de sistema.
        
    - Agente: Executa `dbt compile` via MCP `dbt` para validar a sintaxe SQL instantaneamente.
        
3. **Orquestração (Fase Airflow):**
    
    - Agente: Gera `dags/shopify_etl.py`. Configura o `DbtTaskGroup` para rodar os modelos criados.
        
    - Agente: Usa o MCP `docker` para verificar se o contentor `airflow-scheduler` está saudável.
        
4. **Validação e Auto-Correção:**
    
    - Agente: Dispara o DAG via MCP `airflow`. Monitoriza o estado.
        
    - Cenário de Erro: O DAG falha. O agente usa `get_task_logs`, percebe que falta uma coluna na tabela de destino, altera o modelo dbt, corre `dbt run` novamente e reinicia a tarefa no Airflow. Tudo sem intervenção humana.
        

---

## 7. Conclusão e Próximos Passos

A implementação do **DataEngOS** no **Google Antigravity** não é apenas uma mudança de ferramenta, é uma mudança de fluxo de trabalho. Ao configurar corretamente o ambiente Windows/WSL2, integrar a "stack" de dados via MCP e aplicar estratégias de gestão de contexto como RLM e TOON, transforma-se o IDE num parceiro ativo.

**Recomendações Finais:**

- Comece por criar uma biblioteca de "Prompts de Sistema" robusta que defina o seu "Design System de Dados".
    
- Invista tempo na configuração correta da rede WSL (`mirrored`), pois é a fonte número um de frustração em setups locais.
    
- Utilize o MCP de forma incremental: comece com o acesso ao Filesystem e Bases de Dados (Read-Only) antes de dar permissão total de execução ao Docker/Airflow.
    

Este ambiente prepara o terreno para um futuro onde a engenharia de dados é menos sobre escrever código repetitivo e mais sobre desenhar sistemas inteligentes e resilientes.