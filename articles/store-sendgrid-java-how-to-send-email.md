---
title: 如何使用 SendGrid 电子邮件服务 (Java) | Microsoft Docs
description: 了解如何在 Azure 上使用 SendGrid 电子邮件服务发送电子邮件。 用 Java 编写的代码示例。
services: ''
documentationcenter: java
author: thinkingserious
manager: sendgrid
editor: mollybos
ms.assetid: cc75c43e-ede9-492b-98c2-9147fcb92c21
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/30/2014
ms.author: erikre
ms.reviewer: elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork
ms.openlocfilehash: 8ae948e9c79cff4cd0c896b250743fd9dc521752
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876508"
---
# <a name="how-to-send-email-using-sendgrid-from-java"></a>如何通过 Java 使用 SendGrid 发送电子邮件
本指南演示了如何在 Azure 上使用 SendGrid 电子邮件服务执行常见编程任务。 示例使用 Java 编写。 涉及的任务包括**创建电子邮件**、**发送电子邮件**、**添加附件**、**使用筛选器**和**更新属性**。 有关 SendGrid 和发送电子邮件的详细信息，请参阅[后续步骤](#next-steps)部分。

## <a name="what-is-the-sendgrid-email-service"></a>什么是 SendGrid 电子邮件服务？
SendGrid 是一项[基于云的电子邮件服务]，该服务提供了可靠的[事务电子邮件传递]、伸缩性、实时分析以及可用于简化自定义集成的灵活的 API。 常见 SendGrid 使用方案包括：

* 自动向客户发送收据
* 管理用于每月向客户发送电子传单和特惠产品/服务的通讯组列表
* 收集诸如已阻止的电子邮件和客户响应性等项目的实时度量值
* 生成用于帮助确定趋势的报告
* 转发客户查询
* 以电子邮件的形式从应用程序发送通知

有关详细信息，请参阅 <https://sendgrid.com>。

## <a name="create-a-sendgrid-account"></a>创建 SendGrid 帐户
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="how-to-use-the-javaxmail-libraries"></a>如何：使用 javax.mail.session 库
获取 javax.mail 库（例如从 <https://www.oracle.com/technetwork/java/javamail> 获取），并将它们导入到代码中。 简而言之，使用 javax.mail 库通过 SMTP 发送电子邮件的过程包括以下操作：

1. 指定 SMTP 值（包括 SMTP 服务器），对于 SendGrid，此项为 smtp.sendgrid.net。

```
        import java.util.Properties;
        import javax.mail.*;
        import javax.mail.internet.*;

        public class MyEmailer {
           private static final String SMTP_HOST_NAME = "smtp.sendgrid.net";
           private static final String SMTP_AUTH_USER = "your_sendgrid_username";
           private static final String SMTP_AUTH_PWD = "your_sendgrid_password";

           public static void main(String[] args) throws Exception{
               new MyEmailer().SendMail();
           }

           public void SendMail() throws Exception
           {
              Properties properties = new Properties();
                 properties.put("mail.transport.protocol", "smtp");
                 properties.put("mail.smtp.host", SMTP_HOST_NAME);
                 properties.put("mail.smtp.port", 587);
                 properties.put("mail.smtp.auth", "true");
                 // …
```

1. 展开 javax.mail.Authenticator  类，然后在对 getPasswordAuthentication  方法的实现中，返回 SendGrid 用户名和密码。  

       private class SMTPAuthenticator extends javax.mail.Authenticator {
       public PasswordAuthentication getPasswordAuthentication() {
          String username = SMTP_AUTH_USER;
          String password = SMTP_AUTH_PWD;
          return new PasswordAuthentication(username, password);
       }
2. 通过 javax.mail.Session  对象创建经过身份验证的电子邮件会话。  

       Authenticator auth = new SMTPAuthenticator();
       Session mailSession = Session.getDefaultInstance(properties, auth);
3. 创建邮件并分配**收件人**、**发件人**、**主题**和内容值。 这一点在[如何：创建电子邮件](#how-to-create-an-email)部分。
4. 通过 javax.mail.Transport  对象发送邮件。 这将显示在 [如何:发送电子邮件] [#how 到发送电子邮件] 部分。

## <a name="how-to-create-an-email"></a>如何：创建电子邮件
以下代码演示如何为电子邮件指定值。

    MimeMessage message = new MimeMessage(mailSession);
    Multipart multipart = new MimeMultipart("alternative");
    BodyPart part1 = new MimeBodyPart();
    part1.setText("Hello, Your Contoso order has shipped. Thank you, John");
    BodyPart part2 = new MimeBodyPart();
    part2.setContent(
        "<p>Hello,</p>
        <p>Your Contoso order has <b>shipped</b>.</p>
        <p>Thank you,<br>John</br></p>",
        "text/html");
    multipart.addBodyPart(part1);
    multipart.addBodyPart(part2);
    message.setFrom(new InternetAddress("john@contoso.com"));
    message.addRecipient(Message.RecipientType.TO,
       new InternetAddress("someone@example.com"));
    message.setSubject("Your recent order");
    message.setContent(multipart);

## <a name="how-to-send-an-email"></a>如何：发送电子邮件
以下代码演示如何发送电子邮件。

    Transport transport = mailSession.getTransport();
    // Connect the transport object.
    transport.connect();
    // Send the message.
    transport.sendMessage(message, message.getAllRecipients());
    // Close the connection.
    transport.close();

## <a name="how-to-add-an-attachment"></a>如何：添加附件
以下代码演示如何添加附件。

    // Local file name and path.
    String attachmentName = "myfile.zip";
    String attachmentPath = "c:\\myfiles\\";
    MimeBodyPart attachmentPart = new MimeBodyPart();
    // Specify the local file to attach.
    DataSource source = new FileDataSource(attachmentPath + attachmentName);
    attachmentPart.setDataHandler(new DataHandler(source));
    // This example uses the local file name as the attachment name.
    // They could be different if you prefer.
    attachmentPart.setFileName(attachmentName);
    multipart.addBodyPart(attachmentPart);

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>如何：使用筛选器启用页脚、跟踪和分析
SendGrid 可通过使用筛选器  提供其他电子邮件功能。 可将这些设置添加到电子邮件以启用特定功能（例如启用单击跟踪、Google 分析、订阅跟踪等）。 有关筛选器的完整列表, 请参阅[筛选器设置][Filter Settings]。

* 以下代码演示如何插入使所发送的电子邮件底部显示 HTML 文本的页脚筛选器。

      message.addHeader("X-SMTPAPI",
          "{\"filters\":
          {\"footer\":
          {\"settings\":
          {\"enable\":1,\"text/html\":
          \"<html><b>Thank you</b> for your business.</html>\"}}}}");
* 筛选器的另一个示例是点击跟踪。 比如说，电子邮件文本中含有超链接（如以下链接），而你想要跟踪点击率：

      messagePart.setContent(
          "Hello,
          <p>This is the body of the message. Visit
          <a href='http://www.contoso.com'>http://www.contoso.com</a>.</p>
          Thank you.",
          "text/html");
* 若要实现点击跟踪，请使用以下代码：

      message.addHeader("X-SMTPAPI",
          "{\"filters\":
          {\"clicktrack\":
          {\"settings\":
          {\"enable\":1}}}}");

## <a name="how-to-update-email-properties"></a>如何：更新电子邮件属性
可使用“设置属性”覆盖某些电子邮件属性，或使用“添加属性”追加某些电子邮件属性   。

例如，若要指定 **ReplyTo** 地址，请使用以下代码：

    InternetAddress addresses[] =
        { new InternetAddress("john@contoso.com"),
          new InternetAddress("wendy@contoso.com") };

    message.setReplyTo(addresses);

若要添加“抄送”  收件人，请使用以下代码：

    message.addRecipient(Message.RecipientType.CC, new
    InternetAddress("john@contoso.com"));

## <a name="how-to-use-additional-sendgrid-services"></a>如何：使用其他 SendGrid 服务
SendGrid 提供了基于 Web 的 API，可通过这些 API 从 Azure 应用程序中使用其他 SendGrid 功能。 有关完整详细信息，请参阅 [SendGrid API 文档][SendGrid API documentation]。

## <a name="next-steps"></a>后续步骤
此时，已了解 SendGrid 电子邮件服务的基础知识，请访问以下链接以了解更多信息。

* 演示在 Azure 部署中使用 SendGrid 的示例:[如何在 Azure 部署中通过 Java 使用 SendGrid 发送电子邮件](store-sendgrid-java-how-to-send-email-example.md)
* SendGrid Java SDK：<https://sendgrid.com/docs/Code_Examples/java.html>
* SendGrid API 文档：<https://sendgrid.com/docs/API_Reference/index.html>
* 面向 Azure 客户的 SendGrid 特惠产品/服务：<https://sendgrid.com/windowsazure.html>

[https://sendgrid.com]: https://sendgrid.com
[https://sendgrid.com/pricing.html]: https://sendgrid.com/pricing.html
[http://www.sendgrid.com/azure.html]: https://www.sendgrid.com/windowsazure.html
[https://sendgrid.com/features]: https://sendgrid.com/features
[https://www.oracle.com/technetwork/java/javamail]: https://www.oracle.com/technetwork/java/javamail/index.html
[Filter Settings]: https://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html
[SendGrid API documentation]: https://sendgrid.com/docs/API_Reference/index.html
[https://sendgrid.com/azure.html]: https://sendgrid.com/windowsazure.html
[基于云的电子邮件服务]: https://sendgrid.com/email-solutions
[事务电子邮件传递]: https://sendgrid.com/transactional-email
