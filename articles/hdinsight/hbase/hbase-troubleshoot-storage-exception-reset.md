---
title: Azure HDInsight 中的连接重置后出现存储异常
description: Azure HDInsight 中的连接重置后出现存储异常
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: 52135391024eafdfea15afd6c05a5d13bf92a2c7
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091583"
---
# <a name="scenario-storage-exception-after-connection-reset-in-azure-hdinsight"></a>场景：Azure HDInsight 中的连接重置后出现存储异常

本文介绍有关在与 Azure HDInsight 群集交互时遇到的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

无法创建新的 Apache HBase 表。

## <a name="cause"></a>原因

在表截断过程中，存在存储连接问题。 已在 HBase 元数据表中删除表项。 删除了除一个 blob 文件以外的所有文件。

尽管存储中没有名`/hbase/data/default/ThatTable`为的文件夹 blob。 WASB 驱动程序发现 blob 文件的存在，因此不允许创建名`/hbase/data/default/ThatTable`为的任何 blob，因为它假定父文件夹已存在，因此创建表将会失败。

## <a name="resolution"></a>分辨率

1. 从 Apache Ambari UI 重启活动的 HMaster。 这将允许两个备用 HMaster 中的一个处于活动状态，而新的活动 HMaster 会重新加载元数据表信息。 因此，你将不会`already-deleted`在 HMaster UI 中看到该表。

1. 可以从 UI 工具（如 Cloud Explorer）或运行命令（如`hdfs dfs -ls /xxxxxx/yyyyy`）中找到孤立 blob 文件。 运行`hdfs dfs -rmr /xxxxx/yyyy`以删除该 blob。 例如， `hdfs dfs -rmr /hbase/data/default/ThatTable/ThatFile` 。

现在，可以在 HBase 中创建同名的新表。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* [@AzureSupport](https://twitter.com/azuresupport)连接-官方 Microsoft Azure 帐户来改善客户体验。 将 Azure 社区连接到正确的资源：答案、支持和专家。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”，或打开“帮助 + 支持”中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限，并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
