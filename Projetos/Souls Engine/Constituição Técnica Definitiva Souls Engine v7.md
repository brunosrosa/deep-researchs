# Constituição Técnica Definitiva: Arquitetura de Integração Hermes Agent e Souls Engine v7 (Canônica) no Windows 11

## 1. Topologia de Sistemas e Anatomia de Interfaces do Hermes Agent

A convergência operacional entre o orquestrador agêntico **Hermes Agent** (Nous Research) e o motor de infraestrutura bare-metal **Souls Engine** estabelece uma divisão rigorosa entre a inteligência executiva deliberativa e os serviços de aceleração computacional de baixo nível. O runtime do Hermes Agent está estruturado na classe executiva `AIAgent` (localizada em `run_agent.py`), responsável pela governança do ciclo de inferência, expansão de ferramentas, montagem de prompts de sistema e resolução de provedores.

O Souls Engine não atua como uma casca genérica em torno de chamadas a modelos de linguagem, mas como um subsistema bare-metal acoplado às interfaces especializadas de extensão do framework Hermes, segregadas em quatro superfícies vitais:
1. Subsistema de persistência de memória primária e provedores externos (`MemoryProvider`).
2. Motor de governança e compactação de contexto (`ContextEngine`).
3. Barramento padronizado de ferramentas externas via Model Context Protocol (MCP).
4. Infraestrutura de computação desacoplada via subagentes (`delegate_task`) e slots auxiliares (`auxiliary.*`).

### 1.1 O Ciclo de Vida da Memória Nativa e Provedores Externos

O Hermes Agent articula a retenção de conhecimento através de dois mecanismos concorrentes: arquivos locais delimitados montados no prompt de sistema e provedores externos governados pela classe abstrata `MemoryProvider` (`agent/memory_provider.py`).

A memória primária textual é sustentada por dois arquivos formatados em Markdown, situados no diretório do perfil ativo do agente:
- `MEMORY.md`: Destinado ao armazenamento de notas operacionais do agente, convenções do ambiente de desenvolvimento, arquiteturas de projeto e lições aprendidas, sujeito a um teto estrito de 2.200 caracteres (~800 tokens).
- `USER.md`: Reservado para o perfil do operador humano, incluindo preferências comunicacionais, diretrizes de trabalho e restrições de comportamento, delimitado a 1.375 caracteres (~500 tokens).

Ambos os arquivos utilizam o delimitador canônico `§` para segregar entradas atômicas. O Hermes Agent opera sob o padrão de **frozen snapshot**: no momento da inicialização da sessão (`session_start`), o conteúdo de `MEMORY.md` e `USER.md` é lido e injetado de forma imutável no bloco de sistema da conversa. Embora o modelo execute ações de modificação (`add`, `replace`, `remove`) que persistem as alterações imediatamente no sistema de arquivos, a representação injetada no contexto conversacional permanece inalterada até o encerramento ou reinicialização da sessão. Essa imutabilidade pontual é indispensável para preservar o alinhamento de prefixo nos mecanismos de _prompt caching_ de provedores comerciais (Anthropic, OpenAI, DeepSeek).

Paralelamente aos arquivos Markdown, o framework disponibiliza a interface de _Memory Provider Plugins_, permitindo a integração de backends de persistência estruturados e de longo alcance. A especificação da interface impõe contratos rigorosos de execução e concorrência:

- `initialize(session_id, **kwargs)`: Invocado na inicialização do agente, fornecendo o parâmetro `hermes_home` para isolamento de dados por perfil.
- `sync_turn(user, assistant, *, session_id="", messages=None)`: Disparado após a finalização de cada turno interativo. A especificação do Hermes exige que este método seja estritamente não bloqueante. Qualquer operação intensiva de I/O, indexação vetorial ou computação relacional deve ser despachada para threads de segundo plano (_daemon threads_) para não degradar a latência percebida no diálogo.
- `prefetch(query, *, session_id="")` e `queue_prefetch(query, *, session_id="")`: Ganchos executados, respectivamente, antes da chamada de inferência e após o fechamento do turno, destinados a pré-aquecer índices ou recuperar memórias contextuais.
- `on_pre_compress(messages)`: Executado imediatamente antes da compactação da janela de contexto. Sob a especificação de versão 2 (`pre_compress_checkpoint_api_version = 2`), se o operador configurar `compression.checkpoint_required: true`, o gancho adota semântica _fail-closed_. Qualquer exceção não tratada na persistência bloqueia o processo de compactação destrutiva com o erro `BLOCKED_MISSING_PREREQUISITE`, preservando o histórico bruto original. Os provedores de versão 2 recebem dados normalizados, com filtros automáticos aplicados a ferramentas internas e sumários intermediários prévios marcados com a flag `_compressed_summary`.

### 1.2 O Motor de Contexto e Políticas de Compactação

A governança da janela de contexto no Hermes Agent assenta na classe abstrata `ContextEngine` (`agent/context_engine.py`). O agente implementa por padrão o `ContextCompressor`, que atua sob uma esteira de dois níveis:
- **Gateway Session Hygiene:** Localizado em `gateway/run_turn.py`, atua como uma rede de segurança executada antes de o agente iniciar o turno. Dispara automaticamente caso a extensão do histórico atinja 85% do limite nominal de contexto do modelo, prevenindo estouros causados por acúmulo assíncrono de eventos.
- **Agent ContextCompressor:** Executado internamente em `agent/context_compressor.py` durante o ciclo de iteração de ferramentas. O gatilho padrão dispara ao atingir 50% da janela útil do modelo.

O modo de compactação padrão, denominado _lean tail compaction_, preserva uma cauda mínima delimitada a 2,5% do contexto total (com piso de 10.000 tokens e teto de 25.000 tokens), protegendo as últimas 20 mensagens (`protect_last_n: 20`) e pelo menos uma mensagem de usuário autêntica (`min_tail_user_messages: 1`). O histórico mais antigo é processado por uma chamada LLM auxiliar que extrai deterministicamente identificadores mecânicos (hashes Git, caminhos de arquivo, parâmetros estruturados) e gera ponteiros de recuperação integrados à ferramenta `session_search`.

A classe `ContextEngine` expõe o gancho `select_context()`, que permite reescrever ou podar o array `messages` antes de cada chamada ao provedor de linguagem. Contudo, mutações dinâmicas e imprevisíveis na ordenação das mensagens invalidam o alinhamento de blocos de prefix cache (como os breakpoints de cache da Anthropic), acarretando penalidades severas financeiras e de latência.

### 1.3 A Interface do Model Context Protocol (MCP)

O Hermes Agent incorpora um cliente nativo completo para o Model Context Protocol (especificação 2024-11-05), com suporte a transporte bidirecional via `stdio` e HTTP/SSE. O cliente gerencia o ciclo de vida dos processos acoplados por meio de temporizadores configuráveis: `idle_timeout_seconds`, que encerra processos ociosos para posterior reinicialização transparente, e `max_lifetime_seconds`, que impõe reciclagem periódica de segurança.

As ferramentas expostas por servidores MCP são mapeadas no catálogo interno com prefixos estruturados, seguindo o padrão `mcp_<servidor>_<ferramenta>`. O sistema suporta recarregamento a quente via comando `/reload-mcp`, que reinicia os transportes ativos, reprocessa os manifestos declarados em `config.yaml` e reconfigura as tabelas de símbolos sem demandar o reinício do processo principal do agente.

### 1.4 Delegação de Subagentes e Slots Auxiliares de Inferência

Além do modelo principal de conversação (`model.default`), o Hermes Agent implementa dois mecanismos nativos essenciais para a distribuição de computação heterogênea:

- **Subagent Delegation (`delegate_task`):** Permite disparar instâncias filhas de `AIAgent` que operam em processos ou threads paralelas com contextos e diretórios totalmente isolados. A chamada suporta configurações explícitas de modelo e endpoint (`delegation.model`, `delegation.provider`, `delegation.base_url`), e suporte opcional a isolamento de branches via git worktree (`.worktrees/subagent-*`). Apenas o sumário consolidado da tarefa retorna ao contexto do agente pai, eliminando a poluição do histórico conversacional com chamadas intermediárias de ferramentas.
- **Auxiliary Model Slots:** O Hermes isola 11 tarefas de suporte operacional em modelos dedicados sob o bloco `auxiliary.*` da configuração. Tarefas como geração de títulos (`title_generation`), compressão de histórico (`compression`), classificação de aprovação de segurança (`approval`), extração de páginas web (`web_extract`), triagem kanban (`triage_specifier`) e revisão de habilidades (`curator`) podem ser roteadas para endpoints e modelos especializados de alta vazão e baixo custo, desonerando o modelo denso principal.

## 2. Transporte Heterogêneo e Concorrência IPC no Windows 11 Nativo

A comunicação entre o interpretador Python 3.11 do Hermes Agent e o binário bare-metal `souls_server.exe` em Rust Tokio sob o kernel NT do Windows 11 exige a parametrização cirúrgica do subsistema de I/O da plataforma Microsoft. Ao contrário de ambientes POSIX baseados em sockets de domínio Unix e semântica permissiva de arquivos, o Windows NT impõe primitivos estruturais distintos baseados em I/O Completion Ports (IOCP) e bloqueio obrigatório de arquivos (_mandatory file locking_).

### 2.1 Análise Comparativa de Transportes IPC no Windows 11

A escolha do vetor de transporte entre processos sob o kernel NT define a estabilidade operacional da solução.
- **Standard I/O (`stdio`):** Embora difundido em servidores MCP locais, apresenta vulnerabilidades estruturais no Windows 11. O runtime C da Microsoft (MSVCRT) e a implementação de subprocessos assíncronos do Python impõem buffering automático de 4KB a 8KB em pipes anônimos quando os descritores não estão associados a uma janela interativa de console. Caso o binário Rust não execute esvaziamento imediato (`flush`) após cada mensagem serializada, ou se ocorrer corrupção por conversão implícita de quebras de linha (`\r\n` vs `\n`), o canal entra em impasses irreversíveis (_deadlocks_).
- **Named Pipes Win32 (`\\.\pipe\*`):** Operam com latências na faixa de dezenas de microssegundos ao tirar proveito direto dos mecanismos de IOCP. Contudo, a integração entre o loop de eventos `ProactorEventLoop` do Python e as chamadas assíncronas do Tokio em Rust adiciona complexidade desproporcional na gestão de ACLs de segurança NT e reconexões atômicas.
- **Localhost TCP Loopback com Streamable HTTP/SSE:** Estabelece o arranjo canônico ótimo. Ao utilizar conexões persistentes baseadas em sockets Winsock2 e pilha HTTP/1.1 com Keep-Alive, o sistema elimina as fragilidades de buffering de pipes anônimos, desacopla totalmente os ciclos de vida dos processos e mantém plena aderência ao cliente MCP nativo do Hermes Agent.

| **Dimensão de Análise**         | **STDIO (Pipes Anônimos Win32)**     | **Localhost TCP (Streamable HTTP/SSE)** | **Win32 Named Pipes**                         |
| ------------------------------- | ------------------------------------ | --------------------------------------- | --------------------------------------------- |
| **Throughput Médio**            | 120 a 250 MB/s                       | 600 a 900 MB/s                          | 1.200 a 2.500 MB/s                            |
| **Latência por Turno**          | 200 a 800 $\mu$s                     | 50 a 150 $\mu$s                         | 10 a 40 $\mu$s                                |
| **Modelo Assíncrono NT**        | Emulação sobre Pipes                 | Winsock2 sobre IOCP                     | Nativo (tokio named_pipe)                     |
| **Risco de Impasse (Deadlock)** | Elevado (buffering de bloco MSVCRT)  | Nulo (gestão de fluxo TCP padrão)       | Baixo (dependente de sincronização)           |
| **Complexidade no Python**      | Baixa em POSIX / Instável no Windows | Mínima (bibliotecas HTTP/SSE padrão)    | Elevada (requer rotinas Win32 especializadas) |
| **Compatibilidade Nativa MCP**  | Nativa (padrão CLI)                  | Nativa (transporte HTTP oficial)        | Inexistente (requer wrappers customizados)    |

A especificação canônica padroniza o transporte em **Localhost TCP Loopback com HTTP/SSE**, vinculando o binário `souls_server.exe` exclusivamente na interface `127.0.0.1:9123`.

### 2.2 Isolamento de Dados em Dev Drive ReFS (`Z:\souls_engine\.souls_data\`)

É estritamente proibido fragmentar os dados do sistema entre diretórios `%APPDATA%` e `%LOCALAPPDATA%`. O Souls Engine opera exclusivamente sobre um volume formatado em **Dev Drive ReFS** (Resilient File System), montado sob a unidade `Z:` no caminho canônico:

`Z:\souls_engine\.souls_data\`

O uso do ReFS Dev Drive no Windows 11 proporciona:
1. **Desempenho de I/O Superior:** Alocação em clusters de 4KB com tecnologia _Block Cloning_ (Copy-on-Write no nível do sistema de arquivos), permitindo snapshots atômicos de bancos de dados em tempo quase nulo sem duplicar blocos físicos no NVMe.
2. **Minimização de Antivirus Overhead:** O Microsoft Defender aplica filtros de varredura assíncrona desacoplados em Dev Drives, reduzindo em até 70% a sobrecarga de I/O em operações de banco de dados e arquivos de tensores.
3. **Estrutura Unificada de Diretórios:**
    - `Z:\souls_engine\.souls_data\db\`: Persistência do FrankenSQLite (`souls_state.db`, `souls_state.db-wal`, `souls_state.db-shm`).
    - `Z:\souls_engine\.souls_data\vectors\`: Diretório colunar Apache Arrow / LanceDB.
    - `Z:\souls_engine\.souls_data\models\`: Pesos quantizados em formato GGUF e ONNX.
    - `Z:\souls_engine\.souls_data\spool\`: Arquivos temporários de spooling e auditoria.

### 2.3 FrankenSQLite com Tipagem Rígida STRICT e Modo WAL

A concorrência relacional no Windows exige configurações estritas para evitar os códigos de erro Win32 `ERROR_SHARING_VIOLATION` (código 32) e contenções `SQLITE_BUSY`. As conexões do SQLite gerenciadas pela crate `souls_memory` devem ser inicializadas com as diretivas Win32 `FILE_SHARE_READ | FILE_SHARE_WRITE | FILE_SHARE_DELETE` e aplicar obrigatoriamente as seguintes diretivas PRAGMA no momento da abertura do pool:
- `PRAGMA journal_mode = WAL;`: Desacopla leitores e escritores, permitindo leituras concorrentes ilimitadas durante uma gravação ativa.
- `PRAGMA synchronous = NORMAL;`: Reduz invocações à função `FlushFileBuffers` do Win32, sincronizando apenas nos pontos críticos do WAL e poupando ciclos de escrita física no SSD sem comprometer a integridade transacional contra falhas de processo.
- `PRAGMA busy_timeout = 5000;`: Estabelece uma janela de espera de cinco segundos com recuo exponencial para contenções temporárias de escrita.
- `PRAGMA temp_store = MEMORY;`: Força tabelas temporárias e ordenações a residirem exclusivamente na RAM.
- `PRAGMA foreign_keys = ON;`: Garante integridade referencial relacional estrita.

Todas as tabelas criadas no FrankenSQLite adotam obrigatoriamente a cláusula `STRICT`, impondo validação rígida de tipos no motor de persistência em tempo de execução:

```
CREATE TABLE IF NOT EXISTS epistemic_memories (
    id TEXT PRIMARY KEY NOT NULL,
    category TEXT NOT NULL,
    content TEXT NOT NULL,
    partition TEXT NOT NULL CHECK(partition IN ('STABLE', 'EVOLVING')),
    salience REAL NOT NULL,
    access_count INTEGER NOT NULL,
    created_at INTEGER NOT NULL,
    updated_at INTEGER NOT NULL
) STRICT;
```

### 2.4 Governança Exclusiva de `mmap` do LanceDB sob Semântica Win32

O motor vetorial LanceDB utiliza estruturas colunares orientadas ao formato Apache Arrow, dependendo extensivamente de arquivos mapeados em memória virtual (`mmap`) através de chamadas da API Win32 (`CreateFileMappingW` e `MapViewOfFile`). No Windows NT, qualquer arquivo mantido em memória virtual impede operações de exclusão, truncamento ou reescrita por outros descritores de processo, disparando instantaneamente o erro `ERROR_USER_MAPPED_FILE` (código Win32 1224).

Em virtude desse bloqueio obrigatório, **o acesso aos arquivos do LanceDB em `Z:\souls_engine\.souls_data\vectors\` é delegado com exclusividade absoluta ao binário `souls_server.exe`**. O interpretador Python do Hermes Agent nunca deve tentar instanciar bibliotecas LanceDB ou ler esses arquivos diretamente. Toda e qualquer operação de busca semântica, inserção de embeddings ou compactação vetorial deve ser despachada para o Souls Engine via JSON-RPC / HTTP.

## 3. Matriz Canônica de Responsabilidades & Catálogo de Ferramentas MCP

A delimitação operacional estrita impede duplicações de escopo, concorrência destrutiva sobre arquivos e desperdício de recursos computacionais.

### 3.1 Fronteiras Funcionais entre Hermes Agent e Souls Engine

- **Soberania do Hermes Agent:** Mantém o controle executivo da sessão, o loop cognitivo de raciocínio, a gestão exclusiva de `MEMORY.md` e `USER.md` (preservando o frozen snapshot de prompt cache), a execução de comandos de terminal no shell MinGit nativo (`%LOCALAPPDATA%\hermes\git`) e a conexão direta com provedores de nuvem comerciais para o chat principal.
- **Soberania do Souls Engine:** Atua como um daemon headless de infraestrutura. Governa a memória profunda de longo prazo (FrankenSQLite + LanceDB), executa análise estática de repositórios em alta velocidade com gramáticas Tree-sitter em código nativo Rust, monitora o silício local via NVML e orquestra a inferência local bare-metal para subtarefas delegadas.

| **Domínio de Sistema**              | **Hermes Agent (Python 3.11)**                                                   | **Souls Engine (Rust / Tokio)**                                                | **Delimitação Arquitetural Canônica**                                                         |
| ----------------------------------- | -------------------------------------------------------------------------------- | ------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------- |
| **Memória Operacional Primária**    | Gestão exclusiva de `MEMORY.md` e `USER.md` (~1.300 tokens de teto).             | Leitura passiva em cold boot; **escrita direta estritamente proibida**.        | Exclusivo Hermes. Preserva a integridade do prefix cache de prompt.                           |
| **Memória Profunda e Vetorial**     | Emissão de requisições estruturadas JSON-RPC via adapter.                        | Gestão soberana do FrankenSQLite STRICT WAL, LanceDB e LadybugDB.              | Delegado ao Souls Engine. Repositório hipocampal de longo prazo.                              |
| **Terminal e Shell OS**             | Execução de comandos no MinGit nativo com políticas de aprovação.                | Nenhuma invocação de processos de sistema operacional.                         | Exclusivo Hermes. Elimina conflitos de PATH, concorrência de handles e variáveis de ambiente. |
| **Análise Estrutural de Código**    | Consumo de fragmentos sintáticos limpos via ferramentas MCP.                     | Parsing nativo via Tree-sitter, extração de esqueletos, Myers Diff e Gitoxide. | Delegado ao Souls Engine. Executado em memória bare-metal sem inflar o runtime Python.        |
| **Chamadas L7 ao Modelo Principal** | Resolução direta de provedores de nuvem, streaming SSE e rotação de chaves.      | **Nenhum papel de proxy intermediário**.                                       | Exclusivo Hermes. Previne quebra de streaming, latência espúria e perda de cache.             |
| **Inferência Local e Auxiliar**     | Orquestração de subagentes (`delegate_task`) e slots auxiliares (`auxiliary.*`). | Execução de ONNX CPU, Gemma-2-2B CPU probing e Qwen Coder CUDA na dGPU.        | Delegado ao Souls Engine sob governança de hardware e ParetoBandit.                           |

### 3.2 Especificação do Catálogo de Ferramentas MCP

O binário `souls_server.exe` expõe o endpoint MCP na rota `/mcp` via transporte HTTP/SSE, disponibilizando as seguintes ferramentas canônicas:

#### 1. `souls_ast_outline`

- **Finalidade:** Gera o esqueleto estrutural de um arquivo de código-fonte, suprimindo integralmente os blocos de implementação internos.
- **Linguagens Suportadas:** Rust, Python, TypeScript, Go.
- **Parâmetros:**
    - `path` (string, obrigatório): Caminho do arquivo relativo ao workspace.
- **Mecanismo:** Percorre a AST concreta do Tree-sitter em Rust, preservando assinaturas de funções, definições de structs/classes, traits/interfaces e docstrings associadas. Substitui blocos de código por marcadores de desidratação (ex: `/* ... implementation omitted [24 lines] ... */`).
- **Economia Típica:** Redução de 70% a 85% no consumo de tokens em comparação com `read_file`.

#### 2. `souls_ast_slice`

- **Finalidade:** Extrai cirurgicamente um fragmento específico da AST com base em coordenadas semânticas de escopo.
- **Parâmetros:**
    - `path` (string, obrigatório): Caminho do arquivo.
    - `symbol_query` (string, obrigatório): Nome do método, função ou tipo estrutural desejado (ex: `AIAgent::step`).
- **Mecanismo:** Realiza travessia Tree-sitter em busca do nó identificador correspondente e retorna exclusivamente o corpo e contexto do símbolo solicitado, sem carregar o arquivo integral na memória do agente.

#### 3. `souls_myers_diff`

- **Finalidade:** Computa a diferença mínima estruturada entre dois blocos textuais ou entre o estado atual de um arquivo e um conteúdo proposto.
- **Parâmetros:**
    - `path` (string, obrigatório): Arquivo de destino.
    - `new_content` (string, obrigatório): Novo conteúdo proposto.
- **Mecanismo:** Executa o algoritmo de Myers Diff com verificação de invariantes sintáticas, garantindo que patches parciais não quebrem o balanceamento de chaves ou a estrutura de declarações.

#### 4. `souls_repo_heatmap`

- **Finalidade:** Mapeia a temperatura e relevância operacional dos arquivos do repositório para orientar o agente sobre áreas de intensa mutação.
- **Parâmetros:**
    - `max_results` (integer, opcional, default: 20): Quantidade máxima de arquivos retornados.
    - `time_window_days` (integer, opcional, default: 30): Janela temporal de análise de commits.
- **Mecanismo:** Utiliza a biblioteca `gitoxide` (`gix`) para inspecionar os commits locais no repositório sem disparar subprocessos externos. Calcula a métrica de **Frecency** ($F$) para cada arquivo:

$$F(\text{arquivo}) = \sum_{c \in \text{Commits}} \text{Changes}(c) \cdot e^{-\lambda (t_{\text{atual}} - t_c)}$$

Onde $\lambda = \frac{\ln(2)}{t_{\text{half-life}}}$, com meia-vida padrão calibrada para 7 dias. Retorna a lista de arquivos priorizada por calor.

#### 5. `souls_memory_recall`

- **Finalidade:** Recupera memórias contextuais profundas através de busca híbrida com fusão recíproca.
- **Parâmetros:**
    - `query` (string, obrigatório): Texto de pesquisa conceitual ou técnica.
    - `limit` (integer, opcional, default: 5): Quantidade de memórias a recuperar.
- **Mecanismo:** Dispara em paralelo uma busca léxica via BM25 (SQLite FTS5) e uma busca de similaridade por cosseno no LanceDB (embeddings ONNX). Os ranqueamentos são consolidados via Reciprocal Rank Fusion (RRF) e filtrados pela barreira ontológica do LadybugDB.

#### 6. `souls_route_subtask`

- **Finalidade:** Recomenda o Tier ótimo de execução e parâmetros de modelo para o despacho de uma subtarefa delegada.
- **Parâmetros:**
    - `task_description` (string, obrigatório): Descrição do objetivo da subtarefa.
    - `estimated_tokens` (integer, obrigatório): Extensão projetada da entrada e saída.
    - `requires_code_generation` (boolean, obrigatório): Indica se a tarefa envolve síntese de código.
- **Mecanismo:** Executa o algoritmo contextual ParetoBandit cruzando a complexidade inferida com a telemetria do hardware e a tabela FinOps, retornando o Tier recomendado, o endpoint exato e os hiperparâmetros de amostragem.

## 4. Padrão de Acoplamento Técnico

A viabilidade arquitetural de integração entre o ecossistema Python do Hermes e o binário nativo em Rust foi avaliada sob três abordagens técnicas.

### 4.1 Análise Comparativa de Abordagens de Integração

1. **Abordagem 1: Sidecar Passivo Puro (Apenas MCP HTTP):**
    - _Mecanismo:_ O binário Rust opera como servidor MCP genérico. O Hermes conecta-se apenas via catálogo de ferramentas.
    - _Limitação:_ Impede a integração com os ganchos do ciclo de vida de memória (`sync_turn`, `on_pre_compress`). A memória profunda depende de chamadas manuais de ferramentas pelo modelo, sem sincronização passiva de sessões.
2. **Abordagem 2: Plugin Nativo In-Process via PyO3 / C-ABI:**
    - _Mecanismo:_ Compilação do código Rust como dynamic link library (`souls_ffi.pyd`) importada diretamente no processo Python.
    - _Limitação:_ Riscos severos de estabilidade no Windows 11. Qualquer falha de acesso à memória ou incompatibilidade na toolchain C/C++ entre o gerenciador `uv` do Hermes e o compilador MSVC derruba instantaneamente o processo Python. Conflitos crônicos entre o Tokio Runtime e o Global Interpreter Lock (GIL) geram bloqueios mútuos (_deadlocks_).
3. **Abordagem 3: Modelo Híbrido Desacoplado (Canônico):**
    - _Mecanismo:_ O Souls Engine é executado como daemon autônomo bare-metal (`souls_server.exe`). No Hermes Agent, implanta-se um **Thin Adapter** em Python puro em `$HERMES_HOME/plugins/memory/souls/`, herdando de `MemoryProvider`.
    - _Vantagens:_ Isolamento total de falhas (crash safety), aderência perfeita aos contratos do Hermes, suporte a processamento assíncrono não bloqueante via threads daemon em Python e eliminação de dependências binárias cruzadas.

| **Métrica / Dimensão Técnica**          | **Abordagem 1: Sidecar Passivo** | **Abordagem 2: In-Process (PyO3)**  | **Abordagem 3: Híbrido Desacoplado (Canônico)** |
| --------------------------------------- | -------------------------------- | ----------------------------------- | ----------------------------------------------- |
| **Latência por Invocação MCP**          | 1 a 3 ms (Loopback TCP)          | < 0,1 ms (In-process)               | 1 a 3 ms (Loopback TCP)                         |
| **Latência no Ciclo de Memória**        | Não implementa ganchos           | < 0,2 ms (In-process)               | < 1 ms (Thread daemon assíncrona)               |
| **Isolamento de Falhas (Crash Safety)** | Total (Processos isolados)       | Nulo (Crash em Rust derruba Python) | Total (Processos isolados)                      |
| **Complexidade de Compilação Win32**    | Nula (Binário Rust independente) | Crítica (Linkage MSVC / uv Python)  | Nula (Python puro + Binário Rust)               |
| **Impacto no Streaming SSE de Tokens**  | Nulo (Sem proxy L7)              | Nulo                                | Nulo (Sem proxy L7)                             |
| **Aderência aos Contratos do Hermes**   | Média (Apenas ferramentas)       | Alta (Implementa ABC nativa)        | Perfeita (Implementa ABC e cliente MCP)         |

### 4.2 Estrutura do Thin Adapter Python

O adaptador reside em `$HERMES_HOME/plugins/memory/souls/__init__.py`:

```
import threading
import requests
from agent.memory_provider import MemoryProvider

class SoulsMemoryProvider(MemoryProvider):
    pre_compress_checkpoint_api_version = 2

    def __init__(self):
        super().__init__()
        self.endpoint = "http://127.0.0.1:9123/api/v1/memory"
        self.session = requests.Session()

    def initialize(self, session_id: str, **kwargs) -> None:
        self.session_id = session_id

    def sync_turn(self, user: str, assistant: str, *, session_id: str = "", messages=None) -> None:
        # Despacho estritamente não bloqueante em daemon thread
        payload = {
            "session_id": session_id or self.session_id,
            "user_turn": user,
            "assistant_turn": assistant,
            "messages": messages or []
        }
        t = threading.Thread(target=self._post_turn, args=(payload,), daemon=True)
        t.start()

    def _post_turn(self, payload: dict) -> None:
        try:
            self.session.post(f"{self.endpoint}/turn", json=payload, timeout=3.0)
        except Exception:
            # Falhas no sync_turn degradam graciosamente sem travar o agente
            pass

    def on_pre_compress(self, messages: list) -> None:
        # Semântica síncrona com API v2: fail-closed sob checkpoint obrigatório
        payload = {
            "session_id": self.session_id,
            "messages": messages,
            "api_version": self.pre_compress_checkpoint_api_version
        }
        resp = self.session.post(f"{self.endpoint}/pre_compress", json=payload, timeout=5.0)
        if resp.status_code != 200:
            raise RuntimeError(f"BLOCKED_MISSING_PREREQUISITE: Souls Memory persistence failed with status {resp.status_code}")
```

## 5. Taxonomia dos 8 Tiers de Modelos e Governança Físico-Computacional

A arquitetura do Souls Engine estabelece uma hierarquia de inteligência em oito Tiers funcionais, balanceando rigorosamente custo financeiro, velocidade de execução e alinhamento de contexto contra as restrições físicas do silício local: CPU Intel Core i9 (16 threads com extensões AVX2), 32 GB de RAM DDR4 física e GPU dedicada NVIDIA GeForce RTX 2060m com 6.144 MB de VRAM GDDR6.

### 5.1 Especificação e Mapeamento dos 8 Tiers Funcionais

- **Tier 0 (Classificação e Filtro Síncrono):** Modelos `ModernBERT` ou `GLiClass Multilang Ultra` operando via ONNX Runtime acoplado exclusivamente à CPU (extensões vetoriais AVX2 / OpenVINO). Executa triagem síncrona de intenções, classificação de tarefas, detecção de injeções adversárias de prompt e gating de RAG em menos de 15 ms, com consumo rigorosamente nulo de VRAM ($0\text{ MB}$).
- **Tier 0.5 (Epistêmico e Logit Probing):** Modelo `Gemma-2-2B` em formato GGUF executado em CPU através do runtime llama.cpp interno. Não executa geração livre de texto: extrai probabilidades brutas (logits) de tokens de controle para calcular a entropia informacional e o grau de incerteza de fragmentos conceituais em cerca de 100 ms.
- **Tier 1 (Maestro Local / Live Coder):** Modelos `Qwen2.5-Coder-3B-Instruct` ou `Qwen2.5-Coder-7B` (quantizado em Q3_K_M) alocados na dGPU RTX 2060m. Opera via llama.cpp nativo com aceleração CUDA, decodificação guiada via `llguidance` e KV Cache assimétrico (chaves K em FP16 e valores V quantizados em Q4_0 com FlashAttention-2). Garante geração superior a 45 tokens/s para síntese de código e raciocínio rápido.
- **Tier 1.5 CLI (CLI Agêntica Especializada de Custo Fixo):** Ferramentas corporativas em linha de comando (Claude Code, OpenAI Codex CLI) orquestradas pelo Hermes via terminais virtuais isolados (`pty=true`). Consomem cotas fixas de planos de desenvolvedor sem gerar faturamento de tokens por chamada HTTP.
- **Tier 2 (Local Heavy - MoE Assíncrono):** Modelos Mixture-of-Experts compactos (ex: arquiteturas MoE 33B com 3B a 4B ativos por token) rodando em background com particionamento híbrido: camadas críticas de atenção na dGPU (1.500 MB) e especialistas descarregados na RAM física (16 GB). Destinado a refatorações complexas em lote disparadas por subagentes desacoplados.
- **Tier 3 (Nuvem Fast):** Modelos comerciais de altíssima vazão e baixo custo por token (DeepSeek V3/V4 Flash, Gemini 2.5 Flash) consumidos diretamente pelo Hermes via API externa HTTPS. Aplicado a extração de dados web, sumarização massiva de logs e slots auxiliares.
- **Tier 4 (Nuvem Heavy - O Cérebro Denso):** Modelos de fronteira analítica e raciocínio profundo (Claude 3.5 Sonnet, DeepSeek V4 Pro, GPT-5). Invocados exclusivamente pelo modelo de governança principal do Hermes para planejamento de alto nível, decomposição de DAGs e auditoria arquitetural crítica.
- **Tier 5 (Nuvem Free Contingency):** Endpoints gratuitos de contingência (OpenRouter Free Tiers) configurados na esteira de `fallback_providers` do Hermes para manter o sistema operacional em caso de esgotamento de cotas de APIs comerciais.

| **Tier**         | **Modelo / Carga de Trabalho**  | **Runtime / Dispositivo**    | **Consumo VRAM** | **Consumo RAM** | **Latência Típica**   |
| ---------------- | ------------------------------- | ---------------------------- | ---------------- | --------------- | --------------------- |
| **Tier 0**       | ModernBERT / GLiClass           | ONNX Runtime (CPU AVX2)      | 0 MB             | ~ 350 MB        | < 15 ms               |
| **Tier 0.5**     | Gemma-2-2B (GGUF)               | llama.cpp (CPU Probing)      | 0 MB             | ~ 1.800 MB      | ~ 100 ms              |
| **Tier 1**       | Qwen2.5-Coder-3B/7B (Q3_K_M)    | llama.cpp (CUDA dGPU)        | ~ 3.000 MB       | ~ 500 MB        | 20 a 25 ms (TTFT)     |
| **Tier 1.5 CLI** | Claude Code / Codex CLI         | Processo PTY Nativo          | 0 MB             | ~ 400 MB        | N/A (CLI externa)     |
| **Tier 2**       | Compact MoE (33B/3B)            | llama.cpp (Híbrido dGPU+RAM) | ~ 1.500 MB       | ~ 16.000 MB     | 150 a 300 ms          |
| **Tier 3**       | DeepSeek Flash / Gemini Flash   | API Externa (HTTPS)          | 0 MB             | Negligível      | 200 a 400 ms          |
| **Tier 4**       | Claude 3.5 Sonnet / V4 Pro      | API Externa (HTTPS)          | 0 MB             | Negligível      | 800 a 1.500 ms        |
| **Tier 5**       | Provedores Gratuitos OpenRouter | API Externa (HTTPS)          | 0 MB             | Negligível      | Variável (Fila livre) |

### 5.2 Governança Física da RTX 2060m e Teto Útil de 5.2GB no WDDM

O Windows Display Driver Model (WDDM 3.x) no Windows 11 aloca compulsoriamente entre 500 MB e 850 MB de VRAM para a composição gráfica do Desktop Window Manager (`dwm.exe`) e aceleração da interface gráfica. A margem útil real da GPU dedicada é expressa por:

$$\text{VRAM}_{\text{disponível}} = \text{VRAM}_{\text{física}} - \text{VRAM}_{\text{WDDM}} = 6.144\text{ MB} - 850\text{ MB} \approx 5.294\text{ MB}$$

Caso as alocações da GPU ultrapassem 5.294 MB, o subsistema de memória do Windows ativa compulsoriamente o **Shared GPU Memory Fallback**, descarregando tensores excedentes para a memória RAM através do barramento PCIe 3.0 x16. A consequência imediata é o colapso catastrófico da taxa de geração: a vazão despenca de 45+ tokens/s para menos de 1,5 tokens/s, paralisando a execução do agente.

Para garantir estabilidade contínua, o Souls Engine impõe uma política de **Exclusão Mútua de VRAM**:

1. Os Tiers 1 e 2 **nunca coexistem simultaneamente na VRAM dedicada**.
2. Em regime padrão com o Tier 1 ativo, os tensores do modelo (2.100 MB) e o KV Cache assimétrico de 8.192 tokens (900 MB) totalizam 3.000 MB de alocação CUDA. Somados aos 850 MB do WDDM, a ocupação agregada atinge 3.850 MB, assegurando uma folga térmica e de fragmentação de **1.444 MB livres** na dGPU.
3. Caso uma carga assíncrona pesada de Tier 2 seja despachada, o Souls Engine executa um ciclo transacional de desalocação: descarrega os tensores do Tier 1 da GPU (`cudaFree`) antes de alocar as matrizes do Tier 2 ou, alternativamente, desvia a execução do Tier 2 integralmente para a CPU Intel i9 utilizando instruções AVX2.

### 5.3 Leitor GGUF Zero-Copy em $\mathcal{O}(1)$ via `memmap2`

Para inspecionar pesos locais sem incorrer em alocações custosas na memória RAM nem disparar I/O desnecessário no NVMe, as crates `souls_inference_runtime` e `souls_llm_local_arena` utilizam um parser binário GGUF estruturado sobre a crate Rust `memmap2`.

Ao invés de ler o arquivo de modelo via streams de bytes convencionais (`read_to_end`), o Souls Engine instancia uma visualização de memória virtual compartilhada com o kernel NT:

```
use memmap2::MmapOptions;
use std::fs::File;

pub struct GgufMetadataViewer {
    _file: File,
    mmap: memmap2::Mmap,
}

impl GgufMetadataViewer {
    pub fn open(path: &std::path::Path) -> Result<Self, std::io::Error> {
        let file = File::open(path)?;
        // Mapeamento em memória virtual sem carregar blocos físicos
        let mmap = unsafe { MmapOptions::new().map(&file)? };
        Ok(Self { _file: file, mmap })
    }

    pub fn parse_header_o1(&self) -> Result<GgufHeaderInfo, ParseError> {
        // Validação de número mágico "GGUF" nos primeiros 4 bytes
        if &self.mmap[0..4] != b"GGUF" {
            return Err(ParseError::InvalidMagic);
        }
        // Inspeção direta de ponteiros binários: versão, contagem de tensores e metadados
        // Complexidade O(1) sem alocar os gigabytes de tensores de pesos
        // ... decodificação de pares chave-valor de metadados ...
        Ok(header_info)
    }
}
```

Essa abordagem permite verificar dinamicamente a arquitetura do modelo, a extensão do contexto nativo, a tipagem de quantização e os tensores de alinhamento com custo computacional nulo e consumo de memória RAM inferior a 128 KB.

### 5.4 O Watchdog Termodinâmico NVML

A crate `souls_inference_runtime` executa uma tarefa assíncrona dedicada em background conectada à NVIDIA Management Library (`nvml-wrapper`), coletando métricas a cada 500 milissegundos:

- **Faixa Nominal (**$\text{VRAM}_{\text{livre}} > 1.400\text{ MB}$ **e** $T_{\text{GPU}} < 75^\circ\text{C}$**):** Permissão plena para despacho de inferência do Tier 1 na dGPU com aceleração CUDA.
- **Faixa Preventiva (**$800\text{ MB} < \text{VRAM}_{\text{livre}} \le 1.400\text{ MB}$ **ou** $75^\circ\text{C} \le T_{\text{GPU}} < 82^\circ\text{C}$**):** Restrição a novas alocações de contexto. O motor reduz o limite de geração de novos tokens e força o redirecionamento de embeddings para o runtime ONNX em CPU.
- **Faixa Crítica (**$\text{VRAM}_{\text{livre}} \le 800\text{ MB}$ **ou** $T_{\text{GPU}} \ge 82^\circ\text{C}$**):** Suspensão compulsória de inferência na GPU. Os contextos locais são desalojados imediatamente, executando-se `cudaDeviceReset`. As requisições pendentes são desviadas de forma graciosa para nuvem (Tier 3) ou processamento em CPU.

## 6. O Mecanismo ParetoBandit, FinOps (Métrica $E^3$) e Desidratação Sintática LEAN

O despacho inteligente de computação entre modelos locais bare-metal e provedores de nuvem comerciais exige uma formulação matemática rigorosa que balanceie qualidade de resposta, consumo orçamentário e integridade de contexto.

### 6.1 Roteamento em Subagentes versus Proibição de Comutação Intradiálogo

É estritamente proibido aplicar o chaveamento de modelos pelo ParetoBandit turno-a-turno dentro da conversa principal interativa (_intradialogue turn-level model switching_).

Em provedores comerciais de nuvem (Anthropic, OpenAI, DeepSeek), os blocos de _prompt caching_ são gerados e preservados nos clusters de inferência com base na identidade estrita do modelo e na chave de autorização. Se um sistema comuta o modelo principal no meio de um diálogo (por exemplo, alternando do Claude 3.5 Sonnet para o DeepSeek Flash), o novo modelo não possui o histórico em cache. O provedor é forçado a reprocessar todos os tokens acumulados pela tarifa de entrada cheia (_uncached input tokens_), gerando um pico financeiro e de latência que anula qualquer ganho pontual.

Por essa razão, o algoritmo **ParetoBandit** do Souls Engine atua exclusivamente em dois pontos de desacoplamento estruturais do Hermes Agent:

1. **Despacho de Subagentes (`delegate_task`):** Subagentes são instâncias limpas de `AIAgent` geradas para resolver tarefas autocontidas em diretórios ou branches isoladas (`.worktrees/subagent-*`). Como iniciam com histórico zero, não dependem do prefix cache da conversa principal. O ParetoBandit determina dinamicamente o modelo e endpoint ótimos a serem injetados nos parâmetros `delegation.model` e `delegation.base_url`.
2. **Resolução de Slots Auxiliares (`auxiliary.*`):** O Hermes Agent delega tarefas rotineiras de suporte (resumos de compressão, extração web, triagem de kanban) para modelos especializados. O ParetoBandit aloca essas cargas para os Tiers 0, 0.5, 1 ou 3 com base no contexto específico da subtarefa.

### 6.2 Formalização Matemática do ParetoBandit Multiobjetivo

O ParetoBandit é modelado como um problema de _Multi-Armed Bandit Contextual_ multiobjetivo com amostragem bayesiana (Thompson Sampling) estendido por uma função de penalização termodinâmica.

Para cada subtarefa $i$, caracterizada por um vetor de contexto $x_i \in \mathbb{R}^d$ (composto por: extensão projetada da entrada em tokens, densidade de referências a código-fonte, complexidade ciclomática estimada e profundidade de raciocínio requerida), o despachante avalia os braços elegíveis correspondentes aos Tiers $k \in \{0.5, 1, 1.5\text{-CLI}, 2, 3, 4, 5\}$.

A função de utilidade escalarizada $U(k \mid x_i)$ a ser maximizada é definida por:

$$U(k \mid x_i) = w_q \cdot \hat{Q}_k(x_i) - w_c \cdot C_k(x_i) - w_l \cdot \hat{L}_k(x_i) - \Phi(T_{\text{GPU}}, V_{\text{livre}}, k)$$

Onde:

- $\hat{Q}_k(x_i) \in [0, 1]$: Amostra da distribuição de qualidade estimada do Tier $k$ para o contexto $x_i$, extraída via Thompson Sampling a partir de distribuições Beta a posteriori $\text{Beta}(\alpha_{k,c}, \beta_{k,c})$, estratificadas pela categoria de tarefa $c$. Os hiperparâmetros $\alpha$ e $\beta$ são atualizados continuamente com base em feedback estrutural (sucesso na compilação, aprovação de testes unitários, validação sintática do diff).
- $C_k(x_i) \ge 0$: Custo financeiro direto projetado para a execução no Tier $k$ ($C_k = 0$ para todos os Tiers locais 0, 0.5, 1, 2 e para o Tier 1.5 CLI de cota fixa).
- $\hat{L}_k(x_i)$: Latência esperada de conclusão em segundos, modelada por regressão linear baseada na volumetria estimada de tokens.
- $w_q, w_c, w_l$: Coeficientes de preferência do operador para calibração da fronteira de Pareto (configuração padrão: $w_q = 0,45$, $w_c = 0,35$, $w_l = 0,20$).
- $\Phi(T_{\text{GPU}}, V_{\text{livre}}, k)$: Função de barreira termodinâmica imposta pelo Watchdog NVML. Para Tiers que não utilizam a dGPU (Tiers 0, 0.5, 1.5 CLI, 3, 4, 5), $\Phi = 0$. Para Tiers que requisitam a dGPU (Tier 1 e Tier 2 em modo acelerado):

$$\Phi(T_{\text{GPU}}, V_{\text{livre}}, k) = \begin{cases} 0 & \text{se } V_{\text{livre}} > 1.400\text{ MB e } T_{\text{GPU}} < 75^\circ\text{C} \\ \lambda \cdot \frac{1.400 - V_{\text{livre}}}{600} & \text{se } 800\text{ MB} < V_{\text{livre}} \le 1.400\text{ MB} \\ +\infty & \text{se } V_{\text{livre}} \le 800\text{ MB ou } T_{\text{GPU}} \ge 82^\circ\text{C} \end{cases}$$

Sob exaustão de VRAM ou estresse térmico, a penalização torna-se infinita ($\Phi = +\infty$), forçando o algoritmo a eleger soluções viáveis em CPU ou na nuvem de forma imediata e matemática.

### 6.3 A Métrica $E^3$ de Eficiência FinOps

Para auditoria retrospectiva de despesas e calibração dos priors do ParetoBandit, o Souls Engine registra a **Métrica** $E^3$ **(Economia, Eficiência, Eficácia)** para cada ciclo de execução:

$$E^3 = \frac{\text{Eficácia (Score Estrutural } \in [0, 1])}{\text{Custo Direto (USD)} \cdot \text{Latência Total (s)} + \epsilon}$$

Execuções no Tier 1 local, com custo direto $C = 0$, atingem valores de $E^3$ ordens de magnitude superiores aos de modelos comerciais de nuvem, desde que a tarefa seja concluída com aprovação estrutural (Eficácia $= 1,0$). Se o modelo local falhar em gerar um patch válido, a Eficácia colapsa para zero, forçando a atualização bayesiana do ParetoBandit a preferir o Tier 3 ou Tier 4 na iteração subsequente daquela classe de tarefas.

### 6.4 Desidratação Sintática LEAN na Origem (Source-Side Dehydration)

A compactação de contexto de código-fonte via análise de AST não deve ser realizada através de proxies interceptadores de rede. Modificar streams de tokens em trânsito gera riscos graves de corrupção em chamadas de ferramentas nativas do Hermes (`<tool_call>`, parâmetros JSON encadeados) e invalida blocos de strings literais.

O Souls Engine estabelece o paradigma da **Desidratação na Origem (Source-Side Dehydration)**, operando estritamente dentro do catálogo de ferramentas MCP:

- O agente substitui chamadas primitivas de leitura integral (`read_file`) pela ferramenta `mcp_souls_ast_outline`.
- A crate `souls_ast` processa o arquivo-alvo utilizando o parser nativo Tree-sitter em Rust, extrai a árvore sintática concreta e descarta os corpos internos das funções, preservando declarações públicas, tipos estruturais e docstrings.
- O payload é entregue ao agente desidratado, reduzindo o volume de tokens entre 70% e 85% com tempo de parsing inferior a 2 ms, prevenindo a poluição precoce do contexto e eliminando o _context rot_.

## 7. Subsistema de Memória Profunda: FrankenSQLite, LanceDB, LadybugDB e Chyros Daemon

A retenção de longo prazo e a recuperação cognitiva do Souls Engine são governadas pela **Tríade de Memória L3**, operando com isolamento estrito contra SSD write amplification e alucinações contextuais.

### 7.1 Pipeline de I/O Desidratado via MPSC (Proteção contra SSD Write Amplification)

Para poupar a vida útil das células de memória do drive NVMe contra o desgaste físico provocado por Write Amplification, as crates `souls_core` e `souls_memory` proíbem transações síncronas em disco para eventos frequentes de auditoria, logs e telemetria.

Esses eventos são emitidos em canais assíncronos sem bloqueio (`tokio::sync::mpsc::channel(10000)`). Uma tarefa de segundo plano coleta os payloads acumulados na memória RAM e executa a descarga no FrankenSQLite em **lotes transacionais atômicos consolidados a cada 5 segundos**:

```
pub async fn telemetry_flusher_daemon(
    mut rx: tokio::sync::mpsc::Receiver<TelemetryEvent>,
    pool: SqlitePool,
) {
    let mut buffer = Vec::with_capacity(500);
    let mut interval = tokio::time::interval(std::time::Duration::from_secs(5));

    loop {
        tokio::select! {
            Some(event) = rx.recv() => {
                buffer.push(event);
                if buffer.len() >= 500 {
                    flush_batch(&mut buffer, &pool).await;
                }
            }
            _ = interval.tick() => {
                if !buffer.is_empty() {
                    flush_batch(&mut buffer, &pool).await;
                }
            }
        }
    }
}
```

Essa disciplina de engenharia reduz o volume de gravações físicas no NVMe em mais de 94% sob regime de uso contínuo do agente.

### 7.2 Fusão Recíproca de Ranqueamento (RRF)

A recuperação contextual profunda combina busca por palavras-chave (BM25 via FTS5 no SQLite) e busca vetorial de cosseno no LanceDB. Para unificar os dois conjuntos de resultados sem depender de calibração frágil de scores heterogêneos, o Souls Engine emprega o algoritmo **Reciprocal Rank Fusion (RRF)**:

$$RRF(d) = \sum_{m \in M} \frac{1}{k + r_m(d)}$$

Onde:

- $M = \{\text{BM25}, \text{LanceDB}\}$ representa o conjunto de métodos de busca.
- $r_m(d)$ é a posição ordinal do documento $d$ no ranking retornado pelo método $m$ ($1, 2, \dots$).
- $k$ é a constante canônica de suavização, calibrada para $k = 60$.

Os blocos de memória recuperados são ordenados pela pontuação $RRF(d)$ decrescente e submetidos à validação ontológica antes da entrega final ao Hermes.

### 7.3 LadybugDB: Grafo Ontológico de Dependências

O **LadybugDB** reside em memória como uma estrutura de grafo indexada na crate `souls_memory`. Cada nó representa uma entidade conceitual do usuário (módulos de software, regras de negócio, ADRs, restrições financeiras) e as arestas representam dependências lógicas (`depends_on`, `conflicts_with`, `implements`).

Ao recuperar memórias candidatas via RRF, o LadybugDB executa uma **travessia em largura (BFS)** restrita a partir dos nós envolvidos na consulta:

1. **Barreira contra Alucinação:** Se uma memória recuperada vetorialmente conflitar logicamente com uma decisão arquitetural imutável através de uma aresta `conflicts_with`, o LadybugDB expurga a memória candidata imediatamente.
2. **Injeção de Pré-requisitos:** Se a consulta recuperar um nó que possui uma dependência obrigatória (`depends_on`), o nó pai é injetado automaticamente no pacote de contexto entregue ao Hermes, eliminando lacunas de entendimento no raciocínio do modelo.

### 7.4 Chyros Daemon & Langevin Decay (Metabolismo Epistêmico Noturno)

O **Chyros Daemon** é o processo de manutenção assíncrona profunda do Souls Engine, disparado durante períodos de ociosidade noturna da CPU Intel i9.

O sistema divide as memórias persistidas em duas partições ontológicas:

- **Partição STABLE:** Contém decisões arquiteturais (ADRs), regras de diretório, contratos de API e convenções permanentes do usuário. Possui imunidade absoluta ao decaimento epistêmico ($\text{Salience} \equiv 1,0$).
- **Partição EVOLVING:** Armazena notas efêmeras de sessões, rascunhos de deliberação, logs transitórios de depuração e suposições provisórias.

Sobre as memórias da partição `EVOLVING`, o Chyros Daemon aplica a formulação estocástica da **Equação de Langevin**:

$$\frac{d S_m(t)}{dt} = -\gamma \cdot S_m(t) + \sigma \cdot \xi(t)$$

Onde:

- $S_m(t) \in [0, 1]$ representa a saliência epistêmica da memória $m$ no tempo $t$.
- $\gamma$ é o coeficiente de decaimento determinístico, calibrado proporcionalmente ao inverso da frequência de acesso da memória ($\gamma \propto \frac{1}{\text{AccessCount} + 1}$).
- $\sigma \cdot \xi(t)$ representa o termo estocástico (ruído gaussiano) modelando ressonâncias acidentais ou associações semânticas indiretas em tarefas recentes.

Ao final do ciclo noturno:

1. As memórias cuja saliência cair abaixo do limiar crítico ($S_m < 0,15$) são definitivamente expurgadas do FrankenSQLite e do LanceDB.
2. O Chyros Daemon executa compulsoriamente a instrução `VACUUM INTO 'Z:\souls_engine\.souls_data\db\souls_state_compact.db'` no FrankenSQLite, desfragmentando páginas relacionais sem fragmentar o sistema de arquivos ReFS do Dev Drive.

## 8. Interface, Telemetria e Hermes Web Dashboard

O Souls Engine adota o paradigma estrito de **serviço de retaguarda 100% headless**. Não há janelas nativas, ícones na barra de notificação do Windows (Systray), menus Win32 ou servidores de interface Svelte embutidos. Toda a observabilidade e governança são consumidas diretamente pelo painel web oficial do Hermes Agent (`hermes dashboard`).

### 8.1 Arquitetura de Extensão do Painel Web do Hermes

A integração com o painel oficial reside na pasta de extensões `$HERMES_HOME/plugins/souls_dashboard/`, estruturada nos seguintes componentes canônicos:

- `plugin.yaml`: Manifesto declarativo do plugin consumido pelo gerenciador de extensões do Hermes.
- `dashboard/manifest.json`: Manifesto visual declarando a aba `/souls` na navegação do painel, contendo identificador, título e ícone vetorial.
- `dashboard/plugin_api.py`: Roteador FastAPI montado automaticamente pelo servidor web do Hermes sob o caminho `/api/plugins/souls_dashboard/`. Atua como proxy reverso local, consultando o endpoint de telemetria do binário Rust (`http://127.0.0.1:9123/api/v1/telemetry`) e expondo os dados estruturados para a interface.
- `dashboard/dist/index.js`: Pacote compilado em JavaScript puro (formato IIFE), sem dependências redundantes. O pacote registra a interface através da chamada `window.__HERMES_PLUGINS__.register('souls-dashboard', SoulsDashboardComponent)` e utiliza os primitivos de interface compartilhados disponibilizados no escopo global em `window.__HERMES_PLUGIN_SDK__`.

### 8.2 Métricas de Telemetria Expostas em Tempo Real

A aba administrativa do Souls Engine expõe quatro blocos analíticos em tempo real:

1. **Governança Térmica e de Hardware:** Gráficos contínuos de ocupação de VRAM segregada (reserva do WDDM 3.x vs alocação CUDA do Tier 1), temperatura da GPU RTX 2060m em graus Celsius e estado operacional do Watchdog NVML.
2. **Eficiência do ParetoBandit:** Histórico de seleção de Tiers por categoria de tarefa, taxas de convergência das distribuições Beta e economia financeira acumulada expressa pela métrica $E^3$.
3. **Métricas de Infraestrutura MCP:** Volume total de requisições JSON-RPC processadas, distribuição percentilar de latência ($p50$, $p95$, $p99$) no parsing sintático Tree-sitter e taxas de acerto em cache de outlines.
4. **Saúde da Persistência:** Tamanho em disco do banco relacional, número de páginas pendentes no arquivo `.wal`, volume total de vetores indexados no LanceDB e nós ativos no grafo ontológico LadybugDB.

## 9. Resiliência de Sistemas, Resposta Sintática (Healing) e Graceful Shutdown

A estabilidade de um sistema híbrido combinando Rust bare-metal e Python sob o kernel Windows NT assenta em barreiras de contenção de falhas e encerramentos determinísticos.

### 9.1 Contenção de Falhas no Runtime Tokio

Para assegurar disponibilidade ininterrupta do daemon:

- **Barreiras de Pânico Assíncronas:** Todas as tarefas concorrentes despachadas dentro do Tokio Runtime são encapsuladas por primitivos `std::panic::catch_unwind(AssertUnwindSafe(...))`. Caso uma rotina de parsing sintático Tree-sitter sofra terminação anômala em função de código-fonte estruturalmente corrompido, o pânico é contido no escopo daquela requisição sem derrubar o daemon.
- **Padronização de Erros JSON-RPC:** Em caso de exceção de parsing ou violação de parâmetros, o servidor devolve mensagens estruturadas no padrão JSON-RPC (códigos de erro `-32000` a `-32099`), permitindo que o Hermes processe o erro com semântica de domínio sem quebrar o transporte HTTP/SSE.
- **Recuperação a Quente com `/reload-mcp`:** Se o processo `souls_server.exe` for reiniciado externamente, a conexão SSE de loopback é restabelecida na iteração subsequente do agente. A execução do comando `/reload-mcp` reconstrói o catálogo de ferramentas sem necessidade de reinicializar a sessão do Hermes.

### 9.2 Response Healing (Recuperação Sintática em Voo)

Modelos locais menores (Tier 1 / SLM) e ferramentas de análise sintática podem ocasionalmente gerar saídas truncadas por estouro de janela ou com pequenos defeitos estruturais (vírgulas sobressalentes, chaves não fechadas).

Antes de enviar qualquer resposta de ferramenta local ou saída de inferência do Tier 1 ao Hermes Agent, a esteira de saída da crate `souls_inference_runtime` submete o payload ao pipeline de **Response Healing**:

1. **Remoção de Blocos Markdown:** Limpeza de delimitadores residuais `json ...` .
2. **Balanceamento de Delimitadores:** Algoritmo determinístico baseado em pilha que analisa o fluxo de caracteres, identifica chaves `{`, `}` e colchetes `[`, `]` desbalanceados e injeta os caracteres de fechamento apropriados no final da string truncada.
3. **Expurgo de Trailing Commas:** Remoção de vírgulas espúrias precedendo delimitadores de fechamento (ex: `{"chave": "valor",}`).
4. **Validação Estrutural:** Validação final via `serde_json`. Se a cura for bem-sucedida, o JSON sanitizado é entregue ao Hermes; caso contrário, devolve-se um erro semântico amigável, impedindo falhas críticas no orquestrador Python.

### 9.3 Protocolo de Graceful Shutdown Ordenado no Windows NT

O encerramento do daemon `souls_server.exe` deve respeitar uma rotina rigorosa de teardown ao receber sinais do sistema operacional (`CTRL_C_EVENT`, `CTRL_SHUTDOWN_EVENT` ou terminação por sinal):

1. **Fase 1: Fechamento dos Listeners de Rede:** O servidor Axum encerra a recepção de novas requisições HTTP na porta `127.0.0.1:9123`, aguardando a finalização das tarefas ativas (drenagem com timeout de 3 segundos).
2. **Fase 2: Drenagem do Pipeline MPSC:** O canal de auditoria e telemetria é fechado (`rx.close()`). O loop em background descarrega todos os eventos remanescentes do buffer para o FrankenSQLite.
3. **Fase 3: Flush e Checkpoint do SQLite WAL:** O pool de banco de dados executa a instrução `PRAGMA wal_checkpoint(TRUNCATE);`, gravando todas as páginas do arquivo `.wal` de volta no arquivo principal `souls_state.db` e zerando os ponteiros de memória compartilhada `.shm`.
4. **Fase 4: Liberação de Ponteiros `mmap` do LanceDB:** Todas as referências mapeadas em memória dos arquivos colunares Arrow são explicitamente desfeitas (`Drop`), garantindo a liberação dos descritores no kernel NT e evitando o erro `ERROR_USER_MAPPED_FILE` em reinicializações subsequentes.
5. **Fase 5: Desalojamento CUDA e Reset da GPU:** O runtime de inferência descarrega os pesos do Tier 1, libera os buffers de KV Cache via `cudaFree` e invoca `cudaDeviceReset()`, devolvendo a VRAM integral ao controle exclusivo do Desktop Window Manager (DWM).

## 10. A Topologia Definitiva das 9 Crates Rust e Roadmap de Fases

O workspace do Souls Engine adota uma divisão modular rigorosa em 9 crates canônicas, eliminando dependências circulares e assegurando compilação estática de alto rendimento.

### 10.1 Dicionário Estrutural das 9 Crates Canônicas

1. `souls_protocol`: Biblioteca pura contendo contratos DTO, envelopes JSON-RPC 2.0, esquemas MCP, definições de ferramentas e tipos Arrow colunares. Dependência comum de todas as demais crates.
2. `souls_core`: Núcleo de serviços bare-metal. Inicialização do Tokio Runtime, configuração de tracing estruturado, barreiras de pânico `catch_unwind`, canais MPSC desidratados e abstrações de I/O em Dev Drive ReFS.
3. `souls_ast`: Motor de inteligência estática de código. Integra gramáticas nativas Tree-sitter (Rust, Python, TypeScript, Go), o extrator de outlines desidratados, fatiador de escopo (`slice`), algoritmo de Myers Diff e análise de Frecency de repositórios via `gitoxide` (`gix`).
4. `souls_memory`: HIPOCAMPO L3. Gerenciamento do FrankenSQLite em modo STRICT WAL, integração vetorial com LanceDB, grafo ontológico de dependências LadybugDB (busca BFS) e o Chyros Daemon (decaimento de Langevin e `VACUUM INTO`).
5. `souls_inference_runtime`: Governança de silício local e motores bare-metal. Drivers para ONNX Runtime (Tier 0 CPU AVX2), llama.cpp para logit probing (Tier 0.5 CPU) e geração acelerada CUDA com FlashAttention-2 (Tier 1 dGPU), leitor GGUF zero-copy em $\mathcal{O}(1)$ via `memmap2`, pipeline de Response Healing e sensor NVML.
6. `souls_model_router`: Motor de inteligência FinOps. Implementa o algoritmo bayesiano multiobjetivo ParetoBandit, cálculo da Métrica $E^3$ e orquestração de despacho para subagentes (`delegate_task`) e slots auxiliares (`auxiliary.*`).
7. `souls_llm_local_arena`: Suíte de benchmarking empírico e calibração contínua do hardware local (Intel i9 + RTX 2060m). Executa baterias de estresse para aferir tokens por segundo reais, precisão sintática de quantizações GGUF e teste de quebra de gramática via `llguidance`.
8. `souls_anthropophagy`: Esteira de engenharia de software automatizada. Módulos de dissecação sintática de repositórios open-source, firewall de licenças (SPDX Checker), traçador de call-graphs e extrator de algoritmos puros para o catálogo persistido no SQLite.
9. `souls_server`: Binário executável daemon headless (`souls_server.exe`). Monta a aplicação sobre a crate `Axum` na porta `127.0.0.1:9123`, hospedando as rotas MCP HTTP/SSE, os endpoints REST de memória e telemetria, e a gestão unificada de Graceful Shutdown.

### 10.2 Árvore de Diretórios do Workspace Canônico

```
Z:\souls_engine\
├── Cargo.toml                          # Workspace root definindo as 9 crates
├── Cargo.lock
├── .souls_data\                        # Diretório isolado no Dev Drive ReFS
│   ├── db\                             # FrankenSQLite STRICT WAL (souls_state.db)
│   ├── vectors\                        # LanceDB Arrow columnar store
│   ├── models\                         # Modelos GGUF e ONNX locais
│   └── spool\                          # Telemetria e logs transitórios
│
├── crates\
│   ├── souls_protocol\
│   │   ├── Cargo.toml
│   │   └── src\ (dto.rs, mcp.rs, lean.rs, lib.rs)
│   ├── souls_core\
│   │   ├── Cargo.toml
│   │   └── src\ (config.rs, logging.rs, mpsc_flusher.rs, panic.rs, lib.rs)
│   ├── souls_ast\
│   │   ├── Cargo.toml
│   │   └── src\ (treesitter.rs, outline.rs, myers.rs, heatmap.rs, lib.rs)
│   ├── souls_memory\
│   │   ├── Cargo.toml
│   │   └── src\ (sqlite.rs, lancedb.rs, ladybug.rs, chyros.rs, rrf.rs, lib.rs)
│   ├── souls_inference_runtime\
│   │   ├── Cargo.toml
│   │   └── src\ (onnx.rs, llamacpp.rs, gguf_mmap.rs, nvml.rs, healing.rs, lib.rs)
│   ├── souls_model_router\
│   │   ├── Cargo.toml
│   │   └── src\ (bandit.rs, finops.rs, context.rs, lib.rs)
│   ├── souls_llm_local_arena\
│   │   ├── Cargo.toml
│   │   └── src\ (benchmarks.rs, harness.rs, rigidity.rs, lib.rs)
│   ├── souls_anthropophagy\
│   │   ├── Cargo.toml
│   │   └── src\ (spdx.rs, dissector.rs, slicer.rs, vault.rs, lib.rs)
│   └── souls_server\
│       ├── Cargo.toml
│       └── src\ (main.rs, routes_mcp.rs, routes_api.rs, shutdown.rs)
│
└── adapters\
    ├── hermes_memory_souls\            # Thin Adapter Python para $HERMES_HOME/plugins/memory/
    │   ├── __init__.py                 # Implementação de MemoryProvider com API v2
    │   └── plugin.yaml
    └── hermes_dashboard_souls\         # Plugin oficial para $HERMES_HOME/plugins/souls_dashboard/
        ├── plugin.yaml
        └── dashboard\
            ├── manifest.json
            ├── plugin_api.py           # Proxy reverso FastAPI
            └── dist\
                └── index.js            # Componente IIFE React (window.__HERMES_PLUGIN_SDK__)
```

### 10.3 Cronograma de Implementação de Engenharia

O plano de implantação é estruturado em quatro fases sequenciais de engenharia:

#### Fase 1: Fundação Bare-Metal e Infraestrutura IPC (Semanas 1–2)

- Inicialização do workspace Rust no Dev Drive ReFS (`Z:\souls_engine\.souls_data\`).
- Implementação das crates `souls_protocol`, `souls_core` e o binário `souls_server`.
- Configuração do servidor HTTP/SSE Axum em `127.0.0.1:9123` e rotinas de Graceful Shutdown Win32.
- Homologação do pipeline assíncrono MPSC de proteção ao NVMe com descargas em lotes de 5s.

#### Fase 2: Inteligência de AST e Ferramentas MCP (Semanas 3–4)

- Construção da crate `souls_ast` com gramáticas Tree-sitter compiladas nativamente.
- Implementação dos métodos `souls_ast_outline`, `souls_ast_slice`, `souls_myers_diff` e `souls_repo_heatmap` (gitoxide).
- Exposição do catálogo MCP na rota `/mcp` e validação com o comando `/reload-mcp` no Hermes Agent.

#### Fase 3: Persistência Híbrida L3 e Adaptador Hermes (Semanas 5–6)

- Implementação da crate `souls_memory`: FrankenSQLite com DDL STRICT e pragmas WAL, LanceDB vetorial, grafo LadybugDB e Chyros Daemon com Langevin Decay.
- Implementação do algoritmo RRF unificando busca BM25 e cosseno.
- Implantação do Thin Adapter Python `$HERMES_HOME/plugins/memory/souls/` com validação de checkpointing na API v2 de `on_pre_compress`.

#### Fase 4: Inferência Local, ParetoBandit e Dashboard (Semanas 7–8)

- Desenvolvimento de `souls_inference_runtime` com leitor GGUF zero-copy via `memmap2`, monitor NVML, esteira de Response Healing e aceleração CUDA para Tier 1.
- Construção de `souls_model_router` implementando o ParetoBandit com barreira termodinâmica $\Phi$.
- Implantação da extensão de painel `$HERMES_HOME/plugins/souls_dashboard/` com integração React no `window.__HERMES_PLUGIN_SDK__`.

## 11. Portões de Qualidade e Critérios de Homologação (Definition of Done - DoD)

A homologação do Souls Engine v7 é condicionada à aprovação estrita na seguinte suíte de testes de integração e conformidade de engenharia:

### 1. `test_sqlite_wal_strict_concurrency`

- **Procedimento:** Dispara 20 threads concorrentes executando escritas massivas de telemetria e leituras contínuas simuladas por 15 segundos sobre o FrankenSQLite.
- **Critério de Aprovação:** Zero ocorrências de `SQLITE_BUSY`, zero violações de tipo em tabelas `STRICT` e integridade total dos arquivos `-wal` e `-shm`.

### 2. `test_mmap_zero_vram_lance_db_isolation`

- **Procedimento:** Executa indexação vetorial de 50.000 nós no LanceDB no Dev Drive e realiza 1.000 buscas semânticas em paralelo.
- **Critério de Aprovação:** Aumento de alocação de VRAM dedicada da GPU RTX 2060m rigorosamente igual a $0\text{ MB}$, comprovado via chamadas da biblioteca NVML.

### 3. `test_mpsc_batch_flushing_integrity`

- **Procedimento:** Emite 5.000 eventos de telemetria no canal `tokio::sync::mpsc` em menos de 1 segundo e força a drenagem do buffer.
- **Critério de Aprovação:** O daemon consolida as gravações em transações agregadas a cada 5 segundos, sem perda de eventos e com contenção total de IOPS físicos no NVMe.

### 4. `test_pareto_bandit_subagent_routing_boundary`

- **Procedimento:** Submete uma bateria de 100 subtarefas artificiais (desde outlines triviais até refatorações arquiteturais densas) ao método `souls_route_subtask`.
- **Critério de Aprovação:** O algoritmo direciona tarefas simples para Tiers locais (0, 0.5, 1) com custo zero e tarefas reflexivas complexas para Tiers 3 e 4, penalizando braços de GPU caso a temperatura simulada atinja $\ge 82^\circ\text{C}$.

### 5. `test_gguf_zero_copy_metadata_parser`

- **Procedimento:** Instancia o `GgufMetadataViewer` sobre um arquivo de modelo de 15 GB quantizado em disco.
- **Critério de Aprovação:** Extração integral de metadados, arquitetura, contagem de tensores e tamanho de contexto em complexidade $\mathcal{O}(1)$ em tempo inferior a 5 ms, consumindo menos de 500 KB de memória RAM de heap.

### 6. `test_response_healing_malformed_json`

- **Procedimento:** Submete 50 payloads JSON sintaticamente defeituosos (chaves abertas sem fechamento, vírgulas sobressalentes, quebras de linha em strings) gerados por simulação de SLM ao pipeline de cura.
- **Critério de Aprovação:** Restauração bem-sucedida de 100% dos JSONs para estruturas válidas e parseáveis por `serde_json`, sem alucinação de valores.

### 7. `test_ladybug_bfs_ontic_barrier`

- **Procedimento:** Registra uma decisão arquitetural imutável na partição `STABLE` e simula uma recuperação vetorial via LanceDB contendo uma diretriz conflitante.
- **Critério de Aprovação:** A travessia BFS do LadybugDB intercepta e descarta a memória conflitante antes da entrega da resposta, impedindo a contaminação do contexto do agente.

### 8. `test_graceful_shutdown_orderly_teardown`

- **Procedimento:** Dispara o daemon em carga máxima de I/O e inferência e emite um sinal de encerramento do sistema operacional (`CTRL_C_EVENT`).
- **Critério de Aprovação:** Conclusão ordenada das 5 fases de shutdown: encerramento de conexões Axum, esvaziamento do MPSC, checkpoint do WAL, unmap dos arquivos colunares Arrow e desalojamento CUDA via `cudaDeviceReset` em menos de 4 segundos, sem deixar descritores órfãos ou arquivos corrompidos no Dev Drive ReFS.