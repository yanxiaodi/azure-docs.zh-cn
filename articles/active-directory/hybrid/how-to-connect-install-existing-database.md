---
title: 使用现有 ADSync 数据库安装 Azure AD Connect | Microsoft Docs
description: 本主题介绍如何使用现有 ADSync 数据库。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: cychua
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/30/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4dc6993586063c9c99a287c51d799b44f921768d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60245140"
---
# <a name="install-azure-ad-connect-using-an-existing-adsync-database"></a>使用现有 ADSync 数据库安装 Azure AD Connect
Azure AD Connect 要求使用 SQL Server 数据库来存储数据。 可以使用随 Azure AD Connect 一起安装的默认 SQL Server 2012 Express LocalDB，也可以使用自己的完整版本 SQL。 以前，当安装 Azure AD Connect 时，始终会创建一个名为 ADSync 的新数据库。 使用 Azure AD Connect 版本 1.1.613.0（或更高版本），可以选择通过将 Azure AD Connect 指向现有的 ADSync 数据库来安装 Azure AD Connect。

## <a name="benefits-of-using-an-existing-adsync-database"></a>使用现有 ADSync 数据库的优势
通过指向现有 ADSync 数据库：

- 除凭据信息外，ADSync 数据库中存储的同步配置（包括自定义同步规则、连接器、筛选和可选功能配置）在安装过程中会自动恢复并使用。 Azure AD Connect 与本地 AD 和 Azure AD 同步更改所使用的凭据已加密，只能由先前的 Azure AD Connect 服务器访问。
- 同时恢复存储在 ADSync 数据库中的所有标识数据（已与连接器空间和 Metaverse 关联）以及同步 Cookie。 新安装的 Azure AD Connect 服务器可以从先前 Azure AD Connect 服务器离开的位置继续同步，无需执行完全同步。

## <a name="scenarios-where-using-an-existing-adsync-database-is-beneficial"></a>受益于使用现有 ADSync 数据库的方案
这些优势在以下方案中非常有用：


- 拥有现有的 Azure AD Connect 部署。 现有 Azure AD Connect 服务器不再工作，但包含 ADSync 数据库的 SQL Server 仍然可用。 可以安装新的 Azure AD Connect 服务器并将其指向现有 ADSync 数据库。 
- 拥有现有的 Azure AD Connect 部署。 包含 ADSync 数据库的 SQL Server 不再运行。 但是拥有数据库的近期备份。 可先将 ADSync 数据库还原到新的 SQL Server。 随后，可以安装新的 Azure AD Connect 服务器并将其指向已还原的 ADSync 数据库。
- 拥有正在使用 LocalDB 的现有 Azure AD Connect 部署。 由于 LocalDB 规定了 10 GB 的限制，你可能希望迁移到完整的 SQL。 可从 LocalDB 备份 ADSync 数据库并将其还原到 SQL Server。 随后，可以重新安装新的 Azure AD Connect 服务器并将其指向已还原的 ADSync 数据库。
- 正在尝试设置暂存服务器，并希望确保其配置与当前活动服务器的配置相匹配。 可以备份 ADSync 数据库并将其还原到其他 SQL Server。 随后，可以重新安装新的 Azure AD Connect 服务器并将其指向已还原的 ADSync 数据库。

## <a name="prerequisite-information"></a>先决条件信息

在开始之前，请注意如下重要注意事项：

- 请务必查看安装 Azure AD Connect 在硬件和其他方面的先决条件，以及安装 Azure AD Connect 所需的帐户和权限。 通过“使用现有数据库”模式安装 Azure AD Connect 所需的权限与“自定义”安装相同。
- 仅完整的 SQL 才支持针对现有 ADSync 数据库部署 Azure AD Connect。 它不支持 SQL Express LocalDB。 如果 LocalDB 中存在要使用的现有 ADSync 数据库，则必须先备份 ADSync 数据库 (LocalDB) 并将其还原至完整的 SQL。 之后才可使用此方法针对还原的数据库部署 Azure AD Connect。
- 用于安装的 Azure AD Connect 版本必须满足以下条件：
    - 1.1.613.0 或更高版本，并且
    - 与上次同 ADSync 数据库一起使用的 Azure AD Connect 版本相同或比之更高。 如果用于安装的 Azure AD Connect 版本高于上次与 ADSync 数据库一起使用时的版本，则可能需要进行完全同步。  如果两个版本之间存在架构或同步规则更改，则完全同步是必需的。 
- 所使用的 ADSync 数据库应包含相对较新的同步状态。 与现有 ADSync 数据库的最后一次同步活动应在最近三周内。
- 通过“使用现有数据库”方法安装 Azure AD Connect 时，不会保留在之前的 Azure AD Connect 服务器上配置的登录方法。 此外，无法在安装过程中配置登录方法。 只能在安装完成后配置登录方法。
- 多个 Azure AD Connect 服务器不能共享相同的 ADSync 数据库。 “使用现有数据库”方法允许在新的 Azure AD Connect 服务器中重用现有 ADSync 数据库。 此方法不支持共享。

## <a name="steps-to-install-azure-ad-connect-with-use-existing-database-mode"></a>通过“使用现有数据库”模式安装 Azure AD Connect 的步骤
1.  将 Azure AD Connect 安装程序 (AzureADConnect.MSI) 下载到 Windows Server。 双击 Azure AD Connect 安装程序，开始安装 Azure AD Connect。
2.  MSI 安装完成后，将启动 Azure AD Connect 向导，进入快速模式安装。 单击“退出”图标关闭屏幕。
![欢迎使用](./media/how-to-connect-install-existing-database/db1.png)
3.  启动新的命令提示符或 PowerShell 会话。 导航到"C:\Program Files\Microsoft Azure Active Directory Connect"的文件夹。 运行命令 .\AzureADConnect.exe /useexistingdatabase，在“使用现有数据库”安装模式下启动 Azure AD Connect 向导。

> [!NOTE]
> 只有当数据库已包含来自早期 Azure AD Connect 安装的数据时，才应使用 **/UseExistingDatabase** 开关。 例如，当从本地数据库移动到完整 SQL Server 数据库时，或者当重建 Azure AD Connect 服务器并且从早期 Azure AD Connect 安装还原了 ADSync 数据库的 SQL 备份时。 如果数据库为空（即不包含前面的 Azure AD Connect 安装的任何数据），请跳过此步骤。

![PowerShell](./media/how-to-connect-install-existing-database/db2.png)
1. 出现“欢迎使用 Azure AD Connect”屏幕。 同意许可条款和隐私声明后，单击“继续”  。
   ![欢迎使用](./media/how-to-connect-install-existing-database/db3.png)
1. 在“安装所需组件”屏幕上，“使用现有 SQL Server”选项已启用   。 指定托管 ADSync 数据库的 SQL Server 的名称。 如果用于托管 ADSync 数据库的 SQL 引擎实例不是 SQL Server 上的默认实例，则必须指定 SQL 引擎实例名称。 此外，如果没有启用 SQL 浏览，还必须指定 SQL 引擎实例端口号。 例如：         
   ![欢迎使用](./media/how-to-connect-install-existing-database/db4.png)           

1. 在“连接到 Azure AD”屏幕上，必须提供 Azure AD 目录的全局管理员凭据  。 建议使用默认 onmicrosoft.com 域中的帐户。 此帐户仅用于在 Azure AD 中创建服务帐户，在向导完成后将不会使用。
   ![“连接”](./media/how-to-connect-install-existing-database/db5.png)
 
1. 在“连接目录”屏幕上，为目录同步配置的现有 AD 林旁边显示有红色十字图标  。 若要同步本地 AD 林中的更改，需要 AD DS 帐户。 Azure AD Connect 向导无法检索存储在 ADSync 数据库中的 AD DS 帐户凭据，因为凭据已加密，只能由先前的 Azure AD Connect 服务器进行解密。 单击“更改凭据”为 AD 林指定 AD DS 帐户  。
   ![Directories](./media/how-to-connect-install-existing-database/db6.png)
 
 
1. 在弹出对话框中，可以 (i) 提供企业管理员凭据，并让 Azure AD Connect 为你创建 AD DS 帐户，或 (ii) 自行创建 AD DS 帐户，并将其凭据提供给 Azure AD Connect。 选择一个选项并提供必要凭据后，单击“确定”关闭弹出对话框  。
   ![欢迎使用](./media/how-to-connect-install-existing-database/db7.png)
 
 
1. 提供凭据后，红色十字图标将被替换为绿色钩号图标。 单击“下一步”。 
   ![欢迎使用](./media/how-to-connect-install-existing-database/db8.png)
 
 
1. 在“准备好配置”屏幕上，单击“安装”   。
   ![欢迎使用](./media/how-to-connect-install-existing-database/db9.png)
 
 
1. 安装完成后，Azure AD Connect 服务器自动启用暂存模式。 建议在禁用暂存模式之前，查看服务器配置和意外更改的挂起导出。 

## <a name="post-installation-tasks"></a>安装后任务
还原使用低于 1.2.65.0 版本的 Azure AD Connect 创建的数据库备份时，暂存服务器会自动选择登录方法“不配置”。  尽管会还原密码哈希同步和密码写回首选项，但随后必须更改登录方法，以便与活动同步服务器的其他生效策略匹配。  如果不完成这些步骤，当此服务器变为活动状态时，用户可能无法登录。  

使用下表来确认是否需要执行其他任何步骤。

|Feature|Steps|
|-----|-----|
|密码哈希同步| 从 Azure AD Connect 版本 1.2.65.0 开始，密码哈希同步和密码写回设置将完全还原。  如果使用早期版本的 Azure AD Connect 还原，请查看这些功能的同步选项设置，以确保它们与活动的同步服务器匹配。  不必要执行其他任何配置步骤。|
|使用 AD FS 进行联合身份验证|Azure 身份验证将继续使用针对活动同步服务器配置的 AD FS 策略。  如果使用 Azure AD Connect 来管理 AD FS 场，则可以选择性地将登录方法更改为 AD FS 联合身份验证，以应对备用服务器变成活动同步实例时的情况。   如果在活动同步服务器上启用了设备选项，请通过运行“配置设备选项”任务，在此服务器上配置这些选项。|
|直通身份验证和桌面单一登录|更新登录方法，以便与活动同步服务器上的配置匹配。  如果在将服务器提升为主服务器之前未遵循此步骤，则直通身份验证以及无缝单一登录将会禁用，并且在未将密码哈希同步用作备用登录选项时，租户可能会被锁定。 另请注意，在暂存模式下启用直通身份验证时，新的身份验证代理将会安装、注册，并以接受登录请求的高可用性代理形式运行。|
|使用 PingFederate 进行联合身份验证|Azure 身份验证将继续使用针对活动同步服务器配置的 PingFederate 策略。  可以选择性地将登录方法更改为 PingFederate，以应对备用服务器变成活动同步实例时的情况。  可将此步骤推迟到需要使用 PingFederate 联合其他域为止。|

## <a name="next-steps"></a>后续步骤

- 安装 Azure AD Connect 后，可以[验证安装并分配许可证](how-to-connect-post-installation.md)。
- 若要了解有关这些功能（在安装过程中已启用）的详细信息，请参阅：[防止意外删除](how-to-connect-sync-feature-prevent-accidental-deletes.md)和 [Azure AD Connect Health](how-to-connect-health-sync.md)。
- 若要了解有关这些常见主题的详细信息，请参阅[计划程序以及如何触发同步](how-to-connect-sync-feature-scheduler.md)。
- 了解有关 [将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)的详细信息。
