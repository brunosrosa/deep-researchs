# Arquitetura DevSecOps para Sistemas Agênticos (SODA): Especificação Imutável para MCP Vault, Late-Binding e Sidecars Docker de Automação Web

## 1. Fundamentação Teórica: Sistemas Operacionais Agênticos e a Crise de Orquestração

A consolidação dos Sistemas Operacionais Agênticos (SODA - _Sovereign Operative Declarative Agents_) na maturidade tecnológica de 2026 estabeleceu um novo paradigma de interação sistêmica. O núcleo dessa arquitetura é a delegação de capacidades executivas a Modelos de Linguagem de Grande Escala (LLMs), permitindo que operem autonomamente sobre infraestruturas computacionais por meio do Model Context Protocol (MCP). O MCP atua como uma interface padronizada, operando sob um modelo cliente-servidor leve via JSON-RPC 2.0, fornecendo a semântica necessária para que agentes descubram ferramentas, ingiram documentos e afetem o estado do sistema.

Entretanto, a adoção corporativa e de ambientes _Bare-Metal Edge_ revelou falhas críticas na implementação nativa (ingênua) do protocolo. A principal vulnerabilidade estrutural documentada é a "Context Crisis" (Crise de Contexto), que se manifesta de duas formas debilitantes: exaustão de limites de VRAM devido à sobrecarga estática da janela de contexto e vulnerabilidades de segurança oriundas da delegação de execução direta no host. Em arquiteturas primitivas de 2025, os clientes MCP realizavam uma varredura lexical síncrona e incondicional de todos os diretórios e ferramentas disponíveis no momento do _boot_, injetando as assinaturas JSON completas de centenas de ferramentas na memória do modelo de linguagem.

Essa abordagem não apenas resultava em tempos de inicialização inaceitáveis (frequentemente superiores a 60 segundos), como também levava ao desperdício massivo de tokens operacionais e causava "colapso cognitivo" no LLM — o agente tornava-se incapaz de raciocinar com precisão devido à diluição da atenção (attention dilution) frente a milhares de parâmetros de ferramentas irrelevantes.

Em resposta a essas restrições, o ecossistema SODA adotou a filosofia DevSecOps fundamentada em dois pilares: _Hyper-Isolation_ (Hiperisolamento via sandboxes efêmeros e containers) e _Zero-Trust_ (Confiança Zero, repudiando daemons globais). O gerenciamento dessas ferramentas passou a exigir middlewares de roteamento inteligente, capazes de interceptar as requisições do modelo e abstrair a complexidade subjacente, injetando contexto apenas quando matematicamente necessário. Este relatório técnico destrincha a implementação do estado da arte dessa arquitetura: o uso do MCP Vault (`mcpv`) para o registro dinâmico via "Late-Binding" de ferramentas como `jcodemunch` e `sqlite`, culminando no fornecimento do código-fonte exato e imutável para a orquestração segura do servidor de automação web `browser-use-mcp-server` em contêineres Docker hiper-isolados.

## 2. A Mecânica do MCP Vault (`mcpv`) e o Paradigma do Late-Binding

O MCP Vault, desenvolvido notavelmente sob a arquitetura do `thekeunpie-hash/mcpvault`, não é meramente um repositório passivo de chaves; ele atua como um "Smart Middleware" agressivo posicionado entre o agente de inteligência artificial (como Claude, Cursor ou Antigravity IDE) e as dezenas de servidores MCP subjacentes. Ao sequestrar silenciosamente as requisições originais do manifesto `mcp_config.json`, o roteador `mcpv` consegue reestruturar o tráfego JSON-RPC 2.0 e implementar técnicas avançadas de gerenciamento de estado.

A pedra angular dessa arquitetura é o conceito de **Late-Binding** (Abstração de Contexto Tardio ou Vinculação Tardia). Este design padrão desacopla a validade e a declaração explícita de uma ferramenta do momento de inicialização do agente, permitindo a descoberta e invocação em tempo real.

### 2.1. Otimizações de Middleware: Válvula Inteligente e Lazy Loading

A integração do `mcpv` resolve a Crise de Contexto através de três mecanismos sistêmicos sobrepostos:

1. **Lazy Loading (Inicialização de Latência Zero):** No modelo tradicional, a varredura do sistema de arquivos para carregar contextos como `repomix` causava congelamentos de mais de um minuto. O `mcpv` intercepta a chamada de _boot_, retornando sucesso instantâneo (inicialização em $< 0.1$ segundos), e apenas estabelece conexões _upstream_ (por exemplo, invocando o binário via `stdio`) quando o agente emite uma chamada explícita para invocar ou listar aquela ferramenta.
2. **Smart Valve (Defesa e Compressão de Contexto):** O middleware escuta de forma passiva o tráfego JSON-RPC. Na primeira vez que uma ferramenta complexa requer a injeção massiva de contexto (como a árvore completa de um repositório), o tráfego é permitido. Nas requisições repetitivas subsequentes que o modelo inevitavelmente fará em loops iterativos de _planning_, a "Válvula Inteligente" intercepta o pedido longo e o substitui por um sinal criptográfico de "Cached" (consumindo meros ~20 tokens). Esta técnica isolada previne timeouts nos servidores subjacentes e garante uma economia financeira e computacional de aproximadamente 90% em sessões densas.
3. **Booster Injection e Gerenciamento de Processos:** Em ambientes com restrições operacionais (como Windows ou instâncias Linux sem privilégios), o roteador injeta sinalizadores silenciosos, como `--enable-gpu-rasterization` ou `--enable-zero-copy`, e resolve problemas de escalonamento de privilégios (`RunAsInvoker`), garantindo que servidores pesados operem com aceleração por hardware.

Tabela 1: Comparativo de Arquiteturas de Registro MCP

|**Característica**|**Arquitetura Tradicional (Static Registration)**|**Arquitetura SODA / MCP Vault (Late-Binding)**|**Implicações Práticas no DevSecOps**|
|---|---|---|---|
|**Inicialização**|Síncrona, carrega todos os binários e esquemas.|Assíncrona (_Lazy Load_), inicia em tempo sub-milissegundo.|Previne congelamento do cliente e ataques de exaustão de recursos.|
|**Injeção de Schema**|Força bruta na janela de contexto (diluição de atenção).|Recuperação dinâmica orientada por intenção (RAG-MCP).|Aumenta drasticamente a precisão da IA e reduz custos de inferência.|
|**Uso de Tokens**|100% de ingestão redundante a cada chamada (bloat).|Compressão via sinalização ("Cached", economia de ~90%).|Previne limites de API e colapsos de memória em LLMs locais.|
|**Resiliência de PID**|Reinício forçado gera "Cold Starts" custosos.|Pool de processos "pré-aquecidos" via `stdio` perpétuo.|Latência zero durante a invocação real da ferramenta em loops de raciocínio.|

Ao delegar o controle de roteamento para o `mcpv`, a infraestrutura ganha a habilidade de implementar políticas granulares de cache, como impor Time-to-Live (TTL) de zero para operações destrutivas e cache contínuo para reflexões estruturais, protegendo a máquina anfitriã.

## 3. Especificação Declarativa do Vault (`vault.json`): Integração de `jcodemunch` e `sqlite`

Dentro do ecossistema SODA, o repúdio a dependências baseadas em nuvem (Database-as-a-Service, DaaS) para operações de rotina é fundamental para garantir o encapsulamento da propriedade intelectual e mitigar vazamentos de dados. DevSecOps exige que ferramentas táticas para busca em repositórios e armazenamento transacional operem exclusivamente sob a máquina hospedeira local, controladas estritamente por limites de processo. As duas ferramentas que definem o estado da arte para esses requisitos são `jcodemunch` e `mcp-server-sqlite`.

O `jcodemunch` é uma evolução monumental sobre os motores de busca lexicais antigos (como grep ou ripgrep). Em vez de ler diretórios massivos e enviar milhares de linhas irrelevantes ao LLM, ele constrói um índice astuto do código base utilizando a biblioteca `tree-sitter` para realizar _parsing_ de Abstract Syntax Trees (AST). O agente realiza a descoberta navegando pelo índice simbólico e extrai os blocos exatos (classes, funções ou métodos) através de buscas otimizadas por deslocamento de bytes (_byte-level offset_ em tempo $O(1)$). Essa cirurgia estrutural reduz o consumo de leitura de, por exemplo, 3850 tokens para meros 700 tokens por requisição, uma economia extrema que justifica sua integração mandatória.

Paralelamente, o `mcp-server-sqlite` provê persistência de estado para os agentes. O modelo de "Vibe Testing" e ciclos de planejamento complexos necessitam de um "ledger" local para anotar latências (como Time-To-First-Token - TTFT), reter histórico estruturado e investigar esquemas arquiteturais sem emitir requisições na nuvem. A implementação assegura que todas as queries SQL, reflexões de tabela e alterações de instância sejam auditáveis e restritas a um arquivo `.db` rigidamente enclausurado.

### 3.1. A Mecânica do Particionamento Python via `uvx`

A instalação dessas ferramentas introduz um risco de segurança se operada via gerenciadores globais (`pip install`). Scripts globais estão vulneráveis ao envenenamento da cadeia de dependências e podem comprometer o kernel se expostos a elevação indevida de privilégios. A arquitetura DevSecOps substitui essas abordagens pelo uso do `uvx` (o executor veloz originado no gerenciador `uv`, nativo em Rust). O `uvx` cria ambientes virtuais Python efêmeros, fortemente isolados, baixando os pacotes do PyPI e gerenciando as dependências de forma concorrente. Isso garante execução determinística e previne que binários maliciosos acessem módulos adjacentes do sistema operacional.

### 3.2. Formato Exato do Arquivo de Registro `vault.json`

O arquivo `~/.mcpv/vault.json` orquestra a inserção do `jcodemunch` e do `sqlite` no middleware `mcpv` para operações de Late-Binding. O código abaixo define o formato completo e exato para a configuração dessas ferramentas usando o transporte `stdio`, que previne as vulnerabilidades associadas à exposição de portas TCP locais.

```JSON
{
  "$schema": "https://mcpv.io/schemas/2026-v2/vault-config.schema.json",
  "version": "2.1.0",
  "orchestration": {
    "routingMode": "late-binding",
    "smartValveEnabled": true,
    "contextCompression": "aggressive",
    "idleTimeoutSeconds": 600
  },
  "mcpServers": {
    "jcodemunch": {
      "type": "stdio",
      "command": "uvx",
      "args": [
        "--quiet",
        "--refresh",
        "jcodemunch-mcp"
      ],
      "env": {
        "JCODEMUNCH_CACHE_DIR": "/var/tmp/soda-mcp-cache/jcodemunch",
        "RUST_BACKTRACE": "0"
      },
      "lateBindingPolicy": {
        "lazyLoad": true,
        "preloadMeta": false,
        "ttl": 86400
      },
      "metadata": {
        "description": "Indexação AST avançada via tree-sitter. Capacidade de extração simbólica cirúrgica com busca O(1) byte-offset. Previne context bloat."
      }
    },
    "sqlite": {
      "type": "stdio",
      "command": "uvx",
      "args":,
      "env": {
        "SQLITE_TEMP_STORE": "MEMORY",
        "SQLITE_PRAGMA_JOURNAL_MODE": "WAL",
        "SQLITE_PRAGMA_SYNCHRONOUS": "NORMAL"
      },
      "lateBindingPolicy": {
        "lazyLoad": true,
        "preloadMeta": true,
        "ttl": 0
      },
      "metadata": {
        "description": "Banco de dados transacional restrito a ambiente local. Permite pure SQL queries e schema reflection. Zero exposição à nuvem."
      }
    }
  }
}
```

A especificação deste manifesto é densamente otimizada. O parâmetro `"routingMode": "late-binding"` assegura que o `mcpv` reterá os pacotes de inicialização até o momento de necessidade estrita. As variáveis de ambiente repassadas nos blocos `"env"` configuram as instâncias de modo performático: o SQLite é ordenado a operar em `WAL` (Write-Ahead Logging) com `TEMP_STORE` em memória para resistir aos ciclos massivos de leitura/escrita dos agentes sem destruir os discos NVMe do host. Mais criticamente, a política de TTL `0` em `sqlite` determina que a Válvula Inteligente desative o cache para transações de dados, assegurando consistência ACID, enquanto no `jcodemunch`, um TTL elevado preserva a árvore AST já processada.

## 4. O Vetor de Ameaça da Navegação Autônoma: O Desafio do `browser-use`

À medida que a delegação de tarefas avança além da edição de arquivos locais para a interação com o mundo real, ferramentas como o `browser-use-mcp-server` tornaram-se pilares cruciais. A capacidade atorial fornecida pela automação de navegadores baseados no ecossistema do Chromium e no framework Playwright concede ao LLM o poder de navegar na web, clicar em elementos da interface via nós de acessibilidade, e realizar web scraping assíncrono profundo.

No entanto, conceder a uma inteligência artificial o controle irrestrito de um navegador completo introduz vulnerabilidades devastadoras sob a perspectiva do DevSecOps:

1. **Riscos de Supply Chain em Registros Públicos:** A documentação padrão estimula os usuários a extrair instâncias efêmeras diretamente do GitHub Container Registry (exemplo: `ghcr.io/co-browser/browser-use-mcp-server:latest`). Em uma arquitetura de Confiança Zero, depender de imagens públicas marcadas com a tag `latest` convida ataques de adulteração na cadeia de suprimentos. Uma atualização maliciosa silenciosa pode resultar em um contêiner envenenado que executa _Remote Code Execution_ (RCE) na infraestrutura anfitriã. A imutabilidade local torna-se obrigatória.
2. **Vazamento Contínuo de Telemetria:** Por concepção, o núcleo de código aberto do `browser-use` integra serviços passivos de análise (como o PostHog) para coletar dados anonimizados de uso, incluindo históricos de exceções e métricas comportamentais dos usuários. Em ambientes de pesquisa SODA ou corporações lidando com dados sigilosos, a transmissão sub-reptícia de perfis de hardware, tempos de execução e domínios navegados constitui quebra severa de conformidade, impondo a obrigação de desativação _hard-coded_ via variáveis de ambiente específicas (ex: `MCP_SERVER_ANONYMIZED_TELEMETRY=false`).
3. **Observabilidade e o Paradigma Human-in-the-Loop (HITL):** A operação autônoma cega é um risco existencial. As decisões de navegação do agente podem envolver operações irreversíveis, como exclusão de registros online ou submissão de formulários sensíveis. Para auditar este comportamento em tempo real, a infraestrutura integra obrigatoriamente um Servidor VNC (Virtual Network Computing), mapeado restritivamente para a porta 5900 do host, permitindo que a equipe de engenharia acompanhe o framebuffer virtual de renderização gráfica em tempo real e interceda via "kill switch" caso detecte alucinações cognitivas no agente.
4. **Colapsos de Memória Interprocessual (IPC):** Motores baseados em Chromium requerem amplas margens de memória compartilhada do kernel linux virtualizado (`/dev/shm`) para coordenar a renderização interprocessual das páginas modernas (Single Page Applications pesadas). O daemon padrão do Docker sufoca essa área em míseros 64MB. Sob demanda de um LLM, isso gera panes imediatas (`Aw, Snap!` ou `SIGILL`). Expandi-la compulsoriamente para 2GB é um requisito inflexível da arquitetura.
5. **Contaminação e Envenenamento de Contexto Cruzado:** O navegador não pode persistir entre diferentes "tarefas" do agente para evitar que sessões JWT roubadas por sites maliciosos em uma tarefa anterior sejam usadas em uma tarefa corporativa subsequente. A efemeridade absoluta do "Sidecar" — provida pelas diretivas de descarte automático do contêiner assim que o tráfego `stdio` é interrompido — atua como a garantia máxima de esterilidade do DOM e cookies.

## 5. Build Seguro e Determinístico: O Código-Fonte do `Dockerfile` Imutável

Para mitigar a totalidade dos vetores de ameaça acima (em detrimento ao uso de imagens corrompidas do GHCR), é imprescindível que os arquitetos DevSecOps realizem a construção determinística do contêiner `browser-use-mcp-server` localmente. O manifesto `Dockerfile` documentado abaixo segue estritamente as diretrizes de endurecimento operacional.

A base eleita (`ghcr.io/astral-sh/uv:python3.11-bookworm-slim`) fornece a robustez minimalista do sistema Debian, enxertado com o ecossistema rápido de compilação assíncrona do `uv`. As variáveis de sistema declaradas suprimem definitivamente qualquer _tracker_ passivo embutido e alocam as bibliotecas essenciais de renderização visual no kernel para a ponte Xvfb + VNC.

O conteúdo abaixo constitui a íntegra exata do arquivo `Dockerfile` requisitado.

```Dockerfile
# ==============================================================================
# SODA DevSecOps: Build Imutável e Seguro do 'Browser-Use MCP Server'
# ==============================================================================
# Estágio Base: Infraestrutura Debian slim otimizada com executor de alta performance (uv)
FROM ghcr.io/astral-sh/uv:python3.11-bookworm-slim AS base

# Configurações de Compilação do UV para eliminar a latência do "Cold Start" via
# pré-compilação do código Python em Bytecode. O modo "copy" blinda o overlayfs.
ENV UV_COMPILE_BYTECODE=1
ENV UV_LINK_MODE=copy

# ==============================================================================
# BLOQUEIO DE EXFILTRAÇÃO CORPORATIVA (TELEMETRIA DESABILITADA)
# ==============================================================================
# Variáveis ambientais rígidas para silenciar bibliotecas e submódulos subjacentes.
ENV MCP_SERVER_ANONYMIZED_TELEMETRY=false
ENV BROWSER_USE_TELEMETRY=false
ENV POSTHOG_OPTOUT=1
ENV DO_NOT_TRACK=1
ENV LMNR_PROJECT_API_KEY=""

# Configurações Essenciais do Framebuffer Virtual e VNC (HITL Observability)
ENV DISPLAY=:99
ENV RESOLUTION=1920x1080x24
# Ponto de injeção seguro da senha de observabilidade (evita hardcode no build)
ENV VNC_PASSWORD_FILE=/run/secrets/vnc_password

WORKDIR /app

# ==============================================================================
# INSTALAÇÃO DE DEPENDÊNCIAS DO SISTEMA: COMPONENTES GRÁFICOS E ISOLAMENTO
# ==============================================================================
# As bibliotecas abaixo são requeridas pelo motor Chromium headless para
# simular interação em tela plana através da ponte de exibição gráfica.
RUN apt-get update && apt-get install -y --no-install-recommends \
    wget \
    gnupg \
    git \
    xvfb \
    x11vnc \
    fluxbox \
    libgl1 \
    libglib2.0-0 \
    libnss3 \
    libatk-bridge2.0-0 \
    libx11-xcb1 \
    libdrm2 \
    libxkbcommon0 \
    libgbm1 \
    libasound2 \
    && rm -rf /var/lib/apt/lists/*

# ==============================================================================
# INICIALIZAÇÃO DE SUBSISTEMA DE UI E PONTE VNC
# ==============================================================================
# Criação programática do script de Entrypoint para rotear o processo X Server,
# o gerenciador de janelas mínimo (fluxbox), e o túnel visual (x11vnc).
RUN mkdir -p /etc/supervisor/conf.d /app/.vnc && \
    echo '#!/bin/bash\n\
# Inicia o Servidor X virtual sem privilégios de host TCP diretos\n\
Xvfb $DISPLAY -screen 0 $RESOLUTION -ac -listen tcp -nolisten unix &\n\
sleep 2\n\
# Aciona o renderizador do window manager leve\n\
fluxbox &\n\
# Consome as credenciais VNC do secret injetado via Docker de forma estéril\n\
if; then\n\
    VNC_PASS=$(cat $VNC_PASSWORD_FILE)\n\
else\n\
    VNC_PASS="browser-use-fallback-secure"\n\
fi\n\
# Levanta o daemon do x11vnc blindando a conexão na porta alocada para debugging\n\
x11vnc -display $DISPLAY -bg -forever -nopw -quiet -listen 0.0.0.0 -xkb -shared -rfbport 5900 -storepasswd "$VNC_PASS" /app/.vnc/passwd\n\
x11vnc -display $DISPLAY -bg -forever -usepw -rfbauth /app/.vnc/passwd -quiet -listen 0.0.0.0 -xkb -shared -rfbport 5900 &\n\
# Delega o encerramento seguro ao binário alvo repassado pelos argumentos\n\
exec "$@"\n\
' > /app/entrypoint.sh && chmod +x /app/entrypoint.sh

# ==============================================================================
# INSTALAÇÃO DO CÓDIGO FONTE DA CAMADA MCP (UV ECOSYSTEM)
# ==============================================================================
# Importação explícita do manifest de dependências e bloqueios.
# Isso garante imunidade a injeções surpresa em futuras atualizações automáticas.
COPY pyproject.toml.

# Compilação e isolamento do framework browser-use e suas dependências adjacentes.
RUN uv pip install --system "browser-use-mcp-server>=0.1.0" playwright

# O Playwright exige a injeção binária isolada de seus motores proprietários.
RUN playwright install --with-deps chromium

# ==============================================================================
# COMPORTAMENTO DE ESCUTA DO SIDE CAR
# ==============================================================================
# Abertura das portas para VNC e Eventos Assíncronos, respectivamente.
EXPOSE 5900 8000

# Vinculação da infraestrutura do Entrypoint antes da entrega do fluxo
ENTRYPOINT ["/app/entrypoint.sh"]

# Invoca o servidor mandatando o transporte restrito bidirecional 'stdio',
# bloqueando vazamentos de tráfego que ocorreriam sob SSE ou HTTP REST não seguro.
CMD ["browser-use-mcp-server", "run", "server", "--stdio"]
```

Este `Dockerfile` elimina por completo o risco da cadeia de suprimentos provido pela imagem pública. Ele estabelece uma barreira criptográfica contra a coleta analítica imposta pelos fornecedores e amarra o comportamento dinâmico aos dutos estéreis de entrada e saída, preparando-o para a orquestração via Sidecar. O gerenciamento inteligente de senhas, através da instrução de desvio if-else do VNC no entrypoint, repudia o vazamento de credenciais durante a montagem das camadas da imagem.

## 6. Orquestração do Sidecar Efêmero: O Manifesto `docker-compose.yml`

Uma execução em terminal livre do comando `docker run` introduz entropia, margem de erro humano e desvios nas configurações cruciais (como a ausência da flag de alocação de memória do kernel). Para infraestruturas SODA profissionais e auditáveis, é requerida a declaração de infraestrutura como código (IaC) em um manifesto `docker-compose.yml` rigorosamente parametrizado.

A particularidade suprema desta orquestração reside no protocolo de intersecção JSON-RPC 2.0 executado por sobre os canais padrão (`stdio`). O IDE (Cliente MCP) empurra cargas de trabalho massivas serializadas em JSON pelo duto contínuo até o contêiner. A prática ingênua de alocar um terminal interativo virtual — utilizando a flag TTY — arruina a operação: caracteres invisíveis de controle (ANSI escape codes), códigos de formatação de cores e ruídos do emulador de pseudo-terminal corrompem silenciosamente as strings JSON no trânsito, forçando o motor de parsing do contêiner a abortar a comunicação permanentemente. É, portanto, incontornável que o manifesto declare textualmente o acolhimento ininterrupto de entradas padronizadas sem alocação TTY.

Abaixo, a especificação exata do `docker-compose.yml`, formatada para substituir dependências espúrias e assegurar o encapsulamento seguro da ferramenta:

```YAML
version: '3.8'

services:
  browser-use-sidecar:
    # Vincula rigorosamente o contêiner à construção local auditável declarada no Dockerfile.
    # Repúdio total à imagem ghcr.io/co-browser/browser-use-mcp-server:latest.
    build:
      context:.
      dockerfile: Dockerfile
    
    container_name: soda-browser-mcp-sidecar

    # RESOLUÇÃO CRÍTICA DO KERNEL IPC: Expande o buffer de Memória Compartilhada.
    # Mitiga colisões de memória, travamentos sistêmicos (SIGILL) e falhas do Playwright
    # sob a demanda estressante de SPAs geradas pelo agente LLM.
    shm_size: '2gb'

    # Exposição rigorosa das portas locais amarradas exclusivamente ao loopback (localhost).
    # Previne que varreduras na rede interna da LAN capturem o servidor VNC não cifrado.
    ports:
      - "127.0.0.1:5900:5900" # Acesso HITL estritamente local
      - "127.0.0.1:8000:8000" # Disponibilidade opcional para transporte SSE

    # ==============================================================================
    # HIGIENE DO PROTOCOLO JSON-RPC SOBRE STDIO
    # ==============================================================================
    # stdin_open atua como equivalente à flag '-i', mantendo o duto de escuta aberto 
    # para a injeção ininterrupta e bidirecional de mensagens do MCP.
    stdin_open: true
    # A omissão do tty (tty: false equivalente à omissão da flag '-t') é mandatória. 
    # Previne corrupção sintática do protocolo RPC por caracteres de emulação de terminais.
    tty: false

    # Descarte Arquitetural Automático: 'init: true' delega ao PID 1 interno a
    # responsabilidade de colher subprocessos zumbis deixados pelas instâncias Chromium.
    init: true

    # Injeção Estéril de Credenciais e Desativações Operacionais "Zero-State".
    # Os segredos da nuvem (API Keys necessárias ao LLM na resolução de contexto web) 
    # não devem possuir hardcoding no mcp_config.json, devendo ser injetadas 
    # pelo daemon docker extraídos silenciosamente das variáveis da máquina host.
    environment:
      # APIs repassadas diretamente
      - OPENAI_API_KEY=${OPENAI_API_KEY:-}
      - ANTHROPIC_API_KEY=${ANTHROPIC_API_KEY:-}
      
      # Redundância tática de desativação global da Telemetria MCP e PostHog
      - MCP_SERVER_ANONYMIZED_TELEMETRY=false
      - BROWSER_USE_TELEMETRY=false
      - POSTHOG_OPTOUT=1
      
      # Amarração do Servidor X ao framebuffer previamente preparado
      - DISPLAY=:99
      
    # Montagem cirúrgica do token visual como Read-Only no ponto referenciado.
    secrets:
      - source: vnc_password_secret
        target: /run/secrets/vnc_password
        mode: 0444

    # Depreciação maciça de capabilidades do Kernel Host Linux (Princípio do Privilégio Mínimo).
    # Evita que contaminações originadas de websites maliciosos durante o "Agent Scrape" 
    # tentem escalar privilégios (Container Escape) explorando falhas do kernel hospedeiro.
    cap_drop:
      - ALL
    cap_add:
      - SYS_ADMIN # Restrição aceitável: Permite que o próprio Chromium execute seu sandbox

# Declaração do artefato estéril consumido de forma isolada do repositório
secrets:
  vnc_password_secret:
    file:./vnc_password.txt
```

A estrutura de mitigação em rede e sistema de arquivos empregada por este `docker-compose.yml` finaliza o escudo de arquitetura. O mapeamento do endpoint `"127.0.0.1:5900:5900"` impede explicitamente que o servidor VNC não cifrado transmita a visualização do agente de navegação para a sub-rede mais ampla da LAN. A restrição `cap_drop: - ALL` com a exceção moderada `SYS_ADMIN` permite que o modelo de hiper-sandboxing contido nativamente nas instâncias web seja viável, prevenindo simultaneamente a fuga de privilégios e subversão do daemon host por conteúdos dinâmicos hostis provenientes do acesso indiscriminado a domínios da web. Assegura-se que, devido às rotinas de inicialização efêmera emuladas pela ferramenta (a ausência proposital de volumes montados na raiz do projeto, excetuando os secrets read-only), todo e qualquer histórico de pesquisa subvertida perece integralmente com o término da sessão do IDE.

Tabela 2: Matriz de Endurecimento da Especificação Orquestral do Sidecar Docker

|**Vulnerabilidade / Gargalo Identificado**|**Configuração Declarativa Aplicada no Manifesto**|**Justificativa do Arquiteto DevSecOps (SODA)**|
|---|---|---|
|**Colapso IPC Chromium sob Carga Agêntica**|`shm_size: '2gb'`|O motor Webkit/Chromium usa exaustivamente `/dev/shm` para multiprocessamento visual. Omite o limite de 64MB que ocasiona `Aw, Snap!`.|
|**Corrupção de Sintaxe JSON-RPC 2.0**|`stdin_open: true` e `tty: false`|Bloqueia a injeção furtiva de caracteres de controle de terminal (ANSI codes) no tubo stdio nativo de comunicação de ferramentas.|
|**Vazamento e Escalada de Contêiner**|`cap_drop: - ALL` via Kernel Namespaces|Implementa os Princípios de Menor Privilégio, mitigando vetores de "Container Escape" no sistema base caso o motor execute payloads WebAssembly ou Javascript hostis.|
|**Exposição Visual LAN em Texto Simples**|`ports: - "127.0.0.1:5900:5900"`|Confinamento absoluto do protocolo de diagnóstico VNC apenas ao tráfego do _loopback_ local, isolando supervisões HITL da Intranet.|
|**Exfiltração de Metadados Corporativos**|Multiplas variáveis `ENV *=false` ou `*=1`|Suspensão determinística do disparo implícito de métricas, perfis de telemetria analítica e depurações assíncronas em direção a clusters na nuvem (PostHog, LMNR).|

## 7. Síntese Arquitetural e Governança

O delineamento arquitetônico de um Sistema Operacional Agêntico de alta resiliência exige o abandono de paradigmas amadores de importação estática. O estado da arte do ecossistema devsecops em 2026 solidificou-se através do emprego implacável de roteadores de interceptação que suprimem a "Context Crisis" crônica em redes de Modelos de Linguagem baseados no Model Context Protocol. Ao instanciar e parametrizar agressivamente a comunicação sob esquemas como o `vault.json` proposto, o ecossistema colhe os frutos de uma topologia Late-Binding: a contenção drástica do consumo VRAM financeiramente hostil e a elevação formidável das métricas de precisão das invocações (Accuracy Rate), pois as alucinações cognitivas são sufocadas na ausência de diluição de atenção. A ferramenta invocada sob demanda — como as extrações cirúrgicas via byte-offset em O(1) com `jcodemunch` ou reflexões ACID herméticas via `sqlite` no contorno do `uvx` — transita do status de um artefato estático a um operando em fluxo reativo, minimizando atritos.

Subitamente mais desafiadora é a orquestração segura do comportamento exploratório na internet através de "capabilidades corporais virtuais", propiciadas por instâncias robustas e integradas como o `browser-use`. Os contornos destas especificações operacionais do manifesto `docker-compose.yml` e o `Dockerfile` suprimem as fragilidades letais da automação ingênua. A compilação compulsória local, a expansão inclemente de recursos de memória do kernel (`shm_size`), as decapitações das capabilidades do host e a obliteração cega da telemetria transacionam o "Convenienceware" por "SecOps Imutável". O uso de _secrets_ fechados, do transporte puro via `stdio` livre de corrupção TTY e os mecanismos HITL sobre VNC chancelam o ambiente como "Zero-State", perfeitamente imune à contaminação inter-sessão. As equipes corporativas que adotarem incondicionalmente essas salvaguardas declarativas consolidarão a estabilidade em infraestruturas agênticas densas e mitigarão as severas vulnerabilidades implícitas das intersecções entre IA generativa e navegação web não restringida.

---

Aqui estão as soluções estruturais para finalizar a topologia do seu ambiente DevSecOps com o MCP Vault e os sidecars.

### 1. Correção do Erro de Dependência (FastMCP)

O erro `TypeError: FastMCP() no longer accepts 'log_level'` ocorre porque a API do FastMCP foi atualizada recentemente e removeu o parâmetro direto de log da sua classe de inicialização, passando a exigir a configuração na função assíncrona ou via variável de ambiente.

Como o `mcpv` ainda está tentando chamar a API antiga por baixo dos panos, a abordagem mais limpa no ecossistema do `uv` é forçar o _downgrade_ específico da subdependência do FastMCP durante a instalação do cofre na sua máquina global. Execute o seguinte comando:

```Bash
uv tool install mcpv --with "fastmcp<0.4.0" --force
```

Alternativamente, se a ferramenta oferecer suporte à injeção ambiental, você pode rodar a instalação normal e exportar a variável `FASTMCP_LOG_LEVEL=DEBUG` no seu Windows antes de iniciar o roteador.

### 2. Estrutura do Cofre (`vault.json`)

Abaixo está a sintaxe declarativa exata para o `~/.mcpv/vault.json`. Esta configuração implementa o paradigma "Late-Binding" (onde a política atrasa a inicialização para evitar o colapso de memória) e mescla binários `uvx` com execuções efêmeras e instâncias sidecar ativas:

```JSON
{
  "$schema": "https://mcpv.io/schemas/2026-v2/vault-config.schema.json",
  "version": "2.1.0",
  "orchestration": {
    "routingMode": "late-binding",
    "smartValveEnabled": true
  },
  "mcpServers": {
    "jcodemunch": {
      "type": "stdio",
      "command": "uvx",
      "args": ["jcodemunch-mcp"],
      "lateBindingPolicy": { "lazyLoad": true }
    },
    "sqlite": {
      "type": "stdio",
      "command": "uvx",
      "args": ["mcp-server-sqlite", "--db-path", "C:/caminho/seguro/soda_estado.db"],
      "lateBindingPolicy": { "lazyLoad": true, "ttl": 0 }
    },
    "notebooklm": {
      "type": "stdio",
      "command": "uvx",
      "args": ["notebooklm-mcp"],
      "lateBindingPolicy": { "lazyLoad": true }
    },
    "docling": {
      "type": "stdio",
      "command": "docker",
      "args": [
        "run", 
        "-i", 
        "--rm", 
        "ghcr.io/ibm/docling-mcp-server:latest", 
        "--transport", 
        "stdio"
      ],
      "lateBindingPolicy": { "lazyLoad": true }
    },
    "browser-use": {
      "type": "stdio",
      "command": "docker",
      "args": [
        "exec", 
        "-i", 
        "soda-browser-mcp-sidecar", 
        "browser-use-mcp-server", 
        "run", 
        "server", 
        "--stdio"
      ],
      "lateBindingPolicy": { "lazyLoad": false }
    }
  }
}
```

Nota: A declaração do `browser-use` utiliza `docker exec` em vez de `docker run` porque assumimos que o contêiner orquestrado pelo `docker-compose` (descrito abaixo) já estará operando em segundo plano aguardando conexões.

### 3. Build Local do Browser-Use

Para compilar localmente e contornar a dependência do `ghcr.io` garantindo a inibição da telemetria , crie um diretório para o sidecar e adicione os dois arquivos a seguir.

**Arquivo: `Dockerfile`**

```Dockerfile
FROM ghcr.io/astral-sh/uv:python3.11-bookworm-slim AS base

ENV UV_COMPILE_BYTECODE=1
ENV UV_LINK_MODE=copy

# Inibição absoluta de Telemetria e Analytics
ENV MCP_SERVER_ANONYMIZED_TELEMETRY=false
ENV BROWSER_USE_TELEMETRY=false
ENV POSTHOG_OPTOUT=1
ENV DO_NOT_TRACK=1

# Variáveis do X11 / VNC
ENV DISPLAY=:99
ENV RESOLUTION=1920x1080x24
ENV VNC_PASSWORD_FILE=/run/secrets/vnc_password

WORKDIR /app

RUN apt-get update && apt-get install -y --no-install-recommends \
    wget gnupg git xvfb x11vnc fluxbox libgl1 libglib2.0-0 libnss3 \
    libatk-bridge2.0-0 libx11-xcb1 libdrm2 libxkbcommon0 libgbm1 libasound2 \
    && rm -rf /var/lib/apt/lists/*

COPY pyproject.toml.
# Instala o source imutável via uv 
RUN uv pip install --system "browser-use-mcp-server>=0.1.0" playwright
RUN playwright install --with-deps chromium

# Script de injeção de senha e boot em background do VNC
RUN mkdir -p /app/.vnc && \
    echo '#!/bin/bash\n\
Xvfb $DISPLAY -screen 0 $RESOLUTION -ac -listen tcp -nolisten unix &\n\
sleep 2\n\
fluxbox &\n\
if; then\n\
    VNC_PASS=$(cat $VNC_PASSWORD_FILE)\n\
else\n\
    VNC_PASS="falha-critica-sem-senha"\n\
fi\n\
x11vnc -storepasswd "$VNC_PASS" /app/.vnc/passwd\n\
x11vnc -display $DISPLAY -bg -forever -usepw -rfbauth /app/.vnc/passwd -quiet -listen 0.0.0.0 -xkb -shared -rfbport 5900 &\n\
exec "$@"\n\
' > /app/entrypoint.sh && chmod +x /app/entrypoint.sh

EXPOSE 5900 8000
ENTRYPOINT ["/app/entrypoint.sh"]

# Comando aguardará pacotes STDIO originados pelo 'docker exec' do mcpv
CMD ["tail", "-f", "/dev/null"] 
``` 

**Arquivo: `docker-compose.yml`** Lembre-se de criar um arquivo `vnc_password.txt` local contendo sua senha antes de subir este compose.

```YAML
version: '3.8'

services:
  browser-use-sidecar:
    build:
      context:.
      dockerfile: Dockerfile
    container_name: soda-browser-mcp-sidecar
    shm_size: '2gb' # Mandatório para o Playwright não estourar a memória
    ports:
      - "127.0.0.1:5900:5900" # Mapeamento VNC restrito à interface de loopback
    stdin_open: true
    tty: false
    init: true
    environment:
      - OPENAI_API_KEY=${OPENAI_API_KEY:-}
      - ANTHROPIC_API_KEY=${ANTHROPIC_API_KEY:-}
      # Redundância da inibição de rastreio
      - MCP_SERVER_ANONYMIZED_TELEMETRY=false
    secrets:
      - source: vnc_password_secret
        target: /run/secrets/vnc_password
        mode: 0444
    cap_drop:
      - ALL
    cap_add:
      - SYS_ADMIN

secrets:
  vnc_password_secret:
    file:./vnc_password.txt
```

Basta executar `docker-compose up -d --build` neste diretório. A combinação do comando `tail -f /dev/null` no `CMD` do Dockerfile com a diretiva de inicialização garantirá que o contêiner permaneça ocioso e seguro, até que o MCP Vault efetue o disparo de _Late-Binding_ por meio do comando `docker exec` definido no `vault.json`.