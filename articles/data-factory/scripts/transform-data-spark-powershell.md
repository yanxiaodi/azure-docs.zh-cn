---
title: PowerShell 脚本 - 使用数据工厂转换云中的数据 | Microsoft Docs
description: 此 PowerShell 脚本通过在 Azure HDInsight Spark 群集上运行 Spark 程序转换云中的数据。
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/12/2017
ms.openlocfilehash: 973efe90ea1da68e4c4e4b0dbbb4c191be18213d
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2019
ms.locfileid: "70140876"
---
# <a name="powershell-script---transform-data-in-cloud-using-azure-data-factory"></a>PowerShell 脚本 - 使用 Azure 数据工厂转换云中的数据

此示例 PowerShell 脚本通过在 Azure HDInsight Spark 群集上运行 Spark 程序，创建用于转换云中数据的管道。 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>先决条件
* **Azure 存储帐户**。 创建 Python 脚本和输入文件，并将其上传到 Azure 存储。 Spark 程序的输出存储在此存储帐户中。 按需 Spark 群集使用相同的存储帐户作为其主存储。  

### <a name="upload-python-script-to-your-blob-storage-account"></a>将 Python 脚本上传到 Blob 存储帐户
1. 创建包含以下内容的名为 **WordCount_Spark.py** 的 Python 文件： 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
2. 将 **&lt;storageAccountName&gt;** 替换为 Azure 存储帐户的名称。 然后保存文件。 
3. 在 Azure Blob 存储中，创建名为 **adftutorial** 的容器（如果尚不存在）。 
4. 创建名为 **spark** 的文件夹。
5. 在 **spark** 文件夹中创建名为 **script** 的子文件夹。 
6. 将 **WordCount_Spark.py** 文件上传到 **script** 子文件夹。 


### <a name="upload-the-input-file"></a>上传输入文件
1. 创建包含一些文本的名为 **minecraftstory.txt** 的文件。 Spark 程序会统计此文本中的单词数量。 
2. 在 blob 容器的 `spark` 文件夹中创建一个名为 `inputfiles` 的子文件夹。 
3. 将 `minecraftstory.txt` 上传到 `inputfiles` 子文件夹。 

## <a name="sample-script"></a>示例脚本
> [!IMPORTANT]
> 此脚本在硬盘驱动器上的 c:\ 文件夹中创建 JSON 文件，用于定义数据工厂实体（链接服务、数据集和管道）。

[!code-powershell[main](../../../powershell_scripts/data-factory/transform-data-using-spark/transform-data-using-spark.ps1 "Transform data using Spark")]

## <a name="clean-up-deployment"></a>清理部署

运行示例脚本后，可以使用以下命令删除资源组以及与其关联的所有资源：

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
若要从资源组中删除数据工厂，请运行以下命令： 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令：

| Command | 说明 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 创建用于存储所有资源的资源组。 |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | 创建数据工厂。 |
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/set-Azdatafactoryv2linkedservice) | 在数据工厂中创建链接服务。 链接服务可将数据存储或计算链接到数据工厂。 |
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/set-Azdatafactoryv2pipeline) | 在数据工厂中创建管道。 一个管道包含一个或多个可执行特定操作的活动。 在此管道中，spark 活动通过在 Azure HDInsight Spark 群集上运行程序来转换数据。 |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/invoke-Azdatafactoryv2pipeline) | 为管道创建运行。 换而言之，就是运行管道。 |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | 获取管道中活动的运行（活动运行）的相关详细信息。 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组，包括所有嵌套的资源。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/)。

可以在 [Azure 数据工厂 PowerShell 示例](../samples-powershell.md)中找到其他 Azure 数据工厂 PowerShell 脚本示例。
