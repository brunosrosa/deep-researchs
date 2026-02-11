---
aliases:
  - "Agentes Antigravity: Processos e Ferramentas"
---

# Arquitetura de Ambientes de Desenvolvimento Agêntico no Google Antigravity: Um Framework para a Engenharia de Software Assistida por IA

## 1. Introdução: A Mudança de Paradigma para a Engenharia Agêntica

A indústria de desenvolvimento de software encontra-se em um ponto de inflexão histórico, transitando de um modelo centrado na sintaxe e na escrita manual de código para um paradigma "agent-first" (primeiro o agente), focado na orquestração de intenções e na arquitetura de sistemas. Plataformas emergentes como o **Google Antigravity** não são meras atualizações incrementais dos IDEs tradicionais; elas representam uma nova categoria de interface humano-computador onde o desenvolvedor atua menos como um pedreiro digital e mais como um arquiteto ou gerente de produto.

Para o usuário "semi-leigo"—definido aqui como um profissional com profundo conhecimento de domínio (negócios, produto, lógica) mas sem a fluência técnica para gerenciar cadeias de ferramentas complexas ou depurar erros de sintaxe obscuros—essa mudança é libertadora. No entanto, ela introduz um novo conjunto de desafios. A "vibe coding" (codificação baseada apenas na intuição e chat) falha catastroficamente em projetos complexos devido à saturação do contexto dos Grandes Modelos de Linguagem (LLMs), à perda de memória transacional e à falta de rigor estrutural.

Este relatório técnico estabelece um plano arquitetônico exaustivo para configurar um ambiente de desenvolvimento autônomo, resiliente e auditável dentro do Google Antigravity. A proposta integra **Beads** para gestão de memória gráfica, **DevContainers** para infraestrutura imutável, **OpenSpec** e **ADRs** para aterramento cognitivo (grounding), e fluxos de trabalho **Socráticos** e de **TDD** (Test-Driven Development) para garantir a qualidade. O objetivo é criar um "exoesqueleto mecânico" que automatize as tarefas de baixo nível, permitindo que o semi-leigo opere no nível da estratégia e do design.

### 1.1 O Desafio da Saturação de Contexto e a "Amnésia" dos Agentes

Os agentes de IA, embora poderosos, sofrem de limitações cognitivas inerentes, principalmente relacionadas à janela de contexto finita e à falta de permanência de objetos. Quando um usuário interage com um agente por longos períodos, a "memória de trabalho" do modelo se enche. Em fluxos tradicionais baseados em arquivos Markdown soltos (como `TODO.md`) ou conversas longas, o agente eventualmente "esquece" decisões arquiteturais tomadas no início do projeto, resultando em código que não se integra ao todo ou que reintroduz bugs já resolvidos.

A solução proposta neste relatório é a **Externalização do Estado**. Ao mover a memória do projeto para sistemas estruturados e consultáveis (como o banco de dados grafo do Beads ou os artefatos de especificação do OpenSpec), transformamos o ambiente de desenvolvimento em um "cérebro externo". O agente não precisa mais "lembrar" de tudo; ele precisa apenas saber como "consultar" o estado atual do sistema. Para o semi-leigo, isso é crucial: garante que o agente mantenha a coerência lógica do projeto sem que o usuário precise lembrá-lo constantemente das regras de negócio.

---

## 2. A Fundação Infraestrutural: DevContainers e a Reprodutibilidade Determinística

Para que um agente de IA opere com autonomia real, o ambiente subjacente deve ser determinístico. Um agente não pode ser eficaz se passar metade do tempo tentando resolver conflitos de versão do Node.js ou caminhos de biblioteca incorretos na máquina local do usuário. A base desta arquitetura, portanto, é o uso de **DevContainers** (Containers de Desenvolvimento), uma tecnologia que encapsula todo o ambiente de execução—ferramentas, bibliotecas, configurações e extensões—em uma unidade portátil e imutável.

O Google Antigravity, construído sobre a fundação do VS Code, suporta nativamente a especificação DevContainer. Isso permite que o semi-leigo inicie um projeto complexo com um único clique, sem nunca precisar abrir um terminal para instalar dependências manualmente.

### 2.1 Anatomia de um `devcontainer.json` Otimizado para Agentes

A configuração padrão de um DevContainer é insuficiente para fluxos de trabalho agênticos avançados. O container deve ser "preparado" (primed) com um conjunto específico de ferramentas de linha de comando (CLI) que o agente utilizará para interagir com o sistema. A análise dos requisitos sugere uma configuração que injeta binários para controle de versão, especificação e linting semântico diretamente na imagem.

#### 2.1.1 Injeção de Ferramentas e "Features"

Utilizamos o recurso de "Features" da especificação DevContainer para adicionar capacidades sem inflar a complexidade do Dockerfile. As ferramentas críticas incluem:

- **Git & GitHub CLI (`gh`):** Essencial para que o agente possa gerenciar o controle de versão de forma autônoma, criando branches, abrindo Pull Requests (PRs) e lendo issues sem intervenção humana.
- **Node.js & Python:** Runtimes necessários para a maioria das ferramentas de orquestração (OpenSpec é baseado em Node; muitos servidores MCP e ferramentas como Semgrep são baseados em Python).
- **Docker-in-Docker (DinD):** Permite que o agente suba serviços efêmeros (bancos de dados, caches) para testar a aplicação que está construindo, criando um ambiente de teste isolado dentro do ambiente de desenvolvimento.

#### 2.1.2 Persistência de Memória e Montagens (Bind Mounts)

Um aspecto crítico, frequentemente negligenciado, é a persistência da memória do agente entre reconstruções do container. Se o container for recriado, o banco de dados local do Beads (SQLite) e os índices de contexto não podem ser perdidos. A configuração deve incluir montagens explícitas (`mounts`) para vincular os diretórios `.beads` e `.ai-context` do sistema de arquivos hospedeiro para dentro do container. Isso garante que a "memória de longo prazo" do agente sobreviva ao ciclo de vida da infraestrutura.

**Tabela 1: Componentes Essenciais do DevContainer Agêntico**

| **Componente**          | **Função no Ecossistema**     | **Justificativa para o Semi-Leigo**                                           | **Fontes** |
| ----------------------- | ----------------------------- | ----------------------------------------------------------------------------- | ---------- |
| **Imagem Base**         | `typescript-node:20-bullseye` | Base estável e compatível com a maioria das ferramentas modernas.             |            |
| **Feature: GitHub CLI** | Automação de PRs e Issues     | Permite ao agente interagir com o GitHub sem que o usuário use o terminal.    |            |
| **Feature: DinD**       | Testes de Integração          | O agente pode subir um banco de dados real para testar o código que escreveu. |            |
| **Mount:.beads**        | Persistência de Tarefas       | Garante que o plano de trabalho não seja apagado ao reiniciar o ambiente.     |            |
| **PostCreateCommand**   | Bootstrapping Automático      | Instala ferramentas automaticamente, eliminando a configuração manual.        |            |

### 2.2 Automação do Bootstrapping

Para o semi-leigo, a experiência deve ser "zero-config". Isso é alcançado através de um script de inicialização (`agent-bootstrap.sh`) invocado pelo `postCreateCommand`. Este script deve ser idempotente e resiliente, garantindo que todas as ferramentas de suporte—Beads, OpenSpec, Semgrep—estejam instaladas e configuradas antes que o usuário digite o primeiro prompt.

```Bash
#!/bin/bash
# Exemplo conceitual do script de bootstrapping
echo "Inicializando o Ambiente Agêntico..."

# 1. Instalação do Beads (Memória)
if! command -v bd &> /dev/null; then
    curl -fsSL https://raw.githubusercontent.com/steveyegge/beads/main/scripts/install.sh | bash
    bd init # Inicializa o banco de dados SQLite local
fi

# 2. Instalação do OpenSpec (Especificação)
npm install -g @fission-ai/openspec
if [! -f "openspec.yaml" ]; then
    openspec init --yes # Cria a estrutura de pastas padrão
fi

# 3. Preparação do Contexto
mkdir -p.ai-context
# (Scripts adicionais para gerar mapas de símbolos seriam chamados aqui)
```

Essa automação transforma a complexidade da configuração de ferramentas em um detalhe de implementação invisível para o usuário final.

---

## 3. O Sistema Límbico do Agente: Gerenciamento de Memória com Beads

A maior falha dos fluxos de trabalho baseados em chat é a volatilidade. Planos escritos em arquivos Markdown (`PLANS.md` ou `TODO.md`) são passivos; eles exigem que o agente leia, interprete e atualize o texto manualmente, o que consome tokens preciosos e frequentemente leva a estados dessincronizados. **Beads** surge como a solução definitiva para este problema, fornecendo um rastreador de problemas (issue tracker) baseado em grafos que reside nativamente dentro do repositório git.

### 3.1 A Teoria do Grafo de Tarefas vs. Listas Lineares

Diferente de uma lista linear de tarefas, o desenvolvimento de software é um grafo de dependências. A tarefa "Criar Tela de Login" depende da tarefa "Criar API de Autenticação", que por sua vez depende da tarefa "Definir Schema do Usuário". O Beads modela explicitamente essas relações (`blocks`, `blocked_by`, `parent_child`). Isso permite uma funcionalidade crítica para a automação: a detecção de trabalho pronto (`bd ready`). Quando um agente consulta o Beads, ele não vê uma lista de 50 coisas a fazer; ele vê apenas as 3 tarefas que estão desbloqueadas e prontas para execução. Isso foca a atenção do modelo (mechanistic attention), reduzindo alucinações e paralisia de decisão.

### 3.2 Persistência Nativa no Git e IDs Seguros

Para o semi-leigo que confia no controle de versão como uma "rede de segurança", o Beads oferece uma vantagem arquitetural única: o banco de dados é serializado em arquivos JSONL (`issues.jsonl`) dentro da pasta `.beads`. Isso significa que o estado das tarefas viaja junto com o código. Se o usuário reverter um commit mal sucedido, as tarefas associadas a ele voltam ao estado "aberto" automaticamente. Além disso, o uso de IDs baseados em hash (ex: `bd-a1b2`) previne conflitos de merge quando múltiplos agentes trabalham em paralelo, uma característica essencial para a orquestração multi-agente do Antigravity.

### 3.3 A Habilidade de "Explosão de Tarefas" (Task Explosion)

Configurar tarefas manualmente no terminal (`bd create...`) é tedioso e técnico. A solução proposta é criar uma **Skill do Antigravity** que automatiza esse processo. O usuário fornece um objetivo de alto nível (uma "Épica"), e o agente utiliza essa habilidade para decompor o objetivo em tarefas granulares no Beads.

**O Fluxo de Automação:**
1. **Entrada do Usuário:** "Quero um sistema de upload de imagens."
2. **Análise Agêntica:** O agente (configurado com uma persona de Arquiteto) analisa os requisitos implícitos: Frontend (botão, drag-and-drop), Backend (endpoint API), Armazenamento (S3/Blob), Banco de Dados (registro de metadados).
3. **Execução da Skill:** O agente gera um script ou executa comandos `bd` em sequência:
    - Cria a tarefa pai "Sistema de Upload".
    - Cria as tarefas filhas e estabelece as dependências (Backend bloqueia Frontend).
4. **Resultado:** O usuário vê, instantaneamente, um grafo de trabalho estruturado e priorizado, sem ter digitado um único comando CLI.

Essa abordagem "Task Explosion" é citada como uma das práticas mais eficazes para manter agentes focados em tarefas de longo horizonte.

---

## 4. Engenharia de Contexto Estruturado: O Contrato de Intenção

A "Vibe Coding"—pedir ao agente para "fazer algo legal"—funciona para protótipos, mas falha em produção. Para usuários semi-leigos construírem sistemas robustos, é necessário impor restrições cognitivas ao agente. Isso é feito através de **Contexto Estruturado**, utilizando **OpenSpec** para requisitos e **ADRs** para decisões arquiteturais.

### 4.1 OpenSpec: Definindo o "O Que" Antes do "Como"

O OpenSpec é um framework que formaliza o processo de "Spec-Driven Development" (SDD) para agentes. Ele substitui o prompt vago por um ciclo de vida de artefatos rigoroso: `Proposal` → `Spec` → `Design` → `Tasks`.

Para o usuário semi-leigo, o OpenSpec atua como um contrato de segurança. O agente é proibido (via regras do sistema) de escrever código até que o artefato de especificação (`spec.md`) seja aprovado pelo humano.

- **Comando `/opsx:ff` (Fast-Forward):** Esta funcionalidade é crucial para a usabilidade. Ela permite que o usuário dê um prompt conversacional e o agente gere automaticamente todos os artefatos de planejamento (Proposta, Especificação, Design e Tarefas) de uma só vez. O usuário apenas revisa e aprova.
- **Verificação (`/opsx:verify`):** Após a implementação, o OpenSpec verifica se o código atende aos requisitos definidos nos artefatos. Isso fornece ao usuário não técnico uma validação automatizada de que o "contrato" foi cumprido.

### 4.2 Architecture Decision Records (ADRs): A Memória Institucional

Enquanto o OpenSpec lida com funcionalidades, os ADRs lidam com escolhas técnicas (ex: "Usar PostgreSQL ou Mongo?", "Usar Tailwind ou CSS Modules?"). Em um ambiente automatizado, é vital que o agente registre essas escolhas para evitar debates circulares no futuro. Utilizamos o template **MADR** (Markdown Architectural Decision Record) por sua simplicidade e legibilidade por máquinas. Uma regra no `AGENTS.md` instrui o agente a consultar a pasta `docs/decisions/` antes de sugerir novas tecnologias, garantindo consistência arquitetural.

### 4.3 Manutenção Dinâmica de Contexto: "Reverse Documentation"

Agentes "cegos" que não conhecem a base de código alucinam. Para mitigar isso, implementamos uma automação de "Documentação Reversa".

- **Mapeamento de Símbolos (`symbols.md`):** Um script (usando `ctags` ou `tree-sitter`) roda em background e gera um mapa comprimido de todas as funções, classes e variáveis do projeto. O agente lê este arquivo para "entender" a estrutura do código sem precisar abrir milhares de arquivos, economizando tokens e aumentando a precisão.
- **Glossário Dinâmico (`glossary.md`):** Outro agente varre os comentários de código e a documentação para manter um glossário atualizado de termos de domínio (ex: "O que é um 'Lead' neste sistema?"). Isso alinha a terminologia entre o usuário e a máquina.

---

## 5. Protocolos de Orquestração: Workflows Socráticos, TDD e Git

A eficácia do semi-leigo depende da qualidade dos fluxos de trabalho (workflows) que o sistema impõe. Não basta ter as ferramentas; é preciso ter um "modo de operação" que guie o usuário do problema à solução.

### 5.1 O Fluxo de Descoberta Socrática (The Socratic Discovery)

Um erro comum é o agente "Yes-Man" (Sim-Senhor), que obedece cegamente a um pedido mal formulado do usuário, gerando código inútil. O fluxo Socrático inverte essa dinâmica: o agente é configurado para **interrogar** o usuário antes de agir.

**Implementação Técnica:**

Criamos uma Skill (`socratic-plan`) que instrui o agente a assumir a persona de um Gerente de Produto Sênior.

- **Gatilho:** O usuário diz "Tenho uma ideia para um app de tarefas".
- **Resposta do Agente:** Em vez de gerar código, o agente pergunta: "Quem é o usuário alvo? As tarefas devem ser colaborativas? Precisamos de suporte offline? Qual é o MVP?".
- **Objetivo:** Refinar a ideia vaga até que ela esteja clara o suficiente para ser convertida em um artefato OpenSpec robusto. Estudos mostram que essa abordagem "maiêutica" melhora drasticamente a qualidade do raciocínio da IA em tarefas complexas.

### 5.2 O Ciclo de Segurança: Test-Driven Development (TDD)

Para um usuário que não sabe ler código fluentemente, os testes automatizados são a única fonte confiável de verdade. O fluxo TDD (Test-Driven Development) é imposto como regra.

**O Ciclo Automatizado:**

1. **Red:** O agente cria um teste que falha, descrevendo o comportamento esperado (baseado no OpenSpec).
2. **Green:** O agente escreve o código mínimo para fazer o teste passar.
3. **Refactor:** O agente limpa o código, mantendo o teste verde.
4. **Report:** O agente informa ao usuário: "Implementei a funcionalidade. 3 testes criados, todos passando."

Isso transfere a confiança do usuário da _sintaxe_ (que ele não domina) para o _semáforo dos testes_ (verde/vermelho), que é universalmente compreensível.

### 5.3 Automação de Git via MCP (Model Context Protocol)

O **Model Context Protocol (MCP)** é o tecido conectivo que permite ao agente interagir com ferramentas externas de forma segura e estruturada. No contexto do Antigravity, utilizamos o servidor MCP do GitHub para automatizar o ciclo de entrega.

Diferente do uso da CLI do git (que pode ser frágil), o MCP permite que o agente "entenda" o repositório semanticamente.

- **Fluxo:** Ao completar uma tarefa do Beads, o agente (via MCP) comita as alterações com uma mensagem semântica (ex: `feat: add user login (closes bd-123)`), faz o push e abre um Pull Request.
- **Revisão:** O agente pode até usar o MCP para ler o diff do PR e fazer uma auto-crítica antes de solicitar a revisão humana, fechando o ciclo de desenvolvimento sem que o usuário precise sair do IDE.

---

## 6. A Interface de Comando: Configurando o Google Antigravity

O Google Antigravity distingue-se por sua arquitetura de interface dupla: o **Editor View** (para codificação síncrona) e o **Manager Surface** (para orquestração assíncrona). Para o semi-leigo, o Manager Surface é o painel de controle principal.

### 6.1 Configurando o "Manager Surface"

O Manager Surface permite instanciar múltiplos agentes operando em paralelo. A configuração recomendada envolve a criação de agentes especializados, definidos por suas permissões e contextos:

1. **Agente Planejador (The Planner):** Focado em Beads e OpenSpec. Tem acesso de leitura/escrita aos arquivos de documentação, mas acesso apenas de leitura ao código. Sua função é manter o plano atualizado.
2. **Agente Construtor (The Builder):** Focado na execução. Tem acesso ao terminal e ao editor. Ele pega tarefas "prontas" do Beads e executa o ciclo TDD.
3. **Agente Auditor (The Reviewer):** Focado em qualidade. Roda ferramentas de análise estática (Semgrep) e verifica a conformidade com os ADRs.

### 6.2 Definindo Habilidades (Skills)

As "Skills" no Antigravity são pacotes de instruções e scripts que estendem as capacidades do agente. Elas residem na pasta `.agent/skills/`.

## **Exemplo de Configuração da Skill "Task Exploder":** 

Cria-se um arquivo `.agent/skills/task-exploder/SKILL.md` contendo:

```
## name: task-exploder description: Decompõe um requisito de alto nível em tarefas atômicas no Beads.

# Instruções

1. Analise o requisito do usuário e identifique componentes (Frontend, Backend, DB).
2. Para cada componente, gere um comando `bd create` com prioridade adequada.
3. Utilize `bd dep add` para garantir que tarefas de Backend bloqueiem as de Frontend.
4. Apresente o plano visual usando `bd tree` antes de confirmar. Esta definição simples permite que o usuário invoque essa lógica complexa apenas dizendo "Exploda essa tarefa" no chat.
```

---

## 7. Integração e Automação: O Protocolo MCP e Git

A integração profunda com o GitHub através do MCP é o que transforma o ambiente de "local" para "colaborativo". O servidor MCP atua como uma camada de abstração sobre a API do GitHub.

### 7.1 Configuração do Servidor MCP

No arquivo de configuração do Antigravity (geralmente `.antigravity/config.json` ou similar, dependendo da versão de preview), define-se a conexão com o servidor MCP:

```JSON
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "${env:GITHUB_TOKEN}"
      }
    }
  }
}
```

Esta configuração, injetada via DevContainer, garante que o agente tenha as credenciais e as ferramentas necessárias para operar o repositório remotamente.

### 7.2 Guardrails de Qualidade: Semgrep e Linting Semântico

Para garantir que o código gerado pelo agente seja seguro e arquiteturalmente sólido, integramos o **Semgrep**. Diferente de linters tradicionais que focam em formatação, o Semgrep foca em lógica e segurança.

- **Regras Customizadas:** Podemos criar regras no `.semgrep.yml` que proíbem padrões perigosos (ex: SQL Injection, chaves hardcoded) ou arquiteturalmente inválidos (ex: "A camada de View não pode importar a camada de Database diretamente").
- **Auto-Correção:** O agente é instruído a rodar o Semgrep antes de qualquer commit. Se houver erros, ele deve corrigi-los autonomamente. Isso cria um loop de feedback rápido que impede a entrada de código ruim na base.

---

## 8. Estudo de Caso Longitudinal: Da Ideia ao Deploy

Para ilustrar a eficácia desta arquitetura, apresentamos um passo-a-passo narrativo de como um usuário semi-leigo utilizaria este sistema para criar uma nova funcionalidade.

### Fase 1: Descoberta e Planejamento (Socrático)

O usuário abre o **Manager Surface** e invoca o Agente Planejador: _"Quero adicionar um modo noturno ao painel."_

O agente, ativando a skill **Socrática**, pergunta: _"O modo noturno deve seguir a preferência do sistema operacional ou ter um controle manual? Deve persistir entre sessões?"_

Após a resposta do usuário, o agente usa o **OpenSpec** para gerar o `proposal.md`. O usuário revisa e aprova com um simples "Ok".

### Fase 2: Arquitetura e Explosão (Beads)

Com a especificação aprovada, o agente invoca a skill **Task Exploder**. Ele traduz o documento de texto em 3 tarefas no Beads:

1. `bd-x1`: "Criar variáveis CSS para temas" (P0).
2. `bd-x2`: "Implementar Contexto React para Tema" (P1, bloqueado por x1).
3. `bd-x3`: "Adicionar Toggle na UI" (P2, bloqueado por x2).

O usuário visualiza este grafo e confirma.

### Fase 3: Execução e Teste (TDD)

O usuário muda para o **Editor View** e ativa o Agente Construtor para a tarefa `bd-x1`.

O agente segue o ciclo TDD: escreve um teste para verificar se as variáveis CSS são aplicadas, implementa o CSS, roda o teste (Passou), e roda o Semgrep (Passou).

O agente então marca `bd-x1` como concluída no Beads. Automaticamente, `bd-x2` torna-se "ready" (pronta).

### Fase 4: Entrega (MCP/Git)

Após completar todas as tarefas, o agente usa o MCP para criar um branch `feat/dark-mode`, comita as mudanças vinculando aos IDs do Beads, e abre um PR no GitHub com uma descrição gerada a partir do OpenSpec original. O usuário semi-leigo apenas clica em "Merge" na interface do GitHub.

---
## 9. Conclusão e Perspectivas Futuras

A configuração detalhada neste relatório transforma o Google Antigravity de um simples editor de código em um ecossistema de desenvolvimento integrado e semiautônomo. Ao combinar a persistência estruturada do **Beads**, a reprodutibilidade dos **DevContainers**, a clareza contratual do **OpenSpec** e a inteligência procedimental dos **Agentes Socráticos**, capacitamos o usuário semi-leigo a atuar como um arquiteto de software competente.

Este framework não apenas democratiza o desenvolvimento de software, mas também estabelece um padrão de qualidade e rigor que muitas vezes supera o de equipes humanas tradicionais (propensas a esquecer testes ou documentação). À medida que os modelos de IA (como Gemini 3 e sucessores) evoluem, a "fricção" restante—a necessidade de aprovar planos ou revisar especificações—diminuirá, aproximando-nos cada vez mais da visão de software desenvolvido puramente através da intenção e da linguagem natural.

---

### Apêndice Técnico: Script de Inicialização Mestre

Abaixo, consolida-se a lógica de instalação em um script unificado (`init-agent-env.sh`) para ser executado no setup do projeto.

```Bash
#!/bin/bash
set -e

echo ">>> Iniciando Configuração do Ambiente Agêntico Antigravity..."

# 1. Instalar Beads (Memória)
echo "[1/5] Instalando Beads..."
curl -fsSL https://raw.githubusercontent.com/steveyegge/beads/main/scripts/install.sh | bash
export PATH=$PATH:$HOME/go/bin
if [! -d ".beads" ]; then
    bd init
    echo "   -> Banco de dados Beads inicializado."
fi

# 2. Instalar OpenSpec (Contexto)
echo "[2/5] Instalando OpenSpec..."
npm install -g @fission-ai/openspec
if [! -f "openspec.yaml" ]; then
    openspec init --yes
    echo "   -> Estrutura OpenSpec criada."
fi

# 3. Configurar Skills do Agente
echo "[3/5] Configurando Skills..."
mkdir -p.agent/skills/socratic-plan
mkdir -p.agent/skills/tdd-cycle
# (Aqui seriam copiados os arquivos SKILL.md templates para as pastas)
echo "   -> Skills 'socratic-plan' e 'tdd-cycle' injetadas."

# 4. Gerar Mapas de Contexto Iniciais
echo "[4/5] Gerando mapas de contexto..."
mkdir -p.ai-context
# Exemplo simplificado de geração de símbolos
ctags -R -f.ai-context/tags --languages=TypeScript,JavaScript,Python. 2>/dev/null |

| echo "   -> Aviso: ctags não encontrado, pule."
echo "   -> Contexto inicial preparado."

# 5. Configurar Guardrails (Semgrep)
echo "[5/5] Configurando Semgrep..."
pip install semgrep > /dev/null
if [! -f ".semgrep.yml" ]; then
    semgrep --init > /dev/null
    echo "   -> Regras de segurança Semgrep aplicadas."
fi

echo ">>> AMBIENTE PRONTO. Reinicie o Antigravity para carregar o novo contexto."
```

Este script, quando combinado com a definição do DevContainer, entrega a promessa de um ambiente "pronto para voar" para qualquer usuário, independentemente de sua profundidade técnica.