---
title: 在 Azure 自动化中使用 SCCM 集合进行目标更新 - 更新管理
description: 本文的目标是帮助你使用此解决方案配置 System Center Configuration Manager 以管理 SCCM 托管计算机的更新。
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 03/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 92a93982cdd042a92b006cab7052ad4a6fee6fff
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478200"
---
# <a name="integrate-system-center-configuration-manager-with-update-management"></a>将 System Center Configuration Manager 与更新管理集成

在软件更新管理 (SUM) 周期中，已经投资购买 System Center Configuration Manager 来管理电脑、服务器和移动设备的客户还可以依赖其在管理软件更新方面的优势和成熟度。

可以通过在 Configuration Manager 中创建和预暂存软件更新部署来报告和更新托管 Windows 服务器，并使用[更新管理解决方案](automation-update-management.md)获取已完成的更新部署的详细状态。 如果使用 Configuration Manager 提供 Windows 服务器的更新合规性报告而不使用它管理更新部署，则可以继续向 Configuration Manager 进行报告，而使用更新管理解决方案管理安全更新。

## <a name="prerequisites"></a>必备组件

* 必须将[更新管理解决方案](automation-update-management.md)添加到自动化帐户。
* 当前由 System Center Configuration Manager 环境管理的 Windows 服务器还需要向也启用了更新管理解决方案的 Log Analytics 工作区进行报告。
* System Center Configuration Manager 当前分支版本 1606 和更高版本中启用了此功能。 若要与 Azure Monitor 日志集成在 Configuration Manager 管理中心站点或独立主站点并导入集合，请查看[连接到 Azure Monitor Configuration Manager 将记录](../azure-monitor/platform/collect-sccm.md)。  
* 如果 Windows 代理不从 Configuration Manager 接收安全更新，则它们必须配置为与 Windows Server Update Services (WSUS) 服务器进行通信或有权访问 Microsoft 更新。   

如何使用现有 Configuration Manager 环境管理 Azure IaaS 中托管的客户端主要取决于已在 Azure 数据中心与基础结构之间建立的连接。 此连接会影响你可能需要对 Configuration Manager 基础结构所做的任何设计更改，还会影响与支持这些必要更改相关的成本。 若要了解在继续操作之前需要评估哪些规划注意事项，请查看 [Azure 上的 Configuration Manager - 常见问题解答](/sccm/core/understand/configuration-manager-on-azure#networking)。

## <a name="configuration"></a>配置

### <a name="manage-software-updates-from-configuration-manager"></a>从 Configuration Manager 管理软件更新 

如果打算继续从 Configuration Manager 管理更新部署，请执行以下步骤。 Azure 自动化连接到 Configuration Manager 来向连接到 Log Analytics 工作区的客户端计算机应用更新。 可以从客户端计算机缓存获取更新内容，就像部署是由 Configuration Manager 管理的一样。

1. 使用 [deploy software update process](/sccm/sum/deploy-use/deploy-software-updates)（部署软件更新过程）中介绍的过程从 Configuration Manager 层次结构中的顶层站点创建软件更新部署。 与标准部署不同的必须配置的唯一设置是选项“不安装软件更新”，此选项用于控制部署包的下载行为。  此行为是通过在下一步骤中创建计划的更新部署而通过更新管理解决方案管理的。

1. 在 Azure 自动化中，选择“更新管理”。  根据[创建更新部署](automation-tutorial-update-management.md#schedule-an-update-deployment)中介绍的步骤创建一个新部署，并从“类型”下拉列表中选择“导入的组”来选择合适的配置管理器集合。   请记住以下要点：a. 如果为所选的 Configuration Manager 设备集合定义了维护窗口，则它将存储在集合的成员中而不是存储在计划的部署中定义的“持续时间”  设置中。
    b. 目标集合的成员必须连接到 Internet（直接连接、通过代理服务器或者通过 Log Analytics 网关）。

通过 Azure 自动化完成更新部署后，属于所选计算机组的成员的目标计算机将按计划的时间从本地客户端缓存中安装更新。 可以[查看更新部署状态](automation-tutorial-update-management.md#view-results-of-an-update-deployment)来监视部署结果。

### <a name="manage-software-updates-from-azure-automation"></a>从 Azure 自动化管理软件更新

若要从是 Configuration Manager 客户端的 Windows Server VM 管理更新，需要配置客户端策略来为由此解决方案管理的所有客户端禁用软件更新管理功能。 默认情况下，客户端设置以层次结构中的所有设备为应用目标。 有关此策略设置以及如何配置此设置的详细信息，请查看[如何在 System Center Configuration Manager 中配置客户端设置](/sccm/core/clients/deploy/configure-client-settings)。

在执行此配置更改后，根据[创建更新部署](automation-tutorial-update-management.md#schedule-an-update-deployment)中介绍的步骤创建一个新部署，并从“类型”下拉列表中选择“导入的组”来选择合适的配置管理器集合。  

## <a name="next-steps"></a>后续步骤

