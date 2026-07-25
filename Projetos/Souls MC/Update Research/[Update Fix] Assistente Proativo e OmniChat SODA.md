---
sticker: lucide//zap
---
# Relatório de Arquitetura: OmniChat, Consciência de Contexto e a "SOUL" do SODA

Este documento retifica abordagens anteriores e estabelece o design definitivo de como o Agente Principal (Life Coach/Jarvis) do Genesis MC interage com o usuário, com o Sistema Operacional e com a Memória Nativa, respeitando as restrições da RTX 2060m (6GB VRAM) e o perfil cognitivo 2e/TDAH.

## 1. O Fim do Mal-Entendido: SODA > NotebookLM

O Genesis MC **não delega** sua memória primária ao Google NotebookLM. A arquitetura de **Memória Tri-Partite nativa em Rust** já resolve esse problema com superioridade, pois é local e instantânea.

- **Como a IA sabe qual é o "Assunto"?** Ela não precisa adivinhar arquivos externos. Quando você trabalha no Antigravity IDE ou no Canvas do SODA, o _daemon_ em Rust está gravando no **SQLite (FTS5)** e no **Grafo (Petgraph)**.
- **O Roteamento Semântico:** Se você pergunta "Por que este código Rust está falhando?", a IA busca no seu banco vetorial local (LanceDB/Qdrant) os últimos arquivos que você editou e o _Design Document_ do projeto, injetando esse contexto no _prompt_ antes mesmo de a resposta ser gerada.

## 2. A "SOUL" (A Alma do Agente)

O Genesis MC não é um chatbot genérico. Ele possui um arquivo central e imutável de sistema (ex: `SOUL.md` ou `SYSTEM_PROMPT.md`), lido no momento do _boot_ do sistema.

- **O que contém:** Suas preferências como desenvolvedor sênior, seu perfil 2e/TDAH (necessidade de comunicação direta, sem jargões, estruturada), suas diretrizes de UI (Cyber-Purple, Vibe Design) e as restrições do hardware atual.
- **Aplicação Prática:** A IA já nasce sabendo quem você é. Ela atua como um **Sparring Partner** (Pessimismo da razão, otimismo da vontade), questionando suas premissas arquiteturais em vez de apenas concordar cegamente com seu código.

## 3. O OmniChat e o "Spotlight" (Contexto Sob Demanda)

Capturar a tela a 60fps enquanto você joga ou modela em 3D destrói os 6GB de VRAM e a CPU. O SODA opera sob o princípio da **Intervenção Focada e Fricção Zero**.

- **O Comando de Chamada (Omni-Trigger):** Um atalho global de teclado (ex: `Alt + Espaço`).
- **A Interface (OmniChat):** Uma barra flutuante minimalista (estilo Raycast ou Mac Spotlight) aparece. Nada de abrir telas complexas.
- **A Captura Mágica (Snapshot Contextual):** No milissegundo em que você invoca o OmniChat, o daemon em Rust executa uma leitura cirúrgica, mas de baixíssimo custo, do que está ativo no SO (usando `sysinfo` ou integrações nativas de acessibilidade):
    - _Está na IDE?_ Ele puxa o DOM da IDE ou as últimas linhas de código selecionadas.
    - _Está no Browser?_ Ele puxa a URL ativa e um extrato do texto via DOM Parsing em Rust.
    - _Se necessário (e só se necessário):_ Tira um _screenshot_ estático e roda um OCR rápido local para entender a interface gráfica onde você "emperrou".

## 4. O Fluxo de Interação na Prática (Exemplo: Resolvendo um Bug)

**O Cenário:** Você está codificando no Antigravity IDE e recebe um erro esotérico de compilador.

1. **Invocação:** Você pressiona `Alt + Espaço`. A barra do OmniChat surge sobre a IDE.
2. **O Comando Humano:** Você digita (ou fala, via Whisper local rápido): _"Isso aqui não compila. Parece erro de ciclo de vida no Rust."_
3. **A Coleta Invisível:** * O SODA lê o texto selecionado na IDE.
    - O SODA lê o último erro do terminal (via integração MCP da IDE).
    - O SODA consulta a Memória L3 (Grafo/Vetor) e lembra: _"O Bruno decidiu usar MPSC Channels na ADR 04, e não Mutex."_
4. **A Resposta Direta (Life Coach):** A IA processa tudo isso na RTX 2060m em poucos segundos e responde na mesma janela limpa:
    - _"O erro ocorre porque você está bloqueando a thread do Tauri. Lembre-se da nossa regra de usar MPSC Channels do Tokio (ADR-04). Mova essa lógica de persistência para o backend. Quer que eu reescreva o bloco ou prefere fazer você mesmo?"_

## 5. Por que essa arquitetura é imbatível?

Porque ela respeita a termodinâmica da sua máquina. O SODA passa 99% do tempo "dormindo" na VRAM, apenas observando a telemetria do SO com custo de CPU quase zero. O "Assistente" não é uma janela que você precisa alimentar com _copiar e colar_. Ele é um **Context-Aware Daemon** que enxerga o seu ambiente no exato momento em que você pede socorro, combinando isso com o histórico de tudo o que vocês já construíram juntos.