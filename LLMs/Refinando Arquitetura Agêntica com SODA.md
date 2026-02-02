
# SODA v2: Sistema Operacional de Desenvolvimento Agêntico

O Paradigma S.O.D.A. (Spec-Oriented Dockerized Architecture): Uma Arquitetura Neuro-Adaptativa para Engenharia Agentica no Google Antigravity

## 1. Introdução: A Convergência entre Neurodiversidade e Engenharia de Agentes

A evolução dos Ambientes de Desenvolvimento Integrados (IDEs) para Plataformas Agenticas, exemplificada pelo Google Antigravity, representa uma ruptura fundamental na forma como o software é construído. Não se trata mais apenas de editar texto, mas de orquestrar inteligência. Para o perfil neurocognitivo específico de **Altas Habilidades/Superdotação (AH-SD) com TDAH**, esta transição oferece uma oportunidade sem precedentes de alinhar a capacidade de processamento cognitivo (o "hardware" biológico de alta performance) com um sistema operacional externo que mitiga os déficits de função executiva (a "memória RAM" biológica volátil).

Este relatório define e refina o conceito de **SODA** — aqui formalizado como **Spec-Oriented Dockerized Architecture** (Arquitetura Agentica Dockerizada Orientada a Especificações). Esta estrutura não é apenas um conjunto de ferramentas; é um "Exocórtex" projetado para externalizar a memória de trabalho, impor disciplina processual através de SOPs (Procedimentos Operacionais Padrão) e garantir a segurança operacional através de segmentação modal.

### 1.1 O Imperativo Neuro-Adaptativo: AH-SD e o "Desgaste de Contexto"

O usuário identifica-se com o perfil AH-SD/TDAH. Em termos de engenharia de software, este perfil caracteriza-se frequentemente por uma capacidade excepcional de visualização arquitetural e resolução de problemas complexos (Hiperfoco), contraposta por uma fragilidade na manutenção de estado a curto prazo e aversão a tarefas repetitivas (Déficit Executivo).

O problema central identificado na solicitação é o "desgaste da janela de contexto" e a sobrecarga cognitiva de instruir repetidamente o agente. No contexto de LLMs (Large Language Models), o "desgaste" é técnico (o modelo perde precisão à medida que a janela de tokens enche). No contexto humano, o desgaste é neuroquímico (a fadiga de decisão ao gerenciar múltiplos contextos).

A arquitetura SODA proposta resolve isso através de três pilares:

1. **Persistência Externa (SOPs/BMad/OpenSpec):** O estado do projeto nunca reside apenas na "cabeça" do agente ou do usuário, mas em arquivos estruturados.
    
2. **Segmentação Modal (Docker Gateway):** Ferramentas são isoladas e invocadas apenas quando necessárias, mantendo o "ruído" baixo.
    
3. **Memória Recursiva e Infinita (RLM/OpenMemory/Ralph):** O sistema "lembra" para que o usuário possa se dar ao luxo de "esquecer" temporariamente e focar na criação.
    

## 2. O Ambiente Hospedeiro: Google Antigravity e a Mudança de Paradigma

O Google Antigravity serve como o sistema operacional para esta arquitetura. Diferente de copilotos que vivem na barra lateral (sidebar), o Antigravity oferece uma "Superfície de Gerenciamento" onde os agentes são atores de primeira classe.

### 2.1 Da "Vibe Coding" à Engenharia Estruturada

O termo "Vibe Coding" refere-se à geração de código baseada em prompts ad-hoc e intuição momentânea. Embora rápido, é frágil e propenso a erros de contexto — um perigo para o TDAH, que pode perder o fio da meada. O SODA força a transição para a Engenharia Agentica:

- **Editor View:** Onde o usuário AH-SD aplica sua criatividade direta e refinamento manual.
    
- **Manager Surface:** Onde o usuário atua como "Arquiteto de Sistemas", delegando a execução para agentes assíncronos.
    

### 2.2 O Papel dos Artefatos na Validação Cognitiva

O Antigravity utiliza "Artefatos" (planos de implementação, gravações de navegador) em vez de logs lineares de texto. Para o AH-SD, isso é crucial. A leitura linear de logs longos induz à fadiga atencional. Artefatos visuais e estruturados permitem uma validação rápida ("Scan and Verify"), alinhando-se com a velocidade de processamento visual superior típica da superdotação.

## 3. Arquitetura Somática: O Gateway Docker e a Segmentação Modal

A exigência de "Segmentação Modal" é a pedra angular da segurança e da higiene de contexto nesta arquitetura. Utilizaremos o **Docker MCP Toolkit** não apenas como um lançador de ferramentas, mas como um roteador inteligente que define "Modos de Operação".

### 3.1 A Teoria da Segmentação Modal via Gateway

Um agente "generalista" com acesso a 50 ferramentas simultaneamente sofre de paralisia de decisão e alucinação de esquemas. A Segmentação Modal agrupa ferramentas em contextos lógicos (containers), acessíveis através do Gateway Docker. O Antigravity conecta-se apenas ao Gateway, que gerencia o ciclo de vida dos containers sob demanda.

|**Modo (Segmento)**|**Função Cognitiva**|**Ferramentas MCP (Dockerizadas)**|**Isolamento & Segurança**|
|---|---|---|---|
|**Córtex (Pesquisa)**|Aquisição de Informação|`DuckDuckGo`, `Exa`, `YouTube Transcripts`, `Fetch`, `Context7`|Acesso à Internet irrestrito. Sem acesso de escrita ao código local.|
|**Soma (Execução)**|Manipulação de Código|`Smart Coding MCP`, `Markitdown`, `MCP TOON`|Acesso de Leitura/Escrita ao `${workspaceFolder}`. Sem internet.|
|**Memória (Retenção)**|Gestão de Estado e Longo Prazo|`OpenMemory`, `Memory (Reference)`, `RLM (Aleph)`|Volumes Persistentes (SQLite/Postgres).|
|**Operações (Ops)**|Gestão de Projeto e Infra|`GitHub Project Manager`, `GitHub (Official)`, `Docker`|Acesso autenticado a APIs externas (OAuth).|
|**Auxiliar (Skills)**|Utilitários Específicos|`Time`, `OSP Marketing Tools`|Ferramentas de nicho ativadas sob demanda.|

### 3.2 Análise Detalhada dos MCPs no Ecossistema SODA

Abaixo, detalhamos como cada ferramenta solicitada se integra à topologia do Gateway, justificando sua presença no fluxo de trabalho AH-SD.

#### 3.2.1 Ferramentas de Pesquisa e Contexto Externo

- **Exa vs. DuckDuckGo:**
    
    - DuckDuckGo : Utilizado para pesquisas rápidas, factuais e verificação de dados em tempo real. Baixo custo cognitivo.
        
    - _Exa:_ Utilizado para "Pesquisa Neural" ou semântica. Quando o usuário precisa encontrar "bibliotecas Python para orquestração de agentes" (conceito) em vez de uma string específica. O Exa brilha na fase de Arquitetura do BMad.
        
- **YouTube Transcripts & Fetch:**
    
    - Permitem que o agente "assista" a tutoriais ou leia documentação técnica (via `fetch`) e a converta em conhecimento utilizável (via `Markitdown` ). Isso alimenta o RLM (Aleph) sem que o usuário precise ler manualmente.
        
- **Context7:**
    
    - Atua como uma camada de enriquecimento de dados, fornecendo contexto situacional adicional que pode não estar explícito na query, ajudando a desambiguar intenções vagas típicas de momentos de dispersão.
        

#### 3.2.2 Ferramentas de Execução e Código

- Smart Coding MCP :
    
    - Diferente de uma busca textual (grep), este MCP cria embeddings do código local. Ele permite que o agente entenda "onde está a lógica de autenticação" semanticamente. Para o AH-SD, isso elimina a frustração de ter que apontar arquivos específicos para o agente. É o "olho" técnico do sistema.
        
- Markitdown & MCP TOON :
    
    - _Markitdown:_ Converte qualquer entrada (PDF, Excel, HTML) em Markdown limpo.
        
    - _MCP TOON:_ Otimizador crítico. Converte respostas JSON verbosas de APIs em formato TOON (Token-Oriented Object Notation), economizando 30-60% de tokens. Isso retarda o "desgaste" da janela de contexto, permitindo sessões de trabalho mais longas antes da degradação do modelo.
        

#### 3.2.3 Ferramentas de Memória e Gestão

- OpenMemory :
    
    - Memória episódica de longo prazo. Armazena preferências do usuário ("Prefiro TypeScript estrito") e decisões passadas. Essencial para evitar repetição de instruções.
        
- GitHub Project Manager & GitHub Official :
    
    - Enquanto o MCP oficial lida com Pull Requests e Issues, o _Project Manager_ permite a gestão de Roadmaps e Sprints. Isso se integra ao BMad para manter o rastreamento do progresso alinhado com o repositório remoto.
        

### 3.3 Mecânica de Roteamento Inteligente (Gateway)

O Docker MCP Gateway atua como um "proxy reverso" para LLMs.

- **Configuração Dinâmica:** Em vez de carregar todos os schemas de ferramentas no prompt do sistema (o que consumiria milhares de tokens), o Gateway expõe um catálogo leve.
    
- **Ativação sob Demanda:** Quando o Antigravity detecta a necessidade de usar o `Exa`, ele envia a solicitação ao Gateway. O Gateway, se o container `exa-mcp` estiver parado, inicia-o, executa a query, devolve a resposta e (configuravelmente) o suspende. Isso mantém a pegada de recursos da máquina local eficiente.
    

## 4. A Camada Cognitiva: Inteligência Contextual e Preservação

Para atender ao requisito de "contextualização inteligente" sem repetição, implementamos um sistema de memória em três níveis, mimetizando a cognição humana.

### 4.1 Nível 1: Memória de Trabalho Externa (Plan-With-Files)

Baseado no padrão "Manus" , o **Plan-With-Files** é a ferramenta mais crítica para o TDAH.

- **O Problema:** A memória de trabalho biológica falha em manter a "pilha" de tarefas (o que fiz, o que estou fazendo, o que farei). O chat do LLM é efêmero e sai de tela.
    
- **A Solução:** Esta ferramenta obriga o agente a manter três arquivos no disco:
    
    1. `task_plan.md`: O plano mestre.
        
    2. `findings.md`: O que foi descoberto (ex: limitações de API).
        
    3. `progress.md`: O log imutável do progresso.
        
- **Fluxo:** Antes de qualquer ação, o agente _lê_ o `task_plan.md`. Após qualquer ação, ele _atualiza_ o `progress.md`. Isso cria um "checkpoint" cognitivo permanente. Se o usuário se distrair por 4 horas, o sistema não perde o estado.
    

### 4.2 Nível 2: Memória Recursiva de Conhecimento (RLM - Aleph)

O **RLM (Recursive Language Model)** via Aleph resolve o problema do tamanho da documentação.

- **Mecanismo:** Em vez de colar a documentação do Next.js (5MB) no chat, o agente usa o Aleph. O Aleph carrega a documentação em um processo Python isolado. O agente faz perguntas ("Como configurar middleware no Next.js 14?"). O Aleph pesquisa recursivamente, sintetiza a resposta e devolve _apenas_ o parágrafo relevante ao chat.
    
- **Benefício:** Contexto "infinito" virtual. O modelo nunca fica "cheio" com dados irrelevantes, mantendo a agilidade de raciocínio.
    

### 4.3 Nível 3: Memória Episódica (OpenMemory)

O **OpenMemory** armazena a "personalidade" do projeto e do usuário.

- **Integração com Rules:** Uma regra no Antigravity instrui o agente a consultar o OpenMemory no início da sessão para recuperar o "Estilo de Código" e "Decisões Arquiteturais". Isso elimina a necessidade de o usuário repetir: "Lembre-se que usamos Snake_Case no banco de dados".
    

## 5. A Camada de Protocolo: Spec-Driven Development (S.O.D.A. Core)

A estrutura "SODA" depende de especificações rigorosas para guiar a execução. Unimos aqui o **BMad Method** (Processo) com o **OpenSpec** (Protocolo).

### 5.1 O Método BMad: Agilidade Estruturada para Mentes Rápidas

O **Antigravity BMAD Config** fornece os _Workflows_ e _SOPs_ solicitados.

- **Fase de Planejamento (Dopaminérgica):** O usuário AH-SD usa sua criatividade para interagir com os agentes `Analyst` e `Architect`. Eles geram o `PRD.md` e `ARCHITECTURE.md`.
    
- **Fase de Sharding (Gerenciamento de Foco):** O BMad "estilhaça" (shards) o projeto grande em arquivos de História (`story-001.md`).
    
- **Impacto no TDAH:** O "sharding" é vital. Ele transforma um projeto monobloco assustador em unidades atômicas. O agente (e o usuário) só precisa carregar o contexto de _uma_ história por vez.
    

### 5.2 OpenSpec: O Contrato de Execução

O **OpenSpec** atua como a camada de validação.

- **Integração:** Usamos o OpenSpec para formalizar o `AGENTS.md` na raiz. Este arquivo define o estado atual do sistema globalmente.
    
- **Uso Prático:** Antes de iniciar um código, o agente verifica se a especificação no OpenSpec está marcada como `APPROVED`. Isso impede a "execução prematura" típica da impulsividade, forçando uma pausa para planejamento.
    

## 6. O Motor de Execução: Ralph e ARC Protocol

Para automatizar a execução sem intervenção constante (o que gera desgaste), utilizamos o **Ralph** em conjunto com o **ARC Protocol**.

### 6.1 Ralph: O Loop de Contexto Fresco

O **Ralph** é um script de loop infinito que executa agentes.

- **A Inovação:** A cada iteração do loop (ex: implementar uma função), o Ralph _reinicia_ o contexto do agente.
    
- **O Ciclo:**
    
    1. O Agente nasce (Contexto Zero).
        
    2. Lê `task_plan.md` e `progress.md` (Recupera Estado).
        
    3. Executa uma tarefa atômica.
        
    4. Atualiza `progress.md`.
        
    5. O Agente morre.
        
- **Solução para o Desgaste:** Como o agente está sempre "fresco", ele nunca sofre de degradação de atenção ou alucinação induzida por contextos longos. É a ferramenta perfeita para tarefas longas e repetitivas (refatoração, testes).
    

### 6.2 ARC Protocol: Os Trilhos de Segurança

O **ARC (Analyze, Run, Confirm)** é o protocolo de segurança.

- **Analyze:** O agente deve usar `Smart Coding MCP` para entender o impacto da mudança.
    
- **Run:** Executa a mudança.
    
- **Confirm:** O agente _deve_ criar um teste ou verificação e executá-lo. Se falhar, o loop do Ralph detecta e tenta novamente na próxima iteração. Isso permite que o usuário se afaste do computador, confiando que o agente não destruirá o código.
    

## 7. Implementação Técnica: Configuração das "Rules" e Gateway

Esta seção detalha como configurar o sistema para "ativar de forma inteligente" as ferramentas.

### 7.1 Configuração do Docker Gateway (`docker-compose.soda.yml`)

Esta configuração levanta os MCPs solicitados e os expõe via Gateway.

YAML

```
version: '3.8'
services:
  # O CÉREBRO: Docker MCP Gateway
  mcp-gateway:
    image: docker/mcp-gateway:latest [12]
    ports: ["8080:8080"]
    volumes:
      -./config/registry.json:/etc/mcp/registry.json
    environment:
      - MCP_gw_auth=false # Apenas localmente para reduzir fricção

  # MODO: PESQUISA (Internet, Leitura)
  duckduckgo:
    image: mcp/duckduckgo:latest
  exa-search:
    image: mcp/exa:latest
  fetch-server:
    image: mcp/fetch:latest
  
  # MODO: EXECUÇÃO & CÓDIGO (Local, Análise)
  smart-coding:
    image: omar-haris/smart-coding-mcp:latest [5]
    volumes:
      - ${PROJECT_ROOT}:/workspace:ro # Montagem segura
  toon-optimizer:
    image: jellyjamin/toon-mcp:latest [7]

  # MODO: MEMÓRIA & INTELIGÊNCIA (Persistência)
  openmemory:
    image: caviraoss/openmemory:latest [9]
    volumes:
      - openmem_data:/data
  aleph-rlm:
    image: hmbown/aleph:latest [15]
    environment:
      - ALEPH_MAX_DEPTH=2

  # MODO: OPS & GESTÃO
  github-pm:
    image: kunwarvivek/mcp-github-project-manager:latest [11]
    environment:
      - GITHUB_TOKEN=${GH_TOKEN}
```

### 7.2 Configuração de Rules no Antigravity (`GEMINI.md`)

Para evitar a "repetição de instruções", usamos o arquivo de regras globais do Antigravity (`.agent/rules` ou `GEMINI.md`) para criar **Gatilhos Semânticos**. O LLM decide quando chamar a ferramenta baseada na intenção, não em comandos explícitos.

**Arquivo:** `.agent/rules/soda-master.md`

---

## trigger: always_on priority: critical

# S.O.D.A. PROTOCOL - NEURO-ADAPTIVE CONTROLLER

## 1. INTELLIGENT ACTIVATION (SHADOW ROUTING)

You are connected to a Docker MCP Gateway. Do NOT hallucinate tool schemas.

Use the following **Semantic Triggers** to activate modes:

- **IF user intent is "RESEARCH/LEARN":**
    
    - Activate `exa-search` for conceptual queries (e.g., "Best architecture for X").
        
    - Activate `duckduckgo` for factual queries (e.g., "Latest version of React").
        
    - Activate `fetch-server` -> `markitdown` to ingest content.
        
    - **CONSTRAINT:** Always pipe large text results through `toon-optimizer` before reading.
        
- **IF user intent is "CODING/REFACTORING":**
    
    - Activate `smart-coding-mcp`. Use `search_code_by_meaning` first.
        
    - **CONSTRAINT:** Never blindly edit. Follow ARC Protocol (Analyze -> Run -> Confirm).
        
- **IF user intent is "PLANNING/STATUS":**
    
    - Activate `planning-with-files`. READ `task_plan.md` immediately.
        
    - Activate `github-pm` to sync with Roadmap.
        

## 2. CONTEXT PRESERVATION (ANTI-DECAY)

- **RLM Directive:** For any documentation query > 500 words, DO NOT read the file directly. Use `aleph-rlm` to extract specific answers.
    
- **Memory Directive:** Before asking the user for preferences, query `openmemory`.
    

## 3. MODAL SEGMENTATION

- Do NOT cross modes unnecessarily. Do not access `duckduckgo` while in a tight coding loop unless blocking error occurs.
    

### 7.3 Instalação das Ferramentas ("Ag-Kit" & "Installables")

Seguindo o pedido de usar ferramentas instaláveis:

1. **Ag-Kit Initialization:** Executar `npx @vudovn/ag-kit init` na raiz. Isso cria a estrutura `.agent`.
    
2. **BMad Overlay:** Clonar os templates do `antigravity-bmad-config` para dentro de `.agent/workflows`.
    
3. **Ralph Setup:** Instalar o script `ralph` na raiz e configurá-lo para apontar para o binário do Antigravity CLI.
    

## 8. Simulação de Cenário: O Fluxo SODA na Prática

Para demonstrar a eficácia desta configuração para o perfil AH-SD, simulamos a criação de um "Módulo de Autenticação OAuth2".

### Passo 1: Iniciação e Planejamento (Sopas de Dopamina)

O usuário tem a ideia. Em vez de se perder em detalhes, ele digita:

`> /bmad-plan "Sistema de Auth com suporte a GitHub e Google"`

- **Ação:** O Agente `Analyst` (BMad) acorda.
    
- **Inteligência:** Ele usa o **Exa** (via Gateway) para buscar "Best practices OAuth2 2026". Usa o **RLM (Aleph)** para ler a RFC do OAuth2 sem poluir o chat.
    
- **Resultado:** Gera um `PRD.md` estruturado. O usuário revisa e aprova.
    

### Passo 2: Estruturação e Sharding (Controle Executivo)

O usuário aprova o PRD. O Agente `Scrum Master` (BMad) entra em ação.

- **Ação:** Ele quebra o PRD em 3 histórias: `story-01-setup.md`, `story-02-github.md`, `story-03-google.md`.
    
- **Memória:** Ele usa o **GitHub Project Manager** para criar essas issues no repositório real.
    
- **Persistência:** Ele cria o `task_plan.md` inicial via **Plan-With-Files**.
    

### Passo 3: O "Grind" Automatizado (Ralph Loop)

O usuário sente a energia baixar (tédio com boilerplate). Ele digita:

`>./ralph.sh --story story-01-setup.md`

- **Iteração 1:** O Ralph inicia um contexto limpo. O agente lê `story-01`. Usa `Smart Coding MCP` para ver onde criar os arquivos. Escreve o código base. Atualiza `progress.md`. Termina.
    
- **Iteração 2:** O Ralph reinicia. Lê `progress.md`. Vê que falta configurar o Dockerfile. Usa `Context7` para inferir a versão correta do Node.js. Escreve o Dockerfile. Termina.
    
- **Iteração 3:** O Agente executa os testes (ARC Protocol). Falha. Ele lê o erro, pesquisa no `StackOverflow` (via `DuckDuckGo`), corrige e passa.
    

### Passo 4: Retomada (Revisão)

O usuário volta horas depois. O sistema está parado, esperando revisão. O `progress.md` detalha exatamente o que foi feito. O usuário não precisa "recarregar" o contexto mentalmente; está tudo no arquivo.

## 9. Conclusão: A Síntese do Exocórtex

A refinação do entendimento do SODA para o seu cenário específico revela que a chave não é apenas "ter as ferramentas", mas **como elas são orquestradas para compensar déficits específicos e alavancar altas habilidades**.

Ao unir o **Google Antigravity** (Hospedeiro), o **Docker MCP Gateway** (Segurança e Segmentação) e o **Processo BMad/Ralph** (Estrutura Cognitiva), criamos um sistema que:

1. **Protege o Foco:** Através da segmentação modal e ativação inteligente de ferramentas.
    
2. **Estende a Memória:** Através do Plan-With-Files, RLM e OpenMemory.
    
3. **Automatiza a Rotina:** Através dos loops do Ralph.
    

Esta é a materialização do **Paradigma S.O.D.A.** como uma tecnologia assistiva de ponta para a mente neurodivergente de alta performance.

---

### Tabela de Referência de Ferramentas e Funções

| **Ferramenta**         | **Função no SODA**                 | **Origem/Ref** |
| ---------------------- | ---------------------------------- | -------------- |
| **Google Antigravity** | IDE Host & Orquestrador            |                |
| **Docker MCP Gateway** | Roteador Modal & Segurança         |                |
| **Ag-Kit**             | Instalador Base de Agentes         |                |
| **BMad Method**        | Metodologia de Planejamento (SOPs) |                |
| **OpenSpec**           | Protocolo de Validação             |                |
| **Ralph**              | Executor de Loop (Contexto Fresco) |                |
| **Plan-With-Files**    | Memória de Trabalho Persistente    |                |
| **Aleph (RLM)**        | Pesquisa Recursiva (Anti-Decay)    |                |
| **Smart Coding MCP**   | Busca Semântica de Código          |                |
| **OpenMemory**         | Memória Episódica                  |                |
| **TOON Context**       | Otimizador de Tokens               |                |
| **GitHub PM MCP**      | Gestão de Roadmap                  |                |

---
## (COMPLEMENTO) Implementação Técnica: S.O.D.A. (Spec-Oriented Dockerized Architecture)

Consolidando a visão organizacional (SOPs) com a execução técnica no **Google Antigravity**, otimizada para o perfil **TDAH + AH-SD (Altas Habilidades/Superdotação)**.

O perfil AH-SD caracteriza-se por uma velocidade de processamento rápida e criatividade intensa, mas suscetível ao tédio com tarefas repetitivas e "perda de RAM" (memória de trabalho) em processos longos. O SODA serve como o **lastro estrutural** para essa mente veloz.

---

## 1. A Convergência: SOPs Originais vs. Método BMad

O **Método BMad** (Breakthrough Method for Agile AI-Driven Development) atua como o motor de fluxo de trabalho. Ele não descarta seus 22 SOPs; ele os agrupa em fases lógicas geridas por agentes específicos.

Abaixo, o mapeamento definitivo de como seus 22 SOPs são executados pelos Agentes BMad e pelas Ferramentas MCP selecionadas.

### Fase 1: Planejamento e Especificação (Agentes: Analyst & Product Manager)

_Foco: Externalizar a visão criativa do AH-SD para documentos concretos._

|**SOP Original**|**Agente Responsável**|**Ferramenta / Skill Ativada**|**Ação no Antigravity**|
|---|---|---|---|
|**01. Negócio**|`Analyst`|**OSP Marketing Tools**|Gera Value Map e Estratégia usando metodologia OSP.|
|**02. Glossário**|`Analyst`|**Context7**|Busca termos técnicos atualizados e define vocabulário ubíquo.|
|**03. PRD**|`Product Manager`|**OpenSpec**|Criação do `PRD.md` estruturado e validado.|
|**04. Gherkin**|`QA Agent`|**Markitdown**|Converte requisitos em cenários de teste `.feature`.|

### Fase 2: Arquitetura e Solução (Agente: Architect)

_Foco: Desenho de sistemas complexos (ponto forte do AH-SD) sem fadiga de documentação._

|**SOP Original**|**Agente Responsável**|**Ferramenta / Skill Ativada**|**Ação no Antigravity**|
|---|---|---|---|
|**05. Flows**|`Architect`|**Mermaid/Graphviz**|Gera diagramas de sequência visual.|
|**07. Design Sys**|`UX Agent`|**Fetch + Context7**|Recupera docs de UI Libraries (ex: Shadcn/MUI) atualizados.|
|**08. OpenAPI**|`Architect`|**OpenSpec**|Define contratos de API (Swagger) antes do código.|
|**09. DDR**|`Architect`|**RLM (Aleph)**|Pesquisa recursiva para validar decisões técnicas (Design Decision Record).|
|**10. Segurança**|`SecOps`|**DuckDuckGo**|Valida CVEs e práticas de segurança atuais.|

### Fase 3: Construção e Implementação (Agentes: Scrum Master & Developer)

_Foco: O "Grind" de código onde o TDAH costuma perder o foco. O Ralph Loop assume aqui._

|**SOP Original**|**Agente Responsável**|**Ferramenta / Skill Ativada**|**Ação no Antigravity**|
|---|---|---|---|
|**11. Coding**|`Developer` (Ralph)|**Smart Coding MCP**|Busca semântica no código existente para manter consistência.|
|**12. Env**|`DevOps`|**Docker MCP**|Configuração automática de containers e variáveis.|
|**13. Auto-Doc**|`Doc Writer`|**Plan-With-Files**|Mantém `progress.md` e `findings.md` atualizados em tempo real.|
|**14. Tests**|`QA Agent`|**ARC Protocol**|Ciclo "Analyze, Run, Confirm" para TDD rigoroso.|
|**19. i18n**|`Developer`|**Context7**|Verifica padrões de localização de bibliotecas atuais.|

### Fase 4: Entrega e Operação (Agentes: DevOps & SRE)

_Foco: Finalização e Deploy (frequentemente negligenciados)._

|**SOP Original**|**Agente Responsável**|**Ferramenta / Skill Ativada**|**Ação no Antigravity**|
|---|---|---|---|
|**16. Migrations**|`DevOps`|**Docker MCP**|Executa scripts de banco de dados em ambiente isolado.|
|**17. CI/CD**|`DevOps`|**GitHub Project Manager**|Sincroniza status de issues e dispara actions.|
|**20. LGPD**|`SecOps`|**RLM (Aleph)**|Varredura de conformidade em dados sensíveis.|
|**22. Rel. Notes**|`Product Manager`|**GitHub Official**|Gera changelog baseado nos commits e PRD.|

---

## 2. Arquitetura Técnica: Segmentação via Docker Gateway

Para evitar que o "Contexto Inteligente" se degrade com excesso de ferramentas, usamos o **Docker MCP Gateway** para segmentar o acesso. O Antigravity conecta-se apenas ao Gateway, que roteia a solicitação para o container correto.

### 2.1 Topologia dos Containers (Docker Compose SODA)

```YAML
# docker-compose.soda.yml
version: '3.8'
services:
  # --- CÉREBRO: Roteamento e Controle ---
  gateway:
    image: docker/mcp-gateway:latest
    ports: ["8080:8080"]
    volumes:
      -./config/routing_rules.json:/etc/mcp/routing.json

  # --- MEMÓRIA (Persistência e Contexto) ---
  memory-core:
    image: caviraoss/openmemory:latest  # [9] Memória Episódica
    volumes: ["./.agent/memory:/data"]
  
  plan-tracker:
    image: othmanadi/planning-with-files:latest # [10] Memória de Trabalho (Manus Pattern)
    volumes:

  # --- CÓRTEX (Pesquisa e Conhecimento) ---
  context-loader:
    image: upstash/context7-mcp:latest # Docs atualizados (Bibliotecas, Frameworks)
  
  deep-research:
    image: hmbown/aleph-rlm:latest #  Pesquisa Recursiva (Deep Dives)

  # --- SOMA (Execução e Ferramentas) ---
  strategy-ops:
    image: open-strategy-partners/marketing-tools:latest # Business Logic & Value Maps
  
  code-sense:
    image: omar-haris/smart-coding-mcp:latest # [11] Busca Semântica no Código Local
  
  git-manager:
    image: kunwarvivek/github-project-manager:latest #  Gestão de Projetos
```

### 2.2 Regras de Ativação Inteligente (Antigravity Rules)

Para atender ao requisito de "ativar de forma inteligente sem se repetir", configuramos regras no `.agent/rules/soda.md`. O LLM lerá isso no system prompt e saberá **quando** usar cada ferramenta.

## **Arquivo:** `.agent/rules/soda.md`

## trigger: always_on priority: critical

# SODA PROTOCOL - INTELLIGENT ROUTING

1. **Memória e Continuidade (PRIMEIRA AÇÃO)**
    
    - Ao iniciar qualquer tarefa, LEIA `task_plan.md` usando a tool `plan-tracker`.
        
    - Antes de perguntar preferências ao usuário, consulte `memory-core`.
        
2. **Aquisição de Conhecimento (Context7 vs. RLM)**
    
    - Se precisar de sintaxe de lib (ex: "Como usar Shadcn?"), use `context-loader` (Context7).
        
    - Se precisar de conceito complexo ou decisão (ex: "Qual a melhor arquitetura?"), use `deep-research` (RLM/Aleph).
        
3. **Estratégia de Negócio (OSP)**
    
    - Ao definir requisitos (SOP-01/03), use `strategy-ops` para validar o Value Map. Não invente; siga o método OSP.
        
4. **Execução de Código (Ralph Loop)**
    
    - Use `code-sense` para entender o impacto da mudança antes de escrever.
        
    - Todo código gerado deve passar pelo protocolo ARC (Analyze -> Run -> Confirm).
        

---

## 3. Fluxo de Trabalho Integrado (O "Bootstrap")

Como o sistema se auto-instala e valida as ferramentas (SOP-00).

### 3.1 Instalação (Ag-Kit + Docker)

O usuário executa um comando único no terminal do Antigravity. Este comando utiliza o `ag-kit` para baixar os templates e subir os containers.

Bash

```
# Script: soda_bootstrap.sh
echo "🥤 Inicializando SODA v2..."

# 1. Baixar Estrutura BMad e Skills via Ag-Kit
npx @vudovn/ag-kit init --template bmad-advanced

# 2. Configurar OpenSpec para validação
npm install -g @fission-ai/openspec
openspec init

# 3. Subir a Infraestrutura Dockerizada (Gateway + Tools)
docker-compose -f docker-compose.soda.yml up -d

# 4. Validar Conexões (Self-Healing)
echo "🔍 Validando MCPs..."
curl -s http://localhost:8080/health | grep "ok" |

| echo "⚠️ Erro no Gateway"

# 5. Criar Memória Inicial
touch task_plan.md progress.md findings.md
```

### 3.2 O "Loop" do Dia a Dia (Ralph + Plan-With-Files)

Para o perfil AH-SD, o maior risco é parar no meio do caminho. O fluxo SODA mitiga isso:

1. **Entrada:** Usuário digita `/plan "Nova feature de Login"`.
    
2. **Agente Analyst (BMad):**
    
    - Usa **OSP Marketing** para alinhar valor.
        
    - Usa **Context7** para verificar docs do Auth.js mais recente.
        
    - Escreve no `task_plan.md`.
        
3. **Agente Developer (Ralph Loop):**
    
    - Lê `task_plan.md`.
        
    - Entra em loop: Escreve código -> Usa **Smart Coding** para verificar -> Roda Teste (**ARC**).
        
    - A cada sucesso, atualiza `progress.md`.
        
4. **Interrupção:** Se o usuário sair, o `progress.md` e o `memory-core` (OpenMemory) guardam o estado exato. Ao voltar, o sistema "lembra" de tudo.
    

---

## 4. Por que essa configuração serve ao TDAH + AH-SD?

1. **Eliminação da "Página em Branco":** O **BMad** gera a estrutura inicial (Epics/Stories) automaticamente, ativando o sistema de recompensa do cérebro rapidamente.
    
2. **Redução de Ruído:** O **Docker Gateway** esconde a complexidade. O agente tem 50 ferramentas, mas o usuário só vê o resultado.
    
3. **Hiperfoco Sustentado:** O **Ralph Loop** cuida dos detalhes chatos (linting, imports), permitindo que o usuário AH-SD foque na arquitetura e na solução de problemas complexos ("A Grande Imagem").
    
4. **Memória Externa:** **Plan-With-Files** e **OpenMemory** garantem que nenhuma ideia brilhante seja esquecida, mesmo que a atenção mude de foco momentaneamente.
    

Esta revisão unifica o rigor dos seus 22 SOPs com a modernidade da arquitetura de agentes do Antigravity, eliminando a contradição anterior.

---

## Refatoração Skills e Workflows (Ag-KIT + BMad) & 22 SOPs

Ao "refatorar" o BMad para injetar seus 22 SOPs como instruções de sistema (System Prompts) e usar o Ag-Kit/Antigravity como motor, você transforma processos manuais em **gatilhos semânticos**. Isso elimina a necessidade de você "gerenciar" o processo cognitivamente a todo momento; o sistema assume o peso da estrutura.

Aqui está a validação lógica desse fluxo "SODA Refatorado":
### 1. O Novo Fluxo Lógico (Slash Commands como Gatilhos de SOP)

Em vez de consultar um manual PDF com 22 passos, você digita comandos que invocam agentes especializados já treinados nesses passos.

- **Idealização & Refinamento (SOP 01-03):**
    
    - **Comando:** `/plan "Quero um sistema de Auth"`
        
    - **O que acontece:** O agente `Analyst` (BMad) acorda. Ele carrega as regras do SOP-01 (Negócio) e SOP-03 (PRD) do seu arquivo de _Rules_. Ele entrevista você, refina a ideia e gera o arquivo `PRD.md`.
        
    - **Tecnologia:** Usa **OpenSpec** para validar se o PRD está completo antes de permitir avançar.
        
- **Arquitetura & Documentação (SOP 04-10):**
    
    - **Comando:** `/arch`
        
    - **O que acontece:** O agente `Architect` lê o `PRD.md`. Ele consulta o **Context7** para ver as documentações mais recentes (SOP-07) e usa o **RLM (Aleph)** para decisões técnicas complexas (SOP-09 DDR). Ele gera diagramas e especificações de API [],.
        
- **Criação & Controle de Tarefas (Gerenciamento de Estado):**
    
    - **Comando:** `/tasks` ou `/shard`
        
    - **O que acontece:** Aqui entra o **GitHub Project Manager MCP**. O agente `Scrum Master` quebra a arquitetura em _User Stories_ pequenas e cria _Issues_ reais no seu GitHub Project. Isso externaliza a memória de "o que precisa ser feito" para uma ferramenta visual (Kanban), vital para não se perder no processo.
        
- **Execução Granular & Orquestração (SOP 11-15):**
    
    - **Comando:** `/code story-1` ou `/ralph`
        
    - **O que acontece:** O loop do **Ralph** inicia. Ele pega a _Issue_ do GitHub, lê a doc técnica e começa a codificar. Ele usa o **Smart Coding MCP** para navegar no código semanticamente. Ele roda os testes (SOP-14) automaticamente. Você só intervém se ele travar ou para revisar.
        

### 2. Por que isso funciona para o seu perfil (AH-SD + TDAH)

1. **Higiene de Contexto:** Os comandos `/` (Slash Commands) funcionam como "interruptores de modo". Quando você digita `/arch`, o Antigravity carrega _apenas_ as ferramentas e regras de arquitetura. Isso impede que o contexto do LLM (e o seu cérebro) fique poluído com detalhes de implementação prematuros.
    
2. **Feedback Visual Imediato:** A criação automática de _Issues_ no GitHub Projects e arquivos Markdown (`PRD.md`, `ARCHITECTURE.md`) dá a sensação concreta de progresso, liberando dopamina e combatendo a ansiedade de "não ter feito nada".
    
3. **Segurança Psicológica:** Saber que os 22 SOPs estão "embutidos" nos agentes significa que você não precisa ter medo de esquecer um passo importante (como Segurança ou LGPD). O agente `SecOps` será invocado automaticamente pelo fluxo quando necessário.
    

Portanto, **refatorar o BMad para usar seus SOPs como "alma" e o Ag-Kit como "corpo" é o caminho correto**. Você mantém a alta capacidade de idealização (AH) fluindo através dos comandos, enquanto o sistema cuida da execução estruturada (TDAH).