---
title: 'PowerShell: Desdobre continuamente a partir do GitHub'
description: Aprenda a usar o Azure PowerShell para automatizar a implementação e gestão do App Service. Esta amostra mostra como criar uma aplicação com CI/CD do GitHub.
tags: azure-service-management
ms.assetid: 42f901f8-02f7-4869-b22d-d99ef59f874c
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc
ms.openlocfilehash: eee6ac9f9c469f9e1a9344ab4a30626c219d7836
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74685159"
---
# <a name="create-a-web-app-with-continuous-deployment-from-github"></a>Criar uma aplicação Web com implementação contínua a partir do GitHub

Este script de amostra cria uma aplicação web no App Service com os seus recursos relacionados, e depois configura a [implementação contínua](../deploy-continuous-deployment.md) a partir de um repositório GitHub. Para a implementação do GitHub sem implementação contínua, veja [Criar uma aplicação Web e implementar código a partir do GitHub](powershell-deploy-github.md).

Se for preciso, instale o Azure PowerShell com a instrução que se encontra no [Guia do Azure PowerShell](/powershell/azure/overview) e, em seguida, execute `Connect-AzAccount` para criar uma ligação ao Azure. Além disso, certifique-se de que:

- o código da aplicação está num repositório do GitHub público ou privado do qual é proprietário. Para obter construções automáticas, estruture o seu repositório de acordo com a mesa de [rescrito prepare.](../deploy-continuous-deployment.md#prepare-your-repository)
- Criou um sinal de [acesso pessoal na sua conta GitHub.](https://help.github.com/en/articles/creating-a-personal-access-token-for-the-command-line)

## <a name="sample-script"></a>Script de exemplo

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-github-continuous/deploy-github-continuous.ps1?highlight=1-2 "Create a web app with continuous deployment from GitHub")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Depois de executar o script de exemplo, o seguinte comando pode ser utilizado para remover o grupo de recursos, a aplicação Web e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [Novo AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Cria um plano do Serviço de Aplicações. |
| [Novo AzWebApp](/powershell/module/az.websites/new-azwebapp) | Cria uma aplicação Web. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Modifica um recurso num grupo de recursos. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode ver os exemplos do Azure Powershell adicionais para as Aplicações Web do Serviço de Aplicações do Azure em [Exemplos do Azure PowerShell](../samples-powershell.md).
