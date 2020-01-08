---
title: Azure AD Connect：从 DirSync 升级 | Microsoft Docs
description: 了解如何从 DirSync 升级到 Azure AD Connect。 本文介绍从 DirSync 升级到 Azure AD Connect 的步骤。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: baf52da7-76a8-44c9-8e72-33245790001c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f2d9a7c8cfbfc4fb56ff8fba3c65ae9a7925830
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60348523"
---
# <a name="azure-ad-connect-upgrade-from-dirsync"></a>Azure AD Connect：从 DirSync 升级
Azure AD Connect 是 DirSync 的后继产品。 将在本主题中了解可从 DirSync 升级的方式。 这些步骤不适用于从另一个版本的 Azure AD Connect 或从 Azure AD Sync 升级。

开始安装 Azure AD Connect 之前，确保[下载 Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771)，完成 [Azure AD Connect：硬件和先决条件](how-to-connect-install-prerequisites.md)中的预备步骤。 特别是，请阅读以下内容，因为其中描述了与 DirSync 不同的方面：

* 必需的 .NET 和 PowerShell 版本。 服务器上所需要的版本比 DirSync 需要的版本新。
* 代理服务器配置。 如果使用代理服务器连接 Internet，则该设置必须在升级之前配置。 DirSync 始终使用安装它时为用户配置的代理服务器，但是 Azure AD Connect 使用计算机设置。
* 需要在代理服务器中打开的 URL。 对于基本的应用场景，DirSync 也支持这些 URL，要求是一样的。 如果要使用 Azure AD Connect 的一些新功能，则必须打开一些新 URL。

> [!NOTE]
> 启用新的 Azure AD Connect 服务器并开始将更改同步到 Azure AD 以后，不得通过回退来使用 DirSync 或 Azure AD Sync。不支持从 Azure AD Connect 降级到旧客户端（包括 DirSync 和 Azure AD Sync），那样可能会导致各种问题，例如数据在 Azure AD 中丢失。

如果不是从 DirSync 升级，请参阅相关文档了解其他方案。

## <a name="upgrade-from-dirsync"></a>从 DirSync 升级
根据当前的 DirSync 部署，可以使用不同的升级选项。 如果预期的升级时间少于 3 小时，建议执行就地升级。 如果预期的升级时间超过 3 小时，建议在另一台服务器上进行并行部署。 如果对象数目超过 50,000 个，预计需要 3 个多小时才能完成升级。

| 场景 |
| --- |
| [就地升级](#in-place-upgrade) |
| [并行部署](#parallel-deployment) |

> [!NOTE]
> 规划从 DirSync 升级到 Azure AD Connect 时，在升级之前请勿自行卸载 DirSync。 Azure AD Connect 将读取和迁移 DirSync 的配置，并在检查服务器之后卸载 DirSync。

**就地升级**  
向导会显示完成升级的预期所需时间。 这个估计是基于需要 3 小时才能完成包含 50,000 个对象（用户、联系人和组）的数据库的升级的假设。 如果数据库中的对象数少于 50,000 个，则 Azure AD Connect 建议就地升级。 如果确定继续，则在升级期间自动应用当前设置，并且服务器自动恢复活动的同步。

如果想要执行配置迁移并执行并行部署，可以拒绝就地升级建议。 例如，可以借机刷新硬件和操作系统。 有关详细信息，请参阅[并行部署](#parallel-deployment)部分。

**并行部署**  
如果对象数超过 50,000 个，则建议执行并行部署。 此部署可以让用户避免遇到操作延迟。 Azure AD Connect 安装程序将尝试预估升级时的停机时间，但是，如果曾升级过 DirSync，那么，自己的经验可能会提供最佳指导。

### <a name="supported-dirsync-configurations-to-be-upgraded"></a>要升级的受支持 DirSync 配置
升级的 DirSync 支持以下配置更改：

* 域和 OU 筛选
* 备用 ID (UPN)
* 密码同步与 Exchange 混合设置
* 林/域与 Azure AD 设置
* 基于用户属性进行筛选

以下更改无法升级。 如果具有此配置，则会阻止升级：

* 不支持的 DirSync 更改，例如删除属性和使用自定义扩展 DLL

![已阻止升级](./media/how-to-dirsync-upgrade-get-started/analysisblocked.png)

在这些情况下，建议在[过渡模式](how-to-connect-sync-staging-server.md)下安装新的 Azure AD Connect 服务器，并确认旧的 DirSync 及新的 Azure AD Connect 配置。 使用自定义配置重新应用所有更改，如 [Azure AD Connect 同步自定义配置](how-to-connect-sync-whatis.md)中所述。

无法检索且未迁移 DirSync 用于服务帐户的密码。 这些密码会在升级期间重置。

### <a name="high-level-steps-for-upgrading-from-dirsync-to-azure-ad-connect"></a>从 DirSync 升级到 Azure AD Connect 的高级步骤
1. 欢迎使用 Azure AD Connect
2. 当前 DirSync 配置的分析
3. 收集 Azure AD 全局管理员密码
4. 收集企业管理员帐户的凭据（仅在 Azure AD Connect 安装期间使用）
5. 安装 Azure AD Connect
   * 卸载 DirSync（或暂时禁用）
   * 安装 Azure AD Connect
   * （可选）开始同步

发生以下情况时，需要执行其他步骤：

* 当前正在使用完全版 SQL Server - 本地或远程
* 要同步的对象超过 50,000 个

## <a name="in-place-upgrade"></a>就地升级
1. 启动 Azure AD Connect 安装程序 (MSI)。
2. 查看并同意许可条款和隐私声明。  
   ![欢迎使用 Azure](./media/how-to-dirsync-upgrade-get-started/Welcome.png)
3. 单击“下一步”开始分析现有的 DirSync 安装。  
   ![分析现有的目录同步安装](./media/how-to-dirsync-upgrade-get-started/Analyze.png)
4. 完成分析后，可以看到操作建议。  
   * 如果使用 SQL Server Express 并且对象数少于 50,000 个，则会显示以下屏幕：  
     ![分析完成，已准备好从 DirSync 升级](./media/how-to-dirsync-upgrade-get-started/AnalysisReady.png)
   * 如果使用完整的 SQL Server for DirSync，则会看到以下页面：  
     ![分析完成，已准备好从 DirSync 升级](./media/how-to-dirsync-upgrade-get-started/AnalysisReadyFullSQL.png)  
     系统会显示有关 DirSync 使用的现有 SQL Server 数据库服务器的信息。 如果需要，请做相应的调整。 单击“下一步”继续安装。 
   * 如果有超过 50,000 个对象，则会看到以下屏幕：  
     ![分析完成，已准备好从 DirSync 升级](./media/how-to-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)  
     若要继续进行就地升级，请单击以下消息旁的复选框：“继续在此计算机上升级 DirSync”。 
     要改为进行[并行部署](#parallel-deployment)，请导出 DirSync 配置设置，将该配置迁移到新的服务器。
5. 提供当前用于连接 Azure AD 的帐户的密码。 这必须是 DirSync 当前使用的帐户。  
   ![输入 Azure AD 凭据](./media/how-to-dirsync-upgrade-get-started/ConnectToAzureAD.png)  
   如果收到错误消息并且出现了连接问题，请参阅[排查连接问题](tshoot-connect-connectivity.md)。
6. 提供 Active Directory 的企业管理员帐户。  
   ![输入 ADDS 凭据](./media/how-to-dirsync-upgrade-get-started/ConnectToADDS.png)
7. 现在可以开始配置。 单击“升级”  后，会卸载 DirSync 并配置 Azure AD Connect，并开始同步。  
   ![已准备好配置](./media/how-to-dirsync-upgrade-get-started/ReadyToConfigure.png)
8. 安装完成后，请注销并再次登录到 Windows，即可使用同步服务管理器或同步规则编辑器，或者尝试进行其他任何配置更改。

## <a name="parallel-deployment"></a>并行部署
### <a name="export-the-dirsync-configuration"></a>导出 DirSync 配置
**对象数超过 50,000 时执行并行部署**

如果对象数超过 50,000 个，Azure AD Connect 安装程序会建议执行并行部署。

将显示如下屏幕：  
![分析已完成](./media/how-to-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)

如果想继续进行并行部署，需要执行以下步骤：

* 单击“导出设置”按钮。  在单独的服务器上安装 Azure AD Connect 时，会将当前 DirSync 中的这些设置迁移到新的 Azure AD Connect 安装位置。

成功导出设置后，可以退出 DirSync 服务器上的 Azure AD Connect 向导。 继续执行下一步，在不同的服务器上安装 Azure AD Connect

**对象数少于 50,000 时执行并行部署**

如果对象数少于 50,000 个，但仍然想要执行并行部署，请执行以下操作：

1. 运行 Azure AD Connect 安装程序 (MSI)。
2. 看到“欢迎使用 Azure AD Connect”屏幕时，请单击窗口右上角的“X”退出安装向导。 
3. 打开命令提示符。
4. 从 Azure AD Connect 的安装位置（默认值：C:\Program Files\Microsoft Azure Active Directory Connect）执行以下命令：`AzureADConnect.exe /ForceExport`。
5. 单击“导出设置”按钮。  在单独的服务器上安装 Azure AD Connect 时，会将当前 DirSync 中的这些设置迁移到新的 Azure AD Connect 安装位置。

![分析已完成](./media/how-to-dirsync-upgrade-get-started/forceexport.png)

成功导出设置后，可以退出 DirSync 服务器上的 Azure AD Connect 向导。 继续执行下一步，在不同的服务器上安装 Azure AD Connect。

### <a name="install-azure-ad-connect-on-separate-server"></a>在不同的服务器上安装 Azure AD Connect
在新的服务器上安装 Azure AD Connect 时，假设要执行 Azure AD Connect 的全新安装。 由于想要使用 DirSync 配置，因此还要执行一些额外的步骤：

1. 运行 Azure AD Connect 安装程序 (MSI)。
2. 看到“欢迎使用 Azure AD Connect”屏幕时，请单击窗口右上角的“X”退出安装向导。 
3. 打开命令提示符。
4. 从 Azure AD Connect 的安装位置（默认值：C:\Program Files\Microsoft Azure Active Directory Connect）执行以下命令：`AzureADConnect.exe /migrate`。
   Azure AD Connect 安装向导会启动并显示以下屏幕：  
   ![输入 Azure AD 凭据](./media/how-to-dirsync-upgrade-get-started/ImportSettings.png)
5. 选择从 DirSync 安装中导出的设置文件。
6. 配置任何高级选项，包括：
   * Azure AD Connect 的自定义安装位置。
   * 现有 SQL Server 实例（默认值：Azure AD Connect 将安装 SQL Server 2012 Express）。 请不要使用与 DirSync 服务器相同的数据库实例。
   * 用于连接 SQL Server 的服务帐户（如果 SQL Server 数据库位于远程，则此帐户必须是域服务帐户）。
     可以在此屏幕上看到以下选项：  
     ![输入 Azure AD 凭据](./media/how-to-dirsync-upgrade-get-started/advancedsettings.png)
7. 单击“下一步”。 
8. 在“已准备好配置”页上，保留选中“配置完成后立即开始同步过程”。   服务器当前为[过渡模式](how-to-connect-sync-staging-server.md)，更改不会导出到 Azure AD。
9. 单击“安装”  。
10. 安装完成后，请注销并再次登录到 Windows，即可使用同步服务管理器或同步规则编辑器，或者尝试进行其他任何配置更改。

> [!NOTE]
> 开始 Windows Server Active Directory 和 Azure Active Directory 之间的同步，但不会将任何更改导出到 Azure AD。 每次只能有一个同步工具在主动导出更改。 此状态称为[过渡模式](how-to-connect-sync-staging-server.md)。

### <a name="verify-that-azure-ad-connect-is-ready-to-begin-synchronization"></a>验证 Azure AD Connect 是否已准备好开始同步
若要验证 Azure AD Connect 是否已准备好接管 DirSync，需要从“开始”菜单的“Azure AD Connect”组中，打开“同步服务管理器”。  

在应用程序中，转到“操作”选项卡。  在此选项卡上，确认以下操作已完成：

* 在 AD 连接器上导入
* 在 Azure AD 连接器上导入
* 在 AD 连接器上执行完全同步
* 在 Azure AD 连接器上执行完全同步

![导入和同步已完成](./media/how-to-dirsync-upgrade-get-started/importsynccompleted.png)

检查这些操作的结果，并确保没有任何错误。

如果想查看并检查即将导出到 Azure AD 的更改，请阅读有关如何在[过渡模式](how-to-connect-sync-staging-server.md)下验证配置的主题。 进行所需的配置更改，直到没有任何意外的错误。

完成这些步骤并对结果满意之后，就可以从 DirSync 切换到 Azure AD。

### <a name="uninstall-dirsync-old-server"></a>卸载 DirSync（旧服务器）
* 在“程序和功能”中查找“Windows Azure Active Directory 同步工具”  
* 卸载“Windows Azure Active Directory 同步工具” 
* 最长可能需要 15 分钟才能完成卸载。

如果想要稍后卸载 DirSync，也可以暂时关闭服务器或禁用服务。 如果出现问题，则此方法允许重新启用服务。 但是，预期下一步不会出现问题，所以无需重新启用。

卸载或禁用 DirSync 之后，没有任何活动的服务器导出到 Azure AD。 必须完成下一步启用 Azure AD Connect 的操作，才能将本地 Active Directory 中的任何更改继续同步到 Azure AD。

### <a name="enable-azure-ad-connect-new-server"></a>启用 Azure AD Connect（新服务器）
安装之后，重新打开 Azure AD Connect 时可以进行其他配置更改。 从“开始”菜单或桌面快捷方式启动 **Azure AD Connect** 。 请确保不要尝试重新运行安装 MSI。

应该看到以下内容：  
![其他任务](./media/how-to-dirsync-upgrade-get-started/AdditionalTasks.png)

* 选择“配置过渡模式”。 
* 取消选中“已启用过渡模式”复选框可以关闭过渡。 

![输入 Azure AD 凭据](./media/how-to-dirsync-upgrade-get-started/configurestaging.png)

* 单击“下一步”按钮。 
* 在确认页面上，单击“安装”按钮。 

Azure AD Connect 现在为活动服务器，不得切换回去使用现有的 DirSync 服务器。

## <a name="next-steps"></a>后续步骤
安装 Azure AD Connect 后，可以[验证安装并分配许可证](how-to-connect-post-installation.md)。

若要了解有关这些新功能（在安装过程中已启用）的详细信息，请参阅：[自动升级](how-to-connect-install-automatic-upgrade.md)、[防止意外删除](how-to-connect-sync-feature-prevent-accidental-deletes.md)和 [Azure AD Connect Health](how-to-connect-health-sync.md)。

若要了解有关这些常见主题的详细信息，请参阅[计划程序以及如何触发同步](how-to-connect-sync-feature-scheduler.md)。

了解有关 [将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)的详细信息。
