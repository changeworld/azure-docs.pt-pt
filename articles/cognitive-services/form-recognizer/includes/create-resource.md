---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: 33624ab800bd1155b52efbc05f317122a99bb479
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78205831"
---
Vá ao portal Azure <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title="e crie" target="_blank">um novo recurso do <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>Reconhecimento de Formulários criar um novo recurso de Reconhecimento de Formulários. No painel **Criar,** forneça as seguintes informações:

|    |    |
|--|--|
| **Nome** | Um nome descritivo para o seu recurso. Recomendamos a utilização de um nome descritivo, por exemplo *MyNameFormRecogniser*. |
| **Assinatura** | Selecione a subscrição Azure que foi concedida acesso. |
| **Localização** | A localização da sua instância de serviço cognitivo. Diferentes localizações podem introduzir latência, mas não têm impacto na disponibilidade de tempo de execução do seu recurso. |
| **Nível de preços** | O custo do seu recurso depende do nível de preços que escolher e da sua utilização. Para mais informações, consulte os detalhes dos [preços](https://azure.microsoft.com/pricing/details/cognitive-services/)da API .
| **Grupo de recursos** | O [grupo de recursos Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group) que irá conter o seu recurso. Pode criar um novo grupo ou adicioná-lo a um grupo pré-existente. |

> [!IMPORTANT]
> Normalmente, quando se cria um recurso do Serviço Cognitivo no portal Azure, tem a opção de criar uma chave de subscrição multi-serviço (usada em vários serviços cognitivos) ou uma chave de subscrição de um serviço único (usada apenas com um serviço cognitivo específico). No entanto, como o Form Recogniser é um lançamento de pré-visualização, não está incluído na subscrição de vários serviços, e não pode criar a subscrição de serviço único a menos que utilize o link fornecido no e-mail Welcome.

Quando o seu recurso 'Reconhecimento de Formulários' terminar a implantação, encontre-o e selecione-o a partir da lista **de todos os recursos** no portal. Em seguida, selecione o separador **de arranque Rápido** para visualizar os seus dados de subscrição. Guarde os valores de **Key1** e **Endpoint** para uma localização temporária. Vaiusá-los nos seguintes passos.
