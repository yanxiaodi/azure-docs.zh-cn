---
title: 以编程方式监视和管理 Azure 流分析作业
description: 本文说明如何以编程方式监视通过 REST API、Azure SDK 或 PowerShell 创建的流分析作业。
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: bf84a5b89e4769e37c45714a30d5d98300a4328d
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612257"
---
# <a name="programmatically-create-a-stream-analytics-job-monitor"></a>以编程方式创建流分析作业监视器

本文说明如何为流分析作业启用监视功能。 通过 REST API、Azure SDK 或 PowerShell 创建的流分析作业默认不启用监视功能。 可以在 Azure 门户中手动启用此功能，只需转到作业的“监视”页并单击“启用”按钮即可；也可以按本文中所述步骤自动化此过程。 流分析作业的监视数据会显示在 Azure 门户的“指标”区域。

## <a name="prerequisites"></a>先决条件

在开始此过程之前，必须具备以下先决条件：

* Visual Studio 2019 或 2015
* 已下载并安装了 [Azure.NET SDK](https://azure.microsoft.com/downloads/)
* 具有需要已启用监视功能的现有流分析作业

## <a name="create-a-project"></a>创建一个项目

1. 创建 Visual Studio C# .NET 控制台应用程序。
2. 在程序包管理器控制台中运行以下命令以安装 NuGet 包。 第一个是 Azure 流分析管理 .NET SDK。 第二个是 Azure Monitor SDK，将用于启用监视功能。 最后一个是用于进行身份验证的 Azure Active Directory 客户端。
   
   ```powershell
   Install-Package Microsoft.Azure.Management.StreamAnalytics
   Install-Package Microsoft.Azure.Insights -Pre
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```
3. 将下面的 appSettings 部分添加到 App.config 文件。
   
   ```csharp
   <appSettings>
     <!--CSM Prod related values-->
     <add key="ResourceGroupName" value="RESOURCE GROUP NAME" />
     <add key="JobName" value="YOUR JOB NAME" />
     <add key="StorageAccountName" value="YOUR STORAGE ACCOUNT"/>
     <add key="ActiveDirectoryEndpoint" value="https://login.microsoftonline.com/" />
     <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
     <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
     <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
     <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
     <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION ID" />
     <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
   </appSettings>
   ```
   将 *SubscriptionId* 和 *ActiveDirectoryTenantId* 的值替换为 Azure 订阅 ID 和租户 ID。 可以通过运行以下 PowerShell cmdlet 来获取这些值：
   
   ```powershell
   Get-AzureAccount
   ```
4. 将以下 using 语句添加到项目中的源文件 (Program.cs)。
   
   ```csharp
     using System;
     using System.Configuration;
     using System.Threading;
     using Microsoft.Azure;
     using Microsoft.Azure.Management.Insights;
     using Microsoft.Azure.Management.Insights.Models;
     using Microsoft.Azure.Management.StreamAnalytics;
     using Microsoft.Azure.Management.StreamAnalytics.Models;
     using Microsoft.IdentityModel.Clients.ActiveDirectory;
   ```
5. 添加一个身份验证帮助器方法。

   ```csharp   
   public static string GetAuthorizationHeader()
   {
      AuthenticationResult result = null;
      var thread = new Thread(() =>
      {
         try
         {
             var context = new AuthenticationContext(
                ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
                ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
             result = context.AcquireToken(
                 resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                 clientId: ConfigurationManager.AppSettings["AsaClientId"],
                 redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
                 promptBehavior: PromptBehavior.Always);
         }
         catch (Exception threadEx)
         {
             Console.WriteLine(threadEx.Message);
         }
     });

     thread.SetApartmentState(ApartmentState.STA);
     thread.Name = "AcquireTokenThread";
     thread.Start();
     thread.Join();
   
     if (result != null)
     {
         return result.AccessToken;
     }
         throw new InvalidOperationException("Failed to acquire token");
   }
   ```

## <a name="create-management-clients"></a>创建管理客户端

以下代码将设置必需变量和管理客户端。

   ```csharp
    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials =
        new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader());

    Uri resourceManagerUri = new
    Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    // Create Stream Analytics and Insights management client
    StreamAnalyticsManagementClient streamAnalyticsClient = new
    StreamAnalyticsManagementClient(aadTokenCredentials, resourceManagerUri);
    InsightsManagementClient insightsClient = new
    InsightsManagementClient(aadTokenCredentials, resourceManagerUri);
   ```

## <a name="enable-monitoring-for-an-existing-stream-analytics-job"></a>为现有流分析作业启用监视功能

以下代码为现有  流分析作业启用监视功能。 代码的第一部分针对流分析服务执行 GET 请求，目的是检索特定流分析作业的信息。 它使用*ID*属性 （从 GET 请求检索而得） 作为第二个下半部分的代码，将发送 PUT 请求到 Insights 服务 Stream Analytics 作业启用监视功能中的 Put 方法的参数。

> [!WARNING]
> 如果此前为其他流分析作业启用了监视功能，那么不管是通过 Azure 门户进行还是通过以下代码以编程方式完成，我们都建议提供在之前启用监视功能时所使用的同一个存储帐户名称。 
> 
> 存储帐户将链接到创建流分析作业时所在的区域，并不特定于作业本身。
> 
> 该同一区域中的所有流分析作业（以及所有其他 Azure 资源）在存储监视数据时将共享此存储帐户。 如果提供其他存储帐户，则可能会产生意想不到的副作用，这将影响对其他流分析作业或其他 Azure 资源的监视。
> 
> 用于替换以下代码中的 `<YOUR STORAGE ACCOUNT NAME>` 的存储帐户名称应该是与为其启用监视功能的流分析作业所在的同一订阅中的存储帐户。
> 
> 
>    ```csharp
>    // Get an existing Stream Analytics job
>     JobGetParameters jobGetParameters = new JobGetParameters()
>     {
>         PropertiesToExpand = "inputs,transformation,outputs"
>     };
>     JobGetResponse jobGetResponse = streamAnalyticsClient.StreamingJobs.Get(resourceGroupName, streamAnalyticsJobName, jobGetParameters);
>
>    // Enable monitoring
>    ServiceDiagnosticSettingsPutParameters insightPutParameters = new ServiceDiagnosticSettingsPutParameters()
>    {
>            Properties = new ServiceDiagnosticSettings()
>           {
>               StorageAccountName = "<YOUR STORAGE ACCOUNT NAME>"
>           }
>    };
>   insightsClient.ServiceDiagnosticSettingsOperations.Put(jobGetResponse.Job.Id, insightPutParameters);
>   ```


## <a name="get-support"></a>获取支持

如需进一步的帮助，请试用我们的 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>后续步骤

* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-real-time-fraud-detection.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
