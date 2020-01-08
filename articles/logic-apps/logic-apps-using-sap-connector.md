---
title: 连接到 SAP 系统 - Azure 逻辑应用
description: 使用 Azure 逻辑应用将工作流自动化，以访问和管理 SAP 资源
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, LADocs
ms.topic: article
ms.date: 08/30/2019
tags: connectors
ms.openlocfilehash: 98e6b515d5e9d60f95873016ad1cb06a13799bb2
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390121"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>从 Azure 逻辑应用连接到 SAP 系统

> [!IMPORTANT]
> 早于2019年11月30日的 SAP 应用程序服务器和 SAP 消息服务器连接器已计划弃用。 当前 SAP 连接器合并了这些以前的 SAP 连接器，因此你无需更改连接类型，与以前的连接器完全兼容，提供了许多附加功能，并继续使用 SAP .Net 连接器库（SAP NCo）。
>
> 对于使用较旧连接器的逻辑应用，请在弃用日期之前[迁移到最新的连接器](#migrate)。 否则，这些逻辑应用将会遇到执行失败，并且无法将消息发送到 SAP 系统。

本文介绍如何使用 SAP 连接器从逻辑应用中访问本地 SAP 资源。 连接器适用于 SAP 的经典版本，如本地的 R/3 和 ECC 系统。 此连接器还可与 SAP 更新的基于 HANA 的 SAP 系统（例如 S/4 HANA）集成，无论它们是在本地还是在云中托管。 SAP 连接器支持通过中间文档（IDoc）、业务应用程序编程接口（BAPI）或远程函数调用（RFC）与基于 SAP NetWeaver 的系统的消息或数据集成。

SAP 连接器使用 [SAP .NET 连接器 (NCo) 库](https://support.sap.com/en/product/connectors/msnet.html)，并提供以下操作：

* **发送到 SAP**：在 SAP 系统中通过 tRFC 发送 IDoc、通过 RFC 调用 BAPI 函数，或调用 RFC/tRFC。
* **从 SAP 接收**：在 SAP 系统中通过 tRFC 接收 IDoc、通过 tRFC 调用 BAPI 函数，或调用 RFC/tRFC。
* **生成架构**：为 IDoc、BAPI 或 RFC 生成 SAP 项目的架构。

对于这些操作，SAP 连接器支持通过用户名和密码进行基本身份验证。 该连接器还支持[安全网络通信 (SNC)](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true)。 SNC 可用于 SAP NetWeaver 单一登录 (SSO) 或外部安全产品提供的其他安全功能。

SAP 连接器通过[本地数据网关](../logic-apps/logic-apps-gateway-connection.md)与本地 SAP 系统集成。 例如，在“发送”方案中，如果将消息从逻辑应用发送到 SAP 系统，则数据网关将充当 RFC 客户端，将从逻辑应用收到的请求转发到 SAP。 同理，在“接收”方案中，数据网关充当 RFC 服务器，从 SAP 接收请求并将其转发到逻辑应用。

本文介绍如何创建与 SAP 集成的示例逻辑应用，并阐述以前已介绍过的集成方案。 对于使用较旧 SAP 连接器的逻辑应用，本文介绍了如何将逻辑应用迁移到最新的 SAP 连接器。

<a name="pre-reqs"></a>

## <a name="prerequisites"></a>先决条件

若要遵循本文中的步骤，需准备好以下各项：

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 要从中访问 SAP 系统的逻辑应用，以及用于启动逻辑应用工作流的触发器。 如果你不熟悉逻辑应用，请参阅[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)和[快速入门：创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

* [SAP 应用程序服务器](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server)或 [SAP 消息服务器](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm)。

* 在任何本地计算机上下载并安装最新的[本地数据网关](https://www.microsoft.com/download/details.aspx?id=53127)。 在继续操作之前，请确保在 Azure 门户中设置网关。 网关有助于安全访问本地数据和资源。 有关详细信息，请参阅[为 Azure 逻辑应用安装本地数据网关](../logic-apps/logic-apps-gateway-install.md)。

* 如果将 SNC 用于 SSO，请确保以映射到 SAP 用户的用户身份运行网关。 若要更改默认帐户，请选择“更改帐户”并输入用户凭据。

  ![更改网关帐户](./media/logic-apps-using-sap-connector/gateway-account.png)

* 如果对外部安全产品启用 SNC，请在安装网关的同一台计算机上复制 SNC 库或文件。 SNC 产品的部分示例包括 [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm)、Kerberos 和 NTLM。

* 在本地数据网关所在的计算机上下载并安装最新的 SAP 客户端库，该库当前是[使用 .NET Framework 4.0（Windows 64 位 (x64)）编译的用于 Microsoft .NET 3.0.22.0 的 SAP 连接器 (NCo 3.0)](https://softwaredownloads.sap.com/file/0020000001000932019)。 安装此版本或更高版本的原因是：

  * 如果同时发送多个 IDoc 消息，早期的 SAP NCo 版本可能死锁。 这种状态会阻止向 SAP 目标发送所有后续消息，从而导致消息超时。
  
  * 本地数据网关只能在 64 位系统上运行。 否则，会收到“错误的映像”错误，因为数据网关主机服务不支持 32 位程序集。
  
  * 数据网关主机服务和 Microsoft SAP 适配器使用 .NET Framework 4.5。 SAP NCo for .NET Framework 4.0 适用于使用 .NET 运行时 4.0 到 4.7.1 的进程。 SAP NCo for .NET Framework 2.0 适用于使用 .NET 运行时 2.0 到 3.5 的进程，但不再适用于最新的本地数据网关。

* 可以发送到 SAP 服务器的消息内容（例如示例 IDoc 文件）必须采用 XML 格式，并且包含要使用的 SAP 操作的命名空间。

<a name="migrate"></a>

## <a name="migrate-to-current-connector"></a>迁移到当前连接器

1. 如果尚未这样做，请更新[本地数据网关](https://www.microsoft.com/download/details.aspx?id=53127)，以获取最新版本。 有关详细信息，请参阅[为 Azure 逻辑应用安装本地数据网关](../logic-apps/logic-apps-gateway-install.md)。

1. 在使用较旧 SAP 连接器的逻辑应用中，删除 "**发送到 SAP** " 操作。

1. 从最新的 SAP 连接器添加 "**发送到 SAP** " 操作。 使用此操作之前，请重新创建与 SAP 系统的连接。

1. 完成后，保存逻辑应用。

<a name="add-trigger"></a>

## <a name="send-to-sap"></a>发送到 SAP

本示例使用可通过 HTTP 请求触发的逻辑应用。 逻辑应用将 IDoc 发送到 SAP 服务器，并将响应返回给调用逻辑应用的请求者。

### <a name="add-an-http-request-trigger"></a>添加 HTTP 请求触发器

在 Azure 逻辑应用中，每个逻辑应用都必须从[触发器](../logic-apps/logic-apps-overview.md#logic-app-concepts)开始，该触发器在发生特定事件或特定条件得到满足的情况下触发。 每当触发器触发时，逻辑应用引擎就会创建一个逻辑应用实例并开始运行应用的工作流。

本示例创建一个包含 Azure 中的终结点的逻辑应用，以便可将 *HTTP POST 请求*发送到逻辑应用。 当逻辑应用程序收到这些 HTTP 请求时，触发器将会触发，并运行工作流中的下一个步骤。

1. 在 [Azure 门户](https://portal.azure.com)中创建一个空白的逻辑应用。此时会打开逻辑应用设计器。

1. 在搜索框中，输入“http 请求”作为筛选器。 从 "**触发器**" 列表中，选择**接收 HTTP 请求的时间**。

   ![添加 HTTP 请求触发器](./media/logic-apps-using-sap-connector/add-trigger.png)

1. 现在，保存逻辑应用，以便可以为逻辑应用生成终结点 URL。 在设计器工具栏上，选择“保存”。

   终结点 URL 现在会显示在触发器中，例如：

   ![生成终结点的 URL](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

### <a name="add-an-sap-action"></a>添加 SAP 操作

在 Azure 逻辑应用中，[操作](../logic-apps/logic-apps-overview.md#logic-app-concepts)是指工作流中紧跟在某个触发器或另一操作后面执行的一个步骤。 如果尚未将触发器添加到逻辑应用并想要遵循本示例，请[添加此部分所述的触发器](#add-trigger)。

1. 在逻辑应用设计器中的触发器下，选择“新建步骤”。

   ![选择“新建步骤”](./media/logic-apps-using-sap-connector/add-action.png)

1. 在搜索框中，输入“sap”作为筛选器。 在“操作”列表中选择“将消息发送到 SAP”。
  
   ![选择 SAP 发送操作](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   如果不执行搜索，可以选择“企业”选项卡，然后选择 SAP 操作。

   ![在“企业”选项卡中选择 SAP 发送操作](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. 如果系统提示输入连接详细信息，请立即创建 SAP 连接。 否则，如果连接已存在，请继续执行下一步，以便可以设置 SAP 操作。

   **创建本地 SAP 连接**

   提供 SAP 服务器的连接信息。 对于“数据网关”属性，请选择在 Azure 门户中为网关安装创建的数据网关。 完成后，选择“创建”。 逻辑应用会设置并测试连接，确保连接正常工作。

   * 如果“登录类型”属性设置为“应用程序服务器”，则必须指定以下属性（通常显示为可选）：

     ![创建 SAP 应用程序服务器连接](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

   * 如果“登录类型”属性设置为“组”，则必须指定以下属性（通常显示为可选）：

     ![创建 SAP 消息服务器连接](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

   默认会使用强类型化通过针对架构执行 XML 验证来检查无效值。 此行为可帮助提前检测问题。 “安全类型化”选项用于实现后向兼容，它只会检查字符串长度。 了解有关[安全键入选项](#safe-typing)的详细信息。

1. 现在，请找到并选择 SAP 服务器中的某个操作。

   1. 在“SAP 操作”框中，选择文件夹图标。 在文件列表中，找到并选择要使用的 SAP 消息。 使用箭头可在列表中导航。

      此示例选择了类型为“订单”的 IDoc。

      ![找到并选择 IDoc 操作](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      如果找不到所需的操作，可以手动输入路径，例如：

      ![手动提供 IDoc 操作的路径](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > 通过表达式编辑器提供“SAP 操作”的值。 这样，便可以对不同的消息类型使用相同的操作。

      有关 IDoc 操作的详细信息，请参阅 [IDOC 操作的消息架构](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)。

   1. 在“输入消息”框中单击，以显示动态内容列表。 在该列表中的“收到 HTTP 请求时”下面，选择“正文”字段。

      此步骤包括 HTTP 请求触发器的正文内容，并将输出发送到 SAP 服务器。

      ![选择“正文”字段](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      完成后，SAP 操作如以下示例所示：

      ![完成 SAP 操作](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. 保存逻辑应用。 在设计器工具栏上，选择“保存”。

<a name="add-response"></a>

### <a name="add-an-http-response-action"></a>添加 HTTP 响应操作

现在，请将响应操作添加到逻辑应用的工作流，并包含来自 SAP 操作的输出。 这样，逻辑应用便可将来自 SAP 服务器的结果返回到原始请求方。

1. 在逻辑应用设计器中的 SAP 操作下，选择“新建步骤”。

1. 在搜索框中，输入“响应”作为筛选器。 在“操作”列表中选择“响应”。

1. 在“正文”框中单击，以显示动态内容列表。 在该列表中的“将消息发送到 SAP”下面，选择“正文”字段。

   ![完成 SAP 操作](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. 保存逻辑应用。

### <a name="test-your-logic-app"></a>测试逻辑应用

1. 如果尚未启用逻辑应用，请在逻辑应用菜单中选择“概览”。 在工具栏中选择“启用”。

1. 在设计器工具栏上选择“运行”。 此步骤会手动启动逻辑应用。

1. 通过将 HTTP POST 请求发送到 HTTP 请求触发器中的 URL 来触发逻辑应用。
在请求中包括消息内容。 若要发送请求，可以使用 [Postman](https://www.getpostman.com/apps) 等工具。

   在本文中，请求会发送 IDoc 文件，该文件必须采用 XML 格式，并且包含所用 SAP 操作的命名空间，例如：

   ``` xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

1. 发送 HTTP 请求之后，等待逻辑应用返回响应。

   > [!NOTE]
   > 如果未在[请求超时限制](./logic-apps-limits-and-config.md)所规定的时间内完成响应所需的所有步骤，逻辑应用可能会超时。 如果发生这种情况，请求可能会被阻止。 为帮助诊断问题，请了解如何[检查和监视逻辑应用](../logic-apps/logic-apps-monitor-your-logic-apps.md)。

现已创建一个可与 SAP 服务器通信的逻辑应用。 为逻辑应用设置 SAP 连接后，可以探索其他可用的 SAP 操作，例如 BAPI 和 RFC。

## <a name="receive-from-sap"></a>从 SAP 接收

此示例使用当应用从 SAP 系统收到消息时触发的逻辑应用。

### <a name="add-an-sap-trigger"></a>添加 SAP 触发器

1. 在 Azure 门户中创建一个空白的逻辑应用，以便打开逻辑应用设计器。

1. 在搜索框中，输入“sap”作为筛选器。 在“触发器”列表中选择“从 SAP 收到消息时”。

   ![添加 SAP 触发器](./media/logic-apps-using-sap-connector/add-sap-trigger.png)

   或者，可以转到“企业”选项卡并选择触发器：

   ![在“企业”选项卡中添加 SAP 触发器](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. 如果系统提示输入连接详细信息，请立即创建 SAP 连接。 如果连接已存在，请继续下一步，以便可以设置 SAP 操作。

   **创建本地 SAP 连接**

   提供 SAP 服务器的连接信息。 对于“数据网关”属性，请选择在 Azure 门户中为网关安装创建的数据网关。 完成后，选择“创建”。 逻辑应用会设置并测试连接，确保连接正常工作。

   * 如果“登录类型”属性设置为“应用程序服务器”，则必须指定以下属性（通常显示为可选）：

     ![创建 SAP 应用程序服务器连接](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

   * 如果“登录类型”属性设置为“组”，则必须指定以下属性（通常显示为可选）：

     ![创建 SAP 消息服务器连接](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

   默认会使用强类型化通过针对架构执行 XML 验证来检查无效值。 此行为可帮助提前检测问题。 “安全类型化”选项用于实现后向兼容，它只会检查字符串长度。 了解有关[安全键入选项](#safe-typing)的详细信息。

1. 根据 SAP 系统配置提供所需的参数。

   可以选择性地提供一个或多个 SAP 操作。 此操作列表指定触发器通过数据网关从 SAP 服务器接收的消息。 空列表指定触发器接收所有消息。 如果列表中不包含多条消息，则触发器只会接收列表中指定的消息。 网关会拒绝从 SAP 服务器发送的其他任何消息。

   可以通过文件选取器选择 SAP 操作：

   ![选择 SAP 操作](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   或者，可以手动指定操作：

   ![手动输入 SAP 操作](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png)

   以下示例演示将触发器设置为接收多个消息时操作的显示方式。

   ![触发器示例](media/logic-apps-using-sap-connector/example-trigger.png)  

   有关 SAP 操作的详细信息，请参阅 [IDOC 操作的消息架构](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

1. 现在请保存逻辑应用，以便可以开始从 SAP 系统接收消息。
在设计器工具栏上，选择“保存”。

现在，逻辑应用已准备好从 SAP 系统接收消息。

> [!NOTE]
> SAP 触发器不是轮询触发器，而是基于 Webhook 的触发器。 仅当存在消息时，才会从网关调用触发器，因此无需轮询。

### <a name="test-your-logic-app"></a>测试逻辑应用

1. 若要触发逻辑应用，请从 SAP 系统发送一条消息。

1. 在逻辑应用菜单中，选择“概述”。 查看逻辑应用的所有新运行的“运行历史记录”。

1. 打开最近的运行，触发器输出部分会显示从 SAP 系统发送的消息。

## <a name="receive-idoc-packets-from-sap"></a>接收来自 SAP 的 IDOC 数据包

你可以设置 SAP 以[在数据包中发送 idoc](https://help.sap.com/viewer/8f3819b0c24149b5959ab31070b64058/7.4.16/en-US/4ab38886549a6d8ce10000000a42189c.html)，这些数据包是 idoc 的批处理或组。 若要接收 IDOC 数据包，SAP 连接器，特别是触发器，无需额外配置。 但是，若要在触发器收到数据包后处理 IDOC 数据包中的每个项，则需要执行一些额外的步骤来将数据包拆分为单个 Idoc。

下面是一个示例，演示如何使用[ `xpath()`函数](./workflow-definition-language-functions-reference.md#xpath)从包中提取单个 idoc：

1. 在开始之前，需要一个具有 SAP 触发器的逻辑应用。 如果还没有此逻辑应用，请按照本主题中前面的步骤[使用 SAP 触发器设置逻辑应用](#receive-from-sap)。

   例如：

   ![SAP 触发器](./media/logic-apps-using-sap-connector/first-step-trigger.png)

1. 从你的逻辑应用从 SAP 接收的 XML IDOC 获取根命名空间。 若要从 XML 文档中提取此命名空间，请添加一个步骤，该步骤创建一个本地字符串变量，并`xpath()`使用表达式存储该命名空间：

   `xpath(xml(triggerBody()?['Content']), 'namespace-uri(/*)')`

   ![获取命名空间](./media/logic-apps-using-sap-connector/get-namespace.png)

1. 若要提取单个 IDOC，请添加一个步骤，该步骤创建一个数组变量，并使用另一个`xpath()`表达式存储 IDOC 集合：

   `xpath(xml(triggerBody()?['Content']), '/*[local-name()="Receive"]/*[local-name()="idocData"]')`

   ![获取项的数组](./media/logic-apps-using-sap-connector/get-array.png)

   数组变量使每个 IDOC 可供逻辑应用通过枚举集合来单独处理。 在此示例中，逻辑应用使用循环将每个 IDOC 传输到 SFTP 服务器：

   ![发送 IDOC](./media/logic-apps-using-sap-connector/loop-batch.png)

   每个 IDOC 都必须包含根命名空间，这是在这种情况下，在将`<Receive></Receive` IDOC 发送到下游应用或 SFTP 服务器之前，文件内容与根命名空间一起包装在元素内的原因。

> [!TIP]
> 创建新的逻辑应用时，可以通过在逻辑应用设计器中选择此模板来使用此模式的快速入门模板。
>
> ![批处理模板](./media/logic-apps-using-sap-connector/batch-template.png)

## <a name="generate-schemas-for-artifacts-in-sap"></a>为 SAP 中的项目生成架构

本示例使用可通过 HTTP 请求触发的逻辑应用。 SAP 操作向 SAP 系统发送一个请求，以生成指定的 IDoc 和 BAPI 的架构。 使用 Azure 资源管理器连接器将在响应中返回的架构上传到集成帐户。

### <a name="add-an-http-request-trigger"></a>添加 HTTP 请求触发器

1. 在 Azure 门户中创建一个空白的逻辑应用，以便打开逻辑应用设计器。

1. 在搜索框中，输入“http 请求”作为筛选器。 从 "**触发器**" 列表中，选择**接收 HTTP 请求的时间**。

   ![添加 HTTP 请求触发器](./media/logic-apps-using-sap-connector/add-trigger.png)

1. 现在请保存逻辑应用，以便可为逻辑应用生成终结点 URL。
在设计器工具栏上，选择“保存”。

   终结点 URL 现在会显示在触发器中，例如：

   ![生成终结点的 URL](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-an-sap-action-to-generate-schemas"></a>添加 SAP 操作以生成架构

1. 在逻辑应用设计器中的触发器下，选择“新建步骤”。

   ![选择“新建步骤”](./media/logic-apps-using-sap-connector/add-action.png)

1. 在搜索框中，输入“sap”作为筛选器。 在“操作”列表中选择“生成架构”。
  
   ![选择 SAP 发送操作](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   或者，也可以选择“企业”选项卡，然后选择 SAP 操作。

   ![在“企业”选项卡中选择 SAP 发送操作](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. 如果系统提示输入连接详细信息，请立即创建 SAP 连接。 如果连接已存在，请继续下一步，以便可以设置 SAP 操作。

   **创建本地 SAP 连接**

   1. 提供 SAP 服务器的连接信息。 对于“数据网关”属性，请选择在 Azure 门户中为网关安装创建的数据网关。

      * 如果“登录类型”属性设置为“应用程序服务器”，则必须指定以下属性（通常显示为可选）：

        ![创建 SAP 应用程序服务器连接](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * 如果“登录类型”属性设置为“组”，则必须指定以下属性（通常显示为可选）：

        ![创建 SAP 消息服务器连接](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      默认会使用强类型化通过针对架构执行 XML 验证来检查无效值。 此行为可帮助提前检测问题。 “安全类型化”选项用于实现后向兼容，它只会检查字符串长度。 了解有关[安全键入选项](#safe-typing)的详细信息。

   1. 完成后，选择“创建”。

      逻辑应用会设置并测试连接，确保连接正常工作。

1. 提供要为其生成架构的项目的路径。

   可以通过文件选取器选择 SAP 操作：

   ![选择 SAP 操作](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   或者，可以手动输入操作：

   ![手动输入 SAP 操作](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png)

   若要为多个项目生成架构，请提供每个项目的 SAP 操作详细信息，例如：

   ![选择“添加新项”](media/logic-apps-using-sap-connector/schema-generator-array-pick.png)

   ![显示两个项](media/logic-apps-using-sap-connector/schema-generator-example.png)

   有关 SAP 操作的详细信息，请参阅[IDOC 操作的消息架构](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)。

1. 保存逻辑应用。 在设计器工具栏上，选择“保存”。

### <a name="test-your-logic-app"></a>测试逻辑应用

1. 在设计器工具栏上，选择“运行”以触发逻辑应用的运行。

1. 打开该运行，并检查“生成架构”操作的输出。

   输出中会显示针对指定的消息列表生成的架构。

### <a name="upload-schemas-to-an-integration-account"></a>将架构上传到集成帐户

（可选）可以下载生成的架构，或将其存储在 Blob、存储或集成帐户等存储库中。 集成帐户为其他 XML 操作提供一流的体验。本示例演示如何使用 Azure 资源管理器连接器将架构上传到同一逻辑应用的集成帐户。

1. 在逻辑应用设计器中的触发器下，选择“新建步骤”。

1. 在搜索框中，输入“资源管理器”作为筛选器。 选择“创建或更新资源”。

   ![选择 Azure 资源管理器操作](media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. 输入操作的详细信息，包括 Azure 订阅、Azure 资源组和集成帐户。 若要将 SAP 令牌添加到字段，请在这些字段对应的框中单击，然后从显示的动态内容列表中选择。

   1. 打开“添加新参数”列表，然后选择“位置”和“属性”字段。

   1. 按此示例所示，提供这些新字段的详细信息。

      ![输入 Azure 资源管理器操作的详细信息](media/logic-apps-using-sap-connector/azure-resource-manager-action.png)

   SAP 的“生成架构”操作会生成集合形式的架构，因此，设计器会自动将一个 **For each** 循环添加到该操作。 以下示例演示此操作的显示方式：

   ![包含“for each”循环的 Azure 资源管理器操作](media/logic-apps-using-sap-connector/azure-resource-manager-action-foreach.png)  
   > [!NOTE]
   > 架构使用 base64 编码格式。 若要将架构上传到集成帐户，必须使用 `base64ToString()` 函数将其解码。 以下示例演示了 `"properties"` 元素的代码：
   >
   > ```json
   > "properties": {
   >    "Content": "@base64ToString(items('For_each')?['Content'])",
   >    "ContentType": "application/xml",
   >    "SchemaType": "Xml"
   > }
   > ```

1. 保存逻辑应用。 在设计器工具栏上，选择“保存”。

### <a name="test-your-logic-app"></a>测试逻辑应用

1. 在设计器工具栏上，选择“运行”以手动触发逻辑应用。

1. 成功运行后，转到集成帐户，并检查生成的架构是否存在。

## <a name="enable-secure-network-communications"></a>启用安全网络通信

在开始之前，请确保符合先前列出的[先决条件](#pre-reqs)：

* 本地数据网关已安装在 SAP 系统所在的位于同一网络中的某台计算机上。
* 为实现 SSO，需以映射到 SAP 用户的用户身份运行网关。
* 提供其他安全功能的 SNC 库已安装在数据网关所在的同一台计算机上。 部分示例包括 [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm)、Kerberos 和 NTLM。

   若要对传入和传出 SAP 系统的请求启用 SNC，请在 SAP 连接中选中“使用 SNC”复选框，并提供以下属性：

   ![在连接中配置 SAP SNC](media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | 属性 | 描述 |
   |----------| ------------|
   | **SNC 库路径** | SNC 库的名称、相对于 NCo 安装位置的路径或绝对路径。 例如 `sapsnc.dll`、`.\security\sapsnc.dll` 或 `c:\security\sapsnc.dll`。 |
   | **SNC SSO** | 通过 SNC 进行连接时，SNC 标识通常用于对调用方进行身份验证。 另一个选项是重写，以便可以使用用户和密码信息对调用方进行身份验证，但该行仍会加密。 |
   | **我的 SNC 名称** | 在大多数情况下可以省略此属性。 安装的 SNC 解决方案通常知道自己的 SNC 名称。 只有对于支持“多个标识”的解决方案，才需要指定用于此特定目标或服务器的标识。 |
   | **SNC 合作伙伴名称** | 后端 SNC 的名称。 |
   | **SNC 保护质量** | 此特定目标或服务器的 SNC 通信使用的服务质量。 默认值由后端系统定义。 最大值由 SNC 使用的安全产品定义。 |
   |||

   > [!NOTE]
   > 不要在装有数据网关和 SNC 库的计算机上设置 SNC_LIB 和 SNC_LIB_64 环境变量。 如果已设置这些环境变量，它们会优先于通过连接器传递的 SNC 库值。

<a name="safe-typing"></a>

## <a name="safe-typing"></a>安全类型化

创建 SAP 连接时，默认会使用强类型化通过针对架构执行 XML 验证来检查无效值。 此行为可帮助提前检测问题。 “安全类型化”选项用于实现后向兼容，它只会检查字符串长度。 如果选择“安全类型化”，则 SAP 中的 DATS 类型和 TIMS 类型将被视为字符串而不是其 XML 等效形式 `xs:date` 和 `xs:time`，其中 `xmlns:xs="http://www.w3.org/2001/XMLSchema"`。 安全类型化会影响所有架构生成操作、“被发送”有效负载和“被接收”响应的发送消息，以及触发器的行为。 

使用强类型化时（未启用**安全类型化**），架构会将 DATS 和 TIMS 类型映射到更简单的 XML 类型：

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

使用强类型化发送消息时，DATS 和 TIMS 响应符合匹配的 XML 类型格式：

```xml
<DATE>9999-12-31</DATE>
<TIME>23:59:59</TIME>
```

启用**安全类型化**时，架构会将 DATS 和 TIMS 类型映射到只施加长度限制的 XML 字符串字段，例如：

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="8" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="6" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
```

在启用**安全类型化**的情况下发送消息时，DATS 和 TIMS 响应如以下示例所示：

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```

## <a name="advanced-scenarios"></a>高级方案

### <a name="confirm-transaction-explicitly"></a>显式确认事务

当你从逻辑应用向 SAP 发送事务时，此交换将按 SAP 文档[事务 RFC Server 程序](https://help.sap.com/doc/saphelp_nwpi71/7.1/en-US/22/042ad7488911d189490000e829fbbd/content.htm?no_cache=true)中所述的两个步骤进行。 默认情况下，"**发送到 SAP** " 操作会同时处理函数传输的步骤和单个调用中的事务确认步骤。 SAP 连接器提供了用于将这些步骤分离的选项。 您可以发送 IDOC，而不是自动确认该事务，您可以使用显式**确认事务 ID**操作。

如果你不想在 SAP 中复制事务（例如，由于网络问题之类的原因，可能会发生故障），则此用于分离事务 ID 确认的功能很有用。 通过单独确认事务 ID，只在 SAP 系统中完成事务一次。

下面是演示此模式的示例：

1. 创建空白逻辑应用并添加 HTTP 触发器。

1. 从 SAP 连接器添加**SEND IDOC**操作。 提供发送到 SAP 系统的 IDOC 的详细信息。

1. 若要在单独的步骤中显式确认事务 ID，请在 "**确认 TID** " 属性中选择 "**否**"。 对于 "可选**事务 ID GUID** " 属性，您可以手动指定值，也可以通过发送 IDOC 操作在响应中自动生成并返回此 GUID。

   ![发送 IDOC 操作属性](./media/logic-apps-using-sap-connector/send-idoc-action-details.png)

1. 若要显式确认事务 ID，请添加 "**确认事务 id** " 操作。 在 "**事务 ID** " 框中单击，以便显示 "动态内容" 列表。 从该列表中，选择从**SEND IDOC**操作返回的**事务 ID**值。

   ![确认事务 ID 操作](./media/logic-apps-using-sap-connector/explicit-transaction-id.png)

   此步骤运行后，当前事务在两个端上标记为 "已完成"，位于 SAP 连接器端和 SAP 系统端。

## <a name="known-issues-and-limitations"></a>已知问题和限制

下面是 SAP 连接器目前存在的已知问题和限制：

* SAP 触发器不支持数据网关群集。 在某些故障转移案例中，与 SAP 系统通信的数据网关节点可能不同于主动节点，从而导致意外的行为。 对于“发送”方案，支持使用数据网关群集。

* SAP 连接器目前不支持 SAP 路由器字符串。 本地数据网关必须位于要连接的 SAP 系统所在的同一 LAN 中。

## <a name="connector-reference"></a>连接器参考

有关触发器、操作和限制（请参阅连接器的 OpenAPI（以前称为 Swagger）说明）的技术详细信息，请查看[连接器的参考页](/connectors/sap/)。

## <a name="next-steps"></a>后续步骤

* 从 Azure 逻辑应用[连接到本地系统](../logic-apps/logic-apps-gateway-connection.md)。
* 了解如何使用[Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)验证、转换和使用其他消息操作。
* 了解其他[逻辑应用连接器](../connectors/apis-list.md)。
