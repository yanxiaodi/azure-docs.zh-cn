---
title: Azure AD Connect 同步服务功能和配置 | Microsoft Docs
description: 介绍 Azure AD Connect 同步服务的服务端功能。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 213aab20-0a61-434a-9545-c4637628da81
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/25/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: be67a6f287e2d6e77070928cbe12542857696011
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60347528"
---
# <a name="azure-ad-connect-sync-service-features"></a>Azure AD Connect 同步服务功能

Azure AD Connect 的同步功能有两个组件：

* 名为 **Azure AD Connect 同步**的本地组件，也称为**同步引擎**。
* 驻留在 Azure AD 中的服务，也称为 **Azure AD Connect 同步服务**

本主题说明 **Azure AD Connect 同步服务**的以下功能如何工作，以及如何使用 Windows PowerShell 来配置这些功能。

可以使用[用于 Windows PowerShell 的 Azure Active Directory 模块](https://aka.ms/aadposh)来配置这些设置。 请从 Azure AD Connect 单独下载并安装此模块。 [2016 年 3 月版（内部版本 9031.1）](https://social.technet.microsoft.com/wiki/contents/articles/28552.microsoft-azure-active-directory-powershell-module-version-release-history.aspx#Version_9031_1)中引入了本主题所述的 cmdlet。 如果没有本主题中所述的 cmdlet，或者它们不生成相同的结果，请确保运行最新的版本。

若要查看 Azure AD 目录中的配置，请运行 `Get-MsolDirSyncFeatures`。  
![Get-MsolDirSyncFeatures 结果](./media/how-to-connect-syncservice-features/getmsoldirsyncfeatures.png)

其中的许多设置只能由 Azure AD Connect 更改。

`Set-MsolDirSyncFeature` 可以配置以下设置：

| DirSyncFeature | 注释 |
| --- | --- |
| [EnableSoftMatchOnUpn](#userprincipalname-soft-match) |除了允许对象加入主 SMTP 地址，还允许对象加入 userPrincipalName。 |
| [SynchronizeUpnForManagedUsers](#synchronize-userprincipalname-updates) |允许同步引擎更新托管/许可（非联合）用户的 userPrincipalName 属性。 |

启用某个功能后，无法再次将其禁用。

> [!NOTE]
> 从 2016 年 8 月 24 日起，为新的 Azure AD 目录默认启用*重复属性复原*功能。 今后还会针对此日期之前创建的目录推出并启用此功能。 在为目录启用此功能之前的短时间内，用户会收到电子邮件通知。
> 
> 

以下设置是由 Azure AD Connect 配置，无法通过 `Set-MsolDirSyncFeature` 修改：

| DirSyncFeature | 注释 |
| --- | --- |
| DeviceWriteback |[Azure AD Connect：启用设备写回](how-to-connect-device-writeback.md) |
| DirectoryExtensions |[Azure AD Connect 同步：目录扩展](how-to-connect-sync-feature-directory-extensions.md) |
| [DuplicateProxyAddressResiliency<br/>DuplicateUPNResiliency](#duplicate-attribute-resiliency) |如果某些属性是另一个对象的副本而不会在导出期间导致整个对象失败，则允许隔离该属性。 |
| 密码哈希同步 |[使用 Azure AD Connect 同步实现密码哈希同步](how-to-connect-password-hash-synchronization.md) |
|传递身份验证|[使用 Azure Active Directory 直通身份验证的用户登录](how-to-connect-pta.md)|
| UnifiedGroupWriteback |[预览：组写回](how-to-connect-preview.md#group-writeback) |
| UserWriteback |目前不支持。 |

## <a name="duplicate-attribute-resiliency"></a>重复属性复原

将属性“隔离”并分配临时值，而不是使预配包含重复 UPNs/proxyAddresses 的对象失败。 解决冲突时，自动将临时 UPN 更改为适当的值。 有关详细信息，请参阅[标识同步和重复属性复原](how-to-connect-syncservice-duplicate-attribute-resiliency.md)。

## <a name="userprincipalname-soft-match"></a>UserPrincipalName 软匹配

启用此功能后，除了始终启用的[主 SMTP 地址](https://support.microsoft.com/kb/2641663)外，还将为 UPN 启用软匹配。 软匹配功能用于将 Azure AD 中的现有云用户与本地用户进行匹配。

如果需要将本地 AD 帐户与云中创建的现有帐户进行匹配，但未使用 Exchange Online，则此功能非常有用。 在此情况下，通常没有必要在云中设置 SMTP 属性。

在新建的 Azure AD 目录中，默认已打开此功能。 可以运行以下命令查看是否已启用此功能：  

```powershell
Get-MsolDirSyncFeatures -Feature EnableSoftMatchOnUpn
```

如果没有为 Azure AD 目录启用此功能，可以运行以下命令来启用它：  

```powershell
Set-MsolDirSyncFeature -Feature EnableSoftMatchOnUpn -Enable $true
```

## <a name="synchronize-userprincipalname-updates"></a>同步 userPrincipalName 更新

在过去，除非以下两个条件都成立，否则会阻止在本地使用同步服务对 UserPrincipalName 属性进行更新：

* 用户受管理（非联合）。
* 没有为用户分配许可证。

有关详细信息，请参阅 [Office 365、Azure 或 Intune 中的用户名与本地 UPN 或备用登录 ID 不匹配](https://support.microsoft.com/kb/2523192)。

如果启用此功能，同步引擎更新 userPrincipalName 在本地更改，并且使用密码哈希同步或直通身份验证。 如果使用联合身份验证，此功能不受支持。

在新建的 Azure AD 目录中，默认已打开此功能。 可以运行以下命令查看是否已启用此功能：  

```powershell
Get-MsolDirSyncFeatures -Feature SynchronizeUpnForManagedUsers
```

如果没有为 Azure AD 目录启用此功能，可以运行以下命令来启用它：  

```powershell
Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $true
```

启用此功能后，现有的 userPrincipalName 值将保持不变。 下一次在本地更改 userPrincipalName 属性时，对用户进行正常的增量同步会更新 UPN。  

## <a name="see-also"></a>另请参阅

* [Azure AD Connect 同步](how-to-connect-sync-whatis.md)
* [将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)。
