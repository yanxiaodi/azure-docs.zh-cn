---
title: 使用复制数据工具根据 LastModifiedDate 以增量方式复制新的和已更改的文件 | Microsoft Docs
description: 创建一个 Azure 数据工厂，然后使用复制数据工具根据 LastModifiedDate 以增量方式加载新文件。
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 1/24/2019
ms.openlocfilehash: 9f6fd57586603d0d987faa674d40a7e4678530a1
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933845"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>使用复制数据工具根据 LastModifiedDate 以增量方式复制新的和已更改的文件

在本教程中，你将使用 Azure 门户创建数据工厂。 然后，使用复制数据工具创建一个管道，该管道根据新文件和已更改的文件的 **LastModifiedDate**，以增量方式将这些文件从一个 Azure Blob 存储复制到另一个 Azure Blob 存储。

执行此操作时，ADF 会扫描来自源存储的所有文件，按其 LastModifiedDate 应用文件筛选器，然后仅将自上次以来的新文件和已更新文件复制到目标存储。  请注意，如果让 ADF 扫描大量文件，但是仅将少量文件复制到目标，则仍然会预计由于文件扫描所导致的较长持续时间也十分耗时。   

> [!NOTE]
> 如果对 Azure 数据工厂不熟悉，请参阅 [Azure 数据工厂简介](introduction.md)。

在本教程中，你将执行以下任务：

> [!div class="checklist"]
> * 创建数据工厂。
> * 使用“复制数据”工具创建管道。
> * 监视管道和活动运行。

## <a name="prerequisites"></a>先决条件

* **Azure 订阅**：如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/)。
* **Azure 存储帐户**：将 Blob 存储用作源和接收器数据存储。 如果没有 Azure 存储帐户，请参阅[创建存储帐户](../storage/common/storage-quickstart-create-account.md)中的说明。

### <a name="create-two-containers-in-blob-storage"></a>在 Blob 存储中创建两个容器

执行以下步骤，准备本教程所需的 Blob 存储。

1. 创建名为 **source** 的容器。 可以使用各种工具（例如 [Azure 存储资源管理器](https://storageexplorer.com/)）来执行这些任务。

2. 创建名为 **destination** 的容器。 

## <a name="create-a-data-factory"></a>创建数据工厂

1. 在左侧菜单中，选择“创建资源” > “数据 + 分析” > “数据工厂”： 
   
   ![在“新建”窗格中选择“数据工厂”](./media/doc-common-process/new-azure-data-factory-menu.png)

2. 在“新建数据工厂”页的“名称”下输入 **ADFTutorialDataFactory**。 
 
   数据工厂的名称必须全局唯一。 可能会收到以下错误消息：
   
   ![新的数据工厂错误消息](./media/doc-common-process/name-not-available-error.png)

   如果收到有关名称值的错误消息，请为数据工厂输入另一名称。 例如，使用名称 _**yourname**_ **ADFTutorialDataFactory**。 有关数据工厂项目的命名规则，请参阅[数据工厂命名规则](naming-rules.md)。
3. 选择要在其中创建新数据工厂的 Azure **订阅**。 
4. 对于“资源组”，请执行以下步骤之一：
     
    * 选择“使用现有资源组”，并从下拉列表选择现有的资源组。

    * 选择“新建”，并输入资源组的名称。 
         
    若要了解资源组，请参阅[使用资源组管理 Azure 资源](../azure-resource-manager/resource-group-overview.md)。

5. 在“版本”下选择“V2”。
6. 在“位置”下选择数据工厂的位置。 下拉列表中仅显示支持的位置。 数据工厂使用的数据存储（例如，Azure 存储和 SQL 数据库）和计算资源（例如，Azure HDInsight）可以位于其他位置和区域。
7. 选择“固定到仪表板”。 
8. 选择“创建”。
9. 在仪表板中，参考“正在部署数据工厂”磁贴了解过程状态。

    ![“正在部署数据工厂”磁贴](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. 创建完以后，会显示“数据工厂”主页。
   
    ![数据工厂主页](./media/doc-common-process/data-factory-home-page.png)
11. 若要在单独的选项卡中打开 Azure 数据工厂用户界面 (UI)，请选择“创作和监视”磁贴。 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>使用“复制数据”工具创建管道

1. 在“开始使用”页中选择“复制数据”标题，打开“复制数据”工具。 

   ![“复制数据”工具磁贴](./media/doc-common-process/get-started-page.png)
   
2. 在“属性”页上执行以下步骤：

    a. 在“任务名称”下输入 **DeltaCopyFromBlobPipeline**。

    b. 在“任务频率或任务计划”下，选择“按计划定期运行”。

    c. 在“触发器类型”下，选择“翻转窗口”。
    
    d. 在“重复周期”下输入“15 分钟”。 
    
    e. 选择“**下一步**”。 
    
    数据工厂 UI 将使用指定的任务名称创建一个管道。 

    ![“属性”页](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)
    
3. 在“源数据存储”页上，完成以下步骤：

    a. 选择“+ 创建新连接”添加一个连接。
    
    ![“源数据存储”页](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page.png)

    b. 从库中选择“Azure Blob 存储”，然后选择“继续”。
    
    ![“源数据存储”页](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. 在“新建链接服务”页面上，从“存储帐户名称”列表中选择你的存储帐户，然后选择“完成”。
    
    ![“源数据存储”页](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. 选择新创建的链接服务，然后选择“下一步”。 
    
   ![“源数据存储”页](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. 在“选择输入文件或文件夹”页中完成以下步骤：
    
    a. 浏览并选择 **source** 文件夹，然后选择“选择”。
    
    ![选择输入文件或文件夹](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)
    
    b. 在“文件加载行为”下，选择“增量加载:LastModifiedDate”。
    
    ![选择输入文件或文件夹](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-loading-behavior.png)
    
    c. 勾选“二进制副本”，然后选择“下一步”。
    
     ![选择输入文件或文件夹](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)
     
5. 在“目标数据存储”页上，选择“AzureBlobStorage”。 这是与源数据存储相同的存储帐户。 然后，选择“下一步”。

    ![“目标数据存储”页](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/destination-data-store-page-select-linkedservice.png)
    
6. 在“选择输出文件或文件夹”页中完成以下步骤：
    
    a. 浏览并选择 **destination** 文件夹，然后选择“选择”。
    
    ![选择输出文件或文件夹](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)
    
    b. 选择“**下一步**”。
    
     ![选择输出文件或文件夹](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/click-next-after-output-folder.png)
    
7. 在“设置”页中，选择“下一步”。 

    ![“设置”页](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/settings-page.png)
    
8. 在“摘要”页上检查设置，然后选择“下一步”。

    ![“摘要”页](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)
    
9. 在“部署”页中，选择“监视”可以监视管道（任务）。

    ![“部署”页](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)
    
10. 请注意，界面中已自动选择左侧的“监视”选项卡。 “操作”列中包含用于查看活动运行详细信息以及用于重新运行管道的链接。 选择“刷新”以刷新列表，然后在“操作”列中选择“查看活动运行”链接。 

    ![刷新列表并选择“查看活动运行”](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. 该管道只包含一个活动（复制活动），因此只显示了一个条目。 有关复制操作的详细信息，请选择“操作”列中的“详细信息”链接（眼镜图标）。 

    ![复制活动在管道中](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)
    
    由于 Blob 存储帐户中的 **source** 容器内没有文件，因此，你不会看到有任何文件复制到 Blob 存储帐户中的 **destination** 容器。
    
    ![source 容器或 destination 容器中没有文件](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)
    
12. 创建空的文本文件，并将其命名为 **file1.txt**。 将此文本文件上传到存储帐户中的 **source** 容器。 可以使用各种工具（例如 [Azure 存储资源管理器](https://storageexplorer.com/)）来执行这些任务。   

    ![创建 file1.txt 并将其上传到 source 容器](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)
    
13. 若要回到“管道运行”视图，请选择“所有管道运行”，然后等待再次自动触发同一管道。  

    ![选择“所有管道运行”](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. 看到第二个管道运行时，请选择其对应的“查看活动运行”。 然后像操作第一次管道运行时一样检查详细信息。  

    ![选择“查看活动运行”并检查详细信息](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs5.png)

    你会发现，已将一个文件 (file1.txt) 从 Blob 存储帐户的 **source** 容器复制到 **destination** 容器。
    
    ![File1.txt 已从 source 容器复制到 destination 容器](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)
    
15. 创建另一个空的文本文件，并将其命名为 **file2.txt**。 将此文本文件上传到 Blob 存储帐户中的 **source** 容器。   
    
16. 针对这第二个文本文件重复步骤 13 至 14。 在下一个管道运行中你会发现，只有新文件 (file2.txt) 已从存储帐户的 **source** 容器复制到 **destination** 容器。  
    
    ![File2.txt 已从 source 容器复制到 destination 容器](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs7.png)

    还可以通过使用 [Azure 存储资源管理器](https://storageexplorer.com/)扫描这些文件，来验证此过程的结果。
    
    ![使用 Azure 存储资源管理器扫描文件](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>后续步骤
请转到下一篇教程，了解如何使用 Azure 上的 Apache Spark 群集转换数据：

> [!div class="nextstepaction"]
>[使用 Apache Spark 群集转换云中的数据](tutorial-transform-data-spark-portal.md)