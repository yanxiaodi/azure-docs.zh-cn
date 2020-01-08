---
title: 将许可证分配到组 - Azure Active Directory | Microsoft Docs
description: 如何通过 Azure Active Directory 组许可分配许可证
services: active-directory
keywords: Azure AD 许可
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a54d1ad3ab809f2a2f8df6ae0e30b1b061c2be1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60471235"
---
# <a name="assign-licenses-to-users-by-group-membership-in-azure-active-directory"></a>按 Azure Active Directory 中的组成员资格将许可证分配给用户

本文将指导你完成在 Azure Active Directory (Azure AD) 将产品许可证分配给一组用户，然后验证他们是否正确获得许可的过程。

在此示例中，租户包含名为 **HR Department** 的安全组。 此组包括人力资源部的所有成员（大约 1,000 个用户）。 你要将 Office 365 企业版 E3 许可证分配给整个部门。 必须暂时禁用产品中包含的 Yammer Enterprise 服务，直到该部门准备好开始使用它为止。 你还希望将企业移动性 + 安全性许可证部署到同一个用户组。

> [!NOTE]
> 某些 Microsoft 服务不能在所有位置使用。 在将许可证分配给用户之前，管理员必须为该用户指定“使用位置”属性。
> 
> 对于组许可证分配，任何没有指定使用位置的用户将继承该目录的位置。 如果你的用户位于多个位置，则建议你始终在 Azure AD 中的用户创建流程中设置使用位置（例如通过 AAD Connect 配置）- 这可确保许可证分配的结果始终正确并且用户不会在不允许的位置收到服务。

## <a name="step-1-assign-the-required-licenses"></a>步骤 1：分配所需的许可证

1. 登录到[ **Azure AD 管理中心**](https://aad.portal.azure.com)使用许可证的管理员帐户。 若要管理许可证，该帐户必须是许可证管理员、 用户管理员或全局管理员。

2. 选择**许可证**以打开的窗格，可以在其中查看和管理租户中所有可许可的产品。

4. 下**所有产品**，通过选择产品名称来选择 Office 365 企业版 E5 和企业移动性 + 安全性 E3。 若要开始分配，请在窗格顶部选择“分配”  。

   ![选择要分配许可证的产品](./media/licensing-groups-assign/all-products-assign.png)
  
5. 上**分配许可证**窗格中，选择**用户和组**打开用户和组的列表。

6. 选择用户或组，以及如何将**选择**以确认所选内容窗格底部的按钮。

7. 上**分配许可证**窗格中，单击**分配选项**，后者将显示在前面选择的两个产品中包含的所有服务计划。 查找“Yammer Enterprise”  并将设置为“关闭”  ，以便在产品许可证中禁用该服务。 通过单击确认**确定**底部**许可证选项**。

   ![选择许可证的服务计划](./media/licensing-groups-assign/assignment-options.png)
  
8. 若要完成分配，请在“分配许可证”  窗格上，单击该窗格底部的“分配”  。

9. 右上角会显示一条通知，显示分配过程的状态和结果。 如果无法完成向组分配许可证（例如，由于该组中已有许可证），请单击该通知查看失败详细信息。

当将许可证分配给一个组，Azure AD 处理该组的所有现有成员。 此过程可能需要一些时间，不同的组的大小。 下一步骤介绍了如何验证该过程是否完成，并确定是否需要采取进一步的措施来解决问题。

## <a name="step-2-verify-that-the-initial-assignment-has-finished"></a>步骤 2：验证初始分配是否已完成

1. 转到**Azure Active Directory** > **组**。 选择已为其分配许可证的组。

2. 在组窗格中，选择**许可证**。 这使你可以快速确认是否已完全将许可证分配给用户，以及是否有任何需要调查的错误。 该边栏选项卡中提供以下信息：

   - 当前分配到组的产品许可证列表。 选择一个条目可以显示已启用的特定服务以及进行更改。

   - 上次对组所做的许可证更改的状态（是否正在处理更改，或者是否对所有用户成员的处理已完成）。

   - 有关处于错误状态的用户的信息，因为无法将许可证分配给这些用户。

   ![许可错误和许可证状态](./media/licensing-groups-assign/assignment-errors.png)

3. 可在“Azure Active Directory”   > “用户和组”   > “组名称”   > “审核日志”  下查看有关许可证处理的更多详细信息。 请注意以下活动：

   - 活动：**开始向用户应用基于组的许可证**。 当系统选取针对组所做的许可证分配更改并开始向所有用户成员应用该更改时，记录此活动。 其中包含有关所做的更改的信息。

   - 活动：**完成向用户应用基于组的许可证**。 当系统完成对组中所有用户的处理时，记录此活动。 它包含有关已成功处理多少个用户，以及无法为多少个用户分配组许可证的摘要。

   [阅读此部分](licensing-group-advanced.md#use-audit-logs-to-monitor-group-based-licensing-activity)以详细了解如何使用审核日志分析由基于组的许可进行的更改。

## <a name="step-3-check-for-license-problems-and-resolve-them"></a>步骤 3：检查和解决许可证问题

1. 转到**Azure Active Directory** > **组**，并找到已为其分配许可证的组。
2. 在组窗格中，选择**许可证**。 窗格顶部的通知显示无法为 10 个用户分配许可证。 打开它以查看此组的许可错误状态中的所有用户的列表。
3. “失败的分配”列告知无法向这些用户分配这两个产品许可证。  “故障的首要原因”  列包含失败的原因。 在此示例中，原因是**冲突的服务计划**。

   ![无法分配许可证](./media/licensing-groups-assign/failed-assignments.png)

4. 选择用户以打开“许可证”  窗格。 此窗格显示当前已分配给该用户的所有许可证。 在此示例中，该用户具有从“网亭用户  ”组继承的 Office 365 企业版 E1 许可证。 这与系统尝试从 **HR Department** 组应用的 E3 许可证冲突。 因此，该组中的许可证均未分配给该用户。

   ![查看用户的所有许可证冲突](./media/licensing-groups-assign/user-license-view.png)

5. 若要解决此问题，请从“网亭用户”  组中删除了该用户。 Azure AD 处理更改后，将正常分配“人力资源部”许可证。 

   ![此处正常分配许可证](./media/licensing-groups-assign/license-correctly-assigned.png)

## <a name="next-steps"></a>后续步骤

若要详细了解用于通过组进行许可证管理的功能集，请参阅以下文章：

* [Azure Active Directory 中基于组的许可是什么？](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [识别和解决 Azure Active Directory 中组的许可问题](licensing-groups-resolve-problems.md)
* [如何将单个许可用户迁移到 Azure Active Directory 中基于组的许可](licensing-groups-migrate-users.md)
* [如何在 Azure Active Directory 中使用基于组的许可在产品许可证之间迁移用户](licensing-groups-change-licenses.md)
* [Azure Active Directory 基于组的许可的其他方案](../active-directory-licensing-group-advanced.md)
* [Azure Active Directory 中基于组的许可的 PowerShell 示例](licensing-ps-examples.md)
