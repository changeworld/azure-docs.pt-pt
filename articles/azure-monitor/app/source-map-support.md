---
title: Suporte de mapa de origem para aplicações JavaScript - Insights de aplicação do Monitor Azure
description: Saiba como enviar mapas de origem para a sua própria conta de armazenamento Blob contentor usando Application Insights.
ms.topic: conceptual
author: markwolff
ms.author: marwolff
ms.date: 03/04/2020
ms.openlocfilehash: 4b452b31338760a8f53eed54420319101836bc00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474888"
---
# <a name="source-map-support-for-javascript-applications"></a>Suporte de mapa de origem para aplicações JavaScript

Application Insights suporta o upload de mapas de origem para o seu próprio recipiente blob de conta de armazenamento.
Os mapas de origem podem ser usados para não miniminizar pilhas de chamadas encontradas na página final de detalhes de transações. Qualquer exceção enviada pelo [JavaScript SDK][ApplicationInsights-JS] ou pelo [Node.js SDK][ApplicationInsights-Node.js] pode ser desminizada com mapas de origem.

![Unminify a Call Stack ligando-se a uma conta de armazenamento](./media/source-map-support/details-unminify.gif)

## <a name="create-a-new-storage-account-and-blob-container"></a>Criar uma nova conta de armazenamento e recipiente Blob

Se já tiver uma conta de armazenamento ou recipiente de bolha existente, pode saltar este passo.

1. [Criar uma nova conta de armazenamento][create storage account]
2. [Crie um recipiente de bolhas][create blob container] dentro da sua conta de armazenamento. Certifique-se de definir o "nível de acesso público" para, `Private`para garantir que os seus mapas de origem não são acessíveis ao público.

> [!div class="mx-imgBorder"]
>![O seu nível de acesso ao contentor deve ser definido para Privado](./media/source-map-support/container-access-level.png)

## <a name="push-your-source-maps-to-your-blob-container"></a>Empurre os seus mapas de origem para o seu recipiente Blob

Deve integrar o seu pipeline de implantação contínua com a sua conta de armazenamento configurando-o para carregar automaticamente os seus mapas de origem para o recipiente Blob configurado. Não deve enviar os seus mapas de origem para uma subpasta no recipiente Blob; atualmente o mapa de origem só será recolhido da pasta raiz.

### <a name="upload-source-maps-via-azure-pipelines-recommended"></a>Enviar mapas de fonte através de Pipelines Azure (recomendado)

Se estiver a utilizar pipelines Azure para construir e implementar continuamente a sua aplicação, adicione uma tarefa [Azure File Copy][azure file copy] ao seu pipeline para carregar automaticamente os seus mapas de origem.

> [!div class="mx-imgBorder"]
> ![Adicione uma tarefa de cópia de ficheiro saqueada no seu Pipeline para fazer upload dos seus mapas de origem para o Armazenamento de Blob Azure](./media/source-map-support/azure-file-copy.png)

## <a name="configure-your-application-insights-resource-with-a-source-map-storage-account"></a>Configure o seu recurso Deinsights de Aplicação com uma conta de armazenamento do Mapa Fonte

### <a name="from-the-end-to-end-transaction-details-page"></a>A partir da página de detalhes de transação de ponta a ponta

A partir do separador de dados de transação de ponta a ponta, pode clicar no *Unminify* e irá apresentar uma solicitação para configurar se o seu recurso não estiver configurado.

1. No Portal, veja os detalhes de uma exceção que está minizada.
2. Clique em *Unminify*
3. Se o seu recurso não tiver sido configurado, aparecerá uma mensagem, levando-o a configurar.

### <a name="from-the-properties-page"></a>A partir da página de propriedades

Se quiser configurar ou alterar a conta de armazenamento ou o recipiente Blob que está ligado ao seu Recurso de Insights de Aplicação, pode fazê-lo visualizando o separador *Propriedades* do recurso Application Insights.

1. Navegue para o separador *Propriedades* do seu recurso Application Insights.
2. Clique no recipiente de bolha do *mapa de origem Alterar*.
3. Selecione um recipiente Blob diferente como recipiente de mapas de origem.
4. Clique em `Apply`.

> [!div class="mx-imgBorder"]
> ![Reconfigure o seu recipiente Azure Blob selecionado navegando até à Lâmina de Propriedades](./media/source-map-support/reconfigure.png)

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="required-role-based-access-control-rbac-settings-on-your-blob-container"></a>Definições de controlo de acesso baseadas em funções necessárias (RBAC) no seu recipiente Blob

Qualquer utilizador no Portal que utilize esta funcionalidade deve ser atribuído pelo menos como leitor de [dados blob][storage blob data reader] de armazenamento ao seu recipiente Blob. Deve atribuir este papel a qualquer outra pessoa que esteja a utilizar os mapas de origem através desta funcionalidade.

> [!NOTE]
> Dependendo da forma como o recipiente foi criado, este pode não ter sido automaticamente atribuído a si ou à sua equipa.

### <a name="source-map-not-found"></a>Mapa de origem não encontrado

1. Verifique se o mapa de origem correspondente é enviado para o recipiente de bolhas correto
2. Verifique se o ficheiro do mapa de origem tem o nome `.map`do ficheiro JavaScript para o que mapeia, sufixo com .
    - Por exemplo, `/static/js/main.4e2ca5fa.chunk.js` vai procurar a bolha chamada`main.4e2ca5fa.chunk.js.map`
3. Verifique a consola do seu navegador para ver se algum erro está a ser registado. Inclua isto em qualquer bilhete de apoio.

## <a name="next-steps"></a>Passos Seguintes

* [Tarefa de cópia de ficheiro sinuoso](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops)


<!-- Remote URLs -->
[create storage account]: https://docs.microsoft.com/azure/storage/common/storage-account-create?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal
[create blob container]: https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal
[storage blob data reader]: https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader
[ApplicationInsights-JS]: https://github.com/microsoft/applicationinsights-js
[ApplicationInsights-Node.js]: https://github.com/microsoft/applicationinsights-node.js
[azure file copy]: https://aka.ms/azurefilecopyreadme