---
title: 配置 Twitter 身份验证 - Azure 应用服务
description: 了解如何为应用服务应用配置 Twitter 身份验证。
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: f7154da76b41198c208d02b8c563ba26ff8101a1
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2019
ms.locfileid: "70983599"
---
# <a name="how-to-configure-your-app-service-application-to-use-twitter-login"></a>如何将应用服务应用程序配置为使用 Twitter 登录
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本主题介绍如何将 Azure 应用服务配置为使用 Twitter 作为身份验证提供程序。

若要完成本主题中的过程，必须拥有验证了电子邮件地址和电话号码的 Twitter 帐户。 若要创建新的 Twitter 帐户，请转至 <a href="https://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>。

## <a name="register"> </a>向 Twitter 注册应用程序
1. 登录到 [Azure 门户]并导航到应用程序。 复制 **URL**。 你将使用它来配置你的 Twitter 应用。
2. 导航到 [Twitter 开发人员]网站，使用 Twitter 帐户凭据登录，并单击“创建新应用”。
3. 为新应用输入“名称”和“描述”。 在“网站”值中，粘贴应用程序的 **URL**。 然后，对于 "**回调 url**"，键入应用服务应用的 URL 并追加路径`/.auth/login/twitter/callback`。 例如， `https://contoso.azurewebsites.net/.auth/login/twitter/callback` 。 请务必使用 HTTPS 方案。
4. 在页面底部，请阅读并接受条款。 然后单击“创建 Twitter 应用程序”。 将显示应用程序详细信息。
5. 单击“设置”选项卡，选中“允许使用此应用程序进行 Twitter 登录”，并单击“更新设置”。
6. 选择“密钥和访问令牌”选项卡。记下“使用者密钥(API 密钥)”和“使用者机密(API 机密)”的值。
   
   > [!NOTE]
   > 使用者机密是一个非常重要的安全凭据。 请勿与任何人分享此密钥或将密钥随应用分发。
   > 
   > 

## <a name="secrets"></a>向应用程序添加 Twitter 信息
1. 返回 [Azure 门户]，导航到应用程序。 依次单击“设置”和“身份验证/授权”。
2. 如果“身份验证/授权”功能未启用，请切换为“打开”。
3. 单击“Twitter”。 粘贴前面获取的应用程序 ID 和应用程序密钥值。 然后单击“确定”。
   
   ![][1]
   
   默认情况下，应用服务提供身份验证但不限制对站点内容和 API 的已授权访问。 必须在应用代码中为用户授权。
4. （可选）要限制只有通过 Twitter 帐户身份验证的用户可以访问站点，请将“请求未经身份验证时需执行的操作”设置为“Twitter”。 这会要求对所有请求进行身份验证，而所有未经身份验证的请求会被重定向到 Twitter 进行身份验证。

> [!NOTE]
> 以这种方式限制访问权限适用于对应用的所有调用，对于需要公开提供的主页的应用，与在许多单页应用程序中一样。 对于此类应用程序，可以首选 "**允许匿名请求（无操作）** "，应用手动启动登录，如[此处](overview-authentication-authorization.md#authentication-flow)所述。

5. 单击“保存”。

现在，可以在应用中使用 Twitter 进行身份验证了。

## <a name="related-content"> </a>相关内容
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter 开发人员]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[Azure 门户]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
