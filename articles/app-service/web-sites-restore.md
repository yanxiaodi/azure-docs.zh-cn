---
title: 还原应用 - Azure 应用服务
description: 了解如何从备份还原应用。
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: 4444dbf7-363c-47e2-b24a-dbd45cb08491
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 519cf5388b095c7ca6e0ae7d978608f0824dc3a2
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066517"
---
# <a name="restore-an-app-in-azure"></a>在 Azure 中还原应用
本文将演示如何在 [Azure 应用服务](../app-service/overview.md) 中还原已事先备份的应用（请参阅[在 Azure 中备份应用](manage-backup.md)）。 可以根据需要将应用及其链接的数据库还原到以前的状态，或者基于原始应用的备份之一创建新的应用。 Azure 应用服务支持用于备份和还原的以下数据库：
- [SQL 数据库](https://azure.microsoft.com/services/sql-database/)
- [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)
- [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql)
- [MySQL 应用内产品](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)

从备份还原适用于在**标准**和**高级**层中运行的应用。 有关向上缩放应用的信息，请参阅[在 Azure 中向上缩放应用](manage-scale-up.md)。 相比于**标准**层，**高级**层允许执行更多的每日备份量。

<a name="PreviousBackup"></a>

## <a name="restore-an-app-from-an-existing-backup"></a>从现有备份还原应用
1. 在 Azure 门户中应用的“设置”页上，单击“备份”以显示“备份”页。 然后，单击“还原”。
   
    ![选择“立即还原”][ChooseRestoreNow]
2. 在“还原”页中，首先选择备份源。
   
    ![](./media/web-sites-restore/021ChooseSource1.png)
   
    “应用备份”选项显示当前应用的所有现有备份，使你能够轻松地选择一个。
    “存储”选项使你能够从任何现有 Azure 存储帐户和订阅中的容器中选择任何备份 ZIP 文件。
    如果正在尝试还原其他应用的备份，请使用“存储”选项。
3. 然后，在“还原目标”中指定应用还原的目标。
   
    ![](./media/web-sites-restore/022ChooseDestination1.png)
   
   > [!WARNING]
   > 如果选择“覆盖”，则会清除并覆盖当前应用中所有的现有数据。 在单击“确定”之前，请确保该操作正是想要执行的操作。
   > 
   > 
   
   > [!WARNING]
   > 如果应用服务在还原数据库时正在向数据库写入数据，则可能会导致违反主键和数据丢失等症状。 建议在开始还原数据库之前先停止应用服务。
   > 
   > 
   
    可选择“现有应用”将应用备份还原到同一资源组中的其他应用。 使用此选项之前，应已使用应用备份中定义的镜像数据库配置在资源组中创建了其他应用。 还可以创建“新”应用来将内容还原到其中。

4. 单击 **“确定”** 。

<a name="StorageAccount"></a>

## <a name="download-or-delete-a-backup-from-a-storage-account"></a>从存储帐户中下载或删除备份
1. 在 Azure 门户的主“浏览”页中，选择“存储帐户”。 将显示现有存储帐户的列表。
2. 选择包含要下载或删除的备份的存储帐户。 此时显示存储帐户页。
3. 在存储帐户页中，选择所需的容器
   
    ![查看容器][ViewContainers]
4. 选择要下载或删除的备份文件。
   
    ![ViewContainers](./media/web-sites-restore/03ViewFiles.png)
5. 单击“下载”或“删除”，具体取决于要执行的操作。  

<a name="OperationLogs"></a>

## <a name="monitor-a-restore-operation"></a>监视还原操作
若要查看有关应用还原操作成功与否的详细信息，请导航到 Azure 门户中的“活动日志”页。  
 

向下滚动以查找所需的还原操作，并单击以选中。

“详细信息”页会显示与还原操作相关的可用信息。

## <a name="automate-with-scripts"></a>使用脚本自动化

可以在 [Azure CLI](/cli/azure/install-azure-cli) 或 [Azure PowerShell](/powershell/azure/overview) 中使用脚本自动备份管理。

相关示例如下所示：

- [Azure CLI 示例](samples-cli.md)
- [Azure PowerShell 示例](samples-powershell.md)

<!-- ## Next Steps
You can backup and restore App Service apps using REST API. -->


<!-- IMAGES -->
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow1.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
