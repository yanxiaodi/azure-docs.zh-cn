---
author: yegu-ms
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: yegu
ms.openlocfilehash: 61e93e3700b9a396d2ac4fdcbb51fc5c874cf9cb
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2019
ms.locfileid: "68286310"
---
.NET 应用程序可以使用 **StackExchange.Redis** 缓存客户端，可使用 NuGet 包在 Visual Studio 中进行配置，以简化缓存客户端应用程序的配置。 

> [!NOTE]
> 有关详细信息，请参阅 [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) GitHub 页和 [StackExchange.Azure Redis 缓存客户端文档](https://github.com/StackExchange/StackExchange.Redis#documentation)。
>
>

要使用 StackExchange.Redis NuGet 包配置客户端应用程序，请在“解决方案资源管理器”中右键单击项目，并选择“管理 NuGet 包”。   

![管理 NuGet 包](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-manage-nuget-menu.png)

在“搜索”文本框中键入 **StackExchange.Redis** 或 **StackExchange.Redis.StrongName**，从结果选择所需版本，并单击“安装”。 

> [!NOTE]
> 如果希望使用 **StackExchange.Redis** 客户端库强名称版本，请选择 **StackExchange.Redis.StrongName**；否则选择 **StackExchange.Redis**。
>
>

![StackExchange.Redis NuGet 程序包](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-stackexchange-redis.png)

NuGet 程序包会为客户端应用程序下载并添加所需的程序集引用，以通过 StackExchange.Azure Redis 缓存客户端访问 Azure Redis 缓存。

> [!NOTE]
> 如果以前已将项目配置为使用 StackExchange.Redis，则可以通过 **NuGet 包管理器**检查该包是否有更新。 若要检查并安装 StackExchange.Redis NuGet 包的更新版本，请在“NuGet 包管理器”  窗口中单击“更新”  。 如果 StackExchange.Redis NuGet 包有可用更新，则可以更新项目以使用更新后的版本。
>
>

也可安装 StackExchange.Redis NuGet 包，方法是：依次单击“工具”菜单中的“NuGet 包管理器”  、“包管理器控制台”   ，并在“包管理器控制台”窗口中运行以下命令。 

```
Install-Package StackExchange.Redis
```
