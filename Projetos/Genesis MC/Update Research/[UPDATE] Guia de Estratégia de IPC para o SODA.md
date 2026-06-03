# Guia de Estratégia de IPC para o SODA

Este documento define o padrão de comunicação entre agentes para o **SODA (Sovereign Operating Data Architecture)**, substituindo o conceito de `shared-mem-queue` por soluções robustas, seguras e de alta performance.

## 1. Visão Geral da Arquitetura

Para minimizar a superfície de erro dos seus agentes e garantir resiliência, adotamos uma estratégia de **separação de planos**:

- **Data Plane:** Focado em throughput e latência (tensores, buffers, estados densos).
- **Control Plane:** Focado em sinalização, resiliência e comando (mensagens, triggers, logs).

## 2. Seleção de Ferramentas

### A. O Core (Data Plane): `Promisqs`

[Link do Repositório: https://github.com/Fredrik-Reinholdsen/Promisqs](https://github.com/Fredrik-Reinholdsen/Promisqs "null")

- **Por que escolher:** É a escolha de máxima performance (sub-microssegundo). Integra nativamente com o ecossistema `zerocopy` do Rust, permitindo que os dados fluam entre processos sem serialização.
- **Uso Específico:** Transporte de tensores e buffers brutos de dados entre agentes de inferência e módulos de processamento pesado.
- **Trade-off:** Exige disciplina (o uso de `unsafe` para garantir `zerocopy`). Deve ser encapsulado em uma camada segura para que os agentes não tenham acesso direto à lógica de ponteiros.

### B. A Resiliência (Control Plane): `HewlettPackard/sharedq`

[Link do Repositório: https://github.com/HewlettPackard/sharedq](https://github.com/HewlettPackard/sharedq "null")

- **Por que escolher:** Utiliza **Unix Domain Sockets** para notificação de eventos. É extremamente resiliente: se um processo consumidor travar, o SO fecha o socket e limpa a conexão, evitando deadlocks comuns em sistemas de memória compartilhada puros.
- **Uso Específico:** Sinalização entre agentes ("Nova tarefa disponível", "Agente parado/pausado"), gerenciamento de fila de tarefas leves e comandos de controle.
- **Trade-off:** Ligeiramente mais lento que o `Promisqs` em latência pura, mas oferece a **segurança operacional** necessária para que o sistema não "trave" por erros de estado do agente.

### C. A Flexibilidade (Interoperabilidade): `anza-xyz/shaq`

[Link do Repositório: https://github.com/anza-xyz/shaq](https://github.com/anza-xyz/shaq "null")

- **Por que escolher:** Foco em **compatibilidade binária**. Se o SODA crescer para incluir agentes escritos em Go ou C++ para tarefas de sistema de baixo nível, o `shaq` é a garantia de que a fila será legível fora do ecossistema Rust.
- **Uso Específico:** Comunicação com módulos externos ao ecossistema SODA principal.
- **Trade-off:** Menos focado em `zerocopy` extremo que o `Promisqs`, porém mais fácil de manter em ambientes poliglotas.

## 3. Matriz de Decisão

|   |   |   |
|---|---|---|
|**Caso de Uso**|**Recomendação**|**Justificativa**|
|**Tensores/Buffer Massivo**|**`Promisqs`**|Zero-copy real; latência mínima.|
|**Sinalização de Tarefas**|**`HP/sharedq`**|Sockets garantem limpeza se o agente falhar.|
|**Integração Go/C/Rust**|**`shaq`**|Compatibilidade binária testada.|

## 4. Conclusão Técnica

Substituir o `shared-mem-queue` por estas três ferramentas não é uma fragmentação, mas um **aumento de maturidade**. Ao tratar o **Data Plane** com `Promisqs` e o **Control Plane** com `HP/sharedq`, você constrói um sistema que não apenas é rápido, mas que falha de maneira controlada e previsível, reduzindo a necessidade de intervenção humana (e de debug complexo) no futuro.