---
title: 'Tutorial: Treine e implante um modelo utilizando a extensão do Código do Estúdio Visual'
titleSuffix: Azure Machine Learning
description: Saiba como treinar e implementar um modelo de classificação de imagem utilizando o TensorFlow e a extensão do código do estúdio visual de aprendizagem automática Azure
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 04/13/2020
ms.openlocfilehash: f793f8c4cb84f821c098cc5ce98e693d272e725f
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272827"
---
# <a name="train-and-deploy-an-image-classification-tensorflow-model-using-the-azure-machine-learning-visual-studio-code-extension"></a>Treine e implemente um modelo de classificação de imagem TensorFlow utilizando a extensão do código do estúdio visual de aprendizagem automática Azure

Aprenda a treinar e implementar um modelo de classificação de imagem para reconhecer números escritos à mão usando o TensorFlow e a extensão do código visual do estúdio de aprendizagem automática Azure.

Neste tutorial, irá aprender as seguintes tarefas:

> [!div class="checklist"]
> * Compreender o código
> * Criar uma área de trabalho
> * Criar uma experimentação
> * Configurar alvos de computador
> * Executar um ficheiro de configuração
> * Preparar um modelo
> * Registar um modelo
> * Implementar um modelo

## <a name="prerequisites"></a>Pré-requisitos

- Subscrição do Azure. Se não tiver uma, inscreva-se para experimentar a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).
- Instale o [Código do Estúdio Visual,](https://code.visualstudio.com/docs/setup/setup-overview)um editor de código leve e transversal.
- Extensão do Código do Estúdio visual azure Machine Learning Studio. Para instruções de instalação consulte o tutorial de extensão do código de [extensão do código de aprendizagem automática Azure](./tutorial-setup-vscode-extension.md)

## <a name="understand-the-code"></a>Compreender o código

O código para este tutorial utiliza o TensorFlow para treinar um modelo de aprendizagem automática de classificação de imagem que categoriza os dígitos manuscritos de 0-9. Fá-lo criando uma rede neural que leva os valores de pixel de 28 px x 28 px de imagem como entrada e produz uma lista de 10 probabilidades, uma para cada um dos dígitos sendo classificada. Abaixo está uma amostra do aspeto dos dados.  

![Dígitos MNIST](./media/tutorial-train-deploy-image-classification-model-vscode/digits.png)

Obtenha o código para este tutorial descarregando e desapertando as Ferramentas de Código VS para o [repositório](https://github.com/microsoft/vscode-tools-for-ai/archive/master.zip) de IA em qualquer lugar do seu computador.

## <a name="create-a-workspace"></a>Criar uma área de trabalho

A primeira coisa que tem de fazer para construir uma aplicação no Azure Machine Learning é criar um espaço de trabalho. Um espaço de trabalho contém os recursos para treinar modelos, bem como os próprios modelos treinados. Para mais informações, consulte [o que é um espaço de trabalho.](./concept-workspace.md) 

1. Na barra de atividade do Código do Estúdio Visual, selecione o ícone **Azure** para abrir a vista Azure Machine Learning.
1. Clique na subscrição do Azure e selecione **Create Workspace**. 
    
    > [!div class="mx-imgBorder"]
    > ![Criar uma área de trabalho](./media/tutorial-train-deploy-image-classification-model-vscode/create-workspace.png)

1. Por padrão, é gerado um nome contendo a data e a hora da criação. Na caixa de entrada de texto, altere o nome para "TeamWorkspace" e prima **Enter**.
1. Selecione **Criar um novo grupo de recursos**. 
1. Nomeie o seu grupo de recursos "TeamWorkspace-rg" e prima **Enter**. 
1. Escolha um local para o seu espaço de trabalho. Recomenda-se escolher um local mais próximo do local que pretende implementar o seu modelo. Por exemplo, "West US 2".
1. Quando solicitado para selecionar o tipo de espaço de trabalho, selecione **Basic** para criar um espaço de trabalho básico. Para obter mais informações sobre diferentes ofertas de espaço de trabalho, consulte a visão geral do [Azure Machine Learning.](./overview-what-is-azure-ml.md#sku)

Neste momento, é feito um pedido ao Azure para criar um novo espaço de trabalho na sua conta. Após alguns minutos, o novo espaço de trabalho aparece no seu nó de subscrição. 

## <a name="create-an-experiment"></a>Criar uma experimentação

Uma ou mais experiências podem ser criadas no seu espaço de trabalho para rastrear e analisar corridas de formação de modelos individuais. As corridas podem ser feitas na nuvem Azure ou na sua máquina local.

1. Na barra de atividade do Código do Estúdio Visual, selecione o ícone **Azure.** A vista Azure Machine Learning aparece.
1. Expanda o seu nó de subscrição.
1. Expanda o nó **TeamWorkspace.** 
1. Clique no nó das **experiências.**
1. Selecione **Criar experimento** a partir do menu de contexto.

    > [!div class="mx-imgBorder"]
    > ![Criar uma experimentação](./media/tutorial-train-deploy-image-classification-model-vscode/create-experiment.png)

1. Nomeie a sua experiência "MNIST" e prima **Enter** para criar a nova experiência. 

Tal como os espaços de trabalho, é enviado um pedido ao Azure para criar uma experiência com as configurações fornecidas. Após alguns minutos, a nova experiência aparece no nó de *Experiências* do seu espaço de trabalho. 

## <a name="configure-compute-targets"></a>Configure alvos de computação

Um alvo computacional é o recurso de computação ou ambiente onde executa scripts e implementa modelos treinados. Para mais informações, consulte a documentação dos alvos da documentação do [azure Machine Learning.](./concept-compute-target.md)

Para criar um alvo de cálculo:

1. Na barra de atividade do Código do Estúdio Visual, selecione o ícone **Azure.** A vista Azure Machine Learning aparece. 
1. Expanda o seu nó de subscrição. 
1. Expanda o nó **TeamWorkspace.** 
1. Sob o nó do espaço de trabalho, clique no nó **Compute** e escolha **Create Compute**. 

    > [!div class="mx-imgBorder"]
    > ![Criar um alvo de computação](./media/tutorial-train-deploy-image-classification-model-vscode/create-compute.png)

1. Selecione **Azure Machine Learning Compute (AmlCompute)**. A Azure Machine Learning Compute é uma infraestrutura de computação gerida que permite ao utilizador criar facilmente uma computação de um único ou multi-nó que pode ser usada com outros utilizadores no seu espaço de trabalho.
1. Selecionar o tamanho da VM. Selecione **Standard_F2s_v2** da lista de opções. O tamanho do seu VM tem um impacto na quantidade de tempo que leva para treinar os seus modelos. Para obter mais informações sobre tamanhos vm, consulte [tamanhos para máquinas virtuais Linux em Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).
1. Nomeie o seu cálculo "TeamWkspc-com" e prima **Enter** para criar o seu cálculo.

    Um ficheiro aparece no Código VS com conteúdo semelhante ao seguinte:

    ```json
    {
        "location": "westus2",
        "tags": {},
        "properties": {
            "computeType": "AmlCompute",
            "description": "",
            "properties": {
                "vmSize": "Standard_F2s_v2",
                "vmPriority": "dedicated",
                "scaleSettings": {
                    "maxNodeCount": 4,
                    "minNodeCount": 0,
                    "nodeIdleTimeBeforeScaleDown": 120
                },
                "userAccountCredentials": {
                    "adminUserName": "",
                    "adminUserPassword": "",
                    "adminUserSshPublicKey": ""
                },
                "subnetName": "",
                "vnetName": "",
                "vnetResourceGroupName": "",
                "remoteLoginPortPublicAccess": ""
            }
        }
    }
    ```

1. Quando estiver satisfeito com a configuração, abra a paleta de comando selecionando **a Paleta de Comando ver >**.
1. Introduza o seguinte comando na paleta de comando para guardar o ficheiro de configuração do seu execução.

    ```text
    Azure ML: Save and Continue
    ```

Após alguns minutos, o novo alvo computacional aparece no nó *Compute* do seu espaço de trabalho.

## <a name="create-a-run-configuration"></a>Criar uma configuração de execução

Quando submete uma corrida de treino para um alvo de computação, também submete a configuração necessária para executar o trabalho de formação. Por exemplo, o guião que contém o código de treino e as dependências python necessárias para executá-lo.

Para criar uma configuração de execução:

1. Na barra de atividade do Código do Estúdio Visual, selecione o ícone **Azure.** A vista Azure Machine Learning aparece. 
1. Expanda o seu nó de subscrição. 
1. Expanda o nó **teamWorkspace > Compute.** 
1. Sob o nó computacional, clique à direita no nó **computacional TeamWkspc-com** e escolha **Criar Configuração**de Execução .

    > [!div class="mx-imgBorder"]
    > ![Criar uma configuração de execução](./media/tutorial-train-deploy-image-classification-model-vscode/create-run-configuration.png)

1. Nomeie a sua configuração de execução "MNIST-rc" e prima **Enter** para criar a configuração do seu executor.
1. Em seguida, selecione **Criar novo Ambiente Azure ML**. Os ambientes definem as dependências necessárias para executar os seus scripts.
1. Nomeie o seu ambiente "MNIST-env" e prima **Enter**.
1. Selecione **ficheiro de dependências** de Condomínio seletiva da lista.
1. Pressione **Entrar** para navegar no ficheiro de dependências da Conda. Neste caso, o ficheiro de `env.yml` dependências `vscode-tools-for-ai/mnist-vscode-docs-sample` é o ficheiro dentro do diretório.

    Um ficheiro aparece no Código VS com conteúdo semelhante ao seguinte:

    ```json
    {
        "name": "MNIST-env",
        "version": "1",
        "python": {
            "interpreterPath": "python",
            "userManagedDependencies": false,
            "condaDependencies": {
                "name": "vs-code-azure-ml-tutorial",
                "channels": [
                    "defaults"
                ],
                "dependencies": [
                    "python=3.6.2",
                    "tensorflow=1.15.0",
                    "pip",
                    {
                        "pip": [
                            "azureml-defaults"
                        ]
                    }
                ]
            },
            "baseCondaEnvironment": null
        },
        "environmentVariables": {},
        "docker": {
            "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
            "baseDockerfile": null,
            "baseImageRegistry": {
                "address": null,
                "username": null,
                "password": null
            },
            "enabled": false,
            "arguments": []
        },
        "spark": {
            "repositories": [],
            "packages": [],
            "precachePackages": true
        },
        "inferencingStackVersion": null
    }
    ```

1. Assim que estiver satisfeito com a sua configuração, guarde-a abrindo a paleta de comando e introduzindo o seguinte comando:

    ```text
    Azure ML: Save and Continue
    ```

1. Pressione **Introduza** para navegar no ficheiro script para executar no computado. Neste caso, o guião para `train.py` treinar o `vscode-tools-for-ai/mnist-vscode-docs-sample` modelo é o ficheiro dentro do diretório.

    Um ficheiro `MNIST-rc.runconfig` chamado aparece no Código VS com conteúdo semelhante ao abaixo:

    ```json
    {
        "script": "train.py",
        "framework": "Python",
        "communicator": "None",
        "target": "TeamWkspc-com",
        "environment": {
            "name": "MNIST-env",
            "version": "1",
            "python": {
                "interpreterPath": "python",
                "userManagedDependencies": false,
                "condaDependencies": {
                    "name": "vs-code-azure-ml-tutorial",
                    "channels": [
                        "defaults"
                    ],
                    "dependencies": [
                        "python=3.6.2",
                        "tensorflow=1.15.0",
                        "pip",
                        {
                            "pip": [
                                "azureml-defaults"
                            ]
                        }
                    ]
                },
                "baseCondaEnvironment": null
            },
            "environmentVariables": {},
            "docker": {
                "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
                "baseDockerfile": null,
                "baseImageRegistry": {
                    "address": null,
                    "username": null,
                    "password": null
                },
                "enabled": false,
                "arguments": []
            },
            "spark": {
                "repositories": [],
                "packages": [],
                "precachePackages": true
            },
            "inferencingStackVersion": null
        },
        "history": {
            "outputCollection": true,
            "snapshotProject": false,
            "directoriesToWatch": [
                "logs"
            ]
        }
    }
    ```

1. Assim que estiver satisfeito com a sua configuração, guarde-a abrindo a paleta de comando e introduzindo o seguinte comando:

    ```text
    Azure ML: Save and Continue
    ```

A `MNIST-rc` configuração do executo é adicionada sob o nó `MNIST-env` computacional *TeamWkspc-com* e a configuração do ambiente é adicionada sob o nó *ambiente.*

## <a name="train-the-model"></a>Dar formação sobre o modelo

Durante o processo de treino, um modelo TensorFlow é criado através do processamento dos dados de formação e padrões de aprendizagem incorporados nele para cada um dos respetivos dígitos sendo classificado. 

Para executar uma experiência de Aprendizagem automática Azure:

1. Na barra de atividade do Código do Estúdio Visual, selecione o ícone **Azure.** A vista Azure Machine Learning aparece. 
1. Expanda o seu nó de subscrição. 
1. Expanda o nó **de experiências teamWorkspace > Experiments.** 
1. Clique na experiência **MNIST.**
1. Selecione **Executar Experiência**.

    > [!div class="mx-imgBorder"]
    > ![Fazer uma experiência](./media/tutorial-train-deploy-image-classification-model-vscode/run-experiment.png)

1. A partir da lista de opções-alvo computacionais, selecione o alvo computacional **TeamWkspc-com.**
1. Em seguida, selecione a configuração de execução **MNIST-rc.**
1. Neste ponto, um pedido é enviado ao Azure para executar a sua experiência no alvo de computação selecionado no seu espaço de trabalho. Este processo demora vários minutos. A quantidade de tempo para executar o trabalho de formação é impactada por vários fatores como o tipo de computação e o tamanho dos dados de formação. Para acompanhar o progresso da sua experiência, clique no nó de execução atual e selecione **View Run no portal Azure**.
1. Quando aparecer o diálogo a pedir para abrir um website externo, selecione **Open**.

    > [!div class="mx-imgBorder"]
    > ![Acompanhar o progresso da experiência](./media/tutorial-train-deploy-image-classification-model-vscode/track-experiment-progress.png)

Quando o modelo terminar o treino, o rótulo de estado ao lado do nó de execução atualiza para "Completed".

## <a name="register-the-model"></a>Registar o modelo

Agora que treinou o seu modelo, pode registá-lo no seu espaço de trabalho. 

Para registar o seu modelo:

1. Na barra de atividade do Código do Estúdio Visual, selecione o ícone **Azure.** A vista Azure Machine Learning aparece.
1. Expanda o seu nó de subscrição. 
1. Expanda o **TeamWorkspace > Experiments > nó MNIST.**
1. Obtenha as saídas do modelo geradas a partir do treino do modelo. Clique à direita no nó **run 1** e selecione **saídas de download**. 

    > [!div class="mx-imgBorder"]
    > ![Descarregar saídas de modelos](./media/tutorial-train-deploy-image-classification-model-vscode/download-outputs.png)

1. Escolha o diretório para guardar as saídas descarregadas para. Por padrão, as saídas são colocadas no diretório atualmente aberto no Código do Estúdio Visual.
1. Clique no nó dos **Modelos** e escolha o **Modelo de Registo**.

    > [!div class="mx-imgBorder"]
    > ![Registar um modelo](./media/tutorial-train-deploy-image-classification-model-vscode/register-model.png)

1. Nomeie o seu modelo "MNIST-TensorFlow-model" e prima **Enter**.
1. Um modelo TensorFlow é composto por vários ficheiros. Selecione **a pasta Modelo** como formato de caminho do modelo a partir da lista de opções. 
1. Selecione o `azureml_outputs/Run_1/outputs/outputs/model` diretório.

    Um ficheiro que contém as configurações do seu modelo aparece no Código do Estúdio Visual com conteúdo semelhante ao abaixo:

    ```json
    {
        "modelName": "MNIST-TensorFlow-model",
        "tags": {
            "": ""
        },
        "modelPath": "c:\\Dev\\vscode-tools-for-ai\\mnist-vscode-docs-sample\\azureml_outputs\\Run_1\\outputs\\outputs\\model",
        "description": ""
    }
    ```

1. Assim que estiver satisfeito com a sua configuração, guarde-a abrindo a paleta de comando e introduzindo o seguinte comando:

    ```text
    Azure ML: Save and Continue
    ```

Após alguns minutos, o modelo aparece sob o nó *models.*

## <a name="deploy-the-model"></a>Implementar o modelo

No Visual Studio Code, pode implementar o seu modelo como um serviço web para:

+ Instâncias de contentores azure (ACI).
+ Serviço Azure Kubernetes (AKS).

Não é necessário criar um recipiente ACI para testar com antecedência, porque os recipientes ACI são criados conforme necessário. No entanto, precisa de configurar os clusters AKS com antecedência. Para obter mais informações sobre as opções de implementação, consulte [modelos de implementação com o Azure Machine Learning](how-to-deploy-and-where.md) .

Para implantar um serviço web como Um ACI:

1. Na barra de atividade do Código do Estúdio Visual, selecione o ícone **Azure.** A vista Azure Machine Learning aparece.
1. Expanda o seu nó de subscrição. 
1. Expandir o nó **teamWorkspace > Models.** 
1. Clique à direita no **modelo MNIST-TensorFlow** e selecione **Serviço de Implantação do Modelo Registado**.

    > [!div class="mx-imgBorder"]
    > ![Implementar o modelo](./media/tutorial-train-deploy-image-classification-model-vscode/deploy-model.png)

1. Selecione Instâncias de **contentores Azure**.
1. Nomeie o seu serviço "mnist-tensorflow-svc" e prima **Enter**.
1. Escolha o script para ser executado no recipiente pressionando **Enter** `score.py` na caixa `mnist-vscode-docs-sample` de entrada e navegando para o ficheiro no diretório.
1. Forneça as dependências necessárias para **Enter** executar o script pressionando Enter `env.yml` na `mnist-vscode-docs-sample` caixa de entrada e navegando para o ficheiro no diretório.

    Um ficheiro que contém as configurações do seu modelo aparece no Código do Estúdio Visual com conteúdo semelhante ao abaixo:

    ```json
    {
        "name": "mnist-tensorflow-svc",
        "imageConfig": {
            "runtime": "python",
            "executionScript": "score.py",
            "dockerFile": null,
            "condaFile": "env.yml",
            "dependencies": [],
            "schemaFile": null,
            "enableGpu": false,
            "description": ""
        },
        "deploymentConfig": {
            "cpu_cores": 1,
            "memory_gb": 10,
            "tags": {
                "": ""
            },
            "description": ""
        },
        "deploymentType": "ACI",
        "modelIds": [
            "MNIST-TensorFlow-model:1"
        ]
    }
    ```

1. Assim que estiver satisfeito com a sua configuração, guarde-a abrindo a paleta de comando e introduzindo o seguinte comando:

    ```text
    Azure ML: Save and Continue
    ```

Neste momento, é enviado um pedido ao Azure para implementar o seu serviço web. Este processo demora vários minutos. Uma vez implementado, o novo serviço aparece sob o nó *Endpoints.*

## <a name="next-steps"></a>Passos seguintes

* Para uma caminhada de como treinar com Azure Machine Learning fora do Visual Studio Code, consulte [Tutorial: Modelos de comboio com Machine Learning Azure.](tutorial-train-models-with-aml.md)
* Para uma passagem de como editar, correr e depurar código localmente, consulte o [tutorial python olá-mundo.](https://code.visualstudio.com/docs/Python/Python-tutorial)

