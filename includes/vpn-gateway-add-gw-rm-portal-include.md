---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/24/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 12d3f06ca349413231d69fc9de7b9d2f36645b23
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71273786"
---
1. 在门户左侧单击“+ 创建资源”，然后在搜索框中键入“虚拟网关”。 在搜索返回结果中找到“虚拟网络网关”，并单击该条目。 在“虚拟网络网关”页上，单击“创建”。 这会打开“创建虚拟网关”页。

   ![创建虚拟网关页字段](./media/vpn-gateway-add-gw-rm-portal-include/p2sgw.png "创建虚拟网关页字段")

   ![创建虚拟网关页字段](./media/vpn-gateway-add-gw-rm-portal-include/p2sgw2.png "创建虚拟网关页字段")
2. 在“创建虚拟网关”页中，填写虚拟网关的值。

   **项目详细信息**

   - **订阅**：从下拉列表中选择要使用的订阅。
   - **资源组**：在此页上选择虚拟网络时，会自动填充此设置。

   **实例详细信息**

   - **名称**：为网关命名。 为网关命名与为网关子网命名不同。 它是要创建的网关对象的名称。
   - **区域**：选择要在其中创建此资源的区域。 网关的区域必须与虚拟网络相同。
   - **网关类型**：选择“VPN”。 VPN 网关使用虚拟网络网关类型“VPN”。 
   - **VPN 类型**：选择为你的配置指定的 VPN 类型。 大多数配置要求基于路由的 VPN 类型。
   - **SKU**：从下拉列表中选择网关 SKU。 下拉列表中列出的 SKU 取决于选择的 VPN 类型。 有关网关 SKU 的详细信息，请参阅[网关 SKU](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku)。

      **虚拟网络**：选择要将此网关添加到其中的虚拟网络。

      **网关子网地址范围**：仅当所选虚拟网络没有网关子网时，才会显示此字段。 如果还没有网关子网，请填写范围。 如果可能，请将范围设为“/27”或更大（/26、/25 等）

   **公共 IP 地址**：此设置指定与 VPN 网关关联的公共 IP 地址对象。 创建 VPN 网关后，会将公共 IP 地址动态分配给此对象。 公共 IP 地址只在删除或重新创建网关时更改。 该地址不会因为 VPN 网关大小调整、重置或其他内部维护/升级而更改。

     - **公共 IP 地址**：让“新建”保持选中状态。
     - **公共 IP 地址名称**：在文本框中，键入公共 IP 地址实例的名称。
     - **分配**：VPN 网关仅支持“动态”。

   **主动-主动模式**：仅当要创建主动-主动网关配置时，才选择“启用主动-主动模式”。 否则，请将此设置保留未选择状态。

   让“配置 BGP ASN”保留取消选中状态，除非你的配置特别需要此设置。 如果确实需要此设置，则默认 ASN 为 65515，但可以更改此值。

3. 单击“查看 + 创建”以运行验证。 验证通过后，单击“创建”以部署 VPN 网关。 网关可能需要长达 45 分钟才能完全创建和部署。 可以在网关的“概述”页上查看部署状态。

创建网关后，可以通过在门户中查看虚拟网络，来查看已分配给网关的 IP 地址。 网关显示为连接的设备。
