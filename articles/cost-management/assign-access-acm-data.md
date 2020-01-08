---
title: 分配对 Azure 成本管理数据的访问权限 | Microsoft Docs
description: 本文介绍如何分配不同访问范围的 Azure 成本管理数据访问权限。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/30/2019
ms.topic: conceptual
ms.service: cost-management
manager: vitavor
ms.custom: secdec18
ms.openlocfilehash: f9db07f648956130bb5bdebb23321b0eb14679c7
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695392"
---
# <a name="assign-access-to-cost-management-data"></a>分配对成本管理数据的访问权限

具有 Azure Enterprise 协议的用户在 Azure 门户和企业 (EA) 门户中被授予了一些权限的组合，这些权限定义了用户对 Azure 成本管理数据的访问级别。 对于具有其他 Azure 帐户类型的用户，通过使用 Azure 基于角色的访问控制，可以更简单地定义用户对成本管理数据的访问级别。 本文介绍如何分配对成本管理数据的访问权限。 用户分配到权限组合后，会基于他们有权访问的范围和他们在 Azure 门户中所选的范围查看成本管理中的数据。

用户所选的范围将用于整个成本管理，以提供数据整合并控制对成本信息的访问。 使用范围时，用户不要多选它们。 而应选择一个子范围要汇总到的较大范围，然后筛选到要查看的范围。 理解数据整合很重要，因为有些人不应该访问子范围汇总到的父范围。

观看[如何使用 Azure 成本管理来分配访问](https://www.youtube.com/watch?v=J997ckmwTa8)视频，了解如何通过 azure 基于角色的访问控制分配访问权限，以查看成本和费用。

>[!VIDEO https://www.youtube.com/embed/J997ckmwTa8]

## <a name="cost-management-scopes"></a>成本管理范围

成本管理支持各种 Azure 帐户类型。 若要查看支持的帐户类型的完整列表，请参阅[了解成本管理数据](understand-cost-mgt-data.md)。 帐户的类型确定可用范围。

### <a name="azure-ea-subscription-scopes"></a>Azure EA 订阅范围

若要查看 Azure EA 订阅的成本数据，用户必须至少具有以下一个或多个范围的读取权限。

| **范围** | **定义位置** | **查看数据所需的访问权限** | **先决条件 EA 设置** | **将数据合并到** |
| --- | --- | --- | --- | --- |
| 计费帐户<sup>1</sup> | [https://ea.azure.com](https://ea.azure.com/) | 企业管理员 | 无 | 企业协议中的所有订阅 |
| 部门 | [https://ea.azure.com](https://ea.azure.com/) | 部门管理员 | **DA 视图费用**已启用 | 属于一个合约帐户的所有订阅，该帐户已关联到部门 |
| 合约帐户<sup>2</sup> | [https://ea.azure.com](https://ea.azure.com/) | 帐户所有者 | **AO 视图费用**已启用 | 注册帐户的所有订阅 |
| 管理组 | [https://portal.azure.com](https://portal.azure.com/) | 成本管理读者（或读者） | **AO 视图费用**已启用 | 管理组下的所有订阅 |
| 订阅 | [https://portal.azure.com](https://portal.azure.com/) | 成本管理读者（或读者） | **AO 视图费用**已启用 | 订阅中的所有资源/资源组 |
| 资源组 | [https://portal.azure.com](https://portal.azure.com/) | 成本管理读者（或读者） | **AO 视图费用**已启用 | 资源组中的所有资源 |

<sup>1</sup> 计费帐户还称为“企业协议”或“合约”。

<sup>2</sup> 合约帐户还称为“帐户所有者”。

下图说明了成本管理范围与角色和 EA 门户设置之间的关系。

![此图说明了成本管理范围与角色和 EA 门户设置之间的关系](./media/assign-access-acm-data/scope-access-relationship-diagram.png)

如果在 EA 门户中禁用了“DA 视图费用”，则在尝试查看部门和帐户的成本时会显示一条消息，表明“为组织禁用了成本”。

同样，如果在 EA 门户中禁用了“AO 视图费用”，则在尝试查看注册帐户、管理组、订阅和资源组的成本时会显示一条消息，表明“为组织禁用了成本”。

## <a name="other-azure-account-scopes"></a>其他 Azure 帐户范围

若要查看其他 Azure 订阅的成本数据，用户必须至少具有以下一个或多个范围的读取权限：

- Azure 帐户
- 管理组
- 资源组

## <a name="enable-access-to-costs-in-the-ea-portal"></a>启动对 EA 门户中的成本的访问

部门范围需要在 EA 门户中启用“DA 视图费用”选项。 所有其他范围需要在 EA 门户中启用“AO 视图费用”选项。

若要启用此选项，请执行以下操作：

1. 使用企业管理员帐户在 [https://ea.azure.com](https://ea.azure.com) 登录到 EA 门户。
2. 在左窗格中选择“管理”。
3. 为想提供访问权限的成本管理范围启用“DA 视图费用”和“AO 视图费用”的费用选项。  
    ![显示 DA 和 AO 视图费用选项的“合约”选项卡](./media/assign-access-acm-data/ea-portal-enrollment-tab.png)

启用视图费用选项后，大多数范围还需要 Azure 门户中的基于角色的访问控制 (RBAC) 权限配置。

## <a name="enterprise-administrator-role"></a>企业管理员角色

默认情况下，企业管理员有权访问计费帐户（企业协议/合约）和所有其他范围（子范围）。 企业管理员为其他用户分配对这些范围的访问权限。 考虑到业务连续性，最好始终保持有两位用户具有企业管理员访问权限。 以下部分提供示例来演示企业管理员如何为其他用户分配对这些范围的访问权限。

## <a name="assign-billing-account-scope-access"></a>分配计费帐户范围的访问权限

要访问计费帐户范围，需要在 EA 门户中具备企业管理员权限。 企业管理员有权查看整个 EA 合约或多个合约的成本。 对于计费帐户范围，在 Azure 门户中不需要任何操作。

1. 使用企业管理员帐户在 [https://ea.azure.com](https://ea.azure.com) 登录到 EA 门户。
2. 在左窗格中选择“管理”。
3. 在“合约”选项卡中选择要管理的合约。  
    ![在 EA 门户中选择注册](./media/assign-access-acm-data/ea-portal.png)
4. 单击“+ 添加管理员”。
5. 在“添加管理员”框中选择身份验证类型并键入用户的电子邮件地址。
6. 如果要向用户授予对成本和使用情况数据的只读访问权限，请在“只读”下选择“是”。  否则请选择“否”。
7. 单击“添加”以创建帐户。  
    ![在“添加管理员”框中显示的示例信息](./media/assign-access-acm-data/add-admin.png)

可能在 30 分钟以后，该新用户才能访问成本管理中的数据。

### <a name="assign-department-scope-access"></a>分配部门范围访问权限

若要访问部门范围，需要在 EA 门户中具备部门管理员（DA 视图费用）访问权限。 部门管理员有权查看与一个或多个部门关联的成本和使用情况数据。 部门数据包含属于关联至该部门的合约帐户的所有订阅。 在 Azure 门户中不需要任何操作。

1. 使用企业管理员帐户在 [https://ea.azure.com](https://ea.azure.com) 登录到 EA 门户。
2. 在左窗格中选择“管理”。
3. 在“合约”选项卡中选择要管理的合约。
4. 单击“部门”选项卡，然后单击“添加管理员”。
5. 在“添加部门管理员”框中选择身份验证类型并键入用户的电子邮件地址。
6. 如果要向用户授予对成本和使用情况数据的只读访问权限，请在“只读”下选择“是”。  否则请选择“否”。
7. 选择要授予其部门管理权限的部门。
8. 单击“添加”以创建帐户。  
    ![在“添加部门管理员”框中输入所需的信息](./media/assign-access-acm-data/add-depart-admin.png)

## <a name="assign-enrollment-account-scope-access"></a>分配合约帐户范围的访问权限

若要访问合约范围，需要在 EA 门户中具备帐户所有者（AO 视图费用）访问权限。 帐户所有者可以查看与从该合约帐户创建的订阅关联的成本和使用情况数据。 在 Azure 门户中不需要任何操作。

1. 使用企业管理员帐户在 [https://ea.azure.com](https://ea.azure.com) 登录到 EA 门户。
2. 在左窗格中选择“管理”。
3. 在“合约”选项卡中选择要管理的合约。
4. 单击“帐户”选项卡，然后单击“添加帐户”。
5. 在“添加帐户”框中，选择帐户要关联到的“部门”，或将其保留为未分配状态。
6. 选择身份验证类型并键入帐户名。
7. 键入用户的电子邮件地址，然后根据需要键入成本中心。
8. 单击“添加”以创建帐户。  
    ![在注册帐户的“添加帐户”框中输入所需信息](./media/assign-access-acm-data/add-account.png)

完成上述步骤后，用户帐户将成为企业门户中的合约帐户并且可以创建订阅。 用户可以访问他们创建的订阅的成本和使用情况数据。

## <a name="assign-management-group-scope-access"></a>分配管理组范围的访问权限

查看管理组作用域的访问权限至少需要成本管理读者（或读者）权限。 可以在 Azure 门户中配置对管理组的访问权限。 你必须至少对管理组具有“用户访问权限管理员”（或“所有者”）权限才能为其他人启用访问权限。 此外对于 Azure EA 帐户，还必须在 EA 门户中启用“AO 视图费用”设置。

1. 在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。
2. 在侧栏中选择“所有服务”，搜索“管理组”，然后选择“管理组”。
3. 在层次结构中选择管理组。
4. 单击管理组名称旁边的“详细信息”。
5. 从左窗格中选择“访问控制(标识和访问管理)”。
6. 单击“添加”。
7. 在“角色”下选择“成本管理读取者”。
8. 在“分配访问权限至”下选择“Azure AD 用户、组或应用程序”。
9. 若要分配访问权限，请搜索用户并选择用户。
10. 单击“保存”。  
    ![管理组中的“添加权限”框中的示例信息](./media/assign-access-acm-data/add-permissions.png)

## <a name="assign-subscription-scope-access"></a>分配订阅范围的访问权限

若要访问订阅，至少需要成本管理读取者（或读取者）权限。 可以在 Azure 门户中配置对订阅的访问权限。 你必须至少对订阅具有“用户访问权限管理员”（或“所有者”）权限才能为其他人启用访问权限。 此外对于 Azure EA 帐户，还必须在 EA 门户中启用“AO 视图费用”设置。

1. 在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。
2. 在侧栏中选择“所有服务”，搜索“订阅”，然后选择“订阅”。
3. 选择订阅。
4. 从左窗格中选择“访问控制(标识和访问管理)”。
5. 单击“添加”。
6. 在“角色”下选择“成本管理读取者”。
7. 在“分配访问权限至”下选择“Azure AD 用户、组或应用程序”。
8. 若要分配访问权限，请搜索用户并选择用户。
9. 单击“保存”。

## <a name="assign-resource-group-scope-access"></a>分配资源组范围的访问权限

若要访问资源组，至少需要成本管理读取者（或读取者）权限。 可以在 Azure 门户中配置对资源组的访问权限。 你必须至少对资源组具有“用户访问权限管理员”（或“所有者”）权限才能为其他人启用访问权限。 此外对于 Azure EA 帐户，还必须在 EA 门户中启用“AO 视图费用”设置。

1. 在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。
2. 在侧栏中选择“所有服务”，搜索“资源组”，然后选择“资源组”。
3. 选择你的资源组。
4. 从左窗格中选择“访问控制(标识和访问管理)”。
5. 单击“添加”。
6. 在“角色”下选择“成本管理读取者”。
7. 在“分配访问权限至”下选择“Azure AD 用户、组或应用程序”。
8. 若要分配访问权限，请搜索用户并选择用户。
9. 单击“保存”。

## <a name="cross-tenant-authentication-issues"></a>跨租户身份验证问题

目前，Azure 成本管理对跨租户身份验证的支持有限。 在某些情况下，尝试跨租户进行身份验证时，可能会在成本分析中收到 "**拒绝访问**" 错误。 如果将基于角色的访问控制（RBAC）配置到其他租户的订阅，然后尝试查看成本数据，则可能出现此问题。

*若要解决此问题*：配置跨租户 RBAC 后，请等待一小时。 然后，尝试查看成本分析中的成本或授予对这两个租户中用户的成本管理访问权限。  


## <a name="next-steps"></a>后续步骤

- 如果尚未完成有关成本管理的第一个快速入门，请阅读[开始分析成本](quick-acm-cost-analysis.md)。
