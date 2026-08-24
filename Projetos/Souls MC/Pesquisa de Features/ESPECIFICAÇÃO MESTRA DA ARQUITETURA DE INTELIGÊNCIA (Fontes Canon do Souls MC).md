---
aliases:
  - ESPECIFICAÇÃO MESTRA DA ARQUITETURA DE INTELIGÊNCIA (Fontes "Canon" do Souls MC)
---

# ESPECIFICAÇÃO MESTRA DA ARQUITETURA DE INTELIGÊNCIA (Fontes "Canon" do Souls MC)

## Projeto: Souls MC - Canon Base - v7 - Unified

**Data de Emissão:** Março de 2026
**Target Runtime:** Executável Nativo em Rust (Edição 2021)
**Objetivo Primário:** Unificação, curadoria semântica, enriquecimento e indexação hierárquica (RAG) de todas as fontes históricas (Gemini Notebook/NotebookLM e Obsidian) e criação da pipeline reutilizável de ingestão contínua.

## 1. VISÃO GERAL E ESCOPO DO PROJETO

O **Souls MC** está migrando e unificando todo o seu acervo de conhecimento — relatórios, atas de reunião, pesquisas de mercado, diretrizes e análises estratégicas — atualmente disperso em duas fontes primárias:

1. **Google NotebookLM (Gemini Notebook):** Aproximadamente 260 fontes (entre notas, resumos gerados por IA e documentos importados).
2. **Obsidian Vault Local:** Repositório local em Markdown. (sem estruturação correta (ainda))

### A Missão do Sistema

Criar a nova base de dados definitiva e soberana denominada **`Souls MC - Canon Base - v7 - Unified`**. Esta base deve operar como uma "Wiki/Cérebro Orgânico" local, em que a busca de inteligência por LLMs não dependa do nome ou formato do arquivo de origem, mas sim da **sabedoria conceitual capturada, estruturada e relacionada nos bancos de dados**.

## 2. ARQUITETURA EM DUAS FASES

O agente deve implementar uma arquitetura estritamente dividida em duas fases operacionais:

### Fase 1: Migração e Unificação Massiva Inicial (One-Time Extração)

- **Extração do NotebookLM:** Raspagem e conversão de todas as ~260 fontes, conversas de estúdio e notas para arquivos Markdown (`.md`) locais, preservando as marcas de tempo (`timestamps`) de criação original armazenadas na nuvem.
- **Conversão de Documentos Brutos (PDFs, Office):** Processamento de arquivos binários anexados através de utilitários efêmeros de CLI (IBM Docling para PDFs/tabelas complexas e Microsoft MarkItDown para documentos Word/Excel/PPTX).
- **Deduplicação Semântica:** Identificação de fontes redundantes (pois muitas fontes do NotebookLM vieram originalmente do Obsidian) e fusão sintética dos conteúdos sobrepostos.

### Fase 2: Pipeline Reutilizável de Ingestão e RAG Local em Rust (Runtime Permanente)

- **Motor de Ingestão Continuo:** Pipeline desenvolvida 100% em **Rust**. Sempre que uma nova pesquisa, ata ou arquivo for adicionado no futuro, o sistema trata, enriquece e indexa o documento automaticamente. (verificar como isso será "acionado")
- **Base Canônica Local:** Geração de Nova e Manutenção de estrutura física em arquivos Markdown dentro do Obsidian, organizada por "Esferas de Conhecimento" e enriquecida com metadados YAML e `[[wikilinks]]`.
- **Indexação Híbrida e Hierárquica:** Armazenamento dos conceitos no ecossistema de bancos de dados locais (FrankSQLite + LanceDB + LadyBugDB / `reasonkit-mem`) utilizando busca vetorial, busca léxica BM25 e resumos hierárquicos RAPTOR.

## 3. REGRAS E RESTRIÇÕES DE ARQUITETURA (CONSTRAINTS)

1. **Linguagem Principal:** **Rust**. O _runtime_ do Souls MC deve ser um binário nativo e ultraeficiente em Rust.
2. **Proibição de Bloat e Microsserviços:** **Proibido** manter serviços ou contêineres persistentes em Python ou Node.js rodando em segundo plano no ambiente de produção.
3. **Subprocessos Efêmeros:** Ferramentas de conversão de documentos baseadas em Python (como Docling ou MarkItDown) só podem ser chamadas pelo Rust via subprocesso no momento exato de ingestão de um PDF/Office, sendo encerradas imediatamente após a conversão para `.md`.
4. **Dependências Enxutas (`Cargo.toml`):** Ativar apenas _crates_ especializadas com `default-features = false` para evitar tempos longos de compilação e sobrecarga de memória.
5. **Privacidade e Soberania Total:** Toda a vetorização, busca, indexação em grafo e sumarização devem rodar de forma 100% local e ilimitada.

## 4. PROTOCOLO DE DEDUPLICAÇÃO E FUSÃO SEMÂNTICA

A eliminação de redundâncias entre o NotebookLM e o Obsidian segue uma estratégia em três camadas:

### Camada A: Hash Determinístico

Execução do hash **BLAKE3** ou **SHA-256** sobre o texto limpo. Arquivos com hash 100% idêntico são unificados imediatamente, registrando os caminhos de origem nos metadados.

### Camada B: Similaridade Semântica por Embeddings

Cálculo do vetor de embedding de cada documento e avaliação da Similaridade de Cosseno:

$$\text{Similaridade}(A, B) = \frac{A \cdot B}{\Vert{}A\Vert{} \Vert{}B\Vert{}} = \frac{\sum_{i=1}^{n} A_i B_i}{\sqrt{\sum_{i=1}^{n} A_i^2} \sqrt{\sum_{i=1}^{n} B_i^2}}$$

### Camada C: Regras de Decisão por Limiar

|**Limiar de Similaridade**|**Classificação**|**Ação do Agente**|
|---|---|---|
|**$\ge 0,95$**|Duplicata Semântica Quase Identica|Retém o arquivo de maior densidade/riqueza textual e mescla os metadados.|
|**$0,85$ a $0,94$**|Sobreposição Conceitual com Dados Complementares|Aciona LLM local para realizar **Destilação Sintética**: gera um novo documento canônico unificado sem repetições.|
|**$< 0,85$**|Conteúdo Autônomo Único|Mantém o documento como nota canônica individual.|

## 5. ESTRUTURA DE DADOS, TAXONOMIA E YAML FRONTMATTER

### Estrutura do Repositório (`Souls_MC_Canon_Base_v7_Unified/`)

O repositório local em Markdown deve ser organizado nas seguintes "Esferas de Entendimento":

- `01_Diretrizes_Estrategicas/`: Visão executiva, teses centrais, escopo e governança.
- `02_Pesquisas_e_Fundamentacao/`: Estudos, benchmarking, referências técnicas e artigos.
- `03_Relatorios_e_Analises/`: Diagnósticos operacionais, relatórios e análises extraídas.
- `04_Atas_e_Discussoes_Refinadas/`: Transcrições tratadas, sínteses de reuniões e decisões.
- `05_Sinteses_e_Destilacoes/`: Documentos de alta densidade informativa gerados por fusão semântica.

OBS.: Se após análises dos documentos se entender que existem outras melhores maneiras de organizar, podemos alterar essa estrutura e/ou ampliar ela. É apenas uma estrutura "Base" para começar, mas talvez tenha maneiras melhores de organizar ao entender o conteúdo das fontes.

### Esquema do Cabeçalho YAML (Mandatório)

Todo arquivo `.md` tratado e inserido na base deve conter obrigatoriamente o seguinte cabeçalho no topo:

```YAML
---
canonical_id: "SMC-CANON-V7-001"
title: "Título Claro e Conciso do Documento"
esfera_conhecimento: "02_Pesquisas_e_Fundamentacao"
tags:
  - "arquitetura"
  - "souls-mc"
  - "pesquisa"
fontes_origem:
  - "Gemini_Notebook_Source_12.md"
  - "Obsidian_Vault/Arch_Notes.md"
versao_massa: "v7.0-Unified"
created_at: "2024-11-15T14:30:00Z" # Timestamp original resgatado
updated_at: "2026-03-30T20:00:00Z" # Timestamp do tratamento
valid_from: "2026-03-30"
supersedes: null # ID do documento antigo se este o substituir
status: "ativo" # Opções: ativo, rascunho, obsoleto
nivel_confiabilidade: "Canônico"
---
```

## 6. ARQUITETURA DE BANCO DE DADOS E RAG (RUST NATIVO)

Para permitir que a LLM responda com contexto absoluto sobre o projeto, o agente deve integrar três motores operando em conjunto sob a stack do Souls MC (FrankSQLite + LanceDB + LadyBugDB) ou abstraídos pela crate embutida **`reasonkit-mem`**:

```
[Entrada de Consulta do Usuário / LLM]
              │
              ├──► 1. Busca Léxica BM25 (FrankSQLite FTS5 / Tantivy) ────► Termos exatos, IDs, Códigos
              │
              ├──► 2. Busca Vetorial Densa (LanceDB / Embeddings) ──────► Similaridade semântica direta
              │
              ├──► 3. Grafo Semântico (LadyBugDB / LightRAG) ───────────► Relações entre Entidades
              │
              └──► 4. Árvore Hierárquica RAPTOR (Resumos LLM) ──────────► Análises globais e sínteses
```

### Detalhamento dos Componentes RAG:

1. **Busca Híbrida (BM25 + Vetor):** Unifica a busca por palavras-chave e códigos exatos (via módulo FTS5 do SQLite) com a busca semântica por proximidade vetorial no LanceDB.
2. **Grafo de Conhecimento (LightRAG / LadyBugDB):** Mapeia conexões conceituais entre documentos e entidades através de Wikilinks (`[[nota]]`) e relações de substituição (`supersedes`).
3. **Indexação Hierárquica RAPTOR:** O sistema agrupa vetores de _chunks_ por similaridade (usando GMM), gera resumos desses grupos via LLM e re-vetoriza os resumos em camadas sucessivas. Consultas globais ou conceituais acessam o topo da árvore; dúvidas técnicas pontuais acessam a base.

### Gestão do Tempo e Invalidação de Dados Antigos

- **Decaimento Temporal (Time-decay Weighting):** Na busca vetorial, fragmentos com datas (`updated_at`) mais recentes recebem leve acréscimo de pontuação para desambiguação.
- **Resolução de Conflitos:** Se um documento novo contradisser uma diretriz antiga, a LLM marca o metadado do documento antigo como `status: obsoleto` e o grafo registra a relação `substituido_por`, garantindo que a LLM conheça o histórico sem confundir decisões passadas com as diretrizes atuais.

## 7. CRATES RUST SELECIONADAS E RESPONSABILIDADES

O agente deve utilizar estritamente as seguintes crates Rust no manifesto `Cargo.toml`:

- **`gray_matter` (v0.3):** Utilizada para parsing e extração rápida do YAML Frontmatter sem a necessidade de carregar a AST completa do Markdown na memória.
- **`pulldown-cmark` (v0.13, `default-features = false`, `features = ["simd"]`):** Pull parser orientado a eventos para ler o corpo do Markdown e gerar _byte offsets_ exatos (`into_offset_iter()`) para fragmentação (_chunking_) inteligente por parágrafos.
- **`serde` & `serde_json`:** Para serialização e deserialização tipada dos metadados e objetos de estado.
- **`reasonkit-mem` (v0.1):** Crate em Rust para gerenciamento de vetores embutidos, BM25 via Tantivy, busca híbrida e construção nativa das árvores RAPTOR.
- **`rusqlite` / `lancedb`:** Para comunicação nativa direta com a camada SQLite e LanceDB.

## 8. PLANO DE EXECUÇÃO PASSO A PASSO PARA O AGENTE DE CÓDIGO

Ao iniciar o desenvolvimento, o agente de IA na IDE deve seguir sequencialmente estes passos:

### Passo 1: Estruturação do Projeto e Cargo.toml

Criar a estrutura do módulo de ingestão em Rust e configurar o `Cargo.toml` ativando estritamente as crates e _features_ especificadas no Item 7.

### Passo 2: Módulo de Conversão do Lote Inicial (Extração NotebookLM & Binary Converters)

- Criar script CLI para exportar fontes do NotebookLM em formato `.md` capturando os _timestamps_ do caderno.
- Implementar invocador de subprocessos efêmeros em Rust para passar PDFs/documentos do Office pelo IBM Docling / MarkItDown. (Verificar a necessidade, pois acredito que 99% vai vir como .md já, apenas sem "trato" nenhum)

### Passo 3: Parser de Markdown, Metadados e Chunking

- Implementar função em Rust combinando `gray_matter` (para extrair e deserializar a struct `CanonMetadata`) e `pulldown-cmark`.
- Usar `into_offset_iter()` do `pulldown-cmark` para recortar o texto em fragmentos baseados nos limites reais de parágrafos/seções.

### Passo 4: Pipeline de Deduplicação e Enriquecimento LLM

- Escrever o verificador de hash (BLAKE3).
- Implementar função de similaridade de cosseno para os embeddings.
- Criar a rotina de chamada à LLM local para: (1) Gerar YAML Frontmatter padronizado; (2) Inserir `[[wikilinks]]`; (3) Executar a destilação sintética em casos de similaridade entre $0,85$ e $0,94$.

### Passo 5: Indexação no Banco RAG (Vetores + BM25 + RAPTOR)

- Instanciar o armazenamento do `reasonkit-mem` em modo embutido.
- Ingerir os fragmentos canônicos, criando o índice esparso BM25 e os vetores densos.
- Executar a rotina de agrupamento e geração recursiva de resumos para erguer a árvore hierárquica RAPTOR.

### Passo 6: Servidor de Consulta e Ingestão Incremental

- Criar o pipeline de ingestão contínua, para adições futuras.
- Implementar a atualização incremental da árvore RAPTOR: recalcular **apenas os resumos intermediários afetados** pelos novos documentos sem reprocessar a base inteira.

## 9. CONTRATOS DE PROMPTS PARA LLM LOCAL (PROMPT TEMPLATES)

Para evitar alucinações durante o enriquecimento da base, o agente deve implementar as seguintes estruturas de instrução ao chamar a LLM local:
### A. Prompt para Geração de YAML Frontmatter e Wikilinks (Exemplo)

```
Você é um Engenheiro de Conhecimento encarregado de classificar e estruturar notas do projeto 'Souls MC'.

Analise o texto abaixo e retorne EXCLUSIVAMENTE um bloco YAML válido delimitado por '---'.

Diretrizes:

1. Extraia o título mais preciso.
2. Identifique a esfera de conhecimento (Escolha entre: 01_Diretrizes_Estrategicas, 02_Pesquisas_e_Fundamentacao, 03_Relatorios_e_Analises, 04_Atas_e_Discussoes_Refinadas, 05_Sinteses_e_Destilacoes).
3. Crie de 3 a 5 tags em kebab-case pertinentes ao projeto.
4. Identifique conceitos chave no texto e sugira 2 a 4 [[wikilinks]] para notas relacionadas.
   
Documento de Entrada:
{TEXTO_DO_DOCUMENTO}
```


### B. Prompt para Destilação Sintética (Fusão de Fontes $0,85 - 0,94$) (Exemplo)

```
Você é um Especialista em Síntese de Informações do projeto 'Souls MC'.

Sua tarefa é fundir o Documento A e o Documento B em um único Documento Canônico Unificado.

Regras Estritas:

1. Elimine todas as redundâncias e paráfrases repetitivas.
2. Preserve 100% das decisões técnicas, parâmetros, datas e nomes específicos presentes em ambos os documentos.
3. Se houver contradição entre os documentos, mantenha a informação com a data mais recente ou explicite a divergência.
4. Escreva o texto final em Markdown estruturado, mantendo alta densidade de informação.

Documento A:
{TEXTO_DOC_A}

Documento B:
{TEXTO_DOC_B}
```

## 10. CONTRATO DE COMUNICAÇÃO HTTP COM LLM LOCAL

O binário Rust deve se comunicar com o servidor LLM local utilizando.

- **Configuração Recomendada:** `temperature: 0.1` para extração de metadados e deduplicação (foco em precisão determinística); `temperature: 0.3` para a construção dos resumos da árvore RAPTOR.
- **Timeout Rígido:** As chamadas para a LLM local devem possuir um _timeout_ assíncrono máximo de 60 segundos por requisição no Tokio Runtime, prevenindo que o pipeline de ingestão trave em documentos excepcionalmente longos.

## 11. RESILIÊNCIA, FALLBACKS E TRATAMENTO DE ERROS

O agente de código deve garantir que o sistema seja tolerante a falhas no processamento de lotes:

1. **Recuperação de YAML Malformado:** Se a crate `gray_matter` falhar em decodificar o Frontmatter devido a caracteres inválidos gerados por conversões antigas, o Rust deve aplicar um regex de sanitização e, caso persista o erro, injetar um cabeçalho padrão com a tag `status: necessita_revisao_manual` sem interromper a execução do lote.
2. **Fallback de Conversores de PDF/Office:** Se a chamada via subprocesso efêmero ao IBM Docling ou MarkItDown retornar um código de erro diferente de zero (por ex., PDF protegido com senha ou corrompido), o Rust deve capturar a saída de erro (`stderr`), mover o arquivo bruto para a pasta `/errors` e registrar um evento detalhado no arquivo `ingestion_errors.log`.
3. **Atomicidade no Banco de Dados:** Todas as operações de inserção no `reasonkit-mem` (vetores + BM25 + RAPTOR) devem ser tratadas em transações atômicas para garantir que uma interrupção inesperada não deixe índices vetoriais parciais ou corrompidos.

## 12. PROTOCOLO DE TESTES E VALIDAÇÃO PARA O AGENTE

Para validar que o desenvolvimento em Rust foi concluído com sucesso, o agente de IA na IDE deve criar e executar a seguinte suíte de testes unitários e de integração (`cargo test`):

- **`test_frontmatter_extraction`**: Garante que o `gray_matter` consegue ler o cabeçalho YAML e deserializar a struct de metadados sem erros.
- **`test_markdown_chunking_offsets`**: Garante que os limites de bytes (`byte_start` e `byte_end`) retornados pelo `pulldown-cmark` correspondem exatamente ao texto original em Markdown.
- **`test_deduplication_thresholds`**: Valida se documentos fictícios com alta similaridade são redirecionados para a fusão e os distintos mantidos como notas independentes.
- **`test_raptor_tree_building`**: Garante que uma sequência de 10 fragmentos de teste gera uma árvore hierárquica com pelo menos 2 níveis de resumos dentro da biblioteca `reasonkit-mem`.