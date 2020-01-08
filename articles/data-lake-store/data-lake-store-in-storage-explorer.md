---
title: 在 Azure 存储资源管理器中管理 Azure Data Lake Storage Gen1 资源
description: 了解如何在 Azure 存储资源管理器中访问和管理 Azure Data Lake Storage Gen1 数据和资源
Keywords: Azure Data Lake Store，Azure 存储资源管理器
services: Data Lake Store
documentationcenter: ''
author: jejiang
manager: DJ
editor: Jenny Jiang
ms.assetid: ''
ms.service: data-lake-store
ms.custom: Azure Data Lake Store
ms.devlang: dotnet
ms.topic: article
ms.date: 02/05/2018
ms.author: jejiang
ms.openlocfilehash: 18ef1f182611a9c8fbf24cd08026633f5449bbe9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60613534"
---
# <a name="manage-azure-data-lake-storage-gen1-resources-by-using-storage-explorer"></a>使用存储资源管理器管理 Azure Data Lake Storage Gen1 资源

[Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) 是一项用于存储大量的非结构化数据（例如文本数据或二进制数据）的服务。 可以通过 HTTP 或 HTTPS 从任何位置访问这些数据。 Azure 存储资源管理器中的 Data Lake Storage Gen1 允许访问和管理 Data Lake Storage Gen1 数据和资源，以及 Blob 和队列之类的其他 Azure 实体。 现在可以使用相同工具在一个位置管理不同 Azure 实体。

另一优势是，不需拥有订阅权限即可管理 Data Lake Storage Gen1 数据。 在存储资源管理器中，可以将 Data Lake Storage Gen1 路径附加到“本地和附加”节点，只要有人授予权限即可  。

## <a name="prerequisites"></a>必备组件
若要完成本文中的步骤，需要符合以下先决条件：

*   Azure 订阅。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial)。
*   Data Lake Storage Gen1 帐户。 有关如何创建帐户的说明，请参阅 [Azure Data Lake Storage Gen1 入门](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)。

## <a name="install-storage-explorer"></a>安装存储资源管理器

从[产品网页](https://azure.microsoft.com/features/storage-explorer/)安装最新的 Azure 存储资源管理器版本。 此安装支持 Windows、Linux 和 Mac 版本。

## <a name="connect-to-an-azure-subscription"></a>连接到 Azure 订阅

1. 在存储资源管理器中，选择左侧的插件图标。
       
   ![插件图标](./media/data-lake-store-in-storage-explorer/plug-in-icon.png)
 
2. 选择“添加 Azure 帐户”  ，然后选择“登录”  。

   ![“连接到 Azure 存储”对话框](./media/data-lake-store-in-storage-explorer/connect-to-azure-subscription.png)

2. 在“登录到帐户”  对话框中，输入 Azure 凭据。

    ![Azure 登录对话框](./media/data-lake-store-in-storage-explorer/sign-in.png)

3. 从列表中选择订阅，然后选择“应用”  。

    ![订阅信息和“应用”按钮](./media/data-lake-store-in-storage-explorer/apply-subscription.png)

    “资源管理器”窗格会进行更新，并显示所选订阅中的帐户。 

    ![帐户列表](./media/data-lake-store-in-storage-explorer/account-list.png)

已成功地将 Data Lake Storage Gen1 连接到 Azure 订阅。

## <a name="connect-to-data-lake-storage-gen1"></a>连接到 Data Lake Storage Gen1
可以访问订阅中不存在的资源，但前提是有人提供这些资源的 URI。 然后就可以在登录后使用该 URI 连接到 Data Lake Storage Gen1。
1. 打开存储资源管理器。
2. 在左窗格中，展开“本地和附加”  。
3. 右键单击“Data Lake Store”，然后选择“连接到 Data Lake Store”。  

      ![快捷菜单中的“连接到 Data Lake Store”](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach.png)

4. 输入 URI。 此工具会浏览到刚输入的 URL 的位置。

      ![“连接到 Data Lake Store”对话框，包含用于输入 URI 的文本框](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

      ![连接到 Data Lake Storage Gen1 的结果](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-attach-finish.png)

## <a name="view-a-data-lake-storage-gen1-accounts-contents"></a>查看 Data Lake Storage Gen1 帐户内容
Data Lake Storage Gen1 帐户的资源包含文件夹和文件。

以下步骤演示了如何查看存储资源管理器中 Data Lake Storage Gen1 帐户的内容：

1. 打开存储资源管理器。
2. 在左窗格中，展开包含要查看的 Data Lake Storage Gen1 帐户的订阅。
3. 展开“Data Lake Store”。 
4. 右键单击要查看的 Data Lake Storage Gen1 帐户节点，然后选择“打开”  。 也可双击 Data Lake Storage Gen1 帐户，将其打开。 
   
   主窗格显示 Data Lake Storage Gen1 帐户的内容。

   ![包含文件夹列表的主窗格](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-toolbar-mainpane.png) 

## <a name="manage-resources-in-data-lake-storage-gen1"></a>管理 Data Lake Storage Gen1 中的资源

可通过以下操作管理 Data Lake Storage Gen1 资源：
*   浏览多个 Data Lake Storage Gen1 帐户中的 Data Lake Storage Gen1 资源。  
*   使用连接字符串直接连接到 Data Lake Storage Gen1 并对其进行管理。 
*   通过“本地和附加”项下的 ACL 查看他人共享的 Data Lake Storage Gen1 资源  。
*   执行文件和文件夹 CRUD 操作：支持递归文件夹和多选文件。 
*   将文件夹拖放并添加到进行快速访问的最新位置， 此操作镜像桌面文件资源管理器体验。 
*   在存储资源管理器中复制并打开 Data Lake Storage Gen1 超链接，只需单击一下即可。 
*   在右下窗格中显示“活动日志”，以便查看活动状态。
*   显示文件夹统计信息和文件属性。

## <a name="manage-resources-in-azure-storage-explorer"></a>在 Azure 存储资源管理器中管理资源
创建 Data Lake Storage Gen1 帐户后，即可：

* 在本地计算机上上传和下载文件夹和文件，以及打开资源。
* 固定到“快速访问”、新建文件夹、复制 URL、全选。 
* 复制和粘贴、重命名、删除、获取文件夹统计信息、刷新。

以下项目演示了如何管理 Data Lake Storage Gen1 帐户中的资源。 执行以下步骤，这些步骤适用于要执行的任务。

### <a name="upload-files"></a>上传文件

1. 在主窗格的工具栏中选择“上传”，然后在下拉菜单中选择“上传文件”。  

   ![“上传文件”菜单项](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-menu.png) 

2. 在“选择要上传的文件”对话框中，选择要上传的文件。 

   ![用于上传文件的对话框](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-dialog.png)

3. 选择“打开”，  开始上传。

### <a name="upload-a-folder"></a>上传文件夹

1. 在主窗格的工具栏中选择“上传”，然后在下拉菜单中选择“上传文件夹”。  

   ![“上传文件夹”菜单项](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-menu.png) 
     
2. 在“选择要上传的文件夹”对话框中，选择要上传的文件夹。  然后单击“选择文件夹”  。

   ![用于上传文件夹的对话框](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-dialog.png)      

   上传开始。

   ![显示上传正在进行的对话框](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-drag.png) 

> [!NOTE]
> 直接在本地计算机上拖动文件夹和文件即可开始上传。 
       
### <a name="download-folders-or-files-to-your-local-computer"></a>将文件夹或文件下载到本地计算机

1. 选择要下载的文件夹或文件。
2. 在主窗格的工具栏上，选择“下载”。 
3. 在“选择用于保存已下载文件的文件夹”对话框中，指定位置和名称。 
4. 选择“保存”。 

### <a name="open-a-folder-or-file-from-your-local-computer"></a>从本地计算机打开文件夹或文件

1. 选择要打开的文件夹或文件。
2. 在主窗格的工具栏上，选择“打开”。  或者右键单击所选文件夹或文件，然后在快捷菜单中选择“打开”。 

可以在文件下载后通过与基础文件类型相关联的应用程序将其打开。 也可以在主窗格中打开文件夹。

![打开的文件](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-open.png) 

### <a name="copy-folders-or-files-to-the-clipboard"></a>将文件夹或文件复制到剪贴板

1. 选择要复制的文件夹或文件。
2. 在主窗格的工具栏上，选择“复制”。  或者右键单击所选文件夹或文件，然后在快捷菜单中选择“复制”。 
3. 在左窗格中浏览到另一 Data Lake Storage Gen1 帐户，然后通过双击在主窗格中查看它。
4. 在主窗格的工具栏中选择“粘贴”，创建一个副本。  也可在目标的快捷菜单中选择“粘贴”。 

![用于复制文件夹的选择](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-copy-paste.png)

> [!NOTE]
> 不支持跨存储类型进行的复制/粘贴操作。 可以复制 Data Lake Storage Gen1 文件夹或文件，然后将其粘贴到另一 Data Lake Storage Gen1 帐户中。 但是，不能将 Data Lake Storage Gen1 文件夹或文件复制粘贴到 Azure Blob 存储中，反之亦然  。
> 
> 复制/粘贴操作的工作原理是，先将文件夹或文件下载到本地计算机，然后再将其上传到目标。 此工具  不在后端执行该操作。 大型文件的复制/粘贴操作速度较慢。 目前正在对高性能的文件复制/移动进行优化。

### <a name="delete-folders-or-files"></a>删除文件夹或文件

1. 选择要删除的文件夹或文件。
2. 在主窗格的工具栏上，选择“删除”。  或者右键单击所选文件夹或文件，然后在快捷菜单中选择“删除”。 
3. 在确认对话框中选择“是”。 

![用于删除文件夹的选择](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-delete.png)

### <a name="pin-to-quick-access"></a>固定到“快速访问”

1. 选择要固定的文件夹。
2. 在主窗格的工具栏中，选择“固定到快速访问”。 

   在左窗格中，所选文件夹添加到“快速访问”节点。 

   ![用于将文件夹固定到“快速访问”的选择](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-quick-access.png)

将文件夹固定到“快速访问”节点以后，即可轻松地访问资源。 

### <a name="use-deep-links"></a>使用深层链接
如果有 URL，则可在文件资源管理器或浏览器的地址路径中输入该 URL。 然后，Storage Explorer.exe 就会自动运行，转到 URL 的位置。

![文件资源管理器中的深层链接](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-deep-link.png)


## <a name="next-steps"></a>后续步骤
* 查看[最新的存储资源管理器发行说明和视频](https://www.storageexplorer.com)。
* 了解如何[在 Azure 存储资源管理器中管理 Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/storage-explorer)。
* [存储资源管理器入门](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)。
* [Azure Data Lake Storage Gen1 入门](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview)。
* 观看[关于如何在 Azure 存储资源管理器中使用 Azure Cosmos DB 的 YouTube 视频](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be)。
