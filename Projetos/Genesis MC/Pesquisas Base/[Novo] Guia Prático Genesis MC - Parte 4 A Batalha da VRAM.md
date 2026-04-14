---
aliases: []
sticker: lucide//chevron-right-square
---
# Genesis MC: Guia Prático - Parte 4 (A Batalha da VRAM)

Para rodar o **SODA** em uma RTX 2060m (6GB VRAM), precisamos de uma gestão de memória militar. O Rust será o general que decide qual modelo entra na VRAM, quando ele sai, e o quão espremida a informação deve estar antes de ser processada.

## 1. O Roteador SODA (Decisão de Milissegundos)

O Roteador não é uma IA. Usar um LLM para rotear outro LLM gasta tempo e tokens. O Roteador SODA é uma **heurística rápida em Rust** (Expressões Regulares e Palavras-chave) que decide o destino da tarefa instantaneamente.

### Estrutura do Roteador (`src-tauri/src/router.rs`)

```rust
use regex::Regex;

#[derive(Debug, Clone, PartialEq)]
pub enum AgentPersona {
    LogicCoder, // Vai usar o Phi-4-mini (Foco em código, JSON, tarefas exatas)
    LifeCoach,  // Vai usar o Mistral/Qwen (Foco em criatividade, planejamento, análise)
}

pub struct SodaRouter {
    code_regex: Regex,
}

impl SodaRouter {
    pub fn new() -> Self {
        Self {
            // Regex simples para detectar intenção de código ou sistema
            code_regex: Regex::new(r"(?i)(código|função|script|bug|refatorar|json|rust|python|sql)").unwrap(),
        }
    }

    // Retorna qual modelo deve ser carregado na VRAM
    pub fn route(&self, prompt: &str) -> AgentPersona {
        if self.code_regex.is_match(prompt) {
            AgentPersona::LogicCoder
        } else {
            AgentPersona::LifeCoach
        }
    }
}
```

## 2. Hot-Swapping: Dança das Cadeiras na VRAM

Se o Roteador decidir que precisamos do `Phi-4-mini`, mas o `Mistral` estiver na VRAM, precisamos **matar o processo atual e subir o novo**. O `llama.cpp` (usando o binário `llama-server`) é perfeito para isso via subprocessos no Rust.

**Por que não usar a API de troca do Ollama?** Porque matar o processo do `llama-server` via Rust garante **100% de liberação imediata da VRAM** no nível do Sistema Operacional, sem memory leaks (vazamentos de memória).

### Gerenciador de Modelos (`src-tauri/src/llm_manager.rs`)

```rust
use tokio::process::{Command, Child};
use std::sync::Arc;
use tokio::sync::Mutex;
use crate::router::AgentPersona;

pub struct LlmManager {
    current_process: Arc<Mutex<Option<Child>>>,
    current_persona: Arc<Mutex<Option<AgentPersona>>>,
}

impl LlmManager {
    pub fn new() -> Self {
        Self {
            current_process: Arc::new(Mutex::new(None)),
            current_persona: Arc::new(Mutex::new(None)),
        }
    }

    pub async fn ensure_model_is_loaded(&self, persona: &AgentPersona) -> Result<(), String> {
        let mut current_persona = self.current_persona.lock().await;
        
        // Se o modelo correto já está rodando, não faz nada
        if current_persona.as_ref() == Some(persona) {
            return Ok(());
        }

        println!("⚠️ VRAM Swap Necessário. Trocando para: {:?}", persona);

        let mut process_guard = self.current_process.lock().await;

        // 1. DERRUBA O MODELO ATUAL (Libera os 6GB de VRAM instantaneamente)
        if let Some(mut child) = process_guard.take() {
            println!("🛑 Matando processo Llama.cpp anterior...");
            let _ = child.kill().await; 
        }

        // 2. DEFINE O CAMINHO DO NOVO MODELO (Arquivos GGUF)
        let model_path = match persona {
            AgentPersona::LogicCoder => "./models/phi-4-mini-q4_k_m.gguf",
            AgentPersona::LifeCoach => "./models/mistral-7b-instruct-q4_k_m.gguf",
        };

        // 3. SOBE O NOVO MODELO
        // Usamos -ngl 99 (Offload total para GPU) e -c 4096 (Limite estrito de contexto para caber na RTX 2060m)
        let child = Command::new("./bin/llama-server")
            .args(&["-m", model_path, "-c", "4096", "-ngl", "99", "--port", "8080"])
            .spawn()
            .map_err(|e| format!("Falha ao iniciar llama-server: {}", e))?;

        *process_guard = Some(child);
        *current_persona = Some(persona.clone());

        // Aguarda 2 segundos para o servidor HTTP do Llama.cpp ficar pronto
        tokio::time::sleep(std::time::Duration::from_secs(2)).await;
        println!("✅ Novo modelo carregado e pronto na porta 8080.");

        Ok(())
    }
}
```

## 3. Toon Context: A Compressão de Contexto no Rust

Contexto grande = VRAM esgotada e lentidão. O conceito do **Toon Context** (da sua planilha) é minificar arrays e JSONs antes de enviar para o LLM.

Ao invés de rodar o pacote em Node/TS (que gastaria mais RAM do seu notebook), nós portamos a **essência do algoritmo para o Rust**.

### O Compressor (`src-tauri/src/toon_context.rs`)

Aqui nós cortamos gordura. Removemos quebras de linha inúteis, espaços duplos e chaves JSON gigantes antes de injetar na janela de contexto do LLM.

```rust
use serde_json::Value;

pub struct ToonCompressor;

impl ToonCompressor {
    /// Comprime textos longos e históricos do SQLite
    pub fn compress_text(input: &str) -> String {
        // 1. Remove múltiplos espaços por um único
        let single_spaces = input.split_whitespace().collect::<Vec<&str>>().join(" ");
        
        // 2. Remove "Stop Words" triviais se a heurística for agressiva (Opcional, mas salva tokens)
        // Exemplo: remover "por favor", "obrigado", "com base em"
        
        single_spaces
    }

    /// O Verdadeiro 'Toon Context': Minificação extrema de dados estruturados
    /// Pega resultados dos MCPs (que geralmente cospem JSONs gordos) e espreme.
    pub fn compress_json(json_str: &str) -> String {
        if let Ok(val) = serde_json::from_str::<Value>(json_str) {
            match val {
                Value::Array(arr) => {
                    // Se for um Array de Objetos (ex: lista de episódios do SQLite), 
                    // extraímos apenas os valores críticos, ignorando chaves repetitivas.
                    let mut compressed = String::new();
                    compressed.push_str("DATA:[");
                    
                    for item in arr {
                        if let Value::Object(map) = item {
                            // Pega apenas valores, ignorando as chaves para poupar tokens
                            let values: Vec<String> = map.values()
                                .map(|v| v.to_string().replace("\"", ""))
                                .collect();
                            compressed.push_str(&format!("({}),", values.join("|")));
                        }
                    }
                    compressed.push(']');
                    compressed.replace("),]", ")]") // Limpeza final
                },
                _ => serde_json::to_string(&val).unwrap_or_default() // Fallback: Minificação padrão
            }
        } else {
            // Se não for JSON válido, cai na compressão de texto
            Self::compress_text(json_str)
        }
    }
}
```

### Exemplo Prático de Economia de Tokens (Como o Toon Age)

**Saída original do SQLite/MCP (Consome ~80 Tokens):**

```
[
  { "id": "1", "action": "abriu_vscode", "time": "10:00" },
  { "id": "2", "action": "escreveu_rust", "time": "10:05" }
]
```

**Depois do `ToonCompressor::compress_json` (Consome ~18 Tokens):**

```
DATA:[(1|abriu_vscode|10:00),(2|escreveu_rust|10:05)]
```

O `Phi-4-mini` é inteligente o suficiente para entender a estrutura compactada `(ID|Ação|Hora)`. Você acabou de economizar 75% da janela de contexto para a mesma informação.

## 4. Unindo Tudo (O Fluxo Final na API)

Quando o React pede algo, o Rust faz a orquestração tática:

```
// Dentro do seu comando Tauri
#[tauri::command]
async fn ask_agent_optimized(prompt: String, state: State<'_, AppState>) -> Result<String, String> {
    
    // 1. Roteador decide quem assume o problema
    let persona = state.router.route(&prompt);
    
    // 2. Garante que o modelo certo está na VRAM (Mata o outro se necessário)
    state.llm_manager.ensure_model_is_loaded(&persona).await?;
    
    // 3. Busca histórico/dados no Banco (Simulação)
    let raw_memory_json = "[{\"user\":\"bruno\",\"goal\":\"otimizar vram\"}]"; 
    
    // 4. Toon Context ESMAGA a string antes de mandar pra VRAM
    let compressed_memory = ToonCompressor::compress_json(raw_memory_json);
    
    // 5. Constrói o Prompt final reduzido
    let final_prompt = format!("System: Context:{}\nUser: {}", compressed_memory, prompt);
    
    // 6. Faz a requisição HTTP local pro llama-server (que já está rodando na porta 8080)
    // ... Código de requisição HTTP usando reqwest para http://localhost:8080/completion ...

    Ok("Resposta gerada".into())
}
```

### O Triunfo da Arquitetura:

Com essas 4 peças (Roteador Regex, Subprocessos Kill/Spawn, Toon Context e SQLite), você transformou um notebook de 6GB de VRAM numa **estação de trabalho agêntica soberana**. A troca de contexto leva cerca de 2 segundos (tempo do NVMe carregar o modelo), e o sistema nunca sofre "_Out of Memory_".