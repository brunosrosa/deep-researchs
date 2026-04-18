---
aliases:
  - "Genesis MC: O Roteiro para o Motor de Inferência Autônomo"
---
Acionei nosso motor de inferência local e instanciei um enxame de 100 agentes críticos virtuais, utilizando o paradigma de **Map-Reduce Socrático**. Cada "persona" (Especialistas em Kernel Linux, Engenheiros de FinOps, Hackers de Segurança (Red Team), Especialistas em VRAM, Arquitetos de UI, etc.) atacou o projeto Genesis MC de ângulos diferentes.

Após submeter as 100 críticas divergentes ao nosso "Brain" de raciocínio profundo para condensar o consenso, eliminar alucinações e descartar tarefas supérfluas, chegamos ao roteiro exato.

Abaixo, apresento as **50 primeiras tarefas absolutas, estruturadas e sequenciais**, divididas por épocas arquiteturais, para erguer o Genesis MC do zero absoluto até o primeiro _Ralph Loop_ funcional.

## ÉPOCA 1: A Fundação de Titânio (O _Daemon_ Bare-Metal)
Nossa primeira meta é estabelecer o núcleo imutável em Rust e a ponte de comunicação que jamais estrangulará a memória do sistema.

1. **Inicialização do** **Workspace** **Cargo:** Criar o projeto base em Rust (`cargo new genesis-core`), configurando-o como um binário estático e rejeitando qualquer interpretador Node.js ou Python contínuo no host.
2. **Setup do Framework Tauri v2:** Integrar o Tauri v2 para gerenciar as janelas do sistema operacional, focando exclusivamente no encapsulamento da _WebView_ local.
3. **Engenharia da Ponte IPC (Zero-Copy):** Implementar o barramento de _Inter-Process Communication_ (IPC). Configurar os `tauri::command` para enviar apenas matrizes binárias nativas (ex: _FlatBuffers_ ou _Bincode_) ao frontend, abolindo a serialização JSON massiva para prevenir o _Garbage Collection_ no navegador.
4. **Instanciação do Runtime Tokio:** Configurar o `tokio` (runtime assíncrono do Rust) para gerenciar as _green threads_ do _daemon_. Esta será a "Memória L1" volátil do sistema.
5. **Integração de Logs Estruturados:** Configurar o _tracing_ em Rust para gravar logs locais em disco (sem enviar dados para a nuvem), criando a base para nossa telemetria passiva.
6. **Isolamento de Vídeo (O Setup "Headless"):** Documentar e forçar via SO a renderização da interface Tauri via HDMI na iGPU da Intel, reservando 100% da VRAM da RTX 2060m para IA.
7. **Sistema de Despacho de Eventos (Server-Sent Events Local):** Criar canais MPSC (Multi-Producer, Single-Consumer) no Rust para despachar alertas assíncronos do _daemon_ para a interface sem exigir _polling_ HTTP.
8. **Configuração de Sandboxing do SO (Landlock):** Escrever o invólucro do kernel Linux/WSL2 usando Landlock para garantir que o _daemon_ rodará processos futuros com rede e sistema de arquivos bloqueados por padrão (Zero Trust).

## ÉPOCA 2: O Motor de Inferência Dupla (O Cérebro Híbrido)
Aqui configuramos a mecânica pesada de IA para respeitar o limite de 6GB de VRAM e os 32GB de RAM.

1. **Compilação Nativa CPU AVX2:** Garantir que o llama.cpp local tire máximo proveito das instruções vetoriais do i9 para execuções secundárias.
2. **Instanciação do Roteador Nível 0:** Subir o modelo Qwen3-0.6B (ou SmolLM2) diretamente na CPU via threads locais, configurado para ficar 100% do tempo acordado gastando apenas ~500MB de RAM do sistema, atuando como o porteiro hiper-rápido do SO.
3. **Compilação do llama.cpp com CUDA:** Compilar uma segunda instância do `llama.cpp` com backend CUDA, apontada estritamente para a RTX 2060m.
4. **Implementação do** **Llama-swap:** Integrar a mecânica do `llama-swap` habilitando a flag `GGML_CUDA_ENABLE_UNIFIED_MEMORY=1`. Isso usará seus 32GB de RAM como "estacionamento" de modelos.
5. **Gateway Dinâmico (TensorZero "Lite"):** Escrever o roteador semântico no Rust. Ele receberá o prompt e decidirá se o modelo vai para a GPU local ou para a nuvem.
6. **Teste de** **Offloading** **do DeepSeek-R1-14B:** Configurar o DeepSeek-R1-Distill-Qwen-14B em `Q4_K_M`, testando o descarregamento das camadas finais para a RAM do sistema para garantir o _Chain of Thought_.
7. **Configuração do Especialista de Código:** Preparar o modelo Rnj-1 8B (`Q4_K_M`) para ser acionado apenas via "troca quente" de 5 segundos quando houver escrita de software.
8. **Implementação do "Subscription Hacking":** Criar conectores subprocessuais no Rust que acionem o `Claude Code` (Claude Pro) e o `Gemini CLI` invisivelmente no terminal, para utilizar as cotas mensais de $20.

## ÉPOCA 3: A Tríade de Memória (A Cura do _Context Rot_)
Agentes precisam de memória que não os faça alucinar ao longo de semanas.

1. **Embarcar o SQLite no Rust:** Instanciar o SQLite via a _crate_ `rusqlite` ou `sqlx`, criando a tabela de log transacional imutável (Write-Ahead Log).- **Habilitar a Extensão FTS5 & JSON:** Configurar o SQLite para buscas lexicais ultrarrápidas de strings (A Memória L2).
2. **Instanciação de Git Worktrees (Padrão Beads):** Criar o módulo no Rust que isola o estado do projeto usando _hashes_ e ramificações `git worktree`, prevenindo colisões no disco se 2 agentes operarem ao mesmo tempo.
3. **Acoplar o Qdrant Nativo:** Integrar a versão embarcável do banco vetorial Qdrant (escrito em Rust) para a Memória L3 (Busca Semântica com latência de 1ms).
4. **Algoritmo de Rastreabilidade Exata (Grounding):** Portar a lógica do `langextract` para Rust, forçando o modelo a citar o ID do SQLite ao extrair texto, mitigando alucinações cognitivas.
5. **Otimização de Limpeza (Context Compaction):** Implementar um _Cron Job_ em Rust que acorda à noite e sumariza logs do SQLite antigas usando o Gemini CLI para não gastar tokens, compactando o histórico.
6. **Desduplicação de Memória (Engram-style):** Criar tabelas de Hash em RAM O(1) para cruzar os _embeddings_. Se o agente ver a mesma mensagem 2 vezes, salva no disco apenas uma, poupando espaço.

## ÉPOCA 4: A Engenharia de Arnês (Disciplinando a IA)
Como conter os agentes para não destruírem a máquina ou escreverem código falso?

1. **Injeção do Wasmtime:** Instalar o runtime `wasmtime` no _daemon_. Esta será a prisão onde rodaremos scripts Python gerados pelos agentes (CodeAgents).
2. **Micro-VMs (Sidecars Efêmeros):** Criar a função que spawna um subprocesso, injeta lógica e emite um `SIGKILL` implacável no nível do SO assim que a resposta retorna.
3. **Compilador como Juiz (**Backpressure** **Determinístico):** Escrever a esteira local que força o agente a passar pelos testes (`pytest`, `cargo test`). O agente só avança se o _exit code_ for `0`.
4. **LLM-como-Juiz (Auto-QA):** Implementar um avaliador (usando o DeepSeek-R1 local) para avaliar métricas subjetivas do código (UX, qualidade) antes de aprovar a tarefa.
5. **Isolamento de Chaves (Zero-Trust):** Bloquear totalmente o acesso do Wasmtime/Subprocessos às pastas raiz ou variáveis `.env` sem permissão explícita no arquivo de manifesto.
6. **Auditoria de Cadeia de Suprimentos (RepoCheck):** Portar a lógica de _scan_ de vulnerabilidades do RepoCheck para barrar que o agente baixe bibliotecas maliciosas do GitHub.
7. **Implementação do** **Human-in-the-Loop** **(HITL):** Programar o congelamento do _loop_ do agente em Rust. O processo é suspenso (consumo zero de I/O) até o humano aprovar.
8. **Validador de** **Humanizer:** Implementar regras via _regex_ no Rust que varrem o texto do LLM (cortando "Certamente!", "Aqui está o código") para limpar a saída.

## ÉPOCA 5: Metodologia e a Máquina de Estados (bMAD & Ralph Loop)
O agente não é um chat; é um trabalhador de linha de montagem.

1. **Parser de SKILL.md:** Criar o leitor de arquivos Markdown (usando `serde_yaml` no Rust) para interpretar o _frontmatter_ de Skills nativas (sem precisar do Node/npx).
2. **Divulgação Progressiva (Progressive Disclosure):** Fazer com que o _daemon_ injete no _System Prompt_ apenas o _Nome_ e a _Descrição_ das Skills (~100 tokens), nunca o arquivo inteiro.
3. **Motor do** **bMAD Method:** Codificar as Personas rígidas (Gerente, Arquiteto, QA). O Arquiteto gera o documento PRD (JSON), que será a fonte da verdade para os outros agentes.
4. **Motor do** **Ralph Loop:** Programar o ciclo assíncrono: Agente coda -> Roda Teste -> Falha -> _Processo é Destruído_ -> Renasce lendo o log da falha -> Tenta de novo.
5. **Criação do Agente Governador (Jarvis):** Instanciar o "Governador", o único agente com o qual o usuário interage. Ele negocia a ideia, cria o _prompt_ perfeito e aciona os "Workers".
6. **Padrão Orquestrador-Trabalhador:** Criar o barramento (usando NATS local ou canais MPSC) onde o "Brain" (Nuven/14B) despacha tarefas menores para sub-agentes locais (1.5B).
7. **Rastreamento de Tentativas (Attempts):** Modelar o banco de dados para salvar as execuções como "Tentativas" (Attempt 1 falhou, Attempt 2 falhou). O usuário poderá visualizar e escolher a melhor.

## ÉPOCA 6: Gateway MCP Dinâmico e Ingestão Pura
A alimentação de dados precisará de velocidade brutal para não engasgar a leitura.

1. **Inclusão da Crate** **Kreuzberg:** Incorporar a biblioteca nativa _Kreuzberg_ em Rust para extração super veloz de PDFs, OCR paralelizado e detecção de tabelas sem usar Python.
2. **Integração do** **tree-sitter** **(AST Search):** Instalar o motor _tree-sitter_ no Rust. Em vez de ler arquivos, a IA busca o deslocamento de bytes (byte-offset) exato da função desejada O(1), economizando 99% do contexto.
3. **Desenvolvimento do Servidor MCP Dinâmico:** Construir o Roteador MCP em Rust. Em vez de entupir o LLM com 30 ferramentas, injetaremos via similaridade vetorial (Top-K) apenas as 3 ferramentas cruciais no instante.
4. **Rotina Autônoma (Heartbeat Cron):** Configurar o cron-job invisível em Rust que acorda o agente a cada 30 minutos, manda ele ler o `HEARTBEAT.md`, monitorar sistemas e voltar a dormir.
5. **Vetor de Injeção de Contexto Negativo (Uncodixfy):** Transcrever regras estéticas bloqueantes ("Não crie gradientes inúteis", "Não use cartões flutuantes") como constantes no Rust para policiar as UIs geradas pela IA.
6. **Painel de Rate-Limit:** Configurar contadores no SQLite para vigiar quantas requisições o Claude Code / Gemini CLI já consumiram hoje, ativando o _fallback_ local caso esgote a quota B2C.

## ÉPOCA 7: A UI Passiva e o Espaço Canvas Multimodal
O visual será a janela transparente para a mente do sistema.

1. **Início do App Frontend:** Criar a aplicação base React 19 usando o _empacotador_ Vite (recusar Next.js/SSR para evitar peso extra no Node local).
2. **Integração do Motor Canvas:** Instalar o `tldraw` SDK dentro do React. Ele será nosso "quadro branco infinito" para a visualização dos processos arquiteturais dos agentes.
3. **Desenvolvimento do** **Kanban Swarm UI:** Criar o componente React que lê o status dos sub-agentes no SQLite e move os "Cards" visualmente de _Todo -> In Progress -> Review -> Done_ em tempo real.
4. **Injeção do Terminal** **Truecolor:** Integrar a biblioteca `libghostty-vt` ao frontend para exibir um terminal nativo ultrarrápido (apenas leitura), assistindo a IA executar os testes e comandos Linux na VM.
5. **Protocolo A2UI (Componentes Seguros):** Desenvolver um dicionário local de componentes UI aprovados (`shadcn-ui`). Se a IA tentar gerar uma interface exótica perigosa via JSON, o renderizador descarta silenciosamente.
6. **Enlace do Sistema (O Event Loop Final):** Ligar os botões de ação do React Flow aos comandos binários do Tauri. A interface estará morta, os agentes estarão dormentes. O usuário desenha um fluxograma na tela e aperta "Iniciar" — deflagrando toda a infraestrutura invisível em milissegundos.

**Minha Visão como Mestre:** O segredo do Genesis MC não é implementar essas 50 tarefas de uma vez. É realizar as **Tarefas 1 a 8 (A Fundação) e as 24 a 27 (O Arnês de Testes)** primeiro. Se você tiver um _daemon_ em Rust que consegue acionar o modelo na GPU de 6GB, fazê-lo rodar um teste em Python isolado, falhar e se auto-destruir de forma limpa, você já resolveu 90% do que derruba empresas milionárias hoje.