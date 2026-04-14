---
sticker: lucide//bot
---
# Relatório de Engenharia: Orquestração e Viabilidade de Inferência Agêntica em Hardware Restrito (Edge Computing)

## 1. Análise de Contenção de Hardware e Física de Memória

A concepção e implementação de um Sistema Operacional Agêntico (Genesis MC) operando na borda (Edge Computing) exige uma arquitetura de inferência rigorosamente otimizada. A topologia física em questão é baseada em um **Asus ZenBook Duo UX581** compreendendo um processador **Intel Core i9 (9ª Geração)** com gráficos integrados (iGPU), auxiliado por **32 GB de RAM** de sistema e uma Unidade de Processamento Gráfico (GPU) dedicada **NVIDIA RTX 2060m, equipada com 6 GB de VRAM**.

Um trunfo vital deste setup é a configuração de exibição: ao desligar as duas telas internas 4K (OLED principal e ScreenPad Plus) e utilizar exclusivamente um monitor externo via HDMI, o sistema passa a rotear a interface gráfica primariamente através da iGPU da Intel. Este cenário isola a RTX 2060m, deixando-a operar em um estado quase "headless" (sem interface gráfica), o que altera positivamente as restrições absolutas na alocação de tensores e KV Cache.

A viabilidade de execução de LLMs até a faixa de 14 bilhões de parâmetros depende da adoção de algoritmos de compressão de peso e quantização agressiva, como GGUF (tipicamente no formato Q4_K_M), AWQ e GPTQ. Através da quantização para 4-bits, a exigência recai para aproximadamente 0,5 a 0,7 GB por bilhão de parâmetros, comprimindo a pegada estática de um modelo de 8B para a faixa de 4,2 a 4,8 GB.

### 1.1. O Limite Matemático de VRAM e a Vantagem "Headless"

Em sistemas tradicionais de tela única renderizados pela GPU dedicada, impõe-se um _overhead_ de segurança de 20% para evitar falhas por falta de memória (OOM). Contudo, com a interface gráfica do monitor HDMI sendo gerida pela iGPU da Intel, a RTX 2060m é liberada do fardo de alocar buffers de tela pesados. A equação de disponibilidade de memória na sua placa gráfica é redefinida da seguinte forma:

|**Componente de Memória**|**Capacidade Alocada**|**Impacto Operacional**|
|---|---|---|
|**VRAM Física Total**|6.144 MB (6,00 GB)|Limite rígido do hardware da NVIDIA RTX 2060m.|
|**Overhead do Sistema (Otimizado)**|~400 MB (~0,40 GB)|Mínimo residual exigido pelo driver NVIDIA e processos de background do SO.|
|**VRAM Efetiva para Inferência**|**~5.744 MB (~5,60 GB)**|Teto estendido para a soma dos pesos do modelo, ativações e KV Cache.|
|**RAM do Sistema (Reserva)**|**32.000 MB (32 GB)**|Buffer massivo para _offloading_ de camadas excedentes e KV Cache sem esgotamento do sistema.|

Qualquer configuração que ultrapasse a marca de 5,6 GB de VRAM forçará um _spillover_ (transbordamento) para a RAM do sistema (32 GB). Embora a transferência via barramento PCIe reduza a taxa de geração de tokens (TPS), a abundância de 32 GB de RAM garante que o sistema não sofrerá travamentos catastróficos, suportando a degradação da latência de forma controlada.

### 1.2. O Paradoxo do Contexto Expandido e a Arquitetura de Atenção

O dimensionamento da janela de contexto tem um impacto direto no consumo de VRAM devido à necessidade de armazenar o KV Cache. Para solucionar este problema em dispositivos de borda com 6GB, a indústria adotou arquiteturas alternativas:

- **Grouped-Query Attention (GQA):** Utilizada em modelos como Mistral e Qwen, a GQA compartilha as cabeças de chaves e valores (KV) entre múltiplas cabeças de consulta (Query), reduzindo a pegada do cache em um fator de 4x a 8x.
- **Multi-Head Latent Attention (MLA):** Introduzida pela série DeepSeek (V2, V3 e R1), a MLA comprime os tensores KV em um espaço vetorial de dimensão muito menor. Esta inovação permite uma redução de até 93% do tamanho do KV Cache em comparação com implementações tradicionais, tornando contextos massivos viáveis na VRAM restrita da RTX 2060m.

## 2. Avaliação Exaustiva de Modelos Fundacionais

A seleção dos modelos para o Genesis MC agora se beneficia da capacidade de fazer _offload_ agressivo para a RAM de 32 GB quando a VRAM de 6 GB estiver cheia.

### 2.1. A Família Qwen 3.5 (Alibaba Cloud)

A série Qwen 3.5 adota uma arquitetura híbrida inovadora que funde redes de atenção linear (_Gated Delta Networks_) com mecanismos de atenção tradicional.

| **Modelo (Série Qwen 3.5)** | **Quantização** | **VRAM Estática Exigida** | **Arquitetura** | **Janela de Contexto Nativa** |
| --------------------------- | --------------- | ------------------------- | --------------- | ----------------------------- |
| **Qwen 3.5 4B**             | Q4_K_M          | ~3.49 GB                  | Híbrida / GQA   | 262k                          |
| **Qwen 3.5 9B**             | Q4_K_M          | ~6.49 GB                  | Híbrida / GQA   | 262k                          |

**Análise de Viabilidade:** O modelo **Qwen 3.5 9B** exige 6,49 GB estáticos o que excede levemente o total de 6GB da GPU. Em um sistema com 16GB, isso seria um gargalo doloroso. Contudo, com seus **32 GB de RAM**, você pode alocar confortavelmente as últimas camadas e o vasto KV Cache para a memória do sistema. Como a maior parte do modelo (~85%) caberá na RTX 2060m, a queda de performance será mínima, operando a velocidades altamente funcionais (entre 20 e 30 tps) enquanto lida com raciocínio agêntico de alto nível.

### 2.2. Ecossistema Mistral: A Série Ministral 3 e Mistral Small

O modelo **Ministral 3 8B** possui 8.4 bilhões de parâmetros para raciocínio lógico e um codificador de visão nativo de 400M, concedendo habilidade inata de analisar fluxos multimodais.

- **Gestão de Memória:** Na quantização Q4_K_M (aproximadamente 4,5 a 5 GB), o modelo atinge o teto da VRAM disponível. Ele suporta uma vasta janela de contexto de 256.000 tokens. Com seus 32 GB de RAM, o contexto pode se expandir livremente para a RAM principal quando o limite da VRAM for atingido.
- **Potencial Agêntico:** A Mistral calibrou este modelo para aderência estrita a chamadas de função (Tool Calling / JSON outputs) tornando-o um cérebro de orquestração perfeito para o Genesis MC, roteando tarefas sem alucinar a sintaxe.

### 2.3. Rnj-1: O Especialista em Código

O modelo **Rnj-1 (8.3B)** foi concebido puramente do zero com foco implacável em geração de código de alta complexidade.

- **Desafio de Memória:** Diferente de outros modelos, o Rnj-1 utiliza exclusivamente atenção global em todas as suas 32 camadas (sem janelas deslizantes). Isso resulta em um consumo de VRAM formidável quando o contexto de 32k se expande. A quantização Q4_K_M comprime os pesos para cerca de 4,8 GB.
- **A Vantagem dos 32GB:** O KV Cache expansivo do Rnj-1 transbordará da RTX 2060m rapidamente. No entanto, os seus 32GB de RAM absorverão esse cache perfeitamente. Ele deve ser convocado para sessões isoladas de geração de código profundo, onde a acurácia é mais importante que a velocidade extrema de tokens.

### 2.4. DeepSeek-R1-Distill (7B/14B) e DeepSeek V4

A série R1 destilada emula as cadeias de pensamento (Chain-of-Thought) de modelos massivos.

- **DeepSeek-R1-Distill-Qwen-7B:** Exige cerca de 4,5 a 4,8 GB de VRAM em Q4_K_M. Isso cabe perfeitamente na sua cota efetiva de 5,6 GB. É o modelo ideal de raciocínio passo a passo residente na GPU.
- **DeepSeek-R1-Distill-Qwen-14B:** Em Q4_K_M, os pesos ocupam cerca de 8 a 10 GB. Graças aos seus 32 GB de RAM, é plenamente possível rodar o 14B através de _layer offloading_ (ex: 20 camadas na GPU, 20 na CPU). A velocidade cairá para ~15 tps, mas a qualidade analítica em refatorações complexas será muito superior.
- **DeepSeek V4:** Esperado para introduzir memórias condicionais _Engram_ e atenção hiper-restrita (mHC), permitindo contextos de 1 milhão de tokens de forma muito mais barata. Quando versões destiladas do V4 saírem, o impacto na VRAM será ainda menor.

### 2.5. FunctionGemma: O Paradigma de Tool Calling Extremo

O **FunctionGemma 3 270M** foi treinado com o propósito exclusivo de traduzir intenções não-estruturadas em saídas determinísticas de API.

- **Consumo Invisível:** O modelo consome míseros 288 MB no armazenamento em INT8 e opera com um pico de RAM em torno de 550 MB.
- **Uso Estratégico:** Com um Time-To-First-Token (TTFT) de 0,3 segundos ele pode viver permanentemente rodando na sua RAM do sistema ou na iGPU da Intel (mais sobre isso abaixo), servindo como o porteiro hiper-rápido do seu SO.

## 3. Exploração Aberta: Contextos Massivos (32k+) em Modelos Emergentes

A alocação de contextos de 32k a 128k requer GBs adicionais para o KV Cache. A sua configuração de 32 GB de RAM combinada com 6 GB de VRAM permite explorar os seguintes modelos que suportam contextos massivos:

1. **Llama 3.3 8B Instruct (Variantes Abliteradas):** Suporta nativamente 128.000 tokens. Compactando o KV Cache no llama.cpp (Flash Attention), você pode inserir livros inteiros no contexto usando a folga da RAM do sistema.
2. **Phi-4 Mini (3.8B):** Um modelo denso de 3.8B com forte capacidade de raciocínio que ocupa apenas ~3.5GB em RAM/VRAM combinadas, ideal para rodar integralmente dentro da RTX 2060m deixando gigabytes livres para um contexto gigantesco.
3. **Qwen 2.5 Coder 7B / OpenCoder 8B:** Essenciais para ingerir múltiplos arquivos e diretórios simultaneamente durante sessões de codificação agêntica.

## 4. Análise de Ferramentas de Execução e Orquestração VRAM/RAM

O desafio do seu hardware não é a capacidade total (38 GB somados), mas a largura de banda. A topologia deve minimizar a transferência cega de pesos entre a RAM DDR4 e a VRAM GDDR6.

### 4.1. vLLM e o Limite do PagedAttention

O **vLLM** e seu _PagedAttention_ otimizam o KV Cache magistralmente. Contudo, o vLLM é projetado para cenários onde o modelo cabe na GPU. Quando forçado a fazer _offloading_ para a CPU (como você faria com modelos de 9B a 14B), a arquitetura do vLLM sofre gargalos brutais de latência no barramento PCIe, chegando a quedas de 35x no desempenho. O vLLM deve ser evitado em seu caso de uso para os modelos especialistas que excedam 5,6 GB.

### 4.2. llama.cpp: A Supremacia do mmap e Modo Router

O **llama.cpp** é a escolha definitiva. O uso da chamada de sistema `mmap()` significa que os 32 GB de RAM e o NVMe atuarão como um cache hiper-eficiente. Ao alocar modelos que excedam os 6GB (como o Qwen 3.5 9B ou o DeepSeek 14B), o llama.cpp divide matematicamente o processamento das camadas entre a sua CPU i9 e a RTX 2060m de forma linear, mantendo uma geração de tokens perfeitamente utilizável para agentes autônomos. O recente "Router Mode" permite gerenciar requisições e manter contextos em memória sem precisar religar os servidores.

### 4.3. Isolamento Multi-GPU (Intel iGPU + NVIDIA dGPU)

Com o monitor na porta HDMI (gerida pela iGPU Intel UHD), a arquitetura `llama.cpp` suporta execução distribuída híbrida (SYCL e CUDA simultaneamente).

Você pode instruir o sistema a instanciar o **FunctionGemma 270M** compilado com o backend SYCL (Intel) diretamente na iGPU, utilizando os 32GB de RAM do sistema. Simultaneamente, o servidor CUDA lida com os pesos pesados (7B/9B) exclusivamente na RTX 2060m. Isso cria uma rodovia livre de colisões térmicas e de dados.

### 4.4. Llama-swap e a Mecânica do "Swap" Ultrarrápido

Dado o seu generoso pool de 32 GB de RAM, manter modelos densos simultaneamente é totalmente possível usando o **llama-swap**.

- Ao habilitar a flag oculta `GGML_CUDA_ENABLE_UNIFIED_MEMORY=1`, o llama-swap utiliza a RAM principal como um estacionamento morno para os pesos do modelo.
- Se o Rnj-1 estiver na VRAM codificando, e o agente rotear uma validação para o DeepSeek R1 7B, o Rnj-1 é empurrado inteiramente para a RAM do i9, e o DeepSeek (que já estava na RAM) é copiado para a VRAM. Usando as vias rápidas do PCIe Gen3/4, a troca dos 5GB de pesos leva de **2 a 5 segundos**. Isso é uma ordem de magnitude mais rápido que uma carga a frio do SSD.

## 5. Proposta de Topologia de Inferência Local para o Genesis MC

Considerando o hardware otimizado (RTX 2060m Headless de 6GB, i9 9ª Ger, 32GB RAM e isolamento de exibição), a orquestração prescreve uma verdadeira configuração de Mistura de Especialistas (MoE) gerenciada no nível do sistema operacional.

### Nível 1: Gateway Cognitivo Always-On

**Modelo Assinalado:** FunctionGemma 3 270M (INT8) **Mecanismo de Execução:** Llama.cpp compilado com SYCL (Backend Intel). **Alocação:** iGPU Intel UHD + RAM do Sistema. **Função:** Opera em latência próxima de zero. Analisa todas as solicitações do usuário e emite chamadas JSON instantâneas para ferramentas simples do sistema (bash, arquivos), sem jamais tocar ou acordar a placa NVIDIA.

### Nível 2: Executores Especialistas (Heavy Workers)

**Modelos em Repouso Quente (RAM de 32 GB):**
- Especialista em Código/Engenharia: `Rnj-1 8B` (Q4_K_M).
- Raciocínio Matemático e Lógico: `DeepSeek-R1-Distill-Qwen-7B` (Q4_K_M).
- Fluxos Multimodais/Generalista: `Ministral 3 8B` (Q4_K_M).

**Mecanismo de Execução:** Proxy Llama-swap com `GGML_CUDA_ENABLE_UNIFIED_MEMORY=1`. **Alocação Ativa:** NVIDIA RTX 2060m (6GB VRAM Efetiva). 
**Fluxo Operacional:**
1. Quando uma tarefa complexa é identificada, o Llama-swap injeta o especialista correspondente (ex: `Rnj-1`) da RAM (onde estava armazenado) para a VRAM da RTX 2060m em 2 a 5 segundos.
2. Todo o poder computacional dos Tensor Cores da NVIDIA é direcionado ao modelo selecionado, garantindo entre 30 a 50 tokens por segundo.
3. Como a RTX 2060m não está lidando com nenhuma renderização de telas, o limite de 6GB é aproveitado em sua totalidade (cerca de 5,6GB livres), permitindo manter um contexto imenso na placa gráfica sem travar. O que exceder este limite flui pacificamente para os 32GB de RAM do i9.
4. Após o trabalho, o modelo retorna à RAM, aguardando o próximo passo do ciclo agêntico, garantindo um Sistema Operacional fluido, local e altamente responsivo.