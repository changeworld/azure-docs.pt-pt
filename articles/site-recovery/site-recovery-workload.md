---
title: Sobre a recuperação de desastres para apps no local com recuperação de site azure
description: Descreve as cargas de trabalho que podem ser protegidas com a recuperação após desastre, com o serviço do Azure Site Recovery.
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 2b901425a0020c0ccc7b834ee36d965910028018
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062828"
---
# <a name="about-disaster-recovery-for-on-premises-apps"></a>Acerca da recuperação após desastre de aplicações no local

Este artigo descreve cargas de trabalho e aplicações no local que pode proteger para recuperação de desastres com o serviço de recuperação de [sites azure.](site-recovery-overview.md)

## <a name="overview"></a>Descrição geral

As organizações precisam de uma estratégia de continuidade de negócios e recuperação de desastres (BCDR) para manter as cargas de trabalho e os dados seguros e disponíveis durante o tempo de paragem planeado e não planeado. E, recupere para as condições normais de trabalho.

A Recuperação de Sites é um serviço do Azure que contribui para a sua estratégia de BCDR. Através da Recuperação de Sites, pode implementar a replicação com reconhecimento de aplicações para a nuvem ou para um site secundário. Pode utilizar a Recuperação do Site para gerir a replicação, realizar testes de recuperação de desastres e executar falhas e failback. As suas aplicações podem ser executadas em computadores baseados no Windows ou no Linux, servidores físicos, VMware ou Hyper-V.

A Recovery do Site integra-se com aplicações da Microsoft como SharePoint, Exchange, Dynamics, SQL Server e Ative Directory. A Microsoft trabalha em estreita colaboração com os principais fornecedores, incluindo a Oracle, SAP e Red Hat. Pode personalizar soluções de replicação nas aplicações de forma individual.

## <a name="why-use-site-recovery-for-application-replication"></a>Porquê utilizar a Recuperação de Sites para replicação de aplicações?

A Recuperação de Sites contribui para a proteção e recuperação a nível de aplicação da seguinte forma:

- App-agnóstico e fornece replicação para qualquer carga de trabalho em funcionamento em uma máquina suportada.
- Replicação quase sincronizada, com objetivos de ponto de recuperação (RPO) tão baixo como 30 segundos para atender às necessidades das aplicações empresariais mais críticas.
- Instantâneos consistentes com aplicações para aplicações com uma ou múltiplas camadas.
- Integração com o SQL Server AlwaysOn, e parceria com outras tecnologias de replicação ao nível da aplicação. Por exemplo, replicação de Diretório Ativo, SQL AlwaysOn e Grupos de Disponibilidade de Dados de Dados de Câmbio (DAGs).
- Planos de recuperação flexíveis que lhe permitem recuperar uma pilha inteira de aplicações com um único clique, e incluir scripts externos e ações manuais no plano.
- Gestão avançada de rede em Recovery site e Azure para simplificar os requisitos da rede de aplicações. Gestão de rede, tais como a capacidade de reservar endereços IP, configurar o equilíbrio de carga e integração com o Gestor de Tráfego Azure para os objetivos de tempo de recuperação mais baixos (RTO) de transição de rede.
- Uma biblioteca de Automatização do Azure completa que fornece scripts específicos de aplicação, prontos para produção, que podem ser transferidos e integrados com os planos de recuperação.

## <a name="workload-summary"></a>Resumo da carga de trabalho

A Recuperação de Sites pode replicar qualquer aplicação em execução numa máquina. Estabelecemos uma parceria com equipas de produtos para fazer testes adicionais para as aplicações especificadas na tabela seguinte.

| **Carga de trabalho** |**Replicar VMs do Azure para o Azure** |**Replicar VMs Hyper-V para um site secundário** | **Replicar VMs Hyper-V para o Azure** | **Replicar VMs VMware para um site secundário** | **Replicar VMware VMs para Azure** |
| --- | --- | --- | --- | --- |---|
| Active Directory, DNS |Sim |Sim |Sim |Sim |Sim|
| Web Apps (IIS, SQL) |Sim |Sim |Sim |Sim |Sim|
| System Center Operations Manager |Sim |Sim |Sim |Sim |Sim|
| SharePoint |Sim |Sim |Sim |Sim |Sim|
| SAP<br/><br/>Replicar site SAP para o Azure de não cluster |Sim (testado pela Microsoft) |Sim (testado pela Microsoft) |Sim (testado pela Microsoft) |Sim (testado pela Microsoft) |Sim (testado pela Microsoft)|
| Exchange (não DAG) |Sim |Sim |Sim |Sim |Sim|
| Ambiente de Trabalho Remoto/VDI |Sim |Sim |Sim |Sim |Sim|
| Linux (sistema operativo e aplicações) |Sim (testado pela Microsoft) |Sim (testado pela Microsoft) |Sim (testado pela Microsoft) |Sim (testado pela Microsoft) |Sim (testado pela Microsoft)|
| Dynamics AX |Sim |Sim |Sim |Sim |Sim|
| Servidor de Ficheiros do Windows |Sim |Sim |Sim |Sim |Sim|
| Citrix XenApp e XenDesktop |Sim|N/D |Sim |N/D |Sim |

## <a name="replicate-active-directory-and-dns"></a>Replicar o Active Directory e o DNS

As infraestruturas de DNS e Active Directory são essenciais para a maioria das aplicações da empresa. Durante a recuperação de desastres, terá de proteger e recuperar estes componentes da infraestrutura, antes de recuperar cargas de trabalho e apps.

Pode utilizar a Recuperação de Sites para criar um plano de recuperação após desastre automatizado completa para Active Directory e DNS. Por exemplo, para falhar sobre o SharePoint e o SAP de um site primário para um site secundário, pode configurar um plano de recuperação que falha primeiro sobre o Ative Directory. Em seguida, use um plano adicional de recuperação específico para falhar sobre as outras aplicações que dependem do Ative Directory.

[Saiba mais](site-recovery-active-directory.md) sobre a recuperação de desastres para Ative Directory e DNS.

## <a name="protect-sql-server"></a>Proteger o SQL Server

O SQL Server fornece uma base de serviços de dados para muitas aplicações empresariais num centro de dados no local. A Recuperação do Site pode ser usada com tecnologias SQL Server HA/DR, para proteger aplicações empresariais de vários níveis que utilizam o Servidor SQL.

A Recuperação de Sites proporciona:

- Uma solução de recuperação após desastre simples e rentável para o SQL Server. Replique várias versões e edições de servidores independentes e clusters do SQL Server, para o Azure ou para um site secundário.
- Integração com grupos de disponibilidade AlwaysOn de SQL Server, para gerir a ativação e a reativação pós-falha com planos de recuperação do Azure Site Recovery.
- Planos de recuperação completos para todas as camadas numa aplicação, incluindo as bases de dados do SQL Server.
- Escalagem do SQL Server para cargas _bursting_ máximas com recuperação do site, explodindo-as em tamanhos de máquina virtual IaaS maiores em Azure.
- Testes fáceis de recuperação após desastres do SQL Server. Pode executar as ativações pós-falha de teste para analisar os dados e executar verificações de compatibilidade, sem afetar o seu ambiente de produção.

[Saiba mais](site-recovery-sql.md) sobre a recuperação de desastres para o servidor SQL.

## <a name="protect-sharepoint"></a>Proteger o SharePoint

O Azure Site Recovery ajuda a proteger as implementações do SharePoint da seguinte forma:

- Elimina a necessidade e os custos da infraestrutura associada a um farm autónomo na recuperação após desastre. Utilize a Recuperação do Site para replicar toda uma quinta (web, app e níveis de base de dados) para o Azure ou para um site secundário.
- Simplifica a gestão e implementação das aplicações. As atualizações implementadas no local principal são automaticamente replicadas. As atualizações estão disponíveis após a falha e recuperação de uma quinta num local secundário. Reduz a complexidade da gestão e os custos associados à manutenção de uma exploração stand-by até à data.
- Simplifica o desenvolvimento e o teste de aplicações de SharePoint através da criação de um ambiente de réplica de cópia a pedido do tipo produção para realizar testes e depuração.
- Simplifica a transição para a nuvem utilizando a Recuperação de Sites para migrar as implementações do SharePoint para o Azure.

[Saiba mais](site-recovery-sharepoint.md) sobre a recuperação de desastres para o SharePoint.

## <a name="protect-dynamics-ax"></a>Proteger Dynamics AX

O Azure Site Recovery ajuda a proteger a sua solução ERP de Dynamics AX:

- Gerir a replicação de todo o seu ambiente Dynamics AX (níveis Web e AOS, níveis de base de dados, SharePoint) para O Azure, ou para um site secundário.
- Simplificação da migração de implementações de Dynamics AX para a nuvem (Azure).
- Simplificação do desenvolvimento e teste de aplicações do Dynamics AX através de uma cópia do tipo produção a pedido para teste e depuração.

[Saiba mais](site-recovery-dynamicsax.md) sobre a recuperação de desastres para a Dynamic AX.

## <a name="protect-remote-desktop-services"></a>Proteja os serviços de ambiente de trabalho remoto

Os Serviços de Ambiente de Trabalho Remoto (RDS) permitem a infraestrutura virtual de desktop (VDI), desktops e aplicações baseadas em sessão, que permitem que os utilizadores trabalhem em qualquer lugar.

Com a Recuperação do Site Azure pode replicar os seguintes serviços:

- Replicar ambientes de trabalho virtuais geridos ou não geridos para um local secundário.
- Replicar aplicações e sessões remotas para um site secundário ou Azure.

A tabela que se segue mostra as opções de replicação:

| **RDS** |**Replicar VMs do Azure para o Azure** | **Replicar VMs Hyper-V para um site secundário** | **Replicar VMs Hyper-V para o Azure** | **Replicar VMs VMware para um site secundário** | **Replicar VMware VMs para Azure** | **Replicar servidores físicos para um site secundário** | **Replicar servidores físicos para o Azure** |
|---| --- | --- | --- | --- | --- | --- | --- |
| **Ambiente de Trabalho Virtual Agrupado (não gerido)** |Não|Sim |Não |Sim |Não |Sim |Não |
| **Ambiente de Trabalho Virtual Agrupado (gerido e sem UDP)** |Não|Sim |Não |Sim |Não |Sim |Não |
| **Aplicações remotas e sessões de Ambiente de Trabalho (sem UDP)** |Sim|Sim |Sim |Sim |Sim |Sim |Sim |

[Saiba mais](/windows-server/remote/remote-desktop-services/rds-disaster-recovery-with-azure) sobre a recuperação de desastres para rds.

## <a name="protect-exchange"></a>Proteger o Exchange

A Recuperação de Sites ajuda a proteger o Exchange da seguinte forma:

- Em pequenas implementações do Exchange, como servidores únicos ou autónomos, a Recuperação de Sites pode replicar e efetuar a ativação pós-falha para o Azure ou para um site secundário.
- Em grandes implementações, a Recuperação de Sites integra-se com os DAGs do Exchange.
- Os DAGs do Exchange são a solução recomendada para a recuperação após desastre do Exchange de uma empresa.  Os planos de recuperação da o Recuperação de Sites podem incluir DAGs para coordenar a ativação pós-falha do DAG entre sites.

Para saber mais sobre a recuperação de desastres para a Exchange, consulte [exchange DAGs](/Exchange/high-availability/database-availability-groups/database-availability-groups) e [Exchange disaster recovery](/Exchange/high-availability/disaster-recovery/disaster-recovery).

## <a name="protect-sap"></a>Proteger o SAP

Utilize a Recuperação de Sites para proteger a sua implementação de SAP da seguinte forma:

- Ative a proteção das aplicações SAP NetWeaver e não NetWeaver Production em execução no local, através da replicação de componentes para o Azure.
- Ative a proteção das aplicações SAP NetWeaver e não NetWeaver Production em execução no Azure, através da replicação de componentes para outro datacenter do Azure.
- Simplificar a migração para a nuvem utilizando a Recuperação de Sites para migrar a implementação do SAP para o Azure.
- Simplifique as atualizações, testes e protótipos de projetos SAP ao criar um clone de produção a pedido para testar aplicações SAP.

[Saiba mais](site-recovery-sap.md) sobre a recuperação de desastres para a SAP.

## <a name="protect-internet-information-services"></a>Proteger os Serviços de Informação da Internet

Utilize a Recuperação do Site para proteger a implementação dos serviços de informação da Internet (IIS), da seguinte forma:

O Azure Site Recovery fornece a recuperação após desastre ao replicar os componentes críticos no seu ambiente para um site remoto sem interesse ou para uma cloud pública, como o Microsoft Azure. Uma vez que as máquinas virtuais com o servidor web e a base de dados são replicadas para o site de recuperação, não há requisito para uma cópia de segurança separada para ficheiros de configuração ou certificados. Os mapeamentos de aplicação e enlaces dependentes em variáveis de ambiente que são alterados após a ativação pós-falha podem ser atualizados através de scripts integrados em planos de recuperação após desastre. As máquinas virtuais são criadas no local de recuperação apenas durante uma falha. A Recuperação do Site Azure também o ajuda a orquestrar a falha de ponta a ponta, fornecendo-lhe as seguintes capacidades:

- Sequenciação do encerramento e arranque das máquinas virtuais nas várias camadas.
- Adicionar scripts para permitir atualizações de dependências de aplicações e ligações nas máquinas virtuais após o seu início. Os scripts também podem ser utilizados para atualizar o servidor DNS que aponta para o site de recuperação.
- Aloque endereços IP para máquinas virtuais antes do pré-failover mapeando as redes primárias e de recuperação e use scripts que não precisam de ser atualizados após a falha.
- Capacidade para uma falha de um clique para várias aplicações web que elimina a margem de confusão durante um desastre.
- Capacidade para testar os planos de recuperação num ambiente isolado para testes de recuperação após desastre.

[Saiba mais](site-recovery-iis.md) sobre a recuperação de desastres para o IIS.

## <a name="protect-citrix-xenapp-and-xendesktop"></a>Proteger o Citrix XenApp e o XenDesktop

Utilize o Site Recovery para proteger as suas implementações do Citrix XenApp e do XenDesktop deployments, da seguinte forma:

- Ativar a proteção da implementação Citrix XenApp e XenDesktop. Replicar as diferentes camadas de implementação para Azure: Ative Directory, Servidor DNS, servidor de base de dados SQL, Controlador de Entrega Citrix, Servidor StoreFront, XenApp Master (VDA), Citrix XenApp License Server.
- Utilize o Site Recovery para migrar as suas implementações do Citrix XenApp e do XenDesktop para o Azure, de modo a simplificar a migração para a cloud.
- Crie uma cópia de tipo de produção e a pedido para testes e depuração, para simplificar os testes de Citrix XenApp/XenDesktop.
- Esta solução aplica-se apenas aos desktops virtuais do Windows Server e não aos ambientes de trabalho virtuais do cliente. Os desktops virtuais dos clientes ainda não são suportados para licenciamento em Azure. [Saiba mais](https://azure.microsoft.com/pricing/licensing-faq/) sobre o licenciamento para ambientes de trabalho de cliente/servidor no Azure.

[Saiba mais](site-recovery-citrix-xenapp-and-xendesktop.md) sobre a recuperação de desastres para as implementações de Citrix XenApp e XenDesktop. Ou, pode referir-se ao [livro branco Citrix.](https://aka.ms/citrix-xenapp-xendesktop-with-asr)

## <a name="next-steps"></a>Passos seguintes

[Saiba mais](azure-to-azure-quickstart.md) sobre a recuperação de desastres para um VM Azure.
