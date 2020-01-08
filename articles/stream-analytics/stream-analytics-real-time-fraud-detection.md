---
title: 使用 Azure 流分析实时检测欺诈行为
description: 了解如何通过流分析创建实时欺诈行为检测解决方案。 使用事件中心进行实时事件处理。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 19c9448b6a743302eb81bb208444336d6435f114
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947049"
---
# <a name="get-started-using-azure-stream-analytics-real-time-fraud-detection"></a>Azure 流分析入门：实时欺诈检测

本教程提供有关如何使用 Azure 流分析的端到端说明。 学习如何： 

* 将流事件引入 Azure 事件中心实例。 本教程将使用一个应用来模拟移动电话元数据记录流。

* 编写类似 SQL 的流分析查询来转换数据、聚合信息或查找模式。 了解如何使用查询来检查传入的流，并查找可能存在欺诈的呼叫。

* 将结果发送给输出接收器（存储），进行分析以获得其他见解。 在这种情况下，可以将可疑呼叫数据发送到 Azure Blob 存储。

本教程将使用基于电话呼叫数据的实时欺诈检测的示例。 所演示的技术还适用于其他类型的欺诈检测，如信用卡欺诈或身份盗用。 

## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>方案:实时进行远程通信和 SIM 欺诈检测

电信公司的传入呼叫数据量很大。 公司希望实时检测欺诈呼叫，以便他们可以通知客户或关闭特定数目的服务。 有这样一种 SIM 欺诈，即在同一时间出现多个同一身份发起的呼叫，但这些呼叫却位于不同的地理位置。 若要检测这种类型的欺诈, 公司需要检查传入的电话记录, 并查找特定模式-在本例中, 对于在不同国家/地区的相同时间拨打的呼叫。 任何属于此类别的电话记录都将写入存储，以供后续分析。

## <a name="prerequisites"></a>先决条件

本教程将通过使用生成示例电话呼叫元数据的客户端应用来模拟电话呼叫数据。 应用生成的某些记录看起来类似欺诈呼叫。 

在开始之前，请确保做好以下准备：

* 一个 Azure 帐户。
* 呼叫事件生成器应用 [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip)，可以从 Microsoft 下载中心下载此应用。 将此包解压缩到计算机上的文件夹中。 如果想要查看代码，并在调试程序中运行该应用，可从 [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator) 获取应用源代码。 

    >[!NOTE]
    >Windows 可能会阻止下载的 .zip 文件。 如果无法将其解压缩，请右键单击该文件，然后选择“属性”。 如果看到“此文件来自其他计算机，可能被阻止以帮助保护该计算机”的消息，则选择“取消阻止”选项，然后单击“应用”。

如果想要检查流分析作业的结果，还需要一种用于查看 Azure Blob 存储容器内容的工具。 如果使用 Visual Studio，则可以使用 [Azure Tools for Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) 或 [Visual Studio Cloud Explorer](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer)。 或者，可以安装独立工具，如 [Azure 存储资源管理器](https://storageexplorer.com/)或 [Cerulean](https://www.cerebrata.com/products/cerulean/features/azure-storage)。 

## <a name="create-an-azure-event-hubs-to-ingest-events"></a>创建 Azure 事件中心以引入事件

若要分析数据流，请将其引入 Azure。 引入数据的典型方式是使用 [Azure 事件中心](../event-hubs/event-hubs-what-is-event-hubs.md)，该中心每秒可以引入数百万个事件，从而使用户能够处理并存储事件信息。 本教程将创建事件中心，然后让呼叫事件生成器应用将呼叫数据发送至该事件中心。 有关事件中心的详细信息，请参阅 [Azure 服务总线文档](https://docs.microsoft.com/azure/service-bus/)。

>[!NOTE]
>有关此过程的更详细版本，请参阅[使用 Azure 门户创建事件中心命名空间和事件中心](../event-hubs/event-hubs-create.md)。 

### <a name="create-a-namespace-and-event-hub"></a>创建命名空间和事件中心
在此过程中，首先创建事件中心命名空间，然后将事件中心添加到该命名空间。 事件中心命名空间用于逻辑分组相关的事件总线实例。 

1. 登录 Azure 门户，然后依次单击“创建资源” > “物联网” > “事件中心”。 

2. 在“创建命名空间”窗格中，输入命名空间名称，例如 `<yourname>-eh-ns-demo`。 可以对命名空间使用任何名称，但该名称必须对 URL 有效，并且在 Azure 中必须唯一。 
    
3. 选择订阅并创建或选择一个资源组，然后单击“创建”。

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-namespace-new-portal.png" alt="Create event hub namespace in Azure portal" width="300px"/>

4. 完成部署命名空间后，在 Azure 资源列表中找到事件中心命名空间。 

5. 单击新的命名空间，然后在“命名空间”窗格中，单击“事件中心”。

   ![用于创建新事件中心的“添加事件中心”按钮](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-button-new-portal.png)    
 
6. 将新事件中心命名为 `asa-eh-frauddetection-demo`。 可以使用其他名称。 如果使用其他名称，请记下该名称，稍后会用到。 不需要立即为事件中心设置任何其他选项。

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-new-portal.png" alt="Name event hub in Azure portal" width="400px"/>
    
 
7. 单击“创建”。

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>授予对事件中心的访问权限，并获取连接字符串

在进程可以将数据发送到事件中心之前，事件中心必须具有允许适当访问的策略。 访问策略生成包含授权信息的连接字符串。

1.  在“事件命名空间”窗格中，单击“事件中心”，然后单击新事件中心的名称。

2.  在“事件中心”窗格中，单击“共享访问策略”，然后单击“+&nbsp;添加”。

    >[!NOTE]
    >确保使用的是事件中心，而不是事件中心命名空间。

3.  添加名为 `sa-policy-manage-demo` 的策略并对“声明”选择“管理”。

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-shared-access-policy-manage-new-portal.png" alt="Create shared access policy for Stream Analytics" width="300px"/>
 
4.  单击“创建”。

5.  部署策略后，在共享访问策略列表中单击该策略。

6.  找到标记为“连接字符串 - 主键”的框，然后单击连接字符串旁边的“复制”按钮。 

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-shared-access-policy-copy-connection-string-new-portal.png" alt="Stream Analytics shared access policy" width="300px"/>
 
7.  将连接字符串粘贴到文本编辑器中。 对此连接字符串稍加编辑，以便在下一部分中使用。

    连接字符串如下所示：

        Endpoint=sb://YOURNAME-eh-ns-demo.servicebus.windows.net/;SharedAccessKeyName=asa-policy-manage-demo;SharedAccessKey=Gw2NFZwU1Di+rxA2T+6hJYAtFExKRXaC2oSQa0ZsPkI=;EntityPath=asa-eh-frauddetection-demo

    请注意，连接字符串包含多个键值对，用分号分隔：`Endpoint`、`SharedAccessKeyName`、`SharedAccessKey` 和 `EntityPath`。  

## <a name="configure-and-start-the-event-generator-application"></a>配置并启动事件生成器应用程序

在启动 TelcoGenerator 应用之前，必须对该应用进行配置，使其能够向创建的事件中心发送呼叫记录。

### <a name="configure-the-telcogenerator-app"></a>配置 TelcoGenerator 应用

1. 在复制连接字符串的编辑器中，记下 `EntityPath` 值，然后删除 `EntityPath` 对（不要忘了删除它前面的分号）。 

2. 在解压缩 TelcoGenerator.zip 文件的文件夹中，在编辑器中打开 telcodatagen.exe.config 文件。 （该文件夹中有多个 .config 文件，因此请确保打开正确的文件。）

3. 在 `<appSettings>` 元素中：

   * 将 `EventHubName` 键的值设置为事件中心名称（即实体路径的值）。
   * 将 `Microsoft.ServiceBus.ConnectionString` 键的值设置为连接字符串。 

   `<appSettings>` 部分与以下示例类似。 （为清楚起见，包装这些行，并从授权令牌中删除一些字符。）

   ![TelcoGenerator 配置文件显示事件中心名称和连接字符串](./media/stream-analytics-real-time-fraud-detection/stream-analytics-telcogenerator-config-file-app-settings.png)
 
4. 保存该文件。 

### <a name="start-the-app"></a>启动应用
1.  打开命令窗口，然后切换到解压缩 TelcoGenerator 应用的文件夹。

2.  输入以下命令：

   ```cmd
   telcodatagen.exe 1000 0.2 2
   ```

   参数包括： 

   * 每小时的 CDR 数。 
   * SIM 卡欺诈概率：应用模拟欺诈呼叫的频率（占所有呼叫的百分比）。 值 0.2 表示大约有 20% 的通话记录似乎具有欺诈性。
   * 持续时间（以小时为单位）。 应用应运行的小时数。 还可以通过在命令行按 Ctrl+C 来随时停止该应用。

   几秒钟后，当应用将电话通话记录发送到事件中心时，应用将开始在屏幕上显示通话记录。

将在此实时欺诈检测应用程序中使用的某些关键字段如下所示：

|**记录**|**定义**|
|----------|--------------|
|`CallrecTime`|呼叫开始时间的时间戳。 |
|`SwitchNum`|用于连接呼叫的电话交换机。 在此示例中，交换机是表示来源国家/地区（美国、中国、英国、德国或澳大利亚）的字符串。 |
|`CallingNum`|呼叫方的电话号码。 |
|`CallingIMSI`|国际移动用户标识 (IMSI)。 这是呼叫方的唯一标识符。 |
|`CalledNum`|呼叫接收人的电话号码。 |
|`CalledIMSI`|国际移动用户标识 (IMSI)。 这是呼叫接收人的唯一标识符。 |


## <a name="create-a-stream-analytics-job-to-manage-streaming-data"></a>创建流分析作业来管理流数据

现在已拥有呼叫事件的流，可以设置一个流分析作业。 该作业将从设置的事件中心读取数据。 

### <a name="create-the-job"></a>创建作业 

1. 在 Azure 门户中，单击“创建资源” > “物联网” > “流分析作业”。

2. 将作业命名为 `asa_frauddetection_job_demo`，然后指定订阅、资源组和位置。

    为获得最佳性能，最好将作业和事件中心放置在同一区域，这样就不需要在不同区域之间传输数据。

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-job-new-portal.png" alt="Create Stream Analytics job in portal" width="300px"/>

3. 单击“创建”。

    创建作业后，门户就会显示作业详细信息。 尽管尚无任何应用正在运行，但必须先配置该作业，然后才能启动。

### <a name="configure-job-input"></a>配置作业输入

1. 在仪表板或“所有资源”窗格中，查找并选择 `asa_frauddetection_job_demo` 流分析作业。 
2. 在“流分析作业”窗格的“概述”部分，单击“输入”框。

   ![“流分析作业”窗格的“拓扑”下的“输入”框](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-input-box-new-portal.png)
 
3. 单击“添加流输入”并选择“事件中心”。 然后使用以下信息填充“新建输入”页：

   |**设置**  |**建议的值**  |**说明**  |
   |---------|---------|---------|
   |输入别名  |  CallStream   |  输入一个名称，用于标识作业的输入。   |
   |订阅   |  用户的订阅\<\> |  选择包含创建的事件中心的 Azure 订阅。   |
   |事件中心命名空间  |  asa-eh-ns-demo |  输入事件中心命名空间的名称。   |
   |事件中心名称  | asa-eh-frauddetection-demo | 选择事件中心的名称。   |
   |事件中心策略名称  | asa-policy-manage-demo | 选择之前创建的访问策略。   |

    </br>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-input-new-portal.png" alt="Create Stream Analytics input in portal" width="300px"/>


4. 单击“创建”。

## <a name="create-queries-to-transform-real-time-data"></a>创建查询以转换实时数据

此时，设置一个流分析作业以读取传入数据流。 接下来创建一个分析实时数据的查询。 流分析支持简单的声明性查询模型，用于描述实时处理的转换。 这些查询使用类似 SQL 的语言，该语言具有特定于流分析的一些扩展。 

简单的查询可能只会读取所有传入的数据。 但通常需要创建查找特定数据或数据关系的查询。 本教程的这一部分会创建并测试多个查询，展示可以转换输入流以便进行分析的几种方法。 

此处创建的查询只会在屏幕中显示已转换的数据。 后面的部分将配置一个输出接收器和将转换的数据写入该接收器的查询。

若要了解有关语言的详细信息，请参阅 [Azure 流分析查询语言参考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)。

### <a name="get-sample-data-for-testing-queries"></a>获取用于测试查询的示例数据

TelcoGenerator 应用正在将呼叫记录发送到事件中心，流分析作业已配置为从事件中心读取记录。 可以使用查询测试作业以确保它正确读取。 若要在 Azure 控制台中测试查询，则需要提供示例数据。 本演练将从要进入事件中心的流中提取示例数据。

1. 请确保 TelcoGenerator 应用正在运行，并且将生成呼叫记录。
2. 在门户中，返回到“流分析作业”窗格。 （如果关闭了此窗格，请在“所有资源”窗格中搜索 `asa_frauddetection_job_demo`。）
3. 单击“查询”框。 Azure 会列出为作业配置的输入和输出，并允许创建查询，以便在将输入流发送到输出时对其进行转换。
4. 在“查询”窗格中，单击 `CallStream` 输入旁边的点，然后选择“来自输入的示例数据”。

   ![对流分析作业条目使用示例数据的菜单选项，其中选择了“来自输入的示例数据”](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sample-data-from-input.png)


5. 将“分钟”设置为 3，然后单击“确定”。 
    
   ![用于对输入流进行采样的选项，其中选择了“3 分钟”](./media/stream-analytics-real-time-fraud-detection/stream-analytics-input-create-sample-data.png)

    Azure 会从输入流中进行 3 分钟的数据采样，并在示例数据准备就绪时发出通知。 （这用不了多长时间。） 

查询窗口打开时，示例数据会临时存储并可供使用。 如果关闭查询窗口，示例数据将被丢弃，必须创建一组新的示例数据。 

或者，也可以[从 GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json) 获取其中具有示例数据的 .json 文件，然后上传该 .json 文件以用作 `CallStream` 输入的示例数据。 

### <a name="test-using-a-pass-through-query"></a>使用传递查询进行测试

如果想要将每个事件存档，可使用传递查询读取事件负载中的所有字段。

1. 在查询窗口中输入以下查询：
        
   ```SQL
   SELECT 
       *
   FROM 
       CallStream
   ```

    >[!NOTE]
    >对于 SQL，关键字不区分大小写，空格也不重要。

    在此查询中，`CallStream` 是创建输入时指定的别名。 如果使用了其他别名，请改为使用该名称。

2. 单击“测试”。

    流分析作业对示例数据运行查询，并在窗口底部显示输出。 这些结果指示已正确配置事件中心和流分析作业。 （如前所述，稍后需要创建查询可以向其写入数据的输出接收器。）

   ![流分析作业输出，其中显示生成的 73 条记录](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output.png)

    你看到的确切记录数取决于你在 3 分钟采样中捕获的记录数。
 
### <a name="reduce-the-number-of-fields-using-a-column-projection"></a>减少使用列投影的字段数

在许多情况下，分析并不需要输入流中的所有列。 可以使用查询投影一组返回的字段，这些字段比传递查询中的字段要小。

1. 在代码编辑器中将查询更改为以下内容：

   ```SQL
   SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum 
   FROM 
       CallStream
   ```

2. 再次单击“测试”。 

   ![用于投影的流分析作业输出显示 25 条记录](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-projection.png)
 
### <a name="count-incoming-calls-by-region-tumbling-window-with-aggregation"></a>按区域计算传入呼叫数：带聚合功能的翻转窗口

假设要计算每个区域的传入呼叫数。 在流数据中，当要执行聚合函数（如计数）时，需要将流划分为临时单位（因为数据流本身实际上是无限的）。 使用流分析[开窗函数](stream-analytics-window-functions.md)执行此操作。 然后，可以使用该窗口中的数据作为一个单元。

此转换需要一个不重叠的时间窗口序列，每个窗口将具有一组可对其进行分组和聚合的离散数据。 这种类型的窗口称为“翻转窗口”。 在翻转窗口中，可以获得按 `SwitchNum`（它表示发起呼叫的国家/地区）分组的传入呼叫的计数。 

1. 在代码编辑器中将查询更改为以下内容：

        ```SQL
        SELECT 
            System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount 
        FROM
            CallStream TIMESTAMP BY CallRecTime 
        GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum
        ```

    此查询在 `FROM` 子句中使用 `Timestamp By` 关键字来指定输入流中要用于定义翻转窗口的时间戳字段。 在这种情况下，窗口按每条记录中的 `CallRecTime` 字段将数据划分为段。 （如果未指定任何字段，开窗操作将使用每个事件到达事件中心的时间。 请参阅[流分析查询语言参考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)中的“到达时间与应用程序时间”。 

    投影包括 `System.Timestamp`，后者将返回每个窗口结束时的时间戳。 

    若要指定想要使用翻转窗口，请在 `GROUP BY` 子句中使用 [TUMBLINGWINDOW](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics) 函数。 在函数中，可以指定时间单位（从微秒到一天的任意时间）和窗口大小（单位数）。 在此示例中，翻转窗口由 5 秒时间间隔组成，因此你会收到按国家/地区的每隔 5 秒的呼叫计数。

2. 再次单击“测试”。 在结果中，请注意“WindowEnd”下的时间戳以 5 秒为增量。

   ![用于聚合的流分析作业输出，其中显示 13 条记录](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-aggregation.png)
 
### <a name="detect-sim-fraud-using-a-self-join"></a>使用自联接检测 SIM 欺诈

在此示例中，将欺诈使用情况视为来自同一用户的呼叫，但与另一个 5 秒内的呼叫位于不同的位置。 例如，同一用户不能合法地同时从美国和澳大利亚发起呼叫。 

若要检查这些情况，可以使用流数据的自联接基于 `CallRecTime` 值将流联接到自身。 然后，可以查找 `CallingIMSI` 值（始发号码）相同，但 `SwitchNum` 值（来源国家/地区）不同的呼叫记录。

当对流数据使用联接时，该联接必须对可以及时分隔匹配行的程度施加一定限制。 （如前所述，流数据实际上是无限的。）使用 `DATEDIFF` 函数在联接的 `ON` 子句中指定关系的时间限制。 此示例中联接基于调用数据的 5 秒时间间隔。

1. 在代码编辑器中将查询更改为以下内容： 

        ```SQL
        SELECT  System.Timestamp as Time, 
            CS1.CallingIMSI, 
            CS1.CallingNum as CallingNum1, 
            CS2.CallingNum as CallingNum2, 
            CS1.SwitchNum as Switch1, 
            CS2.SwitchNum as Switch2 
        FROM CallStream CS1 TIMESTAMP BY CallRecTime 
            JOIN CallStream CS2 TIMESTAMP BY CallRecTime 
            ON CS1.CallingIMSI = CS2.CallingIMSI 
            AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5 
        WHERE CS1.SwitchNum != CS2.SwitchNum
        ```

    此查询类似任何 SQL 联接，除联接中的 `DATEDIFF` 函数以外。 此 `DATEDIFF` 版本特定于流分析，它必须显示在 `ON...BETWEEN` 子句中。 参数为时间单位（此示例中为秒）和联接的两个源的别名。 这与标准 SQL `DATEDIFF` 函数不同。

    `WHERE` 子句包含标志欺诈呼叫的条件：始发交换机不同。 

2. 再次单击“测试”。 

   ![用于自联接的流分析作业输出，其中显示生成的 6 条记录](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-self-join.png)

3. 单击“保存”以将自联接查询保存为流分析作业的一部分。 （不会保存示例数据。）

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-query-editor-save-button-new-portal.png" alt="Save Stream Analytics query in portal" width="300px"/>

## <a name="create-an-output-sink-to-store-transformed-data"></a>创建输出接收器以存储转换后的数据

现已定义事件流、用于引入事件的事件中心输入，以及用于通过流执行转换的查询。 最后一步是定义作业的输出接收器，即转换后的流要写入的位置。 

可以使用许多资源作为输出接收器：SQL Server 数据库、表存储、Data Lake 存储、Power BI，甚至另一个事件中心。 本教程将流写入 Azure Blob 存储，该存储是收集事件信息供后续分析的典型选择，因为它可以包括非结构化数据。

如果已有 Blob 存储帐户，则可以使用该帐户。 在本教程中，你将了解如何创建新的存储帐户。

### <a name="create-an-azure-blob-storage-account"></a>创建 Azure Blob 存储帐户

1. 从 Azure 门户的左上角选择“创建资源” > “存储” > “存储帐户”。 填充“存储帐户作业”页，将“名称”设置为“asaehstorage”，将“位置”设置为“美国东部”，将“资源组”设置为“asa-eh-ns-rg”（请将相同资源组中的存储帐户托管为流式处理作业，以便提高性能）。 余下设置可以保留默认值。  

   ![在 Azure 门户中创建存储帐户](./media/stream-analytics-real-time-fraud-detection/stream-analytics-storage-account-create.png)

2. 在 Azure 门户中，返回到“流分析作业”窗格。 （如果关闭了此窗格，请在“所有资源”窗格中搜索 `asa_frauddetection_job_demo`。）

3. 在“作业拓扑”部分，单击“输出”框。

4. 在“输出”窗格中，单击“添加”，然后选择“BIob 存储”。 使用以下信息填写“新建输出”页：

   |**设置**  |**建议的值**  |**说明**  |
   |---------|---------|---------|
   |输出别名  |  CallStream-FraudulentCalls   |  输入一个名称，用于标识作业的输出。   |
   |订阅   |  用户的订阅\<\> |  选择包含已创建的存储帐户的 Azure 订阅。 存储帐户可以在同一订阅中，也可以在另一订阅中。 此示例假定已在同一订阅中创建存储帐户。 |
   |存储帐户  |  asaehstorage |  输入创建的存储帐户的名称。 |
   |容器  | asa-fraudulentcalls-demo | 选择“创建新名称”并输入容器名称。 |

    <br/>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-output-blob-storage-new-console.png" alt="Create blob output for Stream Analytics job" width="300px"/>
    
5. 单击“保存”。 


## <a name="start-the-streaming-analytics-job"></a>启动流分析作业

现已配置作业。 你已指定输入（事件中心）、转换（查找欺诈呼叫的查询）和输出（Blob 存储）。 现在可以启动作业。 

1. 请确保 TelcoGenerator 应用正在运行。

2. 在“作业”窗格中，单击“启动”。 在“启动作业”窗格中，为作业输出开始时间选择“现在”。 

   ![启动流分析作业](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-start.png)



## <a name="examine-the-transformed-data"></a>检查转换后的数据

现已完成流分析作业。 作业是检查电话呼叫元数据流、实时查找欺诈电话呼叫以及将这些欺诈呼叫的相关信息写入存储。 

若要完成本教程，建议查看流分析作业捕获的数据。 该数据正在以区块（文件）的形式写入 Azure Blog 存储。 可以使用任何可读取 Azure Blob 存储的工具。 如“先决条件”部分中所述，可以在 Visual Studio 中使用 Azure 扩展，或使用如 [Azure 存储资源管理器](https://storageexplorer.com/)或 [Cerulean](https://www.cerebrata.com/products/cerulean/features/azure-storage) 之类的工具。 

当检查 blob 存储中的文件内容时，将看到如下所示的内容：

   ![Azure blob 存储与流分析输出](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-blob-storage-view.png)
 

## <a name="clean-up-resources"></a>清理资源

提供继续介绍欺诈检测方案以及使用已在本教程中创建的资源的其他文章。 如果想要继续，请参阅“后续步骤”下的建议。

但是，如果你已完成，并且不需要已创建的资源，则可以删除它们，以免产生不必要的 Azure 费用。 在这种情况下，建议执行以下操作：

1. 停止流分析作业。 在“作业”窗格中，单击顶部的“停止”。
2. 停止 Telco Generator 应用。 在启动应用的命令窗口中，按 Ctrl+C。
3. 如果为本教程创建了新的 Blob 存储帐户，请将其删除。 
4. 删除流分析作业。
5. 删除事件中心。
6. 删除事件中心命名空间。

## <a name="get-support"></a>获取支持

如需进一步的帮助，请尝试参考 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>后续步骤

可以继续阅读本教程的以下文章：

* [流分析和 Power BI：针对流数据的实时分析仪表板](stream-analytics-power-bi-dashboard.md)。 本文介绍如何将流分析作业的 TelCo 输出发送到 Power BI 以进行实时可视化和分析。

有关常规流分析的详细信息，请查看以下文章：

* [Azure 流分析简介](stream-analytics-introduction.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
