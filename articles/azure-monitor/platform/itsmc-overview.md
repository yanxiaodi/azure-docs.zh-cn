---
title: Azure Log Analytics 中的 IT Service Management Connector | Microsoft Docs
description: 本文提供 IT 服务管理连接器 (ITSMC) 的概述以及有关如何使用此解决方案集中监视和管理 Azure Log Analytics 中的 ITSM 工作项并快速解决任何问题的信息。
services: log-analytics
documentationcenter: ''
author: jyothirmaisuri
manager: riyazp
editor: ''
ms.assetid: 0b1414d9-b0a7-4e4e-a652-d3a6ff1118c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: v-jysur
ms.openlocfilehash: 31d9307d23d308192b362d9570911c86a7dd8372
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051835"
---
# <a name="connect-azure-to-itsm-tools-using-it-service-management-connector"></a>使用 IT 服务管理连接器将 Azure 连接到 ITSM 工具

![IT Service Management Connector 符号](media/itsmc-overview/itsmc-symbol.png)

使用 IT 服务管理连接器 (ITSMC) 可以连接 Azure 和支持的 IT 服务管理 (ITSM) 产品/服务。

Log Analytics 和 Azure Monitor 等 Azure 服务提供所需的工具用于检测、分析和排查 Azure 与非 Azure 资源的问题。 但是，与问题相关的工作项通常驻留在 ITSM 产品/服务中。 ITSM 连接器在 Azure 与 ITSM 工具之间提供双向连接，有助于更快解决问题。

ITSMC 支持使用以下 ITSM 工具建立的连接：

-   ServiceNow
-   System Center Service Manager
-   Provance
-   Cherwell

通过 ITSMC，你可以：

-  在 ITSM 工具中，根据 Azure 警报（指标警报、活动日志警报和 Log Analytics 警报）创建工作项。
-  可以选择将 ITSM 工具中的事件和更改请求数据同步到 Azure Log Analytics 工作区。

详细了解[法律条款和隐私策略](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9)。

执行以下步骤即可开始使用 ITSM 连接器：

1.  [添加 ITSM 连接器解决方案](#adding-the-it-service-management-connector-solution)
2.  创建 ITSM 连接
3.  [使用连接](#using-the-solution)


##  <a name="adding-the-it-service-management-connector-solution"></a>添加 IT Service Management Connector 解决方案

在创建连接之前，需要先添加 ITSM 连接器解决方案。

1. 在 Azure 门户中，单击“+ 新建”图标。

   ![Azure 新资源](media/itsmc-overview/azure-add-new-resource.png)

2. 在市场中搜索“IT 服务管理连接器”，然后单击“创建”。

   ![添加 ITSMC 解决方案](media/itsmc-overview/add-itsmc-solution.png)

3. 在“OMS 工作区”部分，选择要在其中安装解决方案的 Azure Log Analytics 工作区。
   >[!NOTE]
   > * 作为从 Microsoft Operations Management Suite (OMS) 到 Azure Monitor 的持续过渡的一部分，OMS 现工作区在称为 Log Analytics 工作区。
   > * 只能在以下区域的 Log Analytics 工作区中安装 ITSM 连接器:美国东部、西欧、东南亚、东南部、美国西部、美国东部、日本西部、印度中部、美国东部、加拿大中部。

4. 在“OMS 工作区设置”部分，选择要在其中创建解决方案资源的资源组。

   ![ITSMC 工作区](media/itsmc-overview/itsmc-solution-workspace.png)
   >[!NOTE]
   >作为从 Microsoft Operations Management Suite (OMS) 到 Azure Monitor 的持续过渡的一部分，OMS 现工作区在称为 Log Analytics 工作区。

5. 单击“创建”。

部署解决方案资源时，窗口右上角会显示通知。


## <a name="creating-an-itsm--connection"></a>创建 ITSM 连接

安装解决方案后，可以创建连接。

若要创建连接，需要准备好 ITSM 工具，以允许从 ITSM 连接器解决方案建立连接。  

根据要连接到 ITSM 产品执行以下步骤：

- [System Center Service Manager (SCSM)](../../azure-monitor/platform/itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](../../azure-monitor/platform/itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](../../azure-monitor/platform/itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](../../azure-monitor/platform/itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

准备好 ITSM 工具之后，请遵循以下步骤创建连接：

1. 转到“所有资源”，找到“ServiceDesk(YourWorkspaceName)”。
2. 在左窗格中的“工作区数据源”下，单击“ITSM 连接”。
   ![ITSM 连接](media/itsmc-overview/itsm-connections.png)

   此页显示连接列表。
3. 单击“添加连接”。

   ![添加 ITSM 连接](media/itsmc-overview/add-new-itsm-connection.png)

4. 根据[使用 ITSM 产品/服务配置 ITSMC 连接](../../azure-monitor/platform/itsmc-connections.md)一文中所述指定连接设置。

   > [!NOTE]
   >
   > 默认情况下，ITSMC 每隔 24 小时刷新连接配置数据一次。 若要即时刷新连接的数据以获取执行的任何编辑或模板更新，单击连接边栏选项卡上的“同步”按钮。

   ![连接刷新](media/itsmc-overview/itsmc-connections-refresh.png)


## <a name="using-the-solution"></a>使用解决方案
   使用 ITSM 连接器解决方案，可以基于 Azure 警报、Log Analytics 警报和 Log Analytics 日志记录创建工作项。

## <a name="create-itsm-work-items-from-azure-alerts"></a>根据 Azure 警报创建 ITSM 工作项

创建 ITSM 连接后，可以使用**操作组**中的 **ITSM 操作**，在 ITSM 工具中基于 Azure 警报创建工作项。

操作组对 Azure 警报提供模块化且可重用的方法来触发操作。 可以在 Azure 门户结合指标警报、活动日志警报和 Azure Log Analytics 警报使用操作组。

请按以下过程操作：

1. 在 Azure 门户中，单击“监视器”。
2. 在左窗格中，单击“操作组”。 “添加操作组”窗口随即显示。

    ![操作组](media/itsmc-overview/action-groups.png)

3. 为操作组提供“名称”和“短名称”。 选择要创建操作组的“资源组”和“订阅”。

    ![操作组详细信息](media/itsmc-overview/action-groups-details.png)

4. 在“操作”列表中，从“操作类型”下拉列表菜单中选择“ITSM”。 提供操作的“名称”并单击“编辑详细信息”。
5. 选择 Log Analytics 工作区所在的“订阅”。 选择后跟工作区名称的“连接”名称（你的 ITSM 连接器名称）。 例如，“MyITSMMConnector(MyWorkspace)。”

    ![ITSM 操作详细信息](media/itsmc-overview/itsm-action-details.png)

6. 从下拉列表菜单中选择“工作项”类型。
   选择使用现有模板或填充 ITSM 产品要求的字段。
7. 单击 **“确定”** 。

创建/编辑 Azure 警报规则时，使用具有 ITSM 操作的操作组。 警报触发时，会在 ITSM 工具中创建/更新工作项。

> [!NOTE]
>
> 有关 ITSM 操作的定价信息，请参阅操作组的[定价页](https://azure.microsoft.com/pricing/details/monitor/)。


## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>可视化和分析事件与更改请求数据

根据配置，在设置连接时，ITSM 连接器可以同步最长 120 天的事件和更改请求数据。 [下一部分](#additional-information)提供了此数据的日志记录架构。

可以在解决方案中使用 ITSM 连接器仪表板将事件和更改请求数据可视化。

![Log Analytics 屏幕](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

仪表板还提供有关连接器状态的信息，在分析任何连接问题时，可以从这些信息着手。

还可以在服务映射解决方案中，将针对受影响计算机同步的事件可视化。

服务映射自动发现 Windows 和 Linux 系统上的应用程序组件并映射服务之间的通信。 它允许如所想一般作为提供重要服务的互连系统查看服务器。 服务映射显示任何 TCP 连接的体系结构中服务器、进程和端口之间的连接，只需安装代理，无需任何其他配置。 [了解详细信息](../../azure-monitor/insights/service-map.md)。

如果使用了服务映射解决方案，则可以查看 ITSM 解决方案中创建的服务台工作项，如以下示例中所示：

![Log Analytics 屏幕](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

详细信息：[服务地图](../../azure-monitor/insights/service-map.md)


## <a name="additional-information"></a>其他信息

### <a name="data-synced-from-itsm-product"></a>从 ITSM 产品同步的数据
事件和更改请求根据连接配置从 ITSM 产品同步到 Log Analytics 工作区。

以下信息显示 ITSMC 收集的数据示例：

> [!NOTE]
>
> 根据导入 Log Analytics 的工作项类型，**ServiceDesk_CL** 包含以下字段：

**工作项：** **事件**  
ServiceDeskWorkItemType_s="Incident"

**Fields**

- 服务台连接名称
- 服务台 ID
- 状态
- 紧急性
- 影响
- Priority
- 升级
- 创建者
- 解决者
- 关闭者
- Source
- 已分配给
- 类别
- 标题
- 描述
- 创建日期
- 关闭日期
- 解决日期
- 上次修改日期
- 计算机


**工作项：** **更改请求**

ServiceDeskWorkItemType_s="ChangeRequest"

**Fields**
- 服务台连接名称
- 服务台 ID
- 创建者
- 关闭者
- Source
- 分配给
- 标题
- 类型
- 类别
- 状态
- 升级
- 冲突状态
- 紧急性
- Priority
- 风险
- 影响
- 已分配给
- 创建日期
- 关闭日期
- 上次修改日期
- 请求日期
- 计划开始日期
- 计划结束日期
- 工作开始日期
- 工作结束日期
- 描述
- 计算机

## <a name="output-data-for-a-servicenow-incident"></a>ServiceNow 事件的输出数据

| Log Analytics 字段 | ServiceNow 字段 |
|:--- |:--- |
| ServiceDeskId_s| 数量 |
| IncidentState_s | 状态 |
| Urgency_s |紧急性 |
| Impact_s |影响|
| Priority_s | Priority |
| CreatedBy_s | 打开者 |
| ResolvedBy_s | 解决者|
| ClosedBy_s  | 关闭者 |
| Source_s| 联系类型 |
| AssignedTo_s | 已分配到  |
| Category_s | 类别 |
| Title_s|  简短说明 |
| Description_s|  说明 |
| CreatedDate_t|  已打开 |
| ClosedDate_t| 已关闭|
| ResolvedDate_t|已解决|
| 计算机  | 配置项 |

## <a name="output-data-for-a-servicenow-change-request"></a>ServiceNow 更改请求的输出数据

| Log Analytics | ServiceNow 字段 |
|:--- |:--- |
| ServiceDeskId_s| 数量 |
| CreatedBy_s | 请求者 |
| ClosedBy_s | 关闭者 |
| AssignedTo_s | 分配给  |
| Title_s|  简短说明 |
| Type_s|  类型 |
| Category_s|  类别 |
| CRState_s|  状态|
| Urgency_s|  紧急性 |
| Priority_s| Priority|
| Risk_s| 风险|
| Impact_s| 影响|
| RequestedDate_t  | 请求日期 |
| ClosedDate_t | 关闭日期 |
| PlannedStartDate_t  |     计划开始日期 |
| PlannedEndDate_t  |   计划结束日期 |
| WorkStartDate_t  | 实际开始日期 |
| WorkEndDate_t | 实际结束日期|
| Description_s | 描述 |
| 计算机  | 配置项 |


## <a name="troubleshoot-itsm-connections"></a>排查 ITSM 连接问题
1. 如果从连接源的 UI 建立连接失败，并出现“保存连接时出错”消息，请执行以下步骤：
   - 对于 ServiceNow、Cherwell 和 Provance 连接，  
   - 请确保正确输入每个连接的用户名、密码、客户端 ID 和客户端密码。  
   - 检查在相应 ITSM 产品中是否拥有建立连接的足够权限。  
   - 对于 Service Manager 连接，  
   - 确保成功部署 Web 应用并创建混合连接。 若要验证是否已成功建立与本地 Service Manager 计算机的连接，请访问建立[混合连接](../../azure-monitor/platform/itsmc-connections.md#configure-the-hybrid-connection)文档中详细介绍的 Web 应用 URL。  

2. 如果未向 Log Analytics 同步来自 ServiceNow 的数据，请确保 ServiceNow 实例处于非休眠状态。 如果 ServiceNow 开发实例长时间处于空闲状态，有时会进入休眠状态。 否则，请报告问题。
3. 如果 Log Analytics 警报触发但未在 ITSM 产品中创建工作项，或配置项未创建/未链接到工作项，或出于任何一般信息的目的，请查看以下位置：
   -  ITSMC：此解决方案显示连接/工作项/计算机等的摘要。单击显示“连接器状态”的磁贴，可以跳转到具有相关查询的“日志搜索”。 查看含有 LogType_S as ERROR 的日志记录，了解详细信息。
   - “日志搜索”页：`*`使用 ServiceDeskLog_CL`*` 查询直接查看错误/相关信息。

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Service Manager Web 应用部署故障排除
1.  如果 Web 应用部署出现任何问题，请确保在订阅中拥有提及的足够权限，以能够创建/部署资源。
2.  如果在运行[脚本](itsmc-service-manager-script.md)时出现“对象引用未设置为某个对象的实例”消息，请确保在“用户配置”部分下输入有效的值。
3.  如果未能创建服务总线中继命名空间，请确保在订阅中注册所需的资源提供程序。 如果未注册，请手动从 Azure 门户创建服务总线中继命名空间。 从 Azure 门户[创建混合连接](../../azure-monitor/platform/itsmc-connections.md#configure-the-hybrid-connection)时，也可进行创建。


## <a name="contact-us"></a>联系我们

在 IT Service Management Connector 方面如有任何咨询或反馈，请通过 [omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com) 联系我们。

## <a name="next-steps"></a>后续步骤
[将 ITSM 产品/服务添加到 IT Service Management Connector](../../azure-monitor/platform/itsmc-connections.md)。
