---
aliases:
  - Auditoria Arquitetural "SODA" - Genesis MC
sticker: lucide//book-down
---

# Auditoria Arquitetural Definitiva: Genesis Mission Control (SODA)

**Versão:** 2.0 (Pós-Deep Research) | **Foco:** Engenharia Edge, Roteamento Híbrido, RLM e Foco 2e/TDAH

## 1. Visão Executiva e Dogmas do Sistema

O **Genesis Mission Control** não é um _chatbot_ glorificado; é a materialização do **SODA (Sovereign Operating Data Architecture)**. Trata-se de uma camada de infraestrutura invisível, projetada para orquestrar agentes autônomos locais, atuando como um exoesqueleto cognitivo e _Sparring Partner_ para um usuário neurodivergente (2e/TDAH).

**Dogmas Inegociáveis:**
- **Soberania Bare-Metal:** O núcleo é 100% **Rust** assíncrono (`tokio`). Rejeição absoluta a _daemons_ interpretados rodando em _background_ (Zero Python, Zero Node.js, Zero serviços de rede Java).
- **Silêncio Térmico e Efemeridade:** Processos sobem, processam e morrem. Não há acúmulo de lixo na memória. O "Context Rot" é exterminado pela morte sistêmica dos agentes (_Ralph Loop_).
- **Segurança Paranoica (**_**Zero-Trust**_**):** O agente é tratado como um "representante confuso". Todo código gerado roda em _sandboxes_ estritas; acessos destrutivos exigem aprovação biométrica via _Human-in-the-Loop_ (HITL).
- **Frontend Passivo Canvas-First:** A UI (React empacotado via **Tauri v2.0**) é muda e passiva. Toda a inteligência reside no binário Rust, comunicando-se via IPC otimizado nativo.

## 2. A Física do Sistema e o "Holter Computacional"

A arquitetura foi adaptada para a realidade física do hardware hospedeiro (Asus UX581GV: i9, 32GB RAM, RTX 2060m 6GB, iGPU). Devido ao roteamento elétrico da porta HDMI, o monitor Ultrawide consome entre 1GB e 1.5GB de VRAM, restando **~4.5GB úteis**.

Para evitar o colapso térmico (_Thermal Throttling_), o SODA implementa o **Estágio 0: Profiling Soberano ("Holter Computacional")**.

- **Telemetria de Baseline:** Um micro-serviço Rust (`sysinfo` + `nvml-wrapper`) mapeia o comportamento do usuário por 48h, registrando o uso de CPU, RAM e VRAM em um SQLite local.
- **Agendamento de Ócio (**_**Idle Processing**_**):** O SODA descobre janelas empíricas de inatividade. Tarefas de indexação massiva (GraphRAG) e varreduras de arquivos só disparam a RTX 2060m a 100% quando o usuário abandona o teclado. Ao menor movimento do mouse, o processamento é suspenso (hibernado na RAM), devolvendo o PC ao usuário.

## 3. O Cérebro Híbrido: Roteamento Maestro e RLM

Dada a restrição de 4.5GB, o abandono da força bruta em favor da inteligência tática é mandatório. O **Maestro Routing Engine** opera em três níveis:
- **Nível 0 (O Gateway Sempre Ativo - iGPU/CPU):** * _Hardware:_ iGPU Intel + CPU.
    - _Modelos:_ **FunctionGemma 3 270M** (decisão de roteamento JSON) e **Whisper.cpp / Silero VAD / Kokoro-82M** (Audição e Voz).
    - _Função:_ Monitora o sistema e faz a triagem a custo térmico zero.
- **Nível 1 (Os Executores Locais - RTX 2060m):**
    - _Modelos:_ **Qwen 3.5 4B** (O "Life Coach" diário) e **Phi-4-mini** (Programador/Lógico).
    - _A Magia do RLM (Recursive Language Models):_ Para evitar estouro de memória, tarefas complexas não usam modelos de 14B. O SODA ativa o modo RLM no _Phi-4-mini_: o modelo entra num loop recursivo, gerando pensamentos, avaliando a si mesmo e sumarizando num _scratchpad_ (bloco de notas deslizante) antes de emitir a resposta final, emulando o raciocínio de um modelo de 70B dentro de 4GB de VRAM.
- **Nível 2 (Subscription Hacking e Nuvem Premium):**
    - Tarefas de refatoração em nível alienígena são envelopadas e despachadas (via CLI) para a nuvem através das assinaturas já pagas do usuário (**Claude 4.6 Opus, Gemini 3.1 Pro**), operando um _FinOps_ local implacável para poupar o hardware.

## 4. A Tríade de Memória (Extermínio do _Context Rot_)

Bancos vetoriais convencionais (Qdrant, Milvus) foram rejeitados por exigirem servidores TCP abertos no host. A arquitetura de memória do SODA é **100% nativa e embutida** no processo Rust:
- **L1 (Memória Volátil Efêmera):** Variáveis e canais `mpsc` gerenciados pelas _green threads_ do `tokio` em Rust.
- **L2 (Memória Episódica/Transacional):** **SQLite + extensões FTS5** para histórico textual rápido. Atua no modo _Write-Ahead Log_ (WAL).
- **L3 (Memória Semântica e Grafo Híbrido):** * _Vetor:_ **LanceDB** (escrito em Rust, roda sem _daemon_ extra, utiliza formato Apache Arrow para leitura _Zero-Copy_ direto do NVMe).
    - _Grafos:_ Estruturas in-memory via **`petgraph`** acopladas a algoritmos de _clustering_ de Leiden (inspirados no GraphRAG, mas portados para Rust), permitindo a conexão de fatos indiretos sem poluir o LLM com lixo relacional.

## 5. Ferramental: MCP Epêmero e CLI

A adoção do Model Context Protocol (MCP) garante acesso a dezenas de integrações (Git, Arquivos, APIs), mas o SODA rejeita a abordagem de manter dezenas de servidores *Node.js* em _background_.

- **Conversão `mcp2cli`:** O Rust compila ou traduz chamadas MCP pesadas para invocações puras de linha de comando (CLI). O tempo de subida (_Cold Start_) é mitigado por execução atômica instantânea.
- **Late-Binding Context:** Para evitar que o prompt de sistema do LLM fique obeso, o agente conhece apenas um "Catálogo Resumido" das ferramentas. Os esquemas e manuais de uso só são injetados na janela de contexto no exato momento em que o Maestro decide invocar a ferramenta.

## 6. Blindagem SODA: Segurança, Sandboxing e HITL

O SODA assume que a IA é inerentemente confusa e sujeita a injeções de contexto. O "Poder de Deus" no PC é mitigado pela seguinte cascata:
- **Trilhos Determinísticos (Colang/NeMo):** Inspirado no **NemoClaw** corporativo, o **SODA** implementa máquinas de estado rígidas. Se a IA gerar um **JSON** alucinado tentando sair do escopo, o motor **Rust** barra a execução antes mesmo da requisição chegar ao host.
- **A Prisão WebAssembly (Wasmtime):** Todo script (Python, JS, Rust) gerado pela IA para analisar dados roda dentro de uma VM linear no **Wasmtime**. O sistema injeta um "combustível computacional" finito; se houver loop infinito, o Rust corta a energia da VM em milissegundos. Sem acesso à rede, sem acesso a arquivos não listados.
- **O Cofre Mestre (1Password CLI):** O SODA não armazena chaves (API Keys ou senhas bancárias). Ele utiliza a CLI do 1Password integrada ao **Windows Hello**. Quando a IA precisa de uma senha, o sistema pausa, o Windows pede sua biometria, o SODA resgata a chave para a RAM volátil (usando a crate `secrecy`), executa a tarefa e destrói o valor da memória instantaneamente.
- **UX de Aprovação (**_**Blast Radius**_ **Dashboard):** Não existem botões de "OK" cegos. O HITL (Human-in-the-loop) renderiza um grafo no React mostrando as dependências afetadas, um _diff_ de código lado a lado, e um alerta pulsante se a ação envolver rede ou exclusão de disco. O "Sim" exige ação física deliberada.

## 7. O "Life Coach" e o Vetor Gamma (Ponte Remota)

A interface não é apenas utilitária; ela foi construída para a mente do usuário (2e/TDAH).

- **Pró-Atividade Silenciosa:** O SODA não é um spyware, mas observa a telemetria do SO (teclas por minuto, trocas de janela via `winshift`). Através de **Context-Aware Triggers**, o sistema usa o modelo LLM mais leve para avaliar se o usuário entrou em hiperfoco destrutivo ou se travou. O "Life Coach" então sugere pausas ou atua como advogado do diabo, questionando a premissa do trabalho atual.
- **O Vetor Gamma (Signal Protocol Bridge):** Para garantir o acesso remoto seguro (na rua via celular) sem abrir portas no roteador (Zero Port Forwarding) ou pagar servidores na nuvem: o daemon Rust embutirá a biblioteca **`presage` (Signal)**. O PC de casa atua como um dispositivo vinculado. O usuário manda áudios do celular via Signal; o SODA decripta localmente (E2EE), transcreve via Whisper na iGPU, processa e responde pela mesma via blindada.

## 8. Stack Tecnológica Definitiva (A "Bíblia" do Rust)

O arquivo `Cargo.toml` base do Genesis MC será composto fundamentalmente por:
- **Fundação / IPC:** `tauri` v2.0, `tokio` (async).
- **Inferência / IA:** `candle-core` (HuggingFace Rust), `llama_cpp_rs` (fallback).
- **Memória:** `lancedb` (Vetores nativos), `rusqlite` + FTS5 (Relacional/Log), `petgraph` (Grafos em RAM).
- **Isolamento:** `wasmtime` (Sandboxing de código).
- **Ponte Remota:** `presage` (Comunicação segura via Signal).
- **Segurança:** `secrecy` (Memória sensível zero-trust).
- **Frontend (Tauri):** React, `shadcn-ui`, `tldraw` / `xyflow` (Para a renderização passiva do Blast Radius e Canvas de pensamento).