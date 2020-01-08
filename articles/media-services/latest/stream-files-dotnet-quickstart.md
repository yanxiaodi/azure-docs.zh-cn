---
title: 使用 Azure 媒体服务流式传输视频文件 - .NET | Microsoft Docs
description: 按照本教程的步骤，创建新的 Azure 媒体服务帐户、编码文件并将文件流式传输到 Azure Media Player。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
keywords: Azure 媒体服务, 流式传输
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.custom: mvc
ms.date: 08/19/2019
ms.author: juliako
ms.openlocfilehash: 7f997865ba33a51c3e3aa7a4c7e990037be9e534
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69637334"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---net"></a>教程：基于 URL 对远程文件进行编码并流式传输视频 - .NET

本教程展示了使用 Azure 媒体服务在各种浏览器和设备上对视频进行编码和流式处理有多轻松。 可以使用 HTTPS、URL、SAS URL 或位于 Azure Blob 存储中的文件路径来指定输入内容。
本主题中的示例对可通过 HTTPS URL 访问的内容进行编码。 请注意，目前，AMS v3 不支持基于 HTTPS URL 的块传输编码。

完成本教程后即可对视频进行流式处理。  

![播放视频](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

- 如果没有安装 Visual Studio，可下载 [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15)。
- [创建媒体服务帐户](create-account-cli-how-to.md)。<br/>请务必记住用于资源组名称和媒体服务帐户名称的值。
- 遵循[使用 Azure CLI 访问 Azure 媒体服务 API](access-api-cli-how-to.md) 中的步骤并保存凭据。 需要使用这些凭据来访问 API。

## <a name="download-and-configure-the-sample"></a>下载并配置示例

使用以下命令将包含流式处理 .NET 示例的 GitHub 存储库克隆到计算机：  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts.git
 ```

该示例位于 [EncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/tree/master/AMSV3Quickstarts/EncodeAndStreamFiles) 文件夹。

打开下载的项目中的 [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/appsettings.json)。 将值替换为在[访问 API](access-api-cli-how-to.md) 中获取的凭据。

该示例执行以下操作：

1. 创建一个**转换**（首先，检查指定的转换是否存在）。 
2. 创建一个输出**资产**，用作编码**作业**的输出。
3. 创建基于 HTTPS URL 的**作业**输入。
4. 使用先前创建的输入和输出提交编码**作业**。
5. 检查作业的状态。
6. 创建**流式处理定位符**。
7. 生成流式处理 URL。

有关示例代码中的每个功能的内容介绍，请在[此源文件](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)中查看相关代码和注释。

## <a name="run-the-sample-app"></a>运行示例应用

运行应用时，显示使用不同协议播放视频的 URL。 

1. 按 Ctrl+F5 运行 EncodeAndStreamFiles 应用程序  。
2. 选择 Apple 的“HLS”协议（以 manifest(format=m3u8-aapl) 结束），并从控制台复制流式处理 URL   。

![输出](./media/stream-files-tutorial-with-api/output.png)

在此示例的[源代码](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)中，可查看 URL 的生成方式。 若要生成 URL，需要连接流式处理终结点的主机名和流式处理定位符路径。  

## <a name="test-with-azure-media-player"></a>使用 Azure Media Player 进行测试

本文使用 Azure Media Player 测试流式传输。 

> [!NOTE]
> 如果播放器在 Https 站点上进行托管，请确保将 URL 更新为“https”。

1. 打开 Web 浏览器并导航到 [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/)。
2. 在“URL:”框中，粘贴运行应用程序时获取的某个流式处理 URL 值  。 
 
     可以粘贴 HLS、Dash 或 Smooth 格式的 URL，Azure Media Player将切换到适当的流协议，以便在你的设备上自动播放。
3. 按“更新播放器”  。

Azure Media Player 可用于测试，但不可在生产环境中使用。 

## <a name="clean-up-resources"></a>清理资源

如果不再需要你的资源组中的任何一个资源（包括为本教程创建的媒体服务和存储帐户），请删除该资源组。

执行以下 CLI 命令：

```azurecli
az group delete --name amsResourceGroup
```

## <a name="examine-the-code"></a>检查代码

有关示例代码中的每个功能的内容介绍，请在[此源文件](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)中查看相关代码和注释。

如需了解更高级的流式处理示例及其详细说明，请参阅[上传、编码和流式处理文件](stream-files-tutorial-with-api.md)教程。 

### <a name="job-error-codes"></a>作业错误代码

请参阅[错误代码](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)。

## <a name="multithreading"></a>多线程处理

Azure 媒体服务 v3 SDK 不是线程安全的。 使用多线程应用程序时，应在每个线程上生成一个新的 AzureMediaServicesClient 对象。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：上传、编码和流式处理文件](stream-files-tutorial-with-api.md)
