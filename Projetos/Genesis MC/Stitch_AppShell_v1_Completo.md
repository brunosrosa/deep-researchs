========================================

📋 ARTEFATO 1: DESIGN.MD GERADO

========================================

# Design System Specification: Cyber-Neuro Synthesis

## 1. Overview & Creative North Star: "The Ethereal Command"
This design system is built upon the "Cyber-Neuro Synthesis" paradigm—a fusion of advanced technical telemetry and "Nothing Design" principles. The goal is to move beyond the cluttered "HUD" tropes of sci-fi and instead create a sophisticated, editorial interface that feels like a biological extension of the user’s consciousness.

**The Creative North Star: The Ethereal Command.**
The interface should not feel like software "installed" on a screen; it should feel like a localized density of light and data within a void. We achieve this through:
*   **Intentional Asymmetry:** Avoid perfect grid-mirroring. Use "tension" by placing heavy telemetry data against wide-open negative space.
*   **Tonal Depth:** Replacing structural borders with light-based depth.
*   **Ephemeral Motion:** Transitions are near-instant (75ms), mimicking the speed of neural firing rather than mechanical sliding.

---

## 2. Colors & Surface Philosophy
The palette is rooted in a deep, atmospheric "Substrate" that avoids the amateur mistake of #000 pitch black, favoring instead a layered, cosmic neutral.

### The Substrate
*   **Global Background:** A radial gradient centered on the primary focal point.
    *   `from-zinc-900` (Inner)
    *   `via-[#0a0a0e]` (Middle)
    *   `to-black` (Outer edge)

### Surface Hierarchy (The "No-Line" Rule)
Sectioning must be achieved through background shifts, never 1px solid lines.
*   **Surface (Base):** `#0e0e12` – The foundational plane.
*   **Surface-Container-Low:** `#131317` – For subtle grouping of secondary data.
*   **Surface-Container-High:** `#1f1f25` – For interactive panels and active telemetry.
*   **The Glass & Gradient Rule:** Use `bg-black/40 backdrop-blur-xl` for any floating modal or overlay. Main CTAs should utilize a subtle gradient from `primary` (#ba9eff) to `primary_container` (#ac91f1) to give the light "weight."

---

## 3. Typography: Technical Editorial
We utilize two typefaces to distinguish between "Human Intent" and "System Data."

*   **Space Grotesk (Headings/UI):** The voice of the user. It is expressive yet engineered.
    *   **Display-LG (3.5rem):** Used for singular mission-critical metrics or high-level section headers.
    *   **Headline-SM (1.5rem):** Used for panel titles.
*   **Space Mono (Telemetry/Technical):** The voice of the machine.
    *   **Label-MD (0.75rem):** Used for all data readouts, timestamps, and coordinate tracking.

**Hierarchy Strategy:** Always pair a large `Display-MD` Space Grotesk title with a tiny `Label-SM` Space Mono subtitle. This contrast in scale creates a high-end, bespoke editorial feel that standard UI lacks.

---

## 4. Elevation & Depth: The Layering Principle
Traditional structural lines are prohibited. Instead, we use "Tonal Stacking."

*   **Ambient Shadows (The Neural Pulse):** For floating elements, use a signature glow instead of a black shadow: `shadow-[0_0_15px_rgba(186,158,255,0.15)]`. This mimics the radiance of an OLED screen.
*   **The Ghost Border:** If high-contrast accessibility is required, use the `outline_variant` (#48474c) at **15% opacity**. It should be felt, not seen.
*   **Haptic Transitions:** `duration-75 ease-out`. This system must feel incredibly responsive. Hover states should reflect an immediate "pulse" in brightness rather than a slow fade.

---

## 5. Components

### Buttons & Interaction
*   **Primary:** Gradient background (`primary` to `primary_container`), `on_primary` text. No rounded corners beyond `0.25rem`. 
*   **Tertiary (Ghost):** No background, `Space Mono` text, `primary` color. Underline on hover only.
*   **Neural Chips:** Small, high-density data points using `surface_container_high`. Use `primary` for the "active" state indicator—a 2px dot rather than a full color fill.

### Input Fields & Telemetry
*   **Text Inputs:** No background fill. Only a bottom "Ghost Border" (15% opacity). Upon focus, the border pulses to 100% `primary` opacity.
*   **Checkboxes/Radios:** Square (`rounded-sm`). Checked states use the "Neural Pulse Glow" to indicate vitality.

### Cards & Lists (The "Anti-Divider" Rule)
*   **Cards:** Forbid the use of divider lines. Separate content using `1.5rem` to `2rem` of vertical whitespace or a transition from `surface-container-low` to `surface-container-highest`.
*   **Lists:** Leading elements (icons/numbers) should be set in `Space Mono` at `0.6875rem` to emphasize the technical nature of the mission control substrate.

---

## 6. Do’s and Don’ts

### Do:
*   **Nesting:** Place a `surface_container_highest` element inside a `surface_container_low` section to create natural focus.
*   **Negative Space:** Allow headers to "breathe" with at least 64px of top margin.
*   **Micro-Telemetry:** Add non-interactive `Space Mono` labels like `[SYS_LOAD_04]` in corners to reinforce the Cyber-Neuro aesthetic.

### Don't:
*   **No #000:** Never use pure pitch black for backgrounds; it kills the "depth" of the radial gradient.
*   **No Rounded-3XL:** The maximum corner radius is `xl` (0.75rem). The system must feel precise and sharp, not "bubbly" or consumer-grade.
*   **No Heavy Shadows:** Never use `black/50` drop shadows. If it doesn't glow, it shouldn't have a shadow.
*   **No Dividers:** If you feel the need to draw a line to separate content, increase the whitespace or shift the background tone instead.

---

## 7. Signature Element: The Pulse Transition
Whenever a new data panel is initialized, it should not slide. It should appear at 90% opacity and instantly "snap" to 100% with a slight `backdrop-blur` increase. This is the hallmark of the Cyber-Neuro Synthesis: speed over ornamentation.



========================================

💻 ARTEFATO 2: CODE.JSX / HTML GERADO

========================================

<!DOCTYPE html>

  

<html class="dark" lang="en"><head>

<meta charset="utf-8"/>

<meta content="width=device-width, initial-scale=1.0" name="viewport"/>

<title>GENESIS Mission Control</title>

<script src="https://cdn.tailwindcss.com?plugins=forms,container-queries"></script>

<link href="https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@300;400;500;600;700&amp;family=Space+Mono:wght@400;700&amp;display=swap" rel="stylesheet"/>

<link href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:wght,FILL@100..700,0..1&amp;display=swap" rel="stylesheet"/>

<link href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:wght,FILL@100..700,0..1&amp;display=swap" rel="stylesheet"/>

<script id="tailwind-config">

        tailwind.config = {

          darkMode: "class",

          theme: {

            extend: {

              "colors": {

                      "tertiary-dim": "#f0779d",

                      "on-tertiary-fixed": "#380018",

                      "tertiary": "#ff97b5",

                      "on-tertiary": "#6a0934",

                      "on-secondary-container": "#e3c4ff",

                      "primary-fixed-dim": "#ac91f1",

                      "secondary-fixed-dim": "#dab4ff",

                      "surface-variant": "#25252b",

                      "secondary-fixed": "#e4c6ff",

                      "on-error-container": "#ffb2b9",

                      "primary-container": "#ac91f1",

                      "on-tertiary-fixed-variant": "#701039",

                      "on-primary-fixed-variant": "#40247e",

                      "tertiary-fixed-dim": "#f67ca3",

                      "surface-tint": "#ba9eff",

                      "on-surface": "#fcf8fe",

                      "error": "#ff6e84",

                      "secondary-container": "#5e2c91",

                      "surface-container": "#19191e",

                      "outline": "#76757a",

                      "error-dim": "#d73357",

                      "surface": "#0e0e12",

                      "on-background": "#fcf8fe",

                      "primary": "#ba9eff",

                      "secondary": "#c08cf7",

                      "surface-container-lowest": "#000000",

                      "inverse-primary": "#694fa9",

                      "primary-fixed": "#ba9eff",

                      "on-error": "#490013",

                      "on-secondary-fixed-variant": "#69379c",

                      "tertiary-container": "#fd81a8",

                      "surface-container-high": "#1f1f25",

                      "on-surface-variant": "#acaab0",

                      "on-primary-container": "#2b0569",

                      "on-secondary-fixed": "#4b147d",

                      "background": "#0e0e12",

                      "tertiary-fixed": "#ff8eb0",

                      "outline-variant": "#48474c",

                      "surface-container-highest": "#25252b",

                      "inverse-surface": "#fcf8fe",

                      "secondary-dim": "#bb87f1",

                      "primary-dim": "#ac91f1",

                      "on-primary": "#371975",

                      "error-container": "#a70138",

                      "on-secondary": "#390068",

                      "surface-dim": "#0e0e12",

                      "surface-container-low": "#131317",

                      "on-tertiary-container": "#59002a",

                      "inverse-on-surface": "#555459",

                      "surface-bright": "#2c2b32",

                      "on-primary-fixed": "#1b004a"

              },

              "borderRadius": {

                      "DEFAULT": "0.125rem",

                      "lg": "0.25rem",

                      "xl": "0.5rem",

                      "full": "0.75rem"

              },

              "fontFamily": {

                      "headline": ["Space Grotesk"],

                      "body": ["Space Grotesk"],

                      "label": ["Space Mono"]

              }

            },

          },

        }

    </script>

<style>

        body {

            font-family: 'Space Grotesk', sans-serif;

            background-color: #0e0e12;

            color: #fcf8fe;

        }

        .telemetry {

            font-family: 'Space Mono', monospace;

        }

        .cyber-grid {

            background-image: linear-gradient(rgba(186, 158, 255, 0.02) 1px, transparent 1px),

                              linear-gradient(90deg, rgba(186, 158, 255, 0.02) 1px, transparent 1px);

            background-size: 40px 40px;

        }

    </style>

</head>

<body class="overflow-hidden selection:bg-primary/30">

<!-- Substrate Background -->

<div class="fixed inset-0 z-[-1] bg-[radial-gradient(ellipse_at_center,_var(--tw-gradient-stops))] from-zinc-900 via-[#0a0a0e] to-black">

<div class="absolute inset-0 cyber-grid"></div>

</div>

<!-- TopNavBar (The Compass) -->

<nav class="fixed top-0 w-full h-14 z-50 bg-zinc-900/80 backdrop-blur-xl flex items-center justify-between px-6 shadow-[0_0_15px_rgba(186,158,255,0.1)] border-b border-white/5">

<div class="flex items-center gap-6">

<span class="text-xl font-bold text-[#BA9EFF] uppercase tracking-widest font-['Space_Grotesk']">GENESIS</span>

<div class="flex items-center gap-2 text-zinc-500 font-['Space_Mono'] text-[10px] tracking-tighter">

<span>ROOT</span>

<span class="material-symbols-outlined text-[10px]">chevron_right</span>

<span class="text-[#BA9EFF]">MISSION_CONTROL</span>

</div>

</div>

<!-- OmniBar Search -->

<div class="flex-1 max-w-xl px-12">

<div class="relative group">

<span class="absolute left-3 top-1/2 -translate-y-1/2 material-symbols-outlined text-zinc-500 group-focus-within:text-[#BA9EFF] transition-colors">search</span>

<input class="w-full bg-white/5 border-none ring-1 ring-white/10 focus:ring-2 focus:ring-[#BA9EFF]/20 rounded-lg py-1.5 pl-10 pr-4 text-xs font-['Space_Mono'] transition-all placeholder:text-zinc-700" placeholder="EXEC_COMMAND_OR_SEARCH..." type="text"/>

</div>

</div>

<div class="flex items-center gap-4">

<div class="flex gap-4 text-zinc-500 font-['Space_Mono'] text-xs">

<button class="hover:text-white transition-all duration-75 uppercase">MISSION</button>

<button class="hover:text-white transition-all duration-75 uppercase">TELEMETRY</button>

<button class="hover:text-white transition-all duration-75 uppercase">COORDS</button>

</div>

<div class="h-6 w-[1px] bg-white/10 mx-2"></div>

<div class="flex items-center gap-3">

<span class="material-symbols-outlined text-zinc-500 hover:text-[#BA9EFF] cursor-pointer transition-colors" data-icon="notifications">notifications</span>

<span class="material-symbols-outlined text-zinc-500 hover:text-[#BA9EFF] cursor-pointer transition-colors" data-icon="settings">settings</span>

<div class="w-8 h-8 rounded-lg overflow-hidden border border-[#BA9EFF]/20">

<img class="w-full h-full object-cover" data-alt="Stylized abstract digital portrait with violet neon accents and glitch art aesthetic" src="https://lh3.googleusercontent.com/aida-public/AB6AXuAdfFXm1mQTUG3QhAgjXvCQspYRVwDVH6kVUwujOAAqqlICW15MsH0er3AaEI1E9RFGfnyZ4lDk1JOS8uwp-uMeorVapCBEwCPoS-egQD0_Nlr5qhp57JNE3yKsYA7Bf6btUnLU6JJr8uLAX4WFHfjt4J3k2IkgkD1-40unVgWc-w58LisWmTqQTv5jahLmWUg5jlL8EoZSZJkgLnQFhF9-FpKdqLbcVEYFJhNi1Y-b6RCk80HFltQNJga8-90kCNMsrccFXVq-LQ"/>

</div>

</div>

</div>

</nav>

<!-- SideNavBar (Governor Rail) -->

<aside class="fixed left-0 top-14 h-[calc(100vh-3.5rem-2rem)] w-64 z-40 bg-[#0e0e12]/60 backdrop-blur-2xl border-r border-white/5 flex flex-col py-8 gap-2">

<div class="px-6 mb-8">

<h2 class="text-[#BA9EFF] font-black font-['Space_Mono'] text-sm tracking-widest">GOVERNOR</h2>

<p class="text-[10px] text-zinc-600 font-['Space_Mono']">SYSTEM_ACTIVE_01</p>

</div>

<div class="flex flex-col gap-1">

<a class="bg-[#BA9EFF]/10 text-[#BA9EFF] border-r-2 border-[#BA9EFF] flex items-center gap-3 px-6 py-3 font-['Space_Mono'] text-xs uppercase transition-colors" href="#">

<span class="material-symbols-outlined text-lg" data-icon="waves">waves</span>

                FLOW

            </a>

<a class="text-zinc-600 flex items-center gap-3 px-6 py-3 font-['Space_Mono'] text-xs uppercase hover:bg-[#1f1f25] hover:text-zinc-200 transition-colors duration-75" href="#">

<span class="material-symbols-outlined text-lg" data-icon="database">database</span>

                VAULT

            </a>

<a class="text-zinc-600 flex items-center gap-3 px-6 py-3 font-['Space_Mono'] text-xs uppercase hover:bg-[#1f1f25] hover:text-zinc-200 transition-colors duration-75" href="#">

<span class="material-symbols-outlined text-lg" data-icon="build">build</span>

                FORGE

            </a>

<a class="text-zinc-600 flex items-center gap-3 px-6 py-3 font-['Space_Mono'] text-xs uppercase hover:bg-[#1f1f25] hover:text-zinc-200 transition-colors duration-75" href="#">

<span class="material-symbols-outlined text-lg" data-icon="memory">memory</span>

                CORE

            </a>

</div>

<div class="mt-auto px-6">

<button class="w-full bg-primary text-on-primary-fixed font-bold text-xs py-3 rounded-lg hover:brightness-110 active:scale-95 transition-all uppercase tracking-widest">

                INITIALIZE

            </button>

</div>

</aside>

<!-- Main Content (The Nexus) -->

<main class="ml-64 mt-14 h-[calc(100vh-3.5rem-2rem)] overflow-y-auto bg-transparent relative">

<!-- Focus Rack -->

<div class="sticky top-0 z-30 bg-surface/80 backdrop-blur-md border-b border-white/5 px-8 flex items-center gap-8">

<button class="py-4 text-xs font-bold font-['Space_Mono'] border-b-2 border-[#BA9EFF] text-[#BA9EFF] tracking-widest">ARCHITECTURE</button>

<button class="py-4 text-xs font-bold font-['Space_Mono'] text-zinc-500 hover:text-zinc-300 transition-colors tracking-widest">SCHEMA</button>

<button class="py-4 text-xs font-bold font-['Space_Mono'] text-zinc-500 hover:text-zinc-300 transition-colors tracking-widest">LOGS</button>

</div>

<!-- Nexus Workspace -->

<div class="p-12 max-w-7xl mx-auto">

<header class="mb-16">

<div class="text-[#BA9EFF] text-xs font-['Space_Mono'] mb-2">[SYS_LOAD_04] / NEXUS_ID: 9812-AX</div>

<h1 class="text-6xl font-bold font-['Space_Grotesk'] tracking-tighter text-white mb-4">Central Neural Core</h1>

<p class="text-zinc-500 max-w-xl font-medium leading-relaxed">System architecture deployment visualization and real-time telemetry orchestration for the Genesis project.</p>

</header>

<!-- Bento Grid -->

<div class="grid grid-cols-12 gap-6">

<!-- Main Visualizer -->

<div class="col-span-8 bg-surface-container-low rounded-xl p-6 border border-white/5 relative overflow-hidden group min-h-[400px]">

<div class="absolute inset-0 opacity-40 group-hover:opacity-60 transition-opacity">

<img class="w-full h-full object-cover grayscale brightness-50" data-alt="Abstract digital network sphere with flowing light particles and deep cosmic colors" src="https://lh3.googleusercontent.com/aida-public/AB6AXuBB7Vp8eFa0LizkPaVD7gboC7LIKkkQzTPLFLwUWM_ZYz0JUmbmYyww8iQnMvjXAUQBpmeUbFYqtUvG383iFh7pjX4Jp_j1ENHooYiwrnCi8JVHXX83nZ6mlNmnlI8F1l9XnDX9q7G_8SCdFxXYHzxR-8yCiEsmWJXSO2PMBDptQZ4TahgH20tp-AhQ2qYDP3lYi_lDQQVUs2S1P58Cuq-Nz5Nnxx70k59dLA4KTFBbcLH2QBW1_C-reCyj5PeWmydMeQvtS3McPw"/>

</div>

<div class="relative z-10 h-full flex flex-col justify-between">

<div class="flex justify-between items-start">

<div>

<span class="bg-[#BA9EFF]/20 text-[#BA9EFF] text-[10px] font-['Space_Mono'] px-2 py-1 rounded">LIVE_FEED</span>

<h3 class="text-2xl font-bold mt-4">Structural Analysis</h3>

</div>

<span class="material-symbols-outlined text-zinc-500" data-icon="fullscreen">fullscreen</span>

</div>

<div class="flex gap-4">

<div class="bg-black/60 backdrop-blur-md p-4 rounded-lg border border-white/5">

<div class="text-[10px] text-zinc-500 font-['Space_Mono'] mb-1">NODE_LATENCY</div>

<div class="text-xl font-bold text-[#BA9EFF]">14.2ms</div>

</div>

<div class="bg-black/60 backdrop-blur-md p-4 rounded-lg border border-white/5">

<div class="text-[10px] text-zinc-500 font-['Space_Mono'] mb-1">CONCURRENT_THREADS</div>

<div class="text-xl font-bold text-[#BA9EFF]">1,024</div>

</div>

</div>

</div>

</div>

<!-- Metrics Column -->

<div class="col-span-4 flex flex-col gap-6">

<div class="bg-surface-container-high rounded-xl p-6 border border-white/5 hover:border-[#BA9EFF]/30 transition-all duration-75">

<div class="flex items-center gap-3 mb-6">

<span class="material-symbols-outlined text-[#BA9EFF]" data-icon="bolt">bolt</span>

<span class="font-bold text-sm tracking-widest uppercase">Energy Vector</span>

</div>

<div class="h-1 bg-zinc-800 w-full rounded-full overflow-hidden mb-2">

<div class="h-full bg-[#BA9EFF] w-[74%]"></div>

</div>

<div class="flex justify-between font-['Space_Mono'] text-[10px]">

<span class="text-zinc-500">CONSUMPTION</span>

<span class="text-white">74%</span>

</div>

</div>

<div class="bg-surface-container-high rounded-xl p-6 border border-white/5 flex-1 flex flex-col justify-between">

<div>

<div class="flex items-center justify-between mb-8">

<span class="font-bold text-sm tracking-widest uppercase">VRAM Cluster</span>

<span class="material-symbols-outlined text-zinc-500 text-sm" data-icon="info">info</span>

</div>

<div class="flex flex-col gap-4">

<div class="flex items-center justify-between">

<span class="text-xs text-zinc-400">ALLOCATED</span>

<span class="text-xs font-['Space_Mono']">4.2 GB</span>

</div>

<div class="flex items-center justify-between">

<span class="text-xs text-zinc-400">RESERVED</span>

<span class="text-xs font-['Space_Mono']">8.0 GB</span>

</div>

<div class="flex items-center justify-between">

<span class="text-xs text-zinc-400">POOL_ID</span>

<span class="text-xs font-['Space_Mono'] text-[#BA9EFF]">#992-0</span>

</div>

</div>

</div>

<button class="w-full text-[10px] text-zinc-500 border border-white/10 py-2 rounded font-['Space_Mono'] hover:text-white hover:bg-white/5 transition-all">PURGE_CACHE</button>

</div>

</div>

<!-- Telemetry Tape -->

<div class="col-span-12 bg-surface-container-low rounded-xl p-8 border border-white/5">

<div class="flex items-center justify-between mb-8">

<h3 class="font-bold tracking-widest uppercase text-sm">System Event Log</h3>

<span class="text-[10px] font-['Space_Mono'] text-[#BA9EFF]">[RECORDING_ENABLED]</span>

</div>

<div class="space-y-4 font-['Space_Mono'] text-[11px]">

<div class="flex gap-4 p-3 rounded hover:bg-white/5 transition-colors cursor-pointer">

<span class="text-zinc-600">12:00:04.12</span>

<span class="text-[#BA9EFF]">[SYSTEM]</span>

<span class="text-zinc-300">NEURAL_KERNEL_INIT: SUCCESS</span>

<span class="ml-auto text-zinc-700">0x0021A</span>

</div>

<div class="flex gap-4 p-3 rounded hover:bg-white/5 transition-colors cursor-pointer">

<span class="text-zinc-600">12:00:04.45</span>

<span class="text-tertiary">[ERROR]</span>

<span class="text-zinc-300">HANDSHAKE_TIMEOUT_OVERRIDE: MANUAL_INTERVENTION</span>

<span class="ml-auto text-zinc-700">0x00F12</span>

</div>

<div class="flex gap-4 p-3 rounded hover:bg-white/5 transition-colors cursor-pointer">

<span class="text-zinc-600">12:00:05.01</span>

<span class="text-[#BA9EFF]">[SYSTEM]</span>

<span class="text-zinc-300">TELEMETRY_STREAM_STABILIZED</span>

<span class="ml-auto text-zinc-700">0x00A04</span>

</div>

</div>

</div>

</div>

</div>

</main>

<!-- Footer (The Engine Room) -->

<footer class="fixed bottom-0 w-full h-8 z-50 bg-black/80 flex items-center px-4 border-t border-zinc-800/20 justify-between">

<div class="flex items-center gap-6">

<span class="text-zinc-700 font-['Space_Mono'] text-[10px] tracking-tighter uppercase">[TOKENS: 14K | VRAM: 4.2GB | PING: 2MS]</span>

<div class="flex gap-4 font-['Space_Mono'] text-[10px]">

<a class="text-zinc-700 hover:text-zinc-400 transition-colors uppercase" href="#">SYS_LOG</a>

<a class="text-zinc-700 hover:text-zinc-400 transition-colors uppercase" href="#">ERR_MON</a>

</div>

</div>

<div class="flex items-center gap-4 text-zinc-700 font-['Space_Mono'] text-[10px]">

<span class="flex items-center gap-1.5">

<span class="w-1.5 h-1.5 rounded-full bg-[#BA9EFF] animate-pulse"></span>

                STABLE_CONNECTION

            </span>

<span>v0.4.2-ALPHA</span>

</div>

</footer>

</body></html>