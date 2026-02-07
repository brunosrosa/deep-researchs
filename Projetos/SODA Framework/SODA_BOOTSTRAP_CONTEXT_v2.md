---
aliases:
  - SODA_BOOTSTRAP_CONTEXT_v2
---
# ARQUIVO DE MEMÓRIA MESTRE: SODA FRAMEWORK

**Snapshot Version:** v1.8 (The "Hardware Agnostic" Enterprise Edition)
**Contexto:** Bootstrap e População de Arquivos
**Status Físico:** Estrutura de diretórios criada e validada (tree). Conteúdo lógico pendente.

## 1. O PERFIL DO USUÁRIO ("O Piloto")

- **Nome:** Bruno (Tech Agent / Data Scientist).
- **Neurotipo:** **2e (Dupla Excepcionalidade: AH/SD + TDAH)**.
- _Modo de Operação:_ Alta visão sistêmica, mas trava na iniciação. Precisa de "Steel Thread" (passos atômicos), feedback visual e estrutura rígida.
- _Filosofia:_ "Pessimismo da Razão, Otimismo da Vontade". Verdade técnica acima de conveniência.
- **RESTRIÇÃO TÁTICA DE HARDWARE (ATENÇÃO):**
- **Situação:** A infraestrutura suporta inferência local (GPU), mas a ventoinha da GPU física atual está quebrada.
- **CONFIGURAÇÃO ATUAL:** O sistema deve ser configurado para ser **Agnóstico de Hardware**.
- **Default (Agora):** Cloud-First (Gemini via Antigravity/OAuth/CLI) ou CPU-Only (Scripts Python leves).
- **Futuro (Switch):** A arquitetura deve permitir ligar a GPU local apenas mudando uma flag no `.env`, sem refatorar o código.

## 2. O PROJETO: S.O.D.A. v1.8

**Sistema Operacional de Desenvolvimento Agêntico (Spec-Oriented Dockerized Architecture)**

- **Definição:** Meta-framework que orquestra Agentes, MCPs e 22 SOPs para transformar desenvolvimento em linha de montagem determinística.
- **Runtime:** Google Antigravity (WSL2).

### Decisões Técnicas Consolidadas (v1.8)

1. **Inteligência Híbrida & Flexível:**
    - **Big Brain:** Gemini 1.5 Pro (via Google Antigravity/OAuth - Custo Zero/Incluso).
    - **Fast Brain:** Configurável. Pode ser Gemini Flash (API) HOJE, ou Qwen 2.5 (Local GPU) AMANHÃ. O código do Ralph deve respeitar essa configuração via `.env`. 
2. **Soberania de Leitura:** Rejeitamos ferramentas "caixa-preta". Usamos **Docfork** (Primário) e **Rtfmbro** (Fallback) para converter a web em Markdown limpo.
3. **Execução:** **Ralph Loop v1.8**. Um motor em Python (Smolagents) que orquestra a execução. Ele deve ser capaz de usar o contexto de autenticação do ambiente (Google Auth) sempre que possível.
4. **Stack de Busca:** Brave Search, ArXiv, Bing.

## 3. ARQUITETURA TÉCNICA DEFINITIVA

### Axiomas Imutáveis

1. **Clean Root:** Raiz contém apenas `PROJECT_CHARTER.md` e `src/`. Todo o resto (`.agent/`) é oculto.
2. **Spec-Lock:** Nenhum código (Fase 6) sem Spec (Fase 2/4).
3. **Flow Integrity:** Fases posteriores não alteram documentos anteriores sem Rollback.

### A "Super-Stack" (Toolchain)

- **Orquestrador:** GitHub Project Manager MCP.
- **Memória Fria:** OpenMemory (GraphDB via Docker).
- **Motor:** Ralph Loop v1.8 (Script Python em `.agent/scripts/ralph_loop.py`).

## 4. ESTADO FÍSICO ATUAL

A estrutura de pastas foi criada e validada via comando `tree`.

- **Existem:** Pastas `.agent`, `sops_registry`, `workflows`, `skills`, `docker-mcp`.
- **Faltam:** O conteúdo real dos arquivos.
- **Próximo Passo:** O Agente deve fornecer o conteúdo dos arquivos para o Humano salvar.

## 5. REPOSITÓRIO DE CONTEÚDO (Payloads Canônicos v1.8)

**ATENÇÃO AGENTE:** Sua missão na nova sessão é gerar o conteúdo destes arquivos baseados nas definições v1.8.

### ARTEFATO A: A Bíblia (`SODA_v1.8_Master_Architecture.md`)

- Deve detalhar os 22 SOPs.
- Deve explicar a configuração agnóstica de hardware.

### ARTEFATO B: A Constituição (`GEMINI_v2.md`)

- Regras globais de comportamento híbrido (Bootstrap vs Operação).

### ARTEFATO C: O Motor (`Ralph Loop v1.8`)

- Código Python que usa `Smolagents`.
- Deve ler configurações do `.env` para decidir se usa API ou Local (preparado para o futuro).

## 6. DIRETRIZES FINAIS PARA O NOVO AGENTE

1. **Não force GPU agora**, mas deixe o código pronto para ela.
2. **Use Auth Existente:** Priorize o uso do Gemini via Antigravity/CLI (OAuth).
3. **Foco na População:** O objetivo da sessão é preencher os arquivos vazios.
4. **Instalação:** Após criar os arquivos, guie a instalação do `uv` e das dependências.