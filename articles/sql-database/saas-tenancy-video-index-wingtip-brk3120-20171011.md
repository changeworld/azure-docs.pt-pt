---
title: Vídeo de aplicativo SaaS SQL
description: Este artigo indexa vários pontos de tempo no nosso vídeo de 81 minutos sobre o design de aplicações de arrendamento SaaS DB, da conferência Ignite realizada a 11 de outubro de 2017. Podes saltar para a parte que te interessa. Pelo menos 3 padrões são descritos. Descrevem-se as características do Azure que simplificam o desenvolvimento e a gestão.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, sstein
ms.date: 12/18/2018
ms.topic: conceptual
ms.openlocfilehash: 1ee8f2fff958045f652b72358ab928f82920fd6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067622"
---
# <a name="video-indexed-and-annotated-for-multi-tenant-saas-app-using-azure-sql-database"></a>Vídeo indexado e anotado para aplicação SaaS multi-inquilino usando base de dados Azure SQL

Este artigo é um índice anotado nos locais de tempo de um vídeo de 81 minutos sobre modelos ou padrões de arrendamento SaaS. Este artigo permite-lhe saltar para trás ou para a frente no vídeo a que parte lhe interessa. O vídeo explica as principais opções de design para uma aplicação de base de dados multi-inquilinos na Base de Dados Azure SQL. O vídeo inclui demonstrações, walkthroughs do código de gestão, e às vezes mais detalhes informados pela experiência do que poderia estar na nossa documentação escrita.

O vídeo amplifica a informação na nossa documentação escrita, encontrada em: 
- *Conceptual:* Padrões de arrendamento de [base de dados Multi-inquilinos SaaS][saas-concept-design-patterns-563e]
- *Tutoriais:* [A aplicação Wingtip Tickets SaaS][saas-how-welcome-wingtip-app-679t]

O vídeo e os artigos descrevem as muitas fases da criação de uma aplicação multi-arrendatária na Base de Dados Azure SQL na nuvem. As funcionalidades especiais da Base de Dados Azure SQL facilitam o desenvolvimento e implementação de aplicações multi-arrendatárias que são simultaneamente mais fáceis de gerir e de forma fiável.

Atualizamos rotineiramente a nossa documentação escrita. O vídeo não é editado ou atualizado, pelo que eventualmente mais detalhes podem ficar desatualizados.



## <a name="sequence-of-38-time-indexed-screenshots"></a>Sequência de 38 imagens com índice de tempo

Esta secção indexa a localização do tempo para 38 discussões ao longo do vídeo de 81 minutos. Cada vez que o índice é anotado com uma imagem do vídeo, e às vezes com informações adicionais.

Cada vez que o índice está no formato *h:mm:ss*. Por exemplo, o segundo local de hora indexado, marcado **sessão,** começa no local de tempo aproximado de **0:03:11**.


### <a name="compact-links-to-video-indexed-time-locations"></a>Links compactos para locais de tempo indexados a vídeo

Os seguintes títulos são ligações às secções anotadas correspondentes mais tarde neste artigo:

- [1. **(Iniciar)** Slide de boas-vindas, 0:00:03](#anchor-image-wtip-min00001)
- [2. Objetivos da sessão, 0:03:11](#anchor-image-wtip-min00311)
- [3. Agenda, 0:04:17](#anchor-image-wtip-min00417)
- [4. Aplicação web multi-inquilino, 0:05:05](#anchor-image-wtip-min00505)
- [5. Formulário web da aplicação em ação, 0:05:55](#anchor-image-wtip-min00555)
- [6. Custo por inquilino (escala, isolamento, recuperação), 0:09:31](#anchor-image-wtip-min00931)
- [7. Modelos de base de dados para multi-inquilinos: prós e contras, 0:11:59](#anchor-image-wtip-min01159)
- [8. Modelo híbrido mistura benefícios de MT/ST, 0:13:01](#anchor-image-wtip-min01301)
- [9. Inquilino único vs multi-inquilino: prós e contras, 0:16:44](#anchor-image-wtip-min01644)
- [10. As piscinas são rentáveis para cargas de trabalho imprevisíveis, 0:19:36](#anchor-image-wtip-min01936)
- [11. Demonstração de base de dados por inquilino e híbrido ST/MT, 0:20:08](#anchor-image-wtip-min02008)
- [12. Formulário de aplicativo ao vivo mostrando Dojo, 0:20:29](#anchor-image-wtip-min02029)
- [13. MYOB e não um DBA à vista, 0:28:54](#anchor-image-wtip-min02854)
- [14. Exemplo de utilização da piscina elástica MYOB, 0:29:40](#anchor-image-wtip-min02940)
- [15. Aprendizagem do MYOB e de outros ISVs, 0:31:36](#anchor-image-wtip-min03136)
- [16. Padrões compõem-se no cenário E2E SaaS, 0:43:15](#anchor-image-wtip-min04315)
- [17. Aplicação SaaS híbrida canónica, 0:47:33](#anchor-image-wtip-min04733)
- [18. App de amostras Wingtip SaaS, 0:48:10](#anchor-image-wtip-min04810)
- [19. Cenários e padrões explorados nos tutoriais, 0:49:10](#anchor-image-wtip-min04910)
- [20. Demonstração de tutoriais e repositório GitHub, 0:50:18](#anchor-image-wtip-min05018)
- [21. GitHub repo Microsoft/WingtipSaaS, 0:50:38](#anchor-image-wtip-min05038)
- [22. Explorando os padrões, 0:56:20](#anchor-image-wtip-min05620)
- [23. Provisioning tenants and onboarding, 0:57:44](#anchor-image-wtip-min05744)
- [24. Provisionamento de inquilinos e ligação de candidatura, 0:58:58](#anchor-image-wtip-min05858)
- [25. Demonstração de scripts de gestão que aprovisionam um único inquilino, 0:59:43](#anchor-image-wtip-min05943)
- [26. PowerShell para fornecer e catalogar, 1:00:02](#anchor-image-wtip-min10002)
- [27. T-SQL SELECT * FROM TenantsExtended, 1:03:30](#anchor-image-wtip-min10330)
- [28. Gestão de cargas horárias imprevisíveis de inquilinos, 1:04:36](#anchor-image-wtip-min10436)
- [29. Monitorização elástica da piscina, 1:06:39](#anchor-image-wtip-min10639)
- [30. Geração de carga e monitorização de desempenho, 1:09:42](#anchor-image-wtip-min10942)
- [31. Gestão de Schema em escala, 1:10:33](#anchor-image-wtip-min11033)
- [32. Consulta distribuída através das bases de dados dos inquilinos, 1:12:21](#anchor-image-wtip-min11221)
- [33. Demonstração da geração de bilhetes, 1:12:32](#anchor-image-wtip-min11232)
- [34. Análise adhoc SSMS, 1:12:46](#anchor-image-wtip-min11246)
- [35. Extrair dados de inquilinos em SQL DW, 1:16:32](#anchor-image-wtip-min11632)
- [36. Gráfico de distribuição diária de venda, 1:16:48](#anchor-image-wtip-min11648)
- [37. Embrulhe e ligue para a ação, 1:19:52](#anchor-image-wtip-min11952)
- [38. Recursos para mais informações, 1:20:42](#anchor-image-wtip-min12042)


&nbsp;

### <a name="annotated-index-time-locations-in-the-video"></a>Localizações anotadas do índice no vídeo

Clicar em qualquer imagem de imagem leva-o ao local exato do vídeo.


&nbsp; <a name="anchor-image-wtip-min00001"/>
#### <a name="1-start-welcome-slide-00001"></a>1. *(Iniciar)* Slide de boas-vindas, 0:00:01

*Aprender com MYOB: Padrões de design para aplicações SaaS na Base de Dados Azure SQL - BRK3120*

[![Slide de boas-vindas][image-wtip-min00003-brk3120-whole-welcome]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1)

- Título: Aprendizagem do MYOB: Padrões de design para aplicações SaaS na Base de Dados Azure SQL
- Bill.Gibson@microsoft.com
- Gestor principal de programas, Base de Dados Azure SQL
- Microsoft Ignite sessão BRK3120, Orlando, FL USA, outubro/11 2017


&nbsp; <a name="anchor-image-wtip-min00311"/>
#### <a name="2-session-objectives-00153"></a>2. Objetivos da sessão, 0:01:53
[![Objetivos da sessão][image-wtip-min00311-session]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=113)

- Modelos alternativos para aplicações multi-inquilinos, com prós e contras.
- Padrões SaaS para reduzir os custos de desenvolvimento, gestão e recursos.
- Uma aplicação de amostra + scripts.
- As funcionalidades paaS + padrões SaaS fazem da Base de Dados SQL uma plataforma de dados altamente escalável e rentável para o Multi-inquilino SaaS.


&nbsp; <a name="anchor-image-wtip-min00417"/>
#### <a name="3-agenda-00409"></a>3. Agenda, 0:04:09
[![Agenda][image-wtip-min00417-agenda]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=249)


&nbsp; <a name="anchor-image-wtip-min00505"/>
#### <a name="4-multi-tenant-web-app-00500"></a>4. Aplicação web multi-inquilino, 0:05:00
[![App Wingtip SaaS: Aplicação web multi-inquilino][image-wtip-min00505-web-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=300)


&nbsp; <a name="anchor-image-wtip-min00555"/>
#### <a name="5-app-web-form-in-action-00539"></a>5. Formulário web da aplicação em ação, 0:05:39
[![Formulário web da aplicação em ação][image-wtip-min00555-app-web-form]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=339)


&nbsp; <a name="anchor-image-wtip-min00931"/>
#### <a name="6-per-tenant-cost-scale-isolation-recovery-00658"></a>6. Custo por inquilino (escala, isolamento, recuperação), 0:06:58
[![Custo por inquilino, escala, isolamento, recuperação][image-wtip-min00931-per-tenant-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=418)


&nbsp; <a name="anchor-image-wtip-min01159"/>
#### <a name="7-database-models-for-multi-tenant-pros-and-cons-00952"></a>7. Modelos de base de dados para multi-inquilinos: prós e contras, 0:09:52
[![Modelos de base de dados para multi-inquilinos: prós e contras][image-wtip-min01159-db-models-pros-cons]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=592)


&nbsp; <a name="anchor-image-wtip-min01301"/>
#### <a name="8-hybrid-model-blends-benefits-of-mtst-01229"></a>8. Modelo híbrido mistura benefícios de MT/ST, 0:12:29
[![Modelo híbrido mistura benefícios de MT/ST][image-wtip-min01301-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=749)


&nbsp; <a name="anchor-image-wtip-min01644"/>
#### <a name="9-single-tenant-vs-multi-tenant-pros-and-cons-01311"></a>9. Inquilino único vs multi-inquilino: prós e contras, 0:13:11
[![Inquilino único vs multi-inquilino: prós e contras][image-wtip-min01644-st-vs-mt]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=791)


&nbsp; <a name="anchor-image-wtip-min01936"/>
#### <a name="10-pools-are-cost-effective-for-unpredictable-workloads-01749"></a>10. As piscinas são rentáveis para cargas de trabalho imprevisíveis, 0:17:49
[![As piscinas são rentáveis para cargas de trabalho imprevisíveis][image-wtip-min01936-pools-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1069)


&nbsp; <a name="anchor-image-wtip-min02008"/>
#### <a name="11-demo-of-database-per-tenant-and-hybrid-stmt-01959"></a>11. Demonstração de base de dados por inquilino e híbrido ST/MT, 0:19:59
[![Demonstração de base de dados por inquilino e st/mt híbrido][image-wtip-min02008-demo-st-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1199)


&nbsp; <a name="anchor-image-wtip-min02029"/>
#### <a name="12-live-app-form-showing-dojo-02010"></a>12. Formulário de aplicativo ao vivo mostrando Dojo, 0:20:10
[![Formulário de aplicativo ao vivo mostrando Dojo][image-wtip-min02029-live-app-form-dojo]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1210)

&nbsp; <a name="anchor-image-wtip-min02854"/>
#### <a name="13-myob-and-not-a-dba-in-sight-02506"></a>13. MYOB e não um DBA à vista, 0:25:06
[![MYOB e não um DBA à vista][image-wtip-min02854-myob-no-dba]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1506)


&nbsp; <a name="anchor-image-wtip-min02940"/>
#### <a name="14-myob-elastic-pool-usage-example-02930"></a>14. Exemplo de utilização da piscina elástica MYOB, 0:29:30
[![Exemplo de utilização da piscina elástica MYOB][image-wtip-min02940-myob-elastic]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1770)


&nbsp; <a name="anchor-image-wtip-min03136"/>
#### <a name="15-learning-from-myob-and-other-isvs-03125"></a>15. Aprendizagem do MYOB e de outros ISVs, 0:31:25
[![Aprender com MYOB e outros ISVs][image-wtip-min03136-learning-isvs]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1885)


&nbsp; <a name="anchor-image-wtip-min04315"/>
#### <a name="16-patterns-compose-into-e2e-saas-scenario-03142"></a>16. Padrões compõem-se no cenário E2E SaaS, 0:31:42
[![Padrões compõem-se no cenário E2E SaaS][image-wtip-min04315-patterns-compose]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1902)


&nbsp; <a name="anchor-image-wtip-min04733"/>
#### <a name="17-canonical-hybrid-multi-tenant-saas-app-04604"></a>17. Aplicação SaaS híbrida canónica, 0:46:04
[![App SaaS híbrida canónica multi-inquilino][image-wtip-min04733-canonical-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2764)


&nbsp; <a name="anchor-image-wtip-min04810"/>
#### <a name="18-wingtip-saas-sample-app-04801"></a>18. App de amostras Wingtip SaaS, 0:48:01
[![App de amostras Wingtip SaaS][image-wtip-min04810-wingtip-saas-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2881)


&nbsp; <a name="anchor-image-wtip-min04910"/>
#### <a name="19-scenarios-and-patterns-explored-in-the-tutorials-04900"></a>19. Cenários e padrões explorados nos tutoriais, 0:49:00
[![Cenários e padrões explorados nos tutoriais][image-wtip-min04910-scenarios-tutorials]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2940)


&nbsp; <a name="anchor-image-wtip-min05018"/>
#### <a name="20-demo-of-tutorials-and-github-repository-05012"></a>20. Demonstração de tutoriais e repositório GitHub, 0:50:12
[![Tutoriais de demonstração e repo GitHub][image-wtip-min05018-demo-tutorials-github]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3012)


&nbsp; <a name="anchor-image-wtip-min05038"/>
#### <a name="21-github-repo-microsoftwingtipsaas-05032"></a>21. GitHub repo Microsoft/WingtipSaaS, 0:50:32
[![GitHub repo Microsoft/WingtipSaaS][image-wtip-min05038-github-wingtipsaas]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3032)


&nbsp; <a name="anchor-image-wtip-min05620"/>
#### <a name="22-exploring-the-patterns-05615"></a>22. Explorando os padrões, 0:56:15
[![Explorando os padrões][image-wtip-min05620-exploring-patterns]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3375)


&nbsp; <a name="anchor-image-wtip-min05744"/>
#### <a name="23-provisioning-tenants-and-onboarding-05619"></a>23. Provisioning tenants and onboarding, 0:56:19
[![Provisionamento de inquilinos e embarque][image-wtip-min05744-provisioning-tenants-onboarding-1]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3379)


&nbsp; <a name="anchor-image-wtip-min05858"/>
#### <a name="24-provisioning-tenants-and-application-connection-05752"></a>24. Provisionamento de inquilinos e ligação de candidatura, 0:57:52
[![Provisionamento de inquilinos e ligação de candidatura][image-wtip-min05858-provisioning-tenants-app-connection-2]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3472)


&nbsp; <a name="anchor-image-wtip-min05943"/>
#### <a name="25-demo-of-management-scripts-provisioning-a-single-tenant-05936"></a>25. Demonstração de scripts de gestão que aprovisionam um único inquilino, 0:59:36
[![Demonstração de scripts de gestão que aprovisionam um único inquilino][image-wtip-min05943-demo-management-scripts-st]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3576)


&nbsp; <a name="anchor-image-wtip-min10002"/>
#### <a name="26-powershell-to-provision-and-catalog-05956"></a>26. PowerShell para fornecer e catalogar, 0:59:56
[![PowerShell para fornecer e catalogar][image-wtip-min10002-powershell-provision-catalog]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3596)


&nbsp; <a name="anchor-image-wtip-min10330"/>
#### <a name="27-t-sql-select--from-tenantsextended-10325"></a>27. T-SQL SELECT * FROM TenantsExtended, 1:03:25
[![T-SQL SELECT * FROM TenantsExtended][image-wtip-min10330-sql-select-tenantsextended]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3805)


&nbsp; <a name="anchor-image-wtip-min10436"/>
#### <a name="28-managing-unpredictable-tenant-workloads-10334"></a>28. Gestão de cargas horárias imprevisíveis de inquilinos, 1:03:34
[![Gestão de cargas de trabalho imprevisíveis dos inquilinos][image-wtip-min10436-managing-unpredictable-workloads]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3814)


&nbsp; <a name="anchor-image-wtip-min10639"/>
#### <a name="29-elastic-pool-monitoring-10632"></a>29. Monitorização elástica da piscina, 1:06:32
[![Monitorização elástica da piscina][image-wtip-min10639-elastic-pool-monitoring]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3992)


&nbsp; <a name="anchor-image-wtip-min10942"/>
#### <a name="30-load-generation-and-performance-monitoring-10937"></a>30. Geração de carga e monitorização de desempenho, 1:09:37
[![Geração de carga e monitorização do desempenho][image-wtip-min10942-load-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4117)


&nbsp; <a name="anchor-image-wtip-min11033"/>
#### <a name="31-schema-management-at-scale-10940"></a>31. Gestão de Schema em escala, 1:09:40
[![Gestão de schema em escala][image-wtip-min11033-schema-management-scale]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=34120)


&nbsp; <a name="anchor-image-wtip-min11221"/>
#### <a name="32-distributed-query-across-tenant-databases-11118"></a>32. Consulta distribuída através das bases de dados dos inquilinos, 1:11:18
[![Consulta distribuída através das bases de dados dos inquilinos][image-wtip-min11221-distributed-query]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4278)


&nbsp; <a name="anchor-image-wtip-min11232"/>
#### <a name="33-demo-of-ticket-generation-11228"></a>33. Demonstração da geração de bilhetes, 1:12:28
[![Demonstração da geração de bilhetes][image-wtip-min11232-demo-ticket-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4348)


&nbsp; <a name="anchor-image-wtip-min11246"/>
#### <a name="34-ssms-adhoc-analytics-11235"></a>34. Análise adhoc SSMS, 1:12:35
[![Análise adhoc SSMS][image-wtip-min11246-ssms-adhoc-analytics]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4355)


&nbsp; <a name="anchor-image-wtip-min11632"/>
#### <a name="35-extract-tenant-data-into-sql-dw-11546"></a>35. Extrair dados de inquilinos em SQL DW, 1:15:46
[![Extrair dados de inquilinos em SQL DW][image-wtip-min11632-extract-tenant-data-sql-dw]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4546)


&nbsp; <a name="anchor-image-wtip-min11648"/>
#### <a name="36-graph-of-daily-sale-distribution-11638"></a>36. Gráfico de distribuição diária de venda, 1:16:38
[![Gráfico da distribuição diária de venda][image-wtip-min11648-graph-daily-sale-distribution]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4598)


&nbsp; <a name="anchor-image-wtip-min11952"/>
#### <a name="37-wrap-up-and-call-to-action-11743"></a>37. Embrulhe e ligue para a ação, 1:17:43
[![Embrulhe e chame à ação][image-wtip-min11952-wrap-up-call-action]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4663)


&nbsp; <a name="anchor-image-wtip-min12042"/>
#### <a name="38-resources-for-more-information-12035"></a>38. Recursos para mais informações, 1:20:35
[![Recursos para mais informação][image-wtip-min12042-resources-more-info]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4835)

- [Post blog, 22 de maio de 2017][resource-blog-saas-patterns-app-dev-sql-db-768h]

- *Conceptual:* Padrões de arrendamento de [base de dados Multi-inquilinos SaaS][saas-concept-design-patterns-563e]

- *Tutoriais:* [A aplicação Wingtip Tickets SaaS][saas-how-welcome-wingtip-app-679t]

- Repositórios GitHub para sabores da aplicação de arrendamento Wingtip Tickets SaaS:
    - [GitHub repo para - Modelo de aplicação autónoma][github-wingtip-standaloneapp].
    - [GitHub repo para - DB Per Tenant modelo][github-wingtip-dbpertenant].
    - [GitHub repo para - Modelo DB multi-inquilino][github-wingtip-multitenantdb].





## <a name="next-steps"></a>Passos seguintes

- [Primeiro artigo tutorial][saas-how-welcome-wingtip-app-679t]




<!-- Image link reference IDs. -->

[image-wtip-min00003-brk3120-whole-welcome]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00003-brk3120-welcome-myob-design-saas-app-sql-db.png "Slide de boas-vindas"

[image-wtip-min00311-session]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00311-session-objectives-takeaway.png "Objetivos da sessão."

[image-wtip-min00417-agenda]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00417-agenda-app-management-models-patterns.png "Agenda."

[image-wtip-min00505-web-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00505-wingtip-saas-app-mt-web.png "App Wingtip SaaS: Aplicação web multi-inquilino"

[image-wtip-min00555-app-web-form]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00555-app-form-contoso-concert-hall-night-opera.png "Formulário web da aplicação em ação"

[image-wtip-min00931-per-tenant-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00931-saas-data-management-concerns.png "Custo por inquilino, escala, isolamento, recuperação"

[image-wtip-min01159-db-models-pros-cons]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01159-db-models-multi-tenant-saas-apps.png "Modelos de base de dados para multi-inquilinos: prós e contras"

[image-wtip-min01301-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01301-hybrib-model-blends-benefits-mt-st.png "Modelo híbrido mistura benefícios de MT/ST"

[image-wtip-min01644-st-vs-mt]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01644-st-mt-pros-cons.png "Inquilino único vs multi-inquilino: prós e contras"

[image-wtip-min01936-pools-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01936-pools-cost-effective-unpredictable-workloads.png "As piscinas são rentáveis para cargas de trabalho imprevisíveis"

[image-wtip-min02008-demo-st-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02008-demo-st-hybrid.png "Demonstração de base de dados por inquilino e st/mt híbrido"

[image-wtip-min02029-live-app-form-dojo]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02029-app-form-dogwwod-dojo.png "Formulário de aplicativo ao vivo mostrando Dojo"

[image-wtip-min02854-myob-no-dba]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02854-myob-no-dba.png "MYOB e não um DBA à vista"

[image-wtip-min02940-myob-elastic]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02940-myob-elastic-pool-usage-example.png "Exemplo de utilização da piscina elástica MYOB"

[image-wtip-min03136-learning-isvs]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min03136-myob-isv-saas-patterns-design-scale.png "Aprender com MYOB e outros ISVs"

[image-wtip-min04315-patterns-compose]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04315-patterns-compose-into-e2e-saas-scenario-st-mt.png "Padrões compõem-se no cenário E2E SaaS"

[image-wtip-min04733-canonical-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04733-canonical-hybrid-mt-saas-app.png "App SaaS híbrida canónica multi-inquilino"

[image-wtip-min04810-wingtip-saas-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04810-saas-sample-app-descr-of-modules-links.png "App de amostras Wingtip SaaS"

[image-wtip-min04910-scenarios-tutorials]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04910-scenarios-patterns-explored-tutorials.png "Cenários e padrões explorados nos tutoriais"

[image-wtip-min05018-demo-tutorials-github]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05018-demo-saas-tutorials-github-repo.png "Demonstração de tutoriais e repo GitHub"

[image-wtip-min05038-github-wingtipsaas]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05038-github-repo-wingtipsaas.png "GitHub repo Microsoft/WingtipSaaS"

[image-wtip-min05620-exploring-patterns]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05620-exploring-patterns-tutorials.png "Explorando os padrões"

[image-wtip-min05744-provisioning-tenants-onboarding-1]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05744-provisioning-tenants-connecting-run-time-1.png "Provisionamento de inquilinos e embarque"

[image-wtip-min05858-provisioning-tenants-app-connection-2]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05858-provisioning-tenants-connecting-run-time-2.png "Provisionamento de inquilinos e ligação de candidatura"

[image-wtip-min05943-demo-management-scripts-st]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05943-demo-management-scripts-provisioning-st.png "Demonstração de scripts de gestão que aprovisionam um único inquilino"

[image-wtip-min10002-powershell-provision-catalog]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10002-powershell-code.png "PowerShell para fornecer e catalogar"

[image-wtip-min10330-sql-select-tenantsextended]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10330-ssms-tenantcatalog.png "T-SQL SELECT * FROM TenantsExtended"

[image-wtip-min10436-managing-unpredictable-workloads]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10436-managing-unpredictable-tenant-workloads.png "Gestão de cargas de trabalho imprevisíveis dos inquilinos"

[image-wtip-min10639-elastic-pool-monitoring]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10639-elastic-pool-monitoring.png "Monitorização elástica da piscina"

[image-wtip-min10942-load-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10942-schema-management-scale.png "Geração de carga e monitorização do desempenho"

[image-wtip-min11033-schema-management-scale]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11033-schema-manage-1000s-dbs-one.png "Gestão de schema em escala"

[image-wtip-min11221-distributed-query]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11221-distributed-query-all-tenants-asif-single-db.png "Consulta distribuída através das bases de dados dos inquilinos"

[image-wtip-min11232-demo-ticket-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11232-demo-ticket-generation-distributed-query.png "Demonstração da geração de bilhetes"

[image-wtip-min11246-ssms-adhoc-analytics]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11246-tsql-adhoc-analystics-db-elastic-query.png "Análise adhoc SSMS"

[image-wtip-min11632-extract-tenant-data-sql-dw]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11632-extract-tenant-data-analytics-db-dw.png "Extrair dados de inquilinos em SQL DW"

[image-wtip-min11648-graph-daily-sale-distribution]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11648-graph-daily-sale-contoso-concert-hall.png "Gráfico da distribuição diária de venda"

[image-wtip-min11952-wrap-up-call-action]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11952-wrap-call-action-saasfeedback.png "Embrulhe e chame à ação"

[image-wtip-min12042-resources-more-info]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min12042-resources-blog-github-tutorials-get-started.png "Recursos para mais informação"




<!-- Article link reference IDs. -->

[saas-concept-design-patterns-563e]: saas-tenancy-app-design-patterns.md

[saas-how-welcome-wingtip-app-679t]: saas-tenancy-welcome-wingtip-tickets-app.md


[video-on-youtube-com-478y]: https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1

[video-on-channel9-479c]: https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK3120


[resource-blog-saas-patterns-app-dev-sql-db-768h]: https://azure.microsoft.com/blog/saas-patterns-accelerate-saas-application-development-on-sql-database/


[github-wingtip-standaloneapp]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp/

[github-wingtip-dbpertenant]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/

[github-wingtip-multitenantdb]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/

