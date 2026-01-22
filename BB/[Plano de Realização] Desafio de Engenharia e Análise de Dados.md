# Plano de Realização do Desafio de Engenharia e Análise de Dados

## Sumário Executivo

Este documento apresenta um plano de ação estratégico e detalhado para a execução de um projeto de engenharia e análise de dados de ponta a ponta. O plano foi elaborado considerando as especificidades do desafio: um conjunto de cinco arquivos CSV de volume moderado (< 50 mil linhas) e de natureza estática, ou seja, sem previsão de atualizações futuras.

O foco do plano transcende a mera execução técnica, priorizando a **reprodutibilidade, a clareza da modelagem e a comunicação de alto impacto dos resultados**. A metodologia proposta guiará a equipe através de cinco fases distintas, desde a investigação inicial dos dados brutos até a entrega de uma análise final convincente, garantindo que cada etapa seja executada com rigor e excelência. A abordagem de modelagem recomendada é o **Star Schema**, não por uma necessidade de performance — dado o volume dos dados —, mas como uma decisão estratégica para garantir a organização, a clareza conceitual e a adesão às melhores práticas da indústria de Business Intelligence.
## Fase 1: Descoberta e Documentação (O Diagnóstico "AS-IS")

Esta fase é o alicerce de todo o projeto. O objetivo é realizar um diagnóstico completo do estado bruto dos dados, sem realizar qualquer modificação, para entender profundamente os desafios e oportunidades que eles apresentam.

### Seção 1.1: Análise Exploratória de Dados (AED) – O Processo Investigativo

A AED é o primeiro contato com os dados, onde atuamos como detetives para documentar cada detalhe da "cena" original. 1

**O que se espera documentar para cada CSV:**

- **Estrutura e Volume:** Dimensões (linhas x colunas), tipos de dados de cada coluna e nomes exatos das colunas. 1
    
- **Qualidade dos Dados:** Quantidade e percentual de valores ausentes, existência de registros duplicados e identificação de outliers ou valores implausíveis. 3
    
- **Conteúdo e Semântica:** Cardinalidade (valores únicos), distribuição de frequência de categorias (para identificar inconsistências de texto) e distribuição estatística de valores numéricos. 1
    
- **Hipóteses de Relacionamento:** Identificação de colunas que parecem ser chaves primárias (identificadores únicos) e chaves estrangeiras (que conectam a outras tabelas). 1
    
#### **Entregável da Fase 1.1**:

Um Jupyter Notebook (.ipynb) por arquivo (ou um único notebook bem seccionado) que narra a investigação. Este documento deve conter não apenas o código Python (usando Pandas, Matplotlib, Seaborn) para as verificações, mas também células de texto em Markdown com as conclusões e observações após cada análise. Este notebook é a prova documental que justifica cada passo do processo de limpeza futuro. 4
### Seção 1.2: Mapeando o Terreno – A Visão "AS-IS"

Com base nas descobertas da AED, o próximo passo é criar uma representação visual da arquitetura de dados atual.

- **Processo:** Utilizando os arquivos CSV **como estão, sem nenhum tratamento**, desenhe um Diagrama de Entidade-Relacionamento (DER) que mostre as tabelas e os relacionamentos que você hipotetizou.
    
- **Propósito:** Este diagrama "AS-IS" é a sua fotografia do problema. Ele será usado na apresentação final para contextualizar o desafio e demonstrar o valor da solução que você construiu.
    
### Seção 1.3: O Inventário Inicial – Dicionário de Dados "AS-IS"

Este dicionário é o inventário formal dos seus dados brutos, um produto direto da AED.

- **Propósito:** Documentar de forma tabular cada coluna de cada arquivo em seu estado original, incluindo todos os problemas identificados.
    
- **Estrutura Recomendada:**
    
| Arquivo          | Coluna Original      | Tipo Inferido | % Nulos | Exemplo de Valores             | Observações da AED                                            |
| ---------------- | -------------------- | ------------- | ------- | ------------------------------ | ------------------------------------------------------------- |
| `usuarios.csv`   | `academic_formation` | `object`      | 15%     | `'Eng. Comp.'`, `'engenharia'` | Inconsistência de texto. Necessita padronização.              |
| `matriculas.csv` | `course_date`        | `object`      | 2%      | `'05-20-2024'`, `'21/05/2024'` | Formatos de data misturados. Precisa converter para datetime. |

## Fase 2: Desenho da Solução (O Blueprint "TO-BE")

Com o diagnóstico completo em mãos, esta fase é dedicada a projetar a solução ideal. O planejamento cuidadoso aqui economiza tempo e evita retrabalho na fase de implementação.

### Seção 2.1: A Decisão Estratégica de Modelagem

Para um projeto de análise de dados, a escolha da estrutura final é crucial. Dadas as características do seu desafio, a discussão se concentra em duas abordagens principais:

- **Tabela Plana (Flat File / One Big Table):** Consiste em unir todos os dados em uma única e grande tabela.
    
    - **Quando considerar:** Em cenários com dados de baixo volume como o seu, a diferença de performance de consulta para um Star Schema é praticamente insignificante. 5 A simplicidade de ter uma única tabela pode ser atraente para análises rápidas e diretas.
        
    - **Desvantagens:** Torna a manutenção da lógica mais complexa, é menos intuitiva para navegação e não segue as melhores práticas para ferramentas de BI, o que pode dificultar a criação de cálculos e a escalabilidade da análise. 6
        
- **Star Schema (Esquema Estrela):** Organiza os dados em tabelas de **Fatos** (eventos, métricas, como as matrículas) e **Dimensões** (entidades descritivas, como usuários, cursos, tempo).
    
    - **Quando é a melhor opção:** É a abordagem padrão da indústria para Business Intelligence e análise de dados. 8 Mesmo com dados de baixo volume, os benefícios são imensos:
        
        1. **Clareza e Organização:** O modelo é intuitivo e espelha a lógica de negócio ("analisar matrículas _por_ aluno e _por_ curso"). 8
            
        2. **Manutenção e Escalabilidade:** Facilita a adição de novos atributos ou métricas sem quebrar a estrutura. 6
            
        3. **Otimização para Ferramentas de BI:** É a estrutura para a qual ferramentas como o Power BI são otimizadas, simplificando a criação de relacionamentos, medidas DAX e visuais interativos. 8
            

**Conclusão para este Desafio:** A recomendação é a adoção do **Star Schema**. A decisão não se baseia em performance, mas em **qualidade, organização, clareza e adesão às melhores práticas do mercado**, o que constitui um critério de excelência.
### Seção 2.2: O Plano de Construção – Dicionário de Dados "TO-BE"

Antes de escrever qualquer código de transformação, você deve documentar o seu alvo. O dicionário "TO-BE" é o blueprint da sua nova arquitetura de dados.

- **Propósito:** Descrever a estrutura final, limpa e padronizada que você irá construir.
    
- **Estrutura Recomendada:**
    

| Tabela Final      | Coluna Final         | Tipo Final | Descrição de Negócio                                    |
| ----------------- | -------------------- | ---------- | ------------------------------------------------------- |
| `dim_usuarios`    | `usuario_key`        | `int32`    | Identificador único para cada usuário.                  |
| `dim_usuarios`    | `formacao_academica` | `string`   | Formação acadêmica padronizada do usuário.              |
| `fact_matriculas` | `dias_para_concluir` | `int32`    | Número de dias que o aluno levou para concluir o curso. |

### Seção 2.3: Ferramentas de Modelagem Visual

Para criar os diagramas "AS-IS" e "TO-BE", você pode usar duas abordagens complementares:

- **Draw.io (Abordagem Visual):** Ideal para brainstorming e colaboração. É uma ferramenta de arrastar e soltar que permite criar DERs de forma rápida e intuitiva.
    
- **Mermaid.js (Abordagem "Código como Documentação"):** Perfeito para a documentação final dentro do seu repositório GitLab. Você escreve uma sintaxe simples em um bloco de código Markdown, e o GitLab renderiza o diagrama. Isso garante que sua arquitetura de dados seja versionada junto com seu código.
    

**Exemplo de Star Schema em Mermaid.js:**

Snippet de código

```
erDiagram
    fact_matriculas {
        int matricula_key PK
        int usuario_key FK
        int curso_key FK
        date data_matricula_key FK
    }

    dim_usuarios {
        int usuario_key PK
        string nome_usuario
        string formacao
    }

    dim_cursos {
        int curso_key PK
        string nome_curso
        string categoria
    }

    fact_matriculas |

|--o{ dim_usuarios : "pertence a"
    fact_matriculas |

|--o{ dim_cursos : "refere-se a"
```

## Fase 3: Implementação do ETL (A Linha de Montagem)

Com o blueprint "TO-BE" definido, esta fase é a execução. Como os dados são estáticos, o foco é criar um **script único e reprodutível**.

- **Processo:** Desenvolver um script Python (`.py`) ou um notebook Jupyter (`.ipynb`) que execute todas as etapas de limpeza e transformação de forma sequencial. 4
    
- **Técnicas de Limpeza:** O script deve aplicar as correções identificadas na AED, como:
    
    - Tratamento de valores ausentes (preenchendo com 'Não Informado' ou removendo a linha, com justificativa). 11
        
    - Padronização de texto (`.str.lower()`, `.str.strip()`). 11
        
    - Correção de tipos de dados (especialmente `pd.to_datetime` para datas). 11
        
    - Remoção de duplicatas. 11
        
- **Transformação:** O script deve, então, usar junções (`pd.merge`) para construir as tabelas de dimensão e a tabela de fatos, conforme projetado no modelo "TO-BE".
    
- **Carga:** Salvar os DataFrames finais na pasta `data/processed/`, preferencialmente em formato **Parquet** para melhor performance de leitura e preservação dos tipos de dados.
    

## Fase 4: Análise e Geração de Insights (Extraindo Valor)

Com a base de dados limpa e estruturada, a busca por respostas pode começar.

### Seção 4.1: Formulando as Perguntas de Negócio

A análise deve ser guiada por perguntas claras. O processo para defini-las é iterativo:

1. **Hipóteses Iniciais:** Com base no edital dos cursos e no PPT do desafio, formule de 3 a 5 perguntas iniciais. Ex: "Qual o perfil do aluno que mais conclui os cursos?".
    
2. **Refinamento com a AED:** A exploração dos dados pode revelar novas possibilidades e permitir o refinamento das perguntas iniciais.
    
3. **Pesquisa de Mercado:** Busque por "KPIs para cursos online" ou "métricas de engajamento em EAD". Isso pode trazer insights valiosos sobre o que o mercado considera importante, como **Taxa de Conclusão, Tempo Médio para Conclusão, Pontos de Abandono (Drop-off Points) e Engajamento por Curso**. 15
    

### Seção 4.2: Análise e Visualização

- **Análise Quantitativa:** Use Python e Pandas (`.groupby()`, `.agg()`) para calcular os KPIs definidos.
    
- **Visualização de Dados:** Utilize `matplotlib` e `seaborn` para criar gráficos que respondam às perguntas de negócio. Lembre-se que cada gráfico deve ter um propósito claro e contar uma parte da história. 10
    

## Fase 5: Comunicação e Entrega (A Narrativa Final)

A melhor análise é inútil se não for bem comunicada. O foco aqui é no **Data Storytelling**. 29

- **Estrutura da Apresentação (Pirâmide Invertida):**
    
    1. **Comece com a Resposta:** Apresente o insight mais importante logo no início. 21
        
    2. **Contextualize o Problema:** Mostre o diagrama "AS-IS" e explique por que a estrutura original era um desafio.
        
    3. **Apresente a Solução:** Mostre o diagrama "TO-BE" e explique os benefícios da nova modelagem.
        
    4. **Forneça as Evidências:** Use seus gráficos para suportar cada ponto da sua conclusão principal.
        
    5. **Conclua com Recomendações:** Sugira próximos passos e ações baseadas nos seus achados. 35
        
- **Documentação Final (GitLab):**
    
    - Garanta que o `README.md` esteja completo, explicando o objetivo do projeto, a estrutura de pastas e como executar o script de ETL para reproduzir toda a análise. 4
        
    - O código deve estar limpo, comentado e os notebooks com as saídas limpas antes do commit final. 4
        

Seguindo este plano, a equipe não apenas resolverá o desafio técnico, mas também entregará um projeto completo, documentado e de alto valor estratégico.