---
title: 使用 Azure 门户创建包含 Azure Data Lake Storage Gen1 的 Azure HDInsight 群集 | Microsoft Docs
description: 在 Azure 门户中创建和使用包含 Azure Data Lake Storage Gen1 的 HDInsight 群集
services: data-lake-store,hdinsight
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: a8c45a83-a8e3-4227-8b02-1bc1e1de6767
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 6f9064c6027499fff3a8551ee60722cd66c54dc2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60877547"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-by-using-the-azure-portal"></a>使用 Azure 门户创建包含 Azure Data Lake Storage Gen1 的 HDInsight 群集
> [!div class="op_single_selector"]
> * [使用 Azure 门户](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [使用 PowerShell（对于默认存储）](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [使用 PowerShell（对于附加存储）](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [使用资源管理器](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

了解如何使用 Azure 门户创建将 Azure Data Lake Storage Gen1 帐户用作默认存储或额外存储的 HDInsight 群集。 即使附加存储只是 HDInsight 群集的可选选项，仍建议将业务数据存储在附加存储帐户中。

## <a name="prerequisites"></a>必备组件
在开始学习本教程之前，请确保满足以下要求：

* **Azure 订阅**。 转到[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* **Data Lake Storage Gen1 帐户**。 请遵循[通过 Azure 门户开始使用 Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md) 中的说明进行操作。 还必须在该帐户上创建根文件夹。  本教程使用名为 __/clusters__ 的根文件夹。
* **一个 Azure Active Directory 服务主体**。 本教程提供了有关如何在 Azure Active Directory (Azure AD) 中创建服务主体的说明。 但是，只有 Azure AD 管理员才能创建服务主体。 管理员可以跳过此先决条件部分，继续阅读本教程。

    >[!NOTE]
    >仅是 Azure AD 管理员时，才能创建服务主体。 Azure AD 管理员必须先创建服务主体，才能创建包含 Data Lake Storage Gen1 的 HDInsight 群集。 此外，必须根据[使用证书创建服务主体](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate)中所述，使用证书创建服务主体。
    >

## <a name="create-an-hdinsight-cluster"></a>创建 HDInsight 群集

本部分创建使用 Data Lake Storage Gen1 帐户作为默认存储或额外存储的 HDInsight 群集。 本文仅重点介绍配置 Data Lake Storage Gen1 帐户的部分过程。  有关创建群集的一般信息和过程，请参阅[在 HDInsight 中创建 Hadoop 群集](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)。

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-default-storage"></a>创建使用 Data Lake Storage Gen1 作为默认存储的群集

**创建使用 Data Lake Storage Gen1 帐户作为默认存储帐户的 HDInsight 群集**

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 有关创建 HDInsight 群集的一般信息，请遵循[创建群集](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters)。
3. 在“存储”边栏选项卡的“主存储类型”下，选择“Azure Data Lake Storage Gen1”，然后输入以下信息    ：

    ![将服务主体添加到 HDInsight 群集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "将服务主体添加到 HDInsight 群集")

    - **选择 Data Lake Store 帐户**：选择现有的 Data Lake Storage Gen1 帐户。 必须具有现有的 Data Lake Storage Gen1 帐户。  请参阅[先决条件](#prerequisites)。
    - **根路径**：输入群集特定文件的存储路径。 在屏幕截图中，它是 __/clusters/myhdiadlcluster/__ ，其中， __/clusters__ 文件夹是必须存在的，*myhdicluster* 文件夹则由门户创建。  *myhdicluster* 是群集名称。
    - **Data Lake Store 访问**：配置 Data Lake Storage Gen1 帐户和 HDInsight 群集之间的访问。 有关说明，请参阅“配置 Data Lake Storage Gen1 访问”。
    - **附加存储帐户**：添加 Azure 存储帐户作为群集的附加存储帐户。 添加额外的 Data Lake Storage Gen1 帐户后，为群集提供对更多 Data Lake Storage Gen1 帐户中数据的权限，并将某个 Data Lake Storage Gen1 帐户配置为主存储类型。 请参阅“配置 Data Lake Storage Gen1 访问”。

4. 在“Data Lake Store 访问”中单击“选择”，并根据[在 HDInsight 中创建 Hadoop 群集](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md)中所述继续创建群集。  


### <a name="create-a-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>创建使用 Data Lake Storage Gen1 作为额外存储的群集

以下说明会创建一个 HDInsight 群集，该群集将 Azure 存储帐户作为默认存储，将 Data Lake Storage Gen1 帐户作为额外存储。

**创建使用 Data Lake Storage Gen1 帐户作为额外存储帐户的 HDInsight 群集**

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 有关创建 HDInsight 群集的一般信息，请遵循[创建群集](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters)。
3. 在“存储”  边栏选项卡的“主存储类型”  下，选择“Azure 存储”  ，然后输入以下信息：

    ![将服务主体添加到 HDInsight 群集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "将服务主体添加到 HDInsight 群集")

    - **选择方法**：使用以下选项之一：

        * 要指定属于 Azure 订阅的存储帐户，请选择“我的订阅”  ，并选择存储帐户。
        * 要指定不属于 Azure 订阅的存储帐户，请选择“访问密钥”  ，并提供该外部存储帐户的信息。

    - **默认容器**：使用默认值或指定自己的名称。

    - 附加存储帐户：添加更多 Azure 存储帐户作为附加存储。
    - Data Lake Store 访问：配置 Data Lake Storage Gen1 帐户和 HDInsight 群集之间的访问。 有关说明，请参阅[配置 Data Lake Storage Gen1 访问](#configure-data-lake-storage-gen1-access)。

## <a name="configure-data-lake-storage-gen1-access"></a>配置 Data Lake Storage Gen1 访问 

本部分使用 Azure Active Directory 服务主体从 HDInsight 群集配置 Data Lake Storage Gen1 访问。 

### <a name="specify-a-service-principal"></a>指定服务主体

在 Azure 门户中，可以使用现有的服务主体，或创建一个新的服务主体。

**在 Azure 门户中创建服务主体**

1. 从“存储”边栏选项卡中单击“Data Lake Store 访问”  。
2. 在“Data Lake Storage Gen1 访问”边栏选项卡中，单击“新建”   。
3. 单击“服务主体”，然后按照说明创建服务主体  。
4. 下载证书（如果决定以后再次使用）。 如要想要在创建其他 HDInsight 群集时使用相同的服务主体，下载证书的做法将非常有用。

    ![将服务主体添加到 HDInsight 群集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "将服务主体添加到 HDInsight 群集")

4. 单击“访问权限”  配置文件夹访问权限。  请参阅[配置文件权限](#configure-file-permissions)。


**使用 Azure 门户中的现有服务主体**

1. 单击“Data Lake Store 访问”  。
1. 在“Data Lake Storage Gen1 访问”边栏选项卡中，单击“使用现有项”   。
2. 单击“服务主体”，然后选择服务主体  。 
3. 上传与所选服务主体关联的证书（.pfx 文件），然后输入证书密码。

    ![将服务主体添加到 HDInsight 群集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "将服务主体添加到 HDInsight 群集")

4. 单击“访问权限”  配置文件夹访问权限。  请参阅[配置文件权限](#configure-file-permissions)。


### <a name="configure-file-permissions"></a>配置文件权限

配置过程根据帐户是用作默认存储还是附加存储帐户而有所不同：

- 用作默认存储

    - Data Lake Storage Gen1 帐户根级别的权限
    - HDInsight 群集存储根级别的权限。 例如，本教程前面使用的 __/clusters__ 文件夹。
- 用作附加存储

    - 需要文件访问权限的文件夹上的权限。

**分配 Data Lake Storage Gen1 帐户根级别的权限**

1. 在“Data Lake Storage Gen1 访问”边栏选项卡中，单击“访问”   。 随即打开“选择文件权限”  边栏选项卡。 其中列出了你订阅中的所有 Data Lake Storage Gen1 帐户。
2. 将鼠标悬停（不要单击）在 Data Lake Storage Gen1 帐户的名称上，使复选框可见，然后选中复选框。

    ![将服务主体添加到 HDInsight 群集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "将服务主体添加到 HDInsight 群集")

   “读取”  、“写入”  和“执行”  默认全部选中。

3. 单击页面底部的“选择”  。
4. 单击“运行”  分配权限。
5. 单击“完成”  。

**分配 HDInsight 群集根级别的权限**

1. 在“Data Lake Storage Gen1 访问”边栏选项卡中，单击“访问”   。 随即打开“选择文件权限”  边栏选项卡。 其中列出了你订阅中的所有 Data Lake Storage Gen1 帐户。
1. 从“选择文件权限”边栏选项卡中，单击 Data Lake Storage Gen1 帐户名显示其内容  。
2. 通过选中文件夹左侧的复选框，选择 HDInsight 群集存储根目录。 根据之前的屏幕截图，群集存储根目录是选择 Data Lake Storage Gen1 作为默认存储时指定的 __/clusters__ 文件夹。
3. 设置文件夹上的权限。  “读取”、“写入”和“执行”默认全部选中。
4. 单击页面底部的“选择”  。
5. 单击“运行”  。
6. 单击“完成”  。

如果使用 Data Lake Storage Gen1 作为额外存储，则必须仅为要从 HDInsight 群集访问的文件夹分配权限。 例如，在以下屏幕截图中，仅提供对 Data Lake Storage Gen1 帐户中 mynewfolder 文件夹的访问权限  。

![将服务主体权限分配给 HDInsight 群集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "将服务主体权限分配给 HDInsight 群集")


## <a name="verify-cluster-set-up"></a>验证群集设置

群集设置完成后，在群集边栏选项卡中通过执行以下一个或两个步骤来验证结果：

* 若要验证群集的关联存储是否为指定的 Data Lake Storage Gen1 帐户，请在左窗格中单击“存储帐户”  。

    ![将服务主体添加到 HDInsight 群集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "将服务主体添加到 HDInsight 群集")

* 若要验证服务主体是否已正确关联到 HDInsight 群集，请在左窗格中单击“Data Lake Storage Gen1 访问”  。

    ![将服务主体添加到 HDInsight 群集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "将服务主体添加到 HDInsight 群集")


## <a name="examples"></a>示例

将包含 Data Lake Storage Gen1 的群集设置为存储后，请参考以下示例，了解如何使用 HDInsight 群集来分析 Data Lake Storage Gen1 中存储的数据。

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-primary-storage"></a>针对 Data Lake Storage Gen1 帐户（用作主存储）中的数据运行 Hive 查询

若要运行 Hive 查询，请在 Ambari 门户中使用 Hive 视图界面。 有关 Ambari Hive 视图用法的说明，请参阅[将 Hive 视图与 HDInsight 中的 Hadoop 配合使用](../hdinsight/hadoop/apache-hadoop-use-hive-ambari-view.md)。

处理 Data Lake Storage Gen1 帐户中的数据时，需要更改几个字符串。

例如，如使用以 Data Lake Storage Gen1 作为主存储的群集，则数据的路径是：adl://<data_lake_storage_gen1_account_name>/azuredatalakestore.net/path/to/file  。 基于 Data Lake Storage Gen1 帐户中存储的示例数据创建表的 Hive 查询如以下语句所示：

    CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsg1storage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

说明：
* `adl://hdiadlsg1storage.azuredatalakestore.net/` 是 Data Lake Storage Gen1 帐户的根。
* `/clusters/myhdiadlcluster` 是在创建群集时指定的群集数据的根。
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` 是查询中所用示例文件的位置。

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-additional-storage"></a>针对 Data Lake Storage Gen1 帐户（用作额外存储）中的数据运行 Hive 查询

如果创建的群集使用 Blob 存储作为默认存储，示例数据将不会包含在用作额外存储的 Data Lake Storage Gen1 帐户中。 在这种情况下，请先将数据从 Blob 存储传输到 Data Lake Storage Gen1 帐户，然后按前面的示例中所示运行查询。

有关如何将数据从 Blob 存储复制到 Data Lake Storage Gen1 帐户的信息，请参阅以下文章：

* [使用 Distcp 在 Azure 存储 Blob 与 Data Lake Storage Gen1 之间复制数据](data-lake-store-copy-data-wasb-distcp.md)
* [使用 AdlCopy 将数据从 Azure 存储 Blob 复制到 Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-storage-gen1-with-a-spark-cluster"></a>在 Spark 群集中使用 Data Lake Storage Gen1
可以使用 Spark 群集对存储在 Data Lake Storage Gen1 帐户中的数据运行 Spark 作业。 有关详细信息，请参阅[使用 HDInsight Spark 群集分析 Data Lake Storage Gen1 中的数据](../hdinsight/spark/apache-spark-use-with-data-lake-store.md)。


### <a name="use-data-lake-storage-gen1-in-a-storm-topology"></a>在 Storm 拓扑中使用 Data Lake Storage Gen1
可使用 Data Lake Storage Gen1 帐户从 Storm 拓扑写入数据。 有关如何实现此方案的说明，请参阅[将 Azure Data Lake Storage Gen1 与 HDInsight 上的 Apache Storm 配合使用](../hdinsight/storm/apache-storm-write-data-lake-store.md)。

## <a name="see-also"></a>另请参阅
* [将 Data Lake Storage Gen1 与 Azure HDInsight 群集配合使用](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [PowerShell：创建 HDInsight 群集以使用 Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
