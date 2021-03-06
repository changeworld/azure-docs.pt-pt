---
title: Migração de conta do Cloud Partner Portal para Partner Center - mercado comercial para o Azure
description: Como migrar a sua conta de CPP para Partner Center. - mercado comercial para o Azure
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: e17a76d5a017400287644ad2da46caa5b6636654
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262303"
---
# <a name="account-migration-from-cloud-partner-portal-to-partner-center"></a>Migração da conta do Cloud Partner Portal para o Centro de Parceiros

Se tiver uma conta existente no Portal do Parceiro cloud (CPP), as definições da sua conta têm de ser migradas para o Partner Center.

## <a name="account-migration-process"></a>Processo de migração de contas

Se for um utilizador com a função de Proprietário em CPP para uma determinada conta, é mostrado um banner amarelo na sua página de Perfil de Editor. Pode pertencer a um dos dois casos seguintes:

- A sua conta já foi migrada e está pronta para gerir as Definições de Conta no Centro de Parceiros.
- A sua conta não foi migrada para o Partner Center e precisa de tomar mais medidas.

### <a name="your-account-has-been-migrated-to-partner-center"></a>A sua conta foi migrada para o Partner Center.

Para todas as contas que tenham concluído a migração do CPP para o Partner Center, a gestão de conta vai acontecer no Partner Center. Alterações como a adição/eliminação do utilizador serão sincronizadas de volta ao CPP.

### <a name="you-have-not-yet-migrated-your-account-to-partner-center"></a>Ainda não emigrou a sua conta para o Partner Center.

Clique no banner para iniciar o processo de migração da sua conta. Espera-se que introduza os seguintes itens:

1. Endereço de e-mail de trabalho: <br> <br> Na maioria dos casos, inscreva-se no endereço de e-mail que utiliza para assinar em CPP. Em certos casos, deve ser utilizado um endereço de e-mail diferente:

    * Conta Microsoft: Se a conta CPP for uma conta Microsoft, introduza um email de trabalho válido associado ao inquilino para o qual o ID da Microsoft Partner Network (MPN) está registado. Para mais informações, consulte O Programa de Rede de [Parceiros da Microsoft](#sign-up-for-microsoft-partner-network-program).

    * Incompatibilidade de inquilino: Se o seu endereço de e-mail de trabalho não pertencer ao inquilino associado ao ID da Microsoft Partner Network presente na sua conta CPP, então verá um erro. Para ultrapassar este erro, insira um endereço de e-mail associado ao inquilino. Uma mensagem de erro fornecerá o nome do inquilino.

2. Inscreva-se no programa Microsoft Partner Network

    Se a sua conta CPP não tiver um ID da Microsoft Partner Network ou tiver um que seja inválido, terá de se inscrever no programa Microsoft Partner Network como parte do processo de ativação.

## <a name="sign-up-for-microsoft-partner-network-program"></a>Inscreva-se no programa Microsoft Partner Network

As empresas que queiram fazer parceria com a Microsoft devem juntar-se à Microsoft Partner Network (MPN) e obter um ID MPN. Se já é membro da Microsoft Partner Network e tem um ID MPN, mantenha a informação à mão, pois necessitará durante o processo de ativação da conta.  

Se não for membro da Microsoft Partner Network, pode [juntar-se aqui](https://signup.microsoft.com/signup?sku=StoreForBusinessIW&origin=partnerdashboard&culture=en-us&ru=https://partner.microsoft.com/dashboard/account/v3/xpu/onboard?ru=/en-us/dashboard/account/v3/enrollment/companyprofile/basicpartnernetwork/new) para obter uma identificação de MPN. Tome nota da sua identificação mpn, pois terá de o introduzir durante o processo de ativação da conta.

Para saber mais sobre a Microsoft Partner Network, consulte [Join the Microsoft Partner Network](https://partner.microsoft.com/en-US/membership) no site do parceiro. Para saber mais sobre os benefícios do ISV na Rede de Parceiros da Microsoft, consulte o [IsV Resource Hub](https://partner.microsoft.com/isv-resource-hub).  

## <a name="move-dynamics-365-and-powerapps-offers-to-partner-center"></a>Move Dynamics 365 e PowerApps oferece ao Partner Center

Para agilizar a gestão de conta e oferecer gestão para a Dynamics 365 Customer Engagement, PowerApps e Dynamics 365 Operations, as ofertas foram transferidas para [partner Center.](https://partner.microsoft.com/) O movimento garante que o mesmo conteúdo está disponível tanto para catálogos públicos como para vendedores.

Para obter informações específicas sobre o que precisa de ser feito até 15 de outubro de **2019** para as ofertas de Participação de Clientes Da Dynamics 365, PowerApps e Dynamics 365 Operations, siga as instruções abaixo.

> [!NOTE]
> Isto não se aplica às ofertas da Dynamics 365 Business Central.  

1. Se a sua conta de adesão à MPN foi originalmente criada no Partner Membership Center (PMC), inscreva-se no [Partner Center](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) para confirmar que a sua conta foi migrada. Se vir um ecrã de perfil com a sua identificação mpn, está pronto para continuar. Caso contrário, tem de iniciar a sua migração de conta seguindo as indicações no [Centro de Adesão](https://partners.microsoft.com/partnerprogram/Welcome.aspx)ao Parceiro . Se precisar de ajuda, visite [o apoio.](https://partner.microsoft.com/support?issueid=100-0077)
2. Vá à página de visão geral do [Mercado Comercial no Partner Center.](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) Se vir "Mercado Comercial" no painel de navegação à esquerda, está matriculado e deve continuar até ao próximo passo. Caso contrário, [inscreva-se no mercado comercial](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv) agora.
3. Confirme que as suas ofertas estão no AppSource [procurando as suas ofertas.](https://appsource.microsoft.com/) Se as suas ofertas já estiverem no AppSource, continue para o próximo passo. Para qualquer oferta que não seja no AppSource, crie uma nova oferta de Envolvimento com o [Cliente Dynamics 365](create-new-customer-engagement-offer.md) ou uma nova oferta de [Operações Dynamics 365.](create-new-operations-offer.md)
4. Na página de [Acordos](https://partner.microsoft.com/dashboard/account/agreements)do Partner Center, certifique-se de que reviu e aceitou a **Addendum ISV Addendum de Aplicações Empresariais**.
5. Nas [definições](https://partner.microsoft.com/dashboard/account/v3/accountsettings/billingprofile)de Conta do Partner Center, certifique-se de que as informações de faturação estão completas.
6. Envie cada nova e existente oferta para certificação e publicação, mesmo que as suas ofertas tenham sido previamente certificadas.
    * Complete os ecrãs de informação, incluindo o fornecimento da sua app para certificação, bem como informações de marketing. Selecione **Submeter** (canto superior direito do ecrã) até 15 de outubro de **2019**. Estes passos devem ser concluídos para evitar o impacto na disponibilidade da oferta.
    * Se for elegível, poderá solicitar a participação no escalão premium durante este processo.
    * A certificação ou recertificação requer que a sua aplicação suporte a versão mais recente da nossa Plataforma de Aplicações Empresariais.
    * Uma vez aprovada a sua aplicação, receberá um e-mail para voltar à oferta e selecionar "ir ao vivo" para permitir que a oferta seja transmitida em direto no Microsoft AppSource.

## <a name="additional-resources"></a>Recursos Adicionais

Junte-se à chamada semanal da [comunidade Dynamics ISV](https://aka.ms/DynamicsISV-CommunityCall) para apoio e atualizações.

Se precisar de ajuda para publicar, certificar ou gerir as suas ofertas de marketplace, envie um bilhete de [apoio.](https://aka.ms/MarketplacePublisherSupport)

## <a name="next-steps"></a>Passos seguintes

- [Gerencie a sua conta de mercado comercial no Partner Center](./manage-account.md)
