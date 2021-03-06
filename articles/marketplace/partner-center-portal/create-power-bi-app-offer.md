---
title: Criar uma oferta de aplicativo Power BI - Azure Marketplace
description: Saiba como criar e publicar uma oferta de aplicação Power BI para o Microsoft AppSource.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: 776311d6e6395cbe462f958bd8685fa0259e1fc2
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80674192"
---
# <a name="create-a-power-bi-app"></a>Criar uma aplicação Power BI

> [!IMPORTANT]
> Estamos a mover a gestão das ofertas da sua aplicação Power BI do Cloud Partner Portal para partner Center. Até que as suas ofertas sejam migradas, siga as instruções na [Oferta de Aplicações Create a Power BI](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/power-bi/cpp-create-offer) para o Cloud Partner Portal para gerir as suas ofertas.

Este artigo descreve como criar e publicar uma oferta de aplicação Power BI para o Microsoft [AppSource](https://appsource.microsoft.com/).

Antes de poder criar uma oferta de aplicações Power BI, deve ter uma conta de marketplace comercial no Partner Center. Se ainda não criou uma, consulte criar uma conta de Mercado Comercial na conta [partner center.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)

## <a name="create-a-new-offer"></a>Criar uma nova oferta

1. Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard/home).
2. No menu de navegação à esquerda, selecione **Commercial Marketplace** > **Overview**.

    :::image type="content" source="media/power-bi-menu-overview.png" alt-text="visão geral do menu do mercado comercial" border="false":::

3. Selecione **+ Nova oferta** > **Power BI App**. A caixa de diálogo **da Nova Oferta** aparece.

> [!IMPORTANT]
> Se a opção **Power BI App** não for&#39;mostrada ou&#39;não estiver ativada, a sua conta não&#39;ter permissão para criar este tipo de oferta. Por favor, verifique se&#39;cumpriu todos os [requisitos](create-power-bi-app-overview.md) para este tipo de oferta, incluindo o registo para uma conta de desenvolvedor

### <a name="offer-id-and-alias"></a>Oferecer ID e pseudónimo

Introduza um **ID de oferta**. Este é um identificador único para cada oferta na sua conta.

- Este ID é visível para os clientes no endereço web para a oferta de mercado e modelos do Gestor de Recursos Azure, se aplicável.
- Utilize apenas letras minúsculas e números. Pode incluir hífenes e sublinhados, mas sem espaços, e está limitado a 50 caracteres. Por exemplo, se introduzir o **test-offer-1,** `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`o endereço web da oferta será .

- O ID da Oferta não pode ser alterado depois de selecionar **Criar**.

Insira um **pseudónimo da Oferta.** Este é o nome usado para se referir à oferta no Partner Center.

- Este nome não é usado no mercado e é diferente do nome da oferta e outros valores mostrados aos clientes.
- Isto não pode ser alterado depois de selecionar **Criar**.

Depois de introduzir estes dois valores, selecione **Criar** para continuar na página de visão geral da Oferta.

## <a name="offer-overview"></a>Visão geral da oferta

A página geral da **Oferta** mostra uma representação visual dos passos necessários para publicar esta oferta (completa e próxima) e quanto tempo cada passo deve demorar a ser concluído.

Inclui links para realizar operações nesta oferta com base na seleção que faz. Por exemplo:

- Se a oferta for um rascunho - [Eliminar a oferta](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer) de rascunho
- Se a oferta estiver ao vivo - [Pare de vender a oferta](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Se a oferta estiver em pré-visualização - [Go-live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Se ainda não tiver concluído a inscrição da editora - [Cancele a publicação](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>Configuração de oferta

Siga estes passos para configurar a sua oferta.

### <a name="connect-lead-management"></a>Ligar a gestão de chumbo

Ao publicar a sua oferta no mercado com o Partner Center, deve ligá-la ao seu sistema de Gestão de Relacionamento com o Cliente (CRM). Isto permite-lhe receber informações de contacto do cliente assim que alguém expressa interesse ou utiliza o seu produto.

1. Selecione um destino de oportunidades potenciais para onde quer que enviemos as oportunidades potenciais de clientes. Partner Center suporta os seguintes sistemas CRM:

    - [Dinâmica 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) para envolvimento com o cliente
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Se o seu sistema CRM não estiver listado acima, utilize [o Azure Table](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) ou [o Https Endpoint](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) para armazenar os dados de chumbo do cliente. Em seguida, exporte os dados para o seu sistema crm.

2. Ligue a sua oferta ao destino principal ao publicar no Partner Center.
3. Confirme se a ligação ao destino principal está corretamente configurada. Depois de publicá-lo no Partner Center, validamos a ligação e enviaremos-lhe um teste. Enquanto faz a pré-visualização da oferta antes de ser transmitida ao vivo, também pode testar a sua ligação de chumbo tentando adquirir a oferta no ambiente de pré-visualização.
4. Certifique-se de que a ligação ao destino principal permanece atualizada para que não perca nenhuma pista.

Aqui estão alguns recursos adicionais de gestão de chumbo:

- [Visão geral da gestão de chumbo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [FAQs de gestão de oportunidades potenciais](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Erros de configuração comuns de oportunidades potenciais](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Visão geral da gestão de chumbo](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (certifique-se de que o seu bloqueador pop-up está desligado)

Selecione **Guardar rascunho** antes de continuar na secção seguinte, Propriedades.

## <a name="properties"></a>Propriedades

Esta página permite definir as categorias e indústrias usadas para agrupar a sua oferta no mercado, na sua versão de aplicação e nos contratos legais que suportam a sua oferta.

### <a name="category"></a>Categoria

Selecione um mínimo de uma e um máximo de três categorias. Estas categorias são usadas para colocar a sua oferta nas áreas de pesquisa de mercado apropriadas e são mostradas na sua página de detalhes de oferta. Na descrição da oferta, explique como a sua oferta suporta estas categorias.

### <a name="industry"></a>Setor

Opcionalmente, selecione até duas indústrias e duas verticais sob cada indústria. Enquanto as categorias são usadas para exibir a sua oferta, a indústria e os verticais são usados em filtros de pesquisa e são aplicados na Montra. Se a sua oferta visar uma indústria específica e/ou vertical, use a descrição da oferta para explicar como a sua oferta suporta as indústrias ou verticais selecionadas. Se a sua oferta não for&#39;específica da indústria, deixe esta secção em branco.

> [!NOTE]
> À medida que trabalhamos para introduzir novas indústrias e verticais para melhorar a experiência de descoberta de ofertas, algumas indústrias ou verticais podem ainda não ser visíveis na Montra. Indústrias e verticais marcados com um (*) estarão disponíveis numa data futura. Todas as ofertas publicadas são detetáveis através da pesquisa de palavras-chave.
<p>&nbsp;

| **Setor** | **Subindústria** |
| --- | --- |
| *Automóvel | *Automóvel |
| Agricultura | *Outros - Não segmentado |
| Distribuição | *Por grosso<br>Envio de encomendas e pacotes |
| Education | *Ensino<br> Superior* Ensino Primário e Secundário / K-12<br>*Bibliotecas e Museus |
| Serviços Financeiros | *Mercados<br> Bancários e De Capitais* Seguro |
| Governo | *Defesa e Inteligência (costumava ser chamado<br> de Segurança Nacional e Pública)* Segurança Pública e Justiça<br>*Governo Civil |
| Cuidados de saúde (costumavam chamar-se Saúde) | *Pagador de<br> Saúde* Provedor de Saúde<br>*Farmacêuticos |
| Fabrico e Recursos (costumava chamar-se Fabricação) | *Química e<br> Agroquímica* Fabrico discreto<br>*Energia |
| Produtos de retalho e de consumo (costumavam chamar-se Retalho) | *Bens de<br> Consumo* Retalhistas |
| *Media and Communications (costumava chamar-se Media and Entertainment) | *Media e<br> Entretenimento* Telecomunicações |
| Serviços Profissionais | *Legal<br> * Serviços Profissionais parceiros |
| *Arquitetura e Construção (costumava ser chamada engenharia de arquitetura) | *Outros - Não segmentado |
| *Hospitalidade e Viagens | *Hotéis<br> e Lazer* Viagens e Transportes<br>*Restaurantes e Serviços alimentares |
| *Outras Indústrias do Sector Público | *Silvicultura<br> e Pesca* Sem fins lucrativos |
| *Imóveis | *Outros - Não segmentado |

### <a name="legal"></a>Legal

#### <a name="terms-and-conditions"></a>Termos e condições

Para fornecer os seus próprios termos e condições personalizados, insira até 10.000 caracteres na caixa **de Condições e Termos.** Se os seus termos e condições requerem uma descrição mais longa, insira um único link web para onde possam ser encontrados. Mostrará aos clientes como um link ativo.

Os clientes devem aceitar estes termos antes de poderem experimentar a sua oferta.

Selecione **Guardar rascunho** antes de continuar na secção seguinte, Oferta de listagem.

## <a name="offer-listing"></a>Oferta listagem

Aqui irá definir os detalhes da oferta que são exibidos no mercado. Isto inclui o nome da oferta, descrição, imagens, e assim por diante.

### <a name="language"></a>Idioma

Selecione o idioma em que a sua oferta será listada. Atualmente, **o inglês (Estados Unidos)** é a única opção disponível.

Defina detalhes do mercado (como nome, descrição e imagens de oferta) para cada idioma/mercado. Selecione o nome idioma/mercado para fornecer esta informação.

> [!NOTE]
> Os detalhes da oferta não são necessários para estar &quot;em inglês se a descrição da oferta começar com a frase, esta aplicação está disponível apenas em [língua não inglesa]. &quot; Também&#39;é bom fornecer um Link Útil para oferecer conteúdo num idioma que&#39;é diferente daquele usado na listagem de ofertas.

### <a name="name"></a>Nome

O nome que insere aqui mostra como o título da sua oferta. Este campo está pré-preenchido com o texto que inseriu na caixa **de pseudónimos Offer** quando criou a oferta. Pode alterar este nome posteriormente.

O nome:

- Pode ser marcado por marca (e pode incluir símbolos de marca ou direitos de autor).
- Não deve ter mais de 50 caracteres.
- Não posso incluir emojis.

### <a name="search-results-summary"></a>Resumo dos resultados da pesquisa

Forneça uma breve descrição da sua oferta. Isto pode ter até 100 caracteres de comprimento e é usado em resultados de pesquisa de mercado.

### <a name="description"></a>Descrição

Forneça uma descrição mais longa da sua oferta, até 3.000 caracteres. Isto é apresentado aos clientes na visão geral da listagem do mercado.

Inclua um ou mais dos seguintes na sua descrição:

- O valor e os benefícios fundamentais que a sua oferta proporciona.
- Categoria ou associações industriais, ou ambas.
- Oportunidades de compra na aplicação.
- Quaisquer divulgações necessárias.

Aqui ficam algumas dicas para escrever a sua descrição:

- Descreva claramente o valor da sua oferta nas primeiras frases da sua descrição. Incluir os seguintes itens:
  - Descrição da oferta.
  - O tipo de utilizador que beneficia da oferta.
  - As necessidades do cliente ou emite os endereços da oferta.
- Lembre-se que as primeiras frases podem ser exibidas nos resultados da pesquisa.
- Não confie em funcionalidades e funcionalidades para vender o seu produto. Em vez disso, concentre-se no valor que a sua oferta proporciona.
- Tente utilizar vocabulário específico da indústria ou formulação baseada em benefícios.

Para tornar a sua oferta **Descrição** mais envolvente, use o rico editor de texto para formatar a sua descrição. O rico editor de texto permite-lhe adicionar números, balas, ousados, itálicos e travessos para tornar a sua descrição mais legível.

:::image type="content" source="media/power-bi-rich-text-editor.png" alt-text="ilustra o rico editor de texto" border="false":::

### <a name="search-keywords"></a>Pesquisar palavras-chave

Insira até três palavras-chave de pesquisa opcionais para ajudar os clientes a encontrar a sua oferta no mercado. Para obter os melhores resultados, use também estas palavras-chave na sua descrição.

### <a name="helpprivacy-web-addresses"></a>Endereços web de ajuda/privacidade

Forneça links para ajudar os clientes a entender melhor a sua oferta.

#### <a name="help-link"></a>Link de ajuda

Insira o endereço web onde os clientes podem saber mais sobre a sua oferta.

#### <a name="privacy-policy-url"></a>URL de política de privacidade

Insira o endereço web para a política de privacidade da sua organização. É responsável por garantir que a sua oferta está em conformidade com as leis e regulamentos de privacidade. Também é responsável por publicar uma política de privacidade válida no seu site.

### <a name="contact-information"></a>Informações de Contacto

Deve fornecer o nome, e-mail e número de telefone para um contacto de **suporte** e um **contacto de Engenharia**. Esta informação não é&#39;mostrada aos clientes. Está disponível para a Microsoft e pode ser fornecido aos parceiros do Cloud Solution Provider (CSP).

- Contacto de suporte (obrigatório): Para questões de apoio geral.
- Contacto de engenharia (obrigatório): Para questões técnicas e questões de certificação.
- Contacto do Programa CSP (opcional): Para questões de revendedorrelacionados com o programa CSP.

Na secção de contacto de **suporte,** forneça o endereço web do **site de Suporte** onde os parceiros possam encontrar suporte para a sua oferta.

### <a name="supporting-documents"></a>Documentos comprovantes

Forneça pelo menos um e até três documentos de marketing relacionados em formato PDF. Por exemplo, papéis brancos, brochuras, listas de verificação ou apresentações.

### <a name="marketplace-images"></a>Imagens do mercado

Forneça logotipos e imagens para usar com a sua oferta. Todas as imagens devem estar em formato .png. Imagens desfocadas serão rejeitadas.

#### <a name="store-logos"></a>Logotipos de loja

Forneça ficheiros .png da sua oferta&#39;logotipo s em dois tamanhos: **Pequeno** (48 x 48 pixels) e **Large** (216 x 216 pixels).

Ambos os logótipos são necessários e são usados em diferentes lugares na listagem do mercado.

#### <a name="screenshots"></a>Capturas de Ecrã

Adicione pelo menos uma e até cinco imagens que mostram como a sua oferta funciona. Cada um deve ter 1280 x 720 pixels de tamanho e em formato .png.

#### <a name="videos-optional"></a>Vídeos (opcional)

Adicione cinco vídeos que demonstram a sua oferta. Introduza o vídeo&#39;nome, o seu endereço web e a imagem miniatura do vídeo a 1280 x 720 pixels de tamanho.

#### <a name="additional-marketplace-listing-resources"></a>Recursos adicionais de listagem de mercado

Para saber mais sobre a criação de anúncios de oferta, consulte [oferta de listas de boas práticas.](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="technical-configuration"></a>Configuração técnica

Promova a sua aplicação no Power BI Service para produção e forneça o link instalador de aplicações Power BI que permite aos clientes instalarem a sua aplicação. Para mais informações, consulte [Publicar aplicações com dashboards e relatórios no Power BI](https://docs.microsoft.com/power-bi/service-create-distribute-apps).

## <a name="supplemental-content"></a>Conteúdo suplementar

Forneça informações adicionais sobre a sua oferta para nos ajudar a validá-la. Esta informação não é&#39;mostrada aos clientes nem publicada no mercado.

### <a name="validation-assets"></a>Ativos de validação

Opcionalmente, adicione instruções (até 3.000 caracteres) para ajudar a equipa de validação da Microsoft a configurar, ligar e testar a sua aplicação. Inclua as configurações típicas de configuração, contas, parâmetros ou outras informações que possam ser usadas para testar a opção Connect Data. Esta informação é visível apenas para a equipa de validação e é utilizada apenas para fins de validação.

## <a name="review-and-publish"></a>Rever e publicar

Depois de ter concluído&#39;todas as secções necessárias da oferta, pode submeter a sua oferta para rever e publicar.

No canto superior direito do portal, selecione **Rever e publique**.

Na página de revisão pode:

- Consulte o estado de conclusão de cada secção da oferta. Não pode publicar&#39;até que todas as secções da oferta estejam marcadas como completas.
  - **Não começou** - A secção ainda não foi iniciada e precisa de ser concluída.
  - **Incompleto** - A secção tem erros que precisam de ser corrigidos ou exigem que forneça mais informações. Consulte as secções anteriores neste documento para obter orientação.
  - **Completo** - A secção tem todos os dados necessários e não há erros. Todas as secções da oferta devem estar completas antes de poder submeter a oferta.
- Forneça instruções de teste à equipa de certificação para garantir que a sua aplicação é testada corretamente. Além disso, forneça quaisquer notas suplementares que sejam úteis para entender a sua oferta.

Para submeter a oferta para publicação, selecione **Publicar**.

Enviaremos um e-mail para informá-lo quando uma versão de pré-visualização da oferta está disponível para rever e aprovar. Para publicar a sua oferta ao público (ou se uma oferta privada, para um público privado), vá ao Partner Center e selecione **Go-live**.
