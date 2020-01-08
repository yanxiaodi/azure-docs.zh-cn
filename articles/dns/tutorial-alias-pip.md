---
title: 教程 - 创建表示 Azure 公共 IP 地址的 Azure DNS 别名记录。
description: 本教程介绍如何配置表示 Azure 公共 IP 地址的 Azure DNS 别名记录。
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 7dcbfdaf00b0e628541cfd1a3b79df8cf8334ed3
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57536872"
---
# <a name="tutorial-configure-an-alias-record-to-refer-to-an-azure-public-ip-address"></a>教程：配置表示 Azure 公共 IP 地址的别名记录 

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建网络基础结构。
> * 创建 Web 服务器虚拟机。
> * 创建别名记录。
> * 测试别名记录。


如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件
必须具有可用于在 Azure DNS 中托管以供测试的域名。 必须能够完全控制此域。 完全控制包括能够为域设置名称服务器 (NS) 记录。

有关在 Azure DNS 中托管域的说明，请参阅[教程：在 Azure DNS 中托管域](dns-delegate-domain-azure-dns.md)。

本教程中使用的示例域为 contoso.com，但请使用自己的域名。

## <a name="create-the-network-infrastructure"></a>创建网络基础结构
首先，创建要放置 Web 服务器的虚拟网络和子网。
1. 通过 https://portal.azure.com 登录到 Azure 门户。
2. 在门户的左上角，选择“创建资源”。 在搜索框中输入“资源组”，然后创建名为 **RG-DNS-Alias-pip** 的资源组。
3. 选择“创建资源” > “网络” > “虚拟网络”。
4. 创建名为“VNet-Server”的虚拟网络。 将其放在 **RG-DNS-Alias-pip** 资源组中，然后将子网命名为 **SN-Web**。

## <a name="create-a-web-server-virtual-machine"></a>创建 Web 服务器虚拟机
1. 选择“创建资源” > “Windows Server 2016 VM”。
2. 输入名称“Web-01”，然后将 VM 放在“RG-DNS-Alias-TM”资源组中。 输入用户名和密码，然后选择“确定”。
3. 对于“大小”，请选择具有 8 GB RAM 的 SKU。
4. 对于“设置”，请选择“VNet-Servers”虚拟网络和“SN-Web”子网。 对于公共入站端口，请选择“HTTP” > “HTTPS” > “RDP (3389)”，然后选择“确定”。
5. 在“摘要”页中，选择“创建”。

此过程需要几分钟才能完成。

### <a name="install-iis"></a>安装 IIS

在 **Web-01** 上安装 IIS

1. 连接到 Web-01 并登录。
2. 在“服务器管理器”仪表板上，选择“添加角色和功能”。
3. 选择“下一步”三次。 在“服务器角色”页上，选择“Web 服务器(IIS)”。
4. 选择“添加功能”，然后选择“下一步”。
5. 选择“下一步”四次，然后选择“安装”。 此过程需要几分钟才能完成。
6. 安装完成后，选择“关闭”。
7. 打开 Web 浏览器。 浏览到“localhost”以验证是否显示默认 IIS 网页。

## <a name="create-an-alias-record"></a>创建别名记录

创建指向公共 IP 地址的别名记录。

1. 选择 Azure DNS 区域以打开该区域。
2. 选择“记录集”。
3. 在“名称”文本框中选择“web01”。
4. 将“类型”保留为 A 记录。
5. 选择“别名记录集”复选框。
6. 选择“选择 Azure 服务”，然后选择“Web-01-ip”公共 IP 地址。

## <a name="test-the-alias-record"></a>测试别名记录

1. 在“RG-DNS-Alias-pip”资源组中，选择“Web-01”虚拟机。 记录公共 IP 地址。
1. 在 Web 浏览器中，浏览到 Web01-01 虚拟机的完全限定域名。 例如，**web01.contoso.com**。 此时会看到 IIS 默认网页。
2. 关闭 Web 浏览器。
3. 停止“Web-01” 虚拟机，然后将其重新启动。
4. 虚拟机重启后，记录虚拟机的新公共 IP 地址。
5. 打开新的浏览器。 再次浏览到 Web01-01 虚拟机的完全限定域名。 例如，**web01.contoso.com**。

此过程成功是因为你使用了指向公共 IP 地址资源的别名记录，而非标准的 A 记录。

## <a name="clean-up-resources"></a>清理资源

不再需要为本教程创建的资源时，请删除 **RG-DNS-Alias-pip** 资源组。


## <a name="next-steps"></a>后续步骤

在本教程中，你已创建表示 Azure 公共 IP 地址的别名记录。 若要了解 Azure DNS 和 Web 应用，请继续学习适用于 Web 应用的教程。

> [!div class="nextstepaction"]
> [在自定义域中为 web 应用创建 DNS 记录](./dns-web-sites-custom-domain.md)
