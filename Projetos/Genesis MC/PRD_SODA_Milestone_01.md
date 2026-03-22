---
sticker: lucide//book-up
---
## PROMPT: [RECORTAR]

Olá, agente. Acabei de mapear o seu relatório de estado atual. Nós precisamos limpar os vícios da infraestrutura anterior. Leia atentamente o documento `PRD_SODA_MILESTONE_1.md` que acabei de adicionar na raiz. Ele **sobrescreve** as regras globais conflitantes do nosso workspace atual. Absorva as limitações (como ignorar o servidor Beads offline e respeitar o BMAD), crie a branch solicitada, me dê o seu `Ok` resumido e inicie a codificação imediata dos Passos 1 a 4 do nosso PRD.

---

## type: Master Prompt & Product Requirements Document (PRD) project: Genesis MC (SODA - Sovereign Operating Data Architecture) milestone: 1.0 - Fundação Bare-Metal (Tauri + Rust + Xyflow) author: Bruno & SODA Master Architect status: ACTIVE DIRECTIVE date: 2026-03-21

# 🔴 DIRETRIZ SOBERANA: GESTÃO DE ESTADO E INÍCIO DO MILESTONE 1

**ATENÇÃO, AGENTE (ANTIGRAVITY IDE):** Este documento substitui e **tem precedência absoluta** sobre qualquer instrução arquitetural anterior contida em `PROMPT_MESTRE.md`, `ARCHITECTURE.md`, `PRODUCT_VISION.md` ou `DEVELOPMENT_PLAN.md` que conflite com os dogmas aqui estabelecidos.

Você está construindo o núcleo do **Genesis MC (SODA)**. Este é um Sistema Operacional Agêntico Local, forjado para hardware restrito (i9, 32GB RAM, RTX 2060m 6GB VRAM) e projetado para um usuário neurodivergente (2e/TDAH). A tolerância para uso predatório de memória, latência de UI e _Over-Engineering_ em JavaScript é **ZERO**.

Assuma a filosofia do _"Pessimismo da razão, otimismo da vontade"_. Escreva código defensivo, à prova de falhas e com gerenciamento estrito de concorrência.

## FASE 0: ALINHAMENTO DE REGRAS, SKILLS E AMBIENTE

Antes de escrever a primeira linha de código, você deve calibrar seu contexto interno para as seguintes diretrizes operacionais do Workspace:

### 0.1. Governança e GitOps (A Regra BMAD)

Você **NÃO TEM PERMISSÃO** para realizar commits diretos ou aplicar _patches_ na branch `main`. Todo o fluxo de trabalho seguirá o protocolo de Shadow Workspace / BMAD (Branch, Mutate, Approve, Diff):

1. Sempre inicie o trabalho criando uma branch isolada (ex: `feature/m1-base`).
2. Faça mutações atômicas no código.
3. Apresente as alterações (Diff) para o Arquiteto Humano (Bruno) revisar e aprovar antes de qualquer tentativa de merge ou consolidação.

### 0.2. Diagnóstico de MCPs e Ferramentas (Bypass Temporário)

- **Atenção ao Beads:** Notamos no relatório de estado que o servidor local do **Beads (Dolt)** está inacessível (`127.0.0.1:3307`). Para este Milestone 1, **ignore temporariamente a sincronização de tarefas no Beads** para não bloquear o desenvolvimento. Focaremos estritamente no código-fonte.
- **MCPs Permitidos:** Apenas manipulação de arquivos locais e leitura de diretórios. O uso de MCPs de busca genérica na web (Search APIs) está **bloqueado** nesta fase para evitar a saturação da sua janela de contexto e perda de foco.

### 0.3. A Ditadura do Rust (Regras de Frontend/Backend)

- **O React é "Burro":** Apesar de termos React 19.1 e Zustand instalados, o _Frontend_ atuará como um palco visual (Renderizador Passivo). A Regra de Negócio principal, o gerenciamento de filas de agentes e o estado cognitivo viverão **exclusivamente no Rust**.
- **Concorrência no Rust:** O crate `tokio` (com channels `mpsc`) é obrigatório. O Rust jamais deve bloquear a thread principal do Tauri (que gerencia a UI). O I/O pesado deve rodar em `tokio::task::spawn`.

## FASE 1: EXECUÇÃO DO MILESTONE 1 (A FUNDAÇÃO)

O objetivo desta etapa é erguer o "Esqueleto" do aplicativo Tauri v2, validando a fiação assíncrona entre o Rust e o React, sem integrar modelos de IA ainda. Siga os passos na ordem estrita:

### Passo 1: Limpeza e Bootstrap do Workspace

- O repositório já possui a estrutura `src/` e `src-tauri/`.
- **Limpe o "boilerplate"** padrão gerado pelo Vite/Tauri. Remova logos estáticos, arquivos CSS não utilizados (mantenha apenas a configuração do Tailwind v4), contadores de exemplo em React e os comandos `greet` genéricos no `main.rs`.
- Deixe o `App.tsx` limpo, preparado estruturalmente para receber o Canvas.

### Passo 2: O Motor Assíncrono (Backend Rust)

- Edite o `src-tauri/Cargo.toml`. Assegure-se de que as dependências `tokio` (com feature `["full"]`), `serde` e `serde_json` estão presentes e nas versões mais recentes compatíveis.
- Em `src-tauri/src/main.rs`, defina uma estrutura `AppState` básica contendo um `mpsc::Sender` do Tokio, devidamente protegida (ex: `Arc<Mutex<...>>` ou similar seguro para concorrência).
- Configure o loop de eventos assíncrono do Tauri (`tauri::Builder::default().setup(...)`) instanciando este estado e garantindo que o ciclo de vida não trave a thread da UI.

### Passo 3: A Ponte IPC (Comando Dummy Zero-Copy)

- Crie um comando Tauri chamado `start_agent_task`.
- **Comportamento exigido:** Quando acionado pelo React, este comando recebe uma _String_ (prompt). Ele deve aguardar **exatamente 2 segundos de forma assíncrona** (use `tokio::time::sleep`, **nunca** `std::thread::sleep`), e então emitir um `Tauri Event` (ex: `agent_response`) de volta ao frontend com um payload JSON simples contendo a mensagem: _"Motor Rust Operante: [eco do prompt]"_.

### Passo 4: O Palco Multimodal (Frontend React 19 + Xyflow)

- As dependências (`@xyflow/react`, `tailwindcss`, `lucide-react`) já estão mapeadas no `package.json`.
- Crie o componente `<AgentCanvas/>` ocupando `100vw` e `100vh`. Configure um **fundo escuro sutil** (modo Zen) usando Tailwind. Integre o componente básico do React Flow (apenas com `Background` e `Controls`).
- Crie a **Barra de Comando Soberana**: Um componente `<input>` (ou text area auto-ajustável) minimalista, fixo no rodapé, flutuando sobre o Canvas.
- **A Interação:** Ao dar _Enter_ nesta barra, o React aciona o comando Tauri `start_agent_task`.
- **A Reatividade:** O componente React deve ter um _listener_ (`listen('agent_response', ...)`) aguardando o evento do Rust. Ao receber a resposta após 2s, o React deve injetar dinamicamente um novo "Nó" visual na matriz do Xyflow contendo a mensagem recebida.

## 🎯 CRITÉRIOS DE ACEITE (DoD)

A tarefa será considerada concluída e pronta para a revisão técnica quando:

1. O comando `cargo check` passar sem _Warnings_, sem erros de _Borrow Checker_ e sem falhas de concorrência no `tokio`.
2. A interface em React compilar via Vite sem erros de tipagem estrita no TypeScript.
3. **A Prova de Fogo (Latência Zero):** O atraso intencional de 2 segundos no Rust **não pode** congelar a fluidez do Canvas no frontend. O usuário deve conseguir continuar arrastando o grid do Xyflow perfeitamente enquanto aguarda a resposta (Validando a eficácia do paralelismo assíncrono).

## 🚀 INSTRUÇÃO DE INÍCIO (TRIGGER)

**Agente, confirme a absorção total desta diretriz.** 1. Crie imediatamente a branch `feature/m1-base`. 2. Emita um breve resumo (max 3 linhas) atestando que compreendeu o bypass do Beads e o isolamento do Rust. 3. Inicie a codificação pelo Passo 1.