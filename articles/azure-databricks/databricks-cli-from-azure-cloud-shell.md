---
title: '从 Azure Cloud Shell 中使用 Databricks CLI '
description: 了解如何从 Azure Cloud Shell 中使用 Databricks CLI。
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: mamccrea
ms.openlocfilehash: dae481fb477223f149404c6a09cad024bc15cd90
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204962"
---
# <a name="use-databricks-cli-from-azure-cloud-shell"></a>从 Azure Cloud Shell 中使用 Databricks CLI

了解如何从 Azure Cloud Shell 中使用 Databricks CLI 对 Databricks 执行操作。

## <a name="prerequisites"></a>必备组件

* 一个 Azure Databricks 工作区和群集。 有关说明，请参阅 [Azure Databricks 入门](quickstart-create-databricks-workspace-portal.md)。 

* 在 Databricks 中设置个人访问令牌。 有关说明，请参阅[令牌管理](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management)。

## <a name="use-the-azure-cloud-shell"></a>使用 Azure Cloud Shell

1. 登录到 [Azure 门户](https://portal.azure.com)。
 
2. 在右上角单击 **Cloud Shell** 图标。

   ![启动 Cloud Shell](./media/databricks-cli-from-azure-cloud-shell/launch-azure-cloud-shell.png "启动 Azure Cloud Shell")

3. 请确保已选择 **Bash** 作为 Cloud Shell 环境。 可以从下拉列表选项中进行选择，如以下屏幕截图中所示。

   ![选择 Bash 作为 Cloud Shell 环境](./media/databricks-cli-from-azure-cloud-shell/select-bash-for-shell.png "选择 Bash") 

4. 创建可在其中安装 Databricks CLI 的虚拟环境。 在下面的代码片段中，将创建一个名为 `databrickscli` 的虚拟环境。

       virtualenv -p /usr/bin/python2.7 databrickscli

5. 切换到所创建的虚拟环境。

       source databrickscli/bin/activate

6. 安装 Databricks CLI。

       pip install databricks-cli

7. 使用访问令牌通过 Databricks 设置身份验证，该访问令牌必须已创建并已作为先决条件的一部分列出。 请使用以下命令：

       databricks configure --token

    你会收到以下提示：

    * 首先，系统会提示输入 Databricks 主机。 按格式 `https://eastus2.azuredatabricks.net` 输入值。 在这里，**美国东部 2** 是你在其中创建 Azure Databricks 工作区的 Azure 区域。

    * 接下来，系统会提示输入令牌。 输入前面创建的令牌。

完成这些步骤后，便可以开始从 Azure Cloud Shell 中使用 Databricks CLI 了。

## <a name="use-databricks-cli"></a>使用 Databricks CLI

现在可以开始使用 Databricks CLI。 例如，运行以下命令以列出你在工作区中拥有的所有 Databricks 群集。

    databricks clusters list

还可以使用以下命令访问 Databricks 文件系统 (DBFS)。

    databricks fs ls


有关命令的完整参考，请参阅 [Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html)。


## <a name="next-steps"></a>后续步骤

* 若要了解有关 Azure CLI 的详细信息，请参阅 [Azure CLI 概述](../cloud-shell/overview.md)
* 若要查看 Azure CLI 命令的列表，请参阅 [Azure CLI 参考](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest)
* 若要查看 Databricks CLI 命令的列表，请参阅 [Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html)


