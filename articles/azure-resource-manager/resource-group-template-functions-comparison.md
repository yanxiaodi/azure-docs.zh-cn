---
title: Azure 资源管理器模板函数 - 比较 | Microsoft Docs
description: 介绍可在 Azure 资源管理器模板中使用的用于比较值的函数。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/05/2017
ms.author: tomfitz
ms.openlocfilehash: 05e51af62be974f925fefc46ad63c489f27fd163
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70194832"
---
# <a name="comparison-functions-for-azure-resource-manager-templates"></a>用于 Azure 资源管理器模板的比较函数

Resource Manager 提供了多个用于在模板中进行比较的函数。

* [equals](#equals)
* [greater](#greater)
* [greaterOrEquals](#greaterorequals)
* [less](#less)
* [lessOrEquals](#lessorequals)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="equals"></a>equals
`equals(arg1, arg2)`

检查两个值是否相等。

### <a name="parameters"></a>Parameters

| 参数 | 必填 | 类型 | 描述 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |int、string、array 或 object |要检查是否相等的第一个值。 |
| arg2 |是 |int、string、array 或 object |要检查是否相等的第二个值。 |

### <a name="return-value"></a>返回值

如果值相等，返回 **True**；否则返回 **False**。

### <a name="remarks"></a>备注

Equals 函数通常与 `condition` 元素配合使用来测试是否部署资源。

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

### <a name="example"></a>示例

下列[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/equals.json)检查不同类型的值是否相等。 所有默认值都返回 True。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 1
        },
        "firstString": {
            "type": "string",
            "defaultValue": "a"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["a", "b"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["a", "b"]
        },
        "firstObject": {
            "type": "object",
            "defaultValue": {"a": "b"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"a": "b"}
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[equals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[equals(parameters('firstString'), parameters('secondString'))]"
        },
        "checkArrays": {
            "type": "bool",
            "value": "[equals(parameters('firstArray'), parameters('secondArray'))]"
        },
        "checkObjects": {
            "type": "bool",
            "value": "[equals(parameters('firstObject'), parameters('secondObject'))]"
        }
    }
}
```

上面具有默认值的示例的输出为：

| 姓名 | 类型 | ReplTest1 |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | True |
| checkArrays | Bool | True |
| checkObjects | Bool | True |

要使用 Azure CLI 部署此示例模板，请使用：

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/equals.json
```

要使用 PowerShell 部署此示例模板，请使用：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/equals.json 
```

以下[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json)结合使用 [not](resource-group-template-functions-logical.md#not) 和 **equals**。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "checkNotEquals": {
            "type": "bool",
            "value": "[not(equals(1, 2))]"
        }
    }
}
```

前述示例的输出为：

| 姓名 | 类型 | ReplTest1 |
| ---- | ---- | ----- |
| checkNotEquals | Bool | True |

要使用 Azure CLI 部署此示例模板，请使用：

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json
```

要使用 PowerShell 部署此示例模板，请使用：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json 
```

## <a name="greater"></a>greater
`greater(arg1, arg2)`

检查第一个值是否大于第二个值。

### <a name="parameters"></a>Parameters

| 参数 | 必填 | type | 描述 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |int 或 string |用于大于比较的第一个值。 |
| arg2 |是 |int 或 string |用于大于比较的第二个值。 |

### <a name="return-value"></a>返回值

如果第一个值大于第二个值，返回 **True**；否则返回 **False**。

### <a name="example"></a>示例

下列[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greater.json)检查一个值是否大于另一个值。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[greater(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[greater(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

上面具有默认值的示例的输出为：

| 姓名 | 类型 | ReplTest1 |
| ---- | ---- | ----- |
| checkInts | Bool | False |
| checkStrings | Bool | True |

要使用 Azure CLI 部署此示例模板，请使用：

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greater.json
```

要使用 PowerShell 部署此示例模板，请使用：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greater.json 
```

## <a name="greaterorequals"></a>greaterOrEquals
`greaterOrEquals(arg1, arg2)`

检查第一个值是否大于或等于第二个值。

### <a name="parameters"></a>Parameters

| 参数 | 必填 | 类型 | 描述 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |int 或 string |用于大于或等于比较的第一个值。 |
| arg2 |是 |int 或 string |用于大于或等于比较的第二个值。 |

### <a name="return-value"></a>返回值

如果第一个值大于或等于第二个值，返回 **True**；否则返回 **False**。

### <a name="example"></a>示例

下列[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greaterorequals.json)检查一个值是否大于等于另一个值。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[greaterOrEquals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[greaterOrEquals(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

上面具有默认值的示例的输出为：

| 姓名 | 类型 | ReplTest1 |
| ---- | ---- | ----- |
| checkInts | Bool | False |
| checkStrings | Bool | True |

要使用 Azure CLI 部署此示例模板，请使用：

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greaterorequals.json
```

要使用 PowerShell 部署此示例模板，请使用：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greaterorequals.json 
```

## <a name="less"></a>更少
`less(arg1, arg2)`

检查第一个值是否小于第二个值。

### <a name="parameters"></a>Parameters

| 参数 | 必填 | 类型 | 描述 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |int 或 string |用于小于比较的第一个值。 |
| arg2 |是 |int 或 string |用于小于比较的第二个值。 |

### <a name="return-value"></a>返回值

如果第一个值小于第二个值，返回 **True**；否则返回 **False**。

### <a name="example"></a>示例

下列[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/less.json)检查一个值是否小于另一个值。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[less(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[less(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

上面具有默认值的示例的输出为：

| 姓名 | 类型 | ReplTest1 |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | False |

要使用 Azure CLI 部署此示例模板，请使用：

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/less.json
```

要使用 PowerShell 部署此示例模板，请使用：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/less.json 
```

## <a name="lessorequals"></a>lessOrEquals
`lessOrEquals(arg1, arg2)`

检查第一个值是否小于或等于第二个值。

### <a name="parameters"></a>Parameters

| 参数 | 必填 | 类型 | 描述 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |int 或 string |用于小于或等于比较的第一个值。 |
| arg2 |是 |int 或 string |用于小于或等于比较的第二个值。 |

### <a name="return-value"></a>返回值

如果第一个值小于或等于第二个值，返回 **True**；否则返回 **False**。

### <a name="example"></a>示例

下列[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/lessorequals.json)检查一个值是否小于等于另一个值。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[lessOrEquals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[lessOrEquals(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

上面具有默认值的示例的输出为：

| 姓名 | 类型 | ReplTest1 |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | False |

要使用 Azure CLI 部署此示例模板，请使用：

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/lessorequals.json
```

要使用 PowerShell 部署此示例模板，请使用：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/lessorequals.json 
```

## <a name="next-steps"></a>后续步骤
* 有关 Azure 资源管理器模板中各部分的说明，请参阅[创作 Azure 资源管理器模板](resource-group-authoring-templates.md)。
* 要合并多个模板，请参阅[将链接的模板与 Azure 资源管理器配合使用](resource-group-linked-templates.md)。
* 若要在创建资源类型时迭代指定的次数，请参阅[在 Azure 资源管理器中创建多个资源实例](resource-group-create-multiple.md)。
* 若要查看如何部署已创建的模板，请参阅[使用 Azure 资源管理器模板部署应用程序](resource-group-template-deploy.md)。

