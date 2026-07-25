---
type: Research Report & Vibe Design Prompt Strategy
project: Genesis MC (SODA - Sovereign Operating Data Architecture)
domain: UI/UX Generativa, Google Stitch, Design para Neurodiversidade
status: REFINED & ACTIVE (Correção da Matriz Brutalista)
sticker: lucide//at-sign
---
# Relatório Técnico: Engenharia de Prompts e Vibe Design para Interfaces SODA (Cyber-Neuro Synthesis)

## 1. O Desafio do Vibe Design no Contexto SODA

A transição para a orquestração de interfaces via inteligência artificial (Vibe Design) em plataformas como o Google Stitch ou v0.dev apresenta um atrito sistemático: o "Viés de Normalidade". As ferramentas generativas foram treinadas predominantemente em painéis de SaaS corporativos (layouts brancos, botões azuis arredondados, sombras difusas e animações lentas).

Para o **Genesis Mission Control (SODA)**, um Sistema Operacional Agêntico de execução local desenhado para um utilizador 2e/TDAH, este padrão corporativo é inaceitável. A interface deve operar como um **"Cockpit Neural"** de alto desempenho (Bare-Metal), onde a estética serve diretamente a função executiva e o foco cognitivo.

## 2. A Armadilha do Brutalismo (Crítica à Iteração Anterior)

As primeiras tentativas de mitigar a poluição visual corporativa através de engenharia de prompts caíram num extremo perigoso: **A Armadilha do Brutalismo Morto**.

Ao instruir a IA para remover excessos, o resultado foi uma interface mecanicamente ríspida, composta por fundos pretos absolutos (`#000`), ausência total de sombras, linhas divisórias agressivas e a eliminação de qualquer animação.

**Por que razão isto falha para o cérebro 2e/TDAH?**

1. **Morte da Hierarquia Espacial:** Sem sombras ou desfoques (_blurs_), os menus flutuantes parecem recortes colados no ecrã. O cérebro gasta energia cognitiva a tentar decifrar o que está por cima e o que está por baixo.
2. **Ausência de Recompensa Tátil:** Eliminar micro-interações destrói a resposta de dopamina. Um botão precisa de reagir ao clique como um interruptor físico de alta precisão, confirmando a ação instantaneamente.
3. **Fadiga Visual:** Fundos pretos absolutos com texto branco puro causam um efeito de "halo" e cansam os olhos rapidamente (astigmatismo digital).

## 3. A Alma do SODA: O Paradigma "Cyber-Neuro Synthesis"

O design Bare-Metal elegante não significa ausência de estética, mas sim **estética com um propósito implacável**. A nossa diretriz substitui o brutalismo por uma profundidade tátil, estruturada em quatro regras visuais:

1. **A Profundidade do Substrato:** O ecrã não é um vácuo negro. O fundo deve respirar através de um gradiente radial extremamente subtil (roxo abismal no centro a desvanecer para preto) ou uma textura de grelha cibernética com 2% de opacidade.
2. **Glassmorphism Focado (Vidro Tátil):** O Header (Bússola) e o Footer (Telemetria) não são blocos sólidos. Utilizam `backdrop-blur` acoplado a fundos escuros translúcidos (`bg-black/40`), permitindo que o Canvas central deslize por baixo deles, mantendo o contexto geográfico.
3. **Ghost Borders & Pulso Neural:** Em vez de sombras pesadas (`drop-shadow`), a elevação é criada com bordas translúcidas (`border-white/10`) e um "Primary Glow" de 15% apenas nos elementos ativos. O estado da IA (a pensar/executar) comunica-se através de um pulso de opacidade, não de _spinners_ rotativos.
4. **Instância Mecânica (A Regra dos 50ms):** As micro-interações devem imitar equipamento físico de precisão. As transições (`transition-all`) devem ser hiper-rápidas (`duration-75 ease-out`). O ecrã reage num estalo.

## 4. A Estratégia de Construção Modular (Locking)

As IAs de Vibe Design perdem a coerência espacial se lhes for exigida a interface completa num único prompt. A estratégia para materializar o SODA exige **Construção Isolada em Fases**:

- **Fase 1 (A Fundação):** Gerar exclusivamente o Substrato (Fundo), o _Compass_ (Header) e a _Engine Room_ (Footer).
- **Fase 2 (A Âncora):** Trancar (_Lock_) a Fundação. Injetar o prompt para o _Governor Rail_ (Menu Esquerdo), garantindo que recolhe usando `transform: translateX` e não `width` (para evitar _layout shift_).
- **Fase 3 (O Palco):** Trancar as margens. Gerar o _Nexus_ (Canvas Central) e o seu mecanismo de _Focus Rack_ (as gavetas dinâmicas).

## 5. O Super Prompt de Inicialização (Fase 1)

_Este é o prompt algemado à nossa "Alma". Deve ser copiado e colado diretamente no Google Stitch, v0.dev ou Cursor. Está redigido em inglês para maximizar a interpretação semântica das classes do Tailwind CSS._

```
ACT AS A SENIOR VIBE DESIGNER AND UI ARCHITECT FOR MISSION-CRITICAL SYSTEMS.

PROJECT CONTEXT:
I am building the "Genesis Mission Control" (SODA), a bare-metal Agentic Operating System. The aesthetic is "Cyber-Neuro Synthesis" — a highly dense, elegant, hyper-focused Neural Cockpit designed for neurodivergent (ADHD) users. It requires extreme precision, no visual noise, but rich tactile depth. 

THE MISSION (PHASE 1):
Generate the App Shell (Foundation) using React, Tailwind CSS, and Lucide React icons. 
DO NOT build the Left Sidebar or the Central Canvas yet. Focus strictly on the Background, the Header, and the Footer.

AESTHETIC RULES & GLOSSARY (MANDATORY):
1. Color Palette: Deep dark substrate logic with "Cyber-Purple" accents (Primary: #BA9EFF, Primary-Dim: #8455EF).
2. The Background: Do NOT use pure flat #000. Use a very subtle, almost imperceptible dark radial gradient (center slightly lighter dark-purple/black fading to pure black at the edges), OR a 2% opacity cyber grid.
3. Glassmorphism: The Header and Footer must use `backdrop-blur-md` with `bg-black/40` or `bg-zinc-950/60` and a subtle top/bottom "Ghost Border" of `border-white/10`.
4. Typography: Use 'Space Grotesk' for prominent numbers/status, and 'Inter' for UI labels. Text should be small (`text-xs` or `text-sm`), dense, and uppercase with slight tracking (`tracking-wide`) for metadata.
5. Micro-interactions (Mechanical Instancy): Any interactive icon MUST have a 50ms transition (`duration-75 ease-out`) with a subtle background shift on hover. No slow, floaty animations.

STRUCTURAL REQUIREMENTS:
1. The App Shell: Flex column, h-screen, w-screen, overflow-hidden.
2. THE HEADER ("The Compass" - h-12, fixed top, z-50):
   - Left: Minimalist breadcrumbs (e.g., "NEXUS / SYSTEM / IDLE").
   - Center: An "OmniBar" (Ctrl+K style input). Small, glassmorphic, ghost borders, subtle 10% purple inner glow on focus.
   - Right: Minimalist Notification bell icon, User Avatar (simple geometric), and a dark-red "Kill Switch" octagon icon (for emergency VRAM purge).
3. THE FOOTER ("The Engine Room" - h-8, fixed bottom, z-50, mono-spaced font):
   - Left: Active LLM status (e.g., "🧠 Phi-4-mini [Q4_K]").
   - Center: Rust Daemon heartbeat (e.g., "DB: WAL Syncing").
   - Right: Telemetry data (VRAM: 4.2/6.0 GB, RAM: 14/32 GB, Spd: 22 t/s) and a Terminal Drawer toggle icon.

ANTI-HALLUCINATION PROTOCOL:
- REJECT corporate SaaS aesthetics.
- REJECT pure flat, dead colors. Use glows and blurs to define depth and hierarchy without clutter.
- REJECT large, bubbly, fully-rounded buttons (use `rounded-sm` or `rounded-md` max).
- REJECT slow animations.

Generate the clean, production-ready React component for this foundation.
```

## 6. Próximos Passos de Integração

Ao integrar este documento no **NotebookLM**, o modelo adquire a capacidade de auditar proativamente as futuras interfaces geradas. Sempre que um componente visual for proposto, o NotebookLM poderá confrontá-lo com as regras do _Glassmorphism_, _Ghost Borders_ e _Instância Mecânica_ definidas neste manifesto, protegendo a plataforma SODA de qualquer regressão estética.