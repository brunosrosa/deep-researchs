**Objetivo da Pesquisa:** Mapear arquiteturas de software, fluxos de _User Experience_ (UX) e frameworks jurídicos (LGPD/ECA/COPPA) para a criação de "contas dependentes" e supervisão parental em redes sociais hiperlocais e sistemas de controle de acesso (PropTech).

**Contexto do Sistema (O Problema):** O "CommunitZ" é uma rede social hiperlocal focada em vizinhanças (bairros e condomínios) que exige identidade real validada por KYC (ex: Gov.br) para erradicar o anonimato tóxico. A plataforma possui um braço "PropTech" (usado para abrir catracas e portas do condomínio via celular) e um braço "Social/Econômico" (fóruns do bairro, votações cívicas e mercado de _Micro-Gigs_). O dilema: Adolescentes (menores de 18 anos) precisam usar o app para acessar suas casas e interagir com amigos do condomínio, mas não podem ter exposição irrestrita a fóruns públicos de adultos, votações orçamentárias ou ofertas de trabalho. Precisamos de um modelo de "Conta Dependente", atrelada ao KYC do responsável legal, que ofereça proteção total contra predadores e cyberbullying, dê aos pais capacidade de auditoria em casos de emergência, mas preserve a privacidade psicológica do adolescente no dia a dia.

**O que JÁ TEMOS na arquitetura (Excluir dos resultados da busca):**

- _Desacoplamento de Acesso:_ Já temos uma arquitetura onde o banco de dados de "Acesso Físico" é separado do banco "Social"._KYC de Adultos:_ Já usamos Provas de Conhecimento Zero (ZKP) e integrações com Gov.br para atestar que os pais são humanos reais e moram no local.

**O que BUSCAMOS descobrir (Foco da Pesquisa Externa):**

- **Arquitetura de Contas Dependentes (Sub-Accounts):** Como os ecossistemas digitais modernos (Web2 avançada, Web3 ou carteiras de identidade descentralizada - DID) estruturam o vínculo criptográfico e o provisionamento de contas para menores atreladas a um "Master Profile" (conta do responsável)?
- **Auditoria Parental vs. Privacidade Psicológica:** Quais são os _designs_ de interface (UX) e protocolos de segurança mais recomendados para que pais recebam "Alertas de Risco" (ex: a IA da plataforma detectou assédio ou linguagem predatória no chat do filho) _sem_ dar aos pais acesso irrestrito para ler todas as conversas privadas banais do adolescente?
- **Limites Legais de Participação (ECA / LGPD / COPPA):** Quais são as restrições jurídicas atuais para menores participarem de ferramentas cívicas (como responder a enquetes de condomínio) ou de plataformas de escambo/favores locais? Como plataformas educacionais e familiares lidam com o consentimento parental granular?

**Palavras-chave Sugeridas:**

- "Parental control architecture delegated accounts Web3 DID"
- "Privacy-preserving parental audit child social network UX"
- "COPPA LGPD compliance teen sub-accounts platform design"
- "PropTech minor access control and social boundaries"