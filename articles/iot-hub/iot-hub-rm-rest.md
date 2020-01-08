---
title: 使用资源提供程序 REST API 创建 Azure IoT 中心 | Microsoft Docs
description: 如何使用资源提供程序 REST API 创建 IoT 中心。
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.openlocfilehash: 6d91f5e61dfd7c3cb4d1869edf0c6cb8c2c85190
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65827504"
---
# <a name="create-an-iot-hub-using-the-resource-provider-rest-api-net"></a>使用资源提供程序 REST API 创建 IoT 中心 (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

可以通过编程方式使用 [IoT 中心资源提供程序 REST API](https://docs.microsoft.com/rest/api/iothub/iothubresource) 创建和管理 Azure IoT 中心。 本教程介绍如何使用 IoT 中心资源提供程序 REST API 通过 C# 程序创建 IoT 中心。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

要完成本教程，需要以下各项：

* Visual Studio。

* 有效的 Azure 帐户。 如果没有帐户，只需花费几分钟就能创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

* [Azure PowerShell 1.0](https://docs.microsoft.com/powershell/azure/install-Az-ps)或更高版本。

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>准备 Visual Studio 项目

1. 在 Visual Studio 中，使用“控制台应用(.NET Framework)”  项目模板创建 Visual C# Windows 经典桌面项目。 将该项目命名为 **CreateIoTHubREST**。

2. 在解决方案资源管理器中右键单击项目，然后单击“**管理 NuGet 包**”。

3. 在 NuGet 包管理器中，选中“包括预发行版”，并在“浏览”页上搜索 **Microsoft.Azure.Management.ResourceManager**   。 选择该包，单击“安装”  ，在“审阅更改”  中单击“确定”  ，并单击“我接受”  以接受许可证。

4. 在 NuGet 包管理器中，搜索 **Microsoft.IdentityModel.Clients.ActiveDirectory**。  单击“**安装**”，在“**审阅更改**”中单击“**确定**”，并单击“**我接受**”以接受许可证。

5. 在 Program.cs 中，将现有 **using** 语句替换为以下代码：

    ```csharp
    using System;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json;
    using Microsoft.Rest;
    using System.Linq;
    using System.Threading;
    ```

6. 在 Program.cs 中，将占位符值替换为以下静态变量。 在本教程前面的介绍中，已记下 **ApplicationId**、**SubscriptionId**、**TenantId** 和 **Password**。 资源组名称  是创建 IoT 中心时要使用的资源组名称。 可以使用现有的资源组或新资源组。 “IoT 中心名称”  是你创建的 IoT 中心的名称，例如“MyIoTHub”  。 IoT 中心的名称必须是全局唯一的。 **部署名称**是部署的名称，例如 **Deployment_01**。

    ```csharp
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";

    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name including your initials}";
    ```
   
    [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="use-the-resource-provider-rest-api-to-create-an-iot-hub"></a>使用资源提供程序 REST API 创建 IoT 中心

使用[IoT 中心资源提供程序 REST API](https://docs.microsoft.com/rest/api/iothub/iothubresource)在资源组中创建 IoT 中心。 还可以使用资源提供程序 REST API 更改现有的 IoT 中心。

1. 将以下方法添加到 Program.cs：

    ```csharp
    static void CreateIoTHub(string token)
    {

    }
    ```

2. 将以下代码添加到 **CreateIoTHub** 方法中。 该代码使用标头中的身份验证令牌创建 **HttpClient** 对象：

    ```csharp
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```

3. 将以下代码添加到 **CreateIoTHub** 方法中。 此代码描述要创建的 IoT 中心，并生成 JSON 表示形式。 有关支持 IoT 中心的位置的当前列表请参阅[Azure 状态](https://azure.microsoft.com/status/):

    ```csharp
    var description = new
    {
      name = iotHubName,
      location = "East US",
      sku = new
      {
        name = "S1",
        tier = "Standard",
        capacity = 1
      }
    };

    var json = JsonConvert.SerializeObject(description, Formatting.Indented);
    ```

4. 将以下代码添加到 **CreateIoTHub** 方法中。 使用此代码将 REST 请求提交到 Azure。 然后该代码会检查响应，并检索可用于监视部署任务状态的 URL：

    ```csharp
    var content = new StringContent(JsonConvert.SerializeObject(description), Encoding.UTF8, "application/json");
    var requestUri = string.Format("https://management.azure.com/subscriptions/{0}/resourcegroups/{1}/providers/Microsoft.devices/IotHubs/{2}?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var result = client.PutAsync(requestUri, content).Result;

    if (!result.IsSuccessStatusCode)
    {
      Console.WriteLine("Failed {0}", result.Content.ReadAsStringAsync().Result);
      return;
    }

    var asyncStatusUri = result.Headers.GetValues("Azure-AsyncOperation").First();
    ```

5. 将以下代码添加到 **CreateIoTHub** 方法的末尾。 该代码使用上一个步骤中检索的 **asyncStatusUri** 地址来等待部署完成：

    ```csharp
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{\"status\":\"Running\"}");
    ```

6. 将以下代码添加到 **CreateIoTHub** 方法的末尾。 此代码检索创建的 IoT 中心的键，并将其打印到控制台：

    ```csharp
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;

    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```

## <a name="complete-and-run-the-application"></a>完成并运行应用程序

现在，可以调用 CreateIoTHub  方法来完成应用程序，然后生成并运行该应用程序。

1. 将以下代码添加到 **Main** 方法末尾：

    ```csharp
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```

2. 单击“**生成**”，并单击“**生成解决方案**”。 更正所有错误。

3. 单击“**调试**”，并单击“**开始调试**”以运行应用程序。 运行部署可能需要几分钟时间。

4. 若要验证你的应用程序添加了新的 IoT 中心，请访问[Azure 门户](https://portal.azure.com/)并查看资源列表。 另外，也可以使用 **Get-AzResource** PowerShell cmdlet。

> [!NOTE]
> 本示例应用程序将添加用于对你计费的 S1 标准 IoT 中心。 完成后，可以删除通过 IoT 中心[Azure 门户](https://portal.azure.com/)或使用**删除 AzResource** PowerShell cmdlet 在完成时。

## <a name="next-steps"></a>后续步骤

现在，已使用资源提供程序 REST API 部署了一个 IoT 中心，接下来可以进一步进行探索：

* 阅读有关的功能[IoT 中心资源提供程序 REST API](https://docs.microsoft.com/rest/api/iothub/iothubresource)。

* 读取[Azure 资源管理器概述](../azure-resource-manager/resource-group-overview.md)若要了解有关 Azure 资源管理器的功能的详细信息。

若要详细了解如何开发 IoT 中心，请参阅以下文章：

* [C SDK 简介](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK](iot-hub-devguide-sdks.md)

若要进一步探索 IoT 中心的功能，请参阅：

* [使用 Azure IoT Edge 将 AI 部署到边缘设备](../iot-edge/tutorial-simulate-device-linux.md)