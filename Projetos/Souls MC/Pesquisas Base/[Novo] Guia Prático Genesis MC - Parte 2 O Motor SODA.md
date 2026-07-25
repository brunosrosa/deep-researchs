---
aliases: []
sticker: lucide//chevron-right-square
---
# Genesis MC: Guia Prático - Parte 2 (O Motor SODA)

Este documento detalha a implementação do backend do **Genesis MC (SODA)** usando **Rust, Tauri e SQLite**. O objetivo deste motor é ser brutalmente eficiente, consumindo o mínimo de RAM e CPU, deixando a VRAM livre para os seus SLMs locais (Phi-4-mini, Qwen, etc).

## 1. O Loop Principal e IPC (Inter-Process Communication)

No Tauri, o Frontend (React) e o Backend (Rust) rodam em processos separados. Para que os agentes "pensem" sem congelar a sua UI (Canvas), você **nunca** deve rodar inferência de IA ou queries pesadas na thread principal do Tauri.

**O Padrão Arquitetural:** `MPSC Channels` (Multi-Producer, Single-Consumer) do `tokio` combinados com `Tauri Events`.

### Estrutura do `src-tauri/src/main.rs`

```rust
// Importações essenciais
use tauri::{Manager, State, Emitter};
use tokio::sync::mpsc;
use serde::{Serialize, Deserialize};

// 1. Definição do Payload de Comunicação
#[derive(Clone, Serialize)]
struct AgentThought {
    agent_id: String,
    content: String,
    is_final: bool,
}

// 2. O Estado Global da Aplicação (Injetado via Tauri State)
struct AppState {
    // Canal para enviar comandos do Frontend para o Loop de IA no Rust
    tx_ai_commands: mpsc::Sender<String>, 
}

// 3. O Comando Tauri (Chamado pelo React)
#[tauri::command]
async fn ask_agent(prompt: String, state: State<'_, AppState>) -> Result<(), String> {
    // Apenas envia a mensagem para o canal, NÃO bloqueia a UI aguardando a IA
    state.tx_ai_commands.send(prompt).await.map_err(|e| e.to_string())?;
    Ok(())
}

fn main() {
    // Criação dos canais assíncronos
    let (tx, mut rx) = mpsc::channel::<String>(32);

    tauri::Builder::default()
        .manage(AppState { tx_ai_commands: tx })
        .setup(|app| {
            let app_handle = app.handle().clone();
            
            // 4. O "Motor SODA" rodando em background
            tauri::async_runtime::spawn(async move {
                while let Some(prompt) = rx.recv().await {
                    println!("Agente recebeu: {}", prompt);
                    
                    // AQUI entra a chamada para o Llama.cpp ou MCP
                    // Simulando o raciocínio em streaming do agente
                    for i in 1..=5 {
                        tokio::time::sleep(std::time::Duration::from_millis(500)).await;
                        
                        // Emitindo eventos em tempo real para o Frontend (React atualizar o Canvas)
                        app_handle.emit("agent-thought", AgentThought {
                            agent_id: "phi-4-mini".to_string(),
                            content: format!("Processando token {}...", i),
                            is_final: i == 5,
                        }).unwrap();
                    }
                }
            });

            Ok(())
        })
        .invoke_handler(tauri::generate_handler![ask_agent])
        .run(tauri::generate_context!())
        .expect("Erro ao rodar Genesis MC");
}
```

**Por que isso importa?** Este padrão garante que o React dispare a pergunta e fique livre para animar a UI. As respostas chegam via _event listeners_ no Frontend (veremos isso na Parte 3).

## 2. Memory Bank (SQLite) - O Hippocampus do Life Coach

Para que o sistema seja verdadeiramente "Sovereign" e aprenda com você (o conceito de _Progressive Disclosure_ e _Co-evolução_), ele precisa de uma memória de longo prazo. Não use Vector Databases gigantescos agora. Use **SQLite + FTS5** (Full Text Search) para velocidade extrema.

Crie uma pasta `src-tauri/db` e utilize a crate `sqlx` (fortemente recomendada para Rust assíncrono).

### O Schema SQL (Estrutura do Grafo de Conhecimento Relacional)

```sql
-- Arquivo: src-tauri/db/schema.sql

-- 1. EPISÓDIOS: A Memória Episódica (O que aconteceu e quando)
CREATE TABLE IF NOT EXISTS episodes (
    id TEXT PRIMARY KEY,           -- UUID
    timestamp DATETIME DEFAULT CURRENT_TIMESTAMP,
    agent_id TEXT NOT NULL,        -- Qual agente estava ativo
    user_prompt TEXT NOT NULL,
    agent_response TEXT NOT NULL,
    summary TEXT,                  -- Resumo gerado por um modelo menor em background
    embedding BLOB                 -- (Futuro) Para busca vetorial local com sqlite-vec
);

-- 2. ENTIDADES: A Memória Semântica (Quem/O que o agente conhece)
CREATE TABLE IF NOT EXISTS entities (
    id TEXT PRIMARY KEY,           -- UUID
    name TEXT NOT NULL UNIQUE,     -- Ex: "Bruno", "RTX 2060m", "Projeto SODA"
    type TEXT NOT NULL,            -- Ex: "Pessoa", "Hardware", "Conceito"
    attributes TEXT,               -- JSON contendo detalhes (Ex: {"VRAM": "6GB", "Arquitetura": "Turing"})
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    last_updated DATETIME DEFAULT CURRENT_TIMESTAMP
);

-- 3. RELACIONAMENTOS: O Grafo de Conhecimento Local
CREATE TABLE IF NOT EXISTS entity_relations (
    source_id TEXT NOT NULL,
    target_id TEXT NOT NULL,
    relation_type TEXT NOT NULL,   -- Ex: "POSSUI", "TRABALHA_EM", "LIMITA"
    weight INTEGER DEFAULT 1,      -- Força da relação
    FOREIGN KEY(source_id) REFERENCES entities(id),
    FOREIGN KEY(target_id) REFERENCES entities(id),
    PRIMARY KEY (source_id, target_id, relation_type)
);

-- Índice para busca rápida de texto completo
CREATE VIRTUAL TABLE IF NOT EXISTS episodes_fts USING fts5(
    summary, user_prompt, agent_response, content='episodes', content_rowid='id'
);
```

**Integração Prática com as NFRs (Non-Functional Requirements):**

A tabela de `entities` é onde o agente armazena suas restrições. Quando você disser _"Crie um modelo 3D"_, o Agente busca `RTX 2060m` nas `entities`, lê o JSON `{"VRAM": "6GB"}`, e o **Toon Context** comprime isso e envia para o Phi-4-mini: _"Aviso do Sistema: Usuário possui apenas 6GB de VRAM. Não proponha soluções pesadas."_

## 3. Spawning MCP Servers (Model Context Protocol) no Rust

Como mapeado na sua planilha, usaremos MCPs vitais como o **Time MCP** (para dar noção cronológica) e o **Sequential Thinking MCP**. O Rust atuará como o cliente (Host) que inicia (spawna) esses servidores em _background_ como processos filhos (`child processes`) e se comunica com eles via `stdio` (Standard Input/Output).

### Lógica de Gerenciamento de Subprocessos MCP (`src-tauri/src/mcp_host.rs`)

```rust
use tokio::process::{Command, Child};
use std::process::Stdio;
use tokio::io::{AsyncWriteExt, AsyncBufReadExt, BufReader};

pub struct McpServer {
    process: Child,
    name: String,
}

impl McpServer {
    // Função para iniciar um MCP (Ex: o Time MCP em Node.js ou um binário Go compilado)
    pub async fn start(name: &str, command: &str, args: &[&str]) -> Result<Self, String> {
        let mut child = Command::new(command)
            .args(args)
            .stdin(Stdio::piped())
            .stdout(Stdio::piped())
            .stderr(Stdio::piped()) // Capturar erros para não poluir o terminal principal
            .spawn()
            .map_err(|e| format!("Falha ao iniciar MCP {}: {}", name, e))?;

        println!("🟢 MCP Server '{}' iniciado com sucesso.", name);

        // Opcional: Monitorar o stderr em background para debugging
        if let Some(stderr) = child.stderr.take() {
            tokio::spawn(async move {
                let mut reader = BufReader::new(stderr);
                let mut line = String::new();
                while let Ok(bytes) = reader.read_line(&mut line).await {
                    if bytes == 0 { break; }
                    eprintln!("[MCP {} Erro]: {}", name, line.trim());
                    line.clear();
                }
            });
        }

        Ok(McpServer {
            process: child,
            name: name.to_string(),
        })
    }

    // Exemplo de como enviar um request JSON-RPC para o MCP
    pub async fn send_request(&mut self, payload: &str) -> Result<(), String> {
        if let Some(stdin) = self.process.stdin.as_mut() {
            let message = format!("{}\n", payload);
            stdin.write_all(message.as_bytes()).await.map_err(|e| e.to_string())?;
            Ok(())
        } else {
            Err("Stdin não disponível para o MCP".to_string())
        }
    }
}
```

### Acoplando no Motor SODA (Main.rs)

No seu `setup` do Tauri, você irá inicializar seus MCPs essenciais:

```rust
// Dentro do tauri::Builder::default().setup(|app| { ... })
tauri::async_runtime::spawn(async move {
    // 1. Iniciar o Time MCP (Garante que o Life Coach saiba o horário exato)
    // Supondo que o Time MCP foi empacotado via npx ou é um script local
    let mut time_mcp = McpServer::start(
        "Time_MCP", 
        "node", 
        &["../.agents/mcps/time-mcp/index.js"]
    ).await.expect("Falha no Time MCP");

    // 2. Iniciar Jcodemunch (Leitor de código ultra-rápido em Go)
    let mut code_mcp = McpServer::start(
        "JcodeMunch", 
        "../bin/jcodemunch", 
        &[]
    ).await.expect("Falha no Jcodemunch");

    // Mantenha as referências vivas ou coloque em um Arc<Mutex<>> no AppState
});
```

## 4. O Fluxo de Trabalho (O que o Motor faz quando você digita)

Quando você digita _"O que eu fiz no projeto SODA ontem?"_ no Canvas:

1. **Frontend:** Chama `ask_agent`.
2. **Event Loop (Rust):** Recebe o prompt.
3. **Time MCP (Rust chama o Node):** Pede a data de hoje. (O Rust injeta silenciosamente: _"Hoje é 14/08/2026"_).
4. **SQLite (Hippocampus):** O Rust faz um query no FTS5 buscando _"projeto SODA"_. Encontra os `episodes` de ontem.
5. **Toon Context (Compressão):** O Rust pega os 10 episódios encontrados, minifica o JSON, remove stop-words e compacta (Poupando VRAM).
6. **Llama.cpp (LLM Local):** O Rust envia o prompt comprimido + contexto para o modelo carregado na VRAM.
7. **Tauri Emitter:** Conforme o modelo cospe os tokens, o Rust dispara `app_handle.emit("agent-thought", ...)`.
8. **Frontend:** Renderiza o texto fluindo no seu Canvas React.

Este é o Motor V8 do seu ambiente agêntico. Zero APIs de nuvem. Totalmente em sua posse.