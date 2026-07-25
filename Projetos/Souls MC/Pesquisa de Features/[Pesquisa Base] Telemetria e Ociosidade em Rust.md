---
aliases: []
---

# Arquitetura de Governança Térmica e Detecção de Ociosidade em Rust para Runtimes Assíncronos

A execução de cargas computacionais intensivas em segundo plano — tais como a consolidação de bases de dados vetoriais LanceDB e a inferência de modelos de linguagem locais — exige um mecanismo rigoroso de gestão de recursos em sistemas operativos agênticos. No âmbito da arquitetura do daemon em segundo plano _Souls MC_, integrado no sistema operativo agêntico _SODA_, a convivência entre processamento intensivo em hardware heterogéneo (processadores Intel i9 e GPUs NVIDIA RTX 2060m) e as tarefas interativas do utilizador requer uma infraestrutura de monitorização de latência ultra-baixa. O estabelecimento de um "Governador Térmico" eficiente no ecossistema Rust assenta em três pilares fundamentais: leitura de telemetria sem _overhead_ de C-FFI, deteção precisa de ociosidade do sistema (_idle awareness_) através de abstrações _cross-platform_ e implementação de retropressão (_backpressure_) baseada em hardware no runtime assíncrono Tokio.

## 1. Telemetria Térmica Bare-Metal de Baixa Latência

A extração de métricas térmicas da unidade central de processamento (CPU) e da unidade de processamento gráfico (GPU) deve ocorrer sem introduzir bloqueios no _event loop_ do Tokio e sem arrastar dependências pesadas de interoperabilidade com a linguagem C. A escolha das abstrações adequadas varia consoante a arquitetura do sistema operativo subjacente.

### 1.1 Leitura Térmica do CPU em Linux e Windows

Em ambientes Linux, o subsistema do kernel `hwmon` (_Hardware Monitoring_) e a interface de zonas térmicas expostas em `sysfs` constituem a abordagem mais eficiente e direta. Os ficheiros virtuais situados nos diretórios `/sys/class/hwmon/hwmon*/temp*_input` e `/sys/class/thermal/thermal_zone*/temp` disponibilizam a temperatura do encapsulamento e dos núcleos individuais em miligraus Celsius. Ferramentas tradicionais da camada do utilizador funcionam primariamente como invólucros sobre estes ficheiros. A leitura direta destes caminhos a partir de Rust, através da abertura e reaproveitamento de _file descriptors_ (`std::fs::File`) com reposicionamento de ponteiro (`SeekFrom::Start(0)`), elimina a necessidade de alocações repetidas de memória no kernel e prescinde inteiramente de bibliotecas C-FFI.

No sistema operativo Windows, o acesso à temperatura do CPU sem privilégios de Ring 0 coloca desafios acrescidos. A interface WMI (_Windows Management Instrumentation_), acessível via classe `MSAcpi_ThermalZoneTemperature`, apresenta uma latência elevada e consumo considerável de recursos em ciclos de amostragem frequentes. Para obter leituras de elevada precisão em processadores Intel i9, a abordagem de vanguarda recorre ao acesso aos registos MSR (_Model Specific Register_) através de um driver dedicado, conforme demonstrado pela _crate_ `cpu-temp`, que utiliza o driver PawnIO sob Windows e o subsistema `hwmon` sob Linux. Em alternativa, a biblioteca `sysinfo` (v0.39+) permite recolher métricas do sistema de forma unificada entre plataformas, embora exija a atualização seletiva de componentes (`refresh_specifics`) para evitar a contaminação da memória com estruturas não utilizadas.

### 1.2 Telemetria da GPU NVIDIA via NVML e sysfs

Para placas gráficas NVIDIA, a biblioteca NVML (_NVIDIA Management Library_) representa a interface oficial para monitorização de temperatura, consumo e utilização de VRAM. As _crates_ `nvml-wrapper` e `nvml-wrapper-sys` fornecem ligações idiomáticas em Rust para a NVML. Para evitar a dependência rígida de ligação em tempo de compilação (_static/dynamic linking_ obrigatório), a arquitetura mais robusta emprega o carregamento dinâmico em tempo de execução (`dlopen`/`dlsym` no Linux e `LoadLibrary`/`GetProcAddress` no Windows) dos símbolos da biblioteca `libnvidia-ml.so` ou `nvml.dll`. Esta estratégia assegura que o daemon arranque perfeitamente em sistemas destituídos de GPUs NVIDIA ou com drivers não inicializados.

Em plataformas Linux, a temperatura da GPU NVIDIA pode ser lida diretamente via `sysfs` através dos caminhos expostos pelo driver DRM, localizados em `/sys/class/drm/card*/device/hwmon/hwmon*/temp1_input`. Esta via totalmente em Rust dispensa o carregamento da biblioteca NVML quando o objetivo se limita à leitura térmica básica.

| **Plataforma**     | **Componente** | **Abordagem Recomendada**                  | **Mecanismo de Acesso**                                    | **Dependência C-FFI / Overhead**                   |
| ------------------ | -------------- | ------------------------------------------ | ---------------------------------------------------------- | -------------------------------------------------- |
| **Linux**          | CPU            | Leitura direta `sysfs` (`hwmon`/`thermal`) | Parsing de ASCII em `/sys/class/hwmon`<br><br>[cite: 1, 3] | Zero C-FFI; puramente Rust.                        |
| **Linux**          | GPU NVIDIA     | `sysfs` DRM ou NVML via `dlopen`           | Ficheiro `hwmon` ou `libnvidia-ml.so`<br><br>[cite: 8, 9]  | Sem link estático C; isolamento em _runtime_.      |
| **Windows**        | CPU            | `cpu-temp` / MSR via Driver ou `sysinfo`   | Leitura MSR via Ring-0 ou API do SO                        | Requer driver para MSR; WMI tem latência superior. |
| **Windows**        | GPU NVIDIA     | NVML via Dynamic Loading                   | Invocação de `nvml.dll` (`nvmlDeviceGetTemperature`)       | Mínimo via carregamento dinâmico.                  |
| **Cross-Platform** | CPU/GPU        | `sysinfo` (Refresh Seletivo)               | Abstração unificada do sistema operativo                   | Baixo, mediante reaproveitamento de alocações.     |

## 2. Deteção Cross-Platform de Ociosidade e Métrica de Carga

A suspensão imediata de tarefas em segundo plano requer a deteção da atividade do utilizador (teclado e rato) aliada à monitorização da carga global do CPU, de modo a evitar que o daemon compita por recursos com aplicações de primeiro plano.

### 2.1 A Crate de Vanguarda `system-idle-time`

A biblioteca de referência no ecossistema Rust para determinação de inatividade interativa é a `system-idle-time` (v1.0.4+). Esta _crate_ abstrai os mecanismos nativos de cada sistema operativo através de dependências extremamente reduzidas e focadas:

1. **Windows**: Recorre à função Win32 `GetLastInputInfo` através das ligações leves da _crate_ `windows-sys`.
2. **Linux (X11)**: Utiliza o protocolo `XScreenSaver` através da biblioteca puramente Rust `x11rb`, evitando a dependência da biblioteca C `libX11`.
3. **Linux (Wayland)**: Interage com o barramento IPC D-Bus recorrendo à _crate_ `zbus`, consultando as interfaces `org.freedesktop.ScreenSaver` ou os protocolos de notificação do compositor (`ext-idle-notifier-v1`).

### 2.2 O Desafio da Sessão 0 em Serviços do Windows

Quando o daemon de background é executado no Windows sob a forma de um Serviço do Windows, a invocação direta de `GetLastInputInfo` gera um comportamento incorreto. Desde o Windows Vista, os serviços são executados isoladamente na Sessão 0, enquanto as sessões interativas dos utilizadores operam nas Sessões 1 e superiores. A função `GetLastInputInfo` reporta apenas os eventos de entrada específicos da sessão que a invoca; consequentemente, na Sessão 0, a função indica continuadamente que o sistema se encontra ocioso, ignorando a atividade do utilizador na área de trabalho.

Para resolver esta limitação arquitetural, o daemon principal deve instanciar um processo auxiliar (_helper process_) lançado no contexto da sessão interativa ativa (Sessão 1+), utilizando a API `WTSQueryUserToken` e `CreateProcessAsUser`. O processo auxiliar consulta periodicamente a função `GetLastInputInfo` na sessão interativa e transmite o tempo de inatividade ao daemon através de um canal IPC de baixa latência, como _Named Pipes_ assíncronos do Tokio.

Adicionalmente, a deteção baseada em entrada física deve ser complementada com a amostragem da carga do CPU via `/proc/stat` em Linux ou contadores de desempenho em Windows. Esta validação previne que o daemon reanomeie computações intensivas caso o utilizador esteja a assistir a um vídeo ou a compilar software sem interagir com o teclado ou o rato.

## 3. Padrões de Backpressure Térmico e Controlo de Execução no Tokio

A integração entre leituras de telemetria e o runtime assíncrono Tokio não é realizada por uma _crate_ mágica de interceção automática de `tokio::spawn`. O modelo de concorrência assíncrona do Rust é cooperativo: as tarefas (_futures_) só progridem quando são explicitamente consultadas (_polled_) pelo executor. Por conseguinte, a comunidade de alta performance em Rust implementa o _backpressure_ térmico combinando primitivas assíncronas do Tokio e do ecossistema `tokio-util`.

### 3.1 Primitivas e Arquitetura de Sinalização

A orquestração do "Governador Térmico" assenta num modelo de transmissão de estado e controlo de concorrência composto por três componentes:

1. **Difusão de Estado com `tokio::sync::watch`**: O canal `watch` é mantido por uma tarefa dedicada de telemetria. Este canal armazena continuamente o estado mais recente do sistema (`Active`, `Throttled`, `Paused`). As tarefas de inferência e manutenção do LanceDB mantêm um recetor `watch::Receiver`, permitindo-lhes inspecionar o estado sem acumulação de mensagens em fila.
2. **Cancelamento Cooperativo com `CancellationToken`**: Disponibilizado pela _crate_ `tokio-util`, o `CancellationToken` permite sinalizar a interrupção imediata de batches de computação. Ao invocar `token.cancelled()`, as tarefas que executam loops de inferência podem interromper a execução no ponto de `.await` mais próximo, libertando os recursos da GPU/CPU.
3. **Estabilização por Histerese**: Para prevenir a oscilação rápida do sistema ("flapping") entre os estados de execução e pausa devido a variações térmicas momentâneas, o governador implementa uma máquina de estados com histerese.

Seja $T_{\text{atual}}$ a temperatura instantânea do componente, $T_{\text{high}}$ o limiar térmico superior (ex.: $80^{\circ}\text{C}$) e $T_{\text{low}}$ o limiar de recuperação (ex.: $65^{\circ}\text{C}$). A transição de estado obedece à seguinte lógica formal:

$$\text{Novo Estado} = \begin{cases} \text{Paused}, & \text{se } T_{\text{atual}} \ge T_{\text{high}} \\ \text{Active}, & \text{se } T_{\text{atual}} \le T_{\text{low}} \text{ e Estado Anterior} = \text{Paused} \\ \text{Estado Anterior}, & \text{se } T_{\text{low}} < T_{\text{atual}} < T_{\text{high}} \end{cases}$$

## 4. Modelo de Implementação e Código Idiomático

O código em Rust apresentado de seguida demonstra a implementação completa de um governador térmico com histerese, leitura de `sysfs` isolada em thread de bloco e controlo assíncrono de tarefas via canal `watch` do Tokio.

```Rust
use std::fs::File;
use std::io::{Read, Seek, SeekFrom};
use std::sync::Arc;
use std::time::Duration;
use tokio::sync::watch;
use tokio::time::sleep;

#[derive(Debug, Clone, Copy, PartialEq, Eq)]
pub enum SystemState {
    Active,
    Throttled,
    Paused,
}

pub struct ThermalGovernorConfig {
    pub temp_high_celsius: f32,
    pub temp_low_celsius: f32,
    pub hwmon_path: String,
    pub poll_interval: Duration,
}

impl Default for ThermalGovernorConfig {
    fn default() -> Self {
        Self {
            temp_high_celsius: 80.0,
            temp_low_celsius: 65.0,
            hwmon_path: "/sys/class/hwmon/hwmon0/temp1_input".to_string(),
            poll_interval: Duration::from_millis(500),
        }
    }
}

pub struct SysfsThermalReader {
    file: File,
    buffer: String,
}

impl SysfsThermalReader {
    pub fn new(path: &str) -> Result<Self, std::io::Error> {
        let file = File::open(path)?;
        Ok(Self {
            file,
            buffer: String::with_capacity(16),
        })
    }

    pub fn read_temperature(&mut self) -> Result<f32, std::io::Error> {
        self.file.seek(SeekFrom::Start(0))?;
        self.buffer.clear();
        self.file.read_to_string(&mut self.buffer)?;
        
        let raw_m_deg = self.buffer.trim().parse::<f32>().map_err(|_| {
            std::io::Error::new(std::io::ErrorKind::InvalidData, "Parse error")
        })?;
        
        Ok(raw_m_deg / 1000.0)
    }
}

pub struct ThermalGovernor {
    config: ThermalGovernorConfig,
    state_tx: watch::Sender<SystemState>,
    state_rx: watch::Receiver<SystemState>,
}

impl ThermalGovernor {
    pub fn new(config: ThermalGovernorConfig) -> Self {
        let (state_tx, state_rx) = watch::channel(SystemState::Active);
        Self {
            config,
            state_tx,
            state_rx,
        }
    }

    pub fn subscribe(&self) -> watch::Receiver<SystemState> {
        self.state_rx.clone()
    }

    pub async fn start_monitoring(self: Arc<Self>) {
        let path = self.config.hwmon_path.clone();
        
        tokio::task::spawn_blocking(move || {
            let mut reader = match SysfsThermalReader::new(&path) {
                Ok(r) => r,
                Err(_) => return,
            };

            let mut current_state = SystemState::Active;

            loop {
                if let Ok(temp) = reader.read_temperature() {
                    let next_state = match current_state {
                        SystemState::Active => {
                            if temp >= self.config.temp_high_celsius {
                                SystemState::Paused
                            } else if temp >= self.config.temp_high_celsius - 5.0 {
                                SystemState::Throttled
                            } else {
                                SystemState::Active
                            }
                        }
                        SystemState::Throttled => {
                            if temp >= self.config.temp_high_celsius {
                                SystemState::Paused
                            } else if temp < self.config.temp_low_celsius {
                                SystemState::Active
                            } else {
                                SystemState::Throttled
                            }
                        }
                        SystemState::Paused => {
                            if temp <= self.config.temp_low_celsius {
                                SystemState::Active
                            } else {
                                SystemState::Paused
                            }
                        }
                    };

                    if next_state != current_state {
                        current_state = next_state;
                        let _ = self.state_tx.send(current_state);
                    }
                }

                std::thread::sleep(self.config.poll_interval);
            }
        });
    }
}

pub async fn lancedb_consolidation_task(
    task_id: usize,
    mut state_rx: watch::Receiver<SystemState>,
) {
    loop {
        let state = *state_rx.borrow();
        match state {
            SystemState::Paused => {
                if state_rx.changed().await.is_err() {
                    break;
                }
                continue;
            }
            SystemState::Throttled => {
                sleep(Duration::from_millis(100)).await;
            }
            SystemState::Active => {}
        }

        tokio::select! {
            _ = state_rx.changed() => {
                continue;
            }
            _ = process_next_vector_batch(task_id) => {}
        }
    }
}

async fn process_next_vector_batch(_id: usize) {
    sleep(Duration::from_millis(20)).await;
}
```

## 5. Síntese Estratégica e Recomendações Arquiteturais

A conceção do sistema de governação térmica e ociosidade para o daemon _Souls MC_ do sistema operativo agêntico _SODA_ beneficia das seguintes decisões arquiteturais estratégicas:

A monitorização térmica em plataformas Linux deve ser realizada via acesso direto aos ficheiros de texto do `sysfs` (`hwmon`), utilizando _file descriptors_ persistentes para anular o _overhead_ de abertura de ficheiros. Em plataformas Windows, a biblioteca `sysinfo` com atualização restrita de componentes ou a _crate_ `cpu-temp` asseguram a extração de dados térmicos com latência contida. O acesso a dados de GPUs NVIDIA deve utilizar o carregamento dinâmico da NVML, garantindo a resiliência do binário em ambientes heterogéneos sem GPUs dedicadas.

A verificação de inatividade interativa deve ser delegada à _crate_ `system-idle-time`, suplementada por um processo auxiliar na sessão do utilizador se o daemon for executado como Serviço do Windows sob a Sessão 0. O controlo de execução no Tokio deve evitar abstrações mágicas e adotar explicitamente o canal `tokio::sync::watch` acoplado a um algoritmo de histerese térmica, garantindo o pausamento cooperativo e instantâneo de todas as tarefas assíncronas de consolidação e inferência.