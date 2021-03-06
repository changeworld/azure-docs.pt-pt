---
title: Introdução ao Armazenamento do Azure - Armazenamento da cloud no Azure | Microsoft Docs
description: A plataforma core Azure Storage é a solução de armazenamento em nuvem da Microsoft. O Armazenamento do Azure oferece armazenamento de objetos de dados altamente disponível, seguro, durável, extremamente dimensionável e redundante.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 1cc047ee60cf8287f32a42b878371c5fc9680b7a
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985750"
---
# <a name="introduction-to-the-core-azure-storage-services"></a>Introdução aos principais serviços de armazenamento azure

A plataforma De armazenamento Azure é a solução de armazenamento em nuvem da Microsoft para cenários modernos de armazenamento de dados. Os serviços de armazenamento core oferecem uma loja de objetos massivamente escalável para objetos de dados, armazenamento em disco para máquinas virtuais Azure (VMs), um serviço de sistema de ficheiros para a nuvem, uma loja de mensagens para mensagens fiáveis e uma loja NoSQL. Os serviços são:

- **Durável e de elevada disponibilidade.** A redundância garante que os dados estão seguros em caso de falhas de hardware transitórias. Também pode optar por replicar dados em centros de dados ou regiões geográficas para obter proteção adicional contra catástrofes locais ou desastres naturais. Os dados replicados desta forma permanecem altamente disponíveis no caso de uma falha inesperada.
- **Seguro, seguro.** Todos os dados escritos numa conta de armazenamento Azure são encriptados pelo serviço. O Armazenamento do Azure oferece-lhe controlo detalhado sobre quem tem acesso aos seus dados.
- **Escalável.** O Armazenamento do Azure foi criado para ser extremamente dimensionável para satisfazer as necessidades de armazenamento e desempenho de dados das aplicações atuais.
- **Gerido.** O Azure lida com a manutenção de hardware, atualizações e questões críticas para si.
- **Acessível.** Os dados no Armazenamento do Azure são acessíveis a partir de qualquer local no mundo através de HTTP ou HTTPS. A Microsoft fornece bibliotecas de clientes para o Armazenamento Azure em uma variedade de idiomas, incluindo .NET, Java, Node.js, Python, PHP, Ruby, Go, entre outros, bem como uma API REST madura. O Armazenamento do Azure suporta scripting no Azure PowerShell ou na CLI do Azure. E o portal do Azure e o Explorador de Armazenamento do Azure oferecem soluções visuais simples para trabalhar com os seus dados.  

## <a name="core-storage-services"></a>Serviços de armazenamento de núcleo

A plataforma de armazenamento Azure inclui os seguintes serviços de dados:

- [Blobs do Azure](../blobs/storage-blobs-introduction.md): um arquivo de objetos extremamente dimensionável para texto e dados binários. Também inclui suporte para análise de big data através do Data Lake Storage Gen2.
- [Ficheiros do Azure](../files/storage-files-introduction.md): partilhas de ficheiros geridos para a cloud ou implementações locais.
- [Filas do Azure](../queues/storage-queues-introduction.md): arquivo de mensagens para mensagens fiáveis entre componentes da aplicação.
- [Tabelas do Azure](../tables/table-storage-overview.md): um arquivo do NoSQL para armazenamento sem esquemas de dados estruturados.
- [Discos Azure](../../virtual-machines/windows/managed-disks-overview.md): Volumes de armazenamento ao nível do bloco para VMs Azure.

Cada serviço é acedido através de uma conta de armazenamento. Para começar a utilizar, veja [Criar uma conta de armazenamento](storage-account-create.md).

## <a name="example-scenarios"></a>Cenários de exemplo

A tabela seguinte compara Ficheiros, Blobs, Discos, Filas e Tabelas, e mostra cenários de exemplo para cada um.

| Funcionalidade | Descrição | Quando utilizar |
|--------------|-------------|-------------|
| **Ficheiros do Azure** |Oferece partilhas de ficheiros em nuvem totalmente geridas a que pode aceder a partir de qualquer lugar através do protocolo padrão do Bloco de Mensagens do Servidor (SMB) da indústria.<br><br>Pode montar partilhas de ficheiros Azure a partir de implementações de cloud ou no local de Windows, Linux e macOS. | Pretende "levantar e deslocar" uma aplicação para a nuvem que já utiliza o sistema de ficheiros nativo APIs para partilhar dados entre ele e outras aplicações em funcionamento no Azure.<br/><br/>Pretende substituir ou complementar servidores de ficheiros no local ou dispositivos NAS.<br><br> Você quer armazenar ferramentas de desenvolvimento e depuração que precisam ser acedidas a partir de muitas máquinas virtuais. |
| **Blobs do Azure** | Permite que os dados não estruturados sejam armazenados e acedidos a uma escala massiva em blocos de bolhas.<br/><br/>Também suporta [o Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md) para soluções de análise de big data da empresa. | Deseja que a sua aplicação suporte o streaming e os cenários de acesso aleatório.<br/><br/>Pretende aceder aos dados da aplicação a partir de qualquer lugar.<br/><br/>Você quer construir um lago de dados da empresa em Azure e realizar análise de big data. |
| **Discos do Azure** | Permite que os dados sejam armazenados e acedidos persistentemente a partir de um disco rígido virtual anexo. | Pretende "levantar e mudar" aplicações que utilizam APIs do sistema de ficheiros nativopara ler e escrever dados para discos persistentes.<br/><br/>Pretende armazenar dados que não são necessários para serem acedidos de fora da máquina virtual a que o disco está ligado. |
| **Filas do Azure** | Permite a fila de mensagens assíncronas entre componentes de aplicação. | Pretende dissociar componentes de aplicações e utilizar mensagens assíncronas para comunicar entre eles.<br><br>Para obter orientações sobre quando utilizar o armazenamento de fila versus filas de ônibus de serviço, consulte as filas de [armazenamento e as filas de ônibus de serviço - em comparação e contraste](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted). |
| **Tabelas do Azure** | Permitir-lhe armazenar dados noSQL estruturados na nuvem, fornecendo uma loja chave/atributo com um design sem esquemas. | Pretende armazenar conjuntos de dados flexíveis, como dados de utilizadores para aplicações web, livros de endereços, informações do dispositivo ou outros tipos de metadados que o seu serviço necessita. <br/><br/>Para obter orientações sobre quando utilizar o armazenamento de mesa Azure Cosmos DB API, consulte [Desenvolvimento com API de Mesa Db Azure Cosmos e armazenamento de mesa azure.](../../cosmos-db/table-support.md) |

## <a name="blob-storage"></a>Armazenamento de blobs

O Armazenamento de Blobs do Azure é a solução de armazenamento de objetos da Microsoft para a cloud. O Armazenamento de blobs está otimizado para armazenar grandes quantidades de dados não estruturados, como dados de texto ou binários.

O armazenamento de blobs é ideal para:

- Entrega de imagens ou documentos diretamente a um browser.
- Armazenamento de ficheiros para acesso distribuído.
- Transmissão de áudio e vídeo.
- Armazenamento de dados de cópia de segurança e restauro, recuperação após desastre e arquivo.
- Armazenamento de dados para análise por um serviço no local ou alojado no Azure.

Os Objetos no Armazenamento de blobs podem ser acedidos em qualquer local no mundo através de HTTP ou HTTPS. Os utilizadores ou aplicações de cliente podem aceder a blobs através de URLs, à [API REST do Armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), ao [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), à [CLI do Azure](https://docs.microsoft.com/cli/azure/storage) ou a uma biblioteca de cliente do Armazenamento do Azure. As bibliotecas de clientes de armazenamento estão disponíveis para muitas linguagens, incluindo [.NET](/dotnet/api/overview/azure/storage?view=azure-dotnet), [Java](https://docs.microsoft.com/java/api/overview/azure/storage), [Node.js](https://azure.github.io/azure-storage-node), [Python](https://azure-storage.readthedocs.io/), [PHP](https://azure.github.io/azure-storage-php/) e [Ruby](https://azure.github.io/azure-storage-ruby).

Para mais informações sobre o armazenamento blob, consulte [Introdução ao armazenamento blob](../blobs/storage-blobs-introduction.md).

## <a name="azure-files"></a>Ficheiros do Azure

O serviço [Ficheiros do Azure](../files/storage-files-introduction.md) permite configurar partilhas de ficheiros de rede de elevada disponibilidade que podem ser acedidas através do protocolo SMB (Server Message Block) padrão. Isto significa que múltiplas VMs podem partilhar os mesmos ficheiros com acesso de leitura e de escrita. Também pode ler os ficheiros através da interface REST ou das bibliotecas de cliente de armazenamento.

A única coisa que distingue os Ficheiros do Azure dos ficheiros numa partilha de ficheiros empresarial é o facto de o utilizador poder aceder aos ficheiros a partir de qualquer parte do mundo através de um URL que aponta para o ficheiro e inclui um token de assinatura de acesso partilhado (SAS). Pode gerar tokens SAS; estes permitem o acesso específico a um recurso privado durante um período de tempo específico.

As partilhas de ficheiros podem ser utilizadas para inúmeros cenários comuns:

- Muitas aplicações no local utilizam partilhas de ficheiros. Esta funcionalidade facilita a migração dessas aplicações que partilham dados no Azure. Se montar a partilha de ficheiros na mesma letra de unidade utilizada pela aplicação no local, a parte da sua aplicação que acede à partilha de ficheiros deve funcionar com alterações mínimas, se existirem.

- Os ficheiros de configuração podem ser armazenados numa partilha de ficheiros e acedidos a partir de múltiplas VMs. As ferramentas e os utilitários utilizados pelos múltiplos programadores num grupo podem ser armazenados numa partilha de ficheiros, o que garante que todos podem encontrá-los e que utilizam a mesma versão.

- Os registos de diagnóstico, métricas e informações de falha são apenas três exemplos de dados que podem ser escritos numa partilha de ficheiros e processados ou analisados mais tarde.

Para obter mais informações sobre o serviço Ficheiros do Azure, veja [Introdução ao serviço Ficheiros do Azure](../files/storage-files-introduction.md).

Algumas funcionalidades SMB não são aplicáveis à nuvem. Para mais informações, consulte [Funcionalidades não suportadas pelo serviço De Ficheiros Azure](/rest/api/storageservices/features-not-supported-by-the-azure-file-service).

## <a name="queue-storage"></a>Armazenamento de filas

O serviço Filas do Azure é utilizado para armazenar e obter mensagens. As mensagens das filas podem ter até 64 KB de tamanho, ao passo que as filas podem conter milhões de mensagens. Geralmente, as filas são utilizadas para armazenar listas de mensagens que vão ser processadas de forma assíncrona.

Por exemplo, imaginemos que quer dar aos seus clientes a capacidade de carregar imagens e que quer igualmente criar miniaturas de cada uma delas. Pode fazer com que os clientes esperem até criar as miniaturas enquanto carregam as imagens. Uma alternativa é utilizar uma fila. Quando o cliente terminar o upload, escreva uma mensagem para a fila. Em seguida, crie um função das Função do Azure para obter a mensagem da fila e criar as miniaturas. Cada uma das partes envolvidas neste processamento pode ser dimensionada à parte, dando-lhe mais controlo para a otimizar para a sua utilização.

Para obter mais informações sobre o serviço Filas do Azure, veja [Introdução ao serviço Filas do Azure](../queues/storage-queues-introduction.md).

## <a name="table-storage"></a>Table Storage

O armazenamento de Tabelas do Azure faz agora parte do Azure Cosmos DB. Para ver a documentação do armazenamento de Tabelas do Azure, veja a [Descrição Geral do Armazenamento de Tabelas do Azure](../tables/table-storage-overview.md). Para além do serviço de armazenamento de Tabelas do Azure já existente, há uma nova oferta de API de Tabela do Azure Cosmos DB que disponibiliza tabelas otimizadas para débito, distribuição global e índices secundários automáticos. Para saber mais e experimentar a nova experiência premium, consulte [a Azure Cosmos DB Table API](https://aka.ms/premiumtables).

Para obter mais informações sobre o Armazenamento de tabelas, veja [Descrição geral do Armazenamento de tabelas do Azure](../tables/table-storage-overview.md).

## <a name="disk-storage"></a>Armazenamento em disco

Um disco gerido pelo Azure é um disco rígido virtual (VHD). Pode pensar nisso como um disco físico num servidor no local, mas virtualizado. Os discos geridos pelo Azure são armazenados como bolhas de página, que são um objeto de armazenamento IO aleatório em Azure. Chamamos um disco gerido 'gerido' porque é uma abstração sobre bolhas de página, recipientes de blob e contas de armazenamento Azure. Com discos geridos, tudo o que tem que fazer é fornecer o disco, e Azure cuida do resto.

Para obter mais informações sobre discos geridos, consulte [Introdução aos discos geridos](../../virtual-machines/windows/managed-disks-overview.md)pelo Azure .

## <a name="types-of-storage-accounts"></a>Tipos de contas de armazenamento

O Azure Storage oferece vários tipos de contas de armazenamento. Cada tipo suporta diferentes funcionalidades e tem o seu próprio modelo de preços. Para obter mais informações sobre os tipos de conta de armazenamento, consulte a visão geral da conta de [armazenamento do Azure.](storage-account-overview.md)

## <a name="secure-access-to-storage-accounts"></a>Acesso seguro a contas de armazenamento

Todos os pedidos ao Armazenamento Azure devem ser autorizados. O Armazenamento Azure suporta os seguintes métodos de autorização:

- **Integração do Azure Ative Directory (Azure AD) para dados de blob e fila.** O Azure Storage suporta a autenticação e autorização com a Azure AD para os serviços Blob e Queue através do controlo de acesso baseado em funções (RBAC). A autorização de pedidos com a AD Azuré é recomendada para uma segurança superior e facilidade de utilização. Para mais informações, consulte [Autorizar o acesso a blobs e filas Azure utilizando o Diretório Ativo Azure](storage-auth-aad.md).
- **Autorização da Azure AD sobre sMB para Ficheiros Azure.** O Azure Files suporta a autorização baseada na identidade sobre o SMB (Bloco de Mensagens de Servidor) através dos Serviços de Domínio de Diretório Ativo azure (Azure AD DS) ou no local, serviços de domínio de diretório ativo (pré-visualização). Os VMs do Windows, filiados no domínio, podem aceder a partilhas de ficheiros Azure utilizando credenciais de AD Azure. Para mais informações, consulte o suporte de [autenticação baseado na identidade do Azure Files para acesso](../files/storage-files-active-directory-overview.md) e [planeamento de SMB para uma implementação de Ficheiros Azure](../files/storage-files-planning.md#identity).
- **Autorização com Chave Partilhada.** A Blob de Armazenamento Azure, Ficheiros, Fila e Serviços de Mesa suportam autorização com chave partilhada. Um cliente que usa a autorização Shared Key passa um cabeçalho com cada pedido assinado usando a chave de acesso à conta de armazenamento. Para mais informações, consulte [Autorizar com chave partilhada](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key).
- **Autorização utilizando assinaturas de acesso partilhado (SAS).** Uma assinatura de acesso partilhado (SAS) é uma cadeia que contém um símbolo de segurança que pode ser anexado ao URI para um recurso de armazenamento. O símbolo de segurança engloba constrangimentos tais como permissões e o intervalo de acesso. Para mais informações, consulte A Utilização de Assinaturas de [Acesso Partilhado (SAS)](storage-sas-overview.md).
- **Acesso anónimo a contentores e bolhas.** Um recipiente e as suas bolhas podem estar disponíveis ao público. Quando especificar que um recipiente ou bolha é público, qualquer pessoa pode lê-lo anonimamente; não é necessária autenticação. Para obter mais informações, veja [Manage anonymous read access to containers and blobs](../blobs/storage-manage-access-to-resources.md) (Gerir o acesso de leitura anónima a contentores e blobs).

## <a name="encryption"></a>Encriptação

Existem dois tipos básicos de encriptação disponíveis para os principais serviços de armazenamento. Para obter mais informações sobre segurança e encriptação, veja o [Guia de segurança do Armazenamento do Azure](../blobs/security-recommendations.md).

### <a name="encryption-at-rest"></a>Encriptação inativa

A encriptação Azure Storage protege e salvaguarda os seus dados para cumprir os seus compromissos de segurança organizacional e conformidade. O Azure Storage encripta automaticamente todos os dados antes de persistir na conta de armazenamento e desencripta-os antes da recuperação. Os processos de encriptação, desencriptação e gestão chave são transparentes para os utilizadores. Os clientes também podem optar por gerir as suas próprias chaves utilizando o Cofre de Chaves Azure. Para mais informações, consulte [a encriptação do Armazenamento Azure para obter dados em repouso](storage-service-encryption.md).

### <a name="client-side-encryption"></a>Encriptação do lado do cliente

As bibliotecas de clientes do Azure Storage fornecem métodos para encriptar dados da biblioteca do cliente antes de enviá-los através do fio e desencriptar a resposta. Os dados encriptados através da encriptação do lado do cliente também são encriptados em repouso pelo Armazenamento Azure. Para obter mais informações sobre encriptação do lado do cliente, consulte [a encriptação do lado do Cliente com .NET para Armazenamento Azure](storage-client-side-encryption.md).

## <a name="redundancy"></a>Redundância

Para garantir que os seus dados são duráveis, o Azure Storage armazena várias cópias dos seus dados. Quando configurar a sua conta de armazenamento, selecione uma opção de redundância. Para obter mais informações, veja [Redundância do Armazenamento do Microsoft Azure](/storage-redundancy?toc=/azure/storage/blobs/toc.json).

## <a name="transfer-data-to-and-from-azure-storage"></a>Transferir dados de e para o Armazenamento Azure

Tem várias opções para mover dados para dentro ou para fora do Armazenamento Azure. A opção que escolher depende do tamanho do seu conjunto de dados e da largura de banda da rede. Para mais informações, consulte [Escolha uma solução Azure para transferência de dados](storage-choose-data-transfer-solution.md).

## <a name="pricing"></a>Preços

Ao tomar decisões sobre como os seus dados são armazenados e acedidos, deve também considerar os custos envolvidos. Para mais informações, consulte o preço do [Armazenamento Azure.](https://azure.microsoft.com/pricing/details/storage/)

## <a name="storage-apis-libraries-and-tools"></a>APIs de armazenamento, bibliotecas e ferramentas

Pode aceder a recursos numa conta de armazenamento por qualquer idioma que possa fazer pedidos HTTP/HTTPS. Além disso, os principais serviços de Armazenamento Azure oferecem bibliotecas de programação para várias línguas populares. Estas bibliotecas simplificam muitos aspetos do trabalho com o Storage do Azure ao processar detalhes como a invocação síncrona e assíncrona, a criação de batches de operações, a gestão de exceções, as tentativas automáticas, o comportamento operacional, etc. Atualmente, as bibliotecas estão disponíveis para os seguintes idiomas e plataformas, com outros no pipeline:

### <a name="azure-storage-data-api-and-library-references"></a>API de dados do Armazenamento do Azure e referências da biblioteca

- [Azure Storage REST API](https://docs.microsoft.com/rest/api/storageservices/) (API REST do Armazenamento do Azure)
- [Biblioteca de clientes azure storage para .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
- [Biblioteca de clientes Azure Storage para Java/Android](https://docs.microsoft.com/java/api/overview/azure/storage)
- [Biblioteca de clientes Azure Storage para Node.js](https://docs.microsoft.com/javascript/api/overview/azure/storage-overview)
- [Biblioteca de clientes azure storage para Python](https://github.com/Azure/azure-storage-python)
- [Biblioteca de clientes azure storage para PHP](https://github.com/Azure/azure-storage-php)
- [Biblioteca de clientes azure storage para Ruby](https://github.com/Azure/azure-storage-ruby)
- [Biblioteca de clientes Azure Storage para C++](https://github.com/Azure/azure-storage-cpp)

### <a name="azure-storage-management-api-and-library-references"></a>API de gestão do Armazenamento do Azure e referências da biblioteca

- [API REST do Fornecedor de Recursos de Armazenamento](https://docs.microsoft.com/rest/api/storagerp/)
- [Biblioteca de Clientes do Fornecedor de Recursos do Storage para o .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/management)
- [API REST da Gestão de Serviços do Storage (Clássica)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### <a name="azure-storage-data-movement-api-and-library-references"></a>API de movimentação do Armazenamento do Azure e referências da biblioteca

- [API REST do Serviço de Importação/Exportação do Storage](https://docs.microsoft.com/rest/api/storageimportexport/)
- [Biblioteca de Clientes do Movimento de Dados do Storage para o .NET](/dotnet/api/microsoft.azure.storage.datamovement)

### <a name="tools-and-utilities"></a>Ferramentas e utilitários

- [Cmdlets do Azure PowerShell para Armazenamento](https://docs.microsoft.com/powershell/module/az.storage)
- [Cmdlets da CLI do Azure para Armazenamento](https://docs.microsoft.com/cli/azure/storage)
- [Utilitário da Linha de Comandos do AzCopy](https://aka.ms/downloadazcopy)
- O [Explorador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) é uma aplicação autónoma e gratuita da Microsoft, que lhe permite trabalhar visualmente com dados do Armazenamento do Azure no Windows, macOS e Linux.
- [Ferramentas de cliente de armazenamento azure](../storage-explorers.md)
- [Ferramentas de Programação do Azure](https://azure.microsoft.com/tools/)

## <a name="next-steps"></a>Passos seguintes

Para se levantar e funcionar com os principais serviços de Armazenamento Azure, consulte [Criar uma conta](storage-account-create.md)de armazenamento .
