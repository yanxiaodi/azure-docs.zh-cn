---
title: 关于进行跨界 Azure 连接的 VPN 设备 | Microsoft 文档
description: 本文介绍用于 S2S VPN 网关跨界连接的 VPN 设备和 IPsec 参数。 提供了指向配置说明和示例的链接。
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/20/2019
ms.author: yushwang
ms.openlocfilehash: 1d80c30e3573d76aabcf854b2d97ea849197577c
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173035"
---
# <a name="about-vpn-devices-and-ipsecike-parameters-for-site-to-site-vpn-gateway-connections"></a>关于用于站点到站点 VPN 网关连接的 VPN 设备和 IPsec/IKE 参数

通过 VPN 网关配置站点到站点 (S2S) 跨界 VPN 连接需要用到 VPN 设备。 在创建混合解决方案时，或者每想要在本地网络与虚拟网络之间建立安全连接时，可以使用站点到站点连接。 本文提供了一个已验证 VPN 设备的列表，以及一个适用于 VPN 网关的 IPsec/IKE 参数的列表。

> [!IMPORTANT]
> 如果遇到本地 VPN 设备与 VPN 网关之间的连接问题，请参阅[已知的设备兼容性问题](#known)。
>

### <a name="items-to-note-when-viewing-the-tables"></a>查看表时的注意事项：

* Azure VPN 网关的术语已更改。 只有名称已更改。 没有功能更改。
  * 静态路由 = PolicyBased
  * 动态路由 = RouteBased
* 除非另有说明，否则高性能 VPN 网关和 RouteBased VPN 网关的规范相同。 例如，经验证与 RouteBased VPN 网关兼容的 VPN 设备也与高性能 VPN 网关兼容。

## <a name="devicetable"></a>验证的 VPN 设备和设备配置指南

我们在与设备供应商合作的过程中验证了一系列的标准 VPN 设备。 以下列表的设备系列中的所有设备都应适用于 VPN 网关。 请参阅[关于 VPN 网关设置](vpn-gateway-about-vpn-gateway-settings.md#vpntype)，了解如何将 VPN 类型（PolicyBased 或 RouteBased）用于要配置的 VPN 网关解决方案。

若要获取配置 VPN 设备的帮助，请参考相应设备系列所对应的链接。 我们会尽力提供各种配置说明链接。 如需 VPN 设备支持，请联系设备制造商。

|**供应商**          |**设备系列**     |**最低操作系统版本** |**PolicyBased 配置说明** |**RouteBased 配置说明** |
| ---                | ---                  | ---                   | ---            | ---           |
| A10 Networks, Inc. |Thunder CFW           |ACOS 4.1.1             |不兼容  |[配置指南](https://www.a10networks.com/wp-content/uploads/A10-DG-16161-EN.pdf)|
| Allied Telesis     |AR 系列 VPN 路由器 |AR 系列 5.4.7+               | [配置指南](https://www.alliedtelesis.com/documents/how-to/configure/site-to-site-vpn-between-azure-and-ar-series-router) |[配置指南](https://www.alliedtelesis.com/documents/how-to/configure/site-to-site-vpn-between-azure-and-ar-series-router)|
| Barracuda Networks, Inc. |Barracuda CloudGen 防火墙 |PolicyBased：5.4.3<br>RouteBased：6.2.0 |[配置指南](https://campus.barracuda.com/product/cloudgenfirewall/doc/79462887/how-to-configure-an-ikev1-ipsec-site-to-site-vpn-to-the-static-microsoft-azure-vpn-gateway/) |[配置指南](https://campus.barracuda.com/product/cloudgenfirewall/doc/79462889/how-to-configure-bgp-over-ikev2-ipsec-site-to-site-vpn-to-an-azure-vpn-gateway/) |
| Check Point |安全网关 |R80.10 |[配置指南](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |[配置指南](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco              |ASA       |8.3<br>8.4+ (IKEv2*) |支持 |[配置指南*](https://www.cisco.com/c/en/us/support/docs/security/adaptive-security-appliance-asa-software/214109-configure-asa-ipsec-vti-connection-to-az.html) |
| Cisco |ASR |PolicyBased：IOS 15.1<br>RouteBased：IOS 15.2 |支持 |支持 |
| Cisco | CSR | RouteBased：IOS-XE 16.10 | （未测试） | [配置脚本](vpn-gateway-download-vpndevicescript.md) |
| Cisco |ISR |PolicyBased：IOS 15.0<br>RouteBased*：IOS 15.1 |支持 |支持 |
| Cisco |Meraki |不可用 |不兼容 |不兼容 |
| Cisco | vEdge （Viptela OS） | 18.4.0 （主动/被动模式）<br><br>19.2 （主动/主动模式） | 不兼容 |  [手动配置（主动/被动）](https://community.cisco.com/t5/networking-documents/how-to-configure-ipsec-vpn-connection-between-cisco-vedge-and/ta-p/3841454)<br><br>[Cloud 云中配置（主动/主动）](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/Network-Optimization-and-High-Availability/Network-Optimization-High-Availability-book/b_Network-Optimization-and-HA_chapter_00.html) |
| Citrix |NetScaler MPX、SDX、VPX |10.1 及以上 |[配置指南](https://docs.citrix.com/en-us/netscaler/11-1/system/cloudbridge-connector-introduction/cloudbridge-connector-azure.html) |不兼容 |
| F5 |BIG-IP 系列 |12.0 |[配置指南](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip) |[配置指南](https://devcentral.f5.com/articles/big-ip-to-azure-dynamic-ipsec-tunneling) |
| Fortinet |FortiGate |FortiOS 5.6 | （未测试） |[配置指南](https://docs.fortinet.com/document/fortigate/5.6.0/cookbook/255100/ipsec-vpn-to-azure) |
| Hillstone 网络 | 下一代防火墙（NGFW） | 5.5 R7  | （未测试） | [配置指南](https://www.hillstonenet.com/wp-content/uploads/How-to-setup-Site-to-Site-VPN-between-Microsoft-Azure-and-an-on-premise-Hillstone-Networks-Security-Gateway.pdf) |
| Internet Initiative Japan (IIJ) |SEIL 系列 |SEIL/X 4.60<br>SEIL/B1 4.60<br>SEIL/x86 3.20 |[配置指南](https://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf) |不兼容 |
| Juniper |SRX |PolicyBased：JunOS 10.2<br>Routebased：JunOS 11.4 |支持 |[配置脚本](vpn-gateway-download-vpndevicescript.md) |
| Juniper |J 系列 |PolicyBased：JunOS 10.4r9<br>RouteBased：JunOS 11.4 |支持 |[配置脚本](vpn-gateway-download-vpndevicescript.md) |
| Juniper |ISG |ScreenOS 6.3 |支持 |[配置脚本](vpn-gateway-download-vpndevicescript.md) |
| Juniper |SSG |ScreenOS 6.2 |支持 |[配置脚本](vpn-gateway-download-vpndevicescript.md) |
| Juniper |MX |JunOS 12.x|支持 |[配置脚本](vpn-gateway-download-vpndevicescript.md) |
| Microsoft |路由和远程访问服务 |Windows Server 2012 |不兼容 |支持 |
| 打开系统 AG |任务控制安全网关 |不可用 |[配置指南](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) |不兼容 |
| Palo Alto Networks |运行 PAN-OS 的所有设备 |PAN-OS<br>PolicyBased：6.1.5 或更高版本<br>RouteBased：7.1.4 |支持 |[配置指南](https://knowledgebase.paloaltonetworks.com/KCSArticleDetail?id=kA10g000000Cm6WCAS) |
| Sentrium （开发人员） | VyOS | VyOS 1.2。2 | （未测试） | [配置指南](https://vyos.readthedocs.io/en/latest/appendix/examples/azure-vpn-bgp.html)|
| ShareTech | Next Generation UTM（NU 系列） | 9.0.1.3 | 不兼容 | [配置指南](http://www.sharetech.com.tw/images/file/Solution/NU_UTM/S2S_VPN_with_Azure_Route_Based_en.pdf) |
| SonicWall |TZ 系列、NSA 系列<br>SuperMassive 系列<br>E 类 NSA 系列 |SonicOS 5.8.x<br>SonicOS 5.9.x<br>SonicOS 6.x |不兼容 |[配置指南](https://www.sonicwall.com/support/knowledge-base/170505320011694) |
| Sophos | XG 下一代防火墙 | XG v17 | （未测试） | [配置指南](https://community.sophos.com/kb/127546)<br><br>[配置指南 - 多个 SA](https://community.sophos.com/kb/en-us/133154) |
| Synology | MR2200ac <br>RT2600ac <br>RT1900ac | SRM1.1.5/VpnPlusServer-1.2.0 | （未测试） | [配置指南](https://www.synology.com/en-global/knowledgebase/SRM/tutorial/VPN/How_to_set_up_Site_to_Site_VPN_between_Synology_Router_and_MS_Azure) |
| Ubiquiti | EdgeRouter | EdgeOS v1.10 | （未测试） | [基于 IKEv2/IPsec 的BGP](https://help.ubnt.com/hc/en-us/articles/115012374708)<br><br>[基于 IKEv2/IPsec 的 VTI](https://help.ubnt.com/hc/en-us/articles/115012305347)
| WatchGuard |All |Fireware XTM<br> PolicyBased：v11.11.x<br>RouteBased：v11.12.x |[配置指南](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA2F00000000LI7KAM&lang=en_US) |[配置指南](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA22A000000XZogSAG&lang=en_US)|
| Zyxel |ZyWALL USG 系列<br>ZyWALL ATP 系列<br>ZyWALL VPN 系列 | ZLD v4.32+ | （未测试） | [基于 IKEv2/IPsec 的 VTI](https://businessforum.zyxel.com/discussion/2648/)<br><br>[基于 IKEv2/IPsec 的BGP](https://businessforum.zyxel.com/discussion/2650/)|

> [!NOTE]
>
> (*) Cisco ASA 版本 8.4+ 增加了 IKEv2 支持，可以通过“UsePolicyBasedTrafficSelectors”选项使用自定义 IPsec/IKE 策略连接到 Azure VPN 网关。 请参阅此[操作说明文章](vpn-gateway-connect-multiple-policybased-rm-ps.md)。
>
> (\*\*) ISR 7200 系列路由器仅支持 PolicyBased VPN。

## <a name="configscripts"></a>从 Azure 下载 VPN 设备配置脚本

对于某些设备，你可以直接从 Azure 下载配置脚本。 有关详细信息和下载说明，请参阅[下载 VPN 设备配置脚本](vpn-gateway-download-vpndevicescript.md)。

### <a name="devices-with-available-configuration-scripts"></a>具有可用配置脚本的设备

[!INCLUDE [scripts](../../includes/vpn-gateway-device-configuration-scripts.md)]

## <a name="additionaldevices"></a>非验证的 VPN 设备

即使没有看到设备在“已验证的 VPN 设备”表列出中，该设备也有可能适用于站点到站点连接。 请联系设备制造商，了解更多支持和配置说明。

## <a name="editing"></a>编辑设备配置示例

在下载提供的 VPN 设备配置示例后，需要替换一些值来反映你环境的设置。

### <a name="to-edit-a-sample"></a>编辑示例的步骤：

1. 使用记事本打开示例。
2. 搜索所有 <*text*> 字符串并将其替换为与环境相关的值。 请确保包含 < 和 >。 指定名称时，选择的名称应是唯一的。 如果命令无效，请查看设备制造商文档。

| **示例文本** | **更改为** |
| --- | --- |
| &lt;RP_OnPremisesNetwork&gt; |为此对象选择的名称。 示例：myOnPremisesNetwork |
| &lt;RP_AzureNetwork&gt; |为此对象选择的名称。 示例：myAzureNetwork |
| &lt;RP_AccessList&gt; |为此对象选择的名称。 示例：myAzureAccessList |
| &lt;RP_IPSecTransformSet&gt; |为此对象选择的名称。 示例：myIPSecTransformSet |
| &lt;RP_IPSecCryptoMap&gt; |为此对象选择的名称。 示例：myIPSecCryptoMap |
| &lt;SP_AzureNetworkIpRange&gt; |指定范围。 例如：192.168.0.0 |
| &lt;SP_AzureNetworkSubnetMask&gt; |指定子网掩码。 例如：255.255.0.0 |
| &lt;SP_OnPremisesNetworkIpRange&gt; |指定本地范围。 例如：10.2.1.0 |
| &lt;SP_OnPremisesNetworkSubnetMask&gt; |指定本地子网掩码。 例如：255.255.255.0 |
| &lt;SP_AzureGatewayIpAddress&gt; |此信息特定于虚拟网络，位于管理门户的“网关 IP 地址”中。 |
| &lt;SP_PresharedKey&gt; |此信息特定于虚拟网络，位于管理门户的“管理密钥”中。 |

## <a name="ipsec"></a>IPsec/IKE 参数

> [!IMPORTANT]
> 1. 下面各表包含了 Azure VPN 网关在默认配置中使用的算法和参数的组合。 对于使用 Azure 资源管理器部署模型创建的基于路由的 VPN 网关，可以在每个单独的连接上指定一个自定义策略。 有关详细说明，请参阅[配置 IPsec/IKE 策略](vpn-gateway-ipsecikepolicy-rm-powershell.md)。
>
> 2. 此外，还必须将 TCP **MSS** 固定在 **1350**。 或者，如果 VPN 设备不支持 MSS 固定，则可以改为在隧道接口上将 **MTU** 设置为 **1400** 字节。
>

在下面的表中：

* SA = 安全关联
* IKE 阶段 1 也称“主模式”
* IKE 阶段 2 也称“快速模式”

### <a name="ike-phase-1-main-mode-parameters"></a>IKE 阶段 1（主模式）参数

| **属性**          |**PolicyBased**    | **RouteBased**    |
| ---                   | ---               | ---               |
| SDK 版本           |IKEv1              |IKEv2              |
| Diffie-Hellman 组  |组 2（1024 位） |组 2（1024 位） |
| 身份验证方法 |预共享密钥     |预共享密钥     |
| 加密和哈希算法 |1.AES256、SHA256<br>2.AES256、SHA1<br>3.AES128、SHA1<br>4. 3DES、SHA1 |1.AES256、SHA1<br>2.AES256、SHA256<br>3.AES128、SHA1<br>4.AES128、SHA256<br>5. 3DES、SHA1<br>6. 3DES、SHA256 |
| SA 生存期           |28,800 秒     |28,800 秒     |

### <a name="ike-phase-2-quick-mode-parameters"></a>IKE 阶段 2（快速模式）参数

| **Property**                  |**PolicyBased**| **RouteBased**                              |
| ---                           | ---           | ---                                         |
| SDK 版本                   |IKEv1          |IKEv2                                        |
| 加密和哈希算法 |1.AES256、SHA256<br>2.AES256、SHA1<br>3.AES128、SHA1<br>4. 3DES、SHA1 |[RouteBased QM SA 产品/服务](#RouteBasedOffers) |
| SA 生存期（时间）            |3,600 秒  |27,000 秒                                |
| SA 生存期（字节数）           |102,400,000 KB | -                                           |
| 完全向前保密 (PFS) |否             |[RouteBased QM SA 产品/服务](#RouteBasedOffers) |
| 死对等体检测 (DPD)     |不支持  |支持                                    |


### <a name ="RouteBasedOffers"></a>RouteBased VPN IPsec 安全关联（IKE 快速模式 SA）产品/服务

下表列出了 IPsec SA（IKE 快速模式）产品/服务。 这些产品按提供或接受产品的偏好顺序列出。

#### <a name="azure-gateway-as-initiator"></a>Azure 网关作为发起方

|-  |**加密**|**身份验证**|**PFS 组**|
|---| ---          |---               |---          |
| 1 |GCM AES256    |GCM (AES256)      |无         |
| 2 |AES256        |SHA1              |无         |
| 3 |3DES          |SHA1              |无         |
| 4 |AES256        |SHA256            |无         |
| 5 |AES128        |SHA1              |无         |
| 6 |3DES          |SHA256            |无         |

#### <a name="azure-gateway-as-responder"></a>Azure 网关作为响应方

|-  |**加密**|**身份验证**|**PFS 组**|
|---| ---          | ---              |---          |
| 1 |GCM AES256    |GCM (AES256)      |无         |
| 2 |AES256        |SHA1              |无         |
| 3 |3DES          |SHA1              |无         |
| 4 |AES256        |SHA256            |无         |
| 5 |AES128        |SHA1              |无         |
| 6 |3DES          |SHA256            |无         |
| 7 |DES           |SHA1              |无         |
| 8 |AES256        |SHA1              |1            |
| 9 |AES256        |SHA1              |2            |
| 10|AES256        |SHA1              |14           |
| 11|AES128        |SHA1              |1            |
| 12|AES128        |SHA1              |2            |
| 13|AES128        |SHA1              |14           |
| 14|3DES          |SHA1              |1            |
| 15|3DES          |SHA1              |2            |
| 16|3DES          |SHA256            |2            |
| 17|AES256        |SHA256            |1            |
| 18|AES256        |SHA256            |2            |
| 19|AES256        |SHA256            |14           |
| 20|AES256        |SHA1              |24           |
| 21|AES256        |SHA256            |24           |
| 22|AES128        |SHA256            |无         |
| 23|AES128        |SHA256            |1            |
| 24|AES128        |SHA256            |2            |
| 25|AES128        |SHA256            |14           |
| 26|3DES          |SHA1              |14           |

* 可以使用 RouteBased 和 HighPerformance VPN 网关指定 IPsec ESP NULL 加密。 基于 Null 的加密不对传输中的数据提供保护，仅应在需要最大吞吐量和最小延迟时才使用。 客户端可以在 VNet 到 VNet 通信方案中选择使用此方法，或者在解决方案中的其他位置应用加密时使用此方法。
* 若要通过 Internet 建立跨界连接，请使用默认的 Azure VPN 网关设置以及上表中列出的加密和哈希算法，确保关键通信的安全性。

## <a name="known"></a>已知的设备兼容性问题

> [!IMPORTANT]
> 这些是第三方 VPN 设备与 Azure VPN 网关之间的已知兼容性问题。 Azure 团队正积极与供应商合作解决此处列出的问题。 解决问题后，将使用最新的信息更新此页。 请定期查看。
>
>

### <a name="feb-16-2017"></a>2017 年 2 月 16 日

**早于 7.1.4 版的 Palo Alto Networks 设备**（针对 Azure 基于路由的 VPN）：如果使用的是早于 7.1.4 版的 Palo Alto Networks VPN 设备，并且遇到 Azure 基于路由的 VPN 网关的连接问题，请执行以下步骤：

1. 检查 Palo Alto Networks 设备的固件版本。 如果 PAN-OS 版本低于 7.1.4，请升级到 7.1.4。
2. 连接到 Azure VPN 网关时，请在 Palo Alto Networks 设备上将阶段 2 SA（或快速模式 SA）生存期更改为 28,800 秒（8 小时）。
3. 如果仍然遇到连接问题，请从 Azure 门户开具支持请求票证。
