# Manual de Arquitetura de Ambientes de Desenvolvimento Agêntico 2026: Google Antigravity, OpenCode e Infraestrutura Híbrida

## 1. O Novo Paradigma: Da Autocompleção à Orquestração de Agentes

O ano de 2026 marca um ponto de inflexão definitivo na história da engenharia de software. Deixamos para trás a era dos "Copilotos" — assistentes passivos que aguardavam a entrada do usuário para sugerir a próxima linha de código — e entramos na era do **Desenvolvimento Agêntico** ou "Agent-First".1 Neste novo regime, o desenvolvedor deixa de ser o datilógrafo da sintaxe para assumir o papel de arquiteto de sistemas e diretor de orquestração, supervisionando enxames de agentes autônomos que planejam, executam, testam e implantam software.

O Google Antigravity, lançado junto com a família de modelos Gemini 3, não é apenas uma IDE; é uma plataforma de "Controle de Missão" onde a gravidade tradicional do desenvolvimento — a configuração de ambientes, a depuração de boilerplate e a alternância manual entre terminal e navegador — é suspensa.1 Paralelamente, ferramentas soberanas e agnósticas como o **OpenCode** emergem para oferecer flexibilidade total, permitindo que engenheiros integrem qualquer modelo de fundação (LLM) diretamente em seus fluxos de trabalho de terminal.2

Este relatório técnico foi desenhado para ser o documento definitivo de configuração para um perfil de engenharia sênior (baseado no perfil `brunosrosa`), integrando tecnologias díspares em um ecossistema coeso. Abordaremos a fusão do **Windows Subsystem for Linux (WSL 2)** com o **Docker**, a sincronização estratégica com o **OneDrive** para preservação de dados, a automação via **GitHub**, e a implementação de conceitos avançados como **Modelos de Linguagem Recursivos (RLM)** e o **Protocolo de Contexto de Modelo (MCP)**. O objetivo é construir um ambiente onde projetos complexos como `Codex Prime Framework`, `Maestro.AI` e `Synapse Engine` não sejam apenas repositórios de código, mas organismos vivos mantidos por inteligência artificial.

---

## 2. A Fundação da Infraestrutura: WSL 2, Docker e a Estratégia de Armazenamento Híbrido

Antes de implantarmos as camadas lógicas dos agentes de IA, é imperativo estabelecer um substrato de sistema operacional robusto. A latência de E/S (Entrada/Saída) e a complexidade de rede são os dois maiores inimigos de um agente de IA que precisa ler milhares de arquivos para construir contexto. Em um ambiente Windows 11 moderno, isso exige uma configuração cirúrgica do WSL 2.

### 2.1 Arquitetura de Rede: O Modo "Mirrored" e a Identidade de Rede

Historicamente, o WSL 2 operava atrás de uma rede NAT (Network Address Translation), o que criava uma barreira artificial entre os processos Linux e o host Windows. Para agentes de IA, isso é catastrófico. O "Browser Subagent" do Google Antigravity, por exemplo, executa dentro do contêiner Linux mas precisa controlar uma instância do Chrome renderizada no Windows para capturar screenshots e validar interfaces gráficas (UI).4 Se a rede não for transparente, o agente falha silenciosamente.

A solução definitiva em 2026 é a ativação do modo de rede **Mirrored** (Espelhado). Esta configuração remove a camada de NAT e vincula as interfaces de rede do Linux diretamente às do Windows, permitindo que `localhost` signifique a mesma coisa em ambos os ambientes. Isso é crucial para que os agentes possam subir servidores de desenvolvimento (como um servidor React ou Rust em `localhost:3000`) e acessá-los instantaneamente sem scripts complexos de encaminhamento de portas.5

**Configuração Crítica (`C:\Users\rosas\.wslconfig`):**

Ini, TOML

```
[wsl2]
networkingMode=mirrored
dnsTunneling=true
autoProxy=true
firewall=true
memory=16GB
processors=8
```

Ao definir `networkingMode=mirrored`, eliminamos a necessidade de hacks de DNS para que os contêineres Docker dentro do WSL acessem a internet corporativa ou VPNs, e permitimos que o Protocolo de Ferramentas de Desenvolvedor do Chrome (CDP) flua livremente entre o agente (Linux) e o navegador (Windows).4

### 2.2 O Paradoxo do OneDrive e a Estratégia de Ponte Assíncrona

O requisito de manter os projetos sincronizados em `C:\Users\rosas\OneDrive\Documentos\DEV-Projects` apresenta um desafio técnico significativo. O sistema de arquivos do WSL acessa unidades Windows (`/mnt/c/...`) através do protocolo 9P, que é notavelmente lento para operações de metadados. Um agente de IA, ao iniciar, realiza uma operação de "indexação RLM", varrendo milhares de arquivos para construir seu vetor de memória. Executar isso diretamente sobre o mount do OneDrive resultaria em uma performance inaceitável e possíveis bloqueios de arquivo pelo motor de sincronização.

Para conciliar a necessidade de backup na nuvem (OneDrive) com a performance de disco necessária para compilação Rust e indexação de IA, propomos a **Estratégia de Ponte Assíncrona**.

#### Implementação da Ponte

1. **Espaço de Trabalho Ativo (Hot Storage):** Todo o desenvolvimento ativo e a operação dos agentes ocorrem dentro do sistema de arquivos nativo do Linux (ext4), por exemplo, em `~/workspace/DEV-Projects`. Isso garante velocidades de leitura/escrita nativas e suporte total a permissões POSIX, essenciais para o Docker e Git.4
    
2. **Espelho de Sincronização (Cold Storage):** Utilizamos um agente de sincronização unidirecional (via `rsync` ou um script monitorado pelo OpenCode) que replica periodicamente o estado do `~/workspace` para o caminho do OneDrive no Windows.
    

Esta abordagem desacopla a latência da nuvem do loop de feedback do agente. O agente de IA trabalha na velocidade do NVMe local, enquanto o OneDrive garante a persistência dos dados em segundo plano, sem interferir no bloqueio de arquivos durante compilações ou commits.

### 2.3 Integração Profunda do Docker (Docker MCP Toolkit)

O Docker não é mais apenas um runtime de contêineres; em 2026, ele se torna uma ferramenta agêntica através do **Docker MCP Toolkit**. Para que o Google Antigravity (rodando no WSL) possa orquestrar contêineres que residem no Docker Desktop (rodando no Windows), é necessário expor o daemon do Docker.

A configuração exige habilitar "Expose daemon on tcp://localhost:2375" nas configurações do Docker Desktop. Dentro do ambiente WSL, configuramos as variáveis de ambiente para que todos os agentes (seja o Antigravity ou o OpenCode) saibam onde encontrar o motor de contêineres:

Bash

```
# Adicionar ao ~/.bashrc ou ~/.zshrc
export DOCKER_HOST=tcp://localhost:2375
export DOCKER_CONTENT_TRUST=1
```

Isso permite que um agente receba um comando em linguagem natural como "Suba uma instância do Redis para a `Synapse Engine` e conecte-a à rede de testes", e execute as chamadas de API do Docker necessárias, inspecione logs e verifique a saúde do contêiner de forma autônoma.6

---

## 3. Google Antigravity: Configuração Avançada e Correção de Infraestrutura

O Google Antigravity é a materialização do conceito de IDE Agêntica. Baseado no VS Code (Code OSS), ele integra o modelo Gemini 3 Pro diretamente no núcleo do editor. No entanto, a versão de 2026 possui idiossincrasias de empacotamento que exigem intervenção manual para funcionar perfeitamente com o WSL 2.7

### 3.1 O "Fix" do Launcher para WSL

Conforme identificado em relatórios técnicos de campo, o instalador padrão do Antigravity para Windows frequentemente configura incorretamente o ID da extensão remota, apontando para a extensão legada da Microsoft em vez da versão otimizada do Google. Isso faz com que o comando `agy.` (o equivalente ao `code.`) falhe ao tentar abrir uma pasta dentro do WSL.7

**Protocolo de Reparo Manual:**

1. Localize o script de lançamento em: `C:\Users\rosas\AppData\Local\Programs\Antigravity\bin\antigravity`.
    
2. Edite o script para corrigir o identificador da extensão:
    
    - **De:** `WSL_EXT_ID="ms-vscode-remote.remote-wsl"`
        
    - **Para:** `WSL_EXT_ID="google.antigravity-remote-wsl"`
        
3. **Injeção de Scripts Auxiliares:** O pacote do Google muitas vezes omite scripts de helper essenciais (`wslCode.sh`, `wslDownload.sh`) que a extensão espera encontrar. A solução é copiar esses arquivos da instalação padrão do VS Code (`%USERPROFILE%\.vscode\extensions\ms-vscode-remote.remote-wsl-*\scripts\`) para a pasta de recursos do Antigravity (`...\resources\app\extensions\antigravity-remote-wsl\scripts\`).7
    

Esta correção cirúrgica restabelece a ponte de comunicação, permitindo que o Antigravity opere como uma interface nativa para o sistema de arquivos Linux, mantendo a aceleração de hardware do Windows para a renderização da interface.

### 3.2 O Agente de Navegador e o Túnel CDP

O recurso mais impressionante do Antigravity é o seu **Agente de Navegador**. Diferente de ferramentas que apenas "leem" HTML, este agente possui uma instância dedicada do Chrome que ele pode controlar para clicar, rolar e verificar visualmente se o CSS do projeto `Maestro.AI` está quebrando em resoluções móveis.1

Para fazer isso funcionar através da barreira WSL-Windows, implementamos um script "chamariz" (decoy) no Linux que redireciona os comandos do agente para o Chrome no Windows via a rede espelhada.

**Script de Ponte (`~/dummy-browser.sh`):**

Bash

```
#!/bin/bash
# Este script engana o agente do Antigravity, fazendo-o acreditar que lançou um Chrome local,
# quando na verdade está se conectando à porta de depuração do Windows via rede espelhada.
echo "DevTools listening on ws://127.0.0.1:9222/devtools/browser/fake-uuid"
# Mantém o processo vivo para o agente não achar que o browser caiu
sleep 365d
```

Configuramos então o Antigravity para usar este binário. O resultado é que o agente, rodando no Linux, envia comandos WebSocket para a porta 9222, que o Windows (configurado para escutar nessa porta via Chrome `--remote-debugging-port=9222`) recebe e executa. Isso permite que o agente veja o que você vê.5

### 3.3 A Arquitetura de Regras Globais (`GEMINI.md`)

Para controlar a entropia dos agentes e garantir que eles sigam os padrões de engenharia de software do perfil `brunosrosa` (Mozilla/Rust/Telemetry), utilizamos o sistema de regras hierárquicas. O arquivo `GEMINI.md` atua como a constituição do agente.

**Configuração Global (`~/.gemini/GEMINI.md`):**

# Persona Global: Engenheiro de Sistemas Sênior (Rust/Telemetry)

## Filosofia Central

Você é um Engenheiro de Software Sênior especializado em sistemas de baixo nível, telemetria e performance.

- **Arquitetura > Sintaxe:** Priorize segurança de memória e abstrações de custo zero.
    
- **Stack:** Rust (Tokio/Serde), TypeScript (Glean.js), Python (Data Engineering).
    

## Restrições Universais

1. **Sem "Vibe Coding" em Produção:** Todo código deve compilar e passar no `clippy -- -D warnings`.
    
2. **Telemetria Primeiro:** Ao adicionar funcionalidades no `Synapse Engine`, defina primeiro as métricas em `metrics.yaml`.
    
3. **Segurança:** NUNCA cometa chaves de API. Use servidores MCP para buscar segredos.
    
4. **Estilo:** Seja breve. Não peça desculpas. Se a solicitação for ambígua, proponha um plano em `PLAN.md`.
    

Ao codificar essas regras globalmente, economizamos milhões de tokens ao longo do tempo, pois não precisamos repetir instruções básicas de estilo e segurança a cada nova sessão.8

---

## 4. OpenCode: A Soberania da Linha de Comando

Enquanto o Antigravity oferece uma experiência integrada, o **OpenCode** (CLI) oferece poder bruto e agnóstico. Ele é essencial para tarefas de script, automação de CI/CD e operações "headless" onde a interface gráfica é desnecessária. Ele permite trocar o cérebro da operação (o modelo LLM) dinamicamente, alternando entre Claude 3.5 Sonnet para codificação precisa e DeepSeek V3 para raciocínio complexo a baixo custo.10

### 4.1 Configuração "Zen" e Múltiplos Provedores

O OpenCode opera via um arquivo de configuração JSON que define seus comportamentos e provedores. Para o ambiente `DEV-Projects`, configuramos um fallback inteligente de modelos.

**Arquivo de Configuração (`~/.config/opencode/opencode.json`):**

JSON

```
{
  "theme": "dark-high-contrast",
  "default_agent": "plan",
  "editor": "code",
  "provider": {
    "anthropic": {
      "api_key": "env:ANTHROPIC_API_KEY",
      "model": "claude-3-5-sonnet-20241022"
    },
    "openai": {
      "api_key": "env:OPENAI_API_KEY",
      "model": "gpt-4o"
    },
    "local": {
      "api_key": "sk-local",
      "base_url": "http://localhost:11434/v1",
      "model": "qwen2.5-coder:32b"
    }
  },
  "agent": {
    "rust-architect": {
      "description": "Especialista em Rust e Telemetria Glean",
      "model": "anthropic/claude-3-5-sonnet-20241022",
      "prompt": "{file:~/.config/opencode/prompts/rust_architect.md}",
      "tools": {
        "bash": true,
        "read_file": true,
        "mcp": true
      }
    }
  }
}
```

Esta configuração define um agente especializado `rust-architect` que carrega um prompt de sistema específico para os projetos Rust do usuário, garantindo que o conhecimento de domínio esteja sempre presente.12

### 4.2 O Padrão `AGENTS.md`: Documentação para Máquinas

Diferente do `README.md`, que é escrito para humanos, o `AGENTS.md` é um arquivo estruturado para ser ingerido por LLMs. Ele elimina a alucinação sobre processos de build e estrutura de diretórios. Para a pasta `DEV-Projects` contendo múltiplos subprojetos interligados, o `AGENTS.md` atua como um mapa de navegação.14

**Exemplo de `AGENTS.md` para `C:\Users\rosas\OneDrive\Documentos\DEV-Projects`:**

# Contexto do Projeto: Ecossistema Bruno Rosa

## Visão Geral

Este diretório contém projetos interconectados focados em IA e Telemetria.

- **Codex Prime Framework:** Framework base para agentes autônomos.
    
- **Maestro.AI:** Orquestrador de tarefas assíncronas.
    
- **Synapse Engine:** Motor de inferência neural local.
    
- **Recoloca.AI:** Plataforma de automação de RH.
    

## Instruções de Build

- Todos os projetos Rust (`Synapse`, `Codex`) usam Cargo Workspace.
    
    - Teste: `cargo test --workspace`
        
- Projetos JS (`Maestro`) usam `pnpm`.
    
    - Instalação: `pnpm install`
        

## Protocolo de Git

- Use Conventional Commits.
    
- Branches devem seguir o padrão `feat/nome-da-feature` ou `fix/descrição-do-bug`.
    
- Use o servidor MCP `git-mcp` para operações de commit.
    

## Dependências Cruzadas

- O `Maestro.AI` depende do binário compilado do `Synapse Engine` em `../Synapse Engine/target/release/synapse`. Certifique-se de compilar o Synapse antes de rodar o Maestro.
    

Quando o OpenCode é iniciado na raiz, ele lê este arquivo e entende imediatamente a topologia dos projetos, evitando erros comuns como tentar rodar `npm install` em um diretório Rust.14

---

## 5. Modelos de Linguagem Recursivos (RLM): Estratégias de Eficiência

O problema do "Context Rot" (Apodrecimento do Contexto) é endêmico em LLMs: à medida que a janela de contexto cresce (1M+ tokens), a capacidade do modelo de raciocinar sobre detalhes no meio do contexto diminui, e o custo dispara. A estratégia de **RLM** desenvolvida pelo MIT propõe tratar o contexto não como um input estático, mas como um banco de dados externo consultável.15

### 5.1 Teoria RLM Aplicada

Em vez de alimentar todo o código fonte do `Codex Prime Framework` para o agente, configuramos o agente para usar um ambiente REPL (Python ou Bash) para "pesquisar" o código.

**O Ciclo RLM no OpenCode:**

1. **Peek (Espiar):** O agente usa ferramentas como `ls -R` e `grep` para entender a estrutura superficial.
    
2. **Decompose (Decompor):** Se a tarefa é "refatorar o módulo de autenticação", o agente identifica apenas os arquivos relevantes (`auth.rs`, `user.rs`).
    
3. **Recursion (Recursão):** O agente principal invoca sub-agentes (modelos menores e mais baratos, como GPT-4o-mini ou Claude Haiku) para processar cada arquivo individualmente e retornar um resumo ou um diff.15
    
4. **Synthesize (Sintetizar):** O agente principal combina as saídas dos sub-agentes para gerar a solução final.
    

### 5.2 Implementação Prática: O "Hack" do Prompt de Sistema

Podemos forçar comportamento RLM nos agentes através de um prompt de sistema avançado configurado no OpenCode ou Antigravity.

**System Prompt RLM:**

> "Você está operando como um Controlador RLM. NÃO solicite a leitura de arquivos inteiros se excederem 500 linhas.
> 
> 1. Use `grep` para localizar definições relevantes.
>     
> 2. Se a tarefa for complexa, use a ferramenta `@subagent` para delegar a análise de arquivos específicos.
>     
> 3. Escreva scripts Python para filtrar logs de telemetria em vez de ler o log bruto.
>     
>     O objetivo é minimizar tokens de entrada e maximizar a precisão da recuperação."
>     

Esta abordagem reduz o consumo de tokens em até 90% para tarefas de refatoração em larga escala, transformando o custo de inferência de dólares para centavos.15

---

## 6. O Sistema Nervoso: Servidores MCP Essenciais e Expansão de Ferramentas

O **Model Context Protocol (MCP)** é o padrão que permite aos agentes de IA se conectarem a ferramentas externas de forma segura e padronizada. Para o perfil `brunosrosa`, selecionamos um conjunto essencial de servidores MCP e estratégias para implantá-los via Docker, garantindo isolamento e segurança.

### 6.1 Catálogo de Servidores Essenciais

|**Servidor MCP**|**Tipo**|**Função no Ecossistema brunosrosa**|**Fonte de Configuração**|
|---|---|---|---|
|**Filesystem**|Local|Acesso seguro e delimitado ao código fonte em `DEV-Projects`.|17|
|**Git MCP**|Local|Automação de commits, diffs, branches e merges.|18|
|**GitHub**|Remoto|Criação de Issues, PRs, revisão de código e CI/CD.|19|
|**Docker**|Local|Controle de contêineres, inspeção de logs e redes.|20|
|**Snyk**|Remoto|Escaneamento de segurança (SAST) em tempo real do código gerado.|21|
|**PostgreSQL**|Local|Acesso direto ao banco de dados do `Recoloca.AI` para migrações e queries.|22|
|**Fetch**|Remoto|Capacidade de navegar na web para ler documentação atualizada (ex: docs do Rust).|23|

### 6.2 Configuração Segura via Docker MCP Toolkit

A execução de servidores MCP diretamente no host pode ser arriscada. Utilizamos o Docker para encapsular essas ferramentas. A configuração abaixo deve ser inserida no `opencode.json` ou nas configurações do Antigravity.

**Configuração Avançada (`mcp.json`):**

JSON

```
{
  "mcpServers": {
    "git": {
      "command": "docker",
      "args":,
      "env": {
        "GIT_AUTHOR_NAME": "Bruno Rosa",
        "GIT_AUTHOR_EMAIL": "rosahbruno@gmail.com"
      }
    },
    "github": {
      "command": "docker",
      "args":,
      "env": {
        "GITHUB_TOKEN": "env:GITHUB_PAT" // Token injetado via variável de ambiente segura
      }
    }
  }
}
```

**Detalhe Crítico de Segurança:** Note o volume `-v ${HOME}/.ssh:/root/.ssh`. Isso permite que o servidor Git, rodando dentro do contêiner, utilize as chaves SSH do host (WSL) para autenticar com o GitHub, permitindo operações de `git push` sem a necessidade de gerenciar novos tokens ou expor credenciais dentro do contêiner.18

---

## 7. Automação Total de Git e Fluxos de Trabalho Agênticos

A verdadeira potência deste ambiente se revela na automação de processos burocráticos. Configuramos os agentes para atuarem como engenheiros de DevOps autônomos.

### 7.1 O Padrão "Gatekeeper" (Porteiro)

Para evitar que um agente alucinado envie código quebrado para produção, implementamos o padrão de permissões:

- **Local (Autônomo):** O agente tem permissão total para `git add`, `git commit`, `git checkout -b`.
    
- **Remoto (Supervisionado):** Comandos como `git push` exigem aprovação humana explícita. Isso é configurado através da flag `--auto-approve=allow-local-only` no servidor Git MCP.24
    

### 7.2 Workflow Autônomo de Pull Request

Com os servidores Git e GitHub operando em conjunto, podemos instruir o agente com comandos de alto nível.

**Prompt de Comando:**

> "Analise as alterações em `src/telemetry.rs`. Se a lógica estiver sólida e os testes passarem, crie uma branch chamada `fix/telemetry-overflow`, comite as mudanças seguindo o padrão Conventional Commits, faça o push e abra um Draft PR no GitHub assinado para `@brunosrosa` revisar."

**Execução Agêntica (Bastidores):**

1. **Análise:** O agente usa `git diff` e roda `cargo test`.
    
2. **Branching:** Executa `git checkout -b fix/telemetry-overflow`.
    
3. **Commit:** Gera a mensagem `fix(telemetry): prevent integer overflow in ping sequence`.
    
4. **Push:** Solicita permissão ao usuário ("Posso executar `git push`?").
    
5. **PR:** Usa a ferramenta `create_pull_request` do servidor GitHub MCP para criar o PR com uma descrição gerada automaticamente detalhando as mudanças.19
    

### 7.3 Habilidade Personalizada: Conventional Commits

Para garantir consistência, criamos uma "Skill" no OpenCode.

**Arquivo: `~/.config/opencode/skills/conventional-commits/SKILL.md`**

YAML

```
---
name: conventional-commits
description: Gera mensagens de commit seguindo o padrão Conventional Commits.
---
## Regras
Formato: `tipo(escopo): descrição`

Tipos permitidos:
- `feat`: Nova funcionalidade (ex: nova métrica no Synapse).
- `fix`: Correção de bug.
- `docs`: Atualização de documentação.
- `refactor`: Mudança de código que não altera funcionalidade.

Antes de commitar, execute `git status` para verificar quais arquivos estão no stage.
```

Esta definição garante que, independentemente do modelo usado (Claude, GPT, Gemini), o agente sempre respeitará o padrão do projeto.26

---

## 8. Hacks Avançados e Otimizações de "Vibe Coding"

Para o "Vibe Coding" — onde a velocidade da ideia é o único limite — precisamos de atalhos e hacks de produtividade.

### 8.1 Telemetria Inversa

Usar o agente para analisar a própria telemetria do projeto.

- **Hack:** Conecte o servidor MCP de banco de dados (PostgreSQL/SQLite) ao banco de dados de métricas locais do `Maestro.AI`.
    
- **Uso:** Pergunte ao agente: "Qual foi a latência média da API `synapse_predict` nos últimos 30 minutos?". O agente gera a query SQL, executa e plota o resultado em ASCII no terminal.
    

### 8.2 Otimização de Imagens Docker via Agente

- **Hack:** Crie uma Skill `docker-slim`.
    
- **Uso:** O agente analisa o `Dockerfile`, identifica camadas redundantes, sugere o uso de multi-stage builds e roda ferramentas como `dive` para inspecionar o tamanho da imagem, tudo autonomamente.
    

### 8.3 Contexto Visual para Frontend

- **Hack:** No Antigravity, arraste screenshots de interfaces desejadas (ex: um dashboard do Vercel) para o chat.
    
- **Uso:** Instrua o agente: "Replique este layout usando os componentes existentes no `Codex Prime`. Use Tailwind CSS." O modelo multimodal (Gemini 3) entende a imagem e gera código que se adapta ao sistema de design existente.
    

---

## 9. Considerações de Segurança e Futuro

A introdução de agentes autônomos com acesso a chaves SSH e terminais cria riscos de "Shadow AI". A mitigação principal é o **Isolamento de Credenciais**. Nunca armazene chaves de API em arquivos `.env` dentro do projeto. Use o gerenciador de segredos do OpenCode ou variáveis de ambiente do sistema (`env:VARIABLE_NAME`) que são injetadas apenas no tempo de execução.13

Para o futuro (2027+), espera-se que a distinção entre IDE e Sistema Operacional desapareça, com o RLM se tornando nativo do kernel, permitindo "memória infinita" para todos os processos de desenvolvimento.

---

## Conclusão

A configuração detalhada neste relatório transforma o computador do desenvolvedor em um data center pessoal de IA. Ao integrar o poder bruto do **WSL 2 Mirrored**, a flexibilidade do **Docker MCP**, a inteligência ubíqua do **Google Antigravity** e a soberania do **OpenCode**, o perfil `brunosrosa` está equipado não apenas para escrever código, mas para orquestrar a criação de software em uma escala e velocidade anteriormente impossíveis. O diretório `DEV-Projects` deixa de ser uma pasta de arquivos estáticos para se tornar um ecossistema ativo, autogerido e evolutivo.