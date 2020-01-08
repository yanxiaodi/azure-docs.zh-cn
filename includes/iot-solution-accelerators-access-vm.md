---
title: include 文件
description: include 文件
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 08/16/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: a58e408feadd10e6dbc9d6878b82a4d045918ea6
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/05/2019
ms.locfileid: "68781429"
---
## <a name="access-the-virtual-machine"></a>访问虚拟机

以下步骤使用 Azure Cloud Shell 中的 Azure CLI。 如果愿意, 可以在开发计算机上[安装 Azure CLI](/cli/azure/install-azure-cli) , 并在本地运行命令。

以下步骤演示如何配置 Azure 虚拟机，以允许 SSH 访问。 所示步骤假定为解决方案加速器选择的名称为 contoso-simulation - 将此值替换为部署名称：

1. 列出包含解决方案加速器资源的资源组的内容：

    ```azurecli-interactive
    az resource list -g contoso-simulation -o table
    ```

    记下虚拟机名称、公共 IP 地址和网络安全组 - 稍后需要使用这些值。

1. 更新网络安全组，以允许 SSH 访问。 以下命令假定网络安全组的名称为 contoso-simulation-nsg - 将此值替换为你的网络安全组的名称：

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name contoso-simulation-nsg -g contoso-simulation --access Allow -o table
    ```

    仅在测试和开发期间启用 SSH 访问。 如果启用 SSH，[应尽快再次禁用](https://docs.microsoft.com/azure/security/fundamentals/network-best-practices#disable-rdpssh-access-to-virtual-machines)。

1. 在虚拟机上将 azureuser 帐户的密码更新为你知道的密码。 运行以下命令时，选择自己的密码：

    ```azurecli-interactive
    az vm user update --name vm-vikxv --username azureuser --password YOURSECRETPASSWORD  -g contoso-simulation
    ```

1. 查找虚拟机的公共 IP 地址。 以下命令假定虚拟机的名称为 vm-vikxv - 将此值替换为之前记下的虚拟机的名称：

    ```azurecli-interactive
    az vm list-ip-addresses --name vm-vikxv -g contoso-simulation -o table
    ```

    记下虚拟机的公共 IP 地址。
