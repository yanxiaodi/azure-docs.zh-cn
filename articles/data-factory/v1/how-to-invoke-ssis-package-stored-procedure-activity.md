---
title: 使用 Azure 数据工厂调用 SSIS 包 - 存储过程活动 | Microsoft Docs
description: 本文介绍如何使用存储过程活动从 Azure 数据工厂管道调用 SQL Server Integration Services (SSIS) 包。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: jingwang
ms.openlocfilehash: 030617d3afd73c68793ca0a1d6185264c92b791f
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839899"
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>在 Azure 数据工厂中使用存储过程活动调用 SSIS 包
本文介绍如何使用存储过程活动从 Azure 数据工厂管道中调用 SSIS 包。 

> [!NOTE]
> 本文适用于数据工厂版本 1。 如果使用当前版本的数据工厂服务，请参阅[使用存储过程活动调用 SSIS 包](../how-to-invoke-ssis-package-stored-procedure-activity.md)。

## <a name="prerequisites"></a>系统必备

### <a name="azure-sql-database"></a>Azure SQL 数据库 
本文中的演练使用托管 SSIS 目录的 Azure SQL 数据库。 还可使用 Azure SQL 数据库托管实例。

### <a name="create-an-azure-ssis-integration-runtime"></a>创建 Azure-SSIS 集成运行时
如果还没有 Azure-SSIS 集成运行时，请按照[教程：部署 SSIS 包](../tutorial-create-azure-ssis-runtime-portal.md)。 无法使用数据工厂版本 1 创建 Azure-SSIS 集成运行时。 

## <a name="azure-powershell"></a>Azure PowerShell
在此部分中，将使用 Azure PowerShell 创建数据工厂管道，管道中包含可调用 SSIS 包的存储过程活动。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

按[如何安装和配置 Azure PowerShell](/powershell/azure/install-az-ps) 中的说明安装最新的 Azure PowerShell 模块。

### <a name="create-a-data-factory"></a>创建数据工厂
下列过程提供创建数据工厂的步骤。 可在数据工厂中使用存储过程活动创建管道。 存储过程活动在 SSISDB 数据库中执行存储过程，运行 SSIS 包。

1. 为资源组名称定义一个变量，稍后会在 PowerShell 命令中使用该变量。 将以下命令文本复制到 PowerShell，在双引号中指定 [Azure 资源组](../../azure-resource-manager/resource-group-overview.md)的名称，然后运行命令。 例如：`"adfrg"`。 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    如果该资源组已存在，请勿覆盖它。 为 `$ResourceGroupName` 变量分配另一个值，然后再次运行命令
2. 若要创建 Azure 资源组，请运行以下命令： 

    ```powershell
    $ResGrp = New-AzResourceGroup $resourceGroupName -location 'eastus'
    ``` 
    如果该资源组已存在，请勿覆盖它。 为 `$ResourceGroupName` 变量分配另一个值，然后再次运行命令。 
3. 定义一个用于数据工厂名称的变量。 

    > [!IMPORTANT]
    >  更新数据工厂名称，使之全局唯一。 

    ```powershell
    $DataFactoryName = "ADFTutorialFactory";
    ```

5. 若要创建数据工厂，请运行以下**新建 AzDataFactory** cmdlet，使用 $ResGrp 变量中的 Location 和 ResourceGroupName 属性： 
    
    ```powershell       
    $df = New-AzDataFactory -ResourceGroupName $ResourceGroupName -Name $dataFactoryName -Location "East US"
    ```

请注意以下几点：

* Azure 数据工厂的名称必须全局唯一。 如果收到以下错误，请更改名称并重试。

    ```
    The specified Data Factory name 'ADFTutorialFactory' is already in use. Data Factory names must be globally unique.
    ```
* 若要创建数据工厂实例，用于登录到 Azure 的用户帐户必须属于**参与者**或**所有者**角色，或者是 Azure 订阅的**管理员**。

### <a name="create-an-azure-sql-database-linked-service"></a>创建 Azure SQL 数据库链接服务
创建一个链接服务，将托管 SSIS 目录的 Azure SQL 数据库链接到数据工厂。 数据工厂使用此链接服务中的信息连接到 SSISDB 数据库，并执行存储过程来运行 SSIS 包。 

1. 在 C:\ADF\RunSSISPackage 文件夹中创建一个名为 AzureSqlDatabaseLinkedService.json 的 JSON 文件，并在其中包含以下内容   ： 

    > [!IMPORTANT]
    > 保存文件之前，请将 &lt;servername&gt;、&lt;username&gt;@&lt;servername&gt; 和 &lt;password&gt; 替换为 Azure SQL 数据库的值。

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=SSISDB;User ID=<username>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
        }
    ```
2. 在 Azure PowerShell 中，切换到 C:\ADF\RunSSISPackage 文件夹   。
3. 运行 **New-AzDataFactoryLinkedService** cmdlet 以创建链接服务：**AzureSqlDatabaseLinkedService**。 

    ```powershell
    New-AzDataFactoryLinkedService $df -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-an-output-dataset"></a>创建输出数据集
此输出数据集是一个虚拟数据集，用于驱动管道的计划。 注意，频率设置为“小时”，间隔设置为“1”。 因此，管道在启动至结束期间，一小时运行一次。 

1. 创建一个 OutputDataset.json 文件，并在其中包含以下内容： 
    
    ```json
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": { },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
2. 运行**新建 AzDataFactoryDataset** cmdlet 来创建数据集。 

    ```powershell
    New-AzDataFactoryDataset $df -File ".\OutputDataset.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>使用存储过程活动创建管道 
在此步骤中，使用存储过程活动创建管道。 该活动调用 sp_executesql 存储过程来运行 SSIS 包。 

1. 在 C:\ADF\RunSSISPackage 文件夹中创建一个名为 MyPipeline.json 的 JSON 文件，并在其中包含以下内容   ：

    > [!IMPORTANT]
    > 保存文件之前，请将 &lt;folder name&gt;、&lt;project name&gt; 和 &lt;package name&gt; 替换为 SSIS 目录中文件夹、项目和包的名称。

    ```json
    {
        "name": "MyPipeline",
        "properties": {
            "activities": [{
                "name": "SprocActivitySample",
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_executesql",
                    "storedProcedureParameters": {
                        "stmt": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<folder name>', @project_name=N'<project name>', @package_name=N'<package name>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                    }
                },
                "outputs": [{
                    "name": "sprocsampleout"
                }],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }],
            "start": "2017-10-01T00:00:00Z",
            "end": "2017-10-01T05:00:00Z",
            "isPaused": false
        }
    }    
    ```

2. 若要创建管道 **RunSSISPackagePipeline**，请运行**新建 AzDataFactoryPipeline** cmdlet。

    ```powershell
    $DFPipeLine = New-AzDataFactoryPipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

### <a name="monitor-the-pipeline-run"></a>监视管道运行

1. 运行**Get AzDataFactorySlice**以获取有关所有切片的输出数据集 * *，这是管道的输出表的详细信息。

    ```powershell
    Get-AzDataFactorySlice $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```
    请注意，此处指定的 StartDateTime 与在管道 JSON 中指定的开始时间是相同的。 
1. 运行 **Get-AzDataFactoryRun**，获取特定切片的活动运行详细信息。

    ```powershell
    Get-AzDataFactoryRun $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```

    可以继续运行此 cmdlet，直到切片进入“就绪”状态或“失败”状态。   

    可在 Azure SQL 服务器中针对 SSISDB 数据库运行以下查询，验证是否执行了该包。 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>后续步骤
有关存储过程活动的详细信息，请参阅[存储过程活动](data-factory-stored-proc-activity.md)一文。

