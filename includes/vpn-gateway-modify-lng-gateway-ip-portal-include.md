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
ms.openlocfilehash: 52084b065ef65a69a6691b6646d1e199f011910d
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172785"
---
### <a name="gwipnoconnection"></a> 修改本地网络网关 IP 地址 - 无网关连接

请使用此示例修改没有网关连接的本地网关。 修改此值时，还可同时修改地址前缀。

1. 在“本地网络网关”资源的“设置”  部分，单击“配置”  。
2. 在“IP 地址”  框中，修改 IP 地址。
3. 单击“保存”  保存这些设置。

### <a name="gwipwithconnection"></a>修改本地网络网关 IP 地址的具体步骤 - 现有网关连接

若要修改具有连接的本地网络网关，需先删除该连接。 删除连接后，可修改网关 IP 地址并重新创建一个新的连接。 此外可同时修改地址前缀。 这会导致 VPN 连接中断一段时间。 修改网关 IP 地址时，不需删除 VPN 网关。 只需删除连接。
 
#### <a name="1-remove-the-connection"></a>1.删除连接。

1. 在“本地网络网关”资源的“设置”  部分，单击“连接”  。
2. 在连接的行上单击“...”  ，然后单击“删除”  。
3. 单击**保存**以保存设置。

#### <a name="2-modify-the-ip-address"></a>2.修改 IP 地址。

此外可同时修改地址前缀。

1. 在“IP 地址”  框中，修改 IP 地址。
2. 单击“保存”  保存这些设置。

#### <a name="3-recreate-the-connection"></a>3.重新创建连接。

1. 导航到 VNet 的虚拟网络网关。 （不是本地网络网关。）
2. 在“虚拟网络网关”的“设置”  部分，单击“连接”  。
3. 单击“+ 添加”  打开“添加连接”  边栏选项卡。
4. 重新创建连接。
5. 单击“确定”创建连接。 