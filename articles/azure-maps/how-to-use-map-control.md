---
title: Azure Maps 中的 web 地图控件入门 |Microsoft Docs
description: 了解如何使用 Azure Maps map control 客户端 Javascript 库。
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: fa6a3af7893830eba2f4a5c43184991bff68d8a8
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69898209"
---
# <a name="use-the-azure-maps-map-control"></a>使用 Azure Maps map 控件

可通过 Map Control 客户端 Javascript 库呈现地图，并将 Azure Maps 功能嵌入到 Web 或移动应用中。

## <a name="create-a-new-map-in-a-web-page"></a>在网页中创建新地图

通过使用 Map Control 客户端 Javascript 库，可以在网页中嵌入地图。

1. 创建新的 HTML 文件。

2. 载入 Azure Maps Web SDK。 可以使用以下两个选项之一执行此操作：

    a. 通过在该文件的 `<head>` 元素中添加样式表和脚本引用的 URL 终结点，使用 Azure Maps Web SDK 的全局承载的 CDN 版本：

    ```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
    ```

    b. 另外，还可以使用 [azure-maps-control](https://www.npmjs.com/package/azure-maps-control) NPM 程序包将 Azure Maps Web SDK 源代码加载到本地并将其与你的应用承载在一起。 此程序包还包括了 TypeScript 定义。

    > npm install azure-maps-control

    然后，在该文件的 `<head>` 元素中添加对 Azure Maps 样式表和脚本源的引用：

    ```HTML
    <link rel="stylesheet" href="node_modules/azure-maps-control/dist/css/atlas.min.css" type="text/css">
    <script src="node_modules/azure-maps-control/dist/js/atlas.min.js"></script>
    ```

3. 若要以填满整个页面正文的方式呈现地图，请向 `<head>` 元素中添加以下 `<style>` 元素。

    ```HTML
    <style>
        html, body {
            margin: 0;
        }

        #myMap {
            height: 100vh;
            width: 100vw;
        }
    </style>
    ```

4. 在页面的正文中，添加一个 `<div>` 元素并将其 `id` 指定为 **myMap**。

    ```HTML
    <body>
        <div id="myMap"></div>
    </body>
    ```

5. 要初始化地图控件，请在 html 正文中定义新部分并创建脚本。 `Map` `document.getElementById('myMap')` `<div>`在创建类的实例时, 传入`HTMLElement`映射的或 (例如) 作为第一个参数。 `id` 通过[身份验证选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions)使用你自己的 Azure Maps 帐户密钥或 Azure Active Directory (AAD) 凭据对地图进行身份验证。 如果需要创建帐户或查找密钥，请参阅[如何管理 Azure Maps 帐户和密钥](how-to-manage-account-keys.md)。 **language** 选项指定用于地图标签和控件的语言。 有关受支持语言的详细信息，请参阅[支持的语言](supported-languages.md)。 如果使用订阅密钥进行身份验证：

    ```HTML
    <script type="text/javascript">
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'subscriptionKey',
                subscriptionKey: '<Your Azure Maps Key>'
            }
        });
    </script>
    ```

    如果使用 Azure Active Directory (AAD) 进行身份验证：

    ```HTML
    <script type="text/javascript">
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'aad',
                clientId: '<Your AAD Client Id>',
                aadAppId: '<Your AAD App Id>',
                aadTenant: '<Your AAD Tenant Id>'
            }
        });
    </script>
    ```

    有关详细信息, 请参阅[Azure Maps 的身份验证](azure-maps-authentication.md)文档。

6. （可选）在页面的头部添加以下元标记元素，你会发现这比较有用：

    ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    ```

7. 将 HTML 文件放在一起应类似于以下代码:

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <style>
            html, body {
                margin: 0;
            }

            #myMap {
                height: 100vh;
                width: 100vw;
            }
        </style>
    </head>
    <body>
        <div id="myMap"></div>

        <script type="text/javascript">
            //Create an instance of the map control and set some options.
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });
        </script>
    </body>
    </html>
    ```

8. 在 Web 浏览器中打开该文件并查看呈现的地图。 它应类似于以下代码:

    <iframe height="700" style="width: 100%;" scrolling="no" title="如何使用地图控件" src="//codepen.io/azuremaps/embed/yZpEYL/?height=557&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">请参阅 CodePen 上的<a href='https://codepen.io'></a>(<a href='https://codepen.io/azuremaps'>@azuremaps</a>), 了解<a href='https://codepen.io/azuremaps/pen/yZpEYL/'>如何使用地图 Azure Maps 控件</a>。
    </iframe>

## <a name="localizing-the-map"></a>本地化地图

Azure Maps 提供了两种不同的方法来设置地图的语言和区域视图。 第一种方法是将此信息添加到全局`atlas`命名空间, 这会导致应用中的所有地图控件实例默认设置为这些设置。 下面将语言设置为法语 ("fr"), 并将区域视图设置为 "auto":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

第二种方法是在加载映射时将此信息传递到映射选项, 如下所示:

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'auto',

    authOptions: {
        authType: 'aad',
        clientId: '<Your AAD Client Id>',
        aadAppId: '<Your AAD App Id>',
        aadTenant: '<Your AAD Tenant Id>'
    }
});
```

> [!Note]
> 利用 Web SDK, 可以在具有不同语言和区域设置的同一页上加载多个映射实例。 此外, 在使用`setStyle`映射的函数加载映射后, 可以更新这些设置。 

下面是将语言设置为 "fr" 并将区域视图设置为 "自动" 的 Azure Maps 的示例。

![以法语显示标签的地图图像](./media/how-to-use-map-control/websdk-localization.png)

[此处](supported-languages.md)介绍了支持的语言和区域视图的完整列表。

## <a name="next-steps"></a>后续步骤

了解如何创建地图并与之进行交互：

> [!div class="nextstepaction"]
> [创建地图](map-create.md)

了解如何设置地图样式：

> [!div class="nextstepaction"]
> [选择地图样式](choose-map-style.md)

将更多数据添加到地图:

> [!div class="nextstepaction"]
> [创建地图](map-create.md)

> [!div class="nextstepaction"]
> [代码示例](https://docs.microsoft.com/samples/browse/?products=azure-maps)