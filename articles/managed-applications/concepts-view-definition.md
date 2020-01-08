---
title: Azure 托管应用程序中的视图定义概述 |Microsoft Docs
description: 介绍为 Azure 托管应用程序创建视图定义的概念。
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 06/12/2019
ms.openlocfilehash: ff96bddef1b34f5a8bf743ccaaccba2da01534dc
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335065"
---
# <a name="view-definition-artifact-in-azure-managed-applications"></a>查看 Azure 托管应用程序中的定义项目

视图定义是 Azure 托管应用程序中的可选项目。 它允许自定义 "概述" 页, 并添加更多视图, 例如度量值和自定义资源。

本文提供了查看定义项目及其功能的概述。

## <a name="view-definition-artifact"></a>视图定义项目

视图定义项目的名称必须是**viewDefinition** , 并在创建托管应用程序定义的 .zip 包中与**createUiDefinition**和**maintemplate.json**位于同一级别。 若要了解如何创建 .zip 包并发布托管应用程序定义, 请参阅[发布 Azure 托管应用程序定义](publish-managed-app-definition-quickstart.md)

## <a name="view-definition-schema"></a>查看定义架构

**ViewDefinition**文件只有一个顶级`views`属性, 这是一组视图。 每个视图在托管应用程序用户界面中显示为目录中的单独菜单项。 每个视图都`kind`有一个属性, 用于设置视图的类型。 它必须设置为以下值之一:[概述](#overview)、[指标](#metrics)、 [CustomResources](#custom-resources)。 有关详细信息, 请参阅[viewDefinition 的当前 JSON 架构](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)。

视图定义的示例 JSON:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#",
    "contentVersion": "0.0.0.1",
    "views": [
        {
            "kind": "Overview",
            "properties": {
                "header": "Welcome to your Azure Managed Application",
                "description": "This managed application is for demo purposes only.",
                "commands": [
                    {
                        "displayName": "Test Action",
                        "path": "testAction"
                    }
                ]
            }
        },
        {
            "kind": "Metrics",
            "properties": {
                "displayName": "This is my metrics view",
                "version": "1.0.0",
                "charts": [
                    {
                        "displayName": "Sample chart",
                        "chartType": "Bar",
                        "metrics": [
                            {
                                "name": "Availability",
                                "aggregationType": "avg",
                                "resourceTagFilter": [ "tag1" ],
                                "resourceType": "Microsoft.Storage/storageAccounts",
                                "namespace": "Microsoft.Storage/storageAccounts"
                            }
                        ]
                    }
                ]
            }
        },
        {
            "kind": "CustomResources",
            "properties": {
                "displayName": "Test custom resource type",
                "version": "1.0.0",
                "resourceType": "testCustomResource",
                "createUIDefinition": { },
                "commands": [
                    {
                        "displayName": "Custom Test Action",
                        "path": "testAction"
                    },
                    {
                        "displayName": "Custom Context Action",
                        "path": "testCustomResource/testContextAction",
                        "icon": "Stop",
                        "createUIDefinition": { }
                    }
                ],
                "columns": [
                    {"key": "name", "displayName": "Name"},
                    {"key": "properties.myProperty1", "displayName": "Property 1"},
                    {"key": "properties.myProperty2", "displayName": "Property 2", "optional": true}
                ]
            }
        }
    ]
}

```

## <a name="overview"></a>概述

`"kind": "Overview"`

在**viewDefinition**中提供此视图时, 它将覆盖托管应用程序中的默认概述页。

```json
{
    "kind": "Overview",
    "properties": {
        "header": "Welcome to your Azure Managed Application",
        "description": "This managed application is for demo purposes only.",
        "commands": [
            {
                "displayName": "Test Action",
                "path": "testAction"
            }
        ]
    }
}
```

|属性|必填|描述|
|---------|---------|---------|
|标头的值开始缓存响应|否|"概述" 页的标题。|
|description|否|托管应用程序的说明。|
|命令|否|"概述" 页的其他工具栏按钮的数组, 请参阅[命令](#commands)。|

![概述](./media/view-definition/overview.png)

## <a name="metrics"></a>指标

`"kind": "Metrics"`

利用 "指标" 视图, 可以在[Azure Monitor 度量值](../azure-monitor/platform/data-platform-metrics.md)的托管应用程序资源中收集和聚合数据。

```json
{
    "kind": "Metrics",
    "properties": {
        "displayName": "This is my metrics view",
        "version": "1.0.0",
        "charts": [
            {
                "displayName": "Sample chart",
                "chartType": "Bar",
                "metrics": [
                    {
                        "name": "Availability",
                        "aggregationType": "avg",
                        "resourceTagFilter": [ "tag1" ],
                        "resourceType": "Microsoft.Storage/storageAccounts",
                        "namespace": "Microsoft.Storage/storageAccounts"
                    }
                ]
            }
        ]
    }
}
```

|属性|必填|描述|
|---------|---------|---------|
|displayName|否|视图的显示标题。|
|version|否|用于呈现视图的平台版本。|
|时间表|是|"指标" 页的图表数组。|

### <a name="chart"></a>图表

|属性|必填|描述|
|---------|---------|---------|
|displayName|是|图表的显示标题。|
|chartType|否|要用于此图表的可视化效果。 默认情况下, 它使用折线图。 支持的图表类型`Bar, Line, Area, Scatter`:。|
|metrics|是|要在此图表上绘制的度量值的数组。 若要详细了解 Azure 门户中支持的指标, 请参阅[支持的指标和 Azure Monitor](../azure-monitor/platform/metrics-supported.md)|

### <a name="metric"></a>指标

|属性|必填|描述|
|---------|---------|---------|
|name|是|指标的名称。|
|aggregationType|是|要用于此指标的聚合类型。 支持的聚合类型:`none, sum, min, max, avg, unique, percentile, count`|
|命名空间|否|确定正确的指标提供程序时要使用的其他信息。|
|resourceTagFilter|否|将为其显示指标的资源标记数组`or` (将以 word 分隔)。 应用于资源类型筛选器之上。|
|resourceType|是|要为其显示指标的资源类型。|

![指标](./media/view-definition/metrics.png)

## <a name="custom-resources"></a>自定义资源

`"kind": "CustomResources"`

您可以定义此类型的多个视图。 每个视图都表示**maintemplate.json**中定义的自定义提供程序的**唯一**自定义资源类型。 有关自定义提供程序的简介，请参阅 [Azure 自定义提供程序预览版概述](custom-providers-overview.md)。

在此视图中, 可以对自定义资源类型执行 GET、PUT、DELETE 和 POST 操作。 POST 操作可以是自定义资源类型上下文中的全局自定义操作或自定义操作。

```json
{
    "kind": "CustomResources",
    "properties": {
        "displayName": "Test custom resource type",
        "version": "1.0.0",
        "resourceType": "testCustomResource",
        "createUIDefinition": { },
        "commands": [
            {
                "displayName": "Custom Test Action",
                "path": "testAction"
            },
            {
                "displayName": "Custom Context Action",
                "path": "testCustomResource/testContextAction",
                "icon": "Stop",
                "createUIDefinition": { },
            }
        ],
        "columns": [
            {"key": "name", "displayName": "Name"},
            {"key": "properties.myProperty1", "displayName": "Property 1"},
            {"key": "properties.myProperty2", "displayName": "Property 2", "optional": true}
        ]
    }
}
```

|属性|必填|描述|
|---------|---------|---------|
|displayName|是|视图的显示标题。 对于**viewDefinition**中的每个 CustomResources 视图, 标题应是**唯一**的。|
|version|否|用于呈现视图的平台版本。|
|resourceType|是|自定义资源类型。 必须是自定义提供程序的**唯一**自定义资源类型。|
|createUIDefinition|否|创建用于创建自定义资源命令的 UI 定义架构。 有关创建 UI 定义的简介, 请参阅[CreateUiDefinition](create-uidefinition-overview.md)入门|
|命令|否|CustomResources 视图的其他工具栏按钮的数组, 请参阅[命令](#commands)。|
|columns|否|自定义资源的列的数组。 如果未定义, `name`则默认情况下将显示列。 列必须包含`"key"`和`"displayName"`。 对于 "键", 提供要在视图中显示的属性的键。 如果嵌套, 请使用点作为分隔符, 例如`"key": "name"`或。 `"key": "properties.property1"` 对于 "显示名称", 提供要在视图中显示的属性的显示名称。 还可以提供`"optional"`属性。 如果设置为 true, 则默认情况下将在视图中隐藏列。|

![CustomResources](./media/view-definition/customresources.png)

## <a name="commands"></a>命令

命令是一组显示在页面上的附加工具栏按钮。 每个命令都代表**maintemplate.json**中定义的 Azure 自定义提供程序的 POST 操作。 有关自定义提供程序的简介, 请参阅[Azure 自定义提供程序概述](custom-providers-overview.md)。

```json
{
    "commands": [
        {
            "displayName": "Start Test Action",
            "path": "testAction",
            "icon": "Start",
            "createUIDefinition": { }
        },
    ]
}
```

|属性|必填|描述|
|---------|---------|---------|
|displayName|是|命令按钮的显示名称。|
|path|是|自定义提供程序操作名称。 操作必须在**maintemplate.json**中定义。|
|icon|否|命令按钮的图标。 [JSON 架构](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)中定义了支持的图标列表。|
|createUIDefinition|否|为命令创建 UI 定义架构。 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。|

## <a name="looking-for-help"></a>寻求帮助

如果有关于 Azure 托管应用程序的问题, 请尝试[Stack Overflow](http://stackoverflow.com/questions/tagged/azure-managedapps)。 可能已提出和回答类似的问题, 因此请先检查, 然后再发布。 添加标记`azure-managedapps`以获取快速响应!

## <a name="next-steps"></a>后续步骤

- 有关托管应用程序的简介，请参阅 [Azure 托管应用程序概述](overview.md)。
- 有关自定义提供程序的简介, 请参阅[Azure 自定义提供程序概述](custom-providers-overview.md)。
- 有关使用 azure 自定义提供程序创建 azure 托管应用程序[的详细说明, 请参阅教程:创建具有自定义提供程序操作和资源类型的托管应用程序](tutorial-create-managed-app-with-custom-provider.md)
