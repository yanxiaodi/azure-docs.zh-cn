---
title: 虚拟网络服务终结点 - Azure 事件中心 | Microsoft Docs
description: 本文介绍如何将 Microsoft.EventHub 服务终结点添加到虚拟网络。
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 03/12/2019
ms.author: shvija
ms.openlocfilehash: 15912ce2e100a4317e775d72972ca6eacfac0d42
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080537"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>将虚拟网络服务终结点与 Azure 事件中心配合使用

通过将事件中心与[虚拟网络 (VNet) 服务终结点][vnet-sep]相集成，可从绑定到虚拟网络的工作负荷（例如虚拟机）安全地访问消息传递功能，同时在两端保护网络流量路径。

配置为绑定到至少一个虚拟网络子网服务终结点后，相应事件中心命名空间不再接受来自任何位置的流量，但获得授权的虚拟网络中的子网。 从虚拟网络的角度来看，通过将事件中心命名空间绑定到服务终结点，可配置从虚拟网络子网到消息传递服务的独立网络隧道。 

然后，绑定到子网的工作负荷与相应的事件中心命名空间之间将存在专用和独立的关系，消息传递服务终结点的可观察网络地址位于公共 IP 范围内对此没有影响。 没有此行为异常。 默认情况下，启用服务终结点，将启用与虚拟网络关联的 IP 防火墙中的 denyall 规则。 若要启用对事件中心公共终结点的访问的 IP 防火墙中，可以添加特定的 IP 地址。 


>[!WARNING]
> 实现虚拟网络集成可以防止其他 Azure 服务与事件中心进行交互。
>
> 实现虚拟网络时，受信任的 Microsoft 服务不受支持。
>
> 不适用于虚拟网络常见 Azure 方案（请注意，该列表内容并不详尽）  -
> - Azure Monitor
> - Azure 流分析
> - 与 Azure 事件网格的集成
> - Azure IoT 中心路由
> - Azure IoT Device Explorer
> - Azure 数据资源管理器
>
> 以下 Microsoft 服务必须在虚拟网络中
> - Azure Web 应用
> - Azure Functions

> [!IMPORTANT]
> 事件中心的标准  和专用  层支持虚拟网络。 基本层不支持它。

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>通过 VNet 集成启用的高级安全方案 

解决方案需要紧密和化的安全性，以及虚拟网络子网在其中提供分段之间的分隔开的服务，仍需要驻留在这些隔离舱中的服务之间的通信路径。

隔离舱之间的任何即时 IP 路由（包括通过 TCP/IP 承载 HTTPS 的）都存在利用网络层漏洞的风险。 消息传递服务提供完全隔离的通信路径，其中消息在各方之间转换时会以平均方式写入磁盘。 绑定到同一个事件中心实例的两个不同虚拟网络中的工作负荷可通过消息进行高效和可靠的通信，同时保留各自的网络隔离边界完整性。
 
这意味着安全敏感云解决方案不仅可以访问 Azure 行业领先的可靠且可扩展的异步消息传递功能，而且现在可以使用消息传递在安全解决方案隔离舱之间创建通信路径，这些隔离舱本质上比利用任何对等通信模式（包括 HTTPS 和其他 TLS 安全套接字协议）更加安全。

## <a name="bind-event-hubs-to-virtual-networks"></a>将事件中心绑定到虚拟网络

虚拟网络规则  是一种防火墙安全功能，用于控制是否允许 Azure 事件中心命名空间接受来自特定虚拟网络子网的连接。

将事件中心命名空间绑定到虚拟网络的过程分为两步。 首先需要在虚拟网络子网上创建“虚拟网络服务终结点”，并按照[服务终结点概述][vnet-sep]中的说明为“Microsoft.EventHub”启用该终结点  。 添加服务终结点后，使用虚拟网络规则将事件中心命名空间绑定到该终结点  。

虚拟网络规则是事件中心命名空间与虚拟网络子网的关联。 存在此规则时，绑定到子网的所有工作负荷都有权访问事件中心命名空间。 事件中心本身永远不会建立出站连接，不需要获得访问权限，因此永远不会通过启用此规则来授予对子网的访问权限。

### <a name="create-a-virtual-network-rule-with-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板创建虚拟网络规则

以下资源管理器模板可用于向现有的事件中心命名空间添加虚拟网络规则。

模板参数：

* **namespaceName**：事件中心命名空间。
* **vnetRuleName**：要创建的虚拟网络规则的名称。
* **virtualNetworkingSubnetId**：虚拟网络子网的完全限定的资源管理器路径；例如，虚拟网络默认子网的 `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default`。

> [!NOTE]
> 虽然不可能具有拒绝规则，但 Azure 资源管理器模板的默认操作设置为“允许”，不限制连接  。
> 制定虚拟网络或防火墙规则时，必须更改“defaultAction”
> 
> from
> ```json
> "defaultAction": "Allow"
> ```
> 至
> ```json
> "defaultAction": "Deny"
> ```
>

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "eventhubNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Event Hubs namespace"
        }
      },
      "virtualNetworkName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Rule"
        }
      },
      "subnetName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Sub Net"
        }
      },
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('eventhubNamespaceName'), concat('/', 'default'))]",
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('eventhubNamespaceName')]",
        "type": "Microsoft.EventHub/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
        },
        "properties": { }
      },
      {
        "apiVersion": "2017-09-01",
        "name": "[parameters('virtualNetworkName')]",
        "location": "[parameters('location')]",
        "type": "Microsoft.Network/virtualNetworks",
        "properties": {
          "addressSpace": {
            "addressPrefixes": [
              "10.0.0.0/23"
            ]
          },
          "subnets": [
            {
              "name": "[parameters('subnetName')]",
              "properties": {
                "addressPrefix": "10.0.0.0/23",
                "serviceEndpoints": [
                  {
                    "service": "Microsoft.EventHub"
                  }
                ]
              }
            }
          ]
        }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": 
          [
            {
              "subnet": {
                "id": "[variables('subNetId')]"
              },
              "ignoreMissingVnetServiceEndpoint": false
            }
          ],
          "ipRules":[<YOUR EXISTING IP RULES>],
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

若要部署模板，请按照 [Azure 资源管理器][lnk-deploy]的说明进行操作。

## <a name="next-steps"></a>后续步骤

有关虚拟网络的详细信息，请参阅以下链接：

- [Azure 虚拟网络服务终结点][vnet-sep]
- [Azure 事件中心 IP 筛选][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[ip-filtering]: event-hubs-ip-filtering.md
