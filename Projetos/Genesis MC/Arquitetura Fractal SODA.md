---
sticker: lucide//anchor
---

# 🏛️ Arquitetura Fractal de Documentação: Genesis MC (SODA)

**Versão:** 1.0 | **Status:** APROVADO | **Alvo:** Agentes LLM e Arquiteto 2e

## 1. A Filosofia da "Revelação Progressiva"

A documentação do Genesis MC não é um livro linear; é um banco de dados relacional em formato de texto. Um Agente de IA nunca deve ler o projeto inteiro. Ele lê a `Constituição`, entende o `Milestone Atual` e consulta apenas o `Vetor` ou `ADR` no qual está trabalhando.

## 2. A Árvore de Diretórios do SDD

Esta é a estrutura exata que deve ser criada na pasta `docs/` do seu repositório Rust/Tauri. Cada arquivo deve ser hiper-conciso.

```
/docs
├── 📜 00_MANIFESTO_SODA.md       (A Constituição: "Bare-metal, Local-first, Zero Python")
├── 📜 01_AGENTS_PROTOCOL.md      (Instruções de sistema imutáveis para qualquer LLM que ler este repo)
│
├── /architecture                 (O SDD Macro)
│   ├── 🏗️ MACRO_TOPOLOGY.md      (Como Rust, Tauri, SQLite e Llama.cpp se conectam)
│   ├── 🧠 MEMORY_HIPPOCAMPUS.md  (Esquemas do SQLite, Vetores e Grafos)
│   ├── ⚡ INFERENCE_ENGINE.md    (Gestão de VRAM, iGPU, Llama.cpp vs Ollama)
│   └── 🛡️ SANDBOX_SECURITY.md    (Wasmtime e Limites de Execução)
│
├── /adrs                         (Architecture Decision Records - O Registro Histórico)
│   ├── ADR-001-use-rust-tokio.md
│   ├── ADR-002-reject-python-daemons.md
│   ├── ADR-003-xyflow-canvas-ui.md
│   └── ADR-004-sqlite-wal-mode-over-vector-dbs.md
│
├── /vectors                      (As 9 Frentes de Batalha de Longo Prazo)
│   ├── VETOR_ALPHA_MEMORIA.md
│   ├── VETOR_BETA_SEGURANCA.md
│   └── ... (Até o Vetor Iota)
│
└── /milestones                   (PRDs Granulares e Acionáveis - Onde o código nasce)
    ├── 🎯 MILESTONE_00_TOOLCHAIN.md (Setup Rust, Tauri, Vite - Sem IA)
    ├── 🎯 MILESTONE_01_SKELETON.md  (IPC Rust <-> React, XYFlow base)
    ├── 🎯 MILESTONE_02_MEMORY.md    (Conexão Rust <-> SQLite)
    └── 🎯 MILESTONE_03_INFERENCE.md (Primeiro "Hello World" do Phi-4 na VRAM)
```

## 3. O Protocolo de Orquestração do Agente (BMAD adaptado)

Sempre que você utilizar o Antigravity IDE ou o Gemini CLI para criar um código, o Agente **deve** ser instruído a seguir este fluxo:

1. **CONTEXTO INICIAL:** O Agente deve ler `00_MANIFESTO_SODA.md` e `01_AGENTS_PROTOCOL.md`.
2. **FOCO DA TAREFA:** O usuário (você) aponta o Agente para um Milestone específico (ex: `Leia /milestones/MILESTONE_01_SKELETON.md`).
3. **VERIFICAÇÃO DE DÍVIDA (Flow-Debt):** Antes de codificar, o Agente deve verificar a pasta `/adrs` para garantir que não está sugerindo uma biblioteca pesada de Node.js proibida pelo ADR-002.
4. **EXECUÇÃO ATÔMICA:** O Agente escreve o código, focado apenas naquele Milestone.

## 4. O PRD Granular: A Anatomia de um "Milestone"

Um documento de Milestone (O seu PRD tático) não descreve o "futuro". Ele descreve a próxima semana. Todo Milestone deve ter:

- **Objetivo Atômico:** O que essa versão faz.
- **Restrições (Constraints):** O que é expressamente proibido (Ex: "Proibido usar bibliotecas de roteamento web no React").
- **Critérios de Aceite (DoD - Definition of Done):** Condições binárias (Passou/Falhou) mensuráveis via compilador.
- **Tarefas (Checklist):** Passos sequenciais para o "Logic Coder" executar.