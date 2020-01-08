---
title: 使用 Azure 复制数据工具复制数据 | Microsoft Docs
description: 创建一个 Azure 数据工厂，然后使用“复制数据”工具将数据从 Azure Blob 存储中的一个位置复制到另一个位置。
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: quickstart
ms.date: 06/20/2018
ms.author: yexu
ms.openlocfilehash: b330c6010ddb5401dbf9753c2ea91bfeedf35c3b
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2019
ms.locfileid: "70020071"
---
# <a name="quickstart-use-the-copy-data-tool-to-copy-data"></a>快速入门：使用“复制数据”工具复制数据

> [!div class="op_single_selector" title1="选择所使用的数据工厂服务版本："]
> * [版本 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [当前版本](quickstart-create-data-factory-copy-data-tool.md)

在本快速入门中，我们将使用 Azure 门户创建一个数据工厂。 然后，使用“复制数据”工具创建一个管道，用于将数据从 Azure Blob 存储中的某个文件夹复制到另一个文件夹。 

> [!NOTE]
> 如果你对 Azure 数据工厂不太熟悉，请在学习本快速入门之前参阅 [Azure 数据工厂简介](data-factory-introduction.md)。 

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

## <a name="create-a-data-factory"></a>创建数据工厂

1. 启动 **Microsoft Edge** 或 **Google Chrome** Web 浏览器。 目前，仅 Microsoft Edge 和 Google Chrome Web 浏览器支持数据工厂 UI。
1. 转到 [Azure 门户](https://portal.azure.com)。 
1. 在左侧菜单中选择“创建资源”，然后依次选择“分析”、“数据工厂”。    
   
   ![在“新建”窗格中选择“数据工厂”](./media/doc-common-process/new-azure-data-factory-menu.png)
1. 在“新建数据工厂”  页中，输入 **ADFTutorialDataFactory** 作为**名称**。 
 
   Azure 数据工厂的名称必须 *全局唯一*。 如果出现以下错误，请更改数据工厂的名称（例如改为 **&lt;yourname&gt;ADFTutorialDataFactory**），并重新尝试创建。 有关数据工厂项目的命名规则，请参阅[数据工厂 - 命名规则](naming-rules.md)一文。
  
   ![名称不可用时出错](./media/doc-common-process/name-not-available-error.png)
1. 对于“订阅”，请选择要在其中创建数据工厂的 Azure 订阅。  
1. 对于“资源组”，请使用以下步骤之一： 
     
   - 选择“使用现有”，并从列表中选择现有的资源组。  
   - 选择“新建”，并输入资源组的名称。    
         
   若要了解有关资源组的详细信息，请参阅 [使用资源组管理 Azure 资源](../azure-resource-manager/resource-group-overview.md)。  
1. 对于“版本”，选择“V2”。  
1. 对于“位置”，请选择数据工厂所在的位置。 

   该列表仅显示数据工厂支持的位置，以及 Azure 数据工厂元数据要存储到的位置。 数据工厂使用的关联数据存储（如 Azure 存储和 Azure SQL 数据库）和计算（如 Azure HDInsight）可以在其他区域中运行。

1. 选择“创建”  。

1. 创建完成后，会显示“数据工厂”页。  选择“创作和监视”磁贴，在单独的选项卡中启动 Azure 数据工厂用户界面 (UI) 应用程序。 
   
   ![数据工厂的主页，其中包含“创作和监视”磁贴](./media/doc-common-process/data-factory-home-page.png)

## <a name="start-the-copy-data-tool"></a>启动“复制数据”工具

1. 在“开始”页中，选择“复制数据”磁贴启动“复制数据”工具。   

   ![“复制数据”磁贴](./media/doc-common-process/get-started-page.png)

1. 在“复制数据”工具的“属性”页上，  可以指定管道的名称及其说明，然后选择“下一步”。  

   ![“属性”页](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-properties-page.png)
1. 在“源数据存储”  页上，完成以下步骤：

    a. 单击“+ 创建新连接”，添加一个连接。 

    b. 从库中选择“Azure Blob 存储”   ，然后选择“继续”。

    c. 在“新建链接服务(Azure Blob 存储)”  页上，指定链接服务的名称。 从“存储帐户名称”  列表中选择存储帐户，测试连接，然后选择“完成”  。 

   ![配置 Azure Blob 存储帐户](./media/quickstart-create-data-factory-copy-data-tool/configure-blob-storage.png)

    d. 选择新创建的链接服务作为源，然后单击“下一步”。 


1. 在“选择输入文件或文件夹”页中完成以下步骤： 

   a. 单击“浏览”  导航到 **adftutorial/input** 文件夹，选择 **emp.txt** 文件，然后单击“选择”。  

   d. 选中“二进制复制”  复选框以便按原样复制文件，然后选择“下一步”。  

   ![“选择输入文件或文件夹”页](./media/quickstart-create-data-factory-copy-data-tool/select-binary-copy.png)


1. 在“目标数据存储”  页上，选择已创建的“Azure Blob 存储”链接服务  ，然后选择“下一步”  。 

1. 在“选择输出文件或文件夹”页上输入 **adftutorial/output** 作为文件夹路径，然后选择“下一步”。   

   ![“选择输出文件或文件夹”页](./media/quickstart-create-data-factory-copy-data-tool/configure-sink-path.png) 

1. 在“设置”页中选择“下一步”，以便使用默认配置。   

1. 在“摘要”  页中查看所有设置，然后选择“下一步”  。 

1. 在“部署已完成”页中，选择“监视”可监视创建的管道。   

    ![“部署已完成”页](./media/quickstart-create-data-factory-copy-data-tool/deployment-page.png)

1. 应用程序将切换到“监视”选项卡。  可在此选项卡中查看管道的状态。选择“刷新”可刷新列表。  
    
1. 在“操作”列中选择“查看活动运行”链接。   该管道只包含一个“复制”类型的活动。  
    
1. 若要查看复制操作的详细信息，请选择“操作”列中的“详细信息”（眼镜图像）链接。   有关属性的详细信息，请参阅[复制活动概述](copy-activity-overview.md)。

1. 验证 **adftutorial** 容器的 **output** 文件夹中是否创建了 **emp.txt** 文件。 如果 output 文件夹不存在，数据工厂服务会自动创建它。 

1. 切换到左面板的“监视”选项卡上的“创作”选项卡，以便编辑链接服务、数据集和管道。   若要了解如何在数据工厂 UI 中编辑这些实体，请参阅[使用 Azure 门户创建数据工厂](quickstart-create-data-factory-portal.md)。

## <a name="next-steps"></a>后续步骤
此示例中的管道将数据从 Azure Blob 存储中的一个位置复制到另一个位置。 若要了解如何在更多方案中使用数据工厂，请完成相关[教程](tutorial-copy-data-portal.md)。 