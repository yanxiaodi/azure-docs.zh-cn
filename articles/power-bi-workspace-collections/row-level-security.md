---
title: 将行级别安全性用于 Power BI 工作区集合
description: 有关将行级别安全性用于 Power BI 工作区集合的详细信息
services: power-bi-workspace-collections
ms.service: power-bi-embedded
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: 2a26cc7573abb970dc58c6f7c327dfbc659cb646
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672482"
---
# <a name="row-level-security-with-power-bi-workspace-collections"></a>将行级别安全性用于 Power BI 工作区集合

行级别安全性 (RLS) 可用于限制用户对报表或数据集内特定数据的访问，使多个不同的用户在查看不同数据的同时，能够使用相同的报表。 Power BI 工作区集合支持使用 RLS 配置的数据集。

![Power BI 工作区集合中的行级别安全性流程](media/row-level-security/flow-1.png)

> [!IMPORTANT]
> Power BI 工作区集合已弃用，到 2018 年 6 月 或合同指示时可用。 建议你规划到 Power BI Embedded 的迁移以避免应用程序中断。 有关如何将数据迁移到 Power BI Embedded 的信息，请参阅[如何将 Power BI 工作区集合内容迁移到 Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/)。

若要利用 RLS，必须了解三个重要概念：用户、角色和规则。 下面更详细地讲解每个概念：

**用户** - 查看报表的实际最终用户。 在 Power BI 工作区集合中，用户按应用令牌中的用户名属性标识。

**角色** - 用户属于角色。 角色是规则的容器，可命名为类似于“销售经理”或“销售代表”的名称。 在 Power BI 工作区集合中，用户按应用令牌中的角色属性标识。

**规则** - 角色具有规则，这些规则是要应用到数据的实际筛选器。 规则可以像“Country = USA”一样简单，也可以是更动态的对象。

### <a name="example"></a>示例

本文的余下部分将提供编写 RLS，然后在嵌入式应用程序中使用 RLS 的示例。 本例使用[零售分析示例](https://go.microsoft.com/fwlink/?LinkID=780547) PBIX 文件。

![示例销售报表](media/row-level-security/scenario-2.png)

零售分析示例显示特定零售链中所有商店的销售额。 如果不使用 RLS，无论哪个区域的经理在登录后查看报表时，都会看到相同的数据。 高级管理层决定只允许每个区域经理查看他们所管理的商店的销售额。为实现此目的，可以使用 RLS。

RLS 是在 Power BI Desktop 中编写的。 打开数据集和报表时，可以切换到图示视图来查看架构：

![Power BI Desktop 中的模型图](media/row-level-security/diagram-view-3.png)

下面是此架构的一些注意事项：

* 所有度量值，例如“总销售额”  ，存储在“销售”  事实表中。
* 有四个附加的相关维度表：“项”、“时间”、“商店”和“区域”     。
* 关系线的箭头表示筛选器可以从一个表流向另一个表的方向。 例如，如果筛选器位于 **Time[Date]** 中，则在当前架构中，它只向下筛选“销售”  表中的值。 其他表不受此筛选器的影响，因为关系线的所有箭头都指向销售表，未指向其他方向。
* “区域”  表指明谁是每个区域的经理：
  
  ![区域表行](media/row-level-security/district-table-4.png)

根据此架构，如果将筛选器应用到“区域”表中的“区域经理”  列，并且该筛选器与查看报表的用户匹配，则该筛选器也向下筛选“商店”  和“销售”  表，只显示该特定区域经理的数据。

方法如下：

1. 在“建模”选项卡中，单击“管理角色”  。  
   ![“建模”功能区中的“管理角色”按钮](media/row-level-security/modeling-tab-5.png)
2. 创建名为“经理”  的新角色。  
   ![Power BI Desktop 中的角色创建](media/row-level-security/manager-role-6.png)
3. 在“区域”  表中输入以下 DAX 表达式： **[District Manager] = USERNAME()**  
   ![角色中用于表的 DAX 筛选表达式](media/row-level-security/manager-role-7.png)
4. 为确保规则正常运行，请在“建模”  选项卡中单击“以角色身份查看”  ，并输入以下内容：  
   ![以角色身份查看](media/row-level-security/view-as-roles-8.png)

   报表随即会显示数据，与使用 **Andrew Ma** 登录时的情况一样。

如前所述应用筛选器可向下筛选“区域”  、“商店”  和“销售”  表中的所有记录。 但是，由于对“销售”  与“时间”  之间的关系应用了筛选方向，因此，“销售”  和“项”  ，以及“项”  和“时间”  表不会向下筛选。

![突出显示关系的图示视图](media/row-level-security/diagram-view-9.png)

这种模式也许能够满足某种要求，但如果不希望经理查看他们没有任何销售的项，则可以针对关系启用双向交叉筛选，使安全筛选器同时流向两个方向。 为此，可以编辑“销售”  与“项”  之间的关系，如下所示：

![针对关系的交叉筛选器方向](media/row-level-security/edit-relationship-10.png)

现在，筛选器可以从“销售”表流向“项”  表：

![图示视图中关系的筛选器方向图标](media/row-level-security/diagram-view-11.png)

> [!NOTE]
> 如果针对数据使用 DirectQuery 模式，必须选择以下两个选项来启用双向交叉筛选：

1. “文件”   -> “选项和设置”   -> “预览功能”   -> “为 DirectQuery 启用两个方向的交叉筛选”  。
2. “文件”   -> “选项和设置”   -> “DirectQuery”   -> “允许 DirectQuery 模式下不受限制的度量”  。

若要了解有关双向交叉筛选的详细信息，请下载 [Bidirectional cross-filtering in SQL Server Analysis Services 2016 and Power BI Desktop](https://download.microsoft.com/download/2/7/8/2782DF95-3E0D-40CD-BFC8-749A2882E109/Bidirectional%20cross-filtering%20in%20Analysis%20Services%202016%20and%20Power%20BI.docx)（SQL Server Analysis Services 2016 和 Power BI Desktop 中的双向交叉筛选）白皮书。

这就是需要在 Power BI Desktop 中完成的所有工作，但要使定义的 RLS 规则能够在 Power BI Embedded 中正常运行，还有一个小问题需要解决。 用户由应用程序进行身份验证和授权，应用令牌用于授予用户对特定 Power BI Embedded 报表的访问权限。 Power BI Embedded 并不具体地知道谁是用户。 要使 RLS 正常运行，需要将一些附加上下文作为应用令牌的一部分传递：

* **username**（可选）- 与 RLS 一起使用，这是一个字符串，可以在应用 RLS 规则时帮助标识用户。 请参阅“Using Row Level Security with Power BI Embedded”（在 Power BI Embedded 中使用行级别安全性）
* **roles** - 一个字符串，包含应用行级别安全性规则时可选择的角色。 如果传递多个角色，则应当以字符串数组形式传递它们。

使用 [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN) 方法创建令牌。 如果提供 username 属性，则也必须在角色中至少传递一个值。

例如，可以更改 EmbedSample。 DashboardController 第 55 行无法执行以下更新：从

    var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id);

to

    var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id, "Andrew Ma", ["Manager"]);'

完整的应用令牌如下所示：

![JSON Web 令牌示例](media/row-level-security/app-token-string-12.png)

现已组合所有信息片段，当某人登录应用程序查看此报表时，会根据行级别安全性的定义，看到其有权查看的数据。

![应用程序中显示的报表](media/row-level-security/dashboard-13.png)

## <a name="see-also"></a>请参阅

[Power BI 行级别安全性 (RLS)](https://powerbi.microsoft.com/documentation/powerbi-admin-rls/)  
[在 Power BI 工作区集合中进行身份验证和授权](app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[JavaScript 嵌入示例](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

有更多问题？ [尝试 Power BI 社区](https://community.powerbi.com/)
