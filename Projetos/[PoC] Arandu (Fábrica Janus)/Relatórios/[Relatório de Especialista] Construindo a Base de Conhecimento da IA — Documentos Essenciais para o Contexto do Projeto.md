---
sticker: lucide//brain-circuit
---
## Relatório de Especialista: Construindo a Base de Conhecimento da IA — Documentos Essenciais para o Contexto do Projeto

Para elevar um agente de IA de um simples executor de tarefas a um verdadeiro colaborador estratégico, é imperativo fornecer-lhe um contexto rico e estruturado. Enquanto o arquivo `project_rules.md` define as "regras do jogo", a verdadeira inteligência e alinhamento surgem quando a IA tem acesso a uma biblioteca de documentos que codificam o conhecimento, as decisões e o estado atual do projeto.

Esta prática, conhecida como "aterramento" (grounding), ancora o conhecimento da IA na realidade do seu repositório, em vez de depender de seus dados de treinamento genéricos. A seguir, detalhamos os documentos mais importantes que você deve criar e manter para construir uma base de conhecimento robusta para seus agentes no Trae IDE.

### 1. O Documento de Requisitos do Produto (PRD.md) — A Estrela-Guia

Antes de escrever uma única linha de código, todo projeto precisa de uma direção clara. O PRD é essa direção, um documento de alto nível que serve como a fonte única da verdade sobre o propósito, os recursos e os objetivos do projeto. Fornecer este documento à IA garante que todas as suas ações estejam alinhadas com a visão do produto.  

**Conteúdo Essencial:**

- **Propósito e Visão:** Uma declaração concisa sobre o que o produto faz e qual problema ele resolve.
    
- **Público-Alvo:** Descrição dos usuários para quem o produto está sendo construído.
    
- **Pilha de Tecnologia (Tech Stack):** As principais linguagens, frameworks e serviços a serem utilizados.
    
- **Histórias de Usuário:** Descrições curtas de funcionalidades da perspectiva do usuário (ex: "Como um usuário, eu quero poder me cadastrar com meu e-mail para acessar o aplicativo").
    
- **Recursos-Chave:** Uma lista detalhada das funcionalidades (ex: "Painel de controle com arrastar e soltar", "Autenticação via OAuth2 com Google").
    
- **Requisitos Não Funcionais:** Critérios como performance, segurança, escalabilidade e design responsivo.
    
- **O que está Fora do Escopo:** Definir explicitamente o que _não_ será construído para manter o foco.
    

**Exemplo de Estrutura (`PRD.md`):**

# Documento de Requisitos do Produto: Gestor de Tarefas Diárias

**1. Propósito:** Construir um aplicativo web intuitivo que ajuda freelancers a organizar, priorizar e acompanhar suas tarefas diárias em um painel visual estilo Kanban.

**2. Pilha de Tecnologia:**

- **Frontend:** Next.js, TypeScript, Tailwind CSS
    
- **Backend:** FastAPI (Python), PostgreSQL
    
- **Hospedagem:** Vercel
    

**3. Histórias de Usuário:**

- Como usuário, quero criar uma nova tarefa com título e descrição.
    
- Como usuário, quero arrastar uma tarefa entre as colunas "A Fazer", "Em Andamento" e "Concluído".
    
- Como usuário, quero editar o conteúdo de uma tarefa existente.
    

**4. Recursos-Chave:**

- Painel com três colunas para status de tarefas.
    
- Persistência de dados em um banco de dados PostgreSQL.
    
- Interface com design "mobile-first".
    

**5. Fora do Escopo (Versão 1.0):**

- Login com múltiplos usuários.
    
- Notificações por e-mail.
    
- Anexos de arquivos nas tarefas.
    

### 2. O Plano de Projeto (`STATUS.md` ou `CHANGELOG.md`) — A Memória Dinâmica

Enquanto o PRD é estratégico e relativamente estático, o agente de IA precisa de um documento tático que rastreie o progresso diário. Um arquivo `STATUS.md` ou similar funciona como a memória de curto prazo do projeto, informando à IA o que já foi feito, o que está em andamento e quais são os próximos passos.  

**Conteúdo Essencial:**

- **Objetivo Atual:** O recurso ou épico que está sendo desenvolvido no momento.
    
- **Tarefas Concluídas:** Uma lista de verificação (checklist) dos itens que já foram implementados e testados.
    
- **Próximos Passos:** As tarefas imediatas que precisam ser abordadas.
    
- **Bloqueios ou Questões:** Qualquer obstáculo ou pergunta que precise de atenção.
    

**Exemplo de Estrutura (`STATUS.md`):**

# Status da Feature: Autenticação com Google (OAuth2)

**Última Atualização:** 08/07/2025

## Concluído

- [x] Configuração do projeto no Google Cloud Console e obtenção das credenciais.
    
- [x] Criação do endpoint de backend `POST /auth/google` para iniciar o fluxo de login.
    
- [x] Adição do botão "Login com Google" na interface do frontend.
    

## Próximos Passos

- [ ] Implementar o endpoint de callback `GET /auth/google/callback` para lidar com o retorno do Google.
    
- [ ] Criar a lógica no frontend para gerenciar o token do usuário após o login bem-sucedido.
    
- [ ] Escrever teste de integração para o fluxo completo de autenticação.
    

## Bloqueios

- Nenhum no momento.
    

### 3. Registros de Decisão de Arquitetura (ADRs) — O Diário de Bordo Técnico

Projetos de software são uma série de decisões. Os ADRs são documentos curtos e focados que capturam o "porquê" por trás de cada decisão arquitetônica significativa. Eles são inestimáveis para alinhar a equipe e garantir que a IA compreenda e respeite as escolhas técnicas feitas, evitando que ela sugira soluções que já foram descartadas.  

**Conteúdo Essencial:**

- **Título:** Uma frase curta descrevendo a decisão (ex: "ADR-001: Escolha do Banco de Dados").
    
- **Status:** Proposto, Aceito, Recusado ou Superado.
    
- **Contexto:** O problema ou a necessidade que motivou a decisão.
    
- **Opções Consideradas:** Uma lista das alternativas avaliadas.
    
- **Decisão:** A escolha final e a justificativa detalhada, incluindo prós e contras.
    
- **Consequências:** Os resultados e impactos da decisão (positivos e negativos).
    

**Exemplo de Estrutura (`docs/adr/ADR-002-gerenciamento-de-estado-frontend.md`):**

# ADR-002: Escolha da Biblioteca de Gerenciamento de Estado no Frontend

**Status:** Aceito

**Contexto:** A aplicação precisa de uma solução para gerenciar o estado global, como informações do usuário autenticado e a lista de tarefas, que são compartilhados entre múltiplos componentes.

**Opções Consideradas:**

1. **Redux Toolkit:** Robusto e padrão da indústria, mas com uma curva de aprendizado e boilerplate significativos.
    
2. **Zustand:** Leve, com uma API mínima e baseada em hooks, o que se alinha bem com a nossa abordagem funcional.
    
3. **React Context API:** Nativo do React, mas pode causar problemas de performance com re-renderizações desnecessárias em aplicações complexas.
    

**Decisão:** Escolhemos usar **Zustand**. Sua simplicidade, baixo boilerplate e excelente performance para o nosso caso de uso superam a familiaridade do Redux. A API minimalista permitirá um desenvolvimento mais rápido e um código mais limpo.

**Consequências:**

- A equipe precisará se familiarizar com a API do Zustand.
    
- O código de gerenciamento de estado será mais conciso e fácil de manter.
    
- Ganhamos flexibilidade para criar "slices" de estado por funcionalidade.
    

### 4. Documentos de Aterramento da Base de Código

Este conjunto de documentos fornece à IA o conhecimento específico e detalhado sobre _como_ o código é escrito e estruturado no seu projeto.

- **`README.md`:** A porta de entrada do projeto. Deve conter uma visão geral, instruções de instalação e como executar o projeto localmente. Uma estrutura clara ajuda a IA a entender rapidamente como interagir com o ambiente.  
    
- **`CONTRIBUTING.md`:** Um arquivo que detalha as diretrizes de contribuição, incluindo o estilo de código (ex: "Usamos Prettier para formatação"), convenções de nomenclatura, e o fluxo de trabalho de pull requests. Isso é essencial para que o código gerado pela IA seja consistente com o da equipe.  
    
- **Documentação de API e Componentes:** Se o seu projeto expõe uma API ou possui uma biblioteca de componentes de UI, a documentação clara e semântica é crucial. Para APIs, descreva cada endpoint, seus parâmetros, e o formato da resposta. Para componentes, documente suas props e forneça exemplos de uso.  
    
- **`AGENTS.md` ou `CLAUDE.md`:** Um meta-documento, ou um "README para a IA". Este arquivo pode ser colocado na raiz ou em pastas específicas para fornecer instruções diretas ao agente, como:  
    
    - Comandos comuns a serem usados (ex: `npm run test:watch`).
        
    - Ponteiros para arquivos-chave (ex: "A lógica de autenticação principal está em `src/lib/auth.ts`").
        
    - Instruções sobre como usar os outros documentos (ex: "Antes de propor uma nova solução, verifique os ADRs em `docs/adr/`").
        

### Conclusão: A Disciplina da Engenharia de Contexto

A eficácia de um agente de IA é diretamente proporcional à qualidade do contexto que lhe é fornecido. A criação e manutenção desses documentos não deve ser vista como uma tarefa burocrática, mas como uma disciplina fundamental da **Engenharia de Contexto**. Ao tratar sua documentação como um artefato vivo, versionado e integrado ao seu fluxo de trabalho, você transforma a IA de uma ferramenta genérica em um membro da equipe altamente especializado, informado e alinhado, capaz de contribuir para o seu projeto com uma profundidade e precisão notáveis.