---
title: 使用 Azure 数据工厂以增量方式复制表 | Microsoft Docs
description: 在本教程中，我们将创建一个 Azure 数据工厂管道，它能够以增量方式将 Azure SQL 数据库中的数据复制到 Azure Blob 存储。
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/11/2018
ms.author: yexu
ms.openlocfilehash: 3626e68c8cedfdd2d22f47cd92d6e7c4b8b5d180
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806373"
---
# <a name="incrementally-load-data-from-an-azure-sql-database-to-azure-blob-storage"></a>以增量方式将 Azure SQL 数据库中的数据加载到 Azure Blob 存储
在本教程中，请创建一个带管道的 Azure 数据工厂，将增量数据从 Azure SQL 数据库中的表加载到 Azure Blob 存储。 

在本教程中执行以下步骤：

> [!div class="checklist"]
> * 准备用于存储水印值的数据存储。
> * 创建数据工厂。
> * 创建链接服务。 
> * 创建源、接收器和水印数据集。
> * 创建管道。
> * 运行管道。
> * 监视管道运行。 
> * 查看结果
> * 向源添加更多数据。
> * 再次运行管道。
> * 监视第二个管道运行
> * 查看第二个运行的结果


## <a name="overview"></a>概述
下面是高级解决方案示意图： 

![以增量方式加载数据](media/tutorial-Incremental-copy-portal/incrementally-load.png)

下面是创建此解决方案所要执行的重要步骤： 

1. **选择水印列**。
    在源数据存储中选择一个列，该列可用于将每个运行的新记录或已更新记录切片。 通常，在创建或更新行时，此选定列中的数据（例如 last_modify_time 或 ID）会不断递增。 此列中的最大值用作水印。

2. **准备用于存储水印值的数据存储**。 本教程在 SQL 数据库中存储水印值。
    
3. **创建采用以下工作流的管道**： 
    
    此解决方案中的管道具有以下活动：
  
    * 创建两个 Lookup 活动。 使用第一个 Lookup 活动检索上一个水印值。 使用第二个 Lookup 活动检索新的水印值。 这些水印值会传递到 Copy 活动。 
    * 创建 Copy 活动，用于复制源数据存储中其水印列值大于旧水印值但小于新水印值的行。 然后，该活动将源数据存储中的增量数据作为新文件复制到 Blob 存储。 
    * 创建 StoredProcedure 活动，用于更新下一次运行的管道的水印值。 


如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="prerequisites"></a>先决条件
* **Azure SQL 数据库**。 将数据库用作源数据存储。 如果没有 SQL 数据库，请参阅[创建 Azure SQL 数据库](../sql-database/sql-database-get-started-portal.md)，了解创建该数据库的步骤。
* **Azure 存储**。 将 Blob 存储用作接收器数据存储。 如果没有存储帐户，请参阅[创建存储帐户](../storage/common/storage-quickstart-create-account.md)以获取创建步骤。 创建名为 adftutorial 的容器。 

### <a name="create-a-data-source-table-in-your-sql-database"></a>在 SQL 数据库中创建数据源表
1. 打开 SQL Server Management Studio。 在“服务器资源管理器”中  ，右键单击数据库，然后选择“新建查询”。 

2. 针对 SQL 数据库运行以下 SQL 命令，创建名为 `data_source_table` 的表作为数据源存储： 
    
    ```sql
    create table data_source_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );

    INSERT INTO data_source_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'aaaa','9/1/2017 12:56:00 AM'),
    (2, 'bbbb','9/2/2017 5:23:00 AM'),
    (3, 'cccc','9/3/2017 2:36:00 AM'),
    (4, 'dddd','9/4/2017 3:21:00 AM'),
    (5, 'eeee','9/5/2017 8:06:00 AM');
    ```
    本教程使用 LastModifytime 作为水印列。 下表显示了数据源存储中的数据：

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    ```

### <a name="create-another-table-in-your-sql-database-to-store-the-high-watermark-value"></a>在 SQL 数据库中创建另一个表，用于存储高水印值
1. 针对 SQL 数据库运行以下 SQL 命令，创建名为 `watermarktable` 的表，用于存储水印值：  
    
    ```sql
    create table watermarktable
    (
    
    TableName varchar(255),
    WatermarkValue datetime,
    );
    ```
2. 使用源数据存储的表名设置高水印的默认值。 在本教程中，表名为 data_source_table。

    ```sql
    INSERT INTO watermarktable
    VALUES ('data_source_table','1/1/2010 12:00:00 AM')    
    ```
3. 查看 `watermarktable` 表中的数据。
    
    ```sql
    Select * from watermarktable
    ```
    输出： 

    ```
    TableName  | WatermarkValue
    ----------  | --------------
    data_source_table | 2010-01-01 00:00:00.000
    ```

### <a name="create-a-stored-procedure-in-your-sql-database"></a>在 SQL 数据库中创建存储过程 

运行以下命令，在 SQL 数据库中创建存储过程：

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN
    
    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName
    
END
```

## <a name="create-a-data-factory"></a>创建数据工厂

1. 启动 **Microsoft Edge** 或 **Google Chrome** Web 浏览器。 目前，仅 Microsoft Edge 和 Google Chrome Web 浏览器支持数据工厂 UI。
2. 在左侧菜单中，选择“创建资源”   > “Analytics”   > “数据工厂”  ： 
   
   ![在“新建”窗格中选择“数据工厂”](./media/doc-common-process/new-azure-data-factory-menu.png)

3. 在“新建数据工厂”页中，输入 ADFIncCopyTutorialDF 作为**名称**。   
 
   Azure 数据工厂的名称必须 **全局唯一**。 如果看到红色感叹号和以下错误，请更改数据工厂的名称（例如改为 yournameADFIncCopyTutorialDF），并重新尝试创建。 有关数据工厂项目命名规则，请参阅[数据工厂 - 命名规则](naming-rules.md)一文。
  
       `Data factory name "ADFIncCopyTutorialDF" is not available`
4. 选择要在其中创建数据工厂的 Azure **订阅**。 
5. 对于**资源组**，请执行以下步骤之一：
     
      - 选择“使用现有资源组”，并从下拉列表选择现有的资源组。  
      - 选择“新建”，并输入资源组的名称。    
         
        若要了解有关资源组的详细信息，请参阅 [使用资源组管理 Azure 资源](../azure-resource-manager/resource-group-overview.md)。  
6. 选择“V2”  作为“版本”  。
7. 选择数据工厂的**位置**。 下拉列表中仅显示支持的位置。 数据工厂使用的数据存储（Azure 存储、Azure SQL 数据库，等等）和计算资源（HDInsight 等）可以位于其他区域中。
8. 单击“创建”。       
9. 创建完成后，可以看到图中所示的“数据工厂”页。 
   
   ![数据工厂主页](./media/doc-common-process/data-factory-home-page.png)
10. 单击“创作和监视”磁贴，在单独的选项卡中启动 Azure 数据工厂用户界面 (UI)。 

## <a name="create-a-pipeline"></a>创建管道
本教程创建包含两个 Lookup 活动、一个 Copy 活动和一个 StoredProcedure 活动的管道，这些活动链接在一个管道中。 

1. 在数据工厂 UI 的“入门”页中，单击“创建管道”磁贴。   

   ![数据工厂 UI 的“入门”页](./media/doc-common-process/get-started-page.png)    
3. 在管道的“属性”窗口的“常规”页中，输入名称“IncrementalCopyPipeline”  。   

4. 请添加第一个查找活动，获取旧水印值。 在“活动”工具箱中展开“常规”，   将**查找**活动拖放到管道设计器图面。 将活动的名称更改为 **LookupOldWaterMarkActivity**。

   ![第一个查找活动 - 名称](./media/tutorial-incremental-copy-portal/first-lookup-name.png)
5. 切换到“设置”选项卡，针对“源数据集”单击“+ 新建”。    在此步骤中，请创建一个代表 **watermarktable** 中数据的数据集。 此表包含在前一复制操作中使用过的旧水印。 

6. 在“新建数据集”窗口中，选择“Azure SQL 数据库”，然后单击“继续”。    此时可以看到为数据集打开了一个新窗口。 

7. 在数据集的“设置属性”属性窗口中，  输入“WatermarkDataset”  作为“名称”  。

8. 对于“链接服务”  ，选择“新建”  ，然后执行下列步骤：

    1. 对于“名称”，请输入 **AzureSqlDatabaseLinkedService**。  
    2. 对于“服务器名称”，请选择 Azure SQL Server  。
    3. 从下拉列表中选择“数据库名称”。  
    4. 请输入“用户名”   & “密码”  。 
    5.  若要测试到 Azure SQL 数据库的连接，请单击“测试连接”。
    6. 单击“完成”  。
    7. 对于“链接服务”，确认选择了“AzureSqlDatabaseLinkedService”。  
       
        ![“新建链接服务”窗口](./media/tutorial-incremental-copy-portal/azure-sql-linked-service-settings.png)
    8. 选择“完成”。 
9. 在“连接”  选项卡中，对于“表”，选择“[dbo].[watermarktable]”。   若要预览表中的数据，请单击“预览数据”。 

    ![水印数据集 - 连接设置](./media/tutorial-incremental-copy-portal/watermark-dataset-connection-settings.png)
10. 通过单击顶部的管道选项卡，或者单击左侧树状视图中管道的名称，切换到管道编辑器。 在**查找**活动的属性窗口中，确认对于“源数据集”字段，是否已选择 **WatermarkDataset**。  

11. 在“活动”工具箱中展开“常规”，   将另一**查找**活动拖放到管道设计器图面，然后在属性窗口的“常规”选项卡中将名称设置为 **LookupNewWaterMarkActivity**。  此“查找”活动从特定表获取新的水印值，该表包含的源数据可以复制到目标。 

12. 在第二个“复制”活动的属性窗口中切换到“设置”选项卡，然后单击“新建”。    请创建一个数据集，使之指向源表，该表包含新的水印值（LastModifyTime 的最大值）。 

13. 在“新建数据集”窗口中，选择“Azure SQL 数据库”，然后单击“继续”。    
14. 在属性窗口的“常规”选项卡中，对于“名称”输入“SourceDataset”   。  为“链接服务”选择“AzureSqlDatabaseLinkedService”。  
15. 对于“表”，请选择“[dbo].[data_source_table]”。  本教程后面需指定一个针对此数据集的查询。 此查询优先于在此步骤中指定的表。
16. 选择“完成”。  
17. 通过单击顶部的管道选项卡，或者单击左侧树状视图中管道的名称，切换到管道编辑器。 在**查找**活动的属性窗口中，确认对于“源数据集”字段，是否已选择 **SourceDataset**。  
18. 对于“使用查询”字段，请选择“查询”，   然后输入以下查询：仅从 **data_source_table** 中选择 **LastModifytime** 的最大值。 请确保还选中了“仅第一行”  。

    ```sql
    select MAX(LastModifytime) as NewWatermarkvalue from data_source_table
    ```

    ![第二个查找活动 - 查询](./media/tutorial-incremental-copy-portal/query-for-new-watermark.png)
19. 在“活动”工具箱中，展开“移动和转换”  ，然后从“活动”工具箱拖放“复制”  活动，并将名称设置为“IncrementalCopyActivity”  。  

20. 通过将附加到“查找”活动的**绿色按钮**拖至“复制”活动，**将两个“查找”活动都连接到“复制”活动**。 看到“复制”活动的边框颜色变为蓝色时，松开鼠标按键。 

    ![将“查找”活动连接到“复制”活动](./media/tutorial-incremental-copy-portal/connection-lookups-to-copy.png)
21. 选择 **“复制”活动**，确认在“属性”窗口看到活动的属性。  

22. 在“属性”窗口中切换到“源”选项卡，然后执行以下步骤：  

    1. 对于“源数据集”字段，请选择“SourceDataset”。   
    2. 对于“使用查询”字段，请选择“查询”。   
    3. 对于“查询”字段，请输入以下 SQL 查询。  

        ```sql
        select * from data_source_table where LastModifytime > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and LastModifytime <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'
        ```
    
        ![“复制”活动 - 源](./media/tutorial-incremental-copy-portal/copy-activity-source.png)
23. 切换到“接收器”选项卡。对于“接收器数据集”字段，请单击“+ 新建”。    

24. 在本教程中，接收器数据存储属于“Azure Blob 存储”类型。 因此，请在“新建数据集”窗口中选择“Azure Blob 存储”，然后单击“继续”。    
25. 在“选择格式”窗口中选择数据的格式类型，然后单击“继续”。  
25. 在“设置属性”窗口中，对于“名称”输入“SinkDataset”   。  对于“链接服务”  ，选择“+新建”  。 此步骤创建一个连接（链接服务），用于连接到 **Azure Blob 存储**。
26. 在“新建链接服务(Azure Blob 存储)”  窗口中执行以下步骤： 

    1. 输入 **AzureStorageLinkedService** 作为**名称**。 
    2. 对于“存储帐户名称”，请选择自己的 Azure 存储帐户。 
    3. 测试连接，然后单击  “完成”。 

27. 在“设置属性”窗口中，对于“链接服务”，确认选择了“AzureStorageLinkedService”。    然后选择“完成”。 
28. 转到 SinkDataset 的“连接”  选项卡，然后执行以下步骤：
    1. 对于“文件路径”字段  ，请输入“adftutorial/incrementalcopy”  。 **adftutorial** 是 Blob 容器名称，**incrementalcopy** 是文件夹名称。 此代码片段假设 Blob 存储中有一个名为 adftutorial 的 Blob 容器。 创建容器（如果不存在），或者将容器设置为现有容器的名称。 Azure 数据工厂自动创建输出文件夹 **incrementalcopy**（如果不存在）。 对于“文件路径”，也可使用“浏览”按钮导航到 Blob 容器中的某个文件夹。  
    2. 对于“文件路径”字段的“文件”部分，选择“添加动态内容 [Alt+P]”，然后在打开的窗口中输入 `@CONCAT('Incremental-', pipeline().RunId, '.txt')`。    然后选择“完成”。  文件名是使用表达式动态生成的。 每次管道运行都有唯一的 ID。 “复制”活动使用运行 ID 生成文件名。 

28. 通过单击顶部的管道选项卡，或者单击左侧树状视图中管道的名称，切换到**管道**编辑器。 
29. 在“活动”工具箱中，展开“常规”  ，然后将**存储过程**活动从“活动”工具箱拖放到管道设计器图面。   将**复制**活动的绿色（成功）输出**连接**到**存储过程**活动。 

24. 在管道设计器中选择“存储过程活动”，  将其名称更改为 **StoredProceduretoWriteWatermarkActivity**。 

25. 切换到“SQL 帐户”选项卡，对于“链接服务”，请选择“AzureSqlDatabaseLinkedService”  。   

26. 切换到“存储过程”  选项卡，然后执行以下步骤： 

    1. 对于“存储过程名称”  ，请选择 **usp_write_watermark**。 
    2. 若要指定存储过程参数的值，请单击“导入参数”，然后为参数输入以下值：  

        | Name | 类型 | 值 | 
        | ---- | ---- | ----- | 
        | LastModifiedtime | DateTime | @{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue} |
        | TableName | String | @{activity('LookupOldWaterMarkActivity').output.firstRow.TableName} |

    ![存储过程活动 - 存储过程设置](./media/tutorial-incremental-copy-portal/sproc-activity-stored-procedure-settings.png)
27. 若要验证管道设置，请单击工具栏中的“验证”  。 确认没有任何验证错误。 若要关闭“管道验证报告”窗口，请单击 >>。    

28. 选择“全部发布”按钮，将实体（链接服务、数据集和管道）发布到 Azure 数据工厂服务。  等待“发布成功”消息出现。 


## <a name="trigger-a-pipeline-run"></a>触发管道运行
1. 单击工具栏中的“添加触发器”，然后单击“立即触发”。   

2. 在“管道运行”窗口中选择“完成”。   

## <a name="monitor-the-pipeline-run"></a>监视管道运行

1. 在左侧切换到“监视”选项卡。  可以看到手动触发器触发的管道运行的状态。 单击“刷新”按钮刷新列表。  
    
2. 若要查看与此管道运行关联的活动运行，请单击“操作”列中的第一个链接（“查看活动运行”）。   单击顶部的“管道”可以回到上一视图。  单击“刷新”按钮刷新列表。 


## <a name="review-the-results"></a>查看结果
1. 使用 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)之类的工具连接到 Azure 存储帐户。 验证 **adftutorial** 容器的 **incrementalcopy** 文件夹中是否创建了一个输出文件。

    ![第一个输出文件](./media/tutorial-incremental-copy-portal/first-output-file.png)
2. 打开输出文件，请注意，所有数据已从 **data_source_table** 复制到 Blob 文件。 

    ```
    1,aaaa,2017-09-01 00:56:00.0000000
    2,bbbb,2017-09-02 05:23:00.0000000
    3,cccc,2017-09-03 02:36:00.0000000
    4,dddd,2017-09-04 03:21:00.0000000
    5,eeee,2017-09-05 08:06:00.0000000
    ```
3. 在 `watermarktable` 中查看最新值。 可看到水印值已更新。

    ```sql
    Select * from watermarktable
    ```
    
    输出如下：
 
    | TableName | WatermarkValue |
    | --------- | -------------- |
    | data_source_table | 2017-09-05    8:06:00.000 | 

## <a name="add-more-data-to-source"></a>向源添加更多数据

在 SQL 数据库（数据源存储）中插入新数据。

```sql
INSERT INTO data_source_table
VALUES (6, 'newdata','9/6/2017 2:23:00 AM')

INSERT INTO data_source_table
VALUES (7, 'newdata','9/7/2017 9:01:00 AM')
``` 

SQL 数据库中的更新数据为：

```
PersonID | Name | LastModifytime
-------- | ---- | --------------
1 | aaaa | 2017-09-01 00:56:00.000
2 | bbbb | 2017-09-02 05:23:00.000
3 | cccc | 2017-09-03 02:36:00.000
4 | dddd | 2017-09-04 03:21:00.000
5 | eeee | 2017-09-05 08:06:00.000
6 | newdata | 2017-09-06 02:23:00.000
7 | newdata | 2017-09-07 09:01:00.000
```


## <a name="trigger-another-pipeline-run"></a>触发另一管道运行
1. 切换到“编辑”  选项卡。单击树状视图中的管道（如果未在设计器中打开）。 

2. 单击工具栏中的“添加触发器”，然后单击“立即触发”。   


## <a name="monitor-the-second-pipeline-run"></a>监视第二个管道运行

1. 在左侧切换到“监视”选项卡。  可以看到手动触发器触发的管道运行的状态。 单击“刷新”按钮刷新列表。  
    
2. 若要查看与此管道运行关联的活动运行，请单击“操作”列中的第一个链接（“查看活动运行”）。   单击顶部的“管道”可以回到上一视图。  单击“刷新”按钮刷新列表。 


## <a name="verify-the-second-output"></a>验证第二个输出

1. 在 Blob 存储中，可以看到另一文件已创建。 在本教程中，新文件名为 `Incremental-<GUID>.txt`。 打开该文件，会看到其中包含两行记录。

    ```
    6,newdata,2017-09-06 02:23:00.0000000
    7,newdata,2017-09-07 09:01:00.0000000    
    ```
2. 在 `watermarktable` 中查看最新值。 可看到水印值已再次更新。

    ```sql
    Select * from watermarktable
    ```
    示例输出： 
    
    | TableName | WatermarkValue |
    | --------- | --------------- |
    | data_source_table | 2017-09-07 09:01:00.000 |


     
## <a name="next-steps"></a>后续步骤
已在本教程中执行了以下步骤： 

> [!div class="checklist"]
> * 准备用于存储水印值的数据存储。
> * 创建数据工厂。
> * 创建链接服务。 
> * 创建源、接收器和水印数据集。
> * 创建管道。
> * 运行管道。
> * 监视管道运行。 
> * 查看结果
> * 向源添加更多数据。
> * 再次运行管道。
> * 监视第二个管道运行
> * 查看第二个运行的结果

在本教程中，管道将数据从 SQL 数据库中的单个表复制到了 Blob 存储。 转到下面的教程，了解如何将数据从本地 SQL Server 数据库中的多个表复制到 SQL 数据库。 

> [!div class="nextstepaction"]
>[以增量方式将数据从 SQL Server 中的多个表加载到 Azure SQL 数据库](tutorial-incremental-copy-multiple-tables-portal.md)



