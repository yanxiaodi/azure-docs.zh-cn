---
title: Apache Ambari Tez View 在 Azure HDInsight 中的加载速度缓慢
description: Apache Ambari Tez 视图可能会加载缓慢或根本不会加载到 Azure HDInsight 中
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 218f1b66152354cbf7f23b48e0ebf01be62e5550
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091275"
---
# <a name="scenario-apache-ambari-tez-view-loads-slowly-in-azure-hdinsight"></a>场景：Apache Ambari Tez View 在 Azure HDInsight 中的加载速度缓慢

本文介绍在 Azure HDInsight 群集中使用交互式查询组件时遇到的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

Apache Ambari Tez 视图可能负载缓慢或根本无法加载。 加载 Ambari Tez 视图时，可能会看到头节点上的进程变得无响应。

## <a name="cause"></a>原因

如果群集上运行了大量的 Hive 作业，则访问 Yarn ATS Api 有时可能会对 Oct 2017 之前创建的群集产生不良性能。

## <a name="resolution"></a>分辨率

这是已在10月2017中解决的问题。 重新创建群集后，就不会再次遇到此问题。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* 与[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帐户联系，通过将 Azure 社区连接到适当的资源来改进客户体验：答案、支持和专家。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”，或打开“帮助 + 支持”中心。 有关更多详细信息，请查看[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限，并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
