---
title: 使用媒体服务 .NET SDK 管理资产和相关的实体
description: 了解如何使用适用于 .NET 的媒体服务 SDK 管理资产和相关的实体。
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 1bd8fd42-7306-463d-bfe5-f642802f1906
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: a686465b0006c2e9aac6e06cb4ab12d30921e8c5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "61235419"
---
# <a name="managing-assets-and-related-entities-with-media-services-net-sdk"></a>使用媒体服务 .NET SDK 管理资产和相关的实体
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-manage-entities.md)
> * [REST](media-services-rest-manage-entities.md)
> 
> 

> [!NOTE]
> 不会向媒体服务 v2 添加任何新特性或新功能。 <br/>查看最新版本：[媒体服务 v3](https://docs.microsoft.com/azure/media-services/latest/)。 此外，请参阅[从 v2 到 v3 迁移指南](../latest/migrate-from-v2-to-v3.md)

本主题演示如何使用 .NET 管理 Azure 媒体服务实体。

自 2017 年 4 月 1 日起，即使记录总数低于最大配额，也会自动删除帐户中所有超过 90 天的作业记录，及其相关的任务记录。 例如，将于 2017 年 4 月 1 日自动删除帐户中 2016 年 12 月 31 日前的所有作业记录。 在需要时，可使用本主题中所述的代码存档作业/任务信息。

## <a name="prerequisites"></a>必备组件

设置开发环境，并根据[使用 .NET 进行媒体服务开发](media-services-dotnet-how-to-use.md)中所述，在 app.config 文件中填充连接信息。 

## <a name="get-an-asset-reference"></a>获取资产引用
一个常见的任务是获取对媒体服务中某个现有资产的引用。 以下代码示例演示了如何根据资产 ID，从服务器上下文对象上的资产集合中获取资产引用。以下代码示例使用 Linq 查询来获取对现有 IAsset 对象的引用。

```csharp
    static IAsset GetAsset(string assetId)
    {
        // Use a LINQ Select query to get an asset.
        var assetInstance =
            from a in _context.Assets
            where a.Id == assetId
            select a;
        // Reference the asset as an IAsset.
        IAsset asset = assetInstance.FirstOrDefault();

        return asset;
    }
```

## <a name="list-all-assets"></a>列出所有资产
随着存储中的资产数量的增长，这对列出你的资产很有用。 以下代码示例演示了如何循环访问服务器上下文对象上的资产集合。 对于每个资产，该代码示例还会将其一些属性值写入控制台。 例如，每个资产可以包含多个媒体文件。 代码示例会写出与每个资产关联的所有文件。

```csharp
    static void ListAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);

        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();

        foreach (IAsset asset in _context.Assets)
        {
            // Display the collection of assets.
            builder.AppendLine("");
            builder.AppendLine("******ASSET******");
            builder.AppendLine("Asset ID: " + asset.Id);
            builder.AppendLine("Name: " + asset.Name);
            builder.AppendLine("==============");
            builder.AppendLine("******ASSET FILES******");

            // Display the files associated with each asset. 
            foreach (IAssetFile fileItem in asset.AssetFiles)
            {
                builder.AppendLine("Name: " + fileItem.Name);
                builder.AppendLine("Size: " + fileItem.ContentFileSize);
                builder.AppendLine("==============");
            }
        }

        // Display output in console.
        Console.Write(builder.ToString());
    }
```

## <a name="get-a-job-reference"></a>获取作业引用

处理媒体服务代码中的任务时，通常需要根据 ID 获取对现有作业的引用。以下代码示例演示了如何获取对作业集合中某个 IJob 对象的引用。

开始长时运行的编码作业时，可能需要获取作业引用，并且需要检查线程上的作业状态。 在这种情况下，当方法从某个线程返回时，需要检索对作业的刷新引用。

```csharp
    static IJob GetJob(string jobId)
    {
        // Use a Linq select query to get an updated 
        // reference by Id. 
        var jobInstance =
            from j in _context.Jobs
            where j.Id == jobId
            select j;
        // Return the job reference as an Ijob. 
        IJob job = jobInstance.FirstOrDefault();

        return job;
    }
```

## <a name="list-jobs-and-assets"></a>列出作业和资产
在媒体服务中列出资产及其关联作业是一项重要的相关任务。 以下代码示例演示了如何列出每个 IJob 对象，然后，针对每个作业，它会显示作业的相关属性、所有相关的任务、所有输入资产和所有输出资产。 本示例中的代码对各种其他任务也有所帮助。 例如，如果想要列出你先前运行的一个或多个编码作业的输出资产，本代码将演示如何访问输出资产。 如果拥有对某个输出资产的引用，可以通过下载或提供 URL 的方式，将内容传递给其他用户或应用程序。 

有关传递资产选项的详细信息，请参阅[使用适用于 .NET 的媒体服务 SDK 传递资产](media-services-deliver-streaming-content.md)。

```csharp
    // List all jobs on the server, and for each job, also list 
    // all tasks, all input assets, all output assets.

    static void ListJobsAndAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);

        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();

        foreach (IJob job in _context.Jobs)
        {
            // Display the collection of jobs on the server.
            builder.AppendLine("");
            builder.AppendLine("******JOB*******");
            builder.AppendLine("Job ID: " + job.Id);
            builder.AppendLine("Name: " + job.Name);
            builder.AppendLine("State: " + job.State);
            builder.AppendLine("Order: " + job.Priority);
            builder.AppendLine("==============");


            // For each job, display the associated tasks (a job  
            // has one or more tasks). 
            builder.AppendLine("******TASKS*******");
            foreach (ITask task in job.Tasks)
            {
                builder.AppendLine("Task Id: " + task.Id);
                builder.AppendLine("Name: " + task.Name);
                builder.AppendLine("Progress: " + task.Progress);
                builder.AppendLine("Configuration: " + task.Configuration);
                if (task.ErrorDetails != null)
                {
                    builder.AppendLine("Error: " + task.ErrorDetails);
                }
                builder.AppendLine("==============");
            }

            // For each job, display the list of input media assets.
            builder.AppendLine("******JOB INPUT MEDIA ASSETS*******");
            foreach (IAsset inputAsset in job.InputMediaAssets)
            {

                if (inputAsset != null)
                {
                    builder.AppendLine("Input Asset Id: " + inputAsset.Id);
                    builder.AppendLine("Name: " + inputAsset.Name);
                    builder.AppendLine("==============");
                }
            }

            // For each job, display the list of output media assets.
            builder.AppendLine("******JOB OUTPUT MEDIA ASSETS*******");
            foreach (IAsset theAsset in job.OutputMediaAssets)
            {
                if (theAsset != null)
                {
                    builder.AppendLine("Output Asset Id: " + theAsset.Id);
                    builder.AppendLine("Name: " + theAsset.Name);
                    builder.AppendLine("==============");
                }
            }

        }

        // Display output in console.
        Console.Write(builder.ToString());
    }
```

## <a name="list-all-access-policies"></a>列出所有访问策略
在媒体服务中，可以对资产或其文件定义访问策略。 访问策略定义文件或资产的权限（访问类型以及持续时间）。 在媒体服务代码中，通常通过创建 IAccessPolicy 对象来定义访问策略对象，并将其与现有资产相关联。 然后创建一个 ILocator 对象，它允许你提供对媒体服务中的资产的直接访问。 本文档系列随附的 Visual Studio 项目包含几个代码示例，这些代码示例演示如何创建和分配访问策略和定位符到资产。

以下代码示例演示如何列出服务器上所有的访问策略，并显示与每个策略关联的权限类型。 查看访问策略的另一个有用方法是列出服务器上的所有 ILocator 对象，然后针对每个定位符，可以使用其 AccessPolicy 属性列出其关联的访问策略。

```csharp
    static void ListAllPolicies()
    {
        foreach (IAccessPolicy policy in _context.AccessPolicies)
        {
            Console.WriteLine("");
            Console.WriteLine("Name:  " + policy.Name);
            Console.WriteLine("ID:  " + policy.Id);
            Console.WriteLine("Permissions: " + policy.Permissions);
            Console.WriteLine("==============");

        }
    }
```
    
## <a name="limit-access-policies"></a>限制访问策略 

>[!NOTE]
> 不同 AMS 策略的策略限制为 1,000,000 个（例如，对于定位器策略或 ContentKeyAuthorizationPolicy）。 如果始终使用相同的日期/访问权限，则应使用相同的策略 ID，例如，用于要长期就地保留的定位符的策略（非上传策略）。 

例如，可以使用以下代码创建一组一般策略，这些代码只会在应用程序中运行一次。 可以将 ID 记录到日志文件以供以后使用：

```csharp
    double year = 365.25;
    double week = 7;
    IAccessPolicy policyYear = _context.AccessPolicies.Create("One Year", TimeSpan.FromDays(year), AccessPermissions.Read);
    IAccessPolicy policy100Year = _context.AccessPolicies.Create("Hundred Years", TimeSpan.FromDays(year * 100), AccessPermissions.Read);
    IAccessPolicy policyWeek = _context.AccessPolicies.Create("One Week", TimeSpan.FromDays(week), AccessPermissions.Read);

    Console.WriteLine("One year policy ID is: " + policyYear.Id);
    Console.WriteLine("100 year policy ID is: " + policy100Year.Id);
    Console.WriteLine("One week policy ID is: " + policyWeek.Id);
```

然后，可在代码中使用现有 ID，如下所示：

```csharp
    const string policy1YearId = "nb:pid:UUID:2a4f0104-51a9-4078-ae26-c730f88d35cf";


    // Get the standard policy for 1 year read only
    var tempPolicyId = from b in _context.AccessPolicies
                       where b.Id == policy1YearId
                       select b;
    IAccessPolicy policy1Year = tempPolicyId.FirstOrDefault();

    // Get the existing asset
    var tempAsset = from a in _context.Assets
                where a.Id == assetID
                select a;
    IAsset asset = tempAsset.SingleOrDefault();

    ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
        policy1Year,
        DateTime.UtcNow.AddMinutes(-5));
    Console.WriteLine("The locator base path is " + originLocator.BaseUri.ToString());
```

## <a name="list-all-locators"></a>列出所有定位符
定位符是一个 URL，提供访问资产的直接路径，以及定位符的关联访问策略所定义的对该资产的权限。 每个资产都有一个在其定位符属性上与其关联的 ILocator 对象集合。 服务器上下文还具有一个包含所有定位符的定位符集合。

以下代码示例列出了服务器上的所有定位符。 对于每个定位符，它会显示相关资产和访问策略的 ID。 它也显示权限的类型、到期日期和访问资产的完整路径。

请注意，访问资产的定位符路径仅仅是访问资产的基本 URL。 要创建用户或应用程序可以浏览到的单个文件的直接路径，代码必须将特定文件路径添加到定位符路径。 有关如何进行操作的详细信息，请参阅主题[使用适用于 .NET 的媒体服务 SDK 传递资产](media-services-deliver-streaming-content.md)。

```csharp
    static void ListAllLocators()
    {
        foreach (ILocator locator in _context.Locators)
        {
            Console.WriteLine("***********");
            Console.WriteLine("Locator Id: " + locator.Id);
            Console.WriteLine("Locator asset Id: " + locator.AssetId);
            Console.WriteLine("Locator access policy Id: " + locator.AccessPolicyId);
            Console.WriteLine("Access policy permissions: " + locator.AccessPolicy.Permissions);
            Console.WriteLine("Locator expiration: " + locator.ExpirationDateTime);
            // The locator path is the base or parent path (with included permissions) to access  
            // the media content of an asset. To create a full URL to a specific media file, take 
            // the locator path and then append a file name and info as needed.  
            Console.WriteLine("Locator base path: " + locator.Path);
            Console.WriteLine("");
        }
    }
```

## <a name="enumerating-through-large-collections-of-entities"></a>枚举大型实体集合
查询实体时，一次返回的实体数限制为 1000 个，因为公共 REST v2 将查询结果数限制为 1000 个。 枚举大型实体集合时，需要使用 Skip 和 Take。 

以下函数将循环访问所提供的媒体服务帐户中的所有作业。 媒体服务会在作业集合中返回 1000 个作业。 该函数使用 Skip 和 Take 来确保枚举所有作业（如果你帐户中的作业超过 1000 个）。

```csharp
    static void ProcessJobs()
    {
        try
        {

            int skipSize = 0;
            int batchSize = 1000;
            int currentBatch = 0;

            while (true)
            {
                // Loop through all Jobs (1000 at a time) in the Media Services account
                IQueryable _jobsCollectionQuery = _context.Jobs.Skip(skipSize).Take(batchSize);
                foreach (IJob job in _jobsCollectionQuery)
                {
                    currentBatch++;
                    Console.WriteLine("Processing Job Id:" + job.Id);
                }

                if (currentBatch == batchSize)
                {
                    skipSize += batchSize;
                    currentBatch = 0;
                }
                else
                {
                    break;
                }
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.Message);
        }
    }
```

## <a name="delete-an-asset"></a>删除资产
以下示例删除了一个资产。

```csharp
    static void DeleteAsset( IAsset asset)
    {
        // delete the asset
        asset.Delete();

        // Verify asset deletion
        if (GetAsset(asset.Id) == null)
            Console.WriteLine("Deleted the Asset");

    }
```

## <a name="delete-a-job"></a>删除作业
若要删除某一作业，必须检查该作业的状态是否如“状态”属性中所示。 可以删除已完成或已取消的作业，但是必须先取消处于其他状态（如已排队、已计划、或处理中）的作业，才可以删除这些作业。

以下代码示例演示了一种删除作业的方法，通过检查作业状态，当作业状态为已完成或取消时，删除作业。 此代码取决于本主题的上一节，用于获取对作业的引用：获取作业引用。

```csharp
    static void DeleteJob(string jobId)
    {
        bool jobDeleted = false;

        while (!jobDeleted)
        {
            // Get an updated job reference.  
            IJob job = GetJob(jobId);

            // Check and handle various possible job states. You can 
            // only delete a job whose state is Finished, Error, or Canceled.   
            // You can cancel jobs that are Queued, Scheduled, or Processing,  
            // and then delete after they are canceled.
            switch (job.State)
            {
                case JobState.Finished:
                case JobState.Canceled:
                case JobState.Error:
                    // Job errors should already be logged by polling or event 
                    // handling methods such as CheckJobProgress or StateChanged.
                    // You can also call job.DeleteAsync to do async deletes.
                    job.Delete();
                    Console.WriteLine("Job has been deleted.");
                    jobDeleted = true;
                    break;
                case JobState.Canceling:
                    Console.WriteLine("Job is cancelling and will be deleted "
                        + "when finished.");
                    Console.WriteLine("Wait while job finishes canceling...");
                    Thread.Sleep(5000);
                    break;
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    job.Cancel();
                    Console.WriteLine("Job is scheduled or processing and will "
                        + "be deleted.");
                    break;
                default:
                    break;
            }

        }
    }
```


## <a name="delete-an-access-policy"></a>删除访问策略
以下代码示例演示如何基于策略 ID，获取对访问策略的引用，并删除该策略。

```csharp
    static void DeleteAccessPolicy(string existingPolicyId)
    {
        // To delete a specific access policy, get a reference to the policy.  
        // based on the policy Id passed to the method.
        var policyInstance =
                from p in _context.AccessPolicies
                where p.Id == existingPolicyId
                select p;
        IAccessPolicy policy = policyInstance.FirstOrDefault();

        policy.Delete();

    }
```


## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

