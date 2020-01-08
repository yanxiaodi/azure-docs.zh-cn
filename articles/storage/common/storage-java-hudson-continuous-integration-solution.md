---
title: 如何将 Hudson 与 Blob 存储一起使用 | Microsoft Docs
description: 介绍如何将 Hudson 与 Microsoft Azure Blob 存储一起使用作为生成项目的存储库。
services: storage
author: seguler
ms.service: storage
ms.devlang: Java
ms.topic: article
ms.date: 08/13/2019
ms.author: tarcher
ms.subservice: common
ms.openlocfilehash: 10bfc3ce4666ee1653110099a3c8d22a58d80f35
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68985302"
---
# <a name="using-azure-storage-with-a-hudson-continuous-integration-solution"></a>将 Azure 存储用于 Hudson 持续集成解决方案
## <a name="overview"></a>概述
下列信息演示了如何将 Blob 存储用作 Hudson 持续集成 (CI) 解决方案创建的生成项目的存储库，或者用作要在生成过程中使用的可下载文件的源。 在以下情况下，会发现这一做法很有用：你在敏捷开发环境进行编码（使用 Java 或其他语言），生成基于持续集成运行并且需要一个适用于生成项目的存储库，以便（举例来说）你能与其他组织成员、客户共享生成项目或维护存档。  另一种情况是，当生成作业本身需要其他文件时，例如需要下载依赖项作为生成输入的一部分时。

在本教程中, 你将使用 Microsoft 提供的适用于 Hudson CI 的 Azure 存储插件。

## <a name="introduction-to-hudson"></a>Hudson 简介
Hudson 通过允许开发人员轻松地集成其代码更改以及自动和频繁地生成版本，实现了软件项目的持续集成，因此提高了开发人员的工作效率。 生成是版本控制的，并且可将生成项目上传到不同存储库中。 本文将演示如何将 Azure Blob 存储用作生成项目的存储库。 它还将演示如何从 Azure Blob 存储下载依赖项。

有关 Hudson 的更多信息，请访问 [Hudson 概览](https://wiki.eclipse.org/Hudson-ci/Meet_Hudson)。

## <a name="benefits-of-using-the-blob-service"></a>使用 Blob 服务的好处
使用 Blob 服务承载敏捷开发生成项目的好处包括：

* 生成项目和/或可下载依赖项的高可用性。
* Hudson CI 解决方案上传生成项目时的性能。
* 客户和合作伙伴下载生成项目时的性能。
* 通过选择匿名访问、基于过期的共享访问、签名访问、专用访问等来控制用户访问策略。

## <a name="prerequisites"></a>先决条件
需要下列项才能将 Blob 服务用于 Hudson CI 解决方案：

* 一个 Hudson 持续集成解决方案。
  
    如果当前没有 Hudson CI 解决方案，可以使用以下技术运行一个 Hudson CI 解决方案：
  
  1. 在已启用 Java 的计算机上，从 <http://hudson-ci.org/> 下载 Hudson WAR。
  2. 在打开到包含 Hudson WAR 的文件夹的命令提示符下，运行 Hudson WAR。 例如，如果下载了版本 3.1.2：
     
      `java -jar hudson-3.1.2.war`

  3. 在浏览器中，打开 `http://localhost:8080/`。 这会打开 Hudson 仪表板。
  4. 首次使用 Hudson 时，在以下网址完成初始设置：`http://localhost:8080/`。
  5. 完成初始设置后, 取消 Hudson war 的正在运行的实例, 再次启动 Hudson war, 然后重新打开 Hudson 仪表板, `http://localhost:8080/`该仪表板将用于安装和配置 Azure 存储插件。
     
      虽然典型 Hudson CI 解决方案将设置为作为一个服务运行，但在本教程中，通过命令行运行 Hudson war 就足够了。
* 一个 Azure 帐户。 可在 <https://www.azure.com> 注册 Azure 帐户。
* 一个 Azure 存储帐户。 如果还没有存储帐户，可使用[创建存储帐户](../common/storage-quickstart-create-account.md)中的步骤创建一个。
* 建议熟悉 Hudson CI 解决方案（但不是必需的），因为以下内容将使用一个基本示例向你演示使用 Blob 服务作为 Hudson CI 生成项目的存储库时所需的步骤。

## <a name="how-to-use-the-blob-service-with-hudson-ci"></a>如何将 Blob 服务用于 Hudson CI
要将 Blob 服务用于 Hudson，需要安装 Azure 存储插件，并对该插件进行配置以使用你的存储帐户，然后创建一个将生成项目上载到存储帐户的生成后操作。 将在下面各节中介绍这些步骤。

## <a name="how-to-install-the-azure-storage-plugin"></a>如何安装 Azure 存储插件
1. 在 Hudson 仪表板中，单击“管理 Hudson”。
2. 在“管理 Hudson”页，单击“管理插件”。
3. 单击“可用”选项卡。
4. 单击“其他”。
5. 在“项目上传程序”部分，选择“Microsoft Azure 存储插件”。
6. 单击“安装”。
7. 安装完毕后，重新启动 Hudson。

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>如何配置 Azure 存储插件以使用存储帐户
1. 在 Hudson 仪表板中，单击“管理 Hudson”。
2. 在“管理 Hudson”页，单击“配置系统”。
3. 在“Microsoft Azure 存储帐户配置”部分中：
   
    a. 输入你的存储帐户名称, 你可以从[Azure 门户](https://portal.azure.com)获取该名称。
   
    b. 输入存储帐户密钥, 该密钥也可从[Azure 门户](https://portal.azure.com)中获得。
   
    c. 如果要使用全局 Azure 云，请对“BLOB 服务终结点 URL”使用默认值。 如果你使用的是不同的 Azure 云, 请使用存储帐户的[Azure 门户](https://portal.azure.com)中指定的终结点。
   
    d. 单击“验证存储凭据”以验证存储帐户。
   
    e. [可选]如果有其他存储帐户并且希望其可供 Hudson CI 使用，请单击“添加更多存储帐户”。
   
    f. 单击**保存**以保存设置。

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>如何创建将生成项目上传到存储帐户的后期生成操作
为了进行说明，首先我们将需要创建一个将创建若干文件的作业，然后添加后期生成操作以将文件上传到存储帐户。

1. 在 Hudson 仪表板中，单击“新建作业”。
2. 将此作业命名为 **MyJob**，单击“生成自由格式的软件作业”，然后单击“确定”。
3. 在作业配置的“生成”部分，单击“添加生成步骤”并选择“执行 Windows 批处理命令”。
4. 在“命令”中，使用下列命令：

    ```   
        md text
        cd text
        echo Hello Azure Storage from Hudson > hello.txt
        date /t > date.txt
        time /t >> date.txt
    ```

5. 在作业配置的“生成后操作”部分，单击“将项目上传到 Microsoft Azure Blob 存储”。
6. 对于“存储帐户名称”，选择要使用的存储帐户。
7. 对于“容器名称”，请指定容器名称。 （如果上传生成项目时不存在该容器，则将创建该容器。）可使用环境变量，因此在此示例中，请输入 **${JOB_NAME}** 作为容器名称。
   
    **提示**
   
    针对“执行 Windows 批处理命令”输入脚本的“命令”部分下方，有一个指向 Hudson 所识别环境变量的链接。 单击此链接可了解环境变量名称和说明。 不允许将包含特殊字符的环境变量（如 BUILD_URL 环境变量）用作容器名称或通用虚拟路径。
8. 对于此示例，请单击“默认将新容器设为公开”。 （如果要使用私有容器，需要创建共享访问签名以允许访问。 这超出了本文的范围。 可在[使用共享访问签名 (SAS)](storage-sas-overview.md)中了解有关共享访问签名的详细信息。）
9. [可选] 如果要在上传生成项目之前清除容器的内容，请单击“在上传前清除容器”（如果不希望清除容器的内容，则使该复选框保持未选中状态）。
10. 对于“要上传的项目列表”，请输入 text/*.txt。
11. 对于“已上传项目的通用虚拟路径”，请输入 **${BUILD\_ID}/${BUILD\_NUMBER}** 。
12. 单击“保存”以保存设置。
13. 在 Hudson 仪表板中，单击“立即生成”以运行 **MyJob**。 检查控制台输出中的状态。 当生成后操作开始上传生成项目时，Azure 存储的状态消息将包括在控制台输出中。
14. 成功完成此作业后，可通过打开公共 Blob 检查生成项目。
    
    a. 登录到 [Azure 门户](https://portal.azure.com)。
    
    b. 单击“存储”。
    
    c. 单击用于 Hudson 的存储帐户名称。
    
    d. 单击“容器”。
    
    e. 单击名为 **myjob** 的容器，该名称是创建 Hudson 作业时分配的作业名称的小写形式。 在 Azure 存储中，容器名称和 Blob 名称都是小写的（并且区分大小写）。 在名为 **myjob** 的容器的 Blob 列表中，应能看到 **hello.txt** 和 **date.txt**。 复制这两项中任一项的 URL 并在浏览器中打开。 会看到已作为生成项目上传的文本文件。

每个作业只能创建一个用于将项目上传到 Azure Blob 存储的生成后操作。 用于将项目上传到 Azure Blob 存储的单个生成后操作可以在**要上传的项目列表**中使用分号作为分隔符指定不同的文件 (包括通配符) 和文件路径。 例如，如果 Hudson 生成在工作区的 **build** 文件夹中生成了 JAR 文件和 TXT 文件，并且希望将两者都上传到 Azure Blob 存储，请使用以下项作为“要上传的项目列表”值：**build/\*.jar;build/\*.txt**。 还可以使用双冒号语法指定要在 Blob 名称内使用的路径。 例如，如果要在 Blob 路径中使用 **binaries** 上传 JAR 并在 Blob 路径中使用 **notices** 上传 TXT 文件，请使用以下项作为“要上传的项目列表”值：**build/\*.jar::binaries;build/\*.txt::notices**。

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>如何创建从 Azure Blob 存储进行下载的生成步骤
以下步骤演示了如何配置从 Azure Blob 存储下载项目的生成步骤。 如果希望在生成中包括这些项（例如你保存在 Azure Blob 存储中的 JAR），则这会非常有用。

1. 在作业配置的“生成”部分中，单击“添加生成步骤”并选择“从 Azure Blob 存储下载”。
2. 对于“存储帐户名称”，请选择要使用的存储帐户。
3. 对于“容器名称”，指定包含要下载的 Blob 容器的名称。 可以使用环境变量。
4. 对于“Blob 名称”，请指定 Blob 名称。 可以使用环境变量。 另外，在指定 Blob 名称的初始字母后，可以使用星号作为通配符。 例如，**project\\** * 将指定名称以 **project** 开头的所有 Blob。
5. [可选]对于“下载路径”，在 Hudson 计算机上指定希望将文件从 Azure Blob 存储下载到的路径。 也可以使用环境变量。 （如未提供“下载路径”的值，则会将 Azure Blob 存储中的文件下载到作业工作区。）

如果还希望从 Azure Blob 存储下载其他项，可以创建其他生成步骤。

在运行生成后，可以检查生成历史记录控制台输出或下载位置，看是否成功下载了需要的 Blob。

## <a name="components-used-by-the-blob-service"></a>Blob 服务使用的组件
以下信息概述了 Blob 服务组件。

* **存储帐户**：对 Azure 存储进行的所有访问都要通过存储帐户完成。 存储帐户是访问 blob 的最高级别的命名空间。 一个帐户可以包含无限个容器，只要这些容器的总大小不超过 100 TB 即可。
* **容器**：一个容器包含一组 Blob 集。 所有 blob 必须位于相应的容器中。 一个帐户可以包含无限个容器。 一个容器可以存储无限个 Blob。
* **Blob**：任何类型和大小的文件。 可将两类 Blob 存储到 Azure 存储中：块 Blob 和页 Blob。 大部分文件都是块 blob。 单个块 Blob 最大可以为 200 GB。 本教程使用的是块 Blob。 另一种 blob 类型为页 blob，其大小可达 1 TB，对文件中的一系列字节进行频繁修改时，这种 blob 更加高效。 有关 Blob 的详细信息，请参阅 [Understanding Block Blobs, Append Blobs, and Page Blobs](https://msdn.microsoft.com/library/azure/ee691964.aspx)（了解块 Blob、追加 Blob 和页 Blob）。
* **URL 格式**：使用以下 URL 格式可对 Blob 寻址：
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    （以上格式适用于全局 Azure 云。 如果你使用的是不同的 Azure 云, 请使用[Azure 门户](https://portal.azure.com)中的终结点来确定你的 URL 终结点。)
  
    在以上格式中，`storageaccount` 表示存储帐户的名称，`container_name` 表示容器的名称，而 `blob_name` 表示 Blob 的名称。 在容器名称中，可具有多个由正斜杠 **/** 分隔的路径。 本教程的示例容器名称为 **MyJob**， **${BUILD\_ID}/${BUILD\_NUMBER}** 用于通用虚拟路径，因此 Blob 具有以下格式的 URL：
  
    `http://example.blob.core.windows.net/myjob/2014-05-01_11-56-22/1/hello.txt`

## <a name="next-steps"></a>后续步骤
* [Hudson 简介](https://wiki.eclipse.org/Hudson-ci/Meet_Hudson)
* [用于 Java 的 Azure 存储 SDK](https://github.com/azure/azure-storage-java)
* [Azure 存储客户端 SDK 参考](http://dl.windowsazure.com/storage/javadoc/)
* [Azure 存储服务 REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Azure 存储团队博客](https://blogs.msdn.com/b/windowsazurestorage/)

有关详细信息，请访问[面向 Java 开发人员的 Azure](/java/azure)。
