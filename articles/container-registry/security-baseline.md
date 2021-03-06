---
title: Linha de base de segurança azure para registo de contentores azure
description: Linha de base de segurança azure para registo de contentores azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 9225cfd9793a84f371387d6450a3dfa80ba74de3
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547530"
---
# <a name="azure-security-baseline-for-azure-container-registry"></a>Linha de base de segurança azure para registo de contentores azure

A Linha de Base de Segurança Azure para o Registo de Contentores Azure contém recomendações que o ajudarão a melhorar a postura de segurança da sua implantação.

A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark,](https://docs.microsoft.com/azure/security/benchmarks/overview)que fornece recomendações sobre como pode garantir as suas soluções em nuvem no Azure com as nossas melhores práticas de orientação.

Para mais informações, consulte a [visão geral da Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Segurança de Rede

*Para mais informações, consulte [Security Control: Network Security](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteja os recursos utilizando grupos de segurança de rede ou firewall azure na sua Rede Virtual

**Orientação**: A Rede Virtual Azure fornece redes seguras e privadas para os seus recursos Azure e no local. Ao limitar o acesso ao seu registo de contentores Azure privado saem de uma rede virtual Azure, garante que apenas os recursos na rede virtual acedem ao registo. Para cenários transversais, também pode configurar regras de firewall para permitir o acesso ao registo apenas a partir de endereços IP específicos. Por detrás de uma firewall, configure as regras de acesso à firewall e as etiquetas de serviço para aceder ao registo do seu contentor.

Restringir o acesso a um registo de contentores Azure utilizando uma rede virtual Azure ou regras de firewall:https://docs.microsoft.com/azure/container-registry/container-registry-vnet 

Configure as regras para aceder a um registo de contentores Azure atrás de uma firewall:https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules


**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitore e registe a configuração e tráfego de Vnets, Subnets e NICs

**Orientação**: Utilize o Azure Security Center e remediar as recomendações de proteção da rede para ajudar a proteger os seus recursos de rede em Azure. Ative os registos de fluxo do NSG e envie registos numa Conta de Armazenamento para auditoria de tráfego.

Como ativar os registos de fluxo nsg:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Proteja os seus recursos de rede:https://docs.microsoft.com/azure/security-center/security-center-network-recommendations



**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação**: Não aplicável. O benchmark destina-se ao Azure App Service ou aos recursos de computação que hospedam aplicações web.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Ativar a proteção Padrão DDoS nas suas redes virtuais para proteções contra ataques DDoS. Utilize o Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP da Internet maliciosos ou não utilizados conhecidos.  Implementar o Azure Firewall em cada um dos limites de rede da organização com a Threat Intelligence habilitada e configurada para "Alertar e negar" para tráfego de rede malicioso.

Pode utilizar o azure Security Center Just In Time Network acesso para configurar NSGs para limitar a exposição de pontos finais a endereços IP aprovados por um período limitado. Além disso, utilize o Azure Security Center Adaptive Network Hardening para recomendar configurações de NSG que limitem os IPs de Portos e Fonte supor com base na inteligência real de tráfego e ameaças.

Como configurar a proteção DDoS:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Como implantar o Firewall Azure:https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Compreender o Azure Security Center Integrated Threat Intelligence:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

Compreender o Centro de Segurança Azure Adaptive Network Endureing:https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

Centro de Segurança Azure Just in Time Network Access Control:https://docs.microsoft.com/azure/security-center/security-center-just-in-time


**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Pacotes de rede de registos e registos de fluxo

**Orientação**: Ativar os registos de fluxo do grupo de segurança da rede (NSG) para o NSG ligado à subrede que está a ser utilizado para proteger o registo do seu contentor Azure. Pode gravar os registos de fluxo nsg numa Conta de Armazenamento Azure para gerar registos de fluxo. Se necessário para investigar atividades anómalas, ative a captura de pacotes do Azure Network Watcher.

Como ativar os registos de fluxo nsg:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Como ativar o Observador da Rede:https://docs.microsoft.com/azure/network-watcher/network-watcher-create


**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implantar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Selecione uma oferta do Mercado Azure que suporta a funcionalidade IDS/IPS com capacidades de inspeção de carga útil. Se a deteção e/ou prevenção de intrusões com base na inspeção da carga útil não for um requisito, o Azure Firewall com a Threat Intelligence pode ser utilizado. A filtragem baseada na inteligência da Ameaça de Firewall Azure pode alertar e negar o tráfego de e para endereços e domínios ip maliciosos conhecidos. Os endereços e domínios IP são obtidos a partir do feed da Microsoft Threat Intelligence.

Implemente a solução de firewall da sua escolha em cada um dos limites de rede da sua organização para detetar e/ou negar tráfego malicioso.

Mercado Azure:https://azuremarketplace.microsoft.com/marketplace/?term=Firewall 

Como implantar o Firewall Azure:https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Como configurar alertas com firewall Azure:https://docs.microsoft.com/azure/firewall/threat-intel


**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerir o tráfego para aplicações web

**Orientação**: Não aplicável. O benchmark destina-se a aplicações web em execução no Azure App Service ou recursos de computação.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Para recursos que necessitem de acesso ao seu registo de contentores, utilize etiquetas de serviço de rede virtuais para o serviço de registo de contentores Azure para definir controlos de acesso à rede em Grupos de Segurança da Rede ou firewall Azure. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Especificando a etiqueta de serviço "AzureContainerRegistry" no campo de origem ou destino apropriado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereço sacados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

Permitir o acesso por etiqueta de serviço:https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules#allow-access-by-service-tag


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para os recursos de rede associados aos registos de contentores Do Azure com a Política Azure. Utilize pseudónimos da Política Azure nos espaços de nome "Microsoft.ContainerRegistry" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração da rede dos seus registos de contentores. 

Pode utilizar as Plantas Azure para simplificar as implementações azure em larga escala, embalagendo artefactos ambientais chave, tais como modelos de Gestor de Recursos Azure, controlos RBAC e políticas, numa única definição de planta. Aplique facilmente o projeto a novas subscrições e afinar o controlo e a gestão através da versão.

Auditoria cumprimento dos registos de contentores Azure utilizando a Política Azure:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Como criar um Projeto Azure:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: O cliente pode utilizar as Plantas Azure para simplificar as implementações azure em larga escala através da embalagem de artefactos ambientais chave, tais como modelos de Gestor de Recursos Azure, controlos RBAC e políticas, numa única definição de planta. Aplique facilmente o projeto a novas subscrições e afinar o controlo e a gestão através da versão.

Como criar um Projeto Azure:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o Registo de Atividade seleções do Azure para monitorizar as configurações de recursos de rede e detetar alterações para os recursos de rede relacionados com os registos dos seus contentores. Crie alertas dentro do Monitor Azure que irão desencadear quando ocorrerem alterações aos recursos críticos da rede.

Como visualizar e recuperar eventos de registo de atividade sinuosa:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Como criar alertas no Monitor Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

*Para mais informações, consulte [Controlo de Segurança: Registo e Monitorização](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilize fontes de sincronização do tempo aprovadas

**Orientação**: A Microsoft mantém fontes de tempo para os recursos Do Azure, no entanto, tem a opção de gerir as definições de sincronização de tempo para os seus recursos computacionais.

Como configurar a sincronização do tempo para os recursos computacionais do Azure:https://docs.microsoft.com/azure/virtual-machines/windows/time-sync


**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central de registos de segurança

**Orientação**: Ingerir registos via Azure Monitor para agregar dados de segurança gerados por um registo de contentores Azure. Dentro do Monitor Azure, utilize log analytics workspace(s) para consultar e realizar análises, e utilize contas de armazenamento Azure para armazenamento a longo prazo/arquivo.

Registo de contentores azure para avaliação e auditoria de diagnóstico:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs



**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditoria aos recursos do Azure

**Orientação**: O Monitor Azure recolhe registos de recursos (anteriormente chamados registos de diagnóstico) para eventos orientados pelo utilizador no seu registo. Recolha e consuma estes dados para auditar eventos de autenticação de registos e fornecer um rasto de atividade completo em artefactos de registo, tais como eventos de pull e push para que possa diagnosticar problemas de segurança com o seu registo.

Registo de contentores azure para avaliação e auditoria de diagnóstico:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs


**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Recolher registos de segurança dos sistemas operativos

**Orientação**: Não aplicável. O benchmark destina-se aos recursos de computação.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de registos de segurança

**Orientação**: Dentro do Monitor Azure, detete o período de retenção do espaço de trabalho de Log Analytics de acordo com as regras de conformidade da sua organização. Utilize contas de armazenamento Azure para armazenamento a longo prazo/arquivo.

Como definir parâmetros de retenção de registos para espaços de trabalho de Log Analytics:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitore e reveja registos

**Orientação**: Analise e monitorize os registos do Registo de Contentores Azure para comportamento anómalo e reveja regularmente os resultados. Utilize o log analytics workspace do Azure Monitor para rever os registos e executar consultas nos dados de registo.

Registo de contentores azure para avaliação e auditoria de diagnóstico:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

Compreender o espaço de trabalho de Log Analytics:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Como realizar consultas personalizadas no Monitor Azure:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Ativar alertas para atividades anómalas

**Orientação**: Utilize o espaço de trabalho azure Log Analytics para monitorização e alerta sobre atividades anómalas em registos de segurança e eventos relacionados com o registo de contentores Azure.

Registo de contentores azure para avaliação e auditoria de diagnóstico:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

Como alertar sobre os dados de registo de análise de registo:https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a exploração anti-malware

**Orientação**: Não aplicável. O Registo de Contentores Azure não processa nem produz registos relacionados com anti-malware.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="29-enable-dns-query-logging"></a>2.9: Ativar a exploração de consultas dNS

**Orientação**: Não aplicável. O Registo de Contentores Azure é um ponto final e não inicia a comunicação, e o serviço não consulta o DNS.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="210-enable-command-line-audit-logging"></a>2.10: Ativar a exploração de auditoria da linha de comando

**Orientação**: Não aplicável. O benchmark destina-se aos recursos de computação.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="identity-and-access-control"></a>Identidade e Controlo de Acesso

*Para mais informações, consulte [Controlo de Segurança: Controlo de Identidade e Acesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: O Azure Ative Directory (Azure AD) tem funções incorporadas que devem ser explicitamente atribuídas e são consultadas. Utilize o módulo Azure AD PowerShell para realizar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

Para cada registo de contentores Azure, acompanhe se a conta de administração incorporada está ativada ou desativada. Desative a conta quando não estiver a ser utilizada.

Como obter um papel de diretório em Azure AD com powerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Como obter membros de um papel de diretório em Azure AD com PowerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Conta de administração do Registo de Contentores Azure:https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account


**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar as palavras-passe por defeito sempre que aplicável

**Orientação**: O Diretório Ativo Azure (Azure AD) não tem o conceito de senhas padrão. Outros recursos do Azure que exigem uma senha forçam a criação de uma palavra-passe com requisitos de complexidade e um comprimento mínimo de senha, que diferem consoante o serviço. Você é responsável por aplicações de terceiros e serviços de Marketplace que podem usar senhas padrão.

Se a conta de administração padrão de um registo de contentores Azure estiver ativada, as palavras-passe complexas são automaticamente criadas e devem ser rodadas. Desative a conta quando não estiver a ser utilizada.

Conta de administração do Registo de Contentores Azure:https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Criar procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas. Utilize a Identidade e Gestão de Acesso do Azure Security Center para monitorizar o número de contas administrativas.

Além disso, crie procedimentos que permitam a conta de administração incorporada de um registo de contentores. Desative a conta quando não estiver a ser utilizada.

Compreender identidade e acesso do Centro de Segurança Azure:https://docs.microsoft.com/azure/security-center/security-center-identity-access

Conta de administração do Registo de Contentores Azure:https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Utilize um único sinal (SSO) com diretório ativo Azure

**Orientação**: Sempre que possível, utilize o Azure Ative Directory SSO em vez de configurar credenciais individuais autónomas por serviço. Utilize recomendações de Identidade e Gestão de Acesso do Centro de Segurança Azure.

Para acesso individual ao registo de contentores, utilize login individual integrado com o Diretório Ativo Azure.

Compreenda o SSO com a Azure AD:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

Login individual para um registo de contentores:https://docs.microsoft.com/azure/container-registry/container-registry-authentication#individual-login-with-azure-ad


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilizar a autenticação multifactor para todos os acessos baseados em Diretório Ativo Azure

**Orientação**: Permitir a autenticação multifactor (Azure AD) do Diretório Ativo (Azure AD) e seguir as recomendações de Identidade e Gestão de Acesso do Azure Security Center.

Como permitir o MFA em Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Como monitorizar a identidade e o acesso dentro do Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Postos de Trabalho de Acesso Privilegiado) para todas as tarefas administrativas

**Orientação**: Utilize PAWs (estações de trabalho de acesso privilegiados) com MFA configurado para iniciar sessão e configurar os recursos Azure.

Saiba mais sobre postos de trabalho de acesso privilegiados:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Como permitir o MFA em Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


**Monitorização**do Centro de Segurança Azure : N/A

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Utilize relatórios de segurança do Azure Ative Directory (Azure AD) para a geração de registos e alertas quando ocorrer atividade suspeita ou insegura no ambiente. Utilize o Azure Security Center para monitorizar a atividade de identidade e acesso.

Como identificar utilizadores de Anúncios Azure sinalizados para atividades de risco:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Como monitorizar a identidade e a atividade de acesso dos utilizadores no Centro de Segurança Azure:https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados

**Orientação**: Utilizar locais de acesso condicional nomeados para permitir o acesso a partir de apenas agrupamentos lógicos específicos de gamas de endereços IP ou países/regiões.

Como configurar localizações nomeadas em Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations


**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Diretório Ativo Azure (Azure AD) como sistema central de autenticação e autorização. A Azure AD protege os dados utilizando encriptação forte para dados em repouso e em trânsito. A Azure AD também sais, hashes e armazena de forma segura credenciais de utilizador.

Como criar e configurar uma instância azure ad:https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Rever regularmente e conciliar o acesso ao utilizador

**Orientação**: O Azure Ative Directory (Azure AD) fornece registos para ajudar a descobrir contas velhas. Além disso, utilize as Análises de Acesso à Identidade do Azure para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de papéis. O acesso ao utilizador pode ser revisto regularmente para garantir que apenas os Utilizadores certos tenham acesso continuado.

Compreender relatórios da AD Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Como utilizar avaliações de acesso à identidade do Azure:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorização tenta aceder a contas desativadas

**Orientação**: Tem acesso a fontes de registo de registo de atividade, auditoria e registo de eventos de eventos de auditoria e risco do Azure Ative Directory (Azure AD), que lhe permitem integrar-se com qualquer ferramenta de Informação de Segurança e Gestão de Eventos (SIEM) /Ferramenta de Monitorização.

Pode simplificar este processo criando Definições de Diagnóstico para contas de utilizadores do Diretório Ativo Azure e enviando os registos de auditoria e registos de log-in para um espaço de trabalho de Log Analytics. Pode configurar os alertas desejados dentro do espaço de trabalho de Log Analytics.

Como integrar registos de atividade do Azure no Monitor Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics


**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login de conta

**Orientação**: Utilize funcionalidades de risco e proteção de identidade do Azure Ative Directory (Azure AD) para configurar respostas automatizadas a ações suspeitas detetadas relacionadas com identidades dos utilizadores. 

Como ver os sign-ins de risco da AD Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Como configurar e ativar políticas de risco de proteção de identidade:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies


**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante os cenários de suporte

**Orientação**: Não disponível; O Bloqueio do Cliente não é atualmente suportado para o Registo de Contentores Azure.

Lista de serviços suportados pelo Customer Lockbox:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de Dados

*Para mais informações, consulte [Controlo de Segurança: Proteção de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas de recursos para ajudar a rastrear os registos de contentores Azure que armazenam ou processam informações sensíveis.

Tag e versão imagens de contentores ou outros artefactos num registo, e bloquear imagens ou repositórios, para ajudar no rastreio de imagens que armazenam ou processam informações sensíveis.

Como criar e utilizar etiquetas:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Recomendações para a marcação e versão de imagens de contentores:https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version

Bloqueie uma imagem de contentor num registo de contentores Azure:https://docs.microsoft.com/azure/container-registry/container-registry-image-lock



**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou processamento de informações sensíveis

**Orientação**: Implementar registos separados de contentores, assinaturas e/ou grupos de gestão para desenvolvimento, teste e produção. Os recursos que armazenam ou processam dados sensíveis devem ser suficientemente isolados.

Os recursos devem ser separados por rede virtual ou subnet, marcados adequadamente e protegidos por um grupo de segurança de rede (NSG) ou Azure Firewall.

Como criar subscrições adicionais do Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar grupos de gestão:https://docs.microsoft.com/azure/governance/management-groups/create

Como criar e utilizar etiquetas:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Restringir o acesso a um registo de contentores Azure utilizando uma rede virtual Azure ou regras de firewall:https://docs.microsoft.com/azure/container-registry/container-registry-vnet

Como criar um NSG com um config de segurança:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Como implantar o Firewall Azure:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Como configurar alerta ou alerta e negar com firewall Azure:

https://docs.microsoft.com/azure/firewall/threat-intel



**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorizar e bloquear transferência não autorizada de informações sensíveis

**Orientação**: Implementar uma ferramenta automatizada em perímetros de rede que monitorize para transferência não autorizada de informações sensíveis e bloqueie essas transferências enquanto alerta os profissionais de segurança da informação.

Para a plataforma subjacente que é gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

Compreender a proteção de dados dos clientes em Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Partilhado

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: Certifique-se de que todos os clientes que se ligam ao registo de contentores Azure são capazes de negociar TLS 1.2 ou superior. Os recursos do Microsoft Azure negoceiam o TLS 1.2 por padrão.

Siga as recomendações do Azure Security Center para encriptação em repouso e encriptação em trânsito, quando aplicável.

Compreenda a encriptação em trânsito com o Azure:https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit



**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Partilhado

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: As características de identificação, classificação e prevenção de perdas ainda não estão disponíveis para o Registo de Contentores Azure. Implementar solução de terceiros, se necessário para efeitos de conformidade.

Para a plataforma subjacente que é gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

Compreender a proteção de dados dos clientes em Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Partilhado

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Utilizar o Azure RBAC para controlar o acesso aos recursos

**Orientação**: Utilize o Azure Ative Directory (Azure AD) RBAC para controlar o acesso a dados e recursos num registo de contentores Azure. 

Como configurar o RBAC em Azure:https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Funções e permissões do Registo de Contentores Azure:https://docs.microsoft.com/azure/container-registry/container-registry-roles



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a prevenção da perda de dados baseada no hospedeiro para impor o controlo do acesso

**Orientação**: Se necessário para o cumprimento dos recursos computacionais, implemente uma ferramenta de terceiros, como uma solução automatizada de prevenção da perda de dados baseada em hospedeiros, para impor controlos de acesso aos dados mesmo quando os dados forem copiados de um sistema.

Para a plataforma subjacente que é gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

Compreender a proteção de dados dos clientes em Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Partilhado

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Encriptar informações sensíveis em repouso

**Orientação**: Utilize a encriptação em repouso em todos os recursos do Azure. Por padrão, todos os dados de um registo de contentores Azure são encriptados em repouso utilizando chaves geridas pela Microsoft.

Compreenda a encriptação em repouso em Azure:https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest

Chaves geridas pelo cliente no Registo de Contentores Azure:https://aka.ms/acr/cmk



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: O Monitor Azure recolhe registos de recursos (anteriormente chamados registos de diagnóstico) para eventos orientados pelo utilizador no seu registo. Recolha e consuma estes dados para auditar eventos de autenticação de registos e fornecer um rasto de atividade completa em artefactos de registo, tais como eventos de pull e pull para que possa diagnosticar problemas operacionais com o seu registo.

Registo de contentores azure para avaliação e auditoria de diagnóstico:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gestão de Vulnerabilidades

*Para mais informações, consulte [Controlo de Segurança: Gestão de Vulnerabilidades](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas de digitalização automática de vulnerabilidades

**Orientação**: Siga as recomendações do Azure Security Center sobre a realização de avaliações de vulnerabilidade nas imagens do seu contentor. Implementar opcionalmente soluções de terceiros do Azure Marketplace para realizar avaliações de vulnerabilidade de imagem.

Como implementar recomendações de avaliação de vulnerabilidade do Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

Integração do Registo de Contentores Azure com Centro de Segurança (Pré-visualização):https://docs.microsoft.com/azure/security-center/azure-container-registry-integration



**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementar solução automatizada de gestão de patch do sistema operativo

**Orientação**: A Microsoft realiza a gestão de patch nos sistemas subjacentes que suportam o Registo de Contentores Azure.

Automatizar atualizações de imagem de contentores quando forem detetadas atualizações de imagens de base do sistema operativo e de outras manchas.

Sobre atualizações de imagem base para tarefas de Registo de Contentores Azure:https://docs.microsoft.com/azure/container-registry/container-registry-tasks-base-images


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Implementar solução automatizada de gestão de patch de software de terceiros

**Orientação**: Pode utilizar uma solução de terceiros para corrigir imagens de aplicação.  Além disso, pode executar tarefas do Registo de Contentores Azure para automatizar atualizações para aplicar imagens num registo de contentores com base em patches de segurança ou outras atualizações em imagens base.

Sobre atualizações de imagem base para Tarefas ACR:https://docs.microsoft.com/azure/container-registry/container-registry-tasks-base-images



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar as varreduras de vulnerabilidade consecutivas

**Orientação**: Integrar o Registo de Contentores Azure (ACR) com o Centro de Segurança Azure para permitir a digitalização periódica de imagens de contentores para vulnerabilidades. Implementar opcionalmente soluções de terceiros do Azure Marketplace para realizar análises periódicas de vulnerabilidade de imagem.

Integração do Registo de Contentores Azure com Centro de Segurança (Pré-visualização):https://docs.microsoft.com/azure/security-center/azure-container-registry-integration


**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a remediação de vulnerabilidades descobertas

**Orientação**: Integrar o Registo de Contentores Azure (ACR) com o Centro de Segurança Azure para permitir a digitalização periódica de imagens de contentores para vulnerabilidades e classificar riscos. Implementar opcionalmente soluções de terceiros do Azure Marketplace para realizar análises periódicas de vulnerabilidade de imagem e classificação de risco.

Integração do Registo de Contentores Azure com Centro de Segurança (Pré-visualização):https://docs.microsoft.com/azure/security-center/azure-container-registry-integration



**Monitorização**do Centro de Segurança Azure : N/A

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Gestão de Recursos e Inventário

*Para mais informações, consulte [Controlo de Segurança: Inventário e Gestão de Ativos.](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)*

### <a name="61-use-azure-asset-discovery"></a>6.1: Utilizar a Descoberta de Ativos Azure

**Orientação**: Utilize o Gráfico de Recursos Azure para consultar/descobrir todos os recursos (tais como computação, armazenamento, rede, portas e protocolos, etc.) dentro da sua subscrição(s).  Certifique-se de permissões (ler) adequadas no seu inquilino e enumera todas as subscrições do Azure, bem como recursos dentro das suas subscrições.

Embora os recursos clássicos do Azure possam ser descobertos através do Resource Graph, é altamente recomendado criar e utilizar recursos do Gestor de Recursos Azure para avançar.

Como criar consultas com o Gráfico de Recursos Azure:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Como visualizar as suas Assinaturas Azure:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0 

Compreender o Azure RBAC:https://docs.microsoft.com/azure/role-based-access-control/overview



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: O Registo de Contentores Azure mantém metadados incluindo etiquetas e manifestos para imagens num registo. Siga as práticas recomendadas para a marcação de artefactos.

Sobre registos, repositórios e imagens:https://docs.microsoft.com/azure/container-registry/container-registry-concepts

Recomendações para a marcação e versão de imagens de contentores:https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos Azure não autorizados

**Orientação**: O Registo de Contentores Azure mantém metadados incluindo etiquetas e manifestos para imagens num registo. Siga as práticas recomendadas para a marcação de artefactos.

Sobre registos, repositórios e imagens:https://docs.microsoft.com/azure/container-registry/container-registry-concepts

Recomendações para a marcação e versão de imagens de contentores:https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Manter um inventário dos títulos aprovados de recursos e software do Azure

**Orientação**: Terá de criar um inventário dos recursos Azure aprovados de acordo com as suas necessidades organizacionais.  

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para os recursos Azure não aprovados

**Orientação**: Utilize a Política Azure para colocar restrições ao tipo de recursos que podem ser criados na sua subscrição.

Utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro da sua subscrição.  Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados.

Auditoria cumprimento dos registos de contentores Azure utilizando a Política Azure:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Como configurar e gerir a Política Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como criar consultas com o Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro dos recursos do cálculo

**Orientação**: Analise e monitorize os registos do Registo de Contentores Azure para comportamento anómalo e reveja regularmente os resultados. Utilize o log analytics workspace do Azure Monitor para rever os registos e executar consultas nos dados de registo.

Registo de contentores azure para avaliação e auditoria de diagnóstico:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

Compreender o espaço de trabalho de Log Analytics:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Como realizar consultas personalizadas no Monitor Azure:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos não aprovados do Azure e aplicações de software

**Orientação**: A Automação Azure fornece controlo total durante a implantação, operações e desmantelamento de cargas de trabalho e recursos.  Pode implementar a sua própria solução para remover recursos Azure não autorizados. Uma introdução à Automação Azure:https://docs.microsoft.com/azure/automation/automation-intro


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="68-use-only-approved-applications"></a>6.8: Utilize apenas pedidos aprovados

**Orientação**: Não aplicável. O benchmark é projetado para os recursos de computação.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas os serviços Azure aprovados

**Orientação**: Alavancar a Política Azure para restringir quais os serviços que pode fornecer no seu ambiente.

Auditoria cumprimento dos registos de contentores Azure utilizando a Política Azure:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Como configurar e gerir a Política Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como negar um tipo específico de recurso com a Política Azure:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: Implementar lista de candidaturas aprovada

**Orientação**: Não aplicável. O benchmark é projetado para os recursos de computação.



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11: Limitar a capacidade dos utilizadores de interagir com o Gestor de Recursos AzureResources através de scripts

**Orientação**: Utilize configurações específicas do sistema operativo ou recursos de terceiros para limitar a capacidade dos utilizadores de executarscripts dentro dos recursos da computação Azure.

Como configurar o Acesso Condicional ao bloqueio de acesso ao Gestor de Recursos Azure:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitar a capacidade dos utilizadores de executar scripts dentro dos recursos computacionais

**Orientação**: Utilize configurações específicas do sistema operativo ou recursos de terceiros para limitar a capacidade dos utilizadores de executarscripts dentro dos recursos da computação Azure.

Por exemplo, como controlar a execução do script PowerShell em Ambientes Windows:https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Segregar fisicamente ou logicamente aplicações de alto risco

**Orientação**: O software necessário para operações comerciais, mas que pode incorrer em maior risco para a organização, deve ser isolado dentro da sua própria máquina virtual e/ou rede virtual e suficientemente seguro com um Azure Firewall ou um Grupo de Segurança de Rede.

Como criar uma rede virtual:https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Como criar um NSG com um config de segurança:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="secure-configuration"></a>Configuração Segura

*Para mais informações, consulte [Controlo de Segurança: Configuração segura](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos do Azure

**Orientação**: Utilize a Política Azure ou o Azure Security Center para manter as configurações de segurança de todos os Recursos Azure.

Como configurar e gerir a Política Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Auditoria cumprimento dos registos de contentores Azure utilizando a Política Azure:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operativo

**Orientação**: Utilize a recomendação do Centro de Segurança Azure "Remediar vulnerabilidades em configurações de segurança nas suas Máquinas Virtuais" para manter configurações de segurança em todos os recursos do computação.

Como monitorizar as recomendações do Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-recommendations

Como remediar as recomendações do Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação:** Utilize a política azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos Azure.

Auditoria cumprimento dos registos de contentores Azure utilizando a Política Azure:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Como configurar e gerir a Política Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Compreender os efeitos da política azure:https://docs.microsoft.com/azure/governance/policy/concepts/effects



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operativo

**Orientação**: Não aplicável. O benchmark destina-se aos recursos de computação.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Partilhado

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuração segura dos recursos Do Azure

**Orientação**: Se utilizar definições políticas personalizadas do Azure, utilize o Azure Repos para armazenar e gerir de forma segura o seu código.

Como armazenar código em Azure DevOps:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Documentação Azure Repos:https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar de forma segura imagens do sistema operativo personalizado

**Orientação**: Não aplicável. O benchmark aplica-se aos recursos de computação.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Implementar ferramentas de gestão de configuração do sistema

**Orientação**: Utilize a Política Azure para alertar, auditar e impor as configurações do sistema. Além disso, desenvolver um processo e um oleoduto para gerir as exceções políticas.

Auditoria cumprimento dos registos de contentores Azure utilizando a Política Azure:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Como configurar e gerir a Política Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração do sistema para sistemas operativos

**Orientação**: Não aplicável. O benchmark aplica-se aos recursos de computação.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementar monitorização automatizada de configuração para os serviços Do Azure

**Orientação**: Utilize o Centro de Segurança Azure para efetuar as análises de base para os seus Recursos Azure.

Utilize a Política Azure para colocar restrições ao tipo de recursos que podem ser criados na sua subscrição.

Como remediar recomendações no Centro de Segurança Azure:https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

Auditoria cumprimento dos registos de contentores Azure utilizando a Política Azure:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização automatizada de configuração para sistemas operativos

**Orientação**: Não aplicável. O benchmark aplica-se aos recursos de computação.


**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: Utilize a Identidade de Serviço Gerida em conjunto com o Cofre de Chaves Azure para simplificar e proteger a gestão secreta das suas aplicações na nuvem.

Como integrar-se com identidades geridas pelo Azure:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Como criar um Cofre chave:https://docs.microsoft.com/azure/key-vault/quick-create-portal

Como fornecer a autenticação do Cofre Chave com uma identidade gerida:https://docs.microsoft.com/azure/key-vault/managed-identity

Utilize uma identidade gerida pelo Azure nas tarefas do Registo de Contentores De Azure:https://docs.microsoft.com/azure/container-registry/container-registry-tasks-authentication-managed-identity


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: Utilize identidades geridas para fornecer aos serviços Azure uma identidade gerida automaticamente em Azure AD. Identidades Geridas permite-lhe autenticar qualquer serviço que suporte a autenticação AD Azure, incluindo o Key Vault, sem qualquer credencial no seu código.

Como configurar identidades geridas:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Utilize uma identidade gerida para autenticar um registo de contentores Azure:https://docs.microsoft.com/azure/container-registry/container-registry-authentication-managed-identity


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner Credencial para identificar credenciais dentro do código. O Credential Scanner também incentivará a mudança de credenciais descobertas para locais mais seguros, como o Cofre chave Azure.

Como configurar o Scanner Credencial:https://secdevtools.azurewebsites.net/helpcredscan.html


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra Software maligno

*Para mais informações, consulte [Security Control: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Utilize software anti-malware gerido centralmente

**Orientação**: Utilize o Microsoft Antimalware para serviços de nuvem azure e máquinas virtuais para monitorizar e defender continuamente os seus recursos. Para o Linux, utilize uma solução antimalware de terceiros.

Como configurar o Microsoft Antimalware para Serviços em Nuvem e Máquinas Virtuais:https://docs.microsoft.com/azure/security/fundamentals/antimalware


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem carregados para recursos não computacionais do Azure

**Orientação**: O Microsoft Antimalware está ativado no hospedeiro subjacente que suporta os serviços Do Azure (por exemplo, registo de contentores Azure), no entanto não funciona com conteúdo do cliente.

Pré-digitalização de quaisquer ficheiros que sejam enviados para recursos não computacionais do Azure, tais como Serviço de Aplicações, Armazenamento de Data Lake, Armazenamento blob, etc.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que software anti-malware e assinaturas são atualizados

**Orientação**: Não aplicável. O benchmark destina-se aos recursos de computação. A Microsoft lida com anti-malware para a plataforma subjacente.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="data-recovery"></a>Recuperação de Dados

*Para mais informações, consulte [Controlo de Segurança: Recuperação de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir backups automáticos regulares

**Orientação**: Os dados do registo de contentores do Microsoft Azure são sempre replicados automaticamente para garantir durabilidade e elevada disponibilidade. Registo de contentores Azure copia os seus dados para que esteja protegido contra eventos planeados e não planeados

Opcionalmente geo-replicam um registo de contentores para manter réplicas de registo em várias regiões de Azure. 

Geo-replicação no Registo de Contentores De Azure:https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Executar cópias de segurança completas do sistema e fazer cópias de segurança de quaisquer chaves geridas pelo cliente

**Orientação**: Opcionalmente, recue as imagens do contentor importando de um registo para outro.

Faça as chaves geridas pelo cliente no Cofre de Chaves Azure utilizando ferramentas de linha de comando Azure ou SDKs.

Importar imagens de contentores para um registo de contentores:https://docs.microsoft.com/azure/container-registry/container-registry-import-images

Como apoiar as chaves do cofre em Azure:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: Teste a restauração de chaves geridas por clientes no Cofre de Chaves Azure utilizando ferramentas de linha de comando Azure ou SDKs.

Como restaurar as chaves do Cofre chave Azure em Azure:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação:** Pode ativar o Soft-Delete no Cofre de Chaves Azure para proteger as chaves contra a eliminação acidental ou maliciosa.

Como ativar o Soft-Delete no Cofre de Chaves:https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a Incidentes

*Para mais informações, consulte [Controlo de Segurança: Resposta](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)a Incidentes .*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: Eleve um guia de resposta a incidentes para a sua organização. Certifique-se de que existem planos escritos de resposta a incidentes que definem todas as funções de pessoal, bem como fases de tratamento/gestão de incidentes desde a deteção até à revisão pós-incidente.

Como configurar as automatizações de fluxo de trabalho dentro do Centro de Segurança Azure:https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Orientação sobre a construção do seu próprio processo de resposta a incidentes de segurança:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomia de um incidente do Microsoft Security Response Center:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

O cliente também pode aproveitar o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes:https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança Azure atribui uma gravidade a cada alerta para o ajudar a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança do Centro de Segurança na descoberta ou na analítica usada para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque claramente as assinaturas (para ex. produção, não-prod) e criar um sistema de nomeação para identificar e categorizar claramente os recursos Azure.


**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança de teste

**Orientação**: Realizar exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular. Identifique pontos fracos e lacunas e reveja o plano conforme necessário.

Consulte a publicação do NIST: Guia para programas de teste, formação e exercício para planos e capacidades de TI:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os dados do cliente foram acedidos por uma parte ilegal ou não autorizada.  Reveja os incidentes após o facto de garantir que as questões sejam resolvidas.

Como definir o contacto de segurança do Centro de Segurança Azure:https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorpore alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a funcionalidade De Exportação Contínua. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Centro de Segurança Azure para transmitir os alertas Sentinel.

Como configurar a exportação contínua:https://docs.microsoft.com/azure/security-center/continuous-export

Como transmitir alertas para O Sentinela Azul:https://docs.microsoft.com/azure/sentinel/connect-azure-security-center


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a funcionalidade de automatização de fluxos de trabalho no Centro de Segurança Azure para ativar automaticamente respostas através de "Aplicações Lógicas" em alertas e recomendações de segurança.

Como configurar a Automatização do Fluxo de Trabalho e aplicações lógicas:https://docs.microsoft.com/azure/security-center/workflow-automation


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de Penetração e Exercícios de Red Team

*Para mais informações, consulte [Controlo de Segurança: Testes de Penetração e Exercícios de Equipa Vermelha](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todos os resultados críticos de segurança no prazo de 60 dias

**Orientação**: Siga as Regras de Envolvimento da Microsoft para garantir que os seus Testes de Penetração não violam as políticas da Microsoft:https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Pode encontrar mais informações sobre a estratégia e execução da Microsoft de Red Teaming e testes de penetração no site ao vivo contra infraestruturas, serviços e aplicações de nuvem geridas pela Microsoft, aqui:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Partilhado

## <a name="next-steps"></a>Passos seguintes

- Ver o [Benchmark de Segurança Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Saiba mais sobre [as linhas de base de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
