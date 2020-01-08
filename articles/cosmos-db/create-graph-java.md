---
title: 在 Azure Cosmos DB 中使用 Java 生成图形数据库
description: 演示一个可以用来连接到 Azure Cosmos DB 并使用 Gremlin 查询其中图形数据的 Java 代码示例。
author: luisbosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: java
ms.topic: quickstart
ms.date: 03/26/2019
ms.author: lbosq
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 1b37475cfa8df38a00ea6017d47e90677ed457d2
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212641"
---
# <a name="quickstart-build-a-graph-database-with-the-java-sdk-and-the-azure-cosmos-db-table-api"></a>快速入门：使用 Java SDK 和Azure Cosmos DB 表 API 构建图形数据库

> [!div class="op_single_selector"]
> * [Gremlin 控制台](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

Azure Cosmos DB 是 Microsoft 提供的全球分布式多模型数据库服务。 使用 Azure Cosmos DB，可以快速创建和查询托管的文档、表和图形数据库。 

本快速入门使用适用于 Azure Cosmos DB 的 Azure 门户工具，创建简单的图形数据库。 本快速入门还介绍了如何使用 [Gremlin API](graph-introduction.md) 数据库（该数据库使用 OSS [Apache TinkerPop](https://tinkerpop.apache.org/) 驱动程序）快速创建 Java 控制台应用。 本快速入门中的说明适用于任何能够运行 Java 的操作系统。 借助本快速入门，可以熟悉如何通过 UI 或编程方式（以首选方式为准）创建和修改图形。 

## <a name="prerequisites"></a>先决条件
[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

此外：

* [Java 开发工具包 (JDK) 版本 8](https://aka.ms/azure-jdks)
    * 请确保设置 JAVA_HOME 环境变量，使之指向在其中安装了 JDK 的文件夹。
* [下载](https://maven.apache.org/download.cgi)和[安装](https://maven.apache.org/install.html) [Maven](https://maven.apache.org/) 二进制存档
    * 在 Ubuntu 上，可以通过运行 `apt-get install maven` 来安装 Maven。
* [Git](https://www.git-scm.com/)
    * 在 Ubuntu 上，可以通过运行 `sudo apt-get install git` 来安装 Git。

## <a name="create-a-database-account"></a>创建数据库帐户

在创建图形数据库之前，需通过 Azure Cosmos DB 创建 Gremlin (Graph) 数据库帐户。

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>添加图形

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>克隆示例应用程序

现在，让我们转到如何使用代码上来。 从 GitHub 克隆 Gremlin API 应用，设置连接字符串，并运行应用。 会看到以编程方式处理数据是多么容易。  

1. 打开命令提示符，新建一个名为“git-samples”的文件夹，然后关闭命令提示符。

    ```bash
    md "C:\git-samples"
    ```

2. 打开诸如 git bash 之类的 git 终端窗口，并使用 `cd` 命令更改为相应的示例应用程序安装文件夹。  

    ```bash
    cd "C:\git-samples"
    ```

3. 运行下列命令以克隆示例存储库。 此命令在计算机上创建示例应用程序的副本。 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-java-getting-started.git
    ```

## <a name="review-the-code"></a>查看代码

此步骤是可选的。 如果有意了解如何使用代码创建数据库资源，可以查看以下代码片段。 否则，可以直接跳转到[更新连接字符串](#update-your-connection-information)。

以代码片段全部摘自 C:\git-samples\azure-cosmos-db-graph-java-getting-started\src\GetStarted\Program.java 文件。

* Gremlin `Client` 已从 C:\git-samples\azure-cosmos-db-graph-java-getting-started\src\remote.yaml 文件中的配置初始化。

    ```java
    cluster = Cluster.build(new File("src/remote.yaml")).create();
    ...
    client = cluster.connect();
    ```

* 将使用 `client.submit` 方法执行一系列 Gremlin 步骤。

    ```java
    ResultSet results = client.submit(gremlin);

    CompletableFuture<List<Result>> completableFutureResults = results.all();
    List<Result> resultList = completableFutureResults.get();

    for (Result result : resultList) {
        System.out.println(result.toString());
    }
    ```

## <a name="update-your-connection-information"></a>更新连接信息

现在，返回到 Azure 门户，获取连接信息，并将信息复制到应用程序中。 借助这些设置，应用程序可以与托管的数据库进行通信。

1. 在 [Azure 门户](https://portal.azure.com/)中，选择“密钥”  。 

    复制 URI 值的第一部分。

    ![在 Azure 门户的“密钥”页中，查看并复制访问密钥](./media/create-graph-java/copy-access-key-azure-portal.png)
2. 打开 src/remote.yaml 文件，并覆盖 `hosts: [$name$.graphs.azure.com]` 中的 `$name$` 粘贴唯一 ID 值。

    remote.yaml 的第 1 行现应如下所示 

    `hosts: [test-graph.graphs.azure.com]`

3. 将 `endpoint` 值中的 `graphs` 更改为 `gremlin.cosmosdb`。 （如果是在 2017 年 12 月 20 日之前创建的图形数据库帐户，请勿更改终结点值，继续执行下一步即可。）

    终结点值现在应如下所示：

    `"endpoint": "https://testgraphacct.gremlin.cosmosdb.azure.com:443/"`

4. 在 Azure 门户中，使用“复制”按钮复制主密钥，并将它粘贴到 `password: $masterKey$` 中的 `$masterKey$`。

    remote.yaml 的第 4 行现应如下所示 

    `password: 2Ggkr662ifxz2Mg==`

5. 将 remote.yaml 的第 3 行从

    `username: /dbs/$database$/colls/$collection$`

    to 

    `username: /dbs/sample-database/colls/sample-graph`

    如果为示例数据库或图形使用了唯一名称，请相应地更新这些值。

6. 保存 remote.yaml 文件。

## <a name="run-the-console-app"></a>运行控制台应用

1. 在 git 终端窗口中，通过 `cd` 命令转到 azure-cosmos-db-graph-java-getting-started 文件夹。

    ```git
    cd "C:\git-samples\azure-cosmos-db-graph-java-getting-started"
    ```

2. 在 git 终端窗口中，使用以下命令安装所需的 Java 包。

   ```git
   mvn package
   ```

3. 在 git 终端窗口中，使用以下命令启动 Java 应用程序。
    
    ```git
    mvn exec:java -D exec.mainClass=GetStarted.Program
    ```

    终端窗口会显示添加到图形的顶点。 
    
    如果遇到超时错误，请在[更新你的连接信息](#update-your-connection-information)中检查是否正确更新了连接信息，并尝试再次运行上一个命令。 
    
    一旦程序停止运行，请选择 Enter，然后在 Internet 浏览器中立即切换回 Azure 门户。 

<a id="add-sample-data"></a>
## <a name="review-and-add-sample-data"></a>查看并添加示例数据

现在可以回到数据资源管理器，查看添加到图形的顶点，并添加其他数据点。

1. 选择“数据资源管理器”  ，展开“sample-graph”  ，再依次选择“图”  和“应用筛选器”  。 

   ![在 Azure 门户的数据资源管理器中创建新文档](./media/create-graph-java/azure-cosmosdb-data-explorer-expanded.png)

2. 在“结果”列表中，请注意添加到图形的新用户。  选择“ben”，请注意，该用户已连接到 robin。  可以通过拖放操作来移动顶点，也可以通过滚动鼠标滚轮进行缩放，并能用双箭头放大图形。 

   ![在 Azure 门户数据资源管理器的图形中的新顶点](./media/create-graph-java/azure-cosmosdb-graph-explorer-new.png)

3. 接下来，添加几个新用户。 选择“新建顶点”，向图添加数据。 

   ![在 Azure 门户的数据资源管理器中创建新文档](./media/create-graph-java/azure-cosmosdb-data-explorer-new-vertex.png)

4. 在标签框中，输入 *person*。

5. 选择“添加属性”  ，以添加下列每个属性。 注意，可以在图形中为每个人创建唯一属性。 仅 id 键是必需的。

    key|值|说明
    ----|----|----
    id|ashley|顶点的唯一标识符。 如果未指定 id，将为你生成一个。
    gender|女| 
    技术 | java | 

    > [!NOTE]
    > 在本快速入门中，将创建未分区的集合。 但是，如果在创建集合过程中通过指定分区键创建了分区的集合，则需在每个新顶点中包括该分区键作为键。 

6. 选择“确定”  。 可能需要展开屏幕才能在屏幕底部看到“确定”。 

7. 再次选择“新建顶点”，添加其他新用户。  

8. 输入标签“人员”  。

9. 选择“添加属性”  ，以添加下列每个属性：

    key|值|说明
    ----|----|----
    id|rakesh|顶点的唯一标识符。 如果未指定 id，将为你生成一个。
    gender|男| 
    学校|MIT| 

10. 选择“确定”  。 

11. 选择“应用筛选器”  按钮（使用默认 `g.V()` 筛选器），显示图中的所有值。 所有用户此时会显示在“结果”列表中。  

    添加更多数据时，可以使用筛选器来限制结果。 默认情况下，数据资源管理器使用 `g.V()` 检索图形中的所有顶点。 可以更改为其他[图形查询](tutorial-query-graph.md)（如 `g.V().count()`），以 JSON 格式返回图形中所有 顶点的计数。 如果更改了筛选器，请将筛选器更改回 `g.V()`，并选择“应用筛选器”  ，再次显示所有结果。

12. 现在可以连接 rakesh 与 ashley。 确保“ashley”在“结果”列表中为选中状态，然后选择右下侧“目标”旁边的“更改图中某个顶点的目标”。   ![](./media/create-graph-java/edit-pencil-button.png)  可能需要扩大窗口才能看到该按钮。

    ![更改图形中某个顶点的目标 - Azure CosmosDB](./media/create-graph-java/azure-cosmosdb-data-explorer-edit-target.png)

13. 在“目标”框中输入“rakesh”，   在“Edge 标签”框中输入“认识”，然后选中复选框。  

    ![在数据资源管理器中添加连接 - Azure CosmosDB](./media/create-graph-java/azure-cosmosdb-data-explorer-set-target.png)

14. 现在，从结果列表中选择“rakesh”即可看到  ashley 和 rakesh 已连接。 

    ![在数据资源管理器中连接的两个顶点 - Azure CosmosDB](./media/create-graph-java/azure-cosmosdb-graph-explorer.png)

    这就完成了本教程的资源创建部分。 可以继续向图形添加顶点、修改现有顶点，也可以更改查询。 现在，回顾一下 Azure Cosmos DB 提供的指标，然后清理资源。 

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 门户中查看 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门教程中，已了解如何创建 Azure Cosmos DB 帐户、使用数据资源管理器创建图形和运行应用。 现在可以使用 Gremlin 构建更复杂的查询，实现功能强大的图形遍历逻辑。 

> [!div class="nextstepaction"]
> [使用 Gremlin 查询](tutorial-query-graph.md)

