---
title: Azure AD Connect：帐户和权限 | Microsoft Docs
description: 本主题介绍使用和创建的帐户以及所需的权限。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: cychua
ms.assetid: b93e595b-354a-479d-85ec-a95553dd9cc2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 09/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8e7bd33d74d9ecf6ebc35981df7255ecc19253c7
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71812600"
---
# <a name="azure-ad-connect-accounts-and-permissions"></a>Azure AD Connect：帐户和权限

## <a name="accounts-used-for-azure-ad-connect"></a>用于 Azure AD Connect 的帐户

![帐户概述](media/reference-connect-accounts-permissions/account5.png)

Azure AD Connect 使用 3 个帐户，将信息从本地或 Windows Server Active Directory 同步到 Azure Active Directory。  这些帐户是：

- AD DS 连接器帐户：用于将信息读/写到 Windows Server Active Directory

- ADSync 服务帐户：用于运行同步服务和访问 SQL 数据库

- Azure AD 连接器帐户：用于将信息写入 Azure AD

除了用于运行 Azure AD Connect 的这三个帐户外，还需要以下其他帐户以安装 Azure AD Connect。  这些是：

- **本地管理员帐户**：将安装 Azure AD Connect 并且在计算机上具有本地管理员权限的管理员。

- **AD DS 企业管理员帐户**：可以选择使用此帐户创建上面的“AD DS 连接器帐户”。

- Azure AD 全局管理员帐户：用于创建 Azure AD 连接器帐户和配置 Azure AD。

- SQL SA 帐户（可选）：用于使用完整版 SQL Server 时创建 ADSync 数据库。  此 SQL Server 对 Azure AD Connect 安装而言可能是本地或远程的。  此帐户可能是企业管理员的帐户。  现在，可以由 SQL 管理员在带外进行数据库预配，然后由具有数据库所有者权限的 Azure AD Connect 管理员完成安装。  有关详细信息，请参阅[使用 SQL 委派的管理员权限安装 Azure AD Connect](how-to-connect-install-sql-delegation.md)

<<<<<<< HEAD
>[!IMPORTANT]
> 在 build 1.4. # # #. # 中，不再支持使用企业管理员帐户或域管理员帐户作为 AD DS 连接器帐户。  如果在指定 "**使用现有帐户**" 时尝试输入企业管理员或域管理员帐户，则会收到错误。
=======
> [!NOTE]
> 支持从 ESAE 管理林（也称为 "Red 林"）管理 Azure AD Connect 中使用的管理帐户。
> 专用管理林允许组织在安全控制比生产环境更强的环境中托管管理帐户、工作站和组。
> 若要了解有关专用管理林的详细信息，请参阅[ESAE 管理林设计方法](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material#esae-administrative-forest-design-approach)
>>>>>>> e683a61b0ed62ae739941410f658a127534e2481

> [!NOTE]
> 在初始安装后不需要全局管理员角色，并且唯一需要的帐户将是**目录同步帐户**角色帐户。 这并不 necssarily 意味着只需删除具有全局管理员角色的帐户。 最好将角色更改为不太强大的角色，因为如果需要重新运行向导，则完全删除帐户可能会导致问题。 通过减少角色的权限，你始终可以重新提升权限（如果必须再次使用 Azure AD Connect 向导）。 

## <a name="installing-azure-ad-connect"></a>正在安装 Azure AD Connect
Azure AD Connect 安装向导提供提供两种不同的路径：

* 在“快速设置”中，此向导需要更多权限。  这样便可以轻松设置配置，而无需创建用户或配置权限。
* 在“自定义设置”中，此向导可提供更多选择和选项。 但是，在某些情况下，需要确保自己具有相应的权限。



## <a name="express-settings-installation"></a>快速设置安装
在“快速设置”中，安装向导要求提供以下内容：

  - AD DS 企业管理员凭据
  - Azure AD 全局管理员凭据

### <a name="ad-ds-enterprise-admin-credentials"></a>AD DS 企业管理员凭据
AD DS 企业管理员帐户用于配置本地 Active Directory。 这些凭据只能在安装期间使用，而不能在安装完成后使用。 由企业管理员而不是域管理员确保可以在所有域中设置 Active Directory 中的权限。

如果从 DirSync 升级，AD DS 企业管理员凭据可用于重置 DirSync 所用帐户的密码。 此外，还需要 Azure AD 全局管理员凭据。

### <a name="azure-ad-global-admin-credentials"></a>Azure AD 全局管理员凭据
这些凭据只能在安装期间使用，而不能在安装完成后使用。 它用于创建 Azure AD 连接器帐户，以便将更改同步到 Azure AD。 该帐户还会在 Azure AD 中启用同步作为功能。

### <a name="ad-ds-connector-account-required-permissions-for-express-settings"></a>AD DS 连接器帐户需要快速设置权限
创建 AD DS 连接器帐户，用于读取和写入 Windows Server AD，如果由快速设置创建，该帐户具有以下权限：

| 权限 | 用途 |
| --- | --- |
| <li>复制目录更改</li><li>复制所有目录更改 |密码哈希同步 |
| 读取/写入所有用户属性 |导入和执行 Exchange 混合部署 |
| 读取/写入所有 iNetOrgPerson 属性 |导入和执行 Exchange 混合部署 |
| 读取/写入所有组属性 |导入和执行 Exchange 混合部署 |
| 读取/写入所有联系人属性 |导入和执行 Exchange 混合部署 |
| 重置密码 |准备启用密码写回 |

### <a name="express-installation-wizard-summary"></a>快速安装向导摘要

![快速安装](./media/reference-connect-accounts-permissions/express.png)

以下是有关快速安装向导页、所收集凭据及其用途的摘要。

| 向导页 | 收集的凭据 | 所需的权限 | 用途 |
| --- | --- | --- | --- |
| 不可用 |运行安装向导的用户 |本地服务器的管理员 |<li>创建用于运行同步服务的 ADSync 服务帐户。 |
| 连接到 Azure AD |Azure AD 目录凭据 |Azure AD 中的全局管理员角色 |<li>在 Azure AD 目录中启用同步。</li>  <li>创建在 Azure AD 中用于持续同步操作的 Azure AD 连接器帐户。</li> |
| 连接到 AD DS |本地 Active Directory 凭据 |Active Directory 中企业管理员 (EA) 组的成员 |<li>在 Active Directory 中创建 AD DS 连接器帐户并向其授予权限。 同步期间，所创建的该帐户用于读取和写入目录信息。</li> |


## <a name="custom-installation-settings"></a>自定义安装设置

通过自定义设置安装，此向导可提供更多选择和选项。 

### <a name="custom-installation-wizard-summary"></a>自定义安装向导摘要

以下是有关自定义安装向导页、所收集凭据及其用途的摘要。

![快速安装](./media/reference-connect-accounts-permissions/customize.png)

| 向导页 | 收集的凭据 | 所需的权限 | 用途 |
| --- | --- | --- | --- |
| 不可用 |运行安装向导的用户 |<li>本地服务器的管理员</li><li>只有 SQL 中的系统管理员 (SA) 才可使用 SQL Server 完整版。</li> |默认情况下，将创建充当同步引擎服务帐户的本地帐户。 只有在管理员未指定特定帐户时才创建该帐户。 |
| 安装同步服务，服务帐户选项 |AD 或本地用户帐户凭据 |用户，权限由安装向导授予 |如果管理员指定了帐户，则此帐户将用作同步服务的服务帐户。 |
| 连接到 Azure AD |Azure AD 目录凭据 |Azure AD 中的全局管理员角色 |<li>在 Azure AD 目录中启用同步。</li>  <li>创建在 Azure AD 中用于持续同步操作的 Azure AD 连接器帐户。</li> |
| 连接目录 |要连接到 Azure AD 的每个林的本地 Active Directory 凭据 |权限随所启用的功能而定，可在“创建 AD DS 连接器帐户”中查找 |在同步期间，将使用此帐户读取和写入目录信息。 |
| AD FS 服务器 |对于列表中的每个服务器，如果运行向导的用户的登录凭据权限不足，因而无法连接，则向导会收集凭据 |域管理员 |安装和配置 AD FS 服务器角色。 |
| Web 应用程序代理服务器 |对于列表中的每个服务器，如果运行向导的用户的登录凭据权限不足，因而无法连接，则向导会收集凭据 |目标计算机上的本地管理员 |安装和配置 WAP 服务器角色。 |
| 代理信任凭据 |联合身份验证服务信任凭据（代理用来注册 FS 信任证书的凭据） |作为 AD FS 服务器本地管理员的域帐户 |初始注册 FS-WAP 信任证书。 |
| “AD FS 服务帐户”页上的“使用域用户帐户选项” |AD 用户帐户凭据 |域用户 |提供了其凭据的 Azure AD 用户帐户用作 AD FS 服务的登录帐户。 |

### <a name="create-the-ad-ds-connector-account"></a>创建 AD DS 连接器帐户

>[!IMPORTANT]
>内部版本 1.1.880.0（发布于 2018 年 8 月）中引入了名为 ADSyncConfig.psm1 的新 PowerShell 模块，其中包括有助于为 Azure AD DS 连接器帐户配置正确 Active Directory 权限的 cmdlet 集合。
>
>有关详细信息，请参阅 [Azure AD Connect：配置 AD DS 连接器帐户权限](how-to-connect-configure-ad-ds-connector-account.md)

“连接目录”页上指定的帐户必须在安装之前存在于 Active Directory 中。  Azure AD Connect 版本 1.1.524.0 及更高版本提供了相应选项，让 Azure AD Connect 向导创建用于连接 Active Directory 的 AD DS 连接器帐户。  

还必须向它授予所需的权限。 安装向导不会验证权限，任何问题只能在同步期间发现。

需要哪些权限取决于启用的可选功能。 如果有多个域，则必须对林中的所有域授予权限。 如果某项功能未启动，则默认的**域用户**权限已足够。

| 功能 | 权限 |
| --- | --- |
| ms DS ConsistencyGuid 功能 |对[设计概念 - 使用 ms-DS-ConsistencyGuid 作为 sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) 中所述的 ms-DS-ConsistencyGuid 属性的写入权限。 | 
| 密码哈希同步 |<li>复制目录更改</li>  <li>复制所有目录更改 |
| Exchange 混合部署 |针对用户、组和联系人的属性的写入权限，详见[Exchange 混合写回](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback)。 |
| Exchange 邮件公共文件夹 |对 [Exchange 邮件公用文件夹](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder)中所述的公用文件夹属性的读取权限。 | 
| 密码写回 |针对用户的属性的写入权限，详见[密码管理入门](../authentication/howto-sspr-writeback.md)。 |
| 设备写回 |通过 PowerShell 脚本授予的权限，详见[设备写回](how-to-connect-device-writeback.md)。 |
| 组写回 |允许你将 **Office 365 组**写回到已安装 Exchange 的林中。  有关详细信息，请参阅[组写回](how-to-connect-preview.md#group-writeback)。|

## <a name="upgrade"></a>升级
从 Azure AD Connect 的一个版本升级到新版本时，需要拥有以下权限：

>[!IMPORTANT]
>从版本 1.1.484 开始，Azure AD Connect 引入了一个回归 bug，导致需要 sysadmin 权限才能升级 SQL 数据库。  在内部版本 1.1.647 中解决了此 bug。  若要升级到此版本，需要 sysadmin 权限。  Dbo 权限是不够的。  如果尝试在没有 sysadmin 权限的情况下升级 Azure AD Connect，升级将失败，之后 Azure AD Connect 将不再正常工作。  Microsoft 已意识到此问题，并在努力更正此问题。


| 主体 | 所需的权限 | 用途 |
| --- | --- | --- |
| 运行安装向导的用户 |本地服务器的管理员 |更新二进制文件 |
| 运行安装向导的用户 |ADSyncAdmins 的成员 |对同步规则和其他配置进行更改。 |
| 运行安装向导的用户 |如果使用完整 SQL 服务器：需有同步引擎数据库的 DBO 权限（或类似权限） |进行数据库级别的更改，例如使用新列更新表。 |

## <a name="more-about-the-created-accounts"></a>有关所创建帐户的详细信息
### <a name="ad-ds-connector-account"></a>AD DS 连接器帐户
如果使用快速设置，会在 Active Directory 中创建用于同步的帐户。 创建的帐户位于用户容器的林根域中，其名称前缀为 **MSOL_** 。 该帐户带有永不过期的长复杂密码。 如果域中有密码策略，请确保允许此帐户使用长密码和复杂密码。

![AD 帐户](./media/reference-connect-accounts-permissions/adsyncserviceaccount.png)

如果使用自定义设置，则需负责在开始安装之前创建帐户。  请参阅“创建 AD DS 连接器帐户”。

### <a name="adsync-service-account"></a>ADSync 服务帐户
同步服务可在不同帐户下运行。 它可在**虚拟服务帐户** (VSA)、**组托管服务帐户** (gMSA/sMSA) 或常规用户帐户下运行。 2017 年 4 月版本的 Connect 的支持选项已更改（若进行全新安装）。 如果从早期版本的 Azure AD Connect 升级，这些附加选项将不可用。

| 帐户的类型 | 安装选项 | 描述 |
| --- | --- | --- |
| [虚拟服务帐户](#virtual-service-account) | 快速和自定义，2017 年 4 月版及更高版本 | 此选项适用于所有快速安装，在域控制器上的安装除外。 对于自定义安装，除非使用了其他选项，否则它便是默认选项。 |
| [组托管服务帐户](#group-managed-service-account) | 自定义，2017 年 4 月版及更高版本 | 如果使用远程 SQL Server，则建议使用组托管服务帐户。 |
| [用户帐户](#user-account) | 快速和自定义，2017 年 4 月版及更高版本 | 只有在 Windows Server 2008 以及域控制器上安装时才会创建前缀为 AAD_ 的用户帐户。 |
| [用户帐户](#user-account) | 快速和自定义，2017 年 3 月版及更早版本 | 在安装期间创建前缀为 AAD_ 的本地帐户。 使用自定义安装时，可指定另一个帐户。 |

如果将 Connect 与 2017 年 3 月的版本或更早版本一起使用，则不应重置服务帐户中的密码，因为出于安全原因，Windows 会销毁加密密钥。 无法在不重装 Azure AD Connect 的情况下将帐户更改为任何其他帐户。 如果升级到 2017 年 4 月的版本或更高版本，则支持更改服务帐户中的密码，但不能更改使用的帐户。

> [!Important]
> 只能在首次安装时设置服务帐户。 完成安装后，不支持更改服务帐户。

下面是针对同步服务帐户的默认、建议和支持的选项表。

图例：

- **粗体**表示默认选项，并且在大多数情况下是建议选项。
- *斜体*表示建议选项（当该选项不是默认选项时）。
- 2008 - 在 Windows Server 2008 上安装时的默认选项
- 非粗体 - 支持的选项
- 本地帐户 - 服务器上的本地用户帐户
- 域帐户 - 域用户帐户
- sMSA - [独立托管服务帐户](https://technet.microsoft.com/library/dd548356.aspx)
- gMSA - [组托管服务帐户](https://technet.microsoft.com/library/hh831782.aspx)

| | LocalDB</br>Express | LocalDB/LocalSQL</br>“自定义” | 远程 SQL</br>自定义 |
| --- | --- | --- | --- |
| **独立/工作组计算机** | 不支持 | **VSA**</br>本地帐户 (2008)</br>本地帐户 |  不支持 |
| **加入域的计算机** | **VSA**</br>本地帐户 (2008) | **VSA**</br>本地帐户 (2008)</br>本地帐户</br>域帐户</br>sMSA、gMSA | **gMSA**</br>域帐户 |
| **域控制器** | **域帐户** | *gMSA*</br>**域帐户**</br>sMSA| *gMSA*</br>**域帐户**|

#### <a name="virtual-service-account"></a>虚拟服务帐户
虚拟服务帐户是一种特殊类型的帐户，它没有密码且由 Windows 管理。

![VSA](./media/reference-connect-accounts-permissions/aadsyncvsa.png)

VSA 旨在当同步引擎和 SQL 位于同一服务器上时使用。 如果使用远程 SQL，则建议改用组托管服务帐户。

此功能需要 Windows Server 2008 R2 或更高版本。 如果在 Windows Server 2008 上安装 Azure AD Connect，则安装将回退改用[用户帐户](#user-account)。

#### <a name="group-managed-service-account"></a>组托管服务帐户
如果使用远程 SQL Server，则建议使用**组托管服务帐户**。 若要详细了解如何为组托管服务帐户准备 Active Directory ，请参阅 [Group Managed Service Accounts Overview](https://technet.microsoft.com/library/hh831782.aspx)（组托管服务帐户概述）。

若要使用此选项，请[安装所需的组件](how-to-connect-install-custom.md#install-required-components)页上选择“使用现有的服务帐户”，然后选择“托管服务帐户”。  
![VSA](./media/reference-connect-accounts-permissions/serviceaccount.png)  
还支持使用[独立托管服务帐户](https://technet.microsoft.com/library/dd548356.aspx)。 但是，这些帐户只能在本地计算机上使用，因此使用这些帐户相对默认虚拟服务帐户而言并没有好处。

此功能需要 Windows Server 2012 或更高版本。 如果需要使用早期版本的操作系统和远程 SQL，则必须使用[用户帐户](#user-account)。

#### <a name="user-account"></a>用户帐户
本地服务帐户由安装向导创建（除非在自定义设置指定了要使用的帐户）。 该帐户具有 **AAD_** 前缀，可用作实际同步服务的运行帐户。 如果在域控制器上安装 Azure AD Connect，则会在该域中创建帐户。 在以下情况下，**AAD_** 服务帐户必须位于域中：
   - 使用运行 SQL Server 的远程服务器
   - 使用需要身份验证的代理

![同步服务帐户](./media/reference-connect-accounts-permissions/syncserviceaccount.png)

该帐户带有永不过期的长复杂密码。

此帐户用于以安全方式存储其他帐户的密码。 其他这些帐户密码以加密形式存储在数据库中。 通过使用 Windows 数据保护 API (DPAPI) 的密钥加密服务来保护加密密钥的私钥。

如果使用完整的 SQL Server，服务帐户将是为同步引擎创建的数据库的 DBO。 如果使用其他权限，服务无法按预期工作。 此外会创建 SQL 登录名。

该帐户也会获取对文件、注册表项和与同步引擎相关的其他对象的权限。

### <a name="azure-ad-connector-account"></a>Azure AD 连接器帐户
将在 Azure AD 中创建帐户供同步服务使用。 可以根据显示名称来识别此帐户。

![AD 帐户](./media/reference-connect-accounts-permissions/aadsyncserviceaccount2.png)

使用该帐户的服务器名称可以根据用户名的第二个部分来识别。 在上图中，服务器名称为 DC1。 如果部署了暂存服务器，每个服务器都有自身的帐户。

该帐户带有永不过期的长复杂密码。 系统为其授予了特殊角色“目录同步帐户”，该角色仅可执行目录同步任务。 此特殊内置角色不能在 Azure AD Connect 向导之外授予。 Azure 门户显示具有“用户”角色的此帐户。

Azure AD 将同步服务帐户数目限制为 20 个。 若要在 Azure AD 中获取现有 Azure AD 服务帐户的列表，请运行以下 Azure AD PowerShell cmdlet：`Get-AzureADDirectoryRole | where {$_.DisplayName -eq "Directory Synchronization Accounts"} | Get-AzureADDirectoryRoleMember`

若要删除未使用的 Azure AD 服务帐户，请运行以下 Azure AD PowerShell cmdlet：`Remove-AzureADUser -ObjectId <ObjectId-of-the-account-you-wish-to-remove>`

>[!NOTE]
>在可以使用上述 PowerShell 命令之前，你将需要安装[Azure Active Directory PowerShell For Graph 模块](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0#installing-the-azure-ad-module)，并使用[AzureAD](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0)连接到 Azure AD 实例

有关如何管理或重置 Azure AD Connect 帐户密码的更多信息，请参阅[管理 Azure AD Connect 帐户](how-to-connect-azureadaccount.md)

## <a name="related-documentation"></a>相关文档
如果尚未阅读文档了解如何[将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)，请查看下表获取相关主题的链接。

|主题 |链接|  
| --- | --- |
|下载 Azure AD Connect | [下载 Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771)|
|使用快速设置安装 | [Azure AD Connect 的快速安装](how-to-connect-install-express.md)|
|使用自定义设置安装 | [Azure AD Connect 的自定义安装](./how-to-connect-install-custom.md)|
|从 DirSync 升级 | [从 Azure AD 同步工具 (DirSync) 升级](how-to-dirsync-upgrade-get-started.md)|
|安装后 | [验证安装并分配许可证](how-to-connect-post-installation.md)|

## <a name="next-steps"></a>后续步骤
了解有关 [将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)的详细信息。
