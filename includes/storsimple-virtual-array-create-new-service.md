---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: alkohli
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: 4ba5c8b69776b39d8a6640744b0c24600f3a0d5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67184446"
---
#### <a name="to-create-a-new-service"></a>Para criar um novo serviço

1.  Utilizando as credenciais da sua conta Microsoft, inscreva-se no portal Azure neste URL: <https://portal.azure.com/>. Se implementar o dispositivo no portal do Governo, inscreva-se em:<https://portal.azure.us/>

2.  No portal Azure, clique **+ Crie um recurso** &gt; **Armazenamento** &gt; **Série Virtual StorSimple**.

    ![Criar um novo serviço](./media/storsimple-virtual-array-create-new-service/createnewservice2.png) 

3.  Na lâmina **StorSimple Device Manager** que se abre, faça o seguinte:

    1.  Forneça um **Nome do recurso** exclusivo para o serviço. O nome do recurso é um nome amigável que pode ser usado para identificar o serviço. O nome pode ter entre 2 e 50 carateres que podem ser letras, números e hífenes. O nome tem de começar e terminar com uma letra ou um número.

    2.  Escolha uma **Subscrição** na lista pendente. A subscrição está ligada à sua conta de faturação. Este campo não estará presente se tiver apenas uma subscrição.

    3.  Para **o grupo Recursos,** selecione um grupo existente ou crie um novo grupo. Para obter mais informações, veja [Azure resource groups](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/) (Grupos de recursos do Azure).

    4.  Forneça uma **Localização** para o serviço. Consulte as [Regiões Azure](https://azure.microsoft.com/regions/#services) para obter mais informações sobre quais os serviços disponíveis em que região. Em geral, escolha um **Local** mais próximo da região geográfica onde pretende implementar o seu dispositivo. Pode também querer ter em conta o seguinte:

        -   Se tiver cargas de trabalho existentes em Azure que também pretende implementar com o seu dispositivo StorSimple, recomendamos que utilize esse datacenter.

        -   O seu Gestor de Dispositivos StorSimple e o armazenamento Azure podem estar em dois locais distintos. Se for esse o caso, terá de criar separadamente a conta do Gestor de Dispositivos do StorSimple e a conta de armazenamento do Azure. Para criar uma conta de armazenamento Azure, navegue até ao Armazenamento Azure no portal Azure e siga os passos descritos na [Create a storage account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). Uma vez criada esta conta, adicione-a ao Gestor de Dispositivos do StorSimple seguindo os passos em [Configure a new storage account for the service](https://azure.microsoft.com/documentation/articles/storsimple-deployment-walkthrough/#configure-a-new-storage-account-for-the-service) (Configurar uma nova conta de armazenamento para o serviço).

        -   Se implementar o dispositivo virtual no Portal do Governo, o serviço StorSimple Device Manager está disponível nas localizações dos EUA Iowa e Virgínia dos EUA.

    5.  Selecione Criar uma nova conta de **armazenamento Azure** para criar automaticamente uma conta de armazenamento com o serviço. Especifique um nome de **conta de armazenamento**. Se precisar de ter os seus dados numa localização diferente, desmarque esta caixa.

    6.  Assinale **Afixar ao dashboard** se pretender uma ligação rápida para este serviço no seu dashboard.

    7.  Clique em **Criar** para criar o Gestor de Dispositivos do StorSimple.

        ![Criar um novo serviço](./media/storsimple-virtual-array-create-new-service/createnewservice4.png)  

Está direcionado para a página de aterragem do **Serviço.** A criação do serviço demora alguns minutos. Após criar o serviço com êxito, será devidamente notificado e o estado do serviço será alterado para **Ativo**.


