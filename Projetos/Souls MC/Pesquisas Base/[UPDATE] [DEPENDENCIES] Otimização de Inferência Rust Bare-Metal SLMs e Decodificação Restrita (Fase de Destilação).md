---
aliases:
  - "Otimização de Inferência Rust Bare-Metal: SLMs e Decodificação Restrita (Fase de Destilação)"
---

# Otimização de Inferência Local Bare-Metal em Rust utilizando SLMs para Extração Estruturada de JSON Rígido em Contextos de Larga Escala (30k) sob Restrições de VRAM (6GB)

A execução local e bare-metal de Modelos de Linguagem Pequenos (SLMs) estabeleceu-se como uma abordagem de eleição para o processamento descentralizado e focado na privacidade. Operar sob restrições extremas de memória — especificamente utilizando uma dGPU móvel NVIDIA RTX 2060m equipada com apenas 6 GB de VRAM — impõe desafios complexos de engenharia de sistemas. O principal obstáculo reside em acomodar simultaneamente os pesos quantizados do modelo, os buffers de ativação do motor de inferência e a volumosa estrutura do cache de Chaves e Valores (KV Cache) gerada por janelas de contexto longas de até 30k tokens.

A resolução desta equação requer um alinhamento rigoroso entre a seleção de arquiteturas sub-5B de alta capacidade, a aplicação de técnicas de quantização estática calibrada por matriz de importância (imatrix), o uso de motores de inferência em Rust otimizados para processamento paralelo e filtragem gramatical por CPU, e a implementação de mecanismos avançados de atenção esparsa para evitar a perda de informação em contextos extensos.

## O Panorama de SLMs em 2026: Arquiteturas Híbridas e Eficiência Local

O mercado de modelos de linguagem compactos na faixa sub-5B evoluiu significativamente, oferecendo alternativas que competem em capacidades de raciocínio com modelos que outrora exigiam recursos de computação consideravelmente superiores. A seleção do modelo base determina o limiar de qualidade para a geração estruturada, especialmente quando o comportamento semântico e a aderência a esquemas complexos são avaliados em contextos longos.

### Família Microsoft Phi-4-mini

O Phi-4-mini (3.8B de parâmetros) destaca-se como uma referência em termos de raciocínio lógico, matemático e de código, rivalizando diretamente com modelos tradicionais de 8B de parâmetros como o Llama 3.1 enquanto consome cerca de metade da memória. A sua arquitetura assenta num mecanismo de Atenção por Agrupamento de Consultas (GQA) estruturado com 8 cabeças de chaves e valores (KV) para 32 cabeças de consulta, um vocabulário expandido de 200k tokens para suporte multilíngue otimizado e incorporações (embeddings) partilhadas de entrada e saída. O modelo foi treinado com foco em dados sintéticos densos em lógica, o que lhe confere uma excelente capacidade para processar e estruturar respostas sob esquemas complexos.

### Família Google Gemma 4

Na linha de desenvolvimento da Google, o Gemma 4 exibe uma versatilidade notável, operando sob o paradigma de Mistura de Especialistas (MoE). A versão Gemma 4 E2B (Edge-to-Browser) foi especificamente desenhada para ambientes móveis e embutidos, permitindo carregamento em formatos otimizados com pegadas de memória inferiores a 1 GB de VRAM via executores simplificados. A arquitetura MoE do Gemma 4 totaliza cerca de 26B de parâmetros totais, mas ativa apenas 4B de parâmetros por token, garantindo uma eficiência de processamento que supera modelos densos convencionais e otimizando a latência de geração no hardware local.

### Família Alibaba Qwen 3.6 Coder

O Qwen 3.6 Coder representa a especialização máxima em engenharia de software e formatação estruturada de dados. Também estruturado sobre uma arquitetura MoE — com cerca de 30-35B de parâmetros totais e aproximadamente 3B de parâmetros ativos por token —, este modelo herda uma capacidade nativa de chamada de funções otimizada através do protocolo OpenAI.

Contudo, testes empíricos revelam limitações importantes em cenários de execução de agentes com sessões longas. Sob falhas de ferramentas ou erros de processamento ao lidar com ficheiros volumosos, os modelos Qwen tendem a sofrer falhas de compressão de contexto. Este fenómeno resulta no esquecimento das instruções originais e no consequente reinício ou paragem da tarefa estruturada.

|**Métrica / Atributo**|**Microsoft Phi-4-mini**|**Google Gemma 4 E2B**|**Qwen 3.6 Coder (MoE)**|
|---|---|---|---|
|**Parâmetros Totais**|3.8B (Densa)|~26B (MoE)|~35B (MoE)|
|**Parâmetros Ativos**|3.8B|~4B|~3B|
|**Janela de Contexto**|128k tokens|128k tokens|262k tokens|
|**GQA Ratio**|4 (8 cabeças KV)|N/D|N/D|
|**Tamanho do Vocabulário**|200 000 tokens|~256 000 tokens|~151 000 tokens|
|**Vulnerabilidade de Contexto**|Elevada estabilidade|Elevada estabilidade|Compressão instável em erro|
|**Licença**|MIT (Permissiva)|Comercial / Aberta|Comercial / Aberta|

## Quantização Severa e Alocação de Precisão via iMatrix

Operar uma dGPU RTX 2060m com 6 GB de VRAM sob uma carga de trabalho de 30k tokens exige uma estratégia extrema de redução de precisão nos pesos do modelo. Excluindo os consumos do sistema operativo e de processos gráficos secundários (que tipicamente subtraem entre 800 MB e 1200 MB), a capacidade real utilizável para inferência fixa-se em aproximadamente 5 GB. Carregar o Phi-4-mini em FP16 exigiria mais de 7.5 GB, inviabilizando imediatamente a operação local. Por este motivo, as técnicas de quantização estática agressiva abaixo de 4 bits tornam-se indispensáveis.

A quantização uniforme tradicional a resoluções muito baixas (como Q3_K_M ou Q3_K_S) penaliza severamente a perplexidade do modelo, gerando saídas incoerentes e falhas sistemáticas na geração de sintaxes JSON válidas. Para reverter esta perda de capacidade semântica, adota-se a quantização baseada em Matriz de Importância (imatrix).

Este processo recorre a um conjunto de dados de calibração linguisticamente rico para mapear as atividades de ativação de cada neurónio e camada ao longo do treino ou calibração pós-treino. A compressão é aplicada de forma assimétrica: os pesos que demonstram maior variabilidade e relevância semântica são protegidos com resoluções mais elevadas, enquanto os tensores menos influentes sofrem uma redução de precisão drástica.

### Alocação de Precisão Dinâmica (IQ4_XS e IQ3_M)

O ecossistema de formatos quantizados em 2026 estabilizou em torno de dois perfis de quantização severa baseada em `imatrix` para cenários de escassez de recursos:

- **IQ4_XS:** Utiliza aproximadamente 4.0 bits por peso. Este formato preserva cerca de 90% da precisão original de FP16, oferecendo um excelente balanço para a geração estruturada. Para otimizar a sua execução, as camadas críticas do modelo — como as de incorporação de tokens (embeddings) e as de projeção de saída — são protegidas utilizando precisão mista a Q5_K ou Q8_0, o que reduz a propagação de erros de arredondamento em até 38%.
- **IQ3_M:** Reduz os pesos do modelo para uma média de 3.3 bits por parâmetro, permitindo que o Phi-4-mini ocupe escassos 1.57 GB de VRAM. Embora a degradação semântica seja percetível em tarefas criativas ou de redação literária, o alinhamento de precisão baseado em `imatrix` garante que o modelo mantenha a sua lógica estrutural de programação ativa, sendo perfeitamente capaz de gerar JSON rígido sob restrições CFG.

|**Formato de Quantização**|**Bits Médios por Peso**|**Tamanho do Modelo (Phi-4-mini)**|**Preservação de Perplexidade**|**Margem de VRAM p/ KV Cache (RTX 2060m)**|
|---|---|---|---|---|
|**FP16**|16.0|7.60 GB|100% (Referência)|Inviável (Excede 6 GB de VRAM)|
|**IQ4_XS (imatrix)**|~4.0|1.90 GB|~90%|~3.10 GB|
|**IQ3_M (imatrix)**|~3.3|1.57 GB|~85%|~3.43 GB|

Ao libertar mais de 3.4 GB de memória gráfica estática com o formato IQ3_M, assegura-se o espaço físico necessário para processar o KV Cache dinâmico sem riscos de fragmentação ou erros de falta de memória (OOM) na dGPU.

## Engenharia de Sistemas em Rust: Crates de Inferência e Decodificação Restrita

A construção de uma infraestrutura bare-metal robusta em Rust permite maximizar a taxa de transferência e obter latências previsíveis, contornando os estrangulamentos de tempo de execução comuns às soluções baseadas em interpretadores ou ambientes geridos. O ecossistema de computação local para modelos de linguagem assenta em três crates principais:

- `candle`: Focado em simplicidade e portabilidade pura, este framework minimalista da Hugging Face serve de base para o desenvolvimento de operadores matemáticos nativos e kernels customizados, mas exige desenvolvimento manual de otimizações complexas de sistema.
- `llama-cpp-2`: Fornece bindings idiomáticos e de alta performance para a biblioteca de referência em C++ `llama.cpp`. Destaca-se pelo excelente suporte ao carregamento de ficheiros GGUF via mapeamento de memória direta (`mmap`), reduzindo cópias no espaço de memória de utilizador e maximizando o reaproveitamento de páginas de memória do sistema operativo.
- `mistral.rs`: Esta biblioteca de inferência nativa em Rust oferece suporte assíncrono avançado com agendamento dinâmico de pedidos e suporte a arquiteturas complexas.

### Avaliação de Desempenho: mistral.rs vs. llama.cpp

Para processamento de contextos longos (30k tokens), a fase de preenchimento do prompt (prefill phase) constitui o maior gargalo computacional. Benchmarks de sistema revelam uma diferenciação drástica entre a performance do `mistral.rs` e do `llama.cpp` consoante a fase de inferência:

- **Fase de Prefill (Prompt Processing):** O `mistral.rs` supera o `llama.cpp` de forma avassaladora, alcançando uma velocidade de processamento de prompt de 937.68 tokens por segundo contra apenas 14.79 tokens por segundo do `llama.cpp` (medido para blocos padrão de 256 tokens). Esta vantagem computacional deve-se ao paralelismo otimizado de carregamento de tensores de prompt e à otimização de kernels em Rust. Sob contextos de longo alcance (5000 tokens), o `mistral.rs` sustenta uma taxa de geração de 35.38 tokens por segundo, enquanto o `llama.cpp` opera a 33.05 tokens por segundo.
- **Fase de Decodificação (Token Generation):** Para a geração autoregressiva individual de tokens, o `llama.cpp` retém uma ligeira vantagem competitiva, registando 78.3 tokens por segundo face aos 68.78 tokens por segundo obtidos pelo `mistral.rs`.

Dada a exigência de processar volumes massivos de contexto inicial (até 30k tokens), a escolha arquitetural recai sobre o `mistral.rs`, visto que a latência acumulada no prefill do `llama.cpp` inviabilizaria a operação interativa local.

### Decodificação Estruturada via llguidance

Para garantir a geração de um JSON rígido e em estrita conformidade com esquemas definidos, a engenharia de prompts tradicional falha devido à sensibilidade do modelo quantizado. Adota-se, como tal, a decodificação estruturada restringindo o espaço de procura do tokenizador com a biblioteca Rust `llguidance`.

O `llguidance` implementa um parser baseado no algoritmo de Earley acoplado a um lexer construído de forma preguiçosa (lazy) com base em derivadas de expressões regulares. A grande vantagem do `llguidance` sobre alternativas como o `Outlines` ou `XGrammar` reside na eficiência de recursos e de inicialização:

- O `Outlines` constrói e compila previamente um autómato finito completo para todos os estados possíveis da gramática. Este processo introduz tempos de arranque significativos (que podem atingir vários minutos em esquemas complexos) e um consumo massivo de memória RAM para indexação de estados.
- O `llguidance` efetua a computação das máscaras binárias em tempo de execução, token a token, com um custo de arranque desprezível. Graças à otimização baseada em "slices" do tokenizador, o cálculo de máscaras válidas sobre vocabulários complexos (como o do Phi-4-mini com 200k tokens) consome um tempo médio de apenas 50 microsegundos na CPU.

```
[ Geração de Logits na GPU ] ────> [ Amostragem de Token ] ────> [ Token Válido ]
          │                                 ▲
          ▼                                 │
[ Thread Separada na CPU ] ─────────────────┘
  Cálculo da Máscara Gramatical (llguidance)
  SIMD AVX2: Logits Inválidos -> -inf
  Earley Parser e Lexer Lazy (<50μs)
```

O processamento do `llguidance` é executado paralelamente à propagação direta (forward pass) do modelo na dGPU. Através de threads de CPU de baixa prioridade geridas via `rayon`, o motor de inferência executa o método `compute_mask` enquanto a GPU calcula os logits do passo seguinte.

Os logits resultantes são transferidos para a memória do sistema, onde as extensões vetoriais AVX2 da CPU processam o mascaramento em paralelo, forçando o valor $-\infty$ sobre os índices de tokens que violam a sintaxe do JSON. O uso de instruções SIMD de 256 bits garante taxas de transferência que evitam estrangulamentos de largura de banda, proporcionando acelerações expressivas nas rotinas de manipulação do vocabulário na CPU.

## Compressão de KV Cache e Mitigação da Amnésia de Outliers

Para contextos de 30k tokens, o KV Cache torna-se o componente mais crítico na gestão de memória gráfica. Utilizando a arquitetura de referência do Phi-4-mini, a quantidade de VRAM necessária para armazenar o cache de Chaves e Valores em precisão FP16 ao longo da sequência de 30k tokens é deduzida através da seguinte formulação matemática:

$$V_{cache} = 2 \times N_{layers} \times N_{kv\_heads} \times D_{head} \times L_{seq} \times B_{elem}$$

[cite: 11]

Onde $N_{layers}$ representa o número de camadas (32), $N_{kv\_heads}$ o número de cabeças KV após o agrupamento GQA (8), $D_{head}$ a dimensão de cada cabeça de atenção (128), $L_{seq}$ o comprimento total da janela de contexto (30 000 tokens) e $B_{elem}$ os bytes de representação do elemento matemático (2 bytes para FP16).

$$V_{cache} = 2 \times 32 \times 8 \times 128 \times 30000 \times 2 = 3\\\ \text{ bytes} \approx 3750 \text{ MB} \text{ (~3.66 GB)}$$

[cite: 11]

Adicionar esta pegada dinâmica de 3.66 GB aos pesos quantizados do modelo estático resultará inevitavelmente em falhas catastróficas de Out-of-Memory (OOM) na RTX 2060m. Torna-se imperativo adotar estratégias de compressão e quantização do próprio cache de Chaves e Valores:

- **Quantização de Cache Q4_K:** Reduz a precisão do KV Cache para 4 bits por elemento com escalas calibradas por bloco, diminuindo a ocupação de memória do cache para apenas 937.5 MB.
- **Atenção Esparsa Híbrida (TurboQuant):** Implementa quantização assimétrica per-channel de 2 a 4 bits combinada com algoritmos de remoção inteligente (como H2O ou PyramidKV), comprimindo a necessidade espacial de armazenamento do cache para menos de 468 MB, mantendo desvios de perplexidade inferiores a 0.5%.

### O Efeito Filtro Passa-Baixo do Mean Pooling sob RoPE

Embora a compressão do KV Cache seja crucial, a amostragem baseada na média local de blocos (Mean Pooling) para a seleção de blocos esparsos destrói a precisão posicional do modelo em janelas longas, gerando "amnésia de outliers". Este fenómeno decorre diretamente da interação do Mean Pooling com as Incorporações Posicionais Rotativas (RoPE).

O RoPE atua aplicando rotações ortogonais aos vetores ao longo das dimensões ocultas das cabeças de atenção. Para uma dada característica no índice da dimensão $j$ e na posição $n$, a rotação é regida pela frequência geométrica $\theta_j$:

$$x^{(j)}_n = x^{(j)}_{nope} \cdot e^{in\theta_j}$$

[cite: 37]

$$\theta_j = b^{-2j/d}$$

[cite: 37]

Isto gera uma diferenciação espectral clara entre as dimensões de alta frequência ($j \to 0$, com rotação angular veloz que codifica distâncias curtas de precisão local) e dimensões de baixa frequência ($j \to d/2$, que preservam estabilidade global para semântica de longo prazo).

Ao realizar o Mean Pooling de um bloco local de chaves de tamanho $B$ na coordenada posicional $n_0$, assumindo que a componente de representação semântica pura $c^{(j)}$ permanece estável ao longo do bloco, a resultante espacial do pooling aproxima-se de uma soma geométrica:

$$\bar{q}^{(j)} \approx \frac{c^{(j)}}{B} e^{in_0\theta_j} \sum_{k=0}^{B-1} e^{ik\theta_j}$$

[cite: 37]

A resolução matemática desta soma revela o fator de atenuação espectral $\lambda_j(B)$ induzido pelo processo de pooling:

$$\lambda_j(B) \triangleq \frac{|\bar{q}^{(j)}|}{|c^{(j)}|} = \frac{1}{B} \left| \frac{\sin\left(\frac{B\theta_j}{2}\right)}{\sin\left(\frac{\theta_j}{2}\right)} \right| \approx \left| \text{sinc}\left(\frac{B\theta_j}{2\pi}\right) \right|$$

[cite: 37]

A função sinc comprova de forma inequívoca que o Mean Pooling se comporta como um filtro passa-baixo clássico. Nas dimensões iniciais de alta frequência (responsáveis pela precisão e alinhamento do RoPE a curta distância), o tamanho do bloco $B = 128$ gera uma atenuação severa ($\lambda_j(B) \to 0$) devido à interferência destrutiva das fases na soma. Esta atenuação elimina o sinal posicional fino, criando uma "zona cega" que impossibilita o alinhamento da consulta com coordenadas espaciais exatas nos 30k tokens.

### Resolução via Max Pooling e Arquitetura Prism

Para restabelecer a precisão posicional em contextos de larga escala sem introduzir sobrecargas computacionais de pesquisa ao nível do token, duas soluções técnicas alternativas são integradas no motor de inferência:

#### Max Pooling de Ativações

Ao contrário da média aritmética, a aplicação de **Max Pooling** sobre os pesos ou ativações de atenção dentro de cada bloco de chaves isola as ativações extremas:

$$\bar{k}_{max}^{(j)} = \max_{0 \le k < B} \left( k_{n_0 + k}^{(j)} \right)$$

O Max Pooling é nativamente robusto à atenuação rotacional do RoPE, pois ao selecionar o valor máximo absoluto em cada dimensão em vez de efetuar uma soma vetorial ponderada, o operador preserva as coordenadas de ativação dos tokens âncora (outliers de atenção), mantendo o sinal de alta frequência intacto. Isto impede a amnésia de outliers, garantindo que o modelo localize informações específicas mesmo sob quantização severa.

#### Arquitetura Prism (Spectral-Aware Block-Sparse Attention)

A técnica Prism resolve a degradação de sinal separando a atenção em duas bandas espectrais distintas:

1. **Decomposição em Duas Bandas:** Fatiam-se os tensores de chaves e consultas isolando as dimensões de alta frequência ($d_{high}$) e de baixa frequência ($d_{low}$), aplicando o pooling local de forma independente a cada subgrupo de canais para mitigar a interferência destrutiva de fase.
2. **Calibração de Temperatura por Energia:** Como a compressão atenua residualmente as amplitudes de logits, o Prism aplica fatores de escala de temperatura dinâmicos baseados no balanço energético (RMS norm) das componentes originais versus as componentes resultantes do pooling:

$$\tau_{high} = \sqrt{\frac{d_{high}}{d}} \cdot \left(\frac{\text{rms}(\bar{Q}_{high})}{\text{rms}(\bar{Q})}\right) \cdot \left(\frac{\text{rms}(\bar{K}_{high})}{\text{rms}(\bar{K})}\right)$$

[cite: 37]

$$\tau_{low} = \sqrt{\frac{d_{low}}{d}} \cdot \left(\frac{\text{rms}(\bar{Q}_{low})}{\text{rms}(\bar{Q})}\right) \cdot \left(\frac{\text{rms}(\bar{K}_{low})}{\text{rms}(\bar{K})}\right)$$

[cite: 37]

Esta calibração matemática restaura as amplitudes dos logits de atenção sem introduzir novos hiperparâmetros ou requerer etapas de treino adicionais, assegurando a recuperação correta da informação ao longo dos 30k tokens.

## Conclusões e Recomendações de Engenharia para Produção

A inferência local estável e de alta performance de modelos sub-5B em contextos longos de até 30k tokens sob restrições extremas de memória (6 GB de VRAM) é perfeitamente viável através de uma abordagem disciplinada de engenharia de sistemas em Rust.

### Diretrizes de Implementação

- **Modelo Base e Quantização:** Adotar o Microsoft Phi-4-mini (3.8B) quantizado no formato IQ3_M utilizando matrizes de importância (`imatrix`) geradas com dados representativos de esquemas estruturados. Isto restringe os pesos estáticos a 1.57 GB, disponibilizando a VRAM restante para buffers e dados de contexto.
- **Gestão de KV Cache:** Configurar a retenção do KV Cache quantizado em precisão Q4_K. Adicionalmente, ativar o operador Prism ou amostragem baseada em Max Pooling de ativações para evitar a degradação de sinal e garantir a recuperação da "agulha no palheiro" ao longo de todo o contexto.
- **Pilha de Execução de Software:** Utilizar a biblioteca `mistral.rs` compilada com suporte a CUDA, aproveitando as taxas superiores de preenchimento de prompt (prefill). Integrar a decodificação estruturada via `llguidance` com agendamento multithreading assíncrono na CPU, aplicando a filtragem e mascaramento de logits inválidos a nível de registos SIMD AVX2 de 256 bits.

Esta arquitetura assegura uma solução bare-metal de elevada fiabilidade sem depender de recursos na nuvem, maximizando a eficiência de processamento e garantindo a validade absoluta das estruturas JSON geradas.

---

# Resumo Executivo

Como Engenheiros de IA focados em FinOps locais, a nossa investigação para a Fase 1.5 de Destilação no núcleo do SODA nos traz verdades dolorosas, mas também nos dá um norte arquitetural extremamente claro para operar no limite físico da RTX 2060m de 6 GB de VRAM.

Abaixo está o **resumo executivo** das nossas descobertas e os 3 aprendizados de engenharia mais críticos obtidos na vanguarda tecnológica de 2026.

### O Resumo Arquitetural (Como resolvemos a equação de VRAM)

Para enfiar um contexto de 30k tokens e um parser determinístico dentro de 6 GB de VRAM (onde temos ~5 GB utilizáveis na prática devido ao consumo de vídeo do SO):

- **Os Pesos do SLM:** Devem ocupar no máximo **1.57 GB** de VRAM. Isso é alcançado usando o **Microsoft Phi-4-mini (3.8B)** sob quantização extrema **IQ3_M** baseada em matriz de importância (`imatrix`).
- **O KV Cache:** Em FP16, ele consumiria sozinho ~3.66 GB, gerando um erro inevitável de Out-of-Memory (OOM). Ao quantizarmos o KV Cache para **Q4_K** (e adotarmos compressão por atenção esparsa), reduzimos essa pegada para **937.5 MB**.
- **Garantia de Esquema JSON:** Sem overhead térmico na GPU, gerido em tempo de execução via CPU em Rust.

### Os 3 Grandes Aprendizados da Investigação

#### 1. Seleção do Operário: Phi-4-mini é o trator; Qwen sofre sob fadiga

Embora a família Qwen 3.6 Coder MoE apresente um excelente suporte nativo de chamada de ferramentas e geração estruturada, testes práticos de 2026 apontam uma falha crítica em cenários de agentes de longa duração. Sob falhas sucessivas de ferramentas ou requisições complexas em arquivos extensos, os modelos Qwen tendem a falhar na compressão de contexto, sofrendo "amnésia" de instruções e reiniciando a tarefa do zero.

Por outro lado, o **Phi-4-mini (3.8B)** destaca-se pela impressionante densidade lógica e de raciocínio lógico-matemático. Ao aplicar a quantização baseada em `imatrix` (formato IQ3_M), as camadas críticas de embedding e projeção de saída são protegidas, o que reduz erros de arredondamento em até 38%. O modelo mantém uma resiliência impecável à imposição de esquemas JSON mesmo sob quantização severa.

#### 2. O Gargalo de Prefill e a Decodificação Restrita via llguidance na CPU

Integrar gramáticas em Rust nativo elimina qualquer gargalo induzido por Python. O aprendizado crítico aqui é o comportamento das ferramentas em relação às fases da inferência:

- **Prefill:** O processamento inicial dos 30k tokens de entrada é o nosso maior gargalo. Benchmarks mostram que a biblioteca **`mistral.rs`** processa prompts de forma esmagadora frente aos bindings do `llama.cpp` (937.68 tokens por segundo contra pífios 14.79 tokens por segundo). Portanto, o pipeline deve rodar sobre o `mistral.rs`.
- **Garantia de Esquema:** A biblioteca **`llguidance`** é a melhor escolha. Ela utiliza um Earley parser acoplado a um lexer preguiçoso baseado em derivadas de regex. Diferente de ferramentas como o _Outlines_ (que pré-compila autômatos completos, exigindo gigabytes de memória RAM e minutos de startup em esquemas gigantescos), o `llguidance` calcula as máscaras de tokens dinamicamente em menos de 50 microsegundos por token de CPU.
- **Paralelismo de Logits:** A máscara binária calculada pelo `llguidance` roda em threads de CPU em paralelo com a GPU. Quando a GPU entrega os logits brutos, os logits inválidos que violam o JSON Schema são forçados a $-\infty$ diretamente na CPU utilizando instruções vetoriais SIMD AVX2 de 256 bits. Isso poupa preciosa largura de banda e resfria termicamente a nossa RTX 2060m.

#### 3. A Física do KV Cache: Evitando a "Amnésia de Outliers" (Max Pooling vs. Mean Pooling)

Ao processar contextos massivos, precisamos compactar ou descartar partes do KV Cache. No entanto, a literatura de 2026 prova matematicamente que técnicas comuns baseadas em _Mean Pooling_ (como agrupar chaves tirando a média local) destroem a sinalização posicional fina.

O motivo reside na interação com as Incorporações Posicionais Rotativas (RoPE). As dimensões de alta frequência do RoPE (que codificam distâncias curtas e ordenação sintática local) sofrem interferência destrutiva na média matemática, gerando uma atenuação espectral drástica que se comporta como um filtro passa-baixo clássico. O modelo simplesmente fica "cego" para padrões sintáticos e de ordenação local (como URIs, caminhos de arquivo, caminhos AST) no meio de um arquivo volumoso.

**Como resolver:**

- **Max Pooling:** Em vez da média aritmética, a aplicação do operador de _Max Pooling_ local preserva as ativações extremas (os "outliers" de atenção que carregam a semântica de interesse). O operador de máximo é imune à atenuação de fase rotacional do RoPE, mantendo o sinal de alta frequência intacto.
- **Arquitetura Prism:** O Prism divide a atenção em duas bandas espectrais (alta frequência para posições locais e baixa frequência para dependências semânticas globais). Ao calibrar a temperatura de cada ramo com base em energia RMS norm, o Prism recupera de forma limpa os sinais de localização atenuados, eliminando a necessidade de recomputação de tokens na GPU e garantindo precisão absoluta no teste "Needle in a Haystack".

### Recomendação de Pilha Tecnológica para o SODA

1. **Modelo:** Phi-4-mini (3.8B, MIT License) carregado via GGUF no formato de quantização `imatrix` **IQ3_M** (pegada estática de ~1.57 GB).
2. **Motor de Inferência:** `mistral.rs` compilado com CUDA e Flash Attention 2 habilitados.
3. **Constrained Decoding:** Crate `llguidance` integrada nativamente com mascaramento paralelo via CPU SIMD AVX2.
4. **Gestão do Contexto:** Ativação de quantização de KV Cache a 4 bits (`Q4_K`) combinada com a técnica **Prism** ou compressão baseada em **Max Pooling** de ativações para eliminar a amnésia de outliers posicional.