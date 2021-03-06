---
title: Gerir a expiração do armazenamento da Blob Azure
titleSuffix: Azure Content Delivery Network
description: Conheça as opções para controlar o tempo-a-viver para as bolhas em cdn Azure.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: ad4801e9-d09a-49bf-b35c-efdc4e6034e8
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 02/1/2018
ms.author: mazha
ms.openlocfilehash: f28282a802e4b38fadc05c7090fa2a2af154de54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083165"
---
# <a name="manage-expiration-of-azure-blob-storage-in-azure-cdn"></a>Gerir a expiração do armazenamento azure blob em Azure CDN
> [!div class="op_single_selector"]
> * [Conteúdo Web do Azure](cdn-manage-expiration-of-cloud-service-content.md)
> * [Armazenamento Azure Blob](cdn-manage-expiration-of-blob-content.md)
> 
> 

O serviço de [armazenamento Blob](../storage/common/storage-introduction.md#blob-storage) em Armazenamento Azure é uma das várias origens baseadas no Azure integradas na Rede de Entrega de Conteúdos Azure (CDN). Qualquer conteúdo de blob acessível ao público pode ser cacheem em Azure CDN até que o seu tempo de vida (TTL) decorrido. O TTL é `Cache-Control` determinado pelo cabeçalho na resposta HTTP do servidor de origem. Este artigo descreve várias formas de configurar o `Cache-Control` cabeçalho numa bolha no Armazenamento Azure.

Também pode controlar as definições de cache do portal Azure, definindo regras de cache CDN. Se criar uma regra de cache e definir o seu comportamento de cache de **sobreposição** ou **bypass,** as definições de cache fornecidas pela origem discutidas neste artigo são ignoradas. Para obter informações sobre conceitos gerais de cache, veja como funciona o [cache.](cdn-how-caching-works.md)

> [!TIP]
> Pode optar por definir nenhuma TTL numa bolha. Neste caso, o Azure CDN aplica automaticamente um TTL padrão de sete dias, a menos que tenha estabelecido regras de cache no portal Azure. Este TTL padrão aplica-se apenas às otimizações gerais de entrega web. Para grandes otimizações de ficheiros, o TTL padrão é um dia, e para otimizações de streaming de mídia, o TTL padrão é de um ano.
> 
> Para obter mais informações sobre como o Azure CDN trabalha para acelerar o acesso a blobs e outros ficheiros, consulte a [Visão Geral da Rede de Entrega de Conteúdos Azure](cdn-overview.md).
> 
> Para mais informações sobre o armazenamento de Blob Azure, consulte [Introdução ao armazenamento blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).
 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>Definição de cabeçalhos de controlo de cache utilizando regras de cache cdn
O método preferido para definir `Cache-Control` o cabeçalho de uma bolha é usar regras de cache no portal Azure. Para obter mais informações sobre as regras de cache da CDN, consulte [controlazur e cDN comportamento de cache com regras](cdn-caching-rules.md)de cache .

> [!NOTE] 
> As regras de cache estão disponíveis apenas para **o Azure CDN Standard da Verizon** e **Azure CDN Standard a partir dos** perfis da Akamai. Para **o Azure CDN Premium a partir de** perfis Verizon, deve utilizar o motor de regras [Azure CDN](cdn-rules-engine.md) no portal **Manage** para funcionalidades semelhantes.

**Para navegar para a página de regras de cache cdN:**

1. No portal Azure, selecione um perfil CDN e, em seguida, selecione o ponto final para a bolha.

2. No painel esquerdo, em Definições, selecione **Regras de colocação em cache**.

   ![Botão de regras de cache CDN](./media/cdn-manage-expiration-of-blob-content/cdn-caching-rules-btn.png)

   É apresentada a página **Regras de colocação em cache**.

   ![Página de cache CDN](./media/cdn-manage-expiration-of-blob-content/cdn-caching-page.png)


**Para definir os cabeçalhos de Cache-Control de um serviço de armazenamento Blob utilizando regras globais de cache:**

1. De acordo com **as regras globais**de cache, deset o comportamento de **cache de cordas de consulta** para ignorar cordas de **consulta** e definir o comportamento **de Caching** para **sobrepor**.
      
2. Para a duração de validade do **Cache,** introduza 3600 na caixa **Seconds** ou 1 na caixa **Horas.** 

   ![CDN regras globais de cache exemplo](./media/cdn-manage-expiration-of-blob-content/cdn-global-caching-rules-example.png)

   Esta regra global de cache define uma duração de cache de uma hora e afeta todos os pedidos até ao ponto final. Substitui quaisquer `Cache-Control` `Expires` cabeçalhos http ou HTTP que são enviados pelo servidor de origem especificado pelo ponto final.   

3. Selecione **Guardar**.
 
**Para definir os cabeçalhos de Cache-Control de um ficheiro blob utilizando regras personalizadas de cacheching:**

1. De acordo com **as regras personalizadas de cache,** crie duas condições de jogo:

     R. Para a primeira condição de jogo, `/blobcontainer1/*` deset condição de **jogo** para **caminho** e introduza para o valor do **jogo**. Desloque o **comportamento de Caching** para **sobrepor-se** e introduza 4 na caixa **horas.**

    B. Para a segunda condição de jogo, `/blobcontainer1/blob1.txt` descoloque a condição de **jogo** para o **Caminho** e introduza para o valor do **jogo**. Desloque o comportamento de **Caching** para **sobrepor-se** e introduza 2 na caixa **horas.**

    ![CDN regras de cache personalizado exemplo](./media/cdn-manage-expiration-of-blob-content/cdn-custom-caching-rules-example.png)

    A primeira regra de cache personalizada define uma duração de `/blobcontainer1` cache de quatro horas para quaisquer ficheiros blob na pasta no servidor de origem especificado pelo seu ponto final. A segunda regra substitui a `blob1.txt` primeira regra apenas para o ficheiro blob e define uma duração de cache de duas horas para o mesmo.

2. Selecione **Guardar**.


## <a name="setting-cache-control-headers-by-using-azure-powershell"></a>Definição de cabeçalhos de controlo de cache utilizando o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[A Azure PowerShell](/powershell/azure/overview) é uma das formas mais rápidas e poderosas de administrar os seus serviços Azure. Utilize `Get-AzStorageBlob` o cmdlet para obter uma referência à `.ICloudBlob.Properties.CacheControl` bolha e, em seguida, desloque a propriedade. 

Por exemplo:

```powershell
# Create a storage context
$context = New-AzStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

> [!TIP]
> Também pode utilizar o PowerShell para [gerir os seus perfis e pontos finais da CDN](cdn-manage-powershell.md).
> 
>

## <a name="setting-cache-control-headers-by-using-net"></a>Definição de cabeçalhos de controlo de cache utilizando .NET
Para especificar o cabeçalho de `Cache-Control` uma bolha utilizando o código .NET, utilize a Biblioteca do Cliente de Armazenamento [Azure para .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) para definir a propriedade [CloudBlob.Properties.CacheControl.](/dotnet/api/microsoft.azure.storage.blob.blobproperties.cachecontrol)

Por exemplo:

```csharp
class Program
{
    const string connectionString = "<storage connection string>";
    static void Main()
    {
        // Retrieve storage account information from connection string
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);

        // Create a blob client for interacting with the blob service.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Create a reference to the container
        CloudBlobContainer <container name> = blobClient.GetContainerReference("<container name>");

        // Create a reference to the blob
        CloudBlob <blob name> = container.GetBlobReference("<blob name>");

        // Set the CacheControl property to expire in 1 hour (3600 seconds)
        blob.Properties.CacheControl = "max-age=3600";

        // Update the blob's properties in the cloud
        blob.SetProperties();
    }
}
```

> [!TIP]
> Existem mais amostras de código .NET disponíveis em Amostras de [Armazenamento De Blob Azure para .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/).
> 

## <a name="setting-cache-control-headers-by-using-other-methods"></a>Definição de cabeçalhos de controlo de cache utilizando outros métodos

### <a name="azure-storage-explorer"></a>Explorador do Storage do Azure
Com o [Azure Storage Explorer,](https://azure.microsoft.com/features/storage-explorer/)pode ver e editar os seus recursos de armazenamento blob, incluindo propriedades como a propriedade *CacheControl.* 

Para atualizar a propriedade *CacheControl* de uma bolha com o Azure Storage Explorer:
   1. Selecione uma bolha e, em seguida, selecione **Propriedades** do menu de contexto. 
   2. Desça até a propriedade *CacheControl.*
   3. Introduza um valor e, em seguida, selecione **Guardar**.


![Propriedades do Explorador de Armazenamento Azure](./media/cdn-manage-expiration-of-blob-content/cdn-storage-explorer-properties.png)

### <a name="azure-command-line-interface"></a>Interface de Linha de Comandos do Azure
Com a Interface de [Linha de Comando Azure](https://docs.microsoft.com/cli/azure) (CLI), pode gerir os recursos de blob Azure a partir da linha de comando. Para definir o cabeçalho de controlo de cache quando carregar uma bolha com o `-p` ClI Azure, detete a propriedade *cacheControl* utilizando o interruptor. O exemplo que se segue mostra como definir o TTL para uma hora (3600 segundos):
  
```azurecli
azure storage blob upload -c <connectionstring> -p cacheControl="max-age=3600" .\<blob name> <container name> <blob name>
```

### <a name="azure-storage-services-rest-api"></a>Serviços de armazenamento azure REST API
Pode utilizar os serviços de [armazenamento Azure REST API](/rest/api/storageservices/) para definir explicitamente a propriedade *x-ms-blob-cache-control* utilizando as seguintes operações a pedido:
  
   - [Coloque Blob](/rest/api/storageservices/Put-Blob)
   - [Lista de blocos](/rest/api/storageservices/Put-Block-List)
   - [Definir propriedades blob](/rest/api/storageservices/Set-Blob-Properties)

## <a name="testing-the-cache-control-header"></a>Testar o cabeçalho cache-control
Pode verificar facilmente as definições de TTL das suas bolhas. Com [as ferramentas](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)de desenvolvimento do seu `Cache-Control` navegador, teste que a sua bolha inclui o cabeçalho de resposta. Também pode utilizar uma ferramenta como [Wget,](https://www.gnu.org/software/wget/) [Carteiro](https://www.getpostman.com/)ou [Violinista](https://www.telerik.com/fiddler) para examinar os cabeçalhos de resposta.

## <a name="next-steps"></a>Passos Seguintes
* [Saiba como gerir a expiração dos conteúdos do Cloud Service no Azure CDN](cdn-manage-expiration-of-cloud-service-content.md)
* [Saiba sobre conceitos de cache](cdn-how-caching-works.md)

