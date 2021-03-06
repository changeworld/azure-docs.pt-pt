---
title: O tamanho da pasta TEMP padrão é demasiado pequeno para um papel Microsoft Docs
description: Uma função de serviço na nuvem tem uma quantidade limitada de espaço para a pasta TEMP. Este artigo fornece algumas sugestões sobre como evitar ficar sem espaço.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 0b869b73a79872d9263058bedfead018e18721c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71154986"
---
# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-webworker-role"></a>O tamanho da pasta TEMP padrão é muito pequeno numa função web/trabalhador do serviço de nuvem
O diretório temporário padrão de um trabalhador de serviço na nuvem ou função web tem um tamanho máximo de 100 MB, que pode ficar cheio em algum momento. Este artigo descreve como evitar ficar sem espaço para o diretório temporário.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-do-i-run-out-of-space"></a>Por que fico sem espaço?
As variáveis padrão do ambiente Windows TEMP e TMP estão disponíveis para codificar que está em execução na sua aplicação. Tanto a TEMP como a TMP apontam para um único diretório com um tamanho máximo de 100 MB. Quaisquer dados armazenados neste diretório não são persistidos ao longo do ciclo de vida do serviço de nuvem; se as instâncias de papel num serviço de nuvem forem recicladas, o diretório é limpo.

## <a name="suggestion-to-fix-the-problem"></a>Sugestão para corrigir o problema
Implementar uma das seguintes alternativas:

* Configure um recurso de armazenamento local e aceda-o diretamente em vez de utilizar TEMP ou TMP. Para aceder a um recurso de armazenamento local a partir de código que está a ser gerido dentro da sua aplicação, ligue para o método [RoleEnvironment.GetLocalResource.](/previous-versions/azure/reference/ee772845(v=azure.100))
* Configure um recurso de armazenamento local e indique os diretórios TEMP e TMP para apontar o caminho do recurso de armazenamento local. Esta modificação deve ser realizada dentro do método [RoleEntryPoint.OnStart.](/previous-versions/azure/reference/ee772851(v=azure.100))

O seguinte exemplo de código mostra como modificar os directórios-alvo para TEMP e TMP a partir do método OnStart:

```csharp
using System;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override bool OnStart()
        {
            // The local resource declaration must have been added to the
            // service definition file for the role named WorkerRole1:
            //
            // <LocalResources>
            //    <LocalStorage name="CustomTempLocalStore"
            //                  cleanOnRoleRecycle="false"
            //                  sizeInMB="1024" />
            // </LocalResources>

            string customTempLocalResourcePath =
            RoleEnvironment.GetLocalResource("CustomTempLocalStore").RootPath;
            Environment.SetEnvironmentVariable("TMP", customTempLocalResourcePath);
            Environment.SetEnvironmentVariable("TEMP", customTempLocalResourcePath);

            // The rest of your startup code goes here…

            return base.OnStart();
        }
    }
}
```

## <a name="next-steps"></a>Passos seguintes
Leia um blog que descreve [como aumentar o tamanho da Função Web Azure ASP.NET pasta Temporária.](https://blogs.msdn.com/b/kwill/archive/2011/07/18/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder.aspx)

Veja mais artigos de [resolução de problemas](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/vs-azure-tools-debugging-cloud-services-overview.md) para serviços na nuvem.

Para aprender a resolver problemas de papel de serviço na nuvem utilizando dados de diagnóstico de computador Azure PaaS, veja a [série de blogs de Kevin Williamson.](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx)
