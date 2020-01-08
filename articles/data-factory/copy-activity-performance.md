---
title: Azure 数据工厂中的复制活动性能和可伸缩性指南 |Microsoft Docs
description: 了解使用复制活动时影响 Azure 数据工厂中数据移动性能的关键因素。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: f760917ae8f4ab11902799e36973ae896c4a2b43
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232351"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>复制活动性能和可伸缩性指南
> [!div class="op_single_selector" title1="选择要使用的 Azure 数据工厂的版本:"]
> * [版本 1](v1/data-factory-copy-activity-performance.md)
> * [当前版本](copy-activity-performance.md)

无论你是要执行从 data lake 或企业数据仓库 (EDW) 到 Azure 的大规模数据迁移, 还是想要将来自不同源的大规模数据迁移到 azure 以进行大数据分析, 实现最佳性能和能力.  Azure 数据工厂提供了一种高性能、可复原且经济高效的机制, 用于大规模引入数据, 使其适合于希望构建高性能和可缩放的数据引入管道的数据工程师。

阅读本文后，能够回答以下问题：

- 对于数据迁移和数据引入方案, 可以使用 ADF 复制活动实现哪些级别的性能和可伸缩性？

- 我应该采取哪些步骤来优化 ADF 复制活动的性能？
- 我可以利用什么 ADF 性能优化旋钮来优化单次复制活动运行的性能？
- 优化复制性能时, 应考虑 ADF 以外的其他因素？

> [!NOTE]
> 如果你对常规复制活动不熟悉，在阅读本文前请参阅[复制活动概述](copy-activity-overview.md)。

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>使用 ADF 可实现复制性能和可伸缩性

ADF 提供的无服务器体系结构允许不同级别的并行度, 这允许开发人员构建管道来充分利用网络带宽以及存储 IOPS 和带宽, 最大程度地提高环境的数据移动吞吐量。  这意味着, 可以通过度量源数据存储中提供的最小吞吐量、目标数据存储以及源和目标之间的网络带宽, 来估计可以实现的吞吐量。  下表根据数据大小和环境的带宽限制来计算复制持续时间。 

| 数据大小/ <br/> 带宽 | 50 Mbps    | 100 Mbps  | 500 Mbps  | 1 Gbps   | 5 Gbps   | 10 Gbps  | 50 Gbps   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1 GB**                    | 2.7 分钟    | 1.4 分钟   | 0.3 分钟   | 0.1 分钟  | 0.03 分钟 | 0.01 分钟 | 0.0 分钟   |
| **10 GB**                   | 27.3 分钟   | 13.7 分钟  | 2.7 分钟   | 1.3 分钟  | 0.3 分钟  | 0.1 分钟  | 0.03 分钟  |
| **100 GB**                  | 4.6 小时    | 2.3 小时   | 0.5 小时   | 0.2 小时  | 0.05 小时 | 0.02 小时 | 0.0 小时   |
| **1 TB**                    | 46.6 小时   | 23.3 小时  | 4.7 小时   | 2.3 小时  | 0.5 小时  | 0.2 小时  | 0.05 小时  |
| **10 TB**                   | 19.4 天  | 9.7 天  | 1.9 天  | 0.9 天 | 0.2 天 | 0.1 天 | 0.02 天 |
| **100 TB**                  | 194.2 天 | 97.1 天 | 19.4 天 | 9.7 天 | 1.9 天 | 1天   | 0.2 天  |
| **1 PB**                    | 64.7 mo    | 32.4 mo   | 6.5 mo    | 3.2 mo   | 0.6 mo   | 0.3 mo   | 0.06 mo   |
| **10 PB**                   | 647.3 mo   | 323.6 mo  | 64.7 mo   | 31.6 mo  | 6.5 mo   | 3.2 mo   | 0.6 mo    |

ADF 副本可在不同级别进行缩放:

![ADF 复制的缩放方式](media/copy-activity-performance/adf-copy-scalability.png)

- ADF 控制流可以并行启动多个复制活动, 例如,[为每个循环](control-flow-for-each-activity.md)使用。
- 单个复制活动可以利用可缩放的计算资源: 使用 Azure Integration Runtime 时, 可以以无服务器方式为每个复制活动指定[最多 256 DIUs](#data-integration-units) ;使用自承载 Integration Runtime 时, 可以手动纵向扩展计算机或向外扩展到多台计算机 ([最多4个节点](create-self-hosted-integration-runtime.md#high-availability-and-scalability)), 单个复制活动会将其文件集分区在所有节点上。
- 单个复制活动将使用多个线程[并行](#parallel-copy)读取和写入数据存储区。

## <a name="performance-tuning-steps"></a>性能优化步骤

请执行以下步骤，通过复制活动优化 Azure 数据工厂服务的性能。

1. **选取测试数据集并建立基线。** 在开发阶段，通过对代表性数据示例使用复制活动来测试管道。 你选择的数据集应表示典型的数据模式 (文件夹结构、文件模式、数据架构等), 并且足够大以评估复制性能, 例如, 复制活动需要10分钟或更长时间才能完成。 按照[复制活动监视](copy-activity-overview.md#monitoring)收集执行详细信息和性能特征。

2. **如何最大程度地提高单个复制活动的性能**:

   首先, 建议使用单个复制活动最大限度地提高性能。

   **如果复制活动正在 Azure Integration Runtime 上执行:**

   从[数据集成单元 (DIU)](#data-integration-units)和[并行复制](#parallel-copy)设置的默认值开始。  执行性能测试运行, 并记下实现的性能以及用于 DIUs 和并行复制的实际值。  有关如何收集所使用的运行结果和性能设置的说明, 请参阅[复制活动监视](copy-activity-overview.md#monitoring)。

   现在, 每次执行其他性能测试运行, 每次都将 DIU 设置的值加倍。  或者, 如果你认为使用默认设置实现的性能远远低于预期, 则可以更显著地提高后续测试运行中的 DIU 设置。

   当你增加 DIU 设置时, 复制活动应尽可能接近线性缩放。  如果通过将 DIU 设置加倍而不显示吞吐量 double, 则可能会发生两种情况:

   - 要运行的特定复制模式无法从添加更多 DIUs 中获益。  即使您指定了较大的 DIU 值, 实际使用的 DIU 仍保持不变, 因此, 您将获得与以前相同的吞吐量。  如果是这种情况, 请通过运行多个副本同时引用步骤3来最大程度地提高聚合吞吐量。
   - 通过添加更多的 DIUs (更好的性能), 进而驱动数据提取、传输和加载的速度, 源数据存储、源中的网络或目标数据存储已达到其瓶颈, 而且可能会受到限制。  如果是这种情况, 请尝试与数据存储管理员或网络管理员联系以提高上限, 或减少 DIU 设置, 直到限制停止发生。

   **如果在自承载 Integration Runtime 上执行复制活动:**

   我们建议你将专用计算机与托管数据存储的服务器分开, 以承载集成运行时。

   从[并行复制](#parallel-copy)设置的默认值开始, 对自承载 IR 使用单个节点。  执行性能测试运行, 并记下实现的性能。

   如果要实现更高的吞吐量, 可以纵向扩展或横向扩展自承载 IR:

   - 如果自承载 IR 节点上的 CPU 和可用内存未充分利用, 但并发作业的执行达到了限制, 则应通过增加可在节点上运行的并发作业数来向上缩放。  有关说明, 请参阅[此处](create-self-hosted-integration-runtime.md#scale-up)。
   - 另一方面, 如果 CPU 在自承载 IR 节点上很高, 或者可用内存较低, 则可以添加新节点以帮助在多个节点之间横向扩展负载。  有关说明, 请参阅[此处](create-self-hosted-integration-runtime.md#high-availability-and-scalability)。

   扩展或横向扩展自承载 IR 的容量时, 请重复执行性能测试, 以查看是否获得更好的吞吐量。  如果吞吐量停止提高, 很可能是因为源数据存储、源网络或目标数据存储已达到其瓶颈, 因而开始受到限制。 如果是这种情况, 请尝试联系你的数据存储管理员或网络管理员以提高上限, 或者返回到你先前的自承载 IR 缩放设置。 

3. **如何通过并行运行多个副本来最大程度地提高聚合吞吐量:**

   现在, 你已将单个复制活动的性能最大化, 但如果尚未达到你的环境的吞吐量上限–网络、源数据存储和目标数据存储, 则可以使用 ADF 并行运行多个复制活动控制流构造, 如[For each 循环](control-flow-for-each-activity.md)。

4. **性能优化提示和优化功能。** 在某些情况下, 当你在 Azure 数据工厂中运行复制活动时,[将在复制活动监视](copy-activity-overview.md#monitor-visually)顶部看到 "性能优化提示" 消息, 如以下示例中所示。 该消息将告知针对给定复制运行识别到的瓶颈。 此外，它还会提供有关如何进行更改以提升复制吞吐量的指导。 性能优化提示目前提供如下建议：

   - 将数据加载到 Azure SQL 数据仓库时使用 PolyBase。
   - 当数据存储端的资源造成瓶颈时，增加 Azure Cosmos DB 请求单位数或 Azure SQL 数据库 DTU（数据库吞吐量单位）数。
   - 删除不必要的暂存副本。

   性能优化规则也将逐渐丰富。

   示例：**复制到 Azure SQL 数据库时的性能优化提示**

   在此示例中，在执行复制运行过程中，Azure 数据工厂将会告知接收器 Azure SQL 数据库已达到高 DTU 利用率，从而减慢了写入速度。 建议使用更多的 DTU 来提高 Azure SQL 数据库层。 

   ![包含性能优化提示的复制监视](media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

   此外, 还应注意以下一些性能优化功能:

   - [并行复制](#parallel-copy)
   - [数据集成单元](#data-integration-units)
   - [暂存复制](#staged-copy)
   - [自承载集成运行时可伸缩性](concepts-integration-runtime.md#self-hosted-integration-runtime)

5. **将配置扩展至整个数据集。** 对执行结果和性能满意时，可以扩展定义和管道以覆盖整个数据集。

## <a name="copy-performance-optimization-features"></a>复制性能优化功能

Azure 数据工厂提供以下性能优化功能:

- [并行复制](#parallel-copy)
- [数据集成单元](#data-integration-units)
- [暂存复制](#staged-copy)

### <a name="data-integration-units"></a>数据集成单元

数据集成单元是一种度量单位，代表单个单位在 Azure 数据工厂中的能力（包含 CPU、内存、网络资源分配）。 数据集成单元仅适用于 [Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime)，而不适用于[自承载集成运行时](concepts-integration-runtime.md#self-hosted-integration-runtime)。

将按 **# \*个使用的 DIUs 复制持续时间\*单位价格/DIU**。 请参阅[此处](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)的当前价格。 每个订阅类型可能应用本地货币和单独折扣。

允许复制活动运行的允许 DIUs**介于2到256之间**。 如果未指定或在用户界面上选择 "自动", 则数据工厂会根据源接收器对和数据模式动态应用最佳 DIU 设置。 下表列出了不同复制方案中使用的默认 DIUs:

| 复制方案 | 服务决定的默认 DIU 数目 |
|:--- |:--- |
| 在基于文件的存储之间复制数据 | 4 到 32 个，具体取决于文件的数量和大小 |
| 将数据复制到 Azure SQL 数据库或 Azure Cosmos DB |4 到 16 个，具体取决于接收器 Azure SQL 数据库或 Cosmos DB 的层（DTU/RU 数目） |
| 所有其他复制方案 | 4 |

若要替代此默认值，请如下所示指定 **dataIntegrationUnits** 属性的值。 复制操作在运行时使用的*实际 DIU 数*等于或小于配置的值，具体取决于数据模式。

监视活动运行时，可以在复制活动输出中查看用于每个复制运行的 DIU。 有关详细信息，请参阅[复制活动监视](copy-activity-overview.md#monitoring)。

> [!NOTE]
> 仅当你将多个文件从 Azure 存储、Azure Data Lake Storage、Amazon S3、Google Cloud Storage、cloud FTP 或云 SFTP 复制到任何其他云数据存储时, 才会将设置为大于4的 DIUs。

**示例：**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "dataIntegrationUnits": 32
        }
    }
]
```

### <a name="parallel-copy"></a>并行复制

可使用 **parallelCopies** 属性指示要让复制活动使用的并行度。 可将此属性视为复制活动内，可从源并行读取或并行写入接收器数据存储的最大线程数。

对于每个复制活动运行，Azure 数据工厂确定用于将数据从源数据存储复制到目标数据存储的并行复制数。 它使用的默认并行复制数取决于使用的源和接收器类型。

| 复制方案 | 由服务确定的默认并行复制计数 |
| --- | --- |
| 在基于文件的存储之间复制数据 |取决于文件大小以及用于在两个云数据存储之间复制数据的 DIU 数，或自承载集成运行时计算机的物理配置。 |
| 从关系数据存储中复制已启用分区选项 (包括[Oracle](connector-oracle.md#oracle-as-source)、 [Netezza](connector-netezza.md#netezza-as-source)、 [Teradata](connector-teradata.md#teradata-as-source)、 [sap 表](connector-sap-table.md#sap-table-as-source)和[sap 开放式集线器](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source))|4 |
| 将数据从任何源存储复制到 Azure 表存储 |4 |
| 所有其他复制方案 |1 |

> [!TIP]
> 在基于文件的存储之间复制数据时，默认行为通常可提供最佳吞吐量。 默认行为是根据源文件模式自动确定的。

若要控制托管数据存储的计算机上的负载或优化复制性能，可以替代默认值并为 **parallelCopies** 属性指定值。 该值必须是大于或等于 1 的整数。 在运行时，为了获得最佳性能，复制活动使用小于或等于所设置的值。

**需要注意的要点：**

- 在基于文件的存储之间复制数据时，**parallelCopies** 确定文件级别的并行度。 单个文件内的区块化会自动透明地在文件下进行。 它旨在对给定源数据存储类型使用最佳区块大小，以并行独立方式将数据加载到 **parallelCopies**。 数据移动服务在运行时用于复制操作的并行复制的实际数量不超过所拥有的文件数。 如果复制行为是 **mergeFile**，复制活动无法利用文件级别的并行度。
- 如果从不基于文件的存储 ( [Oracle](connector-oracle.md#oracle-as-source)、 [Netezza](connector-netezza.md#netezza-as-source)、 [Teradata](connector-teradata.md#teradata-as-source)、 [sap 表](connector-sap-table.md#sap-table-as-source)和[sap 开放式中心](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)连接器除外) 将数据复制到基于文件的存储, 则数据移动服务将忽略**parallelCopies**属性。 即使指定了并行性，在此情况下也不适用。
- **parallelCopies** 属性独立于 **dataIntegrationUnits**。 前者跨所有数据集成单元进行计数。
- 为 **parallelCopies** 属性指定值时，请考虑源和接收器数据存储上的负载会增加。 另外请考虑到，如果复制活动由自承载集成运行时提供支持（例如，使用混合复制时），则自承载集成运行时的负载也会增加。 尤其在有多个活动或针对同一数据存储运行的相同活动有并发运行时，会发生这种负载增加的情况。 如果注意到数据存储或自承载集成运行时负载过重，请降低 **parallelCopies**  值以减轻负载。

**示例：**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 32
        }
    }
]
```

### <a name="staged-copy"></a>暂存复制

将数据从源数据存储复制到接收器数据存储时，可能会选择使用 Blob 存储作为过渡暂存存储。 暂存在以下情况下特别有用：

- **通过 PolyBase 从各种数据存储将数据引入 SQL 数据仓库。** SQL 数据仓库使用 PolyBase 作为高吞吐量机制，将大量数据加载到 SQL 数据仓库中。 源数据必须位于 Blob 存储或 Azure Data Lake Store 中, 并且必须满足其他条件。 从 Blob 存储或 Azure Data Lake Store 以外的数据存储加载数据时，可通过过渡暂存 Blob 存储激活数据复制。 在这种情况下，Azure 数据工厂会执行所需的数据转换，确保其满足 PolyBase 的要求。 然后，它使用 PolyBase 将数据高效加载到 SQL 数据仓库。 有关详细信息，请参阅[使用 PolyBase 将数据加载到 Azure SQL 数据仓库](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)。
- **有时，通过速度慢的网络连接执行混合数据移动（即从本地数据存储复制到云数据存储）需要一段时间。** 为了提高性能，可以使用暂存复制来压缩本地数据，缩短将数据移动到云中的暂存数据存储的时间。 然后，可先在暂存存储中解压缩数据，再将它们加载到目标数据存储。
- **由于企业 IT 策略，不希望在防火墙中打开除端口 80 和端口 443 以外的端口。** 例如，将数据从本地数据存储复制到 Azure SQL 数据库接收器或 Azure SQL 数据仓库接收器时，需要对 Windows 防火墙和公司防火墙激活端口 1433 上的出站 TCP 通信。 在这种情况下，暂存复制可以利用自承载集成运行时首先在端口 443 上通过 HTTP 或 HTTPS 将数据复制到 Blob 存储暂存实例。 然后，它可以将数据从 Blob 暂存存储加载到 SQL 数据库或 SQL 数据仓库中。 在此流中，不需要启用端口 1433。

#### <a name="how-staged-copy-works"></a>暂存复制的工作原理

激活暂存功能时，首先将数据从源数据存储复制到暂存 Blob 存储（自带）。 然后，将数据从暂存数据存储复制到接收器数据存储。 Azure 数据工厂自动管理两阶段流。 数据移动完成后，Azure 数据工厂还将清除暂存存储中的临时数据。

![暂存复制](media/copy-activity-performance/staged-copy.png)

使用暂存存储激活数据移动时，可指定是否要先压缩数据，再将数据从源数据存储移动到过渡数据存储或暂存数据存储，然后先解压缩数据，再将数据从过渡数据存储或暂存数据移动到接收器数据存储。

目前，无论是否使用暂存复制，都无法在通过不同自承载 IR 连接的两个数据存储之间复制数据。 对于这种情况，可以配置两个显式链接的复制活动，将数据从源复制到暂存存储，然后从暂存存储复制到接收器。

#### <a name="configuration"></a>配置

在复制活动中配置 **enableStaging** 设置，指定在将数据加载到目标数据存储之前是否要在 Blob 存储中暂存。 将 **enableStaging** 设置为 `TRUE` 时，请指定下表中列出的其他属性。 如果未指定，则还需要创建 Azure 存储或存储共享访问签名链接服务供暂存用。

| 属性 | 说明 | 默认值 | 必填 |
| --- | --- | --- | --- |
| enableStaging |指定是否要通过过渡暂存存储复制数据。 |False |否 |
| linkedServiceName |指定 [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) 链接服务的名称，这指用作过渡暂存存储的存储实例。 <br/><br/> 无法使用具有共享访问签名的存储通过 PolyBase 将数据加载到 SQL 数据仓库。 可在其他任何情况下使用它。 |不适用 |将 **enableStaging** 设置为 TRUE 时，则为是 |
| path |指定要包含此暂存数据的 Blob 存储路径。 如果不提供路径，该服务将创建容器以存储临时数据。 <br/><br/> 只在使用具有共享访问签名的存储时，或者要求临时数据位于特定位置时才指定路径。 |不适用 |否 |
| enableCompression |指定是否应先压缩数据，再将数据复制到目标。 此设置可减少传输的数据量。 |False |否 |

>[!NOTE]
> 若使用暂存复制并启用压缩，则不支持对暂存 Blob 链接服务的服务主体或 MSI 身份验证。

以下是具有上表所述属性的复制活动的示例定义：

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "stagingcontainer/path",
                "enableCompression": true
            }
        }
    }
]
```

#### <a name="staged-copy-billing-impact"></a>暂存复制计费影响

基于两个步骤进行计费：复制持续时间和复制类型。

* 在云复制期间（将数据从一个云数据存储复制到另一个云数据存储，两个阶段均由 Azure Integration Runtime 提供支持）使用暂存时，需要支付 [步骤 1 和步骤 2 的复制持续时间总和] x [云复制单元价格]。
* 在混合复制期间（将数据从本地数据存储复制到云数据存储，一个阶段由自承载集成运行时提供支持）使用暂存时，需要支付 [混合复制持续时间] x [混合复制单元价格] + [云复制持续时间] x [云复制单元价格]。

## <a name="references"></a>参考资料

下面是有关一些受支持数据存储的性能监视和优化参考：

* Azure 存储，包括 Blob 存储和表存储：[Azure 存储可伸缩性目标](../storage/common/storage-scalability-targets.md)和 [Azure存储性能和可伸缩性清单](../storage/common/storage-performance-checklist.md)。
* Azure SQL 数据库：可[监视性能](../sql-database/sql-database-single-database-monitor.md)并检查数据库事务单位 (DTU) 百分比。
* Azure SQL 数据仓库：其功能以数据仓库单位 (DWU) 衡量。 请参阅[管理 Azure SQL 数据仓库中的计算能力（概述）](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)。
* Azure Cosmos DB：[Azure Cosmos DB 中的性能级别](../cosmos-db/performance-levels.md)。
* 本地 SQL Server：[性能监视和优化](https://msdn.microsoft.com/library/ms189081.aspx)。
* 本地文件服务器：[文件服务器性能优化](https://msdn.microsoft.com/library/dn567661.aspx)。

## <a name="next-steps"></a>后续步骤
请参阅其他复制活动文章：

- [复制活动概述](copy-activity-overview.md)
- [使用 Azure 数据工厂将数据从 data lake 或数据仓库迁移到 Azure](data-migration-guidance-overview.md)
- [将数据从 Amazon S3 迁移到 Azure 存储](data-migration-guidance-s3-azure-storage.md)
