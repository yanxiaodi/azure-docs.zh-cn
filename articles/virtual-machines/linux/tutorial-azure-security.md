---
title: 教程 - 使用 Azure 安全中心保护 Azure 中的 Linux VM | Microsoft Docs
description: 本教程介绍如何使用 Azure 安全中心功能保护 Azure 中的 Linux 虚拟机。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/11/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 0ac5c95fa51ca5032f5d3e057af2064f872ea15c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70081715"
---
# <a name="tutorial-use-azure-security-center-to-monitor-linux-virtual-machines"></a>教程：使用 Azure 安全中心监视 Linux 虚拟机

Azure 安全中心可以帮助你深入了解 Azure 资源安全做法。 安全中心提供了集成的安全监视功能。 它可以检测到在其他方式下可能不会注意到的风险。 本教程介绍 Azure 安全中心，以及如何执行以下操作：
 
> [!div class="checklist"]
> * 设置数据收集
> * 设置安全策略
> * 查看和修复配置运行状况问题
> * 查看检测到的威胁

## <a name="security-center-overview"></a>安全中心概述

安全中心可帮助识别潜在的虚拟机 (VM) 配置问题和目标安全威胁。 这可能包括缺少网络安全组的 VM、未加密的磁盘以及暴力远程桌面协议 (RDP) 攻击。 安全中心仪表板上易于阅读的图中显示了此信息。

若要访问安全中心仪表板，请在 Azure 门户中的菜单上选择**安全中心**。 在仪表板上，可以查看 Azure 环境的安全状况、查找当前建议的计数以及查看威胁警报的当前状态。 可以展开每个高级别图表来查看更多详细信息。

![安全中心仪表板](./media/tutorial-azure-security/asc-dash.png)

安全中心不仅提供数据发现功能，而且还针对它检测到到问题提供建议。 例如，如果所部署的 VM 没有附加的网络安全组，则安全中心会显示一个建议，其中提供了可以采取的修正步骤。 无需退出安全中心的上下文即可自动完成修正。  

![建议](./media/tutorial-azure-security/recommendations.png)

## <a name="set-up-data-collection"></a>设置数据收集

若要深入了解 VM 安全配置，首先需要设置安全中心数据收集。 这涉及启用数据收集，从而会自动在订阅中的所有 VM 上安装 Microsoft Monitoring Agent。

1. 在安全中心仪表板上单击“安全策略”，并选择你的订阅。  
2. 对于**数据收集**，请在“自动预配”  中选择“启用”  。
3. 对于“默认工作区配置”  ，请将其保留为“使用安全中心创建的工作区(默认)”  。
4. 在“安全事件”  下，保留默认选项“通用”  。
4. 单击页顶部的“保存”。  

然后，会在所有 VM 上安装安全中心数据收集代理并开始收集数据。 

## <a name="set-up-a-security-policy"></a>设置安全策略

安全策略用于定义安全中心要为哪些项收集数据并提供建议。 可将不同的安全策略应用到不同的 Azure 资源集。 尽管默认情况下会对照所有策略项评估 Azure 资源，但可以针对所有 Azure 资源或某个资源组关闭单个策略项。 有关安全中心安全策略的详细信息，请参阅[在 Azure 安全中心设置安全策略](../../security-center/security-center-policies.md)。 

若要为整个订阅设置安全策略，请执行以下操作：

1. 在安全中心仪表板上选择“安全策略”，并选择订阅。 
2. 在“安全策略”边栏选项卡上，选择“安全策略”。   
3. 在“安全策略 - 安全策略”  边栏选项卡中，打开或关闭要应用到订阅的策略项。
4. 完成设置选择后，选择边栏选项卡顶部的“保存”。  

![唯一策略](./media/tutorial-azure-security/unique-policy.png)

## <a name="view-vm-configuration-health"></a>查看 VM 配置运行状况

打开数据收集并设置安全策略后，安全中心将开始提供警报和建议。 部署 VM 时，将安装数据收集代理。 然后，安全中心内将填充新 VM 的数据。 有关 VM 配置运行状况的详细信息，请参阅[在安全中心保护 VM](../../security-center/security-center-virtual-machine-protection.md)。 

收集数据时，每个 VM 和相关 Azure 资源的资源运行状况会聚合。 这些信息会显示在易于阅读的图表中。 

查看资源运行状况：

1.  在安全中心仪表板上的“防护”下面，选择“计算”。   
2.  在“计算”边栏选项卡上选择“VM 和计算机”。   此视图提供所有 VM 的配置状态摘要。

![计算运行状况](./media/tutorial-azure-security/compute-health.png)

若要查看针对某个 VM 的所有建议，请选择该 VM。 

## <a name="remediate-configuration-issues"></a>修正配置问题

在安全中心内开始填充配置数据后，系统会根据设置的安全策略生成建议。 例如，如果设置 VM 时未关联网络安全组，系统将生成有关创建网络安全组的建议。 

查看所有建议列表： 

1. 在安全中心仪表板上选择“建议”。 
2. 选择特定的建议。 将显示该建议适用于的所有资源的列表。
3. 若要应用某个建议，请选择资源。 
4. 按照修正步骤的说明进行操作。 

在许多情况下，安全中心会提供可行的步骤来遵照建议解决问题，并且无需退出安全中心。 在以下示例中，安全中心检测到一个具有不受限入站规则的网络安全组。 在建议页上，可以选择“编辑入站规则”按钮。  此时会显示用于修改规则的 UI。 

![建议](./media/tutorial-azure-security/remediation.png)

建议修正后将标记为已解决。 

## <a name="view-detected-threats"></a>查看检测到的威胁

除了资源配置建议外，安全中心还显示威胁检测警报。 安全警报功能聚合从每个 VM、Azure 网络日志和连接的合作伙伴解决方案中收集的数据，以便检测针对 Azure 资源的安全威胁。 有关安全中心威胁检测功能的深入信息，请参阅[安全中心如何检测威胁？](../../security-center/security-center-alerts-overview.md#detect-threats)。

安全警报功能要求将安全中心定价层从“免费”提升到“标准”。   迁移到这个更高的定价层后，可以**免费试用**。 

更改定价层：  

1. 在安全中心仪表板上单击“安全策略”，并选择你的订阅。 
2. 选择“定价层”。 
3. 选择“标准”  ，然后单击边栏选项卡顶部的“保存”  。


更改定价层后，安全警报图表会在检测到安全威胁时开始填充。

![安全警报](./media/tutorial-azure-security/security-alerts.png)

选择一个警报可查看信息。 例如，可以看到威胁说明、检测时间、所有威胁企图和建议的修正措施。 在下面的示例中，检测到一个 RDP 暴力攻击以及 294 次失败的 RDP 尝试。 提供了建议的解决方案。

![RDP 攻击](./media/tutorial-azure-security/rdp-attack.png)

## <a name="next-steps"></a>后续步骤
在本教程中，用户设置了安全中心，并查看了安全中心内的 VM。 你已了解如何：

> [!div class="checklist"]
> * 设置数据收集
> * 设置安全策略
> * 查看和修复配置运行状况问题
> * 查看检测到的威胁

请继续学习下一教程，详细了解如何使用 Jenkins、GitHub 和 Docker 创建 CI/CD 管道。

> [!div class="nextstepaction"]
> [使用 Jenkins、GitHub 和 Docker 创建 CI/CD 基础结构](tutorial-jenkins-github-docker-cicd.md)

