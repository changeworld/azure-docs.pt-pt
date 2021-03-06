---
title: VMs azure alta disponibilidade para SAP NetWeaver em SLES Microsoft Docs
description: Guia de alta disponibilidade para SAP NetWeaver no SUSE Linux Enterprise Server para aplicações SAP
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/26/2020
ms.author: radeltch
ms.openlocfilehash: 05effb7d2e64c5f27acabad4b086ba27d6849cc8
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348825"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-for-sap-applications"></a>Alta disponibilidade para SAP NetWeaver em VMs Azure no SUSE Linux Enterprise Server para aplicações SAP

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

Este artigo descreve como implementar as máquinas virtuais, configurar as máquinas virtuais, instalar a estrutura de cluster e instalar um sistema SAP NetWeaver 7.50 altamente disponível.
Nas configurações de exemplo, comandos de instalação etc. É utilizado o número 00 da instância ASCS, a instância ERS 02 e o Sistema SAP ID NW1. Os nomes dos recursos (por exemplo, máquinas virtuais, redes virtuais) no exemplo assumem que você usou o [modelo convergente][template-converged] com o sistema SAP ID NW1 para criar os recursos.

Leia as seguintes Notas e papéis SAP primeiro

* Nota SAP [1928533,][1928533]que tem:
  * Lista de tamanhos De VM Azure que são suportados para a implementação de software SAP
  * Informações importantes sobre a capacidade para tamanhos de VM Azure
  * Software SAP suportado e sistema operativo (OS) e combinações de bases de dados
  * Versão necessária do kernel SAP para Windows e Linux no Microsoft Azure

* O SAP Note [2015553][2015553] lista os pré-requisitos para implementações de software SAP suportadas pela SAP em Azure.
* SAP Nota [2205917][2205917] recomendou definições de OS para SUSE Linux Enterprise Server para Aplicações SAP
* SAP Nota [1944799][1944799] tem Diretrizes SAP HANA para SUSE Linux Enterprise Server para Aplicações SAP
* O SAP Note [2178632][2178632] tem informações detalhadas sobre todas as métricas de monitorização reportadas para o SAP em Azure.
* O SAP Note [2191498][2191498] tem a versão necessária do Agente anfitrião SAP para o Linux em Azure.
* SAP Nota [2243692][2243692] tem informações sobre licenciamento SAP em Linux em Azure.
* SAP Note [1984787][1984787] tem informações gerais sobre o SUSE Linux Enterprise Server 12.
* A Nota [SAP 1999351][1999351] tem informações adicionais de resolução de problemas para a extensão de monitorização avançada do Azure para sAP.
* [A SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) exigiu todas as notas SAP para linux.
* [Planeamento e implementação de Máquinas Virtuais Azure para SAP em Linux][planning-guide]
* [Implantação de Máquinas Virtuais Azure para SAP em Linux][deployment-guide]
* [Implantação de DBMS de Máquinas Virtuais Azure para SAP em Linux][dbms-guide]
* [Guias de boas práticas SUSE SAP HA][suse-ha-guide] Os guias contêm todas as informações necessárias para configurar a Replicação do Sistema Netweaver HA e SAP HANA no local. Utilize estes guias como base geral. Fornecem informações muito mais detalhadas.
* [Extensão de alta disponibilidade sUSE 12 Notas de lançamento SP3][suse-ha-12sp3-relnotes]

## <a name="overview"></a>Descrição geral

Para obter uma elevada disponibilidade, o SAP NetWeaver requer um servidor NFS. O servidor NFS está configurado num cluster separado e pode ser utilizado por vários sistemas SAP.

![Visão geral de alta disponibilidade da SAP NetWeaver](./media/high-availability-guide-suse/ha-suse.png)

O servidor NFS, SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS e a base de dados SAP HANA utilizam o nome de anfitrião virtual e os endereços IP virtuais. No Azure, é necessário utilizar um endereço IP virtual. Recomendamos a utilização do [equilíbrio de carga Standard](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal). A lista seguinte mostra a configuração do (A)SCS e do equilibrador de carga ERS.

### <a name="ascs"></a>A SCS

* Configuração frontend
  * Endereço IP 10.0.0.7
* Porta sonda
  * Porto 620<strong>&lt;nr&gt;</strong>
* Regras de equilíbrio de carga
  * Se utilizar o Balancer de Carga Padrão, selecione **portas HA**
  * Se utilizar o Equilíbrio de Carga Básico, crie regras de equilíbrio de carga para as seguintes portas
    * 32<strong>&lt;nr&gt; </strong> TCP
    * 36<strong>&lt;nr&gt; </strong> TCP
    * 39<strong>&lt;nr&gt; </strong> TCP
    * 81<strong>&lt;nr&gt; </strong> TCP
    * 5<strong>&lt;nr&gt;</strong>13 TCP
    * 5<strong>&lt;nr&gt;</strong>14 TCP
    * 5<strong>&lt;nr&gt;</strong>16 TCP

### <a name="ers"></a>ERS

* Configuração frontend
  * Endereço IP 10.0.0.8
* Porta sonda
  * Porta 621<strong>&lt;nr&gt;</strong>
* Regras de equilíbrio de carga
  * Se utilizar o Balancer de Carga Padrão, selecione **portas HA**
  * Se utilizar o Equilíbrio de Carga Básico, crie regras de equilíbrio de carga para as seguintes portas
    * 32<strong>&lt;nr&gt; </strong> TCP
    * 33<strong>&lt;nr&gt; </strong> TCP
    * 5<strong>&lt;nr&gt;</strong>13 TCP
    * 5<strong>&lt;nr&gt;</strong>14 TCP
    * 5<strong>&lt;nr&gt;</strong>16 TCP

* Configuração de backend
  * Ligado às interfaces de rede primárias de todas as máquinas virtuais que devem fazer parte do cluster (A)SCS/ERS


## <a name="setting-up-a-highly-available-nfs-server"></a>Configuração de um servidor NFS altamente disponível

A SAP NetWeaver requer armazenamento partilhado para o diretório de transporte e perfil. Leia [alta disponibilidade para NFS em VMs Azure no SUSE Linux Enterprise Server][nfs-ha] sobre como configurar um servidor NFS para SAP NetWeaver.

## <a name="setting-up-ascs"></a>Configuração (A)SCS

Pode utilizar um modelo Azure do GitHub para implementar todos os recursos necessários do Azure, incluindo as máquinas virtuais, o conjunto de disponibilidade e o equilíbrio de carga ou pode implementar os recursos manualmente.

### <a name="deploy-linux-via-azure-template"></a>Implementar linux via modelo Azure

O Azure Marketplace contém uma imagem para o SUSE Linux Enterprise Server para aplicações SAP 12 que pode utilizar para implementar novas máquinas virtuais. A imagem de mercado contém o agente de recursos para o SAP NetWeaver.

Você pode usar um dos modelos de arranque rápido no GitHub para implementar todos os recursos necessários. O modelo implanta as máquinas virtuais, o equilibrador de carga, conjunto de disponibilidade, etc. Siga estes passos para implementar o modelo:

1. Abra o [modelo ASCS/SCS Multi SID][template-multisid-xscs] ou o modelo [convergente][template-converged] no portal Azure. 
   O modelo ASCS/SCS apenas cria as regras de equilíbrio de carga para as instâncias SAP NetWeaver ASCS/SCS e ERS (apenas Linux), enquanto o modelo convergente também cria as regras de equilíbrio de carga para uma base de dados (por exemplo, Microsoft SQL Server ou SAP HANA). Se planeia instalar um sistema baseado em SAP NetWeaver e também pretende instalar a base de dados nas mesmas máquinas, utilize o [modelo convergente][template-converged].
1. Insira os seguintes parâmetros
   1. Prefixo de recursos (apenas modelo DE SID ASCS/SCS)  
      Introduza o prefixo que pretende utilizar. O valor é usado como prefixo para os recursos que são implantados.
   3. ID do sistema de seiva (apenas modelo convergente)  
      Introduza o ID do sistema SAP do sistema SAP que pretende instalar. O ID é usado como prefixo para os recursos que são implantados.
   4. Tipo de pilha  
      Selecione o tipo de pilha SAP NetWeaver
   5. Tipo Os  
      Selecione uma das distribuições linux. Para este exemplo, selecione SLES 12 BYOS
   6. Tipo Db  
      Selecione HANA
   7. Tamanho do sistema de seiva.  
      A quantidade de SAPS que o novo sistema fornece. Se não tem a certeza de quantos SAPS o sistema necessita, pergunte ao seu Parceiro de Tecnologia SAP ou Integrador de Sistemas
   8. Disponibilidade do Sistema  
      Selecione HA
   9. Nome de utilizador e senha de administrador  
      É criado um novo utilizador que pode ser utilizado para iniciar sessão na máquina.
   10. Id da sub-rede  
   Se pretender implantar o VM numa VNet existente onde tem uma sub-rede definida a VM deve ser atribuída, diga o nome da identificação dessa sub-rede específica. O ID geralmente parece /subscrições/**&lt;ID&gt;de subscrição**/recursosGroups/**&lt;nome&gt;** de grupo de recursos /fornecedores/Microsoft.Network/virtualNetworks/**&lt;virtual network name&gt;**/subnets/**&lt;subnet name&gt; **

### <a name="deploy-linux-manually-via-azure-portal"></a>Implementar o Linux manualmente através do portal Azure

Primeiro é necessário criar as máquinas virtuais para este cluster NFS. Depois, cria-se um equilibrador de carga e utiliza as máquinas virtuais na piscina de backend.

1. Criar um Grupo de Recursos
1. Criar uma Rede Virtual
1. Criar um conjunto de disponibilidade  
   Definir domínio de atualização max
1. Criar máquina virtual 1  
   Utilize pelo menos SLES4SAP 12 SP1, neste exemplo a imagem SLES4SAP 12 SP1https://portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP1PremiumImage-ARM  
   SLES Para Aplicações SAP 12 SP1 é usado  
   Selecione Conjunto de Disponibilidade criado anteriormente  
1. Criar máquina virtual 2  
   Utilize pelo menos SLES4SAP 12 SP1, neste exemplo a imagem SLES4SAP 12 SP1https://portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP1PremiumImage-ARM  
   SLES Para Aplicações SAP 12 SP1 é usado  
   Selecione Conjunto de Disponibilidade criado anteriormente  
1. Adicione pelo menos um disco de dados a ambas as máquinas virtuais  
   Os discos de dados são utilizados para`<SAPSID` o diretório /usr/seap/>
1. Criar um equilibrador de carga (interno, standard):  
   1. Crie os endereços IP frontend
      1. Endereço IP 10.0.0.7 para o ASCS
         1. Abra o equilibrador de carga, selecione pool IP frontend e clique em Adicionar
         1. Introduza o nome do novo pool IP frontend (por exemplo **nw1-ascs-frontend)**
         1. Definir a Atribuição à Estática e introduzir o endereço IP (por exemplo **10.0.0.7**)
         1. Clique OK
      1. Endereço IP 10.0.0.8 para a ASCS ERS
         * Repita os passos acima para criar um endereço IP para o ERS (por exemplo **10.0.0.8** e **nw1-aers-backend**)
   1. Criar o conjunto de back-end
      1. Abra o equilibrador de carga, selecione piscinas de backend e clique em Adicionar
      1. Introduza o nome da nova piscina de backend (por exemplo **nw1-backend)**
      1. Clique em Adicionar uma máquina virtual.
      1. Selecione Máquina Virtual
      1. Selecione as máquinas virtuais do cluster (A)SCS e os seus endereços IP.
      1. Clique em Adicionar
   1. Criar as sondas de saúde
      1. Porta 620**00** para ASCS
         1. Abra o equilibrador de carga, selecione sondas de saúde e clique em Adicionar
         1. Introduza o nome da nova sonda de saúde (por exemplo **nw1-ascs-hp)**
         1. Selecione TCP como protocolo, porta 620**00,** mantenha intervalo 5 e limiar insalubre 2
         1. Clique OK
      1. Porto 621**02** para ASCS ERS
         * Repita os passos acima para criar uma sonda de saúde para a ERS (por exemplo 621**02** e **nw1-aers-hp**)
   1. Regras de equilíbrio de carga
      1. Regras de equilíbrio de carga para asCS
         1. Abra o equilibrador de carga, selecione regras de equilíbrio de carga e clique em Adicionar
         1. Introduza o nome da nova regra do equilibrador de carga (por **exemplo, nw1-lb-ascs)**
         1. Selecione o endereço IP frontend, o backend pool e a sonda de saúde que criou anteriormente (por **exemplo, nw1-ascs-frontend,** **nw1-backend** e **nw1-ascs-hp**)
         1. Selecione **portas HA**
         1. Aumente o tempo inativo para 30 minutos
         1. **Certifique-se de ativar o IP flutuante**
         1. Clique OK
         * Repita os passos acima para criar regras de equilíbrio de carga para a ERS (por **exemplo, nw1-lb-ers**)
1. Alternativamente, se o seu cenário requer um equilíbrio básico de carga (interno), siga estes passos:  
   1. Crie os endereços IP frontend
      1. Endereço IP 10.0.0.7 para o ASCS
         1. Abra o equilibrador de carga, selecione pool IP frontend e clique em Adicionar
         1. Introduza o nome do novo pool IP frontend (por exemplo **nw1-ascs-frontend)**
         1. Definir a Atribuição à Estática e introduzir o endereço IP (por exemplo **10.0.0.7**)
         1. Clique OK
      1. Endereço IP 10.0.0.8 para a ASCS ERS
         * Repita os passos acima para criar um endereço IP para o ERS (por exemplo **10.0.0.8** e **nw1-aers-frontend)**
   1. Criar o conjunto de back-end
      1. Abra o equilibrador de carga, selecione piscinas de backend e clique em Adicionar
      1. Introduza o nome da nova piscina de backend (por exemplo **nw1-backend)**
      1. Clique em Adicionar uma máquina virtual.
      1. Selecione o Conjunto de Disponibilidade que criou anteriormente
      1. Selecione as máquinas virtuais do cluster (A)SCS
      1. Clique OK
   1. Criar as sondas de saúde
      1. Porta 620**00** para ASCS
         1. Abra o equilibrador de carga, selecione sondas de saúde e clique em Adicionar
         1. Introduza o nome da nova sonda de saúde (por exemplo **nw1-ascs-hp)**
         1. Selecione TCP como protocolo, porta 620**00,** mantenha intervalo 5 e limiar insalubre 2
         1. Clique OK
      1. Porto 621**02** para ASCS ERS
         * Repita os passos acima para criar uma sonda de saúde para a ERS (por exemplo 621**02** e **nw1-aers-hp**)
   1. Regras de equilíbrio de carga
      1. 32**00** TCP para ASCS
         1. Abra o equilibrador de carga, selecione regras de equilíbrio de carga e clique em Adicionar
         1. Introduza o nome da nova regra do equilibrador de carga (por **exemplo, nw1-lb-3200**)
         1. Selecione o endereço IP frontend, a piscina de backend e a sonda de saúde que criou anteriormente (por **exemplo, nw1-ascs-frontend)**
         1. Manter o protocolo **TCP,** entrar na porta **3200**
         1. Aumente o tempo inativo para 30 minutos
         1. **Certifique-se de ativar o IP flutuante**
         1. Clique OK
      1. Portas adicionais para o ASCS
         * Repita os passos acima para os portos 36**00**, 39**00,** 81**00**, 5**00**13, 5**00**14, 5**00**16 e TCP para o ASCS
      1. Portas adicionais para a ASCS ERS
         * Repita os passos acima para os portos 33**02**, 5**02**13, 5**02**14, 5**02**16 e TCP para o ASCS ERS

> [!Note]
> Quando os VMs sem endereços IP públicos forem colocados no conjunto de backend do interno (sem endereço IP público) O equilíbrio de carga Standard Azure não haverá conectividade de saída na Internet, a menos que seja realizada uma configuração adicional para permitir o encaminhamento para pontos finais públicos. Para mais detalhes sobre como alcançar a conectividade de saída, consulte [a conectividade do ponto final público para máquinas virtuais utilizando o Equilíbrio de Carga Padrão Azure em cenários de alta disponibilidade SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections).  

> [!IMPORTANT]
> Não permita os carimbos de tempo da TCP em VMs Azure colocados atrás do Equilíbrio de Carga Azure. Permitir os selos temporais da TCP fará com que as sondas de saúde falhem. Definir parâmetro **net.ipv4.tcp_timestamps** a **0**. Para mais detalhes consulte as sondas de [saúde load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

### <a name="create-pacemaker-cluster"></a>Criar cluster Pacemaker

Siga os passos na configuração do [Pacemaker no SUSE Linux Enterprise Server em Azure](high-availability-guide-suse-pacemaker.md) para criar um cluster pacemaker básico para este servidor (A)SCS.

### <a name="installation"></a>Instalação

Os seguintes itens são pré-fixados com **[A]** - aplicável a todos os nós, **[1]** - apenas aplicável ao nó 1 ou **[2]** - apenas aplicável ao nó 2.

1. **[A]** Instalar conector SUSE

   <pre><code>sudo zypper install sap-suse-cluster-connector
   </code></pre>

   > [!NOTE]
   > O problema conhecido com a utilização de um traço nos nomes do anfitrião é fixado com a versão **3.1.1** do **conector de conjunto sap-suse-suse.** Certifique-se de que está a utilizar pelo menos a versão 3.1.1 do conector de aglomerado de seiva-suse,se utilizar nós de cluster com traço no nome do hospedeiro. Caso contrário, o seu agrupamento não funcionará. 

   Certifique-se de que instalou a nova versão do conector de cluster SAP SUSE. O antigo chamava-se sap_suse_cluster_connector e o novo **chama-se conector de aglomerado de sáb.**

   ```
   sudo zypper info sap-suse-cluster-connector
   
   Information for package sap-suse-cluster-connector:
   ---------------------------------------------------
   Repository     : SLE-12-SP3-SAP-Updates
   Name           : sap-suse-cluster-connector
   <b>Version        : 3.0.0-2.2</b>
   Arch           : noarch
   Vendor         : SUSE LLC <https://www.suse.com/>
   Support Level  : Level 3
   Installed Size : 41.6 KiB
   <b>Installed      : Yes</b>
   Status         : up-to-date
   Source package : sap-suse-cluster-connector-3.0.0-2.2.src
   Summary        : SUSE High Availability Setup for SAP Products
   ```

1. **[A]** Atualizar agentes de recursos SAP  
   
   Um patch para o pacote de agentes de recursos é necessário para usar a nova configuração, que é descrita neste artigo. Pode verificar se o patch já está instalado com o seguinte comando

   <pre><code>sudo grep 'parameter name="IS_ERS"' /usr/lib/ocf/resource.d/heartbeat/SAPInstance
   </code></pre>

   A saída deve ser semelhante a

   <pre><code>&lt;parameter name="IS_ERS" unique="0" required="0"&gt;
   </code></pre>

   Se o comando grep não encontrar o parâmetro IS_ERS, tem de instalar o patch listado na página de [descarregamento sUSE](https://download.suse.com/patch/finder/#bu=suse&familyId=&productId=&dateRange=&startDate=&endDate=&priority=&architecture=&keywords=resource-agents)

   <pre><code># example for patch for SLES 12 SP1
   sudo zypper in -t patch SUSE-SLE-HA-12-SP1-2017-885=1
   # example for patch for SLES 12 SP2
   sudo zypper in -t patch SUSE-SLE-HA-12-SP2-2017-886=1
   </code></pre>

1. **[A]** Configuração resolução de nome de anfitrião

   Pode utilizar um servidor DNS ou modificar os /etc/anfitriões em todos os nós. Este exemplo mostra como usar o ficheiro /etc/anfitriões.
   Substitua o endereço IP e o nome de anfitrião nos seguintes comandos

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Insira as seguintes linhas para /etc/anfitriões. Altere o endereço IP e o nome de anfitrião para combinar com o seu ambiente   

   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   </code></pre>

## <a name="prepare-for-sap-netweaver-installation"></a>Prepare-se para a instalação SAP NetWeaver

1. **[A]** Criar os diretórios partilhados

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>NW1</b>/SYS
   sudo mkdir -p /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo mkdir -p /usr/sap/<b>NW1</b>/ERS<b>02</b>
   
   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>NW1</b>/SYS
   sudo chattr +i /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo chattr +i /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>

1. **[A]** Configurar autofs

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   +auto.master
   /- /etc/auto.direct
   </code></pre>

   Criar um ficheiro com

   <pre><code>sudo vi /etc/auto.direct
   
   # Add the following lines to the file, save and exit
   /sapmnt/<b>NW1</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/trans
   /usr/sap/<b>NW1</b>/SYS -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sidsys
   </code></pre>

   Restart autofs para montar as novas ações

   <pre><code>sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[A]** Arquivo SWAP configurar

   <pre><code>sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Reiniciar o Agente para ativar a alteração

   <pre><code>sudo service waagent restart
   </code></pre>


### <a name="installing-sap-netweaver-ascsers"></a>Instalação SAP NetWeaver ASCS/ERS

1. **[1]** Criar um recurso IP virtual e uma sonda de saúde para a instância ASCS

   > [!IMPORTANT]
   > Testes recentes revelaram situações, em que o netcat deixa de responder aos pedidos devido a atrasos e à sua limitação de manuseamento apenas uma ligação. O recurso netcat para de ouvir os pedidos do balanceador de carga Azure e o IP flutuante fica indisponível.  
   > Para os aglomerados Pacemaker existentes, recomendamos no passado substituir o netcat por socat. Atualmente recomendamos a utilização de um agente de recursos azure-lb, que faz parte dos agentes de recursos do pacote, com os seguintes requisitos de versão de pacote:
   > - Para o SLES 12 SP4/SP5, a versão deve ser pelo menos agentes de recursos-4.3.018.a7fb5035-3.30.1.  
   > - Para o SLES 15/15 SP1, a versão deve ser pelo menos agentes de recursos-4.3.0184.6ee15eb2-4.13.1.  
   >
   > Note que a alteração exigirá um breve tempo de inatividade.  
   > Para os clusters Pacemaker existentes, se a configuração já foi alterada para utilizar o socat conforme descrito no Endurecimento de Deteção de Equilíbrio [de Carga azure,](https://www.suse.com/support/kb/doc/?id=7024128)não há necessidade de mudar imediatamente para o agente de recursos azure-lb.

   <pre><code>sudo crm node standby <b>nw1-cl-1</b>
   
   sudo crm configure primitive fs_<b>NW1</b>_ASCS Filesystem device='<b>nw1-nfs</b>:/<b>NW1</b>/ASCS' directory='/usr/sap/<b>NW1</b>/ASCS<b>00</b>' fstype='nfs4' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>NW1</b>_ASCS IPaddr2 \
     params ip=<b>10.0.0.7</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_ASCS azure-lb port=620<b>00</b>
   
   sudo crm configure group g-<b>NW1</b>_ASCS fs_<b>NW1</b>_ASCS nc_<b>NW1</b>_ASCS vip_<b>NW1</b>_ASCS \
      meta resource-stickiness=3000
   </code></pre>

   Certifique-se de que o estado do cluster está ok e que todos os recursos são iniciados. Não é importante em que nó dos recursos estão a funcionar.

   <pre><code>sudo crm_mon -r
   
   # Node nw1-cl-1: standby
   # <b>Online: [ nw1-cl-0 ]</b>
   # 
   # Full list of resources:
   # 
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-0</b>
   #  Resource Group: g-NW1_ASCS
   #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-0</b>
   #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-0</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-0</b>
   </code></pre>

1. **[1]** Instalar asCS SAP NetWeaver  

   Instale o SAP NetWeaver ASCS como raiz no primeiro nó utilizando um nome de anfitrião virtual que mapeie para o endereço IP da configuração frontal do equilíbrio de carga para o ASCS, por exemplo <b>nw1-ascs</b>, <b>10.0.0.7</b> e o número de instância que usou para a sonda do equilibrador de carga, por exemplo <b>00</b>.

   Pode utilizar o parâmetro sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um utilizador sem raízes se coneca ao sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Se a instalação não criar uma subpasta em /usr/seap/**NW1**/ASCS**00,** tente configurar o proprietário e o grupo da pasta ASCS**00** e voltar a tentar.

   <pre><code>chown nw1adm /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   chgrp sapsys /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   </code></pre>

1. **[1]** Criar um recurso IP virtual e uma sonda de saúde para a instância DA

   <pre><code>sudo crm node online <b>nw1-cl-1</b>
   sudo crm node standby <b>nw1-cl-0</b>
   
   sudo crm configure primitive fs_<b>NW1</b>_ERS Filesystem device='<b>nw1-nfs</b>:/<b>NW1</b>/ASCSERS' directory='/usr/sap/<b>NW1</b>/ERS<b>02</b>' fstype='nfs4' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>NW1</b>_ERS IPaddr2 \
     params ip=<b>10.0.0.8</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_ERS azure-lb port=621<b>02</b>
   
   sudo crm configure group g-<b>NW1</b>_ERS fs_<b>NW1</b>_ERS nc_<b>NW1</b>_ERS vip_<b>NW1</b>_ERS
   </code></pre>

   Certifique-se de que o estado do cluster está ok e que todos os recursos são iniciados. Não é importante em que nó dos recursos estão a funcionar.

   <pre><code>sudo crm_mon -r
   
   # Node <b>nw1-cl-0: standby</b>
   # <b>Online: [ nw1-cl-1 ]</b>
   # 
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ASCS
   #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-1</b>
   #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ERS
   #      fs_NW1_ERS (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-1</b>
   #      nc_NW1_ERS (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   </code></pre>

1. **[2]** Instalar SAP NetWeaver ERS

   Instale o SAP NetWeaver ERS como raiz no segundo nó utilizando um nome de anfitrião virtual que mapeie para o endereço IP da configuração frontal do equilíbrio de carga para o ERS, por exemplo <b>nw1-aers</b>, <b>10.0.0.8</b> e o número de exemplo que usou para a sonda do equilibrador de carga, por exemplo <b>02</b>.

   Pode utilizar o parâmetro sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um utilizador sem raízes se coneca ao sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   > [!NOTE]
   > Utilize SWPM SP 20 PL 05 ou superior. Versões inferiores não fixam corretamente as permissões e a instalação falhará.

   Se a instalação não criar uma subpasta em /usr/seiva/**NW1/ERS****02**, tente configurar o proprietário e o grupo da pasta ERS**02** e voltar a tentar.

   <pre><code>chown nw1adm /usr/sap/<b>NW1</b>/ERS<b>02</b>
   chgrp sapsys /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>


1. **[1]** Adaptar os perfis de instância ASCS/SCS e ERS
 
   * Perfil ASCS/SCS

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_<b>ASCS00</b>_<b>nw1-ascs</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   </code></pre>

   * Perfil ERS

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>

1. **[A]** Configure Manter Vivo

   A comunicação entre o servidor de aplicação SAP NetWeaver e o ASCS/SCS é encaminhada através de um equilíbriode carga de software. O equilibrador de carga desliga ligações inativas após um tempo de tempo configurável. Para evitar isto, é necessário definir um parâmetro no perfil SAP NetWeaver ASCS/SCS e alterar as definições do sistema Linux. Leia [a Nota SAP 1410736][1410736] para mais informações.

   O parâmetro de perfil ASCS/SCS enque/ncni/set_so_keepalive já foi adicionado no último passo.

   <pre><code># Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   </code></pre>

1. **[A]** Configurar os utilizadores do SAP após a instalação

   <pre><code># Add sidadm to the haclient group
   sudo usermod -aG haclient <b>nw1</b>adm
   </code></pre>

1. **[1]** Adicione os serviços ASCS e ERS SAP ao ficheiro sapservice

   Adicione a entrada de serviço ASCS ao segundo nó e copie a entrada de serviço ERS no primeiro nó.

   <pre><code>cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>nw1-cl-1</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>nw1-cl-1</b> "cat /usr/sap/sapservices" | grep ERS<b>02</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

1. **[1]** Criar os recursos de cluster SAP

Se utilizar a arquitetura enqueue server 1 (ENSA1), defina os recursos da seguinte forma:

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ERS<b>02</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>" AUTOMATIC_RECOVER=false IS_ERS=true \
    meta priority=1000
   
   sudo crm configure modgroup g-<b>NW1</b>_ASCS add rsc_sap_<b>NW1</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>NW1</b>_ERS add rsc_sap_<b>NW1</b>_ERS<b>02</b>
   
   sudo crm configure colocation col_sap_<b>NW1</b>_no_both -5000: g-<b>NW1</b>_ERS g-<b>NW1</b>_ASCS
   sudo crm configure location loc_sap_<b>NW1</b>_failover_to_ers rsc_sap_<b>NW1</b>_ASCS<b>00</b> rule 2000: runs_ers_<b>NW1</b> eq 1
   sudo crm configure order ord_sap_<b>NW1</b>_first_start_ascs Optional: rsc_sap_<b>NW1</b>_ASCS<b>00</b>:start rsc_sap_<b>NW1</b>_ERS<b>02</b>:stop symmetrical=false
   
   sudo crm node online <b>nw1-cl-0</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

  O SAP introduziu suporte para o servidor de fila 2, incluindo replicação, a partir de SAP NW 7.52. A partir da Plataforma ABAP 1809, o servidor enfila 2 é instalado por padrão. Consulte a nota SAP [2630416](https://launchpad.support.sap.com/#/notes/2630416) para obter suporte ao servidor 2.
Se utilizar a arquitetura enqueue server 2[(ENSA2),](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)defina os recursos da seguinte forma:

<pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ERS<b>02</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>" AUTOMATIC_RECOVER=false IS_ERS=true 
   
   sudo crm configure modgroup g-<b>NW1</b>_ASCS add rsc_sap_<b>NW1</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>NW1</b>_ERS add rsc_sap_<b>NW1</b>_ERS<b>02</b>
   
   sudo crm configure colocation col_sap_<b>NW1</b>_no_both -5000: g-<b>NW1</b>_ERS g-<b>NW1</b>_ASCS
   sudo crm configure order ord_sap_<b>NW1</b>_first_start_ascs Optional: rsc_sap_<b>NW1</b>_ASCS<b>00</b>:start rsc_sap_<b>NW1</b>_ERS<b>02</b>:stop symmetrical=false
   
   sudo crm node online <b>nw1-cl-0</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

  Se estiver a atualizar a partir de uma versão mais antiga e a mudar para o servidor de fila 2, consulte a nota [SAP 2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   Certifique-se de que o estado do cluster está ok e que todos os recursos são iniciados. Não é importante em que nó dos recursos estão a funcionar.


   <pre><code>sudo crm_mon -r
   
   # Online: <b>[ nw1-cl-0 nw1-cl-1 ]</b>
   #
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ASCS
   #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-1</b>
   #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   #      rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ERS
   #      fs_NW1_ERS (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-0</b>
   #      nc_NW1_ERS (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-0</b>
   #      vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-0</b>
   #      rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   <b>Started nw1-cl-0</b>
   </code></pre>

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Preparação do servidor de aplicações SAP NetWeaver

Algumas bases de dados requerem que a instalação da instância de base de dados seja executada num servidor de aplicações. Prepare as máquinas virtuais do servidor de aplicação para poder utilizá-las nestes casos.

Os passos assumem que instala o servidor de aplicação num servidor diferente dos servidores ASCS/SCS e HANA. Caso contrário, alguns dos passos abaixo (como configurar a resolução de nome sinuoso) não são necessários.

1. Configurar o sistema operativo

   Reduza o tamanho da cache suja. Para mais informações, consulte [o desempenho de Baixa escrita nos servidores SLES 11/12 com RAM grande](https://www.suse.com/support/kb/doc/?id=7010287).

   <pre><code>sudo vi /etc/sysctl.conf

   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. Configurar resolução de nome de anfitrião

   Pode utilizar um servidor DNS ou modificar os /etc/anfitriões em todos os nós. Este exemplo mostra como usar o ficheiro /etc/anfitriões.
   Substitua o endereço IP e o nome de anfitrião nos seguintes comandos

   ```bash
   sudo vi /etc/hosts
   ```

   Insira as seguintes linhas para /etc/anfitriões. Altere o endereço IP e o nome de anfitrião para combinar com o seu ambiente

   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS/SCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   # IP address of all application servers
   <b>10.0.0.20 nw1-di-0</b>
   <b>10.0.0.21 nw1-di-1</b>
   </code></pre>

1. Crie o diretório sapmnt

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. Configure autofs

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   +auto.master
   /- /etc/auto.direct
   </code></pre>

   Criar um novo arquivo com

   <pre><code>sudo vi /etc/auto.direct
   
   # Add the following lines to the file, save and exit
   /sapmnt/<b>NW1</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/trans
   </code></pre>

   Restart autofs para montar as novas ações

   <pre><code>sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. Configure ficheiro SWAP

   <pre><code>sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Reiniciar o Agente para ativar a alteração

   <pre><code>sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>Instalar base de dados

Neste exemplo, o SAP NetWeaver está instalado no SAP HANA. Pode utilizar todas as bases de dados suportadas para esta instalação. Para obter mais informações sobre como instalar o SAP HANA em Azure, consulte [A Alta Disponibilidade de SAP HANA em Máquinas Virtuais Azure (VMs)][sap-hana-ha]. Para obter uma lista de bases de dados suportadas, consulte [SAP Nota 1928533][1928533].

1. Executar a instalação de instância de base de dados SAP

   Instale a instância de base de dados SAP NetWeaver como raiz utilizando um nome de anfitrião virtual que mapeie para o endereço IP da configuração frontal do equilíbrio de carga para a base de dados, por <b>exemplo, nw1-db</b> e <b>10.0.0.13</b>.

   Pode utilizar o parâmetro sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um utilizador sem raízes se coneca ao sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>Instalação do servidor de aplicações SAP NetWeaver

Siga estes passos para instalar um servidor de aplicação SAP.

1. Preparar servidor de aplicações

   Siga os passos na preparação do servidor de [aplicações SAP NetWeaver](high-availability-guide-suse.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) acima para preparar o servidor de aplicação.

1. Instalar servidor de aplicações SAP NetWeaver

   Instale um servidor de aplicações SAP NetWeaver primário ou adicional.

   Pode utilizar o parâmetro sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um utilizador sem raízes se coneca ao sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. Atualizar loja segura SAP HANA

   Atualize a loja segura SAP HANA para apontar para o nome virtual da configuração de replicação do sistema SAP HANA.

   Executar o seguinte comando para listar as entradas
   <pre><code>hdbuserstore List
   </code></pre>

   Isto deve listar todas as entradas e deve ser semelhante a
   <pre><code>DATA FILE       : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.DAT
   KEY FILE        : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.0.0.14:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>HN1</b>
   </code></pre>

   A saída mostra que o endereço IP da entrada predefinida está a apontar para a máquina virtual e não para o endereço IP do equilibrador de carga. Esta entrada tem de ser alterada para indicar o nome de anfitrião virtual do equilibrador de carga. Certifique-se de utilizar a mesma porta **(30313** na saída acima) e nome de base de dados **(HN1** na saída acima)!

   <pre><code>su - <b>nw1</b>adm
   hdbuserstore SET DEFAULT <b>nw1-db:30313@HN1</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>Testar a configuração do cluster

Os seguintes testes são uma cópia dos casos de teste nos guias de boas práticas da SUSE. São copiados para sua conveniência. Leia sempre também os guias de boas práticas e efetue todos os testes adicionais que possam ter sido adicionados.

1. Teste HAGetFailoverConfig, HACheckConfig e HACheckFailoverConfig

   Execute os seguintes comandos como \<sapsid>adm no nó onde a instância ASCS está atualmente em execução. Se os comandos falharem com FAIL: Memória insuficiente, pode ser causada por traços no seu nome de anfitrião. Esta é uma questão conhecida e será corrigida pela SUSE no pacote de conector de sáb-suse-cluster.

   <pre><code>nw1-cl-0:nw1adm 54> sapcontrol -nr <b>00</b> -function HAGetFailoverConfig
   
   # 15.08.2018 13:50:36
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: Toolchain Module
   # HASAPInterfaceVersion: Toolchain Module (sap_suse_cluster_connector 3.0.1)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode:
   # HANodes: nw1-cl-0, nw1-cl-1
   
   nw1-cl-0:nw1adm 55> sapcontrol -nr 00 -function HACheckConfig
   
   # 15.08.2018 14:00:04
   # HACheckConfig
   # OK
   # state, category, description, comment
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP instance configuration, 2 ABAP instances detected
   # SUCCESS, SAP CONFIGURATION, Redundant Java instance configuration, 0 Java instances detected
   # SUCCESS, SAP CONFIGURATION, Enqueue separation, All Enqueue server separated from application server
   # SUCCESS, SAP CONFIGURATION, MessageServer separation, All MessageServer separated from application server
   # SUCCESS, SAP CONFIGURATION, ABAP instances on multiple hosts, ABAP instances on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP SPOOL service configuration, 2 ABAP instances with SPOOL service detected
   # SUCCESS, SAP STATE, Redundant ABAP SPOOL service state, 2 ABAP instances with active SPOOL service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP SPOOL service on multiple hosts, ABAP instances with active ABAP SPOOL service on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP BATCH service configuration, 2 ABAP instances with BATCH service detected
   # SUCCESS, SAP STATE, Redundant ABAP BATCH service state, 2 ABAP instances with active BATCH service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP BATCH service on multiple hosts, ABAP instances with active ABAP BATCH service on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP DIALOG service configuration, 2 ABAP instances with DIALOG service detected
   # SUCCESS, SAP STATE, Redundant ABAP DIALOG service state, 2 ABAP instances with active DIALOG service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP DIALOG service on multiple hosts, ABAP instances with active ABAP DIALOG service on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP UPDATE service configuration, 2 ABAP instances with UPDATE service detected
   # SUCCESS, SAP STATE, Redundant ABAP UPDATE service state, 2 ABAP instances with active UPDATE service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP UPDATE service on multiple hosts, ABAP instances with active ABAP UPDATE service on multiple hosts detected
   # SUCCESS, SAP STATE, SCS instance running, SCS instance status ok
   # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version (nw1-ascs_NW1_00), SAPInstance includes is-ers patch
   # SUCCESS, SAP CONFIGURATION, Enqueue replication (nw1-ascs_NW1_00), Enqueue replication enabled
   # SUCCESS, SAP STATE, Enqueue replication state (nw1-ascs_NW1_00), Enqueue replication active
   
   nw1-cl-0:nw1adm 56> sapcontrol -nr 00 -function HACheckFailoverConfig
   
   # 15.08.2018 14:04:08
   # HACheckFailoverConfig
   # OK
   # state, category, description, comment
   # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch
   </code></pre>

1. Migrar manualmente a instância ASCS

   Estado de recurso antes de iniciar o teste:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Executar os seguintes comandos como raiz para migrar a instância ASCS.

   <pre><code>nw1-cl-0:~ # crm resource migrate rsc_sap_NW1_ASCS00 force
   # INFO: Move constraint created for rsc_sap_NW1_ASCS00
   
   nw1-cl-0:~ # crm resource unmigrate rsc_sap_NW1_ASCS00
   # INFO: Removed migration constraints for rsc_sap_NW1_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Estado de recurso após o teste:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Teste HAFailoverToNode

   Estado de recurso antes de iniciar o teste:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Executar os seguintes \<comandos como sapsid>adm para migrar a instância ASCS.

   <pre><code>nw1-cl-0:nw1adm 55> sapcontrol -nr 00 -host nw1-ascs -user nw1adm &lt;password&gt; -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   # Remove migration constraints
   nw1-cl-0:~ # crm resource clear rsc_sap_NW1_ASCS00
   #INFO: Removed migration constraints for rsc_sap_NW1_ASCS00
   </code></pre>

   Estado de recurso após o teste:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Simular a queda do nó

   Estado de recurso antes de iniciar o teste:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Executar o seguinte comando como raiz no nó onde a instância ASCS está em execução

   <pre><code>nw1-cl-0:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Se utilizar o SBD, o Pacemaker não deve iniciar automaticamente o nó morto. O estado após o nó ser reiniciado deve ser assim.

   <pre><code>Online: [ nw1-cl-1 ]
   OFFLINE: [ nw1-cl-0 ]
   
   Full list of resources:
   
   stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   
   Failed Actions:
   * rsc_sap_NW1_ERS02_monitor_11000 on nw1-cl-1 'not running' (7): call=219, status=complete, exitreason='none',
       last-rc-change='Wed Aug 15 14:38:38 2018', queued=0ms, exec=0ms
   </code></pre>

   Use os seguintes comandos para iniciar o Pacemaker no nó morto, limpe as mensagens SBD e limpe os recursos falhados.

   <pre><code># run as root
   # list the SBD device(s)
   nw1-cl-0:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   nw1-cl-0:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message nw1-cl-0 clear
   
   nw1-cl-0:~ # systemctl start pacemaker
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ASCS00
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Estado de recurso após o teste:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Reiniciar manual de teste da instância ASCS

   Estado de recurso antes de iniciar o teste:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Crie um bloqueio de fila, por exemplo, editar um utilizador em transações su01. Execute os seguintes comandos como \<sapsid>adm no nó onde a instância ASCS está em execução. Os comandos pararão a instância ASCS e reiniciá-lo-ão. Se utilizar a arquitetura enqueue server 1, espera-se que o bloqueio de fila se perca neste teste. Se utilizar a arquitetura enqueue server 2, a fila será mantida. 

   <pre><code>nw1-cl-1:nw1adm 54> sapcontrol -nr 00 -function StopWait 600 2
   </code></pre>

   A instância ASCS deve agora ser desativada no Pacemaker

   <pre><code>rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Stopped (disabled)
   </code></pre>

   Reinicie a instância ASCS no mesmo nó.

   <pre><code>nw1-cl-1:nw1adm 54> sapcontrol -nr 00 -function StartWait 600 2
   </code></pre>

   O bloqueio de transação su01 deve ser perdido e o back-end deveria ter sido reiniciado. Estado de recurso após o teste:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Matar o processo do servidor de mensagens

   Estado de recurso antes de iniciar o teste:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Executar os seguintes comandos como raiz para identificar o processo do servidor de mensagem e matá-lo.

   <pre><code>nw1-cl-1:~ # pgrep ms.sapNW1 | xargs kill -9
   </code></pre>

   Se matar o servidor de mensagens uma vez, será reiniciado por sapstart. Se o matarcom frequência, o Pacemaker irá eventualmente mover a instância ASCS para o outro nó. Executar os seguintes comandos como raiz para limpar o estado de recurso da instância ASCS e ERS após o teste.

   <pre><code>nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ASCS00
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Estado de recurso após o teste:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Matar o processo do servidor enfila

   Estado de recurso antes de iniciar o teste:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Executar os seguintes comandos como raiz no nó onde a instância ASCS está a correr para matar o servidor de fila.

   <pre><code>nw1-cl-0:~ # pgrep en.sapNW1 | xargs kill -9
   </code></pre>

   A instância ASCS deve falhar imediatamente no outro nó. A instância ERS também deve falhar depois de iniciado o caso ASCS. Executar os seguintes comandos como raiz para limpar o estado de recurso da instância ASCS e ERS após o teste.

   <pre><code>nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ASCS00
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Estado de recurso após o teste:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Matar processo de servidor de replicação de enfila

   Estado de recurso antes de iniciar o teste:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Executar o seguinte comando como raiz no nó onde a instância ERS está a correr para matar o processo do servidor de replicação de fila.

   <pre><code>nw1-cl-0:~ # pgrep er.sapNW1 | xargs kill -9
   </code></pre>

   Se só executar o comando uma vez, a sapstart reiniciará o processo. Se o executar com frequência, o sapstart não reiniciará o processo e o recurso estará em estado de paragem. Executar os seguintes comandos como raiz para limpar o estado de recurso da instância ERS após o teste.

   <pre><code>nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Estado de recurso após o teste:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Kill enqueue sapstartsrv processo

   Estado de recurso antes de iniciar o teste:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Executar os seguintes comandos como raiz no nó onde o ASCS está em execução.

   <pre><code>nw1-cl-1:~ # pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   nw1-cl-1:~ # kill -9 59545
   </code></pre>

   O processo sapstartsrv deve ser sempre reiniciado pelo agente de recursos Pacemaker. Estado de recurso após o teste:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

## <a name="next-steps"></a>Passos seguintes

* [HA para SAP NW em VMs Azure em SLES para aplicações SAP guia multi-SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)
* [Planeamento e implementação de Máquinas Virtuais Azure para SAP][planning-guide]
* [Implantação de Máquinas Virtuais Azure para SAP][deployment-guide]
* [Implantação de DBMS de Máquinas Virtuais Azure para SAP][dbms-guide]
* Para aprender como estabelecer alta disponibilidade e plano para a recuperação de desastres de SAP HANA em VMs Azure, consulte [Alta Disponibilidade de SAP HANA em Máquinas Virtuais Azure (VMs)][sap-hana-ha]
