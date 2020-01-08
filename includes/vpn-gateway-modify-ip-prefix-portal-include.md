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
ms.openlocfilehash: 1199819d274590cc81d0234680f8765f9cc36c0a
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172786"
---
### <a name="noconnection"></a>修改本地网关 IP 地址前缀 - 无网关连接

#### <a name="to-add-additional-address-prefixes"></a>添加其他地址前缀：

1. 在“本地网络网关”资源的“设置”  部分，单击“配置”  。
2. 在“添加更多地址范围”  框中添加 IP 地址空间。
3. 单击**保存**以保存设置。

#### <a name="to-remove-address-prefixes"></a>删除地址前缀：

1. 在“本地网络网关”资源的“设置”  部分，单击“配置”  。
2. 在包含要删除的前缀的行上，单击“...”  。
3. 单击“删除”。 
4. 单击**保存**以保存设置。

### <a name="withconnection"></a>修改本地网关 IP 地址前缀 - 存在网关连接

如果有一个网关连接并且想要添加或删除包含在本地网关中的 IP 地址前缀，则需要按顺序执行以下步骤。 这会导致 VPN 连接中断一段时间。 修改 IP 地址前缀时，不需删除 VPN 网关。 只需删除连接。

#### <a name="1-remove-the-connection"></a>1.删除连接。

1. 在“本地网络网关”资源的“设置”  部分中，单击“连接”  。
2. 在用于每个连接的行上单击“...”  ，然后单击“删除”  。
3. 单击**保存**以保存设置。

#### <a name="2-modify-the-address-prefixes"></a>2.修改地址前缀。

添加其他地址前缀：

1. 在“本地网络网关”资源的“设置”  部分中，单击“配置”  。
2. 添加 IP 地址空间。
3. 单击**保存**以保存设置。

删除地址前缀：

1. 在“本地网络网关”资源的“设置”  部分，单击“配置”  。
2. 在含有要删除前缀的行上，单击“...”  。
3. 单击“删除”。 
4. 单击**保存**以保存设置。

#### <a name="3-recreate-the-connection"></a>3.重新创建连接。

1. 导航到 VNet 的虚拟网络网关。 （不是本地网络网关。）
2. 在“虚拟网络网关”的“设置”  部分，单击“连接”  。
3. 单击“+ 添加”  打开“添加连接”  边栏选项卡。
4. 重新创建连接。
5. 单击“确定”创建连接。 