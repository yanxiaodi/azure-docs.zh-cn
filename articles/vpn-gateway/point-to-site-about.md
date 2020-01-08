---
title: 关于 Azure 点到站点 VPN 连接 | Microsoft Docs
description: 可以借助本文了解点到站点连接，并确定要使用的 P2S VPN 网关身份验证类型。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: cherylmc
ms.openlocfilehash: f1e014bb14b2b5c1ae924f4371e08aa8bf8698f2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056468"
---
# <a name="about-point-to-site-vpn"></a>关于点到站点 VPN

点到站点 (P2S) VPN 网关连接用于创建从单个客户端计算机到虚拟网络的安全连接。 可通过从客户端计算机启动连接来建立 P2S 连接。 对于要从远程位置（例如从家里或会议室）连接到 Azure VNet 的远程工作者，此解决方案很有用。 如果只有一些客户端需要连接到 VNet，则还可以使用 P2S VPN 这一解决方案来代替 S2S VPN。 本文适用于 Resource Manager 部署模型。

## <a name="protocol"></a>P2S 使用哪种协议？

点到站点 VPN 可使用以下协议之一：

* **OpenVPN® 协议**，SSL/TLS 基于 VPN 协议。 SSL VPN 解决方案可以穿透防火墙，因为大多数防火墙打开 TCP 端口 443 出站，SSL 使用。 OpenVPN 可以用于 Android、 iOS 从连接 (版本 11.0 和更高版本)，Windows、 Linux 和 Mac 设备 (OSX 版本 10.13 及更高版本)。

* 安全套接字隧道协议 (SSTP)，这是一种基于 SSL 的专属协议。 SSL VPN 解决方案可以穿透防火墙，因为大多数防火墙打开 TCP 端口 443 出站，SSL 使用。 只有 Windows 设备支持 SSTP。 Azure 支持所有采用 SSTP 的 Windows 版本（Windows 7 和更高版本）。

* IKEv2 VPN，这是一种基于标准的 IPsec VPN 解决方案。 IKEv2 VPN 可用于从 Mac 设备进行连接（OSX 10.11 和更高版本）。


>[!NOTE]
>P2S 的 IKEv2 和 OpenVPN 仅可用于资源管理器部署模型。 它们不可用于经典部署模型。
>

## <a name="authentication"></a>如何对 P2S VPN 客户端进行身份验证？

在 Azure 接受 P2S VPN 连接之前，必须先对用户进行身份验证。 Azure 提供两种机制用于对连接方用户进行身份验证。

### <a name="authenticate-using-native-azure-certificate-authentication"></a>使用本机 Azure 证书身份验证进行身份验证

使用本机 Azure 证书身份验证时，设备上的客户端证书用于对连接方用户进行身份验证。 客户端证书从受信任的根证书生成，并安装在每台客户端计算机上。 可以使用通过企业解决方案生成的根证书，也可以生成自签名证书。

客户端证书的验证由 VPN 网关执行，在建立 P2S VPN 连接期间发生。 验证时需要使用根证书，必须将该证书上传到 Azure。

### <a name="authenticate-using-active-directory-ad-domain-server"></a>使用 Active Directory (AD) 域服务器进行身份验证

AD 域身份验证可让用户使用其组织域凭据连接到 Azure。 它需要一台与 AD 服务器集成的 RADIUS 服务器。 组织也可以利用其现有的 RADIUS 部署。   
  
可将 RADIUS 服务器部署在本地或 Azure VNET 中。 在身份验证期间，Azure VPN 网关充当传递设备，在 RADIUS 服务器与连接方设备之间来回转发身份验证消息。 因此，RADIUS 服务器必须能够访问网关。 如果 RADIUS 服务器位于本地，需要建立从 Azure 到本地站点的 VPN S2S 连接，才能实现这种访问。  
  
RADIUS 服务器还能与 AD 证书服务集成。 这样，便可以使用 RADIUS 服务器以及用于 P2S 证书身份验证的企业证书部署，作为 Azure 证书身份验证的替代方法。 此方法的优点是不需要将根证书和吊销的证书上传到 Azure。

RADIUS 服务器还能与其他外部标识系统集成。 这样就为 P2S VPN 提供了大量的身份验证选项，包括多重身份验证选项。

>[!NOTE]
>**OpenVPN® 协议**不支持 RADIUS 身份验证。
>

![点到站点](./media/point-to-site-about/p2s.png "点到站点")

## <a name="what-are-the-client-configuration-requirements"></a>客户端配置要求是什么？

>[!NOTE]
>对于 Windows 客户端，你必须具有客户端设备上的管理员权限，才能发起从客户端设备到 Azure 的 VPN 连接。
>

用户使用 Windows 和 Mac 设备上的本机 VPN 客户端建立 P2S 连接。 Azure 提供一个 VPN 客户端配置 zip 文件，其中包含这些本机客户端连接到 Azure 时所需的设置。

* 对于 Windows 设备，VPN 客户端配置包括用户在其设备上安装的安装程序包。
* 对于 Mac 设备，该配置包括用户在其设备上安装的 mobileconfig 文件。

该 zip 文件还提供 Azure 端上的一些重要设置的值，使用这些设置可为这些设备创建你自己的配置文件。 其中一些值包括 VPN 网关地址、配置的隧道类型、路由，以及用于网关验证的根证书。

>[!NOTE]
>[!INCLUDE [TLS version changes](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="gwsku"></a>哪些网关 SKU 支持 P2S VPN？

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* 有关网关 SKU 的建议，请参阅[关于 VPN 网关设置](vpn-gateway-about-vpn-gateway-settings.md#gwsku)。

>[!NOTE]
>基本 SKU 不支持 IKEv2 或 RADIUS 身份验证。
>

## <a name="IKE/IPsec policies"></a>IKE/IPsec 策略对 P2S VPN 网关上配置？


**IKEv2**

|**Cipher** | **完整性** | **PRF** | **DH 组** |
|---        | ---           | ---       | ---   |
|GCM_AES256 |   GCM_AES256  | SHA384    | GROUP_24 |
|GCM_AES256 |   GCM_AES256  | SHA384    | GROUP_14 |
|GCM_AES256 |   GCM_AES256  | SHA384    | GROUP_ECP384 |
|GCM_AES256 |   GCM_AES256  | SHA384    | GROUP_ECP256 |
|GCM_AES256 |   GCM_AES256  | SHA256    | GROUP_24 |
|GCM_AES256 |   GCM_AES256  | SHA256    | GROUP_14 |
|GCM_AES256 |   GCM_AES256  | SHA256    | GROUP_ECP384 |
|GCM_AES256 |   GCM_AES256  | SHA256    | GROUP_ECP256 |
|AES256     |   SHA384      | SHA384    | GROUP_24 |
|AES256     |   SHA384      | SHA384    | GROUP_14 |
|AES256     |   SHA384      | SHA384    | GROUP_ECP384 |
|AES256     |   SHA384      | SHA384    | GROUP_ECP256 |
|AES256     |   SHA256      | SHA256    | GROUP_24 |
|AES256     |   SHA256      | SHA256    | GROUP_14 |
|AES256     |   SHA256      | SHA256    | GROUP_ECP384 |
|AES256     |   SHA256      | SHA256    | GROUP_ECP256 |
|AES256     |   SHA256      | SHA256    | GROUP_2 |

**IPsec**

|**Cipher** | **完整性** | **PFS 组** |
|---        | ---           | ---       |
|GCM_AES256 | GCM_AES256 | GROUP_NONE |
|GCM_AES256 | GCM_AES256 | GROUP_24 |
|GCM_AES256 | GCM_AES256 | GROUP_14 |
|GCM_AES256 | GCM_AES256 | GROUP_ECP384 |
|GCM_AES256 | GCM_AES256 | GROUP_ECP256 |
| AES256    | SHA256 | GROUP_NONE |
| AES256    | SHA256 | GROUP_24 |
| AES256    | SHA256 | GROUP_14 |
| AES256    | SHA256 | GROUP_ECP384 |
| AES256    | SHA256 | GROUP_ECP256 |
| AES256    | SHA1 | GROUP_NONE |

## <a name="TLS policies"></a>对 P2S VPN 网关上配置哪些 TLS 策略？
**TLS**

|**策略** |
|---| 
|TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 |
|TLS_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_RSA_WITH_AES_256_CBC_SHA256 |




## <a name="configure"></a>如何配置 P2S 连接？

P2S 配置需要相当多的特定步骤。 以下文章包含引导你完成 P2S 配置的步骤，以及用于配置 VPN 客户端设备的链接：

* [配置 P2S 连接 - RADIUS 身份验证](point-to-site-how-to-radius-ps.md)

* [配置 P2S 连接 - Azure 本机证书身份验证](vpn-gateway-howto-point-to-site-rm-ps.md)

* [配置 OpenVPN](vpn-gateway-howto-openvpn.md)

## <a name="how-do-i-remove-the-configuration-of-a-p2s-connection"></a>如何删除 P2S 连接的配置？

可以使用 az cli 和以下命令删除 P2S 配置： 

`az network vnet-gateway update --name <gateway-name> --resource-group <resource-group name> --remove "vpnClientConfiguration"`
 
## <a name="faqcert"></a>本机 Azure 证书身份验证常见问题解答

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faqradius"></a>RADIUS 身份验证常见问题解答

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>后续步骤

* [配置 P2S 连接 - RADIUS 身份验证](point-to-site-how-to-radius-ps.md)

* [配置 P2S 连接 - Azure 本机证书身份验证](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN"是 OpenVPN Inc.的商标**
