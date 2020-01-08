---
title: 在 Azure Active Directory B2C 中使用自定义策略将访问令牌传递给应用程序
description: 了解如何在 Azure Active Directory B2C 中使用自定义策略将 OAuth2.0 标识提供者的访问令牌作为声明传递给应用程序。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b6795af0829a288c36cad5b848fed50a99dc1bfc
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2019
ms.locfileid: "69510126"
---
# <a name="pass-an-access-token-through-a-custom-policy-to-your-application-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自定义策略将访问令牌传递给应用程序

Azure Active Directory B2C (Azure AD B2C) 中的[自定义策略](active-directory-b2c-get-started-custom.md)为应用程序的用户提供了使用标识提供者注册或登录的机会。 当发生此行为时，Azure AD B2C 会从标识提供者收到一个[访问令牌](active-directory-b2c-reference-tokens.md)。 Azure AD B2C 使用该令牌来检索有关用户的信息。 你在自定义策略中添加声明类型和输出声明来将该令牌传递给你在 Azure AD B2C 中注册的应用程序。

Azure AD B2C 支持传递 [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) 和 [OpenID Connect](active-directory-b2c-reference-oidc.md) 标识提供者的访问令牌。 对于所有其他标识提供者，声明将返回空白。

## <a name="prerequisites"></a>先决条件

* 自定义策略使用 OAuth 2.0 或 OpenID Connect 标识提供者进行配置。

## <a name="add-the-claim-elements"></a>添加声明元素

1. 打开 *TrustframeworkExtensions.xml* 文件，向 **ClaimsSchema** 元素中添加标识符为 `identityProviderAccessToken` 的以下 **ClaimType** 元素：

    ```XML
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="identityProviderAccessToken">
          <DisplayName>Identity Provider Access Token</DisplayName>
          <DataType>string</DataType>
          <AdminHelpText>Stores the access token of the identity provider.</AdminHelpText>
        </ClaimType>
        ...
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. 针对你需要其访问令牌的每个 OAuth 2.0 标识提供者，向 **TechnicalProfile** 元素中添加 **OutputClaim** 元素。 下面的示例显示了添加到 Facebook 技术配置文件的该元素：

    ```XML
    <ClaimsProvider>
      <DisplayName>Facebook</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Facebook-OAUTH">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="{oauth2:access_token}" />
          </OutputClaims>
          ...
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

3. 保存 *TrustframeworkExtensions.xml* 文件。
4. 打开你的信赖方策略文件，例如 *SignUpOrSignIn.xml*，向 **TechnicalProfile** 中添加 **OutputClaim** 元素：

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="idp_access_token"/>
        </OutputClaims>
        ...
      </TechnicalProfile>
    </RelyingParty>
    ```

5. 保存策略文件。

## <a name="test-your-policy"></a>测试策略

在 Azure AD B2C 中测试应用程序时，可以使 Azure AD B2C 令牌返回到 `https://jwt.ms` 以便能够在其中查看声明，这可能很有用处。

### <a name="upload-the-files"></a>上传文件

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 通过单击顶部菜单中的 "**目录 + 订阅**" 筛选器并选择包含租户的目录, 确保使用的是包含 Azure AD B2C 租户的目录。
3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”。
4. 选择“标识体验框架”。
5. 在“自定义策略”页上，单击“上传策略”。
6. 选择“覆盖策略(若存在)”，然后搜索并选择 *TrustframeworkExtensions.xml* 文件。
7. 选择“上传”。
8. 针对信赖方文件（例如 *SignUpOrSignIn.xml*）重复步骤 5 到 7。

### <a name="run-the-policy"></a>运行策略

1. 打开你更改的策略。 例如，*B2C_1A_signup_signin*。
2. 对于“应用程序”，选择你之前注册的应用程序。 “回复 URL”应当显示 `https://jwt.ms` 才能看到以下示例中的令牌。
3. 选择“立即运行”。

    应会看到类似于以下示例的内容：

    ![jwt.ms 中突出显示了 idp_access_token 块的已解码令牌](./media/idp-pass-through-custom/idp-pass-through-custom-token.PNG)

## <a name="next-steps"></a>后续步骤

详细了解[Azure Active Directory B2C 令牌参考](active-directory-b2c-reference-tokens.md)中的令牌。
