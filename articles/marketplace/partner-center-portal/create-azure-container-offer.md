---
title: Criar uma oferta de contentores Azure no Partner Center - Azure Marketplace
description: Este artigo descreve como criar e publicar uma oferta de contentores para o Azure Marketplace.
author: mingshen
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: b96fe12ccc0292bb5f689fbecabd53d2af54846e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266321"
---
# <a name="create-an-azure-container-offer"></a>Criar uma oferta de contentores Azure

> [!IMPORTANT]
> Estamos a mover a gestão das suas ofertas de contentores Azure do Cloud Partner Portal para partner Center. Até que as suas ofertas sejam migradas, siga as instruções em [Recipientes](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-containers-offer) para o Portal do Parceiro cloud para gerir as suas ofertas.

Este artigo descreve como criar e publicar uma oferta de contentores para o Azure Marketplace. Antes de começar, [Crie uma conta de Marketplace Comercial no Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account).

## <a name="create-a-new-offer"></a>Criar uma nova oferta

1. Faça login no [Partner Center](https://partner.microsoft.com/dashboard/home), e depois a partir do menu superior, selecione **Dashboard**.
2. No menu esquerdo, selecione **Mercado Comercial**, em **seguida, visão geral**.
3. Na página **'Visão Geral',** selecione **+ Nova oferta,** em **seguida, Recipiente Azure**. A caixa de diálogo **da Nova Oferta** aparece.

:::image type="content" source="media/azure-create-container-offer-images/azure-create-new-container.png" alt-text="Ilustrou a página de visão geral no Partner Center. O botão de oferta Nova e oferta de serviço de Consultoria estão em destaque.":::

> [!TIP]
> Depois de publicada uma oferta, as edificações feitas no Partner Center só aparecem nas montras depois de reeditarem a oferta. Certifique-se de que republique sempre depois de fazer alterações.

### <a name="offer-id-and-alias"></a>Oferecer ID e pseudónimo

Introduza um **ID de oferta**. Este é um identificador único para cada oferta na sua conta.

- Este ID pode ser visto pelos clientes no endereço web para a oferta de mercado e modelos do Gestor de Recursos Azure, se aplicável.
- Utilize apenas letras minúsculas e números. Pode incluir hífenes e sublinhados, mas sem espaços, e está limitado a 50 caracteres. Por exemplo, se introduzir o **test-offer-1,** `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`o endereço web da oferta será .
- O ID da Oferta não pode ser alterado depois de selecionar **Criar**.

**Insira um** **pseudónimo da Oferta.** Este é o nome usado para se referir à oferta no Partner Center.

- Este nome não é usado no mercado e é diferente do nome da oferta e outros valores mostrados aos clientes.
- Isto não pode ser alterado depois de selecionar **Criar**.

Selecione **Criar** antes de continuar.

## <a name="offer-overview"></a>Visão geral da oferta

A página geral da **Oferta** mostra uma representação visual dos passos necessários para publicar esta oferta (completa e próxima) e quanto tempo cada passo deve demorar a ser concluído.

Esta página mostra diferentes links com base no estado atual da oferta. Por exemplo:

- Se a oferta for um rascunho - [Eliminar a oferta](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer) de rascunho
- Se a oferta estiver ao vivo - [Pare de vender a oferta](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Se a oferta estiver em pré-visualização - [Go-live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Se ainda não tiver concluído a inscrição da editora - [Cancele a publicação](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>Configuração de oferta

Siga estes passos para configurar a sua oferta.

### <a name="connect-lead-management--optional"></a>Conectar gestão de chumbo - opcional

Ao publicar a sua oferta no mercado com o Partner Center, pode ligá-la ao seu sistema de Gestão de Relacionamento com o Cliente (CRM). Isto permite-lhe receber informações de contacto do cliente assim que alguém expressa interesse ou utiliza o seu produto.

1. **Selecione um destino principal onde deseja que enviemos pistas de cliente**. Partner Center suporta os seguintes sistemas CRM:

- [Dinâmica 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) para envolvimento com o cliente
- [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
- [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

> [!NOTE]
> Se o seu sistema CRM não estiver listado acima, utilize [o Azure Table](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) ou [o Https Endpoint](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) para armazenar os dados de chumbo do cliente e, em seguida, exporte os dados para o seu sistema CRM.

2. Ligue a sua oferta ao destino principal ao publicar no Partner Center.
3. Confirme que a ligação ao destino principal está configurada corretamente. Depois de publicá-lo no Partner Center, validamos a ligação e enviaremos-lhe um teste. Enquanto faz a pré-visualização da oferta antes de ser transmitida ao vivo, também pode testar a sua ligação de chumbo tentando adquirir a oferta no ambiente de pré-visualização.
4. Certifique-se de que a ligação ao destino principal permanece atualizada para que não perca nenhuma pista.

Aqui estão alguns recursos adicionais de gestão de chumbo:

- [Visão geral da gestão de chumbo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [FAQs de gestão de oportunidades potenciais](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Erros de configuração comuns de oportunidades potenciais](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Visão geral da gestão de chumbo](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (Certifique-se de que o seu bloqueador pop-up está desligado)

Selecione **Guardar rascunho** antes de continuar na secção seguinte, Propriedades.

### <a name="properties"></a>Propriedades

Esta página permite definir as categorias utilizadas para agrupar a sua oferta no mercado e os contratos legais que suportam a sua oferta.

#### <a name="category"></a>Categoria

Selecione um mínimo de uma e um máximo para cinco categorias. Estas categorias são usadas para colocar a sua oferta nas áreas de pesquisa de mercado apropriadas, e são mostradas na sua página de detalhes de oferta. Na descrição da oferta, explique como a sua oferta suporta estas categorias. Os recipientes aparecem em **contentores** e, em seguida, na categoria Imagens do **Recipiente.**

#### <a name="legal"></a>Legal

Deve fornecer termos e condições para a oferta. Existem duas opções:

- Utilize o Contrato Padrão para o mercado comercial da Microsoft.
- Forneça os seus próprios termos e condições.

##### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Contrato padrão para o mercado comercial da Microsoft

Oferecemos um modelo de Contrato Padrão para ajudar a facilitar transações no mercado comercial. Pode optar por oferecer a sua solução ao abrigo do Contrato Padrão, que os clientes só precisam de verificar e aceitar uma vez. Esta é uma boa opção se não quiser criar termos e condições personalizados.

Para saber mais sobre o Contrato Padrão, consulte [o Standard Contract para o mercado comercial da Microsoft.](https://docs.microsoft.com/azure/marketplace/standard-contract) Também pode baixar o [Standard Contract](https://go.microsoft.com/fwlink/?linkid=2041178) PDF (certifique-se de que o seu bloqueador pop-up está desligado).

Para utilizar o Contrato Padrão, selecione o Contrato Padrão para a caixa de verificação de **mercado comercial da Microsoft** e, em seguida, clique em **Aceitar**.

> [!NOTE]
> Depois de publicar uma oferta utilizando o contrato Standard para o mercado comercial da Microsoft, não pode utilizar os seus próprios termos e condições personalizados. Ou ofereça a sua solução ao abrigo do Contrato Padrão ou nos seus próprios termos e condições.

:::image type="content" source="media/azure-create-container-offer-images/azure-create-1-standard-contract.png" alt-text="Ilustra o Contrato Padrão de Utilização para a caixa de verificação de mercado comercial da Microsoft.":::

##### <a name="your-own-terms-and-conditions"></a>Seus próprios termos e condições

Para fornecer os seus próprios termos e condições personalizados, insira-os na caixa **de Condições e Condições.** Pode introduzir uma quantidade ilimitada de caracteres de texto nesta caixa. Os clientes devem aceitar estes termos antes de poderem experimentar a sua oferta.

Selecione **Guardar rascunho** antes de continuar na secção seguinte, Oferta de listagem.

## <a name="offer-listing"></a>Oferta listagem

Esta página permite definir os detalhes da oferta que são exibidos no mercado comercial. Isto inclui o nome da oferta, descrição e imagens.

> [!NOTE]
> Os detalhes da oferta não são necessários para estar em inglês se a descrição da oferta começar com a frase"Esta aplicação está disponível apenas em [língua não inglesa]." Também é normal fornecer um Link Útil para oferecer conteúdo num idioma diferente do usado nos detalhes da listagem de oferta.

### <a name="name"></a>Nome

O nome que insere aqui mostra como o título da sua oferta. Este campo está pré-preenchido com o texto que inseriu na caixa **de pseudónimos Offer** quando criou a oferta. Pode alterar este nome posteriormente.

O nome:

- Pode ser marcado por marca (e pode incluir símbolos de marca e direitos de autor).
- Não deve ter mais de 50 caracteres.
- Não posso incluir emojis.

### <a name="search-results-summary"></a>Resumo dos resultados da pesquisa

Uma breve descrição da sua oferta. Isto pode ter até 100 caracteres de comprimento e é usado em resultados de pesquisa de mercado.

### <a name="long-summary"></a>Resumo longo

Uma descrição mais detalhada da sua oferta. Isto pode ter até 256 caracteres de comprimento e é usado em resultados de pesquisa de mercado.

### <a name="description"></a>Descrição

Forneça uma descrição mais longa da sua oferta, até 3.000 caracteres. Isto é apresentado aos clientes na visão geral da listagem do mercado.

Inclua um ou mais dos seguintes na sua descrição:

- O valor e os benefícios-chave que a sua oferta proporciona
- Categoria ou associações industriais, ou ambos
- Oportunidades de compra na aplicação
- Quaisquer divulgações necessárias

Aqui ficam algumas dicas para escrever a sua descrição:

- Descreva claramente o valor da sua oferta nas primeiras frases da sua descrição. Incluir os seguintes itens:
  - Descrição da oferta.
  - O tipo de utilizador que beneficia da oferta
  - As necessidades do cliente ou emite os endereços da oferta.
- Lembre-se que as primeiras frases podem ser exibidas nos resultados da pesquisa.
- Não confie em funcionalidades e funcionalidades para vender o seu produto. Em vez disso, concentre-se no valor que a sua oferta proporciona.
- Tente utilizar vocabulário específico da indústria ou formulação baseada em benefícios.

Para tornar a sua **descrição** mais envolvente, use o rico editor de texto, para formatar a sua descrição. com numeração, balas, arrojado, itálico e travessos para tornar a sua descrição mais legível.

:::image type="content" source="media/text-editor2.png" alt-text="Ilustra o rico editor de texto." border="false" :::

- Utilize esta entrega para aplicar um estilo de parágrafo ao texto.

    :::image type="content" source="media/text-editor3.png" alt-text="Ilustra o controlo do estilo de texto no rico editor de texto." border="false":::

- Utilize estes ícones para aplicar numeração ou balas por texto.

     :::image type="content" source="media/text-editor4.png" alt-text="Ilustra os controlos da lista de números e balas no rico editor de texto." border="false":::

- Utilize estes ícones para adicionar ou remover o entalhe de ou para o texto.

    :::image type="content" source="media/text-editor5.png" alt-text="Ilustra os controlos de entalhes no rico editor de texto." border="false":::

#### <a name="privacy-policy-link"></a>Ligação política de privacidade

Insira o endereço web da política de privacidade da sua organização. É responsável por garantir que a sua oferta está em conformidade com as leis e regulamentos de privacidade. Também é responsável por publicar uma política de privacidade válida no seu site.

#### <a name="useful-links"></a>Ligações úteis

Forneça documentos online suplementares sobre a sua oferta. Pode adicionar 25 links. Para adicionar um link, selecione **+ Adicione um link** e, em seguida, complete os seguintes campos:

- **Título** – Os clientes verão isso na página de detalhes da sua oferta.
- **Link (URL)** – Introduza um link para os clientes verem o seu documento online. A ligação deve começar com http:// ou https://.

### <a name="contact-information"></a>Informações de Contacto

Deve fornecer o nome, e-mail e número de telefone para um contacto de **suporte** e um **contacto de Engenharia**. Esta informação não é mostrada aos clientes, mas está disponível para a Microsoft. Também pode ser fornecido aos parceiros do Cloud Solution Provider (CSP).

- Contacto de suporte (obrigatório): Para questões de apoio geral.
- Contacto de engenharia (obrigatório): Para questões técnicas e questões de certificação.
- Contacto do Programa CSP (opcional): Para questões de revendedorrelacionados com o programa CSP.

Na secção de contacto de **suporte,** forneça o **site de Suporte** onde os parceiros possam encontrar apoio para a sua oferta com base no facto de a oferta estar disponível no Global Azure, Governo Azure, ou ambos.

Na secção de contacto do **Programa CSP,** forneça o link ( **CSP Program Marketing Materials)** onde os parceiros do CSP podem encontrar materiais de marketing para a sua oferta.

#### <a name="additional-marketplace-listing-resources"></a>Recursos adicionais de listagem de mercado

Para saber mais sobre a criação de anúncios de oferta, consulte [oferta de listas de boas práticas](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

### <a name="marketplace-images"></a>Imagens do mercado

Forneça logotipos e imagens para usar com a sua oferta. Todas as imagens devem estar em formato PNG. Imagens desfocadas serão rejeitadas.

#### <a name="store-logos"></a>Logotipos de loja

 Forneça ficheiros PNG do logótipo da sua oferta em cada um dos seguintes quatro tamanhos de pixel:

- **Pequeno** (48 x 48)
- **Médio** (90 X 90)
- **Grande** (216 x 216)
- **Ampla** (255 X 115)

Todos os quatro logótipos são necessários e são usados em diferentes lugares na listagem do mercado.

#### <a name="screenshots-optional"></a>Screenshots (opcional)

Adicione cinco imagens que mostram como a sua oferta funciona. Cada um deve ter 1280 x 720 pixels em tamanho e em formato PNG.

#### <a name="videos-optional"></a>Vídeos (opcional)

Adicione cinco vídeos que demonstram a sua oferta. Introduza o nome do vídeo, o seu endereço web e uma imagem PNG miniatura do vídeo a 1280 x 720 pixels de tamanho.

#### <a name="offer-examples"></a>Exemplos de oferta

Os seguintes exemplos mostram como os campos de listagem de oferta aparecem em diferentes lugares da oferta.

Isto mostra a página **de listagem de ofertas** no Azure Marketplace:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-6-offer-listing-mkt-plc.png" alt-text="Ilustra a página de listagem de oferta seleções no Azure Marketplace." :::

Isto mostra resultados de pesquisa no Azure Marketplace:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-7-search-results-mkt-plc.png" alt-text="Ilustra os resultados da pesquisa no Azure Marketplace.":::

Isto mostra a página **de listagem de oferta** no portal Azure:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-8-offer-listing-portal.png" alt-text="Ilustra a página de listagem de oferta no portal Azure.":::

Isto mostra resultados de pesquisa no portal Azure:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-9-search-results-portal.png" alt-text="Ilustra os resultados da pesquisa no portal Azure.":::

## <a name="preview"></a>Pré-visualização

No separador Preview, pode escolher um Público de **Pré-visualização** limitado para validar a sua oferta antes de publicá-la ao vivo.

> [!IMPORTANT]
> Depois de ver a sua oferta em **Preview,** tem de selecionar **ir ao vivo** para publicar a sua oferta ao público.

Especifique o seu público de pré-visualização utilizando GUIDs de assinatura Azure, juntamente com uma descrição opcional para cada um. Nenhum destes campos pode ser visto pelos clientes.

> [!NOTE]
> Pode encontrar o seu ID de subscrição Azure na página de Subscrições no portal Azure.

Adicione pelo menos um ID de subscrição Azure, individualmente (até 10) ou carregando um ficheiro CSV (até 100). Ao adicionar estes IDs de subscrição, determina quem pode pré-visualizar a sua oferta antes de ser publicado ao vivo. Se a sua oferta já estiver ao vivo, pode escolher um público de pré-visualização para testar alterações ou atualizações da sua oferta.

> [!NOTE]
> O público de pré-visualização difere de um público privado. Um público **pré-visualizado** pode ver e confirmar todos os planos de oferta antes de serem ao vivo no mercado, incluindo aqueles que serão publicados apenas para um público **privado** (definido no separador Disponibilidade).

Selecione **guardar rascunho** antes de continuar.

### <a name="plan-overview"></a>Visão geral do plano

Este separador permite-lhe fornecer diferentes opções de plano dentro da mesma oferta. Estes planos eram anteriormente referidos como SKUs, ou unidades de armazenamento de stocks. Os planos podem diferir em termos do que as nuvens estão disponíveis, como nuvens globais, nuvens do Governo e a imagem referenciada pelo plano. Para listar a sua oferta no mercado comercial, deve configurar pelo menos um plano.

Depois de criar os seus planos, o separador de **visão geral** do Plano mostra:

- Nomes de planos
- Modelo preços
- Disponibilidade em nuvem (Global ou Governo)
- Estado editorial atual
- Quaisquer ações disponíveis

As ações disponíveis na visão geral do Plano variam consoante o estado atual do seu plano. Os relatórios incluem:

- **Apagar o projeto** – Se o estado do plano for um Projeto.
- **Stop sell plan** – Se o estado do plano for publicado ao vivo.

#### <a name="create-new-plan"></a>Criar novo plano

Selecione **Criar um novo plano**. A caixa de diálogo **do novo plano** aparece.

Na caixa de ID do **Plano,** crie um identificador de plano único para cada plano nesta oferta. Este ID será visível para os clientes no endereço web do produto. Utilize apenas letras minúsculas e números, traços ou sublinhados, e um máximo de 50 caracteres.

> [!NOTE]
> O ID do plano não pode ser alterado depois de selecionar **Criar**.

Na caixa de **nome** sinuosa, insira um nome para este plano. Os clientes vêem este nome ao decidir qual o plano a selecionar dentro da sua oferta. Crie um nome único para cada plano nesta oferta. Por exemplo, pode utilizar um nome de oferta do **Windows Server** com planos Windows **Server 2016** e **Windows Server 2019**.

### <a name="plan-setup"></a>Configuração do plano

Este separador permite-lhe escolher em que nuvens o plano está disponível. As suas respostas neste separador afetam os campos apresentados noutros separadores.

#### <a name="cloud-availability"></a>Disponibilidade de nuvem

O seu plano deve estar disponível em pelo menos uma nuvem.

Selecione a opção **Azure Global** para que o seu plano possa ser usado por clientes em todas as regiões globais do Azure que utilizam o mercado comercial. Para mais detalhes, consulte disponibilidade [geográfica e suporte cambial.](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies)

Selecione a opção [**Azure Government Cloud**](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) para fazer a sua solução aparecer aqui. Esta é uma nuvem da comunidade governamental com acesso controlado para clientes de agências governamentais federais, estaduais e locais ou tribais dos EUA, bem como parceiros elegíveis para servi-los. Como editora, é responsável por quaisquer controlos de conformidade, medidas de segurança e boas práticas para esta comunidade de nuvem. O Governo azure utiliza centros e redes de dados fisicamente isolados (localizados apenas nos EUA).

Antes de [publicar](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners) ao Governo Azure, teste e confirme a sua solução dentro dessa área, uma vez que os resultados podem ser diferentes. Para criar e testar a sua solução, solicite uma conta de teste do [julgamento do Governo microsoft Azure](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> Depois de o seu plano ser publicado e disponível numa nuvem específica, não pode remover essa nuvem.

#### <a name="azure-government-cloud-certifications"></a>Certificações Azure Government Cloud

Esta opção só pode ser vista se **a Nuvem governamental Azure** for selecionada sob **disponibilidade**cloud .

Os serviços do Governo Azure tratam de dados que estão sujeitos a certos regulamentos e requisitos governamentais. Por exemplo, FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS.

Para mostrar as suas certificações para estes programas, pode fornecer até 100 links que os descrevem. Estes podem ser links para as suas listas no programa diretamente ou para o seu próprio site. Estas ligações são visíveis apenas para os clientes do Governo Azure.

## <a name="plan-listing"></a>Listagem de planos

Este separador exibe informações específicas para cada plano diferente dentro da oferta atual.

### <a name="plan-name"></a>Nome do plano

Isto está preenchido com o nome que deu ao seu plano quando o criou. Pode mudar este nome quando necessário. Pode ter até 50 caracteres de comprimento. Este nome aparece como o título deste plano no portal Azure Marketplace e Azure. É usado como o nome padrão do módulo depois do plano estar pronto para ser usado.

### <a name="plan-summary"></a>Resumo do plano

Um resumo curto do seu plano de software (não a oferta). Este resumo aparece nos resultados de pesquisa do Azure Marketplace e pode conter até 100 caracteres.

### <a name="plan-description"></a>Descrição do plano

Descreva o que torna este plano de software único, bem como diferenças entre planos dentro da sua oferta. Não descreva a oferta, só o plano. Esta descrição aparecerá no Azure Marketplace e no portal Azure na página **de listagem de oferta.** Pode ser o mesmo conteúdo que forneceu no resumo do plano e conter até 2.000 caracteres.

Selecione **Guardar** depois de completar estes campos.

#### <a name="plan-examples"></a>Exemplos de planos

Os exemplos seguintes mostram como os campos de listagem do plano aparecem em diferentes pontos de vista.

Estes são os campos do Mercado Azure ao visualizar detalhes do plano:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-10-plan-details-mtplc.png" alt-text="Ilustra os campos que vê ao ver os detalhes do plano no Azure Marketplace.":::

Estes são detalhes do plano no portal Azure:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-11-plan-details-portal.png" alt-text="Ilustra detalhes do plano no portal Azure.":::

## <a name="plan-availability"></a>Disponibilidade do plano

Se quiser esconder a sua oferta publicada para que os clientes não possam pesquisar, navegar ou comprá-la no mercado, selecione a caixa de verificação do **plano Ocultar** no separador **Disponibilidade.**

Este campo é utilizado quando:

- A oferta destina-se a ser utilizada indiretamente quando referenciada, embora outra aplicação.
- A oferta não deve ser comprada individualmente.
- O plano foi utilizado para os testes iniciais e já não é relevante.
- O plano foi utilizado para ofertas temporárias ou sazonais e não deve mais ser oferecido.

## <a name="technical-configuration"></a>Configuração técnica

As imagens do contentor devem ser alojadas num registo privado de [contentores azure.](https://azure.microsoft.com/services/container-registry/) No separador **Configuração Técnica,** forneça informações de referência para o seu repositório de imagem de contentor dentro do Registo de Contentores Azure.

Após a publicação da oferta, a sua imagem de contentor é copiada para o Azure Marketplace num registo específico de contentores públicos. Todos os pedidos para usar a sua imagem de contentor são servidos a partir do registo de contentores públicos do Azure Marketplace, e não do seu privado. Para mais detalhes, consulte [Prepare os seus ativos técnicos do Recipiente Azure.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets)

### <a name="image-repository-details"></a>Detalhes do repositório de imagem

Forneça as seguintes informações sobre o separador de dados do **repositório imagem.**

**ID de subscrição Azure** – Forneça o ID de subscrição onde o uso é reportado e os serviços são faturados para o Registo de Contentores Azure que inclui a sua imagem de contentor. Pode encontrar este ID na [página de Subscrições](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) no portal Azure.

Nome do grupo de **recursos Azure** – Forneça o nome do grupo de [recursos](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal) que contém o Registo de Contentores Azure com a sua imagem de contentor. O grupo de recursos deve estar acessível no ID de subscrição (acima). Pode encontrar o nome na página dos [grupos de Recursos](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) no portal Azure.

Nome do Registo de **Contentores Azure** – Forneça o nome do Registo de [Contentores Azure](https://docs.microsoft.com/azure/container-registry/container-registry-intro) que tem a sua imagem de contentor. O registo de contentores deve estar no grupo de recursos Azure que forneceu anteriormente. Inclua apenas o nome do registo, não o nome completo do servidor de login. Certifique-se de omitir **azurecr.io** do nome. Pode encontrar o nome do registo na [página de Registos](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) de Contentores no portal Azure.

Nome de utilizador administrativo para o Registo de **Contentores Azure** – Forneça o nome de [utilizador do administrador](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account) ligado ao Registo de Contentores Azure que tem a sua imagem de contentor. O nome de utilizador e a palavra-passe são necessários para garantir que a sua empresa tem acesso ao registo. Para obter o nome de utilizador e a palavra-passe do administrador, delineie a propriedade **ativada pela administração** para **True** utilizando a Interface de Linha de Comando Azure (CLI). Pode configurar opcionalmente **o utilizador da Admin** para **ativar** no portal Azure.

 :::image type="content" source="media/azure-create-container-offer-images/azure-create-12-update-container-registry-edit.png" alt-text="Ilustra a caixa de diálogo de registo de recipientes Update.":::

**Palavra-passe para o Registo de Contentores Azure** – Forneça a palavra-passe para o nome de utilizador do administrador que está associado ao Registo de Contentores Azure e tem a sua imagem de contentor. O nome de utilizador e a palavra-passe são necessários para garantir que a sua empresa tem acesso ao registo. Pode obter a palavra-passe do portal Azure indo para**As Chaves** de Acesso ao Registo > de **Contentores**ou com o Azure CLI utilizando o comando do [show](https://docs.microsoft.com/cli/azure/acr/credential?view=azure-cli-latest#az-acr-credential-show).

:::image type="content" source="media/azure-create-container-offer-images/azure-create-13-access-keys.png" alt-text="Ilustra o menu Chave de Acesso.":::

**Nome do repositório no registo do contentor azure**. Forneça o nome do repositório do Registo de Contentores Azure que tem a sua imagem. Inclua o nome do repositório quando empurrar a imagem para o registo. Você pode encontrar o nome do repositório indo para a página de > **Repositórios** do Registo de [Contentores.](https://azure.microsoft.com/services/container-registry/) Para mais informações, consulte [os repositórios](https://docs.microsoft.com/azure/container-registry/container-registry-repositories)de registo de contentores no portal Azure .

> [!NOTE]
> Depois do nome estar definido, não pode ser alterado. Use um nome único para cada oferta na sua conta.

### <a name="image-tags-for-new-versions-of-your-offer"></a>Etiquetas de imagem para novas versões da sua oferta

Os clientes devem poder receber automaticamente atualizações do Mercado Azure quando publicar uma atualização. Se não quiserem atualizar, devem poder ficar numa versão específica da sua imagem. Pode fazê-lo adicionando novas etiquetas de imagem cada vez que faz uma atualização à imagem.

### <a name="image-tag"></a>Etiqueta de imagem

Este campo deve incluir uma etiqueta **mais recente** que aponte para a versão mais recente da sua imagem em todas as plataformas suportadas. Deve também incluir uma etiqueta de versão (por exemplo, começando pelo xx.xx.xx.xx, onde xx é um número). Os clientes devem usar [etiquetas de manifesto](https://github.com/estesp/manifest-tool) para direcionar várias plataformas. Todas as etiquetas referenciadas por uma etiqueta de manifesto também devem ser adicionadas para que possamos carregá-las.

Todas as etiquetas de manifesto (exceto a **-** etiqueta mais recente) devem começar com X.Y ou X.Y.Z- onde X, Y e Z são inteiros. Por exemplo, se uma etiqueta **mais recente** aponta para 1.0.1-linux-x64, 1.0.1-linux-arm32 e 1.0.1-windows-arm32, estas seis tags precisam de ser adicionadas a este campo. Para mais detalhes, consulte [Prepare os seus ativos técnicos do Recipiente Azure.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets)

> [!NOTE]
> Lembre-se de adicionar uma etiqueta de teste à sua imagem para que possa identificar a imagem durante os testes.

## <a name="review-and-publish"></a>Rever e publicar

Depois de ter completado todas as secções necessárias da oferta, pode submetê-la para rever e publicar.

No canto superior direito do portal, selecione **Rever e** **publique**.

Na página de revisão pode:

- Consulte o estado de conclusão de cada secção da oferta. Não pode publicar até que todas as secções da oferta estejam marcadas como completas.
  - **Não começou** – Ainda não foi iniciado e precisa de ser concluído.
  - **Incompleto** – Tem erros que precisam de ser corrigidos ou que exigem que forneça mais informações. Consulte as secções mais cedo neste documento para obter ajuda.
  - **Completo** – Inclui todos os dados necessários sem erros. Todas as secções da oferta devem estar completas antes de poder submeter a oferta.
- Forneça instruções de teste à equipa de certificação para garantir que a sua oferta é testada corretamente. Além disso, forneça quaisquer notas suplementares que sejam úteis para entender a sua oferta.

Para submeter a oferta para publicação, selecione **Publicar**.

Enviaremos um e-mail para informá-lo quando uma versão de pré-visualização da oferta está disponível para rever e aprovar.

Para publicar a sua oferta ao público (ou se uma oferta privada, para um público privado), vá ao Partner Center e selecione **Go-live**.

## <a name="next-step"></a>Passo seguinte

- [Atualizar uma oferta existente no Marketplace Comercial](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
