---
title: Crie e partilhe um caderno Jupyter na Pré-visualização de cadernos Azure
description: Crie e execute rapidamente um caderno Jupyter na Pré-visualização de Cadernos Azure, em seguida, partilhe esse caderno com outros.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: d3310444fa28240b8fb1344199514a9601a2c615
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "77064449"
---
# <a name="quickstart-create-and-share-a-notebook-in-azure-notebooks-preview"></a>Quickstart: Crie e partilhe um caderno na Pré-visualização de Cadernos Azure

Neste arranque rápido, cria-se e executa um caderno Jupyter nos Cadernos Azure, e depois partilha-se esse caderno com outros. Jupyter permite combinar facilmente texto Markdown, código executável, dados persistentes, gráficos e visualizações numa tela sharable, o caderno. O Azure Notebooks é um serviço alojado gratuito que serve para desenvolver e executar blocos de notas Jupyter na cloud sem instalação.

## <a name="prerequisites"></a>Pré-requisitos
Nenhum.

## <a name="create-a-new-project-and-notebook"></a>Criar um novo projeto e caderno

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

1. Vá ao [site azurehttps://notebooks.azure.com) Notebooks (e](https://notebooks.azure.com) inscreva-se. Para mais detalhes, consulte [Quickstart - Inscreva-se nos Cadernos Azure](quickstart-sign-in-azure-notebooks.md).

1. Na sua página de perfil público, selecione **My Projects** no topo da página:

    ![My Projects link no topo da janela do navegador](media/quickstarts/my-projects-link.png)

1. Na página **My Projects,** selecione **+ Novo Projeto** (atalho de teclado: n). O botão só **+** pode aparecer como se a janela do navegador fosse estreita:

    ![Novo comando do Projeto na página my projects](media/quickstarts/new-project-command.png)

1. No popup **Create New Project** que aparece, introduza ou detetete os seguintes detalhes, em seguida, selecione **Criar:**

   - **Nome do projeto**: Hello World in Python
   - **Id do projeto**: olá-world-python
   - **Projeto público**: (apurado)
   - **Criar um README.md:**(limpo)

     ![Popup do Novo Projeto com detalhes povoados](media/quickstarts/new-project-popup.png)

1. Após alguns momentos, os Cadernos Azure navegam para o novo projeto. Adicione um caderno ao projeto selecionando o **+ Novo** drop-down (que pode aparecer como apenas **+**), e, em seguida, selecionando O **Caderno:**

    [![](media/quickstarts/empty-project-new-notebook-button.png "A new, empty project and add notebook command")](media/quickstarts/empty-project-new-notebook-button.png#lightbox)

1. No popup **Create New Notebook** que aparece, introduza um nome de ficheiro para o seu caderno, como *HelloWorldInPython.ipynb* (*.ipynb* significa IronPython (Jupyter) Notebook), e selecione **Python 3.6** para o idioma (também referido como *o kernel*):

    ![O popup Create New Notebook](media/quickstarts/new-notebook-popup.png)

1. Selecione **Novo** para terminar a criação do caderno, que aparece na lista de ficheiros do seu projeto:

    ![Novo caderno que aparece na lista de ficheiros do projeto](media/quickstarts/new-notebook-created.png)

## <a name="run-the-notebook"></a>Executar o bloco de notas

1. Selecione o novo caderno para executá-lo no editor; o núcleo selecionado (Python 3.6 neste exemplo) é automaticamente ativado para este caderno:

    ![Vista de um novo caderno em Cadernos Azure](media/quickstarts/create-notebook-first-open.png)

1. Por padrão, o caderno tem uma célula de código vazia. Para alterar o tipo de célula para **Markdown,** utilize o tipo de pilha drop-down para selecionar **Markdown:**

    ![Mudar o tipo de célula num novo caderno](media/quickstarts/create-notebook-cell-type.png)

1. Introduza ou cola o seguinte texto de título na célula:

    ```markdown
    # Hello World in Python
    ```

1. Como estás a editar o Markdown, o texto aparece como um cabeçalho com o "#". Para tornar o Markdown em HTML, selecione o botão **Executar.** Os Cadernos Azure criam automaticamente uma nova célula de código depois:

    ![O botão de execução para uma célula e renderizado Markdown](media/quickstarts/run-cell-markdown-render.png)

1. Na célula de código, introduza o seguinte código Python:

    ```python
    from datetime import datetime

    now = datetime.now()
    msg = "Hello, Azure Notebooks! Today is %s"  % now.strftime("%A, %d %B, %Y")
    print(msg)
    ```

1. Selecione **Executar** (atalho do teclado: Shift+Enter) para executar o código. Abaixo da célula deve ver uma saída bem sucedida semelhante ao seguinte texto:

    ```output
    Hello, Azure Notebooks! Today is Thursday, 15 November, 2018
    ```

1. Selecione o ícone de salvamento para salvar o seu trabalho:

    ![Guarde o ícone na barra de ferramentas do portátil Jupyter](media/quickstarts/hello-results-save-icon.png)

1. Selecione o comando do menu **'Fechar e parar'** de **ficheiros** > para parar o servidor e fechar a janela do navegador.

## <a name="share-the-notebook"></a>Partilhar o caderno

Para partilhar o seu caderno, mude para a página do projeto se necessário, clique no ficheiro do portátil, selecione **Copy Link** (atalho de teclado: y) e cole esse link numa mensagem apropriada (e-mail, IM, etc.).

Na página do projeto, também pode utilizar o menu **Partilhar** para obter um link, criar uma mensagem de e-mail com o link, ou obter código de incorporação HTML e Markdown:

![Comando de partilha de projeto](media/quickstarts/share-project-command.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Crie e execute um caderno jupyter para fazer regressão linear](tutorial-create-run-jupyter-notebook.md)
