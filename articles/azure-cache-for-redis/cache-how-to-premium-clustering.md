---
title: 如何为高级 Azure Redis 缓存配置 Redis 群集功能 | Microsoft Docs
description: 了解如何为高级层的 Azure Redis 缓存实例创建和管理 Redis 群集功能
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 62208eec-52ae-4713-b077-62659fd844ab
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 06/13/2018
ms.author: yegu
ms.openlocfilehash: a919ccd2a23acf6e1bd04cda8a5dd18782ff31b0
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2019
ms.locfileid: "71315983"
---
# <a name="how-to-configure-redis-clustering-for-a-premium-azure-cache-for-redis"></a>如何为高级 Azure Redis 缓存配置 Redis 群集功能
Azure Redis 缓存具有不同的缓存产品/服务，从而在缓存大小和功能（包括群集、暂留和虚拟网络支持等高级层功能）的选择上具有灵活性。 本文介绍如何配置高级 Azure Redis 缓存实例中的群集功能。

有关其他高级缓存功能的信息，请参阅 [Azure Redis 缓存高级层简介](cache-premium-tier-intro.md)。

## <a name="what-is-redis-cluster"></a>什么是 Redis 群集？
Azure Redis 缓存提供的 Redis 群集与 [在 Redis 中实施](https://redis.io/topics/cluster-tutorial)的一样。 Redis 群集具有以下优势： 

* 能够在多个节点中自动拆分数据集。 
* 能够在部分节点遇到故障或无法与群集其余部分通信的情况下继续运行。 
* 更大的吞吐量：增加分片数时，吞吐量呈线性增加。 
* 更大的内存大小：增加分片数时，内存大小呈线性增加。  

群集不会增加可用于群集缓存的连接数。 若要深入了解高级缓存的大小、吞吐量和带宽，请参阅[应使用哪种类型和大小的 Azure Redis 缓存产品/服务？](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)

在 Azure 中，Redis 群集以主/副模型提供。在该模型中，每个分片都有一个带副本的主/副对，副本由 Azure Redis 缓存服务管理。 

## <a name="clustering"></a>群集功能
在创建缓存期间，在“新建 Azure Redis 缓存”边栏选项卡上启用群集功能。 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

在“Redis 群集”边栏选项卡上配置群集功能。

![群集功能][redis-cache-clustering]

群集中最多可以有 10 个分片。 单击“启用”，滑动滑块或者针对“分片计数”键入一个 1 到 10 之间的数字，并单击“确定”。

每个分片都是一个由 Azure 管理的主/副缓存对，而缓存的总大小则通过将定价层中选择的缓存大小乘以分片数来计算。 

![群集功能][redis-cache-clustering-selected]

创建缓存后，即可连接到该缓存，并像非群集缓存一样使用，Redis 会将数据分发到整个缓存分片中。 如果诊断[已启用](cache-how-to-monitor.md#enable-cache-diagnostics)，则会为每个分片单独捕获度量值，这些度量值可在 Azure Redis 缓存边栏选项卡中[查看](cache-how-to-monitor.md)。 

> [!NOTE]
> 
> 配置了群集时，客户端应用程序的要求会有一些细微差异。 有关详细信息，请参阅[使用群集功能时，是否需要对客户端应用程序进行更改？](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 

有关在 StackExchange.Redis 客户端中使用群集功能的示例代码，请参阅 [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) 示例的 [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) 部分。

<a name="cluster-size"></a>

## <a name="change-the-cluster-size-on-a-running-premium-cache"></a>更改正在运行的高级缓存上的群集大小
若要更改正在运行并且已启用群集功能的高级缓存上的群集大小，请在“资源菜单”中单击“Redis 群集大小”。

> [!NOTE]
> 虽然 Azure Redis 缓存高级层已发行正式发布版，但 Redis 群集大小功能目前以预览版提供。
> 
> 

![Redis 群集大小][redis-cache-redis-cluster-size]

要更改群集大小，请使用滑块，或在“分片计数”文本框中键入 1 到 10 之间的数字，并单击“确定”进行保存。

增加群集大小会增加最大吞吐量和缓存大小。 增加群集大小不会增加用于客户端的最大连接数据。

> [!NOTE]
> 缩放群集会运行 [MIGRATE](https://redis.io/commands/migrate) 命令，此命令需耗费大量资源，因此为使其影响最小，请考虑在非高峰时段运行此操作。 在迁移过程中，服务器加载将达到峰值。 缩放群集的运行过程耗时较长，所花费的时间量取决于密钥数以及与这些密钥相关联的值的大小。
> 
> 

## <a name="clustering-faq"></a>群集功能常见问题
以下列表包含有关 Azure Redis 缓存群集功能的常见问题解答。

* [使用群集功能时，是否需要对客户端应用程序进行更改？](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
* [密钥在群集中是如何分布的？](#how-are-keys-distributed-in-a-cluster)
* [我可以创建的最大缓存大小是多大？](#what-is-the-largest-cache-size-i-can-create)
* [是否所有 Redis 客户端都支持群集功能？](#do-all-redis-clients-support-clustering)
* [启用群集功能后，如何连接到我的缓存？](#how-do-i-connect-to-my-cache-when-clustering-is-enabled)
* [我可以直接连接到缓存的各个分片吗？](#can-i-directly-connect-to-the-individual-shards-of-my-cache)
* [我可以为以前创建的缓存配置群集功能吗？](#can-i-configure-clustering-for-a-previously-created-cache)
* [我可以为基本缓存或标准缓存配置群集功能吗？](#can-i-configure-clustering-for-a-basic-or-standard-cache)
* [能否在 Redis ASP.NET 会话状态和输出缓存提供程序中使用群集功能？](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)
* [我在使用 StackExchange.Redis 和群集功能时出现 MOVE 异常，应该怎么办？](#i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do)

### <a name="do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering"></a>使用群集功能时，是否需要对客户端应用程序进行更改？
* 启用群集功能时，仅数据库 0 可用。 如果客户端应用程序使用多个数据库并尝试读取或写入数据库 0 之外的其他数据库，则会引发以下异常。 `Unhandled Exception: StackExchange.Redis.RedisConnectionException: ProtocolFailure on GET --->` `StackExchange.Redis.RedisCommandException: Multiple databases are not supported on this server; cannot switch to database: 6`
  
  有关详细信息，请参阅 [Redis Cluster Specification - Implemented subset](https://redis.io/topics/cluster-spec#implemented-subset)（Redis 群集规范 - 已实现子集）。
* 如果使用的是 [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/)，则必须使用 1.0.481 或更高版本。 连接到该缓存时，可以使用的[终结点、端口和密钥](cache-configure.md#properties)与连接到未启用群集功能的缓存时使用的相同。 唯一的区别是，所有读取和写入都必须在数据库 0 中进行。
  
  * 其他客户端可能有不同的要求。 请参阅[是否所有 Redis 客户端都支持群集功能？](#do-all-redis-clients-support-clustering)
* 如果应用程序使用的多个密钥操作都在单个命令中成批执行，则所有密钥都必须位于同一分片。 若要查找同一分片中的密钥，请参阅[密钥在群集中是如何分布的？](#how-are-keys-distributed-in-a-cluster)
* 如果使用的是 Redis ASP.NET 会话状态提供程序，则必须使用 2.0.1 或更高版本。 请参阅[能否在 Redis ASP.NET 会话状态和输出缓存提供程序中使用群集功能？](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)

### <a name="how-are-keys-distributed-in-a-cluster"></a>密钥在群集中是如何分布的？
请参阅 Redis [密钥分布模型](https://redis.io/topics/cluster-spec#keys-distribution-model)文档：密钥空间拆分成 16384 个槽。 每个密钥都经过哈希处理并分配到其中一个槽，这些槽分布在群集的节点中。 对密钥的哪部分进行哈希处理是可以配置的，这样可确保多个使用哈希标记的密钥位于同一分片。

* 使用哈希标记的密钥 - 如果将密钥的任意部分括在 `{` 和 `}` 中，则只会对密钥的该部分进行哈希处理，以便确定密钥的哈希槽。 例如，以下 3 个密钥将位于同一分片中：`{key}1`、`{key}2` 和 `{key}3`，因为只对名称的 `key` 部分进行了哈希处理。 如需密钥哈希标记规范的完整列表，请参阅[Keys hash tags](https://redis.io/topics/cluster-spec#keys-hash-tags)（密钥哈希标记）。
* 没有哈希标记的密钥 - 使用整个密钥名称进行哈希处理。 从统计学意义上来说，这样会导致密钥平均分布到缓存的各个分片中。

为了优化性能和吞吐量，建议将密钥平均分布。 如果使用带哈希标记的密钥，则应用程序会负责确保密钥平均分布。

有关详细信息，请参阅[Keys distribution mode](https://redis.io/topics/cluster-spec#keys-distribution-model)（密钥分布模型）、[Redis Cluster data sharding](https://redis.io/topics/cluster-tutorial#redis-cluster-data-sharding)（Redis 群集数据分片）和 [Keys hash tags](https://redis.io/topics/cluster-spec#keys-hash-tags)（密钥哈希标记）。

有关在 StackExchange.Redis 客户端中使用群集和查找同一分片中的密钥的示例代码，请参阅 [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) 示例的 [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) 部分。

### <a name="what-is-the-largest-cache-size-i-can-create"></a>我可以创建的最大缓存大小是多大？
最大的高级缓存大小为 120 GB。 最多可以创建10个分片，最大大小为 1.2 TB GB。 如果需要的大小更大，则可[请求更多](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase)。 有关详细信息，请参阅 [Azure Redis 缓存定价](https://azure.microsoft.com/pricing/details/cache/)。

### <a name="do-all-redis-clients-support-clustering"></a>是否所有 Redis 客户端都支持群集功能？
目前，并非所有客户端都支持 Redis 群集功能。 StackExchange.Redis 是不支持该功能的客户端。 有关其他客户端的详细信息，请参阅 [Redis cluster tutorial](https://redis.io/topics/cluster-tutorial)（Redis 群集教程）的 [Playing with the cluster](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster)（操作群集）部分。 

Redis 群集协议要求每个客户端直接以群集模式连接到每个分片。 尝试使用不支持群集的客户端可能会导致大量 [MOVED 重定向异常](https://redis.io/topics/cluster-spec#moved-redirection)。

> [!NOTE]
> 如果使用 StackExchange.Redis 作为客户端，请确保使用最新版本的 [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/)，即 1.0.481 或更高版本，以便群集功能能够正常使用。 如果对 move 异常有任何疑问，请参阅 [move 异常](#move-exceptions)了解详细信息。
> 
> 

### <a name="how-do-i-connect-to-my-cache-when-clustering-is-enabled"></a>启用群集功能后，如何连接到缓存？
连接到缓存时，可以使用的[终结点](cache-configure.md#properties)、[端口](cache-configure.md#properties)和[密钥](cache-configure.md#access-keys)与连接到未启用群集功能的缓存时使用的相同。 Redis 在后端管理群集功能，因此不需要你通过客户端来管理它。

### <a name="can-i-directly-connect-to-the-individual-shards-of-my-cache"></a>我可以直接连接到缓存的各个分片吗？
群集协议要求客户端进行正确的分片连接。 因此客户端应该为你正确地执行此操作。 话虽如此，但每个分片都是由主/副缓存对组成的，该缓存对统称为缓存实例。 可以在 GitHub 上通过 Redis 存储库的[不稳定](https://redis.io/download)分支使用 redis-cli 实用程序连接到这些缓存实例。 使用 `-c` 开关启动后，此版本可实现基本的支持。 有关详细信息，请参阅 [https://redis.io](https://redis.io) 上 [Redis cluster tutorial](https://redis.io/topics/cluster-tutorial)（Redis 群集教程）中的[操作群集](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster)。

对于非 ssl，请使用以下命令。

    Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
    Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
    Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
    ...
    Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)

对于 ssl，请将 `1300N` 替换为 `1500N`。

### <a name="can-i-configure-clustering-for-a-previously-created-cache"></a>我可以为以前创建的缓存配置群集功能吗？
目前，只能在创建缓存时启用群集。 创建缓存后，可以更改群集大小，但不能将群集添加到高级缓存，或者从高级缓存中删除群集。 已启用群集且只包含一个分片的高级缓存不同于具有相同大小且没有群集的高级缓存。

### <a name="can-i-configure-clustering-for-a-basic-or-standard-cache"></a>我可以为基本缓存或标准缓存配置群集功能吗？
群集功能仅适用于高级缓存。

### <a name="can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers"></a>能否在 Redis ASP.NET 会话状态和输出缓存提供程序中使用群集功能？
* **Redis 输出缓存提供程序** - 无需进行更改。
* **Redis 会话状态提供程序** - 若要使用群集功能，必须使用 [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 或更高版本，否则会引发异常。 这是一项重大更改；有关详细信息，请参阅 [2.0.0 版重大更改详细信息](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details)。

<a name="move-exceptions"></a>

### <a name="i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do"></a>我在使用 StackExchange.Redis 和群集功能时出现 MOVE 异常，应该怎么办？
如果使用的是 StackExchange.Redis 并在使用群集功能时收到 `MOVE` 异常，请确保使用的是 [StackExchange.Redis 1.1.603](https://www.nuget.org/packages/StackExchange.Redis/) 或更高版本。 有关如何配置 .NET 应用程序以使用 StackExchange.Redis 的说明，请参阅[配置缓存客户端](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients)。

## <a name="next-steps"></a>后续步骤
了解如何使用更多的高级版缓存功能。

* [Azure Redis 缓存高级层简介](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

[redis-cache-redis-cluster-size]: ./media/cache-how-to-premium-clustering/redis-cache-redis-cluster-size.png







