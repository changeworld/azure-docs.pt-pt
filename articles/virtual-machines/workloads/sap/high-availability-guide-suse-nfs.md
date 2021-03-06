---
title: Alta disponibilidade para NFS em VMs Azure em SLES Microsoft Docs
description: Alta disponibilidade para NFS em VMs Azure no SUSE Linux Enterprise Server
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/26/2020
ms.author: radeltch
ms.openlocfilehash: 4dce0a675f5841591da00a322b72718964d382ac
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348878"
---
# <a name="high-availability-for-nfs-on-azure-vms-on-suse-linux-enterprise-server"></a>Alta disponibilidade para NFS em VMs Azure no SUSE Linux Enterprise Server

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

[sles-hae-guides]:https://www.suse.com/documentation/sle-ha-12/
[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md

Este artigo descreve como implementar as máquinas virtuais, configurar as máquinas virtuais, instalar a estrutura de cluster e instalar um servidor NFS altamente disponível que pode ser usado para armazenar os dados partilhados de um sistema SAP altamente disponível.
Este guia descreve como configurar um servidor NFS altamente disponível que é usado por dois sistemas SAP, NW1 e NW2. Os nomes dos recursos (por exemplo, máquinas virtuais, redes virtuais) no exemplo assumem que utilizou o modelo de servidor de [ficheiros SAP][template-file-server] com **profixo**de recursos prod .

Leia as seguintes Notas e papéis SAP primeiro

* Nota SAP [1928533,]que tem:
  * Lista de tamanhos De VM Azure que são suportados para a implementação de software SAP
  * Informações importantes sobre a capacidade para tamanhos de VM Azure
  * Software SAP suportado e sistema operativo (OS) e combinações de bases de dados
  * Versão necessária do kernel SAP para Windows e Linux no Microsoft Azure

* O SAP Note [2015553] lista os pré-requisitos para implementações de software SAP suportadas pela SAP em Azure.
* SAP Nota [2205917] recomendou definições de OS para SUSE Linux Enterprise Server para Aplicações SAP
* SAP Nota [1944799] tem Diretrizes SAP HANA para SUSE Linux Enterprise Server para Aplicações SAP
* O SAP Note [2178632] tem informações detalhadas sobre todas as métricas de monitorização reportadas para o SAP em Azure.
* O SAP Note [2191498] tem a versão necessária do Agente anfitrião SAP para o Linux em Azure.
* SAP Nota [2243692] tem informações sobre licenciamento SAP em Linux em Azure.
* SAP Note [1984787] tem informações gerais sobre o SUSE Linux Enterprise Server 12.
* A Nota [SAP 1999351] tem informações adicionais de resolução de problemas para a extensão de monitorização avançada do Azure para sAP.
* [A SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) exigiu todas as notas SAP para linux.
* [Planeamento e implementação de Máquinas Virtuais Azure para SAP em Linux][planning-guide]
* [Implantação de Máquinas Virtuais Azure para SAP em Linux (este artigo)][deployment-guide]
* [Implantação de DBMS de Máquinas Virtuais Azure para SAP em Linux][dbms-guide]
* [SUSE Linux Enterprise High Availability Extension 12 SP3 guias de boas práticas][sles-hae-guides]
  * Armazenamento NFS altamente disponível com DRBD e Pacemaker
* [SUSE Linux Enterprise Server para Aplicações SAP 12 Guias de boas práticas SP3][sles-for-sap-bp]
* [Extensão de alta disponibilidade sUSE 12 Notas de lançamento SP3][suse-ha-12sp3-relnotes]

## <a name="overview"></a>Descrição geral

Para obter uma elevada disponibilidade, o SAP NetWeaver requer um servidor NFS. O servidor NFS está configurado num cluster separado e pode ser utilizado por vários sistemas SAP.

![Visão geral de alta disponibilidade da SAP NetWeaver](./media/high-availability-guide-nfs/ha-suse-nfs.png)

O servidor NFS utiliza um nome de anfitrião virtual dedicado e endereços IP virtuais para cada sistema SAP que utiliza este servidor NFS. No Azure, é necessário utilizar um endereço IP virtual. A lista que se segue mostra a configuração do equilibrador de carga.        

* Configuração frontend
  * Endereço IP 10.0.0.4 para NW1
  * Endereço IP 10.0.0.5 para NW2
* Configuração de backend
  * Ligado às interfaces de rede primáriade todas as máquinas virtuais que devem fazer parte do cluster NFS
* Porta sonda
  * Porto 61000 para NW1
  * Porto 61001 para NW2
* Regras de equilíbrio de carga (se utilizar um equilíbrio de carga básico)
  * TCP de 2049 para NW1
  * UDP 2049 para NW1
  * TCP de 2049 para NW2
  * UDP 2049 para NW2

## <a name="set-up-a-highly-available-nfs-server"></a>Configurar um servidor NFS altamente disponível

Pode utilizar um modelo Azure do GitHub para implementar todos os recursos necessários do Azure, incluindo as máquinas virtuais, o conjunto de disponibilidade e o equilibrador de carga ou pode implementar os recursos manualmente.

### <a name="deploy-linux-via-azure-template"></a>Implementar linux via modelo Azure

O Azure Marketplace contém uma imagem para o SUSE Linux Enterprise Server para aplicações SAP 12 que pode utilizar para implementar novas máquinas virtuais.
Você pode usar um dos modelos de arranque rápido no GitHub para implementar todos os recursos necessários. O modelo implanta as máquinas virtuais, o equilibrador de carga, conjunto de disponibilidade, etc. Siga estes passos para implementar o modelo:

1. Abra o modelo do servidor de [ficheiros SAP][template-file-server] no portal Azure   
1. Insira os seguintes parâmetros
   1. Prefixo de recurso  
      Introduza o prefixo que pretende utilizar. O valor é usado como prefixo para os recursos que são implantados.
   2. Contagem do sistema SAP  
      Introduza o número de sistemas SAP que utilizarão este servidor de ficheiros. Isto irá implantar a quantidade necessária de configurações frontais, regras de equilíbrio de carga, portas de sonda, discos, etc.
   3. Tipo Os  
      Selecione uma das distribuições linux. Para este exemplo, selecione SLES 12
   4. Nome de utilizador e senha de administrador  
      É criado um novo utilizador que pode ser utilizado para iniciar sessão na máquina.
   5. Id da sub-rede  
      Se pretender implantar o VM numa VNet existente onde tem uma sub-rede definida a VM deve ser atribuída, diga o nome da identificação dessa sub-rede específica. O ID geralmente parece /subscrições/**&lt;ID&gt;de subscrição**/recursosGroups/**&lt;nome&gt;** de grupo de recursos /fornecedores/Microsoft.Network/virtualNetworks/**&lt;virtual network name&gt;**/subnets/**&lt;subnet name&gt; **

### <a name="deploy-linux-manually-via-azure-portal"></a>Implementar o Linux manualmente através do portal Azure

Primeiro é necessário criar as máquinas virtuais para este cluster NFS. Depois, cria-se um equilibrador de carga e utiliza as máquinas virtuais nas piscinas de backend.

1. Criar um Grupo de Recursos
1. Criar uma Rede Virtual
1. Criar um conjunto de disponibilidade  
   Definir domínio de atualização max
1. Criar Máquina Virtual 1 Utilize pelo menos SLES4SAP 12 SP3, neste exemplo é utilizada a imagem SLES4SAP 12 SP3 BYOS Para aplicações SAP 12 SP3 (BYOS)  
   Selecione Conjunto de Disponibilidade criado anteriormente  
1. Criar máquina virtual 2 Utilize pelo menos SLES4SAP 12 SP3, neste exemplo a imagem SLES4SAP 12 SP3 BYOS  
   SLES Para Aplicações SAP 12 SP3 (BYOS) é usado  
   Selecione Conjunto de Disponibilidade criado anteriormente  
1. Adicione um disco de dados para cada sistema SAP a ambas as máquinas virtuais.
1. Criar um Balancer de Carga (interno). Recomendamos um [equilíbrio de carga padrão.](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)  
   1. Siga estas instruções para criar um equilíbrio de carga padrão:
      1. Crie os endereços IP frontend
         1. Endereço IP 10.0.0.4 para NW1
            1. Abra o equilibrador de carga, selecione pool IP frontend e clique em Adicionar
            1. Introduza o nome do novo pool IP frontend (por exemplo **nw1-frontend)**
            1. Definir a Atribuição à Estática e introduzir o endereço IP (por exemplo **10.0.0.4**)
            1. Clique OK
         1. Endereço IP 10.0.0.5 para NW2
            * Repita os passos acima para NW2
      1. Crie as piscinas de backend
         1. Ligado às interfaces de rede primáriade todas as máquinas virtuais que devem fazer parte do cluster NFS
            1. Abra o equilibrador de carga, selecione piscinas de backend e clique em Adicionar
            1. Introduza o nome da nova piscina de backend (por **exemplo, nw-backend)**
            1. Selecione Rede Virtual
            1. Clique em Adicionar uma máquina virtual
            1. Selecione as máquinas virtuais do cluster NFS e os seus endereços IP.
            1. Clique em Adicionar.
      1. Criar as sondas de saúde
         1. Porto 61000 para NW1
            1. Abra o equilibrador de carga, selecione sondas de saúde e clique em Adicionar
            1. Introduza o nome da nova sonda de saúde (por **exemplo, nw1-hp)**
            1. Selecione TCP como protocolo, porta 610**00,** mantenha intervalo 5 e limiar insalubre 2
            1. Clique OK
         1. Porto 61001 para NW2
            * Repita os passos acima para criar uma sonda de saúde para a NW2
      1. Regras de equilíbrio de carga
         1. Abra o equilibrador de carga, selecione regras de equilíbrio de carga e clique em Adicionar
         1. Introduza o nome da nova regra do equilibrador de carga (por **exemplo, nw1-lb)**
         1. Selecione o endereço IP frontend, a piscina de backend e a sonda de saúde que criou anteriormente (por **exemplo, nw1-frontend**. **nw-backend** e **nw1-hp**)
         1. Selecione **Portas HA**.
         1. Aumente o tempo inativo para 30 minutos
         1. **Certifique-se de ativar o IP flutuante**
         1. Clique OK
         * Repita os passos acima para criar a regra de equilíbrio de carga para A NW2
   1. Em alternativa, se o seu cenário necessitar de um equilíbrio básico de carga, siga estas instruções:
      1. Crie os endereços IP frontend
         1. Endereço IP 10.0.0.4 para NW1
            1. Abra o equilibrador de carga, selecione pool IP frontend e clique em Adicionar
            1. Introduza o nome do novo pool IP frontend (por exemplo **nw1-frontend)**
            1. Definir a Atribuição à Estática e introduzir o endereço IP (por exemplo **10.0.0.4**)
            1. Clique OK
         1. Endereço IP 10.0.0.5 para NW2
            * Repita os passos acima para NW2
      1. Crie as piscinas de backend
         1. Ligado às interfaces de rede primáriade todas as máquinas virtuais que devem fazer parte do cluster NFS
            1. Abra o equilibrador de carga, selecione piscinas de backend e clique em Adicionar
            1. Introduza o nome da nova piscina de backend (por **exemplo, nw-backend)**
            1. Clique em Adicionar uma máquina virtual
            1. Selecione o Conjunto de Disponibilidade que criou anteriormente
            1. Selecione as máquinas virtuais do cluster NFS
            1. Clique OK
      1. Criar as sondas de saúde
         1. Porto 61000 para NW1
            1. Abra o equilibrador de carga, selecione sondas de saúde e clique em Adicionar
            1. Introduza o nome da nova sonda de saúde (por **exemplo, nw1-hp)**
            1. Selecione TCP como protocolo, porta 610**00,** mantenha intervalo 5 e limiar insalubre 2
            1. Clique OK
         1. Porto 61001 para NW2
            * Repita os passos acima para criar uma sonda de saúde para a NW2
      1. Regras de equilíbrio de carga
         1. TCP de 2049 para NW1
            1. Abra o equilibrador de carga, selecione regras de equilíbrio de carga e clique em Adicionar
            1. Introduza o nome da nova regra do equilibrador de carga (por **exemplo, nw1-lb-2049)**
            1. Selecione o endereço IP frontend, a piscina de backend e a sonda de saúde que criou anteriormente (por **exemplo, nw1-frontend)**
            1. Manter o protocolo **TCP,** entrar na porta **2049**
            1. Aumente o tempo inativo para 30 minutos
            1. **Certifique-se de ativar o IP flutuante**
            1. Clique OK
         1. UDP 2049 para NW1
            * Repita os passos acima para o porto 2049 e UDP para NW1
         1. TCP de 2049 para NW2
            * Repita os passos acima para o porto 2049 e TCP para NW2
         1. UDP 2049 para NW2
            * Repita os passos acima para o porto 2049 e UDP para NW2

> [!Note]
> Quando os VMs sem endereços IP públicos forem colocados no conjunto de backend do interno (sem endereço IP público) O equilíbrio de carga Standard Azure não haverá conectividade de saída na Internet, a menos que seja realizada uma configuração adicional para permitir o encaminhamento para pontos finais públicos. Para mais detalhes sobre como alcançar a conectividade de saída, consulte [a conectividade do ponto final público para máquinas virtuais utilizando o Equilíbrio de Carga Padrão Azure em cenários de alta disponibilidade SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections).  

> [!IMPORTANT]
> Não permita os carimbos de tempo da TCP em VMs Azure colocados atrás do Equilíbrio de Carga Azure. Permitir os selos temporais da TCP fará com que as sondas de saúde falhem. Definir parâmetro **net.ipv4.tcp_timestamps** a **0**. Para mais detalhes consulte as sondas de [saúde load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

### <a name="create-pacemaker-cluster"></a>Criar cluster Pacemaker

Siga os passos na configuração do [Pacemaker no SUSE Linux Enterprise Server em Azure](high-availability-guide-suse-pacemaker.md) para criar um cluster pacemaker básico para este servidor NFS.

### <a name="configure-nfs-server"></a>Configurar o servidor NFS

Os seguintes itens são pré-fixados com **[A]** - aplicável a todos os nós, **[1]** - apenas aplicável ao nó 1 ou **[2]** - apenas aplicável ao nó 2.

1. **[A]** Configuração resolução de nome de anfitrião

   Pode utilizar um servidor DNS ou modificar os /etc/anfitriões em todos os nós. Este exemplo mostra como usar o ficheiro /etc/anfitriões.
   Substitua o endereço IP e o nome de anfitrião nos seguintes comandos

   <pre><code>sudo vi /etc/hosts
   </code></pre>
   
   Insira as seguintes linhas para /etc/anfitriões. Altere o endereço IP e o nome de anfitrião para combinar com o seu ambiente
   
   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   <b>10.0.0.5 nw2-nfs</b>
   </code></pre>

1. **[A]** Ativar servidor NFS

   Criar a entrada de exportação nFS raiz

   <pre><code>sudo sh -c 'echo /srv/nfs/ *\(rw,no_root_squash,fsid=0\)>/etc/exports'
   
   sudo mkdir /srv/nfs/
   </code></pre>

1. **[A]** Instalar componentes de drbd

   <pre><code>sudo zypper install drbd drbd-kmp-default drbd-utils
   </code></pre>

1. **[A]** Criar uma partição para os dispositivos drbd

   Listar todos os discos de dados disponíveis

   <pre><code>sudo ls /dev/disk/azure/scsi1/
   </code></pre>

   Saída de exemplo
   
   ```
   lun0  lun1
   ```

   Criar divisórias para cada disco de dados

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun1'
   </code></pre>

1. **[A]** Criar configurações LVM

   Enumerar todas as divisórias disponíveis

   <pre><code>ls /dev/disk/azure/scsi1/lun*-part*
   </code></pre>

   Saída de exemplo
   
   ```
   /dev/disk/azure/scsi1/lun0-part1  /dev/disk/azure/scsi1/lun1-part1
   ```

   Crie volumes LVM para cada partição

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0-part1  
   sudo vgcreate vg-<b>NW1</b>-NFS /dev/disk/azure/scsi1/lun0-part1
   sudo lvcreate -l 100%FREE -n <b>NW1</b> vg-<b>NW1</b>-NFS

   sudo pvcreate /dev/disk/azure/scsi1/lun1-part1
   sudo vgcreate vg-<b>NW2</b>-NFS /dev/disk/azure/scsi1/lun1-part1
   sudo lvcreate -l 100%FREE -n <b>NW2</b> vg-<b>NW2</b>-NFS
   </code></pre>

1. **[A]** Configure drbd

   <pre><code>sudo vi /etc/drbd.conf
   </code></pre>

   Certifique-se de que o ficheiro drbd.conf contém as duas seguintes linhas

   <pre><code>include "drbd.d/global_common.conf";
   include "drbd.d/*.res";
   </code></pre>

   Alterar a configuração global de drbd

   <pre><code>sudo vi /etc/drbd.d/global_common.conf
   </code></pre>

   Adicione as seguintes entradas à secção manipuladora e net.

   <pre><code>global {
        usage-count no;
   }
   common {
        handlers {
             fence-peer "/usr/lib/drbd/crm-fence-peer.sh";
             after-resync-target "/usr/lib/drbd/crm-unfence-peer.sh";
             split-brain "/usr/lib/drbd/notify-split-brain.sh root";
             pri-lost-after-sb "/usr/lib/drbd/notify-pri-lost-after-sb.sh; /usr/lib/drbd/notify-emergency-reboot.sh; echo b > /proc/sysrq-trigger ; reboot -f";
        }
        startup {
             wfc-timeout 0;
        }
        options {
        }
        disk {
             md-flushes yes;
             disk-flushes yes;
             c-plan-ahead 1;
             c-min-rate 100M;
             c-fill-target 20M;
             c-max-rate 4G;
        }
        net {
             after-sb-0pri discard-younger-primary;
             after-sb-1pri discard-secondary;
             after-sb-2pri call-pri-lost-after-sb;
             protocol     C;
             tcp-cork yes;
             max-buffers 20000;
             max-epoch-size 20000;
             sndbuf-size 0;
             rcvbuf-size 0;
        }
   }
   </code></pre>

1. **[A]** Criar os dispositivos de drbd NFS

   <pre><code>sudo vi /etc/drbd.d/<b>NW1</b>-nfs.res
   </code></pre>

   Insira a configuração para o novo dispositivo e saída

   <pre><code>resource <b>NW1</b>-nfs {
        protocol     C;
        disk {
             on-io-error       detach;
        }
        on <b>prod-nfs-0</b> {
             address   <b>10.0.0.6:7790</b>;
             device    /dev/drbd<b>0</b>;
             disk      /dev/<b>vg-NW1-NFS</b>/<b>NW1</b>;
             meta-disk internal;
        }
        on <b>prod-nfs-1</b> {
             address   <b>10.0.0.7:7790</b>;
             device    /dev/drbd<b>0</b>;
             disk      /dev/<b>vg-NW1-NFS</b>/<b>NW1</b>;
             meta-disk internal;
        }
   }
   </code></pre>

   <pre><code>sudo vi /etc/drbd.d/<b>NW2</b>-nfs.res
   </code></pre>

   Insira a configuração para o novo dispositivo e saída

   <pre><code>resource <b>NW2</b>-nfs {
        protocol     C;
        disk {
             on-io-error       detach;
        }
        on <b>prod-nfs-0</b> {
             address   <b>10.0.0.6:7791</b>;
             device    /dev/drbd<b>1</b>;
             disk      /dev/<b>vg-NW2-NFS</b>/<b>NW2</b>;
             meta-disk internal;
        }
        on <b>prod-nfs-1</b> {
             address   <b>10.0.0.7:7791</b>;
             device    /dev/drbd<b>1</b>;
             disk      /dev/<b>vg-NW2-NFS</b>/<b>NW2</b>;
             meta-disk internal;
        }
   }
   </code></pre>

   Crie o dispositivo drbd e ligue-o

   <pre><code>sudo drbdadm create-md <b>NW1</b>-nfs
   sudo drbdadm create-md <b>NW2</b>-nfs
   sudo drbdadm up <b>NW1</b>-nfs
   sudo drbdadm up <b>NW2</b>-nfs
   </code></pre>

1. **[1]** Sem sincronização inicial

   <pre><code>sudo drbdadm new-current-uuid --clear-bitmap <b>NW1</b>-nfs
   sudo drbdadm new-current-uuid --clear-bitmap <b>NW2</b>-nfs
   </code></pre>

1. **[1]** Definir o nó principal

   <pre><code>sudo drbdadm primary --force <b>NW1</b>-nfs
   sudo drbdadm primary --force <b>NW2</b>-nfs
   </code></pre>

1. **[1]** Aguarde até que os novos dispositivos drbd sejam sincronizados

   <pre><code>sudo drbdsetup wait-sync-resource NW1-nfs
   sudo drbdsetup wait-sync-resource NW2-nfs
   </code></pre>

1. **[1]** Criar sistemas de ficheiros nos dispositivos drbd

   <pre><code>sudo mkfs.xfs /dev/drbd0
   sudo mkdir /srv/nfs/NW1
   sudo chattr +i /srv/nfs/NW1
   sudo mount -t xfs /dev/drbd0 /srv/nfs/NW1
   sudo mkdir /srv/nfs/NW1/sidsys
   sudo mkdir /srv/nfs/NW1/sapmntsid
   sudo mkdir /srv/nfs/NW1/trans
   sudo mkdir /srv/nfs/NW1/ASCS
   sudo mkdir /srv/nfs/NW1/ASCSERS
   sudo mkdir /srv/nfs/NW1/SCS
   sudo mkdir /srv/nfs/NW1/SCSERS
   sudo umount /srv/nfs/NW1

   sudo mkfs.xfs /dev/drbd1
   sudo mkdir /srv/nfs/NW2
   sudo chattr +i /srv/nfs/NW2
   sudo mount -t xfs /dev/drbd1 /srv/nfs/NW2
   sudo mkdir /srv/nfs/NW2/sidsys
   sudo mkdir /srv/nfs/NW2/sapmntsid
   sudo mkdir /srv/nfs/NW2/trans
   sudo mkdir /srv/nfs/NW2/ASCS
   sudo mkdir /srv/nfs/NW2/ASCSERS
   sudo mkdir /srv/nfs/NW2/SCS
   sudo mkdir /srv/nfs/NW2/SCSERS
   sudo umount /srv/nfs/NW2
   </code></pre>

1. **[A]** Configuração drbd deteção de cérebro dividido

   Quando se usa o drbd para sincronizar dados de um hospedeiro para outro, pode ocorrer um chamado cérebro dividido. Um cérebro dividido é um cenário onde ambos os nós de cluster promoveram o dispositivo drbd para ser o principal e dessincronizado. Pode ser uma situação rara, mas ainda queres lidar com um cérebro dividido o mais rápido possível. Por isso, é importante ser notificado quando um cérebro dividido aconteceu.

   Leia [a documentação oficial](https://www.linbit.com/drbd-user-guide/users-guide-drbd-8-4/#s-split-brain-notification) sobre como configurar uma notificação cerebral dividida.

   Também é possível recuperar automaticamente de um cenário cerebral dividido. Para mais informações, leia as políticas automáticas de [recuperação cerebral divididas](https://www.linbit.com/drbd-user-guide/users-guide-drbd-8-4/#s-automatic-split-brain-recovery-configuration)
   
### <a name="configure-cluster-framework"></a>Configurar quadro de cluster

1. **[1]** Adicione os dispositivos de drbd NFS para o sistema SAP NW1 à configuração do cluster

   > [!IMPORTANT]
   > Testes recentes revelaram situações, em que o netcat deixa de responder aos pedidos devido a atrasos e à sua limitação de manuseamento apenas uma ligação. O recurso netcat para de ouvir os pedidos do balanceador de carga Azure e o IP flutuante fica indisponível.  
   > Para os aglomerados Pacemaker existentes, recomendamos no passado substituir o netcat por socat. Atualmente recomendamos a utilização de um agente de recursos azure-lb, que faz parte dos agentes de recursos do pacote, com os seguintes requisitos de versão de pacote:
   > - Para o SLES 12 SP4/SP5, a versão deve ser pelo menos agentes de recursos-4.3.018.a7fb5035-3.30.1.  
   > - Para o SLES 15/15 SP1, a versão deve ser pelo menos agentes de recursos-4.3.0184.6ee15eb2-4.13.1.  
   >
   > Note que a alteração exigirá um breve tempo de inatividade.  
   > Para os clusters Pacemaker existentes, se a configuração já foi alterada para utilizar o socat conforme descrito no Endurecimento de Deteção de Equilíbrio [de Carga azure,](https://www.suse.com/support/kb/doc/?id=7024128)não há necessidade de mudar imediatamente para o agente de recursos azure-lb.

   <pre><code>sudo crm configure rsc_defaults resource-stickiness="200"

   # Enable maintenance mode
   sudo crm configure property maintenance-mode=true
   
   sudo crm configure primitive drbd_<b>NW1</b>_nfs \
     ocf:linbit:drbd \
     params drbd_resource="<b>NW1</b>-nfs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"
   
   sudo crm configure ms ms-drbd_<b>NW1</b>_nfs drbd_<b>NW1</b>_nfs \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true" interleave="true"
   
   sudo crm configure primitive fs_<b>NW1</b>_sapmnt \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd0 \
     directory=/srv/nfs/<b>NW1</b>  \
     fstype=xfs \
     op monitor interval="10s"
   
   sudo crm configure primitive nfsserver systemd:nfs-server \
     op monitor interval="30s"
   sudo crm configure clone cl-nfsserver nfsserver

   sudo crm configure primitive exportfs_<b>NW1</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>" \
     options="rw,no_root_squash,crossmnt" clientspec="*" fsid=1 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW1</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.4</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_nfs azure-lb port=<b>61000</b>
   
   sudo crm configure group g-<b>NW1</b>_nfs \
     fs_<b>NW1</b>_sapmnt exportfs_<b>NW1</b> nc_<b>NW1</b>_nfs vip_<b>NW1</b>_nfs
   
   sudo crm configure order o-<b>NW1</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW1</b>_nfs:promote g-<b>NW1</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW1</b>_nfs_on_drbd inf: \
     g-<b>NW1</b>_nfs ms-drbd_<b>NW1</b>_nfs:Master
   </code></pre>

1. **[1]** Adicione os dispositivos de drbd NFS para o sistema SAP NW2 à configuração do cluster

   <pre><code># Enable maintenance mode
   sudo crm configure property maintenance-mode=true
   
   sudo crm configure primitive drbd_<b>NW2</b>_nfs \
     ocf:linbit:drbd \
     params drbd_resource="<b>NW2</b>-nfs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"
   
   sudo crm configure ms ms-drbd_<b>NW2</b>_nfs drbd_<b>NW2</b>_nfs \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true" interleave="true"
   
   sudo crm configure primitive fs_<b>NW2</b>_sapmnt \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd1 \
     directory=/srv/nfs/<b>NW2</b>  \
     fstype=xfs \
     op monitor interval="10s"
   
   sudo crm configure primitive exportfs_<b>NW2</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>" \
     options="rw,no_root_squash,crossmnt" clientspec="*" fsid=2 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW2</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.5</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW2</b>_nfs azure-lb port=<b>61001</b>
   
   sudo crm configure group g-<b>NW2</b>_nfs \
     fs_<b>NW2</b>_sapmnt exportfs_<b>NW2</b> nc_<b>NW2</b>_nfs vip_<b>NW2</b>_nfs
   
   sudo crm configure order o-<b>NW2</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW2</b>_nfs:promote g-<b>NW2</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW2</b>_nfs_on_drbd inf: \
     g-<b>NW2</b>_nfs ms-drbd_<b>NW2</b>_nfs:Master
   </code></pre>

   A `crossmnt` opção `exportfs` nos recursos de cluster está presente na nossa documentação para retrocompatibilidade com versões SLES mais antigas.  

1. **[1]** Desativar o modo de manutenção
   
   <pre><code>sudo crm configure property maintenance-mode=false
   </code></pre>

## <a name="next-steps"></a>Passos seguintes

* [Instale o SAP ASCS e a base de dados](high-availability-guide-suse.md)
* [Planeamento e implementação de Máquinas Virtuais Azure para SAP][planning-guide]
* [Implantação de Máquinas Virtuais Azure para SAP][deployment-guide]
* [Implantação de DBMS de Máquinas Virtuais Azure para SAP][dbms-guide]
* Para aprender como estabelecer alta disponibilidade e plano para a recuperação de desastres de SAP HANA em VMs Azure, consulte [Alta Disponibilidade de SAP HANA em Máquinas Virtuais Azure (VMs)][sap-hana-ha]
