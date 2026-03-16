# Protocolo de Pesquisa Profunda: Genesis MC (SODA)

_Instruções de Uso:_ Copie e cole o conteúdo integral de cada Bloco (incluindo o Contexto Mestre) em uma sessão LIMPA e NOVA de uma ferramenta de Deep Research (Gemini Advanced, ChatGPT Pro, etc).

## 🔍 PROMPT 1: VETOR ALPHA (Memória Vetorial/Grafos Embutida)

**[COPIE A PARTIR DAQUI]**

ATUE COMO UM ARQUITETO DE SISTEMAS MASTER E PESQUISADOR DE VANGUARDA (RUST/C++).

**CONTEXTO MESTRE (PROJETO GENESIS MC - SODA):**

Estou desenvolvendo o Genesis Mission Control, um Sistema Operacional Agêntico Soberano (SODA) rodando localmente num i9, 32GB RAM e RTX 2060m (6GB VRAM). Nossos valores inegociáveis:

1. "Bare-metal" e Soberania: O núcleo é escrito 100% em Rust (assíncrono via Tokio).
2. Repúdio a interpretadores: ZERO processos Python, Node.js ou Java rodando em background.
3. Silêncio Térmico e Memória: Desprezamos ferramentas que rodam como serviços de rede separados consumindo RAM contínua na máquina host.

**A MISSÃO DE PESQUISA (VETOR ALPHA - MEMÓRIA):**

Preciso definir a arquitetura da Memória Tri-Partide do agente (focada em evitar o "Context Rot"). Recusamos usar bancos vetoriais como Qdrant, Milvus ou Pinecone se eles exigirem rodar como um servidor/daemon à parte.

Realize uma pesquisa profunda no ecossistema atual (GitHub, papers, crates.io, fóruns) para encontrar o estado da arte em Bancos de Dados Vetoriais e de Grafos que sejam EMBUTÍVEIS (Embeddable) diretamente no binário Rust.

Busque e compare opções como: `sqlite-vec` (e seu ecossistema FTS5 acoplado), `USearch` (Unum), `LanceDB` (Rust native), ou outras soluções C/Rust que rodem na mesma thread/processo do host. Analise também como integrar lógicas de GraphRAG (como agrupamento de Leiden) nativamente no Rust sem depender de pipelines em Python.

**FORMATO DE SAÍDA EXIGIDO:**

Gere um documento Markdown detalhado contendo:

1. **Síntese Executiva:** A viabilidade de uma memória híbrida (Vetorial + Grafo) 100% embutida em Rust.
2. **Análise Comparativa de Soluções (Tabela):** Biblioteca, Linguagem Nativa, Suporte a Vetores/Grafos, Impacto na RAM, Nível de Maturidade.
3. **Mergulho Técnico:** Como a ferramenta vencedora se integra ao ecossistema Rust/Tokio.
4. **Resolução de Conflitos:** Como aplicar clustering de Grafos (estilo GraphRAG) sem usar Python.
5. **Recomendação Arquitetural Final:** O que eu devo usar no Genesis MC.

  **[FIM DA CÓPIA]**

## 🔍 PROMPT 2: VETOR BETA (Segurança, Cofres e Sandboxing)

**[COPIE A PARTIR DAQUI]**

ATUE COMO UM ESPECIALISTA EM CYBERSECURITY, SANDBOXING E UX DE INTERAÇÃO HUMANO-MÁQUINA.

**CONTEXTO MESTRE (PROJETO GENESIS MC - SODA):**

Estou desenvolvendo o Genesis Mission Control, um Sistema Operacional Agêntico Soberano (SODA) local (i9, 32GB RAM, núcleo em Rust, UI em React/Tauri). O sistema possui Agentes LLM que escrevem e executam código autonomamente. Nossos valores: Zero-Trust paranoico, execução estritamente local e repúdio a daemons interpretados (Python/Node).

**A MISSÃO DE PESQUISA (VETOR BETA - SEGURANÇA E HITL):**

Preciso definir como conceder poder real ao agente local sem comprometer meus arquivos ou vazar minhas senhas, além de criar uma UX de aprovação que não seja um simples e irritante "Pop-up de OK".

Pesquise profundamente as seguintes frentes de segurança acopladas ao Rust:

1. **Integração Segura de Cofres:** Como integrar o Rust/Tauri ao 1Password CLI usando biometria do SO (Windows Hello/TouchID) para que o agente peça um token temporário, mas nunca acesse a senha mestre.
2. **Sandboxing de Execução:** Como embutir o `Wasmtime` no Rust para executar código (JS/Python/Rust gerados pela IA) de forma hiper-isolada (sem acesso à rede/disco por padrão). Avalie também o uso de `Landlock` (Linux) ou equivalentes para restringir processos do host.
3. **UX de Human-in-the-Loop (HITL):** Pesquise papers ou soluções de design de interface que mostrem o "Blast Radius" (Raio de Explosão/Impacto) de uma ação da IA para o usuário aprovar de forma informada, em vez de cliques cegos.

**FORMATO DE SAÍDA EXIGIDO:**

Gere um documento Markdown contendo:

1. **Arquitetura Zero-Trust Proposta:** O fluxo de vida de um comando perigoso.
2. **Análise de Sandboxing:** Wasmtime vs. outras alternativas Rust-native.
3. **Integração com 1Password:** Guia técnico/teórico de como o binário Rust lida com autenticação via CLI sem armazenar segredos.
4. **Protótipo Conceitual de UX HITL:** Como a tela do Tauri deve apresentar o risco ao usuário antes da aprovação.

**[FIM DA CÓPIA]**

## 🔍 PROMPT 3: VETOR GAMMA (Acesso Remoto Seguro via Messengers)

**[COPIE A PARTIR DAQUI]**

ATUE COMO UM ENGENHEIRO DE REDES DISTRIBUÍDAS E CRIPTOGRAFIA (RUST).

**CONTEXTO MESTRE (PROJETO GENESIS MC - SODA):**

Desenvolvo o Genesis MC, um núcleo SODA (Sovereign Agentic OS) escrito em Rust. Ele roda 100% local num desktop. O projeto tem um foco especial em um usuário 2e (Dupla Excepcionalidade/TDAH) que atua com o agente como um "Life Coach" proativo.

**A MISSÃO DE PESQUISA (VETOR GAMMA - BRIDGE REMOTA):**

O SODA mora no desktop, mas preciso conversar com meu agente quando estiver na rua usando o celular. **Regra de ouro:** NÃO quero abrir portas locais no meu roteador para a internet aberta (Zero Ngrok/Port Forwarding) e NÃO quero um servidor na nuvem processando mensagens.

Pesquise a melhor forma de criar uma "Ponte" (Bridge) no daemon Rust para se comunicar comigo via mensageiros estabelecidos (Telegram, Signal ou Matrix).

O daemon Rust deve atuar como um "Cliente" ou fazer "Long Polling" seguro contra a API do mensageiro, trazendo o áudio/texto para o meu hardware processar, e devolvendo a resposta pelo mesmo canal, com criptografia ponta-a-ponta sempre que possível.

**FORMATO DE SAÍDA EXIGIDO:**

Gere um documento Markdown contendo:

1. **Análise Comparativa de Protocolos:** Telegram Bot API (Polling) vs. Signal Client (Rust) vs. Matrix (Rust SDK). Prós, contras e riscos de privacidade.
2. **Topologia de Rede Recomendada:** Como o Rust mantém a conexão ativa sem drenar recursos ou expor o IP.
3. **Viabilidade Multimodal:** Como a ponte escolhida lida com envio de áudios (voz) da rua para o desktop decodificar localmente.
4. **Decisão Arquitetural:** Qual protocolo/biblioteca Rust eu devo embarcar no Genesis MC.

**[FIM DA CÓPIA]**

## 🔍 PROMPT 4: VETOR DELTA (Pró-Atividade e Life Coach)

**[COPIE A PARTIR DAQUI]**

ATUE COMO UM ARQUITETO DE IA E PESQUISADOR DE COMPORTAMENTO AGÊNTICO E CIÊNCIA COGNITIVA.

**CONTEXTO MESTRE (PROJETO GENESIS MC - SODA):**

O Genesis MC é um ecossistema agêntico local em Rust. O usuário é um perfil 2e (Dupla Excepcionalidade - Altas Habilidades + TDAH). O objetivo principal da persona governante do SODA é ser um "Life Coach" e um "Sparring Partner" co-evolutivo. Ele não deve apenas responder prompts, mas ser PRÓ-ATIVO, questionar premissas e interromper hiperfocos destrutivos.

**A MISSÃO DE PESQUISA (VETOR DELTA - PRÓ-ATIVIDADE):**

Como programar a "iniciativa" em um agente local sem que ele se torne um spyware intrusivo? Pesquise padrões arquiteturais de IA proativa e monitoramento passivo de Sistema Operacional escrito em Rust.

1. Busque como o daemon Rust pode observar eventos do SO (troca de janelas, tempo de inatividade, uso excessivo de CPU de um jogo vs IDE) usando bibliotecas de baixo nível, de forma leve e com foco total em privacidade.
2. Pesquise arquiteturas de "Context-Aware Triggers": Como usar esses dados do SO para disparar um "pensamento de fundo" num LLM leve que decidirá SE deve enviar uma notificação provocativa ao usuário via UI (Tauri).
3. Busque heurísticas da psicologia do TDAH/2e para definir o limite entre "ajuda bem-vinda" e "interrupção irritante".

**FORMATO DE SAÍDA EXIGIDO:**

Gere um documento Markdown contendo:

1. **Arquitetura de Observabilidade OS:** Quais crates Rust usar para ler o contexto da máquina sem afetar a performance.
2. **O Motor de Pró-Atividade:** O diagrama de decisão (quando o agente observa, quando ele pensa, quando ele age/fala).
3. **Diretrizes Cognitivas (2e/TDAH):** Regras de ouro arquiteturais para desenhar um "Sparring Partner" que saiba a hora de intervir de forma humanizada.

**[FIM DA CÓPIA]**

## 🔍 PROMPT 5: VETOR EPSILON (Inferência e Multimodalidade Nativa)

**[COPIE A PARTIR DAQUI]**

ATUE COMO UM ENGENHEIRO DE MACHINE LEARNING ESPECIALIZADO EM HARDWARE EDGE (C++/RUST).

**CONTEXTO MESTRE (PROJETO GENESIS MC - SODA):**

Hardware: i9, 32GB RAM, RTX 2060m (6GB VRAM) e iGPU Intel.

O SODA é um daemon escrito em Rust puro. Precisamos rodar modelos LLM, TTS (Texto para Voz) e STT (Voz para Texto) LOCALMENTE, sem iniciar servidores Python (Zero FastAPI/Ollama em background).

**A MISSÃO DE PESQUISA (VETOR EPSILON - INFERÊNCIA EMBARCADA):**

Pesquise profundamente como EMBUTIR diretamente a inferência de IA dentro de um processo Rust, aproveitando a RAM do sistema (32GB) e a VRAM (6GB) de forma cirúrgica.

1. **Core de Raciocínio (LLM):** Compare `llama_cpp_rs` (bindings de C++) vs `Candle` (framework Rust-native da HuggingFace). Como carregar dinamicamente os pesos (mmap) de RAM para VRAM só quando necessário?
2. **Audição e Fala (Multimodalidade):** Como embutir `Whisper.cpp` (STT) para o agente ouvir o host o tempo todo sem drenar o i9? Como embutir motores de voz ultra-leves e humanos (como Piper TTS ou Kokoro-82M usando ONNX no Rust) para o agente falar?
3. **Visão (Opcional):** Como extrair telas do host eficientemente para análise de modelos de visão (VLM).

**FORMATO DE SAÍDA EXIGIDO:**

Gere um documento Markdown contendo:

1. **A Matriz de Inferência Rust:** A escolha definitiva entre Llama.cpp via Bindings vs. Candle Native.
2. **Arquitetura Multimodal (Voz):** O pipeline exato de como o áudio entra no Rust, é transcrito (Whisper), processado (LLM) e falado de volta (Piper/Kokoro) em milissegundos.
3. **Gestão de VRAM:** Estratégias para uma RTX 2060m não dar Out-Of-Memory (OOM) ao trocar entre modelos visuais, de voz e texto.

**[FIM DA CÓPIA]**