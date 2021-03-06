---
title: 'Tutorial: oleodutos ML para pontuação de lotes'
titleSuffix: Azure Machine Learning
description: Neste tutorial, você constrói um pipeline de aprendizagem automática para realizar pontuação de lote num modelo de classificação de imagem. O Azure Machine Learning permite-lhe focar-se na aprendizagem automática em vez de infraestruturas e automação.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: laobri
ms.date: 03/11/2020
ms.openlocfilehash: 1ccd7a7f33c6ee5cab8b7173d8eb93365b6cb587
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79472225"
---
# <a name="tutorial-build-an-azure-machine-learning-pipeline-for-batch-scoring"></a>Tutorial: Construa um oleoduto azure machine learning para pontuação de lotes

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Aprenda a construir um oleoduto em Azure Machine Learning para executar um trabalho de pontuação de lote. Os gasodutos de aprendizagem automática otimizam o seu fluxo de trabalho com velocidade, portabilidade e reutilização, para que possa focar-se na aprendizagem automática em vez de infraestrutura e automação. Depois de construir e publicar um pipeline, configura um ponto final REST que pode utilizar para acionar o pipeline a partir de qualquer biblioteca HTTP em qualquer plataforma. 

O exemplo utiliza um modelo de rede neural convolucional [Inception-V3 pré-treinado](https://arxiv.org/abs/1512.00567) implementado no Tensorflow para classificar imagens não marcadas. [Saiba mais sobre os gasodutos de aprendizagem automática.](concept-ml-pipelines.md)

Neste tutorial, vai concluir as seguintes tarefas:

> [!div class="checklist"]
> * Configurar a área de trabalho 
> * Descarregue e guarde dados de amostras
> * Criar objetos conjuntos de dados para recolher e obter dados
> * Descarregue, prepare e registe o modelo no seu espaço de trabalho
> * Objetivos de computação de provisão e criar um roteiro de pontuação
> * Use `ParallelRunStep` a classe para pontuação de lote assinizador
> * Construir, correr e publicar um oleoduto
> * Ativar um ponto final DE REPOUSO para o gasoduto

Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)

## <a name="prerequisites"></a>Pré-requisitos

* Se ainda não tiver um espaço de trabalho azure Machine Learning ou uma máquina virtual de caderno, complete a [Parte 1 do tutorial de configuração](tutorial-1st-experiment-sdk-setup.md).
* Quando terminar o tutorial de configuração, utilize o mesmo servidor de portátil para abrir os *tutoriais/machine-learning-pipeline-pipeline-pipeline-batch-scoring-classification.ipynb.*

Se quiser executar o tutorial de configuração no seu próprio [ambiente local,](how-to-configure-environment.md#local)pode aceder ao tutorial no [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials). Corra `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-contrib-pipeline-steps pandas requests` para obter os pacotes necessários.

## <a name="configure-workspace-and-create-a-datastore"></a>Configure espaço de trabalho e crie uma loja de dados

Crie um objeto espaço de trabalho a partir do espaço de trabalho de Aprendizagem automática Azure existente.

- Um [espaço de trabalho](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) é uma classe que aceita a sua informação de subscrição azure e recursos. O espaço de trabalho também cria um recurso em nuvem que pode usar para monitorizar e rastrear as execuções do seu modelo. 
- `Workspace.from_config()`lê `config.json` o ficheiro e, em seguida, `ws`carrega os detalhes da autenticação num objeto chamado . O `ws` objeto é usado no código ao longo deste tutorial.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

## <a name="create-a-datastore-for-sample-images"></a>Criar uma loja de dados para imagens de amostra

Na `pipelinedata` conta, obtenha a amostra de dados `sampledata` públicos de avaliação imageNet do recipiente de blob público. Ligue `register_azure_blob_container()` para disponibilizar os dados ao espaço `images_datastore`de trabalho com o nome . Em seguida, detete tede o espaço de trabalho padrão datastore como a loja de dados de saída. Utilize a loja de dados de saída para marcar a saída no pipeline.

```python
from azureml.core.datastore import Datastore

batchscore_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="images_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata", 
                      overwrite=True)

def_data_store = ws.get_default_datastore()
```

## <a name="create-dataset-objects"></a>Criar objetos conjuntos de dados

Ao construir oleodutos, `Dataset` os objetos são usados `PipelineData` para ler dados de lojas de dados do espaço de trabalho, e os objetos são usados para transferir dados intermédios entre passos de pipeline.

> [!Important]
> O exemplo de pontuação do lote neste tutorial usa apenas um passo de oleoduto. Nos casos de utilização que têm múltiplas etapas, o fluxo típico incluirá estes passos:
>
> 1. Use `Dataset` objetos como *inputs* para recolher dados brutos, realizar alguma transformação e, em seguida, *forrindo* um `PipelineData` objeto.
>
> 2. Utilize `PipelineData` o objeto de *saída* no passo anterior como objeto de *entrada*. Repita-o para os passos seguintes.

Neste cenário, cria-se `Dataset` objetos que correspondam aos diretórios da datastore tanto para as imagens de entrada como para as etiquetas de classificação (valores de teste y). Também cria `PipelineData` um objeto para os dados de saída de pontuação do lote.

```python
from azureml.core.dataset import Dataset
from azureml.pipeline.core import PipelineData

input_images = Dataset.File.from_files((batchscore_blob, "batchscoring/images/"))
label_ds = Dataset.File.from_files((batchscore_blob, "batchscoring/labels/*.txt"))
output_dir = PipelineData(name="scores", 
                          datastore=def_data_store, 
                          output_path_on_compute="batchscoring/results")
```

Em seguida, registe os conjuntos de dados no espaço de trabalho.

```python

input_images = input_images.register(workspace = ws, name = "input_images")
label_ds = label_ds.register(workspace = ws, name = "label_ds")
```

## <a name="download-and-register-the-model"></a>Descarregue e registe o modelo

Descarregue o modelo tensorflow pré-treinado para usá-lo para pontuação de lote num pipeline. Primeiro, crie um diretório local onde guarde o modelo. Em seguida, faça o download e extrate o modelo.

```python
import os
import tarfile
import urllib.request

if not os.path.isdir("models"):
    os.mkdir("models")
    
response = urllib.request.urlretrieve("http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz", "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall("models")
```

Em seguida, registe o modelo no seu espaço de trabalho, para que possa recuperar facilmente o modelo no processo do gasoduto. Na `register()` função estática, o `model_name` parâmetro é a chave que utiliza para localizar o seu modelo em todo o SDK.

```python
from azureml.core.model import Model
 
model = Model.register(model_path="models/inception_v3.ckpt",
                       model_name="inception",
                       tags={"pretrained": "inception"},
                       description="Imagenet trained tensorflow inception",
                       workspace=ws)
```

## <a name="create-and-attach-the-remote-compute-target"></a>Criar e fixar o alvo de computação remota

Os gasodutos de aprendizagem automática não podem ser executados localmente, por isso executa-os em recursos em nuvem ou *alvos de computação remota.* Um alvo de computação remota é um ambiente de computação virtual reutilizável onde você executa experiências e fluxos de trabalho de aprendizagem automática. 

Execute o seguinte código para criar [`AmlCompute`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) um alvo ativado por GPU e, em seguida, anexe-o ao seu espaço de trabalho. Para obter mais informações sobre alvos de cálculo, consulte o [artigo conceptual](https://docs.microsoft.com/azure/machine-learning/concept-compute-target).


```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.exceptions import ComputeTargetException
compute_name = "gpu-cluster"

# checks to see if compute target already exists in workspace, else create it
try:
    compute_target = ComputeTarget(workspace=ws, name=compute_name)
except ComputeTargetException:
    config = AmlCompute.provisioning_configuration(vm_size="STANDARD_NC6",
                                                   vm_priority="lowpriority", 
                                                   min_nodes=0, 
                                                   max_nodes=1)

    compute_target = ComputeTarget.create(workspace=ws, name=compute_name, provisioning_configuration=config)
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

## <a name="write-a-scoring-script"></a>Escreva um roteiro de pontuação

Para fazer a pontuação, crie `batch_scoring.py`um roteiro de pontuação de lote chamado , e depois escreva-o para o atual diretório. O script tira imagens de entrada, aplica o modelo de classificação e, em seguida, produz as previsões para um ficheiro de resultados.

O `batch_scoring.py` guião toma os seguintes parâmetros, que são passados a `ParallelRunStep` partir do que cria mais tarde:

- `--model_name`: O nome do modelo que está a ser utilizado.
- `--labels_name`: O nome `Dataset` do `labels.txt` ficheiro que contém.

A infraestrutura `ArgumentParser` do gasoduto utiliza a classe para passar parâmetros em passos de gasoduto. Por exemplo, no seguinte código, `--model_name` o primeiro argumento `model_name`é dado ao identificador de propriedade . Na `init()` função, `Model.get_model_path(args.model_name)` é utilizado para aceder a esta propriedade.


```python
%%writefile batch_scoring.py

import os
import argparse
import datetime
import time
import tensorflow as tf
from math import ceil
import numpy as np
import shutil
from tensorflow.contrib.slim.python.slim.nets import inception_v3

from azureml.core import Run
from azureml.core.model import Model
from azureml.core.dataset import Dataset

slim = tf.contrib.slim

image_size = 299
num_channel = 3


def get_class_label_dict():
    label = []
    proto_as_ascii_lines = tf.gfile.GFile("labels.txt").readlines()
    for l in proto_as_ascii_lines:
        label.append(l.rstrip())
    return label


def init():
    global g_tf_sess, probabilities, label_dict, input_images

    parser = argparse.ArgumentParser(description="Start a tensorflow model serving")
    parser.add_argument('--model_name', dest="model_name", required=True)
    parser.add_argument('--labels_name', dest="labels_name", required=True)
    args, _ = parser.parse_known_args()

    workspace = Run.get_context(allow_offline=False).experiment.workspace
    label_ds = Dataset.get_by_name(workspace=workspace, name=args.labels_name)
    label_ds.download(target_path='.', overwrite=True)

    label_dict = get_class_label_dict()
    classes_num = len(label_dict)

    with slim.arg_scope(inception_v3.inception_v3_arg_scope()):
        input_images = tf.placeholder(tf.float32, [1, image_size, image_size, num_channel])
        logits, _ = inception_v3.inception_v3(input_images,
                                              num_classes=classes_num,
                                              is_training=False)
        probabilities = tf.argmax(logits, 1)

    config = tf.ConfigProto()
    config.gpu_options.allow_growth = True
    g_tf_sess = tf.Session(config=config)
    g_tf_sess.run(tf.global_variables_initializer())
    g_tf_sess.run(tf.local_variables_initializer())

    model_path = Model.get_model_path(args.model_name)
    saver = tf.train.Saver()
    saver.restore(g_tf_sess, model_path)


def file_to_tensor(file_path):
    image_string = tf.read_file(file_path)
    image = tf.image.decode_image(image_string, channels=3)

    image.set_shape([None, None, None])
    image = tf.image.resize_images(image, [image_size, image_size])
    image = tf.divide(tf.subtract(image, [0]), [255])
    image.set_shape([image_size, image_size, num_channel])
    return image


def run(mini_batch):
    result_list = []
    for file_path in mini_batch:
        test_image = file_to_tensor(file_path)
        out = g_tf_sess.run(test_image)
        result = g_tf_sess.run(probabilities, feed_dict={input_images: [out]})
        result_list.append(os.path.basename(file_path) + ": " + label_dict[result[0]])
    return result_list
```

> [!TIP]
> O oleoduto neste tutorial tem apenas um passo, e escreve a saída para um ficheiro. Para gasodutos em várias `ArgumentParser` etapas, também utiliza para definir um diretório para escrever dados de saída para entrada em etapas posteriores. Para um exemplo de passagem de dados `ArgumentParser` entre vários passos de pipeline utilizando o padrão de design, consulte o [caderno](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb).

## <a name="build-the-pipeline"></a>Construir o oleoduto

Antes de executar o oleoduto, crie um objeto que defina `batch_scoring.py` o ambiente Python e crie as dependências que o seu script necessita. A principal dependência necessária é a Tensorflow, mas também instala `azureml-defaults` para processos de fundo. Crie `RunConfiguration` um objeto usando as dependências. Além disso, especifique o apoio do Docker e do Docker-GPU.

```python
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

cd = CondaDependencies.create(pip_packages=["tensorflow-gpu==1.13.1", "azureml-defaults"])
env = Environment(name="parallelenv")
env.python.conda_dependencies = cd
env.docker.base_image = DEFAULT_GPU_IMAGE
```

### <a name="create-the-configuration-to-wrap-the-script"></a>Criar a configuração para embrulhar o script

Crie o passo do gasoduto utilizando o script, configuração do ambiente e parâmetros. Especifique o alvo computacional que já está ligado ao seu espaço de trabalho.

```python
from azureml.contrib.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    environment=env,
    entry_script="batch_scoring.py",
    source_directory=".",
    output_action="append_row",
    mini_batch_size="20",
    error_threshold=1,
    compute_target=compute_target,
    process_count_per_node=2,
    node_count=1
)
```

### <a name="create-the-pipeline-step"></a>Criar o passo do oleoduto

Um passo de oleoduto é um objeto que encapsula tudo o que precisa para executar um oleoduto, incluindo:

* Ambiente e regulação da dependência
* O recurso computacional para executar o oleoduto em
* Dados de entrada e saída, e quaisquer parâmetros personalizados
* Referência a uma lógica de script ou SDK para executar durante o passo

Várias classes herdam da classe [`PipelineStep`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py)dos pais. Você pode escolher aulas para usar quadros ou pilhas específicos para construir um passo. Neste exemplo, você `ParallelRunStep` usa a classe para definir a sua lógica de passo usando um script python personalizado. Se um argumento para o seu script for uma entrada para o passo ou uma saída do `input` passo, `output` o argumento deve ser definido tanto na `arguments` *matriz* *como* no parâmetro, respectivamente. 

Nos cenários em que há mais de um `outputs` passo, uma referência de objeto na matriz fica disponível como *uma entrada* para um passo de pipeline subsequente.

```python
from azureml.contrib.pipeline.steps import ParallelRunStep

batch_score_step = ParallelRunStep(
    name="parallel-step-test",
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    models=[model],
    arguments=["--model_name", "inception",
               "--labels_name", "label_ds"],
    parallel_run_config=parallel_run_config,
    allow_reuse=False
)
```

Para uma lista de todas as classes que pode utilizar para diferentes tipos de passos, consulte o [pacote de passos](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py).

## <a name="submit-the-pipeline"></a>Submeter o gasoduto

Agora, corre o oleoduto. Primeiro, crie um `Pipeline` objeto utilizando a referência do espaço de trabalho e o passo do gasoduto que criou. O `steps` parâmetro é uma variedade de passos. Neste caso, só há um passo para marcar lotes. Para construir oleodutos com vários passos, coloque os passos em ordem nesta matriz.

Em seguida, `Experiment.submit()` utilize a função para submeter o gasoduto para execução. Também especifica o parâmetro `param_batch_size`personalizado . A `wait_for_completion` função é de saída de troncos durante o processo de construção do gasoduto. Pode utilizar os registos para ver o progresso atual.

> [!IMPORTANT]
> A primeira execução do gasoduto demora cerca de *15 minutos*. Todas as dependências devem ser descarregadas, uma imagem do Docker é criada, e o ambiente Python é aprovisionado e criado. A execução do gasoduto volta a demorar significativamente menos tempo, porque esses recursos são reutilizados em vez de serem criados. No entanto, o tempo total de execução para o gasoduto depende da carga de trabalho dos seus scripts e dos processos que estão a decorrer em cada passo do gasoduto.

```python
from azureml.core import Experiment
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline)
pipeline_run.wait_for_completion(show_output=True)
```

### <a name="download-and-review-output"></a>Descarregar e rever saída

Executar o seguinte código para descarregar o ficheiro `batch_scoring.py` de saída que é criado a partir do script. Depois, explore os resultados das pontuações.

```python
import pandas as pd

batch_run = next(pipeline_run.get_children())
batch_output = batch_run.get_output_data("scores")
batch_output.download(local_path="inception_results")

for root, dirs, files in os.walk("inception_results"):
    for file in files:
        if file.endswith("parallel_run_step.txt"):
            result_file = os.path.join(root, file)

df = pd.read_csv(result_file, delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
print("Prediction has ", df.shape[0], " rows")
df.head(10)
```

## <a name="publish-and-run-from-a-rest-endpoint"></a>Publicar e executar a partir de um ponto final REST

Execute o seguinte código para publicar o gasoduto no seu espaço de trabalho. No seu espaço de trabalho no estúdio Azure Machine Learning, pode ver metadados para o pipeline, incluindo histórico de execução e durações. Também pode executar o gasoduto manualmente a partir do estúdio.

A publicação do pipeline permite um ponto final REST que pode utilizar para executar o pipeline a partir de qualquer biblioteca HTTP em qualquer plataforma.

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", description="Batch scoring using Inception v3 model", version="1.0")

published_pipeline
```

Para executar o gasoduto a partir do ponto final REST, você precisa de um cabeçalho de autenticação tipo OAuth2. O exemplo seguinte utiliza a autenticação interativa (para fins de ilustração), mas para a maioria dos cenários de produção que requerem autenticação automatizada ou sem cabeça, utilize a autenticação principal do serviço [como descrito neste artigo.](how-to-setup-authentication.md)

A autenticação principal do serviço envolve a criação de um Registo de *Aplicações* no *Diretório Ativo Azure.* Primeiro, gera um segredo de cliente e depois concede o seu *papel* principal de serviço ao seu espaço de trabalho de aprendizagem automática. Use [`ServicePrincipalAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?view=azure-ml-py) a classe para gerir o seu fluxo de autenticação. 

Ambos [`InteractiveLoginAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.interactiveloginauthentication?view=azure-ml-py) `ServicePrincipalAuthentication` e `AbstractAuthentication`herdados de . Em ambos os [`get_authentication_header()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.abstractauthentication?view=azure-ml-py#get-authentication-header--) casos, utilize a função da mesma forma para obter o cabeçalho:

```python
from azureml.core.authentication import InteractiveLoginAuthentication

interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()
```

Obtenha o URL `endpoint` REST da propriedade do objeto de pipeline publicado. Também pode encontrar o URL REST no seu espaço de trabalho no estúdio Azure Machine Learning. 

Construa um pedido HTTP POST para o ponto final. Especifique o seu cabeçalho de autenticação no pedido. Adicione um objeto de carga útil JSON que tenha o nome da experiência e o parâmetro do tamanho do lote. Como notado anteriormente no `param_batch_size` tutorial, é `batch_scoring.py` passado para o seu `PipelineParameter` script porque o definiu como um objeto na configuração do passo.

Faça o pedido para desencadear a corrida. Inclua código `Id` para aceder à chave do dicionário de resposta para obter o valor do ID de execução.

```python
import requests

rest_endpoint = published_pipeline.endpoint
response = requests.post(rest_endpoint, 
                         headers=auth_header, 
                         json={"ExperimentName": "batch_scoring",
                               "ParameterAssignments": {"param_batch_size": 50}})
run_id = response.json()["Id"]
```

Utilize o ID de execução para monitorizar o estado da nova execução. A nova corrida leva mais 10-15 min para terminar. 

A nova corrida será semelhante ao oleoduto que correu mais cedo no tutorial. Pode optar por não ver a saída completa.

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.widgets import RunDetails

published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)
RunDetails(published_pipeline_run).show()
```

## <a name="clean-up-resources"></a>Limpar recursos

Não complete esta secção se planeia executar outros tutoriais de Aprendizagem automática azure.

### <a name="stop-the-compute-instance"></a>Pare a instância computacional

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Apagar tudo

Se não planeia usar os recursos que criou, elimine-os, para que não incorra em quaisquer acusações:

1. No portal Azure, no menu esquerdo, selecione **Grupos de Recursos.**
1. Na lista de grupos de recursos, selecione o grupo de recursos que criou.
1. Selecione **Eliminar grupo de recursos**.
1. Insira o nome do grupo de recursos. Em seguida, selecione **Delete**.

Também pode manter o grupo de recursos, mas eliminar um único espaço de trabalho. Mostrar as propriedades do espaço de trabalho e, em seguida, selecionar **Apagar**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial de pipelines de aprendizagem automática, fez as seguintes tarefas:

> [!div class="checklist"]
> * Construiu um oleoduto com dependências ambientais para funcionar com um recurso de computação GPU remoto.
> * Criou um script de pontuação para executar previsões de lote usando um modelo tenso tenso.
> * Publicou um oleoduto e permitiu que fosse executado a partir de um ponto final REST.

Para mais exemplos de como construir oleodutos utilizando o SDK de aprendizagem automática, consulte o [repositório](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)de cadernos .
