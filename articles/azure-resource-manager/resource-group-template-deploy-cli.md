---
title: 使用 Azure CLI 和模板部署资源 | Microsoft Docs
description: 使用 Azure 资源管理器和 Azure CLI 将资源部署到 Azure。 资源在 Resource Manager 模板中定义。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: tomfitz
ms.openlocfilehash: bd43e919cc0b2bcf1d130c7e616b7da064abcc65
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69971017"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>使用 Resource Manager 模板和 Azure CLI 部署资源

本文介绍如何将 Azure CLI 与资源管理器模板配合使用将资源部署到 Azure。 如果不熟悉部署和管理 Azure 解决方案的概念，请参阅 [Azure 资源管理器概述](resource-group-overview.md)。  

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

如果没有安装 Azure CLI，则可使用 [Cloud Shell](#deploy-template-from-cloud-shell)。

## <a name="deployment-scope"></a>部署范围

可将部署目标设定为 Azure 订阅或订阅中的资源组。 大多数情况下，我们会将以资源组指定为部署目标。 可以使用订阅部署在整个订阅中应用策略和角色分配。 还可以使用订阅部署创建资源组并向其部署资源。 根据部署范围使用不同的命令。

若要部署到**资源组**，请使用 [az group deployment create](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)：

```azurecli
az group deployment create --resource-group <resource-group-name> --template-file <path-to-template>
```

若要部署到**订阅**，请使用 [az deployment create](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create)：

```azurecli
az deployment create --location <location> --template-file <path-to-template>
```

目前，仅通过 REST API 支持管理组部署。 请参阅[使用资源管理器模板和资源管理器 REST API 部署资源](resource-group-template-deploy-rest.md)。

本文中的示例使用资源组部署。 有关订阅部署的详细信息，请参阅[在订阅级别创建资源组和资源](deploy-to-subscription.md)。

## <a name="deploy-local-template"></a>部署本地模板

将资源部署到 Azure 时，执行以下操作：

1. 登录到 Azure 帐户
2. 创建用作已部署资源的容器的资源组。 资源组名称只能包含字母数字字符、句点、下划线、连字符和括号。 它最多可以包含 90 个字符。 它不能以句点结尾。
3. 将定义了要创建的资源的模板部署到资源组

模板可以包括可用于自定义部署的参数。 例如，可以提供为特定环境（如开发环境、测试环境和生产环境）定制的值。 示例模板定义了存储帐户 SKU 的参数。 

以下示例将创建一个资源组，并从本地计算机部署模板：

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

部署可能需要几分钟才能完成。 完成后，会看到一条包含结果的消息：

```azurecli
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-template"></a>部署远程模板

可能更愿意将 Resource Manager 模板存储在外部位置，而不是将它们存储在本地计算机上。 可以将模板存储在源控件存储库（例如 GitHub）中。 另外，还可以将其存储在 Azure 存储帐户中，以便在组织中共享访问。

若要部署外部模板，请使用 **template-uri** 参数。 使用示例中的 URI 从 GitHub 部署示例模板。

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

前面的示例要求模板的 URI 可公开访问，它适用于大多数情况，因为模板应该不会包含敏感数据。 如果需要指定敏感数据（如管理员密码），请以安全参数的形式传递该值。 但是，如果不希望模板可公开访问，可以通过将其存储在专用存储容器中来保护它。 有关部署需要共享访问签名 (SAS) 令牌的模板的信息，请参阅[部署具有 SAS 令牌的专用模板](resource-manager-cli-sas-token.md)。

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../includes/resource-manager-cloud-shell-deploy.md)]

在 Cloud Shell 中使用以下命令：

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az group deployment create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="redeploy-when-deployment-fails"></a>部署失败时，重新部署

此功能也称为“出错时回滚”。 部署失败时，可以自动重新部署部署历史记录中先前成功的部署。 若要指定重新部署，请在部署命令中使用 `--rollback-on-error` 参数。 如果基础结构部署存在一个已知良好的状态，并且你希望还原到此状态，则此功能非常有用。 有许多需要注意的问题和限制：

- 重新部署使用与以前运行它时相同的参数以相同的方式运行。 无法更改参数。
- 以前的部署是使用[完整模式](./deployment-modes.md#complete-mode)运行的。 以前的部署中未包括的任何资源都将被删除，任何资源配置都将设置为以前的状态。 请确保你完全理解[部署模式](./deployment-modes.md)。
- 重新部署只会影响资源，而不会影响任何数据更改。
- 只有资源组部署支持此功能，订阅级部署不支持此功能。 有关订阅级部署的详细信息，请参阅[在订阅级别创建资源组和资源](./deploy-to-subscription.md)。

若要使用此选项，部署必须具有唯一的名称，以便可以在历史记录中标识它们。 如果没有唯一名称，则当前失败的部署可能会覆盖历史记录中以前成功的部署。 只能将此选项用于根级别部署。 从嵌套模板进行的部署不可用于重新部署。

若要重新部署最后一个成功的部署，请将 `--rollback-on-error` 参数添加为标志。

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error
```

若要重新部署某个特定部署，请使用 `--rollback-on-error` 参数并提供部署名称。

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment02 \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error ExampleDeployment01
```

指定的部署必须已成功。

## <a name="parameters"></a>Parameters

若要传递参数值，可以使用内联参数或参数文件。

### <a name="inline-parameters"></a>内联参数。

若要传递内联参数，请在 `parameters` 中提供值。 例如，若要在 Bash shell 中将字符串和数组传递给模板，请使用：

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

如果将 Azure CLI 与 Windows 命令提示符 (CMD) 或 PowerShell 一起使用, 请按以下格式传递此数组`exampleArray="['value1','value2']"`:。

还可以获取文件的内容并将该内容作为内联参数提供。

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

当需要提供配置值时，从文件中获取参数值非常有用。 例如，可以[为 Linux 虚拟机提供 cloud-init 值](../virtual-machines/linux/using-cloud-init.md)。

arrayContent.json 格式为：

```json
[
    "value1",
    "value2"
]
```

### <a name="parameter-files"></a>参数文件

与在脚本中以内联值的形式传递参数相比，可能会发现使用包含参数值的 JSON 文件更为容易。 参数文件必须是本地文件。 Azure CLI 不支持外部参数文件。

有关参数文件的详细信息, 请参阅[Create 资源管理器 parameter file](resource-manager-parameter-files.md)。

若要传递本地参数文件，请使用 `@` 指定名为 storage.parameters.json 的本地文件。

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

## <a name="test-a-template-deployment"></a>测试模板部署

若要测试模板和参数值而不实际部署任何资源，请使用 [az group deployment validate](/cli/azure/group/deployment#az-group-deployment-validate)。 

```azurecli-interactive
az group deployment validate \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

如果未检测到错误，则该命令将返回有关测试部署的信息。 需要特别注意的是，**error** 值为 null。

```azurecli
{
  "error": null,
  "properties": {
      ...
```

如果检测到错误，则该命令将返回一条错误消息。 例如，如果为存储帐户 SKU 传递不正确的值，将返回以下错误：

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template validation failed: 'The provided value 'badSKU' for the template parameter 
      'storageAccountType' at line '13' and column '20' is not valid. The parameter value is not part of the allowed 
      value(s): 'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.",
    "target": null
  },
  "properties": null
}
```

如果模板有语法错误，该命令将返回一个错误，指示它无法分析该模板。 该消息会指出分析错误的行号和位置。

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template parse failed: 'After parsing a value an unexpected character was encountered:
      \". Path 'variables', line 31, position 3.'.",
    "target": null
  },
  "properties": null
}
```

## <a name="next-steps"></a>后续步骤

- 本文中的示例将资源部署到默认订阅中的资源组。 若要使用其他订阅，请参阅[管理多个 Azure 订阅](/cli/azure/manage-azure-subscriptions-azure-cli)。
- 若要指定如何处理存在于资源组中但未在模板中定义的资源，请参阅 [Azure 资源管理器部署模式](deployment-modes.md)。
- 若要了解如何在模板中定义参数，请参阅[了解 Azure 资源管理器模板的结构和语法](resource-group-authoring-templates.md)。
- 有关解决常见部署错误的提示，请参阅[排查使用 Azure 资源管理器时的常见 Azure 部署错误](resource-manager-common-deployment-errors.md)。
- 有关部署需要 SAS 令牌的模板的信息，请参阅[使用 SAS 令牌部署专用模板](resource-manager-cli-sas-token.md)。
- 若要安全地将服务扩展到多个区域，请参阅 [Azure 部署管理器](deployment-manager-overview.md)。
