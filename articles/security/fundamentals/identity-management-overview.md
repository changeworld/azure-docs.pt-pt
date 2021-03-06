---
title: Funcionalidades de segurança azure que ajudam na gestão de identidades Microsoft Docs
description: " Este artigo fornece uma visão geral das principais funcionalidades de segurança do Azure que ajudam na gestão da identidade. As soluções de gestão de identidade e acesso da Microsoft ajudam a proteger o acesso a aplicações e recursos através do datacenter corporativo e na nuvem, permitindo níveis adicionais de validação, como a Autenticação multi-Factor e o Acesso Condicional políticas. "
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 5aa0a7ac-8f18-4ede-92a1-ae0dfe585e28
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2018
ms.author: terrylan
Customer intent: As an IT Pro or decision maker I am trying to learn about identity management capabilities in Azure
ms.openlocfilehash: f61b6193a0d2082296a17128b41d7220f9b7e05f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565898"
---
# <a name="azure-identity-management-security-overview"></a>Visão geral da segurança da gestão de identidade azure

 A gestão da identidade é o processo de autenticação e autorização de diretores de [segurança.](/windows/security/identity-protection/access-control/security-principals) Envolve também o controlo da informação sobre esses princípios (identidades). Os diretores de segurança (identidades) podem incluir serviços, aplicações, utilizadores, grupos, etc. As soluções de gestão de identidade e acesso da Microsoft ajudam a proteger o acesso a aplicações e recursos através do datacenter corporativo e na nuvem. Esta proteção permite níveis adicionais de validação, tais como a autenticação multi-factor e as políticas de acesso condicional. Monitorizar atividades suspeitas através de relatórios de segurança avançados, auditoria e alerta ajuda a mitigar potenciais problemas de segurança. [O Azure Ative Directory Premium](/azure/active-directory/active-directory-editions) fornece um único sinal (SSO) a milhares de software sem aviso si como aplicações de serviço (SaaS) e acesso a aplicações web que executa no local.
 
Aproveitando os benefícios de segurança do Azure Ative Directory (Azure AD), pode:

* Crie e faça a gestão de uma única identidade para cada utilizador em toda a sua empresa híbrida, ao manter os utilizadores, os grupos e os dispositivos sincronizados. 
* Forneça acesso sso às suas aplicações, incluindo milhares de aplicações SaaS pré-integradas.
* Ativar a segurança do acesso a aplicações mediante a aplicação da Multi-Factor Authentication baseada em regras, tanto nas aplicações no local, como na cloud.
* Provisão segura acesso remoto a aplicações web no local através do Proxy de Aplicação AD Azure.

O objetivo deste artigo é fornecer uma visão geral das principais funcionalidades de segurança do Azure que ajudam na gestão da identidade. Também fornecemos links para artigos que dão detalhes de cada funcionalidade para que possa saber mais.  

O artigo centra-se nas seguintes capacidades de gestão da identidade Azure:

* Início de sessão único
* Proxy inverso
* Multi-Factor Authentication
* Controlo de acesso baseado em funções (RBAC)
* Relatórios de monitorização de segurança, alertas e relatórios baseados em machine learning
* Identidade do consumidor e gestão de acesso
* Registo de dispositivo
* Privileged Identity Management
* Proteção de identidade
* Gestão de identidade híbrida/Ligação Azure AD
* Revisões de acesso do Azure AD

## <a name="single-sign-on"></a>Início de sessão único

SSO significa poder aceder a todas as aplicações e recursos que precisa para fazer negócios, assinando apenas uma vez usando uma única conta de utilizador. Uma vez inscrito, pode aceder a todas as aplicações de que necessita sem ser obrigado a autenticar (por exemplo, digitar uma palavra-passe) uma segunda vez.

Muitas organizações dependem de aplicações SaaS como Office 365, Box e Salesforce para a produtividade dos utilizadores. Historicamente, o pessoal de TI precisava de criar e atualizar individualmente as contas dos utilizadores em cada aplicação SaaS, e os utilizadores tinham de se lembrar de uma palavra-passe para cada aplicação SaaS.

A Azure AD alarga os ambientes de Diretório Ativo no local para a nuvem, permitindo que os utilizadores utilizem a sua conta organizacional primária para assinar não só para os seus dispositivos e recursos da empresa, mas também para todas as aplicações web e SaaS de que necessitam para o seu trabalho.

Não só os utilizadores não têm de gerir vários conjuntos de nomes de utilizadores e palavras-passe, como pode fornecer ou desfornecer o acesso de aplicação automaticamente, com base nos seus grupos organizacionais e no seu estado de funcionário. A Azure AD introduz controlos de governança de segurança e acesso com os quais pode gerir centralmente o acesso dos utilizadores através das aplicações SaaS.

Saiba mais:

* [Visão geral de um único sign-on](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/)
* [What is application access and single sign-on with Azure Active Directory?](../../active-directory/manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Integrar o diretório ativo azure único com aplicações SaaS](../../active-directory/manage-apps/configure-single-sign-on-non-gallery-applications.md)

## <a name="reverse-proxy"></a>Proxy inverso

A Aplicação Azure AD Proxy permite-lhe publicar aplicações no local, tais como sites [SharePoint,](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US) [Outlook Web App](https://technet.microsoft.com/library/jj657718.aspx)e aplicações baseadas no [IIS](https://www.iis.net/)dentro da sua rede privada e fornece acesso seguro aos utilizadores fora da sua rede. Application Proxy fornece acesso remoto e SSO para muitos tipos de aplicações web no local com os milhares de aplicações SaaS que a Azure AD suporta. Os colaboradores podem iniciar sessão nas suas apps a partir de casa nos seus próprios dispositivos e autenticar através deste proxy baseado na nuvem.

Saiba mais:

* [Habilitar a procuração de aplicação ad azure](/azure/active-directory/manage-apps/application-proxy-enable)
* [Publicar aplicações com o Proxy da Aplicação do Azure AD](/azure/active-directory/active-directory-application-proxy-publish)
* [Início de sessão único com o Proxy de Aplicações](../../active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
* [Trabalhar com acesso condicional](../../active-directory/manage-apps/application-proxy-integrate-with-sharepoint-server.md)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

A autenticação multi-factor Azure é um método de autenticação que requer a utilização de mais de um método de verificação e adiciona uma segunda camada crítica de segurança aos insines e transações do utilizador. A Autenticação Multi-Factor ajuda a salvaguardar o acesso a dados e aplicações, ao mesmo tempo que satisfaz a procura do utilizador por um processo simples de iniciar sessão. Fornece autenticação forte através de um leque de opções de verificação: chamadas telefónicas, mensagens de texto ou notificações de aplicações móveis ou códigos de verificação e fichas oAuth de terceiros.

Saiba mais:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [O que é a Multi-Factor Authentication do Azure?](/azure/active-directory/authentication/multi-factor-authentication)
* [Como funciona o Multi-Factor Authentication do Azure](../../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="rbac"></a>RBAC

O RBAC é um sistema de autorização criado com base no Azure Resource Manager que fornece gestão pormenorizada de acesso de recursos no Azure. O RBAC permite controlar granularmente o nível de acesso que os utilizadores têm. Por exemplo, pode limitar um utilizador a gerir apenas redes virtuais e outro utilizador para gerir todos os recursos num grupo de recursos. O Azure inclui várias funções incorporadas que pode utilizar. São apresentadas em seguida quatro funções incorporadas fundamentais. As três primeiras aplicam-se a todos os tipos de recursos.

Saiba mais:

* [O que é o controlo de acesso baseado em funções (RBAC)?](/azure/role-based-access-control/overview)
* [Built-in roles for Azure resources](/azure/role-based-access-control/built-in-roles) (Funções incorporadas para recursos do Azure)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Relatórios de monitorização de segurança, alertas e relatórios baseados em machine learning

Monitorização de segurança, alertas e relatórios baseados em machine learning que identifiquem padrões de acesso inconsistentes podem ajudá-lo a proteger o seu negócio. Pode utilizar relatórios de acesso e utilização de Anúncios Azure para obter visibilidade na integridade e segurança do diretório da sua organização. Com esta informação, um administrador de diretório pode determinar melhor onde possíveis riscos de segurança podem estar para que possam planear adequadamente mitigar esses riscos.

No portal Azure, os relatórios enquadram-se nas seguintes categorias:

* **Relatórios de anomalias**: Contém eventos de inscrição que considerámos anómalos. O nosso objetivo é torná-lo consciente de tal atividade e permitir-lhe determinar se um evento é suspeito.
* **Relatórios de aplicação integrados**: Forneça informações sobre como as aplicações em nuvem estão a ser usadas na sua organização. A Azure AD oferece integração com milhares de aplicações em nuvem.
* **Relatórios de erro**: Indique erros que podem ocorrer quando fornecer contas a aplicações externas.
* **Relatórios específicos do utilizador**: Mostrar os dados de atividade do dispositivo para um utilizador específico.
* **Registos de atividades**: Contenha um registo de todos os eventos auditados nas últimas 24 horas, últimos 7 dias ou 30 dias, e alterações de atividade do grupo e reset de password e atividade de registo.

Saiba mais:

* [Ver os relatórios de acesso e utilização](/azure/active-directory/active-directory-view-access-usage-reports)
* [Começar com relatórios de Diretório Ativo Azure](/azure/active-directory/active-directory-reporting-getting-started)
* [Guia de relatórios de Diretório Ativo Azure](/azure/active-directory/active-directory-reporting-guide)

## <a name="consumer-identity-and-access-management"></a>Identidade do consumidor e gestão de acesso

O Azure AD B2C é um serviço de gestão de identidade altamente disponível, global e global, para aplicações viradas para o consumidor que escala para centenas de milhões de identidades. Pode ser integrado entre plataformas móveis e Web. Os seus consumidores podem iniciar sessão em todas as suas aplicações através de experiências personalizáveis utilizando as suas contas sociais existentes ou criando novas credenciais.

No passado, os desenvolvedores de aplicações que quisessem inscrever os clientes e inscrevê-los nas suas aplicações teriam escrito o seu próprio código. E teriam de utilizar sistemas ou bases de dados no local para armazenar os nomes de utilizador e as palavras-passe. O Azure AD B2C oferece à sua organização uma melhor forma de integrar a gestão da identidade dos consumidores em aplicações com a ajuda de uma plataforma segura e baseada em padrões e um grande conjunto de políticas extensíveis.

Quando utiliza o Azure AD B2C, os seus consumidores podem inscrever-se nas suas aplicações utilizando as suas contas sociais existentes (Facebook, Google, Amazon, LinkedIn) ou criando novas credenciais (endereço de e-mail e palavra-passe, ou nome de utilizador e palavra-passe).

Saiba mais:

* [O que é o Azure Active Directory B2C?](https://azure.microsoft.com/services/active-directory-b2c/)
* [Pré-visualização do Azure Active Directory B2C: inscrever-se e iniciar sessão em consumidores nas suas aplicações](../../active-directory-b2c/overview.md)
* [Pré-visualização do Diretório Ativo Azure B2C: Tipos de aplicações](../../active-directory-b2c/application-types.md)

## <a name="device-registration"></a>Registo de dispositivo

O registo do dispositivo Azure AD é a base para cenários de [acesso condicional](/azure/active-directory/active-directory-conditional-access-device-registration-overview) baseados em dispositivos. Quando um dispositivo está registado, o registo do dispositivo Azure AD fornece ao dispositivo uma identidade que utiliza para autenticar o dispositivo quando um utilizador faz a entrada. O dispositivo autenticado e os atributos do dispositivo podem então ser utilizados para impor políticas de Acesso Condicional para aplicações alojadas na nuvem e no local.

Quando combinado com uma solução de gestão de dispositivos móveis, como o Intune, os atributos do dispositivo em Azure AD são atualizados com informações adicionais sobre o dispositivo. Em seguida, pode criar regras de Acesso Condicional que impõem o acesso dos dispositivos para cumprir os seus padrões de segurança e conformidade.

Saiba mais:

* [Começar com o registo do dispositivo Azure AD](/azure/active-directory/active-directory-conditional-access-device-registration-overview)
* [Registo automático de dispositivos com AD Azure para dispositivos ligados ao domínio do Windows](/azure/active-directory/active-directory-conditional-access-automatic-device-registration)
* [Configurar registo automático de dispositivos ligados ao domínio do Windows com AD Azure](/azure/active-directory/active-directory-conditional-access-automatic-device-registration-setup)

## <a name="privileged-identity-management"></a>Privileged Identity Management

Com a Azure AD Privileged Identity Management, pode gerir, controlar e monitorizar as suas identidades privilegiadas e acesso a recursos em Azure AD, bem como outros serviços online da Microsoft, como o Office 365 e o Microsoft Intune.

Por vezes, os utilizadores precisam de realizar operações privilegiadas em recursos Azure ou Office 365, ou noutras aplicações do SaaS. Isto significa muitas vezes que as organizações têm de dar aos utilizadores acesso privilegiado permanente em Azure AD. Este acesso é um risco crescente de segurança para os recursos hospedados na nuvem, porque as organizações não conseguem monitorizar suficientemente o que os utilizadores estão a fazer com os seus privilégios de administrador. Além disso, se uma conta de utilizador com acesso privilegiado for comprometida, essa violação pode afetar a segurança total da organização na nuvem. A Azure AD Privileged Identity Management ajuda a mitigar este risco.

Com a Azure AD Privileged Identity Management, pode:

* Veja quais os utilizadores que são administradores da Azure AD.
* Permitir o acesso administrativo a pedido, just-in-time (JIT) aos serviços da Microsoft, tais como o Office 365 e o Intune.
* Obtenha relatórios sobre o histórico de acesso do administrador e alterações nas atribuições de administradores.
* Receba alertas sobre o acesso a um papel privilegiado.

Saiba mais:

* [O que é o Azure AD Privileged Identity Management?](../../active-directory/privileged-identity-management/pim-configure.md)
* [Atribuir funções de diretório da AD Azure na PIM](../../active-directory/privileged-identity-management/pim-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>Proteção de identidade

A Azure AD Identity Protection é um serviço de segurança que fornece uma visão consolidada sobre deteções de risco e potenciais vulnerabilidades que afetam as identidades da sua organização. A Proteção de Identidade tira partido das capacidades de deteção de anomalias da AD Azure existentes, que estão disponíveis através de relatórios de Atividade AD AD Do Azure. A Proteção de Identidade também introduz novos tipos de deteção de risco que podem detetar anomalias em tempo real.

Saiba mais:

* [Azure AD Identity Protection](/azure/active-directory/identity-protection/overview)
* [Canal 9: Azure AD e Mostra de Identidade: Pré-visualização da Proteção de Identidade](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-managementazure-ad-connect"></a>Gestão de identidade híbrida/Ligação Azure AD

As soluções de identidade da Microsoft abrangem capacidades baseadas na cloud e no local, ao criar uma única identidade de utilizador para autenticação e autorização a todos os recursos, independentemente da localização. Chamamos-lhe identidade híbrida. O Azure AD Connect é a ferramenta da Microsoft concebida para satisfazer e atingir os seus objetivos de identidade híbrida. Isto permite-lhe fornecer uma identidade comum para o utilizadores das aplicações do Office 365, do Azure e do SaaS integradas com o Azure AD. Proporciona as seguintes funcionalidades:

* Sincronização
* AD FS e integração da federação
* Passar por autenticação
* Monitorização do Estado de Funcionamento

Saiba mais:

* [Papel branco de identidade híbrida](https://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
* [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [Blog da equipa Azure AD](https://blogs.technet.microsoft.com/ad/)

## <a name="azure-ad-access-reviews"></a>Revisões de acesso do Azure AD

As revisões de acesso do Azure Active Directory (Azure AD) permitem que as organizações façam uma gestão eficiente das associações a grupos, acesso às aplicações empresariais e atribuições de funções privilegiadas.

Saiba mais:

* [Revisões de acesso do Azure AD](../../active-directory/governance/access-reviews-overview.md)
* [Gerir o acesso de utilizador com as revisões de acesso do Azure AD](../../active-directory/governance/access-reviews-overview.md)
