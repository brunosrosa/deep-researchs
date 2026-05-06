---
aliases:
  - "Dicionário de Dados: Blueprint Arquitetural SODA ETL V3 - MASTER_SOLUTIONS_v3 (45 Colunas)"
---
# Dicionário de Dados: Blueprint Arquitetural SODA ETL V3 - MASTER_SOLUTIONS_v3 (45 Colunas)

Este blueprint define a estrutura de dados canônica para o ecossistema **SODA (Sovereign Operating Data Architecture)**. Como Curador Arquitetural, exijo obediência absoluta a este esquema. Operamos sob o **Pessimismo da Razão**: cada byte é planejado para sobreviver à falha catastrófica. O objetivo é a **Pureza do Silício** e a **Soberania de Dados**, exterminando qualquer vestígio de infraestrutura tóxica (Node.js, React, Electron).

## 1. Fundamentos da Taxonomia e Validação Pydantic

A SODA impõe uma tipagem forte entre o backend Rust (Tokio) e a camada de validação. Proibimos tipos dinâmicos que induzam ao _Context Rot_. A validação deve ser cirúrgica, utilizando Pydantic v2 com `strict=True`.

### Regras Globais de Integridade Silicon-Only

|   |   |   |
|---|---|---|
|Regra|Descrição Técnica|Restrição Pydantic|
|**Soberania Zero-None**|Campos críticos não admitem `None`.|`strict=True` + Tipagem estrita.|
|**Silicon Purity**|Bloqueio imediato de strings contendo "node_modules", "react" ou "webpack".|`@field_validator` mandatório.|
|**Aritmética Exata**|Timestamps devem usar aritmética de 86400s para evitar "Falsa Amnésia".|`Int64` (UNIX Epoch).|
|**VRAM Guard**|Alocações acima do limite físico da RTX 2060m (6GB) abortam o processo.|`Field(lt=6442450944)`.|

--------------------------------------------------------------------------------

## 2. Eixo 1: Metadados de Identidade e Roteamento (Colunas 01-09)

Sintetiza a identificação unívoca sob o protocolo **BMAD** (Branch, Mutate, Approve, Diff).

|   |   |   |   |   |
|---|---|---|---|---|
|ID|Nome da Coluna|Tipo (Rust/Py)|Descrição Técnica|Rationale (Porquê)|
|01|`record_id`|`UUID` / `str`|Identificador único universal.|Rastreabilidade determinística.|
|02|`agent_id`|`String` / `str`|ID do agente (conforme `gateway-config`).|Soberania de agência.|
|03|`task_uuid`|`UUID` / `str`|ID da tarefa (No Ticket, No Code).|Prevenção de **Ghost Coding**.|
|04|`branch_name`|`String` / `str`|Nome da branch no Shadow Workspace.|Isolamento via protocolo BMAD.|
|05|`routing_mode`|`IntEnum`|Modo de roteamento (Local/Cloud).|Eficiência FinOps bare-metal.|
|06|`source_protocol`|`String` / `str`|Protocolo (MCP, A2A, IPC).|Unificação de interface.|
|07|`trace_id`|`String` / `str`|ID de rastreio OpenTelemetry.|Observabilidade profunda.|
|08|`priority_level`|`u8` / `int`|Prioridade de execução no Tokio.|Gestão de CPU e afinidade de core.|
|09|`checksum_id`|`String` / `str`|SHA-256 do cabeçalho de identidade.|Integridade contra SDC.|

--------------------------------------------------------------------------------

## 3. Eixo 2: Memória e Estabilidade Taxonômica (Colunas 10-18)

Governa a persistência e a detecção de contradições, mitigando o **Flow-Debt**.

|   |   |   |   |   |
|---|---|---|---|---|
|ID|Nome da Coluna|Tipo (Rust/Py)|Descrição Técnica|Rationale (Porquê)|
|10|`stability_tag`|`IntEnum`|`STABLE` (0) ou `EVOLVING` (1).|Diferenciação de persistência.|
|11|`memory_tier`|`IntEnum`|L1 (Transac), L2 (Episod), L3 (Deep).|Hierarquia de latência.|
|12|`persistence_db`|`String`|`sqlite`, `lancedb` ou `ladybugdb`.|Especialização de motor Rust.|
|13|`created_at`|`Int64`|UNIX Epoch Int64 de criação.|Ordenação causal absoluta.|
|14|`valid_to`|`Int64`|Expiração via **Dinâmica de Langevin**.|Move "lixo" para o espaço hiperbólico.|
|15|`is_archived`|`Boolean`|Flag para `.archive/` local.|Higiene de Workspace.|
|16|`ontology_link`|`String`|Arestas para o LadybugDB.|Conectividade de grafos multi-hop.|
|17|`semantic_hash`|`String`|Vetor de similaridade via **FRQAD**.|Precisão Fisher-Rao em kNN.|
|18|`phronesis_idx`|`Float`|Índice de Phronesis (\Phi) em O(N \log N).|Exterminar contradições lógicas.|

--------------------------------------------------------------------------------

## 4. Eixo 3: Políticas de IA e Proteção de Prompt (Colunas 19-27)

Mapeamento de segurança e autorização via CEL (Common Expression Language).

|   |   |   |   |   |
|---|---|---|---|---|
|ID|Nome da Coluna|Tipo (Rust/Py)|Descrição Técnica|Rationale (Porquê)|
|19|`pguard_id`|`String`|ID da política `promptGuard` ativa.|Defesa contra injeção.|
|20|`jailbreak_scr`|`Float`|Score de detecção de ataque.|Segurança de resposta determinística.|
|21|`cel_filter_hit`|`Boolean`|Ex: `!has(mcp.tool) \| mcp.tool.name.matches('soda_.*')`.|Autorização L7 via CEL.|
|22|`safety_cat`|`String`|Categoria de risco (Hate, Violence).|Conformidade ética local.|
|23|`mcp_tool_auth`|`Boolean`|Permissão de execução para ferramenta MCP.|Sandboxing via Wasmtime/KVM.|
|24|`transform_l`|`String`|Lista de transformações aplicadas.|Enriquecimento de contexto.|
|25|`model_alias`|`String`|Alias do modelo para abstração.|Independência de provedor.|
|26|`cache_tokens`|`Integer`|Contagem de tokens de sistema cacheados.|Otimização FinOps.|
|27|`jailbreak_halt`|`Boolean`|Interrupção imediata via Blocklist.|Rejeição de prompt tóxico.|

--------------------------------------------------------------------------------

## 5. Eixo 4: Telemetria de Hardware e Performance (Colunas 28-36)

Monitoramento bare-metal focado na sobrevivência da RTX 2060m e i9.

|   |   |   |   |   |
|---|---|---|---|---|
|ID|Nome da Coluna|Tipo (Rust/Py)|Descrição Técnica|Rationale (Porquê)|
|28|`vram_bytes`|`u64`|Consumo real de VRAM.|Limite estrito de **6.442.450,944 bytes**.|
|29|`avx2_util`|`Boolean`|Uso de **SIMD/AVX2** no processador i9.|Throughput bare-metal máximo.|
|30|`pcie_latency`|`Float`|Latência do barramento PCIe em ms.|Detecção de gargalo de transferência.|
|31|`mmap_active`|`Boolean`|Uso de mmap via llama.cpp.|Eficiência de residência de memória.|
|32|`tensor_split`|`String`|**HybridGen (K-RAM / V-VRAM)**.|K em RAM (AVX2), V em VRAM.|
|33|`therm_throttle`|`Boolean`|Flag de estrangulamento térmico.|Integridade física do hardware.|
|34|`mmap_resident`|`u64`|Memória mapeada residente em KB.|Prevenção de OOM sistêmico.|
|35|`cpu_core_id`|`String`|ID do núcleo (Cluster Admin vs Comput).|Afinidade de core e MMCSS.|
|36|`vram_spillover`|`Boolean`|Transbordo de VRAM para RAM do sistema.|Alerta de colapso de performance.|

--------------------------------------------------------------------------------

## 6. Eixo 5: FinOps e Canibalização Cirúrgica (Colunas 37-45)

Poda de lixo tóxico e extração da "Alma Matemática".

|   |   |   |   |   |
|---|---|---|---|---|
|ID|Nome da Coluna|Tipo (Rust/Py)|Descrição Técnica|Rationale (Porquê)|
|37|`cost_usd`|`Float`|Custo real da transação IA.|Governança econômica.|
|38|`latency_ms`|`u32`|Latência total (IPC + Inferência).|UX Neuro-Inclusiva (50ms-150ms).|
|39|`cache_hit`|`Boolean`|Hit no cache de prompt.|Redução de custo O(1).|
|40|`cannibal_score`|`Float`|**Extração AST via** `**jcodemunch**`.|Proporção Alma Matemática vs Lixo.|
|41|`ghost_dep_det`|`Boolean`|**Poda Térmica (Thermal Pruning)**.|Exterminar resíduos de Node.js/Python.|
|42|`ralph_panic`|`Boolean`|Ativado após 3 falhas do Ralph Loop.|"Pessimismo da Razão" em ação.|
|43|`quant_level`|`String`|Nível (Q4_K_M ou Q8_0).|Balanço VRAM/Precisão.|
|44|`logic_density`|`Float`|Densidade semântica via AST.|Eficiência de extração cirúrgica.|
|45|`exit_code`|`Integer`|Exit Code da ferramenta de canibalização.|Sucesso determinístico.|

--------------------------------------------------------------------------------

## 7. Regras de Integridade e ENUMs Mandatórios

Utilize `IntEnum` para compatibilidade com o backend Rust e economia de memória.

- `**stability_tag**`: `0: STABLE`, `1: EVOLVING`.
- `**memory_tier**`: `0: L1_TRANSACIONAL`, `1: L2_EPISODICA`, `2: L3_DEEP`.
- `**routing_mode**`: `0: LOCAL_FIRST`, `1: CLOUD_FALLBACK`, `2: BATCH_ONLY`.
- `**optimization_lvl**`: `0: Q4_K_M` (Padrão), `1: Q8_0` (Máxima).

--------------------------------------------------------------------------------

## 8. Exemplo de Implementação Pydantic (SODA_ETL_Record)

```python
from pydantic import BaseModel, Field, field_validator
from enum import IntEnum

class RoutingMode(IntEnum):
    LOCAL_FIRST = 0
    CLOUD_FALLBACK = 1
    BATCH_ONLY = 2

class SODA_ETL_Record(BaseModel):
    record_id: str = Field(..., description="UUID gerado pelo Rust")
    agent_id: str = Field(..., pattern=r"^ag-.*")
    task_uuid: str = Field(..., description="Mandatório: No Ticket, No Code")
    vram_usage_bytes: int = Field(..., lt=6442450944) # Máximo 6GB RTX 2060m
    cannibal_score: float = Field(..., gt=0.85) # Exige 85% de pureza AST
    created_at: int = Field(..., description="UNIX Epoch Int64")
    
    model_config = {
        "strict": True,
        "frozen": True
    }

    @field_validator('agent_id')
    @classmethod
    def validate_silicon_sovereignty(cls, v: str) -> str:
        toxic_terms = ["node", "react", "npm", "electron", "nextjs"]
        if any(term in v.lower() for term in toxic_terms):
            raise ValueError("Infraestrutura tóxica detectada. Purga necessária.")
        return v
```

--------------------------------------------------------------------------------

## 9. Protocolo de Auditoria e Ralph Loop Kill-Switch

Para aniquilar a **Corrupção Silenciosa de Dados (SDC)**, o sistema impõe o seguinte protocolo de segurança:

1. **Validação SHA-256**: Se `checksum_id` (Col 09) falhar na comparação com o dado persistido, o **Ralph Loop** é engatilhado automaticamente.
2. **Iteração Limitada**: O Ralph Loop executará no máximo **3 tentativas** de autorrecuperação e correção sintática.
3. **Kill-Switch Crítico**: Caso a falha persista após a 3ª iteração, o sistema DEVE:
    - Emitir o evento `CRITICAL_DAEMON_PANIC`.
    - Interromper a inferência para proteger a VRAM.
    - Acionar o **Zombie UI Fallback**, salvando o estado atual no **IndexedDB** local e exigindo intervenção humana (HITL) imediata.

**Assinado:** _Curador Arquitetural SODA - Genesis MC_ _"Your machine, your rules. No garbage allowed."_