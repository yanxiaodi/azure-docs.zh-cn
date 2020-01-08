---
title: 使用读取访问异地冗余存储（GZRS 或 GRS）设计高度可用的应用程序 |Microsoft Docs
description: 如何使用 Azure GZRS 或 GRS 存储来构建高度可用的应用程序，使其有足够的灵活性来处理中断。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/14/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: a6d724f834fb8a4c54cd613c61ca90a77a36bdea
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673117"
---
# <a name="designing-highly-available-applications-using-read-access-geo-redundant-storage"></a>使用读取访问异地冗余存储设计高度可用的应用程序

基于云的基础结构（如 Azure 存储）的一个常见功能是提供用于托管应用程序的高度可用平台。 基于云的应用程序开发人员必须仔细考虑如何利用此平台为其用户提供高度可用的应用程序。 本文重点介绍开发人员如何使用 Azure 的异地冗余复制选项之一来确保其 Azure 存储应用程序高度可用。

为异地冗余复制配置的存储帐户将以同步方式复制到主要区域，然后以异步方式复制到数百英里以外的次要区域。 Azure 存储提供两种类型的异地冗余复制：

* [区域冗余存储（GZRS）（预览版）](storage-redundancy-gzrs.md)可为需要高可用性和最大持续性的方案提供复制。 使用区域冗余存储（ZRS）以同步方式将数据复制到主区域中的三个 Azure 可用性区域，并将其异步复制到次要区域。 若要对次要区域中的数据进行读取访问，请启用读取访问权限异地冗余存储（GZRS）。
* [异地冗余存储 (GRS)](storage-redundancy-grs.md) 提供跨区域复制来防范区域性的服务中断。 数据将使用本地冗余存储 (LRS) 在主要区域中以同步方式复制三次，然后以异步方式复制到次要区域。 若要对次要区域中的数据进行读取访问，请启用读取访问异地冗余存储 (RA-GRS)。

本文介绍如何设计应用程序以应对主要区域中发生的服务中断。 如果主要区域不可用，应用程序可以调整为对次要区域执行读取操作。 在开始之前，请确保已为 GRS 或 GZRS 配置存储帐户。

如需深入了解主要区域与次要区域的配对情况，请参阅[业务连续性和灾难恢复 (BCDR)：Azure 配对区域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)。

本文包含代码片段，末尾有完成示例的链接，可以下载并运行。

## <a name="application-design-considerations-when-reading-from-the-secondary"></a>从次要区域读取数据时的应用程序设计注意事项

本文旨在介绍：如何设计在主数据中心发生重大灾难时仍可继续使用（有限功能）的应用程序。 可以将应用程序设计为在出现问题无法从主要区域读取时，通过从次要区域读取来处理暂时性或长时间运行的问题。 当主要区域重新变为可用时，应用程序可恢复为从主要区域读取。

设计适用于 GRS 或 GZRS 的应用程序时，请记住以下要点：

* Azure 存储在次要区域中保留主要区域中存储的数据的只读副本。 如上所述，存储服务确定次要区域的位置。

* 只读副本与主要区域中的数据[最终一致](https://en.wikipedia.org/wiki/Eventual_consistency)。

* 对于 blob、表和队列，可以从次要区域查询上次同步时间的值，了解上次从主要区域复制到次要区域的时间。 （Azure 文件不支持此操作，因为其目前不具有 RA-GRS 冗余。）

* 可以使用存储客户端库在主要或次要区域中读取和写入数据。 如果到主要区域的读取请求超时，还可将读取请求自动重定向到次要区域。

* 如果主要区域变得不可用，则可发起帐户故障转移。 故障转移到次要区域时，指向主要区域的 DNS 条目更改为指向次要区域。 故障转移完成后，GRS 和 RA-GRS 帐户的写入访问会恢复。 有关详细信息，请参阅 [Azure 存储中的灾难恢复和存储帐户故障转移（预览版）](storage-disaster-recovery-guidance.md)。

> [!NOTE]
> 客户管理的帐户故障转移（预览版）在支持 GZRS/GZRS 的区域中尚不可用，因此客户当前无法使用 GZRS 和 RA GZRS 帐户来管理帐户故障转移事件。 在预览期间，Microsoft 将管理影响 GZRS/GZRS 帐户的任何故障转移事件。

### <a name="using-eventually-consistent-data"></a>使用最终一致的数据

此建议的解决方案假定可以向调用方应用程序返回可能过时的数据。 由于次要区域中的数据是最终一致的，因此在对次要区域的更新完成复制前，主要区域可能会变为不可访问。

例如，假设客户提交更新成功，但在更新传播到次要区域前主要区域发生故障。 当客户要求读回数据时，将收到来自次要区域的过时数据而非更新后的数据。 在设计应用程序时，必须确定这是否可接受，如果可接受，如何告知客户。 

本文的后面部分介绍如何查看次要数据的“上次同步时间”，以了解次要区域是否为最新状态。

### <a name="handling-services-separately-or-all-together"></a>单独或整体处理服务

尽管可能性不大，但当其他服务仍然完全可用时，某一个服务可能不可用。 可单独为每个服务（blob、队列、表）处理重试操作或只读模式，或者以一般方式为所有存储服务统一处理重试操作。

例如，如果在应用程序中使用队列和 blob，可以使用单独的代码处理每个队列或 blob 的重试错误。 然后如果从 blob 服务中重试，但队列服务仍在工作，那么只有应用程序处理 blob 的部分会受到影响。 如果决定以一般方式处理所有存储服务重试操作，并使对 blob 服务的调用返回可重试错误，则对 blob 服务和队列服务的请求将受到影响。

从根本上讲，这取决于应用程序的复杂程度。 当检测到主要区域中的任何存储服务存在问题时，可以不按服务处理失败，而改为将对所有存储服务的读取请求重定向到次要区域，并在只读模式下运行应用程序。

### <a name="other-considerations"></a>其他注意事项

本文的其余部分将讨论其他注意事项。

* 使用断路器模式处理读取请求的重试操作

* 最终一致的数据和上次同步时间

* 正在测试

## <a name="running-your-application-in-read-only-mode"></a>在只读模式下运行应用程序

若要有效地应对主要区域中发生的服务中断，必须能够处理失败的读取请求和失败的更新请求（此处所谓的更新是指插入、更新和删除）。 如果主要区域发生故障，读取请求可重定向到次要区域。 但更新请求不能重定向到备用数据中心，因为备用数据中心是只读的。 因此，需要将应用程序设计为在只读模式下运行。

例如，可以设置一个标志，在向 Azure 存储提交任何更新请求前需检查此标志。 当其中一个更新请求成功时，可以跳过它，并向客户返回适当的响应。 在问题解决前，甚至可以禁用某些功能，并通知用户这些功能是暂时不可用。

如果决定分别处理每个服务的错误，则还需要处理在只读模式下按服务运行应用程序的能力。 例如，可以为每个可启用和禁用的服务设置只读标志。 然后可以在代码中的适当位置处理该标志。

无法在只读模式下运行应用程序具有另一个连带好处 - 可在主要应用程序升级期间确保有限的功能。 可以触发应用程序在只读模式下运行并指向备用数据中心，确保在升级时没有任何用户访问主要区域中的数据。

## <a name="handling-updates-when-running-in-read-only-mode"></a>以只读模式运行时处理更新

以只读模式运行时，可使用多种方法处理更新请求。 我们不会对此进行全面介绍，但通常可考虑以下几种模式。

1. 可以对用户进行响应，并告知他们当前不接受更新。 例如，联系人管理系统可使客户访问联系信息但不能进行更新。

2. 可将更新放入另一区域进行排队。 在这种情况下，可将挂起的更新请求写入不同区域中的队列，并在主数据中心再次联机后以某种方式处理这些请求。 在此方案中，应让客户知道更新请求已排队等待稍后处理。

3. 可将更新写入其他区域中的存储帐户。 然后在主数据中心重新联机后，可以某种方式将这些更新合并到主要数据中，具体取决于数据的结构。 例如，如果使用名称中的日期/时间戳创建单独的文件，可将这些文件复制回主要区域。 此操作适用于某些工作负荷，例如日志记录和 iOT 数据。

## <a name="handling-retries"></a>处理重试操作

Azure 存储客户端库可帮助你确定可重试的错误。 例如，404 错误（找不到资源）是可重试的，因为重试不太可能成功。 而 500 错误是不可重试的，因为这属于服务器错误，而且可能只是暂时性问题。 有关详细信息，请参阅 .NET 存储客户端库中的[打开 ExponentialRetry 类的源代码](https://github.com/Azure/azure-storage-net/blob/87b84b3d5ee884c7adc10e494e2c7060956515d0/Lib/Common/RetryPolicies/ExponentialRetry.cs)。 （查找 ShouldRetry 方法。）

### <a name="read-requests"></a>阅读请求

如果主存储存在问题，读取请求可重定向到辅助存储。 如在上文[使用最终一致的数据](#using-eventually-consistent-data)中所述，应用程序必须可潜在读取过时数据。 如果使用存储客户端库访问次要区域中的数据，可通过将 **LocationMode** 属性设置为以下之一的值来指定读取请求的重试行为：

* **PrimaryOnly**（默认值）

* **PrimaryThenSecondary**

* **SecondaryOnly**

* **SecondaryThenPrimary**

将 **LocationMode** 设置为 **PrimaryThenSecondary** 时，如果对主终结点的初始读取请求失败且出现可重试的错误，则客户端将自动向辅助终结点发出另一次读取请求。 如果错误是服务器超时，则客户端需要等待超时到期，才能收到来自服务的可重试错误。

确定如何响应可重试错误时，基本上可考虑两种方案：

* 这是一个隔离的问题，对主终结点的后续请求将不会返回可重试错误。 暂时性网络错误就是此情况的示例。

    在此方案中，将 **LocationMode** 设置为 **PrimaryThenSecondary** 不会显著影响性能，这种情况很少发生。

* 这是主要区域中至少一个存储服务可能出现的问题，对主要区域中该服务的所有后续请求都可能在某一时期内返回可重试错误。 主要区域完全不可访问便是此情况的示例。

    此方案会对性能产生负面影响，因为所有读取请求将首先尝试主终结点，等待超时过期，然后才能切换到辅助终结点。

对于这些方案，应注意到主终结点存在一个持续性问题，通过将 **LocationMode** 属性设置为 **SecondaryOnly** 可将所有读取请求直接发送到辅助终结点。 此时，还应将应用程序更改为在只读模式下运行。 此方法称为[断路器模式](/azure/architecture/patterns/circuit-breaker)。

### <a name="update-requests"></a>更新请求

断路器模式还可应用于更新请求。 但是，更新请求不能重定向到辅助存储，因为辅助存储是只读的。 对于这些请求，应将 **LocationMode** 属性设置为 **PrimaryOnly**（默认值）。 要处理这些错误，可将指标应用于这些请求 - 例如一行中 10 个故障 - 并在达到阈值时，将应用程序转换为只读模式。 对于返回到更新模式，可以使用下一部分中描述的关于断路器模式的相同方法。

## <a name="circuit-breaker-pattern"></a>断路器模式

使用应用程序中的断路器模式阻止尝试可能重复失败的操作。 它允许应用程序继续运行，而不是在多次重试操作时占用时间。 它还会在错误修复后进行检测，此时应用程序可再次重试操作。

### <a name="how-to-implement-the-circuit-breaker-pattern"></a>如何实现断路器模式

若要确定主终结点存在持续性问题，可以监视客户端遇到可重试错误的频率。 由于每种情况都不同，因此需要确定切换到辅助终结点并在只读模式下运行应用程序时使用的阈值。 例如，可决定在一行中存在 10 次失败且没有成功记录时执行转换。 另一个示例是在 2 分钟内存在 90% 失败请求时切换。

对于第一个方案，只需保留失败的计数，并且如果在达到最大值前成功，则将计数重新设置为零。 对于第二种方案，一种实现方法是使用 MemoryCache 对象（在 .NET 中）。 对于每个请求，将 CacheItem 添加到缓存，将值设置为成功 (1) 或失败 (0)，并将过期时间设置为从现在起 2 分钟（或任意时间约束）。 当达到条目的过期时间时，会自动删除该条目。 这会提供 2 分钟的滚动窗口。 每次向存储服务发起请求时，首先使用跨 MemoryCache 对象的 Linq 查询通过对值进行求和并除以计数来计算成功的百分比。 当成功百分比低于某个阈值（如 10%）时，将读取权限的 **LocationMode** 属性设置为 **SecondaryOnly**，并在继续前将应用程序切换到只读模式。

用于确定何时切换的错误的阈值根据应用程序中的不同服务而有所差异，因此应考虑将它们设置为可配置参数。 此时还应确定分别还是整体处理可重试错误，如前文所述。

另一个注意事项是如何处理应用程序的多个实例，以及在每个实例中检测到可重试错误时应如何操作。 例如，可以运行 20 个加载相同应用程序的 VM。 是否分别处理每个实例？ 如果实例启动时出现问题，是限制为仅对一个实例作出响应，还是在一个实例出现问题时仍以相同方法对所有实例作出响应？ 单独处理实例比尝试协调跨实例的响应简单得多，但具体操作取决于应用程序的体系结构。

### <a name="options-for-monitoring-the-error-frequency"></a>监视错误频率的选项

可使用三个主要选项监视主要区域中的重试频率，以便确定何时切换到次要区域并将应用程序更改为在只读模式下运行。

* 为传递到存储请求的 [**OperationContext**](https://docs.microsoft.com/java/api/com.microsoft.applicationinsights.extensibility.context.operationcontext) 对象上的[**重试**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.operationcontext.retrying)事件添加处理程序 - 这是本文演示的方法，且在随附的示例中使用了该方法。 每当客户端重试请求时都将触发这些事件，以便跟踪客户端在主终结点上遇到可重试错误的频率。

    ```csharp
    operationContext.Retrying += (sender, arguments) =>
    {
        // Retrying in the primary region
        if (arguments.Request.Host == primaryhostname)
            ...
    };
    ```

* 在自定义重试策略的 [**Evaluate**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.iextendedretrypolicy.evaluate) 方法中，每次重试时均可运行自定义代码。 除了在重试时进行记录外，还可利用此操作修改重试行为。

    ```csharp 
    public RetryInfo Evaluate(RetryContext retryContext,
    OperationContext operationContext)
    {
        var statusCode = retryContext.LastRequestResult.HttpStatusCode;
        if (retryContext.CurrentRetryCount >= this.maximumAttempts
            || ((statusCode >= 300 && statusCode < 500 && statusCode != 408)
            || statusCode == 501 // Not Implemented
            || statusCode == 505 // Version Not Supported
            ))
        {
            // Do not retry
            return null;
        }

        // Monitor retries in the primary location
        ...

        // Determine RetryInterval and TargetLocation
        RetryInfo info =
            CreateRetryInfo(retryContext.CurrentRetryCount);

        return info;
    }
    ```

* 第三种方法是在应用程序中实现自定义监视组件，应用程序对具有虚拟读取请求（如读取小型 blob）的主存储终结点持续执行 ping 操作，以确定其运行状况。 这会占用一些资源，但占用量不大。 发现达到阈值的问题时，则切换到 **SecondaryOnly** 和只读模式。

有时，可能想切换回使用主终结点或允许更新。 如果使用上文列出的前两种方法，则只需在任意选择时间长度或操作数量后切换回主终结点并启用更新模式。 可以再次执行重试逻辑操作。 如果问题得到解决，它将继续使用主终结点，并允许更新。 如果仍然有问题，它会在无法满足设置的标准后再次重新切换到辅助终结点和只读模式。

对于第三个方案，当再次对主存储终结点成功执行 ping 操作时，可触发切换回 **PrimaryOnly** 并继续允许更新。

## <a name="handling-eventually-consistent-data"></a>处理最终一致的数据

异地冗余存储的工作方式是将事务从主要区域复制到次要区域。 此复制过程可确保次要区域中的数据是最终一致的。 这意味着，主要区域中的所有事务最终将都出现在次要区域中，但可能出现延迟，并且无法确保事物按主要区域中的相同原始顺序到达次要区域。 如果事务未按顺序到达次要区域，则在服务生效前，可以认为次要区域中的数据处于不一致状态。

下表显示了更新员工详细信息以使其成为“管理员”角色的成员时可能发生的情况的示例。 此示例要求更新**员工**条目实体和**管理员角色**实体的管理员总数。 请注意更新如何以无序方式在次要区域中应用。

| **时间** | **事务**                                            | **复制**                       | **上次同步时间** | **结果** |
|----------|------------------------------------------------------------|---------------------------------------|--------------------|------------| 
| T0       | 事务 A： <br> 在主要区域中 <br> 插入员工实体 |                                   |                    | 事务 A 已插入到主要区域，<br> 但尚未复制。 |
| T1       |                                                            | 事务 A <br> 复制到<br> 次要区域 | T1 | 事物 A 已复制到次要区域。 <br>已更新“上次同步时间”。    |
| T2       | 事务 B：<br>Update<br> 主要区域中的<br> 员工实体  |                                | T1                 | 事务 B 已写入主要区域，<br> 但尚未复制。  |
| T3       | 事务 C：<br> 更新 <br>主要区域中的<br>中的角色实体<br>primary |                    | T1                 | 事务 C 已写入主要区域，<br> 但尚未复制。  |
| *T4*     |                                                       | 事务 C <br>复制到<br> 次要区域 | T1         | 事物 C 已复制到次要区域。<br>LastSyncTime 未更新，因为 <br>事务 B 尚未复制。|
| *T5*     | 从次要区域 <br>读取实体                           |                                  | T1                 | 获取员工实体的过时值， <br> 因为事务 B 尚未 <br> 复制。 获取管理员角色实体的新值<br> 因为 C 已<br> 复制。 上次同步时间仍未<br> 更新，因为事务 B<br> 尚未复制。 可以判断出<br>管理员角色实体不一致 <br>因为实体日期/时间晚于 <br>上次同步时间。 |
| *T6*     |                                                      | 事务 B<br> 复制到<br> 辅助 | T6                 | *T6* - 通过 C 的所有事务都已 <br>复制，上次同步时间<br> 已更新。 |

在此示例中，假定客户端在 T5 从次要区域切换到读取。 它此时能够成功读取**管理员角色**实体，但该实体包含的管理员数量值与次要区域中此时标记的**员工**数量不一致。 客户端只需显示此值，并且具有信息不一致的风险。 或者，客户端可能会尝试确定**管理员角色**可能是不一致的状态，因为更新是无序进行的，并随后告知用户这一事实。

要识别它可能具有不一致的数据，客户端可以使用通过随时查询存储服务获取的 *上次同步时间* 的值。 借此可了解次要区域中的数据上一次一致的时间，以及服务在该时间点前应用所有事务的时间。 在上述示例中，服务在次要区域中插入**员工**实体后，上次同步时间将设置为 *T1*。 当服务更新次要区域中的**员工**实体前，它仍然保持为 *T1*，之后则设置为 *T6*。 如果客户端在其读取 *T5* 处的实体时检索上次同步时间，它会将其与实体上的时间戳进行对比。 如果实体上的时间戳晚于上次同步时间，则实体可能处于不一致状态，可对应用程序采取任何适当操作。 使用此字段要求了解到主要区域上次更新的时间。

## <a name="getting-the-last-sync-time"></a>获取上次同步时间

可以使用 PowerShell 或 Azure CLI 检索上次同步时间，以确定上次将数据写入次要区域的时间。

### <a name="powershell"></a>PowerShell

若要使用 PowerShell 获取存储帐户的上次同步时间，请安装可支持获取异地复制统计信息的 Azure 存储预览版模块。例如：

```powershell
Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force
```

然后检查存储帐户的 **GeoReplicationStats.LastSyncTime** 属性。 请务必将占位符值替换为你自己的值：

```powershell
$lastSyncTime = $(Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account> `
    -IncludeGeoReplicationStats).GeoReplicationStats.LastSyncTime
```

### <a name="azure-cli"></a>Azure CLI

若要使用 Azure CLI 获取存储帐户的上次同步时间，请检查存储帐户的 **geoReplicationStats.lastSyncTime** 属性。 使用 `--expand` 参数返回在 **geoReplicationStats** 下嵌套的属性的值。 请务必将占位符值替换为你自己的值：

```azurecli
$lastSyncTime=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --expand geoReplicationStats \
    --query geoReplicationStats.lastSyncTime \
    --output tsv)
```

## <a name="testing"></a>正在测试

当应用程序遇到可重试错误时，请务必测试应用程序的行为是否与预期一致。 例如，需要测试应用程序在检测到问题时会切换到辅助数据库和只读模式，并在主要区域可用时再次切换回去。 若要执行此操作，需以某种方式模拟可重试错误并控制其出现的频率。

可以使用 [Fiddler](https://www.telerik.com/fiddler) 在脚本中截获和修改 HTTP 响应。 此脚本可以标识来自主终结点的响应，并将 HTTP 状态代码更改为存储客户端库识别为可重试错误的代码。 此代码片段显示 Fiddler 脚本的简单示例，此脚本截获响应以读取对 **employeedata** 表的读取请求，并返回 502 状态：

```java
static function OnBeforeResponse(oSession: Session) {
    ...
    if ((oSession.hostname == "\[yourstorageaccount\].table.core.windows.net")
      && (oSession.PathAndQuery.StartsWith("/employeedata?$filter"))) {
        oSession.responseCode = 502;
    }
}
```

可使用此示例对范围更广的请求进行截获，并更改其中一些请求的 **responseCode** 以更好地模拟真实方案。 有关自定义 Fiddler 脚本的详细信息，请参阅 Fiddler 文档中的 [Modifying a Request or Response](https://docs.telerik.com/fiddler/KnowledgeBase/FiddlerScript/ModifyRequestOrResponse)（修改请求或响应）。

如果已用于将应用程序切换到只读模式的阈值设置为可配置，则可轻松使用非生产事务量测试行为。

## <a name="next-steps"></a>后续步骤

* 有关如何从次要区域读取数据的详细信息，包括有关如何设置“上次同步时间”属性的另一个示例，请参阅 [Azure 存储冗余选项和读取访问异地冗余存储](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/)。

* 有关演示如何在主终结点和辅助终结点之间来回切换的完整示例，请参阅[Azure 示例–使用 GRS 存储的断路器模式](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs)。
