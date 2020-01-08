---
title: Azure 到 Azure 网络连接问题和错误的 Azure Site Recovery 疑难解答 |Microsoft Docs
description: 排查复制 Azure 虚拟机进行灾难恢复时出现的错误和问题
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/05/2019
ms.author: asgang
ms.openlocfilehash: 8e1350a22554bab257e8c99954c2beaa357de2ff
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934521"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>Azure 到 Azure VM 网络连接问题疑难解答

本文介绍将 Azure 虚拟机从一个区域复制和恢复到另一个区域时, 与网络连接相关的常见问题。 有关网络要求的详细信息, 请参阅[复制 Azure vm 的连接要求](azure-to-azure-about-networking.md)。

若要 Site Recovery 复制正常运行，需要从 VM 到特定 URL 或 IP 范围的出站连接。 如果 VM 位于防火墙后或使用网络安全组 (NSG) 规则来控制出站连接，则可能会遇到以下问题之一。

**URL** | **详细信息**  
--- | ---
\* .blob.core.windows.net | 必需，以便从 VM 将数据写入到源区域中的缓存存储帐户。 如果你知道 Vm 的所有缓存存储帐户, 则可以允许-列出特定存储帐户 Url (例如, cache1.blob.core.windows.net 和 cache2.blob.core.windows.net), 而不是 *. blob.core.windows.net
login.microsoftonline.com | 必需，用于向 Site Recovery 服务 URL 进行授权和身份验证。
*.hypervrecoverymanager.windowsazure.com | 必需，以便从 VM 进行 Site Recovery 服务通信。 如果防火墙代理支持 IP，则可以使用相应的“Site Recovery IP”。
*.servicebus.windows.net | 必需，以便从 VM 写入 Site Recovery 监视和诊断数据。 如果防火墙代理支持 IP，则可以使用相应的“Site Recovery 监视 IP”。

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Site Recovery URL 或 IP 范围的出站连接（错误代码 151037 或 151072）

## <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>问题 1：未能向 Site Recovery 注册 Azure 虚拟机 (151195) </br>
- **可能的原因** </br>
  - 由于 DNS 解析失败而无法建立到 Site Recovery 终结点的连接。
  - 在重新保护期间，对虚拟机进行故障转移但无法从 DR 区域访问 DNS 服务器时经常会出现此问题。

- **解决方法**
   - 如果使用的是自定义 DNS, 请确保可从灾难恢复区域访问 DNS 服务器。 若要检查你是否具有自定义 DNS，请转到“VM”>“灾难恢复网络”>“DNS 服务器”。 尝试从虚拟机访问 DNS 服务器。 如果不可访问, 则可通过在 DNS 服务器上进行故障转移, 或者在 DR 网络和 DNS 之间创建网络线路来访问它。

    ![com-error](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


## <a name="issue-2-site-recovery-configuration-failed-151196"></a>问题 2：Site Recovery 配置失败 (151196)

> [!NOTE]
> 如果虚拟机位于**标准**内部负载均衡器后面, 则默认情况下, 它不会访问 O365 ip (即 login.microsoftonline.com)。 如[本文](https://aka.ms/lboutboundrulescli)中所述, 将其更改为**基本**内部负载均衡器类型或创建出站访问。

- **可能的原因** </br>
  - 无法建立到 Office 365 身份验证和标识 IP4 终结点的连接。

- **解决方法**
  - Azure Site Recovery 需要具有对 Office 365 IP 范围的访问权限来进行身份验证。
    如果你使用 Azure 网络安全组 (NSG) 规则/防火墙代理控制 VM 上的出站网络连接，请确保允许到 O365 IP 范围的通信。 创建基于[Azure Active Directory (Azure AD) 服务标记](../virtual-network/security-overview.md#service-tags)的 NSG 规则, 以允许访问相应的所有 IP 地址 Azure AD
      - 如果以后将新地址添加到 Azure AD, 则需要创建新的 NSG 规则。

### <a name="example-nsg-configuration"></a>NSG 配置示例

此示例演示如何为要复制的 VM 配置 NSG 规则。

- 如果使用 NSG 规则控制出站连接，请对所有必需的 IP 地址范围使用端口 443 的“允许 HTTPS 出站”规则。
- 此示例假设 VM 源位置是“美国东部”，目标位置是“美国中部”。

### <a name="nsg-rules---east-us"></a>NSG 规则 - 美国东部

1. 基于 NSG 规则为“Storage.EastUS”创建出站 HTTPS (443) 安全规则，如以下屏幕截图所示。

      ![storage-tag](./media/azure-to-azure-about-networking/storage-tag.png)

2. 基于 NSG 规则为“AzureActiveDirectory”创建出站 HTTPS (443) 安全规则，如以下屏幕截图所示。

      ![aad-tag](./media/azure-to-azure-about-networking/aad-tag.png)

3. 为对应于目标位置的 Site Recovery IP 创建出站 HTTPS (443) 规则：

   **Location** | **Site Recovery IP 地址** |  **Site Recovery 监视 IP 地址**
    --- | --- | ---
   美国中部 | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>NSG 规则 - 美国中部

必须创建这些规则，才能在故障转移后启用从目标区域到源区域的复制：

1. 基于 NSG 为“Storage.CentralUS”创建出站 HTTPS (443) 安全规则。

2. 基于 NSG 规则为“AzureActiveDirectory”创建出站 HTTPS (443) 安全规则。

3. 为对应于源位置的 Site Recovery IP 创建出站 HTTPS (443) 规则：

   **Location** | **Site Recovery IP 地址** |  **Site Recovery 监视 IP 地址**
    --- | --- | ---
   美国中部 | 13.82.88.226 | 104.45.147.24
## <a name="issue-3-site-recovery-configuration-failed-151197"></a>问题 3：Site Recovery 配置失败 (151197)
- **可能的原因** </br>
  - 无法建立到 Azure Site Recovery 服务终结点的连接。

- **解决方法**
  - Azure Site Recovery 需要根据区域访问 [Site Recovery IP 范围](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)。 请确保可以从虚拟机访问所需的 IP 范围。


## <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>问题 4：当网络流量通过本地代理服务器时 A2A 复制失败 (151072)
- **可能的原因** </br>
  - 自定义代理设置无效, 并且 Azure Site Recovery 移动服务代理未自动检测来自 IE 的代理设置


- **解决方法**
  1. 移动服务代理通过 Windows 上的 IE 和 Linux 上的 /etc/environment 检测代理设置。
  2. 如果希望仅为 Azure Site Recovery 移动服务设置代理, 可以在位于以下位置的 ProxyInfo 中提供代理详细信息:</br>
     - ***Linux*** 上的 ``/usr/local/InMage/config/``
     - ***Windows*** 上的 ``C:\ProgramData\Microsoft Azure Site Recovery\Config``
  3. ProxyInfo.conf 应包含采用以下 INI 格式的代理设置。</br>
                *[proxy]*</br>
                *Address=http://1.2.3.4*</br>
                *Port=567*</br>
  4. Azure Site Recovery 移动服务代理仅支持未经身份验证的代理。

### <a name="fix-the-problem"></a>解决问题
若要允许[所需 URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) 或[所需 IP 范围](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges)，请按照[网络指南文档](site-recovery-azure-to-azure-networking-guidance.md)中的步骤进行操作。


## <a name="next-steps"></a>后续步骤
[复制 Azure 虚拟机](site-recovery-replicate-azure-to-azure.md)
