**Atue como um Engenheiro de Otimização de Inferência CUDA e Arquiteto de IA Edge (Estado da Arte de 2026).**

**CONTEXTO DO PROJETO (SODA - Genesis MC):**

Estamos construindo um ecossistema de agentes locais e persistentes. O "cérebro" local é executado em uma NVIDIA RTX 2060m com um limite de hardware estrito de exatos **6GB de VRAM**. Estamos utilizando modelos da família DeepSeek-R1-Distill (ex: 1.5B parâmetros), que ocupam cerca de 1.2GB a 1.5GB em quantização de 4-bits. O restante da VRAM é devorado pelo _KV Cache_ em conversas multi-turnos.

A DeepSeek introduziu a arquitetura _Multi-Head Latent Attention_ (MLA), que desacopla e comprime chaves e valores em um vetor latente (ex: usando dimensões $d_c + d_R \approx 576$), reduzindo o KV Cache severamente quando comparado ao MHA ou GQA tradicional.

**A MISSÃO:**

Elabore um relatório de engenharia matemática focado na fisiologia da memória para a nossa dGPU de 6GB. O relatório deve conter:

1. **A Anatomia do MLA:** Como exatamente funciona a compressão de baixo ranque (Low-Rank Compression) e a injeção do RoPE (Rotary Position Embedding) desacoplado no formato MLA da DeepSeek?
    
2. **A Fórmula Exata de VRAM:** Apresente a fórmula matemática para calcular o consumo em Megabytes do KV Cache por token usando MLA. Compare isso com o consumo de um modelo equivalente usando GQA.
    
3. **Planejamento de Orçamento SODA:** Dado que temos $\sim 4.5\text{GB}$ livres após o carregamento dos pesos, calcule exatamente o limite máximo de tokens de contexto simultâneos que podemos manter na RTX 2060m usando um modelo de 1.5B com arquitetura MLA (em precisão FP16 ou FP8).
    
4. **Profilaxia de OOM em K-V Cache:** Como configurar backends (como vLLM ou llama.cpp) para não fragmentar a memória em cenários agênticos? Aborde o uso de flags modernas como `expandable_segments` e paginação de atenção (_PagedAttention_).
    

Seja estritamente técnico e matemático. Sem conselhos genéricos sobre "comprar mais hardware".