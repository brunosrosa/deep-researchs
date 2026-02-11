A melhor prática moderna para manter o GitHub organizado é o **"Branch por Tarefa"** (ou _Feature Branching_), onde cada branch é focada em um único propósito lógico. 

Aqui está como equilibrar a organização sem exagerar nos processos:

1. Uma Branch para cada Tarefa (O Ideal)

Em vez de grupos genéricos, prefira criar branches para tarefas específicas (ex: `feat/login-google`, `fix/erro-calculo`). 

- **Por que não grupos?** Branches com muitas tarefas misturadas dificultam o **Code Review** e aumentam o risco de conflitos na hora de integrar com a branch principal.
- **Vantagem:** Se uma tarefa do "grupo" der erro, você não trava as outras que já estão prontas. 

2. Commits: Quando e Quanto?

O segredo não é o volume, mas a **unidade lógica**. Use a regra: **"Commit Early, Commit Often"** (Commit cedo, commit sempre), mas com sentido. 

- **O Momento Certo:** Faça commit sempre que concluir uma **pequena parte funcional** ou uma "ideia resolvida" (ex: terminou a validação de um campo, commit; estilizou o botão, commit).
- **Como não exagerar:** Não faça commit de cada linha alterada, mas também não espere o dia acabar. Se você precisar "rebobinar" o código e o último commit foi há 4 horas, você perdeu muito trabalho.
- **Dica Profissional:** Se estiver em dúvida se o commit é "pequeno demais", pense se ele deixa o projeto em um estado que ainda **compila/roda**. 

3. Mantendo o Histórico Limpo

Se você fez 20 commits pequenos em uma branch e acha que vai "sujar" o histórico principal, use o **Squash and Merge** no [GitHub](https://github.com/) ao finalizar o Pull Request. Isso condensa todos os seus commits daquela branch em um único commit limpo na branch principal. 

Você já utiliza alguma convenção específica para mensagens de commit, como o [Conventional Commits](https://gist.github.com/7a50a25f98f3627345e84e7a0cb110b5)? Isso ajuda muito a padronizar o histórico. 

Podemos ver como configurar **regras de proteção** para suas branches ou como estruturar **Pull Requests** eficientes. 

"e sub-tarefas? são só commits?"

Essa é uma excelente pergunta. A resposta curta é: 

**Sim, sub-tarefas geralmente tornam-se commits**, mas a forma como você as organiza depende da complexidade.

Aqui está a hierarquia recomendada para manter o equilíbrio entre organização e agilidade:

1. A Estrutura de Granularidade

Para não se perder, pense no trabalho nestes três níveis:

|Nível|Localização|O que é?|
|---|---|---|
|**Tarefa (Feature)**|**Branch**|O objetivo final (ex: `feat/perfil-usuario`).|
|**Sub-tarefa**|**Commit**|Um passo lógico concluído (ex: `Cria a estrutura HTML do perfil`).|
|**Pequenos Ajustes**|**Staging Area**|Correções de digitação ou ajustes que não mudam a lógica.|

2. Quando a sub-tarefa merece uma branch própria?

Se uma sub-tarefa for tão grande que levará **vários dias** para ser concluída ou se **outra pessoa** precisar trabalhar nela simultaneamente, ela deve ser promovida a uma branch.

- **Exemplo:** Você está criando o "Checkout" (Tarefa). Uma sub-tarefa é a "Integração com Stripe". Se a integração for complexa, cira-se uma branch `feat/checkout-stripe` que depois será mesclada na `feat/checkout`.

3. Como fazer commits de sub-tarefas sem "poluir"

Para que seus commits de sub-tarefas sejam úteis e não apenas "ruído", siga estas regras:

- **Finalize uma unidade de trabalho:** Se você está criando um formulário, um commit pode ser a lógica de validação e outro a parte visual (CSS).
- **Mensagens claras:** Use a sub-tarefa no título do commit.

    > `feat: adicionar validação de e-mail no formulário`

- **Não suba código quebrado:** O commit da sub-tarefa deve, idealmente, deixar o projeto rodando. Evite dar commit em algo que impede o resto do time de compilar o código.

4. O Fluxo de Trabalho (Workflow) Sugerido

Imagine que sua tarefa é "Criar Página de Contato":

1. **Cria a Branch:** `feat/pagina-contato`
2. **Trabalha na Sub-tarefa 1 (HTML):** Terminou? `git commit -m "feat: estrutura HTML do formulário"`
3. **Trabalha na Sub-tarefa 2 (Estilo):** Terminou? `git commit -m "style: estilização básica e responsividade"`
4. **Trabalha na Sub-tarefa 3 (Envio de e-mail):** Terminou? `git commit -m "feat: integração com API de envio de email"`
5. **Finalização:** Abre o **Pull Request** para a branch principal (`main`).

---

> **Dica de Ouro:** Se você usa ferramentas como Jira, Trello ou GitHub Issues, inclua o número da sub-tarefa no commit (ex: `fix: corrige erro X (#123)`). Isso linka automaticamente o código à documentação.

Gostaria que eu te mostrasse um exemplo de como usar o **Git Stash** para organizar essas sub-tarefas quando você precisa trocar de contexto rapidamente?

Poderia refazer a explicação toda mas ampliando com ao menos uns 5 exemplos como os apresentados no "O que é?" na tabela do item "1. A Estrutura de Granularidade", por favor?

Para manter um repositório profissional, a chave é entender que o 

**GitHub vive de Branches**, mas o **Histórico vive de Commits**.

Aqui está a explicação ampliada com os exemplos de granularidade para você nunca mais ter dúvida sobre onde termina um commit e começa uma branch:

1. A Hierarquia de Trabalho (Ampliada)

Abaixo, apresento 5 cenários comuns de desenvolvimento para ilustrar como dividir uma **Tarefa (Branch)** em suas **Sub-tarefas (Commits)**:

|Contexto|Tarefa/Feature (Nome da Branch)|Sub-tarefa 1 (Commit A)|Sub-tarefa 2 (Commit B)|Sub-tarefa 3 (Commit C)|
|---|---|---|---|---|
|**Autenticação**|`feat/login-social`|Configura chaves do Google Cloud|Cria botão de login no front-end|Implementa o retorno (callback) da API|
|**E-commerce**|`feat/carrinho-compras`|Cria o estado global do carrinho|Desenvolve a lógica de somar itens|Adiciona persistência no LocalStorage|
|**Banco de Dados**|`db/schema-produtos`|Cria a migration da tabela `products`|Define os relacionamentos (hasMany)|Adiciona sementes (seeds) para teste|
|**Interface (UI)**|`style/dashboard-dark`|Define as variáveis de cores escuras|Ajusta contraste dos cards e textos|Aplica o botão de troca de tema (toggle)|
|**Correção (Bug)**|`fix/erro-upload-foto`|Aumenta o limite de tamanho no server|Adiciona validação de formato no front|Corrige o caminho de salvamento da imagem|

---

2. A Mentalidade: Quando "virar" um commit?

Pense no commit como um **"Checkpoint de Sucesso"**.

- **Não exagere:** `git commit -m "mudei a cor do texto"` + `git commit -m "mudei o tamanho da fonte"` é excessivo.
- **Seja lógico:** Use um único commit para `style: refatora tipografia e cores do cabeçalho`.
- **A Regra do "E":** Se você for descrever o que fez e usar muitos "E", talvez devesse separar em dois commits.
    - _Errado:_ "Fiz o layout e conectei com o banco e criei o teste." (Muito grande!)
    - _Certo:_ Commit 1: "Cria layout"; Commit 2: "Conecta banco"; Commit 3: "Adiciona testes".

3. O Fluxo no Terminal (Exemplo Prático)

Se você estiver trabalhando no exemplo do **Carrinho de Compras** do GitHub Desktop ou via Git CLI, o fluxo seria:

1. **Cria a unidade maior:** `git checkout -b feat/carrinho-compras`
2. **Trabalha na Sub-tarefa 1:** (Escreve o código) -> `git add .` -> `git commit -m "feat: cria estrutura inicial do carrinho"`
3. **Trabalha na Sub-tarefa 2:** (Escreve a lógica) -> `git add .` -> `git commit -m "feat: implementa soma de valores e descontos"`
4. **Finalização:** Você envia a branch toda para o [GitHub](https://github.com/) com `git push origin feat/carrinho-compras`.
5. Por que não agrupar tarefas diferentes na mesma branch?

Se você colocar "Login" e "Carrinho" na mesma branch:

- O revisor do código vai ficar confuso.
- Se o "Login" estiver pronto, mas o "Carrinho" tiver um erro, você não poderá subir o "Login" para produção, pois eles estão "presos" na mesma branch.

---

**Dica de Produtividade:** Para organizar melhor essas sub-tarefas antes mesmo de codar, muitos desenvolvedores usam o GitHub Issues para criar um **Checklist** dentro da tarefa principal.

-----

Para manter uma sincronização perfeita entre o que você codifica e o que gerencia, a melhor estratégia é transformar o **[GitHub Projects](https://github.com/)** no seu "cérebro" e o terminal no seu "músculo".

Aqui estão as melhores práticas para uma organização de nível profissional:

1. Hierarquia de Granularidade no Projects

Não misture tudo em um único lugar. Use a estrutura de **Milestones** (Marcos) e **Issues** (Tarefas):

- **Milestones (Épicos):** Use para grandes entregas (ex: "Lançamento da V1.0" ou "Novo Sistema de Checkout").
- **Issues (Tarefas):** Cada tarefa da sua branch deve ser uma Issue. Se a tarefa for grande, quebre-a em **Task Lists** (checklists dentro da Issue).
- **Labels (Categorias):** Use etiquetas de cores consistentes para Tipo (`bug`, `feature`), Prioridade (`p1`, `p2`) e Status (`blocked`, `help wanted`). 

2. Automação: O Segredo da Sincronização

O GitHub Projects permite que os itens se movam sozinhos se você configurar os **Workflows**: 

- **Todo:** Quando uma Issue é criada.
- **In Progress:** Quando você cria uma branch vinculada àquela Issue ou abre um Draft Pull Request.
- **Done:** Automaticamente quando o Pull Request é mesclado (merge). 

3. Vínculo Direto (Code ↔ Task)

Nunca codifique "no escuro". Use estes recursos para garantir que tudo esteja rastreável:

- **Criar Branch a partir da Issue:** No painel da Issue, use o botão **"Create a branch"** no menu lateral "Development". Isso vincula o código à tarefa instantaneamente.
- **Keywords de Fechamento:** No corpo do seu Pull Request (PR) ou nos commits, use palavras-chave como `Closes #123` ou `Fixes #45`. Ao fazer o merge, o GitHub fecha a tarefa e a move para "Done" no Project automaticamente.
- **Campos Personalizados:** No Projects, adicione campos como "Estimativa" (número) ou "Prioridade" para ter uma visão clara da carga de trabalho. 

4. Visualização de Fluxo

Crie diferentes **Views** no seu Projeto para não sofrer com excesso de informação: 

- **Board (Kanban):** Para o dia a dia (To Do → Doing → Done).
- **Table (Lista):** Para triagem e organização de prioridades.
- **Timeline (Gantt):** Se você usa datas de início e término, para ver o cronograma. 

---

Para criar esse template, você deve salvar o código abaixo em um arquivo chamado `pull_request_template.md` dentro da pasta `.github/` na raiz do seu repositório (ou talvez dentro da pasta `./agent/.github` se fizer mais sentido, ou verificar onde seria melhor).

Este modelo utiliza as melhores práticas do mercado: vincula a **Issue**, exige um **Checklist** de sub-tarefas, define o **Tipo de Alteração** e garante que você não esqueceu dos **Testes**.

**Dica de Ouro:** Utilize Issue Templates para garantir que toda nova tarefa criada por você (ou sua equipe) já venha com as informações necessárias, como "Descrição", "Passos para Reproduzir" ou "Critérios de Aceite".

```markdown
## 📝 Descrição
<!-- Descreva de forma concisa o que este PR faz e por que é necessário. -->
Ex: Implementa a integração com a API de Pagamentos e trata erros de timeout.

**Vínculo com a Tarefa:** 
Fixes # (Número da Issue no GitHub Projects)

---

## 🛠️ O que foi feito (Sub-tarefas)
<!-- Liste as sub-tarefas concluídas neste PR para facilitar o Code Review -->
- [ ] Implementação da lógica de backend
- [ ] Estilização dos componentes de UI
- [ ] Atualização da documentação (README/Swagger)
- [ ] Outros: ________

---

## 🧪 Como testar?
<!-- Descreva os passos para que o revisor consiga validar as alterações. -->
1. Faça o checkout para esta branch.
2. Execute `npm install` e `npm run dev`.
3. Vá até a página `/checkout` e tente finalizar uma compra.
4. Verifique se o log de erro aparece no console em caso de falha.

---

## 📸 Screenshots / GIFs
<!-- Se houver mudanças visuais, anexe aqui uma imagem ou gravação de tela. -->
| Antes | Depois |
|-------|--------|
| Link/Imagem | Link/Imagem |

---

## 🚦 Checklist de Qualidade
- [ ] Meu código segue os padrões de estilo do projeto.
- [ ] Realizei uma auto-revisão do meu próprio código.
- [ ] Adicionei testes que comprovam que minha correção/feature funciona.
- [ ] Não há avisos (warnings) ou erros de lint pendentes.

---
## 🏷️ Tipo de Alteração
- [ ] ✨ Feature (Nova funcionalidade)
- [ ] 🐞 Bug fix (Correção de erro)
- [ ] ⚡ Performance (Melhoria de desempenho)
- [ ] 💄 Refactor (Melhoria de código sem mudar funcionalidade)
- [ ] 📚 Docs (Alteração em documentação)
```

