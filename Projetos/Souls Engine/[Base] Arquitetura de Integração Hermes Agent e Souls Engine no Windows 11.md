# Arquitetura e Engenharia de Integração: Hermes Agent e Souls Engine em Ambiente Bare-Metal Windows 11

## 1. Topologia de Sistemas e Anatomia de Interfaces do Hermes Agent

A convergência entre o orquestrador agêntico Hermes Agent e o motor de infraestrutura bare-metal Souls Engine exige uma delimitação formal de fronteiras baseada na anatomia interna do runtime do Hermes. O núcleo do Hermes Agent está estruturado na classe `AIAgent`, localizada em `run_agent.py`, responsável pela governança do loop de raciocínio, montagem do contexto de sistema, resolução de provedores e execução concorrente de ferramentas. Em vez de um invólucro genérico em torno de chamadas a modelos de linguagem, o runtime implementa abstrações especializadas de extensibilidade que operam de modo síncrono e assíncrono ao longo do ciclo de vida da interação.

A arquitetura do Hermes Agent estabelece uma divisão rigorosa entre quatro superfícies principais: o subsistema de memória persistente e transitória, o motor de gestão e compactação de contexto, o barramento de ferramentas externas orquestrado através do Model Context Protocol (MCP), e a infraestrutura de delegação de subagentes e slots auxiliares de inferência. Para que o Souls Engine atue como extensão de alto rendimento sem gerar concorrência destrutiva de estado ou duplicar rotinas internas, é indispensável analisar as premissas operacionais e os contratos formais dessas interfaces.

### 1.1 O Ciclo de Vida da Memória Nativa e Provedores Externos

O Hermes Agent articula a retenção de conhecimento através de dois mecanismos concorrentes: arquivos locais delimitados montados no prompt de sistema e provedores externos governados pela classe abstrata `MemoryProvider`, localizada em `agent/memory_provider.py`.

A memória primária é sustentada por dois arquivos formatados em Markdown, situados no diretório do perfil ativo:

* `MEMORY.md`: Destinado ao armazenamento de notas operacionais do agente, convenções do ambiente de desenvolvimento, arquiteturas de projeto e lições aprendidas, sujeito a um limite estrito de 2.200 caracteres (~800 tokens).
* `USER.md`: Reservado para o perfil do utilizador, incluindo preferências comunicacionais, diretrizes de trabalho e restrições de comportamento, delimitado a 1.375 caracteres (~500 tokens).

Ambos os arquivos utilizam o delimitador canónico `§` para segregar entradas atómicas. O Hermes Agent opera sob o padrão de *frozen snapshot*: no momento da inicialização da sessão, o conteúdo de `MEMORY.md` e `USER.md` é lido e injetado de forma imutável no bloco de sistema da conversa. Embora o modelo execute ações de modificação (`add`, `replace`, `remove`) que persistem as alterações imediatamente no sistema de arquivos, a representação injetada no contexto conversacional permanece inalterada até o encerramento da sessão. Essa imutabilidade pontual é indispensável para preservar o alinhamento de prefixo nos mecanismos de *prompt caching* de provedores como Anthropic, OpenAI e DeepSeek.

Paralelamente aos arquivos Markdown, o framework disponibiliza a interface de *Memory Provider Plugins*, permitindo a integração de backends de persistência estruturados e de longo alcance. A especificação da interface impõe contratos rigorosos de execução e concorrência:

* `initialize(session_id, **kwargs)`: Invocado na inicialização do agente, fornecendo o parâmetro `hermes_home` para isolamento de dados por perfil.
* `sync_turn(user, assistant, *, session_id="", messages=None)`: Disparado após a finalização de cada turno. A especificação do Hermes exige que este método seja estritamente não bloqueante. Qualquer operação intensiva de I/O, indexação vetorial ou computação relacional deve ser despachada para threads de segundo plano (daemon threads) para não degradar a latência percebida no diálogo.
* `prefetch(query, *, session_id="")` e `queue_prefetch(query, *, session_id="")`: Ganchos executados, respetivamente, antes da chamada de inferência e após o fechamento do turno, destinados a pré-aquecer índices ou recuperar memórias contextuais.
* `on_pre_compress(messages)`: Executado imediatamente antes da compactação da janela de contexto. Sob a especificação de versão 2 (`pre_compress_checkpoint_api_version = 2`), se o operador configurar `compression.checkpoint_required: true`, o gancho adota semântica *fail-closed*. Qualquer exceção não tratada na persistência bloqueia o processo de compactação destrutiva com o erro `BLOCKED_MISSING_PREREQUISITE`, preservando o histórico bruto original. Os provedores de versão 2 recebem dados normalizados, com filtros automáticos aplicados a ferramentas internas e sumários intermediários prévios marcados com a flag `_compressed_summary`.

### 1.2 O Motor de Contexto e Políticas de Compactação

A governança da janela de contexto no Hermes Agent assenta na classe abstrata `ContextEngine` (`agent/context_engine.py`). O agente implementa por padrão o `ContextCompressor`, que atua sob uma esteira de dois níveis:

* **Gateway Session Hygiene:** Localizado em `gateway/run_turn.py`, atua como uma rede de segurança executada antes de o agente iniciar o turno. Dispara automaticamente caso a extensão do histórico atinja 85% do limite nominal de contexto do modelo, prevenindo estouros causados por acúmulo assíncrono de eventos.
* **Agent ContextCompressor:** Executado internamente em `agent/context_compressor.py` durante o ciclo de iteração de ferramentas. O gatilho padrão dispara ao atingir 50% da janela útil do modelo.

O modo de compactação padrão, denominado *lean tail compaction*, preserva uma cauda mínima delimitada a 2,5% do contexto total (com piso de 10.000 tokens e teto de 25.000 tokens), protegendo as últimas 20 mensagens (`protect_last_n: 20`) e pelo menos uma mensagem de utilizador autêntica (`min_tail_user_messages: 1`). O histórico mais antigo é processado por uma chamada LLM auxiliar que extrai deterministicamente identificadores mecânicos (hashes Git, caminhos de arquivo, parâmetros estruturados) e gera ponteiros de recuperação integrados à ferramenta `session_search`.

A classe `ContextEngine` expõe o gancho `select_context()`, que permite reescrever ou podar o array `messages` antes de cada chamada ao provedor de linguagem. No entanto, os contratos do framework ressaltam que mutações dinâmicas e imprevisíveis na ordenação das mensagens invalidam o alinhamento de blocos de prefix cache (Anthropic cache breakpoints), acarretando penalidades financeiras e de latência.

### 1.3 A Interface do Model Context Protocol (MCP)

O Hermes Agent incorpora um cliente completo para o Model Context Protocol, com suporte a transporte bidirecional via `stdio` e HTTP/SSE. O cliente gerencia o ciclo de vida dos processos acoplados por meio de temporizadores configuráveis: `idle_timeout_seconds`, que encerra processos ociosos para posterior reinicialização transparente, e `max_lifetime_seconds`, que impõe reciclagem periódica de segurança.

As ferramentas expostas por servidores MCP são mapeadas no catálogo interno com prefixos estruturados, seguindo o padrão `mcp_<servidor>_<ferramenta>`. O sistema suporta recarregamento a quente via comando `/reload-mcp`, que reinicia os transportes ativos, reprocessa os manifestos declarados em `config.yaml` e reconfigura as tabelas de símbolos sem demandar o reinício do processo principal do agente.

### 1.4 Delegação de Subagentes e Slots Auxiliares de Inferência

Além do modelo principal de conversação (`model.default`), o Hermes Agent implementa dois mecanismos nativos essenciais para a distribuição de computação heterogênea:

* **Subagent Delegation (`delegate_task`):** Permite disparar instâncias filhas de `AIAgent` que operam em processos ou threads paralelas com contextos e diretórios totalmente isolados. A chamada suporta configurações explícitas de modelo e endpoint (`delegation.model`, `delegation.provider`, `delegation.base_url`), e suporte opcional a isolamento de branches via git worktree (`.worktrees/subagent-*`). Apenas o sumário consolidado da tarefa retorna ao contexto do agente pai, eliminando a poluição do histórico conversacional com chamadas intermediárias de ferramentas.
* **Auxiliary Model Slots:** O Hermes isola 11 tarefas de suporte operacional em modelos dedicados sob o bloco `auxiliary.*` da configuração. Tarefas como geração de títulos (`title_generation`), compressão de histórico (`compression`), classificação de aprovação de segurança (`approval`), extração de páginas web (`web_extract`), triagem kanban (`triage_specifier`) e revisão de habilidades (`curator`) podem ser roteadas para endpoints e modelos especializados de alta vazão e baixo custo, desonerando o modelo denso principal.

---

## 2. Transporte Heterogêneo e Concorrência IPC no Windows 11 Nativo

A comunicação entre o interpretador Python 3.11 do Hermes Agent e o binário bare-metal Souls Engine em Rust Tokio sob o kernel NT do Windows 11 exige a avaliação rigorosa das características do subsistema de I/O da plataforma Microsoft. Ao contrário de ambientes POSIX baseados em sockets de domínio Unix e semântica permissiva de arquivos, o Windows impõe primitivos estruturais distintos.

### 2.1 Análise Comparativa de Transportes IPC no Windows 11

A escolha do vetor de transporte entre processos heterogêneos sob o kernel NT define a estabilidade operacional da solução. O transporte via Standard I/O (`stdio`), embora amplamente difundido em servidores MCP, apresenta vulnerabilidades no Windows 11. O runtime C da Microsoft (MSVCRT) e a implementação de subprocessos assíncronos do Python impõem buffering automático de 4KB a 8KB em pipes anônimos quando os descritores não estão associados a uma janela interativa de console. Caso o binário Rust não execute esvaziamento imediato (`flush`) após cada mensagem serializada, ou se ocorrer corrupção por conversão implícita de quebras de linha (`\r\n` vs `\n`), o canal entra em impasses de leitura irreversíveis.

Por outro lado, os Named Pipes do Windows (`\\.\pipe\*`) operam com latências na faixa de dezenas de microssegundos ao tirar proveito direto dos mecanismos de I/O Completion Ports (IOCP). No entanto, a integração entre o loop de eventos `ProactorEventLoop` do Python e as chamadas assíncronas do Tokio em Rust adiciona complexidade na gestão de permissões de segurança e reconexões atômicas.

O transporte via Localhost TCP Loopback com Streamable HTTP/SSE estabelece o melhor compromisso operacional. Ao utilizar conexões persistentes baseadas em sockets Winsock2 e pilha HTTP/1.1 com Keep-Alive, o sistema elimina as fragilidades de buffering de pipes e mantém plena compatibilidade com a especificação de rede do cliente MCP nativo do Hermes Agent.

| Dimensão de Análise | STDIO (Pipes Anônimos Win32) | Localhost TCP (Streamable HTTP/SSE) | Win32 Named Pipes |
| --- | --- | --- | --- |
| Throughput Médio | 120 a 250 MB/s | 600 a 900 MB/s | 1.200 a 2.500 MB/s |
| Latência por Turno | 200 a 800 microssegundos | 50 a 150 microssegundos | 10 a 40 microssegundos |
| Modelo Assíncrono NT | Emulação sobre Pipes | Winsock2 sobre IOCP | Nativo (tokio named_pipe) |
| Risco de Impasse (Deadlock) | Elevado (buffering de bloco MSVCRT) | Mínimo (gestão TCP padrão) | Baixo (dependente de sincronização) |
| Complexidade no Python | Baixa em POSIX / Instável no Windows | Baixa (bibliotecas HTTP/SSE padrão) | Elevada (requer rotinas Win32 especializadas) |
| Compatibilidade Nativa MCP | Nativa (padrão em ferramentas CLI) | Nativa (suportada via HTTP transport)

 | Inexistente (requer adaptadores externos) |

A recomendação técnica para o ambiente Windows 11 nativo é a padronização no **Localhost TCP Loopback com transporte HTTP/SSE**, vinculando o binário Rust na porta de loopback (`127.0.0.1:9123`). Esse arranjo isola o ciclo de vida dos processos, viabiliza inspeções de rede com utilitários convencionais e anula riscos de bloqueio em descritores de console.

### 2.2 Gestão de Concorrência sobre SQLite (WAL) e LanceDB sob Semântica Win32

A execução concorrente em sistemas Windows impõe restrições severas ao nível do sistema de arquivos devido ao bloqueio obrigatório (*mandatory file locking*). Diferente do ambiente Linux, onde múltiplos processos podem acessar arquivos abertos simultaneamente por meio de bloqueios consultivos (*advisory locking*), no Windows qualquer arquivo mantido em memória virtual através de chamadas a `CreateFileMappingW` ou `MapViewOfFile` impede operações de truncamento, exclusão ou reescrita por outros descritores. Tentativas dessa natureza disparam instantaneamente os erros de sistema `ERROR_USER_MAPPED_FILE` (código de erro Win32 1224) ou `ERROR_SHARING_VIOLATION` (código de erro Win32 32).

Para assegurar a operação concorrente do SQLite pelo Souls Engine em modo WAL (Write-Ahead Logging), as rotinas de abertura do driver em Rust devem explicitar as diretivas de compartilhamento `FILE_SHARE_READ | FILE_SHARE_WRITE | FILE_SHARE_DELETE`. Na inicialização do pool de conexões com o banco, é obrigatória a aplicação das seguintes diretivas PRAGMA:

* `PRAGMA journal_mode = WAL;`: Desacopla leituras e gravações, permitindo que múltiplos leitores operem de forma simultânea a uma gravação ativa.
* `PRAGMA synchronous = NORMAL;`: Reduz drasticamente as invocações à função `FlushFileBuffers` do Win32, mantendo a integridade transacional contra falhas de processo sem incorrer nas pesadas penalizações de I/O em disco do Windows.
* `PRAGMA busy_timeout = 5000;`: Estabelece uma janela de espera de cinco segundos com recuo progressivo para resolução de contenções temporárias de escrita.

No caso do LanceDB, o motor utiliza estruturas colunares orientadas ao formato Apache Arrow, dependendo extensivamente de arquivos mapeados em memória (`mmap`). Em virtude do bloqueio obrigatório do Windows, o diretório de dados do LanceDB deve ter seu acesso delegado com **exclusividade ao binário Rust do Souls Engine**. Nenhuma rotina em Python no processo do Hermes Agent deve instanciar leitores diretos nos arquivos do banco vetorial. Toda a recuperação semântica e gravação de novos vetores deve ser solicitada remotamente ao Souls Engine via requisições estruturadas JSON-RPC.

---

## 3. Matriz de Responsabilidades e Separação de Estados

Para eliminar sobreposições operacionais e contenções entre os dois sistemas, a governança de dados e execução é delimitada segundo critérios estritos de responsabilidade.

### 3.1 Fronteiras Funcionais entre Hermes Agent e Souls Engine

O Hermes Agent mantém a soberania executiva e o controle estratégico da sessão interativa. Os arquivos Markdown `MEMORY.md` e `USER.md` pertencem com exclusividade ao processo Python do Hermes, que lê, injeta e atualiza as diretrizes primárias de comportamento. O Souls Engine não grava nesses arquivos, garantindo que o ciclo de *frozen snapshot* e o alinhamento de cache dos modelos permaneçam intocados. A responsabilidade pela persistência profunda, busca relacional, indexação FTS5 de histórico bruto e recuperação de vetores densos é integralmente transferida ao Souls Engine.

A camada de execução de sistema e comandos de terminal reside no ambiente MinGit/Git Bash fornecido nativamente pelo instalador do Hermes (`%LOCALAPPDATA%\hermes\git`). O Souls Engine não executa processos de terminal genéricos, prevenindo conflitos de variáveis de ambiente e privilégios. Em contrapartida, as análises estruturais de código, inspeções sintáticas e manipulações pesadas de texto são atribuídas ao Souls Engine via ferramentas MCP, evitando que o Hermes Agent precise carregar arquivos de texto massivos em memória pura através do Python.

A gestão do pipeline L7 de chamadas diretas ao modelo principal permanece sob o controle do Hermes Agent. O agente já implementa lógicas de chaveamento de credenciais em pool, reconfiguração dinâmica em tempo de execução via `/model` e fallbacks automáticos em caso de indisponibilidade de cotas ou endpoints (`fallback_providers`). Colocar um proxy L7 intermediário cego entre o Hermes e os provedores externos de inferência geraria penalidades desnecessárias de latência, riscos de corrupção em fluxos SSE e quebras no emparelhamento de cabeçalhos de prompt caching. O Souls Engine atua como despachante de modelos locais e hospedeiro de inferência bare-metal sob rigorosa vigilância de hardware.

| Domínio de Sistema             | Hermes Agent (Python 3.11)                                                           | Souls Engine (Rust / Tokio)                                                            | Delimitação Arquitetural                                                    |
| ------------------------------ | ------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------------- | --------------------------------------------------------------------------- |
| Memória Operacional            | Gestão exclusiva de MEMORY.md e USER.md (~1.300 tokens de teto total).               | Leitura passiva opcional em cold boot; sem permissão de escrita direta.                | Exclusivo Hermes. Preserva a integridade do prefix cache de prompt.         |
| Memória Profunda e Vetorial    | Emissão de requisições de consulta contextual e disparo de ganchos de ciclo de vida. | Gestão soberana do SQLite WAL (FTS5) e LanceDB; indexação e deduplicação semântica.    | Delegado ao Souls Engine. Repositório relacional e vetorial de longo prazo. |
| Terminal e Sistema Operacional | Execução de comandos no shell nativo/MinGit com suporte a aprovações.                | Nenhuma execução de processos de sistema operacional.                                  | Exclusivo Hermes. Elimina concorrência de controle de terminal.             |
| Análise Sintática e Código     | Consumo de fragmentos estruturados compactados via ferramentas de agente.            | Parsing nativo via Tree-sitter, extração de esqueletos e Myers Diff.                   | Delegado ao Souls Engine. Executado via ferramentas MCP de alto rendimento. |
| Resolução de Modelos e L7      | Roteamento dinâmico de provedores, streaming SSE e rotação de chaves.                | Telemetria passiva e endpoints locais para Tiers 0, 0.5 e 1.                           | Exclusivo Hermes. Previne overhead e quebra de streaming.                   |
| Inferência Local e Auxiliar    | Orquestração de subagentes delegados e tarefas de apoio (auxiliary.*).               | Execução exclusiva de embeddings ONNX, logit probing e SLM local com watchdog térmico. | Delegado ao Souls Engine. Governança estrita de CPU AVX2 e dGPU RTX 2060m.  |

### 3.2 Especificação das Ferramentas MCP Especializadas do Souls Engine

O servidor MCP exportado pelo Souls Engine substitui a leitura de arquivos brutos por ferramentas de abstração sintática e consulta:

* `souls_ast_outline`: Processa linguagens-alvo (Rust, Python, TypeScript, Go) e devolve apenas a assinatura pública, tipos estruturais, traits/interfaces e docstrings associadas, suprimindo integralmente os blocos internos de implementação.
* `souls_ast_slice`: Executa consultas pontuais em árvores sintáticas Tree-sitter baseadas em escopo funcional, retornando exclusivamente nós semânticos específicos sem sobrecarregar a memória do agente.
* `souls_myers_diff`: Computa diferenças mínimas entre arquivos de código e gera saídas validadas estruturalmente, prevenindo falhas em reescritas parciais.
* `souls_memory_recall`: Realiza consultas híbridas combinando busca de texto completo (BM25 via SQLite FTS5) e busca vetorial de cosseno (LanceDB), entregando blocos de relevância ranqueados para enriquecimento de contexto.
* `souls_route_subtask`: Consulta a fronteira do ParetoBandit para receber a recomendação ótima de Tier de modelo (0.5 a 4) e parâmetros de execução para uma subtarefa arbitrária.

---

## 4. Padrões de Integração Arquitetural

A análise de viabilidade técnica para o acoplamento do Souls Engine ao Hermes Agent ponderou três modelos operacionais.

### 4.1 Abordagem 1: Sidecar Passivo Puro (MCP Server HTTP + Endpoint Customizado)

No arranjo de sidecar passivo, o Souls Engine é executado como um binário daemon independente (`souls_server.exe`). O Hermes Agent é configurado para interagir com o motor através da declaração padrão de um servidor MCP via HTTP em `config.yaml` (`mcp_servers.souls.url`) e apontando modelos locais através de configurações de provedor customizado (`providers.custom.base_url`).

As vantagens desse modelo residem no isolamento total entre os processos, imunidade a crashes cruzados e ausência de dependências de compilação entre a toolchain do Python e a do Rust. Por outro lado, o modelo impede a integração do motor de infraestrutura aos ganchos de ciclo de vida profundos do agente (`sync_turn`, `on_pre_compress`), dependendo de intervenções manuais ou invocações explícitas de ferramentas pelo modelo de linguagem.

### 4.2 Abordagem 2: Plugin Nativo In-Process via PyO3 / C-ABI

Na abordagem em processo, a base de código do Souls Engine é compilada como uma dynamic link library (`souls_ffi.pyd`) acoplada diretamente ao interpretador Python por meio do ecossistema PyO3. A biblioteca implementa nativamente as classes abstratas `MemoryProvider` e `ContextEngine` do framework Hermes.

Embora essa estratégia elimine o overhead de I/O em loopback e viabilize latências na escala de microssegundos, os custos de estabilidade no Windows 11 são proibitivos. Qualquer falha crítica de memória ou violação de acesso em código Rust derruba instantaneamente o processo Python do Hermes Agent sem possibilidade de recuperação. Adicionalmente, as diferenças entre a toolchain de compilação do instalador do Hermes (que distribui runtimes gerenciados via uv) e o compilador MSVC do Rust impõem elevada complexidade de compilação e risco contínuo de bloqueio mútuo (deadlock) entre as threads do Tokio e o Global Interpreter Lock (GIL) do Python.

### 4.3 Abordagem 3: Modelo Híbrido Desacoplado (Sidecar Bare-Metal + Thin Adapter Python)

O modelo híbrido desacoplado constitui o desenho arquitetural ótimo para a solução. O binário em Rust do Souls Engine atua de forma autônoma como um daemon local, expondo uma interface HTTP/SSE MCP para o catálogo de ferramentas e rotas REST para operações administrativas e telemetria. Simultaneamente, implanta-se na árvore de plugins do Hermes Agent um adaptador leve escrito em Python puro (`$HERMES_HOME/plugins/memory/souls/`), derivado da classe `MemoryProvider`.

Nessa configuração, o adaptador Python atua como despachante de eventos. Quando o Hermes aciona o gancho `sync_turn`, o adaptador delega o payload da interação a uma thread daemon em segundo plano, que executa uma chamada assíncrona contra o daemon Rust (`POST [http://127.0.0.1:9123/api/memory/turn](http://127.0.0.1:9123/api/memory/turn)`) sem introduzir latência no fluxo conversacional. No gancho de compactação `on_pre_compress`, o adaptador despacha o snapshot bruto dos dados com semântica de versão 2, aguardando a persistência síncrona no SQLite WAL para validar a operação. As ferramentas de processamento pesado de código (Tree-sitter, Myers Diff, ParetoBandit) são simultaneamente registradas no cliente MCP nativo do Hermes via transporte HTTP.

| Métrica / Dimensão Técnica | Abordagem 1: Sidecar Passivo Puro | Abordagem 2: Plugin In-Process (PyO3) | Abordagem 3: Modelo Híbrido Desacoplado |
| --- | --- | --- | --- |
| Latência por Invocação de Tool | 1 a 3 ms (Loopback TCP) | < 0,1 ms (In-process) | 1 a 3 ms (Loopback TCP) |
| Latência no Ciclo de Memória | Não implementa ganchos de ciclo | < 0,2 ms (In-process) | < 1 ms (Thread daemon assíncrona) |
| Isolamento de Falhas (Crash Safety) | Completo (Processos isolados) | Nulo (Crash em Rust encerra o Python) | Completo (Processos isolados) |
| Complexidade de Compilação Win32 | Nula (Binário Rust independente) | Crítica (Linkage MSVC com Python uv) | Nula (Adapter Python puro + Binário Rust) |
| Impacto no Streaming SSE de Tokens | Nulo (Não atua como proxy LLM) | Nulo | Nulo (Não atua como proxy LLM) |
| Aderência aos Contratos do Hermes | Média (Apenas catálogo de ferramentas) | Alta (Implementa ABC nativa) | Perfeita (Implementa ABC e cliente MCP) |

---

## 5. Taxonomia de Tiers de Modelos e Governança Físico-Computacional

A arquitetura do Souls Engine estabelece uma hierarquia de inteligência em sete Tiers funcionais, projetada para balancear custo financeiro, velocidade de execução e integridade de contexto, respeitando estritamente os limites do hardware local (Intel Core i9, 32GB RAM, GPU NVIDIA GeForce RTX 2060m com 6GB VRAM GDDR6 no Windows 11).

### 5.1 Especificação e Alocação dos Tiers de Inteligência

A segregação divide as cargas entre motores locais bare-metal e endpoints de nuvem orquestrados:

* **Tier 0 (Classificação e Filtro Síncrono):** Modelo `GLiClass Multilang Ultra` ou `ModernBERT` rodando em ONNX Runtime vinculado exclusivamente à CPU (aceleração AVX2/OpenVINO). Executa triagem síncrona de intenção, detecção de injeção de prompt e decisões binárias de acionamento de RAG em menos de 15 ms, com consumo nulo de VRAM.
* **Tier 0.5 (Epistêmico e Logit Probing):** Modelo `Gemma-2-2B` em formato GGUF executado em CPU via runtime interno do llama.cpp em Rust. Opera em regime restrito de inferência sem geração livre de texto: extrai probabilidades brutas (logits) de tokens de controle para calcular a entropia informacional e o grau de ambiguidade de requisições ou fragmentos de memória em cerca de 100 ms.
* **Tier 1 / 1.5 (Maestro Local / Live Chat / Coder):** Modelo `Qwen2.5-Coder-3B-Instruct` ou `Qwen2.5-Coder-7B` (quantizado em Q3_K_M) operando diretamente na GPU RTX 2060m. Utiliza motor llama.cpp nativo com aceleração CUDA, decodificação guiada via `llguidance` e KV Cache assimétrico (tensores de chave K em FP16 e valores V em Q4_0 com FlashAttention-2). Garante geração superior a 45 tokens por segundo para diálogo em tempo real e autocomplete.
* **Tier 2 (Local Heavy - Assíncrono):** Modelos Mixture-of-Experts compactos (ex: arquiteturas MoE 33B com 3B a 4B ativos por token) rodando em background com particionamento híbrido: tensores de atenção crítica alocados na dGPU (1.500 MB) e matrizes de especialistas roteadas para a memória RAM física (16 GB). Destinado a refatorações em lote e varreduras profundas de repositórios disparadas por subagentes assíncronos.
* **Tier 1.5 Nuvem (CLI Agêntica Especializada):** Integrações nativas via terminal com ferramentas de desenvolvedor em linha de comando (Claude Code, OpenAI Codex CLI). O Hermes Agent orquestra esses binários em terminais virtuais isolados (`pty=true`), consumindo cotas corporativas de taxa fixa sem incorrer em custos de tokens por requisição.
* **Tier 3 (Nuvem Fast):** Modelos de altíssima vazão e baixo custo por token (DeepSeek V3/V4 Flash, Gemini 2.5 Flash) consumidos via API HTTP externa. Ideal para sumarização de documentações extensas e etapas intermediárias de pipelines de dados.
* **Tier 4 (Nuvem Heavy - O Cérebro Denso):** Modelos de fronteira analítica (Claude 3.5 Sonnet, DeepSeek V4 Pro, GPT-5). Acionado estritamente por exceção para planejamento de alto nível, decomposição de grafos acíclicos dirigidos (DAGs) de tarefas e auditorias críticas de arquitetura de software.
* **Tier 5 (Nuvem Free Tiers):** Endpoints gratuitos de contingência (OpenRouter Free) configurados na esteira de `fallback_providers` do Hermes para manter o agente operante em tarefas de baixo risco quando cotas comerciais são excedidas.

| Tier | Modelo / Carga | Runtime / Dispositivo | Consumo VRAM | Consumo RAM | Latência Típica |
| --- | --- | --- | --- | --- | --- |
| Tier 0 | GLiClass / ModernBERT | ONNX (CPU AVX2) | 0 MB | ~ 350 MB | < 15 ms |
| Tier 0.5 | Gemma-2-2B (GGUF) | llama.cpp (CPU) | 0 MB | ~ 1.800 MB | ~ 100 ms |
| Tier 1 | Qwen2.5-Coder-3B (Q4_K_M) | llama.cpp (CUDA dGPU) | ~ 2.800 MB | ~ 500 MB | 20 a 25 ms (TTFT) |
| Tier 2 | Compact MoE (33B/3B) | Híbrido (dGPU + RAM) | ~ 1.500 MB | ~ 16.000 MB | 150 a 300 ms |
| Tier 1.5 CLI | Claude Code / Codex CLI | Processo PTY Nativo | 0 MB | ~ 400 MB | Variável (CLI externa) |
| Tier 3 | DeepSeek Flash / Gemini Flash | API Externa (HTTPS) | 0 MB | Negligível | 200 a 400 ms |
| Tier 4 | Claude 3.5 Sonnet / V4 Pro | API Externa (HTTPS) | 0 MB | Negligível | 800 a 1.500 ms |
| Tier 5 | Modelos Free OpenRouter | API Externa (HTTPS) | 0 MB | Negligível | Variável (Fila livre) |

### 5.2 Particionamento Físico e Governança da RTX 2060m (6GB)

O modelo de driver Windows Display Driver Model (WDDM 2.x/3.x) no Windows 11 aloca compulsoriamente entre 500 MB e 850 MB de memória de vídeo para o Desktop Window Manager (`dwm.exe`) e aceleração do sistema operacional. O espaço útil total da RTX 2060m é delimitado pela expressão:

$$\text{VRAM}_{\text{disponível}} = \text{VRAM}_{\text{física}} - \text{VRAM}_{\text{WDDM}}$$

Considerando os 6.144 MB de VRAM física e a reserva típica de 850 MB pelo WDDM, a margem útil operacional é de aproximadamente 5.294 MB.

Quando a alocação agregada da GPU ultrapassa essa fronteira, o subsistema de memória do Windows ativa a transferência de tensores para a memória compartilhada de sistema (*Shared GPU Memory Fallback*) através do barramento PCIe 3.0. A taxa de geração colapsa de mais de 45 tokens por segundo para menos de 1,5 tokens por segundo, paralisando a comunicação do agente.

Para garantir que a RTX 2060m opere sem degradação, o Souls Engine implementa uma política de **Exclusão Mútua de VRAM**:

* O **Tier 1** e o **Tier 2** não podem estar ativos simultaneamente na memória de vídeo dedicada.
* Em regime nominal (Tier 1 ativo), os pesos do modelo (2.100 MB) e o KV Cache assimétrico de 8.192 tokens (900 MB) ocupam aproximadamente 3.000 MB. Somados aos 850 MB do WDDM, a ocupação totaliza 3.850 MB, garantindo uma margem de segurança de 1.444 MB livres na dGPU.
* Quando o despachante aloca uma carga de trabalho assíncrona pesada para o Tier 2, o Souls Engine executa um ciclo transacional de swapping: descarrega (*unload*) os buffers do Tier 1 da GPU para a memória virtual ou instrui o backend do llama.cpp a executar a tarefa do Tier 2 inteiramente na CPU Intel Core i9 utilizando extensões vetoriais AVX2, onde a vazão média de 10 a 16 tokens por segundo é suficiente para operações em segundo plano sem impactar a estabilidade da GPU.

### 5.3 O Watchdog Termodinâmico NVML

A crate `souls_inference` incorpora um monitor ativo acoplado à NVIDIA Management Library (`nvml-wrapper`), executando medições de telemetria a intervalos de 500 milissegundos:

* **Faixa Nominal ($\text{VRAM}_{\text{livre}} > 1.400\text{ MB}$ e $T_{\text{GPU}} < 75^\circ\text{C}$):** Autorização plena para inferência do Tier 1 na dGPU com aceleração CUDA.
* **Faixa Preventiva ($800\text{ MB} < \text{VRAM}_{\text{livre}} \le 1.400\text{ MB}$ ou $75^\circ\text{C} \le T_{\text{GPU}} < 82^\circ\text{C}$):** Restrição de novas alocações de contexto. O modelo reduz o teto de novos tokens e direciona rotinas de vetorização semântica para o provedor de execução CPU do ONNX Runtime.
* **Faixa Crítica ($\text{VRAM}_{\text{livre}} \le 800\text{ MB}$ ou $T_{\text{GPU}} \ge 82^\circ\text{C}$):** Suspensão compulsória da GPU. Os contextos de inferência local são descarregados, emitindo-se o comando de liberação de alocações `cudaDeviceReset`. As requisições pendentes são desviadas de forma graciosa para provedores de nuvem (Tier 3) ou processamento em CPU.

---

## 6. O Mecanismo ParetoBandit e a Desidratação Sintática de Contexto (LEAN)

A seleção inteligente de modelos entre Tiers locais e provedores de nuvem exige uma formulação rigorosa que maximize a qualidade da resposta sem comprometer o alinhamento de cache ou o orçamento operacional.

### 6.1 Roteamento por Subagentes versus Comutação Intradiálogo

Um dos erros conceituais mais comuns em arquiteturas agênticas híbridas consiste na tentativa de alternar o modelo de linguagem principal a cada turno da conversa interativa (*turn-level model switching*).

Nos provedores comerciais modernos de fronteira, os blocos de prompt caching são construídos e preservados na memória do servidor com base na identidade estrita do modelo e na chave de autorização utilizada. Quando um agente comuta o modelo executivo principal (por exemplo, do Claude 3.5 Sonnet para o DeepSeek Flash) no meio de uma conversa, o novo modelo não possui o prefixo armazenado em cache. O provedor é forçado a processar todo o histórico acumulado pelo valor de entrada integral (*uncached input tokens*), gerando um pico financeiro e de latência que anula a economia pretendida. Além disso, a troca de volta para o modelo anterior invalida novamente o cache na direção oposta.

Por essa razão, o algoritmo **ParetoBandit** do Souls Engine não atua no loop intradiálogo principal. O diálogo mestre permanece fixado em um modelo de governança contínua (seja o Tier 1 local em sessões offline ou o Tier 4 em sessões avançadas de desenvolvimento). A atuação do ParetoBandit ocorre exclusivamente em dois pontos de desacoplamento nativos do Hermes Agent:

1. **Despacho de Tarefas Delegadas (`delegate_task`):** Conforme a documentação do Hermes, subagentes são instâncias limpas de `AIAgent` geradas para cumprir metas autocontidas, recebendo apenas o prompt da meta e o contexto estritamente relevante. Como os subagentes iniciam com histórico zero, eles não dependem do prefix cache da conversa principal. O ParetoBandit determina dinamicamente o modelo e endpoint ótimos a serem injetados nos parâmetros `delegation.model` e `delegation.base_url` para cada subtarefa.
2. **Resolução de Slots Auxiliares (`auxiliary.*`):** O Hermes Agent gerencia independentemente os provedores de suas 11 tarefas de suporte. O ParetoBandit atua orientando as configurações de tarefas pontuais (como resumos de compressão, extração web e triagem) para os Tiers 0, 0.5 ou 3 conforme a complexidade detectada.

### 6.2 Formalização Matemática do ParetoBandit Multiobjetivo

O ParetoBandit é modelado como um problema de *Multi-Armed Bandit Contextual* multiobjetivo com amostragem bayesiana (Thompson Sampling) estendido por restrições termodinâmicas.

Para cada subtarefa $i$ caracterizada por um vetor de contexto $x_i \in \mathbb{R}^d$ (composto por: extensão estimada da entrada em tokens, densidade de referências a arquivos de código, complexidade ciclomática média e exigência de raciocínio de múltiplos passos), o despachante avalia os braços disponíveis correspondentes aos Tiers funcionais $k \in \{0.5, 1, 2, 1.5\text{-CLI}, 3, 4, 5\}$.

A função de utilidade escalarizada $U(k \mid x_i)$ a ser maximizada é definida pela seguinte equação:

$$U(k \mid x_i) = w_q \cdot \hat{Q}_k(x_i) - w_c \cdot C_k(x_i) - w_l \cdot \hat{L}_k(x_i) - \Phi(T_{\text{GPU}}, V_{\text{livre}}, k)$$

Onde:

* $\hat{Q}_k(x_i) \in [0, 1]$: Estimativa amostrada da qualidade de execução do Tier $k$ para o contexto $x_i$, derivada de distribuições Beta a posteriori $\text{Beta}(\alpha_{k,c}, \beta_{k,c})$ condicionadas à categoria de tarefa $c$. O histórico de sucessos e falhas é atualizado com base em feedback estrutural (testes unitários aprovados, conformidade sintática de diffs gerados).
* $C_k(x_i) \ge 0$: Custo financeiro direto projetado para a execução da tarefa no Tier $k$ (onde $C_k = 0$ para todos os Tiers locais 0, 0.5, 1 e 2, bem como para o Tier 1.5 CLI de plano ilimitado).
* $\hat{L}_k(x_i)$: Latência esperada de conclusão em segundos, modelada por regressão linear baseada na contagem de tokens de entrada e saída projetados.
* $w_q, w_c, w_l$: Pesos de preferência do operador para calibração da fronteira de Pareto (por padrão, calibrados para $w_q = 0,45$, $w_c = 0,35$, $w_l = 0,20$).
* $\Phi(T_{\text{GPU}}, V_{\text{livre}}, k)$: Função de barreira termodinâmica imposta pelo watchdog NVML. Para Tiers que não utilizam a dGPU (Tiers 0, 0.5 na CPU, Tiers de nuvem 3 e 4), $\Phi = 0$. Para Tiers que requisitam a dGPU (Tier 1 e Tier 2 em modo híbrido):

$$\Phi(T_{\text{GPU}}, V_{\text{livre}}, k) = \begin{cases} 0 & \text{se } V_{\text{livre}} > 1400 \text{ e } T_{\text{GPU}} < 75 \\ \lambda_1 \cdot \frac{1400 - V_{\text{livre}}}{600} & \text{se } 800 < V_{\text{livre}} \le 1400 \\ +\infty & \text{se } V_{\text{livre}} \le 800 \text{ ou } T_{\text{GPU}} \ge 82 \end{cases}$$

Essa formulação garante que, sob estresse térmico ou exaustão de VRAM, os braços que demandam a GPU sofram penalização infinita, forçando o algoritmo a eleger soluções viáveis em CPU ou na nuvem de forma matemática e automática.

### 6.3 Ponto de Aplicação da Desidratação Sintática (LEAN)

A compactação sintática de código fonte (LEAN) via análise de árvores sintáticas (AST) pelo Souls Engine não deve ser realizada através de proxies interceptadores de tráfego HTTP. Interceptar chamadas no nível de rede para podar strings em trânsito acarreta o risco de quebrar marcações de controle de chamadas de ferramentas nativas do Hermes (`<tool_call>`, esquemas JSON de parâmetros) e corromper blocos de código com strings multilinhas.

O ponto de aplicação ótimo e deterministicamente seguro é a **Desidratação na Origem (Source-Side Dehydration)**, operando dentro do catálogo de ferramentas MCP:

* Em vez de o agente invocar a ferramenta genérica de leitura de arquivos (`read_file`), ele executa `mcp_souls_ast_outline`.
* A crate `souls_ast` processa o arquivo-alvo utilizando o parser nativo Tree-sitter em Rust, extrai a árvore sintática concreta e descarta integralmente os corpos de implementação das funções e métodos, preservando declarações de escopo, contratos de interface, tipos estruturais e anotações de documentação.
* O payload retornado ao agente chega com **redução de 70% a 85% no consumo de tokens**, com custo de parsing inferior a 2 ms e garantia absoluta de integridade sintática na janela de contexto.

---

## 7. Governança, Telemetria e Integração com o Hermes Web Dashboard

O Hermes Agent inclui um painel web nativo para administração e governança (`hermes dashboard`), suportando arquitetura de extensão com descoberta automática baseada em manifestos e comunicação de dados estruturados.

### 7.1 Arquitetura de Extensão do Painel Web

A consolidação da visibilidade operacional do binário Souls Engine é implementada através do subsistema de plugins de painel do Hermes Agent, evitando a execução de múltiplos servidores web isolados. A estrutura do plugin reside em `$HERMES_HOME/plugins/souls_dashboard/`:

* `plugin.yaml`: Arquivo de manifesto consumido pelo gerenciador de plugins do Hermes, identificando as capacidades declaradas.
* `__init__.py`: Módulo de registro para ganchos de interface e comandos de linha de terminal.
* `dashboard/manifest.json`: Manifesto de interface gráfica web declarando uma nova aba interativa (`/souls`), com identificador, rótulo e ícone de visualização.
* `dashboard/plugin_api.py`: Módulo Python expondo um roteador FastAPI. O servidor web do Hermes detecta o arquivo e monta as rotas automaticamente sob o caminho `/api/plugins/souls_dashboard/`. Quando acionado pela interface, o roteador atua como proxy reverso local, consultando o endpoint de telemetria do binário Rust (`[http://127.0.0.1:9123/api/v1/telemetry](http://127.0.0.1:9123/api/v1/telemetry)`) e retornando dados normalizados.
* `dashboard/dist/index.js`: Pacote estático em JavaScript compilado no formato IIFE, sem empacotar dependências externas redundantes. O pacote registra o componente visual através de `window.__HERMES_PLUGINS__.register('souls-dashboard', Component)` e utiliza o conjunto oficial de componentes React e primitivos de layout disponibilizados no escopo global em `window.__HERMES_PLUGIN_SDK__`.

### 7.2 Métricas Operacionais Expostas na Interface

A aba administrativa do Souls Engine expõe as seguintes métricas de telemetria em tempo real:

* **Telemetria Termodinâmica da GPU:** Gráficos em tempo real de ocupação de VRAM segregada (consumo WDDM do sistema operacional vs consumo do Tier 1/llama.cpp), curva de temperatura em graus Celsius e status do Watchdog térmico.
* **Distribuição Operacional do ParetoBandit:** Histórico de seleção de Tiers por categoria de tarefa, taxas empíricas de sucesso por braço e estimativas de economia financeira acumulada contra execuções integrais no Tier 4.
* **Métricas do Servidor MCP:** Volume total de requisições JSON-RPC processadas, distribuição percentilar de latência ($p50$, $p95$, $p99$) em operações de parsing sintático Tree-sitter e taxas de acerto de cache de esqueletos de código.
* **Estado dos Bancos de Dados:** Tamanho em disco do banco relacional, contagem de páginas ativas no arquivo `.wal`, volume total de vetores indexados no LanceDB e índice de fragmentação de índices colunares.

---

## 8. Resiliência de Sistemas, Heartbeat e Degradação Graciosa

A estabilidade em sistemas híbridos que integram runtimes interpretados e código nativo de baixo nível assenta na contenção rigorosa de exceções e em contratos determinísticos de tolerância a falhas.

### 8.1 Contenção de Falhas no Binário Rust

O binário do Souls Engine emprega salvaguardas internas para assegurar disponibilidade contínua de serviço:

* **Barreiras de Pânico Assíncronas:** Todas as tarefas concorrentes despachadas dentro do runtime Tokio são encapsuladas por primitivos `std::panic::catch_unwind(AssertUnwindSafe(...))`. Caso uma rotina de parsing sintático Tree-sitter sofra terminação anômala em função de arquivos com anomalias estruturais, o pânico é contido no escopo daquela tarefa específica sem derrubar o daemon.
* **Padronização de Erros JSON-RPC:** Em caso de exceção de parsing ou indisponibilidade de arquivos, o servidor emite uma mensagem de erro JSON-RPC estruturada (faixa de códigos de erro `-32000` a `-32099`), permitindo que o cliente MCP do Hermes processe a falha com semântica de domínio em vez de sofrer quebra de conexão.
* **Supervisão do Daemon:** O binário executa uma thread de monitoramento contínuo responsável por verificar o estado dos pools de conexões e sockets. Na ocorrência de anomalias que comprometam a estabilidade do runtime de I/O, o sistema executa um reinício ordenado de suas estruturas internas, salvaguardando a consistência dos dados do SQLite WAL.

### 8.2 Contratos de Degradação Graciosa no Hermes Agent

O framework do Hermes Agent incorpora mecanismos robustos para mitigar indisponibilidades do backend:

* **Temporização e Reciclagem de Ferramentas:** Conforme declarado na especificação do MCP do Hermes, a conexão com o servidor suporta parametrização de limites temporais via `timeout` e `connect_timeout`. Caso o processamento de uma análise sintática exceda o tempo delimitado, o Hermes encerra a requisição e devolve uma notificação de timeout ao modelo. O agente está instruído por diretrizes de prompt a recuar de modo transparente para a leitura nativa por blocos (`read_file` com deslocamento de linha) sem interromper a execução.
* **Recuperação Automática de Transporte (`/reload-mcp`):** Se o processo em Rust for reiniciado externamente, a conexão SSE de loopback é restabelecida na próxima iteração do agente. A qualquer momento, a invocação do comando de barra `/reload-mcp` reconstrói as tabelas de ferramentas e executa o handshake inicial com o Souls Engine sem exigir o reinício do processo principal do agente.
* **Degradação de Ganchos de Memória:** Caso o binário do Souls Engine esteja temporariamente inacessível durante o gancho `sync_turn`, o adaptador Python do plugin descarta a operação ou enfileira os dados em um arquivo local temporário (`.hermes/souls_turn_spool.jsonl`), impedindo que a indisponibilidade afete o diálogo com o utilizador. Apenas no caso de `on_pre_compress` configurado com checkpoint obrigatório (`compression.checkpoint_required: true`), o sistema interrompe a compactação destrutiva para proteger os dados conversacionais originais.

---

## 9. Topologia Definitiva de Crates Rust e Roadmap de Implementação

A inclusão do algoritmo ParetoBandit e da taxonomia multimodelo direciona a organização definitiva do workspace Souls Engine em Rust.

### 9.1 Racional de Estruturação do Workspace Rust

As diretrizes técnicas para as crates são assim distribuídas:

* **`souls_protocol` (Manter & Refinar):** Centraliza contratos DTOs de dados, serializadores JSON-RPC, esquemas MCP de ferramentas e tipos Arrow para intercâmbio colunar.
* **`souls_core` (Manter):** Configurações gerais, inicialização de tracing assíncrono Tokio, barreiras contra pânico e abstrações de I/O Win32.
* **`souls_memory` (Manter & Consolidar):** Gestão exclusiva de concorrência sobre SQLite em modo WAL (FTS5) e LanceDB vetorial.
* **`souls_ast` (Manter Especializada):** Compilação de gramáticas Tree-sitter nativas, gerador de outlines de código e computação de Myers Diff.
* **`souls_inference` (Refatorar & Especializar):** Hospeda os runtimes de inferência local: ONNX Runtime em CPU para Tier 0, llama.cpp em CPU para Tier 0.5 (logit probing), llama.cpp com aceleração CUDA para Tier 1, e o monitor térmico NVML.
* **`souls_router` (Nova Crate Especializada):** Implementa o algoritmo de aprendizado contextual **ParetoBandit**, persistindo a matriz de recompensas bayesianas no SQLite e exportando métodos de cálculo de utilidade.
* **`souls_server` (Nova Crate Executável):** Binário executável daemon baseado em Axum, unificando o servidor MCP (HTTP/SSE), os endpoints REST de telemetria consumidos pelo dashboard e as rotas de inferência local compatíveis com a especificação OpenAI.

### 9.2 Organização de Diretórios do Workspace Rust

A árvore de diretórios do repositório está estruturada da seguinte forma:

* `souls-engine/` (Raiz do Workspace Rust)
* `Cargo.toml` (Configuração dos membros virtuais do workspace)
* `crates/`
* `souls_protocol/` (Tipos JSON-RPC, contratos de ferramentas MCP e DTOs)
* `souls_core/` (Configuração TOML, logging tracing e salvaguardas de pânico)
* `souls_memory/` (Controladores SQLite WAL/FTS5 e LanceDB)
* `souls_ast/` (Gramáticas Tree-sitter nativas e algoritmo de Myers Diff)
* `souls_inference/` (Drivers ONNX Runtime, llama.cpp com llguidance e NVML)
* `souls_router/` (Motor matemático do ParetoBandit e regressão contextual)
* `souls_server/` (Daemon unificado em Axum: MCP HTTP/SSE e rotas REST)
* `adapters/` (Componentes de Integração com o Hermes)
* `hermes_memory_souls/` (Plugin leve em Python para `$HERMES_HOME/plugins/memory/`)
* `__init__.py` (Implementação formal da classe `MemoryProvider` do Hermes)
* `plugin.yaml` (Manifesto declarativo de configuração de memória)
* `hermes_dashboard_souls/` (Plugin do Web Dashboard para `$HERMES_HOME/plugins/souls_dashboard/`)
* `plugin.yaml` (Manifesto do plugin para o framework Hermes)
* `dashboard/manifest.json` (Declaração de rotas, título e ícone da interface web)
* `dashboard/plugin_api.py` (Roteador FastAPI que consulta os dados REST do Souls Engine)
* `dashboard/dist/index.js` (Componente React compilado em formato IIFE consumindo o SDK global)

### 9.3 Cronograma Determinístico de Implementação do MVP (Windows 11)

A implementação do sistema ocorre em quatro fases de engenharia sequenciais e verificáveis:

#### Fase 1: Fundação Bare-Metal e Comunicação Base (Semanas 1–2)

* Estruturação do workspace Rust no Windows 11 com toolchain `stable-x86_64-pc-windows-msvc`.
* Implementação das crates `souls_core` (tracing e interceptadores de pânico) e `souls_protocol` (contratos JSON-RPC).
* Desenvolvimento do daemon executável `souls_server` baseado em Axum na porta de loopback `127.0.0.1:9123`.
* Homologação da estabilidade da camada TCP de loopback no Windows 11 sob concorrência de I/O contínua.

#### Fase 2: Motor de AST e Integração com o Catálogo MCP (Semanas 3–4)

* Desenvolvimento da crate `souls_ast` integrando gramáticas Tree-sitter compiladas para Rust, Python e TypeScript.
* Implementação dos algoritmos determinísticos de extração de esqueletos (`souls_ast_outline`, `souls_ast_slice`) e diferenças mínimas (`souls_myers_diff`).
* Exposição dos métodos de AST no servidor MCP hospedado em `souls_server` sob protocolo Streamable HTTP.
* Validação do catálogo de ferramentas no Hermes Agent via `config.yaml` e comando `/reload-mcp`.

#### Fase 3: Persistência Híbrida e Adaptador de Memória Python (Semanas 5–6)

* Desenvolvimento da crate `souls_memory`, inicializando SQLite com flags Win32 (`PRAGMA journal_mode = WAL`, `PRAGMA synchronous = NORMAL`).
* Configuração do armazenamento vetorial colunar via LanceDB com governança de arquivos exclusiva mantida pelo processo Rust.
* Implementação do plugin em Python puro `$HERMES_HOME/plugins/memory/souls/`, mapeando as chamadas assíncronas de `sync_turn` e validação de versão 2 em `on_pre_compress`.
* Validação de isolamento de concorrência com o Hermes operando `MEMORY.md` e `USER.md` sem conflitos de disco.

#### Fase 4: Tiers Locais, ParetoBandit e Dashboard Unificado (Semanas 7–8)

* Integração da biblioteca NVML na crate `souls_inference` e ativação do monitor térmico a cada 500 milissegundos.
* Implantação dos Tiers 0 e 0.5 em CPU via ONNX e llama.cpp, e ativação do Tier 1 na dGPU com FlashAttention-2 e KV Cache assimétrico.
* Desenvolvimento da crate `souls_router` com a formulação bayesiana do ParetoBandit e despacho via ferramenta MCP `souls_route_subtask`.
* Construção do plugin `$HERMES_HOME/plugins/souls_dashboard/`, integrando o roteador FastAPI e a interface React com `window.__HERMES_PLUGIN_SDK__`.
* Condução de testes de estresse com injeção de pânicos forçados no binário Rust, confirmando a continuidade operacional ininterrupta do Hermes Agent.

---

## 10. Considerações Finais e Diretrizes de Engenharia

A integração arquitetural entre o Hermes Agent e o Souls Engine estabelece um ecossistema cooperativo e complementar: o Hermes Agent atua como o **orquestrador executivo de raciocínio e interface agêntica**, enquanto o Souls Engine opera como a **infraestrutura bare-metal de dados, aceleração de código e governança física de hardware**.

A ancoragem do algoritmo ParetoBandit na fronteira de delegação de subagentes (`delegate_task`) e tarefas auxiliares soluciona de forma definitiva o problema de quebra de prefix cache em provedores comerciais de nuvem. O isolamento do hardware físico sob o teto estrito de 6GB da GPU RTX 2060m impede a degradação de desempenho para a memória RAM compartilhada, enquanto a desidratação sintática de código via Tree-sitter em Rust minimiza drasticamente a janela de tokens e os custos operacionais. Essa arquitetura assegura soberania local, alta performance e resiliência em ambiente nativo Windows 11.