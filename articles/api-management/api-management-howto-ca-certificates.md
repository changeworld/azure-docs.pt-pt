---
title: Adicione um certificado CA personalizado - Azure API Management [ Microsoft Docs
description: Saiba como adicionar um certificado DE CA personalizado na Gestão API Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/20/2018
ms.author: apimpm
ms.openlocfilehash: 21d5869f2bcdfb6383b6ef89869d8098135ea7ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70073610"
---
# <a name="how-to-add-a-custom-ca-certificate-in-azure-api-management"></a>Como adicionar um certificado DE CA personalizado na Gestão API Azure

A Azure API Management permite instalar certificados CA na máquina dentro das lojas de certificados de raiz e intermédio seletivas fidedignas. Esta funcionalidade deve ser utilizada se os seus serviços necessitarem de um certificado CA personalizado.

O artigo mostra como gerir certificados ca de uma instância de serviço de Gestão API Azure no portal Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upload-a-ca-certificate"></a><a name="step1"> </a>Faça upload de um certificado CA

![Adicionar certificados CA](media/api-management-howto-ca-certificates/00.png)

Siga os passos abaixo para fazer upload de um novo certificado CA. Se ainda não criou uma instância de serviço de Gestão API, consulte o tutorial Criar uma instância de serviço de [Gestão API](get-started-create-service-instance.md).

1. Navegue para a sua instância de serviço azure API Management no portal Azure.

2. Selecione **certificados CA** do menu.

3. Clique no botão **+ Adicionar**.  

    ![Adicionar certificados CA](media/api-management-howto-ca-certificates/01.png)  

4. Procure o certificado e decida sobre o certificado. Só é necessária a chave pública, pelo que a palavra-passe não é necessária.

    ![Adicionar certificados CA](media/api-management-howto-ca-certificates/02.png)  

5. Clique em **Guardar**. Esta operação poderá demorar alguns minutos.

    ![Adicionar certificados CA](media/api-management-howto-ca-certificates/03.png)  

> [!NOTE]
> Pode fazer o upload `New-AzApiManagementSystemCertificate` de um certificado CA utilizando o comando Powershell.

## <a name="delete-a-client-certificate"></a><a name="step1a"> </a>Eliminar um certificado de cliente

Para eliminar um certificado, clique no menu de **contexto...** e **selecione Excluir** ao lado do certificado.

![Eliminar certificados CA](media/api-management-howto-ca-certificates/04.png)  

[Upload a CA certificate]: #step1
[Delete a CA certificate]: #step1a
