---
title: 为 Azure API 管理实例配置自定义域名 | Microsoft Docs
description: 本主题介绍了如何为 Azure API 管理实例配置自定义域名。
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/12/2019
ms.author: apimpm
ms.openlocfilehash: 52eeedf4651cee012569034ae635a4736b3f3335
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2019
ms.locfileid: "70135540"
---
# <a name="configure-a-custom-domain-name"></a>配置自定义域名

创建 Azure API 管理服务实例时，Azure 会为其分配 azure-api.net 的子域（例如，`apim-service-name.azure-api.net`）。 不过，你可以使用自己的自定义域名（例如 **contoso.com**）公开 API 管理终结点。 本教程演示了如何将现有的自定义 DNS 名称映射到 API 管理实例公开的终结点。

> [!WARNING]
> 想要使用证书固定改进其应用程序安全性的客户必须使用自定义域名和他们管理的证书，而不是使用默认证书。 改为固定默认证书的客户将硬依赖于他们不控制的证书属性，建议不要这样做。

## <a name="prerequisites"></a>先决条件

若要执行本文中所述的步骤，必须具有：

-   一个有效的 Azure 订阅。

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   API 管理实例。 有关详细信息，请参阅[创建 Azure API 管理实例](get-started-create-service-instance.md)。
-   由你或你的组织拥有的自定义域名。 本主题不提供有关如何购买自定义域名的说明。
-   托管在 DNS 服务器上的 CNAME 记录, 用于将自定义域名映射到 API 管理实例的默认域名。 本主题不提供有关如何承载 CNAME 记录的说明。
-   必须具有有效的带有公钥和私钥 (.PFX) 的证书。 使用者或使用者可选名称 (SAN) 必须与域名匹配（这使得 API 管理实例可以通过 SSL 安全地公开 URL）。

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>使用 Azure 门户设置自定义域名

1. 在 [Azure 门户](https://portal.azure.com/)中导航到自己的 API 管理实例。
1. 选择 "**自定义域**"。

    可以为许多终结点分配自定义域名。 当前有以下终结点可用：

    - **网关**(默认值为`<apim-service-name>.azure-api.net`:),
    - **门户**（默认值为：`<apim-service-name>.portal.azure-api.net`），
    - **管理**（默认值为：`<apim-service-name>.management.azure-api.net`），
    - **SCM**（默认值为：`<apim-service-name>.scm.azure-api.net`）。

    > [!NOTE]
    > 只有可用于配置的**网关**终结点才可用于配置。
    > 可以更新所有终结点或者更新其中的一部分。 通常, 客户更新**网关**(此 URL 用于调用通过 api 管理公开的 api) 和**门户**(开发人员门户 URL)。
    > **管理**终结点和**SCM**终结点仅由 API 管理实例所有者在内部使用, 因此不太频繁地分配自定义域名。
    > **高级**层支持为**网关**终结点设置多个主机名。

1. 选择要更新的终结点。
1. 在右侧窗口中，单击“自定义”。

    - 在“自定义域名”中，指定要使用的名称。 例如， `api.contoso.com` 。
    - 在**证书**中，从密钥保管库中选择证书。 如果证书受密码保护，你还可以上传有效的 .PFX 文件并提供其**密码**。

    > [!NOTE]
    > 通配符域名 (例如`*.contoso.com` ) 在所有层中受支持, 但使用层除外。

    > [!TIP]
    > 我们建议使用 Azure Key Vault 来管理证书并将其设置为“自动轮换”。
    > 如果使用 Azure 密钥保管库来管理自定义域 SSL 证书，请确保该证书[作为证书](https://docs.microsoft.com/rest/api/keyvault/CreateCertificate/CreateCertificate)而不是机密插入到密钥保管库中。
    >
    > 若要提取 SSL 证书，API 管理必须使列表对包含证书的 Azure Key Vault 具有“获取机密”权限。 使用 Azure 门户时，所有必要的配置步骤都将自动完成。 使用命令行工具或管理 API 时，必须手动授予这些权限。 此过程分为两个步骤。 首先，使用 API 管理实例上的“托管标识”页确保已启用“托管标识”并记下该页上显示的主体 ID。 其次，在包含证书的 Azure Key Vault 上为此主体 ID 提供权限列表并获取机密权限。
    >
    > 如果证书设置为“自动轮换”，API 管理将自动选取最新版本，而不会对服务造成任何停机（如果你的 API 管理层具有SLA - 即在除了开发人员层之外的所有层中）。

1. 单击“应用”。

    > [!NOTE]
    > 分配证书的过程可能需要 15 分钟或更久，这取决于部署规模。 开发人员 SKU 停机, 基本和更高的 Sku 不会造成停机。

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="dns-configuration"></a>DNS 配置

为自定义域名配置 DNS 时, 有两个选项:

-   配置一个 CNAME 记录, 该记录指向配置的自定义域名的终结点。
-   配置指向你的 API 管理网关 IP 地址的 A 记录。

> [!NOTE]
> 尽管 API 管理实例 IP 地址是静态的, 但在少数情况下, 它可能会更改。 因此, 建议在配置自定义域时使用 CNAME。 选择 DNS 配置方法时要考虑这一点。 有关详细信息, 请参阅[API MANANAGEMENT 常见问题解答](api-management-faq.md#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)。

## <a name="next-steps"></a>后续步骤

[升级和缩放你的服务](upgrade-and-scale.md)
