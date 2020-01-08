---
title: 将 Azure自动化与事件网格相集成 | Microsoft Docs
description: 了解如何在创建新的 VM 并向 Microsoft Teams 发送通知时自动添加标记。
keywords: 自动化, runbook, Teams, 事件网格, 虚拟机, VM
services: automation
author: eamonoreilly
manager: ''
ms.service: automation
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 05/10/2019
ms.author: eamono
ms.openlocfilehash: 9f99ce5862850c2453e9e72241fff77fe091616f
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2019
ms.locfileid: "65521430"
---
# <a name="tutorial-integrate-azure-automation-with-event-grid-and-microsoft-teams"></a>教程：将 Azure 自动化与事件网格和 Microsoft Teams 相集成

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 导入事件网格示例 runbook。
> * 创建可选 Microsoft Teams Webhook。
> * 为 runbook 创建 Webhook。
> * 创建事件网格订阅。
> * 创建触发 runbook 的 VM。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

若要完成此教程，需要 [Azure 自动化帐户](../automation/automation-offering-get-started.md)，以保存从 Azure 事件网格订阅触发的 runbook。

* 需要将 `AzureRM.Tags` 模块加载到你的自动化帐户中，请参阅[如何在 Azure 自动化中导入模块](../automation/automation-update-azure-modules.md)来了解如何将模块导入到 Azure 自动化中。

## <a name="import-an-event-grid-sample-runbook"></a>导入事件网格示例 runbook

1. 选择自动化帐户并选择 Runbook 页  。

   ![选择 runbook](./media/ensure-tags-exists-on-new-virtual-machines/select-runbooks.png)

2. 选择“浏览库”按钮  。

3. 搜索“事件网格”并选择“集成 Azure 自动化与事件网格”   。

    ![导入库 runbook](media/ensure-tags-exists-on-new-virtual-machines/gallery-event-grid.png)

4. 选择“导入”并将其命名为 Watch-VMWrite   。

5. 导入后，选择“编辑”查看 runbook 源  。 
6. 更新脚本中的第 74 行以使用 `Tag` 而不是 `Tags`。

    ```powershell
    Update-AzureRmVM -ResourceGroupName $VMResourceGroup -VM $VM -Tag $Tag | Write-Verbose
    ```
7. 选择“发布”按钮  。

## <a name="create-an-optional-microsoft-teams-webhook"></a>创建可选 Microsoft Teams Webhook

1. 在 Microsoft Teams 中，选择通道名称旁的“更多选项”，然后选择“连接器”   。

    ![Microsoft Teams 连接](media/ensure-tags-exists-on-new-virtual-machines/teams-webhook.png)

2. 在连接器列表中滚动到“传入 Webhook”，并选择“添加”   。

3. 输入 AzureAutomationIntegration 作为名称，并选择“创建”   。

4. 将 Webhook URL 复制到剪贴板并保存。 Webhook URL 用于将信息发送到 Microsoft Teams。

5. 选择“完成”保存 Webhook  。

## <a name="create-a-webhook-for-the-runbook"></a>为 runbook 创建 Webhook

1. 打开 Watch-VMWrite runbook。

2. 选择 Webhook 并选择“添加 Webhook”按钮   。

3. 输入 WatchVMEventGrid 作为名称  。 将 URL 复制到剪贴板并保存。

    ![配置 Webhook 名称](media/ensure-tags-exists-on-new-virtual-machines/copy-url.png)

4. 选择“配置参数并运行设置”，然后输入 Microsoft Teams Webhook URL 作为 CHANNELURL   。 将 WEBHOOKDATA 保留空白  。

    ![配置 Webhook 参数](media/ensure-tags-exists-on-new-virtual-machines/configure-webhook-parameters.png)

5. 选择“创建”创建自动化 runbook Webhook  。

## <a name="create-an-event-grid-subscription"></a>创建事件网格订阅

1. 在“自动化帐户”概述页上，选择“事件网格”   。

    ![选择“事件网格”](media/ensure-tags-exists-on-new-virtual-machines/select-event-grid.png)

2. 单击“+ 事件订阅”  。

3. 使用以下信息配置订阅：
    1. 对于“主题类型”，选择“Azure 订阅”   。
    2. 取消选中“订阅所有事件类型”复选框  。
    3. 输入 AzureAutomation 作为名称  。
    4. 在“定义的事件类型”  下拉列表中，取消选中除“资源写入成功”  之外的所有选项。

        > [!NOTE] 
        > Azure 资源管理器当前不区分创建和更新，因此在 Azure 订阅中对所有 Microsoft.Resources.ResourceWriteSuccess 事件实现本教程可能会导致大量调用。
    1. 对于“终结点类型”  ，选择 **Webhook**。
    2. 单击“选择终结点”  。 在打开的“选择 Web Hook”  页上，粘贴你为 Watch-VMWrite Runbook 创建的 Webhook URL。
    3. 在“筛选器”下，输入需要在其中查找新创建的 VM 的订阅和资源组  。 它看起来应该如下所示：`/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines`

4. 选择“创建”保存事件网格订阅  。

## <a name="create-a-vm-that-triggers-the-runbook"></a>创建触发 runbook 的 VM

1. 在事件网格订阅前缀筛选器中指定的资源组中创建新的 VM。

2. 应调用 Watch-VMWrite runbook，且一个新的标记应添加到 VM。

    ![VM 标记](media/ensure-tags-exists-on-new-virtual-machines/vm-tag.png)

3. 向 Microsoft Teams 通道发送了一条新消息。

    ![Microsoft Teams 通知](media/ensure-tags-exists-on-new-virtual-machines/teams-vm-message.png)

## <a name="next-steps"></a>后续步骤

在本教程中，在事件网格和自动化之间设置集成。 你已了解如何：

> [!div class="checklist"]
> * 导入事件网格示例 runbook。
> * 创建可选 Microsoft Teams Webhook。
> * 为 runbook 创建 Webhook。
> * 创建事件网格订阅。
> * 创建触发 runbook 的 VM。

> [!div class="nextstepaction"]
> [使用事件网格创建和路由自定义事件](../event-grid/custom-event-quickstart.md)
