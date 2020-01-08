---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 61082333afc88abef0a8d8a57d1f1b1d893b6148
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172808"
---
### <a name="to-view-local-network-gateways"></a>查看本地网关

若要查看本地网关的列表，请使用 [az network local-gateway list](https://docs.microsoft.com/cli/azure/network/local-gateway) 命令。

```azurecli
az network local-gateway list --resource-group TestRG1
```

[!INCLUDE [modify-prefix](vpn-gateway-modify-ip-prefix-cli-include.md)]

[!INCLUDE [modify-gateway-IP](vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

### <a name="to-verify-the-shared-key-values"></a>验证共享密钥值

验证共享密钥值与用于 VPN 设备配置的值是否相同。 如果不同，请使用设备提供的值再次运行链接，或者使用返回的值更新设备。 值必须匹配。 若要查看共享的密钥，请使用 [az network vpn-connection-list](https://docs.microsoft.com/cli/azure/network/vpn-connection)。

```azurecli
az network vpn-connection shared-key show --connection-name VNet1toSite2 --resource-group TestRG1
```
### <a name="to-view-the-vpn-gateway-public-ip-address"></a>查看 VPN 网关的公共 IP 地址

若要查找虚拟网关的公共 IP 地址，请使用 [az network public-ip list](https://docs.microsoft.com/cli/azure/network/public-ip) 命令。 为了方便阅读，对本示例的输出进行了格式化，以表格式显示一系列公共 IP。

```azurecli
az network public-ip list --resource-group TestRG1 --output table
```
