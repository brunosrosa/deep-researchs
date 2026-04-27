---
sticker: lucide//cloud-lightning
---
# Arquitetura do Gerenciador de Modelos Locais para Sistemas Agênticos Bare-Metal

## Introdução e Fundamentação Arquitetural do Sistema SODA

A transição da inferência de Modelos de Linguagem de Grande Escala (LLMs) de infraestruturas de data centers centralizados para ambientes de borda (edge) e hardwares com restrições severas representa um dos desafios mais complexos da engenharia de sistemas contemporânea. No contexto do desenvolvimento do Sistema Operacional Agêntico (SODA), a execução de inteligência artificial em um ambiente bare-metal dotado de um processador Intel i9, 32 GB de RAM e, fundamentalmente, uma placa de vídeo RTX 2060m com um limite estrito de 6 GB de VRAM, impõe exigências arquiteturais rigorosas. O orquestrador interno, operando sob a tutela do roteador cognitivo ParetoBandit, deve ser capaz de gerenciar o ciclo de vida de artefatos pesados de rede neural, predominantemente nos formatos GGUF e Safetensors, de forma autônoma, assíncrona e absolutamente invisível (headless).

A premissa de construir um "Local Model Manager" nativo em Rust fundamenta-se na necessidade de prescindir de daemons e serviços de terceiros, como o Ollama ou o LM Studio. Embora essas ferramentas sejam robustas para o usuário final, a introdução de processos externos em um sistema operacional agêntico introduz latência de Comunicação Inter-Processos (IPC), sobrecarga de memória não controlada e vulnerabilidades de segurança inerentes a servidores locais escutando em portas de rede. A construção de uma arquitetura interna embutida no binário do SODA, alavancando o runtime assíncrono Tokio, garante que o controle de concorrência, o gerenciamento térmico, a alocação de VRAM e a retenção de disco sejam governados por um único ciclo de vida de aplicação, otimizando cada ciclo de clock e cada byte de memória.

Este relatório técnico exaustivo mapeia o estado da arte da engenharia de sistemas em Rust (focado no horizonte 2025/2026) para a orquestração de IA local. A investigação e a síntese arquitetural estão estruturadas em três eixos críticos e interdependentes. O primeiro eixo disseca a integração nativa com repositórios de modelos via HTTP para downloads resilientes e verificação de integridade criptográfica sem bloqueio do event-loop. O segundo eixo aprofunda-se na heurística de auto-profiling de hardware, formulando o cálculo matemático de alocação de VRAM e a seleção determinística de níveis de quantização (com ênfase na dicotomia entre Q4_K_M e Q8_0) para micro-modelos especializados. O terceiro e último eixo projeta as políticas de gerenciamento de disco baseadas em Least Recently Used (LRU) para a prevenção de exaustão de armazenamento de estado sólido (SSD), utilizando padrões de concorrência avançados.

As soluções delineadas neste documento privilegiam crates nativos do ecossistema Rust e observam estritamente o princípio de Pareto (regra 90/10), visando maximizar a resiliência e o desempenho da inferência com a menor complexidade de código possível. O objetivo final é garantir que o roteador ParetoBandit possa transitar fluidamente entre APIs de nuvem e inferência bare-metal, coordenando modelos como Qwen 3B para geração de código, FunctionGemma para roteamento lógico e Kokoro-82M para síntese de voz, sem interrupções operacionais.

## Eixo 1: Integração Assíncrona e Aquisição de Modelos em Rust

A aquisição de modelos de inteligência artificial, cujos tamanhos variam de algumas centenas de megabytes (no caso de micro-modelos e vocabulários) a dezenas de gigabytes (para LLMs densos), requer um subsistema de rede altamente resiliente e otimizado. Em um sistema operacional agêntico que opera silenciosamente em background, oscilações de conectividade ou interrupções de rede não podem, sob nenhuma circunstância, resultar em arquivos corrompidos ou na necessidade computacionalmente dispendiosa de reiniciar downloads massivos a partir do byte zero.

### Seleção de Crates e Ecossistema de Transporte de Rede

Para interagir com o repositório principal da indústria, o Hugging Face Hub, de maneira nativa em Rust, o ecossistema oferece opções maduras. O crate oficial `hf-hub` evoluiu substancialmente e agora oferece suporte integrado e robusto ao ecossistema assíncrono do Tokio. Esta biblioteca emula grande parte da funcionalidade do pacote Python correspondente (`huggingface_hub`), implementando um cache local compatível, resolução de links simbólicos e suporte a downloads específicos por versão, utilizando hashes de commit para garantir a reprodutibilidade determinística dos artefatos.

Contudo, para um controle granular absoluto sobre a telemetria de rede, tamanhos de chunks, limites de taxa (rate limiting adaptativo) e, de importância primordial, a retomada explícita e controlada de downloads (resume), a engenharia subjacente a utilitários modernos como o `rust-hf-downloader` e `model-hub` fornece o blueprint arquitetural ideal. A arquitetura de download do Local Model Manager deve ser construída sobre uma fundação tecnológica específica:

1. **Cliente HTTP e Multiplexação:** Utilização do crate `reqwest` com suporte total a execução assíncrona e fluxos (streams) de dados, permitindo que os bytes sejam processados à medida que chegam na interface de rede, sem alocar o arquivo inteiro na RAM.
2. **Camada de Criptografia TLS:** Adoção do `rustls`, garantindo uma implementação de Transport Layer Security puramente em Rust. Isso remove a dependência de bibliotecas C dinâmicas do sistema operacional host (como OpenSSL), o que é vital para a portabilidade e segurança em ambientes bare-metal, reduzindo a superfície de ataque e potenciais falhas de linkagem dinâmica.
3. **Runtime Assíncrono:** O `tokio` configurado explicitamente com a feature `rt-multi-thread`, habilitando o paralelismo real na manipulação de múltiplos streams de micro-modelos simultaneamente e permitindo o escalonamento eficiente de tarefas de I/O em todos os núcleos lógicos do processador Intel i9.

|**Componente da Pilha de Rede**|**Crate Recomendado**|**Justificativa Arquitetural no Contexto do SODA**|
|---|---|---|
|**Interface de Repositório**|`hf-hub` (via API) ou Custom HTTP|Integração direta com a topologia e cache do Hugging Face. Facilita a resolução de meta-dados e localização de _blobs_.|
|**Transporte HTTP**|`reqwest`|Suporte robusto a streaming assíncrono, conexão persistente (keep-alive) e injeção fluida de cabeçalhos HTTP customizados.|
|**Segurança (TLS)**|`rustls`|Eliminação de dependências C dinâmicas externas, garantindo que o binário Rust seja verdadeiramente autônomo (standalone).|
|**Concorrência e Event-Loop**|`tokio`|Despacho não-bloqueante de operações de rede, permitindo que o roteador ParetoBandit continue processando lógica agêntica.|

### Semântica de Retomada de Downloads (Resumable Downloads) e Fluxos de Dados

A capacidade de retomada de downloads interrompidos é uma funcionalidade complexa e absolutamente não trivial quando se lida com arquivos GGUF multiparte que pesam vários gigabytes. A infraestrutura de entrega de conteúdo (CDN) do Hugging Face suporta ativamente a transferência de dados baseada em chunks (transferência frequentemente habilitada por protocolos como Xet) e permite solicitações de intervalos específicos de bytes.

A implementação de uma retomada pragmática e resiliente exige que o sistema rastreie autonomamente o progresso da escrita no disco local. A heurística recomendada para o Local Model Manager do SODA envolve a criação de uma máquina de estados de transferência associada a arquivos temporários no sistema de arquivos. Quando o SODA decide baixar um arquivo `model.gguf`, a seguinte sequência lógica deve ser rigorosamente seguida:

1. **Verificação de Estado Transitório:** O gerenciador verifica se existe um arquivo com sufixo `.incomplete` correspondente ao artefato alvo no disco de armazenamento.
2. **Sondagem de Offset:** O sistema lê o tamanho atual do arquivo incompleto em bytes (por exemplo, se o arquivo possui 2.14 GB de um total esperado de 4.00 GB).
3. **Negociação HTTP Partial Content:** O gerenciador injeta dinamicamente um cabeçalho HTTP padrão da RFC 7233, `Range: bytes=N-`, na requisição `reqwest`, onde `N` é o tamanho atual do arquivo incompleto no disco local.
4. **Anexação de Fluxo (Streaming Append):** O servidor do Hugging Face, se suportar a retomada (o que é padrão na plataforma), responderá com um código de status HTTP `206 Partial Content`. O stream de dados de resposta é então anexado (appended) diretamente ao final do arquivo `.incomplete` existente, evitando o desperdício computacional e de banda de re-baixar os 2.14 GB já presentes.
5. **Finalização Atômica:** Uma vez que o tamanho em disco corresponda ao tamanho esperado reportado nos metadados da API do Hugging Face, o arquivo `.incomplete` é renomeado atomicamente para o seu nome final `.gguf`. Operações atômicas de renomeação no nível do sistema de arquivos previnem condições de corrida (race conditions) caso o modelo tente ser carregado no exato momento da conclusão.

Esta lógica de _tracking_ por posição de byte garante que, se o SODA for desligado abruptamente — uma restrição operacional comum em laptops ou hardwares operando sob estresse de bateria ou reinicializações de kernel — o estado da transferência de rede não seja perdido, assegurando a robustez da orquestração invisível.

### Verificação de Integridade Criptográfica (SHA-256) e a Preservação do Event-Loop

A validação da integridade dos dados baixados é, sem dúvida, o calcanhar de Aquiles de aplicações assíncronas mal projetadas em Rust. Arquivos GGUF garantem sua integridade estrutural e mitigam riscos de corrupção ou ataques de injeção atestando assinaturas criptográficas SHA-256, fornecidas via API ou em arquivos de hash acompanhantes. Contudo, a computação de um hash SHA-256 sobre um arquivo de 5 GB é uma tarefa estritamente ligada à CPU (CPU-bound).

Se a computação desse hash for executada diretamente em uma tarefa (task) padrão do Tokio — por exemplo, utilizando um `tokio::spawn` normal que lê blocos do arquivo de forma assíncrona e calcula o hash sequencialmente — ocorrerá um fenômeno sistêmico perigoso conhecido como _starvation_ (inanição) do agendador (scheduler). O modelo arquitetural do Tokio pressupõe fundamentalmente que as tasks farão _yield_ (cederão o controle cooperativamente de volta ao agendador) em intervalos extremamente curtos, idealmente não excedendo 10 a 100 microssegundos.

O bloqueio de uma thread de processamento (worker thread) do Tokio por vários segundos (o tempo necessário para hashear gigabytes de dados) impedirá o event-loop de processar outras promessas (futures). Isso significa que o roteador cognitivo `ParetoBandit` seria incapaz de processar novas chamadas de rede, responder a interrupções do sistema, ou analisar eventos de telemetria simultaneamente, resultando em latência inaceitável e falhas de timeout em toda a malha do Sistema Operacional Agêntico.

A abordagem arquitetural mandatória para evitar este cenário de catástrofe de concorrência é realizar o _offload_ (descarregamento) de toda a validação de integridade para o _thread pool_ dedicado a operações bloqueantes do Tokio, utilizando a função `tokio::task::spawn_blocking`. Este pool de threads não tem um limite estrito de concorrência para o event-loop principal e é projetado especificamente para operações síncronas pesadas ou cálculos intensivos de CPU que não podem fazer yield.

A estrutura otimizada do verificador de integridade dentro do Local Model Manager deve operar da seguinte forma:

1. **Transição de Contexto:** Ao concluir o download atômico, o caminho do arquivo GGUF finalizado é passado como parâmetro para uma _closure_ encapsulada em `tokio::task::spawn_blocking`.
2. **Seleção de I/O Síncrono:** Dentro deste escopo de bloqueio, utiliza-se expressamente a biblioteca padrão do Rust `std::fs::File`, e não a sua contraparte assíncrona `tokio::fs::File`. O uso intensivo de `tokio::fs::File` em loops que leem blocos pequenos de dados incorre em uma sobrecarga massiva, pois cada operação de leitura assíncrona sob o capô delega para o `spawn_blocking`, resultando em milhares de trocas de contexto (context switches) desnecessárias e degradação drástica da taxa de transferência do SSD.
3. **Processamento em Janelas (Chunked Hashing):** O crate de criptografia rápida, como `sha2` (parte do ecossistema `RustCrypto`), é instanciado. O arquivo é lido sequencialmente em blocos contíguos grandes (tipicamente de 1 MB a 4 MB) pré-alocados em memória, que são diretamente alimentados na função de atualização contínua do hash.
4. **Validação Condicional:** O _digest_ resultante final é comparado com a assinatura SHA-256 remota extraída dos metadados da API.
5. **Ação Corretiva:** Se a verificação falhar (indicando corrupção em trânsito ou gravação incompleta), o arquivo corrompido é sumariamente deletado via I/O síncrono. O `spawn_blocking` retorna um `Result::Err` para o event-loop principal, e o gerenciador de estado reagenda o download, ativando uma política de _backoff_ exponencial (tipicamente com um teto de 3 a 5 tentativas) para mitigar falhas transientes do provedor de nuvem.

| **Estratégia de Verificação SHA-256** | **Impacto no Event-Loop Tokio** | **Taxa de Leitura de Disco (I/O)**       | **Risco Arquitetural**                                                                                      |
| ------------------------------------- | ------------------------------- | ---------------------------------------- | ----------------------------------------------------------------------------------------------------------- |
| `tokio::spawn` + `tokio::fs`          | **Grave (Starvation)**          | Lenta (Overhead de contexto constante)   | Congelamento do Roteador ParetoBandit.                                                                      |
| `spawn_blocking` + `tokio::fs`        | Moderado                        | Muito Lenta (Duplo dispatch de bloqueio) | Desperdício massivo de ciclos de CPU.                                                                       |
| **`spawn_blocking` + `std::fs`**      | **Nulo (Total Isolamento)**     | **Máxima (I/O Nativo do SO)**            | **Ideal.** Requer apenas cuidado para não esgotar o pool de threads com excesso de concorrência simultânea. |

Esta segregação estrita entre I/O puramente assíncrono (requisições de rede) e I/O bloqueante associado a computação pesada (leitura intensiva de disco acoplada a hash criptográfico) garante que o "Local Model Manager" permaneça 100% não bloqueante, ágil e capaz de orquestrar a IA de forma responsiva.

## Eixo 2: Auto-Profiling e Orquestração Preditiva de Hardware (VRAM)

O gerenciamento em tempo real da Memória de Vídeo (VRAM) constitui a fronteira mais crítica para a viabilidade de sistemas baseados em LLMs operando em hardwares com restrições arquiteturais severas, como a GPU RTX 2060m móvel com seu limite absoluto de 6 GB. Em inferência local, quando a combinação do tamanho estático do modelo e seu KV Cache (Key-Value Cache) dinâmico excede a capacidade dedicada da VRAM, o sistema é forçado a realizar _spill_ (transbordar) as ativações e tensores excedentes para a memória RAM do sistema via barramento.

A latência introduzida pelo barramento PCIe para essa comunicação constante de dupla via destrói completamente o desempenho de geração. A vazão (throughput) de tokens por segundo sofre uma penalidade excruciante, frequentemente caindo de taxas fluidas (50-100 t/s) para taxas inutilizáveis de 2-5 t/s. Para fluxos agênticos autônomos, onde dezenas de iterações de pensamento e invocações de ferramentas devem ocorrer em frações de segundo, essa degradação é sistemicamente inaceitável. Portanto, a orquestração deve ser preditiva e restritiva, garantindo que o footprint total da inferência permaneça rigorosamente confinado aos limites da VRAM.

### Telemetria e Detecção Multiplataforma (Além da NVIDIA)

Para que o roteador cognitivo tome decisões precisas, a aplicação SODA deve inspecionar dinamicamente as capacidades do host durante a inicialização (boot sequence). Embora tradicionalmente a comunidade tenha dependido exclusivamente do crate `nvml-wrapper` para acessar dados via NVIDIA Management Library (NVML) , o cenário de implantação de um sistema operacional agêntico bare-metal em 2025/2026 exige suporte abrangente e agnóstico de hardware.

O crate ideal recomendado para o SODA é o utilitário integrado `all-smi` (frequentemente utilizado em conjunto com bibliotecas de baixo nível como `savant-nvidia-gpu-utils` ou `rocm_smi_lib`). O `all-smi` unifica a extração de telemetria lidando nativamente com múltiplas infraestruturas:

- **NVIDIA:** Utiliza bindings dinâmicos NVML para reportar limites exatos de VRAM livre, uso de núcleos e estrangulamento térmico.
- **AMD (Radeon/Instinct):** Fornece _fallback_ para a plataforma ROCm, lendo os nós de memória GTT e topologia do driver.
- **Apple Silicon:** Interage com o framework `Metal` do macOS e `powermetrics`, permitindo que o sistema diferencie a memória RAM unificada disponível da porção efetivamente bloqueada pelo Neural Engine.

Essa abstração garante que as consultas iniciais falhem graciosamente se o driver primário não for detectado e forneçam imediatamente a capacidade máxima estrita (ex: 6.0 GB na RTX 2060m) ao ParetoBandit sem comprometer o _runtime_.

### Extração de Metadados GGUF (Zero-Copy) para Dimensionamento

Saber o limite de VRAM não é suficiente; o sistema precisa saber o quanto um modelo consumirá _antes_ de enviá-lo para a GPU. Um arquivo GGUF encapsula não apenas os pesos neurais, mas um cabeçalho contendo toda a topologia arquitetural (como contagem de camadas e dimensões do KV Cache).

Para calcular a matemática de memória preventivamente sem causar _Out of Memory_ (OOM), o SODA não deve carregar as dezenas de gigabytes do modelo na RAM. Em vez disso, deve-se adotar crates de inspeção puramente dedicados a _Zero-Copy Metadata Extraction_, como o `inspector_gguf` ou o `bare-metal-gguf`. Estas bibliotecas utilizam exclusivamente funções do sistema operacional como `mmap()` (memory mapping) sobre o arquivo dormente no SSD, lendo _apenas_ os metadados do cabeçalho binário (como `llama.attention.head_count_kv` e `llama.context_length`) e deixando a seção massiva de tensores inexplorada no disco. Esta verificação consome praticamente 0 bytes de RAM e ocorre instantaneamente.

### A Matemática da Inferência: Anatomia do Consumo de VRAM

Uma vez que os metadados são extraídos assincronamente pelo `inspector_gguf`, a alocação preditiva passa pela equação:

$$VRAM_{Total} = Memoria_{Pesos} + Memoria_{KVCache} + Memoria_{Overhead}$$

1. **Overhead de Infraestrutura ($Memoria_{Overhead}$):** Contextos lógicos de CUDA ou Vulkan demandam uma alocação de segurança estipulada em **1.0 GB a 1.5 GB de VRAM**. Em uma RTX 2060m, isso reduz o espaço funcional para cerca de **4.5 GB**.
2. **Pegada dos Pesos ($Memoria_{Pesos}$):** Definida pela quantização do modelo, mapeada dinamicamente via metadados.
3. **Key-Value Cache Dinâmico ($Memoria_{KVCache}$):** A métrica mais volátil, pois escala linearmente conforme a profundidade do contexto. Através dos dados lidos pelo `inspector_gguf`, aplica-se a heurística redutora dependendo da arquitetura (por exemplo, modelos GQA como Llama 3 reduzem consideravelmente o cache comparado à MHA convencional).

### Análise Comparativa de Quantização: Q4_K_M vs. Q8_0 para Micro-Modelos

Para os modelos especialistas alvejados pelo SODA (como Qwen 3B para código e FunctionGemma 2B para chamadas de ferramentas), o ParetoBandit tomará a decisão sobre os níveis de quantização `Q4_K_M` e `Q8_0`.

- **Q4_K_M:** Comprime pesos vitais via heurística de _outliers_ para ~4-bits agrupados, consumindo em média **~0.55 a 0.57 bytes por parâmetro**.
- **Q8_0:** Uma conversão quase simétrica e rigorosa, operando de maneira inalterada e requerendo exatamente **1.0 byte por parâmetro**.

Tabela de Projeção Dinâmica de VRAM para um Micro-Modelo de 3.0 Bilhões de Parâmetros (ex: Qwen 3B) sob contexto de 4.096 Tokens (arquitetura GQA):

|**Tipo de Quantização**|**Eficiência e Precisão**|**Consumo de Pesos Estimado**|**Consumo KV Cache Projetado**|**VRAM Operacional (Excluindo Overhead de 1.5GB)**|
|---|---|---|---|---|
|**Q4_K_M**|Excelente (Perda Marginal de Raciocínio)|**~1.65 GB a 1.70 GB**|~0.50 GB a 1.00 GB|**~2.15 GB a 2.70 GB**|
|**Q8_0**|Qualidade Máxima (Quase Nativa)|**~3.00 GB**|~0.50 GB a 1.00 GB|**~3.50 GB a 4.00 GB**|

A consolidação inelutável sob a placa limitadora de 6 GB aponta o **`Q4_K_M` como a escolha algorítmica universal preferencial**. Carregar Qwen 3B em `Q8_0` engolfaria a GPU (5.5 GB com overhead), deixando o SODA sem fôlego para rodar o modelo concorrente Kokoro-82M de síntese de voz (que por si só demanda ~1 a 2 GB). O formato `Q4_K_M` cede um _headroom_ necessário de até 1.8 GB, permitindo que a comutação lógica e os fluxos de ferramentaria operem simultaneamente sem forçar falhas letais do agendador por exaustão de memória.

## Eixo 3: Gerenciamento de Espaço Físico, Protocolos na Nuvem e Evicção (LRU)

Modelos GGUF impõem uma carga formidável em unidades SSD. Com tamanhos de múltiplos gigabytes sendo continuamente iterados durante o roteamento automatizado do ParetoBandit, é imperativo estruturar uma política restritiva de evicção baseada em tempo de acesso (Least Recently Used - LRU) a fim de expurgar autonomamente arquivos que ultrapassem limites rígidos predefinidos de armazenamento.

### Protocolos de Registro de Modelo (Model Context Protocol)

Em vez de formatar o "Model Registry" como um JSON genérico isolado, a arquitetura moderna (horizonte 2026) da Inteligência Artificial adotou largamente o **Model Context Protocol (MCP)**.

O MCP opera nativamente usando a especificação JSON-RPC 2.0 e fornece o padrão da indústria para catalogar não apenas a infraestrutura de hospedagem GGUF no provedor primário, mas também definir quais ferramentas (como formatadores de saída JSON) o modelo consegue assimilar. Ao construir a matriz remota e local dos metadados do SODA sob a chancela MCP, garante-se uma padronização universal para que o orquestrador determine se a versão do modelo baixada atende perfeitamente ao prompt esperado para aquela função lógica específica.

### A Mecânica Pragmatica de Cache (Regra 90/10) via Tokio Actors

Pacotes pesados como bancos SQLite (para mapear 20 arquivos GGUF) ou frameworks complexos do ecossistema C++ como `foyer` incorrem em severas violações de simplicidade sob as leis da arquitetura bare-metal orientada pela regra 90/10. Por outro lado, confiar primitivamente na medição nativa de Kernel dos atributos temporais de disco (inodes via `atime`) frequentemente resulta em confusões oriundas de varreduras do SO hospedeiro ou falhas com os limites do disco configurados em `noatime`.

A melhor e mais robusta abordagem é empregar **estruturas baseadas em persistência de arquivos orientadas a coleções densas, combinadas a Padrões de Atores Assíncronos**, como o ecossistema fornecido pelo `file_backed` encapsulado através do design `tokio-actors` (ou `rsActor`).

1. **In-Memory LRU Persistido no FS:** O crate `file_backed` foi arquitetado especificamente para atuar como ponte em coleções de arquivos pesando em gigabytes. Ele gerencia logicamente o rastreio da validade através de uma abstração em memória da cadeia LRU, mas realiza a manutenção e apagamento dos blocos diretamente atrelando aos caminhos temporários no sistema SSD de base, executando limpezas automáticas garantidas via canais subjacentes.
2. **Isolamento Absoluto Contra Condições de Corrida:** O problema de uma instrução agêntica decidir deletar o arquivo GGUF milissegundos antes do ParetoBandit o requisitar para alocação mmap é resolvido por crates especializados como o `tokio-actors` ou `rsActor`. A orquestração das informações fica confinada unicamente à _task_ do Ator (`LocalModelManagerActor`), que possui total autoridade privada de acesso e deleção, bloqueando contenções destrutivas comumente geradas por `Arc<Mutex<T>>` massivos.
3. **Prevenção de OOM do Próprio SODA:** Em um sistema multiagente, o tráfego de deleção, carga e controle pode inundar canais de mensagens. Utilitários refinados em Rust, como `tokio-actors`, dispõem de limites demarcados (bounded mailboxes) e _backpressure_ com políticas configuráveis em borda de carga (ex: `MissPolicy::Skip`), previnindo o colapso interno do programa orquestrador quando sujeito a alta frequência transacional dos agentes invisíveis.
4. **Operação de Varredura Assíncrona:** A limpeza e evicção do cache LRU finaliza despachando chamadas sistêmicas seguras via rotinas `tokio::fs::remove_file` sob as determinações estritas das tarefas de background não bloqueantes do `file_backed`, liberando imediatamente gigabytes na unidade de estado sólido sempre que a marca limite predefinida pelo sistema SODA for violada.

Esta infraestrutura combina a visibilidade analítica, resiliência térmica, extração inteligente de metadados de inferência com segurança extrema de canal. Permite que o SODA opere em background, com absoluta leveza assíncrona, transitando modelos fundamentais sobre a linha de limite da VRAM em hardwares bare-metal altamente confinados sem interceder daemons alheios ao contexto central.