---
title: Descrição Geral do Azure Policy
description: O Azure Policy é um serviço no Azure utilizado para criar, atribuir e gerir definições de política no seu ambiente do Azure.
ms.date: 11/25/2019
ms.topic: overview
ms.custom: fasttrack-edit
ms.openlocfilehash: e886f37a8d7f1395b5c831e81e600ecc6e2dd20f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241529"
---
# <a name="what-is-azure-policy"></a>O que é o Azure Policy?

A governação valida que a sua organização pode atingir os seus objetivos através de uma utilização eficaz e eficiente das TI. Satisfaz esta necessidade criando clareza entre os objetivos de negócio e os projetos de TI.

A sua empresa tem um número significativo de problemas de TI que parece nunca ficarem resolvidos? Uma boa governação de TI envolve o planeamento das suas iniciativas e a definição de prioridades num nível estratégico para ajudar a gerir e evitar problemas. Esta necessidade estratégica é onde entra a Política Azure.

O Azure Policy é um serviço do Azure que utiliza para criar, atribuir e gerir políticas. Estas políticas impõem diferentes regras e efeitos aos recursos, de forma a que esses recursos se mantenham em conformidade com as normas empresariais e os contratos de nível de serviço. O Azure Policy responde a esta necessidade ao avaliar os seus recursos para detetar os que não estão em conformidade com as políticas atribuídas. Todos os dados armazenados pela Azure Policy são encriptados em repouso.

Por exemplo, pode ter uma política para permitir apenas um determinado tamanho de SKU de máquinas virtuais no seu ambiente. Após esta política ser implementada, os recursos novos e existentes são sujeitos a uma avaliação de conformidade. Com o tipo de política adequado, os recursos existentes podem ser colocados em conformidade. Mais tarde nesta documentação, vamos analisar mais detalhes sobre como criar e implementar políticas com a Política Azure.

> [!IMPORTANT]
> A avaliação de conformidade do Azure Policy é agora fornecida para todas as atribuições, independentemente do escalão de preço. Se a suas atribuições não mostrarem os dados de conformidade, certifique-se de que a subscrição está registada com o fornecedor de recursos Microsoft.PolicyInsights.

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-is-it-different-from-rbac"></a>Em que medida é diferente do RBAC?

Existem algumas diferenças fundamentais entre a Política Azure e o controlo de acesso baseado em papéis (RBAC). O RBAC concentra-se nas ações do utilizador em âmbitos diferentes. Pode ser adicionado ao papel de contribuinte de um grupo de recursos, permitindo-lhe fazer alterações nesse grupo de recursos. A Política Azure centra-se nas propriedades dos recursos durante a implantação e nos recursos já existentes. O Azure Policy controla propriedades como os tipos de localizações de recursos. Ao contrário do RBAC, a Política Azure é um sistema de negação predefinido e explícito.

### <a name="rbac-permissions-in-azure-policy"></a>Permissões RBAC no Azure Policy

O Azure Policy tem várias permissões, conhecidas como operações, em dois Fornecedores de Recursos:

- [Microsoft.Authorization](../../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft.PolicyInsights](../../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

Muitas Funções incorporadas concedem permissão aos recursos do Azure Policy. O papel **de Contribuinte da Política de Recursos** inclui a maioria das operações da Política Azure. **O dono** tem todos os direitos. Tanto **o Colaborador** como o **Reader** podem utilizar todas as operações da Política Azure, mas o **Contribuinte** também pode desencadear a reparação.

Se nenhuma das Funções incorporadas tiver as permissões exigidas, crie uma [função personalizada](../../role-based-access-control/custom-roles.md).

## <a name="policy-definition"></a>Definição de política

O percurso de criar e implementar uma política no Azure Policy começa pela criação de uma definição de política. Todas as definições políticas têm condições em que é aplicada. E, tem um efeito definido que ocorre se as condições forem cumpridas.

Na Política Azure, oferecemos várias políticas incorporadas que estão disponíveis por defeito. Por exemplo:

- Conta de **Armazenamento Permitida SKUs**: Determina se uma conta de armazenamento que está a ser implantada está dentro de um conjunto de tamanhos SKU. O seu efeito é negar todas as contas de armazenamento que não aderirem ao conjunto de tamanhos SKU definidos.
- **Tipo de recurso permitido:** Define os tipos de recursos que pode implementar. O seu efeito é negar todos os recursos que não fazem parte desta lista definida.
- **Locais Permitidos**: Restringe as localizações disponíveis para novos recursos. O efeito é utilizado para impor os requisitos de geoconformidade.
- **Máquina virtual Permitida SKUs**: Especifica um conjunto de SKUs da máquina virtual que pode implantar.
- **Adicione uma etiqueta aos recursos**: Aplica uma etiqueta requerida e o seu valor predefinido se não for especificado pelo pedido de implantação.
- **Impor etiqueta e o seu valor**: Aplica uma etiqueta necessária e o seu valor a um recurso.
- Tipos de **recursos não permitidos**: Impede a implantação de uma lista de tipos de recursos.

Para implementar estas definições políticas (ambas as definições incorporadas e personalizadas), terá de as atribuir. Pode atribuir qualquer uma destas políticas através do portal do Azure, do PowerShell ou da CLI do Azure.

A avaliação política acontece com várias ações diferentes, tais como atribuição de políticas ou atualizações políticas. Para obter uma lista completa, consulte os gatilhos de [avaliação política](./how-to/get-compliance-data.md#evaluation-triggers).

Para saber mais sobre as estruturas de definições de política, veja [Estrutura de Definição de Política](./concepts/definition-structure.md).

## <a name="policy-assignment"></a>Atribuição de política

Uma atribuição de política é uma definição de política que foi atribuída para ter lugar num âmbito específico. Este âmbito pode ir de um grupo de [gestão](../management-groups/overview.md) a um recurso individual. O *âmbito* do termo refere-se a todos os recursos, grupos de recursos, subscrições ou grupos de gestão a que a definição de política é atribuída. As atribuições de política são herdadas por todos os recursos subordinados. Este desenho significa que uma política aplicada a um grupo de recursos também é aplicada aos recursos desse grupo de recursos. No entanto, pode excluir um subâmbito da atribuição de política.

Por exemplo, no âmbito da subscrição, pode atribuir uma política que impede a criação de recursos de rede. Poderia excluir um grupo de recursos nessa subscrição que se destina a infraestruturas de networking. Em seguida, concede acesso a este grupo de recursos de networking aos utilizadores em que confia na criação de recursos de networking.

Noutro exemplo, é possível atribuir uma política de listas de tipo de recursos ao nível do grupo de gestão. Em seguida, atribua uma política mais permissiva (permitindo mais tipos de recursos) num grupo de gestão subordinado ou mesmo diretamente em subscrições. No entanto, este exemplo não funciona porque a política é um sistema de negação explícita. Em vez disso, tem de excluir o grupo de gestão do subordinado ou uma subscrição da atribuição de política ao nível do grupo de gestão. Em seguida, atribua a política mais permissiva no grupo de gestão subordinado ou ao nível da subscrição. Se alguma política resultar na recusa de um recurso, então a única forma de permitir o recurso é modificar a política de negação.

Para obter mais informações sobre a configuração de definições e atribuições de política através do portal, veja [Criar uma atribuição de política para identificar recursos não conformes no ambiente do Azure](assign-policy-portal.md). Também estão disponíveis passos para o [PowerShell](assign-policy-powershell.md) e a [CLI do Azure](assign-policy-azurecli.md).

## <a name="policy-parameters"></a>Parâmetros de política

Os parâmetros de política ajudam a simplificar a gestão de políticas ao reduzir o número de definições de política que tem de criar. Pode definir os parâmetros durante a criação de uma definição de política para torná-la mais genérica. Em seguida, pode reutilizar essa definição de política para diferentes cenários. Pode fazê-lo ao transmitir diferentes valores quando atribui a definição de política. Por exemplo, pode especificar um conjunto de localizações para uma subscrição.

Os parâmetros são definidos na criação de uma definição de política. Quando um parâmetro é definido, é fornecido um nome e opcionalmente um valor para o mesmo. Por exemplo, pode definir um parâmetro para uma política intitulada *localização*. Em seguida, pode atribuir-lhe valores diferentes, tais como *EastUS* ou *WestUS* quando atribui uma política.

Para obter mais informações sobre parâmetros de política, consulte [A estrutura definição - Parâmetros](./concepts/definition-structure.md#parameters).

## <a name="initiative-definition"></a>Definição de iniciativa

Uma definição de iniciativa é uma coleção de definições de política adaptadas para alcançar um objetivo único abrangente. As definições de iniciativa simplificam a gestão e a atribuição de definições de política. Simplificam através do agrupamento de um conjunto de políticas num único item. Por exemplo, pode criar uma iniciativa intitulada **Ativar Monitorização no Centro de Segurança do Azure**, com o objetivo de monitorizar todas as recomendações de segurança disponíveis no Centro de Segurança do Azure.

> [!NOTE]
> O SDK, como o Azure CLI e o Azure PowerShell, utiliza propriedades e parâmetros nomeados **PolicySet** para se referir a iniciativas.

Ao abrigo desta iniciativa, terá definições de política como:

- **Monitorizar a Base de Dados SQL não encriptada no Centro de Segurança** – Para monitorização de servidores e bases de dados SQL não encriptados.
- **Monitorize as vulnerabilidades do OS no Security Center** – Para monitorizar servidores que não satisfaçam a linha de base configurada.
- **Monitorizar a Proteção de Ponto Final em falta no Centro de Segurança** – Para monitorização de servidores sem um agente de proteção de ponto final instalado.

## <a name="initiative-assignment"></a>Atribuição de iniciativa

Tal como uma atribuição de política, uma atribuição de iniciativa é uma definição de iniciativa atribuída a um âmbito específico. As atribuições de iniciativa reduzem a necessidade de efetuar várias definições de iniciativa para cada âmbito. Este âmbito também pode ir de um grupo de gestão a um recurso individual.

Cada iniciativa é atribuída a diferentes âmbitos. Uma iniciativa pode ser atribuída tanto à **subscrição A** como à **subscriçãoB**.

## <a name="initiative-parameters"></a>Parâmetros de iniciativa

Tal como os parâmetros de política, os parâmetros de iniciativa ajudam a simplificar a gestão de iniciativas ao reduzir a redundância. Os parâmetros de iniciativa são parâmetros que estão a ser utilizados pelas definições políticas no âmbito da iniciativa.

Por exemplo, considere um cenário em que tem uma definição de iniciativa - **initiativeC**, com as definições de política **policyA** e **policyB**, em que cada uma espera um tipo diferente de parâmetro:

| Política | Nome do parâmetro |Tipo de parâmetro  |Nota |
|---|---|---|---|
| policyA | allowedLocations | array  |Este parâmetro espera uma lista de cadeias para um valor, uma vez que o tipo de parâmetro foi definido como uma matriz |
| policyB | allowedSingleLocation |string |Este parâmetro espera uma palavra para um valor, uma vez que o tipo de parâmetro foi definido como uma cadeia |

Neste cenário, quando define os parâmetros da iniciativa para **initiativeC**, tem três opções:

- Utilizar os parâmetros das definições de política nesta iniciativa: neste exemplo, *allowedLocations* e *allowedSingleLocation* tornam-se parâmetros de iniciativa para **initiativeC**.
- Indicar os valores para os parâmetros das definições de política nesta definição de iniciativa. Neste exemplo, pode fornecer uma lista de localizações para **o parâmetro da PolicyA – permitidaaALocalização** e **parâmetro de políticaB – permitidaAÚnicaLocalização.** Também pode fornecer valores quando atribui esta iniciativa.
- Fornecer uma lista das opções de *valor* que podem ser utilizadas quando atribui esta iniciativa. Quando atribui esta iniciativa, os parâmetros herdados das definições de política na iniciativa apenas podem ter valores desta lista fornecida.

Ao criar opções de valor numa definição de iniciativa, não é capaz de inserir um valor diferente durante a atribuição de iniciativa porque não faz parte da lista.

## <a name="maximum-count-of-azure-policy-objects"></a>Contagem máxima de objetos da Política Azure

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="recommendations-for-managing-policies"></a>Recomendações para a gestão de políticas

Aqui estão algumas dicas e dicas a ter em mente:

- Comece com um efeito de auditoria em vez de um efeito de negação para acompanhar o impacto da sua definição política nos recursos do seu ambiente. Se já tiver scripts para escalar automaticamente as suas aplicações, definir um efeito de negação pode dificultar tais tarefas de automação já em vigor.

- Considere hierarquias organizacionais ao criar definições e atribuições. Recomendamos a criação de definições em níveis mais elevados, como o grupo de gestão ou o nível de subscrição. Então, crie a tarefa ao nível da próxima criança. Se criar uma definição num grupo de gestão, a atribuição pode ser consultada a um grupo de subscrição ou recursos dentro desse grupo de gestão.

- Recomendamos a criação e a atribuição de definições de iniciativa, mesmo para uma definição política única.
  Por exemplo, tem política de definição de *políticasDefA* e criá-la no âmbito da iniciativa de definição de *iniciativaDefC*. Se criar outra definição política mais tarde para *a políticaDefB* com objetivos semelhantes à *políticaDefA,* pode adicioná-la sob *iniciativaDefC* e rastreá-las em conjunto.

- Uma vez criado um trabalho de iniciativa, as definições políticas adicionadas à iniciativa também fazem parte dessas iniciativas.

- Quando uma atribuição de iniciativa é avaliada, todas as políticas dentro da iniciativa também são avaliadas.
  Se precisa avaliar uma política individualmente, é melhor não incluí-la numa iniciativa.

## <a name="video-overview"></a>Descrição geral em vídeo

A seguinte descrição geral do Azure Policy é do Build 2018. Para slides ou download de vídeo, visite [Governe o seu ambiente Azure através](https://channel9.msdn.com/events/Build/2018/THR2030) da Política Azure no Canal 9.

> [!VIDEO https://www.youtube.com/embed/dxMaYF2GB7o]

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma ideia geral do Azure Policy e de alguns dos principais conceitos, seguem-se os passos sugeridos seguintes:

- [Atribuir uma definição de política utilizando o portal](./assign-policy-portal.md).
- [Designar uma definição de política utilizando o Azure CLI](./assign-policy-azurecli.md).
- [Atribuir uma definição](./assign-policy-powershell.md)de política utilizando powerShell .
