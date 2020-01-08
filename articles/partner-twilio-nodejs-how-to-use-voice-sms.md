---
title: 在 Azure 中使用 Twilio for Voice、VoIP 和 SMS 消息传递
description: 了解如何在 Azure 中使用 Twilio API 服务发起电话呼叫和发送短信。 代码示例用 Node.js 编写。
services: ''
documentationcenter: nodejs
author: georgewallace
ms.assetid: f558cbbd-13d2-416f-b9b1-33a99c426af9
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 164bedffcf9a1aca9f1fa46dea254fb928abcf04
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69637269"
---
# <a name="using-twilio-for-voice-voip-and-sms-messaging-in-azure"></a>在 Azure 中使用 Twilio for Voice、VoIP 和 SMS 消息传递
本指南演示如何构建与 Azure 上的 Twilio 和 node.js 进行通信的应用程序。

<a id="whatis"/>

## <a name="what-is-twilio"></a>什么是 Twilio？
Twilio 是一种 API 平台，借助于该平台，开发人员能够轻松地收发电话呼叫、收发短信以及将 VoIP 呼叫嵌入到基于浏览器的应用程序和本机移动应用程序中。 在我们深入探讨其功能之前先简要看一下它的工作方式。

### <a name="receiving-calls-and-text-messages"></a>接收呼叫和短信
Twilio 允许开发人员[购买可编程电话号码][purchase_phone], 该号码可用于发送和接收电话和短信。 在某一 Twilio 号码接收到传入呼叫或文本时，Twilio 将向 Web 应用程序发送一个 HTTP POST 或 GET 请求，请求提供有关如何处理该呼叫或文本的说明。 服务器将使用[TwiML][twiml]响应 TWILIO 的 HTTP 请求, 这是一组简单的 XML 标记, 其中包含有关如何处理调用或文本的说明。 稍后会显示 TwiML 的示例。

### <a name="making-calls-and-sending-text-messages"></a>发出呼叫和发送短信
通过向 Twilio Web 服务 API 发出 HTTP 请求，开发人员可以发送短信或发起传出电话呼叫。 对于传出呼叫，开发人员必须还指定一个 URL，该 URL 返回有关在其连接后如何处理该传出呼叫的 TwiML 说明。

### <a name="embedding-voip-capabilities-in-ui-code-javascript-ios-or-android"></a>在 UI 代码中嵌入 VoIP 功能（JavaScript、iOS 或 Android）
Twilio 提供一个客户端 SDK，它可以将任何桌面 Web 浏览器、iOS 应用程序或 Android 应用程序转换为 VoIP 电话。 在本文中，我们将重点介绍如何在浏览器中使用 VoIP 呼叫。 除了在浏览器中运行的 *Twilio JavaScript SDK* 以外，还必须使用服务器端应用程序（我们的 node.js 应用程序）向 JavaScript 客户端发布一个“功能标记”。 你可以[在 Twilio dev 博客上][voipnode]阅读有关将 VoIP 与 node.js 配合使用的详细信息。

<a id="signup"/>

## <a name="sign-up-for-twilio-microsoft-discount"></a>注册 Twilio (Microsoft Discount)
使用 Twilio services 之前, 必须首先[注册帐户][signup]。 Microsoft Azure 客户收到特别折扣-[请务必在此处注册][signup]!

<a id="azuresite"/>

## <a name="create-and-deploy-a-nodejs-azure-website"></a>创建和部署 node.js Azure 网站
接下来，需要创建在 Azure 上运行的 node.js 网站。 [此处提供了有关执行此操作的正式文档][azure_new_site]。 概括地说，将执行以下操作：

* 如果没有 Azure 帐户，请注册 Azure 帐户
* 使用 Azure 管理控制台创建一个新网站
* 添加源代码管理支持（假定使用 Git）
* 使用一个简单的 node.js Web 应用程序创建文件 `server.js`
* 将这个简单的应用程序部署到 Azure

<a id="twiliomodule"/>

## <a name="configure-the-twilio-module"></a>配置 Twilio 模块
接下来，我们将开始编写一个可使用 Twilio API 的简单的 node.js 应用程序。 在开始前，我们需要配置我们的 Twilio 帐户凭据。

### <a name="configuring-twilio-credentials-in-system-environment-variables"></a>在系统环境变量中配置 Twilio 凭据
为了向 Twilio 后端发出已验证了身份的请求，我们需要我们的帐户 SID 和授权标记，它起到为我们的 Twilio 帐户设置的用户名和密码的功能。 将它们配置为可用于 Azure 中的节点模块的最安全方式是通过系统环境变量，可直接在 Azure 管理控制台中设置这些变量。

选择你的 node.js 网站，并单击“配置”链接。  如果向下滚动一点，会看到一个区域，可以在该区域中为应用程序设置配置属性。  按如下所示输入你的 Twilio 帐户凭据 ([在你的 Twilio 控制台上找到][twilio_console])- `TWILIO_ACCOUNT_SID`请`TWILIO_AUTH_TOKEN`确保将它们分别命名为:

![Azure 管理控制台][azure-admin-console]

在配置了这些变量后，在 Azure 控制台中重新启动应用程序。

### <a name="declaring-the-twilio-module-in-packagejson"></a>在 package.json 中声明 Twilio 模块
接下来，请创建一个 package.json，以便通过 [npm] 管理节点模块依赖项。 在与在 *Azure/node.js* 教程中创建的 `server.js` 文件所处的相同级别上，创建一个名为 `package.json` 的文件。  在此文件中，放置以下代码：

```json
{
  "name": "application-name",
  "version": "0.0.1",
  "private": true,
  "scripts": {
    "start": "node server"
  },
  "dependencies": {
    "body-parser": "^1.16.1",
    "ejs": "^2.5.5",
    "errorhandler": "^1.5.0",
    "express": "^4.14.1",
    "morgan": "^1.8.1",
    "twilio": "^2.11.1"
  }
}
```

这会将 twilio 模块声明为一个依赖项, 以及常见的[Express web 框架][express]和 index.ejs 模板引擎。  好了，现在全都设置好了 - 我们就来编写一些代码吧！

<a id="makecall"/>

## <a name="make-an-outbound-call"></a>发起传出呼叫
我们将创建一个简单的表单，它将向我们选择的号码发出呼叫。 打开 `server.js`，并输入以下代码。 请注意，在出现“CHANGE_ME”的地方放置 azure 网站的名称：

```javascript
// Module dependencies
const express = require('express');
const path = require('path');
const http = require('http');
const twilio = require('twilio');
const logger = require('morgan');
const bodyParser = require('body-parser');
const errorHandler = require('errorhandler');
const accountSid = process.env.TWILIO_ACCOUNT_SID;
const authToken = process.env.TWILIO_AUTH_TOKEN;
// Create Express web application
const app = express();

// Express configuration
app.set('port', process.env.PORT || 3000);
app.set('views', __dirname + '/views');
app.set('view engine', 'ejs');
app.use(logger('tiny'));
app.use(bodyParser.urlencoded({ extended: false }))
app.use(bodyParser.json())
app.use(express.static(path.join(__dirname, 'public')));

if (app.get('env') !== 'production') {
  app.use(errorHandler());
}

// Render an HTML user interface for the application's home page
app.get('/', (request, response) => response.render('index'));

// Handle the form POST to place a call
app.post('/call', (request, response) => {
  var client = twilio(accountSid, authToken);

  client.makeCall({
    // make a call to this number
    to:request.body.number,

    // Change to a Twilio number you bought - see:
    // https://www.twilio.com/console/phone-numbers/incoming
    from:'+15558675309',

    // A URL in our app which generates TwiML
    // Change "CHANGE_ME" to your app's name
    url:'https://CHANGE_ME.azurewebsites.net/outbound_call'
  }, () => {
      // Go back to the home page
      response.redirect('/');
  });
});

// Generate TwiML to handle an outbound call
app.post('/outbound_call', (request, response) => {
  var twiml = new twilio.TwimlResponse();

  // Say a message to the call's receiver
  twiml.say('hello - thanks for checking out Twilio and Azure', {
      voice:'woman'
  });

  response.set('Content-Type', 'text/xml');
  response.send(twiml.toString());
});

// Start server
app.listen(app.get('port'), function(){
  console.log(`Express server listening on port ${app.get('port')}`);
});
```

接下来，创建名为 `views` 的目录 - 在该目录内，创建包含以下内容的名为 `index.ejs` 的文件：

```html
<!DOCTYPE html>
<html>
<head>
  <title>Twilio Test</title>
  <style>
    input { height:20px; width:300px; font-size:18px; margin:5px; padding:5px; }
  </style>
</head>
<body>
  <h1>Twilio Test</h1>
  <form action="/call" method="POST">
      <input placeholder="Enter a phone number" name="number"/>
      <br/>
      <input type="submit" value="Call the number above"/>
  </form>
</body>
</html>
```

现在，将网站部署到 Azure 并打开主页。 应该能够在文本字段中输入电话号码，并收到来自 Twilio 号码的呼叫！

<a id="sendmessage"/>

## <a name="send-an-sms-message"></a>发送 SMS 消息
现在，我们将设置一个用户界面并且构建用于发送短信的处理逻辑。 打开 `server.js`，然后将以下代码添加到对 `app.post` 的最后一个调用后：

```javascript
app.post('/sms', (request, response) => {
  const client = twilio(accountSid, authToken);

  client.sendSms({
      // send a text to this number
      to:request.body.number,

      // A Twilio number you bought - see:
      // https://www.twilio.com/console/phone-numbers/incoming
      from:'+15558675309',

      // The body of the text message
      body: request.body.message

  }, () => {
      // Go back to the home page
      response.redirect('/');
  });
});
```

在 `views/index.ejs` 中，在第一个表单下添加另一个表单，以便提交一个号码和一个短信：

```html
<form action="/sms" method="POST">
  <input placeholder="Enter a phone number" name="number"/>
  <br/>
  <input placeholder="Enter a message to send" name="message"/>
  <br/>
  <input type="submit" value="Send text to the number above"/>
</form>
```

将应用程序重新部署到 Azure，现在你应该能够提交该表单并且亲自（或者任何密友）发送短信了！

<a id="nextsteps"/>

## <a name="next-steps"></a>后续步骤
现在了解了使用 node.js 和 Twilio 生成进行通信的应用程序的基础知识。 但这些示例还只是涉及了 Twilio 和 node.js 的一些浅显的功能。 有关使用 Twilio 和 node.js 的详细信息，请参阅下列资源：

* [正式模块文档][docs]
* [有关具有 node.js 应用程序的 VoIP 的教程][voipnode]
* [Votr-具有 node.js 和 CouchDB 的实时 SMS 投票应用程序 (三个部分)][votr]
* [在浏览器中将编程与 node.js 结合在一起][pair]

我们希望你喜欢在 Azure 上使用 node.js 和 Twilio！

[purchase_phone]: https://www.twilio.com/console/phone-numbers/search
[twiml]: https://www.twilio.com/docs/api/twiml
[signup]: https://ahoy.twilio.com/azure
[azure_new_site]: app-service/app-service-web-get-started-nodejs.md
[twilio_console]: https://www.twilio.com/console
[npm]: https://npmjs.org
[express]: https://expressjs.com
[voipnode]: https://www.twilio.com/blog/2013/04/introduction-to-twilio-client-with-node-js.html
[docs]: https://www.twilio.com/docs/libraries/reference/twilio-node/
[votr]: https://www.twilio.com/blog/2012/09/building-a-real-time-sms-voting-app-part-1-node-js-couchdb.html
[pair]: https://www.twilio.com/blog/2013/06/pair-programming-in-the-browser-with-twilio.html
[azure-admin-console]: ./media/partner-twilio-nodejs-how-to-use-voice-sms/twilio_1.png
