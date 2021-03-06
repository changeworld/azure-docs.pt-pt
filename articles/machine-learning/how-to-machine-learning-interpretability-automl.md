---
title: Explanabilidade na aprendizagem automática de máquinas
titleSuffix: Azure Machine Learning
description: Saiba como obter explicações sobre como o seu modelo ML automatizado determina a importância da funcionalidade e faz previsões ao utilizar o Azure Machine Learning SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.date: 03/11/2020
ms.openlocfilehash: ecf7cde1a7f804ef1c43b21ac5c1d2a488660590
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257169"
---
# <a name="interpretability-model-explanations-in-automated-machine-learning"></a>Interpretação: explicações de modelo saqueadas em machine learning automatizado

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprende-se a obter explicações para aprendizagem automática de máquinas (ML) em Aprendizagem automática de Máquinas azure. Ml automatizado ajuda-o a compreender a importância da funcionalidade projetada. 

Todas as versões SDK após `model_explainability=True` 1.0.85 definidas por padrão. Na versão SDK 1.0.85 e versões `model_explainability=True` anteriores os utilizadores precisam de definir o `AutoMLConfig` objeto para utilizar a interpretação do modelo. 

Neste artigo, vai aprender a:

- Execute a interpretação durante o treino para o melhor modelo ou qualquer modelo.
- Ative visualizações para ajudá-lo a ver padrões em dados e explicações.
- Implementar a interpretação durante a inferência ou pontuação.

## <a name="prerequisites"></a>Pré-requisitos

- Características de interpretação. Corra `pip install azureml-interpret azureml-contrib-interpret` para obter os pacotes necessários.
- Conhecimento da construção de experiências automatizadas de ML. Para obter mais informações sobre como utilizar o Azure Machine Learning SDK, complete este tutorial do modelo de [regressão](tutorial-auto-train-models.md) ou veja como [configurar experiências automatizadas](how-to-configure-auto-train.md)de ML .

## <a name="interpretability-during-training-for-the-best-model"></a>Interpretação durante o treino para o melhor modelo

Recupere a `best_run`explicação da , que inclui explicações para as características da engenharia.

### <a name="download-engineered-feature-importance-from-artifact-store"></a>Descarregue a importância da funcionalidade projetada da loja de artefactos

Pode utilizar `ExplanationClient` para descarregar as explicações de recurso `best_run`da empresa de artefactos da . 

```python
from azureml.explain.model._internal.explanation_client import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>Interpretação durante o treino para qualquer modelo 

Quando calcula explicações de modelo seleção e as visualiza, não se limita a uma explicação de modelo existente para um modelo ML automatizado. Também pode obter uma explicação para o seu modelo com diferentes dados de teste. Os passos nesta secção mostram-lhe como calcular e visualizar a importância da funcionalidade de engenharia com base nos dados do seu teste.

### <a name="retrieve-any-other-automl-model-from-training"></a>Recupere qualquer outro modelo AutoML do treino

```python
automl_run, fitted_model = local_run.get_output(metric='accuracy')
```

### <a name="set-up-the-model-explanations"></a>Configurar as explicações do modelo

Use `automl_setup_model_explanations` para obter as explicações da engenharia. A `fitted_model` lata pode gerar os seguintes itens:

- Dados em destaque de amostras treinadas ou de teste
- Listas de nomes de funcionalidades projetadas
- Aulas findáveis na sua coluna rotulada em cenários de classificação

Contém `automl_explainer_setup_obj` todas as estruturas de cima da lista.

```python
from azureml.train.automl.runtime.automl_explain_utilities import automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```

### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>Inicialize o Explicador Mimic para a importância da característica

Para gerar uma explicação para `MimicWrapper` os modelos AutoML, utilize a classe. Pode inicializar o MimicWrapper com estes parâmetros:

- O objeto de configuração explicador
- O seu espaço de trabalho
- Um modelo LightGBM, que funciona `fitted_model` como substituto do modelo ml automatizado

O MimicWrapper também `automl_run` leva o objeto onde as explicações concebidas serão carregadas.

```python
from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel
from azureml.explain.model.mimic_wrapper import MimicWrapper

# Initialize the Mimic Explainer
explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator, LGBMExplainableModel, 
                         init_dataset=automl_explainer_setup_obj.X_transform, run=automl_run,
                         features=automl_explainer_setup_obj.engineered_feature_names, 
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes)
```

### <a name="use-mimicexplainer-for-computing-and-visualizing-engineered-feature-importance"></a>Use mimicExplainer para computação e visualização de características projetadas

Pode chamar `explain()` o método em MimicWrapper com as amostras de teste transformadas para obter a importância da funcionalidade para as características criadas. Também pode `ExplanationDashboard` utilizar para visualizar a visualização do dashboard dos valores de importância da característica das características criadas por recursos ML automatizados.

```python
engineered_explanations = explainer.explain(['local', 'global'], eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(engineered_explanations.get_feature_importance_dict())
```

### <a name="interpretability-during-inference"></a>Interpretação durante a inferência

Nesta secção, aprende-se a operacionalizar um modelo ML automatizado com o explicador que foi utilizado para calcular as explicações na secção anterior.

### <a name="register-the-model-and-the-scoring-explainer"></a>Registe o modelo e o explicador de pontuação

Use `TreeScoringExplainer` o para criar o explicador de pontuação que calculará os valores de importância da característica projetada no momento da inferência. Inicializa-se o explicador `feature_map` de pontuação com o que foi calculado anteriormente. 

Guarde o explicador de pontuação e, em seguida, registe o modelo e o explicador de pontuação com o Serviço de Gestão de Modelos. Executar o seguinte código:

```python
from azureml.interpret.scoring.scoring_explainer import TreeScoringExplainer, save

# Initialize the ScoringExplainer
scoring_explainer = TreeScoringExplainer(explainer.explainer, feature_maps=[automl_explainer_setup_obj.feature_map])

# Pickle scoring explainer locally
save(scoring_explainer, exist_ok=True)

# Register trained automl model present in the 'outputs' folder in the artifacts
original_model = automl_run.register_model(model_name='automl_model', 
                                           model_path='outputs/model.pkl')

# Register scoring explainer
automl_run.upload_file('scoring_explainer.pkl', 'scoring_explainer.pkl')
scoring_explainer_model = automl_run.register_model(model_name='scoring_explainer', model_path='scoring_explainer.pkl')
```

### <a name="create-the-conda-dependencies-for-setting-up-the-service"></a>Crie as dependências do condomínio para a criação do serviço

Em seguida, crie as dependências ambientais necessárias no recipiente para o modelo implantado. Por favor, note que os incumprimentos em azureml com a versão >= 1.0.45 devem ser listados como uma dependência do pip, porque contém a funcionalidade necessária para hospedar o modelo como um serviço web.

```python
from azureml.core.conda_dependencies import CondaDependencies

azureml_pip_packages = [
    'azureml-interpret', 'azureml-train-automl', 'azureml-defaults'
]

myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas', 'numpy', 'py-xgboost<=0.80'],
                                 pip_packages=azureml_pip_packages,
                                 pin_sdk_version=True)

with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())

with open("myenv.yml","r") as f:
    print(f.read())

```

### <a name="deploy-the-service"></a>Implementar o serviço

Implemente o serviço utilizando o ficheiro conda e o ficheiro de pontuação dos passos anteriores.

```python
from azureml.core.webservice import Webservice
from azureml.core.webservice import AciWebservice
from azureml.core.model import Model, InferenceConfig
from azureml.core.environment import Environment

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                               memory_gb=1,
                                               tags={"data": "Bank Marketing",  
                                                     "method" : "local_explanation"},
                                               description='Get local explanations for Bank marketing test data')
myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score_local_explain.py", environment=myenv)

# Use configs and models generated above
service = Model.deploy(ws,
                       'model-scoring',
                       [scoring_explainer_model, original_model],
                       inference_config,
                       aciconfig)
service.wait_for_deployment(show_output=True)
```

### <a name="inference-with-test-data"></a>Inferência com dados de teste

Inferência com alguns dados de teste para ver o valor previsto do modelo ml automatizado. Veja a importância da característica projetada para o valor previsto.

```python
if service.state == 'Healthy':
    # Serialize the first row of the test data into json
    X_test_json = X_test[:1].to_json(orient='records')
    print(X_test_json)
    # Call the service to get the predictions and the engineered explanations
    output = service.run(X_test_json)
    # Print the predicted value
    print(output['predictions'])
    # Print the engineered feature importances for the predicted value
    print(output['engineered_local_importance_values'])
```

### <a name="visualize-to-discover-patterns-in-data-and-explanations-at-training-time"></a>Visualizar para descobrir padrões em dados e explicações no tempo de treino

Pode visualizar o gráfico de importância da funcionalidade no seu espaço de trabalho no [estúdio Azure Machine Learning.](https://ml.azure.com) Depois de concluída a sua execução automática ml, selecione **Ver detalhes do modelo** para visualizar uma execução específica. Selecione o separador **Explicações** para ver o painel de visualização da explicação.

[![Arquitetura de interpretação de aprendizagem automática](./media/how-to-machine-learning-interpretability-automl/automl-explainability.png)](./media/how-to-machine-learning-interpretability-automl/automl-explainability.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre como pode ativar explicações de modelos e características importantes em áreas do Azure Machine Learning SDK que não seja machine learning automatizado, consulte o [artigo conceptual sobre interpretação](how-to-machine-learning-interpretability.md).
