---
title: Consulta Performance Insight - Base de Dados Azure para MariaDB
description: Este artigo descreve a funcionalidade Desinformação do Desempenho da Consulta na Base de Dados Azure para MariaDB
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 88777ee44551ed6abdb7a6c7c909d6bf55db48c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527849"
---
# <a name="query-performance-insight-in-azure-database-for-mariadb"></a>Query Performance Insight no Azure Database for MariaDB

**Aplica-se a:** Base de Dados Azure para MariaDB 10.2

A Consulta Performance Insight ajuda-o a identificar rapidamente quais são as suas consultas de execução mais longas, como mudam ao longo do tempo, e quais as esperas que as estão a afetar.

## <a name="common-scenarios"></a>Cenários comuns

### <a name="long-running-queries"></a>Consultas de execução prolongada

- Identificando as consultas de execução mais longas nas últimas x horas
- Identificação de consultas de topo n que estão à espera de recursos
 
### <a name="wait-statistics"></a>Estatísticas de espera

- Compreender a natureza de esperar por uma consulta
- Compreender as tendências para a espera de recursos e onde existe a contenção de recursos

## <a name="permissions"></a>Permissões

As permissões **Proprietário**ou **Contribuidor** necessárias para ver o texto das consultas no Query Performance Insight. O **Leitor** pode ver gráficos e tabelas, mas não o texto da consulta.

## <a name="prerequisites"></a>Pré-requisitos

Para que a Consulta Performance Insight funcione, os dados devem existir na Loja de [Consultas](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Visualização de insights de desempenho

A vista [Query Performance Insight](concepts-query-performance-insight.md) no portal do Azure vai apresentar visualizações sobre informações importantes do Arquivo de Consultas.

Na página do portal da sua Base de Dados Azure para servidor MariaDB, selecione **Query Performance Insight** sob a secção **de Desempenho Inteligente** da barra de menus.

### <a name="long-running-queries"></a>Consultas de execução prolongada

O separador de consultas de **longa duração** mostra as 5 principais consultas por duração média por execução, agregadas em intervalos de 15 minutos. Pode ver mais consultas selecionando a partir do **número de consultas** que descem. Ao fazer isso, as cores do gráfico podem ser alteradas para um ID de Consulta específico.

Pode clicar e arrastar no gráfico para restringir a uma janela de tempo específica. Em alternativa, utilize o zoom para dentro e para fora ícones para ver um período de tempo menor ou maior, respectivamente.

![Consultas de longa duração do Question Performance Insight](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

### <a name="wait-statistics"></a>Estatísticas de espera 

> [!NOTE]
> As estatísticas de espera destinam-se a problemas de resolução de problemas de desempenho. Recomenda-se que seja ligado apenas para efeitos de resolução de problemas. <br>Se receber a mensagem de erro no portal Azure "*O problema encontrado para 'Microsoft.DBforMariaDB'; não pode cumprir o pedido. Se este problema continuar ou for inesperado, contacte o suporte com esta informação."* ao visualizar as estatísticas de espera, use um período de tempo menor.

As estatísticas de espera fornecem uma visão dos eventos de espera que ocorrem durante a execução de uma consulta específica. Saiba mais sobre os tipos de eventos de espera na documentação do [motor MySQL](https://go.microsoft.com/fwlink/?linkid=2098206).

Selecione o separador **Estatísticas de Espera** para ver as visualizações correspondentes sobre esperas no servidor.

As consultas apresentadas na vista das estatísticas de espera são agrupadas pelas consultas que exibem as maiores esperas durante o intervalo de tempo especificado.

![Consulta Performance Insight aguarda estatísticas](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [monitorização e sintonização](concepts-monitoring.md) na Base de Dados Azure para MariaDB.