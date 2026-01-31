---
aliases:
  - "Vibe Coding com Agentes: Guia Completo"
---
# Relatório de Pesquisa: Blueprint Arquitetural para o DataEngOS - Engenharia de Dados Agêntica com Google Antigravity

## 1. Introdução: A Mudança de Paradigma para a Engenharia "Agent-First"

A indústria de desenvolvimento de software atravessa um momento de inflexão tectônica, transitando de um modelo focado na construção manual de sintaxe para uma arquitetura de orquestração agêntica. Para o projeto **DataEngOS** — concebido por Bruno como um Sistema Operacional para Engenharia de Dados — esta mudança não representa apenas um ganho incremental de produtividade; ela constitui um requisito fundacional para a viabilidade moderna. A convergência entre o **Google Antigravity**, o framework **Ag-Kit** e o **Model Context Protocol (MCP)** cria um ecossistema onde o desenvolvedor deixa de ser um "digitador de código" para assumir o papel de "comandante de missão" ou arquiteto de sistemas autônomos.

Este relatório, elaborado sob uma perspectiva didática e investigativa, visa profissionalizar o conceito de _"Vibe Coding"_ — frequentemente mal interpretado como uma prática casual — transformando-o em uma metodologia de engenharia rigorosa. O foco recai sobre a estruturação de processos robustos que permitam a Bruno orquestrar Skills, Workflows e MCPs, estruturar fases de desenvolvimento desde o Documento de Requisitos do Produto (PRD) até o deploy, e resolver os conflitos técnicos inerentes ao ambiente híbrido WSL/Windows.

### 1.1 A Tese Central: Orquestração sobre Sintaxe

Os Ambientes de Desenvolvimento Integrado (IDEs) tradicionais foram desenhados para a manipulação de texto: autocompletar, realce de sintaxe e refatoração estática. O Google Antigravity inaugura o conceito de "Ambiente Agêntico". Neste modelo, a responsabilidade do engenheiro desloca-se da implementação do "como" (a sintaxe específica do Python ou SQL) para a definição do "o quê" (intencionalidade via PRDs) e das "restrições" (governança via Regras). Agentes autônomos executam a implementação através de múltiplos arquivos, ferramentas e ambientes simultaneamente.

Para o DataEngOS, isso implica que a plataforma não é composta apenas por código estático, mas por um sistema vivo de agentes capazes de compreender a linhagem de dados (data lineage), executar transformações dbt e gerenciar o estado da infraestrutura através de protocolos padronizados. A eficácia deste modelo depende, contudo, de uma rigorosa gestão de contexto e de uma arquitetura de prompts que elimine a ambiguidade.

---

## 2. O Ambiente Operacional: Arquitetura do Google Antigravity

Para dominar o fluxo de trabalho proposto, é imperativo compreender profundamente a ferramenta. O Google Antigravity não é apenas um "fork" do VS Code com esteroides de IA; é uma reengenharia completa da superfície do IDE, projetada para suportar a carga cognitiva de gerenciar inteligências artificiais assíncronas.

### 2.1 Anatomia de um IDE Agêntico

O Antigravity introduz três superfícies operacionais distintas, cada uma servindo a uma fase crítica no ciclo de vida do desenvolvimento do DataEngOS. O domínio destas superfícies é o que diferencia um usuário casual de um engenheiro de IA profissional.

#### 2.1.1 O Editor (Modo Síncrono)

Esta é a visão centrada no código, familiar a qualquer desenvolvedor. No entanto, no Antigravity, ela é aumentada com "Consciência Agêntica". Quando o usuário destaca um bloco de código e emite um prompt, o agente possui acesso imediato ao contexto do cursor, à estrutura de arquivos adjacentes e ao estado ativo do terminal.

- **Aplicação no DataEngOS:** Este modo é ideal para tarefas de baixa latência e alta precisão, como renomear variáveis em um script de ingestão Python, corrigir bugs localizados em uma _macro_ Jinja do dbt, ou otimizar uma consulta SQL específica.
    
- **Limitação:** O modo editor é linear e síncrono. Ele bloqueia o desenvolvedor enquanto a IA "pensa", tornando-o ineficiente para tarefas arquiteturais complexas.
    

#### 2.1.2 O Gerente de Agentes (Mission Control - Modo Assíncrono)

Esta é a funcionalidade distintiva do Antigravity. Trata-se de uma interface dedicada para "spawnar" (criar) e gerenciar agentes assíncronos. Aqui, o desenvolvedor pode designar missões de alto nível, como _"Refatorar todo o pipeline de ingestão para utilizar a nova arquitetura Bronze-Silver-Gold"_ ou _"Criar testes unitários para todos os modelos de dados críticos"_.

- **Paralelismo:** Esta separação permite o desenvolvimento paralelo. Enquanto um agente pesquisa atualizações de bibliotecas, outro pode estar escrevendo documentação e um terceiro executando testes de integração.
    
- **Artefatos:** Estes agentes não respondem apenas com texto; eles produzem **Artefatos** — planos de implementação, listas de tarefas e _diffs_ de código que o usuário deve revisar e aprovar. Isso introduz uma camada de governança essencial para projetos sérios como o DataEngOS.
    

#### 2.1.3 O Navegador (Superfície de Atuação)

O Antigravity inclui um subagente de navegador _headless_ (sem interface gráfica obrigatória) capaz de interagir com interfaces web.

- **Aplicação no DataEngOS:** Para engenharia de dados, isso é crucial para tarefas de verificação que vão além do código. O agente pode, por exemplo, fazer login na UI do Apache Airflow para verificar se uma DAG (Directed Acyclic Graph) foi renderizada corretamente, ou navegar pela documentação do dbt gerada localmente para validar a linhagem de dados, tudo sem que o desenvolvedor precise sair do contexto do IDE.
    

### 2.2 O "Cérebro" do IDE: Gestão de Contexto e Regras

O sucesso de um agente depende inteiramente da qualidade e relevância do contexto que lhe é fornecido. O Antigravity gerencia este contexto através de uma estrutura hierárquica de arquivos que Bruno deve implementar rigorosamente para que o DataEngOS funcione de forma determinística e confiável.

|**Nível de Contexto**|**Localização do Arquivo**|**Função e Aplicação no DataEngOS**|
|---|---|---|
|**Global (Constituição)**|`~/.gemini/GEMINI.md`|Define as "Regras de Deus" aplicáveis a todos os projetos do usuário. Ex: "Sempre prefira CTEs a subqueries", "Nunca commite segredos/chaves de API", "Use inglês para comentários de código".|
|**Workspace (Projeto)**|`.agent/rules/`|Define a "Física" do projeto específico. Contém arquivos como `tech_stack.md` (versões das libs), `architecture.md` (padrões de design) e `style_guide.md`.|
|**Workflow (Procedural)**|`.agent/workflows/`|Templates de prompts salvos acionados por comandos de barra (`/`). Ex: `/test` para rodar suites de teste específicas ou `/doc` para gerar documentação padronizada.|

**Insight Profundo:** O mecanismo de `.rules` atua como uma "Constituição" para a IA. Sem ele, o agente recorre aos dados genéricos de seu treinamento, o que frequentemente resulta em alucinações — como sugerir bibliotecas depreciadas ou sintaxe incompatível com a versão do Python em uso. Para o DataEngOS, regras explícitas são a principal defesa contra a dívida técnica gerada por IA.

---

## 3. Profissionalizando o "Vibe Coding": Metodologia Spec-Driven (SDD)

O termo "Vibe Coding" refere-se à prática de codificar através de prompts em linguagem natural. Contudo, em sua forma ingênua (loops de tentativa e erro), é ineficiente e perigosa para pipelines de dados, onde erros lógicos (como um _fan-out_ não intencional em um JOIN) podem corromper dados silenciosamente sem gerar erros de sintaxe. Para profissionalizar o uso dessas ferramentas no DataEngOS, recomendamos a adoção do **Spec-Driven Development (SDD)**.

### 3.1 O Fracasso do Vibe Coding Ingênuo

A abordagem comum de "Pedir -> Código -> Erro -> Pedir Correção" falha em complexidade. Agentes de IA, como LLMs, possuem janelas de contexto limitadas e tendem a perder a "visão do todo" em conversas longas. Sem uma especificação clara, o agente "alucina" requisitos não ditos.

### 3.2 O Fluxo de Trabalho SDD para Agentes

Inspirado nos frameworks **Spec Kit** do GitHub e **Open Spec**, o SDD inverte o fluxo: os agentes devem "pensar" e "planejar" antes de "agir".

#### Fase 1: O Documento de Requisitos do Produto (PRD)

Antes de gerar uma única linha de SQL ou Python, o agente deve ser instruído a criar um PRD.

- **Estratégia de Prompt:** _"Atue como um Engenheiro de Dados Líder. Quero construir um módulo para o DataEngOS que detecte 'drift' de esquema. Entreviste-me para coletar os requisitos e, em seguida, redija um arquivo `PRD.md`."_
    
- **Artefato:** O `PRD.md` deve definir a História do Usuário, Requisitos Funcionais, Requisitos Não-Funcionais (latência, custo) e Contratos de API.
    

#### Fase 2: A Especificação Técnica (O "Spec")

Uma vez aprovado o PRD, o agente deve traduzi-lo em uma especificação técnica detalhada.

- **Comando:** `/plan` (se utilizando workflows do Ag-Kit) ou prompt manual: _"Baseado no `PRD.md`, crie um `TECH_SPEC.md` detalhando a estrutura de classes Python, o grafo de dependência dos modelos dbt e as bibliotecas específicas a serem usadas."_
    
- **Verificação:** Bruno deve revisar este documento. Este é o ponto mais barato para encontrar erros. Se a especificação diz "Use Pandas" mas o volume de dados exige "Polars" ou "Spark", a correção acontece aqui, economizando horas de refatoração.
    

#### Fase 3: A Decomposição em Tarefas

O agente quebra o `TECH_SPEC.md` em tarefas atômicas e sequenciais.

- **Artefato:** `TASKS.md` ou `todo.md`.
    
- **Execução:** O agente (ou Bruno) marca os itens um a um. _"Implemente a classe `SchemaValidator`."_ _"Escreva testes unitários para `SchemaValidator`."_ Isso impede que o agente tente gerar todo o sistema em uma única janela de contexto, o que inevitavelmente leva a truncamento e erros lógicos.
    

**Tabela 1: Comparação de Metodologias de Codificação**

|**Característica**|**Codificação Tradicional**|**Vibe Coding Ingênuo**|**Spec-Driven Vibe Coding (Recomendado)**|
|---|---|---|---|
|**Input Primário**|Sintaxe Precisa|Prompts Naturais Vagos|Documentos Estruturados (PRD, Spec)|
|**Tratamento de Erro**|Debugging Manual|Prompting Iterativo ("Corrija isso")|Revisão Arquitetural Prévia|
|**Janela de Contexto**|Memória do Dev|Limitada pelo Token Limit|Gerenciada via `.rules` e `Spec.md`|
|**Escalabilidade**|Alta (com disciplina)|Baixa (colapso na complexidade)|Alta (via delegação de agentes)|
|**Papel Humano**|Digitador/Lógico|Promptador/Revisor|Arquiteto/Gerente de Produto|

---

## 4. Orquestração: O Framework Ag-Kit e a Arquitetura de Agentes

Para profissionalizar o DataEngOS, não podemos confiar em um "Chatbot" genérico. Devemos implantar uma equipe de agentes especializados. É aqui que entra o **Ag-Kit** (`@vudovn/ag-kit`), um multiplicador de força para o Antigravity.

### 4.1 O Ecossistema Ag-Kit

O Ag-Kit é um aprimoramento "batteries-included" para o Antigravity que pré-configura o ambiente com personas e workflows especializados. A instalação via `npx @vudovn/ag-kit init` instancia um diretório local `.agent` contendo mais de 16 agentes especializados, prontos para uso.

**Agentes Críticos para DataEngOS:**

1. **`@backend-specialist`:** Especialista em Python, design de API e infraestrutura. Use este agente para construir a camada de orquestração do DataEngOS (ex: Airflow, Prefect).
    
2. **`@debugger`:** Um solucionador de problemas sistemático. Ele não apenas "adivinha" correções; ele analisa _stack traces_ contra o contexto da base de código para identificar a causa raiz.
    
3. **`@security-auditor`:** Fundamental para Engenharia de Dados. Ele varre o código em busca de segredos _hardcoded_, vulnerabilidades de injeção de SQL na geração dinâmica de queries e problemas de permissão.
    
4. **`@frontend-specialist`:** Útil se o DataEngOS tiver uma interface de usuário (UI) para monitoramento ou configuração.
    

### 4.2 Criação de Agentes Personalizados: O "DataEng Agent"

Embora o Ag-Kit forneça uma base sólida, Bruno precisará de um agente customizado, especializado em sua stack específica (dbt, Airflow, Docker, Cloud Provider). O Ag-Kit permite a criação de agentes personalizados definindo arquivos `SKILL.md` e configurações de agente.

**Tutorial: Criando o Agente Especialista em Engenharia de Dados**

1. **Definir a Persona:** Crie um arquivo `.agent/agents/data-eng.yaml` (ou JSON/TS, dependendo da versão do Ag-Kit).
    
    - _Instruções:_ "Você é um Engenheiro de Dados Sênior especializado em dbt e Airflow. Você prioriza idempotência, testes de qualidade de dados e SQL modular. Você sempre assume uma arquitetura 'Medallion' (Bronze/Silver/Gold)."
        
2. **Equipar com Skills:** Atribua habilidades específicas a este agente.
    
    - `dbt-runner`: Uma habilidade capaz de executar comandos `dbt run`, `dbt test` e analisar os logs JSON resultantes.
        
    - `sql-optimizer`: Uma habilidade contendo regras para otimização de consultas (ex: poda de partição, evitar `SELECT *`).
        
3. **Definir Workflows:** Crie `.agent/workflows/data-pipeline.md`.
    
    - _Gatilho:_ `/pipeline`
        
    - _Passos:_
        
        1. Analisar o arquivo `schema.yml`.
            
        2. Gerar modelos de _staging_ (`stg_*.sql`).
            
        3. Gerar modelos intermediários com lógica de negócios.
            
        4. Gerar documentação e testes genéricos.
            
        5. Executar `dbt test` e reportar falhas.
            

**Insight:** Ao criar este agente personalizado, Bruno transita de "pedir ao Gemini genérico para escrever SQL" para "comandar um agente especializado a construir um componente de pipeline em conformidade com as regras do projeto".

### 4.3 Diferença entre Skills e Workflows

É crucial entender a distinção para orquestrar corretamente:

- **Skills (Habilidades):** São capacidades atômicas ou ferramentas que o agente pode _usar_ quando julgar necessário. Ex: "Ler um arquivo PDF", "Executar uma query no BigQuery", "Validar um arquivo YAML". As skills são carregadas sob demanda para economizar tokens de contexto.
    
- **Workflows (Fluxos de Trabalho):** São procedimentos roteirizados, passo-a-passo, que guiam o agente através de um processo complexo. Ex: "Fazer o deploy", "Criar uma nova feature". Workflows garantem consistência em processos repetitivos.
    

---

## 5. Model Context Protocol (MCP): Conectando o Cérebro aos Dados

O **Model Context Protocol (MCP)** é a integração tecnológica mais crítica para o DataEngOS. Ele resolve o "problema do isolamento", onde a IA gera código "cego", sem acesso ao esquema real do banco de dados, ao estado do data warehouse ou à linhagem viva do dbt.

### 5.1 Por que o MCP é Inegociável?

Sem o MCP, a IA "alucina" definições de tabelas e tipos de colunas baseada em nomes de variáveis ou comentários. Com o MCP, a IA pode consultar os metadados do banco de dados diretamente. O MCP atua como uma porta USB-C padrão para conectar ferramentas de IA a fontes de dados externas.

### 5.2 Configurando o Servidor dbt MCP

O **Servidor dbt MCP** atua como uma ponte entre o Antigravity e o projeto dbt local. Ele expõe ferramentas como `get_lineage` (obter linhagem), `list_models` (listar modelos), `compile_sql` (compilar SQL) e `run_test` (rodar testes) diretamente para o agente.

**Guia de Configuração para o Antigravity:**

A configuração de MCP no Antigravity é sutil, pois o arquivo de configuração muitas vezes está oculto e não segue os padrões exatos do VS Code.

1. **Localizar a Configuração:** O arquivo de configuração é o `mcp_config.json`. Ele geralmente reside em `%userprofile%/.gemini/antigravity/` (Windows) ou `~/.gemini/antigravity/` (Linux/Mac). Também é possível acessá-lo via Painel do Agente > menu `...` > "Manage MCP Servers" > "View raw config".
    
2. **Instalar o Servidor:** Utilizaremos o servidor dbt MCP local para evitar dependências de nuvem e garantir privacidade.
    
    - _Pré-requisito:_ Instalar `uv` ou `pip` para gerenciar pacotes Python.
        
3. **Configuração JSON:**
    
    Insira o seguinte bloco no `mcp_config.json`:
    

JSON

```
{
  "mcpServers": {
    "dbt": {
      "command": "uvx",
      "args": ["dbt-mcp"],
      "env": {
        "DBT_PROJECT_DIR": "/home/bruno/DataEngOS/dbt_project",
        "DBT_PATH": "/home/bruno/.local/bin/dbt",
        "DBT_CLI_TIMEOUT": "120"
      }
    }
  }
}
```

_Nota Crítica:_ Ajuste os caminhos para o ambiente específico. Se estiver rodando no WSL, os caminhos devem ser os caminhos _Linux_ (ex: `/home/bruno/...`). O comando `uvx` deve estar acessível no PATH do ambiente onde o servidor MCP é iniciado.

### 5.3 Workflows Avançados com MCP

Uma vez configurado, o agente ganha "superpoderes" de dados:

- **Descoberta:** _"Agente, liste todos os modelos à jusante (downstream) de `stg_orders`."_ (O agente chama a ferramenta `get_lineage`).
    
- **Consulta Semântica:** _"Agente, qual é a definição da métrica `monthly_recurring_revenue`?"_ (O agente verifica a Camada Semântica do dbt).
    
- **Execução e Correção:** _"Agente, rode um teste no novo modelo e corrija quaisquer erros de SQL."_ (O agente roda `dbt test`, lê o log de erro via MCP, edita o SQL no editor e re-executa).
    

---

## 6. Resolvendo o Enigma WSL/Windows

O usuário solicitou especificamente soluções para o ambiente **WSL/Windows**. Este é um ponto de fricção notório para IDEs de IA devido à "fronteira" entre o host Windows (onde a GUI e o Chrome geralmente vivem) e o subsistema Linux (onde o código, o Docker e as ferramentas vivem).

### 6.1 A Arquitetura do Conflito

O Antigravity, assim como o VS Code, precisa de um componente servidor rodando dentro do WSL para gerenciar operações de arquivo e comandos de terminal. No entanto, ao contrário do VS Code, os agentes do Antigravity frequentemente tentam lançar navegadores ou acessar arquivos de configuração globais que podem residir do outro lado da fronteira.

- **Problema 1: Acesso a Arquivos.** O agente diz "Não consigo ver os arquivos" porque o workspace foi aberto via um caminho Windows (ex: `C:\Users`) em vez de uma conexão remota WSL, ou as permissões de montagem estão incorretas.
    
- **Problema 2: Conectividade do Navegador.** O Agente de Navegador rodando dentro do WSL tenta se conectar a `localhost:9222` para controlar o Chrome. No WSL 2, `localhost` muitas vezes refere-se à VM Linux, não ao host Windows, fazendo a conexão falhar.
    
- **Problema 3: O CLI (`agy`).** O comando `agy` pode não ser encontrado no terminal WSL, impedindo a abertura correta do projeto via linha de comando.
    

### 6.2 O "Guia do Mochileiro" para Correção no WSL

Para estabilizar o DataEngOS no WSL, Bruno deve aplicar as seguintes correções de engenharia:

**Correção 1: Carregamento Correto do Workspace**

Nunca abra a pasta via `\\wsl$\...` a partir do Windows Explorer.

1. Abra o Antigravity no Windows.
    
2. **Melhor Prática:** Lance a partir do terminal. Dentro do terminal WSL (Ubuntu/Debian), navegue até a pasta do projeto e digite `agy.` (requer a Correção 2).
    
3. Isso garante que o servidor do Antigravity inicialize no contexto Linux, tendo acesso nativo às ferramentas instaladas no WSL (Python, dbt, Git).
    

**Correção 2: O Symlink do CLI**

Para fazer o comando `agy` funcionar dentro do WSL (permitindo `agy.`):

Execute no terminal WSL:

Bash

```
ln -sf "/mnt/c/Users/<SEU_USUARIO_WINDOWS>/AppData/Local/Programs/Antigravity/bin/antigravity" ~/.local/bin/agy
```

_Nota:_ Substitua `<SEU_USUARIO_WINDOWS>` pelo nome real do usuário. Certifique-se de que `~/.local/bin` está no seu `$PATH`.

**Correção 3: Rede para o Agente de Navegador**

Para permitir que o Agente no WSL controle o Chrome no Windows:

1. **Habilite o Networking Espelhado (Mirrored Networking):** No Windows 11, crie ou edite o arquivo `.wslconfig` no diretório de usuário do Windows (`%UserProfile%`):
    
    Ini, TOML
    
    ```
    [wsl2]
    networkingMode=mirrored
    ```
    
    Isso faz com que o `localhost` no WSL atinja efetivamente o `localhost` no Windows, unificando a rede.
    
2. **Método Alternativo (Proxy de Porta - Windows 10):** Se o modo espelhado não for opção, use o PowerShell como Admin para criar um proxy:
    
    PowerShell
    
    ```
    netsh interface portproxy add v4tov4 listenport=9222 listenaddress=0.0.0.0 connectport=9222 connectaddress=$(wsl hostname -I)
    ```
    
    _Insight:_ O modo espelhado é a solução moderna e robusta. O método de proxy é frágil e deve ser evitado se possível.
    

**Correção 4: Permissões de Arquivo**

Se os agentes falham ao escrever arquivos ou o `git status` mostra alterações em todos os arquivos (devido a bits de permissão), é um problema de metadados na montagem.

Atualize o arquivo `/etc/wsl.conf` no Linux:

Ini, TOML

```
[automount]
options = "metadata,umask=22,fmask=11"
```

Isso força permissões Linux corretas em drives montados, evitando que o agente veja todos os arquivos como executáveis (777), o que confunde linters e o git.

---

## 7. Deep Research Agents: A Fase de "Planejamento"

Antes de codificar, o DataEngOS requer pesquisa profunda — comparar padrões arquiteturais (ex: "Iceberg vs Delta Lake"), encontrar as versões corretas de bibliotecas ou sintetizar documentação dispersa. Os agentes padrão do Antigravity são bons, mas **Agentes de Pesquisa Profunda (Deep Research Agents)** são superiores para raciocínio estendido.

### 7.1 Integrando Deep Research

Podemos integrar um loop de "Deep Research" utilizando bibliotecas como **LangGraph** ou fluxos de pesquisa do **Ag-Kit**.

- **Conceito:** Um "Deep Research Agent" recebe uma consulta, quebra-a em subquestões, navega na web iterativamente, sintetiza as descobertas e produz um relatório (Artefato).
    
- **Implementação no Antigravity:**
    
    1. **Instalar Ferramenta de Pesquisa:** Use `uv` para instalar um pesquisador profundo baseado em Python (ex: implementações open-source de deep research).
        
    2. **Envelopar como MCP:** A maneira mais elegante de usar isso no Antigravity é expor o pesquisador como uma Ferramenta MCP.
        
    3. **O Fluxo:**
        
        - Usuário: _"Pesquise a melhor maneira de lidar com evolução de esquema no BigQuery para o DataEngOS."_
            
        - Agente Antigravity -> chama `deep_research_tool` (via MCP).
            
        - Ferramenta de Pesquisa -> Navega, faz scraping, sumariza.
            
        - Saída -> Um relatório Markdown salvo em `docs/research/schema_evolution.md`.
            
    4. **Ação:** O agente de codificação então lê este relatório para gerar o plano de implementação.
        

**Insight:** Isso separa o "Pensar" do "Codificar". Ao forçar a criação de um artefato de pesquisa, prevenimos que o agente de codificação "adivinhe" APIs que não existem ou use padrões obsoletos.

---

## 8. O Fluxo de Trabalho Definitivo do "DataEngOS"

Combinando todos os elementos, apresentamos o fluxo de trabalho prescrito para Bruno, cobrindo do PRD ao Deploy:

### Fase 1: Iniciação e Pesquisa (O Arquiteto)

1. **Gatilho:** Bruno digita `/brainstorm` (Workflow do Ag-Kit).
    
2. **Prompt:** _"Precisamos de um módulo de qualidade de dados usando Great Expectations."_
    
3. **Ação do Agente:** O agente utiliza o **Deep Research MCP** para encontrar as configurações mais recentes do GE compatíveis com a stack atual (definida em `.agent/rules/tech_stack.md`).
    
4. **Artefato:** Produz `research/ge_integration_options.md` com prós e contras.
    

### Fase 2: Especificação (O Gerente de Produto)

1. **Gatilho:** `/plan` ou prompt manual baseado na pesquisa.
    
2. **Prompt:** _"Crie um PRD e uma Spec Técnica para a Opção B da pesquisa."_
    
3. **Ação do Agente:** Rascunha `specs/PRD-001-DQ.md` e `specs/TECH-001-DQ.md`.
    
4. **Revisão Humana:** Bruno edita a spec para garantir conformidade com nomenclaturas ou regras de negócio específicas. Esta é a "Barreira de Qualidade".
    

### Fase 3: Implementação (Os Engenheiros)

1. **Orquestração:** Bruno abre a aba **Agent Manager**.
    
2. **Tarefas Paralelas:**
    
    - Agente A (SQL Specialist): _"Implemente as macros dbt definidas em `TECH-001-DQ.md`."_ (Usa dbt MCP para verificar sintaxe e colunas).
        
    - Agente B (Backend Specialist): _"Crie a DAG Python no Airflow para disparar estes testes."_
        
3. **Contexto:** Ambos os agentes recebem o `TECH-001-DQ.md` e as regras globais `.agent/rules/` como contexto.
    

### Fase 4: Verificação (O QA)

1. **Gatilho:** `/test` ou prompt manual.
    
2. **Ação do Agente:** O agente roda `dbt test` via terminal. Se um teste falhar, ele lê o log de erro, abre o arquivo SQL ofensor, corrige a lógica e re-executa.
    
3. **Verificação no Navegador:** O Agente de Navegador loga na UI do Airflow (acessível via `localhost` graças à correção de rede WSL) e verifica se a DAG é válida e agendável.
    

### Fase 5: Deploy

1. **Gatilho:** `/deploy` (Workflow do Ag-Kit).
    
2. **Ação:** O agente executa `git commit`, `git push` e monitora o status do CI/CD no navegador (ex: GitHub Actions), reportando o sucesso ou falha do build.
    

---

## 9. Conclusão e Perspectivas Futuras

A profissionalização do uso do Google Antigravity para o projeto DataEngOS exige um abandono disciplinado do "vibe coding" casual. Ao tratar os agentes como membros da equipe que requerem documentação precisa (PRDs/Specs), ao utilizar o Model Context Protocol para fundamentá-los na realidade dos dados, e ao resolver as fricções de infraestrutura do WSL, Bruno pode transformar radicalmente seu processo de desenvolvimento.

O futuro da engenharia não reside na escrita de código, mas na definição e orquestração do **sistema de agentes** que escreve o código. Com a arquitetura detalhada acima — ancorada pelo Ag-Kit, dbt MCP e Desenvolvimento Orientado a Especificações (SDD) — o DataEngOS não será apenas um projeto construído _com_ IA, mas um projeto construído _por_ uma organização coesa de Humanos e IA.

### 9.1 Checklist de Ação Imediata para Bruno

- [ ] **Instalar Ag-Kit:** Executar `npx @vudovn/ag-kit init` na raiz do projeto.
    
- [ ] **Configurar dbt MCP:** Editar `mcp_config.json` com os caminhos absolutos corretos do WSL.
    
- [ ] **Corrigir WSL:** Implementar o symlink para `agy` e habilitar `networkingMode=mirrored` no `.wslconfig`.
    
- [ ] **Criar "Regras de Deus":** Popular `~/.gemini/GEMINI.md` com padrões de codificação inegociáveis.
    
- [ ] **Adotar SDD:** Parar de solicitar código imediatamente. Solicitar Specs primeiro.
    

---

### Apêndice A: Estrutura de Diretórios Recomendada para DataEngOS Agêntico

DataEngOS/

├──.agent/ # Configuração Ag-Kit & Antigravity

│ ├── agents/ # Definições de Agentes Customizados

│ │ └── data_engineer.yaml

│ ├── rules/ # Regras de Contexto do Workspace

│ │ ├── tech_stack.md # "Usamos dbt v1.8..."

│ │ └── style_guide.md # "Guia de Estilo SQL..."

│ ├── skills/ # Skills Customizadas (Python/Bash)

│ │ └── dbt_runner.py # Script para rodar/parsear dbt

│ └── workflows/ # Definições de Slash Command

│ ├── deep_research.md # Cadeia de prompts "/research"

│ └── deploy_pipeline.md # Cadeia de prompts "/deploy"

├── specs/ # Artefatos do Spec-Driven Development

│ ├── PRD/ # Requisitos do Produto

│ └── ARCH/ # Arquiteturas Técnicas

├── dbt_project/ # Camada de Transformação de Dados

└── airflow_dags/ # Camada de Orquestração