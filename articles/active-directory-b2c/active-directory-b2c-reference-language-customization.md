---
title: Azure Active Directory B2C 中的语言自定义
description: 了解如何自定义用户流中的语言体验。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: bced7a4b994172a1a2076149d6f25adb39c99b54
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2019
ms.locfileid: "69015567"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 中的语言自定义

用户流可以使用 Azure Active Directory B2C (Azure AD B2C) 中的语言自定义来适应不同的语言，从而满足客户需求。 Microsoft 提供 [36 种语言](#supported-languages)的翻译，但你也可以为任何语言提供自己的翻译。 即使体验是针对一种语言提供的，也可以自定义页面上的任何文本。

## <a name="how-language-customization-works"></a>语言自定义的工作原理

通过语言自定义可选择用户流可用于的语言。 启用该功能后，可以从应用程序提供查询字符串参数 `ui_locales`。 调用 Azure AD B2C 时，我们会根据指定的区域设置翻译页面。 使用此配置类型可以完全控制用户流中的语言，并忽略客户浏览器的语言设置。

可能不需要对客户看到的语言拥有这种控制度。 如果未提供 `ui_locales` 参数，客户的体验由其浏览器设置确定。 仍可以控制要将用户流翻译成哪种语言，只需将该语言添加为支持的语言即可。 如果客户浏览器设置为显示你不希望支持的语言，则将改为显示你在受支持区域性中选为默认的语言。

* **ui-locales 指定的语言**：启用语言自定义后，用户流将翻译成此处指定的语言。
* **浏览器请求的语言**：如果未指定 `ui_locales` 参数，用户流将翻译成浏览器请求的语言（*如果该语言受支持*）。
* **策略默认语言**：如果浏览器未指定语言，或者指定了不支持的语言，用户流将翻译成用户流默认语言。

> [!NOTE]
> 如果使用自定义用户属性，需要提供自己的翻译。 有关详细信息，请参阅[自定义字符串](#customize-your-strings)。

## <a name="support-requested-languages-for-ui_locales"></a>支持 ui_locales 请求的语言

在语言自定义正式版发布之前创建的策略将需要先启用此功能。 之后创建的策略和用户流默认情况下已启用语言自定义。

在用户流中启用语言自定义后，可以通过添加 `ui_locales` 参数来控制用户流的语言。

1. 在 Azure AD B2C 租户中，选择“用户流”。
1. 单击想要启用翻译的用户流。
1. 选择“语言”。
1. 选择“启用语言自定义”。

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>选择要在用户流中启用的语言

为用户流启用一组语言，以便在无 `ui_locales` 参数的浏览器提出请求时翻译成这些语言。

1. 确保已根据前面的说明为用户流启用语言自定义。
1. 在用户流的“语言”页面，选择想要支持的语言。
1. 在属性窗格中，将“已启用”更改为“是”。
1. 选择属性窗格顶部的“保存”。

>[!NOTE]
>如果未提供 `ui_locales` 参数，则仅当客户的浏览器语言已启用时，才将页面翻译成此语言
>

## <a name="customize-your-strings"></a>自定义字符串

使用语言自定义可以自定义用户流中的任何字符串。

1. 确保已根据前面的说明为用户流启用语言自定义。
1. 在用户流的“语言”页面，选择想要自定义的语言。
1. 在“页面级别资源文件”下，选择想要编辑的页面。
1. 选择“下载默认值”（如果以前已编辑这种语言，则选择“下载重写”）。

执行这些步骤可以创建用于开始编辑字符串的 JSON 文件。

### <a name="change-any-string-on-the-page"></a>更改页面上的任何字符串

1. 在 JSON 编辑器中打开根据前面的说明下载的 JSON 文件。
1. 找到想要更改的元素。 可以查找所需字符串的 `StringId`，或者查找想要更改的 `Value` 属性。
1. 使用想要显示的内容更新 `Value` 属性。
1. 对于每个要更改的字符串，将 `Override` 更改为 `true`。
1. 保存文件并上传更改。 （可在下载 JSON 文件的同一位置找到上传控件。）

> [!IMPORTANT]
> 如果需要重写字符串，请确保将 `Override` 值设置为 `true`。 如果未更改该值，将忽略该条目。

### <a name="change-extension-attributes"></a>更改扩展属性

若要更改自定义用户属性的字符串或者想要在 JSON 中添加一个字符串，该字符串需采用以下格式：

```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```

将 `<ExtensionAttribute>` 替换为自定义用户属性的名称。

将 `<ExtensionAttributeValue>` 替换为要显示的新字符串。

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>使用 LocalizedCollections 提供值列表

若要为响应提供一组值列表，需要创建 `LocalizedCollections` 属性。 `LocalizedCollections` 是 `Name` 和 `Value` 对的数组。 项目的顺序将是它们显示的顺序。 若要添加 `LocalizedCollections`，请使用以下格式：

```JSON
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType",
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```

* `ElementId` 是此 `LocalizedCollections` 属性响应的用户属性。
* `Name` 是向用户显示的值。
* `Value` 是选择此选项时要在声明中返回的内容。

### <a name="upload-your-changes"></a>上传更改

1. 完成对 JSON 文件的更改后，返回到 B2C 租户。
1. 选择“用户流”，单击想要启用翻译的用户流。
1. 选择“语言”。
1. 选择要翻译成的语言。
1. 选择想要提供翻译的页面。
1. 选择文件夹图标，选择要上传的 JSON 文件。

更改将自动保存到用户流。

## <a name="customize-the-page-ui-by-using-language-customization"></a>使用语言自定义来自定义页面 UI

可通过两种方法本地化 HTML 内容。 一种方法是启用[语言自定义](active-directory-b2c-reference-language-customization.md)。 启用此功能可允许 Azure AD B2C 将 OpenID connect 参数`ui-locales`转发到终结点。 内容服务器可使用此参数提供特定语言的自定义 HTML 页面。

或者，可以基于所用的区域设置从不同位置拉取内容。 在已启用 CORS 的终结点中，可以设置文件夹结构以托管特定语言的内容。 如果使用通配符值 `{Culture:RFC5646}`，则会调用正确的语言。 例如，假设自定义页 URI 如下：

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```

可以在 `fr` 中加载页面。 当页面拉取 HTML 和 CSS 内容时，将从以下项拉取：

```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>添加自定义语言

还可以添加 Microsoft 目前未为其提供翻译的语言。 需要为用户流中的所有字符串提供翻译。 语言和区域设置代码仅限于 ISO 639-1 标准中的代码。

1. 在 Azure AD B2C 租户中，选择“用户流”。
2. 单击想要添加自定义语言的用户流，然后单击“语言”。
3. 从页面顶部选择“添加自定义语言”。
4. 在打开的上下文窗格中，通过输入有效的区域设置代码确定要为其提供翻译的语言。
5. 对于每个页，可以下载一组英语重写，并处理翻译。
6. 完成 JSON 文件后，可为每个页面上传这些文件。
7. 选择“启用”，用户流即可为用户显示此语言。
8. 保存语言。

>[!IMPORTANT]
>在保存之前，你需要启用自定义语言或上传替代语言。

## <a name="additional-information"></a>其他信息

### <a name="page-ui-customization-labels-as-overrides"></a>页面 UI 自定义标签保留为重写

启用语言自定义时，会在英语 (en) 的 JSON 文件中保留以前使用页面 UI 自定义的标签编辑。 可以通过在语言自定义中上传语言资源来继续更改标签和其他字符串。

### <a name="up-to-date-translations"></a>最新的翻译

Microsoft 致力于提供最新的翻译以供使用。 Microsoft 会持续改进翻译，使其符合需要。 Microsoft 将识别全局术语中的 bug 和更改，并在用户流中进行无缝更新。

### <a name="support-for-right-to-left-languages"></a>对从右向左书写的语言的支持

Microsoft 目前不支持从右向左书写的语言。 你可以通过使用自定义区域设置并使用 CSS 更改字符串的显示方式来实现此目的。 如果需要此功能，请在 [Azure 反馈](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag)中为此功能投票。

### <a name="social-identity-provider-translations"></a>社交标识提供者翻译

Microsoft 为社交登录名提供 `ui_locales` OIDC 参数。 但某些社交标识提供者（包括 Facebook 和 Google）并不遵循此参数。

### <a name="browser-behavior"></a>浏览器行为

Chrome 和 Firefox 都会请求其设置的语言。 如果支持该语言，将先显示该语言，再显示默认语言。 Microsoft Edge 目前不会请求语言，而是直接使用默认语言。

## <a name="supported-languages"></a>支持的语言

Azure AD B2C 包括对以下语言的支持。 用户流语言由 Azure AD B2C 提供。 多因素身份验证 (MFA) 通知语言由[AZURE MFA](../active-directory/authentication/concept-mfa-howitworks.md)提供。

| 语言              | 语言代码 | 用户流         | MFA 通知  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| 阿拉伯语                | ar            | :x:                | :heavy_check_mark: |
| 保加利亚语             | bg            | :x:                | :heavy_check_mark: |
| 孟加拉语                | bn            | :heavy_check_mark: | :x:                |
| 加泰罗尼亚语               | 认证            | :x:                | :heavy_check_mark: |
| 捷克语                 | cs            | :heavy_check_mark: | :heavy_check_mark: |
| 丹麦语                | da            | :heavy_check_mark: | :heavy_check_mark: |
| 德语                | de            | :heavy_check_mark: | :heavy_check_mark: |
| 希腊语                 | el            | :heavy_check_mark: | :heavy_check_mark: |
| 英语               | zh-CN            | :heavy_check_mark: | :heavy_check_mark: |
| 西班牙语               | es            | :heavy_check_mark: | :heavy_check_mark: |
| 爱沙尼亚语              | et            | :x:                | :heavy_check_mark: |
| 巴斯克语                | 各            | :x:                | :heavy_check_mark: |
| 芬兰语               | fi            | :heavy_check_mark: | :heavy_check_mark: |
| 法语                | fr            | :heavy_check_mark: | :heavy_check_mark: |
| 加利西亚语              | gl            | :x:                | :heavy_check_mark: |
| 古吉拉特语              | gu            | :heavy_check_mark: | :x:                |
| 希伯来语                | he            | :x:                | :heavy_check_mark: |
| 印地语                 | hi            | :heavy_check_mark: | :heavy_check_mark: |
| 克罗地亚语              | 小时            | :heavy_check_mark: | :heavy_check_mark: |
| 匈牙利语             | hu            | :heavy_check_mark: | :heavy_check_mark: |
| 印度尼西亚语            | id            | :x:                | :heavy_check_mark: |
| 意大利语               | it            | :heavy_check_mark: | :heavy_check_mark: |
| 日语              | ja            | :heavy_check_mark: | :heavy_check_mark: |
| 哈萨克语                | kk            | :x:                | :heavy_check_mark: |
| 卡纳达语               | kn            | :heavy_check_mark: | :x:                |
| 韩语                | ko            | :heavy_check_mark: | :heavy_check_mark: |
| 立陶宛语            | lt            | :x:                | :heavy_check_mark: |
| 拉脱维亚语               | lv            | :x:                | :heavy_check_mark: |
| 马拉雅拉姆语             | ml            | :heavy_check_mark: | :x:                |
| 马拉地语               | mr            | :heavy_check_mark: | :x:                |
| 马来语                 | 毫秒            | :heavy_check_mark: | :heavy_check_mark: |
| 挪威博克马尔语      | nb            | :heavy_check_mark: | :x:                |
| 荷兰语                 | nl            | :heavy_check_mark: | :heavy_check_mark: |
| 挪威语             | 否            | :x:                | :heavy_check_mark: |
| 旁遮普语               | pa            | :heavy_check_mark: | :x:                |
| 波兰语                | pl            | :heavy_check_mark: | :heavy_check_mark: |
| 葡萄牙语 - 巴西   | pt-br         | :heavy_check_mark: | :heavy_check_mark: |
| 葡萄牙语 - 葡萄牙 | pt-pt         | :heavy_check_mark: | :heavy_check_mark: |
| 罗马尼亚语              | ro            | :heavy_check_mark: | :heavy_check_mark: |
| 俄语               | ru            | :heavy_check_mark: | :heavy_check_mark: |
| 斯洛伐克语                | sk            | :heavy_check_mark: | :heavy_check_mark: |
| 斯洛文尼亚语             | sl            | :x:                | :heavy_check_mark: |
| 塞尔维亚语-西里尔语    | cryl-cs    | :x:                | :heavy_check_mark: |
| 塞尔维亚语-拉丁语       | latn-cs    | :x:                | :heavy_check_mark: |
| 瑞典语               | sv            | :heavy_check_mark: | :heavy_check_mark: |
| 泰米尔语                 | ta            | :heavy_check_mark: | :x:                |
| 泰卢固语                | te            | :heavy_check_mark: | :x:                |
| 泰语                  | %            | :heavy_check_mark: | :heavy_check_mark: |
| 土耳其语               | tr            | :heavy_check_mark: | :heavy_check_mark: |
| 乌克兰语             | 英式            | :x:                | :heavy_check_mark: |
| 越南语            | vi            | :x:                | :heavy_check_mark: |
| 简体中文  | zh-hans       | :heavy_check_mark: | :heavy_check_mark: |
| 繁体中文 | zh-hant       | :heavy_check_mark: | :heavy_check_mark: |
