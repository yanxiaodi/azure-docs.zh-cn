---
title: Azure AD Connect 同步：更改 AD DS 帐户密码 | Microsoft Docs
description: 本主题文档介绍如何在 AD DS 帐户的密码更改后更新 Azure AD Connect。
services: active-directory
keywords: AD DS 帐户, Active Directory 帐户, 密码
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 35e04be046e20883f60c576745a29342add68a81
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60241590"
---
# <a name="changing-the-ad-ds-account-password"></a>更改 AD DS 帐户密码
AD DS 帐户指 Azure AD connect 用来与本地 Active Directory 通信的用户帐户。 如果更改 AD DS 帐户的密码，则必须使用新密码更新 Azure AD Connect Synchronization Service。 否则，同步服务将再也不能正确地通过本地 Active Directory 进行同步，会遇到以下错误：

* 在 Synchronization Service Manager 中，本地 AD 的导入/导出操作失败，出现 **no-start-credentials** 错误。

* 在 Windows 事件查看器下，应用程序事件日志包含一个错误，**事件 ID 为 6000**，消息为 **“管理代理‘contoso.com’未能运行，因为凭据无效”** 。


## <a name="how-to-update-the-synchronization-service-with-new-password-for-ad-ds-account"></a>如何使用 AD DS 帐户的新密码更新 Synchronization Service
使用新密码更新 Synchronization Service：

1. 启动 Synchronization Service Manager（“开始”→“同步服务”）。
</br>![Sync Service Manager](./media/how-to-connect-sync-change-addsacct-pass/startmenu.png)  

2. 转到“连接器”  选项卡。

3. 选择对应于密码已更改的 AD DS 帐户的 **AD 连接器**。

4. 在“操作”下面，选择“属性”。  

5. 在弹出对话框中，选择“连接到 Active Directory 林”  ：

6. 在“密码”  文本框中输入 AD DS 帐户的新密码。

7. 单击“确定”  保存新密码并关闭弹出对话框。

8. 在 Windows 服务控制管理器下重启 Azure AD Connect Synchronization Service。 这是为了确保从内存缓存中删除任何对旧密码的引用。

## <a name="next-steps"></a>后续步骤
**概述主题**

* [Azure AD Connect 同步：了解和自定义同步](how-to-connect-sync-whatis.md)

* [将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)
