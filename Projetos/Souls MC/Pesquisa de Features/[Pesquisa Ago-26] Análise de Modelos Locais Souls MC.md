# Relatório Técnico de Avaliação Arquitetural: Integração dos Modelos GLiClass e Laguna e Racionalização do Stack Cognitivo para o Souls MC (SODA)

## Introdução e Contextualização do Ecossistema Operacional SODA

A concepção do _Sovereign Operating Data Architecture_ (SODA) no âmbito do ecossistema Souls MC responde à necessidade de executar agentes autônomos de alta densidade cognitiva em ambientes de borda com restrições severas de hardware. O ambiente de execução do sistema é delimitado por uma unidade de processamento gráfico NVIDIA GeForce RTX 2060 Mobile com 6 GB de VRAM GDDR6, suportada por um processador Intel Core i9 com aceleração vetorial AVX2, 32 GB de memória RAM de sistema e armazenamento NVMe.

Em arquiteturas agênticas locais, a alocação de modelos de inteligência artificial exige disciplina estrita contra a inflação de dependências. O carregamento simultâneo de múltiplos Modelos de Linguagem de Grande Escala (LLMs) ou classificadores redundantes resulta em degradação acelerada do desempenho devido ao transbordo (_spillover_) da VRAM para a memória RAM através do barramento PCIe. Esse fenômeno reduz a taxa de geração de tokens, aumenta a latência de comunicação inter-processos e induz gargalos térmicos.

Este dociê apresenta uma avaliação exaustiva dos modelos de linguagem e classificadores **GLiClass** (Knowledgator) e **Laguna** (Poolside). Mapeiam-se as suas divergências fundamentais, mecanismos de inferência e modos de integração no Souls MC. Adicionalmente, amplia-se o ecossistema com a análise de modelos contemporâneos e estabelece-se uma estratégia de canibalização de stack. O objetivo é garantir que cada modelo mantido no repositório execute uma função operacional única e insubstituível, eliminando qualquer sobreposição funcional.

## Análise Técnica Detalhada do Modelo GLiClass (Knowledgator)

### Origem, Arquitetura e Mecanismo de Inferência

O GLiClass é uma família de modelos não gerativos desenvolvida pela Knowledgator, concebida especificamente para classificação de sequências e rotulamento semântico _zero-shot_ e _few-shot_ de altíssima eficiência. Inspirado na arquitetura do GLiNER, o GLiClass abandona a abordagem autorregressiva tradicional dos LLMs em favor de uma topologia baseada em codificador (_encoder_) bidirecional. O modelo processa o texto de entrada e as descrições dos rótulos em uma única passagem direta (_single forward pass_), utilizando mecânicas de atenção cruzada (_cross-attention_) para projetar o texto e as categorias em um espaço vetorial compartilhado.

Diferente de um LLM gerativo, que necessita amostrar múltiplos tokens sequencialmente para formatar um resultado estruturado, o GLiClass calcula diretamente a probabilidade de correspondência entre o texto e a lista de rótulos. Essa característica confere ao modelo uma velocidade de processamento até 50 vezes superior à de _cross-encoders_ tradicionais e várias ordens de grandeza mais rápida que a de LLMs gerativos, mantendo uma precisão F1 equivalente ou superior nos benchmarks de classificação.

A evolução da linhagem GLiClass resultou na disponibilização de múltiplos pontos de checagem na plataforma Hugging Face, estruturados em três gerações principais:

- **Linhagem v1.0 / v2.0**: Fundamentada em codificadores compactos (`gliclass-small-v1.0` de 144M, `gliclass-base-v1.0` de 186M e `gliclass-large-v1.0` de 438M), otimizada para inferência rápida em CPUs e GPUs de baixa escala.
- **Linhagem v3.0 (Raciocínio e Lógica)**: Incorpora avanços no pré-treinamento com dados lógicos sintéticos (`knowledgator/gliclass-v3-logic-dataset`) e refinamento via Aprendizado por Reforço (RL). Esta geração divide-se em arquiteturas baseadas em DeBERTa-v3 (`gliclass-base-v3.0` de 187M e `gliclass-large-v3.0` de 439M) e arquiteturas baseadas em ModernBERT (`gliclass-modern-base-v3.0` de 151M e `gliclass-modern-large-v3.0` de 399M), além do micro-modelo `gliclass-edge-v3.0` de 32.7M de parâmetros.
- **Linhagem Multilang (Ultra / Mini)**: Modelos treinados nativamente em 20 idiomas (incluindo Português, Espanhol, Inglês, Alemão e Chinês), com capacidade _cross-lingual_, permitindo classificar textos em Português utilizando rótulos definidos em Inglês ou vice-versa.

### Desempenho Empírico e Recursos Avançados

Os dados de benchmark da série v3.0 revelam um ganho substancial de precisão à medida que a escala do modelo aumenta, conforme demonstrado na avaliação de F1-score médio e velocidade de inferência.

|**Modelo GLiClass (v3.0)**|**Tamanho em Disco**|**Contagem de Parâmetros**|**F1-Score Médio (Benchmark)**|**Velocidade de Inferência (Amostras/s)**|
|---|---|---|---|---|
|`gliclass-edge-v3.0`|131 MB|32,7M|0,4900|97,29|
|`gliclass-modern-base-v3.0`|606 MB|151M|0,5577|54,46|
|`gliclass-modern-large-v3.0`|1,60 GB|399M|0,6197|43,80|
|`gliclass-base-v3.0`|746 MB|187M|0,6764|51,61|
|`gliclass-large-v3.0`|1,75 GB|439M|0,7193|25,22|

A análise estatística dos resultados indica que os modelos baseados na espinha dorsal DeBERTa-v3 superam consistentemente as variantes baseadas em ModernBERT em acurácia absoluta. O `gliclass-large-v3.0` alcança um F1-score de 0,7193, superando a linha de base de _cross-encoders_ densos como o `deberta-v3-large-zeroshot` (0,6821) em +5,5% relativo.

Além da classificação simples, as iterações mais recentes do GLiClass oferecem suporte a rótulos hierárquicos mapeados por notação de ponto (ex.: `categoria.tecnica`), injeção de descrições em linguagem natural para cada classe, contexto _few-shot_ via exemplos diretos no prompt e re-ranqueamento vetorial em pipelines RAG (_reranking_).

### Papel Operacional do GLiClass no Souls MC

No ecossistema SODA, o GLiClass é implantado exclusivamente na **Camada de Borda e Triagem Epistêmica (Tier 0)**, executado no processador Intel Core i9 através do runtime de CPU `OrtScorerEngine` (ONNX Runtime compilado em C/Rust) ou via abstração de tensores com a biblioteca `Candle`. Por não necessitar da GPU dGPU, o modelo opera sem alocar VRAM, preservando os 6 GB da RTX 2060m para a inferência gerativa principal.

As responsabilidades do GLiClass no Souls MC compreendem:

1. **Roteamento de Intenção e Pre-Flight Check**: Intercepta a instrução do usuário enviada à interface reativa Svelte 5 / Tauri. Em uma latência inferior a 15 milissegundos, avalia a entrada e atribui pontuações de confiança a classes como `criação_de_código`, `pesquisa_memória`, `alteração_sistema` ou `comando_ambíguo`.
2. **Disjuntor de Incerteza e Ambiguidade**: Caso a pontuação da classe `comando_ambíguo` ultrapasse o limiar de 0,85, o SODA suspende a transmissão do prompt para os modelos gerativos pagos ou pesados. O sistema renderiza um componente socrático perguntando ao operador humano por esclarecimentos, evitando o desperdício de tokens e ciclos de inferência.
3. **Re-ranqueamento no RAG (Reranker)**: Durante a recuperação de contexto no banco relacional SQLite com `sqlite-vec` ou LanceDB, o GLiClass atua como o re-ranqueador de alta velocidade, pontuando a relevância entre a consulta e as passagens encontradas em uma única passagem.
4. **Guarda-Costas de Segurança e Conformidade**: Avalia o texto contra rótulos de risco de injeção de prompt (`prompt_injection`), vazamento de dados (`data_exfiltration`) e execução de comandos não permitidos (`unsafe_command`), bloqueando requisições maliciosas na borda do sistema.

## Análise Técnica Detalhada da Família Laguna (Poolside)

### Origem, Arquitetura e Mecanismo de Inferência

O Laguna é uma família de modelos de linguagem de grande escala baseados na arquitetura _Mixture-of-Experts_ (MoE) densa e esparsa, desenvolvida pela Poolside. Projetado especificamente para engenharia de software autônoma, codificação agêntica e execução de tarefas de longo horizonte em ambiente de terminal, o Laguna é um modelo causal e autorregressivo (decodificador).

A arquitetura do Laguna introduz modificações em relação aos transformadores SwiGLU MoE convencionais:

- **Atribuição de Cabeças por Camada (`num_attention_heads_per_layer`)**: Permite que diferentes camadas do decodificador possuam contagens distintas de cabeças de consulta (_query heads_), enquanto compartilham a mesma estrutura de cache Key-Value (KV), otimizando a pegada de memória durante a geração autorregressiva.
- **Roteador MoE com Função Sigmóide**: O mecanismo de seleção de especialistas utiliza a pontuação sigmóide com correção de viés aprendida (`e_score_correction_bias`) e balanceamento de carga sem perda auxiliar. Isso elimina o atrito de convergência e melhora a especialização dos especialistas.
- **Atenção Híbrida de Janela Deslizante (SWA)**: Intercala camadas de Atenção de Janela Deslizante (janela de 512 tokens) com camadas de Atenção Global na proporção de 3:1, reduzindo drasticamente o consumo de memória do KV Cache em janelas de contexto estendidas.
- **Raciocínio Intercalado e Suporte a Ferramenta**: Suporta a alternância dinâmica de raciocínio interno (_thinking mode_) entre chamadas de ferramentas (_tool calls_), permitindo ativar ou desativar o orçamento de pensamento por requisição.

A família Laguna distribui-se em três classes funcionais:

1. **Laguna XS**: Possui 33 bilhões de parâmetros totais e 3 bilhões de parâmetros ativos por token. Contém 40 camadas (30 com SWA e 10 com Atenção Global), suporte nativo a contexto de 262.144 tokens, cache KV em FP8 e licença Apache 2.0.
2. **Laguna S 2.1**: Trata-se do dimensionamento da linha XS, otimizado via Aprendizado por Reforço (RL) em precisão FP8. Registra 70,2% de acerto no benchmark _Terminal-Bench 2.1_, posicionando-se como o modelo mais capaz em sua classe de peso para automação de terminal de comandos.
3. **Laguna M**: Modelo MoE massivo de escala industrial com 225 bilhões de parâmetros totais e 23 bilhões de parâmetros ativos por token, estruturado em 70 camadas (3 camadas SwiGLU densas e 67 camadas esparsas MoE com 256 especialistas e roteamento _top-k=16_).

### Especificações Técnicas e Benchmarks da Família Laguna

Os dados consolidados de arquitetura e desempenho dos modelos Laguna refletem sua especialização em desenvolvimento de software e tarefas agênticas.

|**Métrica / Parâmetro**|**Laguna XS**|**Laguna S 2.1**|**Laguna M**|
|---|---|---|---|
|**Parâmetros Totais / Ativos**|33B / 3B por token|33B / 3B por token|225B / 23B por token|
|**Camadas e Atenção**|40 camadas (30 SWA + 10 Global)|40 camadas (30 SWA + 10 Global)|70 camadas (3 Densas + 67 MoE Global)|
|**Total de Especialistas**|256 especialistas (1 compartilhado)|256 especialistas (1 compartilhado)|256 especialistas (1 compartilhado)|
|**Janela de Contexto**|262.144 tokens|262.144 tokens|262.144 tokens|
|**SWE-bench Verified**|69,9%|Superior a 70%|Desempenho de nível de fronteira|
|**Terminal-Bench 2.0 / 2.1**|35,7% (v2.0)|70,2% (v2.1)|Competitivo com modelos de fronteira|
|**Licença de Software**|Apache 2.0|Apache 2.0|Apache 2.0|

### Papel Operacional do Laguna no Souls MC

O Laguna (especificamente as variantes **Laguna XS e S 2.1** em formato quantizado GGUF Q4_K_M ou FP8) é alocado no Souls MC como o **Operário Agêntico de Longo Horizonte (Tier 2/3)**. Devido aos seus 33B de parâmetros totais, o modelo em formato Q4 consome entre 18 GB e 20 GB de memória. Na GPU RTX 2060m de 6 GB, o Laguna **não pode rodar inteiramente alocado na VRAM**. Sua execução exige o particionamento de camadas, com cerca de 25% mantidos na dGPU e 75% descarregados na memória RAM de 32 GB do sistema através do barramento PCIe.

Em razão dessa limitação física, o Laguna **não é utilizado para o chat interativo síncrono**, pois a transferência de tensores via PCIe limita a velocidade a taxas entre 3 e 8 tokens por segundo. Em vez disso, seu papel no Souls MC é restrito às seguintes tarefas:

1. **Execução Assíncrona de Background ("Morning Briefing" / Batch Jobs)**: Durante períodos de inatividade humana ou tarefas agênticas em lote, o SODA aciona o Laguna em segundo plano. O modelo assume tarefas de refatoração profunda de código, correção de bugs em múltiplos arquivos, varredura de repositórios e navegação em terminal.
2. **Orquestração MCP de Alto Horizonte**: Quando uma instrução exige o encadeamento autônomo de dezenas de ferramentas MCP (ler diretórios, compilar via cargo, interpretar logs de erro, aplicar alterações via diff e retestar), o Laguna é acionado. Sua capacidade de alternar o modo de pensamento (_thinking mode_) garante a integridade do raciocínio em tarefas sequenciais complexas.

## Comparação Estrutural e Funcional: GLiClass vs. Laguna

As diferenças entre o GLiClass e o Laguna são de ordem arquitetural, funcional e de alocação de hardware. Enquanto o GLiClass é um codificador bidirecional minimalista projetado para transformar textos em métricas numéricas de classificação em milissegundos, o Laguna é um decodificador MoE massivo construído para raciocinar e gerar código.

|**Dimensão de Análise**|**GLiClass (Knowledgator Multilang / v3.0)**|**Laguna (Poolside XS / S 2.1)**|
|---|---|---|
|**Família e Topologia**|Codificador Bi-direcional (Encoder Não-generativo)|Decodador Causal Autorregressivo (MoE Sparse)|
|**Contagem de Parâmetros**|32,7M a 439M parâmetros|33B totais (3B ativos) a 225B (23B ativos)|
|**Formato do Output**|Vetor denso de probabilidades `[0.0 a 1.0]` por classe|Sequência de Tokens (Texto, Código, Chamadas MCP)|
|**Consumo de Memória**|130 MB a 1,75 GB (Reside 100% na RAM da CPU)|18 GB a 20 GB em Q4 (Exige dGPU + Transbordo em RAM)|
|**Tempo de Resposta (Latência)**|10 ms a 40 ms por inferência (Passagem Direta Única)|3 a 8 t/s em modo híbrido CPU/GPU; 35+ t/s em GPU pura|
|**Janela de Contexto**|512 a 8.192 tokens (Focado em passagens e frases)|262.144 tokens (Focado em repositórios inteiros)|
|**Hardware Primário no SODA**|CPU Intel Core i9 (AVX2 / ONNX Runtime / Candle)|Execution Híbrida (RTX 2060m + 32 GB RAM do sistema)|
|**Caso de Uso Central**|Roteamento de intenção, RAG reranking, segurança, filtro|Automação de terminal, codificação agêntica em lote|

## Expansão do Stack com Modelos Contemporâneos e Análise de Canibalização

A expansão do repositório de modelos do Souls MC deve obedecer ao **Princípio do Filtro de Eficiência e Canibalização**: _se um modelo demonstra um equilíbrio superior de tamanho, eficiência computacional e capacidade geral para um uso específico, ele canibaliza e elimina os modelos concorrentes de sua categoria, impedindo a coexistência de componentes redundantes_.

### Modelos Candidatos Analisados

A varredura do ecossistema open-source identificou cinco famílias de modelos de alta eficiência para avaliação:

- **Gemma 4 E2B / E4B (Google DeepMind)**: Modelos de linguagem pequenos de nova geração. O **Gemma 4 E2B** (~2B parâmetros) demonstra um avanço em relação às gerações Gemma 2/3, atingindo 70% de eficácia em avaliações de raciocínio causacional multi-etapas e 93,3% de precisão em filtros de segurança contra injeção de prompt.
- **Qwen 3.5 Coder 4B (Alibaba Cloud)**: Modelo denso focado em codificação e estruturação JSON. A quantização Q4_K_M ocupa apenas ~2,5 GB de VRAM, permitindo execução na dGPU RTX 2060m sem transbordo de memória e com suporte a predição multi-token (_Multi-Token Prediction_ - MTP).
- **Phi-4-mini (Microsoft - 3,8B)**: Modelo híbrido focado em matemática e lógica. Apresenta alta capacidade em testes sintéticos, mas demonstra propensão a _overfitting_, além de consumir mais de 3,8 GB de RAM quando executado na CPU, gerando maior latência de inicialização.
- **SmolLM2-360M / SmolLM-135M (Hugging Face)**: Micro-modelos treinados para alocação mínima. O SmolLM-135M atinge taxas de 15 a 26 tokens por segundo na CPU com consumo inferior a 200 MB de RAM, sendo ideal para orientar instaladores e rotinas de inicialização (_cold start_).
- **DeepSeek V4 Distill 7B/8B**: Modelos resultantes da destilação do cérebro DeepSeek V4 sobre bases densas, mantendo alta capacidade de interpretação de ferramentas e raciocínio lógico em formatos compactos.

### Análise e Processo de Canibalização no Stack SODA

Ao aplicar a matriz de requisitos do Souls MC aos modelos candidatos, determinam-se as seguintes substituições e eliminação de componentes redundantes:

#### Canibalização na Categoria A: Classificação de Sequências na CPU

- **Candidatos Avaliados**: GLiClass v1.0, GLiClass Edge, ModernGLiClass, DistilBERT, RoBERTa, DeBERTa-v3 Cross-Encoders.
- **Modelo Vencedor**: **`knowledgator/gliclass-multilang-ultra`**.
- **Racional de Canibalização**: O `gliclass-multilang-ultra` (variante 0.3B / 2B) absorve e elimina todas as outras versões do GLiClass e classificadores BERT legados. O modelo oferece suporte nativo a Português e mais 19 idiomas, executa re-ranqueamento no RAG, suporte a rótulos hierárquicos e avaliação _few-shot_ em uma única passagem de CPU com consumo de ~1,5 GB de RAM. Não há justificativa para manter o `gliclass-edge-v3.0` ou versões em Inglês, pois o modelo Multilang Ultra cobre a totalidade do domínio funcional com precisão superior.

#### Canibalização na Categoria B: Avaliação Epistêmica e Metacognição na CPU

- **Candidatos Avaliados**: Phi-4-mini (3,8B), Gemma 2B, TinyLLaMA (1,1B), Qwen 3.5 0.8B.
- **Modelo Vencedor**: **`Gemma 4 E2B` (Quantizado Q8_0 na CPU via llama-cpp-2 / Candle)**.
- **Racional de Canibalização**: O Phi-4-mini (3,8B) é eliminado devido ao _overfitting_ sintético em seu treinamento, que compromete a generalização em perguntas abertas, além de ocupar quase 4 GB de RAM. O `Gemma 4 E2B` (~2B) entrega desempenho em raciocínio causal (70% de eficácia) e segurança (93,3%), consumindo apenas ~1,5 GB de RAM. Ele canibaliza o Phi-4-mini e o Gemma 2B para a função de avaliação de incerteza (_Logit Probing_) no Hipocampo local do SODA.

#### Canibalização na Categoria C: Operário Gerativo Síncrono na dGPU

- **Candidatos Avaliados**: Laguna XS (33B MoE), Qwen 2.5 7B, Llama 3.2 3B, Gemma 4 12B, Mistral 7B.
- **Modelo Vencedor**: **`Qwen 3.5 Coder 4B` (Quantizado Q4_K_M / GGUF)**.
- **Racional de Canibalização**: O `Qwen 3.5 Coder 4B` em formato Q4_K_M consome apenas **~2,5 GB de VRAM**. Isso permite alocar **100% dos pesos do modelo dentro dos 6 GB da dGPU RTX 2060m**, sem necessidade de transbordo para a RAM. A margem restante de **3,5 GB de VRAM** é reservada para o KV Cache de contextos longos, garantindo uma velocidade estável de 45 tokens por segundo. Modelos como o Llama 3.2 3B e o Qwen 2.5 7B são canibalizados e removidos, pois o Qwen 3.5 Coder 4B oferece desempenho superior em código e JSON estruturado com menor pegada de memória.

#### Canibalização na Categoria D: Agente de Terminal Assíncrono de Longo Horizonte

- **Candidatos Avaliados**: Laguna XS (33B / 3B ativo), Qwen 3.6 35B-A3B, DeepSeek V4 Distill 8B.
- **Modelo Vencedor**: **`Laguna XS` (e Laguna S 2.1 via API / Batch Híbrido)**.
- **Racional de Retenção**: O Laguna XS é mantido exclusivamente para tarefas agênticas assíncronas em lote (_background jobs_). Devido ao seu benchmark de 70,2% em automação de terminal (_Terminal-Bench 2.1_) e suporte a 256k tokens de contexto com raciocínio intercalado, ele supera modelos concorrentes em tarefas de refatoração de repositórios complexos. Por ser executado de forma assíncrona, a latência do transbordo PCIe é tolerada pelo sistema.

## Arquitetura de Alocação de Hardware e Topologia do Ecossistema Souls MC

### Fluxo Integrado de Processamento e Roteamento de Dados

A topologia do SODA organiza o fluxo de dados em estágios sequenciais de processamento, garantindo que as requisições passem pelos modelos de menor custo computacional antes de acionar instâncias mais complexas.

A jornada de uma instrução no Souls MC obedece à seguinte sequência operacional:

1. **Recepção na Interface e Despacho de Borda**: A instrução emitida pelo usuário na interface reativa (Svelte 5 / Tauri) é recebida pelo `SODA Gateway` desenvolvido em Rust.
2. **Triagem Semântica no `OrtScorerEngine` (CPU)**: O Gateway transmite a string para o `GLiClass Multilang Ultra`, rodando via ONNX Runtime na CPU. O modelo classifica a intenção, avalia a necessidade de busca no RAG e verifica indicadores de ambiguidade ou risco de segurança em menos de 15 ms.
3. **Avaliação Metacognitiva no `LlamaCpp4LogitEngine` (CPU)**: Se houver divergência entre o comando e a memória episódica persistida no SQLite/LadybugDB, o `Gemma 4 E2B` executa uma amostragem de probabilidade (_Logit Probing_) na CPU sem gerar texto livre. Se o conflito for elevado, o Disjuntor de Incerteza é acionado.
4. **Execução Síncrona no `LlamaCppEngine` (dGPU RTX 2060m)**: Para respostas de chat em tempo real, geração de código ou invocações de ferramentas diretas, a requisição é direcionada ao `Qwen 3.5 Coder 4B`. O modelo roda inteiramente na VRAM (2,5 GB ocupados), mantendo a latência baixa e gerando respostas a 45 tokens por segundo.
5. **Garantia de Estrutura via `llguidance` (CPU)**: Para chamadas de função ou respostas JSON, o backend Rust aplica a crate `llguidance` em nível de instrução AVX2 na CPU. O `llguidance` mascara os _logits_ gerados pelo Qwen 3.5 Coder 4B em tempo real, forçando o modelo a obedecer rigorosamente à gramática JSON especificada, evitando alucinações sintáticas.
6. **Enfileiramento Assíncrono em Background**: Caso o GLiClass identifique uma tarefa de alta complexidade (refatoração de repositório completo ou automação de terminal), a requisição é enfileirada no gerenciador de tarefas assíncronas do SODA. O `Laguna XS` é instanciado em modo híbrido (GPU/RAM), executando a tarefa em segundo plano sem travar a interface do usuário.

### Matriz Unificada do Stack de Modelos do Souls MC (Zero Redundância)

A consolidação final do repositório de modelos do Souls MC elimina redundâncias e estabelece uma arquitetura em quatro camadas:

|**Tier Arquitetural**|**Modelo Selecionado**|**Formato / Quantização**|**Alocação de Hardware**|**Papel Operacional Exclusivo**|**Modelos Canibalizados e Removidos**|
|---|---|---|---|---|---|
|**Tier 0 (Bootstrap)**|**SmolLM-135M**|ONNX / FP16 Native|CPU (Intel i9 / AVX2)|Micro-guia de boot, diálogos de instalação e verificação de integridade do SO.|Llama-3.2-1B, Qwen-0.5B (Removidos devido ao peso excessivo no instalador).|
|**Tier 0 (Classificação)**|**GLiClass Multilang Ultra**|ONNX / Safetensors|CPU (RAM ~1,5 GB)|Roteamento de intenção, RAG reranking, disjuntor de ambiguidade, filtro de segurança.|GLiClass Edge/Base/v1/v2, ModernGLiClass, DeBERTa/RoBERTa Cross-Encoders.|
|**Tier 0.5 (Epistêmico)**|**Gemma 4 E2B**|GGUF / Q8_0|CPU (Logit Probing via Candle)|Avaliação metacognitiva de conflitos na memória episódica, métrica de dúvida.|Phi-4-mini (3,8B), Gemma 2B, TinyLLaMA (Removidos por overfitting ou consumo de RAM).|
|**Tier 1 (Síncrono GPU)**|**Qwen 3.5 Coder 4B**|GGUF / Q4_K_M|dGPU (RTX 2060m - 100% VRAM ~2,5 GB)|Autocomplete de código, chat em tempo real, chamadas de ferramentas síncronas.|Llama 3.2 3B, Qwen 2.5 7B, Gemma 4 12B, Mistral 7B.|
|**Tier 2 (Assíncrono)**|**Laguna XS (33B / 3B ativo)**|GGUF / Q4_K_M / FP8|GPU (1,5 GB) + RAM (16 GB Offload)|Automação de terminal de longo horizonte, refatoração em lote de código, MCP complexo.|Qwen 3.6 35B-A3B (Removido devido ao menor score em automação de terminal).|
|**Tier 3 (Nuvem)**|**Claude Opus 4.7 / DeepSeek V4 Pro**|API Externa (Gateway SODA)|Nuvem (OpenRouter / APIs Diretas)|Planejamento arquitetural de alto nível, geração de grafos DAG de execução, arbitragem.|GPT-4o, Gemini 1.5 Pro (Canibalizados por menor custo-benefício em agentic coding).|

## Conclusões e Diretrizes Táticas de Implementação

1. **Diferenciação Clara entre GLiClass e Laguna**: O GLiClass é um codificador bidirecional de passagem única otimizado para classificação e pontuação de sequências na CPU, enquanto o Laguna é um decodificador gerativo MoE focado em raciocínio longo e codificação agêntica em terminal. Eles desempenham papéis complementares e não-concorrentes na arquitetura.
2. **Adoção do GLiClass Multilang Ultra como Classificador Unificado**: Padronizar a camada de triagem do Souls MC no modelo `knowledgator/gliclass-multilang-ultra` executado via ONNX Runtime no backend em Rust (`OrtScorerEngine`). O modelo assume as funções de roteamento de intenção, re-ranqueamento vetorial e auditoria de segurança em Português e outros 19 idiomas.
3. **Consolidação do Qwen 3.5 Coder 4B para Inferência Gerativa Síncrona**: Adotar o `Qwen 3.5 Coder 4B` (Q4_K_M) como o único operário gerativo mantido continuamente na VRAM da placa RTX 2060m. Sua pegada de 2,5 GB garante a preservação de 3,5 GB de VRAM para o KV Cache de contexto longo e elimina o engarrafamento do barramento PCIe durante sessões interativas.
4. **Alocação Restrita do Laguna XS para Trabalhos Agênticos em Lote**: Restringir a implantação do Laguna XS / S 2.1 a processos assíncronos de segundo plano. O modelo deve ser instanciado via enfileiramento de tarefas para refatorações de código e automações de terminal de longo horizonte.
5. **Eliminação de Modelos Redundantes**: Aplicar o plano de canibalização para remover do repositório os modelos Phi-4-mini, Llama 3.2 3B, Qwen 2.5 7B e classificadores BERT/DeBERTa legados, mantendo o stack do Souls MC enxuto, determinístico e adaptado aos limites do hardware.

---

# Parte 2

Esta é a **Parte 2** do nosso estudo arquitetural para o **Souls MC (SODA)**. Aqui detalhamos o papel do agente Maestro, as otimizações de motor para contornar o gargalo do Laguna na RAM, o setup exato do GLiClass, a matriz completa de runtimes por Tier, o roteamento dinâmico via ParetoBandit para modelos asiáticos na nuvem e a inclusão das CLIs Agênticas.

### 1. SmolLM-135M vs. SmolLM2-360M e a Definição do Agente "Maestro" (Tier 1.5)

#### O destino do SmolLM-135M e SmolLM2-360M

O **SmolLM-135M** não é um modelo de conversação contínua. Ele foi projetado para a fase de **Bootstrap (Tier 0)**. O seu papel é subir na CPU em menos de 100 ms, guiar os diálogos de inicialização/instalação do sistema e verificar a integridade da máquina. Logo após o boot, o backend Rust aplica a **Guilhotina Atômica (ADR-027)**, desalocando o SmolLM da RAM para liberar espaço.

O **SmolLM2-360M** é um micro-modelo útil para _micro-guardrails_ de entrada ou sanitização de texto, mas **nenhum dos dois serve para o Chat Live (Maestro)**. Eles não possuem capacidade cognitiva para sustentar diálogos de múltiplos turnos, interpretar contextos complexos ou gerenciar ferramentas sem alucinar.

#### Quem é o Agente "Maestro / Governador" (Live Chat 1x1)?

O Maestro é o agente principal que conversa diretamente com o humano no chat 1x1 em tempo real. Ele recebe a intenção, consulta a memória, despacha pesquisas no RAG e decide se a tarefa deve ser resolvida imediatamente ou enviada para sub-agentes e rotinas em background.

O modelo base ideal para o Maestro no hardware local (sem depender 100% da nuvem) é o **`Qwen 3.5 Coder 4B`** (ou a variante **`Qwen 3.5 4B / 7B`**) quantizado em **GGUF Q4_K_M**:

- **Por que ele é o Maestro ideal?** Em Q4_K_M, o modelo ocupa apenas **~2,5 GB de VRAM** na RTX 2060m. Isso garante que 100% dos seus pesos fiquem alocados na GPU, gerando respostas síncronas a mais de 45 tokens por segundo.
- **Gestão de Contexto:** Os 3,5 GB restantes da VRAM ficam totalmente dedicados ao KV Cache, permitindo manter o histórico de conversa e o prompt de governança do Maestro.
- **Orquestração de Ferramentas:** Ele possui excelente capacidade nativa para chamadas de função (JSON/Tools). No SODA, o backend Rust aplica a crate `llguidance` via AVX2 na CPU para mascarar a gramática do output do Qwen em tempo real, garantindo que as chamadas para sub-agentes e pesquisas venham em JSON perfeito.

### 2. Laguna XS: Como Otimizar a Velocidade na RAM e Opções Concorrentes

Rodar um modelo MoE de 33B como o Laguna XS atravessando o barramento PCIe para a RAM do sistema introduz uma latência inevitável em relação à GPU pura. No entanto, para evitar que o tempo de resposta fique excessivamente lento durante a execução assíncrona, existem otimizações de motor no `llama.cpp` e alternativas no mercado:

#### Otimizações Práticas para Acelerar o Offload de RAM

1. **Ajuste do Micro-Batch Size (`-ub 2048`):** O `llama.cpp` por padrão utiliza `-ub 512`. Elevar o micro-batch para 2048 ou 4096 acelera o processamento inicial de prompts (_prefill_) em até 5x na CPU/RAM, mantendo os núcleos de processamento saturados.
2. **Offload Exclusivo dos Especialistas Roteados (`--n-cpu-moe` ou `-ot "exps=CPU"`):** Mantém as camadas de Atenção, o FFN Denso e o Especialista Compartilhado na VRAM da GPU (`-ngl 99`), enviando **apenas** os 256 especialistas esparsos para a RAM da CPU.
3. **Multi-Token Prediction (MTP):** Habilitar a predição de múltiplos tokens por passada (`--spec-draft-n-max 2`) gera um ganho de velocidade de 20% a 60% na geração autorregressiva de modelos MoE offloaded.
4. **Quantização do KV Cache (`--cache-type-k q4_0 --cache-type-v q4_0`):** Reduz o consumo do cache de contexto em até 75%, liberando banda de memória.

#### Concorrente de Peso do Laguna: `Qwen 3.6 35B-A3B`

Se o objetivo for substituir ou complementar o Laguna com um modelo mais moderno da mesma classe, a melhor opção atual é o **`Qwen 3.6 35B-A3B`**. Ele é uma arquitetura MoE de 35B de parâmetros com apenas 3B ativos por token e suporte nativo a MTP, entregando um desempenho equivalente ou superior em código e automação de terminal com respostas mais ágeis sob offloading de RAM.

### 3. Setup de Download e Execução do GLiClass Multilang Ultra

#### O que baixar no Hugging Face?

No repositório oficial `knowledgator/gliclass-multilang-ultra`, para rodar no motor nativo de alta performance sem depender do runtime completo do PyTorch/Python, você deve baixar:

- `model.safetensors` (ou os arquivos exportados em formato ONNX `model.onnx`).
- `config.json`
- `tokenizer.json` e `special_tokens_map.json`

#### Motor e Setup de Execução Otimizado

- **Motor Primário:** **`OrtScorerEngine`** (Runtime ONNX compilado em C++/Rust FFI com suporte a instruções vetoriais AVX2 da CPU).
- **Vantagem do Setup:** O GLiClass realiza uma passagem direta única (_single forward pass_). Ele projeta os rótulos e o texto em uma matriz compartilhada via _Cross-Attention_.
- **Extração Máxima de Desempenho:**
    - **Rótulos Hierárquicos:** Suporta notação por ponto (ex: `intenção.codigo` ou `risco.prompt_injection`) em uma única chamada.
    - **Contexto Few-Shot Nativo:** Pode-se incluir até 2 exemplos de classificação diretamente nas descrições dos rótulos para aumentar a acurácia sem comprometer a velocidade da CPU.

### 4. Matriz Unificada de Motores (Runtimes) por Tier do Souls MC

Para garantir a máxima extração do hardware com zero vazamento de memória, cada camada do SODA utiliza um motor específico:

|**Tier do Sistema**|**Modelo Alocado**|**Motor de Inferência (Runtime)**|**Estratégia de Hardware / Otimização**|
|---|---|---|---|
|**Tier 0 (Bootstrap)**|SmolLM-135M|`PulpLeleEngine` / `Candle`|CPU (AVX2). Execução estática zero-allocation na pilha, desalocado pós-boot.|
|**Tier 0 (Classificação)**|GLiClass Multilang Ultra|`OrtScorerEngine`|CPU (AVX2 via ONNX Runtime). Passagem direta em <15 ms, sem uso de VRAM.|
|**Tier 0.5 (Epistêmico)**|Gemma 4 E2B|`LlamaCpp4LogitEngine`|CPU (AVX2 via llama-cpp-2 FFI). Executa apenas _Logit Probing_ de probabilidade.|
|**Tier 1 / 1.5 (Maestro Chat Live)**|Qwen 3.5 Coder 4B (Q4_K_M)|`LlamaCppEngine`|dGPU RTX 2060m (100% VRAM ~2,5 GB) + CUDA + FlashAttention + `llguidance` em CPU.|
|**Tier 2 (Assíncrono Background)**|Laguna XS / Qwen 3.6 35B-A3B|`LlamaVanguardEngine`|Híbrido (dGPU + RAM). Instanciado com `--n-cpu-moe`, `-ub 2048` e MTP ativado.|

### 5. Tier 3 (Nuvem), ParetoBandit e Foco nos Modelos Asiáticos

As chamadas de nuvem no SODA não utilizam exemplos estáticos. Elas são gerenciadas pelo algoritmo **ParetoBandit**, um roteador dinâmico que calcula em tempo real o equilíbrio de Pareto entre custo por tarefa, latência e acurácia.

A arquitetura prioriza os modelos asiáticos de ponta devido à combinação de alta capacidade agêntica e custo reduzido:

1. **GLM-5.1 / GLM-5.2 (Zhipu AI):** Utilizado para planejamento arquitetural complexo e refatorações de longo horizonte com janela de contexto estendida.
2. **Kimi K2.5 / K2.6 (Moonshot AI):** Escolha primária para ingestão e resumo de grandes volumes de documentos Markdown/PDFs e análise de repositórios.
3. **Qwen 3.6 Plus / Max (Alibaba Cloud):** Orquestração avançada de ferramentas MCP e raciocínio de código em nível de fronteira.
4. **DeepSeek V4 Pro / Flash (DeepSeek):** Atua como o oráculo de custos mínimos para tarefas intensivas de raciocínio lógico e síntese.

### 6. O Braço Operacional das CLIs Agênticas (CLIs Atômicas)

Para evitar chamadas desnecessárias de APIs e permitir que o usuário aproveite planos de taxa fixa ou ferramentas locais, o SODA integra o braço das **CLIs Agênticas (Sidecars Atômicos)**:

- **Conceito:** Ferramentas utilitárias em linha de comando (como `claude-code`, `gemini-cli` ou binários CLI nativos em Rust) são registradas no `AgentGateway` do SODA como operários de tarefas atômicas.
- **Funcionamento:** Quando o Maestro (Tier 1.5) identifica uma tarefa delimitada e autônoma (ex: extração de AST via `tree-sitter`, geração de _diffs_ de código ou formatação de arquivo único), ele despacha um payload isolado para a CLI agêntica correspondente.
- **Vantagem:** A CLI executa a tarefa em sandbox, devolve o resultado via buffer e é encerrada. Isso economiza custos de API, reduz o acúmulo de contexto no chat principal e oferece uma forma simples de acoplamento ao ecossistema Souls MC.

---

## Refinamento Parte 2

O ecossistema de modelos chineses e asiáticos avançou em um ritmo avassalador. Modelos como **Kimi K3 / K2.7 Code**, **GLM-5.2**, **DeepSeek V4 Pro**, **MiniMax M3**, **Qwen 3.7 / 3.6 Max**, **Step 3.5 Flash** e **Doubao Seed 2.0 Pro** reescreveram as métricas de _agentic coding_ e o custo por tarefa no mercado internacional.

Para refinar a arquitetura do **Souls MC (SODA)** e cobrir com precisão cirúrgica o que baixar, como configurar os motores de execução e como integrar o braço de CLIs Agênticas, estruturei o plano de pesquisa para a **"Parte 2"** do relatório técnico.

### Plano de Pesquisa e Estruturação: Relatório Técnico (Parte 2)

#### 1. Mapeamento de Artefatos e Arquivos Exatos por Modelo ("O Que Baixar")

- **GLiClass Multilang Ultra**: Identificar os arquivos necessários no repositório Hugging Face (`model.safetensors`, `config.json`, `tokenizer.json`, exportações ONNX `model.onnx` e matrizes de atenção).
- **Demais Tiers**: Listar os binários `.gguf` exatos, quantizações indicadas (ex.: Q4_K_M, IQ3_M, Q8_0) e projetores necessários para o Gemma 4 E2B, Qwen 3.5 Coder 4B, Laguna XS e SmolLM-135M.

#### 2. Motores de Execução (Runtimes) e Setup Detalhado por Tier

- **Tier 0 (Bootstrap / Cold-Start)**: Detalhar a execução do SmolLM-135M via `PulpLeleEngine` / `Candle` na CPU com alocação estática na pilha e sua eliminação pós-boot pela Guilhotina Atômica (ADR-027).
- **Tier 0 (Classificação / Triagem)**: Configurar o `OrtScorerEngine` (ONNX Runtime em C++/Rust com instruções vetoriais AVX2) para o GLiClass Multilang Ultra, aproveitando _Cross-Attention_ em passagem única.
- **Tier 0.5 (Avaliador Epistêmico / Hipocampo)**: Definir a execução do Gemma 4 E2B via `LlamaCpp4LogitEngine` na CPU para _Logit Probing_ de probabilidades lógicas sem geração de texto livre.
- **Tier 1 / 1.5 (Agente Maestro / Chat Live 1x1)**: Definir o modelo gerador síncrono que atua como o "Governador" do chat interativo (ex.: Qwen 3.5 Coder 4B em Q4_K_M). Detalhar a alocação de 100% dos pesos na dGPU RTX 2060m (~2,5 GB VRAM), o uso de 3,5 GB restantes para KV Cache e o acoplamento da crate `llguidance` em AVX2 na CPU para mascaração de gramática JSON em tempo real.
- **Tier 2 (Agente Assíncrono / Background)**: Otimizações para o Laguna XS e concorrentes MoE em regime de offloading de RAM.

#### 3. Otimização de Offload PCIe/RAM e Modelos Concorrentes de Pesos Abertos

- **Aceleração de Offload**: Detalhar os parâmetros exatos de inicialização no `llama.cpp` / `mistral-rs` para mitigar o gargalo do barramento PCIe ao utilizar a RAM do sistema:
    - Saturação de prefill com micro-batches elevados (`-ub 2048` ou `-ub 4096`).
    - Offloading exclusivo de especialistas esparsos (`--n-cpu-moe` ou `-ot "exps=CPU"`) mantendo atenção e FFNs densos na GPU.
    - Predição Multi-Token (`--spec-draft-n-max 2` via MTP) para aumentar tokens por segundo.
    - Quantização do KV Cache (`--cache-type-k q4_0 --cache-type-v q4_0`).
- **Análise de Concorrentes MoE/Esparsos**: Avaliar alternativas contemporâneas de peso aberto que disputam a posição do Laguna XS para tarefas assíncronas de terminal e código, como o **Qwen 3.6 35B-A3B** e o **MiniMax M3**.

#### 4. Atualização de Fronteira do Tier 3 (Nuvem) & Algoritmo ParetoBandit

- **Ecossistema Asiático de Fronteira**: Mapear os modelos de ponta para o roteamento do Gateway SODA:
    - **Kimi K3 / K2.7 Code (Moonshot AI)**: Para tarefas pesadas de _agentic coding_ e janelas de contexto extensas.
    - **GLM-5.2 / GLM-5.1 (Zhipu AI)**: Para planejamento de engenharia e orquestração autônoma de sistemas.
    - **DeepSeek V4 Pro / Flash**: Para raciocínio matemático, síntese e código com custo de entrada mínimo.
    - **MiniMax M3 / M2.7**: Para ingestão multimodal e processamento de contextos em lote.
    - **Qwen 3.7 / 3.6 Max (Alibaba Cloud)**: Para orquestração avançada via ferramentas MCP.
    - **Step 3.5 Flash e Doubao Seed 2.0 Pro (ByteDance)**: Como alternativas de altíssima velocidade e baixo custo para tarefas intermediárias.
- **Roteamento por Custo por Tarefa**: Como o `ParetoBandit` utiliza o fracionamento tarifário (prompt, completion, cache-hit) e os limites de latência para escolher dinamicamente a melhor opção asiática em tempo real.

#### 5. O Braço de CLIs Agênticas (Sidecars Atômicos)

- **Arquitetura de Posição e Acoplamento**: Mapear como ferramentas de linha de comando (`claude-code`, `gemini-cli` e binários CLI customizados em Rust) são registradas no `AgentGateway`.
- **Execução de Tarefas Atômicas**: Definir o fluxo onde o Maestro (Tier 1.5) envia payloads isolados para CLIs locais executarem em sandbox (ex.: extração de AST via `tree-sitter`, geração de _diffs_ de código ou automação de git), economizando requisições de API e preservando o contexto do chat.

#### 6. Matriz Tática Consolidada de Hardware e Motores

- Criar a tabela definitiva de configuração do Souls MC contendo: Tier Arquitetural, Modelo, Formato/Quantização, Motor de Execução (Runtime), Parâmetros de Launch (Flags), Alocação de Memória (VRAM vs. RAM) e Caso de Uso Exclusivo.

---

## Respostas Práticas Sobre uso do Modelo Laguna XS 2.1

Vamos às respostas práticas e diretas para as suas dúvidas sobre o que baixar, as diferenças entre as versões do **Laguna XS 2.1** e como extrair a velocidade máxima no seu hardware (RTX 2060m 6 GB VRAM + 32 GB RAM).

### 1. Diferença entre `Myric (APEX)` vs. `lmstudio-community`

- **`lmstudio-community/Laguna-XS-2.1-GGUF` (Quantização Convencional)**:
    - **O que é**: Usa os métodos tradicionais de quantização do `llama.cpp` (como `Q4_K_M`, `Q5_K_M`).
    - **Como funciona**: Aplica o mesmo nível de compressão de forma homogênea por quase todo o modelo.
    - **Vantagem**: Compatibilidade 100% garantida com qualquer versão do `llama.cpp`, `llama-server` ou `ollama`.
- **`Myric/Laguna-XS-2.1-APEX-GGUF` (Quantização Inteligente para MoE)**:
    - **O que é**: **APEX** significa _Adaptive Precision Expert Quantization_. É uma técnica desenhada especificamente para arquiteturas _Mixture-of-Experts_ (MoE).
    - **Como funciona**: Em vez de esmagar o modelo inteiro com o mesmo peso, o APEX divide os tensores por relevância:
        1. Mantenha o **Especialista Compartilhado** (_Shared Expert_) e as **Camadas de Atenção/Borda** em altíssima precisão (`Q6_K` ou `Q8_0`), pois eles são ativados em _todas_ as passadas de tokens.
        2. Aplica compressão agressiva (`IQ4_XS`, `Q4_K` ou `Q3_K`) apenas nos **Especialistas Esparsos do Meio**, que só são ativados esporadicamente.
    - **Vantagem**: Entrega a qualidade semântica e lógica de uma quantização pesada (como `Q5`/`Q6`), mas com o tamanho e consumo de memória de uma `Q4`.

#### Qual vai funcionar melhor para você?

- **Para a melhor qualidade semântica pelo mesmo tamanho de RAM**: Baixe a versão **`Myric (APEX-i-compact)`**. Ela preserva as partes críticas do Laguna no pico de precisão sem estourar sua memória RAM.
- **Para testes rápidos de compatibilidade**: Se quiser o caminho sem riscos de bugs de parser de tensor, a opção **`Q4_K_M`** do `lmstudio-community` é a mais padrão do ecossistema.

### 2. O que é o `RespectMathias/Laguna-XS-2.1-DSpark-GGUF` (DFlash / Draft Model)?

**SIM, você deve baixar isso (ou o arquivo de Rascunho/Draft correspondente) e ele trará ganhos astronômicos de velocidade!**

#### O que é esse arquivo?

Ele **NÃO é o modelo principal**. Ele é um **Modelo Rascunho (_Draft Model_)** baseado na arquitetura **DFlash / DSpark**. É um micro-modelo levíssimo (geralmente entre 600 MB e 1.1 GB em `Q4_K_M` ou `Q8_0`).

#### Por que isso é revolucionário para a sua RAM e para o barramento PCIe?

Como o Laguna XS 2.1 principal (33B) não cabe inteiro na sua VRAM de 6 GB, você é obrigado a rodar grande parte dos seus pesos transbordados na RAM do sistema. A transferência de tensores pela PCIe é o seu grande gargalo de velocidade.

A tecnologia **Decodificação Especulativa (_Speculative Decoding_) com DFlash/DSpark** resolve isso da seguinte forma:

1. O micro-modelo Rascunho (`DSpark` / `DFlash` de ~600 MB) fica **100% alocado dentro da VRAM da sua RTX 2060m**.
2. A GPU gera super rápido de 2 a 4 tokens hipotéticos em rascunho a dezenas de tokens por segundo na VRAM.
3. O Laguna principal na RAM do sistema faz apenas **uma única passada de validação** (_forward pass_) para aprovar todos os tokens do rascunho de uma só vez.
4. **O Ganho**: Em vez de fazer 4 viagens lentas pela RAM/PCIe para gerar 4 tokens, o sistema faz apenas 1 viagem. Isso reduz o afunilamento de memória e **aumenta a taxa de geração em 30% a 60%**.

### 3. Existe "MTP" (Multi-Token Prediction) para o Laguna?

**Sim!** O suporte a predição e geração multi-token no Laguna é implementado exatamente através dos modelos **DFlash / DSpark** via Decodificação Especulativa nativa.

No motor `llama.cpp` (ou `llama-server`), você ativa o MTP / Especulativo fornecendo o modelo principal e acoplando o modelo rascunho com a flag `-md`:

```Bash
llama-server \
  -m Laguna-XS-2.1-APEX-compact.gguf \
  -md Laguna-XS-2.1-DSpark-Q4_K_M.gguf \
  -ngl 99 \
  --cpu-moe \
  -ub 2048 \
  --spec-draft-n-max 2
```

### Resumo do "Kit de Download" Ideal para o Laguna

Para o seu pipeline assíncrono do Souls MC, a combinação recomendada é:

1. **Modelo Principal (Cérebro MoE)**:
    - `Myric/Laguna-XS-2.1-APEX-GGUF` (arquivo da variante `APEX-i-compact`) **OU** `lmstudio-community/Laguna-XS-2.1-GGUF` (arquivo `Q4_K_M`).
2. **Modelo Rascunho (Acelerador de VRAM)**:
    - `RespectMathias/Laguna-XS-2.1-DSpark-GGUF` (ou o `DFlash-Q4_K_M.gguf` do repositório).
3. **Resultado no Hardware**:
    - O micro-modelo de rascunho (~600 MB) e as camadas de atenção/shared-expert residem na VRAM da RTX 2060m.
    - Os especialistas esparsos residem na RAM de 32 GB.
    - O MTP/DFlash cuida de adiantar a geração, contornando a lentidão da PCIe.