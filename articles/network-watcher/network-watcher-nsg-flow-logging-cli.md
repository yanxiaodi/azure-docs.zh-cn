---
title: 使用 Azure 网络观察程序管理网络安全组流日志 - Azure CLI | Microsoft Docs
description: 此页说明如何在 Azure 网络观察程序中使用 Azure CLI 管理网络安全组流日志
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 2dfc3112-8294-4357-b2f8-f81840da67d3
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 5e7c09c1a06a94a2ed64f3624ee38dc42606d7bc
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563484"
---
# <a name="configuring-network-security-group-flow-logs-with-azure-cli"></a>使用 Azure CLI 配置网络安全组流日志

> [!div class="op_single_selector"]
> - [Azure 门户](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

网络安全组流日志是网络观察程序的一项功能，用于查看通过网络安全组的入口和出口 IP 流量的信息。 这些流日志以 json 格式编写，并根据规则显示出站和入站流、流所适用的 NIC、有关流的 5 元组信息（源/目标 IP、源/目标端口、协议），以及是允许还是拒绝流量。

若要执行本文中的步骤，需要[安装适用于 Mac、Linux 和 Windows 的 Azure 命令行接口 (CLI)](/cli/azure/install-azure-cli)。

## <a name="register-insights-provider"></a>注册 Insights 提供程序

要使流日志记录正常工作，必须注册 **Microsoft.Insights** 提供程序。 如果不确定 **Microsoft.Insights** 提供程序是否已注册，请运行以下脚本。

```azurecli
az provider register --namespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs"></a>启用网络安全组流日志

以下示例显示了用于启用流日志的命令：

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName
# Configure 
az network watcher flow-log configure --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName  --format JSON --log-version 2
```

你指定的存储帐户不能配置有仅限 Microsoft 服务或特定虚拟网络进行网络访问的网络规则。 存储帐户可以与启用流日志的 NSG 使用相同或不同的 Azure 订阅。 如果使用不同的订阅，它们必须都与同一 Azure Active Directory 租户相关联。 用于每个订阅的帐户必须有[必要的权限](required-rbac-permissions.md)。 

如果存储帐户与网络安全组位于不同的资源组或订阅中，请指定存储帐户的完整 ID，而非其名称。 例如，如果存储帐户位于名为 RG-Storage 的资源组中，则请指定 /subscriptions/ {SubscriptionID}/resourceGroups/RG-Storage/providers/Microsoft.Storage/storageAccounts/storageAccountName，而不是在上一命令中指定 storageAccountName。

## <a name="disable-network-security-group-flow-logs"></a>禁用网络安全组流日志

使用以下示例禁用流日志：

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled false --nsg nsgName
```

## <a name="download-a-flow-log"></a>下载流日志

流日志的存储位置是在创建时定义的。 用于访问这些保存到存储帐户的流日志的便利工具是 Microsoft Azure 存储资源管理器，下载地址为： https://storageexplorer.com/

如果指定了存储帐户，则会将流日志文件保存到以下位置的存储帐户：

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

> [!IMPORTANT]
> 目前存在一个问题: 网络观察程序的[网络安全组 (NSG) 流日志](network-watcher-nsg-flow-logging-overview.md)不会根据保留策略设置从 Blob 存储中自动删除。 如果你有现有的非零保留策略, 我们建议你定期删除超出保留期的存储 blob, 以避免产生任何费用。 有关如何删除 NSG 流日志存储的详细信息, 请参阅[删除 NSG 流日志存储 blob](network-watcher-delete-nsg-flow-log-blobs.md)。

## <a name="next-steps"></a>后续步骤

了解如何[使用 PowerBI 直观地显示 NSG 流日志](network-watcher-visualize-nsg-flow-logs-power-bi.md)

了解如何[使用开源工具直观地显示 NSG 流日志](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
