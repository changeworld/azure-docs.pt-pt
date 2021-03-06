---
title: Criar um teste de Análise de Internet utilizando o CLI [ Microsoft Docs
description: Neste artigo, aprenda a criar o seu primeiro teste de Análise de Internet.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: d474442086e2a114f26df279ab2682cd7628a5f5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74184272"
---
# <a name="create-an-internet-analyzer-test-using-cli-preview"></a>Criar um teste de análise da Internet utilizando o CLI (Pré-visualização)

Existem duas formas de criar um recurso analisador de Internet - utilizando o [portal Azure](internet-analyzer-create-test-portal.md) ou usando o CLI. Esta secção ajuda-o a criar um novo recurso Azure Internet Analyzer utilizando a nossa experiência CLI. 


> [!IMPORTANT]
> Esta pré-visualização pública é disponibilizada sem um contrato de nível de serviço e não deve ser utilizada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, podem ter capacidades restringidas ou podem não estar disponíveis em todas as localizações do Azure. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.
>

## <a name="before-you-begin"></a>Antes de começar

A pré-visualização pública está disponível para ser usada globalmente; no entanto, o armazenamento de dados está limitado a *US West 2* durante a pré-visualização.

## <a name="object-model"></a>Modelo de objeto
O Internet Analyzer CLI expõe os seguintes tipos de recursos:
* **Testes** - Um teste compara o desempenho do utilizador final de dois pontos finais de internet (A e B) ao longo do tempo.
* **Perfis** - Os testes são criados sob um perfil de Analisador de Internet. Os perfis permitem a grumos agrupar testes conexos; um único perfil pode conter um ou mais testes.
* **Pontos Finais reconfigurados** - Estabelecemos pontos finais com uma variedade de configurações (regiões, tecnologias de aceleração, etc.). Pode utilizar qualquer um destes pontos finais pré-configurados nos seus testes.
* **Scorecards** - Um cartão de pontuação fornece resumos rápidos e significativos dos resultados da medição. Consulte [a Interpretação do seu Cartão de Pontuação](internet-analyzer-scorecard.md).
* **Série de tempo** - Uma série de tempo mostra como uma métrica muda ao longo do tempo.

## <a name="profile-and-test-creation"></a>Criação de Perfis e Testes
1. Obtenha acesso de pré-visualização do Internet Analyzer seguindo o [Azure Internet Analyzer FAQ](internet-analyzer-faq.md) **como posso participar na pré-visualização?**
2. [Instale o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
3. Executar `login` o comando para iniciar uma sessão CLI:
    ```azurecli-interactive
    az login
    ```

    Se o CLI conseguir abrir o seu navegador predefinido, fá-lo-á e carregará uma página de entrada azure.
    Caso contrário, abra uma https://aka.ms/devicelogin página do navegador e introduza o código de autorização apresentado no seu terminal.

4. Inicie sessão com as credenciais da sua conta no browser.

5. Selecione o seu ID de subscrição que tenha tido acesso à pré-visualização pública do Analisador de Internet.

    Após o início do sessão, consulte uma lista de subscrições associadas à sua conta Azure. A informação `isDefault: true` de subscrição é a subscrição ativada atualmente após o login. Para selecionar outra subscrição, utilize o comando conjunto de [contas az](https://docs.microsoft.com/cli/azure/account#az-account-set) com o ID de subscrição para mudar. Para obter mais informações sobre a seleção de subscrições, consulte [Utilize várias subscrições do Azure.](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)

    Existem formas de iniciar sessão de forma não interativa, que são abordadas em detalhe em [Iniciar sessão com a CLI do Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

6. **[Opcional]** Crie um novo Grupo de Recursos Azure:
    ```azurecli-interactive
    az group create --location eastus --name "MyInternetAnalyzerResourceGroup"
    ```

7. Instale a extensão do Analisador de Internet Azure CLI:
     ```azurecli-interactive
    az extension add --name internet-analyzer
    ```

8. Criar um novo perfil de Analisador de Internet:
    ```azurecli-interactive
    az internet-analyzer profile create --location eastus --resource-group "MyInternetAnalyzerResourceGroup" --name "MyInternetAnalyzerProfile" --enabled-state Enabled
    ```

9. Enumerar todos os pontos finais pré-configurados disponíveis para o perfil recém-criado:
    ```azurecli-interactive
    az internet-analyzer preconfigured-endpoint list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
    ```

10. Criar um novo teste sob o perfil de InternetAnalyzer recém-criado:
    ```azurecli-interactive
    az internet-analyzer test create --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --endpoint-a-name "contoso" --endpoint-a-endpoint "www.contoso.com/some/path/to/trans.gif" --endpoint-b-name "microsoft" --endpoint-b-endpoint "www.microsoft.com/another/path/to/trans.gif" --name "MyFirstInternetAnalyzerTest" --enabled-state Enabled
    ```

    O comando acima pressupõe que ambos `www.contoso.com` e `www.microsoft.com` estão hospedando a imagem de um pixel[(trans.gif](https://fpc.msedge.net/apc/trans.gif)) em caminhos personalizados. Se um caminho de objeto não for especificado `/apc/trans.gif` explicitamente, o Analisador de Internet usará como o caminho do objeto por padrão, que é onde os pontos finais pré-configurados estão hospedando a imagem de um pixel. Note também que o esquema (https/http) não precisa de ser especificado; O Analisador de Internet suporta apenas pontos finais HTTPS, pelo que https é assumido.

11. O novo teste deve aparecer no perfil do Analisador de Internet:
    ```azurecli-interactive
    az internet-analyzer test list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
    ```

    Exemplo de saída:
    ````
    [
        {
            "description": null,
            "enabledState": "Enabled",
            "endpointA": {
            "endpoint": "www.contoso.com/some/path/to/1k.jpg",
            "name": "contoso"
            },
            "endpointB": {
            "endpoint": "www.microsoft.com/another/path/to/1k.jpg",
            "name": "microsoft"
            },
            "id": "/subscriptions/faa9ddd0-9137-4659-99b7-cdc55a953342/resourcegroups/MyInternetAnalyzerResourceGroup/providers/Microsoft.Network/networkexperimentprofiles/MyInternetAnalyzerProfile/experiments/MyFirstInternetAnalyzerTest",
            "location": null,
            "name": "MyFirstInternetAnalyzerTest",
            "resourceGroup": "MyInternetAnalyzerResourceGroup",
            "resourceState": "Enabled",
            "scriptFileUri": "https://fpc.msedge.net/client/v2/d8c6fc64238d464c882cee4a310898b2/ab.min.js",
            "status": "Created",
            "tags": null,
            "type": "Microsoft.Network/networkexperimentprofiles/experiments"
        }
    ]
    ````

12. Para começar a gerar medições, o ficheiro JavaScript apontado pelo **script FileUri** do teste deve ser incorporado na sua aplicação Web. Instruções específicas podem ser encontradas na página do Cliente do Analisador de [Internet Incorporado.](internet-analyzer-embed-client.md)

13. Pode monitorizar o progresso do teste mantendo o seu valor de "estado":
    ```azurecli-interactive
    az internet-analyzer test show --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest"
    ```

14. Pode inspecionar os resultados recolhidos do teste gerando séries de tempo ou cartões de pontuação para o mesmo:
    ```azurecli-interactive
    az internet-analyzer show-scorecard --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Daily" --end-date-time-utc "2019-10-24T00:00:00"
    ```

    ```azurecli-interactive
    az internet-analyzer show-timeseries --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Hourly" --start-date-time-utc "2019-10-23T00:00:00" --end-date-time-utc "2019-10-24T00:00:00" --timeseries-type MeasurementCounts
    ```


## <a name="next-steps"></a>Passos seguintes

* Consulte a [referência CLI](https://docs.microsoft.com/cli/azure/ext/internet-analyzer/internet-analyzer?view=azure-cli-latest) do Analisador de Internet para obter a lista completa de comandos suportados e exemplos de utilização.
* Leia o FaQ do [Analisador](internet-analyzer-faq.md)de Internet .
* Saiba mais sobre incorporar o [Cliente analisador](internet-analyzer-embed-client.md) de Internet e criar um [ponto final personalizado.](internet-analyzer-custom-endpoint.md) 
