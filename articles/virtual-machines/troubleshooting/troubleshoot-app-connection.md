---
title: 排查 Azure 中 VM 应用程序访问问题 | Microsoft Docs
description: 使用以下详细故障排除步骤可以查明连接到 Azure 中虚拟机上运行的应用程序时遇到的问题。
services: virtual-machines
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: 无法启动应用程序, 程序打不开, 侦听端口受阻, 无法启动程序, 侦听端口受阻
ms.assetid: b9ff7cd0-0c5d-4c3c-a6be-3ac47abf31ba
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: caf73ffbc18a603ace22acfbd0da490048da698a
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058123"
---
# <a name="troubleshoot-application-connectivity-issues-on-virtual-machines-in-azure"></a>排查 Azure 中虚拟机上的应用程序连接问题

有多种原因可导致无法启用或连接到在 Azure 虚拟机 (VM) 上运行的应用程序。 原因包括应用程序未在预期端口上运行或侦听、侦听端口受到阻止，或网络规则未将流量正确传递到应用程序。 本文说明有条理地找到问题并更正问题。

如果在使用 RDP 或 SSH 连接到 VM 时发生问题，请先参阅以下文章之一：

* [对与基于 Windows 的 Azure 虚拟机的远程桌面连接进行故障排除](troubleshoot-rdp-connection.md)
* [对于基于 Linux 的 Azure 虚拟机的 Secure Shell (SSH) 连接进行故障排除](troubleshoot-ssh-connection.md)。

如果对本文中的任何内容需要更多帮助，可以联系 [MSDN Azure 和堆栈溢出论坛](https://azure.microsoft.com/support/forums/)上的 Azure 专家。 或者，也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **获取支持**。

## <a name="quick-start-troubleshooting-steps"></a>快速入门故障排除步骤
如果在连接到应用程序时发生问题，请尝试以下一般故障排除步骤。 执行每个步骤之后，尝试重新连接到应用程序：

* 重启虚拟机
* 重新创建终结点/防火墙规则/网络安全组 (NSG) 规则
  * [Resource Manager 模型 - 管理网络安全组](../../virtual-network/manage-network-security-group.md)
  * [经典模型 - 管理云服务终结点](../../cloud-services/cloud-services-enable-communication-role-instances.md)
* 从不同的位置（例如不同的 Azure 虚拟网络）进行连接
* 重新部署虚拟机
  * [重新部署 Windows VM](redeploy-to-new-node-windows.md)
  * [重新部署 Linux VM](redeploy-to-new-node-linux.md)
* 重新创建虚拟机

有关详细信息，请参阅[终结点连接（RDP/SSH/HTTP 等故障）疑难解答](https://social.msdn.microsoft.com/Forums/azure/en-US/538a8f18-7c1f-4d6e-b81c-70c00e25c93d/troubleshooting-endpoint-connectivity-rdpsshhttp-etc-failures?forum=WAVirtualMachinesforWindows)。

## <a name="detailed-troubleshooting-overview"></a>详细故障排除概述
有四个主要区域需要对 Azure 虚拟机上运行的应用程序的访问进行故障排除。

![对无法启动应用程序进行故障排除](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access1.png)

1. 在 Azure 虚拟机上运行的应用程序。
   * 应用程序本身是否正常运行？
2. Azure 虚拟机。
   * VM 本身是否正常运行并响应请求？
3. Azure 网络终结点。
   * 用于经典部署模型中虚拟机的云服务终结点。
   * 用于 Resource Manager 部署模型中虚拟机的网络安全组和入站 NAT 规则。
   * 流量是否可以通过预期的端口从用户流向 VM/应用程序？
4. Internet 边缘设备。
   * 是否有防火墙规则阻止流量正常流动？

对于通过站点到站点 VPN 或 ExpressRoute 连接访问应用程序的客户端计算机，可能会导致问题的主要区域是应用程序和 Azure 虚拟机。

若要确定问题根源并进行更正，请执行以下步骤。

## <a name="step-1-access-application-from-target-vm"></a>步骤 1：从目标 VM 访问应用程序
尝试使用适当的客户端程序，从运行该程序的 VM 访问应用程序。 使用本地主机名、本地 IP 地址或环回地址 (127.0.0.1)。

![直接从 VM 启动应用程序](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access2.png)

例如，如果应用程序是 Web 服务器，则在 VM 上打开浏览器，并尝试访问 VM 上托管的网页。

如果可以访问应用程序，请转到[步骤 2](#step2)。

如果不能访问应用程序，请验证以下设置：

* 应用程序是否在目标虚拟机上运行。
* 应用程序是否在预期 TCP 和 UDP 端口侦听。

在基于 Windows 和基于 Linux 的虚拟机上，使用 **netstat -a** 命令显示活动的侦听端口。 检查应用程序应侦听的预期端口的输出。 重新启动应用程序，或根据需要将其配置为使用预期的端口，然后尝试在本地重新访问应用程序。

## <a id="step2"></a>步骤 2：从同一虚拟网络中的另一个 VM 访问应用程序
使用 VM 的主机名或其 Azure 分配的公共、专用或提供程序 IP 地址尝试访问位于不同 VM 但相同虚拟网络中的应用程序。 对于使用经典部署模型创建的虚拟机，请不要使用云服务的公共 IP 地址。

![从不同的 VM 启动应用程序](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access3.png)

例如，如果应用程序是 Web 服务器，则尝试在相同虚拟网络中的不同 VM 上使用浏览器访问网页。

如果可以访问应用程序，请转到[步骤 3](#step3)。

如果不能访问应用程序，请验证以下设置：

* 目标 VM 上的主机防火墙允许入站请求和出站响应流量。
* 目标 VM 上运行的入侵检测或网络监视软件允许流量。
* 云服务终结点或网络安全组允许流量：
  * [经典模型 - 管理云服务终结点](../../cloud-services/cloud-services-enable-communication-role-instances.md)
  * [Resource Manager 模型 - 管理网络安全组](../../virtual-network/manage-network-security-group.md)
* VM 中在测试 VM 和 VM 之间的路径运行的单独组件（例如负载均衡器或防火墙）允许流量。

在基于 Windows 的虚拟机上，使用具有高级安全性的 Windows 防火墙确定防火墙规则是否排除应用程序的入站和出站流量。

## <a id="step3"></a>步骤 3：从虚拟网络外部访问应用程序
尝试通过虚拟网络之外的计算机访问应用程序，作为应用程序运行的 VM。 使用其他网络作为原始客户端计算机。

![从虚拟网络外部的计算机启动应用程序。](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access4.png)

例如，如果应用程序是 Web 服务器，则尝试通过不在虚拟网络中的虚拟机使用浏览器访问网页。

如果不能访问应用程序，请验证以下设置：

* 对于使用经典部署模型创建的 VM：
  
  * 确保 VM 的终结点配置允许传入流量，尤其是协议（TCP 或 UDP）及公用和专用端口号。
  * 确保终结点上的访问控制列表 (ACL) 不会阻止来自 Internet 的传入流量。
  * 有关详细信息，请参阅[如何对虚拟机设置终结点](../windows/classic/setup-endpoints.md)。
* 对于使用 Resource Manager 部署模型创建的 VM：
  
  * 确保 VM 的入站 NAT 规则配置允许传入流量，尤其是协议（TCP 或 UDP）及公用和专用端口号。
  * 确保网络安全组允许入站请求和出站响应流量。
  * 有关详细信息，请参阅[什么是网络安全组？](../../virtual-network/security-overview.md)

如果虚拟机或终结点是负载均衡集的成员，则：

* 验证探测协议（TCP 或 UDP）和端口号是否正确。
* 如果探测协议和端口与负载均衡集协议和端口不同，则：
  * 验证应用程序是否在探测协议（TCP 或 UDP）和端口号（在目标 VM 上使用 **netstat –a**）上侦听。
  * 确保目标 VM 上的主机防火墙允许入站探测请求和出站探测响应流量。

如果可以访问应用程序，请确保 Internet 边缘设备允许：

* 从客户端计算机到 Azure 虚拟机的出站应用程序请求流量。
* 来自 Azure 虚拟机的入站应用程序响应流量。

## <a name="step-4-if-you-cannot-access-the-application-use-ip-verify-to-check-the-settings"></a>步骤 4：如果无法访问应用程序，请使用“IP 验证”来检查设置。 

有关详细信息，请参阅 [Azure network monitoring overview](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)（Azure 网络监视概述）。 

## <a name="additional-resources"></a>其他资源
[对与基于 Windows 的 Azure 虚拟机的远程桌面连接进行故障排除](troubleshoot-rdp-connection.md)

[对与基于 Linux 的 Azure 虚拟机的 Secure Shell (SSH) 连接进行故障排除](troubleshoot-ssh-connection.md)


