---
title: Azure web 应用程序防火墙的 Azure 第一道防线是什么？ （预览版）
description: 了解 Azure web 应用程序防火墙的 Azure 第一道防线服务免受恶意攻击保护 web 应用程序。
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud;tyao
ms.openlocfilehash: 122e9687ee313edff34e5a4fd9a44b1026a63811
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66478794"
---
# <a name="what-is-azure-web-application-firewall-for-azure-front-door"></a>Azure web 应用程序防火墙的 Azure 第一道防线是什么？

Azure Web 应用程序防火墙 (WAF) 为那些使用 Azure Front Door 进行全球交付的 Web 应用程序提供集中保护。 设计和运行此防火墙的目的是保护 Web 服务免受常见的攻击和漏洞的危害，使服务对你的用户高度可用，另外还帮助你满足符合性要求。


Web 应用程序越来越多的恶意攻击，例如拒绝服务洪水、 SQL 注入攻击和跨站点脚本攻击目标。 这些恶意攻击可能会导致服务中断和数据丢失，会带来严重的威胁到 web 应用程序所有者。

防止应用程序代码遭受此类攻击颇具挑战性，并且可能需要对应用程序拓扑的多个层进行严格的维护、修补和监视。 集中式 Web 应用程序防火墙有助于大幅简化安全管理，为抵卸威胁或入侵的应用程序管理员提供更好的保障。 此外，通过修补在中央位置，而不是保护每个单独的 web 应用程序的已知的漏洞的安全威胁更快地响应可以 WAF 解决方案。

WAF 的第一道防线是一个全局和集中式解决方案。 部署在全球各地的 Azure 网络边缘位置上，每个传入请求 WAF 启用 web 应用程序提供的第一道防线检查在网络边缘。 这允许 WAF，以防止恶意攻击的攻击源，接近，才能进入虚拟网络，并提供大规模的全局保护而不牺牲性能。 WAF 策略可以方便地链接到你的订阅中的任何第一道防线配置文件和新的规则可以部署在几分钟之内，从而可以快速响应不断变化的威胁的模式。

![Azure web 应用程序防火墙](./media/waf-overview/web-application-firewall-overview2.png)

此外可以使用应用程序网关启用 azure WAF。 有关详细信息，请参阅[Web 应用程序防火墙](../application-gateway/waf-overview.md)。

## <a name="waf-policy-and-rules"></a>WAF 的策略和规则

您可以配置 WAF 策略并将关联到一个或多个第一道防线前端来保护该策略。 WAF 策略包括两种类型的安全规则：

- 由客户编写的自定义规则。

- 是 Azure 托管的集合的托管规则集进行预配置规则的集。

当同时存在时，会处理中托管的规则集的规则之前处理自定义规则。 规则由某个匹配条件、 优先级和操作。 支持的操作类型包括：允许，块、 日志和重定向。 可以创建完全自定义的策略通过组合托管和自定义规则来满足您特定的应用程序的保护要求。

策略中的规则处理优先顺序其中优先级是一个唯一的整数，它定义要处理的规则的顺序。 较小的整数值表示较高的优先级，这些具有较高的整数值的规则之前评估。 一旦匹配规则时，相应在规则中定义的操作被应用于该请求。 这种匹配处理后，不会进一步处理优先级较低的规则。

提供的第一道防线的 web 应用程序可以具有与之关联一次只能有一个 WAF 策略。 但是，您可以不包含任何与之关联的 WAF 策略具有第一道防线配置。 如果存在 WAF 策略，则它被复制到所有我们边缘位置，以确保在世界各地的安全策略中的一致性。

## <a name="waf-modes"></a>WAF 模式

WAF 策略可以配置为在以下两种模式中运行：

- **检测模式：** 在检测模式下运行时，WAF 不采用监视器以外的任何其他操作，并记录到 WAF 日志将记录请求和其匹配的 WAF 规则。 您可以打开日志记录诊断的第一道防线 (使用门户时，这可以通过转到**诊断**在 Azure 门户中的部分)。

- **阻止模式下：** 配置为在阻止模式下运行时，WAF 会采取指定的操作，如果某个请求与规则匹配，并且如果找到匹配项，则计算具有较低优先级没有更多的规则。 此外在 WAF 日志中记录任何匹配的请求。

## <a name="waf-actions"></a>WAF 操作

WAF 客户可以选择当请求符合规则的条件时运行从某项操作：

- **Allow：** 请求将经过 WAF 并转发到后端。 无需再较低优先级的规则可以阻止此请求。
- **块：** 请求被阻塞和 WAF 而无需将请求转发到后端发送到客户端的响应。
- **日志：** WAF 日志中记录请求和 WAF 将继续评估低优先级规则。
- **重定向：** WAF 将请求重定向到指定的 URI。 指定的 URI 是一个策略级别设置。 一次配置相匹配的所有请求**重定向**操作将发送到该 URI。

## <a name="waf-rules"></a>WAF 规则

WAF 策略可以包含两种类型的安全规则-自定义规则，客户和托管规则集，由 Azure 托管预配置的规则集。

### <a name="custom-authored-rules"></a>创作的自定义规则

可以配置自定义规则 WAF，如下所示：

- **IP 允许列表和阻止列表：** 可以配置自定义规则以控制对 web 应用程序基于客户端 IP 地址或 IP 地址范围的列表的访问。 支持 IPv4 和 IPv6 地址类型。 此列表可以配置为阻止或允许这些请求的源 IP 与列表中的 IP 相匹配。

- **地理基于的访问控制：** 可以配置自定义规则以控制对 web 应用程序基于与客户端的 IP 地址关联的国家/地区代码的访问。

- **HTTP 参数基于访问控制：** 你可以配置自定义规则基于匹配的 HTTP/HTTPS 请求参数，例如查询字符串、 POST 参数，请求 URI、 请求标头和请求正文的字符串。

- **请求基于方法的访问控制：** 您可以配置基于如 GET、 PUT 或 HEAD 请求的 HTTP 请求方法的自定义规则。

- **大小限制：** 你可以配置自定义规则基于的查询字符串的 Uri，如请求的特定部分的长度或请求正文。

- **速率限制规则：** 速率控制规则用于限制任何客户端 IP 发出的异常高的流量。 上一分钟持续期间从客户端 IP 允许 web 请求数，可以配置阈值。 这是不同于 IP 基于列表的允许/阻止自定义规则，或者允许所有或从客户端 IP 的所有请求的块。 速率限制可以与其他匹配条件，例如 HTTP (S) 参数匹配的粒度速率控制的组合。

### <a name="azure-managed-rule-sets"></a>Azure 托管的规则集

Azure 托管的规则集可以方便地部署一组通用的安全威胁的防护。 由于此类规则集由 Azure 管理，根据需要来防止新的攻击特征码更新的规则。 在公共预览版中，Azure 托管默认规则集包含的规则与以下的威胁类别：

- 跨站点脚本
- Java 攻击
- 本地文件包含
- PHP 注入攻击
- 远程命令执行
- 远程文件包含
- 会话固定
- SQL 注入保护
- 协议攻击者

新的攻击特征添加到规则集时，默认规则集的版本号将递增。
默认情况下，在检测模式下在 WAF 策略中启用了默认规则集。 您可以禁用或启用到默认规则集内以满足应用程序的需求的单个规则。 此外可以设置每个规则的特定操作 （允许/阻止/重定向/日志）。 默认操作是到块。 此外，自定义规则可以在相同的 WAF 策略中配置如果你想要绕过所有默认规则集中的预配置规则。
然后再评估中的默认规则集的规则始终应用自定义规则。 如果某个请求与匹配的自定义规则，则应用相应规则操作，并且阻止或向后端，而无需任何进一步自定义规则或规则在默认规则集的调用传递请求。 此外，您可以选择从 WAF 策略中删除默认规则集。


### <a name="bot-protection-rule-preview"></a>智能机器人应用程序防护规则 （预览版）

Waf 对来自已知的恶意 IP 地址的请求执行自定义操作，可以启用托管的智能机器人应用程序保护规则集。 IP 地址源自 Microsoft 威胁智能源。 [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence)为 Microsoft 威胁智能提供支持，并由多个服务，包括 Azure 安全中心。

![智能机器人应用程序保护规则集](./media/waf-front-door-configure-bot-protection/BotProtect2.png)

> [!IMPORTANT]
> 智能机器人应用程序保护规则集目前处于公共预览状态，并提供预览版服务级别协议。 某些功能可能不受支持或者受限。  有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

如果启用了智能机器人应用程序保护，在 FrontdoorWebApplicationFirewallLog 日志记录匹配恶意机器人的客户端 Ip 的传入请求。 你可以从存储帐户访问 WAF 日志事件中心或 log analytics。 

## <a name="configuration"></a>配置

配置和部署所有 WAF 规则类型完全支持使用 Azure 门户、 REST Api、 Azure 资源管理器模板和 Azure PowerShell。

## <a name="monitoring"></a>监视

在第一道防线 waf 监视与 Azure Monitor 来跟踪警报和轻松监视流量趋势集成。

## <a name="next-steps"></a>后续步骤

- 了解如何[WAF 策略配置为使用 Azure 门户的第一道防线](waf-front-door-create-portal.md)