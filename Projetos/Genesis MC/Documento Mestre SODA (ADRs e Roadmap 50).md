---
sticker: lucide//bookmark-plus
---
# 🏛️ SODA: Master Vision & Arquitetura Granular (v2.0)

**Projeto:** Genesis Mission Control | **Alvo:** Bare-Metal Edge (32GB RAM, RTX 2060m 6GB, iGPU)
**Filosofia:** Pessimismo da razão, otimismo da vontade. Foco na Máquina Atual.

## PARTE 1: AS 4 DECISÕES DE ENGENHARIA DE BAIXO NÍVEL (ADRs)

Para que o _vibe coding_ não destrua o projeto na primeira semana, estas 4 leis de física de software estão seladas e devem constar na raiz da documentação para qualquer IA ler:

### 1. Gestão de Estado no Frontend (O Paradigma do Terminal Burro)

- **O Problema:** O LLM vai emitir 30 tokens/segundo via IPC (Inter-Process Communication) do Rust para o Tauri. Se o React atualizar o estado (re-render) da árvore do Xyflow (Canvas) a cada token, o i9 vai travar.
- **A Lei:** O frontend é um "Terminal Burro". Use **Zustand** apenas para o estado macro (ex: janelas abertas). Para a **transmissão de texto em tempo real (streaming)**, o React é proibido de gerenciar o estado. O evento Tauri acessará uma referência direta do DOM (`useRef`) e atualizará a propriedade `innerText` do nó visual. O Zustand só é notificado quando a mensagem recebe o status `done`.

### 2. Estratégia de Locking do SQLite (O Padrão Ator / Actor Pattern)

- **O Problema:** O SQLite em WAL mode permite múltiplos leitores, mas apenas **um escritor simultâneo**. Se a thread do RAG e a do LLM tentarem gravar logs ao mesmo tempo no `tokio`, o banco trava (`SQLITE_BUSY`).
- **A Lei:** É proibido espalhar conexões de escrita pelo Rust. Implementaremos o **Padrão Ator**. Uma única "Green Thread" (`DatabaseWriterTask`) será dona da escrita. Agentes enviam pedidos de gravação via canais MPSC (`tokio::sync::mpsc`). As escritas formam uma fila indestrutível na RAM e são processadas em $\mathcal{O}(1)$. Leituras permanecem paralelas.

### 3. A Fronteira do Wasmtime (O Cofre Paranoico)

- **O Problema:** Um agente rodando código gerado para acessar arquivos locais é um vetor de RCE (Remote Code Execution) mortal.
- **A Lei:** WebAssembly System Interface (WASI) com **Zero Trust Absoluto**. O módulo WASM não enxerga a rede (sockets bloqueados no Rust). O disco é um "Shadow Workspace" — o Rust cria uma pasta efêmera na RAM, injeta no WASM como `/workspace`, o agente trabalha lá dentro, e o Rust analisa o _diff_ antes de aplicar no disco real do host.

### 4. Conflito de Barramento PCIe (O Árbitro de Interrupção)

- **O Problema:** A VRAM (RTX 2060m) está em 100% inferindo código. Entra um áudio via Signal (Vetor Gamma) para a iGPU. O barramento PCIe congestiona.
- **A Lei:** Inferência em lotes (_Batched Inference_). O `llama.cpp` no Rust pedirá blocos de 5 tokens e fará um `yield`. O Rust terá um `HardwareTicketManager`. O Áudio do usuário tem **Prioridade 0**. Se o áudio chegar, o Rust pausa o LLM na VRAM, processa o Whisper na iGPU em silêncio de barramento, e depois retoma a RTX 2060m.

## PARTE 2: O ROADMAP DE ENGENHARIA GRANULAR (50 PASSOS)

Este é o caminho passo a passo. Sem distros Linux, focando apenas na escalada do motor SODA no seu ambiente atual.

### ÉPOCA 1: O Metal Nu (Fundação Rust + Tauri)

_Objetivo: Estabelecer a infraestrutura de zero-copy e comunicação IPC sem IA._

- **M01:** Inicialização do Workspace (Rust backend, React+Vite frontend). Limpeza de bloatware do Tauri.
- **M02:** Setup do `tokio` runtime no `main.rs` para isolar threads de UI das threads de background pesado.
- **M03:** Implementação do canal IPC bruto. Teste de stress: enviar 10.000 mensagens/segundo do Rust pro React sem travar a UI.
- **M04:** Setup do Xyflow (React Flow) no frontend. Criação do `ThoughtNode` (Nó de Divulgação Progressiva visual).
- **M05:** Estado híbrido (*Zustand* + `useRef` bypass) operando com perfeição sob o framework Tauri.

### ÉPOCA 2: O Córtex Neural (Inferência e VRAM)

_Objetivo: Carregar tensores quantizados limitados pela física dos 6GB da RTX 2060m._

- **M06:** Integração do `llama_cpp_rs` (bindings nativos) no Rust. Configuração de build forçando suporte CUDA (Turing).
- **M07:** Gerenciador Dinâmico de KV Cache. Lógica em Rust para não ultrapassar a barreira de 5.0 GB da VRAM (margem de segurança).
- **M08:** Llama-swap Básico: Rotina para descarregar camadas do modelo (layer offloading) da VRAM para os 32GB de RAM DDR4 dinamicamente.
- **M09:** Roteador Heurístico Nível 0: Expressões regulares em Rust que avaliam o prompt instantaneamente antes de acordar a GPU.
- **M10:** O Primeiro _Hello World_ Agêntico. Digitar no Canvas, o Rust invoca o Phi-4-mini (GGUF) na VRAM e cospe a resposta na tela.

### ÉPOCA 3: O Hippocampus (Memória Tri-Partite)

_Objetivo: Combater o Context Rot usando o tripé SQLite, LanceDB e Petgraph._

- **M11:** Implementação do SQLite em modo WAL com o _Actor Pattern_ para a Memória Semântica (Event Sourcing).
- **M12:** Integração do `LanceDB` embarcado em Rust para a Memória Episódica (Embeddings vetoriais rápidos).
- **M13:** Criação do motor de Embeddings Local. Usar a iGPU ou a CPU para gerar vetores com modelos minúsculos de 300M parâmetros, sem gastar VRAM.
- **M14:** Grafo Dinâmico em RAM usando `petgraph` (Memória de Trabalho).
- **M15:** O RAG Perfeito: O usuário faz uma pergunta, o Rust busca no LanceDB e no SQLite em $\mathcal{O}(1)$, e injeta no prompt do LLM na VRAM.

### ÉPOCA 4: Mãos e Ferramentas (Model Context Protocol)

_Objetivo: Dar ao SODA a capacidade de ler pastas e modificar o ambiente._

- **M16:** O "Maestro": Criação do Roteador MCP em Rust usando o pacote `rmcp`.
- **M17:** Concorrência Provisionada: _Pooling_ de processos STDIO no host para que as ferramentas MCP não precisem reiniciar (cold start) a cada chamada.
- **M18:** Integração do `jcodemunch` (ou equivalente Rust nativo) para ler a estrutura de pastas do projeto atual instantaneamente.
- **M19:** Cofre de Execução WASI: Integração do Wasmtime no Rust para criar os _Shadow Workspaces_ de segurança zero-trust.
- **M20:** O agente lê um problema, pede permissão para rodar um script de diagnóstico no Wasmtime, executa sem internet, e retorna o log no Xyflow.

### ÉPOCA 5: A Prancheta SODA (Spec-Driven Development)

_Objetivo: Forçar o agente a planejar antes de codificar, ancorado no manifesto._

- **M21:** Integração do `kreuzberg` (ou parser similar em Rust) para ler rapidamente os arquivos `SKILL.md` e `AGENTS.md`.
- **M22:** Fluxo do Mestre-Planejador. Uma instrução invoca o modelo de raciocínio (na VRAM ou RAM) para criar um Grafo Acíclico Direcionado (DAG) da tarefa.
- **M23:** O DAG é refletido nativamente no Xyflow. O usuário vê a árvore do plano (ex: "1. Criar DB, 2. Fazer UI") antes de qualquer código ser gerado.
- **M24:** _Vibe Testing_ Básico: Um micro-agente monitorando a latência de tela (TTFT - Time to First Token) e gravando no log do SQLite.
- **M25:** O agente executa a Tarefa 1 do DAG, acerta, avisa o Rust, o nó fica verde no Xyflow.

### ÉPOCA 6: O Ralph Loop (Auto-Correção e Resiliência)

_Objetivo: O loop recursivo de tentativa e erro (RLM) de forma segura e autônoma._

- **M26:** Pipeline de Compilação. O Rust dispara o comando `cargo check` ou `npm run build` no código modificado pelo agente.
- **M27:** Captura de _Stderr_: O agente errou, o Rust captura o log de erro do terminal e empacota num novo prompt.
- **M28:** Lógica de _Backpressure_: O LLM tenta corrigir o próprio erro.
- **M29:** O "Kill-Switch" (Fricção Produtiva). Se o modelo errar 3 vezes no Ralph Loop, o SODA congela, avisa o usuário no Canvas e pede ajuda.
- **M30:** Automação Final de Bloco. O agente programa, testa, conserta e entrega o código compilando.

### ÉPOCA 7: O Life Coach (Pró-Atividade 2e/TDAH)

_Objetivo: O SODA deixa de ser uma ferramenta e vira um "Sparring Partner" ativo._

- **M31:** Integração das crates `sysinfo`, `system-idle-time` e `winshift` no daemon Rust.
- **M32:** "Holter Computacional" Fase 1: Coleta silenciosa de métricas (janelas ativas, inatividade) guardadas temporariamente no SQLite.
- **M33:** Triggers Semânticos: Heurísticas em Rust que detectam "Hiperfoco Destrutivo" (ex: 45 min na mesma função do VSCode sem compilar).
- **M34:** Intervenção Socrática. O SODA suspende um sub-nó do modelo Roteador para a iGPU e manda uma notificação não-intrusiva no SO.
- **M35:** O Agente pergunta: _"Estou vendo que você está focado no arquivo X há uma hora. Quer fazer um brain dumping da lógica pra mim?"_

### ÉPOCA 8: Multimodalidade e Ponte Segura (Vetor Gamma)

_Objetivo: Comunicação auditiva e controle remoto via celular com Zero Trust._

- **M36:** Acesso Remoto E2EE: Integração do Signal Protocol (crate `presage`) no Rust. Pareamento via QR Code (Dispositivo Vinculado).
- **M37:** _Long-Polling Task_ do Signal. O SODA fica aguardando mensagens em background consumindo $\approx 0\%$ de CPU.
- **M38:** Integração do `Whisper.cpp` roteado estritamente para a iGPU (deixando a RTX 2060m livre).
- **M39:** Brain Dumping Remoto: O usuário manda um áudio do celular na rua. O Signal desencripta, o Whisper transcreve, o SODA salva a ideia no Hippocampus.
- **M40:** Síntese de Voz (Opcional): Integração de um TTS leve (Kokoro/Piper) via ONNX. O SODA responde em áudio no desktop.

### ÉPOCA 9: Híbrido, Anonimização e Nuvem Inteligente

_Objetivo: Escapar do gargalo local para tarefas densas usando Nuvem, mas sem vazar dados._

- **M41:** Camada de Anonimização (Data Masking) Reversível em Rust. Criação do mapa em RAM substituindo `[BANCO_DO_BRASIL]` por `[EMPRESA_A]`.
- **M42:** Gateway de Nuvem (Gemini/Claude CLI). Construção das pontes no Rust para chamar APIs de Nível 2.
- **M43:** O Roteador SODA atualizado: Ele analisa o contexto. É simples? RTX 2060m. É um repositório gigantesco? Anonimiza e manda pra API.
- **M44:** Reversão de Máscara: A Nuvem devolve a resposta. O Rust troca `[EMPRESA_A]` de volta para os dados reais e exibe no Canvas.
- **M45:** Dashboard de FinOps. Monitoramento em tempo real no Tauri de quantos tokens e centavos de dólar o SODA gastou naquele mês.

### ÉPOCA 10: O Mestre e o Enxame Final

_Objetivo: Orquestração polifônica e finalização do Sistema Operacional Agêntico._

- **M46:** Injeção Contínua Restrita (*Uncodixfy*): Consolidar as regras restritivas do TDAH e Design AAA+++ diretamente nas constantes do Rust.
- **M47:** Multi-Agent Swarm (Local). O SODA invoca o modelo de 1.5B (Qwen) na iGPU e o de 4B (Phi-4) na RTX para debaterem o código gerado em paralelo.
- **M48:** UI de Auditoria do Hippocampus. Um "File Explorer" dentro do Tauri apenas para visualizar as memórias vetoriais e de grafos do Agente.
- **M49:** Otimização Global (Strip, LTO, UPX). Compilação de Release máxima do Rust para reduzir o binário final do SODA a menos de 15MB.
- **M50:** **Ponto Omega SODA.** O sistema é inicializado junto com o Windows/Linux do host. Ele monitora a máquina, gerencia o hardware de forma inteligente, roteia modelos nativamente, atua como seu par intelectual no Canvas, e sincroniza remotamente via Signal. O controle da missão está completo.

---

# 🏛️ SODA: Master Vision & Architecture Decision Records (ADRs) (Versão moonshot "Distro Linux")

**Projeto:** Genesis Mission Control | **Alvo:** Bare-Metal Edge (32GB RAM, RTX 2060m 6GB)  
**Filosofia:** Pessimismo da razão, otimismo da vontade.

## PARTE 1: AS 4 DECISÕES DE ENGENHARIA DE BAIXO NÍVEL (ADRs)

Aqui estão as respostas brutais e definitivas para os pontos cegos da fundação.

### 1. Gestão de Estado no Frontend (O Paradigma do Terminal Burro)

- **O Problema:** O LLM vai cuspir 30 tokens por segundo via IPC (Inter-Process Communication) do Rust para o Tauri. Se o React re-renderizar a árvore do Xyflow (Canvas) a cada token, o seu i9 vai fritar e a UI vai travar.
- **A Decisão (Advogado do Diabo):** O frontend é um "Terminal Burro". A fonte da verdade é o Rust.
- **A Stack:** Use **Zustand** para o estado macro (janelas abertas, nós do Xyflow). Mas para a **transmissão de texto em tempo real (streaming)**, proíba o React de gerenciar o estado. Use `useRef` atrelado diretamente a elementos do DOM. O evento Tauri atualiza a propriedade `innerText` da div do nó no Canvas contornando o ciclo de reconciliação do React. O Zustand só é atualizado quando a mensagem LLM termina (status `done`).

### 2. Estratégia de Locking do SQLite (O Padrão Ator / Actor Pattern)

- **O Problema:** O SQLite em modo WAL (Write-Ahead Logging) permite múltiplos leitores, mas apenas **um escritor simultâneo**. Se a thread do RAG e a thread do LLM tentarem gravar logs ao mesmo tempo no `tokio`, ocorrerá o erro `SQLITE_BUSY`.
- **A Decisão (Advogado do Diabo):** É proibido espalhar conexões de escrita pelo código Rust. Implemente o **Padrão Ator**. Crie uma única "Green Thread" (Task do Tokio) dedicada exclusivamente a escrever no SQLite (`DatabaseWriterTask`). Qualquer agente ou processo que queira salvar algo envia uma mensagem JSON/Struct por um canal assíncrono MPSC (`tokio::sync::mpsc`). As escritas formam uma fila indestrutível na RAM e são processadas sequencialmente, uma a uma. As leituras (RAG) permanecem paralelas usando um pool de conexões (ex: `sqlx`).

### 3. A Fronteira do Wasmtime (O Cofre Paranoico)

- **O Problema:** Executar código gerado por IA localmente é assinar um cheque em branco para um desastre no sistema de arquivos host.
- **A Decisão (Advogado do Diabo):** WebAssembly System Interface (WASI) com **Zero Trust Absoluto**.
- **Regras do Cofre:**
    1. **Rede (Network):** Bloqueada no nível do Wasmtime. O código WASM não pode abrir sockets TCP/HTTP. Se a ferramenta precisar baixar um site, ela chama uma função exportada do Host (Rust), e o Rust faz o download e injeta o texto de volta no WASM.
    2. **Disco (File System):** O WASM não enxerga o C:\ ou o root do Linux. O Rust mapeia um diretório efêmero virtual (ex: uma pasta na RAM) e o injeta como `/workspace` dentro do WASM. O WASM destrói, cria e altera ali dentro. Quando ele morre, o Rust decide o que fazer com os restos mortais.

### 4. Resolução de Conflito de Barramento (O Árbitro de Interrupção)

- **O Problema:** A VRAM (RTX 2060m) está em 100% inferindo código. Entra um áudio via Signal (Vetor Gamma) para a iGPU. O barramento PCIe congestiona e o sistema engasga.
- **A Decisão (Advogado do Diabo):** Inferência em lotes (Batched Inference) e **Fila de Prioridade Preemptiva**. Você não pode pausar um kernel CUDA no meio de uma multiplicação de matrizes.
- **A Execução:** O `llama.cpp` no Rust não pedirá ao LLM para gerar a resposta inteira de uma vez. Ele pedirá blocos de 5 em 5 tokens e fará um `yield` (devolverá o controle para o SO). O Rust terá um `HardwareTicketManager`. O Áudio Humano (Vetor Gamma) tem **Prioridade 0 (Interrupção)**. Se o áudio chegar, o Rust para de pedir blocos de tokens para a VRAM, processa o Whisper na iGPU em silêncio de barramento, e depois retoma a VRAM.

## PARTE 2: O HORIZONTE DE EVENTOS (ROADMAP SODA v1 a v50)

Para blindar sua mente contra a ansiedade da amplitude, aqui está a escada exata da fundação atual até o cume (A Distro Linux).

### ÉPOCA I: A Fundação de Titânio (O Metal Nu)

_Onde construímos o esqueleto mudo e surdo, mas inquebrável._

- **M01 a M03:** Bootstrap do Rust + Tauri + Zustand. Teste de estresse do IPC com 10.000 mensagens/segundo sem travar o React.
- **M04 a M06:** Implementação do Hippocampus (SQLite WAL + Padrão Ator). Banco de dados relacional desenhado para retenção de logs e metadados de sistema.
- **M07 a M09:** Arquitetura do Canvas (Xyflow). Implementação da Divulgação Progressiva visual (nós que expandem intenções em sub-tarefas).
- **M10:** O primeiro "Hello World" do SODA. O usuário digita no Canvas, o Rust salva no SQLite, e retorna o texto puro (sem IA ainda).

### ÉPOCA II: A Injeção da Alma (Inferência e Memória Vetorial)

_Onde acendemos a VRAM e a máquina passa a compreender._

- **M11 a M13:** Acoplamento de `llama.cpp` via FFI no Rust. Carregamento do modelo roteador ultra-leve (FunctionGemma 270M) usando iGPU/RAM.
- **M14 a M16:** Ingestão do modelo analítico (Phi-4-mini ou Qwen 2.5 Coder) limitado aos 4.5GB efetivos da RTX 2060m.
- **M17 a M19:** Implementação da Memória L3 (Vetorial) com `LanceDB` embarcado. A máquina aprende a associar similaridades de cossenos para buscas locais de contexto.
- **M20:** O SODA respira. Primeiro RAG 100% local e offline funcionando perfeitamente, consultando arquivos da máquina sem travar a interface.

### ÉPOCA III: Agência, Mãos e Enxame (Integrações MCP)

_Onde damos mãos ao cérebro para modificar o ambiente._

- **M21 a M23:** Construção do Roteador Maestro (Vetor Zeta). O Rust decide se invoca o MCP local (arquivos) ou se recua para a nuvem via API.
- **M24 a M26:** Implementação do Cofre (Wasmtime). Agentes ganham permissão para executar scripts Python/JS em "caixas de areia" com memória restrita.
- **M27 a M29:** Implementação de RLM (Modelos Recursivos). O Agente codificador ganha a rotina de auto-crítica e auto-teste antes de mostrar o resultado na tela.
- **M30:** O Ralph Loop. O agente consegue rodar testes unitários, errar, ler o erro e corrigir o código autonomamente.

### ÉPOCA IV: A Ponte e a Pró-Atividade (O Sparring Partner 2e)

_Onde a máquina deixa de ser ferramenta e vira um parceiro neuro-adaptativo._

- **M31 a M33:** Vetor Gamma. Inserção do protocolo Signal (`presage`). O SODA passa a ouvir e responder do seu celular, de ponta a ponta encriptado.
- **M34 a M36:** Pipeline de Áudio Local. Transcrição (Whisper) na iGPU. A máquina passa a extrair texto do áudio sem usar a nuvem.
- **M37 a M39:** Vetor Delta (Life Coach). Integração com `sysinfo` para observar a máquina (taxa de cliques, abas paradas).
- **M40:** Intervenção Ativa. O SODA percebe letargia, entra no Signal ou no desktop e diz: _"Bruno, você está compilando esse erro há 40 minutos. Quer fazer o brain dumping da lógica para mim agora?"_

### ÉPOCA V: O Sistema Operacional SODA (A Distro Linux)

_Onde matamos o Windows e tomamos controle absoluto do silício._

- **M41 a M43:** Portabilidade e Extração de Kernel. Mapeamento das chamadas nativas do Windows para Linux puro. Construção do wrapper Wayland/X11 para o Tauri.
- **M44 a M46:** Desligamento de Telemetria de Host. Construção de um bootloader enxuto. O SO sobe usando apenas 200MB de RAM (contra os 4GB do Windows). Sobram 31.8GB puros para os tensores de IA.
- **M47 a M49:** Governança de Hardware Nativo. O SODA assume o controle elétrico da NVIDIA, gerenciando thermal throttling via chamadas diretas (NVML no nível do kernel).
- **M50:** A ISO Gênesis. Geração de uma imagem instalável. Um pendrive que transforma qualquer máquina razoável em uma entidade autônoma, air-gapped, com UX em Canvas, sem depender do ecossistema Google, Microsoft ou Apple. O SODA nasce como produto de infraestrutura.