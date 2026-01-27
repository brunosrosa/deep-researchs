Quando usamos **“vibe coding”** (pedir “faz X pra mim” e iterar no feeling). A sensação de que “nunca fecha” não é só impressão: é um **efeito sistêmico** de como LLMs geram código **+** de como conduzimos o fluxo. Vamos destrinchar os _porquês_ e, mais importante, **como virar esse jogo**.

---

## TL;DR

**Por que parece incompleto?**

- LLMs otimizam para **texto plausível**, não para **software integrado, testado e pronto**.
- Prompts costumam carecer de **escopo, critérios de aceitação, não-funcionais** (performance, segurança), e **constraints do ambiente**.
- Falta **feedback executável** (tests/CI/rodar de ponta a ponta). Sem feedback, a IA tende a **atalhos**.
- A gente pede **“feature”**; projeto completo exige **arquitetura, testes, docs, deploy, observabilidade**.

**Como resolver?**

- Mudar do “oráculo que escreve código” para **pipeline com critérios claros**: **Definição de Pronto (DoD)**, **spec-first**, **tests**, **CI**, **scaffold** e **checklists**.
- Usar **prompts estruturados** e **ganchos de verificação** (ex.: “inclua Dockerfile, .env.example, migrations, seed, CI, README com Quickstart, testes de fumaça, comandos de run”).
- Rodar ciclos curtos: **planejar → gerar → rodar → testar → corrigir → documentar**.

---

## Por que “vibe coding” entrega algo “meia-boca”?

1. **O objetivo do modelo é plausibilidade textual**, não **completude de sistema**. Ele preenche padrões; sem especificação forte, “parece certo” > “é certo”.
2. **Especificação insuficiente**: sem **escopo + critérios de aceitação + NFRs** (segurança, logs, escalabilidade, latência), o modelo toma **atalhos**.
3. **Contexto limitado**: o modelo não “lembra” todo o projeto; sem **documentos âncora** (README, ADRs, spec), a coerência degrada.
4. **Sem feedback de execução**: se o código **não é rodado/testado**, o modelo não recebe correção **aninhada na realidade**.
5. **Incerteza do ambiente**: versões de libs, secrets, DB, ports, build steps—se isso não está explícito, ele assume defaults genéricos.
6. **Arquitetura subespecificada**: ele cria um “happy path” sem **ínsumos de integração** (infra, deploy, observabilidade).
7. **Optimização por atalho** (_specification gaming_): se você pede “API que responde X”, ele só vai até o que satisfaz o enunciado de forma minimal.
8. **Ausência de testes**: sem **TDD-lite** ou pelo menos **testes de fumaça**, arestas ficam invisíveis.
9. **Não-funcionais ignorados**: segurança, auditoria, tracing, performance, i18n… raramente aparecem se você não exigir.
10. **Docs e scripts de run falham**: sem **README com Quickstart, Makefile/scripts, Dockerfile**, a “completude” subjetivamente não aparece.

---

## Como **mudar o fluxo** para “entregável de verdade”

### 1) Comece por **Definição de Pronto (DoD)** minimalista

Use isso como checklist para **cada entrega** (feature ou MVP):

- ✅ **Funciona local** com 1 comando (ex.: `make dev` ou `docker compose up`).
- ✅ **README** com **Quickstart** (setup, run, test), escopo e limitações.
- ✅ **.env.example** com todas as variáveis.
- ✅ **Tests**: pelo menos **fumaça** (subir app, endpoint chave, caminho crítico).
- ✅ **Logs** básicos + **erros padronizados**.
- ✅ **CI** rodando lint + tests.
- ✅ **Estrutura de pastas** consistente e explicada.
- ✅ **Lista de pendências** (TODO/NICE-TO-HAVE) no final do README.

> Se algo do DoD não for aplicável, **registre por quê**.

### 2) **Spec-first** (curta e clara)

Antes do código, peça para a IA **escrever/atualizar a spec**:

- Objetivo, personas, principais **user stories** e **critérios de aceitação**.
- Constraints de **stack/ambiente** (versões, DB, porta, libs).
- **NFRs**: segurança (authn/authz), latência alvo, limites, logs, métricas.
- Integrações (APIs externas), erros esperados e **tratamento**.
- **Arquitetura** proposta (componentes e responsabilidades).
- **Planos de teste** (fumaça, unitários, integração).
- **Riscos** e trade-offs.

### 3) **Scaffold completo** antes de features

Peça um **esqueleto rodável** com:

- `Dockerfile` + `docker-compose.yml`.
- `Makefile` com `dev`, `test`, `lint`, `build`.
- `README.md` (Quickstart + troubleshooting).
- `src/` e `tests/` com exemplos.
- `.env.example` e carregamento seguro de envs.
- **CI** (GitHub Actions/GitLab CI) com lint + testes.
- (Se houver DB) **migrations** + **seed** mínimos.

> Só avance para features quando o scaffold **passar no DoD**.

### 4) **Teste de fumaça** primeiro

Duas ou três provas de vida:

- Sobe serviço e responde `/health`.
- **Happy path** de negócio (ex.: criar pedido e listar).
- **Erro** controlado (ex.: payload inválido).

### 5) **Iterar em ciclos curtos com feedback real**

**Planejar → gerar → rodar → consertar → documentar**.  
Sempre feche o ciclo atualizando **README**, **checklists**, **TODOs**.

---

## Prompts prontos (copiar/colar e adaptar)

### A) Prompt para **Scaffold Completo**

> **Contexto/Stack**: [ex.: Node 20 + Express + Postgres 15].  
> **Objetivo**: Gerar um scaffold **rodável** localmente.  
> **Entregáveis obrigatórios**:
> 
> - `Dockerfile`, `docker-compose.yml` (db + app).
> - `Makefile` com `dev`, `test`, `lint`, `build`.
> - `.env.example` (todas as variáveis) e leitura segura.
> - `README.md` com Quickstart (setup, run, test), troubleshooting comum.
> - Estrutura de pastas (`src`, `tests`) e um teste de fumaça que roda em CI.
> - CI (GitHub Actions) com lint e testes.
> - (Se DB) **migration inicial** e **seed** mínimos.  
>     **Critérios de aceitação (DoD)**: tudo roda com `make dev`; `make test` passa; `GET /health` responde 200; README cobre setup completo.  
>     **Formato da saída**: lista de arquivos + conteúdo de cada arquivo, e o **comando único** para subir local.

### B) Prompt para **Feature** com verificação

> **Tarefa**: Implementar [feature].  
> **Critérios de aceitação**: [especifique].  
> **Não-funcionais**: auth (JWT), logs estruturados, tempo de resposta < 200ms para payload X, limites.  
> **Testes**: adicione testes (fumaça + unitários para [componentes críticos]).  
> **Docs**: atualize README (endpoints, exemplos de curl).  
> **Saída**:
> 
> 1. Mudanças de código (arquivos/patches).
> 2. Novos testes e como rodá-los.
> 3. Ajustes no README.
> 4. Comandos para rodar local e verificar.

### C) Prompt para **Correções guiadas por feedback**

> **Erro ao rodar**: [cole logs/stacktrace].  
> **Ambiente**: [versões, SO].  
> **O que já tentei**: […].  
> **Peço**: diagnóstico provável, passos de correção, patches, e teste para prevenir regressão.

---

## Checklist de **Completude por Camada**

**Aplicação**

- [ ] Healthcheck
- [ ] Tratamento de erros padronizado
- [ ] Logs estruturados
- [ ] Config por env (com defaults e validações)

**Segurança**

- [ ] Authn/Authz (ou mock se MVP)
- [ ] Rate limit básico
- [ ] Sanitização/validação de inputs

**Dados**

- [ ] Migrations + seed
- [ ] Estrutura de repositório/ORM
- [ ] Teste de conexão no boot

**Qualidade**

- [ ] Lint/formatter
- [ ] Tipagem (se aplicável)
- [ ] Testes: fumaça + unit/integração chave

**Infra**

- [ ] Dockerfile + Compose
- [ ] Makefile/scripts de automação
- [ ] CI com lint + test

**Docs**

- [ ] README Quickstart
- [ ] Lista de endpoints (ou CLI)
- [ ] ADRs curtos (decisões arquiteturais)

---

## Anti‑padrões de “vibe coding” (para você evitar)

- **Pedir “faz X” sem DoD** → obtenha trecho útil, não sistema.
- **Ignorar ambiente** (versões/DB/secrets) → gera código que não roda.
- **Sem testes/CI** → nada valida.
- **Pular scaffold** → cada feature vira gambiarra.
- **Deixar docs para o fim** → o fim nunca chega.

---

## Quando “vibe coding” é bom?

- **Spikes/exploração**: rascunhar ideias, comparar abordagens.
- **Geração de boilerplate**: _desde que_ você **amarre no DoD**.
- **Produção de alternativas**: “me dê 3 arquiteturas com prós/cons”.

> Para **produção**, troque “vibe” por **pipeline com validações**.

---

## Um roteiro que funciona (ciclo de ~90 min)

1. **Spec curta** + **DoD** (10–15 min).
2. **Scaffold** com todos os artefatos (15–25 min).
3. **Testes de fumaça** + rodar tudo (15–20 min).
4. **Feature 1** com testes (20–25 min).
5. **Fechamento**: atualizar README, TODOs, abrir issues (5–10 min).

Repita para cada feature/módulo.

---

## Quer que eu te ajude a **operacionalizar** já?

Posso:

- Gerar um **pacote de prompts** personalizado ao seu **stack** (ex.: Python/FastAPI + Postgres, ou Node/NestJS).
- Criar uma **lista de DoD** adaptada ao contexto do BB (segurança, auditoria, logs, compliance).
- Montar um **scaffold completo** (Docker, Makefile, CI, tests, README) para você colar no repositório.

**Qual é seu stack-alvo (linguagem/framework/DB)** e o tipo de projeto (API, ETL, microserviço, lib, front web)? Se quiser, já trago um scaffold com **testes de fumaça** e **CI** prontos para rodar.