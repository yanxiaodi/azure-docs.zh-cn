---
title: 如何使用 Twilio 实现语音和短信功能 (.NET) | Microsoft Docs
description: 了解如何在 Azure 中使用 Twilio API 服务发起电话呼叫和发送短信。 采用 .NET 编写的代码示例。
services: ''
documentationcenter: .net
author: georgewallace
ms.assetid: 74d4f3c9-f1cb-4968-b744-36b32cd0e834
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/24/2015
ms.author: gwallace
ms.openlocfilehash: 22b33d7b4b0ff69a2e751cadff70453f73ed4f8e
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876807"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-from-azure"></a>如何在 Azure 中使用 Twilio 实现语音和短信功能
本指南演示如何在 Azure 中使用 Twilio API 服务执行常见编程任务。 所涉及的任务包括发起电话呼叫和发送短信服务 (SMS) 消息。 有关 Twilio 以及在应用程序中使用语音和短信的详细信息，请参阅[后续步骤](#NextSteps)部分。

## <a id="WhatIs"></a>什么是 Twilio？
Twilio 为将来的商业沟通提供强大支持，并使开发人员能够将语音、VoIP 和消息传送嵌入到应用程序中。 它们对基于云的全球环境中所需的所有基础结构进行虚拟化，并通过 Twilio 通信 API 平台将其公开。 可轻松构建和扩展应用程序。 享受即用即付定价所带来的灵活性，并从云可靠性中受益。

利用 **Twilio 语音**，应用程序可以发起和接收电话呼叫。 **Twilio SMS** 使应用程序能够发送和接收 SMS 消息。 利用 **Twilio 客户端**，可以从任何手机、平板电脑或浏览器发起 VoIP 呼叫并支持 WebRTC。

## <a id="Pricing"></a>Twilio 定价和特别优惠
Azure 客户在升级 Twilio 帐户后即可获得[特惠套餐](https://www.twilio.com/azure)：10 美元的 Twilio 信用额度。 此 Twilio 信用可应用于任何 Twilio 使用（10 美元信用等价于发送多达 1,000 条 SMS 消息或接收长达 1000 分钟的入站语音，具体取决电话号码和消息或呼叫目标的位置）。 兑换此 Twilio 信用额度, 开始在[twilio.com/azure](https://twilio.com/azure)。

Twilio 是一种现用现付服务。 没有设置费用，并且可以随时关闭帐户。 可以在 [Twilio 定价](https://www.twilio.com/voice/pricing)中找到更多详细信息。

## <a id="Concepts"></a>概念
Twilio API 是一个为应用程序提供语音和 SMS 功能的 RESTful API。 提供多种语言的客户端库;有关列表, 请参阅[TWILIO API 库][twilio_libraries]。

Twilio API 的关键方面是 Twilio 谓词和 Twilio 标记语言 (TwiML)。

### <a id="Verbs"></a>Twilio 谓词
API 利用了 Twilio 谓词；例如， **&lt;Say&gt;** 谓词指示 Twilio 在呼叫时传递语音消息。

下面是 Twilio 谓词的列表。  通过 [Twilio 标记语言文档](https://www.twilio.com/docs/api/twiml)了解其他谓词和功能。

* `<Dial>`：将呼叫方连接到其他电话。
* `<Gather>`：收集通过电话按键输入的数字。
* `<Hangup>`：结束呼叫。
* `<Play>`：播放音频文件。
* `<Pause>`：安静地等待指定的秒数。
* `<Record>`：录制呼叫方的声音并返回包含该录音的文件 URL。
* `<Redirect>`：将对呼叫或短信的控制转移到其他 URL 上的 TwiML。
* `<Reject>`：拒绝对 Twilio 号码的传入呼叫且无需付费
* `<Say>`：将文本转换为通话语音。
* `<Sms>`：发送短信。

### <a name="twiml"></a>TwiML
TwiML 是一组基于 XML 的指令，这些指令以用于指示 Twilio 如何处理呼叫或 SMS 的 Twilio 谓词为基础。

例如，以下 TwiML 将文本 **Hello World** 转换为语音。

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<Response>
  <Say>Hello World</Say>
</Response>
```

当应用程序调用 Twilio API 时，某个 API 参数将为返回 TwiML 响应的 URL。 在开发过程中，可以使用 Twilio 提供的 URL 来提供应用程序所使用的 TwiML 响应。 还可以托管自己的 URL 来生成 TwiML 响应，也可以选择使用 **TwiMLResponse** 对象。

有关 Twilio 谓词、其属性和 TwiML 的详细信息, 请参阅[TwiML][twiml]。 有关 Twilio API 的其他信息, 请参阅[TWILIO api][twilio_api]。

## <a id="CreateAccount"></a>创建 Twilio 帐户
准备好获取 Twilio 帐户后, 请在[试用 Twilio][try_twilio]上注册。 可以先使用免费帐户，以后再升级帐户。

注册 Twilio 帐户时，将收到帐户 ID 和身份验证令牌。 需要二者才能发起 Twilio API 呼叫。 为了防止对帐户进行未经授权的访问，请保护身份验证令牌。 你的帐户 ID 和身份验证令牌可在[Twilio 帐户页][twilio_account]上分别在标记为 "**帐户 SID** " 和 "**身份验证令牌**" 的字段中查看。

## <a id="create_app"></a>创建 Azure 应用程序
托管启用 Twilio 的应用程序的 Azure 应用程序与其他任何 Azure 应用程序没有区别。 需添加 Twilio .NET 库并将角色配置为使用 Twilio .NET 库。
有关创建初始 Azure 项目的信息, 请参阅[使用 Visual Studio 创建 Azure 项目][vs_project]。

## <a id="configure_app"></a>将应用程序配置为使用 Twilio 库
Twilio 提供了一系列可包装 Twilio 各个方面的 .NET 帮助程序库，因此，能够以简单且轻松地方式与 Twilio REST API 和 Twilio 客户端进行交互，从而生成 TwiML 响应。

Twilio 为 .NET 开发人员提供了 5 个库：

| 库 | 描述 |
| --- | --- |
| Twilio.API | 可在友好的 .NET 库中包装 Twilio REST API 的核心 Twilio 库。 此库可用于 .NET、Silverlight 和 Windows Phone 7。 |
| Twilio.TwiML | 可提供一种 .NET 友好方式来生成 TwiML 标记。 |
| Twilio.MVC | 对于使用 ASP.NET MVC 的开发人员，此库包括 TwilioController、TwiML ActionResult，及请求验证特性。 |
| Twilio.WebMatrix | 对于使用 Microsoft 免费 WebMatrix 开发工具的开发人员，此库包含适用于各种 Twilio 操作的 Razor 语法帮助程序。 |
| Twilio.Client.Capability | 包含可用于 Twilio 客户端 JavaScript SDK 的 Capability 令牌生成器。 |

> [!Important]
> 所有库都需要 .NET 3.5、Silverlight 4 或者 Windows Phone 7 或更高版本。

本指南中提供的示例使用 Twilio.API 库。

这些库可以[使用 NuGet 程序包管理器扩展进行安装](https://www.twilio.com/docs/csharp/install)，该扩展适用于 Visual Studio 2010 到 2015。  源代码托管在[GitHub][twilio_github_repo]上, 它包括一个包含有关使用这些库的完整文档的 Wiki。

默认情况下，Microsoft Visual Studio 2010 安装 1.2 版的 NuGet。 安装 Twilio 库需要 1.6 或更高版本的 NuGet。 有关安装或更新 NuGet 的信息, 请[https://nuget.org/][nuget]参阅。

> [!NOTE]
> 若要安装 NuGet 的最新版本，必须首先使用 Visual Studio Extension Manager 卸载已加载的版本。 为此，必须以管理员的身份运行 Visual Studio。 否则，“卸载”按钮将处于禁用状态。
>
>

### <a id="use_nuget"></a>向 Visual Studio 项目添加 Twilio 库：
1. 在 Visual Studio 中打开解决方案。
2. 右键单击“引用”。
3. 单击“管理 NuGet 包”。
4. 单击“联机”。
5. 在联机搜索框中，键入 *twilio*。
6. 单击 Twilio 程序包对应的“安装”。

## <a id="howto_make_call"></a>如何：发起传出呼叫
以下代码演示如何使用 **CallResource** 类发起传出呼叫。 此代码还使用 Twilio 提供的网站返回 Twilio 标记语言 (TwiML) 响应。 用自己的值替换“被呼叫方”和“呼叫方”电话号码，并确保在运行代码之前验证 Twilio 帐户的“呼叫方”电话号码。

```csharp
// Use your account SID and authentication token instead
// of the placeholders shown here.
const string accountSID = "your_twilio_account";
const string authToken = "your_twilio_authentication_token";

// Initialize the TwilioClient.
TwilioClient.Init(accountSID, authToken);

// Use the Twilio-provided site for the TwiML response.
var url = "https://twimlets.com/message";
url = $"{url}?Message%5B0%5D=Hello%20World";

// Set the call From, To, and URL values to use for the call.
// This sample uses the sandbox number provided by
// Twilio to make the call.
var call = CallResource.Create(
    to: new PhoneNumber("+NNNNNNNNNN"),
    from: new PhoneNumber("NNNNNNNNNN"),
    url: new Uri(url));
    }
```

有关传入到**callresource.create**方法的参数的详细信息, 请参阅[https://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls]。

如前所述，此代码使用 Twilio 提供的网站返回 TwiML 响应。 可以改用自己的站点来提供 TwiML 响应。 有关更多信息，请参阅[如何：从自己的网站提供 TwiML 响应](#howto_provide_twiml_responses)。

## <a id="howto_send_sms"></a>如何：发送短信
以下屏幕截图演示如何使用 **MessageResource** 类发送短信。 “呼叫方”号码由 Twilio 提供，供试用帐户用来发送短信。 在运行代码前，必须验证 Twilio 帐户的“被呼叫方”号码。

```csharp
// Use your account SID and authentication token instead
// of the placeholders shown here.
const string accountSID = "your_twilio_account";
const string authToken = "your_twilio_authentication_token";

// Initialize the TwilioClient.
TwilioClient.Init(accountSID, authToken);

try
{
    // Send an SMS message.
    var message = MessageResource.Create(
        to: new PhoneNumber("+12069419717"),
        from: new PhoneNumber("+14155992671"),
        body: "This is my SMS message.");
}
catch (TwilioException ex)
{
    // An exception occurred making the REST call
    Console.WriteLine(ex.Message);
}
```

## <a id="howto_provide_twiml_responses"></a>如何：从自己的网站提供 TwiML 响应
当应用程序启动对 Twilio API 的调用时（例如通过 **CallResource.Create** 方法），Twilio 会将请求发送到应该返回 TwiML 响应的 URL。 [如何：发出传出呼叫](#howto_make_call) , 使用 Twilio 提供的 URL [https://twimlets.com/message][twimlet_message_url]返回响应。

> [!NOTE]
> 虽然 TwiML 专供 Web 服务使用，但可以在浏览器中查看 TwiML。 例如, [https://twimlets.com/message][twimlet_message_url]单击以查看空`<Response>` 元素; 如另一个示例`<Response>` , 请单击[https://twimlets.com/message?Message%5B0%5D=Hello%20World](https://twimlets.com/message?Message%5B0%5D=Hello%20World) &gt; 以查看包含 contains &lt; 元素的元素。
>

可以创建自己的返回 HTTP 响应的 URL 网站，而不用依赖 Twilio 提供的 URL。 可以使用任何语言创建返回 HTTP 响应的站点。 本主题假设要从 ASP.NET 一般处理程序承载该 URL。

以下 ASP.NET 处理程序将生成在呼叫时表述 **Hello World** 的 TwiML 响应。

```csharp
using System.Text;
using System.Web;

namespace WebRole1
{
    /// <summary>
    /// Summary description for Handler1
    /// </summary>
    public class Handler1 : IHttpHandler
    {
        public void ProcessRequest(HttpContext context)
        {
            const string twiMLResponse =
                "<Response><Say>Hello World.</Say></Response>";

            context.Response.Clear();
            context.Response.ContentType = "text/xml";
            context.Response.ContentEncoding = Encoding.UTF8;
            context.Response.Write(twiMLResponse);
            context.Response.End();
        }

        public bool IsReusable
        {
            get
            {
                return false;
            }
        }
    }
}
```
    
如上面的示例中所示，TwiML 响应只是一个 XML 文档。 Twilio.TwiML 库包含将生成 TwiML 的类。 以下示例将生成与上面所示相同的响应，但该响应会使用 **VoiceResponse** 类。

```csharp
using System.Web;
using Twilio.TwiML;

namespace WebRole1
{
    /// <summary>
    /// Summary description for Handler1
    /// </summary>
    public class Handler1 : IHttpHandler
    {

        public void ProcessRequest(HttpContext context)
        {
            var twiml = new VoiceResponse();
            twiml.Say("Hello World.");

            context.Response.Clear();
            context.Response.ContentType = "text/xml";
            context.Response.Write(twiml.ToString());
            context.Response.End();
        }

        public bool IsReusable
        {
            get
            {
                return false;
            }
        }
    }
}
```

有关 TwiML 的详细信息，请参阅 [https://www.twilio.com/docs/api/twiml](https://www.twilio.com/docs/api/twiml)。

在设置提供 TwiML 响应的方法后，可将此 URL 传入 **CallResource.Create** 方法中。 例如，如果将名为 MyTwiML 的 Web 应用程序部署到 Azure 云服务，则 ASP.NET 处理程序的名称将为 mytwiml.ashx，并且可将 URL 传递到 **CallResource.Create**，如以下代码示例中所示：

```csharp
// This sample uses the sandbox number provided by Twilio to make the call.
// Place the call.
var call = CallResource.Create(
    to: new PhoneNumber("+NNNNNNNNNN"),
    from: new PhoneNumber("NNNNNNNNNN"),
    url: new Uri("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.ashx"));
    }
```

有关在 Azure 上将 Twilio 与 ASP.NET 配合使用的其他信息, 请参阅[如何在 azure 的 web 角色中使用 Twilio 发起电话呼叫][howto_phonecall_dotnet]。

[!INCLUDE [twilio-additional-services-and-next-steps](../includes/twilio-additional-services-and-next-steps.md)]

[howto_phonecall_dotnet]: partner-twilio-cloud-services-dotnet-phone-call-web-role.md

[twimlet_message_url]: https://twimlets.com/message

[twilio_rest_making_calls]: https://www.twilio.com/docs/api/rest/making-calls

[vs_project]:https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-azure-project-create
[nuget]:https://nuget.org/
[twilio_github_repo]:https://github.com/twilio/twilio-csharp

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/console
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified
