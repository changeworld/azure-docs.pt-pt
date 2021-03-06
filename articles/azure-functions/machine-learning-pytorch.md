---
title: Implementar um modelo PyTorch como uma aplicação Funções Azure
description: Utilize uma rede neural profunda ResNet 18 pré-treinada do PyTorch com funções Azure para atribuir 1 de 1000 etiquetas ImageNet a uma imagem.
author: gvashishtha
ms.topic: tutorial
ms.date: 02/28/2020
ms.author: gopalv
ms.openlocfilehash: 17acb7e351d5f1c009a6a8a14717e987fae3e895
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "78379900"
---
# <a name="tutorial-deploy-a-pre-trained-image-classification-model-to-azure-functions-with-pytorch"></a>Tutorial: Implemente um modelo de classificação de imagem pré-treinado para funções azure com pyTorch

Neste artigo, aprende-se a utilizar funções Python, PyTorch e Azure para carregar um modelo pré-treinado para classificar uma imagem com base no seu conteúdo. Como todos trabalham localmente e não criam recursos Azure na nuvem, não há qualquer custo para completar este tutorial.

> [!div class="checklist"]
> * Inicialize um ambiente local para o desenvolvimento de Funções Azure em Python.
> * Importe um modelo de aprendizagem automática PyTorch pré-treinado para uma aplicação de função.
> * Construa um HTTP API sem servidor para classificar uma imagem como uma das 1000 [classes](https://gist.github.com/yrevar/942d3a0ac09ec9e5eb3a)ImageNet .
> * Consumir a API a partir de uma aplicação web.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Python 3.7.4 ou superior](https://www.python.org/downloads/release/python-374/). (Python 3.8.x e Python 3.6.x também são verificados com funções Azure.)
- As [ferramentas nucleares das funções azure](functions-run-local.md#install-the-azure-functions-core-tools)
- Um editor de código como [Visual Studio Code](https://code.visualstudio.com/)

### <a name="prerequisite-check"></a>Verificação pré-requisito

1. Numa janela de terminal `func --version` ou comando, corra para verificar se as Ferramentas Core funções Do Azure são a versão 2.7.1846 ou posterior.
1. Executar `python --version` (Linux/MacOS) `py --version` ou (Windows) para verificar os relatórios da versão Python 3.7.x.

## <a name="clone-the-tutorial-repository"></a>Clone o repositório tutorial

1. Numa janela de terminais ou comando, clone o seguinte repositório utilizando Git:

    ```
    git clone https://github.com/Azure-Samples/functions-python-pytorch-tutorial.git
    ```

1. Navegue na pasta e examine o seu conteúdo.

    ```
    cd functions-python-pytorch-tutorial
    ```

    - *começar* é a sua pasta de trabalho para o tutorial.
    - *final* é o resultado final e implementação completa para a sua referência.
    - *recursos* contém o modelo de aprendizagem automática e bibliotecas auxiliares.
    - *frontend* é um site que chama a app de função.

## <a name="create-and-activate-a-python-virtual-environment"></a>Criar e ativar um ambiente virtual Python

Navegue até *à* pasta inicial e execute os seguintes comandos para criar e ativar um ambiente virtual chamado `.venv`.


# <a name="bash"></a>[bash](#tab/bash)

```bash
cd start
python -m venv .venv
source .venv/bin/activate
```

Se python não instalou o pacote de veado na sua distribuição Linux, execute o seguinte comando:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
cd start
py -m venv .venv
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
cd start
py -m venv .venv
.venv\scripts\activate
```

---

Executa todos os comandos subsequentes neste ambiente virtual ativado. (Para sair do ambiente `deactivate`virtual, corra .)


## <a name="create-a-local-functions-project"></a>Criar um projeto de funções locais

Nas Funções Azure, um projeto de função é um recipiente para uma ou mais funções individuais que cada um responde a um gatilho específico. Todas as funções de um projeto partilham as mesmas configurações locais e de hospedagem. Nesta secção, cria-se um projeto de função `classify` que contém uma única função de placa de caldeira chamada que fornece um ponto final HTTP. Adicione código mais específico numa secção posterior.

1. Na pasta *inicial,* utilize as Ferramentas Core funções do Azure para inicializar uma aplicação de função Python:

    ```
    func init --worker-runtime python
    ```

    Após a inicialização, a pasta de *início* contém vários ficheiros para o projeto, incluindo ficheiros de configurações chamados [local.settings.json](functions-run-local.md#local-settings-file) e [host.json](functions-host-json.md). Como *as configurações locais.json* podem conter segredos descarregados do Azure, o ficheiro é excluído do controlo de origem por padrão no ficheiro *.gitignore.*

    > [!TIP]
    > Como um projeto de função está ligado a um tempo de execução específico, todas as funções do projeto devem ser escritas com a mesma língua.

1. Adicione uma função ao seu projeto utilizando `--name` o seguinte comando, onde `--template` o argumento é o nome único da sua função e o argumento especifica o gatilho da função. `func new`criar uma subpasta que contenha um ficheiro de código adequado ao idioma escolhido do projeto e um ficheiro de configuração denominado *função.json*.

    ```
    func new --name classify --template "HTTP trigger"
    ```

    Este comando cria uma pasta que corresponde ao nome da função, *classifica.* Nessa pasta encontram-se dois ficheiros: * \_ \_init.py\_\_*, que contém o código de função, e *função.json*, que descreve o gatilho da função e as suas encadernações de entrada e saída. Para obter informações sobre o conteúdo destes ficheiros, consulte [Examinar o conteúdo do ficheiro](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-python#optional-examine-the-file-contents) no arranque rápido da Python.


## <a name="run-the-function-locally"></a>Executar localmente a função

1. Inicie a função iniciando o hospedeiro de funcionamento das Funções Azure locais na pasta de *início:*

    ```
    func start
    ```

1. Assim que `classify` vir o ponto final aparecer na ```http://localhost:7071/api/classify?name=Azure```saída, navegue para o URL, . A mensagem "Olá Azure!" deve aparecer na saída.

1. Use **ctrl**-**C** para parar o hospedeiro.


## <a name="import-the-pytorch-model-and-add-helper-code"></a>Importar o modelo PyTorch e adicionar código de ajuda

Para modificar `classify` a função para classificar uma imagem com base no seu conteúdo, utiliza um modelo [ResNet](https://arxiv.org/abs/1512.03385) pré-treinado. O modelo pré-treinado, que vem do [PyTorch,](https://pytorch.org/hub/pytorch_vision_resnet/)classifica uma imagem em 1 de 1000 [classes ImageNet](https://gist.github.com/yrevar/942d3a0ac09ec9e5eb3a). Em seguida, adicione um pouco de código de ajuda e dependências ao seu projeto.

1. Na pasta *inicial,* execute o seguinte comando para copiar o código de previsão e etiquetas na pasta *de classificação.*

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    cp ../resources/predict.py classify
    cp ../resources/labels.txt classify
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    copy ..\resources\predict.py classify
    copy ..\resources\labels.txt classify
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    copy ..\resources\predict.py classify
    copy ..\resources\labels.txt classify
    ```

    ---

1. Verifique se a pasta *de classificação* contém ficheiros denominados *predict.py* e *labels.txt*. Caso contrário, verifique se executou o comando na pasta de *início.*

1. Abra *o início/requisitos.txt* num editor de texto e adicione as dependências exigidas pelo código auxiliar, que deve parecer o seguinte:

    ```txt
    azure-functions
    requests
    numpy==1.15.4
    https://download.pytorch.org/whl/cpu/torch-1.4.0%2Bcpu-cp36-cp36m-win_amd64.whl; sys_platform == 'win32' and python_version == '3.6'
    https://download.pytorch.org/whl/cpu/torch-1.4.0%2Bcpu-cp36-cp36m-linux_x86_64.whl; sys_platform == 'linux' and python_version == '3.6'
    https://download.pytorch.org/whl/cpu/torch-1.4.0%2Bcpu-cp37-cp37m-win_amd64.whl; sys_platform == 'win32' and python_version == '3.7'
    https://download.pytorch.org/whl/cpu/torch-1.4.0%2Bcpu-cp37-cp37m-linux_x86_64.whl; sys_platform == 'linux' and python_version == '3.7'
    https://download.pytorch.org/whl/cpu/torch-1.4.0%2Bcpu-cp38-cp38-win_amd64.whl; sys_platform == 'win32' and python_version == '3.8'
    https://download.pytorch.org/whl/cpu/torch-1.4.0%2Bcpu-cp38-cp38-linux_x86_64.whl; sys_platform == 'linux' and python_version == '3.8'
    torchvision==0.5.0
    ```

1. Guarde *os requisitos.txt*e, em seguida, executar o seguinte comando a partir da pasta de *início* para instalar as dependências.


    ```
    pip install --no-cache-dir -r requirements.txt
    ```

A instalação pode demorar alguns minutos, durante o qual pode proceder com a modificação da função na secção seguinte.
> [!TIP]
> >No Windows, pode encontrar o erro: "Não foi possível instalar pacotes devido a um EnvironmentError: [Errno 2] Nenhum ficheiro ou diretório:" seguido de um longo nome de caminho para um ficheiro como *sharded_mutable_dense_hashtable.cpython-37.pyc*. Normalmente, este erro acontece porque a profundidade do caminho da pasta torna-se demasiado longa. Neste caso, detete `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\FileSystem@LongPathsEnabled` a `1` chave de registo para permitir longos caminhos. Alternadamente, verifique onde está instalado o seu intérprete Python. Se esse local tiver um longo caminho, tente reinstalar-se numa pasta com um caminho mais curto.

## <a name="update-the-function-to-run-predictions"></a>Atualizar a função para executar previsões

1. Abra *classificar/init\_\_\_\_.py* em um editor de texto `import` e adicionar as seguintes linhas após as declarações existentes para importar a biblioteca padrão JSON e os ajudantes *previstos:*

    :::code language="python" source="~/functions-pytorch/end/classify/__init__.py" range="1-6" highlight="5-6":::

1. Substitua todo o `main` conteúdo da função pelo seguinte código:

    :::code language="python" source="~/functions-pytorch/end/classify/__init__.py" range="8-19":::

    Esta função recebe um URL de imagem `img`num parâmetro de corda de consulta chamado . Em seguida, liga `predict_image_from_url` da biblioteca de ajudantes para descarregar e classificar a imagem usando o modelo PyTorch. A função devolve então uma resposta HTTP com os resultados.

    > [!IMPORTANT]
    > Como este ponto final http é chamado por uma página web `Access-Control-Allow-Origin` hospedada em outro domínio, a resposta inclui um cabeçalho para satisfazer os requisitos de Partilha de Recursos De Origem Cruzada (CORS) do navegador.
    >
    > Numa aplicação de `*` produção, mude para a origem específica da página web para maior segurança.

1. Guarde as suas alterações, assumindo então que as `func start`dependências terminaram de instalar, inicie novamente o hospedeiro de funções local com . Certifique-se de que executa o hospedeiro na *pasta* inicial com o ambiente virtual ativado. Caso contrário, o hospedeiro começará, mas verá erros ao invocar a função.

    ```
    func start
    ```

1. Num browser, abra o seguinte URL para invocar a função com o URL de uma imagem bernese mountain dog e confirmar que o JSON devolvido classifica a imagem como um Cão de Montanha Bernês.

    ```
    http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/Bernese-Mountain-Dog-Temperament-long.jpg
    ```

1. Mantenha o hospedeiro a funcionar porque o usa no próximo passo.

### <a name="run-the-local-web-app-front-end-to-test-the-function"></a>Executar a extremidade frontal da aplicação web local para testar a função

Para testar a invocação do ponto final da função a partir de outra aplicação web, há uma aplicação simples na pasta *frontal* do repositório.

1. Abra um novo terminal ou comando e ative o ambiente virtual (como descrito anteriormente em [Create e ative um ambiente virtual Python).](#create-and-activate-a-python-virtual-environment)

1. Navegue para a pasta *frontal* do repositório.

1. Inicie um servidor HTTP com Python:

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    python -m http.server
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    py -m http.server
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    py -m http.server
    ```

1. Num browser, navegue para, `localhost:8000`em seguida, introduzir uma das seguintes URLs fotográficas na caixa de texto, ou utilizar o URL de qualquer imagem acessível ao público.

    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/Bernese-Mountain-Dog-Temperament-long.jpg`
    - `https://github.com/Azure-Samples/functions-python-pytorch-tutorial/blob/master/resources/assets/bald-eagle.jpg?raw=true`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/penguin.jpg`

1. Selecione **Submeter** para invocar o ponto final da função para classificar a imagem.

    ![Screenshot do projeto acabado](media/machine-learning-pytorch/screenshot.png)

    Se o navegador reportar um erro ao submeter o URL de imagem, verifique o terminal em que está a executar a aplicação de funções. Se vir um erro como "Nenhum módulo encontrado 'PIL'", pode ter iniciado a aplicação de funções na *pasta* inicial sem antes ativar o ambiente virtual que criou anteriormente. Se ainda vir erros, volte a correr `pip install -r requirements.txt` com o ambiente virtual ativado e procure erros.

## <a name="clean-up-resources"></a>Limpar recursos

Como todo este tutorial funciona localmente na sua máquina, não existem recursos ou serviços Azure para limpar.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a construir e personalizar um ponto final http API com funções Azure para classificar imagens usando um modelo PyTorch. Também aprendeu a chamar a API de uma aplicação web. Pode utilizar as técnicas deste tutorial para construir APIs de qualquer complexidade, tudo enquanto executa o modelo de computação sem servidor fornecido pela Azure Functions.

Veja também:

- [Implemente a função para Azure utilizando o Código do Estúdio Visual](https://code.visualstudio.com/docs/python/tutorial-azure-functions).
- [Guia de desenvolvimento de funções azure Python](./functions-reference-python.md)


> [!div class="nextstepaction"]
> [Implementar a função para funções Azure utilizando o Guia CLI Azure](./functions-run-local.md#publish)
