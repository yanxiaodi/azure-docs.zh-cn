---
title: Azure AD Connect 同步服务影子属性 | Microsoft Docs
description: 介绍影子属性在 Azure AD Connect 同步服务中的工作方式。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10a4078f49abbdf431f42c6cde7cf882112e5848
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60384693"
---
# <a name="azure-ad-connect-sync-service-shadow-attributes"></a>Azure AD Connect 同步服务影子属性
大多数属性在 Azure AD 中的表示方式与在本地 Active Directory 中相同。 但某些属性有一些特殊处理，并且 Azure AD 中的属性值可能不同于 Azure AD Connect 同步的值。

## <a name="introducing-shadow-attributes"></a>引入了影子属性
某些属性在 Azure AD 中有两种表示形式。 本地值和计算所得的值都会进行存储。 这些额外的属性称为影子属性。 表示此行为的两个最常用属性是 **userPrincipalName** 和 **proxyAddress**。 当这些属性中有表示非已验证域的值时，属性值将发生更改。 但 Connect 中的同步引擎可读取影子属性的值，因此从其角度来看，该属性已由 Azure AD 确认。

无法使用 Azure 门户或 PowerShell 查看影子属性。 但对于属性在本地和云中具有不同值的某些方案，了解此概念可帮助对这些方案进行故障排除。

若要更好地了解此行为，请查看 Fabrikam 中的此示例：  
![域](./media/how-to-connect-syncservice-shadow-attributes/domains.png)  
这些域在其本地 Active Directory 中有多个 UPN 后缀，但只验证了其中一个。

### <a name="userprincipalname"></a>userPrincipalName
用户在非已验证域中具有以下属性值：

| 特性 | 值 |
| --- | --- |
| 本地 userPrincipalName | lee.sperry@fabrikam.com |
| Azure AD shadowUserPrincipalName | lee.sperry@fabrikam.com |
| Azure AD userPrincipalName | lee.sperry@fabrikam.onmicrosoft.com |

userPrincipalName 属性是在使用 PowerShell 时显示的值。

由于实际本地属性值存储在 Azure AD 中，因此验证 fabrikam.com 域时，Azure AD 使用 shadowUserPrincipalName 的值更新 userPrincipalName 属性。 对于要更新的这些值，无需从 Azure AD Connect 同步任何更改。

### <a name="proxyaddresses"></a>proxyAddresses
用于仅包括已验证域的相同过程也会对 proxyAddresses 执行，但会使用一些其他逻辑。 仅对邮箱用户进行已验证域检查。 启用邮件的用户或联系人代表其他 Exchange 组织中的用户，可以将 proxyAddress 中的任何值添加到这些对象。

对于邮箱用户（不管是在本地还是在 Exchange Online 中），仅显示验证域的值。 它可能如下所示：

| 特性 | 值 |
| --- | --- |
| 本地 proxyAddresses | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie.spencer@fabrikam.com</br>smtp:abbie@fabrikamonline.com |
| Exchange Online proxyAddresses | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie@fabrikamonline.com</br>SIP:abbie.spencer@fabrikamonline.com |

在本例中，**smtp:abbie.spencer\@fabrikam.com** 已删除，因为该域尚未验证。 但是，Exchange 还添加了 **SIP:abbie.spencer\@fabrikamonline.com**。 Fabrikam 未使用本地 Lync/Skype，但 Azure AD 和 Exchange Online 准备使用它。

proxyAddresses 的此逻辑称为 **ProxyCalc**。 每当出现以下情况，导致用户出现变化时，就会调用 ProxyCalc：

- 为用户分配了包含 Exchange Online 的服务计划，即使未授权该用户使用 Exchange，也是如此。 例如，如果为用户分配了 Office E3 SKU，但仅为其分配了 SharePoint Online。 即使邮箱仍在本地，也是如此。
- 属性 msExchRecipientTypeDetails 具有值。
- 更改 proxyAddresses 或 userPrincipalName。

ProxyCalc 可能需要一些时间来处理用户更改，并且不会与 Azure AD Connect 导出过程同步。

> [!NOTE]
> 对于本主题中未介绍的高级方案，ProxyCalc 逻辑具有一些其他行为。 本主题供你了解行为并未记录所有内部逻辑。

### <a name="quarantined-attribute-values"></a>隔离的属性值
有重复的属性值时，也使用影子属性。 有关详细信息，请参阅[重复属性复原](how-to-connect-syncservice-duplicate-attribute-resiliency.md)。

## <a name="see-also"></a>另请参阅
* [Azure AD Connect 同步](how-to-connect-sync-whatis.md)
* [将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)。
