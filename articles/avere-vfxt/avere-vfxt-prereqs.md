---
title: Pré-requisitos avere vFXT - Azure
description: Pré-requisitos para Avere vFXT para Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: rohogue
ms.openlocfilehash: a183989cc666f00da4be077c719c40d2524fd6e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252548"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>Preparar para criar o vFXT Avere

Este artigo explica tarefas pré-requisitos para a criação de um cluster Avere vFXT.

## <a name="create-a-new-subscription"></a>Criar uma nova subscrição

Comece por criar uma nova subscrição Azure. Utilize uma subscrição separada para cada projeto Avere vFXT para simplificar o rastreio e limpeza de despesas e para garantir que outros projetos não sejam afetados por quotas ou estrangulamento de recursos ao fornecer o fluxo de trabalho do cluster.

Para criar uma nova subscrição Azure no portal Azure:

1. Navegue para a [lâmina de assinaturas](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)
1. Clique no botão **+ Adicionar** na parte superior
1. Inscreva-se se solicitado
1. Selecione uma oferta e caminhe pelos passos para criar uma nova subscrição

## <a name="configure-subscription-owner-permissions"></a>Configure permissões do proprietário da subscrição

Um utilizador com permissões do proprietário para a subscrição deve criar o cluster vFXT. A criação de cluster requer que um proprietário aceite os termos de serviço do software e autorize alterações aos recursos de rede e armazenamento.

Existem algumas sobras para permitir que um não proprietário crie um Avere vFXT para cluster Azure. Estes cenários envolvem restringir os recursos e atribuir ao criador funções adicionais de controlo de acesso (RBAC) com base em papéis. Em todos estes casos, um proprietário de subscrição também deve [aceitar os termos do software Avere vFXT](#accept-software-terms) com antecedência.

| Cenário | Restrições | Funções de acesso necessárias para criar o cluster Avere vFXT |
|----------|--------|-------|
| Administrador do grupo de recursos cria o vFXT | A rede virtual, o controlador de cluster e os nós de cluster devem ser criados dentro do grupo de recursos. | [Funções](../role-based-access-control/built-in-roles.md#user-access-administrator) de Administrador de Acesso ao Utilizador e [Colaborador,](../role-based-access-control/built-in-roles.md#contributor) ambas orientadas para o grupo de recursos-alvo. |
| Utilize uma rede virtual externa existente | O controlador de cluster e os nós de cluster são criados dentro do grupo de recursos do VFXT, mas usam uma rede virtual existente num grupo de recursos diferente. | (1) Funções de Administrador de [Acesso ao Utilizador](../role-based-access-control/built-in-roles.md#user-access-administrator) e Do [Contribuinte](../role-based-access-control/built-in-roles.md#contributor) ao grupo de recursos vFXT; e (2) [Contribuidor de Máquinas Virtuais,](../role-based-access-control/built-in-roles.md#virtual-machine-contributor) [Administrador de Acesso ao Utilizador](../role-based-access-control/built-in-roles.md#user-access-administrator)e Funções do Colaborador [Avere](../role-based-access-control/built-in-roles.md#avere-contributor) ao grupo de recursos da rede virtual. |
| Papel personalizado para criadores de clusters | Sem restrições de colocação de recursos. Este método confere aos não proprietários privilégios significativos. | O proprietário da subscrição cria uma função RBAC personalizada, como explicado [neste artigo.](avere-vfxt-non-owner.md) |

## <a name="quota-for-the-vfxt-cluster"></a>Quota para o cluster vFXT

Verifique se tem quota suficiente para os seguintes componentes Azure. Se necessário, [solicite um aumento de quota.](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)

> [!NOTE]
> As máquinas virtuais e os componentes SSD listados aqui são para o próprio cluster vFXT. Lembre-se que também precisa de quota para os VMs e SSDs que utilizará para a sua quinta de cálculo.
>
> Certifique-se de que a quota está ativada para a região onde pretende executar o fluxo de trabalho.

|Componente do Azure|Quota|
|----------|-----------|
|Virtual Machines|3 ou mais E32s_v3 (um por nó de cluster) |
|Armazenamento SSD Premium|200 GB de espaço para SO e 1 a 4 TB de espaço na cache por nó |
|Conta de armazenamento (opcional) |v2|
|Armazenamento de back-end de dados de dados (opcional) |Um novo recipiente LRS Blob |
<!-- this table also appears in the overview - update it there if updating here -->

## <a name="accept-software-terms"></a>Aceitar termos de software

> [!TIP]
> Ignore este passo se um proprietário de subscrição criar o cluster Avere vFXT.

Durante a criação do cluster, deve aceitar os termos de serviço para o software Avere vFXT. Se não for proprietário de subscrição, faça com que um proprietário de subscrição aceite os termos com antecedência.

Este passo só tem de ser feito uma vez por subscrição.

Para aceitar os termos do software com antecedência:

1. Abra uma casca de nuvem no portal <https://shell.azure.com>Azure ou navegando para . Inscreva-se com o seu ID de subscrição.

   ```azurecli
    az login
    az account set --subscription <subscription ID>
   ```

1. Emita este comando para aceitar termos de serviço e permitir o acesso programático para o Avere vFXT para a imagem de software Azure:

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   ```

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>Crie um ponto final do serviço de armazenamento na sua rede virtual (se necessário)

Um [ponto final](../virtual-network/virtual-network-service-endpoints-overview.md) de serviço mantém o tráfego de Azure Blob local em vez de encaminha-lo para fora da rede virtual. Recomenda-se qualquer Avere vFXT para cluster Azure que utilize o Azure Blob para armazenamento de dados de back-end.

Se criar uma nova rede virtual enquanto cria o cluster, é criado automaticamente um ponto final. Se fornecer uma rede virtual existente, deve ter um ponto final do serviço de armazenamento da Microsoft se quiser criar um novo recipiente de armazenamento Blob durante a criação do cluster.<!-- if there is no endpoint in that situation, the cluster creation will fail -->

> [!TIP]
>
>* Ignore este passo se estiver a criar uma nova rede virtual como parte da criação de clusters.
>* Um ponto final é opcional se não estiver a criar armazenamento Blob durante a criação de cluster. Nesse caso, pode criar o ponto final do serviço mais tarde se decidir utilizar o Azure Blob.

Crie o ponto final do serviço de armazenamento a partir do portal Azure.

1. No portal, abra a sua lista de redes virtuais.
1. Selecione a rede virtual para o seu cluster.
1. Clique em **pontos finais** do Serviço a partir do menu esquerdo.
1. Clique em **Adicionar** no topo.
1. Escolha o ``Microsoft.Storage``serviço .
1. Selecione a sub-rede do cluster.
1. Na parte inferior, clique em **Adicionar**.

   ![Imagem do portal Azure com anotações para os passos de criação do ponto final do serviço](media/avere-vfxt-service-endpoint.png)

## <a name="next-steps"></a>Passos seguintes

Depois de completar estes pré-requisitos, pode criar o cluster. Leia [Implementar o cluster vFXT](avere-vfxt-deploy.md) para obter instruções.
