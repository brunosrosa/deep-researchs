**Atue como um Engenheiro de Machine Learning e Arquiteto de IA de Vanguarda (Estado da Arte de 2026).**

**CONTEXTO DO PROJETO (SODA - Genesis MC):** Estamos desenvolvendo o SODA (Sovereign Operating Data Architecture), um ecossistema agêntico local com hardware estrito e assimétrico:

1. CPU Intel i9 (32GB RAM) - Usada para roteamento ultra-rápido sub-50ms e modelos de extração leves.
    
2. dGPU NVIDIA RTX 2060m (Limite rígido de 6GB VRAM) - Usada para inferência pesada com modelos destilados (ex: DeepSeek-R1-Distill-Qwen-1.5B/3B).
    
3. Nuvem via MCP (Model Context Protocol) - Fallback para tarefas que estouram a memória ou o conhecimento local.
    

Atualmente, usamos um roteador baseado em _embeddings_ e KNN na CPU para classificar a intenção do _prompt_. No entanto, a similaridade semântica falha em prever a **dificuldade real** da tarefa para o modelo local. O roteador frequentemente manda tarefas complexas para a GPU só porque o "tema" parece certo, resultando em falhas.

**A MISSÃO:** Precisamos substituir o KNN pelo **Roteamento Mecanicista**, utilizando as "Prefill Activations" (ativações geradas antes do primeiro token). Pesquisas recentes apontam para a arquitetura **SharedTrunkNet** e o conceito de **Encoder-Target Decoupling**, que provaram fechar 45.58% da lacuna de precisão de um oráculo perfeito.

Elabore um relatório técnico arquitetural focado em:

1. **Encoder-Target Decoupling:** Como usar um modelo minúsculo na CPU i9 (o Encoder) para extrair estados ocultos e prever se o modelo de 1.5B/3B na GPU (o Target) vai falhar naquela tarefa específica?
    
2. **Matemática da Extração:** Como aplicar sondas baseadas em Separabilidade de Fisher e Dimensionalidade Efetiva para encontrar as camadas corretas do Encoder de onde extrair esses sinais?
    
3. **Arquitetura SharedTrunkNet:** Desenhe o diagrama lógico (ou descreva detalhadamente) de como essa rede neural conjunta processa as ativações na CPU para gerar uma pontuação de "probabilidade de sucesso".
    
4. **Integração SODA:** Como essa inferência do SharedTrunkNet se encaixa no nosso limite de latência de 50ms? Quais as ferramentas em Rust ou C++ recomendadas para rodar essa predição em tempo real?
    

Seja técnico, direto e focado em engenharia de sistemas com restrição severa de VRAM. Sem introduções genéricas.