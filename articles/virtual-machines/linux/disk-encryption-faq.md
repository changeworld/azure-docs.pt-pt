---
title: FAQ - Encriptação de disco azure para VMs Linux
description: Este artigo fornece respostas a perguntas frequentes sobre encriptação do disco Microsoft Azure para VMs Linux IaaS.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: ae3743530440c9df9094a0b9784922d2d6a3dfdf
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985410"
---
# <a name="azure-disk-encryption-for-linux-virtual-machines-faq"></a>Encriptação de disco azure para máquinas virtuais Linux FAQ

Este artigo fornece respostas a perguntas frequentes (FAQ) sobre encriptação de disco azure para máquinas virtuais Linux (VMs). Para mais informações sobre este serviço, consulte a visão geral da [Encriptação do Disco Azure](disk-encryption-overview.md).

## <a name="what-is-azure-disk-encryption-for-linux-vms"></a>O que é encriptação de disco azure para VMs Linux?

A encriptação do disco Azure para VMs Linux utiliza a funcionalidade dm-criptografia do Linux para fornecer encriptação completa do disco de disco dos OS* e dos discos de dados. Além disso, fornece encriptação do disco de recursos efémeros ao utilizar a [funcionalidade EncryptFormatAll](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms). O conteúdo flui encriptado do VM para o backend do Armazenamento. Assim, fornecendo encriptação de ponta a ponta com uma chave gerida pelo cliente.
 
Ver [VMs suportados e sistemas operativos.](disk-encryption-overview.md#supported-vms-and-operating-systems)

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Onde está a encriptação do disco azure na disponibilidade geral (GA)?

A encriptação do disco azure para VMs Linux está em disponibilidade geral em todas as regiões públicas do Azure.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Que experiências de utilizador estão disponíveis com encriptação de disco azure?

A Encriptação do Disco Azure GA suporta os modelos do Gestor de Recursos Azure, O PowerShell e o Azure CLI. As diferentes experiências de utilizador dão-lhe flexibilidade. Tem três opções diferentes para permitir a encriptação do disco para os seus VMs. Para obter mais informações sobre a experiência do utilizador e orientação passo a passo disponível na Encriptação do Disco Azure, consulte os cenários de encriptação do [Disco Azure para o Linux](disk-encryption-linux.md).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Quanto custa a encriptação do disco Azure?

Não há nenhuma acusação para encriptar discos VM com encriptação de disco azure, mas há cargas associadas com o uso do Cofre de Chaves Azure. Para obter mais informações sobre os custos do Cofre de Chaves Do Azure, consulte a página de preços do [Cofre chave.](https://azure.microsoft.com/pricing/details/key-vault/)

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Como posso começar a usar encriptação de disco azure?

Para começar, leia a visão geral da [Encriptação do Disco Azure.](disk-encryption-overview.md)

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Que tamanhos vm e sistemas operativos suportam encriptação de disco azure?

O artigo de encriptação de [disco azure](disk-encryption-overview.md) lista os [tamanhos VM](disk-encryption-overview.md#supported-vms) e [sistemas operativos VM](disk-encryption-overview.md#supported-operating-systems) que suportam a encriptação do disco Azure.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Posso encriptar volumes de arranque e dados com encriptação de disco azure?

Sim, pode encriptar tanto os volumes de arranque como os volumes de dados, ou pode encriptar o volume de dados sem ter de encriptar primeiro o volume de SO. 

Depois de ter encriptado o volume de S, a encriptação incapacitante no volume de SO não é suportada. Para os VMs Linux num conjunto de escala, apenas o volume de dados pode ser encriptado.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Posso encriptar um volume não montado com encriptação de disco azure?

Não, a encriptação do disco Azure apenas encripta volumes montados.

## <a name="what-is-storage-server-side-encryption"></a>O que é encriptação do lado do servidor de armazenamento?

A encriptação do lado do servidor de armazenamento encripta os discos geridos pelo Azure no Armazenamento Azure. Os discos geridos são encriptados por padrão com encriptação do lado do Servidor com uma chave gerida pela plataforma (a partir de 10 de junho de 2017). Pode gerir a encriptação de discos geridos com as suas próprias chaves, especificando uma chave gerida pelo cliente. Para mais informações consulte: [Encriptação do lado do servidor dos discos geridos pelo Azure](disk-encryption.md).
 
## <a name="how-is-azure-disk-encryption-different-from-storage-server-side-encryption-with-customer-managed-key-and-when-should-i-use-each-solution"></a>Como é que a encriptação do disco Azure é diferente da encriptação do lado do servidor de armazenamento com a chave gerida pelo cliente e quando devo usar cada solução?

A encriptação do disco Azure fornece encriptação de ponta a ponta para o disco DE, discos de dados e o disco de recursos efémeros com uma chave gerida pelo cliente.
- Se os seus requisitos incluem encriptar toda a encriptação acima e de ponta a ponta, utilize encriptação do Disco Azure. 
- Se os seus requisitos incluem encriptar apenas dados em repouso com a chave gerida pelo cliente, em seguida, use [a encriptação do lado do Servidor com chaves geridas pelo cliente](disk-encryption.md). Não é possível encriptar um disco com encriptação de encriptação do disco Azure e encriptação do lado do servidor de armazenamento com chaves geridas pelo cliente. 
- Se o seu distro Linux não estiver listado em [sistemas operativos suportados para encriptação](disk-encryption-overview.md#supported-operating-systems) de disco azure ou estiver a utilizar um cenário chamado nos [cenários não suportados para windows](disk-encryption-linux.md#unsupported-scenarios), considere a [encriptação do lado do Servidor com chaves geridas pelo cliente](disk-encryption.md).
- Se a política da sua organização lhe permitir encriptar conteúdo em repouso com uma chave gerida pelo Azure, então não é necessária nenhuma ação - o conteúdo é encriptado por padrão. Para discos geridos, o conteúdo no interior do armazenamento é encriptado por padrão com encriptação do lado do Servidor com chave gerida pela plataforma. A chave é gerida pelo serviço de Armazenamento Azure. 



## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Como posso rodar segredos ou chaves de encriptação?

Para rodar segredos, basta ligar para o mesmo comando que usou originalmente para ativar a encriptação do disco, especificando um Cofre chave diferente. Para rodar a chave de encriptação da chave, ligue para o mesmo comando que usou originalmente para ativar a encriptação do disco, especificando a encriptação da nova chave. 

>[!WARNING]
> - Se já utilizou anteriormente a [Encriptação do Disco Azure com a aplicação Azure AD,](disk-encryption-linux-aad.md) especificando as credenciais de AD Azure para encriptar este VM, terá de continuar a utilizar esta opção para encriptar o seu VM. Não é possível utilizar a Encriptação do Disco Azure neste VM encriptado, uma vez que este não é um cenário suportado, o que significa que a mudança da aplicação AAD para este VM encriptado ainda não é suportada.

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>Como adiciono ou remove uma chave de encriptação se não usei originalmente uma?

Para adicionar uma chave de encriptação, ligue novamente para o comando de ativação, passando o parâmetro da chave de encriptação da chave. Para remover uma chave de encriptação, volte a ligar para o comando de ativação sem o parâmetro da chave de encriptação.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>A encriptação do disco Azure permite-lhe trazer a sua própria chave (BYOK)?

Sim, podefornecer as suas chaves de encriptação. Estas chaves são salvaguardadas no Cofre de Chaves Azure, que é a loja chave para encriptação de discos Azure. Para obter mais informações sobre os cenários de suporte das teclas de encriptação, consulte [Criar e configurar um cofre chave para encriptação](disk-encryption-key-vault.md)de disco azure .

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Posso usar uma chave de encriptação criada pelo Azure?

Sim, pode usar o Cofre de Chaves Azure para gerar uma chave de encriptação para o uso de encriptação do disco Azure. Estas chaves são salvaguardadas no Cofre de Chaves Azure, que é a loja chave para encriptação de discos Azure. Para obter mais informações sobre a chave de encriptação, consulte [Criar e configurar um cofre chave para encriptação](disk-encryption-key-vault.md)de disco azure .

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Posso usar um serviço de gestão de chaves no local ou HSM para salvaguardar as chaves de encriptação?

Não é possível utilizar o serviço de gestão de chaves no local ou o HSM para salvaguardar as chaves de encriptação com encriptação do disco Azure. Só pode utilizar o serviço Azure Key Vault para salvaguardar as chaves de encriptação. Para obter mais informações sobre os cenários de suporte da chave de encriptação, consulte [Criar e configurar um cofre chave para encriptação](disk-encryption-key-vault.md)de disco azure .

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Quais são os pré-requisitos para configurar a encriptação do disco Azure?

Existem pré-requisitos para encriptação do disco Azure. Consulte a [Criação e configuração](disk-encryption-key-vault.md) de um cofre chave para o artigo de encriptação do disco Azure para criar um novo cofre de chaves, ou configurar um cofre chave existente para acesso de encriptação de disco para permitir encriptação, e salvaguardar segredos e chaves. Para obter mais informações sobre os cenários de suporte da chave de encriptação, consulte [Criar e configurar um cofre chave para encriptação](disk-encryption-key-vault.md)de disco azure .

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>Quais são os pré-requisitos para configurar a Encriptação do Disco Azure com uma aplicação Azure AD (versão anterior)?

Existem pré-requisitos para encriptação do disco Azure. Consulte a Encriptação do Disco Azure com conteúdo [da AD Azure](disk-encryption-linux-aad.md) para criar uma aplicação Azure Ative Directory, criar um novo cofre chave ou criar um cofre chave existente para acesso à encriptação de discos para permitir encriptação e salvaguardar segredos e chaves. Para obter mais informações sobre os cenários de suporte da chave de encriptação, consulte [Criar e configurar um cofre chave para encriptação de disco azure com AD Azure](disk-encryption-key-vault-aad.md).

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>A Encriptação do Disco Azure está a utilizar uma aplicação Azure AD (versão anterior) ainda suportada?
Sim. A encriptação do disco utilizando uma aplicação Azure AD ainda é suportada. No entanto, ao encriptar novos VMs, recomenda-se que utilize o novo método em vez de encriptar com uma aplicação Azure AD. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Posso migrar VMs que foram encriptados com uma aplicação Azure AD para encriptar sem uma aplicação Azure AD?
  Atualmente, não existe um caminho de migração direta para máquinas que foram encriptadas com uma aplicação Azure AD para encriptar sem uma aplicação Azure AD. Além disso, não existe um caminho direto desde a encriptação sem uma aplicação Azure AD até à encriptação com uma aplicação ad. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Que versão do Azure PowerShell suporta a Encriptação do Disco Azure?

Utilize a versão mais recente do Azure PowerShell SDK para configurar a Encriptação do Disco Azure. Descarregue a versão mais recente do [Azure PowerShell.](https://github.com/Azure/azure-powershell/releases) A encriptação do disco Azure *não* é suportada pela versão 1.1.0 do Azure SDK.

> [!NOTE]
> A extensão de pré-visualização de encriptação do disco Linux Azure "Microsoft.OSTCExtension.AzureDiskCryptonForLinux" é depreciada. Esta extensão foi publicada para a versão de pré-visualização de encriptação do disco Azure. Não deve utilizar a versão de pré-visualização da extensão nos seus testes ou implantação de produção.

> Para cenários de implementação como o Azure Resource Manager (ARM), onde é necessário implementar a extensão de encriptação do disco Azure para o Linux VM para permitir a encriptação no seu VM Linux IaaS, deve utilizar a extensão suportada pela produção de encriptação do disco Azure "Microsoft.Azure.Security.AzureDiskCryptonForLinux".

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>Posso aplicar encriptação do Disco Azure na minha imagem personalizada do Linux?

Não pode aplicar encriptação de disco azure na sua imagem personalizada de Linux. Apenas as imagens linux da galeria para as distribuições apoiadas chamadas anteriormente são suportadas. As imagens personalizadas do Linux não são suportadas atualmente.

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>Posso aplicar atualizações a um VM de chapéu vermelho Linux que usa a atualização yum?

Sim, pode sacar uma atualização de yum num VM De Chapéu Vermelho.  Para mais informações, consulte a [Encriptação do Disco Azure numa rede isolada](disk-encryption-isolated-network.md).

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>Qual é o fluxo de trabalho recomendado para encriptação de disco Azure para o Linux?

Recomenda-se que o seguinte fluxo de trabalho tenha os melhores resultados no Linux:
* Comece pela imagem não modificada da galeria de stock correspondente ao distro e versão necessários
* Volte a montar quaisquer unidades montadas que sejam encriptadas.  Este backup permite a recuperação se houver uma falha, por exemplo, se o VM for reiniciado antes de a encriptação ter sido concluída.
* Encriptar (pode demorar várias horas ou mesmo dias dependendo das características vm e tamanho de quaisquer discos de dados anexados)
* Personalize e adicione software à imagem conforme necessário.

Se este fluxo de trabalho não for possível, confiar na [Encriptação](../../storage/common/storage-service-encryption.md) do Serviço de Armazenamento (SSE) na camada de conta de armazenamento da plataforma pode ser uma alternativa à encriptação completa do disco usando dm-criptografia.

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>O que é o disco "Volume Bek" ou "/mnt/azure_bek_disk"?

O "volume Bek" é um volume de dados local que armazena de forma segura as chaves de encriptação para VMs Azure encriptados.
> [!NOTE]
> Não elimine nem edite nenhum conteúdo neste disco. Não desmonte o disco uma vez que a presença da chave de encriptação é necessária para quaisquer operações de encriptação no VM IaaS.


## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Que método de encriptação utiliza a encriptação do Disco Azure?

A Encriptação do Disco Azure utiliza o padrão de desencriptação do aes-xts-plain64 com uma chave master de volume de 256 bits.

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>Se eu usar o EncryptFormatAll e especificar todos os tipos de volume, irá apagar os dados nas unidades de dados que já encriptamos?
Não, os dados não serão apagados de unidades de dados que já estão encriptadas usando encriptação de disco azure. Semelhante à forma como o EncryptFormatAll não reencripta a unidade DE, não vai voltar a encriptar a unidade de dados já encriptada. Para mais informações, consulte os [critérios do EncryptFormatAll](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms).        

## <a name="is-xfs-filesystem-supported"></a>O sistema de ficheiros XFS é suportado?
Os volumes XFS são suportados para encriptação de discos de dados apenas com o EncryptFormatAll. Isto irá reformar o volume, apagando quaisquer dados anteriormente existentes. Para mais informações, consulte os [critérios do EncryptFormatAll](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms).

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>Posso fazer backup e restaurar um VM encriptado? 

O Azure Backup fornece um mecanismo para fazer backup e restaurar vm's encriptados dentro da mesma subscrição e região.  Para obter instruções, consulte [O Backup e restaure as máquinas virtuais encriptadas com cópia](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)de segurança Azure .  Restaurar um VM encriptado para uma região diferente não é atualmente suportado.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Onde posso ir fazer perguntas ou dar feedback?

Pode fazer perguntas ou fornecer feedback sobre o fórum de encriptação do [disco Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu mais sobre as questões mais frequentes relacionadas com a Encriptação do Disco Azure. Para mais informações sobre este serviço, consulte os seguintes artigos:

- [Descrição geral do Azure Disk Encryption](disk-encryption-overview.md)
- [Aplicar encriptação de disco no Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Encriptação de dados azure em repouso](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
