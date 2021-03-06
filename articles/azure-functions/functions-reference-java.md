---
title: Referência do desenvolvedor java para funções azure
description: Entenda como desenvolver funções com Java.
ms.topic: conceptual
ms.date: 09/14/2018
ms.openlocfilehash: 4b1f39ff4fd48a3ed99b34391e9cc6efdad86a5d
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673007"
---
# <a name="azure-functions-java-developer-guide"></a>Guia de desenvolvimento de Funções Azure Java

O tempo de funcionamento das Funções Azure suporta [o Java SE 8 LTS (zulu8.31.0.2-jre8.0.181-win_x64)](https://repos.azul.com/azure-only/zulu/packages/zulu-8/8u181/). Este guia contém informações sobre as complexidades da escrita de Funções Azure com Java.

Como acontece com outros idiomas, uma App de Funções pode ter uma ou mais funções. Uma função `public` Java é um método, `@FunctionName`decorado com a anotação. Este método define a entrada para uma função Java, e deve ser único num determinado pacote. Uma aplicação de função escrita em Java pode ter `@FunctionName`várias classes com múltiplos métodos públicos anotados com .

Este artigo assume que já leu a referência do desenvolvedor de [Funções Azure](functions-reference.md). Também deve completar as Funções rapidamente para criar a sua primeira função, utilizando o [Visual Studio Code](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-java) ou [maven](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-java).

## <a name="programming-model"></a>Modelo de programação 

Os conceitos de [gatilhos e encadernações](functions-triggers-bindings.md) são fundamentais para as Funções Azure. Os gatilhos iniciam a execução do seu código. As encadernações dão-lhe uma forma de passar dados e devolver dados de uma função, sem ter de escrever código de acesso personalizado a dados.

## <a name="create-java-functions"></a>Criar funções java

Para facilitar a criação de funções java, existem ferramentas e arquétipos baseados em Maven que usam modelos java pré-definidos para ajudá-lo a criar projetos com um gatilho de função específica.    

### <a name="maven-based-tooling"></a>Ferramentas baseadas em Maven

Os seguintes ambientes de desenvolvimento têm ferramentas Azure Functions que permite criar projetos de função Java: 

+ [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)
+ [Eclipse](functions-create-maven-eclipse.md)
+ [IntelliJ](functions-create-maven-intellij.md)

Os links de artigo acima mostram-lhe como criar as suas primeiras funções usando o seu IDE de eleição. 

### <a name="project-scaffolding"></a>Andaime do Projeto

Se preferir o desenvolvimento da linha de comando do Terminal, a forma `Apache Maven` mais simples de andaimes de função baseadas em Java é usar arquétipos. O arquétipo Java Maven para funções azure é publicado sob o seguinte _grupoId_:_artifactId_: [com.microsoft.azure:azure-functions-archetype](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-archetype/). 

O seguinte comando gera um novo projeto de função Java usando este arquétipo:

```
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype 
```

Para começar a usar este arquétipo, veja o início rápido de [Java.](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-java) 

## <a name="create-kotlin-functions-preview"></a>Criar funções Kotlin (pré-visualização)

Há também um arquétipo Maven para gerar funções kotlin. Este arquétipo, atualmente em pré-visualização, é publicado sob o seguinte _grupoId_:_artifactId_: [com.microsoft.azure:azure-functions-kotlin-archetype](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-kotlin-archetype/). 

O seguinte comando gera um novo projeto de função Java usando este arquétipo:

```
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-kotlin-archetype
```

Para começar a usar este arquétipo, veja o arranque rápido de [Kotlin.](functions-create-first-kotlin-maven.md)

## <a name="folder-structure"></a>Estrutura de pasta

Aqui está a estrutura de pastas de um projeto Azure Functions Java:

```
FunctionsProject
 | - src
 | | - main
 | | | - java
 | | | | - FunctionApp
 | | | | | - MyFirstFunction.java
 | | | | | - MySecondFunction.java
 | - target
 | | - azure-functions
 | | | - FunctionApp
 | | | | - FunctionApp.jar
 | | | | - host.json
 | | | | - MyFirstFunction
 | | | | | - function.json
 | | | | - MySecondFunction
 | | | | | - function.json
 | | | | - bin
 | | | | - lib
 | - pom.xml
```

_* O projeto Kotlin parece muito semelhante uma vez que ainda é Maven_

Pode utilizar um ficheiro [host.json](functions-host-json.md) partilhado para configurar a aplicação de função. Cada função tem o seu próprio ficheiro de código (.java) e ficheiro de configuração de ligação (função.json).

Pode colocar mais do que uma função num projeto. Evite colocar as suas funções em frascos separados. O `FunctionApp` directório-alvo é o que é implantado na sua aplicação de funções em Azure.

## <a name="triggers-and-annotations"></a>Gatilhos e anotações

 As funções são invocadas por um gatilho, como um pedido HTTP, um temporizador ou uma atualização aos dados. A sua função precisa de processar esse gatilho, e quaisquer outras inputs, para produzir uma ou mais saídas.

Utilize as anotações Java incluídas no pacote [com.microsoft.azure.functions.anotation.*](/java/api/com.microsoft.azure.functions.annotation) para ligar a entrada e as saídas aos seus métodos. Para mais informações, consulte os [médicos](/java/api/com.microsoft.azure.functions.annotation)de referência java .

> [!IMPORTANT] 
> Você deve configurar uma conta de Armazenamento Azure no seu [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) para executar armazenamento Azure Blob, armazenamento de fila Azure ou gatilhos de armazenamento de mesa Azure localmente.

Exemplo:

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

Aqui está o `function.json` gerado correspondente pelo [azure-functions-maven-plugin:](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin)

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.Function.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}

```

## <a name="jdk-runtime-availability-and-support"></a>Disponibilidade e suporte de tempo de execução jDK 

Para o desenvolvimento local de aplicações de função Java, descarregue e use a [Blue Zulu Enterprise para Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) Java 8 JDKs da Azul [Systems.](https://www.azul.com/downloads/azure-only/zulu/) As Funções Azure utilizam o tempo de funcionamento do Blue Java 8 JDK quando implementa as suas aplicações de funções para a nuvem.

[O suporte azure](https://azure.microsoft.com/support/) para problemas com os JDKs e aplicações de função está disponível com um plano de [suporte qualificado.](https://azure.microsoft.com/support/plans/)

## <a name="customize-jvm"></a>Personalizar JVM

As funções permitem personalizar a máquina virtual Java (JVM) usada para executar as suas funções Java. As [seguintes opções JVM](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7) são utilizadas por predefinição:

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

Pode fornecer argumentos adicionais numa `JAVA_OPTS`definição de aplicação chamada . Pode adicionar definições de aplicativos à sua aplicação de função implantada para o Azure no portal Azure ou no Azure CLI.

### <a name="azure-portal"></a>Portal do Azure

No [portal Azure,](https://portal.azure.com)utilize o [separador Definições](functions-how-to-use-azure-function-app-settings.md#settings) de Aplicação para adicionar a `JAVA_OPTS` definição.

### <a name="azure-cli"></a>CLI do Azure

Pode utilizar o comando de definição de definições de definição de definições de definição de definições de definições de definição `JAVA_OPTS`do conjunto de definições da linha de [função az,](/cli/azure/functionapp/config/appsettings) como no exemplo seguinte:

```azurecli-interactive
az functionapp config appsettings set --name <APP_NAME> \
--resource-group <RESOURCE_GROUP> \
--settings "JAVA_OPTS=-Djava.awt.headless=true"
```
Este exemplo permite o modo sem cabeça. Substitua-o `<APP_NAME>` pelo nome da `<RESOURCE_GROUP>` sua aplicação de função e pelo grupo de recursos.

> [!WARNING]  
> No [plano consumo,](functions-scale.md#consumption-plan)deve `WEBSITE_USE_PLACEHOLDER` adicionar a definição com um valor de `0`.  
Esta definição aumenta os tempos de início frio para as funções java.

## <a name="third-party-libraries"></a>Bibliotecas de terceiros 

A Azure Functions apoia a utilização de bibliotecas de terceiros. Por predefinição, todas as `pom.xml` dependências especificadas no [`mvn package`](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) seu ficheiro de projeto são automaticamente agregadas durante o objetivo. Para bibliotecas não especificadas `pom.xml` como dependências no `lib` ficheiro, coloque-as num diretório no diretório raiz da função. As dependências `lib` colocadas no diretório são adicionadas ao carregador de classe do sistema em tempo de funcionação.

A `com.microsoft.azure.functions:azure-functions-java-library` dependência é fornecida no caminho da classe por padrão, e `lib` não precisa de ser incluída no diretório. Além disso, [o azure-functions-java-worker](https://github.com/Azure/azure-functions-java-worker) adiciona dependências listadas [aqui](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies) para o caminho de classe.

## <a name="data-type-support"></a>Suporte de tipo de dados

Pode utilizar objetos Java antigos (POJOs), tipos definidos em `azure-functions-java-library`tipos de dados primitivos, como String e Integer, para se ligar a encadernações de entrada ou de saída.

### <a name="pojos"></a>POJOs

Para converter dados de entrada para POJO, o [azure-functions-java-worker](https://github.com/Azure/azure-functions-java-worker) utiliza a biblioteca [gson.](https://github.com/google/gson) Os tipos POJO utilizados como inputs para funções devem ser `public`.

### <a name="binary-data"></a>Dados binários

Ligue as inputs ou `byte[]`saídas binárias a, definindo `dataType` `binary`o campo na sua função.json para:

```java
   @FunctionName("BlobTrigger")
    @StorageAccount("AzureWebJobsStorage")
     public void blobTrigger(
        @BlobTrigger(name = "content", path = "myblob/{fileName}", dataType = "binary") byte[] content,
        @BindingName("fileName") String fileName,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Blob trigger function processed a blob.\n Name: " + fileName + "\n Size: " + content.length + " Bytes");
    }
```

Se espera valores `Optional<T>`nulos, utilize.

## <a name="bindings"></a>Enlaces

As encadernações de entrada e saída fornecem uma forma declarativa de se conectar em dados de dentro do seu código. Uma função pode ter várias encadernações de entrada e saída.

### <a name="input-binding-example"></a>Exemplo vinculativo de entrada

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { "put" }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String inputReq,
        @TableInput(name = "item", tableName = "items", partitionKey = "Example", rowKey = "{id}", connection = "AzureWebJobsStorage") TestInputData inputData
        @TableOutput(name = "myOutputTable", tableName = "Person", connection = "AzureWebJobsStorage") OutputBinding<Person> testOutputData,
    ) {
        testOutputData.setValue(new Person(httpbody + "Partition", httpbody + "Row", httpbody + "Name"));
        return "Hello, " + inputReq + " and " + inputData.getKey() + ".";
    }

    public static class TestInputData {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
    public static class Person {
        public String PartitionKey;
        public String RowKey;
        public String Name;

        public Person(String p, String r, String n) {
            this.PartitionKey = p;
            this.RowKey = r;
            this.Name = n;
        }
    }
}
```

Invoca esta função com um pedido HTTP. 
- HTTP solicitar a carga `String` útil é `inputReq`passada como um para o argumento .
- Uma entrada é recuperada do armazenamento `TestInputData` da mesa, e é passada quanto ao argumento `inputData`.

Para receber um lote de inputs, `POJO[]` `List<String>`pode `List<POJO>`ligar-se a, `String[]`ou .

```java
@FunctionName("ProcessIotMessages")
    public void processIotMessages(
        @EventHubTrigger(name = "message", eventHubName = "%AzureWebJobsEventHubPath%", connection = "AzureWebJobsEventHubSender", cardinality = Cardinality.MANY) List<TestEventData> messages,
        final ExecutionContext context)
    {
        context.getLogger().info("Java Event Hub trigger received messages. Batch size: " + messages.size());
    }
    
    public class TestEventData {
    public String id;
}

```

Esta função é desencadeada sempre que há novos dados no centro de eventos configurado. Como `cardinality` a função está definida para `MANY`, a função recebe um lote de mensagens do centro do evento. `EventData`do centro de `TestEventData` eventos é convertido para a execução da função.

### <a name="output-binding-example"></a>Exemplo de encadernação de saída

Pode ligar uma ligação de saída `$return`ao valor de devolução utilizando . 

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("copy")
    @StorageAccount("AzureWebJobsStorage")
    @BlobOutput(name = "$return", path = "samples-output-java/{name}")
    public static String copy(@BlobTrigger(name = "blob", path = "samples-input-java/{name}") String content) {
        return content;
    }
}
```

Se existirem várias encadernações de saída, utilize o valor de devolução para apenas uma delas.

Para enviar vários `OutputBinding<T>` valores `azure-functions-java-library` de saída, utilize o uso definido na embalagem. 

```java
@FunctionName("QueueOutputPOJOList")
    public HttpResponseMessage QueueOutputPOJOList(@HttpTrigger(name = "req", methods = { HttpMethod.GET,
            HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "itemsOut", queueName = "test-output-java-pojo", connection = "AzureWebJobsStorage") OutputBinding<List<TestData>> itemsOut, 
            final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");
       
        String query = request.getQueryParameters().get("queueMessageId");
        String queueMessageId = request.getBody().orElse(query);
        itemsOut.setValue(new ArrayList<TestData>());
        if (queueMessageId != null) {
            TestData testData1 = new TestData();
            testData1.id = "msg1"+queueMessageId;
            TestData testData2 = new TestData();
            testData2.id = "msg2"+queueMessageId;

            itemsOut.getValue().add(testData1);
            itemsOut.getValue().add(testData2);

            return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + queueMessageId).build();
        } else {
            return request.createResponseBuilder(HttpStatus.INTERNAL_SERVER_ERROR)
                    .body("Did not find expected items in CosmosDB input list").build();
        }
    }

     public static class TestData {
        public String id;
    }
```

Invoca esta função num HttpRequest. Escreve vários valores para o armazenamento de fila.

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage e HttpResponseMessage

 Estes são `azure-functions-java-library`definidos em . São tipos de ajuda para trabalhar com funções httpTrigger.

| Tipo especializado      |       Destino        | Uso típico                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    Acionador HTTP     | Obtém método, cabeçalhos ou consultas |
| `HttpResponseMessage` | Ligação de saída http | Estatuto de devoluções que não seja 200   |

## <a name="metadata"></a>Metadados

Poucos gatilhos enviam [metadados](/azure/azure-functions/functions-triggers-bindings) de gatilho juntamente com dados de entrada. Pode usar aanotação `@BindingName` para ligar ao gatilho de metadados.


```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.functions.annotation.*;


public class Function {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { "get", "post" }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```
No exemplo anterior, `queryValue` o parâmetro de corda de `name` consulta no URL `http://{example.host}/api/metadata?name=test`de pedido HTTP, . Aqui está outro exemplo, mostrando `Id` como se ligar a partir de metadados de gatilho de fila.

```java
 @FunctionName("QueueTriggerMetadata")
    public void QueueTriggerMetadata(
        @QueueTrigger(name = "message", queueName = "test-input-java-metadata", connection = "AzureWebJobsStorage") String message,@BindingName("Id") String metadataId,
        @QueueOutput(name = "output", queueName = "test-output-java-metadata", connection = "AzureWebJobsStorage") OutputBinding<TestData> output,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Queue trigger function processed a message: " + message + " with metadaId:" + metadataId );
        TestData testData = new TestData();
        testData.id = metadataId;
        output.setValue(testData);
    }
```

> [!NOTE]
> O nome fornecido na anotação precisa de corresponder à propriedade dos metadados.

## <a name="execution-context"></a>Contexto de execução

`ExecutionContext`, definido `azure-functions-java-library`no , contém métodos de ajuda para comunicar com as funções tempo de funcionamento.

### <a name="logger"></a>Madeireiro

Utilizar, `getLogger`definido, `ExecutionContext`para escrever registos a partir do código de função.

Exemplo:

```java

import com.microsoft.azure.functions.*;
import com.microsoft.azure.functions.annotation.*;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received by function " + context.getFunctionName() + " with invocation " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="view-logs-and-trace"></a>Ver troncos e vestígios

Pode utilizar o CLI Azure para transmitir stdout e stderr logging de Java, bem como outras explorações de registo de aplicações. 

Aqui está como configurar a sua aplicação de função para escrever registo de aplicações utilizando o Azure CLI:

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

Para transmitir a saída de registo para a sua aplicação de função utilizando o Azure CLI, abra um novo pedido de comando, sessão de Bash ou Terminal, e introduza o seguinte comando:

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
O comando da cauda de [log az webapp](/cli/azure/webapp/log) tem opções para filtrar a saída utilizando a opção. `--provider` 

Para descarregar os ficheiros de registo como um único ficheiro ZIP utilizando o Azure CLI, abra um novo pedido de comando, sessão de Bash ou Terminal, e introduza o seguinte comando:

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

Deve ter ativado o registo do sistema de ficheiros no portal Azure ou no Azure CLI antes de executar este comando.

## <a name="environment-variables"></a>Variáveis de ambiente

Em Funções, [as definições](functions-app-settings.md)de aplicativos , tais como cordas de ligação ao serviço, são expostas como variáveis ambientais durante a execução. Pode aceder a estas `System.getenv("AzureWebJobsStorage")`definições utilizando.

O exemplo seguinte obtém a definição de [aplicação,](functions-how-to-use-azure-function-app-settings.md#settings)com a chave denominada: `myAppSetting`

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("My app setting value: "+ System.getenv("myAppSetting"));
        return String.format(req);
    }
}

```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o desenvolvimento das Funções Azure Java, consulte os seguintes recursos:

* [Best Practices for Azure Functions (Melhores Práticas para as Funções do Azure)](functions-best-practices.md)
* [Referência para programadores das Funções do Azure](functions-reference.md)
* [Funções Azure desencadeiam e encadernam](functions-triggers-bindings.md)
* Desenvolvimento local e depuração com [Código de Estúdio Visual,](https://code.visualstudio.com/docs/java/java-azurefunctions) [IntelliJ](functions-create-maven-intellij.md)e [Eclipse](functions-create-maven-eclipse.md)
* [Funções remotas de Debug Java Azure com Código de Estúdio Visual](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Plugin Maven para funções azure](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* Agilize a `azure-functions:add` criação de funções através do objetivo e prepare um diretório de encenação para a implementação de [ficheiros ZIP](deployment-zip-push.md).
