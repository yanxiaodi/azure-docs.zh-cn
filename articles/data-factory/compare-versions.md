---
title: Azure 数据工厂与数据工厂版本 1 之对比 | Microsoft Docs
description: 本文将 Azure 数据工厂与 Azure 数据工厂版本 1 进行了比较。
services: data-factory
documentationcenter: ''
author: kromerm
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: overview
ms.date: 04/09/2018
ms.author: makromer
ms.openlocfilehash: 4cdb517e644d55504bfdafbd3bacdfd4bfa0b36c
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479301"
---
# <a name="compare-azure-data-factory-with-data-factory-version-1"></a>Azure 数据工厂与数据工厂版本 1 之对比
本文将数据工厂与数据工厂版本 1 进行了比较。 有关数据工厂的简介，请参阅[数据工厂简介](introduction.md)。有关数据工厂版本 1 的简介，请参阅 [Azure 数据工厂简介](v1/data-factory-introduction.md)。 

## <a name="feature-comparison"></a>功能比较
下表将数据工厂的功能与数据工厂版本 1 的功能进行了比较。 

| Feature | 版本 1 | 当前版本 | 
| ------- | --------- | --------- | 
| 数据集 | 数据的命名视图，以输入和输出的形式引用活动中要使用的数据。 数据集可识别不同数据存储（如表、文件、文件夹和文档）中的数据。 例如，Azure Blob 数据集可在 Azure Blob 存储中指定供活动读取数据的 Blob 容器和文件夹。<br/><br/>**可用性**定义数据集的处理时段切片模型（例如，每小时、每天等）。 | 数据集在当前版本中相同。 但不需要为数据集定义**可用性**计划。 可以定义触发器资源，以便根据时钟计划程序范例来计划管道。 有关详细信息，请参阅[触发器](concepts-pipeline-execution-triggers.md#triggers)和[数据集](concepts-datasets-linked-services.md)。 | 
| 链接服务 | 链接服务十分类似于连接字符串，用于定义数据工厂连接到外部资源时所需的连接信息。 | 链接服务与数据工厂 V1 中的相同，但有一个新的 **connectVia** 属性，可以利用数据工厂的当前版本的集成运行时计算环境。 有关详细信息，请参阅 [Azure 数据工厂中的 Integration Runtime](concepts-integration-runtime.md) 和 [Azure Blob 存储的链接服务属性](connector-azure-blob-storage.md#linked-service-properties)。 |
| 管道 | 数据工厂可以包含一个或多个数据管道。 “管道”是共同执行一项任务的活动的逻辑分组。 可使用 startTime、endTime、isPaused 来计划和运行管道。 | 管道是对数据执行的成组活动。 但是，管道中活动的计划已单独划归到新的触发器资源中。 可以将数据工厂的当前版本中的管道视为“工作流单位”，可以单独地通过触发器对其进行计划，这样更贴切些。 <br/><br/>在数据工厂的当前版本中，管道不按时间“段”来执行。 数据工厂 V1 中的 startTime、endTime 和 isPaused 概念在数据工厂的当前版本中不再存在。 有关详细信息，请参阅[管道执行和触发器](concepts-pipeline-execution-triggers.md)与[管道和活动](concepts-pipelines-activities.md)。 |
| 活动 | “活动”用于定义在管道中对数据执行的操作。 支持数据移动（复制活动）和数据转换活动（例如 Hive、Pig 和 MapReduce）。 | 在数据工厂的当前版本中，活动仍然是在管道中定义的操作。 数据工厂的当前版本引入了新的[控制流活动](concepts-pipelines-activities.md#control-activities)。 可以在控制流（循环和分支）中使用这些活动。 在 V1 中受支持的数据移动和数据转换活动在当前版本中也受支持。 在当前版本中，在定义转换活动时可以不使用数据集。 |
| 混合数据移动和活动分派 | [数据管理网关](v1/data-factory-data-management-gateway.md)即现在的 Integration Runtime，支持在本地和云之间移动数据。| 数据管理网关现在称为自承载 Integration Runtime。 它提供的功能与 V1 中的相同。 <br/><br/> 数据工厂的当前版本中的 Azure-SSIS Integration Runtime 还支持在云中部署和运行 SQL Server Integration Services (SSIS) 包。 有关详细信息，请参阅 [Azure 数据工厂中的集成运行时](concepts-integration-runtime.md)。|
| parameters | NA | 参数是在管道中定义的只读配置设置的键值对。 可以在手动运行管道时，传递参数的自变量。 如果使用计划程序触发器，该触发器还可以传递参数的值。 管道中的活动使用参数值。  |
| 表达式 | 数据工厂 V1 允许在数据选择查询和活动/数据集属性中使用函数和系统变量。 | 在数据工厂的当前版本中，可以在 JSON 字符串值中的任何位置使用表达式。 有关详细信息，请参阅[数据工厂的当前版本中的表达式和函数](control-flow-expression-language-functions.md)。|
| 管道运行 | NA | 管道执行的单个实例。 例如，假设你有一个管道分别在上午 8 点、9 点和 10 点执行。 在这种情况下，将分三次单独运行管道（即三次管道运行）。 每次管道运行都有唯一的管道运行 ID。 管道运行 ID 是一个 GUID，用于对特定的管道运行进行唯一定义。 管道运行通常通过将自变量传递给管道中定义的参数进行实例化。 |
| 活动运行 | NA | 管道中活动执行的实例。 | 
| 触发器运行 | NA | 触发器执行的实例。 有关详细信息，请参阅[触发器](concepts-pipeline-execution-triggers.md)。 |
| 计划 | 按管道开始/结束时间和数据集可用性进行计划。 | 使用计划程序触发器进行计划，或者通过外部计划程序来执行计划。 有关详细信息，请参阅[管道执行和触发器](concepts-pipeline-execution-triggers.md)。 |

以下各部分详细介绍了当前版本的功能。 

## <a name="control-flow"></a>控制流  
为了支持现代数据仓库中的各种集成流和模式，数据工厂的当前版本启用了新的灵活的数据管道模型，该模型不再与时序数据绑定。 有几个常见流是以前不可用但现在可用的。 这些流在下面各节中介绍。   

### <a name="chaining-activities"></a>链接活动
在 V1 中，必须将一个活动的输出配置为另一个活动的输入才能将两个活动链接在一起。 在当前版本中，可以在管道中将活动按顺序链接起来。 可以使用活动定义中的 **dependsOn** 属性将该活动与上游活动链接起来。 有关详细信息和示例，请参阅[管道和活动](concepts-pipelines-activities.md#multiple-activities-in-a-pipeline)与[分支和链接活动](tutorial-control-flow.md)。 

### <a name="branching-activities"></a>分支活动
在当前版本中，可以在管道中对活动进行分支。 [If-condition 活动](control-flow-if-condition-activity.md)可提供 `if` 语句在编程语言中提供的相同功能。 当条件计算结果为 `true` 时，它会计算一组活动，当条件计算结果为 `false` 时，它会计算另一组活动。 有关分支活动的示例，请参阅[分支和链接活动](tutorial-control-flow.md)教程。

### <a name="parameters"></a>parameters 
可以在管道级别定义参数，在按需或通过触发器调用管道时传递自变量。 活动可以使用传递给管道的自变量。 有关详细信息，请参阅[管道和触发器](concepts-pipeline-execution-triggers.md)。 

### <a name="custom-state-passing"></a>自定义状态传递
包含状态的活动输出可供管道中的后续活动使用。 例如，在活动的 JSON 定义中，可以使用以下语法访问上一活动的输出：`@activity('NameofPreviousActivity').output.value`。 可以使用此功能生成工作流，以便通过活动来传递值。

### <a name="looping-containers"></a>循环容器
[ForEach 活动](control-flow-for-each-activity.md)在管道中定义重复的控制流。 此活动可循环访问集合，并可在循环中运行指定的活动。 此活动的循环实现类似于采用编程语言的 Foreach 循环结构。 

[Until](control-flow-until-activity.md) 活动提供的功能与编程语言中 do-until 循环结构提供的功能相同。 它在循环中运行一组活动，直到与活动相关联的条件的计算结果为 `true`。 你可以在数据工厂中为 Until 活动指定超时值。  

### <a name="trigger-based-flows"></a>基于触发器的流
可以按需（基于事件，即 blob post）或按时钟时间触发管道。 [管道和触发器](concepts-pipeline-execution-triggers.md)一文详细介绍了触发器。 

### <a name="invoking-a-pipeline-from-another-pipeline"></a>从一个管道调用另一个管道
[Execute Pipeline 活动](control-flow-execute-pipeline-activity.md)允许一个数据工厂管道调用另一个管道。

### <a name="delta-flows"></a>增量流
在 ETL 模式中，一个重要用例是“增量加载”，即只加载自上次管道迭代后更改了的数据。 利用当前版本中提供的新功能（例如[查找活动](control-flow-lookup-activity.md)、灵活计划、控制流），可以很自然地启用该用例。 有关分步说明的教程，请参阅[教程：增量复制](tutorial-incremental-copy-powershell.md)。

### <a name="other-control-flow-activities"></a>其他控制流活动
下面是数据工厂的当前版本支持的其他控制流活动。 

控制活动 | 说明
---------------- | -----------
[ForEach 活动](control-flow-for-each-activity.md) | 在管道中定义重复的控制流。 此活动用于循环访问集合，并在循环中运行指定的活动。 此活动的循环实现类似于采用编程语言的 Foreach 循环结构。
[Web 活动](control-flow-web-activity.md) | 从数据工厂管道调用自定义的 REST 终结点。 可以传递数据集和链接服务以供活动使用和访问。 
[Lookup 活动](control-flow-lookup-activity.md) | 从任何外部源读取或查找记录或表名称值。 此输出可进一步由后续活动引用。 
[Get Metadata 活动](control-flow-get-metadata-activity.md) | 在 Azure 数据工厂中检索任何数据的元数据。 
[Wait 活动](control-flow-wait-activity.md) | 暂停管道一段指定的时间。

## <a name="deploy-ssis-packages-to-azure"></a>将 SSIS 包部署到 Azure 
若要将 SSIS 工作负荷移至云，请使用 Azure-SSIS，通过当前版本创建数据工厂，然后预配 Azure-SSIS Integration Runtime。

Azure-SSIS Integration Runtime 是由 Azure VM（节点）构成的完全托管群集，专用于在云中运行 SSIS 包。 预配 Azure-SSIS Integration Runtime 以后，即可使用曾经用过的相同工具将 SSIS 包部署到本地 SSIS 环境。 

例如，可以使用 SQL Server Data Tools 或 SQL Server Management Studio 将 SSIS 包部署到 Azure 上的此运行时。 有关分步说明，请参阅教程：[将 SQL Server Integration Services 包部署到 Azure](tutorial-create-azure-ssis-runtime-portal.md)。 

## <a name="flexible-scheduling"></a>灵活计划
在数据工厂的当前版本中，不需定义数据集可用性计划。 可以定义触发器资源，以便根据时钟计划程序范例来计划管道。 对于灵活的计划和执行模型，还可以将参数从触发器传递到管道。 

在数据工厂的当前版本中，管道不按时间“段”来执行。 数据工厂 V1 中的 startTime、endTime 和 isPaused 概念在数据工厂的当前版本中不存在。 若要详细了解如何在数据工厂的当前版本中生成并计划管道，请参阅[管道执行和触发器](concepts-pipeline-execution-triggers.md)。

## <a name="support-for-more-data-stores"></a>支持更多数据存储
与 V1 相比，当前版本支持将数据复制到更多数据存储，以及从更多数据存储复制数据。 有关受支持的数据存储的列表，请参阅以下文章：

- [版本 1 - 支持的数据存储](v1/data-factory-data-movement-activities.md#supported-data-stores-and-formats)
- [当前版本 - 支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)

## <a name="support-for-on-demand-spark-cluster"></a>支持按需 Spark 群集
当前版本支持创建按需 Azure HDInsight Spark 群集。 若要创建按需 Spark 群集，请在按需 HDInsight 链接服务定义中将群集类型指定为“Spark”。 然后即可在管道中将 Spark 活动配置为使用该链接服务。 

数据工厂服务在运行时（执行活动时）自动为你创建 Spark 群集。 有关详细信息，请参阅以下文章：

- [数据工厂的当前版本中的 Spark 活动](transform-data-using-spark.md)
- [Azure HDInsight 按需链接服务](compute-linked-services.md#azure-hdinsight-on-demand-linked-service)

## <a name="custom-activities"></a>自定义活动
在 V1 中，可以通过一个实现 IDotNetActivity 接口的 Execute 方法的类来创建 .NET 类库项目，从而实现（自定义）DotNet 活动代码。 因此，需在 .NET Framework 4.5.2 中编写自定义代码，并需在基于 Windows 的 Azure Batch 池节点上运行该代码。 

在当前版本中，在自定义活动中无需实现 .NET 接口。 可以直接运行命令、脚本和自己的已编译为可执行文件的自定义代码。 

有关详细信息，请参阅[数据工厂与版本 1 之间自定义活动的区别](transform-data-using-dotnet-custom-activity.md#compare-v2-v1)。

## <a name="sdks"></a>SDK
 数据工厂的当前版本提供了可以用来创作、管理和监视管道的更为丰富的一组 SDK。

- .NET SDK  ：.NET SDK 在当前版本中进行了更新。

- PowerShell  ：PowerShell cmdlet 在当前版本中进行了更新。 当前版本的 cmdlet 在名称中带有 **DataFactoryV2**，例如：Get-AzDataFactoryV2. 

- **Python SDK**：此 SDK 是当前版本中新增的。

- **REST API**：REST API 在当前版本中进行了更新。 

在当前版本中进行了更新的 SDK 不能向后兼容 V1 客户端。 

## <a name="authoring-experience"></a>创作体验

| &nbsp; | V2 | V1 |
| ------ | -- | -- | 
| Azure 门户 | [是](quickstart-create-data-factory-portal.md) | 否 |
| Azure PowerShell | [是](quickstart-create-data-factory-powershell.md) | [是](data-factory-build-your-first-pipeline-using-powershell.md) |
| .NET SDK | [是](quickstart-create-data-factory-dot-net.md) | [是](data-factory-build-your-first-pipeline-using-vs.md) |
| REST API | [是](quickstart-create-data-factory-rest-api.md) | [是](data-factory-build-your-first-pipeline-using-rest-api.md) |
| Python SDK | [是](quickstart-create-data-factory-python.md) | 否 |
| 资源管理器模板 | [是](quickstart-create-data-factory-resource-manager-template.md) | [是](data-factory-build-your-first-pipeline-using-arm.md) | 

## <a name="roles-and-permissions"></a>角色和权限

可以使用数据工厂版本 1 参与者角色创建和管理当前版本的数据工厂资源。 有关详细信息，请参阅[数据工厂参与者](../role-based-access-control/built-in-roles.md#data-factory-contributor)。

## <a name="monitoring-experience"></a>监视体验
在当前版本中，也可通过 [Azure Monitor](monitor-using-azure-monitor.md) 来监视数据工厂。 新的 PowerShell cmdlet 支持对 [Integration Runtime](monitor-integration-runtime.md) 进行监视。 V1 和 V2 都支持通过可以从 Azure 门户启动的监视应用程序进行视觉监视。


## <a name="next-steps"></a>后续步骤
了解如何按照以下快速入门中的分步说明创建数据工厂：[PowerShell](quickstart-create-data-factory-powershell.md)、[.NET](quickstart-create-data-factory-dot-net.md)、[Python](quickstart-create-data-factory-python.md)、[REST API](quickstart-create-data-factory-rest-api.md)。 
