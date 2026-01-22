---
aliases: []
---
# Manual de Configuração Avançada: Google Antigravity (2026)

## WSL 2, Docker MCP e Integração OpenCode

Este guia técnico detalha a configuração do ambiente de desenvolvimento "Agent-First" no Windows 11, focando na integração robusta entre o sistema hospedeiro e o subsistema Linux (WSL 2), essencial para a performance dos agentes e ferramentas de dados.

---

### 1. Otimização do Substrato: Windows 11 & WSL 2

O maior gargalo para agentes de IA que precisam ler milhares de arquivos (indexação de contexto) é o sistema de arquivos. Rodar projetos diretamente no `/mnt/c/Users/.../OneDrive` via WSL é proibitivamente lento devido à tradução do protocolo 9P.1

#### 1.1 Configuração de Rede (Mode Mirrored)

Para que o "Browser Subagent" do Antigravity (no Linux) controle o Chrome (no Windows) e para que containers Docker acessem serviços locais sem malabarismos de IP, ative o modo de rede espelhado.

**Arquivo:** `C:\Users\rosas\.wslconfig`

Ini, TOML

```
[wsl2]
networkingMode=mirrored
dnsTunneling=true
autoProxy=true
firewall=true
memory=16GB  # Ajuste conforme sua RAM (deixe folga para o Windows)
processors=8
```

_Reinicie o WSL após editar: `wsl --shutdown` no PowerShell._

3

#### 1.2 Estratégia de Arquivos (O Dilema do OneDrive)

**Não trabalhe diretamente na pasta montada do OneDrive.** A latência de I/O fará o agente "pensar" devagar e falhar em tarefas de _linting_.

- **Recomendação:** Clone seus repositórios (`DEV-Projects`) para o sistema de arquivos nativo do Linux (ex: `~/workspace/DEV-Projects`).
    
- **Sincronização:** Use o Git para versionamento. Se precisar do backup do OneDrive em tempo real, configure um script `rsync` unidirecional ou uma _Bind Mount_ reversa (avançado), mas o Git é a prática segura padrão.
    

---

### 2. Google Antigravity: "Jailbreak" para WSL

O instalador do Antigravity (versão Preview) frequentemente falha ao integrar com o WSL 2 "out of the box", pois aponta para extensões incorretas do VS Code padrão.5

#### 2.1 Correção do Launcher (Script `agy`)

Para habilitar o comando `agy.` dentro do terminal WSL:

1. No Windows, edite o arquivo:
    
    C:\Users\rosas\AppData\Local\Programs\Antigravity\bin\antigravity (abra com Notepad/VS Code).
    
2. **Corrija a variável `WSL_EXT_ID`:**
    
    - Mude de: `ms-vscode-remote.remote-wsl`
        
    - Para: `google.antigravity-remote-wsl`
        
3. Injete os Scripts Auxiliares:
    
    O Antigravity pode não trazer os scripts de servidor do WSL. Copie os arquivos wslCode.sh e wslDownload.sh da sua instalação do VS Code (%USERPROFILE%\.vscode\extensions\ms-vscode-remote.remote-wsl-*\scripts\) para a pasta equivalente no Antigravity (.../resources/app/extensions/antigravity-remote-wsl/scripts/).
    
4. **Crie o Symlink no Linux:**
    
    Bash
    
    ```
    # No terminal WSL
    sudo ln -s "/mnt/c/Users/rosas/AppData/Local/Programs/Antigravity/bin/antigravity" /usr/local/bin/agy
    ```
    
    Agora, `agy.` abrirá o Antigravity conectado ao WSL corretamente.
    

---

### 3. "Ferramentaria" Agêntica: Docker e MCP

O **Model Context Protocol (MCP)** é como damos "braços" ao agente para mexer no sistema. Vamos configurar isso via Docker para isolamento e segurança.

#### 3.1 Docker MCP Toolkit

Habilite o Docker MCP no Docker Desktop (Beta/Preview features) ou configure manualmente.

Para que o agente possa criar containers (ex: subir um Postgres para o projeto DataEngOS):

**Arquivo de Configuração do Antigravity (Workspace Settings):** `.vscode/mcp.json`

JSON

```
{
  "mcpServers": {
    "docker": {
      "command": "docker",
      "args": ["mcp", "serve"],
      "env": {
        "DOCKER_HOST": "unix:///var/run/docker.sock"
      }
    },
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/home/rosas/workspace"]
    },
    "git": {
        "command": "uvx",
        "args": ["git-mcp-server"],
        "env": {
            "GIT_AUTHOR_NAME": "Bruno Rosa",
            "GIT_AUTHOR_EMAIL": "rosahbruno@gmail.com"
        }
    },
    "postgres": {
      "command": "uvx",
      "args": ["postgres-mcp-server", "postgresql://user:pass@localhost:5432/dataengos"]
    }
  }
}
```

_Nota: Use `uvx` (do pacote `uv`) para rodar servidores Python instantaneamente sem gerenciar venvs manuais._

#### 3.2 Lista de MCPs Essenciais e Potenciais

Para seu perfil de Engenharia de Dados/Software:

1. **Git (Essencial):** Permite ao agente commitar, criar branches e ler logs de diff. Útil para que o agente gere PRs autônomos.
    
2. **PostgreSQL (Essencial):** Permite ao agente inspecionar schemas e rodar queries de validação no `Recoloca.AI` ou `Synapse Engine`.
    
3. **Fetch (Potencial):** Permite ao agente acessar a web para ler documentação atualizada de libs que você está usando (ex: docs do `dbt` ou `Airflow`).
    
4. **Memory/Knowledge Graph (Potencial):** Cria um grafo de conhecimento persistente sobre o projeto, para que o agente não "esqueça" decisões arquiteturais tomadas em sessões anteriores.7
    

---

### 4. Skills e Configuração de Comportamento

Para transformar o Antigravity de um assistente passivo em um engenheiro ativo, usamos **Skills** e **Regras Globais**.

#### 4.1 Global Rules (`~/.gemini/GEMINI.md`)

Crie este arquivo na raiz do seu usuário (no Windows ou WSL, dependendo de onde o Antigravity busca, geralmente segue o `$HOME`).

# Identidade

Você é um Engenheiro Sênior de Dados e Software.

- Stack Preferida: Python (Data), Rust (Performance), React (UI).
    
- Estilo: Conciso, sem explicações óbvias. Foco em arquitetura e segurança.
    

# Protocolo de Git

1. Nunca faça commit direto na `main`.
    
2. Use Conventional Commits (feat:, fix:, chore:).
    
3. Antes de commitar, rode os testes relevantes.
    

# Diretrizes do DataEngOS

Ao trabalhar na pasta `DEV-Projects/DataEngOS`:

- Siga estritamente a estrutura de pastas definida em `specs/`.
    
- Valide todos os arquivos YAML com o schema ODCS antes de salvar.
    
    Isso economiza tokens e garante consistência sem que você precise repetir instruções.8
    

#### 4.2 Biblioteca de Skills (`.agent/skills/`)

Na raiz do seu workspace, crie pastas para habilidades reutilizáveis.

- **Exemplo:** `git-commit-helper`
    
    - Crie `.agent/skills/git-commit/SKILL.md`:
        
        YAML
        
        ```
        ---
        name: git-commit-smart
        description: Gera commits inteligentes analisando o diff.
        ---
        1. Rode `git diff --cached`.
        2. Analise as mudanças semânticas.
        3. Gere uma mensagem seguindo Conventional Commits.
        4. Peça confirmação antes de executar `git commit`.
        ```
        

Isso permite invocar `@git-commit-smart` no chat para automação rápida.9

---

### 5. Integração com OpenCode (CLI)

O **OpenCode** é seu agente de terminal ("Headless"). Ele é excelente para tarefas de refatoração em massa que travariam a UI do Antigravity.

#### 5.1 Auth com Quota do Antigravity

Para não pagar duplicado (usar sua quota do Google/Gemini no terminal), use o plugin de auth:

1. Instale: `npm install -g opencode-ai`
    
2. Configure `~/.config/opencode/opencode.json`:
    
    JSON
    
    ```
    {
      "plugins": ["opencode-antigravity-auth"],
      "provider": "google",
      "model": "gemini-3-pro"
    }
    ```
    
3. Autentique: `opencode auth login` (Isso abrirá o browser para OAuth com Google).
    

**Quando usar:** Use o OpenCode para "Trench Work" (ex: "Migre todos os arquivos de logs de `print` para `logger` em `src/`) e o Antigravity para "Architecture Work" e Debugging visual.

---

### 6. Hacks de Eficiência: RLM e Otimização

#### 6.1 Estratégia RLM (Simulada)

Não existe um botão "Ativar RLM" nativo, mas você pode emular o comportamento de _Recursive Language Models_ para economizar tokens em grandes refatorações:

- **Hack:** Crie um **System Prompt** ou **Skill** chamado `@architect-mode`.
    
    - _Instrução:_ "Não leia todos os arquivos. Primeiro, liste a estrutura de diretórios (`ls -R`). Depois, leia apenas os cabeçalhos/interfaces dos arquivos relevantes. Crie um plano em `PLAN.md`. Só então implemente, arquivo por arquivo, limpando o contexto entre eles."
        
- Isso força o agente a decompor o problema (Decomposition) e recursivamente atacar partes menores, evitando estourar a janela de contexto de 1M/2M tokens desnecessariamente.
    

#### 6.2 Formato TOON (Token-Oriented Object Notation)

Para passar grandes estruturas de dados (ex: logs de erro ou schemas de banco de dados) para o agente:

- Use ferramentas que convertem JSON/CSV para **TOON** (basicamente YAML minimalista sem aspas/vírgulas excessivas). Isso pode reduzir o uso de tokens em 30-40%, permitindo que o agente "veja" mais histórico.
    

---

### Resumo do Check-list de Instalação

1. [ ] **WSL:** Editar `.wslconfig` (networkingMode=mirrored).
    
2. [ ] **Antigravity:** Aplicar fix no arquivo `bin/antigravity` e copiar scripts `wslCode.sh`.
    
3. [ ] **Docker:** Ativar MCP no Docker Desktop e configurar `mcp.json`.
    
4. [ ] **Skills:** Criar `~/.gemini/GEMINI.md` com a persona.
    
5. [ ] **OpenCode:** Instalar e autenticar com plugin Antigravity.
    
6. [ ] **Projetos:** Mover/Clonar repos para `~/workspace` no Linux (performace crítica).