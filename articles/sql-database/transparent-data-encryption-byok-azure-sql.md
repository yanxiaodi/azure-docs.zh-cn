---
title: TDE - Azure Key Vault 集成或创建自己的密钥 (BYOK) - Azure SQL 数据库 |Microsoft Docs
description: SQL 数据库和数据仓库 Azure Key Vault 的透明数据加密 (TDE) 的“创建自己的密钥”(BYOK) 支持。 支持 BYOK 的 TDE 概述、优势、工作原理、注意事项和建议。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
ms.date: 07/18/2019
ms.openlocfilehash: 095ecc360e5639a5d47dff4bc4675fc237cf81da
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348918"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-keys-in-azure-key-vault-bring-your-own-key-support"></a>使用 Azure Key Vault 中由客户管理的密钥进行 Azure SQL 透明数据加密：自带密钥支持

集成了 Azure Key Vault 的[透明数据加密 (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) 允许使用由客户管理的非对称密钥（称为 TDE 保护器）对数据库加密密钥 (DEK) 进行加密。 这通常也称为透明数据加密的创建自己的密匙 (BYOK) 支持。  在 BYOK 方案中，TDE 保护器存储在由客户拥有和管理的 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)（Azure 的基于云的外部密钥管理系统）中。 TDE 保护器可由密钥保管库[生成](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates)，也可以从本地 HSM 设备[转移](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys)到密钥保管库。 TDE DEK 存储在数据库的启动页上，由 TDE 保护器进行加密和解密，该保护器存储在 Azure Key Vault 中并且从不会离开密钥保管库。  需要向 SQL 数据库授予对客户管理的密钥保管库的权限才能对 DEK 进行解密和加密。 如果撤销了逻辑 SQL Server 对 Key Vault 的权限，则数据库将不可访问，连接将被拒绝，并且所有数据将会加密。 对于 Azure SQL 数据库，TDE 保护器是在逻辑 SQL Server 级别设置的，并由该服务器关联的所有数据库继承。 对于 [Azure SQL 托管实例](https://docs.microsoft.com/azure/sql-database/sql-database-howto-managed-instance)，TDE 保护器是在实例级别设置的，并由该实例上所有加密的数据库继承。 除非另有说明，否则术语“服务器”在整个文档中指的是服务器和实例。

> [!NOTE]
> Azure SQL 数据库托管实例的集成了 Azure Key Vault 的透明数据加密（创建自己的密钥）处于预览状态。


使用集成了 Azure Key Vault 的 TDE，用户可以控制密钥管理任务，包括密钥轮换、密钥保管库权限、密钥备份，以及使用 Azure Key Vault 功能对所有 TDE 保护器启用审核/报告。 Key Vault 提供了集中化密钥管理功能，利用严格监控的硬件安全模块 (HSM)，并可在密钥与数据管理之间实现职责分离，以帮助满足安全策略的要求。  

集成了 Azure Key Vault 的 TDE 具有以下优势：

- 更高的透明度和细化控制，能够自我管理 TDE 保护器
- 随时能够撤销权限，使数据库不可访问
- 通过将 TDE 保护器（以及其他 Azure 服务中使用的其他密钥和机密）托管在 Key Vault 中，对其进行集中管理
- 将组织内部的密钥与数据管理责任相分离，以支持职责分离
- 自己的客户端更值得信赖，因为 Key Vault 的设计可以防止 Microsoft 查看或提取任何加密密钥。
- 支持密钥轮换

> [!IMPORTANT]
> 对于目前正在使用服务托管的 TDE，但想要开始使用 Key Vault 的用户，在切换到 Key Vault 中 TDE 保护器的过程中，会保持启用 TDE。 这既不会造成停机，也无需重新加密数据库文件。 从服务托管的密钥切换到 Key Vault 密钥只需重新加密数据库加密密钥 (DEK)，此操作非常快捷且可在线完成。

## <a name="how-does-tde-with-azure-key-vault-integration-support-work"></a>支持 Azure Key Vault 集成的 TDE 如何工作

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure 资源管理器模块仍受 Azure SQL 数据库的支持，但所有未来的开发都是针对 Az.Sql 模块的。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。

![在 Key Vault 中对服务器进行身份验证](./media/transparent-data-encryption-byok-azure-sql/tde-byok-server-authentication-flow.PNG)

首次将 TDE 配置为使用 Key Vault 中的 TDE 保护器后，服务器会将每个已启用 TDE 的数据库的 DEK 发送到 Key Vault，以发出包装密钥请求。 Key Vault 返回存储在用户数据库中的已加密数据库加密密钥。  

> [!IMPORTANT]
> 必须注意，将 TDE 保护器存储在 Azure Key Vault 中之后，它永远不会离开 Azure Key Vault。 服务器只能将密钥操作请求发送到 Key Vault 中的 TDE 保护器密钥材料，并且永远不会访问或缓存 TDE 保护器。 Key Vault 管理员随时有权撤销服务器的 Key Vault 权限，在这种情况下，将拒绝与数据库建立任何连接。

## <a name="guidelines-for-configuring-tde-with-azure-key-vault"></a>有关配置采用 Azure Key Vault 的 TDE 的准则

### <a name="general-guidelines"></a>一般性指导

- 确保 Azure Key Vault 和 Azure SQL 数据库/托管实例位于同一租户中。  **不支持** Key Vault 与服务器进行跨租户的交互。
- 如果你正在规划租户迁移，则必须重新配置 TDE 和 AKV。有关详细信息，请参阅[移动资源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)。
- 配置采用 Azure Key Vault 的 TDE 时，必须考虑到重复的包装/解包操作在密钥保管库中施加的负载。 例如，由于与 SQL 数据库服务器关联的所有数据库使用相同的 TDE 保护器，因此，该服务器的故障转移将会针对保管库触发密钥操作：服务器中有多少个数据库，就会触发多少次密钥操作。 根据我们的经验以及 [Key Vault 服务限制](https://docs.microsoft.com/azure/key-vault/key-vault-service-limits)中所述，我们建议将最多 500 个标准/常规用途库或 200 个高级/业务关键型数据库与单个订阅中的 1 个 Azure Key Vault 相关联，以确保在访问保管库中的 TDE 保护器时能够获得一致的高可用性。
- 建议：在本地保留 TDE 保护器的副本。  这要求 HSM 设备在本地创建一个 TDE 保护器，并创建一个密钥托管系统来存储 TDE 保护器的本地副本。  了解[如何将密钥从本地 HSM 转移到 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys)。


### <a name="guidelines-for-configuring-azure-key-vault"></a>有关配置 Azure Key Vault 的指导

- 创建启用[软删除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)和清除保护的 Key Vault，以防在意外删除了密钥或 Key Vault 时丢失数据。  必须使用 [PowerShell 在 Key Vault 中启用“软删除”属性](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell)（目前无法从 AKV 门户使用此选项 – 但 Azure SQL 需要此选项）：  
  - 除非进行恢复或清除，否则软删除的资源将保留设置的一段时间（90 天）。
  - “恢复”和“清除”操作在密钥保管库访问策略中各自具有相关联的权限。
- 在 Key Vault 中设置资源锁可以控制谁能删除此关键资源，并帮助防止意外或未经授权的删除。  [详细了解资源锁](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)

- 使用 SQL 数据库服务器的 Azure Active Directory (Azure AD) 标识授予其对密钥保管库的访问权限。  使用门户 UI 时，会自动创建 Azure AD 标识，并向服务器授予 Key Vault 访问权限。  使用 PowerShell 配置支持 BYOK 的 TDE 时，必须手动创建 Azure AD 标识，且验证完成进度。 有关使用 PowerShell 进行配置的详细分步说明，请参阅[配置支持 BYOK 的 TDE](transparent-data-encryption-byok-azure-sql-configure.md) 和[配置支持托管实例 BYOK 的 TDE](https://aka.ms/sqlmibyoktdepowershell)。

   > [!NOTE]
   > 如果**意外删除了 Azure AD 标识、使用 Key Vault 的访问策略撤销了服务器的权限，或者将服务器移到不同的租户时意外撤销了服务器的权限**，则在还原逻辑服务器的 Azure AD 标识和权限之前，服务器将失去 Key Vault 的访问权限，TDE 加密的数据库将不可访问，并且登录将被拒绝。  

- 在对 Azure Key Vault 使用防火墙和虚拟网络时，必须允许受信任的 Microsoft 服务绕过此防火墙。 选择“是”。

   > [!NOTE]
   > 如果 TDE 加密的 SQL 数据库由于无法绕过防火墙而失去了对 Key Vault 的访问权限，则在还原防火墙绕过权限之前，这些数据库将不可访问，并且登录将被拒绝。

- 对所有加密密钥启用审核和报告：Key Vault 提供可以轻松注入到其他安全信息和事件管理 (SIEM) 工具的日志。 Operations Management Suite (OMS) [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault) 是已集成的服务的一个示例。
- 为了确保已加密数据库的高可用性，请在每个 SQL 数据库服务器上配置两个驻留在不同区域的 Azure Key Vault。


### <a name="guidelines-for-configuring-the-tde-protector-asymmetric-key"></a>有关配置 TDE 保护器（非对称密钥）的指导

- 在本地 HSM 设备上创建加密密钥。 请确保这是不对称的、RSA 2048 或 RSA HSM 2048 的密钥，以便 Azure Key Vault 中存储。
- 将密钥托管在密钥托管系统中。  
- 将加密密钥文件（.pfx、.byok 或 .backup）导入 Azure Key Vault。

   > [!NOTE]
   > 测试时，可以使用 Azure Key Vault 创建密钥，但无法托管此密钥，因为私钥永远无法离开 Key Vault。  始终备份并托管用于加密生产数据的密钥，因为丢失密钥（在 Key Vault 中意外删除、过期等等）会导致永久性的数据丢失。

- 如果你使用具有到期日期的密钥–实现过期警告系统以便在密钥过期之前对其进行轮替：**一旦密钥过期，加密的数据库将失去对其 TDE 保护程序的访问权限**，并且所有登录都将被拒绝，直到该键已轮换到新键，并选择作为逻辑 SQL 服务器的新键和默认 TDE 保护程序。
- 确保密钥已启用并有权执行“获取”、“包装密钥”和“解包密钥”操作。
- 在首次使用 Azure Key Vault 中的密钥之前，请创建 Azure Key Vault 密钥备份。 了解有关[AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey)命令的详细信息。
- 每次对密钥进行任何更改后（例如，添加 ACL、添加标记、添加密钥属性），请创建新备份。
- 轮换密钥时保留 Key Vault 中密钥的**先前版本**，以便可以还原旧数据库备份。 更改数据库的 TDE 保护器后，数据库的旧备份**不会更新**为使用最新的 TDE 保护器。  在还原时，每个备份需要创建该备份时所用的 TDE 保护器。 可以遵照[使用 PowerShell 轮换透明数据加密保护器](transparent-data-encryption-byok-azure-sql-key-rotation.md)中的说明执行密钥轮换。
- 改回到服务托管的密钥后，请保留 Azure Key Vault 中以前用过的所有密钥。  这可以确保能够使用 Azure Key Vault 中存储的 TDE 保护器还原数据库备份。  通过 Azure Key Vault 创建的 TDE 保护器必须一直保留到使用服务托管的密钥创建所有存储的备份为止。  
- 使用[AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey)创建这些密钥的可恢复副本。
- 若要在出现安全事件期间删除可能已泄露的密钥并避免数据丢失的风险，请遵循[删除可能已泄露的密钥](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)中所述的步骤。

### <a name="guidelines-for-monitoring-the-tde-with-azure-key-vault-configuration"></a>用于监视 TDE 与 Azure Key Vault 配置的准则

如果逻辑 SQL server 在 Azure Key Vault 中无法访问客户托管的 TDE 保护程序, 则该数据库将拒绝所有连接, 并在 Azure 门户中显示为无法访问。  最常见的原因是:
- 意外删除或位于防火墙后面的密钥保管库
- 意外删除、禁用或过期的密钥保管库密钥
- 意外删除了逻辑 SQL Server 实例 AppId
- 已吊销逻辑 SQL Server 实例 AppId 的特定于密钥的权限

 > [!NOTE]
 > 如果在48小时内恢复客户托管的 TDE 保护程序的访问权限, 则数据库将自动进行自我修复, 并自动变为联机状态。  如果数据库由于间歇性网络中断而无法访问, 则无需执行任何操作, 数据库也会自动返回到联机状态。
  
- 有关排除现有配置故障的详细信息, 请参阅[排查 TDE 问题](https://docs.microsoft.com/sql/relational-databases/security/encryption/troubleshoot-tde)

- 若要监视数据库状态并为丢失 TDE 保护程序访问启用警报, 请配置以下 Azure 功能:
    - [Azure 资源运行状况](https://docs.microsoft.com/azure/service-health/resource-health-overview)。 在第一次连接到数据库后, 无法访问的数据库将显示为 "不可用"。
    - [活动日志](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications)当客户管理的密钥保管库中的 TDE 保护程序发生故障时, 会将条目添加到活动日志。  通过为这些事件创建警报, 你可以尽快恢复访问权限。
    - [操作组](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)可以定义为根据你的偏好发送通知和警报, 例如电子邮件/短信/推送/语音、逻辑应用、WEBHOOK、ITSM 或自动化 Runbook。
    

## <a name="high-availability-geo-replication-and-backup--restore"></a>高可用性、异地复制和备份/还原

### <a name="high-availability-and-disaster-recovery"></a>高可用性和灾难恢复

如何使用 Azure Key Vault 配置高可用性取决于数据库和 SQL 数据库服务器的配置，下面针对两种不同的情况提供了建议的配置。  第一种情况是未配置异地冗余的独立数据库或 SQL 数据库服务器。  第二种情况是配置了故障转移组或异地冗余的数据库或 SQL 数据库服务器，其中，必须确保每个异地冗余副本在故障转移组中具有一个本地 Azure Key Vault，以保证异地故障转移能够正常工作。

对于第一种情况，如果要求未配置异地冗余的数据库和 SQL 数据库服务器具有高可用性，我们强烈建议将服务器配置为使用两个不同区域中具有相同密钥材料的两个不同 Key Vault。 若要实现此目的，可以使用与 SQL 数据库服务器共置在同一区域中的主要 Key Vault 来创建一个 TDE 保护器，并将密钥克隆到位于不同 Azure 区域中的 Key Vault，以便在主要 Key Vault 遇到服务中断时，服务器能够访问另一个 Key Vault，同时让数据库保持正常运行。 使用 Backup-AzKeyVaultKey cmdlet 从主要 Key Vault 检索加密格式的密钥，然后使用 Restore-AzKeyVaultKey cmdlet 并指定第二个区域中的 Key Vault。

![单服务器 HA 和无异地灾难恢复](./media/transparent-data-encryption-byok-azure-sql/SingleServer_HA_Config.PNG)

## <a name="how-to-configure-geo-dr-with-azure-key-vault"></a>如何使用 Azure Key Vault 配置异地灾难恢复

若要保持已加密数据库的 TDE 保护器的高可用性，必须基于现有或所需的 SQL 数据库故障转移组或活动异地复制实例配置冗余的 Azure Key Vault。  每个异地复制的服务器需要一个单独的 Key Vault，该 Key Vault 必须与服务器共置在同一 Azure 区域中。 如果一个区域发生的服务中断导致主数据库不可访问，并且触发了故障转移，则辅助数据库能够使用辅助 Key Vault 接管工作。

对于异地复制的 Azure SQL 数据库，需要使用以下 Azure Key Vault 配置：

- 一个主数据库和一个辅助数据库各自使用区域中的某个 Key Vault。
- 至少需要一个辅助数据库，最多支持四个辅助数据库。
- 不支持辅助数据库的辅助数据库（链接）。

以下部分将更详细地介绍设置和配置步骤。

### <a name="azure-key-vault-configuration-steps"></a>Azure Key Vault 配置步骤

- 安装 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)
- 使用 [PowerShell 在 Key Vault 中启用“软删除”属性](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell)（目前无法从 AKV 门户使用此选项 – 但 SQL 需要此选项），在两个不同的区域中创建两个 Azure Key Vault。
- 这两个 Azure Key Vault 必须位于同一 Azure 地理位置中的两个区域，这样才能正常备份和还原密钥。  如果需要将两个密钥保管库定位在不同的地理位置以满足 SQL 异地灾难恢复的要求，请遵循从本地 HSM 导入密钥的 [BYOK 过程](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys)。
- 在第一个 Key Vault 中创建新密钥：  
  - RSA/RSA-HSM 2048 密钥
  - 无过期日期
  - 密钥已启用并有权执行“获取”、“包装密钥”和“解包密钥”操作
- 备份主密钥，并将密钥还原到第二个 Key Vault。  请参阅 [BackupAzureKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey) 和 [Restore-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey)。

### <a name="azure-sql-database-configuration-steps"></a>Azure SQL 数据库配置步骤

根据是要从新的 SQL 部署开始，还是使用现有的 SQL 异地灾难恢复部署，以下配置步骤会有所不同。  我们先概述新部署的配置步骤，然后说明如何将 Azure Key Vault 中存储的 TDE 保护器分配到已建立异地灾难恢复链接的现有部署。

**适用于新部署的步骤**：

- 在前面创建的密钥保管库所在的两个相同区域创建两个 SQL 数据库服务器。
- 选择 SQL 数据库服务器 TDE 窗格，并为每个 SQL 数据库服务器：  
  - 选择同一区域中的 AKV
  - 选择用作 TDE 保护器的密钥 - 每个服务器将使用 TDE 保护器的本地副本。
  - 在门户中执行此操作会创建 SQL 数据库服务器的 [AppID](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)，此标识用于向 SQL 数据库服务器分配密钥保管库的访问权限 - 请不要删除此标识。 可以通过在 Azure Key Vault 中删除相应的权限（而不是对 SQL 数据库服务器执行此类操作），以撤销 SQL 数据库服务器对密钥保管库的访问权限。
- 创建主数据库。
- 遵循[活动异地复制指导](sql-database-geo-replication-overview.md)来完成该方案，此步骤会创建辅助数据库。

![故障转移组和异地灾难恢复](./media/transparent-data-encryption-byok-azure-sql/Geo_DR_Config.PNG)

> [!NOTE]
> 继续在数据库之间建立异地链接之前，必须确保相同的 TDE 保护器在这两个 Key Vault 中存在。

**适用于异地灾难恢复部署中现有 SQL 数据库的步骤：** ：

由于 SQL 数据库服务器已存在并且已分配主数据库和辅助数据库，因此，必须按以下顺序执行配置 Azure Key Vault 的步骤：

- 从托管辅助数据库的 SQL 数据库服务器开始：
  - 分配位于同一区域中的 Key Vault
  - 分配 TDE 保护器
- 现在，请转到托管主数据库的 SQL 数据库服务器：
  - 选择用于辅助数据库的同一 TDE 保护器

![故障转移组和异地灾难恢复](./media/transparent-data-encryption-byok-azure-sql/geo_DR_ex_config.PNG)

> [!NOTE]
> 将 Key Vault 分配到服务器时，必须从辅助服务器开始。  在第二个步骤中，将 Key Vault 分配到主服务器并更新 TDE 保护器；异地灾难恢复链接会继续工作，因为复制的数据库使用的 TDE 保护器此时可供这两个服务器使用。

在使用 Azure Key Vault 中客户管理的密钥为 SQL 数据库异地灾难恢复方案启用 TDE 之前，必须使用同一区域中用于 SQL 数据库异地复制的相同内容创建并维护两个 Azure Key Vault。  具体而言，“相同内容”是指这两个 Key Vault 必须包含相同 TDE 保护器的副本，以便这两个服务器能够访问所有数据库使用的 TDE 保护器。  接下来，必须使这两个 Key Vault 保持同步，这意味着，它们在密钥轮换之后必须包含 TDE 保护器的相同副本，并保留用于日志文件或备份的旧版密钥；TDE 保护器必须保留相同的密钥属性；Key Vault 必须保留 SQL 的相同访问权限。  

遵循[活动异地复制概述](sql-database-geo-replication-overview.md)中的步骤来测试和触发故障转移，应定期执行此操作，以确认保留了 SQL 对这两个 Key Vault 的访问权限。

### <a name="backup-and-restore"></a>备份和还原

使用 Key Vault 中的密钥通过 TDE 加密数据库后，也会使用相同的 TDE 保护器加密所有生成的备份。

若要从 Key Vault 中还原使用 TDE 保护器加密的备份，请确保密钥材料仍在原始保管库中，并使用原始密钥名称。 更改数据库的 TDE 保护器后，数据库的旧备份**不会**更新为使用最新的 TDE 保护器。 因此，我们建议在 Key Vault 中保留所有旧版 TDE 保护器，以便可以还原数据库备份。

如果还原备份可能需要的密钥不再位于其原始 Key Vault 中，则会返回以下错误消息：“目标服务器 `<Servername>` 无权访问在 \<时间戳 #1> 与 \<时间戳 #2> 之间创建的所有 AKV URI。 请在还原所有 AKV URI 之后重试操作。”

若要缓解此问题，请运行 [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) cmdlet，从 Key Vault 中返回已添加到服务器的密钥列表（除非用户已将其删除）。 为了确保可以还原所有备份，请确保备份的目标服务器能够访问所有这些密钥。

```powershell
Get-AzSqlServerKeyVaultKey `
  -ServerName <LogicalServerName> `
  -ResourceGroup <SQLDatabaseResourceGroupName>
```

若要详细了解 SQL 数据库的备份恢复，请参阅[恢复 Azure SQL 数据库](sql-database-recovery-using-backups.md)。 若要详细了解 SQL 数据仓库的备份恢复，请参阅[恢复 Azure SQL 数据仓库](../sql-data-warehouse/backup-and-restore.md)。

有关备份的日志文件的其他注意事项：备份的日志文件仍会使用原始 TDE 加密器保持加密，即使 TDE 保护器已轮换，并且数据库正在使用新的 TDE 保护器。  还原时，需要使用这两个密钥来还原数据库。  如果日志文件使用 Azure Key Vault 中存储的 TDE 保护器，则还原时需要此密钥，即使数据库同时已改用服务托管的 TDE。
