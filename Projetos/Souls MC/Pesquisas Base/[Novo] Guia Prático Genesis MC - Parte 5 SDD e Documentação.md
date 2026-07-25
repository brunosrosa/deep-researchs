---
aliases: []
sticker: lucide//chevron-right-square
---
# Genesis MC: Guia Prático - Parte 5 (A Fonte da Verdade e SDD)

A premissa aqui é o **Pessimismo da razão, otimismo da vontade**: assuma que o Agente (LLM) vai tentar ser prolixo, esquecer regras de infraestrutura e sugerir bibliotecas pesadas. Para extrair a genialidade da IA (otimismo), precisamos algemá-la com especificações determinísticas (razão).

O seu repositório do SODA deve ter a seguinte estrutura raiz para a mente da IA:

```
/
├── .agents/
│   ├── rules/
│   │   └── global_nfrs.md    # Restrições de hardware e segurança
│   ├── skills/
│   │   └── ler_sqlite.md     # Como o agente deve usar a tool
├── docs/
│   └── adrs/                 # Architecture Decision Records
├── AGENTS.md                 # A Constituição (O LLM lê isso PRIMEIRO)
├── src-tauri/
└── src/
```

## 1. O Fluxo SDD (Spec-Driven Development) no SODA

No Antigravity IDE (ou no seu Canvas SODA), você não pede "Crie um botão que salva no banco". Você força a IA a passar por 4 fases. O Roteador SODA que construímos na Parte 4 lida com isso.

1. **`/specify` (Especificar):** Você escreve o que quer em texto puro. O agente _Life Coach (Mistral)_ refina e gera um arquivo `.md` de requisitos.
2. **`/plan` (Planejar):** O _Life Coach_ lê os `docs/adrs/` para garantir que o plano obedece à arquitetura (ex: "Usar Tauri IPC, não HTTP"). Gera a arquitetura.
3. **`/tasks` (Dividir):** O plano vira um checklist determinístico.
4. **`/implement` (Programar):** O _Logic Coder (Phi-4-mini)_ entra em ação na VRAM, lê a Task 1, e cospe apenas o código. Zero conversa.

## 2. A Constituição: O Template `AGENTS.md`

Este é o arquivo mais importante do projeto. É o prompt de sistema estático que o **Toon Context** deve anexar (de forma comprimida) no início de _todas_ as sessões críticas.

**Arquivo:** `/AGENTS.md`

```markdown
# 🤖 Constituição do Genesis MC (SODA)

**Você é um Agente do SODA (Sovereign OS).** Você opera localmente na máquina do usuário.
Você não é um assistente de chat. Você é um motor de raciocínio executável.

## 1. Regras de Ouro (NFRs - Non-Functional Requirements)
- **VRAM Strict Limit:** O hardware alvo possui uma **RTX 2060m (6GB VRAM)**. NUNCA sugira, importe ou planeje soluções que exijam mais de 4GB de VRAM sustentada (deixando 2GB para o SO).
- **Zero Cloud (Local-First):** É estritamente proibido sugerir APIs externas (OpenAI, AWS, Firebase). Todos os dados moram em SQLite local.
- **Eficiência de Tokens:** Não seja prolixo. Seja direto, brutalmente honesto e conciso. Use bullet points.
- **Comunicação Assíncrona:** A UI no React (Canvas) NUNCA pode ser bloqueada. Tudo no backend Rust deve rodar em `tokio::spawn`.

## 2. Divulgação Progressiva (Progressive Disclosure)
Ao gerar código ou explicar conceitos complexos para o usuário (Bruno, perfil 2e):
1. Dê a resposta direta e o código final primeiro.
2. Coloque explicações teóricas, logs de MCPs ou raciocínios secundários dentro de blocos `<details>` ou em nós secundários do Xyflow.
3. Não presuma que o usuário não sabe. Se desafiado, assuma o papel de "Advogado do Diabo" e aponte os pontos cegos do código.

## 3. Stack Tecnológica Obrigatória
- **Frontend:** React, TypeScript, TailwindCSS, Xyflow (para grafos), Lucide React.
- **Backend:** Rust, Tauri (IPC via Canais MPSC), Tokio.
- **Banco de Dados:** SQLite (via sqlx) com FTS5 habilitado.
```

## 3. As Habilidades: Template de SKILL.md

Para evitar saturar a janela de contexto de 4096 tokens do _Phi-4-mini_, não ensine todas as ferramentas de uma vez. Use o **Progressive Disclosure** na arquitetura. O arquivo `SKILL.md` descreve como o agente deve invocar um servidor MCP (ex: _Jcodemunch_).

**Arquivo:** `/.agents/skills/jcodemunch_mcp.md`

````markdown
# 🛠️ Skill: Leitura de Repositório (Jcodemunch MCP)

**Quando usar:** Quando o usuário pedir para analisar código, refatorar um arquivo grande ou encontrar bugs em diretórios que não estão no contexto atual.

**Como invocar (Output Esperado do LLM):**
Para acionar esta habilidade, você DEVE emitir o seguinte bloco JSON (e nada mais) no seu output. O motor Rust irá interceptar e executar a busca.

```json
{
  "tool": "jcodemunch",
  "action": "search",
  "path": "src-tauri/src/",
  "query": "impl McpServer"
}
````

**Restrições da Skill:**

- Não tente adivinhar a estrutura do código. Se você não tem certeza, invoque a tool e aguarde o motor Rust injetar o resultado no próximo turno.
- Após receber o resultado do Jcodemunch, use o **Toon Context** mentalmente: extraia apenas a assinatura das funções antes de planejar a refatoração.

````markdown

---

## 4. Architecture Decision Records (ADRs)

A IA é treinada na internet inteira, então ela sempre vai sugerir "use Postgres", "use Redis". Os ADRs são a vacina contra essa alucinação. Quando a IA roda o `/plan` no fluxo SDD, ela deve ler os ADRs para saber o que **já foi decidido e não deve ser questionado**.

**Arquivo:** `/docs/adrs/0001-uso-de-sqlite-para-memoria.md`
```markdown
# ADR 0001: Uso Exclusivo de SQLite (FTS5) para Memory Bank

**Status:** Aceito
**Data:** 19 de Março de 2026

## Contexto e Problema
O Genesis MC (SODA) requer um grafo de conhecimento e uma memória episódica para o "Life Coach". LLMs tradicionais usam Vector Databases (Milvus, Pinecone, ChromaDB) ou Grafos Nativos (Neo4j). No entanto, o sistema deve rodar inteiramente de forma local e com recursos limitados de RAM.

## Restrições (Decision Drivers)
- Inicialização da aplicação em menos de 2 segundos.
- Ocupar menos de 50MB de RAM na ociosidade (idle).
- Sem dependência de Docker ou instâncias JVM de fundo.

## Opções Consideradas
1. **ChromaDB / Qdrant:** Descartados. Exigem processos pesados em background e estouram a RAM.
2. **Neo4j:** Descartado. Overhead da JVM massivo.
3. **SQLite com FTS5 (Texto Completo) + sqlite-vec (Futuro):** Escolhido.

## Decisão (A Fonte da Verdade)
Nós escolhemos **SQLite**. Toda persistência de dados do SODA (Memória Semântica, Episódica, Relacional) DEVE ser modelada em tabelas relacionais clássicas dentro do diretório `src-tauri/db/`. 
As buscas de contexto inicial devem utilizar consultas textuais (FTS5) pela sua velocidade extrema em I/O local.

## Consequências
- **Positivo:** O footprint de memória do banco é praticamente zero. É apenas um arquivo em disco rápido.
- **Positivo:** Integração perfeita, segura e assíncrona com Rust via `sqlx`.
- **Negativo:** A criação de embeddings para buscas semânticas puras exigirá um esforço manual extra via extensões (ex: `sqlite-vec`) no futuro, ou roteamento para um modelo SLM de embedding dedicado.
````

## Como Automatizar isso no seu Fluxo de Trabalho Hoje:

1. **Crie a pasta `.agents`** no seu projeto agora.
2. **Cole o `AGENTS.md`** na raiz.
3. **Configure o seu prompt de sistema** no motor **Rust** (Parte 4) para fazer um _file read_ do `AGENTS.md` toda vez que carregar o modelo na VRAM. O LLM sempre acordará sabendo quem ele é e quais são os limites da sua placa de vídeo.