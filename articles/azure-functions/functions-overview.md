---
title: Azure Functions 概述 | Microsoft Docs
description: 了解如何使用 Azure Functions 以分钟为单位优化异步工作负荷。
documentationcenter: na
author: mattchenderson
manager: jeconnoc
keywords: Azure Functions, Functions, 事件处理, webhook, 动态计算, 无服务体系结构
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.service: azure-functions
ms.topic: overview
ms.date: 10/03/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: f3fc7691fc3afa3a1fe886655353d9ed41f631cc
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70096083"
---
# <a name="an-introduction-to-azure-functions"></a>Azure Functions 简介  
Azure Functions 是用于在云中轻松运行小段代码或“函数”的一个解决方案。 用户可以只编写解决现有问题所需的代码，而无需担心要运行该代码的整个应用程序或基础结构。 Functions 可使开发更有效率，并可以使用自己所选的开发语言，例如 C#、Java、JavaScript、PowerShell 和 Python。 只需为代码运行的时间付费，并可信任 Azure 会根据需要进行调整。 使用 Azure Functions，可在 Microsoft Azure 上开发[无服务器](https://azure.microsoft.com/solutions/serverless/)应用程序。

本主题提供有关 Azure Functions 的高级概述。 如果要立即投入和开始使用 Functions，请从[创建第一个 Azure 函数](functions-create-first-azure-function.md)开始。 如果要查找有关 Functions 的更多技术信息，请参阅 [开发人员参考](functions-reference.md)。

## <a name="features"></a>功能
下面是 Functions 的一些主要功能：

* **语言选择** - 使用所选的 C#、Java、Javascript、Python 和其他语言编写函数。 有关完整列表，请参阅[支持的语言](supported-languages.md)。
* **按使用付费定价模型** - 仅为运行代码所用的时间付费。 请参阅[定价部分](#pricing)中的使用托管计划选项。  
* **引入自己的依赖项** - Functions 支持 NuGet 和 NPM，因此用户可以使用自己的常用库。  
* **集成安全性** - 使用 OAuth 提供程序（如 Azure Active Directory、Facebook、Google、Twitter 和 Microsoft 帐户）保护 HTTP 触发的函数。  
* **简化集成** - 轻松利用 Azure 服务和软件即服务 (SaaS) 产品/服务。 有关一些示例，请参阅[集成部分](#integrations)。  
* **灵活开发** - 直接在门户中编写函数代码，或者通过 [GitHub](../app-service/scripts/cli-continuous-deployment-github.md)、[Azure DevOps Services](../app-service/scripts/cli-continuous-deployment-vsts.md) 和其他[受支持的开发工具](../app-service/deploy-local-git.md)设置持续集成和部署代码。  
* **开放源代码** - Functions 运行时是一个开放源代码， [可在 GitHub 上找到](https://github.com/azure/azure-webjobs-sdk-script)。  

## <a name="what-can-i-do-with-functions"></a>使用 Functions 可以做什么？
Functions 是一个理想的解决方案，用于处理数据、集成系统、使用物联网 (IoT) 以及生成简单的 API 和微服务。 对于以下任务请考虑使用 Functions：例如，图像或订单处理、文件维护，或者要按计划运行的任何任务。 

Functions 提供模板，以帮助用户从主要方案开始，包括以下模板：

* **HTTPTrigger** - 使用 HTTP 请求触发执行代码。 有关示例，请参阅[创建第一个函数](functions-create-first-azure-function.md)。
* **TimerTrigger** - 按预定义的计划执行清除或其他批处理任务。 有关示例，请参阅[创建由计时器触发的函数](functions-create-scheduled-function.md)。
* **CosmosDBTrigger** - 在 NoSQL 数据库中以集合形式添加或更新 Azure Cosmos DB 文档时，对这些文档进行处理。 有关详细信息，请参阅 [Azure Cosmos DB 绑定](functions-bindings-cosmosdb-v2.md)。
* **BlobTrigger** - Azure 存储 blob 添加到容器时，处理这些 blob。 可以使用此函数调整图像大小。 有关详细信息，请参阅 [Blob 存储帐户绑定](functions-bindings-storage-blob.md)。
* **QueueTrigger** - 当消息到达 Azure 存储队列时，响应这些消息。 有关详细信息，请参阅 [Azure 队列存储绑定](functions-bindings-storage-queue.md)。
* **EventGridTrigger** - 响应传递到 Azure 事件网格中的订阅的事件。 支持使用基于订阅的模型（包括筛选）来接收事件。 它是用于构建基于事件的体系结构的良好解决方案。 有关示例，请参阅[使用事件网格自动调整上传图像的大小](../event-grid/resize-images-on-storage-blob-upload-event.md)。
* **EventHubTrigger** - 响应传送到 Azure 事件中心的事件。 在应用程序检测、用户体验或工作流处理以及物联网 (IoT) 方案中特别有用。 有关详细信息，请参阅[事件中心绑定](functions-bindings-event-hubs.md)。
* **ServiceBusQueueTrigger** - 通过侦听消息队列将代码连接到其他 Azure 服务或本地服务。 有关详细信息，请参阅[服务总线绑定](functions-bindings-service-bus.md)。
* **ServiceBusTopicTrigger** - 通过订阅主题将代码连接到其他 Azure 服务或本地服务。 有关详细信息，请参阅[服务总线绑定](functions-bindings-service-bus.md)。

Azure Functions 支持 *触发器*（用于启动代码执行）和*绑定*（用于简化针对输入和输出数据进行的编码）。 有关 Azure Functions 提供的触发器和绑定的详细说明，请参阅 [Azure Functions 触发器和绑定开发人员参考](functions-triggers-bindings.md)。

## <a name="integrations"></a>集成
Azure Functions 可与各种 Azure 和第三方服务集成。 这些服务可以触发函数开始执行，或者可用作代码的输入和输出。 Azure Functions 支持以下服务集成：

* Azure Cosmos DB
* Azure 事件中心
* Azure 事件网格
* Azure 通知中心
* Azure 服务总线（队列和主题）
* Azure 存储（blob、队列和表）
* 本地（使用服务总线）
* Twilio（短信）

## <a name="pricing"></a>Functions 的费用是多少？
Azure Functions 有两种定价计划。 请选择最适合自己的那种： 

* **使用计划** - 用户的函数运行时，Azure 提供所有所需的计算资源。 用户不必担心资源管理，只需为自己的代码运行的时间付费。 
* **应用服务计划** - 将函数像 Web 应用一样运行。 如果已对其他应用程序使用应用服务，可以按相同的计划运行自己的函数，而不用另外付费。 

有关托管计划的详细信息，请参阅 [Azure Functions 托管计划比较](functions-scale.md)。 完整的定价详细信息可在 [Functions 定价页](https://azure.microsoft.com/pricing/details/functions/)中找到。

## <a name="next-steps"></a>后续步骤
* [创建第一个 Azure 函数](functions-create-first-azure-function.md)  
  使用 Azure Functions 快速入门立即投入并创建第一个函数。 
* [Azure Functions 开发人员参考](functions-reference.md)  
  提供有关 Azure Functions 运行时的更多技术信息，并为编码函数及定义触发器和绑定提供参考。
* [测试 Azure Functions](functions-test-a-function.md)  
  介绍可用于测试函数的各种工具和技巧。
* [如何缩放 Azure Functions](functions-scale.md)  
  讨论 Azure Functions 提供的服务计划（包括使用托管计划）以及如何选择合适的计划。 
* [详细了解 Azure 应用服务](../app-service/overview.md)  
  Azure Functions 利用 Azure 应用服务执行核心功能，例如部署、环境变量和诊断。 

