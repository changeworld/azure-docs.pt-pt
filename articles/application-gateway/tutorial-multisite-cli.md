---
title: Alojamento de vários sites usando CLI - Portal de Aplicação Azure
description: Saiba como criar um portal de aplicações que acolhe vários sites usando o Azure CLI.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 0e58d9ecfbd0731fc9bf91664763e73d8c56e64a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294754"
---
# <a name="create-an-application-gateway-with-multiple-site-hosting-using-the-azure-cli"></a>Criar um portal de aplicações com vários sites de hospedagem usando o Azure CLI

Pode utilizar o Azure CLI para configurar o [alojamento de vários web sites](application-gateway-multi-site-overview.md) quando criar um gateway de [aplicação](application-gateway-introduction.md). Neste tutorial, você cria piscinas de backend usando conjuntos de escala de máquinas virtuais. Em seguida, vai configurar os serviços de escuta e as regras com base nos domínios que possui para assegurar que o tráfego Web chega aos servidores adequados nos conjuntos. Este tutorial assume que possui vários domínios `www.contoso.com` `www.fabrikam.com`e utiliza exemplos de e .

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Configurar a rede
> * Criar um gateway de aplicação
> * Criar ouvintes e regras de encaminhamento
> * Criar conjuntos de dimensionamento de máquinas virtuais com conjuntos de back-end
> * Criar um registo CNAME no seu domínio

![Exemplo de encaminhamento multilocal](./media/tutorial-multisite-cli/scenario.png)

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0.4 ou posterior da CLI do Azure. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos. Crie um grupo de recursos com [az group create](/cli/azure/group).

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroupAG* na localização *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Criar recursos de rede 

Crie a rede virtual denominada *myVNet* e a sub-rede denominada *myAGSubnet* com [az network vnet create](/cli/azure/network/vnet). Em seguida, pode adicionar a sub-rede denominada *myBackendSubnet* que é necessária para os servidores de back-end, com [az network vnet subnet create](/cli/azure/network/vnet/subnet). Crie o endereço IP público denominado *myAGPublicIPAddress* com [az network public-ip create](/cli/azure/network/public-ip).

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-the-application-gateway"></a>Criar o gateway de aplicação

Pode utilizar [az network application-gateway create](/cli/azure/network/application-gateway) para criar o gateway de aplicação denominado *myAppGateway*. Quando cria um gateway de aplicação com a CLI do Azure, especifica informações de configuração, tais como a capacidade, sku e definições de HTTP. O gateway de aplicação é atribuído a *myAGSubnet* e *myAGPublicIPAddress* que criou anteriormente. 

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

A criação do gateway de aplicação pode demorar vários minutos. Depois de criado o gateway de aplicação, pode ver estas novas funcionalidades do mesmo:

- *appGatewayBackendPool* - um gateway de aplicação tem de ter, pelo menos, um conjunto de endereços de back-end.
- *appGatewayBackendHttpSettings* - especifica que a porta 80 e um protocolo HTTP são utilizados para a comunicação.
- *appGatewayHttpListener* - o serviço de escuta predefinido associado a *appGatewayBackendPool*.
- *appGatewayFrontendIP* - atribui *myAGPublicIPAddress* a *appGatewayHttpListener*.
- *rule1* - a regra de encaminhamento predefinida associada a *appGatewayHttpListener*.

### <a name="add-the-backend-pools"></a>Adicionar os conjuntos de back-end

Adicione as piscinas de backend chamadas *contosoPool* e *fabrikamPool* que são necessárias para conter os servidores backend usando a criação de [endereços de gateway de aplicação de rede az](/cli/azure/network/application-gateway).

```azurecli-interactive
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name contosoPool
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name fabrikamPool
```

### <a name="add-listeners"></a>Adicionar serviços de escuta

É necessário um serviço de escuta para permitir ao gateway de aplicação encaminhar o tráfego adequadamente para o conjunto de back-end. Neste tutorial, vai criar dois serviços de escuta para os seus dois domínios. Neste exemplo, os serviços de escuta são criados para os domínios de *www.contoso.com* e *www.fabrikam.com*. 

Adicione os *ouvintes chamados contosoListener* e *fabrikamListener* que são necessários para direcionar o tráfego usando a [criação de um gateway de aplicação de rede az](/cli/azure/network/application-gateway).

```azurecli-interactive
az network application-gateway http-listener create \
  --name contosoListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.com
az network application-gateway http-listener create \
  --name fabrikamListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.fabrikam.com   
  ```

### <a name="add-routing-rules"></a>Adicionar regras de encaminhamento

As regras são processadas na ordem em que são criadas, e o tráfego é direcionado usando a primeira regra que corresponde ao URL enviado para o gateway da aplicação. Por exemplo, se tiver uma regra com um serviço de escuta básico e uma regra com uma escuta de vários sites, ambas na mesma porta, a regra com o serviço de escuta de vários sites tem de estar listada antes da regra com o serviço de escuta básico, para que a regra de vários sites funcione conforme esperado. 

Neste exemplo, vai criar duas novas regras e eliminar a regra predefinida que foi criada quando criou o gateway de aplicação. Pode adicionar a regra com [az network application-gateway rule create](/cli/azure/network/application-gateway).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoListener \
  --rule-type Basic \
  --address-pool contosoPool
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name fabrikamRule \
  --resource-group myResourceGroupAG \
  --http-listener fabrikamListener \
  --rule-type Basic \
  --address-pool fabrikamPool
az network application-gateway rule delete \
  --gateway-name myAppGateway \
  --name rule1 \
  --resource-group myResourceGroupAG
```

## <a name="create-virtual-machine-scale-sets"></a>Criar conjuntos de dimensionamento de máquinas virtuais

Neste exemplo, vai criar três conjuntos de dimensionamento de máquinas virtuais que suportam os três conjuntos de back-end no gateway de aplicação. Os conjuntos de dimensionamento que criar são denominados *myvmss1*, *myvmss2* e *myvmss3*. Cada conjunto de dimensionamento contém duas instâncias de máquina virtual onde vai instalar o NGINX.

```azurecli-interactive
for i in `seq 1 2`; do
  if [ $i -eq 1 ]
  then
    poolName="contosoPool"
  fi
  if [ $i -eq 2 ]
  then
    poolName="fabrikamPool"
  fi
  az vmss create \
    --name myvmss$i \
    --resource-group myResourceGroupAG \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password Azure123456! \
    --instance-count 2 \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --vm-sku Standard_DS2 \
    --upgrade-policy-mode Automatic \
    --app-gateway myAppGateway \
    --backend-pool-name $poolName
done
```

### <a name="install-nginx"></a>Instalar o NGINX

```azurecli-interactive
for i in `seq 1 2`; do
  az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroupAG \
    --vmss-name myvmss$i \
    --settings '{
  "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'
done
```

## <a name="create-a-cname-record-in-your-domain"></a>Criar um registo CNAME no seu domínio

Depois de criar o gateway de aplicação com o respetivo endereço IP público, pode obter o endereço DNS e utilizá-lo para criar um registo CNAME no seu domínio. Pode utilizar [az network public-ip show](/cli/azure/network/public-ip) para obter o endereço DNS do gateway de aplicação. Copie o valor *fqdn* de DNSSettings e utilize-o como o valor do registo CNAME que criar. 

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [dnsSettings.fqdn] \
  --output tsv
```

Não é recomendada a utilização de registos A, uma vez que o VIP pode ser alterado no reinício do gateway de aplicação.

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicação

Introduza o nome de domínio na barra de endereço do seu browser. Tais como,\:http //www.contoso.com.

![Testar o site contoso no gateway de aplicação](./media/tutorial-multisite-cli/application-gateway-nginxtest1.png)

Altere o endereço para o outro domínio, e deverá ver algo semelhante ao seguinte exemplo:

![Testar o site fabrikam no gateway de aplicação](./media/tutorial-multisite-cli/application-gateway-nginxtest2.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Configurar a rede
> * Criar um gateway de aplicação
> * Criar ouvintes e regras de encaminhamento
> * Criar conjuntos de dimensionamento de máquinas virtuais com conjuntos de back-end
> * Criar um registo CNAME no seu domínio

> [!div class="nextstepaction"]
> [Saiba mais sobre o que pode fazer com o gateway de aplicação](application-gateway-introduction.md)
