---
sticker: lucide//arrow-up-down
---
# Relatório Arquitetural: Engenharia de ETL Cognitivo para Planilha de Blueprint de Repositórios Projeto SODA e FinOps para o Sistema Operacional Agêntico

O desenvolvimento de um Sistema Operacional Agêntico de arquitetura local e estritamente isolado da nuvem impõe desafios de engenharia que transcendem a simples escrita de código. No contexto do projeto focado em execução bare-metal e soberania de dados, cada componente de software, biblioteca ou padrão arquitetural candidato à absorção exige uma triagem exaustiva. O desafio contemporâneo concentra-se na resolução de um passivo técnico colossal: o processamento de centenas de repositórios hospedados no GitHub para extração de metadados profundamente semânticos e qualitativos. A conversão de bases de código desestruturadas em matrizes de conhecimento rígidas exige um processo denominado Extração, Transformação e Carga (ETL) Cognitivo, onde a inteligência artificial não atua apenas como um processador sintático, mas como um avaliador arquitetural subordinado a dogmas preestabelecidos.

A infraestrutura atual para a análise desta vasta gama de repositórios depende da intervenção humana em interfaces conversacionais baseadas na web. A execução manual deste paradigma gerou uma patologia operacional documentada como Dívida de Fluxo. Este fenômeno caracteriza-se pela extrema exaustão cognitiva decorrente da submissão repetitiva de comandos, da necessidade de correção contínua de alucinações estruturais geradas pelo modelo e da frustração com os limites de sessão em interfaces gráficas de usuário. Para viabilizar o projeto e mitigar a Dívida de Fluxo, é mandatória a transição imediata para um gasoduto de processamento autônomo, assíncrono e financeiramente otimizado (FinOps Zero-Cost).

A avaliação do componente a ser extraído baseia-se em uma planilha de controle canônica contendo quase quatrocentas URLs de repositórios. O sistema deve preencher rigorosamente quase quatro dezenas de colunas descritivas e numéricas. A taxonomia destas colunas ilustra a complexidade do ETL Cognitivo. O modelo deve inferir métricas quantitativas precisas e avaliações qualitativas densas, delineando o padrão lógico matemático do repositório, o ouro a ser extraído daquela base de código, os limites de onde a inteligência artificial não deve operar autonomamente e as instruções exatas de canibalização para conversão do código para a linguagem Rust.

Toda esta avaliação deve ser ancorada não no conhecimento genérico do modelo, mas em um manifesto de preferências arquiteturais corporativas atualmente hospedado em uma base do Google NotebookLM. O desafio de engenharia é projetar a infraestrutura ideal no estado da arte de 2026 para automatizar este duto utilizando exclusivamente infraestruturas de inferência de custo zero, garantindo que o preenchimento da planilha ocorra com total ausência de alucinações de esquema.

## Avaliação de Abordagens de Automação e ETL Cognitivo

A engenharia de um pipeline de ETL Cognitivo requer o abandono de suposições baseadas em ferramentas de consumo e a adoção de paradigmas de automação baseados em infraestrutura programática e orquestração local via servidores de contexto.

### O Paradigma de Interfaces Gráficas e Construtores Visuais

A tentativa de automatizar o preenchimento de matrizes de dados complexas através de interfaces web de conversação (Gemini Web, ChatGPT) ou através de construtores visuais (n8n, MindStudio) esbarra em barreiras intransponíveis. As interfaces gráficas de conversação são desenhadas para o consumo episódico e impõem mecanismos de truncamento rigorosos. O comportamento padrão da interface diante de uma extração massiva é a interrupção prematura da geração.

As plataformas de baixo código (Low-Code) revelam fragilidades quando submetidas ao estresse de um ETL Cognitivo denso. A fragmentação artificial do código do repositório para caber nos limites de memória dos nós visuais destrói a percepção holística que o modelo necessita. Além disso, a ausência de primitivas matemáticas rigorosas para a restrição da geração de texto (Structured Outputs) nestes fluxos visuais resulta em alucinações de formato frequentes. Portanto, estas abordagens são categoricamente inadequadas.

### A Verdade Sobre a Infraestrutura de APIs Gratuitas em 2026

O estado da arte no controle da saída gerada pelas APIs em 2026 superou a mera engenharia de instruções e consolidou-se na adoção de saídas estruturadas estritas (Constrained Decoding). Este processo matemático zera a probabilidade de qualquer token que viole a gramática estipulada do esquema JSON.

Contudo, para realizar a extração sem incorrer em custos (FinOps Zero-Cost), a engenharia de software não pode depender ingenuamente das promessas de marketing de "Endpoints Gratuitos". Uma auditoria nos limites de taxa reais destas APIs em 2026 revela estrangulamentos severos que poderiam colapsar o projeto SODA:

|**Provedor de API**|**Realidade do Limite e Restrições Ocultas (2026)**|**Avaliação para o Pipeline SODA**|
|---|---|---|
|**Google AI Studio (Free Tier)**|Generoso limite diário de 1.500 requisições para desenvolvedores. Dá acesso ao estado da arte como o Gemini 3.1 Pro Preview, que possui 1.048.576 tokens de entrada e um teto massivo de 65.536 tokens de saída.|**Aprovado (Rota Primária).** O volume diário atende facilmente as 370 linhas. A janela de saída de 65K tokens comporta com imensa margem a injeção do JSON de 37 colunas sem qualquer quebra.|
|**OpenRouter (Free Tier)**|Fornece acesso a modelos massivos com saídas colossais (como 131K tokens). Porém, impõe um bloqueio estrito de 20 requisições por minuto (RPM) e um teto intransponível de 200 requisições por dia (RPD). Possui recursos nativos de redirecionamento de falhas.|**Aprovado (Retaguarda).** O teto de 200 RPD impede que ele processe a planilha de 370 URLs sozinho. Contudo, é perfeito como uma rota de escape gratuita caso a API primária falhe.|
|**Nvidia NIM (Free Endpoints)**|Impõe um afunilamento implacável de apenas 40 RPM em contas de desenvolvimento. Pior ainda, restringe severamente a cadeia gerada; modelos vitais como Llama 4 sofrem truncamento forçado no teto de míseros 8.000 tokens de saída.|**Inviável para Extração Massiva.** O teto de 8K tokens corromperia irreversivelmente a validação do JSON exigido pelas longas justificativas arquiteturais do projeto.|
|**xAI Grok**|A gratuidade ilimitada para desenvolvedores foi encerrada. O uso programático depende do nível de assinatura (Tier 1 a 4) ou de saldo ativo, com cobranças ativas na ordem de dezenas de centavos por milhão de tokens (ex: Grok 4.1 Fast).|**Descartado.** Viola o princípio arquitetural estrito de operação contínua com custo zero.|

### Orquestração Local Através do Protocolo de Contexto do Modelo

O pilar da infraestrutura repousa em gerenciar a execução dos roteiros de captura e gravação localmente. No cenário projetado, um motor central escrito em Python utiliza a biblioteca Pydantic AI para instanciar servidores e clientes baseados em comunicações de canais padronizados do Protocolo de Contexto do Modelo (MCP).

A aquisição das heurísticas essenciais do Google NotebookLM era considerada uma barreira. Todavia, a versão unificada `v0.2.7` da ferramenta comunitária `notebooklm-mcp-cli` introduziu suporte a requisições HTTP nativas e resolveu os conflitos de perfis, integrando numa única dependência o serviço MCP e a interface de terminal. Operando diretamente como um servidor MCP, ele adquire programaticamente o contexto consolidado da sua conta, injetando-o como base inquestionável do orquestrador, eliminando automações frágeis de navegadores que sofriam com detecções de bots.

A etapa primária de exploração adota o fornecedor `webcrawl-mcp`, projetado expressamente para consumir e rastrear repositórios do GitHub, limpando detritos sintáticos e exfiltrando o âmago informacional de forma limpa.

Por fim, a gravação de dados ocorre sem falhas utilizando-se o `mcp-google-sheets`. Este provedor possibilita funções de atualização massiva (`batch_update`). Ao despachar chamadas atômicas como solicitações em lote, a API garante que as atualizações dos trinta e sete conjuntos complexos ocorram em paralelo absoluto, protegendo ativamente a planilha contra limites rigorosos do servidor.

## O Veredito Definitivo: A Arquitetura de Roteamento em Cascata (Waterfall)

Diante do cenário restritivo das APIs, a melhor arquitetura de automação de 2026 recusa construtores em nuvem e provedores pagos. O Veredito Definitivo foca no desenvolvimento de um **Orquestrador Local Python baseado em Pydantic AI e FastMCP**, utilizando estratégias de **Roteamento em Cascata (Waterfall Routing)** para transitar perfeitamente entre instâncias gratuitas, garantindo que o processamento das 370 linhas não colapse sob as restrições invisíveis da nuvem.

### Solução Cirúrgica para os 4 Gargalos Técnicos da Operação

**1. A Fragilidade Crônica do NotebookLM (Shadow API)**

A solução definitiva emprega a atualização `v0.2.7` do pacote unificado `notebooklm-mcp-cli`. Esta versão introduziu o suporte a transferências diretas de arquivos e abandonou as instâncias instáveis do navegador em segundo plano, estabilizando a ingestão da sua base filosófica arquitetural para alimentar os agentes de extração.

**2. O Estrangulamento de Limites de Taxa do Google Sheets**

A API do Google Sheets impõe limites rígidos de 60 requisições de gravação por minuto por usuário. O orquestrador é projetado para reter as saídas JSON em memória global e invocar primariamente o método `batchUpdate` do servidor MCP. Esta função agrupa todas as edições pendentes em um único documento lógico e efetua todas as gravações atômicas em uma só tacada, evaporando a ameaça do erro 503.

**3. O Esgotamento Oculto em APIs e Truncamento de Saída** Para anular as restrições brutais (como a asfixia de 8K tokens da Nvidia NIM), o script de Pydantic AI centraliza sua execução primária no ecossistema do Google AI Studio usando os modelos da classe Gemini 3.1 Pro Preview ou 2.5 Flash, que oferecem margens superlativas de mais de sessenta e cinco mil tokens por geração.

**4. A Mitigação de Falhas Sistêmicas com Fallback em Cascata**

Para suportar o fardo de extração de trezentos e setenta repositórios inteiros sem sofrer restrições da cota diária principal, programa-se no Pydantic AI uma tolerância de falhas em cascata:

- **Rota Primária:** O script consulta o Gemini (Google AI Studio).
- **Rota de Resgate:** Caso a API principal reporte exaustão de cota (`HTTP 429 Too Many Requests`), o próprio código intercepta o erro em vez de paralisar, redirecionando dinamicamente a tarefa daquela linha da planilha para o serviço gratuito do OpenRouter (utilizando modelos que suportam 131K tokens de saída, blindados pelo sistema de _Model Fallbacks_ do provedor).
- **Atrasos Matemáticos:** Adiciona-se uma suspensão temporária programada (backoff) de 5 a 10 segundos entre ciclos, garantindo que nenhum servidor dispare alarmes contra ataques de negação de serviço.

### Arquitetura Exata da Construção dos Contratos (Constrained Decoding)

A integridade estrutural do JSON é forçada na camada do interpretador utilizando bibliotecas agressivas de adequação de formato como o `llguidance`. O motor de inferência zera a probabilidade de qualquer token que não se alinhe com a classe base do Python. A tipagem exige:

- **Definições de Registro:** Campos de texto como `project_name` e `repo_url` validados por expressões regulares.
- **Vetores Numéricos Limitados:** Os escores `score_bare_metal_fit`, `score_architectural_extractability`, `score_operability` devem ser restritos estritamente ao tipo inteiro, delimitados entre os valores de 0 a 10.
- **Restrição Moral e Estrutural:** O campo `classificacao_terminal` é bloqueado via restrições enumeradas (`Enum`), permitindo apenas opções restritas do sistema SODA, como `INTEGRATE_AS_COMPONENT`, `ABSORB_PARTIALLY`, ou `REESCRITA_RUST`.

Com este contrato selado no núcleo do Pydantic AI, a orquestração processará os trezentos e setenta links utilizando as APIs gratuitas assincronamente e enviará lotes de pacotes preenchidos à sua aba `MASTER_SOLUTIONS`.

## Fluxo de Revisão e Rebalanceamento Pós-Preenchimento

Após o preenchimento inicial da planilha, um segundo script local inicia o ciclo de Rebalanceamento Estrutural. Modelos analíticos tendem perigosamente a concentrar-se no extremo mais elevado do desvio padrão para evitar julgamentos punitivos incertos sobre bases de código complexas.

O estágio da purificação engloba a medição passiva e automatizada de variância. Se um dado segmento dos dados revelar a repetição matemática não natural das notas mais elevadas, o sistema impõe um embargo à linha afetada. Lotes diagnosticados sob suspeita de viés complacente são submetidos a uma rodada de validação local usando a metodologia de _Schema-Guided Reasoning_. Um modelo focado e adverso disseca rigorosamente as matrizes, forçado pelo esquema a preencher colunas de defeitos críticos (como `real_structural_problem`) antes de ter permissão sintática para calcular o novo escore rebalanceado.

A fase conclusiva do roteiro baseia-se na dispersão estrutural relacional. O orquestrador aciona seus conectores do `mcp-google-sheets` e utiliza sua função de atualizações atômicas em bloco para desmembrar o repositório principal e instituir visualizações secundárias no ecossistema:

1. Aba matriz de topologia (**SODA_GRAPH_TOPOLOGY**): O script agrupa os pacotes que compartilham a mesma `stack_base` e dependências.
2. Aba matriz operacional (**ACTION_MATRIX - Canibalização**): O motor filtra implacavelmente apenas os arquivos classificados como necessitando intervenção de `REESCRITA_RUST`, unificando a instrução explícita contida em `acao_de_canibalizacao` e em `ouro_a_extrair` para gerar os tickets de desenvolvimento robótico.
3. Aba de quarentena (**QUARANTINE_RADAR**): Componentes classificados com elevadas pontuações em `design_misuse_risk` e `intrinsic_ethics_risk` são isolados visualmente. Este panorama sintético restrito converte-se no único repositório onde a aprovação explícita e obrigatória no modelo humano (Human-in-the-Loop) deverá atuar de forma a resgatar o elemento da suspensão impeditiva.

---

Para implementar a nossa arquitetura de Roteamento em Cascata com custo zero, você precisará gerar apenas duas chaves de API tradicionais e fazer um login via terminal para o NotebookLM.

Aqui está o passo a passo exato de onde e como configurar cada uma delas:

**1. Google AI Studio (Sua API Principal)** Esta será a rota que fará o trabalho pesado e processará a maior parte da sua planilha utilizando a cota gratuita.

- **Onde ir:** Acesse o painel do desenvolvedor em aistudio.google.com.
- **Como criar:** Faça login com uma conta do Google. No menu lateral ou superior, procure pelo botão "Get API Key" (Obter chave de API) e clique em "Create API key".
- **O que ela entrega:** Essa chave conectará o seu script local diretamente à infraestrutura do Google, dando acesso sem custo aos modelos de ponta, como o `gemini-3.1-pro-preview` e o `gemini-2.5-flash`.

**2. OpenRouter (Sua API de Resgate/Fallback)** Esta será a sua rede de segurança. Se a API do Google acusar limite de taxa no meio da leitura de um link, o script vai rotear o pedido para cá.

- **Onde ir:** Acesse o site openrouter.ai.
- **Como criar:** Faça login na sua conta (aquela onde você já possui saldo). Acesse o menu de configurações, vá até a seção "Keys" (Chaves) e clique para gerar uma nova chave de API para o seu projeto.
- **O que ela entrega:** O OpenRouter atua como um roteador universal. O nosso script usará essa chave para acionar modelos alternativos e com ampla janela de contexto em caso de falha da API principal.

**3. Google NotebookLM (Autenticação Local)** Como discutimos, o Google NotebookLM não possui uma área no site para você gerar uma chave de API como os outros serviços.

- **Onde e Como:** A "chave" será criada diretamente no terminal do seu computador. Você instalará o pacote unificado do servidor MCP e digitará o comando `nlm login`.
- **O que isso faz:** O comando abrirá uma janela do navegador pedindo que você acesse a sua conta do Google. Uma vez logado, o sistema extrai os cookies da sessão de forma automática e os armazena de forma segura no seu ambiente local. Isso permite que o seu script consulte as suas anotações do SODA sem precisar de nenhuma chave de API tradicional.

Para colocar o projeto para rodar, você só precisará copiar as chaves geradas no passo 1 e no passo 2 e colá-las em um arquivo `.env` (variáveis de ambiente) na mesma pasta onde o seu script de orquestração for construído. O código lidará com a alternância entre elas automaticamente.

---

Baseado na arquitetura de Roteamento em Cascata e no uso de servidores locais MCP que definimos, o seu arquivo `.env` (que deve ficar na raiz do seu projeto Python/Rust) será bem enxuto.

Você precisará das credenciais dos dois provedores de LLM e da configuração para permitir que o sistema edite a sua planilha. Aqui está exatamente o que você deve colocar no arquivo:

```
# ==========================================
# 1. ROTA PRIMÁRIA (Google AI Studio)
# Crie em: https://aistudio.google.com/
# ==========================================
GEMINI_API_KEY="AIzaSy_cole_sua_chave_aqui..."

# ==========================================
# 2. ROTA DE RESGATE / FALLBACK (OpenRouter)
# Crie em: https://openrouter.ai/keys
# ==========================================
OPENROUTER_API_KEY="sk-or-v1-cole_sua_chave_aqui..."

# ==========================================
# 3. GRAVAÇÃO NA PLANILHA (mcp-google-sheets)
# ==========================================
# Caminho absoluto ou relativo para o arquivo JSON gerado no Google Cloud 
SERVICE_ACCOUNT_PATH="./credenciais/service-account-key.json"

# (Opcional) ID da pasta do Drive caso o script precise isolar arquivos 
DRIVE_FOLDER_ID="COLE_O_ID_DA_PASTA_AQUI"
```

**Por que o NotebookLM não está no `.env`?**

A ferramenta que extrai sua base de conhecimento (`notebooklm-mcp-cli`) não utiliza chaves de API tradicionais. A autenticação é feita diretamente no seu terminal através do comando `nlm login`. Esse comando abrirá o navegador uma única vez para você logar na sua conta Google e, em seguida, extrairá e salvará os cookies de sessão de forma segura no seu ambiente local. O seu script usará esses dados guardados automaticamente, sem precisar de configuração no `.env`.

**Um detalhe importante sobre o Google Sheets:** Para que o provedor `mcp-google-sheets` consiga atualizar as 370 linhas em lote (batch-update) sem interrupções e sem precisar abrir janelas de autorização, a melhor prática é usar uma Conta de Serviço (Service Account). Você deve ir ao painel do Google Cloud, gerar uma chave JSON para a Service Account, baixar o arquivo para o seu projeto e apontar o caminho dele na variável `SERVICE_ACCOUNT_PATH`. Lembre-se também de ir na sua planilha do SODA e compartilhá-la com o e-mail da Service Account gerada (dando permissão de "Editor") para que o script possa gravar os dados extraídos.

---

No script, utilizando a biblioteca Pydantic AI, os modelos são definidos no momento de instanciar o agente (classe `Agent`), passando uma string que identifica o provedor e a versão exata do modelo.

Aqui estão as strings exatas que você deve usar na sua configuração:

**1. Para a Rota Principal (Google AI Studio)**

O SDK conectará automaticamente usando sua `GEMINI_API_KEY`.

- Para o modelo de raciocínio mais avançado (com o limite massivo de 65.536 tokens de saída): `'gemini-3.1-pro-preview'`.
- Para a alternativa super-rápida e leve do Google: `'gemini-2.5-flash'`.

**2. Para a Rota de Resgate (OpenRouter)**

Como o OpenRouter é um roteador universal que segue o padrão de API da OpenAI, você deve usar as strings exatas do catálogo deles.

- Para usar o modelo massivo com opções gratuitas da Nvidia: `'nvidia/nemotron-3-super-120b-a12b:free'`.
- Para usar a versão poderosa mais recente da DeepSeek: `'deepseek/deepseek-v4-pro'`.

**Exemplo prático de como isso fica no código (Pydantic AI):**

```Python
from pydantic_ai import Agent

# Definindo o agente principal que usará sua cota do Google AI Studio
agente_primario = Agent('gemini-3.1-pro-preview')

# Definindo o agente de fallback para o OpenRouter
# (No Pydantic AI, você usa o prefixo 'openai:' para acessar o OpenRouter)
agente_resgate = Agent('openai:deepseek/deepseek-v4-pro')
```

Com essas strings definidas, o seu script saberá exatamente qual modelo acionar e poderá realizar a troca automática de resgate caso a API principal fique sobrecarregada.

---

# Relatório Estratégico de Seleção, Orçamentação e Orquestração de Modelos de Inteligência Artificial para Extração de Metadados Tabulares

## 1. Introdução e Contextualização do Desafio Arquitetural

A automação de fluxos de trabalho baseados em dados estruturados, especialmente a extração e o preenchimento de metadados complexos em planilhas de larga escala, sofreu uma revolução arquitetural no ecossistema de Inteligência Artificial (IA) até o mês de abril de 2026. O presente relatório técnico foi elaborado para fornecer uma resposta exaustiva, fundamentada e estratégica sobre a seleção dos melhores modelos de linguagem de grande escala (LLMs) disponíveis no agregador OpenRouter e na plataforma Google AI Studio. O objetivo central é viabilizar a conclusão de uma planilha de engenharia de software contendo 37 colunas de metadados arquiteturais intrincados, minimizando custos operacionais e maximizando a precisão semântica.

O desafio imposto requer um processo de "ETL Cognitivo" (Extração, Transformação e Carga guiadas por semântica). As colunas exigidas pelo escopo do projeto (conforme evidenciado pelo artefato _SODA Blueprint_) demandam avaliações profundas, tais como `score_architectural_extractability`, `logic_math_heuristic`, `entropy_risk`, e decisões complexas de conversão de código e adequação a sistemas operacionais (_bare_metal_fit_). Para preencher essas colunas com alta fidelidade a partir da análise de repositórios de código aberto (como `hw-smi`, `boxsh`, `iron-proxy`, entre outros), os modelos selecionados não devem apenas realizar análise sintática de texto superficial. Eles precisam demonstrar um raciocínio de engenharia de software de nível sênior, capacidade de aderir a esquemas JSON estritos, e suportar vastas janelas de contexto para a ingestão integral de bases de código.

Este documento responde de forma estruturada às demandas operacionais: a definição das três melhores opções gratuitas e pagas no ecossistema OpenRouter; o detalhamento meticuloso das políticas de faturamento e obrigatoriedade de cartão de crédito no Google AI Studio vigentes a partir de 2026; a nomenclatura exata dos identificadores de API do Google; a ordem de preferência para a construção de um duto (pipeline) de inferência; e, finalmente, a orçamentação matemática do custo esperado para finalizar esta planilha.

A análise técnica a seguir confirma, categoricamente, que a combinação criteriosa das APIs do OpenRouter e do Google AI Studio fornece a infraestrutura robusta, resiliente e economicamente viável necessária para "matar essa planilha" com excelência e sem alucinações de formatação.

## 2. A Dinâmica da Extração de Dados e a Redundância Estrutural

Antes de elencar os modelos, é imperativo compreender a natureza computacional da tarefa. Preencher 37 colunas de dados para múltiplos repositórios envolve um fenômeno conhecido na engenharia de IA de 2026 como "Redundância Estrutural" em cargas de trabalho de serialização.

Quando um LLM é instruído a gerar saídas formatadas (seja em JSON, CSV ou tabelas Markdown), uma quantidade significativa dos tokens gerados não carrega inteligência, mas sim sintaxe. Chaves como `"score_philosophical_fit": "..."`, `"justificativa_decisao": "..."`, e `"acao_de_canibalizacao": "..."` repetidas para dezenas de repositórios consomem rapidamente o orçamento de tokens de saída (_Output Tokens_), que tradicionalmente custam de três a dez vezes mais do que os tokens de entrada (_Input Tokens_).

Estudos acadêmicos e benchmarks focados em processamento tabular, como o MMTU (Michigan Multi-Task Table Understanding) e o framework _Cents_, demonstram que modelos de linguagem frequentemente sucumbem a falhas quando submetidos a extrações muito extensas. O principal modo de falha é a "Evasão de Restrição sob Implementação Parcial", onde o modelo, após gerar 15 ou 20 colunas perfeitas, perde a aderência ao esquema tabular e começa a aglutinar dados ou pular colunas obrigatórias para economizar o esforço computacional do decodificador.

Portanto, a escolha dos modelos não recai apenas sobre qual é o mais "inteligente", mas sobre qual possui a melhor implementação de **Decodificação Restrita** (_Constrained Decoding_) e suporte nativo a **Saídas Estruturadas** (_Structured Outputs_). Um modelo ideal deve ser capaz de ser pareado com bibliotecas como Pydantic AI para garantir que, caso o JSON retornado esteja faltando a coluna `do_not_absorb` ou `real_structural_problem`, a própria API recuse a saída e force uma regeneração em nível de token.

## 3. Ecossistema OpenRouter: Seleção Estratégica de Modelos Gratuitos (Free Tier)

A plataforma OpenRouter atua como um agregador e roteador de inferência que democratiza o acesso a centenas de modelos fundacionais, unificando a experiência de desenvolvimento sob uma única interface de API. Até abril de 2026, a oferta de modelos operando a um custo de $0.00 por milhão de tokens expandiu-se dramaticamente, englobando modelos de pesos abertos (_open-weights_) de altíssima capacidade.

Contudo, a utilização de modelos gratuitos requer a aceitação de uma contrapartida arquitetural: os dados fornecidos nos _prompts_ podem ser registrados, monitorados e utilizados pelos provedores originais para o treinamento de iterações futuras e o aprimoramento de sistemas de moderação. Assumindo que os dados da planilha referem-se a avaliações de projetos de código aberto (como ferramentas hospedadas no GitHub, e.g., `docling`, `goose`, `tauri` ) e não contêm segredos industriais corporativos restritos (IP), o uso ostensivo da camada gratuita é altamente encorajado e representa uma alavancagem financeira brutal.

Abaixo estão as três opções gratuitas mais recomendadas para a extração de dados estruturados e preenchimento das 37 colunas, ordenadas rigorosamente por preferência técnica e estabilidade de decodificação.

### 3.1. Primeira Opção: NVIDIA Nemotron 3 Super 120B

A NVIDIA revolucionou o cenário de modelos de fronteira gratuitos com o lançamento da família Nemotron 3 em março de 2026. O Nemotron 3 Super é, sob qualquer métrica de engenharia, o modelo de pesos abertos mais poderoso disponível gratuitamente no OpenRouter.

- **Identificador de API Exato:** `nvidia/nemotron-3-super-120b-a12b:free`
- **Contexto Máximo:** 262.000 tokens.
- **Estrutura de Custos:** $0.00 / 1M Input | $0.00 / 1M Output.
- **Arquitetura:** Mistura de Especialistas (MoE) híbrida baseada em blocos Mamba e Transformer, possuindo 120 bilhões de parâmetros totais, mas ativando apenas 12 bilhões de parâmetros por passe de inferência. Essa escassez ativacional permite que ele rode em velocidades extremas.

**Justificativa de Preferência:** Para preencher a planilha, precisamos avaliar componentes como `score_bare_metal_fit` ou a viabilidade de `acao_de_canibalizacao` de projetos escritos em C++ (ex: `hw-smi`) para Rust puro. Isso exige profunda erudição em software. O Nemotron 3 Super alcançou uma taxa de resolução impressionante de 60.47% no benchmark _SWE-Bench Verified_, o que o coloca no topo da hierarquia de modelos abertos para codificação em abril de 2026. Ele suporta retenção de coerência a longo prazo (ideal para ler longos arquivos CSV ou repositórios) e foi treinado intensamente em ambientes de aprendizado por reforço para aderir a formatos complexos. Se a instrução exigir um JSON contendo 37 colunas perfeitamente tipadas, este modelo possui o maior rigor matemático para não falhar na sintaxe.

### 3.2. Segunda Opção: Tencent Hy3 Preview

O cenário asiático de desenvolvimento de IA produziu ferramentas excepcionais para _agentic workflows_, onde o modelo não apenas responde perguntas, mas atua como um processador autônomo dentro de um duto de dados. O Hy3 da Tencent é uma anomalia de eficiência disponibilizada gratuitamente.

- **Identificador de API Exato:** `tencent/hy3-preview:free`
- **Contexto Máximo:** 262.000 tokens.
- **Estrutura de Custos:** $0.00 / 1M Input | $0.00 / 1M Output.
- **Arquitetura:** Modelo denso de altíssima escala treinado sobre 1.14 trilhões de tokens, otimizado para operações de múltiplos passos.

**Justificativa de Preferência:** A característica mais valiosa do Hy3 é a sua compatibilidade com a alocação dinâmica de computação (níveis de raciocínio configuráveis). O modelo permite que o desenvolvedor instrua a engine a gastar mais tempo "pensando" antes de emitir a resposta tabular. Na extração de metadados, muitas vezes a determinação do `extractability_level` de uma biblioteca em Node.js (como o projeto `Paperclip` ) não está escrita no repositório; ela deve ser inferida avaliando as dependências. O Hy3 é excepcional em digerir essas nuances e mapeá-las em categorias estritas. Ele ocupa o segundo lugar por ser marginalmente inferior ao Nemotron em compreensão de nichos ultra-baixos como Rust _no_std_, mas é fenomenal na manutenção de consistência de saída JSON.

### 3.3. Terceira Opção: OpenAI gpt-oss-120b

Em uma manobra estratégica no final de 2025, a OpenAI disponibilizou a sua própria arquitetura de pesos abertos. Embora não possua o brilho das iterações mais recentes (como o GPT-5.5), o `gpt-oss-120b` carrega a assinatura de estabilidade e alinhamento pela qual a OpenAI é renomada.

- **Identificador de API Exato:** `openai/gpt-oss-120b:free`
- **Contexto Máximo:** 131.000 tokens.
- **Estrutura de Custos:** $0.00 / 1M Input | $0.00 / 1M Output.
- **Arquitetura:** Mistura de Especialistas (MoE) de 117 bilhões de parâmetros, ativando 5.1 bilhões por inferência, otimizado para quantização MXFP4 e execução em GPUs únicas.

**Justificativa de Preferência:** O valor deste modelo para a finalização da planilha reside na sua familiaridade com o formato de resposta "Harmony" da OpenAI e sua aderência absoluta a _function calling_ (chamada de ferramentas). Muitas bibliotecas de extração, como o LangChain ou o Pydantic AI, possuem rotinas de conversão de dados otimizadas para as peculiaridades de como os modelos da OpenAI estruturam seus arrays JSON. Se a extração das 37 colunas falhar repetidamente nos modelos da NVIDIA ou Tencent devido a incompatibilidades de _parser_, o `gpt-oss-120b` serve como a rede de segurança (o _fallback_) perfeita. A limitação é a sua janela de contexto de 131K, que é a metade dos seus pares , o que exige que o código fonte do repositório avaliado seja sumarizado antes da injeção.

|**Ordem**|**Modelo (ID OpenRouter)**|**Foco Principal para a Planilha**|**Contexto**|**Custo**|
|---|---|---|---|---|
|**1**|`nvidia/nemotron-3-super-120b-a12b:free`|Extração de lógica bare-metal, análise de Rust/C++ e geração estruturada confiável.|262K|$0.00|
|**2**|`tencent/hy3-preview:free`|Tarefas de múltiplos passos e reflexão profunda sobre adequação filosófica.|262K|$0.00|
|**3**|`openai/gpt-oss-120b:free`|Alta compatibilidade com bibliotecas de parser JSON e ferramentas rigorosas.|131K|$0.00|

## 4. Ecossistema OpenRouter: Seleção Estratégica de Modelos Pagos (Premium)

Quando a integridade dos dados atinge uma massa crítica, ou quando as proteções de privacidade se tornam não-negociáveis (visto que modelos pagos no OpenRouter garantem o não-armazenamento e não-treinamento sobre os dados da API ), a transição para a camada _Premium_ é indispensável. Em abril de 2026, o cenário de precificação desmoronou em favor dos desenvolvedores; a chegada da arquitetura DeepSeek V4 colapsou os preços de fronteira, criando o que analistas chamam de a maior relação "Inteligência por Dólar" da história da computação gerativa.

Na camada premium, a métrica de seleção inverte-se. O foco não é apenas "quem pode fazer", mas sim "quem fará de forma determinística pelo menor custo de token de saída".

### 4.1. Primeira Opção: DeepSeek V4 Pro

Lançado na última semana de abril de 2026, o modelo V4 Pro da DeepSeek reescreveu a economia do roteamento de APIs. O modelo não é apenas estupendamente barato, mas é considerado um "oráculo" em tarefas de codificação.

- **Identificador de API Exato:** `deepseek/deepseek-v4-pro`
- **Contexto Máximo:** 1.000.000 de tokens.
- **Custo de Entrada (Input):** ~$1.74 por milhão de tokens (com retenção de cache derrubando o preço para níveis inferiores).
- **Custo de Saída (Output):** ~$3.48 por milhão de tokens.
- **Arquitetura:** O maior modelo de pesos abertos de sua era, com 1.6 trilhão de parâmetros totais (49B ativos por token), implementando atenção esparsa avançada.

**Justificativa de Preferência:** A planilha _SODA Blueprint_ contém dezenas de colunas que exigem discernimento subjetivo, mas baseadas em código objetivo: e.g., por que o projeto `Goose` economiza meses de engenharia, mas requer a extirpação do Electron/TypeScript para se alinhar aos dogmas do Rust. O DeepSeek V4 Pro foi exaustivamente treinado em _Agentic Coding_ e supera quase todos os modelos concorrentes em matemática e ciência da computação. Ao preço de $3.48 por milhão de tokens de saída (uma fração ridícula dos $15.00 cobrados pela Anthropic ), este modelo é o trator industrial definitivo para processar bases de dados imensas. Ele permite que o script leia dezenas de repositórios, injetando todo o seu histórico no contexto de 1 milhão de tokens, e cuspa a planilha completamente formatada por menos do custo de um café.

### 4.2. Segunda Opção: Qwen 3.6 Plus

A Alibaba continua sua marcha implacável de excelência com a série Qwen. O modelo Qwen 3.6 Plus opera em uma categoria quase anômala: ele possui a inteligência de modelos caros, mas é precificado quase como um modelo de triagem.

- **Identificador de API Exato:** `qwen/qwen3.6-plus`
- **Contexto Máximo:** 1.000.000 de tokens.
- **Custo de Entrada (Input):** $0.325 por milhão de tokens (para contextos até 256K; sobe para $1.30 após esse limite).
- **Custo de Saída (Output):** $1.95 por milhão de tokens (para contextos até 256K; sobe para $3.90 após esse limite).
- **Arquitetura:** Mistura de especialistas esparsa com atenção linear otimizada, suportando a mesma janela massiva que o DeepSeek e o Gemini.

**Justificativa de Preferência:** O ponto forte inegável do Qwen 3.6 Plus é o seu custo de ingestão de $0.325 por milhão de tokens. A extração de metadados a partir de repositórios exige que os códigos fontes brutos, _readmes_ e _issues_ sejam despejados na API. Frequentemente, 90% do volume da requisição é a leitura de milhares de linhas de código que não sofrerão alterações, apenas avaliações. Com esse preço, o modelo pode ler bibliotecas inteiras do GitHub para preencher com exatidão a coluna `logic_math_heuristic`. A desvantagem sutil em relação aos rivais ocidentais é a política de _Caching_ do Qwen, que foca no cache de prefixo em vez de armazenamento duradouro de chave-valor , mas o custo nominal base é tão diminuto que ele se justifica facilmente. Odiar TypeScript e priorizar Rust é um traço recorrente na base de dados avaliada ; o Qwen 3.6 demonstra um "vibe coding" formidável para captar e aplicar preferências filosóficas desse tipo no preenchimento de metadados.

### 4.3. Terceira Opção: Anthropic Claude 4.6 Sonnet (Via OpenRouter)

O mercado pode competir em preço, mas quando o sistema não pode falhar sob hipótese alguma, a indústria recorre invariavelmente à Anthropic. O Claude Sonnet, em sua iteração mais recente (4.6), é a espinha dorsal de fluxos agênticos complexos em todo o mundo corporativo.

- **Identificador de API Exato:** `anthropic/claude-sonnet-latest` (resolve para a versão 4.6).
- **Contexto Máximo:** 1.000.000 de tokens (expansão recente de abril de 2026).
- **Custo de Entrada (Input):** $3.00 por milhão de tokens.
- **Custo de Saída (Output):** $15.00 por milhão de tokens.
- **Arquitetura:** Modelo denso/híbrido focado em segurança estrita, _steerability_ (direcionamento) e ausência de degradação atencional.

**Justificativa de Preferência:** A disparidade de preço é notável — a saída do Claude 4.6 Sonnet é quatro vezes mais cara que a do DeepSeek V4 Pro. No entanto, a razão pela qual ele está na lista de preferência é a sua invulnerabilidade à "Evasão sob Implementação Parcial" mencionada anteriormente. Se a planilha for processada em um único dote gigante, exigindo que o LLM preencha 37 colunas para 50 projetos simultaneamente sem errar uma única vírgula de formatação de JSON, o Claude Sonnet entregará o resultado perfeito na primeira tentativa. Além disso, a precificação pesada é parcialmente mitigada pelo sistema de _Prompt Caching_ agressivo da Anthropic; se o esquema da planilha for injetado em cada chamada de API, os tokens de esquema cacheados caem de preço consideravelmente, viabilizando operações iterativas.

|**Ordem**|**Modelo (ID OpenRouter)**|**Foco Principal para a Planilha**|**Custo Estimado (In/Out por 1M)**|
|---|---|---|---|
|**1**|`deepseek/deepseek-v4-pro`|Equilíbrio supremo: O melhor intelecto arquitetural pelo menor custo do mercado em 2026.|$1.74 / $3.48|
|**2**|`qwen/qwen3.6-plus`|Leitura massiva de código devido ao preço de entrada (Input) de 32 centavos de dólar.|$0.325 / $1.95|
|**3**|`anthropic/claude-sonnet-latest`|Garantia de não-alucinação sintática em tabelas massivas, ideal para saídas irreversíveis.|$3.00 / $15.00|

## 5. A API do Google AI Studio: Nomenclaturas, Ordem de Uso e Políticas de Cartão

O usuário apresentou dúvidas específicas, operacionais e críticas sobre o ecossistema do Google: a necessidade de criar contas atreladas a cartões de crédito e as nomenclaturas exatas dos modelos visando maximizar o custo-benefício.

O ano de 2026 testemunhou uma reorganização tectônica nas políticas do Google AI Studio. Nos meses que antecederam abril, o Google implementou regras financeiras estritas para conter abusos e estabilizar a infraestrutura.

### 5.1. A Política do "Free Tier" e o Cartão de Crédito

A pergunta central é: _"A API do Google é aquela Free que dá pra se criar ou é pra gerar uma atrelada a cartão de crédito?"_

A resposta exige uma distinção clara entre duas fases e capacidades de conta:

**Fase 1: O "Free Tier" Puro (Sem Cartão)** Sim, a API do Google **continua permitindo a criação de chaves e a utilização de um patamar gratuito sem a necessidade imediata de inserir um cartão de crédito**. Como o AI Studio foi arquiteturalmente desvinculado de exigências profundas do Google Cloud Platform (GCP) para funções básicas, um desenvolvedor pode testar modelos minutos após o cadastro. Contudo, esse "Free Tier" tem limitações paralisantes se utilizado para processamento automatizado em larga escala :

1. **Limites Punitivos de API (Rate Limits):** A conta gratuita está limitada a meras 15 Requisições Por Minuto (RPM) e 1.500 Requisições Por Dia (RPD). Para rotear centenas de repositórios e extrair as 37 colunas da planilha através de agentes, esse limite causará erros contínuos de `429 Too Many Requests`.
2. **Castração de Modelos (Abril de 2026):** A partir de 1º de abril de 2026, **o Google removeu todos os modelos da série "Pro" (como o Gemini 3.1 Pro) da camada gratuita**. Na camada livre, o usuário é restrito a interagir quase que exclusivamente com a família "Flash" e "Flash-Lite", que são velozes, mas desprovidos da profundidade arquitetural que o preenchimento de planilhas de infraestrutura exige.
3. **Privacidade Comprometida:** Qualquer dado submetido sob a camada gratuita é explicitamente marcado como elegível para treinar futuras iterações dos produtos Google.

**Fase 2: A Camada "Tier 1" Paga (Com Cartão e Prepay)** Para viabilizar a conclusão profissional da planilha — superando a lentidão, acessando os modelos de raciocínio profundo e protegendo a integridade dos dados inseridos — a vinculação de uma conta de faturamento (que requer cartão de crédito) torna-se mandatória. A grande novidade imposta pelo Google a partir de 23 de março de 2026 é o sistema **Prepay (Pré-pagamento)**. Em vez de atrelar o cartão e ser faturado retroativamente ao final do mês, contas recém-criadas devem depositar um crédito inicial (mínimo de $10 USD) no balanço da conta. À medida que o modelo consome tokens para ler o código-fonte e cuspir os metadados da planilha, os valores (centavos de dólar) são subtraídos instantaneamente do saldo. Se o saldo atingir zero, a API paralisa imediatamente , fornecendo um mecanismo de segurança absoluta contra choques de cobrança (faturas-surpresa). Adicionalmente, as contas pagas recebem o teto restritivo de gastos (_Spend Caps_), que no Tier 1 trava em $250 mensais, mitigando riscos de _loops_ infinitos no código do usuário consumirem fundos descontroladamente.

### 5.2. Nomes Exatos dos Modelos Google e Ordem de Qualidade e Custo

No universo de APIs do Google, a formatação das _strings_ numéricas é crucial para roteamento. Para tentar "matar a planilha" aproveitando a dicotomia Qualidade x Custo, o sistema de extração deve ser configurado com o conhecimento exato do arsenal disponível em 2026.

|**Hierarquia (Qualidade vs Custo)**|**Identificador Exato na API (Model String)**|**Classe do Modelo**|**Contexto & Capacidade Específica**|**Custo Padrão (In / Out por 1M)**|
|---|---|---|---|---|
|**1. O Mestre Estrutural (Qualidade Máxima)**|`gemini-3.1-pro-preview-customtools`|Pago (Tier 1+)|1M de tokens. Esta é uma variante cirúrgica recém-lançada. Ele impede o modelo de alucinar comportamentos genéricos e o força a obedecer às regras estritas de ferramentas JSON e esquemas fornecidos. É a única opção aceitável para forjar as 37 colunas lógicas sem falhas.|$2.00 / $12.00|
|**2. O Raciocínio Generalista (Alto Custo-Benefício)**|`gemini-3.1-pro-preview`|Pago (Tier 1+)|1M de tokens. A versão base com o mesmo raciocínio (77.1% no ARC-AGI-2), ideal para ler documentações vastas e gerar textos orgânicos profundos. No entanto, é menos rígido em matrizes formatadas do que a versão _customtools_.|$2.00 / $12.00|
|**3. O Executor Intermediário (Alta Velocidade)**|`gemini-3.1-flash-preview`|Gratuito / Pago|1M de tokens. Ferramenta versátil. Perfeito para estruturar os dados extraídos sem os custos astronômicos do modelo Pro, mantendo formatação determinística.|$0.50 / $3.00|
|**4. O Triador Massivo (Custo Mínimo/Volume)**|`gemini-3.1-flash-lite-preview`|Gratuito / Pago|1M de tokens. A inovação focada em alta latência do Google. Serve puramente para processar texto bruto de forma bruta. Ele deve ser usado para ler códigos imensos ou logs imensos e descartar ruído antes da inferência real ser delegada aos modelos maiores.|$0.25 / $1.50|

_Nota Crítica Operacional:_ A limitação severa de tokens de saída no ecossistema Google. Embora todos os modelos listados acima possuam uma janela de entrada monumental de 1.048.576 tokens, a saída máxima (Output Limit) por padrão está travada na ridícula cifra de 8.192 tokens. Para que o modelo não corte a planilha na metade durante a geração do array JSON ou Markdown, o desenvolvedor deve obrigatoriamente injetar o parâmetro `maxOutputTokens: 65536` no arquivo de configuração da API.

## 6. Orçamentação e Avaliação dos Custos Esperados

A determinação do orçamento (o "valor esperado") exige modelagem matemática do duto de processamento. Assumiremos, a partir dos padrões da indústria subjacentes em solicitações de processamento volumoso (como o indicador tangencial de "370 repositórios do GitHub" ), uma amostragem realista de volume de tokens.

Para cada linha da planilha, o LLM deve:

1. Ler o contexto de código-fonte, metadados do repositório, ou sumários arquiteturais (Ex: projeto `Paperclip` ou `tauri` ).
2. Produzir as 37 colunas detalhadas contendo julgamentos elaborados (`visão_do_enxame`, `proposta_original_resumo`, `observacoes`).

**Premissas do Cálculo (Pipeline Direto):**

- **Total de Registros a Processar:** 370 Repositórios/Projetos.
- **Custo de Leitura por Registro (Tokens de Ingestão):** Estimado em pesados 50.000 tokens (equivalente a 100 páginas de arquivos de código ou documentação compactada) para embasar as deliberações.
    - _Total de Input:_ 370 x 50.000 = **18.500.000 tokens (18.5M)**.
- **Custo de Escrita por Registro (Tokens de Geração):** A geração das 37 colunas em JSON ou formato de tabela, dada a "redundância estrutural" e os julgamentos extensos, demandará cerca de 2.000 tokens.
    - _Total de Output:_ 370 x 2.000 = **740.000 tokens (0.74M)**.

Abaixo, a extrapolação orçamentária distribuída nos modelos sugeridos, refletindo as tabelas de preços de abril de 2026:

|**Arquitetura Escolhida**|**Custo com Input (18.5M)**|**Custo com Output (0.74M)**|**Custo Financeiro Total Estimado**|
|---|---|---|---|
|**Cenário 1: Totalmente Gratuito** (Nemotron + Gemini Flash-Lite no Free Tier)|$0.00|$0.00|**$0.00 USD** (Com severo risco de instabilidade por Rate Limits e eventuais recusas em colunas complexas).|
|**Cenário 2: Otimizado Inteligente** (Uso integral do **DeepSeek V4 Pro** via OpenRouter)|$32.19 (18.5 x $1.74)|$2.57 (0.74 x $3.48)|**~$34.76 USD** (Custo final formidável, o 'ponto ideal' do mercado).|
|**Cenário 3: Google Híbrido** (**Gemini 3.1 Flash-Lite** para leitura de massa + **Gemini 3.1 Pro Customtools** para deliberação final via API Nativa Tier 1)|~$4.62 (se o Lite processar o Input base)|~$8.88 (se o Pro Customtools processar apenas os metadados refinados)|**~$13.50 USD**|
|**Cenário 4: Máxima Fidelidade Premium** (Uso integral do **Claude 4.6 Sonnet** via OpenRouter)|$55.50 (18.5 x $3.00)|$11.10 (0.74 x $15.00)|**~$66.60 USD** (Opção onde falhas de formatação JSON são funcionalmente nulas).|

Nota: Os custos da Cenário 4 podem ser comprimidos em até 70% a 90% se o sistema explorar o recurso de "Prompt Caching" (Cache de Prefixo) provido nativamente pelo OpenRouter para a Anthropic, caso a documentação e os esquemas se mantenham estáveis repetidas vezes no loop do script.

**A conclusão sobre o orçamento:** Para liquidar as avaliações intrincadas de 370 entidades com 37 colunas de julgamentos e taxonomia rigorosa, a empresa deve esperar desembolsar **entre US$ 13.00 e US$ 40.00** nos cenários pagos altamente recomendáveis (usando Google ou DeepSeek). Se a organização desejar a garantia divina contra falhas de extração da Anthropic, o teto sobe para **cerca de US$ 66.00**. É um custo perfeitamente negligenciável considerando a enormidade cognitiva do trabalho executado, dissipando os temores de gastos colossais.

## 7. A Orquestração Arquitetural do Sucesso

Para responder objetivamente à indagação conclusiva: _"Beleza, acho que usa do OpenRouter e Google nos conseguiremos matar essa planilha, certo?"_

**Absolutamente certo. O desafio é trivialmente superável com as ferramentas eleitas.** No entanto, o sucesso dessa operação e a blindagem contra os custos de redundância descritos não advêm simplesmente de despejar prompts cegos num roteador de API. A arquitetura de execução deve ser construída sob as bases da engenharia de agentes autônomos padronizada no final de 2025/início de 2026.

As publicações especializadas sobre arquiteturas agênticas e sistemas _ODD_ (_Outcome-Driven Development_) provam que abordagens de _Single Agent_ (onde você pede para o modelo ler o repositório e já vomitar a tabela inteira) geram atrito extremo e perda de coerência.

A arquitetura para garantir o preenchimento dessas 37 colunas deve utilizar o **Modelo de Orquestração Sequencial (Pipeline Workflow)** associado a bibliotecas de aderência de protocolo:

1. **Ingestão via Model Context Protocol (MCP):** Ao invés de concatenar manualmente códigos na memória, o duto deve utilizar servidores MCP locais (ou gerenciados ) para expor as documentações nativamente à janela de contexto de 1 Milhão de tokens. O Google e os modelos via OpenRouter consultarão diretamente a estrutura de arquivos para encontrar as definições de linguagens e métodos.
2. **Triagem Periférica:** A leitura crua para responder a colunas base (como `stack_base`, `proposta_original_resumo`, `categoria_arquitetural` ) deve ser despachada para o **Gemini 3.1 Flash-Lite** ou **Qwen 3.6 Plus**. Modelos velozes aglutinam a informação factual gastando centavos de dólar.
3. **Deliberação Cognitiva e Julgamento (Decodificação Restrita):** As avaliações intrincadas, que requerem abstrações profundas, como medir a "Fascinação técnica avassaladora" de um pacote Rust, devem ser agrupadas no formato de schemas rígidos utilizando bibliotecas como Pydantic AI. Esses _schemas_ declaram que a coluna `operability_level` é um enum restrito e que `justificativa_decisao` é uma string imperativa. Esse pacote tipado é enviado exclusivamente para o **DeepSeek V4 Pro** (via OpenRouter) ou **Gemini 3.1 Pro Customtools** (via Google API). Esses gigantes da inferência receberão o resumo dos triadores, ponderarão sobre a arquitetura e obedecerão à infraestrutura Pydantic para devolver o objeto JSON purificado e sem caracteres ruidosos, que será magicamente inserido de volta no CSV da planilha.

## 8. Conclusão Final

A automação e formatação da matriz _SODA Blueprint_ e os metadados associados atestam a maturação completa dos modelos de linguagem acessíveis de forma modular e independente de plataformas fixas.

A escolha inteligente, alicerçada no estado tecnológico de abril de 2026, consolida-se em:

- Para operações exploratórias e isentas de pagamentos diretos, os gigantes de pesos abertos no OpenRouter (NVIDIA Nemotron 3 e Tencent Hy3) suportarão a carga, enquanto houver tolerância a inconsistências pontuais.
- Para operação sistêmica, a injeção do modelo `deepseek/deepseek-v4-pro` altera a equação de viabilidade de projetos massivos.
- O uso nativo do Google AI Studio requer a adaptação ao sistema _Prepay_ na camada Tier 1, mas confere acesso ao `gemini-3.1-pro-preview-customtools`, cujo valor para extrações JSON restritivas e estruturadas é simplesmente insubstituível na prevenção de deturpações da formatação final.

Tratando a redundância estrutural com APIs de validação e operando sob o arranjo de custo apresentado, a materialização perfeita e algorítmica da planilha se torna não apenas certa, mas trivialmente viável.

---

Fatiar o processo (criar um pipeline sequencial ou orquestração multi-agente) é exatamente o estado da arte para fluxos de trabalho agênticos em 2026. Pedir para um único modelo ler o código, debater filosoficamente como três especialistas e cuspir um JSON perfeito de 37 colunas é a receita para o fracasso estrutural e custos altíssimos.

A sua ideia de usar a "wiki" oficial dos repositórios é brilhante. Usar apenas o código-fonte força a IA a deduzir a intenção do autor, o que gera alucinações. Ingerir a documentação oficial (`codewiki`) usando ferramentas como o `mcp-server-webcrawl` ancora o modelo na "verdade oficial" da ferramenta, economizando tokens de raciocínio.

Aqui está a estratégia arquitetural exata para fatiar o processo, aplicar o Enxame de Especialistas e derrubar o custo para quase zero.

### O Pipeline Fatiado em 3 Fases (O "ETL Cognitivo")

A lógica aqui é usar modelos "baratos/rápidos" para ler volumes massivos de dados, modelos de "raciocínio" para o debate das personas, e modelos "estritos" para a formatação final.

#### Fase 1: O "Leitor Rápido" (Ingestão de Contexto Bruto)

Nesta fase, o objetivo é ler o código, o README e a `codewiki`, extraindo apenas os fatos (linguagem, dependências, o que a ferramenta faz).

- **A Ferramenta:** O script usa o `mcp-server-webcrawl` para raspar a documentação.
- **O Modelo Ideal:** `gemini-3.1-flash-lite-preview` (via Google AI Studio Free Tier) ou `qwen/qwen3.6-plus` (via OpenRouter).
- **Por que:** O Gemini 3.1 Flash-Lite tem 1 milhão de tokens de contexto e é projetado especificamente para extração massiva e de alta velocidade com custo baixíssimo (ou zero, no Free Tier). O Qwen 3.6 Plus também é fenomenal para leitura de código, com o custo de entrada de apenas $0.325 por milhão de tokens.
- **Output desta fase:** Um resumo condensado em Markdown (apenas fatos e trechos de código cruciais), reduzindo os 50.000 tokens originais do repositório para uns 3.000 tokens de puro suco de informação.

#### Fase 2: O "Enxame Cognitivo" (A Tríade de Especialistas)

Aqui entram as personas. Pegamos o resumo de 3.000 tokens da Fase 1 e passamos para o seu Enxame: o **Arquiteto**, o **Produto/UX** e o **Canibal (Guardião do Bare-Metal)**.

- **A Abordagem:** Em vez de rodar os 3 juntos, rodamos os prompts em paralelo. O Arquiteto avalia a escalabilidade; o UX foca na acessibilidade e interface; o Canibal procura bibliotecas pesadas, lixo em Node.js e avalia a `acao_de_canibalizacao` para Rust.
- **O Modelo Ideal:** `deepseek/deepseek-v4-flash` (via OpenRouter) ou `tencent/hy3-preview:free`.
- **Por que:** O DeepSeek V4 Flash custa apenas $0.14 de entrada e $0.28 de saída por milhão de tokens e é otimizado para raciocínio de código e tarefas agênticas. O `hy3-preview:free` da Tencent é um modelo de Mistura de Especialistas (MoE) brilhante e 100% gratuito no OpenRouter.
- **Output desta fase:** Três blocos de texto contendo o parecer apaixonado de cada especialista sobre aquele repositório.

#### Fase 3: O "Formatador Rigoroso" (Fechamento do JSON)

Nesta última etapa, o script pega os resumos factuais (Fase 1) e os debates do Enxame (Fase 2) e compila tudo nas 37 colunas rigorosas da planilha.

- **O Modelo Ideal:** `gemini-3.1-pro-preview` (via Google AI Studio) ou `openai/gpt-oss-120b:free` (OpenRouter).
- **Configuração Obrigatória:** Aqui usamos a biblioteca Pydantic AI com o modelo Pydantic da sua tabela (JSON Schema). Se usar o Gemini 3.1 Pro, você **precisa** forçar a configuração `maxOutputTokens: 65536` na chamada da API. Por padrão, ele corta as respostas em 8.192 tokens, o que quebraria seu JSON no meio.
- **Por que:** O Gemini 3.1 Pro tem suporte nativo inigualável para Structured Outputs (esquemas JSON). O `gpt-oss-120b` é a alternativa gratuita da OpenAI que também segue esquemas rigorosamente. Esta fase exige zero "raciocínio profundo", pois todo o pensamento já foi feito na Fase 2; a IA atua apenas como uma secretária executiva organizando os dados nas 37 colunas.

### Como Melhorar o Output Esperado (Dicas de Arquitetura)

Para garantir que a revisão e o rebalanceamento futuro tenham sucesso, você deve aplicar duas táticas de melhoria na sua Fase 3:

1. **Schema-Guided Reasoning (SGR) no JSON:** Nunca peça para a IA dar a nota (ex: `score_bare_metal_fit`) _antes_ de escrever a justificativa. Em 2026, a prática padrão de SGR dita que o seu schema (a ordem das chaves do JSON que você pede para o Pydantic) deve forçar a IA a primeiro escrever a "análise de falhas" ou "justificativa_decisao" e **somente depois** declarar a pontuação. Isso previne que a IA dê um "10" inflado por inércia, pois o texto prévio a obriga a considerar os defeitos.
2. **Taxonomia Enumerada (Enums limitados):** Para colunas como `classificacao_terminal` ou `where_ai_should_not_enter`, não deixe o modelo escrever texto livre. Force-o a escolher de uma lista estrita usando o tipo `Enum` no Python. Isso facilita infinitamente a criação das futuras abas (como a DEEP_COMPONENTS) usando filtros de código, em vez de depender de interpretação semântica.

### Expectativa de Custo Final (A Matemática)

Com essa estratégia fatiada, você não gastará nem perto de 40 dólares. Veja a estimativa realista:

- **Fase 1 (Ingestão de 370 repositórios x 50k tokens):** 18.5 milhões de tokens. Usando o Free Tier do Gemini Flash-Lite ou o modelo gratuito da Qwen no OpenRouter = **$0.00**.
- **Fase 2 (Enxame de Especialistas):** Processar os resumos (370 x 3.000 tokens = 1.1M tokens) x 3 personas em paralelo. Usando DeepSeek V4 Flash ($0.14 entrada/$0.28 saída) = **~$0.50**. (Ou $0,00 se usar o modelo da Tencent).
- **Fase 3 (Geração do JSON de 37 colunas):** Passar todo o contexto (fatos + debates) para o Gemini 3.1 Pro. Aproximadamente 2 milhões de tokens de entrada totais e cerca de 740 mil tokens de saída. O custo do Gemini 3.1 Pro é $2/1M in e $12/1M out. Custo = $4.00 + $8.88 = **~$12.88**.

**Custo Total Estimado:** Cerca de **$13.38 dólares** para preencher com precisão insana as 37 colunas das 370 linhas (ou literalmente **Zero Dólares** se você utilizar exclusivamente a camada gratuita do Google AI Studio e os roteadores gratuitos do OpenRouter em cascata).

Dessa forma, você delega o trabalho burro e pesado para as APIs de custo zero, restringe o raciocínio complexo a modelos ultra-baratos como DeepSeek Flash, e reserva o orçamento apenas para o modelo que fará a "costura" final e segura do seu schema de banco de dados.

---

A Fase 3 ("Formatador Rigoroso") tem o único objetivo de organizar o texto analítico, que já foi gerado pelos especialistas, dentro de um JSON contendo as suas 37 colunas. Como o trabalho cognitivo pesado já foi resolvido na Fase 2, você não precisa pagar pelo poder de raciocínio premium do Gemini 3.1 Pro para montar a tabela. Você só precisa de modelos que ofereçam obediência absoluta a esquemas (_Structured Outputs_).

Para substituir o modelo mais caro e derrubar o custo dessa formatação final para quase zero, o mercado atual oferece as seguintes opções seguras:

**1. Modelos 100% Gratuitos (via OpenRouter)**

- **`openai/gpt-oss-120b:free`**: A versão de pesos abertos da OpenAI suporta nativamente as capacidades agênticas e a geração de saídas estruturadas (_Structured Outputs_). Por ser treinada no formato de resposta da própria OpenAI, ela tem compatibilidade perfeita com os analisadores de JSON da biblioteca Pydantic AI.
- **`nvidia/nemotron-3-super-120b-a12b:free`**: É um dos melhores modelos abertos de 2026. Ele usa uma arquitetura de Mistura de Especialistas (MoE) que ativa apenas 12 bilhões de parâmetros por vez, garantindo formatação rápida, determinística e com 100% de desconto via OpenRouter.

**2. Modelos de Baixíssimo Custo (via OpenRouter)**

- **DeepSeek V4 Flash (`deepseek/deepseek-v4-flash`)**: Com um custo praticamente residual de apenas US$ 0,14 por milhão de tokens de entrada e US$ 0,28 na saída , este modelo é perfeito para o trabalho repetitivo de formatar chaves. Ele prioriza a velocidade e o baixo custo, deixando o raciocínio profundo apenas para a linha "Pro".
- **Qwen 3.6 Flash (`qwen/qwen3.6-flash`)**: Custando US$ 0,25 na entrada e US$ 1,50 na saída , a linha Flash da Alibaba é altamente recomendada para extração massiva. O modelo foi otimizado para lidar de forma extremamente eficiente com tarefas de serialização de dados e código.

**3. A Alternativa Econômica do Google AI Studio**

- **Gemini 3.1 Flash-Lite Preview (`gemini-3.1-flash-lite-preview`)**: O Google expandiu o suporte oficial a _Structured Outputs_ (JSON Schema) para abranger toda a família Gemini, incluindo as versões "Lite". Custando os mesmos US$ 0,25 de entrada e US$ 1,50 de saída , ele preserva a generosa margem de até 65.536 tokens máximos de saída. Ele atua perfeitamente como o formatador do seu JSON final mantendo as coisas dentro do mesmo provedor, mas evitando o peso financeiro da versão Pro.

**4. A Opção de Custo Zero Absoluto (Para Infraestrutura Local)** Como seu objetivo futuro é usar o hardware _bare-metal_ do projeto (uma RTX 2060m), o estado da arte de 2026 permite realizar o preenchimento dessas colunas de graça usando **Decodificação Restrita** (_Constrained Decoding_). Bibliotecas como o **`llguidance`** ou **`XGrammar 2`** interceptam o modelo no seu servidor local (vLLM ou SGLang) e alteram matematicamente as probabilidades dos tokens. Isso leva apenas cerca de 50μs de processamento por token e força até mesmo os menores modelos gratuitos de borda (como o Nemotron Nano 9B V2 ) a imprimirem as 37 colunas perfeitamente, invalidando completamente a necessidade de enviar a Fase 3 para a nuvem.

Aplicando o padrão do Pydantic AI para injetar os dados da Fase 2 em um modelo gratuito ou de classe "Flash" na Fase 3, aquele custo projetado de quase US$ 13 evapora, entregando a planilha pronta com precisão cirúrgica.

---

Sim, exatamente. No design de sistemas agênticos em produção, essa prática é conhecida como **Estado Persistente (Persistent State)** ou **Execução Durável (Durable Execution)**.

Em vez de manter todos os dados empilhados apenas na memória do script (o que faria você perder todo o progresso e os tokens gastos se a API cair ou o script travar na última etapa), o ideal é que o orquestrador grave fisicamente o resultado de cada fase antes de invocar a próxima.

Na prática, o fluxo de arquivos consumíveis para cada repositório funcionaria assim:

1. **Output da Fase 1 (Leitura):** O script raspa o repositório e o codewiki, condensando o conteúdo e salvando um arquivo como `[nome_do_repo]_contexto.md` (ou gravando em um banco de dados local simples como SQLite).
2. **Output da Fase 2 (Enxame):** O script carrega o `[nome_do_repo]_contexto.md` e o envia para os especialistas (Arquiteto, UX, Canibal). O resultado dos debates e das perspectivas é salvo em um novo arquivo, como `[nome_do_repo]_debate.json`.
3. **Output da Fase 3 (Formatação):** O modelo formatador rigoroso lê os arquivos anteriores e enquadra tudo no schema exato, salvando o arquivo `[nome_do_repo]_metadados_finais.json`.
4. **Etapa de Carga (Sem IA):** Um comando determinístico lê o JSON final e usa a integração de planilhas para enviá-lo com segurança para a nuvem.

**Os três grandes benefícios de arquitetar o código dessa forma:**

- **Tolerância a Falhas (Resiliência):** Se a API der um erro de limite de taxa (_rate limit_) bem no meio da Fase 3, a execução é interrompida de forma segura. Quando o script reiniciar, ele não precisa refazer a Fase 1 e a Fase 2; ele apenas lê os arquivos já salvos e retoma o trabalho de onde parou, preservando seus tokens.
- **Auditoria e Observabilidade:** Se um repositório receber uma nota absurda na planilha, você não precisará "adivinhar" o que aconteceu. Basta abrir o arquivo do "debate" gerado na Fase 2 e investigar se a falha de lógica ocorreu na argumentação dos agentes ou se a culpa foi do formatador.
- **Controle de Qualidade Intermediário:** Se no futuro você quiser fazer ajustes (como o rebalanceamento), o LLM revisor poderá consultar os arquivos intermediários em disco sem precisar raspar a web novamente.

---

Para o seu cenário (um pipeline de 3 fases com um "enxame" de agentes rodando em paralelo), o **SQLite é a escolha arquitetural superior e mais segura**.

Embora o uso de arquivos Markdown (ou JSON) soltos em pastas pareça o caminho mais fácil inicialmente, a engenharia de agentes em 2026 já documenta claramente os limites de cada abordagem. Aqui está o porquê você deve optar pelo SQLite:

**1. O "Problema do Markdown" na Comunicação Máquina-a-Máquina**
Arquivos Markdown são excelentes pela transparência humana: você pode abrir o arquivo, ler o que a IA pensou e usar o Git para versionamento. No entanto, o Markdown é um formato feito para leitura humana . Quando você usa arquivos `.md` como estado intermediário, você força a IA a pegar um conhecimento estruturado e "achatá-lo" em prosa, o que exige que o próximo agente da cadeia gaste esforço cognitivo (e tokens) apenas para re-interpretar aquele texto. Com o SQLite, os dados transitam de forma estruturada.

**2. Segurança em Concorrência (O Gargalo do Enxame)**
Na sua Fase 2, você terá três especialistas (Arquiteto, Produto/UX e Canibal) analisando o mesmo repositório simultaneamente. Gravações concorrentes de vários agentes no mesmo sistema de arquivos local podem corromper os dados silenciosamente. O SQLite exige zero configuração de infraestrutura, é um único arquivo local e lida perfeitamente com bloqueios de gravação e concorrência, garantindo a integridade dos dados da sua tríade .

**3. Segurança Transacional**
Se o seu script estiver gravando um arquivo `.md` e a conexão de rede com a API da IA cair exatamente naquele milissegundo, você ficará com um arquivo corrompido ou preenchido pela metade. O SQLite possui segurança transacional nativa. Se a extração falhar no meio, a transação inteira é revertida (rollback) e seu banco de dados permanece limpo, permitindo que o script simplesmente tente de novo .

**Como estruturar isso na prática:**
Em vez de pastas cheias de arquivos, o seu orquestrador Python/Rust deve criar um único arquivo `soda_memoria_etl.db`.

Dentro dele, você cria tabelas simples que representam o seu "estado" da tarefa:
* **Tabela `repositorios`**: Guarda as URLs, o status de processamento (Pendente, Em Fase 1, Concluído) e o contexto bruto raspado do código/wiki.
* **Tabela `debates_enxame`**: Cada linha aqui guarda a visão individual gerada pelos especialistas (Arquiteto, UX, Canibal) atrelada ao ID do repositório correspondente.
* **Tabela `resultado_final`**: Onde a Fase 3 consolida e grava as 37 colunas perfeitamente validadas.

Dessa forma, você transforma o seu SQLite na camada oficial de comunicação e memória dos agentes. Quando todos os repositórios estiverem com o status "Concluído" no banco, seu script só precisará ler a tabela final e despachar tudo de uma vez para o Google Sheets através de uma atualização em lote.

---

Você não precisa escrever todo o código SQL da tabela manualmente, mas também não é recomendado deixar o agente "adivinhar" e criar a estrutura do banco de dados por conta própria.

A solução ideal e mais segura é **gerar o schema do SQLite automaticamente a partir do seu modelo Pydantic**.

Como definimos que a Fase 3 usará o Pydantic AI para forçar a IA a preencher as 37 colunas corretamente (Decodificação Restrita), você já terá uma classe `BaseModel` no Python definindo exatamente o nome e o tipo de cada uma das colunas. Você pode usar bibliotecas do ecossistema (como integrações entre Pydantic e SQLAlchemy ou SQLModel) para traduzir esses modelos diretamente para a estrutura do banco de dados. Isso cria as tabelas no SQLite automaticamente antes da execução e garante que o banco de dados estará sempre 100% sincronizado com o formato do JSON que a IA vai gerar.

Para que esse banco de dados atenda aos requisitos do projeto, ao gerar a tabela a partir do seu modelo, aplique as seguintes práticas de engenharia de dados recomendadas para SQLite:

- **Chaves Primárias Explícitas:** Force a criação de um ID real (ex: `event_id INTEGER PRIMARY KEY`) em vez de depender das chaves implícitas de linha do SQLite.
- **Tipagem Rígida:** Garanta que o modelo passe as afinidades corretas (`TEXT`, `INTEGER`) para as colunas; embora o SQLite seja flexível, declarar os tipos corretamente mantém a consistência dos dados gerados.
- **Timestamps de Auditoria:** Inclua campos automáticos de `created_at` e `updated_at` no modelo para que você saiba exatamente a data e hora em que a IA processou aquele repositório específico.

Dessa forma, você escreve a estrutura das 37 colunas apenas uma vez no Python, e ela servirá tanto como a "trava matemática" que impede a IA de errar o JSON, quanto como o "molde" que constrói a tabela do banco de dados.