---
aliases:
  - |-
    SODA 
    Framework Anti-SLOP V3 Final
sticker: lucide//arrow-down-circle
---

# Framework Anti-SLOP V3: Engenharia Atômica, Dualidade Sistêmica e Roteamento FinOps (SODA)

Este documento dita o protocolo de **Spec-Driven Development (SDD)** sob o paradigma do "Pessimismo da Razão". Ele enjaula os Agentes de IA, evita o _Vibe Coding_, orquestra o roteamento cognitivo (FinOps) e garante a transição estrita entre o ambiente de desenvolvimento e a compilação do software final.

## 0. A Dualidade Sistêmica (Fronteira de Execução)

O ecossistema reconhece dois territórios com leis imigratórias distintas. A IA deve declarar em qual território está operando antes de gerar a solução:

- **A Fábrica (Antigravity IDE / Dev Env):** Ambiente pragmático de construção. Permite o uso temporário de Python, Node.js, contêineres Docker e chamadas de API externas via MCPs para rodar extrações brutas (ETL), testar TDDs e validar conceitos rápidos.
- **O Produto (Genesis MC / Instalação SODA):** O binário final. **Intolerância Absoluta**. Estritamente Rust (Tokio) no backend e Svelte 5 (Tauri v2) no frontend. Nenhuma dependência não nativa, runtimes pesados ou lógicas de negócios no frontend são permitidas. Ferramentas externas operam unicamente como _Sidecars Efêmeros_ (Sandboxing via Wasmtime/Landlock) que morrem instantaneamente após o uso.

## 1. O Padrão Orchestrator-Worker (Roteamento FinOps)

O padrão Orchestrator-Worker (Brain/Worker) fundamenta o roteamento FinOps da arquitetura SODA, garantindo que o ecossistema seja agnóstico a provedores. **Modelos densos e caros devem ser poupados para o raciocínio puro, enquanto modelos rápidos e baratos devem assumir o trabalho braçal e repetitivo.**

Para estruturar essa separação sem margem para dúvidas, aplica-se a seguinte matriz de decisão via Variáveis de Ambiente:

### 1.1. Modelo `HEAVY` (Ex: Gemini 3.1 Pro, Claude Opus, DeepSeek-R1) [O Arquiteto / Cérebro]

Este é o modelo de alto custo cognitivo. Ele deve ser usado **apenas** quando o problema exige visão de longo prazo, resolução de ambiguidades ou arquitetura de sistemas.

- **Mapeamento de Dependências (DAG):** Para olhar um requisito amplo e fatiá-lo logicamente em nós isolados.
- **Escrita de PRDs:** Para redigir as especificações atômicas, definindo regras de I/O, as "Leis Duras" do SODA e os cenários de falha.
- **Revisão de Arquitetura (Code Review Crítico):** Para auditar se um conjunto de PRDs gerados faz sentido em conjunto, ou atuar na "Alfândega" entre a Fábrica e o Produto.
- **Resolução de Deadlocks:** Se o modelo operário travar em um erro complexo de Rust (ex: conflitos de _Borrow Checker_), a execução é pausada, o contexto é extraído e sobe para o `HEAVY` resolver.

### 1.2. Modelo `FAST` (Ex: Gemini 3.1 Flash, Llama-3 8B, Qwen Local) [O Operário / Executor]

Este é o modelo tático. Ele tem baixa latência e segue instruções rígidas muito bem, mas **não deve tomar decisões arquiteturais**. Ele atua estritamente dentro da "jaula" criada pelo `HEAVY`.

- **Test-Driven Development (TDD):** Ler o PRD e escrever estritamente os testes unitários (_Red_).
- **Codificação Isolada:** Escrever o código mínimo (em Rust ou Svelte 5) para fazer os testes passarem (_Green_), respeitando proibições tóxicas injetadas.
- **Refatoração Micro-Focada:** Ajustar formatação, aplicar linters (clippy) ou isolar lógicas menores dentro de uma função.
- **ETL e Parsing:** Extrair, limpar e formatar dados onde a regra de conversão já é explícita.

### 1.3. A Regra de Ouro do Roteamento (Escalonamento Dinâmico)

Para manter o fluxo contínuo e blindar o orçamento, o Agente Mestre aplica a **Regra dos 3 Erros**:

1. Entregue o PRD ao modelo `FAST` e peça a codificação via TDD.
2. Se o código falhar, devolva o erro do compilador para o `FAST` corrigir.
3. Se o `FAST` falhar **3 vezes consecutivas**, **aborte**.
4. Encaminhe o PRD, o código e o log de erros para o modelo `HEAVY` destrancar o raciocínio. Resolvido o impasse, o controle volta imediatamente para o `FAST`.

### 1.4. A Regra da Delegação Fractal (Micro-FinOps em Sub-partes)

O `HEAVY` é o "Dono" das Fases de raciocínio (0, A, B e D), mas ele atua como um maestro e **nunca deve executar o trabalho braçal de suas próprias fases**. A eficiência de custo exige o acionamento de modelos `FAST` para sub-partes repetitivas ou mecânicas:

- **Mapeamento Mecânico (Fase 0/A):** Se o `HEAVY` precisa auditar a toxicidade de um repositório, ele delega ao `FAST` a tarefa de extrair a Árvore de Sintaxe Abstrata (AST) e limpar os metadados. O `HEAVY` lê apenas o resumo enxuto resultante.
- **Digitação Estruturada (Fase B):** O `HEAVY` projeta a arquitetura de uma _struct_ complexa, mas delega ao `FAST` a geração repetitiva das dezenas de campos, JSONs de configuração ou formatações textuais do PRD.
- **Correção de Linter (Fase D):** Se o `HEAVY` aprova a lógica de um código, mas o compilador alerta sobre formatações (warnings), o `HEAVY` não reescreve o arquivo; ele emite a ordem de correção para o `FAST` limpar o código.

## 2. A Régua Atômica: O Teste de "I/O + 1 Falha"

Se o PRD exige a palavra **"E"** para descrever as ações fundamentais da função, a tarefa está grande demais (Risco de SLOP).

- Toda função deve ter uma **Entrada** restrita (ponteiros/memória), uma **Saída** exata (dados estruturados) e **UM cenário principal de falha** explicitamente mapeado.

## 3. O Fluxo de Execução (As 5 Fases do SDD)

### 🔴 Fase 0: A Auto-Descoberta Tóxica (Devil's Advocate)

- **Ator Principal:** `HEAVY_MODEL` (Delega scraping e resumos ao `FAST_MODEL`)
- **Prompt Mestre:** _"Atue como Advogado do Diabo sob as Leis do SODA Canon. Vamos construir a funcionalidade [X] para o [PRODUTO ou FÁBRICA]. 1) Qual seria a abordagem 'preguiçosa/SLOP' padrão do mercado? 2) Quais dependências ou arquiteturas tóxicas (ex: runtimes interpretados, VDOM) uma IA tentaria empurrar? 3) Formule 3 'Proibições Tóxicas Específicas' inegociáveis para esta funcionalidade."_

### 🟡 Fase A: Mapeamento de Dependências (DAG)

- **Ator Principal:** `HEAVY_MODEL`
- **Prompt Mestre:** _"Considerando as proibições da Fase 0 e o território-alvo, desenhe o Grafo Direcionado de Dependências (DAG) estrito. Não escreva código. Liste apenas os nós, suas linguagens exatas (Rust/Svelte), I/O (Entrada/Saída) e a hierarquia funcional."_

### 🟡 Fase B: Especificação (Os PRDs Atômicos)

- **Ator Principal:** `HEAVY_MODEL` (Delega formatação longa ao `FAST_MODEL`)
- **Prompt Mestre:** _"Transforme o nó [Y] no PRD-001. Defina a assinatura exata e os cenários de falha para testes. **Injete obrigatoriamente as Proibições Tóxicas Específicas mapeadas na Fase 0**."_

### 🟢 Fase C: O TDD Implacável (Execução)

- **Ator Principal:** `FAST_MODEL` (Sujeito à Regra dos 3 Erros)
- **Prompt Mestre:** _"Leia o PRD-001. Passo 1: Escreva apenas os testes unitários (Fail-Fast) focados em memória, latência e I/O. Aguarde aprovação. Passo 2: Escreva o código mínimo (exit code 0) respeitando rigorosamente as proibições tóxicas."_

### 🔵 Fase D: A Alfândega de Purificação (Fábrica -> Produto)

- **Ator Principal:** `HEAVY_MODEL` (Delega micro-refatorações ao `FAST_MODEL`)
- **Prompt Mestre:** _"Revise o código gerado na Fase C. Esse código vai ser embarcado no Produto SODA. Existe algum script Python, Node.js, ou dependência pesada disfarçada aqui? O estado está vazando para a interface (Svelte)? Se sim, instrua o reparo via Rust puro ou encapsule a lógica em um Sidecar Efêmero (Wasmtime). Aprove a passagem apenas se a integridade Bare-Metal for 100% comprovada."_

## 4. Injeção de Leis Duras Gerais (Poda Tóxica SODA)

Independentemente do PRD, estas leis são globais e devem constar na base do contexto:

- **Zero-Copy IPC:** Proibida clonagem de buffers massivos na RAM. Comunicação Rust-Svelte ocorre exclusivamente via mmap, referências e ponteiros.
- **Fail-Fast Barulhento:** Nenhuma função pode engolir erros silenciosamente. Se o Rate Limit estourar ou a VRAM bater no teto, o sistema deve registrar a falha no SQLite atômico e _crashar_ de forma explícita.
- **Frontend Passivo (Zero Lógica):** Svelte 5 atua apenas como lente de renderização mecânica. Nenhuma regra de negócio, roteamento cognitivo ou cálculo deve habitar o Typescript/Javascript.

## 5. Definition of Done (DoD) Matemático

Um PRD só ganha o status `CONCLUIDO` se o `FAST_MODEL` (e, em caso de escalonamento, o `HEAVY_MODEL`) aprovarem o checklist:

- [ ] O código sabe diferenciar se está operando na _Fábrica_ ou no _Produto_?
- [ ] A Fase 0 foi executada e a IA listou antecipadamente as falhas preguiçosas/tóxicas?
- [ ] O código (Green) passa no `cargo clippy` (Rust) sem nenhum warning?
- [ ] O frontend respeita a renderização baseada em Runes do Svelte 5 sem acionar Virtual DOM?
- [ ] Os testes provam tratamento de erro (ex: `Result<T, E>` no Rust) sem panics arbitrários?