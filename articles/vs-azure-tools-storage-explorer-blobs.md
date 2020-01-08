---
title: 使用存储资源管理器管理 Azure Blob 存储资源 | Microsoft Docs
description: 使用存储资源管理器管理 Azure Blob 容器和 Blob
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: 2f09e545-ec94-4d89-b96c-14783cc9d7a9
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/21/2019
ms.author: cawa
ms.openlocfilehash: 56c20c995a95058b5039b7268c7b7b1426e900fa
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442987"
---
# <a name="manage-azure-blob-storage-resources-with-storage-explorer"></a>使用存储资源管理器管理 Azure Blob 存储资源

## <a name="overview"></a>概述

[Azure Blob 存储](storage/blobs/storage-dotnet-how-to-use-blobs.md)是用于存储大量非结构化数据（例如文本或二进制数据）的服务，这些数据可通过 HTTP 或 HTTPS 从世界各地进行访问。
可以使用 Blob 存储向外公开数据，或者私下存储应用程序数据。 本文介绍如何使用存储资源管理器来处理 Blob 容器和 Blob。

## <a name="prerequisites"></a>必备组件

若要完成本文中的步骤，需要满足以下先决条件：

* [下载并安装存储资源管理器](https://www.storageexplorer.com)
* [连接到 Azure 存储帐户或服务](vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## <a name="create-a-blob-container"></a>创建 Blob 容器

所有 Blob 都必须驻留在 Blob 容器中。简单说来，该容器就是对 Blob 进行逻辑分组。 一个帐户可以包含无限数量的容器，一个容器可以存储无限数量的 Blob。

以下步骤演示了如何在存储资源管理器中创建 Blob 容器。

1. 打开存储资源管理器。
2. 在左窗格中，展开需要在其中创建 Blob 容器的存储帐户。
3. 右键单击“Blob 容器”，并从上下文菜单中选择“创建 Blob 容器”。  

   ![“创建 Blob 容器”上下文菜单][0]
4. 此时会在“Blob 容器”文件夹下显示一个文本框。  输入 Blob 容器的名称。 请参阅[创建容器](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container)有关规则和 blob 容器命名限制的信息。

   ![“创建 Blob 容器”文本框][1]
5. 完成时按 **Enter** 可创建 Blob 容器，按 **Esc** 可取消相关操作。 成功创建 Blob 容器后，该容器会显示在所选存储帐户的“Blob 容器”文件夹下。 

   ![已创建的 Blob 容器][2]

## <a name="view-a-blob-containers-contents"></a>查看 Blob 容器的内容

Blob 容器包含 Blob 和文件夹（其中也可能包含 Blob）。

以下步骤演示了如何在存储资源管理器中查看 Blob 容器的内容：

1. 打开存储资源管理器。
2. 在左窗格中，展开包含想要查看的 Blob 容器的存储帐户。
3. 展开该存储帐户的“Blob 容器”。 
4. 右键单击想要查看的 Blob 容器，并从上下文菜单中选择“打开 Blob 容器编辑器”。 
   也可双击想要查看的 Blob 容器。

   ![“打开 Blob 容器编辑器”上下文菜单][19]
5. 主窗格会显示该 Blob 容器的内容。

   ![Blob 容器编辑器][3]

## <a name="delete-a-blob-container"></a>删除 Blob 容器

可以根据需要轻松地创建和删除 Blob 容器。 （若要了解如何删除各个 Blob，请参阅[管理 Blob 容器中的 Blob](#managing-blobs-in-a-blob-container) 部分。）

以下步骤演示了如何在存储资源管理器中删除 Blob 容器。

1. 打开存储资源管理器。
2. 在左窗格中，展开包含想要查看的 Blob 容器的存储帐户。
3. 展开该存储帐户的“Blob 容器”。 
4. 右键单击想要删除的 Blob 容器，并从上下文菜单中选择“删除”。 
   也可通过按“删除”来删除当前选定的 Blob 容器。 

   ![“删除 Blob 容器”上下文菜单][4]
5. 出现确认对话框时，选择“是”。 

   ![“删除 Blob 容器”确认][5]

## <a name="copy-a-blob-container"></a>复制 Blob 容器

可以通过存储资源管理器将 Blob 容器复制到剪贴板，然后再将该 Blob 容器粘贴到另一存储帐户中。 （若要了解如何复制各个 Blob，请参阅[管理 Blob 容器中的 Blob](#managing-blobs-in-a-blob-container) 部分。）

以下步骤演示了如何将 Blob 容器从一个存储帐户复制到另一个存储帐户。

1. 打开存储资源管理器。
2. 在左窗格中，展开包含想要复制的 Blob 容器的存储帐户。
3. 展开该存储帐户的“Blob 容器”。 
4. 右键单击想要复制的 Blob 容器，并从上下文菜单中选择“复制 Blob 容器”。 

   ![“复制 Blob 容器”上下文菜单][6]
5. 右键单击要将 Blob 容器粘贴到其中的“目标”存储帐户，并从上下文菜单中选择“粘贴 Blob 容器”。 

   ![“粘贴 Blob 容器”上下文菜单][7]

## <a name="get-the-sas-for-a-blob-container"></a>获取 Blob 容器的 SAS

[共享访问签名 (SAS)](storage/common/storage-dotnet-shared-access-signature-part-1.md) 用于对存储帐户中的资源进行委托访问。
这意味着可以授权客户端在指定时间段内，以一组指定权限有限地访问存储帐户中的对象，而不必共享帐户访问密钥。

以下步骤演示了如何为 Blob 容器创建 SAS：

1. 打开存储资源管理器。
2. 在左窗格中，展开想要获取其 SAS 的 Blob 容器所在的存储帐户。
3. 展开该存储帐户的“Blob 容器”。 
4. 右键单击所需 Blob 容器，并从上下文菜单中选择“获取共享访问签名”。 

   ![“获取 SAS”上下文菜单][8]
5. 在“共享访问签名”对话框中，根据需要为资源指定策略、开始和过期日期、时区以及访问级别。 

   ![“获取 SAS”选项][9]
6. 指定完 SAS 选项以后，选择“创建”。 
7. 然后会显示第二个“共享访问签名”对话框，其中列出了可用来访问存储资源的 Blob 容器以及 URL 和 QueryString。 
   选择要复制到剪贴板的 URL 旁边的“复制”。 

   ![复制 SAS URL][10]
8. 完成后，选择“关闭”。 

## <a name="manage-access-policies-for-a-blob-container"></a>管理 Blob 容器的访问策略

以下步骤演示了如何管理（添加和删除）Blob 容器的访问策略：

1. 打开存储资源管理器。
2. 在左窗格中，展开想要管理其访问策略的 Blob 容器所在的存储帐户。
3. 展开该存储帐户的“Blob 容器”。 
4. 选择所需 Blob 容器，并从上下文菜单中选择“管理访问策略”。 

   ![“管理访问策略”上下文菜单][11]
5. “访问策略”对话框将列出为所选 Blob 容器创建的任何访问策略。 

   ![“访问策略”选项][12]
6. 根据访问策略管理任务完成以下步骤：

   * **添加新的访问策略** - 选择“添加”。  生成后，“访问策略”对话框会显示新添加的访问策略（以及默认设置）。 
   * **编辑访问策略** - 进行需要的编辑，并选择“保存”。 
   * **删除访问策略** - 在要删除的访问策略旁边选择“删除”。 

## <a name="set-the-public-access-level-for-a-blob-container"></a>为 Blob 容器设置公共访问级别

默认情况下，每个 Blob 容器都设置为“无公共访问权限”。

以下步骤演示了如何指定 Blob 容器的公共访问级别。

1. 打开存储资源管理器。
2. 在左窗格中，展开想要管理其访问策略的 Blob 容器所在的存储帐户。
3. 展开该存储帐户的“Blob 容器”。 
4. 选择所需 Blob 容器，并从上下文菜单中选择“设置公共访问级别”。 

   ![“设置公共访问级别”上下文菜单][13]
5. 在“设置容器公共访问级别”对话框中，指定所需的访问级别。 

   ![“设置公共访问级别”选项][14]
6. 选择“应用”。 

## <a name="managing-blobs-in-a-blob-container"></a>管理 Blob 容器中的 Blob

创建 Blob 容器以后，即可将 Blob 上传到该 Blob 容器、将 Blob 下载到本地计算机、在本地计算机上打开 Blob，等等。

以下步骤演示如何管理 Blob 容器中的 Blob（和文件夹）。

1. 打开存储资源管理器。
2. 在左窗格中，展开想要管理的 Blob 容器所在的存储帐户。
3. 展开该存储帐户的“Blob 容器”。 
4. 双击想要查看的 Blob 容器。
5. 主窗格会显示该 Blob 容器的内容。

   ![查看 Blob 容器][3]
6. 主窗格会显示该 Blob 容器的内容。
7. 根据所要执行的任务完成以下步骤：

   * **将文件上传到 blob 容器**

     1. 在主窗格的工具栏上选择“上载”，并从下拉菜单中选择“上载文件”。  

        ![“上传文件”菜单][15]
     2. 在 **“上传文件”** 对话框中，选择 **“文件”** 文本框右侧的省略号 ( **…** ) 按钮，以选择要上传的文件。

        ![“上传文件”选项][16]
     3. 将类型指定为“Blob 类型”。  请参阅[创建容器](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container)有关详细信息。
     4. （可选）指定要将选定文件上传到其中的目标文件夹。 如果目标文件夹不存在，系统会创建一个。
     5. 选择 **“上传”** 。
   * **将文件夹上传到 Blob 容器**

     1. 在主窗格的工具栏上选择“上载”，并从下拉菜单中选择“上载文件夹”。  

        ![“上传文件夹”菜单][17]
     2. 在 **“上传文件夹”** 对话框中，选择 **“文件夹”** 文本框右侧的省略号 ( **…** ) 按钮，以选择要上传其内容的文件夹。

        ![“上传文件夹”选项][18]
     3. 将类型指定为“Blob 类型”。  请参阅[创建容器](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container)有关详细信息。
     4. （可选）指定要将选定文件夹的内容上传到其中的目标文件夹。 如果目标文件夹不存在，系统会创建一个。
     5. 选择 **“上传”** 。
   * **将 Blob 下载到本地计算机**

     1. 选择要下载的 Blob。
     2. 在主窗格的工具栏上，选择“下载”。 
     3. 在“指定已下载 Blob 的保存位置”对话框中，指定要将 Blob 下载到其中的位置，以及要为 Blob 提供的名称。   
     4. 选择“保存”。 
   * **在本地计算机上打开 Blob**

     1. 选择要打开的 Blob。
     2. 在主窗格的工具栏上，选择“打开”。 
     3. 将使用与 Blob 的基础文件类型相关联的应用程序下载和打开 Blob。
   * **将 Blob 复制到剪贴板**

     1. 选择要复制的 Blob。
     2. 在主窗格的工具栏上，选择“复制”。 
     3. 在左窗格中导航到另一 Blob 容器，并通过双击在主窗格中查看它。
     4. 在主窗格的工具栏上选择“粘贴”，以创建 Blob 的副本。 
   * **删除 Blob**

     1. 选择要删除的 Blob。
     2. 在主窗格的工具栏上，选择“删除”。 
     3. 出现确认对话框时，选择“是”。 

## <a name="next-steps"></a>后续步骤

* 查看[最新的存储资源管理器发行说明和视频](https://www.storageexplorer.com)。
* 了解如何[使用 Azure Blob、表、队列和文件创建应用程序](https://azure.microsoft.com/documentation/services/storage/)。

[0]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-create-context-menu.png
[1]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create.png
[2]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create-done.png
[3]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-editor.png
[4]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-context-menu.png
[5]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-confirmation.png
[6]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-copy-context-menu.png
[7]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-paste-context-menu.png
[8]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-context-menu.png
[9]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-options.png
[10]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-urls.png
[11]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-context-menu.png
[12]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-options.png
[13]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-context-menu.png
[14]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-options.png
[15]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-menu.png
[16]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-options.png
[17]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-menu.png
[18]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-options.png
[19]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-open-editor-context-menu.png
