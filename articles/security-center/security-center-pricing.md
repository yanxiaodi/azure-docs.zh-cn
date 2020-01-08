---
title: 升级到安全中心的标准层以增强安全性 | Microsoft Docs
description: 本文提供有关 Azure 安全中心定价的信息。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/17/2019
ms.author: memildin
ms.openlocfilehash: 0663e97dabe8b5f50ee9605f0bc9319c7a2aa13c
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71201245"
---
# <a name="upgrade-to-security-centers-standard-tier-for-enhanced-security"></a>升级到安全中心的标准层以增强安全性
Azure 安全中心为 Azure、本地和其他云中运行的工作负载提供统一的安全管理和高级威胁防护功能。 它可以提供针对混合云工作负载的可见性和可控性、可减小在威胁下的曝光面的积极防御功能以及有助于随时响应快速演变的网络攻击的智能检测功能。

## <a name="pricing-tiers"></a>定价层
安全中心分两个层提供：

- 免费层在所有 Azure 订阅上自动启用，并提供安全策略、持续的安全评估和切实可行的安全建议来帮助你保护 Azure 资源。
- 标准层将免费层的功能扩展到私有云和其他公有云中运行的工作负载，并在混合云工作负载中提供了统一的安全管理和威胁防护。 标准层还增加了高级威胁检测功能，它使用内置行为分析和机器学习识别攻击和零时差攻击，并使用访问和应用程序控件减小在网络攻击和恶意软件下的曝光面，此外还有更多其他操作。 可以免费试用标准层。 安全中心标准版支持 Azure 资源，包括 Vm、虚拟机规模集、应用服务、SQL 服务器和存储帐户。 如果你使用 Azure 安全中心标准，则可以根据资源类型选择不再支持。 


有关详细信息，请参阅安全中心[定价页](https://azure.microsoft.com/pricing/details/security-center/)。

## <a name="try-standard-free-for-30-days"></a>免费试用标准层 30 天
标准层在前 30 天免费提供。 30 天后，如果选择继续使用服务，会自动开始收取使用费用。

可将整个 Azure 订阅升级到标准层，这样此订阅中的所有资源均将继承此层。

若要获取标准层，请执行以下操作：

1. 选择 "**安全中心**" 主菜单上的 "**定价 & 设置**"。
2. 选择要升级到标准的订阅。
3. 选择 "**定价层**"。
4. 选择“标准层”以进行升级。
5. 单击“保存”。

（图像中的价格仅用于示例目的。）![安全中心定价](./media/security-center-pricing/get-standard.png)

> [!NOTE]
> 若要启用所有的安全中心功能，必须将标准定价层应用到包含适用虚拟机的订阅。 为工作区配置定价层不会为 Azure 资源启用实时 VM 访问、自适应应用程序控件和网络检测功能。
>
>

## <a name="why-upgrade-to-standard"></a>为什么要升级到标准？
安全中心可为混合云工作负载提供增强的安全和威胁防护功能，其中包括：

- 混合安全 – 在所有本地和云工作负载上获得统一的安全视图。 应用安全策略并持续评估混合云工作负载的安全性，确保符合安全标准。 收集、搜索并分析来自各种来源（包括防火墙和其他合作伙伴解决方案）的安全数据。
- 高级威胁检测 - 使用高级分析和 Microsoft Intelligent Security Graph，获得针对不断演变的网络攻击的优势。  利用内置行为分析和机器学习来识别攻击和零时差攻击。 监视网络、计算机和云服务是否出现有即将来袭的攻击和攻破后活动。 使用交互工具和上下文威胁智能简化调查。
- 访问和应用程序控件 - 通过应用适合特定工作负载且由机器学习提供支持的允许列表建议，阻止恶意软件和其他不需要的应用程序。 通过对 Azure VM 上管理端口的实时控制访问减小网络攻击面，显著减小在暴力和其他网络攻击下的曝光面。


## <a name="next-steps"></a>后续步骤
本文介绍了安全中心定价。 若要详细了解标准层增强的安全性和高级威胁防护功能，请参阅：

- [高级威胁检测](security-center-threat-report.md)
- [实时 VM 访问控制](security-center-just-in-time.md)

<!--Image references-->
[1]: ./media/security-center-pricing/get-standard.png
