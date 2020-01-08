---
title: 创建面向 Internet 的负载均衡器 - Azure 经典 CLI
titlesuffix: Azure Load Balancer
description: 了解如何使用 Azure 经典 CLI 在经典部署模型中创建面向 Internet 的负载均衡器
services: load-balancer
documentationcenter: na
author: genlin
manager: dcscontentpm
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 2d7f1971c6aff446f9bf8f983bd7859e831c1213
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71090929"
---
# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-the-azure-classic-cli"></a>开始使用 Azure 经典 CLI 创建面向 Internet 的负载均衡器（经典）

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-classic-ps.md)
> * [Azure 经典 CLI](../load-balancer/load-balancer-get-started-internet-classic-cli.md)
> * [Azure 云服务](../load-balancer/load-balancer-get-started-internet-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

> [!IMPORTANT]
> 在使用 Azure 资源之前，请务必了解 Azure 当前具有的两种部署模型：Azure 资源管理器和经典。 在使用任何 Azure 资源之前，请确保了解 [部署模型和工具](../azure-classic-rm.md) 。 可以通过单击本文顶部的选项卡来查看不同工具的文档。 本文介绍经典部署模型。 还可以[了解如何使用 Azure 资源管理器创建面向 Internet 的负载均衡器](load-balancer-get-started-internet-arm-ps.md)。

[!INCLUDE [requires-classic-cli](../../includes/contains-classic-cli-content.md)]

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="create-an-internet-facing-load-balancer-using-cli"></a>使用 CLI 创建面向 Internet 的负载均衡器

本指南演示如何基于上述方案创建 Internet 负载均衡器。

1. 如果从未使用过 Azure 经典 CLI，请参阅[安装和配置 Azure 经典 CLI](../cli-install-nodejs.md)，并按照说明操作，直到选择 Azure 帐户和订阅这一步。
2. 运行 **azure config mode** 命令以切换到经典模式，如下所示。

    ```azurecli
    azure config mode asm
    ```

    预期输出：

        info:    New mode is asm

## <a name="create-endpoint-and-load-balancer-set"></a>创建终结点和负载均衡器集

此方案假定已创建虚拟机“web1”和“web2”。
本指南会使用端口 80 作为公用端口和本地端口创建负载均衡器集。 还会在端口 80 上配置探测端口，并将负载均衡器集命名为“lbset”。

### <a name="step-1"></a>步骤 1

使用 `azure network vm endpoint create` 为虚拟机“web1”创建第一个终结点和负载均衡器集。

```azurecli
azure vm endpoint create web1 80 --local-port 80 --protocol tcp --probe-port 80 --load-balanced-set-name lbset
```

### <a name="step-2"></a>步骤 2

将第二个虚拟机“web2”添加到负载均衡器集。

```azurecli
azure vm endpoint create web2 80 --local-port 80 --protocol tcp --probe-port 80 --load-balanced-set-name lbset
```

### <a name="step-3"></a>步骤 3

使用 `azure vm show` 验证负载均衡器配置。

```azurecli
azure vm show web1
```

输出将为：

    data:    DNSName "contoso.cloudapp.net"
    data:    Location "East US"
    data:    VMName "web1"
    data:    IPAddress "10.0.0.5"
    data:    InstanceStatus "ReadyRole"
    data:    InstanceSize "Standard_D1"
    data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-2015
    6-en.us-127GB.vhd"
    data:    OSDisk hostCaching "ReadWrite"
    data:    OSDisk name "joaoma-1-web1-0-201509251804250879"
    data:    OSDisk mediaLink "https://XXXXXXXXXXXXXXX.blob.core.windows.
    /vhds/joaomatest-web1-2015-09-25.vhd"
    data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Se
    r-2012-R2-20150916-en.us-127GB.vhd"
    data:    OSDisk operatingSystem "Windows"
    data:    OSDisk iOType "Standard"
    data:    ReservedIPName ""
    data:    VirtualIPAddresses 0 address "XXXXXXXXXXXXXXXX"
    data:    VirtualIPAddresses 0 name "XXXXXXXXXXXXXXXXXXXX"
    data:    VirtualIPAddresses 0 isDnsProgrammed true
    data:    Network Endpoints 0 loadBalancedEndpointSetName "lbset"
    data:    Network Endpoints 0 localPort 80
    data:    Network Endpoints 0 name "tcp-80-80"
    data:    Network Endpoints 0 port 80
    data:    Network Endpoints 0 loadBalancerProbe port 80
    data:    Network Endpoints 0 loadBalancerProbe protocol "tcp"
    data:    Network Endpoints 0 loadBalancerProbe intervalInSeconds 15
    data:    Network Endpoints 0 loadBalancerProbe timeoutInSeconds 31
    data:    Network Endpoints 0 protocol "tcp"
    data:    Network Endpoints 0 virtualIPAddress "XXXXXXXXXXXX"
    data:    Network Endpoints 0 enableDirectServerReturn false
    data:    Network Endpoints 1 localPort 5986
    data:    Network Endpoints 1 name "PowerShell"
    data:    Network Endpoints 1 port 5986
    data:    Network Endpoints 1 protocol "tcp"
    data:    Network Endpoints 1 virtualIPAddress "XXXXXXXXXXXX"
    data:    Network Endpoints 1 enableDirectServerReturn false
    data:    Network Endpoints 2 localPort 3389
    data:    Network Endpoints 2 name "Remote Desktop"
    data:    Network Endpoints 2 port 58081
    info:    vm show command OK

## <a name="create-a-remote-desktop-endpoint-for-a-virtual-machine"></a>为虚拟机创建远程桌面终结点

可以使用 `azure vm endpoint create` 创建远程桌面终结点，将网络流量从公共端口转发到特定虚拟机的本地端口。

```azurecli
azure vm endpoint create web1 54580 -k 3389
```

## <a name="remove-virtual-machine-from-load-balancer"></a>从负载均衡器中删除虚拟机

必须从虚拟机中删除关联到负载均衡器集的终结点。 删除终结点后，虚拟机不再属于该负载均衡器集。

使用上面的示例，可以使用命令 `azure vm endpoint delete` 从负载均衡器“lbset”中删除为虚拟机“web1”创建的终结点。

```azurecli
azure vm endpoint delete web1 tcp-80-80
```

> [!NOTE]
> 可以使用命令 `azure vm endpoint --help` 浏览管理终结点的更多选项

## <a name="next-steps"></a>后续步骤

[开始配置内部负载均衡器](load-balancer-get-started-ilb-arm-ps.md)

[配置负载均衡器分发模式](load-balancer-distribution-mode.md)

[配置负载均衡器的空闲 TCP 超时设置](load-balancer-tcp-idle-timeout.md)
