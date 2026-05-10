**Objetivo da Pesquisa:** Realizar um _benchmarking_ e análise de prós e contras das tecnologias de vanguarda (Stack) necessárias para suportar uma Rede Social Hiperlocal baseada em Edge Computing, Provas de Conhecimento Zero (ZKP) no mobile, e roteamento geoespacial de alta frequência.

**Contexto do Sistema:** O "CommunitZ" precisa de um stack tecnológico que suporte: 1) Geração de provas criptográficas pesadas (ZKP) diretamente no celular do usuário (iOS/Android); 2) Cálculos de geofencing probabilístico em tempo real com milhões de pings; 3) Roteamento de mensagens/eventos e filas de dados assíncronas para moderação de shadowbanning; 4) Gateways de borda (Edge) capazes de aplicar rate-limiting geoespacial dinâmico.

**O que BUSCAMOS descobrir (Foco da Pesquisa Externa):**

- **Stack de ZKP Mobile (Prós e Contras):** Comparar o ecossistema atual proposto (Circom + snarkjs/Groth16) com alternativas modernas de geração de provas no cliente, como **Halo2, Noir, ou RISC Zero zkVM**. Qual oferece o melhor trade-off entre tamanho da prova, consumo de bateria do smartphone e facilidade de integração com React Native/Flutter?
- **Motor Geoespacial em Tempo Real:** Comparar a arquitetura híbrida de **PostGIS + Uber H3 no Redis** com bancos de dados espaciais _in-memory_ nativos como o **Tile38** ou **Tarantool**. Qual é a arquitetura mais barata e escalável para validar 100.000 usuários se movendo simultaneamente?
- **Edge Proxy e Defesa L7:** Analisar as vantagens e desvantagens de usar **Envoy Proxy + WebAssembly (Wasm em Rust)** versus **OpenResty (Nginx + Lua)** ou abordagens Serverless (como **Cloudflare Workers**). O que é mais sustentável para uma startup construir rate-limiting customizado baseado na distância do GPS?
- **Mensageria e Filas de Auditoria:** Comparar o clássico **Apache Kafka** com alternativas mais modernas e leves, como **Redpanda** ou **Apache Pulsar**, para gerenciar filas assíncronas de moderação (soft deletes) e logs imutáveis.

**Palavras-chave Sugeridas (Query Strings):**

- "Client-side ZKP generation benchmark mobile Circom vs Halo2 vs Noir"
- "Tile38 vs Redis H3 spatial index high concurrency"
- "Envoy Wasm vs OpenResty Lua custom rate limiting performance"
- "Redpanda vs Kafka architectural trade-offs startup"