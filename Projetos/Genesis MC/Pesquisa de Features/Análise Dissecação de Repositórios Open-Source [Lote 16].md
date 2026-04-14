---
sticker: lucide//pocket
---

# Relatório de Pesquisa e Arquitetura: Canibalização Cirúrgica de Agentes e Gestão de Conhecimento para o SoustractionMC

| **Projeto**             | **Categoria**                | **Risco Principal (O Lixo Tóxico)**                           | **Ouro/Inspiração a Extrair**                                  | **Devemos Canibalizar?**       | **Score** |
| ----------------------- | ---------------------------- | ------------------------------------------------------------- | -------------------------------------------------------------- | ------------------------------ | --------- |
| **Onyx**                | Enterprise RAG / Busca       | Contaminação de escopo; stack de nuvem pesada e acoplada.     | Heurísticas de roteamento de busca híbrida e RBAC.             | **Sim** (Apenas Heurísticas)   | 4.5       |
| **goose**               | Framework de Agentes Nativos | Over-engineering na UI (TS/JS) e dependências web pesadas.    | Crates Rust nativos, Model Context Protocol (MCP).             | **Sim** (Prioridade Alta)      | 9.0       |
| **Understand-Anything** | Pipeline Multi-Agente / AST  | Monorepo Node.js, pnpm, TS contínuo; overhead de RAM.         | Pipeline de 5-7 estágios, UI Adaptativa, React Flow Graph.     | **Sim** (Reescrita Total)      | 8.5       |
| **llmwiki**             | Wiki Autônoma (Web)          | Arquitetura monolítica web (Next.js, FastAPI, Postgres).      | Loop de "Bookkeeping" (Ingest, Query, Lint), camadas de dados. | **Sim** (Apenas Lógica)        | 6.0       |
| **PandaWiki**           | Base de Conhecimento RAG     | Dependência estrita de Docker (Go/TS); deploy tradicional.    | Heurísticas de parsing de arquivos offline (PDF/Word).         | **Não** (Descarte Recomendado) | 3.0       |
| **llm-wiki-compiler**   | Compilador de Conhecimento   | Runtime Node.js, acoplamento estrito à API da Anthropic.      | Hash SHA-256 incremental, Pipeline de 2 fases, Proveniência.   | **Sim** (Matemática Pura)      | 8.5       |
| **llm-wiki-agent**      | Grafo de Conhecimento Local  | Stack 100% Python em background; algoritmos pesados em RAM.   | Detecção Louvain, Passes de Grafo Determinístico vs Semântico. | **Sim** (Transpilação Urgente) | 9.5       |
| **sciwrite**            | Skill de Agente (Prompting)  | Nenhum risco de software (apenas texto Markdown/YAML).        | Auditoria de 5 passos, "The Banana Rule", tags de severidade.  | **Sim** (Integração Direta)    | 8.0       |
| **claude-obsidian**     | Automação de Vault           | 100% Shell scripts frágeis; acoplamento ao Electron/Obsidian. | "Hot Cache" (memória deslizante), Loop de Autoresearch.        | **Sim** (Lógica de Cache)      | 7.5       |
| **Markdown Viewer**     | Motor de Renderização        | Dependência do Java (PlantUML) e visualizadores web pesados.  | Especificação JSON Canvas, templates HTML/CSS determinísticos. | **Sim** (JSON Canvas UI)       | 7.0       |

---

### [Onyx / onyx-dot-app/onyx]

- **Originalmente serve para:** Atuar como um motor de busca corporativo de código aberto (Open-source AI chat) e plataforma RAG que se conecta a documentos, aplicativos e bases de dados internas para responder a perguntas corporativas.
- **Visão do Enxame (Síntese dos 3 Especialistas):** A análise dos metadados revela uma sobreposição de escopo com soluções corporativas monolíticas, focadas em Retrieval-Augmented Generation (RAG) em larga escala. O Arquiteto Bare-Metal rejeita categoricamente a adoção de qualquer infraestrutura que exija bancos de dados vetoriais pesados (como Typesense ou Vespa) rodando em paralelo, pois a indexação densa destruiria o limite de 6GB de VRAM da RTX 2060m e faria swap letal nos 32GB de RAM do sistema. O Estrategista de Neuro-UX, contudo, aponta que a premissa de um roteamento de acesso à informação transparente e baseado em permissões (RBAC) reduz a ansiedade cognitiva do usuário 2e/TDAH, eliminando o "barulho" de dados irrelevantes. O Engenheiro de Transpilação foca estritamente nas heurísticas de busca híbrida, buscando extrair a matemática de ranqueamento que funde busca léxica e semântica.
- **Problema Principal que Resolve/Valor de Uso:** Fornece um mecanismo de busca híbrida e RAG corporativo com controle de acesso rigoroso sobre repositórios de dados internos.
- **Stack Tecnológica Base:** Python (FastAPI), React, Postgres, Bancos Vetoriais (implícito pela categoria Enterprise RAG).
- **Tipo de Integração Atual:** Aplicação Web Monolítica / Containerizada.
- **Categorias Arquiteturais:** Motor de Busca Híbrida, Pipeline RAG Corporativo, Proxy de Roteamento de LLM.
- **Vetores de Risco (O Lixo Tóxico):** A arquitetura de microsserviços em containers Docker é a antítese do binário imutável Rust/Tauri exigido pelo Genesis Mission Control (SoustractionMC). Executar backends Python concorrentes para gestão de documentos exaure ciclos de CPU i9 que devem ser dedicados inteiramente à inferência de modelos GGUF locais via Llama.cpp e Wasmtime.
- **Ouro Oculto e Inspirações (Visão UX/Produto):** A clareza no processo de citação de fontes no RAG corporativo. Para o perfil neurodivergente, a capacidade de rastrear a proveniência exata de um dado sem quebrar o hiperfoco é vital. O conceito de rotear perguntas apenas para sub-grafos de conhecimento autorizados previne a paralisia por análise induzida por excesso de contexto.
- **Ações Mitigadoras (O Plano de Canibalização):** O código fonte original será ignorado. Apenas a lógica de _Hybrid Search_ (pesos de normalização entre pontuações BM25 e embeddings densos) será extraída. Essa matemática será reescrita no núcleo do SODA usando bibliotecas Rust puras e leves, como o `tantivy` para indexação léxica em disco, acoplada a um armazenamento vetorial efêmero alocado em memória. O controle de acesso será traduzido para um filtro de contexto executado em sandboxes estritas no Wasmtime.
- **Devemos Canibalizar? e PQ?:** Sim, mas de forma restrita às heurísticas. A matemática de ranqueamento é essencial para garantir que o contexto enviado ao LLM local (limitado a pequenas janelas devido à VRAM de 6GB) seja denso em valor e livre de ruído.
- **Score de 'Vale a pena?':** 4.5

---

### [goose / aaif-goose/goose]

- **Originalmente serve para:** Servir como um agente de IA nativo e extensível (sob a Linux Foundation) projetado para automatizar tarefas de engenharia executando comandos, editando arquivos e testando código diretamente na máquina do usuário.
- **Visão do Enxame (Síntese dos 3 Especialistas):** Um consenso de alto valor estratégico. O Arquiteto Bare-Metal celebra a fundação robusta em Rust (58.8% do repositório), que garante execução local de alta performance, gerenciamento de memória seguro e portabilidade exigida pelo SoustractionMC. O Engenheiro de Transpilação já iniciou a dissecação dos crates que implementam o Model Context Protocol (MCP) e o Agent Connectivity Protocol (ACP). O Estrategista de Neuro-UX reconhece o valor de uma inteligência que age de forma transparente sobre o sistema ("provider agnosticism"), mas exige o descarte absoluto da interface TypeScript/Electron (33.7%), que introduz latência visual e carga cognitiva desnecessária.
- **Problema Principal que Resolve/Valor de Uso:** Um agente de IA de propósito geral que não apenas sugere, mas instala, executa, edita e testa código e fluxos de trabalho nativamente na máquina do usuário.
- **Stack Tecnológica Base:** Rust (58.8%), TypeScript (33.7%), Shell (1.9%), Python (1.5%); Cargo, Nix, Docker.
- **Tipo de Integração Atual:** Aplicação Desktop (macOS/Linux/Windows), CLI, API Embutida.
- **Categorias Arquiteturais:** Framework de Agentes Nativos, Motor de Execução Local, Proxy de Protocolos Abertos (MCP/ACP).
- **Vetores de Risco (O Lixo Tóxico):** A presença de camadas frontend pesadas em TypeScript/JavaScript e o uso do motor V8 (`vendor/v8`) introduzem vulnerabilidades de memória e um overhead de comunicação inter-processos (IPC) inaceitável para o nosso daemon. A telemetria implícita em frameworks desktop genéricos polui um ambiente estritamente air-gapped e zero-trust.
- **Ouro Oculto e Inspirações (Visão UX/Produto):** O design de "Workflow Recipes" (`release_risk_check`). Transformar ações operacionais complexas em gatilhos determinísticos de clique único elimina a carga executiva severa enfrentada por usuários com TDAH. O "Provider Agnosticism" permite que a UI do Canvas permaneça imutável, independentemente de o motor estar rodando um modelo quantizado de 4-bit na RTX 2060m ou roteando via ACP.

|**Componente Extraído**|**Função no SoustractionMC (SODA)**|**Método de Canibalização**|
|---|---|---|
|`crates/goose-mcp`|Comunicação padronizada com ferramentas externas.|Compilação direta no daemon Rust.|
|`goose-acp`|Roteamento de fallback assíncrono.|Transpilação de macros para IPC Tauri.|
|`workflow_recipes/`|Automação de tarefas estruturadas.|Conversão para grafos JSON executáveis.|

- **Ações Mitigadoras (O Plano de Canibalização):** A camada de interface UI/Electron será sumariamente extirpada. Os diretórios vitais, especificamente `crates/goose` e as implementações MCP/ACP, serão isolados. O código Rust será canibalizado e compilado como sub-rotinas integradas diretamente no daemon de background do SODA. Para garantir a segurança zero-trust, as ferramentas do MCP serão enclausuradas e executadas através do nosso motor Wasmtime, garantindo que nenhum script gerado pelo agente tenha acesso irrestrito ao sistema hospedeiro.
- **Devemos Canibalizar? e PQ?:** Sim, com prioridade máxima. A implementação do Model Context Protocol (MCP) em Rust puro é um ativo inestimável que economiza meses de engenharia, fornecendo a fundação matemática para a interoperabilidade de ferramentas em execução local sem onerar o hardware.
- **Score de 'Vale a pena?':** 9.0

---

### [Understand-Anything / Lum1104/Understand-Anything]

- **Originalmente serve para:** Transformar qualquer base de código, wiki ou documentação complexa em um grafo de conhecimento interativo e visualmente navegável através da análise estrutural de um pipeline de múltiplos agentes.
- **Visão do Enxame (Síntese dos 3 Especialistas):** Este repositório instiga um debate polarizado. O Estrategista de Neuro-UX está fascinado com o grafo interativo estrutural (React Flow), a visualização de domínio horizontal e a interface adaptativa baseada em persona, que se alinham perfeitamente com a filosofia "Canvas-first" de redução de carga cognitiva. O Canibal saliva sobre o pipeline de múltiplos agentes orquestrados em estágios rigorosos e as heurísticas de atualização incremental. O Arquiteto Bare-Metal, no entanto, declara guerra à stack: 84.6% de TypeScript rodando de forma contínua via interpretadores V8/Node.js locais é uma heresia computacional que destruirá os 32GB de RAM necessários para o contexto do LLM.
- **Problema Principal que Resolve/Valor de Uso:** Transforma bases de código maciças e wikis não estruturadas em grafos de conhecimento navegáveis, colorificados e interativos de forma totalmente autônoma.
- **Stack Tecnológica Base:** TypeScript (84.6%), Python (9.4%), Astro (3.7%); React Flow, Vite, pnpm.
- **Tipo de Integração Atual:** Framework CLI nativo / Plugin de IDE (Claude Code, Cursor, Copilot).
- **Categorias Arquiteturais:** Utilitário de Parsing (AST), Pipeline de Agentes Analíticos, Motor de Renderização de Grafos.
- **Vetores de Risco (O Lixo Tóxico):** A execução baseada no ecossistema Node/pnpm significa gigabytes de dependências em `node_modules` e o peso contínuo do Garbage Collection na memória RAM. O uso de subprocessos interpretados para análise de arquivos em paralelo introduz latência inaceitável e uso agressivo da CPU i9, competindo diretamente com os ciclos necessários para a inferência local do LLM.
- **Ouro Oculto e Inspirações (Visão UX/Produto):** A representação espacial do código (Spatial Mapping) permite um offloading cognitivo profundo; o usuário TDAH não precisa manter a hierarquia de pastas ou a árvore de dependências na memória de trabalho. A separação por camadas arquiteturais (`API, Service, Data, UI, Utility`) codificadas visualmente por cores e a função de Impact Analysis (`/understand-diff`) eliminam a paralisia de decisão antes de um commit.

|**Agente do Pipeline**|**Função Matemática a Extrair**|**Destino no SODA (Reescrita)**|
|---|---|---|
|`project-scanner`|Detecção heurística de frameworks.|Módulo Rust nativo (Regex ultraleve).|
|`file-analyzer`|Extração de AST (Abstract Syntax Tree).|Integração direta com `tree-sitter` (Rust/C).|
|`architecture-analyzer`|Classificação topológica de grafos.|Passagem única em background via Tauri.|
|`domain-analyzer`|Mapeamento de fluxos de negócios.|Prompt determinístico Llama.cpp (GGUF).|

- **Ações Mitigadoras (O Plano de Canibalização):** A destruição da stack TypeScript backend é mandatória. A lógica do `file-analyzer` será reescrita inteiramente no núcleo Rust utilizando a biblioteca nativa `tree-sitter`, permitindo a extração de Abstract Syntax Trees (ASTs) com overhead computacional próximo a zero e segurança de alocação de memória. O pipeline de orquestração será portado para a máquina de estados finitos do daemon Rust. A interface do React Flow será cirurgicamente transplantada para o frontend passivo em React do SoustractionMC, sendo alimentada de forma assíncrona por eventos IPC do Tauri.
- **Devemos Canibalizar? e PQ?:** Sim, através de reescrita total. As heurísticas de extração estrutural e a lógica de UI reduzem drasticamente o custo interativo de exploração, alinhando-se perfeitamente aos requisitos de neuro-acessibilidade do projeto.
- **Score de 'Vale a pena?':** 8.5

---

### [llmwiki / lucasastorian/llmwiki]

- **Originalmente serve para:** Implementar o conceito de "LLM Wiki" (proposto por Andrej Karpathy), automatizando a manutenção de bases de conhecimento ao delegar para a IA a leitura de fontes brutas e a compilação contínua de páginas markdown interligadas.
- **Visão do Enxame (Síntese dos 3 Especialistas):** Este repositório gera uma rejeição arquitetural severa, mas exige resgates táticos. O Arquiteto Bare-Metal repudia o amálgama de microsserviços (Next.js + Python/FastAPI + Postgres/Supabase + LibreOffice), que configura um ambiente impossível de ser mantido dentro das restrições de um hardware air-gapped. O Engenheiro Canibal, ignorando a infraestrutura tóxica, foca na essência da especificação "LLM Wiki" de Andrej Karpathy: a máquina de estados perpétua de Ingest-Query-Lint. O Estrategista de Neuro-UX aplaude o conceito de delegar o "Bookkeeping" (manutenção estrutural e ligações) para a máquina, liberando a mente humana.
- **Problema Principal que Resolve/Valor de Uso:** Automatiza a manutenção pesada e o "context rot" de bases de conhecimento, forçando a IA a atuar como uma bibliotecária autônoma sobre um conjunto de fontes imutáveis.
- **Stack Tecnológica Base:** TypeScript (Next.js, 61.9%), Python (FastAPI, 32.3%), Supabase (Postgres + PGroonga), LibreOffice.
- **Tipo de Integração Atual:** Aplicação Web Full-Stack com Servidor MCP.
- **Categorias Arquiteturais:** Motor de Gestão de Conhecimento, RAG Compilado, Orquestrador de Tarefas em Background.
- **Vetores de Risco (O Lixo Tóxico):** Levantar uma instância de banco de dados Postgres via Supabase localmente, junto com um servidor FastAPI em Python e instâncias do LibreOffice em background para conversão de PDF, exauriria instantaneamente a RAM e VRAM do sistema. O design web monolítico requer roteamento de portas locais e serviços persistentes que violam os paradigmas de daemon ultraleve do SODA.
- **Ouro Oculto e Inspirações (Visão UX/Produto):** A rigidez conceitual da separação em camadas: Fontes Brutas (Imutáveis) vs. A Wiki (Gerada e pertencente ao LLM) vs. As Ferramentas (Orquestração MCP). Isso elimina a dissonância cognitiva de forçar o usuário a gerenciar arquivos de texto. Quando um novo documento entra no sistema e o agente atualiza autonomamente "10 a 15 páginas relacionadas", o cérebro 2e/TDAH é blindado contra a fadiga de gerenciamento de estado e organização de pastas.
- **Ações Mitigadoras (O Plano de Canibalização):** Substituição infraestrutural agressiva. O banco de dados Postgres com a extensão PGroonga será substituído por um banco embeddado e performático em Rust (como o `sqlite` através da biblioteca `rusqlite`, habilitado com extensões FTS5 para busca textual). O pipeline pesado em Python para processamento de documentos será descartado em favor de parsers nativos e ultraleves em Rust (como `pdf-extract`). As definições matemáticas das ferramentas MCP (`guide`, `search`, `read`, `write`, `delete`) serão absorvidas e compiladas como sandboxes isoladas no Wasmtime, garantindo execução efêmera.
- **Devemos Canibalizar? e PQ?:** Sim, mas restrito exclusivamente à máquina de estados lógicos. O padrão de compilação contínua de conhecimento (em oposição à recuperação estática do RAG) é o mecanismo definitivo para evitar a fragmentação de contexto sem penalizar a memória de trabalho do usuário.
- **Score de 'Vale a pena?':** 6.0

---

### [PandaWiki / PandaWiki] 

- **Originalmente serve para:** Funcionar como um sistema de base de conhecimento web focado em criar documentações de produtos, manuais e portais de FAQ voltados ao público, impulsionados por modelos RAG corporativos.
- **Visão do Enxame (Síntese dos 3 Especialistas):** Um descarte quase total. O Arquiteto Bare-Metal veta a fundação do projeto, que depende estritamente do ecossistema Docker 20.x no Linux. O Engenheiro Canibal não encontra inovações matemáticas ou fluxogramas lógicos únicos além de pipelines padrão de RAG encapsulados em Go. O Estrategista de UX não identifica nenhum valor em replicar uma interface de portal wiki corporativo clássico para um ambiente focado em foco extremo.
- **Problema Principal que Resolve/Valor de Uso:** Gera portais web (Wiki sites) autônomos baseados em coleções de documentos indexados por modelos RAG para busca e Q&A corporativos.
- **Stack Tecnológica Base:** TypeScript (66.8%), Go (29.5%), CSS, Docker.
- **Tipo de Integração Atual:** Serviço Web Conteinerizado.
- **Categorias Arquiteturais:** Sistema de Gestão de Conteúdo (CMS), Aplicação Monolítica, Motor RAG.
- **Vetores de Risco (O Lixo Tóxico):** A arquitetura é construída para escalabilidade de servidor tradicional, acoplada a gerenciadores de container pesados. O backend em Go, embora ofereça boa performance e concorrência, adiciona um runtime compilado desnecessário quando o SODA padronizou seu motor operacional inteiramente em torno do Rust para garantir o máximo controle sobre alocação de memória e execução `zero-copy`.
- **Ouro Oculto e Inspirações (Visão UX/Produto):** A capacidade de rotear eficientemente a ingestão de "Arquivos Offline (Word, PDF, Markdown)" e parsear "Sitemaps" para transformar conteúdo estático em contexto conversacional.
- **Ações Mitigadoras (O Plano de Canibalização):** Nenhuma mitigação arquitetural sistêmica será aplicada. O repositório será analisado pontualmente apenas em seu diretório `sdk/rag` para extrair quaisquer heurísticas sofisticadas de expressões regulares (Regex) usadas na sanitização e conversão de documentos Word/HTML para Markdown. Estas heurísticas serão usadas para refinar os próprios parsers construídos em Rust do SoustractionMC.
- **Devemos Canibalizar? e PQ?:** Não. A comunidade e o ecossistema Rust oferecem crates superiores, nativos e otimizados para pipelines RAG e parsing textual, não havendo justificativa para importar a dívida técnica de uma arquitetura Docker/Go.
- **Score de 'Vale a pena?':** 3.0

---

### [llm-wiki-compiler / atomicmemory/llm-wiki-compiler]

- **Originalmente serve para:** Operar como um compilador CLI que transforma fontes brutas em uma wiki interligada em markdown, realizando reconstruções incrementais para que o conhecimento se acumule de forma rastreável ao longo do tempo, em vez de depender de RAG estático.
- **Visão do Enxame (Síntese dos 3 Especialistas):** Um banquete metodológico, apesar da infraestrutura falha. O Engenheiro Canibal celebra a lógica algorítmica de reconstrução incremental através de hashes e a engenhosidade do pipeline de compilação em duas fases, que mitiga severamente a latência e o custo de inferência. O Arquiteto lamenta o invólucro construído sobre TypeScript/Node.js, mas concorda que a execução efêmera baseada em CLI é superior a processos em background contínuos. O Estrategista de Neuro-UX aprova a injeção determinística de metadados e as marcações de proveniência (tracking de origem) que promovem a "confiança algorítmica" na ferramenta.
- **Problema Principal que Resolve/Valor de Uso:** Compila conhecimento fragmentado em artefatos markdown estruturados, persistentes e interligados, contornando a limitação primária do RAG tradicional que é o "esquecimento" de contexto (Context Rot) após cada query.
- **Stack Tecnológica Base:** TypeScript (99.6%), Shell (0.4%), Node.js $\ge$ 18.
- **Tipo de Integração Atual:** Ferramenta Utilitária de Linha de Comando (CLI).
- **Categorias Arquiteturais:** Compilador Algorítmico Heurístico, Parseador de Grafo Textual, Gestor de Cache Incremental.
- **Vetores de Risco (O Lixo Tóxico):** A inicialização repetida do runtime Node.js em uma arquitetura CLI degrada a eficiência de inicialização a frio e pulveriza memória RAM. Além disso, o acoplamento estrito e hardcoded à API da Anthropic viola o princípio soberano, offline e "air-gapped" obrigatório para o SODA operando na RTX 2060m.
- **Ouro Oculto e Inspirações (Visão UX/Produto):** O mecanismo de "Truncamento Honesto", que injeta explicitamente a flag `truncated: true` no frontmatter quando um documento excede o limite de contexto. Isso previne ativamente as "alucinações cognitivas" e o overpromising, estabelecendo limites claros que ancoram as expectativas do usuário 2e/TDAH. As anotações de proveniência em nível de parágrafo (usando a sintaxe `^[filename.md]`) mantêm uma rastreabilidade impecável e granular sem introduzir poluição visual na interface do Canvas.
- **Ações Mitigadoras (O Plano de Canibalização):** A engine será totalmente reescrita em Rust.
    
    1. O loop de validação de arquivos incrementais será codificado através do crate genérico `sha2`, validando estados estáticos de documentos em microssegundos matemáticos: $H(state_{t}) = \text{SHA-256}(\text{source\_content})$.
    2. O pipeline bifásico (Fase 1: Extração de Conceitos independente de ordem $\rightarrow$ Fase 2: Geração Definitiva de Página) será traduzido em chamadas isoladas e determinísticas enviadas aos modelos Llama.cpp (em formato GGUF) via Wasmtime no backend Rust.
    3. A restrição e dependência da Anthropic será roteada e contornada para usar exclusivamente os modelos rodando localmente na GPU.
        
- **Devemos Canibalizar? e PQ?:** Sim. A filosofia central de "Compilar Conhecimento" em contraposição a "Consultar Vetores em Tempo Real" reduz maciçamente a pressão sobre os exíguos 6GB de VRAM, transferindo inteligentemente o ônus computacional para as operações de escrita em disco em momentos de ociosidade do sistema.
- **Score de 'Vale a pena?':** 8.5

---
### [llm-wiki-agent / llm-wiki-agent]

- **Originalmente serve para:** Atuar como um script local que usa modelos de linguagem para ler diretórios de anotações e manter autonomamente um grafo de conhecimento (NetworkX/Louvain) em arquivos markdown puros, sem depender de banco de dados.
- **Visão do Enxame (Síntese dos 3 Especialistas):** Considerada a obra-prima arquitetural a ser canibalizada neste lote. O Engenheiro Canibal está fascinado pela aplicação do algoritmo de detecção de comunidades de Louvain e pela inteligência topológica do grafo de dois passes (Determinístico vs Semântico). O Arquiteto aprova a elegante arquitetura "Database-less/Server-less" ancorada puramente em sistema de arquivos estáticos `.md`, porém, decreta o extermínio sistemático da stack Python e da biblioteca de processamento `NetworkX` devido à sua propensão a inflar o uso de RAM de forma descontrolada. O Estrategista de Produto apoia a visualização espacial através do motor `vis.js`, percebendo-a como o layout fundacional ideal para o Canvas do projeto.
- **Problema Principal que Resolve/Valor de Uso:** Constroi, atualiza e faz a manutenção autônoma de um grafo de conhecimento através da fusão de inferências semânticas e extração léxica, mantendo os dados persistentes puramente em diretórios markdown locais.
- **Stack Tecnológica Base:** Python (100%), NetworkX, Louvain Algorithm, vis.js.
- **Tipo de Integração Atual:** Scripts Executáveis Python Standalone / Arquivos de Perfil de Agentes (`AGENTS.md`).
- **Categorias Arquiteturais:** Motor Computacional de Grafos (Graph Engine), Pipeline Analítico, Motor de Clustering Semântico.
- **Vetores de Risco (O Lixo Tóxico):** Submeter algoritmos de teoria de grafos pesados e densos (como os encontrados no `NetworkX`) a um interpretador Python operando em background é um risco fatal. O _Global Interpreter Lock_ (GIL) e as estruturas de dados massivas e ineficientes do Python causarão picos de CPU no i9 e levarão os 32GB de RAM à exaustão rapidamente à medida que as conexões nodais da base de conhecimento escalarem.
- **Ouro Oculto e Inspirações (Visão UX/Produto):** A heurística de "Contradiction Flagging" proativo (inserção de tags lógicas `[!contradiction]`) no instante exato da ingestão do documento. Para mentes 2e/TDAH, esbarrar em contradições lógicas reativas durante o fluxo da query desencadeia quebras catastróficas de hiperfoco. Um sistema que resolve ou circunscreve conflitos de dados no momento da compilação preserva a banda de memória de trabalho executiva.

|**Estágio Analítico do Grafo**|**Mecânica Matemática Original**|**Mitigação Rust / SODA**|
|---|---|---|
|**Passo Determinístico**|Extração de strings `[[wikilinks]]`.|Regex puro processado via motor ultra-rápido.|
|**Passo Semântico**|LLM infere conexões implícitas com score.|Prompts com JSON Schema via Llama.cpp constraints.|
|**Clustering (Louvain)**|Divisão de grafos por modularidade.|Implementação de matriz esparsa usando crate `nalgebra`.|

- **Ações Mitigadoras (O Plano de Canibalização):** A essência puramente matemática será extraída e portada:
    
    1. As operações ineficientes de processamento de grafos do Python serão extirpadas e reescritas usando o crate Rust nativo `petgraph`, garantindo execução em tempos sub-milissegundos e layouts de memória adjacentes e compactos.
    2. O algoritmo de detecção de comunidades de Louvain será recodificado usando matemática de baixo nível. A operação, que matematicamente maximiza o índice de modularidade $Q = \frac{1}{2m} \sum_{i,j} \left[ A_{ij} - \frac{k_i k_j}{2m} \right] \delta(c_i, c_j)$, será processada em matrizes esparsas através do ecossistema `nalgebra`.
    3. As threads de passagem de extração (léxica e semântica) serão encapsuladas em workers assíncronos de baixo custo no Rust, isoladas em sandboxes Wasmtime.
        
- **Devemos Canibalizar? e PQ?:** Sim, classificando-se como extração de prioridade emergencial. Uma arquitetura de inteligência interconectada baseada puramente em grafos estruturais (eliminando a necessidade de bancos de dados complexos) é o pilar ideal para a persistência assíncrona, imutável e descentralizada almejada pelo SODA.
- **Score de 'Vale a pena?':** 9.5

---

### [sciwrite / labarba/sciwrite]

- **Originalmente serve para:** Funcionar como uma "Agent Skill" (instrução encapsulada) focada estritamente na revisão e edição cirúrgica de manuscritos científicos e acadêmicos, aplicando regras rigorosas de clareza sem alterar os dados técnicos originais.
- **Visão do Enxame (Síntese dos 3 Especialistas):** Um raro momento de consenso imediato fundado na simplicidade do design. O Arquiteto Bare-Metal não detecta qualquer débito ou risco estrutural, uma vez que a solução é composta estritamente de arquivos textuais formatados no "Agent Skill Standard" (YAML + Markdown). O Engenheiro Canibal não hesita em capturar os heurísticos refinados de engenharia de prompt incrustados no protocolo estrito de 5 passes de auditoria. O Estrategista de Neuro-UX adota de imediato o princípio da "Banana Rule" e a sistemática hierarquia de níveis de severidade para pautar o design de retorno visual (UI feedback) do sistema.
- **Problema Principal que Resolve/Valor de Uso:** Executa avaliações editoriais frias e cirúrgicas aplicando um framework de prompt logicamente inalterável, otimizando a precisão linguística sem manipular, alucinar ou alterar o conteúdo e os dados técnicos originais.
- **Stack Tecnológica Base:** Texto puro em Markdown e YAML (seguindo especificações estruturais de Agent Skills).
- **Tipo de Integração Atual:** Injeção de Contexto Baseada em Arquivo / Profiling de Prompt via Metadados (`SKILL.md`).
- **Categorias Arquiteturais:** Conjunto Heurístico de Engenharia de Prompt, Árvore de Decisão Lógica em Linguagem Natural.
- **Vetores de Risco (O Lixo Tóxico):** Inexistência de riscos de infraestrutura ou overhead computacional. O vetor de falha potencial único no ambiente SODA decorre da propensão matemática dos modelos LLM quantizados pesadamente (rodando a 4-bit para acomodar os 6GB de VRAM) a falharem em seguir restrições determinísticas complexas em janelas de contexto estendidas.
- **Ouro Oculto e Inspirações (Visão UX/Produto):** A imposição rigorosa da "Banana Rule" (a diretriz de manutenção incondicional da terminologia técnica em detrimento da "variação elegante" literária). Essa premissa matemática é uma âncora para mentes neurodivergentes, pois elimina o "context rot" mental causado pela ambiguidade lexical. As marcações esquemáticas de severidade (`CRITICAL`, `MAJOR`, `MINOR`) estabelecem uma hierarquia visual determinística, permitindo ao usuário ancorar sua atenção de forma segura, mecanismo essencial para preservar a memória de trabalho no TDAH. A segmentação do fluxo em um "Interactive mode" executado parágrafo por parágrafo atua como contenção contra o overflow cognitivo massivo.
- **Ações Mitigadoras (O Plano de Canibalização):** A lógica operacional contida nos 5 passos do protocolo de auditoria (1. Extração de Desordem, 2. Vitalidade de Verbo/Voz, 3. Arquitetura de Sentença, 4. Consistência de Palavras-chave, 5. Integridade Numérica e de Citação) será incutida nos sistemas de inicialização dos agentes efêmeros operados nas instâncias Wasmtime do SODA. O arquivo de especificação estruturada JSON/YAML presente no `SKILL.md` será traduzido em vetores rigorosos de formatação _JSON Schema (Grammar constraints)_ engatados diretamente à engine do Llama.cpp, forçando o modelo quantizado a aderir a uma saída computacional rígida e previsível, impossibilitando derivações não autorizadas da estrutura requisitada.
- **Devemos Canibalizar? e PQ?:** Sim, integralmente. A imposição de lógicas de avaliação estruturadas em fases sucessivas eleva exponencialmente a eficácia semântica e a confiabilidade factual de modelos compactos processados em inferência local (edge). Força o LLM a iterar seu raciocínio passo a passo, reduzindo drasticamente as falhas de alucinação agregada.
- **Score de 'Vale a pena?':** 8.0

---
### [claude-obsidian / claude-obsidian]

- **Originalmente serve para:** Transformar um cofre de anotações do Obsidian em um "cérebro de IA" persistente, usando scripts locais e o Claude (via MCP) para ingerir dados, pesquisar autonomamente e realizar a manutenção contínua das ligações entre os arquivos.
- **Visão do Enxame (Síntese dos 3 Especialistas):** Um repositório que acende alarmes no Arquiteto Bare-Metal: a dependência de 100% da lógica central construída sobre fragéis Shell Scripts acoplados a um framework desktop absurdamente pesado (Electron base do Obsidian) configura ineficiência de recursos. Contudo, o Engenheiro Canibal rapidamente intercepta o valor algorítmico latente nas lógicas de preservação do "Hot Cache" e nos controladores de loop do mecanismo de "Autoresearch". O Estrategista de Produto anota meticulosamente a eficiência tática de orquestrar a visualização em painéis determinísticos estáticos (manipulando Canvas e Banners do Obsidian) para conter e aliviar a fadiga e a sobrecarga de informações visuais do usuário.
- **Problema Principal que Resolve/Valor de Uso:** Automatiza a construção estruturada e a governança de sistemas de wikis no ecossistema do Obsidian por meio de rotinas de pesquisa web autônomas, retendo e transferindo a continuidade de contexto entre instâncias efêmeras de chat através de vetores condensados de memórias curtas.
- **Stack Tecnológica Base:** Conjuntos de Shell Scripts, Plugins nativos do ecossistema Obsidian (Electron/JS), APIs REST orientadas a loopback (localhost), implementações customizadas MCP.
- **Tipo de Integração Atual:** Matriz de Scripts Executáveis e ecossistema de Plugins Desktop engatilhados.
- **Categorias Arquiteturais:** Sistema de Orquestração de Agentes Locais, Engine Automata de Busca/Scraping, Arquitetura de Gestão de Estado Epistemológico (Hot Cache).
- **Vetores de Risco (O Lixo Tóxico):** A subordinação de fluxos de controle a runtimes de execução externa, sabidamente vulneráveis e notórios por consumo excessivo de RAM (como é o caso de Plugins não assinados do Obsidian, integrações de API REST em loop local contínuo e a base Electron). Estruturar a orquestração de lógica neural complexa em bash/shell scripts expõe a aplicação a race conditions inerentes a I/O, possíveis vetores de injeção de comandos não higienizados e quebras previsíveis em ambientes não-POSIX.
- **Ouro Oculto e Inspirações (Visão UX/Produto):** A elegância arquitetural incutida no arquivo de retenção `wiki/hot.md` (o núcleo do conceito de "Hot Cache"). Imediatamente após a conclusão da sessão interativa, o agente é instruído a condensar os vetores factuais e referenciar as _threads_ de hiperfoco mantidas ativas, armazenando-os num bloco textual rígido de até 500 palavras. A iteração de sessão subsequente sofre um bootstrap alimentado por esta cápsula de context. Em um contexto 2e/TDAH — onde os indivíduos são assaltados por distorções de "cegueira temporal" e frequentes disrupções abruptas de ciclos de hiperfoco —, garantir a reidratação automática e orgânica das balizas do contexto da tarefa atua como uma prótese de acessibilidade neurológica crítica e indispensável.
- **Ações Mitigadoras (O Plano de Canibalização):** O ecossistema do Obsidian e os arquivos shell scripts orquestradores serão terminantemente erradicados.
    
    1. A lógica algorítmica por trás da injeção do "Hot Cache" (ancorada em arquivos `hooks.json` engatilhando vetores SessionStart e SessionStop) será integralmente transportada para a thread isolada do core em Rust, escrita como parte de um serviço nativo e altamente performático do Event Loop assíncrono (utilizando os paradigmas robustos do `tokio`). Esse vetor de cache transiente ficará abrigado criptograficamente nos segmentos de dados do binário, induzindo exatos zero bytes de overhead de transferência à limitada VRAM da GPU.
    2. O ciclo autônomo e infinito do diretório `/autoresearch`, balizado em 3 estágios lógicos estritos (Pesquisa Parametrizada $\rightarrow$ Captura de Dados $\rightarrow$ Síntese Factual $\rightarrow$ Escrita e Armazenamento via Gap-Filling dinâmico) será transcrito matematicamente em uma FSM (Máquina de Estados Finitos/State Machine) sólida escrita no núcleo do backend em Rust, forçando todo o tráfego de rotinas de captura de rede (Scraping/Fetches) a operar estritamente a partir das fronteiras das sandboxes determinísticas providas pelas instâncias efêmeras do motor Wasmtime.
        
- **Devemos Canibalizar? e PQ?:** Sim, motivados estritamente pela eficácia operacional provada pela mecânica do "Hot Cache". Essa heurística específica converte o limite de parede de tijolos dos pequenos buffers de contexto ditados por hardware portátil e GPUs de 6GB de VRAM em janelas de processamento semi-infinita, forjando uma estrutura sintética de pseudo-memória persistente, barata e altamente funcional, que sobrevive aos reinícios de runtime.
- **Score de 'Vale a pena?':** 7.5

---

### [Markdown Viewer / markdown-viewer/skills]

- **Originalmente serve para:** Fornecer um catálogo de templates e instruções (Skills) que ensinam agentes de IA a gerarem diagramas, arquiteturas e cartões de informação complexos e com design de alta fidelidade diretamente na sintaxe do Markdown.
- **Visão do Enxame (Síntese dos 3 Especialistas):** O Arquiteto Bare-Metal rejeita a dependência do motor legado do PlantUML (Java) e o uso de extensões de navegador baseadas em Node.js. Contudo, o Estrategista de Produto enxerga imenso valor na capacidade de gerar "Info Cards" precisos e topologias visuais usando `JSON Canvas` e HTML/CSS estático, elementos perfeitos para manter a "permanência espacial" e reduzir a carga cognitiva no TDAH. O Engenheiro Canibal já planeja extrair os templates YAML e as rigorosas restrições de sintaxe dos arquivos `SKILL.md`.
- **Problema Principal que Resolve/Valor de Uso:** Fornece um catálogo de instruções rigorosas ("Skills") que ensinam agentes de IA a gerar diagramas e layouts complexos em Markdown nativo sem alucinar na sintaxe.
- **Stack Tecnológica Base:** Markdown, YAML, JSON Canvas, HTML/CSS, Vega-Lite, PlantUML.
- **Tipo de Integração Atual:** Repositório de prompts/templates injetados no contexto de agentes.
- **Categorias Arquiteturais:** Motor de Templates Estáticos, Utilitário de Prompting Visual.
- **Vetores de Risco (O Lixo Tóxico):** A necessidade de uma extensão de browser de terceiros para renderizar a saída visual e a dependência do runtime Java subjacente ao ecossistema do PlantUML, o que é inaceactável em nossa fundação isolada.
- **Ouro Oculto e Inspirações (Visão UX/Produto):** A matriz de layouts pré-definidos (ex: 13 layouts e 12 estilos para arquiteturas) combinada ao padrão `JSON Canvas`. Isso permite forçar o LLM a simplesmente apontar coordenadas espaciais determinísticas em vez de gerar CSS caótico, garantindo que os painéis do nosso Canvas permaneçam imutáveis.
- **Ações Mitigadoras (O Plano de Canibalização):** Descartaremos o motor PlantUML e as extensões web. Extrairemos os templates de `layouts/` e `styles/` convertendo-os em componentes React passivos estilizados com Tailwind v4. Absorveremos as "Critical Syntax Rules" do repositório, transpilando-as para regras rigorosas de _JSON Schema (Grammar constraints)_ injetadas no Llama.cpp.
- **Devemos Canibalizar? e PQ?:** Sim (Parcialmente). O conceito de transferir o peso da renderização visual da IA para a interface através de restrições determinísticas (JSON/YAML) protege a VRAM e estabiliza a UI para o nosso usuário neurodivergente.
- **Score de 'Vale a pena?':** 7.0