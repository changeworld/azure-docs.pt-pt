---
title: Tutorial - Provisão simulado dispositivo X.509 para O Hub Azure IoT usando Java e grupos de inscrição
description: Neste tutorial, crie e forneça um dispositivo X.509 simulado utilizando dispositivo java e serviço SDK e grupos de inscrição para ioT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: d6cb3af134ff272d79cfc440047a3d90733ee9e8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74976813"
---
# <a name="tutorial-create-and-provision-a-simulated-x509-device-using-java-device-and-service-sdk-and-group-enrollments-for-iot-hub-device-provisioning-service"></a>Tutorial: Criar e fornecer um dispositivo X.509 simulado utilizando dispositivo e serviço Java SDK e inscrições em grupo para o Serviço de Provisionamento de Dispositivos IoT Hub

Estes passos mostram como simular um dispositivo X.509 no seu computador de desenvolvimento que executa o SO Windows e utilizar um código de exemplo para ligar esse dispositivo ao Serviço Aprovisionamento de Dispositivos e ao seu hub IoT mediante a utilização de grupos de inscrição. 

Antes de avançar, certifique-se de que executa os passos descritos em [Configurar o Serviço de Aprovisionamento de Dispositivos no Hub IoT com o portal do Azure](./quick-setup-auto-provision.md).


## <a name="prepare-the-environment"></a>Preparar o ambiente 

1. Certifique-se de que tem o [Java SE Development Kit 8](https://aka.ms/azure-jdks) instalado no seu computador.

1. Transfira e instale o [Maven](https://maven.apache.org/install.html).

1. Verifique se `git` está instalado no computador e que é adicionado às variáveis de ambiente às quais a janela de comandos pode aceder. Veja as [ferramentas de cliente Git da Software Freedom Conservancy](https://git-scm.com/download/) relativamente à mais recente versão das ferramentas de `git` a instalar, que incluem o **Git Bash**, a aplicação de linha de comandos que pode utilizar para interagir com o seu repositório Git local. 

1. Utilize a seguinte [visão geral](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) do certificado para criar os seus certificados de teste.

    > [!NOTE]
    > Este passo requer [o OpenSSL](https://www.openssl.org/), que pode ser construído e instalado a partir de fonte ou descarregado e instalado a partir de uma [terceira parte](https://wiki.openssl.org/index.php/Binaries) como [esta](https://sourceforge.net/projects/openssl/). Se já tiver criado os certificados de _raiz_, _intermédios_ e de _dispositivo_, pode ignorar este passo.
    >

    1. Siga os dois primeiros passos para criar os certificados de _raiz_ e _ intermédio_.

    1. Inicie sessão no portal do Azure, clique no botão **Todos os recursos**, no menu do lado esquerdo, e abra o seu serviço de aprovisionamento.

        1. No painel de resumo do Serviço de Aprovisionamento de Dispositivos, selecione **Certificados** e clique no botão **Adicionar** na parte superior.

        1. Em **Adicionar Certificado**, introduza as informações seguintes:
            - Introduza um nome exclusivo de certificado.
            - Selecione o ficheiro **_RootCA.pem_** que criou.
            - Quando tiver terminado, clique no botão **Guardar**.

           ![Adicionar certificado](./media/tutorial-group-enrollments/add-certificate.png)

        1. Selecione o certificado que acabou de criar:
            - Clique em **Gerar Código de Verificação**. Copie o código gerado.
            - Execute o passo de verificação. Introduza o _código de verificação_ ou clique com o botão direito do rato na janela do PowerShell que está em execução.  Pressione **Entrar**.
            - Selecione o ficheiro **_verifyCert4.pem_** acabado de criar no portal do Azure. Clique em **Verificar**.

              ![Validar certificado](./media/tutorial-group-enrollments/validate-certificate.png)

    1. Execute os passos para criar os certificados do dispositivo e limpar recursos para concluir.

       > [!NOTE]
       > Ao criar certificados de dispositivo, não se esqueça de utilizar apenas carateres alfanuméricos em letra minúscula e hífenes no nome do dispositivo.
       >


## <a name="create-a-device-enrollment-entry"></a>Criar uma entrada de inscrição de dispositivos

1. Abra uma linha de comandos. Clone o repositório do GitHub para os exemplos de código do SDK Java:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

1. No código fonte transferido, navegue até à pasta de exemplo **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_**. Abra o ficheiro **_/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentGroupSample.java_** num editor à sua escolha e adicione os seguintes detalhes:

    1. Adicione `[Provisioning Connection String]` para o seu serviço de aprovisionamento, a partir do portal da seguinte forma:

        1. Navegue para o seu serviço de provisionamento no [portal Azure.](https://portal.azure.com)

        1. Abra as **Políticas de acesso partilhado** e selecione uma política que tenha a permissão *EnrollmentWrite*.

        1. Copie a **Cadeia de ligação da chave primária**.

            ![Obter a cadeia de ligação de aprovisionamento a partir do portal](./media/tutorial-group-enrollments/provisioning-string.png)  

        1. No ficheiro de código de exemplo **_ServiceEnrollmentGroupSample.java_**, substitua a cadeia `[Provisioning Connection String]` pela **Cadeia de ligação da chave primária**.

            ```java
            private static final String PROVISIONING_CONNECTION_STRING = "[Provisioning Connection String]";
            ```

    1. Abra o seu ficheiro de certificado de assinatura intermédio num editor de texto. Atualize `PUBLIC_KEY_CERTIFICATE_STRING` o valor com o valor do seu certificado de assinatura intermédio.

        Se gerou os certificados do seu dispositivo com concha bash, *./certs/azure-iot-test-only.intermediate.cert.pem* contém a chave de certificado intermédio. Se os seus certificados foram gerados com powerShell, *./Intermediate1.pem* será o seu ficheiro de certificado intermédio.

        ```java
        private static final String PUBLIC_KEY_CERTIFICATE_STRING =
                "-----BEGIN CERTIFICATE-----\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "-----END CERTIFICATE-----\n";
        ```

    1. Navegue até ao hub IoT ligado ao seu serviço de aprovisionamento no [portal do Azure](https://portal.azure.com). Abra o separador **Descrição geral** para o hub e copie o **Hostname**. Atribua este **Hostname** ao parâmetro *IOTHUB_HOST_NAME*.

        ```java
        private static final String IOTHUB_HOST_NAME = "[Host name].azure-devices.net";
        ```

    1. Estude o código de exemplo. Este cria, atualiza, consulta e elimina uma inscrição de grupo para dispositivos X.509. Para verificar a inscrição com êxito no portal, comente temporariamente as seguintes linhas de código no final do ficheiro _ServiceEnrollmentGroupSample.java_:

        ```java
        // ************************************** Delete info of enrollmentGroup ***************************************
        System.out.println("\nDelete the enrollmentGroup...");
        provisioningServiceClient.deleteEnrollmentGroup(enrollmentGroupId);
        ```

    1. Guarde o ficheiro _ServiceEnrollmentGroupSample.java_.

1. Abra uma janela de comando e navegue até à pasta **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_**.

1. Compile o código de exemplo, utilizando este comando:

    ```cmd\sh
    mvn install -DskipTests
    ```

1. Execute o exemplo, utilizando estes comandos na janela de comando:

    ```cmd\sh
    cd target
    java -jar ./service-enrollment-group-sample-{version}-with-deps.jar
    ```

1. Observe a janela de saída para ver se a inscrição foi executada com êxito.

    ![Inscrição com êxito](./media/tutorial-group-enrollments/enrollment.png) 

1. Navegue até ao serviço de aprovisionamento no portal do Azure. Clique em **Gerir inscrições**. Repare que o seu grupo de dispositivos X.509 é apresentado no separador **Grupos de Inscrição**, com um *NOME DE GRUPO* gerado automaticamente.

## <a name="simulate-the-device"></a>Simular o dispositivo

1. Na lâmina de resumo do Serviço de Fornecimento de Dispositivos, selecione **visão geral** e note o seu _ponto_ final global do id scope e _provisioning Service_.

    ![Informações de serviço](./media/tutorial-group-enrollments/extract-dps-endpoints.png)

1. Abra uma linha de comandos. Navegue até à pasta do projeto de exemplo.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

1. Edite `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java` para incluir o seu _id scope_ e _provisioning service Global Endpoint_ que você observou anteriormente.

    ```java
    private static final String idScope = "[Your ID scope here]";
    private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
    private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
    private static final int MAX_TIME_TO_WAIT_FOR_REGISTRATION = 10000; // in milli seconds
    private static final String leafPublicPem = "<Your Public PEM Certificate here>";
    private static final String leafPrivateKey = "<Your Private PEM Key here>";
    ```

1. Atualize `leafPublicPem` `leafPrivateKey` as variáveis e variáveis com os certificados de dispositivopúblico e privado.

    Se gerou os certificados do seu dispositivo com powerShell, os ficheiros mydevice* contêm a chave pública, a chave privada e o PFX para o dispositivo.

    Se gerou os certificados do seu dispositivo com concha Bash, ./certs/new-device.cert.pem contém a chave pública. A chave privada do dispositivo estará no ficheiro ./private/new-device.key.pem.

    Abra o seu ficheiro `leafPublicPem` de chave pública e atualize a variável com esse valor. Copie o texto de _-----BEGIN PRIVATE KEY-----_ para _-----END PRIVATE KEY-----_.

    ```java
    private static final String leafPublicPem = "-----BEGIN CERTIFICATE-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END CERTIFICATE-----\n";
    ```

    Abra o seu ficheiro `leafPrivatePem` de chave privada e atualize a variável com esse valor. Copie o texto de _-----BEGIN RSA PRIVATE KEY-----_ para _-----END RSA PRIVATE KEY-----_.

    ```java
    private static final String leafPrivateKey = "-----BEGIN RSA PRIVATE KEY-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END RSA PRIVATE KEY-----\n";
    ```

1. Adicione uma nova `leafPrivateKey` variável logo abaixo para o seu certificado intermédio. Nomeie `intermediateKey`esta nova variável. Dê-lhe o valor do seu certificado de assinatura intermédio.

    Se gerou os certificados do seu dispositivo com concha bash, *./certs/azure-iot-test-only.intermediate.cert.pem* contém a chave de certificado intermédio. Se os seus certificados foram gerados com powerShell, *./Intermediate1.pem* será o seu ficheiro de certificado intermédio.

    ```java
    private static final String intermediateKey = "-----BEGIN CERTIFICATE-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END CERTIFICATE-----\n";
    ```

1. Na `main` função, `intermediateKey` adicione `signerCertificates` a coleção antes `securityProviderX509`da inicialização de .

    ```java
    public static void main(String[] args) throws Exception
    {
        ...

        try
        {
            ProvisioningStatus provisioningStatus = new ProvisioningStatus();

            // Add intermediate certificate as part of the certificate key chain.
            signerCertificates.add(intermediateKey);

            SecurityProvider securityProviderX509 = new SecurityProviderX509Cert(leafPublicPem, leafPrivateKey, signerCertificates);
    ```

1. Guarde as suas alterações e construa a amostra. Navegue até à pasta de destino e execute o ficheiro jar criado.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

    ![Registo com êxito](./media/tutorial-group-enrollments/registration.png)

1. No portal, navegue para o IoT hub ligado ao seu serviço de aprovisionamento e abra o painel **Explorador de Dispositivos**. Após o aprovisionamento bem-sucedido do dispositivo X.509 simulado no hub, o ID de dispositivo aparece no painel **Explorador de Dispositivos**, com o *ESTADO***ativado**. Tenha em atenção que poderá ter de clicar no botão **Atualizar**, na parte superior, se já tiver aberto o painel antes de executar a aplicação de dispositivo de exemplo. 

    ![O dispositivo é registado no hub IoT](./media/tutorial-group-enrollments/hub-registration.png) 


## <a name="clean-up-resources"></a>Limpar recursos

Se quiser continuar a trabalhar e a explorar o exemplo de cliente do dispositivo, não limpe os recursos criados neste Guia Rápido. Se não planear continuar, utilize os passos seguintes para eliminar todos os recursos criados no Guia Rápido.

1. Feche a janela da saída do exemplo de dispositivo cliente no seu computador.
1. No menu do lado esquerdo do portal do Azure, clique em **Todos os recursos** e selecione o seu Serviço Aprovisionamento de Dispositivos. Abra a lâmina **'Gerir as Inscrições'** para o seu *REGISTRATION ID* serviço e, em seguida, clique no separador **Delete** **Individual Registrations.** 
1. No menu do lado esquerdo do portal do Azure, clique em **Todos os recursos** e selecione o seu hub IoT. Abra o painel **Dispositivos IoT** do seu hub, selecione o *ID DE DISPOSITIVO* do dispositivo que registou neste Manual de Início Rápido e, em seguida, clique no botão **Eliminar** na parte superior.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou um dispositivo X.509 simulado no seu computador Windows e aprovisionou-o no seu hub IoT com o Serviço Aprovisionamento de Dispositivos no Hub IoT e os grupos de inscrição. Para saber mais sobre o seu dispositivo X.509, avance para os conceitos do dispositivo. 

> [!div class="nextstepaction"]
> [IoT Hub Device Provisioning Service device concepts](concepts-device.md) (Conceitos de dispositivo do Serviço Aprovisionamento de Dispositivos no Hub IoT)
