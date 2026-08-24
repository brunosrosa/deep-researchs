# Estudo Arquitetural de Integração: Transformação do Framework Souls MC através dos Padrões do DeepSeek Harness e Runtime Rust Nativo

## 1. Análise Arquitetural Aprofundada do DeepSeek Harness e Meta-Framework Cordis

O DeepSeek Harness (`dsh`) estabelece uma reformulação profunda no ecossistema de ambientes de execução para agentes autonomizados baseados em modelos de linguagem. Ao contrário de abordagens convencionais (como LangChain, AutoGPT ou sistemas rígidos de ReAct) que dependem de cadeias fixas de invocação, o DeepSeek Harness opera sobre um microkernel desprovido de um núcleo privilegiado, estendendo a sua funcionalidade através do meta-framework Cordis.

A premissa central determina que cada subsistema operacional — desde a adaptação de modelos até ao registo de ferramentas, persistência de logs de sessão, políticas de _sandbox_ e o próprio ciclo de decisão do agente — existe sob a forma de _plugins_ modulares, dinamicamente substituíveis em tempo de execução sem requerer a alteração da base de código principal.

### 1.1 Composibilidade Espaciotemporal e Gestão Revertível de Efeitos

O suporte fundamental do DeepSeek Harness assenta na formalização teórica da composibilidade espaciotemporal desenvolvida para o meta-framework Cordis:

- **Composibilidade Temporal (Efeitos Revertíveis):** Assegura que qualquer alteração de estado ou efeito secundário provocado pela montagem de um _plugin_ (como escutar eventos, abrir _sockets_ de comunicação ou registar rotas de API) seja revertido na totalidade quando o _plugin_ é desativado. O tempo de execução do Cordis monitoriza cada transformação de contexto via `ctx.effect()` e associa-lhe uma função de limpeza disposta numa pilha de execução no formato _Last In, First Out_ (LIFO). A remoção de um componente resulta no encerramento ordenado de conexões de rede e na libertação de recursos sem a necessidade de reiniciar o processo principal.
- **Composibilidade Espacial (Coefectos Reativos):** Gere reativamente as dependências cruzadas entre múltiplos _plugins_ através de coefectos. No contexto partilhado (`ctx`), cada componente declara formalmente os serviços que consome e disponibiliza via injeção/devolução (`ctx.inject` / `ctx.provide`). Sempre que um serviço no contexto sofre mutação, substituição ou desativação, os componentes dependentes são notificados instantaneamente, reconfigurando os seus estados internos de forma previsível e inercial.

### 1.2 Sistema de Camadas: Perfis, Pacotes e Correções (_Patches_)

A composição de uma instância ativa do DeepSeek Harness é resolvida durante a fase de inicialização através da construção de uma árvore hierárquica de _plugins_. A resolução de configurações obedece a uma ordem estrita de sobreposição:

 - **Perfis (_Profiles_):** Definem composições nomeadas mantidas no diretório do Harness, mapeando os conjuntos de pacotes a carregar. O sistema disponibiliza perfis pré-configurados, tais como `web` para interfaces de navegador e `headless` para execuções automatizadas via CLI.
 - **Pacotes (_Bundles_):** Formatos de distribuição de código e ficheiros de configuração do Cordis declarados no `package.json` sob a chave `dsh`. O pacote base `dsh-base` constitui a camada obrigatória primária em qualquer perfil, fornecendo adaptadores para modelos, registo de ferramentas, persistência e regras de aprovação.
 - **Correções (_Patches_):** Através do ficheiro `cordis.patch.yml`, o sistema permite substituir as linhas de configuração de _plugins_ existentes ou inserir novas entradas sem alterar o código dos pacotes subjacentes.

 A ordem de resolução de camadas parte de uma lista vazia e aplica sequencialmente os pacotes listados no perfil, seguidos pelo `cordis.patch.yml` do perfil, pelo `cordis.patch.yml` global do utilizador e, finalmente, por sobreposições especificadas na linha de comandos via `--patch`.

### 1.3 Costuras de Capacidade (_Capability Seams_)

O desacoplamento arquitetural é viabilizado pelo conceito de costuras (_seams_), que estabelecem fronteiras rígidas de abstração divididas em três papéis:

1. **Definição de Serviço (_Service Definition_):** Interface que estabelece os contratos funcionais.
2. **Provedor de Serviço (_Service Provider_):** Implementação concreta da interface.
3. **Consumidor (_Consumer_):** Módulo ou ferramenta que consome o serviço a partir do contexto partilhado (`ctx`).

A alteração de um Provedor de Serviço altera automaticamente o comportamento de todos os Consumidores no sistema sem exigir modificações nas implementações internas.

### 1.4 Invariante do Log de Sessão e Execução Programática (Code Mode SDK)

O Harness impõe a invariante de que qualquer informação apresentada ao modelo de linguagem deve ser estritamente reconstruível a partir de um log de eventos de sessão imutável (_append-only_), no qual a projeção do histórico é calculada pela função `deriveMessages()`.

Além disso, o sistema introduz o **Code Mode SDK**. Em vez de forçar o agente a executar chamadas de ferramentas de forma iterativa passo a passo no padrão ReAct tradicional, as ferramentas do sistema são expostas como bibliotecas tipadas. O modelo de linguagem escreve programas que orquestram e encadeiam múltiplas chamadas de ferramentas dentro de uma _sandbox_ segura, reduzindo drasticamente o consumo de _tokens_ e a latência de execução.

### 1.5 Regras e Diretrizes Arquiteturais Essenciais do Harness

 Para garantir a resiliência e a modularidade do ambiente de execução, o Harness exige a adesão a quatro regras operacionais:
 
 1. **Eliminação do Núcleo Privilegiado:** Nenhum módulo detém privilégios diretos sobre a orquestração do agente. Todas as funcionalidades existem como serviços registrados no contexto partilhado (`ctx`).
 2. **Gestão Revertível LIFO:** Qualquer alteração efetuada nos estados do agente deve registrar uma função de limpeza no Cordis, garantindo a desmontagem limpa de componentes sem deixar rastros na memória.
 3. **Invariante do Log de Sessão ("Model-Visible Means Logged"):** É proibido incluir informações no contexto da LLM que não sejam estritamente reconstruíveis a partir do log de eventos duradouro.
 4. **Orquestração Programática:** Adoção preferencial do Code Mode SDK para encadear múltiplas operações num único programa em TypeScript/JavaScript, otimizando o consumo de recursos.

## 2. Mapeamento Conceitual do Souls MC e Hierarquia Agêntica

O **Souls MC** integra a profundidade psicológica e cognitiva herdada do ecossistema Open-Sable — como a persistência de identidade (`SOUL.md`), a auto-reflexão contínua, a memória cognitiva e o acompanhamento das esferas de vida do utilizador humano — com a infraestrutura de microkernel do DeepSeek Harness.

### 2.1 Hierarquia de Agentes no Souls MC

A arquitetura do Souls MC organiza a orquestração agêntica em três camadas distintas de responsabilidade:

1. **Agente Master (Interface do Humano):** Agente primário responsável pelo acolhimento, empatia e compreensão do contexto geral do utilizador. Ele mantém a visão consolidada das "esferas de vida" (Trabalho, Finanças, Saúde, Projetos), convertendo intenções humanas em objetivos de alto nível.
2. **Agente Orquestrador (Gestor de Tarefas e FinOps):** Recebe os objetivos do Agente Master e atua na costura de equipas (`ctx.agentTeams`). Ele decompõe objetivos em quadros de tarefas (_Task Boards_), consulta políticas de custo (FinOps) para selecionar os modelos de LLM mais eficientes para cada tarefa e orquestra a execução.
3. **Sub-Agentes Dinâmicos (Executores de Tarefas):** Instâncias temporárias geradas sob demanda para resolver subtarefas específicas. Cada sub-agente recebe um **Contexto Escopado (_Scoped Context_)** contendo apenas as ferramentas, permissões e modelos de LLM estritamente necessários para a sua tarefa.

#### Sequência do Ciclo de Eventos do Agente (_Event Lifecycle_)

A execução de tarefas no Harness divide-se estruturalmente em Turnos (_Turns_) e Passos (_Steps_). Um Passo compreende uma requisição única à LLM e a resolução do lote de ferramentas solicitadas, enquanto um Turno envolve o ciclo completo de resolução da requisição do utilizador.

A execução do ciclo encadeia rigorosamente a seguinte sequência de eventos:
 1. `turn/start`: Início da intenção do utilizador.
 2. `agent/pre-step`: Interceptação por _plugins_ observadores/metacognitivos para reescrever requisições.
 3. `step/start`: Gravação das entradas no log duradouro e derivação do histórico via `deriveMessages()`.
 4. `agent/request` e `llm/stream`: Invocação e _streaming_ do modelo de linguagem.
 5. `tools/pre-execute` $\rightarrow$ `tools/execute` $\rightarrow$ `tools/post-execute`: Pipeline de execução e validação de ferramentas.
6. `step/end`: Finalização do passo atual.
 7. `agent/turn-stopping` e `turn/end`: Verificação de conclusão e encerramento do turno.

 O Harness suporta múltiplos modos funcionais, destacando-se o **Modo Padrão (_Standard_)**, o **Modo Mínimo (_Minimal_)**, o **Modo Criador (_Creator_)** e o **Modo Código (_Code Mode_)**.

### 2.2 O Prompt de Sistema Dinâmico (`born_prompt`) e Preservação do KV Cache

O `born_prompt` (prompt de sistema do agente) não é um ficheiro estático colado na inicialização. No Souls MC, o serviço `ctx.systemPrompt` atua como um agregador modular reativo.

Para evitar a perda do **KV Cache** (Prompt Cache) nos provedores de LLM — o que aumentaria os custos e a latência de resposta —, o `ctx.systemPrompt` organiza a compilação do prompt em blocos hierárquicos ordenados por volatilidade:

- **Prefixo Estático (100% Cacheável):** Instruções base do sistema, diretrizes imutáveis da Alma e esquemas globais de ferramentas. Este bloco é mantido rigorosamente fixo para garantir a correspondência de prefixo (_prefix matching_) nas APIs de LLM.
- **Seções de Estado de Longo Prazo (Rara Alteração):** Perfil do utilizador, crenças consolidadas da Alma e diretrizes éticas ativas fornecidas pelo serviço `ctx.soul`.
- **Sufixo Dinâmico (Alta Volatilidade):** Memórias de curto prazo injetadas pontualmente, contexto do turno atual e histórico de conversas derivadas via `deriveMessages()`.

|**Componente Souls MC**|**Papel no Harness/Cordis**|**Estratégia de Preservação e Execução**|
|---|---|---|
|**Identity Core (`SOUL.md`)**|Provedor de Serviço Revertível (`ctx.soul`)|Injeta tom e ética na seção de longo prazo do `ctx.systemPrompt`.|
|**MC Engine (Metacognição)**|Plugin Observador do Ciclo de Vida|Intercepta `agent/pre-step` e `agent/turn-stopping` para consolidar memórias sem corromper o prefixo do cache.|
|**Esferas do Humano & Tarefas**|Serviço `ctx.agentTeams`|Gerencia quadros de tarefas e caixas de correio de sub-agentes.|
|**FinOps & Roteamento**|Injeção de Contexto Escopado|O Orquestrador sobrescreve `ctx.llm` e `ctx.sandbox` em memória por subtarefa.

## 3. Decisão de Engenharia de Stack: Arquitetura Rust Nativa vs. Sidecar Node/Bun

O Souls MC é construído sobre a pilha desktop de alta performance **Rust + Tauri v2 + Svelte 5**. Avaliou-se o impacto de integrar a lógica do Harness via processo filho separado (_Sidecar_ em Node.js/Bun) versus uma **implementação nativa em Rust**.

### 3.1 Análise de Impacto do Modelo Sidecar (Node.js/Bun)

- **Distribuição e Instalação:** Exige empacotar o runtime da V8 (Node.js ou Bun) compilado para cada sistema operativo. O instalador da aplicação cresce de **~15 MB para ~150 MB**.
- **Consumo de Recursos:** Adiciona um overhead permanente de **80 MB a 150 MB de RAM** apenas para manter o processo do Sidecar ocioso, somando-se ao consumo da WebView do Tauri.
- **Gerenciamento de Processos:** Exige que a aplicação em Rust gerencie a saúde, erros e reinicialização de processos filhos, adicionando complexidade de IPC (Comunicação Inter-Processos).

### 3.2 A Solução Nativa em Rust (`cordis-rs` + `deno_core`)

A arquitetura nativa em Rust é tecnicamente superior e perfeitamente viável através de bibliotecas existentes no ecossistema Rust:

1. **Kernel Cordis Nativo (`cordis-rs`):** As garantias de composibilidade temporal e limpeza LIFO do Cordis mapeiam-se nativamente no Rust através do padrão **RAII (_Resource Acquisition Is Initialization_)** e da _trait_ `Drop`. Quando um plugin do Souls MC sai de escopo, o próprio compilador do Rust executa a ordem inversa de descarte (LIFO) de forma nativa e determinística. Os crates `cordis-rs-core` e `cordis-rs-framework` fornecem essa infraestrutura de microkernel diretamente em Rust.
2. **Execução do Code Mode SDK via `deno_core`:** Para permitir que o agente escreva e execute scripts TypeScript/JavaScript isolados para orquestrar ferramentas, o Souls MC utiliza o crate **`deno_core`**. O `deno_core` embuti a engine V8 diretamente no próprio binário em Rust. Os scripts gerados pelo agente rodam dentro de um _V8 Isolate_ na memória do próprio processo Rust, garantindo zero latência de IPC, consumo mínimo de RAM e isolamento de segurança em _sandbox_ sem requerer Node.js instalado na máquina do utilizador.

## 4. Ciclo Autônomo de Aprendizado e Evolução de Skills

A recuperação de erros no estilo "Ralph Loop" (tentativa -> erro -> leitura de _traceback_ -> nova tentativa) atua como um mecanismo primário de resiliência funcional. No entanto, o Souls MC implementa um **Ciclo de Evolução de Habilidades** orientado a métricas metacognitivas.

### 4.1 Métricas de Avaliação da Metacognição (`ctx.mc`)

Ao final de cada turno de tarefa (`agent/turn-stopping`), o motor de Metacognição analisa a trajetória registrada no log imutável de sessão através de quatro dimensões de desempenho:

1. **Eficiência FinOps:** Quantidade de _tokens_ consumidos e total de iterações (_Steps_) executadas. Tarefas orquestradas via programas do Code Mode SDK recebem pontuação superior por resolverem múltiplos passos num único turno.
2. **Determinismo e Estabilidade:** Número de retentativas exigidas no "Ralph Loop" até a conclusão com sucesso. Scripts que passam sem erros na primeira execução ganham prioridade para cristalização.
3. **Latência de Execução:** Tempo decorrido para a execução do algoritmo dentro do ambiente seguro `ctx.sandbox`.
4. **Alinhamento e Feedback Humano:** Avaliação do feedback explícito (aprovações) ou implícito (ausência de pedidos de correção por parte do utilizador).

### 4.2 Pipeline de Cristalização e Refinamento de Skills

```
[Entrada de Tarefa] -> [Code Mode SDK (Geração de Script TS)]
                             |
                             v
                 [Execução na Sandbox V8]
                             |
                   (Sucesso na Tarefa)
                             |
                             v
           [Avaliação Metacognitiva (ctx.mc)]
         (Métricas: FinOps + Latência + Estabilidade)
                             |
                             v
            [Refatoração para Função Tipada]
                             |
                             v
             [Teste Unitário Silencioso V8]
                             |
                             v
            [Armazenamento em /skills da Alma]
```

Quando um script dinâmico atinge pontuações elevadas nas métricas, o motor metacognitivo aciona um sub-agente refatorador que converte o script temporário numa **Skill Parametrizada Reutilizável**. Essa Skill é submetida a um teste unitário silencioso no `deno_core` e armazenada no repositório permanente de habilidades do agente (`skills/`). Em interações futuras, o agente não precisa regenerar a lógica do zero: ele carrega a Skill compilada no `ctx.tools`, garantindo execuções mais rápidas, baratas e determinísticas.

## 5. Mapeamento de Engenharia e Instruções Estruturadas para Agentes em IDE (v2)

Para orientar os agentes de programação em IDEs (Cursor, Windsurf, Claude Code) na refatoração do repositório em Rust (`souls-mc`), estabelece-se a seguinte estrutura de diretórios e o plano de implementação dividido em 4 fases:

- **`souls-mc/`**
    - **`crates/`**
        - `souls-core/`: Microkernel baseado em `cordis-rs-core`, gerador do `ctx`, barramento de eventos e pilha LIFO de descartes.
        - `souls-session/`: Log de eventos imutável (_append-only_), persistência e projeção `deriveMessages()`.
        - `souls-prompt/`: Compilador modular `ctx.systemPrompt` com preservação de KV Cache.
        - `souls-seams/`: Contratos e interfaces em Rust (`SoulService`, `MetacognitionService`, `SandboxService`).
        - `souls-plugins/`:
            - `plugin-soul/`: Carregador dinâmico do `SOUL.md` e reator de identidade.
            - `plugin-mc/`: Motor metacognitivo, cálculo de métricas e cristalizador de habilidades.
            - `plugin-code-sdk/`: Runtime V8 embutido via `deno_core` para o Code Mode SDK.
    - **`src-tauri/`**: App Tauri v2 em Rust integrando a interface de utilizador (Svelte 5) com os crates do Souls MC.

### Instruções Estruturadas de Engenharia para Agentes de IDE

#### FASE 1: KERNEL CORDIS EM RUST E DEFINIÇÃO DE COSTURAS DE SERVIÇO

**OBJETIVO**:
Implementar o microkernel do Souls MC em Rust utilizando as abstrações de composibilidade do Cordis e gerenciamento LIFO via RAII.

**REQUISITOS TÉCNICOS**:
1. No crate `crates/souls-core`, configurar o runtime de contexto partilhado (`Context` / `ctx`) integrado com `cordis-rs-core`:
    - Implementar o registo de serviços reativos (`ctx.provide`, `ctx.inject`).
    - Implementar a pilha de descarte de efeitos em ordem LIFO aproveitando os `Drop` traits nativos do Rust.
2. No crate `crates/souls-seams`, definir as interfaces funcionais (Traits):
    - `SoulService`: Métodos para obter tom de voz, estado emocional e seções de prompt da Alma.
    - `MetacognitionService`: Métodos para avaliação episódica, cálculo de métricas FinOps e gestão de Skills.
    - `SandboxService`: Métodos para execução isolada de scripts.
3. Garantir que a montagem e desmontagem de plugins limpe todos os handlers de eventos e conexões sem deixar resíduos de memória.

#### FASE 2: PLUGIN SOUL E COMPILADOR DE PROMPT COM KV CACHE PRESERVATION

**OBJETIVO**:
Converter a gestão da Alma num plugin dinâmico e implementar o montador do prompt de sistema otimizado para Prompt Caching.

**REQUISITOS TÉCNICOS**:
1. Criar o crate `crates/souls-plugins/plugin-soul` implementando a Trait `SoulService`.
2. No crate `crates/souls-prompt`, criar o serviço `ctx.systemPrompt`:
    - Estruturar a montagem do prompt de sistema em três blocos rígidos: Prefixo Estático (instruções base/ferramentas), Seções de Longo Prazo (Alma/Perfil) e Sufixo Dinâmico (Contexto/Histórico).
    - Garantir que modificações no `SOUL.md` atualizem apenas as Seções de Longo Prazo, preservando a imutabilidade do Prefixo Estático para reaproveitamento do KV Cache nas chamadas à LLM.
3. Associar o descarte do plugin da Alma à remoção automática das suas seções de prompt através da pilha LIFO do Cordis.

#### FASE 3: LOG DE SESSÃO IMUTÁVEL E MOTOR METACOGNITIVO DE APRENDIZADO

**OBJETIVO**:
Implementar o log de eventos imutável e o plugin de Metacognição orientado ao cálculo de métricas e evolução de habilidades.

**REQUISITOS TÉCNICOS**:
1. No crate `crates/souls-session`, implementar o motor de log imutável (_append-only_) e a função de projeção de mensagens `deriveMessages()`.
2. Criar o crate `crates/souls-plugins/plugin-mc` conectando o motor aos eventos de ciclo de vida do agente:
    - No evento `agent/turn-stopping`: Ler a trajetória do log, calcular o consumo de tokens, contagem de passos e latência de execução.
    - Se uma solução no Code Mode atingir notas elevadas de eficiência FinOps e estabilidade, acionar a refatoração do código para uma Skill tipada.
3. Gravar todas as reflexões e novas Skills no log imutável como eventos do tipo `mc/reflection-added` e `mc/skill-created`.

#### FASE 4: CODE MODE SDK EMBUTIDO COM DENO_CORE E INTEGRACAO TAURI V2

**OBJETIVO**:
Integrar o runtime V8 embutido via `deno_core` para o Code Mode SDK e conectar o microkernel à interface Svelte 5 via Tauri.

**REQUISITOS TÉCNICOS**:
1. No crate `crates/souls-plugins/plugin-code-sdk`, inicializar o `deno_core` para criar um ambiente isolado de V8 (_V8 Isolate_) na memória do próprio processo Rust.
2. Expor as ferramentas registadas em `ctx.tools` como funções TypeScript tipadas acessíveis dentro da sandbox V8.
3. Permitir que sub-agentes enviem scripts em TypeScript para serem executados no `deno_core`, devolvendo os resultados consolidados diretamente ao motor em Rust num único passo de turno.
4. Conectar o barramento de eventos do `souls-core` aos comandos IPC do Tauri v2, permitindo que a interface em Svelte 5 exiba logs de sessão, métricas de FinOps e quadros de tarefas em tempo real.

## 6. Conclusões e Diretrizes de Implementação

A arquitetura unifica a profundidade e empatia da camada de "Alma" com o rigor de engenharia do DeepSeek Harness e a performance nativa do ecossistema Rust.

As três diretrizes fundamentais para a execução do projeto são:
1. **Adesão Estrita à Arquitetura Nativa em Rust:** A utilização de `cordis-rs` e `deno_core` elimina a necessidade de rurntimes externos (Node.js/Bun), garantindo que a aplicação desktop mantêm o seu tamanho compacto (~15 MB), baixíssimo consumo de RAM e alta velocidade de execução.
2. **Otimização Contínua do KV Cache:** A estruturação rígida do `ctx.systemPrompt` em blocos por volatilidade garante que a dinâmica reativa da Alma e da Metacognição não comprometa o reaproveitamento do cache de prefixo nas APIs de modelos de linguagem.
3. **Evolução de Skills por Metacognição:** A transição do padrão ReAct tradicional para a execução programática no **Code Mode SDK**, combinada com o pipeline de refinamento do motor metacognitivo, assegura que o Souls MC reduza progressivamente os seus custos operacionais (FinOps) e aumente a sua autonomia à medida que resolve tarefas do utilizador.

