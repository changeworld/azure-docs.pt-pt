---
title: Novidades Notas de lançamento - Diretório Ativo Azure / Microsoft Docs
description: Saiba o que há de novo com o Azure Ative Directory, como as mais recentes notas de lançamento, questões conhecidas, correções de bugs, funcionalidade depreciada e próximas alterações.
services: active-directory
author: msmimart
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 491908b651cd2b875fcfeed4c55d34f0e8c5cfa1
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80802514"
---
# <a name="whats-new-in-azure-active-directory"></a>Quais as novidades no Azure Ative Directory?

>Seja notificado sobre quando revisitar esta página para atualizações copiando ![e colando](./media/whats-new/feed-icon-16x16.png) este URL: `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` no leitor de feed do leitor de feed RSS.

A Azure AD recebe melhorias de forma contínua. Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece-lhe informações sobre:

- Os últimos lançamentos
- Problemas conhecidos
- Correções de erros
- Funcionalidade preterida
- Planos para alterações

Esta página é atualizada mensalmente, por isso revisite-a regularmente. Se procura itens com mais de seis meses, pode encontrá-los no [Arquivo para o que há de novo no Diretório Ativo Azure.](whats-new-archive.md)

---

## <a name="march-2020"></a>Março de 2020

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march-2021"></a>Contas de Diretório Ativo Azure não geridas em atualização B2B para março de 2021

**Tipo:** Plano de mudança  
**Categoria de serviço:** B2B  
**Capacidade do produto:** B2B/B2C
 
A partir de 31 de março de **2021,** a Microsoft deixará de apoiar o resgate de convites através da criação de contas não geridas do Azure Ative Directory (Azure AD) e dos inquilinos para cenários de colaboração B2B. Em preparação para isso, encorajamo-lo a optar por [e-mail de autenticação de senha única.](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode)

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>Os utilizadores com a função de acesso predefinido estarão no âmbito do provisionamento

**Tipo:** Plano de mudança  
**Categoria de serviço:** Provisionamento de Aplicativos  
**Capacidade do produto:** Gestão do ciclo de vida de identidade
 
Historicamente, os utilizadores com a função de acesso predefinido têm estado fora de âmbito para o provisionamento. Ouvimos comentários de que os clientes querem que os utilizadores com este papel estejam no âmbito do provisionamento. Estamos a trabalhar na implementação de uma alteração para que todas as novas configurações de fornecimento permitam aos utilizadores com a função de acesso padrão ser provisionado. Gradualmente, vamos alterar o comportamento das configurações de provisionamento existentes para apoiar o fornecimento de utilizadores com esta função. Não é necessária nenhuma ação do cliente. Publicaremos uma atualização da nossa [documentação](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned) assim que esta mudança estiver em vigor.

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>A colaboração Azure AD B2B estará disponível no Microsoft Azure operado por inquilinos da 21Vianet (Azure China 21Vianet)

**Tipo:** Plano de mudança  
**Categoria de serviço:** B2B  
**Capacidade do produto:** B2B/B2C
 
As capacidades de colaboração Azure AD B2B serão disponibilizadas na Microsoft Azure operada por inquilinos da 21Vianet (Azure China 21Vianet), permitindo aos utilizadores de um inquilino Azure China 21Vianet colaborar em perfeitas colaborações com utilizadores de outros inquilinos Azure China 21Vianet. Saiba mais sobre a [colaboração Azure AD B2B.](https://docs.microsoft.com/azure/active-directory/b2b/)

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Azure AD B2B Colaboração convite redesenho de e-mail

**Tipo:** Plano de mudança  
**Categoria de serviço:** B2B  
**Capacidade do produto:** B2B/B2C
 
Os [e-mails enviados](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) pelo serviço de convite de colaboração Azure AD B2B para convidar os utilizadores para o diretório serão redesenhados para tornar a informação do convite e os próximos passos do utilizador mais claros.

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>As mudanças de política homeRealmDiscovery aparecerão nos registos de auditoria

**Tipo:** Fixo  
**Categoria de serviço:** Auditoria  
**Capacidade do produto:** Relatório de & de monitorização
 
Corrigimos um bug onde as alterações à [política HomeRealmDiscovery](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal) não foram incluídas nos registos de auditoria. Agora poderão ver quando e como a política foi alterada, e por quem. 

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2020"></a>Novas Aplicações Federadas disponíveis na galeria Azure AD App - março de 2020

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** 3ª Integração do Partido
 
Em março de 2020, adicionámos estas 51 novas aplicações com apoio da Federação à galeria de aplicações: 

[Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect), [Zoho One China](https://docs.microsoft.com/azure/active-directory/saas-apps/zoho-one-china-tutorial), [PlusPlus](https://test.plusplus.app/auth/login/azuread-outlook/), [Profit.co App SAML,](https://docs.microsoft.com/azure/active-directory/saas-apps/profitco-saml-app-tutorial) [iPoint Service Provider](https://docs.microsoft.com/azure/active-directory/saas-apps/ipoint-service-provider-tutorial), contexxt.ai [SPHERE,](https://contexxt-sphere.com/login) [Wisdom By Invictus](https://docs.microsoft.com/azure/active-directory/saas-apps/wisdom-by-invictus-tutorial), Flare Digital [Signage,](https://spark-dev.pixelnebula.com/login) [Logz.io - Cloud Observability for Engineers,](https://docs.microsoft.com/azure/active-directory/saas-apps/logzio-cloud-observability-for-engineers-tutorial) [SpectrumU,](https://docs.microsoft.com/azure/active-directory/saas-apps/spectrumu-tutorial) [BizzContact,](https://bizzcontact.app/) [Elqano SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/elqano-sso-tutorial), [MarketSignShare](http://www.signshare.com/), [CrossKnowledge Learning Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/crossknowledge-learning-suite-tutorial), [Netvision Compas,](https://docs.microsoft.com/azure/active-directory/saas-apps/netvision-compas-tutorial) [FCM HUB,](https://docs.microsoft.com/azure/active-directory/saas-apps/fcm-hub-tutorial) [RIB A/S Byggeweb Mobile,](https://apps.apple.com/us/app/docia/id529058757) [GoLinks,](https://docs.microsoft.com/azure/active-directory/saas-apps/golinks-tutorial) [Datadog,](https://docs.microsoft.com/azure/active-directory/saas-apps/datadog-tutorial) [Zscaler B2B User Portal,](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-b2b-user-portal-tutorial) [LIFT,](https://docs.microsoft.com/azure/active-directory/saas-apps/lift-tutorial) [Planview Enterprise One,](https://docs.microsoft.com/azure/active-directory/saas-apps/planview-enterprise-one-tutorial) [WatchTeams,](https://www.devfinition.com/) [Aster,](https://demo.asterapp.io/login) [Skills Workflow](https://docs.microsoft.com/azure/active-directory/saas-apps/skills-workflow-tutorial), [Node Insight](https://admin.nodeinsight.com/AADLogin.aspx), IP [Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/ip-platform-tutorial), [InVision](https://docs.microsoft.com/azure/active-directory/saas-apps/invision-tutorial), [Pipedrive,](https://docs.microsoft.com/azure/active-directory/saas-apps/pipedrive-tutorial) [Showcase Workshop](https://app.showcaseworkshop.com/), Greenlight Integration Platform , [Greenlight](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-integration-platform-tutorial) [Compliant Access Management](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-compliant-access-management-tutorial), [Grok Learning,](https://docs.microsoft.com/azure/active-directory/saas-apps/grok-learning-tutorial) [Miradore Online,](https://login.online.miradore.com/) [Khoros Care,](https://docs.microsoft.com/azure/active-directory/saas-apps/khoros-care-tutorial) [AskYourTeam,](https://docs.microsoft.com/azure/active-directory/saas-apps/askyourteam-tutorial) [TruNarrative,](https://docs.microsoft.com/azure/active-directory/saas-apps/trunarrative-tutorial) [Smartwaiver,](https://www.smartwaiver.com/m/user/sw_login.php?wms_login) [Biza Estúdio gi para Automação de Processos Digitais,](https://docs.microsoft.com/azure/active-directory/saas-apps/bizagi-studio-for-digital-process-automation-tutorial) [insuiteX,](https://www.insuite.jp/) [sybo,](https://www.systexsoftware.com.tw/) [Britive](https://docs.microsoft.com/azure/active-directory/saas-apps/britive-tutorial), [WhosOffice](https://docs.microsoft.com/azure/active-directory/saas-apps/whosoffice-tutorial), [E-days,](https://docs.microsoft.com/azure/active-directory/saas-apps/e-days-tutorial) [Kollective SDN,](https://portal.kollective.app/login) [Witivio,](https://app.witivio.com/) [Playvox,](https://my.playvox.com/login) [Korn Ferry 360](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-360-tutorial), [Campus Café,](https://docs.microsoft.com/azure/active-directory/saas-apps/campus-cafe-tutorial) [Catchpoint,](https://docs.microsoft.com/azure/active-directory/saas-apps/catchpoint-tutorial) [Code42](https://docs.microsoft.com/azure/active-directory/saas-apps/code42-tutorial)

Para obter mais informações sobre as aplicações, consulte [a integração da aplicação SaaS com o Azure Ative Directory](https://aka.ms/appstutorial). Para mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte Lista da sua aplicação na galeria de [aplicações do Diretório Ativo Azure](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Colaboração Azure AD B2B disponível em inquilinos do Governo azure

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2B  
**Capacidade do produto:** B2B/B2C
 
As funcionalidades de colaboração Azure AD B2B estão agora disponíveis entre alguns inquilinos do Governo Azure.  Para saber se o seu inquilino é capaz de usar estas capacidades, siga as instruções em Como posso dizer se a [colaboração B2B está disponível no meu inquilino do Governo dos EUA?](https://docs.microsoft.com/azure/active-directory/b2b/current-limitations#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>Integração do Azure Monitor para A Tetos Azure já está disponível no Governo do Azure

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Reportagem  
**Capacidade do produto:** Relatório de & de monitorização
 
A integração do Azure Monitor com os registos da AD Azure já está disponível no Governo Azure. Pode encaminhar os Registos De AD Azure (Registos de Auditoria e Login) para uma conta de armazenamento, Hub de Eventos e Log Analytics. Consulte a [documentação detalhada,](https://aka.ms/aadlogsinamd) bem como os planos de [implementação para reporte e monitorização](https://docs.microsoft.com/azure/active-directory/reports-monitoring/plan-monitoring-and-reporting) para cenários de AD Azure.

---

### <a name="identity-protection-refresh-in-azure-government"></a>Atualização da Proteção de Identidade no Governo de Azure

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Proteção de Identidade  
**Capacidade do produto:** Proteção de & de Segurança de Identidade

Estamos entusiasmados por partilhar que já lançámos a renovada experiência de [Proteção](https://aka.ms/IdentityProtectionDocs) de Identidade azure no [portal do Governo microsoft Azure.](https://portal.azure.us/) Para mais informações, consulte o nosso post de blog de [anúncios.](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667)

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>Recuperação de desastres: Descarregue e guarde a sua configuração de provisionamento

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Provisionamento de Aplicativos  
**Capacidade do produto:** Gestão do ciclo de vida de identidade
 
O serviço de provisionamento de Anúncios Azure fornece um conjunto rico de capacidades de configuração. Os clientes precisam de ser capazes de guardar a sua configuração para que possam remeter-se mais tarde ou reverter para uma versão conhecida e boa. Adicionámos a capacidade de descarregar a sua configuração de provisionamento como um ficheiro JSON e carregá-la quando precisar. [Saiba mais](https://docs.microsoft.com/azure/active-directory/app-provisioning/export-import-provisioning-configuration).

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>SSPR (reset de senha de autosserviço) requer agora dois portões para administradores no Microsoft Azure operados pela 21Vianet (Azure China 21Vianet) 

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Reset de palavra-passe self-service  
**Capacidade do produto:** Proteção de & de Segurança de Identidade
 
Anteriormente no Microsoft Azure operado pela 21Vianet (Azure China 21Vianet), administra-se a utilizar o reset de palavra-passe self-service (SSPR) para redefinir as suas próprias palavras-passe necessárias apenas um "gate" (desafio) para provar a sua identidade. Nas nuvens públicas e outras nacionais, os administradores geralmente devem usar dois portões para provar a sua identidade ao usar sSPR. Mas como não apoiámos SMS ou telefonemas no Azure China 21Vianet, permitimos a redefinição de uma palavra-passe de um portão por administradores.

Estamos a criar paridade de recursos SSPR entre o Azure China 21Vianet e a nuvem pública. Para a frente, os administradores devem usar dois portões quando utilizarem SSPR. Serão suportadas notificações e códigos de aplicações SMS, chamadas telefónicas e autenticadores. [Saiba mais](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#administrator-reset-policy-differences).

---

### <a name="password-length-is-limited-to-256-characters"></a>O comprimento da palavra-passe é limitado a 256 caracteres

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Autenticação do Utilizador
 
Para garantir a fiabilidade do serviço Azure AD, as palavras-passe dos utilizadores estão agora limitadas em comprimento a 256 caracteres. Os utilizadores com senhas mais longas do que esta serão solicitadas a alterar a sua palavra-passe no login subsequente, quer contactando a sua administração ou utilizando a função de reset de palavra-passe de autosserviço.

Esta alteração foi ativada a 13 de março de 2020, às 10:00 PST (18:00 UTC), e o erro é AADSTS 50052, InvalidPasswordExceedsMaxLength. Consulte o aviso de alteração de [rutura](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#user-passwords-will-be-restricted-to-256-characters) para mais detalhes.

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>Os registos de entrada da Azure AD já estão disponíveis para todos os inquilinos gratuitos através do portal Azure

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Reportagem  
**Capacidade do produto:** Relatório de & de monitorização
 
A partir de agora, os clientes que tenham inquilinos gratuitos podem aceder aos registos de entrada da [Azure AD a partir do portal Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) por um máximo de 7 dias. Anteriormente, os registos de login estavam disponíveis apenas para clientes com licenças Azure Ative Directory Premium. Com esta mudança, todos os inquilinos podem aceder a estes registos através do portal.

> [!NOTE]
> Os clientes ainda precisam de uma licença premium (Azure Ative Directory Premium P1 ou P2) para aceder aos registos de entrada através do Microsoft Graph API e do Azure Monitor.

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>Deprecation da opção de grupos de diretório saem de Configurações Gerais de Grupos no portal Azure

**Tipo:** Depreciado  
**Categoria de serviço:** Gestão de Grupos  
**Capacidade do produto:** Colaboração

Para proporcionar uma forma mais flexível para os clientes criarem grupos de diretórios que melhor satisfaçam as suas necessidades, substituímos a opção grupos de **diretórios** das configurações**gerais** dos **grupos** > no portal Azure com uma ligação à [documentação dinâmica do grupo.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership) Melhorámos a nossa documentação para incluir mais instruções para que os administradores possam criar grupos de todos os utilizadores que incluam ou excluam os utilizadores convidados.

---

## <a name="february-2020"></a>Fevereiro de 2020

### <a name="upcoming-changes-to-custom-controls"></a>Próximas alterações aos controlos personalizados

**Tipo:** Plano de mudança  
**Categoria de serviço:** MFA  
**Capacidade do produto:** Proteção de & de Segurança de Identidade
 
Estamos a planear substituir a pré-visualização de controlos personalizados atuais por uma abordagem que permite que as capacidades de autenticação fornecidas pelo parceiro funcionem perfeitamente com o administrador do Diretório Ativo Azure e experiências de utilizador final. Hoje, as soluções parceiras do MFA enfrentam as seguintes limitações: funcionam apenas depois de ter sido introduzida uma senha; não servem de MFA para autenticação de aumento em outros cenários-chave; e não se integram com funções de gestão de credenciais de utilizador final ou de credencial administrativa. A nova implementação permitirá que os fatores de autenticação fornecidos pelo parceiro funcionem em conjunto com fatores incorporados para cenários-chave, incluindo registo, utilização, reclamações de MFA, autenticação de aumento, reporte e exploração madeireira. 

Os controlos personalizados continuarão a ser suportados na pré-visualização ao lado do novo design até atingir a disponibilidade geral. Nessa altura, daremos tempo aos clientes para migrarem para o novo design. Devido às limitações da abordagem atual, não embarcaremos nos novos fornecedores até que o novo design esteja disponível. Estamos a trabalhar em estreita colaboração com clientes e fornecedores e comunicaremos a linha temporal à medida que nos aproximamos. [Saiba mais](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-custom-controls/ba-p/1144696#).

---

### <a name="identity-secure-score---mfa-improvement-action-updates"></a>Identity Secure Score - Atualizações de ação de melhoria do MFA

**Tipo:** Plano de mudança  
**Categoria de serviço:** MFA  
**Capacidade do produto:** Proteção de & de Segurança de Identidade
 
Para refletir a necessidade de as empresas garantirem a segurança mais alta ao mesmo tempo que aplicam políticas que trabalham com o seu negócio, o Microsoft Secure Score está a remover três ações de melhoria centradas na autenticação de vários fatores (MFA), e adicionando duas.

As seguintes ações de melhoria serão removidas:

- Registe todos os utilizadores para MFA
- Pedir MFA para todos os utilizadores
- Exigir MFA para funções privilegiadas da Azure AD

Serão adicionadas as seguintes ações de melhoria:

- Garantir que todos os utilizadores podem completar o MFA para acesso seguro
- Exigir MFA para funções administrativas

Estas novas ações de melhoria exigirão registar os seus utilizadores ou administradores para mfa em todo o seu diretório e estabelecer o conjunto certo de políticas que se adequam às suas necessidades organizacionais. O principal objetivo é ter flexibilidade, garantindo que todos os seus utilizadores e administradores podem autenticar com múltiplos fatores ou solicitações de verificação de identidade baseadas no risco. Isto pode assumir a forma de definir falhas de segurança que permitem à Microsoft decidir quando desafiar os utilizadores para mfa, ou ter múltiplas políticas que aplicam decisões de âmbito. Como parte destas atualizações de ação de melhoria, as políticas de proteção de base deixarão de ser incluídas nos cálculos de pontuação. [Leia mais sobre o que está por vir no Microsoft Secure Score](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score-whats-coming?view=o365-worldwide).

---

### <a name="azure-ad-domain-services-sku-selection"></a>Sku serviços de domínio Azure AD

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Serviços de Domínio Azure AD  
**Capacidade do produto:** Serviços de Domínio Azure AD
 
Ouvimos comentários de que os clientes da Azure AD Domain Services querem mais flexibilidade na seleção dos níveis de desempenho para os seus casos. A partir de 1 de fevereiro de 2020, passamos de um modelo dinâmico (onde a Azure AD determina o nível de desempenho e preços baseado na contagem de objetos) para um modelo de autosseleção. Agora os clientes podem escolher um nível de desempenho que corresponda ao seu ambiente. Esta mudança também nos permite permitir novos cenários como Florestas de Recursos, e funcionalidades Premium como backups diários. A contagem de objetos é agora ilimitada para todas as SKUs, mas continuaremos a oferecer sugestões de contagem de objetos para cada nível.

**Não é necessária nenhuma ação imediata do cliente.** Para os clientes existentes, o nível dinâmico que estava em uso a 1 de fevereiro de 2020, determina o novo nível padrão. Não existe qualquer preço ou impacto de desempenho como resultado desta mudança. Daqui para a frente, os clientes da Azure AD DS terão de avaliar os requisitos de desempenho à medida que o seu tamanho de diretório e características de carga de trabalho mudarem. A troca entre os níveis de serviço continuará a ser uma operação sem tempo de inatividade, e deixaremos de transferir automaticamente os clientes para novos níveis com base no crescimento do seu diretório. Além disso, não haverá aumentos de preços, e novos preços irão alinhar-se com o nosso atual modelo de faturação. Para mais informações, consulte a [documentação Azure AD DS SKUs](https://docs.microsoft.com/azure/active-directory-domain-services/administration-concepts#azure-ad-ds-skus) e a página de preços dos Serviços de [Domínio Azure AD](https://azure.microsoft.com/pricing/details/active-directory-ds/).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Novas Aplicações Federadas disponíveis na galeria Azure AD App - fevereiro de 2020

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** 3ª Integração do Partido
 
Em fevereiro de 2020, adicionámos estas 31 novas aplicações com apoio da Federação à galeria de aplicações: 

Plataforma de [Patentes IamIP](https://docs.microsoft.com/azure/active-directory/saas-apps/iamip-patent-platform-tutorial), [Experience Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/experience-cloud-tutorial), [NS1 SSO For Azure](https://docs.microsoft.com/azure/active-directory/saas-apps/ns1-sso-azure-tutorial), [Barracuda Email Security Service](https://ess.barracudanetworks.com/sso/azure), [ABa Reporting](https://myaba.co.uk/client-access/signin/auth/msad), Em caso de crise - Portal [Online](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-online-portal-tutorial), Bic [Cloud Design,](https://docs.microsoft.com/azure/active-directory/saas-apps/bic-cloud-design-tutorial) [Apicultor Azure AD Data Connector,](https://docs.microsoft.com/azure/active-directory/saas-apps/beekeeper-azure-ad-data-connector-tutorial) [Korn Ferry Assessments,](https://www.kornferry.com/solutions/kf-digital/kf-assess) [Comando Verkada,](https://docs.microsoft.com/azure/active-directory/saas-apps/verkada-command-tutorial) [Splashtop,](https://docs.microsoft.com/azure/active-directory/saas-apps/splashtop-tutorial) [Syxsense,](https://docs.microsoft.com/azure/active-directory/saas-apps/syxsense-tutorial) [EAB Navigate,](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-tutorial) [New Relíquia (Lançamento Limitado)](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial), [Thulium](https://admin.thulium.com/login/instance), Gestor de [Bilhetes](https://docs.microsoft.com/azure/active-directory/saas-apps/ticketmanager-tutorial), [Modelo de Escolha para Equipas](https://links.officeatwork.com/templatechooser-download-teams), [Beesy,](https://www.beesy.me/index.php/site/login)Sistema de [Suporte à Saúde,](https://docs.microsoft.com/azure/active-directory/saas-apps/health-support-system-tutorial) [MURAL,](https://app.mural.co/signup) [Colmeia,](https://docs.microsoft.com/azure/active-directory/saas-apps/hive-tutorial) [Lavado,](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview) [Wakelet,](https://wakelet.com/login) [Firmex VDR,](https://docs.microsoft.com/azure/active-directory/saas-apps/firmex-vdr-tutorial) [ThingLink para Professores e Escolas,](https://www.thinglink.com/) [Coda,](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial) [NearpodApp](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product), [WEDO,](https://docs.microsoft.com/azure/active-directory/saas-apps/wedo-tutorial) [Convidadores,](https://invitepeople.com/login) [Reimpressões Desk - Artigo Galaxy,](https://docs.microsoft.com/azure/active-directory/saas-apps/reprints-desk-article-galaxy-tutorial) [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-tutorial)

 
Para obter mais informações sobre as aplicações, consulte [a integração da aplicação SaaS com o Azure Ative Directory](https://aka.ms/appstutorial). Para mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte Lista da sua aplicação na galeria de [aplicações do Diretório Ativo Azure](https://aka.ms/azureadapprequest).

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Novos conectores de provisionamento na Galeria de Aplicação da AD Azure - fevereiro de 2020

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** 3ª Integração do Partido
 
Agora pode automatizar a criação, atualização e a eliminar as contas dos utilizadores destas aplicações recentemente integradas:

- [Mixpanel](https://docs.microsoft.com/azure/active-directory/saas-apps/mixpanel-provisioning-tutorial)
- [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-provisioning-tutorial)
- [Azure Databricks](https://docs.microsoft.com/azure/active-directory/saas-apps/azure-databricks-scim-connector-provisioning-tutorial)
- [PureCloud da Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-provisioning-tutorial)
- [Zapier](https://docs.microsoft.com/azure/active-directory/saas-apps/zapier-provisioning-tutorial)

Para obter mais informações sobre como proteger melhor a sua organização utilizando o fornecimento automatizado de conta de utilizador, consulte o fornecimento de [utilizadores automate para aplicações SaaS com AD Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>Suporte da AD Azure para chaves de segurança FIDO2 em ambientes híbridos

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Autenticação do Utilizador
 
Estamos anunciando a pré-visualização pública do suporte da AD Azure para chaves de segurança FIDO2 em ambientes híbridos. Os utilizadores podem agora utilizar chaves de segurança FIDO2 para iniciar sessão nos seus dispositivos Hybrid Azure AD e obter um sinal perfeito para os seus recursos no local e na nuvem. O suporte para ambientes Híbridos tem sido a funcionalidade mais requisitada dos nossos clientes sem palavras-passe desde que inicialmente lançámos a pré-visualização pública para suporte fido2 em dispositivos aderidos ao Azure AD. A autenticação sem palavras-passe utilizando tecnologias avançadas como biometria e criptografia de chaves públicas/privadas proporcionam conveniência e facilidade de utilização enquanto estão seguras. Com esta pré-visualização pública, pode agora usar a autenticação moderna como as chaves de segurança FIDO2 para aceder aos recursos tradicionais do Diretório Ativo. Para mais informações, vá à [SSO para os recursos no local.](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises) 

Para começar, visite as chaves de [segurança FIDO2 para o seu inquilino](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key) para instruções passo a passo. 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>A nova experiência My Account está agora geralmente disponível

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** O meu perfil/conta  
**Capacidade do produto:** Experiências de utilizador final
 
A Minha Conta, a única loja para todas as necessidades de gestão de conta de utilizador final, está agora geralmente disponível! Os utilizadores finais podem aceder a este novo site via URL, ou no cabeçalho da nova experiência My Apps. Saiba mais sobre todas as capacidades de self-service que a nova experiência oferece na [Visão Geral do Portal da Minha Conta.](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview)

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>Atualização do URL do site da minha conta para myaccount.microsoft.com

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** O meu perfil/conta  
**Capacidade do produto:** Experiências de utilizador final
 
A nova experiência de utilizador final da `https://myaccount.microsoft.com` My Account irá atualizar o seu URL para o próximo mês. Encontre mais informações sobre a experiência e todas as capacidades de self-service da conta que oferece aos utilizadores finais no [portal My Account.](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview)

---
 
## <a name="january-2020"></a>Janeiro de 2020
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>O novo portal My Apps já está geralmente disponível

**Tipo:** Plano de mudança  
**Categoria de serviço:** Minhas Apps  
**Capacidade do produto:** Experiências de utilizador final
 
Atualize a sua organização para o novo portal My Apps que já está geralmente disponível! Encontre mais informações sobre o novo portal e coleções [na Create collections no portal My Apps.](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections)

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>Espaços de trabalho em Azure AD foram renomeados para coleções

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Minhas Apps   
**Capacidade do produto:** Experiências de utilizador final
 
Os espaços de trabalho, os administradores de filtros podem configurar para organizar as suas aplicações de utilizadores, serão agora referidos como coleções. Encontre mais informações sobre como configurá-las na [Create collections no portal My Apps](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections).

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>Inscrição e inscrição do telefone Azure AD B2C e inscrição utilizando a política personalizada (Visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2C - Gestão de Identidade do Consumidor  
**Capacidade do produto:** B2B/B2C
 
Com o registo do número de telefone e o registo, os desenvolvedores e empresas podem permitir que os seus clientes se inscrevam e inscrevam-se usando uma senha única enviada para o número de telefone do utilizador através de SMS. Esta funcionalidade também permite ao cliente alterar o seu número de telefone se perder o acesso ao seu telemóvel. Com o poder das políticas personalizadas, o registo telefónico e o registo permitem que os desenvolvedores e empresas comuniquem a sua marca através da personalização da página. Descubra como [configurar o registo telefónico e iniciar sessão com políticas personalizadas em Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/phone-authentication).
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Novos conectores de provisionamento na Galeria de Aplicação da AD Azure - janeiro 2020

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** 3ª Integração do Partido
 
Agora pode automatizar a criação, atualização e a eliminar as contas dos utilizadores destas aplicações recentemente integradas:

- [Promapp]( https://docs.microsoft.com/azure/active-directory/saas-apps/promapp-provisioning-tutorial)
- [Zscaler Private Access](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-private-access-provisioning-tutorial)

Para obter mais informações sobre como proteger melhor a sua organização utilizando o fornecimento automatizado de conta de utilizador, consulte o fornecimento de [utilizadores automate para aplicações SaaS com AD Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Novas Aplicações Federadas disponíveis na galeria Azure AD App - janeiro de 2020

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** 3ª Integração do Partido
 
Em janeiro de 2020, adicionámos estas 33 novas aplicações com apoio da Federação à galeria de aplicações: 

[JOSA](https://docs.microsoft.com/azure/active-directory/saas-apps/josa-tutorial), [Fastly Edge Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/fastly-edge-cloud-tutorial), [Terraform Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/terraform-enterprise-tutorial), [Spintr SSO,](https://docs.microsoft.com/azure/active-directory/saas-apps/spintr-sso-tutorial) [Abibot Netlogistik,](https://azuremarketplace.microsoft.com/marketplace/apps/aad.abibotnetlogistik) [SkyKick](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access), [Upshotly](https://docs.microsoft.com/azure/active-directory/saas-apps/upshotly-tutorial), [LeaveBot,](https://leavebot.io/#home) [DataCamp,](https://docs.microsoft.com/azure/active-directory/saas-apps/datacamp-tutorial) [TripActions,](https://docs.microsoft.com/azure/active-directory/saas-apps/tripactions-tutorial) [SmartWork,](https://www.intumit.com/english/SmartWork.html) [Dotcom-Monitor,](https://docs.microsoft.com/azure/active-directory/saas-apps/dotcom-monitor-tutorial) [SSOGEN - Azure AD SSO Gateway para oracle E-Business Suite - EBS, PeopleSoft, e JDE](https://docs.microsoft.com/azure/active-directory/saas-apps/ssogen-tutorial), [Hospedado MyCirqa SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-mycirqa-sso-tutorial), [Yuhu Property Management Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/yuhu-property-management-platform-tutorial), [LumApps](https://sites.lumapps.com/login), [Upwork Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/upwork-enterprise-tutorial), [Talentsoft](https://docs.microsoft.com/azure/active-directory/saas-apps/talentsoft-tutorial), [SmartDB for Microsoft Teams,](http://teams.smartdb.jp/login/) [PressPage,](https://docs.microsoft.com/azure/active-directory/saas-apps/presspage-tutorial) [ContractSafe Saml2 SSO,](https://docs.microsoft.com/azure/active-directory/saas-apps/contractsafe-saml2-sso-tutorial) [Maxient Conduct Manager Software,](https://docs.microsoft.com/azure/active-directory/saas-apps/maxient-conduct-manager-software-tutorial) [Helpshift](https://docs.microsoft.com/azure/active-directory/saas-apps/helpshift-tutorial), [PortalTalk 365](https://www.portaltalk.com/), [CoreView](https://portal.coreview.com/), [Squelch Cloud Office365 Connector,](https://laxmi.squelch.io/login) [PingFlow Authentication](https://app-staging.pingview.io/), [PrinterLogic SaaS,](https://docs.microsoft.com/azure/active-directory/saas-apps/printerlogic-saas-tutorial) [Taskize Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/taskize-connect-tutorial), [Sandwai,](https://app.sandwai.com/) [EZRentOut,](https://docs.microsoft.com/azure/active-directory/saas-apps/ezrentout-tutorial) [AssetSonar,](https://docs.microsoft.com/azure/active-directory/saas-apps/assetsonar-tutorial) [Assistente Virtual Akari](https://akari.io/akari-virtual-assistant/)

Para obter mais informações sobre as aplicações, consulte [a integração da aplicação SaaS com o Azure Ative Directory](https://aka.ms/appstutorial). Para mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte Lista da sua aplicação na galeria de [aplicações do Diretório Ativo Azure](https://aka.ms/azureadapprequest).

---

### <a name="two-new-identity-protection-detections"></a>Duas novas deteções de Proteção de Identidade

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Proteção de Identidade  
**Capacidade do produto:** Proteção de & de Segurança de Identidade
 
Adicionámos dois novos tipos de deteção ligados à Proteção de Identidade: regras suspeitas de manipulação de caixas de entrada e viagens impossíveis. Estas deteções offline são descobertas pelo Microsoft Cloud App Security (MCAS) e influenciam o utilizador e o risco de iniciar sessão na Proteção de Identidade. Para obter mais informações sobre estas deteções, consulte os nossos [tipos de risco de inscrição](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks#sign-in-risk).
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>Breaking Change: Os fragmentos de URI não serão transportados através do redirecionamento de login

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Autenticação do Utilizador
 
A partir de 8 de fevereiro de 2020, quando um pedido é enviado para login.microsoftonline.com para assinar em um utilizador, o serviço anexará um fragmento vazio ao pedido.  Isto impede uma classe de ataques redirecionados, garantindo que o navegador elimina qualquer fragmento existente no pedido. Nenhuma aplicação deve ter uma dependência deste comportamento. Para mais informações, consulte [as alterações](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#february-2020) de Rutura na documentação da plataforma de identidade da Microsoft.

---

## <a name="december-2019"></a>Dezembro de 2019

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>Integrar o fornecimento de Fatores de Sucesso SAP em AD Azure e no local AD (Visualização Pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Provisionamento de Aplicativos  
**Capacidade do produto:** Gestão do ciclo de vida de identidade

Agora pode integrar o SAP SuccessFactors como uma fonte de identidade autoritária em Azure AD. Esta integração ajuda-o a automatizar o ciclo de vida de identidade de ponta a ponta, incluindo a utilização de eventos baseados em RH, como novas contratações ou rescisões, para controlar o fornecimento de contas Azure AD.

Para obter mais informações sobre como configurar o fornecimento de entrada sap SuccessFactors para a Azure AD, consulte o tutorial [de provisionamento automático Configure SAP SuccessFactors.](https://aka.ms/SAPSuccessFactorsInboundTutorial)

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>Suporte para e-mails personalizados em Azure AD B2C (Pré-visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2C - Gestão de Identidade do Consumidor  
**Capacidade do produto:** B2B/B2C

Agora pode utilizar o Azure AD B2C para criar e-mails personalizados quando os seus utilizadores se inscreverem para utilizar as suas apps. Ao utilizar o DisplayControls (atualmente em pré-visualização) e um fornecedor de e-mail de terceiros (como, [SendGrid,](https://sendgrid.com/) [SparkPost](https://sparkpost.com/), ou um API REST personalizado), pode utilizar o seu próprio modelo de e-mail, **a partir do** endereço e do texto sujeito, bem como apoiar as definições de localização e senha personalizada (OTP).

Para mais informações, consulte [a verificação de email personalizada no Diretório Ativo Azure B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-email).

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>Substituição de políticas de base por incumprimentos de segurança

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Outros  
**Capacidade do produto:** Segurança e Proteção de Identidade

Como parte de um modelo seguro por padrão para a autenticação, estamos a remover as políticas de proteção de base existentes de todos os inquilinos. Esta remoção está prevista para ser concluída no final de fevereiro. A substituição destas políticas de proteção de base são incumprimentos de segurança. Se tem usado políticas de proteção de base, deve planear avançar para a nova política de incumprimentos de segurança ou para o Acesso Condicional. Se não usaste estas políticas, não há ação para tomares.

Para obter mais informações sobre os novos incumprimentos de segurança, consulte [o que são falhas de segurança?](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) Para obter mais informações sobre as políticas de Acesso Condicional, consulte [as políticas de Acesso Condicional Comum.](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common)

---

## <a name="november-2019"></a>Novembro de 2019

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>Suporte para o atributo SameSite e Chrome 80

**Tipo:** Plano de mudança  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Autenticação do Utilizador

Como parte de um modelo seguro por padrão para cookies, o navegador Chrome 80 está a mudar a forma como trata os cookies sem o `SameSite` atributo. Qualquer cookie que não `SameSite` especifique o atributo será `SameSite=Lax`tratado como se estivesse definido , o que resultará em Chrome bloqueando certos cenários de partilha de cookies de domínio transversal de que a sua aplicação pode depender. Para manter o comportamento chrome mais `SameSite=None` antigo, pode `Secure` utilizar o atributo e adicionar um atributo adicional, pelo que os cookies de cross-site só podem ser acedidos sobre ligações HTTPS. O Chrome deverá concluir esta alteração até 4 de fevereiro de 2020.

Recomendamos que todos os nossos desenvolvedores testem as suas aplicações usando esta orientação:

- Defina o valor predefinido para a definição de **Cookie seguro de utilização** para **Sim**.

- Detete o valor predefinido para o atributo **do SameSite** a **None**.

- Adicione um `SameSite` atributo adicional de **Secure**.

Para mais informações, consulte as [próximas Alterações de Cookies do SameSite em ASP.NET e ASP.NET Core](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/) e Potencial perturbação nos websites dos clientes e produtos e [serviços da Microsoft na versão 79 do Chrome e posteriormente.](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79)

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>Novo hotfix para Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2)

**Tipo:** Fixo  
**Categoria de serviço:** Gestor de Identidade da Microsoft  
**Capacidade do produto:** Gestão do ciclo de vida de identidade

Um pacote de rollup hotfix (build 4.6.34.0) está disponível para microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2). Este pacote de rollup resolve problemas e adiciona melhorias que são descritas na secção "Questões fixas e melhorias adicionadas nesta atualização".

Para mais informações e para descarregar o pacote hotfix, consulte [o Microsoft Identity Manager 2016 Service Pack 2 (build 4.6.34.0) Update Rollup](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r).

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>Novo relatório de atividade de aplicações da AD FS para ajudar a migrar aplicações para a AD Azure (Visualização Pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** SSO

Utilize o novo relatório de atividade da app Ative Directory Federation Services (AD FS), no portal Azure, para identificar quais das suas aplicações são capazes de ser migradas para a AD Azure. O relatório avalia todas as aplicações aD FS para compatibilidade com a Azure AD, verifica quaisquer problemas e dá orientações sobre a preparação de aplicações individuais para a migração.

Para mais informações, consulte Utilize o relatório de atividade de [aplicação da AD FS para migrar aplicações para a AD Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity).

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>Novo fluxo de trabalho para os utilizadores solicitarem o consentimento do administrador (Pré-visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** Controlo de Acesso

O novo fluxo de trabalho de consentimento da administração dá aos administradores uma forma de conceder acesso a apps que requerem aprovação administrativa. Se um utilizador tentar aceder a uma aplicação, mas não conseguir dar consentimento, pode agora enviar um pedido de aprovação de administradores. O pedido é enviado por e-mail, e colocado numa fila acessível a partir do portal Azure, a todos os administradores que foram designados como revisores. Depois de um revisor tomar medidas sobre um pedido pendente, os utilizadores solicitados são notificados da ação.

Para mais informações, consulte Configure o fluxo de trabalho de [consentimento do administrador (pré-visualização)](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow).

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>Nova app Azure AD Inscrições Token experiência de configuração para gestão de reclamações opcionais (Pré-visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Outros  
**Capacidade do produto:** Experiência de Desenvolvimento

A nova lâmina de **configuração de registos de aplicações da AD Azure** no portal Azure mostra agora aos programadores de aplicações uma lista dinâmica de reclamações opcionais para as suas apps. Esta nova experiência ajuda a agilizar as migrações de aplicações da AD Azure e a minimizar as configurações de sinistros opcionais.

Para mais informações, consulte [Fornecer reclamações opcionais na sua aplicação Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Novo fluxo de trabalho de aprovação em duas fases na gestão de direitos da AD Azure (Visualização Pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Outros  
**Capacidade do produto:** Gestão de Direitos

Introduzimos um novo fluxo de trabalho de aprovação em duas fases que lhe permite exigir dois aprovadores para aprovar o pedido de um utilizador para um pacote de acesso. Por exemplo, pode defini-lo para que o gestor do utilizador que solicita aprove primeiro, e depois também pode exigir que um proprietário de recursos aprove. Se um dos aprovadores não aprovar, o acesso não é concedido.

Para mais informações, consulte as definições de pedido de alteração e aprovação para um pacote de [acesso na gestão de direitos da AD Azure](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-request-policy).

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Atualizações para a página My Apps juntamente com novos espaços de trabalho (Pré-visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Minhas Apps  
**Capacidade do produto:** 3ª Integração do Partido

Agora pode personalizar a forma como os utilizadores da sua organização vêem e acedem à experiência renovada das Minhas Apps. Esta nova experiência inclui também a funcionalidade de novos espaços de trabalho, o que facilita a descoberta e organização de apps pelos utilizadores.

Para obter mais informações sobre a experiência das novas Minhas Apps e criar espaços de trabalho, consulte Criar espaços de [trabalho no portal My Apps](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces).

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>Suporte de ID social da Google para colaboração Azure AD B2B (Disponibilidade Geral)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2B  
**Capacidade do produto:** Autenticação do Utilizador

Novo suporte para a utilização de IDs sociais do Google (contas Gmail) em Anúncio sacana do Azure ajuda a tornar a colaboração mais simples para os seus utilizadores e parceiros. Já não é necessário que os seus parceiros criem e gerem uma nova conta específica da Microsoft. O Microsoft Teams agora suporta totalmente os utilizadores do Google em todos os clientes e através dos pontos finais de autenticação comuns e relacionados com o arrendatário.

Para mais informações, consulte adicionar o Google como um fornecedor de [identidade para utilizadores convidados B2B](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>Suporte móvel microsoft edge para acesso condicional e entrada única (Disponibilidade Geral)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Acesso Condicional  
**Capacidade do produto:** Proteção de & de Segurança de Identidade

O Azure AD para o Microsoft Edge no iOS e Android suporta agora o Acesso Único e Único Do AD do Azure AD:

- **Microsoft Edge single sign-on (SSO):** O único sign-on está agora disponível em clientes nativos (como o Microsoft Outlook e o Microsoft Edge) para todas as aplicações ligadas ao Azure AD.

- **Acesso condicional do Microsoft Edge:** Através de políticas de acesso condicional baseadas em aplicações, os utilizadores devem utilizar navegadores protegidos pelo Microsoft Intune, como o Microsoft Edge.

Para obter mais informações sobre acesso condicional e SSO com o Microsoft Edge, consulte o Suporte Móvel microsoft Edge para acesso condicional e post de blog [single-on agora disponível.](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179) Para obter mais informações sobre como configurar as suas aplicações de clientes utilizando [acesso condicional baseado em aplicativos](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) ou acesso condicional baseado em [dispositivos,](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)consulte Gerir o acesso à Web utilizando um navegador protegido por políticas [microsoft Intune](https://docs.microsoft.com/intune/apps/app-configuration-managed-browser).

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Gestão de direitos da AD Azure (Disponibilidade Geral)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Outros  
**Capacidade do produto:** Gestão de Direitos

A gestão de direitos da Azure AD é um novo recurso de governação de identidade, que ajuda as organizações a gerir a identidade e a aceder ao ciclo de vida em escala. Esta nova funcionalidade ajuda automatizando fluxos de trabalho de pedidos de acesso, atribuições de acesso, avaliações e expiração entre grupos, apps e sites SharePoint Online.

Com a gestão de direitos da Azure AD, pode gerir de forma mais eficiente o acesso tanto para os colaboradores como para utilizadores fora da sua organização que precisam de acesso a esses recursos.

Para mais informações, consulte o que é a gestão de [direitos da Azure AD?](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview#license-requirements)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizar o fornecimento de conta de utilizador para estas aplicações SaaS recentemente suportadas

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** 3ª Integração do Partido  

Agora pode automatizar a criação, atualização e a eliminar as contas dos utilizadores destas aplicações recentemente integradas:

Serviço de [Autenticação de Identidade](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial)da Plataforma SAP Cloud , [RingCentral,](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-provisioning-tutorial) [SpaceIQ,](https://docs.microsoft.com/azure/active-directory/saas-apps/spaceiq-provisioning-tutorial) [Miro,](https://docs.microsoft.com/azure/active-directory/saas-apps/miro-provisioning-tutorial) [Cloudgate,](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial) [Infor CloudSuite,](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloudsuite-provisioning-tutorial) [OfficeSpace Software,](https://docs.microsoft.com/azure/active-directory/saas-apps/officespace-software-provisioning-tutorial) [Priority Matrix](https://docs.microsoft.com/azure/active-directory/saas-apps/priority-matrix-provisioning-tutorial)

Para obter mais informações sobre como proteger melhor a sua organização utilizando o fornecimento automatizado de conta de utilizador, consulte o fornecimento de [utilizadores automate para aplicações SaaS com AD Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Novas Aplicações Federadas disponíveis na galeria Azure AD App - novembro de 2019

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** 3ª Integração do Partido

Em novembro de 2019, adicionámos estas 21 novas aplicações com apoio da Federação à galeria de aplicações:

[Airtable](https://docs.microsoft.com/azure/active-directory/saas-apps/airtable-tutorial), [Hootsuite](https://docs.microsoft.com/azure/active-directory/saas-apps/hootsuite-tutorial), [Blue Access for Members (BAM)](https://docs.microsoft.com/azure/active-directory/saas-apps/blue-access-for-members-tutorial), [Bitly,](https://docs.microsoft.com/azure/active-directory/saas-apps/bitly-tutorial) [Riva](https://docs.microsoft.com/azure/active-directory/saas-apps/riva-tutorial), [ResLife Portal,](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=) [NegometrixPortal Single Sign On (SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/negometrixportal-tutorial), [TeamsChamp,](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279) [Motus,](https://docs.microsoft.com/azure/active-directory/saas-apps/motus-tutorial) [MyAryaka](https://docs.microsoft.com/azure/active-directory/saas-apps/myaryaka-tutorial), [BlueMail,](https://loginself1.bluemail.me/) [Beedle,](https://teams-web.beedle.co/#/) [Visma,](https://docs.microsoft.com/azure/active-directory/saas-apps/visma-tutorial) [OneDesk,](https://docs.microsoft.com/azure/active-directory/saas-apps/onedesk-tutorial) [Foko Retail,](https://docs.microsoft.com/azure/active-directory/saas-apps/foko-retail-tutorial) [Qmarkets Idea & Innovation Management](https://docs.microsoft.com/azure/active-directory/saas-apps/qmarkets-idea-innovation-management-tutorial), [Netskope User Authentication,](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-user-authentication-tutorial) [uniFLOW Online,](https://docs.microsoft.com/azure/active-directory/saas-apps/uniflow-online-tutorial) [Claromentis,](https://docs.microsoft.com/azure/active-directory/saas-apps/claromentis-tutorial) [Jisc Student Voter Registration,](https://docs.microsoft.com/azure/active-directory/saas-apps/jisc-student-voter-registration-tutorial) [e4enable](https://portal.e4enable.com/)

Para obter mais informações sobre as aplicações, consulte [a integração da aplicação SaaS com o Azure Ative Directory](https://aka.ms/appstutorial). Para mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte Lista da sua aplicação na galeria de [aplicações do Diretório Ativo Azure](https://aka.ms/azureadapprequest).

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>Galeria de aplicações da AD Azure nova e melhorada

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** SSO

Atualizámos a galeria de aplicações da Azure AD para facilitar a sua descoberta de aplicações pré-integradas que suportam o provisionamento, OpenID Connect e SAML no seu inquilino azure Ative Directory.

Para mais informações, consulte Adicionar uma aplicação ao seu inquilino do [Diretório Ativo Azure.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal)

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>Limite de comprimento de definição de função de aplicação aumentado de 120 para 240 caracteres

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** SSO

Ouvimos dos clientes que o limite de comprimento para o valor de definição de função da aplicação em algumas apps e serviços é muito curto em 120 caracteres. Em resposta, aumentámos o comprimento máximo da definição de valor de papel para 240 caracteres.

Para obter mais informações sobre a utilização de definições de funções específicas para aplicações, consulte adicionar funções de [aplicação na sua aplicação e receba-as no token](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps).

---

## <a name="october-2019"></a>Outubro de 2019

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Depreciação da identidadeRiskEvent API para deteções de risco de proteção de identidade Azure AD  

**Tipo:** Plano de mudança  
**Categoria de serviço:** Proteção de Identidade  
**Capacidade do produto:** Proteção de & de Segurança de Identidade

Em resposta ao feedback do desenvolvedor, os subscritores do Azure AD Premium P2 podem agora realizar consultas complexas sobre os dados de deteção de risco da Azure AD Identity Protection utilizando a nova API de deteção de risco para o Microsoft Graph. A versão beta da [API](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta) de identidade existente deixará de devolver dados por volta de 10 de janeiro de **2020**. Se a sua organização estiver a usar a identidadeRiskEvent API, deverá transitar para a nova API de riscoDetecção.

Para obter mais informações sobre a nova API de deteção de risco, consulte a documentação de referência da API de [deteção](https://aka.ms/RiskDetectionsAPI)de risco .

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>Suporte proxy de aplicação para o Mesmo Site Atributo e Chrome 80

**Tipo:** Plano de mudança  
**Categoria de serviço:** Procuração de aplicativos  
**Capacidade do produto:** Controlo de Acesso

Algumas semanas antes do lançamento do navegador Chrome 80, planeamos atualizar a forma como os cookies proxy de aplicação tratam o atributo **do MesmoSite.** Com o lançamento do Chrome 80, qualquer cookie que não especifique o `SameSite=Lax`atributo **do SameSite** será tratado como se estivesse definido para .

Para ajudar a evitar impactos potencialmente negativos devido a esta mudança, estamos a atualizar o acesso do Application Proxy e os cookies de sessão por:

- Definindo o valor predefinido para a definição de **Cookie seguro de utilização** para **Sim**.

- Definindo o valor predefinido para o atributo **do SameSite** a **None**.

    >[!NOTE]
    > Os cookies de acesso proxy de aplicação sempre foram transmitidos exclusivamente através de canais seguros. Estas alterações aplicam-se apenas aos cookies de sessão.

Para obter mais informações sobre as definições de cookies proxy de aplicação, consulte as definições de [Cookiepara aceder às aplicações no local no Diretório Ativo do Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="app-registrations-legacy-and-converged-app-management-from-the-application-registration-portal-appsdevmicrosoftcom-will-no-longer-be-available"></a>As inscrições de aplicações (legado) e a gestão de aplicações convergentes do Portal de Registo de Aplicações (apps.dev.microsoft.com) deixarão de estar disponíveis

**Tipo:** Plano de mudança  
**Categoria de serviço:** N/A  
**Capacidade do produto:** Experiência de Desenvolvimento

Num futuro próximo, os utilizadores com contas Azure AD deixarão de poder registar-se e gerir aplicações convergentes utilizando o Portal de Registo de Aplicações (apps.dev.microsoft.com), ou registar e gerir aplicações na experiência de registos da App (legado) no portal Azure.

Para saber mais sobre a experiência de registos da nova App, consulte as inscrições da App no guia de [formação do portal Azure.](../develop/app-registrations-training-guide-for-app-registrations-legacy-users.md)

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>Os utilizadores deixaram de ser obrigados a reregistar-se durante a migração do MFA por utilizador para o MFA baseado no Acesso Condicional

**Tipo:** Fixo  
**Categoria de serviço:** MFA  
**Capacidade do produto:** Proteção de & de Segurança de Identidade

Corrigimos um problema conhecido em que os utilizadores eram obrigados a reregistar-se se fossem desativados para a autenticação multi-factor por utilizador (MFA) e depois habilitados para mFA através de uma política de Acesso Condicional.

Para exigir que os utilizadores se reregiissem, pode selecionar a opção **MFA re-registrada exigida** a partir dos métodos de autenticação do utilizador no portal Azure AD. Para obter mais informações sobre utilizadores migratórios de MFA por utilizador para MFA baseado em Acesso Condicional, consulte [Converter utilizadores de MFA por utilizador para MFA baseado em Acesso Condicional](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted#convert-users-from-per-user-mfa-to-conditional-access-based-mfa).

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>Novas capacidades para transformar e enviar reclamações no seu token SAML

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** SSO

Adicionámos capacidades adicionais para o ajudar a personalizar e enviar reclamações no seu token SAML. Estas novas capacidades incluem:

- Funções adicionais de transformação de sinistros, ajudando-o a modificar o valor que envia na reclamação.

- Capacidade de aplicar múltiplas transformações numa única reivindicação.

- Capacidade de especificar a fonte de reclamação, com base no tipo de utilizador e no grupo a que o utilizador pertence.

Para obter informações detalhadas sobre estas novas capacidades, incluindo como usá-las, consulte [Personalizar as reclamações emitidas no token SAML para aplicações empresariais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Nova página my sign-ins para utilizadores finais em Azure AD

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Relatório de & de monitorização

Adicionámos uma nova página **My Sign-ins** parahttps://mysignins.microsoft.com) permitir que os utilizadores da sua organização vejam o seu histórico de registorecente para verificar qualquer atividade invulgar. Esta nova página permite que os seus utilizadores vejam:

- Se alguém está a tentar adivinhar a sua senha.

- Se um intruso assinou com sucesso na sua conta e a partir de que localização.

- Que aplicações o agressor tentou aceder.

Para mais informações, consulte os Utilizadores que agora podem verificar o [seu histórico de sessão para](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066) blogs de atividades incomuns.

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>Migração de Serviços de Domínio Azure AD (Azure AD DS) de redes virtuais clássicas para o Gestor de Recursos Azure

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Serviços de Domínio Azure AD  
**Capacidade do produto:** Serviços de Domínio Azure AD

Para os nossos clientes que estiveram presos em redes virtuais clássicas. Agora pode realizar uma migração única de uma rede virtual clássica para uma rede virtual existente do Gestor de Recursos. Depois de se mudar para a rede virtual do Gestor de Recursos, poderá tirar partido das funcionalidades adicionais e atualizadas, tais como, políticas de senha de grãos finos, notificações de e-mail e registos de auditoria.

Para mais informações, consulte [Preview - Migrate Azure AD Domain Services from the Classic virtual network model to Resource Manager](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet).

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Atualizações para o layout do contrato da página Azure AD B2C

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2C - Gestão de Identidade do Consumidor  
**Capacidade do produto:** B2B/B2C

Introduzimos algumas novas alterações na versão 1.2.0 do contrato de página para o Azure AD B2C. Nesta versão atualizada, pode agora controlar a ordem de carga dos seus elementos, o que também pode ajudar a parar o piscar que acontece quando a folha de estilo (CSS) é carregada.

Para obter uma lista completa das alterações feitas ao contrato da página, consulte o registo de alteração da [versão](https://docs.microsoft.com/azure/active-directory-b2c/page-layout#120).

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Atualização para a página My Apps juntamente com novos espaços de trabalho (pré-visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Minhas Apps  
**Capacidade do produto:** Controlo de Acesso

Agora pode personalizar a forma como os utilizadores da sua organização vêem e acedem à nova experiência my apps, incluindo a utilização da nova funcionalidade de espaços de trabalho para facilitar a sua descoberta de apps. A funcionalidade dos novos espaços de trabalho funciona como um filtro para as aplicações a que os utilizadores da sua organização já têm acesso.

Para obter mais informações sobre o lançamento da nova experiência My Apps e criar espaços de trabalho, consulte [Create espaços de trabalho no portal My Apps (pré-visualização).](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces)

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>Suporte para o modelo de faturação baseado no utilizador ativo mensal (disponibilidade geral)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2C - Gestão de Identidade do Consumidor  
**Capacidade do produto:** B2B/B2C

O Azure AD B2C suporta agora a faturação mensal dos utilizadores ativos (MAU). A faturação da MAU baseia-se no número de utilizadores únicos com atividade de autenticação durante um mês civil. Os clientes existentes podem mudar para este novo método de faturação a qualquer momento.

A partir de 1 de novembro de 2019, todos os novos clientes serão automaticamente faturados usando este método. Este método de faturação beneficia os clientes através de benefícios de custos e da capacidade de planear com antecedência.

Para mais informações, consulte [o Upgrade para o modelo de faturação](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing#upgrade-to-monthly-active-users-billing-model)mensal dos utilizadores ativos.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Novas Aplicações Federadas disponíveis na galeria Azure AD App - outubro de 2019

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** 3ª Integração do Partido

Em outubro de 2019, adicionámos estas 35 novas aplicações com apoio da Federação à galeria de aplicações:

[Em caso de crise – Móvel](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-mobile-tutorial), [Juno Journey](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial), [ExponentHR](https://docs.microsoft.com/azure/active-directory/saas-apps/exponenthr-tutorial), [Tact](https://tact.ai/assistant/), [OpusCapita Cash Management](http://cm1.opuscapita.com/tenantname), [Salestim](https://prd.salestim.io/forms), [Learnster](https://docs.microsoft.com/azure/active-directory/saas-apps/learnster-tutorial), [Dynatrace,](https://docs.microsoft.com/azure/active-directory/saas-apps/dynatrace-tutorial) [HunchBuzz](https://login.hunchbuzz.com/integrations/azure/process), [Freshworks](https://docs.microsoft.com/azure/active-directory/saas-apps/freshworks-tutorial), [eCornell,](https://docs.microsoft.com/azure/active-directory/saas-apps/ecornell-tutorial) [ShipHazmat,](https://docs.microsoft.com/azure/active-directory/saas-apps/shiphazmat-tutorial) [Netskope Cloud Security,](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial) [Content Fulful](https://docs.microsoft.com/azure/active-directory/saas-apps/contentful-tutorial), [Bindtuning](https://bindtuning.com/login), [HireVue Coordenada – Europa](https://www.hirevue.com/), [HireVue Coordenada - USOnly](https://www.hirevue.com/), [HireVue Coordenada - EUA,](https://www.hirevue.com/) [WittyParrot Knowledge Box](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup), [Cloudmore](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudmore-tutorial), [Visit.org](https://docs.microsoft.com/azure/active-directory/saas-apps/visitorg-tutorial), [Cambium Xirrus EasyPass Portal](https://login.xirrus.com/azure-signup), [Paylocity](https://docs.microsoft.com/azure/active-directory/saas-apps/paylocity-tutorial), Mail [Luck!](https://docs.microsoft.com/azure/active-directory/saas-apps/mail-luck-tutorial), [Teamie](https://theteamie.com/), [Velocity for Teams](https://velocity.peakup.org/teams/login), [SIGNL4](https://account.signl4.com/manage), [EAB Navigate IMPL,](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-impl-tutorial) [ScreenMeet,](https://console.screenmeet.com/) [Omega Point,](https://pi.ompnt.com/) [Speaking Email for Intune (iPhone)](https://speaking.email/FAQ/98/email-access-via-microsoft-intune), [Speaking Email for Office 365 Direct (iPhone/Android)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct), [ExactCare SSO,](https://docs.microsoft.com/azure/active-directory/saas-apps/exactcare-sso-tutorial) [iHealthHome Care Navigation System](https://ihealthnav.com/account/signin), [Qubie](https://qubie.azurewebsites.net/static/adminTab/authorize.html)

Para obter mais informações sobre as aplicações, consulte [a integração da aplicação SaaS com o Azure Ative Directory](https://aka.ms/appstutorial). Para mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte Lista da sua aplicação na galeria de [aplicações do Diretório Ativo Azure](https://aka.ms/azureadapprequest).

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Item de menu de segurança consolidada no portal Azure AD

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Proteção de Identidade  
**Capacidade do produto:** Proteção de & de Segurança de Identidade

Já pode aceder a todas as funcionalidades de segurança da Azure AD disponíveis a partir do novo item do menu **Security,** e a partir do bar **Search,** no portal Azure. Além disso, a nova página de aterragem de **Segurança,** chamada **Security - Getting Start,** fornecerá links para a nossa documentação pública, orientação de segurança e guias de implementação.

O novo menu **de Segurança** inclui:

- Acesso Condicional
- Identity Protection
- Centro de Segurança
- Pontuação segura de identidade
- Métodos de autenticação
- MFA
- Relatórios de risco - Utilizadores de risco, inscrições arriscadas, deteções de risco
- E mais...

Para mais informações, consulte [Segurança - Início](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted).

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Política de expiração de grupos 365 grupos reforçada com renovação automática

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Gestão de Grupos  
**Capacidade do produto:** Gestão do ciclo de vida de identidade

A política de expiração dos grupos 365 foi reforçada para renovar automaticamente os grupos que estão ativamente em uso pelos seus membros. Os grupos serão renovados automaticamente com base na atividade do utilizador em todas as aplicações do Office 365, incluindo Outlook, SharePoint e Teams.

Esta melhoria ajuda a reduzir as notificações de expiração do seu grupo e ajuda a garantir que os grupos ativos continuam disponíveis. Se já tem uma política de expiração ativa para os seus grupos office 365, não precisa de fazer nada para ativar esta nova funcionalidade.

Para mais informações, consulte Configure a política de [expiração dos grupos Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-lifecycle).

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>Experiência de criação atualizada dos Serviços de Domínio Azure (Azure AD DS)

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Serviços de Domínio Azure AD  
**Capacidade do produto:** Serviços de Domínio Azure AD

Atualizámos o Azure AD Domain Services (Azure AD DS) para incluir uma nova e melhorada experiência de criação, ajudando-o a criar um domínio gerido em apenas três cliques! Além disso, pode agora carregar e implantar DS Azure a partir de um modelo.

Para mais informações, consulte [Tutorial: Crie e configure uma instância de Serviços de Domínio de Diretório Ativo Azure.](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance)

---
