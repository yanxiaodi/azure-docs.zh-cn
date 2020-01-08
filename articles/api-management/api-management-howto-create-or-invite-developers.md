---
title: 如何在 Azure API 管理中管理用户帐户 | Microsoft Docs
description: 了解如何在 Azure API 管理中创建或邀请用户
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: 9de75b5537b3e267116882e7f151d893de575071
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072468"
---
# <a name="how-to-manage-user-accounts-in-azure-api-management"></a>如何在 Azure API 管理中管理用户帐户

在 API 管理（预览版）中，开发人员是公开使用 API 管理的 API 的用户。 本指南演示如何创建和邀请开发人员使用通过 API 管理实例向其提供的 API 和产品。 有关以编程方式管理用户帐户的信息，请参阅 [API 管理 REST](/rest/api/apimanagement/)参考中的[用户实体](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/user)文档。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>先决条件

完成此文中的任务：[创建一个 Azure API 管理实例](get-started-create-service-instance.md)。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-developer"> </a>创建新开发人员

若要添加新用户，请执行本部分中的步骤：

1. 选择屏幕左侧的“用户”选项卡。
2. 按“+添加”。
3. 输入用户的相应信息。
4. 按“添加”。

    ![添加新用户](./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png)

默认情况下，新创建的开发人员帐户处于“活动”状态，并且与“开发人员”组相关联。 处于“活动”状态的开发人员帐户可用于访问它们具有订阅的所有 API。 要将新创建的开发人员与其他组相关联，请参阅[如何将组与开发人员关联][How to associate groups with developers]。

## <a name="invite-developer"> </a>邀请开发人员
若要邀请开发人员，请执行本部分中的步骤：

1. 选择屏幕左侧的“用户”选项卡。
2. 按“+邀请”。

将显示一条确认消息，但新受邀请的开发人员之后不会出现在列表中，直到他们接受邀请。 

当邀请一名开发人员时，会将一封电子邮件发送给开发人员。 此电子邮件使用一个模板生成并可自定义。 有关详细信息，请参阅[配置电子邮件模板][Configure email templates]。

一旦接受邀请，该帐户将变为活动状态。

## <a name="block-developer"> </a> 停用或重新激活开发人员帐户

默认情况下，新创建的或受邀请的开发人员帐户处于“活动”状态。 若要停用开发人员帐户，请单击“阻止”。 若要重新激活阻止的开发人员帐户，请单击“激活”。 阻止的开发人员帐户不能访问开发人员门户或调用任何 API。 若要删除用户帐户，请单击“删除”。

若要阻止用户，请执行以下步骤。

1. 选择屏幕左侧的“用户”选项卡。
2. 单击想要阻止的用户。
3. 按“阻止”。

## <a name="reset-a-user-password"></a>重置用户密码

若要以编程方式使用用户帐户, 请参阅[API 管理 REST API](/rest/api/apimanagement/)参考中的用户实体文档。 要将用户帐户密码重置为特定值，可使用[更新用户](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-user-entity#UpdateUser)操作并指定所需密码。

## <a name="next-steps"></a>后续步骤
创建开发人员帐户后，可以将其与角色相关联，并订阅产品和 API。 有关详细信息，请参阅 [如何创建和使用组][How to create and use groups]。

[api-management-management-console]: ./media/api-management-howto-create-or-invite-developers/api-management-management-console.png
[api-management-add-new-user]: ./media/api-management-howto-create-or-invite-developers/api-management-add-new-user.png
[api-management-create-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png
[api-management-invite-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer.png
[api-management-new-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-new-developer.png
[api-management-invite-developer-window]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-window.png
[api-management-invite-developer-confirmation]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-confirmation.png
[api-management-pending-verification]: ./media/api-management-howto-create-or-invite-developers/api-management-pending-verification.png
[api-management-view-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-view-developer.png
[api-management-reset-password]: ./media/api-management-howto-create-or-invite-developers/api-management-reset-password.png


[Create a new developer]: #create-developer
[Invite a developer]: #invite-developer
[Deactivate or reactivate a developer account]: #block-developer
[Next steps]: #next-steps
[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[Configure email templates]: api-management-howto-configure-notifications.md#email-templates
