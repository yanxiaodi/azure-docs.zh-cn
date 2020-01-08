---
title: 使用 .NET 向 Azure Key Vault 进行服务到服务身份验证
description: 使用 Microsoft.Azure.Services.AppAuthentication 库通过 .NET 向 Azure Key Vault 进行身份验证。
keywords: azure key-vault 身份验证本地凭据
author: msmbaldwin
manager: rkarlin
services: key-vault
ms.author: mbaldwin
ms.date: 08/28/2019
ms.topic: conceptual
ms.service: key-vault
ms.openlocfilehash: 201f35e7b3ccf7c113ae30a6d007ad3a1f9adb98
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087689"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>使用 .NET 向 Azure Key Vault 进行服务到服务身份验证

若要对 Azure Key Vault 进行身份验证，需要一个 Azure Active Directory （Azure AD）凭据，即共享机密或证书。

管理此类凭据可能比较困难。 通过将凭据包含在源或配置文件中，可以将凭据捆绑到应用中。 用于 .NET 库的 `Microsoft.Azure.Services.AppAuthentication` 简化了此问题。 它使用开发人员的凭据在本地开发期间进行身份验证。 随后将解决方案部署到 Azure 时，该库会自动切换到应用程序凭据。 在本地开发期间使用开发人员凭据更安全，因为无需创建 Azure AD 凭据或在开发人员之间共享凭据。

`Microsoft.Azure.Services.AppAuthentication`库自动管理身份验证，从而使你可以专注于你的解决方案，而不是你的凭据。 该库支持使用 Microsoft Visual Studio、Azure CLI 或 Azure AD 集成身份验证进行本地开发。 部署到支持托管标识的 Azure 资源时，该库会自动使用 [Azure 资源的托管标识](../active-directory/msi-overview.md)。 不需代码或配置更改。 当托管标识不可用时，或在本地开发期间无法确定开发人员的安全上下文时，该库还支持直接使用 Azure AD 的[客户端凭据](../azure-resource-manager/resource-group-authenticate-service-principal.md)。

## <a name="prerequisites"></a>先决条件

- [Visual studio 2019](https://www.visualstudio.com/downloads/)或[visual studio 2017 v 15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/)。

- 适用于 Visual Studio 的应用程序身份验证扩展插件，可用作 Visual Studio 2017 Update 5 的单独扩展，并与 Update 6 和更高版本中的产品捆绑在一起。 使用 Update 6 或更高版本时，可以验证是否安装了应用身份验证扩展，方法是在 Visual Studio 安装程序中选择 Azure 开发工具。

## <a name="using-the-library"></a>使用库

对于 .NET 应用程序，若要使用托管标识，最简单的方式是通过 `Microsoft.Azure.Services.AppAuthentication` 包来使用。 下面介绍如何入门：

1. 选择 "**工具** > " "**nuget 包管理器** > " "**管理解决方案的 NuGet 包**" 以添加对[microsoft.azure.services.appauthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)和[KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)的引用。NuGet 包到你的项目。

1. 添加以下代码：

    ``` csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;

    // Instantiate a new KeyVaultClient object, with an access token to Key Vault
    var azureServiceTokenProvider1 = new AzureServiceTokenProvider();
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider1.KeyVaultTokenCallback));

    // Optional: Request an access token to other Azure services
    var azureServiceTokenProvider2 = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider2.GetAccessTokenAsync("https://management.azure.com/").ConfigureAwait(false);
    ```

`AzureServiceTokenProvider` 类将令牌缓存在内存中，在过期前才将其从 Azure AD 检索出来。 因此，在调用`GetAccessTokenAsync`方法之前，您不再需要检查到期时间。 在需要使用令牌时直接调用该方法即可。

`GetAccessTokenAsync` 方法需要资源标识符。 若要了解有关 Microsoft Azure 服务的详细信息，请参阅[什么是 Azure 资源的托管标识](../active-directory/msi-overview.md)。

## <a name="local-development-authentication"></a>本地开发身份验证

对于本地开发，有两种主要的身份验证方案：[对 Azure 服务进行身份验证](#authenticating-to-azure-services)；[对自定义服务进行身份验证](#authenticating-to-custom-services)。

### <a name="authenticating-to-azure-services"></a>向 Azure 服务进行身份验证

本地计算机不支持 Azure 资源的托管标识。 因此，`Microsoft.Azure.Services.AppAuthentication` 库使用开发人员凭据在本地开发环境中运行。 当解决方案部署到 Azure 时，该库使用托管标识切换到 OAuth 2.0 客户端凭据授予流。 此方法意味着，你可以在本地和远程测试相同的代码，而无需担心。

对于本地开发，`AzureServiceTokenProvider` 使用 **Visual Studio**、**Azure 命令行界面** (CLI) 或 **Azure AD 集成身份验证**提取令牌。 将按顺序尝试每个选项，该库会使用获得成功的第一个选项。 如果没有选项成功，则会引发一个包含详细信息的 `AzureServiceTokenProviderException` 意外。

#### <a name="authenticating-with-visual-studio"></a>使用 Visual Studio 进行身份验证

使用 Visual Studio 进行身份验证：

1. 登录到 Visual Studio，然后使用 **"工具**&nbsp;>&nbsp;"**选项**打开**选项**。

1. 选择 " **Azure 服务身份验证**"，选择用于本地开发的帐户，然后选择 **"确定"** 。

如果使用 Visual Studio 时遇到问题（如涉及令牌提供程序文件的错误），请仔细查看前面的步骤。

可能需要重新验证开发人员令牌。 为此，请选择 **"工具**&nbsp;>&nbsp;" "**选项**"，然后选择 " **Azure&nbsp;服务&nbsp;身份验证**"。 查找所选帐户下的 "**重新身份验证**" 链接。 选择该链接进行身份验证。

#### <a name="authenticating-with-azure-cli"></a>使用 Azure CLI 进行身份验证

若要将 Azure CLI 用于本地开发，请确保具有版本[Azure CLI v 2.0.12](/cli/azure/install-azure-cli)或更高版本。

使用 Azure CLI：

1. 在 Windows 任务栏中搜索 Azure CLI 打开**Microsoft Azure 命令提示符**。

1. 登录到 Azure 门户： *az login*登录到 Azure。

1. 输入*az account get access-token*来验证访问权限。 如果收到错误，请检查是否正确安装了正确版本的 Azure CLI。

   如果 Azure CLI 未安装到默认目录，你可能会收到错误报告， `AzureServiceTokenProvider`找不到 Azure CLI 的路径。 请使用 **AzureCLIPath** 环境变量来定义 Azure CLI 安装文件夹。 `AzureServiceTokenProvider` 在需要时将 **AzureCLIPath** 环境变量中指定的目录添加到 **Path** 环境变量。

1. 如果使用多个帐户登录到 Azure CLI，或者你的帐户有权访问多个订阅，则需指定要使用的订阅。 输入命令*az account set--订阅 < 订阅 id >* 。

此命令仅在发生故障时生成输出。 若要验证当前的帐户设置，请输入`az account list`命令。

#### <a name="authenticating-with-azure-ad-authentication"></a>使用 Azure AD 身份验证进行身份验证

若要使用 Azure AD 身份验证，请验证：

- 本地 Active Directory 同步到 Azure AD。 有关详细信息，请参阅[什么是混合标识与 Azure Active Directory？](../active-directory/connect/active-directory-aadconnect.md)。

- 你的代码在加入域的计算机上运行。

### <a name="authenticating-to-custom-services"></a>向自定义服务进行身份验证

当某个服务调用 Azure 服务时，上述步骤适用，因为 Azure 服务允许访问用户和应用程序。

创建调用自定义服务的服务时，请使用 Azure AD 客户端凭据进行本地开发身份验证。 有两个选项：

- 使用服务主体登录到 Azure：

    1. 创建服务主体。 有关详细信息，请参阅[使用 Azure CLI 创建 Azure 服务主体](/cli/azure/create-an-azure-service-principal-azure-cli)。

    1. 使用 Azure CLI，使用以下命令登录：

        ```azurecli
        az login --service-principal -u <principal-id> --password <password> --tenant <tenant-id> --allow-no-subscriptions
        ```

        由于服务主体可能没有订阅访问权限，因此请使用 `--allow-no-subscriptions` 参数。

- 使用环境变量来指定服务主体详细信息。 有关详细信息，请参阅[使用服务主体运行应用程序](#running-the-application-using-a-service-principal)。

登录到 Azure 后， `AzureServiceTokenProvider`将使用服务主体检索用于本地开发的令牌。

此方法仅适用于本地开发。 当解决方案部署到 Azure 时，库会切换到用于身份验证的托管标识。

## <a name="running-the-application-using-managed-identity-or-user-assigned-identity"></a>使用托管标识或用户分配标识运行应用程序

在启用托管标识的 Azure 应用服务或 Azure VM 上运行代码时，库自动使用托管标识。 不需要更改代码，但托管标识必须具有密钥保管库的*get*权限。 可以通过密钥保管库的*访问策略*为托管标识*获取*权限。

或者，您可以使用用户分配的标识进行身份验证。 有关用户分配的标识的详细信息，请参阅[关于 Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)。 若要使用用户分配的标识进行身份验证，需要在连接字符串中指定用户分配的标识的客户端 ID。 连接字符串是在[连接字符串支持](#connection-string-support)中指定的。

## <a name="running-the-application-using-a-service-principal"></a>使用服务主体运行应用程序

可能需要创建一个用于身份验证的 Azure AD 客户端凭据。 在以下示例中，可能会发生这种情况：

- 代码运行在本地开发环境中，但没有使用开发人员的标识。 例如，Service Fabric 使用 [NetworkService 帐户](../service-fabric/service-fabric-application-secret-management.md)进行本地开发。

- 代码在本地开发环境中运行，而身份验证则通过自定义服务进行，因此不能使用开发人员标识。

- 你的代码在 Azure 计算资源上运行，但尚不支持 Azure 资源的托管标识，如 Azure Batch。

可通过三种主要方法使用服务主体来运行应用程序。 若要使用其中任何一个，必须先创建服务主体。 有关详细信息，请参阅[使用 Azure CLI 创建 Azure 服务主体](/cli/azure/create-an-azure-service-principal-azure-cli)。

### <a name="use-a-certificate-in-local-keystore-to-sign-into-azure-ad"></a>使用本地密钥存储中的证书登录到 Azure AD

1. 使用 Azure CLI [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 命令创建服务主体证书。

    ```azurecli
    az ad sp create-for-rbac --create-cert
    ```

    此命令创建一个存储在主目录中的 pem 文件（私钥）。 将此证书部署到 *LocalMachine* 或 *CurrentUser* 存储。

    > [!Important]
    > CLI 命令生成一个 pem 文件，但 Windows 只为 PFX 证书提供本机支持。 若要改为生成 PFX 证书，请使用此处所示的 PowerShell 命令：[创建具有自签名证书的服务主体](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate)。 这些命令也会自动部署证书。

1. 将名为**AzureServicesAuthConnectionString**的环境变量设置为以下值：

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={CertificateStore}
    ```

    将{AppId}、{TenantId} 和{Thumbprint} 替换为步骤 1 中生成的值。 根据你的部署计划，将 *{CertificateStore}* 替换为*LocalMachine*或*CurrentUser*。

1. 运行该应用程序。

### <a name="use-a-shared-secret-credential-to-sign-into-azure-ad"></a>使用共享机密凭据登录到 Azure AD

1. 使用 [az ad sp create-for-rbac --password](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 创建包含密码的服务主体证书。

1. 将名为**AzureServicesAuthConnectionString**的环境变量设置为以下值：

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}
    ```

    将 _{AppId}_ 、 _{TenantId}_ 和 _{ClientSecret}_ 替换为步骤 1 中生成的值。

1. 运行该应用程序。

一切正确设置以后，不需进一步更改代码。 `AzureServiceTokenProvider` 使用环境变量和证书向 Azure AD 进行身份验证。

### <a name="use-a-certificate-in-key-vault-to-sign-into-azure-ad"></a>使用 Key Vault 中的证书登录到 Azure AD

此选项允许你将服务主体的客户端证书存储在 Key Vault 中，并将其用于服务主体身份验证。 对于以下方案，可以使用此选项：

- 本地身份验证：你想要使用显式服务主体进行身份验证，并希望将服务主体凭据安全保存在 Key Vault 中。 开发人员帐户必须有权访问 Key Vault。

- 从 Azure 进行身份验证，你希望使用显式凭据，并想要将服务主体凭据安全地保存在密钥保管库中。 对于跨租户方案，可以使用此选项。 托管标识必须有权访问 Key Vault。

托管标识或开发人员标识必须有权从 Key Vault 检索客户端证书。 AppAuthentication 库使用检索到的证书作为服务主体的客户端凭据。

使用客户端证书进行服务主体身份验证：

1. 创建一个服务主体证书，并自动将其存储在 Key Vault 中。 使用 Azure CLI [az ad sp create \<--keyvault keyvaultname >--cert \<certificatename >--](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) --

    ```azurecli
    az ad sp create-for-rbac --keyvault <keyvaultname> --cert <certificatename> --create-cert --skip-assignment
    ```

    证书标识符是采用 `https://<keyvaultname>.vault.azure.net/secrets/<certificatename>` 格式的 URL

1. 请将此连接字符串中的 `{KeyVaultCertificateSecretIdentifier}` 替换为证书标识符：

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}
    ```

    例如，如果你的密钥保管库名为*myKeyVault* ，并且你创建了一个名为*mycert.cer*的证书，则该证书标识符将为：

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier=https://myKeyVault.vault.azure.net/secrets/myCert
    ```

## <a name="connection-string-support"></a>连接字符串支持

默认情况下，`AzureServiceTokenProvider` 使用多种方法来检索令牌。

若要控制此过程，请使用传递到 `AzureServiceTokenProvider` 构造函数或在AzureServicesAuthConnectionString 环境变量中指定的连接字符串。

可以使用以下选项：

| 连接字符串选项 | 应用场景 | 注释|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | 本地开发 | `AzureServiceTokenProvider`使用 AzureCli 获取令牌。 |
| `RunAs=Developer; DeveloperTool=VisualStudio` | 本地开发 | `AzureServiceTokenProvider`使用 Visual Studio 获取令牌。 |
| `RunAs=CurrentUser` | 本地开发 | `AzureServiceTokenProvider`使用 Azure AD 集成身份验证获取令牌。 |
| `RunAs=App` | [Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/index.yml) | `AzureServiceTokenProvider`使用托管标识获取令牌。 |
| `RunAs=App;AppId={ClientId of user-assigned identity}` | [Azure 资源的用户分配的标识](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) | `AzureServiceTokenProvider`使用用户分配的标识获取令牌。 |
| `RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}` | 自定义服务身份验证 | `KeyVaultCertificateSecretIdentifier`证书的机密标识符。 |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`| 服务主体 | `AzureServiceTokenProvider` 使用证书从 Azure AD 获取令牌。 |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateSubjectName={Subject};CertificateStoreLocation={LocalMachine or CurrentUser}` | 服务主体 | `AzureServiceTokenProvider` 使用证书从 Azure AD 获取令牌|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | 服务主体 |`AzureServiceTokenProvider` 使用机密从 Azure AD 获取令牌。 |

## <a name="samples"></a>示例

若要查看`Microsoft.Azure.Services.AppAuthentication`正在运行的库，请参阅以下代码示例。

- [Use a managed identity to retrieve a secret from Azure Key Vault at runtime](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)（在运行时使用托管标识从 Azure Key Vault 检索机密）

- [Programmatically deploy an Azure Resource Manager template from an Azure VM with a managed identity](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet)（使用托管标识以编程方式从 Azure VM 部署 Azure 资源管理器模板）。

- [Use .NET Core sample and a managed identity to call Azure services from an Azure Linux VM](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/)（使用 .NET Core 示例和托管标识从 Azure Linux VM 调用 Azure 服务）。

## <a name="appauthentication-troubleshooting"></a>Microsoft.azure.services.appauthentication 故障排除

### <a name="common-issues-during-local-development"></a>本地开发期间的常见问题

#### <a name="azure-cli-is-not-installed-youre-not-logged-in-or-you-dont-have-the-latest-version"></a>未安装 Azure CLI，你未登录，或者你没有最新版本

若要查看 Azure CLI 是否显示了令牌，请运行*az account get 访问令牌*。 如果**未找到此类程序**，请安装[最新版本的 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。 系统可能会提示你登录。

#### <a name="azureservicetokenprovider-cant-find-the-path-for-azure-cli"></a>AzureServiceTokenProvider 找不到 Azure CLI 的路径

AzureServiceTokenProvider 在默认安装位置查找 Azure CLI。 如果找不到 Azure CLI，请将环境变量**AzureCLIPath**设置为 Azure CLI 安装文件夹。 AzureServiceTokenProvider 会将环境变量添加到 Path 环境变量中。

#### <a name="youre-logged-into-azure-cli-using-multiple-accounts-the-same-account-has-access-to-subscriptions-in-multiple-tenants-or-you-get-an-access-denied-error-when-trying-to-make-calls-during-local-development"></a>你使用多个帐户登录 Azure CLI，同一帐户有权访问多个租户中的订阅，或者在本地开发期间尝试进行调用时收到拒绝访问错误

使用 Azure CLI 将默认订阅设置为具有要使用的帐户的订阅。 订阅必须与要访问的资源在同一租户中： **az account set--订阅 [订阅 id]** 。 如果未显示任何输出，则它成功。 使用**az account list**验证正确的帐户是否为默认帐户。

### <a name="common-issues-across-environments"></a>跨环境的常见问题

#### <a name="unauthorized-access-access-denied-forbidden-or-similar-error"></a>未经授权的访问、拒绝访问、禁止访问或类似错误

使用的主体没有访问尝试访问的资源的权限。 向你的用户帐户或应用服务的 MSI "参与者" 授予对资源的访问权限。 哪一个取决于你是在本地计算机上运行示例，还是在 Azure 中部署到应用服务。 某些资源（例如 key vault）还具有其自己的[访问策略](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault#data-plane-and-access-policies)，你可以使用它们授予对主体（例如用户、应用和组）的访问权限。

### <a name="common-issues-when-deployed-to-azure-app-service"></a>部署到 Azure App Service 时遇到的常见问题

#### <a name="managed-identity-isnt-set-up-on-the-app-service"></a>未在应用服务上设置托管标识

使用[Kudu 调试控制台](https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/)检查环境变量 MSI_ENDPOINT 和 MSI_SECRET 是否存在。 如果这些环境变量不存在，则不会在应用服务上启用托管标识。

### <a name="common-issues-when-deployed-locally-with-iis"></a>在本地与 IIS 一起部署时遇到的常见问题

#### <a name="cant-retrieve-tokens-when-debugging-app-in-iis"></a>在 IIS 中调试应用时无法检索令牌

默认情况下，AppAuth 在 IIS 的不同用户上下文中运行。 这就是为什么无法使用开发人员标识来检索访问令牌的原因。 可以将 IIS 配置为在用户上下文中运行，步骤如下：
- 将 web 应用的应用程序池配置为以当前用户帐户身份运行。 请在[此处](https://docs.microsoft.com/iis/manage/configuring-security/application-pool-identities#configuring-iis-application-pool-identities)查看详细信息
- 将 "setProfileEnvironment" 配置为 "True"。 请参阅[此处](https://docs.microsoft.com/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)的详细信息。 

    - 中转到%windir%\System32\inetsrv\config\applicationHost.config
    - 搜索 "setProfileEnvironment"。 如果设置为 "False"，则将其更改为 "True"。 如果该元素不存在，请将其作为属性添加到 processModel 元素（/configuration/system.applicationHost/applicationPools/applicationPoolDefaults/processModel/@setProfileEnvironment），并将其设置为 "True"。

- 详细了解 [Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/index.yml)。
- 详细了解 [Azure AD 身份验证方案](../active-directory/develop/active-directory-authentication-scenarios.md)。
