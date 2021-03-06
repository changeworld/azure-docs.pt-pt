---
title: Quickstart - Crie uma bolha com o portal Azure
titleSuffix: Azure Storage
description: Neste início rápido, irá utilizar o portal do Azure no armazenamento de objetos (Blobs). Em seguida, utilize o portal do Azure para carregar um blob para o Armazenamento do Microsoft Azure, transferir um blob e listar os blobs num contentor.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: 3ceeb878121782ea3664e1ed62d5d632b606f08e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "80061383"
---
# <a name="quickstart-upload-download-and-list-blobs-with-the-azure-portal"></a>Início Rápido: carregar, transferir e listar blobs com o portal do Azure

Neste guia de introdução, irá aprender a utilizar o [portal do Azure](https://portal.azure.com/) para criar um contentor no Armazenamento do Azure e para carregar e transferir blobs de blocos nesse contentor.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

## <a name="create-a-container"></a>Criar um contentor

Para criar um contentor no portal do Azure, siga estes passos:

1. No portal do Azure, navegue para a sua nova conta de armazenamento.
2. No menu esquerdo para a conta de armazenamento, desloque-se para a secção de **serviço Blob** e, em seguida, selecione **Recipientes**.
3. Selecione o botão **+ Contentor**.
4. Escreva um nome para o novo contentor. O nome do contentor tem estar em minúsculas, tem de começar com uma letra ou um número e só pode incluir letras, números e o caráter de travessão (-). Para obter mais informações sobre nomeações de contentores e blobs, consulte [Nomear e referenciar contentores, bolhas e metadados.](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
5. Defina o nível de acesso público ao contentor. O nível predefinido é **Privado (sem acesso anónimo)**.
6. Selecione **OK** para criar o contentor.

    ![A captura de ecrã mostra como criar um contentor no portal do Azure](media/storage-quickstart-blobs-portal/create-container.png)

## <a name="upload-a-block-blob"></a>Carregar um blob de blocos

Os blobs de blocos são compostos por blocos de dados reunidos para criar um blob. A maioria dos cenários com o armazenamento de Blobs emprega blobs de blocos. Os blobs de blocos são ideais para armazenar dados de texto e binários na cloud, como ficheiros, imagens e vídeos. Este guia de introdução mostra como trabalhar com blobs de blocos. 

Para carregar um blob de blocos para o novo contentor no portal do Azure, siga estes passos:

1. No portal do Azure, navegue para o contentor que criou na secção anterior.
2. Selecione o contentor para mostrar uma lista dos blobs que ele contém. Uma vez que este contentor é novo, ainda não contém blobs.
3. Selecione o botão **upload** para abrir a lâmina de upload
4. Navegue no seu sistema de ficheiros local para encontrar um ficheiro para carregar como uma bolha de bloco
     
    ![A captura de ecrã mostra como carregar um blob a partir do disco local](media/storage-quickstart-blobs-portal/upload-blob.png)

5. Opcionalmente, expanda a secção avançada para definir outras definições, como tipo de autenticação, nível de acesso ou caminho de pasta virtual.
6. Selecione o botão **Upload** para comprometer o upload
7. Carregue os blobs que quiser desta forma. Verá que os novos blobs estão agora listados no contentor.

## <a name="download-a-block-blob"></a>Transferir um blob de blocos

Pode transferir um blob de blocos para apresentar no browser ou guardar no seu sistema de ficheiros local. Para transferir um blob de blocos, siga estes passos:

1. Navegue para a lista de blobs que carregou na secção anterior. 
2. Clique com o botão direito do rato no blob que pretende transferir e selecione **Transferir**. 

## <a name="clean-up-resources"></a>Limpar recursos

Para remover os recursos que criou neste início rápido, pode eliminar o contentor. Todos os blobs no contentor também serão eliminados.

Para eliminar o contentor:

1. No portal do Azure, navegue para a lista de contentores na sua conta de armazenamento.
2. Selecione o contentor a eliminar.
3. Selecione o botão **Mais** (**... **) e selecione **Eliminar**.
4. Certifique-se de que pretende eliminar o contentor.

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, aprendeu a transferir ficheiros entre um disco local e o armazenamento de Blobs do Azure com o portal do Azure. Para saber mais sobre a utilização do armazenamento de Blobs, avance para os procedimentos do armazenamento de blobs.

> [!div class="nextstepaction"]
> [Procedimentos de Operações de Armazenamento de Blobs](storage-dotnet-how-to-use-blobs.md)

