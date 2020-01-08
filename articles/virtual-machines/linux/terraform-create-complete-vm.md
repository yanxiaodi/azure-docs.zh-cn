---
title: 在 Azure 中使用 Terraform 创建完整的 Linux VM | Microsoft Docs
description: 了解如何在 Azure 中使用 Terraform 创建和管理完整的 Linux 虚拟机环境
services: virtual-machines-linux
documentationcenter: virtual-machines
author: tomarchermsft
manager: gwallace
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/20/2019
ms.author: tarcher
ms.openlocfilehash: b9e379907f28c0d8698eb11aacb88970cf8d6dc4
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173848"
---
# <a name="create-a-complete-linux-virtual-machine-infrastructure-in-azure-with-terraform"></a>在 Azure 中使用 Terraform 创建完整的 Linux 虚拟机基础结构

使用 Terraform 可以在 Azure 中定义和创建完整的基础结构部署。 以用户可读格式生成 Terraform 模板，用于以一致且可重现的方式创建和配置 Azure 资源。 本文介绍了如何使用 Terraform 创建完整的 Linux 环境和支持资源。 另外，还可以了解如何[安装和配置 Terraform](terraform-install-configure.md)。


## <a name="create-azure-connection-and-resource-group"></a>创建 Azure 连接和资源组

我们来详细地了解 Terraform 模板的每个部分。 还可以看到完整版本的 [Terraform 模板](#complete-terraform-script)，可以复制并粘贴这些模板。

`provider` 部分告知 Terraform 使用 Azure 提供程序。 若要获取 subscription_id、client_id、client_secret 和 *tenant_id* 的值，请参阅[安装和配置 Terraform](terraform-install-configure.md)。 

> [!TIP]
> 如果要为值创建环境变量，或要使用 [Azure Cloud Shell Bash 体验](/azure/cloud-shell/overview)，则无需在此节中包括变量声明。

```hcl
provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

以下部分在 `eastus` 位置创建名为 `myResourceGroup` 的资源组：

```hcl
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags = {
        environment = "Terraform Demo"
    }
}
```

在其他部分中，可以使用 ${azurerm_resource_group.myterraformgroup.name} 引用该资源组。

## <a name="create-virtual-network"></a>创建虚拟网络
以下部分在 10.0.0.0/16 地址空间中创建名为 myVnet 的虚拟网络：

```hcl
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"

    tags = {
        environment = "Terraform Demo"
    }
}
```

以下部分在 myVnet 虚拟网络中创建名为 mySubnet 的子网：

```hcl
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = "${azurerm_resource_group.myterraformgroup.name}"
    virtual_network_name = "${azurerm_virtual_network.myterraformnetwork.name}"
    address_prefix       = "10.0.2.0/24"
}
```


## <a name="create-public-ip-address"></a>创建公共 IP 地址
若要通过 Internet 访问资源，请创建公共 IP 地址并将其分配到 VM。 以下部分创建名为 myPublicIP 的公共 IP 地址：

```hcl
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = "${azurerm_resource_group.myterraformgroup.name}"
    allocation_method            = "Dynamic"

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-network-security-group"></a>创建网络安全组
网络安全组控制传入和传出 VM 的网络流量。 以下部分创建名为 myNetworkSecurityGroup 的网络安全组并定义允许 TCP 端口 22 上的 SSH 流量的规则：

```hcl
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"
    
    security_rule {
        name                       = "SSH"
        priority                   = 1001
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "22"
        source_address_prefix      = "*"
        destination_address_prefix = "*"
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-network-interface-card"></a>创建虚拟网络接口卡
虚拟网络接口卡 (NIC) 将 VM 连接到规定的虚拟网络、公共 IP 地址和网络安全组。 Terraform 模板的以下部分创建名为“myNIC”的虚拟 NIC，并连接到已创建的虚拟网络资源：

```hcl
resource "azurerm_network_interface" "myterraformnic" {
    name                = "myNIC"
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"
    network_security_group_id = "${azurerm_network_security_group.myterraformnsg.id}"

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = "${azurerm_subnet.myterraformsubnet.id}"
        private_ip_address_allocation = "Dynamic"
        public_ip_address_id          = "${azurerm_public_ip.myterraformpublicip.id}"
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-storage-account-for-diagnostics"></a>创建存储帐户以进行诊断
若要为 VM 存储启动诊断，需要一个存储帐户。 这些启动诊断可帮助你排查问题和监视 VM 状态。 你创建的存储帐户仅用于存储启动诊断数据。 由于每个存储帐户必须具有唯一名称，以下部分会生成一些随机文本：

```hcl
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = "${azurerm_resource_group.myterraformgroup.name}"
    }
    
    byte_length = 8
}
```

现在可以创建存储帐户了。 以下部分创建一个存储帐户，其名称基于上一步中生成的随机文本：

```hcl
resource "azurerm_storage_account" "mystorageaccount" {
    name                = "diag${random_id.randomId.hex}"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"
    location            = "eastus"
    account_replication_type = "LRS"
    account_tier = "Standard"

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-machine"></a>创建虚拟机

最后一步是创建 VM 并使用所有已创建的资源。 以下部分创建名为 myVM 的 VM 并附加名为 myNIC 的虚拟 NIC。 将使用最新的 Ubuntu 16.04 LTS 映像，并且在禁用密码身份验证的情况下创建名为 azureuser 的用户。

 ssh_keys 部分中提供了 SSH 密钥数据。 在 *key_data* 字段中提供有效的公共 SSH 密钥。

```hcl
resource "azurerm_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = "${azurerm_resource_group.myterraformgroup.name}"
    network_interface_ids = ["${azurerm_network_interface.myterraformnic.id}"]
    vm_size               = "Standard_DS1_v2"

    storage_os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Premium_LRS"
    }

    storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    os_profile {
        computer_name  = "myvm"
        admin_username = "azureuser"
    }

    os_profile_linux_config {
        disable_password_authentication = true
        ssh_keys {
            path     = "/home/azureuser/.ssh/authorized_keys"
            key_data = "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
        }
    }

    boot_diagnostics {
        enabled     = "true"
        storage_uri = "${azurerm_storage_account.mystorageaccount.primary_blob_endpoint}"
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```

## <a name="complete-terraform-script"></a>完成 Terraform 脚本

若要将所有这些部分组合在一起，并在操作中看到 Terraform，请创建名为 terraform_azure.tf 的文件并粘贴以下内容：

```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}

# Create a resource group if it doesn’t exist
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create virtual network
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create subnet
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = "${azurerm_resource_group.myterraformgroup.name}"
    virtual_network_name = "${azurerm_virtual_network.myterraformnetwork.name}"
    address_prefix       = "10.0.1.0/24"
}

# Create public IPs
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = "${azurerm_resource_group.myterraformgroup.name}"
    allocation_method            = "Dynamic"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create Network Security Group and rule
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"
    
    security_rule {
        name                       = "SSH"
        priority                   = 1001
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "22"
        source_address_prefix      = "*"
        destination_address_prefix = "*"
    }

    tags = {
        environment = "Terraform Demo"
    }
}

# Create network interface
resource "azurerm_network_interface" "myterraformnic" {
    name                      = "myNIC"
    location                  = "eastus"
    resource_group_name       = "${azurerm_resource_group.myterraformgroup.name}"
    network_security_group_id = "${azurerm_network_security_group.myterraformnsg.id}"

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = "${azurerm_subnet.myterraformsubnet.id}"
        private_ip_address_allocation = "Dynamic"
        public_ip_address_id          = "${azurerm_public_ip.myterraformpublicip.id}"
    }

    tags = {
        environment = "Terraform Demo"
    }
}

# Generate random text for a unique storage account name
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = "${azurerm_resource_group.myterraformgroup.name}"
    }
    
    byte_length = 8
}

# Create storage account for boot diagnostics
resource "azurerm_storage_account" "mystorageaccount" {
    name                        = "diag${random_id.randomId.hex}"
    resource_group_name         = "${azurerm_resource_group.myterraformgroup.name}"
    location                    = "eastus"
    account_tier                = "Standard"
    account_replication_type    = "LRS"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create virtual machine
resource "azurerm_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = "${azurerm_resource_group.myterraformgroup.name}"
    network_interface_ids = ["${azurerm_network_interface.myterraformnic.id}"]
    vm_size               = "Standard_DS1_v2"

    storage_os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Premium_LRS"
    }

    storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    os_profile {
        computer_name  = "myvm"
        admin_username = "azureuser"
    }

    os_profile_linux_config {
        disable_password_authentication = true
        ssh_keys {
            path     = "/home/azureuser/.ssh/authorized_keys"
            key_data = "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
        }
    }

    boot_diagnostics {
        enabled = "true"
        storage_uri = "${azurerm_storage_account.mystorageaccount.primary_blob_endpoint}"
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="build-and-deploy-the-infrastructure"></a>构建并部署基础结构
创建 Terraform 模板后，第一步是初始化 Terraform。 此步骤可确保 Terraform 具有你要在 Azure 中生成模板的所有必备组件。

```bash
terraform init
```

下一步是让 Terraform 检查并验证模板。 此步骤将请求的资源与 Terraform 保存的状态信息进行比较，然后输出计划的执行。 资源不是在 Azure 中创建的。

```bash
terraform plan
```

执行上述命令后，应会出现类似于以下屏幕的内容：

```console
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


...

Note: You didn’t specify an “-out” parameter to save this plan, so when
“apply” is called, Terraform can’t guarantee this is what will execute.
  + azurerm_resource_group.myterraform
      <snip>
  + azurerm_virtual_network.myterraformnetwork
      <snip>
  + azurerm_network_interface.myterraformnic
      <snip>
  + azurerm_network_security_group.myterraformnsg
      <snip>
  + azurerm_public_ip.myterraformpublicip
      <snip>
  + azurerm_subnet.myterraformsubnet
      <snip>
  + azurerm_virtual_machine.myterraformvm
      <snip>
Plan: 7 to add, 0 to change, 0 to destroy.
```

如果所有内容正确无误，并且你已准备好在 Azure 中构建基础结构，请在 Terraform 中应用模板：

```bash
terraform apply
```

Terraform 完成后，VM 基础结构即已准备完毕。 可使用 [az vm show](/cli/azure/vm) 获取 VM 的 公共 IP 地址：

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
```

然后，可以通过 SSH 连接到 VM：

```bash
ssh azureuser@<publicIps>
```

## <a name="next-steps"></a>后续步骤
现已使用 Terraform 在 Azure 中创建了基本基础结构。 有关更复杂的方案（包括使用负载均衡器和虚拟机规模集的示例），请参阅众多的[适用于 Azure 的 Terraform 示例](https://github.com/hashicorp/terraform/tree/master/examples)。 有关受支持 Azure 提供程序的最新列表，请参阅 [Terraform 文档](https://www.terraform.io/docs/providers/azurerm/index.html)。
