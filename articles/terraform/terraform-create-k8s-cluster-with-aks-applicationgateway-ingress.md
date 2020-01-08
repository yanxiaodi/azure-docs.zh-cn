---
title: 创建包含 Azure Kubernetes 服务 (AKS) 和用作入口控制器的应用程序网关的 Kubernetes 群集
description: 演示如何创建包含 Azure Kubernetes 服务和用作入口控制器的应用程序网关的 Kubernetes 群集的教程
services: terraform
ms.service: azure
keywords: terraform, devops, 虚拟机, azure, kubernetes, 入口, 应用程序网关
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2019
ms.openlocfilehash: 0373b254a900fd34232bb6863c93802fa7b51aab
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2019
ms.locfileid: "71169962"
---
# <a name="create-a-kubernetes-cluster-with-application-gateway-ingress-controller-using-azure-kubernetes-service-and-terraform"></a>使用 Azure Kubernetes 服务和 Terraform 创建包含应用程序网关入口控制器的 Kubernetes 群集
[Azure Kubernetes 服务 (AKS)](/azure/aks/) 管理托管的 Kubernetes 环境。 使用 AKS 可以快速轻松地部署和管理容器化应用程序，而无需具备容器业务流程方面的专业知识。 它还通过按需预配、升级和缩放资源，消除了正在进行的操作和维护的负担，而无需使应用程序脱机。

入口控制器是一个软件片段，为 Kubernetes 服务提供反向代理、可配置的流量路由和 TLS 终止。 Kubernetes 入口资源用于配置各个 Kubernetes 服务的入口规则和路由。 借助入口控制器和入口规则，可以使用单个 IP 地址将流量路由到 Kubernetes 群集中的多个服务。 上述所有功能由 Azure [应用程序网关](/azure/Application-Gateway/)提供，因此，应用程序网关是 Azure 上的 Kubernetes 的理想入口控制器。 

本教程介绍如何执行创建使用 AKS 并包含用作入口控制器的应用程序网关的 [Kubernetes](https://www.redhat.com/en/topics/containers/what-is-kubernetes) 群集的任务：

> [!div class="checklist"]
> * 使用 HCL（HashiCorp 语言）定义 Kubernetes 群集
> * 使用 Terraform 创建应用程序网关资源
> * 使用 Terraform 和 AKS 创建 Kubernetes 群集
> * 使用 kubectl 工具测试 Kubernetes 群集的可用性

## <a name="prerequisites"></a>先决条件

- **Azure 订阅**：如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

- **配置 Terraform**：遵循[安装 Terraform 并配置对 Azure 的访问权限](/azure/virtual-machines/linux/terraform-install-configure)一文中的指导

- **Azure 服务主体**：遵循[使用 Azure CLI 创建 Azure 服务主体](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)一文的“创建服务主体”部分中的指导  。 记下 appId、displayName 和 password 的值。
  - 运行以下命令并记下服务主体的对象 ID

    ```azurecli
    az ad sp list --display-name <displayName>
    ```

## <a name="create-the-directory-structure"></a>创建目录结构
首先，创建包含 Terraform 配置文件的目录用于练习。

1. 浏览到 [Azure 门户](https://portal.azure.com)。

1. 打开 [Azure Cloud Shell](/azure/cloud-shell/overview)。 如果事先未选择环境，请选择“Bash”作为环境。 

    ![Cloud Shell 提示符](./media/terraform-k8s-cluster-appgw-with-tf-aks/azure-portal-cloud-shell-button-min.png)

1. 切换到 `clouddrive` 目录。

    ```bash
    cd clouddrive
    ```

1. 创建名为 `terraform-aks-k8s` 的目录。

    ```bash
    mkdir terraform-aks-appgw-ingress
    ```

1. 将目录切换到新目录：

    ```bash
    cd terraform-aks-appgw-ingress
    ```

## <a name="declare-the-azure-provider"></a>声明 Azure 提供程序
创建声明 Azure 提供程序的 Terraform 配置文件。

1. 在 Cloud Shell 中，创建名为 `main.tf` 的文件。

    ```bash
    vi main.tf
    ```

1. 按 I 键进入插入模式。

1. 在编辑器中粘贴以下代码：

    ```hcl
    provider "azurerm" {
        version = "~>1.18"
    }

    terraform {
        backend "azurerm" {}
    }
    ```

1. 按 **Esc** 键退出插入模式。

1. 保存文件，然后输入以下命令退出 vi 编辑器：

    ```bash
    :wq
    ```

## <a name="define-input-variables"></a>定义输入变量
创建 Terraform 配置文件，用于列出此部署所需的所有变量。

1. 在 Cloud Shell 中，创建名为 `variables.tf` 的文件。

    ```bash
    vi variables.tf
    ```

1. 按 I 键进入插入模式。

1. 在编辑器中粘贴以下代码：
    
    ```hcl
    variable "resource_group_name" {
      description = "Name of the resource group already created."
    }
    
    variable "location" {
      description = "Location of the cluster."
    }
    
    variable "aks_service_principal_app_id" {
      description = "Application ID/Client ID  of the service principal. Used by AKS to manage AKS related resources on Azure like vms, subnets."
    }
    
    variable "aks_service_principal_client_secret" {
      description = "Secret of the service principal. Used by AKS to manage Azure."
    }
    
    variable "aks_service_principal_object_id" {
      description = "Object ID of the service principal."
    }
    
    variable "virtual_network_name" {
      description = "Virtual network name"
      default     = "aksVirtualNetwork"
    }
    
    variable "virtual_network_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.0.0.0/8"
    }
    
    variable "aks_subnet_name" {
      description = "AKS Subnet Name."
      default     = "kubesubnet"
    }
    
    variable "aks_subnet_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.0.0.0/16"
    }
    
    variable "app_gateway_subnet_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.1.0.0/16"
    }
    
    variable "app_gateway_name" {
      description = "Name of the Application Gateway."
      default = "ApplicationGateway1"
    }
    
    variable "app_gateway_sku" {
      description = "Name of the Application Gateway SKU."
      default = "Standard_v2"
    }
    
    
    variable "app_gateway_tier" {
      description = "Tier of the Application Gateway SKU."
      default = "Standard_v2"
    }
    
    
    variable "aks_name" {
      description = "Name of the AKS cluster."
      default     = "aks-cluster1"
    }
    variable "aks_dns_prefix" {
      description = "Optional DNS prefix to use with hosted Kubernetes API server FQDN."
      default     = "aks"
    }
    
    
    variable "aks_agent_os_disk_size" {
      description = "Disk size (in GB) to provision for each of the agent pool nodes. This value ranges from 0 to 1023. Specifying 0 will apply the default disk size for that agentVMSize."
      default     = 40
    }
    
    variable "aks_agent_count" {
      description = "The number of agent nodes for the cluster."
      default     = 3
    }
    
    variable "aks_agent_vm_size" {
      description = "The size of the Virtual Machine."
      default     = "Standard_D3_v2"
    }
    
    variable "kubernetes_version" {
      description = "The version of Kubernetes."
      default     = "1.11.5"
    }
    
    variable "aks_service_cidr" {
      description = "A CIDR notation IP range from which to assign service cluster IPs."
      default     = "10.0.0.0/16"
    }
    
    variable "aks_dns_service_ip" {
      description = "Containers DNS server IP address."
      default     = "10.0.0.10"
    }
    
    variable "aks_docker_bridge_cidr" {
      description = "A CIDR notation IP for Docker bridge."
      default     = "172.17.0.1/16"
    }
    
    variable "aks_enable_rbac" {
      description = "Enable RBAC on the AKS cluster. Defaults to false."
      default     = "false"
    }
    
    variable "vm_user_name" {
      description = "User name for the VM"
      default     = "vmuser1"
    }
    
    variable "public_ssh_key_path" {
      description = "Public key path for SSH."
      default     = "~/.ssh/id_rsa.pub"
    }
    
    variable "tags" {
      type = "map"
    
      default = {
        source = "terraform"
      }
    }
    ```

## <a name="define-the-resources"></a>定义资源 
创建 Terraform 配置文件，用于创建所有资源。 

1. 在 Cloud Shell 中，创建名为 `resources.tf` 的文件。

    ```bash
    vi resources.tf
    ```

1. 按 I 键进入插入模式。

1. 在编辑器中粘贴以下代码块：

    a. 创建 locals 块，以重复使用计算的变量。

    ```hcl
    # # Locals block for hardcoded names. 
    locals {
        backend_address_pool_name      = "${azurerm_virtual_network.test.name}-beap"
        frontend_port_name             = "${azurerm_virtual_network.test.name}-feport"
        frontend_ip_configuration_name = "${azurerm_virtual_network.test.name}-feip"
        http_setting_name              = "${azurerm_virtual_network.test.name}-be-htst"
        listener_name                  = "${azurerm_virtual_network.test.name}-httplstn"
        request_routing_rule_name      = "${azurerm_virtual_network.test.name}-rqrt"
        app_gateway_subnet_name = "appgwsubnet"
    }
    ```

    b. 创建资源组的数据源（新用户标识）。

    ```hcl
    data "azurerm_resource_group" "rg" {
      name = "${var.resource_group_name}"
    }
    
    # User Assigned Idntities 
    resource "azurerm_user_assigned_identity" "testIdentity" {
      resource_group_name = "${data.azurerm_resource_group.rg.name}"
      location            = "${data.azurerm_resource_group.rg.location}"
    
      name = "identity1"
    
      tags = "${var.tags}"
    }
    ```

    c. 创建基本网络资源。

    ```hcl
    resource "azurerm_virtual_network" "test" {
      name                = "${var.virtual_network_name}"
      location            = "${data.azurerm_resource_group.rg.location}"
      resource_group_name = "${data.azurerm_resource_group.rg.name}"
      address_space       = ["${var.virtual_network_address_prefix}"]
    
      subnet {
        name           = "${var.aks_subnet_name}"
        address_prefix = "${var.aks_subnet_address_prefix}" 
      }
    
      subnet {
        name           = "appgwsubnet"
        address_prefix = "${var.app_gateway_subnet_address_prefix}"
      }
    
      tags = "${var.tags}"
    }
    
    data "azurerm_subnet" "kubesubnet" {
      name                 = "${var.aks_subnet_name}"
      virtual_network_name = "${azurerm_virtual_network.test.name}"
      resource_group_name  = "${data.azurerm_resource_group.rg.name}"
    }
    
    data "azurerm_subnet" "appgwsubnet" {
      name                 = "appgwsubnet"
      virtual_network_name = "${azurerm_virtual_network.test.name}"
      resource_group_name  = "${data.azurerm_resource_group.rg.name}"
    }
    
    # Public Ip 
    resource "azurerm_public_ip" "test" {
      name                         = "publicIp1"
      location                     = "${data.azurerm_resource_group.rg.location}"
      resource_group_name          = "${data.azurerm_resource_group.rg.name}"
      public_ip_address_allocation = "static"
      sku                          = "Standard"
    
      tags = "${var.tags}"
    }
    ```

    d. 创建应用程序网关资源。

    ```hcl
    resource "azurerm_application_gateway" "network" {
      name                = "${var.app_gateway_name}"
      resource_group_name = "${data.azurerm_resource_group.rg.name}"
      location            = "${data.azurerm_resource_group.rg.location}"
    
      sku {
        name     = "${var.app_gateway_sku}"
        tier     = "Standard_v2"
        capacity = 2
      }
    
      gateway_ip_configuration {
        name      = "appGatewayIpConfig"
        subnet_id = "${data.azurerm_subnet.appgwsubnet.id}"
      }
    
      frontend_port {
        name = "${local.frontend_port_name}"
        port = 80
      }
    
      frontend_port {
        name = "httpsPort"
        port = 443
      }
    
      frontend_ip_configuration {
        name                 = "${local.frontend_ip_configuration_name}"
        public_ip_address_id = "${azurerm_public_ip.test.id}"
      }
    
      backend_address_pool {
        name = "${local.backend_address_pool_name}"
      }
    
      backend_http_settings {
        name                  = "${local.http_setting_name}"
        cookie_based_affinity = "Disabled"
        port                  = 80
        protocol              = "Http"
        request_timeout       = 1
      }
    
      http_listener {
        name                           = "${local.listener_name}"
        frontend_ip_configuration_name = "${local.frontend_ip_configuration_name}"
        frontend_port_name             = "${local.frontend_port_name}"
        protocol                       = "Http"
      }
    
      request_routing_rule {
        name                       = "${local.request_routing_rule_name}"
        rule_type                  = "Basic"
        http_listener_name         = "${local.listener_name}"
        backend_address_pool_name  = "${local.backend_address_pool_name}"
        backend_http_settings_name = "${local.http_setting_name}"
      }
    
      tags = "${var.tags}"
    
      depends_on = ["azurerm_virtual_network.test", "azurerm_public_ip.test"]
    }
    ```

    e. 创建角色分配。

    ```hcl
    resource "azurerm_role_assignment" "ra1" {
      scope                = "${data.azurerm_subnet.kubesubnet.id}"
      role_definition_name = "Network Contributor"
      principal_id         = "${var.aks_service_principal_object_id }"
    
      depends_on = ["azurerm_virtual_network.test"]
    }
    
    resource "azurerm_role_assignment" "ra2" {
      scope                = "${azurerm_user_assigned_identity.testIdentity.id}"
      role_definition_name = "Managed Identity Operator"
      principal_id         = "${var.aks_service_principal_object_id}"
      depends_on           = ["azurerm_user_assigned_identity.testIdentity"]
    }
    
    resource "azurerm_role_assignment" "ra3" {
      scope                = "${azurerm_application_gateway.network.id}"
      role_definition_name = "Contributor"
      principal_id         = "${azurerm_user_assigned_identity.testIdentity.principal_id}"
      depends_on           = ["azurerm_user_assigned_identity.testIdentity", "azurerm_application_gateway.network"]
    }
    
    resource "azurerm_role_assignment" "ra4" {
      scope                = "${data.azurerm_resource_group.rg.id}"
      role_definition_name = "Reader"
      principal_id         = "${azurerm_user_assigned_identity.testIdentity.principal_id}"
      depends_on           = ["azurerm_user_assigned_identity.testIdentity", "azurerm_application_gateway.network"]
    }
    ```

    f. 创建 Kubernetes 群集。

    ```hcl
    resource "azurerm_kubernetes_cluster" "k8s" {
      name       = "${var.aks_name}"
      location   = "${data.azurerm_resource_group.rg.location}"
      dns_prefix = "${var.aks_dns_prefix}"
    
      resource_group_name = "${data.azurerm_resource_group.rg.name}"
    
      linux_profile {
        admin_username = "${var.vm_user_name}"
    
        ssh_key {
          key_data = "${file(var.public_ssh_key_path)}"
        }
      }
    
      addon_profile {
        http_application_routing {
          enabled = false
        }
      }
    
      agent_pool_profile {
        name            = "agentpool"
        count           = "${var.aks_agent_count}"
        vm_size         = "${var.aks_agent_vm_size}"
        os_type         = "Linux"
        os_disk_size_gb = "${var.aks_agent_os_disk_size}"
        vnet_subnet_id  = "${data.azurerm_subnet.kubesubnet.id}"
      }
    
      service_principal {
        client_id     = "${var.aks_service_principal_app_id}"
        client_secret = "${var.aks_service_principal_client_secret}"
      }
    
      network_profile {
        network_plugin     = "azure"
        dns_service_ip     = "${var.aks_dns_service_ip}"
        docker_bridge_cidr = "${var.aks_docker_bridge_cidr}"
        service_cidr       = "${var.aks_service_cidr}"
      }
    
      depends_on = ["azurerm_virtual_network.test", "azurerm_application_gateway.network"]
      tags       = "${var.tags}"
    }
    
    ```

    上面的代码设置群集的名称、位置和 resource_group_name。 此外，设置了 dns_prefix 值 - 构成了用于访问群集的完全限定域名 (FQDN) 的一部分。

    使用 **linux_profile** 记录可以配置用于通过 SSH 登录到工作节点的设置。

    使用 AKS 时，只需支付工作节点的费用。 **agent_pool_profile** 记录配置这些工作节点的详细信息。 **agent_pool_profile 记录**包含要创建的工作节点数，以及工作节点的类型。 如果将来需要纵向扩展或缩减群集，请修改此记录中的 **count** 值。

1. 按 **Esc** 键退出插入模式。

1. 保存文件，然后输入以下命令退出 vi 编辑器：

    ```bash
    :wq
    ```


## <a name="create-a-terraform-output-file"></a>创建 Terraform 输出文件
使用 [Terraform 输出](https://www.terraform.io/docs/configuration/outputs.html)可以定义当 Terraform 应用计划时要向用户突出显示的、可以使用 `terraform output` 命令查询的值。 在本部分，我们将创建一个输出文件，以便使用 [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/) 访问群集。

1. 在 Cloud Shell 中，创建名为 `output.tf` 的文件。

    ```bash
    vi output.tf
    ```

1. 按 I 键进入插入模式。

1. 在编辑器中粘贴以下代码：

    ```hcl
    output "client_key" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.client_key}"
    }

    output "client_certificate" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.client_certificate}"
    }

    output "cluster_ca_certificate" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.cluster_ca_certificate}"
    }

    output "cluster_username" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.username}"
    }

    output "cluster_password" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.password}"
    }

    output "kube_config" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config_raw}"
    }

    output "host" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.host}"
    }
    ```

1. 按 **Esc** 键退出插入模式。

1. 保存文件，然后输入以下命令退出 vi 编辑器：

    ```bash
    :wq
    ```

## <a name="set-up-azure-storage-to-store-terraform-state"></a>将 Azure 存储设置为存储 Terraform 状态
Terraform 在本地通过 `terraform.tfstate` 文件跟踪状态。 在单用户环境中，此模式非常合适。 但是，在更常见的多用户环境中，需要使用 [Azure 存储](/azure/storage/)来跟踪服务器上的状态。 在本部分，我们将检索所需的存储帐户信息（帐户名称和帐户密钥），并创建用于存储 Terraform 状态信息的存储容器。

1. 在 Azure 门户的左侧菜单中，选择“所有服务”。 

1. 选择“存储帐户”。 

1. 在“存储帐户”选项卡上，选择用于存储 Terraform 状态信息的存储帐户名称。  例如，可以使用首次打开 Cloud Shell 时创建的存储帐户。  Cloud Shell 创建的存储帐户名称通常以 `cs` 开头，后接由数字和字母组成的随机字符串。 **请记下所选存储帐户的名称，因为稍后需要用到。**

1. 在存储帐户选项卡上，选择“访问密钥”。 

    ![存储帐户菜单](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account.png)

1. 记下“密钥 1”密钥值。   （选择密钥右侧的图标将值复制到剪贴板。）

    ![存储帐户访问密钥](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account-access-key.png)

1. 在 Cloud Shell 中，在 Azure 存储帐户内创建一个容器（请将 &lt;YourAzureStorageAccountName> 和 &lt;YourAzureStorageAccountAccessKey> 占位符替换为 Azure 存储帐户的相应值）。

    ```azurecli
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>创建 Kubernetes 群集
本部分介绍如何使用 `terraform init` 命令来创建资源，这些资源定义了前面部分中所创建的配置文件。

1. 在 Cloud Shell 中启动 Terraform（请将 &lt;YourAzureStorageAccountName> 和 &lt;YourAzureStorageAccountAccessKey> 占位符替换为 Azure 存储帐户的相应值）。

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
    
    `terraform init` 命令显示成功初始化后端和提供程序插件：

    ![“Terraform init”结果示例](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-init-complete.png)

1. 创建一个 variables 文件用于提供输入值。在 Cloud Shell 中，创建名为 `main.tf` 的文件。

    ```bash
    vi terraform.tfvars
    ```

1. 按 I 键进入插入模式。

1. 将前面创建的以下变量粘贴到编辑器中：

    ```hcl
      resource_group_name = <Name of the Resource Group already created>

      location = <Location of the Resource Group>
        
      aks_service_principal_app_id = <Service Principal AppId>
        
      aks_service_principal_client_secret = <Service Principal Client Secret>
        
      aks_service_principal_object_id = <Service Principal Object Id>
        
    ```

1. 按 **Esc** 键退出插入模式。

1. 保存文件，然后输入以下命令退出 vi 编辑器：

    ```bash
    :wq
    ```

1. 运行 `terraform plan` 命令，以创建定义基础结构元素的 Terraform 计划。 

    ```bash
    terraform plan -out out.plan
    ```

    `terraform plan` 命令显示运行 `terraform apply` 命令时要创建的资源：

    ![“Terraform plan”结果示例](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-plan-complete.png)

1. 运行 `terraform apply` 命令，以应用该计划来创建 Kubernetes 群集。 创建 Kubernetes 群集的过程可能需要花费几分钟时间，从而导致 Cloud Shell 会话超时。如果 Cloud Shell 会话超时，可以遵循“在 Cloud Shell 超时后进行恢复”部分中的步骤来完成本教程。

    ```bash
    terraform apply out.plan
    ```

    `terraform apply` 命令显示创建配置文件中定义的资源的结果：

    ![“Terraform apply”结果示例](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-apply-complete.png)

1. 在 Azure 门户的左侧菜单中选择“资源组”，查看为选定资源组中的新 Kubernetese 群集创建的资源。 

    ![Cloud Shell 提示符](./media/terraform-k8s-cluster-appgw-with-tf-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>在 Cloud Shell 超时后进行恢复
如果 Cloud Shell 会话超时，可使用以下步骤予以恢复：

1. 启动 Cloud Shell 会话。

1. 切换到包含 Terraform 配置文件的目录。

    ```bash
    cd /clouddrive/terraform-aks-k8s
    ```

1. 运行以下命令：

    ```bash
    export KUBECONFIG=./azurek8s
    ```
    
## <a name="test-the-kubernetes-cluster"></a>测试 Kubernetes 群集
可以使用 Kubernetes 工具来验证新建的群集。

1. 从 Terraform 状态中获取 Kubernetes 配置，并将其存储在 kubectl 可以读取的文件中。

    ```bash
    echo "$(terraform output kube_config)" > ./azurek8s
    ```

1. 设置环境变量，使 kubectl 拾取正确的配置。

    ```bash
    export KUBECONFIG=./azurek8s
    ```

1. 验证群集的运行状况。

    ```bash
    kubectl get nodes
    ```

    应会看到工作节点的详细信息，并且这些节点的状态为“就绪”，如下图所示： 

    ![使用 kubectl 工具可以验证 Kubernetes 群集的运行状况](./media/terraform-k8s-cluster-appgw-with-tf-aks/kubectl-get-nodes.png)


## <a name="next-steps"></a>后续步骤
本文已介绍如何使用 Terraform 和 AKS 创建 Kubernetes 群集。 请参阅以下附加资源，帮助自己详细了解 Azure 上的 Terraform。
 
 > [!div class="nextstepaction"] 
 > [Microsoft.com 中的 Terraform 中心](https://docs.microsoft.com/azure/terraform/)
 
