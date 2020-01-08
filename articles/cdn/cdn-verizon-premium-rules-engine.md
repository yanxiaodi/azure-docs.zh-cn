---
title: 重写 HTTP 行为使用 Azure CDN from Verizon Premium 规则引擎 |Microsoft Docs
description: 规则引擎允许你自定义 HTTP 请求例如，阻止交付某些类型的内容的处理通过 Azure CDN from Verizon Premium、 定义缓存策略以及修改 HTTP 标头。
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: fa08f59f3b69395d548649bb131e04c5071c053d
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593193"
---
# <a name="override-http-behavior-using-the-azure-cdn-from-verizon-premium-rules-engine"></a>重写 HTTP 行为使用 Azure CDN from Verizon Premium 规则引擎

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>概述

Azure CDN 规则引擎允许你自定义处理 HTTP 请求的方式。 例如，阻止传送某些内容类型、定义缓存策略或修改 HTTP 标头。 本教程演示如何创建一个用于更改 CDN 资产缓存行为的规则。 有关规则引擎语法的详细信息，请参阅 [Azure CDN 规则引擎参考](cdn-verizon-premium-rules-engine-reference.md)。

## <a name="access"></a>Access

若要访问规则引擎，必须先从“CDN 配置文件”  页顶部选择“管理”  以访问 Azure CDN 管理页。 然后根据是否终结点已针对动态站点加速 (DSA) 进行优化，使用适用于终结点类型的规则集访问规则引擎：

- 已针对常规 Web 交付优化的终结点或已进行其他非 DSA 优化的终结点：
    
    选择“HTTP 大型”  选项卡，然后选择“规则引擎”  。

    ![适用于 HTTP 的规则引擎](./media/cdn-rules-engine/cdn-http-rules-engine.png)

- 已针对 DSA 进行优化的终结点：
    
    选择“ADN”  选项卡，然后选择“规则引擎”  。
    
    ADN 是 Verizon 用于指定 DSA 内容的术语。 配置文件中未针对 DSA 进行优化的任何终结点将忽略此处创建的任何规则。

    ![适用于 DSA 的规则引擎](./media/cdn-rules-engine/cdn-dsa-rules-engine.png)

## <a name="tutorial"></a>教程

1. 从“CDN 配置文件”页中，选择“管理”   。
   
    ![CDN 配置文件管理按钮](./media/cdn-rules-engine/cdn-manage-btn.png)
   
    CDN 管理门户打开。

2. 选择“HTTP 大型”  选项卡，然后选择“规则引擎”  。
   
    将显示新规则的选项。
   
    ![CDN 新规则选项](./media/cdn-rules-engine/cdn-new-rule.png)
   
   > [!IMPORTANT]
   > 多个规则的列出顺序会影响处理它们的方式。 后一个规则可能会覆盖前一个规则所指定的操作。
   >

3. 在“**名称/描述**”文本框中输入一个名称。

4. 标识该规则适用的请求类型。 使用默认的匹配条件：**始终**。
   
   ![CDN 规则匹配条件](./media/cdn-rules-engine/cdn-request-type.png)
   
   > [!NOTE]
   > 此下拉列表中提供了多个匹配条件。 有关当前所选匹配条件的信息，请选择其左侧的蓝色信息图标。
   >
   >  有关条件表达式的详细列表，请参阅[规则引擎条件表达式](cdn-verizon-premium-rules-engine-reference-match-conditions.md)。
   >  
   > 有关匹配条件的详细列表，请参阅[规则引擎匹配条件](cdn-verizon-premium-rules-engine-reference-match-conditions.md)。
   >
   >

5. 若要添加新功能，请选择“功能”  旁边的 **+** 按钮。  在左侧的下拉列表中，选择“**强制内部最长时间**”。  在显示的文本框中，输入 **300**。 不要更改剩余的默认值。
   
   ![CDN 规则功能](./media/cdn-rules-engine/cdn-new-feature.png)
   
   > [!NOTE]
   > 此下拉列表中提供了多个功能。 有关当前所选功能的信息，请选择其左侧的蓝色信息图标。
   >
   > 对于“强制内部最大有效期”  ，将重写资产的 `Cache-Control` 和 `Expires` 标头，以控制 CDN 边缘节点何时从源刷新资产。 在此示例中，CDN 边缘节点将缓存资产 300 秒或 5 分钟，然后再从其源刷新资产。
   >
   > 有关功能的详细列表，请参阅[规则引擎功能](cdn-verizon-premium-rules-engine-reference-features.md)。
   >
   >

6. 选择“添加”  ，以保存新规则。  新规则现在正在等待批准。 获得批准后，状态将从“挂起的 XML”  更改为“活动 XML”  。
   
   > [!IMPORTANT]
   > 规则更改最多可能需要 10 分钟，才能在整个 Azure CDN 中传播。
   >
   >

## <a name="see-also"></a>请参阅

- [Azure CDN 概述](cdn-overview.md)
- [规则引擎参考](cdn-verizon-premium-rules-engine-reference.md)
- [规则引擎匹配条件](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [规则引擎条件表达式](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [规则引擎功能](cdn-verizon-premium-rules-engine-reference-features.md)
- [Azure Fridays:Azure CDN 强大的新高级功能](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/)（视频）