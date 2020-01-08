---
title: 创建应用程序代理应用程序时出现问题 | Microsoft Docs
description: 如何排查在 Azure AD 管理门户中创建应用程序代理应用程序时出现的问题
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 104b98cba0948ec5d0896877e54eab1e7cd4049f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65825821"
---
# <a name="problem-creating-an-application-proxy-application"></a>创建应用程序代理应用程序时出现问题 

以下是用户在创建新应用程序代理应用程序时遇到的一些常见问题。

## <a name="recommended-documents"></a>建议的文档 

若要了解通过管理门户创建应用程序代理应用程序的详细信息，请参阅[使用 Azure AD 应用程序代理发布应用程序](application-proxy-add-on-premises-application.md)。

如果按照该文档中的步骤操作，但在创建应用程序时出错，请参阅错误详细信息以获取有关如何修复应用程序的信息和建议。 大多数的错误消息都包含建议的修复方法。 

## <a name="specific-things-to-check"></a>检查的具体内容

为避免常见的错误，请验证：

-   是有权创建应用程序代理应用程序的管理员

-   内部 URL 唯一

-   外部 URL 唯一

-   URL 以 http 或 https 开头，以“/”结尾

-   URL 应该是域名，而不是 IP 地址

在创建应用程序时，错误消息应显示在右上角。 还可以选择通知图标以查看错误消息。

   ![通知提示](./media/application-proxy-config-problem/error-message.png)

## <a name="next-steps"></a>后续步骤
[在 Azure 门户中启用应用程序代理](application-proxy-add-on-premises-application.md)
