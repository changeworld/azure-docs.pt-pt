---
title: Refuncionando o assistente de instalação Azure AD Connect [ Microsoft Docs
description: Explica como funciona o assistente de instalação da segunda vez que o executa.
keywords: O assistente de instalação Azure AD Connect permite configurar as definições de manutenção da segunda vez que o executa
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: d800214e-e591-4297-b9b5-d0b1581cc36a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/17/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5440c54b01f62b3ad61b355f4c622a31910a65c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261336"
---
# <a name="azure-ad-connect-sync-running-the-installation-wizard-a-second-time"></a>Sincronização Azure AD Connect: Executar o assistente de instalação uma segunda vez
A primeira vez que executa o assistente de instalação Azure AD Connect, ele te acompanha como configurar a sua instalação. Se voltar a executar o assistente de instalação, oferece opções de manutenção.

>[!IMPORTANT]
>Esteja ciente de que não pode executar o assistente de instalação enquanto estiver em curso uma sincronização.  Verifique se uma sincronização não está a decorrer antes de lançar o assistente.

Pode encontrar o assistente de instalação no menu inicial chamado **Azure AD Connect**.

![Menu Iniciar](./media/how-to-connect-installation-wizard/startmenu.png)

Quando iniciar o assistente de instalação, consulte uma página com estas opções:

![Página com uma lista de tarefas adicionais](./media/how-to-connect-installation-wizard/additionaltasks.png)

Se instalou a ADFS com o Azure AD Connect, tem ainda mais opções. As opções adicionais que tem para a ADFS estão documentadas na [gestão da ADFS.](how-to-connect-fed-management.md#manage-ad-fs)

Selecione uma das tarefas e clique **em Next** para continuar.

> [!IMPORTANT]
> Enquanto tiver o assistente de instalação aberto, todas as operações no motor de sincronização estão suspensas. Certifique-se de que fecha o assistente de instalação assim que tiver concluído as alterações de configuração.
>
>

## <a name="view-current-configuration"></a>Ver configuração atual
Esta opção dá-lhe uma visão rápida das suas opções atualmente configuradas.

![Página com uma lista de todas as opções e seu estado](./media/how-to-connect-installation-wizard/viewconfig.png)

Clique em **Anterior** para voltar. Se selecionar **saída,** feche o assistente de instalação.

## <a name="customize-synchronization-options"></a>Personalizar opções de sincronização
Esta opção é usada para fazer alterações na configuração de sincronização. Você vê um subconjunto de opções a partir do caminho de instalação de configuração personalizado. Vê esta opção mesmo que tenha usado inicialmente a instalação expressa.

* [Adicione mais diretórios.](how-to-connect-install-custom.md#connect-your-directories) Para remover um diretório, consulte [Eliminar um Conector](how-to-connect-sync-service-manager-ui-connectors.md#delete).
* [Alterar a filtragem de Domínio e U](how-to-connect-install-custom.md#domain-and-ou-filtering).
* Remova a filtragem do Grupo.
* [Alterar funcionalidades opcionais.](how-to-connect-install-custom.md#optional-features)

As outras opções da instalação inicial não podem ser alteradas e não estão disponíveis. Estas opções são:

* Altere o atributo a utilizar para userPrincipalName e sourceAnchor.
* Mude o método de junção de objetos de diferentes florestas.
* Ativar a filtragem baseada em grupo.

## <a name="refresh-directory-schema"></a>Refresh diretório schema
Esta opção é usada se tiver mudado o esquema numa das suas florestas AD DS no local. Por exemplo, pode ter instalado o Exchange ou atualizado para um esquema do Windows Server 2012 com objetos de dispositivo. Neste caso, é necessário instruir o Azure AD Connect a ler o esquema novamente a partir de AD DS e atualizar a sua cache. Esta ação também regenera as Regras de Sincronização. Se adicionar o esquema de troca, como exemplo, as Regras de Sincronização de Troca são adicionadas à configuração.

Quando selecionar esta opção, todos os diretórios da sua configuração estão listados. Pode manter a definição padrão e refrescar todas as florestas ou desseleccionar algumas delas.

![Página com uma lista de todos os diretórios no ambiente](./media/how-to-connect-installation-wizard/refreshschema.png)

## <a name="configure-staging-mode"></a>Modo de encenação configurar
Esta opção permite-lhe ativar e desativar o modo de paragem no servidor. Mais informações sobre o modo de encenação e como é utilizado podem ser encontradas em [Operações](how-to-connect-sync-staging-server.md).

A opção mostra se a encenação está ativada ou desativada:  
![Opção que também mostra o estado atual do modo de encenação](./media/how-to-connect-installation-wizard/stagingmodecurrentstate.png)

Para alterar o estado, selecione esta opção e selecione ou desmarque a caixa de verificação.  
![Opção que também mostra o estado atual do modo de encenação](./media/how-to-connect-installation-wizard/stagingmodeenable.png)

## <a name="change-user-sign-in"></a>Alterar o sessão de inscrição do utilizador
Esta opção permite alterar o método de entrada do utilizador de e para a sincronização de hash de e para a palavra-passe, autenticação pass-through ou federação. Não é possível mudar para **não configurar**.

Para obter mais informações sobre esta opção, consulte o utilizador de [início de sessão](plan-connect-user-signin.md#changing-the-user-sign-in-method).

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre o modelo de configuração utilizado pelo Azure AD Connect sincronizado em [Compreensão disposição declarativa](concept-azure-ad-connect-sync-declarative-provisioning.md).

**Tópicos de visão geral**

* [Sincronização Azure AD Connect: Compreender e personalizar a sincronização](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
