---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: tanmaygore
ms.openlocfilehash: 215057640dd08d9ea524d8f6b3bed8b03a8b5b8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77068422"
---
## <a name="migrate-iaas-resources-from-the-classic-deployment-model-to-azure-resource-manager"></a>Migrar recursos IaaS do modelo clássico de implantação para O Gestor de Recursos Azure
Em primeiro lugar, é importante compreender a diferença entre operações de data-plane e gestão-plane na infraestrutura como recursos de serviço (IaaS).

* O plano de *gestão/controlo* descreve as chamadas que entram no plano de gestão/controlo ou na API para modificar recursos. Por exemplo, operações como criar uma VM, reiniciar uma VM e atualizar uma rede virtual com uma nova sub-rede gerem os recursos em execução. Não afetam diretamente a ligação aos VMs.
* *O plano* de dados (aplicação) descreve o tempo de execução da própria aplicação, e envolve interação com casos que não passam pela API Azure. Por exemplo, aceder ao seu website ou retirar dados de uma instância de Servidor SQL em execução ou de um servidor MongoDB, são interações de plano de dados ou aplicações. Outros exemplos incluem copiar uma bolha de uma conta de armazenamento e aceder a um endereço IP público para utilizar o Protocolo de Ambiente de Trabalho Remoto (RDP) ou Secure Shell (SSH) na máquina virtual. Estas operações mantêm a aplicação em execução em processos, redes e armazenamento.

O plano de dados é o mesmo entre o modelo clássico de implantação e as pilhas de Gestor de Recursos. A diferença é que durante o processo de migração, a Microsoft traduz a representação dos recursos do modelo clássico de implementação para o da stack Do Gestor de Recursos. Como resultado, você precisa usar novas ferramentas, APIs e SDKs para gerir os seus recursos na stack De Recursos Manager.

![Diagrama que mostra a diferença entre plano de gestão/controlo e plano de dados](../articles/virtual-machines/media/virtual-machines-windows-migration-classic-resource-manager/data-control-plane.png)


> [!NOTE]
> Em alguns cenários de migração, a plataforma Azure para, desaloca e reinicia as suas máquinas virtuais. Isto causa um breve tempo de paragem de um avião de dados.
>

## <a name="the-migration-experience"></a>A experiência de migração
Antes de começar a migração:

* Confirme que os recursos que quer migrar não utilizam funcionalidades ou configurações não suportadas. Normalmente, a plataforma deteta estes problemas e gera um erro.
* Se tiver VMs que não estejam numa rede virtual, são parados e deallocalizados como parte da operação de preparação. Se não quiser perder o endereço IP público, considere reservar o endereço IP antes de desencadear a operação de preparação. Se os VMs estiverem numa rede virtual, não são parados e transferidos.
* Planeie a migração para ocorrer fora do horário de expediente, de modo a poder dar resposta a falhas inesperadas que possam surgir.
* Transfira a configuração atual das suas VMs com o PowerShell, os comandos da interface de linha de comandos (CLI) ou as APIs REST, de modo a facilitar a validação após a fase de preparação estar concluída.
* Atualize os scripts de automação e operacionalização para lidar com o modelo de implementação do Gestor de Recursos, antes de iniciar a migração. Opcionalmente, pode efetuar operações GET quando os recursos estão no estado pronto.
* Avalie as políticas de Controlo de Acesso Baseado em Funções (RBAC) que estão configuradas nos recursos IaaS no modelo clássico de implantação, e planeie depois da migração estar concluída.

O fluxo de trabalho migratório é o seguinte:

![Diagrama que mostra o fluxo de trabalho migratório](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-workflow.png)

> [!NOTE]
> As operações descritas nas seguintes secções são todas idempotentes. Se tiver um problema que não seja uma funcionalidade não suportada ou um erro de configuração, tente novamente a preparação, aborte ou cometa o funcionamento. Azure tenta a ação de novo.
>
>

### <a name="validate"></a>Validação
A operação de validação é o primeiro passo do processo de migração. O objetivo deste passo é analisar o estado dos recursos que pretende migrar no modelo clássico de implantação. A operação avalia se os recursos são capazes de migrar (sucesso ou fracasso).

Seleciona a rede virtual ou um serviço na nuvem (se não estiver numa rede virtual) que pretende validar para a migração. Se o recurso não for capaz de migrar, azure enumera as razões.

#### <a name="checks-not-done-in-the-validate-operation"></a>Verificações não efetuadas na operação de validação

A operação de validação apenas analisa o estado dos recursos no modelo clássico de implantação. Pode verificar todas as falhas e cenários não suportados devido a várias configurações no modelo de implementação clássico. Não é possível verificar todas as questões que a pilha de Gestor de Recursos Azure pode impor aos recursos durante a migração. Estas questões só são verificadas quando os recursos passam por transformação no próximo passo da migração (a operação de preparação). A tabela que se segue enumera todas as questões não verificadas na operação de validação:


|Verificações de rede não na operação de validação|
|-|
|Uma rede virtual com portais de entrada ER e VPN.|
|Uma ligação de gateway de rede virtual em um estado desligado.|
|Todos os circuitos er são pré-migrados para a pilha de Gestor de Recursos Azure.|
|O Gestor de Recursos Azure verifica os recursos de rede. Por exemplo: IP público estático, IPs públicos dinâmicos, equilibrador de carga, grupos de segurança de rede, tabelas de rotas e interfaces de rede. |
| Todas as regras do equilíbrio de carga são válidas em toda a implementação e na rede virtual. |
| IPs privados contraditórios entre VMs de stop-deal localizados na mesma rede virtual. |

### <a name="prepare"></a>Preparação
A operação de preparação é o segundo passo do processo de migração. O objetivo deste passo é simular a transformação dos recursos IaaS do modelo clássico de implantação para recursos do Gestor de Recursos. Além disso, a operação de preparação apresenta este lado a lado para que você possa visualizar.

> [!NOTE] 
> Os seus recursos no modelo de implantação clássico não são modificados durante este passo. É um passo seguro para correr se estiveres a tentar a migração. 

Seleciona a rede virtual ou o serviço de nuvem (se não for uma rede virtual) que pretende preparar para a migração.

* Se o recurso não for capaz de migrar, o Azure para o processo de migração e enumera a razão pela qual a operação de preparação falhou.
* Se o recurso for capaz de migrar, o Azure bloqueia as operações de gestão-avião para os recursos em migração. Por exemplo, não pode adicionar um disco de dados a uma VM em migração.

O Azure inicia então a migração de metadados do modelo clássico de implantação para O Gestor de Recursos para os recursos migratórios.

Após a operação de preparação estar concluída, tem a opção de visualizar os recursos tanto no modelo clássico de implementação como no Gestor de Recursos. A plataforma Azure cria um nome de grupo de recursos, com o padrão `cloud-service-name>-Migrated`, para cada serviço cloud no modelo de implementação clássica.

> [!NOTE]
> Não é possível selecionar o nome de um grupo de recursos criado para recursos migrados (isto é, "migrado"). Após a migração estar concluída, no entanto, você pode usar a funcionalidade de movimento do Gestor de Recursos Azure para mover recursos para qualquer grupo de recursos que você quiser. Para obter mais informações, consulte [Mover recursos para um novo grupo de recursos ou subscrição](../articles/resource-group-move-resources.md).

As duas imagens seguintes mostram o resultado após uma operação de preparação bem sucedida. O primeiro mostra um grupo de recursos que contém o serviço original de nuvem. O segundo mostra o novo grupo de recursos "migrado" que contém os recursos equivalentes do Gestor de Recursos Azure.

![Screenshot que mostra serviço original de nuvem](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-classic.png)

![Screenshot que mostra recursos do Gestor de Recursos azure na operação de preparação](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-arm.png)

Aqui está um olhar nos bastidores dos seus recursos após a conclusão da fase de preparação. Note que o recurso no plano de dados é o mesmo. Está representado tanto no plano de gestão (modelo clássico de implantação) como no plano de controlo (Gestor de Recursos).

![Diagrama da fase de preparação](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-prepare.png)

> [!NOTE]
> Os VMs que não se encontram numa rede virtual no modelo de implantação clássico são parados e deal-localizados nesta fase da migração.
>

### <a name="check-manual-or-scripted"></a>Verificação (manual ou com script)
Na etapa de verificação, tem a opção de utilizar a configuração que descarregou anteriormente para validar que a migração parece correta. Em alternativa, pode iniciar sessão no portal e verificar as propriedades e recursos para validar que a migração de metadados parece boa.

Se estiver a migrar uma rede virtual, a maioria das configurações de máquinas virtuais não são reiniciadas. Para aplicações nesses VMs, pode validar que a aplicação ainda está em execução.

Pode testar os seus scripts de monitorização e operacionais para ver se os VMs estão a funcionar como esperado e se os seus scripts atualizados funcionam corretamente. Quando os recursos estão no estado pronto, só são suportadas operações OBTER.

Não há uma janela de tempo definida antes da qual precisa de comprometer a migração. Pode permanecer neste estado o tempo que quiser. Contudo, o plano de gestão é bloqueado para estes recursos enquanto não abortar ou consolidar.

Se vir erros, pode sempre abortar a migração e regressar ao modelo de implementação clássica. Depois de voltar, o Azure abre as operações de gestão-avião sobre os recursos, para que possa retomar as operações normais nesses VMs no modelo de implantação clássico.

### <a name="abort"></a>Abortar
Este é um passo opcional se quiser reverter as suas alterações para o modelo de implementação clássico e parar a migração. Esta operação elimina os metadados do Gestor de Recursos (criados na etapa de preparação) para os seus recursos. 

![Diagrama do passo abortar](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-abort.png)


> [!NOTE]
> Esta operação não pode ser feita depois de ter desencadeado a operação de compromisso.     
>

### <a name="commit"></a>Consolidação
Depois de concluída a validação, pode consolidar a migração. Os recursos já não aparecem no modelo clássico de implementação, e estão disponíveis apenas no modelo de implementação do Gestor de Recursos. Os recursos migrados só podem ser geridos no portal novo.

> [!NOTE]
> Esta é uma operação idempotent. Se falhar, tente novamente a operação. Se continuar a falhar, crie um bilhete de suporte ou crie um fórum no [Microsoft Q&A](https://docs.microsoft.com/answers/index.html)
>
>

![Diagrama de passo de compromisso](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-commit.png)

## <a name="migration-flowchart"></a>Fluxograma de migração

Aqui está um fluxograma que mostra como proceder com a migração:

![Captura de ecrã que mostra os passos da migração](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="translation-of-the-classic-deployment-model-to-resource-manager-resources"></a>Tradução do modelo clássico de implantação para recursos do Gestor de Recursos
Pode encontrar o modelo clássico de implantação e representações do Gestor de Recursos dos recursos na tabela seguinte. Atualmente, não são suportadas outras funcionalidades e recursos.

| Representação clássica | Representação do Resource Manager | Notas |
| --- | --- | --- |
| Nome do serviço cloud |Nome DNS |Durante a migração, é criado um grupo de recursos novo para cada serviço cloud, com o padrão de nomenclatura `<cloudservicename>-migrated`. Este grupo de recursos contém todos os seus recursos. O nome do serviço cloud converte-se um nome DNS que está associado ao endereço IP público. |
| Máquina virtual |Máquina virtual |As propriedades específicas de cada VM são migradas sem alterações. Certas informações do osProfile, como o nome do computador, não estão armazenadas no modelo clássico de implementação, e permanecem vazias após a migração. |
| Recursos de disco ligados à VM |Discos implícitos ligados à VM |Os discos não são modelados como recursos de nível superior no modelo de implementação Resource Manager. São migrados como discos implícitos na VM. Atualmente, apenas os discos que estão ligados a uma VM são suportados. Os VMs do Gestor de Recursos podem agora utilizar contas de armazenamento no modelo clássico de implementação, o que permite que os discos sejam facilmente migrados sem quaisquer atualizações. |
| Extensões de VM |Extensões de VM |Todas as extensões de recursos, exceto as extensões XML, são migradas do modelo de implementação clássica. |
| Certificados de máquinas virtuais |Certificados no Azure Key Vault |Se um serviço de nuvem contiver certificados de serviço, a migração cria um novo cofre chave Azure por serviço de nuvem, e move os certificados para o cofre chave. As VMs são atualizadas, de modo a fazerem referência aos certificados no cofre de chaves. <br><br> Não apague o cofre da chave. Isto pode fazer com que o VM entre num estado falhado. |
| Configuração WinRM |Configuração WinRM em osProfile |A configuração Gestão Remota do Windows é movida sem quaisquer alterações como parte da migração. |
| Propriedade Availability-set |Recurso Availability-set | A especificação de disponibilidade é uma propriedade no VM no modelo clássico de implementação. Como parte da migração, os conjuntos de disponibilidade convertem-se num recurso de nível superior. As configurações seguintes não são suportadas: múltiplos conjuntos de disponibilidade por serviço cloud, um ou mais conjuntos de disponibilidade juntamente com VMs que não estão em nenhum conjunto de disponibilidade num serviço cloud. |
| Configuração de rede numa VM |Interface de rede primária |A configuração de rede numa VM é representada como o recurso de interface de rede primária após a migração. Nas VMs que não estão numa rede virtual, o endereço IP é alterado durante a migração. |
| Várias interfaces de rede numa VM |Interfaces de rede |Se um VM tiver múltiplas interfaces de rede associadas a ele, cada interface de rede torna-se um recurso de nível superior como parte da migração, juntamente com todas as propriedades. |
| Conjunto de pontos finais com balanceamento de carga |Load balancer |No modelo de implementação clássica, a plataforma atribuía um balanceador de carga implícito a cada serviço cloud. Durante a migração, é criado um recurso de balanceador de carga novo e o conjunto de pontos finais com balanceamento de carga converte-se em regras de balanceador de carga. |
| Regras NAT de entrada |Regras NAT de entrada |Os pontos finais de entrada definidos na VM são convertidos em regras de tradução de endereços de rede de entrada no balanceador de carga durante a migração. |
| Endereço VIP |Endereço IP público com o nome DNS |O endereço IP virtual torna-se um endereço IP público, e está associado ao equilibrista de carga. Os IPs virtuais só podem ser migrados se lhes tiverem sido atribuídos pontos finais de entrada. |
| Rede virtual |Rede virtual |A rede virtual é migrada, com todas as propriedades, para o modelo de implementação Resource Manager. É criado um grupo de recursos novo com o nome `-migrated`. |
| IPs Reservados |Endereço IP público com o método de alocação estática |Os IPs Reservados associados ao balanceador de carga são migrados, juntamente com a migração do serviço cloud ou da máquina virtual. Atualmente, não é suportada a migração de IPs reservados. |
| Endereço IP público por VM |Endereço IP público com o método de alocação dinâmica |O endereço IP público associado à VM é convertido num recurso de endereço IP público, com o método de alocação definido como estático. |
| NSGs |NSGs |Os grupos de segurança de rede associados a uma sub-rede são clonados como parte da migração para o modelo de implementação Resource Manager. O NSG no modelo de implementação clássica não é removido durante a migração. No entanto, as operações do plano de gestão para o NSG são bloqueadas enquanto a migração está em curso. |
| Servidores DNS |Servidores DNS |Os servidores DNS associados a uma rede virtual ou à VM são migrados como parte da migração de recursos correspondente, juntamente com todas as propriedades. |
| UDRs |UDRs |As rotas definidas pelo utilizador associadas a uma sub-rede são clonados como parte da migração para o modelo de implementação Resource Manager. O URD no modelo de implementação clássica não é removido durante a migração. As operações do plano de gestão para o UDR são bloqueadas enquanto a migração está em curso. |
| Propriedade de reencaminhamento de IPs na configuração de rede de uma VM |Propriedade de reencaminhamento de IPs na NIC |A propriedade de reencaminhamento de IPs numa VM é convertida numa propriedade na interface de rede durante a migração. |
| Balanceador de carga com vários IPs |Balanceador de carga com vários recursos de IP público |Todos os IP públicos associados ao equilibrador de carga são convertidos para um recurso IP público, e associados ao equilibrista de carga após a migração. |
| Nomes DNS internos na VM |Nomes DNS internos na NIC |Durante a migração, os sufixos de DNS internos das VMs são migrados para uma propriedade só de leitura com o nome “InternalDomainNameSuffix” na NIC. O sufixo permanece inalterado após a migração, e a resolução vm deve continuar a funcionar como anteriormente. |
| Gateway de rede virtual |Gateway de rede virtual |As propriedades de gateway de rede virtual são migradas inalteradas. O VIP associado ao gateway também não muda. |
| Site de rede local |Gateway de rede local |As propriedades do site da rede local são migradas inalteradas para um novo recurso chamado gateway de rede local. Isto representa prefixos de endereço no local e o IP de gateway remoto. |
| Referências de ligações |Ligação |As referências de conectividade entre o gateway e o site de rede local na configuração da rede são representadas por um novo recurso chamado Connection. Todas as propriedades de referência de conectividade nos ficheiros de configuração da rede são copiadas inalteradas para o recurso De ligação. A conectividade entre redes virtuais no modelo clássico de implantação é alcançada através da criação de dois túneis IPsec para sites de rede locais que representam as redes virtuais. Isto é transformado para o tipo de ligação rede virtual-rede-rede no modelo Desativação, sem necessitar de gateways de rede locais. |

## <a name="changes-to-your-automation-and-tooling-after-migration"></a>Alterações à automatização e às ferramentas após a migração
Como parte da migração dos seus recursos do modelo clássico de implementação para o modelo de implementação do Gestor de Recursos, deve atualizar a sua automatização ou ferramenta existente para garantir que continua a funcionar após a migração.
