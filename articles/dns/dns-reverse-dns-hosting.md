---
title: 在 Azure DNS 中托管反向 DNS 查找区域 | Microsoft Docs
description: 了解如何使用 Azure DNS 托管 IP 范围的反向 DNS 查找区域
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: victorh
ms.openlocfilehash: cb2f04c692d4b5f385a89ba6a3071c20ef1bdf21
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66143672"
---
# <a name="host-reverse-dns-lookup-zones-in-azure-dns"></a>在 Azure DNS 中托管反向 DNS 查找区域

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

本文介绍如何在 Azure DNS 中托管所分配的 IP 范围的反向 DNS 查找区域。 必须将反向查找区域表示的 IP 范围分配给组织（通常通过 ISP 执行）。

若要为分配给 Azure 服务的 Azure 拥有的 IP 地址配置反向 DNS，请参阅[为 Azure 中托管的服务配置反向 DNS](dns-reverse-dns-for-azure-services.md)。

阅读本文之前，应已熟悉此[反向 DNS 和 Azure 支持概述](dns-reverse-dns-overview.md)。

本文将逐步指导用户使用 Azure 门户、Azure PowerShell、Azure 经典 CLI 或 Azure CLI 创建自己的第一个 DNS 区域和记录。

## <a name="create-a-reverse-lookup-dns-zone"></a>创建反向查找 DNS 区域

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在“中心”菜单上，单击“新建” > “网络”，然后单击“DNS 区域”。    

   ![“DNS 区域”选项](./media/dns-reverse-dns-hosting/figure1.png)

1. 在“创建 DNS 区域”窗格中，命名 DNS 区域。  IPv4 和 IPv6 前缀的区域名称不同。 使用有关 [IPv4](#ipv4) 或 [IPv6](#ipv6) 的说明为区域命名。 完成后，选择“创建”以创建区域。 

### <a name="ipv4"></a>IPv4

IPv4 反向查找区域的名称基于其所代表的 IP 范围。 应采用以下格式：`<IPv4 network prefix in reverse order>.in-addr.arpa`。 有关示例，请参阅[反向 DNS 和 Azure 支持概述](dns-reverse-dns-overview.md#ipv4)。

> [!NOTE]
> 在 Azure DNS 中创建无类别反向 DNS 查找区域时，区域名称中必须使用连字符 (`-`) 而不是正斜杠 (`/`)。
>
> 例如，对于 IP 范围 192.0.2.128/26，区域名称必须为 `128-26.2.0.192.in-addr.arpa`，而不是 `128/26.2.0.192.in-addr.arpa`。
>
> 虽然 DNS 标准支持这两种形式，但 Azure DNS 中不支持包含正斜杠 (`/`) 字符的 DNS 区域名称。

以下示例演示如何通过 Azure 门户在 Azure DNS 中创建名为 `2.0.192.in-addr.arpa` 的“类 C”的反向 DNS 区域：

 ![“创建 DNS 区域”窗格，已填写其中的输入框](./media/dns-reverse-dns-hosting/figure2.png)

“资源组位置”定义资源组的位置。  它对 DNS 区域没有影响。 DNS 区域位置始终是“全局”，并且不会显示。

以下示例演示如何通过 Azure PowerShell 和 Azure CLI 完成此任务。

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsZone -Name 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Azure 经典 CLI

```azurecli
azure network dns zone create MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns zone create -g MyResourceGroup -n 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

IPv6 反向查找区域的名称应采用以下格式：`<IPv6 network prefix in reverse order>.ip6.arpa`。  有关示例，请参阅[反向 DNS 和 Azure 支持概述](dns-reverse-dns-overview.md#ipv6)。


下面的示例演示如何通过 Azure 门户在 Azure DNS 中创建名为 `0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa` 的 IPv6 反向 DNS 查找区域：

 ![“创建 DNS 区域”窗格，已填写其中的输入框](./media/dns-reverse-dns-hosting/figure3.png)

“资源组位置”定义资源组的位置。  它对 DNS 区域没有影响。 DNS 区域位置始终是“全局”，并且不会显示。

以下示例演示如何通过 Azure PowerShell 和 Azure CLI 完成此任务。

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsZone -Name 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Azure 经典 CLI

```azurecli
azure network dns zone create MyResourceGroup 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns zone create -g MyResourceGroup -n 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="delegate-a-reverse-dns-lookup-zone"></a>委托反向 DNS 查找区域

创建反向 DNS 查找区域后，必须确保从父区域委托该区域。 DNS 委托使 DNS 名称解析过程能够找到托管反向 DNS 查找区域的名称服务器。 如此，这些名称服务器便可响应针对地址范围内 IP 地址的 DNS 反向查询。

对于前向查找区域，[将域委托给 Azure DNS](dns-delegate-domain-azure-dns.md) 中介绍了相关 DNS 区域委托过程。 反向查找区域的委托方式与之相同。 唯一的区别是，需使用提供 IP 范围的 ISP（而不是域名注册机构）配置名称服务器。

## <a name="create-a-dns-ptr-record"></a>创建 DNS PTR 记录

### <a name="ipv4"></a>IPv4

以下示例指导用户在 Azure DNS 的反向 DNS 区域中创建 PTR 记录。 若要了解其他记录类型并修改现有记录，请参阅[使用 Azure 门户管理 DNS 记录和记录集](dns-operations-recordsets-portal.md)。

1. 在“DNS 区域”  窗格顶部，选择“+ 记录集”  打开“添加记录集”  窗格。

   ![用于创建记录集的按钮](./media/dns-reverse-dns-hosting/figure4.png)

1. PTR 记录的记录集名称需为以倒序排序的 IPv4 地址的其余部分。 

   在此示例中，已填充前三个八进制数，作为区域名称 (.2.0.192) 的一部分。 因此，“名称”框中提供仅最后一个八进制数。  例如，对于 IP 地址为 192.0.2.15 的资源，可将记录集命名为 **15**。  
1. 对于“类型”，请选择“PTR”。    
1. 在“域名”字段中，输入使用该 IP 的资源的完全限定域名 (FQDN)  。
1. 单击窗格底部的“确定”创建 DNS 记录。 

   ![“添加记录集”窗格，已填写其中的输入框](./media/dns-reverse-dns-hosting/figure5.png)

以下示例演示如何使用 PowerShell 或 Azure CLI 完成此任务。

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsRecordSet -Name 15 -RecordType PTR -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "dc1.contoso.com")
```
#### <a name="azure-classic-cli"></a>Azure 经典 CLI

```azurecli
azure network dns record-set add-record MyResourceGroup 2.0.192.in-addr.arpa 15 PTR --ptrdname dc1.contoso.com  
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
    az network dns record-set ptr add-record -g MyResourceGroup -z 2.0.192.in-addr.arpa -n 15 --ptrdname dc1.contoso.com
```

### <a name="ipv6"></a>IPv6

以下示例将指导用户创建新的 PTR 记录。 若要了解其他记录类型并修改现有记录，请参阅[使用 Azure 门户管理 DNS 记录和记录集](dns-operations-recordsets-portal.md)。

1. 在“DNS 区域”  窗格顶部，选择“+ 记录集”  打开“添加记录集”  窗格。

   ![用于创建记录集的按钮](./media/dns-reverse-dns-hosting/figure6.png)

2. PTR 记录的记录集名称需为以倒序排序的 IPv6 地址的其余部分。 不能包含任何零压缩。 

   在此示例中，已填充 IPv6 的前 64 位，作为区域名称的一部分 (0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa)。 因此，“名称”框中仅提供了最后 64 位。  以倒序顺序输入 IP 地址的后 64 位，使用句点作为十六进制数之间的分隔符。 例如，对于 IP 地址为 2001:0db8:abdc:0000:f524:10bc:1af9:405e 的资源，可将记录集命名为 **e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f**。  
3. 对于“类型”，请选择“PTR”。    
4. 在“域名”字段中，输入使用该 IP 的资源的 FQDN  。
5. 单击窗格底部的“确定”创建 DNS 记录。 

![“添加记录集”窗格，已填写其中的输入框](./media/dns-reverse-dns-hosting/figure7.png)

以下示例演示如何使用 PowerShell 或 Azure CLI 完成此任务。

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsRecordSet -Name "e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f" -RecordType PTR -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "dc2.contoso.com")
```

#### <a name="azure-classic-cli"></a>Azure 经典 CLI

```
azure network dns record-set add-record MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f PTR --ptrdname dc2.contoso.com 
```
 
#### <a name="azure-cli"></a>Azure CLI

```azurecli
    az network dns record-set ptr add-record -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -n e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f --ptrdname dc2.contoso.com
```

## <a name="view-records"></a>查看记录

若要查看创建的记录，请在 Azure 门户中浏览到 DNS 区域。 在“DNS 区域”窗格的下半部分，可以看到 DNS 区域的记录。  应会看到默认的 NS 和 SOA 记录以及创建的新记录。 NS 和 SOA 记录已在每个区域中创建。 

### <a name="ipv4"></a>IPv4

“DNS 区域”窗格显示 IPv4 PTR 记录： 

![“DNS 区域”窗格，其中包含 IPv4 记录](./media/dns-reverse-dns-hosting/figure8.png)

以下示例演示如何使用 PowerShell 或 Azure CLI 查看 PTR 记录。

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzDnsRecordSet -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Azure 经典 CLI

```azurecli
    azure network dns record-set list MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
    azure network dns record-set list -g MyResourceGroup -z 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

“DNS 区域”窗格显示 IPv6 PTR 记录： 

![“DNS 区域”窗格，其中包含 IPv6 记录](./media/dns-reverse-dns-hosting/figure9.png)

以下示例演示如何使用 PowerShell 或 Azure CLI 查看记录。

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzDnsRecordSet -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Azure 经典 CLI

```azurecli
    azure network dns record-set list MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
    azure network dns record-set list -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="faq"></a>常见问题解答

### <a name="can-i-host-reverse-dns-lookup-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>是否可以在 Azure DNS 上托管 ISP 分配的 IP 块的反向 DNS 查找区域？

是的。 完全支持在 Azure DNS 托管自己的 IP 范围的反向查找 (ARPA) 区域。

按照本文所述步骤在 Azure DNS 中创建反向查找区域，然后使用 ISP [委托区域](dns-domain-delegation.md)。 然后，便可以像处理其他记录类型一样，管理每个反向查找的 PTR 记录。

### <a name="how-much-does-hosting-my-reverse-dns-lookup-zone-cost"></a>托管反向 DNS 查找区域的成本是多少？

在 Azure DNS 中托管 ISP 分配的 IP 块的反向 DNS 查找区域根据[标准 Azure DNS 费率](https://azure.microsoft.com/pricing/details/dns/)收费。

### <a name="can-i-host-reverse-dns-lookup-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>是否可以在 Azure DNS 中托管 IPv4 和 IPv6 地址的反向 DNS 查找区域？

是的。 本文介绍如何在 Azure DNS 中创建 IPv4 和 IPv6 的反向 DNS 查找区域。

### <a name="can-i-import-an-existing-reverse-dns-lookup-zone"></a>是否可以导入现有的反向 DNS 查找区域？

是的。 可以使用 Azure CLI 将现有的 DNS 区域导入 Azure DNS。 此方法适用于正向查找区域和反向查找区域。

有关详细信息，请参阅[使用 Azure CLI 导入和导出 DNS 区域文件](dns-import-export.md)。

## <a name="next-steps"></a>后续步骤

有关反向 DNS 的详细信息，请参阅[反向 DNS 查找](https://en.wikipedia.org/wiki/Reverse_DNS_lookup)。
<br>
了解如何[管理 Azure 服务的反向 DNS 记录](dns-reverse-dns-for-azure-services.md)。
