---
title: 如何从 Twilio (PHP) 发起电话呼叫 | Microsoft Docs
description: 了解如何在 Azure 中使用 Twilio API 服务发起电话呼叫和发送短信。 示例用于 PHP 应用程序。
documentationcenter: php
services: ''
author: georgewallace
ms.assetid: 44e35adc-be06-4700-beee-8c9e2c20c540
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: fb1623c4a409f1c6cba94bad56d773e166d2b182
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69637322"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-php-application-on-azure"></a>如何在 Azure 的 PHP 应用程序中使用 Twilio 发起电话呼叫
以下示例演示了如何使用 Twilio 从 Azure 中托管的 PHP 网页发起呼叫。 最终的应用程序将提示用户输入电话呼叫值, 如以下屏幕截图所示。

![使用 Twilio 和 PHP 的 Azure 呼叫窗体][twilio_php]

需要执行以下操作来使用本主题中的代码：

1. 从[Twilio 控制台][twilio_console]获取 Twilio 帐户和身份验证令牌。 若要开始 Twilio, 请评估价格[https://www.twilio.com/pricing][twilio_pricing]。 你可以在[https://www.twilio.com/try-twilio][try_twilio]注册试用帐户。
2. 获取[用于 PHP 的 Twilio 库](https://github.com/twilio/twilio-php)，或将其作为 PEAR 包安装。 有关详细信息，请参阅[自述文件](https://github.com/twilio/twilio-php/blob/master/README.md)。
3. 安装 Azure SDK for PHP。 
<!-- For an overview of the SDK and instructions on installing it, see [Set up the Azure SDK for PHP](app-service-web/web-sites-php-mysql-deploy-use-git.md) -->

## <a name="create-a-web-form-for-making-a-call"></a>创建用于发起呼叫的 Web 窗体
以下 HTML 代码演示了如何生成检索用于发起呼叫的用户数据的网页 (**callform.html**)：

```html
<!DOCTYPE html>
<html>
<head>
  <title>Automated call form</title>
</head>
<body>
  <h1>Automated Call Form</h1>
  <p>Fill in all fields and click <b>Make this call</b>.</p>
  <form action="makecall.php" method="post">
    <table>
      <tr>
        <td>To:</td>
        <td><input name="callTo" size="50" type="text" value=""></td>
      </tr>
      <tr>
        <td>From:</td>
        <td><input name="callFrom" size="50" type="text" value=""></td>
      </tr>
      <tr>
        <td>Call message:</td>
        <td><input name="callText" size="100" type="text" value="Hello. This is the call text. Good bye."></td>
      </tr>
      <tr>
        <td colspan="2"><input type="submit" value="Make this call"></td>
      </tr>
    </table>
  </form><br>
</body>
</html>
```

## <a name="create-the-code-to-make-the-call"></a>创建用于发起呼叫的代码
以下代码演示如何生成在用户提交 **callform.html** 显示的表单时调用的 **makecall.php**。 下面显示的代码将创建呼叫消息和生成呼叫。 此外, 请确保使用[Twilio 控制台][twilio_console]中的 Twilio 帐户和身份验证令牌, 而不是分配给以下代码中 **$sid**和 **$token**的占位符值。

```html
<html>
<head><title>Making call...</title></head>
<body>
<p>Your call is being made.</p>

<?php
require_once 'path/to/vendor/autoload.php';

$sid   = "your_account_sid";
$token = "your_authentication_token";

$from_number = $_POST['callFrom']; // Calls must be made from a registered Twilio number.
$to_number   = $_POST['callTo'];
$message     = $_POST['callText'];

$client = new Twilio\Rest\Client($sid, $token);

$call = $client->calls->create(
            $to_number,
            $from_number,
            array('url' => https://twimlets.com/message?Message=' . urlencode($message))
        );

echo "Call status: " . $call->status . "<br />";
echo "URI resource: " . $call->uri . "<br />";
?>
</body>
</html>
```

除了发起呼叫外，**makecall.php** 还显示一些呼叫元数据，如下图中所示。 有关调用元数据的详细信息, [https://www.twilio.com/docs/api/rest/call#instance-properties][twilio_call_properties]请参阅。

![使用 Twilio 和 PHP 的 Azure 呼叫响应][twilio_php_response]

## <a name="run-the-application"></a>运行应用程序
下一步是[使用 Git 将应用程序部署到 Azure Web 应用](app-service/app-service-web-get-started-php.md)（尽管并非该文的所有这些信息都相关）。 

## <a name="next-steps"></a>后续步骤
提供此代码是为了演示通过 Azure 上的 PHP 使用 Twilio 的基本功能。 在生产中部署到 Azure 之前，可能希望添加更多错误处理或其他功能。 例如：

* 可以使用 Azure 存储 Blob 或 SQL 数据库存储电话号码和呼叫文本，而不使用 Web 窗体。 有关使用 PHP 中的 Azure 存储 blob 的信息, 请参阅在[Php 应用程序中使用 Azure 存储][howto_blob_storage_php]。 有关在 PHP 中使用 SQL 数据库的信息, 请参阅将[Sql 数据库用于 Php 应用程序][howto_sql_azure_php]。
* **Makecall.jsp**代码使用 Twilio 提供的 URL ([https://twimlets.com/message][twimlet_message_url]) 来提供 Twilio 标记语言 (TwiML) 响应, 通知 Twilio 如何继续进行呼叫。 例如，返回的 TwiML 可能包含 `<Say>` 谓词，该谓词生成了与呼叫接收人的谈话的文本。 您可以生成自己的服务来响应 Twilio 的请求, 而不使用 Twilio 提供的 URL;有关详细信息, 请参阅[如何通过 PHP 使用 Twilio 实现语音和短信功能][howto_twilio_voice_sms_php]。 有关 TwiML 的详细信息[https://www.twilio.com/docs/api/twiml][twiml], 请参阅, 还可以在[https://www.twilio.com/docs/api/twiml/say][twilio_say]中`<Say>` 找到有关和其他 Twilio 谓词的详细信息。
* 阅读 Twilio 安全准则, 网址[https://www.twilio.com/docs/security][twilio_docs_security]为。

有关 Twilio 的其他信息, 请[https://www.twilio.com/docs][twilio_docs]参阅。

## <a name="see-also"></a>请参阅
* [如何通过 PHP 使用 Twilio 实现语音和 SMS 功能](partner-twilio-php-how-to-use-voice-sms.md)

[twilio_console]: https://www.twilio.com/console
[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/docs/api
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified
[twimlet_message_url]: https://twimlets.com/message
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api_service]: https://api.twilio.com
[build_php_azure_app]: http://azurephp.interoperabilitybridges.com/articles/build-and-deploy-a-windows-azure-php-application
[howto_twilio_voice_sms_php]: partner-twilio-php-how-to-use-voice-sms.md
[howto_blob_storage_php]: https://azure.microsoft.com/documentation/articles/storage-php-how-to-use-blobs/
[howto_sql_azure_php]: https://azure.microsoft.com/documentation/articles/sql-database-php-how-to-use/
[twilio_call_properties]: https://www.twilio.com/docs/api/rest/call#instance-properties
[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_php]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg
[twilio_php_response]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg
[twilio_php_github]: https://github.com/twilio/twilio-php
