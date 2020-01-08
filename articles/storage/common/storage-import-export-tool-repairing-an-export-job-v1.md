---
title: 修复 Azure 导入/导出服务的导出作业 - v1 | Microsoft Docs
description: 了解如何使用 Azure 导入/导出服务修复已创建和运行的导出作业。
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 915cf1e66ec400e0d2461873d9fb3d66be9883fb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "61477937"
---
# <a name="repairing-an-export-job"></a>修复导出作业
在完成导出作业后，可以在本地运行 Microsoft Azure 导入/导出工具来执行以下操作：  
  
1.  下载 Azure 导入/导出服务无法导出的任何文件。  
  
2.  验证是否正确导出了驱动器上的文件。  
  
若要使用此功能，必须与 Azure 存储建立连接。  
  
用于修复导入作业的命令是 **RepairExport**。

## <a name="repairexport-parameters"></a>RepairExport 参数

可以使用 **RepairExport** 指定以下参数：  
  
|参数|描述|  
|---------------|-----------------|  
|**/r:<RepairFile\>**|必需。 修复文件的路径。该文件用于跟踪修复进度，以及恢复已中断的修复。 每个驱动器都必须有且仅有一个修复文件。 在开始对某个给定驱动器进行修复时，会传入尚不存在的某个修复文件的路径。 若要恢复已中断的修复，应该传入现有修复文件的名称。 始终必须指定与目标驱动器对应的修复文件。|  
|**/logdir:<LogDirectory\>**|可选。 日志目录。 详细日志文件将写入此目录。 如果未指定任何日志目录，将使用当前目录作为日志目录。|  
|**/d:<TargetDirectory\>**|必需。 用于验证和修复的目录。 此目录通常是导出驱动器的根目录，但也可以是包含已导出的文件副本的网络文件共享。|  
|**/bk:<BitLockerKey\>**|可选。 如果希望工具将存储已导出文件的加密目录解锁，应指定 BitLocker 密钥。|  
|**/sn:<StorageAccountName\>**|必需。 导出作业的存储帐户的名称。|  
|**/sk:<StorageAccountKey\>**|当且仅当未指定容器 SAS 时才是**必需**的。 导出作业的存储帐户的帐户密钥。|  
|**/csas:<ContainerSas\>**|当且仅当未指定存储帐户密钥时才是**必需**的。 用于访问与导出作业关联的 Blob 的容器 SAS。|  
|**/CopyLogFile:<DriveCopyLogFile\>**|必需。 驱动器复制日志文件的路径。 该文件由 Microsoft Azure 导入/导出服务生成，可以从与该作业关联的 Blob 存储下载。 复制日志文件包含有关要修复的已失败 Blob 或文件的信息。|  
|**/ManifestFile:<DriveManifestFile\>**|可选。 导出驱动器的清单文件的路径。 此文件由 Windows Azure 导入/导出服务生成，存储在导出驱动器上，或者位于与该作业关联的存储帐户的 Blob 中。<br /><br /> 将使用在此文件中包含的 MD5 哈希验证导出驱动器上文件的内容。 确定下载已损坏的所有文件并将其重新写入目标目录。|  
  
## <a name="using-repairexport-mode-to-correct-failed-exports"></a>使用 RepairExport 模式更正失败的导出  
可以使用 Azure 导入/导出工具来下载未能导出的文件。 复制日志文件包含未能导出的文件列表。  
  
导出失败的可能原因包括：  
  
-   驱动器受损  
  
-   传输过程中更改了存储帐户密钥  
  
要在 **RepairExport** 模式下运行该工具，首先需要将包含已导出文件的驱动器连接到计算机。 接下来，运行 Azure 导入/导出工具，并使用 `/d` 参数指定该驱动器的路径。 还需要指定已下载的驱动器复制日志文件的路径。 以下示例命令行运行该工具，修复未能导出的所有文件：  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log  
```  
  
下面是复制日志文件的一个示例，显示未能导出的 Blob 中的一个块：  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog>  
  <DriveId>9WM35C2V</DriveId>  
  <Blob Status="CompletedWithErrors">  
    <BlobPath>pictures/wild/desert.jpg</BlobPath>  
    <FilePath>\pictures\wild\desert.jpg</FilePath>  
    <LastModified>2012-09-18T23:47:08Z</LastModified>  
    <Length>163840</Length>  
    <BlockList>  
      <Block Offset="65536" Length="65536" Id="AQAAAA==" Status="Failed" />  
    </BlockList>  
  </Blob>  
  <Status>CompletedWithErrors</Status>  
</DriveLog>  
```  
  
复制日志文件指示 Windows Azure 导入/导出服务在将 Blob 的某个块下载到导出驱动器上的文件时发生失败。 该文件的其他组成部分已成功下载，并且正确设置了文件长度。 在本例中，工具会在驱动器上打开该文件，从存储帐户下载该块并将其写入从偏移位置 65536 开始、长度为 65536 的文件范围。  
  
## <a name="using-repairexport-to-validate-drive-contents"></a>使用 RepairExport 验证驱动器内容  
还可以使用提供 **RepairExport** 选项的 Azure 导入/导出服务来验证驱动器上的内容是否正确。 每个导出驱动器上的清单文件包含驱动器内容的 MD5 哈希。  
  
Azure 导入/导出服务还可以在导出过程中将清单文件保存到某个存储帐户。 完成作业后，可通过[获取作业](/rest/api/storageimportexport/jobs)操作获得清单文件的位置。 有关驱动器清单文件格式的详细信息，请参阅[导入/导出服务清单文件格式](storage-import-export-file-format-metadata-and-properties.md)。  
  
以下示例演示如何结合 **/ManifestFile** 和 **/CopyLogFile** 参数运行 Azure 导入/导出工具：  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log /ManifestFile:G:\9WM35C3U.manifest  
```  
  
下面是清单文件的一个示例：  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveManifest Version="2011-10-01">  
  <Drive>  
    <DriveId>9WM35C3U</DriveId>  
    <ClientCreator>Windows Azure Import/Export service</ClientCreator>  
    <BlobList>
      <Blob>  
        <BlobPath>pictures/city/redmond.jpg</BlobPath>  
        <FilePath>\pictures\city\redmond.jpg</FilePath>  
        <Length>15360</Length>  
        <PageRangeList>  
          <PageRange Offset="0" Length="3584" Hash="72FC55ED9AFDD40A0C8D5C4193208416" />  
          <PageRange Offset="3584" Length="3584" Hash="68B28A561B73D1DA769D4C24AA427DB8" />  
          <PageRange Offset="7168" Length="512" Hash="F521DF2F50C46BC5F9EA9FB787A23EED" />  
        </PageRangeList>  
        <PropertiesPath Hash="E72A22EA959566066AD89E3B49020C0A">\pictures\city\redmond.jpg.properties</PropertiesPath>  
      </Blob>  
      <Blob>  
        <BlobPath>pictures/wild/canyon.jpg</BlobPath>  
        <FilePath>\pictures\wild\canyon.jpg</FilePath>  
        <Length>10884</Length>  
        <BlockList>  
          <Block Offset="0" Length="2721" Id="AAAAAA==" Hash="263DC9C4B99C2177769C5EBE04787037" />  
          <Block Offset="2721" Length="2721" Id="AQAAAA==" Hash="0C52BAE2CC20EFEC15CC1E3045517AA6" />  
          <Block Offset="5442" Length="2721" Id="AgAAAA==" Hash="73D1CB62CB426230C34C9F57B7148F10" />  
          <Block Offset="8163" Length="2721" Id="AwAAAA==" Hash="11210E665C5F8E7E4F136D053B243E6A" />  
        </BlockList>  
        <PropertiesPath Hash="81D7F81B2C29F10D6E123D386C3A4D5A">\pictures\wild\canyon.jpg.properties</PropertiesPath>  
      </Blob> 
    </BlobList>  
 </Drive>  
</DriveManifest>  
``` 
  
完成修复过程后，工具将读取在该清单文件中引用的每个文件，并使用 MD5 哈希验证该文件的完整性。 对于上面的清单文件，工具将遍历以下组成部分。  

```  
G:\pictures\city\redmond.jpg, offset 0, length 3584  
  
G:\pictures\city\redmond.jpg, offset 3584, length 3584  
  
G:\pictures\city\redmond.jpg, offset 7168, length 3584  
  
G:\pictures\city\redmond.jpg.properties  
  
G:\pictures\wild\canyon.jpg, offset 0, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 2721, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 5442, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 8163, length 2721  
  
G:\pictures\wild\canyon.jpg.properties  
```

工具将下载验证失败的所有组成部分，并将其重新写入驱动器上的同一文件。  
  
## <a name="next-steps"></a>后续步骤
 
* [设置 Azure 导入/导出工具](storage-import-export-tool-setup-v1.md)   
* [为导入作业准备硬盘驱动器](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [使用复制日志文件查看作业状态](storage-import-export-tool-reviewing-job-status-v1.md)   
* [修复导入作业](storage-import-export-tool-repairing-an-import-job-v1.md)   
* [排查 Azure 导入/导出工具问题](storage-import-export-tool-troubleshooting-v1.md)
