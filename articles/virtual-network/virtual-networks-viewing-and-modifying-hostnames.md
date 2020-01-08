---
title: 查看和修改主机名 | Microsoft 文档
description: 如何查看和更改 Azure 虚拟机、Web 角色和辅助角色的主机名以进行名称解析
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: c668cd8e-4e43-4d05-acc3-db64fa78d828
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: cce248e2906f4a36737388e8cc7124b1bb19fbae
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058676"
---
# <a name="viewing-and-modifying-hostnames"></a>查看和修改主机名
若要允许通过主机名引用角色实例，必须在服务配置文件中为每个角色设置主机名的值。 可以通过将所需主机名添加到 **Role** 元素的 **vmName** 属性来执行该操作。 **vmName** 属性的值将用作每个角色实例的主机名的基本元素。 例如，如果 **vmName** 是 *webrole*，并且该角色有三个实例，则这些实例的主机名将为 *webrole0*、*webrole1* 和 *webrole2*。 无需在配置文件中为虚拟机指定主机名，因为虚拟机的主机名会基于虚拟机名称填充。 有关配置 Microsoft Azure 服务的详细信息，请参阅 [Azure Service 配置架构（.cscfg 文件）](https://msdn.microsoft.com/library/azure/ee758710.aspx)

## <a name="viewing-hostnames"></a>查看主机名
可以使用下列任一工具来查看云服务中虚拟机和角色实例的主机名。

### <a name="service-configuration-file"></a>服务配置文件
可以从 Azure 门户中服务的“**配置**”边栏选项卡下载已部署服务的服务配置文件。 然后，可以查找**角色名称**元素的 **vmName** 属性以查看主机名。 请记住，此主机名将用作每个角色实例的主机名的基本元素。 例如，如果 **vmName** 是 *webrole*，并且该角色有三个实例，则这些实例的主机名将为 *webrole0*、*webrole1* 和 *webrole2*。

### <a name="remote-desktop"></a>远程桌面
启用与你的虚拟机或角色实例的远程桌面 (Windows) 连接、Windows PowerShell 远程处理 (Windows) 连接或 SSH（Linux 和 Windows）连接后，你可以通过多种方式从活动的远程桌面连接查看主机名：

* 在命令提示符下或 SSH 终端键入主机名。
* 在命令提示符下键入 ipconfig /all（仅限 Windows）。
* 查看系统设置中的计算机名称（仅限 Windows）。

### <a name="azure-service-management-rest-api"></a>Azure 服务管理 REST API
从 REST 客户端，按照以下说明进行操作：

1. 确保有用于连接到 Azure 门户的客户端证书。 若要获取客户端证书，请执行[如何：下载和导入发布设置和订阅信息](https://msdn.microsoft.com/library/dn385850.aspx)中提供的步骤。 
2. 使用值 2013-11-01 设置名为 x-ms-version 的标头条目。
3. 按以下格式发送\/请求： https：/management.core.windows.net/\<subscrition-id-id\>/services/hostedservices/service-name\<服务名称\>？ embed-detail = true
4. 在 **HostName** 元素中查找每个 **RoleInstance** 元素。

> [!WARNING]
> 还可以通过以下方式从 REST 调用响应查看云服务的内部域后缀：查看 **InternalDnsSuffix** 元素，或通过在远程桌面会话 (Windows) 中的命令提示符下运行 ipconfig /all 或通过从 SSH 终端 (Linux) 运行 cat /etc/resolv.conf。
> 
> 

## <a name="modifying-a-hostname"></a>修改主机名
可以通过上传已修改的服务配置文件，或从远程桌面会话重命名计算机来修改任何虚拟机或角色实例的主机名。

## <a name="next-steps"></a>后续步骤
[名称解析 (DNS)](virtual-networks-name-resolution-for-vms-and-role-instances.md)

[Azure 服务配置架构 (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710.aspx)

[Azure 虚拟网络配置架构](https://go.microsoft.com/fwlink/?LinkId=248093)

[使用网络配置文件指定 DNS 设置](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)

