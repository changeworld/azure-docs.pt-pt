---
title: Ativar o reset da palavra-passe self-service do Diretório Ativo Azure
description: Neste tutorial, aprende-se a permitir o reset da palavra-passe self-service do Azure Ative Directory para um grupo de utilizadores e testar o processo de redefinição da palavra-passe.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: iainfou
author: iainfoulds
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71b9052f364dfbae205dd324ba69de9578ccc225
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77027679"
---
# <a name="tutorial-enable-users-to-unlock-their-account-or-reset-passwords-using-azure-active-directory-self-service-password-reset"></a>Tutorial: Permitir que os utilizadores desbloqueiem a sua conta ou reporem palavras-passe utilizando o reset de palavra-passe self-service do Diretório Ativo Azure

O reset de autosserviço do Azure Ative Directory (Azure AD) dá aos utilizadores a possibilidade de alterar ou redefinir a sua palavra-passe, sem qualquer envolvimento de administrador ou de secretária. Se a conta de um utilizador estiver bloqueada ou se esquecer em palavra-passe, pode seguir as indicações para se desbloquear em si e voltar ao trabalho. Esta capacidade reduz as chamadas de secretária e a perda de produtividade quando um utilizador não pode iniciar sessão no seu dispositivo ou numa aplicação.

> [!IMPORTANT]
> Este quickstart mostra a um administrador como ativar o reset da palavra-passe self-service. Se é um utilizador final já registado para reset de senha de autosserviço e precisa de voltar à sua conta, vá para https://aka.ms/sspr.
>
> Se a sua equipa de TI não tiver ativado a capacidade de redefinir a sua própria palavra-passe, contacte o seu helpdesk para obter assistência adicional.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Ativar o reset de senha de autosserviço para um grupo de utilizadores de Anúncios Azure
> * Configurar métodos de autenticação e opções de registo
> * Testar o processo SSPR como utilizador

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, necessita dos seguintes recursos e privilégios:

* Um inquilino do Azure AD em funcionamento com, pelo menos, uma licença de avaliação ativada.
    * Se necessário, [crie um de graça.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Uma conta com privilégios *de Administrador Global.*
* Um utilizador não administrador com uma palavra-passe que conheça, como *testuser*. Testa a experiência SSPR do utilizador final utilizando esta conta neste tutorial.
    * Se precisar de criar um utilizador, consulte [Quickstart: Adicione novos utilizadores ao Diretório Ativo Do Azure](../add-users-azure-active-directory.md).
* Um grupo do que o utilizador não administrador é membro, como o *SSPR-Test-Group*. Você permite sSPR para este grupo neste tutorial.
    * Se precisa de criar um grupo, veja como [criar um grupo e adicione membros no Azure Ative Directory](../active-directory-groups-create-azure-portal.md).

## <a name="enable-self-service-password-reset"></a>Ativar a reposição de palavras-passe self-service

O Azure AD permite ativar o SSPR para *nenhum,* *selecionado*ou *todos os* utilizadores. Esta capacidade granular permite-lhe escolher um subconjunto de utilizadores para testar o processo de registo sSPR e o fluxo de trabalho. Quando se sente confortável com o processo e pode comunicar os requisitos com um conjunto mais alargado de utilizadores, pode selecionar grupos adicionais de utilizadores para ativar o SSPR. Ou, você pode ativar SSPR para todos no inquilino da AD Azure.

Neste tutorial, configure o SSPR para um conjunto de utilizadores num grupo de teste. No exemplo seguinte, é utilizado o grupo *SSPR-Test-Group.* Forneça o seu próprio grupo Azure AD conforme necessário:

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta com permissões *de administrador global.*
1. Procure e selecione **Azure Ative Directory,** em seguida, escolha **o reset password** do menu do lado esquerdo.
1. A partir da página **Propriedades,** sob a opção Reset de password de *self service ativado,* escolha **Select group**
1. Procure e selecione o seu grupo Azure AD, como *o SSPR-Test-Group,* e depois escolha *Select*.

    [![](media/tutorial-enable-sspr/enable-sspr-for-group-cropped.png "Select a group in the Azure portal to enable for self-service password reset")](media/tutorial-enable-sspr/enable-sspr-for-group.png#lightbox)

    Como parte de uma maior implantação da SSPR, os grupos aninhados são apoiados. Certifique-se de que os utilizadores do(s) grupo(s) que escolher têm as licenças adequadas atribuídas. Não existe atualmente nenhum processo de validação destes requisitos de licenciamento.

1. Para ativar o SSPR para os utilizadores selecionados, selecione **Guardar**.

## <a name="select-authentication-methods-and-registration-options"></a>Selecione métodos de autenticação e opções de registo

Quando os utilizadores precisam de desbloquear a sua conta ou redefinir a sua palavra-passe, são solicitados para um método de confirmação adicional. Este fator de autenticação adicional garante que apenas os eventos SSPR aprovados são concluídos. Pode escolher quais os métodos de autenticação a permitir, com base nas informações de registo que o utilizador fornece.

1. Na página dos **métodos** de Autenticação do menu do lado esquerdo, delineie o **número de métodos necessários para repor** para *1*.

    Para melhorar a segurança, pode aumentar o número de métodos de autenticação necessários para o SSPR.

1. Escolha os **Métodos disponíveis para os utilizadores** que a sua organização quer permitir. Para este tutorial, verifique as caixas para ativar os seguintes métodos:

    * *Notificação de aplicação móvel*
    * *Código da aplicação móvel*
    * *E-mail*
    * *Número de telemóvel*
    * *Telefone do emprego*

1. Para aplicar os métodos de autenticação, selecione **Guardar**.

Antes que os utilizadores possam desbloquear a sua conta ou redefinir uma palavra-passe, devem registar as suas informações de contacto. Estas informações de contacto são utilizadas para os diferentes métodos de autenticação configurados nas etapas anteriores.

Um administrador pode fornecer manualmente estas informações de contacto, ou os utilizadores podem recorrer a um portal de registo para fornecer a informação por si mesmos. Neste tutorial, configure os utilizadores para serem solicitados para o registo quando iniciarem o seu próximo início.

1. Na página **de registo** do menu do lado esquerdo, selecione *Sim* para Exigir que os utilizadores **se registem ao iniciar a sua inscrição**.
1. É importante que a informação de contacto seja atualizada. Se as informações de contacto estiverem desatualizadas quando um evento SSPR for iniciado, o utilizador poderá não conseguir desbloquear a sua conta ou redefinir a sua palavra-passe.

    Defina o **Número de dias antes de ser pedido aos utilizadores que voltem a confirmar as informações de autenticação** como *180*.
1. Para aplicar as definições de registo, selecione **Guardar**.

## <a name="configure-notifications-and-customizations"></a>Configurar notificações e personalizações

Para manter os utilizadores informados sobre a atividade da conta, pode configurar notificações de e-mail a serem enviadas quando um evento SSPR acontecer. Estas notificações podem abranger tanto as contas regulares de utilizador como as contas de administração. Para as contas de administração, esta notificação fornece uma camada adicional de consciência quando uma senha de conta de administrador privilegiado é redefinida usando SSPR.

1. Na página **Notificações** do menu do lado esquerdo, configure as seguintes opções:

   * Defina a opção **Notificar os utilizadores sobre reposições de palavra-passe** como *Sim*.
   * Defina **Notificar todos os administradores quando outros administradores repõem as palavras-passe deles** como *Sim*.

1. Para aplicar as preferências de notificação, selecione **Guardar**.

Se os utilizadores precisarem de ajuda adicional com o processo SSPR, pode personalizar o link para "Contacte o seu administrador". Este link é utilizado no processo de registo SSPR e quando um utilizador desbloqueia a sua conta ou redefine a sua palavra-passe. Para garantir que os seus utilizadores obtêm o suporte necessário, é altamente recomendado fornecer um e-mail de helpdesk personalizado ou URL.

1. Na página de **Personalização** do menu do lado esquerdo, desempor o link de *helpdesk para* **Sim**.
1. No **e-mail de helpdesk personalizado ou** no campo URL, forneça um endereço de e-mail ou URL da página web onde os seus utilizadores possam obter ajuda adicional da sua organização, como*https://support.contoso.com/*
1. Para aplicar o link personalizado, selecione **Guardar**.

## <a name="test-self-service-password-reset"></a>Testar a reposição personalizada de palavra-passe

Com o SSPR ativado e configurado, teste o processo SSPR com um utilizador que faz parte do grupo selecionado na secção anterior, como *Test-SSPR-Group*. No exemplo seguinte, a conta *testuser* é utilizada. Forneça a sua própria conta de utilizador que faça parte do grupo que ativou para a SSPR na primeira secção deste tutorial.

> [!NOTE]
> Quando testar a redefinição da palavra-passe de autosserviço, utilize uma conta não administradora. Os administradores estão sempre habilitados para o reset da palavra-passe self-service e são obrigados a utilizar dois métodos de autenticação para redefinir a sua palavra-passe.

1. Para ver o processo de registo manual, abra uma nova janela do [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)navegador no modo InPrivate ou incógnito, e navegue até . Os utilizadores devem ser direcionados para este portal de registo quando iniciarem o seu próximo início de sessão.
1. Inscreva-se com um utilizador de teste não administrador, como *testuser,* e registe as informações de contacto dos seus métodos de autenticação.
1. Uma vez concluído, selecione o botão marcado **Parece bom** e feche a janela do navegador.
1. Abra uma nova janela de navegador em modo InPrivate [https://aka.ms/sspr](https://aka.ms/sspr)ou incógnito, e navegue para .
1. Introduza as informações da conta dos utilizadores de testes não administradores, tais como *testuser,* os caracteres do CAPTCHA e, em seguida, selecione **Next**.

    ![Introduza informações da conta do utilizador para redefinir a palavra-passe](media/tutorial-enable-sspr/password-reset-page.png)

1. Siga as medidas de verificação para redefinir a sua palavra-passe. Quando estiver concluída, deverá receber uma notificação por e-mail de que a sua palavra-passe foi redefinida.

## <a name="clean-up-resources"></a>Limpar recursos

Num seguinte tutorial nesta série, configura-se a reescrita da palavra-passe. Esta funcionalidade escreve alterações de palavra-passe do Azure AD SSPR de volta para um ambiente ad no local. Se quiser continuar com esta série tutorial para configurar a redação da palavra-passe, não desative o SSPR agora.

Se já não pretender utilizar a funcionalidade SSPR configurada como parte deste tutorial, detete o estado SSPR para **Nenhum** utilizando os seguintes passos:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Procure e selecione **Azure Ative Directory,** em seguida, escolha **o reset password** do menu do lado esquerdo.
1. A partir da página **Propriedades,** sob a opção Reset de senha de *serviço Self service ativada,* escolha **Nenhuma**.
1. Para aplicar a alteração SSPR, selecione **Guardar**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, permitiu o reset da palavra-passe autosserviço azure AD para um grupo selecionado de utilizadores. Aprendeu a:

> [!div class="checklist"]
> * Ativar o reset de senha de autosserviço para um grupo de utilizadores de Anúncios Azure
> * Configurar métodos de autenticação e opções de registo
> * Testar o processo SSPR como utilizador

> [!div class="nextstepaction"]
> [Ativar a Multi-Factor Authentication do Azure](tutorial-mfa-applications.md)
