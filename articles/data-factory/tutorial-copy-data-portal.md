---
title: 使用 Azure 门户创建数据工厂管道 | Microsoft Docs
description: 本教程分步说明了如何使用 Azure 门户创建带管道的数据工厂。 该管道通过复制活动将数据从 Azure Blob 存储复制到 SQL 数据库。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 06/21/2018
ms.author: jingwang
ms.openlocfilehash: 9a2ad8070c0406446f53c1bcaa6d341cdca0bb2a
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2019
ms.locfileid: "70140718"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-azure-data-factory"></a>使用 Azure 数据工厂，将数据从 Azure Blob 存储复制到 SQL 数据库
在本教程中，请使用 Azure 数据工厂用户界面 (UI) 创建数据工厂。 此数据工厂中的管道将数据从 Azure Blob 存储复制到 SQL 数据库。 本教程中的配置模式适用于从基于文件的数据存储复制到关系数据存储。 如需可以用作源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

> [!NOTE]
> - 如果对数据工厂不熟悉，请参阅 [Azure 数据工厂简介](introduction.md)。

将在本教程中执行以下步骤：

> [!div class="checklist"]
> * 创建数据工厂。
> * 创建包含复制活动的管道。
> * 测试性运行管道。
> * 手动触发管道。
> * 按计划触发管道。
> * 监视管道和活动运行。

## <a name="prerequisites"></a>先决条件
* **Azure 订阅**。 如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。
* **Azure 存储帐户**。 可将 Blob 存储用作  源数据存储。 如果没有存储帐户，请参阅[创建 Azure 存储帐户](../storage/common/storage-quickstart-create-account.md)以获取创建步骤。
* **Azure SQL 数据库**。 将数据库用作  接收器数据存储。 如果没有 SQL 数据库，请参阅[创建 SQL 数据库](../sql-database/sql-database-get-started-portal.md)，了解创建该数据库的步骤。

### <a name="create-a-blob-and-a-sql-table"></a>创建 blob 和 SQL 表

现在，请执行以下步骤来准备本教程所需的 Blob 存储和 SQL 数据库。

#### <a name="create-a-source-blob"></a>创建源 blob

1. 启动记事本。 复制以下文本并将其在磁盘上另存为 **emp.txt** 文件：

    ```
    John,Doe
    Jane,Doe
    ```

1. 在 Blob 存储中创建名为 **adftutorial** 的容器。 在该容器中创建名为 input  的文件夹。 然后，将 **emp.txt** 文件上传到 **input** 文件夹。 请使用 Azure 门户或工具（例如 [Azure 存储资源管理器](https://storageexplorer.com/)）执行这些任务。

#### <a name="create-a-sink-sql-table"></a>创建接收器 SQL 表

1. 使用以下 SQL 脚本在 SQL 数据库中创建 **dbo.emp** 表：

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

1. 允许 Azure 服务访问 SQL Server。 确保 SQL Server 的“允许访问 Azure 服务”  处于“打开”  状态，以便数据工厂可以将数据写入 SQL Server。 若要验证并启用此设置，请转至 Azure SQL Server >“概述”>“设置服务器防火墙”>将“允许访问 Azure 服务”  选项设置为“打开”  。

## <a name="create-a-data-factory"></a>创建数据工厂
在此步骤中，请先创建数据工厂，然后启动数据工厂 UI，在该数据工厂中创建一个管道。 

1. 打开 **Microsoft Edge** 或 **Google Chrome**。 目前，仅 Microsoft Edge 和 Google Chrome Web 浏览器支持数据工厂 UI。
2. 在左侧菜单中，选择“创建资源”   > “Analytics”   > “数据工厂”  ： 
  
   ![在“新建”窗格中选择“数据工厂”](./media/doc-common-process/new-azure-data-factory-menu.png)

3. 在“新建数据工厂”  页的“名称”下输入 **ADFTutorialDataFactory**  。 
 
   Azure 数据工厂的名称必须 *全局唯一*。 如果收到有关名称值的错误消息，请为数据工厂输入另一名称。 （例如 yournameADFTutorialDataFactory）。 有关数据工厂项目的命名规则，请参阅[数据工厂命名规则](naming-rules.md)。
        
     ![新建数据工厂](./media/doc-common-process/name-not-available-error.png)
4. 选择要在其中创建数据工厂的 Azure **订阅**。 
5. 对于“资源组”，请执行以下步骤之一： 
     
    a. 选择“使用现有资源组”，并从下拉列表选择现有的资源组。 

    b. 选择“新建”，并输入资源组的名称。  
         
    若要了解资源组，请参阅[使用资源组管理 Azure 资源](../azure-resource-manager/resource-group-overview.md)。 
6. 在“版本”下选择“V2”。  
7. 在“位置”下选择数据工厂所在的位置。  下拉列表中仅显示支持的位置。 数据工厂使用的数据存储（例如，Azure 存储和 SQL 数据库）和计算资源（例如，Azure HDInsight）可以位于其他区域。
8. 选择“创建”  。 
9. 创建完成后，通知中心内会显示通知。 选择“转到资源”导航到“数据工厂”页。 
10. 选择“创作和监视”，在单独的选项卡中启动数据工厂 UI。 


## <a name="create-a-pipeline"></a>创建管道
本步骤在数据工厂中创建包含复制活动的管道。 复制活动将数据从 Blob 存储复制到 SQL 数据库。 在[快速入门教程](quickstart-create-data-factory-portal.md)中，已通过以下步骤创建一个管道：

1. 创建链接服务。 
1. 创建输入和输出数据集。
1. 创建管道。

在本教程中，请首先创建管道， 然后在配置管道时根据需要创建链接服务和数据集。 

1. 在“开始使用”页中，选择“创建管道”。   

   ![创建管道](./media/doc-common-process/get-started-page.png)
1. 在管道的“常规”  选项卡中，输入 **CopyPipeline** 作为管道的**名称**。

1. 在“活动”工具箱中，展开“移动和转换”类别，然后将“复制数据”活动从工具箱拖放到管道设计器图面。    指定 **CopyFromBlobToSql** 作为**名称**。

    ![复制活动](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)

### <a name="configure-source"></a>配置源

1. 转到“源”选项卡。  选择“+ 新建”创建源数据集。  

1. 在“新建数据集”对话框中选择“Azure Blob 存储”，然后选择“继续”。    源数据位于 Blob 存储中，因此选择“Azure Blob 存储”作为源数据集。  

1. 在“选择格式”对话框中选择数据的格式类型，然后选择“继续”。  

    ![数据格式类型](./media/doc-common-process/select-data-format.png)

1. 在“设置属性”对话框中，输入 **SourceBlobDataset** 作为名称。  在“链接服务”文本框旁边，选择“+ 新建”。   
    
1. 在“新建链接服务(Azure Blob 存储)”窗口中，输入 **AzureStorageLinkedService** 作为名称，从“存储帐户名称”列表中选择你的存储帐户。   测试连接，然后选择“完成”以部署该链接服务。 

1. 创建链接服务后，会导航回到“设置属性”页。  在“文件路径”旁边，选择“浏览”。  

1. 导航到 **adftutorial/input** 文件夹，选择 **emp.txt** 文件，然后选择“完成”  。

1. 将自动导航到管道页。 在“源”选项卡中，确认已选择“SourceBlobDataset”。   若要预览此页上的数据，请选择“预览数据”  。 
    
    ![源数据集](./media/tutorial-copy-data-portal/source-dataset-selected.png)

### <a name="configure-sink"></a>配置接收器

1. 转到“接收器”选项卡，选择“+ 新建”，创建一个接收器数据集。   

1. 在“新建数据集”对话框中的搜索框内输入“SQL”来筛选连接器，选择“Azure SQL 数据库”，然后选择“继续”。    在本教程中，请将数据复制到 SQL 数据库。 

1. 在“设置属性”对话框中，输入 **OutputSqlDataset** 作为名称。  在“链接服务”文本框旁边，选择“+ 新建”。   数据集必须与链接服务相关联。 该链接服务包含的连接字符串可供数据工厂用于在运行时连接到 SQL 数据库。 数据集指定可将数据复制到其中的容器、文件夹和文件（可选）。 
      
1. 在“新建链接服务(Azure SQL 数据库)”对话框中执行以下步骤：  

    a. 在“名称”下输入 **AzureSqlDatabaseLinkedService**。 

    b. 在“服务器名称”下选择 SQL Server 实例。 

    c. 在“数据库名称”下选择自己的 SQL 数据库。 

    d. 在“用户名”下  输入用户的名称。

    e. 在“密码”下输入用户的密码。 

    f. 选择“测试连接”  以测试连接。

    g. 选择“完成”以部署该链接服务。  
    
    ![保存新建链接服务](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

1. 将自动导航到“设置属性”对话框。  在“表”中选择“[dbo].[emp]”。   然后选择“完成”。 

1. 转到包含管道的选项卡。在“接收器数据集”中，确认已选中“OutputSqlDataset”。  

    ![“管道”选项卡](./media/tutorial-copy-data-portal/pipeline-tab-2.png)       

可以选择通过按照[复制活动中的架构映射](copy-activity-schema-and-type-mapping.md)中所述操作将源架构映射到对应的目标架构
    
## <a name="validate-the-pipeline"></a>验证管道
若要验证管道，请从工具栏中选择“验证”  。
 
可以通过单击右上角的“代码”来查看与管道关联的 JSON 代码。 

## <a name="debug-and-publish-the-pipeline"></a>调试和发布管道
可以先调试管道，然后再将项目（链接服务、数据集和管道）发布到数据工厂或自己的 Azure Repos GIT 存储库。 

1. 若要调试管道，请在工具栏上选择“调试”。  可以在窗口底部的“输出”选项卡中看到管道运行的状态。  

1. 在管道可以成功运行后，在顶部工具栏中选择“全部发布”。  此操作将所创建的实体（数据集和管道）发布到数据工厂。

1. 等待“已成功发布”消息出现。  若要查看通知消息，请单击右上角的“显示通知”（铃铛按钮）。  

## <a name="trigger-the-pipeline-manually"></a>手动触发管道
在此步骤中，请手动触发在前面的步骤中发布的管道。 

1. 选择工具栏中的“添加触发器”，然后选择“立即触发”。   在“管道运行”页上选择“完成”。    

1. 转到左侧的“监视”选项卡。  此时会看到由手动触发器触发的管道运行。 可以使用“操作”列中的链接来查看活动详细信息以及重新运行该管道。 

    ![监视管道运行](./media/tutorial-copy-data-portal/monitor-pipeline.png)

1. 若要查看与管道运行关联的活动运行，请选择“操作”列中的“查看活动运行”链接。   此示例中只有一个活动，因此列表中只看到一个条目。 有关复制操作的详细信息，请选择“操作”列中的“详细信息”链接（眼镜图标）。   若要回到“管道运行”视图，请选择顶部的“管道运行”  。 若要刷新视图，请选择“刷新”。 

    ![监视活动运行](./media/tutorial-copy-data-portal/view-activity-runs.png)

1. 验证是否又向 SQL 数据库的 **emp** 表添加了两个行。 

## <a name="trigger-the-pipeline-on-a-schedule"></a>按计划触发管道
在此计划中，请为管道创建计划触发器。 触发器按指定的计划（例如，每小时或每天）运行管道。 此处，你要将触发器设置为每分钟运行一次，直至指定的结束日期/时间。 

1. 转到左侧位于“监视器”选项卡上方的“创作”选项卡。  

1. 转到你的管道，在工具栏上单击“添加触发器”，然后选择“新建/编辑”。   

1. 在“添加触发器”对话框中，在“选择触发器”区域选择“+ 新建”。   

1. 在“新建触发器”  窗口中，执行以下步骤： 

    a. 在“名称”下输入 **RunEveryMinute**。 

    b. 在“结束”下选择“在特定日期”。  

    c. 在“结束日期”下选择下拉列表。 

    d. 选择“当天”选项。  默认情况下，结束日期设置为第二天。

    e. 更新“结束时间”部分，使之超过当前的日期/时间数分钟。  触发器只会在发布所做的更改后激活。 如果将其设置为仅数分钟后激活，而到时又不进行发布，则看不到触发器运行。

    f. 选择“应用”。  

    g. 对于“已激活”选项，请选择“是”。   

    h. 选择“**下一步**”。

    ![“已激活”按钮](./media/tutorial-copy-data-portal/trigger-activiated-next.png)

    > [!IMPORTANT]
    > 每个管道运行都有相关联的成本，因此请正确设置结束日期。 
1. 在“触发器运行参数”页中查看以下警告，然后选择“完成”。   此示例中的管道不采用任何参数。 

1. 单击“全部发布”  来发布更改。 

1. 转到左侧的“监视”选项卡，查看触发的管道运行。  

    ![触发的管道运行](./media/tutorial-copy-data-portal/triggered-pipeline-runs.png)   
 
1. 若要从“管道运行”视图切换到“触发器运行”视图，请选择窗口顶部的“触发器运行”。   

1. 可以在列表中看到触发器运行。 

1. 验证是否每分钟将两个行（对于每个管道运行）插入 **emp** 表中，直至指定的结束时间。 

## <a name="next-steps"></a>后续步骤
此示例中的管道将数据从 Blob 存储中的一个位置复制到另一个位置。 你已了解如何： 

> [!div class="checklist"]
> * 创建数据工厂。
> * 创建包含复制活动的管道。
> * 测试性运行管道。
> * 手动触发管道。
> * 按计划触发管道。
> * 监视管道和活动运行。


若要了解如何将数据从本地复制到云，请转到以下教程： 

> [!div class="nextstepaction"]
>[将数据从本地复制到云](tutorial-hybrid-copy-portal.md)
