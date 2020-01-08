---
title: 配置安全性以访问和管理 Azure 时序见解预览版 | Microsoft Docs
description: 本文介绍如何将安全性和权限配置为管理访问策略和数据访问策略以保护 Azure 时序见解预览版。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 4455d499ab4c52a27a7d9cf878e8130ff38b1c62
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2019
ms.locfileid: "68846946"
---
# <a name="grant-data-access-to-an-environment"></a>授予对环境的数据访问权限

本文讨论了两种类型的 Azure 时序见解预览版访问策略。

## <a name="sign-in-to-time-series-insights"></a>登录到 Azure 时序见解

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 查找时序见解环境。 在“搜索”框中输入 `Time Series`。 在搜索结果中选择“时序环境”。
1. 从列表中选择时序见解环境。

## <a name="grant-data-access"></a>授予数据访问权限

遵循以下步骤向用户主体授予数据访问权限。

1. 选择“数据访问策略”，然后选择“+ 添加”。

    [![Data-access-one](media/data-access/data-access-one.png)](media/data-access/data-access-one.png#lightbox)

1. 选择“选择用户”。 搜索用户名称或电子邮件地址，查找要添加的用户。 选择“选择”以确认选择。

    [![Data-access-two](media/data-access/data-access-two.png)](media/data-access/data-access-two.png#lightbox)

1. 选择“选择角色”。 为用户选择相应的访问角色：

    * 若要允许用户更改引用数据，以及与环境的其他用户共享保存的查询和透视，请选择“参与者”。

    * 否则请选择“读者”，允许用户查询环境中的数据，以及在环境中保存个人的非共享查询。

   选择“确定”，确认角色选择。

    [![Data-access-three](media/data-access/data-access-three.png)](media/data-access/data-access-three.png#lightbox)

1. 在“选择用户角色”页中，选择“确定”。

    [![Data-access-four](media/data-access/data-access-four.png)](media/data-access/data-access-four.png#lightbox)

1. 确认“数据访问策略”页列出了用户和每个用户的角色。

    [![Data-access-five](media/data-access/data-access-five.png)](media/data-access/data-access-five.png#lightbox)

## <a name="provide-guest-access-from-another-aad-tenant"></a>提供从另一个 AAD 租户进行来宾访问的权限

`Guest` 不是管理角色。 它是一个术语，是指从一个租户被邀请到另一个租户的帐户。 在将来宾帐户邀请到租户目录后，它就可以像任何其他帐户一样拥有相同的访问控制。 可以使用“访问控制(IAM)”边栏选项卡授予对时序见解环境的管理访问权限。 也可以通过“数据访问策略”边栏选项卡授予对环境中数据的访问权限。 若要详细了解 Azure Active Directory (Azure AD) 租户来宾访问权限，请阅读[在 Azure 门户中添加 Azure Active Directory B2B 协作用户](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator)。

请按照以下步骤将来宾对时序见解环境的访问权限授予另一个租户的 Azure AD 用户。

1. 选择“数据访问策略”，然后选择“+ 邀请”。

    [![Data-access-six](media/data-access/data-access-six.png)](media/data-access/data-access-six.png#lightbox)

1. 输入要邀请的用户的电子邮件地址。 此电子邮件地址必须与 Azure AD 关联。 你可以根据情况在邀请中包括个人消息。

    [![Data-access-seven](media/data-access/data-access-seven.png)](media/data-access/data-access-seven.png#lightbox)

1. 查找屏幕上显示的确认气泡。

    [![Data-access-eight](media/data-access/data-access-eight.png)](media/data-access/data-access-eight.png#lightbox)

1. 选择“选择用户”。 搜索已邀请的来宾用户的电子邮件地址，找到要添加的用户。 然后单击“选择”以确认选择。

    [![Data-access-nine](media/data-access/data-access-nine.png)](media/data-access/data-access-nine.png#lightbox)

1. 选择“选择角色”。 为来宾用户选择相应的访问角色：

    * 若要允许用户更改引用数据，以及与环境的其他用户共享保存的查询和透视，请选择“参与者”。

    * 否则请选择“读者”，允许用户查询环境中的数据，以及在环境中保存个人的非共享查询。

   选择“确定”，确认角色选择。

    [![Data-access-ten](media/data-access/data-access-ten.png)](media/data-access/data-access-ten.png#lightbox)

1. 在“选择用户角色”页中，选择“确定”。

1. 确认“数据访问策略”页列出了来宾用户和每个来宾用户的角色。

    [![Data-access-eleven](media/data-access/data-access-eleven.png)](media/data-access/data-access-eleven.png#lightbox)

1. 现在，来宾用户必须按步骤访问他们被邀请到的位于 Azure 租户中的环境。 首先，他们需要接受你发送给他们的邀请。 这份邀请通过电子邮件发送到你在步骤 5 中使用的电子邮件地址。 他们选择“开始”即可接受邀请。

    [![Data-access-twelve](media/data-access/data-access-twelve.png)](media/data-access/data-access-twelve.png#lightbox)

1. 接下来，来宾用户接受与管理员组织关联的权限。

    [![Data-access-thirteen](media/data-access/data-access-thirteen.png)](media/data-access/data-access-thirteen.png#lightbox)

1. 当来宾用户登录到你用来邀请他们的电子邮件地址后，他们就可以接受邀请，然后转到 insights.azure.com。 在那里，他们可以选择屏幕右上角的电子邮件地址旁的头像。

    [![Data-access-fourteen](media/data-access/data-access-fourteen.png)](media/data-access/data-access-fourteen.png#lightbox)

1. 接下来，来宾用户从目录下拉菜单中选择你的 Azure 租户。 此租户是你邀请他们访问的租户。

    [![Data-access-fifteen](media/data-access/data-access-fifteen.png)](media/data-access/data-access-fifteen.png#lightbox)

当来宾用户选择你的租户后，他们会看到你为他们提供访问权限的时序见解环境。 现在，他们拥有了与你在**步骤 5** 中提供的角色相关联的所有功能。

## <a name="next-steps"></a>后续步骤

* 了解[如何将 Azure 事件中心事件源添加到](./time-series-insights-how-to-add-an-event-source-eventhub.md)时序见解环境。

* 发送[事件到事件源](./time-series-insights-send-events.md)。

* [在时序见解预览版资源管理器中查看环境](./time-series-insights-update-explorer.md)。
