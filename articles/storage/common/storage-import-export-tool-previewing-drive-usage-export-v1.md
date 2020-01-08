---
title: 预览 Azure 导入/导出的导出作业的驱动器使用情况 - v1 | Microsoft Docs
description: 了解如何预览针对 Azure 导入/导出服务中的导出作业选择的 blob 列表。
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 53ab1e28c5864b403d52bf5e73f0c5c41b8f18a8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "61478447"
---
# <a name="previewing-drive-usage-for-an-export-job"></a>预览导出作业的驱动器使用情况
在创建导出作业之前，需要选择一组要导出的 blob。 Microsoft Azure 导入/导出服务允许使用一系列 blob 路径或 blob 前缀来表示选定的 blob。  
  
接下来，需确定所要发送的驱动器数量。 导入/导出工具提供 `PreviewExport` 命令，用于根据所要使用的驱动器大小来预览选定 blob 的驱动器使用情况。

## <a name="command-line-parameters"></a>命令行参数

使用导入/导出工具的 `PreviewExport` 命令时，可使用以下参数。

|命令行参数|描述|  
|--------------------------|-----------------|  
|**/logdir:** <LogDirectory\>|可选。 日志目录。 详细日志文件将写入此目录。 如果未指定任何日志目录，将使用当前目录作为日志目录。|  
|/sn:  <StorageAccountName\>|必需。 导出作业的存储帐户的名称。|  
|**/sk:** <StorageAccountKey\>|当且仅当未指定容器 SAS 时才是必需的。 导出作业的存储帐户的帐户密钥。|  
|**/csas:** <ContainerSas\>|当且仅当未指定存储帐户密钥时才是必需的。 用于列出要在导出作业中导出的 Blob 的容器 SAS。|  
|**/ExportBlobListFile:** <ExportBlobListFile\>|必需。 包含要导出的 Blob 的 Blob 路径列表或 Blob 路径前缀的 XML 文件的路径。 导入/导出服务 REST API 的[放置作业](/rest/api/storageimportexport/jobs)操作的 `BlobListBlobPath` 元素中使用的文件格式。|  
|**/DriveSize:** <DriveSize\>|必需。 用于导出作业的驱动器大小，例如  500GB、1.5TB。|  

## <a name="command-line-example"></a>命令行示例

以下示例演示了 `PreviewExport` 命令：  
  
```  
WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB    
```  
  
导出 Blob 列表文件可能包含 Blob 名称和 Blob 前缀，如下所示：  
  
```xml 
<?xml version="1.0" encoding="utf-8"?>  
<BlobList>  
<BlobPath>pictures/animals/koala.jpg</BlobPath>  
<BlobPathPrefix>/vhds/</BlobPathPrefix>  
<BlobPathPrefix>/movies/</BlobPathPrefix>  
</BlobList>  
```

Azure 导入/导出工具可列出要导出的所有 blob，在考虑所有必要开销的情况下计算如何将其打包到指定大小的驱动器，然后估算保存 blob 和驱动器使用情况信息所需的驱动器数量。  
  
下面是一个省略了信息性日志的输出示例：  
  
```  
Number of unique blob paths/prefixes:   3  
Number of duplicate blob paths/prefixes:        0  
Number of nonexistent blob paths/prefixes:      1  
  
Drive size:     500.00 GB  
Number of blobs that can be exported:   6  
Number of blobs that cannot be exported:        2  
Number of drives needed:        3  
        Drive #1:       blobs = 1, occupied space = 454.74 GB  
        Drive #2:       blobs = 3, occupied space = 441.37 GB  
        Drive #3:       blobs = 2, occupied space = 131.28 GB    
```  
  
## <a name="next-steps"></a>后续步骤

* [Azure 导入/导出工具参考](../storage-import-export-tool-how-to-v1.md)
