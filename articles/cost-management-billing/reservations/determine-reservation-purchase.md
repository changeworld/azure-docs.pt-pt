---
title: Determinar a reserva do Azure que deve comprar
description: Este artigo ajuda a determinar a reserva que deve comprar.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/22/2020
ms.author: banders
ms.openlocfilehash: 1b639da3494c0527141347ca61e77980d29a59ea
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "80135560"
---
# <a name="determine-what-reservation-to-purchase"></a>Determinar a reserva a comprar

Todas as reservas, com exceção do Azure Databricks, são aplicadas de hora a hora. Deve comprar reservas com base numa utilização consistente. Existem várias formas de determinar o que comprar e este artigo ajuda a determinar a reserva que deve comprar.

Comprar mais capacidade do que os seus resultados de utilização histórica resulta numa reserva subutilizada. Deve evitar a subutilização sempre que possível. A capacidade reservada não utilizada não transita de uma hora para a hora seguinte.  A utilização que ultrapasse a quantidade reservada é cobrada com taxas pay as you go mais caras.

## <a name="analyze-usage-data"></a>Analisar dados de utilização

Utilize as secções seguintes para ajudar a analisar os seus dados de utilização diária para determinar a sua utilização base e a reserva que deve comprar.

### <a name="analyze-usage-for-a-vm-reserved-instance-purchase"></a>Analisar a utilização para a compra de uma instância de VM reservada

Identifique o tamanho de VM certo para a sua compra. Por exemplo, uma reserva comprada para VMs de série ES não se aplica a VMs de série E, e vice-versa.

As VMs de série Promo não recebem um desconto de reserva. Por isso, remova-as da sua análise.

Para se restringir à utilização de VM elegível, aplique os seguintes filtros nos seus dados de utilização:

- Filtre **MeterCategory** para **Máquinas Virtuais**.
- Obtenha informações de **ServiceType** a partir de **AdditionalInfo**. A informação sugere o tamanho de VM certo. Por exemplo, Standard E32.
- Utilize o campo **ResourceLocation** para determinar o datacenter de utilização.

Ignore os recursos que tenham menos de 24 horas de utilização num dia.

Se quiser analisar ao nível da família de tamanho de instância, pode obter os valores de flexibilidade de tamanho de instância em [https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv](https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv). Combine os valores com os seus dados para fazer a análise. Para obter mais informações sobre a flexibilidade de tamanho de instância, veja [Flexibilidade de tamanho de máquina virtual com Instâncias de Máquina Virtual Reservadas](../../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).

### <a name="analyze-usage-for-a-sql-database-reserved-instance-purchase"></a>Analisar a utilização para a compra de uma instância reservada de Base de Dados SQL

A capacidade reservada aplica-se aos preços de computação de vCore das Bases de Dados SQL. Não se aplica aos preços baseados em DTU, custo da licença do SQL ou a quaisquer custos para além dos de computação.

Para restringir a utilização de SQL elegível, aplique os seguintes filtros nos seus dados de utilização:


- Filtre **MeterCategory** para **Base de Dados SQL**.
- Filtre **MeterName** para **vCore**.
- Filtre **MeterSubCategory** para todos os registos de utilização que tenham _Compute_ no nome.

A partir de **AdditionalInfo**, obtenha o valor de **vCores**. Este valor indica a quantidade de vCores que foram utilizados. A quantidade corresponde a **vCores** multiplicado pelo número de horas que a base de dados foi utilizada.

Os dados informam sobre a utilização consistente para:

- Combinação do tipo de base de dados. Por exemplo, instância gerida ou conjunto elástico por base de dados individual.
- Escalão de serviço. Por exemplo, fins gerais ou crítico para a empresa.
- Geração. Por exemplo, Ger 5.
- Localização do Recurso

### <a name="analysis-for-sql-data-warehouse"></a>Análise para o SQL Data Warehouse

A capacidade reservada aplica-se à utilização de DWU do SQL Data Warehouse e é comprada em incrementos de 100 DWU. Para restringir a utilização de SQL elegível, aplique os seguintes filtros nos seus dados de utilização:

- Filtre **MeterName** para **100 DWUs**.
- Filtre **Subcategoria do Medidor** para **Otimizado para Computação Ger2**.

Utilize o campo **Localização do Recurso** para determinar a utilização do SQL DW numa região.

A utilização do SQL Data Warehouse pode aumentar ou reduzir verticalmente ao longo do dia. Fale com a equipa que geriu a instância do SQL Data Warehouse para obter informações sobre a utilização base.

Aceda a Reservas no portal do Azure e compre capacidade reservada do SQL Data Warehouse em múltiplos de 100 DWUs.

## <a name="reservation-purchase-recommendations"></a>Recomendações de compra de reservas

As recomendações de compra de reservas são calculadas através da análise dos seus dados de utilização horária ao longo dos últimos 7, 30 e 60 dias. O Azure calcula quais seriam os seus custos se tivesse uma reserva e compara-os com os seus custos reais pay as you go incorridos ao longo do período de tempo. O cálculo é realizado para todas as quantidades que utilizou durante o período de tempo. É recomendada a quantidade que maximiza a sua poupança. 

Por exemplo, poderá utilizar 500 VMs a maior parte do tempo, mas às vezes a utilização aumenta subitamente para 700 VMs. Neste exemplo, o Azure calcula as suas poupanças tanto para as quantidades de 500 como de 700 VM. Como a utilização de 700 VM é esporádica, o cálculo da recomendação determina que as poupanças são maximizadas numa compra de reserva de 500 VM e a recomendação é fornecida para essa quantidade.

Tenha em atenção os seguintes pontos:

- As recomendações de reserva são calculadas utilizando as taxas de utilização a pedido que se aplicam a si.
- As recomendações são calculadas para tamanhos individuais, não para a família de tamanho da instância.
- A quantidade recomendada para um âmbito é reduzida no mesmo dia em que compra reservas para o âmbito.
    - No entanto, uma atualização da recomendação de quantidade de reserva em todos os âmbitos pode demorar até 25 dias. Por exemplo, se comprar com base em recomendações de âmbito partilhado, as recomendações de âmbito de subscrição individual podem demorar até 25 dias a serem ajustadas.

## <a name="recommendations-in-the-azure-portal"></a>Recomendações no portal do Azure

As compras de reservas calculadas pelo motor de recomendações são apresentadas no separador **Recomendado** no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/docs). Segue-se uma imagem de exemplo.

![Imagem que mostra recomendações](./media/determine-reservation-purchase/select-product-ri.png)

## <a name="recommendations-in-the-cost-management-power-bi-app"></a>Recomendações na aplicação Power BI do Cost Management

Os clientes do Contrato Enterprise e do Contrato de Cliente Microsoft podem utilizar os relatórios de RI de VM para recomendações de compra e VMs. Os relatórios de cobertura mostram a utilização total e a utilização coberta pelas instâncias reservadas.

1. Obtenha a [Cost Management App](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp).
2. Aceda ao relatório de Cobertura de RI de VM – Partilhado ou Âmbito único, consoante o âmbito em que pretenda comprar.
3. Selecione a região, a família de tamanho de instância para ver a utilização, a cobertura de RI e a recomendação de compra para o filtro selecionado.

## <a name="recommendations-in-azure-advisor"></a>Recomendações do Assistente do Azure

As recomendações de compra de reservas estão disponíveis no [Assistente do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Expert/AdvisorMenuBlade/overview).

- O Assistente tem apenas recomendações de âmbito de subscrição única.
- As recomendações do Assistente são calculadas com um período retroativo de 30 dias. As poupanças previstas são para um período de reserva de 3 anos.
- Se comprar uma reserva de âmbito partilhado, as recomendações de compra de reservas do Assistente podem demorar até 30 dias a desaparecer.

## <a name="recommendations-using-apis"></a>Recomendações com APIs

Utilize a API REST [Recomendações de Reservas](/rest/api/consumption/reservationrecommendations/list) para ver as recomendações de forma programática.

## <a name="next-steps"></a>Passos seguintes

- [Gerir o Azure Reservations](manage-reserved-vm-instance.md)
- [Compreender a utilização de reservas na sua subscrição com taxas pay as you go](understand-reserved-instance-usage.md)
- [Compreender a utilização de reservas na inscrição Enterprise](understand-reserved-instance-usage-ea.md)
- [Custos de software Windows não incluídos nas reservas](reserved-instance-windows-software-costs.md)