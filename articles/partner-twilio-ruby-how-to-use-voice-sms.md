---
title: 如何使用 Twilio 实现语音和 SMS (Ruby) | Microsoft Docs
description: 了解如何在 Azure 中使用 Twilio API 服务发起电话呼叫和发送短信。 用 Ruby 编写的代码示例。
services: ''
documentationcenter: ruby
author: georgewallace
ms.assetid: 60e512f6-fa47-47c0-aedc-f19bb72a1158
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 4822e6feb29f5a17c653a60937b895ec584e0ee4
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69637205"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-ruby"></a>如何通过 Ruby 使用 Twilio 实现语音和 SMS 功能
本指南演示如何在 Azure 中使用 Twilio API 服务执行常见编程任务。 所涉及的任务包括发起电话呼叫和发送短信服务 (SMS) 消息。 有关 Twilio 以及在应用程序中使用语音和短信的详细信息，请参阅[后续步骤](#NextSteps)部分。

## <a id="WhatIs"></a>什么是 Twilio？
Twilio 是一种电话 Web 服务 API，可方便用户利用现有 Web 语言和技能生成语音和短信应用程序。 Twilio 属于第三方服务（而非 Azure 功能和 Microsoft 产品）。

利用 **Twilio 语音**，应用程序可以发起和接收电话呼叫。 应用程序可以使用 **Twilio SMS** 发出和接收 SMS 消息。 应用程序可以通过 **Twilio 客户端**使用现有 Internet 连接（包括移动连接）启用语音通信。

## <a id="Pricing"></a>Twilio 定价和特别优惠
[Twilio 定价][twilio_pricing]中提供了有关 Twilio 定价的信息。 Azure 客户收到[特别优惠][special_offer]: 免费信用额度为1000或1000入站分钟。 若要注册此优惠或获取详细信息, 请访问[https://ahoy.twilio.com/azure][special_offer]。  

## <a id="Concepts"></a>概念
Twilio API 是一个为应用程序提供语音和 SMS 功能的 RESTful API。 提供多种语言的客户端库;有关列表, 请参阅[TWILIO API 库][twilio_libraries]。

### <a id="TwiML"></a>TwiML
TwiML 是一组基于 XML 的指令，可指示 Twilio 如何处理呼叫或短信。

例如，以下 TwiML 将文本 **Hello World** 转换为语音。

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

所有 TwiML 文档都将 `<Response>` 作为其根元素。 可以在根元素中使用 Twilio 谓词定义应用程序的行为。

### <a id="Verbs"></a>TwiML 谓词
Twilio 谓词是指示 Twilio **执行**哪些操作的 XML 标记。 例如， **&lt;Say&gt;** 谓词指示 Twilio 在呼叫时传递语音消息。 

下面是 Twilio 谓词的列表。

* **&lt;Dial&gt;** ：将呼叫方连接到其他电话。
* **&lt;Gather&gt;** ：收集通过电话按键输入的数字。
* **&lt;Hangup&gt;** ：结束呼叫。
* **&lt;Play&gt;** ：播放音频文件。
* **&lt;Pause&gt;** ：安静地等待指定的秒数。
* **&lt;Record&gt;** ：录制呼叫方的声音并返回包含该录音的文件的 URL。
* **&lt;Redirect&gt;** ：将对呼叫或短信的控制转移到其他 URL 上的 TwiML。
* **&lt;Reject&gt;** ：拒绝对 Twilio 号码的传入呼叫且无需付费
* **&lt;Say&gt;** ：将文本转换为通话语音。
* **&lt;Sms&gt;** ：发送短信。

有关 Twilio 谓词、其属性和 TwiML 的详细信息, 请参阅[TwiML][twiml]。 有关 Twilio API 的其他信息, 请参阅[TWILIO api][twilio_api]。

## <a id="CreateAccount"></a>创建 Twilio 帐户
准备好获取 Twilio 帐户后, 请在[试用 Twilio][try_twilio]上注册。 可以先使用免费帐户，以后再升级帐户。

注册 Twilio 帐户时，将收到适用于应用程序的一个免费电话号码。 还会收到帐户 SID 和身份验证令牌。 需要二者才能发起 Twilio API 呼叫。 为了防止对帐户进行未经授权的访问，请保护身份验证令牌。 你的帐户 SID 和身份验证令牌可以在 "**帐户 sid** " 和 "**身份验证令牌**" 字段中的[Twilio 帐户页][twilio_account]上查看。

### <a id="VerifyPhoneNumbers"></a>验证电话号码
除了 Twilio 提供的号码外，还可以验证自己控制用于应用程序的号码（即手机号码或住宅电话号码）。 

有关如何验证电话号码的信息, 请参阅[管理号码][verify_phone]。

## <a id="create_app"></a>创建 Ruby 应用程序
使用 Twilio 服务且在 Azure 中运行的 Ruby 应用程序与任何其他使用 Twilio 服务的 Ruby 应用程序之间没有任何差别。 尽管 Twilio 服务是 RESTful 的, 并且可以通过多种方式从 Ruby 中调用, 本文将重点介绍如何将 Twilio 服务与用于[Ruby 的 Twilio 帮助程序库][twilio_ruby]一起使用。

首先,[设置一个新的 Azure LINUX VM][azure_vm_setup] , 作为新 Ruby web 应用程序的主机。 忽略创建 Rails 应用程序所涉及的步骤，只设置虚拟机。 确保创建的终结点的外部端口为 80，内部端口为 5000。

在下面的示例中, 我们将使用[Sinatra][sinatra], 这是一个非常简单的用于 Ruby 的 web 框架。 不过，当然可以将用于 Ruby 的 Twilio 帮助程序库与任何其他 Web 框架（包括 Ruby on Rails）一起使用。

通过 SSH 登录到新虚拟机并为新应用程序创建一个目录。 在该目录内创建一个名为 Gemfile 的文件并将以下代码复制到该文件中：

    source 'https://rubygems.org'
    gem 'sinatra'
    gem 'thin'

在命令行上，运行 `bundle install`。 这会安装上述依赖项。 接下来，创建名为 `web.rb` 的文件。 它将包含 Web 应用程序的代码。 将以下代码粘贴到该文件中：

    require 'sinatra'

    get '/' do
        "Hello Monkey!"
    end

此时，应该能够运行命令 `ruby web.rb -p 5000`。 这会在端口 5000 上启动一个小型 web 服务器。 通过访问为 Azure VM 设置的 URL，可以在浏览器中浏览到此应用程序。 在浏览器中打开 Web 应用程序后，即可开始生成 Twilio 应用程序。

## <a id="configure_app"></a>将应用程序配置为使用 Twilio
可以通过将 `Gemfile` 更新为包含以下行，将 Web 应用程序配置为使用 Twilio 库：

    gem 'twilio-ruby'

在命令行上，运行 `bundle install`。 现在打开 `web.rb` 并在开头添加下面一行：

    require 'twilio-ruby'

现在已经可以在 Web 应用程序中使用用于 Ruby 的 Twilio 帮助程序库。

## <a id="howto_make_call"></a>如何：发起传出呼叫
下面演示如何发起传出呼叫。 主要概念包括使用用于 Ruby 的 Twilio 帮助程序调用 REST API 以及呈现 TwiML。 用自己的值替换“呼叫方”和“被呼叫方”电话号码，并确保在运行代码之前验证 Twilio 帐户的“呼叫方”电话号码。

将此函数添加到 `web.md`：

    # Set your account ID and authentication token.
    sid = "your_twilio_account_sid";
    token = "your_twilio_authentication_token";

    # The number of the phone initiating the call.
    # This should either be a Twilio number or a number that you've verified
    from = "NNNNNNNNNNN";

    # The number of the phone receiving call.
    to = "NNNNNNNNNNN";

    # Use the Twilio-provided site for the TwiML response.
    url = "http://yourdomain.cloudapp.net/voice_url";

    get '/make_call' do
      # Create the call client.
      client = Twilio::REST::Client.new(sid, token);

      # Make the call
      client.account.calls.create(to: to, from: from, url: url)
    end

    post '/voice_url' do
      "<Response>
         <Say>Hello Monkey!</Say>
       </Response>"
    end

如果在浏览器中打开 `http://yourdomain.cloudapp.net/make_call`，会触发对 Twilio API 的调用以发起电话呼叫。 `client.account.calls.create` 中的前两个参数都相当容易理解：呼叫号码是 `from` 和呼叫号码是 `to`。 

第三个参数 (`url`) 是 Twilio 请求的 URL，以获取有关在连接呼叫后要执行的操作的指令。 在本例中，我们设置的 URL (`http://yourdomain.cloudapp.net`) 会返回一个简单 TwiML 文档，并使用 `<Say>` 谓词进行一些文本到语音转换，以便向接收呼叫的人员说出“Hello Monkey”。

## <a id="howto_receive_sms"></a>如何：接收短信
在前面的示例中，我们发起了一个**传出**电话呼叫。 这一次，我们使用在注册过程中 Twilio 提供的电话号码来处理**传入**的 SMS 消息。

首先, 请登录到[Twilio 仪表板][twilio_account]。 单击顶部导航栏中的“Numbers”（号码），然后单击提供给 Twilio 号码。 会看到两个可以配置的 URL。 一个语音请求 URL 和一个短信请求 URL。 每当有人拨打号码或向号码发送短信时，Twilio 就会调用这些 URL。 这些 URL 也称为“Web 挂钩”。

我们想要处理传入的 SMS 消息，因此需要将 URL 更新为 `http://yourdomain.cloudapp.net/sms_url`。 继续操作并单击页面底部的“Save Changes”（保存更改）。 现在，回到 `web.rb`，对应用程序进行编程以处理此更改：

    post '/sms_url' do
      "<Response>
         <Message>Hey, thanks for the ping! Twilio and Azure rock!</Message>
       </Response>"
    end

进行更改后，确保重新启动 Web 应用程序。 现在，拿出手机，向 Twilio 号码发送一条短信。 应该会立即收到短信响应，内容为“Hey, thanks for the ping! Twilio and Azure rock!”。

## <a id="additional_services"></a>如何：使用其他 Twilio 服务
除了此处所示的示例之外，Twilio 还提供了基于 Web 的 API，可通过这些 API 从 Azure 应用程序中使用其他 Twilio 功能。 有关完整详细信息, 请参阅[TWILIO API 文档][twilio_api_documentation]。

### <a id="NextSteps"></a>后续步骤
现在，已了解 Twilio 服务的基础知识，单击下面的链接可以了解详细信息：

* [Twilio 安全准则][twilio_security_guidelines]
* [Twilio 操作方法和示例代码][twilio_howtos]
* [Twilio 快速入门教程][twilio_quickstarts] 
* [GitHub 上的 Twilio][twilio_on_github]
* [与 Twilio 支持人员交流][twilio_support]

[twilio_ruby]: https://www.twilio.com/docs/ruby/install





[twilio_pricing]: https://www.twilio.com/pricing
[special_offer]: https://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: https://www.twilio.com/api
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
[sinatra]: http://www.sinatrarb.com/
[azure_vm_setup]: https://docs.microsoft.com/azure/virtual-machines/linux/classic/ruby-rails-web-app
