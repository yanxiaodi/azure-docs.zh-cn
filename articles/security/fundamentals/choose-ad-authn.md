---
title: 为 Azure AD 混合标识解决方案选择正确的身份验证方法 | Microsoft Docs
description: 本指南为中型到大型组织中负责为 Azure AD 混合标识解决方案选择身份验证方法的 CEO、CIO、CISO、首席标识架构师、企业架构师以及安全和 IT 决策制定者提供帮助。
keywords: ''
author: martincoetzer
ms.author: martinco
ms.date: 04/12/2018
ms.topic: article
ms.service: security
ms.subservice: security-fundamentals
ms.workload: identity
ms.openlocfilehash: ba9cda5aeebaf0764068a463cdb55f3ef5542ea3
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997820"
---
# <a name="choose-the-right-authentication-method-for-your-azure-active-directory-hybrid-identity-solution"></a>为 Azure Active Directory 混合标识解决方案选择正确的身份验证方法 

本文是本系列文章中的第一篇，旨在帮助组织实现完整的 Azure Active Directory (Azure AD) 混合标识解决方案。 此解决方案已概述为[混合标识数字转换框架](https://aka.ms/aadframework)。 它涵盖了组织可为实现可靠且安全的混合标识解决方案而关注的业务成果和目标。 

框架的第一个业务成果阐述在用户访问云应用时对组织确保身份验证过程安全的要求。 身份验证保护的业务成果的第一个业务目标是，用户可以使用其本地用户名和密码登录云应用。 此登录和身份验证过程使云中的所有内容成为可能。

对于希望将应用移动到云的组织来说，应首先考虑选择正确的身份验证方式。 请不要轻易做出此决策，原因如下：

1. 这是希望移动到云的组织做的第一个决定。 

2. 对于组织在云中的存在而言，身份验证方法是其关键组成部分。 它控制着对所有云数据和资源的访问。

3. 它是 Azure AD 中所有其他高级安全和用户体验功能的基础。

4. 身份验证方法在实现后很难更改。

标识是 IT 安全性的新控制平面。 因此，身份验证是组织对全新云世界的访问防护。 组织需要一个标识控制平面，以便增强其安全性并使其云应用免受入侵者攻击。

### <a name="out-of-scope"></a>超出范围
没有现有本地目录占用的组织不是本文的重点。 通常，那些仅在云中创建标识的企业不需要混合标识解决方案。 “仅限云”标识单独存在于云中，不与相应本地标识相关联。

## <a name="authentication-methods"></a>身份验证方法
当 Azure AD 混合标识解决方案成为你的新控制平面时，身份验证会是云访问的基础。 选择正确的身份验证方法是设置 Azure AD 混合标识解决方案至关重要的第一个决定。 请实现通过使用 Azure AD Connect（还可在云中预配用户）配置的身份验证方法。

若要选择身份验证方法，需要考虑时间、现有基础结构、复杂性和实现所选内容的成本。 这些因素对每个组织都不同，并可能随时间变化。 

>[!VIDEO https://www.youtube.com/embed/YtW2cmVqSEw]

Azure AD 支持以下适用于混合标识解决方案的身份验证方法。

### <a name="cloud-authentication"></a>云身份验证
选择此身份验证方法时，Azure AD 会处理用户的登录过程。 结合使用无缝单一登录 (SSO)，用户可以登录到云应用，无需重新输入其凭据。 如果使用的是云身份验证，可以从以下两个选项中选择： 

**Azure AD 密码哈希同步**。 这是在 Azure AD 中为本地目录对象启用身份验证的最简单方法。 用户可以使用其在本地使用的同一用户名和密码，不必部署任何其他基础结构。 Azure AD 的某些高级功能 (例如 Identity Protection 和[Azure AD 域服务](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)) 需要密码哈希同步, 无论选择哪种身份验证方法。

> [!NOTE] 
> 请勿以明文形式存储密码，或者使用 Azure AD 不可逆算法加密。 有关密码哈希同步实际过程的详细信息，请参阅[使用 Azure AD Connect 同步实现密码哈希同步](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)。 

**Azure AD 直通身份验证**。 通过使用在一个或多个本地服务器上运行的软件代理，为 Azure AD 身份验证服务提供简单密码验证。 服务器直接使用本地 Active Directory 验证用户，这将确保云中不发生密码验证。 

具有即时强制本地用户帐户状态、密码策略和登录小时数生效的安全要求的公司可能会使用此身份验证方法。 有关实际直通身份验证过程的详细信息，请参阅[使用 Azure AD 直通身份验证的用户登录](../../active-directory/hybrid/how-to-connect-pta.md)。

### <a name="federated-authentication"></a>联合身份验证
选择此身份验证方法时，Azure AD 将身份验证过程移交给单独的受信任身份验证系统（例如本地 Active Directory 联合身份验证服务 (AD FS)）来验证用户的密码。

身份验证系统可以提供其他高级身份验证要求。 例如，基于智能卡的身份验证或第三方多重身份验证。 有关详细信息，请参阅[部署 Active Directory 联合身份验证服务](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide)。

以下部分使用决策树帮助你决定哪种身份验证方法适合自己。 该决策树帮助你确定为 Azure AD 混合标识解决方案部署云身份验证还是联合身份验证。

## <a name="decision-tree"></a>决策树

> [!NOTE]
> 当 UserPrincipalName 被选为备用 ID 时, PTA 仅适用于备用 ID。 仅将本地 UserPrincipalName 从 AD 同步到 AAD。 有关详细信息, 请参阅[Pass 身份验证是否支持 "备用 ID" 作为用户名, 而不是 "userPrincipalName"？](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/how-to-connect-pta-faq#does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname)。

![Azure AD 身份验证决策树](./media/choose-ad-authn/azure-ad-authn-image1.png)

有关决策问题的详细信息：

1. Azure AD 可以处理不依赖于本地组件来验证密码的用户的登录。
2. Azure AD 可以将用户登录移交给受信任的身份验证提供 程序，例如 Microsoft 的 AD FS。
3. 如果需要在用户每次登录时应用用户级 Active Directory 安全策略，例如帐户过期、帐户禁用、密码过期、帐户锁定和登录小时数，则 Azure AD 需要一些本地组件。
4. Azure AD 本身不支持的登录功能：
   * 使用智能卡或证书进行登录。
   * 使用本地 MFA 服务器进行登录。
   * 使用第三方身份验证解决方案进行登录。
   * 多站点本地身份验证解决方案。
5. 无论你选择了哪种登录方法，Azure AD 标识保护都需要使用“密码哈希同步”来提供“凭据泄漏的用户”报告。 如果组织的主要登录方法失败，并且该方法是在发生故障事件之前配置的，则组织可以故障转移到“密码哈希同步”。

> [!NOTE]
> “Azure AD 标识保护”需要 [Azure AD Premium P2](https://azure.microsoft.com/pricing/details/active-directory/) 许可证。

## <a name="detailed-considerations"></a>详细的注意事项

### <a name="cloud-authentication-password-hash-synchronization"></a>云身份验证：密码哈希同步

* **工作量**。 密码哈希同步所需的有关部署、维护和基础结构的工作量最少。  此级别的工作量通常适用于只需用户登录到 Office 365、SaaS 应用以及其他基于 Azure AD 的资源的组织。 启用后，密码哈希同步即是 Azure AD Connect 同步过程的一部分，并且每两分钟运行一次。

* **用户体验**。 若要改善用户的登录体验，请将无缝 SSO 随密码哈希同步一起部署。 在用户登录时，无缝 SSO 将消除不必要的提示。

* **高级方案**。 如果组织选择，可以将来自标识的见解与 Azure AD Premium P2 的“Azure AD 标识保护”报告配合使用。 例如已泄漏凭据报告。 [使用密码哈希同步时](https://docs.microsoft.com/windows/access-protection/hello-for-business/hello-identity-verification), Windows Hello 企业版具有特定的要求。 [Azure AD 域服务](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)需要密码哈希同步才能在托管域中设置用户的公司凭据。

    需要多重身份验证和密码哈希同步的组织必须使用 Azure AD 多重身份验证或[条件访问自定义控件](../../active-directory/conditional-access/controls.md#custom-controls-preview)。 这些组织不能使用依赖于联合的第三方或本地多重因素身份验证方法。

> [!NOTE]
> Azure AD 条件访问需要[Azure AD Premium P1](https://azure.microsoft.com/pricing/details/active-directory/)许可证。

* **业务连续性**。 密码哈希同步与云身份验证结合在一起完全可以作为云服务使用，适合所有 Microsoft 数据中心。 若要确保密码哈希同步不会在长时间内无法工作，请在备用配置中部署另一个处于暂存模式的 Azure AD Connect 服务器。

* **注意事项**。 目前，密码哈希同步不会即时强制本地帐户状态更改生效。 在此情况下，除非用户帐户状态已同步到 Azure AD，否则用户有权访问云应用。 组织可能希望在管理员对本地用户帐户状态执行批量更新后运行新的同步循环来克服此限制。 例如禁用帐户。

> [!NOTE]
> 密码过期和帐户锁定状态当前不会借助 Azure AD Connect 同步到 Azure AD。 更改用户的密码并将 "*用户必须在下一次登录时更改密码*" 标志设置为时, 不会将密码哈希同步到 Azure AD Connect Azure AD, 直到用户更改其密码。

请参阅[实现密码哈希同步](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)了解相关部署步骤。

### <a name="cloud-authentication-pass-through-authentication"></a>云身份验证：直通身份验证  

* **工作量**。 对于直通身份验证，需要有一个或多个（我们建议三个）轻量代理安装在现有服务器上。 这些代理必须有权访问本地 Active Directory 域服务，包括本地 AD 域控制器。 它们需要具有对 Internet 的出站访问权限以及对域控制器的访问权限。 因此，不支持将这些代理部署在外围网络中。 

    直通身份验证需要对域控制器进行不受约束的网络访问。 所有流量都已进行加密并受限于身份验证请求。 有关此过程的详细信息，请参阅传递身份验证的[安全性深入研究](../../active-directory/hybrid/how-to-connect-pta-security-deep-dive.md)。

* **用户体验**。 若要改善用户的登录体验，请将无缝 SSO 随直通身份验证一起部署。 在用户登录后，无缝 SSO 将消除不必要的提示。

* **高级方案**。 直通身份验证在登录时强制实施本地帐户策略。 例如，如果本地用户的帐户状态为禁用、锁定或[密码过期](../../active-directory/hybrid/how-to-connect-pta-faq.md#what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication)，或者超出用户被允许登录的小时数，访问会被拒绝。 

    需要使用传递身份验证进行多重身份验证的组织必须使用 Azure 多重身份验证 (MFA) 或[条件访问自定义控件](../../active-directory/conditional-access/controls.md#custom-controls-preview)。 这些组织不能使用依赖于联合的第三方或本地多重因素身份验证方法。 高级功能都需要密码哈希同步已部署，无论你是否选择直通身份验证。 例如“标识保护”的已泄漏凭据报表。

* **业务连续性**。 我们建议部署两个额外的直通身份验证代理。 这些额外的代理是 Azure AD Connect 服务器上第一个代理之外的代理。 此额外部署将确保身份验证请求的高可用性。 如果部署了三个代理，关闭一个代理进行维护时另一个仍然可能失败。 

    在除了部署直通身份验证之外还部署密码哈希同步有另一个好处。 它将在主要身份验证方法不再可用时充当备份身份验证方法。

* **注意事项**。 当代理由于明显的本地故障而无法验证用户的凭据时，可以使用密码哈希同步作为直通身份验证的备份身份验证方法。 故障转移到密码哈希同步不会自动发生，并且必须使用 Azure AD Connect 手动切换登录方法。 

    有关直通身份验证的其他注意事项（包括备用 ID 支持），请参阅[常见问题](../../active-directory/hybrid/how-to-connect-pta-faq.md)。

请参阅[实现传递身份验证](../../active-directory/hybrid/how-to-connect-pta.md)了解部署步骤。

### <a name="federated-authentication"></a>联合身份验证

* **工作量**。 联合身份验证系统依赖于外部受信任系统对用户进行身份验证。 某些公司想要借助 Azure AD 混合标识解决方案重复使用现有联合系统投资。 联合系统的维护和管理超出 Azure AD 控制。 这由组织负责，组织可通过使用联合系统确保它已安全部署并可处理身份验证负载。 

* **用户体验**。 联合身份验证的用户体验依赖于联合服务器场功能、拓扑和配置的实现。 某些组织需要这种灵活性来调整和配置对联合服务器场的访问权限，以满足其安全要求。 例如，可以配置内部连接用户和设备以使用户自动登录，不会提示其输入凭据。 因为他们已登录到其设备，所以此配置将发挥作用。 必要时，某些高级安全功能将使用户的登录过程更加困难。

* **高级方案**。 客户有 Azure AD 本身不支持的身份验证要求时通常需要联合身份验证解决方案。 请参阅详细信息以帮助你[选择正确的登录选项](https://blogs.msdn.microsoft.com/samueld/2017/06/13/choosing-the-right-sign-in-option-to-connect-to-azure-ad-office-365/)。 请考虑以下常见要求：

  * 需要智能卡或证书的身份验证。
  * 需要联合标识提供者的本地 MFA 服务器或第三方多因素提供程序。
  * 使用第三方身份验证解决方案的身份验证。 请参阅[Azure AD 联合身份验证兼容性列表](../../active-directory/hybrid/how-to-connect-fed-compatibility.md)。
  * 需要 sAMAccountName（例如 DOMAIN\username）而不是用户主体名称 (UPN)（例如 user@domain.com）的登录。

* **业务连续性**。 联合身份验证系统通常需要负载均衡的服务器阵列（称为场）。 此场在内部网络和外围网络拓扑中配置，以便为身份验证请求确保高可用性。

    请将密码哈希同步以及联合身份验证部署为当主要身份验证方法不再可用时使用的备份身份验证方法。 例如，当本地服务器不可用时。 某些大型企业组织需要联合解决方案，以支持针对低延迟身份验证请求配置了异地 DNS 的多个 Internet 入口点。

* **注意事项**。 联合系统通常在本地基础结构方面需要更可观的投资。 大多数组织会选择此选项，前提是它们已有本地联合身份验证投资， 并且使用单标识提供者是非常强烈的业务需求。 与云身份验证解决方案相比，联合的操作和故障排除更加复杂。

对于无法在 Azure AD 中验证的不可路由域，需要进行额外配置才能实现用户 ID 登录。 此要求被称为“备用登录 ID 支持”。 相关限制和要求，请参阅[配置备用登录 ID](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)。 如果选择将第三方多重身份验证提供程序与联合身份验证配合使用，请确保该提供程序支持 WS-Trust，以允许设备加入 Azure AD。

有关部署步骤，请参阅[部署联合服务器](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/deploying-federation-servers)。

> [!NOTE] 
> 部署 Azure AD 混合标识解决方案时，必须实现 Azure AD Connect 的某一个受支持拓扑。 有关受支持和不受支持的配置详细信息，请参阅 [Azure AD Connect 拓扑](../../active-directory/hybrid/plan-connect-topologies.md)。

## <a name="architecture-diagrams"></a>体系结构关系图

下图扼要地绘出了每个可用于 Azure AD 混合标识解决方案的身份验证方法所需的高级体系结构组件。 此外还提供了概述，以便帮助你比较解决方案之间的差异。

* 密码哈希同步解决方案的简单性：

    ![使用密码哈希同步的 Azure AD 混合标识](./media/choose-ad-authn/azure-ad-authn-image2.png)

* 传递身份验证的代理要求, 使用两个代理进行冗余:

    ![使用直通身份验证的 Azure AD 混合标识](./media/choose-ad-authn/azure-ad-authn-image3.png)

* 组织的外围和内部网络中联合身份验证所需的组件：

    ![使用联合身份验证的 Azure AD 混合标识](./media/choose-ad-authn/azure-ad-authn-image4.png)

## <a name="comparing-methods"></a>比较方法

|注意事项|密码哈希同步 + 无缝 SSO|直通身份验证 + 无缝 SSO|使用 AD FS 进行联合身份验证|
|:-----|:-----|:-----|:-----|
|身份验证在何处发生？|在云中|在云中，在使用本地身份验证代理进行安全密码验证交换后|本地|
|除预配系统外的本地服务器要求是什么：Azure AD Connect？|None|为额外的每个身份验证代理提供一台服务器|两台或更多 AD FS 服务器<br><br>外围网络中的两台或更多 WAP 服务器|
|除预配系统外的本地 Internet 和网络的要求是什么？|无|从运行身份验证代理的服务器进行[出站 Internet 访问](../../active-directory/hybrid/how-to-connect-pta-quick-start.md)|对外围网络中的 WAP 服务器进行[入站 Internet 访问](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/ad-fs-requirements)<br><br>从外围网络中的 WAP 服务器对 AD FS 服务器进行入站网络访问<br><br>网络负载均衡|
|是否有 SSL 证书要求？|否|否|是|
|是否有运行状况监视解决方案？|不需要|[Azure Active Directory 管理中心](../../active-directory/hybrid/tshoot-connect-pass-through-authentication.md)提供的代理状态|[Azure AD Connect Health](../../active-directory/hybrid/how-to-connect-health-adfs.md)|
|用户是否可在公司网络内从加入域的设备单一登录到云资源？|是，同时使用[无缝 SSO](../../active-directory/hybrid/how-to-connect-sso.md)|是，同时使用[无缝 SSO](../../active-directory/hybrid/how-to-connect-sso.md)|是|
|支持哪些登录类型？|UserPrincipalName + 密码<br><br>通过使用[无缝 SSO](../../active-directory/hybrid/how-to-connect-sso.md) 进行的 Windows 集成身份验证<br><br>[备用登录 ID](../../active-directory/hybrid/how-to-connect-install-custom.md)|UserPrincipalName + 密码<br><br>通过使用[无缝 SSO](../../active-directory/hybrid/how-to-connect-sso.md) 进行的 Windows 集成身份验证<br><br>[备用登录 ID](../../active-directory/hybrid/how-to-connect-pta-faq.md)|UserPrincipalName + 密码<br><br>sAMAccountName + 密码<br><br>Windows 集成身份验证<br><br>[证书和智能卡身份验证](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)<br><br>[备用登录 ID](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)|
|是否支持 Windows Hello 企业版？|[密钥信任模型](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)|[密钥信任模型](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>*需要 Windows Server 2016 域功能级别*|[密钥信任模型](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[证书信任模型](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-key-trust-adfs)|
|有哪些多重身份验证选项？|[Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[具有条件访问的自定义控件 *](../../active-directory/conditional-access/controls.md)|[Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[具有条件访问的自定义控件 *](../../active-directory/conditional-access/controls.md)|[Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[Azure MFA 服务器](../../active-directory/authentication/howto-mfaserver-deploy.md)<br><br>[第三方 MFA](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)<br><br>[具有条件访问的自定义控件 *](../../active-directory/conditional-access/controls.md)|
|支持哪些用户帐户状态？|已禁用帐户<br>（最多 30 分钟延迟）|已禁用帐户<br><br>帐户已锁定<br><br>帐户已过期<br><br>密码已过期<br><br>登录小时数|已禁用帐户<br><br>帐户已锁定<br><br>帐户已过期<br><br>密码已过期<br><br>登录小时数|
|什么是条件性访问选项？|[使用 Azure AD Premium Azure AD 条件性访问](../../active-directory/conditional-access/overview.md)|[使用 Azure AD Premium Azure AD 条件性访问](../../active-directory/conditional-access/overview.md)|[使用 Azure AD Premium Azure AD 条件性访问](../../active-directory/conditional-access/overview.md)<br><br>[AD FS 声明规则](https://adfshelp.microsoft.com/AadTrustClaims/ClaimsGenerator)|
|是否支持阻止旧协议？|[是](../../active-directory/conditional-access/conditions.md)|[是](../../active-directory/conditional-access/conditions.md)|[是](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)|
|是否可以自定义登录页面的徽标、图像和说明？|[是，可使用 Azure AD Premium](../../active-directory/fundamentals/customize-branding.md)|[是，可使用 Azure AD Premium](../../active-directory/fundamentals/customize-branding.md)|[是](../../active-directory/hybrid/how-to-connect-fed-management.md)|
|支持哪些高级方案？|[智能密码锁定](../../active-directory/authentication/concept-sspr-howitworks.md)<br><br>[Azure AD Premium P2 的已泄漏凭据报告](../../active-directory/reports-monitoring/concept-risk-events.md)|[智能密码锁定](../../active-directory/authentication/howto-password-smart-lockout.md)|多站点低延迟身份验证系统<br><br>[AD FS Extranet 锁定](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)<br><br>[与第三方标识系统集成](../../active-directory/hybrid/how-to-connect-fed-compatibility.md)|

> [!NOTE] 
> Azure AD 条件访问中的自定义控件当前不支持设备注册。

## <a name="recommendations"></a>建议
标识系统确保用户有权访问云应用、迁移到云中的业务线应用以及在云中提供的业务线应用。 为了使授权用户保持效率并防止歹徒接触组织的敏感数据，身份验证会控制对应用的访问。

无论选择了哪种身份验证方法，都请为其使用或启用密码哈希同步，原因如下：

1. **高可用性和灾难恢复**。 直通身份验证和联合身份验证依赖于本地基础结构。 对于直通身份验证，本地占用的设备包括直通身份验证代理所需的服务器硬件和网络。 对于联合身份验证，本地占用的设备更多。 它需要外围网络中的服务器来代理身份验证请求，并且还需要内部联合服务器。 

    若要避免单点故障，请部署冗余的服务器。 这样，如果任何组件出现故障，系统均会始终为身份验证请求提供服务。 直通身份验证和联合身份验证都也依赖于域控制器来响应身份验证请求（也可能会失败）。 这些组件中的许多组件都需要维护以保持正常运行。 如果未正确计划并实施维护，很有可能会发生服务中断。 请使用密码哈希同步避免服务中断，因为 Microsoft Azure AD 云身份验证服务可在全球扩展且始终可用。

2. **本地服务中断生存**。  网络攻击或灾难造成的本地服务中断可能导致从品牌信誉损失到瘫痪的组织无法处理攻击等一系列的严重后果。 最近，许多组织都受到了恶意软件（包括有针对性的勒索软件）的攻击，导致其本地服务器停机。 Microsoft 在帮助客户处理这些类型的攻击时发现两种类型的组织：

   * 先前已启用密码哈希同步的组织更改了其身份验证方法以使用密码哈希同步。 它们在数小时内即可重新联机。 通过 Office 365 使用对电子邮件的访问权限，可以解决问题并访问其他基于云的工作负荷。

   * 之前未启用密码哈希同步的组织必须使用不受信任的外部消费者电子邮件系统来解决问题。 在这些情况下, 在用户能够再次登录到基于云的应用之前, 需要几周的时间来还原其本地标识基础结构。

3. **标识保护**。 在云中保护用户的最佳方法之一是将 Azure AD 标识保护与 Azure AD Premium P2 配合使用。 Microsoft 不断扫描 Internet 以查找歹徒在暗网上销售和提供的用户和密码列表。 Azure AD 可以用此信息验证组织的任意用户名和密码是否泄漏。 所以，无论使用哪种身份验证方法（无论其是联合身份验证还是直通身份验证），启用密码哈希同步都至关重要。 泄漏的凭据将以报表形式提供。 使用此信息可阻止或强制用户在尝试使用泄漏的密码登录时更改其密码。

最后，根据 [Gartner](https://info.microsoft.com/landingIAMGartnerreportregistration.html) 提供的信息，Microsoft 拥有功能最完备的标识和访问管理功能集。 Microsoft 每月处理 [4,500 亿个身份验证请求](https://www.microsoft.com/en-us/security/intelligence-report)，几乎可从任何设备提供对数千个 SaaS 应用程序（如 Office 365）的访问权限。 

## <a name="conclusion"></a>结束语

本文概述了多种身份验证选项，组织可以配置和部署它们以支持对云应用的访问。 为了满足各种业务、安全和技术要求，组织可以在密码哈希同步、直通身份验证和联合身份验证之间进行选择。 

请考虑每一种身份验证方法。 部署解决方案的工作量以及登录过程的用户体验是否满足你的业务要求？ 请评估组织是否需要每种身份验证方法的高级方案和业务连续性功能。 最后，请评估每种身份验证方法的注意事项。 其中是否有任何注意事项会阻止你实现你的选择？

## <a name="next-steps"></a>后续步骤

当今世界中，威胁一天 24 小时内都存在，且可能来自任意位置。 请实现正确的身份验证方法，它将减轻安全风险并保护标识。

[开始使用](../../active-directory/fundamentals/get-started-azure-ad.md) Azure AD 并为组织部署正确的身份验证解决方案。

如果你正在考虑从联合身份验证迁移到云身份验证，请了解有关[更改登录方法](../../active-directory/hybrid/plan-connect-user-signin.md)的详细信息。 为帮助你计划和实施迁移，请使用[这些项目部署计划](https://aka.ms/deploymentplans)。
