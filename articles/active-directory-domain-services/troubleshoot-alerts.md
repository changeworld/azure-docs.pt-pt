---
title: Alertas e resoluções comuns nos Serviços de Domínio da AD azure [ Microsoft Docs
description: Saiba como resolver alertas comuns gerados como parte do estado de saúde dos Serviços de Domínio de Diretório Ativo Azure
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: ed791ea10c072308c16baac9fa469a46b19ec648
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654503"
---
# <a name="known-issues-common-alerts-and-resolutions-in-azure-active-directory-domain-services"></a>Questões conhecidas: Alertas e resoluções comuns nos Serviços de Domínio ativo do Diretório Azure

Como parte central da identidade e autenticação para aplicações, o Azure Ative Directory Domain Services (Azure AD DS) tem, por vezes, problemas. Se tiver problemas, existem alguns alertas comuns e passos associados para ajudar a pôr as coisas a funcionar novamente. A qualquer momento, também pode abrir um pedido de [apoio azure][azure-support] para assistência adicional para resolução de problemas.

Este artigo fornece informações de resolução de problemas para alertas comuns em Azure AD DS.

## <a name="aadds100-missing-directory"></a>AADDS100: Diretório desaparecido

### <a name="alert-message"></a>Mensagem de alerta

*O diretório Azure AD associado ao seu domínio gerido pode ter sido eliminado. O domínio gerido já não se encontra numa configuração suportada. A Microsoft não pode monitorizar, gerir, corrigir e sincronizar o seu domínio gerido.*

### <a name="resolution"></a>Resolução

Este erro é geralmente causado quando uma subscrição Azure é transferida para um novo diretório Azure AD e o antigo diretório Azure AD que está associado com Azure AD DS é eliminado.

Este erro é irrecuperável. Para resolver o alerta, [elimine o domínio gerido pelo Azure AD DS existente](delete-aadds.md) e recrie-o no seu novo diretório. Se tiver dificuldade em apagar o domínio gerido pela AD DS Azure, abra um pedido de [apoio Azure][azure-support] para assistência adicional para resolução de problemas.

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101: Azure AD B2C está em execução neste diretório

### <a name="alert-message"></a>Mensagem de alerta

*Os Serviços de Domínio Azure AD não podem ser ativados num Diretório Azure AD B2C.*

### <a name="resolution"></a>Resolução

O Azure AD DS sincroniza-se automaticamente com um diretório Azure AD. Se o diretório Azure AD estiver configurado para B2C, o Azure AD DS não pode ser implantado e sincronizado.

Para utilizar o Azure AD DS, deve recriar o seu domínio gerido num diretório AD B2C não-Azure utilizando os seguintes passos:

1. [Elimine o domínio gerido pelo Azure AD DS](delete-aadds.md) a partir do seu diretório Azure AD existente.
1. Crie um novo diretório Azure AD que não seja um diretório Azure AD B2C.
1. [Criar um domínio gerido azure DS de substituição.](tutorial-create-instance.md)

O Azure AD DS gerido a saúde do domínio atualiza-se automaticamente dentro de duas horas e remove o alerta.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103: Endereço está numa gama pública de IP

### <a name="alert-message"></a>Mensagem de alerta

*A gama de endereços IP para a rede virtual em que ativou os Serviços de Domínio Azure AD está numa gama pública de IP. Os Serviços de Domínio Azure AD devem ser ativados numa rede virtual com uma gama privada de endereços IP. Esta configuração afeta a capacidade da Microsoft de monitorizar, gerir, corrigir e sincronizar o seu domínio gerido.*

### <a name="resolution"></a>Resolução

Antes de começar, certifique-se de que compreende os espaços privados de [endereço IP v4](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces).

Dentro de uma rede virtual, os VMs podem fazer pedidos aos recursos do Azure na mesma gama de endereços IP configurado para a subrede. Se configurar uma gama pública de endereços IP para uma subnet, os pedidos encaminhados para dentro de uma rede virtual podem não chegar aos recursos web pretendidos. Esta configuração pode levar a erros imprevisíveis com O DS Azure.

> [!NOTE]
> Se possuir a gama de endereços IP na internet que está configurada na sua rede virtual, este alerta pode ser ignorado. No entanto, os Serviços de Domínio Da Azure AD não podem comprometer-se com o [SLA](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/)com esta configuração, uma vez que pode levar a erros imprevisíveis.

Para resolver este alerta, elimine o domínio gerido pelo Azure AD DS existente e recrie-o numa rede virtual com uma gama de endereços IP privado. Este processo é disruptivo, uma vez que o domínio gerido pelo Azure AD DS não está disponível e quaisquer recursos personalizados que tenha criado como OUs ou contas de serviço estão perdidos.

1. [Elimine o domínio gerido pelo Azure AD DS](delete-aadds.md) do seu diretório.
1. Para atualizar a gama de endereços IP da rede virtual, procure e selecione *rede Virtual* no portal Azure. Selecione a rede virtual para Azure AD DS que tenha incorretamente um conjunto de endereços IP públicos.
1. Em **Definições,** selecione *Espaço de Endereço*.
1. Atualize a gama de endereços escolhendo a gama de endereços existente e editando-a, ou adicionando uma gama de endereços adicional. Certifique-se de que a nova gama de endereços IP está numa gama ip privada. Quando estiver pronto, **guarde** as alterações.
1. Selecione **Subredes** na navegação à esquerda.
1. Escolha a sub-rede que deseja editar ou crie uma sub-rede adicional.
1. Atualize ou especifique uma gama de endereços IP privado, em seguida, **Guarde** as suas alterações.
1. [Criar um domínio gerido azure DS de substituição.](tutorial-create-instance.md) Certifique-se de que escolhe a subnet a rede virtual atualizada com uma gama privada de endereços IP.

O Azure AD DS gerido a saúde do domínio atualiza-se automaticamente dentro de duas horas e remove o alerta.

## <a name="aadds106-your-azure-subscription-is-not-found"></a>AADDS106: A sua subscrição Azure não é encontrada

### <a name="alert-message"></a>Mensagem de alerta

*A subscrição Azure associada ao seu domínio gerido foi eliminada.  Os Serviços de Domínio Azure AD exigem uma subscrição ativa para continuar a funcionar corretamente.*

### <a name="resolution"></a>Resolução

O Azure AD DS requer uma subscrição ativa e não pode ser transferido para uma subscrição diferente. Se a subscrição Azure com a que o domínio gerido pelo Azure AD DS foi associado for eliminada, deve recriar uma subscrição Azure e o domínio gerido pelo Azure AD DS.

1. [Criar uma subscrição Azure.](../cost-management-billing/manage/create-subscription.md)
1. [Elimine o domínio gerido pelo Azure AD DS](delete-aadds.md) a partir do seu diretório Azure AD existente.
1. [Criar um domínio gerido azure DS de substituição.](tutorial-create-instance.md)

## <a name="aadds107-your-azure-subscription-is-disabled"></a>AADDS107: A sua subscrição Azure está desativada

### <a name="alert-message"></a>Mensagem de alerta

*A subscrição azure associada ao seu domínio gerido não está ativa.  Os Serviços de Domínio Azure AD exigem uma subscrição ativa para continuar a funcionar corretamente.*

### <a name="resolution"></a>Resolução

O Azure AD DS requer uma subscrição ativa. Se a subscrição Azure com a qual o domínio gerido pelo Azure AD DS estava associado não estiver ativa, tem de o renovar para reativar a subscrição.

1. [Renove a sua subscrição Azure.](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable)
2. Uma vez renovada a subscrição, uma notificação Azure AD DS permite-lhe reativar o domínio gerido.

Quando o domínio gerido é ativado novamente, o Azure AD DS gerido a saúde do domínio atualiza-se automaticamente dentro de duas horas e remove o alerta.

## <a name="aadds108-subscription-moved-directories"></a>AADDS108: Diretórios movidos por subscrição

### <a name="alert-message"></a>Mensagem de alerta

*A subscrição utilizada pela Azure AD Domain Services foi transferida para outro diretório. Os Serviços de Domínio Azure AD precisam de ter uma subscrição ativa no mesmo diretório para funcionar corretamente.*

### <a name="resolution"></a>Resolução

O Azure AD DS requer uma subscrição ativa e não pode ser transferido para uma subscrição diferente. Se a subscrição Azure com a que o domínio gerido pelo Azure AD DS estava associado for movida, desloque a subscrição para o diretório anterior ou [elimine](delete-aadds.md) o seu domínio gerido do diretório existente e crie um domínio gerido pelo [Azure AD DS na subscrição escolhida.](tutorial-create-instance.md)

## <a name="aadds109-resources-for-your-managed-domain-cannot-be-found"></a>AADDS109: Não se encontram recursos para o seu domínio gerido

### <a name="alert-message"></a>Mensagem de alerta

*Foi eliminado um recurso utilizado para o seu domínio gerido. Este recurso é necessário para que os Serviços de Domínio Azure AD funcionem corretamente.*

### <a name="resolution"></a>Resolução

O Azure AD DS cria recursos adicionais para funcionar corretamente, tais como endereços IP públicos, interfaces de rede virtual e um equilibrador de carga. Se algum destes recursos for eliminado, o domínio gerido encontra-se num estado não apoiado e impede que o domínio seja gerido. Para obter mais informações sobre estes recursos, consulte [os recursos da Rede utilizados pela Azure AD DS](network-considerations.md#network-resources-used-by-azure-ad-ds).

Este alerta é gerado quando um destes recursos necessários é eliminado. Se o recurso foi apagado há menos de 4 horas, existe a possibilidade de a plataforma Azure poder recriar automaticamente o recurso eliminado. Os seguintes passos descrevem como verificar o estado de saúde e a marca ção de tempo para a eliminação dos recursos:

1. No portal Azure, procure e selecione Serviços de **Domínio.** Escolha o seu domínio gerido pelo Azure AD DS, como *aaddscontoso.com*.
1. Na navegação à esquerda, selecione **Health**.
1. Na página de saúde, selecione o alerta com o ID *AADDS109*.
1. O alerta tem um carimbo temporal para quando foi encontrado pela primeira vez. Se essa marca de tempo for inferior a 4 horas atrás, a plataforma Azure poderá ser capaz de recriar automaticamente o recurso e resolver o alerta por si só.

    Se o alerta tiver mais de 4 horas de idade, o domínio gerido pela AD DS azure está num estado irrecuperável. [Elimine o domínio gerido pelo Azure AD DS](delete-aadds.md) e, em seguida, [crie um domínio gerido](tutorial-create-instance.md)por substituição .

## <a name="aadds110-the-subnet-associated-with-your-managed-domain-is-full"></a>AADDS110: A subnet associada ao seu domínio gerido está cheia

### <a name="alert-message"></a>Mensagem de alerta

*A sub-rede selecionada para implantação dos Serviços de Domínio Azure AD está cheia e não tem espaço para o controlador de domínio adicional que precisa de ser criado.*

### <a name="resolution"></a>Resolução

A subnet de rede virtual para O DS Azure necessita de endereços IP suficientes para os recursos criados automaticamente. Este espaço de endereço IP inclui a necessidade de criar recursos de substituição se houver um evento de manutenção. Para minimizar o risco de ficar sem endereços IP disponíveis, não desloque recursos adicionais, como os seus próprios VMs, na mesma subnet de rede virtual que o Azure AD DS.

Este erro é irrecuperável. Para resolver o alerta, [elimine o domínio gerido pelo Azure AD DS existente](delete-aadds.md) e recrie-o. Se tiver dificuldade em apagar o domínio gerido pela AD DS Azure, abra um pedido de [apoio Azure][azure-support] para assistência adicional para resolução de problemas.

## <a name="aadds111-service-principal-unauthorized"></a>AADDS111: Diretor de serviço não autorizado

### <a name="alert-message"></a>Mensagem de alerta

*Um diretor de serviço que o Azure AD Domain Services utiliza para servir o seu domínio não está autorizado a gerir recursos na subscrição do Azure. O diretor de serviço precisa de obter permissões para servir o seu domínio gerido.*

### <a name="resolution"></a>Resolução

Alguns diretores de serviço gerados automaticamente são usados para gerir e criar recursos para um domínio gerido pelo Azure AD DS. Se as permissões de acesso a um destes diretores de serviço forem alteradas, o domínio não é capaz de gerir corretamente os recursos. Os seguintes passos mostram-lhe como compreender e, em seguida, conceder permissões de acesso a um diretor de serviço:

1. Leia sobre [o controlo de acesso baseado em papéis e como conceder acesso a aplicações no portal Azure.](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
2. Reveja o acesso que o diretor de serviço com o ID *abba844e-bc0e-44b0-947a-dc74e5d090222* tem e concede o acesso que foi negado numa data anterior.

## <a name="aadds112-not-enough-ip-address-in-the-managed-domain"></a>AADDS112: Não há endereço IP suficiente no domínio gerido

### <a name="alert-message"></a>Mensagem de alerta

*Identificámos que a sub-rede da rede virtual neste domínio pode não ter endereços IP suficientes. Os Serviços de Domínio Azure AD precisam de pelo menos dois endereços IP disponíveis dentro da sub-rede em que está ativado. Recomendamos que tenha pelo menos 3-5 endereços IP sobressalentes dentro da sub-rede. Isto pode ter ocorrido se outras máquinas virtuais forem implantadas dentro da sub-rede, esgotando assim o número de endereços IP disponíveis ou se houver uma restrição ao número de endereços IP disponíveis na sub-rede.*

### <a name="resolution"></a>Resolução

A subnet de rede virtual para Azure AD DS necessita de endereços IP suficientes para os recursos criados automaticamente. Este espaço de endereço IP inclui a necessidade de criar recursos de substituição se houver um evento de manutenção. Para minimizar o risco de ficar sem endereços IP disponíveis, não desloque recursos adicionais, como os seus próprios VMs, na mesma subnet de rede virtual que o Azure AD DS.

Para resolver este alerta, elimine o domínio gerido pelo Azure AD DS existente e recrie-o numa rede virtual com uma gama de endereços IP suficientemente grande. Este processo é disruptivo, uma vez que o domínio gerido pelo Azure AD DS não está disponível e quaisquer recursos personalizados que tenha criado como OUs ou contas de serviço estão perdidos.

1. [Elimine o domínio gerido pelo Azure AD DS](delete-aadds.md) do seu diretório.
1. Para atualizar a gama de endereços IP da rede virtual, procure e selecione *rede Virtual* no portal Azure. Selecione a rede virtual para Azure AD DS que tenha a pequena gama de endereços IP.
1. Em **Definições,** selecione *Espaço de Endereço*.
1. Atualize a gama de endereços escolhendo a gama de endereços existente e editando-a, ou adicionando uma gama de endereços adicional. Certifique-se de que a nova gama de endereços IP é suficientemente grande para a gama de sub-redes Azure AD DS. Quando estiver pronto, **guarde** as alterações.
1. Selecione **Subredes** na navegação à esquerda.
1. Escolha a sub-rede que deseja editar ou crie uma sub-rede adicional.
1. Atualize ou especifique uma gama de endereços IP suficientemente grande e, em seguida, **Guarde** as suas alterações.
1. [Criar um domínio gerido azure DS de substituição.](tutorial-create-instance.md) Certifique-se de que escolhe a subnet a rede virtual atualizada com uma gama de endereços IP suficientemente grande.

O Azure AD DS gerido a saúde do domínio atualiza-se automaticamente dentro de duas horas e remove o alerta.

## <a name="aadds113-resources-are-unrecoverable"></a>AADDS113: Os recursos são irrecuperáveis

### <a name="alert-message"></a>Mensagem de alerta

*Os recursos utilizados pelos Serviços de Domínio da AD Azure foram detetados num estado inesperado e não podem ser recuperados.*

### <a name="resolution"></a>Resolução

Este erro é irrecuperável. Para resolver o alerta, [elimine o domínio gerido pelo Azure AD DS existente](delete-aadds.md) e recrie-o. Se tiver dificuldade em apagar o domínio gerido pela AD DS Azure, abra um pedido de [apoio Azure][azure-support] para assistência adicional para resolução de problemas.

## <a name="aadds114-subnet-invalid"></a>AADDS114: Subnet inválido

### <a name="alert-message"></a>Mensagem de alerta

*A sub-rede selecionada para implantação dos Serviços de Domínio Azure AD é inválida e não pode ser utilizada.*

### <a name="resolution"></a>Resolução

Este erro é irrecuperável. Para resolver o alerta, [elimine o domínio gerido pelo Azure AD DS existente](delete-aadds.md) e recrie-o. Se tiver dificuldade em apagar o domínio gerido pela AD DS Azure, abra um pedido de [apoio Azure][azure-support] para assistência adicional para resolução de problemas.

## <a name="aadds115-resources-are-locked"></a>AADDS115: Os recursos estão bloqueados

### <a name="alert-message"></a>Mensagem de alerta

*Um ou mais dos recursos de rede utilizados pelo domínio gerido não podem ser utilizados, uma vez que o âmbito-alvo foi bloqueado.*

### <a name="resolution"></a>Resolução

Os bloqueios de recursos podem ser aplicados aos recursos do Azure para evitar alterações ou supressão. Como o Azure AD DS é um serviço gerido, a plataforma Azure precisa da capacidade de fazer alterações de configuração. Se for aplicado um bloqueio de recursos em alguns dos componentes Da DS Azure, a plataforma Azure não pode executar as suas tarefas de gestão.

Para verificar se há bloqueios de recursos nos componentes AD DS do Azure e removê-los, complete os seguintes passos:

1. Para cada um dos componentes da rede Azure AD DS no seu grupo de recursos, tais como rede virtual, interface de rede ou endereço IP público, verifique os registos de operação no portal Azure. Estes registos de funcionamento devem indicar por que razão uma operação está a falhar e onde é aplicado um bloqueio de recursos.
1. Selecione o recurso onde é aplicado um bloqueio, em seguida, sob **fechaduras,** selecione e retire o ou os bloqueios.

## <a name="aadds116-resources-are-unusable"></a>AADDS116: Os recursos são inutilizáveis

### <a name="alert-message"></a>Mensagem de alerta

*Um ou mais dos recursos de rede utilizados pelo domínio gerido não podem ser utilizados devido a restrições de políticas.*

### <a name="resolution"></a>Resolução

As políticas são aplicadas aos recursos e grupos de recursos do Azure que controlam que ações de configuração são permitidas. Como o Azure AD DS é um serviço gerido, a plataforma Azure precisa da capacidade de fazer alterações de configuração. Se for aplicada uma política em alguns dos componentes da AD DS Azure, a plataforma Azure poderá não ser capaz de executar as suas tarefas de gestão.

Para verificar se existem políticas aplicadas nos componentes Da DS Azure e atualizá-los, complete os seguintes passos:

1. Para cada um dos componentes da rede Azure AD DS no seu grupo de recursos, tais como rede virtual, NIC ou endereço IP público, verifique os registos de operação no portal Azure. Estes registos de funcionamento devem indicar por que razão uma operação está a falhar e onde é aplicada uma política restritiva.
1. Selecione o recurso onde uma política é aplicada, em seguida, em **Políticas,** selecione e edite a política para que seja menos restritiva.

## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500: A sincronização não está concluída há algum tempo

### <a name="alert-message"></a>Mensagem de alerta

*O domínio gerido foi sincronizado pela última vez com a AD Azure em [data]. Os utilizadores podem não conseguir iniciar sessão no domínio gerido ou as adesões do grupo podem não estar em sintonia com a AD Azure.*

### <a name="resolution"></a>Resolução

[Verifique se a saúde Azure AD DS](check-health.md) está a ver se há alertas que indiquem problemas na configuração do domínio gerido. Problemas com a configuração da rede podem bloquear a sincronização a partir de Azure AD. Se conseguir resolver alertas que indiquem um problema de configuração, aguarde duas horas e volte a verificar se a sincronização foi concluída com sucesso.

As seguintes razões comuns fazem com que a sincronização pare num DS DS azure:

* A conectividade de rede necessária está bloqueada. Para saber mais sobre como verificar a rede virtual Azure para obter problemas e o que é necessário, consulte [os grupos](alert-nsg.md) de segurança da rede de resolução de problemas e os requisitos de rede para os Serviços de [Domínio Azure AD](network-considerations.md).
*  A sincronização da palavra-passe não foi criada ou concluída com sucesso quando o domínio gerido pelo Azure AD DS foi implementado. Pode configurar a sincronização de [palavras-passe](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) para utilizadores apenas na nuvem ou utilizadores híbridos a [partir de on-prem](tutorial-configure-password-hash-sync.md).

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501: Um backup não foi tomado há algum tempo

### <a name="alert-message"></a>Mensagem de alerta

*O domínio gerido foi apoiado pela última vez em [data].*

### <a name="resolution"></a>Resolução

Verifique se há alertas de ds da [AD Azure](check-health.md) que indiquem problemas na configuração do domínio gerido. Problemas com a configuração da rede podem impedir a plataforma Azure de receber backups com sucesso. Se conseguir resolver alertas que indiquem um problema de configuração, aguarde duas horas e volte a verificar se a sincronização foi concluída com sucesso.

## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503: Suspensão devido a subscrição desativada

### <a name="alert-message"></a>Mensagem de alerta

*O domínio gerido é suspenso porque a subscrição Azure associada ao domínio não está ativa.*

### <a name="resolution"></a>Resolução

> [!WARNING]
> Se um domínio gerido pelo Azure AD DS for suspenso por um longo período de tempo, existe o perigo de ser eliminado. Resolva a razão da suspensão o mais rápido possível. Para mais informações, consulte [Compreender os estados suspensos para a AD DS Azure.](suspension.md)

O Azure AD DS requer uma subscrição ativa. Se a subscrição Azure com a qual o domínio gerido pelo Azure AD DS estava associado não estiver ativa, tem de o renovar para reativar a subscrição.

1. [Renove a sua subscrição Azure.](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable)
2. Uma vez renovada a subscrição, uma notificação Azure AD DS permite-lhe reativar o domínio gerido.

Quando o domínio gerido é ativado novamente, o Azure AD DS gerido a saúde do domínio atualiza-se automaticamente dentro de duas horas e remove o alerta.

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504: Suspensão devido a uma configuração inválida

### <a name="alert-message"></a>Mensagem de alerta

*O domínio gerido é suspenso devido a uma configuração inválida. O serviço não consegue gerir, corrigir ou atualizar os controladores de domínio para o seu domínio gerido durante muito tempo.*

### <a name="resolution"></a>Resolução

> [!WARNING]
> Se um domínio gerido pelo Azure AD DS for suspenso por um longo período de tempo, existe o perigo de ser eliminado. Resolva a razão da suspensão o mais rápido possível. Para mais informações, consulte [Compreender os estados suspensos para a AD DS Azure.](suspension.md)

Verifique se há alertas de ds da [AD Azure](check-health.md) que indiquem problemas na configuração do domínio gerido. Se conseguir resolver alertas que indiquem um problema de configuração, aguarde duas horas e volte a verificar se a sincronização está concluída. Quando estiver pronto, abra um pedido de [apoio Azure][azure-support] para reativar o domínio gerido pelo Azure AD DS.

## <a name="next-steps"></a>Passos seguintes

Se ainda tiver problemas, abra um pedido de [apoio azure][azure-support] para assistência adicional para resolução de problemas.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
