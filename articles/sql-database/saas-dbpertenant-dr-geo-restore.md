---
title: 'Aplicativos SaaS: Backups georedundantes para recuperação de desastres'
description: Aprenda a usar backups georedundantes da Base de Dados Azure SQL para recuperar uma aplicação SaaS multiarrendatária em caso de paragem
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: AyoOlubeko
ms.author: craigg
ms.reviewer: sstein
ms.date: 01/14/2019
ms.openlocfilehash: 270fc157fa14efa19ed30d35b614fb769804b72e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73826464"
---
# <a name="use-geo-restore-to-recover-a-multitenant-saas-application-from-database-backups"></a>Use geo-restauro para recuperar uma aplicação SaaS multiarrendatária de backups de bases de dados

Este tutorial explora um cenário completo de recuperação de desastres para uma aplicação SaaS multiarrendatária implementada com a base de dados por modelo de inquilino. Você usa [geo-restauro](sql-database-recovery-using-backups.md) para recuperar o catálogo e as bases de dados de inquilinos de backups geo-redundantes automaticamente mantidos em uma região de recuperação alternativa. Após a paralisação ser resolvida, você usa [geo-replicação](sql-database-geo-replication-overview.md) para repatriar bases de dados alteradas para a sua região original.

![Geo-restauração-arquitetura](media/saas-dbpertenant-dr-geo-restore/geo-restore-architecture.png)

A Geo-restore é a solução de recuperação de desastres mais baixa para a Base de Dados Azure SQL. No entanto, restaurar de backups geo-redundantes pode resultar numa perda de dados de até uma hora. Pode levar muito tempo, dependendo do tamanho de cada base de dados. 

> [!NOTE]
> Recupere as aplicações com o RPO e RTO mais baixos possíveis utilizando a geo-replicação em vez de geo-restauro.

Este tutorial explora tanto fluxos de trabalho de restauro como de repatriamento. Saiba como:
> [!div class="checklist"]
> 
> * Sync base de dados e informações de configuração de piscina elástica no catálogo de inquilinos.
> * Instale um ambiente de imagem espelhada numa região de recuperação que inclua aplicações, servidores e piscinas.   
> * Recupere as bases de dados de catálogos e inquilinos utilizando geo-restauro.
> * Use a geo-replicação para repatriar o catálogo de inquilinos e altere as bases de dados dos inquilinos após a paralisação ser resolvida.
> * Atualize o catálogo à medida que cada base de dados for restaurada (ou repatriada) para acompanhar a localização atual da cópia ativa da base de dados de cada inquilino.
> * Certifique-se de que a base de dados de aplicação e inquilino saem sempre co-localizadas na mesma região de Azure para reduzir a latência. 
 

Antes de iniciar este tutorial, preencha os seguintes pré-requisitos:
* Implemente a base de dados Wingtip Tickets SaaS por app de inquilinos. Para implantar em menos de cinco minutos, consulte Implementar e explorar a base de [dados Wingtip Tickets SaaS por aplicação de inquilino](saas-dbpertenant-get-started-deploy.md). 
* Instale o Azure PowerShell. Para mais detalhes, consulte [Getting started with Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-geo-restore-recovery-pattern"></a>Introdução ao padrão de recuperação geo-restauração

A recuperação de desastres (DR) é uma consideração importante para muitas aplicações, seja por razões de conformidade ou continuidade do negócio. Se houver uma interrupção prolongada do serviço, um plano de DR bem preparado pode minimizar a perturbação do negócio. Um plano DR baseado em geo-restauro deve atingir vários objetivos:
 * Reserve toda a capacidade necessária na região de recuperação escolhida o mais rapidamente possível para garantir que está disponível para restaurar as bases de dados dos inquilinos.
 * Estabeleça um ambiente de recuperação de imagem espelhada que reflita a configuração original da piscina e da base de dados. 
 * Permita o cancelamento do processo de restauro a meio do voo se a região original voltar a funcionar.
 * Permitir o fornecimento de inquilinos rapidamente para que o novo inquilino embarque possa reiniciar o mais rápido possível.
 * Seja otimizado para restaurar os inquilinos em ordem prioritária.
 * Seja otimizado para colocar os inquilinos on-line o mais rápido possível, fazendo passos em paralelo onde prático.
 * Seja resiliente ao fracasso, reinufrável e idempotente.
 * Repatriar bases de dados para a sua região original com o mínimo impacto para os inquilinos quando a paralisação for resolvida.  

> [!NOTE]
> O pedido é recuperado na região emparelhada da região em que o pedido é implementado. Para mais informações, consulte [regiões emparelhadas de Azure.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)   

Este tutorial utiliza funcionalidades da Base de Dados Azure SQL e da plataforma Azure para responder a estes desafios:

* Modelos de Gestor de [Recursos Azure,](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)para reservar toda a capacidade necessária o mais rápido possível. Os modelos do Gestor de Recursos Azure são usados para fornecer uma imagem espelhada dos servidores originais e piscinas elásticas na região de recuperação. Um servidor separado e piscina também são criados para fornecer novos inquilinos.
* [Biblioteca](sql-database-elastic-database-client-library.md) de Clientes de Base de Dados Elástica (EDCL), para criar e manter um catálogo de bases de dados de inquilinos. O catálogo alargado inclui informações de configuração de piscina e base de dados periodicamente renovadas.
* [Funcionalidades](sql-database-elastic-database-recovery-manager.md) de recuperação de gestão de fragmentos do EDCL, para manter as entradas de localização da base de dados no catálogo durante a recuperação e repatriamento.  
* [Geo-restauro,](sql-database-disaster-recovery.md)para recuperar as bases de dados do catálogo e dos inquilinos de cópias de segurança georedundantes automaticamente mantidas. 
* [As operações de restauro assíncronos,](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations)enviadas por ordem prioritária para o arrendatário, são colocadas em fila por cada piscina pelo sistema e processadas em lotes para que a piscina não esteja sobrecarregada. Estas operações podem ser canceladas antes ou durante a execução, se necessário.   
* [Geo-replicação,](sql-database-geo-replication-overview.md)para repatriar bases de dados para a região original após a paralisação. Não há perda de dados e impacto mínimo no inquilino quando se utiliza a geo-replicação.
* [Pseudónimos DNS do servidor SQL,](dns-alias-overview.md)para permitir que o processo de sincronização do catálogo se conectem ao catálogo ativo, independentemente da sua localização.  

## <a name="get-the-disaster-recovery-scripts"></a>Obtenha os scripts de recuperação de desastres

Os scripts DR utilizados neste tutorial estão disponíveis na base de [dados Wingtip Tickets SaaS por repositório GitHub.](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) Confira as [orientações gerais](saas-tenancy-wingtip-app-guidance-tips.md) para os passos para descarregar e desbloquear os scripts de gestão de Bilhetes Wingtip.

> [!IMPORTANT]
> Como todos os scripts de gestão de Bilhetes Wingtip, os scripts DR são de qualidade de amostra e não são utilizados na produção.

## <a name="review-the-healthy-state-of-the-application"></a>Rever o estado saudável da aplicação
Antes de iniciar o processo de recuperação, reveja o estado normal e saudável da aplicação.

1. No seu navegador web, abra ohttp://events.wingtip-dpt.&ltcentro&gt;de eventos &lt;&gt; Wingtip Tickets ( .user .trafficmanager.net, substitua o utilizador pelo valor de utilizador da sua implementação).
    
   Percorra a parte inferior da página e note o nome e localização do servidor do catálogo no rodapé. A localização é a região em que implementou a aplicação.    

   > [!TIP]
   > Passe o rato sobre o local para ampliar o visor.

   ![Eventos hub estado saudável na região original](media/saas-dbpertenant-dr-geo-restore/events-hub-original-region.png)

2. Selecione o inquilino da Sala de Concertos Contoso e abra a sua página de eventos.

   No rodapé, repare no nome do servidor do inquilino. A localização é a mesma que a localização do servidor do catálogo.

   ![Região original da Sala de Concertos Contoso](media/saas-dbpertenant-dr-geo-restore/contoso-original-location.png) 

3. No [portal Azure, reveja](https://portal.azure.com)e abra o grupo de recursos no qual implementou a app.

   Note os recursos e a região em que os componentes do serviço de aplicações e servidores de base de dados SQL são implantados.

## <a name="sync-the-tenant-configuration-into-the-catalog"></a>Sincronizar a configuração do inquilino no catálogo

Nesta tarefa, inicia-se um processo de sincronização da configuração dos servidores, piscinas elásticas e bases de dados no catálogo de inquilinos. Esta informação é usada mais tarde para configurar um ambiente de imagem espelhada na região de recuperação.

> [!IMPORTANT]
> Para a simplicidade, o processo de sincronização e outros processos de recuperação e repatriamento a longo prazo são implementados nestas amostras como empregos ou sessões locais da PowerShell que funcionam sob o login do utilizador do seu cliente. Os tokens de autenticação emitidos quando iniciar sessão expiram após várias horas, e os trabalhos falharão. Num cenário de produção, os processos de longo prazo devem ser implementados como serviços azure fiáveis de algum tipo, funcionando sob um diretor de serviço. Consulte [a Utilização do PowerShell Azure para criar um diretor](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)de serviço com um certificado . 

1. No PowerShell ISE, abra o ficheiro ...\Learning Modules\UserConfig.psm1. `<resourcegroup>` Substitua `<user>` e nas linhas 10 e 11 com o valor utilizado quando implementou a aplicação. Guarde o ficheiro.

2. No PowerShell ISE, abra o script ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1.

    Neste tutorial, executa cada um dos cenários deste script PowerShell, por isso mantenha este ficheiro aberto.

3. Definir o seguinte:

    $DemoScenario = 1: Inicie um trabalho de fundo que sincronize o servidor do inquilino e a informação de configuração do pool no catálogo.

4. Para executar o script de sincronização, selecione F5. 

    Esta informação é usada mais tarde para garantir que a recuperação cria uma imagem espelhada dos servidores, piscinas e bases de dados na região de recuperação.  

    ![Processo de sincronização](media/saas-dbpertenant-dr-geo-restore/sync-process.png)

Deixe a janela PowerShell em segundo plano e continue com o resto deste tutorial.

> [!NOTE]
> O processo de sincronização liga-se ao catálogo através de um pseudónimo DNS. O pseudónimo é modificado durante a restauração e repatriamento para apontar para o catálogo ativo. O processo de sincronização mantém o catálogo atualizado com qualquer base de dados ou alterações de configuração do pool feitas na região de recuperação. Durante o repatriamento, estas alterações são aplicadas aos recursos equivalentes na região original.

## <a name="geo-restore-recovery-process-overview"></a>Visão geral do processo de recuperação de georestauros

O processo de recuperação geo-restauração implanta a aplicação e restaura bases de dados de backups para a região de recuperação.

O processo de recuperação faz o seguinte:

1. Desativa o ponto final do Gestor de Tráfego Azure para a aplicação web na região original. Desativar o ponto final impede que os utilizadores se conectem à aplicação em estado inválido caso a região original fique online durante a recuperação.

2. Disponibiliza um servidor de catálogo de recuperação na região de recuperação, restaura a base de dados do catálogo e atualiza o pseudónimo do catálogo ativo para apontar para o servidor de catálogo restaurado. A alteração do pseudónimo do catálogo garante que o processo de sincronização do catálogo sincroniza sempre o catálogo ativo.

3. Marca todos os inquilinos existentes no catálogo de recuperação como offline para impedir o acesso às bases de dados dos inquilinos antes de serem restaurados.

4. Prevê uma instância da app na região de recuperação e configura-a para usar o catálogo restaurado naquela região. Para manter a latência ao mínimo, a aplicação de amostras foi concebida para se ligar sempre a uma base de dados de inquilinos na mesma região.

5. Provisões um servidor e uma piscina elástica em que novos inquilinos são aprovisionados. A criação destes recursos garante que o fornecimento de novos inquilinos não interfere com a recuperação dos inquilinos existentes.

6. Atualiza o novo pseudónimo de inquilino para apontar para o servidor para novas bases de dados de inquilinos na região de recuperação. A alteração deste pseudónimo garante que as bases de dados para quaisquer novos inquilinos sejam aprovisionadas na região de recuperação.
        
7. Provisões servidores e piscinas elásticas na região de recuperação para restaurar bases de dados de inquilinos. Estes servidores e piscinas são uma imagem espelhada da configuração na região original. O fornecimento de piscinas à frente reserva a capacidade necessária para restaurar todas as bases de dados.

    Uma paralisação numa região pode colocar uma pressão significativa sobre os recursos disponíveis na região emparelhada. Se você confiar em geo-restauro para DR, então reservar recursos rapidamente é recomendado. Considere a geo-replicação se é fundamental que uma aplicação seja recuperada numa região específica. 

8. Permite o ponto final do Gestor de Tráfego para a aplicação web na região de recuperação. Ativar este ponto final permite a aplicação para aprovisionamento de novos inquilinos. Nesta fase, os inquilinos existentes ainda estão offline.

9. Submete lotes de pedidos para restaurar bases de dados em ordem prioritária. 

    * Os lotes são organizados para que as bases de dados sejam restauradas paralelamente em todas as piscinas.  

    * Os pedidos de restauro são submetidos assincronicamente para que sejam submetidos rapidamente e em fila para execução em cada piscina.

    * Como os pedidos de restauro são processados em paralelo em todas as piscinas, é melhor distribuir inquilinos importantes em muitas piscinas. 

10. Monitoriza o serviço de base de dados SQL para determinar quando as bases de dados são restauradas. Depois de uma base de dados de inquilinos ser restaurada, está marcada online no catálogo, e uma soma de versão para a base de dados dos inquilinos é registada. 

    * As bases de dados dos inquilinos podem ser acedidas pela aplicação assim que estiverem marcadas online no catálogo.

    * Uma soma de valores de versão de linha na base de dados dos inquilinos está armazenada no catálogo. Esta soma funciona como uma impressão digital que permite ao processo de repatriamento determinar se a base de dados foi atualizada na região de recuperação.       

## <a name="run-the-recovery-script"></a>Executar o script de recuperação

> [!IMPORTANT]
> Este tutorial restaura bases de dados de backups georedundantes. Embora estes backups estejam normalmente disponíveis dentro de 10 minutos, pode levar até uma hora. O guião para até estarem disponíveis.

Imagine que há uma falha na região em que a aplicação é implementada, e executar o roteiro de recuperação:

1. No PowerShell ISE, nos módulos de aprendizagem\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1, definir o seguinte valor:

    $DemoScenario = 2: Recuperar a app numa região de recuperação, restaurando de backups georedundantes.

2. Para executar o script, selecione F5.  

    * O guião abre numa nova janela PowerShell e inicia um conjunto de trabalhos powerShell que funcionam em paralelo. Estes trabalhos restauram servidores, piscinas e bases de dados para a região de recuperação.

    * A região de recuperação é a região emparelhada associada à região de Azure em que implementou a aplicação. Para mais informações, consulte [regiões emparelhadas de Azure.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) 

3. Monitorize o estado do processo de recuperação na janela PowerShell.

    ![Processo de recuperação](media/saas-dbpertenant-dr-geo-restore/dr-in-progress.png)

> [!NOTE]
> Para explorar o código para os trabalhos de recuperação, reveja os scripts PowerShell na pasta ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\RecoveryJobs.

## <a name="review-the-application-state-during-recovery"></a>Rever o estado de candidatura durante a recuperação
Enquanto o ponto final da aplicação está desativado no Traffic Manager, a aplicação não está disponível. O catálogo está restaurado, e todos os inquilinos estão marcados offline. O ponto final da aplicação na região de recuperação está então ativado, e a aplicação está novamente online. Embora a aplicação esteja disponível, os inquilinos aparecem offline no centro de eventos até que as suas bases de dados sejam restauradas. É importante projetar a sua aplicação para lidar com bases de dados de inquilinos offline.

* Depois de a base de dados do catálogo ter sido recuperada, mas antes que os inquilinos estejam de novo on-line, refresque o centro de eventos wingtip Tickets no seu navegador web.

  * No rodapé, note que o nome do servidor do catálogo tem agora um sufixo de recuperação e está localizado na região de recuperação.

  * Note que os inquilinos que ainda não estão restaurados estão marcados como offline e não são selecionáveis.   
 
    ![Processo de recuperação](media/saas-dbpertenant-dr-geo-restore/events-hub-tenants-offline-in-recovery-region.png)    

  * Se abrir a página de eventos de um inquilino diretamente enquanto o inquilino estiver offline, a página exibe uma notificação offline do inquilino. Por exemplo, se a Sala de Concertos Contoso estiver offline, tente abrir http://events.wingtip-dpt.&lt(utilizador).trafficmanager.net/contosoconcerthall.&gt;

    ![Processo de recuperação](media/saas-dbpertenant-dr-geo-restore/dr-in-progress-offline-contosoconcerthall.png)

## <a name="provision-a-new-tenant-in-the-recovery-region"></a>Provisão de um novo inquilino na região de recuperação
Mesmo antes de as bases de dados dos inquilinos serem restauradas, você pode fornecer novos inquilinos na região de recuperação. As novas bases de dados de inquilinos aprovisionadas na região de recuperação são repatriadas com as bases de dados recuperadas mais tarde.   

1. No PowerShell ISE, nos módulos de aprendizagem\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1, definir a seguinte propriedade:

    $DemoScenario = 3: Providenciar um novo inquilino na região de recuperação.

2. Para executar o script, selecione F5.

3. A página de eventos hawthorn Hall abre no navegador ao fornecer acabamentos. 

    Note que a base de dados hawthorn Hall está localizada na região de recuperação.

    ![Hawthorn Hall provisionado na região de recuperação](media/saas-dbpertenant-dr-geo-restore/hawthorn-hall-provisioned-in-recovery-region.png)

4. No navegador, refresque a página de eventos de eventos wingtip tickets para ver Hawthorn Hall incluído. 

    Se você forprovisionou Hawthorn Hall sem esperar que os outros inquilinos restaurassem, outros inquilinos ainda podem estar offline.

## <a name="review-the-recovered-state-of-the-application"></a>Rever o estado recuperado do pedido

Quando o processo de recuperação terminar, a candidatura e todos os inquilinos estão totalmente funcionais na região de recuperação. 

1. Depois do ecrã na janela da consola PowerShell indicar que todos os inquilinos estão recuperados, refresque o centro de eventos. 

    Os inquilinos aparecem todos online, incluindo o novo inquilino, Hawthorn Hall.

    ![Recuperados e novos inquilinos no centro de eventos](media/saas-dbpertenant-dr-geo-restore/events-hub-with-hawthorn-hall.png)

2. Clique na Sala de Concertos de Contoso e abra a sua página de eventos. 

    No rodapé, note que a base de dados está localizada no servidor de recuperação localizado na região de recuperação.

    ![Contoso na região de recuperação](media/saas-dbpertenant-dr-geo-restore/contoso-recovery-location.png)

3. No [portal Azure,](https://portal.azure.com)abra a lista de grupos de recursos.  

    Note o grupo de recursos que implementou, mais o grupo de recursos de recuperação, com o sufixo de recuperação. O grupo de recursos de recuperação contém todos os recursos criados durante o processo de recuperação, além de novos recursos criados durante a paralisação. 

4. Abra o grupo de recursos de recuperação e repare nos seguintes itens:

   * As versões de recuperação do catálogo e dos inquilinos1 servidores, com o sufixo de recuperação. As bases de dados de catálogo restaurados e inquilinos nestes servidores têm todos os nomes usados na região original.

   * Os&lt;inquilinos2-dpt-&gt;servidor SQL de recuperação do utilizador. Este servidor é utilizado para fornecer novos inquilinos durante a paralisação.

   * O serviço de aplicações nomeou&lt;eventos-wingtip-dpt-recoveryregion&gt;-&lt;user&gt;, que é a instância de recuperação da app de eventos.

     ![Recursos contoso na região de recuperação](media/saas-dbpertenant-dr-geo-restore/resources-in-recovery-region.png) 
    
5. Abra os inquilinos2-dpt-&lt;user-recovery&gt;SQL servidor. Note que contém a base de dados hawthornhall e a piscina elástica Pool1. A base de dados hawthornhall está configurada como uma base de dados elástica na piscina elástica Pool1.

## <a name="change-the-tenant-data"></a>Alterar os dados do inquilino 
Nesta tarefa, atualiza uma das bases de dados de inquilinos restaurados. O processo de repatriamento copia bases de dados restauradas que foram alteradas para a região original. 

1. No seu navegador, encontre a lista de eventos para a Sala de Concertos Contoso, percorra os eventos e note o último evento, Serious Strauss.

2. No PowerShell ISE, nos módulos de aprendizagem\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1, definir o seguinte valor:

    $DemoScenario = 4: Apagar um evento de um inquilino na região de recuperação.

3. Para executar o script, selecione F5.

4. Refresque a páginahttp://events.wingtip-dpt.&ltde&gt;eventos da Sala de Concertos contoso ((utilizador .trafficmanager.net/contosoconcerthall) e note que o evento Serious Strauss está desaparecido.

Nesta altura do tutorial, recuperou a candidatura, que está agora a decorrer na região de recuperação. Você disponibilizou um novo inquilino na região de recuperação e dados modificados de um dos inquilinos restaurados.  

> [!NOTE]
> Outros tutoriais na amostra não foram concebidos para funcionar com a app no estado de recuperação. Se quiser explorar outros tutoriais, não se esqueça de repatriar a aplicação primeiro.

## <a name="repatriation-process-overview"></a>Visão geral do processo de repatriamento

O processo de repatriamento reverte a aplicação e as suas bases de dados para a sua região original após a resolução de uma paralisação.

![Repatriamento de geo-restauro](media/saas-dbpertenant-dr-geo-restore/geo-restore-repatriation.png) 

O processo:

1. Para qualquer atividade de restauro em curso e cancela quaisquer pedidos pendentes ou de base de dados a bordo.

2. Reativa as bases de dados de inquilinos da região original que não foram alteradas desde a paralisação. Estas bases de dados incluem as que ainda não foram recuperadas e as recuperadas, mas não alteradas depois. As bases de dados reativadas são exatamente como a última a ser acessada pelos seus inquilinos.

3. Prevê uma imagem espelhada do servidor do novo inquilino e piscina elástica na região original. Após esta ação estar concluída, o novo pseudónimo de inquilino é atualizado para apontar para este servidor. A atualização do pseudónimo faz com que ocorra um novo inquilino na região original em vez da região de recuperação.

3. Usa a geo-replicação para mover o catálogo para a região original da região de recuperação.

4. Atualiza a configuração do pool na região original, pelo que é consistente com as mudanças que foram feitas na região de recuperação durante a paralisação.

5. Cria os servidores e piscinas necessários para alojar quaisquer novas bases de dados criadas durante a paralisação.

6. Usa a geo-replicação para repatriar bases de dados de inquilinos restaurados que foram atualizadas após o restauro e todas as novas bases de dados de inquilinos aprovisionadas durante a paralisação. 

7. Limpa os recursos criados na região de recuperação durante o processo de recuperação.

Para limitar o número de bases de dados de inquilinos que precisam de ser repatriadas, as etapas 1 a 3 são feitas rapidamente.  

O passo 4 só é feito se o catálogo da região de recuperação tiver sido modificado durante a paralisação. O catálogo é atualizado se forem criados novos inquilinos ou se alguma base de dados ou configuração de piscina for alterada na região de recuperação.

É importante que o passo 7 cause uma interrupção mínima para os inquilinos e não se percam dados. Para atingir este objetivo, o processo utiliza a geo-replicação.

Antes de cada base de dados ser geo-replicada, a base de dados correspondente na região original é eliminada. A base de dados na região de recuperação é então geo-replicada, criando uma réplica secundária na região original. Após a replicação estar concluída, o inquilino é marcado offline no catálogo, que quebra quaisquer ligações à base de dados na região de recuperação. A base de dados é então falhada, fazendo com que quaisquer transações pendentes sejam processadas no secundário para que não se percam dados. 

No momento da falha, as funções de base de dados são invertidas. O secundário na região original torna-se a base de dados primária de leitura para leitura, e a base de dados na região de recuperação torna-se secundária. A entrada do arrendatário no catálogo é atualizada para referenciar a base de dados da região original, e o inquilino está marcado online. Neste momento, o repatriamento da base de dados está completo. 

As aplicações devem ser escritas com lógica de retry para garantir que se reconectam automaticamente quando as ligações são quebradas. Quando utilizam o catálogo para intermediar a reconexão, ligam-se à base de dados repatriada da região original. Embora a breve desconexão muitas vezes não seja notada, pode optar por repatriar bases de dados fora do horário comercial.

Após o repatriamento de uma base de dados, a base de dados secundária da região de recuperação pode ser eliminada. A base de dados da região original depende novamente do geo-restauro para a proteção de DR.

No passo 8, os recursos na região de recuperação, incluindo os servidores de recuperação e piscinas, são eliminados.

## <a name="run-the-repatriation-script"></a>Executar o roteiro de repatriamento
Imaginemos que a paralisação está resolvida e que executem o guião de repatriamento.

Se seguiu o tutorial, o guião reativa imediatamente o Fabrikam Jazz Club e o Dogwood Dojo na região original porque não mudam. Em seguida, repatria o novo inquilino, Hawthorn Hall, e A Sala de Concertos Contoso porque foi modificado. O guião também repatria o catálogo, que foi atualizado quando Hawthorn Hall foi provisionado.
  
1. No PowerShell ISE, nos módulos de aprendizagem\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1, verifique se o processo de Sincronização de Catálogo ainda está em execução na sua instância PowerShell. Se necessário, reinicie-o definindo:

    $DemoScenario = 1: Comece a sincronizar informações de servidor de inquilinos, piscina e configuração de base de dados no catálogo.

    Para executar o script, selecione F5.

2.  Em seguida, para iniciar o processo de repatriamento, definir:

    $DemoScenario = 5: Repatriar a app para a sua região original.

    Para executar o script de recuperação numa nova janela PowerShell, selecione F5. O repatriamento demora vários minutos e pode ser monitorizado na janela PowerShell.

3. Enquanto o script estiver em execução,&gt;refresque a página do centro de eventos (.userhttp://events.wingtip-dpt.&lt.trafficmanager.net).

    Note que todos os inquilinos estão online e acessíveis ao longo deste processo.

4. Selecione o Fabrikam Jazz Club para abri-lo. Se não modificou este inquilino, note pelo rodapé que o servidor já está reverteu para o servidor original.

5. Abra ou refresque a página de eventos da Sala de Concertos Contoso. Note pelo rodapé que, inicialmente, a base de dados ainda está no servidor de recuperação. 

6. Refresque a página de eventos da Sala de Concertos de Contoso quando o processo de repatriamento terminar, e note que a base de dados está agora na sua região original.

7. Refresque o centro de eventos novamente e abra o Hawthorn Hall. Note que a sua base de dados também está localizada na região original. 

## <a name="clean-up-recovery-region-resources-after-repatriation"></a>Limpar os recursos da região de recuperação após o repatriamento
Após o repatriamento estar concluído, é seguro apagar os recursos na região de recuperação. 

> [!IMPORTANT]
> Apague estes recursos prontamente para parar todas as faturas para eles.

O processo de recuperação cria todos os recursos de recuperação num grupo de recursos de recuperação. O processo de limpeza elimina este grupo de recursos e remove todas as referências aos recursos do catálogo. 

1. No PowerShell ISE, no ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1, definido:
    
    $DemoScenario = 6: Eliminar recursos obsoletos da região de recuperação.

2. Para executar o script, selecione F5.

Depois de limpar os scripts, a aplicação está de volta onde começou. Neste ponto, você pode executar o script novamente ou experimentar outros tutoriais.

## <a name="designing-the-application-to-ensure-that-the-app-and-the-database-are-co-located"></a>Conceber a aplicação para garantir que a aplicação e a base de dados estão co-localizadas 
A aplicação destina-se sempre a ligar-se a partir de um caso na mesma região que a base de dados do arrendatário. Este desenho reduz a latência entre a aplicação e a base de dados. Esta otimização pressupõe que a interação app-a-database é mais chata do que a interação entre o utilizador e a aplicação.  

As bases de dados dos inquilinos podem estar espalhadas por regiões de recuperação e originais durante algum tempo durante o repatriamento. Para cada base de dados, a aplicação procura a região em que a base de dados está localizada fazendo uma pesquisa dNS sobre o nome do servidor do inquilino. Na Base de Dados SQL, o nome do servidor é um pseudónimo. O nome do servidor pseudónimo contém o nome da região. Se a aplicação não estiver na mesma região que a base de dados, redireciona para a instância na mesma região que o servidor de base de dados. Redirecionando para a ocorrência na mesma região que a base de dados minimiza a latência entre a app e a base de dados.  

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:
> [!div class="checklist"]
> 
> * Utilize o catálogo de inquilinos para guardar informações de configuração periodicamente renovadas, o que permite criar um ambiente de recuperação de imagem espelhada noutra região.
> * Recupere as bases de dados Azure SQL na região de recuperação utilizando geo-restauro.
> * Atualize o catálogo de inquilinos para refletir as localizações restauradas da base de dados dos inquilinos. 
> * Utilize um pseudónimo DNS para permitir que uma aplicação se ligue ao catálogo de inquilinos sem reconfiguração.
> * Utilize a geo-replicação para repatriar bases de dados recuperadas para a sua região original depois de uma paragem ser resolvida.

Experimente a recuperação de [desastres para uma aplicação SaaS multiarrendatária usando](saas-dbpertenant-dr-geo-replication.md) o tutorial de georeplicação da base de dados para aprender a usar a geo-replicação para reduzir drasticamente o tempo necessário para recuperar uma aplicação multiarrendatária em larga escala.

## <a name="additional-resources"></a>Recursos adicionais

[Tutoriais adicionais que se baseiam na aplicação Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
