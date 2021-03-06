---
title: Problema configurar o fornecimento de utilizadores a uma aplicação da Azure AD Gallery
description: Como resolver problemas comuns ao configurar o fornecimento de utilizadores a uma aplicação já listada na Galeria de Aplicações Da AD do Azure
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
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3955b96e4a7edfc79a229d927523bdd4473409d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522769"
---
# <a name="problem-configuring-user-provisioning-to-an-azure-ad-gallery-application"></a>Problema de configuração do fornecimento de utilizadores a uma aplicação da Galeria AD Azure

Configurar [o fornecimento automático de utilizadores](user-provisioning.md) para uma aplicação (quando suportado), requer que sejam seguidas instruções específicas para preparar a aplicação para o fornecimento automático. Em seguida, pode utilizar o portal Azure para configurar o serviço de provisionamento para sincronizar as contas dos utilizadores na aplicação.

Deve sempre começar por encontrar o tutorial de configuração específico para a configuração do fornecimento para a sua aplicação. Em seguida, siga esses passos para configurar tanto a app como a Azure AD para criar a ligação de provisionamento. Uma lista de tutoriais de aplicações pode ser encontrada na [Lista de Tutoriais sobre Como Integrar apps SaaS com Diretório Ativo Azure](../saas-apps/tutorial-list.md).

## <a name="how-to-see-if-provisioning-is-working"></a>Como ver se o provisionamento está funcionando 

Uma vez configurado o serviço, a maioria dos conhecimentos sobre o funcionamento do serviço podem ser retirados de dois locais:

-   **Registos de fornecimento (pré-visualização)** – Os [registos](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) de fornecimento registam todas as operações efetuadas pelo serviço de provisionamento, incluindo a consulta da Azure AD para utilizadores designados que estejam no âmbito do provisionamento. Consultar a aplicação-alvo para a existência desses utilizadores, comparando os objetos de utilizador entre o sistema. Em seguida, adicione, atualize ou desative a conta de utilizador no sistema alvo com base na comparação. Pode aceder aos registos de fornecimento no portal Azure selecionando registos de provisionamento de &gt; **aplicações** &gt; de **diretório ativo azure** **(pré-visualização)** na secção **Atividade.**

-   **Estado atual –** Um resumo da última execução de provisionamento para uma determinada aplicação pode ser visto na secção de fornecimento de ** &gt; nomes &gt; \[\] &gt;** de aplicações de aplicações de aplicações de aplicações de aplicações de aplicações de aplicações de diretório ativo azure, na parte inferior do ecrã sob as definições de serviço. A secção Estado Atual mostra se um ciclo de provisionamento começou a fornecer contas de utilizador. Pode ver o progresso do ciclo, ver quantos utilizadores e grupos foram aprovisionados, e ver quantas funções são criadas. Se houver algum erro, os detalhes podem ser encontrados nos [registos de provisionamento (.. /reports-monitoring/concept-provisioning-logs.md?context=azure/active-directório/manage-apps/context/manage-apps-context/manage-apps-context).

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Áreas problemáticas gerais com disposição a considerar

Abaixo está uma lista das áreas problemáticas gerais que você pode perfurar se você tem uma ideia de por onde começar.

* [O serviço de provisionamento não parece ter começado](#provisioning-service-does-not-appear-to-start)
* Não é possível salvar a configuração devido a credenciais de aplicações que não funcionam
* [Os registos de provisionamento dizem que os utilizadores são "ignorados" e não são provisionados, mesmo que sejam atribuídos](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>O serviço de provisionamento não parece ter começado

Se definir o Estatuto de **Provisionamento** para estar **ligado** na secção de fornecimento de nomes ** &gt; &gt; \[\] &gt;** de aplicações de aplicações de aplicações de diretório ativo Azure do portal Azure. No entanto, não são apresentados outros detalhes do estado nessa página após as recargas subsequentes. É provável que o serviço esteja em execução, mas ainda não tenha concluído um ciclo inicial. Verifique os **registos de provisionamento** acima descritos para determinar que operações o serviço está a executar e se existem erros.

>[!NOTE]
>Um ciclo inicial pode demorar entre 20 minutos e várias horas, dependendo da dimensão do diretório Azure AD e do número de utilizadores no âmbito do provisionamento. As sincronizações subsequentes após o ciclo inicial são mais rápidas, uma vez que o serviço de fornecimento armazena marcas de água que representam o estado de ambos os sistemas após o ciclo inicial, melhorando o desempenho das sincronizações subsequentes.
>
>

## <a name="cant-save-configuration-due-to-app-credentials-not-working"></a>Não é possível salvar a configuração devido a credenciais de aplicações que não funcionam

Para que o provisionamento funcione, a Azure AD requer credenciais válidas que lhe permitam ligar-se a uma API de gestão de utilizadores fornecida por essa app. Se estas credenciais não funcionarem, ou se não souberem quais são, reveja o tutorial para a configuração desta aplicação, descrita anteriormente.

## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Os registos de provisionamento dizem que os utilizadores são ignorados e não são provisionados, mesmo que sejam atribuídos

Quando um utilizador aparece como "ignorado" nos registos de fornecimento, é muito importante ler os detalhes estendidos na mensagem de registo para determinar o motivo. Seguem-se razões e resoluções comuns:

- **Foi configurado um filtro** de deteção **que está a filtrar o utilizador com base num valor**de atributo . Para obter mais informações, consulte o [fornecimento de aplicações baseado no Atributo com filtros](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)de deteção .

- **O utilizador "não tem direito a efetivamente".** Se vir esta mensagem de erro específica, é porque existe um problema com o registo de atribuição de utilizadores armazenado em Azure AD. Para corrigir este problema, não atribuir o utilizador (ou grupo) da aplicação e atribuí-lo novamente. Para mais informações, consulte [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md).

- **Falta um atributo obrigatório ou não é povoado para um utilizador.** Uma coisa importante a considerar ao configurar o provisionamento é rever e configurar os mapeamentos e fluxos de trabalho de atributos que definem quais as propriedades do utilizador (ou grupo) que fluem da AD Azure para a aplicação. Isto inclui a definição da "propriedade correspondente" que é usada para identificar e combinar exclusivamente utilizadores/grupos entre os dois sistemas. Para obter mais informações sobre este importante processo, consulte personalizar [o fornecimento de atributos-mapeamentos.](../app-provisioning/customize-application-attributes.md)

  * **Atribuim mapeamentos para grupos:** Fornecimento do nome do grupo e dos detalhes do grupo, para além dos membros, se apoiados em algumas candidaturas. Pode ativar ou desativar esta funcionalidade, permitindo ou desativando o **Mapeamento** para objetos de grupo mostrados no separador **Provisioning.** Se os grupos de aprovisionamento estiverem ativados, certifique-se de rever os mapeamentos do atributo para garantir que está a ser utilizado um campo adequado para o "ID correspondente". Este pode ser o nome de exibição ou pseudónimo de e-mail), uma vez que o grupo e os seus membros não são provisionados se a propriedade correspondente estiver vazia ou não povoada para um grupo em Azure AD.

## <a name="next-steps"></a>Passos seguintes
[Automatizar o fornecimento e o deprovisionamento de utilizadores às aplicações SaaS com diretório ativo Azure](user-provisioning.md)
