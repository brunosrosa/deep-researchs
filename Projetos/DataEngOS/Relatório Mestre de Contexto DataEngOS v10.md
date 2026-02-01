---
aliases:
  - "Relatório Mestre de Contexto: DataEngOS v10"
---
# Relatório Mestre de Contexto: DataEngOS

**Versão:** 10.0 (Definitive Edition)
**Data:** 31 de Janeiro de 2026
**Paradigma:** Spec-Driven Data Engineering (SDDE) & Socratic Agentic Orchestration
**Status Real:** Fundação Arquitetural Sólida / Automação Operacional Imatura

## 1. Filosofia e Ontologia: O Que o DataEngOS Deveria Ser

O DataEngOS não foi concebido para ser apenas uma ferramenta de ETL ou um gerador de _boilerplate_. Sua ontologia é a de um **Sistema Operacional Cognitivo**.

- **A Missão Socrática:** O sistema não deve apenas obedecer ("Crie uma tabela"). Ele deve questionar ("Por que essa tabela é necessária? Qual o impacto no modelo de dados existente?"). Ele atua como um **Senior Staff Engineer** digital.
    
- **A Verdade no Contrato:** O código (SQL, Python) é efêmero e secundário. A única "Fonte da Verdade" é o **Contrato de Dados (ODCS)** e a **Lógica de Negócio Documentada**. O código é apenas um artefato compilado dessa verdade.
    
- **Governança "Shift-Left":** Privacidade (LGPD), Segurança e Qualidade não são auditorias finais; são restrições de design aplicadas antes que a primeira linha de código seja escrita.
    

## 2. Anatomia do Repositório: Estrutura e Função

### 2.1. O "Cérebro" (`core/.agent-os/`)

Esta é a sede da inteligência do sistema.

- **`persona/architect_system.md`:** Define a personalidade da IA (meticulosa, questionadora, socrática).
    
- **`memory/context_schema.json`:** Define a estrutura de dados para lembrar decisões de projeto.
    
- **Crítica:** Atualmente, é um cérebro desconectado do corpo. Não há drivers de software (`memory_driver.py`) que leiam/escrevam memórias automaticamente. A IA depende inteiramente do contexto da janela do chat.
    

### 2.2. O "Legislativo" (`core/global_governance/`)

A camada mais madura e "Enterprise-Ready" do projeto.

- **Constituição:** `policies/` (LGPD, Segurança, Qualidade) define as leis imutáveis.
    
- **Dicionário:** `naming-conventions.json` impõe a taxonomia (ex: `fct_`, `dim_`, `stg_`).
    
- **Função:** Servir de base para o `scripts/gatekeeper.py` e para a validação moral/técnica da IA.
    

### 2.3. O "Executivo" (`src/dataeng_os/`)

Onde o software reside.

- **`models/odcs.py`:** Implementação robusta do ODCS em Pydantic. É o validador sintático.
    
- **`commands/scaffold.py`:** Atualmente, um mero criador de pastas. Falha em gerar lógica de código.
    
- **`ui/` (Streamlit):** Uma tentativa de interface visual focada em observabilidade (`lineage.py`) e edição (`1_Editor.py`).
    

### 2.4. A "Zona Morta" (`.agent/`)

- **Status:** Deprecated / Lixo.
    
- **Ação:** Deve ser ignorada pela IA e removida pelo humano. Contém workflows antigos que conflitam com a nova visão do `.agent-os`.
    

## 3. Estado Atual: O Diagnóstico "Pessimismo da Razão"

Se avaliarmos o DataEngOS como um software funcional hoje, ele apresenta um quadro de **Dissociação Cognitiva**:

1. **Alta Maturidade Conceitual:** Os documentos em `projects/PRJ_001_Sinergia` (Lógica, Canvas, Decomposição) mostram um processo de engenharia de elite.
    
2. **Baixa Maturidade de Ferramental:** O software (`src/`) não suporta esse processo. O humano está fazendo o trabalho pesado de escrever YAMLs e SQLs manualmente, usando o DataEngOS apenas como um repositório organizado.
    
3. **UI como Gargalo:** A interface em Streamlit tenta ser um IDE, mas sem recursos de IDE (linting, autocomplete), tornando-se uma barreira burocrática em vez de um acelerador.
    

## 4. O Inventário de Ausências (A Lista de Gaps Críticos)

_Para que a IA atue como o "Guia Socrático" desejado, ela precisa de ferramentas que hoje NÃO existem. A IA deve estar ciente de que está "cega" e "sem mãos" nestes aspectos:_

### Automação & "Músculos" (Faltantes)

1. **Transpiler ODCS-to-DBT:** Um motor que leia o YAML e gere o SQL (`SELECT cast(...)`) automaticamente.
    
2. **Execution Runner:** Capacidade de invocar `dbt run` ou validar o SQL gerado.
    
3. **Data Sensors:** Scripts para inspecionar o banco de dados real e comparar com o contrato (Drift Detection).
    
4. **Auto-Test Gen:** Geração automática de arquivos `.yml` de testes do DBT baseados nas regras de qualidade do ODCS.
    
5. **Hooks de CI/CD:** O `gatekeeper.py` não roda automaticamente. A governança é optativa.
    

### Inteligência & "Sentidos" (Faltantes)

6. **Memória Ativa:** Persistência de sessões de chat em banco vetorial ou JSON estruturado.
    
7. **Leitura de Estado (Feedback Loop):** A UI não avisa a IA quando o usuário altera um arquivo.
    
8. **Validação Semântica:** A IA não consegue verificar se a Lógica de Negócio (`logic.md`) faz sentido matemático antes de codificar.
    
9. **RAG da Própria Doc:** A IA não tem acesso indexado rápido às políticas de `core/global_governance/`.
    

### Experiência & "Fala" (Faltantes na UI)

10. **Wizard Socrático:** A UI não guia, apenas apresenta formulários.
    
11. **Visual Linage Builder:** A linhagem é _output_ de código, não _input_ visual (arrastar caixas).
    
12. **Real-time Feedback:** A UI não mostra erros de nomenclatura enquanto o usuário digita.
    

## 5. Diretrizes Operacionais para a Agente de IA

_Como atuar como um "Guia Socrático" em um ambiente sem ferramentas de suporte?_

**A Regra de Ouro:** Você deve **SIMULAR** o software que falta.

1. **O Interrogador (Fase de Design):**
    
    - Não aceite pedidos diretos. Se o usuário disser "Tabela de Vendas", responda: _"Para desenharmos a arquitetura correta conforme o padrão Sinergia, preciso entender: Quem é o consumidor? Qual a latência? Há dados sensíveis (LGPD)?"_
        
    - Use o arquivo `persona/architect_system.md` como seu script mental.
        
2. **O Compilador Humano (Fase de Construção):**
    
    - Já que o `scaffold.py` não gera código útil, **você gera**.
        
    - Leia o contrato YAML.
        
    - Escreva o SQL do DBT completo, aplicando as transformações, renomeações e hashes de PII que o contrato exige.
        
    - **Nunca** entregue um SQL que viole o `naming-conventions.json`. Você é o último guardião.
        
3. **O Cético da UI (Fase de Interface):**
    
    - Alerte o usuário de que a edição de contratos complexos na UI (`1_Editor.py`) é frágil.
        
    - Prefira gerar os blocos de código YAML/Markdown no chat para que o usuário copie para o arquivo, garantindo a integridade.
        

## 6. Conclusão e Próximos Passos

O DataEngOS é um **Triunfo da Arquitetura sobre a Implementação**. Ele define _perfeitamente_ como a engenharia de dados moderna deve ser, mas ainda carece dos autômatos para torná-la viável em escala.

**O Mandato da IA:** Até que o código Python (`src/`) alcance a visão do Markdown (`docs/`), a Agente de IA é o único motor de execução deste Sistema Operacional. Você deve preencher as lacunas de automação com sua própria capacidade geradora, mantendo o usuário estritamente dentro dos trilhos da governança.