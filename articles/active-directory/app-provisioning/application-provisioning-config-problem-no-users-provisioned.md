---
title: Nenhum utilizador está a ser provisionado para uma aplicação da Galeria AD Azure
description: Como resolver problemas comuns quando não se vê utilizadores a aparecer numa Aplicação de Galeria AD Azure configurada para o fornecimento de utilizadores com AD Azure
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b60261d63e1bcb75aea9d2e8a6b74902520f391
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522922"
---
# <a name="no-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Nenhum utilizador está a ser provisionado para uma aplicação da Galeria AD Azure
Depois de configurado o fornecimento automático para uma aplicação (incluindo verificar se as credenciais de aplicação fornecidas à Azure AD para se conectarem à app são válidas), então os utilizadores e/ou grupos são fornecidos à aplicação. O provisionamento é determinado pelas seguintes coisas:

-   Quais os utilizadores e grupos que foram **atribuídos** à aplicação. Note que o fornecimento de grupos aninhados ou grupos do Office 365 não é apoiado. Para obter mais informações sobre a atribuição, consulte [Atribuir um utilizador ou grupo a uma aplicação empresarial no Azure Ative Directory](../manage-apps/assign-user-or-group-access-portal.md).
-   Se os **mapeamentos de atributos** estão ou não ativados e configurados para sincronizar atributos válidos do Azure AD para a app. Para obter mais informações sobre mapeamentos de atributos, consulte o personalização dos [mapeamentos de atributos para aplicações SaaS no Diretório Ativo Azure](customize-application-attributes.md).
-   Se existe ou não um filtro de **deteção** que está a filtrar os utilizadores com base em valores específicos do atributo. Para obter mais informações sobre filtros de deteção, consulte o [fornecimento de aplicações baseado no Atributo com filtros](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)de deteção .
  
Se observar que os utilizadores não estão a ser aprovisionados, consulte os registos de [provisionamento (pré-visualização)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) em Azure AD. Procure entradas de registo para um utilizador específico.

Pode aceder aos registos de fornecimento no portal Azure selecionando registos de provisionamento de &gt; **aplicações** &gt; de **diretório ativo azure** **(pré-visualização)** na secção **Atividade.** Pode pesquisar os dados de provisionamento com base no nome do utilizador ou no identificador no sistema de origem ou no sistema de destino. Para mais detalhes, consulte [os registos de provisionamento (pré-visualização)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). 

Os registos de fornecimento registam todas as operações efetuadas pelo serviço de provisionamento, incluindo a consulta da AD Azure para utilizadores designados que estão no âmbito do provisionamento, consultando a app-alvo para a existência desses utilizadores, comparando os objetos de utilizador entre o sistema. Em seguida, adicione, atualize ou desative a conta de utilizador no sistema alvo com base na comparação.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Áreas de problemas gerais com provisionamento a considerar
Abaixo está uma lista das áreas problemáticas gerais que você pode perfurar se você tem uma ideia de por onde começar.

- [O serviço de provisionamento não parece ter começado](#provisioning-service-does-not-appear-to-start)
- [Os registos de provisionamento dizem que os utilizadores são ignorados e não são provisionados, mesmo que sejam atribuídos](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>O serviço de provisionamento não parece ter começado
Se definir o Estatuto de **Provisionamento** para estar **ligado** na secção de fornecimento de nomes ** &gt; &gt; \[\] &gt;** de aplicações de aplicações de aplicações de diretório ativo Azure do portal Azure. No entanto, não são apresentados outros detalhes de estado nessa página após as recargas subsequentes, é provável que o serviço esteja em execução, mas ainda não tenha concluído um ciclo inicial. Verifique os **registos de provisionamento (pré-visualização)** acima descritos para determinar que operações o serviço está a executar e se existem erros.

>[!NOTE]
>Um ciclo inicial pode demorar entre 20 minutos e várias horas, dependendo da dimensão do diretório Azure AD e do número de utilizadores no âmbito do provisionamento. As sincronizações subsequentes após o ciclo inicial são mais rápidas, uma vez que o serviço de fornecimento armazena marcas de água que representam o estado de ambos os sistemas após o ciclo inicial. O ciclo inicial melhora o desempenho das sincronizações subsequentes.
>


## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Os registos de provisionamento dizem que os utilizadores são ignorados e não são provisionados, mesmo que sejam atribuídos

Quando um utilizador aparece como "ignorado" nos registos de fornecimento, é importante rever o separador **Passos** do registo para determinar a razão. Seguem-se razões e resoluções comuns:

- **Foi configurado um filtro** de deteção **que está a filtrar o utilizador com base num valor**de atributo . Para obter mais informações sobre filtros de deteção, consulte [filtros de deteção](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
- **O utilizador "não tem direito a efetivamente".** Se vir esta mensagem de erro específica, é porque existe um problema com o registo de atribuição de utilizadores armazenado em Azure AD. Para corrigir este problema, desatribua o utilizador (ou grupo) da aplicação e reatribua-o novamente. Para obter mais informações sobre a atribuição, consulte [O utilizador da Atribuição ou o acesso ao grupo](../manage-apps/assign-user-or-group-access-portal.md).
- **Falta um atributo obrigatório ou não é povoado para um utilizador.** Uma coisa importante a ter em conta na configuração do provisionamento é rever e configurar os mapeamentos e fluxos de trabalho de atributos que definem quais as propriedades do utilizador (ou grupo) que fluem da AD Azure para a aplicação. Esta configuração inclui a definição da "propriedade correspondente" que é usada para identificar e combinar exclusivamente utilizadores/grupos entre os dois sistemas. Para obter mais informações sobre este importante processo, consulte a personalização dos [mapeamentos de atributos para aplicações SaaS no Diretório Ativo Azure](customize-application-attributes.md).
- **Atribuim mapeamentos para grupos:** Fornecimento do nome do grupo e dos detalhes do grupo, para além dos membros, se apoiados em algumas candidaturas. Pode ativar ou desativar esta funcionalidade, permitindo ou desativando o **Mapeamento** para objetos de grupo mostrados no separador **Provisioning.** Se os grupos de aprovisionamento estiverem ativados, certifique-se de rever os mapeamentos do atributo para garantir que está a ser utilizado um campo adequado para o "ID correspondente". O ID correspondente pode ser o nome de exibição ou o pseudónimo de e-mail. O grupo e os seus membros não são provisionados se a propriedade correspondente estiver vazia ou não povoada para um grupo em Azure AD.

## <a name="next-steps"></a>Passos seguintes

[Sincronização Azure AD Connect: Compreensão do Provisionamento Declarativo](../hybrid/concept-azure-ad-connect-sync-declarative-provisioning.md)
