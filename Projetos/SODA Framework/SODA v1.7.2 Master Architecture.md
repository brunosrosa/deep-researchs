# S.O.D.A. v1.7.2 - Sistema Operacional de Desenvolvimento Agêntico

**(Spec-Oriented Dockerized Architecture - The "Completão" Enterprise Edition)**

**Versão:** 1.7.2 (Definitive Master)
**Status:** Gold Standard (Production Ready)
**Target Runtime:** Google Antigravity (WSL2)
**Constraint de Hardware:** **Hardware-Safe Mode** (GPU Preservation).

**Filosofia:** "A Linha de Montagem Cognitiva". Uma fusão da metodologia de processos granulares (SOPs) com uma arquitetura de execução soberana e segura.

## 1. Manifesto Axiomático (As Leis Imutáveis)

Para operar o SODA, o Agente e o Humano devem obedecer a estas leis da física do projeto:

1. **Clean Root Axiom (Raiz Limpa):** A raiz do projeto é sagrada. Contém apenas a _Intenção_ (`PROJECT_CHARTER.md`) e o _Produto_ (`src`). Toda a complexidade operacional, scripts, logs e memórias intermediárias são encapsulados no diretório oculto `.agent/`. O humano vê apenas o que importa.
    
2. **Spec-Lock Protocol:** Nenhuma linha de código de produção (Fase 6) é escrita sem que existam antes um Requisito de Produto (Fase 2) e um Contrato de Arquitetura (Fase 4) aprovados. O código é apenas um efeito colateral da especificação.
    
3. **Sovereign Reading (Leitura Soberana):** Agentes são proibidos de ler HTML "sujo" da web. Toda ingestão de conhecimento deve passar por sanitização via **Docfork** (para documentação técnica) ou **Rtfmbro** (para repositórios), convertendo tudo para Markdown limpo antes de entrar na janela de contexto.
    
4. **Flow Integrity (Integridade de Fluxo):** O output da Fase N é o input _read-only_ da Fase N+1. Um desenvolvedor (Fase 6) nunca altera o PRD (Fase 2) silenciosamente. Se o requisito mudar, executa-se um _Rollback_.
    
5. **State Persistence (Memória Externa):** O estado nunca reside no chat. O estado reside em `task_plan.md` (O que fazer), `findings.md` (O que aprendi) e `progress.md` (O que fiz).
    

## 2. A "Super-Stack" Convergente (Ferramentas & Infraestrutura)

A seleção de ferramentas para a v1.7.2 foca em eficiência de tokens e proteção de hardware local.

### A. O Cérebro (Orquestração & Governança)

- **Host:** Google Antigravity (WSL2) + Python 3.12 (`uv` managed).
    
- **Gerente de Projeto:** **GitHub Project Manager MCP**.
    
    - _Função:_ Cria a Matriz de Rastreabilidade. Cada linha de código deve ser rastreável até uma Issue, que é rastreável até um requisito do PRD.
        

### B. A Memória (Persistência de Estado)

- **Hot Memory (Tática):** **Plan-With-Files** (`.agent/memory/hot/`).
    
    - _Arquivos:_ `task_plan.md` (Kanban), `findings.md` (Wiki Volátil), `progress.md` (Log Imutável).
        
- **Cold Memory (Episódica):** **OpenMemory** (GraphDB via Docker).
    
    - _Função:_ Armazena "Decisões Arquiteturais" (ADRs) e preferências do usuário. Evita que o agente pergunte a mesma coisa duas vezes.
        

### C. Os Sentidos (Ingestão & Busca)

- **Ingestão Soberana (Leitura):**
    
    1. **Primário:** **Docfork** (Container Docker). Converte sites de documentação em Markdown puro.
        
    2. **Fallback/Deep:** **Rtfmbro** (Container Docker). Leitura bruta de repositórios/manuais.
        
- **Armada de Busca (Federada):**
    
    - **Brave Search:** Web geral, limpa e privada.
        
    - **ArXiv:** Papers técnicos e algoritmos (Deep Tech).
        
    - **Bing:** Notícias e fatos recentes (Fallback).
        
    - **DuckDuckGo:** Fatos rápidos (Baixa latência).
        
- **Percepção de Código:** **Heuristic-MCP**.
    
    - _Função:_ Busca semântica e _call-graph_ no código local. Roda na CPU. Superior ao `grep` simples.
        

### D. As Mãos (Execução & Isolamento)

- **Motor:** **Ralph Loop v1.7** (Powered by **Smolagents**).
    
    - _Lógica:_ O Ralph usa `CodeAgent` para gerar scripts Python que resolvem tarefas. Roda na CPU (leve), usa API do Gemini para raciocínio (Zero GPU Load).
        
- **Isolamento:** **Docker MCP Gateway**.
    
    - _Função:_ Roteia ferramentas. No modo "Coding", corta a internet aberta para evitar distrações.
        

## 3. Anatomia do Sistema: A Árvore de Diretórios ("Hyper-Structured")

Esta é a estrutura física de referência para validação via `tree`.

```
/RAIZ_DO_PROJETO
│
├── PROJECT_CHARTER.md          # [Fase 1] A Constituição (PID-Context).
├── src/                        # [Fase 6] O Código Fonte.
├── tests/                      # [Fase 7] Testes E2E e Unitários.
│
├── docs/                       # [Memória Humana & Auditável]
│   ├── business/               # SOP-01 (Regras), SOP-02 (Glossário).
│   ├── product/                # SOP-03 (PRD), SOP-04 (Gherkin/Features).
│   ├── design/                 # SOP-05 (Flows), SOP-06 (Copy), SOP-07 (System).
│   ├── architecture/           # SOP-08 (API), SOP-09 (DDR), SOP-10 (Threats).
│   ├── research/               # Relatórios da Armada de Busca (ArXiv/Exa).
│   └── operations/             # SOP-20 (Privacy), SOP-22 (Release Notes).
│
├── .agent/                     # [Kernel SODA - O Cérebro Oculto]
│   ├── AGENTS.md               # Contexto Vivo (Passagem de turno).
│   ├── config/
│   │   ├── gateway.yaml        # Configuração do Docker MCP.
│   │   └── master_rule.md      # A Regra Global injetada.
│   ├── memory/
│   │   ├── hot/                # task_plan.md, findings.md, progress.md.
│   │   └── cold/               # Dados do OpenMemory.
│   ├── sops_registry/          # [O MANUAL] Os 22 SOPs em Markdown/YAML.
│   │   ├── SOP-01_BusinessRules.md
│   │   ├── SOP-02_UbiquitousLanguage.md
│   │   └── ... (até SOP-22)
│   ├── workflows/              # [OS GATILHOS] Comandos numerados.
│   │   ├── 01_Fase1_Inception.md
│   │   └── ...
│   ├── skills/                 # [AS FERRAMENTAS]
│   │   ├── ingestion/          # Wrappers para Docfork/Rtfmbro.
│   │   ├── search/             # Wrappers para Armada.
│   │   └── coding/             # Scripts do Smolagents.
│   └── scripts/
│       └── ralph_loop.py       # O Motor de Execução.
│
├── .openspec/                  # [Especificações Machine-Readable]
│   ├── api/                    # OpenAPI/Swagger.
│   ├── db/                     # Schemas e Migrations Plans.
│   └── security/               # Políticas RBAC.
│
├── docker-compose.soda.yml     # [Infra] O Gateway na Raiz.
└── .env.example                # Template de variáveis.
```

## 4. O Ciclo de Vida SODA: Detalhamento dos 22 SOPs

O desenvolvimento é uma jornada linear e bloqueante.

### 🟢 Fase 1: Fundação de Negócio (A Bússola)

_Comando:_ **`/01_Fase1_Inception`**

- **SOP-01 (Business Rules):** Documenta leis imutáveis (ex: regras de cálculo). _Tool: OSP Marketing._
    
- **SOP-02 (Ubiquitous Language):** Cria o glossário de domínio. _Tool: Rtfmbro._
    

### 🔵 Fase 2: Definição de Produto (O Planejamento)

_Comando:_ **`/02_Fase2_Product`**

- **SOP-03 (Product Requirements - PRD):** O roteiro mestre. _Tool: OpenSpec + Docfork._
    
- **SOP-04 (Acceptance Criteria - Gherkin):** Traduz PRD em testes `Dado/Quando/Então`.
    

### 🎨 Fase 3: Experiência e Interface (O Design)

_Comando:_ **`/03_Fase3_Design`**

- **SOP-05 (User Flows):** Mapeia árvore de decisão. _Tool: Mermaid Renderer._
    
- **SOP-06 (UX Writing):** Define o tom de voz.
    
- **SOP-07 (Design System Align):** Valida componentes visuais. _Tool: Docfork._
    

### 🏗️ Fase 4: Arquitetura Técnica (A Engenharia)

_Comando:_ **`/04_Fase4_Arch`**

- **SOP-08 (API Contracts):** Define endpoints e tipos. _Tool: OpenSpec._
    
- **SOP-09 (Data Design - DDR):** Modela ERD e índices. _Tool: OpenMemory._
    
- **SOP-10 (Threat Modeling):** Análise de riscos STRIDE. _Tool: ArXiv._
    

### ⚔️ Fase 5: Planejamento Tático (O "Sharding")

_Comando:_ **`/05_Fase5_Sharding`**

- Ação crítica de quebra de complexidade. Transforma os artefatos das fases anteriores em tarefas atômicas no `task_plan.md` e Issues no GitHub.
    

### 💻 Fase 6: Construção (A Execução)

_Comando:_ **`/06_Fase6_Code`** (Invoca **Ralph Loop**)

- **SOP-11 (Implementation):** Codificação iterativa. _Tool: Smolagents + Heuristic-MCP._
    
- **SOP-12 (Secret Management):** Move variáveis sensíveis para `.env`.
    
- **SOP-13 (Auto-Documentation):** Gera JSDoc/Pydoc.
    

### 🧪 Fase 7: Qualidade (A Verificação)

_Comando:_ **`/07_Fase7_Verify`**

- **SOP-14 (Test Generation):** Cria suite automatizada. _Tool: Smolagents._
    
- **SOP-15 (Static Analysis):** Revisor automático. _Tool: ARC Protocol._
    

### 🚀 Fase 8: Operações e Entrega (O Lançamento)

_Comando:_ **`/08_Fase8_Release`**

- **SOP-16 (DB Migrations):** Cria scripts SQL seguros.
    
- **SOP-17 (CI/CD Pipelines):** Configura GitHub Actions.
    
- **SOP-18 (Observability):** Configura logs e métricas.
    
- **SOP-19 (I18n):** Internacionalização.
    
- **SOP-20 (Compliance):** Auditoria LGPD.
    
- **SOP-21 (RCA):** Postmortem de erros.
    
- **SOP-22 (Release Notes):** Traduz commits em valor. _Tool: GitHub PM._
    

## 5. Protocolos de Hardware & Segurança

1. **Proteção de GPU (Hardware-Safe):** Devido à restrição térmica, o sistema é configurado para **NUNCA** executar inferência neural localmente. Toda inteligência pesada é offloaded para APIs (Gemini/Claude).
    
2. **Agentes de Código (CPU-Bound):** O `Smolagents` executa scripts Python localmente usando apenas a CPU, o que é seguro e termicamente eficiente.
    
3. **Segurança de Leitura:** O sistema recusa ler URLs não sanitizadas para evitar injeção de prompt via HTML malicioso.