---
sticker: lucide//corner-down-left
---
# Design System Specification: Cyber-Neuro Synthesis (NotebookLM Try - Apr/26)

## 1. Overview & Creative North Star: "The Ethereal Command"

This design system is built upon the "Cyber-Neuro Synthesis" paradigm—a fusion of advanced technical telemetry and "Nothing Design" principles. The goal is to move beyond the cluttered "HUD" tropes of sci-fi and instead create a sophisticated, editorial interface that feels like a biological extension of the user’s consciousness.

**The Creative North Star: The Ethereal Command.**  
The interface should not feel like software "installed" on a screen; it should feel like a localized density of light and data within a void. We achieve this through:

- **Intentional Asymmetry:** Avoid perfect grid-mirroring. Use "tension" by placing heavy telemetry data against wide-open negative space.
- **Tonal Depth:** Replacing structural borders with light-based depth.
- **Ephemeral Motion:** Transitions are near-instant (75ms), mimicking the speed of neural firing rather than mechanical sliding.

---

## 2. Colors & Surface Philosophy

The palette is rooted in a deep, atmospheric "Substrate" that avoids the amateur mistake of #000 pitch black, favoring instead a layered, cosmic neutral.

### The Substrate

- **Global Background:** A radial gradient centered on the primary focal point.
    - `from-zinc-900` (Inner)
    - `via-[#0a0a0e]` (Middle)
    - `to-black` (Outer edge)

### Surface Hierarchy (The "No-Line" Rule)

Sectioning must be achieved through background shifts, never 1px solid lines.

- **Surface (Base):** `#0e0e12` – The foundational plane.
- **Surface-Container-Low:** `#131317` – For subtle grouping of secondary data.
- **Surface-Container-High:** `#1f1f25` – For interactive panels and active telemetry.
- **The Glass & Gradient Rule:** Use `bg-black/40 backdrop-blur-xl` for any floating modal or overlay. Main CTAs should utilize a subtle gradient from `primary` (#ba9eff) to `primary_container` (#ac91f1) to give the light "weight."

---

## 3. Typography: Technical Editorial

We utilize two typefaces to distinguish between "Human Intent" and "System Data."

- **Space Grotesk (Headings/UI):** The voice of the user. It is expressive yet engineered.
    - **Display-LG (3.5rem):** Used for singular mission-critical metrics or high-level section headers.
    - **Headline-SM (1.5rem):** Used for panel titles.
- **Space Mono (Telemetry/Technical):** The voice of the machine.
    - **Label-MD (0.75rem):** Used for all data readouts, timestamps, and coordinate tracking.

**Hierarchy Strategy:** Always pair a large `Display-MD` Space Grotesk title with a tiny `Label-SM` Space Mono subtitle. This contrast in scale creates a high-end, bespoke editorial feel that standard UI lacks.

---

## 4. Elevation & Depth: The Layering Principle

Traditional structural lines are prohibited. Instead, we use "Tonal Stacking."

- **Ambient Shadows (The Neural Pulse):** For floating elements, use a signature glow instead of a black shadow: `shadow-[0_0_15px_rgba(186,158,255,0.15)]`. This mimics the radiance of an OLED screen.
- **The Ghost Border:** If high-contrast accessibility is required, use the `outline_variant` (#48474c) at **15% opacity**. It should be felt, not seen.
- **Haptic Transitions:** `duration-75 ease-out`. This system must feel incredibly responsive. Hover states should reflect an immediate "pulse" in brightness rather than a slow fade.

---

## 5. Components

### Buttons & Interaction

- **Primary:** Gradient background (`primary` to `primary_container`), `on_primary` text. No rounded corners beyond `0.25rem`.
- **Tertiary (Ghost):** No background, `Space Mono` text, `primary` color. Underline on hover only.
- **Neural Chips:** Small, high-density data points using `surface_container_high`. Use `primary` for the "active" state indicator—a 2px dot rather than a full color fill.

### Input Fields & Telemetry

- **Text Inputs:** No background fill. Only a bottom "Ghost Border" (15% opacity). Upon focus, the border pulses to 100% `primary` opacity.
- **Checkboxes/Radios:** Square (`rounded-sm`). Checked states use the "Neural Pulse Glow" to indicate vitality.

### Cards & Lists (The "Anti-Divider" Rule)

- **Cards:** Forbid the use of divider lines. Separate content using `1.5rem` to `2rem` of vertical whitespace or a transition from `surface-container-low` to `surface-container-highest`.
- **Lists:** Leading elements (icons/numbers) should be set in `Space Mono` at `0.6875rem` to emphasize the technical nature of the mission control substrate.

---

## 6. Do’s and Don’ts

### Do:

- **Nesting:** Place a `surface_container_highest` element inside a `surface_container_low` section to create natural focus.
- **Negative Space:** Allow headers to "breathe" with at least 64px of top margin.
- **Micro-Telemetry:** Add non-interactive `Space Mono` labels like `[SYS_LOAD_04]` in corners to reinforce the Cyber-Neuro aesthetic.

### Don't:

- **No #000:** Never use pure pitch black for backgrounds; it kills the "depth" of the radial gradient.
- **No Rounded-3XL:** The maximum corner radius is `xl` (0.75rem). The system must feel precise and sharp, not "bubbly" or consumer-grade.
- **No Heavy Shadows:** Never use `black/50` drop shadows. If it doesn't glow, it shouldn't have a shadow.
- **No Dividers:** If you feel the need to draw a line to separate content, increase the whitespace or shift the background tone instead.

---

## 7. Signature Element: The Pulse Transition

Whenever a new data panel is initialized, it should not slide. It should appear at 90% opacity and instantly "snap" to 100% with a slight `backdrop-blur` increase. This is the hallmark of the Cyber-Neuro Synthesis: speed over ornamentation.

-----

# THE DESIGN SYSTEM: CYBER-NEURO SYNTHESIS PROTOCOL (Genesis-Obsidian)

## 1. Overview & Creative North Star: "The Neural Cockpit"

This design system is engineered as a high-performance "Neural Cockpit"—a bare-metal, hyper-focused interface designed to eliminate cognitive load for neurodivergent operators. We reject the "soft and bubbly" corporate SaaS aesthetic in favor of a precision-instrument feel.

**Creative North Star: The Sovereign Operator.**  
The UI does not "guide" the user; it serves them. Through intentional asymmetry, dense information clusters, and a substrate that feels like deep space, we create a sanctuary for hyper-focus. We achieve this by breaking the traditional grid with "floating" glass modules and prioritizing raw data over decorative padding.

---

## 2. Colors & Substrate Strategy

We banish "flat" colors. Every surface must feel like a physical material or a light-emitting phosphor.

### The Substrate (The Void)

- **Primary Background:** Never flat black. Use a radial gradient: `Center: #17111b` (Surface Dim) to `Edges: #000000`.
- **The Cyber-Grid:** Overlay a `2% opacity` white grid (16px subdivisions) over the substrate to provide a sense of scale and mathematical "grounding."

### Accent & Signal Palette

- **Primary (Action):** `#BA9EFF` (Cyber-Purple). Used for active states and critical data paths.
- **Primary-Dim (Utility):** `#8455EF`. Used for secondary controls to prevent visual overstimulation.
- **Alert (Kill-Switch):** `#7f1d1d` (Dark-red). Reserved exclusively for destructive actions or critical system failures.

### The "No-Line" Rule

Prohibit 1px solid borders for sectioning. Boundaries are defined by:

1. **Tonal Shifting:** Moving from `surface_container_lowest` to `surface_container_low`.
2. **Glassmorphism:** Using `backdrop-blur-md` with `bg-black/40` to create a "lifted" perceptual layer.
3. **Ghost Borders:** A `white/10` opacity border is only permitted on floating glass panels to catch "specular" edge light.

---

## 3. Typography: Precision Hierarchy

Typography is treated as telemetry. It is dense, technical, and authoritative.

- **Display & Status (Space Grotesk):** Used for numbers, timers, and high-level headers. It should feel like a readout on a mechanical HUD.
    - _Style:_ `Uppercase`, `tracking-widest`, `font-bold`.
- **UI Labels & Data (Inter):** Used for all functional labels and body text.
    - _Style:_ `text-xs` or `text-sm`, `tracking-wide`, `uppercase`.
- **Intentional Asymmetry:** Avoid centering text. Align all telemetry to the left or right to mimic cockpit readouts. High-contrast scales (e.g., a `3.5rem` number next to a `0.68rem` label) are encouraged to create clear information hierarchies.

---

## 4. Elevation & Depth: Tonal Layering

Depth in this system is not "shadows"—it is "light-transmission."

- **The Layering Principle:**
    - **Base:** `surface_dim` (The Void).
    - **Mid-Tier:** `surface_container_low` (Integrated Modules).
    - **Top-Tier:** Glass panels with `backdrop-blur-md`.
- **Ambient Shadows:** Standard drop shadows are forbidden. If a panel must "float," use a large-radius (32px+) glow using the `primary` color at `4%` opacity to simulate a neon-light spill.
- **Mechanical Tactility:** Every interactive element must feel like a physical toggle. Use hard edges (`0px` radius) to maintain the "bare-metal" ethos.

---

## 5. Components: The Hardware Modules

### Buttons (Tactile Toggles)

- **Primary:** Background `#BA9EFF`, Text `#23005c` (On-Primary-Fixed). `0px` border-radius.
- **Secondary (Ghost):** No background. `1px` ghost border (`white/10`). Text `#BA9EFF`.
- **Interaction:** `duration-75 ease-out`. On hover, the background should "glitch" or shift opacity instantly (50ms) to provide immediate haptic-like feedback.

### Input Fields (Data Ports)

- **Style:** Underline only (2px `outline_variant`). No boxes.
- **Focus State:** Underline shifts to `primary` with a subtle `2%` glow.
- **Typography:** Input text uses `Space Grotesk` for that "entry-code" feel.

### Cards & Lists (Telemetry Clusters)

- **Forbid Dividers:** Use vertical spacing (`spacing-4`) or a `10%` shift in surface tone to separate items.
- **Nesting:** A list of items should sit on a `surface_container_highest` background to pull it forward from the substrate.

### The Octagon Kill-Switch (Alerts)

- **Geometry:** Use a clipped-corner (octagon) shape for critical alerts.
- **Color:** `bg-tertiary_container` (#7d1c1c).
- **Interaction:** Requires a "Long Press" (300ms) to activate, visually represented by a filling border-trace.

---

## 6. Do’s and Don’ts

### Do:

- **Use "Dense" Spacing:** ADHD users often benefit from seeing the "whole picture." Use the `0.5` to `2` spacing tokens for tight data clusters.
- **Embrace Hard Edges:** Keep all corner radii at `0px`. Roundness is "soft"—this system is "hard."
- **Use Motion Sparingly:** Transitions must be near-instant (50ms). We want "snappy," not "smooth."

### Don't:

- **No Bubbly UI:** Avoid any component that looks like it belongs on a mobile consumer app.
- **No Flat Gray:** Use the `surface` tokens which are tinted with purple/black to maintain the "Cyber-Neuro" atmosphere.
- **No Slow Animations:** Avoid "bouncing" or "sliding" animations. Elements should "blink" into existence or fade rapidly.

---

## 7. Interaction Protocol

To satisfy the "tactile" requirement, every click should feel like a physical connection.

- **Active State:** When a component is pressed, shift the `bg-opacity` by 20% or invert the colors instantly.
- **Feedback:** Use high-frequency visual "pings" (small 2px squares that flash at the corners of a module) to acknowledge system updates.

-----

# Design System: Cyber-Neuro Synthesis (SODA / Genesis MC)

## 1. Creative North Star: "The Bare-Metal Neural Cockpit"

This design system is engineered for a Sovereign Agentic OS running on constrained hardware (RTX 2060m). It is designed as an **Executive Function Prosthetic** for neurodivergent (ADHD/2e) users.

**Aesthetic:** Dark, dense, tactical, and highly atmospheric. It must look like a high-performance instrument panel, NOT a generic corporate B2B SaaS dashboard.

## 2. The Anti-Hallucination Protocol (STRICT RESTRICTIONS)

If you are an AI generating UI for this project, you will instantly fail if you violate these rules:

- **NO CORPORATE SAAS AESTHETICS:** Do not use playful, bubbly, or large rounded elements. Max border-radius is `rounded-md`.
- **NO RAINBOW ICONS:** Do not use multiple colors (yellow, green, blue) for generic icons. The UI is strictly monochromatic Cyber-Purple against dark substrates.
- **NO FLAT GRAY PLASTIC:** Never use opaque flat grays (like `bg-zinc-900`) for floating panels. You MUST use Glassmorphism (`backdrop-blur`).
- **NO DEAD BLACK BACKGROUNDS:** Do not use pure `#000000` or `#0e0e0f` without a texture or radial gradient.
- **NO SLOW ANIMATIONS:** Do not use floaty 500ms transitions. All interactive elements must have a `50ms` mechanical snap (`duration-75 ease-out`).

## 3. Color Theory & Substrates

- **The Substrate (Background):** A 2% opacity cyber-grid OR a very deep, dark-purple-to-black radial gradient. It must feel like deep space, not a flat wall.
- **Primary Accent (Cyber-Purple):** `#BA9EFF` (Tailwind `purple-300` or `primary-light`). Used for active states, icons, and glowing elements.
- **Primary Dim:** `#8455EF`. Used for hover states or secondary highlights.
- **System/Telemetry Text:** Muted slates/zincs (e.g., `text-slate-400` or `text-zinc-500`).

## 4. Structural Elements & Depth (The "Glass & Ghost" Rule)

We do not use heavy drop shadows or thick solid borders to separate components. We use depth and light.

- **Glassmorphism (Panels, Headers, Footers):** Must use `backdrop-blur-md` (or `xl`) combined with a highly transparent dark background (e.g., `bg-black/40` or `bg-zinc-950/60`).
- **Ghost Borders:** To separate the Glassmorphic panels from the substrate, use a 1px highly transparent border, such as `border-t border-white/10` or `border-primary/20`.
- **Neural Pulse (Glows):** Active elements or buttons shouldn't rely on drop-shadows. Use a subtle inner or outer glow of the Primary Cyber-Purple (e.g., `shadow-[0_0_15px_rgba(186,158,255,0.15)]`).

## 5. Typography

- **Display / Status / Telemetry:** Use a monospace or technical font (e.g., `Space Grotesk` or `ui-monospace`). Text must be small (`text-[10px]` or `text-xs`), uppercase, and with wide tracking (`tracking-wider`).
- **UI Labels:** `Inter` or system sans-serif. Highly legible, muted unless active.

## 6. Micro-Interactions (Mechanical Instancy)

- **Hover States:** A button hover should instantly shift the background to a dim purple with a ghost border, providing immediate dopamine-reward tactile feedback.
- **Active States:** Apply the "Neural Pulse" (subtle purple glow) to indicate an active module or an agent currently processing.

------

# DESIGN.md (Cyber-Neuro Synthesis)

## Design System Specification: The Neural Cockpit

## 1. Overview & Creative North Star

### Creative North Star: "The Silent Architect"

This design system rejects the "noisy" nature of modern SaaS interfaces in favor of a high-fidelity, atmospheric environment designed for the "Executive Function." It is a visual manifestation of a calm, hyper-focused mind. By merging the brutalist precision of a developer's kernel with the ethereal translucency of a premium OS, we create a **Cyber-Neuro Synthesis**.

The layout moves beyond the rigid grid. It utilizes intentional asymmetry and "The Empty Canvas" philosophy to ensure the user is never confronted with a "wall of data." We leverage high-contrast typography scales and deep, spatial layering to anchor the user’s focus, ensuring the UI acts as an extension of the user’s intent rather than a distraction from it.

---

## 2. Color Palette & The Anti-RSD Protocol

The color system is the primary tool for emotional regulation and cognitive load management.

### The Substrate & Surface Hierarchy

We utilize a "Nesting" logic to define depth. Avoid 1px solid borders for sectioning. Boundaries are defined by shifting between `surface` tiers.

* **The Substrate**: `background` (#0e0e0f) is the infinite void.
* **Layering Rule**: To create a section, place a `surface-container-low` (#131314) element upon the `background`. If further nesting is required, use `surface-container-high` (#201f21). This creates a soft, natural lift that feels integrated, not "pasted on."

### The "No-Line" Rule

Explicitly prohibit 100% opaque, 1px solid borders for layout division. Contrast must be achieved via background shifts or ****"Ghost Borders"**** (the `outline-variant` token at 10-20% opacity).

### The Anti-RSD (Rejection Sensitive Dysphoria) Protocol

Neurodivergent users often perceive red/yellow as punitive alerts, triggering cortisol spikes.

* **AI Processing**: Under no circumstances use yellow or orange. All AI computation, "thinking" states, and skeleton loaders must use `secondary` (****Cyber-Blue****, #61c2ff).
* **The Grit**: Use `primary` (*Neon-Violet*, #ba9eff) for user-driven actions. This is the dopamine trigger, signaling agency and successful execution.

### Glass & Gradient Rule

Floating elements (Modals, Hover Menus) must utilize Tauri’s native translucency with `backdrop-blur-xl`. Apply a subtle linear gradient from `primary` (#ba9eff) to `primary-dim` (#8455ef) at a 15% opacity for hero CTAs to provide a "signature" glow that flat colors cannot replicate.

---

## 3. Typography: Editorial Authority

The typography system balances the "grit" of a mission control center with the legibility of a high-end editorial journal.

* **Display & Headlines**: `spaceGrotesk`. This font provides the geometric harshness required for a "Cyber-Kernel" feel. Use `display-lg` (3.5rem) with tighter letter-spacing (-0.02em) for impactful, low-distraction landing states.
* **Body & Labels**: `inter`. The "workhorse" of the system. It is hyper-legible and recedes into the background, allowing the content to lead.
* **Hierarchy as Navigation**: Use `label-sm` (all-caps, 0.05em tracking) for non-interactive metadata. This creates a clear visual distinction between "Data to Read" and "Actions to Take."

---

## 4. Elevation & Depth: Tonal Layering

Traditional shadows are too heavy for a high-performance "Neural Cockpit." We define depth through light and transparency.

* **The Layering Principle**: Stack `surface-container-lowest` cards on a `surface-container-low` section to create a "recessed" effect.
* **Ambient Glows:** When a floating element requires a shadow, use a tinted shadow (4-8% opacity) using the `primary-dim` color. This mimics the ambient light of a neon display reflecting off a dark surface.
* **Glassmorphism:**** Use `surface-container-high` at 70% opacity with a `backdrop-blur-xl`. This allows the "Substrate" to bleed through, maintaining the user’s spatial orientation even when windows overlap.

---

## 5. Components

Each component is a tool for "*Mechanical Instancy*".

### Buttons (The Grit)

* **Primary**: Background `primary` (#ba9eff), text `on-primary` (#39008c). Zero border.
* **Secondary**: Background `surface-container-highest`, border "Ghost Border" (15% `outline-variant`).
* **States**: Hover must trigger an immediate (50ms) background shift to `primary-dim`.

### Input Fields

* **Style**: Minimalist. No bottom line or box. Use `surface-container-low` as the background with `roundedness.sm`.
* **Focus State**: A 1px "Ghost Border" of `primary` and a subtle 15% `primary` outer glow.

### The Governor Rail (Sidebar)

* **Geography**: Must remain fixed at `spacing`.
* **Active State**: The active icon and text label use `primary`. Non-active states use `on-surface-variant` (#adaaab).
* **Interaction**: No auto-hide. The rail is the "Anchor" that prevents context-switching exhaustion.

### Neural Pulse (AI Feedback)

* **The Component**: A non-linear, "breathing" pulse (15% opacity) of `secondary` (#61c2ff).
* **Context**: Used behind text while AI is generating or as a "Ghost Glow" on the edge of the screen to signal background processing.

---

## 6. Do's and Don'ts

### Do

* **Use Progressive Disclosure:** Start with the "Empty Canvas." Only show complexity when the user interacts with an anchor.
* **Maintain Spatial Predictability:** Keep navigation elements in the same geographical coordinate at all times.
* **Leverage Micro-Copy**: Icons must always be accompanied by `label-md` text to reduce cognitive decryption.

### Don't

* **No Red Alerts for AI**: Never use red/orange for AI errors or thinking states. Use `error-dim` (#d73357) only for critical, system-breaking hardware failures.
* **No Floaty Motion**: Avoid 500ms+ animations. If the user clicks, the response must be felt in 50ms.
* **No 100% Opaque Borders**: This shatters the "Neural Cockpit" immersion and creates visual clutter that triggers sensory overload.