---
title: Execute a sua aplicação a partir de um pacote ZIP
description: Implemente o pacote ZIP da sua aplicação com atómico. Melhore a previsibilidade e fiabilidade do comportamento da sua aplicação durante o processo de implementação do ZIP.
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 5cc909d79b3f5ea2b4c6a3da12bc7250addbe00c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920727"
---
# <a name="run-your-app-in-azure-app-service-directly-from-a-zip-package"></a>Execute a sua aplicação no Serviço de Aplicações Azure diretamente de um pacote ZIP

No [Azure App Service,](overview.md)pode executar as suas aplicações diretamente a partir de um ficheiro de pacote ZIP de implementação. Este artigo mostra como ativar esta funcionalidade na sua aplicação.

Todos os outros métodos de implementação no App Service têm algo em comum: os seus ficheiros são implantados para *D:\home\site\wwwroot* na sua app (ou */home/site/wwwroot* para aplicações Linux). Uma vez que o mesmo diretório é utilizado pela sua app em tempo de execução, é possível que a implementação falhe devido a conflitos de bloqueio de ficheiros, e para que a app se compore de forma imprevisível porque alguns dos ficheiros ainda não estão atualizados.

Em contrapartida, quando se executa diretamente de um pacote, os ficheiros da embalagem não são copiados para o diretório *wwwroot.* Em vez disso, o pacote ZIP em si é montado diretamente como o diretório *wwwroot* apenas de leitura. Existem vários benefícios para correr diretamente a partir de um pacote:

- Elimina os conflitos de bloqueio de ficheiros entre a implantação e o tempo de execução.
- Garante que apenas as aplicações totalmente implementadas estão a funcionar a qualquer momento.
- Pode ser implantado numa aplicação de produção (com reinício).
- Melhora o desempenho das implementações do Gestor de Recursos Azure.
- Pode reduzir os tempos de arranque a frio, particularmente para funções JavaScript com grandes árvores de embalagem npm.

> [!NOTE]
> Atualmente, apenas os ficheiros de pacotezip são suportados.

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

## <a name="enable-running-from-package"></a>Ativar o funcionamento do pacote

A `WEBSITE_RUN_FROM_PACKAGE` definição da aplicação permite a execução de um pacote. Para o definir, execute o seguinte comando com o Azure CLI.

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_RUN_FROM_PACKAGE="1"
```

`WEBSITE_RUN_FROM_PACKAGE="1"`Permite-lhe executar a sua aplicação de um pacote local para a sua aplicação. Também pode correr a [partir de um pacote remoto.](#run-from-external-url-instead)

## <a name="run-the-package"></a>Executar o pacote

The easiest way to run a package in your App Service is with the Azure CLI [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) command. Por exemplo:

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src <filename>.zip
```

Como `WEBSITE_RUN_FROM_PACKAGE` a definição da aplicação está definida, este comando não extrai o conteúdo do pacote para o *d:\home\site\wwwroot* diretório da sua app. Em vez disso, ele envia o ficheiro ZIP como é para *D:\home\data\SitePackages*, e cria um *nome de pacote.txt* no mesmo diretório, que contém o nome do pacote ZIP para carregar no tempo de execução. Se fizer o upload do seu pacote ZIP de uma forma diferente (como [FTP),](deploy-ftp.md)tem de criar manualmente o diretório *D:\home\data\SitePackages* e o ficheiro *packagename.txt* manualmente.

O comando também reinicia a aplicação. Como `WEBSITE_RUN_FROM_PACKAGE` está definido, o App Service monta o pacote carregado como o diretório *wwwroot* apenas de leitura e executa a app diretamente a partir desse diretório montado.

## <a name="run-from-external-url-instead"></a>Executar a partir de URL externo em vez

Também pode executar uma embalagem a partir de um URL externo, como o Armazenamento De Blob Azure. Pode utilizar o [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) para fazer o upload de ficheiros de pacotes para a sua conta de armazenamento Blob. Deve utilizar um recipiente de armazenamento privado com uma Assinatura de [Acesso Partilhado (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) para permitir que o tempo de execução do Serviço de Aplicações aceda de forma segura à embalagem. 

Assim que enviar o seu ficheiro para o armazenamento blob `WEBSITE_RUN_FROM_PACKAGE` e tiver um URL SAS para o ficheiro, defina a definição da aplicação para o URL. O exemplo seguinte fá-lo utilizando o Azure CLI:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="https://myblobstorage.blob.core.windows.net/content/SampleCoreMVCApp.zip?st=2018-02-13T09%3A48%3A00Z&se=2044-06-14T09%3A48%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=bNrVrEFzRHQB17GFJ7boEanetyJ9DGwBSV8OM3Mdh%2FM%3D"
```

Se publicar um pacote atualizado com o mesmo nome para o armazenamento Blob, precisa de reiniciar a sua aplicação para que o pacote atualizado seja carregado no Serviço de Aplicações.

## <a name="troubleshooting"></a>Resolução de problemas

- Correr diretamente de `wwwroot` um pacote faz apenas leitura. A sua aplicação receberá um erro se tentar escrever ficheiros para este diretório.
- Os formatos TAR e GZIP não são suportados.
- Esta função não é compatível com [cache local](overview-local-cache.md).
- Para um melhor desempenho a frio, utilize`WEBSITE_RUN_FROM_PACKAGE`a opção Zip local (=1).

## <a name="more-resources"></a>Mais recursos

- [Implantação contínua para o Serviço de Aplicações Azure](deploy-continuous-deployment.md)
- [Implementar código com um ficheiro ZIP ou WAR](deploy-zip.md)
