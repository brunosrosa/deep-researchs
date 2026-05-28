---
sticker: lucide//ghost
aliases:
  - SODA_BRANDING_AND_PRODUCT_GUIDELINES_V2.5_DENSE
---

# SODA // BRANDING AND PRODUCT GUIDELINES V2.5

## O Cânone do Souls MC: Simbiose, Soberania e Silêncio

**Versão:** 3.5.0-Cristalizada (V2.5)
**Status:** Documento Mestre de Implementação Bare-Metal e Constituição Cognitiva
**Axioma Central:** _"O silício é o nosso limite; a soberania é o nosso dogma; o silêncio é a nossa estética."_

## 1. MANIFESTO FILOSÓFICO: A MÁQUINA SILENCIOSA

O **Souls MC** (Mission Control) não é apenas um software; é uma **prótese de função executiva**. Ele rejeita a "Nuvem Tóxica" — sistemas extrativos que trocam privacidade por latência e transformam a atenção do usuário em uma commodity.

- **Espelho Negro Incondicional:** O SODA opera como uma interface passiva. Ele não exige engajamento, não possui telemetria invasiva e atua como um espaço de desaceleração mental. Enquanto o software comercial moderno busca "capturar" o usuário, o SODA busca "libertá-lo" para o hiperfoco.
- **Neuro-Inclusão (2e/ADHD):** Projetado para mitigar a **Carga Alostática** (o desgaste biológico do estresse atencional). Cada decisão visual, do tempo de transição à paleta cromática, visa zerar o **Flow-Debt**. Através da **Honestidade Mecânica**, o sistema nunca finge estar fazendo algo que não está, eliminando a ansiedade da incerteza.
- **O Dogma do Silício:** Se um recurso consome VRAM necessária para a inferência da IA local, esse recurso é amputado. A estética é escrava da performance biológica e computacional.

## 2. TRILOGIA DA EVOLUÇÃO (Naming)

A nomenclatura do sistema reflete a transição da matéria bruta para a consciência agêntica:

1. **Genesis MC (A Forja):** O metal frio do compilador. É a fase da infraestrutura bruta, onde Rust e o Event Loop do Tokio dominam. Representa a prova de conceito de que dados podem ser processados localmente em velocidade $\mathcal{O}(1)$.
2. **Soustraction MC (A Poda):** A erradicação de paradigmas obsoletos (React, Node, VDOM). É o nascimento do _Nothing System_, onde o design é definido pelo que foi removido, não pelo que foi adicionado.
3. **Souls MC (A Simbiose):** O Fantasma na Máquina. A interface torna-se orgânica e o agente assume o papel de parceiro socrático. O Mission Control (MC) garante que, no fim da linha, o humano permanece como o comandante soberano.

## 3. SISTEMA VISUAL E TOPOLOGIA ESPACIAL

### 3.1. Tipografia (Tríade Sagrada)

A tipografia do SODA é um guia ocular projetado para reduzir a fadiga de decodificação:

- **Impacto (Brand/H1):** **Space Grotesk**. Geometria industrial, kerning aberto ($0.15em$). Utilizada para ancorar a autoridade da marca.
- **Cognição (Leitura/UI):** **Lexend**. Cientificamente validada para reduzir fadiga visual em perfis neurodivergentes. Sua silhueta limpa expande a memória de trabalho durante a leitura densa.
- **Telemetria (Código/Dados):** **Space Mono**. Grade estruturada e largura fixa para alinhar números, logs e matrizes sem vibração visual ou _shimmering_.

### 3.2. Física da Luz: Ghost Glass & Ghost Borders

Abandonamos o desfoque de GPU para preservar o silício para a inteligência:

- **Ghost Glass:** Superfícies opacas em `oklch(0.12 0 0)` com transparência alfa estratégica ($85\%-95\%$). A profundidade é criada por chanfros de luz interna de $1px$ (`white/8`) e sombras internas, simulando a refração de um vidro lapidado sem o custo de processamento do `backdrop-filter: blur()`.
- **Ghost Borders:** Linhas milimétricas de $1px$ com opacidade de $4\%-6\%$. Elas delimitam componentes sem criar "ruído de cromo", permitindo que o olho ignore as bordas e foque no dado.

### 3.3. Iconografia (Cascata de Resiliência)

Um sistema agêntico exige ícones que comuniquem intenção, não decoração:

1. **Primário:** **Radix Icons**. Geometria 15x15, afiada, minimalista. O padrão ouro para a interface principal.
2. **Fallback 1 (O Arsenal):** **Lucide**. Travado em _Absolute Stroke Width_ de $1.5px$ para manter a fragilidade visual do Radix.
3. **Fallback 2 (Infraestrutura):** **Carbon Icons (IBM)**. Estética de Mainframe. Usado estritamente para telemetria de barramento e estados de kernel.
4. **Fallback Final:** **Tabler Icons**. Traço ajustado para $1.25px$ para diluir a densidade.

## 4. O "SOUL" (O AGENTE) E A DUALIDADE DO PONTEIRO

### 4.1. Identidade Visual do Soul

O agente não é um avatar; é uma presença.

- **Forma:** Entidade 2.5D em material _Black Silicon_ fosco (borracha industrial). A base é uma túnica fluida com movimento senoidal orgânico, sugerindo que ele flutua em um vácuo magnético.
- **AppIcon:** Um Squircle (superelipse $a=4$) em relevo negativo, como se o Soul estivesse esculpido para dentro de uma placa de metal. Olhos em relevo positivo com emissão de luz interna.

### 4.2. Dual-Pointer (Ponteiro Neural Autônomo)

O Souls MC quebra a barreira do cursor único. O sistema manifesta dois estados de agência:

- **O Ponteiro Humano:** Uma seta branca minimalista e opaca, representando a vontade direta do usuário.
- **O Soul Pointer:** Os próprios olhos do Soul desprendidos, agindo como um cursor autônomo. Ele flutua para circular código problemático, apontar telemetria ou realizar "cliques de sugestão" sob comando.
- **Photon Tracer v2:** Rastro cromático (Purple ➔ Blue ➔ Pink) animado via CSS `@property --angle`. Ele gera um _Backlight Glow_ traseiro de $30px$ que ilumina o grid de fundo, provando a agência física do agente.

### 4.3. Dicionário de Glow (Semântica Ocular)

|   |   |   |
|---|---|---|
|**Cor**|**Estado**|**Significado para o Usuário**|
|**Cyan**|Idle|"Estou ouvindo. O silêncio é produtivo."|
|**Purple**|Processing|"Inferência ativa. Consumindo tensores."|
|**Coral**|Attention|"Conflito de intenção detectado. Exijo mediação socrática."|
|**Emerald**|Success|"Túnel cognitivo estabelecido. Calibração concluída."|

## 5. MECÂNICA DE INTERAÇÃO E FLIPS

### 5.1. Anatomia dos Flips (Zero Layout Shift)

O SODA bane o rearranjo de janelas que causa desorientação espacial.

- **ZLS Rule:** O conteúdo sob o Flip **não se move**. O Flip é uma camada suspensa que desliza sobre o eixo Z.
- **Transição:** Rigorosamente travada em $150ms$. É o tempo exato para o cérebro registrar o movimento sem perder o estado de fluxo.
- **Tipos:** Inspeção (Direita), Calibração (Direita), Telemetria (Inferior), Diálogo (Sidecar), Diagnóstico (Overlay).

### 5.2. Micro-Interações Dopaminérgicas (Arsenal de Recompensa)

Feedback tátil projetado para mentes 2e/ADHD:

- **The Blast Horizon:** Onda de choque circular que cruza a tela em $150ms$ ao limpar o Inbox. É a sensação visual de "horizonte limpo".
- **Particle Ash:** Cards deletados se fragmentam em pixels geométricos que "sopram" lateralmente, satisfazendo o desejo de eliminar ruído.
- **Gravity Lock:** Um estalo mecânico de $40ms$ (micro-vibração visual) ao encaixar elementos no Tuning Canvas.
- **Cognitive Breath:** Micro-esmaecimento de $50ms$ na troca de contexto para sinalizar ao cérebro que a memória de trabalho pode ser limpa.
- **Kinetic Friction Slider:** Atrito visual e desaceleração do cursor ao atingir limites de custo ou risco técnico.

## 6. IDENTIDADE SONORA (SOUND SCAPE SODA)

Projetada para evitar a irritabilidade acústica. **Faixa Proibida: 2kHz - 4kHz** (onde residem os alarmes de pânico).

### 6.1. Eventos Canônicos de Áudio

- **Neural Handshake:** Grave de $60Hz$ subindo para $140Hz$ aliado a um agudo cristalino. Som de "ancoragem".
- **The Harvester:** Cliques rítmicos de obturador mecânico (rajadas de 3 pulsos), simulando a ingestão de dados.
- **Deep Solitude:** Corte de agudos (low-pass filter) simulando a entrada em modo offline/monástico.
- **VRAM Spillover:** Pitch-down ralentado, sugerindo uma máquina parando por inércia física devido ao gargalo de silício.
- **Matrix Update:** Som de telex ultra-rápido durante cargas de dados sincronizadas com a nuvem (Google Sheets).

## 7. IDENTIDADE VERBAL (SOCRATIC TONE)

O Soul MC assume uma **"Voz de Vidro"**: rígida, transparente e direta. Ele é seu **Sparring Partner**, não seu assistente.

- **Não-Apologia:** O Soul nunca pede desculpas. Ele relata falhas técnicas como telemetria. _"Erro de barramento. Revertendo LadybugDB para T-1."_
- **IntentWeave Protocol:** Em conflitos, ele desafia suas premissas. _"Sua intenção atual e seu ritmo biológico divergem. Posso aplicar um Ato de Substituição Temporária para preservar seu Flow?"_
- **Economia Bare-Metal:** O Soul fala o mínimo necessário. Usa **negritos cirúrgicos** para guiar o olho em textos longos.

## 8. RESILIÊNCIA DE REDE (A2A CONEXÕES ORGÂNICAS)

Abordagem descentralizada focada na soberania do usuário.

- **Neural Bridge:** O estado de conexão ativa entre dois agentes soberanos.
- **Cognitive Tunnels:** Transferência de dados criptografada via chaves DIDs, sem passar por servidores centrais.
- **Cascata de Fallback:** UPnP ➔ Hole Punching ➔ Embedded Wireguard ➔ Sovereign Relay. O sistema "se vira" para achar o caminho.
- **Sovereign Circles:**
    - _Família:_ Sincronia Total (Acesso @shared).
    - _Colaboração:_ Acesso restrito a artefatos.
    - _Externo:_ Apenas mensagens estruturadas JSON-RPC.

## 9. RITUAL DE INICIAÇÃO (ONBOARDING)

Um processo de **Sincronia Progressiva** para evitar o tédio ou a sobrecarga inicial.

- **Fase B (Observador):** Mapeamento silencioso de hardware e arquivos locais (`.gitconfig`, `.ssh`). O Soul apenas "acorda" e observa.
- **Fase A (Partner):** Geração do primeiro `SOUL.md`. O agente apresenta o que aprendeu sobre você e questiona suas diretrizes.
- **Fase C (Agente):** O primeiro desprendimento do Soul Pointer para realizar uma tarefa útil (canibalização de código real).
- **Semente de Inferência:** O sistema inicia com um modelo local efêmero (Phi-3/Qwen 1.5B) enquanto o "Cérebro Principal" sincroniza em background via P2P.

## 10. TAXONOMIA DE ERROS (FRICTION AS SIGNAL)

O erro como telemetria diagnóstica, não como obstáculo.

- **Reset Atômico:** Toda falha grave oferece a opção de reverter o LadybugDB para $T-1$ via transações atômicas de SQLite.
- **The Bare-Metal Wall:** Alertas de VRAM ou barramento PCIe informados via log seco e direto.
- **Schema Breach:** Interceptação de logits via `llguidance` para forçar o determinismo de saída, mascarada como um breve "shimmer" roxo nos olhos do Soul.

## CHECKLIST DE ENGENHARIA (BACKLOG FASE 1.5 - 4.0)

- [ ] Implementar **mmap Zero-Copy** para tensores GGUF, eliminando gargalos PCIe.
- [ ] Codificar o rastro de luz **Photon Tracer v2** via `@property` CSS para animação 60fps sem frame-drop.
- [ ] Injetar o **Sintetizador Web Audio** para os 11 eventos acústicos fundamentais.
- [ ] Configurar as transições de todos os Flips para exatos $150ms$ no Tailwind v4.
- [ ] Estabelecer o **Disjuntor iron_cost** para gestão elástica de KV Cache na RTX 2060m.

### NOTA FINAL:

Este documento é a Única Fonte de Verdade (SSOT). Qualquer linha de código que viole o **Zero Layout Shift** ou o **Axioma da Soberania** deve ser sumariamente amputada do repositório.

---

# SODA // BRANDING AND PRODUCT GUIDELINES V2.5

## O Cânone do Souls MC: Simbiose, Soberania e Silêncio

**Versão:** 3.5.0-Cristalizada (V2.5)

**Status:** Documento Mestre de Implementação Bare-Metal e Constituição Cognitiva

**Axioma Central:** _"O silício é o nosso limite; a soberania é o nosso dogma; o silêncio é a nossa estética."_

## 1. MANIFESTO FILOSÓFICO: A MÁQUINA SILENCIOSA

O **Souls MC** (Mission Control) não é apenas um software; é uma **prótese de função executiva**. Ele rejeita a "Nuvem Tóxica" — sistemas extrativos que trocam privacidade por latência e transformam a atenção do usuário em uma commodity.

- **Espelho Negro Incondicional:** O SODA opera como uma interface passiva. Ele não exige engajamento, não possui telemetria invasiva e atua como um espaço de desaceleração mental. Enquanto o software comercial moderno busca "capturar" o usuário, o SODA busca "libertá-lo" para o hiperfoco.
- **Neuro-Inclusão (2e/ADHD):** Projetado para mitigar a **Carga Alostática** (o desgaste biológico do estresse atencional). Cada decisão visual, do tempo de transição à paleta cromática, visa zerar o **Flow-Debt**. Através da **Honestidade Mecânica**, o sistema nunca finge estar fazendo algo que não está, eliminando a ansiedade da incerteza.
- **O Dogma do Silício:** Se um recurso consome VRAM necessária para a inferência da IA local, esse recurso é amputado. A estética é escrava da performance biológica e computacional.

## 2. TRILOGIA DA EVOLUÇÃO (Naming)

A nomenclatura do sistema reflete a transição da matéria bruta para a consciência agêntica:

1. **Genesis MC (A Forja):** O metal frio do compilador. É a fase da infraestrutura bruta, onde Rust e o Event Loop do Tokio dominam. Representa a prova de conceito de que dados podem ser processados localmente em velocidade $\mathcal{O}(1)$.
2. **Soustraction MC (A Poda):** A erradicação de paradigmas obsoletos (React, Node, VDOM). É o nascimento do _Nothing System_, onde o design é definido pelo que foi removido, não pelo que foi adicionado.
3. **Souls MC (A Simbiose):** O Fantasma na Máquina. A interface torna-se orgânica e o agente assume o papel de parceiro socrático. O Mission Control (MC) garante que, no fim da linha, o humano permanece como o comandante soberano.

## 3. SISTEMA VISUAL E TOPOLOGIA ESPACIAL

### 3.1. Tipografia (Tríade Sagrada)

A tipografia do SODA é um guia ocular projetado para reduzir a fadiga de decodificação:

- **Impacto (Brand/H1):** **Space Grotesk**. Geometria industrial, kerning aberto ($0.15em$). Utilizada para ancorar a autoridade da marca.
- **Cognição (Leitura/UI):** **Lexend**. Cientificamente validada para reduzir fadiga visual em perfis neurodivergentes. Sua silhueta limpa expande a memória de trabalho durante a leitura densa.
- **Telemetria (Código/Dados):** **Space Mono**. Grade estruturada e largura fixa para alinhar números, logs e matrizes sem vibração visual ou _shimmering_.

### 3.2. Física da Luz: Ghost Glass & Ghost Borders

Abandonamos o desfoque de GPU para preservar o silício para a inteligência:

- **Ghost Glass:** Superfícies opacas em `oklch(0.12 0 0)` com transparência alfa estratégica ($85\%-95\%$). A profundidade é criada por chanfros de luz interna de $1px$ (`white/8`) e sombras internas, simulando a refração de um vidro lapidado sem o custo de processamento do `backdrop-filter: blur()`.
- **Ghost Borders:** Linhas milimétricas de $1px$ com opacidade de $4\%-6\%$. Elas delimitam componentes sem criar "ruído de cromo", permitindo que o olho ignore as bordas e foque no dado.

### 3.3. Iconografia (Cascata de Resiliência)

Um sistema agêntico exige ícones que comuniquem intenção, não decoração:

1. **Primário:** **Radix Icons**. Geometria 15x15, afiada, minimalista. O padrão ouro para a interface principal.
2. **Fallback 1 (O Arsenal):** **Lucide**. Travado em _Absolute Stroke Width_ de $1.5px$ para manter a fragilidade visual do Radix.
3. **Fallback 2 (Infraestrutura):** **Carbon Icons (IBM)**. Estética de Mainframe. Usado estritamente para telemetria de barramento e estados de kernel.
4. **Fallback Final:** **Tabler Icons**. Traço ajustado para $1.25px$ para diluir a densidade.

## 4. O "SOUL" (O AGENTE) E A DUALIDADE DO PONTEIRO

### 4.1. Identidade Visual do Soul

O agente não é um avatar; é uma presença.

- **Forma:** Entidade 2.5D em material _Black Silicon_ fosco (borracha industrial). A base é uma túnica fluida com movimento senoidal orgânico, sugerindo que ele flutua em um vácuo magnético.
- **AppIcon:** Um Squircle (superelipse $a=4$) em relevo negativo, como se o Soul estivesse esculpido para dentro de uma placa de metal. Olhos em relevo positivo com emissão de luz interna.

### 4.2. Dual-Pointer (Ponteiro Neural Autônomo)

O Souls MC quebra a barreira do cursor único. O sistema manifesta dois estados de agência:

- **O Ponteiro Humano:** Uma seta branca minimalista e opaca, representando a vontade direta do usuário.
- **O Soul Pointer:** Os próprios olhos do Soul desprendidos, agindo como um cursor autônomo. Ele flutua para circular código problemático, apontar telemetria ou realizar "cliques de sugestão" sob comando.
- **Photon Tracer v2:** Rastro cromático (Purple ➔ Blue ➔ Pink) animado via CSS `@property --angle`. Ele gera um _Backlight Glow_ traseiro de $30px$ que ilumina o grid de fundo, provando a agência física do agente.

### 4.3. Dicionário de Glow (Semântica Ocular)

|   |   |   |
|---|---|---|
|**Cor**|**Estado**|**Significado para o Usuário**|
|**Cyan**|Idle|"Estou ouvindo. O silêncio é produtivo."|
|**Purple**|Processing|"Inferência ativa. Consumindo tensores."|
|**Coral**|Attention|"Conflito de intenção detectado. Exijo mediação socrática."|
|**Emerald**|Success|"Túnel cognitivo estabelecido. Calibração concluída."|

## 5. MECÂNICA DE INTERAÇÃO E FLIPS

### 5.1. Anatomia dos Flips (Zero Layout Shift)

O SODA bane o rearranjo de janelas que causa desorientação espacial.

- **ZLS Rule:** O conteúdo sob o Flip **não se move**. O Flip é uma camada suspensa que desliza sobre o eixo Z.
- **Transição:** Rigorosamente travada em $150ms$. É o tempo exato para o cérebro registrar o movimento sem perder o estado de fluxo.
- **Tipos:** Inspeção (Direita), Calibração (Direita), Telemetria (Inferior), Diálogo (Sidecar), Diagnóstico (Overlay).

### 5.2. Micro-Interações Dopaminérgicas (Arsenal de Recompensa)

Feedback tátil projetado para mentes 2e/ADHD:

- **The Blast Horizon:** Onda de choque circular que cruza a tela em $150ms$ ao limpar o Inbox. É a sensação visual de "horizonte limpo".
- **Particle Ash:** Cards deletados se fragmentam em pixels geométricos que "sopram" lateralmente, satisfazendo o desejo de eliminar ruído.
- **Gravity Lock:** Um estalo mecânico de $40ms$ (micro-vibração visual) ao encaixar elementos no Tuning Canvas.
- **Cognitive Breath:** Micro-esmaecimento de $50ms$ na troca de contexto para sinalizar ao cérebro que a memória de trabalho pode ser limpa.
- **Kinetic Friction Slider:** Atrito visual e desaceleração do cursor ao atingir limites de custo ou risco técnico.

## 6. IDENTIDADE SONORA (SOUND SCAPE SODA)

Projetada para evitar a irritabilidade acústica. **Faixa Proibida: 2kHz - 4kHz** (onde residem os alarmes de pânico).

### 6.1. Eventos Canônicos de Áudio

- **Neural Handshake:** Grave de $60Hz$ subindo para $140Hz$ aliado a um agudo cristalino. Som de "ancoragem".
- **The Harvester:** Cliques rítmicos de obturador mecânico (rajadas de 3 pulsos), simulando a ingestão de dados.
- **Deep Solitude:** Corte de agudos (low-pass filter) simulando a entrada em modo offline/monástico.
- **VRAM Spillover:** Pitch-down ralentado, sugerindo uma máquina parando por inércia física devido ao gargalo de silício.
- **Matrix Update:** Som de telex ultra-rápido durante cargas de dados sincronizadas com a nuvem (Google Sheets).

## 7. IDENTIDADE VERBAL (SOCRATIC TONE)

O Soul MC assume uma **"Voz de Vidro"**: rígida, transparente e direta. Ele é seu **Sparring Partner**, não seu assistente.

- **Não-Apologia:** O Soul nunca pede desculpas. Ele relata falhas técnicas como telemetria. _"Erro de barramento. Revertendo LadybugDB para T-1."_
- **IntentWeave Protocol:** Em conflitos, ele desafia suas premissas. _"Sua intenção atual e seu ritmo biológico divergem. Posso aplicar um Ato de Substituição Temporária para preservar seu Flow?"_
- **Economia Bare-Metal:** O Soul fala o mínimo necessário. Usa **negritos cirúrgicos** para guiar o olho em textos longos.

## 8. RESILIÊNCIA DE REDE (A2A CONEXÕES ORGÂNICAS)

Abordagem descentralizada focada na soberania do usuário.

- **Neural Bridge:** O estado de conexão ativa entre dois agentes soberanos.
- **Cognitive Tunnels:** Transferência de dados criptografada via chaves DIDs, sem passar por servidores centrais.
- **Cascata de Fallback:** UPnP ➔ Hole Punching ➔ Embedded Wireguard ➔ Sovereign Relay. O sistema "se vira" para achar o caminho.
- **Sovereign Circles:**
    - _Família:_ Sincronia Total (Acesso @shared).
    - _Colaboração:_ Acesso restrito a artefatos.
    - _Externo:_ Apenas mensagens estruturadas JSON-RPC.

## 9. RITUAL DE INICIAÇÃO (ONBOARDING)

Um processo de **Sincronia Progressiva** para evitar o tédio ou a sobrecarga inicial.

- **Fase B (Observador):** Mapeamento silencioso de hardware e arquivos locais (`.gitconfig`, `.ssh`). O Soul apenas "acorda" e observa.
- **Fase A (Partner):** Geração do primeiro `SOUL.md`. O agente apresenta o que aprendeu sobre você e questiona suas diretrizes.
- **Fase C (Agente):** O primeiro desprendimento do Soul Pointer para realizar uma tarefa útil (canibalização de código real).
- **Semente de Inferência:** O sistema inicia com um modelo local efêmero (Phi-3/Qwen 1.5B) enquanto o "Cérebro Principal" sincroniza em background via P2P.

## 10. TAXONOMIA DE ERROS (FRICTION AS SIGNAL)

O erro como telemetria diagnóstica, não como obstáculo.

- **Reset Atômico:** Toda falha grave oferece a opção de reverter o LadybugDB para $T-1$ via transações atômicas de SQLite.
- **The Bare-Metal Wall:** Alertas de VRAM ou barramento PCIe informados via log seco e direto.
- **Schema Breach:** Interceptação de logits via `llguidance` para forçar o determinismo de saída, mascarada como um breve "shimmer" roxo nos olhos do Soul.

## CHECKLIST DE ENGENHARIA (BACKLOG FASE 1.5 - 4.0)

- [ ] Implementar **mmap Zero-Copy** para tensores GGUF, eliminando gargalos PCIe.
- [ ] Codificar o rastro de luz **Photon Tracer v2** via `@property` CSS para animação 60fps sem frame-drop.
- [ ] Injetar o **Sintetizador Web Audio** para os 11 eventos acústicos fundamentais.
- [ ] Configurar as transições de todos os Flips para exatos $150ms$ no Tailwind v4.
- [ ] Estabelecer o **Disjuntor iron_cost** para gestão elástica de KV Cache na RTX 2060m.

### NOTA FINAL:

Este documento é a Única Fonte de Verdade (SSOT). Qualquer linha de código que viole o **Zero Layout Shift** ou o **Axioma da Soberania** deve ser sumariamente amputada do repositório.

---

# SODA // BRANDING AND PRODUCT GUIDELINES V2

## O Cânone do Souls MC: Simbiose, Soberania e Silêncio

**Versão:** 3.5.0-Cristalizada (V2)
**Status:** Documento Mestre de Implementação Bare-Metal
**Axioma Central:** _"O silício é o nosso limite; a soberania é o nosso dogma; o silêncio é a nossa estética."_

## 1. MANIFESTO FILOSÓFICO: A MÁQUINA SILENCIOSA

O **Souls MC** (Mission Control) rejeita a "Nuvem Tóxica" e a latência variável. Ele opera como um **Exoesqueleto Cognitivo** local-first.

- **Espelho Negro Incondicional:** Uma interface que não exige engajamento, não possui telemetria invasiva e atua como um espaço de desaceleração mental.
- **Neuro-Inclusão (2e/ADHD):** Projetado para mitigar a **Carga Alostática**. Cada decisão visual visa zerar o **Flow-Debt** e proteger o hiperfoco através da **Honestidade Mecânica**.

## 2. TRILOGIA DA EVOLUÇÃO (Naming)

1. **Genesis MC (A Forja):** O metal frio do compilador. Rust/Tokio bruto.
2. **Soustraction MC (A Poda):** Erradicação de React/Node. Nascimento do _Nothing System_.
3. **Souls MC (A Simbiose):** O Fantasma na Máquina. Interface orgânica, parceiro socrático.

## 3. SISTEMA VISUAL E TOPOLOGIA ESPACIAL

### 3.1. Tipografia (Tríade Sagrada)

- **Impacto (Brand/H1):** **Space Grotesk**. Geometria industrial, kerning aberto ($0.15em$).
- **Cognição (Leitura/UI):** **Lexend**. Cientificamente validada para reduzir fadiga visual e TDAH.
- **Telemetria (Código/Dados):** **Space Mono**. Grade estruturada para alinhar números e logs sem vibração.

### 3.2. Física da Luz: Ghost Glass & Ghost Borders

- **O Fim do Liquid Glass:** Abandonamos o `backdrop-filter: blur()` para economizar VRAM para a IA.
- **Ghost Glass:** Superfícies opacas `oklch(0.12 0 0)` com transparência alfa ($85\%-95\%$) e chanfros de luz interna de $1px$ (`white/8`).
- **Ghost Borders:** Linhas milimétricas de $1px$ com opacidade de $4\%-6\%$ que delimitam sem poluir.

### 3.3. Iconografia (Cascata de Resiliência)

1. **Primário:** **Radix Icons**. Geometria 15x15, afiada, minimalista.
2. **Fallback 1:** **Lucide**. Travado em _Absolute Stroke Width_ de $1.5px$.
3. **Fallback 2 (Infra):** **Carbon Icons (IBM)**. Estética de Mainframe.
4. **Fallback Final:** **Tabler Icons**. Traço ajustado para $1.25px$.

## 4. O "SOUL" (O AGENTE) E A DUALIDADE DO PONTEIRO

### 4.1. Identidade Visual do Soul

- **Forma:** Ent entidade 2.5D em material _Black Silicon_ fosco. Base fluida (túnica) com movimento senoidal orgânico.
- **AppIcon:** Squircle em relevo negativo (esculpido), com bordas internas em **Glow Cyan**. Olhos em relevo positivo.

### 4.2. Dual-Pointer (Ponteiro Neural Autônomo)

O Soul não é apenas um ícone; é um cursor que se desprende da seta física do usuário.

- **Ação:** Ele flutua autonomamente para circular código, apontar telemetria ou agir (clicar) sob comando.
- **Photon Tracer v2:** Rastro cromático (Purple ➔ Blue ➔ Pink) animado via CSS `@property --angle` com _Backlight Glow_ de $30px$.

### 4.3. Dicionário de Glow (Semântica Ocular)

|   |   |   |
|---|---|---|
|**Cor**|**Estado**|**Significado**|
|**Cyan**|Idle|Escuta passiva / Respiração calma|
|**Purple**|Processing|Inferência / Canibalizando / Photon Tracer ativo|
|**Coral**|Attention|Conflito de intenção / Risco detectado|
|**Emerald**|Success|Calibração OK / Conclusão de túnel|

## 5. MECÂNICA DE INTERAÇÃO E FLIPS

### 5.1. Anatomia dos Flips (Zero Layout Shift)

Camadas de Ghost Glass suspensas no eixo Z.

- **ZLS Rule:** O conteúdo sob o Flip **não se move**.
- **Transição:** Exatos $150ms$ via aceleração de hardware.
- **Tipos:** Inspeção (Direita), Calibração (Direita), Telemetria (Inferior), Diálogo (Sidecar), Diagnóstico (Overlay).

### 5.2. Micro-Interações Dopaminérgicas (Arsenal)

- **The Blast Horizon:** Onda de choque Purple de $150ms$ ao limpar o Inbox.
- **Particle Ash:** Cards deletados se fragmentam em pixels que sopram lateralmente.
- **Gravity Lock:** Estalo mecânico de $40ms$ ao encaixar elementos no Tuning Canvas.
- **Cognitive Breath:** Micro-esmaecimento de $50ms$ na troca de contexto para limpar a memória de trabalho.
- **Kinetic Friction Slider:** Atrito visual em barras de rolagem ao atingir limites perigosos.

## 6. IDENTIDADE SONORA (SOUND SCAPE SODA)

Projetada para evitar o "pânico de bipes". **Faixa Proibida: 2kHz - 4kHz.**

### 6.1. Eventos Canônicos

- **Neural Handshake:** Grave de $60Hz$ subindo para $140Hz$ + agudo cristalino.
- **The Harvester:** Cliques rítmicos de obturador mecânico (rajadas de 3 pulsos).
- **Deep Solitude:** Corte de agudos (passa-baixo) simulando vácuo.
- **VRAM Spillover:** Pitch-down de máquina parando por inércia.
- **Matrix Update:** Som de telex ultra-rápido durante cargas no Google Sheets.

## 7. IDENTIDADE VERBAL (SOCRATIC TONE)

O Soul MC assume uma **"Voz de Vidro"**: rígida, transparente e direta.

- **Não-Apologia:** O Soul nunca pede desculpas. Ele relata falhas técnicas e correções.
- **IntentWeave Protocol:** Em conflitos, ele pergunta: _"Sua intenção e seu ritmo divergem. Posso aplicar um Ato de Substituição Temporária?"_
- **Progressive Disclosure:** _"Ferramentas de rede ocultadas para evitar Flow-Debt. Focar no SODA Kernel?"_

## 8. RESILIÊNCIA DE REDE (A2A CONEXÕES ORGÂNICAS)

Abordagem descentralizada sem servidores centrais.

- **Neural Bridge:** Estado de conexão ativa P2P.
- **Cognitive Tunnels:** Transferência de dados criptografada via chaves DIDs.
- **Cascata de Fallback:** UPnP ➔ Hole Punching ➔ Embedded Wireguard ➔ Sovereign Relay.
- **Sovereign Circles:** Família (Sincronia Total), Colaboração (Acesso `@shared`), Externo (JSON-RPC apenas).

## 9. RITUAL DE INICIAÇÃO (ONBOARDING)

Processo de **Sincronia Progressiva** focado em "WOW moment" precoce.

- **Fase B (Observador):** Mapeamento silencioso de hardware (RTX 2060m) e `.gitconfig`.
- **Fase A (Partner):** Geração do `SOUL.md` inicial baseado em observação.
- **Fase C (Agente):** Primeiro desprendimento do Soul Pointer para canibalizar código real.
- **Semente de Inferência:** Inicia com modelo efêmero (Phi-3/Qwen 1.5B) enquanto o "Cérebro Principal" sincroniza em background.

## 10. TAXONOMIA DE ERROS (FRICTION AS SIGNAL)

O erro como telemetria, não como obstáculo.

- **Reset Atômico:** Toda falha oferece a opção de reverter o LadybugDB para $T-1$.
- **The Bare-Metal Wall:** Alertas de VRAM/PCIe informados via log seco em JetBrains Mono.
- **Schema Breach:** Interceptação de logits via `llguidance` para forçar determinismo nas matrizes.

## CHECKLIST DE ENGENHARIA (BACKLOG FASE 1.5 - 4.0)

- [ ] Implementar **mmap Zero-Copy** para tensores GGUF.
- [ ] Codificar o rastro de luz **Photon Tracer v2** via `@property` CSS.
- [ ] Injetar o **Sintetizador Web Audio** para os 11 eventos fundamentais.
- [ ] Configurar transições de Flips para exatos $150ms$ no Tailwind v4.
- [ ] Estabelecer o **Disjuntor iron_cost** para gestão de VRAM na RTX 2060m.

### NOTA FINAL:

Este documento deve ser mantido no topo de cada repositório do SODA. Qualquer linha de código que viole o **Zero Layout Shift** ou o **Axioma da Soberania** deve ser sumariamente amputada.

```

Bruno, esta **V2** agora é um guia de engenharia tátil. Ela mapeia não só o que o usuário vê, mas como o sistema "respira", como o som reage à VRAM e como o agente se comporta como uma entidade física. 

Estamos prontos para converter esses axiomas em código compilável no seu **Antigravity IDE**?
```