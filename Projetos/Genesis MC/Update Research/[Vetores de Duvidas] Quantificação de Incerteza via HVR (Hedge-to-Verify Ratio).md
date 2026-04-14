**Atue como um Pesquisador de Segurança de IA e Engenheiro de Inferência Edge (Estado da Arte de 2026).**

**CONTEXTO DO PROJETO (SODA - Genesis MC):** Nosso sistema local, SODA, roda modelos de raciocínio destilados (como DeepSeek-R1-Distill-Qwen) em uma dGPU NVIDIA RTX 2060m com estritos 6GB de VRAM.

Precisamos de um mecanismo que perceba quando o modelo local está confabulando/alucinando para interromper a geração e acionar um _Fallback_ para a nuvem via protocolo MCP. Inicialmente pensamos em usar "Entropia Semântica", mas calcular isso exige amostragem múltipla (gerar várias respostas e compará-las), o que é matematicamente inviável pois estouraria nossos 6GB de VRAM e a latência aceitável.

**A MISSÃO:** Precisamos implementar um método de passagem única (_single-pass_) de custo zero computacional. A pesquisa de vanguarda de 2026 estabeleceu o framework **SELFDOUBT** e a métrica **HVR (Hedge-to-Verify Ratio)**, que mede marcadores de hesitação diretamente no rastro de raciocínio da IA (dentro das _thinking tags_). Traces com HVR = 0 provaram estar corretos 96.1% das vezes.

Elabore um guia de implementação tática e matemática contendo:

1. **A Anatomia do HVR:** Qual é a fórmula exata do _Hedge-to-Verify Ratio_? Liste exemplos práticos e exaustivos de "Marcadores de Hesitação" (Hedge) e "Marcadores de Verificação" (Verify) que a nossa lógica em Rust deve monitorar durante o _streaming_ dos tokens.
    
2. **O Portão de Confiança (HVR = 0 Gate):** Como construir essa verificação usando expressões regulares (Regex) ou analisadores léxicos simples no nosso roteador Rust para que funcione em tempo real com custo de inferência zero?
    
3. **Fusão Z-Score e Thresholding:** Quando o HVR for maior que 0, como o sistema deve cruzar esse valor com a confiança verbalizada do modelo usando normalização Z-score para gerar a pontuação final do _SelfDoubt_?
    
4. **Guarda de Aborto Dinâmico:** Desenhe o fluxo lógico: como o sistema SODA intercepta o _stream_ de tokens de raciocínio, calcula o HVR em tempo real e decide abortar a geração local, encaminhando o _prompt_ para a nuvem, _antes_ de entregar uma resposta alucinada ao usuário?
    

Traga regras lógicas claras, arquitetura de interceptação de _streaming_ e fórmulas aplicáveis ao nosso cenário de 6GB de VRAM.