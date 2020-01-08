---
title: 快速入门 - 使用 Azure Active Directory 条件访问检测到会话风险时阻止访问 | Microsoft Docs
description: 本快速入门介绍如何配置 Azure Active Directory (Azure AD) 条件访问策略，以基于会话风险阻止登录。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: quickstart
ms.date: 12/14/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 928900c526ec0e77f84c621f630ac5894cdb2d23
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70125663"
---
# <a name="quickstart-block-access-when-a-session-risk-is-detected-with-azure-active-directory-conditional-access"></a>快速入门：使用 Azure Active Directory 条件访问检测到会话风险时阻止访问  

若要使环境保持受保护状态，可能需要阻止可疑用户登录。 [Azure Active Directory (Azure AD) 标识保护](../active-directory-identityprotection.md)会分析每个登录活动，并计算不是由用户帐户合法所有者执行的登录尝试的可能性。 这种可能性（低、中、高）以称作[登录风险级别](conditions.md#sign-in-risk)的计算值来表示。 通过设置登录风险条件，可以配置一个条件访问策略对特定的登录风险级别做出响应。

本快速入门介绍如何配置[条件访问策略](../active-directory-conditional-access-azure-portal.md)，以便在检测到已配置的登录风险级别时阻止登录。

![创建策略](./media/app-sign-in-risk/1000.png)

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

若要完成本教程中的方案，需要：

- **有权访问 Azure AD Premium P2 版本** - 尽管条件访问是 Azure AD Premium P1 的一项功能，但需要使用 P2 版本，因为本快速入门中的方案需要 Identity Protection。
- **标识保护** - 本快速入门中的方案需要启用“标识保护”。 如果不知道如何启用“标识保护”，请参阅[启用 Azure Active Directory 标识保护](../identity-protection/enable.md)。
- **Tor 浏览器** - [Tor 浏览器](https://www.torproject.org/projects/torbrowser.html.en)可帮助保护你在网络中的隐私。 Identity Protection 将来自 Tor 浏览器的登录检测为“从匿名 IP 地址登录”，这种登录具有中等风险级别。 有关详细信息，请参阅 [Azure Active Directory 风险检测](../reports-monitoring/concept-risk-events.md)。  
- **名为 Alain Charon 的测试帐户**：如果不知道如何创建测试帐户，请参阅[添加基于云的用户](../fundamentals/add-users-azure-active-directory.md#add-a-new-user)。

## <a name="test-your-sign-in"></a>测试登录

此步骤旨在确保测试帐户可以使用 Tor 浏览器访问你的租户。

**测试登录：**

1. 以 **Alain Charon** 的身份登录到 [Azure 门户](https://portal.azure.com)。
1. 注销。

## <a name="create-your-conditional-access-policy"></a>创建条件访问策略

本快速入门中的方案使用 Tor 浏览器中的登录名生成检测到的“从匿名 IP 地址登录”风险检测。  此风险检测的风险级别为中等。 若要响应此风险检测，请将登录风险条件设置为中等。 在生产环境中，应将登录风险条件设置为高或者中和高。

此部分介绍如何创建所需的条件访问策略。 在策略中，设置：

| 设置 | 值 |
| --- | --- |
| 用户和组 | Alain Charon  |
| 云应用 | 所有云应用 |
| 登录风险 | 中型 |
| 授权 | 阻止访问 |

![创建策略](./media/app-sign-in-risk/130.png)

**若要配置条件访问策略，请执行以下操作：**

1. 以全局管理员、安全管理员或条件访问管理员的身份登录到 [Azure 门户](https://portal.azure.com)。
1. 在 Azure 门户的左侧导航栏中，单击“Azure Active Directory”。 

   ![Azure Active Directory](./media/app-sign-in-risk/02.png)

1. 在“Azure Active Directory”  页的“安全性”  部分中，单击“条件访问”  。

   ![条件性访问](./media/app-sign-in-risk/03.png)

1. 在“条件性访问”页顶部的工具栏中，单击“添加”   。

   ![Name](./media/app-sign-in-risk/108.png)

1. 在“新建”页上的“名称”文本框中，键入“出现中等风险级别时阻止访问”。   

   ![Name](./media/app-sign-in-risk/104.png)

1. 在“分配”部分中，单击“用户和组”。  

   ![用户和组](./media/app-sign-in-risk/06.png)

1. 在“用户和组”页上： 

   ![条件性访问](./media/app-sign-in-risk/107.png)

   1. 单击“选择用户和组”，然后选择“用户和组”   。
   1. 单击“选择”  。
   1. 在“选择”页上选择“Alain Charon”，然后单击“选择”。   
   1. 在“用户和组”页，单击“完成”   。
1. 单击“云应用”  。

   ![云应用](./media/app-sign-in-risk/08.png)

1. 在“云应用”页上： 

   ![条件性访问](./media/app-sign-in-risk/109.png)

   1. 单击“所有云应用”。 
   1. 单击“完成”  。
1. 单击“条件”  。

   ![访问控制](./media/app-sign-in-risk/19.png)

1. 在“条件”页面上  ：

   ![登录风险级别](./media/app-sign-in-risk/21.png)

   1. 单击“登录风险”  。
   1. 对于“配置”，单击“是”   。
   1. 对于登录风险级别，选择“中”  。
   1. 单击“选择”  。
   1. 在“条件”页面上，单击“完成”   。
1. 在“访问控制”部分中，单击“授予”。  

   ![访问控制](./media/app-sign-in-risk/10.png)

1. 在“授予”页上： 

   ![条件性访问](./media/app-sign-in-risk/105.png)

   1. 选择“阻止访问”。 
   1. 单击“选择”  。
1. 在“启用策略”部分中，单击“开”   。

   ![启用策略](./media/app-sign-in-risk/18.png)

1. 单击“创建”。 

## <a name="evaluate-a-simulated-sign-in"></a>评估模拟登录

你已经配置了条件访问策略，现在可能想知道它是否按预期工作。 第一步，使用条件访问 **what if 策略工具**模拟测试用户登录。 该模拟会估计此登录对策略的影响并生成模拟报表。  

对此策略运行 **what if 策略工具**时，“出现中等风险级别时阻止访问”应列在“要应用的策略”下面。  

![用户](./media/app-sign-in-risk/117.png)

**若要评估条件访问策略，请执行以下操作：**

1. 在[条件访问 - 策略](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)页上，单击顶部菜单中的“What If”。   

   ![What If](./media/app-sign-in-risk/14.png)

1. 单击“用户”，在“用户”页上选择“Alan Charon”，然后单击“选择”。    

   ![用户](./media/app-sign-in-risk/116.png)

1. 对于“登录风险”，请选择“中”。  

   ![用户](./media/app-sign-in-risk/119.png)

1. 单击“What If”  。

## <a name="test-your-conditional-access-policy"></a>测试条件访问策略

在上一部分中，你已经了解如何评估模拟登录。 除了模拟以外，还应该测试条件访问策略，以确保其按预期工作。

若要测试策略，请尝试使用 Tor 浏览器以 **Alan Charon** 身份登录到 [Azure 门户](https://portal.azure.com)。 条件访问策略应会阻止你的登录尝试。

![多重身份验证](./media/app-sign-in-risk/118.png)

## <a name="clean-up-resources"></a>清理资源

不再需要测试用户、Tor 浏览器和条件访问策略时，请将其删除：

- 如果不知道如何删除 Azure AD 用户，请参阅[从 Azure AD 中删除用户](../fundamentals/add-users-azure-active-directory.md#delete-a-user)。
- 若要删除策略，请选择该策略，然后在快速访问工具栏中单击“删除”  。

   ![多重身份验证](./media/app-sign-in-risk/33.png)

- 有关删除 Tor 浏览器的说明，请参阅[卸载](https://tb-manual.torproject.org/uninstalling/)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [需要接受使用条款](require-tou.md)
> [需要对特定的应用执行 MFA](app-based-mfa.md)
