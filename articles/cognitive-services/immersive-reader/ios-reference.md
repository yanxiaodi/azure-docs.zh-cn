---
title: 沉浸式读者 iOS SDK 参考
titleSuffix: Azure Cognitive Services
description: 沉浸式读者 iOS SDK 参考
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 08/01/2019
ms.author: metan
ms.openlocfilehash: 615c09dd8a7287918bb009ce11854278b21554c1
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69899411"
---
# <a name="immersive-reader-sdk-reference"></a>沉浸式读者 SDK 参考

沉浸式读者 iOS SDK 是一个 Swift CocoaPod, 可让你将沉浸式读者集成到 iOS 应用程序中。

## <a name="functions"></a>函数

SDK 公开单一函数`launchImmersiveReader(navController, token, subdomain, content, options, onSuccess, onFailure)`。

### <a name="launchimmersivereader"></a>launchImmersiveReader

在 iOS 应用程序中启动视图控制器, 启动沉浸式读取器。

```swift
public func launchImmersiveReader(navController: UINavigationController, token: String, subdomain: String, content: Content, options: Options?, onSuccess: @escaping () -> Void, onFailure: @escaping (_ error: Error) -> Void)
```

#### <a name="parameters"></a>Parameters

| 姓名 | 类型 | 描述 |
| ---- | ---- |------------ |
| `navController` | UINavigationController | 从中调用函数的 iOS 应用程序的导航控制器。 |
| `token` | String | Azure AD 身份验证令牌。 请参阅[Azure AD authentication 操作方法](./azure-active-directory-authentication.md)。 |
| `subdomain` | String | Azure 中沉浸式读者资源的自定义子域。 请参阅[Azure AD authentication 操作方法](./azure-active-directory-authentication.md)。 |
| `content` | [内容](#content) | 一个对象, 该对象包含要在沉浸式读取器中显示的内容。 |
| `options` | [选项](#options) | 用于配置沉浸式读者的某些行为的选项。 可选。 |
| `onSuccess` | ()-> Void | 当沉浸式阅读器成功启动时调用的闭包。 |
| `onFailure` | (_ 错误:[错误](#error))-> Void | 当沉浸式读取器加载失败时调用的闭包。 此关闭将返回[`Error`](#error)一个对象, 该对象表示与失败关联的错误代码和错误消息。 有关详细信息, 请参阅[错误代码](#error-codes)。 |

## <a name="types"></a>类型

### <a name="content"></a>内容

包含要在沉浸式阅读器中显示的内容。

```swift
struct Content: Encodable {
    var title: String
    var chunks: [Chunk]
}
```

#### <a name="supported-mime-types"></a>支持的 MIME 类型

| MIME 类型 | 描述 |
| --------- | ----------- |
| text/plain | 纯文本。 |
| application/mathml+xml | 数学标记语言 (MathML)。 [了解详细信息](https://developer.mozilla.org/en-US/docs/Web/MathML)。

### <a name="options"></a>选项

包含配置沉浸式读者的某些行为的属性。

```swift
struct Options {
    var uiLang: String?
    var timeout: TimeInterval?
}
```

### <a name="error"></a>Error

包含有关错误的信息。

```swift
struct Error {
    var code: String
    var message: String
}
```

#### <a name="error-codes"></a>错误代码

| 代码 | 描述 |
| ---- | ----------- |
| BadArgument | 提供的参数无效, 有关`message`详细信息, 请参阅。 |
| 超时 | 沉浸式读取器无法在指定的超时内加载。 |
| TokenExpired | 提供的令牌已过期。 |
| 已终止 | 超出了调用速率限制。 |
| InternalError | 沉浸式读者视图控制器内部出现内部错误。 有关`message`详细信息, 请参阅。|

## <a name="os-version-support"></a>操作系统版本支持

在 iPad 和 iPhone 上, 适用于 iOS 9.0 或更高版本的沉浸式读者 iOS SDK 受支持。

## <a name="next-steps"></a>后续步骤

* 了解[GitHub 上的沉浸式读者 IOS SDK](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS)
* [快速入门：创建启动沉浸式读者 (Swift) 的 iOS 应用](./ios-quickstart.md)