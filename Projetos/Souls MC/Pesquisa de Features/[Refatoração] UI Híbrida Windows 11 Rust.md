---
aliases: []
---
# Interfaces de Usuário Híbridas para Desktop no Windows 11: Arquitetura de OS Overlay Transparente com Rust, egui e Svelte 5

A evolução das interfaces de usuário no ecossistema desktop atingiu um ponto de inflexão na engenharia de sistemas modernos. A busca por aplicações com estética refinada — inspirada na linguagem visual do Apple macOS com efeitos de vidro fosco (_Frosted Glass_), bordas submilimétricas de baixa opacidade e brilhos neon difusos — colide frequentemente com a necessidade de desempenho extremo e pegada mínima de recursos computacionais. Em ambientes Windows 11, a implementação de uma camada de interface em tela cheia (_Fullscreen Borderless OS Overlay_) capaz de interagir com o sistema sem bloquear os cliques do usuário (_click-through_ dinâmico) exige um controle de baixo nível sobre a API Win32, a pilha de composição do Desktop Window Manager (DWM) e a orquestração do ciclo de vida da GPU.

A combinação de Rust Puro como motor de orquestração, `egui` para renderização gráfica nativa de alta velocidade e `wry` (WebView2) para a execução de interfaces dinâmicas geradas por inteligência artificial (_GenUI/A2UI_) com Svelte 5 representa o estado da arte para arquiteturas desktop híbridas. Este relatório fornece uma análise detalhada dos mecanismos de engenharia necessários para construir esse chassi de alto desempenho, garantindo uma taxa de utilização de GPU de exatamente 0% quando o overlay estiver em estado de repouso (_idle_).

## 1. Composição de Janela e Click-Through Dinâmico (Win32 API & Native Rust)

Para criar uma interface em tela cheia que sobreponha o sistema operacional de forma imperceptível, a janela principal deve ocupar 100% da área dos monitores sem apresentar bordas de janela legadas, barras de título ou artefatos visuais de fundo. A conquista desse comportamento exige o uso direto das bibliotecas `windows` ou `windows-sys` em Rust, contornando abstrações genéricas que cobrem a complexidade do ecossistema Win32.

### Configuração de Janela Fullscreen Borderless de Baixa Latência

A criação de uma janela transparente sobreposta requer a combinação precisa dos estilos estendidos de janela (`WS_EX_STYLE`) e estilos padrão (`WS_STYLE`). As constantes essenciais incluem:

- `WS_POPUP`: Remove todos os caixilhos e decorações de janela padrão.
- `WS_EX_TOPMOST`: Garante que a janela permaneça na camada z-index mais elevada, flutuando sobre qualquer outra aplicação.
- `WS_EX_LAYERED`: Habilita a transparência por pixel e a composição avançada pelo DWM.
- `WS_EX_TOOLWINDOW`: Remove a aplicação da barra de tarefas do Windows e do alternador de tarefas Alt+Tab, agindo estritamente como um overlay.

### Mecânica de Click-Through Dinâmico via Hit-Testing e Estilos Win32

O maior desafio em overlays transparentes em tela cheia é permitir que os cliques do mouse passem livremente para as janelas subjacentes (navegadores, IDEs, jogos) nas regiões onde a interface gráfica é transparente, enquanto captura as interações nas áreas onde há elementos de UI (painéis, botões, campos de texto).

Existem duas abordagens para essa alternância dinamicamente executadas em Rust:

1. **Mutação Dinâmica de Estilos Win32 (`WS_EX_TRANSPARENT`)**: A adição da flag `WS_EX_TRANSPARENT` via `SetWindowLongPtrW` força o sistema a ignorar a janela durante o evento de _hit-testing_ do mouse, repassando a mensagem `WM_NCHITTEST` para a janela situada logo abaixo na ordem Z. Ao detectar que o ponteiro do mouse entrou nas áreas delimitadoras (_bounding boxes_) da UI do `egui` ou da `WebView2`, a aplicação Rust remove a flag `WS_EX_TRANSPARENT`, tornando a região clicável.
2. **Abstração por Hit-Test de Cursor (`set_cursor_hittest`)**: Na biblioteca `winit`, essa funcionalidade é exposta por meio do método `window.set_cursor_hittest(false)`, que aplica nativamente as modificações no `GWL_EXSTYLE` do Win32 sem a necessidade de recriar o contexto da janela.

### Aplicação Nativa dos Efeitos Mica e Acrylic do Windows 11

No Windows 11, os efeitos de translucidez evoluíram do antigo _Aero Glass_ e do _SetWindowCompositionAttribute_ (descontinuado) para a API unificada do Desktop Window Manager via `DwmSetWindowAttribute`. A constante de atributo `DWMWA_SYSTEMBACKDROP_TYPE` (ID 38) permite injetar o material de fundo nativo renderizado diretamente pela GPU do sistema operacional.

|**Constante DWM (DWM_SYSTEMBACKDROP_TYPE)**|**Valor Numérico**|**Descrição e Aplicação Técnica**|
|---|---|---|
|`DWMSBT_AUTO`|0|O DWM decide automaticamente o efeito com base no estilo da janela.|
|`DWMSBT_NONE`|1|Desativa qualquer efeito de fundo do sistema (superfície opaca padrão).|
|`DWMSBT_MAINWINDOW`|2|Aplica o efeito **Mica** padrão, sintonizado com o papel de parede do usuário.|
|`DWMSBT_TRANSIENTWINDOW`|3|Aplica o **Desktop Acrylic** (vidro fosco com desfoque gaussiano de alta intensidade).|
|`DWMSBT_TABBEDWINDOW`|4|Aplica o **Mica Alt** (variação de Mica com maior contraste para interfaces com abas).|

Para que o efeito Mica ou Acrylic seja visível através da janela criada em Rust, a superfície da janela deve ser configurada como transparente e limpa com uma cor preta nula (`RGBA(0, 0, 0, 0)`), além de estender a margem do frame para a área do cliente utilizando `DwmExtendFrameIntoClientArea` com margens de `-1`.

Rust

```
use windows::Win32::Foundation::HWND;
use windows::Win32::Graphics::Dwm::{
    DwmExtendFrameIntoClientArea, DwmSetWindowAttribute, DWMWA_SYSTEMBACKDROP_TYPE,
    DWM_SYSTEMBACKDROP_TYPE, DWMSBT_TRANSIENTWINDOW, MARGINS,
};
use windows::Win32::UI::WindowsAndMessaging::{
    GetWindowLongPtrW, SetWindowLongPtrW, GWL_EXSTYLE, WS_EX_LAYERED, WS_EX_TOPMOST,
    WS_EX_TRANSPARENT,
};

/// Configura os estilos avançados da janela Win32 para suporte a overlay e transparência DWM
pub unsafe fn setup_overlay_window(hwnd: HWND) {
    // 1. Aplicar estilos estendidos para janela flutuante e de alta camada
    let mut ex_style = GetWindowLongPtrW(hwnd, GWL_EXSTYLE) as u32;
    ex_style |= WS_EX_TOPMOST | WS_EX_LAYERED;
    SetWindowLongPtrW(hwnd, GWL_EXSTYLE, ex_style as isize);

    // 2. Estender o frame transparente para a área de cliente (Margins = -1 faz o efeito cobrir 100% da janela)
    let margins = MARGINS {
        cxLeftWidth: -1,
        cxRightWidth: -1,
        cyTopHeight: -1,
        cyBottomHeight: -1,
    };
    let _ = DwmExtendFrameIntoClientArea(hwnd, &margins);

    // 3. Aplicar Desktop Acrylic nativo do Windows 11
    let backdrop_type = DWMSBT_TRANSIENTWINDOW; // Efeito Acrylic de alta opacidade e blur
    let _ = DwmSetWindowAttribute(
        hwnd,
        DWMWA_SYSTEMBACKDROP_TYPE,
        &backdrop_type as *const _ as *const _,
        std::mem::size_of::<DWM_SYSTEMBACKDROP_TYPE>() as u32,
    );
}

/// Alterna dinamicamente a transparência a cliques da janela (Click-Through)
pub unsafe fn set_click_through(hwnd: HWND, passthrough: bool) {
    let current_style = GetWindowLongPtrW(hwnd, GWL_EXSTYLE) as u32;
    let new_style = if passthrough {
        current_style | WS_EX_TRANSPARENT
    } else {
        current_style & !WS_EX_TRANSPARENT
    };

    if current_style != new_style {
        SetWindowLongPtrW(hwnd, GWL_EXSTYLE, new_style as isize);
    }
}
```

## 2. egui para Estética Sleek (Custom Styling & Ciclo Reativo de 0% GPU)

O ecossistema `egui` oferece uma solução de UI de modo imediato (_Immediate Mode GUI_) extraordinariamente rápida e leve em Rust. No entanto, sua configuração visual padrão apresenta uma estética técnica utilitária. Para transformar o `egui` em um painel minimalista com padrão de qualidade Apple macOS, é necessário redefinir a paleta de cores da biblioteca, os cantos arredondados, os traços de bordas e carregar tipografia profissional personalizada.

### Estilização Avançada e Tipografia Premium

A chave para o visual minimalista está na aplicação de um _Design System_ baseado em princípios de física de luz:

- **Cantos Arredondados Suaves**: Raio de curvatura entre 10px e 16px para todos os painéis e popups.
- **Falsas Bordas Submilimétricas**: Traços de 1.0px de largura utilizando cores brancas com baixíssima opacidade (`RGBA(255, 255, 255, 18)` a `25`), criando um efeito de refração nas bordas sobre o fundo desfocado.
- **Glows Neon Difusos**: Renderização de sombras externas com raios de espalhamento amplos usando cores neon saturadas (como Cyan `#00E5FF` ou Magenta `#FF0055`) com transparências ajustadas para 5% a 10% de alfa.
- **Tipografia Personalizada**: Substituição total das fontes padrão pelas famílias _Inter_ (para elementos proporcionais da UI) e _JetBrains Mono_ ou _Space Grotesk_ (para elementos de código ou visualizadores de dados).

Rust

```
use egui::{
    Color32, CornerRadius, FontData, FontDefinitions, FontFamily, Shadow, Stroke, Visuals,
};
use std::sync::Arc;

/// Injeta tipografia customizada e o tema visual "Apple Premium Glass" no contexto do egui
pub fn apply_premium_glass_theme(ctx: &egui::Context) {
    let mut fonts = FontDefinitions::default();

    // Carregar arquivos de fonte dos assets compilados no binário
    fonts.font_data.insert(
        "Inter-Medium".to_string(),
        Arc::new(FontData::from_static(include_bytes!("../assets/fonts/Inter-Medium.ttf"))),
    );
    fonts.font_data.insert(
        "JetBrainsMono-Regular".to_string(),
        Arc::new(FontData::from_static(include_bytes!("../assets/fonts/JetBrainsMono-Regular.ttf"))),
    );

    // Mapear fontes para as famílias Proporcional e Monospaced
    fonts
        .families
        .get_mut(&FontFamily::Proportional)
        .unwrap()
        .insert(0, "Inter-Medium".to_string());

    fonts
        .families
        .get_mut(&FontFamily::Monospace)
        .unwrap()
        .insert(0, "JetBrainsMono-Regular".to_string());

    ctx.set_fonts(fonts);

    // Configuração dos parâmetros visuais de transparência e vidro
    let mut visuals = Visuals::dark();
    
    // Configurar transparências de fundo para integrar com o Acrylic do Win32
    visuals.window_fill = Color32::from_rgba_unmultiplied(12, 12, 16, 140);
    visuals.panel_fill = Color32::from_rgba_unmultiplied(18, 18, 22, 100);
    
    // Raio de cantos arredondados premium
    visuals.window_corner_radius = CornerRadius::same(14);
    
    // Bordas de 1px com transparência sutil de refração
    visuals.window_stroke = Stroke::new(1.0, Color32::from_rgba_unmultiplied(255, 255, 255, 22));
    visuals.widgets.noninteractive.bg_stroke = Stroke::new(1.0, Color32::from_rgba_unmultiplied(255, 255, 255, 15));

    // Neon Glow Difuso usando Sombras Customizadas (Neon Cyan com baixa opacidade)
    visuals.window_shadow = Shadow {
        offset: [0, 8],
        blur: 24,
        spread: 2,
        color: Color32::from_rgba_unmultiplied(0, 229, 255, 18),
    };

    ctx.set_visuals(visuals);
}
```

### Garantia de 0% de Uso de GPU em Idle (Reactive Repaint Loop)

Por padrão, motores de renderização gráfica baseados em jogos ou UIs de modo imediato tendem a executar em um laço infinito de repintura (_Continuous Repaint Loop_), consumindo entre 1% e 5% de GPU mesmo com a janela estática. Para atingir a meta estrita de **0% de uso de GPU em idle**, o ciclo de vida do `egui` e a fila de eventos do `winit` devem operar em modo estritamente Reativo.

1. **Configuração de Event Loop Reativo**: A repintura ocorre apenas quando um evento de entrada do sistema operacional (`WindowEvent`) for disparado (movimento do mouse, digitação, redimensionamento).
2. **Repinturas Sob Demanda via Canais Async**: Atualizações provenientes de serviços de segundo plano (como servidores MCP ou queries do SQLite) devem notificar a thread de UI invocando explicitamente `ctx.request_repaint()`.
3. **Evitar Animações Infinitas Indefinidas**: Animações contínuas (como spinners) devem ser pausadas quando a janela perder interatividade ou quando os dados terminarem de carregar.

Rust

```
use eframe::{App, Frame};
use egui::Context;
use std::sync::mpsc::Receiver;

pub struct OverlayApp {
    rx_mcp_updates: Receiver<String>,
    latest_data: String,
}

impl App for OverlayApp {
    fn update(&mut self, ctx: &Context, _frame: &mut Frame) {
        // Verificar se há novas mensagens do backend sem bloquear o loop
        if let Ok(new_data) = self.rx_mcp_updates.try_recv() {
            self.latest_data = new_data;
            // Solicita uma ÚNICA repintura da GPU para renderizar o novo estado
            ctx.request_repaint();
        }

        egui::CentralPanel::default()
            .frame(egui::Frame::none().fill(egui::Color32::TRANSPARENT))
            .show(ctx, |ui| {
                ui.heading("Overlay HUD");
                ui.label(format!("MCP Data: {}", self.latest_data));
            });

        // NOTA CRÍTICA: Não invocar `ctx.request_repaint()` no final deste método!
        // Sem essa chamada, o egui entra em repouso absoluto, zerando o uso de GPU.
    }
}
```

## 3. Embutir WebView2 para Svelte 5 com Glassmorphic UI

Embora o `egui` seja ideal para HUDs nativos de alta velocidade, a renderização de componentes dinâmicos orientados a IA (_GenUI/A2UI_) exige o ecossistema Web. Para este propósito, o motor Microsoft WebView2 embutido através da crate `wry` oferece a melhor infraestrutura existente no ecossistema Rust.

A biblioteca experimental `egui_webview` é valiosa como referência, contudo, a abordagem de produção mais estável consiste em instanciar a `wry::WebView` como uma janela filha nativa associada ao identificador HWND da janela pai gerenciada pelo Rust.

### Resolução Definitiva do Problema do "Airspace" (Z-Order) e Transparência de Fundo

O problema clássico de _Airspace_ ocorre em janelas híbridas quando o controle de renderização da WebView2 intercepta a cadeia de composição e pinta um canvas de fundo opaco (geralmente branco ou preto), sobrescrevendo o efeito de transparência Acrylic/Mica aplicado pelo Rust na janela pai Win32.

A solução técnica definitiva exige três etapas sincronizadas:

1. **Configuração Transparente na crate `wry`**: Configurar a propriedade `with_transparent(true)` na `WebViewBuilder`. Isso altera a propriedade `DefaultBackgroundColor` do `ICoreWebView2Controller2` subjacente para `RGBA(0, 0, 0, 0)`.
2. **Remoção de Sombra Undecorated no Win32**: Durante a criação da janela base via `tao` ou `winit`, deve-se temporariamente desativar a sombra nativa não decorada (`with_undecorated_shadow(false)`) para evitar artefatos de linhas pretas nas bordas arredondadas durante o redimensionamento.
3. **Estilização de Vidro via CSS e Backdrop-Filter**: O container DOM do Svelte 5 não deve declarar cores de fundo sólidas no elemento `body` ou `html`. A translucidez é controlada por CSS usando `backdrop-filter: blur()`.

Rust

```
use wry::{WebView, WebViewBuilder};
use raw_window_handle::HasWindowHandle;

pub fn init_transparent_webview<W: HasWindowHandle>(
    window: &W,
    initial_url: &str,
) -> Result<WebView, Box<dyn std::error::Error>> {
    let webview = WebViewBuilder::new()
        // Habilita a transparência do canal alpha na WebView2 (ICoreWebView2Controller2)
        .with_transparent(true)
        .with_url(initial_url)
        // Injeta o manipulador IPC nativo para comunicação de ultra-baixa latência com Rust
        .with_ipc_handler(|msg| {
            println!("Mensagem recebida do Svelte 5: {}", msg.body());
        })
        .build(window)?;

    Ok(webview)
}
```

### Svelte 5 com Runes e Layouts Responsivos em Ultrawide e Diferentes Aspect Ratios

No ecossistema Svelte 5, o sistema de reatividade foi reconstruído do zero em torno de **Runes** (`$state`, `$derived`, `$effect`, `$props`), abandonando a reatividade baseada em sinalizadores de compilação legados (`$:`) em favor de sinais explícitos universais. Essa mudança é fundamental para interfaces GenUI que recebem payloads JSON de alta frequência vindos do backend em Rust.

Para suportar variações extremas de proporção de tela (como monitores Ultrawide `21:9`, `32:9` e telas convencionais `16:9` ou `16:10`), o layout CSS deve abandonar `media queries` globais baseadas no viewport total e adotar **CSS Container Queries** (`@container`), garantindo que os painéis flutuantes do overlay se adaptem ao seu próprio espaço de contexto sem distorções horizontais.

HTML

```
<!-- OverlayPanel.svelte -->
<script lang="ts">
  // Uso estrito de Runes no Svelte 5
  let { title = "Painel de Controle", mcpStatus = "Conectado" } = $props();

  // Estado reativo universal com $state
  let isExpanded = $state(false);

  // Propriedade derivada reativa usando $derived
  let statusColor = $derived(
    mcpStatus === "Conectado" ? "#00E5FF" : "#FF0055"
  );

  function toggleExpand() {
    isExpanded = !isExpanded;
  }
</script>

<!-- Container Query Context -->
<div class="panel-container">
  <div class="glass-card" class:expanded={isExpanded}>
    <header class="glass-header">
      <div class="status-indicator" style:background-color={statusColor}></div>
      <h3 class="font-inter">{title}</h3>
      <button class="expand-btn" onclick={toggleExpand}>
        {isExpanded ? "−" : "+"}
      </button>
    </header>

    {#if isExpanded}
      <main class="glass-content">
        <p class="font-mono">Status MCP: {mcpStatus}</p>
        <div class="glow-box">
          <span class="neon-text">GenUI Active</span>
        </div>
      </main>
    {/if}
  </div>
</div>

<style>
  /* Remoção total de fundos no canvas global */
  :global(html, body) {
    margin: 0;
    padding: 0;
    background: transparent !important;
    overflow: hidden;
    user-select: none;
  }

  /* Definição do contexto de Container Query */
  .panel-container {
    container-type: inline-size;
    container-name: overlay-panel;
    width: 100%;
    max-width: 1200px;
    margin: 0 auto;
  }

  /* Efeito Apple macOS Frosted Glass com bordas de refração */
  .glass-card {
    background: rgba(18, 18, 24, 0.45);
    backdrop-filter: blur(28px) saturate(190%);
    -webkit-backdrop-filter: blur(28px) saturate(190%);
    border: 1px solid rgba(255, 255, 255, 0.09);
    border-radius: 16px;
    box-shadow: 
      0 12px 32px 0 rgba(0, 0, 0, 0.37),
      inset 0 1px 0 0 rgba(255, 255, 255, 0.12);
    transition: all 0.3s cubic-bezier(0.16, 1, 0.3, 1);
    padding: 16px;
  }

  /* Glow Neon Difuso em hover */
  .glass-card:hover {
    border-color: rgba(0, 229, 255, 0.3);
    box-shadow: 
      0 12px 40px 0 rgba(0, 0, 0, 0.5),
      0 0 20px 2px rgba(0, 229, 255, 0.15),
      inset 0 1px 0 0 rgba(255, 255, 255, 0.2);
  }

  .glass-header {
    display: flex;
    align-items: center;
    gap: 12px;
  }

  .status-indicator {
    width: 8px;
    height: 8px;
    border-radius: 50%;
    box-shadow: 0 0 8px currentColor;
  }

  .font-inter {
    font-family: 'Inter', sans-serif;
    color: rgba(255, 255, 255, 0.92);
    font-weight: 500;
  }

  .font-mono {
    font-family: 'JetBrains Mono', monospace;
    color: rgba(255, 255, 255, 0.65);
    font-size: 0.85rem;
  }

  .neon-text {
    color: #00E5FF;
    text-shadow: 0 0 10px rgba(0, 229, 255, 0.5);
  }

  /* Adaptação fluida via Container Queries para telas Ultrawide */
  @container overlay-panel (min-width: 700px) {
    .glass-card {
      padding: 24px;
    }
    .glass-header h3 {
      font-size: 1.25rem;
    }
  }
</style>
```

## 4. Evitando Dívidas Técnicas na Transição do Tauri para a Engine Híbrida em Rust

A migração de uma arquitetura legada baseada no framework Tauri para um motor customizado em **Rust Puro + egui + wry** elimina as camadas de abstração do Tauri que impõem limitações no controle de janela e aumentam o overhead do IPC. O objetivo fundamental é isolar os serviços existentes — como a camada de banco de dados SQLite e os servidores MCP (_Model Context Protocol_) — garantindo que funcionem de forma totalmente agnóstica em relação ao subsistema de renderização visual.

### Matriz Comparativa Arquitetural

|**Requisito do Sistema**|**Arquitetura Tauri Legada**|**Nova Arquitetura Híbrida (Rust Core + egui + wry)**|
|---|---|---|
|**Abstração de Janelas**|Gerenciada pelo runtime do Tauri (`tauri::Window`).|Controle direto via `winit` e chamadas nativas `windows` / Win32 API.|
|**Transparência & Click-Through**|Limitada pelas configurações do `tauri.conf.json`.|Controle estrito de `WS_EX_TRANSPARENT` e `DwmSetWindowAttribute` por pixel.|
|**Uso de GPU em Idle**|0.8% a 3% devido ao ciclo contínuo do Webview/Tauri.|**0.0% estrito** via ciclo de repintura Reativo controlado no Rust.|
|**Renderização Nativa HUD**|Exige canvas HTML/CSS ou janelas separadas.|Sub-painéis nativos via `egui` desenhados diretamente via `wgpu` / DirectX.|
|**Comunicação Backend**|`tauri::command` baseado em serialização IPC genérica.|IPC direto via `wry::WebViewBuilder::with_ipc_handler` ou memória compartilhada.|

### Estruturação do Workspace e Desacoplamento Hexagonal

Para impedir que a lógica de banco de dados e as integrações MCP fiquem acopladas às bibliotecas de interface gráfica, o projeto deve ser estruturado como um _Cargo Workspace_ composto por três crates isoladas:

- **`core_engine` (Crate de Negócio & Dados)**: Contém o pool de conexões com o **SQLite** (gerenciado por `sqlx` ou `rusqlite`) e a máquina de estados dos servidores **MCP** sobre o runtime assíncrono `tokio`. Esta crate não possui nenhuma dependência gráfica (`winit`, `egui` ou `wry`).
- **`ipc_bridge` (Crate de Protocolo)**: Define as estruturas de dados fortemente tipadas (`Serialize`/`Deserialize` via `serde`) compartilhadas entre a lógica Rust, a UI nativa `egui` e a WebView Svelte 5.
- **`ui_shell` (Crate Host de Exibição)**: Inicializa o loop de eventos `winit`, aplica as chamadas da Win32 API para o efeito Acrylic e gerencia a coexistência entre o `egui` e a `wry::WebView`.

### Fluxo de Dados Assíncrono sem Acoplamento via Tokio Channels

A comunicação entre o `core_engine` assíncrono e o `ui_shell` gráfico é realizada exclusivamente por meio de canais de transmissão de mensagens de alta velocidade (`tokio::sync::mpsc`). Quando a máquina de estados MCP processa um novo payload de inteligência artificial ou o SQLite completa uma consulta, o evento é enviado pelo canal e desperta uma repintura pontual da interface gráfica nativa através de `ctx.request_repaint()`.

Rust

```
// core_engine/src/lib.rs
use tokio::sync::mpsc;
use serde::{Serialize, Deserialize};

#[derive(Debug, Clone, Serialize, Deserialize)]
pub enum EngineEvent {
    McpDataReceived { server_id: String, payload: String },
    SqliteQueryResult { query_id: u64, rows_json: String },
}

pub struct CoreEngine {
    tx_to_ui: mpsc::UnboundedSender<EngineEvent>,
}

impl CoreEngine {
    pub fn new(tx_to_ui: mpsc::UnboundedSender<EngineEvent>) -> Self {
        Self { tx_to_ui }
    }

    pub async fn process_mcp_stream(&self, server_id: &str) {
        // Processamento assíncrono simulado
        let event = EngineEvent::McpDataReceived {
            server_id: server_id.to_string(),
            payload: r#"{"status": "ready"}"#.to_string(),
        };
        // Envio thread-safe sem dependências de frameworks GUI
        let _ = self.tx_to_ui.send(event);
    }
}
```

No módulo de exibição (`ui_shell`), os eventos consumidos do canal alimentam o contexto reativo do `egui` e acionam o envio de payloads formatados para a WebView2 utilizando `webview.evaluate_script()`. Essa divisão garante uma arquitetura limpa, com manutenibilidade de longo prazo e total independência de frameworks engessados.

## 5. Conclusões e Recomendações Técnicas

A implementação de interfaces de usuário híbridas para desktop no Windows 11 com foco no estado da arte exige o abandono de abstrações genéricas e a adoção de um modelo de engenharia de precisão:

1. **Composição de Janela**: O uso direto das APIs Win32 via crate `windows` é indispensável. A manipulação dinâmica do estilo `WS_EX_TRANSPARENT` combinada com o atributo `DWMWA_SYSTEMBACKDROP_TYPE` permite alcançar o efeito Desktop Acrylic original com controle absoluto sobre a captura de cliques (_click-through_).
2. **Desempenho da GPU**: O cumprimento do requisito de **0% de GPU em idle** é obtido ao interromper o ciclo contínuo de repintura do `egui`. O laço de atualização deve ser estritamente reativo, acionado apenas por entradas do usuário ou por mensagens provenientes dos canais dos servidores MCP e do banco SQLite.
3. **Integração Web com Svelte 5**: A eliminação do problema de _Airspace_ é garantida ao configurar a transparência da `wry::WebView` (`DefaultBackgroundColor` alpha = 0) e estender a transparência do DWM no Win32. O uso dos **Runes** no Svelte 5 e de **CSS Container Queries** assegura que componentes de GenUI escalem de maneira fluida em telas Ultrawide sem distorcer as proporções de layout.
4. **Estratégia de Arquitetura**: A migração bem-sucedida do Tauri para esta engine híbrida assenta no desacoplamento da lógica de negócio em crates Rust puras. O isolamento do SQLite e dos motores MCP em um runtime `tokio` independente garante uma transição limpa, sustentável e livre de dívida técnica.