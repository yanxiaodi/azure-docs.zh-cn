---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/14/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 36b3fcfa90b5b1de9c9d3262da1f3e519cc99c19
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172773"
---
### <a name="is-custom-ipsecike-policy-supported-on-all-azure-vpn-gateway-skus"></a>是否所有 Azure VPN 网关 SKU 都支持自定义 IPsec/IKE 策略？
自定义 IPsec/IKE 策略在 Azure VpnGw1、VpnGw2、VpnGw3、标准  VPN 网关和高性能  VPN 网关上受支持。 不支持基本 SKU。  

### <a name="how-many-policies-can-i-specify-on-a-connection"></a>在一个连接上可以指定多少个策略？
一个给定的连接只能指定一个策略组合。

### <a name="can-i-specify-a-partial-policy-on-a-connection-for-example-only-ike-algorithms-but-not-ipsec"></a>能否在一个连接上指定部分策略？ （例如，仅指定 IKE 算法，不指定 IPsec）
否，必须指定 IKE（主模式）和 IPsec（快速模式）的所有算法和参数。 不允许指定部分策略。

### <a name="what-are-the-algorithms-and-key-strengths-supported-in-the-custom-policy"></a>自定义策略中支持的算法和密钥强度有哪些？
下表列出了支持的加密算法和密钥强度，客户可自行配置。 必须为每个字段选择一个选项。

|  IPsec/IKEv2  |  选项                                                                   |
| ---              | ---                                                                           |
| IKEv2 加密 | AES256、AES192、AES128、DES3、DES                                             |
| IKEv2 完整性  | SHA384、SHA256、SHA1、MD5                                                     |
| DH 组         | DHGroup24、ECP384、ECP256、DHGroup14 (DHGroup2048)、DHGroup2、DHGroup1、无 |
| IPsec 加密 | GCMAES256、GCMAES192、GCMAES128、AES256、AES192、AES128、DES3、DES、无      |
| IPsec 完整性  | GCMAES256、GCMAES192、GCMAES128、SHA256、SHA1、MD5                            |
| PFS 组        | PFS24、ECP384、ECP256、PFS2048、PFS2、PFS1、无                              |
| QM SA 生存期   | 秒（整数；  至少为 300 秒/默认为 27000 秒）<br>KB（整数；  至少为 1024 KB/默认为 102400000 KB）           |
| 流量选择器 | UsePolicyBasedTrafficSelectors ($True/$False; default $False)                 |
|                  |                                                                               |

> [!IMPORTANT]
> 1. 在 IKE 和 IPsec PFS 中，DHGroup2048 和 PFS2048 与 Diffie-Hellman 组  14 相同。 如需完整的映射，请参阅 [Diffie-Hellman 组](#DH)。
> 2. 对于 GCMAES 算法，必须为 IPsec 加密和完整性指定相同的 GCMAES 算法和密钥长度。
> 3. 在 Azure VPN 网关上，IKEv2 主模式 SA 生存期固定为 28,800 秒
> 4. QM SA 生存期是可选参数。 如果未指定，则使用默认值 27,000 秒（7.5 小时）和 102400000 KB (102GB)。
> 5. UsePolicyBasedTrafficSelector 是连接的可选参数。 请参阅下一针对“UsePolicyBasedTrafficSelectors”的常见问题解答项

### <a name="does-everything-need-to-match-between-the-azure-vpn-gateway-policy-and-my-on-premises-vpn-device-configurations"></a>Azure VPN 网关策略与本地 VPN 设备配置是否需完全匹配？
本地 VPN 设备配置必须匹配，或者必须包含可在 Azure IPsec/IKE 策略中指定的以下算法和参数：

* IKE 加密算法
* IKE 完整性算法
* DH 组
* IPsec 加密算法
* IPsec 完整性算法
* PFS 组
* 流量选择器 (*)

SA 生存期是本地规范，不需匹配。

如果启用  UsePolicyBasedTrafficSelectors，则需确保对于本地网络（本地网关）前缀与 Azure 虚拟网络前缀的所有组合，VPN 设备都定义了与之匹配的流量选择器（而不是任意到任意）。 例如，如果本地网络前缀为 10.1.0.0/16 和 10.2.0.0/16，虚拟网络前缀为 192.168.0.0/16 和 172.16.0.0/16，则需指定以下流量选择器：
* 10.1.0.0/16 <====> 192.168.0.0/16
* 10.1.0.0/16 <====> 172.16.0.0/16
* 10.2.0.0/16 <====> 192.168.0.0/16
* 10.2.0.0/16 <====> 172.16.0.0/16

有关详细信息，请参阅[连接多个基于策略的本地 VPN 设备](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md)。

### <a name ="DH"></a>支持哪些 Diffie-Hellman 组？
下表列出了支持的 Diffie-Hellman 组，分别针对 IKE (DHGroup) 和 IPsec (PFSGroup)：

| **Diffie-Hellman 组**  | **DHGroup**              | **PFSGroup** | 密钥长度  |
| ---                       | ---                      | ---          | ---            |
| 第                         | DHGroup1                 | PFS1         | 768 位 MODP   |
| 2                         | DHGroup2                 | PFS2         | 1024 位 MODP  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | 2048 位 MODP  |
| 19                        | ECP256                   | ECP256       | 256 位 ECP    |
| 20                        | ECP384                   | ECP384       | 384 位 ECP    |
| 24                        | DHGroup24                | PFS24        | 2048 位 MODP  |
|                           |                          |              |                |

有关详细信息，请参阅 [RFC3526](https://tools.ietf.org/html/rfc3526) 和 [RFC5114](https://tools.ietf.org/html/rfc5114)。

### <a name="does-the-custom-policy-replace-the-default-ipsecike-policy-sets-for-azure-vpn-gateways"></a>自定义策略是否会替换 Azure VPN 网关的默认 IPsec/IKE 策略集？
是的。一旦在连接上指定自定义策略，Azure VPN 网关就会只使用该连接的策略，既充当 IKE 发起方，又充当 IKE 响应方。

### <a name="if-i-remove-a-custom-ipsecike-policy-does-the-connection-become-unprotected"></a>如果删除自定义 IPsec/IKE 策略，连接是否会变得不受保护？
否。连接仍受 IPsec/IKE 保护。 从连接中删除自定义策略以后，Azure VPN 网关会还原为[默认的 IPsec/IKE 提议列表](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md)，并再次重启与本地 VPN 设备的 IKE 握手。

### <a name="would-adding-or-updating-an-ipsecike-policy-disrupt-my-vpn-connection"></a>添加或更新 IPsec/IKE 策略是否会中断 VPN 连接？
是的。那样会导致短时中断（数秒），因为 Azure VPN 网关会断开现有连接并重启 IKE 握手，以便使用新的加密算法和参数重建 IPsec 隧道。 请确保也使用匹配的算法和密钥强度对本地 VPN 设备进行配置，尽量减少中断。

### <a name="can-i-use-different-policies-on-different-connections"></a>是否可以在不同的连接上使用不同的策略？
是的。 自定义策略是在单个连接的基础上应用的。 可以在不同的连接上创建并应用不同的 IPsec/IKE 策略。 也可选择在连接子集上应用自定义策略。 剩余连接使用 Azure 默认 IPsec/IKE 策略集。

### <a name="can-i-use-the-custom-policy-on-vnet-to-vnet-connection-as-well"></a>是否也可在 VNet 到 VNet 连接上使用自定义策略？
是的。可以在 IPsec 跨界连接或 VNet 到 VNet 连接上应用自定义策略。

### <a name="do-i-need-to-specify-the-same-policy-on-both-vnet-to-vnet-connection-resources"></a>是否需在两个 VNet 到 VNet 连接资源上指定同一策略？
是的。 VNet 到 VNet 隧道包含 Azure 中的两个连接资源，一个方向一个资源。 请确保两个连接资源的策略相同，否则无法建立 VNet 到 VNet 连接。

### <a name="does-custom-ipsecike-policy-work-on-expressroute-connection"></a>能否在 ExpressRoute 连接上使用自定义 IPsec/IKE 策略？
不。 只能通过 Azure VPN 网关在 S2S VPN 和 VNet 到 VNet 连接上使用 IPsec/IKE 策略。

### <a name="where-can-i-find-more-configuration-information-for-ipsec"></a>在哪里可以找到有关 IPsec 的详细配置信息？
请参阅[为 S2S 或 VNet 到 VNet 连接配置 IPsec/IKE 策略](../articles/vpn-gateway/vpn-gateway-ipsecikepolicy-rm-powershell.md)
