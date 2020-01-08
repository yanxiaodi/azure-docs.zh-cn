---
title: Azure 安全中心快速入门 - 将 Azure 订阅加入安全中心标准层 | Microsoft Docs
description: 本快速入门介绍如何升级到安全中心的标准定价层，以提高安全性。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/3/2018
ms.author: memildin
ms.openlocfilehash: 138d3fd74b95fe8d51593fcbb2cedda017e35d80
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202161"
---
# <a name="quickstart-onboard-your-azure-subscription-to-security-center-standard"></a>快速入门：将 Azure 订阅加入安全中心标准层
Azure 安全中心跨混合云工作负荷提供统一的安全管理和威胁防护。 免费层只能为 Azure 资源提供有限的安全性，而标准层将这些功能扩展到了本地和其他云。 借助安全中心标准层，可以查找和修复安全漏洞、应用访问控制和应用程序控制来阻止恶意活动、使用分析和智能功能检测威胁，以及在受到攻击时迅速做出响应。 可以免费试用安全中心标准版。 若要了解详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/security-center/)。

本文介绍如何升级到标准层以提高安全性，以及在虚拟机上安装 Microsoft Monitoring Agent 来监视安全漏洞和威胁。

## <a name="prerequisites"></a>先决条件
若要开始使用安全中心，必须具有 Microsoft Azure 订阅。 如果尚无订阅，可注册[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

若要将订阅升级到标准层，必须拥有“订阅所有者”、“订阅参与者”或“安全管理员”角色。

## <a name="enable-your-azure-subscription"></a>启用 Azure 订阅

1. 登录到 [Azure 门户](https://azure.microsoft.com/features/azure-portal/)。
2. 在 **Microsoft Azure** 菜单上选择“安全中心”  。 此时会打开“安全中心 - 概览”。 

   ![安全中心概述][2]

“安全中心 - 概述”提供统一的视图用于查看混合云工作负荷的安全局势，可让你发现和评估工作负荷的安全性，以及识别和缓解风险。  安全中心会自动启用以前尚未由你或其他订阅用户加入到免费层的所有 Azure 订阅。

可以通过单击“订阅”菜单项来查看和筛选订阅列表。  现在，安全中心将开始评估这些订阅的安全性，以识别安全漏洞。 若要自定义评估类型，可以修改安全策略。 安全策略定义了工作负载的相应配置，有助于确保用户遵守公司或法规方面的安全要求。

在首次启动安全中心后的几分钟内，可以看到：

- 有关如何改善 Azure 订阅安全性的**建议**。 单击“建议”磁贴会启动一个优先级列表。 
- 该列表中包含安全中心目前正在评估的“计算和应用”、“网络”、“数据安全性”以及“标识和访问”资源的清单以及每个项的安全局势。    

若要充分利用安全中心，需要完成以下步骤升级到标准层，并安装 Microsoft Monitoring Agent。

## <a name="upgrade-to-the-standard-tier"></a>升级到标准层
若要学习安全中心快速入门和教程，必须升级到标准层。 有一个免费试用的安全中心标准版。 若要了解详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/security-center/)。 

1. 在“安全中心”主菜单下，选择“入门”  。
 
   ![入门][4]

2. 在“升级”  下，安全中心将列出符合载入条件的订阅和工作区。 
   - 可以单击可展开的“申请试用”  来查看所有订阅和工作区的列表及其试用资格状态。
   -    你可以升级没有资格试用的订阅和工作区。
   -    可以选择符合试用条件的工作区和订阅来开始试用。
3. 单击“开始试用”  在所选订阅上开始试用。


  ![安全警报][9]

## <a name="automate-data-collection"></a>自动收集数据
安全中心从 Azure VM 和非 Azure 计算机收集数据以监视安全漏洞和威胁。 数据是使用 Microsoft Monitoring Agent 收集的，它从计算机中读取各种安全相关的配置和事件日志，然后将数据复制到工作区以进行分析。 默认情况下，安全中心会自动创建新工作区。

启动自动预配后，安全中心可在所有受支持的 Azure VM 以及任何新建的 Azure VM 中安装 Microsoft Monitoring Agent。 我们强烈建议启用自动预配。

启用 Microsoft Monitoring Agent 自动设置的步骤：

1. 在“安全中心”主菜单下，选择“安全策略”  。
2. 在订阅对应的行上，选择“编辑设置>”。 
3. 在“数据收集”  选项卡上，将“自动预配”  设置为“开启”。 
4. 选择“保存”。 
---
  ![启用自动设置][6]

根据针对 Azure VM 生成的这些新见解，安全中心可以提供与系统更新状态、OS 安全配置、终结点保护相关的其他建议，并生成其他安全警报。

  ![建议][8]

## <a name="clean-up-resources"></a>清理资源
本系列中的其他快速入门和教程是在本快速入门的基础上制作的。 如果打算继续学习后续的快速入门和教程，请继续运行“标准”层并让自动预配保持启用状态。 如果不打算继续或想要返回到“免费”层，请执行以下操作：

1. 返回到安全中心主菜单，选择“安全策略”。 
2. 在要返回到“免费”的订阅对应的行上，选择“编辑设置>”。 
3. 选择“定价层”并选择“免费”，将订阅从“标准”层更改为“免费”层。  
5. 选择“保存”。 

如果希望禁用自动预配，请执行以下操作：

1. 返回到安全中心主菜单，选择“安全策略”。 
2. 在希望禁用自动预配的订阅对应的行上，选择“编辑设置>”。 
3. 在“数据收集”  选项卡上，将“自动预配”  设置为“关闭”。 
4. 选择“保存”。 

>[!NOTE]
> 禁用自动设置不会从已预配代理的 Azure VM 中删除 Microsoft Monitoring Agent。 禁用自动设置会限制对资源的安全监视。
>

## <a name="next-steps"></a>后续步骤
在本快速入门中，我们已升级到标准层，并预配了 Microsoft Monitoring Agent，用于在混合云工作负荷之间进行统一的安全管理和威胁防护。 若要详细了解如何使用安全中心，请继续学习有关如何加入本地和其他云中的 Windows 计算机的快速入门。

> [!div class="nextstepaction"]
> [快速入门：将 Windows 计算机加入安全中心](quick-onboard-windows-computer.md)

<!--Image references-->
[2]: ./media/security-center-get-started/overview.png
[4]: ./media/security-center-get-started/get-started.png
[5]: ./media/security-center-get-started/pricing.png
[6]: ./media/security-center-get-started/enable-automatic-provisioning.png
[7]: ./media/security-center-get-started/security-alerts.png
[8]: ./media/security-center-get-started/recommendations.png
[9]: ./media/security-center-get-started/select-subscription.png
