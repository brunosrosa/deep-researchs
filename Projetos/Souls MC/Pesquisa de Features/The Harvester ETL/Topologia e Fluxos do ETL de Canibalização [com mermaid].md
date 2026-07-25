
# SODA_V3_Topologia_Daemon_Orquestrador_Master

```mermaid
graph TD
    classDef skill fill:#ff5722,stroke:#bf360c,stroke-width:2px,color:#fff;
    classDef script fill:#1e1e1e,stroke:#4CAF50,stroke-width:2px,color:#fff;
    classDef raw fill:#2d2d2d,stroke:#2196F3,stroke-width:2px,color:#fff;
    classDef ai fill:#3d1a40,stroke:#9C27B0,stroke-width:2px,color:#fff;
    classDef db fill:#1a365d,stroke:#03A9F4,stroke-width:2px,color:#fff;
    classDef danger fill:#b91c1c,stroke:#ef4444,stroke-width:2px,color:#fff;
    classDef safe fill:#047857,stroke:#34d399,stroke-width:2px,color:#fff;

    A(["Início: Daemon SODA (Rust/Tokio)"])
    class A script;

    subgraph FASE_0 ["FASE 0: BOOTSTRAPPING & CACHE EPISTÊMICO"]
        HW_CHECK["Auditoria de Hardware<br>(all-smi / pcics)"]
        CANON_LOAD["uvx notebooklm-mcp-cli<br>(Download do Cânone SODA)"]
        DB_CANON[("SQLite: canon_cache<br>(Validade: 30 dias)")]

        class HW_CHECK,CANON_LOAD script;
        class DB_CANON db;

        A --> HW_CHECK
        HW_CHECK --> CANON_LOAD
        CANON_LOAD --> DB_CANON
    end

    subgraph FASE_MINUS_1 ["FASE -1: GUARDIÃO DE DRIFT & ANTI-BAN"]
        RATE_LIMIT["Token Bucket Assíncrono<br>(Prevenção de Ban no GitHub API)"]
        DRIFT_CHK{"Hash ou Tag Remota Mudou?"}
        SKIP(["Curto-Circuito: Pula Repositório<br>(Economia FinOps 100%)"])
        INS_PEND[("Upsert SQLite:<br>Status = PENDENTE")]

        class RATE_LIMIT,DRIFT_CHK script;
        class SKIP skill;
        class INS_PEND db;

        DB_CANON --> RATE_LIMIT
        RATE_LIMIT --> DRIFT_CHK
        DRIFT_CHK -->|"Igual"| SKIP
        DRIFT_CHK -->|"Novo ou Drift Detectado"| INS_PEND
    end

    LOOP{"ORQUESTRADOR MPSC (Tokio)<br>Consumidor de Tarefas Assíncronas"}
    class LOOP script;

    INS_PEND --> LOOP

    subgraph FASE_1 ["FASE 1: HARVESTER POLIGLOTA & ISOLAMENTO NATIVO"]
        RAMDISK["Aloca Ramdisk 2GB<br>(Protege SSD NVMe contra TBW)"]
        SANDBOX["Windows Sandbox (.wsb)<br>Partição Segura (Read-Only / Zero Egress)"]
        CLONE["Git Clone Blobless<br>Direto no Ramdisk isolado"]
        LANG_DETECT{"Roteador Poliglota<br>Detecta Stack Base"}

        GENERIC_EXT["JCodemunch (AST) / Aider / Manifestos"]
        RUST_EXT["cargo clippy / cargo test"]
        JS_EXT["npx oxlint / semgrep"]

        PURGE_SANDBOX["Process Pool Guard (RAII):<br>SIGKILL Sandbox & Desmonta Ramdisk"]

        TOK_COUNT{"Tiktoken Pre-Flight<br>(Contagem Isolada por Lente Alvo)"}
        DISTILL["Fase 1.5: Destilação Semântica<br>(Gemini Flash-Lite / Qwen)"]
        NORMALIZED_DB[("SQLite: artefatos_brutos<br>(Tabela Relacional Normalizada por Lente)")]

        class RAMDISK,SANDBOX,CLONE,LANG_DETECT,TOK_COUNT script;
        class GENERIC_EXT,RUST_EXT,JS_EXT raw;
        class PURGE_SANDBOX safe;
        class DISTILL ai;
        class NORMALIZED_DB db;

        LOOP -->|"Puxa PENDENTE"| RAMDISK
        RAMDISK --> SANDBOX
        SANDBOX --> CLONE
        CLONE --> LANG_DETECT

        LANG_DETECT -->|"Agnóstico"| GENERIC_EXT
        LANG_DETECT -->|"Rust"| RUST_EXT
        LANG_DETECT -->|"JS/TS"| JS_EXT

        GENERIC_EXT --> PURGE_SANDBOX
        RUST_EXT --> PURGE_SANDBOX
        JS_EXT --> PURGE_SANDBOX

        PURGE_SANDBOX --> TOK_COUNT

        TOK_COUNT -->|"> Limite Seguro (Ex: 128k)"| DISTILL
        DISTILL --> NORMALIZED_DB
        TOK_COUNT -->|"< Limite Seguro"| NORMALIZED_DB
    end

    subgraph FASE_2 ["FASE 2: ENXAME DETERMINÍSTICO & DISJUNTOR FINOPS"]
        FINOPS_BREAKER{"Disjuntor de Micropagamentos<br>(iron_cost) Orçamento OK?"}
        ROUTER_F2["Query Semântica O(1)<br>(Carrega apenas a Lente necessária da RAM)"]

        L1["Lente A (Produto/UX)<br>Claude Opus 4.7"]
        L2["Lente B (Arquitetura)<br>DeepSeek Pro"]
        L3["Lente C (Ops/FinOps)<br>GLM-5.1"]

        FALLBACK_LOCAL["Fallback Local Worker<br>(RTX 2060m - Qwen 3B)<br>Custo Zero"]

        DEBATE_DB[("SQLite: debates_enxame<br>Status: FASE_2_OK")]

        class FINOPS_BREAKER,ROUTER_F2 script;
        class L1,L2,L3,FALLBACK_LOCAL ai;
        class DEBATE_DB db;

        NORMALIZED_DB -->|"Puxa FASE_1_OK"| FINOPS_BREAKER
        FINOPS_BREAKER -->|"Estouro de Orçamento"| FALLBACK_LOCAL
        FINOPS_BREAKER -->|"Saldo OK"| ROUTER_F2

        ROUTER_F2 -->|"Manifestos/UX"| L1
        ROUTER_F2 -->|"AST/Topologia"| L2
        ROUTER_F2 -->|"Logs/Segurança"| L3

        L1 --> DEBATE_DB
        L2 --> DEBATE_DB
        L3 --> DEBATE_DB
        FALLBACK_LOCAL --> DEBATE_DB
    end

    subgraph FASE_3 ["FASE 3: SINTETIZADOR BARE-METAL & RALPH LOOP"]
        LOCAL_SLM["Local Worker (RTX 2060m)<br>Qwen 3B ou Gemma 2B"]
        LLGUIDANCE["llguidance (Rust)<br>Decodificação Restrita (CFG em 50µs)"]
        RALPH_LOOP{"Ralph Loop (Resiliência)<br>Auditoria Lógica de Conteúdo"}

        HEURISTIC_DB[("SQLite: repo_heuristics<br>Status: CONCLUIDO")]
        ERRO_SINTESE[("Status: ERRO_SINTESE<br>(Falha Irrecuperável)")]

        class LOCAL_SLM ai;
        class LLGUIDANCE,RALPH_LOOP script;
        class HEURISTIC_DB db;
        class ERRO_SINTESE danger;

        DEBATE_DB -->|"Puxa FASE_2_OK"| LOCAL_SLM
        LOCAL_SLM -->|"Gera Outputs Controlados"| LLGUIDANCE
        LLGUIDANCE -->|"Força Máscara JSON O(1)"| RALPH_LOOP

        RALPH_LOOP -->|"Erro Lógico (< 3 Tentativas)<br>Limpa VRAM e Tenta Novamente"| LOCAL_SLM
        RALPH_LOOP -->|"Sucesso (JSON Lógico e Perfeito)"| HEURISTIC_DB
        RALPH_LOOP -->|"Falha após 3 Tentativas"| ERRO_SINTESE
    end

    subgraph FASE_4 ["FASE 4: SINCRONIA TRILATERAL (SSOT)"]
        BATCH_SHEETS["mcp-google-sheets (Batch Update)<br>Preenche a Janela de Vidro"]
        LADYBUG["LadybugDB (Grafo 100% Rust)<br>Conecta Relacionamentos da Ontologia SODA"]
        SYNC_OK[("Status Final: SYNC_OK")]

        class BATCH_SHEETS,LADYBUG script;
        class SYNC_OK db;

        HEURISTIC_DB -->|"Puxa CONCLUIDO"| BATCH_SHEETS
        HEURISTIC_DB -->|"Puxa CONCLUIDO"| LADYBUG
        BATCH_SHEETS --> SYNC_OK
        LADYBUG --> SYNC_OK
    end

    SYNC_OK -->|"Libera Thread p/ Próximo Job"| LOOP
    ERRO_SINTESE -->|"Libera Thread p/ Próximo Job"| LOOP
```

# SODA_ETL_V3_DeepDive_Fases_0_e_Minus1_Guardiao

```mermaid
graph TD
    classDef skill fill:#ff5722,stroke:#bf360c,stroke-width:2px,color:#fff;
    classDef script fill:#1e1e1e,stroke:#4CAF50,stroke-width:2px,color:#fff;
    classDef db fill:#1a365d,stroke:#03A9F4,stroke-width:2px,color:#fff;
    classDef danger fill:#b91c1c,stroke:#ef4444,stroke-width:2px,color:#fff;
    classDef safe fill:#047857,stroke:#34d399,stroke-width:2px,color:#fff;

    START(["Início: Daemon SODA (Rust/Tokio)"])
    class START script;

    subgraph FASE_0 ["FASE 0: BOOTSTRAPPING & CACHE EPISTÊMICO"]
        HW_CHECK["Auditoria de Hardware Bare-Metal<br>(all-smi / pcics)"]
        class HW_CHECK script;

        CHK_CACHE{"SQLite: canon_cache<br>Existe e validade &lt; 30 dias?"}
        class CHK_CACHE script;

        NLM_CALL["uvx notebooklm-mcp-cli<br>(Download do Cânone SODA)"]
        class NLM_CALL skill;

        FAIL_NLM(["Fail-Closed: Abortar Lote<br>(Erro MCP / Cookie Expirado)"])
        class FAIL_NLM danger;

        UPDATE_CACHE[("Upsert SQLite:<br>Atualiza tabela canon_cache")]
        class UPDATE_CACHE db;

        LOAD_CANON["Carrega Cânone na RAM<br>(Injeção Zero-Copy)"]
        class LOAD_CANON safe;

        START --> HW_CHECK
        HW_CHECK --> CHK_CACHE

        CHK_CACHE -->|"Não (Expirado/Inédito)"| NLM_CALL
        NLM_CALL -->|"Falha API"| FAIL_NLM
        NLM_CALL -->|"Sucesso"| UPDATE_CACHE
        UPDATE_CACHE --> LOAD_CANON

        CHK_CACHE -->|"Sim (Válido)"| LOAD_CANON
    end

    subgraph FASE_MINUS_1 ["FASE -1: GUARDIÃO DE DRIFT & ANTI-BAN"]
        FETCH_URLS["Puxa URLs do Lote Inicial"]
        class FETCH_URLS script;

        TOKEN_BUCKET["Rate Limiter (Token Bucket Assíncrono)<br>Jitter Anti-Ban GitHub API"]
        class TOKEN_BUCKET safe;

        API_CHK{"GitHub API (gh api / httpx)<br>Hash/Tag na Web mudou?"}
        class API_CHK script;

        SKIP(["Curto-Circuito: Pula Repositório<br>(Economia FinOps 100%)"])
        class SKIP safe;

        UPSERT_PENDENTE[("Upsert SQLite (Tabela: repositorios):<br>ultima_versao_online = Nova Tag<br>status_processamento = PENDENTE")]
        class UPSERT_PENDENTE db;

        LOAD_CANON --> FETCH_URLS
        FETCH_URLS -->|"Loop de URLs"| TOKEN_BUCKET
        TOKEN_BUCKET -->|"Disparo Seguro"| API_CHK

        API_CHK -->|"Igual a repo_version"| SKIP
        API_CHK -->|"Novo Hash/Tag (Drift)"| UPSERT_PENDENTE
    end

    NEXT_STAGE{"Fila MPSC (Tokio)<br>Worker Puxa Status 'PENDENTE' (Avança para FASE 1)"}
    class NEXT_STAGE script;

    UPSERT_PENDENTE -->|"Enfileira Job"| NEXT_STAGE
```

# SODA_ETL_V3_DeepDive_Fase1_Harvester_e_Sandbox

```mermaid
graph TD
    classDef skill fill:#ff5722,stroke:#bf360c,stroke-width:2px,color:#fff;
    classDef script fill:#1e1e1e,stroke:#4CAF50,stroke-width:2px,color:#fff;
    classDef raw fill:#2d2d2d,stroke:#2196F3,stroke-width:2px,color:#fff;
    classDef ai fill:#3d1a40,stroke:#9C27B0,stroke-width:2px,color:#fff;
    classDef db fill:#1a365d,stroke:#03A9F4,stroke-width:2px,color:#fff;
    classDef danger fill:#b91c1c,stroke:#ef4444,stroke-width:2px,color:#fff;
    classDef safe fill:#047857,stroke:#34d399,stroke-width:2px,color:#fff;

    START(["Fila MPSC Tokio<br>(Puxa tarefa com Status: PENDENTE)"])
    class START script;

    subgraph PREP ["1. PREPARAÇÃO BARE-METAL & RAMDISK"]
        RAMDISK["Aloca Ramdisk 2GB Dinâmico<br>(Evita desgaste TBW do SSD)"]
        class RAMDISK script;

        WSB_XML["Daemon Rust gera XML .wsb<br>(Configuração: Zero Egress / Read-Only)"]
        class WSB_XML script;

        CLONE["Git Clone Blobless<br>(--depth 1 --filter=blob:none no Ramdisk)"]
        class CLONE script;

        RAMDISK --> WSB_XML --> CLONE
    end

    START --> RAMDISK

    subgraph SANDBOX_ZONE ["2. WINDOWS SANDBOX (Isolamento de Execução)"]
        EXEC_WSB["Inicia Processo Isolado do Windows Sandbox"]
        class EXEC_WSB script;

        ROUTER{"Roteador Poliglota<br>(Detecta Stack Predominante)"}
        class ROUTER script;

        RUST_T["Trilha Rust<br>(cargo clippy, cargo test --list)"]
        class RUST_T raw;

        JS_T["Trilha JS/TS/Node<br>(npx oxlint, semgrep)"]
        class JS_T raw;

        GEN_T["Trilha Universal/Agnóstica<br>(JCodemunch AST, Extração de Manifestos)"]
        class GEN_T raw;

        EXEC_WSB --> ROUTER
        ROUTER -->|"Rust"| RUST_T
        ROUTER -->|"Node/JS"| JS_T
        ROUTER -->|"Universal"| GEN_T
    end

    CLONE --> EXEC_WSB

    subgraph PURGE ["3. EXTRAÇÃO PARA RAM & PURGA NUCLEAR"]
        EXTRACT_MEM["Daemon lê os arquivos .txt/.json gerados<br>direto para a Memória (VRAM/RAM)"]
        class EXTRACT_MEM raw;

        KILL_GUARD["Process Pool Guard (RAII)<br>Emite SIGKILL Atômico ao Sandbox"]
        class KILL_GUARD safe;

        UNMOUNT["Desmonta e Aniquila o Ramdisk<br>(Zero Lixo Deixado no Host)"]
        class UNMOUNT safe;

        RUST_T & JS_T & GEN_T --> EXTRACT_MEM
        EXTRACT_MEM --> KILL_GUARD --> UNMOUNT
    end

    subgraph NORMALIZATION ["4. PRE-FLIGHT & NORMALIZAÇÃO NO SQLITE"]
        TIKTOKEN{"Tiktoken Pre-Flight<br>(Calcula Tokens de forma isolada por Lente)"}
        class TIKTOKEN script;

        DISTILL["Fase 1.5: Destilação Semântica<br>(Qwen Local ou Flash-Lite resume a sujeira)"]
        class DISTILL ai;

        DB_INSERT[("SQLite: artefatos_brutos<br>(Salva Normalizado: repo_id, lente_alvo, conteudo_blob)")]
        class DB_INSERT db;

        DB_UPDATE[("SQLite: repositorios<br>(Altera Status_Processamento = FASE_1_OK)")]
        class DB_UPDATE db;

        UNMOUNT --> TIKTOKEN
        TIKTOKEN -->|"> Limite Limpo (Ex: 128k)"| DISTILL
        DISTILL --> DB_INSERT
        TIKTOKEN -->|"< Limite Limpo"| DB_INSERT
        DB_INSERT --> DB_UPDATE
    end

    NEXT_PHASE(["Libera Thread do Tokio<br>Avança Job para Fase 2 (Enxame)"])
    class NEXT_PHASE script;

    DB_UPDATE --> NEXT_PHASE
```

# SODA_ETL_V3_MicroFluxo_1_Arsenal_Tatico_RAW

```mermaid
graph TD
    classDef skill fill:#ff5722,stroke:#bf360c,stroke-width:2px,color:#fff;
    classDef script fill:#1e1e1e,stroke:#4CAF50,stroke-width:2px,color:#fff;
    classDef raw fill:#2d2d2d,stroke:#2196F3,stroke-width:2px,color:#fff;
    classDef db fill:#1a365d,stroke:#03A9F4,stroke-width:2px,color:#fff;
    classDef safe fill:#047857,stroke:#34d399,stroke-width:2px,color:#fff;

    START(["Daemon SODA (Rust/Tokio)<br>Controlador de Sidecars Efêmeros no Sandbox"])
    class START script;

    subgraph ARSENAL ["1. ARSENAL TÁTICO DE CLI (Força Bruta Mecânica)"]
        HTTPX["httpx / webcrawl-mcp<br>Captura: Promessa (README)"]
        OXC["oxc CLI<br>Captura: UX Contracts (Props/States)"]
        JCODE["jcodemunch-mcp<br>Captura: Árvore AST em O(1)"]
        AIDER["aider --repo-map<br>Captura: Topologia Arquitetural"]
        LINTERS["cargo clippy / semgrep<br>Captura: Lixo Tóxico e Unsafe Hotspots"]

        class HTTPX,OXC,JCODE,AIDER,LINTERS raw;
    end

    START -->|"Spawna Processos (Zero Arquivos Físicos)"| ARSENAL

    subgraph TRANSPORTE ["2. ROTEAMENTO IPC ZERO-COPY"]
        STDIO{"Interceptação Direta via stdio<br>(Morte absoluta da pasta _RAW_DATA)"}
        class STDIO safe;

        HTTPX -->|"Stream de Bytes"| STDIO
        OXC -->|"Stream de Bytes"| STDIO
        JCODE -->|"Stream de Bytes"| STDIO
        AIDER -->|"Stream de Bytes"| STDIO
        LINTERS -->|"Stream de Bytes"| STDIO
    end

    subgraph PERSISTENCIA ["3. INJEÇÃO ATÔMICA (SSOT)"]
        BLOB_NORM["Normalizador Rust<br>Empacota Streams em Blobs/Strings"]
        class BLOB_NORM script;

        DB_SQLITE[("soda_heuristic_vault.db<br>Tabela: artefatos_brutos<br>(Normalizado e pronto para Roteamento)")]
        class DB_SQLITE db;

        STDIO --> BLOB_NORM
        BLOB_NORM -->|"INSERT INTO artefatos_brutos"| DB_SQLITE
    end

    NEXT(["Libera Thread do Orquestrador<br>Avança para Micro-Fluxo 2 (Lentes)"])
    class NEXT script;

    DB_SQLITE --> NEXT
```

# SODA_ETL_V3_MicroFluxo_2_Roteamento_Lentes

```mermaid
graph TD
    classDef skill fill:#ff5722,stroke:#bf360c,stroke-width:2px,color:#fff;
    classDef script fill:#1e1e1e,stroke:#4CAF50,stroke-width:2px,color:#fff;
    classDef raw fill:#2d2d2d,stroke:#2196F3,stroke-width:2px,color:#fff;
    classDef ai fill:#3d1a40,stroke:#9C27B0,stroke-width:2px,color:#fff;
    classDef db fill:#1a365d,stroke:#03A9F4,stroke-width:2px,color:#fff;
    classDef safe fill:#047857,stroke:#34d399,stroke-width:2px,color:#fff;

    START(["Orquestrador Tokio (Fase 2)<br>Puxa Lote com Status: FASE_1_OK"])
    class START script;

    subgraph INGESTAO ["1. LEITURA DE BLOBS (Zero-Copy)"]
        DB_IN[("soda_heuristic_vault.db<br>Tabela: artefatos_brutos")]
        class DB_IN db;
        
        CANON_CTX["Contexto SODA (10_soda_canon_context.md)<br>Regras Duras e Dogmas Injetados"]
        class CANON_CTX safe;
        
        SLICER{"Roteador Semântico O(1)<br>Fatia Arquivos Específicos por Lente"}
        class SLICER script;

        START --> DB_IN --> SLICER
        CANON_CTX --> SLICER
    end

    subgraph LENTES ["2. ENXAME DE ESPECIALISTAS (Orchestrator-Worker)"]
        LENS_A["LENTE A (Visão Produto/UX)<br>Modelo: Claude Opus 4.7<br>Foco: Neuro-inclusão e Flow-Debt"]
        class LENS_A ai;
        
        LENS_B["LENTE B (Arquitetura/Bare-Metal)<br>Modelo: DeepSeek V4 Pro<br>Foco: Extração O(1), AST e Limite VRAM"]
        class LENS_B ai;
        
        LENS_C["LENTE C (Operação/Sustentação)<br>Modelo: GLM-5.1<br>Foco: Lixo Tóxico, FinOps e Rate Limits"]
        class LENS_C ai;

        SLICER -->|"+ 01_promessa_readme.md<br>+ 03_ux_contracts.txt<br>+ 03_test_intent.txt"| LENS_A
        
        SLICER -->|"+ 04_repo_outline.txt<br>+ 05_architecture_map.txt"| LENS_B
        
        SLICER -->|"+ 02_dependency_manifest.json<br>+ 06_unsafe_hotspots.txt<br>+ 07_ops_blueprint.txt<br>+ 08_health_report.json<br>+ 09_community_meta.json"| LENS_C
    end

    subgraph DEBATE ["3. CONSOLIDAÇÃO DO PARECER DIALÉTICO"]
        MERGE["Agregador Assíncrono<br>Junta as 3 narrativas brutas"]
        class MERGE script;

        DB_OUT[("soda_heuristic_vault.db<br>Tabela: debates_enxame<br>(Salva: lente_a_output_bruto, lente_b..., lente_c...)")]
        class DB_OUT db;

        LENS_A --> MERGE
        LENS_B --> MERGE
        LENS_C --> MERGE
        MERGE --> DB_OUT
    end

    NEXT(["Libera Thread do Orquestrador<br>Avança para Micro-Fluxo 3 (Funil Pydantic)"])
    class NEXT script;

    DB_OUT --> NEXT
```


# SODA_ETL_V3_DeepDive_Fases_2_e_3_Enxame_e_Sintese

```mermaid
graph TD
    classDef skill fill:#ff5722,stroke:#bf360c,stroke-width:2px,color:#fff;
    classDef script fill:#1e1e1e,stroke:#4CAF50,stroke-width:2px,color:#fff;
    classDef ai fill:#3d1a40,stroke:#9C27B0,stroke-width:2px,color:#fff;
    classDef db fill:#1a365d,stroke:#03A9F4,stroke-width:2px,color:#fff;
    classDef danger fill:#b91c1c,stroke:#ef4444,stroke-width:2px,color:#fff;
    classDef safe fill:#047857,stroke:#34d399,stroke-width:2px,color:#fff;

    START(["Fila MPSC Tokio<br>(Puxa tarefa com Status: FASE_1_OK)"])
    class START script;

    subgraph FASE_2 ["1. FASE 2: ENXAME DETERMINÍSTICO E FINOPS"]
        DB_IN[("SQLite: artefatos_brutos<br>(Contexto Normalizado)")]
        class DB_IN db;

        FINOPS{"iron_cost (Disjuntor)<br>Micropagamentos e Orçamento OK?"}
        class FINOPS script;

        ROUTER["Roteador Semântico O(1)<br>Fatia os artefatos por Lente"]
        class ROUTER script;

        L1["Lente A (Produto/UX)<br>Claude Opus 4.7"]
        class L1 ai;

        L2["Lente B (Arquitetura)<br>DeepSeek Pro"]
        class L2 ai;

        L3["Lente C (Ops/FinOps)<br>GLM-5.1"]
        class L3 ai;

        FALLBACK["Fallback Local Worker<br>(RTX 2060m - Qwen 3B)<br>Custo Zero"]
        class FALLBACK safe;

        DB_DEBATE[("SQLite: debates_enxame<br>(Salva Pareceres)<br>Status = FASE_2_OK")]
        class DB_DEBATE db;

        START --> DB_IN --> FINOPS
        FINOPS -->|"Estouro FinOps / Rate Limit"| FALLBACK
        FINOPS -->|"Saldo Aprovado"| ROUTER

        ROUTER -->|"UX Contracts + Promessa"| L1
        ROUTER -->|"AST + Mapa Arq"| L2
        ROUTER -->|"Manifestos + Hotspots"| L3

        L1 & L2 & L3 --> DB_DEBATE
        FALLBACK --> DB_DEBATE
    end

    subgraph FASE_3 ["2. FASE 3: SÍNTESE BARE-METAL E RALPH LOOP"]
        FETCH_DEBATE["Carrega Pareceres + Resumos<br>para Consolidação"]
        class FETCH_DEBATE script;

        LOCAL_SLM["Local Worker (RTX 2060m)<br>Qwen 3B ou Gemma 2B"]
        class LOCAL_SLM ai;

        LLGUIDANCE["llguidance (Rust)<br>Decodificação Restrita (CFG em 50µs)<br>Força JSON das 45 Colunas"]
        class LLGUIDANCE safe;

        RALPH_LOOP{"Ralph Loop<br>Auditoria Lógica de Conteúdo"}
        class RALPH_LOOP script;

        RETRY["Limpa VRAM e Re-injeta<br>(Morte e Renascimento)<br>Tentativa &lt; 3"]
        class RETRY skill;

        ERRO_SINTESE[("SQLite: etl_errors<br>Status = ERRO_SINTESE<br>(Falha Irrecuperável)")]
        class ERRO_SINTESE danger;

        DB_FINAL[("SQLite: repo_heuristics<br>Status = CONCLUIDO")]
        class DB_FINAL db;

        DB_DEBATE --> FETCH_DEBATE --> LOCAL_SLM
        LOCAL_SLM -->|"Gera Output"| LLGUIDANCE
        LLGUIDANCE -->|"Valida Pydantic AI"| RALPH_LOOP

        RALPH_LOOP -->|"Erro Lógico / Ambiguidade"| RETRY
        RETRY --> LOCAL_SLM

        RALPH_LOOP -->|"Falha após 3x"| ERRO_SINTESE
        RALPH_LOOP -->|"JSON Impecável"| DB_FINAL
    end

    NEXT_PHASE(["Libera Thread do Tokio<br>Avança Job para Fase 4 (SSOT)"])
    class NEXT_PHASE script;

    DB_FINAL --> NEXT_PHASE
    ERRO_SINTESE -->|"Libera e Pula Repositório"| NEXT_PHASE
```

# SODA_ETL_V3_MicroFluxo_3_Funil_Pydantic

```mermaid
graph TD
    classDef skill fill:#ff5722,stroke:#bf360c,stroke-width:2px,color:#fff;
    classDef script fill:#1e1e1e,stroke:#4CAF50,stroke-width:2px,color:#fff;
    classDef ai fill:#3d1a40,stroke:#9C27B0,stroke-width:2px,color:#fff;
    classDef db fill:#1a365d,stroke:#03A9F4,stroke-width:2px,color:#fff;
    classDef danger fill:#b91c1c,stroke:#ef4444,stroke-width:2px,color:#fff;
    classDef safe fill:#047857,stroke:#34d399,stroke-width:2px,color:#fff;

    START(["Orquestrador Tokio (Fase 3)<br>Puxa Lote com Status: FASE_2_OK"])
    class START script;

    subgraph INGESTAO ["1. LEITURA DO DEBATE DIALÉTICO"]
        DB_IN[("soda_heuristic_vault.db<br>Tabela: debates_enxame")]
        class DB_IN db;
        
        EXTRACT["Extrai Visões Opostas O(1)<br>(Lente A, Lente B, Lente C)"]
        class EXTRACT script;
        
        START --> DB_IN --> EXTRACT
    end

    subgraph FUNIL ["2. O FUNIL PYDANTIC & DECODIFICAÇÃO RESTRITA"]
        WORKER["Local Worker (RTX 2060m)<br>Qwen 2.5 3B ou Llama 3.2 3B"]
        class WORKER ai;

        LLGUIDANCE{"llguidance (Rust)<br>Aplica o Pydantic Schema em 50µs"}
        class LLGUIDANCE safe;

        SGR["Schema-Guided Reasoning (SGR)<br>Ordem de Geração Forçada"]
        class SGR script;

        TXT_GEN["1º: Campos Narrativos<br>justificativa_decisao, executive_verdict..."]
        class TXT_GEN ai;

        ENUM_GEN["2º: Enums e Scores Rígidos<br>classificacao_terminal, acao_de_canibalizacao..."]
        class ENUM_GEN ai;

        EXTRACT --> WORKER --> LLGUIDANCE
        LLGUIDANCE -->|"Valida Gramática no Nível do Token"| SGR
        
        SGR -->|"Obriga a IA a pensar antes de dar a nota"| TXT_GEN
        TXT_GEN -->|"Com base no texto, trava a classificação"| ENUM_GEN
    end

    subgraph CARGA ["3. CARGA ATÔMICA E JANELA DE VIDRO (52+ Colunas)"]
        DB_OUT[("soda_heuristic_vault.db<br>Tabela: repo_heuristics<br>(JSON Impecável e Estruturado)")]
        class DB_OUT db;

        MCP_SHEETS["mcp-google-sheets<br>Carga Atômica Batch Update"]
        class MCP_SHEETS skill;

        GS[("Google Sheets<br>Aba: MASTER_SOLUTIONS_v3")]
        class GS db;

        ENUM_GEN --> DB_OUT
        DB_OUT -->|"Sincronia SSOT"| MCP_SHEETS
        MCP_SHEETS -->|"Preenche as 52+ Colunas"| GS
    end

    NEXT(["Libera Thread do Orquestrador<br>Atualiza Status: CONCLUIDO"])
    class NEXT script;

    GS --> NEXT
```

# SODA_ETL_V3_DeepDive_Fase4_Sincronia_SSOT

```mermaid
graph TD
    classDef skill fill:#ff5722,stroke:#bf360c,stroke-width:2px,color:#fff;
    classDef script fill:#1e1e1e,stroke:#4CAF50,stroke-width:2px,color:#fff;
    classDef ai fill:#3d1a40,stroke:#9C27B0,stroke-width:2px,color:#fff;
    classDef db fill:#1a365d,stroke:#03A9F4,stroke-width:2px,color:#fff;
    classDef danger fill:#b91c1c,stroke:#ef4444,stroke-width:2px,color:#fff;
    classDef safe fill:#047857,stroke:#34d399,stroke-width:2px,color:#fff;

    START(["Fila MPSC Tokio<br>Puxa tarefa com Status: CONCLUIDO"])
    class START script;

    subgraph FASE_4 ["1. FASE 4: SINCRONIA TRILATERAL E CARGA ATÔMICA"]
        DB_IN[("SQLite: repo_heuristics<br>Contém JSON com 45 Colunas Formatadas")]
        class DB_IN db;

        DISPATCHER{"Orquestrador de Sincronia<br>Rust/Tokio - Distribuição Paralela"}
        class DISPATCHER script;

        RL_SHEETS{"Rate Limiter Token Bucket<br>Mitigação Estrita: 60 RPM Google API"}
        class RL_SHEETS safe;

        MCP_SHEETS["uvx mcp-google-sheets<br>batchUpdate Atômico / Destrutivo"]
        class MCP_SHEETS skill;

        GS[("Google Sheets<br>MASTER_SOLUTIONS_v3<br>A Janela de Vidro")]
        class GS db;

        LADYBUG_PARSE["Mapeamento Ontológico O(1)<br>Extrai Relações e Nós do JSON"]
        class LADYBUG_PARSE script;

        LADYBUG_DB[("LadybugDB Rust Nativo<br>Grafo Semântico Local<br>Ex: Repo -> resolve -> Padrão UX")]
        class LADYBUG_DB db;

        START --> DB_IN --> DISPATCHER

        DISPATCHER -->|"1. Carga Visual Humana"| RL_SHEETS
        RL_SHEETS -->|"Sinal Verde"| MCP_SHEETS
        MCP_SHEETS -->|"Sobrescreve Linha da Planilha"| GS

        DISPATCHER -->|"2. Carga Estrutural Máquina"| LADYBUG_PARSE
        LADYBUG_PARSE -->|"Persiste Arestas em Memória"| LADYBUG_DB
    end

    subgraph CLEANUP ["2. ENCERRAMENTO E AUDITORIA DE TRANSAÇÃO"]
        VERIFY{"As Duas Cargas Sheets e Grafo<br>Foram Bem Sucedidas?"}
        class VERIFY script;

        FAIL_SYNC[("SQLite: etl_errors<br>Status = ERRO_SYNC<br>Falha de Conexão ou API")]
        class FAIL_SYNC danger;

        SUCCESS_SYNC[("SQLite: repositorios<br>Status = SYNC_OK<br>Transação Concluída")]
        class SUCCESS_SYNC db;

        GS & LADYBUG_DB --> VERIFY

        VERIFY -->|"Sim SSOT Confirmado"| SUCCESS_SYNC
        VERIFY -->|"Falha Timeout ou Rate Limit"| FAIL_SYNC
    end

    NEXT_PHASE(["Libera Thread do Tokio<br>Orquestrador Puxa Próximo Job do Lote"])
    class NEXT_PHASE script;

    SUCCESS_SYNC --> NEXT_PHASE
    FAIL_SYNC -->|"Loga Erro e Pula"| NEXT_PHASE
```

# SODA_ETL_V3_DeepDive_Malha_Tokio_e_RalphLoop

```mermaid
graph TD
    classDef skill fill:#ff5722,stroke:#bf360c,stroke-width:2px,color:#fff;
    classDef script fill:#1e1e1e,stroke:#4CAF50,stroke-width:2px,color:#fff;
    classDef ai fill:#3d1a40,stroke:#9C27B0,stroke-width:2px,color:#fff;
    classDef db fill:#1a365d,stroke:#03A9F4,stroke-width:2px,color:#fff;
    classDef danger fill:#b91c1c,stroke:#ef4444,stroke-width:2px,color:#fff;
    classDef safe fill:#047857,stroke:#34d399,stroke-width:2px,color:#fff;

    ORCH(["Orquestrador MPSC Tokio<br>(Loop Assíncrono Principal)"])
    class ORCH script;

    DB_STATE[("SQLite: repositorios<br>Consulta de Status de Máquina de Estados")]
    class DB_STATE db;

    ORCH --> DB_STATE

    subgraph FAIL_FAST ["1. FAIL-FAST E CURTO-CIRCUITO (Proteção de Base)"]
        PULL_JOB["Worker puxa Job da Fila"]
        class PULL_JOB script;

        CHECK_TOXIC{"Análise Preliminar Estática<br>(Só PDFs? Vazio? domain=unknown?)"}
        class CHECK_TOXIC script;

        SHORT_CIRCUIT[("Upsert SQLite:<br>Status = SHORT-CIRCUIT")]
        class SHORT_CIRCUIT danger;

        DB_STATE --> PULL_JOB --> CHECK_TOXIC
        CHECK_TOXIC -->|"Sim (Lixo/Inválido)"| SHORT_CIRCUIT
        SHORT_CIRCUIT -->|"Ejeta do Circuito (Protege Tokens)"| ORCH
    end

    subgraph WORKER_PIPELINE ["2. PIPELINE DE EXTRAÇÃO"]
        EXEC_F1_F2["Orquestra Fases 1 (Harvester)<br>e 2 (Enxame Cognitivo)"]
        class EXEC_F1_F2 script;

        CHECK_TOXIC -->|"Não (Código Válido)"| EXEC_F1_F2
    end

    subgraph RALPH_LOOP ["3. RALPH LOOP: MALHA DE RESILIÊNCIA (Fase 3)"]
        START_RALPH["Inicia Síntese Pydantic AI<br>(Tentativa = 1)"]
        class START_RALPH script;

        EXEC_F1_F2 -->|"Puxa FASE_2_OK"| START_RALPH

        LLM_EVAL["Local SLM + llguidance<br>(Decodificação Restrita / Geração JSON)"]
        class LLM_EVAL ai;

        VALIDATE{"Validação Estrita<br>(Auditoria Lógica e de Schema)"}
        class VALIDATE script;

        START_RALPH --> LLM_EVAL --> VALIDATE

        CHECK_LIMIT{"Tentativa &lt; 3?"}
        class CHECK_LIMIT script;

        PURGE["Context Purge (Morte e Renascimento)<br>Aniquila VRAM e Esquece Erro Anterior"]
        class PURGE safe;

        VALIDATE -->|"Falha (Erro Sintático/Alucinação)"| CHECK_LIMIT
        CHECK_LIMIT -->|"Sim"| PURGE
        PURGE -->|"Incrementa Tentativa (+1)"| LLM_EVAL

        KILL_SWITCH[("SQLite: etl_errors<br>Status = ERRO_SINTESE<br>(Kill-Switch Ativado)")]
        class KILL_SWITCH danger;

        CHECK_LIMIT -->|"Não (Estourou Limite 3x)"| KILL_SWITCH
    end

    subgraph SUCCESS_ROUTE ["4. SUCESSO E LIBERAÇÃO"]
        SAVE_OK[("SQLite: repo_heuristics<br>Status = CONCLUIDO")]
        class SAVE_OK db;

        VALIDATE -->|"Sucesso (JSON Perfeito / Exit Code 0)"| SAVE_OK
    end

    KILL_SWITCH -->|"Loga Erro e Libera Thread"| ORCH
    SAVE_OK -->|"Avança para Fase 4 (SSOT/Sincronia)"| ORCH
```
