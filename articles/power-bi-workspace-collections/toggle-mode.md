---
title: 在 Power BI 工作区集合中在报表的查看和编辑模式之间切换 | Microsoft Docs
description: 了解在 Power BI 工作区集合中如何在报表的查看和编辑模式之间切换。
services: power-bi-workspace-collections
ms.service: power-bi-embedded
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: 327f2fdcd4d1bc9e71e3aabb3541c6fd30f02811
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672366"
---
# <a name="toggle-between-view-and-edit-mode-for-reports-in-power-bi-workspace-collections"></a>在 Power BI 工作区集合中在报表的查看和编辑模式之间切换

了解在 Power BI 工作区集合中如何在报表的查看和编辑模式之间切换。

> [!IMPORTANT]
> Power BI 工作区集合已弃用，到 2018 年 6 月 或合同指示时可用。 建议你规划到 Power BI Embedded 的迁移以避免应用程序中断。 有关如何将数据迁移到 Power BI Embedded 的信息，请参阅[如何将 Power BI 工作区集合内容迁移到 Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/)。

## <a name="creating-an-access-token"></a>创建访问令牌

需要创建一个访问令牌，令牌可授予你查看和编辑报表的能力。 要编辑并保存报表，需要 **Report.ReadWrite** 令牌权限。 有关详细信息，请参阅[在 Power BI 工作区集合中进行身份验证和授权](app-token-flow.md)。

> [!NOTE]
> 这可以让你编辑现有报表并保存更改。 如果还需要支持“另存为”  的功能，则需要提供额外的权限。 有关详细信息，请参阅[作用域](app-token-flow.md#scopes)。

```csharp
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Report.ReadWrite";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="embed-configuration"></a>嵌入配置

为了在编辑模式下看到“保存”按钮，需要提供权限和 viewMode。 有关详细信息，请参阅[嵌入配置详细信息](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details)。

例如，在 JavaScript 中：

```html
   <div id="reportContainer"></div>

    <script>
    // Get models. Models, it contains enums that can be used.
    var models = window['powerbi-client'].models;

    // Embed configuration used to describe the what and how to embed.
    // This object is used when calling powerbi.embed.
    // This also includes settings and options such as filters.
    // You can find more information at https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details.
    var config= {
        type: 'report',
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        id:  '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
        permissions: models.Permissions.ReadWrite /*both save & save as buttons will be visible*/,
        viewMode: models.ViewMode.View,
        settings: {
            filterPaneEnabled: true,
            navContentPaneEnabled: true
        }
    };

    // Get a reference to the embedded report HTML element
    var reportContainer = $('#reportContainer')[0];

    // Embed the report and display it within the div container.
    var report = powerbi.embed(reportContainer, config);
    </script>
```

这表示会根据设置为 models.ViewMode.View  的 viewMode  在查看模式下嵌入报表。

## <a name="view-mode"></a>查看模式

如果处于编辑模式下，可以使用以下 JavaScript 切换到查看模式。

```javascript
// Get a reference to the embedded report HTML element
var reportContainer = $('#reportContainer')[0];

// Get a reference to the embedded report.
report = powerbi.get(reportContainer);

// Switch to view mode.
report.switchMode("view");

```

## <a name="edit-mode"></a>编辑模式

如果处于查看模式下，可以使用以下 JavaScript 切换到编辑模式。

```javascript
// Get a reference to the embedded report HTML element
var reportContainer = $('#reportContainer')[0];

// Get a reference to the embedded report.
report = powerbi.get(reportContainer);

// Switch to edit mode.
report.switchMode("edit");

```

## <a name="see-also"></a>请参阅

[示例入门](get-started-sample.md)  
[嵌入报表](embed-report.md)  
[在 Power BI 工作区集合中进行身份验证和授权](app-token-flow.md)  
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN)  
[JavaScript 嵌入示例](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[PowerBI-CSharp Git 存储库](https://github.com/Microsoft/PowerBI-CSharp)  
[PowerBI-Node Git 存储库](https://github.com/Microsoft/PowerBI-Node)  

有更多问题？ [尝试 Power BI 社区](https://community.powerbi.com/)
