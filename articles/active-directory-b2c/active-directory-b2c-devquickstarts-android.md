---
title: 在 Azure Active Directory B2C 中使用 Android 应用程序获取令牌 | Microsoft Docs
description: 本文说明如何创建一个使用 AppAuth 和 Azure Active Directory B2C 来管理用户标识以及对用户进行身份验证的 Android 应用。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 29f1fc2a6fd23ef3a770f58fd78d5067672136dd
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326310"
---
# <a name="sign-in-using-an-android-application-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用 Android 应用程序登录

Microsoft 标识平台使用开放式标准，例如 OAuth2 和 OpenID Connect。 这些标准允许你利用任何你希望与 Azure Active Directory B2C 集成的库。 为了帮助使用其他库，可以使用演练（例如本演练），演示如何配置第三方库，使其连接到 Microsoft 标识平台。 大部分实施 [RFC6749 OAuth2 规范](https://tools.ietf.org/html/rfc6749)的库都能连接到 Microsoft 标识平台。

> [!WARNING]
> Microsoft 不提供第三方库的修复程序，且尚未审查这些库。 本示例使用名为 AppAuth 的第三方库，该库经测试可与 Azure AD B2C 的基本方案兼容。 问题和功能请求应重定向到库的开源项目。 有关详细信息，请参阅[此文](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries)。
>
>

对于 OAuth2 或 OpenID Connect 的新手，该示例配置中的大部分内容可能较难理解。 建议查看 [此处所述的简要协议概述](active-directory-b2c-reference-protocols.md)。

## <a name="get-an-azure-ad-b2c-directory"></a>获取 Azure AD B2C 目录

只有在创建目录或租户之后，才可使用 Azure AD B2C。 目录是所有用户、应用、组等对象的容器。 如果没有容器，请先 [创建 B2C 目录](tutorial-create-tenant.md) ，再继续。

## <a name="create-an-application"></a>创建应用程序

接下来，将应用程序注册到 Azure AD B2C 租户。 这为 Azure AD 提供了与应用安全通信所需的信息。

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

记录**应用程序 ID** ，以便在后面的步骤中使用。 接下来，在列表中选择应用程序，并记录**自定义重定向 URI**，还可在后面的步骤中使用。 例如， `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` 。

## <a name="create-your-user-flows"></a>创建用户流

在 Azure AD B2C 中，每个用户体验都是由[用户流](active-directory-b2c-reference-policies.md)定义的，这是一组控制 Azure AD 行为的策略。 该应用程序需要登录和注册用户流。 创建用户流时，请务必：

* 选择“显示名称”作为用户流中的注册属性。
* 在每个用户流中，选择“显示名称”和“对象 ID”应用程序声明。 也可以选择其他声明。
* 创建用户流后，请复制每个用户流的名称。 其前缀应为 `b2c_1_`。  稍后需要用户流名称。

创建用户流后，可以开始构建应用。

## <a name="download-the-sample-code"></a>下载示例代码

我们[在 GitHub 上](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c)提供了有关将 AppAuth 与 Azure AD B2C 配合使用的实践示例。 可以下载该代码并运行它。 可以遵照 [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md) 中的说明，使用自己的 Azure AD B2C 配置快速开始操作自己的应用。

该示例是 [AppAuth](https://openid.github.io/AppAuth-Android/) 提供的示例的修改版。 请访问 AppAuth 的页面来了解该产品及其功能。

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>修改应用，以便将 AppAuth 与 Azure AD B2C 配合使用

> [!NOTE]
> AppAuth 支持 Android API 16 (Jellybean) 和更高版本。 建议使用 API 23 和更高版本。
>

### <a name="configuration"></a>配置

可以通过指定发现 URI 或者指定授权终结点和令牌终结点 URI，来配置与 Azure AD B2C 的通信。 在任一情况下，都需要提供以下信息：

* 租户 ID（例如 contoso.onmicrosoft.com）
* 用户流名称（例如 B2C\_1\_SignUpIn）

如果选择自动发现授权和令牌终结点 URI，需要从发现 URI 中提取信息。 可以通过替换以下 URL 中的 Tenant\_ID 和 Policy\_Name 来生成发现 URI：

```java
String mDiscoveryURI = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/v2.0/.well-known/openid-configuration?p=<Policy_Name>";
```

然后，可以获取授权和令牌终结点 URI，并运行以下命令来创建 AuthorizationServiceConfiguration 对象：

```java
final Uri issuerUri = Uri.parse(mDiscoveryURI);
AuthorizationServiceConfiguration config;

AuthorizationServiceConfiguration.fetchFromIssuer(
    issuerUri,
    new RetrieveConfigurationCallback() {
      @Override public void onFetchConfigurationCompleted(
          @Nullable AuthorizationServiceConfiguration serviceConfiguration,
          @Nullable AuthorizationException ex) {
        if (ex != null) {
            Log.w(TAG, "Failed to retrieve configuration for " + issuerUri, ex);
        } else {
            // service configuration retrieved, proceed to authorization...
        }
      }
  });
```

如果不使用发现功能来获取授权和令牌终结点 URI，也可以通过替换以下 URL 中的 Tenant\_ID 和 Policy\_Name 来显式指定这些 URI：

```java
String mAuthEndpoint = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/oauth2/v2.0/authorize?p=<Policy_Name>";

String mTokenEndpoint = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/oauth2/v2.0/token?p=<Policy_Name>";
```

运行以下代码创建 AuthorizationServiceConfiguration 对象：

```java
AuthorizationServiceConfiguration config =
        new AuthorizationServiceConfiguration(name, mAuthEndpoint, mTokenEndpoint);

// perform the auth request...
```

### <a name="authorizing"></a>授权

配置或检索授权服务配置后，可以构造授权请求。 若要创建该请求，需要提供以下信息：

* 之前记录的客户端 ID （应用程序 ID）。 例如， `00000000-0000-0000-0000-000000000000` 。
* 之前记录的自定义重定向 URI。 例如， `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` 。

[注册应用](#create-an-application)时应已保存这两项信息。

```java
AuthorizationRequest req = new AuthorizationRequest.Builder(
    config,
    clientId,
    ResponseTypeValues.CODE,
    redirectUri)
    .build();
```

有关如何完成余下的过程，请参阅 [AppAuth 指南](https://openid.github.io/AppAuth-Android/)。 如果需要快速开始创建一个正常运行的应用，请查看[我们的示例](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c)。 遵循 [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md) 中的步骤输入自己的 Azure AD B2C 配置。
