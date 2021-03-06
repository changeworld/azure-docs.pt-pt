---
title: Plano Premium de Funções Azure
description: Detalhes e opções de configuração (VNet, sem arranque a frio, duração de execução ilimitada) para o plano Azure Functions Premium.
author: jeffhollan
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: jehollan
ms.openlocfilehash: dd7f6d0760f2b848435e7c77657e261517d29dd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276910"
---
# <a name="azure-functions-premium-plan"></a>Plano Premium de Funções Azure

O plano Azure Functions Premium (por vezes referido como plano Elástico Premium) é uma opção de hospedagem para aplicações de função. O plano Premium fornece funcionalidades como conectividade VNet, sem arranque a frio e hardware premium.  As aplicações de múltiplas funções podem ser implementadas para o mesmo plano Premium, e o plano permite configurar o tamanho da instância computacional, o tamanho do plano base e o tamanho máximo do plano.  Para uma comparação do plano Premium e de outros tipos de plano e hospedagem, consulte a escala de [funções e as opções de hospedagem.](functions-scale.md)

## <a name="create-a-premium-plan"></a>Criar um plano Premium

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

Também pode criar um plano Premium utilizando o [plano de az functionapp criado](/cli/azure/functionapp/plan#az-functionapp-plan-create) no Azure CLI. O exemplo seguinte cria um plano de nível _Premium 1 elástico:_

```azurecli-interactive
az functionapp plan create --resource-group <RESOURCE_GROUP> --name <PLAN_NAME> \
--location <REGION> --sku EP1
```

Neste exemplo, `<RESOURCE_GROUP>` substitua-o `<PLAN_NAME>` pelo seu grupo de recursos e por um nome para o seu plano que é único no grupo de recursos. Especifique [um `<REGION>` ](https://azure.microsoft.com/global-infrastructure/services/?products=functions)suporte . Para criar um plano Premium que suporte `--is-linux` o Linux, inclua a opção.

Com o plano criado, pode utilizar o [az functionapp criar](/cli/azure/functionapp#az-functionapp-create) para criar a sua aplicação de função. No portal, tanto o plano como a app são criados ao mesmo tempo. Para um exemplo de um script Completo Do ClI Azure, consulte [Criar uma aplicação de função num plano Premium](scripts/functions-cli-create-premium-plan.md).

## <a name="features"></a>Funcionalidades

As seguintes funcionalidades estão disponíveis para funcionar aplicações implementadas num plano Premium.

### <a name="pre-warmed-instances"></a>Casos pré-aquecidos

Se não ocorrerem eventos e execuções hoje no plano de consumo, a sua aplicação poderá dimensionar para zero instâncias. Quando novos eventos chegam, uma nova instância precisa de ser especializada com a sua aplicação a funcionar nela.  A especialização de novos casos pode demorar algum tempo dependendo da aplicação.  Esta latência adicional na primeira chamada é muitas vezes chamada de início frio da aplicação.

No plano Premium, pode ter a sua app pré-aquecida num número determinado de casos, até ao tamanho mínimo do plano.  As instâncias pré-aquecidas também permitem pré-escalar uma aplicação antes de uma carga elevada. À medida que a aplicação se baseia, ele entra primeiro nos casos pré-aquecidos. Casos adicionais continuam a tamponar e a aquecer imediatamente na preparação para a próxima operação em escala. Ao ter um tampão de instâncias pré-aquecidas, pode efetivamente evitar atrasos no início do frio.  Casos pré-aquecidos são uma característica do plano Premium, e você precisa manter pelo menos uma instância em funcionamento e disponível em todos os momentos o plano está ativo.

Pode configurar o número de instâncias pré-aquecidas no portal Azure por selecionou a sua **App de Funções,** indo ao separador **Funcionalidades** da Plataforma e selecionando as opções **Scale out.** Na janela de edição da aplicação de funções, as instâncias pré-aquecidas são específicas dessa aplicação, mas as instâncias mínimas e máximas aplicam-se a todo o seu plano.

![Definições de escala elástica](./media/functions-premium-plan/scale-out.png)

Também pode configurar instâncias pré-aquecidas para uma aplicação com o Azure CLI.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="private-network-connectivity"></a>Conectividade de rede privada

As Funções Azure implementadas num plano Premium aproveitam a [nova integração vNet para aplicações web.](../app-service/web-sites-integrate-with-vnet.md)  Quando configurado, a sua aplicação pode comunicar com recursos dentro do seu VNet ou protegido através de pontos finais de serviço.  Estão também disponíveis restrições IP na aplicação para restringir o tráfego de entrada.

Ao atribuir uma sub-rede à sua aplicação de funções num plano Premium, precisa de uma sub-rede com endereços IP suficientes para cada instância potencial. Precisamos de um bloco IP com pelo menos 100 endereços disponíveis.

Para mais informações, consulte [integrar a sua aplicação de funções com um VNet](functions-create-vnet.md).

### <a name="rapid-elastic-scale"></a>Escala elástica rápida

As instâncias de cálculo adicionais são adicionadas automaticamente para a sua aplicação usando a mesma lógica de escala rápida que o plano de consumo.  Para saber mais sobre como funciona a escala, consulte a [escala de funções e](./functions-scale.md#how-the-consumption-and-premium-plans-work)o alojamento.

### <a name="longer-run-duration"></a>Duração de longo prazo

As Funções Azure num plano de consumo estão limitadas a 10 minutos para uma única execução.  No plano Premium, a duração da execução não se aplica a 30 minutos para evitar execuções em fuga. No entanto, pode [modificar a configuração host.json](./functions-host-json.md#functiontimeout) para tornar esta aplicação de plano Premium deslimitada (60 minutos garantida).

## <a name="plan-and-sku-settings"></a>Plan e definições de SKU

Quando cria o plano, configura duas definições: o número mínimo de instâncias (ou tamanho do plano) e o limite máximo de rutura.  As instâncias mínimas são reservadas e sempre em funcionamento.

> [!IMPORTANT]
> É cobrado por cada instância atribuída na contagem mínima de instância, independentemente de as funções estarem a executar ou não.

Se a sua aplicação necessitar de instâncias para além do tamanho do seu plano, pode continuar a escalar até que o número de casos atinja o limite máximo de rutura.  Você é cobrado por casos além do tamanho do seu plano apenas enquanto eles estão correndo e alugado para você.  Faremos um esforço melhor para aumentar a sua aplicação para o seu limite máximo definido, enquanto os casos de plano mínimo são garantidos para a sua aplicação.

Pode configurar o tamanho e os máximos do plano no portal Azure selecionando as opções **Scale out** no plano ou uma aplicação de função implementada para esse plano (segundo as Funcionalidades da **Plataforma).**

Também pode aumentar o limite máximo de rutura do Azure CLI:

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.maximumElasticWorkerCount=<desired_max_burst> --resource-type Microsoft.Web/serverfarms 
```

### <a name="available-instance-skus"></a>SKUs de instância disponível

Ao criar ou escalar o seu plano, pode escolher entre três tamanhos de instância.  Será cobrado pelo número total de núcleos e memória consumidos por segundo.  A sua aplicação pode automaticamente dimensionar para várias instâncias, se necessário.  

|SKU|Núcleos|Memória|Storage|
|--|--|--|--|
|EP1|1|3.5GB|250GB|
|EP2|2|7GB|250GB|
|EP3|4|14GB|250GB|

### <a name="memory-utilization-considerations"></a>Considerações de utilização da memória
Correr numa máquina com mais memória nem sempre significa que a sua aplicação de funções utilizará toda a memória disponível.

Por exemplo, uma aplicação de função JavaScript é limitada pelo limite de memória padrão no Node.js. Para aumentar este limite de memória `languageWorkers:node:arguments` fixo, `--max-old-space-size=<max memory in MB>`adicione a definição da aplicação com um valor de .

## <a name="region-max-scale-out"></a>Região Max Scale out

Abaixo estão os valores de escala máxima atualmente suportados para um único plano em cada região e configuração de OS. Para solicitar um aumento, por favor, abra um bilhete de apoio.

Consulte aqui a disponibilidade regional completa de Funções: [Azure.com](https://azure.microsoft.com/global-infrastructure/services/?products=functions)

|Região| Windows | Linux |
|--| -- | -- |
|Austrália Central| 20 | Não Disponível |
|Austrália Central 2| 20 | Não Disponível |
|Leste da Austrália| 100 | 20 |
|Austrália Sudeste | 100 | 20 |
|Sul do Brasil| 60 | 20 |
|Canadá Central| 100 | 20 |
|E.U.A. Central| 100 | 20 |
|Ásia Leste| 100 | 20 |
|E.U.A. Leste | 100 | 20 |
|E.U.A. Leste 2| 100 | 20 |
|França Central| 100 | 20 |
|Alemanha West Central| 100 | Não Disponível |
|Leste do Japão| 100 | 20 |
|Oeste do Japão| 100 | 20 |
|Coreia do Sul Central| 100 | 20 |
|E.U.A. Centro-Norte| 100 | 20 |
|Europa do Norte| 100 | 20 |
|Noruega Leste| 20 | 20 |
|E.U.A. Centro-Sul| 100 | 20 |
|Sul da Índia | 100 | Não Disponível |
|Ásia Sudeste| 100 | 20 |
|Sul do Reino Unido| 100 | 20 |
|Oeste do Reino Unido| 100 | 20 |
|Europa ocidental| 100 | 20 |
|Oeste da Índia| 100 | 20 |
|E.U.A. Centro-Oeste| 20 | 20 |
|E.U.A. Oeste| 100 | 20 |
|E.U.A.Oeste 2| 100 | 20 |

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Compreender as opções de escala e hospedagem de Funções Azure](functions-scale.md)
