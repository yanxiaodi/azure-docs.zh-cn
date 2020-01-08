---
title: Azure Service Fabric 中支持的群集版本 |Microsoft Docs
description: 了解 Azure Service Fabric 中的群集版本。
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/21/2019
ms.author: atsenthi
ms.openlocfilehash: ca7a3cfe62520ed82dcf494a33f3bd188dbebe76
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279981"
---
# <a name="supported-service-fabric-versions"></a>支持的 Service Fabric 版本

请确保群集始终运行受支持的 Azure Service Fabric 版本。 在公告新版 Service Fabric 的发布日期以后，至少还要过 60 天我们才会结束对旧版本的支持。 可以在 [Service Fabric 团队博客](https://azure.microsoft.com/updates/?product=service-fabric)中找到有关新版本的公告。

若要详细了解如何使群集始终运行受支持的 Service Fabric 版本，请参阅以下文档：

- [升级 Azure Service Fabric 群集](service-fabric-cluster-upgrade.md)
- [升级在单独的 Windows Server 群集上运行的 Service Fabric 版本](service-fabric-cluster-upgrade-windows-server.md)

## <a name="supported-versions"></a>支持的版本

下表列出了 Service Fabric 版本及其支持结束日期。

| 群集中的 Service Fabric 运行时 | 可以直接从群集版本升级 |兼容的 SDK 或 NuGet 包版本 | 结束支持 |
| --- | --- |--- | --- |
| 5\.3.121 之前的所有群集版本 | 5.1.158.* |低于或等于版本 2.3 |2017 年 1 月 20 日 |
| 5.3.* | 5.1.158.* |低于或等于版本 2.3 |2017 年 2 月 24 日 |
| 5.4.* | 5.1.158.* |低于或等于版本 2.4 |2017 年 5 月 10 日       |
| 5.5.* | 5.4.164.* |低于或等于版本 2.5 |2017 年 8 月 10 日    |
| 5.6.* | 5.4.164.* |低于或等于版本 2.6 |2017 年 10 月 13 日   |
| 5.7.* | 5.4.164.* |低于或等于版本 2.7 |2017 年 12 月 15 日  |
| 6.0.* | 5.6.205.* |版本 2.8 或更低版本 |2018 年 3 月 30 日     |
| 6.1.* | 5.7.221.* |版本 3.0 或更低版本 |2018 年 7 月 15日      |
| 6.2.* | 6.0.232.* |版本 3.1 或更低版本 |2018 年 10 月 26日   |
| 6.3.* | 6.1.480.* |版本 3.2 或更低版本 |2019 年 3 月 31 日  |
| 6.4.* | 6.2.301.* |版本 3.3 或更低版本 |2019 年 9 月 15 日 |
| 6.5.* | 6.4.617.* |低于或等于版本 3.4 |当前版本，因此无结束日期 |

## <a name="supported-operating-systems"></a>支持的操作系统

下表列出了支持的 Service Fabric 版本所支持的操作系统。

| 操作系统 | 支持的最低 Service Fabric 版本 |
| --- | --- |
| Windows Server 2012 R2 | 所有版本 |
| Windows Server 2016 | 所有版本 |
| Windows Server 1709 | 6.0 |
| Windows Server 1803 | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16.04 | 6.0 |

## <a name="supported-version-names"></a>支持的版本名称

下表列出了 Service Fabric 的版本名称以及相应的版本号。

| 版本名称 | Windows 版本号 | Linux 版本号 |
| --- | --- | --- |
| 5.3 RTO | 5.3.121.9494 | 不可用 |
| 5.3 CU1 | 5.3.204.9494 | 不可用 |
| 5.3 CU2 | 5.3.301.9590 | 不可用 |
| 5.3 CU3 | 5.3.311.9590 | 不可用 |
| 5.4 CU2 | 5.4.164.9494 | 不可用 |
| 5.5 CU1 | 5.5.216.0    | 不可用 |
| 5.5 CU2 | 5.5.219.0    | 不可用 |
| 5.5 CU3 | 5.5.227.0    | 不可用 |
| 5.5 CU4 | 5.5.232.0    | 不可用 |
| 5.6 RTO | 5.6.204.9494 | 不可用 |
| 5.6 CU2 | 5.6.210.9494 | 不可用 |
| 5.6 CU3 | 5.6.220.9494 | 不可用 |
| 5.7 RTO | 5.7.198.9494 | 不可用 |
| 5.7 CU4 | 5.7.221.9494 | 不可用 |
| 6.0 RTO | 6.0.211.9494 | 6.0.120.1 |
| 6.0 CU1 | 6.0.219.9494 | 6.0.127.1 |
| 6.0 CU2 | 6.0.232.9494 | 6.0.133.1 |
| 6.1 CU1 | 6.1.456.9494 | 6.1.183.1 |
| 6.1 CU2 | 6.1.467.9494 | 6.1.185.1 |
| 6.1 CU3 | 6.1.472.9494 | 不可用 |
| 6.1 CU4 | 6.1.480.9494 | 6.1.187.1 |
| 6.2 RTO | 6.2.269.9494 | 6.2.184.1 | 
| 6.2 CU1 | 6.2.274.9494 | 6.2.191.1 |
| 6.2 CU2 | 6.2.283.9494 | 6.2.194.1 |
| 6.2 CU3 | 6.2.301.9494 | 6.2.199.1 |
| 6.3 RTO | 6.3.162.9494 | 6.3.119.1 |
| 6.3 CU1 | 6.3.176.9494 | 6.3.124.1 |
| 6.3 CU1 | 6.3.187.9494 | 6.3.129.1 |
| 6.4 RTO | 6.4.617.9590 | 6.4.625.1 |
| 6.4 CU2 | 6.4.622.9590 | 不可用 |
| 6.4 CU3 | 6.4.637.9590 | 6.4.634.1 |
| 6.4 CU4 | 6.4.644.9590 | 6.4.639.1 |
| 6.4 CU5 | 6.4.654.9590 | 6.4.649.1 |
| 6.4 CU6 | 6.4.658.9590 | 不可用 |
| 6.4 CU7 | 6.4.664.9590 | 6.4.661.1 |
| 6.4 CU8 | 6.4.670.9590 | 不可用 |
| 6.5 RTO | 6.5.639.9590 | 6.5.435.1 |
| 6.5 CU1 | 6.5.641.9590 | 6.5.454.1 |
| 6.5 CU2 | 6.5.658.9590 | 6.5.460.1 |
| 6.5 CU3 | 6.5.664.9590 | 6.5.466.1 |
