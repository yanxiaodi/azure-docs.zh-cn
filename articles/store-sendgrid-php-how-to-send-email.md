---
title: 如何使用 SendGrid 电子邮件服务 (PHP) | Microsoft Docs
description: 了解如何在 Azure 上使用 SendGrid 电子邮件服务发送电子邮件。 采用 PHP 编写的代码示例。
documentationcenter: php
services: ''
manager: sendgrid
editor: mollybos
author: thinkingserious
ms.assetid: 51a9928a-4c9e-4b0a-aca8-9a408aeb6f47
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 10/30/2014
ms.author: erikre
ms.reviewer: elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork; matt.bernier@sendgrid.com
ms.openlocfilehash: b3a9fee09d1eac6fb4d716af83c348cb2c21f7a9
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2019
ms.locfileid: "67870913"
---
# <a name="how-to-use-the-sendgrid-email-service-from-php"></a>如何通过 PHP 使用 SendGrid 电子邮件服务

本指南演示了如何在 Azure 上使用 SendGrid 电子邮件服务执行常见编程任务。 通过 PHP 编写示例。
所涉及的任务包括**创建电子邮件**、**发送电子邮件**以及**添加附件**。 有关 SendGrid 和发送电子邮件的详细信息，请参阅[后续步骤](#next-steps)部分。

## <a name="what-is-the-sendgrid-email-service"></a>什么是 SendGrid 电子邮件服务？
SendGrid 是一项[基于云的电子邮件服务]，该服务提供了可靠的[事务电子邮件传递]、伸缩性、实时分析以及可用于简化自定义集成的灵活的 API。 常见 SendGrid 使用方案包括：

* 自动向客户发送收据
* 管理用于每月向客户发送电子传单和特惠产品/服务的通讯组列表
* 收集诸如已阻止的电子邮件和客户响应性等项目的实时度量值
* 生成用于帮助确定趋势的报告
* 转发客户查询
* 以电子邮件的形式从应用程序发送通知

有关详细信息，请参阅 [https://sendgrid.com][https://sendgrid.com]。

## <a name="create-a-sendgrid-account"></a>创建 SendGrid 帐户

[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="using-sendgrid-from-your-php-application"></a>从 PHP 应用程序中使用 SendGrid

在 Azure PHP 应用程序中使用 SendGrid 时不需要特殊配置或编码。 由于 SendGrid 是一项服务，可使用从本地应用程序访问该服务的方式从云应用程序中访问该服务。

## <a name="how-to-send-an-email"></a>如何：发送电子邮件

可以使用 SMTP 或由 SendGrid 提供的 Web API 发送电子邮件。

### <a name="smtp-api"></a>SMTP API

若要使用 SendGrid SMTP API 发送电子邮件，请使用 Swift Mailer  ，这是用于从 PHP 应用程序中发送电子邮件的基于组件的库。 可以下载 [Swift Mailer 库](https://swiftmailer.symfony.com/) v5.3.0（使用 [编辑器] 安装 Swift Mailer）。 利用库发送电子邮件涉及创建 `Swift\_SmtpTransport`、`Swift\_Mailer` 和 `Swift\_Message` 类的实例，设置适当的属性以及调用 `Swift\_Mailer::send` 方法。

```php
<?php
 include_once "vendor/autoload.php";
 /*
  * Create the body of the message (a plain-text and an HTML version).
  * $text is your plain-text email
  * $html is your html version of the email
  * If the receiver is able to view html emails then only the html
  * email will be displayed
  */
 $text = "Hi!\nHow are you?\n";
 $html = "<html>
       <head></head>
       <body>
           <p>Hi!<br>
               How are you?<br>
           </p>
       </body>
       </html>";
 // This is your From email address
 $from = array('someone@example.com' => 'Name To Appear');
 // Email recipients
 $to = array(
       'john@contoso.com'=>'Destination 1 Name',
       'anna@contoso.com'=>'Destination 2 Name'
 );
 // Email subject
 $subject = 'Example PHP Email';

 // Login credentials
 $username = 'yoursendgridusername';
 $password = 'yourpassword';

 // Setup Swift mailer parameters
 $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
 $transport->setUsername($username);
 $transport->setPassword($password);
 $swift = Swift_Mailer::newInstance($transport);

 // Create a message (subject)
 $message = new Swift_Message($subject);

 // attach the body of the email
 $message->setFrom($from);
 $message->setBody($html, 'text/html');
 $message->setTo($to);
 $message->addPart($text, 'text/plain');

 // send message
 if ($recipients = $swift->send($message, $failures))
 {
     // This will let us know how many users received this message
     echo 'Message sent out to '.$recipients.' users';
 }
 // something went wrong =(
 else
 {
     echo "Something went wrong - ";
     print_r($failures);
 }
```

### <a name="web-api"></a>Web API
使用 PHP 的[卷函数][curl function]通过 SENDGRID Web API 发送电子邮件。

```php
<?php

 $url = 'https://api.sendgrid.com/';
 $user = 'USERNAME';
 $pass = 'PASSWORD';

 $params = array(
      'api_user' => $user,
      'api_key' => $pass,
      'to' => 'john@contoso.com',
      'subject' => 'testing from curl',
      'html' => 'testing body',
      'text' => 'testing body',
      'from' => 'anna@contoso.com',
   );

 $request = $url.'api/mail.send.json';

 // Generate curl request
 $session = curl_init($request);

 // Tell curl to use HTTP POST
 curl_setopt ($session, CURLOPT_POST, true);

 // Tell curl that this is the body of the POST
 curl_setopt ($session, CURLOPT_POSTFIELDS, $params);

 // Tell curl not to return headers, but do return the response
 curl_setopt($session, CURLOPT_HEADER, false);
 curl_setopt($session, CURLOPT_RETURNTRANSFER, true);

 // obtain response
 $response = curl_exec($session);
 curl_close($session);

 // print everything out
 print_r($response);
```

SendGrid 的 Web API 与 REST API 非常相似，尽管它不是真正的 RESTful API，这是因为在大多数调用中，GET 和 POST 谓词是可互换的。

## <a name="how-to-add-an-attachment"></a>如何：添加附件

### <a name="smtp-api"></a>SMTP API

使用 SMTP API 发送附件涉及在用于通过 Swift Mailer 发送电子邮件的示例脚本中额外添加一行代码。

```php
<?php
 include_once "vendor/autoload.php";
 /*
  * Create the body of the message (a plain-text and an HTML version).
  * $text is your plain-text email
  * $html is your html version of the email
  * If the receiver is able to view html emails then only the html
  * email will be displayed
  */
 $text = "Hi!\nHow are you?\n";
  $html = "<html>
      <head></head>
      <body>
         <p>Hi!<br>
            How are you?<br>
         </p>
      </body>
      </html>";

 // This is your From email address
 $from = array('someone@example.com' => 'Name To Appear');

 // Email recipients
 $to = array(
      'john@contoso.com'=>'Destination 1 Name',
      'anna@contoso.com'=>'Destination 2 Name'
 );
 // Email subject
 $subject = 'Example PHP Email';

 // Login credentials
 $username = 'yoursendgridusername';
 $password = 'yourpassword';

 // Setup Swift mailer parameters
 $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
 $transport->setUsername($username);
 $transport->setPassword($password);
 $swift = Swift_Mailer::newInstance($transport);

 // Create a message (subject)
 $message = new Swift_Message($subject);

 // attach the body of the email
 $message->setFrom($from);
 $message->setBody($html, 'text/html');
 $message->setTo($to);
 $message->addPart($text, 'text/plain');
 $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName("file_name"));

 // send message
 if ($recipients = $swift->send($message, $failures))
 {
      // This will let us know how many users received this message
      echo 'Message sent out to '.$recipients.' users';
 }
 // something went wrong =(
 else
 {
      echo "Something went wrong - ";
      print_r($failures);
 }
```

额外的代码行如下所示：

```php
 $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName('file_name'));
```

该代码行对 `Swift\_Message` 对象调用 attach 方法并对 `Swift\_Attachment` 类使用静态方法 `fromPath` 以获取文件并将其附加到邮件。

### <a name="web-api"></a>Web API

使用 Web API 发送附件与使用 Web API 发送电子邮件非常相似。 但请注意，在以下示例中，参数数组必须包含该元素：

```php
    'files['.$fileName.']' => '@'.$filePath.'/'.$fileName
```

#### <a name="example"></a>示例

```php
<?php

 $url = 'https://api.sendgrid.com/';
 $user = 'USERNAME';
 $pass = 'PASSWORD';

 $fileName = 'myfile';
 $filePath = dirname(__FILE__);

 $params = array(
     'api_user' => $user,
     'api_key' => $pass,
     'to' =>'john@contoso.com',
     'subject' => 'test of file sends',
     'html' => '<p> the HTML </p>',
     'text' => 'the plain text',
     'from' => 'anna@contoso.com',
     'files['.$fileName.']' => '@'.$filePath.'/'.$fileName
 );

 print_r($params);

 $request = $url.'api/mail.send.json';

 // Generate curl request
 $session = curl_init($request);

 // Tell curl to use HTTP POST
 curl_setopt ($session, CURLOPT_POST, true);

 // Tell curl that this is the body of the POST
 curl_setopt ($session, CURLOPT_POSTFIELDS, $params);

 // Tell curl not to return headers, but do return the response
 curl_setopt($session, CURLOPT_HEADER, false);
 curl_setopt($session, CURLOPT_RETURNTRANSFER, true);

 // obtain response
 $response = curl_exec($session);
 curl_close($session);

 // print everything out
 print_r($response);
```

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>如何：使用筛选器启用页脚、跟踪和分析

SendGrid 可通过使用筛选器  提供其他电子邮件功能。 可将这些设置添加到电子邮件以启用特定功能（例如启用单击跟踪、Google 分析、订阅跟踪等）。

可使用 filters 属性将筛选器应用于邮件。 每个筛选器均由一个包含特定于筛选器的设置的哈希指定。 下面的示例将启用页脚筛选器并指定将追加到电子邮件底部的短信。 在此示例中，我们将使用 [sendgrid-php 库]。

使用[编辑器]安装库：

```bash
php composer.phar require sendgrid/sendgrid 2.1.1
```

### <a name="example"></a>示例  

```php
<?php
 /*
  * This example is used for sendgrid-php V2.1.1 (https://github.com/sendgrid/sendgrid-php/tree/v2.1.1)
  */
 include "vendor/autoload.php";

 $email = new SendGrid\Email();
 // The list of addresses this message will be sent to
 // [This list is used for sending multiple emails using just ONE request to SendGrid]
 $toList = array('john@contoso.com', 'anna@contoso.com');

 // Specify the names of the recipients
 $nameList = array('Name 1', 'Name 2');

 // Used as an example of variable substitution
 $timeList = array('4 PM', '5 PM');

 // Set all of the above variables
 $email->setTos($toList);
 $email->addSubstitution('-name-', $nameList);
 $email->addSubstitution('-time-', $timeList);

 // Specify that this is an initial contact message
 $email->addCategory("initial");

 // You can optionally setup individual filters here, in this example, we have
 // enabled the footer filter
 $email->addFilter('footer', 'enable', 1);
 $email->addFilter('footer', "text/plain", "Thank you for your business");
 $email->addFilter('footer', "text/html", "Thank you for your business");

 // The subject of your email
 $subject = 'Example SendGrid Email';

 // Where is this message coming from. For example, this message can be from
 // support@yourcompany.com, info@yourcompany.com
 $from = 'someone@example.com';

 // If you do not specify a sender list above, you can specify the user here. If
 // a sender list IS specified above, this email address becomes irrelevant.
 $to = 'john@contoso.com';

 # Create the body of the message (a plain-text and an HTML version).
 # text is your plain-text email
 # html is your html version of the email
 # if the receiver is able to view html emails then only the html
 # email will be displayed

 /*
  * Note the variable substitution here =)
  */
 $text = "
 Hello -name-,
 Thank you for your interest in our products. We have set up an appointment to call you at -time- EST to discuss your needs in more detail.
 Regards,
 Fred";

 $html = "
 <html>
 <head></head>
 <body>
 <p>Hello -name-,<br>
 Thank you for your interest in our products. We have set up an appointment
 to call you at -time- EST to discuss your needs in more detail.

 Regards,

 Fred<br>
 </p>
 </body>
 </html>";

 // set subject
 $email->setSubject($subject);

 // attach the body of the email
 $email->setFrom($from);
 $email->setHtml($html);
 $email->addTo($to);
 $email->setText($text);

 // Your SendGrid account credentials
 $username = 'sendgridusername@yourdomain.com';
 $password = 'example';

 // Create SendGrid object
 $sendgrid = new SendGrid($username, $password);

 // send message
 $response = $sendgrid->send($email);

 print_r($response);
 ```

## <a name="next-steps"></a>后续步骤

此时，已了解 SendGrid 电子邮件服务的基础知识，请访问以下链接以了解更多信息。

* SendGrid 文档：<https://sendgrid.com/docs>
* SendGrid PHP 库：<https://github.com/sendgrid/sendgrid-php>
* 面向 Azure 客户的 SendGrid 特惠产品/服务：<https://sendgrid.com/windowsazure.html>

有关详细信息，另请参阅 [PHP 开发人员中心](https://azure.microsoft.com/develop/php/)。

[https://sendgrid.com]: https://sendgrid.com
[https://sendgrid.com/transactional-email/pricing]: https://sendgrid.com/transactional-email/pricing
[special offer]: https://www.sendgrid.com/windowsazure.html
[Packaging and Deploying PHP Applications for Azure]: https://msdn.microsoft.com/library/windowsazure/hh674499(v=VS.103).aspx
[http://swiftmailer.org/download]: http://swiftmailer.org/download
[curl function]: https://php.net/curl
[基于云的电子邮件服务]: https://sendgrid.com/email-solutions
[事务电子邮件传递]: https://sendgrid.com/transactional-email
[sendgrid-php 库]: https://github.com/sendgrid/sendgrid-php/tree/v2.1.1
[编辑器]: https://getcomposer.org/download/
