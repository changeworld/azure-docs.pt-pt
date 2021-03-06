---
title: 'Tutorial: Implementar & configurar firewall Azure utilizando o portal Azure'
description: Neste tutorial, irá aprender a implementar e configurar o Azure Firewall com o portal do Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 02/21/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 064fcf618914bca31ad9e7e60c76df8f599cd8bf
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239575"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-using-the-azure-portal"></a>Tutorial: implementar e configurar o Azure Firewall com o portal do Azure

Controlar o acesso de rede de saída é uma parte importante de um plano de segurança de rede geral. Por exemplo, pode querer limitar o acesso aos sites. Ou, pode querer limitar os endereços IP de saída e as portas que podem ser acedidas.

Uma forma de controlar o acesso de rede de saída a partir de uma sub-rede do Azure é com a Azure Firewall. Com a Azure Firewall, pode configurar:

* Regras da aplicação que definem nomes de domínio completamente qualificado (FQDNs) que podem ser acedidos a partir de uma sub-rede.
* Regras de rede que definem o endereço de origem, o protocolo, a porta de destino e o endereço de destino.

O tráfego de rede está sujeito às regras de firewall configuradas quando encaminha o tráfego de rede para a firewall como o gateway padrão de sub-rede.

Neste tutorial, criou uma VNet única simplificada com três sub-redes para uma implementação simples. Para implantações de produção, [recomenda-se](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) um modelo de hub e spoke. A firewall está no seu próprio VNet. Os servidores de carga de trabalho estão em VNets empares na mesma região com uma ou mais subredes.

* **AzureFirewallSubnet** - a firewall está nesta sub-rede.
* **Workload-SN** - o servidor de carga de trabalho está nesta sub-rede. O tráfego de rede desta sub-rede passa pela firewall.
* **Jump-SN** - o servidor “de ligação” está nesta sub-rede. O servidor de ligação tem um endereço IP público ao qual pode ligar com o Ambiente de Trabalho Remoto. A partir daí, pode ligar (com outro Ambiente de Trabalho Remoto) ao servidor de carga de trabalho.

![Tutorial de infraestrutura de rede](media/tutorial-firewall-rules-portal/Tutorial_network.png)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar um ambiente de rede de teste
> * Implementar uma firewall
> * Criar uma rota predefinida
> * Configure uma regra de aplicação para permitir o acesso a www.google.com
> * Configurar uma regra de rede para permitir o acesso aos servidores DNS externos
> * Testar a firewall

Se preferir, pode executar este tutorial com o [Azure PowerShell](deploy-ps.md).

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="set-up-the-network"></a>Configurar a rede

Em primeiro lugar, crie um grupo de recursos para conter os recursos necessários para implementar a firewall. Em seguida, crie uma VNet, sub-redes e servidores de teste.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

O grupo de recursos contém todos os recursos para o tutorial.

1. Inscreva-se no portal [https://portal.azure.com](https://portal.azure.com)Azure em .
2. No menu do portal Azure, selecione **grupos de Recursos** ou procure e selecione *grupos de Recursos* a partir de qualquer página. Em seguida, selecione **Adicionar**.
3. Para o nome do **grupo Resource,** insira *Test-FW-RG*.
4. Em **Subscrição**, selecione a sua subscrição.
5. Em **Localização do grupo de recursos**, selecione uma localização. Todos os outros recursos que cria devem estar no mesmo local.
6. Selecione **Criar**.

### <a name="create-a-vnet"></a>Criar uma VNet

Esta VNet irá conter três sub-redes.

> [!NOTE]
> O tamanho da sub-rede AzureFirewallSubnet é de /26. Para obter mais informações sobre o tamanho da subrede, consulte [O FaQ da Firewall Azure](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

1. No menu do portal do Azure ou a partir da **Home Page**, selecione **Criar um recurso**.
1. Selecione**rede virtual**de **rede** > .
1. Em **Nome**, escreva **Test-FW-VN**.
1. Em **Espaço de endereços**, escreva **10.0.0.0/16**.
1. Em **Subscrição**, selecione a sua subscrição.
1. Para **o grupo Derecursos,** selecione **Test-FW-RG**.
1. Em **Localização**, selecione a mesma localização que utilizou anteriormente.
1. Em **Sub-rede**, em **Nome**, escreva **AzureFirewallSubnet**. A firewall estará nesta sub-rede, e o nome da sub-rede **tem** de ser AzureFirewallSubnet.
1. Para **o intervalo de endereços**, tipo **10.0.1.0/26**.
1. Aceite as outras definições predefinidas e, em seguida, selecione **Criar**.

### <a name="create-additional-subnets"></a>Criar sub-redes adicionais

Em seguida, crie sub-redes para o servidor de ligação e uma sub-rede para os servidores de carga de trabalho.

1. No menu do portal Azure, selecione **grupos de Recursos** ou procure e selecione *grupos de Recursos* a partir de qualquer página. Em seguida, selecione **Test-FW-RG**.
2. Selecione a rede virtual **Test-FW-VN.**
3. Selecione **Subnets** > **+Subnet**.
4. Em **Nome**, escreva **Workload-SN**.
5. Em **Intervalo de endereços**, escreva **10.0.2.0/24**.
6. Selecione **OK**.

Crie outra sub-rede com o nome **Jump-SN** e com o intervalo de endereços **10.0.3.0/24**.

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Agora, crie as máquinas virtuais de ligação e de carga de trabalho, e coloque-as nas sub-redes adequadas.

1. No menu do portal do Azure ou a partir da **Home Page**, selecione **Criar um recurso**.
2. Selecione **Computação** e, em seguida, selecione **Windows Server 2016 Datacenter** na lista Destaques.
3. Introduza estes valores para a máquina virtual:

   |Definição  |Valor  |
   |---------|---------|
   |Grupo de recursos     |**Teste-FW-RG**|
   |Nome da máquina virtual     |**Srv-Jump**|
   |Região     |O mesmo que anterior|
   |Nome de utilizador do administrador     |**azureuser**|
   |Palavra-passe     |**Azure123456!**|

4. De acordo com as regras da **porta de entrada**, para as portas de entrada **pública,** selecione **Permitir portas selecionadas**.
5. Para **selecionar as portas de entrada,** selecione **RDP (3389)**.

6. Aceite as outras predefinições e selecione **Seguinte: Discos**.
7. Aceite as predefinições do disco e selecione **Seguinte: Networking**.
8. Certifique-se de que o **Test-FW-VN** é selecionado para a rede virtual e a sub-rede é **Jump-SN**.
9. Para **IP Público,** aceite o novo nome de endereço ip público (Srv-Jump-ip).
11. Aceite os outros predefinições e selecione **Seguinte: Gestão**.
12. Selecione **Off** para desativar diagnósticos de arranque. Aceite as outras predefinições e selecione **Rever + criar**.
13. Reveja as definições na página resumo e, em seguida, selecione **Criar**.

Utilize as informações na tabela seguinte para configurar outra máquina virtual chamada **Srv-Work**. O resto da configuração é igual à da máquina virtual Srv-Jump.

|Definição  |Valor  |
|---------|---------|
|Subrede|**Workload-SN**|
|IP público|**Nenhum**|
|Portos de entrada pública|**Nenhum**|

## <a name="deploy-the-firewall"></a>Implementar a firewall

Implemente a firewall na VNet.

1. No menu do portal do Azure ou a partir da **Home Page**, selecione **Criar um recurso**.
2. Digite **firewall** na caixa de pesquisa e prima **Enter**.
3. Selecione **firewall** e, em seguida, selecione **Criar**.
4. Na página **Criar uma firewall**, utilize a seguinte tabela para configurar a firewall:

   |Definição  |Valor  |
   |---------|---------|
   |Subscrição     |\<a sua subscrição\>|
   |Grupo de recursos     |**Teste-FW-RG** |
   |Nome     |**Test-FW01**|
   |Localização     |Selecionar a mesma localização que utilizou anteriormente|
   |Escolher uma rede virtual     |**Utilização existente**: **Test-FW-VN**|
   |Endereço IP público     |**Adicione novo**. O endereço IP público tem de ser do tipo SKU Standard.|

5. Selecione **Rever + criar**.
6. Reveja o resumo e, em seguida, selecione **Criar** para criar a firewall.

   Este processo irá demorar alguns minutos a implementar.
7. Após a implementação completa, vá ao grupo de recursos **Test-FW-RG** e selecione a firewall **Test-FW01.**
8. Anote o endereço IP privado. Vai utilizá-lo mais tarde quando criar a rota predefinida.

## <a name="create-a-default-route"></a>Criar uma rota predefinida

Na sub-rede **Workload-SN**, vai configurar a rota de saída predefinida para passar pela firewall.

1. No menu do portal Azure, selecione **Todos os serviços** ou procure e selecione *Todos os serviços* de qualquer página.
2. Em **Rede,** selecione **tabelas de rota.**
3. Selecione **Adicionar**.
4. Em **Nome**, escreva **Firewall-route**.
5. Em **Subscrição**, selecione a sua subscrição.
6. Para **o grupo Derecursos,** selecione **Test-FW-RG**.
7. Em **Localização**, selecione a mesma localização que utilizou anteriormente.
8. Selecione **Criar**.
9. Selecione **Refresh**e, em seguida, selecione a tabela **de rota de rota firewall.**
10. Selecione **Subnets** e, em seguida, selecione **Associate**.
11. Selecione **rede** > virtual**Teste-FW-VN**.
12. Para **sub-rede,** selecione **Workload-SN**. Certifique-se de que seleciona apenas a subnet **Workload-SN** para esta rota, caso contrário a sua firewall não funcionará corretamente.

13. Selecione **OK**.
14. Selecione **Rotas** e, em seguida, selecione **Adicionar**.
15. Para **o nome Rota,** digite **fw-dg.**
16. Em **Prefixo de endereço**, escreva **0.0.0.0/0**.
17. Em **Tipo de salto seguinte**, selecione **Aplicação virtual**.

    O Azure Firewall é, de facto, um serviço gerido, mas a aplicação virtual funciona nesta situação.
18. Em **Endereço do próximo salto**, escreva o endereço IP privado para a firewall, que anotou anteriormente.
19. Selecione **OK**.

## <a name="configure-an-application-rule"></a>Configurar uma regra de aplicação

Esta é a regra da aplicação que permite o acesso de saída à www.google.com.

1. Abra o **Test-FW-RG**e selecione a firewall **Test-FW01.**
2. Na página **Test-FW01,** em **Definições,** selecione **Regras**.
3. Selecione o separador de recolha de **regras da aplicação.**
4. Selecione Adicionar a recolha de regras de **aplicação**.
5. Em **Nome**, escreva **App-Coll01**.
6. Em **Prioridade**, escreva **200**.
7. Em **Ação**, selecione **Permitir**.
8. De acordo com **as Regras**, **Target FQDNs,** para **Nome**, tipo **Allow-Google**.
9. Para **o tipo Fonte,** selecione **endereço IP**.
10. Para **origem**, tipo **10.0.2.0/24**.
11. Em **Protocolo:porta**, escreva **http, https**.
12. Para **FQDNS-alvo,** escreva **www.google.com**
13. Selecione **Adicionar**.

O Azure Firewall inclui uma coleção de regras incorporadas para os FQDNs de infraestrutura que são permitidos por predefinição. Estes FQDNs são específicos da plataforma e não podem ser utilizados para outros fins. Para obter mais informações, veja [FQDNs de Infraestrutura](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Configurar uma regra de rede

Esta é a regra de rede que permite acesso de saída aos dois endereços IP na porta 53 (DNS).

1. Selecione o separador de recolha de **regras da rede.**
2. Selecione **Adicionar a recolha**de regras de rede .
3. Em **Nome**, escreva **Net-Coll01**.
4. Em **Prioridade**, escreva **200**.
5. Em **Ação**, selecione **Permitir**.
6. De acordo com **as Regras,** **endereços IP,** para **Nome,** tipo **Desminos de Adcção .**
7. Em **Protocolo**, selecione **UDP**.
9. Para **o tipo Fonte,** selecione **endereço IP**.
1. Para **origem**, tipo **10.0.2.0/24**.
2. Para **endereço destino**, tipo **209.244.0.3.209.244.0.4**

   Estes são servidores Públicos DNS operados pela CenturyLink.
1. Em **Portas de Destino**, escreva **53**.
2. Selecione **Adicionar**.

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Alterar o endereço DNS primário e secundário para a interface de rede **Srv-Work**

Para efeitos de teste neste tutorial, configure os endereços DNS primários e secundários do servidor. Isto não é um requisito geral da Firewall Azure.

1. No menu do portal Azure, selecione **grupos de Recursos** ou procure e selecione *grupos de Recursos* a partir de qualquer página. Selecione o grupo de recursos **Test-FW-RG.**
2. Selecione a interface de rede para a máquina virtual **Srv-Work.**
3. Em **Definições,** selecione **servidores DNS**.
4. Sob **os servidores DNS,** selecione **Custom**.
5. Escreva **209.244.0.3** na caixa de texto **Adicionar servidor DNS** e **209.244.0.4** na caixa de texto seguinte.
6. Selecione **Guardar**.
7. Reinicie a máquina virtual **Srv-Work**.

## <a name="test-the-firewall"></a>Testar a firewall

Agora, teste a firewall para confirmar que funciona como esperado.

1. No portal do Azure, reveja as definições de rede para a máquina virtual **Srv-Work** e anote o endereço IP privado.
2. Ligue um ambiente de trabalho remoto à máquina virtual **Srv-Jump** e inscreva-se. A partir daí, abra uma ligação remota para o endereço IP privado **Srv-Work.**
3. Abra o Internet Explorer e navegue até https://www.google.com.
4. Selecione **OK** > **Close** nos alertas de segurança do Internet Explorer.

   Devia ver a página inicial do Google.

5. Navegue para https://www.microsoft.com.

   Deve estar bloqueado pela firewall.

Então agora verificou que as regras da firewall estão funcionando:

* Pode navegar para o único FQDN permitido, mas não para quaisquer outros.
* Pode resolver nomes DNS com o servidor DNS externo configurado.

## <a name="clean-up-resources"></a>Limpar recursos

Pode manter os recursos da firewall para o próximo tutorial. Se já não precisar dos mesmos elimine o grupo de recursos **Test-FW-RG** para eliminar todos os recursos relacionados com a firewall.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: monitorizar registos do Azure Firewall](./tutorial-diagnostics.md)
