---
title: 使用媒体服务 REST API 配置资产传送策略 | Microsoft Docs
description: 本主题说明如何使用媒体服务 REST API 配置不同的资产传送策略。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 5cb9d32a-e68b-4585-aa82-58dded0691d0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 5e4e565b0b5272de19458617a9c4bd3509907cce
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60817393"
---
# <a name="configuring-asset-delivery-policies"></a>配置资产传送策略
[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

如果打算传送动态加密的资产，媒体服务内容传送工作流中的步骤之一是为资产配置传送策略。 资产传送策略告知媒体服务希望如何传送资产：应该将资产动态打包成哪种流式处理协议（例如 MPEG DASH、HLS、平滑流或全部），是否要动态加密资产以及如何加密（信封或常用加密）。

本主题介绍为何以及如何创建和配置资产传送策略。

> [!NOTE]
> 创建 AMS 帐户后，会将一个处于“已停止”状态的**默认**流式处理终结点添加到帐户。  若要开始流式传输内容并利用动态打包和动态加密，要从中流式传输内容的流式处理终结点必须处于“正在运行”状态。  
>
> 此外，要使用动态打包和动态加密，资产必须包含一组自适应比特率 MP4 或自适应比特率平滑流式处理文件。

可以将不同的策略应用到同一个资产。 例如，可以将 PlayReady 加密应用到平滑流式处理，将 AES 信封加密应用到 MPEG DASH 和 HLS。 将阻止流式处理传送策略中未定义的任何协议（例如，添加仅将 HLS 指定为协议的单个策略）。 如果根本没有定义任何传送策略，则情况不是这样。 此时，将允许所有明文形式的协议。

如果要传送存储加密资产，则必须配置资产的传送策略。 在流式传输资产之前，流式处理服务器会删除存储加密，再使用指定的传送策略流式传输用户的内容。 例如，若要传送使用高级加密标准 (AES) 信封加密密钥加密的资产，请将策略类型设为“DynamicEnvelopeEncryption”  。 要删除存储加密并以明文的形式流式传输资产，请将策略类型设置为 **NoDynamicEncryption**。 下面是演示如何配置这些策略类型的示例。

根据配置资产传送策略的方式，可以动态打包、动态加密和流式传输以下流式处理协议：平滑流式处理、HLS、MPEG DASH 流。

以下列表显示了用于流式传输平滑流、HLS、DASH 的格式。

平滑流：

{流式处理终结点名称-媒体服务帐户名称}.streaming.mediaservices.windows.net/{定位符 ID}/{文件名}.ism/Manifest

HLS：

{流式处理终结点名称-媒体服务帐户名称}.streaming.mediaservices.windows.net/{定位符 ID}/{文件名}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{流式处理终结点名称-媒体服务帐户名称}.streaming.mediaservices.windows.net/{定位符 ID}/{文件名}.ism/Manifest(format=mpd-time-csf)


有关如何发布资产和生成流 URL 的说明，请参阅 [生成流 URL](media-services-deliver-streaming-content.md)。

## <a name="considerations"></a>注意事项
* 如果某个资产存在 OnDemand（流式处理）定位符，则不能与该资产关联的 AssetDeliveryPolicy。 在删除策略之前，建议先从资产中删除该策略。
* 如果未设置资产传送策略，则无法在存储加密的资产上创建流式处理定位符。  如果资产未经过存储加密，则即使未设置资产传送策略，系统也可让你顺利地创建定位符和流式处理资产。
* 可将多个资产传送策略关联到单个资产，但只能指定一种方法来处理给定的 AssetDeliveryProtocol。  也就是说，如果尝试链接两个指定 AssetDeliveryProtocol.SmoothStreaming 协议的传送策略，则会导致出错，因为当客户端发出平滑流请求时，系统不知道要应用哪个策略。
* 如果资产包含现有流式处理定位符，则不能将新策略链接到该资产、取消现有策略与该资产的链接，或者更新与该资产关联的传送策略。  必须先删除流式传输定位符，再调整策略，然后重新创建流式传输定位符。  在重新创建流式处理定位符时可以使用同一个 locatorId，但应确保该操作不会导致客户端出现问题，因为内容可能已被来源或下游 CDN 缓存。

> [!NOTE]
> 
> 访问媒体服务中的实体时，必须在 HTTP 请求中设置特定标头字段和值。 有关详细信息，请参阅[媒体服务 REST API 开发的设置](media-services-rest-how-to-use.md)。

## <a name="connect-to-media-services"></a>连接到媒体服务

若要了解如何连接到 AMS API，请参阅[通过 Azure AD 身份验证访问 Azure 媒体服务 API](media-services-use-aad-auth-to-access-ams-api.md)。 

## <a name="clear-asset-delivery-policy"></a>清除资产传送策略
### <a id="create_asset_delivery_policy"></a>创建资产传送策略
以下 HTTP 请求将创建一个资产传送策略，该策略指定不应用动态加密，而使用以下任何协议传送流：MPEG DASH、HLS 和平滑流式处理协议。 

有关创建 AssetDeliveryPolicy 时可以指定哪些值的信息，请参阅[定义 AssetDeliveryPolicy 时使用的类型](#types)部分。   

请求：

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
    Host: media.windows.net

    {"Name":"Clear Policy",
    "AssetDeliveryProtocol":7,
    "AssetDeliveryPolicyType":2,
    "AssetDeliveryConfiguration":null}

响应：

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 363
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
    request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
    x-ms-request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 08 Feb 2015 06:21:27 GMT

    {"odata.metadata":"https://media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element",
    "Id":"nb:adpid:UUID:92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd",
    "Name":"Clear Policy",
    "AssetDeliveryProtocol":7,
    "AssetDeliveryPolicyType":2,
    "AssetDeliveryConfiguration":null,
    "Created":"2015-02-08T06:21:27.6908329Z",
    "LastModified":"2015-02-08T06:21:27.6908329Z"}

### <a id="link_asset_with_asset_delivery_policy"></a>将资产与资产传送策略相链接
以下 HTTP 请求将指定的资产链接到资产传送策略。

请求：

    POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A86933344-9539-4d0c-be7d-f842458693e0')/$links/DeliveryPolicies HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Content-Type: application/json
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 56d2763f-6e72-419d-ba3c-685f6db97e81
    Host: media.windows.net

    {"uri":"https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')"}

响应：

    HTTP/1.1 204 No Content


## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>DynamicEnvelopeEncryption 资产传送策略
### <a name="create-content-key-of-the-envelopeencryption-type-and-link-it-to-the-asset"></a>创建 EnvelopeEncryption 类型的内容密钥，并将其链接到资产
在指定 DynamicEnvelopeEncryption 传送策略时，需要确保将资产链接到 EnvelopeEncryption 类型的内容密钥。 有关详细信息，请参阅：[创建内容密钥](media-services-rest-create-contentkey.md)。

### <a id="get_delivery_url"></a>获取传送 URL
获取上一步创建的内容密钥的指定传送方法的传送 URL。 客户端使用返回的 URL 来请求 AES 密钥或 PlayReady 许可证，以播放受保护内容。

指定要在 HTTP 请求正文中获取的 URL 类型。 如果要使用 PlayReady 保护内容，请通过用 1 表示 keyDeliveryType 来请求媒体服务 PlayReady 许可证：{"keyDeliveryType":1}。 如果要使用信封加密来保护内容，请为 keyDeliveryType 指定 2，以请求密钥获取 URL：{"keyDeliveryType":2}。

请求：

    POST https://media.windows.net/api/ContentKeys('nb:kid:UUID:dc88f996-2859-4cf7-a279-c52a9d6b2f04')/GetKeyDeliveryUrl HTTP/1.1
    Content-Type: application/json
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
    Host: media.windows.net
    Content-Length: 21

    {"keyDeliveryType":2}

响应：

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 198
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
    request-id: d26f65d2-fe65-4136-8fcf-31545be68377
    x-ms-request-id: d26f65d2-fe65-4136-8fcf-31545be68377
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 08 Feb 2015 21:42:30 GMT

    {"odata.metadata":"media.windows.net/api/$metadata#Edm.String","value":"https://amsaccount1.keydelivery.mediaservices.windows.net/?KID=dc88f996-2859-4cf7-a279-c52a9d6b2f04"}


### <a name="create-asset-delivery-policy"></a>创建资产传送策略
以下 HTTP 请求将创建 **AssetDeliveryPolicy**，该策略配置为将动态信封加密 (**DynamicEnvelopeEncryption**) 应用到 **HLS** 协议（在本示例中，已阻止流式处理其他协议）。 

有关创建 AssetDeliveryPolicy 时可以指定哪些值的信息，请参阅 [定义 AssetDeliveryPolicy 时使用的类型](#types) 部分。   

请求：

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    Host: media.windows.net

    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]"}


响应：

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 460
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3Aec9b994e-672c-4a5b-8490-a464eeb7964b')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
    x-ms-request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 09 Feb 2015 05:24:38 GMT

    {"odata.metadata":"media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element","Id":"nb:adpid:UUID:ec9b994e-672c-4a5b-8490-a464eeb7964b","Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]","Created":"2015-02-09T05:24:38.9167436Z","LastModified":"2015-02-09T05:24:38.9167436Z"}


### <a name="link-asset-with-asset-delivery-policy"></a>将资产与资产传送策略相链接
请参阅[将资产与资产传送策略相链接](#link_asset_with_asset_delivery_policy)

## <a name="dynamiccommonencryption-asset-delivery-policy"></a>DynamicCommonEncryption 资产传送策略
### <a name="create-content-key-of-the-commonencryption-type-and-link-it-to-the-asset"></a>创建 CommonEncryption 类型的内容密钥，并将其链接到资产
在指定 DynamicCommonEncryption 传送策略时，需要确保将资产链接到 CommonEncryption 类型的内容密钥。 有关详细信息，请参阅：[创建内容密钥](media-services-rest-create-contentkey.md)。

### <a name="get-delivery-url"></a>获取传送 URL
获取上一步创建的内容密钥的 PlayReady 传送方法的传送 URL。 客户端使用返回的 URL 来请求 PlayReady 许可证，以播放受保护内容。 有关详细信息，请参阅[获取传送 URL](#get_delivery_url)。

### <a name="create-asset-delivery-policy"></a>创建资产传送策略
以下 HTTP 请求将创建 **AssetDeliveryPolicy**，该策略配置为将动态通用加密 (**DynamicCommonEncryption**) 应用到**平滑流式处理**协议（在本示例中，已阻止流式处理其他协议）。 

有关创建 AssetDeliveryPolicy 时可以指定哪些值的信息，请参阅[定义 AssetDeliveryPolicy 时使用的类型](#types)部分。   

请求：

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    Host: media.windows.net

    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":1,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\/PlayReady\/"}]"}


要使用 Widevine DRM 保护内容，请更新 AssetDeliveryConfiguration 值以使用 WidevineLicenseAcquisitionUrl（其值为 7），并指定许可证交付服务的 URL。 可以通过以下 AMS 合作伙伴来帮助交付 Widevine 许可证：[Axinom](https://www.axinom.com/press/ibc-axinom-drm-6/)、[EZDRM](https://ezdrm.com/)、[castLabs](https://castlabs.com/company/partners/azure/)。

例如： 

    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":2,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":7,\"Value\":\"https:\\/\\/example.net\/WidevineLicenseAcquisition\/"}]"}

> [!NOTE]
> 使用 Widevine 加密时，只能使用 DASH 传送。 请确保在资产传送协议中指定 DASH (2)。
> 
> 

### <a name="link-asset-with-asset-delivery-policy"></a>将资产与资产传送策略相链接
请参阅[将资产与资产传送策略相链接](#link_asset_with_asset_delivery_policy)

## <a id="types"></a>定义 AssetDeliveryPolicy 时使用的类型

### <a name="assetdeliveryprotocol"></a>AssetDeliveryProtocol

以下枚举说明可以为资产传递协议设置的值。

    [Flags]
    public enum AssetDeliveryProtocol
    {
        /// <summary>
        /// No protocols.
        /// </summary>
        None = 0x0,

        /// <summary>
        /// Smooth streaming protocol.
        /// </summary>
        SmoothStreaming = 0x1,

        /// <summary>
        /// MPEG Dynamic Adaptive Streaming over HTTP (DASH)
        /// </summary>
        Dash = 0x2,

        /// <summary>
        /// Apple HTTP Live Streaming protocol.
        /// </summary>
        HLS = 0x4,

        ProgressiveDownload = 0x10, 
 
        /// <summary>
        /// Include all protocols.
        /// </summary>
        All = 0xFFFF
    }

### <a name="assetdeliverypolicytype"></a>AssetDeliveryPolicyType

以下枚举说明可以为资产传递策略类型设置的值。  

    public enum AssetDeliveryPolicyType
    {
        /// <summary>
        /// Delivery Policy Type not set.  An invalid value.
        /// </summary>
        None,

        /// <summary>
        /// The Asset should not be delivered via this AssetDeliveryProtocol. 
        /// </summary>
        Blocked, 

        /// <summary>
        /// Do not apply dynamic encryption to the asset.
        /// </summary>
        /// 
        NoDynamicEncryption,  

        /// <summary>
        /// Apply Dynamic Envelope encryption.
        /// </summary>
        DynamicEnvelopeEncryption,

        /// <summary>
        /// Apply Dynamic Common encryption.
        /// </summary>
        DynamicCommonEncryption
        }

### <a name="contentkeydeliverytype"></a>ContentKeyDeliveryType

以下枚举说明可用于配置到客户端的内容密钥传递方法的值。
    
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        ///
        </summary>
        None = 0,

        /// <summary>
        /// Use PlayReady License acquisition protocol
        ///
        </summary>
        PlayReadyLicense = 1,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        ///
        </summary>
        BaselineHttp = 2,

        /// <summary>
        /// Use Widevine License acquisition protocol
        ///
        </summary>
        Widevine = 3

    }


### <a name="assetdeliverypolicyconfigurationkey"></a>AssetDeliveryPolicyConfigurationKey

以下枚举说明为配置用于获取资产传递策略的特定配置的密钥可以设置的值。

    public enum AssetDeliveryPolicyConfigurationKey
    {
        /// <summary>
        /// No policies.
        /// </summary>
        None,

        /// <summary>
        /// Exact Envelope key URL.
        /// </summary>
        EnvelopeKeyAcquisitionUrl,

        /// <summary>
        /// Base key url that will have KID=<Guid> appended for Envelope.
        /// </summary>
        EnvelopeBaseKeyAcquisitionUrl,

        /// <summary>
        /// The initialization vector to use for envelope encryption in Base64 format.
        /// </summary>
        EnvelopeEncryptionIVAsBase64,

        /// <summary>
        /// The PlayReady License Acquisition Url to use for common encryption.
        /// </summary>
        PlayReadyLicenseAcquisitionUrl,

        /// <summary>
        /// The PlayReady Custom Attributes to add to the PlayReady Content Header
        /// </summary>
        PlayReadyCustomAttributes,

        /// <summary>
        /// The initialization vector to use for envelope encryption.
        /// </summary>
        EnvelopeEncryptionIV,

        /// <summary>
        /// Widevine DRM acquisition url
        /// </summary>
        WidevineLicenseAcquisitionUrl
    }

## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

