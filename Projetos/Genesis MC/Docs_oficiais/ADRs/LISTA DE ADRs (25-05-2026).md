---
sticker: lucide//activity
---
# CAMADA DE FUNDAÇÃO E HARDWARE (BARE-METAL)

- ADR-001: Core Stack Restrita (Rust/Tokio no Backend, Svelte 5 no Frontend, Repúdio a Node.js/Electron).
- ADR-002: Sandboxing Híbrido Zero-Trust (Wasmtime para lógica pura; Landlock/AppContainer para binários do host).
- ADR-003: Comunicação Zero-Copy IPC (Uso de buffers binários Apache Arrow/rkyv via RAM, proibição de serialização JSON).
- ADR-016: Zero-Config Install & Auto-Profiling (Hardware-awareness no boot usando SmolLM para medir PCIe e VRAM).

# CAMADA DE DADOS E MEMÓRIA COGNITIVA

- ADR-004: A Tríade de Memória SODA (SQLite para transacional MVCC; LanceDB mmap para vetorial; LadybugDB Rust para grafos).
- ADR-005: RAG Temporal e Esquecimento Orgânico (Filtros Hard SQL B-Tree e Dinâmica de Langevin, banimento absoluto do TG-RAG pesado).
- ADR-006: A Única Fonte da Verdade / SSOT (Sincronia do Google Sheets de 84 colunas com SQLite).
- ADR-017: Sovereign Sync e Consistência Eventual (Sincronia P2P criptografada, uso de Gitoxide para Event Sourcing).
- ADR-018: Paradigma NextPlaid e Fatiamento de AST (Proibição de vetores monolíticos; fatiamento de código em sub-árvores para LanceDB).

# CAMADA COGNITIVA E AGÊNTICA

- ADR-007: Avaliador Epistêmico / Hipocampo Híbrido (Uso de SLMs Phi-4-mini na CPU via Logit Probing e MeCo para medir ambiguidade).
- ADR-008: Roteamento FinOps / ParetoBandit (Métrica E³ decidindo dinamicamente entre Nuvem Premium ou Custo Zero Local).
- ADR-009: Decodificação Restrita / SGR (Uso de llguidance e Pydantic forçando IAs a gerarem JSON matemático perfeito).
- ADR-015: Motor Generativo Local (Abandono do llama.cpp para o fluxo principal; Adoção do Candle em Rust gerenciando modelos GGUF Q4_K_M).

# CAMADA DE FLUXO E ENGENHARIA DE SOFTWARE

- ADR-010: Spec-Driven Development / SDD (TDD forçado via Ralph Loop, guiado por PRDs atômicos, proibição total de Vibe Coding).
- ADR-011: Governança HITL e Agent Inbox (Agentes não escrevem no main sozinhos; uso de Pull Requests semânticos aprovados pelo humano).
- ADR-012: O Guardião Idempotente / Fase -1 (Cálculo mecânico e Zero-AI de Drift de versão via API do GitHub).
- ADR-019: Máquina de Estados do ETL (Fase 0 acionada por 'PENDENTE_FASE_0'; Fase 4 gera 'PRONTO_PARA_FASE_5' condicionalmente).

# CAMADA DE PRODUTO E UX

- ADR-013: Cyber-Neuro Synthesis (Estética utilitarista TDAH, Planaridade Absoluta, Ghost Borders, Instância Mecânica de 50ms).
- ADR-014: Fricção Produtiva (Atraso sintético de 800-1500ms para ações agênticas de alto nível, evitando submissão cognitiva).
