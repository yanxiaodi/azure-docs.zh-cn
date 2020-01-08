---
title: 如何将 redis-cli 与 Azure Redis 缓存配合使用 | Microsoft Docs
description: 了解如何将 redis-cli 与 Azure Redis 缓存配合使用。
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: yegu
ms.openlocfilehash: 318d02f5da816ae8fe2fe199b9c87b3748d5d1fc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66133010"
---
# <a name="how-to-use-the-redis-command-line-tool-with-azure-cache-for-redis"></a>如何将 Redis 命令行工具与 Azure Redis 缓存配合使用

redis-cli.exe 是一种常用的命令行工具，可作为客户端与 Azure Redis 缓存进行交互  。 此工具也可与 Azure Redis 缓存一起使用。

下载[用于 Windows 的 Redis 命令行工具](https://github.com/MSOpenTech/redis/releases/) 后，即可在 Windows 平台上使用此工具。 

如需在其他平台上运行该命令行工具，请从 [https://redis.io/download](https://redis.io/download) 下载 Azure Redis 缓存。

## <a name="gather-cache-access-information"></a>收集缓存访问信息

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

可通过三种方式收集访问缓存所需的信息：

1. 在 Azure CLI 中使用 [az redis list-keys](https://docs.microsoft.com/cli/azure/redis?view=azure-cli-latest#az-redis-list-keys)
2. 在 Azure PowerShell 中使用 [Get-AzRedisCacheKey](https://docs.microsoft.com/powershell/module/az.rediscache/Get-AzRedisCacheKey)
3. 使用 Azure 门户。

本部分介绍如何从 Azure 门户检索密钥。

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>为 redis-cli.exe 启用访问权限

使用 Azure Redis 缓存时，默认情况下仅启用 SSL 端口 (6380)。 `redis-cli.exe` 命令行工具不支持 SSL。 可通过两种配置方式使用该命令行工具：

1. [启用非 SSL 端口 (6379)](cache-configure.md#access-ports) - 不建议采用此配置，因为在此配置中，系统通过 TCP 以明文形式发送访问密钥  。 这种更改可能会影响对缓存的访问。 仅当访问测试缓存时才考虑选择此配置。

2. 下载并安装 [stunnel](https://www.stunnel.org/downloads.html)。

    运行 stunnel GUI Start 以启动服务器  。

    右键单击 stunnel 服务器的任务栏图标，然后单击“显示日志窗口”  。

    在 stunnel“日志窗口”菜单上，单击“配置” > “编辑配置”，打开当前配置文件   。

    在“服务定义”部分下向 redis-cli.exe 添加以下项   。 将 `yourcachename` 替换为实际缓存名称。 

    ```
    [redis-cli]
    client = yes
    accept = 127.0.0.1:6380
    connect = yourcachename.redis.cache.windows.net:6380
    ```

    保存并关闭配置文件。 
  
    在 stunnel“日志窗口”菜单中，单击“配置” > “重新加载配置”   。


## <a name="connect-using-the-redis-command-line-tool"></a>使用 Redis 命令行工具进行连接。

使用 stunnel 时，运行 redis-cli.exe，并仅传递端口和访问密钥（主要或次要）以连接到缓存    。

```
redis-cli.exe -p 6380 -a YourAccessKey
```

![在 stunnel 中运行 redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-stunnel.png)

如果将测试缓存与不安全的非 SSL 端口一起使用，请运行 `redis-cli.exe` 并传递主机名、端口和访问密钥（主要或次要），以连接到测试缓存     。

```
redis-cli.exe -h yourcachename.redis.cache.windows.net -p 6379 -a YourAccessKey
```

![在 stunnel 中运行 redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-non-ssl.png)




## <a name="next-steps"></a>后续步骤

了解使用 [Redis 控制台](cache-configure.md#redis-console)发出命令的详细信息。

