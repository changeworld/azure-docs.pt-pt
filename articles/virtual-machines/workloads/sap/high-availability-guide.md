---
title: Máquinas Virtuais Azure alta disponibilidade para SAP NetWeaver [ Microsoft Docs
description: Guia de alta disponibilidade para SAP NetWeaver em Máquinas Virtuais Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/24/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aa0810818bf7cfea21f925ee639b4b5a50dcb23b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246126"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms"></a>Alta disponibilidade para SAP NetWeaver em VMs Azure

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]:https://launchpad.support.sap.com/#/notes/1619967
[1750510]:https://launchpad.support.sap.com/#/notes/1750510
[1752266]:https://launchpad.support.sap.com/#/notes/1752266
[1757924]:https://launchpad.support.sap.com/#/notes/1757924
[1757928]:https://launchpad.support.sap.com/#/notes/1757928
[1758182]:https://launchpad.support.sap.com/#/notes/1758182
[1758496]:https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:https://docs.microsoft.com/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md
[dbms-guide-2.1]:../../virtual-machines-windows-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f
[dbms-guide-2.2]:../../virtual-machines-windows-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91
[dbms-guide-2.3]:../../virtual-machines-windows-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152
[dbms-guide-2]:../../virtual-machines-windows-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64
[dbms-guide-3]:../../virtual-machines-windows-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3
[dbms-guide-5.5.1]:../../virtual-machines-windows-sap-dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268
[dbms-guide-5.5.2]:../../virtual-machines-windows-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b
[dbms-guide-5.6]:../../virtual-machines-windows-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8
[dbms-guide-5.8]:../../virtual-machines-windows-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30
[dbms-guide-5]:../../virtual-machines-windows-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737
[dbms-guide-8.4.1]:../../virtual-machines-windows-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573
[dbms-guide-8.4.2]:../../virtual-machines-windows-sap-dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d
[dbms-guide-8.4.3]:../../virtual-machines-windows-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c
[dbms-guide-8.4.4]:../../virtual-machines-windows-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818
[dbms-guide-900-sap-cache-server-on-premises]:../../virtual-machines-windows-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:../../virtual-machines-windows-sap-deployment-guide.md
[deployment-guide-2.2]:../../virtual-machines-windows-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94
[deployment-guide-3.1.2]:../../virtual-machines-windows-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab
[deployment-guide-3.2]:../../virtual-machines-windows-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281
[deployment-guide-3.3]:../../virtual-machines-windows-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2
[deployment-guide-3.4]:../../virtual-machines-windows-sap-deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1
[deployment-guide-3]:../../virtual-machines-windows-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e
[deployment-guide-4.1]:../../virtual-machines-windows-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7
[deployment-guide-4.2]:../../virtual-machines-windows-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e
[deployment-guide-4.3]:../../virtual-machines-windows-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc
[deployment-guide-4.4.2]:../../virtual-machines-windows-sap-deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542
[deployment-guide-4.4]:../../virtual-machines-windows-sap-deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d
[deployment-guide-4.5.1]:../../virtual-machines-windows-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4
[deployment-guide-4.5.2]:../../virtual-machines-windows-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f
[deployment-guide-4.5]:../../virtual-machines-windows-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca
[deployment-guide-5.1]:../../virtual-machines-windows-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2
[deployment-guide-5.2]:../../virtual-machines-windows-sap-deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1
[deployment-guide-5.3]:../../virtual-machines-windows-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8

[deployment-guide-configure-monitoring-scenario-1]:../../virtual-machines-windows-sap-deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b
[deployment-guide-configure-proxy]:../../virtual-machines-windows-sap-deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d
[deployment-guide-figure-100]:media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:../../virtual-machines-windows-sap-deployment-guide.md#figure-11
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:../../virtual-machines-windows-sap-deployment-guide.md#figure-14
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:../../virtual-machines-windows-sap-deployment-guide.md#figure-5
[deployment-guide-figure-50]:media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:../../virtual-machines-windows-sap-deployment-guide.md#figure-6
[deployment-guide-figure-600]:media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:../../virtual-machines-windows-sap-deployment-guide.md#figure-7
[deployment-guide-figure-700]:media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:../../virtual-machines-windows-sap-deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:../../virtual-machines-windows-sap-deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:../../virtual-machines-windows-sap-deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:../../virtual-machines-windows-sap-deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b

[deploy-template-cli]:../../../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows
[deploy-template-portal]:../../../resource-group-template-deploy.md#deploy-with-the-preview-portal
[deploy-template-powershell]:../../../resource-group-template-deploy.md#deploy-with-powershell

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:../../virtual-machines-windows-sap-get-started.md
[getting-started-dbms]:../../virtual-machines-windows-sap-get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:../../virtual-machines-windows-sap-get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:../../virtual-machines-windows-sap-get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:https://go.microsoft.com/fwlink/?LinkId=613056

[ha-guide]:high-availability-guide.md

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:planning-guide.md
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92

[planning-guide-figure-100]:media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:high-availability-guide.md
[sap-ha-guide-1]:high-availability-guide.md#217c5479-5595-4cd8-870d-15ab00d4f84c
[sap-ha-guide-2]:high-availability-guide.md#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-3]:high-availability-guide.md#42156640c6-01cf-45a9-b225-4baa678b24f1
[sap-ha-guide-3.1]:high-availability-guide.md#f76af273-1993-4d83-b12d-65deeae23686
[sap-ha-guide-3.2]:high-availability-guide.md#3e85fbe0-84b1-4892-87af-d9b65ff91860
[sap-ha-guide-4]:high-availability-guide.md#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-4.1]:high-availability-guide.md#1a3c5408-b168-46d6-99f5-4219ad1b1ff2
[sap-ha-guide-5]:high-availability-guide.md#fdfee875-6e66-483a-a343-14bbaee33275
[sap-ha-guide-5.1]:high-availability-guide.md#be21cf3e-fb01-402b-9955-54fbecf66592
[sap-ha-guide-5.2]:high-availability-guide.md#ff7a9a06-2bc5-4b20-860a-46cdb44669cd
[sap-ha-guide-6]:high-availability-guide.md#2ddba413-a7f5-4e4e-9a51-87908879c10a
[sap-ha-guide-6.1]:high-availability-guide.md#1a464091-922b-48d7-9d08-7cecf757f341
[sap-ha-guide-6.2]:high-availability-guide.md#44641e18-a94e-431f-95ff-303ab65e0bcb
[sap-ha-guide-7]:high-availability-guide.md#2e3fec50-241e-441b-8708-0b1864f66dfa
[sap-ha-guide-7.1]:high-availability-guide.md#93faa747-907e-440a-b00a-1ae0a89b1c0e
[sap-ha-guide-7.2]:high-availability-guide.md#f559c285-ee68-4eec-add1-f60fe7b978db
[sap-ha-guide-7.2.1]:high-availability-guide.md#b5b1fd0b-1db4-4d49-9162-de07a0132a51
[sap-ha-guide-7.3]:high-availability-guide.md#ddd878a0-9c2f-4b8e-8968-26ce60be1027
[sap-ha-guide-7.4]:high-availability-guide.md#045252ed-0277-4fc8-8f46-c5a29694a816
[sap-ha-guide-8]:high-availability-guide.md#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:high-availability-guide.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.2]:high-availability-guide.md#7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310
[sap-ha-guide-8.3]:high-availability-guide.md#47d5300a-a830-41d4-83dd-1a0d1ffdbe6a
[sap-ha-guide-8.4]:high-availability-guide.md#b22d7b3b-4343-40ff-a319-097e13f62f9e
[sap-ha-guide-8.5]:high-availability-guide.md#9fbd43c0-5850-4965-9726-2a921d85d73f
[sap-ha-guide-8.6]:high-availability-guide.md#84c019fe-8c58-4dac-9e54-173efd4b2c30
[sap-ha-guide-8.7]:high-availability-guide.md#7a8f3e9b-0624-4051-9e41-b73fff816a9e
[sap-ha-guide-8.8]:high-availability-guide.md#f19bd997-154d-4583-a46e-7f5a69d0153c
[sap-ha-guide-8.9]:high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.10]:high-availability-guide.md#e69e9a34-4601-47a3-a41c-d2e11c626c0c
[sap-ha-guide-8.11]:high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:high-availability-guide.md#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:high-availability-guide.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.2]:high-availability-guide.md#e49a4529-50c9-4dcf-bde7-15a0c21d21ca
[sap-ha-guide-8.12.2.1]:high-availability-guide.md#06260b30-d697-4c4d-b1c9-d22c0bd64855
[sap-ha-guide-8.12.2.2]:high-availability-guide.md#4c08c387-78a0-46b1-9d27-b497b08cac3d
[sap-ha-guide-8.12.3]:high-availability-guide.md#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:high-availability-guide.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:high-availability-guide.md#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:high-availability-guide.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:high-availability-guide.md#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:high-availability-guide.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097
[sap-ha-guide-9.1.2]:high-availability-guide.md#eb5af918-b42f-4803-bb50-eff41f84b0b0
[sap-ha-guide-9.1.3]:high-availability-guide.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556
[sap-ha-guide-9.1.4]:high-availability-guide.md#10822f4f-32e7-4871-b63a-9b86c76ce761
[sap-ha-guide-9.1.5]:high-availability-guide.md#4498c707-86c0-4cde-9c69-058a7ab8c3ac
[sap-ha-guide-9.2]:high-availability-guide.md#85d78414-b21d-4097-92b6-34d8bcb724b7
[sap-ha-guide-9.3]:high-availability-guide.md#8a276e16-f507-4071-b829-cdc0a4d36748
[sap-ha-guide-9.4]:high-availability-guide.md#094bc895-31d4-4471-91cc-1513b64e406a
[sap-ha-guide-9.5]:high-availability-guide.md#2477e58f-c5a7-4a5d-9ae3-7b91022cafb5
[sap-ha-guide-9.6]:high-availability-guide.md#0ba4a6c1-cc37-4bcf-a8dc-025de4263772
[sap-ha-guide-10]:high-availability-guide.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9
[sap-ha-guide-10.1]:high-availability-guide.md#65fdef0f-9f94-41f9-b314-ea45bbfea445
[sap-ha-guide-10.2]:high-availability-guide.md#5e959fa9-8fcd-49e5-a12c-37f6ba07b916
[sap-ha-guide-10.3]:high-availability-guide.md#755a6b93-0099-4533-9f6d-5c9a613878b5

[sap-ha-multi-sid-guide]:high-availability-multi-sid.md (Configuração de alta disponibilidade sap multi-SID)


[sap-ha-guide-figure-1000]:media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png

[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../../../azure-resource-manager/management/overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam (Matriz de disponibilidade de produto SAP)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../windows/disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/scalability-targets-standard-accounts.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-windows-attach-disk-portal]:../../virtual-machines-windows-attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../azure-resource-manager/management/overview.md
[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-windows-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-capture]:../../linux/capture-image.md#capture-the-vm
[virtual-machines-windows-capture-image]:../../virtual-machines-windows-capture-image.md
[virtual-machines-windows-capture-image-capture]:../../virtual-machines-windows-capture-image.md#capture-the-vm
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:../../virtual-machines-windows-ps-create.md
[virtual-machines-sizes]:../../virtual-machines-windows-sizes.md
[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../../../virtual-network/virtual-network-deploy-multinic-classic-ps.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md


A Azure Virtual Machines é a solução para organizações que precisam de computação, armazenamento e recursos de rede, em tempo mínimo e sem longos ciclos de aquisição. Pode utilizar máquinas virtuais Azure para implementar aplicações clássicas como ABAP, Java, e uma pilha ABAP+Java. Alargar a fiabilidade e disponibilidade sem recursos adicionais no local. As Máquinas Virtuais Azure suportam a conectividade transversal, para que possa integrar as Máquinas Virtuais Azure nos domínios no local da sua organização, nuvens privadas e paisagem do sistema SAP.

Neste artigo, cobrimos os passos que pode tomar para implementar sistemas SAP de alta disponibilidade em Azure utilizando o modelo de implementação do Gestor de Recursos Azure. Nós o percorremos através destas grandes tarefas:

* Encontre as notas e guias de instalação SAP certos, listados na secção [Recursos.][sap-ha-guide-2] Este artigo complementa a documentação de instalação SAP e as Notas SAP, que são os principais recursos que podem ajudá-lo a instalar e implementar software SAP em plataformas específicas.
* Saiba as diferenças entre o modelo de implementação do Gestor de Recursos Azure e o modelo de implantação clássico do Azure.
* Saiba mais sobre os modos de quórum de clusterde do Windows Server Failover, para que possa selecionar o modelo que é adequado para a sua implementação azure.
* Saiba mais sobre o armazenamento partilhado do Windows Server Failover Clustering nos serviços Azure.
* Saiba como ajudar a proteger componentes de um ponto de falha como a Programação Avançada de Aplicações empresariais (ABAP) SAP Central Services (ASCS)/SAP Central Services (SCS) e sistemas de gestão de bases de dados (DBMS) e componentes redundantes como o SAP Application Server, em Azure.
* Siga um exemplo passo a passo de uma instalação e configuração de um sistema SAP de alta disponibilidade num cluster de clustering de falha do servidor do Windows em Azure utilizando o Gestor de Recursos Azure.
* Saiba sobre os passos adicionais necessários para utilizar o Clustering de Failover do Windows Server em Azure, mas que não são necessários numa implementação no local.

Para simplificar a implementação e configuração, neste artigo, utilizamos os modelos sap de alta disponibilidade de recursos de alta disponibilidade. Os modelos automatizam a implantação de toda a infraestrutura que você precisa para um sistema SAP de alta disponibilidade. A infraestrutura também suporta o dimensionamento sap application performance standard (SAPS) do seu sistema SAP.

## <a name="prerequisites"></a><a name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a>Pré-requisitos
Antes de começar, certifique-se de que cumpre os pré-requisitos descritos nas seguintes secções. Além disso, certifique-se de verificar todos os recursos listados na secção [Recursos.][sap-ha-guide-2]

Neste artigo, utilizamos modelos do Gestor de Recursos Azure para [sap netWeaver de três níveis.](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image/) Para uma visão geral útil dos modelos, consulte os modelos do Gestor de [Recursos SAP Azure](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/).

## <a name="resources"></a><a name="42b8f600-7ba3-4606-b8a5-53c4f026da08"></a>Recursos
Estes artigos abrangem as implantações sap em Azure:

* [Planeamento e implementação de Máquinas Virtuais Azure para SAP NetWeaver][planning-guide]
* [Implantação de Máquinas Virtuais Azure para SAP NetWeaver][deployment-guide]
* [Implantação de DBMS de Máquinas Virtuais Azure para SAP NetWeaver][dbms-guide]
* [Máquinas Virtuais Azure alta disponibilidade para SAP NetWeaver (este guia)][sap-ha-guide]

> [!NOTE]
> Sempre que possível, damos-lhe um link para o guia de instalação SAP (consulte as guias de [instalação SAP).][sap-installation-guides] Para pré-requisitos e informações sobre o processo de instalação, é uma boa ideia ler atentamente os guias de instalação SAP NetWeaver. Este artigo abrange apenas tarefas específicas para sistemas baseados em SAP NetWeaver que pode utilizar com máquinas virtuais Azure.
>
>

Estas Notas SAP estão relacionadas com o tema do SAP em Azure:

| Número de nota | Título |
| --- | --- |
| [1928533] |Aplicações SAP em Azure: Produtos e Dimensionamento SAP |
| [2015553] |SAP no Microsoft Azure: Apoiar pré-requisitos |
| [1999351] |Monitorização azure melhorada para SAP |
| [2178632] |Principais métricas de monitorização para SAP no Microsoft Azure |
| [1999351] |Virtualização no Windows: Monitorização melhorada |
| [2243692] |Utilização de Armazenamento SSD Premium Azure para instância SAP DBMS |

Saiba mais sobre as [limitações das subscrições do Azure,][azure-resource-manager/management/azure-subscription-service-limits-subscription]incluindo limitações gerais de incumprimento e limitações máximas.

## <a name="high-availability-sap-with-azure-resource-manager-vs-the-azure-classic-deployment-model"></a><a name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a>SAP de alta disponibilidade com Azure Resource Manager vs. o modelo de implantação clássico do Azure
Os modelos de implantação clássicos do Azure Resource Manager e Azure são diferentes nas seguintes áreas:

- Grupos de recursos
- Dependência do equilíbrio interno de carga do Azure no grupo de recursos Azure
- Suporte para cenários multi-SID SAP

### <a name="resource-groups"></a><a name="f76af273-1993-4d83-b12d-65deeae23686"></a>Grupos de recursos
No Azure Resource Manager, pode utilizar grupos de recursos para gerir todos os recursos de aplicação na sua subscrição Azure. Uma abordagem integrada, num grupo de recursos, todos os recursos têm o mesmo ciclo de vida. Por exemplo, todos os recursos são criados ao mesmo tempo e são eliminados ao mesmo tempo. Saiba mais sobre [grupos de recursos](../../../azure-resource-manager/management/overview.md#resource-groups).

### <a name="azure-internal-load-balancer-dependency-on-the-azure-resource-group"></a><a name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a>Dependência do equilíbrio interno de carga do Azure no grupo de recursos Azure

No modelo de implantação clássico do Azure, existe uma dependência entre o equilibrador de carga interna Azure (serviço Azure Load Balancer) e o grupo de serviço sinuoso. Todos os equilibradores internos de carga precisam de um grupo de serviço sinuoso.

No Azure Resource Manager, não é preciso um grupo de recursos Azure para utilizar o Azure Load Balancer. O ambiente é mais simples e flexível.

### <a name="support-for-sap-multi-sid-scenarios"></a>Suporte para cenários multi-SID SAP

No Azure Resource Manager, pode instalar várias instâncias de identificador de sistema SAP (SID) ASCS/SCS num só cluster. As instâncias multi-SID são possíveis devido ao suporte para vários endereços IP para cada equilíbrio de carga interna Azure.

Para utilizar o modelo de implementação clássico do Azure, siga os procedimentos descritos no [SAP NetWeaver em Azure: Clustering SAP ASCS/SCS utilizando o Clusterde Failover do Servidor Windows em Azure com o SIOS DataKeeper](https://go.microsoft.com/fwlink/?LinkId=613056).

> [!IMPORTANT]
> Recomendamos vivamente que utilize o modelo de implementação do Gestor de Recursos Azure para as suas instalações SAP. Oferece muitos benefícios que não estão disponíveis no modelo clássico de implantação. Saiba mais sobre [os modelos][virtual-machines-azure-resource-manager-architecture-benefits-arm]de implantação do Azure.   
>
>

## <a name="windows-server-failover-clustering"></a><a name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a>Clustering de falha do servidor do Windows
O Clustering de Failover do Windows Server é a base de uma instalação SAP ASCS/SCS de alta disponibilidade e DBMS no Windows.

Um cluster failover é um grupo de servidores independentes de 1+n (nós) que trabalham em conjunto para aumentar a disponibilidade de aplicações e serviços. Se ocorrer uma falha no nó, o Clusterde failover do Windows Server calcula o número de falhas que podem ocorrer mantendo um cluster saudável para fornecer aplicações e serviços. Pode escolher entre diferentes modos de quórum para obter o agrupamento failover.

### <a name="quorum-modes"></a><a name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a>Modos Quórum
Pode escolher entre quatro modos de quórum quando utilizar o Clusterde Failover do Windows Server:

* **Maioria nó.** Cada nó do agrupamento pode votar. O agrupamento funciona apenas com uma maioria de votos, ou seja, com mais de metade dos votos. Recomendamos esta opção para clusters que têm um número desigual de nós. Por exemplo, três nós num aglomerado de sete nós podem falhar, e o cluster ainda consegue uma maioria e continua a funcionar.  
* **Nó e Maioria do Disco.** Cada nó e um disco designado (uma testemunha de disco) no armazenamento do cluster podem votar quando estão disponíveis e em comunicação. O agrupamento funciona apenas com a maioria dos votos, ou seja, com mais de metade dos votos. Este modo faz sentido num ambiente de cluster com um número par de nós. Se metade dos nós e o disco estiverem on-line, o cluster permanece em estado saudável.
* **Node e File Share Majority**. Cada nó mais uma parte de arquivo designada (uma testemunha de partilha de ficheiros) que o administrador cria pode votar, independentemente de os nós e a partilha de ficheiros estarem disponíveis e em comunicação. O agrupamento funciona apenas com a maioria dos votos, ou seja, com mais de metade dos votos. Este modo faz sentido num ambiente de cluster com um número par de nós. É semelhante ao modo Denó e Disque maioria, mas usa uma partilha de ficheiros de testemunhas em vez de um disco de testemunhas. Este modo é fácil de implementar, mas se a partilha de ficheiros em si não estiver altamente disponível, pode tornar-se um único ponto de falha.
* **Sem maioria: Apenas disco**. O cluster tem um quórum se um nó estiver disponível e em comunicação com um disco específico no armazenamento do cluster. Só os nós que também estão em comunicação com esse disco podem juntar-se ao cluster. Recomendamos que não utilize este modo.

## <a name="windows-server-failover-clustering-on-premises"></a><a name="fdfee875-6e66-483a-a343-14bbaee33275"></a>Clustering de failover do servidor do Windows no local
A figura 1 mostra um aglomerado de dois nós. Se a ligação de rede entre os nós falhar e ambos os nós permanecerem a funcionar, um disco de quórum ou uma partilha de ficheiros determinará qual o nó continuará a fornecer as aplicações e serviços do cluster. O nó que tem acesso ao disco quórum ou partilha de ficheiros é o nó que garante que os serviços continuam.

Como este exemplo usa um cluster de dois nós, usamos o modo quórum da maioria do Nó e do File Share. A Maioria do Nó e do Disco também é uma opção válida. Num ambiente de produção, recomendamos que utilize um disco de quórum. Pode utilizar a tecnologia do sistema de rede e armazenamento para torná-la altamente disponível.

![Figura 1: Exemplo de uma configuração de clustering de falha do servidor do Windows para SAP ASCS/SCS em Azure][sap-ha-guide-figure-1000]

_**Figura 1:** Exemplo de uma configuração de clustering de falha do servidor do Windows para SAP ASCS/SCS em Azure_

### <a name="shared-storage"></a><a name="be21cf3e-fb01-402b-9955-54fbecf66592"></a>Armazenamento compartilhado
A figura 1 também mostra um cluster de armazenamento partilhado de dois nós. Num aglomerado de armazenamento partilhado no local, todos os nós do cluster detetam armazenamento partilhado. Um mecanismo de bloqueio protege os dados da corrupção. Todos os nós podem detetar se outro nó falhar. Se um nó falhar, o nó restante toma posse dos recursos de armazenamento e garante a disponibilidade de serviços.

> [!NOTE]
> Não precisa de discos partilhados para uma alta disponibilidade com algumas aplicações DBMS, como com o SQL Server. O SQL Server Always On replica dados dbmS e ficheiros de registo do disco local de um nó de cluster para o disco local de outro nó de cluster. Nesse caso, a configuração do cluster do Windows não necessita de um disco partilhado.
>
>

### <a name="networking-and-name-resolution"></a><a name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a>Resolução de networking e nome
Os computadores clientes chegam ao cluster sobre um endereço IP virtual e um nome de anfitrião virtual que o servidor DNS fornece. Os nós no local e o servidor DNS podem lidar com vários endereços IP.

Numa configuração típica, utiliza duas ou mais ligações de rede:

* Uma ligação dedicada ao armazenamento
* Uma ligação de rede interna de cluster para o batimento cardíaco
* Uma rede pública que os clientes usam para se conectarem ao cluster

## <a name="windows-server-failover-clustering-in-azure"></a><a name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a>Falha de falha do servidor do Windows em Azure
Em comparação com as implementações de metal ou nuvem privada, as Máquinas Virtuais Azure requerem passos adicionais para configurar o Clusterde Failover do Windows Server. Quando constrói um disco de cluster partilhado, precisa de definir vários endereços IP e nomes de anfitriões virtuais para a instância SAP ASCS/SCS.

Neste artigo, discutimos conceitos-chave e os passos adicionais necessários para construir um cluster de serviços centrais de alta disponibilidade SAP em Azure. Mostramos-lhe como configurar a ferramenta de terceiros SIOS DataKeeper e como configurar o equilibrador de carga interna Azure. Pode utilizar estas ferramentas para criar um cluster de failover do Windows com uma testemunha de partilha de ficheiros no Azure.

![Figura 2: Configuração de clustering de falha do servidor do Windows em Azure sem um disco partilhado][sap-ha-guide-figure-1001]

_**Figura 2:** Configuração de clustering de falha do servidor do Windows em Azure sem um disco partilhado_

### <a name="shared-disk-in-azure-with-sios-datakeeper"></a><a name="1a464091-922b-48d7-9d08-7cecf757f341"></a>Disco partilhado em Azure com DataKeeper SIOS
Você precisa de armazenamento partilhado cluster para uma instância sap ascs/SCS de alta disponibilidade. A partir de setembro de 2016, o Azure não oferece armazenamento partilhado que possa usar para criar um cluster de armazenamento partilhado. Pode utilizar o software de terceiros SIOS DataKeeper Cluster Edition para criar um armazenamento espelhado que simula o armazenamento partilhado em cluster. A solução SIOS fornece replicação de dados sincronizados em tempo real. É assim que se pode criar um recurso de disco partilhado para um cluster:

1. Fixe um disco rígido virtual Azure adicional (VHD) a cada uma das máquinas virtuais (VMs) numa configuração de cluster Windows.
2. Executar A Edição de Cluster de DataKeeper SIOS em ambos os nós de máquina virtual.
3. Configure a SIOS DataKeeper Cluster Edition de modo a espelhar o conteúdo do volume adicional ligado ao VHD da máquina virtual de origem até ao volume adicional ligado ao VHD da máquina virtual alvo. O SIOS DataKeeper resumi a fonte e os volumes locais alvo, e depois apresenta-os ao Clusterde Failover do Windows Server como um disco partilhado.

Obtenha mais informações sobre o [SIOS DataKeeper](https://us.sios.com/products/datakeeper-cluster/).

![Figura 3: Configuração de clustering de falha do servidor do Windows em Azure com DataKeeper SIOS][sap-ha-guide-figure-1002]

_**Figura 3:** Configuração de clustering de falha do servidor do Windows em Azure com DataKeeper SIOS_

> [!NOTE]
> Não precisa de discos partilhados para uma alta disponibilidade com alguns produtos DBMS, como o SQL Server. O SQL Server Always On replica dados dbmS e ficheiros de registo do disco local de um nó de cluster para o disco local de outro nó de cluster. Neste caso, a configuração do cluster do Windows não necessita de um disco partilhado.
>
>

### <a name="name-resolution-in-azure"></a><a name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a>Resolução de nomes em Azure
A plataforma cloud Azure não oferece a opção de configurar endereços IP virtuais, como endereços IP flutuantes. Precisa de uma solução alternativa para configurar um endereço IP virtual para chegar ao recurso do cluster na nuvem.
O Azure tem um equilibrador de carga interno no serviço Azure Load Balancer. Com o equilibrador de carga interna, os clientes chegam ao cluster sobre o endereço IP virtual cluster.
É necessário implantar o equilibrador interno de carga no grupo de recursos que contém os nós do cluster. Em seguida, configure todas as regras necessárias de encaminhamento da porta com as portas de sonda do equilibrante de carga interna.
Os clientes podem ligar-se através do nome de anfitrião virtual. O servidor DNS resolve o endereço IP do cluster e o equilibrador de carga interno manuseia a porta encaminha-se para o nó ativo do cluster.

## <a name="sap-netweaver-high-availability-in-azure-infrastructure-as-a-service-iaas"></a><a name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a>SAP NetWeaver alta disponibilidade em Infraestruturas Azure-as-a-Service (IaaS)
Para alcançar a elevada disponibilidade da aplicação SAP, como para componentes de software SAP, é necessário proteger os seguintes componentes:

* Instância do servidor de aplicações SAP
* Instância SAP ASCS/SCS
* Servidor DBMS

Para obter mais informações sobre a proteção dos componentes SAP em cenários de alta disponibilidade, consulte o planeamento e implementação de [Máquinas Virtuais Azure para o SAP NetWeaver](planning-guide.md).

### <a name="high-availability-sap-application-server"></a><a name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a>Servidor de aplicação SAP de alta disponibilidade
Normalmente não precisa de uma solução específica de alta disponibilidade para o Servidor de Aplicações SAP e para as instâncias de diálogo. Obtém uma elevada disponibilidade por redundância e configurará múltiplas instâncias de diálogo em diferentes instâncias de Máquinas Virtuais Azure. Deverá ter pelo menos duas instâncias de aplicação SAP instaladas em dois casos de Máquinas Virtuais Azure.

![Figura 4: Servidor de aplicação SAP de alta disponibilidade][sap-ha-guide-figure-2000]

_**Figura 4:** Servidor de aplicação SAP de alta disponibilidade_

Deve colocar todas as máquinas virtuais que acolhem instâncias do Servidor de Aplicações SAP no mesmo conjunto de disponibilidade seletiva do Azure. Um conjunto de disponibilidade sinuoso do Azure garante que:

* Todas as máquinas virtuais fazem parte do mesmo domínio de upgrade. Um domínio de atualização, por exemplo, garante que as máquinas virtuais não são atualizadas ao mesmo tempo durante o tempo de paragem de manutenção planeado.
* Todas as máquinas virtuais fazem parte do mesmo domínio de avaria. Um domínio de avaria, por exemplo, garante que as máquinas virtuais são implantadas de modo a que nenhum ponto de falha afete a disponibilidade de todas as máquinas virtuais.

Saiba mais sobre como [gerir a disponibilidade de máquinas virtuais.][virtual-machines-manage-availability]

Como a conta de armazenamento Azure é um potencial ponto único de falha, é importante ter pelo menos duas contas de armazenamento Azure, nas quais pelo menos duas máquinas virtuais são distribuídas. Numa configuração ideal, os discos de cada máquina virtual que está a executar uma instância de diálogo SAP seriam implantados numa conta de armazenamento diferente.

### <a name="high-availability-sap-ascsscs-instance"></a><a name="f559c285-ee68-4eec-add1-f60fe7b978db"></a>Instância SAP ASCS/SCS de alta disponibilidade
A figura 5 é um exemplo de uma instância SAP ASCS/SCS de alta disponibilidade.

![Figura 5: Instância SAP ASCS/SCS de alta disponibilidade][sap-ha-guide-figure-2001]

_**Figura 5:** Instância SAP ASCS/SCS de alta disponibilidade_

#### <a name="sap-ascsscs-instance-high-availability-with-windows-server-failover-clustering-in-azure"></a><a name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a>SAP ASCS/SCS caso alta disponibilidade com Clusterde Falha do Servidor Windows em Azure
Em comparação com as implementações de metal ou nuvem privada, as Máquinas Virtuais Azure requerem passos adicionais para configurar o Clusterde Failover do Windows Server. Para construir um cluster de failover do Windows, necessita de um disco de cluster partilhado, vários endereços IP, vários nomes de anfitriões virtuais e um equilíbrio interno de carga Azure para agrupar uma instância SAP ASCS/SCS. Discutimos mais pormenorizadamente mais tarde no artigo.

![Figura 6: Clustering de falha do servidor do Windows para uma configuração SAP ASCS/SCS em Azure utilizando o DataKeeper sIOS][sap-ha-guide-figure-1002]

_**Figura 6:** Clusterde falha do servidor do Windows para uma configuração SAP ASCS/SCS em Azure com DataKeeper SIOS_

### <a name="high-availability-dbms-instance"></a><a name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a>Instância DBMS de alta disponibilidade
O DBMS também é um único ponto de contacto num sistema SAP. Precisa protegê-lo usando uma solução de alta disponibilidade. A figura 7 mostra um Servidor SQL sempre em solução de alta disponibilidade em Azure, com o Clusterde Failover do Servidor windows e o equilibrador de carga interna Azure. O Servidor SQL Always On replica dados dbmS e ficheiros de registo utilizando a sua própria replicação DBMS. Neste caso, não precisa de discos partilhados em cluster, o que simplifica toda a configuração.

![Figura 7: Exemplo de um SAP DBMS de alta disponibilidade, com o Servidor SQL Sempre Ligado][sap-ha-guide-figure-2003]

_**Figura 7:** Exemplo de um SAP DBMS de alta disponibilidade, com O Servidor SQL Sempre Ligado_

Para obter mais informações sobre o clustering SQL Server em Azure utilizando o modelo de implementação do Gestor de Recursos Azure, consulte estes artigos:

* [Configure sempre no grupo de disponibilidade em Máquinas Virtuais Azure manualmente usando o Gestor de Recursos][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]
* [Configure um equilibrador de carga interna Azure para um grupo de disponibilidade Always On em Azure][virtual-machines-windows-portal-sql-alwayson-int-listener]

## <a name="end-to-end-high-availability-deployment-scenarios"></a><a name="045252ed-0277-4fc8-8f46-c5a29694a816"></a>Cenários de implantação de alta disponibilidade de ponta a ponta

### <a name="deployment-scenario-using-architectural-template-1"></a>Cenário de implantação usando o modelo arquitetônico 1

A figura 8 mostra um exemplo de uma arquitetura de alta disponibilidade SAP NetWeaver em Azure para **um** sistema SAP. Este cenário é configurado da seguinte forma:

- Um cluster dedicado é utilizado para a instância SAP ASCS/SCS.
- Um cluster dedicado é usado para a instância DBMS.
- As instâncias do Servidor de Aplicações SAP são implementadas nos seus próprios VMs dedicados.

![Figura 8: Modelo arquitetónico de alta disponibilidade SAP 1, com cluster dedicado para ASCS/SCS e DBMS][sap-ha-guide-figure-2004]

_**Figura 8:** Modelo arquitetónico de alta disponibilidade SAP 1, clusters dedicados para ASCS/SCS e DBMS_

### <a name="deployment-scenario-using-architectural-template-2"></a>Cenário de implantação usando o modelo arquitetônico 2

A Figura 9 mostra um exemplo de uma arquitetura de alta disponibilidade SAP NetWeaver em Azure para **um** sistema SAP. Este cenário é configurado da seguinte forma:

- Um cluster dedicado é utilizado **tanto** para a instância SAP ASCS/SCS como para o DBMS.
- As instâncias do Servidor de Aplicações SAP são implementadas em VMs próprios dedicados.

![Figura 9: Modelo arquitetónico de alta disponibilidade SAP 2, com um cluster dedicado para ASCS/SCS e um cluster dedicado para DBMS][sap-ha-guide-figure-2005]

_**Figura 9:** Modelo arquitetónico de alta disponibilidade SAP 2, com um cluster dedicado para ASCS/SCS e um cluster dedicado para DBMS_

### <a name="deployment-scenario-using-architectural-template-3"></a>Cenário de implantação usando o modelo arquitetônico 3

A figura 10 mostra um exemplo de uma arquitetura de alta disponibilidade SAP NetWeaver &lt;em&gt;Azure para **dois** sistemas SAP, com &lt;SID1&gt; e SID2 . Este cenário é configurado da seguinte forma:

- Um cluster dedicado é utilizado **tanto** para a instância SAP ASCS/SCS SID1 *como* para a instância SAP ASCS/SCS SID2 (um cluster).
- Um cluster dedicado é usado para DBMS SID1, e outro cluster dedicado é usado para DBMS SID2 (dois clusters).
- As instâncias do Servidor de Aplicações SAP para o sistema SAP SID1 têm os seus próprios VMs dedicados.
- As instâncias do Servidor de Aplicações SAP para o sistema SAP SID2 têm os seus próprios VMs dedicados.

![Figura 10: Modelo arquitetónico de alta disponibilidade SAP 3, com um cluster dedicado para diferentes instâncias ASCS/SCS][sap-ha-guide-figure-6003]

_**Figura 10:** Modelo arquitetónico de alta disponibilidade SAP 3, com um cluster dedicado para diferentes instâncias ASCS/SCS_

## <a name="prepare-the-infrastructure"></a><a name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a>Preparar a infraestrutura

### <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Preparar a infraestrutura para o modelo arquitetônico 1
Os modelos do Gestor de Recursos Azure para SAP ajudam a simplificar a implementação dos recursos necessários.

Os modelos de três níveis no Gestor de Recursos Azure também suportam cenários de alta disponibilidade, como no Modelo Arquitetônico 1, que tem dois clusters. Cada cluster é um ponto único de falha do SAP ASCS/SCS e DBMS.

Aqui é onde você pode obter modelos de Gestor de Recursos Azure para o cenário de exemplo que descrevemos neste artigo:

* [Imagem azure marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Imagem personalizada](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)

Para preparar a infraestrutura para o modelo arquitetônico 1:

- No portal Azure, na lâmina **Parâmetros,** na caixa **SYSTEMAVAILABILITY,** selecione **HA**.

  ![Figura 11: Definir parâmetros de alta disponibilidade do SAP Do Gestor de Recursos Azure][sap-ha-guide-figure-3000]

_**Figura 11:** Definir parâmetros de gestor de recursos Azure de alta disponibilidade SAP_


  Os modelos criam:

  * **Máquinas virtuais:**
    * Máquinas virtuais do Servidor de Aplicações SAP: *Number* número>-di-<*<SAPSystemSID*>
    * Máquinas virtuais de cluster ASCS/SCS: <*Número*>-ascs-<*SAPSystemSID*>
    * Cluster DBMS: <*SAPSystemSID*>-db-<*Number*>

  * **Cartões de rede para todas as máquinas virtuais, com endereços IP associados:**
    * <*Número* *sAPSystemSID*>-nic-di-<>
    * <*Número* de>-nic-ascs-<*SAPSystemSID*>
    * <*Número* *sAPSystemSID*>-nic-db-<>

  * **Contas de armazenamento do Azure**

  * **Grupos de disponibilidade** para:
    * Máquinas virtuais do Servidor de Aplicações SAP: <*SAPSystemSID*>-avset-di
    * Máquinas virtuais do cluster SAP ASCS/SCS: <*SAPSystemSID*>-avset-ascs
    * Máquinas virtuais do cluster DBMS: <*SAPSystemSID*>-avset-db

  * Equilíbrio interno de **carga azul:**
    * Com todas as portas para a instância ASCS/SCS e endereço IP <*SAPSystemSID*>-lb-ascs
    * Com todas as portas para o DbMS e endereço IP do Servidor SQL <*SAPSystemSID*>-lb-db

  * **Grupo de segurança da rede**: <*SAPSystemSID*>-nsg-ascs-0  
    * Com uma porta aberta de Protocolo de Ambiente de Trabalho Remoto (RDP) ao <máquina virtual *SAPSystemSID*>-ascs-0

> [!NOTE]
> Todos os endereços IP dos cartões de rede e dos equilibradores de carga internos Azure são **dinâmicos** por padrão. Mude-os para endereços **IP estáticos.** Descrevemos como fazê-lo mais tarde no artigo.
>
>

### <a name="deploy-virtual-machines-with-corporate-network-connectivity-cross-premises-to-use-in-production"></a><a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a>Implementar máquinas virtuais com conectividade de rede corporativa (cross-premises) para utilizar na produção
Para a produção de sistemas SAP, implemente máquinas virtuais Azure com conectividade de rede corporativa utilizando a VPN do Site Azure ou o Azure ExpressRoute.

> [!NOTE]
> Pode utilizar a sua instância de Rede Virtual Azure. A rede virtual e a subnet já foram criadas e preparadas.
>
>

1. No portal Azure, na lâmina **Parâmetros,** na caixa **NEWOREXISTINGSUBNET,** selecione **existindo**.
2. Na caixa **SUBNETID,** adicione a cadeia completa da sua rede Azure preparada SubnetID onde planeia implantar as suas máquinas virtuais Azure.
3. Para obter uma lista de todas as subredes da rede Azure, execute este comando PowerShell:

   ```PowerShell
   (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
   ```

   O campo **de identificação** mostra o **SUBNETID**.
4. Para obter uma lista de todos os valores **SUBNETID,** execute este comando PowerShell:

   ```PowerShell
   (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
   ```

   O **SUBNETID** é assim:

   ```
   /subscriptions/<SubscriptionId>/resourceGroups/<VPNName>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<SubnetName>
   ```

### <a name="deploy-cloud-only-sap-instances-for-test-and-demo"></a><a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a>Implementar instâncias SAP apenas em nuvem para teste e demonstração
Pode implementar o seu sistema SAP de alta disponibilidade num modelo de implementação apenas em nuvem. Este tipo de implantação é principalmente útil para casos de demo e de utilização de testes. Não é adequado para casos de uso de produção.

- No portal Azure, na lâmina **Parâmetros,** na caixa **NEWOREXISTINGSUBNET,** selecione **novos**. Deixe o campo **SUBNETID** vazio.

  O modelo SAP Azure Resource Manager cria automaticamente a rede virtual azure e a subnet.

> [!NOTE]
> Também precisa de implantar pelo menos uma máquina virtual dedicada para Ative Directory e DNS na mesma instância da Rede Virtual Azure. O modelo não cria estas máquinas virtuais.
>
>


### <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Preparar a infraestrutura para o modelo arquitetônico 2

Você pode usar este modelo de Gestor de Recursos Azure para SAP para ajudar a simplificar a implementação de recursos de infraestrutura necessários para o Modelo Arquitetónico SAP 2.

Aqui é onde você pode obter modelos de Gestor de Recursos Azure para este cenário de implementação:

* [Imagem azure marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Imagem personalizada](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)


### <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Preparar a infraestrutura para o modelo arquitetônico 3

Pode preparar a infraestrutura e configurar o SAP para **multi-SID**. Por exemplo, pode adicionar uma instância adicional SAP ASCS/SCS numa configuração de cluster *existente.* Para obter mais informações, consulte [configurar uma instância adicional do SAP ASCS/SCS numa configuração de cluster existente para criar uma configuração multi-SID SAP no Gestor][sap-ha-multi-sid-guide]de Recursos Azure .

Se quiser criar um novo cluster multi-SID, pode utilizar os modelos de arranque rápido multi-SID [no GitHub](https://github.com/Azure/azure-quickstart-templates).
Para criar um novo cluster multi-SID, é necessário implementar os seguintes três modelos:

* [Modelo ASCS/SCS](#ASCS-SCS-template)
* [Modelo de base de dados](#database-template)
* [Modelo de servidores de aplicação](#application-servers-template)

As seguintes secções têm mais detalhes sobre os modelos e os parâmetros que precisa fornecer nos modelos.

#### <a name="ascsscs-template"></a><a name="ASCS-SCS-template"></a>Modelo ASCS/SCS

O modelo ASCS/SCS implementa duas máquinas virtuais que pode utilizar para criar um cluster de falhas do Windows Server que acolhe várias instâncias ASCS/SCS.

Para configurar o modelo ASCS/SCS multi-SID, no [modelo ASCS/SCS multi-SID,][sap-templates-3-tier-multisid-xscs-marketplace-image]introduza valores para os seguintes parâmetros:

  - **Prefixo de recursos**.  Detete o prefixo de recursos, que é usado para pré-fixar todos os recursos que são criados durante a implementação. Como os recursos não pertencem apenas a um sistema SAP, o prefixo do recurso não é o SID de um sistema SAP.  O prefixo deve estar entre **três e seis caracteres.**
  - **Tipo de pilha**. Selecione o tipo de pilha do sistema SAP. Dependendo do tipo de pilha, o Azure Load Balancer tem um (apenas ABAP ou Java) ou dois endereços IP privados (ABAP+Java) por sistema SAP.
  -  **TIPO OS**. Selecione o sistema operativo das máquinas virtuais.
  -  **Contagem do sistema SAP**. Selecione o número de sistemas SAP que pretende instalar neste cluster.
  -  **Disponibilidade do Sistema**. Selecione **HA**.
  -  **Nome de utilizador e senha**de administrador. Crie um novo utilizador que possa ser usado para iniciar sessão na máquina.
  -  **Subnet nova ou existente.** Definir se deve ser criada uma nova rede virtual e uma sub-rede ou uma sub-rede existente. Se já tem uma rede virtual ligada à sua rede no local, **selecione existindo**.
  -  **Id subnet**. Se pretender implantar o VM numa VNet existente onde tem uma sub-rede definida a VM deve ser atribuída, diga o nome da identificação dessa sub-rede específica. O ID geralmente se parece com este: /subscrições/<*id de subscrição*>/recursosGroups/<nome de grupo de *recursos*>/fornecedores/Microsoft.Network/virtualNetworks/<nome de *rede virtual*>/subnets/<nome da *sub-rede*>

O modelo implementa uma instância azure Load Balancer, que suporta vários sistemas SAP.

- Os casos ASCS estão configurados por exemplo número 00, 10, 20...
- Os casos scs estão configurados por exemplo número 01, 11, 21...
- As instâncias do Servidor de Replicação de Enfila ASCS (ERS) (apenas Linux) estão configuradas por exemplo o número 02, 12, 22...
- Os casos SCS ERS (apenas Linux) estão configurados por exemplo número 03, 13, 23...

O equilibrador de carga contém 1 (2 para Linux) VIP(s), 1x VIP para ASCS/SCS e 1x VIP para ERS (apenas Linux).

A lista que se segue contém todas as regras de equilíbrio de carga (onde x é o número do sistema SAP, por exemplo, 1, 2, 3...):
- Portas específicas do Windows para cada sistema SAP: 445,5985
- Portas ASCS (número de instância x0): 32x0, 36x0, 39x0, 81x0, 5x013, 5x014, 5x016
- Portas SCS (número de instância x1): 32x1, 33x1, 39x1, 81x1, 5x113, 5x114, 5x116
- Portas ASCS ERS em Linux (número de instância x2): 33x2, 5x213, 5x214, 5x216
- Portas SCS ERS em Linux (número de instância x3): 33x3, 5x313, 5x314, 5x316

O equilibrista de carga está configurado para utilizar as seguintes portas de sonda (onde x é o número do sistema SAP, por exemplo, 1, 2, 3...):
- Porta de sonda de balanço de carga interna ASCS/SCS: 620x0
- Porta de sonda de balanço interno da ERS (apenas Linux): 621x2

#### <a name="database-template"></a><a name="database-template"></a>Modelo de base de dados

O modelo de base de dados implementa uma ou duas máquinas virtuais que pode utilizar para instalar o sistema de gestão relacional de bases de dados (RDBMS) para um sistema SAP. Por exemplo, se implementar um modelo ASCS/SCS para cinco sistemas SAP, precisa de implementar este modelo cinco vezes.

Para configurar o modelo multi-SID da base de dados, no [modelo multi-SID][sap-templates-3-tier-multisid-db-marketplace-image]da base de dados, introduza valores para os seguintes parâmetros:

- **Id do sistema de seiva.** Introduza o ID do sistema SAP do sistema SAP que pretende instalar. O ID será usado como prefixo para os recursos que são implantados.
- **Os Tipo**. Selecione o sistema operativo das máquinas virtuais.
- **Dbtype.** Selecione o tipo de base de dados que pretende instalar no cluster. Selecione **SQL** se pretender instalar o Microsoft SQL Server. Selecione **HANA** se planeia instalar o SAP HANA nas máquinas virtuais. Certifique-se de selecionar o tipo correto do sistema operativo: selecione **Windows** para SQL e selecione uma distribuição Linux para HANA. O Balancer de carga Azure que está ligado às máquinas virtuais será configurado para suportar o tipo de base de dados selecionado:
  * **SQL**. O equilibrador de carga irá equilibrar a porta 1433. Certifique-se de que utiliza esta porta para o seu Servidor SQL Sempre na configuração.
  * **HANA.** O equilibrador de carga irá equilibrar as portas 35015 e 35017. Certifique-se de instalar o SAP HANA com a instância número **50**.
  O equilibrador de carga utilizará a porta de sonda 62550.
- **Tamanho do sistema de seiva.** Detete o número de SAPS que o novo sistema irá fornecer. Se não tem a certeza de quantos SAPS o sistema irá necessitar, pergunte ao seu Parceiro de Tecnologia SAP ou integrador de sistemas.
- **Disponibilidade do Sistema**. Selecione **HA**.
- **Nome de utilizador e senha**de administrador. Crie um novo utilizador que possa ser usado para iniciar sessão na máquina.
- **Id subnet**. Introduza o ID da sub-rede que utilizou durante a implantação do modelo ASCS/SCS, ou o ID da sub-rede que foi criada como parte da implantação do modelo ASCS/SCS.

#### <a name="application-servers-template"></a><a name="application-servers-template"></a>Modelo de servidores de aplicação

O modelo de servidores de aplicações implementa duas ou mais máquinas virtuais que podem ser usadas como instâncias do Servidor de Aplicações SAP para um sistema SAP. Por exemplo, se implementar um modelo ASCS/SCS para cinco sistemas SAP, precisa de implementar este modelo cinco vezes.

Para configurar o modelo multi-SID dos servidores de aplicações, no [modelo multi-SID][sap-templates-3-tier-multisid-apps-marketplace-image]dos servidores de aplicações, introduza valores para os seguintes parâmetros:

  -  **Id do sistema de seiva.** Introduza o ID do sistema SAP do sistema SAP que pretende instalar. O ID será usado como prefixo para os recursos que são implantados.
  -  **Os Tipo**. Selecione o sistema operativo das máquinas virtuais.
  -  **Tamanho do sistema de seiva.** O número de SAPS que o novo sistema irá fornecer. Se não tem a certeza de quantos SAPS o sistema irá necessitar, pergunte ao seu Parceiro de Tecnologia SAP ou integrador de sistemas.
  -  **Disponibilidade do Sistema**. Selecione **HA**.
  -  **Nome de utilizador e senha**de administrador. Crie um novo utilizador que possa ser usado para iniciar sessão na máquina.
  -  **Id subnet**. Introduza o ID da sub-rede que utilizou durante a implantação do modelo ASCS/SCS, ou o ID da sub-rede que foi criada como parte da implantação do modelo ASCS/SCS.


### <a name="azure-virtual-network"></a><a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a>Rede virtual Azure
No nosso exemplo, o espaço de endereço sintetizador da rede virtual Azure é de 10.0.0.0.0/16. Há uma sub-rede chamada **Subnet,** com uma gama de endereços de 10.0.0.0/24. Todas as máquinas virtuais e os equilibradores internos de carga são implantados nesta rede virtual.

> [!IMPORTANT]
> Não faça alterações nas definições de rede dentro do sistema operativo do hóspede. Isto inclui endereços IP, servidores DNS e sub-rede. Configure todas as definições da sua rede em Azure. O protocolo de configuração do hospedeiro dinâmico (DHCP) propaga as suas definições.
>
>

### <a name="dns-ip-addresses"></a><a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a>Endereços IP DNS

Para definir os endereços IP dNS necessários, faça os seguintes passos.

1. No portal Azure, na lâmina dos **servidores DNS,** certifique-se de que a opção de **servidores DNS** da rede virtual está definida para **DNS Personalizado**.
2. Selecione as suas definições com base no tipo de rede que tem. Para obter mais informações, consulte os seguintes recursos:
   * Adicione os endereços IP dos servidores DNS no local.  
   Pode estender os servidores DNS no local às máquinas virtuais que estão a funcionar em Azure. Nesse cenário, pode adicionar os endereços IP das máquinas virtuais Azure nas quais executa o serviço DNS.
   * Para implementações isoladas em Azure: Implemente uma máquina virtual adicional na mesma instância de Rede Virtual que serve como servidor DNS. Adicione os endereços IP das máquinas virtuais Azure que criou para executar o serviço DNS.

   ![Figura 12: Configure servidores DNS para rede virtual Azure][sap-ha-guide-figure-3001]

   _**Figura 12:** Configure servidores DNS para rede virtual Azure_

   > [!NOTE]
   > Se alterar os endereços IP dos servidores DNS, tem de reiniciar as máquinas virtuais Azure para aplicar a alteração e propagar os novos servidores DNS.
   >
   >

No nosso exemplo, o serviço DNS está instalado e configurado nestas máquinas virtuais do Windows:

| Papel de máquina virtual | Nome do hospedeiro da máquina virtual | Nome do cartão de rede | Endereço IP estático |
| --- | --- | --- | --- |
| Primeiro servidor DNS |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| Segundo servidor DNS |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

### <a name="host-names-and-static-ip-addresses-for-the-sap-ascsscs-clustered-instance-and-dbms-clustered-instance"></a><a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a>Nomes de anfitriões e endereços IP estáticos para a instância agrupada SAP ASCS/SCS e instância agrupada DE DBMS

Para a implantação no local, você precisa destes nomes de anfitriões reservados e endereços IP:

| Papel de nome de anfitrião virtual | Nome de anfitrião virtual | Endereço IP estático virtual |
| --- | --- | --- |
| Primeiro nome de anfitrião virtual do cluster SAP ASCS/SCS (para gestão de clusters) |pr1-ascs-vir |10.0.0.42 |
| Nome de anfitrião virtual sap ASCS/SCS |pr1-ascs-seiva |10.0.0.43 |
| Nome de anfitrião virtual do segundo cluster SAP DBMS (gestão do cluster) |pr1-dbms-vir |10.0.0.32 |

Quando criar o cluster, crie os nomes de anfitriões virtuais **pr1-ascs-vir** e **pr1-dbms-vir** e os endereços IP associados que gerem o próprio cluster. Para obter informações sobre como fazê-lo, consulte Recolher nós de [cluster numa configuração de cluster][sap-ha-guide-8.12.1].

Pode criar manualmente os outros dois nomes de anfitriões virtuais, **pr1-ascs-seap** e **pr1-dbms-seiva,** e os endereços IP associados, no servidor DNS. A instância SAP ASCS/SCS agrupada e a instância DBMS agrupada utilizam estes recursos. Para obter informações sobre como fazê-lo, consulte [Criar um nome de anfitrião virtual para uma instância SAP ASCS/SCS agrupada][sap-ha-guide-9.1.1].

### <a name="set-static-ip-addresses-for-the-sap-virtual-machines"></a><a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a>Definir endereços IP estáticos para as máquinas virtuais SAP
Depois de colocar as máquinas virtuais a utilizar no seu cluster, precisa de definir endereços IP estáticos para todas as máquinas virtuais. Faça-o na configuração da Rede Virtual Azure e não no sistema operativo dos hóspedes.

1. No portal Azure, selecione **Resource Group Network** > **Card** > **Settings** > IP**Address**.
2. Na lâmina de **endereços IP,** em **Atribuição,** **selecione Static**. Na caixa de **endereços IP,** introduza o endereço IP que pretende utilizar.

   > [!NOTE]
   > Se alterar o endereço IP do cartão de rede, tem de reiniciar as máquinas virtuais Azure para aplicar a alteração.  
   >
   >

   ![Figura 13: Definir endereços IP estáticos para o cartão de rede de cada máquina virtual][sap-ha-guide-figure-3002]

   _**Figura 13:** Detete os endereços IP estáticos para o cartão de rede de cada máquina virtual_

   Repita este passo para todas as interfaces de rede, isto é, para todas as máquinas virtuais, incluindo máquinas virtuais que pretende utilizar para o seu serviço Ative Directory/DNS.

No nosso exemplo, temos estas máquinas virtuais e endereços IP estáticos:

| Papel de máquina virtual | Nome do hospedeiro da máquina virtual | Nome do cartão de rede | Endereço IP estático |
| --- | --- | --- | --- |
| Primeira instância do servidor de aplicações SAP |pr1-di-0 |pr1-nic-di-0 |10.0.0.50 |
| Segunda instância do servidor de aplicações SAP |pr1-di-1 |pr1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| Última instância do Servidor de Aplicações SAP |pr1-di-5 |pr1-nic-di-5 |10.0.0.55 |
| Primeiro nó de cluster para instância ASCS/SCS |pr1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| Segundo nó de cluster para instância ASCS/SCS |pr1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| Primeiro nó de cluster para a instância DBMS |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| Segundo nó de cluster para a instância DBMS |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

### <a name="set-a-static-ip-address-for-the-azure-internal-load-balancer"></a><a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a>Defino um endereço IP estático para o equilibrador de carga interna Azure

O modelo SAP Azure Resource Manager cria um equilibrador de carga interna Azure que é utilizado para o cluster de instânciaS AsCS/SCS e o cluster DBMS.

> [!IMPORTANT]
> O endereço IP do nome de anfitrião virtual do SAP ASCS/SCS é o mesmo que o endereço IP do equilíbrio interno de carga SAP ASCS/SCS: **pr1-lb-ascs**.
> O endereço IP do nome virtual do DBMS é o mesmo que o endereço IP do equilíbrio interno de carga DBMS: **pr1-lb-dbms**.
>
>

Para definir um endereço IP estático para o equilibrador de carga interna Azure:

1. A implementação inicial define o endereço IP do equilíbrio interno para **Dynamic**. No portal Azure, na lâmina de **endereços IP,** em **Atribuição,** **selecione Static**.
2. Defino o endereço IP do equilibrador de carga interno **pr1-lb-ascs** no endereço IP do nome de hospedeiro virtual da instância SAP ASCS/SCS.
3. Defino o endereço IP do equilibrador de carga interno **pr1-lb-dbms** no endereço IP do nome de hospedeiro virtual da instância DBMS.

   ![Figura 14: Definir endereços IP estáticos para o equilibrador de carga interno para a instância SAP ASCS/SCS][sap-ha-guide-figure-3003]

   _**Figura 14:** Definir endereços IP estáticos para o equilibrador de carga interno para a instância SAP ASCS/SCS_

No nosso exemplo, temos dois equilibradores de carga internos Azure que têm estes endereços IP estáticos:

| Papel de equilibrador de carga interna azure | Nome do equilibrador de carga interna Azure | Endereço IP estático |
| --- | --- | --- |
| Balanceador interno de carga sAP ASCS/SCS |pr1-lb-ascs |10.0.0.43 |
| Balanceador de carga interna SAP DBMS |pr1-lb-dbms |10.0.0.33 |


### <a name="default-ascsscs-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a>Regras de equilíbrio de carga ASCS/SCS padrão para o equilibrador de carga interna Azure

O modelo SAP Azure Resource Manager cria as portas de que necessita:
* Uma instância AsCS ABAP, com a instância padrão número **00**
* Uma instância Java SCS, com a instância padrão número **01**

Quando instalar a sua instância SAP ASCS/SCS, deve utilizar o número de instância padrão **00** para a sua instância AsCS ABAP e a instância padrão número **01** para a sua instância Java SCS.

Em seguida, crie pontos finais de equilíbrio de carga interna necessários para as portas SAP NetWeaver.

Para criar pontos finais de equilíbrio interno de carga necessários, em primeiro lugar, crie estes pontos finais de equilíbrio de carga para as portas ASCS SAP NetWeaver ABAP:

| Nome da regra de equilíbrio de serviço/carga | Números de porta padrão | Portas de betão para (instância ASCS com número de exemplo 00) (ERS com 10) |
| --- | --- | --- |
| Enqueue Server / *lbrule3200* |32<*Número de instâncias*> |3200 |
| Servidor de mensagem ABAP / *lbrule3600* |36 *<Número de Casos*> |3600 |
| Mensagem Interna ABAP / *lbrule3900* |39<*Número de casos*> |3900 |
| Servidor de Mensagem HTTP / *Lbrule8100* |81<*Número de Instâncias*> |8100 |
| Serviço de Arranque SAP ASCS HTTP / *Lbrule50013* |5 *<InstânciaNúmero*>13 |50013 |
| Serviço de Arranque SAP ASCS HTTPS / *Lbrule50014* |5 *<Instâncianúmero*>14 |50014 |
| Replicação Enqueue / *Lbrule50016* |5 *<Instâncianúmero*>16 |50016 |
| SAP Start Service ERS HTTP *Lbrule51013* |5 *<InstânciaNúmero*>13 |51013 |
| SAP Start Service ERS HTTP *Lbrule51014* |5 *<Instâncianúmero*>14 |51014 |
| Ganhe RM *Lbrule5985* | |5985 |
| Partilha de Ficheiros *Lbrule445* | |445 |

_**Quadro 1:** Números de porta dos casos SAP NetWeaver ABAP ASCS_

Em seguida, crie estes pontos finais de equilíbrio de carga para as portas SAP NetWeaver Java SCS:

| Nome da regra de equilíbrio de serviço/carga | Números de porta padrão | Portas de betão para (instância SCS com instância número 01) (ERS com 11) |
| --- | --- | --- |
| Enqueue Server / *lbrule3201* |32<*Número de instâncias*> |3201 |
| Gateway Server / *lbrule3301* |33<*Número de Casos*> |3301 |
| Java Message Server / *lbrule3900* |39<*Número de casos*> |3901 |
| Servidor de Mensagem HTTP / *Lbrule8101* |81<*Número de Instâncias*> |8101 |
| Serviço de Arranque SAP SCS HTTP / *Lbrule50113* |5 *<InstânciaNúmero*>13 |50113 |
| Serviço de Arranque SCS SCS HTTPS / *Lbrule50114* |5 *<Instâncianúmero*>14 |50114 |
| Replicação Enqueue / *Lbrule50116* |5 *<Instâncianúmero*>16 |50116 |
| SAP Start Service ERS HTTP *Lbrule51113* |5 *<InstânciaNúmero*>13 |51113 |
| SAP Start Service ERS HTTP *Lbrule51114* |5 *<Instâncianúmero*>14 |51114 |
| Ganhe RM *Lbrule5985* | |5985 |
| Partilha de Ficheiros *Lbrule445* | |445 |

_**Quadro 2:** Números de porta dos casos SAP NetWeaver Java SCS_

![Figura 15: Regras de equilíbrio de carga ASCS/SCS padrão para o equilibrador de carga interna Azure][sap-ha-guide-figure-3004]

_**Figura 15:** Regras de equilíbrio de carga ASCS/SCS padrão para o equilibrador de carga interna Azure_

Defino o endereço IP do equilibrador de carga **pr1-lb-dbms** no endereço IP do nome de hospedeiro virtual da instância DBMS.

### <a name="change-the-ascsscs-default-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a>Alterar as regras de equilíbrio da carga padrão ASCS/SCS para o equilibrador de carga interna Azure

Se pretender utilizar diferentes números para as instâncias SAP ASCS ou SCS, tem de alterar os nomes e valores das suas portas a partir de valores predefinidos.

1. No portal Azure, selecione >  ** < *SID*>-lb-ascs carregar**regras de equilíbrio de carga de**carga**.
2. Para todas as regras de equilíbrio de carga que pertencem à instância SAP ASCS ou SCS, altere estes valores:

   * Nome
   * Porta
   * Porto de back-end

   Por exemplo, se pretender alterar o número de instância ASCS padrão de 00 para 31, tem de efazer as alterações para todas as portas listadas no Quadro 1.

   Aqui está um exemplo de uma atualização para o port *lbrule3200*.

   ![Figura 16: Alterar as regras de equilíbrio da carga padrão ASCS/SCS para o equilibrador de carga interna Azure][sap-ha-guide-figure-3005]

   _**Figura 16:** Alterar as regras de equilíbrio da carga padrão ASCS/SCS para o equilibrador de carga interna Azure_

### <a name="add-windows-virtual-machines-to-the-domain"></a><a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a>Adicione máquinas virtuais do Windows ao domínio

Depois de atribuir um endereço IP estático às máquinas virtuais, adicione as máquinas virtuais ao domínio.

![Figura 17: Adicione uma máquina virtual a um domínio][sap-ha-guide-figure-3006]

_**Figura 17:** Adicione uma máquina virtual a um domínio_

### <a name="add-registry-entries-on-both-cluster-nodes-of-the-sap-ascsscs-instance"></a><a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a>Adicione entradas de registo em ambos os nós de cluster da instância SAP ASCS/SCS

O Azure Load Balancer tem um equilibrador de carga interna que fecha as ligações quando as ligações estão inativas durante um determinado período de tempo (um tempo inativo). Os processos de trabalho da SAP em casos de diálogo abrem ligações ao processo de fila sap logo que o primeiro pedido de fila/defilação precisa de ser enviado. Estas ligações geralmente permanecem estabelecidas até que o processo de trabalho ou o processo de fila reinicie. No entanto, se a ligação estiver inativa durante um determinado período de tempo, o equilibrista de carga interna Azure fecha as ligações. Isto não é um problema porque o processo de trabalho da SAP restabelece a ligação ao processo de fila se já não existir. Estas atividades estão documentadas nos vestígios de desenvolvedores de processos SAP, mas criam uma grande quantidade de conteúdo extra nesses vestígios. É uma boa ideia mudar o TCP/IP `KeepAliveTime` e `KeepAliveInterval` em ambos os nós de cluster. Combine estas alterações nos parâmetros TCP/IP com os parâmetros de perfil SAP, descritos posteriormente no artigo.

Para adicionar entradas de registo em ambos os nós de cluster da instância SAP ASCS/SCS, em primeiro lugar, adicione estas entradas de registo windows em ambos os nós do cluster Windows para SAP ASCS/SCS:

| Caminho | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Nome da variável |`KeepAliveTime` |
| Tipo variável |REG_DWORD (Decimal) |
| Valor |120000 |
| Ligação à documentação |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

_**Tabela 3:** Alterar o primeiro parâmetro TCP/IP_

Em seguida, adicione estas entradas de registo do Windows em ambos os nós do cluster Windows para SAP ASCS/SCS:

| Caminho | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Nome da variável |`KeepAliveInterval` |
| Tipo variável |REG_DWORD (Decimal) |
| Valor |120000 |
| Ligação à documentação |[https://technet.microsoft.com/library/cc957548.aspx](https://technet.microsoft.com/library/cc957548.aspx) |

_**Quadro 4:** Alterar o segundo parâmetro TCP/IP_

**Para aplicar as alterações, reinicie os dois nós**do cluster .

### <a name="set-up-a-windows-server-failover-clustering-cluster-for-an-sap-ascsscs-instance"></a><a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a>Configurar um cluster de clustering de falha do servidor do Windows para uma instância SAP ASCS/SCS

A criação de um cluster de clusterde falha do servidor do Windows para uma instância SAP ASCS/SCS envolve estas tarefas:

- Recolher os nós do cluster numa configuração de cluster
- Configurar uma testemunha de partilha de ficheiros de cluster

#### <a name="collect-the-cluster-nodes-in-a-cluster-configuration"></a><a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a>Recolher os nós do cluster numa configuração de cluster

1. No Add Role and Features Wizard, adicione o agrupamento de falhas a ambos os nós do cluster.
2. Configurar o cluster failover utilizando o Failover Cluster Manager. No Failover Cluster Manager, selecione **Create Cluster**, e adicione apenas o nome do primeiro cluster, nó A. Não adicione o segundo nó ainda; você vai adicionar o segundo nó em um passo posterior.

   ![Figura 18: Adicione o nome do servidor ou máquina virtual do primeiro nó de cluster][sap-ha-guide-figure-3007]

   _**Figura 18:** Adicione o nome do servidor ou máquina virtual do primeiro nó de cluster_

3. Introduza o nome da rede (nome de anfitrião virtual) do cluster.

   ![Figura 19: Introduza o nome do cluster][sap-ha-guide-figure-3008]

   _**Figura 19:** Introduza o nome do cluster_

4. Depois de criar o cluster, faça um teste de validação de cluster.

   ![Figura 20: Executar a verificação de validação do cluster][sap-ha-guide-figure-3009]

   _**Figura 20:** Executar a verificação de validação do cluster_

   Pode ignorar quaisquer avisos sobre discos nesta altura do processo. Você adicionará uma testemunha de partilha de ficheiros e os discos partilhados sIOS mais tarde. Nesta fase, não precisas de te preocupar em ter um quórum.

   ![Figura 21: Não é encontrado nenhum disco de quórum][sap-ha-guide-figure-3010]

   _**Figura 21:** Nenhum disco de quórum é encontrado_

   ![Figura 22: O recurso core cluster precisa de um novo endereço IP][sap-ha-guide-figure-3011]

   _**Figura 22:** O recurso core cluster precisa de um novo endereço IP_

5. Altere o endereço IP do serviço de cluster core. O cluster não pode arrancar até alterar o endereço IP do serviço de cluster core, porque o endereço IP do servidor aponta para um dos nós da máquina virtual. Faça isso na página **propriedades** do recurso IP do serviço de cluster central.

   Por exemplo, precisamos atribuir um endereço IP (no nosso exemplo, **10.0.0.42**) para o nome de anfitrião virtual do cluster **pr1-ascs-vir**.

   ![Figura 23: Na caixa de diálogo Properties, altere o endereço IP][sap-ha-guide-figure-3012]

   _**Figura 23:** Na caixa de diálogo **Properties,** altere o endereço IP_

   ![Figura 24: Atribuir o endereço IP reservado para o cluster][sap-ha-guide-figure-3013]

   _**Figura 24:** Atribuir o endereço IP reservado para o cluster_

6. Traga o nome de anfitrião virtual do cluster on-line.

   ![Figura 25: O serviço de núcleo de cluster está em funcionamento, e com o endereço IP correto][sap-ha-guide-figure-3014]

   _**Figura 25:** O serviço core cluster está em funcionamento, e com o endereço IP correto_

7. Adicione o segundo nó de cluster.

   Agora que o serviço de cluster principal está em funcionamento, pode adicionar o segundo nó de cluster.

   ![Figura 26: Adicione o segundo nó de cluster][sap-ha-guide-figure-3015]

   _**Figura 26:** Adicione o segundo nó de cluster_

8. Insira um nome para o segundo anfitrião do nó cluster.

   ![Figura 27: Introduza o segundo nome de anfitrião do nó cluster][sap-ha-guide-figure-3016]

   _**Figura 27:** Insira o segundo nome anfitrião do nó cluster_

   > [!IMPORTANT]
   > Certifique-se de que o Adicionar todo o **armazenamento elegível à** caixa de verificação do cluster **NÃO** está selecionado.  
   >
   >

   ![Figura 28: Não selecione a caixa de verificação][sap-ha-guide-figure-3017]

   _**Figura 28:** Não **not** selecione a caixa de verificação_

   Pode ignorar avisos sobre quórum e discos. Irá definir o quórum e partilhar o disco mais tarde, conforme descrito na Instalação da SIOS DataKeeper Cluster Edition para o disco de partilha de [cluster SAP ASCS/SCS][sap-ha-guide-8.12.3].

   ![Figura 29: Ignore os avisos sobre o quórum do disco][sap-ha-guide-figure-3018]

   _**Figura 29:** Ignore avisos sobre o quórum do disco_


#### <a name="configure-a-cluster-file-share-witness"></a><a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a>Configure uma testemunha de partilha de ficheiros de cluster

Configurar uma testemunha de partilha de ficheiros de cluster envolve estas tarefas:

- Criação de uma partilha de ficheiros
- Definição do quórum de partilha de ficheiros em Failover Cluster Manager

##### <a name="create-a-file-share"></a><a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a>Criar uma partilha de ficheiros

1. Selecione uma testemunha de partilha de ficheiros em vez de um disco de quórum. O SIOS DataKeeper suporta esta opção.

   Nos exemplos deste artigo, a testemunha de partilha de ficheiros está no servidor Ative Directory/DNS que está a funcionar em Azure. A testemunha de partilha de ficheiros **chama-se Domcontr-0.** Como teria configurado uma ligação VPN ao Azure (via Site-to-Site VPN ou Azure ExpressRoute), o seu serviço Ative Directory/DNS está no local e não é adequado para executar uma testemunha de partilha de ficheiros.

   > [!NOTE]
   > Se o seu serviço Ative Directory/DNS funcionar apenas no local, não configure a sua testemunha de partilha de ficheiros no sistema operativo Ative Directory/DNS Windows que esteja a funcionar no local. A latência da rede entre os nós de cluster em funcionamento em Azure e Ative Directory/DNS no local pode ser demasiado grande e causar problemas de conectividade. Certifique-se de configurar a testemunha de partilha de ficheiros numa máquina virtual Azure que está a funcionar perto do nó de cluster.  
   >
   >

   A unidade de quórum precisa de pelo menos 1.024 MB de espaço livre. Recomendamos 2.048 MB de espaço livre para a unidade de quórum.

2. Adicione o objeto de nome do cluster.

   ![Figura 30: Atribuir as permissões na parte para o objeto de nome do cluster][sap-ha-guide-figure-3019]

   _**Figura 30:** Atribuir as permissões na parte para o objeto de nome do cluster_

   Certifique-se de que as permissões incluem a autoridade para alterar dados na parte para o objeto de nome do cluster (no nosso exemplo, **pr1-ascs-vir$**).

3. Para adicionar o objeto de nome do cluster à lista, **selecione Adicionar**. Mude o filtro para verificar se há objetos de computador, para além dos mostrados na Figura 31.

   ![Figura 31: Alterar os Tipos de Objetos para incluir computadores][sap-ha-guide-figure-3020]

   _**Figura 31:** Alterar os Tipos de Objetos para incluir computadores_

   ![Figura 32: Selecione a caixa de verificação de computadores][sap-ha-guide-figure-3021]

   _**Figura 32:** Selecione a caixa de verificação **de Computadores**_

4. Introduza o objeto de nome do cluster como mostrado na Figura 31. Como o disco já foi criado, pode alterar as permissões, como mostra a Figura 30.

5. Selecione o separador **de segurança** da parte e, em seguida, detete permissões mais detalhadas para o objeto de nome do cluster.

   ![Figura 33: Definir os atributos de segurança para o objeto de nome do cluster no quórum de partilha de ficheiros][sap-ha-guide-figure-3022]

   _**Figura 33:** Desdefinir os atributos de segurança para o objeto de nome do cluster no quórum de partilha de ficheiros_

##### <a name="set-the-file-share-witness-quorum-in-failover-cluster-manager"></a><a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a>Desloque o quórum de partilha de ficheiros no Failover Cluster Manager

1. Abra o Assistente de Definição de Quórum configurado.

   ![Figura 34: Inicie o assistente de definição de quórum de cluster configure][sap-ha-guide-figure-3023]

   _**Figura 34:** Inicie o assistente de definição de quórum de cluster configurar_

2. Na página de configuração do **Quórum Selecione** **Selecione a testemunha quórum**.

   ![Figura 35: Configurações de quórum que pode escolher][sap-ha-guide-figure-3024]

   _**Figura 35:** Configurações de quórum que pode escolher_

3. Na página 'Testemunha do **Quorum Select',** selecione **Configurar uma testemunha de partilha**de ficheiros .

   ![Figura 36: Selecione a testemunha de partilha de ficheiros][sap-ha-guide-figure-3025]

   _**Figura 36:** Selecione a testemunha de partilha de ficheiros_

4. Insira o caminho do CNU para \\a partilha de ficheiros (no nosso exemplo, domcontr-0\FSW). Para ver uma lista das alterações que pode fazer, selecione **Next**.

   ![Figura 37: Definir a localização da partilha de ficheiros para a partilha de testemunhas][sap-ha-guide-figure-3026]

   _**Figura 37:** Defina a localização da partilha de ficheiros para a partilha de testemunhas_

5. Selecione as alterações desejadas e, em seguida, selecione **Next**. É necessário reconfigurar com sucesso a configuração do cluster, como mostra a Figura 38.  

   ![Figura 38: Confirmação de que reconfigurou o cluster][sap-ha-guide-figure-3027]

   _**Figura 38:** Confirmação de que reconfigurou o cluster_

Depois de instalar com sucesso o Cluster Failover do Windows, é necessário fazer alterações em alguns limiares para adaptar a deteção de falhas às condições do Azure. Os parâmetros a serem alterados estão documentados neste blog: [https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834). Assumindo que os seus dois VMs que constroem a Configuração do Cluster Windows para ASCS/SCS estão na mesma SubNet, os seguintes parâmetros precisam de ser alterados para estes valores:  
- SameSubNetDelay = 2000  
- SameSubNetThreshold = 15  
- RoutHistoryLength = 30  

Estas configurações foram testadas com os clientes e proporcionaram um bom compromisso para serem suficientemente resistentes de um lado. Por outro lado, essas definições estavam a fornecer falhas suficientemente rápidas em condições reais de erro no software SAP ou na falha do nó/VM. 

### <a name="install-sios-datakeeper-cluster-edition-for-the-sap-ascsscs-cluster-share-disk"></a><a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a>Instale a Edição de Cluster SIOS DataKeeper para o disco de partilha de cluster SAP ASCS/SCS

Tem agora uma configuração de clustering de falha do Servidor Windows em Funcionamento no Azure. Mas, para instalar uma instância SAP ASCS/SCS, precisa de um recurso de disco partilhado. Não é possível criar os recursos de disco partilhados de que necessita em Azure. SIOS DataKeeper Cluster Edition é uma solução de terceiros que pode usar para criar recursos de disco partilhado.

A instalação da Edição de Cluster SIOS DataKeeper para o disco de partilha de cluster SAP ASCS/SCS envolve estas tarefas:

- Adicionar o .NET Framework 3.5
- Instalação do DataKeeper SIOS
- Criação de DataKeeper SIOS

#### <a name="add-the-net-framework-35"></a><a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a>Adicione a .NET Framework 3.5
O Microsoft .NET Framework 3.5 não é ativado ou instalado automaticamente no Windows Server 2012 R2. Uma vez que o SIOS DataKeeper exige que a .NET Framework esteja em todos os nós em que instala o DataKeeper, tem de instalar a .NET Framework 3.5 no sistema operativo de todos os utilizadores de todas as máquinas virtuais do cluster.

Existem duas formas de adicionar o .NET Framework 3.5:

- Utilize o Assistente de Adicionar e Funcionalidades no Windows, conforme mostrado na Figura 39.

  ![Figura 39: Instale a .NET Framework 3.5 utilizando o Assistente de Adicionar e Funcionalidades][sap-ha-guide-figure-3028]

  _**Figura 39:** Instale a .NET Framework 3.5 utilizando o Assistente de Adicionar e Funcionalidades_

  ![Figura 40: Barra de progresso de instalação quando instalar a .NET Framework 3.5 utilizando o Assistente de Adicionar e Funcionalidades][sap-ha-guide-figure-3029]

  _**Figura 40:** Barra de progresso de instalação quando instalar a .NET Framework 3.5 utilizando o Assistente de Adicionar e Funcionalidades_

- Utilize o dism.exe da ferramenta de linha de comando. Para este tipo de instalação, é necessário aceder ao diretório SxS nos meios de instalação windows. A uma solicitação de comando elevado, escreva:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

#### <a name="install-sios-datakeeper"></a><a name="dd41d5a2-8083-415b-9878-839652812102"></a>Instalar datakeeper SIOS

Instale a Edição de Cluster SIOS DataKeeper em cada nó do cluster. Para criar armazenamento partilhado virtual com o SIOS DataKeeper, crie um espelho sincronizado e, em seguida, simular o armazenamento partilhado do cluster.

Antes de instalar o software SIOS, crie o utilizador de domínio **DataKeeperSvc**.

> [!NOTE]
> Adicione o utilizador **DataKeeperSvc** ao grupo **Deadministrador Local** em ambos os nós do cluster.
>
>

Para instalar o DataKeeper SIOS:

1. Instale o software SIOS em ambos os nós do cluster.

   ![Instalador SIOS][sap-ha-guide-figure-3030]

   ![Figura 41: Primeira página da instalação SIOS DataKeeper][sap-ha-guide-figure-3031]

   _**Figura 41:** Primeira página da instalação SIOS DataKeeper_

2. Na caixa de diálogo mostrada na Figura 42, selecione **Sim**.

   ![Figura 42: DataKeeper informa que um serviço será desativado][sap-ha-guide-figure-3032]

   _**Figura 42:** DataKeeper informa que um serviço será desativado_

3. Na caixa de diálogo mostrada na Figura 43, recomendamos que selecione **a conta De domínio ou servidor**.

   ![Figura 43: Seleção de utilizadores para DataKeeper SIOS][sap-ha-guide-figure-3033]

   _**Figura 43:** Seleção de utilizadores para SIOS DataKeeper_

4. Introduza o nome de utilizador da conta de domínio e as palavras-passe que criou para o SIOS DataKeeper.

   ![Figura 44: Introduza o nome de utilizador de domínio e a palavra-passe para a instalação SIOS DataKeeper][sap-ha-guide-figure-3034]

   _**Figura 44:** Introduza o nome de utilizador de domínio e a palavra-passe para a instalação SIOS DataKeeper_

5. Instale a chave de licença para a sua instância SIOS DataKeeper, como mostra a Figura 45.

   ![Figura 45: Introduza a sua chave de licença SIOS DataKeeper][sap-ha-guide-figure-3035]

   _**Figura 45:** Insira a sua chave de licença SIOS DataKeeper_

6. Quando solicitado, reinicie a máquina virtual.

#### <a name="set-up-sios-datakeeper"></a><a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a>Configurar datakeeper SIOS

Depois de instalar o SIOS DataKeeper em ambos os nós, tem de iniciar a configuração. O objetivo da configuração é ter replicação de dados sincronizados entre os VHDs adicionais ligados a cada uma das máquinas virtuais.

1. Inicie a ferramenta de gestão e configuração do DataKeeper e, em seguida, selecione **Connect Server**. (Na Figura 46, esta opção é rodeada a vermelho.)

   ![Figura 46: Ferramenta de Gestão e Configuração de Gestão e Configuração de DataKeeper sIOS][sap-ha-guide-figure-3036]

   _**Figura 46:** Ferramenta de gestão e configuração do DataKeeper SIOS_

2. Introduza o nome ou endereço TCP/IP do primeiro nó a ferramenta de gestão e configuração deve ligar-se e, num segundo passo, o segundo nó.

   ![Figura 47: Insira o nome ou endereço TCP/IP do primeiro nó a ferramenta de gestão e configuração deve ligar-se, e num segundo passo, o segundo nó][sap-ha-guide-figure-3037]

   _**Figura 47:** Insira o nome ou endereço TCP/IP do primeiro nó a ferramenta de gestão e configuração deve ligar-se, e num segundo passo, o segundo nó_

3. Crie o trabalho de replicação entre os dois nós.

   ![Figura 48: Criar um trabalho de replicação][sap-ha-guide-figure-3038]

   _**Figura 48:** Criar um trabalho de replicação_

   Um feiticeiro guia-o através do processo de criação de um trabalho de replicação.
4. Defina o nome, endereço TCP/IP e volume de disco do nó de origem.

   ![Figura 49: Definir o nome do trabalho de replicação][sap-ha-guide-figure-3039]

   _**Figura 49:** Defina o nome do trabalho de replicação_

   ![Figura 50: Definir os dados base para o nó, que deve ser o nó de origem atual][sap-ha-guide-figure-3040]

   _**Figura 50:** Defina os dados base para o nó, que deve ser o nó de origem atual_

5. Defina o nome, endereço TCP/IP e volume de disco do nó alvo.

   ![Figura 51: Definir os dados base para o nó, que deve ser o nó alvo atual][sap-ha-guide-figure-3041]

   _**Figura 51:** Defina os dados base para o nó, que deve ser o nó alvo atual_

6. Defina os algoritmos de compressão. No nosso exemplo, recomendamos que comprima o fluxo de replicação. Especialmente em situações de ressincronização, a compressão do fluxo de replicação reduz drasticamente o tempo de ressincronização. Note que a compressão utiliza os recursos cpu e RAM de uma máquina virtual. À medida que a taxa de compressão aumenta, também o volume de recursos de CPU é utilizado. Também pode ajustar esta definição mais tarde.

7. Outra definição que precisa de verificar é se a replicação ocorre de forma assíncrona ou sincronizada. *Quando proteger as configurações SAP ASCS/SCS, deve utilizar uma replicação sincronizada*.  

   ![Figura 52: Definir detalhes de replicação][sap-ha-guide-figure-3042]

   _**Figura 52:** Definir detalhes de replicação_

8. Defina se o volume replicado pelo trabalho de replicação deve ser representado para uma configuração de cluster de clusterde falhade do Servidor do Windows como um disco partilhado. Para a configuração SAP ASCS/SCS, selecione **Sim** para que o cluster Windows veja o volume replicado como um disco partilhado que pode usar como volume de cluster.

   ![Figura 53: Selecione Sim para definir o volume replicado como um volume de cluster][sap-ha-guide-figure-3043]

   _**Figura 53:** Selecione **Sim** para definir o volume replicado como um volume de cluster_

   Após a criação do volume, a ferramenta dataKeeper Management and Configuration mostra que o trabalho de replicação está ativo.

   ![Figura 54: O espelhamento sincronizado do DataKeeper para o disco de partilha SAP ASCS/SCS está ativo][sap-ha-guide-figure-3044]

   _**Figura 54:** O espelhamento sincronizado dataKeeper para o disco de partilha SAP ASCS/SCS está ativo_

   O Failover Cluster Manager mostra agora o disco como um disco DataKeeper, como mostra a Figura 55.

   ![Figura 55: Gestor de cluster de failover mostra o disco que dataKeeper replicado][sap-ha-guide-figure-3045]

   _**Figura 55:** Failover Cluster Manager mostra o disco que dataKeeper replicava_

## <a name="install-the-sap-netweaver-system"></a><a name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a>Instalar o sistema SAP NetWeaver

Não descreveremos a configuração do DBMS porque as configurações variam consoante o sistema DBMS que utiliza. No entanto, assumimos que as preocupações de alta disponibilidade com o DBMS são abordadas com as funcionalidades que os diferentes fornecedores dbmS suportam para o Azure. Por exemplo, sempre on ou base de dados espelhando para SQL Server, e Oracle Data Guard para bases de dados Oracle. No cenário que usamos neste artigo, não adicionamos mais proteção ao DBMS.

Não existem considerações especiais quando diferentes serviços DBMS interagem com este tipo de configuração SAP ASCS/SCS agrupada em Azure.

> [!NOTE]
> Os procedimentos de instalação dos sistemas SAP NetWeaver ABAP, sistemas Java e ABAP+Java são quase idênticos. A diferença mais significativa é que um sistema SAP ABAP tem uma instância ASCS. O sistema SAP Java tem uma instância SCS. O sistema SAP ABAP+Java tem uma instância ASCS e uma instância SCS a funcionar no mesmo grupo de clusters da Microsoft failover. Quaisquer diferenças de instalação para cada pilha de instalação SAP NetWeaver são explicitamente mencionadas. Pode supor que todas as outras partes são iguais.  
>
>

### <a name="install-sap-with-a-high-availability-ascsscs-instance"></a><a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>Instale o SAP com uma instância ASCS/SCS de alta disponibilidade

> [!IMPORTANT]
> Certifique-se de que não coloca o ficheiro da página em volumes espelhados dataKeeper. DataKeeper não suporta volumes espelhados. Pode deixar o ficheiro de página na unidade temporária D de uma máquina virtual Azure, que é a falha. Se ainda não estiver lá, mova o ficheiro da página do Windows para conduzir D da sua máquina virtual Azure.
>
>

Instalar o SAP com uma instância ASCS/SCS de alta disponibilidade envolve estas tarefas:

- Criação de um nome de anfitrião virtual para a instância SAP ASCS/SCS agrupada
- Instalação do primeiro nó de cluster SAP
- Modificação do perfil SAP da instância ASCS/SCS
- Adicionar uma porta de sonda
- Abrir a porta de sonda de firewall windows

#### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a><a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>Criar um nome de anfitrião virtual para a instância SAP ASCS/SCS agrupada

1. No gestor do DNS do Windows, crie uma entrada DNS para o nome de anfitrião virtual da instância ASCS/SCS.

   > [!IMPORTANT]
   > O endereço IP que atribui ao nome de anfitrião virtual da instância ASCS/SCS deve ser o mesmo que o endereço IP que atribuiu ao Azure Load Balancer**<*(SID*>-lb-ascs).**  
   >
   >

   O endereço IP do nome de anfitrião virtual SAP ASCS/SCS **(pr1-ascs-seiva**) é o mesmo que o endereço IP do Azure Load Balancer **(pr1-lb-ascs).**

   ![Figura 56: Definir a entrada dNS para o nome virtual do cluster SAP ASCS/SCS e endereço TCP/IP][sap-ha-guide-figure-3046]

   _**Figura 56:** Definir a entrada DNS para o nome virtual do cluster SAP ASCS/SCS e endereço TCP/IP_

2. Para definir o endereço IP atribuído ao nome de anfitrião virtual, selecione **DNS Manager** > **Domain**.

   ![Figura 57: Novo nome virtual e endereço TCP/IP para configuração do cluster SAP ASCS/SCS][sap-ha-guide-figure-3047]

   _**Figura 57:** Novo nome virtual e endereço TCP/IP para configuração de cluster SAP ASCS/SCS_

#### <a name="install-the-sap-first-cluster-node"></a><a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a>Instale o primeiro nó de cluster SAP

1. Execute a primeira opção do nó de cluster no nó de cluster A. Por exemplo, no hospedeiro **pr1-ascs-0.**
2. Para manter as portas padrão para o equilibrador de carga interna Azure, selecione:

   * **Sistema ABAP**: **Ascs** instância número **00**
   * **Sistema Java**: **SCS** exemplo número **01**
   * **Sistema ABAP+Java**: **AscS** exemplo número **00** e **scs** número **01**

   Para utilizar números de instância que não 00 para a instância AsCS ABAP e 01 para a instância Java SCS, primeiro é necessário alterar as regras de equilíbrio de carga por defeito do equilíbrio de carga interna Azure, descritas na Alteração das regras de equilíbrio de carga padrão [ASCS/SCS para o equilibrador de carga interna Azure][sap-ha-guide-8.9].

As próximas tarefas não são descritas na documentação padrão de instalação do SAP.

> [!NOTE]
> A documentação de instalação SAP descreve como instalar o primeiro nó de cluster ASCS/SCS.
>
>

#### <a name="modify-the-sap-profile-of-the-ascsscs-instance"></a><a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a>Modificar o perfil SAP da instância ASCS/SCS

Precisa adicionar um novo parâmetro de perfil. O parâmetro de perfil impede que as ligações entre os processos de trabalho do SAP e o servidor de fila de enfila fechem quando estão inativas durante demasiado tempo. Mencionamos o cenário problemático em Adicionar entradas de [registo em ambos os nós de cluster da instância SAP ASCS/SCS][sap-ha-guide-8.11]. Nessa secção, introduzimos também duas alterações a alguns parâmetros básicos de ligação TCP/IP. Num segundo passo, é necessário definir o servidor `keep_alive` de fila para enviar um sinal para que as ligações não atinjam o limiar de marcha lenta do equilíbrio interno do Azure.

Para modificar o perfil SAP da instância ASCS/SCS:

1. Adicione este parâmetro de perfil ao perfil de instância SAP ASCS/SCS:

   ```
   enque/encni/set_so_keepalive = true
   ```
   No nosso exemplo, o caminho é:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

   Por exemplo, para o perfil de instância SAP SCS e caminho correspondente:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2. Para aplicar as alterações, reinicie a instância SAP ASCS/SCS.

#### <a name="add-a-probe-port"></a><a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a>Adicione uma porta de sonda

Utilize a funcionalidade de sonda interna do equilibrante de carga para que toda a configuração do cluster funcione com o Azure Load Balancer. O equilibrador de carga interna Azure geralmente distribui a carga de trabalho de entrada igualmente entre as máquinas virtuais participantes. No entanto, isto não funcionará em algumas configurações de cluster porque apenas uma instância está ativa. A outra instância é passiva e não pode aceitar nenhuma carga de trabalho. Uma funcionalidade de sonda ajuda quando o balanceador de carga interna Azure atribui trabalho apenas a uma instância ativa. Com a funcionalidade da sonda, o equilibrador de carga interna pode detetar quais as instâncias ativas e, em seguida, visar apenas a instância com a carga de trabalho.

Para adicionar uma porta de sonda:

1. Verifique a definição atual **do ProbePort** executando o seguinte comando PowerShell. Execute-o dentro de uma das máquinas virtuais na configuração do cluster.

   ```PowerShell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
   ```

2. Defina uma porta de sonda. O número da porta da sonda padrão é **0**. No nosso exemplo, usamos a porta de sonda **62000**.

   ![Figura 58: A porta de sonda de configuração do cluster é 0 por padrão][sap-ha-guide-figure-3048]

   _**Figura 58:** A porta de sonda de configuração de cluster padrão é 0_

   O número da porta é definido nos modelos sAP Azure Resource Manager. Pode atribuir o número de porta no PowerShell.

   Para definir um novo valor ProbePort para o **SAP <*SID*>** recurso de cluster IP, execute o seguinte script PowerShell. Atualize as variáveis PowerShell para o seu ambiente. Após a eção do script, será solicitado a reiniciar o grupo de cluster SAP para ativar as alterações.

   ```PowerShell
   $SAPSID = "PR1"      # SAP <SID>
   $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

   Clear-Host
   $SAPClusterRoleName = "SAP $SAPSID"
   $SAPIPresourceName = "SAP $SAPSID IP"
   $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
   $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
   $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
   $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
   $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
   $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
   $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value

   $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }

   Write-Host "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" -ForegroundColor Cyan
   Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter

   Write-Host
   Write-Host "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." -ForegroundColor Cyan
   Write-Host
   Write-Host "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." -ForegroundColor Cyan
   Write-Host

   $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}

   Write-Host

   $ActivateChanges = Read-Host "Do you want to take restart SAP cluster role '$SAPClusterRoleName', to activate the changes (yes/no)?"

   if($ActivateChanges -eq "yes"){
   Write-Host
   Write-Host "Activating changes..." -ForegroundColor Cyan

   Write-Host
   write-host "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..." -ForegroundColor Cyan
   Stop-ClusterResource -Name $SAPIPresourceName
   sleep 5

   Write-Host "Starting SAP cluster role '$SAPClusterRoleName' ..." -ForegroundColor Cyan
   Start-ClusterGroup -Name $SAPClusterRoleName

   Write-Host "New ProbePort parameter is active." -ForegroundColor Green
   Write-Host

   Write-Host "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" -ForegroundColor Cyan
   Write-Host
   Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
   }else
   {
   Write-Host "Changes are not activated."
   }
   ```

   Depois de colocar online a função de cluster **SAP <*SID,* > ** verifique se o **ProbePort** está definido para o novo valor.

   ```PowerShell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

   ```

   ![Figura 59: Sondar a porta do cluster depois de definir o novo valor][sap-ha-guide-figure-3049]

   _**Figura 59:** Sondar a porta do cluster depois de definir o novo valor_

#### <a name="open-the-windows-firewall-probe-port"></a><a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a>Abra a porta de sonda de firewall windows

Tens de abrir uma porta de sonda de firewall do Windows em ambos os nós do cluster. Utilize o seguinte script para abrir uma porta de sonda de firewall Windows. Atualize as variáveis PowerShell para o seu ambiente.

  ```PowerShell
  $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

O **ProbePort** está definido para **62000**. Agora você pode aceder à partilha ** \\de ficheiros \ascsha-clsap\sapmnt** de outros anfitriões, como de **ascsha-dbas**.

### <a name="install-the-database-instance"></a><a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a>Instale a instância da base de dados

Para instalar a instância de base de dados, siga o processo descrito na documentação de instalação SAP.

### <a name="install-the-second-cluster-node"></a><a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a>Instale o segundo nó de cluster

Para instalar o segundo cluster, siga os passos do guia de instalação SAP.

### <a name="change-the-start-type-of-the-sap-ers-windows-service-instance"></a><a name="094bc895-31d4-4471-91cc-1513b64e406a"></a>Alterar o tipo de início da instância de serviço SAP ERS Windows

Altere o tipo de início do serviço SAP ERS Windows para **Automático (Arranque Atrasado)** em ambos os nós do cluster.

![Figura 60: Alterar o tipo de serviço para a instância SAP ERS para atraso automático][sap-ha-guide-figure-3050]

_**Figura 60:** Alterar o tipo de serviço para a instância SAP ERS para atraso automático_

### <a name="install-the-sap-primary-application-server"></a><a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a>Instalar o Servidor de Aplicações Primárias SAP

Instale a instância do Servidor de Aplicações Primárias (PAS) <*SID*>-di-0 na máquina virtual que designou para acolher o PAS. Não existem dependências em configurações específicas de Azure ou DataKeeper.

### <a name="install-the-sap-additional-application-server"></a><a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a>Instalar o Servidor de Aplicações Adicionais SAP

Instale um Servidor de Aplicações Adicional SAP (AAS) em todas as máquinas virtuais que designou para hospedar uma instância do Servidor de Aplicações SAP. Por exemplo, em <*SID*>-di-1 para&lt;&gt;<*SID*>-di-n .

> [!NOTE]
> Isto termina a instalação de um sistema SAP NetWeaver de alta disponibilidade. Em seguida, proceda com os testes de failover.
>


## <a name="test-the-sap-ascsscs-instance-failover-and-sios-replication"></a><a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a>Teste a falha da instância SAP ASCS/SCS e a replicação do SIOS
É fácil testar e monitorizar uma falha de falha na instância SAP ASCS/SCS e replicação do disco SIOS utilizando o Failover Cluster Manager e a ferramenta de gestão e configuração do DataKeeper sIOS.

### <a name="sap-ascsscs-instance-is-running-on-cluster-node-a"></a><a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a>A instância SAP ASCS/SCS está a funcionar no nó de cluster A

O grupo de cluster **SAP PR1** está a funcionar no nó de cluster A. Por exemplo, em **pr1-ascs-0**. Atribuir a unidade de disco partilhada S, que faz parte do grupo de cluster **SAP PR1,** e que a instância ASCS/SCS utiliza, para o nó de cluster A.

![Figura 61: Failover Cluster Manager: O Grupo de cluster sAP <SID> está a funcionar no nó de cluster A][sap-ha-guide-figure-5000]

_**Figura 61:** Failover Cluster Manager: O Grupo de cluster *sAP* <SID> está a funcionar no nó de cluster A_

Na ferramenta SIOS DataKeeper Management and Configuration, pode ver que os dados do disco partilhado são sincronizados replicados desde a unidade de volume de origem S no nó de cluster A até à unidade de volume alvo S no nó de cluster B. Por exemplo, é replicado de **pr1-ascs-0 [10.0.0.40]** a **pr1-ascs-1 [10.0.0.41]**.

![Figura 62: No SIOS DataKeeper, replicar o volume local do nó de cluster A ao nó de cluster B][sap-ha-guide-figure-5001]

_**Figura 62:** No SIOS DataKeeper, replicar o volume local do nó de cluster A ao nó de cluster B_

### <a name="failover-from-node-a-to-node-b"></a><a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a>Falha do nó A ao nó B

1. Escolha uma destas opções para iniciar uma falha do SAP <*SID*> cluster group do nó de cluster A ao nó de cluster B:
   - Utilizar o Gestor de Cluster failover  
   - Utilizar powerShell de cluster failover

   ```PowerShell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPClusterGroup = "SAP $SAPSID"
   Move-ClusterGroup -Name $SAPClusterGroup

   ```
2. Reinicie o nó de cluster A dentro do sistema operativo windows (isto inicia uma falha automática do SAP <*SID*> cluster group do nó A ao nó B).  
3. Reinicie o nó de cluster A do portal Azure (isto inicia uma falha automática do SAP <*SID*> cluster cluster do nó A ao nó B).  
4. Reinicie o nó de cluster A utilizando o Azure PowerShell (isto inicia uma falha automática do SAP <*SID*> cluster group do nó A ao nó B).

   Após a falha, o Grupo de cluster *sap* <SID> está a funcionar no nó B do cluster. Por exemplo, está a funcionar em **pr1-ascs-1**.

   ![Figura 63: No Failover Cluster Manager, o SAP <SID> cluster group está funcionando no nó de cluster B][sap-ha-guide-figure-5002]

   _**Figura 63**: No Failover Cluster Manager, o Grupo de cluster sAP <*SID*> está a funcionar no nó b do cluster_

   O disco partilhado está agora montado no nó de cluster B. SIOS DataKeeper está a replicar dados da unidade de volume de origem S no nó de cluster B para o volume de destino S no nó de cluster A. Por exemplo, está a replicar-se de **pr1-ascs-1 [10.0.0.41]** para **pr1-ascs-0 [10.0.0.40]**.

   ![Figura 64: SIOS DataKeeper replica o volume local do nó de cluster B ao nó de cluster A][sap-ha-guide-figure-5003]

   _**Figura 64:** SIOS DataKeeper replica o volume local do nó de cluster B ao nó de cluster A_
