---
title: 认知服务容器常见问题（FAQ）
titleSuffix: Azure Cognitive Services
description: 常见问题和解答。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: dapine
ms.openlocfilehash: 6e218f33bdc33708cef0c94eb85298abf2b8927c
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316622"
---
# <a name="azure-cognitive-services-containers-frequently-asked-questions-faq"></a>Azure 认知服务容器常见问题（FAQ）

## <a name="general-questions"></a>一般问题

**问：提供了哪些功能？**

**答：** [Azure 认知服务中的容器支持](../cognitive-services-container-support.md)允许开发人员使用 azure 中提供的相同智能 api，但具有容器化的[优势](../cognitive-services-container-support.md#features-and-benefits)。 目前已在 Azure 认知服务的子集中发布容器支持预览版，这些服务包括：

> [!div class="checklist"]
> * [异常探测器][ad-containers]
> * [计算机视觉][cv-containers]
> * [人脸][fa-containers]
> * [表单识别器][fr-containers]
> * [语言理解 (LUIS)][lu-containers]
> * [语音服务 API][sp-containers]
> * [文本分析][ta-containers]
<!-- > * [Translator Text][tt-containers] -->

**问：认知服务云和容器之间是否有任何区别？**

**答：** 认知服务容器是认知服务云的替代方案。 容器提供的功能与对应的云服务相同。 客户可在本地或 Azure 中部署容器。 核心 AI 技术、定价层、API 密钥和 API 签名在容器和对应的云服务之间是相同的。 下面是在云服务的等效项上选择容器的[功能和优势](../cognitive-services-container-support.md#features-and-benefits)。

**问：容器是否适用于所有认知服务，以及我们应期待的下一组容器？**

**答：** 我们想要将更多认知服务作为容器产品提供。 联系本地 Microsoft 帐户管理器，获取有关新容器版本和其他认知服务公告的更新。

**问：服务级别协议（SLA）将如何处理认知服务容器？**

**答：** 认知服务容器没有 SLA。

认知服务资源的容器配置由客户控制，因此 Microsoft 不会提供正式发布（GA）的 SLA。 客户可以自由地在本地部署容器，从而定义主机环境。

> [!IMPORTANT]
> 若要了解有关认知服务服务级别协议的详细信息，请[访问我们的 SLA 页](https://azure.microsoft.com/support/legal/sla/cognitive-services/v1_1/)。

**问：这些容器在主权云中是否可用？**

**答：** 并非每个人都熟悉术语 "主权 cloud"，因此让我们从定义开始：

> "主权 cloud" 由[Azure 政府](../../azure-government/documentation-government-welcome.md)版、 [Azure 德国](../../germany/germany-welcome.md)和[azure 中国世纪互联](https://docs.microsoft.com/azure/china/overview-operations)云组成。

遗憾的是，主权云中并*不*本机支持认知服务容器。 容器可以在这些云中运行，但会从公有云中提取，并需要将使用情况数据发送到公共终结点。

### <a name="versioning"></a>版本控制

**问：如何将容器更新到最新版本？**

**答：** 客户可以选择何时更新已部署的容器。 容器将标记为标准[Docker 标记](https://docs.docker.com/engine/reference/commandline/tag/)，如`latest` ，以指示最新版本。 我们鼓励客户在发布时请求最新版本的容器，签入[Azure 容器注册表 webhook](../../container-registry/container-registry-webhook.md) ，详细了解如何在更新映像时获得通知。
 
**问：将支持哪些版本？**

**答：** 将支持容器的当前主版本和最新主版本。 但是，我们鼓励客户及时了解最新的技术。
 
**问：如何对更新进行版本控制？**

**答：** 主版本更改指示 API 签名存在重大更改。 我们预计，这通常会与相应认知服务云产品/服务的主要版本更改一致。 次版本更改指示 bug 修复、模型更新或不会对 API 签名进行重大更改的新功能。

## <a name="technical-questions"></a>技术问题

**问：如何在 IoT 设备上运行认知服务容器？**

你是没有可靠的 internet 连接，还是想要节省带宽成本。 或者，如果具有低延迟要求，或者处理需要在现场进行分析的敏感数据，则[与认知服务容器 Azure IoT Edge](https://azure.microsoft.com/blog/running-cognitive-services-on-iot-edge/)提供与云的一致性。

**问：如何实现提供产品反馈和功能建议？**

**答：** 我们鼓励客户公开[他们的关注](https://cognitive.uservoice.com/)点，并向上投票在潜在问题重叠的其他人。 用户语音工具可用于产品反馈和功能建议。

**问：我应该与谁联系以获得支持？**

**答：** 客户支持渠道与认知服务云产品/服务相同。 所有认知服务容器都包含有助于我们和社区支持客户的日志记录功能。 有关其他支持，请参阅以下选项。

### <a name="customer-support-plan"></a>客户支持计划

客户应参考其[Azure 支持计划](https://azure.microsoft.com/support/plans/)，以查看谁联系以获得支持。

### <a name="azure-knowledge-center"></a>Azure 知识中心

客户可免费探索[Azure 知识中心](https://azure.microsoft.com/resources/knowledge-center/)来回答问题和支持问题。

### <a name="stack-overflow"></a>堆栈溢出

> 对于专业和发烧程序员， [Stack Overflow](https://en.wikipedia.org/wiki/Stack_Overflow)是问题和答案站点。

浏览以下标记，查找满足你的需求的潜在问题和答案。

* [Azure 认知服务](https://stackoverflow.com/questions/tagged/azure-cognitive-services)
* [Microsoft 认知](https://stackoverflow.com/questions/tagged/microsoft-cognitive)

**问：如何计费？**

**答：** 客户按消费计费，与认知服务云类似。 需要将容器配置为将计量数据发送到 Azure，并相应地对事务计费。 跨托管服务和本地服务使用的资源将添加到具有分层定价的单一配额，并针对这两种使用情况进行计数。 有关更多详细信息，请参阅相应产品的定价页。

* [异常探测器][ad-containers-billing]
* [计算机视觉][cv-containers-billing]
* [人脸][fa-containers-billing]
* [表单识别器][fr-containers-billing]
* [语言理解 (LUIS)][lu-containers-billing]
* [语音服务 API][sp-containers-billing]
* [文本分析][ta-containers-billing]
<!-- * [Translator Text][tt-containers-billing] -->

> [!IMPORTANT]
> 如果未连接到 Azure 进行计量，则无法授权并运行认知服务容器。 客户需要始终让容器向计量服务传送账单信息。 认知服务容器不会将客户数据发送给 Microsoft。
 
**问：当前的容器支持保证是什么？**

**答：** 预览没有任何保证。 如果容器正式发布为正式发布（GA），则适用于企业软件的 Microsoft 标准保修。
 
**问：失去 internet 连接时，认知服务容器会发生什么情况？**

**答：** 如果未连接到 Azure 进行计量，则*未授权*认知服务容器运行。 客户需要使容器能够随时与计量服务通信。

**问：容器无需连接到 Azure 即可运行多长时间？**

**答：** 如果未连接到 Azure 进行计量，则*未授权*认知服务容器运行。 客户需要使容器能够随时与计量服务通信。
 
**问：运行这些容器需要什么当前硬件？**

**答：** 认知服务容器是基于 x64 的容器，可运行任何兼容的 Linux 节点、VM 和支持 x64 Linux Docker 容器的边缘设备。 它们都需要 CPU 处理器。 以下提供了每个容器产品/服务的最低和推荐的配置：

* [异常探测器][ad-containers-recommendations]
* [计算机视觉][cv-containers-recommendations]
* [人脸][fa-containers-recommendations]
* [表单识别器][fr-containers-recommendations]
* [语言理解 (LUIS)][lu-containers-recommendations]
* [语音服务 API][sp-containers-recommendations]
* [文本分析][ta-containers-recommendations]
<!-- * [Translator Text][tt-containers-recommendations] -->
 
**问：Windows 上当前是否支持这些容器？**

**答：** 认知服务容器是 Linux 容器，但在 Windows 上对 Linux 容器有一些支持。 有关 Windows 上的 Linux 容器的详细信息，请参阅[Docker 文档](https://blog.docker.com/2017/09/preview-linux-containers-on-windows/)。
 
**问：如何实现发现容器？**

**答：** 认知服务容器在各种位置可用，例如 Azure 门户、Docker 中心和 Azure 容器注册表。 有关最新的容器位置，请参阅[容器存储库和映像](../cognitive-services-container-support.md#container-repositories-and-images)。

**问：认知服务容器与 AWS 和 Google 产品/服务有何不同？**

**答：** Microsoft 是第一个云提供商，它在容器中移动预先训练的 AI 模型，每个事务都有简单的计费，就好像客户使用的是云服务。 Microsoft 相信混合云为客户提供更多的选择。

**问：哪些符合性认证容器有哪些？**

**答：** 认知服务容器没有任何符合性认证

**问：哪些区域包含认知服务容器？**

**答：** 容器可以在任何区域中的任何位置运行，但它们需要一个密钥并回叫 Azure 进行计量。 容器计量调用支持云服务的所有受支持区域。

[!INCLUDE [Containers next steps](includes/containers-next-steps.md)]

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-containers]: ../speech-service/speech-container-howto.md
[ta-containers]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md
<!-- [tt-containers]: ../translator/how-to-install-containers.md -->

[ad-containers-billing]: ../anomaly-Detector/anomaly-detector-container-howto.md#billing
[cv-containers-billing]: ../computer-vision/computer-vision-how-to-install-containers.md#billing
[fa-containers-billing]: ../face/face-how-to-install-containers.md#billing
[fr-containers-billing]: ../form-recognizer/form-recognizer-container-howto.md#billing
[lu-containers-billing]: ../luis/luis-container-howto.md#billing
[sp-containers-billing]: ../speech-service/speech-container-howto.md#billing
[ta-containers-billing]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md#billing
<!-- [tt-containers-billing]: ../translator/how-to-install-containers.md#billing -->

[ad-containers-recommendations]: ../anomaly-Detector/anomaly-detector-container-howto.md#container-requirements-and-recommendations
[cv-containers-recommendations]: ../computer-vision/computer-vision-how-to-install-containers.md#container-requirements-and-recommendations
[fa-containers-recommendations]: ../face/face-how-to-install-containers.md#container-requirements-and-recommendations
[fr-containers-recommendations]: ../form-recognizer/form-recognizer-container-howto.md#container-requirements-and-recommendations
[lu-containers-recommendations]: ../luis/luis-container-howto.md#container-requirements-and-recommendations
[sp-containers-recommendations]: ../speech-service/speech-container-howto.md#container-requirements-and-recommendations
[ta-containers-recommendations]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md#container-requirements-and-recommendations
<!-- [tt-containers-recommendations]: ../translator/how-to-install-containers.md#container-requirements-and-recommendations -->
