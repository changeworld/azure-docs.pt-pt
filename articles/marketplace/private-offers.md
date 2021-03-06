---
title: Ofertas privadas / Mercado Azure
description: Ofertas privadas no Azure Marketplace para editores de aplicações e serviços.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/1/2018
ms.author: dsindona
ms.openlocfilehash: 67aba077304117ad357d4e004ce7bdb25ac58352
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285049"
---
# <a name="private-offers"></a>Ofertas privadas

As ofertas privadas no [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/) permitem que as editoras criem SKUs que só são visíveis para clientes direcionados.

## <a name="unlock-enterprise-deals-with-private-offers"></a>Desbloquear negócios empresariais com ofertas privadas

Os clientes empresariais usam cada vez mais mercados online para encontrar, experimentar e comprar soluções em nuvem. Agora, com ofertas privadas, os editores podem usar o marketplace para partilhar soluções personalizadas privadas com clientes direcionados com capacidades que as empresas exigem:

- *Os preços negociados* permitem que os editores alarguem os descontos e os preços fora da lista de ofertas publicamente disponíveis.
- *Os termos e condições privados* permitem aos editores adaptar os termos e condições a um cliente específico.
- *Configurações especializadas* permitem que os editores adaptem as suas Máquinas Virtuais, Aplicações Azure e Apps SaaS às necessidades de um cliente individual. Esta opção também permite que os editores forneçam acesso de pré-visualização a novas funcionalidades do produto, antes de lançarem mais amplamente a todos os clientes.

As ofertas privadas permitem que os editores aproveitem a escala e a disponibilidade global de um mercado público, com a flexibilidade e controlo necessários para negociar e entregar ofertas e configurações personalizadas. Juntos, estas características abrem a porta para uma forte adoção empresarial de mercados em nuvem.  As empresas podem agora comprar e vender da forma que esperam e exigem.

As ofertas privadas estão agora disponíveis para Máquina Virtual, Aplicação Azure (implementada como modelos de solução ou aplicações geridas) e ofertas de Apps SaaS. Tal como as ofertas públicas, as ofertas privadas podem ser criadas e geridas através do Portal do [Parceiro cloud.](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md)  Os clientes podem ser concedidos ou revogados o acesso a ofertas privadas em minutos.

## <a name="creating-private-offers-using-skus-and-plans"></a>Criação de ofertas privadas utilizando SKUs e planos

Para *ofertas novas ou existentes com SKUs ou planos públicos,* os editores podem facilmente criar novas variações privadas, criando novas SKUs ou planos e marcando-os como privados.  [As SKUs privadas](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) e os planos são componentes de uma oferta e são apenas visíveis e adquiridos pelos clientes visados. As SKUs privadas e os planos podem reutilizar as imagens base e/ou oferecer metadados já publicados para um SKU público ou plano. Esta opção permite que os editores criem múltiplas variações privadas de uma oferta pública sem terem de publicar várias versões da mesma imagem base e oferecer metadados. Para a aplicação Virtual Machine e Azure apenas oferece, quando um SKU privado partilha uma imagem base com um SKU público, quaisquer alterações na imagem base da oferta se espalharão por todas as SKUs públicas e privadas usando essa imagem base.

Para *novas ofertas que incluam apenas SKUs ou planos privados,* os editores podem criar as suas ofertas como qualquer outra oferta, e depois marcar as SKUs ou planos como privados. As ofertas que apenas possuem SKUs ou planos privados não serão detetáveis ou acessíveis via [Azure Marketplace](https://azuremarketplace.microsoft.com) ou do [portal Azure](https://azure.microsoft.com/features/azure-portal/) por clientes que não estejam associados à oferta.

## <a name="targeting-customers-with-private-offers"></a>Direcionar clientes com ofertas privadas
Para ofertas privadas novas e existentes, os editores podem direcionar os clientes usando identificadores de subscrição. Os editores que utilizam uma oferta de Aplicação Virtual Machine ou Azure podem limitar a disponibilidade de um SKU privado para um ID de subscrição azure individual ou carregar um CSV de até 20.000 IDs de subscrição Azure. Ao utilizar uma oferta privada da SaaS App, os editores podem associar um ID de subscrição Azure ou um ID de inquilino para limitar a disponibilidade de um plano privado, utilizando a abordagem de upload manual ou CSV.

Uma vez certificada e publicada uma oferta, os clientes podem ser atualizados ou removidos do SKU ou Plano em poucos minutos utilizando a funcionalidade Sync Private Subscriptions. Esta capacidade permite aos editores atualizar em rápida e facilmente a lista de clientes aos quais o SKU ou plano privado é apresentado sem recertificar ou reeditar a oferta.

## <a name="deploying-private-offers"></a>Implementação de ofertas privadas

As ofertas privadas só são detetáveis através do [portal Azure](https://azure.microsoft.com/features/azure-portal/) e não são apresentadas através do [Azure Marketplace.](https://azuremarketplace.microsoft.com) Uma vez iniciados no portal Azure, os clientes podem selecionar o elemento de navegação Marketplace para aceder às suas ofertas privadas. As Ofertas Privadas também aparecerão nos resultados da pesquisa e podem ser implementadas através da linha de comando e modelos do Gestor de Recursos Azure, como quaisquer outras ofertas.

![[Ofertas privadas]](./media/marketplace-publishers-guide/private-offer.png)

As ofertas privadas também aparecerão nos resultados da pesquisa. Cuidado com o distintivo "Privado".

>[!Note]
>As ofertas privadas não são suportadas com subscrições estabelecidas através de um revendedor do programa Cloud Solution Provider (CSP).

## <a name="next-steps"></a>Passos seguintes

Para começar a utilizar ofertas privadas, siga os passos no guia [Private SKUs and Plans.](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md)
