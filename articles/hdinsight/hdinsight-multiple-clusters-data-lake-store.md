---
title: 将多个 HDInsight 群集与一个 Azure Data Lake Storage 帐户一起使用
description: 了解如何通过单个 Data Lake Storage 帐户使用多个 HDInsight 群集
keywords: hdinsight 存储, hdfs, 结构化数据, 非结构化数据, data lake store
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: hrasheed
ms.openlocfilehash: 776d8f31a5353604ff1c887bdfa214d07b2bfb48
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70733186"
---
# <a name="use-multiple-hdinsight-clusters-with-an-azure-data-lake-storage-account"></a>通过一个 Azure Data Lake Storage 帐户使用多个 HDInsight 群集

从 HDInsight 版本 3.5 开始，可以创建将 Azure Data Lake Storage 帐户用作默认文件系统的 HDInsight 群集。
Data Lake Storage 支持无限存储，因此不仅非常适合用于托管大量数据，而且还适合用于托管共享单个 Data Lake Storage 帐户的多个 HDInsight 群集。 有关如何创建使用 Data Lake Storage 作为存储的 HDInsight 群集的说明，请参阅[快速入门：在 HDInsight 中设置群集](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)。

本文就如何设置可在多个**活动** HDInsight 群集之间使用的单个和共享 Data Lake Storage 帐户，向 Data Lake Storage 管理员提供了一些建议。 这些建议适用于在共享的 Data Lake Storage 帐户中托管多个安全以及不安全的 Apache Hadoop 群集。


## <a name="data-lake-storage-file-and-folder-level-acls"></a>Data Lake Storage 文件级和文件夹级 ACL

本文的余下部分假设你非常熟悉 Azure Data Lake Storage 中的文件级和文件夹级 ACL，[Azure Data Lake Storage 中的访问控制](../data-lake-store/data-lake-store-access-control.md)对此做了详细介绍。

## <a name="data-lake-storage-setup-for-multiple-hdinsight-clusters"></a>多个 HDInsight 群集的 Data Lake Storage 设置
让我们使用一个两层文件夹层次结构来说明如何将多个 HDInsight 群集与 Data Lake Storage 帐户一起使用。 假设 Data Lake Storage 帐户采用文件夹结构 **/clusters/finance**。 由于采用此结构，财务组织所需的所有群集都可以使用 /clusters/finance 作为存储位置。 将来，如果另一个组织（例如营销组织）想要使用同一个 Data Lake Storage 帐户创建 HDInsight 群集，则可以创建 /clusters/marketing。 我们暂时只使用 **/clusters/finance**。

若要让 HDInsight 群集有效地使用此文件夹结构，Data Lake Storage 管理员必须根据表中所述分配适当的权限。 表中所示的权限对应于访问 ACL，而不是默认 ACL。 


|文件夹  |权限  |拥有用户  |拥有组  | 命名用户 | 命名用户权限 | 命名组 | 命名组权限 |
|---------|---------|---------|---------|---------|---------|---------|---------|
|/ | rwxr-x--x  |admin |admin  |服务主体 |--x  |FINGRP   |r-x         |
|/clusters | rwxr-x--x |admin |admin |服务主体 |--x  |FINGRP |r-x         |
|/clusters/finance | rwxr-x--t |admin |FINGRP  |服务主体 |rwx  |-  |-     |

在表中，

- **admin** 是 Data Lake Storage 帐户的创建者和管理员。
- **Service principal** 是与帐户关联的 Azure Active Directory (AAD) 服务主体。
- **FINGRP** 是在 AAD 中创建的用户组，其中包含财务组织中的用户。

有关如何创建 AAD 应用程序（以及创建服务主体）的说明，请参阅[创建 AAD 应用程序](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)。 有关如何在 AAD 中创建用户组的说明，请参阅[在 Azure Active Directory 中管理组](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

需要考虑一些要点。

- 在将存储帐户用于群集**之前**，Data Lake Storage 管理员必须使用适当的权限创建并预配双级文件夹结构 ( **/clusters/finance/** )。 创建群集时不会自动创建此结构。
- 上面的示例建议将拥有组 **/clusters/finance** 设置为 **FINGRP**，并允许 FINGRP 对从根目录开始的整个文件夹层次结构进行 **r-x** 访问。 这可以确保 FINGRP 的成员能够导航从根目录开始的文件夹结构。
- 如果不同的 AAD 服务主体可以在 **/clusters/finance** 下创建群集，则粘性位（如果已针对 **finance** 文件夹设置）可确保一个服务主体创建的文件夹不能被另一个服务主体删除。
- 文件夹结构和权限到位后，HDInsight 群集创建过程会在 **/clusters/finance/** 下创建群集特定的存储位置。 例如，名为 fincluster01 的群集的存储可以是 **/clusters/finance/fincluster01**。 下表显示了 HDInsight 群集创建的文件夹的所有权和权限。

    |文件夹  |权限  |拥有用户  |拥有组  | 命名用户 | 命名用户权限 | 命名组 | 命名组权限 |
    |---------|---------|---------|---------|---------|---------|---------|---------|
    |/clusters/finanace/ fincluster01 | rwxr-x---  |Service Principal |FINGRP  |- |-  |-   |-  | 
   


## <a name="recommendations-for-job-input-and-output-data"></a>有关作业输入和输出数据的建议

我们建议在 **/clusters** 外部的某个文件夹中存储作业的输入数据以及作业的输出。 这样，即使是为了回收一些存储空间而删除了特定于群集的文件夹，也能确保作业输入和输出仍可供将来使用。 在这种情况下，请确保用于存储作业输入和输出的文件夹层次结构允许服务主体进行适当级别的访问。

## <a name="limit-on-clusters-sharing-a-single-storage-account"></a>对共享单个存储帐户的群集的限制

可共享单个 Data Lake Storage 帐户的群集数限制取决于这些群集上正在运行的工作负荷。 使用过多的群集或者在共享某个存储帐户的群集上运行很大的工作负荷可能会导致存储帐户入口/出口受到限制。

## <a name="support-for-default-acls"></a>默认 ACL 的支持

创建具有命名用户访问权限的服务主体（如上表中所示）时，我们建议**不要**使用默认 ACL 添加命名用户。 使用默认 ACL 预配命名用户访问权限会导致为拥有用户、拥有组和其他对象分配 770 个权限。 尽管默认值 770 不会消除拥有用户 (7) 或拥有组 (7) 的权限，但会消除其他对象的权限 (0)。 这样就会导致一个已知的问题，[已知问题和解决方案](#known-issues-and-workarounds)部分详细介绍了此特殊用例。

## <a name="known-issues-and-workarounds"></a>已知问题和解决方法

本部分列出有关将 HDInsight 与 Data Lake Storage 配合使用的已知问题及其解决方法。

### <a name="publicly-visible-localized-apache-hadoop-yarn-resources"></a>公开可见的本地化 Apache Hadoop YARN 资源

创建新的 Azure Data Lake Storage 帐户时，会自动预配访问 ACL 权限位设置为 770 的根目录。 根文件夹的拥有用户设置为创建帐户的用户（Data Lake Storage 管理员），拥有组设置为创建帐户的用户的主要组。 不会为“其他对象”提供任何访问权限。

这些设置已知会影响 [YARN 247](https://hwxmonarch.atlassian.net/browse/YARN-247) 中捕获的一个特定 HDInsight 用例。 作业提交可能失败并出现类似于下面的错误消息：

    Resource XXXX is not publicly accessible and as such cannot be part of the public cache.

如前面链接的 YARN JIRA 中所述，本地化公共资源时，本地化程序将通过检查所有被请求资源对远程文件系统的权限，来验证这些资源是否确实是公共资源。 不符合该条件的任何 LocalResource 会被拒绝本地化。 检查权限，包括“其他对象”对文件的读取访问权限。 如果将 HDInsight 群集托管在 Azure Data Lake 上，则无法现成地运行此方案，因为 Azure Data Lake 会拒绝“其他对象”在根文件夹级别的访问。

#### <a name="workaround"></a>解决方法
通过层次结构为**其他对象**设置读取-执行权限，例如，在上表中所示的 **/** 、 **/clusters** 和 **/clusters/finance** 级别。

## <a name="see-also"></a>请参阅

* [快速入门：在 HDInsight 中设置群集](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [将 Azure Data Lake Storage Gen2 用于 Azure HDInsight 群集](hdinsight-hadoop-use-data-lake-storage-gen2.md)
