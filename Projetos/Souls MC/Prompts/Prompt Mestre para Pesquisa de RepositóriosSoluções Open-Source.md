---
sticker: lucide//at-sign
aliases:
  - Prompt Mestre para Pesquisa de Repositórios/Soluções Open-Source
---
**CONTEXTO DO PROJETO:**  
Estamos "engenheirando" o Genesis Mission Control (SoustractionMC), um Sistema Operacional Agêntico Soberano (SODA) focado em execução local, privacidade absoluta (air-gapped) e eficiência para hardware restrito (i9, 32GB RAM, RTX 2060m com 6GB VRAM limite). A fundação exige um núcleo ultraleve e imutável (compilado em Rust + Tauri) operando como daemon de background. A interface React é estritamente passiva (Canvas-first, design minimalista e utilitário focado em reduzir carga cognitiva 2e/TDAH), comunicando-se via IPC. Repudiamos o Context Rot e a sobrecarga de runtimes interpretados contínuos (Node.js/Python). Agentes são efêmeros, roteados assincronamente e rodam ferramentas em sandboxes estritas (Wasmtime). Nossa filosofia é a "Canibalização Cirúrgica": extrair a alma matemática das ferramentas e descartar o lixo tóxico das dependências.

**INSTRUÇÃO DE EXECUÇÃO (PAINEL DE ESPECIALISTAS):**  
Para dissecar a lista de repositórios abaixo, você não atuará como uma única entidade. Você deve emular um debate interno entre **TRÊS ESPECIALISTAS** com focos distintos:

1. **O Arquiteto Bare-Metal (DevSecOps):** O "Pessimista da Razão". Focado em performance, limites da VRAM, segurança zero-trust e arquitetura Rust/Tauri. Ele odeia dependências pesadas e julga o "Lixo Tóxico".
2. **O Estrategista de Valor e Neuro-UX (Produto):** Focado em utilidade prática, redução de carga cognitiva e "inspirações". Ele olha para a interface, fluxos de uso e pergunta: "Isso ajuda nosso usuário 2e/TDAH? Tem algum componente visual aqui que podemos replicar no nosso Canvas?".
3. **O Engenheiro de Transpilação (O Canibal):** O extrator de valor. Ele ignora a stack ruim e foca na inteligência pura. Ele procura a "alma matemática": heurísticas de prompt, algoritmos de roteamento, expressões regulares, modelos de banco de dados ou fluxogramas lógicos que podemos roubar e reescrever.

**DIRETRIZ DE SAÍDA:**  
Gere um relatório SEM FLOREIOS e SEM VERBOSIDADE. Para cada projeto, apresente uma análise estruturada contendo:

### [Nome do Repositório / Projeto]

- **Score de 'Vale a pena?':** (Um "Score" entre 0 - 10).
- **Originalmente serve para:** (Para adicionar um "resuminho" de qual é a proposta original da solução)

- **Visão do Enxame (Síntese dos 3 Especialistas):** Um parágrafo brutal e direto resumindo o consenso do debate entre o Arquiteto, o Estrategista de UX e o Canibal.
- **Problema Principal que Resolve/Valor de Uso:** (1 ou 2 frases precisas).
- **Stack Tecnológica Base:** (Linguagens dominantes, frameworks, peso de dependências).
- **Tipo de Integração Atual:** (App monolítico? CLI? Framework Web? Biblioteca nativa? ...).
- **Categorias Arquiteturais:** (Motor Low-Level, Interface/Design, Utilitário de Parsing, Agente/Skill, Proxy/Roteamento, ...).
- **Vetores de Risco (O Lixo Tóxico):** (Visão do Arquiteto: exponha dívidas técnicas pesadas, acoplamento a nuvem, uso de Node/Python em background, etc).
- **Ouro Oculto e Inspirações (Visão UX/Produto):** (O que há de genial aqui além do código? Uma mecânica de tela? Uma lógica de negócio? Uma forma diferente de resolver um problema humano?).
- **Ações Mitigadoras (O Plano de Canibalização):** (Como extrair a lógica pura para o SODA? Ex: "Reescrever parser em Rust usando regex puro", "Roubar a paleta de cores para o Tailwind v4", "Ignorar código, absorver apenas heurística do prompt").
- **Devemos Canibalizar? e PQ?:** (Indicação final se devemos trazer para o SoustractionMC, prioridade da extração e os ganhos esperados).

---

**TABELA DE CONSOLIDAÇÃO:**  
No início absoluto da sua resposta (antes das análises individuais), consolide uma Tabela Comparativa contendo as colunas: (Projeto | Score | Categoria | Devemos Canibalizar? | Risco Principal | Ouro/Inspiração a Extrair).

---

**[Lista do Lote X]** _(Inserir links aqui)_