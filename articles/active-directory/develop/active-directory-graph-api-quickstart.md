---
title: 如何使用 Azure AD 图形 API
description: Azure Active Directory (Azure AD) 图形 API 通过 OData REST API 终结点提供对 Azure AD 的编程访问权限。 应用程序可以使用 Azure AD 图形 API 对目录数据和对象执行创建、读取、更新和删除 (CRUD) 操作。
services: active-directory
documentationcenter: n/a
author: rwike77
manager: CelesteDG
editor: ''
tags: ''
ms.assetid: 9dc268a9-32e8-402c-a43f-02b183c295c5
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: ryanwi
ms.reviewer: sureshja
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ee6c4630205561eb8beb19062520f8ae2a35e1b
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073910"
---
# <a name="how-to-use-the-azure-ad-graph-api"></a>如何：使用 Azure AD 图形 API

> [!IMPORTANT]
> 强烈建议使用 [Microsoft Graph](https://developer.microsoft.com/graph)（而非 Azure AD Graph API）访问 Azure Active Directory 资源。 目前，我们在集中开发 Microsoft Graph，未计划进一步改进 Azure AD Graph API。 Azure AD 图形 API 可能仍适用的方案数量非常有限;有关详细信息, 请参阅[Microsoft Graph 或 Azure AD graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph)博客文章并将[Azure AD Graph 应用迁移到 Microsoft Graph](https://docs.microsoft.com/graph/migrate-azure-ad-graph-overview)。

Azure Active Directory (Azure AD) 图形 API 通过 OData REST API 终结点提供对 Azure AD 的编程访问权限。 应用程序可以使用 Azure AD 图形 API 对目录数据和对象执行创建、读取、更新和删除 (CRUD) 操作。 例如，可以使用 Azure AD 图形 API 创建新用户、查看或更新用户的属性、更改用户的密码、检查基于角色的访问的组成员身份、禁用或删除用户。 有关 Azure AD 图形 API 功能和应用程序方案的详细信息, 请参阅[Azure AD 图形 API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)和[Azure AD 必备组件](https://msdn.microsoft.com/library/hh974476.aspx)。 Azure AD 图形 API 仅适用于工作或学校/组织帐户。

本文适用于 Azure AD 图形 API。 有关与 Microsoft Graph API 相关的类似信息，请参阅[使用 Microsoft Graph API](https://developer.microsoft.com/graph/docs/concepts/use_the_api)。

## <a name="how-to-construct-a-graph-api-url"></a>如何构造图形 API URL

在图形 API 中，若要访问要对其执行 CRUD 操作的目录数据和对象（即资源或实体），可以使用基于开放数据 (OData) 协议的 URL。 在图形 API 中使用的 URL 包括四个主要部分：服务根、租户标识符、资源路径和查询字符串选项：`https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`。 以下面的 URL 为例：`https://graph.windows.net/contoso.com/groups?api-version=1.6`。

* **服务根**：在 Azure AD 图形 API 中，服务根始终是 https://graph.windows.net 。
* **租户标识符**：此部分可以是已验证（注册）的域名，在前面示例中为 contoso.com。 也可以是租户对象 ID 或者“myorganization”或“me”别名。 有关详细信息, 请参阅[Azure AD 图形 API 中的实体和操作进行寻址](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview)。
* **资源路径**：URL 的此部分标识要交互的资源（用户、组、特定用户或特定组等等）。在上面示例中，它是用于对资源集寻址的顶级“组”。 也可以对特定的实体寻址，例如“users/{objectId}”或“users/userPrincipalName”。
* **查询参数**：问号 (?) 用于分隔资源路径部分与查询参数部分。 需要对 Azure AD 图形 API 中的所有请求提供“api-version”查询参数。 Azure AD 图形 API 还支持以下 OData 查询选项： **$filter**、 **$orderby**、 **$expand**、 **$top** 和 **$format**。 当前不支持以下查询选项： **$count**、 **$inlinecount** 和 **$skip**。 有关详细信息，请参阅 [Azure AD 图形 API 支持的查询、筛选和分页选项](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options)。

## <a name="graph-api-versions"></a>图形 API 版本

可以在 api-version 查询参数中指定图形 API 请求的版本。 对于版本 1.5 及更高版本，使用数字版本值：api-version=1.6。 对于早期版本，使用遵循 YYYY-MM-DD 格式的日期字符串；例如，api-version=2013-11-08。 对于预览功能，可使用字符串“beta”；例如，api-version=beta。 有关图形 API 版本之间的差异的详细信息, 请参阅[Azure AD 图形 API 版本控制](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning)。

## <a name="graph-api-metadata"></a>图形 API 元数据

要返回Azure AD 图形 API 元数据文件，请在 URL 中的租户标识符后面添加“$metadata”段。例如，以下 URL 将返回演示公司元数据：`https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.6`。 可以在 Web 浏览器的地址栏中输入此 URL 来查看元数据。 返回的 CSDL 元数据文档描述了实体和复杂类型及其属性，以及请求的图形 API 版本公开的函数和操作。 省略 api-version 参数会返回最新版本的元数据。

## <a name="common-queries"></a>常见查询

[Azure AD 图形 API 常见查询](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries)列出了可与 Azure AD 图形一起使用的常见查询, 包括可用于访问目录中的顶层资源的查询, 以及用于在目录中执行操作的查询。

例如，`https://graph.windows.net/contoso.com/tenantDetails?api-version=1.6` 将返回目录 contoso.com 的公司信息。

`https://graph.windows.net/contoso.com/users?api-version=1.6` 将列出目录 contoso.com 中的所有用户对象。

## <a name="using-the-azure-ad-graph-explorer"></a>使用 Azure AD 图形资源管理器
生成应用程序时，可以使用 Azure AD 图形 API 的 Azure AD 图形资源管理器来查询目录数据。

以下屏幕截图是导航到 Azure AD 图形资源管理器，登录并输入 `https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.6` 以显示已登录用户目录中的所有用户时会看到的输出：

![Azure AD 图形 API 资源管理器中的示例输出](./media/active-directory-graph-api-quickstart/graph_explorer.png)

**加载 Azure AD 图形资源管理器**：若要加载工具，请导航到 [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/)。 单击“登录”，并使用 Azure AD 帐户凭据登录，以针对租户运行 Azure AD 图形资源管理器。 如果针对自己的租户运行 Azure AD 图形资源管理器，则你或管理员需要在登录期间表示同意。 如果拥有 Office 365 订阅，则会自动拥有 Azure AD 租户。 用于登录 Office 365 的凭据事实上就是 Azure AD 帐户，可以在 Azure AD 图形资源管理器中使用这些凭据。

**运行查询**：要运行查询，请在请求文本框中键入查询，然后单击“获取”或单击 Enter 键。 结果会显示在响应框中。 例如，`https://graph.windows.net/myorganization/groups?api-version=1.6` 将列出已登录用户目录中的所有组对象。

请注意，Azure AD 图形资源管理器具有以下功能与限制：

* 对资源集的自动完成功能。 若要查看此功能，请单击请求文本框（公司 URL 出现的位置）。 可以从下拉列表中选择资源集。
* 请求历史记录。
* 支持“me”和“myorganization”寻址别名。 例如，可以使用 `https://graph.windows.net/me?api-version=1.6` 来返回登录用户的用户对象，或者使用 `https://graph.windows.net/myorganization/users?api-version=1.6` 来返回已登录用户目录中的所有用户。
* 支持使用 `POST`、`GET`、`PATCH` 和 `DELETE` 对自己的目录执行完整的 CRUD 操作。
* 响应标头部分。 此部分可用来帮助排查运行查询时所发生的问题。
* 具有展开和折叠功能的 JSON 响应查看器。
* 不支持显示或上传缩略图照片。

## <a name="using-fiddler-to-write-to-the-directory"></a>使用 Fiddler 写入目录

在本快速入门指南中，可以使用 Fiddler Web 调试器来练习对 Azure AD 目录执行“写入”操作。 例如，可以获取和上传用户的个人资料照片（无法使用 Azure AD 图形资源管理器实现此目的）。 有关如何安装 Fiddler 的详细信息，请参阅 [https://www.telerik.com/fiddler](https://www.telerik.com/fiddler)。

以下示例使用 Fiddler Web 调试器在 Azure AD 目录中创建一个新的安全组“MyTestGroup”。

**获取访问令牌**：若要访问 Azure AD Graph，客户端需要先成功地向 Azure AD 进行身份验证。 有关详细信息, 请参阅[Azure AD 的身份验证方案](authentication-scenarios.md)。

**编写和运行查询**：完成以下步骤：

1. 打开 Fiddler Web 调试器并切换到“编辑器”选项卡。
2. 由于要创建新的安全组，因此请从下拉菜单中选择“发布”作为 HTTP 方法。 有关组对象的操作和权限的详细信息, 请参阅 Azure AD 图中的[组](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#group-entity) [REST API 引用](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)。
3. 在“发布”旁边的字段中，键入以下请求 URL：`https://graph.windows.net/{mytenantdomain}/groups?api-version=1.6`。
   
   > [!NOTE]
   > 必须将 {mytenantdomain} 替换成自己的 Azure AD 目录的域名。

4. 在紧靠在“发布”下拉列表下面的字段中，键入以下 HTTP 标头：
   
    ```
   Host: graph.windows.net
   Authorization: Bearer <your access token>
   Content-Type: application/json
   ```
   
   > [!NOTE]
   > 将 &lt;your access token&gt; 替换为 Azure AD 目录的访问令牌。

5. 在“请求正文”字段中键入以下 JSON：
   
    ```
        {
            "displayName":"MyTestGroup",
            "mailNickname":"MyTestGroup",
            "mailEnabled":"false",
            "securityEnabled": true
        }
   ```
   
    有关创建组的详细信息，请参阅[创建组](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#CreateGroup)。

有关 Graph 公开的 Azure AD 实体和类型的详细信息, 以及有关可以使用 Graph 对它们执行的操作的信息, 请参阅[Azure AD Graph REST API 引用](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)。

## <a name="next-steps"></a>后续步骤

* 了解有关 [Azure AD 图形 API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) 的详细信息
* 了解[Azure AD 图形 API 权限范围](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)的详细信息
