---
title: Resolução de problemas - QnA Maker
description: A lista com curadoria das perguntas mais frequentes sobre o serviço QnA Maker irá ajudá-lo a adotar o serviço mais rapidamente e com melhores resultados.
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: diberry
ms.openlocfilehash: 7847e21dbcf07f669d6802fffdd1e43623a72340
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804355"
---
# <a name="troubleshooting-for-qna-maker"></a>Resolução de problemas para O Criador de QnA

A lista com curadoria das perguntas mais frequentes sobre o serviço QnA Maker irá ajudá-lo a adotar o serviço mais rapidamente e com melhores resultados.

<a name="how-to-get-the-qnamaker-service-hostname"></a>

## <a name="manage-predictions"></a>Gerir previsões

<details>
<summary><b>Como posso melhorar o desempenho da entrada para previsões de consultas?</b></summary>

**Resposta**: Problemas de desempenho de desempenho de desempenho indicam que precisa de aumentar tanto para o seu serviço de Aplicações como para a sua Pesquisa Cognitiva. Considere adicionar uma réplica à sua Pesquisa Cognitiva para melhorar o desempenho.

Saiba mais sobre [os níveis de preços.](Concepts/azure-resources.md)
</details>

<details>
<summary><b>Como obter o ponto final do serviço QnAMaker</b></summary>

**Resposta**: O ponto final do serviço QnAMaker é útil para fins de depuração quando contacta o QnAMaker Support ou userVoice. O ponto final é um `https://your-resource-name.azurewebsites.net`URL desta forma: .

1. Vá ao seu serviço QnAMaker (grupo de recursos) no [portal Azure](https://portal.azure.com)

    ![Grupo de recursos QnAMaker Azure no portal Azure](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Selecione o Serviço de Aplicações associado ao recurso QnA Maker. Normalmente, os nomes são os mesmos.

     ![Selecione QnAMaker App Service](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. O URL do ponto final está disponível na secção Visão Geral

    ![Ponto final do QnAMaker](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)

</details>

## <a name="manage-the-knowledge-base"></a>Gerir a base de conhecimento

<details>
<summary><b>Apaguei acidentalmente uma parte do meu Fabricante de QnA, o que devo fazer?</b></summary>

**Resposta**: Não elimine nenhum dos serviços Azure criados juntamente com o recurso QnA Maker, como Search ou Web App. Estes são necessários para que o Fabricante qnA funcione, se eliminar um, o Fabricante QnA deixará de funcionar corretamente.

Todos os deletes são permanentes, incluindo pares de perguntas e respostas, ficheiros, URLs, perguntas e respostas personalizadas, bases de conhecimento ou recursos Azure. Certifique-se de que exporta a sua base de conhecimentos a partir da página **Definições** antes de apagar qualquer parte da sua base de conhecimentos.

</details>

<details>
<summary><b>Porque é que o meu URL/ficheiro(s) não está a extrair pares de respostas a perguntas?</b></summary>

**Resposta**: É possível que o Fabricante QnA não possa extrair automaticamente alguns conteúdos de perguntas e respostas (QnA) a partir de URLs FAQ válidos. Nesses casos, pode colar o conteúdo do QnA num ficheiro .txt e ver se a ferramenta pode ingerir. Alternadamente, pode adicionar conteúdo editorialmente à sua base de conhecimentos através do [portal QnA Maker](https://qnamaker.ai).

</details>

<details>
<summary><b>Quão grande é uma base de conhecimento que posso criar?</b></summary>

**Resposta**: O tamanho da base de conhecimentos depende da pesquisa SKU of Azure que escolher ao criar o serviço QnA Maker. Leia [aqui](./Tutorials/choosing-capacity-qnamaker-deployment.md) para mais detalhes.

</details>

<details>
<summary><b>Por que não vejo nada na entrega quando tento criar uma nova base de conhecimento?</b></summary>

**Resposta**: Ainda não criou nenhum serviço qnA Maker em Azure. Leia [aqui](./How-To/set-up-qnamaker-service-azure.md) para aprender a fazer isso.

</details>

<details>
<summary><b>Como partilho uma base de conhecimento com os outros?</b></summary>

**Resposta**: A partilha de trabalhos ao nível de um serviço QnA Maker, ou seja, todas as bases de conhecimento do serviço serão partilhadas. Leia [aqui](./How-To/collaborate-knowledge-base.md) como colaborar numa base de conhecimento.

</details>

<details>
<summary><b>Pode partilhar uma base de conhecimento com um colaborador que não está no mesmo inquilino da AAD, para modificar uma base de conhecimento?</b></summary>

**Resposta**: A partilha baseia-se no controlo de acesso baseado em funções azure (RBAC). Se puder partilhar _qualquer_ recurso em Azure com outro utilizador, também pode partilhar o QnA Maker.

</details>

<details>
<summary><b>Se tiver um Plano de Serviço de Aplicações com 5 bases de conhecimento QnAMaker. Pode atribuir direitos de leitura/escrita a 5 utilizadores diferentes para que cada um deles possa aceder apenas a 1 base de conhecimentos QnAMaker?</b></summary>

**Resposta**: Pode partilhar todo um serviço QnAMaker, não bases de conhecimento individuais.

</details>

<details>
<summary><b>Como posso mudar a mensagem padrão quando não se encontra uma boa correspondência?</b></summary>

**Resposta**: A mensagem predefinida faz parte das definições do seu serviço de Aplicações.
- Vá ao seu recurso de serviço app no portal Azure

![qnamaker appservice](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Clique na opção **Definições**

![definições de serviço de aplicação qnamaker](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- Alterar o valor da definição **DefaultAnswer**
- Reinicie o seu serviço de Aplicações

![qnamaker appservice reiniciar](./media/qnamaker-faq/qnamaker-appservice-restart.png)


</details>

<details>
<summary><b>Porque é que o meu link SharePoint não está a ser extraído?</b></summary>

**Resposta**: Consulte as [localizações](./Concepts/knowledge-base.md#data-source-locations) de origem de dados para obter mais informações.

</details>

<details>
<summary><b>As atualizações que fiz à minha base de conhecimentos não se refletem na publicação. Porque não?</b></summary>

**Resposta**: Cada operação de edição, seja numa atualização de tabela, teste ou definição, precisa de ser guardada antes de poder ser publicada. Certifique-se de clicar no botão **Guardar e treinar** após cada operação de edição.

</details>

<details>
<summary><b>A base de conhecimentos suporta dados ricos ou multimédia?</b></summary>

**Resposta:**

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>Extração automática multimédia para ficheiros e URLs

* URLS - capacidade limitada de conversão HTML-to-Markdown.
* Ficheiros - não suportados

#### <a name="answer-text-in-markdown"></a>Responder texto em marcação
Uma vez que os pares QnA estão na base de conhecimento, você pode editar o texto de marcação de uma resposta para incluir links para meios disponíveis a partir de URLs públicos.


</details>

<details>
<summary><b>O QnA Maker suporta línguas não inglesas?</b></summary>

**Resposta**: Consulte mais detalhes sobre [línguas suportadas](./Overview/languages-supported.md).

Se tiver conteúdo de várias línguas, certifique-se de criar um serviço separado para cada idioma.

</details>

## <a name="manage-service"></a>Gerir serviço

<details>
<summary><b>Quando devo reiniciar o meu serviço de aplicações?</b></summary>

**Resposta**: Refresque o seu serviço de aplicações quando o ícone de precaução estiver ao lado do valor da versão para a base de conhecimentos na tabela de **teclas Endpoint** na [página](https://www.qnamaker.ai/UserSettings) **Definições** do Utilizador .

</details>

<details>
<summary><b>Apaguei o meu serviço de pesquisa existente. Como posso consertar isto?</b></summary>

**Resposta**: Se eliminar um índice de Pesquisa Cognitiva Azure, a operação é definitiva e o índice não pode ser recuperado.

</details>

<details>
<summary><b>Apaguei `testkb` o meu índice no meu serviço de pesquisa. Como posso consertar isto?</b></summary>

**Resposta:** Os seus dados antigos não podem ser recuperados. Crie um novo recurso QnA Maker e crie novamente a sua base de conhecimento.

</details>

<details>
<summary><b>Quando devo refrescar as minhas chaves finais?</b></summary>

**Resposta:** Refresque as chaves do ponto final se suspeitar que foram comprometidas.

</details>

<details>
<summary><b>Posso usar o mesmo recurso azure cognitive search para bases de conhecimento usando várias línguas?</b></summary>

**Resposta**: Para utilizar múltiplas bases de linguagem e conhecimento múltiplas, o utilizador tem de criar um recurso QnA Maker para cada idioma. Isto criará um serviço de pesquisa Azure separado por idioma. Misturar diferentes bases de conhecimento linguístico num único serviço de pesquisa azure resultará numa relevância degradada dos resultados.

</details>

<details>
<summary><b>Como posso alterar o nome do recurso azure cognitive search usado pela QnA Maker?</b></summary>

**Resposta**: O nome do recurso de pesquisa cognitiva Azure é o nome de recurso QnA Maker com algumas letras aleatórias anexadas no final. Isto torna difícil distinguir entre múltiplos recursos de pesquisa para o Fabricante QnA. Crie um serviço de pesquisa separado (nomeando-o como gostaria) e conecte-o ao seu Serviço QnA. Os passos são semelhantes aos passos que precisa de fazer para [atualizar uma pesquisa azure](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service).

</details>

<details>
<summary><b>Quando a QnA Maker retorna, `Runtime core is not initialized,` como é que o arranjo?</b></summary>

**Resposta**: O espaço do disco para o seu serviço de aplicações pode estar cheio. Passos para fixar o seu espaço de disco:

1. No [portal Azure,](https://portal.azure.com)selecione o serviço de Aplicações do Fabricante QnA e, em seguida, pare o serviço.
1. Enquanto ainda está no serviço app, selecione **Ferramentas**de Desenvolvimento , em **seguida, Ferramentas Avançadas**, em **seguida, Ir**. Isto abre uma nova janela do navegador.
1. Selecione **a consola Debug**e, em seguida, **CMD** para abrir uma ferramenta de linha de comando.
1. Navegue para o _site/wwwroot/Data/QnAMaker/_ diretório.
1. Remova todas as pastas `rd`cujo nome começa com .

    **Não apague** o seguinte:

    * Ficheiro KbIdToRankerMappings.txt
    * Ficheiro EndpointSettings.json
    * Pasta EndpointKeys

1. Inicie o serviço app.
1. Aceda à sua base de conhecimentos para verificar se funciona agora.

</details>

## <a name="integrate-with-other-services-including-bots"></a>Integrar outros serviços, incluindo Bots

<details>
<summary><b>Preciso de usar a Bot Framework para usar o Fabricante QnA?</b></summary>

**Resposta**: Não, não precisa de utilizar o [Quadro Bot](https://github.com/Microsoft/botbuilder-dotnet) com o Fabricante QnA. No entanto, o Fabricante QnA é oferecido como um dos vários modelos no [Serviço Bot Azure](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0). O Bot Service permite o rápido desenvolvimento inteligente de bots através do Microsoft Bot Framework, e funciona num ambiente sem servidores.

</details>

<details>
<summary><b>Como posso criar um novo bot com o QnA Maker?</b></summary>

**Resposta**: Siga as instruções [desta](./Quickstarts/create-publish-knowledge-base.md) documentação para criar o seu Bot com o Serviço Bot Azure.

</details>

<details>
<summary><b>Como uso uma base de conhecimento diferente com um serviço de bots Azure existente?</b></summary>

**Resposta**: É necessário ter as seguintes informações sobre a sua base de conhecimentos:

* Identificação da base de conhecimento.
* O nome de subdomínio personalizado de ponto `host`final publicado da base de conhecimento, conhecido como , encontrado na página **Definições** após a sua publicação.
* Chave final publicada da base de conhecimento - encontrada na página **Definições** após a publicação.

Com esta informação, vá ao serviço de aplicações do seu bot no portal Azure. Em **Definições -> Configurações de Configuração -> Aplicação,** altere esses valores.

A chave final da base de `QnAAuthkey` conhecimento está rotulada no serviço ABS.

</details>

<details>
<summary><b>Duas ou mais aplicações de clientes podem partilhar uma base de conhecimento?</b></summary>

**Resposta**: Sim, a base de conhecimento pode ser consultada por qualquer número de clientes. Se a resposta da base de conhecimento parecer lenta ou desativada, considere melhorar o nível de serviço para o serviço de aplicações associado à base de conhecimentos.

</details>

<details>
<summary><b>Como insinto o serviço QnA Maker no meu site?</b></summary>

**Resposta**: Siga estes passos para incorporar o serviço QnA Maker como um controlo de chat web no seu site:

1. Crie o seu bot FAQ seguindo as instruções [aqui](./Quickstarts/create-publish-knowledge-base.md).
2. Ativar o chat web seguindo os passos [aqui](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat)

</details>

## <a name="data-storage"></a>Armazenamento de dados

<details>
<summary><b>Que dados são armazenados e onde são armazenados?</b></summary>

**Resposta:**

Ao criar o seu serviço QnA Maker, selecionou uma região Azure. As suas bases de conhecimento e ficheiros de registo estão armazenados nesta região.

</details>