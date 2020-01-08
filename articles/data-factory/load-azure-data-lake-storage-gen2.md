---
title: 使用 Azure 数据工厂将数据加载到 Azure Data Lake Storage Gen2 中
description: 使用 Azure 数据工厂将数据复制到 Azure Data Lake Storage Gen2 中
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: jingwang
ms.openlocfilehash: f8af34207eddb613f7a59bd3e3d300555e10f985
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65560738"
---
# <a name="load-data-into-azure-data-lake-storage-gen2-with-azure-data-factory"></a>使用 Azure 数据工厂将数据加载到 Azure Data Lake Storage Gen2 中

Azure Data Lake Storage Gen2 是一组专用于大数据分析的功能，内置于 [Azure Blob 存储](../storage/blobs/storage-blobs-introduction.md)中。 它可使用文件系统和对象存储范例与数据进行交互。

Azure 数据工厂 (ADF) 是一项完全托管的基于云的数据集成服务。 通过该服务，可使用丰富的本地数据存储和基于云的数据存储中的数据填充数据湖，并更快速地生成分析解决方案。 若要查看受支持的连接器的详细列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

Azure 数据工厂提供可横向扩展的托管数据移动解决方案。 得益于 ADF 的横向扩展体系结构，它能以较高的吞吐量引入数据。 有关详细信息，请参阅[复制活动性能](copy-activity-performance.md)。

本文介绍如何使用数据工厂复制数据工具将数据从 Amazon Web Services S3 服务加载至 Azure Data Lake Storage Gen2。 可以遵循类似步骤，从其他类型的数据存储中复制数据。

>[!TIP]
>要将 Azure Data Lake Storage Gen1 中的数据复制到 Gen2，请参阅[此特定演练](load-azure-data-lake-storage-gen2-from-gen1.md)。

## <a name="prerequisites"></a>必备组件

* Azure 订阅：如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了 Data Lake Storage Gen2 的 Azure 存储帐户：如果没有存储帐户[创建一个帐户](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。
* AWS 帐户与一个包含数据的 S3 存储桶：本文介绍如何从 Amazon S3 复制数据。 可以按类似步骤使用其他数据存储。

## <a name="create-a-data-factory"></a>创建数据工厂

1. 在左侧菜单中，选择“创建资源” > “数据 + 分析” > “数据工厂”：
   
   ![在“新建”窗格中选择“数据工厂”](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. 在“新建数据工厂”页中，为下图中所示的字段提供值： 
      
   ![“新建数据工厂”页](./media/load-azure-data-lake-storage-gen2//new-azure-data-factory.png)
 
    * **名称**：输入 Azure 数据工厂的全局唯一名称。 如果收到错误“数据工厂名称 \"LoadADLSDemo\" 不可用”，请输入不同的数据工厂名称。 例如，可以使用名称 _**yourname**_ **ADFTutorialDataFactory**。 请重试创建数据工厂。 有关数据工厂项目的命名规则，请参阅[数据工厂命名规则](naming-rules.md)。
    * **订阅**：选择要在其中创建数据工厂的 Azure 订阅。 
    * **资源组**：从下拉列表中选择现有资源组，或选择“新建”选项并输入资源组的名称。 若要了解有关资源组的详细信息，请参阅 [使用资源组管理 Azure 资源](../azure-resource-manager/resource-group-overview.md)。  
    * **版本**：选择“V2”。
    * **位置**：选择数据工厂的位置。 下拉列表中仅显示支持的位置。 数据工厂使用的数据存储可以在其他位置和区域中。 

3. 选择“创建”。
4. 创建操作完成后，请转到数据工厂。 此时会看到“数据工厂”主页，如下图所示： 
   
   ![数据工厂主页](./media/load-azure-data-lake-storage-gen2/data-factory-home-page.png)

   选择“创作和监视”磁贴，在单独的选项卡中启动数据集成应用程序。

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>将数据加载到 Azure Data Lake Storage Gen2 中

1. 在“入门”页中，单击“复制数据”磁贴启动“复制数据”工具： 

   ![“复制数据”工具磁贴](./media/load-azure-data-lake-storage-gen2/copy-data-tool-tile.png)
2. 在“属性”页中，为“任务名称”字段指定“CopyFromAmazonS3ToADLS”，然后选择“下一步”：

    ![“属性”页](./media/load-azure-data-lake-storage-gen2/copy-data-tool-properties-page.png)
3. 在“源数据存储”页面，单击“+ 创建新连接”：

    ![“源数据存储”页](./media/load-azure-data-lake-storage-gen2/source-data-store-page.png)
    
    在连接器库中选择“Amazon S3”，然后选择“继续”
    
    ![“源数据存储 S3”页](./media/load-azure-data-lake-storage-gen2/source-data-store-page-s3.png)
    
4. 在“指定 Amazon S3 连接”页中，执行以下步骤：

   1. 指定“访问密钥 ID”值。
   2. 指定“机密访问密钥”值。
   3. 单击“测试连接”以验证设置，然后选择“完成”。
   4. 随即会显示新创建的连接。 选择“**下一步**”。
   
      ![指定 Amazon S3 帐户](./media/load-azure-data-lake-storage-gen2/specify-amazon-s3-account.png)
      
5. 在“选择输入文件或文件夹”页上，浏览到要复制的文件夹和文件。 选择文件夹/文件，选择“选择”：

    ![选择输入文件或文件夹](./media/load-azure-data-lake-storage-gen2/choose-input-folder.png)

6. 选中“以递归方式复制文件”和“以二进制方式复制”选项，指定复制行为。 选择“下一步”：

    ![指定输出文件夹](./media/load-azure-data-lake-storage-gen2/specify-binary-copy.png)
    
7. 在“目标数据存储”页中，单击“+ 新建连接”，然后选择“Azure Data Lake Storage Gen2”，并选择“继续”：

    ![“目标数据存储”页](./media/load-azure-data-lake-storage-gen2/destination-data-storage-page.png)

8. 在“指定 Azure Data Lake 存储连接”页，执行以下步骤：

   1. 从“存储帐户名称”下拉列表中选择能使用 Data Lake Storage Gen2 的帐户。
   2. 选择“完成”以创建连接。 然后，选择“下一步”。
   
   ![指定 Azure Data Lake Storage Gen2 帐户](./media/load-azure-data-lake-storage-gen2/specify-adls.png)

9. 在中**选择输出文件或文件夹**页上，输入**copyfroms3**作为输出文件夹的名称，然后选择**下一步**。 ADF 将创建相应的 ADLS 第 2 代文件系统和子文件夹在复制期间如果不存在。

    ![指定输出文件夹](./media/load-azure-data-lake-storage-gen2/specify-adls-path.png)

10. 在“设置”页中选择“下一步”，以便使用默认设置：

    ![“设置”页](./media/load-azure-data-lake-storage-gen2/copy-settings.png)
11. 在“摘要”页中检查设置，然后选择“下一步”：

    ![“摘要”页](./media/load-azure-data-lake-storage-gen2/copy-summary.png)
12. 在“部署”页中选择“监视”，来监视管道：

    ![“部署”页](./media/load-azure-data-lake-storage-gen2/deployment-page.png)
13. 请注意，界面中已自动选择左侧的“监视”选项卡。 “操作”列中包含用于查看活动运行详细信息以及用于重新运行管道的链接：

    ![监视管道运行](./media/load-azure-data-lake-storage-gen2/monitor-pipeline-runs.png)

14. 若要查看与管道运行关联的活动运行，请选择“操作”列中的“查看活动运行”链接。 该管道只包含一个活动（复制活动），因此只显示了一个条目。 若要切换回到管道运行视图，请选择顶部的“管道”链接。 选择“刷新”可刷新列表。 

    ![监视活动运行](./media/load-azure-data-lake-storage-gen2/monitor-activity-runs.png)

15. 若要监视每个复制活动的执行详细信息，请在活动监视视图中选择“操作”下的“详细信息”链接（眼镜图标）。 可以监视详细信息，如从源复制到接收器的数据量、吞吐量、执行步骤以及相应的持续时间和使用的配置：

    ![监视活动运行详细信息](./media/load-azure-data-lake-storage-gen2/monitor-activity-run-details.png)

16. 验证数据是否已复制到 Data Lake Storage Gen2 帐户。

## <a name="next-steps"></a>后续步骤

* [复制活动概述](copy-activity-overview.md)
* [Azure Data Lake Storage Gen2 连接器](connector-azure-data-lake-storage.md)
