---
title: 在 Azure Active Directory B2C 中切换到 B2C 租户 | Microsoft Docs
description: 如何切换到 Active Directory B2C 租户的上下文。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/13/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: cbf0e928ae05e723902d41a340aebf4f5781fde5
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2019
ms.locfileid: "67654113"
---
# <a name="switching-to-your-azure-ad-b2c-tenant"></a>切换到 Azure AD B2C 租户

若要配置 Azure AD B2C，需处于 Azure AD B2C 租户的上下文中。

## <a name="log-into-azure-ad-b2c-tenant"></a>登录到 Azure AD B2C 租户

若要导航到 Azure AD B2C 租户，必须以 Azure AD B2C 租户全局管理员身份登录到 Azure 门户。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 单击右上角的电子邮件地址或图片即可切换租户。
1. 在显示的 `Directory` 列表中，选择要管理的 Azure AD B2C 租户。

此时 Azure 门户会刷新。  现在，已登录到 Azure AD B2C 租户上下文中的 Azure 门户。

## <a name="navigate-to-the-b2c-features-pane"></a>导航到 B2C 功能窗格

1. 单击左侧导航上的“浏览”  。
1. 单击“所有服务”，然后在左侧导航窗格中搜索 `Azure AD B2C`。   （若要固定到左侧启动板，请单击 Azure AD B2C 左侧的星号）
1. 单击“Azure AD B2C”  访问 B2C 功能窗格。

    ![浏览到 B2C 功能窗格的屏幕截图](./media/active-directory-b2c-get-started/b2c-browse.png)

> [!IMPORTANT]
> 你需要是能够访问 B2C 功能窗格的 B2C 租户全局管理员。 任何其他租户的全局管理员或任何租户的用户均不能访问它。  可以通过 Azure 门户右上角的租户切换器切换到 B2C 租户。
