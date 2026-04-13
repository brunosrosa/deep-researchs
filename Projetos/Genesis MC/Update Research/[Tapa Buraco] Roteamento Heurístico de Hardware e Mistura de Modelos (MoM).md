---
sticker: lucide//cloud-lightning
---
🔍 PROMPT DE DEEP RESEARCH 3: Roteamento Heurístico de Hardware e Mistura de Modelos (MoM)

**[COPIE A PARTIR DAQUI]**

ATUE COMO UM ARQUITETO DE INFRAESTRUTURA MLOPS, ENGENHEIRO DE EDGE COMPUTING E PESQUISADOR (FOCO NO ESTADO DA ARTE DE 2025/2026).

**CONTEXTO MESTRE (PROJETO GENESIS MC - SODA):** Estamos desenvolvendo o SODA (Sovereign Operating Data Architecture), um ecossistema agêntico local. Nosso hardware é estrito e assimétrico:

- CPU Intel i9 (32GB RAM) - Usada para roteamento ultra-rápido, tarefas triviais e modelos sub-2B.dGPU NVIDIA RTX 2060m (Teto de 6GB VRAM) - Usada para raciocínio pesado isolado (DeepSeek-R1-Distill) e codificação.Nuvem (Subscription Hacking via MCP) - Usada via Gemini CLI / Claude Code apenas para contextos massivos que excedam o hardware físico.

O primeiro passo antes do SODA executar qualquer tarefa é passar pelo Agente Roteador. Este agente deve decidir instantaneamente para qual destes 3 "motores" a tarefa será enviada.

**O PROBLEMA A SER RESOLVIDO (FADIGA DE ROTEAMENTO E OUT-OF-MEMORY):** Se o roteamento for baseado apenas em regras estáticas ("se tiver a palavra 'código', mande para a GPU"), o sistema falhará. Precisamos de um roteamento dinâmico baseado no paradigma "Mixture of Models" (MoM) e na "Entropia Semântica", onde o sistema calcula a confiança, a complexidade e o tamanho do contexto do _prompt_ em tempo real (em milissegundos) para despachar a carga para o hardware correto, evitando erros de _Out-Of-Memory_ (OOM) na placa de vídeo e desperdício de cota na nuvem.

**A MISSÃO DE PESQUISA:** Realize uma pesquisa aprofundada na literatura científica e em gateways pragmáticos de 2025/2026 (como Semantic Router, RouteLLM, TensorZero, Unify) para descobrir a arquitetura definitiva de Roteamento Heurístico para sistemas _Edge_ / Locais.

Entregue um relatório técnico focado nos seguintes pontos:

- **O Motor de Classificação (Zero-Shot Routing):** Como a vanguarda utiliza classificadores minúsculos (rodando puramente na CPU) baseados em Embeddings e _K-Nearest Neighbors_ (KNN) para classificar a intenção e a complexidade do _prompt_ em menos de 50ms, sem invocar LLMs gerativos pesados?- **Entropia Semântica e Confiança:** Como calcular matematicamente a "Incerteza" de uma tarefa? Como o roteador sabe que um modelo de 4B na GPU vai alucinar (alta entropia) e que a tarefa deve ser escalada obrigatoriamente para a Nuvem (Fallback em Cascata)?- **O Algoritmo de Decisão SODA:** Construa a árvore de decisão lógica (FSM) que o nosso roteador em Rust deverá seguir. Quais são as variáveis de entrada (ex: _Token Count_, _Intent Threshold_, _VRAM Disponível_) e as regras exatas de corte para decidir entre: CPU, dGPU ou Nuvem?

**FORMATO DE SAÍDA EXIGIDO:** Gere um documento analítico e arquitetural. Sem código excessivo em Python. Quero o diagrama lógico do roteador (em texto ou Mermaid), as fórmulas de heurística recomendadas e as regras de _prompting_ ou configuração estrutural que formarão a nossa Super Skill de roteamento (`@soda-router-architect`). O objetivo é garantir uma orquestração de hardware impecável e livre de gargalos.

**[FIM DA CÓPIA]**