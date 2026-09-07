# MCP_TOOL_CONTRACTS.md — Catálogo Canônico e Contratos de Ferramentas MCP (v7)

> **ESTATUS DO DOCUMENTO:** CANÔNICO E IMUTÁVEL (DOCS-AS-GUARDRAILS)
> **APLICAÇÃO:** Todos os agentes de IA (Cursor, Windsurf, Claude Code) e engenheiros humanos.
> **CRATES DIRETAMENTE REGIDAS:** `souls_protocol`, `souls_ast`, `souls_memory`, `souls_model_router`, `souls_server`.
> **PROPÓSITO:** Congelar as assinaturas, tipos, parâmetros, esquemas JSON-RPC 2.0 e códigos de erro de domínio expostos pelo daemon `souls_server.exe` ao Hermes Agent. Qualquer alteração não retrocompatível neste catálogo quebra a integração agêntica e é estritamente proibida.

## 1. PREÂMBULO E TRANSPORTE DE COMUNICAÇÃO MCP

O subsistema MCP do Souls Engine é hospedado exclusivamente pelo processo nativo bare-metal `souls_server.exe` através do transporte **Streamable HTTP/SSE em Localhost TCP Loopback**:

- **Endpoint Canônico:** `http://127.0.0.1:9123/mcp`
- **Especificação Base:** Model Context Protocol (especificação 2024-11-05) sobre envelopes JSON-RPC 2.0.
- **Server Identifier (Servername):** `souls_mcp`
- **Teto de Caracteres para Nomes de Ferramentas:** $\le 32\text{ caracteres}$.
- **Teto de Caracteres para Descrições:** $\le 160\text{ caracteres}$, com foco estritamente técnico e utilitário.
- **Resolução e Normalização de Aliases:** O despachante RPC na crate `souls_server` aceita tanto o nome canônico prefixado (`souls_<tool>`) quanto o nome sintético limpo (`<tool>`), mapeando-os de forma transparente para o mesmo executor em Rust.

## 2. CATÁLOGO CANÔNICO DAS 6 FERRAMENTAS MCP

O Souls Engine expõe exatamente seis ferramentas MCP de alta performance. Nenhuma ferramenta externa ou atalho de sistema fora desta lista pode ser exposto sem revisão arquitetural formal.

```
┌───────────────────────────────────────────────────────────────────────────┐
│                       CATÁLOGO DE FERRAMENTAS MCP                         │
│                                                                           │
│   1. souls_ast_outline   ──► Desidratação de código na origem (70-85%)    │
│   2. souls_ast_slice     ──► Recorte cirúrgico de escopo funcional        │
│   3. souls_myers_diff    ──► Diferença mínima estruturada com validação   │
│   4. souls_repo_heatmap  ──► Análise de calor e Frecency via gitoxide     │
│   5. souls_memory_recall ──► Busca híbrida RRF com barreira ontológica    │
│   6. souls_route_subtask ──► Roteador ParetoBandit FinOps para subagentes │
└───────────────────────────────────────────────────────────────────────────┘
```

### 2.1 `souls_ast_outline`

#### Descrição Operacional

Gera o esqueleto sintático desidratado de um arquivo de código-fonte em alta velocidade e em memória. Suprime integralmente os corpos internos de funções, métodos e blocos de controle, preservando assinaturas públicas, tipos estruturais (`struct`, `enum`, `interface`), docstrings e visibilidade. Substitui implementações por marcadores métricos de desidratação (ex: `/* ... omitted [32 lines] ... */`), reduzindo o consumo de tokens entre 70% e 85%.

- **Crate Executora:** `souls_ast`
- **Linguagens Suportadas:** Rust (`.rs`), Python (`.py`), TypeScript/JavaScript (`.ts`, `.tsx`, `.js`, `.jsx`), Go (`.go`).

#### JSON Schema de Entrada (`tools/list` -> `inputSchema`)

```
{
  "type": "object",
  "properties": {
    "path": {
      "type": "string",
      "description": "Caminho relativo do arquivo de código a partir da raiz do workspace (ex: 'crates/souls_core/src/logging.rs'). Finais de linha devem ser LF."
    },
    "include_private": {
      "type": "boolean",
      "description": "Se verdadeiro, preserva assinaturas de funções e tipos privados. Padrão: false.",
      "default": false
    }
  },
  "required": ["path"],
  "additionalProperties": false
}
```

#### Exemplo de Payload de Invocação (`tools/call`)

```
{
  "jsonrpc": "2.0",
  "id": "req_001",
  "method": "tools/call",
  "params": {
    "name": "souls_ast_outline",
    "arguments": {
      "path": "crates/souls_core/src/mpsc_flusher.rs",
      "include_private": false
    }
  }
}
```

#### Exemplo de Resposta Estruturada

```
{
  "jsonrpc": "2.0",
  "id": "req_001",
  "result": {
    "content": [
      {
        "type": "text",
        "text": "// Language: Rust | Original: 128 lines | Dehydrated: 24 lines | Token Reduction: 81.2%\n\npub struct MpscBatchBuffer<T> {\n    tx: tokio::sync::mpsc::Sender<T>,\n}\n\nimpl<T: Send + 'static> MpscBatchBuffer<T> {\n    pub fn new(capacity: usize) -> (Self, tokio::sync::mpsc::Receiver<T>) { /* ... omitted [8 lines] ... */ }\n    pub async fn push(&self, item: T) -> Result<(), CoreError> { /* ... omitted [14 lines] ... */ }\n}\n\npub async fn spawn_flusher_task<T, F, Fut>(rx: tokio::sync::mpsc::Receiver<T>, interval_secs: u64, flusher_fn: F) -> tokio::task::JoinHandle<()>\nwhere\n    T: Send + 'static,\n    F: Fn(Vec<T>) -> Fut + Send + Sync + 'static,\n    Fut: std::future::Future<Output = ()> + Send + 'static,\n{ /* ... omitted [36 lines] ... */ }"
      }
    ],
    "isError": false
  }
}
```

### 2.2 `souls_ast_slice`

#### Descrição Operacional

Localiza cirurgicamente um nó sintático específico na árvore concreta do Tree-sitter com base em uma consulta de símbolo (função, método, struct ou trait) e retorna integralmente seu corpo e contexto de declaração, eliminando a necessidade de ler o arquivo de código por inteiro via `read_file`.

- **Crate Executora:** `souls_ast`

#### JSON Schema de Entrada (`tools/list` -> `inputSchema`)

```
{
  "type": "object",
  "properties": {
    "path": {
      "type": "string",
      "description": "Caminho relativo do arquivo de código (ex: 'crates/souls_memory/src/sqlite.rs')."
    },
    "symbol_query": {
      "type": "string",
      "description": "Identificador ou caminho de símbolo qualificado a extrair (ex: 'SoulsMemoryStore::persist_turn' ou 'canonical_lance_schema')."
    }
  },
  "required": ["path", "symbol_query"],
  "additionalProperties": false
}
```

#### Exemplo de Payload de Invocação (`tools/call`)

```
{
  "jsonrpc": "2.0",
  "id": "req_002",
  "method": "tools/call",
  "params": {
    "name": "souls_ast_slice",
    "arguments": {
      "path": "crates/souls_memory/src/lancedb.rs",
      "symbol_query": "canonical_lance_schema"
    }
  }
}
```

#### Exemplo de Resposta Estruturada

```
{
  "jsonrpc": "2.0",
  "id": "req_002",
  "result": {
    "content": [
      {
        "type": "text",
        "text": "// Symbol: canonical_lance_schema | Kind: function_item | Lines 14-32 | Bytes 412-1085\npub fn canonical_lance_schema() -> Arc<Schema> {\n    Arc::new(Schema::new(vec![\n        Field::new(\"id\", DataType::Utf8, false),\n        Field::new(\n            \"vector\",\n            DataType::FixedSizeList(\n                Arc::new(Field::new(\"item\", DataType::Float32, false)),\n                384,\n            ),\n            false,\n        ),\n        Field::new(\"session_id\", DataType::Utf8, false),\n        Field::new(\"category\", DataType::Utf8, false),\n        Field::new(\"partition\", DataType::Utf8, false),\n        Field::new(\"salience\", DataType::Float32, false),\n        Field::new(\"created_at\", DataType::Int64, false),\n    ]))\n}"
      }
    ],
    "isError": false
  }
}
```

### 2.3 `souls_myers_diff`

#### Descrição Operacional

Computa a diferença mínima estruturada e canônica entre o conteúdo atual de um arquivo de disco e um bloco textual proposto, validando regras de fechamento e integridade sintática antes da aplicação. Retorna o patch unificado e a validação estrutural.

- **Crate Executora:** `souls_ast` (utilizando o algoritmo Myers via crate `similar`)

#### JSON Schema de Entrada (`tools/list` -> `inputSchema`)

```
{
  "type": "object",
  "properties": {
    "path": {
      "type": "string",
      "description": "Caminho relativo do arquivo de destino onde a alteração será avaliada."
    },
    "new_content": {
      "type": "string",
      "description": "Novo conteúdo integral proposto para o arquivo (deve utilizar estritamente finais de linha LF)."
    }
  },
  "required": ["path", "new_content"],
  "additionalProperties": false
}
```

#### Exemplo de Payload de Invocação (`tools/call`)

```
{
  "jsonrpc": "2.0",
  "id": "req_003",
  "method": "tools/call",
  "params": {
    "name": "souls_myers_diff",
    "arguments": {
      "path": "crates/souls_protocol/src/lib.rs",
      "new_content": "#![forbid(unsafe_code)]\n\npub mod dto;\npub mod error;\npub mod lean;\npub mod mcp;\n"
    }
  }
}
```

#### Exemplo de Resposta Estruturada

```
{
  "jsonrpc": "2.0",
  "id": "req_003",
  "result": {
    "content": [
      {
        "type": "text",
        "text": "--- a/crates/souls_protocol/src/lib.rs\n+++ b/crates/souls_protocol/src/lib.rs\n@@ -1,4 +1,6 @@\n+#![forbid(unsafe_code)]\n+\n pub mod dto;\n pub mod error;\n pub mod lean;\n pub mod mcp;\n"
      }
    ],
    "isError": false
  }
}
```

### 2.4 `souls_repo_heatmap`

#### Descrição Operacional

Calcula o índice de calor e relevância operacional (**Frecency**) dos arquivos do repositório inspecionando o grafo de commits locais via biblioteca bare-metal `gitoxide` (`gix`). Permite ao Hermes Agent identificar com precisão cirúrgica quais arquivos sofreram maior intensidade de alterações recentes sem invocar subprocessos de terminal (`git.exe`).

A métrica formal de Frecency $F(a)$ para um arquivo $a$ é computada por:

$$F(a) = \sum_{c \in \text{Commits}} \text{Changes}(a, c) \cdot \exp\left(-\lambda \cdot (t_{\text{atual}} - t_c)\right)$$

Onde $\lambda = \frac{\ln(2)}{t_{\text{half-life}}}$, com constante de meia-vida calibrada para $7.0\text{ dias}$.

- **Crate Executora:** `souls_ast`

#### JSON Schema de Entrada (`tools/list` -> `inputSchema`)

```
{
  "type": "object",
  "properties": {
    "max_results": {
      "type": "integer",
      "description": "Quantidade máxima de arquivos retornados no topo do ranking de calor. Padrão: 20.",
      "default": 20,
      "minimum": 1,
      "maximum": 100
    },
    "time_window_days": {
      "type": "integer",
      "description": "Janela temporal de análise retroativa de commits em dias. Padrão: 30.",
      "default": 30,
      "minimum": 1,
      "maximum": 365
    }
  },
  "additionalProperties": false
}
```

#### Exemplo de Payload de Invocação (`tools/call`)

```
{
  "jsonrpc": "2.0",
  "id": "req_004",
  "method": "tools/call",
  "params": {
    "name": "souls_repo_heatmap",
    "arguments": {
      "max_results": 3,
      "time_window_days": 14
    }
  }
}
```

#### Exemplo de Resposta Estruturada

```
{
  "jsonrpc": "2.0",
  "id": "req_004",
  "result": {
    "content": [
      {
        "type": "text",
        "text": "[\n  {\"path\": \"crates/souls_memory/src/sqlite.rs\", \"frecency_score\": 94.2, \"recent_commits\": 7, \"lines_churned\": 342},\n  {\"path\": \"docs/contracts/MCP_TOOL_CONTRACTS.md\", \"frecency_score\": 88.5, \"recent_commits\": 4, \"lines_churned\": 512},\n  {\"path\": \"crates/souls_server/src/routes_mcp.rs\", \"frecency_score\": 76.1, \"recent_commits\": 3, \"lines_churned\": 180}\n]"
      }
    ],
    "isError": false
  }
}
```

### 2.5 `souls_memory_recall`

#### Descrição Operacional

Recupera fragmentos de conhecimento e diretrizes operacionais através da **Tríade Hipocampal L3**, combinando busca léxica FTS5 (BM25) no FrankenSQLite e busca vetorial de cosseno no LanceDB via **Reciprocal Rank Fusion (RRF)** com constante de suavização $k = 60$. Os resultados são filtrados em memória pela barreira ontológica do grafo **LadybugDB**, expurgando memórias que possuam arestas ativas `conflicts_with` em relação a decisões arquiteturais da partição `STABLE`.

- **Crate Executora:** `souls_memory`

#### JSON Schema de Entrada (`tools/list` -> `inputSchema`)

```
{
  "type": "object",
  "properties": {
    "query": {
      "type": "string",
      "description": "Consulta conceitual, técnica ou léxica a ser pesquisada no repositório hipocampal."
    },
    "limit": {
      "type": "integer",
      "description": "Quantidade máxima de registros ranqueados a retornar. Padrão: 5.",
      "default": 5,
      "minimum": 1,
      "maximum": 20
    },
    "partition_filter": {
      "type": "string",
      "enum": ["ALL", "STABLE", "EVOLVING"],
      "description": "Filtro opcional de partição ontológica. Padrão: 'ALL'.",
      "default": "ALL"
    }
  },
  "required": ["query"],
  "additionalProperties": false
}
```

#### Exemplo de Payload de Invocação (`tools/call`)

```
{
  "jsonrpc": "2.0",
  "id": "req_005",
  "method": "tools/call",
  "params": {
    "name": "souls_memory_recall",
    "arguments": {
      "query": "politica de isolamento de vram rtx 2060m",
      "limit": 2,
      "partition_filter": "STABLE"
    }
  }
}
```

#### Exemplo de Resposta Estruturada

```
{
  "jsonrpc": "2.0",
  "id": "req_005",
  "result": {
    "content": [
      {
        "type": "text",
        "text": "[\n  {\n    \"id\": \"mem_a1f94c8e\",\n    \"partition\": \"STABLE\",\n    \"category\": \"hardware_governance\",\n    \"salience\": 1.0,\n    \"rrf_score\": 0.0328,\n    \"content\": \"A GPU RTX 2060m possui teto útil de 5.294 MB no WDDM. Tier 1 e Tier 2 operam sob estrita exclusão mútua na VRAM dedicada. Acima de 82°C ou menos de 800 MB livres, abortar inferência local imediatamente.\",\n    \"source_ref\": \"docs/architecture/souls_engine_v7_canonical_spec.md\"\n  }\n]"
      }
    ],
    "isError": false
  }
}
```

### 2.6 `souls_route_subtask`

#### Descrição Operacional

Consulta o motor bayesiano multiobjetivo **ParetoBandit** para calcular e recomendar o Tier de execução ótimo, o endpoint canônico e os hiperparâmetros de inferência para uma subtarefa delegada (`delegate_task`) ou slot auxiliar (`auxiliary.*`). Integra a barreira termodinâmica $\Phi(T_{\text{GPU}}, V_{\text{livre}}, k)$ e a pontuação histórica da Métrica $E^3$ (Eficácia, Economia, Eficiência).

- **Crate Executora:** `souls_model_router`
- **Restrição Constitucional:** Esta ferramenta destina-se **exclusivamente a subtarefas autocontidas e subagentes**. É terminantemente proibido utilizar esta recomendação para chavear o modelo do chat mestre interativo intradiálogo.

#### JSON Schema de Entrada (`tools/list` -> `inputSchema`)

```
{
  "type": "object",
  "properties": {
    "task_description": {
      "type": "string",
      "description": "Descrição textual concisa do objetivo da subtarefa a ser delegada."
    },
    "estimated_input_tokens": {
      "type": "integer",
      "description": "Contagem projetada de tokens de entrada (prompt + contexto injetado).",
      "minimum": 1
    },
    "estimated_output_tokens": {
      "type": "integer",
      "description": "Contagem projetada de tokens de saída a serem gerados.",
      "minimum": 1
    },
    "requires_code_generation": {
      "type": "boolean",
      "description": "Indica se a subtarefa demanda geração ou refatoração sintática de código-fonte."
    },
    "complexity_tier_hint": {
      "type": "string",
      "enum": ["TRIVIAL", "STANDARD", "COMPLEX", "REFLECTIVE"],
      "description": "Indicação heurística de profundidade analítica requerida. Padrão: 'STANDARD'.",
      "default": "STANDARD"
    }
  },
  "required": [
    "task_description",
    "estimated_input_tokens",
    "estimated_output_tokens",
    "requires_code_generation"
  ],
  "additionalProperties": false
}
```

#### Exemplo de Payload de Invocação (`tools/call`)

```
{
  "jsonrpc": "2.0",
  "id": "req_006",
  "method": "tools/call",
  "params": {
    "name": "souls_route_subtask",
    "arguments": {
      "task_description": "Extrair assinaturas públicas e gerar esqueleto da crate souls_ast",
      "estimated_input_tokens": 1200,
      "estimated_output_tokens": 350,
      "requires_code_generation": true,
      "complexity_tier_hint": "STANDARD"
    }
  }
}
```

#### Exemplo de Resposta Estruturada

```
{
  "jsonrpc": "2.0",
  "id": "req_006",
  "result": {
    "content": [
      {
        "type": "text",
        "text": "{\n  \"recommended_tier\": \"Tier 1\",\n  \"model_identifier\": \"qwen2.5-coder-3b-instruct\",\n  \"provider\": \"custom_local\",\n  \"base_url\": \"http://127.0.0.1:9123/v1\",\n  \"sampling_params\": {\n    \"temperature\": 0.2,\n    \"top_p\": 0.95,\n    \"max_tokens\": 512\n  },\n  \"projected_cost_usd\": 0.0,\n  \"expected_latency_ms\": 420.0,\n  \"pareto_utility_score\": 0.884,\n  \"thermodynamic_status\": {\n    \"gpu_temp_celsius\": 58.0,\n    \"vram_free_mb\": 2140,\n    \"barrier_penalty\": 0.0\n  }\n}"
      }
    ],
    "isError": false
  }
}
```

## 3. ENVELOPES CANÔNICOS DE TRANSPORTE JSON-RPC 2.0

Para erradicar discrepâncias de serialização entre o runtime Python do Hermes Agent e a camada Axum em Rust, os envelopes canônicos de comunicação MCP seguem estritamente as definições abaixo.

### 3.1 Envelope de Requisição de Execução (`tools/call`)

```
{
  "jsonrpc": "2.0",
  "id": "<identificador_unico_string_ou_inteiro>",
  "method": "tools/call",
  "params": {
    "name": "<nome_da_ferramenta>",
    "arguments": {
      "<chave>": "<valor>"
    }
  }
}
```

### 3.2 Envelope de Resposta com Sucesso

O campo `result.content` deve conter obrigatoriamente um array de blocos estruturados, sendo o tipo primário `"text"` contendo o payload serializado ou string pura:

```
{
  "jsonrpc": "2.0",
  "id": "<identificador_original_da_requisicao>",
  "result": {
    "content": [
      {
        "type": "text",
        "text": "<payload_estruturado_ou_texto_da_resposta>"
      }
    ],
    "isError": false
  }
}
```

### 3.3 Envelope de Erro Padronizado

Caso a execução da ferramenta falhe, a resposta deve manter `isError: true` ou retornar o bloco de erro nativo do JSON-RPC 2.0:

```
{
  "jsonrpc": "2.0",
  "id": "<identificador_original_da_requisicao>",
  "error": {
    "code": -32001,
    "message": "FileTooLargeForAST: O arquivo excede o limite físico de análise de 2 MB.",
    "data": {
      "path": "crates/souls_memory/src/huge_dump.rs",
      "size_bytes": 3145728,
      "max_allowed_bytes": 2097152
    }
  }
}
```

## 4. TABELA CANÔNICA DE CÓDIGOS DE ERRO DE DOMÍNIO

Qualquer exceção capturada pelas barreiras `catch_unwind` da crate `souls_core` ou validadores de esquema deve ser mapeada para os seguintes códigos de domínio fixos (faixa `-32000` a `-32099` reservada para extensões de aplicação do JSON-RPC):

| **Código**   | **Identificador de Erro**     | **Causa Técnica Primária**                                                                                   | **Ação Recomendada para o Hermes Agent**                                |
| ------------ | ----------------------------- | ------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------- |
| **`-32000`** | `InternalEnginePanic`         | Pânico recuperado na thread Tokio via `catch_unwind`.                                                        | Registrar falha crítica; não retentar sem alterar parâmetros.           |
| **`-32001`** | `FileTooLargeForAST`          | Arquivo físico $> 2\text{ MB}$, excedendo teto seguro de parsing em memória.                                 | Recuar de modo transparente para leitura de blocos (`read_file`).       |
| **`-32002`** | `VramThermalThrottled`        | Watchdog NVML disparou barreira: $T \ge 82^\circ\text{C}$ ou $\text{VRAM}_{\text{livre}} \le 800\text{ MB}$. | Desviar subtarefa para Tier 3 (Nuvem Fast) ou processamento em CPU.     |
| **`-32003`** | `SymbolNotFound`              | O identificador fornecido em `symbol_query` não foi encontrado na AST.                                       | Invocar `souls_ast_outline` para verificar a grafia exata dos símbolos. |
| **`-32004`** | `AstGrammarParseError`        | Erro fatal na gramática Tree-sitter decorrente de código estruturalmente quebrado.                           | Inspecionar arquivo de destino manualmente ou tentar diff parcial.      |
| **`-32005`** | `OntologicalBarrierViolation` | LadybugDB detectou conflito explícito com regra `STABLE` via aresta `conflicts_with`.                        | Descartar memória candidata; ela viola diretrizes do projeto.           |
| **`-32006`** | `InvalidInputParameters`      | Violação de tipagem, parâmetro obrigatório ausente ou string vazia.                                          | Ajustar os parâmetros conforme o JSON Schema formal da ferramenta.      |
| **`-32007`** | `RefsPathLeakViolation`       | Caminho fornecido tenta escapar da partição `Z:\` via `..` ou aponta para `C:\`.                             | Corrigir caminho relativo para residir estritamente no workspace `Z:`.  |
| **`-32008`** | `GitoxideRepositoryError`     | Falha ao abrir ou inspecionar o repositório Git bare-metal via `gix`.                                        | Verificar se o diretório `.git` está acessível e não corrompido.        |

## 5. LINHAS VERMELHAS E ANTI-PATTERNS NAS FERRAMENTAS MCP

1. **PROIBIÇÃO DE ALTERAÇÃO DE NOMES DE CHAVES:** É estritamente proibido renomear qualquer campo de entrada nos JSON Schemas (ex: renomear `path` para `file_path`, ou `symbol_query` para `query`). Isso quebra a integração com o Hermes Agent sem aviso prévio.
2. **PROIBIÇÃO DE PAYLOADS CRLF:** Toda ferramenta que devolve código-fonte ou texto estruturado (`souls_ast_outline`, `souls_ast_slice`, `souls_myers_diff`) deve higienizar o payload, garantindo que todas as quebras de linha sejam exclusivamente `\n` (LF).
3. **PROIBIÇÃO DE I/O BLOQUEANTE NO DISCO FORA DO BUFFER:** Ferramentas MCP não devem ler ou gravar arquivos sem passar pelas salvaguardas da crate `souls_core` e validações de caminho em `Z:\`.
4. **PROIBIÇÃO DE LOGS DE TELEMETRIA SÍNCRONOS:** O registro de métricas de invocação de ferramentas MCP deve ser emitido via canal MPSC assíncrono para o buffer de 5 segundos, sem efetuar transações diretas no SQLite a cada chamada de ferramenta.
5. **ESTRITA IMUTABILIDADE DE SCHEMAS:** Nenhuma nova ferramenta pode ser introduzida sem a criação de um teste de conformidade de schema correspondente na suíte de testes de integração do repositório.