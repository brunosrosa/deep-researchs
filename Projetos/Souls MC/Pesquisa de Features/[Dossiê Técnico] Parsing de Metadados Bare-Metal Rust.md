# Dossiê Técnico: Scanner Bare-Metal de Alta Performance e Isolamento para o Souls MC SODA (2026)

## Introdução e Fundamentação Termodinâmica da Memória

A construção de um sistema operacional agêntico soberano, como o Souls MC (SODA), sob restrições físicas de hardware locais, exige uma abordagem intransigente no gerenciamento de recursos. Em cenários operacionais caracterizados por processadores de última geração, mas limitados a GPUs com capacidade de VRAM restrita — como a NVIDIA RTX 2060m equipada com apenas 6 GB de memória de vídeo dedicada —, a orquestração dinâmica de modelos de linguagem de grande escala (LLMs) torna-se um exercício de equilíbrio termodinâmico. Modelos quantizados modernos de escala de 8B parâmetros, como o Phi-4-mini ou o Qwen 3.5 Coder, operam no limiar dos limites físicos do chip gráfico. Dependendo da quantização utilizada (por exemplo, `IQ3_M` baseada em matrizes de importância), a pegada estática do modelo na memória de vídeo varia entre 3.5 GB e 3.8 GB, restando um espaço marginal para acomodar o cache dinâmico de chaves e valores (KV Cache) sob janelas de contexto longas de até 30.000 tokens.

Nesse cenário, o roteador cognitivo do Souls MC precisa de informações instantâneas sobre os pesos disponíveis localmente para tomar decisões de alocação _on-the-fly_. É arquiteturalmente inviável que, durante o ciclo de varredura ativa de diretórios, o sistema operacional carregue gigabytes de dados de tensores na memória RAM do sistema ou inicialize instâncias pesadas de runtimes de inferência apenas para inspecionar parâmetros básicos. A ingestão de metadados deve operar estritamente sob as regras de complexidade temporal $\mathcal{O}(1)$ e cópia zero (_zero-copy_).

Para viabilizar este comportamento, o SODA rejeita qualquer forma de leitura sequencial de arquivos em espaço de usuário, adotando em seu lugar o mapeamento de arquivos em memória virtual através da chamada de sistema `mmap`. Ao projetar o arquivo diretamente no espaço de endereçamento virtual do processo através da crate `memmap2`, o sistema operacional passa a tratar o arquivo em disco como um vetor contíguo de bytes em memória. O kernel do sistema operacional carrega as páginas de memória de forma preguiçosa (_lazy paging_), trazendo para a memória física estritamente os blocos contendo as tabelas de cabeçalho e metadados lógicos do arquivo. Todo o restante do arquivo, correspondente aos gigabytes de pesos e tensores numéricos, permanece intocado no SSD, garantindo que a pegada de memória do scanner permaneça insignificante e constante, independentemente do tamanho do modelo analisado.

Os metadados extraídos pelo scanner — incluindo a arquitetura base (família), a janela de contexto máxima suportada, a quantização de pesos e o tamanho total do modelo em parâmetros — são normalizados e persistidos diretamente em um banco de dados local SQLite (`soda_heuristic_vault.db`). Essa base atua como o registro de metadados unificado do SODA, permitindo ao roteador consultar a disponibilidade física de recursos em microssegundos antes de disparar processos de inferência.

## 1. O Estado da Arte para Parsing de GGUF

O formato GGUF (GPT-Generated Unified Format) consolidou-se como o padrão definitivo para implantação de LLMs locais quantizados. Projetado pela comunidade em substituição aos formatos legados GGML e GGJT, o GGUF resolveu o problema de extensibilidade através de um modelo auto-descritivo fundamentado em um dicionário estruturado de chave-valor tipado antes da declaração dos metadados de tensores. A leitura desse cabeçalho exige ferramentas eficientes que aproveitem o mapeamento de arquivos sem alocar estruturas desnecessárias na memória.

### Avaliação Comparativa de Crates de Parsing GGUF

A tabela a seguir apresenta uma comparação exaustiva das principais soluções de parsing GGUF disponíveis no ecossistema Rust em 2026:

|**Crate GGUF**|**Abordagem de Parsing**|**Suporte a mmap**|**Gestão de Alocação de Heap**|**Segurança do Código (Unsafe)**|**Veredito de Integração no SODA**|
|---|---|---|---|---|---|
|**`gguf-rs`**|Baseada em fluxo síncrono com transmutação rápida.|Sim, via flag opcional `mmap`.|Aloca chaves do dicionário em estruturas dinâmicas padrão do Rust.|Permite blocos unsafe controlados para transmutação.|**Recomendado para uso geral** devido à maturidade e suporte a little/big endian.|
|**`gguf-rs-lib`**|Focada em manipulação de modelos com suporte assíncrono para Tokio e Serde.|Sim, sob demanda.|Moderada alocação de vetores intermediários durante o parsing recursivo de arrays.|Totalmente livre de código unsafe por padrão.|**Viável**, porém as dependências de Serde adicionam sobrecarga desnecessária ao daemon de scan.|
|**`oxillama-gguf`**|Parser de baixo nível desenhado especificamente para o motor de inferência OxiLLaMa.|Sim, integrado nativamente ao fluxo.|Baixa taxa de alocação; lê fatias estruturadas diretamente do buffer binário.|Uso restrito de unsafe para leitura de fluxos de bytes lógicos.|**Excelente alternativa** pela simplicidade estrutural.|
|**`gguf-llms`**|Extrator de configuração de modelos baseado em mapeamento de tipos fortes.|Não possui suporte nativo a `mmap` em versões estáveis.|Elevada alocação na conversão de tipos estruturados.|Código puramente seguro.|**Rejeitado** devido à ausência de carregamento preguiçoso de disco.|
|**Mapeamento Cru via `memmap2`**|Parsing linear ad-hoc escrito diretamente sobre o ponteiro binário do arquivo.|Sim, controle manual absoluto sobre a projeção do arquivo.|**Zero alocação na heap**; trabalha apenas com referências temporais `&str`.|Exige o uso explícito de blocos unsafe para manipulação de ponteiros binários.|**Vencedor Técnico Absoluto** para o hot-path do scanner de latência sub-milissegundo.|

### Pontos Cegos, Limitações e Bugs Conhecidos do GGUF

O desenvolvimento de parsers robustos para o formato GGUF deve tratar ativamente anomalias históricas do formato que geram comportamentos estocásticos ou interrupções no runtime do Rust:

#### A Armadilha de Endianness entre GGUF v2 e v3

Os arquivos de modelo estruturados até a versão v2 assumiam que a ordenação de bytes seria estritamente _Little-Endian_, correspondente à arquitetura x86_64 predominante no mercado de PCs e servidores acelerados por GPU. Com a introdução do GGUF v3 e a necessidade de suportar mainframes e arquiteturas específicas baseadas em _Big-Endian_ (como o IBM s390x), a especificação foi expandida para suportar ordenações de bytes alternativas.

No entanto, o design do cabeçalho omitiu uma flag lógica de endianness que pudesse ser lida de forma independente da ordenação do próprio host. Como consequência direta, se um parser executado em um host x86_64 tentar ler as variáveis de tamanho do cabeçalho de um arquivo v3 codificado em _Big-Endian_, os números de controle de dimensões de arrays e contagem de tensores serão interpretados de forma invertida. Isso leva a tentativas de alocação de heap na ordem de exabytes, gerando pânicos imediatos ou travamentos silenciosos por exaustão de memória virtual.

A vanguarda do ecossistema resolveu este ponto cego através de uma quebra deliberada de especificação. Arquivos GGUF gravados em _Big-Endian_ são obrigados a utilizar a assinatura invertida `"FUGG"` (bytes `0x46 0x55 0x47 0x47`) no primeiro bloco de 4 bytes, em substituição ao clássico `"GGUF"` (bytes `0x47 0x47 0x55 0x46`). O scanner do Souls MC deve ler os primeiros 4 bytes de forma crua; se a assinatura coincidir com `"FUGG"`, o parser deve configurar ativamente decodificadores baseados no módulo `byteorder::BigEndian` para todas as leituras subsequentes do arquivo mapeado.

#### Truncamento Silencioso de Arrays de Vocabulário

Em sua configuração padrão, crates de parsing genéricas limitam deliberadamente a profundidade de leitura de metadados do tipo array para no máximo 3 elementos. Essa escolha de design visa proteger a memória do host de ler massivos vetores textuais (como a lista de tokens do tokenizer do modelo, que frequentemente excede 100.000 entradas) quando o usuário solicita apenas metadados estruturais elementares.

Se o Local Model Manager do SODA precisar acessar o vocabulário para sincronização lógica de agentes locais, chamadas ingênuas retornarão arrays incompletos sem emitir nenhum erro de execução. A resolução desse ponto cego exige o uso explícito de rotinas de leitura de tamanho irrestrito, como `get_gguf_container_array_size` passando o limite `u64::MAX` para áreas selecionadas, aceitando o custo controlado de alocação na heap estritamente para essas propriedades do dicionário.

#### Falhas Catastróficas no Processamento de Quantizações Não-Alinhadas

Modelos hiper-quantizados de alta compressão baseados na série de formatos `IQ` (como `IQ3_M` ou `IQ1_S`) organizam os dados de pesos em estruturas binárias complexas com quantização baseada em blocos não-lineares, com tamanhos que não correspondem a limites exatos de bytes ou words. Parsers de alto nível que tentam percorrer o diretório de tensores realizando a transmutação imediata ou validação de tipos de dados para tipos padrão de Rust geram falhas de alinhamento ao processar limites de layouts de sub-bytes.

Para evitar pânicos no thread do daemon, o scanner do Souls MC deve ser estritamente configurado para ignorar a validação matemática de tensores, extraindo apenas os offsets brutos de posicionamento na memória virtual mapeada, relegando qualquer validação ou dequantização estrutural de pesos para os kernels específicos do backend de execução do motor de inferência.

## 2. O Estado da Arte para Safetensors

O formato Safetensors, mantido sob governança de fundações de inteligência artificial, foi desenhado especificamente para garantir segurança cibernética absoluta no compartilhamento de pesos, impedindo a execução de códigos arbitrários contidos no formato Pickle do PyTorch. Além disso, ele é altamente otimizado para operações de cópia zero. Seu layout físico em disco é linear e previsível:

```
+--------------------------------+--------------------------------------+------------------------------------+
| Header Size Length (8 bytes)   | JSON Header Block (N bytes)          | Raw Tensor Byte Buffer (Resto)     |
| u64 Integer (Little-Endian)    | UTF-8 JSON describing layouts & metadata | Contiguous binary float weights    |
+--------------------------------+--------------------------------------+------------------------------------+
```

Os primeiros 8 bytes contêm um inteiro `u64` sem sinal, codificado em ordenação de bytes _Little-Endian_, que expressa precisamente o tamanho $N$ do cabeçalho em bytes. Os $N$ bytes seguintes correspondem à string do cabeçalho codificada em UTF-8. Essa string deve obrigatoriamente iniciar com o caractere `{` (byte `0x7B`) e pode conter preenchimento opcional de espaços em branco (bytes `0x20`) ao final para garantir alinhamento. O restante do arquivo abriga o buffer bruto contíguo de tensores.

### Estratégia de Extração Zero-Copy e Sem Alocação

Para ler as propriedades contidas no dicionário especial de metadados (`__metadata__`) sem disparar o coletor de lixo da interface ou gerar alocações redundantes na heap do processo Rust, o Souls MC adota o padrão de design de **analisadores baseados em tempos de vida estáticos**. O fatiamento inteligente de strings elimina a necessidade de inicializar tipos `String` proprietários.

O arquivo é mapeado em memória virtual, fornecendo um buffer estável do tipo `&[u8]` cujo tempo de vida está acoplado à existência física da projeção do arquivo na tabela de páginas do processo. Lendo os primeiros 8 bytes lógicos, transmuta-se o valor para um índice numérico `usize` correspondente a $N$. Realiza-se então um fatiamento do buffer virtual limitando o escopo estritamente de `8` até `8 + N`. Essa fatia de bytes contígua é validada e convertida em uma referência textual `&'a str` sem que um único byte seja copiado do buffer original.

### Análise Comparativa de Parsers JSON Zero-Copy

Para analisar a string do cabeçalho sem gerar alocações de heap intermediárias na extração dos metadados, o arquiteto deve avaliar as soluções de parsing JSON disponíveis no ecossistema Rust sob a ótica do consumo térmico e de latência:

|**Parser JSON**|**Modelo de Alocação de Memória**|**Parsing Lazy / On-Demand**|**Velocidade de Processamento**|**Adequação para o Souls MC**|
|---|---|---|---|---|
|**`serde_json`**|Aloca chaves do dicionário e strings não-escapadas em novas instâncias na heap.|Não, desserializa a árvore completa de objetos por padrão.|Moderada; gera fragmentação e pressão sobre o alocador em threads simultâneas.|**Inadequado** para o hot-path devido à alta alocação de memória virtual.|
|**`hifijson`**|Zero-allocation para chaves e números; utiliza `&str` e referências baseadas em lifetimes.|Sim, permite iteração sequencial e descarte de nós irrelevantes.|Altíssimo throughput de caracteres lógicos por ciclo de CPU.|**Altamente Recomendado** para extração tipada de dados estruturados.|
|**`jsode`**|Garante zero alocação mantendo a árvore abstrata (AST) referenciada no buffer original.|Totalmente lazy; adia qualquer cópia ou unescape de dados até o acesso explícito.|Latência de inicialização virtualmente nula.|**Vencedor para Extração Plana** de chaves diretas.|
|**`zjson`**|Implementa especificação ECMA-404 com zero alocação de heap.|Sequencial; exige o consumo linear de nós filhos na iteração.|Alto desempenho em buffers de tamanho moderado.|**Viável**, porém a complexidade da API reduz a ergonomia do código.|

### Implementação de Referência: Parser Safetensors Zero-Copy

Abaixo está detalhado o código de produção idiomático para o Souls MC, demonstrando o mapeamento físico do arquivo em disco via `memmap2` e o parsing seguro sem alocação do dicionário de metadados do Safetensors através de referências baseadas em tempos de vida:

```Rust
use std::fs::File;
use std::path::Path;
use std::str;
use memmap2::MmapOptions;

#[derive(Debug)]
pub enum ScannerError {
    Io(std::io::Error),
    HeaderTooSmall,
    HeaderTooLarge,
    InvalidUtf8,
    InvalidJson,
    MetadataNotFound,
}

pub struct ModelMetadata<'a> {
    pub family: &'a str,
    pub context_length: u64,
    pub quantization: &'a str,
    pub parameters_estimate: u64,
}

pub struct SafetensorsScanner {
    mmap: memmap2::Mmap,
}

impl SafetensorsScanner {
    pub fn open<P: AsRef<Path>>(path: P) -> Result<Self, ScannerError> {
        let file = File::open(path).map_err(ScannerError::Io)?;
        let mmap = unsafe {
            MmapOptions::new()
                .map(&file)
                .map_err(ScannerError::Io)?
        };
        Ok(Self { mmap })
    }

    pub fn extract_metadata(&self) -> Result<ModelMetadata<'_>, ScannerError> {
        if self.mmap.len() < 8 {
            return Err(ScannerError::HeaderTooSmall);
        }

        // Extrai o tamanho N do cabeçalho contido nos primeiros 8 bytes (Little-Endian)
        let mut header_size_bytes = [0u8; 8];
        header_size_bytes.copy_from_slice(&self.mmap[0.]);
        let header_size = u64::from_le_bytes(header_size_bytes) as usize;

        // Impõe o limite físico de segurança do Safetensors de 100MB para evitar DoS
        if header_size > 100 * 1024 * 1024 {
            return Err(ScannerError::HeaderTooLarge);
        }

        if self.mmap.len() < 8 + header_size {
            return Err(ScannerError::HeaderTooSmall);
        }

        // Fatiamento zero-copy do cabeçalho JSON
        let header_bytes = &self.mmap[8. + header_size];
        let header_str = str::from_utf8(header_bytes).map_err(|_| ScannerError::InvalidUtf8)?;

        // Exemplo conceitual de parsing zero-copy utilizando correspondência de padrões
        // Em produção, esta string é passada para os analisadores lazy da crate hifijson
        let family = self.extract_json_key(header_str, "general.architecture")?;
        let context_length = self.extract_json_numeric(header_str, "context_length")?;
        let quantization = self.extract_json_key(header_str, "quantization_version")?;
        let parameters_estimate = self.extract_json_numeric(header_str, "parameters")?;

        Ok(ModelMetadata {
            family,
            context_length,
            quantization,
            parameters_estimate,
        })
    }

    fn extract_json_key<'a>(&self, json: &'a str, key: &str) -> Result<&'a str, ScannerError> {
        // Rotina de busca rápida de strings sem alocação
        if let Some(pos) = json.find(key) {
            let offset_block = &json[pos..];
            if let Some(start_quote) = offset_block.find(':') {
                let val_block = &offset_block[start_quote..];
                if let Some(v_start) = val_block.find('"') {
                    let final_block = &val_block[v_start + 1..];
                    if let Some(v_end) = final_block.find('"') {
                        return Ok(&final_block[..v_end]);
                    }
                }
            }
        }
        Err(ScannerError::MetadataNotFound)
    }

    fn extract_json_numeric(&self, json: &str, key: &str) -> Result<u64, ScannerError> {
        if let Some(pos) = json.find(key) {
            let offset_block = &json[pos..];
            if let Some(start_colon) = offset_block.find(':') {
                let val_block = &offset_block[start_colon + 1..];
                let trimmed = val_block.trim_start_matches(|c: char| c.is_whitespace() || c == '"');
                let end_pos = trimmed.find(|c: char| !c.is_numeric());
                if let Some(end) = end_pos {
                    return trimmed[..end].parse::<u64>().map_err(|_| ScannerError::InvalidJson);
                }
            }
        }
        Ok(0)
    }
}
```

## 3. Armadilhas de Segurança e Fallbacks (O Pessimismo da Razão)

O processamento e a varredura automática de diretórios que contêm arquivos de modelos descarregados de fontes externas introduzem sérios vetores de ataque cibernético e vulnerabilidades de infraestrutura local. Sob a doutrina do _Pessimismo da Razão_, o SODA assume que qualquer arquivo no sistema de arquivos do usuário pode estar deliberadamente corrompido ou modificado para paralisar os daemons do sistema.

### Vetores de Ataque Críticos para o Scanner

#### Esgotamento de Memória Virtual (OOM Attack) por Declarativa Maliciosa

Tanto a especificação GGUF quanto o Safetensors armazenam contadores de 32 e 64 bits para especificar a contagem de elementos do dicionário ou de tensores que devem ser lidos sequencialmente no loop inicial do parser.

Se um atacante modificar um cabeçalho para declarar que o modelo abriga $2^{64} - 1$ tensores, parsers ingênuos em Rust que utilizam funções padrão de coleções como `Vec::with_capacity(tensor_count)` tentarão alocar imediatamente buffers colossais na memória física. O resultado será o esgotamento instantâneo da capacidade do gerenciador de memória do host, culminando em uma falha de falta de memória (OOM Crash) controlada por sinais do kernel do sistema operacional, derrubando a execução de todo o daemon do gerenciador de modelos agênticos.

#### Violações de Acesso Físico (SIGBUS) por Truncamento Dinâmico de Arquivos

A utilização do mapeamento de memória (`mmap`) cria uma dependência direta em relação à estabilidade do arquivo físico subjacente no disco. Se o gerenciador local de modelos do SODA estiver ativamente processando e inspecionando uma fatia de bytes obtida através da projeção de `memmap2`, e um agente concorrente deletar ou truncar dinamicamente o tamanho do arquivo original no disco físico, o ponteiro de memória virtual de Rust passará a apontar para uma página que não corresponde mais a blocos físicos alocados no SSD.

Ao ler qualquer dado contido nessa fatia, o controlador de memória da CPU (MMU) falhará ao tentar traduzir o endereço virtual, gerando instantaneamente uma falha de página irrecuperável. O sistema operacional enviará um sinal síncleto **SIGBUS** (em sistemas baseados em Unix) ou uma violação de acesso severa (em ambientes Windows) diretamente para o thread ativo.

Como o Rust compila por padrão definindo políticas de aborto ou stack unwinding para panics controlados, o sinal SIGBUS não é interceptado por mecanismos lógicos padrão de manipulação de erros (como o `std::panic::catch_unwind` ou o tipo `Result`). O sinal é emitido diretamente para o processo pai, forçando o encerramento ruidoso do binário inteiro.

### Blindagem e Isolamento da Esteira de Varredura

Para proteger o loop de eventos assíncronos principal do Tokio Daemon contra falhas de memória física, interrupções por arquivos truncados e ataques de negação de serviço, a arquitetura do Souls MC exige o isolamento severo das operações do scanner através de sandboxing multiprocesso.

#### A Técnica de Enclausuramento via WebAssembly (Wasmtime e WASI 0.2)

A abordagem mais moderna adotada na vanguarda do ecossistema de sistemas operacionais agênticos soberanos baseia-se na compilação do parser estruturado de metadados em bytecode WebAssembly voltado ao perfil `wasm32-wasip2`. O daemon do Souls MC instancia o parser dentro de um ambiente isolado gerenciado pela engine `Wasmtime`:

- **Isolamento de Memória Virtual:** A memória linear do WebAssembly é virtualizada pelo runtime e delimitada por amplas páginas de guarda física de 2 GB. Qualquer tentativa de leitura além dos limites declarados gera uma exceção de execução padrão dentro do bytecode.
- **Interceptação de Sinais como Erros Lógicos:** Se o parser sofrer uma violação de acesso ou ler um offset corrompido, a falha é capturada pelo runtime da máquina virtual e traduzida de volta para o host do SODA como um erro do tipo `Trap`. A execução da máquina virtual é abortada instantaneamente, permitindo que o processo mestre em Rust descarte o arquivo corrompido de forma pacífica, reinicialize o estado do sandbox em microssegundos e prossiga para o próximo modelo do diretório.
- **Algema de Combustível de Processamento (_Fuel Limits_):** O host configura o limite máximo de instruções computacionais aceitas por execução ativa através de `config.consume_fuel(true)`. Loops de parsing infinitos introduzidos por malformação de cabeçalhos esgotam o combustível e encerram o thread convidado sem impactar a CPU do host.

#### O Padrão de Processos Efêmeros e Comunicação por IPC Binário

Em ambientes bare-metal onde a sobrecarga de compilação ou compatibilidade do WebAssembly impede seu uso imediato, a esteira do scanner é blindada através do isolamento de processos no nível do kernel do sistema operacional host.

- **Spawning de Processos Filhos Isolados (_Workers_):** O orquestrador central do SODA nunca executa leituras de arquivos de pesos ou metadados de tensores dentro de seu próprio espaço de execução. Ao detectar um novo arquivo para varredura, o daemon inicia um subprocesso efêmero e de privilégios severamente restritos, passando o descritor de arquivo físico (ou um caminho limitado via cercas lógicas de diretório).
- **Comunicação Segura e Isolamento de Travamentos:** O subprocesso executa a varredura linear crua via `memmap2` e o parsing zero-copy do cabeçalho. O resultado normalizado é serializado em pacotes compactos binários (através do Apache Arrow FFI ou layouts idênticos de memória estruturados via rkyv) e transmitido de volta para o daemon principal por canais de comunicação IPC seguros (Named Pipes ou stdio).
- **Resiliência contra SIGBUS e OOM:** Se o subprocesso do parser malicioso tentar realizar alocações absurdas de heap ou se deparar com um truncamento de disco físico que dispara uma exceção SIGBUS, o colapso e morte imediata do processo ocorrem exclusivamente no espaço do processo filho. O orquestrador central do SODA intercepta a morte repentina do worker através do monitoramento do término do pipe, descartando o processo, emitindo alertas estruturados nos logs do sistema e mantendo o runtime do Tokio ativo e impenetrável.

## Conclusões e Recomendações de Engenharia

Para garantir que a varredura de diretórios e extração de metadados do Souls MC opere sob latência sub-milissegundo com total proteção cibernética contra anomalias e manipulações intencionais de arquivos, estabelecem-se as seguintes diretrizes arquiteturais estritas:

1. **Adotar o mapeamento de arquivos em memória virtual (`memmap2`)** para leitura de cabeçalhos de pesos (GGUF e Safetensors), garantindo que as operações de I/O em disco ocorram em tempo constante $\mathcal{O}(1)$ e cópia zero.
2. **Rejeitar o uso de parsers de representação pesados baseados em Serde** para o caminho de scan contínuo. O SODA padronizará o parsing do JSON extraído dos arquivos de modelo através de analisadores baseados estritamente em referências lógicas e zero-allocation (como `hifijson` e `jsode`), mantendo o uso de heap no limite mínimo absoluto.
3. **Implementar validação prévia de tamanhos e cabeçalhos binários** em relação aos metadados físicos fornecidos pelo sistema operacional sobre o tamanho real do arquivo, rejeitando qualquer parsing cujos offsets ou declarativas de contagem de tensores excedam os limites físicos reais da unidade de armazenamento NVMe.
4. **Isolar obrigatoriamente a esteira de scanner em subprocessos efêmeros** ou dentro de uma máquina virtual embutida WebAssembly (`Wasmtime` com consumo de combustível configurado), blindando o thread assíncrono principal do daemon do Tokio contra falhas de segmentação irrecuperáveis do tipo SIGBUS e picos de falhas por falta de memória do sistema operacional host.

---

Na verdade, estamos vivendo a transição do **WASI 0.2 (Preview 2)** para o **WASI 0.3 (Preview 3)**.

A escolha de manter o **WASI 0.2** como base estável ou migrar para o **0.3** é uma decisão crítica de design de sistemas. Abaixo, detalho o panorama técnico do que muda, por que o WASI 0.2 ainda é amplamente referenciado e os riscos reais de adotar as versões mais novas imediatamente.

### 1. O que é o WASI 0.2 (Preview 2)?

Lançado no início de 2024, o WASI 0.2 foi o marco divisor de águas que introduziu o **Component Model** (Modelo de Componentes) e a linguagem de definição de interface **WIT**. Ele substituiu as APIs legadas e instáveis do WASI Preview 1 (que pareciam apenas emulações POSIX de baixo nível) por uma abstração de tipos robusta, modular e segura.

É a **fundação estável de produção**. Praticamente todas as ferramentas comerciais e orquestradores de borda (Edge) em produção utilizam o WASI 0.2 como o contrato padrão e imutável.

### 2. O que muda no WASI 0.3 (Preview 3)?

O WASI 0.3 adiciona **assincronismo nativo** ao Modelo de Componentes.

- **O problema no WASI 0.2:** Para lidar com operações de Entrada/Saída (I/O) assíncronas, o WASI 0.2 usava uma infraestrutura complexa e "travada" de IO baseada em streams imperativas e polling manual (recursos do tipo `pollable` de `wasi:io`). Isso forçava os desenvolvedores a usar gambiarras para emular concorrência.
- **A solução no WASI 0.3:** Ele elimina essas streams complexas e introduz concorrência declarativa diretamente na ABI canônica do WebAssembly. Agora, o sistema utiliza tipos nativos como `future<T>` e `stream<T>`, permitindo que os componentes gerenciem I/O assíncrono nativamente, sem bloquear outros componentes e sem códigos redundantes de polling.

### 3. Existem problemas em usar as versões mais novas (WASI 0.3)?

Sim. Sob a ótica do **Pessimismo da Razão**, pular imediatamente para o WASI 0.3 introduz atritos severos no desenvolvimento do Souls MC (SODA):

#### A) A Armadilha de Dependências de Compilação (Dependency Hell)

As versões mais recentes do runtime `Wasmtime` e do `wasmtime-wasi` (que implementam o WASI 0.3) exigem compiladores Rust muito novos e atualizações agressivas na árvore de dependências do seu Workspace.

Isso frequentemente gera **incompatibilidades de traits e quebras físicas em compilações multiplataforma**. Um exemplo clássico é a quebra do crate de abstração de sistema de arquivos `cap-std`/`cap-primitives` no Windows quando compilado com versões recentes do compilador e runtimes WebAssembly modernos, o que pode inviabilizar a estabilidade do seu build bare-metal.

#### B) Suporte de Ecossistema Fragmentado

Embora runtimes líderes de baixo nível como o `Wasmtime` (a partir da versão 43+ / 26+) e ferramentas como o `Spin v3.5` já ofereçam suporte completo e estável ao WASI 0.3, a migração em provedores comerciais de borda e outras linguagens de programação ainda é desigual e lenta. Se o seu SO precisar carregar componentes que não sejam escritos estritamente em Rust ou JavaScript (que dominam o suporte ao 0.3), você enfrentará falhas severas de compatibilidade.

#### C) Threads e GPU ainda estão ausentes

Apesar de trazer um excelente modelo de concorrência assíncrona, o WASI 0.3 **ainda não resolve o paralelismo físico real** (a proposta de WASI Threads está em andamento, mas não está estabilizada no 0.3).

Além disso, **não há acesso padronizado à GPU** dentro da sandbox WASI. Portanto, para o seu "Mediator Broker" de GPU que gerencia as alocações na RTX 2060m, você ainda precisará delegar a orquestração pesada de hardware diretamente para threads dedicadas nativas do host em Rust, em vez de confiar no sandbox WebAssembly para tocar na VRAM.

### Veredito de Arquitetura para o Souls MC

1. **Para o Core do Sistema (Agora):** Utilize o **WASI 0.2**. Ele garante que o seu ambiente de sandboxing compile de forma determinística e limpa tanto em Windows quanto em Linux, protegendo o seu daemon contra instabilidades de APIs em rápida mutação.
2. **Preparação de Terreno para o WASI 0.3:** Desenhe as fronteiras dos seus agentes e as chamadas de sistema host de forma **grosseira e espaçada** (coarse-grained), reduzindo conversas excessivas e síncronas entre guest e host. Isso garantirá que, quando o ecossistema de compilação do WASI 0.3 estiver 100% amadurecido e integrado, a migração para o assincronismo nativo ocorra como um simples upgrade de versão, sem exigir que você reescreva a lógica do Souls MC.

---

Sim, a diferença prática entre o WASI 0.2 e o 0.3 reside em **como a concorrência e o fluxo de dados (I/O) são gerenciados no nível mais baixo da CPU e do sistema operacional**.

Para o cenário do Souls MC (SODA), existem **três cenários específicos** onde usar o WASI 0.3 é absurdamente superior ao WASI 0.2:

### 1. Ingestão de Dados e Tokens sem Buffering (Streaming Puro)

- **O Problema no WASI 0.2:** Se um agente em sandbox precisasse processar um arquivo grande (como uma base de dados de código para RAG ou a saída contínua de um LLM), ele tinha que lidar com _readiness-polling_ manual. O código precisava ler um pedaço do arquivo, pausar, perguntar ao sistema host "já tem mais bytes?", rodar uma máquina de estados síncrona e repetir. Isso gerava alto consumo de CPU e latência.
- **A Solução no WASI 0.3:** O tipo `stream<T>` passou a ser um cidadão de primeira classe na própria ABI canônica do WebAssembly. A API `wasi:io` foi removida, e o próprio runtime do Wasmtime gerencia o fluxo de bytes diretamente na memória. Para o seu agente, isso significa que você pode processar fatias de arquivos ou saídas de tokens em streaming à medida que chegam, de forma **totalmente assíncrona, não-bloqueante e com zero buffers temporários na heap** do sandbox.

### 2. Execução de Agentes Paralelos Cooperativos (Subtasks Sandboxed)

- **O Problema no WASI 0.2:** Se você quisesse que um agente fizesse duas coisas ao mesmo tempo dentro do sandbox (por exemplo, analisar a sintaxe de um código via _tree-sitter_ enquanto faz uma requisição local de rede), você não conseguia. O WASI 0.2 não suportava concorrência interna. O agente precisava terminar uma tarefa para começar a outra.
- **A Solução no WASI 0.3:** Foi introduzido o suporte para `wit_bindgen::spawn()`. Agora, o seu agente em Rust rodando dentro do Wasmtime pode disparar tarefas secundárias (_subtasks_) de forma nativa e paralela. O mais incrível: **o Host (Wasmtime) gerencia o loop de eventos único compartilhado**. Se a subtask $A$ estiver aguardando I/O, o runtime suspende apenas a subtask $A$ e executa a subtask $B$ instantaneamente, sem bloquear a thread do Tokio no seu Daemon do SODA.

### 3. I/O por Conclusão Bare-Metal (IOCP / io_uring nativo)

- **O Problema no WASI 0.2:** O modelo assíncrono era baseado em _readiness_ (estilo `epoll` do Linux). O sandbox precisava perguntar ativamente se o hardware estava "pronto" para ler ou gravar.
- **A Solução no WASI 0.3:** O modelo assíncrono passou a ser **completion-based (baseado em conclusão)**. Ele foi desenhado especificamente para se acoplar diretamente a APIs de alta performance do Kernel, como o **`io_uring` no Linux** e o **IOCP / `IoRing` no Windows**. Como o SODA roda bare-metal diretamente sobre o hardware, o WASI 0.3 permite que as chamadas de arquivos do sandbox conversem diretamente com as filas de conclusão do SSD NVMe, cortando context switches no nível do kernel e reduzindo a latência física de I/O para menos de um milissegundo.

### Resumo Prático para SODA (Onde usar cada um?)

| **Use WASI 0.2 para:**                                                                                                                                                                                                                   | **Use WASI 0.3 para:**                                                                                                                                                                     |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **O Scanner Local de Modelos:** Como ele é uma ferramenta linear de leitura $\mathcal{O}(1)$ que só precisa mapear cabeçalhos via `memmap2`, o WASI 0.2 é ideal porque você não precisa de concorrência ou rede dentro desse isolamento. | **Os Agentes Cognitivos Interativos:** Agentes que tomam decisões, geram código, se comunicam por rede (via sockets/HTTP) e precisam rodar múltiplos sub-processos paralelos de validação. |
| **Operações Síncronas Críticas:** Tarefas estritamente de arquivo que exigem estabilidade absoluta de API de produção hoje.                                                                                                              | **Pipelines de Processamento Complexos:** Agentes que filtram dados pesados de log em tempo real ou processam fluxos de áudio de voz sem latência.                                         |