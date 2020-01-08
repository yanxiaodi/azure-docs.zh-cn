---
title: 使用 Azure 中继的 REST 教程 | Microsoft 文档
description: 生成一个简单的 Azure 服务总线中继主机应用程序，该应用程序公开基于 REST 的接口。
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 1312b2db-94c4-4a48-b815-c5deb5b77a6a
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/06/2018
ms.author: spelluru
ms.openlocfilehash: 4e988724f842ff12cd599eba95c31006fe208fad
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2019
ms.locfileid: "68422892"
---
# <a name="azure-wcf-relay-rest-tutorial"></a>Azure WCF 中继 REST 教程
本教程介绍如何生成简单的服务总线主机应用程序，该应用程序公开基于 REST 的接口。 REST 使 Web 客户端（例如 Web 浏览器）可通过 HTTP 请求访问服务总线 API。

该教程使用 Windows Communication Foundation (WCF) REST 编程模型在 Azure 中继上构建 REST 服务。 有关详细信息，请参阅 WCF 文档中的 [WCF REST 编程模型](/dotnet/framework/wcf/feature-details/wcf-web-http-programming-model)和[设计和实现服务](/dotnet/framework/wcf/designing-and-implementing-services)。

在本教程中，你将执行以下步骤：

> [!div class="checklist"]
> * 创建中继命名空间。
> * 定义基于 REST 的 WCF 服务协定
> * 实现基于 REST 的 WCF 协定
> * 托管并运行基于 REST 的 WCF 服务
> * 运行和测试服务

## <a name="prerequisites"></a>先决条件

若要完成本教程，需要具备以下先决条件：

- Azure 订阅。 如果没有订阅，请在开始之前[创建一个免费帐户](https://azure.microsoft.com/free/)。
- [Visual Studio 2015 或更高版本](https://www.visualstudio.com)。 本教程中的示例使用 Visual Studio 2017。
- 用于 .NET 的 Azure SDK。 从 [SDK 下载页](https://azure.microsoft.com/downloads/)安装它。

## <a name="create-a-relay-namespace"></a>创建中继命名空间

若要开始在 Azure 中使用中继功能，必须先创建一个服务命名空间。 命名空间提供了用于对应用程序中的 Azure 资源进行寻址的范围容器。 请按照[此处的说明](relay-create-namespace-portal.md)创建中继命名空间。

## <a name="define-a-rest-based-wcf-service-contract-to-use-with-azure-relay"></a>定义基于 REST 的 WCF 服务约定以用于 Azure 中继

创建创建 WCF REST 样式的服务时，必须定义约定。 约定指定主机支持的操作。 服务操作可以看作是 Web 服务方法。 协定通过定义 C++、C# 或 Visual Basic 接口来创建。 接口中的每个方法都对应一个特定的服务操作。 必须将 [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) 属性应用到每个接口，且必须将 [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) 属性应用到每个操作。 如果具有 [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) 的接口中的方法没有 [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute)，则该方法是不公开的。 该过程后面的示例中显示了这些任务所用的代码。

WCF 协定和 REST 样式的协定的主要区别在于是否向 [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) 添加一个属性：[WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute)。 此属性允许将接口中的方法映射到该接口另一侧的方法。 此示例使用 [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute) 属性将一个方法链接到 HTTP GET。 这会使服务总线可以准确地检索并解释发送到接口的命令。

### <a name="to-create-a-contract-with-an-interface"></a>使用接口创建协定

1. 以管理员身份打开 Visual Studio：在“开始”菜单中右键单击该程序，然后选择“以管理员身份运行”。
2. 创建新的控制台应用程序项目。 单击“文件”菜单并选择“新建”，并单击“项目”。 在“新建项目”对话框中，单击“Visual C#”，选择“控制台应用程序”模板，并将其命名为“ImageListener”。 使用默认“位置”。 单击**确定**以创建项目。
3. 对于 C# 项目，Visual Studio 会创建 `Program.cs` 文件。 此类包含一个空的 `Main()` 方法，需要此方法才能正确生成控制台应用程序项目。
4. 通过安装服务总线 NuGet 包，向项目添加对服务总线和 **System.ServiceModel.dll** 的引用。 该包自动添加对服务总线库和 WCF **System.ServiceModel**的引用。 在“解决方案资源管理器”中，右键单击“ImageListener”项目，并单击“管理 NuGet 包”。 单击“浏览”选项卡，并搜索 `Microsoft Azure Service Bus`。 单击“安装” 并接受使用条款。
5. 必须在项目中显式添加对 **System.ServiceModel.dll** 的引用：
   
    a. 在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。
   
    b. 在“添加引用”对话框中，单击左侧的“框架”选项卡，并在“搜索”框中键入“System.ServiceModel.Web”。 选择“System.ServiceModel.Web”复选框，并单击“确定”。
6. 在 Program.cs 文件顶部添加以下 `using` 语句。
   
    ```csharp
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using System.ServiceModel.Web;
    using System.IO;
    ```
   
    使用 [System.ServiceModel](/dotnet/api/system.servicemodel) 命名空间，可以编程方式访问 WCF 的基本功能。 WCF 中继使用 WCF 的许多对象和属性来定义服务约定。 将在大多数中继应用程序中使用此命名空间。 同样[System.ServiceModel.Channels](/dotnet/api/system.servicemodel.channels) 可帮助定义通道，通道是用来与 Azure 中继和客户端 Web 浏览器通信的对象。 最后， [System.ServiceModel.Web](/dotnet/api/system.servicemodel.web) 包含的类型可用于创建基于 Web 的应用程序。
7. 将 `ImageListener` 命名空间重命名为 **Microsoft.ServiceBus.Samples**。
   
    ```csharp
    namespace Microsoft.ServiceBus.Samples
    {
        ...
    ```
8. 在命名空间声明的左大括号后面，紧接着定义一个名为 **IImageContract** 的新接口，然后将 **ServiceContractAttribute** 属性应用于该接口，其值为 `https://samples.microsoft.com/ServiceModel/Relay/`。 该命名空间值不同于在整个代码范围内使用的命名空间。 该命名空间值将用作此约定的唯一标识符，并应有版本控制信息。 有关详细信息，请参阅 [服务版本控制](https://go.microsoft.com/fwlink/?LinkID=180498)。 显式指定命名空间可防止将默认的命名空间值添加到约定名称中。
   
    ```csharp
    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
    public interface IImageContract
    {
    }
    ```
9. 在 `IImageContract` 接口中，为 `IImageContract` 协定在接口中公开的单个操作声明一个方法，然后将 `OperationContractAttribute` 属性应用到你希望将其作为公共服务总线协定的一部分进行公开的方法中。
   
    ```csharp
    public interface IImageContract
    {
        [OperationContract]
        Stream GetImage();
    }
    ```
10. 在 **OperationContract** 属性中，添加 **WebGet** 值。
    
    ```csharp
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
    ```
    
    这样做可以让中继服务将 HTTP GET 请求路由到 `GetImage`，并将 `GetImage` 的返回值转换为 HTTP GETRESPONSE 答复。 稍后在本教程中，将使用 Web 浏览器访问此方法，并会在浏览器中显示图像。
11. 直接在 `IImageContract` 定义的后面，声明从 `IImageContract` 和 `IClientChannel` 接口继承的通道。
    
    ```csharp
    public interface IImageChannel : IImageContract, IClientChannel { }
    ```
    
    通道是服务和客户端用来互相传递信息的 WCF 对象。 稍后会在主机应用程序中创建通道。 然后 Azure 中继将使用该通道将浏览器的 HTTP GET 请求传递到 **GetImage** 实现。 中继还使用该通道获取 **GetImage** 返回值并将其转换为客户端浏览器的 HTTP GETRESPONSE。
12. 在“生成”菜单中，单击“生成解决方案”以确认工作的准确性。

### <a name="example"></a>示例
以下代码显示了定义 WCF 中继协定的基本接口。

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="implement-the-rest-based-wcf-service-contract"></a>实现基于 REST 的 WCF 服务协定
创建 REST 样式的 WCF 中继服务首先需要创建使用接口定义的约定。 下一步是实现该接口。 此步骤包括创建名为 **ImageService** 的类，该类用于实现用户定义的 **IImageContract** 接口。 实现约定后，即可使用 App.config 文件配置接口。 该配置文件包含应用程序所需的信息，如服务的名称、协定的名称，以及用来与中继服务通信的协议类型。 该过程后面的示例中提供了这些任务所用的代码。

与前面的步骤一样，实现 REST 样式的约定与实现 WCF 中继约定之间的差别很小。

### <a name="to-implement-a-rest-style-service-bus-contract"></a>实现 REST 样式的服务总线约定
1. 在 **IImageContract** 接口定义的正下方创建名为 **ImageService** 的新类。 **ImageService** 类实现 **IImageContract** 接口。
   
    ```csharp
    class ImageService : IImageContract
    {
    }
    ```
    与其他接口实现类似，可以在另一个文件中实现定义。 但是，在本教程中，实现所在的文件与接口定义和 `Main()` 方法所在的文件相同。
2. 将 [ServiceBehaviorAttribute](/dotnet/api/system.servicemodel.servicebehaviorattribute) 属性应用到 **IImageService** 类，以指示该类是 WCF 协定的实现。
   
    ```csharp
    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
    }
    ```
   
    如前所述，此命名空间不是传统的命名空间， 而是用于标识约定的 WCF 体系结构的一部分。 有关详细信息，请参阅 WCF 文档中的[数据协定名称](https://msdn.microsoft.com/library/ms731045.aspx)一文。
3. 将一幅 .jpg 图像添加到项目中。  
   
    这是服务在接收浏览器中显示的图片。 右键单击项目，并单击“添加”。 然后单击“现有项”。 使用“添加现有项”对话框浏览到相应的 .jpg，并单击“添加”。
   
    添加文件时，请确保在“文件名:”字段旁的下拉列表中选择“所有文件”。 本教程的余下部分假定图像的名称为“image.jpg”。 如果 .jpg 文件名不是这样，则必须重命名图像，或更改代码进行弥补。
4. 为确保正在运行的服务可以找到该图像文件，请在“解决方案资源管理器”中右键单击该图像文件，并单击“属性”。 在“属性”窗格中，将“复制到输出目录”设置为“如果较新则复制”。
5. 在项目中添加对 **System.Drawing.dll** 程序集的引用，以及以下关联的 `using` 语句。  
   
    ```csharp
    using System.Drawing;
    using System.Drawing.Imaging;
    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Web;
    ```
6. 在 **ImageService** 类中定义以下构造函数，以便加载位图并准备将该位图发送到客户端浏览器。
   
    ```csharp
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";
   
        Image bitmap;
   
        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }
    }
    ```
7. 直接在上一代码后面，在 **ImageService** 类中添加以下 **GetImage** 方法，以返回包含该映像的 HTTP 消息。
   
    ```csharp
    public Stream GetImage()
    {
        MemoryStream stream = new MemoryStream();
        this.bitmap.Save(stream, ImageFormat.Jpeg);
   
        stream.Position = 0;
        WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";
   
        return stream;
    }
    ```
   
    此实现使用 **MemoryStream** 检索映像并准备将其流式传输到浏览器。 它将流位置设置为从零开始，将流内容声明为 jpeg，然后流式传输信息。
8. 在“生成”菜单中，单击“生成解决方案”。

### <a name="to-define-the-configuration-for-running-the-web-service-on-service-bus"></a>定义配置以便在服务总线上运行 Web 服务
1. 在“解决方案资源管理器”中，双击“App.config”文件以在 Visual Studio 编辑器中将其打开。
   
    **App.config** 文件包括服务名称、终结点（即，Azure 中继公开的、让客户端和主机相互通信的位置）和绑定（用于通信的协议类型）。 此处的主要差别在于，配置的服务终结点是指 [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) 绑定。
2. `<system.serviceModel>` XML 元素是一个 WCF 元素，用于定义一个或多个服务。 在这里，它用于定义服务名称和终结点。 在 `<system.serviceModel>` 元素的下面（仍在 `<system.serviceModel>` 中）添加具有以下内容的 `<bindings>` 元素。 这样就定义了应用程序中使用的绑定。 可以定义多个绑定，但在本教程中，只要定义一个绑定。
   
    ```xml
    <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
            <binding name="default">
                <security relayClientAuthenticationType="None" />
            </binding>
        </webHttpRelayBinding>
    </bindings>
    ```
   
    前面的代码定义了一个 WCF 中继 [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) 绑定，其中“relayClientAuthenticationType” 设置为“None”。 此设置表明使用此绑定的终结点将不需要客户端凭据。
3. 在 `<bindings>` 元素后面添加 `<services>` 元素。 与绑定类似，可以在单个配置文件中定义多个服务。 但是，在本教程中，只要定义一个服务。
   
    ```xml
    <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
            <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IImageContract"
                    binding="webHttpRelayBinding"
                    bindingConfiguration="default"
                    behaviorConfiguration="sbTokenProvider"
                    address="" />
        </service>
    </services>
    ```
   
    此步骤将配置一个服务，该服务使用前面定义的默认 **webHttpRelayBinding**。 此外，它还使用下一步骤中定义的默认 **sbTokenProvider**。
4. 在 `<services>` 元素的后面，使用以下内容创建 `<behaviors>` 元素，并将“SAS_KEY”替换为先前从 [Azure 门户][Azure portal]获得的共享访问签名 (SAS) 密钥。
   
    ```xml
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedAccessSignature keyName="RootManageSharedAccessKey" key="YOUR_SAS_KEY" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
            </endpointBehaviors>
            <serviceBehaviors>
                <behavior name="default">
                    <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
                </behavior>
            </serviceBehaviors>
    </behaviors>
    ```
5. 仍在 App.config 文件中，在 `<appSettings>` 元素中，将整个连接字符串替换为以前从门户获取的连接字符串。 
   
    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY"/>
    </appSettings>
    ```
6. 在“生成”菜单中，单击“生成解决方案”以生成整个解决方案。

### <a name="example"></a>示例
以下代码演示了一个在服务总线上运行并使用 **WebHttpRelayBinding** 绑定的、基于 REST 的服务的约定和服务实现。

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{


    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

以下示例显示了与该服务关联的 App.config 文件。

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2"/>
    </startup>
    <system.serviceModel>
        <extensions>
            <!-- In this extension section we are introducing all known service bus extensions. User can remove the ones they don't need. -->
            <behaviorExtensions>
                <add name="connectionStatusBehavior"
                    type="Microsoft.ServiceBus.Configuration.ConnectionStatusElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="transportClientEndpointBehavior"
                    type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="serviceRegistrySettings"
                    type="Microsoft.ServiceBus.Configuration.ServiceRegistrySettingsElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </behaviorExtensions>
            <bindingElementExtensions>
                <add name="netMessagingTransport"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingTransportExtensionElement, Microsoft.ServiceBus,  Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="tcpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.TcpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpsRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpsRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="onewayRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.RelayedOnewayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingElementExtensions>
            <bindingExtensions>
                <add name="basicHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.BasicHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="webHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WebHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="ws2007HttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WS2007HttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netOnewayRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetOnewayRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netEventRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetEventRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netMessagingBinding"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingExtensions>
        </extensions>
      <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
          <binding name="default">
            <security relayClientAuthenticationType="None" />
          </binding>
        </webHttpRelayBinding>
      </bindings>
      <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
          <endpoint name="RelayEndpoint"
                  contract="Microsoft.ServiceBus.Samples.IImageContract"
                  binding="webHttpRelayBinding"
                  bindingConfiguration="default"
                  behaviorConfiguration="sbTokenProvider"
                  address="" />
        </service>
      </services>
      <behaviors>
        <endpointBehaviors>
          <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
              <tokenProvider>
                <sharedAccessSignature keyName="RootManageSharedAccessKey" key="YOUR_SAS_KEY" />
              </tokenProvider>
            </transportClientEndpointBehavior>
          </behavior>
        </endpointBehaviors>
        <serviceBehaviors>
          <behavior name="default">
            <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
          </behavior>
        </serviceBehaviors>
      </behaviors>
    </system.serviceModel>
    <appSettings>
        <!-- Service Bus specific app settings for messaging connections -->
        <add key="Microsoft.ServiceBus.ConnectionString"
            value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY>"/>
    </appSettings>
</configuration>
```

## <a name="host-the-rest-based-wcf-service-to-use-azure-relay"></a>托管基于 REST 的 WCF 服务以使用 Azure 中继
此步骤描述如何使用控制台应用程序通过 WCF 中继运行 Web 服务。 此步骤中编写的代码的完整列表会在过程后面的示例中提供。

### <a name="to-create-a-base-address-for-the-service"></a>为服务创建基本地址
1. 在 `Main()` 函数声明中，创建一个变量，用于存储项目的命名空间。 请确保将 `yourNamespace` 替换为之前创建的中继命名空间的名称。
   
    ```csharp
    string serviceNamespace = "yourNamespace";
    ```
    服务总线使用服务命名空间的名称来创建唯一 URI。
2. 为基于服务命名空间的服务的基本地址创建 `Uri` 实例。
   
    ```csharp
    Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
    ```

### <a name="to-create-and-configure-the-web-service-host"></a>创建并配置 Web 服务主机
* 使用之前在本部分中创建的 URI 地址创建 Web 服务主机。
  
    ```csharp
    WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
    ```
    该服务主机是可实例化主机应用程序的 WCF 对象。 本示例会将要创建的主机类型 ( **ImageService**)，以及要公开主机应用程序的地址传递给它。

### <a name="to-run-the-web-service-host"></a>运行 Web 服务主机
1. 打开服务。
   
    ```csharp
    host.Open();
    ```
    服务现在正在运行。
2. 显示表明服务正在运行以及如何停止服务的消息。
   
    ```csharp
    Console.WriteLine("Copy the following address into a browser to see the image: ");
    Console.WriteLine(address + "GetImage");
    Console.WriteLine();
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```
3. 完成后，关闭服务主机。
   
    ```csharp
    host.Close();
    ```

### <a name="example"></a>示例
以下示例包括本教程中前面步骤中使用的服务约定和实现，并将服务托管在控制台应用程序中。 将以下代码编译到名为 ImageListener.exe 的可执行文件中。

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string serviceNamespace = "InsertServiceNamespaceHere";
            Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");

            WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
            host.Open();

            Console.WriteLine("Copy the following address into a browser to see the image: ");
            Console.WriteLine(address + "GetImage");
            Console.WriteLine();
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            host.Close();
        }
    }
}
```

## <a name="run-and-test-the-service"></a>运行和测试服务
生成解决方案之后，请执行以下代码来运行应用程序：

1. 按 **F5**，或浏览找到可执行文件的位置 (ImageListener\bin\Debug\ImageListener.exe) 来运行此服务。 保持应用程序运行，因为这是执行下一步所需要的。
2. 将命令提示符中的地址复制并粘贴到浏览器中以查看图像。
3. 完成后，在命令提示符窗口中按 **Enter** 关闭应用程序。

## <a name="next-steps"></a>后续步骤
至此，你已使用 Azure 中继服务构建一个应用程序，接下来请参阅以下文章了解详细信息：

* [Azure 中继概述](relay-what-is-it.md)
* [如何通过 .NET 使用 WCF 中继服务](service-bus-relay-tutorial.md)

[Azure portal]: https://portal.azure.com
