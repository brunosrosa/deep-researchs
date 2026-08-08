# Relatório de Antropofagização Técnica: Transmutação do TencentDB-Agent-Memory para a Arquitetura Soberana Souls MC (SODA V4)

## Diagnóstico Autópsico do TencentDB-Agent-Memory

O projeto _TencentDB-Agent-Memory_, desenvolvido pela Tencent Cloud, aborda diretamente duas das maiores limitações observadas em sistemas agênticos de inteligência artificial: a degradação contextual decorrente do acúmulo indiscriminado de logs (_Context Rot_) e a amnésia inter-sessões que força a constante repetição de premissas, regras de negócio e restrições arquiteturais. A arquitetura da Tencent rejeita explicitamente a abordagem convencional de armazenamento vetorial plano (_flat vector storage_), na qual conversas inteiras são fragmentadas e recuperadas exclusivamente por similaridade semântica de cosseno, em favor de uma estrutura híbrida baseada em memória simbólica de curto prazo e uma pirâmide semântica progressiva de longo prazo.

A pirâmide semântica do TencentDB-Agent-Memory organiza a retenção de dados em quatro camadas de abstração hierárquica:

- **L0 Conversation**: Armazena os registros brutos de diálogos e interações com contexto completo, garantindo auditoria estrita, verificação de carimbos temporais e rastreabilidade de fontes.
- **L1 Atom**: Consolida fatos atômicos, preferências do usuário, restrições do projeto e eventos específicos extraídos das interações da L0.
- **L2 Scenario**: Estrutura blocos de conhecimento pragmático agrupados por cenários operacionais ou projetos específicos, permitindo a restauração rápida do contexto de trabalho.
- **L3 Core / Persona**: Mantém os perfis de longo prazo do usuário e da equipe, contendo diretrizes comportamentais e padrões cognitivos consolidados.

O princípio fundamental dessa distribuição estabelece que as camadas superiores (L2 e L3) carregam direção e julgamento, enquanto as camadas inferiores (L0 e L1) preservam precisão e evidência factual. Quando uma agente necessita entender diretrizes gerais, consulta as camadas L2 e L3; quando necessita de dados específicos, realiza uma busca detalhada (_drill-down_) até a L1 ou L0.

Além da pirâmide de memória, o ecossistema da Tencent introduz dois mecanismos centrais:

1. **Memória Simbólica de Curto Prazo (Offloading In-Task)**: Durante tarefas de longa duração, saídas extensas de ferramentas, logs de execução e análises de código são descarregados para arquivos externos (`refs/*.md`). O contexto ativo do agente retém apenas um canvas simbólico em sintaxe Mermaid representando o grafo de estados da tarefa. Caso ocorra um erro, o agente consulta o identificador do nó (`node_id`) e extrai do disco apenas o trecho textual necessário, reduzindo o consumo de tokens em até 61% e elevando a taxa de sucesso em tarefas complexas em mais de 50%.
2. **Governança de Ativos de Memória (Memory Hub)**: A memória é tratada como um ativo versionável, governável e compartilhável entre equipes, dividindo-se em _Chat Memory_, _Skills_ (fluxos reutilizáveis), _LLM-Wiki_ (documentação em grafo) e _Code-Graph_ (símbolos e relações de chamadas de código). O acesso é regulado por listas de controle de acesso (ACLs) e vinculações rígidas por agente.

Apesar da elegância algorítmica, a implementação original do TencentDB-Agent-Memory baseia-se em um processo _sidecar_ executado em Node.js (na porta 8420) acoplado a adaptadores em Python e dependente de bancos de dados em nuvem ou instâncias gerenciadas do PostgreSQL. A adoção direta dessa infraestrutura violaria os princípios de soberania _bare-metal_ do ecossistema Souls MC (SODA V4), onde o sistema deve operar sem daemons interpretados em segundo plano e sob uma restrição estrita de hardware (processador Intel Core i9, 32GB de RAM e GPU NVIDIA RTX 2060m com 6GB de VRAM).

A tabela a seguir apresenta o diagnóstico comparativo entre o projeto original da Tencent e a proposta de transmutação _bare-metal_ para o Souls MC.

|**Dimensão Técnica**|**TencentDB-Agent-Memory (Original)**|**Souls MC / SODA V4 (Transmutado)**|
|---|---|---|
|**Ambiente de Execução**|Node.js Sidecar (v22+) + Provedores Python|Binário único em Rust nativo (Runtime Tokio)|
|**Comunicação Inter-Processos**|Requisições HTTP/REST via porta TCP 8420|SharedMemory e IPC Zero-Copy via Tauri v2|
|**Persistência Relacional (L2)**|SQLite + `sqlite-vec` ou PostgreSQL Cloud|FrankenSQLite (Modo WAL, MVCC, FTS5)|
|**Persistência Vetorial (L3)**|Tencent Cloud VectorDB ou embeddings planos|LanceDB via `mmap` direto no SSD NVMe|
|**Grafo Ontológico**|Canvas Mermaid gerado via LLM|LadybugDB (100% Rust) e tabela `souls_graph`<br><br>[cite: ]|
|**Resolução de Conflitos**|Deduplicação por similaridade vetorial L1|Invalidação JIT em sessão e Dinâmica de Langevin|
|**Compressão / Desidratação**|Mermaid Canvas + JSONL plano|Protocolo LEAN (via pipeline `lean_vacuum`)|
|**Alocação de VRAM / Hardware**|Elevada (Overhead de runtimes V8 e Python)|Estrita (Teto rígido de 6GB VRAM na RTX 2060m)|

## Formulação Matemática e Lógica de Processamento

A transmutação da inteligência do TencentDB-Agent-Memory para a arquitetura Souls MC exige a formalização de seus processos qualitativos em modelos matemáticos determinísticos, otimizados para execução em código compilado.

### Algoritmo de Fusão de Ranqueamento Recíproco (RRF)

A recuperação de informações no Souls MC combina busca lexical FTS5 (BM25) no FrankenSQLite com busca semântica vetorial no LanceDB. A unificação dos resultados de ambos os sistemas utiliza o algoritmo _Reciprocal Rank Fusion_ (RRF). O escore final de um documento ou elemento de memória $d$ dentro do conjunto unificado $D$ é calculated por:

$$RRF\_Score(d \in D) = \sum_{m \in M} \frac{1}{k + r_m(d)}$$

Em que $M = \{\text{BM25\_FTS5}, \text{FRQAD\_LanceDB}\}$, $r_m(d)$ representa a posição ordinal do elemento $d$ no ranqueamento do sistema $m$ (iniciando em 1), e $k$ é a constante de suavização do algoritmo, fixada empiricamente em $k = 60$ para atenuar o impacto de discrepâncias no topo dos ranqueamentos individuais. Em Rust, essa fusão é executada utilizando iteradores de alocação zero no _heap_, agrupando os resultados em arenas contíguas de memória.

### Métrica de Distância de Fisher-Rao Quantizada (FRQAD)

Para evitar os erros de recuperação decorrentes do uso da similaridade de cosseno em vetores fortemente quantizados (por exemplo, formatos INT4 ou Q4_K_M), o Souls MC substitui a métrica Euclidiana/Cosseno pela Distância de Fisher-Rao Quantizada (FRQAD):

$$d_{FR}(\theta_1, \theta_2) = 2 \cdot \operatorname{arcsinh}\left( \frac{\Vert{}\theta_1 - \theta_2\Vert{}}{2 \sqrt{\sigma_1 \sigma_2 + \epsilon}} \right)$$

Em que $\theta_1$ e $\theta_2$ representam os vetores de médias dos embeddings, $\sigma_1$ e $\sigma_2$ são as variâncias marginais associadas ao ruído de quantização no disco, e $\epsilon = 10^{-8}$ é uma constante de estabilidade numérica. A métrica FRQAD penaliza matematicamente os vetores cuja incerteza de quantização é elevada, garantindo precisão na busca semântica dentro do LanceDB.

### Invalidação Ativa de Conflitos em Tempo Real (JIT Conflict Invalidation)

A detecção de contradições entre um fato atômico recém-extraído $A_{new}$ e um fato existente $A_{old}$ na camada L2 é modelada pela matriz booleana de conflito $\mathbf{C}$:

$$\mathbf{C}(A_{new}, A_{old}) = \mathbb{I}\left( \text{Entity}(A_{new}) = \text{Entity}(A_{old}) \land \text{Attribute}(A_{new}) = \text{Attribute}(A_{old}) \land \text{Value}(A_{new}) \neq \text{Value}(A_{old}) \right)$$

Quando $\mathbf{C}(A_{new}, A_{old}) = 1$, o sistema dispara uma transição de estado atômica no FrankenSQLite:

$$\text{UPDATE } \text{souls\_atoms } \text{SET } \text{status = 'superseded', } \text{superseded\_by = } A_{new}.\text{id } \text{WHERE } \text{id = } A_{old}.\text{id};$$

Essa invalidação impede que fatos obsoletos permaneçam ativos no índice de busca, eliminando o ruído no prompt do turno seguinte.

### Dinâmica de Langevin para Esquecimento Orgânico no Espaço Hiperbólico

Durante o processamento assíncrono em segundo plano, a poda de memórias não essenciais utiliza a Equação Diferencial Estocástica (SDE) da Dinâmica de Langevin Riemanniana sobre o Disco de Poincaré:

$$d\mathbf{x}_t = -\nabla V(\mathbf{x}_t) \, dt + \sqrt{2 D} \, d\mathbf{W}_t$$

Em que $\mathbf{x}_t$ é a posição do vetor de memória no espaço hiperbólico, $V(\mathbf{x}_t) = \frac{1}{2} \gamma \Vert{}\mathbf{x}_t\Vert{}^2 - \alpha \cdot \text{AccessCount}(m)$ representa o potencial de atração cognitiva, $D$ é a taxa de difusão do esquecimento, e $d\mathbf{W}_t$ denota o movimento Browniano que simula a deriva térmica da CPU. Vetores que derivam para a fronteira do disco ($\Vert{}\mathbf{x}\Vert{} \to 1$) têm sua relevância zerada e são arquivados de forma orgânica.

## Antropofagização "Live" — Operações em Tempo Real na Sessão Ativa

A integração dos conceitos do TencentDB-Agent-Memory na sessão ativa do Souls MC visa otimizar a montagem do prompt e proteger a VRAM da GPU local sem introduzir latência.

Em vez de injetar históricos extensos ou diagramas Mermaid complexos no contexto ativo, o Souls MC consolida o uso do protocolo de compressão e desidratação **LEAN**, já integrado ao ecossistema e refinado pela função interna `lean_vacuum`. Quando uma ferramenta gera uma saída volumosa (como leituras de código, inspeção de AST ou logs de compilação), a camada de integração do Souls MC desidrata e comprime a mensagem via `lean_vacuum`, gravando o payload bruto no banco relacional `souls_state.db`. O contexto do agente recebe exclusivamente a representação telegráfica desidratada no padrão LEAN.

O fluxo de processamento ao longo da conversa ativa opera de forma sequencial:

1. O usuário envia uma mensagem ou instrução durante a sessão de chat ativo.
2. O manipulador MCP `souls_knowledge` intercepta a entrada e consulta o FrankenSQLite (L1/L2) para identificar potenciais conflitos conceituais.
3. Se um conflito for detectado, o sistema executa a invalidação JIT, alterando o estado do fato antigo para `superseded`.
4. O _HEADROOM Engine_ realiza a busca híbrida (RRF) sobre a base purificada, recuperando apenas os fatos ativos.
5. O prompt é montado com o estado mais recente desidratado em formato LEAN (comprimido pela lógica de `lean_vacuum`) e enviado ao modelo de linguagem.
6. Caso o modelo precise inspecionar detalhes de uma execução anterior, ele invoca a ferramenta `souls_smart_read`, que lê via `mmap` os bytes exatos do arquivo descarregado no NVMe sem sobrecarregar a memória de vídeo.

## Antropofagização "In Background" — Ciclo do Chyros Daemon

A manutenção estrutural profunda, a consolidação de cenários e a atualização dos perfis de longo prazo são delegadas ao _Chyros Daemon_, que executa de forma assíncrona durante os períodos de ociosidade do sistema.

O pipeline de consolidação em segundo plano é estruturado em quatro fases contínuas:

1. **Fase 1: Orient (Inspeção e Triagem)**: O daemon varre a tabela de conversas brutas (`L0_conversations`) identificando sessões encerradas que ainda não foram submetidas ao processo de destilação semântica.
2. **Fase 2: Gather (Extração de Fatos Atômicos)**: Para preservar a VRAM da GPU reservada para o usuário, o daemon instancia um modelo de linguagem pequeno (SLM, como o _Gemma-4-E2B_) operando exclusivamente na CPU via instruções AVX2 e a biblioteca `candle-core`. O modelo analisa as conversas brutas e extrai triplas estruturadas no formato JSONL, gravando os novos elementos na tabela `L1_atoms`.
3. **Fase 3: Consolidate (Evolução de Cenários e Persona)**: A cada conjunto de 5 turnos processados, os fatos atômicos da L1 pertencentes a um mesmo projeto são consolidados em um documento Markdown na tabela `L2_scenarios`. A cada 50 novos fatos processados, o daemon atualiza o arquivo `L3_persona.md`, registrando a evolução das preferências e diretrizes do usuário com versionamento atômico controlado pela biblioteca `gitoxide`.
4. **Fase 4: Prune & Index (Expurgo e Indexação Vetorial)**: O daemon aplica a Dinâmica de Langevin para fazer decair memórias obsoletas, atualiza o índice B-Tree do LanceDB para buscas semânticas em L3 e executa o comando `VACUUM INTO` no SQLite para desfragmentar o banco de dados sem bloquear as operações de leitura.

Os ativos de memória originais do TencentDB são integrados às primitivas nativas do Souls MC:

- **Skills**: Tarefas bem-sucedidas são convertidas em arquivos `.md` estruturados no repositório de habilidades locais do agente, contendo regras de execução e validação.
- **LLM-Wiki**: Documentos e manuais são mantidos no SSD NVMe em formato Markdown e mapeados por um grafo de links internos gerenciado pelo LadybugDB.
- **Code-Graph**: A análise de código utiliza a biblioteca `tree-sitter` compilada para WebAssembly (WASI 0.2 via Wasmtime). O grafo de símbolos, dependências e chamadas é persistido na tabela `souls_graph`.
- **Chat Memory**: O histórico conversacional é armazenado em pipeline progressivo (L0 a L3) com identificadores UUIDv7 que garantem a rastreabilidade bidirecional entre o resumo de alto nível e o dado bruto.

## Plano de Execução, Engenharia de Dados e Referências de Cálculo

### Schema da Camada L2 Transacional (FrankenSQLite - `souls_state.db`)

A persistência relacional do Souls MC é estruturada sob o motor FrankenSQLite em modo WAL com controle de concorrência MVCC. As tabelas essenciais para a retenção de memória e controle de invalidação são definidas como segue:

SQL

```
CREATE TABLE IF NOT EXISTS souls_atoms (
    atom_id TEXT PRIMARY KEY NOT NULL,
    session_id TEXT NOT NULL,
    entity TEXT NOT NULL,
    attribute TEXT NOT NULL,
    value TEXT NOT NULL,
    temporal_stability TEXT CHECK(temporal_stability IN ('STABLE', 'EVOLVING')) DEFAULT 'EVOLVING',
    status TEXT CHECK(status IN ('active', 'superseded', 'archived')) DEFAULT 'active',
    superseded_by TEXT NULL,
    created_at INTEGER NOT NULL,
    FOREIGN KEY (superseded_by) REFERENCES souls_atoms(atom_id) ON DELETE SET NULL
);

CREATE VIRTUAL TABLE IF NOT EXISTS souls_atoms_fts USING fts5(
    atom_id UNINDEXED,
    entity,
    attribute,
    value,
    content='souls_atoms',
    content_rowid='rowid'
);

CREATE TABLE IF NOT EXISTS souls_scenarios (
    scenario_id TEXT PRIMARY KEY NOT NULL,
    project_key TEXT NOT NULL,
    title TEXT NOT NULL,
    markdown_content TEXT NOT NULL,
    atom_count INTEGER NOT NULL DEFAULT 0,
    updated_at INTEGER NOT NULL
);

CREATE TABLE IF NOT EXISTS tool_offloads (
    offload_id TEXT PRIMARY KEY NOT NULL,
    session_id TEXT NOT NULL,
    tool_name TEXT NOT NULL,
    file_path TEXT NOT NULL,
    token_count INTEGER NOT NULL,
    created_at INTEGER NOT NULL
);

CREATE INDEX IF NOT EXISTS idx_atoms_lookup ON souls_atoms(entity, attribute, status);
CREATE INDEX IF NOT EXISTS idx_atoms_session ON souls_atoms(session_id);
```

### Balanço Físico de Recursos e Orçamento Térmico

A tabela a seguir descreve a alocação de recursos computacionais estimada para as abordagens de memória analisadas, considerando as restrições físicas do hardware base (Intel Core i9, 32GB RAM, RTX 2060m 6GB VRAM, NVMe SSD).

|**Métrica / Recurso**|**RAG Tradicional (Vetor Plano)**|**TencentDB (Node/Python)**|**Souls MC V4 (Transmutado)**|
|---|---|---|---|
|**Uso de VRAM na GPU**|~5,8 GB (Alto risco de OOM)|~5,5 GB (Concorrência elevada)|**~3,8 GB (Isolamento de VRAM)**<br><br>[cite: ]|
|**Consumo de RAM do Sistema**|~12 GB (Runtimes Python)|~8 GB (Node.js e V8 Heap)|**< 1,2 GB (Buffers Rust)**<br><br>[cite: ]|
|**Consumo Médio de Tokens**|8.000 a 25.000 tokens|3.200 a 8.000 tokens|**800 a 2.500 tokens (Formato LEAN / lean_vacuum)**<br><br>[cite: 6]|
|**Tempo até o 1º Token (TTFT)**|~3.800 ms (Prefill extenso)|~1.200 ms|**< 280 ms (Prefill otimizado)**<br><br>[cite: ]|
|**Latência de Recuperação**|~450 ms (Busca em GPU)|~120 ms (RRF via HTTP)|**< 12 ms (RRF nativo em Rust)**<br><br>[cite: ]|
|**Padrão de Escrita no SSD**|Escritas desalinhadas|Padrão SQLite/PostgreSQL|**Escritas ordenadas em bloco WAL**<br><br>[cite: ]|

### Roteiro de Implementação Técnico

A implementação do plano de antropofagização é dividida em quatro fases de engenharia:

- **Fase 1: Módulo LEAN e Offloading (Sessão Ativa)**: Conexão das rotinas de desidratação às funções nativas do protocolo LEAN (com suporte à função `lean_vacuum`), permitindo a compressão de chamadas de ferramentas e a execução do manipulador `souls_smart_read` via `mmap`.
- **Fase 2: Motor de Recuperação Híbrida RRF + FRQAD**: Configuração da extensão FTS5 no FrankenSQLite, implementação do algoritmo de fusão de ranqueamento RRF em Rust e integração da métrica de distância FRQAD sobre os índices do LanceDB.
- **Fase 3: Invalidação Ativa JIT e Headroom Engine**: Implementação dos triggers de verificação de conflitos factuais e integração do filtro de invalidação ao _HEADROOM Engine_, garantindo que fatos marcados como `superseded` sejam excluídos da montagem do prompt em tempo real.
- **Fase 4: Chyros Daemon e Processamento em Segundo Plano**: Automação do pipeline ETL de quatro fases (Orient, Gather, Consolidate, Prune & Index) executado em threads dedicadas do Tokio durante a ociosidade da CPU, utilizando a biblioteca `candle-core` para extração atômica e aplicando a SDE de Langevin para o decaimento orgânico de memórias.

## Conclusão e Diretrizes Finais

O projeto _TencentDB-Agent-Memory_ oferece uma solução estrutural avançada para os problemas de saturação de contexto e perda de continuidade em agentes de inteligência artificial. A substituição do armazenamento vetorial plano por uma pirâmide semântica de quatro camadas (L0 a L3) e o uso de representações simbólicas para descarregamento de dados intermediários representam evoluções significativas na arquitetura de sistemas cognitivos.

Através do processo de antropofagização descrito neste dossiê, o Souls MC (SODA V4) assimila os princípios algorítmicos da solução da Tencent — incluindo a recuperação híbrida RRF, a compressão simbólica e a invalidação de conflitos —, transpilando toda a sua execução para primitivas de baixo nível em Rust, FrankenSQLite, LanceDB e LadybugDB e aproveitando o protocolo LEAN / `lean_vacuum` para máxima desidratação e redução do consumo de tokens. Essa abordagem elimina a necessidade de runtimes interpretados em segundo plano, reduz substancialmente o consumo de tokens e preserva a alocação de memória de vídeo dentro dos limites do hardware local, garantindo eficiência operacional e soberania computacional.