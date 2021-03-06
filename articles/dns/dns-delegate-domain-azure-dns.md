---
title: 'Tutorial: Acolhe o seu domínio e subdomínio - Azure DNS'
description: Neste artigo, aprenda a configurar o Azure DNS para acolher as suas zonas de DNS.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: rohink
ms.openlocfilehash: 8f29a2bbe0eb392927dd111b13e2260111ddd18e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238973"
---
# <a name="tutorial-host-your-domain-in-azure-dns"></a>Tutorial: Alojar o seu domínio no DNS do Azure

Pode utilizar o DNS do Azure para alojar o seu domínio DNS e gerir os registos DNS. Ao alojar os seus domínios no Azure, pode gerir os recursos DNS com as mesmas credenciais, APIs, ferramentas e faturação dos seus outros serviços do Azure.

Suponha que compra o domínio contoso.net a partir de uma entidade de registo de domínios e cria uma zona com o nome contoso.net no DNS do Azure. Na qualidade de proprietário do domínio, a sua entidade de registo de domínios oferece-lhe a opção de configurar os registos do servidor de nomes (NS) para o seu domínio. A entidade de registo de domínios armazena os registos NS na zona principal .net. Os utilizadores de Internet em todo o mundo são então direcionados para o seu domínio na sua zona DeNs Azure quando tentam resolver os registos de DNS em contoso.net.


Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma zona DNS.
> * Recupere uma lista de servidores de nomes.
> * Delegar o domínio.
> * Verifique se a delegação está a funcionar.


Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Deve ter um nome de domínio disponível para testar com o mesmo que pode hospedar no Azure DNS . Deve ter controlo total sobre este domínio. O controlo total inclui a capacidade de definir os registos do servidor de nomes (NS) do domínio.

O domínio de exemplo usado para este tutorial é contoso.net, mas use o seu próprio nome de domínio.

## <a name="create-a-dns-zone"></a>Criar uma zona DNS

1. Vá ao [portal Azure](https://portal.azure.com/) para criar uma zona DNS. Procure e selecione **zonas DNS**.

   ![Zona DNS](./media/dns-delegate-domain-azure-dns/openzone650.png)

1. Selecione **Criar a zona DNS**.
1. Na página **Criar zona DNS**, introduza os valores seguintes e, em seguida, selecione **Criar**:

   | **Definição** | **Valor** | **Detalhes** |
   |---|---|---|
   |**Nome**|[o seu nome de domínio] |O nome de domínio que comprou. Este tutorial utiliza contoso.net como exemplo.|
   |**Assinatura**|[A sua subscrição]|Selecione uma subscrição na que vai criar a zona.|
   |**Grupo de recursos**|**Criar novo:** contosoRG|Crie um grupo de recursos. O nome do grupo de recursos tem de ser exclusivo dentro da subscrição que selecionou.<br>A localização do grupo de recursos não tem qualquer impacto na zona DNS. A localização da zona DNS é sempre "global", e não é mostrada.|
   |**Localização**|E.U.A. Leste||

## <a name="retrieve-name-servers"></a>Obter servidores de nomes

Antes de pode delegar a zona DNS ao DNS do Azure, terá de conhecer os servidores de nomes da sua zona. O DNS do Azure aloca servidores de nomes a partir de um conjunto sempre que é criada uma zona.

1. Com a zona DNS criada, no painel **Favoritos** do portal do Azure, selecione **Todos os recursos**. Na página **Todos os recursos**, selecione a zona de DNS. Se a subscrição que selecionou já tiver vários recursos, pode introduzir o seu nome de domínio na caixa **de nome Filter** para aceder facilmente ao gateway da aplicação. 

1. Obtenha os servidores de nome na página da zona DNS. Neste exemplo, a zona contoso.net foi atribuído aos servidores *de nomes ns1-01.azure-dns.com,* *ns2-01.azure-dns.net,* *ns3-01.azure-dns.org*e *ns4-01.azure-dns.info:*

   ![Lista dos servidores de nomes](./media/dns-delegate-domain-azure-dns/viewzonens500.png)

O DNS do Azure cria automaticamente registos NS autoritativos na sua zona para os servidores de nomes atribuídos.

## <a name="delegate-the-domain"></a>Delegar o domínio

Agora que a zona DNS está criada e que já tem os servidores de nomes, tem de atualizar o domínio principal com os servidores de nomes DNS do Azure. Cada entidade de registo tem as suas próprias ferramentas de gestão de DNS para alterar os registos do servidor de nomes de um domínio. 

1. Na página de gestão do DNS da entidade de registo, edite os registos NS e substitua-os pelos servidores de nomes do DNS do Azure.

1. Quando delega um domínio no Azure DNS, deve utilizar os servidores de nome que o Azure DNS fornece. Utilize os quatro servidores de nome, independentemente do nome do seu domínio. A delegação de domínio não requer um servidor de nome para usar o mesmo domínio de nível superior que o seu domínio.

> [!NOTE]
> Quando copiar cada endereço do servidor de nomes, certifique-se de que copia o ponto à direita no fim do endereço. O ponto à direita indica o fim de um nome de domínio completamente qualificado. Alguns registradores aterminam o período se o nome NS não o tiver no final. Para estar em conformidade com o DNS RFC, inclua o período de trailing.

As delegações que usam servidores de nome na sua própria zona, por *vezes chamados servidores de nome saque,* não são atualmente suportadas em DNS Azure.

## <a name="verify-the-delegation"></a>Verifique a delegação

Depois de completar a delegação, pode verificar se está a funcionar utilizando uma ferramenta como a *nslookup* para consultar o registo de Início de Autoridade (SOA) para a sua zona. O registo SOA é criado automaticamente quando a zona é criada. Pode ter de esperar 10 minutos ou mais depois de completar a delegação, antes de poder verificar com sucesso que está a funcionar. Pode demorar algum tempo para as alterações serem propagadas em todo o sistema DNS.

Não tem de especificar os servidores de nome SN Do Azure. Se a delegação estiver configurada corretamente, o processo de resolução DNS normal localiza os servidores de nomes automaticamente.

1. A partir de um pedido de comando, introduza um comando nslookup semelhante ao seguinte exemplo:

   ```
   nslookup -type=SOA contoso.net
   ```

1. Verifique se a sua resposta é semelhante à seguinte saída de nslookup:

   ```
   Server: ns1-04.azure-dns.com
   Address: 208.76.47.4

   contoso.net
   primary name server = ns1-04.azure-dns.com
   responsible mail addr = msnhst.microsoft.com
   serial = 1
   refresh = 900 (15 mins)
   retry = 300 (5 mins)
   expire = 604800 (7 days)
   default TTL = 300 (5 mins)
   ```

## <a name="clean-up-resources"></a>Limpar recursos

Pode manter o grupo de recursos **contosoRG** se pretende acompanhar o tutorial seguinte. Caso contrário, elimine o grupo de recursos **contosoRG** para eliminar os recursos criados neste tutorial.

- Selecione o grupo de recursos **contosoRG** e, em seguida, **selecione Eliminar o grupo de recursos**. 

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma zona DNS para o seu domínio e delegou-a para o Azure DNS. Para saber mais sobre o DNS do Azure e as aplicações Web, avance para o tutorial de aplicações Web.

> [!div class="nextstepaction"]
> [Criar registos DNS para uma aplicação Web num domínio personalizado](./dns-web-sites-custom-domain.md)