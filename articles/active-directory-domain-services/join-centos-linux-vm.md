---
title: Junte-se a um CentOS VM para os Serviços de Domínio Azure AD [ Microsoft Docs
description: Aprenda a configurar e junte-se a uma máquina virtual CentOS Linux para um domínio gerido pelo Azure AD Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/23/2020
ms.author: iainfou
ms.openlocfilehash: d08552dcae51c897f2419d94e5e61e857247f09a
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655224"
---
# <a name="join-a-centos-linux-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Junte-se a uma máquina virtual CentOS Linux para um domínio gerido pela Azure AD Domain Services

Para permitir que os utilizadores inscrevam em máquinas virtuais (VMs) em Azure utilizando um único conjunto de credenciais, pode juntar VMs a um domínio gerido pelo Azure Ative Directory Domain Services (AD DS). Quando se junta a um VM a um domínio gerido pelo Azure AD DS, as contas de utilizador e credenciais do domínio podem ser usadas para iniciar sessão e gerir servidores. As adesões do grupo do domínio gerido pelo Azure AD DS também são aplicadas para permitir controlar o acesso a ficheiros ou serviços no VM.

Este artigo mostra-lhe como se juntar a um CentOS Linux VM a um domínio gerido pelo Azure AD DS.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, necessita dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se não tiver uma assinatura Azure, [crie uma conta.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Um inquilino azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou com um diretório apenas na nuvem.
    * Se necessário, crie um inquilino do [Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Um Azure Ative Directory Domain Services gerido domínio habilitado e configurado no seu inquilino Azure AD.
    * Se necessário, o primeiro tutorial cria e configura uma instância de Serviços de [Domínio de Diretório Ativo Azure.][create-azure-ad-ds-instance]
* Uma conta de utilizador que faz parte do domínio gerido pelo Azure AD DS.

## <a name="create-and-connect-to-a-centos-linux-vm"></a>Criar e ligar a um CentOS Linux VM

Se tiver um CentOS Linux VM existente em Azure, ligue-o a ele utilizando SSH e, em seguida, continue para o próximo passo para [começar a configurar o VM](#configure-the-hosts-file).

Se precisar de criar um VM CentOS Linux, ou pretender criar um VM de teste para utilização com este artigo, pode utilizar um dos seguintes métodos:

* [Portal do Azure](../virtual-machines/linux/quick-create-portal.md)
* [CLI do Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

Quando criar o VM, preste atenção às definições de rede virtual para se certificar de que o VM pode comunicar com o domínio gerido pelo Azure AD DS:

* Desloque o VM para a mesma rede virtual, ou uma rede virtual em que permitiu o Azure AD Domain Services.
* Coloque o VM numa subrede diferente da instância dos Serviços de Domínio Azure AD.

Uma vez implantado o VM, siga os passos para ligar ao VM utilizando SSH.

## <a name="configure-the-hosts-file"></a>Configure o ficheiro de anfitriões

Para se certificar de que o nome de anfitrião VM está corretamente configurado para o domínio gerido, edite o ficheiro */etc/anfitriões* e detetete o nome de anfitrião:

```console
sudo vi /etc/hosts
```

No ficheiro dos *anfitriões,* atualize *o* endereço local. No seguinte exemplo:

* *aaddscontoso.com* é o nome de domínio DNS do seu domínio gerido pelo Azure AD DS.
* *centos* é o nome de anfitrião do seu CentOS VM que está se juntando ao domínio gerido.

Atualize estes nomes com os seus próprios valores:

```console
127.0.0.1 centos.aaddscontoso.com centos
```

Quando estiver feito, guarde e `:wq` saia do ficheiro dos *anfitriões* utilizando o comando do editor.

## <a name="install-required-packages"></a>Instalar pacotes necessários

O VM precisa de alguns pacotes adicionais para se juntar ao VM ao domínio gerido pela AD DS Azure. Para instalar e configurar estes pacotes, atualize `yum`e instale as ferramentas de união de domínio utilizando:

```console
sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
```

## <a name="join-vm-to-the-managed-domain"></a>Junte-se à VM ao domínio gerido

Agora que os pacotes necessários são instalados no VM, junte-se ao VM ao domínio gerido pelo Azure AD DS.

1. Use `realm discover` o comando para descobrir o domínio gerido pelo Azure AD DS. O exemplo que se segue descobre o reino *AADDSCONTOSO.COM.* Especifique o seu próprio nome de domínio gerido azure AD DS em TODAS as MAIDES:

    ```console
    sudo realm discover AADDSCONTOSO.COM
    ```

   Se `realm discover` o comando não encontrar o seu domínio gerido pelo Azure AD DS, reveja os seguintes passos de resolução de problemas:

    * Certifique-se de que o domínio é acessível a partir do VM. Tente `ping aaddscontoso.com` ver se uma resposta positiva é devolvida.
    * Verifique se o VM está implantado na mesma rede virtual, ou numa rede virtual em que o domínio gerido pelo Azure AD DS está disponível.
    * Confirme que as definições do servidor DNS para a rede virtual foram atualizadas para apontar para os controladores de domínio do domínio gerido pelo Azure AD DS.

1. Agora rubrica kerberos `kinit` usando o comando. Especifique um utilizador que faça parte do domínio gerido pelo Azure AD DS. Se necessário, [adicione uma conta de utilizador a um grupo em Azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Mais uma vez, o nome de domínio gerido pela AD DS azure deve ser introduzido em TODAS as MAIDES. No exemplo seguinte, a `contosoadmin@aaddscontoso.com` conta nomeada é usada para inicializar Kerberos. Insira a sua própria conta de utilizador que faz parte do domínio gerido pelo Azure AD DS:

    ```console
    kinit contosoadmin@AADDSCONTOSO.COM
    ```

1. Por fim, junte a máquina ao domínio gerido azure AD DS utilizando o `realm join` comando. Utilize a mesma conta de utilizador que faz parte do domínio gerido pelo Azure `kinit` AD `contosoadmin@AADDSCONTOSO.COM`DS que especificou no comando anterior, tais como:

    ```console
    sudo realm join --verbose AADDSCONTOSO.COM -U 'contosoadmin@AADDSCONTOSO.COM'
    ```

Leva alguns momentos para se juntar ao VM ao domínio gerido pela AD DS Azure. A saída de exemplo que se segue mostra que o VM se juntou com sucesso ao domínio gerido pelo Azure AD DS:

```output
Successfully enrolled machine in realm
```

Se o seu VM não conseguir completar com sucesso o processo de união de domínios, certifique-se de que o grupo de segurança de rede da VM permite o tráfego de saída da Kerberos na porta 464 TCP + UDP para a subnet de rede virtual para o seu domínio gerido pelo Azure AD DS.

## <a name="allow-password-authentication-for-ssh"></a>Permitir a autenticação de senha para SSH

Por predefinição, os utilizadores só podem iniciar sessão num VM utilizando a autenticação baseada em chaves SSH. A autenticação baseada em palavra-passe falha. Quando se junta ao VM a um domínio gerido pelo Azure AD DS, essas contas de domínio precisam de utilizar a autenticação baseada em palavra-passe. Atualize a configuração SSH para permitir a autenticação baseada em palavra-passe da seguinte forma.

1. Abra o ficheiro *sshd_conf* com um editor:

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. Atualize a linha para *A Autenticação de Palavras-Passe* para *sim:*

    ```console
    PasswordAuthentication yes
    ```

    Quando estiver feito, *sshd_conf* guarde e `:wq` saia do ficheiro sshd_conf utilizando o comando do editor.

1. Para aplicar as alterações e deixar os utilizadores iniciarem o contrato com uma palavra-passe, reinicie o serviço SSH:

    ```console
    sudo systemctl restart sshd
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Conceda os privilégios do grupo 'Administradores da AAD DC'

Para conceder aos membros do grupo de *administradores da AAD DC privilégios* administrativos no CentOS VM, adicione uma entrada para os */etc/sudoers*. Uma vez adicionados, os membros do grupo `sudo` de administradores da *AAD DC* podem usar o comando no CentOS VM.

1. Abra o ficheiro *sudoers* para edição:

    ```console
    sudo visudo
    ```

1. Adicione a seguinte entrada ao fim do ficheiro */etc/sudoers.* O grupo de administradores da *AAD DC* contém espaço branco no nome, por isso inclua o personagem de fuga backslash no nome do grupo. Adicione o seu próprio nome de domínio, como *aaddscontoso.com:*

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators@aaddscontoso.com ALL=(ALL) NOPASSWD:ALL
    ```

    Quando terminar, guarde e `:wq` saia do editor usando o comando do editor.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Inscreva-se no VM usando uma conta de domínio

Para verificar se o VM foi bem-sucedido juntou-se ao domínio gerido pelo Azure AD DS, inicie uma nova ligação SSH utilizando uma conta de utilizador de domínio. Confirme que foi criado um diretório doméstico e que a adesão ao grupo a partir do domínio é aplicada.

1. Crie uma nova ligação SSH a partir da sua consola. Utilize uma conta de domínio que pertença `ssh -l` ao domínio `contosoadmin@aaddscontoso.com` gerido utilizando o comando, como e, em seguida, introduza o endereço do seu VM, como *centos.aaddscontoso.com*. Se utilizar a Concha de Nuvem Azure, utilize o endereço IP público do VM em vez do nome DNS interno.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com centos.aaddscontoso.com
    ```

1. Quando tiver ligações com sucesso ao VM, verifique se o diretório inicialmente foi inicializado corretamente:

    ```console
    pwd
    ```

    Você deve estar no diretório */home* com o seu próprio diretório que corresponda à conta de utilizador.

1. Verifique agora se os membros do grupo estão a ser resolvidos corretamente:

    ```console
    id
    ```

    Deve ver os seus membros do grupo do domínio gerido pelo Azure AD DS.

1. Se inscreveu o VM como membro do grupo de *administradores da AAD DC,* verifique se pode utilizar corretamente o `sudo` comando:

    ```console
    sudo yum update
    ```

## <a name="next-steps"></a>Passos seguintes

Se tiver problemas em ligar o VM ao domínio gerido pelo Azure AD DS ou iniciar sessão com uma conta de domínio, consulte problemas de resolução de [domínios](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
