---
title: 'Tutorial: Crie um registo de pseudónimo sintetismo azure para se referir a um endereço IP público do Azure'
description: Este tutorial mostra como configurar um registo de alias do DNS do Azure para fazer referência a um endereço IP público do Azure.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: rohink
ms.openlocfilehash: d3017d09e94040d16950598dad360fe32930c16b
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985444"
---
# <a name="tutorial-configure-an-alias-record-to-refer-to-an-azure-public-ip-address"></a>Tutorial: Configurar um registo de alias para fazer referência a um endereço IP público do Azure 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma infraestrutura de rede.
> * Crie uma máquina virtual de servidor web com um IP público.
> * Crie um registo de pseudónimos que aponte para o IP público.
> * Testar o registo de alias.


Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
Deve ter um nome de domínio disponível que possa alojar no DNS do Azure para testar. Deve ter controlo total sobre este domínio. O controlo total inclui a capacidade de definir os registos do servidor de nomes (NS) do domínio.

Para obter instruções sobre como alojar o seu domínio no DNS do Azure, veja [Tutorial: Alojar o seu domínio no DNS do Azure](dns-delegate-domain-azure-dns.md).

O domínio de exemplo utilizado para este tutorial é o contoso.com, mas utilize o seu próprio nome de domínio.

## <a name="create-the-network-infrastructure"></a>Criar a infraestrutura de rede
Primeiro, crie uma rede virtual e uma sub-rede onde possa colocar os servidores Web.
1. Inscreva-se no portal [https://portal.azure.com](https://portal.azure.com)Azure em .
2. No canto superior esquerdo do portal, selecione **Criar um recurso**. Introduza *grupo de recursos* na caixa de pesquisa e crie um grupo de recursos chamado **RG-DNS-Alias-pip**.
3. Selecione Criar uma**rede virtual**de**rede** >  **de recursos** > .
4. Criar uma rede virtual denominada **VNet-Server**. Coloque-o no grupo de recursos **RG-DNS-Alias-pip** e atribua à sub-rede o nome **SN-Web**.

## <a name="create-a-web-server-virtual-machine"></a>Criar uma máquina virtual do servidor Web
1. Selecione **Criar um recurso** > **Windows Server 2016 VM**.
2. Introduza **Web-01** para o nome e coloque a VM no grupo de recursos **RG-DNS-Alias-TM**. Introduza um nome de utilizador e palavra-passe e selecione **OK**.
3. Para **Tamanho**, selecione um SKU com 8 GB de RAM.
4. Em **Definições**, selecione a rede virtual **VNet-Servers** e a sub-rede **SN-Web**. Para as portas de entrada públicas, selecione **HTTP** > **HTTPS** > **RDP (3389)** e, em seguida, selecione **OK**.
5. Na página **Resumo**, selecione **Criar**.

Este procedimento demora alguns minutos a concluir. A máquina virtual terá um NIC anexo, que terá um IP público dinâmico básico chamado Web-01-ip. O IP público mudará sempre que a máquina virtual for reiniciada.

### <a name="install-iis"></a>Instalar o IIS

Instale o IIS na **Web-01**.

1. Ligue-se à **Web-01**e inscreva-se.
2. No dashboard do **Gestor de Servidor**, selecione **Adicionar funções e funcionalidades**.
3. Selecione **Seguinte** três vezes. Na página **Funções de Servidor**, selecione **Servidor Web (IIS)**.
4. Selecione **Adicionar Funcionalidades** e, em seguida, selecione **Seguinte**.
5. Selecione **Seguinte** quatro e, em seguida, selecione **Instalar**. Este procedimento demora alguns minutos a concluir.
6. Após a instalação terminar, selecione **Fechar**.
7. Abra um browser. Navegue até **localhost** para verificar se é apresentada a página Web predefinida do IIS.

## <a name="create-an-alias-record"></a>Criar um registo de alias

Crie um registo de alias que esteja associado ao endereço IP público.

1. Selecione na sua zona DNS do Azure para abrir a zona.
2. Selecione **Conjunto de registos**.
3. Na caixa de texto **Nome**, selecione **web01**.
4. Deixe o **Tipo** como um registo **A**.
5. Selecione a caixa de verificação **Conjunto de Registos de Alias**.
6. Selecione **Escolher serviço do Azure** e, em seguida, selecione o endereço IP público **Web-01-ip**.

## <a name="test-the-alias-record"></a>Testar o registo de alias

1. No grupo de recursos **RG-DNS-Alias-pip**, selecione a máquina virtual **Web-01**. Anote o endereço IP público.
1. Num browser, navegue até ao nome de domínio completamente qualificado da máquina virtual Web01-01. Um exemplo é **web01.contoso.com**. Agora deve ver a página Web predefinida do IIS.
2. Feche o browser.
3. Pare a máquina virtual **Web-01** e, em seguida, reinicie-a.
4. Depois de reiniciar a máquina virtual, anote o novo endereço IP público da mesma.
5. Abra um browser novo. Navegue novamente até ao nome de domínio completamente qualificado da máquina virtual Web01-01. Um exemplo é **web01.contoso.com**.

Este procedimento funciona, uma vez que utilizou um registo de alias associado ao recurso do endereço IP público e não um registo A convencional.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar dos recursos criados para este tutorial, elimine o grupo de recursos **RG-DNS-Alias-pip**.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou um registo de alias para fazer referência a um endereço IP público do Azure. Para saber mais sobre o DNS do Azure e as aplicações Web, avance para o tutorial de aplicações Web.

> [!div class="nextstepaction"]
> [Criar registos DNS para uma aplicação Web num domínio personalizado](./dns-web-sites-custom-domain.md)
