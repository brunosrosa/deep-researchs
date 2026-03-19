# Roadmap de Engenharia SODA: O Caminho para o Genesis MC

**Versão:** 1.0 (Plano de Batalha de Execução)

**Filosofia:** Construção incremental _Bottom-Up_ (do Metal Nu para a Interface).

Este documento detalha os 50 passos atômicos para a materialização do Sistema Operacional Agêntico Soberano (SODA). A arquitetura evolui da infraestrutura base (Rust/Tauri) até os sistemas proativos (Life Coach) e as pontes externas.

## FASE 1: A Fundação de Titânio (Skeleton & Bare-Metal Core)

_Objetivo: Estabelecer o repositório, a comunicação IPC de zero-copy e o Canvas passivo._

1. **Inicialização do Monorepo:** Configurar o projeto base utilizando `Tauri v2.0` (CLI), estabelecendo o _backend_ em Rust puro e o _frontend_ em React/TypeScript.
2. **Estruturação do Tokio Runtime:** Configurar o `tokio` no `main.rs` para suportar _multi-threading_ assíncrono pesado, isolando a thread da UI das threads de I/O de IA.
3. **Ponte IPC (Inter-Process Communication):** Desenvolver as funções `#[tauri::command]` iniciais para tráfego bidirecional de dados entre o Rust e o React, minimizando serialização JSON excessiva.
4. **Setup do Frontend Canvas-First:** Instalar e configurar `tldraw` ou `xyflow` no React, removendo lógicas de estado complexas da UI. A UI apenas "escuta" eventos do Rust.
5. **Integração do Shadcn UI:** Estabelecer o sistema de design base (botões, modais, tipografia) com foco em alto contraste e legibilidade para o perfil 2e/TDAH.
6. **Sistema de Tracing e Logs:** Implementar a crate `tracing` no Rust para auditoria profunda de todos os processos do _daemon_, essencial para o futuro _debug_ de alucinações.

## FASE 2: O Córtex Neural (Inferência & RLM Engine)

_Objetivo: Acordar a IA na máquina, carregando modelos quantizados e controlando a VRAM limitadíssima da RTX 2060m._

7. **Integração do Motor de Inferência:** Importar a crate `candle-core` (HuggingFace) para Rust, configurando suporte CUDA nativo para compilação.
8. **Pipeline de Carregamento GGUF:** Criar a estrutura em Rust capaz de ler e carregar pesos de modelos no formato GGUF (INT4) diretamente do SSD para a RAM/VRAM.
9. **Gerenciador de VRAM (KV Cache Controller):** Desenvolver um alocador rígido que impeça o cache de contexto de ultrapassar a barreira física de 4.5GB da RTX 2060m.
10. **Implementação do Roteador Nível 0:** Carregar o `FunctionGemma 3 (270M)` na iGPU da Intel (ou CPU) via OpenVINO/Candle para triagem rápida de _prompts_ de entrada.
11. **Máquina de Estados RLM (Recursive Loop):** Codificar o _loop_ do RLM no Rust. A função não deve retornar texto para a UI, mas sim repassar a saída do modelo de volta para a entrada dele mesmo, até que uma tag `<FINAL>` seja gerada.
12. **Módulo Scratchpad (Deslizamento de Contexto):** Criar a função que resume as iterações do RLM, garantindo que o modelo `Phi-4-mini` não estoure o contexto limite durante "pensamentos longos".
13. **Parser de Chain-of-Thought (CoT):** Escrever um _parser_ nativo que identifique e oculte tags `<think>` geradas pelo DeepSeek-R1-Distill, enviando apenas a árvore lógica formatada para o _Canvas_.
14. **Benchmark de Estresse:** Rodar scripts puros no _backend_ para validar _Tokens Per Second_ (TPS) usando o _Phi-4-mini_ sob a contenção do monitor Ultrawide.

## FASE 3: A Tríade de Memória (Extermínio do Context Rot)

_Objetivo: Dotar o agente de memória infinita e estruturada, 100% embutida no binário._

15. **Fundação L2 (Episódica):** Integrar `rusqlite` e configurar o banco de dados em modo WAL (_Write-Ahead Log_) para altíssima concorrência de leitura/escrita.
16. **Ativação FTS5:** Criar os esquemas SQL para a tabela de histórico de conversas e pensamentos, utilizando a extensão de busca léxica rápida.
17. **Fundação L3 (Vetorial):** Incorporar o `LanceDB` (ou `sqlite-vec`). Criar a rotina de _Embeddings_ local para transformar textos/documentos em tensores.
18. **Pipeline de Ingestão de Documentos:** Integrar a crate `Kreuzberg` para extrair texto de PDFs e imagens coladas pelo usuário, enviando-os para o particionador de memória.
19. **Fundação L3 (Grafo/Relacional):** Implementar a estrutura in-memory usando a crate `petgraph` para armazenar tríades de relacionamento ("Usuário -> Gosta -> Rust").
20. **Algoritmo de Leiden em Rust:** Portar ou implementar a lógica de _clustering_ do GraphRAG no Rust, varrendo o `petgraph` para criar resumos semânticos de alto nível.
21. **Unified Memory Controller:** Criar a Interface unificada no Maestro que, ao receber um _prompt_, consulta L2 (Recente), L3-Vector (Similar) e L3-Graph (Contexto Amplo) antes de chamar o LLM.

## FASE 4: As Mãos do Maestro (Ferramental, CLI & MCP)

_Objetivo: Permitir que o SODA interaja com arquivos, internet e código, sem usar servidores pesados._

22. **Construção do Wasmtime Sandbox:** Incorporar a crate `wasmtime` no núcleo Rust. Esta será a "câmara de gás" para códigos desconhecidos.
23. **Governador de Recursos (Kill-Switch):** Configurar limites de memória (ex: 50MB) e de _Fuel_ (instruções de CPU) no Wasmtime para que _loops_ infinitos gerados pela IA abortem automaticamente.
24. **Abstração MCP2CLI:** Implementar o _wrapper_ em Rust que converte chamadas do padrão Model Context Protocol em subprocessos CLI atômicos e descartáveis.
25. **Catálogo de Ferramentas (Late-Binding):** Criar o sistema de injeção dinâmica: o LLM conhece apenas os nomes das ferramentas; o SODA injeta a documentação da ferramenta apenas quando ela for solicitada.
26. **Ferramenta Nativa: AST Parser:** Integrar a crate `tree-sitter` para permitir que o agente extraia funções específicas de arquivos de código (em vez de ler 5.000 linhas à toa).
27. **Ferramenta Nativa: FileSystem Sys-calls:** Implementar funções diretas e seguras em Rust para leitura, escrita e exclusão de arquivos no projeto do usuário.
28. **Integração BMAD:** Carregar os metadados e restrições da metodologia BMAD no _System Prompt_ de codificação do Maestro.

## FASE 5: A Armadura Zero-Trust (Segurança & HITL)

_Objetivo: Proteger o PC contra alucinações destrutivas, exigindo o humano no circuito._

29. **Cofre de Memória Sensível:** Integrar a crate `secrecy` para garantir que qualquer API Key lida não seja "vazada" por acidente no arquivo de _log_ do sistema.
30. **Integração 1Password:** Criar a chamada segura para o `op-cli` (1Password CLI), forçando a validação do Windows Hello (Biometria) para extrair chaves de Nuvem ou Banco de Dados.
31. **NeMo Guardrails Nativos (Trilhos):** Implementar Máquinas de Estado Finito (FSM) em Rust que interceptam a saída JSON do modelo. Se o formato estiver errado, recusa a resposta antes de enviar à UI.
32. **Desenvolvimento da UX "Blast Radius":** Criar no React/Tauri a interface visual de aprovação. Em vez de "OK", exibir um _diff_ de código e a árvore de diretórios afetada em vermelho.
33. **Aprovação Física de Segurança:** Implementar a lógica de UI que exige que o usuário arraste um elemento (_Slider_) para aprovar tarefas de alto risco, quebrando o "clique cego".
34. **Restrições de Kernel (Landlock):** (Específico para Linux, ou AppContainer no Windows) Limitar os subprocessos gerados pelo Maestro a não terem acesso à rede não autorizada.

## FASE 6: O "Holter" e o Roteamento Híbrido

_Objetivo: Otimizar o uso de hardware, descobrir janelas de ócio e gerir a economia de Nuvem vs. Local._

35. **Daemon de Telemetria:** Criar rotina usando `sysinfo` para ler uso de CPU/RAM em tempo real, gravando dados leves num SQLite de diagnóstico.
36. **Análise de Inatividade (Idle Hook):** Capturar interrupções do SO (mouse/teclado inativos) para determinar quando a máquina foi "abandonada" pelo usuário.
37. **Gatilhos de Tarefa Assíncrona:** Construir a fila de tarefas do Rust que acelera a RTX 2060m a 100% apenas durante as janelas de ócio registradas pelo Holter.
38. **Roteador Cloud (Nível 2):** Implementar os conectores nativos para as APIs do Claude 3.7 e Gemini, com formatação estrita baseada nas regras do SODA.
39. **Subscription Hacking Automation:** Automatizar o uso das CLIs do `Claude Code` e do `Gemini CLI` para abusar das assinaturas pagas do usuário no processamento massivo.
40. **Decisor Heurístico do Maestro:** O coração do roteamento: Rust analisa o tamanho da tarefa, a VRAM atual (via Holter) e decide: Vai para a iGPU (Nível 0), RTX (Nível 1 RLM) ou Cloud (Nível 2)?

## FASE 7: Life Coach & Interação Pró-Ativa (2e/TDAH)

_Objetivo: Transformar o SODA de um utilitário reativo para um Sparring Partner pró-ativo._

41. **Observabilidade Passiva de UX:** Implementar a crate `winshift` (ou similar) para monitorar qual janela do SO o usuário está focando no momento.
42. **Métricas de APM (Ações Por Minuto):** Sem atuar como _keylogger_, medir a intensidade de cliques/teclas para inferir estresse, hiperfoco ou inércia.
43. **Context-Aware Triggers (Matemática Socrática):** Se APM = 0 por 1h na IDE, o SODA engaja o Nível 0 (Gemma) para decidir se envia uma notificação provocativa ao usuário.
44. **Pipeline de Áudio (Whisper):** Embutir o `Whisper.cpp` usando suporte OpenVINO (Intel) para que o agente consiga "escutar" comandos locais do microfone sem gastar a placa NVIDIA.
45. **Pipeline de Resposta (Kokoro/TTS):** Embutir geração de voz local. Criar a rotina onde o _Life Coach_ pode verbalizar uma pergunta ou _insight_ quebrando o silêncio.

## FASE 8: O Vetor Gamma (Ponte Remota Segura)

_Objetivo: Acesso total ao SODA pelo celular, criptografado de ponta a ponta, sem abrir o roteador._

46. **Integração da Crate `presage`:** Acoplar as bibliotecas do Signal Protocol no daemon Rust do SODA.
47. **Linkagem de Dispositivo:** Criar a rotina de pareamento (QR Code no Canvas do Tauri) para vincular o SODA local ao celular do usuário no ecossistema Signal.
48. **Loop Assíncrono Remoto:** O SODA escuta mensagens do celular, decodifica o E2EE, processa o áudio/texto e devolve a resposta pelo Signal de forma invisível.

## FASE 9: Refinamento Absoluto e Ralph Loop

_Objetivo: Automação completa do desenvolvimento de software e calibração fina._

49. **Integração do Ralph Loop:** Conectar o executor Wasmtime ao sistema de arquivos para criar o loop destrutivo: _Agente Coda -> Rust Compila -> Falhou? -> Envia o erro pro Agente de novo._
50. **O Teste de Fogo (Auto-Refatoração V1):** Dar ao Genesis MC a tarefa de ler seu próprio código-fonte (em Rust) e solicitar que ele sugira e aplique otimizações matemáticas na camada de gerenciamento de memória usando a própria infraestrutura.