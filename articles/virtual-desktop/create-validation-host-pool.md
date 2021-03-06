---
title: Atualizações do serviço de piscina do Windows Virtual Desktop - Azure
description: Como criar um pool de anfitriões de validação para monitorizar as atualizações de serviço antes de lançar atualizações para a produção.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/13/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f2b51213dfc6d7e55f76e78b92d12111f84736be
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79365394"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>Tutorial: Criar um pool de anfitriões para validar atualizações de serviço

As piscinas hospedeiras são uma coleção de uma ou mais máquinas virtuais idênticas dentro dos ambientes de inquilinos do Windows Virtual Desktop. Antes de implementar piscinas hospedeiras para o seu ambiente de produção, recomendamos vivamente que crie um pool de anfitriões de validação. As atualizações são aplicadas primeiro às piscinas de anfitriões de validação, permitindo-lhe monitorizar as atualizações de serviço antes de as lançar para o seu ambiente de produção. Sem um pool de hospedagem de validação, não poderá descobrir alterações que introduzam erros, o que pode resultar em tempo de inatividade para os utilizadores no seu ambiente de produção.

Para garantir que as suas aplicações funcionam com as mais recentes atualizações, o pool anfitrião de validação deve ser o mais semelhante possível às piscinas hospedadas no seu ambiente de produção. Os utilizadores devem ligar-se com a mesma frequência à piscina anfitriã da validação como fazem com a piscina de anfitriões de produção. Se tiver testes automatizados na sua piscina de anfitriões, deverá incluir testes automatizados na piscina de anfitriões de validação.

Pode desinventar problemas no conjunto de anfitriões de validação com a funcionalidade de diagnóstico ou os [artigos](diagnostics-role-service.md) de resolução de [problemas do Windows Virtual Desktop.](troubleshoot-set-up-overview.md)

>[!NOTE]
> Recomendamos que deixe o conjunto de anfitriões de validação no lugar para testar todas as atualizações futuras.

Antes de começar, [faça o download e importe o módulo PowerShell do Windows Virtual Desktop](/powershell/windows-virtual-desktop/overview/), se ainda não o fez. Depois disso, execute o seguinte cmdlet para iniciar sessão na sua conta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-your-host-pool"></a>Crie a sua piscina de anfitrião

Pode criar uma piscina de anfitriões seguindo as instruções em qualquer um destes artigos:
- [Tutorial: Crie uma piscina de anfitriões com o Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Criar um conjunto de anfitriões com um modelo do Azure Resource Manager](create-host-pools-arm-template.md)
- [Criar um conjunto de anfitriões com o PowerShell](create-host-pools-powershell.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>Defina a sua piscina anfitriã como uma piscina de anfitriões de validação

Execute os seguintes cmdlets PowerShell para definir a nova piscina anfitriã como uma piscina anfitriã de validação. Substitua os valores em cotações pelos valores relevantes para a sua sessão:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Set-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

Execute o seguinte cmdlet PowerShell para confirmar que a propriedade de validação foi definida. Substitua os valores em cotações pelos valores relevantes para a sua sessão.

```powershell
Get-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool"
```

Os resultados do cmdlet devem ser semelhantes a esta saída:

```
    TenantName          : contoso 
    TenantGroupName     : Default Tenant Group
    HostPoolName        : contosoHostPool
    FriendlyName        :
    Description         :
    Persistent          : False 
    CustomRdpProperty    : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnv       : True
    Ring                :
```

## <a name="update-schedule"></a>Calendário de atualização

As atualizações de serviço acontecem mensalmente. Se houver grandes problemas, as atualizações críticas serão fornecidas a um ritmo mais frequente.

## <a name="next-steps"></a>Passos seguintes

Agora que criou um conjunto de anfitriões de validação, pode aprender a usar a Azure Service Health para monitorizar a implementação do Windows Virtual Desktop. 

> [!div class="nextstepaction"]
> [Configurar alertas de serviço](./set-up-service-alerts.md)
