---
title: Cliente analisador de Internet incorporado [ Microsoft Docs
description: Neste artigo, aprenda a incorporar o cliente JavaScript do Analisador de Internet na sua aplicação.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: quickstart
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: f9ecb8d731945847160b49c68c554fafdd7285d9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "74896374"
---
# <a name="embed-the-internet-analyzer-client"></a>Incorporar o cliente analisador de Internet

Este artigo mostra-lhe como incorporar o cliente JavaScript na sua aplicação. A instalação deste cliente é necessária para realizar testes e receber análises de cartões de pontuação. **O cliente JavaScript específico do perfil é fornecido após a configuração do primeiro teste.** A partir daí, pode continuar a adicionar ou remover testes a esse perfil sem ter de incorporar um novo script. Para mais informações sobre o Analisador de Internet, consulte a [visão geral](internet-analyzer-overview.md). 

> [!IMPORTANT]
> Esta pré-visualização pública é disponibilizada sem um contrato de nível de serviço e não deve ser utilizada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, podem ter capacidades restringidas ou podem não estar disponíveis em todas as localizações do Azure. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.
>

## <a name="before-you-begin"></a>Antes de começar

O Internet Analyzer requer acesso ao Azure e a outros serviços da Microsoft para funcionar corretamente. Por favor, `fpc.msedge.net` permita o acesso à rede e quaisquer URLs de ponto final pré-configurados (visíveis através [de CLI)](internet-analyzer-cli.md)antes de incorporar o cliente.

## <a name="find-the-client-script-url"></a>Encontre o URL do script do cliente

O URL do script pode ser encontrado através do portal Azure ou do Azure CLI depois de configurado um teste. Para mais informações, consulte [Criar um Recurso analisador](internet-analyzer-create-test-portal.md)de Internet .

Opção 1. No portal Azure, utilize [este link](https://aka.ms/InternetAnalyzerPreviewPortal) para abrir a página do portal de pré-visualização do Azure Internet Analyzer. Navegue para o seu perfil de Analisador de Internet para ver o URL do script indo para **Definições > Configuração**.

Opção 2. Utilizando o Azure CLI, verifique a `scriptFileUri` propriedade.
```azurecli-interactive
    az extension add --name internet-analyzer    
    az internet-analyzer test list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
```

## <a name="client-details"></a>Detalhes do cliente

O script é gerado especificamente para o seu perfil e testes. Depois de carregado, o script executará com um atraso de 2 segundos. Primeiro contacta o serviço De Análise da Internet para obter a lista de pontos finais configurados nos seus testes. Em seguida, executa as medições e envia os resultados cronometrados de volta para o serviço de Analisador de Internet.

## <a name="client-examples"></a>Exemplos de clientes

Estes exemplos mostram alguns métodos básicos para incorporar o javaScript do cliente na sua página web ou aplicação. Usamos `0bfcb32638b44927935b9df86dcfe397` como um id de perfil de exemplo para o URL do script.

### <a name="run-on-page-load"></a>Executar na carga da página
O método mais simples é usar a etiqueta de script dentro do bloco de meta tag. Esta etiqueta executará o script uma vez por carga de página.

```html
<html>
<meta>
    <script src="//fpc.msedge.net/client/v2/0bfcb32638b44927935b9df86dcfe397/ab.min.js"></script>
</meta>
<body></body>
</html>
```

## <a name="next-steps"></a>Passos seguintes

Leia o [FaQ do Analisador](internet-analyzer-faq.md) de Internet
