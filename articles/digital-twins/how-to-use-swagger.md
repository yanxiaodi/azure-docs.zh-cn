---
title: 了解如何使用 Azure 数字孪生 Swagger 参考文档 | Microsoft Docs
description: 了解如何使用 Azure 数字孪生 Swagger 参考文档。
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: v-adgera
ms.custom: seodec18
ms.openlocfilehash: 1344b86b9fa922dc22870dda495ff367a511b8ca
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640264"
---
# <a name="azure-digital-twins-swagger-reference-documentation"></a>Azure 数字孪生 Swagger 参考文档

每个预配的 Azure 数字孪生实例都包含自己生成的 Swagger 参考文档。

[Swagger](https://swagger.io/) 或 [OpenAPI](https://www.openapis.org/) 将复杂的 API 信息整合到一个交互式和语言无关的参考资源中。 Swagger 提供有关要使用哪些 JSON 有效负载、HTTP 方法和特定终结点对 API 执行操作的关键参考资料。

## <a name="swagger-summary"></a>Swagger 摘要

Swagger 提供 API 的交互式摘要，这包括：

* API 和对象模型信息。
* REST API 终结点，用于指定必需的请求有效负载、标头、参数、上下文路径和 HTTP 方法。
* API 功能测试。
* 示例响应信息，用于验证和确认 HTTP 响应。
* 错误代码信息。

Swagger 是一种非常便利的工具，可用于帮助执行对 Azure 数字孪生管理 API 作出的开发和测试调用。

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

## <a name="reference-material"></a>参考资料

自动生成的 Swagger 参考资料提供了重要概念的快速概述、可用的管理 API 终结点和每个对象模型的描述，用于帮助开发和测试。

精简摘要对 API 进行了描述。

[![Swagger 顶部](media/how-to-use-swagger/swagger-management-top-img.png)](media/how-to-use-swagger/swagger-management-top-img.png#lightbox)

还列出了管理 API 对象模型。

[![Swagger 模型](media/how-to-use-swagger/swagger-management-models-img.png)](media/how-to-use-swagger/swagger-management-models-img.png#lightbox)

可以选择每个列出的对象模型来获取关键属性的更详细摘要：

[![Swagger 模型](media/how-to-use-swagger/swagger-management-model-img.png)](media/how-to-use-swagger/swagger-management-model-img.png#lightbox)

生成的 Swagger 对象模型可方便地用于查看所有可用的 Azure 数字孪生[对象和 API](./concepts-objectmodel-spatialgraph.md)。 开发人员在 Azure 数字孪生上生成解决方案时可以使用此资源。

## <a name="endpoint-summary"></a>终结点摘要

Swagger 还对编写管理 API 的所有终结点提供了全面概述。

每个列出的终结点还包括所需的请求信息，例如：

* 必需的参数。
* 必需的参数数据类型。
* 用于访问资源的 HTTP 方法。

[![Swagger 终结点](media/how-to-use-swagger/swagger-management-endpoints-img.png)](media/how-to-use-swagger/swagger-management-endpoints-img.png#lightbox)

若要查看更详细的概述，请选择每个资源。

## <a name="use-swagger-to-test-endpoints"></a>使用 Swagger 测试终结点

Swagger 提供的一个强大功能是能够直接通过文档 UI 测试 API 终结点。

选择特定的终结点后，你会看到**试用**。

[![Swagger 尝试](media/how-to-use-swagger/swagger-management-try-img.png)](media/how-to-use-swagger/swagger-management-try-img.png#lightbox)

展开该部分会显示每个必需字段和可选参数的输入字段。 输入正确的值，并选择“执行”。

[![已尝试 Swagger](media/how-to-use-swagger/swagger-management-tried-img.png)](media/how-to-use-swagger/swagger-management-tried-img.png#lightbox)

执行测试后，可以验证响应数据。

## <a name="swagger-response-data"></a>Swagger 响应数据

每个列出的终结点还包括用以验证开发和测试的响应正文数据。 这些示例包括对于成功的 HTTP 请求你希望看到的状态代码和 JSON。

[![Swagger 响应](media/how-to-use-swagger/swagger-management-response-img.png)](media/how-to-use-swagger/swagger-management-response-img.png#lightbox)

这些示例还包括用以帮助调试或改进失败测试的错误代码。

## <a name="swagger-oauth-20-authorization"></a>Swagger OAuth 2.0 授权

> [!NOTE]
> * 创建 Azure 数字孪生资源的用户主体将具有空间管理员角色分配, 并将能够为其他用户创建其他角色分配。 此类用户及其角色可以获得调用 Api 的权限。

1. 按照[本快速入门](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad)中的步骤进行操作, 或[使用 Azure Active Directory 旧版注册 Azure 数字孪生应用](./how-to-use-legacy-aad.md)来创建和配置 Azure AD 应用程序。 或者, 你可以重复使用现有的应用注册。

1. 将以下回复 url 添加到应用注册:

    ```plaintext
    https://YOUR_SWAGGER_URL/ui/oauth2-redirect-html
    ```
    | 姓名  | 替换为 | 示例 |
    |---------|---------|---------|
    | YOUR_SWAGGER_URL | 你的管理 REST API 在门户中找到的文档 URL  | `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger` |

1. 复制 Azure AD 应用的 ID。

完成 Azure Active Directory 注册后:

1. 在 swagger 页面上选择 "**授权**" 按钮。

    [![选择 "Swagger 授权" 按钮](media/how-to-use-swagger/swagger-select-authorize-btn.png)](media/how-to-use-swagger/swagger-select-authorize-btn.png#lightbox)

1. 将应用程序 ID 粘贴到 " **client_id** " 字段。

    [![Swagger client_id 字段](media/how-to-use-swagger/swagger-auth-form.png)](media/how-to-use-swagger/swagger-auth-form.png#lightbox)

1. 然后, 将重定向到以下成功模式。

    [![Swagger 重定向模式](media/how-to-use-swagger/swagger-auth-redirect-img.png)](media/how-to-use-swagger/swagger-auth-redirect-img.png#lightbox)

若要详细了解如何以交互方式测试受 OAuth 2.0 保护的请求，请参阅[官方文档](https://swagger.io/docs/specification/authentication/oauth2/)。

## <a name="next-steps"></a>后续步骤

- 若要阅读有关 Azure 数字孪生对象模型和空间智能图的详细信息，请阅读[了解 Azure 数字孪生对象模型](./concepts-objectmodel-spatialgraph.md)。

- 若要了解如何使用管理 API 进行身份验证，请阅读[使用 API 进行身份验证](./security-authenticating-apis.md)。