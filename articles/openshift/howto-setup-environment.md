---
title: 设置 Azure Red Hat OpenShift 开发环境 |Microsoft Docs
description: 以下是使用 Microsoft Azure Red Hat OpenShift 的先决条件。
services: openshift
keywords: red hat openshift 的安装程序设置
author: jimzim
ms.author: jzim
ms.date: 05/10/2019
ms.topic: conceptual
ms.service: container-service
manager: jeconnoc
ms.openlocfilehash: a31655e8c8805505bdcc5e90bf25191590d35c18
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672516"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>设置 Azure Red Hat OpenShift 开发环境

若要生成并运行 Microsoft Azure Red Hat OpenShift 的应用程序，你将需要：

* 购买 Azure 虚拟机保留实例。
* 安装版本 2.0.65 （或更高版本） 的 Azure CLI （或使用 Azure Cloud Shell）。
* 注册`AROGA`功能和关联的资源提供程序。
* 创建一个 Azure Active Directory (Azure AD) 租户。
* 创建 Azure AD 应用程序对象。
* 创建 Azure AD 用户。

以下说明将指导你完成所有这些系统必备组件。

## <a name="purchase-azure-red-hat-openshift-application-nodes-reserved-instances"></a>购买 Azure Red Hat OpenShift 应用程序节点保留的实例

可以使用 Azure Red Hat OpenShift 之前，你将需要购买最少 4 个 Azure Red Hat OpenShift 保留应用程序节点，此后您将能够预配群集。

如果是 Azure 客户，[购买 Azure Red Hat OpenShift 保留实例](https://aka.ms/openshift/buy)通过 Azure 门户。 购买后，将在 24 小时内激活你的订阅。

如果您不是 Azure 客户[联系销售人员](https://aka.ms/openshift/contact-sales)并填写销售窗体底部的页后，可以启动进程。

请参阅[定价页 Azure Red Hat OpenShift](https://aka.ms/openshift/pricing)有关详细信息。

## <a name="install-the-azure-cli"></a>安装 Azure CLI

Azure Red Hat OpenShift 需要版本 2.0.65 或更高版本的 Azure cli。 如果你已安装 Azure CLI，可以检查已通过运行哪个版本：

```bash
az --version
```

输出的第一行将具有的 CLI 版本，例如`azure-cli (2.0.65)`。

以下是有关的说明[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)如果需要全新安装或升级。

或者，可以使用[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)。 在使用 Azure Cloud Shell，请务必选择**Bash**如果你打算按照环境[创建和管理 Azure Red Hat OpenShift 群集](tutorial-create-cluster.md)系列教程。

## <a name="register-providers-and-features"></a>注册提供程序和功能

`Microsoft.ContainerService AROGA`功能， `Microsoft.Solutions`， `Microsoft.Compute`， `Microsoft.Storage`，`Microsoft.KeyVault`和`Microsoft.Network`必须到你的订阅部署第一个 Azure Red Hat OpenShift 群集之前手动注册提供程序。

若要手动注册这些提供程序和功能，请在 Azure 门户中使用以下说明从 Bash shell 中，如果你已安装 CLI，或从 Azure Cloud Shell (Bash) 会话：

1. 如果你有多个 Azure 订阅，请指定相关的订阅 ID:

    ```bash
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. 注册 Microsoft.ContainerService AROGA 功能：

    ```bash
    az feature register --namespace Microsoft.ContainerService -n AROGA
    ```

1. 注册的 Microsoft.Storage 提供程序：

    ```bash
    az provider register -n Microsoft.Storage --wait
    ```
    
1. 注册 Microsoft.Compute 提供程序：

    ```bash
    az provider register -n Microsoft.Compute --wait
    ```

1. 注册 Microsoft.Solutions 提供程序：

    ```bash
    az provider register -n Microsoft.Solutions --wait
    ```

1. 注册 Microsoft.Network 提供程序：

    ```bash
    az provider register -n Microsoft.Network --wait
    ```

1. 注册 Microsoft.KeyVault 提供程序：

    ```bash
    az provider register -n Microsoft.KeyVault --wait
    ```

1. 刷新的注册 Microsoft.ContainerService 资源提供程序：

    ```bash
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>创建 Azure Active Directory (Azure AD) 租户

Azure Red Hat OpenShift 服务需要向 Microsoft 表示你的组织和其关系的关联的 Azure Active Directory (Azure AD) 租户。 Azure AD 租户，可注册、 构建和管理应用，以及使用其他 Azure 服务。

如果你没有 Azure AD 要作为租户用于 Azure Red Hat OpenShift 群集，或如果想要创建用于测试租户，请按照中的说明[创建 Azure Red Hat OpenShift 群集的 Azure AD 租户](howto-create-tenant.md)之前继续执行本指南。

## <a name="create-an-azure-ad-user-security-group-and-application-object"></a>创建 Azure AD 用户、 安全组和应用程序对象

Azure Red Hat OpenShift 需要权限才能在你的群集，例如配置存储上执行任务。 这些权限都是通过[服务主体](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object)。 此外需要创建一个新的 Active Directory 用户用于测试你的 Azure Red Hat OpenShift 群集上运行的应用。

按照中的说明[创建 Azure AD 应用程序对象和用户](howto-aad-app-configuration.md)若要创建服务主体，请生成您的应用程序的客户端机密和身份验证回调 URL 并创建一个新 Azure AD 安全组和用户访问群集。

## <a name="next-steps"></a>后续步骤

现在，你可以使用 Azure Red Hat OpenShift ！

请尝试教程：
> [!div class="nextstepaction"]
> [创建 Azure Red Hat OpenShift 群集](tutorial-create-cluster.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
