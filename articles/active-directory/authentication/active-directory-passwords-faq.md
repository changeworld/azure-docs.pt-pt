---
title: Palavra-passe self-service redefinir FAQ - Diretório Ativo Azure
description: Perguntas frequentes sobre o reset da palavra-passe autosserviço da AD Azure
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae3ace24d6f33702c15a364b913f841d90fd8872
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654199"
---
# <a name="password-management-frequently-asked-questions"></a>Gestão de passwords frequentemente perguntas

Seguem-se algumas perguntas frequentes (FAQ) para todas as coisas relacionadas com o reset da palavra-passe.

Se tiver uma pergunta geral sobre o Azure Ative Directory (Azure AD) e o reset de senha de autosserviço (SSPR) que não seja respondida aqui, pode pedir ajuda à comunidade no [fórum Azure AD.](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD) Membros da comunidade incluem engenheiros, gestores de produtos, MVPs e outros profissionais de TI.

Este FAQ é dividido nas seguintes secções:

* [Perguntas sobre o registo de redefinição de palavra-passe](#password-reset-registration)
* [Perguntas sobre reset de palavra-passe](#password-reset)
* [Perguntas sobre mudança de palavra-passe](#password-change)
* [Perguntas sobre relatórios de gestão de passwords](#password-management-reports)
* [Perguntas sobre a reescrita de palavra-passe](#password-writeback)

## <a name="password-reset-registration"></a>Registo de reposição de palavra-passe

* **P: Os meus utilizadores podem registar os seus próprios dados de redefinição de palavra-passe?**

  > **R:** Sim. Enquanto a redefinição da palavra-passe estiver ativada e estiveremhttps://aka.ms/ssprsetup) licenciadas, os utilizadores podem ir ao portal de registo de redefinição de passwords ( para registar as suas informações de autenticação. Os utilizadores também podem registar-se através do Painel de Acesso .https://myapps.microsoft.com) Para se registar através do Painel de Acesso, eles precisam selecionar a sua imagem de perfil, selecionar **Perfil,** e, em seguida, selecionar o Registo para a opção de **redefinição de palavra-passe.**
  >
  >
* **P: Se eu ativar o reset de palavra-passe para um grupo e, em seguida, decidir enableit-lo para todos os meus utilizadores são obrigados a reregistar??**

  > **R:** Não. Os utilizadores que tenham dados de autenticação preenchidos não são obrigados a reregistar-se.
  >
  >
* **P: Posso definir dados de redefinição de palavra-passe em nome dos meus utilizadores?**

  > **A:** Sim, pode fazê-lo com o Azure AD Connect, PowerShell, o [portal Azure,](https://portal.azure.com)ou o [centro de administração Microsoft 365](https://admin.microsoft.com). Para mais informações, consulte dados [utilizados pelo reset da palavra-passe autosserviço Azure AD](howto-sspr-authenticationdata.md).
  >
  >
* **P: Posso sincronizar dados para questões de segurança a partir do local?**

  > **A:** Não, isto não é possível hoje.
  >
  >
* **P: Os meus utilizadores podem registar dados de forma a que outros utilizadores não possam ver estes dados?**

  > **R:** Sim. Quando os utilizadores registam dados utilizando o portal de registo de redefinição de palavra-passe, os dados são guardados em campos de autenticação privados que são visíveis apenas para administradores globais e para o utilizador.
  >
  >
* **P: Os meus utilizadores têm de ser registados antes de poderem utilizar a redefinição da palavra-passe?**

  > **R:** Não. Se definir informações de autenticação suficientes em seu nome, os utilizadores não têm de se registar. O reset de palavra-passe funciona desde que tenha devidamente formatado os dados armazenados nos campos apropriados do diretório.
  >
  >
* **P: Posso sincronizar ou definir o telefone de autenticação, o email de autenticação ou os campos telefónicos alternativos de autenticação em nome dos meus utilizadores?**

  > **A:** Os campos que podem ser definidos por um Administrador Global são definidos no artigo Requisitos de [Dados SSPR](howto-sspr-authenticationdata.md).
  >
  >
* **P: Como é que o portal de registo determina quais as opções a mostrar aos meus utilizadores?**

  > **A:** O portal de registo de redefinição de palavra-passe mostra apenas as opções que tem ativado para os seus utilizadores. Estas opções encontram-se na secção política de **redefinição da palavra-passe** do utilizador do separador **Configurado** do seu diretório. Por exemplo, se não ativar questões de segurança, então os utilizadores não podem registar-se para essa opção.
  >
  >
* **P: Quando é considerado registado um utilizador?**

  > **A:** Um utilizador é considerado registado para SSPR quando tiver registado pelo menos o **Número de métodos necessários para redefinir** uma palavra-passe que definiu no [portal Azure](https://portal.azure.com).
  >
  >

## <a name="password-reset"></a>Reposição de palavras-passe

* **P: Impede os utilizadores de várias tentativas de redefinir uma palavra-passe num curto espaço de tempo?**

  > **A:** Sim, existem funcionalidades de segurança incorporadas no reset de palavra-passe para protegê-la do uso indevido. 
  >
  > Os utilizadores podem tentar apenas cinco tentativas de reset de senha num período de 24 horas antes de serem bloqueados durante 24 horas. 
  >
  > Os utilizadores podem tentar validar um número de telefone, enviar um SMS ou validar questões de segurança e respostas apenas cinco vezes no prazo de uma hora antes de serem bloqueados durante 24 horas. 
  >
  > Os utilizadores podem enviar um e-mail no máximo 10 vezes num período de 10 minutos antes de serem bloqueados durante 24 horas.
  >
  > Os contadores são redefinidos assim que um utilizador repõe a sua palavra-passe.
  >
  >
* **P: Quanto tempo devo esperar para receber um e-mail, SMS ou chamada telefónica a partir do reset da palavra-passe?**

  > **A:** E-mails, mensagens SMS e chamadas telefónicas devem chegar em menos de um minuto. O caso normal é de 5 a 20 segundos.
  > Se não receber a notificação neste período de tempo:
  > * Verifique a sua pasta de lixo.
  > * Verifique se o número ou e-mail que está a ser contactado é o que espera.
  > * Verifique se os dados de autenticação no diretório estão corretamente formatados, por exemplo, +1 4255551234 ou *contoso.com do utilizador\@*. 
* **P: Que idiomas são suportados por reset de palavra-passe?**

  > **A:** A palavra-passe repõe UI, Mensagens SMS e chamadas de voz estão localizadas nas mesmas línguas que são suportadas no Office 365.
  >
  >
* **P: Que partes da experiência de reset de palavra-passe são marcadas quando eu coloco os itens de marca organizacional no separador configurado do meu diretório?**

  > **A:** O portal de reset de palavra-passe mostra o logótipo da sua organização e permite configurar o link "Contacte o seu administrador" para apontar para um e-mail ou URL personalizados. Qualquer e-mail enviado por reset de palavra-passe inclui o logótipo, cores e nome da sua organização no corpo do e-mail, e é personalizado a partir das definições para esse nome em particular.
  >
  >
* **P: Como posso educar os meus utilizadores sobre onde ir para redefinir as suas palavras-passe?**

  > **A:** Experimente algumas das sugestões no nosso artigo de implantação da [SSPR.](howto-sspr-deployment.md#plan-communications)
  >
  >
* **P: Posso usar esta página a partir de um dispositivo móvel?**

  > **A:** Sim, esta página funciona em dispositivos móveis.
  >
  >
* **P: Suporta desbloquear contas locais do Diretório Ativo quando os utilizadores redefinirem as suas palavras-passe?**

  > **R:** Sim. Quando um utilizador repõe a sua palavra-passe, se a reescrita de palavra-passe tiver sido implementada através do Azure AD Connect, a conta desse utilizador é automaticamente desbloqueada quando redefinir a sua palavra-passe.
  >
  >
* **P: Como posso integrar a redefinição da palavra-passe diretamente na experiência de entrada no ambiente de trabalho do meu utilizador?**

  > **A:** Se for um cliente Azure AD Premium, pode instalar o Microsoft Identity Manager sem custos adicionais e implementar a solução de reset de palavra-passe no local.
  >
  >
* **P: Posso definir diferentes questões de segurança para diferentes locais?**

  > **A:** Não, isto não é possível hoje.
  >
  >
* **P: Quantas perguntas posso configurar para a opção de autenticação de questões de segurança?**

  > **A:** Pode configurar até 20 questões de segurança personalizadas no [portal Azure](https://portal.azure.com).
  >
  >
* **P: Quanto tempo podem ser as questões de segurança?**

  > **A:** As questões de segurança podem ter 3 a 200 caracteres de comprimento.
  >
  >
* **P: Quanto tempo podem ser as respostas às questões de segurança?**

  > **A:** As respostas podem ter 3 a 40 caracteres de comprimento.
  >
  >
* **P: As respostas duplicadas às questões de segurança são rejeitadas?**

  > **A:** Sim, rejeitamos respostas duplicadas a questões de segurança.
  >
  >
* **P: Um utilizador pode registar a mesma questão de segurança mais de uma vez?**

  > **R:** Não. Depois de um utilizador registar uma pergunta específica, não pode registar-se pela segunda vez.
  >
  >
* **P: É possível estabelecer um limite mínimo de questões de segurança para registo e reset?**

  > **A:** Sim, um limite pode ser definido para o registo e outro para reset. Três a cinco questões de segurança podem ser necessárias para o registo, e três a cinco questões podem ser necessárias para repor.
  >
  >
* **P: Configurei a minha política para exigir que os utilizadores utilizem questões de segurança para reset, mas os administradores do Azure parecem estar configurados de forma diferente.**

  > **A:** Este é o comportamento esperado. A Microsoft impõe uma política de reposição de palavra-passe predefinida forte de duas portas para qualquer função de administrador do Azure. Isto impede os administradores de utilizarem questões de segurança. Pode encontrar mais informações sobre esta política nas políticas e restrições de Password no artigo [do Diretório Ativo Azure.](concept-sspr-policy.md)
  >
  >
* **P: Se um utilizador registou mais do que o número máximo de perguntas necessárias para repor, como são selecionadas as questões de segurança durante o reset?**

  > **R:** *N* número de questões de segurança são selecionadas aleatoriamente fora do número total de perguntas para as quais um utilizador registou, onde *N* é o valor definido para o Número de **Perguntas necessárias para redefinir a** opção. Por exemplo, se um utilizador tiver registado cinco questões de segurança, mas apenas três são obrigados a redefinir uma palavra-passe, três das cinco perguntas são selecionadas aleatoriamente e são apresentadas no reset. Para evitar a martelada de perguntas, se o utilizador obtiver as respostas às perguntas erradas, o processo de seleção recomeça.
  >
  >
* **P: Quanto tempo é válido o e-mail e as senhas de sms únicas?**

  > **A:** O tempo de vida útil da sessão para reset de palavra-passe é de 15 minutos. Desde o início da operação de reset da palavra-passe, o utilizador tem 15 minutos para redefinir a sua palavra-passe. O e-mail e a senha de uma única vez são válidos por 5 minutos durante a sessão de reset da palavra-passe.
  >
  >
* **P: Posso bloquear os utilizadores de redefinir a sua palavra-passe?**

  > **A:** Sim, se utilizar um grupo para ativar o SSPR, pode remover um utilizador individual do grupo que permite aos utilizadores redefinirem a sua palavra-passe. Se o utilizador for um Administrador Global, manterá a capacidade de redefinir a sua palavra-passe e esta não pode ser desativada.
  >
  >

## <a name="password-change"></a>Alteração da palavra-passe

* **P: Onde devem os meus utilizadores alterar as suas palavras-passe?**

  > **A:** Os utilizadores podem alterar as suas palavras-passe em qualquer lugar que vejam a sua imagem de perfil ou ícone, como no canto superior direito do seu portal [office 365](https://portal.office.com) ou [experiências do Painel](https://myapps.microsoft.com) de Acesso. Os utilizadores podem alterar as suas palavras-passe a partir da página de Perfil do Painel de [Acesso](https://account.activedirectory.windowsazure.com/r#/profile). Os utilizadores também podem ser convidados a alterar automaticamente as suas palavras-passe na página de entrada de anúncios da AD Azure se as suas palavras-passe tiverem expirado. Finalmente, os utilizadores podem navegar diretamente no portal de alteração de [palavras-passe DaA Azure](https://account.activedirectory.windowsazure.com/ChangePassword.aspx) se quiserem alterar as suas palavras-passe.
  >
  >
* **P: Os meus utilizadores podem ser notificados no portal do Office quando a sua senha no local expirar?**

  > **A:** Sim, isso é possível hoje se você usar Serviços da Federação de Diretório Ativo (AD FS). Se utilizar AD FS, siga as instruções na política de envio de passwords com o artigo [AD FS.](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-ad-fs-to-send-password-expiry-claims?f=255&MSPPError=-2147217396) Se utilizar a sincronização de haxixe de senha, isso não é possível hoje em dia. Não sincronizamos as políticas de passwords de diretórios no local, por isso não é possível publicar notificações de expiração para experiências na nuvem. Em qualquer dos casos, também é possível [notificar os utilizadores cujas palavras-passe estão prestes a expirar através](https://social.technet.microsoft.com/wiki/contents/articles/23313.notify-active-directory-users-about-password-expiry-using-powershell.aspx)do PowerShell .
  >
  >
* **P: Posso impedir os utilizadores de alterarem a sua palavra-passe?**

  > **A:** Para utilizadores apenas na nuvem, as alterações de palavra-passe não podem ser bloqueadas. Para os utilizadores no local, pode definir o Utilizador não pode alterar a opção de **palavra-passe** para selecionada. Os utilizadores selecionados não podem alterar a sua palavra-passe.
  >
  >

## <a name="password-management-reports"></a>Relatórios de gestão de passwords

* **P: Quanto tempo leva para os dados aparecerem nos relatórios de gestão de passwords?**

  > **A:** Os dados devem aparecer nos relatórios de gestão de senhas em 5 a 10 minutos. Em alguns casos, pode levar até uma hora para aparecer.
  >
  >
* **P: Como posso filtrar os relatórios de gestão de passwords?**

  > **A:** Para filtrar os relatórios de gestão de passwords, selecione a pequena lupa para a extrema direita das etiquetas da coluna, perto do topo do relatório. Se quiser fazer uma filtragem mais rica, pode transferir o relatório para o Excel e criar uma tabela de pivôs.
  >
  >
* **P: Qual é o número máximo de eventos que são armazenados nos relatórios de gestão de passwords?**

  > **A:** Até 75.000 eventos de reset de palavra-passe ou reset de palavra-passe são armazenados nos relatórios de gestão de passwords, até 30 dias. Estamos a trabalhar para expandir este número para incluir mais eventos.
  >
  >
* **P: Até onde vão os relatórios de gestão de passwords?**

  > **A:** Os relatórios de gestão de passwords mostram operações que ocorreram nos últimos 30 dias. Por enquanto, se precisar de arquivar estes dados, pode descarregar os relatórios periodicamente e guardá-los num local separado.
  >
  >
* **P: Existe um número máximo de linhas que podem aparecer nos relatórios de gestão de passwords?**

  > **R:** Sim. Um máximo de 75.000 linhas podem aparecer em qualquer um dos relatórios de gestão de passwords, quer sejam mostrados na UI ou descarregados.
  >
  >
* **P: Existe uma API para aceder aos dados de reset ou registo de informação de informação?**

  > **R:** Sim. Para saber como pode aceder ao fluxo de dados de redefinição de password, consulte [Saiba como aceder programaticamente a eventos de redefinição](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent)de passwords .
  >
  >

## <a name="password-writeback"></a>Repetição de escrita de palavras-passe

* **P: Como funciona a escrita da palavra-passe nos bastidores?**

  > **A:** Veja o artigo Como funciona a [redação de palavras-passe](howto-sspr-writeback.md) para uma explicação do que acontece quando permite a reescrita de palavra-passe e como os dados fluem através do sistema de volta para o seu ambiente no local.
  >
  >
* **P: Quanto tempo demora a reescrita da palavra-passe para o trabalho? Existe um atraso de sincronização como existe com sincronização de hash password?**

  > **A:** A reescrita da palavra-passe é instantânea. É um pipeline sincronizado que funciona fundamentalmente diferente da sincronização do hash password. A reescrita de palavra-passe permite que os utilizadores obtem feedback em tempo real sobre o sucesso da sua reset ou alteração da operação de alteração da sua palavra-passe. O tempo médio para uma reescrita bem sucedida de uma palavra-passe é inferior a 500 ms.
  >
  >
* **P: Se a minha conta no local está desativada, como é que a minha conta na nuvem e o meu acesso são afetados?**

  > **A:** Se o seu ID no local estiver desativado, o id e o acesso na nuvem também serão desativados no intervalo de sincronização seguinte através do Azure AD Connect. Por padrão, esta sincronização é a cada 30 minutos.
  >
  >
* **P: Se a minha conta no local está limitada por uma política de senhade diretório ativo no local, a SSPR obedece a esta política quando mudo a minha palavra-passe?**

  > **A:** Sim, a SSPR baseia-se e cumpre a política de senhas de diretório ativo no local. Esta política inclui a típica política de palavras-passe de domínio do Diretório Ativo, bem como quaisquer políticas de senha definidas e finas que sejam direcionadas para um utilizador.
  >
  >
* **P: Para que tipos de contas funciona a escrita por palavra-passe?**

  > **A:** A reescrita de palavra-passe funciona para contas de utilizador sincronizadas de diretório ativo no local para Azure AD, incluindo utilizadores federados, de hash de palavra-passe sincronizados e utilizadores de autenticação pass-through.
  >
  >
* **P: A reescrita da palavra-passe aplica as políticas de senha do meu domínio?**

  > **R:** Sim. A reversão da palavra-passe aplica a idade da palavra-passe, histórico, complexidade, filtros e qualquer outra restrição que possa colocar em palavras-passe no seu domínio local.
  >
  >
* **P: A reescrita da palavra-passe é segura?  Como posso ter a certeza que não vou ser hackeado?**

  > **A:** Sim, a redação da palavra-passe é segura. Para ler mais sobre as várias camadas de segurança implementadas pelo serviço de redação de palavras-passe, consulte a secção de segurança de recompra de [palavra-passe](concept-sspr-writeback.md#password-writeback-security) no artigo de resumo da [palavra-passe.](howto-sspr-writeback.md)
  >
  >

## <a name="next-steps"></a>Passos seguintes

* [Como posso concluir uma implementação com êxito da SSPR?](howto-sspr-deployment.md)
* [Redefinir ou alterar a sua palavra-passe](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registar-se na reposição personalizada de palavra-passe](../user-help/active-directory-passwords-reset-register.md)
* [Tem alguma pergunta sobre licenciamento?](concept-sspr-licensing.md)
* [Que dados são utilizados pela SSPR e que dados devem ser preenchidos por si para os seus utilizadores?](howto-sspr-authenticationdata.md)
* [Que métodos de autenticação estão disponíveis para os utilizadores?](concept-sspr-howitworks.md#authentication-methods)
* [Quais são as opções de política da SSPR?](concept-sspr-policy.md)
* [O que é a repetição de escrita de palavras-passe e por que me deve interessar?](howto-sspr-writeback.md)
* [Como posso comunicar a atividade da SSPR?](howto-sspr-reporting.md)
* [Quais são todas as opções na SSPR e o que significam?](concept-sspr-howitworks.md)
* [Acho que algo está partido. Como posso resolver problemas com a SSPR?](active-directory-passwords-troubleshoot.md)
