---
title: 使用 Azure 复制数据工具复制数据 | Microsoft Docs
description: 创建一个 Azure 数据工厂，然后使用“复制数据”工具将数据从 Azure Blob 存储复制到 SQL 数据库。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 09/11/2018
ms.author: jingwang
ms.openlocfilehash: a99f319c25613f811b48923c734b77b135b677bc
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827952"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-the-copy-data-tool"></a>使用“复制数据”工具，将数据从 Azure Blob 存储复制到 SQL 数据库
> [!div class="op_single_selector" title1="选择要使用的数据工厂服务的版本："]
> * [版本 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [当前版本](tutorial-copy-data-tool.md)

在本教程中，我们将使用 Azure 门户创建数据工厂。 然后，使用“复制数据”工具创建一个管道，以便将数据从 Azure Blob 存储复制到 SQL 数据库。

> [!NOTE]
> 如果对 Azure 数据工厂不熟悉，请参阅 [Azure 数据工厂简介](introduction.md)。

将在本教程中执行以下步骤：
> [!div class="checklist"]
> * 创建数据工厂。
> * 使用“复制数据”工具创建管道。
> * 监视管道和活动运行。

## <a name="prerequisites"></a>先决条件

* **Azure 订阅**：如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/)。
* **Azure 存储帐户**：使用 Blob 存储作为 _数据存储。 如果没有 Azure 存储帐户，请参阅[创建存储帐户](../storage/common/storage-quickstart-create-account.md)中的说明。
* **Azure SQL 数据库**：使用 SQL 数据库作为 _数据存储。 如果没有 SQL 数据库，请参阅[创建 SQL 数据库](../sql-database/sql-database-get-started-portal.md)中的说明。

### <a name="create-a-blob-and-a-sql-table"></a>创建 blob 和 SQL 表

执行以下步骤，准备本教程所需的 Blob 存储和 SQL 数据库。

#### <a name="create-a-source-blob"></a>创建源 blob

1. 启动**记事本**。 复制以下文本，并在磁盘上将其保存在名为 **inputEmp.txt** 的文件中：

    ```
    John|Doe
    Jane|Doe
    ```

1. 创建名为 **adfv2tutorial** 的容器，然后将 inputEmp.txt 文件上传到该容器中。 可以使用 Azure 门户或各种工具（如 [Azure 存储资源管理器](https://storageexplorer.com/)）来执行这些任务。

#### <a name="create-a-sink-sql-table"></a>创建接收器 SQL 表

1. 使用以下 SQL 脚本在 SQL 数据库中创建名为 **dbo.emp** 的表：

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

2. 允许 Azure 服务访问 SQL Server。 验证是否已为运行 SQL 数据库的服务器启用“允许访问 Azure 服务”  。 通过此设置，数据工厂可将数据写入数据库实例。 若要验证并启用此设置，请转至 Azure SQL Server >“概述”>“设置服务器防火墙”>将“允许访问 Azure 服务”  选项设置为“打开”  。

## <a name="create-a-data-factory"></a>创建数据工厂

1. 在左侧菜单中，选择“创建资源”   > “Analytics”   > “数据工厂”  ：
    
    ![新建数据工厂](./media/doc-common-process/new-azure-data-factory-menu.png)
1. 在“新建数据工厂”  页的“名称”下输入 **ADFTutorialDataFactory**  。

    数据工厂的名称必须全局唯一。  可能会收到以下错误消息：
    
    ![新的数据工厂错误消息](./media/doc-common-process/name-not-available-error.png)

    如果收到有关名称值的错误消息，请为数据工厂输入另一名称。 例如，使用名称 _**yourname**_ **ADFTutorialDataFactory**。 有关数据工厂项目的命名规则，请参阅[数据工厂命名规则](naming-rules.md)。
1. 选择要在其中创建新数据工厂的 Azure **订阅**。
1. 对于“资源组”，请执行以下步骤之一： 
    
    a. 选择“使用现有资源组”，并从下拉列表选择现有的资源组。 

    b. 选择“新建”，并输入资源组的名称。 
    
    若要了解资源组，请参阅[使用资源组管理 Azure 资源](../azure-resource-manager/resource-group-overview.md)。

1. 在“版本”下选择“V2”作为版本。  
1. 在“位置”下选择数据工厂的位置。  下拉列表中仅显示支持的位置。 数据工厂使用的数据存储（例如，Azure 存储和 SQL 数据库）和计算资源（例如，Azure HDInsight）可以位于其他位置和区域。
1. 选择“创建”  。

1. 创建完以后，会显示“数据工厂”  主页。
    
    ![数据工厂主页](./media/doc-common-process/data-factory-home-page.png)
1. 若要在单独的选项卡中启动 Azure 数据工厂用户界面 (UI)，请选择“创作和监视”磁贴。 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>使用“复制数据”工具创建管道

1. 在“开始使用”页中选择“复制数据”磁贴，启动“复制数据”工具。  

    ![“复制数据”工具磁贴](./media/doc-common-process/get-started-page.png)
1. 在“属性”页的“任务名称”下，输入 **CopyFromBlobToSqlPipeline**。   然后，选择“下一步”  。 数据工厂 UI 将使用指定的任务名称创建一个管道。

1. 在“源数据存储”  页上，完成以下步骤：

    a. 单击“+ 创建新连接”来添加连接 

    b. 从库中选择“Azure Blob 存储”   ，然后选择“继续”。

    c. 在“新建链接服务”页面上，从“存储帐户名称”列表中选择你的存储帐户，然后选择“完成”。   

    d. 选择新创建的链接服务作为源，然后单击“下一步”。 

    ![选择源链接服务](./media/tutorial-copy-data-tool/select-source-linked-service.png)

1. 在“选择输入文件或文件夹”页中完成以下步骤： 
    
    a. 单击“浏览”  导航到 **adfv2tutorial/input** 文件夹，选择 **inputEmp.txt** 文件，然后单击“选择”。 

    b. 单击“下一步”转到下一步骤。 

1. 在“文件格式设置”页中，注意该工具会自动检测列与行的分隔符。  选择“**下一步**”。 还可以在此页中预览数据，以及查看输入数据的架构。

    ![文件格式设置](./media/tutorial-copy-data-tool/file-format-settings-page.png)
1. 在“目标数据存储”  页上，完成以下步骤：

    a. 单击“+ 创建新连接”来添加连接 

    b. 从库中选择“Azure SQL 数据库”，然后选择“继续”   。

    c. 在“新建链接服务”  页面上，从下拉列表中选择你的服务器名称和 DB 名称，指定用户名和密码，然后选择“完成”。 

    ![配置 Azure SQL DB](./media/tutorial-copy-data-tool/config-azure-sql-db.png)

    d. 选择新创建的链接服务作为接收器，然后单击“下一步”  。

    ![选择接收器链接服务](./media/tutorial-copy-data-tool/select-sink-linked-service.png)

1. 在“表映射”页中，选择 **[dbo].[emp]** 表，然后选择“下一步”。  

1. 在“架构映射”页中，注意输入文件中的第一个和第二个列已映射到 **emp** 表的 **FirstName** 和 **LastName** 列。  选择“**下一步**”。

    ![“架构映射”页](./media/tutorial-copy-data-tool/schema-mapping.png)
1. 在“设置”页中，选择“下一步”。  
1. 在“摘要”  页中检查设置，然后选择“下一步”  。
1. 在“部署”页中，选择“监视”可以监视管道（任务）   。
1. 请注意，界面中已自动选择左侧的“监视”选项卡。  “操作”列中包含用于查看活动运行详细信息以及用于重新运行管道的链接  。 选择“刷新”可刷新列表。 

1. 若要查看与管道运行关联的活动运行，请选择“操作”列中的“查看活动运行”链接。   有关复制操作的详细信息，请选择“操作”列中的“详细信息”链接（眼镜图标）。   若要回到“管道运行”视图，请选择顶部的“管道运行”链接  。 若要刷新视图，请选择“刷新”。 

    ![监视活动运行](./media/tutorial-copy-data-tool/activity-monitoring.png)


1. 验证数据是否已插入到 SQL 数据库中的 **emp** 表。


1. 选择左侧的“创作”选项卡切换到编辑器模式。  可以使用编辑器来更新通过该工具创建的链接服务、数据集和管道。 有关在数据工厂 UI 中编辑这些实体的详细信息，请参阅[本教程的 Azure 门户版本](tutorial-copy-data-portal.md)。

## <a name="next-steps"></a>后续步骤
此示例中的管道将数据从 Blob 存储复制到 SQL 数据库。 你已了解如何：

> [!div class="checklist"]
> * 创建数据工厂。
> * 使用“复制数据”工具创建管道。
> * 监视管道和活动运行。

若要了解如何将数据从本地复制到云，请转到以下教程：

>[!div class="nextstepaction"]
>[将数据从本地复制到云](tutorial-hybrid-copy-data-tool.md)
