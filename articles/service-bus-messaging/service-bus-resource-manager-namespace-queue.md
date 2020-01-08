---
title: 使用 Azure 资源管理器模板创建 Azure 服务总线命名空间和队列 | Microsoft Docs
description: 使用 Azure 资源管理器模板创建服务总线命名空间和队列
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: a6bfb5fd-7b98-4588-8aa1-9d5f91b599b6
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 01/23/2019
ms.author: spelluru
ms.openlocfilehash: 6d7e4253d37d5b50fc8c3de1c8c31636e59b2b9c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444789"
---
# <a name="create-a-service-bus-namespace-and-a-queue-using-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板创建服务总线命名空间和队列

本文介绍如何使用创建服务总线命名空间和命名空间中队列的 Azure 资源管理器模板。 本文介绍如何指定要部署的资源以及如何定义执行部署时指定的参数。 可将此模板用于自己的部署，或自定义此模板以满足要求。

有关创建模板的详细信息，请参阅 [创作 Azure Resource Manager 模板][Authoring Azure Resource Manager templates]。

有关完整的模板，请参阅 GitHub 上的[服务总线命名空间和队列模板][Service Bus namespace and queue template]。

> [!NOTE]
> 以下 Azure 资源管理器模板可供下载和部署。
> 
> * [创建包含队列和授权规则的服务总线命名空间](service-bus-resource-manager-namespace-auth-rule.md)
> * [创建包含主题和订阅的服务总线命名空间](service-bus-resource-manager-namespace-topic.md)
> * [创建服务总线命名空间](service-bus-resource-manager-namespace.md)
> * [创建包含主题、订阅和规则的服务总线命名空间](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> 若要检查最新模板，请访问[Azure 快速入门模板][Azure Quickstart Templates]库并搜索**服务总线**。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-will-you-deploy"></a>将部署什么内容？

使用此模板，将部署包含队列的服务总线命名空间。

[服务总线队列](service-bus-queues-topics-subscriptions.md#queues)为一个或多个竞争使用方提供先入先出 (FIFO) 消息传送方式。

若要自动运行部署，请单击以下按钮：

[![部署到 Azure](./media/service-bus-resource-manager-namespace-queue/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="parameters"></a>parameters

使用 Azure 资源管理器，可以定义在部署模板时想要指定的值的参数。 该模板具有一个名为 `Parameters` 的部分，其中包含所有参数值。 应该为随着要部署的项目或要部署到的环境而变化的值定义参数。 不要为永远保持不变的值定义参数。 每个参数值可在模板中用来定义所部署的资源。

模板定义以下参数。

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
要创建的服务总线命名空间的名称。

```json
"serviceBusNamespaceName": {
"type": "string",
"metadata": { 
    "description": "Name of the Service Bus namespace" 
    }
}
```

### <a name="servicebusqueuename"></a>serviceBusQueueName
在服务总线命名空间中创建的队列的名称。

```json
"serviceBusQueueName": {
"type": "string"
}
```

### <a name="servicebusapiversion"></a>serviceBusApiVersion
模板的服务总线 API 版本。

```json
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2017-04-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       }
```

## <a name="resources-to-deploy"></a>要部署的资源
创建“Messaging”  类型的标准服务总线命名空间和队列。

```json
"resources ": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "Standard",
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusQueueName')]",
            "type": "Queues",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusQueueName')]",
            }
        }]
    }]
```

有关 JSON 语法和属性，请参阅[命名空间](/azure/templates/microsoft.servicebus/namespaces)和[队列](/azure/templates/microsoft.servicebus/namespaces/queues)。

## <a name="commands-to-run-deployment"></a>运行部署的命令
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```powershell
New-AzResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI

```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## <a name="next-steps"></a>后续步骤
请参阅以下主题演示如何创建命名空间/队列的授权规则：[创建使用 Azure 资源管理器模板为命名空间和队列服务总线授权规则](service-bus-resource-manager-namespace-auth-rule.md)

了解如何管理这些资源，通过查看以下文章：

* [使用 PowerShell 管理服务总线](service-bus-manage-with-ps.md)
* [使用服务总线 Explorer 管理服务总线资源](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Service Bus namespace and queue template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
