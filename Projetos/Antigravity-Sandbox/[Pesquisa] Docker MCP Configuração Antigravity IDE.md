---
aliases: []
---
# Orquestração de Imagens Docker MCP para o Projeto SODA: Arquitetura de Sidecars Efêmeros no Antigravity IDE

## Plano Direto de Implementação (Sumário Executivo)

Em atendimento direto aos requisitos operacionais para a configuração do arquivo `mcp_config.json` no Antigravity IDE, focado na execução do `docling-mcp-server` e do `co-browser-browser-use-mcp-server` como sidecars efêmeros via Docker, apresenta-se a resolução imediata do erro "image missing / repository does not exist".

### 1. O Caminho Exato do Registry

**Ferramenta 1: Browser-Use MCP Server** O projeto `co-browser` publica e mantém ativamente a imagem oficial pré-compilada contendo o servidor MCP, Chromium, dependências do Playwright e servidor VNC no GitHub Container Registry.

- **URL/Tag Oficial:** `ghcr.io/co-browser/browser-use-mcp-server:latest`
- **Comando Pull:** `docker pull ghcr.io/co-browser/browser-use-mcp-server:latest`.

**Ferramenta 2: IBM Docling MCP Server** O projeto oficial IBM Docling publica imagens pré-compiladas sob o nome `docling-serve` (focadas em API HTTP/FastAPI), mas **não** possui uma imagem oficial dedicada e pré-compilada contendo estritamente o pacote `docling-mcp` para execução local via `stdio`. A documentação oficial orienta o uso do gerenciador de pacotes Python `uv` (`uvx --from docling-mcp docling-mcp-server --transport stdio`). Portanto, para o encapsulamento estrito em Docker exigido pelo projeto SODA, é mandatório o Build Local.

### 2. Instruções de Build Local (IBM Docling MCP)

Para gerar a imagem local do Docling focada no protocolo MCP sobre `stdio`, execute a seguinte sequência de comandos no terminal da sua máquina hospedeira. Este processo utiliza a imagem oficial de alta performance do `uv` como base e isola o pacote necessário.

```Bash
# Crie um diretório temporário para o build
mkdir -p build-docling-mcp && cd build-docling-mcp

# Gere o arquivo Dockerfile
cat << 'EOF' > Dockerfile
FROM ghcr.io/astral-sh/uv:bookworm-slim
ENV UV_COMPILE_BYTECODE=1
ENV UV_LINK_MODE=copy
# Atualização de pacotes sistêmicos vitais para manipulação de PDFs e OCR
RUN apt-get update && apt-get install -y --no-install-recommends \
    libgl1 libglib2.0-0 \
    && rm -rf /var/lib/apt/lists/*
# Instalação isolada do servidor MCP do Docling
RUN uv tool install docling-mcp
# Injeção do caminho binário do uv tool no PATH do container
ENV PATH="/root/.local/bin:${PATH}"
# Ponto de entrada padrão
ENTRYPOINT ["docling-mcp-server"]
EOF

# Execute o build local atribuindo a tag desejada
docker build -t docling-mcp-server:local.
```

(O `browser-use-mcp-server` não requer build local, pois a imagem `ghcr.io/co-browser/browser-use-mcp-server:latest` está perfeitamente funcional para esta arquitetura ).

### 3. Correção do JSON para o Antigravity IDE

O arquivo `mcp_config.json` do Antigravity (localizado em `~/.gemini/antigravity/mcp_config.json`) invoca executáveis em processos filhos usando a interface `child_process.spawn` do Node.js. O bloco abaixo corrige as rotas, aplica a invocação `docker run -i --rm`, garante o transporte `stdio`, e, crucialmente para o browser, aloca a memória compartilhada necessária (`--shm-size=2g`) para evitar falhas do Chromium no Docker.

_Nota de Segurança DevSecOps:_ O Antigravity não suporta interpolação de shell (`${VAR}`) nativamente na string de `args`. Credenciais devem ser passadas no objeto `"env"` da configuração e injetadas no container via a flag `-e` do Docker.

```JSON
{
  "mcpServers": {
    "docling-mcp-server": {
      "command": "docker",
      "args": [
        "run",
        "-i",
        "--rm",
        "--name", "soda-ephemeral-docling",
        "docling-mcp-server:local",
        "--transport",
        "stdio"
      ]
    },
    "co-browser-browser-use-mcp-server": {
      "command": "docker",
      "args":,
      "env": {
        "OPENAI_API_KEY": "INSIRA_SUA_CHAVE_AQUI",
        "ANTHROPIC_API_KEY": "INSIRA_SUA_CHAVE_AQUI"
      }
    }
  }
}
```

---

## Relatório Arquitetural e Análise DevSecOps Detalhada

A adoção de agentes autônomos integrados diretamente em Ambientes de Desenvolvimento Integrados (IDEs), como o Antigravity, representa uma mudança sísmica na engenharia de software. O Model Context Protocol (MCP) emerge como o padrão universal da indústria, projetado para mediar a comunicação entre Modelos de Linguagem Grande (LLMs) e ecossistemas de ferramentas, fontes de dados e ambientes de execução externos. No entanto, a extensão das capacidades do agente para abranger o processamento intensivo de documentos com Inteligência Artificial (IBM Docling) e a navegação web autônoma (Browser-Use) introduz complexidades substanciais de infraestrutura, gerenciamento de dependências e, primariamente, segurança.

O projeto SODA estabelece um requisito estrito: a execução destas "ferramentas pesadas" deve ocorrer exclusivamente como Sidecars Efêmeros no Docker. Esta decisão arquitetural é não apenas prudente, mas alinhada com os mais rigorosos padrões de DevSecOps para sistemas baseados em agentes. A análise que se segue detalha exaustivamente as raízes do erro de repositório enfrentado na configuração inicial, dissecando os ecossistemas do Docling e do Browser-Use, a engenharia de imagens Docker necessária, os mecanismos de transporte JSON-RPC, e as idiossincrasias do Antigravity IDE no gerenciamento de processos e credenciais.

## O Paradigma de Orquestração: Sidecars Efêmeros em Ambientes Agentivos

O conceito de um "Sidecar Efêmero" no contexto do Model Context Protocol (MCP) refere-se à instanciação de um contêiner Docker isolado, empacotando todas as dependências complexas de um servidor MCP, cujo ciclo de vida está estritamente acoplado e restrito à duração da sessão de comunicação do cliente (o agente de IA no IDE).

A execução de bibliotecas pesadas de Python (que requerem bindings de C++, modelos de Machine Learning locais, motores de OCR, binários headless de navegadores e bibliotecas de processamento de imagem) diretamente no sistema operacional hospedeiro (host) gera uma entropia de software massiva. Conflitos de versões do Node.js, incompatibilidades do Python (ex: o Docling abandonou suporte ao Python 3.9 ), colisões de binários do Chromium e a contaminação global de pacotes (`pip install` global) tornam a configuração tradicional de servidores MCP insustentável em ambientes de engenharia de larga escala.

### Benefícios Críticos do Abordagem Docker (`docker run -i --rm`)

A diretiva `docker run -i --rm` invocada pelo arquivo `mcp_config.json` fornece as garantias fundamentais para esta arquitetura:

1. **Isolamento de Estado e Destruição Garantida (`--rm`):** A flag `--rm` instrui o daemon do Docker a destruir o contêiner, seu sistema de arquivos gravável e seus metadados de rede instantaneamente após a terminação do processo principal (neste caso, o encerramento do pipe `stdio` pelo IDE). Para ferramentas como o Docling, que realizam o cache temporário de documentos PDF para otimização de performance , ou o Browser-Use, que acumula cookies de sessão, histórico de navegação e artefatos de estado do DOM , esta destruição garante uma total higienização entre sessões. O agente de IA sempre inicia com um ambiente limpo e imutável, impossibilitando vazamentos de dados horizontais ou envenenamento de contexto entre tarefas (cross-task poisoning).
2. **Blindagem de Segurança (Sandboxing):** Agentes de IA munidos de ferramentas de navegação web e processamento de arquivos arbitrários operam em zonas de alto risco. O processamento de um PDF malicioso ou a navegação autônoma para um domínio comprometido pode resultar em execução remota de código (RCE). O contêiner Docker atua como uma sandbox de nível de kernel (usando namespaces e cgroups), impedindo que o código em execução no servidor MCP alcance o sistema de arquivos do projeto do desenvolvedor no Antigravity IDE, a menos que volumes específicos sejam montados.
3. **Comunicação Interprocessos Estrita (`-i`):** A flag `--interactive` (`-i`) garante que o descritor de arquivo `stdin` (entrada padrão) permaneça aberto e conectado ao processo pai (o Antigravity IDE). O MCP utiliza um formato estrito de JSON-RPC 2.0 sobre `stdio`. É imperativo notar que a flag `-t` (`--tty`), comumente usada em execuções manuais do Docker, **deve ser omitida**. A alocação de um pseudo-TTY injetaria caracteres de controle de terminal, códigos de cores ANSI e formatações de retorno de carro que corromperiam o _parser_ JSON do cliente MCP, resultando na falha silenciosa ou desconexão da ferramenta.

|**Aspecto Operacional**|**Execução Nativa (Host)**|**Orquestração Docker Sidecar**|
|---|---|---|
|**Gerenciamento de Dependências**|Alto risco de colisões (Python venvs, Chromium).|Empacotamento hermético, zero conflitos com o Host.|
|**Higiene de Dados**|Requer scripts de limpeza manuais para caches/cookies.|Limpeza automática no encerramento da sessão (`--rm`).|
|**Postura de Segurança**|Acesso total ao file system do desenvolvedor.|Isolamento via namespaces do Linux; privilégios mínimos.|
|**Reproducibilidade**|Variante dependendo do OS (macOS, Windows, Linux).|Comportamento idêntico transversal a qualquer OS que suporte Docker.|

## Dissecção e Engenharia: IBM Docling MCP Server

O projeto Docling, conduzido pelo IBM Research, consolidou-se como um pilar para a ingestão e extração avançada de documentos em ecossistemas de Inteligência Artificial. Ele transcende o mero parseamento de texto de PDFs, oferecendo um entendimento semântico complexo que engloba análise de layout da página, extração precisa de tabelas estruturadas (alcançando níveis de acurácia de 97.9%), detecção e interpretação de códigos e fórmulas matemáticas, e suporte multimodal com Modelos de Linguagem Visual (VLMs) como o GraniteDocling.

A decisão de expor essas capacidades vastas através do Model Context Protocol visa transformar agentes de IA passivos em analisadores documentais autônomos. O `docling-mcp-server` habilita ferramentas (MCP tools) focadas na conversão de documentos para o formato unificado `DoclingDocument`, exportação sem perdas para JSON ou Markdown, e geração de relatórios complexos baseados no conteúdo processado.

### O Erro "Image Missing": Análise do Repositório

O erro encontrado durante a configuração do Projeto SODA ("image missing / repository does not exist") para a URL presuntiva `ghcr.io/docling-project/docling-mcp-server:latest` ilustra um padrão de publicação de software fragmentado.

A equipe do Docling mantém imagens Docker ativas e extremamente detalhadas publicadas tanto no GitHub Container Registry (`ghcr.io`) quanto no `quay.io`. No entanto, estas imagens são publicadas sob a nomenclatura `docling-serve`. O pacote `docling-serve` foi projetado com uma arquitetura fundamentalmente diferente do fluxo de trabalho padrão do MCP para IDEs. Ele levanta um servidor API REST baseado no framework FastAPI, expondo endpoints HTTP convencionais para a submissão de documentos e operando com uma interface de usuário interativa (Swagger UI/Playground) nas portas 5001.

Existem inúmeras tags para esta imagem, diferenciadas por suporte de hardware, como as variantes exclusivas de CPU (`docling-serve-cpu`) e múltiplas matrizes de suporte CUDA para processamento acelerado em GPU (`docling-serve-cu128`, `docling-serve-cu130`), alcançando tamanhos massivos de até 11.4 GB, dada a inclusão do framework PyTorch e modelos de aprendizado profundo. Embora incrivelmente poderosas para implantações de backend conteinerizado em Kubernetes, essas imagens **não** invocam nativamente o protocolo JSON-RPC sobre `stdio` requisitado pelo padrão cliente-servidor de um IDE como o Antigravity.

O servidor MCP propriamente dito é mantido em um subprojeto e pacote Python distinto, o `docling-mcp`. A documentação de integração primária não oferece uma imagem Docker canônica pré-compilada, instruindo os desenvolvedores a utilizarem o binário instalador rápido `uv` diretamente no host, através de comandos como `uvx --from docling-mcp docling-mcp-server --transport stdio`.

### Estratégia de Encapsulamento: O Build Local Customizado

Para adequar o `docling-mcp` ao requisito rigoroso de ser um Sidecar Docker Efêmero invocado pelo Antigravity sem recorrer a _pulls_ dinâmicos de pacotes em tempo de execução (uma prática que viola os princípios de resiliência e velocidade em ambientes de desenvolvimento e _air-gapped_), a construção de uma imagem personalizada, estática e imutável é a solução definitiva.

A elaboração do arquivo `Dockerfile` apresentado no "Plano Direto" engloba práticas otimizadas de containerização de ferramentas Python de Machine Learning:

1. **Imagem Base de Alta Performance:** O uso de `ghcr.io/astral-sh/uv:bookworm-slim` como imagem base fundamental provê um sistema operacional Debian minimalista equipado com a ferramenta `uv`, um gerenciador de projetos e pacotes Python escrito em Rust, conhecido por resoluções de dependências e instalações em velocidade sub-segundo.
2. **Variáveis de Ambiente Estratégicas:** As flags `UV_COMPILE_BYTECODE=1` e `UV_LINK_MODE=copy` forçam a pré-compilação do código Python para bytecode durante a fase de _build_ da imagem, otimizando drasticamente o tempo de inicialização (cold-start) do container quando invocado pelo IDE. O modo de link por cópia garante a integridade dos artefatos dentro das restrições do sistema de arquivos de overlay do Docker.
3. **Dependências de Sistemas C/C++:** Bibliotecas de processamento de PDF e as engrenagens de OCR subjacentes ao Docling requerem frequentemente dependências partilhadas de interface de sistema para manipulação gráfica e renderização. O acréscimo de pacotes como `libgl1` e `libglib2.0-0` previne falhas obscuras de _ImportError_ em tempo de execução ligadas à biblioteca OpenCV ou similares.
4. **Pathing e Entrypoint Estrito:** Ao instalar as ferramentas globais via `uv tool install docling-mcp`, os executáveis resultantes residem em `/root/.local/bin`. Esta injeção de caminho PATH é combinada com o `ENTRYPOINT ["docling-mcp-server"]`, garantindo que qualquer argumento subsequente transmitido pelo JSON de configuração do Antigravity (como `--transport stdio`) seja concatenado diretamente ao binário do MCP Server.

## Dissecção e Engenharia: Browser-Use MCP Server

Se o processamento de arquivos forma a espinha dorsal analítica de um agente de IA, a navegação autônoma na Web representa seus olhos e mãos no mundo exterior. Tradicionalmente, o acesso de LLMs à internet era restrito a comandos `curl` simples, ferramentas de raspagem de HTML estático ou retornos textuais simplificados, que falham catastroficamente na era moderna de _Single Page Applications_ (SPAs), websites renderizados dinamicamente via JavaScript, e barreiras contra bots.

O projeto `browser-use` revolucionou a interação agentiva ao introduzir a "extração de árvores de acessibilidade" (Accessibility Tree extraction). Ao invés de despejar incontáveis kilobytes de código HTML e CSS confusos na janela de contexto de um modelo, o `browser-use` utiliza o framework Playwright para interceptar a representação estruturada que os navegadores constroem para leitores de tela (screen readers). Este método condensa imensamente o uso de tokens (reduções massivas, de quase 1 milhão de caracteres para menos de 8.000 caracteres no caso da Wikipédia, ou de 80.000 para cerca de 3.000 em repositórios do GitHub), fornecendo _landmarks_ (pontos de referência) e elementos interativos rotulados com IDs numéricos (hashes) únicos, permitindo que a IA determine comandos precisos como "click no botão 45" ou "digite texto no input 12".

### O Pacote Oficial no GitHub Container Registry

A orquestração do `browser-use` sob o protocolo MCP foi perfeitamente encapsulada pelo repositório mantido pela comunidade "co-browser", originando o artefato `co-browser-browser-use-mcp-server`. A pesquisa aprofundada confirma que, diferentemente do Docling, este projeto hospeda e mantém proativamente uma imagem Docker compilada no GitHub Container Registry.

A imagem `ghcr.io/co-browser/browser-use-mcp-server:latest` é um ambiente formidável e autocontido que soluciona o erro de ausência reportado. A complexidade desta imagem advém de seus componentes internos empacotados, refletidos no Dockerfile de origem do projeto :

1. **Motor Chromium Integrado:** O container empacota uma instalação robusta dos binários do Chromium juntamente com todas as suas pesadas bibliotecas de dependência de interface de usuário X11, permitindo a execução _headless_ (sem interface nativa gráfica explícita) rigorosa requerida para o Playwright no Linux.
2. **Streaming VNC:** Para fins de depuração (debugging) e observabilidade "human-in-the-loop", a imagem engloba de forma engenhosa um servidor VNC escutando por padrão na porta `5900` e uma interface web secundária, provendo acesso visual em tempo real à navegação autônoma controlada pelo agente.
3. **Bifurcação de Transporte:** O binário MCP foi estruturado arquiteturalmente para operar em modos duplos de comunicação. Ele pode rodar no formato `SSE` (Server-Sent Events) tradicional em uma porta de rede, mas crucialmente suporta um modo imperativo de `stdio`. Para IDEs como o Antigravity, a transição para este modo requer que a CLI repasse as flags exatas: `browser-use-mcp-server run server --stdio`.

### O Ponto de Falha Crítico: Restrições de Memória Compartilhada no Docker

A incorporação do `co-browser-browser-use-mcp-server` em uma arquitetura de sidecar via `docker run` levanta um aspecto crucial de configuração em nível de infraestrutura: os limites de memória compartilhada (shared memory).

As engines de navegação baseadas no projeto Chromium dependem intrinsecamente do diretório em memória `/dev/shm` (Shared Memory) para facilitar e acelerar a comunicação interprocessos (IPC) entre o processo pai do navegador e as múltiplas abas filhas subjacentes ativas (que gerenciam a renderização assíncrona dos sites, os scripts do DOM e o processamento de imagens e _canvas_).

O daemon do Docker, em suas definições protetoras e limitantes padrão de segurança, atribui estritamente apenas 64 Megabytes a esse dispositivo `/dev/shm` montado dentro de novos containers. Ao delegar ao agente autônomo do Antigravity IDE a manipulação do Playwright para carregar aplicações web modernas, complexas e densas em scripts, o processo do Chromium invariavelmente esgota este limite de 64 MB de forma abrupta. Quando isso ocorre, o navegador sofre um travamento silencioso (process crash com códigos como `SIGILL` ou o infame erro "Aw, Snap!"), o servidor MCP não consegue processar os fluxos, e a comunicação JSON-RPC é rompida, provocando o travamento do agente no IDE.

Portanto, a injeção do argumento de runtime `--shm-size=2g` (expandindo a tolerância para 2 Gigabytes) no array `"args"` da configuração JSON é uma imposição técnica não-negociável para assegurar a estabilidade das tarefas agentivas baseadas em browser no ambiente SODA.

## Arquitetura DevSecOps: Credenciais e Limitações do Antigravity IDE

A adoção do Model Context Protocol estende enormemente o escopo do IDE, transformando-o de um manipulador de texto estático em um orquestrador operacional. Contudo, a mecânica com que o Antigravity e clientes afins (como Cursor ou Windsurf) gerenciam a configuração JSON subjacente (`mcp_config.json`) possui falhas de design arquiteturais relacionadas ao provisionamento de segredos, que exigem mitigações táticas específicas.

As ferramentas agentivas descritas interagem em escalas de nuvem que frequentemente requerem autenticação pesada. Em particular, a ferramenta `browser-use` aciona debaixo dos panos Modelos de Linguagem Grande (LLMs) auxiliares — frequentemente a API da OpenAI, da Anthropic ou Gemini — para processar seletivamente o DOM e decidir as próximas etapas lógicas de interação (clicar, digitar, rolar, extrair) antes mesmo de enviar o resumo final consolidado ao agente principal do Antigravity. Essas bibliotecas requerem chaves de API (`OPENAI_API_KEY`, etc.) expostas no ambiente.

### A Ilusão da Interpolação Shell

Um desenvolvedor habituado com pipelines de CI/CD ou Docker Compose tentaria injetar essas variáveis nos argumentos do Docker no arquivo `mcp_config.json` utilizando chaves sintáticas do Bash, por exemplo:

```JSON
"args":
```

Esse paradigma falha e rompe a segurança da integração. A interface de carregamento do Antigravity processa o arquivo `mcp_config.json` interpretando os parâmetros estritamente como strings literais. O IDE não invoca um interpretador shell intermediário (como `bash -c`) que possua a capacidade de ler o sistema e expandir os símbolos `${VAR}`. Como consequência, a string literal exata `"${OPENAI_API_KEY}"` é transmitida para dentro do contêiner Docker, inviabilizando a autenticação da biblioteca.

A segunda tentação operacional é a prática desastrosa — categorizada em auditorias de segurança como falha crítica de Nível 1 — de colar as senhas de texto simples (plain-text tokens) embutidas de maneira permanente (hardcoded) no próprio arquivo de configuração JSON. Tendo em vista que estes arquivos frequentemente acabam sincronizados em nuvens de backup (dotfiles, iCloud, OneDrive) ou inspecionados em relatórios de diagnostico de log, este vetor compromete a postura de segurança do ecossistema SODA.

### A Resolução via Matriz `"env"` e Transmissão de Ponto-a-Ponto

A mecânica de orquestração suportada e homologada aproveita a arquitetura de processos subjacentes. A especificação do cliente MCP no Antigravity permite declarar o objeto secundário `"env"` adjacente à chave `"args"` para a definição da infraestrutura de cada servidor.

JSON

```
"env": {
  "OPENAI_API_KEY": "token_criptografico_real",
  "ANTHROPIC_API_KEY": "token_criptografico_real"
}
```

Quando o IDE executa a invocação do executável principal declarado em `"command"` (que neste contexto é a CLI do `docker`), ele carrega essas chaves estritamente no segmento de variáveis de ambiente do novo processo invisível criado no sistema hospedeiro (host process space). A CLI nativa do Docker, por sua vez, possui uma funcionalidade desenhada para repasse de ponto a ponto: se a instrução de execução for parametrizada com o sinalizador `-e VARIÁVEL` sem fornecer um valor subsequente assinalado com igualdade (` = `), o daemon do Docker pesquisará ativamente e de forma exclusiva o ambiente protegido do processo originário que submeteu o comando.

Se a chave estiver devidamente presente, ela será lida na memória segura, transitada pela interface do soquete do daemon e instanciada privadamente no namespace do sidecar recém forjado (dentro do contêiner do Browser-Use), tudo isso de maneira opaca à listagem visível de argumentos (`argv`) do sistema operacional. Esta dinâmica de injeção garante que as chaves sensíveis persistam apenas em arquivos controlados estritamente pelo desenvolvedor no IDE e permaneçam ocultas da interface `ps aux` ou de logs em níveis superficiais. As configurações JSON dispostas no "Plano Direto" englobam esta técnica precisa para a orquestração segura do Browser-Use.

## Avaliação Comparativa de Transportes: Stdio versus SSE no Contexto Docker

Uma compreensão arquitetural integral do Model Context Protocol necessita um exame das camadas de transmissão de pacotes subjacentes ao protocolo. O fluxo JSON-RPC do MCP transita em dois padrões de implementação divergentes para servidores dockerizados :

| **Mecanismo de Transporte**        | **Stdio (Standard I/O Streams)**                                              | **HTTP / SSE (Server-Sent Events)**                                                  |
| ---------------------------------- | ----------------------------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| **Via de Comunicação**             | Descritores do Sistema (`stdin`/`stdout`).                                    | Sockets TCP/IP, Loopback Localhost.                                                  |
| **Topologia de Rede**              | Totalmente isolada (Zero Port Forwarding).                                    | Exige publicação de portas (`-p 8080:8080`).                                         |
| **Gerenciamento de Ciclo de Vida** | Processo morre organicamente quando a interface IDE colapsa e fecha os pipes. | O Servidor persiste escutando conexões; requer gerenciamento e encerramento manuais. |
| **Restrições de Docker**           | Nenhuma. Extremamente portátil.                                               | Complexidade com políticas CORS e roteamento do localhost (`host.docker.internal`).  |

Para a orquestração em formato de Sidecar Efêmero sob o IDE Antigravity, o padrão **Stdio** é não apenas a convenção da indústria , mas a única via que endossa completamente as garantias operacionais. Como o IDE invoca a cadeia de comandos `docker run -i --rm`, a destruição assíncrona dependente do encerramento de pipe (Pipe Closure) se torna uma automação natural da plataforma.

A ferramenta Browser-Use suporta adequadamente essa topologia, desde que o sufixo `run server --stdio` seja estritamente incluído em substituição aos parâmetros de portal nativo HTTP observados em imagens tradicionais da internet. De forma análoga, a ferramenta Docling foi pré-configurada no `ENTRYPOINT` e `CMD` da imagem local recomendada para aderir à diretriz `--transport stdio`. Se os contêineres vazassem mensagens casuais, log lines ou strings de depuração para o fluxo `stdout`, o processo JSON-RPC perderia sincronia sintática, e o Antigravity rejeitaria abruptamente a comunicação como pacotes malformados. Portanto, a supressão de mensagens do inicializador e da alocação de terminais TTY nas configurações propostas assegura canais limpos para a extração do IBM Docling e a instrumentação Playwright.

## Sumário das Resoluções e Implementação Final

As complexidades de "imagens não encontradas" documentadas na operação de ferramentas pesadas como o `docling-mcp-server` e o projeto `browser-use` são consequências de desalinhamentos nas convenções de publicação das equipes mantenedoras. O `docling` desagrega propositalmente a sua API HTTP (`docling-serve`) de sua interface em protocolo de agência (`docling-mcp`) por questões de desempenho e modularidade, não fornecendo uma imagem unificada pronta no Docker Hub para o caso de uso estrito do Antigravity. Em contrapartida, os empacotadores da comunidade "co-browser" provisionam artefatos altamente elaborados no GitHub Container Registry contendo binários de navegador subjacentes complexos e camadas VNC.

O Projeto SODA se beneficia amplamente de uma matriz arquitetural alicerçada no Docker para conter estes ecossistemas de dependências caóticos. Ao estruturar o IDE Antigravity para se comunicar por `stdio` com instâncias controladas através do comando `docker run -i --rm`, combinando builds locais otimizados pela engine em Rust `uv` para tarefas de ingestão PDF/Layout de documentos, e aplicando blindagens de gerenciamento de shared-memory e injeção discreta de variáveis de ambiente para a interface de automação de navegação autônoma do Chromium, os engenheiros estabelecem um pipeline DevSecOps maduro.

As recomendações de configuração JSON e os planos de compilação Docker explicitados neste relatório resolvem perfeitamente a incompatibilidade relatada, alinhando a eficiência local com as seguranças arquiteturais de um sistema estritamente isolado e efêmero.