---
title: 在门户中配置应用 - Azure 应用服务
description: 了解如何在 Azure 门户中配置应用服务应用的常用设置。
keywords: azure 应用服务, web 应用, 应用设置, 环境变量
services: app-service\web
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/13/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 11a29a980fbbbafad850daeda5af11b78580bcaa
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70067007"
---
# <a name="configure-an-app-service-app-in-the-azure-portal"></a>在 Azure 门户中配置应用服务应用

本主题介绍如何使用 [Azure 门户]配置 Web 应用、移动后端或 API 应用的常用设置。

## <a name="configure-app-settings"></a>配置应用设置

在应用服务中, 应用设置是作为环境变量传递给应用程序代码的变量。 对于 Linux 应用和自定义容器, 应用服务使用`--env`标志将应用设置传递到容器, 以便在容器中设置环境变量。

在 [Azure 门户]中，导航到应用的管理页。 在应用的左侧菜单中，单击“配置” > “应用程序设置”。

![应用程序设置](./media/configure-common/open-ui.png)

对于 ASP.NET 和 ASP.NET Core 开发人员而言, 在应用服务中设置应用设置类似于`<appSettings>`在 web.config 或*appsettings*中设置应用设置, 但应用服务中的值会替代 web.config 中的值或*appsettings*。 你可以在 web.config 或*appsettings*中保留开发设置 (例如, 本地 MySQL 密码), 但在应用服务中, 可以确保生产机密 (例如, Azure MySQL 数据库密码) 安全。 相同的代码在本地调试时使用开发设置，部署到 Azure 时使用生产机密。

同样, 在运行时将应用设置作为环境变量获取。 有关特定语言堆栈的步骤, 请参阅:

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [自定义容器](containers/configure-custom-container.md#configure-environment-variables)

应用程序设置在存储时始终进行加密（静态加密）。

> [!NOTE]
> 也可以使用 [Key Vault 引用](app-service-key-vault-references.md)从 [Key Vault](/azure/key-vault/) 解析应用设置。

### <a name="show-hidden-values"></a>显示隐藏的值

默认情况下，出于安全考虑，应用设置值会隐藏在门户中。 若要查看某项应用设置的隐藏值，请单击该项设置的“值”字段。 若要查看所有应用设置的值，请单击“显示值”按钮。

### <a name="add-or-edit"></a>添加或编辑

若要添加新的应用设置，请单击“新建应用程序设置”。 在对话框中，可[将设置绑定到当前槽](deploy-staging-slots.md#which-settings-are-swapped)。

若要编辑设置，请单击右侧的“编辑”按钮。

完成后，单击“更新”。 别忘了返回“配置”页并单击“保存”。

> [!NOTE]
> 在默认的 linux 容器或自定义 linux 容器中, 应用设置名称`ApplicationInsights:InstrumentationKey`中的任何嵌套 JSON 密钥结构都需要在应用`ApplicationInsights__InstrumentationKey`服务中配置为密钥名称。 换句话说, any `:`应该`__`替换为 (双下划线)。
>

### <a name="edit-in-bulk"></a>批量编辑

若要批量添加或编辑应用设置，请单击“高级编辑”按钮。 完成后，单击“更新”。 别忘了返回“配置”页并单击“保存”。

应用设置采用以下 JSON 格式：

```json
[
  {
    "name": "<key-1>",
    "value": "<value-1>",
    "slotSetting": false
  },
  {
    "name": "<key-2>",
    "value": "<value-2>",
    "slotSetting": false
  },
  ...
]
```

## <a name="configure-connection-strings"></a>配置连接字符串

在 [Azure 门户]中，导航到应用的管理页。 在应用的左侧菜单中，单击“配置” > “应用程序设置”。

![应用程序设置](./media/configure-common/open-ui.png)

对于 ASP.NET 和 ASP.NET Core 开发人员而言，在应用服务中设置连接字符串类似于在 *Web.config* 中的 `<connectionStrings>` 内进行设置，但应用服务中设置的值会替代 *Web.config* 中的值。您可以在应用服务中安全地保存*web.config*中的开发设置 (例如, 数据库文件) 和生产机密 (例如, SQL 数据库凭据)。 相同的代码在本地调试时使用开发设置，部署到 Azure 时使用生产机密。

对于其他语言堆栈，最好是改用[应用设置](#configure-app-settings)，因为连接字符串需要在变量键中使用特殊的格式才能访问值。 但以下情况例外：如果在应用中配置了相应的连接字符串，则某些 Azure 数据库类型会连同应用一起备份。 有关详细信息，请参阅[备份的内容](manage-backup.md#what-gets-backed-up)。 如果不需要这种自动化备份，请使用应用设置。

在运行时，连接字符串可用作环境变量，其前缀为以下连接类型：

* SQL Server：`SQLCONNSTR_`
* MySQL：`MYSQLCONNSTR_`
* SQL 数据库：`SQLAZURECONNSTR_`
* 自定义：`CUSTOMCONNSTR_`

例如，可以使用环境变量 `MYSQLCONNSTR_connectionString1` 的形式访问名为 *connectionstring1* 的 MySql 连接字符串。 有关特定语言堆栈的步骤, 请参阅:

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [自定义容器](containers/configure-custom-container.md#configure-environment-variables)

连接字符串在存储时始终进行加密（静态加密）。

> [!NOTE]
> 也可以使用 [Key Vault 引用](app-service-key-vault-references.md)从 [Key Vault](/azure/key-vault/) 解析连接字符串。

### <a name="show-hidden-values"></a>显示隐藏的值

默认情况下，出于安全考虑，连接字符串的值会隐藏在门户中。 若要查看连接字符串的隐藏值，只需单击该字符串的“值”字段。 若要查看所有连接字符串的值，请单击“显示值”按钮。

### <a name="add-or-edit"></a>添加或编辑

若要添加新的连接字符串，请单击“新建连接字符串”。 在对话框中，可[将连接字符串绑定到当前槽](deploy-staging-slots.md#which-settings-are-swapped)。

若要编辑设置，请单击右侧的“编辑”按钮。

完成后，单击“更新”。 别忘了返回“配置”页并单击“保存”。

### <a name="edit-in-bulk"></a>批量编辑

若要批量添加或编辑连接字符串，请单击“高级编辑”按钮。 完成后，单击“更新”。 别忘了返回“配置”页并单击“保存”。

连接字符串采用以下 JSON 格式：

```json
[
  {
    "name": "name-1",
    "value": "conn-string-1",
    "type": "SQLServer",
    "slotSetting": false
  },
  {
    "name": "name-2",
    "value": "conn-string-2",
    "type": "PostgreSQL",
    "slotSetting": false
  },
  ...
]
```

<a name="platform"></a>
<a name="alwayson"></a>

## <a name="configure-general-settings"></a>配置常规设置

在 [Azure 门户]中，导航到应用的管理页。 在应用的左侧菜单中，单击“配置” > “应用程序设置”。

![常规设置](./media/configure-common/open-general.png)

在此处可以配置应用的某些常用设置。 某些设置要求[纵向扩展到更高的定价层](manage-scale-up.md)。

- **堆栈设置**：用于运行应用的软件堆栈，包括语言和 SDK 版本。 对于 Linux 应用和自定义的容器应用，还可以设置可选的启动命令或文件。
- **平台设置**：用于配置托管平台的设置，包括：
    - **位数**：32 位或 64 位。
    - **WebSocket 协议**：例如，[ASP.NET SignalR] 或 [socket.io](https://socket.io/)。
    - **Always On**：即使没有流量，也保持应用的加载状态。 它对于连续 Web 作业或使用 CRON 表达式触发的 Web 作业是必需的。
    - **托管管道版本**：IIS [管道模式]。 如果某个旧式应用需要旧版 IIS，请将此选项设置为“经典”。
    - **HTTP 版本**：设置为 **2.0**，以启用对 [HTTPS/2](https://wikipedia.org/wiki/HTTP/2) 协议的支持。
    > [!NOTE]
    > 大多数新型浏览器仅支持通过 TLS 的 HTTP/2 协议，而非加密流量继续使用 HTTP/1.1。 若要确保客户端浏览器通过 HTTP/2 连接到你的应用, 请为应用的自定义域[购买应用服务证书](web-sites-purchase-ssl-web-site.md)或[绑定第三方证书](app-service-web-tutorial-custom-ssl.md)。
    - **ARR 相关性**：在多实例部署中，请确保在会话的整个生存期内，将客户端路由到同一实例。 对于无状态应用程序，请将此选项设置为“关闭”。
- **调试**：启用[ASP.NET](troubleshoot-dotnet-visual-studio.md#remotedebug)、 [ASP.NET Core](/visualstudio/debugger/remote-debugging-azure)或 [node.js](containers/configure-language-nodejs.md#debug-remotely) 应用的远程调试。 此选项在 48 小时后会自动关闭。
- **传入的客户端证书**：要求在[相互身份验证](app-service-web-configure-tls-mutual-auth.md)中使用客户端证书。

## <a name="configure-default-documents"></a>配置默认文档

此设置仅适用于 Windows 应用。

在 [Azure 门户]中，导航到应用的管理页。 在应用的左侧菜单中，单击“配置” > “默认文档”。

![常规设置](./media/configure-common/open-documents.png)

默认文档是在网站的根 URL 中显示的网页。 使用的是列表中的第一个匹配文件。 若要添加新的默认文档，请单击“新建文档”。 别忘了单击“保存”。

如果应用使用的模块基于 URL 进行路由而不是提供静态内容，则无需使用默认文档。

## <a name="configure-path-mappings"></a>配置路径映射

在 [Azure 门户]中，导航到应用的管理页。 在应用的左侧菜单中，单击“配置” > “路径映射”。

![常规设置](./media/configure-common/open-path.png)

“路径映射”页根据 OS 类型显示不同的内容。

### <a name="windows-apps-uncontainerized"></a>Windows 应用（未容器化）

对于 Windows 应用，可以自定义 IIS 处理程序映射和虚拟应用程序与目录。

使用处理程序映射可以添加自定义脚本处理程序用于处理特定文件扩展名的请求。 若要添加自定义处理程序，请单击“新建处理程序”。 按如下所述配置处理程序：

- **扩展名**。 要处理的扩展名，例如 *\*.php* 或 *handler.fcgi*。
- **脚本处理程序**。 脚本处理程序的绝对路径。 与文件扩展名匹配的文件请求由脚本处理程序处理。 使用路径 `D:\home\site\wwwroot` 表示应用的根目录。
- **参数**。 脚本处理程序的可选命令行参数

每个应用具有已映射到 `D:\home\site\wwwroot`（代码的默认部署位置）的默认根路径 (`/`)。 如果应用根位于其他文件夹中，或者存储库包含多个应用程序，则你可以在此处编辑或添加虚拟应用程序和目录。 单击“新建虚拟应用程序或目录”。

若要配置虚拟应用程序和目录，请指定每个虚拟目录及其相对于网站根目录 (`D:\home`) 的物理路径。 还可选中“应用程序”复选框，将虚拟目录标记为应用程序。

### <a name="containerized-apps"></a>容器化应用

你可以[为容器化应用添加自定义存储](containers/how-to-serve-content-from-azure-storage.md)。 容器化应用包括所有 Linux 应用, 还包括在应用服务上运行的 Windows 和 Linux 自定义容器。 单击 "**新建 Azure 存储**", 并按如下所示配置自定义存储:

- **名称**：显示名称。
- **配置选项**:**基本**或**高级**。
- **存储帐户**：包含所需容器的存储帐户。
- **存储类型**：**Azure blob**或**azure 文件**。
  > [!NOTE]
  > Windows 容器应用仅支持 Azure 文件。
- **存储容器**：对于基本配置, 为所需的容器。
- **共享名**:对于高级配置, 为文件共享名称。
- **访问密钥**:对于高级配置, 为 "访问密钥"。
- **装载路径**:容器中用于装载自定义存储的绝对路径。

有关详细信息, 请参阅在[Linux 上的应用服务中提供 Azure 存储中的内容](containers/how-to-serve-content-from-azure-storage.md)。

## <a name="configure-language-stack-settings"></a>配置语言堆栈设置

对于 Linux 应用, 请参阅:

- [ASP.NET Core](containers/configure-language-dotnetcore.md)
- [Node.js](containers/configure-language-nodejs.md)
- [PHP](containers/configure-language-php.md)
- [Python](containers/how-to-configure-python.md)
- [Java](containers/configure-language-java.md)
- [Ruby](containers/configure-language-ruby.md)

## <a name="configure-custom-containers"></a>配置自定义容器

请参阅[为 Azure App Service 配置自定义 Linux 容器](containers/configure-custom-container.md)

## <a name="next-steps"></a>后续步骤

- [在 Azure 应用服务中配置自定义域名]
- [设置过渡环境，在 Azure 应用服务]
- [为 Azure 应用服务中的应用启用 HTTPS]
- [启用诊断日志](troubleshoot-diagnostic-logs.md)
- [在 Azure 应用服务中缩放应用]
- [在 Azure 应用服务中监视基础知识]
- [使用 applicationHost.xdt 更改 applicationHost.config 设置](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[ASP.NET SignalR]: https://www.asp.net/signalr
[Azure 门户]: https://portal.azure.com/
[在 Azure 应用服务中配置自定义域名]: ./app-service-web-tutorial-custom-domain.md
[设置过渡环境，在 Azure 应用服务]: ./deploy-staging-slots.md
[为 Azure 应用服务中的应用启用 HTTPS]: ./app-service-web-tutorial-custom-ssl.md
[How to: Monitor web endpoint status]: https://go.microsoft.com/fwLink/?LinkID=279906
[在 Azure 应用服务中监视基础知识]: ./web-sites-monitor.md
[管道模式]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[在 Azure 应用服务中缩放应用]: ./manage-scale-up.md
