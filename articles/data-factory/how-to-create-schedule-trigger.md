---
title: Criar gatilhos de horário na Fábrica de Dados Azure
description: Aprenda a criar um gatilho na Azure Data Factory que executa um pipeline em um horário.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/23/2018
ms.openlocfilehash: 127db8a484b9624586dea70c44af3bc84b3fc84e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73673778"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>Criar um gatilho que executa um oleoduto em um horário
Este artigo fornece informações sobre o gatilho do horário e os passos para criar, iniciar e monitorizar um gatilho de horário. Para outros tipos de gatilhos, consulte a [execução do gasoduto e os gatilhos](concepts-pipeline-execution-triggers.md).

Ao criar um gatilho de horário, especifice um horário (data de início, recorrência, data de fim, etc.) para o gatilho e associe-se a um gasoduto. Os pipelines e os acionadores têm uma relação muitos para muitos. Múltiplos acionadores podem arrancar um pipeline individual. Um acionador único pode arrancar vários pipelines.

As seguintes secções fornecem passos para criar um gatilho de horário de diferentes maneiras. 

## <a name="data-factory-ui"></a>IU do Data Factory
Pode criar um gatilho de **horário** para agendar um gasoduto para funcionar periodicamente (hora, diariamente, etc.). 

> [!NOTE]
> Para uma completa passagem pela criação de um oleoduto e um gatilho de horário, associando o gatilho ao oleoduto e funcionando e monitorizando o gasoduto, consulte [Quickstart: crie uma fábrica](quickstart-create-data-factory-portal.md)de dados utilizando data Factory UI .

1. Mude para o separador **Editar**. 

    ![Mudar para o separador Editar](./media/how-to-create-schedule-trigger/switch-edit-tab.png)
1. Clique em **Acionador**, no menu, e clique em **Novo/Editar**. 

    ![Menu Novo acionador](./media/how-to-create-schedule-trigger/new-trigger-menu.png)
2. Na página **Adicionar Acionadores**, clique em **Escolher acionador...** e clique em **Novo**. 

    ![Adicionar acionadores - acionador novo](./media/how-to-create-schedule-trigger/add-trigger-new-button.png)
3. Na página **New Trigger,** faça os seguintes passos: 

    1. Confirme que **a Agenda** está selecionada para **Tipo**. 
    2. Especifique a data de início do gatilho para a data de **início (UTC)**. Está definido para a data atual por defeito. 
    3. Especifique **a recorrência** para o gatilho. Selecione um dos valores da lista de lançamentos (A cada minuto, hora, dia, semanal e mensal). Introduza o multiplicador na caixa de texto. Por exemplo, se pretender que o gatilho seja executado uma vez por cada 15 minutos, selecione **Cada Minuto**e introduza **15** na caixa de texto. 
    4. Para o campo **Fim,** se não quiser especificar uma data de fim para o gatilho, selecione **No End**. Para especificar a data limite, selecione **A Data**de Data , e especifique a data de fim e clique **em Aplicar**. Nenhuma execução de pipeline tem custos associados. Se estiver a testar, é melhor garantir que o gasoduto seja acionado apenas algumas vezes. No entanto, certifique-se de que há tempo suficiente para o pipeline ser executado entre a hora de publicação e a hora de fim. O acionador só entra em vigor depois de publicar a solução no Data Factory e não quando guarda o acionador na IU.

        ![Definições do acionador](./media/how-to-create-schedule-trigger/trigger-settings.png)
4. Na janela **New Trigger,** verifique a opção **Ativada** e clique em **Seguinte**. Pode utilizar esta caixa de verificação para desativar o gatilho mais tarde. 

    ![Definições do acionador - botão seguinte](./media/how-to-create-schedule-trigger/trigger-settings-next.png)
5. Na página **Novo Acionador**, reveja a mensagem de aviso e clique em **Concluir**.

    ![Definições do acionador - botão concluir](./media/how-to-create-schedule-trigger/new-trigger-finish.png)
6. Clique em **Publicar** para publicar as alterações no Data Factory. Até publicar alterações na Data Factory, o gatilho não começa a acionar as operações do gasoduto. 

    ![Botão Publicar](./media/how-to-create-schedule-trigger/publish-2.png)
8. Mude para o separador **Monitorizar**, no lado esquerdo. Clique em **Atualizar** para atualizar a lista. Vê-se o gasoduto a ser acionado pelo gatilho programado. Repare nos valores na coluna **Acionado Por**. Se utilizar a opção **Trigger Now,** verá o gatilho manual ser executado na lista. 

    ![Monitorizar execuções acionadas](./media/how-to-create-schedule-trigger/monitor-triggered-runs.png)
9. Clique na seta para baixo junto a **Execuções do Pipeline** para mudar para a vista **Execuções Acionadas**. 

    ![Monitorizar execuções acionadas](./media/how-to-create-schedule-trigger/monitor-trigger-runs.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Esta secção mostra-lhe como usar o Azure PowerShell para criar, iniciar e monitorizar um gatilho de horário. Para ver esta amostra a funcionar, primeiro passe pelo [Quickstart: Crie uma fábrica de dados utilizando o Azure PowerShell](quickstart-create-data-factory-powershell.md). Em seguida, adicione o seguinte código ao método principal, que cria e inicia um gatilho de horário que funciona a cada 15 minutos. O gatilho está associado a um pipeline chamado **Adfv2QuickStartPipeline** que cria como parte do Quickstart.

1. Crie um ficheiro JSON chamado **MyTrigger.json** na pasta C:\ADFv2QuickStartPSH\ com o seguinte conteúdo:

    > [!IMPORTANT]
    > Antes de guardar o ficheiro JSON, detete o valor do elemento **startTime** para o tempo atual utc. Detete o valor do elemento **endTime** para uma hora depois do tempo atual utc.

    ```json   
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Minute",
                    "interval": 15,
                    "startTime": "2017-12-08T00:00:00",
                    "endTime": "2017-12-08T01:00:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "Adfv2QuickStartPipeline"
                    },
                    "parameters": {
                        "inputPath": "adftutorial/input",
                        "outputPath": "adftutorial/output"
                    }
                }
            ]
        }
    }
    ```

    No corte JSON:
    - O elemento **tipo** do gatilho está definido para "ScheduleTrigger".
    - O elemento **de frequência** está definido para "Minuto" e o elemento **de intervalo** está definido para 15. Portanto, o gatilho funciona o gasoduto a cada 15 minutos entre os tempos de início e de fim.
    - O elemento **tempo final** é uma hora após o valor do elemento tempo de **arranque.** Portanto, o gatilho funciona o gasoduto 15 minutos, 30 minutos e 45 minutos após a hora de início. Não se esqueça de atualizar a hora de início para a atual hora utc, e o tempo final para uma hora depois da hora de início. 
    - O gatilho está associado ao gasoduto **Adfv2QuickStartPipeline.** Para associar vários oleodutos a um gatilho, adicione mais secções de **pipelineReference.**
    - O gasoduto no Quickstart tem dois valores de **parâmetros:** **inputPath** e **outputPath**. Portanto, passa-se valores para estes parâmetros a partir do gatilho.

2. Criar um gatilho utilizando o **cmdlet Set-AzDataFactoryV2Trigger:**

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```

3. Confirme que o estado do gatilho é **interrompido** utilizando o cmdlet **Get-AzDataFactoryV2Trigger:**

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

4. Inicie o gatilho utilizando o cmdlet **Start-AzDataFactoryV2Trigger:**

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

5. Confirme que o estado do gatilho é **iniciado** utilizando o cmdlet **Get-AzDataFactoryV2Trigger:**

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

6.  Obtenha o gatilho em Azure PowerShell utilizando o cmdlet **Get-AzDataFactoryV2TriggerRun.** Para obter a informação sobre o gatilho, execute periodicamente o seguinte comando. Atualize os valores **TriggerRunStartedAfter** e **TriggerRunStartedBefore** para corresponder aos valores na definição do gatilho:

    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
    Para monitorizar as condutas do gatilho e as condutas de gasoduto saem no portal Azure, consulte o [gasoduto Monitor .](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)


## <a name="net-sdk"></a>SDK .NET
Esta secção mostra-lhe como usar o SDK .NET para criar, iniciar e monitorizar um gatilho. Para ver esta amostra a funcionar, primeiro passe pelo [Quickstart: Crie uma fábrica de dados utilizando o .NET SDK](quickstart-create-data-factory-dot-net.md). Em seguida, adicione o seguinte código ao método principal, que cria e inicia um gatilho de horário que funciona a cada 15 minutos. O gatilho está associado a um pipeline chamado **Adfv2QuickStartPipeline** que cria como parte do Quickstart.

Para criar e iniciar um gatilho de horário que funciona a cada 15 minutos, adicione o seguinte código ao método principal:

```csharp
            // Create the trigger
            Console.WriteLine("Creating the trigger");

            // Set the start time to the current UTC time
            DateTime startTime = DateTime.UtcNow;

            // Specify values for the inputPath and outputPath parameters
            Dictionary<string, object> pipelineParameters = new Dictionary<string, object>();
            pipelineParameters.Add("inputPath", "adftutorial/input");
            pipelineParameters.Add("outputPath", "adftutorial/output");

            // Create a schedule trigger
            string triggerName = "MyTrigger";
            ScheduleTrigger myTrigger = new ScheduleTrigger()
            {
                Pipelines = new List<TriggerPipelineReference>()
                {
                    // Associate the Adfv2QuickStartPipeline pipeline with the trigger
                    new TriggerPipelineReference()
                    {
                        PipelineReference = new PipelineReference(pipelineName),
                        Parameters = pipelineParameters,
                    }
                },
                Recurrence = new ScheduleTriggerRecurrence()
                {
                    // Set the start time to the current UTC time and the end time to one hour after the start time
                    StartTime = startTime,
                    TimeZone = "UTC",
                    EndTime = startTime.AddHours(1),
                    Frequency = RecurrenceFrequency.Minute,
                    Interval = 15,
                }
            };

            // Now, create the trigger by invoking the CreateOrUpdate method
            TriggerResource triggerResource = new TriggerResource()
            {
                Properties = myTrigger
            };
            client.Triggers.CreateOrUpdate(resourceGroup, dataFactoryName, triggerName, triggerResource);

            // Start the trigger
            Console.WriteLine("Starting the trigger");
            client.Triggers.Start(resourceGroup, dataFactoryName, triggerName);
```

Para monitorizar uma execução do gatilho, `Console.WriteLine` adicione o seguinte código antes da última declaração na amostra:

```csharp
            // Check that the trigger runs every 15 minutes
            Console.WriteLine("Trigger runs. You see the output every 15 minutes");

            for (int i = 0; i < 3; i++)
            {
                System.Threading.Thread.Sleep(TimeSpan.FromMinutes(15));
                List<TriggerRun> triggerRuns = client.Triggers.ListRuns(resourceGroup, dataFactoryName, triggerName, DateTime.UtcNow.AddMinutes(-15 * (i + 1)), DateTime.UtcNow.AddMinutes(2)).ToList();
                Console.WriteLine("{0} trigger runs found", triggerRuns.Count);

                foreach (TriggerRun run in triggerRuns)
                {
                    foreach (KeyValuePair<string, string> triggeredPipeline in run.TriggeredPipelines)
                    {
                        PipelineRun triggeredPipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, triggeredPipeline.Value);
                        Console.WriteLine("Pipeline run ID: {0}, Status: {1}", triggeredPipelineRun.RunId, triggeredPipelineRun.Status);
                        List<ActivityRun> runs = client.ActivityRuns.ListByPipelineRun(resourceGroup, dataFactoryName, triggeredPipelineRun.RunId, run.TriggerRunTimestamp.Value, run.TriggerRunTimestamp.Value.AddMinutes(20)).ToList();
                    }
                }
            }
```

Para monitorizar as condutas do gatilho e as condutas de gasoduto saem no portal Azure, consulte o [gasoduto Monitor .](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)


## <a name="python-sdk"></a>SDK Python
Esta secção mostra-lhe como usar o SDK Python para criar, iniciar e monitorizar um gatilho. Para ver esta amostra a funcionar, primeiro passe pelo [Quickstart: Crie uma fábrica de dados utilizando o Python SDK](quickstart-create-data-factory-python.md). Em seguida, adicione o seguinte bloco de código após o bloco de código "monitoramento do pipeline executar" no script Python. Este código cria um gatilho de horário que funciona a cada 15 minutos entre os tempos de início e fim especificados. Atualize a **variável start_time** ao atual tempo utc, e a **variável end_time** para uma hora depois do tempo atual utc.

```python
    # Create a trigger
    tr_name = 'mytrigger'
    scheduler_recurrence = ScheduleTriggerRecurrence(frequency='Minute', interval='15',start_time='2017-12-12T04:00:00', end_time='2017-12-12T05:00:00', time_zone='UTC')
    pipeline_parameters = {'inputPath':'adftutorial/input', 'outputPath':'adftutorial/output'}
    pipelines_to_run = []
    pipeline_reference = PipelineReference('copyPipeline')
    pipelines_to_run.append(TriggerPipelineReference(pipeline_reference, pipeline_parameters))
    tr_properties = ScheduleTrigger(description='My scheduler trigger', pipelines = pipelines_to_run, recurrence=scheduler_recurrence)    
    adf_client.triggers.create_or_update(rg_name, df_name, tr_name, tr_properties)

    # Start the trigger
    adf_client.triggers.start(rg_name, df_name, tr_name)
```

Para monitorizar as condutas do gatilho e as condutas de gasoduto saem no portal Azure, consulte o [gasoduto Monitor .](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)

## <a name="azure-resource-manager-template"></a>Modelo Azure Resource Manager
Pode utilizar um modelo de Gestor de Recursos Azure para criar um gatilho. Para obter instruções passo a passo, consulte Criar uma fábrica de [dados Azure utilizando um modelo](quickstart-create-data-factory-resource-manager-template.md)de Gestor de Recursos .  

## <a name="pass-the-trigger-start-time-to-a-pipeline"></a>Passe a hora de início do gatilho para um oleoduto
A versão 1 da Azure Data Factory suporta a leitura ou a escrita de dados divididos utilizando as variáveis do sistema: **SliceStart,** **SliceEnd,** **WindowStart**, e **WindowEnd**. Na versão atual da Azure Data Factory, pode conseguir este comportamento utilizando um parâmetro de pipeline. A hora de início e a hora programada para o gatilho são definidas como o valor para o parâmetro do gasoduto. No exemplo seguinte, o tempo programado para o gatilho é passado como um valor para o parâmetro time do gasoduto **programado:**

```json
"parameters": {
    "scheduledRunTime": "@trigger().scheduledTime"
}
```

## <a name="json-schema"></a>Esquema jSON
A seguinte definição jSON mostra-lhe como criar um gatilho de horário com agendamento e recorrência:

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Month>>,
        "interval": <<int>>,             // Optional, specifies how often to fire (default to 1)
        "startTime": <<datetime>>,
        "endTime": <<datetime - optional>>,
        "timeZone": "UTC"
        "schedule": {                    // Optional (advanced scheduling specifics)
          "hours": [<<0-23>>],
          "weekDays": [<<Monday-Sunday>>],
          "minutes": [<<0-59>>],
          "monthDays": [<<1-31>>],
          "monthlyOccurrences": [
               {
                    "day": <<Monday-Sunday>>,
                    "occurrence": <<1-5>>
               }
           ]
        }
      }
    },
   "pipelines": [
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "<Name of your pipeline>"
                },
                "parameters": {
                    "<parameter 1 Name>": {
                        "type": "Expression",
                        "value": "<parameter 1 Value>"
                    },
                    "<parameter 2 Name>" : "<parameter 2 Value>"
                }
           }
      ]
  }
}
```

> [!IMPORTANT]
>  A propriedade **parameters** é uma propriedade obrigatória do elemento **pipelines**. Se o seu pipeline não utiliza parâmetros, deve incluir uma definição JSON vazia para a propriedade **parameters**.


### <a name="schema-overview"></a>Descrição geral do esquema
A tabela que se segue fornece uma descrição geral de alto nível dos principais elementos do esquema relacionados com a periodicidade e o agendamento de um acionador:

| Propriedade JSON | Descrição |
|:--- |:--- |
| **startTime** | Um valor de data/hora. Para agendamentos simples, o valor da propriedade **startTime** aplica-se à primeira ocorrência. Para agendamentos complexos, o acionador é iniciado imediatamente a seguir ao valor especificado em **startTime**. |
| **endTime** | A data e hora de fim do acionador. O acionador não é executado após a data e hora de fim especificadas. O valor da propriedade não pode situar-se no passado. Esta propriedade é opcional. |
| **timeZone** | O fuso horário. Atualmente, o fuso horário UTC é o único suportado. |
| **recurrence** | Um objeto de periodicidade que especifica as regras de periodicidade do acionador. O objeto de periodicidade suporta os elementos **frequency**, **interval**, **endTime**, **count** e **schedule**. Quando um objeto de periodicidade é definido, o elemento **frequency** é obrigatório. Os outros elementos do objeto de periodicidade são opcionais. |
| **frequência** | A unidade que se refere à frequência com que o acionador voltar a ocorrer. Os valores suportados incluem "minute", "hour", "day", "week" e "month". |
| **intervalo** | Um valor inteiro positivo que indica o intervalo do valor **frequency**, que determina o número de vezes que o acionador é executado. Por exemplo, se o valor de **interval** for 3 e o de **frequency** for "week", o acionador repete-se de três em três semanas. |
| **agenda** | A agenda de periodicidade do acionador. Um acionador que tenha um valor **frequency** especificado modifica a respetiva periodicidade com base numa agenda de periodicidade. A propriedade **schedule** contém modificações da periodicidade baseadas em minutos, horas, dias de semana, dias do mês e número da semana.


### <a name="schema-defaults-limits-and-examples"></a>Predefinições, limites e exemplos do esquema

| Propriedade JSON | Tipo | Necessário | Valor predefinido | Valores válidos | Exemplo |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **startTime** | Cadeia | Sim | Nenhuma | Datas-Horas ISO 8601 | `"startTime" : "2013-01-09T09:30:00-08:00"` |
| **recurrence** | Objeto | Sim | Nenhuma | Objeto de periodicidade | `"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **intervalo** | Número | Não | 1 | 1 a 1000 | `"interval":10` |
| **endTime** | Cadeia | Sim | Nenhuma | Um valor de data/hora que representa uma hora no futuro. | `"endTime" : "2013-02-09T09:30:00-08:00"` |
| **agenda** | Objeto | Não | Nenhuma | Objeto da agenda | `"schedule" : { "minute" : [30], "hour" : [8,17] }` |

### <a name="starttime-property"></a>Propriedade startTime
A tabela que se segue mostra o modo como a propriedade **startTime** controla a execução de um acionador:

| valor de startTime | Periodicidade sem agenda | Periodicidade com agenda |
|:--- |:--- |:--- |
| Hora de início no passado | Calcula a primeira hora de execução no futuro após a hora de início e é executada nessa hora.<br/><br/>Executa as execuções subsequentes com base no cálculo da última hora de execução.<br/><br/>Veja o exemplo a seguir à tabela. | O acionador é iniciado _imediatamente a seguir _ à hora de início especificada. A primeira ocorrência tem por base a agenda calculada a partir da hora de início.<br/><br/>Executa as execuções subsequentes com base na agenda de periodicidade. |
| Hora de início no futuro ou no presente | É executada uma vez na hora de início especificada.<br/><br/>Executa as execuções subsequentes com base no cálculo da última hora de execução. | O gatilho começa _o mais cedo_ que a hora de início especificada. A primeira ocorrência tem por base a agenda calculada a partir da hora de início.<br/><br/>Executa as execuções subsequentes com base na agenda de periodicidade. |

Vejamos um exemplo do que acontece quando a hora de início (startTime) se situa no passado, com periodicidade, mas sem agenda. Parta do princípio de que a hora atual é `2017-04-08 13:00`, a hora de início é `2017-04-07 14:00` e a periodicidade é de dois em dois dias. (O valor **da recorrência** é definido definindo a propriedade de **frequência** para "dia" e a propriedade **de intervalo** para 2.) Note que o valor do tempo de **partida** está no passado e ocorre antes do tempo atual.

Nestas condições, a primeira execução é em `2017-04-09 at 14:00`. O motor do Scheduler calcula as ocorrências de execução a partir da hora de início. Quaisquer instâncias no passado são eliminadas. O motor utiliza a instância seguinte que ocorre no futuro. Neste cenário, a hora de início é `2017-04-07 at 2:00pm`, pelo que a instância seguinte é dali a dois dias a contar dessa hora, ou seja, `2017-04-09 at 2:00pm`.

A primeira hora de execução é igual, mesmo que o valor de **startTime** seja `2017-04-05 14:00` ou `2017-04-01 14:00`. Após a primeira execução, as execuções subsequentes são calculadas com base na agenda. Por conseguinte, as execuções subsequentes são a `2017-04-11 at 2:00pm`, depois a `2017-04-13 at 2:00pm`, a seguir a `2017-04-15 at 2:00pm` e assim sucessivamente.

Por último, quando as horas ou os minutos não se encontram definidos na agenda para um acionador, as horas ou minutos da primeira execução são utilizados como as predefinições.

### <a name="schedule-property"></a>Propriedade schedule
Por um lado, a utilização de agendas pode limitar o número de execuções de acionadores. Por exemplo, se um acionador com uma frequência mensal estiver agendado de modo a ser executado apenas no dia 31, o acionador só é executado nos meses que têm 31 dias.

Por outro lado, as agendas também podem expandir o número de execuções de acionadores. Por exemplo, um acionador com uma frequência mensal que esteja agendado de maneira a ser executado nos dias 1 e 2 do mês, é executado nos 1º e 2º dias do mês, em vez de uma vez por mês.

Se forem especificados vários elementos **schedule**, a ordem de avaliação é feita da definição de agenda maior para a mais pequena. A avaliação começa pelo número da semana, seguindo-se o dia do mês, o dia da semana, a hora e, por último, o minuto.

A tabela seguinte descreve os elementos de **schedule** de forma detalhada:


| Elemento JSON | Descrição | Valores válidos |
|:--- |:--- |:--- |
| **minutes** | Minutos da hora em que o acionador é executado. | <ul><li>Número inteiro</li><li>Matriz de números inteiros</li></ul>
| **horas** | Horas do dia em que o acionador é executado. | <ul><li>Número inteiro</li><li>Matriz de números inteiros</li></ul> |
| **weekDays** | Dias da semana em que o acionador é executado. O valor pode ser especificado com uma frequência semanal apenas. | <ul><li>Segunda-feira, terça-feira, quarta-feira, quinta-feira, sexta-feira, sábado, domingo</li><li>Matriz de valores de dia (o tamanho máximo da matriz é 7)</li><li>Os valores de dia não são sensíveis às maiúsculas e minúsculas</li></ul> |
| **monthlyOccurrences** | Dias do mês em que o acionador é executado. O valor pode ser especificado com uma frequência mensal apenas. | <ul><li>Conjunto de objetos `{ "day": day,  "occurrence": occurrence }` **mensais Ocorrência:** .</li><li>O atributo **day** é o dia da semana em que o acionador é executado. Por exemplo, uma propriedade **monthlyOccurrences** com um valor **day** igual a `{Sunday}` significa todos os domingos do mês. O atributo **day** é obrigatório.</li><li>O atributo **occurrence** é a ocorrência do valor **day** especificado durante o mês. Por exemplo, uma propriedade **monthlyOccurrences** com os valores **day** e **occurrence** iguais a `{Sunday, -1}` significa o último domingo do mês. O atributo **occurrence** é opcional.</li></ul> |
| **monthDays** | Dia do mês em que o acionador é executado. O valor pode ser especificado com uma frequência mensal apenas. | <ul><li>Qualquer valor <= -1 e >= -31</li><li>Qualquer valor >= 1 e <= 31</li><li>Matriz de valores</li></ul> |


## <a name="examples-of-trigger-recurrence-schedules"></a>Exemplos de agendas de periodicidade do acionador
Esta secção mostra exemplos de agendas de periodicidade e centra-se no objeto **schedule** e respetivos elementos.

Os exemplos partem do princípio de que o valor **interval** é igual a 1 e que o valor **frequency** está correto de acordo com a definição da agenda. Por exemplo, não pode ter um valor **frequency** igual a "day" e ter simultaneamente uma modificação "monthDays" no objeto **schedule**. Este género de restrições é mencionado na tabela da secção anterior.

| Exemplo | Descrição |
|:--- |:--- |
| `{"hours":[5]}` | Executar todos os dias às 5:00. |
| `{"minutes":[15], "hours":[5]}` | Executar todos os dias às 5:15. |
| `{"minutes":[15], "hours":[5,17]}` | Executar todos os dias às 5:15 e 17:15. |
| `{"minutes":[15,45], "hours":[5,17]}` | Executar todos os dias às 5:15, 5:45, 17:15 e 17:45. |
| `{"minutes":[0,15,30,45]}` | Executar de 15 em 15 minutos. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | Executar hora a hora. Este acionador é executado de hora em hora. Os minutos são controlados pelo valor de **startTime**, quando é especificado um valor. Se não for especificado qualquer valor, os minutos são controlados pela hora de criação. Por exemplo, se a hora de início ou a hora de criação (a que se aplicar) for 12:25, o acionador é executado às 00:25, 01:25, 02:25 e assim sucessivamente até às 23:25.<br/><br/>Esta agenda equivale a ter um acionador com um valor **frequency** igual a "hour", um valor **interval** igual a 1 e zero propriedades **schedule**.  Esta agenda pode ser utilizada com valores **frequency** e **interval** diferentes para criar outros acionadores. Por exemplo, quando o valor **frequency** é igual a "month", a agenda é executada apenas uma vez por mês, em vez de todos os dias, como acontece quando o valor **frequency** é igual a "day". |
| `{"minutes":[0]}` | Executar de hora a hora, à hora certa. Este acionador é executado de hora a hora, à hora certa, começando às 00:00, 1:00, 2:00 e assim sucessivamente.<br/><br/>Esta agenda equivale a ter um acionador com um valor **frequency** igual a "hour" e um valor **startTime** de zero minutos, ou zero propriedades **schedule**, mas um valor **frequency** igual a "day". Se o valor **frequency** for igual a "week" ou "month", a agenda é executada um dia por semana ou um dia por mês apenas, respetivamente. |
| `{"minutes":[15]}` | Executar de hora a hora, 15 minutos após a hora. Este acionador é executado de hora a hora, 15 minutos após a hora, começando às 00:15, 1:15, 2:15 e assim sucessivamente até terminar às 23:15. |
| `{"hours":[17], "weekDays":["saturday"]}` | Executar às 17:00 de sábado todas as semanas. |
| `{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Executar às 17:00 de segunda-feira, quarta-feira e sexta-feira todas as semanas. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Executar às 17:15 e 17:45 de segunda-feira, quarta-feira e sexta-feira todas as semanas. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Executar de 15 em 15 minutos nos dias de semana. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Executar de 15 em 15 minutos nos dias de semana, entre as 9:00 e as 16:45. |
| `{"weekDays":["tuesday", "thursday"]}` | Executar às terças-feiras e quintas-feiras à hora de início especificada. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` | Executar às 6:00 do dia 28 de cada mês (pressupondo um valor **frequency** igual a "month"). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` | Executar às 6:00 no último dia do mês. Para executar um acionador no último dia de um mês, utilize -1 em vez de dia 28, 29, 30 ou 31. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | Executar às 6:00 no primeiro e último dias de cada mês. |
| `{monthDays":[1,14]}` | Executar no primeiro e 14.º dias de cada mês à hora de início especificada. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Executar na primeira sexta-feira de cada mês às 5:00. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Executar na primeira sexta-feira de cada mês à hora de início especificada. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | Executar na terceira sexta-feira do fim do mês, todos os meses, à hora de início especificada. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Executar na primeira e última sextas-feiras de cada mês às 5:15. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Executar na primeira e última sextas-feiras de cada mês à hora de início especificada. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | Executar na quinta sexta-feira de cada mês à hora de início especificada. Quando um mês não tiver cinco sextas-feiras, o pipeline não é executado, uma vez que está agendado para ser executado apenas à quinta sexta-feira. Para executar o acionador na última sexta-feira do mês, considere utilizar -1 em vez de 5 para o valor **occurrence**. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | Executar de 15 em 15 minutos na última sexta-feira do mês. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | Executar às 5:15, 5:45, 17:15 e 17:45 na terceira quarta-feira de cada mês. |


## <a name="next-steps"></a>Passos seguintes
Para obter informações detalhadas sobre os gatilhos, consulte a [execução do Gasoduto e os gatilhos](concepts-pipeline-execution-triggers.md#triggers).
