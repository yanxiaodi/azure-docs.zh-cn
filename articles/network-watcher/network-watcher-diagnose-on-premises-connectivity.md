---
title: 使用 Azure 网络观察程序通过 VPN 网关诊断本地连接 | Microsoft 文档
description: 本文介绍如何使用 Azure 网络观察程序资源故障排除功能通过 VPN 网关诊断本地连接。
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: aeffbf3d-fd19-4d61-831d-a7114f7534f9
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 05335cb6949928244e10641ebe82008275830e67
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66754063"
---
# <a name="diagnose-on-premises-connectivity-via-vpn-gateways"></a>通过 VPN 网关诊断本地连接

使用 Azure VPN 网关可以创建混合解决方案，解决在本地网络与 Azure 虚拟网络之间建立安全连接的需求。 每个人的要求都是独一无二的，选择的本地 VPN 设备也是如此。 Azure 目前支持[多种 VPN 设备](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable)，我们正在持续与设备供应商合作验证这些设备。 在配置本地 VPN 设备之前，请查看特定于设备的配置设置。 同样，Azure VPN 网关中配置了一组[受支持的 IPsec 参数](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec)用于建立连接。 目前无法在 Azure VPN 网关中指定或选择 IPsec 参数的特定组合。 若要在本地与 Azure 之间成功建立连接，本地 VPN 设备设置必须符合 Azure VPN 网关规定的 IPsec 参数。 如果设置正确，则会导致连接断开，而到目前为止，排查这些问题并非小事一桩，通常需要花费几个小时来识别和修复问题。

使用 Azure 网络观察程序故障排除功能，可以诊断任何网关和连接问题，在几分钟内获得足够的信息，就如何解决问题做出明智的决策。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario"></a>场景

想要使用 FortiGate 作为本地 VPN 网关，在 Azure 与本地之间配置站点到站点连接。 若要实现此方案，需要进行以下设置：

1. 虚拟网络网关 - Azure 上的 VPN 网关
1. 本地网络网关 - Azure 云中的[本地 (FortiGate) VPN 网关](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway)表示形式
1. 站点到站点连接（基于路由）- [VPN 网关与本地路由器之间的连接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#CreateConnection)
1. [配置 FortiGate](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/Site-to-Site_VPN_using_FortiGate.md)

有关配置站点到站点配置的详细分步指南，请访问：[使用 Azure 门户创建具有站点到站点连接的 VNet](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)。

一个关键的配置步骤是配置 IPsec 通信参数，任何不当的配置都会导致本地网络与 Azure 之间的连接断开。 目前，Azure VPN 网关配置为支持第 1 阶段的以下 IPsec 参数。 请注意，如前所述，这些设置不可修改。  如下表中所示，Azure VPN 网关支持的加密算法包括 AES256、AES128、和 3DES。

### <a name="ike-phase-1-setup"></a>IKE 第 1 阶段设置

| **属性** | **PolicyBased** | **RouteBased 和标准或高性能 VPN 网关** |
| --- | --- | --- |
| SDK 版本 |IKEv1 |IKEv2 |
| Diffie-Hellman 组 |组 2（1024 位） |组 2（1024 位） |
| 身份验证方法 |预共享密钥 |预共享密钥 |
| 加密算法 |AES256 AES128 3DES |AES256 3DES |
| 哈希算法 |SHA1(SHA128) |SHA1(SHA128)、SHA2(SHA256) |
| 阶段 1 安全关联 (SA) 生命周期（时间） |28,800 秒 |10,800 秒 |

用户需要配置 FortiGate，在 [GitHub](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/fortigate_show%20full-configuration.txt) 上可以找到示例配置。 无意中将 FortiGate 配置为使用 SHA-512 作为哈希算法。 由于基于策略的连接不支持此算法，因此 VPN 连接无法正常工作。

这些问题很难排查，其根本原因通常并不明显。 在这种情况下，可以开具支持票证，请求帮助解决此问题。 但如果使用 Azure 网络观察程序故障排除 API，则可以自行识别这些问题。

## <a name="troubleshooting-using-azure-network-watcher"></a>使用 Azure 网络观察程序进行故障排除

若要诊断连接，请连接到 Azure PowerShell 并启动 `Start-AzNetworkWatcherResourceTroubleshooting` cmdlet。 可以在 [Troubleshoot Virtual Network Gateway and connections - PowerShell](network-watcher-troubleshoot-manage-powershell.md)（排查虚拟网络网关和连接问题 - PowerShell）中找到有关使用此 cmdlet 的详细信息。 此 cmdlet 最长可能需要几分钟时间才能完成。

完成该 cmdlet 后，可以导航到该 cmdlet 中指定的存储位置，获取有关问题和日志的详细信息。 Azure 网络观察程序创建包含以下日志文件的 zip 文件夹：

![第][1]

打开名为 IKEErrors.txt 的文件，其中显示了以下错误，指出存在本地 IKE 设置配置不当的问题。

```
Error: On-premises device rejected Quick Mode settings. Check values.
     based on log : Peer sent NO_PROPOSAL_CHOSEN notify
```

可以通过 Scrubbed-wfpdiag.txt 获取有关错误的详细信息，在本例中，该文件指出 `ERROR_IPSEC_IKE_POLICY_MATCH` 导致连接无法正常工作。

另一种常见的不当配置是指定了错误的共享密钥。 如果在前面的示例中指定不同的共享密钥，IKEErrors.txt 会显示以下错误：`Error: Authentication failed. Check shared key`。

借助 Azure 网络观察程序故障排除功能，可以使用一个简单易用的 PowerShell cmdlet 来诊断和排查 VPN 网关与连接问题。 目前我们支持诊断以下状态，并且正在努力添加更多状态的诊断。

### <a name="gateway"></a>网关

| 错误类型 | 原因 | 日志|
|---|---|---|
| NoFault | 未检测到任何错误。 |是|
| GatewayNotFound | 找不到网关，或网关未预配。 |否|
| PlannedMaintenance |  网关实例处于维护状态。  |否|
| UserDrivenUpdate | 用户更新正在进行。 可能是正在执行大小调整操作。 | 否 |
| VipUnResponsive | 无法访问网关的主实例。 运行状况探测失败时会发生这种情况。 | 否 |
| PlatformInActive | 平台出现问题。 | 否|
| ServiceNotRunning | 底层服务未运行。 | 否|
| NoConnectionsFoundForGateway | 网关未建立连接。 这只是一条警告。| 否|
| ConnectionsNotConnected | 未连接任何连接设备。 这只是一条警告。| 是|
| GatewayCPUUsageExceeded | 当前网关 CPU 使用率超过 95%。 | 是 |

### <a name="connection"></a>连接

| 错误类型 | 原因 | 日志|
|---|---|---|
| NoFault | 未检测到任何错误。 |是|
| GatewayNotFound | 找不到网关，或网关未预配。 |否|
| PlannedMaintenance | 网关实例处于维护状态。  |否|
| UserDrivenUpdate | 用户更新正在进行。 可能是正在执行大小调整操作。  | 否 |
| VipUnResponsive | 无法访问网关的主实例。 运行状况探测失败时会发生这种情况。 | 否 |
| ConnectionEntityNotFound | 缺少连接配置。 | 否 |
| ConnectionIsMarkedDisconnected | 连接标记为“断开连接”。 |否|
| ConnectionNotConfiguredOnGateway | 未在底层服务上配置连接。 | 是 |
| ConnectionMarkedStandby | 底层服务标记为备用。| 是|
| Authentication | 预共享密钥不匹配。 | 是|
| PeerReachability | 无法访问对等网关。 | 是|
| IkePolicyMismatch | 对等网关中的 IKE 策略不受 Azure 支持。 | 是|
| WfpParse Error | 分析 WFP 日志时出错。 |是|

## <a name="next-steps"></a>后续步骤

访问 [Monitor VPN gateways with Azure Network Watcher troubleshooting](network-watcher-monitor-with-azure-automation.md)（使用 Azure 网络观察程序故障排除监视 VPN 网关），了解如何使用 PowerShell 和 Azure 自动化检查 VPN 网关连接

[1]: ./media/network-watcher-diagnose-on-premises-connectivity/figure1.png
