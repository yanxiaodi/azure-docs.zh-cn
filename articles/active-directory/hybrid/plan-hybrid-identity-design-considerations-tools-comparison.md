---
title: 混合标识：目录集成工具比较 | Microsoft Docs
description: 本页将提供针对可用于目录集成的各种目录集成工具进行比较的综合表。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 1e62a4bd-4d55-4609-895e-70131dedbf52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: aed01ea11c1f53cb090d9c2e65ee23f521575649
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60456911"
---
# <a name="hybrid-identity-directory-integration-tools-comparison"></a>混合标识目录集成工具比较
过去数年以来，集成工具已得到发展和演进。  本文档旨在帮助提供这些工具的合并视图，并比较每个工具提供的功能。

<!-- The hardcoded link is a workaround for campaign ids not working in acom links-->

> [!NOTE]
> Azure AD Connect 整合了以前作为 DirSync 和 AAD Sync 发布的组件和功能。这些工具不再单独发布，将来所做的改进将包含在 Azure AD Connect 更新中，因此，始终知道从何处获取最新功能。
> 
> DirSync 和 Azure AD Sync 已弃用。 可在 [此处](reference-connect-dirsync-deprecated.md)找到更多信息。
> 
> 

每个表中的代号解释如下。

● = 现在可用  
FR = 未来版本  
PP = 公开预览版  

## <a name="on-premises-to-cloud-synchronization"></a>本地到云的同步
| Feature | Azure Active Directory Connect | Azure Active Directory 同步服务 (AAD Sync) - 不再支持 | Azure Active Directory 同步工具 (DirSync) - 不再支持 | Forefront Identity Manager 2010 R2 (FIM) | Microsoft 标识管理器 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| 连接到单个本地 AD 林 |● |● |● |● |● |
| 连接到多个本地 AD 林 |● |● | |● |● |
| 连接到多个本地 Exchange 组织 |● | | | | |
| 连接到单个在本地 LDAP 目录 |●* | | |● |● | 
| 连接到多个本地 LDAP 目录 |●*  | | |● |● | 
| 连接到本地 AD 和本地 LDAP 目录 |●* | | |● |● | 
| 连接到自定义系统（例如 SQL、Oracle、MySQL 等） |FR | | |● |● |
| 同步客户定义的属性（目录扩展） |● | | | | |
| 连接到本地 HR（即 SAP、Oracle eBusiness、PeopleSoft） |FR | | |● |● |
| 支持用于预配到本地系统的 FIM 同步规则和连接器。 | | | |● |● |

 
&#42; 目前，这有两个支持的选项。  它们是： 

   1. 可以使用泛型 LDAP 连接器，在 Azure AD Connect 外部将其启用。  这很复杂，需要一位负责载入的合作伙伴，并签署关于维护的顶级支持协议。  此选项可以处理单个和多个 LDAP 目录。 

   2. 可以开发自己的解决方案，将对象从 LDAP 移至 Active Directory。  然后使用 Azure AD Connect 来同步对象。  可以将 MIM 或 FIM 用作移动这些对象的可能解决方案。 

## <a name="cloud-to-on-premises-synchronization"></a>云到本地的同步
| Feature | Azure Active Directory Connect | Azure Active Directory 同步服务 - 不再支持  | Azure Active Directory 同步工具 (DirSync) - 不再支持  | Forefront Identity Manager 2010 R2 (FIM) | Microsoft 标识管理器 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| 设备写回 |● | |● | | |
| 属性写回（适用于 Exchange 混合部署） |● |● |● |● |● |
| 组对象写回 |● | | | | |
| 密码写回（通过自助密码重置 (SSPR) 和密码更改） |● |● | | | |

## <a name="authentication-feature-support"></a>身份验证功能支持
| Feature | Azure Active Directory Connect | Azure Active Directory 同步服务 - 不再支持  | Azure Active Directory 同步工具 (DirSync) - 不再支持  | Forefront Identity Manager 2010 R2 (FIM) | Microsoft 标识管理器 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| 单个本地 AD 林的密码哈希同步 |●|●|● | | |
| 多个本地 AD 林的密码哈希同步 |●|● | | | |
| 单个本地 AD 林的直通身份验证 |●| | | | |
| 使用联合身份验证的单一登录 |● |● |● |● |● |
| 无缝单一登录|● |||||
| 密码写回（通过 SSPR 和密码更改） |● |● | | | |

## <a name="set-up-and-installation"></a>设置和安装
| Feature | Azure Active Directory Connect | Azure Active Directory 同步服务 - 不再支持  | Azure Active Directory 同步工具 (DirSync) - 不再支持  | Microsoft 标识管理器 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|
| 支持在域控制器上安装 |● |● |● | |
| 支持使用 SQL Express 安装 |● |● |● | |
| 从 DirSync 轻松升级 |● | | | |
| 将管理员 UX 本地化为 Windows Server 语言 |● |● |● | |
| 将最终用户 UX 本地化为 Windows Server 语言 | | | |● |
| 对 Windows Server 2008 和 Windows Server 2008 R2 的支持 |● 支持同步，不支持联合身份验证 |● |● |● |
| 对 Windows Server 2012 和 Windows Server 2012 R2 的支持 |● |● |● |● |

## <a name="filtering-and-configuration"></a>筛选和配置
| Feature | Azure Active Directory Connect | Azure Active Directory 同步服务 - 不再支持  | Azure Active Directory 同步工具 (DirSync) - 不再支持  | Forefront Identity Manager 2010 R2 (FIM) | Microsoft 标识管理器 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| 筛选域和组织单位 |● |● |● |● |● |
| 筛选对象的属性值 |● |● |● |● |● |
| 允许同步极少量的属性 (MinSync) |● |● | | | |
| 允许对属性流应用不同的服务模板 |● |● | | | |
| 允许删除从 AD 流向 Azure AD 的属性 |● |● | | | |
| 允许对属性流进行高级自定义 |● |● | |● |● |

## <a name="next-steps"></a>后续步骤
了解有关 [将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)的详细信息。

