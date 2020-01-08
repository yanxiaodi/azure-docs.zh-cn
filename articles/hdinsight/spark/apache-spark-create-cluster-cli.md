---
title: 快速入门：使用 Azure CLI 在 Azure HDInsight 中创建 Apache Spark 群集
description: 本快速入门展示了如何使用 Azure CLI 在 Azure HDInsight 中创建 Apache Spark 群集。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: 72bdab9d7fb5c3019d97ffc4c92257c49ec2b8e5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67066246"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-azure-cli"></a>快速入门：使用 Azure CLI 在 Azure HDInsight 中创建 Apache Spark 群集

在本快速入门中，你将了解如何使用 Azure CLI 在 Azure HDInsight 中创建 Apache Spark 群集。 通过 Apache Spark 可以使用内存处理进行快速数据分析和群集计算。 [Azure 命令行接口 (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 是用于管理 Azure 资源的 Microsoft 跨平台命令行体验。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

Azure CLI。 如果尚未安装 Azure CLI，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 来了解步骤。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-an-apache-spark-cluster"></a>创建 Apache Spark 群集

1. 登录到 Azure 订阅。 如果你打算使用 Azure Cloud Shell，则只需要在代码块的右上角选择“尝试”  。 否则，请输入以下命令：

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. 设置环境变量。 本快速入门中的变量用法基于 Bash。 在其他环境中需要进行细微的更改。 将以下代码片段中的 RESOURCEGROUPNAME、LOCATION、CLUSTERNAME、STORAGEACCOUNTNAME 和 PASSWORD 替换为所需的值。 然后，输入 CLI 命令来设置环境变量。

    ```azurecli-interactive
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME
    export httpCredential='PASSWORD'
    export sshCredentials='PASSWORD'
    
    export AZURE_STORAGE_CONTAINER=$clusterName
    export clusterSizeInNodes=1
    export clusterVersion=3.6
    export clusterType=spark
    export componentVersion=Spark=2.3
    ```

3. 输入以下命令来创建资源组：

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

4. 输入以下命令来创建 Azure 存储帐户：

    ```azurecli-interactive
    az storage account create \
        --name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

5. 通过输入以下命令从 Azure 存储帐户中提取主键，然后将其存储在一个变量中：

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. 输入以下命令来创建 Azure 存储容器：

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. 输入以下命令创建 Apache Spark 群集：

    ```azurecli-interactive
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpCredential \
        --http-user admin \
        --location $location \
        --size $clusterSizeInNodes \
        --ssh-password $sshCredentials \
        --ssh-user sshuser \
        --storage-account $AZURE_STORAGE_ACCOUNT \
        --storage-account-key $AZURE_STORAGE_KEY \
        --storage-default-container $AZURE_STORAGE_CONTAINER \
        --version $clusterVersion
    ```

## <a name="clean-up-resources"></a>清理资源

完成本快速入门后，可以删除群集。 有了 HDInsight，便可以将数据存储在 Azure 存储中，因此可以在群集不用时安全地删除群集。 此外，还需要为 HDInsight 群集付费，即使不用也是如此。 由于群集费用数倍于存储空间费用，因此在群集不用时删除群集可以节省费用。

输入以下命令中的全部或部分来删除资源：

```azurecli-interactive
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $AZURE_STORAGE_ACCOUNT \
    --name $AZURE_STORAGE_CONTAINER

# Remove storage account
az storage account delete \
    --name $AZURE_STORAGE_ACCOUNT \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你学习了如何使用 Azure CLI 在 Azure HDInsight 中创建 Apache Spark 群集。  转到下一教程，了解如何使用 HDInsight Spark 群集针对示例数据运行交互式查询。

> [!div class="nextstepaction"]
> [在 Apache Spark 上运行交互式查询](./apache-spark-load-data-run-query.md)
