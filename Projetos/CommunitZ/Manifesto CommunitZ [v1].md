# CommunitZ - Product Requirements Document (PRD)

## 1. Visão Central e Princípios Norteadores

O CommunitZ é uma rede social baseada em **auto-responsabilidade geoespacial**. O objetivo é solucionar a hiper-fragmentação, o anonimato tóxico e a falta de pertencimento local da internet moderna. A plataforma une a dinâmica de gestão coletiva de canais (inspirada no IRC/Undernet) com uma interface rica e utilitária (inspirada no Discord e ferramentas de gestão), criando um "Sistema Operacional de Vizinhança".

O princípio norteador é o **Pessimismo da Razão na Engenharia Social**: assumimos que a natureza humana busca brechas, logo, a arquitetura de regras (O Bot) dita os limites imutáveis, enquanto as pessoas gerenciam a convivência local dentro dessas fronteiras.

## 2. Identidade e Acesso (O Fim do FAKE)

A premissa base é a aversão absoluta ao anonimato interativo. Para interagir, comprar ou votar, o usuário deve possuir identidade validada.

- **A "Jornada de Entrada" e o Funil de Conversão:** A plataforma utiliza o atrito da validação de identidade (KYC) como ferramenta de retenção através do conceito de _FOMO_ (Fear Of Missing Out).
    - **Modo Fantasma (Read-Only):** Visitantes ou usuários sem cadastro completo podem navegar, ler a Wiki, visualizar o Marketplace e buscar canais geolocalizados. São invisíveis para o ecossistema interativo. Eles consomem o valor da plataforma antes de "pagarem" o preço da validação.
    - **Turistas (Perfil Validado, Vínculo Fraco):** Contas com identidade real validada. Podem interagir, conversar e realizar comércio na maioria dos canais, independentemente de onde moram fisicamente.
    - **Cidadãos (Perfil Validado, Vínculo Forte):** Contas com validação de residência ou vínculo comprovado com o polígono geográfico (Geofencing) de um canal específico. São os únicos com direito de **votar em eleições** e se **candidatar a cargos de liderança** (Operador/Moderador).
- **Selo de Morador (KYC sob Demanda):** Para não matar a aquisição inicial, a validação profunda de endereço não é mandatória no D0 (dia zero). São os próprios canais (através de suas constituições internas) que podem ou não exigir o "Selo de Morador" para liberar interações específicas.

## 3. Dinâmica de Canais e Geoespacialidade

A rede organiza-se através de polígonos geográficos e nomenclaturas não redundantes para evitar a dispersão do _Efeito de Rede_.

- **A Chave Única:** Canais são indexados pelo vetor duplo `Nome + Região` (ex: `#Ciclismo_Urbano_Aguas_Claras`). Isso garante que não existam feudos competindo pela mesma pauta no mesmo local geográfico.
- **Canais Públicos (Geolocalizados):** Atendem uma área física real (País, Estado, Cidade, Bairro, Condomínio, Parque). Nascem sem "dono" humano. A gestão inicial é feita exclusivamente pelo Bot do sistema até que o canal atinja o **Quórum** de desbloqueio eleitoral.
- **Canais Privados (Temáticos/Empresariais):** Espaços geridos por indivíduos, empresas ou grupos de interesse restrito. Não sofrem intervenção democrática (não há eleições).
    - _Transição e Descoberta:_ Utilizam **TAGS Guarda-chuva** para serem indexados nos Canais Públicos de uma região (ex: o grupo privado de RPG do Bairro X aparece listado no diretório do Bairro X), garantindo visibilidade para os criadores sem a penalidade de perderem a soberania sobre o próprio grupo.

## 4. Ecossistema de Funcionalidades (O "Canivete Suíço")

Para evitar a sobrecarga cognitiva (Armadilha do Canivete Suíço), a interface utiliza o **Desbloqueio Progressivo**. Canais recém-nascidos começam enxutos e "destravam" novas abas conforme atingem marcos de engajamento (quantidade de membros, volume de interações).

### Abas de Comunicação e Utilidade

- **Chat Principal:** Comunicação assíncrona/síncrona padrão.
- **Salas Comunitárias (Áudio/Vídeo):** Comunicação instantânea com possibilidade de sub-canais de tamanho restrito.
- **Wiki Comunitária:** Documentação perene de regras, tutoriais locais ou recomendações (A "memória" do canal).
- **Condensador de Notícias/Mídias (Automação):** Bot agregador que captura e exibe alertas de defesa civil, trânsito, corte de água/luz e notícias hiper-locais baseadas no polígono da região. (Gera utilidade no formato "Single-Player" enquanto o canal está vazio).
- **Mural de Avisos (Site Oficial/Integração):** Aba para comunicados oficiais de prefeituras, síndicos ou associações de bairro, imune a comentários casuais.
- **Eventos:** Calendário geo-georreferenciado da comunidade.
- **Álbuns e Mídia:** Repositório visual comunitário.
- **Lista de Cidadãos:** Diretório com controle severo de privacidade. Informações ricas só são expandidas mediante a "conexão/amizade" dupla entre os usuários.

### Abas de Economia e Emergência

- **Marketplace (A Economia Circular):** Focado em produtos e serviços profissionais (ex: Encanador CNPJ, vendas de itens usados). Possui sistema nativo de transação financeira e taxa de retenção.
- **Micro-Gigs (Empregos de Bairro):** Sub-mercado focado em prestação de pequenos serviços rápidos pessoa-física (ex: passeador de cães, babá de fim de semana).
- **Zeladoria e Alertas (SOS):** Aba de abertura de chamados formais para zeladores/síndicos e "Botão Pânico" geolocalizado para avisos críticos da comunidade (crime, animal perdido, acidente). Não admite postagens anônimas (skin in the game).
- **Estatísticas (Painel Analítico):** Dashboards de uso público detalhando engajamento, hashtags e picos de uso.

## 5. Governança e Liderança Comunitária

A hierarquia local é eleita democraticamente, possui tempo de expiração e contrapesos rigorosos para evitar pequenos tiranos (_Astroturfing_ e Coronelismo).

### Mecânica Eleitoral

- **Quórum Inicial:** Define o mínimo de "Cidadãos" validados necessários para abrir a primeira eleição de um canal público.
- **Quarentena Eleitoral:** "Turistas" que acabaram de converter sua conta para "Cidadãos" de um novo polígono sofrem um período de carência (cooldown) antes de terem direito a voto ou candidatura. Blinda a comunidade contra compra de votos e hordas coordenadas.
- **Rate-Limit de Entrada:** Picos anômalos de acesso externo a um canal local acionam bloqueios automatizados (captchas ou rebaixamento para Modo Fantasma) para impedir ataques de brigadas virtuais.

### Papéis e Benefícios

O sistema reconhece que trabalho voluntário não se sustenta sem incentivos alinhados.

1. **Operador (O "Prefeito"):** Eleito pela comunidade por mandato de prazo definido. Fica no topo da lista.
    
    - _Poderes:_ Escolhe Moderadores, direciona as verbas do "Caixa do Canal" (com supervisão do sistema), dita pautas na Central de Governança.
    - _Benefícios:_ Isenção total de taxas de transação no Marketplace do seu canal; acesso a relatórios avançados de dados da região; prova social (score cívico alto).
        
2. **Moderador (Os "Xerifes"):** Nomeados pela chapa eleita. Aplicam _strikes_ e monitoram o fluxo.
    
    - _Poderes:_ Retenção de mensagens, emissão de _strikes_ primários, submissão de casos ao Tribunal Cego.
    - _Benefícios:_ Recebem "Pontos Cívicos" que se convertem em recompensas ou descontos locais. O tempo como moderador é pré-requisito funcional para a candidatura ao cargo de Operador.
        
3. **Curadores (Os "Bibliotecários"):** Papel meritocrático (ganho orgânico).
    
    - _Poderes:_ Curadoria fina da Wiki e do Condensador de Notícias.
    - _Benefícios:_ _Boost_ algorítmico em seus comunicados; elegíveis a "Bounties" (pagamentos do fundo comunitário por tarefas específicas).

### Limites do Poder (Anti-Tirania)

- O Operador obedece o Bot (a "Constituição Global"). Se um Operador tentar comandos que violem os T&C da plataforma, ele é suspenso por "Tentativa de Ruptura".
- Os mandatos expiram, proibindo reeleição vitalícia.
- Existe a mecânica do **Recall (Impeachment):** Assinaturas digitais de % da base validada derrubam o mandato instantaneamente.

## 6. O Sistema de Justiça (Strikes e Tribunal)

A aplicação de punições visa a educação e escalada de atrito, separando a figura do líder local da aplicação de penas graves (evitando vinganças offline).

### Escalada Gradual Punitiva

1. **Aviso Formal:** Bot alerta infração leve (Log registrado).
2. **Suspensão Aguda (Ex: 30 minutos a horas):** Bloqueio de digitação/ação.
3. **Suspensão Grave (Ex: 24h a dias):** Reincidência de infrações em curto período.
4. **O Desterro (Ex: 1 mês):** Infrator contumaz é rebaixado compulsoriamente a "Modo Fantasma" em todos os canais do ecossistema CommunitZ.
5. **Banimento Profundo (Anos a Décadas):** Reservado a crimes reais ou violações irreparáveis dos termos.

### Mecanismos de Moderação

- **O Log Incorruptível:** Toda ação punitiva é registrada de forma imutável (e com justificativa explícita atrelada a uma regra) na Central de Governança. Fim da censura silenciosa.
- **Tribunal Cego Descentralizado:** O Operador/Moderador não pode banir permanentemente ou acionar denúncias policiais por vontade própria. Eles emitem a _recomendação_ ao sistema. O sistema sorteia _N_ "Cidadãos" de outros polígonos, com alto Score Cívico, para auditar o caso às cegas (sem ver o nome das partes). A decisão do tribunal é soberana.
- **Exportação Legal:** Facilitação de empacotamento de _logs_ de chat e atividades para geração de Boletins de Ocorrência, desestimulando crimes cibernéticos ou ameaças de injúria.

## 7. Reputação (Score Cívico) e Transparência Financeira

A economia do CommunitZ depende da clareza inquestionável dos dados.

### O Score Cívico (Gamificação Útil)

Não há "Likes". A reputação é uma métrica tridimensional utilitária. O aumento da reputação diminui o tempo de "quarentenas", destrava privilégios e valida candidaturas.

- **Eixo Comercial:** Avaliações de idoneidade em compras/vendas.
- **Eixo Governamental:** Frequência de voto e atuações justas em Tribunais Cegos.
- **Eixo Zeladoria:** Contribuições reais validadas para a Wiki e relatos úteis na aba de SOS/Alertas.

### O Fundo Comunitário e Transparência

Uma porcentagem (Taxa) das transações realizadas no Marketplace do canal é dividida: 50% para a Plataforma e 50% para o **Caixa do Canal**, gerido pela liderança local. A fiscalização é feita através do conceito de _Extrato de Vidro_.

- **Agregação de Dados Públicos:** O Cidadão comum visualiza as "Entradas" de forma consolidada e agregada (ex: "+R$100 de Taxas" sem identificar quem comprou de quem), para evitar vazamento de privacidade financeira de vizinhos.
- **Log Completo Restrito:** A liderança possui a visão nominal estrita para mediação de golpes.
- **Travas de Liquidez (**_**Smart-Locks**_**):**
    - Gastos Micro: Liberados pela chapa operadora.
    - Gastos Macro: Retidos pelo bot até que o Operador abra e vença um "Pleito Orçamentário" na Central de Governança (ex: Votação para alocar verba na pintura da praça).
- **Sistema de Escrow para Serviços Local:** O dinheiro público do canal só é transferido a prestadores mediante envio de "Prova de Entrega" (foto do serviço) e validação da comunidade na aba de Transparência.

## 8. Estratégia de Bootstrapping (Go-To-Market / Monetização)

Para resolver o problema do deserto inicial (_Cold Start Problem_), o modelo financeiro ignora Ads (Adsense) no início e foca em resolver dores agudas B2B2C.

- **Micro-SaaS de Condomínios (Vantagem Apelona):** Vender o ecossistema pronto (com poder de voto auditável, zeladoria sem denúncia anônima e notificações furadoras de silêncio) para Síndicos. O síndico paga uma mensalidade pelo serviço B2B; como colateral, força dezenas/centenas de moradores a se cadastrarem como "Cidadãos Validados" instantaneamente na região.
- **Selo Vitrine (Comércio Local):** Planos mensais de baixo custo para CNPJs locais ganharem destaque no topo do Marketplace e das buscas da região, além de furarem os limites de postagem gratuita.

_Fim do Documento Base - Versão 1.0_