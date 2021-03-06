---
title: Compreender o uso da máquina virtual Azure
description: Compreender detalhes de utilização da máquina virtual
services: virtual-machines
documentationcenter: ''
author: mmccrory
manager: gwallace
editor: ''
tags: azure-virtual-machine
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 12/04/2017
ms.author: memccror
ms.openlocfilehash: 2aa175d97787d82aae062a95ed519f35ff65816b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75982263"
---
# <a name="understanding-azure-virtual-machine-usage"></a>Compreender o uso da máquina virtual Azure
Ao analisar os seus dados de utilização do Azure, podem ser adquiridos insights de consumo poderosos – insights que podem permitir uma melhor gestão e alocação de custos em toda a sua organização. Este documento proporciona um mergulho profundo nos detalhes de consumo da Sua Computação Azure. Para mais detalhes sobre o uso geral do Azure, navegue para [compreender a sua conta.](../../cost-management-billing/understand/review-individual-bill.md)

## <a name="download-your-usage-details"></a>Transferir os detalhes de utilização
Para começar, [descarregue os seus dados de utilização.](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md) O quadro abaixo fornece a definição e os valores de exemplo de utilização das Máquinas Virtuais implantadas através do Gestor de Recursos Azure. Este documento não contém informações detalhadas para VMs implementadas através do nosso modelo clássico.


| Campos             | Significado                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Valores de exemplo                                                                                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Data de Utilização         | A data em que o recurso foi usado.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |  "11/23/2017"                                                                                                                                                                                                                                                                                                                                                     |
| ID do Medidor           | Identifica o serviço de alto nível para o qual este uso pertence.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | "Máquinas Virtuais"                                                                                                                                                                                                                                                                                                                                               |
| Subcategoria do Medidor | O identificador do medidor faturado. <ul><li>Para a utilização da Compute Hour, existe um medidor para cada Tamanho VM + OS (Windows, Não Windows) + Região.</li><li>Para a utilização do software Premium, há um medidor para cada tipo de software. A maioria das imagens de software premium têm diferentes metros para cada tamanho do núcleo. Para mais informações, visite a Página de [Preços computacionais.](https://azure.microsoft.com/pricing/details/virtual-machines/)</li></ul>                                                                                                                                                                                                                                                                                                                                         | "2005544f-659d-49c9-9094-8e0aea1be3a5"                                                                                                                                                                                                                                                                                                                           |
| Nome do Medidor         | Isto é específico para cada serviço em Azure. Para a computação, é sempre "Compute Hours".                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | "Compute Hours"                                                                                                                                                                                                                                                                                                                                                  |
| Região do Medidor       | Identifica a localização do datacenter para determinados serviços cujo preço é definido com base na localização do datacenter.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |  "JA Leste"                                                                                                                                                                                                                                                                                                                                                       |
| Unidade               | Identifica a unidade em que o serviço é cobrado. Os recursos computacionais são cobrados por hora.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | "Horas"                                                                                                                                                                                                                                                                                                                                                          |
| Consumido           | A quantidade do recurso que foi consumido para aquele dia. Para a Compute, contamos por cada minuto que o VM correu durante uma determinada hora (até 6 decimais de precisão).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |    "1", "0.5"                                                                                                                                                                                                                                                                                                                                                    |
| Localização do Recurso  | Identifica o datacenter onde o recurso está em execução.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | "JA Leste"                                                                                                                                                                                                                                                                                                                                                        |
| Serviço Consumido   | O serviço de plataforma Azure que usou.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | "Microsoft.Compute"                                                                                                                                                                                                                                                                                                                                              |
| Grupo de Recursos     | O grupo de recursos no qual o recurso implementado está em execução. Para mais informações, consulte a [visão geral do Gestor de Recursos do Azure.](../../azure-resource-manager/management/overview.md)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |    "MyRG"                                                                                                                                                                                                                                                                                                                                                        |
| ID da Instância        | O identificador para o recurso. O identificador contém o nome que especificou para o recurso quando o criou. Para VMs, o ID de instância conterá o Id de Subscrição, o Nome do Grupo de Recursos e o Nome VMName (ou nome de conjunto de escala para a utilização do conjunto de escala).                                                                                                                                                                                                                                                                                                                                                                                                                    | "/subscrições/xxxxxxxx-xxxx-xxxx-xxxxxxxxxxxxx/ recursosGroups/MyRG/providers/Microsoft.Compute/virtualMachines/MyVM1"<br><br>ou<br><br>"/subscrições/xxxxxxxx-xxxx-xxxx-xxxxxxxxxxxxx/ recursosGroups/MyRG/providers/Microsoft.Compute/virtualMachineScaleSets/MyVMSS1"                                                                                           |
| Etiquetas               | Marque a atribuição ao recurso. Utilize etiquetas para agrupar os registos de faturação. Aprenda a [etiquetar as suas Máquinas Virtuais.](tag.md) Isto está disponível apenas para VMs de Gestor de Recursos.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | "{"myDepartment":"RD", "myUser":"myName"}"                                                                                                                                                                                                                                                                                                                        |
| Informações Adicionais    | Metadados específicos do serviço. Para VMs, povoamos o seguinte no campo de informações adicionais: <ul><li>Tipo de imagem imagem imagem específica que executou. Encontre a lista completa de cordas suportadas abaixo em Tipos de Imagem.</li><li>Tipo de serviço: o tamanho que implementou.</li><li>VMName: nome do seu VM. Isto só é povoado para vMs de escala. Se precisar do nome VM para vM s, pode encontrar isso na cadeia de ID de instância acima.</li><li>Tipo de utilização: Isto especifica o tipo de utilização que isto representa.<ul><li>ComputeHR é o uso da Hora da Computação para o VM subjacente, como Standard_D1_v2.</li><li>ComputeHR_SW é a taxa de software premium se o VM estiver a usar software premium, como o Microsoft R Server.</li></ul></li></ul>    | Máquinas Virtuais {"ImageType":"Canonical","ServiceType":"Standard_DS1_v2","VMName":"", "UsageType":"ComputeHR"}<br><br>Conjuntos de escala de máquina virtual {"ImageType":"Canonical","ServiceType":"Standard_DS1_v2","VMName":"myVM1", "UsageType":"ComputeHR"}<br><br>Software premium {"ImageType":""ServiceType":"Standard_DS1_v2","VMName":"", "UsageType":"ComputeHR_SW"} |

## <a name="image-type"></a>Tipo de Imagem
Para algumas imagens na galeria Azure, o tipo de imagem é povoado no campo Informação Adicional. Isto permite que os utilizadores compreendam e rastreiem o que implementaram na sua Máquina Virtual. Os valores que são povoados neste campo com base na imagem que implementou são os seguintes:
  - BitRock 
  - Canónico 
  - FreeBSD 
  - Lógica Aberta 
  - Oracle 
  - SLES para SAP 
  - Pré-visualização do Servidor SQL 14 na pré-visualização do Windows Server 2012 R2 
  - SUSE
  - Prémio SUSE
  - StorSimple Cloud Appliance 
  - Red Hat
  - Chapéu Vermelho para Aplicações Empresariais SAP     
  - Chapéu Vermelho para SAP HANA 
  - Cliente Windows BYOL 
  - Windows Server BYOL 
  - Pré-visualização do servidor do Windows 

## <a name="service-type"></a>Tipo de Serviço
O campo do tipo de serviço no campo Informação Adicional corresponde ao tamanho exato de VM que implementou. Os VMs de armazenamento premium (baseados em SSD) e VMs de armazenamento não premium (baseadoem em HDD) têm o mesmo preço. Se implementar um tamanho\_baseado em SSD,\_como o Standard DS2 v2,\_vê\_o tamanho não-SSD ('Standard D2 v2 VM') na coluna subcategoria do medidor e no tamanho SSD ('Standard\_DS2\_v2') no campo Informação Adicional.

## <a name="region-names"></a>Nomes da região
O nome da região povoado no campo de Localização de Recursos nos detalhes de utilização varia do nome da região utilizado no Gestor de Recursos Azure. Aqui está um mapeamento entre os valores da região:

|    **Nome da região do gestor de recursos**       |    **Localização de recursos em detalhes de utilização**    |
|--------------------------|------------------------------------------|
|    austráliaeast         |    AU Leste                               |
|    australiasoutheast    |    AU Sudeste                          |
|    brazilsouth           |    Sul BR                              |
|    CanadáCentral         |    CA Central                            |
|    CanadáEast            |    CA Leste                               |
|    Índia Central          |    IN Central                            |
|    centralus             |    E.U.A. Central                            |
|    chinaeast             |    Leste da China                            |
|    chinanorth            |    Norte da China                           |
|    eastasia              |    Ásia Leste                             |
|    eastus                |    E.U.A. Leste                               |
|    eastus2               |    E.U.A. Leste 2                             |
|    AlemanhaCentral        |    DE Central                            |
|    AlemanhaNordeste      |    DE Nordeste                          |
|    japaneast             |    JA Leste                               |
|    japãowest             |    JA Oeste                               |
|    KoreaCentral          |    KR do Sul Central                            |
|    Coreia do Sul            |    Sul KR do Sul                              |
|    centro-norte        |    E.U.A. Centro-Norte                      |
|    northeurope           |    Europa do Norte                          |
|    southcentralus        |    E.U.A. Centro-Sul                      |
|    southeastasia         |    Ásia Sudeste                        |
|    Sul da Índia            |    IN Sul                              |
|    UKNorth               |    EUA Norte                              |
|    uksouth               |    Sul do Reino Unido                              |
|    UKSouth2              |    Sul do Reino Unido 2                            |
|    ukwest                |    Oeste do Reino Unido                               |
|    USDodCentral          |    US DoD Centro                        |
|    USDoDEast             |    US DoD - Leste                           |
|    USGovArizona          |    USGov Arizona                         |
|    usgoviowa             |    USGov Iowa                            |
|    USGovTexas            |    USGov Texas                           |
|    usgovvirginia         |    USGov Virginia                        |
|    E.U.A. Centro-Oeste         |    E.U.A. Centro-Oeste                       |
|    westeurope            |    Europa ocidental                           |
|    Índia Ocidental             |    IN Oeste                               |
|    westus                |    E.U.A. Oeste                               |
|    westus2               |    E.U.A. Oeste 2                             |


## <a name="virtual-machine-usage-faq"></a>FAQ de utilização de máquina virtual
### <a name="what-resources-are-charged-when-deploying-a-vm"></a>Que recursos são cobrados na implementação de um VM?    
Os VMs adquirem custos para o próprio VM, qualquer software premium em execução no VM, a conta de armazenamento\geriu o disco associado ao VM, e as transferências de largura de banda em rede a partir do VM.
### <a name="how-can-i-tell-if-a-vm-is-using-azure-hybrid-benefit-in-the-usage-csv"></a>Como posso saber se um VM está a usar o Benefício Híbrido Azure no CSV de utilização?
Se implementar utilizando o [Azure Hybrid Benefit,](https://azure.microsoft.com/pricing/hybrid-benefit/)é-lhe cobrada a taxa VM não Windows, uma vez que está a trazer a sua própria licença para a nuvem. Na sua conta, pode distinguir quais Os VMs do Gestor de\_Recursos estão a executar\_o Azure Hybrid Benefit porque têm "Windows Server BYOL" ou "Windows Client BYOL" na coluna ImageType.
### <a name="how-are-basic-vs-standard-vm-types-differentiated-in-the-usage-csv"></a>Como são diferenciados os Tipos De VM Básicovs. Standard no CSV de utilização?
São oferecidos vMs básicos e standard série A. Se implantar um VM básico, na categoria Sub do medidor, tem a corda "Basic". Se implementar um VM série A padrão, então o tamanho VM aparece como "A1 VM" uma vez que o Standard é o padrão. Para saber mais sobre as diferenças entre Básico e Standard, consulte a [Página de Preços](https://azure.microsoft.com/pricing/details/virtual-machines/).
### <a name="what-are-extrasmall-small-medium-large-and-extralarge-sizes"></a>O que são tamanhos ExtraSmall, Small, Medium, Large e ExtraLarge?
ExtraSmall - ExtraLarge são os\_nomes\_legados do Standard A0 – Standard A4. Nos registos clássicos de utilização de VM, pode ver esta convenção usada se tiver implantado estes tamanhos.
### <a name="what-is-the-difference-between-meter-region-and-resource-location"></a>Qual é a diferença entre a Região do Medidor e a Localização dos Recursos?
A Região do Medidor está associada ao contador. Para alguns serviços Azure que utilizam um preço para todas as regiões, o campo da Região dos Contadores pode ficar em branco. No entanto, uma vez que os VMs têm preços dedicados por região para máquinas virtuais, este campo é povoado. Da mesma forma, a Localização do Recurso para Máquinas Virtuais é o local onde o VM é implantado. A região de Azure em ambos os campos é a mesma, embora possam ter uma convenção de cordas diferente para o nome da região.
### <a name="why-is-the-imagetype-value-blank-in-the-additional-info-field"></a>Por que o valor do ImageType está em branco no campo Informação Adicional?
O campo ImageType só é povoado para um subconjunto de imagens. Se não tiver implantado uma das imagens acima, o ImageType fica em branco.
### <a name="why-is-the-vmname-blank-in-the-additional-info"></a>Por que o VMName está em branco na Informação Adicional?
O VMName só é povoado no campo Informação Adicional para VMs num conjunto de escala. O campo InstanceID contém o nome VM para VMs conjuntos não dimensionais.
### <a name="what-does-computehr-mean-in-the-usagetype-field-in-the-additional-info"></a>O que significa ComputeHR no campo UsageType no Adicional info?
ComputeHR significa Compute Hour que representa o evento de utilização para o custo de infraestrutura subjacente. Se o UsageType for\_ComputeHR SW, o evento de utilização representa a taxa de software premium para o VM.
### <a name="how-do-i-know-if-i-am-charged-for-premium-software"></a>Como sei se sou cobrado por software premium?
Ao explorar qual a Imagem VM que melhor se adequa às suas necessidades, certifique-se de que confere o [Azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute) A imagem tem a taxa do plano de software. Se vir "Free" para a tarifa, não há custo adicional para o software. 
### <a name="what-is-the-difference-between-microsoftclassiccompute-and-microsoftcompute-in-the-consumed-service"></a>Qual é a diferença entre microsoft.ClassicCompute e Microsoft.Compute no serviço Consumido?
Microsoft.ClassicCompute representa recursos clássicos implementados através do Gestor de Serviços Azure. Se implementar através do Gestor de Recursos, então a Microsoft.Compute é povoada no serviço consumido. Saiba mais sobre os [modelos de Implantação Azure.](../../azure-resource-manager/management/deployment-models.md)
### <a name="why-is-the-instanceid-field-blank-for-my-virtual-machine-usage"></a>Porque é que o campo InstanceID está em branco para o meu uso da Máquina Virtual?
Se implementar através do modelo de implementação clássico, a cadeia InstanceID não está disponível.
### <a name="why-are-the-tags-for-my-vms-not-flowing-to-the-usage-details"></a>Porque é que as etiquetas para os meus VMs não estão a fluir para os detalhes de uso?
As etiquetas só fluem para si o CSV de utilização apenas para VMs de Gestor de Recursos. As etiquetas de recursos clássicas não estão disponíveis nos detalhes de utilização.
### <a name="how-can-the-consumed-quantity-be-more-than-24-hours-one-day"></a>Como pode a quantidade consumida ser mais de 24 horas por dia?
No modelo Classic, a faturação de recursos é agregada ao nível do Serviço cloud. Se tiver mais de um VM num Serviço cloud que usa o mesmo medidor de faturação, o seu uso é agregado em conjunto. Os VMs implantados via Resource Manager são faturados ao nível de VM, pelo que esta agregação não se aplicará.
### <a name="why-is-pricing-not-available-for-dsfsgsls-sizes-on-the-pricing-page"></a>Porque é que os preços não estão disponíveis para os tamanhos DS/FS/GS/LS na página de preços?
Os VMs capazes de armazenamento premium são cobrados ao mesmo ritmo que os VMs de armazenamento não premium. Apenas os seus custos de armazenamento diferem. Visite a página de preços de [armazenamento](https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/) para mais informações.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre os seus detalhes de utilização, consulte a sua conta para o [Microsoft Azure.](../../cost-management-billing/understand/review-individual-bill.md)

