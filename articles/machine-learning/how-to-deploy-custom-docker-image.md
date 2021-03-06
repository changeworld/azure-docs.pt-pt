---
title: Implementar modelos com imagem personalizada do Docker
titleSuffix: Azure Machine Learning
description: Aprenda a usar uma imagem de base personalizada do Docker ao implementar os seus modelos De Aprendizagem automática Azure. Enquanto o Azure Machine Learning fornece uma imagem de base padrão para si, também pode usar a sua própria imagem base.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 03/16/2020
ms.openlocfilehash: 1f11d6667c22990b3cba2079959bec6f413d5951
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80296938"
---
# <a name="deploy-a-model-using-a-custom-docker-base-image"></a>Implementar um modelo usando uma imagem de base personalizada do Docker
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Aprenda a usar uma imagem de base personalizada do Docker ao implementar modelos treinados com o Azure Machine Learning.

Quando implementa um modelo treinado para um serviço web ou dispositivo IoT Edge, é criado um pacote que contém um servidor web para lidar com pedidos de entrada.

O Azure Machine Learning fornece uma imagem de base padrão do Docker para que não tenha supor que não tenha que se preocupar em criar uma. Também pode utilizar __ambientes__ de Aprendizagem automática Azure para selecionar uma imagem de base específica, ou usar uma imagem personalizada que fornece.

Uma imagem base é usada como ponto de partida quando uma imagem é criada para uma implementação. Fornece o sistema operativo e os componentes subjacentes. O processo de implantação adiciona então componentes adicionais, como o seu modelo, ambiente conda e outros ativos, à imagem antes de o implementar.

Normalmente, cria-se uma imagem de base personalizada quando pretende utilizar o Docker para gerir as suas dependências, manter um controlo mais apertado sobre as versões dos componentes ou economizar tempo durante a implementação. Por exemplo, você pode querer padronizar numa versão específica de Python, Conda ou outro componente. Também pode querer instalar software exigido pelo seu modelo, onde o processo de instalação demora muito tempo. Instalar o software ao criar a imagem base significa que não tem de o instalar para cada implementação.

> [!IMPORTANT]
> Quando implementa um modelo, não pode substituir componentes do núcleo, tais como o servidor web ou os componentes IoT Edge. Estes componentes proporcionam um ambiente de trabalho conhecido que é testado e suportado pela Microsoft.

> [!WARNING]
> A Microsoft pode não ser capaz de ajudar a resolver problemas causados por uma imagem personalizada. Se encontrar problemas, poderá ser-lhe pedido que utilize a imagem predefinida ou uma das imagens que a Microsoft fornece para ver se o problema é específico da sua imagem.

Este documento é dividido em duas secções:

* Criar uma imagem de base personalizada: Fornece informações aos administradores e DevOps sobre a criação de uma imagem personalizada e a autenticação configurante para um Registo de Contentores Azure utilizando o CLI de Aprendizagem Automática Azure e Machine Learning.
* Implemente um modelo utilizando uma imagem de base personalizada: Fornece informações aos Cientistas de Dados e DevOps / ML Engineers sobre a utilização de imagens personalizadas ao implementar um modelo treinado a partir do Python SDK ou ML CLI.

## <a name="prerequisites"></a>Pré-requisitos

* Um grupo de trabalho azure machine learning. Para mais informações, consulte o artigo [Criar um espaço de trabalho.](how-to-manage-workspace.md)
* O [SDK de Aprendizagem automática Azure.](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) 
* O [Azure CLI.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* A [extensão CLI para Aprendizagem automática Azure](reference-azure-machine-learning-cli.md).
* Um registo de [contentores Azure](/azure/container-registry) ou outro registo de Docker que esteja acessível na internet.
* Os passos deste documento assumem que está familiarizado com a criação e utilização de um objeto de configuração de __inferência__ como parte da implementação do modelo. Para mais informações, consulte a secção "prepare-se para implantar" de [Onde implantar e como](how-to-deploy-and-where.md#prepare-to-deploy).

## <a name="create-a-custom-base-image"></a>Criar uma imagem de base personalizada

As informações nesta secção pressupõem que está a usar um Registo de Contentores Azure para armazenar imagens do Docker. Utilize a seguinte lista de verificação quando planeia criar imagens personalizadas para o Azure Machine Learning:

* Utilizará o Registo de Contentores Azure criado para o espaço de trabalho azure machine learning, ou um registo autónomo de contentores Azure?

    Ao utilizar imagens armazenadas no registo do contentor para o espaço de __trabalho,__ não precisa de autenticar o registo. A autenticação é manuseada pelo espaço de trabalho.

    > [!WARNING]
    > O Registo de Contentores Azure para o seu espaço de trabalho é __criado na primeira vez que treina ou desdobra um modelo__ utilizando o espaço de trabalho. Se criou um novo espaço de trabalho, mas não treinou ou criou um modelo, não existirá nenhum Registo de Contentores Azure para o espaço de trabalho.

    Para obter informações sobre a recuperação do nome do Registo de Contentores Azure para o seu espaço de trabalho, consulte a secção de nome do registo de [contentores Get](#getname) deste artigo.

    Ao utilizar imagens armazenadas num __registo de contentores autónomos,__ terá de configurar um diretor de serviço que tenha pelo menos lido o acesso. Em seguida, forneça o ID principal do serviço (nome de utilizador) e a palavra-passe a quem utilizar imagens do registo. A exceção é se tornar o registo de contentores acessível ao público.

    Para obter informações sobre a criação de um registo privado de contentores Azure, consulte [Criar um registo de contentores privados](/azure/container-registry/container-registry-get-started-azure-cli).

    Para obter informações sobre a utilização de diretores de serviço com registo de contentores Azure, consulte a autenticação do Registo de [Contentores Azure com os diretores de serviço](/azure/container-registry/container-registry-auth-service-principal).

* Registo de contentores azure e informações de imagem: Forneça o nome da imagem a quem precisa de o utilizar. Por exemplo, uma `myimage`imagem chamada , armazenada `myregistry`num registo `myregistry.azurecr.io/myimage` chamado , é referenciada como quando se utiliza a imagem para a implementação do modelo

* Requisitos de imagem: O Azure Machine Learning apenas suporta imagens do Docker que fornecem o seguinte software:

    * Ubuntu 16.04 ou maior.
    * Condomínio 4.5.# ou maior.
    * Python 3.5.# ou 3.6.#.

<a id="getname"></a>

### <a name="get-container-registry-information"></a>Obtenha informações sobre o registo de contentores

Nesta secção, aprenda a obter o nome do Registo de Contentores Azure para o seu espaço de trabalho azure machine learning.

> [!WARNING]
> O Registo de Contentores Azure para o seu espaço de trabalho é __criado na primeira vez que treina ou desdobra um modelo__ utilizando o espaço de trabalho. Se criou um novo espaço de trabalho, mas não treinou ou criou um modelo, não existirá nenhum Registo de Contentores Azure para o espaço de trabalho.

Se já treinou ou implementou modelos utilizando o Azure Machine Learning, foi criado um registo de contentores para o seu espaço de trabalho. Para encontrar o nome deste registo de contentores, utilize os seguintes passos:

1. Abra uma nova concha ou comando-pronta e use o seguinte comando para autenticar a sua assinatura Azure:

    ```azurecli-interactive
    az login
    ```

    Siga as instruções para autenticar a subscrição.

    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

2. Utilize o seguinte comando para listar o registo do contentor para o espaço de trabalho. Substitua-o `<myworkspace>` pelo nome do espaço de trabalho Azure Machine Learning. Substitua-o `<resourcegroup>` pelo grupo de recursos Azure que contém o seu espaço de trabalho:

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

    As informações devolvidas são semelhantes ao seguinte texto:

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    O `<registry_name>` valor é o nome do Registo de Contentores Azure para o seu espaço de trabalho.

### <a name="build-a-custom-base-image"></a>Construa uma imagem de base personalizada

Os passos nesta secção walk-through criando uma imagem personalizada do Docker no seu Registo de Contentores Azure.

1. Crie um novo `Dockerfile`ficheiro de texto chamado , e utilize o seguinte texto como conteúdo:

    ```text
    FROM ubuntu:16.04

    ARG CONDA_VERSION=4.5.12
    ARG PYTHON_VERSION=3.6

    ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
    ENV PATH /opt/miniconda/bin:$PATH

    RUN apt-get update --fix-missing && \
        apt-get install -y wget bzip2 && \
        apt-get clean && \
        rm -rf /var/lib/apt/lists/*

    RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-x86_64.sh -O ~/miniconda.sh && \
        /bin/bash ~/miniconda.sh -b -p /opt/miniconda && \
        rm ~/miniconda.sh && \
        /opt/miniconda/bin/conda clean -tipsy

    RUN conda install -y conda=${CONDA_VERSION} python=${PYTHON_VERSION} && \
        conda clean -aqy && \
        rm -rf /opt/miniconda/pkgs && \
        find / -type d -name __pycache__ -prune -exec rm -rf {} \;
    ```

2. A partir de um reservatório ou de um pedido de comando, utilize o seguinte para autenticar o Registo de Contentores Azure. Substitua `<registry_name>` o nome do registo de contentores onde pretende armazenar a imagem em:

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. Para carregar o Dockerfile, e construí-lo, use o seguinte comando. Substitua `<registry_name>` pelo nome do registo de contentores que pretende armazenar a imagem em:

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    > [!TIP]
    > Neste exemplo, uma `:v1` etiqueta é aplicada à imagem. Se não for fornecida nenhuma `:latest` etiqueta, é aplicada uma etiqueta de.

    Durante o processo de construção, a informação é transmitida para trás para a linha de comando. Se a construção for bem sucedida, recebe uma mensagem semelhante ao seguinte texto:

    ```text
    Run ID: cda was successful after 2m56s
    ```

Para obter mais informações sobre imagens de construção com um registo de contentores Azure, consulte Construir e executar uma imagem de contentor usando tarefas de registo de [contentores Azure](https://docs.microsoft.com/azure/container-registry/container-registry-quickstart-task-cli)

Para obter mais informações sobre o upload de imagens existentes para um Registo de Contentores Azure, consulte [Empurre a sua primeira imagem para um registo privado](/azure/container-registry/container-registry-get-started-docker-cli)de contentores Docker .

## <a name="use-a-custom-base-image"></a>Use uma imagem de base personalizada

Para utilizar uma imagem personalizada, necessita das seguintes informações:

* O nome da __imagem.__ Por exemplo, `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` é o caminho para uma Imagem De Docker básica fornecida pela Microsoft.

    > [!IMPORTANT]
    > Para imagens personalizadas que criou, não se esqueça de incluir todas as etiquetas que foram usadas com a imagem. Por exemplo, se a sua imagem foi `:v1`criada com uma etiqueta específica, como . Se não usou uma etiqueta específica na criação `:latest` da imagem, foi aplicada uma etiqueta de.

* Se a imagem estiver num __repositório privado,__ necessita das seguintes informações:

    * O __endereço__do registo. Por exemplo, `myregistry.azureecr.io`.
    * Um nome de __utilizador__ principal de serviço e __senha__ que tenha lido o acesso ao registo.

    Se não tiver estas informações, fale com o administrador do Registo de Contentores Azure que contenha a sua imagem.

### <a name="publicly-available-base-images"></a>Imagens base disponíveis ao público

A Microsoft fornece várias imagens de estivador num repositório acessível ao público, que pode ser usado com os passos nesta secção:

| Imagem | Descrição |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Imagem básica para Aprendizagem automática Azure |
| `mcr.microsoft.com/azureml/onnxruntime:latest` | Contém tempo de execução ONNX para inferência de CPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-cuda` | Contém o tempo de execução onNX e CUDA para GPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-tensorrt` | Contém tempo de execução ONNX e TensorRT para GPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-vadm ` | Contém tempo de execução ONNX<sup> </sup> e OpenVINO para design acelerador de visão intel baseado em Movidius<sup>TM</sup> MyriadX VPUs |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-myriad` | Contém onNX Runtime e OpenVINO para intel<sup> </sup> Movidius<sup>TM</sup> USB sticks |

Para obter mais informações sobre as imagens base onNX Runtime, consulte a [secção de estivador ONNX Runtime](https://github.com/microsoft/onnxruntime/blob/master/dockerfiles/README.md) no repo GitHub.

> [!TIP]
> Uma vez que estas imagens estão disponíveis ao público, não precisa de fornecer um endereço, nome de utilizador ou palavra-passe ao utilizá-las.

Para mais informações, consulte [os recipientes de Aprendizagem automática Azure](https://github.com/Azure/AzureML-Containers).

> [!TIP]
>__Se o seu modelo for treinado na Azure Machine Learning Compute__, utilizando __a versão 1.0.22 ou superior__ do Azure Machine Learning SDK, é criada uma imagem durante o treino. Para descobrir o nome desta `run.properties["AzureML.DerivedImageName"]`imagem, use. O exemplo que se segue demonstra como utilizar esta imagem:
>
> ```python
> # Use an image built during training with SDK 1.0.22 or greater
> image_config.base_image = run.properties["AzureML.DerivedImageName"]
> ```

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Use uma imagem com o Azure Machine Learning SDK

Para utilizar uma imagem armazenada no Registo de **Contentores Azure para**o seu espaço de trabalho, ou um registo de **contentores acessível ao público,** definir os seguintes atributos [ambiente:](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)

+ `docker.enabled=True`
+ `docker.base_image`: Definir para o registo e caminho para a imagem.

```python
from azureml.core.environment import Environment
# Create the environment
myenv = Environment(name="myenv")
# Enable Docker and reference an image
myenv.docker.enabled = True
myenv.docker.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"
```

Para utilizar uma imagem de um __registo de contentores privados__ que não esteja no seu espaço de trabalho, deve utilizar `docker.base_image_registry` para especificar o endereço do repositório e um nome de utilizador e senha:

```python
# Set the container registry information
myenv.docker.base_image_registry.address = "myregistry.azurecr.io"
myenv.docker.base_image_registry.username = "username"
myenv.docker.base_image_registry.password = "password"

myenv.inferencing_stack_version = "latest"  # This will install the inference specific apt packages.

# Define the packages needed by the model and scripts
from azureml.core.conda_dependencies import CondaDependencies
conda_dep = CondaDependencies()
# you must list azureml-defaults as a pip dependency
conda_dep.add_pip_package("azureml-defaults")
myenv.python.conda_dependencies=conda_dep
```

Deve adicionar incumprimentos em azul com versão >= 1,0,45 como dependência do pip. Este pacote contém a funcionalidade necessária para hospedar o modelo como um serviço web. Deve também definir inferencing_stack_version imóvel no ambiente para "mais recente", esta instalação de pacotes aptos específicos necessários pelo serviço web. 

Depois de definir o ambiente, use-o com um objeto [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) para definir o ambiente de inferência em que o modelo e o serviço web funcionarão.

```python
from azureml.core.model import InferenceConfig
# Use environment in InferenceConfig
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

Neste momento, pode continuar com a implantação. Por exemplo, o seguinte código seria implantado um serviço web localmente utilizando a configuração de inferência e a imagem personalizada:

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Para obter mais informações sobre a implementação, consulte [modelos de implantação com o Azure Machine Learning](how-to-deploy-and-where.md).

Para obter mais informações sobre a personalização do seu ambiente Python, consulte [Criar e gerir ambientes para treino e implantação.](how-to-use-environments.md) 

### <a name="use-an-image-with-the-machine-learning-cli"></a>Use uma imagem com o CLI de Aprendizagem automática

> [!IMPORTANT]
> Atualmente, o CLI de Aprendizagem automática pode utilizar imagens do Registo de Contentores Azure para o seu espaço de trabalho ou repositórios acessíveis ao público. Não pode usar imagens de registos privados autónomos.

Antes de implementar um modelo utilizando o CLI de Aprendizagem automática, crie um [ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) que utilize a imagem personalizada. Em seguida, crie um ficheiro de configuração de inferência que referencia o ambiente. Também pode definir o ambiente diretamente no ficheiro de configuração de inferência. O seguinte documento da JSON demonstra como fazer referência a uma imagem num registo de contentores públicos. Neste exemplo, o ambiente é definido inline:

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda",
            "enabled": false,
            "sharedVolumes": true,
            "shmSize": null
        },
        "environmentVariables": {
            "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
        },
        "name": "my-deploy-env",
        "python": {
            "baseCondaEnvironment": null,
            "condaDependencies": {
                "channels": [
                    "conda-forge"
                ],
                "dependencies": [
                    "python=3.6.2",
                    {
                        "pip": [
                            "azureml-defaults",
                            "azureml-telemetry",
                            "scikit-learn",
                            "inference-schema[numpy-support]"
                        ]
                    }
                ],
                "name": "project_environment"
            },
            "condaDependenciesFile": null,
            "interpreterPath": "python",
            "userManagedDependencies": false
        },
        "version": "1"
    }
}
```

Este ficheiro é `az ml model deploy` utilizado com o comando. O `--ic` parâmetro é utilizado para especificar o ficheiro de configuração de inferência.

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

Para obter mais informações sobre a implementação de um modelo utilizando o ML CLI, consulte a secção "registo, perfis e implementação do modelo" da extensão CLI para o artigo [de Aprendizagem automática Azure.](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [onde implantar e como.](how-to-deploy-and-where.md)
* Aprenda a treinar e a implementar modelos de [aprendizagem automática utilizando pipelines Azure](/azure/devops/pipelines/targets/azure-machine-learning?view=azure-devops).
