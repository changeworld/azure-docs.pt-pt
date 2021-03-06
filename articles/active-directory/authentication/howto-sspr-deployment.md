---
title: Considerações de implementação para o reset de palavra-passe self-service do Diretório Ativo Azure
description: Conheça considerações de implementação e estratégia para implementação bem sucedida de reset de senha de autosserviço da AD Azure
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: c11521ec074b63843b873c39102b68bf185d2821
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80676733"
---
# <a name="plan-an-azure-active-directory-self-service-password-reset-deployment"></a>Planeie uma implementação de senha de autosserviço do Azure Ative Diretório

> [!IMPORTANT]
> Este plano de implementação oferece orientação e boas práticas para implementar o reset de senha de autosserviço azure AD (SSPR).
>
> **Se for e terminar utilizador e precisar de voltar [https://aka.ms/sspr](https://aka.ms/sspr)à sua conta, vá para **.

[O Reset de Passwords self-Service (SSPR)](https://www.youtube.com/watch?v=tnb2Qf4hTP8) é uma funcionalidade azure Ative Directory (AD) que permite aos utilizadores redefinir as suas palavras-passe sem contactar o pessoal de TI para obter ajuda. Os utilizadores podem desbloquear-se rapidamente e continuar a trabalhar independentemente de onde estejam ou da hora do dia. Ao permitir que os colaboradores se desbloqueiem, a sua organização pode reduzir o tempo não produtivo e os elevados custos de apoio para as questões mais comuns relacionadas com a palavra-passe.

A SSPR tem as seguintes capacidades-chave:

* O self-service permite que os utilizadores finais reporem as suas palavras-passe expiradas ou não expiradas sem contactar um administrador ou um helpdesk para obter suporte.
* [A Password Writeback](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback) permite a gestão de senhas no local e a resolução do bloqueio da conta embora a nuvem.
* Os relatórios de atividade de gestão de passwords dão aos administradores informações sobre o reset de passwords e a atividade de registo que ocorre na sua organização.

Este guia de implantação mostra-lhe como planear e, em seguida, testar um lançamento sspr.

Para ver rapidamente a SSPR em ação e, em seguida, voltar a compreender considerações adicionais de implantação:

> [!div class="nextstepaction"]
> [Ativar o reset de palavra-passe self-service (SSPR)](tutorial-enable-sspr.md)

## <a name="learn-about-sspr"></a>Saiba mais sobre sSPR

Saiba mais sobre a SSPR. Ver [como funciona: Reset de senha de autosserviço Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks).

### <a name="key-benefits"></a>Principais vantagens

Os principais benefícios de permitir o SSPR são:

* **Gerir o custo.** A SSPR reduz os custos de suporte de TI, permitindo que os utilizadores reporem as palavras-passe por si só. Também reduz o custo de tempo perdido devido a senhas perdidas e bloqueios. 

* **Experiência intuitiva do utilizador.** Fornece um processo intuitivo de registo de utilizadores únicos que permite aos utilizadores redefinir palavras-passe e desbloquear contas a pedido de qualquer dispositivo ou localização. A SSPR permite que os utilizadores voltem ao trabalho mais rapidamente e sejam mais produtivos.

* **Flexibilidade e segurança.** A SSPR permite às empresas aceder à segurança e flexibilidade que uma plataforma em nuvem proporciona. Os administradores podem alterar as definições para acomodar novos requisitos de segurança e lançar estas alterações para os utilizadores sem interromper o seu sessão.

* **Auditoria robusta e rastreio de utilização.** Uma organização pode garantir que os sistemas de negócio permanecem seguros enquanto os seus utilizadores redefinirem as suas próprias palavras-passe. Os registos de auditoria robustos incluem informações de cada passo do processo de reset da palavra-passe. Estes registos estão disponíveis a partir de uma API e permitem ao utilizador importar os dados para um sistema de escolha de Incidentes de Segurança e Deevento (SIEM).

### <a name="licensing"></a>Licenciamento

O Azure Ative Directory é licenciado por utilizador, o que significa que cada utilizador necessita de uma licença adequada para as funcionalidades que utiliza. Recomendamos o licenciamento baseado em grupo para sPr. 

Para comparar edições e funcionalidades e ativar o licenciamento de grupo ou utilizador, consulte os requisitos de licenciamento para o reset de [palavra-passe autosserviço Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-licensing).

Para obter mais informações sobre preços, consulte [o preço do Diretório Ativo Azure.](https://azure.microsoft.com/pricing/details/active-directory/)

### <a name="prerequisites"></a>Pré-requisitos

* Um inquilino do Azure AD em funcionamento com, pelo menos, uma licença de avaliação ativada. Se necessário, [crie um de graça.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

* Uma conta com privilégios de Administrador Global.


### <a name="training-resources"></a>Recursos de formação

| Recursos| Link e Descrição |
| - | - |
| Vídeos| [Capacitar os seus utilizadores com melhor escalabilidade de TI](https://youtu.be/g9RpRnylxS8) 
| |[O que é o reset da palavra-passe self-service?](https://youtu.be/hc97Yx5PJiM)|
| |[Implementação de reset de senha de autosserviço](https://www.youtube.com/watch?v=Pa0eyqjEjvQ&index=18&list=PLLasX02E8BPBm1xNMRdvP6GtA6otQUqp0)|
| |[Como configurar o reset de palavra-passe self-service para utilizadores em Azure AD?](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/) |
| |[Como [preparar os utilizadores] para registar [as suas] informações de segurança para o Diretório Ativo do Azure](https://youtu.be/gXuh0XS18wA) |
| Cursos online|[Gestão de Identidades no Diretório Ativo microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-active-directory-managing-identities) Utilize o SSPR para dar aos seus utilizadores uma experiência moderna e protegida. Consulte especialmente o módulo "[Managing Azure Ative Directory Users and Groups](https://app.pluralsight.com/library/courses/microsoft-azure-active-directory-managing-identities/table-of-contents)". |
|Cursos pagos pluralsight |[As Questões de Gestão de Identidade e Acesso](https://www.pluralsight.com/courses/identity-access-management-issues) Saiba mais sobre o IAM e questões de segurança para estar atento na sua organização. Consulte especialmente o módulo "Outros Métodos de Autenticação".|
| |[Começando com a Microsoft Enterprise Mobility Suite](https://www.pluralsight.com/courses/microsoft-enterprise-mobility-suite-getting-started) Aprenda as melhores práticas para alargar os ativos no local à nuvem de uma forma que permita a autenticação, autorização, encriptação e uma experiência móvel segura. Consulte especialmente o módulo "Configurar Funcionalidades Avançadas do Microsoft Azure Ative Directory Premium".
|Tutoriais |[Complete uma senha de autosserviço Azure AD reset-out piloto](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot) |
| |[Habilitar a reescrita da palavra-passe](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-writeback) |
| |[Palavra-passe da AD Azure redefinida a partir do ecrã de login para windows 10](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows) |
| FAQ|[Gestão de passwords frequentemente perguntas](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-faq) |


### <a name="solution-architecture"></a>Arquitetura de soluções

O exemplo seguinte descreve a arquitetura de solução de reset de palavra-passe para ambientes híbridos comuns.

![diagrama de arquitetura de solução](./media/howto-sspr-deployment//solutions-architecture.png)

Descrição do fluxo de trabalho

Para redefinir a palavra-passe, os utilizadores vão para o portal de reset de [palavra-passe](https://aka.ms/sspr). Devem verificar o método ou métodos de autenticação previamente registados para provar a sua identidade. Se conseguirem redefinir a palavra-passe com sucesso, iniciam o processo de reset.

* Para utilizadores apenas na nuvem, a SSPR armazena a nova senha em Azure AD. 

* Para utilizadores híbridos, a SSPR reescreve a palavra-passe para o Diretório Ativo on-prem através do serviço Azure AD Connect. 

Nota: Para utilizadores que tenham sincronização de [hash password (PHS)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) desativado, a SSPR armazena as palavras-passe apenas no Diretório Ativo on-prem.

### <a name="best-practices"></a>Melhores práticas

Pode ajudar os utilizadores a registarem-se rapidamente, implementando o SSPR ao lado de outra aplicação ou serviço popular na organização. Esta ação gerará um grande volume de inscrições e conduzirá o registo.

Antes de implementar o SSPR, pode optar por determinar o número e o custo médio de cada chamada de reset de palavra-passe. Você pode usar este post de envio de dados para mostrar o valor que a SSPR está a trazer para a organização.

#### <a name="enable-combined-registration-for-sspr-and-mfa"></a>Permitir o registo combinado de SSPR e MFA

A Microsoft recomenda que as organizações permitam a experiência de registo combinado para SSPR e autenticação de vários fatores. Quando ativar esta experiência de registo combinado, os utilizadores só precisam de selecionar as suas informações de registo uma vez para ativar ambas as funcionalidades.

A experiência de registo combinado não requer que as organizações permitam tanto a Autenticação Multi-Factor SSPR como a Azure. O registo combinado proporciona às organizações uma melhor experiência de utilizador. Para mais informações, consulte o registo combinado de informações de [segurança (pré-visualização)](concept-registration-mfa-sspr-combined.md)

## <a name="plan-the-deployment-project"></a>Planear o projeto de implantação

Considere as suas necessidades organizacionais enquanto determina a estratégia para esta implantação no seu ambiente.

### <a name="engage-the-right-stakeholders"></a>Envolver as partes interessadas certas

Quando os projetos tecnológicos falham, normalmente fazem-no devido a expectativas desajustadas sobre impacto, resultados e responsabilidades. Para evitar estas armadilhas, [certifique-se](https://aka.ms/deploymentplans) de que está a envolver as partes interessadas certas e que as partes interessadas no projeto são bem compreendidas documentando as partes interessadas e as suas inputs e responsabilidades do projeto.

#### <a name="required-administrator-roles"></a>Funções de administrador necessárias


| Papel Empresarial/Persona| Função Azure AD (se necessário) |
| - | - |
| Mesa de apoio de nível 1| Administrador de palavras-passe |
| Mesa de apoio de nível 2| Administrador de utilizadores |
| Administrador da SSPR| Administrador global |


### <a name="plan-communications"></a>Planear as comunicações

A comunicação é fundamental para o sucesso de qualquer novo serviço. Deve comunicar proativamente com os seus utilizadores como a sua experiência mudará, quando irá mudar e como obter suporte se experimentarem problemas. Reveja a [palavra-passe self-service redefinir materiais de lançamento no centro de descarregamento da Microsoft](https://www.microsoft.com/download/details.aspx?id=56768) para ideias sobre como planear a sua estratégia de comunicação de utilizador final.

### <a name="plan-a-pilot"></a>Planeie um piloto

Recomendamos que a configuração inicial do SSPR esteja num ambiente de teste. Comece com um grupo piloto, permitindo a SSPR para um subconjunto de utilizadores na sua organização. Consulte [as melhores práticas para um piloto.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans)

Para criar um grupo, veja como [criar um grupo e adicionar membros no Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-groups-create-azure-portal). 

## <a name="plan-configuration"></a>Configuração do plano

São necessárias as seguintes definições para ativar o SSPR juntamente com os valores recomendados.

| Área | Definição | Valor |
| --- | --- | --- |
| **Propriedades SSPR** | Reset de palavra-passe de autosserviço ativado | **Grupo selecionado** para piloto / **Tudo** para produção |
| **Métodos de autenticação** | Métodos de autenticação necessários para se registar | Sempre mais 1 do que o necessário para reset |
|   | Métodos de autenticação necessários para redefinir | Um ou dois |
| **Registo** | Exigir que os utilizadores se registem ao iniciar sessão | Sim |
|   | Número de dias antes de ser pedido aos utilizadores que voltem a confirmar as informações de autenticação | 90 - 180 dias |
| **Notificações** | Notificar os utilizadores sobre reposições de palavras-passe | Sim |
|   | Notificar todos os administradores quando outros administradores repõem as palavras-passe deles | Sim |
| **Personalização** | Personalizar link de helpdesk | Sim |
|   | E-mail ou URL de helpdesk personalizado | Site de suporte ou endereço de e-mail |
| **Integração no local** | Escreva palavras-passe para a AD no local | Sim |
|   | Permitir que os utilizadores desbloqueiem a conta sem redefinir a palavra-passe | Sim |

### <a name="sspr-properties"></a>Propriedades SSPR

Ao ativar o SSPR, escolha um grupo de segurança adequado no ambiente piloto.

* Para impor o registo de SSPR para todos, recomendamos a utilização da opção **All.**
* Caso contrário, selecione o grupo de segurança Azure AD ou AD apropriado.

### <a name="authentication-methods"></a>Métodos de autenticação

Quando o SSPR está ativado, os utilizadores só podem redefinir a sua palavra-passe se tiverem dados presentes nos métodos de autenticação que o administrador ativou. Os métodos incluem notificação de telefone, autenticador, questões de segurança, etc. Para mais informações, consulte [Os métodos de autenticação?](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)

Recomendamos as seguintes definições do método de autenticação:

* Detete os métodos de **autenticação necessários para se registar** em pelo menos mais um do que o número necessário para repor. Permitir a autenticação múltipla confere flexibilidade aos utilizadores quando precisam de ser redefinidos.

* Definir **número de métodos necessários para repor** para um nível adequado à sua organização. Um requer menos atrito, enquanto dois podem aumentar a sua postura de segurança. 

Nota: O utilizador deve ter os métodos de autenticação configurados nas políticas e restrições de [password no Diretório Ativo do Azure](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy).

### <a name="registration-settings"></a>Configurações de registo

Definir **Exigir que os utilizadores se registem ao iniciar sessão** no **Yes**. Esta definição requer que os utilizadores se registem ao iniciar sessão, garantindo que todos os utilizadores estão protegidos.

Set **Número de dias antes de os utilizadores forem solicitados a reconfirmar as suas informações** de autenticação entre **90** e **180** dias, a menos que a sua organização tenha uma necessidade de negócio para um prazo mais curto.

### <a name="notifications-settings"></a>Definições de notificações

Configure tanto os **utilizadores notificar em resets de palavra-passe** como os administradores notificar em todos os administradores quando outros administradores redefinirem a sua **palavra-passe** para **Sim**. Selecionar **Sim** em ambos aumenta a segurança, garantindo que os utilizadores estão cientes quando a sua palavra-passe é redefinida. Também garante que todos os administradores estão cientes quando um administrador muda uma palavra-passe. Se os utilizadores ou administradores receberem uma notificação e não tiverem iniciado a alteração, podem reportar imediatamente um potencial problema de segurança.

### <a name="customization-settings"></a>Definições de personalização

É fundamental personalizar o e-mail ou URL do helpdesk para garantir que os utilizadores que sofrem de problemas possam obter ajuda imediatamente. Detete esta opção para um endereço de e-mail de helpdesk comum ou página web que os seus utilizadores estão familiarizados. 

Para mais informações, consulte [Personalize a funcionalidade Azure AD para redefinir a palavra-passe de autosserviço](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization).

### <a name="password-writeback"></a>Reescrita de palavra-passe

**A Password Writeback** está ativada com [o Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity) e escreve resets de palavra-passe na nuvem de volta a um diretório existente no local em tempo real. Para mais informações, consulte [o que é a Palavra-passe Writeback?](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)

Recomendamos as seguintes definições:

* Certifique-se de que **as palavras-passe de redação para a AD no local** estão definidas para **Sim**. 
* Defina o **Permitir que os utilizadores desbloqueiem a conta sem redefinir a palavra-passe** para **Sim**.

Por padrão, o Azure AD desbloqueia contas quando executa uma reposição de palavra-passe.

### <a name="administrator-password-setting"></a>Definição de palavra-passe do administrador

As contas dos administradores têm permissões elevadas. Os administradores empresariais ou de domínio no local não podem redefinir as suas palavras-passe através do SSPR. As contas de administração no local têm as seguintes restrições:

* só pode alterar a sua palavra-passe no seu ambiente on-prem.
* nunca pode usar as perguntas e respostas secretas como um método para redefinir a sua palavra-passe.

Recomendamos que não sincronize as suas contas de administração de diretório sinuoso com a Azure AD.

### <a name="environments-with-multiple-identity-management-systems"></a>Ambientes com múltiplos sistemas de gestão de identidade

Alguns ambientes têm múltiplos sistemas de gestão de identidade. No local, gestores de identidade como oracle AM e SiteMinder, requerem sincronização com AD para senhas. Pode fazê-lo utilizando uma ferramenta como o Serviço de Notificação de Alteração de Passwords (PCNS) com o Microsoft Identity Manager (MIM). Para encontrar informações sobre este cenário mais complexo, consulte o artigo Implementar o Serviço de Notificação de Alteração de [Palavras-Passe MIM num controlador](https://docs.microsoft.com/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller)de domínio .

## <a name="plan-testing-and-support"></a>Teste e Suporte de Planos

Em cada fase da sua implantação de grupos piloto iniciais através de toda a organização, certifique-se de que os resultados são como esperado.

### <a name="plan-testing"></a>Teste de planos

Para garantir que a sua implementação funciona como esperado, planeje um conjunto de casos de teste para validar a implementação. Para avaliar os casos de teste, precisa de um utilizador de teste não administrador com uma palavra-passe. Se precisar de criar um utilizador, consulte [Adicionar novos utilizadores ao Diretório Ativo Do Azure](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

A tabela seguinte inclui cenários de teste úteis que pode usar para documentar os resultados esperados das suas organizações com base nas suas políticas.
<br>


| Caso de negócios| Resultados esperados |
| - | - |
| Portal SSPR é acessível a partir da rede corporativa| Determinado pela sua organização |
| Portal SSPR é acessível de fora da rede corporativa| Determinado pela sua organização |
| Redefinir a palavra-passe do utilizador a partir do navegador quando o utilizador não está ativado para redefinir palavra-passe| O utilizador não é capaz de aceder ao fluxo de redefinição da palavra-passe |
| Redefinir a palavra-passe do utilizador do navegador quando o utilizador não se registou para redefinir a palavra-passe| O utilizador não é capaz de aceder ao fluxo de redefinição da palavra-passe |
| O utilizador insere-se quando é obrigado a fazer o registo de redefinição da palavra-passe| Solicita ao utilizador que registe informações de segurança |
| O utilizador insere-se quando o registo de reset da palavra-passe estiver completo| Solicita ao utilizador que registe informações de segurança |
| Portal SSPR é acessível quando o utilizador não tem licença| É acessível |
| Redefinir a palavra-passe do utilizador a partir do Windows 10 Azure AD juntou-se ao ecrã de bloqueio do dispositivo| O utilizador pode redefinir a palavra-passe |
| Os dados de registo e utilização da SSPR estão disponíveis para administradores em tempo quase real| Está disponível através de registos de auditoria |

Também pode consultar uma palavra-passe de reset de [autosserviço Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot). Neste tutorial, permitirá um lançamento piloto da SSPR na sua organização e teste utilizando uma conta não administradorada.

### <a name="plan-support"></a>Apoio ao plano

Embora a SSPR não crie normalmente problemas de utilizador, é importante preparar pessoal de apoio para lidar com questões que possam surgir. Embora um administrador possa redefinir a palavra-passe para utilizadores finais através do portal Azure AD, é melhor ajudar a resolver o problema através de um processo de suporte de self-service.

Para permitir o sucesso da sua equipa de suporte, pode criar uma FAQ com base nas questões que recebe dos seus utilizadores. Eis alguns exemplos:

| Cenários| Descrição |
| - | - |
| O utilizador não tem quaisquer métodos de autenticação registados disponíveis| Um utilizador está a tentar redefinir a sua palavra-passe, mas não tem nenhum dos métodos de autenticação que registou disponíveis (Exemplo: deixaram o telemóvel em casa e não conseguem aceder a e-mails) |
| O utilizador não está a receber uma mensagem ou uma chamada no seu escritório ou telemóvel| Um utilizador está a tentar verificar a sua identidade por sms ou chamada, mas não está a receber um texto/chamada. |
| O utilizador não pode aceder ao portal de reset de palavra-passe| Um utilizador quer redefinir a sua palavra-passe, mas não está habilitado para redefinir palavra-passe e não consegue aceder à página para atualizar palavras-passe. |
| O utilizador não pode definir uma nova senha| Um utilizador completa a verificação durante o fluxo de redefinição da palavra-passe, mas não pode definir uma nova palavra-passe. |
| O utilizador não vê uma ligação de palavra-passe redefinida num dispositivo Windows 10| Um utilizador está a tentar redefinir a palavra-passe do ecrã de bloqueio do Windows 10, mas o dispositivo não está associado ao Azure AD, ou a política do dispositivo Intune não está ativada |

### <a name="plan-rollback"></a>Plano de reversão

Para reverter a implantação:

* para um único utilizador, remova o utilizador do grupo de segurança 

* para um grupo, remova o grupo da configuração SSPR

* Para todos, desabilite o SSPR para o inquilino da AD Azure

## <a name="deploy-sspr"></a>Implementar a SSPR

Antes de ser implementado, certifique-se de que fez o seguinte:

1. Criou e começou a executar o seu plano de [comunicação.](#plan-communications)

1. Determinou as definições de [configuração](#plan-configuration)adequadas .

1. Identificou os utilizadores e grupos para os ambientes [piloto](#plan-a-pilot) e de produção.

1. [Definições de configuração determinadas](#plan-configuration) para registo e self-service.

1. [Redação da palavra-passe configurada](#password-writeback) se tiver um ambiente híbrido.


**Está pronto para implantar sSPR!**

Consulte o reset da [palavra-passe de autosserviço Enable](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot#enable-self-service-password-reset) para obter instruções passo a passo completas na configuração das seguintes áreas.

1. [Métodos de autenticação](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)

1. [Configurações de registo](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined)

1. [Definições de notificações](#notifications-settings)

1. [Definições de personalização](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)

1. [Integração no local](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-writeback)

### <a name="enable-sspr-in-windows"></a>Ativar sSPR no Windows
Para máquinas que executam o Windows 7, 8, 8.1 e 10, pode [permitir que os utilizadores reporem a sua palavra-passe no windows sign no ecrã](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-windows)

## <a name="manage-sspr"></a>Gerir o SSPR

A Azure AD pode fornecer informações adicionais sobre o seu desempenho sspr através de auditorias e relatórios.

### <a name="password-management-activity-reports"></a>Relatórios de atividade de gestão de passwords 

Pode utilizar relatórios pré-construídos no portal Azure para medir o desempenho do SSPR. Se você estiver devidamente licenciado, você também pode criar consultas personalizadas. Para mais informações, consulte [opções de Reporte para gestão de passwords Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting)

> [!NOTE]
>  Você deve ser [um administrador global](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles), e você deve optar por que estes dados sejam recolhidos para a sua organização. Para optar, deve visitar o separador Relatório ou os registos de auditoria no Portal Azure pelo menos uma vez. Até lá, os dados não recolhem para a sua organização.

Os registos de auditoria para registo e reset de palavra-passe estão disponíveis por 30 dias. Se a auditoria de segurança dentro da sua corporação necessitar de uma retenção mais longa, os registos devem ser exportados e consumidos numa ferramenta SIEM, como [o Azure Sentinel,](https://docs.microsoft.com/azure/sentinel/connect-azure-active-directory)Splunk ou ArcSight.

![Screenshot de relatório sspr](./media/howto-sspr-deployment/sspr-reporting.png)

### <a name="authentication-methods--usage-and-insights"></a>Métodos de autenticação- Utilização e Insights

[O uso e insights](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights) permitem-lhe entender como os métodos de autenticação para funcionalidades como o Azure MFA e o SSPR estão a trabalhar na sua organização. Esta capacidade de reporte fornece à sua organização os meios para entender quais os métodos que se registam e como usá-los.

### <a name="troubleshoot"></a>Resolução de problemas

* Consulte o reset da [palavra-passe self-service Troubleshoot](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-troubleshoot) 

* Siga a [gestão de passwords frequentemente colocadas perguntas](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-faq) 

### <a name="helpful-documentation"></a>Documentação útil

* [What are authentication methods?](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods) (O que são os métodos de autenticação?)

* [Como funciona: Reset de senha de autosserviço da Azure AD?](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks)

* [Personalize a funcionalidade Azure AD para reset de senha de autosserviço](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)

* [Password policies and restrictions in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy) (Políticas de palavras-passe e restrições no Azure Active Directory)

* [O que é a redação da palavra-passe?](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)

## <a name="next-steps"></a>Passos seguintes

* Para começar a implementar sSPR, consulte [Enable Azure AD self-service password reset](tutorial-enable-sspr.md)

* [Considere implementar a proteção de senhas azure AD](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad)

* [Considere implementar o Bloqueio Inteligente Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout)