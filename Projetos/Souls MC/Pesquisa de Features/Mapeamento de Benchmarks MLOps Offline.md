# Dossiê Técnico: Mapeamento Bare-Metal de Datasets Desidratados para a Arena SODA

## Arquitetura Local-First e Fundamentação Bare-Metal

A implementação de pipelines de avaliação local para modelos de linguagem em ambientes com restrições severas de hardware exige uma estratégia estrita de engenharia de dados _bare-metal_. A execução em GPUs de consumo de classe móvel, como a NVIDIA RTX 2060 Mobile dotada de 6 GB de VRAM, impõe limites intransponíveis à pegada de memória do sistema. O ecossistema padrão de processamento de dados do Hugging Face, centrado na biblioteca `datasets` e na função `load_dataset()`, introduz camadas de abstração, dependências de tempo de execução e mecanismos de _caching_ dinâmico que frequentemente falham ou consomem memória RAM e VRAM de forma desnecessária em cenários offline.

A Arena SODA prevê um pipeline de _benchmarking_ puramente offline, onde a estabilidade do processo depende do descarregamento direto de arquivos físicos desidratados nos formatos `.json`, `.jsonl` ou Parquet. O conceito de desidratação implica a redução prévia da densidade do contexto: remoção de código-fonte irrelevante, pré-filtragem via BM25, conversão de matrizes multidimensionais em texto simples e segmentação de problemas monolíticos em subproblemas atômicos.

O fluxo de dados da Arena SODA dispensa requisições em tempo de execução para APIs externas ou interpretadores do Hugging Face. Os arquivos físicos brutos são armazenados no disco local e consumidos diretamente por _parsers_ em Python com mapeamento de memória (_zero-copy memory mapping_). Os _prompts_ formatados são então injetados sequencialmente na GPU para geração de respostas com tamanho de lote (_batch size_) estritamente unitário, viabilizando o controle total sobre a alocação da KV-Cache.

## Mapeamento Detalhado dos Benchmarks Desidratados

### 1. JSONSchemaBench

- **A) Link Direto de Download do Arquivo Cru / Repositório**: O repositório principal no Hugging Face Hub é `epfl-dlab/JSONSchemaBench`, complementado pelo repositório GitHub `guidance-ai/jsonschemabench`. A árvore direta de arquivos brutos para navegação e download pode ser acedida em `https://huggingface.co/datasets/epfl-dlab/JSONSchemaBench/tree/main`.
- **B) Split / Subpasta Desidratada**: Para prevenir estouro de VRAM na RTX 2060m, o subset recomendado é o `Github_easy` ou `Github_trivial`. A subpasta `data/` abriga as variantes do benchmark, sendo que o subset `Github_easy` foi explicitamente limpo para eliminar qualquer amostra com extensão superior a 1024 tokens. Esta desidratação foca na validação rigorosa de gramática e restrições de schema JSON.
- **C) Status de Autenticação**: Público e Aberto. O repositório opera sob a licença MIT, permitindo requisições HTTP diretas aos arquivos sem necessidade de chaves de API ou _tokens_ de utilizador.

### 2. BFCL v4 Agentic (Berkeley Function Calling Leaderboard)

- **A) Link Direto de Download do Arquivo Cru / Repositório**: O repositório oficial é mantido no GitHub sob a organização Gorilla em `ShishirPatil/gorilla`. Os arquivos brutos de dados para avaliação estão localizados na pasta `https://github.com/ShishirPatil/gorilla/tree/main/berkeley-function-call-leaderboard/bfcl_eval/data`.
- **B) Split / Subpasta Desidratada**: A subpasta exata onde residem os dados de teste sem misturar conjuntos de treino é `berkeley-function-call-leaderboard/bfcl_eval/data/`. Os arquivos JSONL/JSON desidratados contendo unicamente os prompts de avaliação para chamadas de função e agente são o `BFCL_v4_multi_turn_base.json` e o `possible_answer/BFCL_v4_web_search.json`.
- **C) Status de Autenticação**: Público e Aberto. Licenciado sob Apache-2.0, o repositório permite o download do arquivo bruto via `raw.githubusercontent.com` sem qualquer autenticação.

### 3. ARC-AGI-2 (Minified)

- **A) Link Direto de Download do Arquivo Cru / Repositório**: A versão minificada com matrizes convertidas em texto para prompts curtos é mantida no repositório GitHub `TrelisResearch/minimal-arc`. O repositório oficial de dados do ARC-AGI-2 pode ser consultado em `https://github.com/arcprize/ARC-AGI-2` e o do ARC-AGI-1 em `https://github.com/fchollet/ARC-AGI`.
- **B) Split / Subpasta Desidratada**: No repositório `TrelisResearch/minimal-arc`, as matrizes do ARC são achatadas em cadeias de texto simples (ex: valores separados por vírgula ou espaço), reduzindo a dimensão dos _prompts_ para o intervalo de 100 a 200 tokens. No repositório padrão `ARC-AGI-2`, a subpasta `data/evaluation/` abriga os arquivos JSON individuais de cada tarefa.
- **C) Status de Autenticação**: Público e Aberto. Registado sob a licença Apache 2.0, livre de qualquer bloqueio ou restrição de conta no Hugging Face.

### 4. SciCode (Subproblemas)

- **A) Link Direto de Download do Arquivo Cru / Repositório**: O benchmark está hospedado no Hugging Face em `SciCode1/SciCode` e no GitHub em `https://github.com/scicode-bench/SciCode`. Os arquivos brutos no Hugging Face encontram-se em `https://huggingface.co/datasets/SciCode1/SciCode/tree/main`.
- **B) Split / Subpasta Desidratada**: A versão focada em subproblemas é o arquivo `SciCode.json`. Em vez de exigir a geração do código-fonte completo de uma simulação científica complexa, o arquivo desidrata o problema em 338 subproblemas atômicos encadeados. Para a execução de testes numéricos de validação sem carregar bibliotecas externas pesadas, o arquivo `eval/data/test_data.h5` no GitHub fornece as metas de asserção.
- **C) Status de Autenticação**: Público e Aberto. Licenciado sob Apache-2.0, permitindo o descarregamento direto dos arquivos brutos.

### 5. SWE-bench (Versões Lite / BM25 Dehydrated)

- **A) Link Direto de Download do Arquivo Cru / Repositório**: Os datasets desidratados com contexto reduzido via algoritmo BM25 estão disponíveis nos repositórios Hugging Face `princeton-nlp/SWE-bench_Lite_bm25_13K` e `princeton-nlp/SWE-bench_Lite_bm25_27K`. O conjunto _SWE-bench Verified_ padronizado encontra-se em `https://huggingface.co/datasets/SWE-bench/SWE-bench_Verified/tree/main`.
- **B) Split / Subpasta Desidratada**: A subpasta/split essencial para hardware restrito é a `SWE-bench_Lite_bm25_13K`. Esse conjunto extrai unicamente os arquivos e trechos de código mais relevantes identificados por busca BM25, reduzindo o tamanho do prompt para a janela de 8k a 16k tokens e dispensando o download de gigabytes de repositórios Git completos.
- **C) Status de Autenticação**: Público e Aberto. O dataset é aberto e não exige aceite de termos de licenciamento restritivos nem _tokens_ de autenticação.

### 6. BABILong

- **A) Link Direto de Download do Arquivo Cru / Repositório**: O repositório principal no Hugging Face é `RMT-team/babilong`, com uma versão alternativa de amostragem reduzida em `RMT-team/babilong-1k-samples`. Os arquivos brutos podem ser visualizados e baixados diretamente em `https://huggingface.co/datasets/RMT-team/babilong/tree/main`.
- **B) Split / Subpasta Desidratada**: O dataset é segmentado por tarefas (`qa1` a `qa10`) e por extensões de contexto em tokens. Para a Arena SODA, as subpastas a utilizar são exclusivamente as de `4k`, `8k` e `16k` (como a pasta `16k/` que contém arquivos Parquet/JSONL como `qa1-00000-of-00001.parquet`). Estas versões desidratadas testam a degradação de atenção (_context rot_) em janelas estritamente controladas.
- **C) Status de Autenticação**: Público e Aberto. Livre de mecanismos de bloqueio (_gated_), acessível via requisições HTTP standard aos arquivos físicos.

## Lista de Compras Técnica e Especificações do Pipeline

A tabela a seguir apresenta a consolidação técnica necessária para a automação da rotina de ingestão no pipeline offline da Arena SODA.

|**Benchmark**|**Repositório / Fonte Oficial**|**Split / Subpasta Desidratada**|**Formato do Arquivo**|**Status de Autenticação**|**Estimativa de Tamanho**|
|---|---|---|---|---|---|
|**JSONSchemaBench**|`epfl-dlab/JSONSchemaBench`<br><br>[cite: 7]|`Github_easy` / `Github_trivial`<br><br>[cite: 7, 12]|`.parquet` / `.json`<br><br>[cite: 7, 12]|Público (MIT)|~10 - 20 MB|
|**BFCL v4 Agentic**|`ShishirPatil/gorilla`<br><br>[cite: 2, 14]|`berkeley-function-call-leaderboard/bfcl_eval/data/`<br><br>[cite: 14, 15]|`.json` / `.jsonl`<br><br>[cite: 14, 18]|Público (Apache-2.0)|< 15 MB|
|**ARC-AGI-2 (Minified)**|`TrelisResearch/minimal-arc`<br><br>[cite: 9]|Root / `data/evaluation/` (Achatado)|`.json` / `.jsonl`<br><br>[cite: 9, 20]|Público (Apache-2.0)|< 5 MB|
|**SciCode**|`SciCode1/SciCode`<br><br>[cite: 22]|Root / `SciCode.json` (Subproblemas)|`.json`<br><br>[cite: 22]|Público (Apache-2.0)|< 5 MB|
|**SWE-bench (BM25)**|`princeton-nlp/SWE-bench_Lite_bm25_13K`<br><br>[cite: 8]|`data/` / `default` (Contexto BM25)|`.parquet` / `.jsonl`<br><br>[cite: 8]|Público|~10 - 50 MB|
|**BABILong**|`RMT-team/babilong`<br><br>[cite: 26, 28]|Splits `4k`, `8k`, `16k` (Tarefas `qa1`-`qa10`)|`.parquet` / `.json`<br><br>[cite: 26, 27]|Público|~38 MB por split|

## Diretrizes de Engenharia Bare-Metal e Gestão de Memória VRAM

O dimensionamento do pipeline para a GPU NVIDIA RTX 2060 Mobile (6 GB VRAM) exige rigor técnico no cálculo da pegada de memória do modelo e do seu contexto. O orçamento total de VRAM ($M_{\text{total}} = 6144\text{ MB}$) é alocado prioritariamente para os pesos do modelo quantizado, restando uma margem estreita para o buffer da chave-valor (KV-Cache) e tensores temporários de ativação.

A pegada de memória em bytes ocupada pela KV-Cache em precisão total ou parcial é calculada teoricamente pela equação:

$$M_{\text{KV}} = 2 \times b \times s \times l \times h \times d_{\text{head}} \times p$$

Onde:

- $b$ representa o tamanho do lote (_batch size_), que deve ser rigorosamente configurado como $b = 1$.
- $s$ representa o comprimento da sequência em tokens (janela de contexto total: _prompt_ + geração).
- $l$ representa o número total de camadas (_transformer layers_) da arquitetura do modelo.
- $h$ representa o número de cabeças de atenção de chave e valor (Key/Value heads; reduzido em arquiteturas _Grouped-Query Attention_ - GQA).
- $d_{\text{head}}$ representa a dimensão individual de cada cabeça de atenção.
- $p$ representa a precisão em bytes (ex.: $p = 2$ para FP16/BF16, $p = 1$ para INT8/FP8).

Ao carregar um modelo de 7 bilhões de parâmetros quantizado em 4 bits (GGUF/Q4_K_M ou AWQ/GPTQ), os pesos ocupam aproximadamente 3,8 GB a 4,2 GB de VRAM. A alocação restante de cerca de 1,8 GB a 2,0 GB estabelece um teto estrito para $M_{\text{KV}}$. Quando submetido aos testes do **BABILong** (16k tokens) ou do **SWE-bench BM25** (13k tokens), o uso de precisão nativa FP16 na KV-Cache estouraria a VRAM disponível, provocando interrupção por OOM (_Out-Of-Memory_).

Para mitigar a saturação de memória, a engenharia do pipeline bare-metal deve aplicar quantização da KV-Cache em FP8 ou INT8 e impor limites severos de truncamento de contexto. O pré-processamento local de dados deve ler os arquivos `.parquet` ou `.jsonl` baixados, extrair os campos de _prompt_ relevantes e converter o fluxo de texto diretamente em memória RAM do sistema host, transferindo para a VRAM apenas o tensor codificado de entrada necessário para a execução do passe direto (_forward pass_).

## Alertas Operacionais e Recomendações de Ingestão

Com base na varredura detalhada realizada nos repositórios do Hugging Face Hub e do GitHub, é possível emitir os seguintes pareceres operacionais para a Arena SODA:

1. **Ausência de Restrições "Gated"**: NENHUM dos seis conjuntos de dados desidratados requisitados possui status "Gated" ou exige a geração prévia de _Tokens_ de Utilizador do Hugging Face. Todos os repositórios são totalmente públicos e abertos para acesso e download.
2. **Independência de Código Dinâmico**: Para manter a premissa de um ambiente _bare-metal_ offline, a pipeline local de ingestão deve utilizar utilitários padrão de transferência (como `curl` ou `wget`) para salvar os arquivos brutos em disco, seguida por scripts de conversão interna baseados em `pandas` ou no módulo nativo `json` de Python. Esta abordagem elimina falhas decorrentes de atualizações da biblioteca `datasets` ou da falta de scripts de dataset customizados no Hugging Face Hub.
3. **Estratégia de Truncamento no SWE-bench e BABILong**: O pipeline de testes deve validar o comprimento exato do tokenizador para cada amostra dos datasets **SWE-bench Lite BM25** e **BABILong**. Amostras que excedam a capacidade limite alocada para a KV-Cache (calculada pela fórmula teórica) devem ser ignoradas ou truncadas no limite de 16.384 tokens para preservar a estabilidade da GPU RTX 2060m.