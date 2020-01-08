---
title: Azure 安全性和符合性蓝图：符合 PCI DSS 的 PaaS Web 应用程序
description: Azure 安全性和符合性蓝图：符合 PCI DSS 的 PaaS Web 应用程序
services: security
author: meladie
ms.assetid: 5ef64374-7b4e-4176-afe1-0724072f653c
ms.service: security
ms.topic: article
ms.date: 07/03/2018
ms.author: meladie
ms.openlocfilehash: 45c3e5da6cff51e14d22ed3a6a3ab526b96b50ef
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71259657"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-for-pci-dss"></a>Azure 安全性与合规性蓝图：PCI DSS 的 PaaS Web 应用程序

## <a name="overview"></a>概述

Azure 安全性和符合性蓝图自动化提供了相关指南，引导你部署一个适合收集、存储和检索持卡人数据，且符合支付卡行业数据安全标准 (PCI DSS 3.2) 的平台即服务 (PaaS) 环境。 此解决方案自动完成适用于常用参考体系结构的 Azure 资源的部署和配置，演示了客户如何通过多种方式达到特定的安全和符合性要求，是客户在 Azure 上生成和配置其解决方案的基础。 该解决方案实施了 PCI DSS 3.2 中规定的部分要求。 要详细了解 PCI DSS 3.2 要求和本解决方案，请参阅[符合性文档](#compliance-documentation)部分。

Azure 安全性和符合性蓝图自动化可自动部署一个 PaaS Web 应用程序参考体系结构，该结构中预配置有安全控件，可帮助客户符合 PCI DSS 3.2 的要求。 此解决方案包含 Azure 资源管理器模板和 PowerShell 脚本，指导用户进行资源部署和配置。

本体系结构旨在作为客户的基础，以根据他们的特定需求进行调整，而不应在生产环境中按原样使用。 未经修改就向此环境部署应用程序并不足以完全满足 PCI DSS 3.2 所设定的要求。 请注意以下事项：
- 本体系结构提供一个基线，帮助客户按符合 PCI DSS 3.2 要求的方式使用 Azure。
- 客户负责针对使用本体系结构构建的任何解决方案开展相应的安全与合规评估；具体要求根据客户的每种实施方式具体情况而异。

实现 PCI DSS 遵从性需要持证的合格安全评审员 (QSA) 认证客户的生产解决方案。 客户有责任对使用本体系结构构建的任何解决方案进行相应的安全性与符合性评估，因为具体要求可能会因客户的具体实施情况而异。

单击[此处](https://aka.ms/pcidss-paaswa-repo)获取部署说明。

## <a name="architecture-diagram-and-components"></a>体系结构示意图和组件

本文中的 Azure 安全性和符合性蓝图自动化可针对具有 Azure SQL 数据库后端的 PaaS Web 应用程序部署一个参考体系结构。 该 Web 应用程序托管在独立的 Azure 应用服务环境中，该环境是 Azure 数据中心内的私有专用环境。 该环境针对 Azure 管理的虚拟机上的 Web 应用程序流量进行负载均衡。 此体系结构还包括网络安全组、应用程序网关、Azure DNS 和负载均衡器。

对于增强的分析和报告，可以通过列存储索引配置 Azure SQL 数据库。 Azure SQL 数据库可完全按照客户的使用情况进行扩展、缩小或关闭。 所有 SQL 流量都通过包含自签名证书使用 SSL 加密。 Azure 建议使用受信任的证书颁发机构来增强安全性，这是最佳做法。

该解决方案使用 Azure 存储帐户。客户可将存储帐户配置为使用存储服务加密，以维持静态数据的机密性。 Azure 在客户选择的数据中心存储三个数据副本，以提供复原能力。 地理冗余的存储确保将数据复制到数百英里以外的辅助数据中心，并同样在该数据中心存储三个副本，防止客户主要数据中心的不利事件导致数据丢失。

为了增强安全性，将通过 Azure 资源管理器以资源组的形式管理此解决方案中的所有资源。 Azure Active Directory 基于角色的访问控制用于控制对所部署资源（包括 Azure Key Vault 中的密钥）的访问。 系统运行状况通过 Azure Monitor 进行监视。 客户配置两个监视服务捕获日志并在单独的、可轻松导航的仪表板中显示系统运行状况。

Azure SQL 数据库通常通过 SQL Server Management Studio 进行管理，后者通过配置为经由安全 VPN 或 ExpressRoute 连接访问 Azure SQL 数据库的本地计算机运行。

此外，Application Insights 通过 Azure Monitor 日志提供实时应用程序性能管理和分析。 **Microsoft 建议配置 VPN 或 ExpressRoute 连接，从而实现管理并将数据导入参考体系结构子网。**

![符合 PCI DSS 参考体系结构的 PaaS Web 应用程序图示](images/pcidss-paaswa-architecture.png "符合 PCI DSS 参考体系结构的 PaaS Web 应用程序图示")

此解决方案使用以下 Azure 服务。 [部署体系结构](#deployment-architecture)部分提供了部署体系结构的详细信息。

- 应用服务环境 v2
- 应用程序网关
  - (1) Web 应用程序防火墙
    - 防火墙模式：防护
    - 规则集：OWASP 3.0
    - 侦听器端口：443
- Application Insights
- Azure Active Directory
- Azure 自动化
- Azure DNS
- Azure Key Vault
- Azure 负载均衡器
- Azure Monitor
- Azure 资源管理器
- Azure 安全中心
- Azure SQL 数据库
- Azure 存储
- Azure 虚拟网络
    - (1)/16 网络
    - (4)/24 网络
    - (4) 网络安全组
- Azure Web 应用

## <a name="deployment-architecture"></a>部署体系结构

以下部分详细描述了部署和实施要素。

**Azure 资源管理器**：借助[Azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)，客户可以以组的形式使用解决方案中的资源。 客户可以通过一个协调的操作为解决方案部署、更新或删除所有资源。 客户可以使用一个模板来完成部署，该模板适用于不同的环境，例如测试、过渡和生产。 资源管理器提供安全、审核和标记功能，以帮助客户在部署后管理资源。

**守护主机**：守护主机是允许用户访问此环境中已部署资源的单一入口点。 守护主机通过仅允许来自安全列表上的公共 IP 地址的远程流量来提供到已部署资源的安全连接。 要允许远程桌面 (RDP) 流量，需要在网络安全组中定义流量的源。

此解决方案使用以下配置将虚拟机创建为已加入域的守护主机：
-   [反恶意软件扩展](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
-   [Azure 诊断扩展](../../virtual-machines/windows/extensions-diagnostics-template.md)
-   使用 Azure Key Vault 的 [Azure 磁盘加密](../azure-security-disk-encryption-overview.md)
-   [自动关闭策略](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)，在不使用虚拟机时可减少其资源消耗量
-   已启用 [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard)，以便凭据和其他机密在与正在运行的操作系统隔离的受保护环境中运行

**应用服务环境 v2**：Azure 应用服务环境是一项应用服务功能，可提供完全隔离和专用的环境，以便高度安全地运行应用服务应用程序。 此项隔离功能必须满足 PCI 符合性要求。

应用服务环境进行了隔离，仅运行单个客户的应用程序，且始终部署到虚拟网络中。 此项隔离功能让参考体系结构实现了完全的租户隔离，在禁止这些多租户枚举已部署的应用程序环境资源的 Azure 多租户环境中删除了该租户。 客户对于入站和出站的应用网络流量都有更细微的控制，且应用程序可以通过虚拟网络创建与本地公司资源的高速安全连接。 客户可根据负载指标、可用预算或定义的计划，在应用服务环境中“自动缩放”。

利用应用服务环境进行以下控制/配置：

- 托管在受保护的 Azure 虚拟网络中并受网络安全规则的保护
- 用于 HTTPS 通信的自签名 ILB 证书
- [内部负载均衡模式](../../app-service/environment/app-service-environment-with-internal-load-balancer.md)
- 禁用 [TLS 1.0](../../app-service/environment/app-service-app-service-environment-custom-settings.md)
- 更改 [TLS 密码](../../app-service/environment/app-service-app-service-environment-custom-settings.md)
- 控制[入站流量 N/W 端口](../../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md)
- [Web 应用程序防火墙 - 限制数据](../../app-service/environment/app-service-app-service-environment-web-application-firewall.md)
- 允许 [Azure SQL 数据库流量](../../app-service/environment/app-service-app-service-environment-network-architecture-overview.md)

**Azure Web 应用**：使用 [Azure 应用服务](https://docs.microsoft.com/azure/app-service/)，客户可以采用所选编程语言构建和托管 Web 应用程序，而无需管理基础结构。 它提供自动缩放和高可用性，支持 Windows 和 Linux，并支持从 GitHub、Azure DevOps 或任何 Git 存储库进行自动部署。

### <a name="virtual-network"></a>虚拟网络

体系结构定义了一个地址空间为 10.200.0.0/16 的专用虚拟网络。

**网络安全组**：[网络安全组](../../virtual-network/virtual-network-vnet-plan-design-arm.md)包含允许或拒绝虚拟网络中流量的访问控制列表 (ACL)。 网络安全组可用于在子网或单个 VM 级别保护流量。 存在以下网络安全组：

- 应用程序网关有 1 个网络安全组
- 应用服务环境有 1 个网络安全组
- Azure SQL 数据库有 1 个网络安全组
- 堡垒主机有 1 个网络安全组

每个网络安全组打开了特定的端口和协议，使解决方案能够安全正确地工作。 此外，为每个网络安全组启用了以下配置：

- [诊断日志和事件](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)已启用并存储在存储帐户中
- Azure Monitor 日志连接到[网络安全组&#39;的诊断](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**子网**：每个子网都与其对应的网络安全组相关联。

**Azure DNS**：域名系统或 DNS 负责将网站或服务名称转换（或解析）为它的 IP 地址。 [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) 是 DNS 域的托管服务，它使用 Azure 基础结构提供名称解析。 通过在 Azure 中托管域，用户可以使用与其他 Azure 服务相同的凭据、API、工具和账单来管理 DNS 记录。 Azure DNS 还支持 DNS 专用域。

**Azure 负载均衡器**：使用 [Azure 负载均衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)，客户可以缩放应用程序，并为服务创建高可用性。 负载均衡器支持入站和出站场景、提供低延迟和高吞吐量，以及为所有 TCP 和 UDP 应用程序纵向扩展到数以百万计的流。

### <a name="data-in-transit"></a>传输中的数据

默认情况下，Azure 会加密与 Azure 数据中心之间的所有通信。 通过 Azure 门户到 Azure 存储的所有事务均通过 HTTPS 进行。

### <a name="data-at-rest"></a>静态数据

该体系结构通过加密、数据库审核和其他措施保护静态数据。

**Azure 存储**：为了满足静态数据加密要求，[Azure 存储](https://azure.microsoft.com/services/storage/)使用了[存储服务加密](../../storage/common/storage-service-encryption.md)。 这有助于保护持卡人数据，以支持 PCI DSS 3.2 定义的组织安全承诺和符合性要求。

**Azure 磁盘加密**：[Azure 磁盘加密](../azure-security-disk-encryption-overview.md)利用 Windows 的 BitLocker 功能，为数据磁盘提供卷加密。 此解决方案与 Azure Key Vault 集成，可帮助控制和管理磁盘加密密钥。

**Azure SQL 数据库**：Azure SQL 数据库实例使用以下数据库安全措施：

- 使用 [Active Directory 身份验证和授权](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)可在一个中心位置集中管理数据库用户和其他 Microsoft 服务的标识。
- [SQL 数据库审核](../../sql-database/sql-database-auditing.md)跟踪数据库事件，并将事件写入 Azure 存储帐户中的审核日志。
- Azure SQL 数据库配置为使用[透明数据加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)，它对数据库、相关备份和事务日志文件进行实时加密和解密，以保护静态信息。 透明数据加密可确保存储的数据免遭他人未经授权的访问。
- 在授予相应的权限前，[防火墙规则](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)会阻止对数据库服务器的所有访问。 防火墙基于每个请求的起始 IP 地址授予数据库访问权限。
- [SQL 威胁检测](../../sql-database/sql-database-threat-detection.md)可以通过为可疑数据库活动、潜在漏洞、SQL 注入攻击和异常数据库访问模式提供安全警报，在发生潜在威胁时进行检测和响应。
- [加密列](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)可以确保敏感数据永远不会在数据库系统中以明文形式显示。 启用数据加密后，只有具有密钥访问权限的客户端应用程序或应用程序服务器才能访问明文数据。
- [SQL 数据库动态数据掩码](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started)通过对非特权用户或应用程序模糊化敏感数据来限制此类数据的泄漏。 动态数据掩码可以自动发现潜在的敏感数据，并建议应用合适的掩码。 这有助于识别和减少数据访问，避免通过未经授权的访问退出数据库。 客户需要负责调整动态数据掩码设置，使之遵循其数据库架构。

### <a name="identity-management"></a>身份管理

以下技术在 Azure 环境中提供用于管理持卡人数据访问的功能：

- [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 是 Microsoft 提供的多租户、基于云的目录和标识管理服务。 解决方案的所有用户（包括访问 Azure SQL 数据库的用户）都在 Azure Active Directory 中创建。
- 使用 Azure Active Directory 对应用程序执行身份验证。 有关详细信息，请参阅[将应用程序与 Azure Active Directory 集成](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)。 此外，数据库列加密使用 Azure Active Directory 对访问 Azure SQL 数据库的应用程序进行身份验证。 有关详细信息，请参阅如何[保护 Azure SQL 数据库中的敏感数据](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)。
- [Azure 基于角色的访问控制](../../role-based-access-control/role-assignments-portal.md)使管理员能够定义细粒度的访问权限，以仅授予用户执行作业所需的访问量。 无需向每个用户授予 Azure 资源的不受限权限，管理员可以只允许使用特定的操作来访问持卡人数据。 订阅访问仅限于订阅管理员。
- [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) 使客户能够最大限度地减少有权访问持卡人数据等特定信息的用户数量。 管理员可以使用 Azure Active Directory Privileged Identity Management 来发现、限制和监视特权标识及其对资源的访问。 还可以根据需要，使用此功能来实施按需、实时的管理访问。
- [Azure Active Directory 标识保护](../../active-directory/identity-protection/overview.md)会检测到影响组织标识的潜在漏洞，配置自动化的措施来应对所检测到的与组织标识相关的可疑操作，调查可疑的事件以采取相应的措施予以解决。

### <a name="security"></a>安全性

**机密管理**：此解决方案使用 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 管理密钥和机密。 Azure 密钥保管库可帮助保护云应用程序和服务使用的加密密钥和机密。 以下 Azure Key Vault 功能可帮助客户保护和访问此类数据：

- 根据需要配置高级访问权限策略。
- 使用对密钥和机密所需的最低权限来定义 Key Vault 访问策略。
- Key Vault 中的所有密钥和机密都有过期日期。
- Key Vault 中的所有密钥受专用硬件安全模块的保护。 密钥类型为受 HSM 保护的 2048 位 RSA 密钥。
- 使用基于角色的访问控制向所有用户和标识授予了最低必需权限。
- Key Vault 的诊断日志已启用，其保留期至少为 365 天。
- 对密钥进行允许的加密操作时，仅限必需的操作。

**Azure 安全中心**：借助 [Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)，客户可在工作负载中集中应用和管理安全策略、限制威胁暴露，以及检测和应对攻击。 此外，Azure 安全中心会访问 Azure 服务的现有配置，以提供配置与服务建议来帮助改善安全状况和保护数据。

Azure 安全中心使用各种检测功能，提醒客户针对其环境的潜在攻击。 这些警报包含有关触发警报的内容、目标资源以及攻击源的重要信息。 Azure 安全中心有一组[预定义的安全警报](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)，这些警报在出现威胁或可疑活动时触发。 客户可以使用 Azure 安全中心的[自定义警报规则](https://docs.microsoft.com/azure/security-center/security-center-custom-alert)，根据从环境中收集到的数据定义新的安全警报。

Azure 安全中心提供区分优先级的安全警报和事件，让客户更轻松地发现和解决潜在安全问题。 针对检测到的每种威胁生成[威胁智能报告](https://docs.microsoft.com/azure/security-center/security-center-threat-report)，帮助事件响应团队调查和解决威胁。

**Azure 应用程序网关**：体系结构使用配置了 Web 应用程序防火墙并启用了 OWASP 规则集的应用程序网关，来降低安全漏洞风险。 其他功能包括：

- [端到端 SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- 启用 [SSL 卸载](../../application-gateway/create-ssl-portal.md)
- 禁用 [TLS v1.0 和 v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Web 应用程序防火墙](../../application-gateway/waf-overview.md)（预防模式）
- 结合 OWASP 3.0 规则集的[保护模式](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal)
- 启用[诊断日志记录](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [自定义运行状况探测](../../application-gateway/quick-create-portal.md)
- [Azure 安全中心](https://azure.microsoft.com/services/security-center)和 [Azure 顾问](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations)提供额外的保护和通知。 Azure 安全中心还提供信誉系统。

### <a name="logging-and-auditing"></a>日志记录和审核

Azure 服务广泛记录系统和用户活动以及系统运行状况：
- **活动日志**：[活动日志](../../azure-monitor/platform/activity-logs-overview.md)提供对订阅中资源执行的操作的深入信息。 活动日志可帮助确定操作的发起方、发生的时间和状态。
- **诊断日志**：[诊断日志](../../azure-monitor/platform/resource-logs-overview.md)包括每个资源发出的所有日志。 这些日志包括 Windows 事件系统日志、Azure 存储日志、Key Vault 审核日志以及应用程序网关访问和防火墙日志。 所有诊断日志都将写入到集中式加密 Azure 存储帐户以进行存档。 保留期是允许用户配置的，最长为 730 天，具体取决于组织的保留期要求。

**Azure Monitor 日志**：这些日志合并到[Azure Monitor 日志](https://azure.microsoft.com/services/log-analytics/)中，以便进行处理、存储和仪表板报告。 收集后，数据在 Log Analytics 工作区内按数据类型整理到不同的表中，这样即可不考虑最初来源而集中分析所有数据。 此外，Azure 安全中心与 Azure Monitor 日志集成，使客户可以使用 Kusto 查询访问其安全事件数据，并将其与其他服务中的数据合并。

以下 Azure[监视解决方案](../../monitoring/monitoring-solutions.md)包括在此体系结构中：
-   [Active Directory 评估](../../azure-monitor/insights/ad-assessment.md)：Active Directory 运行状况检查解决方案按固定时间间隔评估服务器环境的风险和运行状况，并且提供特定于部署的服务器基础结构的优先建议列表。
- [SQL 评估](../../azure-monitor/insights/sql-assessment.md)：SQL 运行状况检查解决方案按固定时间间隔评估服务器环境的风险和运行状况，并为客户提供特定于部署的服务器基础结构的优先建议列表。
- [代理运行状况](../../monitoring/monitoring-solution-agenthealth.md)：代理运行状况解决方案报告已部署代理的数量及其地理分布，以及无响应的代理数量和提交操作数据的代理数量。
-   [Activity Log Analytics](../../azure-monitor/platform/collect-activity-logs.md)：Activity Log Analytics 解决方案可帮助分析客户的所有 Azure 订阅的 Azure 活动日志。

**Azure 自动化**：[Azure 自动化](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker)可以存储、运行和管理 Runbook。 在此解决方案中，Runbook 可帮助从 Azure SQL 数据库收集日志。 自动化[更改跟踪](../../automation/change-tracking.md)解决方案使得客户能够轻松识别环境中的更改。

**Azure Monitor**：[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) 通过使组织能够审核、创建警报和存档数据（包括在用户的 Azure 资源中跟踪 API 调用），帮助用户跟踪性能、维护安全性和确定趋势。

**Application Insights**：[Application Insights](../../azure-monitor/app/app-insights-overview.md) 是多个平台上面向 Web 开发人员的可扩展应用程序性能管理服务。 Application Insights 可检测性能异常，客户可以使用它来监视实时 Web 应用程序。 它包含强大的分析工具来帮助客户诊断问题，了解用户在应用中实际执行了哪些操作。 它旨在帮助客户持续提高性能和可用性。

## <a name="threat-model"></a>威胁模型

此参考体系结构的数据流图可供[下载](https://aka.ms/pcidss-paaswa-tm)，也可以在下面找到。 此模型有助于客户在做出修改时了解系统基础结构中存在的潜在风险点。

![符合 PCI DSS 威胁模型的 PaaS Web 应用程序](images/pcidss-paaswa-threat-model.png "符合 PCI DSS 威胁模型的 PaaS Web 应用程序")

## <a name="compliance-documentation"></a>符合性文档

[Azure 安全性和符合性蓝图 - PCI DSS 客户责任矩阵](https://aka.ms/pcidss-crm)列出了所有 PCI DSS 3.2 要求的控制方和处理方责任。

[Azure 安全性和符合性蓝图 - PCI DSS PaaS Web 应用程序实施矩阵](https://aka.ms/pcidss-paaswa-cim)提供了有关 PaaS Web 应用程序体系结构解决哪些 PCI DSS 3.2 要求的信息，包括实施方案如何满足每个所述项目要求的详细说明。

## <a name="deploy-this-solution"></a>部署此解决方案
此 Azure 安全性和符合性蓝图自动化包含的 JSON 配置文件和 PowerShell 脚本可供 Azure 资源管理器的 API 服务用来在 Azure 中部署资源。 [此处](https://aka.ms/pcidss-paaswa-repo)提供了详细的部署说明。

#### <a name="quickstart"></a>快速入门
1. 将[此](https://aka.ms/pcidss-paaswa-repo) GitHub 存储库克隆或下载到本地工作站。

2. 查看 0-Setup-AdministrativeAccountAndPermission.md 并运行所提供的命令。

3. 使用 Contoso 示例数据部署测试解决方案，或试用初始生产环境。
   - 1A-ContosoWebStoreDemoAzureResources.ps1
     - 此脚本部署了 Azure 资源，借以通过 Contoso 示例数据展示网上商店。
   - 1-DeployAndConfigureAzureResources.ps1
     - 此脚本部署了支持客户自有 Web 应用程序的生产环境所需的 Azure 资源。 客户应当根据组织要求进一步地自定义此环境。

## <a name="guidance-and-recommendations"></a>指导和建议

### <a name="vpn-and-expressroute"></a>VPN 和 ExpressRoute

需要配置安全 VPN 隧道或 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)，以安全地建立与作为此 PaaS Web 应用程序参考体系结构的一部分部署的资源的连接。 通过适当设置 VPN 或 ExpressRoute，客户可以为传输中的数据添加一层保护。

在 Azure 中实施安全 VPN 隧道，可在本地网络与 Azure 虚拟网络之间创建虚拟专用连接。 此连接通过 Internet 进行，可让客户在其网络与 Azure 之间的加密链路内通过“隧道”安全地传输信息。 站点到站点 VPN 是安全且成熟的技术，各种规模的企业已部署该技术数十年。 此选项使用 [IPsec 隧道模式](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10))作为加密机制。

由于 VPN 隧道中的流量会通过站点到站点 VPN 在 Internet 上遍历，Microsoft 提供了另一个更安全的连接选项。 Azure ExpressRoute 是 Azure 与本地位置或 Exchange 托管提供商之间专用的 WAN 链接。 ExpressRoute 连接并不绕过 Internet，并且与通过 Internet 的典型连接相比，这些连接可靠性更高、速度更快、延迟时间更短且安全性更高。 此外，由于使用的是客户电信提供商的直接连接，数据不会通过 Internet 遍历，因此不会在 Internet 上公开。

我们编写了有关如何实施安全混合网络，以便将本地网络扩展到 Azure 的[最佳做法](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)。

## <a name="disclaimer"></a>免责声明

- 本文档仅供参考。 MICROSOFT 对本文档中的信息不作任何明示、默示或法定的担保。 本文档“按原样”提供。本文档表达的信息和观点，包括 URL 和其他 Internet 网站参考，若有更改，恕不另行通知。 阅读本文档的客户须自行承担使用风险。
- 本文档不向客户提供对任何 Microsoft 产品或解决方案的任何知识产权的任何法律权利。
- 客户可复制本文档，将其用于内部参考。
- 本文档中的某些建议可能会导致 Azure 中数据、网络或计算资源使用量的增加，还可能导致客户 Azure 许可或订阅成本增加。
- 本体系结构旨在作为客户的基础，以根据他们的特定需求进行调整，而不应在生产环境中按原样使用。
- 本文档是作为参考内容制定的，不应该用于定义客户用来满足特定符合性要求和法规的所有方法。 客户应在已批准的客户实施项目中向其组织寻求合法支持。
