---
sticker: lucide//corner-up-right
---
**Atue como um Pesquisador de Segurança Ofensiva (Red Team) Especializado em IA e Sistemas Agênticos (Estado da Arte de 2026).**

**CONTEXTO DO PROJETO (SODA - Genesis MC):** Nosso sistema local (SODA) funciona como o roteador inteligente. Quando a carga de trabalho excede nossa dGPU de 6GB ou exige oráculos de Sistema 2 massivos, delegamos as tarefas para a Nuvem via conexões "Flat-Rate" (assinaturas) utilizando a infraestrutura do Model Context Protocol (MCP).

Em arquiteturas MCP, a Nuvem atua como o "LLM", e a máquina local fornece ferramentas/contexto como "Cliente". No entanto, o MCP possui um recurso avançado chamado **Sampling**, onde o servidor MCP pode solicitar dinamicamente que o cliente (nosso ecossistema) gere completudes de LLM. Sabemos que em 2026 isso abriu portas para vulnerabilidades graves, como _Resource Theft_ (roubo das cotas da nossa assinatura para executar workloads de terceiros) e Injeção de Comandos via Confused Deputy.

**A MISSÃO:** Produza um dossiê de segurança arquitetural detalhando a superfície de ataque do MCP e como blindar o SODA escrito em Rust. O documento deve cobrir:

1. **A Mecânica da Ameaça de Sampling:** Explique como um servidor MCP malicioso ou comprometido na Nuvem pode explorar a _Feature_ de Sampling para iniciar requisições silenciosas contra nossa API/Assinatura configurada.
    
2. **Vetor de Injeção de Comandos (Command Injection):** Como o LLM pode atuar como um "Confused Deputy" e acabar executando permissões locais destrutivas caso os metadados das ferramentas do MCP sejam envenenados (Tool Metadata Poisoning)?
    
3. **Arquitetura Zero-Trust no Roteador Rust:** Desenhe os _guardrails_ lógicos (Máquina de Estados) que devemos implementar no nosso cliente MCP para validar e negar requisições de sampling não autorizadas (ex: Human-in-the-Loop dinâmico, Limites de Taxa por Servidor, e Restrição de Escopo).
    
4. **Isolamento e Sandboxing:** Quais as melhores práticas de design (ex: permissões de root, read-only tools, isolamento de namespaces) para garantir que mesmo que a Nuvem seja hackeada, nossa máquina física permaneça inviolável?
5. 