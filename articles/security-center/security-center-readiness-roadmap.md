---
title: Plano de Preparação do Centro de Segurança do Azure | Microsoft Docs
description: Este documento fornece-lhe um plano de preparação para incrementar no Centro de Segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: fece670cc-df70-445d-9773-b32cbaba8d4a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2018
ms.author: yurid
ms.openlocfilehash: 9d74ea2b967112a794cda204cbbfcac707e1d7c4
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879467"
---
# <a name="azure-security-center-readiness-roadmap"></a>Plano de Preparação do Centro de Segurança do Azure
Este documento fornece um plano de preparação que irá ajudá-lo a começar a utilizar o Centro de Segurança do Azure.

## <a name="understanding-security-center"></a>Compreender o Centro de Segurança
O Centro de Segurança do Azure oferece gestão de segurança unificada e proteção avançada contra ameaças para cargas de trabalho em execução no Azure, no local e noutras clouds. 

Utilize os seguintes recursos para começar a utilizar o Centro de Segurança.

Artigos
* [Introdução ao Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
* [Manual de início rápido do Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-get-started)

Vídeos
* [Vídeo de Introdução Rápida](https://azure.microsoft.com/resources/videos/introduction-to-azure-security-center/)
* [Descrição Geral da Prevenção, Deteção e Capacidades de Resposta do Centro de Segurança](https://azure.microsoft.com/resources/videos/azurecon-2015-new-azure-security-center-helps-you-prevent-detect-and-respond-to-threats/)

## <a name="planning-and-operations"></a>Planeamento e operações

Para tirar o máximo partido do Centro de Segurança, é importante compreender de que forma diferentes pessoas ou equipas na sua organização utilizam o serviço para dar resposta às necessidades de operações seguras, monitorização, governação e resposta a incidentes.

Utilize os seguintes recursos para ajudá-lo durante os processos de planeamento e operações.

Artigo
* [Guia de operações e planeamento do Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)


### <a name="onboarding-computers-to-security-center"></a>Inclusão de computadores no Centro de Segurança
O Centro de Segurança deteta automaticamente quaisquer subscrições ou áreas de trabalho do Azure não ativadas para o Centro de Segurança Standard. Isto inclui as subscrições do Azure que utilizam o Centro de Segurança Gratuito e as áreas de trabalho que não têm a solução de Segurança ativada.

Utilize os seguintes recursos para ajudá-lo durante os processos de inclusão.

Artigo
* [Inclusão do Centro de Segurança do Azure Standard para segurança avançada](https://docs.microsoft.com/azure/security-center/security-center-onboarding)

Vídeo
* [Centro de Segurança do Azure Híbrido - Descrição Geral](https://youtu.be/NMa4L_M597k)

## <a name="mitigating-security-issues-using-security-center"></a>Mitigar os problemas de segurança com o Centro de Segurança
O Centro de Segurança recolhe, analisa e integra automaticamente dados de registo a partir dos seus recursos do Azure, da rede e soluções de parceiros ligadas, tal como soluções de proteção de ponto final e firewall, para detetar ameaças reais e reduzir os falsos positivos.

Utilize os seguintes recursos para ajudá-lo a gerir alertas de segurança e proteger os seus recursos.

Artigos    
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-monitoring)
* [Proteger as máquinas e aplicações no Centro de Segurança do Azure](security-center-virtual-machine-protection.md)
* [Proteger a sua rede no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)
* [Proteger o serviço SQL do Azure e os dados no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations)


Vídeo    
* [Mitigar os Problemas de Segurança com o Centro de Segurança do Azure](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Mitigating-Security-Issues-using-Azure-Security-Center)

### <a name="security-center-for-incident-response"></a>Centro de Segurança para resposta a incidentes
Para reduzir custos e danos, é importante ter um plano de resposta a incidentes antes de um ataque ocorrer. Pode utilizar o Centro de segurança do Azure em várias fases de uma resposta a incidentes.

Utilize os seguintes recursos para compreender como o Centro de Segurança pode ser incorporado no seu processo de resposta a incidentes.

Vídeos    
* [Centro de Segurança do Azure na Resposta a Incidentes](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response)
* [Responder rapidamente a ameaças com a operação e investigação de segurança de próxima geração](https://youtu.be/e8iFCz5RM4g)

Artigos    
* [Utilizar o Centro de Segurança do Azure para resposta a incidentes](https://docs.microsoft.com/azure/security-center/security-center-incident-response)
* [Automatizar resposta com automatização de fluxo de trabalho](workflow-automation.md)

## <a name="advanced-cloud-defense"></a>Defesa de cloud avançada

As VMs do Azure podem tirar partido das capacidades avançadas de defesa da cloud no Centro de Segurança. Estas capacidades incluem acesso a máquina virtual (VM) just-in-time e controlos de aplicação adaptáveis.

Utilize os seguintes recursos para aprender a utilizar estas capacidades no Centro de Segurança.

Vídeos    
* [Centro de Segurança Azure – Acesso VM just-in-time](https://youtu.be/UOQb2FcdQnU)
* [Centro de Segurança do Azure - Controlos de Aplicação Adaptáveis](https://youtu.be/wWWekI1Y9ck)

Artigos    
* [Gerir o acesso virtual à máquina usando o tempo justo](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)
* [Controlos de Aplicação Adaptáveis do Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="hands-on-activities"></a>Atividades práticas

* [Laboratório prático do Centro de Segurança](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=78871abf-6f35-4aa0-840f-d801f5cdbd72)
* [Playbook de recomendações da Firewall de Aplicações Web (WAF) no Centro de Segurança](https://gallery.technet.microsoft.com/ASC-Playbook-Protect-38bd47ff)
* [Manual de Procedimentos do Centro de Segurança do Azure: Alertas de Segurança](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)

## <a name="additional-resources"></a>Recursos adicionais
* [Página de Documentação do Centro de Segurança](https://docs.microsoft.com/azure/security-center/)
* [Página de Documentação da API REST do Centro de Segurança](https://msdn.microsoft.com/library/mt704034.aspx)
* [Perguntas mais frequentes (FAQ) do Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-faq)
* [Página de Preços do Centro de Segurança](https://azure.microsoft.com/pricing/details/security-center/)
* [Melhores práticas da segurança de identidade](https://docs.microsoft.com/azure/security/fundamentals/identity-management-best-practices)
* [Melhores práticas de segurança de rede](https://docs.microsoft.com/azure/security/fundamentals/network-best-practices)
* [Recomendações PaaS](https://docs.microsoft.com/azure/security/security-paas-deployments)
* [Conformidade](https://www.microsoft.com/trustcenter/compliance/due-diligence-checklist)
* [Os clientes de análise de log já podem usar o Azure Security Center para proteger as suas cargas de trabalho híbridas em nuvem](https://blogs.technet.microsoft.com/msoms/2017/09/25/oms-customers-can-now-use-azure-security-center-to-protect-their-hybrid-cloud-workloads/)

## <a name="community-resources"></a>Recursos da Comunidade

* [UserVoice do Centro de Segurança](https://feedback.azure.com/forums/347535-azure-security-center)
* [Fórum da Comunidade do Centro de Segurança](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSecurityCenter)



