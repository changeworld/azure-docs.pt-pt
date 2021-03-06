---
title: 'Tutorial: Caderno Azure - Personalizer'
titleSuffix: Azure Cognitive Services
description: Este tutorial simula um loop Personalizer _system num Caderno Azure, que sugere que tipo de café um cliente deve encomendar. Os utilizadores e as suas preferências são armazenados num conjunto de dados do utilizador. Informações sobre o café também estão disponíveis e armazenadas num conjunto de dados de café.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: tutorial
ms.date: 02/03/2020
ms.author: diberry
ms.openlocfilehash: 03e8b658f7edf4640d738e5ea3af84953185d0f5
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76986840"
---
# <a name="tutorial-use-personalizer-in-azure-notebook"></a>Tutorial: Use personalizer em Caderno Azure

Este tutorial executa um loop Personalizer em um Notebook Azure, demonstrando o fim do ciclo de vida final de um loop Personalizer.

O loop sugere que tipo de café um cliente deve encomendar. Os utilizadores e as suas preferências são armazenados num conjunto de dados do utilizador. A informação sobre o café é armazenada num conjunto de dados de café.

## <a name="users-and-coffee"></a>Utilizadores e café

O caderno, simulando a interação do utilizador com um website, seleciona um utilizador aleatório, hora do dia e tipo de tempo a partir do conjunto de dados. Um resumo da informação do utilizador é:

|Clientes - características de contexto|Horários do dia|Tipos de clima|
|--|--|--|
|Alice<br>Bob<br>Cathy<br>Rio Dave|Manhã<br>Tarde<br>Início da noite|Ensolarado<br>Chuvosa<br>Neve|

Para ajudar o Personalizer a aprender, com o tempo, o _sistema_ também conhece detalhes sobre a seleção do café para cada pessoa.

|Café - características de ação|Tipos de temperatura|Locais de origem|Tipos de assado|Orgânico|
|--|--|--|--|--|
|Cappacino|Acesso Frequente|Quénia|Dark|Orgânico|
|Cerveja fria|Cold|Brasil|Light|Orgânico|
|Mocha gelado|Cold|Etiópia|Light|Não orgânico|
|Latte|Acesso Frequente|Brasil|Dark|Não orgânico|

O **objetivo** do loop Personalizer é encontrar a melhor combinação entre os utilizadores e o café o máximo de tempo possível.

O código para este tutorial está disponível no [repositório Personalizer Samples GitHub](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook).

## <a name="how-the-simulation-works"></a>Como funciona a simulação

No início do sistema de execução, as sugestões da Personalizer só são bem sucedidas entre 20% e 30%. Este sucesso é indicado pela recompensa enviada de volta para a API recompensa da Personalizer, com uma pontuação de 1. Depois de algumas chamadas de Rank e Reward, o sistema melhora.

Após os pedidos iniciais, eexecute uma avaliação offline. Isto permite ao Personalizer rever os dados e sugerir uma melhor política de aprendizagem. Aplique a nova política de aprendizagem e volte a executar o caderno com 20% da contagem de pedidos anteriores. O ciclo terá um melhor desempenho com a nova política de aprendizagem.

## <a name="rank-and-reward-calls"></a>Classificação e chamadas de recompensa

Para cada uma das poucas mil chamadas para o serviço Personalizer, o Caderno Azure envia o pedido de **Rank** para a Rest API:

* Um ID único para o evento Rank/Request
* Características de contexto - Uma escolha aleatória do utilizador, tempo e hora do dia - simulando um utilizador num site ou dispositivo móvel
* Ações com Funcionalidades - _Todos_ os dados do café - a partir dos quais o Personalizer faz uma sugestão

O sistema recebe o pedido e, em seguida, compara essa previsão com a escolha conhecida do utilizador pela mesma hora do dia e tempo. Se a escolha conhecida for a mesma escolha prevista, a **Recompensa** de 1 é enviada de volta para Personalizer. Caso contrário, a recompensa enviada de volta é 0.

> [!Note]
> Esta é uma simulação, por isso o algoritmo para a recompensa é simples. Num cenário real, o algoritmo deve usar a lógica do negócio, possivelmente com pesos para vários aspetos da experiência do cliente, para determinar a pontuação da recompensa.


## <a name="prerequisites"></a>Pré-requisitos

* Uma conta [azure notebook.](https://notebooks.azure.com/)
* Um [recurso Azure Personalizer.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)
    * Se já utilizou o recurso Personalizer, certifique-se de [limpar os dados](how-to-settings.md#clear-data-for-your-learning-loop) no portal Azure para o recurso.
* Faça upload de todos os ficheiros [para esta amostra](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook) num projeto do Azure Notebook.

Descrições do ficheiro:

* [Personalizer.ipynb](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/Personalizer.ipynb) é o caderno Jupyter para este tutorial.
* [O conjunto de dados](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/users.json) do utilizador é armazenado num objeto JSON.
* [O conjunto de dados](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/coffee.json) de café é armazenado num objeto JSON.
* [Exemplo Pedido JSON](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/example-rankrequest.json) é o formato esperado para um pedido de POST para a Rank API.

## <a name="configure-personalizer-resource"></a>Configurar recurso personalizer

No portal Azure, configure o seu [recurso Personalizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer) com a frequência do modelo de **atualização** definida para 15 segundos e um tempo de **espera de recompensa** de 15 segundos. Estes valores encontram-se na página de **[Configuração.](how-to-settings.md#configure-service-settings-in-the-azure-portal)**

|Definição|Valor|
|--|--|
|atualizar frequência de modelo|15 segundos|
|tempo de espera recompensa|15 segundos|

Estes valores têm uma duração muito curta para mostrar alterações neste tutorial. Estes valores não devem ser utilizados num cenário de produção sem validar que atinjam o seu objetivo com o seu loop Personalizer.

## <a name="set-up-the-azure-notebook"></a>Configurar o Caderno Azure

1. Mude o Kernel para `Python 3.6`.
1. Abra o ficheiro `Personalizer.ipynb`.

## <a name="run-notebook-cells"></a>Executar células caderno

Execute cada célula executável e espere que volte. Sabe que é feito quando os suportes ao lado `*`da célula apresentam um número em vez de um . As seguintes secções explicam o que cada célula faz programáticamente e o que esperar para a saída.

### <a name="include-the-python-modules"></a>Incluir os módulos python

Inclua os módulos pitão necessários. A célula não tem saída.

```python
import json
import matplotlib.pyplot as plt
import random
import requests
import time
import uuid
```

### <a name="set-personalizer-resource-key-and-name"></a>Definir chave de recursos personalizador e nome

A partir do portal Azure, encontre a sua chave e ponto final na página **Quickstart** do seu recurso Personalizer. Altere o `<your-resource-name>` valor do nome do seu recurso Personalizer. Altere o `<your-resource-key>` valor da sua tecla Personalizer.

```python
# Replace 'personalization_base_url' and 'resource_key' with your valid endpoint values.
personalization_base_url = "https://<your-resource-name>.cognitiveservices.azure.com/"
resource_key = "<your-resource-key>"
```

### <a name="print-current-date-and-time"></a>Imprimir data e hora correntes
Utilize esta função para observar os tempos de início e de fim da função iterativa, iterações.

Estas células não têm saída. A função produz a data e a hora atuais quando chamada.

```python
# Print out current datetime
def currentDateTime():
    currentDT = datetime.datetime.now()
    print (str(currentDT))
```

### <a name="get-the-last-model-update-time"></a>Obtenha o último tempo de atualização do modelo

Quando a `get_last_updated`função, é chamada, a função imprime a última data e hora modificadas que o modelo foi atualizado.

Estas células não têm saída. A função produz a última data de treino do modelo quando chamada.

A função utiliza uma API GET REST para [obter propriedades](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/GetModelProperties)do modelo .

```python
# ititialize variable for model's last modified date
modelLastModified = ""
```

```python
def get_last_updated(currentModifiedDate):

    print('-----checking model')

    # get model properties
    response = requests.get(personalization_model_properties_url, headers = headers, params = None)

    print(response)
    print(response.json())

    # get lastModifiedTime
    lastModifiedTime = json.dumps(response.json()["lastModifiedTime"])

    if (currentModifiedDate != lastModifiedTime):
        currentModifiedDate = lastModifiedTime
        print(f'-----model updated: {lastModifiedTime}')
```

### <a name="get-policy-and-service-configuration"></a>Obtenha a configuração de política e serviço

Valide o estado do serviço com estas duas chamadas REST.

Estas células não têm saída. A função produz os valores de serviço quando chamado.

```python
def get_service_settings():

    print('-----checking service settings')

    # get learning policy
    response = requests.get(personalization_model_policy_url, headers = headers, params = None)

    print(response)
    print(response.json())

    # get service settings
    response = requests.get(personalization_service_configuration_url, headers = headers, params = None)

    print(response)
    print(response.json())
```

### <a name="construct-urls-and-read-json-data-files"></a>Construa URLs e leia ficheiros de dados da JSON

Esta célula

* constrói os URLs usados em chamadas REST
* define o cabeçalho de segurança usando a chave de recursos Personalizer
* define a semente aleatória para o Id do evento Rank
* lê-se nos ficheiros de dados da JSON
* método `get_last_updated` de chamadas - política de aprendizagem foi removido, por exemplo, produção
* método `get_service_settings` de chamadas

A célula tem saída `get_last_updated` da `get_service_settings` chamada para e funciona.

```python
# build URLs
personalization_rank_url = personalization_base_url + "personalizer/v1.0/rank"
personalization_reward_url = personalization_base_url + "personalizer/v1.0/events/" #add "{eventId}/reward"
personalization_model_properties_url = personalization_base_url + "personalizer/v1.0/model/properties"
personalization_model_policy_url = personalization_base_url + "personalizer/v1.0/configurations/policy"
personalization_service_configuration_url = personalization_base_url + "personalizer/v1.0/configurations/service"

headers = {'Ocp-Apim-Subscription-Key' : resource_key, 'Content-Type': 'application/json'}

# context
users = "users.json"

# action features
coffee = "coffee.json"

# empty JSON for Rank request
requestpath = "example-rankrequest.json"

# initialize random
random.seed(time.time())

userpref = None
rankactionsjsonobj = None
actionfeaturesobj = None

with open(users) as handle:
    userpref = json.loads(handle.read())

with open(coffee) as handle:
    actionfeaturesobj = json.loads(handle.read())

with open(requestpath) as handle:
    rankactionsjsonobj = json.loads(handle.read())

get_last_updated(modelLastModified)
get_service_settings()

print(f'User count {len(userpref)}')
print(f'Coffee count {len(actionfeaturesobj)}')
```

Verifique se a `rewardWaitTime` saída `modelExportFrequency` é e ambos estão definidos para 15 segundos.

```console
-----checking model
<Response [200]>
{'creationTime': '0001-01-01T00:00:00+00:00', 'lastModifiedTime': '0001-01-01T00:00:00+00:00'}
-----model updated: "0001-01-01T00:00:00+00:00"
-----checking service settings
<Response [200]>
{...learning policy...}
<Response [200]>
{'rewardWaitTime': '00:00:15', 'defaultReward': 0.0, 'rewardAggregation': 'earliest', 'explorationPercentage': 0.2, 'modelExportFrequency': '00:00:15', 'logRetentionDays': -1}
User count 4
Coffee count 4
```

### <a name="troubleshooting-the-first-rest-call"></a>Resolução de problemas na primeira chamada do REST

Esta célula anterior é a primeira célula que chama o Personalizer. Certifique-se de que o `<Response [200]>`código de estado DOREST na saída é . Se tiver um erro, como o 404, mas tem a certeza de que a sua chave de recursos e o seu nome estão corretos, recarregue o caderno.

Certifique-se de que a contagem de café e utilizadores é de 4. Se tiver um erro, verifique se fez o upload dos 3 ficheiros JSON.

### <a name="set-up-metric-chart-in-azure-portal"></a>Configurar gráfico métrico no portal Azure

Mais tarde neste tutorial, o processo de longa duração de 10.000 pedidos é visível a partir do navegador com uma caixa de texto atualizada. Pode ser mais fácil de ver num gráfico ou como uma soma total, quando o processo de longo prazo termina. Para visualizar esta informação, utilize as métricas fornecidas com o recurso. Pode criar o gráfico agora que completou um pedido ao serviço e, em seguida, refrescar o gráfico periodicamente enquanto o processo de longa duração está em curso.

1. No portal Azure, selecione o seu recurso Personalizer.
1. Na navegação de recursos, selecione **Métricas** por baixo da Monitorização.
1. Na tabela, selecione **Adicionar métrica**.
1. O espaço de recursos e nomes métricos já está definido. Basta selecionar a métrica das **chamadas bem sucedidas** e a agregação da **soma**.
1. Mude o filtro de tempo para as últimas 4 horas.

    ![Configurar gráficométrico no portal Azure, adicionando métrica para chamadas bem sucedidas nas últimas 4 horas.](./media/tutorial-azure-notebook/metric-chart-setting.png)

    Devia ver três chamadas bem sucedidas na tabela.

### <a name="generate-a-unique-event-id"></a>Gerar um ID de evento único

Esta função gera um ID único para cada chamada de classificação. O ID é usado para identificar a classificação e recompensa de informação de chamada. Este valor pode vir de um processo de negócio, como um ID de vista web ou ID de transação.

A célula não tem saída. A função produz o ID único quando chamado.

```python
def add_event_id(rankjsonobj):
    eventid = uuid.uuid4().hex
    rankjsonobj["eventId"] = eventid
    return eventid
```

### <a name="get-random-user-weather-and-time-of-day"></a>Obtenha utilizador aleatório, tempo e hora do dia

Esta função seleciona um utilizador único, tempo e hora do dia, em seguida, adiciona esses itens ao objeto JSON para enviar ao pedido de Rank.

A célula não tem saída. Quando a função é chamada, devolve o nome do utilizador aleatório, o tempo aleatório e a hora aleatória do dia.

A lista de 4 utilizadores e as suas preferências - apenas algumas preferências são mostradas pela brevidade:

```json
{
  "Alice": {
    "Sunny": {
      "Morning": "Cold brew",
      "Afternoon": "Iced mocha",
      "Evening": "Cold brew"
    }...
  },
  "Bob": {
    "Sunny": {
      "Morning": "Cappucino",
      "Afternoon": "Iced mocha",
      "Evening": "Cold brew"
    }...
  },
  "Cathy": {
    "Sunny": {
      "Morning": "Latte",
      "Afternoon": "Cold brew",
      "Evening": "Cappucino"
    }...
  },
  "Dave": {
    "Sunny": {
      "Morning": "Iced mocha",
      "Afternoon": "Iced mocha",
      "Evening": "Iced mocha"
    }...
  }
}
```

```python
def add_random_user_and_contextfeatures(namesoption, weatheropt, timeofdayopt, rankjsonobj):
    name = namesoption[random.randint(0,3)]
    weather = weatheropt[random.randint(0,2)]
    timeofday = timeofdayopt[random.randint(0,2)]
    rankjsonobj['contextFeatures'] = [{'timeofday': timeofday, 'weather': weather, 'name': name}]
    return [name, weather, timeofday]
```


### <a name="add-all-coffee-data"></a>Adicione todos os dados do café

Esta função adiciona toda a lista de café ao objeto JSON para enviar ao pedido de Rank.

A célula não tem saída. A função `rankjsonobj` muda a quando chamada.


O exemplo das características de um único café é:

```json
{
    "id": "Cappucino",
    "features": [
    {
        "type": "hot",
        "origin": "kenya",
        "organic": "yes",
        "roast": "dark"

    }
}
```

```python
def add_action_features(rankjsonobj):
    rankjsonobj["actions"] = actionfeaturesobj
```

### <a name="compare-prediction-with-known-user-preference"></a>Comparar a previsão com a preferência conhecida do utilizador

Esta função é chamada após a Classificação API é chamada, para cada iteração.

Esta função compara a preferência do utilizador pelo café, com base no tempo e na hora do dia, com a sugestão do Personalizer para o utilizador para esses filtros. Se a sugestão corresponder, uma pontuação de 1 é devolvida, caso contrário o resultado é 0. A célula não tem saída. A função produz a pontuação quando chamada.

```python
def get_reward_from_simulated_data(name, weather, timeofday, prediction):
    if(userpref[name][weather][timeofday] == str(prediction)):
        return 1
    return 0
```

### <a name="loop-through-calls-to-rank-and-reward"></a>Loop através de chamadas para Rank and Reward

A próxima célula é o _principal_ trabalho do Caderno, obtendo um utilizador aleatório, recebendo a lista de café, enviando ambos para a API rank. Comparando a previsão com as preferências conhecidas do utilizador, enviando a recompensa de volta para o serviço Personalizer.

O loop `num_requests` corre para os tempos. O Personalizer precisa de alguns milhares de chamadas para rank and Reward para criar um modelo.

Segue-se um exemplo da JSON enviada para a API de grau. A lista de café não está completa, para a brevidade. Pode ver todo o JSON `coffee.json`para café em .

JSON enviado para a API rank:

```json
{
   'contextFeatures':[
      {
         'timeofday':'Evening',
         'weather':'Snowy',
         'name':'Alice'
      }
   ],
   'actions':[
      {
         'id':'Cappucino',
         'features':[
            {
               'type':'hot',
               'origin':'kenya',
               'organic':'yes',
               'roast':'dark'
            }
         ]
      }
        ...rest of coffee list
   ],
   'excludedActions':[

   ],
   'eventId':'b5c4ef3e8c434f358382b04be8963f62',
   'deferActivation':False
}
```

Resposta json da API rank:

```json
{
    'ranking': [
        {'id': 'Latte', 'probability': 0.85 },
        {'id': 'Iced mocha', 'probability': 0.05 },
        {'id': 'Cappucino', 'probability': 0.05 },
        {'id': 'Cold brew', 'probability': 0.05 }
    ],
    'eventId': '5001bcfe3bb542a1a238e6d18d57f2d2',
    'rewardActionId': 'Latte'
}
```

Finalmente, cada loop mostra a seleção aleatória de utilizador, tempo, hora do dia e recompensa determinada. A recompensa de 1 indica que o recurso Personalizer selecionou o tipo de café correto para o dado utilizador, tempo e hora do dia.

```console
1 Alice Rainy Morning Latte 1
```

A função utiliza:

* Classificação: uma API POST REST para [obter classificação](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank).
* Recompensa: uma API POST REST para [reportar recompensa](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward).

```python
def iterations(n, modelCheck, jsonFormat):

    i = 1

    # default reward value - assumes failed prediction
    reward = 0

    # Print out dateTime
    currentDateTime()

    # collect results to aggregate in graph
    total = 0
    rewards = []
    count = []

    # default list of user, weather, time of day
    namesopt = ['Alice', 'Bob', 'Cathy', 'Dave']
    weatheropt = ['Sunny', 'Rainy', 'Snowy']
    timeofdayopt = ['Morning', 'Afternoon', 'Evening']


    while(i <= n):

        # create unique id to associate with an event
        eventid = add_event_id(jsonFormat)

        # generate a random sample
        [name, weather, timeofday] = add_random_user_and_contextfeatures(namesopt, weatheropt, timeofdayopt, jsonFormat)

        # add action features to rank
        add_action_features(jsonFormat)

        # show JSON to send to Rank
        print('To: ', jsonFormat)

        # choose an action - get prediction from Personalizer
        response = requests.post(personalization_rank_url, headers = headers, params = None, json = jsonFormat)

        # show Rank prediction
        print ('From: ',response.json())

        # compare personalization service recommendation with the simulated data to generate a reward value
        prediction = json.dumps(response.json()["rewardActionId"]).replace('"','')
        reward = get_reward_from_simulated_data(name, weather, timeofday, prediction)

        # show result for iteration
        print(f'   {i} {currentDateTime()} {name} {weather} {timeofday} {prediction} {reward}')

        # send the reward to the service
        response = requests.post(personalization_reward_url + eventid + "/reward", headers = headers, params= None, json = { "value" : reward })

        # for every N rank requests, compute total correct  total
         total =  total + reward

        # every N iteration, get last updated model date and time
        if(i % modelCheck == 0):

            print("**** 10% of loop found")

            get_last_updated(modelLastModified)

        # aggregate so chart is easier to read
        if(i % 10 == 0):
            rewards.append( total)
            count.append(i)
             total = 0

        i = i + 1

    # Print out dateTime
    currentDateTime()

    return [count, rewards]
```

## <a name="run-for-10000-iterations"></a>Correr para 10.000 iterações
Execute o circuito personalizer para 10.000 iterações. Este é um evento de longa duração. Não feche o navegador executando o caderno. Refresque periodicamente o gráfico de métricas no portal Azure para ver o total de chamadas para o serviço. Quando se tem cerca de 20.000 chamadas, uma chamada de patente e recompensa por cada iteração do loop, as iterações são feitas.

```python
# max iterations
num_requests = 200

# check last mod date N% of time - currently 10%
lastModCheck = int(num_requests * .10)

jsonTemplate = rankactionsjsonobj

# main iterations
[count, rewards] = iterations(num_requests, lastModCheck, jsonTemplate)
```



## <a name="chart-results-to-see-improvement"></a>Resultados do gráfico para ver melhoria

Crie um `count` gráfico `rewards`a partir do e .

```python
def createChart(x, y):
    plt.plot(x, y)
    plt.xlabel("Batch of rank events")
    plt.ylabel("Correct recommendations per batch")
    plt.show()
```

## <a name="run-chart-for-10000-rank-requests"></a>Gráfico de execução para 10.000 pedidos de classificação

Executar `createChart` a função.

```python
createChart(count,rewards)
```

## <a name="reading-the-chart"></a>Ler o gráfico

Este gráfico mostra o sucesso do modelo para a atual política de aprendizagem por defeito.

![Este gráfico mostra o sucesso da atual política de aprendizagem durante a duração do teste.](./media/tutorial-azure-notebook/azure-notebook-chart-results.png)


O alvo ideal que, no final do teste, o loop está em média uma taxa de sucesso que está perto de 100 por cento menos a exploração. O valor padrão da exploração é de 20%.

`100-20=80`

Este valor de exploração encontra-se no portal Azure, para o recurso Personalizer, na página **de Configuração.**

Para encontrar uma melhor política de aprendizagem, com base nos seus dados para a Rank API, eexecute uma [avaliação offline](how-to-offline-evaluation.md) no portal para o seu loop Personalizer.

## <a name="run-an-offline-evaluation"></a>Executar uma avaliação offline

1. No portal Azure, abra a página de **Avaliações** do Recurso Personalizer.
1. Selecione **Criar Avaliação**.
1. Introduza os dados necessários do nome da avaliação e a gama de datas para a avaliação do loop. O intervalo de datas deve incluir apenas os dias em que se está a concentrar para a sua avaliação.
    ![No portal Azure, abra a página de Avaliações do Recurso Personalizer. Selecione Criar Avaliação. Introduza o nome de avaliação e o intervalo de data.](./media/tutorial-azure-notebook/create-offline-evaluation.png)

    O objetivo de executar esta avaliação offline é determinar se existe uma melhor política de aprendizagem para as funcionalidades e ações utilizadas neste ciclo. Para encontrar essa melhor política de aprendizagem, certifique-se de que a **Otimização Discovery** está ligada.

1. Selecione **OK** para iniciar a avaliação.
1. Esta página **de Avaliações** lista a nova avaliação e o seu estado atual. Dependendo da quantidade de dados que tem, esta avaliação pode demorar algum tempo. Pode voltar a esta página depois de alguns minutos para ver os resultados.
1. Quando a avaliação estiver concluída, selecione a avaliação e, em seguida, selecione **A comparação de diferentes políticas de aprendizagem**. Isto mostra as políticas de aprendizagem disponíveis e como se comportariam com os dados.
1. Selecione a política de aprendizagem mais alta da tabela e selecione **Aplicar**. Isto aplica a _melhor_ política de aprendizagem ao seu modelo e retrains.

## <a name="change-update-model-frequency-to-5-minutes"></a>Alterar a frequência do modelo de atualização para 5 minutos

1. No portal Azure, ainda no recurso Personalizer, selecione a página **De Configuração.**
1. Altere a frequência de **atualização** do modelo e **reward tempo** de espera para 5 minutos e selecione **Guardar**.

Saiba mais sobre o tempo de [espera de recompensa](concept-rewards.md#reward-wait-time) e a frequência de atualização do [modelo.](how-to-settings.md#model-update-frequency)

```python
#Verify new learning policy and times
get_service_settings()
```

Verifique se a `rewardWaitTime` saída `modelExportFrequency` é e ambos estão definidos para 5 minutos.
```console
-----checking model
<Response [200]>
{'creationTime': '0001-01-01T00:00:00+00:00', 'lastModifiedTime': '0001-01-01T00:00:00+00:00'}
-----model updated: "0001-01-01T00:00:00+00:00"
-----checking service settings
<Response [200]>
{...learning policy...}
<Response [200]>
{'rewardWaitTime': '00:05:00', 'defaultReward': 0.0, 'rewardAggregation': 'earliest', 'explorationPercentage': 0.2, 'modelExportFrequency': '00:05:00', 'logRetentionDays': -1}
User count 4
Coffee count 4
```

## <a name="validate-new-learning-policy"></a>Validar nova política de aprendizagem

Volte ao caderno Azure, e continue executando o mesmo loop, mas por apenas 2.000 iterações. Refresque periodicamente o gráfico de métricas no portal Azure para ver o total de chamadas para o serviço. Quando se tem cerca de 4.000 chamadas, uma chamada de patente e recompensa por cada iteração do loop, as iterações são feitas.

```python
# max iterations
num_requests = 2000

# check last mod date N% of time - currently 10%
lastModCheck2 = int(num_requests * .10)

jsonTemplate2 = rankactionsjsonobj

# main iterations
[count2, rewards2] = iterations(num_requests, lastModCheck2, jsonTemplate)
```

## <a name="run-chart-for-2000-rank-requests"></a>Gráfico de execução para 2.000 pedidos de classificação

Executar `createChart` a função.

```python
createChart(count2,rewards2)
```

## <a name="review-the-second-chart"></a>Reveja o segundo gráfico

O segundo gráfico deve mostrar um aumento visível nas previsões de Rank alinhadas com as preferências dos utilizadores.

![O segundo gráfico deve mostrar um aumento visível nas previsões de Rank alinhadas com as preferências dos utilizadores.](./media/tutorial-azure-notebook/azure-notebook-chart-results-happy-graph.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se não pretende continuar a série tutorial, limpe os seguintes recursos:

* Elimine o seu projeto Azure Notebook.
* Elimine o seu recurso Personalizer.

## <a name="next-steps"></a>Passos seguintes

O [caderno Jupyter e os ficheiros](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook) de dados utilizados nesta amostra estão disponíveis no repo GitHub para Personalizar.

