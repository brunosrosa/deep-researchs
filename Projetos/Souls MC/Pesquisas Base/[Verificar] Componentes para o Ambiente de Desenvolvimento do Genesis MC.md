---
sticker: lucide//boxes
---
Aqui está o desdobramento cirúrgico e direto dos componentes exatos que devem constar no seu radar de instalação e ativação, mapeados camada por camada:

E o uso do Git Subrepo??

**1. Compiladores e CLIs Base (O Metal Nu)**

- **Rust Toolchain:** `rustup`, `rustc`, `cargo`
- **C/C++ Build Tools:** `cmake`, `gcc` ou `MSVC` (vitais para compilar as pontes do Tauri e o motor local do `llama.cpp`)
- **Ecossistema Node:** Node.js (v20 LTS+), `pnpm` (preferencial por ser mais leve/rápido no uso de disco)
- **Framework CLI:** `cargo-tauri` (v2)
- **Controle de Versão:** `git`
- **Sandboxing:** `wasmtime` CLI

**2. Motor de IA, Modelos e Protocolos (O Cérebro Híbrido)**

- **Motor de Inferência Local:** `llama.cpp` (binários compilados especificamente com suporte a CUDA para a arquitetura Turing)
- **Pesos de Modelos (GGUF):** Phi-4-mini (para lógica/JSON), Qwen 3.5 (código), FunctionGemma 270M (roteamento) (outros?)
- **CLIs de Cloud (Nível 2 - Heavy Workers):** 
	- Gemini CLI
	- Claude Code CLI
	- Google Cloud SDK (?)
- **Servidores MCP Operantes:** 
	- `jcodemunch` (leitura e chunking de diretórios) https://github.com/jgravelle/jcodemunch-mcp
	- MCP nativo de **SQLite**
	- MCP de **File System** (arquivos locais) (O Antigravity já tem isso não?)
	- https://github.com/jacob-bd/notebooklm-mcp-cli
	- https://github.com/yokingma/time-mcp
	- https://github.com/softerist/heuristic-mcp
	- https://github.com/jellyjamin/TOON-context-mcp-server
	- E para o controle automatizado do Github (commits atômicos e etc?)? Github Projects?

**3. Ecossistema do Antigravity IDE (O Painel de Controle)**

- **Extensões Mandatórias (Ativar):** 
	- `rust-analyzer` 
	- Tailwind CSS IntelliSense
	- SQLite Viewer
	- Even Better TOML.
- **Extensões Tóxicas (Desativar):** 
	- Auto-completadores genéricos concorrentes
	- formatadores de terceiros pesados que disputem memória.
- **Skills & Workflows Embutidos:** Skill **BMAD** (Branch, Mutate, Approve, Diff), Skill **SDD** (Spec-Driven Development / Planejador de Tasks).
	- https://github.com/bmad-code-org/BMAD-METHOD 
	- https://github.com/LarsCowe/bmalph
	- https://github.com/salacoste/antigravity-bmad-config
	  
	- https://github.com/Fission-AI/OpenSpec
	- https://github.com/github/spec-kit
	- https://github.com/gotalab/cc-sdd
	  
	- https://github.com/AshishOP/arc-protocol
	  
	- https://github.com/obra/superpowers
	
	- https://github.com/vudovn/antigravity-kit
	- https://github.com/sickn33/antigravity-awesome-skills
	- https://github.com/iml1s/antigravity_for_loop
	- https://github.com/Shiritai/sanity-gravity

- **Regras/Contexto Fixado:** `SODA_MILESTONE_1.md` (como regra global/soberana).

**4. Infraestrutura de Dados, I/O e Telemetria Local**

- **Motor de Dados Primário:** Binários do `sqlite3` (com a flag/suporte FTS5 habilitada).
- **Visualizador de Dados:** DB Browser for SQLite (para auditoria humana do _Hippocampus_).
- **Versionamento de Dados:** `dolt` CLI (necessário para reativar o servidor Beads futuramente).
- **Telemetria de Hardware e Termodinâmica:** Ferramentas de leitura de sensores ACPI via CLI (ex: utilitários HWiNFO CLI ou LibreHardwareMonitor) para que o sistema possa monitorar picos de temperatura e agir preventivamente sob estresse térmico.
- **Testes de Comunicação (Vetor Gamma):** `signal-cli` ou utilitários da biblioteca `presage` para homologação isolada da ponte remota.

---