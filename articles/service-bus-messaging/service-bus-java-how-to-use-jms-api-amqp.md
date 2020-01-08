---
title: 结合使用 AMQP 1.0 和 Java 消息服务 API 和 Azure 服务总线
description: 了解如何将 Java 消息服务 (JMS) 用于 Azure 服务总线和高级消息队列协议 (AMQP) 1.0。
services: service-bus-messaging
documentationcenter: java
author: axisc
manager: timlt
editor: spelluru
ms.assetid: be766f42-6fd1-410c-b275-8c400c811519
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 03/05/2019
ms.author: aschhab
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 9dff2cc11b71f314de81fd99ed3b72c6337d977f
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2019
ms.locfileid: "70967964"
---
# <a name="use-the-java-message-service-jms-with-azure-service-bus-and-amqp-10"></a>将 Java 消息服务（JMS）用于 Azure 服务总线和 AMQP 1。0
本文介绍了如何通过使用常用 Java 消息服务（JMS） API 标准的 Java 应用程序使用 Azure 服务总线消息传送功能（队列和发布/订阅主题）。 [本文](service-bus-amqp-dotnet.md)介绍如何使用 Azure 服务总线 .net API 来执行相同操作。 使用 AMQP 1.0，可以同时使用以下两个指南来了解跨平台消息。

高级消息队列协议 (AMQP) 1.0 是一个高效、可靠的线级消息传送协议，可用于构建可靠的跨平台消息传送应用程序。

对 Azure 服务总线中的 AMQP 1.0 的支持意味着可以使用有效的二进制协议，从一系列平台使用队列和发布/订阅中转消息传送功能。 此外，还可以生成由结合使用多个语言、框架和操作系统构建的组件组成的应用程序。

## <a name="get-started-with-service-bus"></a>服务总线入门
本指南假定你已具有包含名为 basicqueue 的队列的服务总线命名空间。 如果没有，则可以使用 [Azure 经典门户](https://portal.azure.com)[创建命名空间和队列](service-bus-create-namespace-portal.md)。 有关如何创建服务总线命名空间和队列的详细信息，请参阅[服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)。

> [!NOTE]
> 分区队列和主题也支持 AMQP。 有关详细信息，请参阅[分区消息实体](service-bus-partitioning.md)和[针对服务总线分区队列和主题的 AMQP 1.0 支持](service-bus-partitioned-queues-and-topics-amqp-overview.md)。
> 
> 

## <a name="downloading-the-amqp-10-jms-client-library"></a>下载 AMQP 1.0 JMS 客户端库
有关 Apache Qpid JMS AMQP 1.0 客户端库最新版本的下载地址的信息，请访问 [https://qpid.apache.org/download.html](https://qpid.apache.org/download.html)。

使用 Service Bus 构建和运行 JMS 应用程序时必须将以下 4 个 JAR 文件从 Apache Qpid JMS AMQP 1.0 分发存档添加到 Java CLASSPATH：

* geronimo-jms\_1.1\_spec-1.0.jar
* qpid-jms-client-[version].jar

> [!NOTE]
> JMS JAR 名称和版本可能已更改。 有关详细信息，请参阅 [Qpid JMS - AMQP 1.0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10)。

## <a name="coding-java-applications"></a>为 Java 应用程序编码
### <a name="java-naming-and-directory-interface-jndi"></a>Java 命名和目录接口 (JNDI)
JMS 使用 Java 命名和目录接口 (JNDI) 创建逻辑名称和物理名称之间的分隔。 将使用 JNDI 解析以下两种类型的 JMS 对象：ConnectionFactory 和 Destination。 JNDI 使用一个提供程序模型，可以在其中插入不同目录服务来处理名称解析任务。 Apache Qpid JMS AMQP 1.0 库附带一个使用以下格式的属性文件配置的、基于属性文件的简单 JNDI 提供程序。

```TEXT
# servicebus.properties - sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### <a name="setup-jndi-context-and-configure-the-connectionfactory"></a>设置 JNDI 上下文和配置 ConnectionFactory

在 [Azure 门户](https://portal.azure.com)“主连接字符串”下的 “共享访问策略”中提供了可引用的 ConnectionString
```java
// The connection string builder is the only part of the azure-servicebus SDK library
// we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
// connection string. 
ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
// set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");

// Look up queue
Destination queue = (Destination) context.lookup("QUEUE");
```

#### <a name="configure-producer-and-consumer-destination-queues"></a>配置制造者和使用者目标队列
用于在 Qpid 属性文件 JNDI 提供程序中定义目标的条目的格式如下：

创建制造者目标队列 - 
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Producer
MessageProducer producer = session.createProducer(queue);
```

创建使用者目标队列 - 
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Consumer
MessageConsumer consumer = session.createConsumer(queue);
```

### <a name="write-the-jms-application"></a>编写 JMS 应用程序
将 JMS 用于服务总线时不需要特殊的 API 或选项。 但是，有一些限制，我们会在后面说明。 与使用任何 JMS 应用程序一样，若要解析 **ConnectionFactory** 和目标，首先需要配置 JNDI 环境。

#### <a name="configure-the-jndi-initialcontext"></a>配置 JNDI InitialContext
JNDI 环境是通过将配置信息的哈希表传入到 javax.naming.InitialContext 类的构造函数中来配置的。 哈希表中的两个必需元素是初始上下文工厂的类名称和提供程序 URL。 以下代码演示了如何配置 JNDI 环境以将基于 Qpid 属性文件的 JNDI 提供程序用于名为 **servicebus.properties** 的属性文件。

```java
// set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + \
"?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>使用服务总线队列的简单 JMS 应用程序
以下示例程序将 JMS TextMessages 发送到 JNDI 逻辑名称为 QUEUE 的 Service Bus 队列，然后接收返回的消息。

可以从 [Azure 服务总线示例 JMS 队列快速启动](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client/JmsQueueQuickstart)中访问所有源代码和配置信息

```java
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE file in the project root for full license information.

package com.microsoft.azure.servicebus.samples.jmsqueuequickstart;

import com.microsoft.azure.servicebus.primitives.ConnectionStringBuilder;
import org.apache.commons.cli.*;
import org.apache.log4j.*;

import javax.jms.*;
import javax.naming.Context;
import javax.naming.InitialContext;
import java.util.Hashtable;
import java.util.concurrent.atomic.AtomicInteger;
import java.util.function.Function;

/**
 * This sample demonstrates how to send messages from a JMS Queue producer into
 * an Azure Service Bus Queue, and receive them with a JMS message consumer.
 * JMS Queue. 
 */
public class JmsQueueQuickstart {

    // Number of messages to send
    private static int totalSend = 10;
    //Tracking counter for how many messages have been received; used as termination condition
    private static AtomicInteger totalReceived = new AtomicInteger(0);
    // log4j logger 
    private static Logger logger = Logger.getRootLogger();

    public void run(String connectionString) throws Exception {

        // The connection string builder is the only part of the azure-servicebus SDK library
        // we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
        // connection string. 
        ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
        // set up JNDI context
        Hashtable<String, String> hashtable = new Hashtable<>();
        hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
        hashtable.put("queue.QUEUE", "BasicQueue");
        hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
        Context context = new InitialContext(hashtable);
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        
        // Look up queue
        Destination queue = (Destination) context.lookup("QUEUE");

        // we create a scope here so we can use the same set of local variables cleanly 
        // again to show the receive side separately with minimal clutter
        {
            // Create Connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            // Create Session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

            // Create producer
            MessageProducer producer = session.createProducer(queue);

            // Send messages
            for (int i = 0; i < totalSend; i++) {
                BytesMessage message = session.createBytesMessage();
                message.writeBytes(String.valueOf(i).getBytes());
                producer.send(message);
                System.out.printf("Sent message %d.\n", i + 1);
            }

            producer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        {
            // Create Connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            connection.start();
            // Create Session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            // Create consumer
            MessageConsumer consumer = session.createConsumer(queue);
            // create a listener callback to receive the messages
            consumer.setMessageListener(message -> {
                try {
                    // receives message is passed to callback
                    System.out.printf("Received message %d with sq#: %s\n",
                            totalReceived.incrementAndGet(), // increments the tracking counter
                            message.getJMSMessageID());
                    message.acknowledge();
                } catch (Exception e) {
                    logger.error(e);
                }
            });

            // wait on the main thread until all sent messages have been received
            while (totalReceived.get() < totalSend) {
                Thread.sleep(1000);
            }
            consumer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        System.out.printf("Received all messages, exiting the sample.\n");
        System.out.printf("Closing queue client.\n");
    }

    public static void main(String[] args) {

        System.exit(runApp(args, (connectionString) -> {
            JmsQueueQuickstart app = new JmsQueueQuickstart();
            try {
                app.run(connectionString);
                return 0;
            } catch (Exception e) {
                System.out.printf("%s", e.toString());
                return 1;
            }
        }));
    }

    static final String SB_SAMPLES_CONNECTIONSTRING = "SB_SAMPLES_CONNECTIONSTRING";

    public static int runApp(String[] args, Function<String, Integer> run) {
        try {

            String connectionString = null;

            // parse connection string from command line
            Options options = new Options();
            options.addOption(new Option("c", true, "Connection string"));
            CommandLineParser clp = new DefaultParser();
            CommandLine cl = clp.parse(options, args);
            if (cl.getOptionValue("c") != null) {
                connectionString = cl.getOptionValue("c");
            }

            // get overrides from the environment
            String env = System.getenv(SB_SAMPLES_CONNECTIONSTRING);
            if (env != null) {
                connectionString = env;
            }

            if (connectionString == null) {
                HelpFormatter formatter = new HelpFormatter();
                formatter.printHelp("run jar with", "", options, "", true);
                return 2;
            }
            return run.apply(connectionString);
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
            return 3;
        }
    }
}
```

### <a name="run-the-application"></a>运行应用程序
传递共享访问策略中的“连接字符串”，以运行应用程序。
以下是通过运行应用程序的表单输出：

```Output
> mvn clean package
>java -jar ./target/jmsqueuequickstart-1.0.0-jar-with-dependencies.jar -c "<CONNECTION_STRING>"

Sent message 1.
Sent message 2.
Sent message 3.
Sent message 4.
Sent message 5.
Sent message 6.
Sent message 7.
Sent message 8.
Sent message 9.
Sent message 10.
Received message 1 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-1
Received message 2 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-2
Received message 3 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-3
Received message 4 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-4
Received message 5 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-5
Received message 6 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-6
Received message 7 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-7
Received message 8 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-8
Received message 9 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-9
Received message 10 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-10
Received all messages, exiting the sample.
Closing queue client.

```

## <a name="amqp-disposition-and-service-bus-operation-mapping"></a>AMQP 处置和服务总线操作映射
以下是将 AMQP 处置转换为服务总线操作的方法：

```Output
ACCEPTED = 1; -> Complete()
REJECTED = 2; -> DeadLetter()
RELEASED = 3; (just unlock the message in service bus, will then get redelivered)
MODIFIED_FAILED = 4; -> Abandon() which increases delivery count
MODIFIED_FAILED_UNDELIVERABLE = 5; -> Defer()
```

## <a name="jms-topics-vs-service-bus-topics"></a>JMS 主题与服务总线主题
通过 Java 消息服务 (JMS) API 使用 Azure 服务总线主题和订阅可以提供基本的发送和接收功能。 从其他使用 JMS 兼容 API 的消息代理处移植应用程序时，这是一种很方便的选择，即使服务总线主题不同于 JMS 主题且需要一些调整。 

Azure 服务总线主题将消息路由到已命名的、共享的、持久的订阅中，这些订阅通过 Azure 资源管理接口、Azure 命令行工具或 Azure 门户进行管理。 每个订阅允许使用最多 2000 条选择规则，每条规则可能有一个筛选器条件以及一项适用于 SQL 筛选器的元数据转换操作。 每次出现筛选器条件匹配的情况时，系统就会选择将要复制到订阅中的输入消息。  

从订阅接收消息与从队列接收消息是相同的。 每个订阅都有一个关联的死信队列，并且可以将消息自动转发给其他队列或主题。 

JMS 主题允许客户端动态创建非持久的和持久的订阅者，这样就可以选择性地允许通过消息选择器来筛选消息。 服务总线不支持这些非共享的实体。 但是，服务总线的 SQL 筛选器规则语法非常类似于 JMS 支持的消息选择器语法。 

如此示例所示，JMS 主题发布者端兼容服务总线，但动态订阅者则不兼容。 不支持将下述与拓扑相关的 JMS API 与服务总线配合使用。 

## <a name="unsupported-features-and-restrictions"></a>不受支持的功能和限制
在将 JMS over AMQP 1.0 用于 Service Bus 时存在以下限制，即：

* 每个**会话**只允许一个 **MessageProducer** 或 **MessageConsumer**。 如果需要在应用程序中创建多个 **MessageProducers** 或 **MessageConsumers**，请分别对其创建专用**会话**。
* 当前不支持易失性主题订阅。
* **MessageSelectors** 。
* 不支持事务处理会话和分布式事务。

此外，Azure 服务总线将控制平面从数据平面拆分了出来，因此，不支持多个 JMS 的动态拓扑函数：

| 不支持的方法          | 替换为                                                                             |
|-----------------------------|------------------------------------------------------------------------------------------|
| createDurableSubscriber     | 创建移植消息选择器的主题订阅                                 |
| createDurableConsumer       | 创建移植消息选择器的主题订阅                                 |
| createSharedConsumer        | 服务总线主题始终可共享，请参阅上述内容                                       |
| createSharedDurableConsumer | 服务总线主题始终可共享，请参阅上述内容                                       |
| createTemporaryTopic        | 通过管理 API/工具/门户创建主题（AutoDeleteOnIdle 被设置为过期期间） |
| createTopic                 | 通过管理 API/工具/门户创建主题                                           |
| unsubscribe                 | 删除主题管理 API/工具/门户                                             |
| createBrowser               | 不受支持。 使用服务总线 API 的 Peek() 功能                         |
| createQueue                 | 通过管理 API/工具/门户创建队列                                           | 
| createTemporaryQueue        | 通过管理 API/工具/门户创建队列（AutoDeleteOnIdle 被设置为过期期间） |
| receiveNoWait               | 使用服务总线 SDK 提供的 receive （）方法，并指定非常低或零的超时 |

## <a name="summary"></a>总结
本操作方法指南演示了如何通过使用常用 JMS API 和 AMQP 1.0 通过 Java 使用 Service Bus 中转消息传送功能（队列和发布/订阅主题）。

也可以通过其他语言（包括 .NET、C、Python 和 PHP）使用 Service Bus AMQP 1.0。 使用这些不同语言构建的组件可以使用服务总线中的 AMQP 1.0 支持可靠且完全无损地交换消息。

## <a name="next-steps"></a>后续步骤
* [Azure 服务总线中的 AMQP 1.0 支持](service-bus-amqp-overview.md)
* [如何将 AMQP 1.0 与服务总线 .NET API 配合使用](service-bus-dotnet-advanced-message-queuing.md)
* [服务总线 AMQP 1.0 开发人员指南](service-bus-amqp-dotnet.md)
* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)
* [Java 开发中心](https://azure.microsoft.com/develop/java/)

