---
title: 开始使用 blob 存储和 Visual Studio 连接服务（云服务）| Microsoft Docs
description: 在使用 Visual Studio 连接服务连接到存储帐户后，如何开始在 Visual Studio 的云服务项目中使用 Azure Blob 存储
services: storage
author: ghogen
manager: jillfra
ms.assetid: 1144a958-f75a-4466-bb21-320b7ae8f304
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 56aceb4c782c15f69c7994df787b4b950523e8b5
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2019
ms.locfileid: "69510706"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-cloud-services-projects"></a>开始使用 Azure Blob 存储和 Visual Studio 连接服务（云服务项目）
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>概述
本文介绍通过使用 Visual Studio 中的“添加连接服务”对话框在 Visual Studio 云服务项目中创建或引用 Azure 存储帐户之后，如何开始使用 Azure blob 存储。 将介绍如何访问和创建 blob 容器以及如何执行常见任务（如上传、列出和下载 blob）。 示例是使用 C\# 编写的并使用了[适用于 .NET 的 Microsoft Azure 存储客户端库](https://msdn.microsoft.com/library/azure/dn261237.aspx)。

Azure Blob 存储是一项可存储大量非结构化数据的服务，用户可在世界任何地方通过 HTTP 或 HTTPS 访问这些数据。 单个 Blob 可以是任意大小。 Blob 可以是图像、音频和视频文件、原始数据以及文档文件等。

正如文件位于文件夹中一样，存储 Blob 位于容器中。 创建存储后，可以在存储中创建一个或多个容器。 例如，在名为“Scrapbook”的存储中，可以在名为“images”的存储中创建容器，用于存储图片，还可以在名为“audio”的存储中创建另一个容器，用于存储音频文件。 创建这些容器后，可以向它们上传单独的 Blob 文件。

* 有关以编程方式操作 Blob 的详细信息，请参阅 [Get started with Azure Blob storage using .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md)（通过 .NET 开始使用 Azure Blob 存储）。
* 有关 Azure 存储的常规信息，请参阅[存储文档](https://azure.microsoft.com/documentation/services/storage/)。
* 有关 Azure 云服务的常规信息，请参阅[云服务文档](https://azure.microsoft.com/documentation/services/cloud-services/)。
* 有关编写 ASP.NET 应用程序的详细信息，请参阅 [ASP.NET](https://www.asp.net)。

## <a name="access-blob-containers-in-code"></a>使用代码访问 blob 容器
若要以编程方式访问云服务项目中的 Blob，需要添加以下项（如果尚未存在）。

1. 在希望以编程方式访问 Azure 存储的任何 C# 文件中，将以下代码命名空间声明添加到文件的顶部。
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;
2. 获取表示存储帐户信息的 **CloudStorageAccount** 对象。 使用下面的代码从 Azure 服务配置获取存储连接字符串和存储帐户信息。
   
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage account name>_AzureStorageConnectionString"));
3. 获取 **CloudBlobClient** 对象，以引用存储帐户中的现有容器。
   
        // Create a blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
4. 获取 **CloudBlobContainer** 对象，以引用特定 blob 容器。
   
        // Get a reference to a container named "mycontainer."
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

> [!NOTE]
> 在接下来的部分中，会在代码的前面使用先前过程中显示的全部代码。
> 
> 

## <a name="create-a-container-in-code"></a>使用代码创建容器
> [!NOTE]
> 在 ASP.NET 中执行 Azure 存储调用的一些 API 是异步的。 有关详细信息，请参阅[使用 Async 和 Await 进行异步编程](https://msdn.microsoft.com/library/hh191443.aspx)。 以下示例中的代码假定使用的是异步编程方法。
> 
> 

若要在存储帐户中创建容器，所需做的只是添加对 **CreateIfNotExistsAsync** 的调用，如以下代码所示：

    // If "mycontainer" doesn't exist, create it.
    await container.CreateIfNotExistsAsync();


如果要让容器中的文件可供所有人使用，则可以使用以下代码将容器设置为公共容器。

    await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });


Internet 中的所有人都可以查看公共容器中的 Blob，但是，仅在具有相应的访问密钥时，才能修改或删除它们。

## <a name="upload-a-blob-into-a-container"></a>将 Blob 上传到容器中
Azure 存储支持块 Blob 和页 Blob。 大多数情况下，推荐使用块 Blob。

要将文件上传到块 Blob，请获取容器引用，并使用它获取块 Blob 引用。 获取 Blob 引用后，可以通过调用 **UploadFromStream** 方法将任何数据流上传到该 Blob。 如果之前不存在 Blob，此操作将创建一个；如果存在 Blob，此操作将覆盖它。 下面的示例演示了如何将 Blob 上传到容器中，并假定已创建容器。

    // Retrieve a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## <a name="list-the-blobs-in-a-container"></a>列出容器中的 Blob
若要列出容器中的 Blob，首先需要获取容器引用。 然后，可以使用容器的 **ListBlobs** 方法来检索其中的 Blob 和/或目录。 要访问返回的 **IListBlobItem** 的丰富属性和方法，必须将它转换为 **CloudBlockBlob**、**CloudPageBlob** 或 **CloudBlobDirectory** 对象。 如果类型未知，可以使用类型检查来确定要将其转换为哪种类型。 以下代码演示了如何检索和输出 **photos** 容器中每项的 URI：

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, false))
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;

            Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);

        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob pageBlob = (CloudPageBlob)item;

            Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);

        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory directory = (CloudBlobDirectory)item;

            Console.WriteLine("Directory: {0}", directory.Uri);
        }
    }

如前一代码示例所示，还可将 Blob 服务视为容器中的目录。 这是为了让用户能够以更类似于文件夹的结构来组织 Blob。 例如，请考虑名为 **photos** 的容器中包含的下面一组块 Blob：

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

当对容器调用 **ListBlobs** 时（如前面的示例所示），返回的集合将包含 **CloudBlobDirectory** 和 **CloudBlockBlob** 对象，分别表示最高层中所含的目录和 Blob。 下面是生成的输出：

    Directory: https://<accountname>.blob.core.windows.net/photos/2010/
    Directory: https://<accountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


另外，也可以将 **ListBlobs** 方法的 **UseFlatBlobListing** 参数设置为 **true**。 这导致每个 Blob 都作为 **CloudBlockBlob** 返回，而无论目录如何。 下面是对 **ListBlobs** 的调用：

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

下面是结果：

    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
    Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
    Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
    Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
    Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
    Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

有关详细信息，请参阅 [CloudBlobContainer.ListBlobs](https://msdn.microsoft.com/library/azure/dd135734.aspx)。

## <a name="download-blobs"></a>下载 Blob
要下载 Blob，请首先检索 Blob 引用，然后调用 **DownloadToStream** 方法。 以下示例使用 **DownloadToStream** 方法将 Blob 内容传输到一个流对象，用户可以将该对象保存到本地文件。

    // Get a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

也可以使用 **DownloadToStream** 方法以文本字符串形式下载 Blob 的内容。

    // Get a reference to a blob named "myblob.txt"
    CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

    string text;
    using (var memoryStream = new MemoryStream())
    {
        blockBlob2.DownloadToStream(memoryStream);
        text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    }

## <a name="delete-blobs"></a>删除 Blob
要删除 Blob，首先要获取 Blob 引用，然后调用 **Delete** 方法。

    // Get a reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();


## <a name="list-blobs-in-pages-asynchronously"></a>以异步方式列出页中的 Blob
如果要列出大量 Blob，或需要控制一个列表操作中返回的结果数，则可以结果页的方式列出 Blob。 此示例显示如何以页面形式异步返回结果，这样就不会在等待返回大型结果集时阻止操作的执行。

此示例演示平面 Blob 列表，但也可以执行分层列表，只需将 **ListBlobsSegmentedAsync** 方法的 **useFlatBlobListing** 参数设置为 **false** 即可。

由于示例方法调用了一个异步方法，因此必须以 **async** 关键字开头，且必须返回 **Task** 对象。 为 **ListBlobsSegmentedAsync** 方法指定的 await 关键字将挂起示例方法的执行，直至列表任务完成。

    async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
    {
        // List blobs to the console window, with paging.
        Console.WriteLine("List blobs in pages:");

        int i = 0;
        BlobContinuationToken continuationToken = null;
        BlobResultSegment resultSegment = null;

        // Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
        // When the continuation token is null, the last page has been returned and execution can exit the loop.
        do
        {
            // This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
            // or by calling a different overload.
            resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);
            if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
            foreach (var blobItem in resultSegment.Results)
            {
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
            }
            Console.WriteLine();

            //Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;
        }
        while (continuationToken != null);
    }

## <a name="next-steps"></a>后续步骤
[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]

