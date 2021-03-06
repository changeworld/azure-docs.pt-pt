---
title: Como utilizar o armazenamento de objetos (Blob) do iOS - Azure [ Microsoft Docs
description: Armazene dados não estruturados na nuvem com o Blob Storage do Azure (armazenamento de objetos).
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/20/2018
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 54085d602246d38adb970ed02f451241ca7ba19d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68726408"
---
# <a name="how-to-use-blob-storage-from-ios"></a>Como utilizar o armazenamento Blob do iOS

Este artigo mostra como executar cenários comuns usando o armazenamento do Microsoft Azure Blob. As amostras estão escritas no Objectivo-C e utilizam a Biblioteca do Cliente de [Armazenamento Azure para iOS.](https://github.com/Azure/azure-storage-ios) Os cenários abrangidos incluem upload, listagem, descarregamento e exclusão de bolhas. Para mais informações sobre as bolhas, consulte a secção [Next Steps.](#next-steps) Também pode descarregar a [aplicação de amostras](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample) para ver rapidamente a utilização do Armazenamento Azure numa aplicação iOS.

Para saber mais sobre o armazenamento blob, consulte [Introdução ao armazenamento De Blob Azure](storage-blobs-introduction.md).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="import-the-azure-storage-ios-library-into-your-application"></a>Importe a biblioteca Azure Storage iOS na sua aplicação

Pode importar a biblioteca Azure Storage iOS para a sua aplicação, quer utilizando o [CocoaPod de Armazenamento Azure,](https://cocoapods.org/pods/AZSClient) quer importando o ficheiro **Framework.** O CocoaPod é a forma recomendada, uma vez que facilita a integração da biblioteca, no entanto importar a partir do ficheiro-quadro é menos intrusivo para o seu projeto existente.

Para utilizar esta biblioteca, precisa do seguinte:

- iOS 8+
- Xcode 7+

## <a name="cocoapod"></a>CocoaPod

1. Se ainda não o fez, [instale CocoaPods](https://guides.cocoapods.org/using/getting-started.html#toc_3) no seu computador abrindo uma janela de terminais e executando o seguinte comando

    ```shell
    sudo gem install cocoapods
    ```

2. Em seguida, no diretório do projeto (o diretório que contém o seu ficheiro .xcodeproj), crie um novo ficheiro chamado _Podfile_(sem extensão de ficheiro). Adicione o seguinte ao _Podfile_ e poupe.

    ```ruby
    platform :ios, '8.0'

    target 'TargetName' do
      pod 'AZSClient'
    end
    ```

3. Na janela do terminal, navegue para o diretório do projeto e executar o seguinte comando

    ```shell
    pod install
    ```

4. Se o seu .xcodeproj estiver aberto em Xcode, feche-o. No seu diretório de projeto abrir o arquivo de projeto recém-criado que terá a extensão .xcworkspace. Este é o ficheiro que vai trabalhar por agora.

## <a name="framework"></a>Arquitetura

A outra forma de utilizar a biblioteca é construir a estrutura manualmente:

1. Primeiro, descarregue ou clone o [azure-storage-ios repo](https://github.com/azure/azure-storage-ios).
2. Entre em *azure-storage-ios* -> *Lib* -> *Azure Storage Client Library*, e abra `AZSClient.xcodeproj` em Xcode.
3. No topo esquerdo do Xcode, mude o esquema ativo de "Biblioteca de Clientes de Armazenamento Azure" para "Framework".
4. Construa o projeto (+B). Isto criará `AZSClient.framework` um ficheiro no seu Ambiente de Trabalho.

Em seguida, pode importar o ficheiro-quadro para o seu pedido fazendo o seguinte:

1. Crie um novo projeto ou abra o seu projeto existente em Xcode.
2. Arraste e `AZSClient.framework` deixe o seu navegador do projeto Xcode.
3. Selecione *itens de cópia se necessário,* e clique em *Terminar*.
4. Clique no seu projeto na navegação à esquerda e clique no separador *Geral* no topo do editor do projeto.
5. Na secção *Quadros e Bibliotecas Ligados,* clique no botão Adicionar (+).
6. Na lista de bibliotecas já `libxml2.2.tbd` fornecidas, procure e adicione ao seu projeto.

## <a name="import-the-library"></a>Importar a Biblioteca

```objc
// Include the following import statement to use blob APIs.
#import <AZSClient/AZSClient.h>
```

Se estiver a usar swift, terá de criar \<um cabeçalho de ponte e importar a AZSClient/AZSClient.h> lá:

1. Crie um `Bridging-Header.h`ficheiro de cabeçalho e adicione a declaração de importação acima.
2. Vá ao separador *'Definições de Construção'* e procure o cabeçalho de *ponte Objective-C*.
3. Clique duas vezes no campo do Cabeçalho de *Ponte Objective-C* e adicione o caminho ao seu ficheiro de cabeçalho:`ProjectName/Bridging-Header.h`
4. Construa o projeto (+B) para verificar se o cabeçalho de ponte foi captado pela Xcode.
5. Comece a usar a biblioteca diretamente em qualquer ficheiro Swift, não há necessidade de declarações de importação.

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="asynchronous-operations"></a>Operações Assíncronas

> [!NOTE]
> Todos os métodos que executam um pedido contra o serviço são operações assíncronas. Nas amostras de código, verá que estes métodos têm um manipulador de conclusão. O código dentro do manipulador de conclusão será executado **após** a conclusão do pedido. O código após a conclusão será executado **enquanto** o pedido está a ser feito.

## <a name="create-a-container"></a>Criar um contentor

Todas as bolhas em Azure Storage devem residir num recipiente. O exemplo que se segue mostra como criar um contentor, chamado *newcontainer,* na sua conta de Armazenamento, se já não existir. Ao escolher um nome para o seu recipiente, esteja atento às regras de nomeação acima mencionadas.

```objc
-(void)createContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"newcontainer"];

    // Create container in your Storage account if the container doesn't already exist
    [blobContainer createContainerIfNotExistsWithCompletionHandler:^(NSError *error, BOOL exists) {
        if (error){
            NSLog(@"Error in creating container.");
        }
    }];
}
```

Pode confirmar que isto funciona olhando para o [Microsoft Azure Storage Explorer](https://storageexplorer.com) e verificando que o novo *contentor* está na lista de contentores para a sua conta de Armazenamento.

## <a name="set-container-permissions"></a>Definir permissões de contentores

As permissões de um contentor estão configuradas para acesso **privado** por padrão. No entanto, os contentores fornecem algumas opções diferentes para o acesso dos contentores:

- **Privado**: Os dados do contentor e da bolha só podem ser lidos pelo proprietário da conta.
- **Blob**: Os dados blob dentro deste recipiente podem ser lidos através de pedido anónimo, mas os dados do contentor não estão disponíveis. Os clientes não podem enumerar bolhas dentro do contentor através de pedido anónimo.
- **Recipiente**: Os dados do recipiente e da bolha podem ser lidos através de pedido anónimo. Os clientes podem enumerar bolhas dentro do contentor através de pedido anónimo, mas não podem enumerar os contentores dentro da conta de armazenamento.

O exemplo que se segue mostra como criar um contentor com permissões de acesso a **contentores,** o que permitirá o acesso público e apenas de leitura a todos os utilizadores na Internet:

```objc
-(void)createContainerWithPublicAccess{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create container in your Storage account if the container doesn't already exist
    [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists){
        if (error){
            NSLog(@"Error in creating container.");
        }
    }];
}
```

## <a name="upload-a-blob-into-a-container"></a>Carregar um blob para um contentor

Como mencionado na secção de conceitos de serviço Blob, o Blob Storage oferece três tipos diferentes de bolhas: blocos de bolhas, bolhas de apêndice e bolhas de página. A biblioteca Azure Storage iOS suporta os três tipos de bolhas. Na maioria dos casos, o blob de blocos é o tipo recomendado a utilizar.

O exemplo que se segue mostra como carregar uma bolha de bloco de um NSString. Se já existir uma bolha com o mesmo nome neste recipiente, o conteúdo desta bolha será substituído.

```objc
-(void)uploadBlobToContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists)
        {
            if (error){
                NSLog(@"Error in creating container.");
            }
            else{
                // Create a local blob object
                AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

                // Upload blob to Storage
                [blockBlob uploadFromText:@"This text will be uploaded to Blob Storage." completionHandler:^(NSError *error) {
                    if (error){
                        NSLog(@"Error in creating blob.");
                    }
                }];
            }
        }];
}
```

Pode confirmar que isto funciona olhando para o [Microsoft Azure Storage Explorer](https://storageexplorer.com) e verificando se o recipiente, *contentorpúblico,* contém a bolha, *sampleblob*. Nesta amostra, usamos um recipiente público para que também possa verificar se esta aplicação funcionou indo para as bolhas URI:

```http
https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob
```

Além de carregar uma bolha de bloco de um NSString, existem métodos semelhantes para NSData, NSInputStream ou um ficheiro local.

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor

O exemplo que se segue mostra como listar todas as bolhas num recipiente. Ao efetuar esta operação, esteja atento aos seguintes parâmetros:

- **continuaçãoToken** - O token de continuação representa onde a operação de cotação deve começar. Se não for fornecido nenhum sinal, irá listar bolhas desde o início. Qualquer número de bolhas pode ser listada, de zero a um máximo definido. Mesmo que este método retorne resultados nulos, se `results.continuationToken` não for nulo, pode haver mais bolhas no serviço que não foram listados.
- **prefixo** - Pode especificar o prefixo a utilizar para a listagem de bolhas. Apenas bolhas que começam com este prefixo serão listadas.
- **useFlatBlobListing** - Como mencionado na secção [de nomeação e referenciação de contentores e bolhas,](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) embora o serviço Blob seja um esquema de armazenamento plano, você pode criar uma hierarquia virtual nomeando bolhas com informações de caminho. No entanto, a listagem não plana não é atualmente suportada. Esta funcionalidade está para breve. Por enquanto, este valor deve ser **SIM.**
- **blobListingDetails** - Pode especificar quais itens a incluir na listagem de blobs
  - _AZSBlobListingDetailsNone_: Lista apenas bolhas comprometidas e não devolve metadados blob.
  - _AZSBlobListingDetailsSnapshots_: Lista de bolhas comprometidas e fotos blob.
  - _AZSBlobListingDetailsMetadata_: Recuperar metadados blob para cada bolha devolvida na listagem.
  - _AZSBlobListingDetailsUncommittedBlobs_: Lista de blobs comprometidos e não comprometidos.
  - _AZSBlobListingDetailsCopy_: Incluir propriedades de cópia na listagem.
  - _AZSBlobListingDetailsAll_: Lista todas as bolhas comprometidas disponíveis, bolhas não comprometidas e instantâneos, e devolve todos os metadados e o estado da cópia para essas bolhas.
- **maxResults** - O número máximo de resultados a devolver para esta operação. Utilize -1 para não estabelecer um limite.
- **conclusãoHandler** - O bloco de código a executar com os resultados da operação de cotação.

Neste exemplo, um método de ajudante é usado para chamar recursivamente o método de blobs da lista cada vez que um token de continuação é devolvido.

```objc
-(void)listBlobsInContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    //List all blobs in container
    [self listBlobsInContainerHelper:blobContainer continuationToken:nil prefix:nil blobListingDetails:AZSBlobListingDetailsAll maxResults:-1 completionHandler:^(NSError *error) {
        if (error != nil){
            NSLog(@"Error in creating container.");
        }
    }];
}

//List blobs helper method
-(void)listBlobsInContainerHelper:(AZSCloudBlobContainer *)container continuationToken:(AZSContinuationToken *)continuationToken prefix:(NSString *)prefix blobListingDetails:(AZSBlobListingDetails)blobListingDetails maxResults:(NSUInteger)maxResults completionHandler:(void (^)(NSError *))completionHandler
{
    [container listBlobsSegmentedWithContinuationToken:continuationToken prefix:prefix useFlatBlobListing:YES blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:^(NSError *error, AZSBlobResultSegment *results) {
        if (error)
        {
            completionHandler(error);
        }
        else
        {
            for (int i = 0; i < results.blobs.count; i++) {
                NSLog(@"%@",[(AZSCloudBlockBlob *)results.blobs[i] blobName]);
            }
            if (results.continuationToken)
            {
                [self listBlobsInContainerHelper:container continuationToken:results.continuationToken prefix:prefix blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:completionHandler];
            }
            else
            {
                completionHandler(nil);
            }
        }
    }];
}
```

## <a name="download-a-blob"></a>Transferir um blob

O exemplo que se segue mostra como descarregar uma bolha para um objeto NSString.

```objc
-(void)downloadBlobToString{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create a local blob object
    AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

    // Download blob
    [blockBlob downloadToTextWithCompletionHandler:^(NSError *error, NSString *text) {
        if (error) {
            NSLog(@"Error in downloading blob");
        }
        else{
            NSLog(@"%@",text);
        }
    }];
}
```

## <a name="delete-a-blob"></a>Eliminar um blob

O exemplo que se segue mostra como apagar uma bolha.

```objc
-(void)deleteBlob{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create a local blob object
    AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob1"];

    // Delete blob
    [blockBlob deleteWithCompletionHandler:^(NSError *error) {
        if (error) {
            NSLog(@"Error in deleting blob.");
        }
    }];
}
```

## <a name="delete-a-blob-container"></a>Eliminar um recipiente de bolhas

O exemplo que se segue mostra como apagar um recipiente.

```objc
-(void)deleteContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Delete container
    [blobContainer deleteContainerIfExistsWithCompletionHandler:^(NSError *error, BOOL success) {
        if(error){
            NSLog(@"Error in deleting container");
        }
    }];
}
```

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a usar o Blob Storage do iOS, siga estes links para saber mais sobre a biblioteca iOS e o serviço de Armazenamento.

- [Biblioteca do Cliente de Armazenamento Azure para iOS](https://github.com/azure/azure-storage-ios)
- [Documentação de referência do IOS de Armazenamento Azure](https://azure.github.io/azure-storage-ios/)
- [API REST dos Serviços do Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Blog da equipe de armazenamento azure](https://blogs.msdn.com/b/windowsazurestorage)

Se tiver dúvidas sobre esta biblioteca, sinta-se à vontade para publicar no nosso [fórum MSDN Azure](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=windowsazuredata) ou [no Stack Overflow](https://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files).
Se tiver sugestões de recurso para o Armazenamento Azure, por favor poste no Feedback de [Armazenamento Azure](https://feedback.azure.com/forums/217298-storage/).
