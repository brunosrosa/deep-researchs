# Dossiê de Benchmarking e Avaliação FinOps de Módulos Sidecar para Edge AI em Hardware Restrito

A implantação de um Sistema Operacional Agêntico Local (SODA) em hardware com restrições severas de memória de vídeo, como a GPU NVIDIA RTX 2060m equipada com apenas 6GB de VRAM, exige uma abordagem estrita de engenharia de sistemas e FinOps de hardware. Em cenários em que o modelo de linguagem principal (LLM) já ocupa a maior parte do espaço de desempenho e memória disponível, a adição de módulos auxiliares (_sidecars_) — tais como projetores visuais (`.mmproj`), módulos de predição multi-token (MTP) para decodificação especulativa, adaptadores LoRA e encoders locais — pode facilmente esgotar a VRAM, forçando a paginação para a memória RAM através do barramento PCIe e degradando drasticamente o tempo até o primeiro token (_Time-To-First-Token_ - TTFT) e a taxa de geração.

Este dossiê estabelece os protocolos de teste, conjuntos de dados de referência (_golden datasets_), métricas formais e metodologias de depuração para validar a viabilidade técnica e a eficiência de módulos _sidecar_ integrados aos motores de execução em Rust (`llama.cpp` via _bindings_ e `mistral.rs`).

## 1. Benchmarking de Módulos de Visão e Projetores Visuais

Em arquiteturas agênticas avançadas, a interpretação de interfaces digitais estruturadas (como aplicações web e ambientes de desktop) é realizada diretamente por meio do parsing do Modelo de Objeto do Documento (DOM) e de Árvores de Sintaxe Abstrata (AST). Essa abordagem reduz a dependência de chamadas visuais e elimina a sobrecarga computacional inerente ao processamento de pixels. No entanto, o subsistema visual é indispensável quando o agente se depara com artefatos não digitais ou documentos não estruturados, incluindo diagramas esquemáticos, recibos amassados, faturas digitalizadas com ruído e documentos com formatação complexa.

Nesses cenários, os modelos de visão e linguagem (VLMs) utilizam projetores visuais alocados em arquivos `.mmproj`, os quais convertem as características extraídas por um encoder de imagem (como o CLIP ou SigLIP) em _tokens_ visuais compatíveis com o espaço de embedding do LLM base.

### 1.1 Datasets de Ouro para Extração Estruturada e OCR Hostil

A avaliação da capacidade de extração estruturada (JSON) e interpretação gráfica por VLMs locais deve ser conduzida com conjuntos de dados consolidados na literatura open-source, eliminando a subjetividade de testes qualitativos.

|**Dataset**|**Domínio de Aplicação**|**Tarefa Primária**|**Métrica Principal**|
|---|---|---|---|
|**CORD** (Consolidated Receipt Dataset)|Faturas, recibos e comprovantes de compra|Extração de Informações-Chave (KIE) em JSON estruturado|Joint F1-Score / Exact Match (EM)|
|**SROIE** (Scanned Receipts OCR)|Documentos fiscais digitalizados com ruído e dobras|Reconhecimento de texto e preenchimento de esquema JSON|F1-Score e Acurácia por Campo|
|**DocVQA**|Documentos genéricos, relatórios e formulários|Pergunta-Resposta Visual em documentos complexos|ANLS (_Average Normalized Levenshtein Similarity_)|
|**ChartQA**|Gráficos de barras, linhas e pizza|Raciocínio lógico e extração de dados numéricos em gráficos|Relaxed Accuracy (margem de erro de 5%)|
|**AECV-Bench**|Desenhos de arquitetura e engenharia|Contagem de símbolos, OCR vetorial e raciocínio espacial|MAPE (_Mean Absolute Percentage Error_) e EM|
|**OCRBench**|Textos em cenários hostis, rotações e baixa resolução|Avaliação holística do motor de OCR do VLM|OCRBench Score (Escala 0-1000)|

O dataset **CORD** e o **SROIE** servem como referência direta para validar a fidelidade da sintaxe JSON gerada a partir de imagens com ruído físico. O **AECV-Bench** é crucial para mensurar a capacidade do projetor `.mmproj` em preservar a ancoragem espacial de elementos vetoriais e símbolos técnicos.

### 1.2 Medição da Eficiência Termodinâmica de Visão

A introdução de um módulo `.mmproj` afeta o orçamento de memória de duas formas: pela pegada estática dos pesos do próprio projetor e do encoder visual (geralmente entre 300MB e 1.2GB de VRAM) e pela pegada dinâmica decorrente da expansão do contexto. Cada imagem processada por um VLM é convertida em uma sequência de _tokens_ visuais (frequentemente de 576 a 2048 _tokens_), ocupando espaço substancial na cache Key-Value (KV) do modelo base.

A **Eficiência Termodinâmica de Visão** ($\eta_{\text{vis}}$) quantifica a relação entre a acurácia obtida pelo VLM na tarefa de extração e a quantidade total de VRAM consumida durante o pico do ciclo de inferência:

$$\eta_{\text{vis}} = \frac{\text{Score}_{\text{Dataset}} (\% )}{\text{VRAM}_{\text{Estática}} + \text{VRAM}_{\text{Dinâmica\_KV}} \text{ (MB)}}$$

Onde:

- $\text{Score}_{\text{Dataset}}$ representa a pontuação percentual obtida no dataset avaliado (por exemplo, F1-Score no CORD).
- $\text{VRAM}_{\text{Estática}}$ é a alocação de memória necessária para manter o LLM quantizado e o arquivo `.mmproj` carregados na VRAM.
- $\text{VRAM}_{\text{Dinâmica\_KV}}$ é a memória alocada estritamente para armazenar as chaves e valores dos _tokens_ visuais gerados pela imagem.

Em placas com 6GB de VRAM, se a expansão da cache KV durante a fase de _prefill_ ultrapassar o limite físico da GPU, o driver NVIDIA acionará a alocação na memória do sistema (_host system memory_), reduzindo drasticamente a velocidade do processamento de contexto devido à limitação de largura de banda do barramento PCIe 3.0/4.0 ($16\text{ GB/s}$ contra $>300\text{ GB/s}$ da VRAM GDDR6). O profiling deve monitorar continuamente o uso de memória via biblioteca NVML (`nvmlDeviceGetMemoryInfo`), rejeitando projetores visuais que apresentem $\eta_{\text{vis}} < 0,12 \text{ pontos/MB}$.

## 2. Metodologia de Benchmarking para Decodificação Especulativa e MTP

A decodificação especulativa e a predição multi-token (MTP) são técnicas projetadas para mitigar o gargalo de largura de banda de memória durante a fase de geração autorregressiva de _tokens_. A técnica funciona prevendo múltiplos _tokens_ futuros por meio de um mecanismo leve ("rascunho") e validando a sequência proposta de forma paralela em um único passo de execução no modelo principal.

### 2.1 Implementações em llama.cpp e mistral.rs

Os motores de execução em Rust e C++ oferecem abordagens distintas para a especulação de _tokens_:

1. **Modelos de Rascunho Independentes (`draft-simple`)**: Utilização de um modelo secundário menor (por exemplo, um modelo de 0.8B atuando como rascunho para um modelo base de 7B).
2. **Cabeças de Predição Integradas e EAGLE-3 (`draft-eagle3` / `draft-mtp`)**: Uso de cabeças de projeção adicionais treinadas para ler os estados ocultos (_hidden states_) das camadas superiores do modelo principal, prevendo _tokens_ subsequentes sem a necessidade de uma arquitetura autônoma.
3. **Decodificação Especulativa Baseada em N-Gramas (`ngram-mod`)**: Construção de tabelas de _hash_ dinâmicas na memória RAM que analisam o histórico do contexto e a saída gerada para propor rascunhos de sequências repetitivas sem executar qualquer rede neural adicional.

### 2.2 Avaliação Empírica do Impacto na VRAM de 6GB

A viabilidade técnica do uso de MTP e decodificação especulativa em ambientes com restrição de VRAM depende da inter-relação entre duas métricas primárias:

- **Taxa de Aceitação ($\alpha$)**: O percentual de _tokens_ propostos pelo mecanismo de rascunho que são validados e aceitos pelo modelo principal.
- **Razão de Aceleração ($S$)**: A proporção entre a velocidade de geração da abordagem especulativa e a geração monolítica convencional:
    
    $$S = \frac{\text{Tokens/s}_{\text{especulativo}}}{\text{Tokens/s}_{\text{baseline}}}$$

Em GPUs de 6GB, a alocação de um modelo de rascunho neural ou de cabeças EAGLE-3/MTP consome VRAM preciosa que, de outro modo, seria utilizada para estender a cache KV do modelo principal ou para evitar a quantização excessiva do peso base.

Demonstra-se empiricamente que, em modelos de menor porte ajustados para hardware de entrada, a sobrecarga de gerenciar duas caches KV separadas (para o modelo principal e para o rascunho) e a contenção no barramento de memória resultam frequentemente em razões de aceleração nulas ou **negativas** ($S < 1,0$), mesmo em cenários com altas taxas de aceitação ($\alpha > 75\%$).

Para a restrição estrita de 6GB VRAM, o mecanismo de especulação baseado em **n-gramas (`ngram-mod`)** mostra-se superior a modelos de rascunho neurais ou cabeças MTP. O `ngram-mod` utiliza uma pegada de memória RAM insignificante (aproximadamente 16MB no _host_), elimina a necessidade de alocar VRAM adicional e atinge taxas de aceleração $S$ entre $1,15$ e $1,45$ em tarefas fortemente estruturadas, como a escrita de código e a geração de documentos JSON.

### 2.3 Scripting e Comandos de Estresse Padronizados

Para quantificar a taxa de aceitação e o impacto real na velocidade de geração em `llama.cpp`, a suíte de testes deve utilizar o binário otimizado `llama-cli` com suporte a métricas de desempenho internas.

#### Execução de Teste Baseline (Sem Especulação)

Bash

```
llama-cli -m qwen2.5-7b-instruct-q4_k_m.gguf \
  -c 4096 \
  -ngl 999 \
  -n 512 \
  --perf \
  -p "Gerar uma estrutura JSON válida contendo os itens de uma fatura comercial com 10 produtos detalhados."
```

#### Execução com Especulação N-Gram (Zero footprint de VRAM)

Bash

```
llama-cli -m qwen2.5-7b-instruct-q4_k_m.gguf \
  -c 4096 \
  -ngl 999 \
  -n 512 \
  --spec-type ngram-mod \
  --spec-ngram-mod-n-match 24 \
  --spec-ngram-mod-n-min 48 \
  --spec-ngram-mod-n-max 64 \
  --perf \
  -p "Gerar uma estrutura JSON válida contendo os itens de uma fatura comercial com 10 produtos detalhados."
```

#### Execução com Modelo de Rascunho Neural (Avaliação de Sobrecarga de VRAM)

Bash

```
llama-cli -m qwen2.5-7b-instruct-q4_k_m.gguf \
  -md qwen2.5-0.5b-instruct-q4_k_m.gguf \
  -c 4096 \
  -ngl 999 \
  -ngld auto \
  -n 512 \
  --spec-type draft-simple \
  --perf \
  -p "Gerar uma estrutura JSON válida contendo os itens de uma fatura comercial com 10 produtos detalhados."
```

Os dados emitidos pela flag `--perf` informam a quantidade exacta de _draft tokens_ aceitos, o tempo de verificação paralela e o consumo de VRAM relatorial. Se o tempo de processamento de verificação superar a economia obtida pela aceitação dos _tokens_, a abordagem deve ser considerada inviável para o perfil FinOps estabelecido.

## 3. Módulos Auxiliares: Adaptadores LoRA, Embeddings Locais e Síntese/Transcrição de Áudio

O ecossistema do SODA acopla adaptadores de baixo posto (_Low-Rank Adaptations_ - LoRAs) e modelos utilitários para tarefas de suporte, visando operar de forma autônoma sem dependência de serviços externos em nuvem.

### 3.1 Granularidade e Arquitetura de Módulos Micro-Sidecar

Os componentes auxiliares integrados ao sistema incluem:

- **Adaptadores LoRA Especializados**: Pequenas matrizes de pesos (de 10MB a 80MB) aplicadas sobre o modelo base para alterar seu comportamento em tarefas específicas (como geração de chamadas de função ou conversão de texto para consultas SQL). O motor `mistral.rs` destaca-se por permitir o carregamento e a troca dinâmica de múltiplos adaptadores (X-LoRA) por requisição, mantendo o modelo base congelado na VRAM.
- **Encoders de Embeddings de Alta Eficiência**: Modelos ultra-compactos projetados para vetorização semântica de textos, como o `bge-micro-v2`, `all-MiniLM-L6-v2` ou o `Qwen3-Embedding-0.6B`.
- **Módulos de Áudio (STT e TTS)**: Motores de Reconhecimento de Fala (_Speech-to-Text_ - STT), como o `Moonshine` ou `Whisper-Tiny`, combinados com sistemas de Síntese de Fala (_Text-to-Speech_ - TTS) leves, como o `Kokoro-82M`.

### 3.2 Avaliação da Qualidade de Embeddings Locais via MTEB

Para certificar se um encoder de embedding local pequeno atende aos requisitos do sistema agêntico sem a necessidade de recorrer à API `text-embedding-3-small` da OpenAI, utiliza-se a biblioteca **MTEB** (_Massive Text Embedding Benchmark_). O foco da avaliação deve concentrar-se nas sub-tarefas de Recuperação de Informação (_Retrieval_), Similaridade Semântica (_STS_) e Classificação de Intenções.

#### Script de Avaliação Comparativa via MTEB (Python)

Python

```
import time
import psutil
import mteb
from sentence_transformers import SentenceTransformer

# 1. Carregamento do modelo local em avaliação
model_id = "BAAI/bge-small-en-v1.5"
model = SentenceTransformer(model_id, device="cpu")

# 2. Seleção de tarefas alinhadas às operações do agente
task_names = [
    "SciFact",                 # Recuperação de documentos
    "STSBenchmark",            # Similaridade de texto
    "Banking77Classification"  # Classificação de intenções
]
tasks = mteb.get_tasks(tasks=task_names)

# 3. Profiling de memória e tempo de execução
process = psutil.Process()
ram_initial = process.memory_info().rss / (1024 * 1024)
start_time = time.time()

evaluation = mteb.Evaluation(model)
results = evaluation.run(tasks, output_folder="./results_mteb_local")

total_time = time.time() - start_time
ram_final = process.memory_info().rss / (1024 * 1024)

print(f"Tempo Total: {total_time:f}s | Impacto na RAM: {ram_final - ram_initial:f}MB")
```

#### Matriz de Comparação entre Embeddings Locais e API

|**Modelo de Embedding**|**Ambiente de Execução**|**Dimensões do Vetor**|**Pontuação MTEB Média**|**Latência Média por Chunk**|**Consumo de RAM/VRAM**|
|---|---|---|---|---|---|
|**text-embedding-3-small**|OpenAI API (Nuvem)|1536 (truncável para 512)|62.3%|~120ms (Dependente da rede)|0 MB (Local)|
|**Qwen3-Embedding-0.6B**|Local (CPU / GPU)|1024|61.5%|~18ms (GPU) / ~85ms (CPU)|~1.2 GB|
|**bge-small-en-v1.5**|Local (CPU)|384|58.4%|~8ms (CPU)|~130 MB (RAM)|
|**BGE-micro-v2**|Local (CPU)|384|54.1%|~3ms (CPU)|~45 MB (RAM)|

A análise de viabilidade demonstra que, para operações locais de busca em memória de curto prazo do agente, modelos como o `bge-small-en-v1.5` oferecem mais de 93% do desempenho do `text-embedding-3-small` da OpenAI, eliminando custos operacionais por token e flutuações de rede, enquanto demandam apenas 130MB de memória RAM no host.

### 3.3 Profiling e Medição de Latência de Áudio na CPU

Para evitar a contenção de recursos na GPU RTX 2060m, os módulos de voz (TTS e STT) devem ser executados nos núcleos da CPU. As métricas fundamentais para qualificar o desempenho dos motores de áudio são:

1. **Fator de Tempo Real (_Real-Time Factor_ - RTF)**:
    
    $$\text{RTF} = \frac{\text{Tempo de Processamento do Áudio (segundos)}}{\text{Duração Total do Áudio (segundos)}}$$
    
    Um valor de $\text{RTF} < 1,0$ indica que o processamento ocorre mais rápido do que a reprodução do áudio em tempo real.
    
2. **Tempo até o Primeiro Áudio (_Time-To-First-Audio_ - TTFA)**: O tempo decorrido (em milissegundos) entre o envio da primeira sentença de texto produzida pelo LLM e a emissão do primeiro buffer de áudio sintetizado pelo Kokoro-82M.

#### Isolamento de Threads e Afinidade de CPU

Para impedir que a síntese de áudio afete o desempenho de outras tarefas executadas no sistema operativo, utiliza-se a ferramenta `taskset` no Linux, fixando os processos de áudio em núcleos específicos da CPU:

Bash

```
# Execução isolada do modelo Kokoro-82M nos núcleos de 0 a 3 da CPU
taskset -c 0-3 python -m kokoro_runner \
  --model kokoro-82m.onnx \
  --text "O módulo agêntico concluiu a extração dos dados do documento." \
  --threads 4
```

Essa abordagem garante que a geração de voz mantenha um $\text{RTF} \le 0,25$ em CPUs modernas de 8 núcleos, sem interferir na fila de inferência paralela gerenciada pelo motor Rust.

## 4. Síntese de Alocação FinOps e Arquitetura do Sistema

Para operar um sistema agêntico com múltiplos módulos auxiliares dentro do limite estrito de 6GB de VRAM, a arquitetura do sistema deve adotar uma política rígida de gerenciamento de recursos. A memória de vídeo deve ser prioritariamente reservada para o modelo de linguagem principal e sua respectiva cache KV, enquanto os demais módulos são gerenciados sob demanda ou alocados na memória do sistema.

|**Componente**|**Módulo / Arquitetura**|**Subsistema de Alocação**|**Custo Estimado de Memória**|**Política de Ciclo de Vida**|
|---|---|---|---|---|
|**LLM Principal**|Qwen 2.5 7B Instruct (Q4_K_M)|VRAM (NVIDIA GPU)|~3800 MB|Permanente na VRAM|
|**Cache KV do LLM**|Contexo de 4096 Tokens (FP16 / Q8_0)|VRAM (NVIDIA GPU)|~1100 MB|Permanente (Alocação dinâmica)|
|**Especulação MTP**|Decodificação N-Gram (`ngram-mod`)|RAM (System Host)|~16 MB|Permanente na RAM|
|**Visão (.mmproj)**|Projetor VLM (ex: MiniCPM-V / LLaVA)|VRAM (NVIDIA GPU)|~750 MB|**Sob Demanda**: Carregado e descarregado dinamicamente|
|**Adaptadores LoRA**|Adaptadores de Função / Tool-Calling|VRAM (NVIDIA GPU)|~50 MB|Troca rápida (_Hot-swapping_) por requisição|
|**Embeddings**|BGE-Small-EN v1.5|RAM (System Host)|~130 MB|Permanente na RAM (Execução via CPU)|
|**Síntese de Voz**|Kokoro-82M (TTS)|RAM (System Host)|~200 MB|Execução isolada na CPU via `taskset`|

### Diretrizes de Engenharia FinOps

A gestão de recursos em ambientes de borda restritos deve seguir três diretrizes fundamentais:

Em primeiro lugar, o carregamento dos arquivos de projeção visual (`.mmproj`) deve ser puramente reativo. Como o agente utiliza parsers nativos de DOM/AST para navegar em interfaces digitais, o módulo de visão só deve ser alocado na VRAM quando o sistema detectar uma falha na interpretação estruturada e exigir o processamento de um documento físico ou gráfico. Após a execução da tarefa e a emissão do JSON correspondente, o projetor e sua cache KV associada devem ser desalocados da VRAM.

Em segundo lugar, a especulação de _tokens_ via redes neurais secundárias deve ser substituída por abordagens baseadas em n-gramas (`ngram-mod`). Essa substituição preserva cerca de 800MB a 1.5GB de VRAM que seriam consumidos por um modelo de rascunho, eliminando o risco de regressão na velocidade de geração em GPUs com largura de banda de memória limitada.

Por fim, os serviços de suporte que não exigem aceleração por GPU — especificamente os encoders de embeddings e os motores de áudio — devem ser mantidos estritamente no subsistema de memória RAM e processados pela CPU. Essa segregação garante que a GPU opere dedicada às tarefas de raciocínio do modelo de linguagem principal, otimizando o uso do hardware sem extrapolar os limites físicos da placa gráfica.

---

## Opções além do Qwen 2.5 (outdated)

A família **Qwen 3.5** está disponível e representa o estado da arte para execução de LLMs locais.

Para a restrição severa de **6GB de VRAM (RTX 2060m)**, você tem duas escolhas principais da família Qwen 3.5 que se aplicam ao seu cenário:

### 1. Qwen 3.5 4B (_A Escolha de Alta Eficiência FinOps_)

- **Consumo de VRAM (Q4_K_M):** ~2,5 GB.
- **Desempenho vs Qwen 2.5 7B:** Os modelos de 4B da geração 3.5 igualam e em diversos benchmarks superam o antigo Qwen 2.5 de 7B em raciocínio lógico, suporte multimodal/ferramentas e tarefas de codificação agêntica.
- **Vantagem para o SODA:** Por consumir apenas 2,5 GB dos 6 GB disponíveis, ele deixa **mais de 3,5 GB de VRAM totalmente livres**. Esse orçamento restante garante espaço de sobra para alocar a cache KV em janelas de contexto estendidas e permite carregar projetores de visão (`.mmproj`) de ~750MB sem estourar o limite físico da GPU.

### 2. Qwen 3.5 9B (_O Substituto Direto da Classe 7B/8B_)

- **Consumo de VRAM (Q4_K_M):** ~5,1 GB a 5,5 GB.
- **Desempenho:** É a referência atual na categoria de modelos intermediários compactos, apresentando alta capacidade em raciocínio complexo.
- **Gargalo no SODA:** Ele ocupa cerca de 85% a 90% de toda a VRAM da sua placa gráfica. Restam apenas cerca de 500 MB livres para a cache KV. Se o seu agente precisar processar um contexto mais longo ou carregar um arquivo `.mmproj` de visão, o driver da NVIDIA será forçado a paginar a memória para a RAM do sistema via barramento PCIe, reduzindo drasticamente a taxa de geração de _tokens_.

### Recomendação de Substituição

Para manter a estabilidade do sistema sem estouros de memória de vídeo, a melhor opção no ecossistema atual é migrar para o **`Qwen3.5-4B-Instruct`**. Ele entrega desempenho equivalente ou superior ao Qwen 2.5 7B anterior com metade da pegada de memória, preservando a margem necessária para operar seus módulos _sidecar_.