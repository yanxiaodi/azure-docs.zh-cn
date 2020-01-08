---
title: 将 HDInsight 群集升级到最新版本 - Azure
description: 了解将 Azure HDInsight 群集升级到较新版本的准则。
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/04/2017
ms.openlocfilehash: 808e592a01bca2066b7d3d37a5fdfa697d739d4b
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122447"
---
# <a name="upgrade-hdinsight-cluster-to-a-newer-version"></a>将 HDInsight 群集升级到更新版本

要利用最新的 HDInsight 功能，我们建议将 HDInsight 群集升级到最新版本。 遵循以下准则升级 HDInsight 群集版本。

> [!NOTE]  
> 有关支持的 HDInsight 版本的信息，请参阅 [HDInsight 组件版本](hdinsight-component-versioning.md#supported-hdinsight-versions)。

## <a name="upgrade-tasks"></a>升级任务

升级 HDInsight 群集的工作流如下所示。
![HDInsight 升级工作流关系图](./media/hdinsight-upgrade-cluster/upgrade-workflow-diagram.png)

1. 请阅读本文档的每个部分，了解在升级 HDInsight 群集时可能需要进行的更改。
2. 创建群集作为测试/质量保证环境。 有关创建群集的详细信息，请参阅[了解如何创建基于 Linux 的 HDInsight 群集](hdinsight-hadoop-provision-linux-clusters.md)
3. 将现有作业、数据源及接收器复制到新环境。
4. 执行验证测试，以确保作业在新群集上按预期工作。

验证一切都按预期工作后，请为迁移安排停机时间。 在此停机期间，请执行以下操作：

1.  备份所有存储在本地群集节点上的暂时性数据。 例如，如果数据直接存储在头节点上。
2.  删除现有的群集。
3.  使用前一群集使用的同一默认数据存储在具有最新（或支持）的 HDI 版本的同一 VNET 子网中创建群集。 这样，新群集便可针对现有生产数据继续运行。
4.  导入任何已备份的暂时性数据。
5.  使用新群集启动作业/继续处理。

## <a name="next-steps"></a>后续步骤

* [了解如何创建基于 Linux 的 HDInsight 群集](hdinsight-hadoop-provision-linux-clusters.md)
* [使用 SSH 连接到 HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)
* [使用 Apache Ambari 管理基于 Linux 的群集](hdinsight-hadoop-manage-ambari.md)
