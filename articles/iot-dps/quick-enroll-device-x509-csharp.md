---
title: 'Inscreva o dispositivo X.509 no Serviço de Provisionamento de Dispositivos Azure utilizando C #'
description: Este início rápido utiliza inscrições em grupo. Neste arranque rápido, inscreva dispositivos X.509 no Serviço de Provisionamento de Dispositivos Hub Azure IoT (DPS) utilizando C#.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc
ms.openlocfilehash: 64bc3921a606ab3211173b46b268ded53952c8bb
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "75434665"
---
# <a name="quickstart-enroll-x509-devices-to-the-device-provisioning-service-using-c"></a>Início Rápido: Inscrever dispositivos X.509 no Serviço de Aprovisionamento de Dispositivos com C#

[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]

Este início rápido mostra como utilizar o C# para criar programaticamente um [Grupo de inscrição](concepts-service.md#enrollment-group) que utiliza certificados X.509 de AC de raiz ou intermediários. O grupo de inscrições é criado utilizando o [Microsoft Azure IoT SDK para .NET](https://github.com/Azure/azure-iot-sdk-csharp) e uma aplicação C# .NET Core. Um grupo de inscrição controla o acesso ao serviço de aprovisionamento de dispositivos que partilham um certificado de assinatura comum na respetiva cadeia de certificados. Para saber mais, veja [Controlar o acesso a dispositivos para o serviço de aprovisionamento com certificados X.509](./concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates). Para obter mais informações sobre como utilizar a Infraestrutura de Chaves Públicas (PKI) baseada em certificados X.509 com o Hub IoT do Azure e o Serviço de Aprovisionamento de Dispositivos, veja [Descrição geral da segurança do certificado de AC X.509](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview). 

Este quickstart espera que já tenha criado um hub IoT e uma instância de Serviço de Provisionamento de Dispositivos. Se ainda não criou estes recursos, complete o Serviço de Provisionamento de [Dispositivos IoT Hub com o portal Azure](./quick-setup-auto-provision.md) antes de continuar com este artigo.

Embora os passos neste artigo funcionem tanto em computadores Windows como Linux, este artigo utiliza um computador de desenvolvimento windows.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Instale o [Estúdio Visual 2019.](https://www.visualstudio.com/vs/)
* Instalar [o Núcleo .NET SDK](https://www.microsoft.com/net/download/windows).
* Instale [Git](https://git-scm.com/download/).

## <a name="prepare-test-certificates"></a>Preparar os certificados de teste

Neste início rápido, precisa de ter um ficheiro .pem ou .cer com a parte pública de um certificado X.509 de AC de raiz ou intermediário. Este certificado tem de ser carregado para o serviço de aprovisionamento e verificado pelo serviço.

O [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) contém ferramentas de teste que podem ajudá-lo a criar uma cadeia de certificados X.509, carregar um certificado de raiz ou intermédio dessa cadeia e fazer comprovativo de posse com o serviço para verificar o certificado.

> [!CAUTION]
> Utilize certificados criados com a ferramenta SDK apenas para testes de desenvolvimento.
> Não utilize estes certificados em produção.
> Contêm senhas codificadas, como *1234,* que expiram após 30 dias.
> Para saber como obter certificados adequados para utilização de produção, veja [Como obter um certificado X.509 de AC](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate) na documentação do Hub IoT do Azure.
>

Para utilizar esta ferramenta de teste para gerar certificados, faça os seguintes passos:

1. Encontre o nome da etiqueta para o [mais recente lançamento](https://github.com/Azure/azure-iot-sdk-c/releases/latest) do Azure IoT C SDK.

2. Abra uma linha de comandos ou shell do Git Bash e mude para uma pasta de trabalho no seu computador. Executar os seguintes comandos para clonar o mais recente lançamento do [repositório Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub. Utilize a etiqueta encontrada no passo anterior `-b` como valor para o parâmetro:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Esta operação deve demorar vários minutos a ser concluída.

   As ferramentas de teste estão localizada em *azure-iot-sdk-c/tools/CACertificates* do repositório que clonou.

3. Siga os passos em [Gerir certificados de AC de teste para exemplos e tutoriais](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

Além da ferramenta no C SDK, a amostra de verificação de [certificados do Grupo](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample) no Microsoft *Azure IoT SDK para .NET* mostra como fazer a prova de posse em C# com um certificado de CA intermédio ou raiz X.509 existente.

## <a name="get-the-connection-string-for-your-provisioning-service"></a>Obter a cadeia de ligação para o serviço de aprovisionamento

Para o exemplo neste início rápido, precisa da cadeia de ligação para o seu serviço de aprovisionamento.

1. Inscreva-se no portal Azure, selecione **Todos os recursos**e, em seguida, o seu Serviço de Provisionamento de Dispositivos.

1. Selecione políticas de **acesso partilhado**e, em seguida, escolha a política de acesso que pretende utilizar para abrir as suas propriedades. Na Política de **Acesso,** copie e guarde a cadeia de ligação principal.

    ![Obter a cadeia de ligação do serviço de aprovisionamento a partir do portal](media/quick-enroll-device-x509-csharp/get-service-connection-string-vs2019.png)

## <a name="create-the-enrollment-group-sample"></a>Criar o exemplo do grupo de inscrição 

Esta secção mostra como criar uma aplicação de consola .NET Core que adiciona um grupo de inscrições ao seu serviço de provisionamento. Com algumas modificações, também pode seguir estes passos para criar uma aplicação de consola [Windows IoT Core](https://developer.microsoft.com/en-us/windows/iot) para adicionar o grupo de inscrição. Para saber mais sobre como programar com o IoT Core, veja a [Documentação para programadores do Windows IoT Core](https://docs.microsoft.com/windows/iot-core/).

1. Open Visual Studio e selecione **Criar um novo projeto.** Em **Criar um novo projeto,** escolha a App consola **(.NET Core)** para o modelo de projeto C# e selecione **Next**.

1. Nomeie o projeto *CreateEnrollmentGroup*e, em seguida, prima **Criar**.

    ![Configure Projeto de Ambiente de Trabalho Clássico do Windows C#](media//quick-enroll-device-x509-csharp/configure-app-vs2019.png)

1. Quando a solução abrir no Estúdio Visual, no painel do **Solution Explorer,** clique à direita no projeto **CreateEnrollmentGroup** e, em seguida, selecione **Gerir pacotes NuGet**.

1. No **NuGet Package Manager**, selecione **Browse,** procure e escolha **Microsoft.Azure.Devices.Provisioning.Service**, e, em seguida, prima **Instalar**.

    ![Janela Gestor de Pacote NuGet](media//quick-enroll-device-x509-csharp/add-nuget.png)

   Este passo descarrega, instala e adiciona uma referência ao pacote [Azure IoT Provisioning Service Client SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) NuGet e suas dependências.

1. Adicione as `using` seguintes `using` declarações após `Program.cs`as outras declarações no topo de:

   ```csharp
   using System.Security.Cryptography.X509Certificates;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Provisioning.Service;
   ```

1. Adicione os seguintes `Program` campos à classe e efaça as alterações listadas.  

   ```csharp
   private static string ProvisioningConnectionString = "{ProvisioningServiceConnectionString}";
   private static string EnrollmentGroupId = "enrollmentgrouptest";
   private static string X509RootCertPath = @"{Path to a .cer or .pem file for a verified root CA or intermediate CA X.509 certificate}";
   ```

   * Substitua `ProvisioningServiceConnectionString` o valor do espaço reservado pela cadeia de ligação do serviço de provisionamento para o que pretende criar a inscrição.

   * Substitua `X509RootCertPath` o valor do espaço reservado por um ficheiro .pem ou .cer. Este ficheiro representa a parte pública de um certificado CA X.509 intermédio ou raiz que foi previamente carregado e verificado com o seu serviço de provisionamento.

   * Pode alterar opcionalmente `EnrollmentGroupId` o valor. A cadeia só pode conter carateres minúsculos e hífenes.

   > [!IMPORTANT]
   > No código de produção, tenha em atenção as seguintes considerações de segurança:
   >
   > * A pré-programação da cadeia de ligação para o administrador do serviço de aprovisionamento vai contra as melhores práticas de segurança. Em vez disso, a cadeia de ligação deve ser mantida de forma segura, tal como num ficheiro de configuração seguro ou no registo.
   > * Carregue apenas a parte pública do certificado de assinatura. Nunca carregue ficheiros .pfx (PKCS12) ou .pem que contêm chaves privadas para o serviço de aprovisionamento.

1. Adicione o seguinte `Program` método à classe. Este código cria uma entrada em `CreateOrUpdateEnrollmentGroupAsync` grupo `ProvisioningServiceClient` de inscrição e, em seguida, chama o método para adicionar o grupo de inscrição ao serviço de provisionamento.

   ```csharp
   public static async Task RunSample()
   {
       Console.WriteLine("Starting sample...");
 
       using (ProvisioningServiceClient provisioningServiceClient =
               ProvisioningServiceClient.CreateFromConnectionString(ProvisioningConnectionString))
       {
           #region Create a new enrollmentGroup config
           Console.WriteLine("\nCreating a new enrollmentGroup...");
           var certificate = new X509Certificate2(X509RootCertPath);
           Attestation attestation = X509Attestation.CreateFromRootCertificates(certificate);
           EnrollmentGroup enrollmentGroup =
                   new EnrollmentGroup(
                           EnrollmentGroupId,
                           attestation)
                   {
                       ProvisioningStatus = ProvisioningStatus.Enabled
                   };
           Console.WriteLine(enrollmentGroup);
           #endregion
 
           #region Create the enrollmentGroup
           Console.WriteLine("\nAdding new enrollmentGroup...");
           EnrollmentGroup enrollmentGroupResult =
               await provisioningServiceClient.CreateOrUpdateEnrollmentGroupAsync(enrollmentGroup).ConfigureAwait(false);
           Console.WriteLine("\nEnrollmentGroup created with success.");
           Console.WriteLine(enrollmentGroupResult);
           #endregion
 
       }
   }
   ```

1. Por fim, substitua `Main` o corpo do método pelas seguintes linhas:

   ```csharp
   RunSample().GetAwaiter().GetResult();
   Console.WriteLine("\nHit <Enter> to exit ...");
   Console.ReadLine();
   ```

1. Compilar a solução.

## <a name="run-the-enrollment-group-sample"></a>Executar o exemplo do grupo de inscrição
  
Execute o exemplo no Visual Studio para criar o grupo de inscrição. Uma janela De Comando Pronta aparecerá e começará a mostrar mensagens de confirmação. Na criação bem sucedida, a janela Command Prompt exibe as propriedades do novo grupo de inscrições.

Pode verificar se o grupo de matrículas foi criado. Vá ao resumo do Serviço de Provisionamento de Dispositivos e selecione **Gerir as matrículas,** em seguida, selecione **Grupos de Inscrição**. Deverá ver uma nova entrada de inscrição que corresponde ao ID de registo utilizado no exemplo.

![Propriedades de inscrição no portal](media/quick-enroll-device-x509-csharp/verify-enrollment-portal-vs2019.png)

Selecione a entrada para verificar a impressão digital do certificado e outras propriedades para a entrada.

## <a name="clean-up-resources"></a>Limpar recursos

Se planeia explorar a amostra de serviço C#, não limpe os recursos criados neste arranque rápido. Caso contrário, utilize os seguintes passos para eliminar todos os recursos criados por este arranque rápido.

1. Feche a janela de saída da amostra C# no seu computador.

1. Navegue para o seu serviço de provisionamento de dispositivos no portal Azure, selecione **Gerir as matrículas,** e depois selecione **Grupos de Inscrição**. Selecione o *ID de registo* para a entrada de inscrição que criou usando este arranque rápido e prima **Eliminar**.

1. Do seu serviço de provisionamento de dispositivos no portal Azure, selecione **Certificados,** escolha o certificado que carregou para este arranque rápido e prima **Apagar** no topo dos Dados do **Certificado**.  

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um grupo de inscrições para um certificado ca intermédio ou raiz X.509 utilizando o Serviço de Provisionamento de Dispositivos Hub Azure IoT. Para ficar a conhecer aprofundadamente o aprovisionamento de dispositivos, prossiga no tutorial para a configuração do Serviço Aprovisionamento de Dispositivos no portal do Azure.

> [!div class="nextstepaction"]
> [Azure IoT Hub Device Provisioning Service tutorials](./tutorial-set-up-cloud.md) (Tutoriais do Serviço Aprovisionamento de Dispositivos no Hub IoT do Azure)
