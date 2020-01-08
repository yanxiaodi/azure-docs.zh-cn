---
title: 将数据从 Azure 存储 Blob 复制到 Azure Data Lake Storage Gen1 | Microsoft Docs
description: 使用 AdlCopy 工具将数据从 Azure 存储 Blob 复制到 Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: dc273ef8-96ef-47a6-b831-98e8a777a5c1
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: be66fd51b37c0e62b2b757a88ee1db9319b2093a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60878817"
---
# <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-storage-gen1"></a>将数据从 Azure 存储 Blob 复制到 Azure Data Lake Storage Gen1
> [!div class="op_single_selector"]
> * [使用 DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [使用 AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Azure Data Lake Storage Gen1 提供的命令行工具 [AdlCopy](https://aka.ms/downloadadlcopy) 从以下源复制数据：

* 从 Azure 存储 Blob 复制到 Data Lake Storage Gen1。 无法使用 AdlCopy 将数据从 Data Lake Storage Gen1 复制到 Azure 存储 blob。
* 在两个 Azure Data Lake Storage Gen1 帐户之间。

此外，可在两个不同的模式下使用 AdlCopy 工具：

* **独立**，此时该工具使用 Data Lake Storage Gen1 资源执行此任务。
* **使用 Data Lake Analytics 帐户**，此时使用分配给 Data Lake Analytics 帐户的单位执行复制操作。 以可预测方式执行复制任务时，你可能希望使用此选项。

## <a name="prerequisites"></a>必备组件
在开始阅读本文前，必须具有：

* **Azure 订阅**。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* 包含一些数据的 **Azure 存储 Blob** 容器。
* **Azure Data Lake Storage Gen1 帐户**。 有关如何创建帐户的说明，请参阅 [Azure Data Lake Storage Gen1 入门](data-lake-store-get-started-portal.md)
* **Azure Data Lake Analytics 帐户（可选）** - 有关如何创建 Data Lake Analytics 帐户的说明，请参阅 [Azure Data Lake Analytics 入门](../data-lake-analytics/data-lake-analytics-get-started-portal.md)。
* **AdlCopy 工具**。 从 [https://aka.ms/downloadadlcopy](https://aka.ms/downloadadlcopy) 安装 AdlCopy 工具。

## <a name="syntax-of-the-adlcopy-tool"></a>AdlCopy 工具语法
对 AdlCopy 工具使用以下语法

    AdlCopy /Source <Blob or Data Lake Storage Gen1 source> /Dest <Data Lake Storage Gen1 destination> /SourceKey <Key for Blob account> /Account <Data Lake Analytics account> /Units <Number of Analytics units> /Pattern

语法中的参数如下所述：

| Option | 描述 |
| --- | --- |
| source |指定 Azure 存储 blob 中源数据的位置。 源可以是 Blob 容器、Blob 或另一 Data Lake Storage Gen1 帐户。 |
| 目标 |指定要复制到的 Data Lake Storage Gen1 目标。 |
| SourceKey |指定 Azure 存储 blob 源的存储访问密钥。 仅在源是 blob 容器或 blob 时必选此项。 |
| 帐户 |**可选**。 如要使用 Azure Data Lake Analytics 帐户运行复制作业，请使用此选项。 如果在语法中使用 /Account 选项但不指定 Data Lake Analytics 帐户，AdlCopy 会使用默认帐户来运行作业。 此外，如果使用此选项，必须添加源（Azure 存储 Blob）和目标 (Azure Data Lake Storage Gen1)，将其作为 Data Lake Analytics 帐户的数据源。 |
| 单位 |指定要用于复制作业的 Data Lake Analytics 单位的数量。 如果使用“/Account”  选项指定 Data Lake Analytics 帐户，此选项为必选。 |
| 模式 |指定 regex 模式，它指示要复制哪些 blob 或文件。 AdlCopy 使用区分大小写匹配。 未指定模式时，使用的默认模式会复制所有项。 不支持指定多个文件模式。 |

## <a name="use-adlcopy-as-standalone-to-copy-data-from-an-azure-storage-blob"></a>使用 AdlCopy（以独立模式）从 Azure 存储 blob 复制数据
1. 打开命令提示符，并导航到 AdlCopy 的安装目录（通常是 `%HOMEPATH%\Documents\adlcopy`）。
2. 运行以下命令，将特定的 Blob 从源容器复制到 Data Lake Storage Gen1 文件夹：

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    例如：

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

    >[!NOTE] 
    >上述语法指定将文件复制到 Data Lake Storage Gen1 帐户中的文件夹。 如果指定的文件夹名不存在，则 AdlCopy 工具会创建一个文件夹。

    系统会提示输入 Azure 订阅（其下提供 Data Lake Storage Gen1 帐户）的凭据。 会显示与下面类似的输出：

        Initializing Copy.
        Copy Started.
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.

1. 也可使用以下命令从一个容器将所有 Blob 复制到 Data Lake Storage Gen1 帐户：

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>        

    例如：

        AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

### <a name="performance-considerations"></a>性能注意事项

如果要从 Azure Blob 存储帐户复制，可能会在 Blob 存储端进行复制的过程中受到限制。 这会降低复制作业的性能。 若要了解有关 Azure Blob 存储限制的详细信息，请在 [Azure 订阅和服务限制](../azure-subscription-service-limits.md)中查看 Azure 存储限制。

## <a name="use-adlcopy-as-standalone-to-copy-data-from-another-data-lake-storage-gen1-account"></a>使用 AdlCopy（以独立模式）从另一 Data Lake Storage Gen1 帐户复制数据
也可使用 AdlCopy 在两个 Data Lake Storage Gen1 帐户之间复制数据。

1. 打开命令提示符，并导航到 AdlCopy 的安装目录（通常是 `%HOMEPATH%\Documents\adlcopy`）。
2. 运行以下命令从一个 Data Lake Storage Gen1 帐户将特定文件复制到另一帐户。

        AdlCopy /Source adl://<source_adlsg1_account>.azuredatalakestore.net/<path_to_file> /dest adl://<dest_adlsg1_account>.azuredatalakestore.net/<path>/

    例如：

        AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/909f2b.log /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/

   > [!NOTE]
   > 上述语法指定将文件复制到目标 Data Lake Storage Gen1 帐户中的文件夹。 如果指定的文件夹名不存在，则 AdlCopy 工具会创建一个文件夹。
   >
   >

    系统会提示输入 Azure 订阅（其下提供 Data Lake Storage Gen1 帐户）的凭据。 会显示与下面类似的输出：

        Initializing Copy.
        Copy Started.|
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.
3. 下面的命令会从源 Data Lake Storage Gen1 帐户中的特定文件夹将所有文件复制到目标 Data Lake Storage Gen1 帐户中的文件夹。

        AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/ /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/

### <a name="performance-considerations"></a>性能注意事项

将 AdlCopy 作为独立工具使用时，副本在共享的 Azure 托管资源上运行。 在此环境中得到的性能取决于系统负载和可用资源。 此模式最好用于临时的小传输。 将 AdlCopy 作为独立工具使用时，不需要调整任何参数。

## <a name="use-adlcopy-with-data-lake-analytics-account-to-copy-data"></a>使用 AdlCopy（通过 Data Lake Analytics 帐户）复制数据
也可使用 Data Lake Analytics 帐户运行 AdlCopy 作业，将数据从 Azure 存储 Blob 复制到 Data Lake Storage Gen1。 要移动的数据大小在千兆字节和兆兆字节范围，且希望更佳和可预测性能吞吐量时，通常会使用此选项。

若要结合使用 Data Lake Analytics 帐户和 AdlCopy 从 Azure 存储 Blob 进行复制，则源（Azure 存储 Blob）必须添加为 Data Lake Analytics 帐户的数据源。 有关将其他数据源添加到 Data Lake Analytics 帐户的说明，请参阅[管理 Data Lake Analytics 帐户数据源](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-data-sources)。

> [!NOTE]
> 如果使用 Data Lake Analytics 帐户从用作源的 Azure Data Lake Storage Gen1 帐户进行复制，则无需将 Data Lake Storage Gen1 帐户与 Data Lake Analytics 帐户相关联。 仅当源是 Azure 存储帐户时要求将源存储和 Data Lake Analytics 帐户关联。
>
>

运行以下命令，使用 Data Lake Analytics 帐户从 Azure 存储 Blob 复制到 Data Lake Storage Gen1 帐户：

    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Units <number_of_data_lake_analytics_units_to_be_used>

例如：

    AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeanalyticaccount /Units 2

同样，运行以下命令，以使用 Data Lake Analytics 帐户从源 Data Lake Storage Gen1 帐户中的特定文件夹将所有文件复制到目标 Data Lake Storage Gen1 帐户中的文件夹：

    AdlCopy /Source adl://mysourcedatalakestorage.azuredatalakestore.net/mynewfolder/ /dest adl://mydestdatastorage.azuredatalakestore.net/mynewfolder/ /Account mydatalakeanalyticaccount /Units 2

### <a name="performance-considerations"></a>性能注意事项

在 TB 范围内复制数据时，将 AdlCopy 与自己的 Azure Data Lake Analytics 帐户一起使用可提供更好且更容易预测的性能。 应调整的参数是用于复制作业的 Azure Data Lake Analytics 单元的数量。 增加单元的数量将提高复制作业的性能。 每个要复制的文件最多可以使用一个单元。 指定比要复制的文件数更多的单元不会提高性能。

## <a name="use-adlcopy-to-copy-data-using-pattern-matching"></a>使用 AdlCopy 以模式匹配复制数据
本部分介绍如何使用 AdlCopy 以模式匹配从源（下面的示例使用的是 Azure 存储 Blob）将数据复制到目标 Data Lake Storage Gen1 帐户。 例如，可使用以下步骤从源 blob 中将所有具有 .csv 扩展名的文件复制到目标。

1. 打开命令提示符，并导航到 AdlCopy 的安装目录（通常是 `%HOMEPATH%\Documents\adlcopy`）。
2. 运行以下命令，从源容器的特定 Blob 中将所有具有 *.csv 扩展名的文件复制到 Data Lake Storage Gen1 文件夹：

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Pattern *.csv

    例如：

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/FoodInspectionData/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Pattern *.csv

## <a name="billing"></a>计费
* 单独使用 AdlCopy 工具时，如果源 Azure 存储帐户和 Data Lake Storage Gen1 帐户不在同一区域，移动数据会产生传出费用。
* 如果通过 Data Lake Analytics 帐户使用 AdlCopy 工具，则采用标准 [Data Lake Analytics 计费费率](https://azure.microsoft.com/pricing/details/data-lake-analytics/)。

## <a name="considerations-for-using-adlcopy"></a>AdlCopy 使用注意事项
* AdlCopy（版本 1.0.5）支持从具有总计数千的文件和文件夹的源中复制数据。 但是，如果复制较大数据集时遇到问题，可将文件/文件夹分发为不同的子文件夹，并改用子文件夹的路径作为源。

## <a name="performance-considerations-for-using-adlcopy"></a>使用 AdlCopy 的性能注意事项

AdlCopy 支持复制包含上千个文件和文件夹的数据。 但是，如果在复制大型数据集时遇到问题，可将文件/文件夹分配到较小的子文件夹中。 AdlCopy 专用于临时副本。 如果要尝试定期复制数据，应考虑使用 [Azure 数据工厂](../data-factory/connector-azure-data-lake-store.md)，此工具提供与复制操作有关的完整管理功能。

## <a name="release-notes"></a>发行说明
* 1.0.13 - 如果通过多个 adlcopy 命令将数据复制到同一 Azure Data Lake Storage Gen1 帐户，则不再需要在每次运行时重新输入凭据。 现在，Adlcopy 会在多次运行之间缓存该信息。

## <a name="next-steps"></a>后续步骤
* [保护 Data Lake Storage Gen1 中的数据](data-lake-store-secure-data.md)
* [配合使用 Azure Data Lake Analytics 和 Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [将 Azure HDInsight 与 Data Lake Storage Gen1 配合使用](data-lake-store-hdinsight-hadoop-use-portal.md)
