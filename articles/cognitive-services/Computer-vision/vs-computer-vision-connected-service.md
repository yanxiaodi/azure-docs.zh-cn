---
title: Visual Studio 连接服务 - 计算机视觉
titleSuffix: Azure Cognitive Services
description: 使用 Visual Studio 连接服务功能从 ASP.NET Core Web 应用程序连接到计算机视觉 API。
services: cognitive-services
author: ghogen
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: ghogen
ms.custom: seodec18
ms.openlocfilehash: ff3ae9ec4a775e2450a552e414ec52597593dd39
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604279"
---
# <a name="use-connected-services-in-visual-studio-to-connect-to-the-computer-vision-api"></a>使用 Visual Studio 中的连接服务连接到计算机视觉 API

本文及其同类文章提供了对认知服务计算机视觉 API 使用 Visual Studio 连接服务功能的详细信息。 安装了认知服务扩展的 Visual Studio 2017 15.7 或更高版本中均提供此功能。

## <a name="prerequisites"></a>必备组件

- Azure 订阅。 如果没有帐户，可以注册一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。
- Visual Studio 2017 版本 15.7 或更高版本，并安装有 **Web 开发**工作负荷。 [立即下载](https://visualstudio.microsoft.com/downloads/)。

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-cognitive-services-computer-vision-api"></a>为项目添加对认知服务计算机视觉 API 的支持

1. 创建新的 ASP.NET Core Web 项目。 使用空项目模板。 

1. 在“解决方案资源管理器”中，选择“添加” > “连接服务”    。
   此时会显示“连接服务”页，其中包含可添加到项目的服务。

   ![右键单击 Visual Studio 项目上的菜单：“添加”>“连接服务”](../media/vs-common/Connected-Service-Menu.PNG)

1. 在可用服务菜单中，选择“认知服务计算机视觉 API”  。

   ![“连接的服务”菜单：列出“分析图像...”](./media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-0.PNG)

   如果已登录到 Visual Studio，并且有与帐户关联的 Azure 订阅，则会显示一个页面，其中提供了订阅下拉列表。

   ![“计算机视觉 API”窗口，其中突出显示了“订阅”下拉列表](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-1.PNG)

1. 选择要使用的订阅，然后选择计算机视觉 API 的名称，或选择“编辑”链接以修改自动生成的名称，选择资源组和定价层。

   ![编辑连接服务详细信息](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-2.PNG)

   有关定价层的详细信息，请访问链接。

1. 选择“添加”，添加对“连接服务”的支持。
   Visual Studio 会修改项目以添加 NuGet 包、配置文件条目和其他更改，从而支持与计算机视觉 API 的连接。 输出窗口显示项目发生情况的日志。 会看到下面这样的内容：

   ```output
   [4/26/2018 5:15:31.664 PM] Adding Computer Vision API to the project.
   [4/26/2018 5:15:32.084 PM] Creating new ComputerVision...
   [4/26/2018 5:15:32.153 PM] Creating new Resource Group...
   [4/26/2018 5:15:40.286 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Vision.ComputerVision' version 2.1.0.
   [4/26/2018 5:15:44.117 PM] Retrieving keys...
   [4/26/2018 5:15:45.602 PM] Changing appsettings.json setting: ComputerVisionAPI_ServiceKey=<service key>
   [4/26/2018 5:15:45.606 PM] Changing appsettings.json setting: ComputerVisionAPI_ServiceEndPoint=https://australiaeast.api.cognitive.microsoft.com/vision/v2.0
   [4/26/2018 5:15:45.609 PM] Changing appsettings.json setting: ComputerVisionAPI_Name=WebApplication-Core-ComputerVision_ComputerVisionAPI
   [4/26/2018 5:15:46.747 PM] Successfully added Computer Vision API to the project.
   ```
 
## <a name="use-the-computer-vision-api-to-detect-attributes-of-an-image"></a>使用计算机视觉 API 检测图像的特征

1. 在 Startup.cs 中添加以下 using 语句。
 
   ```csharp
   using System.IO;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   using System.Net.Http;
   using System.Net.Http.Headers;
   ```
 
1. 添加配置字段，并添加一个构造函数，该构造函数初始化 `Startup` 类中的配置字段以在程序中启用配置。

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. 在项目的 wwwroot 文件夹中，添加图像文件夹，并向 wwwroot 文件夹添加图像文件。 例如，可使用此[计算机视觉 API 页](https://azure.microsoft.com/services/cognitive-services/computer-vision/)中的一张图像。 右键单击其中一张图像，将其保存到本地硬盘驱动器，然后在解决方案资源管理器中右键单击图像文件夹，选择“添加” > “现有项”，将其添加到项目中   。 项目在解决方案资源管理器中应如下所示： 
  
   ![选择了一个图像文件的解决方案资源管理器视图的屏幕截图](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-3.PNG) 

1. 右键单击图像文件，选择“属性”，然后选择“如果较新则复制”  。 

   ![图像属性窗口；“复制到输出目录”设置为“如果较新则复制”](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-5.PNG) 
 
1. 将配置方法替换为以下代码，访问计算机视觉 API 并测试图像。

   ```csharp
    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        // TODO: Change this to your image's path on your site. 
        string imagePath = @"images/subway.png";

        // Enable static files such as image files. 
        app.UseStaticFiles();

        string visionApiKey = this.configuration["ComputerVisionAPI_ServiceKey"];
        string visionApiEndPoint = this.configuration["ComputerVisionAPI_ServiceEndPoint"];

        HttpClient client = new HttpClient();

        // Request headers.
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", visionApiKey);

        // Request parameters. A third optional parameter is "details".
        string requestParameters = "visualFeatures=Categories,Description,Color&language=en";

        // Assemble the URI for the REST API Call.
        string uri = visionApiEndPoint + "/analyze" + "?" + requestParameters;

        HttpResponseMessage response;

        // Request body. Posts an image you've added to your site's images folder. 
        var fileInfo = env.WebRootFileProvider.GetFileInfo(imagePath);
        byte[] byteData = GetImageAsByteArray(fileInfo.PhysicalPath);

        string contentString = string.Empty;
        using (ByteArrayContent content = new ByteArrayContent(byteData))
        {
            // This example uses content type "application/octet-stream".
            // The other content types you can use are "application/json" and "multipart/form-data".
            content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");

            // Execute the REST API call.
            response = client.PostAsync(uri, content).Result;

            // Get the JSON response.
            contentString = response.Content.ReadAsStringAsync().Result;
        }

        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.Run(async (context) =>
        {
            await context.Response.WriteAsync("<h1>Cognitive Services Demo</h1>");
            await context.Response.WriteAsync($"<p><b>Test Image:</b></p>");
            await context.Response.WriteAsync($"<div><img src=\"" + imagePath + "\" /></div>");
            await context.Response.WriteAsync($"<p><b>Computer Vision API results:</b></p>");
            await context.Response.WriteAsync("<p>");
            await context.Response.WriteAsync(JsonPrettyPrint(contentString));
            await context.Response.WriteAsync("<p>");
        });
    }
   ```

    此处的代码会构造计算机视觉 REST API 调用的 HTTP 请求，其中 URI 和图像作为二进制内容。

1. 添加帮助程序函数 GetImageAsByteArray 和 JsonPrettyPrint。

   ```csharp
    /// <summary>
    /// Returns the contents of the specified file as a byte array.
    /// </summary>
    /// <param name="imageFilePath">The image file to read.</param>
    /// <returns>The byte array of the image data.</returns>
    static byte[] GetImageAsByteArray(string imageFilePath)
    {
        FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
        BinaryReader binaryReader = new BinaryReader(fileStream);
        return binaryReader.ReadBytes((int)fileStream.Length);
    }

    /// <summary>
    /// Formats the given JSON string by adding line breaks and indents.
    /// </summary>
    /// <param name="json">The raw JSON string to format.</param>
    /// <returns>The formatted JSON string.</returns>
    static string JsonPrettyPrint(string json)
    {
        if (string.IsNullOrEmpty(json))
            return string.Empty;

        json = json.Replace(Environment.NewLine, "").Replace("\t", "");

        string INDENT_STRING = "    ";
        var indent = 0;
        var quoted = false;
        var sb = new StringBuilder();
        for (var i = 0; i < json.Length; i++)
        {
            var ch = json[i];
            switch (ch)
            {
                case '{':
                case '[':
                    sb.Append(ch);
                    if (!quoted)
                    {
                        sb.AppendLine();
                    }
                    break;
                case '}':
                case ']':
                    if (!quoted)
                    {
                        sb.AppendLine();
                    }
                    sb.Append(ch);
                    break;
                case '"':
                    sb.Append(ch);
                    bool escaped = false;
                    var index = i;
                    while (index > 0 && json[--index] == '\\')
                        escaped = !escaped;
                    if (!escaped)
                        quoted = !quoted;
                    break;
                case ',':
                    sb.Append(ch);
                    if (!quoted)
                    {
                        sb.AppendLine();
                    }
                    break;
                case ':':
                    sb.Append(ch);
                    if (!quoted)
                        sb.Append(" ");
                    break;
                default:
                    sb.Append(ch);
                    break;
            }
        }
        return sb.ToString();
    }
   ```

1. 运行 Web 应用程序并查看计算机视觉 API 在图像中找到的信息。

   ![计算机视觉 API 图像和格式化结果](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-4.PNG)  

## <a name="clean-up-resources"></a>清理资源

不再需要资源组时，可将其删除。 这会删除认知服务及相关资源。 要通过门户删除资源组，请执行以下操作：

1. 在门户顶部的“搜索”框中输入资源组的名称。 在搜索结果中看到在本快速入门中使用的资源组后，将其选中。
2. 选择“删除资源组”  。
3. 在“键入资源组名称:”框中，键入资源组的名称，然后选择“删除”   。

## <a name="next-steps"></a>后续步骤

有关计算机视觉 API 的详细信息，请阅读[计算机视觉 API 文档](Home.md)。
