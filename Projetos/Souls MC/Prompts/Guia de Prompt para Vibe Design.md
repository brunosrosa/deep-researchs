---
type: Prompt Engineering Guide & Vibe Design Strategy
project: Genesis MC (SODA)
focus: UI Generativa (Google Stitch)
status: REFINED & ACTIVE
sticker: lucide//at-sign
---
# Guia Estratégico: Domando o Vibe Design para o SODA

Ferramentas generativas de UI sofrem de um viés de normalidade severo. Se não forem controladas com precisão cirúrgica, reverterão para painéis de SaaS corporativos (fundos brancos, botões azuis arredondados, sombras exageradas). Este documento estabelece como instruir a IA para gerar a verdadeira estética **Cyber-Purple / Neural Cockpit**, reintroduzindo a profundidade e a elegância perdidas no brutalismo cru.

## 1. A Estratégia de Construção Modular (Ancoragem / Locking)

**NUNCA** peça à IA para desenhar o sistema inteiro num único prompt. A carga cognitiva do modelo colapsará, resultando num layout quebrado.

- **Iteração 1 (A Fundação):** Pedir apenas o Fundo (Background), o Header (The Compass) e o Footer (The Engine Room). O centro deve ficar vazio.
- **Iteração 2 (O Leme):** "Trancar" (Lock) o código gerado na Etapa 1. Pedir agora o Governor Rail (Menu Esquerdo), testando a sua expansão/recolha via `transform`.
- **Iteração 3 (O Palco):** Trancar tudo. Pedir a renderização do espaço central (O Canvas / Nexus) com os seus submenus (Focus Rack).

## 2. O Glossário Cyber-Neuro (Para injetar Elegância)

Sempre que redigir um prompt para o Stitch, utilize estas palavras-chave de engenharia para forçar a estética pretendida:

- **Profundidade & Fundo:** Em vez de "fundo preto", exija `subtle radial gradient background` (de um roxo abismal no centro para preto puro nas bordas) ou `2% opacity cyber grid texture`.
- **Glassmorphism (Vidro Tátil):** Para painéis flutuantes, menus e o próprio Header, exija `backdrop-blur-md` com `bg-black/40`. O ecrã não é opaco; ele respira sobre a malha de fundo.
- **Ghost Borders (Elevação sem Sombras Densas):** Abandone o `box-shadow` genérico. Exija `border border-white/10` ou `border-primary/20` para separar elementos.
- **Primary Glow (O Pulso Neural):** Onde antes haveria sombras, exija brilhos direcionais subtis. Ex: `shadow-[0_0_15px_rgba(186,158,255,0.15)]` para destacar um botão ou nó ativo.
- **Mechanical Instancy (Micro-interações):** A interface deve parecer física. Exija transições hiper-rápidas: `transition-all duration-75 ease-out`. Os elementos não devem "flutuar" lentamente; devem responder num estalo (50ms).

## 3. O Prompt de Inicialização (Zero-Shot) - A Fundação

_Copie e cole o bloco abaixo no Google Stitch ou v0.dev. Este prompt gerará a "concha" vazia perfeita do SODA, contendo o espaço profundo, a bússola e os motores._

```
ACT AS A SENIOR VIBE DESIGNER AND UI ARCHITECT FOR MISSION-CRITICAL SYSTEMS.

PROJECT CONTEXT:
I am building the "Genesis Mission Control" (SODA), a bare-metal Agentic Operating System. The aesthetic is "Cyber-Neuro Synthesis" — a highly dense, elegant, hyper-focused Neural Cockpit designed for neurodivergent (ADHD) users. It requires extreme precision, no visual noise, but rich tactile depth. 

THE MISSION (ITERATION 1):
Generate the App Shell (Foundation) using React, Tailwind CSS, and Lucide React icons. 
DO NOT build the Left Menu or the Central Canvas yet. Focus strictly on the Background, the Header, and the Footer.

AESTHETIC RULES & GLOSSARY (MANDATORY):
1. Color Palette: Pure Black background logic with "Cyber-Purple" accents (Primary: #BA9EFF, Primary-Dim: #8455EF).
2. The Background: Do not use flat black. Use a very subtle, almost imperceptible dark radial gradient (center slightly lighter dark-purple/black fading to pure black at the edges), OR a 2% opacity cyber grid.
3. Glassmorphism: The Header and Footer must use `backdrop-blur-md` with `bg-black/40` or `bg-zinc-950/60` and a top/bottom "Ghost Border" of `border-white/10`.
4. Typography: Use 'Space Grotesk' for prominent numbers/status, and 'Inter' for UI labels. Text should be small (`text-xs` or `text-sm`), dense, and uppercase with slight tracking (`tracking-wide`) for metadata.
5. Micro-interactions (Mechanical Instancy): Any interactive icon MUST have a 50ms transition (`duration-75 ease-out`) with a subtle background shift on hover. No slow, floaty animations.

STRUCTURAL REQUIREMENTS:
1. The App Shell: Flex column, h-screen, w-screen, overflow-hidden.
2. THE HEADER ("The Compass" - h-12, fixed top):
   - Left: Minimalist breadcrumbs (e.g., "SODA CORE / SYSTEM / IDLE").
   - Center: An "OmniBar" (Ctrl+K style input). Small, glassmorphic, ghost borders, subtle 10% purple inner glow on focus.
   - Right: Minimalist Notification bell icon, User Avatar (simple geometric), and a dark-red "Kill Switch" octagon icon (for emergency VRAM purge).
3. THE FOOTER ("The Engine Room" - h-8, fixed bottom, mono-spaced font):
   - Left: Active LLM status (e.g., "🧠 Phi-4-mini [Q4_K]").
   - Center: Rust Daemon heartbeat (e.g., "DB: WAL Syncing").
   - Right: Telemetry data (VRAM: 4.2/6.0 GB, RAM: 14/32 GB, Spd: 22 t/s) and a Terminal Drawer toggle icon.

ANTI-HALLUCINATION PROTOCOL:
- REJECT corporate SaaS aesthetics.
- REJECT pure flat, dead colors. Use glows and blurs to define hierarchy.
- REJECT large, bubbly, fully-rounded buttons (use `rounded-sm` or `rounded-md` max).
- REJECT slow animations.

Generate the clean, production-ready React component for this foundation.
```