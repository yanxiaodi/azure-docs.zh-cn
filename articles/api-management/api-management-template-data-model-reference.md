---
title: Azure API 管理模板数据模型参考 | Microsoft 文档
description: 了解数据模型中常用项目的实体和类型表示形式，这些数据模型适用于 Azure API 管理中的开发人员门户模板。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: b0ad7e15-9519-4517-bb73-32e593ed6380
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/05/2017
ms.author: apimpm
ms.openlocfilehash: 323b3effb4c4a63d03ab7ea5251e0d59271d9dcd
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072138"
---
# <a name="azure-api-management-template-data-model-reference"></a>Azure API 管理模板数据模型参考
本主题介绍数据模型中常用项目的实体和类型表示形式，这些数据模型适用于 Azure API 管理中的开发人员门户模板。  
  
 如需详细了解如何使用模板，请参阅[如何使用模板自定义 API 管理开发人员门户](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/)。  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

在“消耗”层中，开发人员门户不可用。

## <a name="reference"></a>参考

-   [API](#API)  
-   [API 摘要](#APISummary)  
-   [应用程序](#Application)  
-   [附件](#Attachment)  
-   [代码示例](#Sample)  
-   [评论](#Comment)  
-   [筛选](#Filtering)  
-   [标头](#Header)  
-   [HTTP 请求](#HTTPRequest)  
-   [HTTP 响应](#HTTPResponse)  
-   [问题](#Issue)  
-   [操作](#Operation)  
-   [操作菜单](#Menu)  
-   [操作菜单项](#MenuItem)  
-   [分页](#Paging)  
-   [Parameter](#Parameter)  
-   [产品](#Product)  
-   [提供程序](#Provider)  
-   [表示形式](#Representation)  
-   [订阅](#Subscription)  
-   [订阅摘要](#SubscriptionSummary)  
-   [用户帐户信息](#UserAccountInfo)  
-   [用户登录](#UseSignIn)  
-   [用户注册](#UserSignUp)  
  
##  <a name="API"></a> API  
 `API` 实体具有以下属性：  
  
|属性|type|描述|  
|--------------|----------|-----------------|  
|`id`|string|资源标识符。 唯一标识当前 API 管理服务实例中的 API。 值是有效的相对 URL，采用 `apis/{id}` 格式，其中 `{id}` 是 API 标识符。 此属性为只读。|  
|`name`|string|API 的名称。 不得为空。 最大长度为 100 个字符。|  
|`description`|string|API 的说明。 不得为空。 可以包含 HTML 格式标记。 最大长度为 1000 个字符。|  
|`serviceUrl`|string|实现此 API 的后端服务的绝对 URL。|  
|`path`|string|相对 URL，用于唯一标识此 API 及其在 API 管理服务实例中的所有资源路径。 可将其追加到在服务实例创建过程中指定的 API 终结点基 URL，构成此 API 的公共 URL。|  
|`protocols`|数字数组|说明可在哪些协议上调用此 API 中的操作。 允许的值为 `1 - http` 和/或 `2 - https`。|  
|`authenticationSettings`|[授权服务器身份验证设置](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#AuthenticationSettings)|此 API 中包含的身份验证设置的集合。|  
|`subscriptionKeyParameterNames`|object|可选属性，可用于指定包含订阅密钥的查询参数和/或标头参数的自定义名称。 如果存在此属性，则其必须包含以下两个属性中的至少一个。<br /><br /> `{   "subscriptionKeyParameterNames":   {     "query": “customQueryParameterName",     "header": “customHeaderParameterName"   } }`|  
  
##  <a name="APISummary"></a> API 摘要  
 `API summary` 实体具有以下属性：  
  
|属性|类型|描述|  
|--------------|----------|-----------------|  
|`id`|string|资源标识符。 唯一标识当前 API 管理服务实例中的 API。 值是有效的相对 URL，采用 `apis/{id}` 格式，其中 `{id}` 是 API 标识符。 此属性为只读。|  
|`name`|string|API 的名称。 不得为空。 最大长度为 100 个字符。|  
|`description`|string|API 的说明。 不得为空。 可以包含 HTML 格式标记。 最大长度为 1000 个字符。|  
  
##  <a name="Application"></a> 应用程序  
 `application` 实体具有以下属性：  
  
|属性|类型|描述|  
|--------------|----------|-----------------|  
|`Id`|string|应用程序的唯一标识符。|  
|`Title`|string|应用程序的标题。|  
|`Description`|string|应用程序的说明。|  
|`Url`|URI|应用程序的 URI。|  
|`Version`|string|应用程序的版本信息。|  
|`Requirements`|string|应用程序的要求的说明。|  
|`State`|号|应用程序的当前状态。<br /><br /> - 0 - 已注册<br /><br /> - 1 - 已提交<br /><br /> - 2 - 已发布<br /><br /> - 3 - 已拒绝<br /><br /> - 4 - 未发布|  
|`RegistrationDate`|DateTime|注册应用程序的日期和时间。|  
|`CategoryId`|号|应用程序的类别（财务、娱乐等）|  
|`DeveloperId`|string|提交应用程序的开发人员的唯一标识符。|  
|`Attachments`|[附件](#Attachment)实体的集合。|应用程序的任何附件，例如屏幕截图或图标。|  
|`Icon`|[附件](#Attachment)|应用程序的图标。|  
  
##  <a name="Attachment"></a> 附件  
 `attachment` 实体具有以下属性：  
  
|属性|类型|描述|  
|--------------|----------|-----------------|  
|`UniqueId`|string|附件的唯一标识符。|  
|`Url`|string|资源的 URL。|  
|`Type`|string|附件的类型。|  
|`ContentType`|string|附件的媒体类型。|  
  
##  <a name="Sample"></a> 代码示例  
  
|属性|类型|描述|  
|--------------|----------|-----------------|  
|`title`|string|操作的名称。|  
|`snippet`|string|此属性已弃用，不应使用。|  
|`brush`|string|显示代码示例时要使用的代码语法着色模板。 允许的值为 `plain`、`php`、`java`、`xml`、`objc`、`python`、`ruby`、`csharp`。|  
|`template`|string|此代码示例模板的名称。|  
|`body`|string|占位符，代表代码片段的代码示例部分。|  
|`method`|string|此操作的 HTTP 方法。|  
|`scheme`|string|将用于操作请求的协议。|  
|`path`|string|操作的路径。|  
|`query`|string|包含已定义参数的查询字符串示例。|  
|`host`|string|API 管理服务网关（针对包含此操作的 API）的 URL。|  
|`headers`|[标头](#Header)实体的集合。|此操作的标头。|  
|`parameters`|[参数](#Parameter)实体的集合。|为此操作定义的参数。|  
  
##  <a name="Comment"></a> 注释  
 `API` 实体具有以下属性：  
  
|属性|type|描述|  
|--------------|----------|-----------------|  
|`Id`|号|注释的 ID。|  
|`CommentText`|string|注释的正文。 可以包含 HTML。|  
|`DeveloperCompany`|string|开发人员所在公司的名称。|  
|`PostedOn`|DateTime|发布注释的日期和时间。|  
  
##  <a name="Issue"></a> 问题  
 `issue` 实体具有以下属性。  
  
|属性|类型|描述|  
|--------------|----------|-----------------|  
|`Id`|string|问题的唯一标识符。|  
|`ApiID`|string|报告此问题时所针对的 API 的 ID。|  
|`Title`|string|问题的标题。|  
|`Description`|string|问题的说明。|  
|`SubscriptionDeveloperName`|string|报告此问题的开发人员的名字。|  
|`IssueState`|string|问题的当前状态。 可能的值为“已提议”、“已打开”、“已关闭”。|  
|`ReportedOn`|DateTime|报告此问题的日期和时间。|  
|`Comments`|[注释](#Comment)实体的集合。|此问题的注释。|  
|`Attachments`|[附件](api-management-template-data-model-reference.md#Attachment)实体的集合。|此问题的任何附件。|  
|`Services`|[API](#API) 实体的集合。|报告此问题的用户所订阅的 API。|  
  
##  <a name="Filtering"></a> 筛选  
 `filtering` 实体具有以下属性：  
  
|属性|类型|描述|  
|--------------|----------|-----------------|  
|`Pattern`|string|当前的搜索词；如果没有搜索词，则为 `null`。|  
|`Placeholder`|string|没有指定搜索词时，在搜索框中显示的文本。|  
  
##  <a name="Header"></a> 标头  
 本部分描述 `parameter` 表示形式。  
  
|属性|类型|描述|  
|--------------|-----------------|----------|  
|`name`|string|参数名称。|  
|`description`|string|参数说明。|  
|`value`|string|标头值。|  
|`typeName`|string|标头值的数据类型。|  
|`options`|string|选项。|  
|`required`|boolean|标头是否为必需。|  
|`readOnly`|boolean|标头是否为只读。|  
  
##  <a name="HTTPRequest"></a> HTTP 请求  
 本部分描述 `request` 表示形式。  
  
|属性|类型|描述|  
|--------------|----------|-----------------|  
|`description`|string|操作请求说明。|  
|`headers`|[标头](#Header)实体数组。|请求标头。|  
|`parameters`|[参数](#Parameter)数组|操作请求参数的集合。|  
|`representations`|[表示形式](#Representation)数组|操作请求表示形式的集合。|  
  
##  <a name="HTTPResponse"></a> HTTP 响应  
 本部分描述 `response` 表示形式。  
  
|属性|类型|描述|  
|--------------|----------|-----------------|  
|`statusCode`|正整数|操作响应状态代码。|  
|`description`|string|操作响应说明。|  
|`representations`|[表示形式](#Representation)数组|操作响应表示形式的集合。|  
  
##  <a name="Operation"></a> 操作  
 `operation` 实体具有以下属性：  
  
|属性|类型|描述|  
|--------------|----------|-----------------|  
|`id`|string|资源标识符。 唯一标识当前 API 管理服务实例中的操作。 值是有效的相对 URL，采用 `apis/{aid}/operations/{id}` 格式，其中 `{aid}` 是 API 标识符，`{id}` 是操作标识符。 此属性为只读。|  
|`name`|string|操作的名称。 不得为空。 最大长度为 100 个字符。|  
|`description`|string|操作说明。 不得为空。 可以包含 HTML 格式标记。 最大长度为 1000 个字符。|  
|`scheme`|string|说明可在哪些协议上调用此 API 中的操作。 允许的值为 `http`、`https` 或者 `http` 和 `https`。|  
|`uriTemplate`|string|相对 URL 模板，标识此操作的目标资源。 可以包括参数。 示例： `customers/{cid}/orders/{oid}/?date={date}`|  
|`host`|string|托管 API 的 API 管理网关 URL。|  
|`httpMethod`|string|操作 HTTP 方法。|  
|`request`|[HTTP 请求](#HTTPRequest)|一个实体，包含请求详细信息。|  
|`responses`|[HTTP 响应](#HTTPResponse)数组|操作 [HTTP 响应](#HTTPResponse)实体数组。|  
  
##  <a name="Menu"></a> 操作菜单  
 `operation menu` 实体具有以下属性：  
  
|属性|类型|描述|  
|--------------|----------|-----------------|  
|`ApiId`|string|当前 API 的 ID。|  
|`CurrentOperationId`|string|当前操作的 ID。|  
|`Action`|string|菜单类型。|  
|`MenuItems`|[操作菜单项](#MenuItem)实体的集合。|当前 API 的操作。|  
  
##  <a name="MenuItem"></a> 操作菜单项  
 `operation menu item` 实体具有以下属性：  
  
|属性|类型|描述|  
|--------------|----------|-----------------|  
|`Id`|string|操作的 ID。|  
|`Title`|string|操作说明。|  
|`HttpMethod`|string|此操作的 Http 方法。|  
  
##  <a name="Paging"></a> 分页  
 `paging` 实体具有以下属性：  
  
|属性|类型|描述|  
|--------------|----------|-----------------|  
|`Page`|号|当前页码。|  
|`PageSize`|号|要显示在单个页面上的最大结果数。|  
|`TotalItemCount`|号|要显示的项数。|  
|`ShowAll`|boolean|是否在单页上显示所有结果。|  
|`PageCount`|号|结果的页数。|  
  
##  <a name="Parameter"></a> 参数  
 本部分描述 `parameter` 表示形式。  
  
|属性|类型|描述|  
|--------------|-----------------|----------|  
|`name`|string|参数名称。|  
|`description`|string|参数说明。|  
|`value`|string|参数值。|  
|`options`|字符串数组|为查询参数值定义的值。|  
|`required`|boolean|指定参数是否为必需。|  
|`kind`|号|此参数是路径参数 (1)，还是查询字符串参数 (2)。|  
|`typeName`|string|参数类型。|  
  
##  <a name="Product"></a> Product  
 `product` 实体具有以下属性：  
  
|属性|type|描述|  
|--------------|----------|-----------------|  
|`Id`|string|资源标识符。 唯一标识当前 API 管理服务实例中的产品。 值是有效的相对 URL，采用 `products/{pid}` 格式，其中 `{pid}` 是产品标识符。 此属性为只读。|  
|`Title`|string|产品的名称。 不得为空。 最大长度为 100 个字符。|  
|`Description`|string|产品说明。 不得为空。 可以包含 HTML 格式标记。 最大长度为 1000 个字符。|  
|`Terms`|string|产品使用条款。 当开发人员尝试订阅此产品时，系统会显示这些条款，开发人员需接受这些条款才能完成订阅过程。|  
|`ProductState`|号|指定产品是否已发布。 开发人员可以在开发人员门户中发现已发布的产品。 尚未发布的产品只对管理员可见。<br /><br /> 允许用于产品状态的值包括：<br /><br /> - `0 - Not Published`<br /><br /> - `1 - Published`<br /><br /> - `2 - Deleted`|  
|`AllowMultipleSubscriptions`|boolean|指定用户是否可以同时拥有此产品的多个订阅。|  
|`MultipleSubscriptionsCount`|号|允许用户同时拥有此产品订阅的最大数。|  
  
##  <a name="Provider"></a> 提供程序  
 `provider` 实体具有以下属性：  
  
|属性|类型|描述|  
|--------------|----------|-----------------|  
|`Properties`|字符串字典|此身份验证提供程序的属性。|  
|`AuthenticationType`|string|提供程序类型。 （Azure Active Directory、Facebook 登录、Google 帐户、Microsoft 帐户、Twitter）。|  
|`Caption`|string|提供程序的显示名称。|  
  
##  <a name="Representation"></a> 表示形式  
 本部分描述 `representation`。  
  
|属性|类型|描述|  
|--------------|----------|-----------------|  
|`contentType`|string|指定此表示形式的已注册内容类型或自定义内容类型，例如 `application/xml`。|  
|`sample`|string|表示形式的示例。|  
  
##  <a name="Subscription"></a> 订阅  
 `subscription` 实体具有以下属性：  
  
|属性|类型|描述|  
|--------------|----------|-----------------|  
|`Id`|string|资源标识符。 唯一标识当前 API 管理服务实例中的订阅。 值是有效的相对 URL，采用 `subscriptions/{sid}` 格式，其中 `{sid}` 是订阅标识符。 此属性为只读。|  
|`ProductId`|string|已订阅产品的产品资源标识符。 值是有效的相对 URL，采用 `products/{pid}` 格式，其中 `{pid}` 是产品标识符。|  
|`ProductTitle`|string|产品的名称。 不得为空。 最大长度为 100 个字符。|  
|`ProductDescription`|string|产品说明。 不得为空。 可以包含 HTML 格式标记。 最大长度为 1000 个字符。|  
|`ProductDetailsUrl`|string|指向产品详细信息的相对 URL。|  
|`state`|string|订阅的状态。 可能的状态包括：<br /><br /> - `0 - suspended` – 订阅被阻止，订阅服务器无法调用产品的任何 API。<br /><br /> - `1 - active` – 订阅处于活动状态。<br /><br /> - `2 - expired` – 订阅已达到其到期日期，因此已停用。<br /><br /> - `3 - submitted` – 开发人员已提交订阅请求，但管理员尚未批准或拒绝该请求。<br /><br /> - `4 - rejected` – 管理员已拒绝订阅请求。<br /><br /> - `5 - cancelled` – 开发人员或管理员已取消订阅。|  
|`DisplayName`|string|订阅的显示名称。|  
|`CreatedDate`|dateTime|订阅的创建日期，采用 ISO 8601 格式：`2014-06-24T16:25:00Z`。|  
|`CanBeCancelled`|boolean|当前用户是否可以取消订阅。|  
|`IsAwaitingApproval`|boolean|订阅是否待批。|  
|`StartDate`|dateTime|订阅的开始日期，采用 ISO 8601 格式：`2014-06-24T16:25:00Z`。|  
|`ExpirationDate`|dateTime|订阅的到期日期，采用 ISO 8601 格式：`2014-06-24T16:25:00Z`。|  
|`NotificationDate`|dateTime|订阅的通知日期，采用 ISO 8601 格式：`2014-06-24T16:25:00Z`。|  
|`primaryKey`|string|主要订阅密钥。 最大长度为 256 个字符。|  
|`secondaryKey`|string|辅助订阅密钥。 最大长度为 256 个字符。|  
|`CanBeRenewed`|boolean|当前用户是否可以续订订阅。|  
|`HasExpired`|boolean|订阅是否已到期。|  
|`IsRejected`|boolean|是否已拒绝订阅请求。|  
|`CancelUrl`|string|用于取消订阅的相对 URL。|  
|`RenewUrl`|string|用于续订订阅的相对 URL。|  
  
##  <a name="SubscriptionSummary"></a> 订阅摘要  
 `subscription summary` 实体具有以下属性：  
  
|属性|type|描述|  
|--------------|----------|-----------------|  
|`Id`|string|资源标识符。 唯一标识当前 API 管理服务实例中的订阅。 值是有效的相对 URL，采用 `subscriptions/{sid}` 格式，其中 `{sid}` 是订阅标识符。 此属性为只读。|  
|`DisplayName`|string|订阅的显示名称|  
  
##  <a name="UserAccountInfo"></a> 用户帐户信息  
 `user account info` 实体具有以下属性：  
  
|属性|类型|描述|  
|--------------|----------|-----------------|  
|`FirstName`|string|名字。 不得为空。 最大长度为 100 个字符。|  
|`LastName`|string|姓氏。 不得为空。 最大长度为 100 个字符。|  
|`Email`|string|电子邮件地址。 不得为空，且必须在服务实例中唯一。 最大长度为 254 个字符。|  
|`Password`|string|用户帐户密码。|  
|`NameIdentifier`|string|帐户标识符，与用户电子邮件相同。|  
|`ProviderName`|string|身份验证提供程序名称。|  
|`IsBasicAccount`|boolean|如果此帐户使用电子邮件和密码注册，则为 true；如果此帐户使用提供程序注册，则为 false。|  
  
##  <a name="UseSignIn"></a> 用户登录  
 `user sign in` 实体具有以下属性：  
  
|属性|type|描述|  
|--------------|----------|-----------------|  
|`Email`|string|电子邮件地址。 不得为空，且必须在服务实例中唯一。 最大长度为 254 个字符。|  
|`Password`|string|用户帐户密码。|  
|`ReturnUrl`|string|用户单击“登录”时所在页面的 URL。|  
|`RememberMe`|boolean|是否保存当前用户的信息。|  
|`RegistrationEnabled`|boolean|是否已启用注册。|  
|`DelegationEnabled`|boolean|是否已启用委派登录。|  
|`DelegationUrl`|string|委派登录 URL（如果已启用）。|  
|`SsoSignUpUrl`|string|用户的单一登录 URL（如果存在）。|  
|`AuxServiceUrl`|string|如果当前用户为管理员，则此项为指向 Azure 门户中服务实例的链接。|  
|`Providers`|[提供程序](#Provider)实体的集合|此用户的身份验证提供程序。|  
|`UserRegistrationTerms`|string|用户必须在登录之前同意的条款。|  
|`UserRegistrationTermsEnabled`|boolean|是否启用条款。|  
  
##  <a name="UserSignUp"></a> 用户注册  
 `user sign up` 实体具有以下属性：  
  
|属性|类型|描述|  
|--------------|----------|-----------------|  
|`PasswordConfirm`|boolean|[注册](api-management-page-controls.md#sign-up)注册控件使用的值。|  
|`Password`|string|用户帐户密码。|  
|`PasswordVerdictLevel`|号|[注册](api-management-page-controls.md#sign-up)注册控件使用的值。|  
|`UserRegistrationTerms`|string|用户必须在登录之前同意的条款。|  
|`UserRegistrationTermsOptions`|号|[注册](api-management-page-controls.md#sign-up)注册控件使用的值。|  
|`ConsentAccepted`|boolean|[注册](api-management-page-controls.md#sign-up)注册控件使用的值。|  
|`Email`|string|电子邮件地址。 不得为空，且必须在服务实例中唯一。 最大长度为 254 个字符。|  
|`FirstName`|string|名字。 不得为空。 最大长度为 100 个字符。|  
|`LastName`|string|姓氏。 不得为空。 最大长度为 100 个字符。|  
|`UserData`|string|[注册](api-management-page-controls.md#sign-up)控件使用的值。|  
|`NameIdentifier`|string|[注册](api-management-page-controls.md#sign-up)注册控件使用的值。|  
|`ProviderName`|string|身份验证提供程序名称。|

## <a name="next-steps"></a>后续步骤
如需详细了解如何使用模板，请参阅[如何使用模板自定义 API 管理开发人员门户](api-management-developer-portal-templates.md)。
