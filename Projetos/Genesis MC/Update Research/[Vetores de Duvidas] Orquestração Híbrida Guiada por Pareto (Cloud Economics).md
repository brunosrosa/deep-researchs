**Atue como um Engenheiro de FinOps de IA e Arquiteto de Nuvem Híbrida (Estado da Arte de 2026).**

**CONTEXTO DO PROJETO (SODA - Genesis MC):** O SODA é o nosso ecossistema agêntico local. O roteador (escrito em Rust com Máquina de Estados) decide instantaneamente se uma tarefa roda localmente (na nossa CPU i9 ou na dGPU RTX 2060m com estritos 6GB de VRAM) ou se precisa de "Fallback" para a nuvem via Model Context Protocol (MCP).

Para a nuvem, o mercado de IA se dividiu em dois regimes econômicos: temos assinaturas "Flat-Rate" com tokens ilimitados (como Featherless.ai para modelos de peso aberto hiper-otimizados) ótimas para alto volume, e APIs Premium cobradas por token (ex: Claude 4.5/4.6, GPT-5.x) em gateways como o OpenRouter para tarefas de missão crítica de raciocínio.

**A MISSÃO:** Precisamos de um algoritmo financeiro de seleção de modelos em tempo real. O sistema não pode usar regras estáticas de provedores, pois eles degradam silenciosamente ou mudam de preço. A lógica deve ser guiada pela **Fronteira de Pareto** (Custo x Qualidade x Latência) e utilizar algoritmos como o **ParetoBandit** (orçamento adaptativo) para escolher o melhor endpoint dinamicamente via cliente MCP.

Elabore um relatório de arquitetura tática contendo:

1. **A Fronteira de Pareto de 2026:** Como o SODA deve mapear e dividir matematicamente as cargas de "Alto Volume/Baixo Valor" (direcionadas para gateways Flat-Rate rodando modelos como DeepSeek-R1/V3) versus "Missão Crítica" (direcionadas para as CLIs restritas ou APIs premium)?
    
2. **O Algoritmo de Bandits Adaptativos:** Como implementar o conceito do `ParetoBandit` (ou similar) na nossa Máquina de Estados (FSM) em Rust? Como ele faz o "pace" (ritmo) do orçamento e se adapta à mudança de qualidade/preço dos provedores on-the-fly?
    
3. **Telemetria Dinâmica e Gateways:** Como utilizar sistemas de avaliação contínua de provedores (como a telemetria do _Auto Exacto_ do OpenRouter, que mede taxa de sucesso de tool-calling a cada 5 minutos) para alimentar nosso roteador sem introduzir latência?
    
4. **A Matriz de Fallback (Cascade Routing):** Crie a árvore de decisão lógica de contingência de custos. Exemplo: Se a dGPU local der _Out-Of-Memory_ -> Tenta o Gateway Flat-Rate ilimitado. Se as conexões concorrentes do Flat-Rate estiverem esgotadas -> Qual é o próximo endpoint na fronteira de Pareto considerando nosso orçamento de tokens?
    

Seja pragmático, voltado para engenharia de sistemas em Rust e orquestração financeira. Ignore explicações genéricas sobre o que é uma API. Foque no código lógico e na topologia de custos.