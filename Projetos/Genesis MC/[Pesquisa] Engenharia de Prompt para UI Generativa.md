# Relatório Técnico: Engenharia de Prompts e Vibe Design para Interfaces Bare-Metal em Ecossistemas Agênticos

## A Evolução do Vibe Design e a Arquitetura de Sistemas Autônomos

A engenharia de software contemporânea atravessa uma transição paradigmática impulsionada pela adoção de modelos fundacionais multimodais, alterando o foco do desenvolvimento estrito de código para a orquestração de intenções. Este fenômeno, inicialmente cunhado como "vibe coding", evoluiu rapidamente para abranger a camada visual e interativa das aplicações, estabelecendo o conceito de "Vibe Design". No epicentro desta transformação, plataformas como o Google Stitch redefiniram o fluxo de trabalho de prototipagem, permitindo que a arquitetura de interfaces de usuário (UI) seja gerada a partir de descrições em linguagem natural, imagens de referência e contextos semânticos.

A premissa do Vibe Design afasta-se da manipulação manual de coordenadas e componentes em telas em branco, exigindo que o arquiteto de software forneça a intenção de negócios, a carga emocional desejada e as restrições operacionais, delegando ao agente de inteligência artificial a execução generativa do layout, tipografia e estrutura de código. As atualizações do Google Stitch, implementadas a partir do final de 2025 e consolidadas em março de 2026, introduziram uma tela infinita nativa de IA, capacidades de prototipagem instantânea, agentes de design com raciocínio de longo prazo e a integração profunda com ecossistemas de desenvolvimento através do protocolo MCP (Model Context Protocol).

Contudo, a aplicação de ferramentas de Vibe Design para a concepção de sistemas hiper-especializados — como um Sistema Operacional Agêntico local construído em Rust e Tauri — revela vulnerabilidades inerentes à natureza estatística dos Modelos de Linguagem de Grande Escala (LLMs). O desenvolvimento do "Genesis Mission Control", um ambiente focado em telemetria, exige uma estética "Bare-Metal" e "Cyber-Purple", caracterizada por densidade informacional extrema, paletas de cores escuras e utilitarismo absoluto. Quando submetidos a essas exigências sem o devido rigor na engenharia de prompts, os modelos generativos falham de forma previsível, um fenômeno intrinsecamente ligado à composição de seus dados de treinamento.

## O Paradigma do Viés de Normalidade na Geração de Interfaces

O maior obstáculo na adoção de ferramentas como o Google Stitch para aplicações de nicho é o "viés de normalidade", um artefato direto do treinamento das redes neurais em vastos repositórios de interfaces web comerciais. A internet moderna é dominada por padrões de design de Software as a Service (SaaS) B2B, que privilegiam a conversão, a retenção de usuários novatos e a estética "limpa" ou amigável. Consequentemente, o espaço latente dos modelos de IA associa os conceitos de "interface", "dashboard" e "aplicativo" a esses padrões médios.

Na prática, isso se manifesta através do que a comunidade de desenvolvedores frequentemente denomina "Purple Problem" ou "SaaS Slop". Quando o modelo é instruído a criar um painel de controle, sua resposta padrão, na ausência de restrições severas, inclui a aplicação de gradientes de fundo, fontes sans-serif corporativas (como Inter ou Roboto), cantos excessivamente arredondados, margens e preenchimentos generosos, e uma disposição de elementos que dilui a densidade de dados em favor da "respiração" visual.

Este viés entra em conflito direto com os requisitos de um "Cockpit Neural", que demanda um particionamento rígido da tela, ausência de rolagem infinita e uma tipografia monoespaçada desenhada para a leitura rápida de logs e métricas de desempenho. Para superar o "Delivery Gap" — a diferença entre a intenção expressa pelo usuário e o resultado estatisticamente provável gerado pela IA — a engenharia de prompts deve ser reconfigurada. Não basta solicitar um estilo diferente; é imperativo construir uma gaiola de restrições semânticas e arquiteturais que impeça o modelo de regredir à média do seu conjunto de dados de treinamento.

|**Característica Arquitetural**|**O Viés de Normalidade (Padrão LLM)**|**Requisito do Cockpit "Bare-Metal"**|**Consequência do Viés na Geração**|
|---|---|---|---|
|**Densidade Espacial**|Baixa. Foco em _whitespace_ e respiro.|Extrema. Otimização de cada pixel da tela.|Interfaces esparsas que exigem rolagem desnecessária para visualizar telemetria.|
|**Tipografia**|Sans-serif humanista (Inter, Open Sans).|Monoespaçada técnica (JetBrains Mono).|Perda de alinhamento tabular em fluxos de logs e métricas financeiras/técnicas.|
|**Paleta de Cores**|Fundos claros ou gradientes "mágicos".|Observatório Escuro (Obsidian) com neons funcionais.|Fadiga visual e perda da distinção entre elementos ativos e passivos.|
|**Estrutura de Layout**|Fluida, responsiva para _mobile-first_.|Rígida, fixada na viewport, orientada a desktop.|O layout quebra ao tentar acomodar barras de ferramentas e painéis colapsáveis complexos.|

## Fundamentação Cognitiva: A Densidade Informacional e a Neurodivergência

O design de interfaces para usuários duplamente excepcionais (2e) — indivíduos que apresentam altas habilidades cognitivas concomitantes a condições como o Transtorno de Déficit de Atenção e Hiperatividade (TDAH) — exige a desconstrução de dogmas clássicos do design de usabilidade. As diretrizes tradicionais de acessibilidade, como o Web Content Accessibility Guidelines (WCAG), frequentemente abordam deficiências motoras e visuais, mas tendem a negligenciar ou simplificar excessivamente a acessibilidade cognitiva. O pressuposto comum e equivocado é que a redução da densidade da informação invariavelmente beneficia usuários com déficit de atenção.

A literatura sobre a neurobiologia do TDAH revela que o transtorno não é essencialmente uma incapacidade de prestar atenção, mas sim um déficit na regulação da atenção, o que inclui a capacidade de entrar em "hiperfoco" em ambientes que fornecem o nível adequado de estimulação contínua. Quando uma interface é excessivamente minimalista e oculta dados cruciais atrás de múltiplos cliques e menus ocultos (como menus hambúrguer), ela impõe uma carga severa à memória de trabalho do usuário. O usuário 2e precisa manter na mente onde a informação está localizada enquanto navega, o que acelera a fadiga decisória e induz à perda de contexto.

Por outro lado, o caos visual não estruturado atua como um gatilho para a sobrecarga sensorial. A solução para este paradoxo encontra-se na "Densidade Estruturada". A clareza em sistemas de alta complexidade não advém da ausência de dados, mas da codificação visual rigorosa de seus relacionamentos. Um painel denso torna-se perfeitamente navegável se empregar hierarquia, ritmo espacial, contraste previsível e consistência absoluta de estado.

Para a construção do "Genesis Mission Control", a interface deve minimizar cliques e expor a superfície operacional inteira imediatamente, operando como um painel de instrumentos em tempo real. O modelo de linguagem utilizado no Vibe Design deve ser parametrizado para gerar componentes que suportem essa densidade sem introduzir ruído periférico, como animações não funcionais, que são sabidamente prejudiciais ao foco do TDAH.

## Heurísticas de Aviação Aplicadas ao Vibe Design

A construção de um sistema operacional agêntico compartilha mais princípios fundamentais com o software de controle de missões aeroespaciais do que com o design web convencional. A operação contínua e a necessidade de interpretar telemetria em tempo real exigem a aplicação de padrões testados em ambientes críticos, os quais devem ser traduzidos para o vocabulário da engenharia de prompts.

A incorporação desses princípios na geração de UI garante que o modelo de IA abandone a criação de "landing pages" e se concentre na engenharia de consoles de comando, otimizando o fluxo visual do usuário para operações de baixa latência.

|**Princípio da Aviação**|**Descrição do Conceito**|**Tradução para o Framework Tailwind CSS e UI Generativa**|
|---|---|---|
|**Dark Display Philosophy**|Painéis permanecem escuros e silenciosos em estados nominais. Apenas anomalias ou necessidades de ação iluminam a tela, evitando fadiga visual constante.|Utilizar tons de `#05050A` e `slate-900` para fundos. Dados estáticos usam `text-slate-500`. Cores como `text-purple-500` ou `bg-red-500` são reservadas para alertas.|
|**Sterile Cockpit Rule**|Eliminação de qualquer atividade, conversa ou exibição de dados não essenciais durante fases críticas da operação, reduzindo a distração a zero.|Proibição explícita de bordas decorativas, sombras espalhadas (`shadow-xl`), fundos em gradiente e ilustrações geradas por IA. Cada elemento deve ter um propósito técnico atrelado aos agentes.|
|**Primary Field of View (FOV)**|Indicadores críticos e funções de uso frequente devem estar localizados a menos de 15 graus da linha de visão normal do operador.|Fixação da telemetria de agentes em um Footer constante. Uso de grids centralizados. Proibição de layouts expansivos que forcem o olhar aos extremos do monitor de forma contínua.|
|**Telemetry Actionability**|Os dados de telemetria não são apenas para visualização; a capacidade de intervir na origem do log deve estar a um clique de distância.|Integração de estados `hover` e foco interativos em tabelas de logs, como `hover:bg-purple-900/30`, tornando os fluxos de dados em botões de ação subliminares.|

## A Estrutura do Ecossistema DESIGN.md no Google Stitch

A mitigação do viés de normalidade e a imposição da estética "Bare-Metal" de alta densidade dependem da consistência ao longo de múltiplas iterações generativas. O Google Stitch introduziu um formato fundamental para ancorar as decisões visuais da inteligência artificial: o arquivo `DESIGN.md`. Ao contrário de guias de estilo tradicionais hospedados em plataformas vetoriais, este artefato é um manifesto em linguagem natural e formatação Markdown, projetado especificamente para ser lido, interpretado e respeitado por modelos multimodais de IA durante a síntese de código.

A funcionalidade primária do `DESIGN.md` é servir como a verdade fundamental do Design System. Quando o arquiteto envia um prompt solicitando uma nova interface, o Stitch anexa automaticamente o conteúdo deste arquivo ao contexto do modelo. Essa injeção pré-geração é a barreira técnica que impede a ferramenta de alucinar componentes e estilos de terceiros, forçando-a a utilizar os "Design Tokens" estritos definidos no documento. Ademais, a integração com o Model Context Protocol (MCP) e ferramentas como o Claude Code ou Google Antigravity permite que essas restrições sejam transportadas da fase de prototipagem em UI diretamente para a base de código do projeto Rust/Tauri.

Para o "Genesis Mission Control", o `DESIGN.md` não deve conter descrições filosóficas extensas, mas sim diretivas utilitárias, limitadas a parâmetros precisos. O texto a seguir exemplifica o conteúdo exato que deve ser fornecido ao sistema como o documento mestre do projeto.

### Arquitetura de Exemplo para o Arquivo DESIGN.md

# Genesis Mission Control (SODA) - Design System Specification

## 1. Core Intent and Cognitive Framework

- **Aesthetic Definition:** Neural Bare-Metal Cockpit. The UI must simulate high-end aerospace or terminal-based military grade interfaces.
- **Cognitive Profile:** Optimized for 2e (Twice Exceptional) and ADHD operators. The UI mandates MAXIMUM information density through structural predictability.
- **Anti-Patterns (STRICTLY PROHIBITED):** No SaaS B2B templates, no rounded floating cards, no drop shadows mimicking depth, no glassmorphism, no gradient backgrounds, no large whitespace, no playful interactions.

## 2. Global CSS Framework and Libraries

- **Styling Framework:** Tailwind CSS ONLY.
- **Icons:** `lucide-react` ONLY. Use consistent `size={16}` and `strokeWidth={1.5}`. Never generate inline SVG icons.
- **Geometry:** Borders must be hard-edged (`rounded-none`) or minimally softened (`rounded-sm`). Padding must be extremely tight (`p-1`, `p-2`).

## 3. Typography Constraints

- **Global Font:** Monospaced ONLY (e.g., Fira Code, JetBrains Mono). The `font-mono` Tailwind class must be forced globally on the body and all containers.
- **Hierarchy:**
    - `text-[10px]` tracking-tight for passive telemetry and log timestamps.
    - `text-xs` for secondary labels, grid headers, and status badges.
    - `text-sm` for active command inputs and primary readouts.
    - `text-base` for absolute top-level component titles. DO NOT use text larger than `text-lg` anywhere in the application.

## 4. Color Palette & Dark Display Philosophy

The interface operates on the "Dark Display" principle. Nominal operations are nearly black and silent. Color implies action or anomaly.

- **Backgrounds:**
    - Base Void: `#05050A` (applied to canvas and structural backgrounds).
    - Surface Elevation 1: `#0A0A12` (applied to panels, left menus).
    - Surface Elevation 2: `#11111A` (applied to inputs, hover states).
- **Borders & Dividers:**
    - Static grid lines: `border-slate-800` or `border-`.
- **Text & Data:**
    - Muted/Passive data: `text-slate-500`.
    - Active readouts: `text-slate-300`.
- **Cyber-Purple & Alert Accents (Use Sparingly):**
    - Primary Action / Agent Active State: `text-purple-500` or `#A855F7`.
    - Background Focus State: `bg-purple-900/30`.
    - Critical Alerts / Anomalies: `text-red-500` or `border-red-500`.

A adoção desta especificação elimina a ambiguidade. A partir do momento em que este artefato é fixado na raiz do projeto no Google Stitch, todas as alucinações baseadas no "Purple Problem" são suprimidas, e o modelo passa a atuar como um compilador das intenções arquiteturais definidas.

## A Anatomia do Prompt Perfeito para Vibe Design

A construção do prompt inicial (Zero-Shot) para a inicialização do ambiente visual é o ponto de inflexão crítico na engenharia do Vibe Design. Em abordagens convencionais, usuários tentam descrever a funcionalidade da aplicação (e.g., "Faça um aplicativo para gerenciar meus agentes de IA"), permitindo que a ferramenta assuma o controle do layout e da estética. Para suplantar essa liberdade criativa indesejada, o prompt deve ser estruturado como uma injeção de restrições de máquina, dividida em quatro domínios de controle específicos.

### Domínio 1: Definição de Papel e Intenção de Negócio

O modelo deve ser imediatamente posicionado fora de seu domínio de conforto. Assumir que a IA está agindo como um "Engenheiro de Sistemas Espaciais e Arquiteto de UI Crítica" calibra os pesos semânticos da rede para ignorar padrões de comércio eletrônico e marketing. A menção ao espectro 2e/TDAH neste bloco força o raciocínio do agente a priorizar a eficiência e a densidade sobre o espaçamento.

### Domínio 2: Restrições Negativas Estratégicas (Anti-Prompting)

A mitigação do viés de normalidade e do "Purple Slop" não se alcança apenas pedindo o que se quer, mas decretando explicitamente o que é inaceitável. O Anti-Prompting atua no bloqueio de tokens de alta probabilidade na geração. Se a instrução não contiver "NÃO use fontes sans-serif", a probabilidade estatística do Tailwind aplicar a família Inter é quase absoluta. O prompt deve vetar gradientes, sobras suaves e layouts fluidos centrados em páginas de rolagem.

### Domínio 3: Injeção de Dependências de Design (Design Tokens)

Embora o `DESIGN.md` forneça o contexto global, o prompt de geração deve reiterar as dependências críticas do stack tecnológico. Reafirmar que a geração deve usar apenas as utilidades do Tailwind CSS, acoplada à imposição restrita da biblioteca `lucide-react` para toda a iconografia, garante a modularidade do código gerado e a possibilidade de integração futura com o backend em Rust.

### Domínio 4: Mapeamento Espacial Geométrico (O App Shell)

A instrução final deve mapear o espaço de forma topológica. A IA generativa lida melhor com restrições fixas. Exigir o uso de `h-screen`, `w-screen`, e particionar as seções utilizando `flex` e `grid` dita a hierarquia do contêiner. O prompt especifica as dimensões absolutas do Header, Menu Lateral e Footer, garantindo que o restante da interface (o Canvas) seja perfeitamente contido na área residual da tela.

## Estratégia de Construção Modular e Gerenciamento de Estado

O erro operacional mais frequente no uso de plataformas de Vibe Design é a tentativa de construir interfaces complexas e de alta densidade em um único ciclo de geração. Quando uma interface excede a capacidade imediata de estruturação do LLM, os componentes começam a quebrar, regras de alinhamento são ignoradas e a alucinação de estilos genéricos reaparece na tentativa do modelo de resolver conflitos espaciais. A abordagem ideal repousa sobre o princípio de "Progressive Disclosure" através da construção modular com travamento de estado (State Locking).

As versões mais recentes do Google Stitch, potencializadas pelos recursos de Protótipos e pelo "Agent Manager" para exploração paralela, permitem que o desenvolvedor estabeleça iterações estruturais que preservam o layout e não o degradam.

A construção do App Shell deve seguir um sequenciamento estratégico:

|**Fase da Geração Modular**|**Foco do Prompt (Isolamento de Estado)**|**Meta Arquitetural**|
|---|---|---|
|**Fase 1: O Esqueleto (Scaffolding)**|Solicitar unicamente a estrutura externa. Uso exclusivo de blocos divisórios vazios e bordas coloridas temporárias para validar a geometria CSS Grid/Flexbox.|Estabelecer uma fundação imutável com `h-screen w-screen overflow-hidden`. Garantir que a tela não apresente barra de rolagem geral.|
|**Fase 2: Ancoragem dos Periféricos**|Focar as instruções no componente do Header e do Footer de Telemetria, instruindo a IA a ignorar o conteúdo do canvas e do menu lateral.|Criar a barra superior fixa e consolidar a sensação de IDE com uma barra inferior de rede/logs utilizando `text-[10px]`.|
|**Fase 3: População da Ferramenta de Navegação**|Concentrar o agente no Menu Esquerdo colapsável. Reforçar a regra de densidade utilizando apenas ícones Lucide empilhados com `gap-2`.|Remover menus expansivos e textuais que consomem área nobre do monitor. A navegação torna-se muscular e puramente simbólica.|
|**Fase 4: População do Canvas Central**|O comando deve ser estrito: "Mantenha o Header, Left Menu e Footer intocados. Modifique apenas a área do Canvas."|Implementar os widgets de controle, gráficos de estado agêntico e terminais, garantindo que o invólucro do sistema não seja perturbado pela complexidade interna.|

Esta metodologia de "micro-prompts" sequenciais atua como um sistema de testes A/B visuais instantâneos. Ao alterar um único componente por prompt, a probabilidade do Google Stitch manter o foco no documento `DESIGN.md` e preservar as restrições da arquitetura Bare-Metal aumenta substancialmente, evitando a regressão ao design comercial B2B.

## Glossário de Vibe Design: Vocabulário Estético de Comando

A sintaxe de comunicação com a inteligência artificial requer um vocabulário semântico específico. A IA associa descritores estéticos a aglomerados estatísticos de padrões de design, o que significa que as palavras escolhidas determinam as bibliotecas de interface invocadas. O uso de palavras-chave precisas (keywords) atua como gatilhos, enquanto o banimento de palavras proibidas (anti-keywords) desativa os templates predefinidos do modelo.

A seguir, a taxonomia linguística necessária para operar a ferramenta no desenvolvimento do "Genesis Mission Control":

### Dicionário de Comando (Palavras-Chave Obrigatórias)

|**Intenção de Design**|**Palavra-chave (Keyword) para o Prompt**|**Efeito Comportamental no Google Stitch**|
|---|---|---|
|**Identidade Visual Geral**|`Bare-metal`, `Cyber-purple`, `Neural Cockpit`, `Command Center`.|O modelo adota cores de baixa luminosidade, priorizando a função sobre o ornamento e aplicando neons funcionais.|
|**Gestão Espacial**|`High information density`, `Utilitarian`, `Zero wasted whitespace`, `Compact grid`.|Substitui os _paddings_ e _margins_ inflados (padrão de Tailwind B2B) por agrupamentos justos e tabelas compactas.|
|**Filosofia de Cores**|`Dark display philosophy`, `Obsidian background`, `High contrast telemetry`.|Garante fundos quase pretos e silencia os dados não ativos. Usa cores saturadas apenas em estados de falha ou foco.|
|**Estilo Tipográfico**|`Strictly monospaced`, `Technical readouts`, `Code-like typography`.|Impede a injeção de fontes sans-serif, forçando a classe `font-mono` para alinhamento e leitura analítica da tela.|

### Dicionário de Bloqueio (Palavras Proibidas)

A presença destas palavras em descrições naturais de design geralmente arruína a densidade requerida pelo TDAH, acionando o viés de normalidade :

- **Evitar:** `Modern`, `Clean`, `Friendly`, `Playful`. (Gatilhos que invocam cantos super arredondados, interfaces infantis e desperdício de espaço no monitor).
- **Evitar:** `SaaS Dashboard`, `B2B Platform`, `Corporate`. (Força a criação de menus gigantes na cor branca, contendo gráficos de vendas ou estatísticas de marketing inadequadas para telemetria agêntica).
- **Evitar:** `Glassmorphism`, `Soft gradients`, `Drop shadows`. (Gera ruído visual excessivo, criando camadas de desfoque que confundem o processamento cognitivo e fogem do estilo "Bare-Metal").

## O Prompt de Inicialização Mestre (Zero-Shot)

Este artefato constitui a injeção inicial de contexto para iniciar a geração do ambiente no Google Stitch. O modelo deve ser inserido exatamente como apresentado abaixo, juntamente com a anexação do arquivo `DESIGN.md` como contexto complementar. Esta instrução foi desenhada para superar a barreira da articulação (Articulation Barrier) e estabelecer a infraestrutura do App Shell em uma única etapa inicial, fixando as amarras para toda a iteração subsequente.

---

ACT AS A LEAD SYSTEMS ARCHITECT AND CRITICAL UI ENGINEER.

Your task is to generate the foundational React + Tailwind CSS "App Shell" for a local Agentic Operating System named "Genesis Mission Control (SODA)".

### 1. CORE VIBE & AESTHETIC DIRECTIVES

- **Vibe:** Bare-Metal, Neural Cockpit, Industrial Command Center, Cyber-Purple.
- **Target Audience:** Technical experts and 2e/ADHD operators who require MAXIMAL INFORMATION DENSITY, strict structural predictability, and zero distracting visual noise.
- **Visual Rule:** "Dark Display Philosophy". The UI must be predominantly dark, stealthy, and passive. Vibrant colors (e.g., Cyber-Purple, Neon Cyan, Red) are ONLY used to indicate active states, agent focus, or critical telemetry alerts.
- **ANTI-BIAS ENFORCEMENT:** DO NOT build a generic, friendly B2B SaaS dashboard. Absolutely NO large whitespace, NO soft drop-shadows, NO gradients, NO rounded-3xl elements, NO glassmorphism, and NO sans-serif fonts.

### 2. STRICT TECHNICAL CONSTRAINTS

- **Styling:** Use ONLY Tailwind CSS utility classes.
- **Typography:** Force `font-mono` on the entire layout. Text must be small and technical (`text-xs` and `text-[10px]` for passive data, `text-sm` for active inputs).
- **Icons:** Use ONLY `lucide-react` components with `size={16}` and `strokeWidth={1.5}`. Never hallucinate inline SVG paths.
- **Color Palette (Strict):**
    - Base Background: Use `#05050A` (deepest black/blue void).
    - Surface Backgrounds (Panels/Sidebar): Use `#0A0A12` or `slate-950`.
    - Borders: `border-slate-800` or `border-purple-900/40`.
    - Text: `text-slate-500` for muted passive data, `text-slate-200` for primary data.
    - Active States: `text-purple-500` with `bg-purple-900/30` background highlights.
- **Geometry:** Borders must be hard-edged (`rounded-none`) or minimally softened (`rounded-sm`). Padding must be extremely tight (`p-1`, `p-2`).

### 3. ARCHITECTURE & LAYOUT (The App Shell)

Create a full-viewport, fixed, non-scrollable layout structure: `h-screen w-screen overflow-hidden flex flex-col bg-[#05050A] font-mono text-slate-300`. It MUST contain the following four specific components:

1. **(Top Container):**
    - Height: `h-10`. Flex container, items centered, strict bottom border (`border-b border-purple-900/50`).
    - Content: A technical logo text "" on the left (accompanied by a Lucide `Cpu` icon). In the center, place global system CPU/RAM usage metrics. Window controls on the far right.
2. **(Middle Section, `flex-1 flex overflow-hidden`):**
        - Width: `w-14` (collapsed icon-only mode).
        - Style: `bg-[#0A0A12] border-r border-slate-800 flex flex-col items-center py-4 gap-4`.
        - Content: 5 stacked Lucide icons (`Terminal`, `Network`, `Activity`, `Database`, `Settings`). Hover states must trigger a sharp background change (`hover:bg-purple-900/30 hover:text-purple-400`).
        - The main central area. `flex-1 bg-[#05050A] p-2 relative`.
        - Content: Inside, generate a dense 2x2 grid (`grid grid-cols-2 gap-2 h-full`). Place empty, dark, bordered panels inside (`border border-slate-800 bg-[#0A0A12] rounded-sm`), featuring a tiny title bar on top of each panel (e.g., "Agent Stream", "Neural Graph").
3. **(Bottom Container):**
    - Height: `h-6`. `bg-purple-950/20 border-t border-purple-900/50 flex items-center px-2 text-[10px] text-slate-500 gap-4`.
    - Content: Simulate a live IDE terminal status bar. Include mock network latency ("Ping: 12ms"), Agent Status ("Active Nodes: 4"), and a scrolling hex code log placeholder.

GENERATE THE PRODUCTION-READY REACT COMPONENT CODE ADHERING STRICTLY TO THIS BLUEPRINT AND THE ATTACHED DESIGN.MD CONTEXT.

---
## Conclusão e Diretrizes Operacionais de Longo Prazo

A engenharia de prompts voltada para a arquitetura de interfaces geradas por IA não se baseia na fluência criativa, mas no cerco cirúrgico do espaço latente do modelo. O uso do Google Stitch como uma plataforma de Vibe Design tem o potencial de eliminar o atrito do processo de prototipagem, desde que o viés de normalidade em direção à "geleia genérica" corporativa seja enfrentado com metodologias severas de restrição.

Ao definir o design através da estrutura manifesta no `DESIGN.md` , focada em alta densidade, restrição de paletas de cor, uso de tipografia analítica e remoção absoluta de interações não essenciais, o ambiente satisfaz não apenas os requisitos operacionais de um sistema "Bare-Metal", mas atende perfeitamente à psicologia cognitiva do espectro TDAH/2e. Esta sinergia entre design centrado no hiperfoco e arquiteturas de telemetria baseadas na aviação estabelece o alicerce pelo qual as plataformas agênticas locais devem operar. O arquiteto abandona o papel de construtor de botões e adota a posição de diretor de missão, orquestrando um conjunto de restrições para manifestar interfaces funcionais, eficientes e de densidade implacável.