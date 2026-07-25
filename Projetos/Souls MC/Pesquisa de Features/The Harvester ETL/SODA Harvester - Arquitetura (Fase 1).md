# SODA Blueprint: Documento Mestre Arquitetural - O Harvester (Fase 1)

**Versão:** 3.0 (Expandida, Definitiva e Fundacional)
**Escopo:** Fase 1 - Extração Determinística, Isolamento de Domínio e Geração de Matéria-Prima (RAW)
**Princípio Regente:** _Pessimismo da Razão, Otimismo da Vontade._
## O Mapa de Fases (Visão Geral do Ecossistema)

Para garantir a clareza absoluta de escopo e evitar confusão de domínios arquiteturais, o pipeline de ingestão e análise do SODA é estritamente dividido nas seguintes etapas:

- **Fase 1: O Harvester (Foco Total deste documento).** A esteira de força bruta, 100% determinística e sem uso de IA, responsável por extrair metadados e algoritmos puros em ambientes isolados.
- **Fase 2: Cofre de Contexto (Rascunho):** Ingestão em banco de dados local (SQLite/LanceDB) para permitir elasticidade dinâmica e proteger a VRAM de 6GB da máquina host.
- **Fase 3: Visão de Enxame (Rascunho):** Agentes de IA ("Analistas de M&A" divididos em Lentes de Produto/UX, Arquitetura e Ops) debatem, cruzam dados do Harvester e extraem valor semântico.
- **Fase 4: Consolidação (Rascunho):** Injeção do JSON validado e resultante na planilha mestre SODA Blueprint.

## 1. O Manifesto Arquitetural (O "Por Quê" Profundo)

A arquitetura do **SODA Harvester** transcende a definição de um simples _script_ de automação; ela é uma resposta de engenharia de software pragmática a um problema historicamente intratável de escala. A necessidade primária do projeto é analisar, classificar e extrair inteligência cirúrgica (algoritmos base, contratos estritos de interface e padrões arquiteturais resilientes) de um lote inicial de 370 repositórios open-source. Estes repositórios possuem origens variadas, qualidades estruturais questionáveis e pertencem a ecossistemas heterogêneos.

O paradigma tradicional do mercado atual — que consiste em "jogar o repositório inteiro no contexto de um Agente de IA avançado e pedir um resumo" — falha de maneira catastrófica e silenciosa em escala por três motivos técnicos letais:

1. **Alucinações Severas por Context Rot (Degradação de Atenção):** A arquitetura dos modelos de linguagem (Transformers) sofre com excesso de ruído. Quando um LLM é inundado com 100.000 linhas de código bruto (incluindo CSS, arquivos de _lock_, dependências minificadas e _boilerplates_), a matriz de _Self-Attention_ degrada. A IA perde a capacidade de focar no que importa, começando a inventar (alucinar) conexões lógicas que não existem no código e a ignorar funções críticas ocultas no "meio" do contexto (o fenômeno _Lost in the Middle_).
2. **O Teto Físico de Hardware (A Restrição Soberana):** A arquitetura SODA impõe uma restrição física inegociável de **VRAM estrita a 6GB** (baseada em hardwares como a RTX 2060m) para garantir operações locais, privadas e _Edge-first_. Carregar Árvores Sintáticas (ASTs) inteiras ou repositórios na memória de contexto de um modelo local de 8B parâmetros causa _Out of Memory (OOM)_ imediato, travando o kernel da GPU e derrubando o _daemon_ do SODA.
3. **Hemorragia Financeira e Rate Limits:** Enviar código bruto continuamente para APIs de nuvem (Gemini 1.5 Pro, Claude 3.5 Sonnet) consome limites de tokens de forma irresponsável. Analisar 370 repositórios com contexto total tornaria o processamento financeiramente inviável, além de esgotar os limites de requisições por minuto (RPM) das APIs, tornando o pipeline inoperante.

Para resolver este trilema, o pipeline SODA é estritamente fracionado. O **Harvester (Fase 1)** atua como a força bruta escavadora, a engrenagem puramente mecânica que precede e viabiliza a inteligência analítica.

### 1.1. A Regra do "Zero AI" na Ingestão (Determinismo Absoluto)

Na Fase 1, **não existem Agentes de IA, inferência semântica ou LLMs**. É proibido o uso de heurísticas probabilísticas. O processo é **100% determinístico, mecânico e algorítmico**.

O objetivo é transformar gigabytes de "Código Espaguete" e dependências inúteis em poucos kilobytes de metadados puros — extraindo o "Sinal" absoluto isolado do "Ruído". Nós assumimos como premissa básica que todo código de terceiros é inerentemente caótico, mal documentado, frágil e hostil até que seja processado, validado e destilado por ferramentas matemáticas e estáticas precisas. A máquina não "entende" o código nesta fase; ela o disseca.

### 1.2. O Conflito de Domínios e a "Zona de Quarentena" (Laboratório Nível 4)

O núcleo operacional do SODA (Runtime Final) exige **Pureza do Silício**, rejeitando visceralmente ecossistemas interpretados, pesados ou vulneráveis a ataques de cadeia de suprimentos, como Node.js, ecossistema React, Webpack e Electron.

No entanto, aplicar essa pureza na ingestão impediria o estudo de soluções brilhantes escritas nessas linguagens. Portanto, o Harvester (Fase 1) atua como uma **Zona de Quarentena (Bio-Lab Nível 4)**. Ele é explicitamente autorizado — e encorajado — a ler, mapear e executar (em Sandbox) dados de projetos TypeScript, Go, Python, C++ ou JS puro.

A premissa filosófica que ancora essa decisão é clara: **não herdamos a infraestrutura tóxica; nós a estudamos para extrair apenas a "Alma Matemática" (Logic Math Heuristic) e os contratos visuais, para transmutação e reescrita posterior no ecossistema soberano (Rust).** O Harvester é o laboratório de contenção onde dissecamos o patógeno (código legado/tóxico) para extrair o seu DNA útil, incinerando o resto.

## 2. A Matriz de Ferramentas (O Arsenal Tático)

Para garantir segurança local absoluta, velocidade de operação na casa dos milissegundos e precisão semântica impecável — tudo isso sem recorrer a contêineres pesados, lentos e consumidores de RAM como instâncias Docker no Windows —, selecionamos um arsenal de ferramentas de Linha de Comando (CLI) que operam nativamente e de forma efêmera.

|   |   |   |
|---|---|---|
|**Ferramenta / CLI**|**Papel Estratégico na Extração**|**O Racional Técnico e Implicações de Segurança (Por que usar?)**|
|**Windows Sandbox (WSB)**|**Isolamento de Execução Absoluto (Anti-RCE)**|O _host_ físico é o Windows. Rodar linters ou instaladores de terceiros (ex: `cargo clippy` que pode engatilhar um `build.rs` obscuro, ou `npm install` que pode ativar `postinstall` maliciosos) é um risco inaceitável de _Remote Code Execution_. O WSB usa tecnologia Hyper-V de hardware para criar um ambiente Windows efêmero, limpo e descartável em menos de 2 segundos através de um arquivo `.wsb` gerado dinamicamente. O código suspeito executa na VM, gera o relatório de saída, e o contêiner VM é aniquilado no nível do kernel. Zero rastro, zero persistência, zero contaminação do _host_.|
|**JCodemunch (via uv/pipx)**|**Extração de Lógica e "Matéria Escura" (AST)**|Extrair dados textuais usando Regex é frágil e propenso a erros de sintaxe. O JCodemunch contorna isso usando a engine _tree-sitter_ para entender a Árvore Sintática Abstrata (AST) autêntica de mais de 70 linguagens. Ele extrai as **assinaturas exatas das funções, tipos, structs, traits e classes**, removendo o corpo funcional (o miolo lógico do código). Na prática, reduz um arquivo de 5.000 linhas de implementação para 50 linhas de assinaturas matemáticas, economizando até 95% dos tokens de IA na Fase 2 e mantendo intacta apenas a heurística arquitetural.|
|**Aider CLI**|**Topologia e Grafo Arquitetural Dimensional**|Usado _exclusivamente_ no modo cego com a flag `--repo-map`. Ele não gera nem sugere código. Sua função é varrer o projeto e desenhar a topologia em árvore do repositório, mapeando o grafo de dependências internas (quem importa o quê, qual diretório concentra o núcleo lógico). Vital para alimentar a _Lente B (Estrutura Arquitetural)_, ajudando a IA a calcular quão acoplado e "espaguete" está o código sem precisar ler o código.|
|**Oxc (The Oxidation Compiler)**|**Extração de Contratos de Interface (UX/UI)**|A premissa atual da indústria de "ler testes unitários para deduzir a UX" é míope, pois testes focam em estados isolados e falham gravemente em descrever jornadas de interação. O Oxc (um linter/parser escrito em Rust ultrarrápido) varre componentes de UI (**especialmente Svelte 5, TS, React**) extraindo exclusivamente os **Contratos Vitais:** as _Props/States_ (os dados e reatividade que entram no componente) e os _Eventos/Dispatchers_ (as ações que saem do componente). Isso mapeia a "previsibilidade mecânica" e a âncora espacial sem necessidade de invocar um navegador _headless_ ou renderizar o DOM.|
|**Semgrep / Clippy**|**Diagnóstico de Saúde Estática, Entropia e Cicatrizes**|Ferramentas de auditoria rigorosa que avaliam a "Qualidade da Alma" do código. Elas extraem alertas de segurança (CVEs em dependências), uso de memória não segura (blocos `unsafe`, ponteiros crus em C/Rust), vazamentos potenciais e complexidade ciclomática absurda. O resultado em formato JSON alimenta diretamente as métricas de _Entropy Risk_ e _Bare Metal Fit_ do SODA.|
|**GitHub CLI (`gh`)**|**Extração de Vitalidade e Ciclo de Vida Social**|Executa requisições limpas à API para baixar em formato JSON estruturado as métricas de tração: as últimas 50 _Issues_ e os _Pull Requests_ mais recentes. Essa ferramenta determinística define matematicamente o `horizonte_extracao` medindo o _lead time_ das respostas e commits: o projeto está vivo e recebendo manutenção, ou está abandonado e maduro para canibalização passiva?|

## 3. A Anatomia do Processo (A Engenharia Prática do "Como")

O fluxo sequencial abaixo é o algoritmo estrito executado iterativamente pelo Python (Orquestrador) para cada uma das 370 URLs. A resiliência arquitetural é a chave: qualquer falha irrecuperável em uma etapa aciona um _fail-fast_ isolado, gera um log de erro detalhado em um `.log` central, executa a purga total do lixo temporário gerado e avança implacavelmente para o próximo repositório. O processo não pode travar.

### Passo 3.1: Resiliência de Rede e Clonagem Cirúrgica (Blobless Clone)

A comunicação externa é o ponto de estrangulamento e falha mais comum em automações em massa. O tráfego de saída é encapsulado na **Tríade de Proteção** para garantir estabilidade e imunidade a bloqueios do GitHub (Error 429 - Too Many Requests) ou quebras de conexão TLS.

1. **O Pedágio (Semáforo de Threading):** Uma trava de concorrência global implementada no nível do processo Python. Garante estritamente apenas 1 requisição externa de API a cada 5 segundos absolutos.
2. **O Amortecedor (Tenacity com Jitter):** Implementação de retentativas resilientes com retardo exponencial e adição de _jitter_ (atraso aleatório em milissegundos) para simular tráfego humano e evitar sincronização destrutiva de retentativas contra a API alvo.

    ```
    from tenacity import retry, wait_random_exponential, stop_after_attempt
    @retry(wait=wait_random_exponential(multiplier=2, max=10), stop=stop_after_attempt(3))
    def fetch_with_protection(url):
        # Acesso de rede restrito e seguro...
    ```

3. **A Rota de Fuga (Fallback Tracker):** Se a 3ª tentativa falhar fatalmente, o repositório é instantaneamente marcado com a _flag_ `NETWORK_QUARANTINE` na base SQLite local, o erro é registrado e o loop avança, poupando tempo de processamento.

**A Engenharia Otimizada da Clonagem:**

```
git clone --filter=blob:none <URL_DO_REPO> ./temp/<REPO_ID>
```

_A implicação técnica profunda:_ O clone raso tradicional (`--depth 1`) destrói a ontologia temporal do código, apagando o histórico de _commits_. Sem o histórico, as ferramentas analíticas não conseguem calcular a "frequência de mutação" ou "Churn" dos arquivos (ex: se um arquivo de roteamento foi alterado 500 vezes por 20 autores no último ano, ele é um _hotspot_ crônico de entropia e quebra de design).

A estratégia `--filter=blob:none` resolve este paradoxo elegantemente: ela baixa a árvore completa de diretórios, metadados e o histórico cronológico de commits inteiro, mas **finge** que baixou o conteúdo dos arquivos (blobs). O download real do texto do código só ocorre no momento milimétrico em que o JCodemunch ou o Semgrep tentam ler aquele arquivo específico. Isso garante velocidade máxima de I/O e mínima ocupação de disco na fase de clonagem.

### Passo 3.2: O Abate Seguro no Sandbox (WSB)

Se o orquestrador Python detectar a necessidade de invocar gerenciadores de pacotes de terceiros (para compilar binários de ferramentas locais necessárias ou rodar o Semgrep de forma intrusiva que necessita de dependências instaladas), ativamos o protocolo rigoroso de isolamento:

1. O Python gera em disco um arquivo efêmero e dinâmico chamado `config_sandbox.wsb`.

    ```
    <Configuration>
      <vGPU>Disable</vGPU>
      <Networking>Disable</Networking>
      <MappedFolders>
        <MappedFolder>
          <HostFolder>C:\soda_etl\temp\<REPO_ID></HostFolder>
          <SandboxFolder>C:\target_repo</SandboxFolder>
          <ReadOnly>true</ReadOnly> <!-- O código cru NUNCA altera a origem -->
        </MappedFolder>
        <MappedFolder>
          <HostFolder>C:\soda_etl\output\<REPO_ID></HostFolder>
          <SandboxFolder>C:\output</SandboxFolder>
          <ReadOnly>false</ReadOnly> <!-- Permite gravação apenas do JSON final -->
        </MappedFolder>
      </MappedFolders>
      <LogonCommand>
        <Command>cmd.exe /c "cd C:\target_repo && cargo clippy --message-format=json > C:\output\health.json"</Command>
      </LogonCommand>
    </Configuration>
    ```

2. O Windows Sandbox é acionado em _background_. Ele sobe com rede e aceleração gráfica desabilitados para mitigar exfiltração de dados e poupar recursos.
3. Dentro da caixa de areia perfeitamente isolada pelo hipervisor (Hyper-V), o código de terceiros sofre a varredura e a execução dos linters.
4. O relatório JSON purificado é depositado na pasta `output` (única via de saída permitida).
5. Ao finalizar, o processo WSB é **encerrado pelo sistema operacional Host**, obliterando a Máquina Virtual, limpando sua memória RAM e destruindo qualquer _malware_ secundário que tentasse escrever no registro.

### Passo 3.3: A Destilação Semântica e Estrutural (A Peneira de AST)

Retornando ao sistema de arquivos do _host_ (que agora possui acesso apenas aos arquivos estáticos seguros), o orquestrador invoca os potentes processadores textuais e de AST:

- **Extraindo a "Alma Matemática":** `jcodemunch-mcp get_repo_outline --path ./temp/<REPO_ID> > raw_logic.txt`. Este comando atravessa recursivamente diretórios e arquivos, decompondo a linguagem em árvores sintáticas (tree-sitter). O resultado final é um mapeamento estrito das declarações e tipagens. (Exemplo prático: Ele expõe `pub fn encrypt_payload(data: &[u8]) -> Result<Vec<u8>, Error>` para análise heurística, mas suprime e ignora deliberadamente os 400 _loops_ e verificações internas do algoritmo, mantendo a abstração cristalina).
- **O Esqueleto de Vizinhança:** `aider --repo-map --map-file raw_arch.txt`. Gera um mapa relacional ultracompacto. Os Analistas de IA da Fase 3 usarão isso para compreender inferências complexas, como: _"A arquitetura de `src/database/` está excessivamente acoplada à `src/ui_layer/`, logo, o score de extractabilidade arquitetural é baixo."_
- **A Realidade Operacional (Lente Ops/Infra):** Um submódulo autônomo Python realiza uma varredura de rastreamento rápido (via _Regex/Grep_ local):
    - Constrói o _Blueprint_ operacional localizando, filtrando e concatenando arquivos vitais de deploy como `.github/workflows/`, `Dockerfiles`, e arquivos `docker-compose.yml`. Isso define com fatos empíricos a "dor de cabeça" da Operabilidade.
    - Faz a contagem bruta e rastreia ocorrências textuais de áreas proibitivas: blocos `unsafe`, funções perigosas como `eval()`, chamadas `any` excessivas e dívidas técnicas assumidas publicamente via anotações `TODO` ou `FIXME` crônicas.

### Passo 3.4: A Lente UX (Extração Autônoma de Contratos Visuais via Oxc)

Para repositórios classificados preliminarmente como possuidores de interfaces gráficas ou web:

- O script restringe o parser Rust **Oxc** aos diretórios clássicos de frontend (`src/components`, `src/routes`, `app/`).
- Ele vasculha componentes de UI recursivamente. Tratando-se de **Svelte 5** (foco primário de UI do SODA), ele mapeia as novas _Runes_ sintáticas reativas: varre extratos de dependência `$props()`, analisa declarações imutáveis `$state()` e computações de ciclo de vida nativas, além de mapear os propagadores de eventos.
- O resultado consolidado é um documento `raw_ux_contracts.txt`. Este documento atua como a _API mecânica e visual do componente_. É com essa matriz que a IA julgará a "previsibilidade da interface", a ausência de atualizações em cascata (_reflows_ custosos) e a modularidade da UX.

### Passo 3.5: O Protocolo de Limpeza Nuclear (Purge Routine)

A integridade, previsibilidade e higiene do sistema dependem da capacidade de não acumular lixo do ecossistema estudado. Ao finalizar a extração do 370º artefato, a diretiva de encerramento do _script_ exige que o diretório bruto de clonagem `.\temp\<REPO_ID>` seja **apagado permanentemente do disco (hard delete/shred)**. O Harvester não arquiva código-fonte de terceiros para consultas futuras. Apenas os destilados absolutos (o "Sinal") permanecem na máquina.

## 4. O Produto Final: O Super Pacote "RAW" (A Matéria-Prima de Alta Densidade)

A conclusão bem-sucedida de todas as etapas da Fase 1 para um único repositório resulta na criação de um diretório enxuto e estruturado (Ex: `./soda_raw_data/id_repo/`), contendo **exatamente 9 artefatos estritos**.

Este "Pacote RAW" é a ponte fundamental que traduz a complexidade caótica e ilimitada do open-source para um contexto finito, denso e digerível pela Inteligência Artificial do SODA. Cada arquivo atende a uma demanda exata das lentes analíticas da planilha matriz.

|   |   |   |
|---|---|---|
|**Arquivo Artefato Gerado**|**O Que Contém e o Formato da Extração**|**Mapeamento Estratégico (Alvo na Planilha SODA V3)**|
|**`01_promessa_readme.md`**|O texto em Markdown puro do pitch de marketing original, abstrações conceituais e intenções comerciais.|`declared_description`, `proposta_original_resumo`|
|**`02_dependency_manifest.json`**|União estruturada de todos os arquivos manifestos cruzados (`Cargo.toml`, `package.json`, `go.mod`). Revela a obesidade empírica do sistema e os custos operacionais (licenças pagas ocultas).|`discipline_dependency`, _Product Market Fit_, `intrinsic_ethics_risk`|
|**`03_ux_contracts.txt`**|A mecânica estrita extraída via Oxc. Estrutura semântica das Props (entradas) e Eventos (saídas). Totalmente limpo de HTML semântico ou CSS irrelevante.|`lente_a_sentido_ux`, `acao_de_canibalizacao` (Decisões de UX/UI)|
|**`04_repo_outline.txt`**|O mapa AST completo gerado pelo JCodemunch (a "matéria escura"). Contém as assinaturas das lógicas primárias de execução matemática, essenciais para transplante de heurísticas.|`logic_math_heuristic`, `deep_pattern`, `transplantable_core`|
|**`05_architecture_map.txt`**|O grafo hierárquico espacial e topológico de pastas e arquivos interligados (criado pelo Aider).|`score_architectural_extractability`, `lente_b_estrutura_arq`|
|**`06_unsafe_hotspots.txt`**|Rastreador cirúrgico de áreas perigosas de alocação de memória (em C/Rust), gambiarras documentadas e vulnerabilidades arquiteturais declaradas (`TODO`).|`where_ai_should_not_enter`, `risco_linha_vermelha`|
|**`07_ops_blueprint.txt`**|A compilação em texto simples de toda a logística mandatória para rodar o software (Configurações Docker complexas, pipelines agressivos de CI/CD, dependência de infra externa).|`score_operability`, `score_creep_risk`, `lente_c_realidade_ops`|
|**`08_health_report.json`**|Alertas profundos de análise estática gerados em Sandbox, avaliações de complexidade ciclomática global e vulnerabilidades de _runtime_ expostas.|`entropy_risk`, `design_misuse_risk`, `bare_metal_fit`|
|**`09_community_meta.json`**|Metadados absolutos do GitHub sobre o ciclo de vida real: Data de início, média temporal de resolução de problemas, índice de abandono e volume ativo de mantenedores.|`horizonte_extracao`|

## 5. A Fronteira da Elasticidade (A Passagem de Bastão para a Fase 2)

É imprescindível estabelecer o mecanismo exato de como este "Pacote RAW" — agora fisicamente consolidado no disco — alimentará os Agentes de M&A (Fase 3) sem disparar a violação grave de limite de hardware (o fatal **OOM - Out of Memory**) na GPU local de 6GB de VRAM. A elasticidade dinâmica garante que o sistema se adapte ao volume de dados e não quebre a execução do pipeline.

1. **O Porteiro (Pre-Flight Context Check):** Imediatamente antes do conjunto dos 9 artefatos ser considerado validado e liberado para a próxima fase, o orquestrador invoca a biblioteca `tiktoken` (utilizando _encodings_ padrão como `cl100k_base` ou equivalentes Llama/Mistral) para realizar a contagem precisa do volume total de tokens representados na colheita daquele repositório.
2. **O Roteamento Elástico Inteligente:** Baseado na contagem, o sistema define o caminho de inferência ótimo:
    
    - **Zona Verde (`< 16k tokens`):** O pacote RAW é considerado enxuto, otimizado e denso. Ele pode e deve ser processado de forma unificada (inteiramente na janela de contexto primária) por um Agente LLM rodando 100% localmente na RTX 2060m. Isso assegura latência zero, privacidade absoluta e custo zero.
    - **Zona Amarela (`16k a 64k tokens`):** O pacote ultrapassa a margem de segurança de processamento linear com contexto simultâneo em hardwares limitados (onde a KV Cache esgotaria a VRAM). Ele é despachado para a **Fase 2 (Cofre de Contexto)**, onde passa pelo processo de _chunking_ semântico (fatiamento de dados respeitando a estrutura lógica) e é vetorizado e armazenado em um banco **LanceDB local**. Na Fase 3, a IA usará técnicas rigorosas de RAG (Retrieval-Augmented Generation), buscando iterativamente dados por Lente Analítica (ex: _"buscar apenas evidências para UX"_), preservando o limite da VRAM em cada transação.
    - **Zona Vermelha (`> 64k tokens`):** Projetos open-source massivamente gigantes (como frameworks inteiros ou sistemas operacionais baseados em código aberto) acionam a **Rota de Fuga Segura (Cloud Fallback)**. O pacote perfeitamente estruturado pelo Harvester é despachado via API REST (utilizando roteadores como OpenRouter ou diretamente a API do Gemini 1.5 Pro). O uso pontual da nuvem garante janelas de contexto colossais (de 1M a 2M tokens), assegurando que o projeto "Titã" seja compreendido em sua magnitude sem destruir o ambiente operacional do Host local.

## 6. O Relaxamento Tático do Pydantic na Camada ETL (A Quarentena Ativa)

Para que o paradigma de "Zona de Quarentena" funcione efetivamente durante a ingestão do banco de dados intermediário (SQLite local que guarda os rastros do processamento do Harvester e gerencia o ETL para a planilha final), o modelo de validação da base é projetado com um paradoxo estrutural arquitetado: ele é intransigente na tipagem e na imutabilidade das chaves, mas deliberadamente permissivo nos valores capturados dos repositórios durante esta única fase.

A estrutura Pydantic que engolirá e formatará os 9 metadados no final da rotina relaxa explicitamente a regra de soberania do `@field_validator` global que compõe o infraestrutura final e sacrossanta do SODA (definida na Master Solutions v3).

```
from pydantic import BaseModel, Field, HttpUrl
from typing import List, Optional

class SODA_Harvester_RawRecord(BaseModel):
    project_name: str = Field(..., max_length=255, description="Nomenclatura canônica rastreada.")
    repo_url: HttpUrl = Field(..., description="A âncora digital de origem.")
    
    # ⚠️ Relaxamento Tático de Domínio: Armazena, registra os "Sintomas", mas NÃO aborta o pipeline.
    # Esta exceção previne que o banco estoure ao encontrar bibliotecas que consideramos
    # lixo tóxico (Node.js, React, Electron, NPM) durante a avaliação do terreno.
    detected_toxic_deps: List[str] = Field(
        default_factory=list,
        description="Quarentena de Ecossistema: Mapeia infraestrutura parasitária e bloqueios."
    )
    
    # A métrica de Transmutação de Ouro: A pureza algorítmica e o potencial
    # de canibalização são exigidos e analisados APENAS sobre as heurísticas e assinaturas
    # isoladas dentro da 'logic_math_heuristic', fechando os olhos matematicamente 
    # para a infraestrutura original onde este ouro estava enterrado.
    transplantable_core_identified: bool = Field(
        default=False, 
        description="Flag booleana indicando viabilidade técnica e arquitetural."
    )
    
    # Configuração Absoluta do Harvester: Proteção de Imutabilidade da ETL.
    # O Harvester não cria, não deduz e não acrescenta colunas não homologadas.
    model_config = {
        "strict": True,        # Tipagem forte absoluta (Int é Int, String é String).
        "frozen": True,        # Prevenção total contra State-Mutation ou Side-Effects silenciosos na pipeline ETL.
        "extra": "forbid"      # Aniquilação de Payload Pollution ou ingestão de parâmetros não declarados.
    }
```

O sistema de processamento **permitirá e gravará deliberadamente** informações e strings como `"node_modules"`, `"react"`, `"webpack"`, `"bun"` ou `"nextjs"` no campo de array referenciado `detected_toxic_deps`. Esta decisão de design garante que possamos mapear o inimigo sistêmico, estudar seus algoritmos refinados que se escondem por trás de pesadas camadas de abstração imperfeitas, e extrair de forma asséptica o seu "ouro" (as fórmulas puras, as estruturas de dados inovadoras, os paradigmas funcionais avançados).

Tudo isto é concluído assegurando que a arquitetura original desses projetos _nunca_ contamine, escape da quarentena, ou execute no santuário que será o Runtime Soberano definitivo do ecossistema SODA.