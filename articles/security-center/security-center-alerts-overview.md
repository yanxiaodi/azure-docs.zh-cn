---
title: Azure 安全中心中的安全警报 |Microsoft Docs
description: 本主题说明 Azure 安全中心提供的安全警报和不同类型。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 1b71e8ad-3bd8-4475-b735-79ca9963b823
ms.service: security-center
ms.topic: conceptual
ms.date: 08/25/2019
ms.author: memildin
ms.openlocfilehash: 3b4b02574c028822d25d841376b127a718243b2e
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202561"
---
# <a name="security-alerts-in-azure-security-center"></a>Azure 安全中心的安全警报

在 Azure 安全中心，许多不同的资源类型都有多种不同的警报。 安全中心为部署在 Azure 上的资源以及部署在本地和混合云环境中的资源生成警报。

Azure 安全中心的标准层提供高级检测功能。 有免费试用版可用。 可以在 [安全策略](security-center-pricing.md)中从选择定价层开始升级。 访问 [“安全中心”页](https://azure.microsoft.com/pricing/details/security-center/) ，了解详细的定价情况。

## 应对今天的威胁<a name="respond-threats"></a>

过去 20 年里，威胁态势有了很大的改变。 在过去，公司通常只需担心网站被各个攻击者改头换面。许多情况下，这些攻击者感兴趣的是看看“自己能够做什么”。 而现在，攻击者则更为复杂，更有组织性。 他们通常有具体的经济和战略目标。 他们的可用资源也更多，因为他们可能是由国家/地区提供资金支持的，可能是有组织犯罪。

这些不断变化的现实导致了攻击者排名的一层空前的专业水准。 他们不再对篡改网页感兴趣。 他们现在对偷窃信息、财务帐户和私有数据感兴趣，他们可以使用这些信息在开放市场上生成现金，或者利用特定业务、政治或军事位置。 比这更引人关注的是，这些以财务为目标的攻击者在侵入网络后会破坏基础结构，对人们造成伤害。

作为响应，组织通常会部署各种点解决方案，查找已知的攻击特征，重点做好企业外围防护或终结点防护。 这些解决方案会生成大量的低保真警报，需要安全分析师进行会审和调查。 大多数组织缺乏必要的时间和专业技术来响应此类警报 – 许多警报被置之不理。  

此外，攻击者还发展了其方法，以破坏许多基于签名的防御并[适应云环境](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/)。 必须采用新方法更快地确定新出现的威胁，加快检测和应对速度。

## <a name="what-are-security-alerts"></a>什么是安全警报？

警报是安全中心检测到对资源的威胁时生成的通知。 安全中心划分优先级并列出警报，以及快速调查问题所需的信息。 安全中心还提供了有关如何修正攻击的建议。

## 安全中心如何检测威胁？ <a name="detect-threats"> </a>

Microsoft 安全研究人员始终在不断地寻找威胁。 由于 Microsoft 在云中和本地都有全球存在，因此他们可以访问一组大规模的遥测数据。 通过丰富的数据集和多样化的集合，可发现新的攻击模式以及其本地使用者和企业产品以及其联机服务的趋势。 因此，当攻击者发布新的越来越复杂的漏斗利用方式时，安全中心就可以快速更新其检测算法。 此方法可以让用户始终跟上变化莫测的威胁环境。

为了检测真正的威胁并减少误报，安全中心从 Azure 资源和网络收集、分析和集成日志数据。 它还适用于连接的合作伙伴解决方案，如防火墙和 endpoint protection 解决方案。 安全中心会分析此信息, 通常会将来自多个来源的信息关联起来来识别威胁。

![安全中心数据收集和呈现](./media/security-center-alerts-overview/security-center-detection-capabilities.png)

安全中心使用各种高级安全分析，远不止几种基于攻击特征的方法。 可以充分利用大数据和 [机器学习](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) 技术的突破跨整个云结构对事件进行评估，检测那些使用手动方式不可能发现的威胁，并预测攻击的发展方式。 此类安全分析包括：

* **集成威胁智能**：利用 Microsoft 产品和服务、Microsoft 数字犯罪部门（DCU）、Microsoft 安全响应中心（MSRC）以及外部源提供的全球威胁情报，查找已知的不良执行组件。
* **行为分析**：运用已知模式发现恶意行为。
* **异常检测**：使用统计分析生成历史基线。 如果出现与已知基线偏离的情况，并且这些情况符合潜在攻击载体的行为，则会发出警报。

以下主题更详细地讨论了其中的每个分析。

### <a name="integrated-threat-intelligence"></a>集成威胁情报

Microsoft 提供大量的全球威胁情报。 遥测数据的来源包括：Azure、Office 365、Microsoft CRM Online、Microsoft Dynamics AX、outlook.com、MSN.com、Microsoft 数字犯罪部门 (DCU)、Microsoft 安全响应中心 (MSRC)。 研究人员也会收到在主要的云服务提供者之间共享的威胁情报信息，以及通过第三方的威胁情报源订阅的此类信息。 Azure 安全中心可能会在分析该信息后发出警报，提醒用户注意来自行为不端攻击者的威胁。

### <a name="behavioral-analytics"></a>行为分析

行为分析是一种技术，该技术会对数据进行分析并将数据与一系列已知模式对比。 不过，这些模式不是简单的特征， 需要对大型数据集运用复杂的机器学习算法来确定， 或者由分析专家通过仔细分析恶意行为来确定。 Azure 安全中心可以使用行为分析对虚拟机日志、虚拟网络设备日志、结构日志、故障转储和其他资源进行分析，确定受攻击的资源。

此外，还可以通过与其他信号的关联性，查看是否存在某个广泛传播活动的支持证据。 此关联性也可用于确定那些符合已确定的攻击特征的事件。 

### <a name="anomaly-detection"></a>异常情况检测

Azure 安全中心也通过异常检测确定威胁。 与行为分析（依赖于已知的从大型数据集派生的模式）相比，异常检测更“个性化”，注重特定于用户部署的基线。 运用机器学习确定部署的正常活动，并生成规则，定义可能表示安全事件的异常条件。

## <a name="continuous-monitoring-and-assessments"></a>持续监视和评估

Azure 安全中心的优势在于，在 Microsoft 的整个过程中，会持续监视威胁环境中的变化。 其中包括以下计划：

* **威胁情报监视**：威胁情报包括现有的或新出现的威胁的机制、指示器、含义和可操作建议。 此信息在安全社区共享，Microsoft 会持续监视内部和外部源提供的威胁情报源。
* **信号共享**：安全团队的见解会跨 Microsoft 的一系列云服务和本地服务、服务器、客户端终结点设备进行共享和分析。
* **Microsoft 安全专家**：持续接触 Microsoft 的各个工作在专业安全领域（例如取证和 Web 攻击检测）的团队。
* **检测优化**：针对实际的客户数据集运行相关算法，安全研究人员与客户一起验证结果。 通过检出率和误报率优化机器学习算法。

将这些措施结合起来，形成新的改进型检测方法，使用户能够即时受益，而用户不需采取任何措施。

## 安全警报类型<a name="security-alert-types"></a>

以下主题将指导你根据资源类型来完成不同的警报：

* [IaaS Vm 和服务器警报](security-center-alerts-iaas.md)
* [本机计算警报](security-center-alerts-compute.md)
* [数据服务警报](security-center-alerts-data-services.md)

以下主题介绍安全中心如何使用它从与 Azure 基础结构集成所收集的不同遥测，以便为 Azure 上部署的资源应用其他保护层：

* [服务层警报](security-center-alerts-service-layer.md)
* [与 Azure 安全产品集成](security-center-alerts-integration.md)

## <a name="what-are-security-incidents"></a>什么是安全事件？

安全事件是相关警报的集合, 而不是单独列出每个警报。 安全中心使用[云智能警报相关](security-center-alerts-cloud-smart.md)，将不同的警报和低保真信号关联到安全事件。

通过使用事件, 安全中心可提供攻击活动的单一视图和所有相关警报。 利用此视图，您可以快速了解攻击者采取的操作以及受影响的资源。 有关详细信息，请参阅[云智能警报相关性](security-center-alerts-cloud-smart.md)。

## <a name="next-steps"></a>后续步骤

本文介绍了安全中心提供的不同类型的警报。 有关详细信息，请参阅：

* [Azure 安全中心规划和操作指南](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [Azure 安全中心常见问题](https://docs.microsoft.com/azure/security-center/security-center-faq)

