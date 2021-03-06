---
title: Configure aplicativos Ruby - Serviço de Aplicações Azure
description: Aprenda a configurar um recipiente Ruby pré-construído para a sua aplicação. Este artigo mostra as tarefas de configuração mais comuns.
ms.topic: quickstart
ms.date: 03/28/2019
ms.reviewer: astay; kraigb
ms.custom: mvc, seodec18
ms.openlocfilehash: 8daebba840223d050a14b4b99cb6ae15472ee4f5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "80046328"
---
# <a name="configure-a-linux-ruby-app-for-azure-app-service"></a>Configure um aplicativo Linux Ruby para o Serviço de Aplicações Azure

Este artigo descreve como o [Azure App Service](app-service-linux-intro.md) executa aplicações Ruby e como pode personalizar o comportamento do App Service quando necessário. As aplicações ruby devem ser implantadas com todas as [joias necessárias.](https://rubygems.org/gems)

Este guia fornece conceitos e instruções fundamentais para os desenvolvedores ruby que usam um recipiente Linux incorporado no Serviço de Aplicações. Se nunca usou o Azure App Service, deve seguir primeiro o [Ruby quickstart](quickstart-ruby.md) e a Ruby com o [tutorial PostgreSQL.](tutorial-ruby-postgres-app.md)

## <a name="show-ruby-version"></a>Mostrar versão Ruby

Para mostrar a versão ruby atual, execute o seguinte comando na [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Para mostrar todas as versões Ruby suportadas, execute o seguinte comando na [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp list-runtimes --linux | grep RUBY
```

Pode executar uma versão não suportada da Ruby construindo a sua própria imagem de contentor. Para mais informações, consulte [a utilização de uma imagem personalizada do Docker.](tutorial-custom-docker-image.md)

## <a name="set-ruby-version"></a>Definir versão Ruby

Executar o seguinte comando na [Cloud Shell](https://shell.azure.com) para definir a versão Ruby para 2.3:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "RUBY|2.3"
```

> [!NOTE]
> Se vir erros semelhantes aos seguintes durante o tempo de implantação:
> ```
> Your Ruby version is 2.3.3, but your Gemfile specified 2.3.1
> ```
> ou
> ```
> rbenv: version `2.3.1' is not installed
> ```
> Significa que a versão Ruby configurada no seu projeto é diferente da versão instalada`2.3.3` no recipiente que está a executar (no exemplo acima). No exemplo acima, verifique tanto *gemfile* como *.ruby-version* e verifique se a versão Ruby não está definida, ou está`2.3.3` definida para a versão que está instalada no recipiente que está a executar (no exemplo acima).

## <a name="access-environment-variables"></a>Aceder a variáveis de ambiente

No Serviço de Aplicações, pode [definir as definições](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) de aplicações fora do seu código de aplicações. Em seguida, pode acessá-los utilizando o padrão [padrão ENV\<['s path-name>']](https://ruby-doc.org/core-2.3.3/ENV.html) Por exemplo, para aceder `WEBSITE_SITE_NAME`a uma definição de aplicação chamada , use o seguinte código:

```ruby
ENV['WEBSITE_SITE_NAME']
```

## <a name="customize-deployment"></a>Personalizar a implementação

Quando implementa um [repositório Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), ou um [pacote Zip](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) com processos de construção ligados, o motor de implantação (Kudu) executa automaticamente os seguintes passos pós-implantação por padrão:

1. Verifique se existe um *Gemfile.*
1. Execute `bundle clean`. 
1. Execute `bundle install --path "vendor/bundle"`.
1. Corra `bundle package` para embalar pedras preciosas na pasta fornecedor/cache.

### <a name="use---without-flag"></a>Uso -- sem bandeira

Para `bundle install` correr com a bandeira [sem bandeira,](https://bundler.io/man/bundle-install.1.html) defina a definição da `BUNDLE_WITHOUT` [aplicação](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) para uma lista separada de grupos. Por exemplo, o comando seguinte `development,test`define-o para .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings BUNDLE_WITHOUT="development,test"
```

Se esta definição for definida, `bundle install` `--without $BUNDLE_WITHOUT`então o motor de implantação funciona com .

### <a name="precompile-assets"></a>Pré-compilação de ativos

Os passos de pós-implantação não pré-compilam os ativos por defeito. Para ligar a pré-compilação `ASSETS_PRECOMPILE` do `true`ativo, defina a definição da [aplicação](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) para . Em seguida, o comando `bundle exec rake --trace assets:precompile` é executado no final dos passos pós-implantação. Por exemplo:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASSETS_PRECOMPILE=true
```

Para mais informações, consulte [Servir ativos estáticos.](#serve-static-assets)

## <a name="customize-start-up"></a>Personalizar o arranque

Por predefinição, o recipiente Ruby inicia o servidor Rails na seguinte sequência (para mais informações, consulte o [script de arranque):](https://github.com/Azure-App-Service/ruby/blob/master/2.3.8/startup.sh)

1. Gerar um [valor secret_key_base,](https://edgeguides.rubyonrails.org/security.html#environmental-security) se já não existir. Este valor é necessário para que a app seja executada em modo de produção.
1. Definir `RAILS_ENV` a variável ambiente para `production`.
1. Elimine qualquer ficheiro *.pid* no diretório *tmp/pids* que seja deixado por um servidor Rails anteriormente em execução.
1. Verifique se todas as dependências estão instaladas. Caso contrário, tente instalar pedras preciosas do diretório *de fornecedor/cache* local.
1. Execute `rails server -e $RAILS_ENV`.

Pode personalizar o processo de arranque das seguintes formas:

- [Servir ativos estáticos](#serve-static-assets)
- [Executado em modo de não produção](#run-in-non-production-mode)
- [Definir secret_key_base manualmente](#set-secret_key_base-manually)

### <a name="serve-static-assets"></a>Servir ativos estáticos

O servidor Rails no recipiente Ruby funciona em modo de produção por padrão, e [assume que os ativos são pré-compilados e são servidos pelo seu servidor web](https://guides.rubyonrails.org/asset_pipeline.html#in-production). Para servir ativos estáticos do servidor Rails, precisa fazer duas coisas:

- **Pré-compilar os ativos** - [Pré-compilar os ativos estáticos localmente](https://guides.rubyonrails.org/asset_pipeline.html#local-precompilation) e implantá-los manualmente. Ou, deixe o motor de implantação manuseá-lo (ver [ativos pré-compilados](#precompile-assets).
- **Ativar a servir ficheiros estáticos** - Para servir `true`ativos estáticos do recipiente Ruby, [defina a definição da `RAILS_SERVE_STATIC_FILES` aplicação](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) para . Por exemplo:

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_SERVE_STATIC_FILES=true
    ```

### <a name="run-in-non-production-mode"></a>Executado em modo de não produção

O servidor Rails funciona em modo de produção por padrão. Para funcionar em modo de desenvolvimento, por exemplo, defina a definição da `RAILS_ENV` [aplicação](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) para `development`.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_ENV="development"
```

No entanto, esta definição por si só faz com que o servidor Rails comece no modo de desenvolvimento, o que aceita apenas pedidos de localização e não é acessível fora do recipiente. Para aceitar pedidos remotos `APP_COMMAND_LINE` de clientes, defina a definição da [aplicação](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) para `rails server -b 0.0.0.0`. Esta definição de aplicativo permite-lhe executar um comando personalizado no recipiente Ruby. Por exemplo:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings APP_COMMAND_LINE="rails server -b 0.0.0.0"
```

### <a name="set-secret_key_base-manually"></a><a name="set-secret_key_base-manually"></a>Definir secret_key_base manualmente

Para utilizar `secret_key_base` o seu próprio valor em vez de `SECRET_KEY_BASE` deixar o App Service gerar um para si, defina a definição da [aplicação](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) com o valor que deseja. Por exemplo:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings SECRET_KEY_BASE="<key-base-value>"
```

## <a name="access-diagnostic-logs"></a>Aceder aos registos de diagnósticos

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Abra a sessão sSH no navegador

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Aplicativo rails com PostgreSQL](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [Serviço de aplicações Linux FAQ](app-service-linux-faq.md)
