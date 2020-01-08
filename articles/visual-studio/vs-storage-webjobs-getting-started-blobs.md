---
title: 开始使用 Blob 存储和 Visual Studio 连接服务（WebJob 项目）| Microsoft Docs
description: 在使用 Visual Studio 连接服务连接到 Azure 存储后，如何开始使用 WebJob 项目中的 Blob 存储
services: storage
author: ghogen
manager: jillfra
ms.assetid: 324c9376-0225-4092-9825-5d1bd5550058
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 1e951fde7e47ccfcce5f64db4ef27ac767d63480
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2019
ms.locfileid: "69510657"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-webjob-projects"></a>开始使用 Azure Blob 存储和 Visual Studio 连接服务（WebJob 项目）
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>概述
本文章提供 C# 代码示例，用于演示如何在创建或更新 Azure blob 后触发进程。 这些代码示例使用 [WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) 版本 1.x。 使用 Visual Studio 的“添加连接服务”对话框将存储帐户添加到 WebJob 项目中时，会安装相应的 Azure 存储 NuGet 包，将相应的 .NET 引用添加到项目中，并会在 App.config 文件中更新存储帐户的连接字符串。

## <a name="how-to-trigger-a-function-when-a-blob-is-created-or-updated"></a>如何在创建或更新 Blob 后触发函数
本部分说明如何使用 **BlobTrigger** 属性。

 **注意：** WebJobs SDK 会扫描日志文件，以观察新的或更改的 blob。 此过程非常缓慢；创建 blob 之后数分钟或更长时间内可能仍不会触发函数。  如果应用程序需要立即处理 blob，建议的方法是在创建该 blob 时创建队列消息，并在处理该 blob 的函数上使用 **QueueTrigger** 属性而非 **BlobTrigger** 属性。

### <a name="single-placeholder-for-blob-name-with-extension"></a>Blob 名称和扩展名的单个占位符
以下代码示例将 *input* 容器中显示的文本 blob 复制到 *output* 容器中：

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("output/{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

属性构造函数采用指定容器名称的字符串参数和 Blob 名称的占位符。 在此示例中，如果在 *input* 容器中创建了名为 *Blob1.txt* 的 Blob，则该函数会在 *output* 容器中创建名为 *Blob1.txt* 的 Blob。

可以指定包含 Blob 名称占位符的名称模式，如以下代码示例中所示：

        public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
            [Blob("output/copy-{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

此代码只会复制名称以“original-”开头的 Blob。 例如，将 *input* 容器中的 *original-Blob1.txt* 复制到 *output* 容器中的 *copy-Blob1.txt*。

如果需要为名称中包含大括号的 Blob 名称指定名称模式，则使用双倍的大括号。 例如，如果想要在 *images* 容器中查找其名称类似以下内容的 blob：

        {20140101}-soundfile.mp3

为模式使用以下代码：

        images/{{20140101}}-{name}

在示例中，*name* 占位符值是 *soundfile.mp3*。

### <a name="separate-blob-name-and-extension-placeholders"></a>单独的 Blob 名称和扩展名占位符
以下代码示例在将 *input* 容器中显示的 blob 复制到 *output* 容器中时更改文件扩展名。 该代码将记录 *input* blob 的扩展名，并将 *output* blob 的扩展名设置为 *.txt*。

        public static void CopyBlobToTxtFile([BlobTrigger("input/{name}.{ext}")] TextReader input,
            [Blob("output/{name}.txt")] out string output,
            string name,
            string ext,
            TextWriter logger)
        {
            logger.WriteLine("Blob name:" + name);
            logger.WriteLine("Blob extension:" + ext);
            output = input.ReadToEnd();
        }

## <a name="types-that-you-can-bind-to-blobs"></a>可绑定到 Blob 的类型
可对以下类型使用 **BlobTrigger** 属性：

* **string**
* **TextReader**
* **Stream**
* **ICloudBlob**
* **CloudBlockBlob**
* **CloudPageBlob**
* 由 [ICloudBlobStreamBinder](#getting-serialized-blob-content-by-using-icloudblobstreambinder) 反序列化的其他类型

如果想要直接使用 Azure 存储帐户，则还可以向方法签名添加 **CloudStorageAccount** 参数。

## <a name="getting-text-blob-content-by-binding-to-string"></a>通过绑定到字符串获取文本 Blob 内容
如果需要文本 blob，可将 **BlobTrigger** 应用于 **string** 参数。 以下代码示例将文本 blob 绑定到名为 **logMessage** 的 **string** 参数。 函数使用该参数将 Blob 的内容写入 WebJobs SDK 仪表板。

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name,
            TextWriter logger)
        {
             logger.WriteLine("Blob name: {0}", name);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }

## <a name="getting-serialized-blob-content-by-using-icloudblobstreambinder"></a>使用 ICloudBlobStreamBinder 获取序列化 Blob 内容
以下代码示例使用实现 **ICloudBlobStreamBinder** 的类来启用 **BlobTrigger** 属性，将 blob 绑定到 **WebImage** 类型。

        public static void WaterMark(
            [BlobTrigger("images3/{name}")] WebImage input,
            [Blob("images3-watermarked/{name}")] out WebImage output)
        {
            output = input.AddTextWatermark("WebJobs SDK",
                horizontalAlign: "Center", verticalAlign: "Middle",
                fontSize: 48, opacity: 50);
        }
        public static void Resize(
            [BlobTrigger("images3-watermarked/{name}")] WebImage input,
            [Blob("images3-resized/{name}")] out WebImage output)
        {
            var width = 180;
            var height = Convert.ToInt32(input.Height * 180 / input.Width);
            output = input.Resize(width, height);
        }

**WebImage** 绑定代码在 **WebImageBinder** 类中提供，该类派生自 **ICloudBlobStreamBinder**。

        public class WebImageBinder : ICloudBlobStreamBinder<WebImage>
        {
            public Task<WebImage> ReadFromStreamAsync(Stream input,
                System.Threading.CancellationToken cancellationToken)
            {
                return Task.FromResult<WebImage>(new WebImage(input));
            }
            public Task WriteToStreamAsync(WebImage value, Stream output,
                System.Threading.CancellationToken cancellationToken)
            {
                var bytes = value.GetBytes();
                return output.WriteAsync(bytes, 0, bytes.Length, cancellationToken);
            }
        }

## <a name="how-to-handle-poison-blobs"></a>如何处理有害 Blob
当 **BlobTrigger** 函数失败时，如果失败是暂时性错误导致的，则 SDK 会再次调用该函数。 如果失败是由 Blob 的内容导致的，则该函数每次尝试处理 Blob 时都会失败。 默认情况下，对于给定的 Blob，SDK 调用一个函数最多 5 次。 如果第五次尝试失败，SDK 会将消息添加到名为 webjobs-blobtrigger-poison 的队列中。

最大尝试次数是可配置的。 处理有害 blob 和有害队列消息时使用相同的 **MaxDequeueCount** 设置。

有害 Blob 的队列消息是包含以下属性的 JSON 对象：

* FunctionId (格式为 *{WebJob 名称}* 。函数. *{Function name}* , 例如:WebJob1.Functions.CopyBlob)
* BlobType（"BlockBlob" 或 "PageBlob"）
* ContainerName
* BlobName
* ETag（blob 版本标识符，例如："0x8D1DC6E70A277EF"）

在下面的代码示例中，**CopyBlob** 函数的代码导致它每次调用时都失败。 SDK 进行最大重试次数的调用之后，有害 blob 队列中会创建消息，该消息通过 **LogPoisonBlob** 函数进行处理。

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("textblobs/output-{name}")] out string output)
        {
            throw new Exception("Exception for testing poison blob handling");
            output = input.ReadToEnd();
        }

        public static void LogPoisonBlob(
        [QueueTrigger("webjobs-blobtrigger-poison")] PoisonBlobMessage message,
            TextWriter logger)
        {
            logger.WriteLine("FunctionId: {0}", message.FunctionId);
            logger.WriteLine("BlobType: {0}", message.BlobType);
            logger.WriteLine("ContainerName: {0}", message.ContainerName);
            logger.WriteLine("BlobName: {0}", message.BlobName);
            logger.WriteLine("ETag: {0}", message.ETag);
        }

SDK 自动反序列化 JSON 消息。 下面是 **PoisonBlobMessage** 类：

        public class PoisonBlobMessage
        {
            public string FunctionId { get; set; }
            public string BlobType { get; set; }
            public string ContainerName { get; set; }
            public string BlobName { get; set; }
            public string ETag { get; set; }
        }

### <a name="blob-polling-algorithm"></a>Blob 轮询算法
启动应用程序时，WebJobs SDK 将扫描 **BlobTrigger** 属性指定的所有容器。 在大型存储帐户中，此扫描可能需要一些时间，因此在查找新 blob 和执行 **BlobTrigger** 函数之前，可能需要一段时间。

若要在应用程序启动后检测新的或已更改的 Blob，SDK 会定期读取从 Blob 存储日志。 blob 日志将进行缓冲，仅每隔 10 分钟左右获取物理写入，因此创建或更新 blob 后可能存在很长的延迟，才会执行对应的 **BlobTrigger** 函数。

使用 **Blob** 属性创建的 blob 例外。 当 WebJobs SDK 创建新 blob 时，会立即将新的 blob 传递给任何匹配的 **BlobTrigger** 函数。 因此，如果建立了 Blob 输入和输出的链接，则 SDK 可以高效地处理它们。 但是，如果想要对通过其他方式创建或更新的 blob 降低运行 blob 处理功能的延迟时间，建议使用 **QueueTrigger** 而非 **BlobTrigger**。

### <a name="blob-receipts"></a>Blob 回执
WebJobs SDK 确保没有为相同的新 blob 或更新 blob 多次调用 **BlobTrigger** 函数。 为此，它会维护 *blob 回执*，以确定是否已处理给定的 blob 版本。

Blob 回执存储在 AzureWebJobsStorage 连接字符串指定的 Azure 存储帐户中名为 *azure-webjobs-hosts* 的容器中。 Blob 回执包含以下信息：

* 为 blob 调用的函数 (" *{WebJob 名称}* "。函数. *{Function name}* ", 例如:".Functions. CopyBlob")
* 容器名称
* Blob 类型（"BlockBlob" 或 "PageBlob"）
* Blob 名称
* ETag（blob 版本标识符，例如："0x8D1DC6E70A277EF"）

如果要强制重新处理某个 blob，则可以从 *azure-webjobs-hosts* 容器中手动删除该 blob 的 blob 回执。

## <a name="related-topics-covered-by-the-queues-article"></a>队列文章涵盖的相关主题
有关如何处理队列消息触发的 blob 处理，或者不特定于 blob 处理的 WebJobs SDK 方案的信息，请参阅[如何通过 WebJobs SDK 使用 Azure 队列存储](https://github.com/Azure/azure-webjobs-sdk/wiki)。

该文章涵盖的相关主题包括：

* 异步函数
* 多个实例
* 正常关闭
* 在函数正文中使用 WebJobs SDK 属性
* 在代码中设置 SDK 连接字符串。
* 在代码中设置 WebJobs SDK 构造函数参数的值
* 配置用于有害 blob 处理的 **MaxDequeueCount**。
* 手动触发函数
* 写入日志

## <a name="next-steps"></a>后续步骤
本文提供了代码示例，演示如何处理用于操作 Azure blob 的常见方案。 有关如何使用 Azure WebJobs 和 WebJobs SDK 的详细信息，请参阅 [Azure WebJobs 文档资源](https://go.microsoft.com/fwlink/?linkid=390226)。

