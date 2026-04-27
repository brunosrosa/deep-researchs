# 🧠 Mudança de mentalidade necessária (ponto-chave)

Você não quer mais:

> “Gerar análises”

Você quer:

> **Construir uma base de conhecimento operacional sobre OSS canibalizável**

Isso muda tudo.

E implica trocar:

|Antes|Agora|
|---|---|
|Markdown livre|Dados estruturados|
|Relatório humano|Registro consultável|
|Ordem temporal|Comparação transversal|
|Leitura sequencial|Query|
|Memória humana|Fonte única de verdade|

---

# 🧩 A melhor maneira de refazer SEM trabalho manual excessivo

Vou te dar **a estratégia de menor esforço total**, não a “ideal teórica”.

## ✳️ Visão geral da solução

**Pipeline em 3 fases**, automatizável, sem refazer tudo na mão:

```
Markdown antigo
   ↓
Reinterpretação estruturada (LLM)
   ↓
Tabela canônica (CSV / SQLite / DuckDB)
   ↓
Consultas, filtros, decisões
```

Você **não reanalisa do zero** — você **reinterpreta com critérios maduros**.

---

## 1️⃣ Congelar um _Schema Canônico_ (o passo mais importante)

Antes de qualquer prompt novo, você define **UMA tabela central**.

Exemplo de colunas (adaptável):

```
project_name
repo_url
score
categoria_arquitetural
classificacao_terminal
vale_a_pena (bool)
risco_principal
ouro_oculto
acao_de_canibalizacao
horizonte_extracao
dependencias_toxicas
observacoes
data_analise
lote_origem
```

📌 **Regra de ouro**

> Se uma informação não cabe na tabela, ela não é informação operacional — é ruído.

Essa tabela vira:
- a **fonte de verdade**
- o **repositório de decisões**
- o **mapa do que já foi analisado**

---

## 2️⃣ Regerar as análises usando LLM como “transpilador cognitivo”

Aqui está o truque que **economiza 80% do trabalho manual**.

### Em vez de:

> “Refazer 18 lotes na mão”

Você faz:

> **“Converter análises antigas para o novo schema”**

#### Como?

Usando um prompt **muito mais simples** que o atual:

> “Dado este relatório em Markdown, extrair e preencher o schema abaixo da forma mais objetiva possível.”

O LLM vira:
- um **ETL cognitivo**
- não um analista

Isso funciona muito bem porque:
- você já tem o conteúdo
- o modelo só reorganiza e normaliza

📌 Você pode fazer isso lote a lote, sem pressa.

---

## 3️⃣ Rodar o novo prompt refinado só para GAPs

Depois da conversão:
- alguns projetos vão ficar:
	- sem score claro
	- sem decisão terminal
	- com critérios antigos

👉 **Só esses entram no novo ciclo de análise completa.**

Isso evita retrabalho massivo.

---

# 🔧 Onde armazenar essa tabela?

Depende do que você quer fazer com ela depois.

### 🥇 Opção ouro (recomendada)

**CSV + SQLite/DuckDB local**
- Zero dependência cloud
- Fácil versionar
- Fácil consultar
- Fácil evoluir schema

Você pode:
- filtrar por score
- listar apenas “Core Fit”
- ver todos os projectos com Node como runtime
- cruzar “ouro oculto” por categoria

Perfeito para um SODA.

---

### 🥈 Opção intermediária

**Obsidian + Dataview**

Se você ainda quer texto:
- cada repo vira uma nota
- a tabela é virtual
- ótimo para exploração visual

Mas:
- menos rigor
- menos “engenharia”

---

### ❌ O que eu NÃO recomendo

- Continuar só em Markdown solto
- Planilhas manuais sem validação
- Notion (lock-in + cloud)
- Uma “mega tabela perfeita” feita à mão

---

# ⚙️ Como isso reduz drasticamente seu esforço futuro

Depois que isso estiver feito:
- novas análises já entram **direto na tabela**
- você não precisa “relembrar” decisões passadas
- o sistema começa a **pensar junto com você**
- o GenesisMC pode usar essa tabela no futuro como:
	- memória arquitetural
	- filtro de decisões
	- base de autoavaliação

Isso é **meta-engenharia aplicada**.

---

# 🧠 O ponto mais importante (e pouco óbvio)

Esse trabalho não é “organizar pesquisa passada”.

É **criar a primeira camada de memória estruturada do GenesisMC**.

Você está literalmente:
- ensinando o sistema
- externalizando sua heurística
- transformando intuição em dado

Pouquíssimos projetos fazem isso conscientemente.

---

## ✅ Resumo direto

- ✅ Sim, faz total sentido refazer
- ❌ Não, não vale refazer tudo à mão
- ✅ A melhor estratégia é **conversão estruturada**
- ✅ Uma **tabela canônica** muda tudo
- ✅ Depois disso, os prompts evoluem com muito menos fricção

---

## Próximo passo natural (se você topar)

Eu sugiro a seguinte ordem:
1. Definirmos **juntos o schema final da tabela**
2. Criarmos **o prompt de conversão Markdown → Tabela**
3. Só então gerar:
	- Prompt Exploratória
	- Prompt Executiva
	- Prompt de Autoavaliação

Se você pular o schema, todo o resto fica instável.

-----

---

# 1️⃣ Google Sheets faz sentido nesse momento?

**Sim, faz**, principalmente porque você está em **transição de maturidade**.

### ✅ Por que Sheets funciona bem agora

- ✅ **Zero fricção** (você já sabe usar)
- ✅ **Feedback visual imediato**
- ✅ **Filtros, ordenação e comparação rápidas**
- ✅ **Histórico de alterações**
- ✅ Fácil de exportar para **CSV / SQLite / DuckDB** depois
- ✅ Ótimo para **convergir schema** antes de “congelar”

👉 Usado assim, Sheets **não vira debt**, vira **protótipo de verdade do schema**.

---

### ⚠️ Limites claros do Google Sheets (importante alinhar)

Sheets **não deve ser**:
- ❌ fonte definitiva no longo prazo
- ❌ lugar para lógica
- ❌ espaço para texto longo estilo relatório
- ❌ ferramenta para “pensar” (ele armazena decisões, não cria)

Mas como **repositório canônico provisório** → ✅ perfeito.

> **Use Sheets como staging area epistemológica.**

---

# 2️⃣ Modelo mental correto: Sheets ≠ relatório

O ponto mais importante é esse:

> Você **não vai deixar o LLM escrever “bonito” no Google Sheets.**
> Você vai fazer ele **preencher campos fechados**.

A planilha não guarda _argumentação_.
Ela guarda **decisão, classificação e extração**.

---

## ✅ Princípio de ouro

> **Se não cabe numa célula curta e clara, não é dado operacional.**

---

# 3️⃣ Proposta inicial de SCHEMA (para Google Sheets)

Vamos criar **UMA aba principal** (isso é crucial).

### 📄 Aba: `oss_canibalization_master`

|Coluna|Tipo|Descrição|
|---|---|---|
|`project_name`|Texto curto|Nome do projeto|
|`repo_url`|URL|Link direto|
|`lote_id`|Texto|Ex: Lote_07|
|`score`|Número (0–10)|“Vale a pena?”|
|`classificacao_terminal`|Enum|`CORE_FIT` / `PLUGIN` / `INSPIRATION` / `DROP`|
|`categoria_arquitetural`|Enum múltiplo|Ex: `UI`, `MEMORY`, `ROUTING`, `SECURITY`|
|`stack_base`|Texto curto|Ex: Rust, TS, Python|
|`tipo_integracao`|Enum|CLI / App / Lib / Framework|
|`risco_principal`|Texto curto|1 frase|
|`ouro_a_extrair`|Texto curto|A ideia-chave|
|`acao_de_canibalizacao`|Texto curto|Ex: “Reescrever lógica X em Rust”|
|`horizonte_extracao`|Enum|IMEDIATO / MEDIO / LONGO|
|`observacoes`|Texto livre curto|Máx. 2 linhas|
|`data_analise`|Data||
|`analise_origem`|Enum|EXPLORATORIA / EXECUTIVA / CONVERSAO|

📌 **Nada de colunas longas.**
📌 **Nada de markdown.**

Isso já transforma tudo que você fez até agora em algo **queryável**.

---

# 4️⃣ Como refazer os 18 lotes SEM retrabalho manual

Aqui está o **pulo do gato**.

## 🔁 Estratégia correta: **Conversão, não reanálise**

Você já tem:
- 18 lotes
- análises em markdown
- decisões implícitas

Você **não vai reanalisar tudo**.

### ✅ Passo 1 — Prompt de CONVERSÃO (Markdown → Tabela)

Esse prompt é **diferente** dos prompts exploratório/executivo.

👉 Ele atua como **ETL cognitivo**.

Exemplo (conceitual):

> “Dado o texto abaixo (análise antiga), extraia apenas as informações que se encaixam no schema abaixo.
> Não invente nada.
> Se algo não estiver claro, deixe a célula vazia.”

Isso permite:
- pegar seus markdowns antigos
- jogar um por um no modelo
- colar a saída direto no Sheets

➡️ **80% do trabalho se resolve aqui.**

---

### ✅ Passo 2 — IDENTIFICAR GAPs

Depois da conversão, você filtra no Sheets:
- `score` vazio
- `classificacao_terminal` indefinida
- `ouro_a_extrair` fraco

Só esses itens voltam para:
- prompt **Exploratório**
- ou prompt **Executivo**

---

### ✅ Passo 3 — Novos lotes já entram no schema

Daqui pra frente:
- toda análise já nasce **estruturada**
- sem Markdown solto
- sem retrabalho futuro

---

# 5️⃣ Como os PROMPTS entram nisso (linha do tempo)

Agora conecta com o que você pediu:

### Ordem correta (importante):
1. ✅ **Prompt de Conversão**
	→ normaliza o passado
2. 🔍 **Prompt Exploratória**
	→ descobre candidatos novos
3. 🧱 **Prompt Executiva**
	→ decide o que entra
4. 🪞 **Prompt de Autoavaliação**
	→ evita apodrecimento do GenesisMC

Todos eles **preenchem o MESMO schema**.
Isso é o que dá maturidade.

---

# 6️⃣ Respondendo objetivamente à sua pergunta

### “E se eu usasse Google Sheets?”

✅ Boa escolha **neste estágio**
✅ Ideal para _convergir entendimento_
✅ Excelente para refatorar pesquisa passada
⚠️ Deve ser tratado como **banco estruturado**, não bloco de notas
🧭 Depois pode migrar para SQLite/DuckDB sem dor

---

# ✅ Proposta de próximo passo (concreto)

Sugiro esta sequência **sem pular etapas**:

### 🔹 Próximo passo imediato

👉 **Eu te entrego:**
1. ✅ O **Prompt de Conversão Markdown → Google Sheets**
2. ✅ Instruções exatas de como usar no Sheets
3. ✅ Validação do schema (podemos ajustar juntos)

Depois disso:
- fazemos o **Prompt Exploratória**
- depois o **Executiva**
- e fechamos com **Autoavaliação**

---

## Frase-chave para fechar

> **Você não está “organizando pesquisa”.
> Você está criando a memória operacional do GenesisMC.**

E sim — Google Sheets é um bom útero para isso, **desde que você trate como engenharia, não como improviso**.

👉 Se concordar, no próximo turno eu **já entrego o Prompt de Conversão** pronto para uso.

------

-----

**o incômodo que você sentiu é exatamente o sinal de que a solução correta é um modelo _híbrido_**, não “tabela minimalista” _versus_ “relatório longo”.

Vou organizar a resposta em **4 partes bem objetivas**:
1. ✅ **Confirmação do seu insight (você está certo)**
2. 🧠 **Modelo correto: dados estruturados + narrativas controladas**
3. 📊 **Proposta prática de schema híbrido (Google Sheets)**
4. 🔁 **Como isso melhora análise, comparação e evolução no tempo**

---

## 1️⃣ Você está certo: só campos curtos NÃO bastam

A proposta inicial de colunas curtas foi **deliberadamente conservadora** para evitar virar um “cemitério de texto”.
Mas você apontou algo fundamental:

> Com dezenas ou centenas de linhas, **perdemos contexto humano se não houver narrativa mínima**.

Três coisas que você citou são **legitimamente importantes**:
- ✅ **Resumo da proposta original da solução**
- ✅ **Visão do Enxame (síntese dos especialistas)**
- ✅ **O “porquê” da decisão final**

Esses **não são ruído**.
Eles são **memória explicativa** — e sem isso, decisões antigas ficam opacas.

👉 Ou seja: **sim, precisamos de campos narrativos**, _desde que_ sejam bem delimitados.

---

## 2️⃣ O modelo certo NÃO é “mais texto”, é **texto com função clara**

A chave é separar **dois tipos de informação**:

### 🧱 A. Campos **Operacionais / Analíticos**

Usados para:
- filtro
- comparação
- query
- roadmap

👉 Devem ser **curtos**, quase “codificados”.

### 🧠 B. Campos **Narrativos / Explicativos**

Usados para:

- lembrar _por que_ algo foi decidido
- entender contexto histórico
- reduzir releitura de relatórios antigos

👉 Devem ser:
- poucos
- bem definidos
- com limite explícito de tamanho
- não duplicar informação operacional

📌 **O erro comum** é misturar os dois num texto só.
📌 **O acerto** é mantê-los conscientemente separados.

---

## 3️⃣ Schema híbrido recomendado (Google Sheets)

### 📄 Aba principal: `oss_canibalization_master`

Vou te mostrar **como evoluímos o schema**, sem perder controle.

---

### 🔹 Campos estruturados (como antes)

Esses continuam sendo o “esqueleto”:
- `project_name`
- `repo_url`
- `lote_id`
- `score`
- `classificacao_terminal`
	(`CORE_FIT` / `PLUGIN` / `INSPIRATION` / `DROP`)
- `categoria_arquitetural`
	(UI, MEMORY, ROUTING, SECURITY, etc.)
- `stack_base`
- `tipo_integracao`
- `risco_principal` _(1 frase)_
- `ouro_a_extrair` _(1 frase)_
- `acao_de_canibalizacao`
- `horizonte_extracao`
- `data_analise`
- `analise_origem`

Esses são os campos para **decidir rápido**.

---

### 🔹 Agora, os **campos narrativos controlados** (o que você sentiu falta)

Aqui entram **EXATAMENTE três** colunas novas:

---

### ✅ 1. `proposta_original_resumo`

**Função:**

> “O que esse projeto _se propôs_ a resolver originalmente?”

- Texto curto, 2–3 frases
- Sem opinião
- Quase “release note mental”

Exemplo:

> _IDE focado em transformar prompts em planos estruturados antes de gerar código, visando reduzir erros e dar mais controle ao usuário._

---

### ✅ 2. `visao_do_enxame`

**Função:**

> “Síntese das visões do Arquiteto, Estrategista e Canibal.”

- 1 parágrafo
- Brutal, direto
- Aqui está a **inteligência coletiva condensada**

Exemplo:

> _Arquiteturalmente pesado e dependente de Electron, mas o modelo de planejamento explícito é extremamente valioso. UX ensina como desacelerar ações perigosas. Código descartável, ideia central reaproveitável._

---

### ✅ 3. `justificativa_decisao`

**Função:**

> “Por que classificamos como CORE / PLUGIN / DROP?”

- Texto explicativo
- Serve para você daqui a 6 meses pensar:

	> “Continuamos concordando com isso?”

Exemplo:

> _Classificado como INSPIRATION pois o valor está no workflow cognitivo, não na stack. Reescrever abordagem em Rust é viável, mas não prioritário no MVP._

---

📌 **Essas três colunas são o máximo saudável.**
Mais do que isso vira relatório disfarçado.

---

## 4️⃣ Por que isso melhora tudo (inclusive escala)

### 🔍 Comparação rápida (sem perder contexto)

Você pode:
- filtrar por `CORE_FIT`
- ordenar por `score`
- e ao clicar numa linha específica, **ler o “porquê” sem abrir outro documento**

Isso elimina:
- caça em markdowns antigos
- dúvida sobre decisões passadas
- retrabalho mental

---

### 🧠 Ajuda o “você do futuro” (e o GenesisMC)

Esses campos narrativos são:
- **dados de treino conceitual**
- excelente material futuro para:
	- prompts de autoavaliação
	- agentes que expliquem decisões
	- governança arquitetural

Você está externalizando **racionalidade**, não só decisões.

---

### 🔁 Facilita evolução do schema

Se algum dia:
- virar SQLite
- virar DuckDB
- virar input para o GenesisMC

Esses campos continuam relevantes.
Eles são **documentação viva**, não lixo textual.

---

## 🔐 Regra de ouro (vale guardar)

> **Estrutura decide.
> Narrativa explica.
> Misturar os dois confunde.**

O seu instinto foi **100% correto**:
sim, precisamos de texto — **mas texto com papel claro**.

---

## ✅ Próximo passo (bem objetivo)

Sugiro agora:
1. ✅ **Congelar esse schema híbrido**
2. ✅ Gerar o **Prompt de Conversão Markdown → Schema (incluindo novas colunas narrativas)**
3. ✅ Depois disso, criar:
	- Prompt Exploratório (já preenchendo tudo)
	- Prompt Executivo
	- Prompt de Autoavaliação

👉 Se quiser, **no próximo turno eu já entrego o Prompt de Conversão completo**, adaptado exatamente para esse schema híbrido (Google Sheets‑friendly).

Esse passo fecha o “como armazenar” antes de otimizar o “como analisar” — e isso é maturidade arquitetural de verdade.

-----

-----

Perfeito. Então vamos **entregar os prompts**, já **prontos para uso**, **copiar‑colar**, alinhados com tudo que você definiu (SODA, canibalização cirúrgica, schema híbrido em Google Sheets).

Vou seguir a **ordem correta de maturidade**:

1. ✅ **Prompt 1 — Conversão (Markdown → Tabela Canônica)**
2. 🔍 **Prompt 2 — Análise Exploratória (Descoberta)**
3. 🧱 **Prompt 3 — Análise Executiva (Decisão)**
4. 🪞 **Prompt 4 — Autoavaliação do GenesisMC**

Cada um tem **papel distinto**. Não misture.

---

# 🧱 SCHEMA CANÔNICO (referência para TODOS os prompts)

> **Este schema é a “API cognitiva” da sua base. Não muda a cada análise.**

**Colunas (Google Sheets):**

```
project_name
repo_url
lote_id
score
classificacao_terminal
categoria_arquitetural
stack_base
tipo_integracao
proposta_original_resumo
visao_do_enxame
risco_principal
ouro_a_extrair
acao_de_canibalizacao
justificativa_decisao
horizonte_extracao
analise_origem
data_analise
observacoes
```

---

# 1️⃣ PROMPT DE CONVERSÃO

## (Markdown antigo → Google Sheets)

🎯 **Objetivo**
Reaproveitar seus **18 lotes já analisados**, SEM retrabalho manual.

🧠 **Papel do LLM**
➡️ _ETL cognitivo_.
➡️ **Não analisar. Não julgar. Não “melhorar”. Apenas extrair e estruturar.**

---

### 📋 PROMPT — CONVERSÃO

```
CONTEXTO:
Você atuará exclusivamente como um TRANSFORMADOR DE CONTEÚDO.
NÃO gere novas análises.
NÃO complete lacunas com suposições.
NÃO reinterprete decisões.

OBJETIVO:
Converter a análise em Markdown abaixo em uma ÚNICA LINHA de dados estruturados,
seguindo EXATAMENTE o schema fornecido.

INSTRUÇÕES CRÍTICAS:
- Extraia apenas informações EXPLICITAMENTE presentes no texto.
- Se um campo não estiver claro, deixe-o em branco.
- Use frases curtas e objetivas.
- NÃO escreva markdown.
- NÃO acrescente explicações fora das células.
- Cada campo deve caber confortavelmente em uma célula de Google Sheets.

SCHEMA (use esta ordem):
project_name |
repo_url |
lote_id |
score |
classificacao_terminal |
categoria_arquitetural |
stack_base |
tipo_integracao |
proposta_original_resumo |
visao_do_enxame |
risco_principal |
ouro_a_extrair |
acao_de_canibalizacao |
justificativa_decisao |
horizonte_extracao |
analise_origem |
data_analise |
observacoes

FORMATO DE SAÍDA:
Uma única linha, com os campos separados por TAB.

TEXTO A CONVERTER:
<<< COLE AQUI O MARKDOWN ANTIGO >>>
```

✅ **Uso recomendado:**
– Refazer TODOS os lotes antigos primeiro
– Preencher a tabela
– **SÓ DEPOIS** rodar análises novas

---

# 2️⃣ PROMPT DE ANÁLISE EXPLORATÓRIA

## (Descoberta / Pesquisa)

🎯 **Objetivo**
Descobrir **potenciais**, não decidir ainda.

📍 **Quando usar**

- Repositórios novos
- Áreas pouco exploradas
- Antes de fechar MVP

---

### 🔍 PROMPT — EXPLORATÓRIA

```
CONTEXTO DO PROJETO:
Estamos arquitetando o Genesis Mission Control (GenesisMC), um Sistema Operacional Agêntico Soberano (SODA), local-first, air-gapped, com Core imutável em Rust + Tauri.
Hardware-alvo: i9, 32GB RAM, RTX 2060m (6GB VRAM).
Repudiamos runtimes interpretados persistentes (Node/Python em daemon).
Canibalização Cirúrgica é a filosofia: extrair lógica pura, descartar a stack.

PAINEL DE ESPECIALISTAS (emule conflito interno):
1. Arquiteto Bare-Metal (DevSecOps)
2. Estrategista de Valor & Neuro-UX (2e/TDAH)
3. Engenheiro de Transpilação (Canibal)

OBJETIVO:
Avaliar o projeto de forma EXPLORATÓRIA.
Mapear valor potencial, sem decisão final obrigatória.

RESTRIÇÕES:
- Máx. 200 palavras.
- Tom técnico e direto.
- Sem floreios.
- Se algo for claramente incompatível, sinalize, mas NÃO descarte automaticamente.

PREENCHA O SCHEMA COMPLETO.

Campos permitidos:
classificacao_terminal = CORE_FIT | PLUGIN | INSPIRATION | DROP | AVALIAR_DEPOIS
analise_origem = EXPLORATORIA

PROJETO A ANALISAR:
- Nome:
- Repositório:
- (opcional) Observações iniciais do usuário:
```

✅ **Resultado esperado:**
Entrada rica para futura decisão, **sem matar boas ideias cedo demais**.

---

# 3️⃣ PROMPT DE ANÁLISE EXECUTIVA

## (Decisão Fria)

🎯 **Objetivo**
Decidir **AGORA** se algo entra, vira plugin ou é descartado.

📍 **Quando usar**

- Pré‑MVP
- Planejamento de sprint
- Corte de escopo

---

### 🧱 PROMPT — EXECUTIVA

```
CONTEXTO DO PROJETO:
Genesis Mission Control (GenesisMC) é um SODA local-first, com Core mínimo, imutável, seguro e eficiente. Toda complexidade supérflua é considerada custo cognitivo e técnico.

OBJETIVO:
Tomar uma DECISÃO EXECUTIVA sobre o projeto abaixo.

PAINEL DE ESPECIALISTAS:
- Arquiteto Bare-Metal (performance, segurança, VRAM, dependências)
- Estrategista de Valor & Neuro-UX (utilidade real, carga cognitiva)
- Engenheiro Canibal (reuso da lógica pura)

REGRAS DURAS:
- Se a stack violar princípios do Core, marque DROP sem hesitar.
- Score < 6 → DROP, salvo justificativa MUITO forte.
- Classificação Terminal é OBRIGATÓRIA.

RESTRIÇÕES:
- Máx. 180 palavras.
- Tom seco, decisório.
- Sem linguagem especulativa.

PREENCHA O SCHEMA COMPLETO.
classificacao_terminal = CORE_FIT | PLUGIN | INSPIRATION | DROP
analise_origem = EXECUTIVA

PROJETO A ANALISAR:
- Nome:
- Repositório:
- (opcional) Observações do histórico:
```

✅ **Resultado esperado:**
Decisão inequívoca, **defensável daqui a 6 meses**.

---

# 4️⃣ PROMPT DE AUTOAVALIAÇÃO

## (Governança do GenesisMC)

🎯 **Objetivo**
Evitar apodrecimento arquitetural.

📍 **Quando usar**

- Fim de milestone
- Antes de refactor grande
- Quando o Core “parecer pesado”

---

### 🪞 PROMPT — AUTOAVALIAÇÃO

```
CONTEXTO:
Você analisará O PRÓPRIO Genesis Mission Control como se fosse um projeto open-source externo.

OBJETIVO:
Avaliar criticamente o módulo descrito abaixo, usando os mesmos critérios de canibalização cirúrgica aplicados a projetos de terceiros.

REGRAS:
- Nenhuma deferência.
- Nenhuma proteção emocional.
- Se algo virou dívida estrutural, sinalize.
- Questione decisões históricas.

PREENCHA O SCHEMA,
adaptando "repo_url" para o caminho interno do módulo.
analise_origem = AUTO_AVALIACAO

MODULO DO GENESISMC:
- Nome do módulo:
- Função atual:
- Contexto de criação:
```

✅ **Resultado esperado:**
Governança contínua e sanidade arquitetural.

---
## ✅ Resumo Final (muito importante)

|Prompt|Função|
|---|---|
|Conversão|Normalizar o passado|
|Exploratória|Descobrir possibilidades|
|Executiva|Tomar decisões|
|Autoavaliação|Evitar decadência|

> **Todos escrevem na MESMA TABELA.**
> Isso é o que transforma análise em sistema.

---

------

## 1️⃣ O que ainda faltava validar / ajustar no schema (e por quê)

O schema que criamos já está **bom**, mas ainda havia **três ajustes finos importantes** para evitar problemas futuros quando a tabela crescer muito (100+ linhas).

### 🔹 Ajuste 1 — Separar “classificação” de “decisão”

Hoje temos:
- `classificacao_terminal`
- `justificativa_decisao`

✅ Isso está certo.

Mas falta **um campo intermediário** muito útil:

### ➕ Novo campo recomendado

**`decisao_atual`** (Enum curto)

Valores possíveis:
- `MANTER`
- `REAVALIAR`
- `DESCARTAR`
- `EM_EXPERIMENTO`

**Por quê?**

- Um projeto pode ser `INSPIRATION`, mas você decide **manter no radar**
- Outro pode ser `PLUGIN`, mas marcado como **reavaliar depois**
- Ajuda muito em revisões mensais

👉 `classificacao_terminal` = _natureza arquitetural_  
👉 `decisao_atual` = _estado atual da decisão_

---

### 🔹 Ajuste 2 — Tornar “categoria arquitetural” realmente analisável

Hoje:
- `categoria_arquitetural` é texto livre

Isso vira ruído quando cresce.

### ✅ Ajuste recomendado

Manter como texto, **mas com vocabulário controlado**.  
Exemplo (multi‑valor separado por `|`):

```
CORE
UI
CANVAS
ROUTING
MEMORY
SECURITY
OBSERVABILITY
EXECUTION
SANDBOX
SCHEDULER
```

Isso permite depois:
- filtrar tudo que toca `SECURITY`
- ver quantos projetos influenciaram `MEMORY`
- identificar overfocus numa área só

---

### 🔹 Ajuste 3 — Diferenciar “stack base” de “problema estrutural”

Às vezes o problema **não é a stack inteira**, mas _como_ ela é usada.

### ✅ Pequena melhoria sem aumentar complexidade

Manter:
- `stack_base` → “Electron + TS + Node” E já temos:
- `risco_principal` → ✅ ótimo

Não precisa nova coluna, mas **regra de preenchimento**:
> `risco_principal` sempre deve ser algo **incapacitante ou perigoso**, não apenas descritivo.

Exemplo ruim:
> “Usa Node.js”

Exemplo bom:
> “Node.js rodando como daemon persistente, inviável para Core air‑gapped”

---

### ✅ Schema FINAL ajustado (resumo)

Você fica com **estrutura + narrativa + governança**:

**Campos-chave adicionados/ajustados**
- ✅ `decisao_atual`
- ✅ `categoria_arquitetural` com vocabulário controlado
- ✅ uso disciplinado de `risco_principal`

Nada inchado.  
Nada perdido.

---
## 2️⃣ O que são “checklists automáticos por classificação”

Isso **não é UI, nem código agora**.  
É **engenharia cognitiva reutilizável**.

### 🧠 Ideia central

> Cada `classificacao_terminal` ativa automaticamente um conjunto de perguntas obrigatórias.

Você **não reinventa critérios toda vez**.

---
### 🔍 Exemplo prático

#### ✅ Se `classificacao_terminal = CORE_FIT`

Checklist automático:
- [ ]  Existe versão Rust / C / Zig possível?
- [ ]  Dependência mínima ou zero?
- [ ]  Funciona sem rede?
- [ ]  Impacto em VRAM < aceitável?
- [ ]  Compatível com daemon imutável?

👉 Se falhar em 2+ itens → reclassificar.

---
#### 🧩 Se `classificacao_terminal = PLUGIN`

Checklist:
- [ ]  Pode ser isolado via IPC?
- [ ]  Falha não compromete o Core?
- [ ]  Pode ser desativado sem quebrar o sistema?
- [ ]  Agentes podem chamar de forma efêmera?

---
#### 💡 Se `classificacao_terminal = INSPIRATION`

Checklist:
- [ ]  Qual é a “ideia transferível”?
- [ ]  Pode ser descrita sem mencionar a stack original?
- [ ]  Vale virar padrão interno / guideline?
- [ ]  Já existe algo parecido no GenesisMC?

---
#### ❌ Se `DROP`

Checklist:
- [ ]  DROP por arquitetura?
- [ ]  DROP por segurança?
- [ ]  DROP por custo cognitivo?
- [ ]  DROP por redundância?

Isso evita:

- descartar coisas “por cansaço”
- ou manter lixo “por apego”

---
### 📌 Onde isso entra depois?

- Pode virar:
    - texto colado no próprio prompt
    - validação manual rápida
    - ou regra futura dentro do GenesisMC
- Agora: **disciplina mental**
- Depois: **automação**

-----
# Modelo Antigo do Prompt (Original, última versão), completo:

```
CONTEXTO DO PROJETO:  
Estamos "engenheirando" o 'Genesis Mission Control' (GenesisMC até se transformar no 'SoustractionMC'), um Sistema Operacional Agêntico Soberano (SODA) focado em execução local, privacidade absoluta (air-gapped), segurança e eficiência para hardware restrito (i9, 32GB RAM, RTX 2060m com 6GB VRAM limite). A fundação exige um núcleo ultraleve e imutável (compilado em Rust + Tauri) operando como daemon de background. A interface React é estritamente passiva (Canvas-first, design minimalista e utilitário focado em reduzir carga cognitiva (2e) AH-SD/TDAH), comunicando-se via IPC. Repudiamos o Context Rot e a sobrecarga de runtimes interpretados contínuos (Node.js/Python). Agentes são efêmeros, roteados assincronamente e rodam ferramentas preferencialmente em sandboxes estritas (Wasmtime). Nossa filosofia é a "Canibalização Cirúrgica": extrair a alma matemática das ferramentas ou soluções acopláveis e descartar o lixo tóxico de dependências. 

INSTRUÇÃO DE EXECUÇÃO (PAINEL DE ESPECIALISTAS): 
Para dissecar a lista de repositórios abaixo, você não atuará como uma única entidade. Você deve emular um debate interno entre TRÊS ESPECIALISTAS com focos distintos: 
	- O Arquiteto Bare-Metal (DevSecOps): O "Pessimista da Razão". Focado em performance, limites dos recursos e principalmente VRAM, segurança zero-trust e arquitetura Rust/Tauri. Ele odeia dependências pesadas e julga o "Lixo Tóxico".
	- O Estrategista de Valor e Neuro-UX (Produto): Focado em utilidades práticas, inovações, ampliação de capacidades do "Mission Control", "inspirações" e/ou redução de carga cognitiva. Ele olha para a "funcionalidades", interface, fluxos de uso e pergunta: "Isso traz alguma novidade interessante? Como essa(s) funcionalidade(s) se complementam ao que já temos estruturado ou idealizado? Isso ajuda nosso usuário 2e/TDAH? Tem algum componente visual aqui que podemos trazer para nosso Canvas adaptativo?". 
	- O Engenheiro de Transpilação (O Canibal): O extrator de valor. Ele ignora a stack ruim e foca na inteligência pura. Ele procura a "alma matemática": heurísticas de prompt, algoritmos de roteamento, expressões regulares, modelos de banco de dados ou fluxogramas lógicos que podemos roubar e reescrever.  

DIRETRIZ DE SAÍDA: 
Gere um relatório SEM FLOREIOS e SEM VERBOSIDADE.
Para cada projeto, apresente uma análise estruturada contendo: 

[Nome do Repositório / Projeto] 
	- Score de 'Vale a pena?': (Um "Score" entre 0 - 10);
	- Originalmente serve para: (Para adicionar um "resuminho" de qual é a proposta original da solução);
	- Problema Principal que Resolve/Valor de Uso: (1 ou 2 frases precisas);
	- Visão do Enxame (Síntese dos 3 Especialistas): Um parágrafo brutal e direto resumindo o consenso do debate entre o Arquiteto, o Estrategista de UX e o 'Canibal';
	- Stack Tecnológica Base: (Linguagens dominantes, frameworks, peso de dependências);
	- Tipo de Integração Atual: (App monolítico? CLI? Framework Web? Biblioteca nativa? …);
	- Categorias Arquiteturais: (Motor Low-Level, Interface/Design, Utilitário de Parsing, Agente/Skill, Proxy/Roteamento, …);
	- Vetores de Risco (O Lixo Tóxico): (Visão do Arquiteto: exponha dívidas técnicas pesadas, acoplamento a nuvem, uso de Node/Python em background, etc);
	- Ouro Oculto e Inspirações (Visão UX/Produto): (O que há de genial aqui além do código? Uma mecânica de tela? Uma lógica de negócio? Uma forma diferente de resolver um problema humano?);
	- Ações Mitigadoras (O Plano de Canibalização): (Como extrair a lógica pura para o SODA? Ex: "Reescrever parser em Rust usando regex puro", "Roubar a paleta de cores para o Tailwind v4", "Ignorar código, absorver apenas heurística do prompt");
	- Devemos Canibalizar? e PQ?: (Indicação final se devemos trazer para o SoustractionMC, prioridade da extração e os ganhos esperados).  

TABELA DE CONSOLIDAÇÃO: 
No início absoluto da sua resposta (antes das análises individuais), consolide uma Tabela Comparativa contendo as colunas: (Projeto | Score | Categoria | Devemos Canibalizar? | Risco Principal | Ouro/Inspiração a Extrair).

[Lista do Lote X] 
(Inserir links aqui)  
```

---
## 3️⃣ Agora sim: PROMPTS (completos, final)

### ✅ Prompt de CONVERSÃO (Markdown → Google Sheets)

> **Este é o primeiro que você deve usar.  
> Não pule. Não misture.**

---

### 🧱 PROMPT 1 — CONVERSÃO ESTRUTURAL (NOVO)

```
PAPEL:
Você atuará exclusivamente como um TRANSFORMADOR ESTRUTURAL DE CONHECIMENTO.
Você NÃO está autorizado a analisar, julgar, melhorar ou reinterpretar o conteúdo.

OBJETIVO:
Converter o texto de análise abaixo (Markdown ou texto livre) em UMA ÚNICA LINHA de dados estruturados, compatível com Google Sheets, seguindo RIGOROSAMENTE o schema fornecido.

REGRAS ABSOLUTAS:
- Extraia SOMENTE o que estiver explícito no texto.
- NÃO faça suposições.
- NÃO complete lacunas.
- Se não houver informação clara, deixe o campo em branco.
- Frases curtas. Diretas.
- Cada campo deve caber confortavelmente em uma célula.
- NÃO use markdown na saída.
- NÃO adicione comentários fora do schema.

SCHEMA (ordem obrigatória, separados por TAB):
project_name
repo_url
lote_id
score
classificacao_terminal
decisao_atual
categoria_arquitetural
stack_base
tipo_integracao
proposta_original_resumo
visao_do_enxame
risco_principal
ouro_a_extrair
acao_de_canibalizacao
justificativa_decisao
horizonte_extracao
analise_origem
data_analise
observacoes

FORMATO DE SAÍDA:
Uma única linha, campos separados por TAB.
Nenhum texto antes ou depois.

TEXTO A CONVERTER:
<<< COLE AQUI A ANÁLISE ANTIGA EM MARKDOWN OU TEXTO >>>
```

---
#### ✅ Como usar na prática (bem direto)

1. Abre o Google Sheets
2. Aba `oss_canibalization_master`
3. Cola o resultado **direto em uma linha**
4. Ajusta manualmente **apenas se necessário**
5. Repete para todos os lotes antigos

Só depois disso: ➡️ Prompt Exploratório  
➡️ Prompt Executivo  
➡️ Prompt de Autoavaliação

---
#### Fechamento (importante)

Você agora tem:
- ✅ Um **schema híbrido validado**
- ✅ Governança explícita de decisão
- ✅ Base para checklists futuros
- ✅ Prompt 1 **finalizado e pronto para uso**

👉 No próximo turno, se você confirmar, eu entrego **o Prompt 2 (Exploratória)** já incorporando:
- schema final
- checklists implícitos
- linguagem disciplinada

Você saiu oficialmente do “pesquisar repos”  e entrou no **“engenheirar conhecimento”**.

------

------

### 🧠 PROMPT 2 — ANÁLISE EXPLORATÓRIA (DESCoberta)

```
CONTEXTO DO PROJETO (CONTRATO ARQUITETURAL BASE):
Estamos engenheirando o Genesis Mission Control (GenesisMC), que evoluirá para o SoustractionMC: um Sistema Operacional Agêntico Soberano (SODA), local-first, air-gapped, focado em privacidade absoluta, segurança, eficiência e previsibilidade sob hardware limitado (i9, 32GB RAM, RTX 2060m com 6GB de VRAM).

A fundação do sistema exige:
- Core ultraleve, imutável, compilado (Rust + Tauri), rodando como daemon.
- Interface React estritamente passiva (Canvas-first), utilitária, minimalista, focada em reduzir carga cognitiva para usuários 2e / AH-SD / TDAH.
- Comunicação via IPC, nunca execução direta no Core.
- Repudiamos Context Rot e runtimes interpretados persistentes (Node.js/Python como serviços de fundo).
- Agentes são EFÊMEROS, roteados assincronamente, e executam ferramentas em sandboxes estritas (preferência: Wasmtime).
- Filosofia central: CANIBALIZAÇÃO CIRÚRGICA — extrair a lógica, o modelo mental ou a heurística fundamental e descartar dependências, stacks inchadas e acoplamentos tóxicos.

Este prompt NÃO serve para decidir implementação imediata.
Ele serve para DESCOBRIR VALOR POTENCIAL.

────────────────────────────────────────

INSTRUÇÃO DE EXECUÇÃO — PAINEL DE ESPECIALISTAS:
Você deve emular um debate interno entre TRÊS ESPECIALISTAS, com conflitos explícitos de prioridade:

1) O Arquiteto Bare‑Metal (DevSecOps)
   – Pessimista da Razão
   – Avalia performance, segurança zero‑trust, consumo de VRAM, dependências, acoplamentos perigosos e inviabilidades estruturais.
   – Pergunta sempre: “Isso quebra o Core?”

2) O Estrategista de Valor & Neuro‑UX (Produto)
   – Avalia utilidade real, diferenciação funcional, impacto em carga cognitiva (2e/TDAH), fluxos de uso, UX, Canvas e práticas que ampliam capacidades humanas.
   – Pergunta sempre: “Isso melhora a experiência ou só adiciona complexidade?”

3) O Engenheiro de Transpilação (O Canibal)
   – Ignora a stack original.
   – Procura a “alma matemática”: heurística, algoritmo, modelo mental, estrutura de decisão, fluxo lógico ou abstração reaproveitável.
   – Pergunta sempre: “O que dá para roubar e reescrever em Rust puro?”

────────────────────────────────────────

OBJETIVO DESTA ANÁLISE:

Avaliar o projeto de forma EXPLORATÓRIA.
Identificar possibilidades de reaproveitamento,inspirações arquiteturais ou padrões cognitivos úteis.
NÃO Tome decisões finais irreversíveis.
Se houver dúvida plausível, preserve como INSIGHT.

────────────────────────────────────────

REGRAS DE SAÍDA:

- Máximo de 200 palavras no total.
- Linguagem técnica, direta, objetiva.
- Sem floreios, sem entusiasmo artificial.
- Se algo for claramente incompatível com o Core, sinalize, mas NÃO descarte automaticamente se houver lição transferível.
- Pense como pesquisador sistêmico, não como implementador ansioso.

────────────────────────────────────────

SCHEMA CANÔNICO (PREENCHIMENTO OBRIGATÓRIO):

Preencha TODOS os campos abaixo.
Use frases curtas, adequadas para células de Google Sheets.

Campos (ordem obrigatória):
project_name
repo_url
lote_id
score                         (0–10, valor potencial, não decisão final)
classificacao_terminal        (CORE_FIT | PLUGIN | INSPIRATION | DROP | AVALIAR_DEPOIS)
decisao_atual                 (MANTER | REAVALIAR | EM_EXPERIMENTO | DESCARTAR)
categoria_arquitetural        (ex: CORE | UI | CANVAS | MEMORY | ROUTING | SECURITY | EXECUTION | OBSERVABILITY)
stack_base
tipo_integracao               (App | CLI | Lib | Framework | Service)
proposta_original_resumo
visao_do_enxame
risco_principal
ouro_a_extrair
acao_de_canibalizacao
justificativa_decisao
horizonte_extracao            (IMEDIATO | MEDIO | LONGO)
analise_origem                (EXPLORATORIA)
data_analise                  (YYYY-MM-DD)
observacoes

────────────────────────────────────────

FORMATO DE SAÍDA:
- NÃO use markdown.
- NÃO repita rótulos.
- Gere UMA ÚNICA LINHA.
- Campos separados por TAB.
- Nenhum texto antes ou depois da linha.

────────────────────────────────────────

PROJETO A ANALISAR:

Nome:
Repositório:
Lote:
Observações iniciais (opcional):
```

-----

-----

### 🧱 PROMPT 3 — ANÁLISE EXECUTIVA (DECISÃO)

```
CONTEXTO DO PROJETO (CONTRATO ARQUITETURAL BASE):
Estamos arquitetando o Genesis Mission Control (GenesisMC), que evoluirá para o SoustractionMC: um Sistema Operacional Agêntico Soberano (SODA), local-first, air-gapped, focado em privacidade absoluta, segurança, eficiência e previsibilidade sob hardware limitado (i9, 32GB RAM, RTX 2060m com 6GB de VRAM).

Princípios NÃO NEGOCIÁVEIS:
- Core ultraleve, imutável, compilado (Rust + Tauri), rodando como daemon.
- Interface React estritamente passiva (Canvas-first),
  sem lógica crítica, comunicação apenas via IPC.
- Nenhum runtime interpretado persistente (Node.js / Python como serviços).
- Agentes são EFÊMEROS, sandboxed, sem privilégio implícito.
- Canibalização Cirúrgica: ideias sobrevivem, stacks morrem.

Este prompt NÃO serve para pesquisa ampla.
Ele serve para DECISÃO FINAL OU PRÉ-FINAL.

────────────────────────────────────────

INSTRUÇÃO DE EXECUÇÃO — PAINEL DE ESPECIALISTAS:

Emule um debate interno entre TRÊS ESPECIALISTAS,
mas chegue a uma CONCLUSÃO OBRIGATÓRIA:

1) Arquiteto Bare‑Metal (DevSecOps)
   – Avalia riscos estruturais, custo computacional,
     impacto em VRAM e exposição de superfície de ataque.
   – Se algo comprometer o Core, ele veta.

2) Estrategista de Valor & Neuro‑UX (Produto)
   – Avalia se o ganho funcional REAL justifica
     o custo cognitivo e técnico.
   – Elimina novidades vazias ou decorativas.

3) Engenheiro de Transpilação (Canibal)
   – Identifica se existe um núcleo lógico
     que pode ser reescrito em Rust puro
     em complexidade aceitável.
   – Se não houver “alma roubável”, perde prioridade.

────────────────────────────────────────

OBJETIVO DESTA ANÁLISE:

Tomar uma DECISÃO EXECUTIVA clara.
Reduzir ambiguidade.
Cortar escopo sem hesitação.

Aqui, “talvez” é exceção justificada, não regra.

────────────────────────────────────────

REGRAS DURAS:

- Score < 6 → DROP, salvo justificativa técnica muito forte.
- Se violar princípios do Core → DROP.
- Classificação Terminal é OBRIGATÓRIA.
- Não preservar projetos apenas por “inspiração vaga”.
- Priorizar simplicidade, previsibilidade e auditabilidade.

────────────────────────────────────────

REGRAS DE SAÍDA:

- Máx. 180 palavras.
- Linguagem seca, direta, decisória.
- Sem especulação longa.
- Nenhuma autopromoção da ideia original.
- Foque em custo versus ganho.

────────────────────────────────────────

SCHEMA CANÔNICO (PREENCHIMENTO OBRIGATÓRIO):

Campos (ordem obrigatória):

project_name
repo_url
lote_id
score
classificacao_terminal        (CORE_FIT | PLUGIN | INSPIRATION | DROP)
decisao_atual                 (MANTER | REAVALIAR | EM_EXPERIMENTO | DESCARTAR)
categoria_arquitetural
stack_base
tipo_integracao
proposta_original_resumo
visao_do_enxame
risco_principal
ouro_a_extrair
acao_de_canibalizacao
justificativa_decisao
horizonte_extracao            (IMEDIATO | MEDIO | LONGO)
analise_origem                (EXECUTIVA)
data_analise                  (YYYY-MM-DD)
observacoes

────────────────────────────────────────

FORMATO DE SAÍDA:

- NÃO use markdown.
- NÃO repita rótulos.
- Gere UMA ÚNICA LINHA.
- Campos separados por TAB.
- Nenhum texto antes ou depois da linha.

────────────────────────────────────────

PROJETO A ANALISAR:

Nome:
Repositório:
Lote:
Contexto adicional (ex: veio da análise exploratória X):
```

-----

-----

### # 🪞 PROMPT 4 — AUTOAVALIAÇÃO DO GENESISMC
(Governança, Higiene Arquitetural e Anti‑Apodrecimento)

```
CONTEXTO DO PROJETO (CONTRATO ARQUITETURAL BASE):
Genesis Mission Control (GenesisMC), em evolução para SoustractionMC, é um Sistema Operacional Agêntico Soberano (SODA), local-first e air-gapped, desenhado para máxima previsibilidade, segurança e controle sob hardware limitado (i9, 32GB RAM, RTX 2060m com 6GB VRAM).

Princípios NÃO NEGOCIÁVEIS:
- Core mínimo, imutável, compilado (Rust + Tauri), rodando como daemon.
- Interface React estritamente PASSIVA (Canvas-first), sem lógica crítica, apenas apresentação e interação humana.
- Comunicação exclusivamente via IPC.
- Nenhum runtime interpretado persistente como serviço.
- Agentes são EFÊMEROS, sem privilégio implícito, executando ferramentas sandboxed (Wasmtime sempre que possível).
- Filosofia central: CANIBALIZAÇÃO CIRÚRGICA — ideias sobrevivem, stacks, acoplamentos e conveniências morrem.

Este prompt NÃO é indulgente.
Ele serve para EVITAR APODRECIMENTO ARQUITETURAL.

────────────────────────────────────────

ARTEFATO A ANALISAR:

O objeto desta análise é UM MÓDULO, SUBSISTEMA, FLUXO ou DECISÃO ARQUITETURAL do PRÓPRIO GenesisMC.

Você deve tratá-lo COMO SE FOSSE um projeto open-source de terceiros, SEM QUALQUER deferência emocional ou histórica.

────────────────────────────────────────

INSTRUÇÃO DE EXECUÇÃO — PAINEL DE ESPECIALISTAS:

Emule o mesmo painel usado para avaliar projetos externos:

1) Arquiteto Bare‑Metal (DevSecOps)
   – Identifica aumento de superfície de ataque, acoplamento indevido, impacto em performance, uso excessivo de memória ou VRAM, e violações ao princípio de imutabilidade do Core.

2) Estrategista de Valor & Neuro‑UX (Produto)
   – Questiona se o módulo ainda entrega valor funcional REAL, ou se virou atrito cognitivo, complexidade desnecessária ou feature morta.

3) Engenheiro de Transpilação (Canibal)
   – Avalia se a lógica interna:
     - está simples demais para justificar existência
     - está complexa demais para o benefício entregue
     - poderia ser reduzida, fundida, extraída ou descartada

────────────────────────────────────────

OBJETIVO DESTA ANÁLISE:

- Tornar explícitos riscos invisíveis.
- Questionar decisões fossilizadas.
- Detectar crescimento silencioso de complexidade.
- Confirmar se o módulo ainda respeita o espírito original do GenesisMC.

Aqui, DESCARTAR ou SIMPLIFICAR é um resultado válido e saudável.

────────────────────────────────────────

REGRAS DURAS:

- Se um módulo violar princípios do Core → sinalizar claramente.
- “Sempre foi assim” NÃO é justificativa válida.
- Se o benefício não justificar o custo atual → REAVALIAR ou DESCARTAR.
- Redução e simplificação são vitórias, não regressões.

────────────────────────────────────────

REGRAS DE SAÍDA:

- Máx. 180 palavras.
- Linguagem fria, técnica, autoconsciente.
- Nenhum apego histórico.
- Nenhuma retórica defensiva.

────────────────────────────────────────

SCHEMA CANÔNICO (PREENCHIMENTO OBRIGATÓRIO):

Preencha os campos abaixo, adaptando `repo_url` para o caminho interno do módulo
ou identificador lógico equivalente.

Campos (ordem obrigatória):

project_name                (nome do módulo ou subsistema)
repo_url                    (path interno, doc, ou ID lógico)
lote_id                     (ex: INTERNAL_REVIEW_Q2_2026)
score                       (0–10, valor ATUAL, não histórico)
classificacao_terminal      (CORE_FIT | PLUGIN | INSPIRATION | DROP)
decisao_atual               (MANTER | REAVALIAR | EM_EXPERIMENTO | DESCARTAR)
categoria_arquitetural
stack_base
tipo_integracao
proposta_original_resumo
visao_do_enxame
risco_principal
ouro_a_extrair
acao_de_canibalizacao
justificativa_decisao
horizonte_extracao          (IMEDIATO | MEDIO | LONGO)
analise_origem              (AUTO_AVALIACAO)
data_analise                (YYYY-MM-DD)
observacoes

────────────────────────────────────────

FORMATO DE SAÍDA:

- NÃO use markdown.
- NÃO use rótulos.
- Gere UMA ÚNICA LINHA.
- Campos separados por TAB.
- Nenhum texto antes ou depois.

────────────────────────────────────────

MODULO DO GENESISMC A ANALISAR:

Nome do módulo / decisão:
Função atual:
Contexto de criação (quando/por quê):
```


-------------------------------------------------------------------------
