---
title: Azure 资源管理器部署模式 | Microsoft Docs
description: 介绍如何使用 Azure 资源管理器指定是使用完整部署模式还是增量部署模式。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: tomfitz
ms.openlocfilehash: c82d8b90d9da44ab8f4b8ea0aa0e063ea70350e2
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70258963"
---
# <a name="azure-resource-manager-deployment-modes"></a>Azure 资源管理器部署模式

部署资源时，可以指定部署为增量更新还是完整更新。  这两种模式的主要区别是资源管理器如何处理资源组中不在模板中的现有资源。 默认模式为增量模式。

对于这两种模式，资源管理器都会尝试创建模板中指定的所有资源。 如果资源已存在于资源组中且其设置未更改，则不会对该资源执行任何操作。 如果更改资源的属性值，则使用这些新值更新资源。 如果尝试更新现有资源的位置或类型，则部署会失败并出现错误。 请改用所需的位置或类型部署新资源。

## <a name="complete-mode"></a>完整模式

在完整模式下，资源管理器删除资源组中已存在但尚未在模板中指定的资源。 由于[条件](conditional-resource-deployment.md)的计算结果为 false，因此不会删除模板中指定但未部署的资源。

将完整模式与[复制循环](resource-group-create-multiple.md)一起使用时要小心。 在解析复制循环后会删除模板中未指定的任何资源。

资源类型处理完整模式删除的方式有所不同。 当父资源不在以完整模式部署的模板中时，将自动删除该资源。 而某些子资源不在模板中时，不会将其自动删除。 但是，如果删除父资源，则会删除这些子资源。 

例如，如果资源组包含 DNS 区域（Microsoft.Network/dnsZones 资源类型）和 CNAME 记录（Microsoft.Network/dnsZones/CNAME 资源类型），则 DNS 区域是 CNAME 记录的父资源。 如果使用完整模式部署并且模板中不包含 DNS 区域，则 DNS 区域和 CNAME 记录都将被删除。 如果在模板中包含 DNS 区域但不包含 CNAME 记录，则不会删除 CNAME。 

有关资源类型如何处理删除的列表，请参阅[针对完全模式部署的 Azure 资源删除](complete-mode-deletion.md)。

如果资源组被[锁定](resource-group-lock-resources.md)，则完整模式不会删除资源。

> [!NOTE]
> 仅根级别模板支持完整部署模式。 对于[链接模板或嵌套模板](resource-group-linked-templates.md)，必须使用增量模式。 
>
> [订阅级别部署](deploy-to-subscription.md)不支持完整模式。
>
> 目前，门户不支持完整模式。
>

## <a name="incremental-mode"></a>增量模式

在增量模式下，资源管理器保留资源组中已存在但尚未在模板中指定的未更改资源。

但是，以增量模式重新部署现有资源时，结果会有所不同。 指定资源的所有属性，而不仅仅是要更新的属性。 一个常见的误解是认为未指定的属性会保持不变。 如果未指定某些属性，资源管理器会将更新解释为覆盖这些值。

## <a name="example-result"></a>示例结果

为了说明增量模式和完整模式的差异，请考虑以下方案。

**资源组**包含：

* 资源 A
* 资源 B
* 资源 C

**模板**包含：

* 资源 A
* 资源 B
* 资源 D

在“增量”模式下部署时，资源组具有：

* 资源 A
* 资源 B
* 资源 C
* 资源 D

在“完整”模式下部署时，会删除资源 C。 资源组具有：

* 资源 A
* 资源 B
* 资源 D

## <a name="set-deployment-mode"></a>设置部署模式

在使用 PowerShell 部署时若要设置部署模式，请使用 `Mode` 参数。

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Mode Complete `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json
```

在使用 Azure CLI 部署时若要设置部署模式，请使用 `mode` 参数。

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --mode Complete \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

以下示例显示了设置为增量部署模式的链接模板：

```json
"resources": [
  {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          <nested-template-or-external-template>
      }
  }
]
```

## <a name="next-steps"></a>后续步骤

* 若要了解如何创建资源管理器模板，请参阅[创作 Azure 资源管理器模板](resource-group-authoring-templates.md)。
* 若要了解如何部署资源，请参阅[使用 Azure 资源管理器模板部署应用程序](resource-group-template-deploy.md)。
* 若要查看资源提供程序的操作，请参阅 [Azure REST API](/rest/api/)。
