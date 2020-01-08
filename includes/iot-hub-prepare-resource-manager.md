---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 4eb794fa35164e3f86a5e3d6f67d446321f91f0a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133875"
---
## <a name="prepare-to-authenticate-azure-resource-manager-requests"></a>准备对 Azure Resource Manager 请求进行身份验证
必须使用 [Azure 资源管理器][lnk-authenticate-arm] 配合 Azure Active Directory (AD) 来验证所有针对资源执行的操作。 最简单的配置方式是使用 PowerShell 或 Azure CLI。

在继续之前，请安装 [Azure PowerShell cmdlet][lnk-powershell-install]。

以下步骤说明如何使用 PowerShell 设置 AD 应用程序的密码身份验证。 可以在标准 PowerShell 会话中运行这些命令。

1. 使用以下命令登录到 Azure 订阅：

    ```powershell
    Connect-AzAccount
    ```

1. 如果有多个 Azure 订阅，则访问 Azure 即有权访问与凭据关联的所有 Azure 订阅。 使用以下命令，列出可供使用的 Azure 订阅：

    ```powershell
    Get-AzSubscription
    ```

    使用以下命令，选择想要用于运行命令以管理 IoT 中心的订阅。 可使用上一命令输出中的订阅名称或 ID：

    ```powershell
    Select-AzSubscription `
        -SubscriptionName "{your subscription name}"
    ```

2. 记下 **TenantId** 和 **SubscriptionId**。 稍后会需要它们。
3. 使用以下命令并替换占位符，以创建新的 Azure Active Directory 应用程序：
   
   * **{Display name}** ：应用程序的显示名称，例如 **MySampleApp**
   * **{Home page URL}:** 如您的应用程序主页的 URL **http:\/mysampleapp/家庭**。 此 URL 不需要指向实际的应用程序。
   * **{Application identifier}：** 唯一标识符，例如**http:\//mysampleapp**。 此 URL 不需要指向实际的应用程序。
   * **{Password}：** 用于向应用进行身份验证的密码。
     
     ```powershell
     $SecurePassword=ConvertTo-SecureString {password} –asplaintext –force
     New-AzADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password $SecurePassword
     ```
4. 请记下创建的应用程序的 **ApplicationId**。 稍后会需要它。
5. 使用以下命令（将 **{MyApplicationId}** 替换为上一步骤中的 **ApplicationId**）创建新的服务主体：
   
    ```powershell
    New-AzADServicePrincipal -ApplicationId {MyApplicationId}
    ```
6. 使用以下命令（将 **{MyApplicationId}** 替换为 **ApplicationId**）设置角色分配。
   
    ```powershell
    New-AzRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```

现在，已创建可从自定义 C# 应用程序进行身份验证的 Azure AD 应用程序。 在本教程的后续内容中，需要用到以下值：

* TenantId
* SubscriptionId
* ApplicationId
* 密码

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx
[lnk-powershell-install]: /powershell/azure/install-az-ps
