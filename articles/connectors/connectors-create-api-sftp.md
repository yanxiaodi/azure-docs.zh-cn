---
title: 连接到 SFTP 帐户-Azure 逻辑应用
description: 使用 Azure 逻辑应用通过 SSH 自动完成用于监视、创建、管理、发送和接收 SFTP 服务器文件的任务与流程
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, klam, LADocs
ms.topic: article
ms.date: 06/18/2019
tags: connectors
ms.openlocfilehash: 5b80339e90947b7cc45b1edb5df00652a13291ac
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67293479"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用监视、创建和管理 SFTP 文件

若要自动完成用于在[安全文件传输协议 (SFTP)](https://www.ssh.com/ssh/sftp/) 服务器上监视、创建、发送和接收文件的任务，可以使用 Azure 逻辑应用和 SFTP 连接器来生成并自动完成集成工作流。 SFTP 是通过任何可靠数据流提供文件访问、文件传输和文件管理的网络协议。 下面是可以自动完成的一些示例任务：

* 添加或更改文件时进行监视。
* 获取、创建、复制、更新、列出和删除文件。
* 获取文件内容和元数据。
* 将存档提取到文件夹。

可以使用触发器来监视 SFTP 服务器上的事件，并使输出可用于其他操作。 可以使用操作针对 SFTP 服务器执行各种任务。 还可以让逻辑应用中的其他操作使用 SFTP 操作的输出。 例如，如果你定期从 SFTP 服务器检索文件，则可以使用 Office 365 Outlook 连接器或 Outlook.com 连接器发送有关这些文件及其内容的电子邮件警报。 如果你不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)

## <a name="limits"></a>限制

SFTP 连接器会处理的文件*50 MB 或更小*，并且不支持[消息分块](../logic-apps/logic-apps-handle-large-messages.md)。 对于较大的文件，请使用[SFTP-SSH 连接器](../connectors/connectors-sftp-ssh.md)。 SFTP 连接器和 SFTP-SSH 连接器之间的差异，有关查看[比较 SFTP-SSH 和 SFTP](../connectors/connectors-sftp-ssh.md#comparison) SFTP-SSH 文章中。

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* SFTP 服务器地址和帐户凭据，可让逻辑应用访问 SFTP 帐户。 若要使用[安全外壳 (SSH)](https://www.ssh.com/ssh/protocol/) 协议，还需要有权访问 SSH 私钥和 SSH 私钥密码。

  > [!NOTE]
  >
  > SFTP 连接器支持以下私钥格式：OpenSSH、ssh.com 和 PuTTY
  >
  > 创建逻辑应用时，在添加所需的 SFTP 触发器或操作之后，需要提供 SFTP 服务器的连接信息。 
  > 如果使用 SSH 私钥，请确保从 SSH 私钥文件中复制密钥，并将其粘贴到连接详细信息。不要手动输入或编辑密钥，否则可能导致连接失败。 
  > 有关详细信息，请参阅本文中的后续步骤。

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

* 要在其中访问 SFTP 帐户的逻辑应用。 若要从 SFTP 触发器开始，请[创建一个空白逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用 SFTP 操作，请使用另一个触发器（例如“重复”触发器）启动逻辑应用。 

## <a name="how-sftp-triggers-work"></a>SFTP 触发工作的方式

SFTP 触发器通过轮询 SFTP 文件系统并寻找自上次轮询后已更改的任何文件的工作。 某些工具允许保留文件更改时的时间戳。 在这种情况下，必须禁用此功能才能让触发器正常工作。 下面是一些常见设置：

| SFTP 客户端 | 操作 |
|-------------|--------|
| Winscp | 转到“选项” > “首选项” > “传输” > “编辑” > “保留时间戳” > “禁用”       |
| FileZilla | 转到“传输” > “保留已传输文件的时间戳” > “禁用”    |
|||

当触发器找到新文件时，会检查该新文件是否完整，以及是否未部分写入。 例如，当触发器检查文件服务器时，可能正在更改某个文件。 为了避免返回部分写入的文件，该触发器会记录具有最近更改的文件的时间戳，但不会立即返回该文件。 仅当再次轮询服务器时，触发器才会返回该文件。 有时，此行为可能会导致延迟，长达触发器轮询间隔的两倍。

## <a name="connect-to-sftp"></a>连接到 SFTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登录到 [Azure 门户](https://portal.azure.com)，在逻辑应用设计器中打开逻辑应用（如果尚未打开）。

1. 对于空白逻辑应用，请在搜索框中输入“sftp”作为筛选器。 在触发器列表下，选择所需的触发器。

   -或-

   对于现有逻辑应用，请在要添加操作的最后一个步骤下，选择“新建步骤”  。 在搜索框中，输入“sftp”作为筛选器。 在操作列表下，选择所需的操作。

   若要在步骤之间添加操作，请将鼠标指针移到步骤之间的箭头上。 选择出现的加号 ( **+** )，然后选择“添加操作”。 

1. 为连接提供所需的详细信息。

   > [!IMPORTANT]
   >
   > 在“SSH 私钥”属性中输入 SSH 私钥时，请遵循以下附加步骤，帮助确保提供此属性的完整正确值。  
   > 无效的密钥会导致连接失败。

   可以使用任何文本编辑器。以下步骤以 Notepad.exe 为例，说明如何正确复制并粘贴密钥。

   1. 在文本编辑器中打开 SSH 私钥文件。 这些步骤以记事本为例。

   1. 在记事本**编辑**菜单中，选择**全**。

   1. 选择“编辑”   >   “复制”。

   1. 在添加的 SFTP 触发器或操作中，粘贴已复制到“SSH 私钥”属性中的完整密钥，支持换行。   请务必粘贴该密钥， 而不要手动输入或编辑密钥。

1. 输入完连接详细信息后，选择“创建”。 

1. 为所选触发器或操作提供所需的详细信息，然后继续生成逻辑应用的工作流。

## <a name="examples"></a>示例

<a name="file-add-modified"></a>

### <a name="sftp-trigger-when-a-file-is-added-or-modified"></a>SFTP 触发器：添加或修改文件时

在 SFTP 服务器上添加或更改文件时，此触发器将启动逻辑应用工作流。 例如，可以添加一个条件，用于检查文件内容，并根据该内容是否符合指定的条件来获取内容。 然后可以添加一个操作，用于获取文件内容并将其放在 SFTP 服务器上的某个文件夹中。

**企业示例**：可以使用此触发器监视 SFTP 文件夹中表示客户订单的新文件。 然后，可以使用“获取文件内容”等 SFTP 操作来获取订单内容以做进一步处理，并将该订单存储在订单数据库中。 

<a name="get-content"></a>

### <a name="sftp-action-get-content"></a>SFTP 操作：获取内容

此操作从 SFTP 服务器上的文件中获取内容。 例如，可以在前面的示例中添加触发器，并添加文件内容必须符合的条件。 如果条件为 true，则可以运行获取内容的操作。

## <a name="connector-reference"></a>连接器参考

有关触发器、操作和限制（请参阅连接器的 OpenAPI（以前称为 Swagger）说明）的技术详细信息，请查看连接器的[参考页](/connectors/sftpconnector/)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)
