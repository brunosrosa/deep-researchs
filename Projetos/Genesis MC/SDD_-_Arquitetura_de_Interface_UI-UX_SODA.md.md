---
aliases:
  - SDD_-_Arquitetura_de_Interface_UI/UX_SODA.md
type: Architecture Decision Record & Software Design Document (SDD)
project: Genesis MC (SODA - Sovereign Operating Data Architecture)
domain: UI/UX, Frontend State, Tauri IPC
author: Bruno & SODA Master Architect 
status: APPROVED / ACTIVE DIRECTIVE
---

# 🔴 DIRETRIZ SOBERANA: Arquitetura de Interface, UX para 2e/TDAH e Gestão de Estado

## 1. Filosofia Fundacional e Restrições de Design

A interface do SODA **não é um site**, é um _Cockpit Neural_ de alto desempenho em Bare-Metal. Projetado estritamente para um perfil neurodivergente (2e/TDAH) rodando sobre restrições térmicas severas (RTX 2060m 6GB / 32GB RAM).

### 1.1. Leis Inegociáveis de UX (Anti-RSD & TDAH)

- **Progressive Disclosure (Divulgação Progressiva):** O Canvas começa vazio. A complexidade apenas se desdobra sob demanda. Menus não possuem 20 opções; eles possuem gavetas lógicas.
- **Mechanical Instancy (Zero Lag):** O feedback tátil a um clique deve ocorrer em menos de 50ms. A IA pode demorar para pensar, mas a interface jamais pode parecer congelada.
- **Spatial Predictability (Bússola Fixa):** O **Header** e o **Footer** JAMAIS somem. Eles são a bússola e o relógio do sistema. Se o usuário se perder hiperfocado no Canvas, a visão periférica informa o estado exato da máquina.
- **Zero Layout Shift:** A expansão ou retração de menus laterais (`Governor Rail`) **deve ser obrigatoriamente** manipulada via CSS `transform: translateX()` manipulado pela GPU. Redimensionar larguras via `width` (flexbox reflow) causará engasgos na renderização e poluição visual, o que é expressamente proibido.

### 1.2. Taxonomia Visual e Iconografia

- **Paleta de Cores:** `Cyber-Purple` estrito (Fundo super escuro, realces focados).
- **Ícones:** Biblioteca **Lucide-React**. Escolhida pela espessura de linha única constante. **Proibido** o uso de ícones preenchidos (solid) ou multicoloridos para evitar o "ruído sub-pixel".
- **Neural Pulse:** Estados da IA (Pensando, Executando) não usam spinners giratórios estressantes, mas uma opacidade pulsante suave (Pulse) aplicada em bordas ou nós do Canvas.

## 2. Topologia da Interface (O Layout Master)

A tela é dividida em 4 quadrantes fixos. A hierarquia de componentes é sagrada.

### 2.1. HEADER (The Compass)

_A Bússola e o Teto Cognitivo._ Altura fixa (`h-12`).

- **Left (Breadcrumbs):** Caminho de navegação atual. Ex: `FLOW / Pessoal / Git Boards`.
- **Center (OmniBar):** A barra de comando universal `Ctrl+K`. Entrada passiva e direta.
- **Right (System Controls):** Ícone de Notificações, Dropdown do Usuário e o **Kill Switch** (Botão vermelho escuro, atômico, que envia `SIGKILL` para qualquer LLM inferindo na VRAM via IPC).

### 2.2. GOVERNOR RAIL (Left Menu / A Âncora)

_O painel principal de controle de estado._ Largura fixa quando aberto (`w-64`), encolhido via `translateX` para mostrar apenas ícones (`w-16`). A estrutura visual das "Gavetas":

- **TOPO:** Ícone do Sistema + Ponto indicativo de _Neural Pulse_.
- **[ BLOCO 1: FLOW ] (O Fluxo Presente)**
    - `OmniTimeline` (A linha do tempo/chat unificado do Life Coach).
    - `Workspaces` (Hub de separação de projetos).
    - `Canvas Hub` (Comporta o mecanismo "Focus Rack" logo abaixo).
- **[ BLOCO 2: VAULT ] (O Passado e Estático)**
    - `Asset Library` (Gestão de arquivos locais/PDFs/RAG e Mount Points).
    - `Engramas` (Painel visual de poda, heatmap de RAG e gestão de memória).
- **[ BLOCO 3: FORGE ] (O Motor e Ferramentas)**
    - `Skills` (Gestão do ecossistema `.md` de habilidades).
    - `Nodes` (Integrações, CLIs e servidores MCP).
    - `LLM Cortex` (Gestão explícita de VRAM, offload e provedores).
    - `Sandbox` (Quarentena HITL para código Wasmtime não confiável).
- **[ FUNDO: CORE ] (Utilitários)**
    - `Settings` (Configurações do Tauri).
    - `Life Coach Toggle` (Ativar/Desativar o agente proativo).

### 2.3. FOOTER (The Engine Room)

_Telemetria pura em fonte monoespaçada (`label-sm`)._ Altura fixa (`h-8`).

- **Left:** Modelo LLM ativo na VRAM (ex: `🧠 Phi-4-mini [Q4_K]`).
- **Center:** Status dos Daemons Rust (ex: `DB: WAL Syncing`).
- **Right:** Carga Termodinâmica (`VRAM: 4.2/6GB`, `RAM: 14/32GB`, `Spd: 22 t/s`) + Botão de `Toggle Terminal`.
- **Terminal Drawer:** Opcional. Uma gaveta absolute que sobe do footer com z-index elevado para mostrar logs brutos.

### 2.4. CANVAS CENTRAL (The Stage)

A área fluida que toma o resto da tela (via `flex-1`). Hospeda o Xyflow ou interfaces limpas baseadas na seleção do _Governor Rail_.

## 3. Gestão de Estado Global (React + Zustand)

A ponte de comunicação (IPC) entre as engrenagens brutais do Rust e o DOM frágil do React não pode ser síncrona.

### 3.1. A Lei dos Seletores Atômicos (Atomic Selectors)

O Zustand deve ser segregado em _Stores_ específicos. **NUNCA** crie um super-objeto de estado.

- `useUIStore`: Gerencia colapso do menu, modais, tema.
- `useCanvasStore`: Gerencia nós, arestas e viewports do Xyflow.
- `useTimelineStore`: Gerencia a lista de mensagens do _OmniTimeline_.
    **Regra Tática:** Uma atualização na linha 10.000 do Log do Rust chegando na Timeline JAMAIS deve causar re-render na matriz do Canvas.

### 3.2. Estrangulamento IPC (IPC Throttling)

Eventos contínuos emitidos pelo Rust (ex: contagem de tokens, logs de compilação) devem passar por um Buffer/Throttle no Frontend (ou na emissão do Rust) a cada `100ms`. O React não deve processar milhares de eventos do Tauri por segundo sob risco de congelamento do WebView2.

### 3.3. A Mecânica do "Focus Rack" (Sub-menu do Canvas)

O slot dinâmico contido abaixo de `FLOW -> Canvas Hub`.

O estado `useUIStore.focusRack` gerencia um Array de máximo 5 slots:

1. **Fixed 1:** `Genesis Board` (Descompressão).
2. **Fixed 2:** Workspace Padrão (Ex: `Git Boards`).
3. **Fixed 3:** Customizado (Ex: `SDD Flow`).
4. **Active (Ephemeral):** O canvas aberto agora (Some ao fechar).
5. **Attention Slot (Priority Overlay):** Injetado pelo Agente Governador se algo quebrar em background. Possui propriedade `pulse: true` forçando o ícone a piscar (Neural Pulse). Desaparece quando `isRead` vira true.

## 4. Estrutura de Diretórios Recomendada (Frontend - React/Vite)

```
/src
 ├── /assets           # Fontes e imagens estáticas (Zero-runtime)
 ├── /components
 │    ├── /core        # UI Kit Base (Botões, Inputs, Tooltips - isolados do Zustand)
 │    ├── /layout      # GovernorRail, HeaderCompass, FooterEngine, AppShell
 │    ├── /canvas      # Arquivos Xyflow (Nodes customizados, Edge rules)
 │    ├── /flow        # Componentes do fluxo de trabalho (OmniTimeline, Workspaces)
 │    ├── /vault       # Componentes estáticos e de auditoria (AssetLibrary, Engramas)
 │    └── /forge       # Formulários de configuração, Painel LLM, Sandbox View
 ├── /store            # Estado global (Zustand)
 │    ├── useUIStore.ts
 │    ├── useCanvasStore.ts
 │    └── useAgentStore.ts
 ├── /hooks            # Custom hooks p/ escuta do Tauri (useTauriEvent.ts)
 ├── /lib              # Helpers utilitários (cn para tailwind merge, formatação)
 └── App.tsx           # Entrypoint e orquestração do Layout Master
```

## 5. Protocolo de Vibe Coding / Desenvolvimento de Componentes

Sempre que um Agente Autônomo for convocado para criar um componente UI para o SODA:

1. Ele DEVE ler as diretrizes deste `ADR_UI_UX_ARCHITECTURE.md`.
2. Ele NÃO PODE criar lógicas complexas dentro do componente; estado fica no Zustand.
3. Ele NÃO PODE usar CSS dinâmico que altere layout-width, apenas classes de utilidade padrão (Tailwind) e transformações de matriz via `translate/opacity`.
4. Ele DEVE usar Ícones da biblioteca `lucide-react`.