---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: dapine
ms.openlocfilehash: 327000173b4c5c378f60c76eb1a24bd155901671
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2020
ms.locfileid: "80666924"
---
Este guia mostra como instalar o [SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) de Discurso para Python. Se só quer que o nome do pacote `pip install azure-cognitiveservices-speech`seja iniciado por conta própria, corra.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Sistemas operativos suportados

- O pacote Python Speech SDK está disponível para estes sistemas operativos:
  - Janelas: x64 e x86
  - Mac: macOS X versão 10.12 ou posterior
  - Linux: Ubuntu 16.04/18.04, Debian 9, RHEL 7/8, CentOS 7/8 em x64

## <a name="prerequisites"></a>Pré-requisitos

- As plataformas Linux suportadas exigirão`libssl` certas bibliotecas instaladas `libasound2` (para suporte de camada de tomadas seguras e para suporte sonoro). Consulte a sua distribuição abaixo para os comandos necessários para instalar as versões corretas destas bibliotecas.

  - Em Ubuntu, execute os seguintes comandos para instalar as embalagens necessárias:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.0 libasound2
        ```

  - No Debian 9, execute os seguintes comandos para instalar as embalagens necessárias:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.2 libasound2
        ```

  - No RHEL/CentOS 8, execute os seguintes comandos para instalar as embalagens necessárias:

        ```sh
        sudo yum update
        sudo yum install alsa-lib openssl python3
        ```

> [!NOTE]
> No RHEL/CentOS 8, siga as instruções sobre como configurar o [OpenSSL para Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

- No Windows, é necessário o [Microsoft Visual C++ Redistribuável para o Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) para a sua plataforma. Note que a instalação desta esta sede pela primeira vez poderá exigir que reinicie o Windows antes de continuar com este guia.
- E finalmente, vai precisar de [Python 3.5 a 3.8.](https://www.python.org/downloads/) Para verificar a sua instalação, abra `python --version` um pedido de comando e escreva o comando e verifique o resultado. Se estiver instalado corretamente, terá uma resposta "Python 3.5.1" ou similar.

## <a name="install-the-speech-sdk-from-pypi"></a>Instale o SDK de Discurso da PyPI

Se estiver a usar o seu próprio ambiente ou a construir ferramentas, execute o seguinte comando para instalar o SDK de Discurso a partir do [PyPI](https://pypi.org/). Para os utilizadores do Visual Studio Code, salte para a próxima subsecção para instalação guiada.

```sh
pip install azure-cognitiveservices-speech
```

Se estiver no macOS, poderá ter de executar `pip` o seguinte comando para que o comando acima funcione:

```sh
python3 -m pip install --upgrade pip
```

Uma vez usado `pip` com sucesso `azure-cognitiveservices-speech`para instalar, pode usar o SDK de Fala importando o espaço de nome para os seus projetos Python.

```py
import azure.cognitiveservices.speech as speechsdk
```

## <a name="install-the-speech-sdk-using-visual-studio-code"></a>Instale o SDK de Fala usando o Código do Estúdio Visual

1. Descarregue e instale a versão mais recente suportada da [Python](https://www.python.org/downloads/) para a sua plataforma, de 3.5 a 3.8.
   - Os utilizadores do Windows certifiquem-se de selecionar "Adicionar Python ao seu PATH" durante o processo de instalação.
1. Descarregue e instale o Código do [Estúdio Visual.](https://code.visualstudio.com/Download)
1. Abra o Código do Estúdio Visual e instale a extensão Python. Selecione**extensões** de**preferências** > de **ficheiro** > sacar do menu. Procure **Python** e clique em **Instalar**.

   ![Instale a extensão Python](~/articles/cognitive-services/speech-service/media/sdk/qs-python-vscode-python-extension.png)

1. Também a partir do Código do Estúdio Visual, instale o pacote Speech SDK Python a partir da linha de comando integrada:
   1. Abra um terminal (a partir dos menus suspensos, **Terminal** > **Novo Terminal)**
   1. No terminal que abre, entre no comando`python -m pip install azure-cognitiveservices-speech`

Se é novo no Visual Studio Code, consulte a [documentação](https://code.visualstudio.com/docs)de código do estúdio visual mais extensa. Para mais informações sobre visual studio code e Python, consulte [visual Studio Code Python tutorial](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="support-and-updates"></a>Suporte e atualizações

As atualizações do pacote Speech SDK Python são distribuídas via PyPI e anunciadas nas notas de [lançamento.](~/articles/cognitive-services/speech-service/releasenotes.md)
Se uma nova versão estiver disponível, pode `pip install --upgrade azure-cognitiveservices-speech`atualizá-la com o comando .
Verifique qual a versão atualmente instalada `azure.cognitiveservices.speech.__version__` inspecionando a variável.

Se tiver algum problema, ou se estiver a perder uma funcionalidade, consulte [opções](~/articles/cognitive-services/speech-service/support.md)de Suporte e Ajuda .

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [windows](../quickstart-list.md)]
