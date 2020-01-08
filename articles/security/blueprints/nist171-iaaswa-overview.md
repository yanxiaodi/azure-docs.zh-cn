---
title: Azure 安全性和符合性蓝图 - 符合 NIST SP 800-171 的 IaaS Web 应用程序
description: Azure 安全性和符合性蓝图 - 符合 NIST SP 800-171 的 IaaS Web 应用程序
services: security
author: jomolesk
ms.assetid: 1f1ad27f-32c3-4e76-abb9-ea768d01747f
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: 83d368e419550f38c173a7a1dca42c84db7d542f
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71259839"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-nist-sp-800-171"></a>Azure 安全性和符合性蓝图 - 符合 NIST SP 800-171 的 IaaS Web 应用程序

## <a name="overview"></a>概述
[NIST Special Publication 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) 提供有关保护驻留在非联邦信息系统和组织中的受控未分类信息 (CUI) 的指导原则。 NIST SP 800-171 为保护 CUI 机密性建立了 14 个系列的安全要求。

本 Azure 安全性和符合性蓝图提供的指导可帮助客户在 Azure 中部署 Web 应用程序体系结构，用于实施一部分 NIST SP 800-171 控制措施。 该解决方案展示了客户可以满足特定安全性和符合性要求的方式。 此外，它还是客户在 Azure 中构建和配置其 Web 应用程序的基础。

此参考体系结构、相关实施指南和威胁模型的主要宗旨是为客户适应其特定要求奠定基础， 请不要在生产环境中原样照搬。 在不经修改的情况下部署此体系结构并不足以完全满足 NIST SP 800-171 的要求。 客户负责对任何使用此体系结构构建的解决方案进行适当的安全性和符合性评估。 要求可能因每个客户的具体实施方案而异。

## <a name="architecture-diagram-and-components"></a>体系结构示意图和组件
此 Azure 安全性与合规性蓝图可针对具有 SQL Server 后端的 IaaS Web 应用程序部署一个参考体系结构。 体系结构包括 Web 层、数据层、Active Directory 基础结构、Azure 应用程序网关和 Azure 负载均衡器。 在可用性集中配置部署到 Web 层和数据层的虚拟机 (VM)。 在 Always On 可用性组中配置 SQL Server 实例以实现高可用性。 VM 已加入域。 Active Directory 组策略在操作系统级别强制实施安全性和符合性配置。

整个解决方案基于客户从 Azure 门户配置的 Azure 存储构建。 存储通过“存储服务加密”加密所有数据，以保持静态数据的机密性。 异地冗余存储可确保客户主数据中心的负面事件不会导致数据丢失。 第二个副本存储在数百英里以外的独立位置。

为了增强安全性，将通过 Azure 资源管理器以资源组的形式管理此解决方案中的所有资源。 Azure Active Directory (Azure AD) 基于角色的访问控制 (RBAC) 用于控制对已部署资源和 Azure Key Vault 中的密钥的访问。 系统运行状况通过 Azure Monitor 进行监视。 客户可将这两个监视服务均配置为捕获日志。 系统运行状况在一个仪表板中显示，方便使用。

可以通过管理守护主机进行安全的连接，以便管理员访问部署的资源。 Microsoft 建议配置 VPN 或 Azure ExpressRoute 连接，从而实现管理并将数据导入参考体系结构子网。


![符合 NIST SP 800-171 参考体系结构的 IaaS Web 应用程序图示](images/nist171-iaaswa-architecture.png "符合 NIST SP 800-171 参考体系结构的 IaaS Web 应用程序图示")

此解决方案使用以下 Azure 服务。 有关详细信息，请参阅[部署体系结构](#deployment-architecture)部分。

- Azure 虚拟机
    - (1) 管理/守护 (Windows Server 2016 Datacenter)
    - (2) Active Directory 域控制器 (Windows Server 2016 Datacenter)
    - (2) SQL Server 群集节点（Windows Server 2016 上的 SQL Server 2017）
    - (2) Web/IIS (Windows Server 2016 Datacenter)
- Azure 虚拟网络
    - (1) /16 网络
    - (5) /24 网络
    - (5) 网络安全组
- 可用性集
    - (1) Active Directory 域控制器
    - (1) SQL 群集节点
    - (1) Web/IIS
- Azure 应用程序网关
    - (1) Web 应用程序防火墙
        - 防火墙模式：防护
        - 规则集：OWASP 3.0
        - 侦听器端口：443
- Azure Active Directory
- Azure Key Vault
- Azure 负载均衡器
- Azure Monitor （日志）
- Azure 资源管理器
- Azure 安全中心
- Azure 存储
- Azure 自动化
- 云见证
- 恢复服务保存库

## <a name="deployment-architecture"></a>部署体系结构
以下部分详细描述了部署和实施要素。

**守护主机**：堡垒主机是用户可用于访问此环境中部署的资源的单一入口点。 守护主机通过仅允许来自安全列表中公共 IP 地址的远程流量来提供到已部署资源的安全连接。 若要允许远程桌面流量，必须在网络安全组 (NSG) 中定义流量的源。

此解决方案使用以下配置将 VM 创建为已加入域的守护主机：
-   [反恶意软件扩展](https://docs.microsoft.com/azure/security/fundamentals/antimalware)。
-   [Azure 诊断扩展](../../virtual-machines/windows/extensions-diagnostics-template.md)。
-   使用 Key Vault 的 [Azure 磁盘加密](../azure-security-disk-encryption-overview.md)。
-   [自动关闭策略](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)，在不使用 VM 时可减少其资源消耗量。
-   已启用 [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard)，以便凭据和其他机密在与运行操作系统相隔离的受保护环境中运行。

### <a name="virtual-network"></a>虚拟网络
体系结构定义了一个地址空间为 10.200.0.0/16 的专用虚拟网络。

**网络安全组**：此解决方案在具有单独子网的体系结构中部署资源，在虚拟网络中进行 web、数据库、Active Directory 和管理。 这些子网在逻辑上通过应用于各个子网的 NSG 规则分开。 这些规则会限制子网间的流量，仅限于系统和管理功能所必需的流量。

请参阅使用此解决方案部署的 [NSG](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) 的配置。 组织可以根据[此文档](../../virtual-network/virtual-network-vnet-plan-design-arm.md)中的说明编辑之前的文件，以对 NSG 进行配置。

每个子网有一个专用的 NSG：
- 一个 NSG 用于应用程序网关 (LBNSG)
- 一个 NSG 用于守护主机 (MGTNSG)
- 一个 NSG 用于主要和备份域控制器 (ADNSG)
- 一个 NSG 用于 SQL Server 和云见证 (SQLNSG)
- 一 NSG 用于 Web 层 (WEBNSG)

### <a name="data-in-transit"></a>传输中的数据
默认情况下，Azure 会加密与 Azure 数据中心之间的所有通信。 此外，所有通过 Azure 门户进行存储的事务均通过 HTTPS 传输。

### <a name="data-at-rest"></a>静态数据
体系结构通过多种措施值保护静态数据。 这些措施包括加密和数据库审核。

**Azure 存储**：为了满足静态数据加密要求，所有[存储](https://azure.microsoft.com/services/storage/)均使用[存储服务加密](../../storage/common/storage-service-encryption.md)。 此功能有助于保护数据，以支持 NIST SP 800-171 定义的组织安全承诺和符合性要求。

**Azure 磁盘加密**：磁盘加密用于加密 Windows IaaS VM 磁盘。 [磁盘加密](../azure-security-disk-encryption-overview.md)使用 Windows 的 BitLocker 功能，为操作系统和数据磁盘提供卷加密。 此解决方案与密钥保管库集成，用于控制和管理磁盘加密密钥。

**SQL Server**：SQL Server 实例使用以下数据库安全措施：
-   [SQL Server 审核](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-2017)跟踪数据库事件并将其写入到审核日志。
-   [透明数据加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017)对数据库、相关备份和事务日志文件进行实时加密和解密，以保护静态信息。 透明数据加密可确保存储的数据免遭他人未经授权的访问。
-   在授予相应的权限前，[防火墙规则](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)会阻止对数据库服务器的所有访问。 防火墙基于每个请求的起始 IP 地址授予数据库访问权限。
-   [加密列](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-wizard?view=sql-server-2017)可以确保敏感数据永远不会在数据库系统中以明文形式显示。 启用数据加密后，只有具有密钥访问权限的客户端应用程序或应用程序服务器才能访问明文数据。
- [动态数据掩码](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?view=sql-server-2017)通过对非特权用户或应用程序模糊化敏感数据来限制此类数据的泄漏。 它可以自动发现潜在的敏感数据，并建议应用合适的掩码。 动态数据掩码有助于减少访问，以便敏感数据不会由未经授权访问而退出数据库。 客户需要负责根据其数据库架构调整设置。

### <a name="identity-management"></a>身份管理
以下技术在 Azure 环境中提供用于管理数据访问的功能：
-   [Azure AD](https://azure.microsoft.com/services/active-directory/) 是 Microsoft 提供的多租户、基于云的目录和标识管理服务。 此解决方案的所有用户（包括访问 SQL Server 实例的用户）均在 Azure AD 中创建。
-   使用 Azure AD 对应用程序执行身份验证。 有关详细信息，请参阅如何[将应用程序与 Azure AD 集成](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)。
-   管理员可以使用 [Azure RBAC](../../role-based-access-control/role-assignments-portal.md) 定义细致的访问权限，以仅授予用户执行作业所需的访问量。 无需向每个用户授予 Azure 资源的不受限权限，管理员可以只允许使用特定的操作来访问数据。 订阅访问仅限于订阅管理员。
- 客户可以使用 [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) 最大限度地减少有权访问特定资源的用户数量。 管理员可以使用 Azure AD Privileged Identity Management 来发现、限制和监视特权标识及其对资源的访问。 此外，还可以根据需要，使用此功能来实施按需、实时的管理性访问。
- [Azure Active Directory 标识保护](../../active-directory/identity-protection/overview.md)可检测到影响组织标识的潜在漏洞。 它将自动响应配置为检测与组织标识相关的可疑操作。 它还会调查可疑事件，以采取适当的措施进行解决。

### <a name="security"></a>安全性
**机密管理**：解决方案使用[Key Vault](https://azure.microsoft.com/services/key-vault/)来管理密钥和机密。 Key Vault 帮助保护云应用程序和服务使用的加密密钥和机密。 以下 Key Vault 功能帮助客户保护数据：
- 根据需要配置高级访问权限策略。
- 使用对密钥和机密所需的最低权限来定义 Key Vault 访问策略。
- Key Vault 中的所有密钥和机密都有过期日期。
- Key Vault 中的所有密钥受专用硬件安全模块的保护。 密钥类型是硬件安全模块保护的 2048 位 RSA 密钥。
- 使用 RBAC 可向所有用户和标识授予最低要求的权限。
- Key Vault 的诊断日志已启用，其保留期至少为 365 天。
- 对密钥进行允许的加密操作时，仅限必需的操作。
- 此解决方案与密钥保管库集成，用于管理 IaaS VM 磁盘加密密钥和机密。

**修补程序管理**：默认情况下，在此参考体系结构中部署的 Windows Vm 配置为从 Windows 更新服务接收自动更新。 此解决方案还包括 [Azure 自动化](https://docs.microsoft.com/azure/automation/automation-intro)服务，可以在需要时创建更新部署，以修补 VM。

**恶意软件防护**：适用于 Vm 的[Microsoft 反恶意](https://docs.microsoft.com/azure/security/fundamentals/antimalware)软件提供了实时保护功能，可帮助识别和删除病毒、间谍软件和其他恶意软件。 客户可以配置当已知恶意软件或不需要的软件试图在受保护的 VM 上安装或运行时生成的警报。

**Azure 安全中心**：借助[安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)，客户可在工作负载中集中应用和管理安全策略、限制威胁暴露，以及检测和应对攻击。 此外，安全中心还会访问 Azure 服务的现有配置，以提供配置与服务建议来帮助改善安全状况和保护数据。

安全中心会使用各种检测功能，提醒客户针对其环境的潜在攻击。 这些警报包含有关触发警报的内容、目标资源以及攻击源的重要信息。 安全中心有一套[预定义安全警报](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)，在发生威胁或可疑活动时会触发此警报。 客户可以使用[自定义警报规则](https://docs.microsoft.com/azure/security-center/security-center-custom-alert)，根据从环境中已经收集到的数据定义新的安全警报。

安全中心提供按优先级排序的安全警报和事件。 安全中心让客户可以更轻松地发现并解决潜在安全问题。 针对每个检测到的威胁会生成一个[威胁智能报告](https://docs.microsoft.com/azure/security-center/security-center-threat-report)。 事件响应团队可在调查和修正威胁时使用这些报告。

此参考体系结构使用安全中心内的[漏洞评估](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)功能。 配置该功能后，合作伙伴代理（例如 Qualys）可向合作伙伴的管理平台报告漏洞数据。 反过来，合作伙伴的管理平台也会向安全中心提供漏洞和运行状况监视数据。 客户可以使用此信息来快速确定易受攻击的 VM。

**Azure 应用程序网关**：该体系结构通过使用配置了 web 应用程序防火墙的应用程序网关和启用了 OWASP 规则集，降低了安全漏洞的风险。 其他功能包括：

- [端到端 SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)。
- 启用 [SSL 卸载](../../application-gateway/create-ssl-portal.md)。
- 禁用 [TLS v1.0 和 v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)。
- [Web 应用程序防火墙](../../application-gateway/waf-overview.md)（保护模式）。
- 结合 OWASP 3.0 规则集的[保护模式](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal)。
- 启用[诊断日志记录](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)。
- [自定义运行状况探测](../../application-gateway/quick-create-portal.md)。
- [安全中心](https://azure.microsoft.com/services/security-center)和 [Azure 顾问](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations)提供额外的保护和通知。 安全中心还提供信誉系统。

### <a name="business-continuity"></a>业务连续性

**高可用性**：该解决方案将部署[可用性集中](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)的所有 vm。 可用性集可确保 VM 能够跨多个隔离的硬件群集分布，从而改进可用性。 计划内或计划外维护活动期间，至少有一台 VM 可用，满足 99.95% Azure SLA 的要求。

**恢复服务保管库**：[恢复服务保管库](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview)存储备份数据并保护此体系结构中的所有 Azure 虚拟机配置。 通过恢复服务保管库，客户可以从 IaaS VM 还原文件和文件夹，而无需还原整个 VM。 此过程可加快还原时间。

**云见证**：[云见证](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness)是 Windows Server 2016 中的一种故障转移群集仲裁见证，使用 Azure 作为仲裁点。 与其他任何仲裁见证一样，云见证可获得投票并可参与仲裁计算。 它使用标准公开可用的 Azure Blob 存储。 这种布排方式消除了在公有云中托管的 VM 的额外维护开销。

### <a name="logging-and-auditing"></a>日志记录和审核

Azure 服务广泛记录系统和用户活动以及系统运行状况：
- **活动日志**：[活动日志](../../azure-monitor/platform/activity-logs-overview.md)提供对订阅中资源执行的操作的深入信息。 活动日志可帮助确定操作的发起方、发生的时间和状态。
- **诊断日志**：[诊断日志](../../azure-monitor/platform/resource-logs-overview.md)包括每个资源发出的所有日志。 这些日志包括 Windows 事件系统日志、存储日志、Key Vault 审核日志以及应用程序网关访问和防火墙日志。 所有诊断日志都将写入到集中式加密 Azure 存储帐户以进行存档。 用户可以配置多达 730 天的保留期，以满足其特定要求。

**Azure Monitor 日志**：这些日志合并到[Azure Monitor 日志](https://azure.microsoft.com/services/log-analytics/)中，以便进行处理、存储和仪表板报告。 收集数据后，会针对 Log Analytics 工作区中的每种数据类型将数据整理到单独的表中。 如此一来，无论数据的原始源如何，所有数据都可以一起分析。 安全中心与 Azure Monitor 日志集成。 客户可以使用 Kusto 查询访问其安全事件数据，并将其与其他服务中的数据合并。

以下 Azure[监视解决方案](../../monitoring/monitoring-solutions.md)包括在此体系结构中：
-   [Active Directory 评估](../../azure-monitor/insights/ad-assessment.md)：Active Directory 运行状况检查解决方案会定期评估服务器环境的风险和运行状况。 此解决方案提供了特定于已部署服务器基础结构的建议优先级列表。
- [SQL 评估](../../azure-monitor/insights/sql-assessment.md)：SQL 运行状况检查解决方案会定期评估服务器环境的风险和运行状况。 此解决方案为客户提供了特定于已部署服务器基础结构的建议优先级列表。
- [代理运行状况](../../monitoring/monitoring-solution-agenthealth.md)：代理运行状况解决方案报告部署的代理数量及其地理分布状况。 此外，它还报告未响应代理数量和提交操作数据的代理数量。
-   [Activity Log Analytics](../../azure-monitor/platform/collect-activity-logs.md)：Activity Log Analytics 解决方案可帮助分析客户的所有 Azure 订阅的 Azure 活动日志。

**Azure 自动化**：[自动化](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker)可以存储、运行和管理 Runbook。 在此解决方案中，Runbook 可帮助从 SQL Server 中收集日志。 客户可以使用自动化[更改跟踪](../../automation/change-tracking.md)解决方案轻松识别环境中的更改。

**Azure Monitor**：[Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) 可以帮助用户跟踪性能、维护安全和确定趋势。 组织可以使用它来审核、创建警报并存档数据。 此外，他们还可以跟踪 Azure 资源中的 API 调用。

## <a name="threat-model"></a>威胁模型

此参考体系结构的数据流图可供[下载](https://aka.ms/nist171-iaaswa-tm)，也可以在此处找到。 此模型有助于客户在做出修改时了解系统基础结构中存在的潜在风险点。

![符合 NIST SP 800-171 威胁模型的 IaaS Web 应用程序](images/nist171-iaaswa-threat-model.png "符合 NIST SP 800-171 威胁模型的 IaaS Web 应用程序")

## <a name="compliance-documentation"></a>符合性文档

[Azure 安全性和符合性蓝图 - NIST SP 800-171 客户责任矩阵](https://aka.ms/nist171-crm)列出了 NIST SP 800-171 要求的所有安全控制措施。 此矩阵详细说明了每项控制措施的实施是由 Microsoft、客户还是两者共同负责。

[Azure 安全性与合规性蓝图 - NIST SP 800-171 IaaS Web 应用程序控制措施实施矩阵](https://aka.ms/nist171-iaaswa-cim)提供了有关 IaaS Web 应用程序体系结构可应对的 NIST SP 800-171 控制措施的信息， 其中包括实施方案如何满足每个所涵盖控制措施要求的详细说明。

## <a name="guidance-and-recommendations"></a>指导和建议
### <a name="vpn-and-expressroute"></a>VPN 和 ExpressRoute
必须配置安全 VPN 隧道或 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)，以安全地建立与作为此 IaaS Web 应用程序参考体系结构的一部分部署的资源的连接。 通过适当设置 VPN 或 ExpressRoute，客户可以为传输中的数据添加一层保护。

在 Azure 中实施安全 VPN 隧道，可在本地网络与 Azure 虚拟网络之间创建虚拟专用连接。 此连接通过 Internet 实现。 客户可以利用此连接在客户的网络和 Azure 之间安全“传输”加密链接内的信息。 站点到站点 VPN 是安全成熟的技术，各种规模的企业已部署数十年。 此选项使用 [IPsec 隧道模式](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10))作为加密机制。

由于 VPN 隧道中的流量会通过站点到站点 VPN 在 Internet 上遍历，因此，Microsoft 提供了另一个更为安全的连接选项。 ExpressRoute 是 Azure 与本地位置或 Exchange 托管提供商之间专用的 WAN 链接。 ExpressRoute 连接可直接连接到客户的电信服务提供商。 因此，数据不会在 Internet 上传输且不会对 Internet 公开。 与典型连接相比，这些连接在可靠性、速度、延迟和安全性方面都更胜一筹。 

我们编写了有关如何实施安全混合网络，以便将本地网络扩展到 Azure 的[最佳做法](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)。

## <a name="disclaimer"></a>免责声明

- 本文档仅供参考。 MICROSOFT 对本文档中的信息不作任何明示、默示或法定的担保。 本文档“按原样”提供。 本文档表达的信息和观点，包括 URL 和其他 Internet 网站参考，若有更改，恕不另行通知。 阅读本文档的客户须自行承担使用风险。 
- 本文档不向客户提供对任何 Microsoft 产品或解决方案的任何知识产权的任何法律权利。 
- 客户可复制本文档，将其用于内部参考。 
- 本文档中的某些建议可能会导致 Azure 中数据、网络或计算资源使用量的增加，还可能导致客户 Azure 许可或订阅成本增加。 
- 本体系结构旨在作为客户的基础，以根据他们的特定需求进行调整，而不应在生产环境中按原样使用。
- 本文档是作为参考内容制定的，不应该用于定义客户用来满足特定符合性要求和法规的所有方法。 客户应在已批准的客户实施项目中向其组织寻求合法支持。
