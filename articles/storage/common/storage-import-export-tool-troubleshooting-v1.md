---
title: 排查 Azure 导入/导出工具问题 | Microsoft Docs
description: 了解使用 Azure 导入/导出工具时遇到的一些常见问题及其解决方法。
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 9a4e47143515c7f9c21d701809c35d61853d91ec
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60320442"
---
# <a name="troubleshooting-the-azure-importexport-tool"></a>排查 Azure 导入/导出工具问题
在遇到问题时，Microsoft Azure 导入/导出工具会返回错误消息。 本主题列出用户可能会遇到的一些常见问题。  
  
## <a name="a-copy-session-fails-what-i-should-do"></a>复制会话失败该怎么办？  
 如果复制会话失败，可以采用两种做法：  
  
 如果该错误可以通过重试来解决，例如，网络共享短期处于脱机状态，而现在已重新联机，则可以恢复复制会话。 如果该错误不可以通过重试来解决，例如，在命令行参数中指定了错误的源文件目录，则需要中止复制会话。 有关恢复和中止复制会话的详细信息，请参阅[为导入作业准备硬盘驱动器](../storage-import-export-tool-preparing-hard-drives-import-v1.md)。  
  
## <a name="i-cant-resume-or-abort-a-copy-session"></a>无法恢复或中止复制会话。  
 如果该复制会话是驱动器的第一个复制会话，错误消息应会指出：“无法恢复或中止第一个复制会话”。 在此情况下，可以删除旧日记文件，并重新运行该命令。  
  
 如果复制会话不是驱动器的第一个复制会话，则始终可以恢复或中止该会话。  
  
## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>在丢失日记文件的情况下是否仍可创建作业？  
 驱动器的日记文件包含将数据复制到此驱动器时记录的完整信息，向驱动器添加更多文件以及创建导入作业时需要该日记文件。 如果丢失日记文件，需要为驱动器重做所有复制会话。  
  
## <a name="next-steps"></a>后续步骤
 
* [设置 Azure 导入/导出工具](../storage-import-export-tool-setup-v1.md)   
* [为导入作业准备硬盘驱动器](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [使用复制日志文件查看作业状态](../storage-import-export-tool-reviewing-job-status-v1.md)   
* [修复导入作业](../storage-import-export-tool-repairing-an-import-job-v1.md)   
* [修复导出作业](../storage-import-export-tool-repairing-an-export-job-v1.md)
