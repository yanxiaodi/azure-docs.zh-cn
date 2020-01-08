---
title: 自定义用户界面 Azure Active Directory B2C
description: 了解如何自定义使用 Azure Active Directory B2C 的应用程序的用户界面。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 6ebaeedf88bc02aa16e8be07fcb734e44ffa5bb6
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71258172"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>自定义用户界面 Azure Active Directory B2C

品牌和自定义用户界面，向客户显示 Azure Active Directory B2C （Azure AD B2C），有助于在应用程序中提供无缝用户体验。 这些体验包括注册、登录、配置文件编辑和密码重置。 本文介绍用户界面（UI）自定义用户流和自定义策略的方法。

## <a name="ui-customization-in-different-scenarios"></a>不同方案中的 UI 自定义

可以通过多种方式自定义用户体验应用程序的用户界面，每种方法都适用于不同的方案。

### <a name="user-flows"></a>用户流

如果使用[用户流](active-directory-b2c-reference-policies.md)，则可以通过使用内置*页面布局模板*或使用自己的 HTML 和 CSS 来更改用户流页面的外观。 本文后面将讨论这两种方法。

使用[Azure 门户](tutorial-customize-ui.md)为用户流配置 UI 自定义。

### <a name="custom-policies"></a>自定义策略

如果要使用[自定义策略](active-directory-b2c-overview-custom.md)在应用程序中提供注册或登录、密码重置或配置文件编辑，请使用[策略文件来自定义 UI](active-directory-b2c-ui-customization-custom.md)。

如果需要根据客户的决定提供动态内容，请使用可以根据在查询字符串中发送的参数[动态更改页面内容](active-directory-b2c-ui-customization-custom-dynamic.md)的自定义策略。 例如，可以基于从 web 或移动应用程序传递的参数，更改 Azure AD B2C 注册或登录页面上的背景图像。

### <a name="javascript"></a>JavaScript

可以在[用户流](user-flow-javascript-overview.md)和[自定义策略](page-layout.md)中启用客户端 JavaScript 代码。

### <a name="sign-in-only-ui-customization"></a>仅登录 UI 自定义

如果你仅提供登录，同时提供其随附的密码重置页面和验证电子邮件，请使用用于[Azure AD 登录页](../active-directory/fundamentals/customize-branding.md)的相同自定义步骤。

如果客户尝试在登录之前编辑其配置文件，则会将其重定向到你使用用于自定义 Azure AD 登录页的相同步骤自定义的页面。

## <a name="page-layout-templates"></a>页面布局模板

用户流提供了多个内置模板，你可以从中进行选择，为你的用户体验页面提供专业的外观。 这些布局模板还可用作自己自定义的起点。

在左侧菜单中的 "**自定义**" 下，选择 "**页面布局**"，然后选择 "**模板**"。

![Azure 门户的用户流页中的模板选择下拉列表](media/customize-ui-overview/template-selection.png)

接下来，从列表中选择一个模板。 下面是每个模板的登录页面示例：

| 海蓝 | 石板灰 | 经典 |
|:-:|:-:|:-:|
|![注册登录页上呈现的“海蓝”模板的示例](media/customize-ui-overview/template-ocean-blue.png)|![在注册登录页上呈现的石板灰色模板的示例](media/customize-ui-overview/template-slate-gray.png)|![在注册登录页上呈现的经典模板的示例](media/customize-ui-overview/template-classic.png)|

选择某个模板时，选定的布局将应用于用户流中的所有页面，并且每个页面的 URI 将显示在“自定义页面 URI”字段中。

## <a name="custom-html-and-css"></a>自定义 HTML 和 CSS

Azure AD B2C 使用称为[跨域资源共享（CORS）](https://www.w3.org/TR/cors/)的方法在客户的浏览器中运行代码。

在运行时，将从你在用户流或自定义策略中指定的 URL 加载内容。 用户体验中的每一页都从你为该页指定的 URL 加载其内容。 从 URL 加载内容后，它会与 Azure AD B2C 插入的 HTML 片段合并，然后向客户显示页面。

在使用自己的 HTML 和 CSS 文件自定义 UI 之前，请查看以下指南：

- Azure AD B2C 将 HTML 内容**合并**到页面中。 请勿复制并尝试更改 Azure AD B2C 提供的默认内容。 最好从头开始构建 HTML5 内容，将默认内容用作参考。
- **JavaScript**可以包含在[用户流](user-flow-javascript-overview.md)和[自定义策略](javascript-samples.md)的自定义内容中。
- 支持的**浏览器版本**包括：
  - Internet Explorer 11、10和 Microsoft Edge
  - 对 Internet Explorer 9 和 8 的支持有限
  - Google Chrome 42.0 和更高版本
  - Mozilla Firefox 38.0 和更高版本
- 不要在 HTML 中包含**窗体标记**。 表单标记会干扰由 Azure AD B2C 插入的 HTML 生成的 POST 操作。

### <a name="where-do-i-store-ui-content"></a>在何处存储 UI 内容？

使用自己的 HTML 和 CSS 文件自定义 UI 时，可以在任何支持 CORS 的公开可用 HTTPS 终结点上托管 UI 内容。 例如， [Azure Blob 存储](../storage/blobs/storage-blobs-introduction.md)、web 服务器、CDN、AWS S3 或文件共享系统。

重要的一点是，可以在启用了 CORS 的公共 HTTPS 终结点上托管内容。 在内容中指定绝对 URL 时，必须使用绝对 URL。

## <a name="get-started-with-custom-html-and-css"></a>自定义 HTML 和 CSS 入门

遵循这些准则，开始在用户体验页中使用自己的 HTML 和 CSS。

- 使用位于 `<body>` 中某处的空 `<div id="api"></div>` 元素创建格式正确的 HTML 内容。 此元素标记插入 Azure AD B2C 内容的位置。 以下示例显示了一个最小化页面：

    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>!Add your title here!</title>
        <link rel="stylesheet" href="https://mystore1.blob.core.windows.net/b2c/style.css">
      </head>
      <body>
        <h1>My B2C Application</h1>
        <div id="api"></div>   <!-- Leave this element empty because Azure AD B2C will insert content here. -->
      </body>
    </html>
    ```

- 使用 CSS 设置 Azure AD B2C 插入页面的 UI 元素的样式。 以下示例显示了一个简单的 CSS 文件，其中还包含注册注入 HTML 元素的设置：

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center;
    }
    .entry {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center;
    }
    .create {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

- 在 HTTPS 终结点上托管内容（允许 CORS）。 配置 CORS 时必须启用 GET 和 OPTIONS 请求方法。
- 创建或编辑用户流或自定义策略，以使用创建的内容。

### <a name="html-fragments-from-azure-ad-b2c"></a>Azure AD B2C 中的 HTML 片段

下表列出了 Azure AD B2C 合并到内容中 `<div id="api"></div>` 元素的 HTML 片段。

| 插入的页 | HTML 说明 |
| ------------- | ------------------- |
| 标识提供者选项 | 包含标识提供者的按钮列表，客户可在注册或登录时选择这些按钮。 这些按钮包括社交标识提供者（如 Facebook 和 Google）或本地帐户（基于电子邮件地址或用户名）。 |
| 本地帐户注册 | 包含一个窗体，用于基于电子邮件地址或用户名的本地帐户注册。 该窗体可以包含不同的输入控件，如文本输入框、密码输入框、单选按钮、单选下拉框和多选复选框。 |
| 社交帐户注册 | 使用社交标识提供者（如 Facebook 或 Google+）的现有帐户进行注册时，可能会显示此页面。 当必须使用注册窗体从客户收集其他信息时，可使用此方法。 |
| 统一注册或登录 | 处理客户的注册和登录，这些客户可以使用社交标识提供者（Facebook、Google）的帐户或本地帐户。 |
| 多重身份验证 | 用户可以在注册或登录期间（使用文字或语音）验证其电话号码。 |
| Error | 向客户提供错误信息。 |

## <a name="localize-content"></a>本地化内容

可通过在 Azure AD B2C 租户中启用[语言自定义](active-directory-b2c-reference-language-customization.md)来本地化 HTML 内容。 启用此功能将允许 Azure AD B2C 将 OpenID connect 参数`ui-locales`转发到终结点。 内容服务器可使用此参数提供特定语言的 HTML 页。

可以基于所用的区域设置从不同位置拉取内容。 在已启用 CORS 的终结点中，可以设置文件夹结构以托管特定语言的内容。 如果使用通配符值 `{Culture:RFC5646}`，则会调用正确的语言。

例如，你的自定义页 URI 可能如下所示：

```HTTP
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

可以通过从以下项拉取内容来加载法语页面：

```HTTP
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="examples"></a>示例

你可以在 GitHub 上的[AzureBlobStorage-客户端](https://github.com/azureadquickstarts/b2c-azureblobstorage-client)存储库中找到多个示例模板文件。

模板中的示例 HTML 和 CSS 文件位于[/sample_templates](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates)目录中。

## <a name="next-steps"></a>后续步骤

- 如果你使用的是**用户流**，则可以使用教程开始自定义 UI：

    [在 Azure Active Directory B2C 中自定义应用程序的用户界面](tutorial-customize-ui.md)。
- 如果使用的是**自定义策略**，则可以使用以下文章开始自定义 UI：

    [使用 Azure Active Directory B2C 中的自定义策略自定义应用程序的用户界面](active-directory-b2c-ui-customization-custom.md)。
