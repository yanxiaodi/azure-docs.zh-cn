---
title: 来自 Verizon 高级版的 azure CDN 规则引擎功能 |Microsoft Docs
description: 规则引擎功能的 Azure CDN from Verizon Premium 的参考文档。
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: 9177ac544c83305ae95ad681d3dc9f84ac64ea36
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593245"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-features"></a>来自 Verizon 的高级规则引擎功能的 azure CDN

本文列出 Azure 内容分发网络 (CDN) [规则引擎](cdn-verizon-premium-rules-engine.md)的可用功能的详细说明。

规则的第三部分是功能。 功能定义将向由一组匹配条件确定的请求类型应用的操作的类型。

## <a name="access-features"></a>访问功能

以下功能旨在控制对内容的访问。

名称 | 用途
-----|--------
[拒绝访问 (403)](#deny-access-403) | 通过“403 禁止访问”响应确定是否拒绝了所有请求。
[令牌身份验证](#token-auth) | 确定是否会向请求应用基于令牌的身份验证。
[令牌身份验证拒绝代码](#token-auth-denial-code) | 确定当请求由于基于令牌的身份验证而被拒绝时返回给用户的响应类型。
[令牌身份验证忽略 URL 大小写](#token-auth-ignore-url-case) | 确定通过基于令牌的身份验证进行的 URL 比较是否区分大小写。
[令牌身份验证参数](#token-auth-parameter) | 确定是否应重命名基于令牌的身份验证查询字符串参数。

## <a name="caching-features"></a>缓存功能

这些功能旨在自定义内容的缓存时间和方式。

名称 | 用途
-----|--------
[带宽参数](#bandwidth-parameters) | 确定是否会启用带宽限制参数（例如 ec_rate 和 ec_prebuf）。
[带宽限制](#bandwidth-throttling) | 限制接入点 (POP) 提供的响应的带宽。
[绕过缓存](#bypass-cache) | 确定请求是否应绕过缓存。
[Cache-Control 标头处理](#cache-control-header-treatment) | 在“外部最大有效期”功能启用时，控制 POP 生成 `Cache-Control` 标头。
[Cache-Key 查询字符串](#cache-key-query-string) | 确定 cache-key 是否包括与请求关联的查询字符串参数。
[Cache-Key 重写](#cache-key-rewrite) | 重写与请求关联的 cache-key。
[完整缓存填充](#complete-cache-fill) | 确定当请求导致 POP 部分缓存未命中时会发生什么情况。
[压缩文件类型](#compress-file-types) | 定义将在服务器上压缩的文件的文件格式。
[默认的内部最大有效期](#default-internal-max-age) | 确定在进行从 POP 到源服务器的缓存重新验证时，默认的最大有效期时间间隔。
[Expires 标头处理](#expires-header-treatment) | 在“外部最大有效期”功能启用时，控制 POP 生成 `Expires` 标头。
[外部最大有效期](#external-max-age) | 确定在进行从浏览器到 POP 的缓存重新验证时的最大有效期时间间隔。
[强制内部最大有效期](#force-internal-max-age) | 确定在进行从 POP 到源服务器的缓存重新验证时的最大有效期时间间隔。
[H.264 支持（HTTP 渐进式下载）](#h264-support-http-progressive-download) | 确定适用于流式处理内容的 H.264 文件格式的类型。
[遵循 No-Cache 请求](#honor-no-cache-request) | 确定是否将 HTTP 客户端的 no-cache 请求转发到源服务器。
[忽略源服务器 No-Cache](#ignore-origin-no-cache) | 确定 CDN 是否会忽略源服务器提供的某些指令。
[忽略无法满足的范围](#ignore-unsatisfiable-ranges) | 确定当请求生成“416 无法满足请求的范围”状态代码时，会为客户端返回的响应。
[内部最大过时期限](#internal-max-stale) | 控制在 POP 无法重新验证源服务器的缓存资产的情况下，允许 POP 在正常到期时间过后多长时间内提供缓存资产。
[部分缓存共享](#partial-cache-sharing) | 确定请求是否可以生成部分缓存的内容。
[预验证缓存内容](#prevalidate-cached-content) | 确定缓存内容在其 TTL 到期之前是否适合进行早期重新验证。
[刷新零字节缓存文件](#refresh-zero-byte-cache-files) | 确定 POP 如何处理 HTTP 客户端要求提供 0 字节缓存资产的请求。
[设置“可缓存”状态代码](#set-cacheable-status-codes) | 定义一组允许进行内容缓存的状态代码。
[在出错时交付过时的内容](#stale-content-delivery-on-error) | 确定在缓存重新验证时出错或者在从客户源服务器检索请求内容时出错的情况下，是否交付到期的缓存内容。
[在重新验证时交付过时的内容](#stale-while-revalidate) | 允许 POP 在重新验证时会过时的客户端内容提供给请求者，以便提高性能。

## <a name="comment-feature"></a>注释功能

此功能用于在规则中提供附加信息。

名称 | 用途
-----|--------
[评论](#comment) | 允许在规则中添加注释。

## <a name="header-features"></a>标头功能

以下功能旨在添加、修改或删除请求或响应中的标头。

名称 | 用途
-----|--------
[Age 响应标头](#age-response-header) | 确定是否在发送给请求者的响应中包括 Age 响应标头。
[调试缓存响应标头](#debug-cache-response-headers) | 确定是否在响应中包括 X-EC-Debug 响应标头，用于说明所请求资产的缓存策略。
[修改客户端请求标头](#modify-client-request-header) | 覆盖、追加或删除请求的标头。
[修改客户端响应标头](#modify-client-response-header) | 覆盖、追加或删除响应的标头。
[设置客户端 IP 自定义标头](#set-client-ip-custom-header) | 允许将请求客户端的 IP 地址作为自定义请求标头添加到请求。

## <a name="logging-features"></a>日志记录功能

这些功能旨在自定义存储在原始日志文件中的数据。

名称 | 用途
-----|--------
[自定义日志字段 1](#custom-log-field-1) | 确定分配给原始日志文件中自定义日志字段的格式和内容。
[日志查询字符串](#log-query-string) | 确定是否将查询字符串和 URL 一起存储在访问日志中。


<!---
## Optimize

These features determine whether a request will undergo the optimizations provided by Edge Optimizer.

Name | Purpose
-----|--------
Edge Optimizer | Determines whether Edge Optimizer can be applied to a request.
Edge Optimizer – Instantiate Configuration | Instantiates or activates the Edge Optimizer configuration associated with a site.

### Edge Optimizer
**Purpose:** Determines whether Edge Optimizer can be applied to a request.

If this feature has been enabled, then the following criteria must also be met before the request will be processed by Edge Optimizer:

- The requested content must use an edge CNAME URL.
- The edge CNAME referenced in the URL must correspond to a site whose configuration has been activated in a rule.

This feature requires the ADN platform and the Edge Optimizer feature.

Value|Result
-|-
Enabled|Indicates that the request is eligible for Edge Optimizer processing.
Disabled|Restores the default behavior. The default behavior is to deliver content over the ADN platform without any additional processing.

**Default Behavior:** Disabled


### Edge Optimizer - Instantiate Configuration
**Purpose:** Instantiates or activates the Edge Optimizer configuration associated with a site.

This feature requires the ADN platform and the Edge Optimizer feature.

Key information:

- Instantiation of a site configuration is required before requests to the corresponding edge CNAME can be processed by Edge Optimizer.
- This instantiation only needs to be performed a single time per site configuration. A site configuration that has been instantiated will remain in that state until the Edge Optimizer – Instantiate Configuration feature that references it is removed from the rule.
- The instantiation of a site configuration does not mean that all requests to the corresponding edge CNAME will automatically be processed by Edge Optimizer. The Edge Optimizer feature determines whether an individual request will be processed.

If the desired site does not appear in the list, then you should edit its configuration and verify that the Active option has been marked.

**Default Behavior:** Site configurations are inactive by default.
--->

## <a name="origin-features"></a>源功能

以下功能旨在控制 CDN 与源服务器的通信方式。

名称 | 用途
-----|--------
[最大 Keep-Alive 请求数](#maximum-keep-alive-requests) | 定义 Keep-Alive 连接在关闭前的最大请求数。
[代理特殊标头](#proxy-special-headers) | 定义一组特定于 CDN 的请求标头，这些标头将从 POP 转发给源服务器。

## <a name="specialty-features"></a>特殊功能

以下功能为高级用户提供高级功能。

名称 | 用途
-----|--------
[可缓存的 HTTP 方法](#cacheable-http-methods) | 确定一组可以在我们的网络上缓存的其他 HTTP 方法。
[可缓存请求正文大小](#cacheable-request-body-size) | 定义的阈值用于确定 POST 响应是否可以缓存。
[User 变量](#user-variable) | 仅供内部使用。

## <a name="url-features"></a>URL 功能

以下功能允许将请求重定向到其他 URL 或者将其重写。

名称 | 用途
-----|--------
[遵循重定向](#follow-redirects) | 确定是否可以将请求重定向到在客户源服务器返回的 Location 标头中定义的主机名。
[URL 重定向](#url-redirect) | 通过 Location 标头重定向请求。
[URL 重写](#url-rewrite)  | 重写请求 URL。

## <a name="azure-cdn-from-verizon-premium-rules-engine-features-reference"></a>来自 Verizon 的高级规则引擎功能参考的 azure CDN

---

### <a name="age-response-header"></a>Age 响应标头

**用途**:确定是否在发送给请求者的响应中包括 Age 响应标头。

ReplTest1|结果
--|--
Enabled | 将在发送给请求者的响应中包括 Age 响应标头。
已禁用 | 将在发送给请求者的响应中排除 Age 响应标头。

**默认行为**:已禁用。

[返回页首](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bandwidth-parameters"></a>带宽参数

**目的：** 确定是否会启用带宽限制参数（例如 ec_rate 和 ec_prebuf）。

带宽限制参数决定了客户端请求的数据传输速率是否受自定义速率限制。

值|结果
--|--
Enabled|允许 POP 遵循带宽限制请求。
已禁用|导致 POP 忽略带宽限制参数。 请求的内容将正常提供（即没有带宽限制）。

**默认行为：** 已启用。

[返回页首](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bandwidth-throttling"></a>带宽限制

**目的：** 限制 pop 提供的响应的带宽。

若要正确设置带宽限制，下面的两个选项都必须定义。

选项|描述
--|--
每秒千字节数|将此选项设置为可以用来提供响应的最大带宽 (Kb/s)。
预缓存秒数|将此选项设置为在限制带宽之前 POP 要等待的秒数。 在此时间段内不限制带宽，目的是防止媒体播放器因带宽限制而出现中断或缓冲问题。

**默认行为：** 已禁用。

[返回页首](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bypass-cache"></a>绕过缓存

**目的：** 确定请求是否应绕过缓存。

ReplTest1|结果
--|--
Enabled|导致所有请求被转到源服务器，即使此前已在 POP 上缓存了内容。
已禁用|导致 POP 根据响应标头中定义的缓存策略缓存资产。

**默认行为：**

- **HTTP 大型：** 已禁用

<!---
- **ADN:** Enabled

--->

[返回页首](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cacheable-http-methods"></a>可缓存的 HTTP 方法

**目的：** 确定一组可以在我们的网络上缓存的其他 HTTP 方法。

重要信息：

- 此功能假定应始终缓存 GET 响应。 因此，在设置此功能时，不应包括 GET HTTP 方法。
- 此功能仅支持 POST HTTP 方法。 将此功能设置为 `POST` 即可启用 POST 响应缓存。
- 默认情况下，仅缓存正文小于 14 Kb 的请求。 使用“可缓存请求正文大小”功能设置请求正文的最大大小。

**默认行为：** 缓存仅 GET 响应。

[返回页首](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cacheable-request-body-size"></a>可缓存请求正文大小

**目的：** 定义的阈值用于确定 POST 响应是否可以缓存。

通过指定请求正文的最大大小来确定此阈值。 不会缓存所含请求正文超出此大小的请求。

重要信息：

- 此功能仅适用于 POST 响应可以进行缓存的情况。 使用“可缓存的 HTTP 方法”功能可启用 POST 请求缓存。
- 以下情况需考虑请求正文：
    - x-www-form-urlencoded 值
    - 确保 cache-key 的唯一
- 将请求正文的最大大小定义得过大可能影响数据交付性能。
    - **建议的值：** 14 Kb
    - **最小值：** 1 Kb

**默认行为：** 14 Kb

[返回页首](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-control-header-treatment"></a>Cache-Control 标头处理

**目的：** 控制生成`Cache-Control`pop 外部最大有效期功能处于活动状态时的标头。

要实现此类配置，最简单的方式是将“外部最大有效期”和“Cache-Control 标头处理”功能置于同一语句中。

ReplTest1|结果
--|--
覆盖|确保会执行以下操作：<br/> - 覆盖源服务器生成的 `Cache-Control` 标头。 <br/>- 向响应添加“外部最大有效期”功能生成的 `Cache-Control` 标头。
传递|确保不向响应添加“外部最大有效期”功能生成的 `Cache-Control` 标头。 <br/> 如果源服务器生成 `Cache-Control` 标头，该标头会传递给最终用户。 <br/> 如果源服务器不生成 `Cache-Control` 标头，则此选项可能会导致响应标头不包含 `Cache-Control` 标头。
缺失情况下添加|如果 `Cache-Control` 标头不是从源服务器接收的，则此选项会添加“外部最大有效期”功能生成的 `Cache-Control` 标头。 此选项用于确保为所有资产分配 `Cache-Control` 标头。
删除| 此选项可确保标头响应不包括 `Cache-Control` 标头。 如果已分配 `Cache-Control` 标头，则会将其从标头响应中删除。

**默认行为：** 覆盖。

[返回页首](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-key-query-string"></a>Cache-Key 查询字符串

**目的：** 确定 cache-key 是否包括与请求关联的查询字符串参数。

重要信息：

- 请指定一个或多个查询字符串参数名称，并用一个空格分隔各个参数名称。
- 此功能确定 cache-key 中是否包括查询字符串参数。 下表提供了每个选项的详细信息。

类型|描述
--|--
 包括|  表示 cache-key 中应包括每个指定的参数。 将为每个包含此功能中定义的查询字符串参数的唯一值的请求生成唯一 cache-key。
 全部包括  |表示将为每个发送到资产的请求生成唯一 cache-key，该资产包括唯一查询字符串。 通常不建议此类配置，因为此类配置可能导致缓存命中百分比低。 缓存命中数少会增加源服务器上的负载，因为它必须发送更多请求。 此配置复制“Query-String 缓存”页上名为“unique-cache”的缓存行为。
 Exclude | 指示仅从 cache-key 中排除指定的参数。 其他所有查询字符串参数都包含在 cache-key 中。
 全部排除  |指示从 cache-key 中排除所有查询字符串参数。 此配置复制“查询字符串缓存”页上“standard-cache”的默认缓存行为。  

使用规则引擎，可以自定义查询字符串缓存的实现方式。 例如，可以指定仅对某些位置或文件类型执行查询字符串缓存。

若要复制“查询字符串缓存”页上“no-cache”的查询字符串缓存行为，请创建一项规则，其中包含“URL 查询通配符”匹配条件和“绕过缓存”功能。 将“URL 查询通配符”匹配条件设置为星号 (*)。

>[!IMPORTANT]
> 如果对此帐户中的任何路径启用了令牌授权，则标准缓存模式是可用于查询字符串缓存的唯一模式。 有关详细信息，请参阅[使用查询字符串控制 Azure CDN 缓存行为](cdn-query-string-premium.md)。

#### <a name="sample-scenarios"></a>示例方案

此功能的以下示例用法提供了示例请求和默认的 cache-key：

- **示例请求：** http://wpc.0001.&lt ;Domain&gt; /800001/Origin/folder/asset.htm?sessionid=1234&language=EN&userid=01
- **默认 cache-key：** /800001/Origin/folder/asset.htm

##### <a name="include"></a>包括

示例配置：

- **类型：** 包括
- **参数：** language

此类配置会生成以下查询字符串参数 cache-key：

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="include-all"></a>全部包括

示例配置：

- **类型：** 全部包括

此类配置会生成以下查询字符串参数 cache-key：

    /800001/Origin/folder/asset.htm?sessionid=1234&language=EN&userid=01

##### <a name="exclude"></a>Exclude

示例配置：

- **类型：** Exclude
- **个参数：** sessioned userid

此类配置会生成以下查询字符串参数 cache-key：

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="exclude-all"></a>全部排除

示例配置：

- **类型：** 全部排除

此类配置会生成以下查询字符串参数 cache-key：

    /800001/Origin/folder/asset.htm

[返回页首](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-key-rewrite"></a>Cache-Key 重写

**目的：** 重写与请求关联的 cache-key。

cache-key 是一个相对路径，用于确定缓存的资产。 换言之，服务器会根据 cache-key 所定义的路径检查缓存版的资产。

同时定义以下两个选项即可配置此功能：

选项|描述
--|--
原始路径| 定义要重新写入其 cache-key 的请求类型的相对路径。 可以先选择基础源路径，然后定义一个正则表达式模式，从而定义相对路径。
新建路径|定义新 cache-key 的相对路径。 可以先选择基础源路径，然后定义一个正则表达式模式，从而定义相对路径。 可以使用 [HTTP 变量](cdn-http-variables.md)动态构造此相对路径。

**默认行为：** 由请求 URI 确定请求的缓存键。

[返回页首](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="comment"></a>注释

**目的：** 允许在规则中添加注释。

此功能的一个用途是提供其他信息，说明规则的常规用途或者向规则添加具体匹配条件或功能的原因。

重要信息：

- 最多可以指定 150 个字符。
- 仅使用字母数字字符。
- 此功能不影响规则的行为。 它只是用于为未来的引用提供信息，或者用于对规则进行故障诊断。

[返回页首](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="complete-cache-fill"></a>完成缓存填充

**目的：** 确定当请求导致 POP 部分缓存未命中时会发生什么情况。

部分缓存未命中描述的是未完全下载到 POP 的资产的缓存状态。 如果资产仅部分缓存在 POP 上，则会将下一个针对该资产的请求再次转发到源服务器。
<!---
This feature is not available for the ADN platform. The typical traffic on this platform consists of relatively small assets. The size of the assets served through these platforms helps mitigate the effects of partial cache misses, since the next request will typically result in the asset being cached on that POP.

--->
部分缓存未命中通常发生在用户中止下载之后，或者发生在单纯使用 HTTP 范围请求来请求资产的情况下。 此功能最适用于用户在下载时通常会半途而废的大型资产（例如视频）。 因此，HTTP Large 平台会默认启用此功能。 所有其他平台都禁用此功能。

保留 HTTP Large 平台的默认配置，因为这样会减少客户源服务器的负载，提高客户下载内容的速度。

值|结果
--|--
Enabled|还原默认行为。 默认行为是强制 POP 启动对源服务器中资产的后台获取。 然后，资产将位于 POP 的本地缓存中。
已禁用|防止 POP 执行资产的后台获取操作。 结果是，下次从该区域请求此资产时，会导致 POP 从客户源服务器请求此资产。

**默认行为：** 已启用。

#### <a name="compatibility"></a>兼容性

考虑到缓存设置的跟踪方式，不能将此功能与以下匹配条件关联：

- AS 编号
- 客户端 IP 地址
- Cookie 参数
- Cookie 参数正则表达式
- Country
- 设备
- Microsoft Edge Cname
- 引用域
- 请求标头文本
- 请求标头正则表达式
- 请求标头通配符
- 请求方法
- 请求方案
- URL 查询文本
- URL 查询正则表达式
- URL 查询通配符
- URL 查询参数

[返回页首](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="compress-file-types"></a>压缩文件类型

**目的：** 定义将在服务器上压缩的文件的文件格式。

文件格式可以通过其 Internet 媒体类型（例如 Content-Type）指定。 Internet 媒体类型是独立于平台的元数据，服务器可以利用它来确定特定资产的文件格式。 下面提供了常见 Internet 媒体类型的列表。

Internet 媒体类型|描述
--|--
text/plain|纯文本文件
text/html| HTML 文件
text/css|级联样式表 (CSS)
application/x-javascript|Javascript
application/javascript|Javascript

重要信息：

- 指定多个 Internet 媒体类型时，可使用单个空格分隔每个类型。
- 此功能仅压缩大小不到 1 MB 的资产。 服务器不会压缩更大的资产。
- 某些类型的内容，例如图像、视频和音频媒体资产（例如 JPG、MP3、MP4 等）已经压缩。 由于再对这些类型的资产进行压缩并不会显著减小文件大小，因此建议不要对它们启用压缩。
- 不支持通配符，例如星号。
- 向规则添加此功能之前，请确保在要应用此规则的平台的“压缩”页上设置“禁用压缩”选项。

[返回页首](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="custom-log-field-1"></a>自定义日志字段 1

**目的：** 确定将要在原始日志文件中分配给自定义日志字段的格式和内容。

此自定义字段可用于确定要存储在日志文件中的请求和响应标头值。

默认情况下，自定义日志字段称为“x-ec_custom-1”。 可以在“原始日志设置”页中自定义该字段的名称。

指定请求标头和响应标头的格式定义如下：

标头类型|格式|示例
-|-|-
请求标头|`%{[RequestHeader]()}[i]()` | %{Accept-Encoding}i <br/> {Referrer}i <br/> %{Authorization}i
响应标头|`%{[ResponseHeader]()}[o]()`| %{Age}o <br/> %{Content-Type}o <br/> %{Cookie}o

重要信息：

- 自定义日志字段可能包含标头字段和纯文本的任意组合。
- 此字段的有效字符如下所示：字母数字（0-9、a-z 和 A-Z）、短划线、冒号、分号、撇号、逗号、句点、下划线、等号、圆括号、方括号、空格。 百分比符号和大括号只能用于指定标头字段。
- 每个指定的标头字段的拼写必须与所需的请求/响应标头名称匹配。
- 如果想要指定多个标头，请使用分隔符来指示每个标头。 例如，可以对每个标头使用缩写：
    - AE: %{Accept-Encoding}i A: %{Authorization}i CT: %{Content-Type}o

**默认值：**  -

[返回页首](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---
### <a name="debug-cache-response-headers"></a>调试缓存响应标头

**目的：** 确定是否可以包括响应[X EC 调试响应标头](cdn-http-debug-headers.md)，其用于所请求资产的缓存策略提供信息。

当以下两个条件均为 true 时，调试缓存响应标头将包括在响应中：

- 已针对指定请求启用了“调试缓存响应标头”功能。
- 指定的请求定义了要在响应中包括的调试缓存响应标头集。

可以通过在请求中包括以下标头和指定的指令来请求调试缓存响应标头：

`X-EC-Debug: _&lt;Directive1&gt;_,_&lt;Directive2&gt;_,_&lt;DirectiveN&gt;_`

**示例：**

X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state

ReplTest1|结果
-|-
Enabled|请求调试缓存响应标头时，会返回包括 X-EC-Debug 标头的响应。
已禁用|X-EC-Debug 响应标头将不包括在响应中。

**默认行为：** 已禁用。

[返回页首](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---
### <a name="default-internal-max-age"></a>默认的内部最大有效期

**目的：** 确定在进行从 POP 到源服务器的缓存重新验证时，默认的最大有效期时间间隔。 也即在 POP 查看缓存资产是否与源服务器上存储的资产匹配之前需等待的时间。

重要信息：

- 执行此操作只是为了获得未在 `Cache-Control` 或 `Expires` 标头中分配最大有效期指示的源服务器的响应。
- 对于那些被视为无法缓存的资产，不会执行此操作。
- 此操作不影响从浏览器到 POP 的缓存重新验证。 这些类型的重新验证取决于发送给浏览器的 `Cache-Control` 或 `Expires` 标头，此类标头可以通过“外部最大有效期”功能自定义。
- 此操作的结果对于从内容所在的 POP 返回的响应标头和内容没有明显的影响，但可能会影响从 POP 发送到源服务器的重新验证流量。
- 通过以下方式配置此功能：
    - 选择可以为其应用默认内部最大有效期的状态代码。
    - 指定一个整数值，并选择所需的时间单位（例如秒、分钟、小时等）。 此值定义默认的内部最大有效期时间间隔。

- 将时间单位设置为“关”时，会为其 `Cache-Control` 或 `Expires` 标头中尚未分配最大有效期指示的请求分配默认的内部最大有效期时间间隔，即 7 天。

**默认值：** 7 天

#### <a name="compatibility"></a>兼容性

考虑到缓存设置的跟踪方式，不能将此功能与以下匹配条件关联：
- AS 编号
- 客户端 IP 地址
- Cookie 参数
- Cookie 参数正则表达式
- Country
- 设备
- 边缘 Cname
- 引用域
- 请求标头文本
- 请求标头正则表达式
- 请求标头通配符
- 请求方法
- 请求方案
- URL 查询文本
- URL 查询正则表达式
- URL 查询通配符
- URL 查询参数

[返回页首](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="deny-access-403"></a>拒绝访问 (403)

**用途**:通过“403 禁止访问”响应确定是否拒绝了所有请求。

ReplTest1 | 结果
------|-------
Enabled| 导致系统发送“403 禁止访问”响应，拒绝满足匹配条件的所有请求。
已禁用| 还原默认行为。 默认行为是允许源服务器确定将返回的响应类型。

**默认行为**:已禁用

> [!TIP]
   > 此功能的一项可能用途是将其与“请求标头”匹配条件关联，阻止访问那些使用内联链接访问用户内容的 HTTP 引用站点。

[返回页首](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="expires-header-treatment"></a>Expires 标头处理

**目的：** 在“外部最大有效期”功能启用时，控制 POP 生成 `Expires` 标头。

要实现此类配置，最简单的方式是将“外部最大有效期”和“Expires 标头处理”功能置于同一语句中。

ReplTest1|结果
--|--
覆盖|确保会执行以下操作：<br/>- 覆盖源服务器生成的 `Expires` 标头。<br/>- 向响应添加“外部最大有效期”功能生成的 `Expires` 标头。
传递|确保不向响应添加“外部最大有效期”功能生成的 `Expires` 标头。 <br/> 如果源服务器生成 `Expires` 标头，该标头会传递给最终用户。 <br/>如果源服务器不生成 `Expires` 标头，则此选项可能会导致响应标头不包含 `Expires` 标头。
缺失情况下添加| 如果 `Expires` 标头不是从源服务器接收的，则此选项会添加“外部最大有效期”功能生成的 `Expires` 标头。 此选项用于确保为所有资产分配 `Expires` 标头。
删除| 确保标头响应不包括 `Expires` 标头。 如果已分配 `Expires` 标头，则会将其从标头响应中删除。

**默认行为：** 覆盖

[返回页首](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="external-max-age"></a>外部最大有效期

**目的：** 确定在进行从浏览器到 POP 的缓存重新验证时的最大有效期时间间隔。 也即在浏览器查看 POP 中是否存在新版资产之前需等待的时间。

启用此功能时，会在 POP 中生成 `Cache-Control: max-age` 和 `Expires` 标头并将其发送到 HTTP 客户端。 默认情况下，这些标头将覆盖源服务器创建的那些标头。 但是，可以使用“Cache-Control 标头处理”和“Expires 标头处理”功能更改此行为。

重要信息：

- 此操作不影响从 POP 到源服务器的缓存重新验证。 这些类型的重新验证取决于从源服务器接收的 `Cache-Control` 和 `Expires` 标头，可以通过“默认的内部最大有效期”和“强制内部最大有效期”功能自定义。
- 配置此功能时，可以指定一个整数值，并选择所需的时间单位（例如秒、分钟、小时等）。
- 将此功能设置为负值时，会导致 POP 将过去针对每个响应设置的 `Cache-Control: no-cache` 和 `Expires` 时间发送至浏览器。 虽然 HTTP 客户端不会缓存响应，但此设置不会影响 POP 缓存源服务器响应的功能。
- 将时间单位设置为“关”会禁用此功能。 缓存在源服务器响应中的 `Cache-Control` 和 `Expires` 标头会传递给浏览器。

**默认行为：** 关闭

[返回页首](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="follow-redirects"></a>遵循重定向

**目的：** 确定是否可以将请求重定向到在客户源服务器返回的 Location 标头中定义的主机名。

重要信息：

- 只能将请求重定向到与同一平台相对应的边缘 CNAME。

ReplTest1|结果
-|-
Enabled|可以重定向请求。
已禁用|不会重定向请求。

**默认行为：** 已禁用。

[返回页首](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="force-internal-max-age"></a>强制内部最大有效期

**目的：** 确定在进行从 POP 到源服务器的缓存重新验证时的最大有效期时间间隔。 也即在 POP 可查看缓存资产是否与源服务器上存储的资产匹配之前需等待的时间。

重要信息：

- 此功能将重写在源服务器生成的 `Cache-Control` 或 `Expires` 标头中定义的最大有效期时间间隔。
- 此功能不影响从浏览器到 POP 的缓存重新验证。 这些类型的重新验证取决于发送给浏览器的 `Cache-Control` 或 `Expires` 标头。
- 此功能对 POP 发送给请求者的响应没有明显的影响， 但可能会影响从 POP 发送到源服务器的重新验证流量。
- 通过以下方式配置此功能：
    - 选择将为其应用内部最大有效期的状态代码。
    - 指定一个整数值，并选择所需的时间单位（例如秒、分钟、小时等）。 此值定义请求的最大有效期时间间隔。

- 将时间单位设置为“关”会禁用此功能。 不会将内部最大有效期时间间隔分配给请求的资产。 如果原始标头不包含缓存指令，则会根据“默认的内部最大有效期”功能中的有效设置对资产进行缓存。

**默认行为：** 关闭

#### <a name="compatibility"></a>兼容性

考虑到缓存设置的跟踪方式，不能将此功能与以下匹配条件关联：
- AS 编号
- 客户端 IP 地址
- Cookie 参数
- Cookie 参数正则表达式
- Country
- 设备
- 边缘 Cname
- 引用域
- 请求标头文本
- 请求标头正则表达式
- 请求标头通配符
- 请求方法
- 请求方案
- URL 查询文本
- URL 查询正则表达式
- URL 查询通配符
- URL 查询参数

[返回页首](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="h264-support-http-progressive-download"></a>H.264 支持（HTTP 渐进式下载）

**目的：** 确定适用于流式处理内容的 H.264 文件格式的类型。

重要信息：

- 在“文件扩展名”选项中定义一组使用空格分隔的、系统允许的 H.264 文件扩展名。 “文件扩展名”选项将重写默认行为。 通过在设置此选项时包括这些文件扩展名，保留对 MP4 和 F4V 的支持。
- 指定每个文件扩展名时请包括句点（例如 _.mp4_、 _.f4v_）。

**默认行为：** HTTP 渐进式下载默认支持 MP4 和 F4V 媒体。

[返回页首](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="honor-no-cache-request"></a>遵循 No-Cache 请求

**目的：** 确定是否会将 HTTP 客户端的 no-cache 请求转发到源服务器。

当 HTTP 客户端在 HTTP 请求中发送 `Cache-Control: no-cache` 和/或 `Pragma: no-cache` 标头时，就会出现 no-cache 请求。

ReplTest1|结果
--|--
Enabled|允许将 HTTP 客户端的 no-cache 请求转发给源服务器，然后源服务器就会将响应标头和正文通过 POP 返回给 HTTP 客户端。
已禁用|还原默认行为。 默认行为是为了防止系统将 no-cache 请求转发到源服务器。

对于所有生产流量，强烈建议将此功能保留为默认禁用状态。 否则，如果最终用户在刷新网页时无意触发多个 no-cache 请求，或者多个常用媒体播放器根据编码在每次进行视频请求时都发送 no-cache 标头，源服务器就会受到影响。 尽管如此，仍可将此功能应用到某些非生产性的分段或测试目录，以便根据需要从源服务器拉取全新的内容。

对于由于此功能而允转发到源服务器的请求，为其报告的状态为 `TCP_Client_Refresh_Miss`。 核心报告模块中提供的缓存状态报告按缓存状态提供统计信息。 用户可以通过此报告跟踪由于此功能而需要转发到源服务器的请求的数目和百分比。

**默认行为：** 已禁用。

[返回页首](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="ignore-origin-no-cache"></a>忽略源服务器 No-Cache

**目的：** 确定 CDN 是否会忽略源服务器中提供的以下指令：

- `Cache-Control: private`
- `Cache-Control: no-store`
- `Cache-Control: no-cache`
- `Pragma: no-cache`

重要信息：

- 配置此功能时，可以定义一个空格分隔的列表，其中包含的状态代码对应于需要忽略的上述指令。
- 此功能的有效状态代码的一组是：200、 203，300，301、 302、 305、 307、 400、 401、 402、 403、 404、 405、 406、 407、 408、 409、 410、 411、 412、 413、 414、 415、 416、 417、 500、 501、 502、 503、 504 和 505。
- 禁用此功能的方法是将其设置为空值。

**默认行为：** 默认行为是遵循上述指令。

#### <a name="compatibility"></a>兼容性

考虑到缓存设置的跟踪方式，不能将此功能与以下匹配条件关联：
- AS 编号
- 客户端 IP 地址
- Cookie 参数
- Cookie 参数正则表达式
- Country
- 设备
- 边缘 Cname
- 引用域
- 请求标头文本
- 请求标头正则表达式
- 请求标头通配符
- 请求方法
- 请求方案
- URL 查询文本
- URL 查询正则表达式
- URL 查询通配符
- URL 查询参数

[返回页首](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="ignore-unsatisfiable-ranges"></a>忽略无法满足的范围

**目的：** 确定当请求生成“416 无法满足请求的范围”状态代码时，会为客户端返回的响应。

默认情况下，当 POP 无法满足指定的 byte-range 请求时，以及当 If-Range 请求标头字段未被指定时，会返回此状态代码。

ReplTest1|结果
-|-
Enabled|防止 POP 使用“416 无法满足请求的范围”状态代码响应无效的 byte-range 请求。 服务器会改为交付请求的资产并为客户端返回“200 正常”。
已禁用|还原默认行为。 默认行为是遵循“416 无法满足请求的范围”状态代码。

**默认行为：** 已禁用。

[返回页首](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="internal-max-stale"></a>内部最大过时期限

**目的：** 控制在 POP 无法重新验证源服务器的缓存资产的情况下，允许 POP 在正常到期时间过后多长时间内提供缓存资产。

通常情况下，当资产的最大有效期时间到期时，POP 会向源服务器发送重新验证请求。 然后，源服务器会使用“304 未修改”进行响应，为 POP 提供缓存资产的全新租约，或者使用“200 正常”进行响应，为 POP 提供更新版的缓存资产。

如果 POP 在尝试此类重新验证时无法建立与源服务器的连接，则此“内部最大过时期限”功能会控制是否允许 POP 继续提供现已过时的资产，以及在多长时间内提供。

请注意，此时间间隔是在资产的最大有效期到期时开始的，而不是在重新验证失败时开始的。 因此，不需重新验证成功即可提供资产的最大时段是指组合使用最大有效期和最大过时期限指定的时间段。 例如，假设资产已在 9:00 进行缓存，最大有效期为 30 分钟，最大过时期限为 15 分钟，这种情况下，如果在 9:44 进行的重新验证尝试失败，则会导致最终用户收到过时的缓存资产；如果在 9:46 进行的重新验证尝试失败，则会导致最终用户收到“504 网关超时”。

为此功能配置的任何值都会被从源服务器收到的 `Cache-Control: must-revalidate` 或 `Cache-Control: proxy-revalidate` 标头取代。 如果在初次缓存资产时从源服务器收到了这其中的一个标头，则 POP 不会提供过时的缓存资产。 这种情况下，如果 POP 在资产的最大有效期时间间隔到期后无法通过源服务器重新进行验证，则 POP 会返回“504 网关超时”错误。

重要信息：

- 通过以下方式配置此功能：
    - 选择将为其应用最大过时期限的状态代码。
    - 指定一个整数值，并选择所需的时间单位（例如秒、分钟、小时等）。 此值定义将要应用的内部最大过时期限。

- 将时间单位设置为“关”会禁用此功能。 在正常的到期时间过后，不会提供缓存资产。

**默认行为：** 两分钟

#### <a name="compatibility"></a>兼容性

考虑到缓存设置的跟踪方式，不能将此功能与以下匹配条件关联：
- AS 编号
- 客户端 IP 地址
- Cookie 参数
- Cookie 参数正则表达式
- Country
- 设备
- 边缘 Cname
- 引用域
- 请求标头文本
- 请求标头正则表达式
- 请求标头通配符
- 请求方法
- 请求方案
- URL 查询文本
- URL 查询正则表达式
- URL 查询通配符
- URL 查询参数

[返回页首](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="log-query-string"></a>日志查询字符串

**目的：** 确定是否会将查询字符串和 URL 一起存储在访问日志中。

值|结果
-|-
Enabled|在访问日志中记录 URL 时，允许存储查询字符串。 如果 URL 中不含查询字符串，则此选项无效。
已禁用|还原默认行为。 默认行为是将 URL 记录到访问日志中时忽略查询字符串。

**默认行为：** 已禁用。

[返回页首](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="maximum-keep-alive-requests"></a>最大 Keep-Alive 请求数

**目的：** 定义 Keep-Alive 连接在关闭前的最大请求数。

建议不要将最大请求数设置过低，否则会导致性能下降。

重要信息：

- 将此值指定为整数。
- 不要在指定的值中包括逗号或句点。

**默认值：** 10,000 个请求

[返回页首](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="modify-client-request-header"></a>修改客户端请求标头

**目的：** 每个请求包含一组用于描述该请求标头。 此功能可以：

- 追加或覆盖分配给请求标头的值。 如果指定的请求标头不存在，则可使用此功能将其添加到请求。
- 从请求中删除请求标头。

转发给源服务器的请求会反映通过此功能所做的更改。

可以对请求标头执行以下操作之一：

选项|描述|示例
-|-|-
附加|指定的值将添加到现有请求标头值的末尾。|**请求标头值（客户端）：**<br/>Value1<br/>**请求标头值（规则引擎）：**<br/>Value2 <br/>**新的请求标头值：** <br/>Value1Value2
覆盖|请求标头值将设置为指定的值。|**请求标头值（客户端）：**<br/>Value1<br/>**请求标头值（规则引擎）：**<br/>Value2<br/>**新的请求标头值：**<br/> Value2 <br/>
DELETE|删除指定的请求标头。|**请求标头值（客户端）：**<br/>Value1<br/>**修改客户端请求标头配置：**<br/>删除问题中的请求标头。<br/>**结果：**<br/>指定的请求标头不会转发给源服务器。

重要信息：

- 确保在“名称”选项中指定的值与所需请求标头完全匹配。
- 在标识标头时不考虑大小写。 例如，可以使用 `Cache-Control` 标头名称的任何下述变体来标识该标头：
    - cache-control
    - CACHE-CONTROL
    - cachE-Control
- 指定标头名称时，请仅使用字母数字字符、短划线或下划线。
- 删除标头即可防止 POP 将其转发给源服务器。
- 以下标头为保留标头，不能通过此功能进行修改：
    - forwarded
    - host
    - via
    - 警告
    - x-forwarded-for
    - 所有以“x-ec”开头的标头名称均为保留名称。

[返回页首](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="modify-client-response-header"></a>修改客户端响应标头

每个响应都包含一组用于描述该响应的响应标头。 此功能可以：

- 追加或覆盖分配给响应标头的值。 如果指定的响应标头不存在，则可使用此功能将其添加到响应。
- 从响应中删除响应标头。

默认情况下，由源服务器和 POP 定义响应标头值。

可以对响应标头执行以下操作之一：

选项|描述|示例
-|-|-
附加|指定的值将添加到现有响应标头值的末尾。|**响应标头值（客户端）：**<br />Value1<br/>**响应标头值（规则引擎）：**<br/>Value2<br/>**新的响应标头值：**<br/>Value1Value2
覆盖|响应标头值将设置为指定的值。|**响应标头值（客户端）：**<br/>Value1<br/>**响应标头值（规则引擎）：**<br/>Value2 <br/>**新的响应标头值：**<br/>Value2 <br/>
DELETE|删除指定的响应标头。|**响应标头值（客户端）：**<br/>Value1<br/>**修改客户端响应标头配置：**<br/>删除问题中的响应标头。<br/>**结果：**<br/>指定的响应标头不会转发给请求者。

重要信息：

- 确保在“名称”选项中指定的值与所需响应标头完全匹配。
- 在标识标头时不考虑大小写。 例如，可以使用 `Cache-Control` 标头名称的任何下述变体来标识该标头：
    - cache-control
    - CACHE-CONTROL
    - cachE-Control
- 删除标头即可防止系统将其转发给请求者。
- 以下标头为保留标头，不能通过此功能进行修改：
    - accept-encoding
    - age
    - 连接
    - content-encoding
    - content-length
    - content-range
    - date
    - server
    - trailer
    - transfer-encoding
    - 升级
    - vary
    - via
    - 警告
    - 所有以“x-ec”开头的标头名称均为保留名称。

[返回页首](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="partial-cache-sharing"></a>部分缓存共享

**目的：** 确定请求是否可以生成部分缓存的内容。

然后，可以使用这个部分缓存履行对该内容的新请求，直到所请求的内容完全缓存。

ReplTest1|结果
-|-
Enabled|请求可以生成部分缓存的内容。
已禁用|请求只能生成所请求内容的完全缓存版本。

**默认行为：** 已禁用。

[返回页首](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="prevalidate-cached-content"></a>预验证缓存内容

**目的：** 确定缓存内容在其 TTL 到期之前是否适合进行早期重新验证。

定义在所请求内容的 TTL 到期之前的时间段，在此期间可以进行早期重新验证。

重要信息：

- 选择“关”作为时间单位时，需在缓存内容的 TTL 到期之后重新进行验证。 不应指定时间，时间会被忽略。

**默认行为：** 关闭。 只能在缓存内容的 TTL 到期后，才能重新进行验证。

[返回页首](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="proxy-special-headers"></a>代理特殊标头

**目的：** 定义一组[特定于 Verizon 的 HTTP 请求标头](cdn-verizon-http-headers.md)，将会从 POP 转发给源服务器。

重要信息：

- 此功能中定义的每个特定于 CDN 的请求标头都会转发到源服务器。 不会转发已排除的标头。
- 若要防止转发 CDN 特定的请求标头，请将其从标头列表字段中以空格分隔的列表中删除。

默认列表中包括以下 HTTP 标头：
- Via
- X-Forwarded-For
- X-Forwarded-Proto
- X-Host
- X-Midgress
- X-Gateway-List
- X-EC-Name
- 主机

**默认行为：** 所有特定于 CDN 的请求标头将转发到源服务器。

[返回页首](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="refresh-zero-byte-cache-files"></a>刷新零字节缓存文件

**目的：** 确定 POP 如何处理 HTTP 客户端要求提供 0 字节缓存资产的请求。

有效值是：

ReplTest1|结果
--|--
Enabled|导致 POP 重新获取源服务器的资产。
已禁用|还原默认行为。 默认行为是在收到请求后提供有效的缓存资产。

此功能不是正确地进行缓存和内容交付所必需的，但可用作一种解决方法。 例如，源服务器上的动态内容生成器可能会意外地导致 0 字节响应被发送到 POP。 这些类型的响应通常由 POP 缓存。 如果您知道 0 字节响应不是此类内容的有效响应，此功能可以防止这些类型的资产提供给你的客户端。

**默认行为：** 已禁用。

[返回页首](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="set-cacheable-status-codes"></a>设置“可缓存”状态代码

**目的：** 定义一组允许进行内容缓存的状态代码。

默认情况下，仅为“200 正常”响应启用缓存。

定义一组所需的、空格分隔的状态代码。

重要信息：

- 启用“忽略源服务器 No-Cache”功能。 如果未启用该功能，则不会缓存非“200 正常”响应。
- 此功能的有效状态代码的一组是：203，300，301、 302、 305、 307、 400、 401、 402、 403、 404、 405、 406、 407、 408、 409、 410、 411、 412、 413、 414、 415、 416、 417、 500、 501、 502、 503、 504 和 505。
- 对于生成“200 正常”状态代码的响应，不能通过此功能禁用缓存。

**默认行为：** 仅为生成 200 OK 状态代码的响应启用缓存。

[返回页首](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="set-client-ip-custom-header"></a>设置客户端 IP 自定义标头

**目的：** 将添加到请求的 IP 地址来标识请求的客户端的自定义标头。

“标头名称”选项用于定义自定义请求标头（会在其中存储客户端的 IP 地址）的名称。

此功能允许客户源服务器通过自定义请求标头查找客户端 IP 地址。 如果请求是从缓存提供的，则不会将客户端的 IP 地址告知源服务器。 因此，建议将此功能用于不进行缓存的资产。

确保指定的标头名称与任何下述名称都不匹配：

- 标准请求标头名称。 可以在 [RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html) 中找到标准标头名称的列表。
- 保留的标头名称：
    - forwarded-for
    - host
    - vary
    - via
    - 警告
    - x-forwarded-for
    - 所有以“x-ec”开头的标头名称均为保留名称。

[返回页首](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="stale-content-delivery-on-error"></a>在出错时交付过时的内容

**目的：** 确定在缓存重新验证时出错或者在从客户源服务器检索请求内容时出错的情况下，是否交付到期的缓存内容。

ReplTest1|结果
-|-
Enabled|如果在连接到源服务器的过程中发生错误，则会向请求者提供过时的内容。
已禁用|源服务器的错误将转发给请求者。

**默认行为：** 已禁用

[返回页首](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="stale-while-revalidate"></a>在重新验证时交付过时的内容

**目的：** 允许 Pop 在重新验证时会过时的内容提供给请求者提高性能。

重要信息：

- 此功能的行为因所选时间单位而异。
    - **时间单位：** 指定的时间长度并选择时间单位 （例如，秒、 分钟、 小时等） 以便交付过时的内容。 此类设置允许内容需要根据以下公式验证之前 CDN 延长它可能会传递的时间长度，以便：**TTL** + **过时时在重新时间**
    - **关闭：** 选择"关闭"以要求在重新验证之前可能会提供过时的内容的请求。
        - 请勿指定时间长度，因为时间长度不适用，会被系统忽略。

**默认行为：** 关闭。 在提交请求的内容之前，必须重新进行验证。

[返回页首](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth"></a>令牌身份验证

**目的：** 确定是否会向请求应用基于令牌的身份验证。

如果启用基于令牌的身份验证，则系统只会遵循提供了加密令牌且符合该令牌所指定要求的请求。

将用于加密和解密令牌值的加密密钥取决于“令牌身份验证”页上的“主密钥”和“备份密钥”选项。 请注意，加密密钥特定于平台。

**默认行为：** 已禁用。

除了 URL 重写功能之外，此功能优先于大多数功能。

ReplTest1 | 结果
------|---------
Enabled | 通过基于令牌的身份验证保护请求的内容。 只遵循客户端发出的提供了有效令牌且符合其要求的请求。 FTP 事务不进行基于令牌的身份验证。
已禁用| 还原默认行为。 默认行为是允许基于令牌的身份验证配置，以便确定是否要对请求进行保护。

#### <a name="compatibility"></a>兼容性

不要将令牌身份验证与“始终”匹配条件一起使用。

[返回页首](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-denial-code"></a>令牌身份验证拒绝代码

**目的：** 确定基于令牌的身份验证为由拒绝请求时为用户返回的响应的类型。

下表列出了可用的响应代码。

响应代码|响应名称|描述
-------------|-------------|--------
301|已永久移动|此状态代码将未经授权的用户重定向到在 Location 标头中指定的 URL。
302|已找到|此状态代码将未经授权的用户重定向到在 Location 标头中指定的 URL。 此状态代码是执行重定向操作的行业标准方法。
307|临时重定向|此状态代码将未经授权的用户重定向到在 Location 标头中指定的 URL。
401|未授权|要提示用户进行身份验证，可将此状态代码与 WWW-Authenticate 响应标头相结合。
403|禁止|此消息是未经授权的用户在尝试访问受保护的内容时会看到的标准“403 禁止访问”状态消息。
404|找不到文件|此状态代码表示 HTTP 客户端可以与服务器通信，但找不到请求的内容。

#### <a name="compatibility"></a>兼容性

不要将令牌身份验证拒绝代码与“始终”匹配条件一起使用。 请改用“管理”  门户的“令牌身份验证”  页的“自定义拒绝处理”  部分。 有关详细信息，请参阅[使用令牌身份验证保护 Azure CDN 资产](cdn-token-auth.md)。

#### <a name="url-redirection"></a>URL 重定向

在配置为返回 3xx 状态代码时，此功能支持将 URL 重定向到用户定义的 URL。 可通过执行以下步骤指定此用户定义 URL：

1. 针对“令牌身份验证拒绝代码”功能选择 3xx 响应代码。
2. 从“可选标头名称”选项中选择“Location”。
3. 将“可选标头值”选项设置为所需的 URL。

如果未为 3xx 状态代码定义 URL，则会为用户返回 3xx 状态代码的标准响应页。

URL 重定向仅适用于 3xx 响应代码。

“可选标头值”选项支持字母数字字符、引号和空格。

#### <a name="authentication"></a>身份验证

此功能允许系统在响应未经授权的请求（请求的是基于令牌的身份验证所保护的内容）时包括 WWW-Authenticate 标头。 如果已在配置中将 WWW-Authenticate 标头设置为“基本”，则会提示未经授权的用户输入帐户凭据。

以上配置可通过执行以下步骤实现：

1. 针对“令牌身份验证拒绝代码”功能选择“401”作为响应代码。
2. 从“可选标头名称”选项中选择“WWW-Authenticate”。
3. 将“可选标头值”选项设置为“基本”。

WWW-Authenticate 标头仅适用于 401 响应代码。

[返回页首](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-ignore-url-case"></a>令牌身份验证忽略 URL 大小写

**目的：** 确定通过基于令牌的身份验证进行的 URL 比较是否区分大小写。

受此功能影响的参数如下：

- ec_url_allow
- ec_ref_allow
- ec_ref_deny

有效值是：

值|结果
---|----
Enabled|导致 POP 在比较基于令牌的身份验证参数的 URL 时忽略大小写。
已禁用|还原默认行为。 默认行为是在针对令牌身份验证进行 URL 比较时区分大小写。

**默认行为：** 已禁用。

[返回页首](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-parameter"></a>令牌身份验证参数

**目的：** 确定是否应重命名基于令牌的身份验证查询字符串参数。

重要信息：

- “值”选项所定义的查询字符串参数名称可用于指定令牌。
- 无法将“值”选项设置为“ec_token”。
- 确保“值”选项中定义的名称只包含有效的 URL 字符。

值|结果
----|----
Enabled|“值”选项所定义的查询字符串参数名称应该用于定义令牌。
已禁用|可将令牌指定为请求 URL 中未定义的查询字符串参数。

**默认行为：** 已禁用。 可将令牌指定为请求 URL 中未定义的查询字符串参数。

[返回页首](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="url-redirect"></a>URL 重定向

**目的：** 通过 Location 标头重定向请求。

此功能的配置需要设置以下选项：

选项|描述
-|-
代码|选择会返回给请求者的响应代码。
源和模式| 这些设置定义的请求 URI 模式用于标识可重定向请求的类型。 只会重定向其 URL 同时满足下述两个条件的请求： <br/> <br/> **源 （或内容访问点）：** 选择用于标识源服务器的相对路径。 该路径是 _/XXXX/_ 部分和终结点名称。 <br/><br/> **源 （模式）：** 必须定义一个通过相对路径标识请求的模式。 此正则表达式模式必须定义一个路径，该路径直接开始于以前选择的内容访问点（见上）之后。 <br/> - 确保上面定义的请求 URI 条件（即源和模式）不与为此功能定义的任何匹配条件冲突。 <br/> - 指定模式；如果使用空白值作为模式，则匹配所有字符串。
目标| 定义要将上述请求重定向到的 URL。 <br/><br/> 通过以下方式动态构造此 URL： <br/> - 正则表达式模式 <br/>- [HTTP 变量](cdn-http-variables.md) <br/><br/> 使用 $_n_ 将源模式中捕获的值替换到目标模式中，其中 _n_ 用于按捕获顺序来标识值。 例如，$1 代表按源模式捕获的第一个值，而 $2 则代表第二个值。 <br/>

强烈建议使用绝对 URL。 使用相对 URL 可能会将 CDN URL 重定向到无效的路径。

**示例方案**

此示例演示如何重定向可解析成以下基 CDN URL 的边缘 CNAME URL：http:\//marketing.azureedge.net/brochures

符合条件的请求将重定向到以下基边缘 CNAME URL：http:\//cdn.mydomain.com/resources

此 URL 重定向可能会通过以下配置：![URL 重定向](./media/cdn-rules-engine-reference/cdn-rules-engine-redirect.png)

**要点：**

- “URL 重定向”功能定义将重定向的请求 URL。 因此，不需要其他匹配条件。 虽然匹配条件被定义为“始终”，但只会重定向指向“marketing”客户源服务器上“brochures”文件夹的请求。
- 所有匹配的请求都会重定向到“目标”选项中定义的边缘 CNAME URL。
    - 示例方案 1：
        - 示例请求 (CDN URL)：http:\//marketing.azureedge.net/brochures/widgets.pdf
        - 请求 URL（重定向后）：http:\//cdn.mydomain.com/resources/widgets.pdf  
    - 示例方案 2：
        - 示例请求（边缘 CNAME URL）：http:\//marketing.mydomain.com/brochures/widgets.pdf
        - 请求 URL（重定向后）：http:\//cdn.mydomain.com/resources/widgets.pdf  示例解决方案
    - 示例方案 3：
        - 示例请求（边缘 CNAME URL）：http:\//brochures.mydomain.com/campaignA/final/productC.ppt
        - 请求 URL（重定向后）：http:\//cdn.mydomain.com/resources/campaignA/final/productC.ppt 
- “目标”选项中利用了请求方案 (%{scheme}) 变量，这可以确保请求的方案在重定向后保持不变。
- 从请求中捕获的 URL 段通过“$1”追加到新的 URL。

[返回页首](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="url-rewrite"></a>URL 重写

**目的：** 重写请求 URL。

重要信息：

- 此功能的配置需要设置以下选项：

选项|描述
-|-
 源和模式 | 这些设置定义的请求 URI 模式用于标识可重写请求的类型。 只会重写其 URL 同时满足下述两个条件的请求： <br/><br/>  - **源 （或内容访问点）：** 选择用于标识源服务器的相对路径。 该路径是 _/XXXX/_ 部分和终结点名称。 <br/><br/> - **源 （模式）：** 必须定义一个通过相对路径标识请求的模式。 此正则表达式模式必须定义一个路径，该路径直接开始于以前选择的内容访问点（见上）之后。 <br/> 确认上面定义的请求 URI 条件（即源和模式）不与为此功能定义的任何匹配条件冲突。 指定模式；如果使用空白值作为模式，则匹配所有字符串。
 目标  |定义要通过其将上述请求重写的相对 URL： <br/>    1.选择用于标识源服务器的内容访问点。 <br/>    2.使用以下方式定义相对路径： <br/>        - 正则表达式模式 <br/>        - [HTTP 变量](cdn-http-variables.md) <br/> <br/> 使用 $_n_ 将源模式中捕获的值替换到目标模式中，其中 _n_ 用于按捕获顺序来标识值。 例如，$1 代表按源模式捕获的第一个值，而 $2 则代表第二个值。

 此功能允许 POP 重写 URL，而不需执行传统的重定向。 也就是说，请求者会收到与已请求了重写 URL 时相同的响应代码。

**示例方案 1**

此示例演示如何重定向可解析成以下基 CDN URL 的边缘 CNAME URL：http:\//marketing.azureedge.net/brochures/

符合条件的请求将重定向到以下基边缘 CNAME URL：http:\//MyOrigin.azureedge.net/resources/

此 URL 重定向可能会通过以下配置：![URL 重定向](./media/cdn-rules-engine-reference/cdn-rules-engine-rewrite.png)

**示例方案 2**

此示例将演示如何使用正则表达式将边缘 CNAME URL 从大写重定向为小写。

此 URL 重定向可能会通过以下配置：![URL 重定向](./media/cdn-rules-engine-reference/cdn-rules-engine-to-lowercase.png)

**要点：**

- “URL 重写”功能定义将重写的请求 URL。 因此，不需要其他匹配条件。 虽然匹配条件被定义为“始终”，但只会重写指向“marketing”客户源服务器上“brochures”文件夹的请求。

- 从请求中捕获的 URL 段通过“$1”追加到新的 URL。

#### <a name="compatibility"></a>兼容性

此功能包含的匹配条件必须满足，然后才能将其应用到请求。 为了防止设置相冲突的匹配条件，此功能不兼容以下匹配条件：

- AS 编号
- CDN 源
- 客户端 IP 地址
- 客户源
- 请求方案
- URL 路径目录
- URL 路径扩展名
- URL 路径文件名
- URL 路径文本
- URL 路径正则表达式
- URL 路径通配符
- URL 查询文本
- URL 查询参数
- URL 查询正则表达式
- URL 查询通配符

[返回页首](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="user-variable"></a>User 变量

**目的：** 仅供内部使用。

[返回页首](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

## <a name="next-steps"></a>后续步骤

- [规则引擎参考](cdn-verizon-premium-rules-engine-reference.md)
- [规则引擎条件表达式](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [规则引擎匹配条件](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [使用规则引擎重写 HTTP 行为](cdn-verizon-premium-rules-engine.md)
- [Azure CDN 概述](cdn-overview.md)