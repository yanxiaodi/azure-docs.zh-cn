---
title: 什么是 Azure 专用 DNS？
description: Microsoft Azure 上的专用 DNS 托管服务概述。
services: dns
author: vhorne
ms.service: dns
ms.topic: overview
ms.date: 6/12/2019
ms.author: victorh
ms.openlocfilehash: 0921a1ac7aa1192fae78f168c2eb51ee3e74e24a
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774620"
---
# <a name="what-is-azure-private-dns"></a>什么是 Azure 专用 DNS？

> [!IMPORTANT]
> Azure 专用 DNS 当前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

域名系统（或称为 DNS）负责将服务名称转换（或解析）为其 IP 地址。  Azure DNS 是 DNS 域的托管服务，它使用 Microsoft Azure 基础结构提供名称解析。 除了支持面向 Internet 的 DNS 域之外，Azure DNS 还支持专用 DNS 区域。

Azure 专用 DNS 提供可靠、安全的 DNS 服务来管理和解析虚拟网络中的域名，无需添加自定义 DNS 解决方案。 借助专用 DNS 区域，可以使用自定义域名而不使用当前可用的由 Azure 提供的名称。 使用自定义域名可帮助你定制虚拟网络体系结构以便最好地满足组织需求。 它在虚拟网络内以及在虚拟网络之间针对虚拟机 (VM) 提供名称解析。 除此之外，还可以通过水平分割视图配置区域名称，从而允许专用和公用 DNS 区域共享名称。

若要从虚拟网络解析专用 DNS 区域的记录，必须将虚拟网络与该区域链接。 链接的虚拟网络具有完全访问权限，可以解析在专用区域中发布的所有 DNS 记录。 此外，还可以在虚拟网络链接上启用自动注册。 如果在虚拟网络链接上启用了自动注册，则该虚拟网络上的虚拟机的 DNS 记录将在专用区域中注册。 启用自动注册后，Azure DNS 还会在创建虚拟机、更改其 IP 地址或删除虚拟机时更新区域记录。

![DNS 概述](./media/private-dns-overview/scenario.png)

> [!NOTE]
> 最好不要将 .local 域用于专用 DNS 区域  。 并非所有操作系统都支持此功能。

## <a name="benefits"></a>优点

Azure 专用 DNS 具有以下优势：

* **无需使用自定义 DNS 解决方案**。 以前，许多客户创建了自定义 DNS 解决方案来管理其虚拟网络。 现可使用本机 Azure 基础结构管理 DNS 区域，这解除了创建和管理自定义 DNS 解决方案的负担。

* **使用所有常见的 DNS 记录类型**。 Azure DNS 支持 A、AAAA、CNAME、MX、PTR、SOA、SRV 和 TXT 记录。

* **自动化主机名记录管理**。 除了承载自定义 DNS 记录之外，Azure 还会自动维护指定虚拟网络中的 VM 的主机名记录。 在此场景中，可以优化所使用的域名，不需要创建自定义 DNS 解决方案或修改应用程序。

* **虚拟网络之间的主机名解析**。 不同于 Azure 提供的主机名，专用 DNS 区域可以在虚拟网络之间共享。 此功能简化了跨网络和服务发现方案，例如，虚拟网络对等互连。

* **熟悉的工具和用户体验**。 为了降低学习难度，此服务使用成熟的 Azure DNS 工具（Azure 门户、Azure PowerShell、Azure CLI、Azure 资源管理器模板和 REST API）。

* **水平分割 DNS 支持**。 借助 Azure DNS，可以使用相同的名称创建在虚拟网络内与在公共 Internet 内分别解析为不同结果的区域。 水平分割 DNS 的典型方案是提供一个专用服务版本以在虚拟网络内部使用。

* **在所有 Azure 区域中可用**。 Azure 公有云中的所有 Azure 区域均已推出 Azure DNS 专用区域功能。

## <a name="capabilities"></a>功能

Azure DNS 提供以下功能：

* **从链接到专用区域并启用了自动注册的虚拟网络中，自动注册虚拟机**。 虚拟机将作为指向其专用 IP 地址的 A 记录注册（添加）到专用区域。 在启用了自动注册的虚拟网络链接中删除虚拟机后，Azure DNS 还会从所链接的专用区域中删除对应的 DNS 记录。

* **在链接到专用区域的虚拟网络之间支持正向 DNS 解析**。 对于跨虚拟网络 DNS 解析，不会明确要求虚拟网络彼此对等互连。 不过，对于其他场景（例如 HTTP 流量），你可能希望将虚拟网络对等互连。

* **在虚拟网络范围内支持反向 DNS 查找**。 对分配到专用区域的虚拟网络中的专用 IP 进行反向 DNS 查找会返回 FQDN，其中包括主机/记录名称以及作为后缀的区域名称。

## <a name="known-issues"></a>已知问题
以下项目是预览版中的已知 bug 和问题：
* 如果删除链接到专用 DNS 区域的虚拟网络，则不会删除指向专用 DNS 区域的链接。 如果重新创建具有相同名称和资源组的虚拟网络，并尝试再次将其链接到任何专用 DNS 区域，则链接将失败。 若要解决此问题，请在其他资源组中创建虚拟网络，或在同一资源组中使用其他名称。
* 如果将虚拟网络移动到另一个资源组或订阅，则不会更新指向专用 DNS 区域的链接。 已移动的虚拟网络的名称解析继续有效，但是查看专用 DNS 区域的虚拟网络链接时，将看到虚拟网络的旧 ARM ID。
* 目前，托管于阿联酋北部、阿联酋中部、南非西部、南非北部、加拿大东部、法国南部的链接虚拟网络可能会失败，你可能会遇到间歇性 DNS 解析问题。 


## <a name="other-considerations"></a>其他注意事项

Azure DNS 具有以下限制：

* 如果启用了自动注册 VM DNS 记录，则特定虚拟网络只能链接到一个专用区域。 但可以将多个虚拟网络链接到单个 DNS 区域。
* 反向 DNS 仅适用于链接虚拟网络中的专用 IP 空间
* 链接虚拟网络的专用 IP 的反向 DNS 返回“internal.cloudapp.net”，作为虚拟机的默认后缀。 对于链接到启用了自动注册的专用区域的虚拟网络，专用 IP 的反向 DNS 返回 2 个 FQDN，一个具有默认后缀 internal.cloudapp.net，另一个具有专用区域后缀  。
* 目前，条件转发不受本机支持。 若要启用 Azure 和本地网络之间的解析，请参阅 [VM 和角色实例的名称解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)。

 
## <a name="pricing"></a>定价

有关定价信息，请参阅 [Azure DNS 定价](https://azure.microsoft.com/pricing/details/dns/)。

## <a name="next-steps"></a>后续步骤

* 了解如何使用 [Azure PowerShell](./private-dns-getstarted-powershell.md) 或 [Azure CLI](./private-dns-getstarted-cli.md) 在 Azure DNS 中创建专用区域。

* 了解可以通过 Azure DNS 中的专用区域实现的一些常见[专用区域方案](./private-dns-scenarios.md)。

* 有关 Azure DNS 专用区域的一些常见问题和解答，包括对于某些类型的操作可以期待的特定行为，请参阅[专用 DNS 常见问题解答](./dns-faq-private.md)。

* 访问 [DNS 区域和记录概述](dns-zones-records.md)，了解 DNS 区域和记录。

* 了解 Azure 的一些其他关键[网络功能](../networking/networking-overview.md)。
