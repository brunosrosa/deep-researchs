---
sticker: lucide//atom
---
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

## 4. Ecossistema de Funcionalidades (O "Canivete Suíço" Local e Modular)

A arquitetura de funcionalidades do CommunitZ não é estática. Para evitar a sobrecarga cognitiva (a "Armadilha do Canivete Suíço", onde o excesso de opções paralisa o usuário recém-chegado), a interface utiliza o conceito vital de **Desbloqueio Progressivo**.

A premissa é clara: um canal não nasce complexo; ele **conquista** a complexidade conforme a comunidade ganha densidade e maturidade. Um canal com cinco pessoas não precisa de um painel de votação orçamentária ou de um mercado complexo; ele precisa apenas de espaço para diálogo e ferramentas de utilidade imediata. Conforme o canal atinge marcos pré-definidos de engajamento — como volume de usuários validados, tempo de existência ativa e interações consistentes — o sistema "destrava" gradativamente novas abas. Esse formato cria um _game design_ natural de cooperação: os moradores são incentivados a convidar vizinhos e manter o espaço ativo justamente para liberar as ferramentas mais avançadas da plataforma.

Abaixo, detalhamos o arsenal completo que um canal maduro pode ostentar, dividido por suas áreas de atuação:

### 4.1 Abas de Comunicação, Utilidade e Conhecimento

O foco desta camada é criar a base da interação diária e consolidar a memória do grupo.

- **Chat Principal (A Praça Pública):** A comunicação base, permitindo interações assíncronas e síncronas. É o local do fluxo rápido de conversas do dia a dia.
- **Salas Comunitárias (Áudio/Vídeo Estruturado):** Espaços para comunicação instantânea, mas com recursos avançados de moderação. Permitem a criação de sub-canais com limites estritos de capacidade (ex: "Sala de Reunião do Condomínio - Máximo 10 pessoas" ou "Palco de Avisos", onde apenas a liderança fala e os demais ouvem).
- **Wiki Comunitária (A Memória Perene):** O antídoto contra o caos dos grupos tradicionais de mensagens, onde a informação útil se perde na rolagem. A Wiki é o repositório estruturado do canal: abriga as regras de convivência, tutoriais locais (ex: "Como funciona a coleta de lixo no bairro"), histórico de atas de reunião e indicações perenes. A edição é restrita a usuários com papéis de Curadoria ou liderança.
- **Condensador de Notícias/Mídias (O Modo Single-Player):** Uma funcionalidade crucial para reter usuários enquanto a comunidade ainda é pequena. Um Bot integrador atua como um "agregador inteligente" focado exclusivamente no polígono da região. Ele varre fontes oficiais e puxa alertas da defesa civil, relatórios de trânsito em tempo real, avisos de corte de água/luz das concessionárias locais e notícias hiper-locais. O usuário entra para ver a utilidade da automação, e acaba ficando para a interação comunitária que se forma em torno dela.
- **Mural de Avisos (Top-Down Oficial):** Uma aba asséptica e imune a comentários casuais. É o equivalente a um quadro de avisos trancado no térreo de um prédio. Destinada exclusivamente para comunicados oficiais de prefeituras, síndicos validados ou associações de moradores.
- **Eventos (O Calendário Vivo):** Um painel georreferenciado e temporal. Permite que a comunidade marque assembleias, feiras de rua, eventos culturais ou mutirões. Os usuários recebem lembretes baseados em sua proximidade do local do evento.
- **Álbuns e Mídia:** O arquivo visual comunitário, organizado por pastas (ex: "Festa Junina 2024", "Obras na Praça Central"), garantindo que imagens importantes não se percam no chat.
- **Lista de Cidadãos (O Diretório Seguro):** Uma lista de membros que aplica um controle severo de privacidade. Por padrão, mostra apenas informações mínimas. O perfil completo, contatos secundários e a localização exata (se o usuário desejar expor) só são "desbloqueados" caso ocorra uma conexão de "amizade" dupla (ambos se aceitam) dentro da plataforma.

### 4.2 Abas de Economia, Zeladoria e Sobrevivência

Esta camada transforma a interação social em impacto tangível, financeiro e físico no mundo real.

- **Marketplace (A Economia Circular Segura):** Muito além de um "classificados". É um mercado nativo focado em produtos e serviços profissionais (ex: venda de itens usados de valor, prestadores de serviço com CNPJ validados como encanadores ou eletricistas). Para evitar golpes, possui um sistema próprio de transação financeira, onde o pagamento pode ficar retido (Escrow) até a confirmação da entrega.
- **Micro-Gigs (O Mercado de Micro-Trabalho):** Um sub-mercado de serviços hiper-locais e informais. É o espaço para a "economia do final de semana": o passeador de cães da rua de baixo, a adolescente oferecendo serviços de babá, ou a montagem rápida de um móvel. É o incentivo à geração de renda ágil entre vizinhos.
- **Zeladoria Estruturada (O Fim do Tribunal do WhatsApp):** A aba para abertura de chamados formais, perfeita para condomínios e bairros. Em vez de reclamações caóticas, o usuário abre um ticket estruturado (ex: "Lâmpada queimada na Rua X"). O sistema registra, notifica o gestor responsável (Síndico/Prefeitura, se integrados) e os demais moradores podem "apoiar" o chamado, gerando um mapa de calor das prioridades do local. Denúncias anônimas não são permitidas; a responsabilidade da denúncia (Skin in the Game) é total.
- **Botão SOS (Alerte de Vizinhança Crítico):** Uma ferramenta de alta prioridade. Usado exclusivamente para alertas graves e imediatos (crime em andamento, emergência médica, animal perdido perigosamente, princípio de incêndio). Disparar este alerta "fura" as configurações padrão de silenciamento de notificações dos usuários validados da região, garantindo visibilidade instantânea.
- **Estatísticas (O Painel Analítico Público):** Dashboards abertos que exibem a saúde da comunidade. Mostra níveis de engajamento, hashtags e tópicos mais discutidos da semana, e os horários de pico. Uma ferramenta poderosa para os Operadores entenderem as necessidades da base e para os comerciantes locais direcionarem suas ofertas.

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