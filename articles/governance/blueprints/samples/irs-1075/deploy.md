---
title: Implementar amostra de planta IRS 1075
description: Desloque os passos para a amostra de planta IRS 1075 (Rev.11-2016), incluindo detalhes do parâmetro de artefacto de plantas.
ms.date: 11/20/2019
ms.topic: sample
ms.openlocfilehash: 15fcac5bfd11d889522d078853bd6f916eb54616
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74546807"
---
# <a name="deploy-the-irs-1075-blueprint-sample"></a>Implementar a amostra de plantas IRS 1075

Para implantar a amostra de plantas azure IRS 1075 (Rev.11-2016), devem ser tomadas as seguintes medidas:

> [!div class="checklist"]
> - Criar uma nova planta a partir da amostra
> - Marque a sua cópia da amostra como **Publicado**
> - Atribuir a sua cópia da planta a uma subscrição existente

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-blueprint-from-sample"></a>Criar a planta a partir da amostra

Em primeiro lugar, implemente a amostra de planta criando uma nova planta no seu ambiente usando a amostra como entrada.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Plantas**.

1. A partir da página **iniciar** à esquerda, selecione o botão **Criar** _uma planta_.

1. Encontre a amostra de plantas **IRS 1075 (Rev.11-2016)** em _Outras Amostras_ e selecione **Utilize esta amostra**.

1. Introduza os _Fundamentos_ da amostra de plantas:

   - **Nome**da planta : Forneça um nome para a sua cópia da amostra de plantas IRS 1075 (Rev.11-2016).
   - **Localização da definição**: Utilize a elipse e selecione o grupo de gestão para guardar a sua cópia da amostra para.

1. Selecione o separador _Artefactos_ na parte superior da página ou **Seguinte: Artefactos** na parte inferior da página.

1. Reveja a lista de artefactos que compõem a amostra da planta. Muitos dos artefactos têm parâmetros que definiremos mais tarde. Selecione **Guardar Rascunho** quando terminar de rever a amostra de plantas.

## <a name="publish-the-sample-copy"></a>Publicar a cópia da amostra

A sua cópia da amostra de plantas foi agora criada no seu ambiente. É criado em modo **Draft** e deve ser **publicado** antes de ser atribuído e implantado. A cópia da amostra de plantas pode ser personalizada para o seu ambiente e necessidades, mas essa modificação pode afastá-la do alinhamento com os comandos NIST SP 800-53.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Plantas**.

1. Selecione a página de **definições** de Blueprint à esquerda. Utilize os filtros para encontrar a sua cópia da amostra de plantas e, em seguida, selecione-a.

1. Selecione **Publicar a planta** no topo da página. Na nova página à direita, forneça uma **Versão** para a sua cópia da amostra de plantas. Esta propriedade é útil para se fizer uma modificação mais tarde. Forneça notas de **alteração** como "Primeira versão publicada a partir da amostra de plantas NIST SP 800-53 R4." Em seguida, **selecione Publicar** na parte inferior da página.

## <a name="assign-the-sample-copy"></a>Atribuir a cópia da amostra

Uma vez que a cópia da amostra de plantas tenha sido **publicada**com sucesso, pode ser atribuída a uma subscrição dentro do grupo de gestão a que foi guardada. Este passo é onde são fornecidos parâmetros para tornar cada implantação da cópia da amostra de plantas única.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Plantas**.

1. Selecione a página de **definições** de Blueprint à esquerda. Utilize os filtros para encontrar a sua cópia da amostra de plantas e, em seguida, selecione-a.

1. Selecione **a planta de atribuição** na parte superior da página de definição de planta.

1. Fornecer os valores do parâmetro para a atribuição do projeto:

   - Noções básicas

     - **Assinaturas**: Selecione uma ou mais das subscrições que estão no grupo de gestão para a qual guardou a sua cópia da amostra de projeto. Se selecionar mais do que uma subscrição, será criada uma atribuição para cada utilização dos parâmetros introduzidos.
     - **Nome de atribuição**: O nome é pré-povoado para si com base no nome da planta.
       Mude conforme necessário ou saia como está.
     - **Localização**: Selecione uma região para a identidade gerida a criar. O Azure Blueprint utiliza esta identidade gerida para implementar todos os artefactos no esquema atribuído. Para saber mais, consulte [identidades geridas para os recursos do Azure.](../../../../active-directory/managed-identities-azure-resources/overview.md)
     - Versão de **definição**de planta : Escolha uma versão **publicada** da sua cópia da amostra de plantas.

   - Atribuição de bloqueio

     Selecione a definição de bloqueio da planta para o seu ambiente. Para obter mais informações, veja [bloqueio de recurso em esquemas](../../concepts/resource-locking.md).

   - Identidade Gerida

     Deixe o sistema predefinido _atribuído_ à opção de identidade gerida.

   - Parâmetros de artefacto

     Os parâmetros definidos nesta secção aplicam-se ao artefacto sob o qual é definido. Estes parâmetros são [parâmetros dinâmicos](../../concepts/parameters.md#dynamic-parameters) uma vez que são definidos durante a atribuição da planta. Para obter uma lista completa ou parâmetros de artefactos e suas descrições, consulte a [tabela de parâmetros do Artefacto](#artifact-parameters-table).

1. Uma vez introduzidos todos os parâmetros, **selecione Atribuir** na parte inferior da página. A atribuição da planta é criada e a implantação de artefactos começa. O destacamento demora cerca de uma hora. Para verificar o estado da implantação, abra a atribuição da planta.

> [!WARNING]
> O serviço Azure Blueprints e as amostras de plantas incorporadas estão **isentos de custos.** Os recursos azure são [avaliados pelo produto.](https://azure.microsoft.com/pricing/) Utilize a [calculadora](https://azure.microsoft.com/pricing/calculator/) de preços para estimar o custo dos recursos de funcionamento implantados por esta amostra de plantas.

## <a name="artifact-parameters-table"></a>Tabela de parâmetros de artefactos

A tabela seguinte fornece uma lista dos parâmetros do artefacto da planta:

|Nome do artefacto|Tipo de artefacto|Nome do parâmetro|Descrição|
|-|-|-|-|
|Auditoria IRS 1075 (Rev.11-2016) controla e implementa extensões vm específicas para apoiar os requisitos de auditoria|Atribuição de política|Log Analytics workspace ID para que os VMs devem ser configurados para|Este é o ID (GUID) do espaço de trabalho Log Analytics para o que os VMs devem ser configurados.|
|Auditoria IRS 1075 (Rev.11-2016) controla e implementa extensões vm específicas para apoiar os requisitos de auditoria|Atribuição de política|Lista de tipos de recursos que devem ter registos de diagnóstico ativados|Lista de tipos de recursos para auditar se a definição de registo de diagnóstico não estiver ativada. Valores aceitáveis podem ser encontrados em [registos de diagnóstico do Monitor Azure.](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type)|
|Auditoria IRS 1075 (Rev.11-2016) controla e implementa extensões vm específicas para apoiar os requisitos de auditoria|Atribuição de política|Lista de utilizadores que devem ser excluídos do grupo de Administradores VM do Windows|Uma lista separada do ponto-e-vírgula dos membros que deve ser excluída no grupo local dos administradores. Ex: Administrador; myUser1; myUser2|
|Auditoria IRS 1075 (Rev.11-2016) controla e implementa extensões vm específicas para apoiar os requisitos de auditoria|Atribuição de política|Lista de utilizadores que devem ser incluídos no grupo de Administradores VM do Windows|Uma lista separada do ponto-e-vírgula dos membros que deve ser incluída no grupo local administradores. Ex: Administrador; myUser1; myUser2|
|Implementar o agente de análise de registo para conjuntos de escala SM (VMSS) (VMSS)|Atribuição de política|Log Analytics espaço de trabalho para conjuntos de escala De VM Linux (VMSS)|Se este espaço de trabalho estiver fora do âmbito da atribuição, deve conceder manualmente permissões de "Log Analytics Contributor" (ou similares) ao ID principal da atribuição de apólices.|
|Implementar o agente de análise de registo para conjuntos de escala SM (VMSS) (VMSS)|Atribuição de política|Opcional: Lista de imagens VM que apoiaram o Sistema Linux OS para adicionar ao âmbito|Uma matriz vazia pode ser utilizada para indicar que não há parâmetros opcionais:\[\]|
|Implementar o Agente de Análise de Registos para VMs Linux|Atribuição de política|Log Analytics espaço de trabalho para VMs Linux|Se este espaço de trabalho estiver fora do âmbito da atribuição, deve conceder manualmente permissões de "Log Analytics Contributor" (ou similares) ao ID principal da atribuição de apólices.|
|Implementar o Agente de Análise de Registos para VMs Linux|Atribuição de política|Opcional: Lista de imagens VM que apoiaram o Sistema Linux OS para adicionar ao âmbito|Uma matriz vazia pode ser utilizada para indicar que não há parâmetros opcionais:\[\]|
|Implementar o agente de análise de registo para conjuntos de escala seletiva seletiva seletiva (VMSS)|Atribuição de política|Log Analytics espaço de trabalho para conjuntos de escala VM windows (VMSS)|Se este espaço de trabalho estiver fora do âmbito da atribuição, deve conceder manualmente permissões de "Log Analytics Contributor" (ou similares) ao ID principal da atribuição de apólices.|
|Implementar o agente de análise de registo para conjuntos de escala seletiva seletiva seletiva (VMSS)|Atribuição de política|Opcional: Lista de imagens VM que têm suportado o Windows OS para adicionar ao âmbito|Uma matriz vazia pode ser utilizada para indicar que não há parâmetros opcionais:\[\]|
|Implementar o Agente de Análise de Registos para VMs do Windows|Atribuição de política|Log Analytics espaço de trabalho para VMs do Windows|Se este espaço de trabalho estiver fora do âmbito da atribuição, deve conceder manualmente permissões de "Log Analytics Contributor" (ou similares) ao ID principal da atribuição de apólices.|
|Implementar o Agente de Análise de Registos para VMs do Windows|Atribuição de política|Opcional: Lista de imagens VM que têm suportado o Windows OS para adicionar ao âmbito|Uma matriz vazia pode ser utilizada para indicar que não há parâmetros opcionais:\[\]|
|Implementar proteção avançada de ameaças em contas de armazenamento|Atribuição de política|Efeito|Informações sobre efeitos políticos podem ser encontradas na [Understand Azure Policy Effects](../../../policy/concepts/effects.md)|
|Implementar auditoria sql|Atribuição de política|O valor nos dias do período de retenção (0 indica retenção ilimitada)|Dias de retenção (opcional, 180 dias se não especificados)|
|Implementar auditoria sql|Atribuição de política|Nome do grupo de recursos para conta de armazenamento para auditoria de servidor SQL|A auditoria escreve eventos de base de dados para um registo de auditoria na sua conta de Armazenamento Azure (será criada uma conta de armazenamento em cada região onde é criado um Servidor SQL que será partilhado por todos os servidores dessa região). Importante - para o bom funcionamento da Auditoria não apague ou mude o nome do grupo de recursos ou das contas de armazenamento.|
|Implementar definições de diagnóstico para grupos de segurança da rede|Atribuição de política|Prefixo de conta de armazenamento para diagnósticos de grupo de segurança de rede|Este prefixo será combinado com a localização do grupo de segurança da rede para formar o nome da conta de armazenamento criada.|
|Implementar definições de diagnóstico para grupos de segurança da rede|Atribuição de política|Nome do grupo de recursos para conta de armazenamento para diagnósticos de grupo de segurança de rede (deve existir)|O grupo de recursos em que a conta de armazenamento será criada. Este grupo de recursos já deve existir.|

## <a name="next-steps"></a>Passos seguintes

Agora que reviu os passos para implementar a amostra de plantas do IRS 1075 (Rev.11-2016), visite os seguintes artigos para conhecer a planta e o mapeamento de controlo:

> [!div class="nextstepaction"]
> [Projeto IRS 1075 - Visão geral](./index.md)
> [do irs 1075 - Mapeamento](./control-mapping.md) de controlo

Artigos adicionais sobre esquemas e como os utilizar:

- Conheça o ciclo de vida da [planta.](../../concepts/lifecycle.md)
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).