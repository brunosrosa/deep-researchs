---
sticker: lucide//ghost
---
# SODA // BRANDING AND PRODUCT GUIDELINES V3.0 (The Empathic Core)

## O Cânone do Souls MC: Simbiose, Soberania e Silêncio

**Versão:** 3.6.0-Cristalizada (V3)
**Status:** Documento Mestre de Implementação Bare-Metal e Constituição Cognitiva
**Axioma Central:** _"O silício é o nosso limite; a soberania é o nosso dogma; o silêncio é a nossa estética."_

## 1. MANIFESTO FILOSÓFICO: A MÁQUINA SILENCIOSA

O **Souls MC** (Mission Control) não é apenas um software; é uma **prótese de função executiva**. Ele rejeita a "Nuvem Tóxica" — sistemas extrativos que trocam privacidade por latência e transformam a atenção do usuário em uma commodity.

### O Paradigma da Desaceleração

Em um mundo saturado de notificações e _micro-interrupções_, o SODA posiciona-se como o antídoto. Sistemas comerciais operam sob o modelo de "captura de engajamento", utilizando interfaces de alto contraste e ruído sensorial para forçar a retenção do usuário. O Souls MC, em contrapartida, é desenhado para o **hiperfoco**. Nós não buscamos capturar a atenção; nós a protegemos.

- **Espelho Negro Incondicional:** O SODA opera como uma interface passiva. Ele não exige engajamento e atua como um espaço de desaceleração mental. Se o usuário não interage, o sistema desaparece visualmente, mantendo apenas a "presença" do Soul — uma âncora visual de que o sistema está funcional, mas não intrusivo.
- **Neuro-Inclusão (2e/ADHD):** Projetado para mitigar a **Carga Alostática**. O design do SODA entende que o estresse de processar interfaces mal desenhadas reduz a capacidade executiva. Cada decisão visual, do tempo de transição à paleta cromática, visa zerar o **Flow-Debt**.
- **O Gradualismo Semântico:** A interface evoluiu para uma entidade que "respira". Notificações e estados de sistema são comunicados através de transições graduais (smooth-shift) e mudanças de cor não estroboscópicas. Ao evitar mudanças abruptas, o sistema respeita a estabilidade do hiperfoco, permitindo que o usuário perceba mudanças via visão periférica, não por impacto direto.

## 2. A TRILOGIA DE EVOLUÇÃO (Naming e Arquitetura)

A nomenclatura reflete nossa jornada de desconstrução tecnológica:

1. **Genesis MC (A Forja):** O metal frio do compilador. Rust e o _Event Loop_ do Tokio. Foco em garantir latência $\mathcal{O}(1)$ na orquestração de dados locais.
2. **Soustraction MC (A Poda):** A erradicação de paradigmas "pesados" (React, Node, VDOM). É o nascimento do _Nothing System_, onde o design é definido pelo que removemos para ganhar performance.
3. **Souls MC (A Simbiose):** O ponto onde o motor se torna uma presença. O agente atua como um parceiro socrático. O Mission Control (MC) assegura que a autonomia do agente nunca sobreponha a soberania do usuário.

## 3. O "SOUL" (O AGENTE) E A FÍSICA DO CUIDADO

O Soul não é apenas um avatar; é uma presença física e semântica de _body-doubling_. Ele serve como um espelho de sua intenção operacional.

### 3.1. Identidade Visual e Semântica Ocular

O "Fantasminha Espectro" opera através de vetores de estado que comunicam prontidão sem ruído:

1. **Ghost Border (O Pulso do Daemon):** A borda do corpo pulsa em um ritmo de "respiração" (`breathing cycle`).
    - _Repouso:_ Pulso lento e sutil (`8000ms`).
    - _Atividade:_ Pulso acelerado e levemente mais opaco, sinalizando trabalho interno (ex: indexação de novos repositórios ou inferência de tensores), sem o uso de _spinners_ rodopiantes.
2. **Ocular Gradual Shift:** A mudança de cor dos olhos não é instantânea. O sistema utiliza uma interpolação linear (`transition: color 1000ms ease-in-out`) para evitar disparos de alerta que causem fadiga visual.
    - _Status Normal:_ Olhos brancos (`oklch(0.95 0 0)`).
    - _Status de Alerta:_ Mudança gradual para Âmbar ou Coral, conforme a necessidade de mediação. A transição é suave, respeitando a sua periferia visual.

### 3.2. Dicionário de Glow (Semântica Ocular)

|   |   |   |   |
|---|---|---|---|
|**Cor**|**Estado**|**Significado para o Usuário**|**Comportamento de Transição**|
|**Cyan**|Idle|Escuta passiva / Estabilidade.|`1000ms` Cross-fade suave.|
|**Purple**|Processing|Inferência ativa / Processamento de tensores.|`1200ms` Pulso de amplitude constante.|
|**Coral**|Attention|Conflito de intenção. Exijo mediação.|`220ms` (Curto) + Gradual Brilho.|
|**Emerald**|Success|Tarefa concluída / Sincronia OK.|`2400ms` Desaceleração exponencial.|

## 4. SISTEMA VISUAL E TOPOLOGIA ESPACIAL

### 4.1. Tipografia (Tríade Sagrada)

- **Impacto (H1):** Space Grotesk (Autoridade e Identidade).
- **Cognição (UI):** Lexend (Redução de fadiga para usuários 2e/ADHD).
- **Telemetria (Código):** Space Mono (Grade estruturada e inquebrável).

### 4.2. Física da Luz: Ghost Glass & Ghost Borders

- **Ghost Glass:** Superfícies opacas em `oklch(0.12 0 0)` com alfa $85\%-95\%$. Zero blur de GPU (preservação de VRAM).
- **Ghost Borders:** Linhas de $1px$ com opacidade de $4\%-6\%$. Elas criam uma hierarquia visual sutil, separando o conteúdo sem "barulho cromático".

## 5. MECÂNICA DE INTERAÇÃO E FLIPS

### 5.1. Anatomia dos Flips (Zero Layout Shift)

O SODA bane o rearranjo de janelas. O conteúdo sob o Flip **não se move**. A transição é travada em $150ms$. O Flip é uma camada suspensa que desliza sobre o eixo Z, mantendo o contexto do usuário intacto.

### 5.2. Micro-Interações Dopaminérgicas

- **The Blast Horizon:** Onda de choque circular (Purple) que cruza a tela em $150ms$ ao limpar o Inbox. É a sensação visual de "horizonte limpo".
- **Gravity Lock:** Um estalo mecânico de $40ms$ (micro-vibração visual) ao encaixar elementos no Tuning Canvas, dando feedback tátil imediato sem distração.

## 6. RESILIÊNCIA E PROTOCOLO A2A

A comunicação descentralizada (Agent-to-Agent) utiliza DIDs e túneis criptografados (`a2a-rs`), garantindo que o seu Soul seja a única entidade com acesso aos seus dados contextuais.

- **Sovereign Circles:**
    - _Família:_ Sincronia Total.
    - _Colaboração:_ Acesso restrito a artefatos.
    - _Externo:_ Apenas mensagens estruturadas JSON-RPC.

### Firewall Cognitivo

A engine em Rust implementa uma camada de isolamento estrito. Qualquer requisição de rede que não esteja assinada com chaves criptográficas previamente autorizadas pelo usuário é descartada. Isso transforma o SODA em um bunker digital, permitindo a colaboração com agentes terceiros sem sacrificar a sua soberania de dados.

## 7. TAXONOMIA DE ERROS (FRICTION AS SIGNAL)

- **Reset Atômico:** Toda falha grave permite reverter o LadybugDB para $T-1$ via transações atômicas de SQLite.
- **The Bare-Metal Wall:** Alertas de VRAM ou barramento PCIe informados via log seco, sem telas de erro "amigáveis" e enganosas.
- **Schema Breach:** Interceptação de logits via `llguidance` mascarada como um breve "shimmer" (brilho) nos olhos do Soul, mantendo a calma enquanto o erro é corrigido silenciosamente.

### NOTA FINAL:

Este documento é a Única Fonte de Verdade (SSOT). Qualquer linha de código que viole o **Zero Layout Shift** ou o **Axioma da Soberania** deve ser sumariamente amputada. O SODA não é apenas software; é a estrutura sobre a qual construímos o nosso futuro, mantendo a mente humana como o centro soberano de todo o silício.