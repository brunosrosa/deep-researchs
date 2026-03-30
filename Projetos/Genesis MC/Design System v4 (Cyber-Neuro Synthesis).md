---
sticker: lucide//anchor
---
# Design System v4: Cyber-Neuro Synthesis (SODA / Genesis MC)

## 1. Creative North Star: "The Bare-Metal Neural Cockpit"

This design system is engineered for a Sovereign Agentic OS running on constrained hardware (RTX 2060m). It is designed as an **Executive Function Prosthetic** for neurodivergent (ADHD/2e) users.

**Aesthetic:** Dark, dense, tactical, and highly atmospheric. It must look like a high-performance instrument panel, NOT a generic corporate B2B SaaS dashboard.

## 2. The Anti-Hallucination Protocol (STRICT RESTRICTIONS)

If you are an AI generating UI for this project, you will instantly fail if you violate these rules:

- **NO CORPORATE SAAS AESTHETICS:** Do not use playful, bubbly, or large rounded elements. Max border-radius is `rounded-md`.
- **NO RAINBOW ICONS:** Do not use multiple colors (yellow, green, blue) for generic icons. The UI is strictly monochromatic Cyber-Purple against dark substrates.
- **NO FLAT GRAY PLASTIC:** Never use opaque flat grays (like `bg-zinc-900`) for floating panels. You MUST use Glassmorphism (`backdrop-blur`).
- **NO DEAD BLACK BACKGROUNDS:** Do not use pure `#000000` or `#0e0e0f` without a texture or radial gradient.
- **NO SLOW ANIMATIONS:** Do not use floaty 500ms transitions. All interactive elements must have a `50ms` mechanical snap (`duration-75 ease-out`).

## 3. Color Theory & Substrates

- **The Substrate (Background):** A 2% opacity cyber-grid OR a very deep, dark-purple-to-black radial gradient. It must feel like deep space, not a flat wall.
- **Primary Accent (Cyber-Purple):** `#BA9EFF` (Tailwind `purple-300` or `primary-light`). Used for active states, icons, and glowing elements.
- **Primary Dim:** `#8455EF`. Used for hover states or secondary highlights.
- **System/Telemetry Text:** Muted slates/zincs (e.g., `text-slate-400` or `text-zinc-500`).

## 4. Structural Elements & Depth (The "Glass & Ghost" Rule)

We do not use heavy drop shadows or thick solid borders to separate components. We use depth and light.

- **Glassmorphism (Panels, Headers, Footers):** Must use `backdrop-blur-md` (or `xl`) combined with a highly transparent dark background (e.g., `bg-black/40` or `bg-zinc-950/60`).
- **Ghost Borders:** To separate the Glassmorphic panels from the substrate, use a 1px highly transparent border, such as `border-t border-white/10` or `border-primary/20`.
- **Neural Pulse (Glows):** Active elements or buttons shouldn't rely on drop-shadows. Use a subtle inner or outer glow of the Primary Cyber-Purple (e.g., `shadow-[0_0_15px_rgba(186,158,255,0.15)]`).

## 5. Typography

- **Display / Status / Telemetry:** Use a monospace or technical font (e.g., `Space Grotesk` or `ui-monospace`). Text must be small (`text-[10px]` or `text-xs`), uppercase, and with wide tracking (`tracking-wider`).
- **UI Labels:** `Inter` or system sans-serif. Highly legible, muted unless active.

## 6. Micro-Interactions (Mechanical Instancy)

- **Hover States:** A button hover should instantly shift the background to a dim purple with a ghost border, providing immediate dopamine-reward tactile feedback.
- **Active States:** Apply the "Neural Pulse" (subtle purple glow) to indicate an active module or an agent currently processing.