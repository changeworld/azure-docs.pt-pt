---
title: Obter a propriedade da faturação das subscrições do Azure
description: Saiba como pedir a propriedade da faturação das subscrições do Azure de outros utilizadores.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 10f1052f9acf9bf91c1d7fb0b64a1d3285487cf3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77200732"
---
# <a name="get-billing-ownership-of-azure-subscriptions-from-other-accounts"></a>Obter a propriedade da faturação das subscrições do Azure de outras contas

Talvez queira assumir a propriedade das subscrições do Azure caso o proprietário da faturação atual esteja de saída da sua organização ou se quiser pagar as subscrições através da sua conta de faturação. Assumir a propriedade transfere as responsabilidades de faturação das subscrições para a sua conta.

Este artigo aplica-se a uma conta de faturação de um Contrato de Cliente da Microsoft. [Verificar se tem acesso a um Contrato de Cliente da Microsoft](#check-for-access).

Para pedir a propriedade da faturação, deve ser um **proprietário da secção da fatura** ou um **contribuidor da secção da fatura**. Para saber mais, veja [tarefas das funções da secção da fatura](understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="request-billing-ownership"></a>Pedir a propriedade da faturação

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como um proprietário ou contribuidor da secção da fatura para uma conta de faturação do Contrato de Cliente da Microsoft.

2. Procure **Cost Management + Faturação**.

    ![Captura de ecrã a mostrar a pesquisa da opção cost management + faturação no portal do Azure](./media/mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Na página dos âmbitos de faturação, selecione a conta de faturação, que seria utilizada para pagar pela utilização das subscrições. A conta de faturação deverá ser do tipo **Contrato de Cliente Microsoft**.

    ![Captura de ecrã que mostra a pesquisa no portal para Cost Management + Faturação](./media/mca-request-billing-ownership/list-of-scopes.png)

    > [!NOTE]
    >
    > O portal do Azure memoriza o último âmbito de faturação ao qual acedeu e apresenta-o na próxima vez que aceder à página Gestão de Custos + Faturação. Não verá a página dos âmbitos de faturação se tiver visitado a Gestão de Custos + Faturação anteriormente. Se assim for, verifique se está no [âmbito certo](#check-for-access). Se não estiver, [mude o âmbito](view-all-accounts.md#switch-billing-scope-in-the-azure-portal) para selecionar a conta de faturação de um Contrato de Cliente Microsoft.

4. Selecione **Perfis de faturação** no lado esquerdo.

    ![Captura de ecrã que mostra a seleção dos perfis de faturação](./media/mca-request-billing-ownership/mca-select-profiles.png)     

    > [!Note]
    >
    > Se não vir Perfis de faturação é porque não está no âmbito da faturação correto. Tem de selecionar uma conta de faturação de um Contrato de Cliente Microsoft e, em seguida, selecionar Perfis de faturação. Para saber como alterar os âmbitos, veja [Alterar os âmbitos de faturação no portal do Azure](view-all-accounts.md#switch-billing-scope-in-the-azure-portal).

5. Selecione um **Perfil de faturação** na lista. Depois de assumir a propriedade das subscrições, as utilizações serão faturadas neste perfil de faturação.

6. Selecione **Secções da fatura** no lado esquerdo.

    ![Captura de ecrã que mostra a seleção das secções da fatura](./media/mca-request-billing-ownership/mca-select-invoice-sections.png)   

7. Selecione uma seção da fatura na lista. Depois de assumir a propriedade das subscrições, as utilizações serão atribuídas a esta secção da fatura do perfil de faturação.

8. Selecione **Pedidos de transferência** no canto inferior esquerdo e, em seguida, selecione **Adicionar um novo pedido**.

    ![Captura de ecrã que mostra a seleção dos pedidos de transferência](./media/mca-request-billing-ownership/mca-select-transfer-requests.png)

9. Introduza o endereço de e-mail do utilizador ao qual está a pedir a propriedade de faturação. O utilizador deve ser um Administrador de Conta numa conta de faturação do Programa do Serviço Online da Microsoft ou um proprietário de conta num Contrato Enterprise. Para obter mais informações, veja [Ver as contas de faturação no portal do Azure](view-all-accounts.md). Selecione **Enviar pedido de transferência**.

    ![Captura de ecrã que mostra o envio de um pedido de transferência](./media/mca-request-billing-ownership/mca-send-transfer-requests.png)

10. O utilizador recebe um e-mail com instruções para rever o pedido de transferência.

    ![Captura de ecrã que mostra o e-mail da revisão do pedido de transferência](./media/mca-request-billing-ownership/mca-review-transfer-request-email.png)

11. Para aprovar o pedido de transferência, o utilizador seleciona a ligação no e-mail e segue as instruções.

    ![Captura de ecrã que mostra o e-mail da revisão do pedido de transferência](./media/mca-request-billing-ownership/mca-review-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>Verificar o estado do pedido de transferência

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. Procure **Cost Management + Faturação**.

    ![Captura de ecrã a mostrar a pesquisa da opção cost management + faturação no portal do Azure](./media/mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Na página dos âmbitos de faturação, selecione a conta de faturação para a qual foi enviado o pedido de transferência.

4. Selecione **Perfis de faturação** no lado esquerdo.

    ![Captura de ecrã que mostra a seleção dos perfis de faturação](./media/mca-request-billing-ownership/mca-select-profiles.png)     

5. Selecione o **Perfil de faturação** para o qual foi enviado o pedido de transferência.

6. Selecione **Secções da fatura** no lado esquerdo.

    ![Captura de ecrã que mostra a seleção das secções da fatura](./media/mca-request-billing-ownership/mca-select-invoice-sections.png)   

7. Selecione a secção da fatura na lista para a qual foi enviado o pedido de transferência.

8. Selecione **Pedidos de transferência** no canto inferior esquerdo. A página Pedidos de transferência apresenta as seguintes informações:

    ![Captura de ecrã que mostra a lista de pedidos de transferência](./media/mca-request-billing-ownership/mca-select-transfer-requests-for-status.png)

   |Coluna|Definição|
   |---------|---------|
   |Data do pedido|A data em que o pedido de transferência foi enviado|
   |Destinatário|O endereço de e-mail do utilizador para o qual enviou o pedido para transferir a propriedade de faturação|
   |Data de validade|A data em que o pedido expirou|
   |Estado|O estado do pedido de transferência|

    O pedido de transferência pode ter um dos seguintes estados:

   |Estado|Definição|
   |---------|---------|
   |Em curso|O utilizador não aceitou o pedido de transferência|
   |Em processamento|O utilizador aprovou o pedido de transferência. A faturação das subscrições que o utilizador selecionou está a ser transferida para a secção da fatura|
   |Concluído| A faturação das subscrições que o utilizador selecionou é transferida para a secção da fatura|
   |Concluído com erros|O pedido foi concluído, mas a faturação de algumas subscrições que o utilizador selecionou não pôde ser transferida|
   |Fora do prazo|O utilizador não aceitou o pedido a tempo e expirou|
   |Cancelado|Alguém com acesso ao pedido de transferência cancelou o pedido|
   |Recusado|O utilizador recusou o pedido de transferência|

9. Selecione um pedido de transferência para ver os detalhes. A página detalhes da transferência apresenta as seguintes informações:

    ![Captura de ecrã que mostra a lista das subscrições transferidas](./media/mca-request-billing-ownership/mca-transfer-completed.png)

   |Coluna  |Definição|
   |---------|---------|
   |ID do pedido de transferência|O ID exclusivo do pedido de transferência. Se submeter um pedido de suporte, partilhe o ID com a equipa do suporte do Azure para acelerar o pedido|
   |Transferência pedida a|A data em que o pedido de transferência foi enviado|
   |Transferência pedida por|O endereço de e-mail do utilizador que enviou o pedido de transferência|
   |Pedido de transferência expirou a| A data em que o pedido de transferência expirou|
   |Endereço de e-mail do destinatário|O endereço de e-mail do utilizador para o qual enviou o pedido para transferir a propriedade de faturação|
   |Ligação da transferência enviada ao destinatário|O URL que foi enviado ao utilizador para analisar o pedido de transferência|

## <a name="supported-subscription-types"></a>Tipos de subscrições suportadas

Pode pedir a propriedade da faturação dos tipos de subscrição listados abaixo.

- [Action pack](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [Azure no Licenciamento Open](https://azure.microsoft.com/offers/ms-azr-0111p/)\*
- [Azure Pass Sponsorship](https://azure.microsoft.com/offers/azure-pass/)\*
- [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Avaliação Gratuita](https://azure.microsoft.com/offers/ms-azr-0044p/)\*
- [Pay As You Go](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Pay As You Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Microsoft Azure Plan](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*
- [Oferta Patrocinada do Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0036p/)\*
- [Microsoft Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p/)\*
- [Subscritores do Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/)\*
- [Subscritores do Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/)\*
- [Subscritores do Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)\*
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)\*
- [Subscritores do Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)\*

\* O crédito disponível na subscrição não estará disponível na nova conta após a transferência.

\*\* Só é suportado para subscrições em contas criadas durante a inscrição no site do Azure.


## <a name="additional-information"></a>Informações adicionais

A secção seguinte fornece informações adicionais sobre como transferir subscrições.

### <a name="no-service-downtime"></a>Nenhum tempo de inatividade do serviço

Os serviços do Azure na subscrição continuam em execução sem quaisquer interrupções. Apenas realizamos a transição da relação de faturação para as subscrições do Azure que o utilizador seleciona para transferir.

### <a name="disabled-subscriptions"></a>Subscrições desativadas

As subscrições desativadas não podem ser transferidas. As subscrições devem estar no estado ativo para transferir a propriedade de cobrança.

### <a name="azure-resources-transfer"></a>Transferência de recursos do Azure

Todos os recursos das subscrições, como VMs, discos e transferência de sites.

### <a name="azure-marketplace-products-transfer"></a>Transferência de produtos do Azure Marketplace

Transferência de produtos do Azure Marketplace juntamente com as respetivas subscrições.

### <a name="azure-reservations-transfer"></a>Transferência das Reservas do Azure

As Reservas do Azure não são movidas automaticamente com as subscrições. [Contacte o suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para mover as Reservas.

### <a name="access-to-azure-services"></a>Acesso aos serviços do Azure

O acesso a utilizadores, grupos ou principais de serviço existentes que foi atribuído com o (RBAC [controlo de acesso baseado em funções] do Azure)(../role-based-access-control/overview.md) não é afetado durante a transição.

### <a name="azure-support-plan"></a>Plano de suporte do Azure

O suporte do Azure não é transferido com as subscrições. Se o utilizador transferir todas as subscrições do Azure, peça-lhe para cancelar o plano de suporte.

### <a name="charges-for-transferred-subscription"></a>Custos da subscrição transferida

O proprietário da faturação original das subscrições é responsável por quaisquer custos que foram comunicados até ao ponto em que a transferência foi concluída. A secção da fatura é responsável pelos custos comunicados a partir do momento da transferência e posteriormente. Podem existir custos incorridos antes da transferência, mas que foram relatados mais tarde. Esses custos aparecem na secção da fatura.

### <a name="cancel-a-transfer-request"></a>Cancelar um pedido de transferência

Pode cancelar o pedido de transferência até que o pedido seja aprovado ou recusado. Para cancelar o pedido de transferência, aceda à [página de detalhes da transferência](#check-the-transfer-request-status) e selecione Cancelar na parte inferior da página.

### <a name="software-as-a-service-saas-transfer"></a>Transferência de Software como Serviço (SaaS)

Os produtos SaaS não são transferidos com as subscrições. Peça ao utilizador para [Contactar o suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para transferir a propriedade da faturação dos produtos SaaS. Juntamente com a propriedade da faturação, o utilizador também pode transferir a propriedade do recurso. A propriedade do recurso permite executar operações de gestão, como eliminar e visualizar os detalhes do produto. O utilizador deve ser um proprietário de recursos no produto SaaS para transferir a propriedade dos recursos.

## <a name="check-for-access"></a>Verificar o acesso
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte

Se precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.

## <a name="next-steps"></a>Passos seguintes

- A propriedade da faturação das subscrições do Azure é transferida para a secção da fatura. Controle os custos destas subscrições no [portal do Azure.](https://portal.azure.com)
- Conceda permissões a outras pessoas para verem e gerirem a faturação destas subscrições. Para obter mais informações, veja [Funções e tarefas da secção da fatura](understand-mca-roles.md#invoice-section-roles-and-tasks).
