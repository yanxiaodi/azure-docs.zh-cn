---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 77019068279261a36f8ba40247e5f5694ca77892
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "67173354"
---
| Resource | 限制 |
| --- | --- |
| 缓存大小 |530 GB |
| 数据库 |64 |
| 连接的最大客户端数 |40,000 |
| Azure Redis 缓存副本，用于高可用性 |1 |
| 启用群集的高级缓存中的分片数 |10 |

每个定价层的 Azure Redis 缓存限制和大小都不相同。 若要查看定价层及其关联的大小，请参阅 [Azure Redis 缓存定价](https://azure.microsoft.com/pricing/details/cache/)。

有关 Azure Redis 缓存配置限制的详细信息，请参阅[默认 Redis 服务器配置](../articles/azure-cache-for-redis/cache-configure.md#default-redis-server-configuration)。

由于 Azure Redis 缓存实例的配置和管理是由 Microsoft 执行的，因此 Azure Redis 缓存并非支持所有 Redis 命令。 有关详细信息，请参阅 [Azure Redis 缓存不支持的 Redis 命令](../articles/azure-cache-for-redis/cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis)。

