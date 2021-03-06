---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: ad5c51b3d373947e8a09762b0cb27afff990e6da
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "69906442"
---
[!INCLUDE [Prerequisites](prerequisites-java.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="initialize-a-project-with-gradle"></a>Inicializar um projeto com Gradle

Vamos começar por criar um diretório de trabalho para este projeto. A partir da linha de comando (ou terminal), executar este comando:

```console
mkdir transliterate-sample
cd transliterate-sample
```

A seguir, vais rubricar um projeto Gradle. Este comando criará ficheiros de construção essenciais para gradle, o mais importante, o `build.gradle.kts`, que é usado no tempo de execução para criar e configurar a sua aplicação. Execute este comando a partir do seu diretório de trabalho:

```console
gradle init --type basic
```

Quando for solicitado a escolher um **DSL,** selecione **Kotlin**.

## <a name="configure-the-build-file"></a>Configure o ficheiro de construção

Localize-o `build.gradle.kts` e abra-o com o seu IDE favorito ou editor de texto. Em seguida, copiar nesta configuração de construção:

```
plugins {
    java
    application
}
application {
    mainClassName = "Transliterate"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
    compile("com.google.code.gson:gson:2.8.5")
}
```

Tome nota que esta amostra tem dependências de OkHttp para pedidos HTTP, e Gson para lidar e analisar JSON. Se quiser saber mais sobre configurações de construção, consulte [Creating New Gradle Builds](https://guides.gradle.org/creating-new-gradle-builds/).

## <a name="create-a-java-file"></a>Criar um ficheiro Java

Vamos criar uma pasta para a sua aplicação de amostras. Do seu diretório de trabalho, corra:

```console
mkdir -p src\main\java
```

Em seguida, nesta pasta, `Transliterate.java`crie um ficheiro chamado .

## <a name="import-required-libraries"></a>Importação de bibliotecas necessárias

Abra `Transliterate.java` e adicione estas declarações de importação:

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;
```


## <a name="define-variables"></a>Definir variáveis

Primeiro, terá de criar uma classe pública para o seu projeto:

```java
public class Transliterate {
  // All project code goes here...
}
```

Adicione estas linhas `Transliterate` à classe. Em primeiro lugar, a chave de subscrição e o ponto final estão a ser lidos a partir de variáveis ambientais. Em seguida, você vai notar `api-version`que, juntamente com os `url`dois parâmetros adicionais foram anexados ao . Estes parâmetros são usados para definir a linguagem de entrada, e os scripts para transliteração. Nesta amostra, está definido para`jpan`japonês (`latn`) e latim ( ). 

```java
private static String subscriptionKey = System.getenv("TRANSLATOR_TEXT_SUBSCRIPTION_KEY");
private static String endpoint = System.getenv("TRANSLATOR_TEXT_ENDPOINT");
String url = endpoint + "/transliterate?api-version=3.0&language=ja&fromScript=jpan&toScript=latn";
```
Se estiver a utilizar uma subscrição multi-serviço `Ocp-Apim-Subscription-Region` de Serviços Cognitivos, também deve incluir os parâmetros do seu pedido. [Saiba mais sobre autenticação com a subscrição de vários serviços.](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)

## <a name="create-a-client-and-build-a-request"></a>Criar um cliente e construir um pedido

Adicione esta linha `Transliterate` à classe `OkHttpClient`para instantaneamente:

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

Em seguida, vamos construir o pedido do POST. Sinta-se livre para mudar o texto para a transliteração.

```java
// This function performs a POST request.
public String Post() throws IOException {
    MediaType mediaType = MediaType.parse("application/json");
    RequestBody body = RequestBody.create(mediaType,
            "[{\n\t\"Text\": \"こんにちは\"\n}]");
    Request request = new Request.Builder()
            .url(url).post(body)
            .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
            .addHeader("Content-type", "application/json").build();
    Response response = client.newCall(request).execute();
    return response.body().string();
}
```

## <a name="create-a-function-to-parse-the-response"></a>Criar uma função para analisar a resposta

Esta função simples analisa e prettifica a resposta JSON do serviço De Texto tradutor.

```java
// This function prettifies the json response.
public static String prettify(String json_text) {
    JsonParser parser = new JsonParser();
    JsonElement json = parser.parse(json_text);
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(json);
}
```

## <a name="put-it-all-together"></a>Juntar tudo

O último passo é fazer um pedido e obter uma resposta. Adicione estas linhas ao seu projeto:

```java
public static void main(String[] args) {
    try {
        Transliterate transliterateRequest = new Transliterate();
        String response = transliterateRequest.Post();
        System.out.println(prettify(response));
    } catch (Exception e) {
        System.out.println(e);
    }
}
```

## <a name="run-the-sample-app"></a>Execute a aplicação de exemplo

Está pronto para executar a sua aplicação de amostras. A partir da linha de comando (ou sessão terminal), navegue até à raiz do seu diretório de trabalho e corra:

```console
gradle build
```

Quando a construção estiver concluída, corra:

```console
gradle run
```

## <a name="sample-response"></a>Resposta de amostra

```json
[
  {
    "text": "konnichiwa",
    "script": "latn"
  }
]
```

## <a name="next-steps"></a>Passos seguintes

Veja a referência da API para entender tudo o que pode fazer com a API de Texto tradutor.

> [!div class="nextstepaction"]
> [Referência da API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
