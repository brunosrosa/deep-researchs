# Dossiê Técnico de Engenharia: Protocolo de Usabilidade e Proxy Agnóstico Multi-Provedor (Linha 2.1)

## Abstração de Roteamento, Viscosidade de Sessão e Arquitetura Local-First (Tier 0 a Tier 2)

A sobrecarga decisória na seleção de modelos de Linguagem de Grande Porte (LLMs) representa uma barreira substancial para a adoção eficiente de inteligência artificial em fluxos de desenvolvimento moderno. No entanto, a ingênua alternância dinâmica de modelos a cada turno de uma mesma conversa introduz ineficiências financeiras e operacionais críticas devido à destruição do **Prompt Caching** nos provedores.

O proxy L7 local adota o paradigma de **Viscosidade de Sessão (_Sticky Routing_)** combinado a uma taxonomia por Tiers de capacidade e um fluxo de **Escalonamento Reativo por Falha (_Self-Correction Loop_)**.

### Viscosidade de Sessão (_Sticky Routing_) e Preservação de Prompt Caching

Os principais provedores de inferência em nuvem (como Anthropic, OpenAI, DeepSeek e Google) aplicam descontos financeiros expressivos (que variam de 50% a 90% no custo dos tokens de entrada) quando um prefixo de contexto é reutilizado na mesma infraestrutura.

Se um roteador dinâmico alterar o modelo de backend no meio de um fluxo de trabalho (por exemplo, enviando a primeira mensagem para o Gemini Flash e a segunda para o Llama-3 no Groq), a taxa de _Cache Miss_ será de 100%. Isso torna as chamadas subsequentes mais lentas e significativamente mais caras do que manter a sessão fixada no mesmo modelo.

```
[Cliente / Agente]
       │
       ▼
[Proxy L7 Rust (Tokio/Hyper)]
       │
       ├── (1) Possui session_id ativo? ─────► SIM ──► Mantém Modelo Atual (Sticky) ──► High Cache Hit
       │
       └── (2) Nova Tarefa / Primeira Chamada?
               │
               ├──► Classifica Tipo de Tarefa
               └──► Seleciona Tier de Entrada (Tier 0 / Tier 1 / Tier 2)
```

1. **Afinidade de Sessão (_Task-Consistent Binding_)**: O proxy intercepta cabeçalhos de sessão (ex: `X-Session-ID` ou `task_id`). A primeira requisição de um fluxo avalia e define o modelo de backend; todas as requisições subsequentes da mesma sessão permanecem fixadas no mesmo modelo para maximizar o reuso do cache.
2. **Escalonamento por Falha (Self-Correction Loop)**: O proxy só altera o modelo de uma sessão ativa se ocorrer um gatilho de escalonamento:
    - **Falha de Validação Sintática/Semântica**: A resposta gerada pelo modelo inicial falha em testes locais automatizados (ex: JSON malformatado em chamadas de ferramentas, erro de compilação de código ou falha em asserções de teste unitário).
    - **Requisição Explícita de Segunda Opinião**: O prompt do usuário ou o agente orquestrador solicita formalmente uma revisão de segurança ou arquitetura.
    - **Prompt Caching Penalty Factor**: O algoritmo de roteamento calcula se a migração para um modelo alternativo compensa financeiramente a perda imediata do desconto do cache do modelo atualmente quente.

### Taxonomia por Tiers e Orquestração de Sub-Agentes (SDD / Souls)

O proxy local organiza a capacidade computacional em três níveis hierárquicos, priorizando o processamento soberano e de custo zero na máquina do desenvolvedor (Tier 0) por meio de motores de inferência embarcados nativos:

|**Tier de Capacidade**|**Provedores e Motores**|**Função no Fluxo de Trabalho**|**Custo e Latência**|
|---|---|---|---|
|**Tier 0: Local Sovereignty**|Native Rust Bindings (`llama-cpp-2` Vanilla / `llama-cpp-2` TurboQuant) em CPU/GPU local.|Formatação JSON, checagens sintáticas, buscas vetoriais, preenchimento automático de código e execução de sub-agentes em fila de background.|Custo zero ($0,00); latência de rede nula; privacidade total.|
|**Tier 1: Cloud FAST**|Gemini 2.0 Flash, Groq Llama-3.3, DeepSeek Flash, GPT-4o-mini.|Leitura de contextos médios/grandes, geração rápida de componentes, resumo de documentação e testes unitários simples.|Custo mínimo ($0,05–$0,50 / 1M tokens); latência extremamente baixa.|
|**Tier 2: Cloud HEAVY**|Claude 3.5 Sonnet, GPT-4o, DeepSeek R1.|Planejamento arquitetural, engenharia de requisitos (Spec Driven Development - SDD), depuração complexa e raciocínio matemático.|Custo de fronteira ($2,50–$15,00 / 1M tokens); prioridade na precisão cognitiva.|

### Orquestração da Fila Local e Preempção por Live Chat

O uso de motores embarcados como `llama-cpp-2` exige um gerenciamento rigoroso de recursos do computador host. Descarregar um modelo da VRAM, carregar um novo e reprocessar o prefixo (_KV-cache prefill penalty_) consome um tempo considerável. Para evitar esse overhead desnecessário e garantir fluidez interativa, o Tier 0 adota um **Escalonador de Fila com Preempção**:

1. **Prioridade Absoluta ao Live Chat (Preempção Ativa)**: Se uma requisição de interatividade direta do usuário ("Live Chat") for iniciada, o proxy interrompe (_pausa_) imediatamente as tarefas assíncronas em execução na fila de background do Tier 0.
2. **Execução Retomada em Ociosidade**: As tarefas dos sub-agentes em background permanecem na fila de prioridade. Elas só voltam a ser processadas quando o proxy detecta que a sessão interativa foi pausada ou que o sistema host voltou ao estado ocioso.
3. **Prevenção de Swapping Frequente de Modelos**: O Tier 0 mantém um modelo local padrão (_Default Local Model_) residente na memória. Se uma tarefa de background solicitar um modelo local especializado diferente, ela não causa o _swap_ imediato. O proxy aplica um declínio de prioridade (_priority decay_) à tarefa, mantendo-a na fila até que uma janela de lote consolidada (_batch window_) justifique a troca do modelo na VRAM ("regra do vale-a-pena").

## Gerenciamento Dinâmico de Credenciais, Resiliência L7 e Proteção Local

O gerenciamento de credenciais em um proxy local agnóstico exige um modelo defensivo capaz de impor limites rígidos de gastos (_Hard Caps_), mitigar falhas de taxa de requisição (_Rate Limits_ HTTP 429) e proteger a máquina host contra explorações de rede local.

### Validação de Hard Caps e Persistência Resiliente contra Crashes

O OpenRouter disponibiliza o endpoint `GET /api/v1/auth/key` para auditoria de chaves e verificação de limites. A resposta fornece a estrutura do estado da credencial:

```JSON
{
  "data": {
    "label": "chave-producao-dev",
    "limit": 50.00,
    "usage": 12.45,
    "limit_remaining": 37.55,
    "is_provisioning_key": false,
    "is_free_tier": false,
    "rate_limit": {
      "requests": 10,
      "interval": "10s"
    }
  }
}
```

Para evitar que erros de processo ou reinicializações do sistema operacional percam o controle de gastos diários, o proxy em Rust implementa a seguinte estratégia de contabilidade:

1. **Persistência de Latência Sub-Milissegundo (WAL/`redb`)**: Em vez de manter dados apenas em memória volátil, o proxy utiliza uma engine de banco de dados chave-valor embarcada em Rust (`redb`). Cada requisição concluída grava o consumo atômico de tokens em um _Write-Ahead Log_ (WAL) no disco antes de retornar os bytes finais ao cliente.
2. **Sincronização Assíncrona de Background**: Um worker Tokio realiza consultas periódicas (a cada 60 segundos) ao endpoint `GET /api/v1/auth/key` para reconciliar o saldo local com a medição oficial do provedor.
3. **Interrupção Proativa (HTTP 402)**: Se o saldo remanescente atingir $0.00$ ou o limite definido pelo usuário no arquivo JSONC for ultrapassado, o proxy rejeita requisições locais imediatamente com o status HTTP 402 (_Payment Required_).

### Detecção de Bloqueio em Nível de IP e Backoff Exponencial

Ao empilhar chaves de API gratuitas (_Free-Tiers_), aggregators e provedores (como OpenRouter e Cloudflare) aplicam limites combinados por chave de API **e por endereço IP de origem**. Se o proxy rotacionar dezenas de chaves gratuitas a partir da mesma conexão residencial, o servidor upstream bloqueará o endereço IP por completo.

```Rust
use std::sync::atomic::{AtomicU32, Ordering};
use std::sync::Arc;
use tokio::time::{sleep, Duration};
use rand::Rng;

pub struct IpRateLimitDetector {
    consecutive_429_errors: Arc<AtomicU32>,
    threshold: u32,
}

impl IpRateLimitDetector {
    pub fn new(threshold: u32) -> Self {
        Self {
            consecutive_429_errors: Arc::new(AtomicU32::new(0)),
            threshold,
        }
    }

    pub fn record_response_status(&self, status_code: u16) -> bool {
        if status_code == 429 || status_code == 403 {
            let count = self.consecutive_429_errors.fetch_add(1, Ordering::SeqCst) + 1;
            return count >= self.threshold; // Retorna true se o IP foi bloqueado
        } else if status_code == 200 {
            self.consecutive_429_errors.store(0, Ordering::SeqCst);
        }
        false
    }
}
```

Se o `IpRateLimitDetector` registrar múltiplas falhas consecutivas de taxa de requisição em chaves distintas, o proxy suspende temporariamente o uso da rota gratuita e realiza um fallback automático para um modelo pago de baixíssimo custo (ex: Gemini Flash pago), prevenindo travamentos na aplicação cliente.

O tempo de espera para retentativas individuais de uma chave utiliza o algoritmo _Full Jitter Exponential Backoff_:

$$T_{\text{wait}} = \text{random}(0, \min(T_{\max}, T_{\text{base}} \times 2^n))$$

Onde $T_{\text{base}} = 100\,\text{ms}$ e $T_{\max} = 10\,\text{s}$.

### Defesa em Profundidade na Porta Local (`127.0.0.1`)

Escutar requisições locais na porta `127.0.0.1:8080` sem autenticação expõe o usuário a ataques Cross-Site Request Forgery (CSRF). Um site malicioso aberto no navegador poderia emitir chamadas `fetch('http://localhost:8080/v1/chat/completions')` e consumir créditos de API ou exfiltrar dados.

O servidor Hyper aplica duas camadas obrigatórias de proteção:

1. **Validação Estrita de Cabeçalho `Origin`**: Requisições contendo cabeçalhos `Origin` ou `Referer` associados a navegadores web externos são sumariamente rejeitadas.
2. **Token de Autenticação Loopback**: Na inicialização, o binário Rust gera um token aleatório em memória. Conexões vindas de ferramentas locais (VS Code, Cursor, scripts) devem fornecer esse token no cabeçalho `Authorization: Bearer <LOCAL_LOOPBACK_TOKEN>`.

## Presets Inteligentes e Configuração JSONC Autodocumentada

O arquivo de configuração `config.jsonc` atua como a única fonte de verdade (_Single Source of Truth_) para o proxy L7 local. Ele declara os bindings locais do `llama-cpp-2`, os provedores em nuvem, os orçamentos, as regras de viscosidade de rota e as políticas de PII.

### Especificação Autodocumentada do Arquivo `config.jsonc`

```
{
  "//": "=================================================================",
  "// CONFIGURAÇÃO UNIFICADA DO PROXY L7 LOCAL (RUST / TOKIO / HYPER)",
  "//=================================================================",

  "server": {
    "host": "127.0.0.1",
    "port": 8080,
    "workers": 4,
    "loopback_token": "auto-generated-or-user-defined-token-here"
  },

  "//": "MOTORES EMBARCADOS LOCAIS (TIER 0) E PROVEDORES EM NUVEM (BYOK)",
  "providers": {
    "local_llamacpp_vanilla": {
      "tier": "Tier0_Local_Native",
      "engine": "llama-cpp-2-bindings",
      "model_path": "./models/llama-3.2-3b-instruct-q4_k_m.gguf",
      "context_size": 8192,
      "turbo_quant": false
    },
    "local_llamacpp_turboquant": {
      "tier": "Tier0_Local_TurboQuant",
      "engine": "llama-cpp-2-turboquant",
      "model_path": "./models/qwen-2.5-coder-7b-tq.gguf",
      "context_size": 16384,
      "turbo_quant": true
    },
    "openrouter": {
      "tier": "Cloud_Aggregator",
      "api_base": "https://openrouter.ai/api/v1",
      "keys": [
        "sk-or-v1-key-principal-usuario-1"
      ]
    }
  },

  "//": "CONTROLE DE ORÇAMENTO E PERSISTÊNCIA EM DISCO",
  "budget_control": {
    "global_hard_cap_usd": 25.00,
    "storage_engine": "RedbEmbeddedWAL",
    "sync_interval_seconds": 60,
    "action_on_exceed": "RejectWith402"
  },

  "//": "MAPEAMENTO DE ROTAS VIRTUAIS E ESCALONAMENTO DE FILA",
  "virtual_routes": {
    "fast_worker_endpoint": {
      "description": "Rota otimizada para latência e custo zero. Prioridade para llama-cpp-2 local.",
      "sticky_routing": true,
      "primary_candidate_models": [
        "local_llamacpp_vanilla",
        "google/gemini-2.0-flash-exp:free",
        "meta-llama/llama-3.3-70b-instruct:free"
      ],
      "escalation_target": "heavy_brain_endpoint"
    },
    "heavy_brain_endpoint": {
      "description": "Rota para raciocínio complexo, planejamento SDD e arquitetura.",
      "sticky_routing": true,
      "primary_candidate_models": [
        "anthropic/claude-3-5-sonnet",
        "openai/gpt-4o",
        "deepseek/deepseek-r1"
      ]
    }
  },

  "//": "HIGIENIZAÇÃO LOCAL DE DADOS (PII REDACTION INBOUND-ONLY)",
  "compliance_hygiene": {
    "enable_pii_redaction": false,
    "mode": "InboundPromptOnly",
    "builtin_categories": [
      "CPF_BR",
      "EMAIL_ADDRESS",
      "BEARER_TOKEN"
    ],
    "replacement_strategy": "ReversibleTokenMap"
  }
}
```

## Higienização Local de Dados: Trade-offs de Streaming e Estratégia Inbound

A remoção de dados pessoais e sensíveis (PII Redaction) apresenta trade-offs severos de desempenho e qualidade de geração quando aplicada de forma cega.

### Análise Crítica dos Trade-offs do PII Redaction

1. **Degradação do Contexto da IA**: Substituir termos por tags genéricas reduz a riqueza do espaço vetorial do modelo. O LLM pode perder a coesão gramatical ou tentar prever o dado omitido.
2. **Destruição do TTFT (_Time To First Token_)**: Processar respostas em tempo real sobre _Server-Sent Events_ (SSE) exige reter chunks em _Sliding Windows_ para verificar se palavras divididas na fronteira de rede formam dados sensíveis. Isso introduz latência visível e elimina a fluidez da interface.
3. **Falsos Positivos em Código**: Em tarefas de desenvolvimento, filtros agressivos podem mascarar hashes de commits, IPs locais (`127.0.0.1`) ou chaves sintéticas de teste necessárias para a depuração.

### Arquitetura Equilibrada: Inbound-Only com Mapeamento Reversível

Para mitigar esses trade-offs, a higienização de dados adota três diretrizes de engenharia:

1. **Configuração Desativada por Padrão (_Opt-In_)**: O recurso é fornecido como `"enable_pii_redaction": false` no arquivo JSONC, sendo ativado apenas em ambientes corporativos restritos.
2. **Higienização Exclusiva de Entrada (_Inbound-Only_)**: O autômato Aho-Corasick em Rust varre **apenas o prompt de envio estático** antes da serialização para a rede. O stream de resposta (_Outbound SSE Stream_) não é interceptado, preservando o TTFT original do provedor.
3. **Mapeamento Reversível de Tokens (_Token Rehydration_)**: Em vez de substituir um dado por `[REDACTED]`, o proxy substitui o termo por um identificador neutro mantido em uma tabela temporária em memória (`"123.456.789-00"` $\rightarrow$ `"VAR_PII_1"`). Quando o modelo responde mencionando `"VAR_PII_1"`, o proxy reidrata o valor original de volta antes de entregar ao cliente.

```Rust
use aho_corasick::AhoCorasick;
use std::collections::HashMap;

pub struct ReversibleSanitizer {
    automa: AhoCorasick,
    patterns: Vec<String>,
}

impl ReversibleSanitizer {
    pub fn new(patterns: Vec<String>) -> Self {
        let automa = AhoCorasick::builder()
            .ascii_case_insensitive(true)
            .build(&patterns)
            .unwrap();
        Self { automa, patterns }
    }

    pub fn sanitize_inbound(&self, prompt: &str) -> (String, HashMap<String, String>) {
        let mut map = HashMap::new();
        let mut sanitized_text = prompt.to_string();

        for (idx, mat) in self.automa.find_iter(prompt).enumerate() {
            let original_val = &prompt[mat.start()..mat.end()];
            let token_var = format!("VAR_PII_{}", idx);
            map.insert(token_var.clone(), original_val.to_string());
            sanitized_text = sanitized_text.replace(original_val, &token_var);
        }

        (sanitized_text, map)
    }
}
```

## Interface Tátil e Dashboard Reativo em Svelte 5 (Comunicação Assíncrona)

O frontend é desenvolvido em **Svelte 5**, utilizando a nova arquitetura baseada em **Runas** (`$state`, `$derived`, `$effect`) para prover reatividade universal extremamente leve.

### Comunicação Assíncrona via Eventos de Transmissão

A interface do usuário não precisa nem deve processar o streaming de tokens em tempo real do backend L7. As métricas do dashboard (gasto acumulado, latência e status do circuito) mudam pontualmente na conclusão de cada requisição.

O backend Tokio disponibiliza um endpoint de eventos assíncronos (`/api/internal/events`) utilizando canais de broadcast em memória (`tokio::sync::broadcast`). O frontend Svelte 5 consome essas notificações via um único _Server-Sent Event_ (SSE) interno e atualiza as Runas reativas.

```TypeScript
// src/lib/stores/gateway.svelte.ts
export interface GatewayMetrics {
  totalSpendTodayUsd: number;
  activeModelFast: string;
  activeModelHeavy: string;
  isLocalTierOnline: boolean;
}

class GatewayStore {
  metrics = $state<GatewayMetrics>({
    totalSpendTodayUsd: 0.0,
    activeModelFast: 'llama-cpp-2:vanilla',
    activeModelHeavy: 'anthropic/claude-3-5-sonnet',
    isLocalTierOnline: true
  });

  budgetLimit = $state<number>(25.00);

  budgetPercentage = $derived(
    (this.metrics.totalSpendTodayUsd / this.budgetLimit) * 100
  );

  constructor() {
    this.initEventStream();
  }

  private initEventStream() {
    if (typeof window === 'undefined') return;
    const eventSource = new EventSource('/api/internal/events');

    eventSource.onmessage = (event) => {
      const data = JSON.parse(event.data);
      if (data.type === 'METRICS_UPDATE') {
        this.metrics.totalSpendTodayUsd = data.totalSpendTodayUsd;
        this.metrics.isLocalTierOnline = data.isLocalTierOnline;
      }
    };
  }
}

export const gatewayStore = new GatewayStore();
```

### Componente de Painel Tátil em Svelte 5

```Svelte
<!-- src/lib/components/TactileControlPanel.svelte -->
<script lang="ts">
  import { gatewayStore } from '$lib/stores/gateway.svelte';

  let spendFormatted = $derived(`$${gatewayStore.metrics.totalSpendTodayUsd.toFixed(2)}`);
  let usagePercentFormatted = $derived(`${gatewayStore.budgetPercentage.toFixed(1)}%`);
</script>

<div class="p-6 bg-slate-900 text-white rounded-xl shadow-2xl border border-slate-800">
  <header class="flex justify-between items-center mb-6">
    <div>
      <h2 class="text-xl font-bold tracking-tight">Proxy L7 Control Dashboard</h2>
      <p class="text-xs text-slate-400">Localhost Engine (Tokio/Hyper) | Svelte 5 Runes</p>
    </div>
    <div class="text-right">
      <span class="text-2xl font-mono font-bold text-emerald-400">{spendFormatted}</span>
      <span class="text-xs block text-slate-500">Gasto Total ({usagePercentFormatted})</span>
    </div>
  </header>

  <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
    <!-- Fast Worker Card -->
    <div class="bg-slate-800/60 p-4 rounded-lg border border-slate-700/50 flex flex-col justify-between">
      <div>
        <div class="flex justify-between items-center mb-2">
          <span class="text-xs font-semibold uppercase text-cyan-400">Fast Worker Route</span>
          {#if gatewayStore.metrics.isLocalTierOnline}
            <span class="text-[10px] bg-emerald-500/20 text-emerald-300 px-2 py-0.5 rounded-full border border-emerald-500/30">llama-cpp-2 Active</span>
          {/if}
        </div>
        <p class="text-sm font-mono text-slate-200 truncate">{gatewayStore.metrics.activeModelFast}</p>
      </div>

      <button
        onclick={() => alert('Alternando rota Fast')}
        class="mt-4 w-full bg-cyan-600 hover:bg-cyan-500 active:scale-[0.98] transition-all py-2 rounded-md text-xs font-semibold"
      >
        1-Clique: Alternar Modelo Fast
      </button>
    </div>

    <!-- Heavy Brain Card -->
    <div class="bg-slate-800/60 p-4 rounded-lg border border-slate-700/50 flex flex-col justify-between">
      <div>
        <div class="flex justify-between items-center mb-2">
          <span class="text-xs font-semibold uppercase text-purple-400">Heavy Brain Route</span>
          <span class="text-[10px] bg-purple-500/20 text-purple-300 px-2 py-0.5 rounded-full border border-purple-500/30">Tier 2 Frontier</span>
        </div>
        <p class="text-sm font-mono text-slate-200 truncate">{gatewayStore.metrics.activeModelHeavy}</p>
      </div>

      <button
        onclick={() => alert('Forçando Claude 3.5 Sonnet')}
        class="mt-4 w-full bg-purple-600 hover:bg-purple-500 active:scale-[0.98] transition-all py-2 rounded-md text-xs font-semibold"
      >
        1-Clique: Forçar Claude 3.5 Sonnet
      </button>
    </div>
  </div>
</div>
```

## Análise de Trade-offs de Segunda Ordem e Estratégias de Mitigação

Ao resolver os gargalos primários de infraestrutura L7, emergem **trade-offs de segunda ordem** — conflitos resultantes das próprias soluções arquiteturais adotadas. A tabela e o detalhamento a seguir formalizam o tratamento dessas variáveis no proxy:

### 1. Viscosidade Rígida (_Sticky Routing_) vs. Degradação Silenciosa de Serviço

- **O Conflito**: O _Sticky Routing_ preserva os descontos de _Prompt Caching_ fixando a sessão no mesmo modelo. Se esse modelo upstream entrar em degradação silenciosa (ex: o _Time To First Token_ saltar de $200\,\text{ms}$ para $18\,\text{s}$), o usuário ficará retido em um endpoint lento.
- **Mitigação**: **Circuit Breaker de Latência Adaptativa**. O proxy monitora continuamente o TTFT da sessão. Se o tempo de resposta do endpoint retido ultrapassar $5\,\text{s}$, o proxy realiza o _un-stick_ temporário da sessão, envia um evento leve para a UI e direciona a requisição para um fallback de menor latência.

### 2. Prioridade do Tier 0 (`llama-cpp-2`) vs. Overhead de Swapping de Modelos e Carga do Host

- **O Conflito**: Carregar modelos locais diretamente via bindings C/C++ (`llama-cpp-2`) economiza custos ($0,00), mas trocar modelos na VRAM exige descarregamento, carregamento e reprocessamento do prefixo de contexto (_KV-cache prefill penalty_), além de poder canibalizar a VRAM/CPU do computador do usuário durante tarefas de background.
- **Mitigação**: **Fila com Preempção por Live Chat e Regra do "Vale-a-Pena"**. Sessões de chat interativo possuem prioridade absoluta e pausam a fila de background. Trocas de modelos locais especializados só ocorrem em janelas de lote quando o acúmulo de tarefas na fila justifica o tempo de descarregamento da VRAM; caso contrário, as tarefas executam sob o modelo padrão residente.

### 3. Reidratação de PII (_Token Map_) vs. Mutilação do Tokenizer pelo LLM

- **O Conflito**: Substituir dados sensíveis por variáveis no prompt (`"123.456.789-00"` $\rightarrow$ `"VAR_PII_0"`) e reidratá-los na resposta falha se o LLM alterar a caixa ou a estrutura da variável (ex: respondendo `var_pii_0` ou `VAR _ PII _ 0`), deixando a variável exposta no texto final.
- **Mitigação**: **Âncoras de Texto Natural e Matcher Fuzzy**. O proxy utiliza identificadores com palavras naturais de dicionário em caixa alta (ex: `EmpresaAlfa`) combinados com busca por tolerância sintática (distância de Levenshtein) no retorno do texto.

### 4. Escrita Síncrona em Disco (`redb`/WAL) vs. Latência de I/O e Desgaste de SSD

- **O Conflito**: Gravar cada token consumido no disco via _Write-Ahead Logging_ para resistir a crashes introduz latência de escrita (`fsync`) e desgaste contínuo no SSD em workloads intensos de sub-agentes.
- **Mitigação**: **Group Commit Assíncrono em Lotes**. A validação de orçamento utiliza contadores atômicos instantâneos na memória (`AtomicU64`), enquanto um worker Tokio realiza chamadas de gravação em lote em disco a cada $200\,\text{ms}$ ou 50 transações acumuladas.

### 5. Sanitização de Schemas JSON vs. Divergência de Dialetos de Tool Calling

- **O Conflito**: Ajustar schemas JSON de chamadas de ferramentas (_Tool Calling_) para evitar erros HTTP 400 pode remover parâmetros válidos exigidos por provedores específicos (como `additionalProperties` na OpenAI ou `anyOf` na Anthropic).
- **Mitigação**: **Adaptadores de Schema Específicos por Provedor**. O pipeline Rust aplica transformadores de schema dedicados ao dialeto exato da API de destino (`OpenAiAdapter`, `AnthropicAdapter`, `GeminiAdapter`).

## Síntese Arquitetural Ampliada e Matriz de Decisões de Engenharia

|**Componente da Arquitetura**|**Decisão de Design Atualizada**|**Justificativa de Engenharia e Usabilidade**|
|---|---|---|
|**Viscosidade de Sessão**|_Sticky Routing_ por `session_id` com retenção de _Prompt Caching_.|Preserva até 90% de desconto em tokens de entrada e reduz a latência de sessões contínuas.|
|**Hierarquia de Roteamento**|Taxonomia de 3 Tiers (Tier 0 Native `llama-cpp-2`, Tier 1 Cloud FAST, Tier 2 Cloud HEAVY).|Prioriza computação nativa local ($0,00) com variantes _vanilla_ e _TurboQuant_.|
|**Orquestração Local**|Fila de prioridade com preempção por Live Chat e otimização de _swap_ de VRAM.|Garante interatividade instantânea pausando sub-agentes de background e evita descarregamentos desnecessários de modelos.|
|**Persistência de Dados**|Gravador de logs atômicos via `redb` (WAL) com _Group Commit_ assíncrono.|Protege o histórico de gastos contra crashes sem introduzir latência de I/O por requisição.|
|**Segurança em Localhost**|Validação de `Origin` e token de autorização loopback.|Impede ataques de CSRF/CORS originados em navegadores sem impactar clientes locais.|
|**Resiliência Free-Tier**|Detecção de limite de taxa por IP (_IP-Level Rate Limit Detector_).|Evita bloqueios residenciais e faz fallback automático para rotas pagas de baixo custo.|
|**Higienização de PII**|Redação exclusiva em prompts estáticos (_Inbound-Only_) com mapa de tokens reversível.|Protege dados sensíveis na CPU sem degradar o TTFT de streaming no retorno do modelo.|
|**Sincronização com UI**|Notificações assíncronas por SSE interno integradas às Runas do Svelte 5.|Mantém o painel reativo atualizado no encerramento de cada requisição sem overhead de CPU.|
