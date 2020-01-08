---
title: 快速入门：使用适用于 Python 的异常探测器客户端库检测数据异常
titleSuffix: Azure Cognitive Services
description: 使用异常检测器 API 能够以批或流数据的形式检测数据系列的异常。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 09/17/2019
ms.author: aahi
ms.openlocfilehash: 320c690eb873f760af89b7514893f14ecc209323
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71106788"
---
# <a name="quickstart-anomaly-detector-client-library-for-nodejs"></a>快速入门：适用于 Node.js 的异常检测器客户端库

开始使用适用于 Node.js 的异常检测器客户端库。 请按照以下步骤安装程序包并试用基本任务的示例代码。 通过异常检测器服务，可以对时序数据自动使用最佳适配模型，从而查找器其中的异常，不限行业、场景或数据量。

使用适用于 Node.js 的异常检测器客户端库，可实现以下操作：

* 以批请求的形式检测整个时序数据集中的异常
* 在时序中检测最新数据点的异常状态

[参考文档](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/?view=azure-node-latest) | [库源代码](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) | [包 (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-anomalydetector) | [示例](https://github.com/Azure-Samples/anomalydetector)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/)
* 最新版本的 [Node.js](https://nodejs.org/)

## <a name="setting-up"></a>设置

### <a name="create-an-anomaly-detector-azure-resource"></a>创建异常检测器 Azure 资源

Azure 认知服务由你订阅的 Azure 资源表示。 使用 [Azure 门户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)或 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) 在本地计算机上创建用于异常检测器的资源。 还可以：

* 免费获取在 7 天内有效的[试用版密钥](https://azure.microsoft.com/try/cognitive-services/#decision)。 注册之后，它将在 [Azure 网站](https://azure.microsoft.com/try/cognitive-services/my-apis/)上提供。  
* 在 [Azure 门户](https://portal.azure.com/)上查看资源。

获取试用订阅或资源的密钥后，请为该密钥创建名为 `ANOMALY_DETECTOR_KEY` 的[环境变量](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)。 然后为 Azure 终结点创建一个名为 `ANOMALY_DETECTOR_ENDPOINT` 的环境变量。
 
### <a name="create-a-new-nodejs-application"></a>创建新的 Node.js 应用程序

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，为应用创建一个新目录并导航到该目录。 

```console
mkdir myapp && cd myapp
```

运行 `npm init` 命令以使用 `package.json` 文件创建一个 node 应用程序。 

```console
npm init
```

创建一个名为 `index.js` 的文件，并导入以下库：

[!code-javascript[Import statements](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=imports)]

为资源的 Azure 终结点和密钥创建变量。 如果在启动应用程序后创建了环境变量，则需要关闭再重新打开运行该应用程序的编辑器、IDE 或 shell 才能访问该变量。 为你将在后面的步骤中下载的示例数据文件创建另一个变量。 然后创建一个 ApiKeyCredentials 对象以包含该密钥。

[!code-javascript[Initial variables](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=vars)]

### <a name="install-the-client-library"></a>安装客户端库

安装 `ms-rest-azure` 和 `azure-cognitiveservices-anomalydetector` NPM 包。 此快速入门中也使用了 csv-parse 库：

```console
npm install  @azure/cognitiveservices-anomalydetector ms-rest-azure csv-parse
```

应用的 `package.json` 文件将使用依赖项进行更新。

## <a name="object-model"></a>对象模型

异常检测器客户端是 [AnomalyDetectorClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest) 对象，该对象使用你的密钥向 Azure 进行身份验证。 该客户端提供两种异常检测方法：对整个数据集使用 [entireDetect()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#entiredetect-request--servicecallback-entiredetectresponse--)，对最新数据点使用 [LastDetect()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#lastdetect-request--msrest-requestoptionsbase-)。 

时序数据作为 [Request](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/request?view=azure-node-latest) 对象中的一系列 [Point](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/point?view=azure-node-latest) 进行发送。 `Request` 对象包含描述数据的属性（例如[Granularity](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/request?view=azure-node-latest#granularity)）以及异常检测的参数。 

异常检测器响应是 [LastDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse?view=azure-node-latest) 或 [EntireDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse?view=azure-node-latest) 对象，具体取决于所使用的方法。 

## <a name="code-examples"></a>代码示例 

这些代码片段展示如何使用适用于 Node.js 的异常检测器客户端库执行以下操作：

* [对客户端进行身份验证](#authenticate-the-client)
* [从文件加载时序数据集](#load-time-series-data-from-a-file)
* [在整个数据集中检测异常](#detect-anomalies-in-the-entire-data-set) 
* [检测最新数据点的异常状态](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>验证客户端

> [!NOTE]
> 本快速入门假定你已为异常检测器密钥[创建了一个环境变量](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)，名为 `ANOMALY_DETECTOR_KEY`。

使用终结点和凭据实例化 [AnomalyDetectorClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest) 对象。

[!code-javascript[Authentication](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=authentication)]

## <a name="load-time-series-data-from-a-file"></a>从文件加载时序数据

从 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/AnomalyDetector/request-data.csv) 下载此快速入门中的示例数据：
1. 在浏览器中，右键单击“原始”  。
2. 单击“将链接另存为”  。
3. 将文件另存为 .csv 文件，保存到你的应用程序目录。

此时序数据的格式为 .csv 文件，它将被发送到异常检测器 API。

使用 csv-parse 库的 `readFileSync()` 方法读取数据文件，并使用 `parse()` 分析文件。 对于每一行，推送包含时间戳和数值的 [Point](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/point?view=azure-node-latest) 对象。

[!code-javascript[Read the data file](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=readFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>在整个数据集中检测异常 

调用 API 以使用客户端的 [entireDetect()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#entiredetect-request--msrest-requestoptionsbase-) 方法批量检测整个时间序列中的异常。 存储返回的 [EntireDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse?view=azure-node-latest) 对象。 循环访问响应的 `isAnomaly` 列表，并输出任何 `true` 值的索引。 如果找到任何此类值，这些值对应于异常数据点的索引。

[!code-javascript[Batch detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=batchCall)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>检测最新数据点的异常状态

调用异常检测器 API，以便使用客户端的 [lastDetect()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#lastdetect-request--msrest-requestoptionsbase-) 方法确定最新数据点是否异常，并存储返回的 [LastDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse?view=azure-node-latest) 对象。 响应的 `isAnomaly` 值是一个布尔值，用于指定该点的异常状态。  

[!code-javascript[Last point detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=lastDetection)]

## <a name="run-the-application"></a>运行应用程序

在快速入门文件中使用 `node` 命令运行应用程序。

```console
node index.js
```

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [门户](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
>[使用 Azure Databricks 流式处理异常检测](../tutorials/anomaly-detection-streaming-databricks.md)

* 什么是[异常检测器 API？](../overview.md)
* 使用异常检测器 API 时的[最佳做法](../concepts/anomaly-detection-best-practices.md)。
* 可以在 [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs) 上找到此示例的源代码。
