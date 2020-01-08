---
title: 使用 .NET SDK 配置资产传送策略 | Microsoft Docs
description: 本主题说明如何通过 Azure 媒体服务 .NET SDK 配置不同的资产传送策略。
services: media-services
documentationcenter: ''
author: Mingfeiy
manager: femila
editor: ''
ms.assetid: 3ec46f58-6cbb-4d49-bac6-1fd01a5a456b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: b5e733c93fef8920c73c8cf460dac7a7051fddb5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "61465603"
---
# <a name="configure-asset-delivery-policies-with-net-sdk"></a>使用 .NET SDK 配置资产传送策略
[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

## <a name="overview"></a>概述
如果打算传送加密资产，媒体服务内容传送工作流中的步骤之一是为资产配置传送策略。 资产传送策略告知媒体服务希望如何传送资产：应该将资产动态打包成哪种流式处理协议（例如 MPEG DASH、HLS、平滑流或全部），是否要动态加密资产以及如何加密（信封或常用加密）。

本文介绍为何以及如何创建和配置资产传送策略。

>[!NOTE]
>创建 AMS 帐户后，系统会将一个处于“已停止”状态的默认  流式处理终结点添加到帐户。  若要开始流式传输内容并利用动态打包和动态加密，要从中流式传输内容的流式处理终结点必须处于“正在运行”状态。  
>
>此外，要使用动态打包和动态加密，资产必须包含一组自适应比特率 MP4 或自适应比特率平滑流式处理文件。

可以将不同的策略应用到同一个资产。 例如，可以将 PlayReady 加密应用到平滑流式处理，将 AES 信封加密应用到 MPEG DASH 和 HLS。 将阻止流式处理传送策略中未定义的任何协议（例如，添加仅将 HLS 指定为协议的单个策略）。 如果根本没有定义任何资产传送策略，则属例外。 此时，将允许所有明文形式的协议。

如果要传送存储加密资产，则必须配置资产的传送策略。 在流式传输资产之前，流式处理服务器会删除存储加密，再使用指定的传送策略流式传输用户的内容。 例如，若要传送使用高级加密标准 (AES) 信封加密密钥加密的资产，请将策略类型设为“DynamicEnvelopeEncryption”  。 要删除存储加密并以明文的形式流式传输资产，请将策略类型设置为 **NoDynamicEncryption**。 下面是演示如何配置这些策略类型的示例。

根据配置资产传送策略的方式，可以动态打包、加密和流式传输以下流式处理协议：平滑流式处理、HLS 和 MPEG DASH。

以下列表显示了用于流式传输平滑流、HLS 和 DASH 的格式。

平滑流：

{流式处理终结点名称-媒体服务帐户名称}.streaming.mediaservices.windows.net/{定位符 ID}/{文件名}.ism/Manifest

HLS：

{流式处理终结点名称-媒体服务帐户名称}.streaming.mediaservices.windows.net/{定位符 ID}/{文件名}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{流式处理终结点名称-媒体服务帐户名称}.streaming.mediaservices.windows.net/{定位符 ID}/{文件名}.ism/Manifest(format=mpd-time-csf)

## <a name="considerations"></a>注意事项
* 删除 AssetDeliveryPolicy 之前，应删除所有与此资产关联的流式处理定位符。 如果需要，可稍后使用新的 AssetDeliveryPolicy 创建新的流式处理定位符。
* 如果未设置资产传送策略，则无法在存储加密的资产上创建流式处理定位符。  如果资产未经过存储加密，则即使未设置资产传送策略，系统也可让你顺利地创建定位符和流式处理资产。
* 可将多个资产传送策略关联到单个资产，但只能指定一种方法来处理给定的 AssetDeliveryProtocol。  也就是说，如果尝试链接两个指定 AssetDeliveryProtocol.SmoothStreaming 协议的传送策略，则会导致出错，因为当客户端发出平滑流请求时，系统不知道要应用哪个策略。
* 如果资产包含现有的流式处理定位符，则不能将新策略链接到该资产（可以取消现有策略与资产的链接，或者更新与该资产关联的传送策略）。  必须先删除流式处理定位符，调整策略，再重新创建流式处理定位符。  在重新创建流式处理定位符时可以使用同一个 locatorId，但应确保该操作不会导致客户端出现问题，因为内容可能已被来源或下游 CDN 缓存。

## <a name="clear-asset-delivery-policy"></a>清除资产传送策略

以下 ConfigureClearAssetDeliveryPolicy 方法会指定不应用动态加密，而是使用以下任一协议传送流：  MPEG DASH、HLS 和平滑流式处理协议。 可能需要对存储加密资产应用此策略。

有关创建 AssetDeliveryPolicy 时可以指定哪些值的信息，请参阅[定义 AssetDeliveryPolicy 时使用的类型](#types)部分。

```csharp
    static public void ConfigureClearAssetDeliveryPolicy(IAsset asset)
    {
        IAssetDeliveryPolicy policy =
        _context.AssetDeliveryPolicies.Create("Clear Policy",
        AssetDeliveryPolicyType.NoDynamicEncryption,
        AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);
        
        asset.DeliveryPolicies.Add(policy);
    }
```
## <a name="dynamiccommonencryption-asset-delivery-policy"></a>DynamicCommonEncryption 资产传送策略

以下 **CreateAssetDeliveryPolicy** 方法将创建 **AssetDeliveryPolicy**，该策略配置为将动态常用加密 (**DynamicCommonEncryption**) 应用到平滑流式处理协议（将阻止流式处理其他协议）。 该方法采用以下两种参数：Asset（要将传送策略应用到的资产）和 IContentKey（CommonEncryption 类型的内容密钥。有关详细信息，请参阅：    [创建内容密钥](media-services-dotnet-create-contentkey.md#common_contentkey)。

有关创建 AssetDeliveryPolicy 时可以指定哪些值的信息，请参阅[定义 AssetDeliveryPolicy 时使用的类型](#types)部分。

```csharp
    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);
        
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
            {
                {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
            };
    
            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                    "AssetDeliveryPolicy",
                AssetDeliveryPolicyType.DynamicCommonEncryption,
                AssetDeliveryProtocol.SmoothStreaming,
                assetDeliveryPolicyConfiguration);
    
            // Add AssetDelivery Policy to the asset
            asset.DeliveryPolicies.Add(assetDeliveryPolicy);
    
            Console.WriteLine();
            Console.WriteLine("Adding Asset Delivery Policy: " +
                assetDeliveryPolicy.AssetDeliveryPolicyType);
     }
```

Azure 媒体服务还允许添加 Widevine 加密。 以下示例演示将 PlayReady 和 Widevine 添加到资产传送策略。

```csharp
    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        // Get the PlayReady license service URL.
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);


        // GetKeyDeliveryUrl for Widevine attaches the KID to the URL.
        // For example: https://amsaccount1.keydelivery.mediaservices.windows.net/Widevine/?KID=268a6dcb-18c8-4648-8c95-f46429e4927c.  
        // The WidevineBaseLicenseAcquisitionUrl (used below) also tells Dynamic Encryption 
        // to append /? KID =< keyId > to the end of the url when creating the manifest.
        // As a result Widevine license acquisition URL will have KID appended twice, 
        // so we need to remove the KID that in the URL when we call GetKeyDeliveryUrl.

        Uri widevineUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine);
        UriBuilder uriBuilder = new UriBuilder(widevineUrl);
        uriBuilder.Query = String.Empty;
        widevineUrl = uriBuilder.Uri;

        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
        {
            {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
            {AssetDeliveryPolicyConfigurationKey.WidevineLicenseAcquisitionUrl, widevineUrl.ToString()}

        };

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.Dash,
            assetDeliveryPolicyConfiguration);


        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

    }
```
> [!NOTE]
> 使用 Widevine 加密时，只能使用 DASH 传送。 请确保在资产传送协议中指定 DASH。
> 
> 

## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>DynamicEnvelopeEncryption 资产传送策略
以下 **CreateAssetDeliveryPolicy** 方法将创建 **AssetDeliveryPolicy**，该策略配置为将动态信封加密 (**DynamicEnvelopeEncryption**) 应用到平滑流式处理、HLS 和 DASH 协议（如果不指定协议，则将阻止对这些协议进行流式处理）。 该方法采用以下两种参数：Asset（要将传送策略应用到的资产）和 IContentKey（EnvelopeEncryption 类型的内容密钥。有关详细信息，请参阅：    [创建内容密钥](media-services-dotnet-create-contentkey.md#envelope_contentkey)。

有关创建 AssetDeliveryPolicy 时可以指定哪些值的信息，请参阅[定义 AssetDeliveryPolicy 时使用的类型](#types)部分。   

```csharp
    private static void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {

        //  Get the Key Delivery Base Url by removing the Query parameter.  The Dynamic Encryption service will
        //  automatically add the correct key identifier to the url when it generates the Envelope encrypted content
        //  manifest.  Omitting the IV will also cause the Dynamic Encryption service to generate a deterministic
        //  IV for the content automatically.  By using the EnvelopeBaseKeyAcquisitionUrl and omitting the IV, this
        //  allows the AssetDelivery policy to be reused by more than one asset.
        //
        Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);
        UriBuilder uriBuilder = new UriBuilder(keyAcquisitionUri);
        uriBuilder.Query = String.Empty;
        keyAcquisitionUri = uriBuilder.Uri;

        // The following policy configuration specifies: 
        //   key url that will have KID=<Guid> appended to the envelope and
        //   the Initialization Vector (IV) to use for the envelope encryption.
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string> 
        {
            {AssetDeliveryPolicyConfigurationKey.EnvelopeBaseKeyAcquisitionUrl, keyAcquisitionUri.ToString()},
        };

        IAssetDeliveryPolicy assetDeliveryPolicy =
            _context.AssetDeliveryPolicies.Create(
                        "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicEnvelopeEncryption,
                        AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.Dash,
                        assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " + assetDeliveryPolicy.AssetDeliveryPolicyType);
    }
```

## <a id="types"></a>定义 AssetDeliveryPolicy 时使用的类型

### <a id="AssetDeliveryProtocol"></a>AssetDeliveryProtocol

以下枚举说明可以为资产传送协议设置的值。

```csharp
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
```
### <a id="AssetDeliveryPolicyType"></a>AssetDeliveryPolicyType

以下枚举说明可以为资产传递策略类型设置的值。  
```csharp
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
```
### <a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

以下枚举说明可用于配置将内容密钥传送到客户端的方法的值。
  ```csharp  
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
```
### <a id="AssetDeliveryPolicyConfigurationKey"></a>AssetDeliveryPolicyConfigurationKey

以下枚举说明为配置用于获取资产传递策略的特定配置的密钥可以设置的值。
```csharp
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
```
## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

