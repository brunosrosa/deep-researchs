Design System v4: Cyber-Neuro Synthesis (SODA / Genesis MC)

1. Creative North Star: "The Bare-Metal Neural Cockpit"

This design system is engineered for a Sovereign Agentic OS running on constrained hardware (RTX 2060m). It is designed as an **Executive Function Prosthetic** for neurodivergent (ADHD/2e) users.

**Aesthetic:** Dark, dense, tactical, and highly atmospheric. It must look like a high-performance instrument panel, NOT a generic corporate B2B SaaS dashboard.

2. The Anti-Hallucination Protocol (STRICT RESTRICTIONS)

If you are an AI generating UI for this project, you will instantly fail if you violate these rules:

- **NO CORPORATE SAAS AESTHETICS:** Do not use playful, bubbly, or large rounded elements. Max border-radius is `rounded-md`.- **NO RAINBOW ICONS:** Do not use multiple colors (yellow, green, blue) for generic icons. The UI is strictly monochromatic Cyber-Purple against dark substrates.- **NO FLAT GRAY PLASTIC:** Never use opaque flat grays (like `bg-zinc-900`) for floating panels. You MUST use Glassmorphism (`backdrop-blur`).- **NO DEAD BLACK BACKGROUNDS:** Do not use pure `#000000` or `#0e0e0f` without a texture or radial gradient.- **NO SLOW ANIMATIONS:** Do not use floaty 500ms transitions. All interactive elements must have a `50ms` mechanical snap (`duration-75 ease-out`).

3. Color Theory & Substrates

- **The Substrate (Background):** A 2% opacity cyber-grid OR a very deep, dark-purple-to-black radial gradient. It must feel like deep space, not a flat wall.- **Primary Accent (Cyber-Purple):** `#BA9EFF` (Tailwind `purple-300` or `primary-light`). Used for active states, icons, and glowing elements.- **Primary Dim:** `#8455EF`. Used for hover states or secondary highlights.- **System/Telemetry Text:** Muted slates/zincs (e.g., `text-slate-400` or `text-zinc-500`).

4. Structural Elements & Depth (The "Glass & Ghost" Rule)

We do not use heavy drop shadows or thick solid borders to separate components. We use depth and light.

- **Glassmorphism (Panels, Headers, Footers):** Must use `backdrop-blur-md` (or `xl`) combined with a highly transparent dark background (e.g., `bg-black/40` or `bg-zinc-950/60`).- **Ghost Borders:** To separate the Glassmorphic panels from the substrate, use a 1px highly transparent border, such as `border-t border-white/10` or `border-primary/20`.- **Neural Pulse (Glows):** Active elements or buttons shouldn't rely on drop-shadows. Use a subtle inner or outer glow of the Primary Cyber-Purple (e.g., `shadow-[0_0_15px_rgba(186,158,255,0.15)]`).

5. Typography

- **Display / Status / Telemetry:** Use a monospace or technical font (e.g., `Space Grotesk` or `ui-monospace`). Text must be small (`text-[10px]` or `text-xs`), uppercase, and with wide tracking (`tracking-wider`).- **UI Labels:** `Inter` or system sans-serif. Highly legible, muted unless active.

6. Micro-Interactions (Mechanical Instancy)

- **Hover States:** A button hover should instantly shift the background to a dim purple with a ghost border, providing immediate dopamine-reward tactile feedback.- **Active States:** Apply the "Neural Pulse" (subtle purple glow) to indicate an active module or an agent currently processing.

-----

Design System v4: Cyber-Neuro Synthesis (SODA / Genesis MC)

1. Creative North Star: "The Bare-Metal Neural Cockpit"

This design system is engineered for a Sovereign Agentic OS running on constrained hardware (RTX 2060m). It is designed as an **Executive Function Prosthetic** for neurodivergent (ADHD/2e) users.

**Aesthetic:** Dark, dense, tactical, and highly atmospheric. It must look like a high-performance instrument panel, NOT a generic corporate B2B SaaS dashboard.

2. The Anti-Hallucination Protocol (STRICT RESTRICTIONS)

If you are an AI generating UI for this project, you will instantly fail if you violate these rules:

- **NO CORPORATE SAAS AESTHETICS:** Do not use playful, bubbly, or large rounded elements. Max border-radius is `rounded-md`.- **NO RAINBOW ICONS:** Do not use multiple colors (yellow, green, blue) for generic icons. The UI is strictly monochromatic Cyber-Purple against dark substrates.- **NO FLAT GRAY PLASTIC:** Never use opaque flat grays (like `bg-zinc-900`) for floating panels. You MUST use Glassmorphism (`backdrop-blur`).- **NO DEAD BLACK BACKGROUNDS:** Do not use pure `#000000` or `#0e0e0f` without a texture or radial gradient.- **NO SLOW ANIMATIONS:** Do not use floaty 500ms transitions. All interactive elements must have a `50ms` mechanical snap (`duration-75 ease-out`).

3. Color Theory & Substrates

- **The Substrate (Background):** A 2% opacity cyber-grid OR a very deep, dark-purple-to-black radial gradient. It must feel like deep space, not a flat wall.- **Primary Accent (Cyber-Purple):** `#BA9EFF` (Tailwind `purple-300` or `primary-light`). Used for active states, icons, and glowing elements.- **Primary Dim:** `#8455EF`. Used for hover states or secondary highlights.- **System/Telemetry Text:** Muted slates/zincs (e.g., `text-slate-400` or `text-zinc-500`).

4. Structural Elements & Depth (The "Glass & Ghost" Rule)

We do not use heavy drop shadows or thick solid borders to separate components. We use depth and light.

- **Glassmorphism (Panels, Headers, Footers):** Must use `backdrop-blur-md` (or `xl`) combined with a highly transparent dark background (e.g., `bg-black/40` or `bg-zinc-950/60`).- **Ghost Borders:** To separate the Glassmorphic panels from the substrate, use a 1px highly transparent border, such as `border-t border-white/10` or `border-primary/20`.- **Neural Pulse (Glows):** Active elements or buttons shouldn't rely on drop-shadows. Use a subtle inner or outer glow of the Primary Cyber-Purple (e.g., `shadow-[0_0_15px_rgba(186,158,255,0.15)]`).

5. Typography

- **Display / Status / Telemetry:** Use a monospace or technical font (e.g., `Space Grotesk` or `ui-monospace`). Text must be small (`text-[10px]` or `text-xs`), uppercase, and with wide tracking (`tracking-wider`).- **UI Labels:** `Inter` or system sans-serif. Highly legible, muted unless active.

6. Micro-Interactions (Mechanical Instancy)

- **Hover States:** A button hover should instantly shift the background to a dim purple with a ghost border, providing immediate dopamine-reward tactile feedback.- **Active States:** Apply the "Neural Pulse" (subtle purple glow) to indicate an active module or an agent currently processing.

------

DESIGN.md (v3) (Cyber-Neuro Synthesis)

**# Design System Specification: The Neural Cockpit**

**## 1. Overview & Creative North Star**

**### Creative North Star: "The Silent Architect"**

This design system rejects the "noisy" nature of modern SaaS interfaces in favor of a high-fidelity, atmospheric environment designed for the "Executive Function." It is a visual manifestation of a calm, hyper-focused mind. By merging the brutalist precision of a developer's kernel with the ethereal translucency of a premium OS, we create a ****Cyber-Neuro Synthesis****.

The layout moves beyond the rigid grid. It utilizes intentional asymmetry and "The Empty Canvas" philosophy to ensure the user is never confronted with a "wall of data." We leverage high-contrast typography scales and deep, spatial layering to anchor the user’s focus, ensuring the UI acts as an extension of the user’s intent rather than a distraction from it.

---

**## 2. Color Palette & The Anti-RSD Protocol**

The color system is the primary tool for emotional regulation and cognitive load management.

**### The Substrate & Surface Hierarchy**

We utilize a "Nesting" logic to define depth. Avoid 1px solid borders for sectioning. Boundaries are defined by shifting between `surface` tiers.

* ****The Substrate:**** `background` (#0e0e0f) is the infinite void.

* ****Layering Rule:**** To create a section, place a `surface-container-low` (#131314) element upon the `background`. If further nesting is required, use `surface-container-high` (#201f21). This creates a soft, natural lift that feels integrated, not "pasted on."

**### The "No-Line" Rule**

Explicitly prohibit 100% opaque, 1px solid borders for layout division. Contrast must be achieved via background shifts or ****"Ghost Borders"**** (the `outline-variant` token at 10-20% opacity).

**### The Anti-RSD (Rejection Sensitive Dysphoria) Protocol**

Neurodivergent users often perceive red/yellow as punitive alerts, triggering cortisol spikes.

* ****AI Processing:**** Under no circumstances use yellow or orange. All AI computation, "thinking" states, and skeleton loaders must use `secondary` (****Cyber-Blue****, #61c2ff).

* ****The Grit:**** Use `primary` (****Neon-Violet****, #ba9eff) for user-driven actions. This is the dopamine trigger, signaling agency and successful execution.

**### Glass & Gradient Rule**

Floating elements (Modals, Hover Menus) must utilize Tauri’s native translucency with `backdrop-blur-xl`. Apply a subtle linear gradient from `primary` (#ba9eff) to `primary-dim` (#8455ef) at a 15% opacity for hero CTAs to provide a "signature" glow that flat colors cannot replicate.

---

**## 3. Typography: Editorial Authority**

The typography system balances the "grit" of a mission control center with the legibility of a high-end editorial journal.

* ****Display & Headlines:**** `spaceGrotesk`. This font provides the geometric harshness required for a "Cyber-Kernel" feel. Use `display-lg` (3.5rem) with tighter letter-spacing (-0.02em) for impactful, low-distraction landing states.

* ****Body & Labels:**** `inter`. The "workhorse" of the system. It is hyper-legible and recedes into the background, allowing the content to lead.

* ****Hierarchy as Navigation:**** Use `label-sm` (all-caps, 0.05em tracking) for non-interactive metadata. This creates a clear visual distinction between "Data to Read" and "Actions to Take."

---

**## 4. Elevation & Depth: Tonal Layering**

Traditional shadows are too heavy for a high-performance "Neural Cockpit." We define depth through light and transparency.

* ****The Layering Principle:**** Stack `surface-container-lowest` cards on a `surface-container-low` section to create a "recessed" effect.

* ****Ambient Glows:**** When a floating element requires a shadow, use a tinted shadow (4-8% opacity) using the `primary-dim` color. This mimics the ambient light of a neon display reflecting off a dark surface.

* ****Glassmorphism:**** Use `surface-container-high` at 70% opacity with a `backdrop-blur-xl`. This allows the "Substrate" to bleed through, maintaining the user’s spatial orientation even when windows overlap.

---

**## 5. Components**

Each component is a tool for "Mechanical Instancy."

**### Buttons (The Grit)**

* ****Primary:**** Background `primary` (#ba9eff), text `on-primary` (#39008c). Zero border.

* ****Secondary:**** Background `surface-container-highest`, border "Ghost Border" (15% `outline-variant`).

* ****States:**** Hover must trigger an immediate (50ms) background shift to `primary-dim`.

**### Input Fields**

* ****Style:**** Minimalist. No bottom line or box. Use `surface-container-low` as the background with `roundedness.sm`.

* ****Focus State:**** A 1px "Ghost Border" of `primary` and a subtle 15% `primary` outer glow.

**### The Governor Rail (Sidebar)**

* ****Geography:**** Must remain fixed at `spacing`.

* ****Active State:**** The active icon and text label use `primary`. Non-active states use `on-surface-variant` (#adaaab).

* ****Interaction:**** No auto-hide. The rail is the "Anchor" that prevents context-switching exhaustion.

**### Neural Pulse (AI Feedback)**

* ****The Component:**** A non-linear, "breathing" pulse (15% opacity) of `secondary` (#61c2ff).

* ****Context:**** Used behind text while AI is generating or as a "Ghost Glow" on the edge of the screen to signal background processing.

---

**## 6. Do's and Don'ts**

**### Do**

* ****Use Progressive Disclosure:**** Start with the "Empty Canvas." Only show complexity when the user interacts with an anchor.

* ****Maintain Spatial Predictability:**** Keep navigation elements in the same geographical coordinate at all times.

* ****Leverage Micro-Copy:**** Icons must always be accompanied by `label-md` text to reduce cognitive decryption.

**### Don't**

* ****No Red Alerts for AI:**** Never use red/orange for AI errors or thinking states. Use `error-dim` (#d73357) only for critical, system-breaking hardware failures.

* ****No Floaty Motion:**** Avoid 500ms+ animations. If the user clicks, the response must be felt in 50ms.

* ****No 100% Opaque Borders:**** This shatters the "Neural Cockpit" immersion and creates visual clutter that triggers sensory overload.