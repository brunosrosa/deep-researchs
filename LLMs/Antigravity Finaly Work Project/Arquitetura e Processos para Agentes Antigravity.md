# Arquitetura de Orquestração Agentica no Google Antigravity: Configuração Avançada do Ciclo de Vida de Software, do Sistema Beads à Engenharia de Contexto Socrática

## Sumário Executivo

A emergência de plataformas de desenvolvimento "Agent-First" (centradas no agente), epitomizada pelo Google Antigravity, marca uma inflexão crítica na engenharia de software contemporânea. Diferente dos assistentes de codificação da geração anterior, que operavam como copilotos passivos reagindo a keystrokes, o Antigravity e seus pares introduzem a "Superfície do Gerenciador" (_Manager Surface_). Este novo paradigma inverte a relação ferramenta-usuário: o desenvolvedor deixa de ser o operador primário do editor de texto para se tornar o orquestrador de uma força de trabalho digital autônoma, composta por agentes baseados em modelos como Gemini 3 Pro e Claude Sonnet.

No entanto, a autonomia agentica traz consigo desafios de coordenação, persistência de memória e integridade arquitetural. Agentes de Inteligência Artificial (IA), por natureza, são propensos a "amnésia" entre sessões e "alucinação" de dependências. Para mitigar esses riscos e viabilizar um ciclo de vida de desenvolvimento robusto — da ideação abstrata ao Pull Request (PR) final — é imperativo implementar uma infraestrutura rigorosa. Este relatório detalha a configuração exaustiva de tal ambiente, ancorado em três pilares fundamentais: (1) o sistema de rastreamento de tarefas baseado em grafos **Beads** para memória persistente ; (2) uma estratégia de **Engenharia de Contexto** estática via ADRs e OpenSpec; e (3) fluxos de trabalho dinâmicos que empregam **Maieutica Socrática** para planejamento e **TDD** (Test-Driven Development) para execução.

A análise a seguir transcende a configuração superficial, abordando correções críticas de infraestrutura para contêineres em 2026 , a teoria por trás da memória de grafos para LLMs e a implementação de "Semantic Linting" como guardrail arquitetural.

---

## 1. Infraestrutura e o Paradigma da "Manager Surface"

A transição para o Google Antigravity exige uma reavaliação da topologia do ambiente de desenvolvimento. O conceito central a ser internalizado é que o IDE não é mais apenas um editor de texto; é um sistema operacional para agentes.

### 1.1. A Dicotomia das Superfícies: Editor vs. Gerenciador

O design do Antigravity bifurca a experiência de desenvolvimento em duas interfaces distintas, cada uma servindo a um propósito cognitivo e operacional específico. Compreender essa distinção é pré-requisito para configurar os fluxos de trabalho corretamente.

#### 1.1.1. Superfície do Editor (Síncrona)

A Superfície do Editor mantém a familiaridade com ambientes baseados no VS Code, oferecendo funcionalidades de edição de texto, _syntax highlighting_ e terminais integrados. É aqui que o desenvolvedor humano intervém para "cirurgias de precisão" no código ou para resolver impasses que os agentes não conseguem transpor. No entanto, em um fluxo agentico maduro, o tempo gasto nesta superfície deve decair exponencialmente à medida que a confiança nos agentes aumenta.

#### 1.1.2. Superfície do Gerenciador (Assíncrona)

A Superfície do Gerenciador (_Manager Surface_) é a inovação disruptiva. Ela funciona como um "Controle de Missão", permitindo ao desenvolvedor instanciar, monitorar e coordenar múltiplos agentes em paralelo. Estes agentes não vivem "dentro" do editor; ao contrário, eles possuem o editor e o terminal como ferramentas em seu cinto de utilidades.

Esta inversão de controle implica que o ambiente deve ser configurado para suportar concorrência. Se múltiplos agentes (um focado em backend, outro em testes, outro em documentação) tentarem acessar o mesmo sistema de arquivos simultaneamente, conflitos de _lock_ e condições de corrida podem ocorrer. Portanto, a configuração do ambiente deve prever isolamento de processos e gerenciamento de estado robusto, temas que exploraremos na seção de DevContainers.

### 1.2. Configuração de Isolamento e Segurança: DevContainers

A autonomia concedida aos agentes no Antigravity — permitindo-lhes executar comandos de terminal, instalar pacotes e manipular o sistema de arquivos — introduz vetores de risco significativos. Um agente mal configurado ou alucinado poderia, teoricamente, executar um comando destrutivo no host. A utilização de **DevContainers** não é apenas uma boa prática; é uma barreira de segurança obrigatória para a contenção de danos e reprodutibilidade do ambiente.

#### 1.2.1. A Crise de Conectividade da Versão 1.16.5 e Soluções

Relatórios técnicos de fevereiro de 2026 identificaram uma regressão crítica na versão 1.16.5 do Antigravity, onde a conexão com DevContainers remotos falha, apresentando erros de "Code 127" e "WebSocket 1006". A análise de causa raiz aponta para uma falha na lógica de descoberta de caminho do servidor do agente (`remoteServerNodePath`) no host, que ignora a estrutura de diretórios prefixada por versão utilizada pelo script de instalação dentro do contêiner.

Para operacionalizar o ambiente hoje, é necessário injetar um script de correção (`workaround`) durante o ciclo de vida de criação do contêiner. Este script deve criar links simbólicos (symlinks) que reconciliem a estrutura de diretórios esperada pelo host com a realidade do contêiner.

**Implementação do Fix no `devcontainer.json`:**

A configuração abaixo não apenas define a imagem base, mas utiliza o gancho `postCreateCommand` para executar a correção de caminhos, garantindo que a Superfície do Gerenciador consiga se comunicar com o ambiente isolado.

```json
{
  "name": "Antigravity Agentic Environment",
  "image": "mcr.microsoft.com/devcontainers/python:3.11",
  "features": {
    "ghcr.io/devcontainers/features/docker-in-docker:2": {
        "version": "latest",
        "moby": true
    },
    "ghcr.io/devcontainers/features/git:1": {}
  },
  "customizations": {
    "vscode": {
      "extensions": [
        "steveyegge.beads-extension",
        "ms-python.python",
        "charliermarsh.ruff"
      ],
      "settings": {
        "terminal.integrated.defaultProfile.linux": "zsh",
        "python.defaultInterpreterPath": "/usr/local/bin/python"
      }
    }
  },
  "postCreateCommand": "bash.devcontainer/setup_agent_env.sh",
  "remoteUser": "vscode"
}
```

**Script de Correção e Inicialização (`.devcontainer/setup_agent_env.sh`):**

Este script shell é vital para a estabilidade do sistema. Ele executa duas funções primordiais: (1) aplica o _patch_ para o bug da versão 1.16.5 e (2) inicializa o sistema Beads em modo "stealth" (furtivo) para evitar poluição prematura do repositório Git.

```bash
#!/bin/bash
set -e

# --- FIX CRÍTICO ANTIGRAVITY v1.16.5 ---
# Cria symlinks para garantir que o agente encontre os binários do servidor
# O bug faz com que o host procure em ~/.antigravity-server/bin/<commit>
# mas a instalação cria em ~/.antigravity-server/bin/1.16.5-<commit>
echo "Aplicando fix de caminhos para Antigravity Server..."
TARGET_DIR="$HOME/.antigravity-server/bin"
if; then
    cd "$TARGET_DIR"
    for dir in 1.16.5-*; do
        if [ -d "$dir" ]; then
            commit_hash="${dir#1.16.5-}"
            if [! -L "$commit_hash" ]; then
                ln -s "$dir" "$commit_hash"
                echo "Symlink criado: $commit_hash -> $dir"
            fi
        fi
    done
fi

# --- INICIALIZAÇÃO DO BEADS ---
# Instalação do CLI do Beads (assumindo npm disponível ou binário pré-baixado)
if! command -v bd &> /dev/null; then
    echo "Instalando Beads CLI..."
    npm install -g @beads/bd
fi

# Inicializa Beads em modo Stealth se ainda não existir
if [! -d ".beads" ]; then
    echo "Inicializando Beads..."
    bd init --stealth
fi

echo "Ambiente Agentico Configurado com Sucesso."
```

Esta camada de infraestrutura garante que, ao solicitar uma tarefa ao agente na Superfície do Gerenciador, ele tenha um ambiente de execução estável, com acesso às ferramentas necessárias e isolado do sistema operacional hospedeiro.

---

## 2. O Sistema Neural: Implementando 'Beads' para Memória Persistente

A maior limitação operacional dos Agentes de IA baseados em LLMs é a volatilidade de sua memória de curto prazo (Janela de Contexto). Embora modelos como o Gemini 3 Pro possuam janelas de milhões de tokens, confiar no contexto da sessão para o gerenciamento de projetos de longo prazo é ineficiente e propenso a erros. "Beads", desenvolvido por Steve Yegge, surge como a solução definitiva para este problema, fornecendo um sistema de rastreamento de issues baseado em grafos, distribuído e legível por máquinas.

### 2.1. Teoria da Memória Baseada em Grafos para Agentes

Sistemas tradicionais de rastreamento (Jira, Trello) ou arquivos planos (`TODO.md`) falham com agentes por duas razões principais: falta de estrutura semântica explícita e dificuldade em representar dependências complexas. O Beads resolve isso modelando tarefas não como listas, mas como nós em um grafo direcionado.

- **Persistência Semântica:** Cada tarefa ("bead") possui um ID único, estado, prioridade e relacionamentos tipados. Isso permite que o agente "ancore" seu raciocínio em identificadores estáveis.
- **Grafo de Dependência:** O agente pode consultar explicitamente "quais tarefas estão bloqueando a tarefa X?". Isso impede a execução prematura de tarefas dependentes, um erro comum em agentes que alucinam a ordem de execução.
- **Compatibilidade JSON:** O Beads foi desenhado para emitir e ingerir JSON, o formato nativo de "pensamento" das ferramentas de chamada de função (Function Calling) dos LLMs atuais.

### 2.2. Inicialização Estratégica e Topologia

A inicialização do Beads deve ser adaptada à natureza do projeto e ao papel do desenvolvedor. O comando `bd init` cria um banco de dados SQLite local (`.beads/beads.db`) e arquivos JSONL para sincronização via Git.

#### 2.2.1. Modos de Operação

- **Modo Padrão (`bd init`):** Ideal para projetos novos ou onde a equipe inteira adota o Beads. Os arquivos de rastreamento são commitados no repositório principal.
- **Modo Contribuidor (`bd init --contributor`):** Essencial para trabalhar em projetos Open Source ou corporativos onde não se tem permissão para adicionar arquivos de configuração na raiz. O Beads redireciona o armazenamento para um repositório separado (ex: `~/.beads-planning`), mantendo o fluxo de trabalho do agente invisível para o repositório principal.
- **Modo Stealth (`bd init --stealth`):** Para uso pessoal e local, sem sincronização remota, permitindo que o desenvolvedor use agentes poderosos em projetos legados sem "pedir permissão".

### 2.3. O Ciclo de Vida da Tarefa no Antigravity

A integração do Beads no fluxo de trabalho do Antigravity transforma a interação com o agente de "faça X" para "gerencie o estado de X".

**Tabela 1: Mapeamento de Comandos Beads para Ações Cognitivas do Agente**

| **Fase do Fluxo** | **Ação Cognitiva**                      | **Comando Beads**                    | **Resultado no Sistema**                                                  |
| ----------------- | --------------------------------------- | ------------------------------------ | ------------------------------------------------------------------------- |
| **Descoberta**    | "O que está pronto para ser feito?"     | `bd ready`                           | Retorna lista JSON de tarefas sem bloqueios pendentes (prioridade P0-P4). |
| **Planejamento**  | "Preciso quebrar esta spec em tarefas." | `bd create "Título" -p <Pri> --json` | Cria novos nós no grafo. Retorna os IDs gerados.                          |
| **Dependência**   | "A tarefa B depende da A."              | `bd dep add <ID-B> <ID-A>`           | Cria uma aresta direcionada B -> A (B bloqueado por A).                   |
| **Execução**      | "Vou começar a trabalhar nisso."        | `bd update <ID> --claim`             | Atribui a tarefa ao agente e muda status para `in_progress`.              |
| **Conclusão**     | "Terminei e testei."                    | `bd close <ID>`                      | Muda status para `closed`. Desbloqueia tarefas dependentes.               |

### 2.4. Manutenção e Higiene do Grafo

Com o tempo, o grafo de tarefas pode crescer e consumir tokens excessivos se alimentado integralmente ao contexto do agente. O Beads implementa um conceito de "Decaimento de Memória" ou "Compactação".

- **Compactação Semântica:** Tarefas fechadas há muito tempo são resumidas ou arquivadas, mantendo na "memória de trabalho" apenas o que é relevante para o contexto atual.
- **Sincronização (`bd sync`):** Como o Beads é _git-backed_, a execução regular de `bd sync` garante que o estado do grafo seja persistido no histórico do Git, permitindo auditoria e reversão se o agente cometer erros de gerenciamento.
- **Diagnóstico (`bd doctor`):** Ferramenta essencial para verificar a integridade do banco de dados SQLite e a consistência dos arquivos JSONL, crucial após merges complexos de Git.

---

## 3. Engenharia de Contexto: A Camada Semântica Estática

Enquanto o Beads gerencia a memória dinâmica (o estado mutável do projeto), a **Engenharia de Contexto** trata da memória estática e das regras imutáveis. No Antigravity, isso é implementado através de arquivos Markdown estruturados que agem como "Constituição" do projeto. A ausência desses arquivos leva a agentes que, embora eficientes, produzem código que viola padrões arquiteturais ou de negócio.

### 3.1. Estrutura de Diretórios Recomendada (`.ai-context`)

Para maximizar a recuperação de informações pelo agente (seja por leitura direta ou RAG), recomenda-se uma estrutura explícita:

```
/
├──.agent/
│ ├── rules/ # Regras globais e locais (Semantic Linting)
│ ├── skills/ # Scripts e ferramentas personalizadas (Skills)
│ └── workflows/ # Definições de fluxos de trabalho
├──.ai-context/ # Contexto estático do projeto
│ ├── architecture.md # Visão geral do sistema, diagramas Mermaid
│ ├── conventions.md # Padrões de código, nomenclatura, estilo
│ └── tech-stack.md # Versões de bibliotecas, ferramentas permitidas
├── docs/
│ └── adr/ # Architecture Decision Records (Imutável)
├── specs/ # Especificações funcionais (Padrão OpenSpec)
│ └── feature-auth.md
└── AGENTS.md # O Meta-Prompt / Ponto de Entrada
```
### 3.2. ADRs (Architecture Decision Records): A Jurisprudência do Projeto

Agentes de IA têm uma propensão a "reinventar a roda" ou sugerir tecnologias modernas que conflitam com decisões passadas. Os ADRs servem como precedentes legais que o agente _deve_ consultar.

- **Mecanismo de Aplicação:** O agente deve ser instruído (via `AGENTS.md`) a verificar a pasta `docs/adr/` antes de propor qualquer mudança estrutural.
- **Exemplo Prático:** Se um ADR define "Uso exclusivo de PostgreSQL", e o agente sugere MongoDB para uma nova feature, o conflito com o ADR-003 deve disparar um erro de validação interna no agente, forçando-o a se corrigir.
- **Prompting de Consulta:** "Verifique os ADRs existentes. Esta proposta viola alguma decisão anterior? Se sim, cite o ADR e ajuste a proposta."
### 3.3. `spec.md` e o Padrão OpenSpec

Especificações vagas geram código bugado. O padrão **OpenSpec** fornece um formato estruturado para comunicar requisitos aos agentes. O arquivo `spec.md` atua como a "Verdade do Produto".

**Estrutura do OpenSpec (`spec.md`):**
O arquivo deve ser delimitado por marcadores que facilitam o _parsing_ pelo agente:

```
# Feature: Autenticação Biométrica
## Status: Draft

## Contexto
Necessidade de reduzir fricção no login mobile.

## Requisitos Funcionais
1. O sistema deve aceitar credenciais WebAuthn.
2. Fallback para senha deve existir.

## Requisitos Técnicos
- Usar biblioteca `passkey-lib` v2.
- Não alterar a tabela `users` existente; criar `user_credentials`.

A instrução no `AGENTS.md` deve ser clara: "Qualquer implementação deve ser rastreável a um item na seção `OPENSPEC` do arquivo de spec correspondente."
```
### 3.4. `rules.md` e "Semantic Linting"

Diferente de linters sintáticos (como ESLint ou Pylint) que verificam pontuação e estilo, o **Semantic Linting** verifica a _intenção_ e a _arquitetura_. No Antigravity, isso é implementado através de arquivos de regras na pasta `.agent/rules/`.

- **Regras Globais:** Aplicáveis a todo o projeto. Ex: "Sempre prefira composição sobre herança."
- **Regras de Workspace:** Específicas do contexto atual. Ex: "Em arquivos dentro de `/infra`, use Terraform v1.5."
- **Implementação:** O Antigravity injeta essas regras no _System Prompt_ do agente. Isso cria um "guardrail" cognitivo. Se o agente tentar escrever código que viola uma regra semântica (ex: acoplar a UI diretamente ao Banco de Dados em uma arquitetura Hexagonal), o modelo penaliza essa probabilidade de geração.

### 3.5. `AGENTS.md`: O Manual de Operações

Este arquivo é o _bootloader_ cognitivo do agente. É a primeira coisa que ele lê. Ele deve orquestrar o uso de todas as outras ferramentas.

**Exemplo de Diretriz em `AGENTS.md`:**

```
> "Você é um Engenheiro de Software Sênior. Sua memória persistente é o sistema `Beads` (comando `bd`). Suas leis imutáveis estão em `docs/adr/`. Suas tarefas atuais estão definidas em `spec.md`.

> **Protocolo de Operação:**
> 1. Inicie lendo `bd ready` para identificar tarefas.
> 2. Antes de codificar, leia os ADRs relevantes.
> 3. Use TDD: Escreva o teste, veja falhar, implemente, refatore.
> 4. Nunca peça desculpas; seja técnico e direto."
```

---

## 4. Fluxos de Trabalho Dinâmicos: Metodologia e Execução

Com a infraestrutura e o contexto estabelecidos, detalhamos agora o fluxo de trabalho "vivo" que guia o agente da ideia abstrata até o código testado. Este fluxo combina a **Maieutica Socrática** para refinar o planejamento e o **TDD** para garantir a execução.

### 4.1. Fase 1: Ideação e Planejamento Socrático ("Plan Mode")

O Antigravity oferece o "Plan Mode" (Modo de Planejamento), onde o agente foca em raciocínio arquitetural em vez de geração de código rápida ("Fast Mode"). Nesta fase, o objetivo é combater a tendência do LLM de ser "agradável" e aceitar especificações incompletas.

#### 4.1.1. O Loop Socrático

Implementamos um _Workflow_ (arquivo `.agent/workflows/socratic-planning.md`) que instrui o agente a adotar uma postura inquisitiva.

**Template de Prompt Socrático:**

```
> "Não gere código ainda. Atue como um Arquiteto Socrático. Analise a minha solicitação inicial para a feature X.
> 	1. Identifique 3 ambiguidades ou riscos ocultos na minha descrição.
> 	2. Faça perguntas difíceis sobre escalabilidade, segurança e manutenção.
> 	3. Proponha alternativas arquiteturais e peça que eu decida.
Somente após eu responder a todas as perguntas e aprovar o plano, você deve gerar o `spec.md` final e explodir as tarefas no Beads."
```

Este processo transforma uma solicitação vaga ("Quero um chat") em uma especificação robusta ("Chat via WebSocket com persistência em Redis e fallback para Long-Polling"), prevenindo o desperdício de tokens em implementações falhas.

#### 4.1.2. Explosão de Tarefas no Beads

Uma vez aprovada a `spec.md`, o agente (ainda em Plan Mode) deve traduzir o documento em nós executáveis no grafo Beads.

- _Comando:_ `bd create "Configurar Redis para PubSub" -p 1 --json`
- _Comando:_ `bd create "Implementar Handler de WebSocket" -p 1 --deps <ID-Redis> --json`

### 4.2. Fase 2: Execução via TDD (Test-Driven Development)

Na fase de execução, o agente transita para a "Superfície do Editor" ou usa o "Fast Mode" para tarefas granulares. O TDD atua como um "Circuit Breaker" (disjuntor) automatizado.

#### 4.2.1. O Ciclo Red-Green-Refactor Agentico

Instruímos o agente (via `rules.md`) a nunca escrever código de produção sem um teste falho prévio.

1. **RED:** O agente seleciona a tarefa (`bd update <id> --claim`) e cria um arquivo de teste (ex: `test_websocket.py`) que afirma o comportamento esperado. Ele executa o teste e confirma a falha.
    - _Insight:_ Se o teste passar de primeira, o agente alucinou ou o teste é inútil. O agente deve ser instruído a tratar "Sucesso no primeiro teste" como uma falha de processo.
2. **GREEN:** O agente implementa o código mínimo necessário para passar o teste.
3. **REFACTOR:** Com o teste verde, o agente aplica as regras de `conventions.md` para limpar o código.

### 4.3. Fase 3: "Reverse Documentation" e PR

Ao finalizar a implementação, o agente deve preparar o terreno para a revisão humana e a próxima iteração.

#### 4.3.1. Documentação Reversa Automatizada

Agentes são excelentes em explicar o que fizeram. Antes de fechar a _issue_ no Beads, o agente deve atualizar a documentação técnica (ex: Swagger, READMEs, comentários de código) para refletir a realidade do código implementado. Isso é chamado de "Reverse Documentation" e garante que a documentação nunca fique obsoleta.

#### 4.3.2. Preparação do PR

O agente consolida o trabalho:

1. Executa `bd close <id>` para todas as tarefas finalizadas.
2. Gera um resumo do PR vinculando os IDs do Beads (`Closes bd-123`).
3. Executa uma última verificação de "Semantic Linting" para garantir que nenhum débito técnico arquitetural foi introduzido.

---

## 5. Operacionalizando o Ciclo Completo: Um Estudo de Caso

Para visualizar a integração desses componentes, consideremos o cenário: "Implementar suporte a chaves de API com rate limiting".

1. **Início (Manager Surface):** O desenvolvedor invoca o agente em **Plan Mode** com o prompt Socrático.
2. **Refinamento:** O agente questiona: "O rate limit é por IP ou por usuário? Qual o armazenamento (Redis/Memcached)?". O desenvolvedor responde.
3. **Definição:** O agente escreve `specs/api-keys.md` e cria o grafo no **Beads**:
    - `bd-101`: "Configurar Redis" (Bloqueia 102)
    - `bd-102`: "Middleware de Rate Limit" (Bloqueia 103)
    - `bd-103`: "Endpoint de Geração de Chaves"
4. **Execução (Editor Surface):** O agente reivindica `bd-101`. Verifica **ADRs** (confirma uso de Redis). Implementa infra via Docker. Testa conexão. Fecha `bd-101`.
5. **TDD:** Agente assume `bd-102`. Escreve teste que simula 100 requisições e espera erro 429. Implementa lógica. Teste passa.
6. **Verificação:** O agente roda o **Semantic Linter**. O linter avisa: "Lógica de negócio no Middleware". Agente refatora para um Service dedicado.
7. **Finalização:** Agente executa `bd sync`, atualiza documentação da API e abre o PR.

---

## 6. Conclusão

A configuração do ambiente no Google Antigravity descrita neste relatório não se trata apenas de instalar ferramentas, mas de estabelecer um sistema sociotécnico onde humanos e agentes colaboram com papéis definidos.

- O **Antigravity** e os **DevContainers** fornecem o "corpo" físico e seguro para a execução.
- O **Beads** fornece a "memória" de longo prazo e a estrutura lógica de dependências.
- A **Engenharia de Contexto** (ADR, Spec, Rules) fornece a "consciência" e os limites éticos/técnicos.
- Os fluxos **Socráticos** e **TDD** fornecem o "método" científico de validação.

Ao adotar esta arquitetura, as organizações podem transitar do modelo artesanal de escrita de código para um modelo industrial de orquestração de software, onde a velocidade de desenvolvimento é limitada apenas pela clareza da especificação e pela capacidade de computação, não mais pela digitação humana.

---

### Tabelas de Dados e Referências

#### Tabela 2: Comparativo de Estratégias de Memória para Agentes

| **Estratégia**                | **Mecanismo**                             | **Vantagens**                                                    | **Desvantagens**                                                     | **Mitigação no Antigravity**                  |
| ----------------------------- | ----------------------------------------- | ---------------------------------------------------------------- | -------------------------------------------------------------------- | --------------------------------------------- |
| **Sessão (Context Window)**   | Memória volátil do LLM                    | Rápido, sem setup.                                               | Amnésia total ao reiniciar; alucinação em projetos longos.           | Uso apenas para tarefas "Fast Mode".          |
| **Arquivos Planos (TODO.md)** | Leitura/Escrita de arquivo texto          | Simples, legível por humanos.                                    | Falta estrutura semântica; difícil gerenciar dependências complexas. | Substituído por OpenSpec para specs.          |
| **Beads (Graph-Based)**       | Banco de dados SQLite + Grafo Direcionado | Persistência robusta; gestão de dependências; query estruturada. | Requer CLI e disciplina de uso.                                      | Integração nativa via prompts em `AGENTS.md`. |
#### Tabela 3: Artefatos de Contexto e Seus Papéis

|**Artefato**|**Localização**|**Função Cognitiva para o Agente**|**Frequência de Acesso**|
|---|---|---|---|
|**AGENTS.md**|Raiz|Meta-Prompt / Bootloader|Início de cada sessão.|
|**ADR**|`docs/adr/`|Jurisprudência / Restrição Arquitetural|Durante o planejamento (Plan Mode).|
|**Spec**|`specs/`|Fonte da Verdade Funcional (OpenSpec)|Durante a implementação e TDD.|
|**Rules**|`.agent/rules/`|Guardrail em Tempo Real (Semantic Linting)|Contínuo (System Prompt).|
