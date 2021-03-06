---
title: Autorizar operações de dados
titleSuffix: Azure Storage
description: Saiba mais sobre as diferentes formas de autorizar o acesso ao Armazenamento Azure, incluindo o Diretório Ativo Azure, a autorização de chave partilhada ou as assinaturas de acesso partilhado (SAS).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 53eca8a0b9e7cc9abb8f89cd56fca5df28f2de0f
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521933"
---
# <a name="authorizing-access-to-data-in-azure-storage"></a>Autorizar o acesso a dados no Armazenamento Do Azure

Cada vez que acede a dados na sua conta de armazenamento, o seu cliente faz um pedido sobre HTTP/HTTPS para o Armazenamento Azure. Cada pedido a um recurso seguro tem de ser autorizado, para que o serviço garanta que o cliente tem as permissões necessárias para aceder aos dados.

O quadro seguinte descreve as opções que o Azure Storage oferece para autorizar o acesso aos recursos:

|  |Chave Partilhada (chave da conta de armazenamento)  |Assinatura de acesso partilhado (SAS)  |Azure Active Directory (Azure AD)  |Diretório Ativo (pré-visualização) |Acesso anónimo ao público de leitura  |
|---------|---------|---------|---------|---------|---------|
|Blobs do Azure     |[Apoiado](/rest/api/storageservices/authorize-with-shared-key/)         |[Apoiado](storage-sas-overview.md)         |[Apoiado](storage-auth-aad.md)         |Não suportado|[Apoiado](../blobs/storage-manage-access-to-resources.md)         |
|Ficheiros Azure (SMB)     |[Apoiado](/rest/api/storageservices/authorize-with-shared-key/)         |Não suportado         |[Suportado, apenas com Serviços de Domínio AAD](../files/storage-files-active-directory-overview.md)         |[Suportadas, credenciais devem ser sincronizadas com a Azure AD](../files/storage-files-active-directory-overview.md)|Não suportado         |
|Ficheiros Azure (REST)     |[Apoiado](/rest/api/storageservices/authorize-with-shared-key/)         |[Apoiado](storage-sas-overview.md)         |Não suportado         |Não suportado |Não suportado         |
|Filas do Azure     |[Apoiado](/rest/api/storageservices/authorize-with-shared-key/)         |[Apoiado](storage-sas-overview.md)         |[Apoiado](storage-auth-aad.md)         |Não suportado | Não suportado         |
|Tabelas do Azure     |[Apoiado](/rest/api/storageservices/authorize-with-shared-key/)         |[Apoiado](storage-sas-overview.md)         |Não suportado         |Não suportado| Não suportado         |

Cada opção de autorização é brevemente descrita abaixo:

- **Integração do Azure Ative Directory (Azure AD)** para bolhas e filas. A Azure AD fornece um controlo de acesso baseado em funções (RBAC) para o controlo do acesso de um cliente aos recursos numa conta de armazenamento. Para obter mais informações sobre a integração da AD Azure para bolhas e filas, consulte [Autorizar o acesso a blobs e filas Azure utilizando o Diretório Ativo Azure.](storage-auth-aad.md)

- **Autenticação azure Ative Directory Domain Services (Azure AD DS)** para Ficheiros Azure. O Azure Files suporta a autorização baseada na identidade sobre o Bloco de Mensagens do Servidor (SMB) através do Azure AD DS. Pode utilizar o RBAC para um controlo fino sobre o acesso de um cliente aos recursos do Azure Files numa conta de armazenamento. Para mais informações sobre a autenticação dos Ficheiros Azure utilizando serviços de domínio, consulte a nossa [visão geral](../files/storage-files-active-directory-overview.md).

- Autenticação de **Diretório Ativo (AD) (pré-visualização)** para Ficheiros Azure. A Azure Files suporta a autorização baseada na identidade sobre a SMB através de AD. O seu serviço de domínio AD pode ser hospedado em máquinas no local ou em VMs Azure. O acesso sMB aos Ficheiros é suportado utilizando credenciais aD de máquinas unidas de domínio, quer no local quer no Azure. Pode utilizar o RBAC para controlo de acesso ao nível de partilha e DACLs NTFS para aplicação de permissão de nível de diretório/ficheiro. Para mais informações sobre a autenticação dos Ficheiros Azure utilizando serviços de domínio, consulte a nossa [visão geral](../files/storage-files-active-directory-overview.md).

- **Autorização chave partilhada** para bolhas, ficheiros, filas e mesas. Um cliente que usa a Chave Partilhada passa um cabeçalho com cada pedido assinado usando a chave de acesso à conta de armazenamento. Para mais informações, consulte [Autorizar com chave partilhada](/rest/api/storageservices/authorize-with-shared-key/).
- **Assinaturas** de acesso partilhadas para bolhas, ficheiros, filas e mesas. As assinaturas de acesso partilhado (SAS) fornecem acesso limitado aos recursos numa conta de armazenamento. A adição de restrições no intervalo de tempo para o qual a assinatura é válida ou em permissões que concede proporciona flexibilidade na gestão do acesso. Para mais informações, consulte [A utilização de assinaturas de acesso partilhado (SAS)](storage-sas-overview.md).
- **O público anónimo leu o acesso** a contentores e bolhas. A autorização não é necessária. Para obter mais informações, veja [Manage anonymous read access to containers and blobs](../blobs/storage-manage-access-to-resources.md) (Gerir o acesso de leitura anónima a contentores e blobs).  

Por predefinição, todos os recursos em Armazenamento Azure estão seguros, e estão disponíveis apenas para o proprietário da conta. Embora possa utilizar qualquer uma das estratégias de autorização acima descritas para garantir aos clientes o acesso aos recursos na sua conta de armazenamento, a Microsoft recomenda a utilização de AD Azure sempre que possível para máxima segurança e facilidade de utilização.

## <a name="next-steps"></a>Passos seguintes

- [Autorizar acesso a blobs e filas Azure utilizando o Diretório Ativo Azure](storage-auth-aad.md)
- [Autorizar com a Chave Partilhada](/rest/api/storageservices/authorize-with-shared-key/)
- [Conceder acesso limitado aos recursos de Armazenamento Azure utilizando assinaturas de acesso partilhado (SAS)](storage-sas-overview.md)
