---
title: 在 Azure HDInsight 上安装自己的自定义 Apache Hadoop 应用程序
description: 了解如何在 Azure HDInsight 中为 Apache Hadoop 群集安装 HDInsight 应用程序。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: b96f08ab03b6db73cfae413b42a4c7a1d75177a0
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076196"
---
# <a name="install-custom-apache-hadoop-applications-on-azure-hdinsight"></a>在 Azure HDInsight 上安装自定义 Apache Hadoop 应用程序

本文介绍如何在 Azure HDInsight 上安装尚未发布到 Azure 门户的 [Apache Hadoop](https://hadoop.apache.org/) 应用程序。 在本文中，要安装的应用程序是 [Hue](https://gethue.com/)。

HDInsight 应用程序是用户可以在基于 Linux 的 HDInsight 群集上安装的应用程序。  这些应用程序可能是 Microsoft、独立软件供应商 (ISV) 或自己开发的。  

其他相关文章：

* [安装 HDInsight 应用程序](hdinsight-apps-install-applications.md)：了解如何将 HDInsight 应用程序安装到群集。
* [发布 HDInsight 应用程序](hdinsight-apps-publish-applications.md)：了解如何将自定义 HDInsight 应用程序发布到 Azure 市场。
* [MSDN：安装 HDInsight 应用程序](https://msdn.microsoft.com/library/mt706515.aspx)：了解如何定义 HDInsight 应用程序。

## <a name="prerequisites"></a>先决条件
如果想要在现有的 HDInsight 群集上安装 HDInsight 应用程序，必须有一个 HDInsight 群集。 若要创建群集，请参阅 [创建群集](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster)。 也可以在创建 HDInsight 群集时安装 HDInsight 应用程序。

## <a name="install-hdinsight-applications"></a>安装 HDInsight 应用程序
可以在创建群集时安装 HDInsight 应用程序，也可以将它安装到现有的 HDInsight 群集。 若要了解如何定义 Azure 资源管理器模板，请参阅 [MSDN：安装 HDInsight 应用程序](https://msdn.microsoft.com/library/mt706515.aspx)。

部署此应用程序 (Hue) 时所需的文件：

* [azuredeploy.json](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/azuredeploy.json)：用于安装 HDInsight 应用程序的资源管理器模板。 有关如何开发自己的资源管理器模板的信息，请参阅 [MSDN：安装 HDInsight 应用程序](https://msdn.microsoft.com/library/mt706515.aspx)。
* [hue-install_v0.sh](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/scripts/Hue-install_v0.sh)：资源管理器模板为配置边缘节点而调用的脚本操作。
* [hue-binaries.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz)：从 hui-install_v0.sh 调用的 hue 二进制文件。
* [hue-binaries-14-04.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz)：从 hui-install_v0.sh 调用的 hue 二进制文件。
* [webwasb-tomcat.tar.gz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/webwasb-tomcat.tar.gz)：从 hui-install_v0.sh 调用的示例 Web 应用程序 (Tomcat)。

**将 Hue 安装到现有的 HDInsight 群集**

1. 单击以下映像以登录到 Azure，并在 Azure 门户中打开 Resource Manager 模板。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FHue%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    单击此按钮可在 Azure 门户上打开 Resource Manager 模板。  资源管理器模板位于 [https://github.com/hdinsight/Iaas-Applications/tree/master/Hue](https://github.com/hdinsight/Iaas-Applications/tree/master/Hue)。  若要了解如何编写此资源管理器模板，请参阅 [MSDN：安装 HDInsight 应用程序](https://msdn.microsoft.com/library/mt706515.aspx)。
2. 在“参数” 边栏选项卡中，输入以下内容：

   * **clusterName**：输入要安装应用程序的群集的名称。 此群集必须是现有的群集。
3. 单击“确定” 保存参数。
4. 在“自定义部署”边栏选项卡中输入“资源组”。  资源组是对群集、依赖存储帐户和其他资源进行分组的容器。 必须使用与群集相同的资源组。
5. 单击“法律条款”，并单击“创建”。
6. 确认已选中“固定到仪表板”复选框，并单击“创建”。 可以从固定到门户仪表板的磁贴和门户通知查看安装状态（单击门户顶部的铃铛图标）。  安装此应用程序大约需要 10 分钟。

**若要创建群集的同时安装色调**

1. 单击以下映像以登录到 Azure，并在 Azure 门户中打开 Resource Manager 模板。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhdinsightapps%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    单击此按钮可在 Azure 门户上打开 Resource Manager 模板。  资源管理器模板位于 [https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json](https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json)。  若要了解如何编写此资源管理器模板，请参阅 [MSDN：安装 HDInsight 应用程序](https://msdn.microsoft.com/library/mt706515.aspx)。
2. 根据说明来创建群集并安装 Hue。 有关创建 HDInsight 群集的详细信息，请参阅 [在 HDInsight 中创建基于 Linux 的 Hadoop 群集](hdinsight-hadoop-provision-linux-clusters.md)。

除了 Azure 门户，也可以使用 [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-powershell) 和 [Azure 经典 CLI](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-azure-cli) 来调用资源管理器模板。

## <a name="validate-the-installation"></a>验证安装
可以在 Azure 门户中检查应用程序状态，以验证应用程序安装。 此外，还可以验证所有 HTTP 终结点和网页（如果有）是否按预期出现：

**打开 Hue 门户**

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在左侧菜单中单击“HDInsight 群集” 。  如果未看到，请单击“浏览”，并单击“HDInsight 群集”。
3. 单击已安装应用程序的群集。
4. 在“设置”边栏选项卡中，单击“常规”类别下的“应用程序”。 应会显示出“已安装的应用”边栏选项卡中列出了“hue”。
5. 单击列表中的“hue” 列出属性。  
6. 单击网页链接以验证网站；在浏览器中打开 HTTP 终结点以验证 Hue Web UI，并使用 SSH 打开 SSH 终结点。 有关信息，请参阅[将 SSH 与 HDInsight 配合使用](hdinsight-hadoop-linux-use-ssh-unix.md)。

## <a name="troubleshoot-the-installation"></a>排查安装问题
可以通过门户通知查看应用程序安装状态（单击门户顶部的铃铛图标）。

如果应用程序安装失败，可以从 3 个位置查看错误消息和调试信息：

* HDInsight 应用程序：常规错误信息。

    从门户打开群集，并在“设置”边栏选项卡中单击“应用程序”：

    ![hdinsight 应用程序安装错误](./media/hdinsight-apps-install-custom-applications/hdinsight-apps-error.png)
* HDInsight 脚本操作：如果 HDInsight 应用程序的错误消息指出脚本操作失败，“脚本操作”窗格会显示有关脚本失败的详细信息。

    在“设置”边栏选项卡中单击“脚本操作”。 脚本操作历史记录中显示了错误消息

    ![hdinsight 应用程序脚本操作错误](./media/hdinsight-apps-install-custom-applications/hdinsight-apps-script-action-error.png)
* Ambari Web UI：如果安装脚本是失败的原因，请使用 Ambari Web UI 来检查有关安装脚本的完整日志。

    有关详细信息，请参阅 [故障排除](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting)。

## <a name="remove-hdinsight-applications"></a>删除 HDInsight 应用程序
可通过多种方式删除 HDInsight 应用程序。

### <a name="use-portal"></a>使用门户
**使用门户删除应用程序**

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在左侧菜单中单击“HDInsight 群集” 。  如果未看到，请单击“浏览”，并单击“HDInsight 群集”。
3. 单击已安装应用程序的群集。
4. 在“设置”边栏选项卡中，单击“常规”类别下的“应用程序”。 会看到已安装的应用程序列表。 本文中的 "**已安装的应用**" 边栏选项卡中列出了**色调**。
5. 右键单击想要删除的应用程序，并单击“删除”。
6. 单击“是”确认。

在门户中，还可以删除群集，或删除包含应用程序的资源组。

### <a name="use-azure-powershell"></a>使用 Azure PowerShell
使用 Azure PowerShell 可以删除群集或删除资源组。 请参阅 [使用 Azure PowerShell 删除群集](hdinsight-administer-use-powershell.md#delete-clusters)。

### <a name="use-azure-cli"></a>使用 Azure CLI
使用 Azure CLI 可以删除群集或删除资源组。 请参阅 [使用 Azure CLI 删除群集](hdinsight-administer-use-command-line.md#delete-clusters)。

## <a name="next-steps"></a>后续步骤
* [MSDN：安装 HDInsight 应用程序](https://msdn.microsoft.com/library/mt706515.aspx)：了解如何开发用于部署 HDInsight 应用程序的资源管理器模板。
* [安装 HDInsight 应用程序](hdinsight-apps-install-applications.md)：了解如何将 HDInsight 应用程序安装到群集。
* [发布 HDInsight 应用程序](hdinsight-apps-publish-applications.md)：了解如何将自定义 HDInsight 应用程序发布到 Azure 市场。
* [使用脚本操作自定义基于 Linux 的 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)：了解如何使用脚本操作安装其他应用程序。
* [使用资源管理器模板在 HDInsight 中创建基于 Linux 的 Apache Hadoop 群集](hdinsight-hadoop-create-linux-clusters-arm-templates.md)：了解如何调用资源管理器模板创建 HDInsight 群集。
* [在 HDInsight 中使用空边缘节点](hdinsight-apps-use-edge-node.md)：了解如何使用空边缘节点访问 HDInsight 群集、测试 HDInsight 应用程序以及托管 HDInsight 应用程序。
