---
title: 在 AD FS 中使用 Azure AD Connect Health | Microsoft Docs
description: 本页与 Azure AD Connect Health 相关，介绍如何监视本地 AD FS 基础结构。
services: active-directory
documentationcenter: ''
ms.reviewer: zhiweiwangmsft
author: billmath
manager: daveba
editor: curtand
ms.assetid: dc0e53d8-403e-462a-9543-164eaa7dd8b3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92825a9ef84edc30b6b34aa875f8a207c70c8511
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60350392"
---
# <a name="monitor-ad-fs-using-azure-ad-connect-health"></a>使用 Azure AD Connect Health 监视 AD FS
以下文档专门介绍如何使用 Azure AD Connect Health 来监视 AD FS 基础结构。 有关使用 Azure AD Connect Health 监视 Azure AD Connect（同步）的信息，请参阅 [使用用于同步的 Azure AD Connect Health](how-to-connect-health-sync.md)。此外，有关使用 Azure AD Connect Health 监视 Active Directory 域服务的信息，请参阅 [在 AD DS 中使用 Azure AD Connect Health](how-to-connect-health-adds.md)。

## <a name="alerts-for-ad-fs"></a>AD FS 的警报
Azure AD Connect Health 警报部分将提供活动警报列表。 每个警报均包含相关信息、解决方法步骤和相关文档的链接。

可以双击某个活动的警报或已解决的警报打开新的边栏选项卡，其中包含附加信息、解决警报的步骤，以及相关文档的链接。 还可以查看过去已解决警报的相关历史数据。

![Azure AD Connect Health 门户](./media/how-to-connect-health-adfs/alert2.png)

## <a name="usage-analytics-for-ad-fs"></a>AD FS 的使用情况分析
Azure AD Connect Health 使用情况分析可分析联合服务器的身份验证流量。 可以双击使用情况分析框打开使用情况分析边栏选项卡，显示多个指标和分组。

> [!NOTE]
> 要将使用情况分析与 AD FS 结合使用，请务必启用 AD FS 审核。 有关详细信息，请参阅 [为 AD FS 启用审核](how-to-connect-health-agent-install.md#enable-auditing-for-ad-fs)。
>
>

![Azure AD Connect Health 门户](./media/how-to-connect-health-adfs/report1.png)

若要选择其他指标、指定时间范围或更改分组，请右键单击使用情况分析图表并选择“编辑图表”。 然后可以指定时间范围、选择不同的指标以及更改分组。 可以查看基于不同“度量值”的身份验证流量分布，并使用以下部分中所述的相关“分组依据”参数对每个度量值进行分组:

**指标：请求总数** - 由 AD FS 服务器处理的请求总数。

|分组依据 | 分组意味着什么，它为什么很有用？ |
| --- | --- |
| 全部 | 显示所有 AD FS 服务器处理的请求总数的计数。|
| 应用程序 | 基于目标信赖方对请求总数分组。 此分组有助于了解具体某个应用程序正在接收多少百分比的总流量。 |
|  服务器 |基于处理请求的服务器对请求总数分组。 此分组有助于了解总流量的负载分布。
| 工作区加入 |基于请求是否来自已加入工作区（已知）的设备对请求总数分组。 此分组有助于了解是否使用标识基础结构未知的设备来访问资源。 |
|  身份验证方法 | 基于用于身份验证的身份验证方法对请求总数分组。 此分组有助于了解用于身份验证的常见身份验证方法。 可能的身份验证方法如下所示： <ol> <li>Windows 集成身份验证 (Windows)</li> <li>基于窗体的身份验证（窗体）</li> <li>SSO（单一登录）</li> <li>X509 证书身份验证（证书）</li> <br>如果联合服务器接收带有 SSO Cookie 的请求，则该请求被计为 SSO（单一登录）。 在这种情况下，如果此 cookie 有效，则不会要求用户提供凭据并该用户可无缝访问该应用程序。 如果有多个受联合服务器保护的信赖方，则这种行为很常见。 |
| 网络位置 | 基于用户的网络位置对请求总数分组。 该位置可以是 Intranet 或 Extranet。 此分组有助于了解来自 Intranet 或 Extranet 的流量分别为多少百分比。 |


**指标：失败请求总数** - 失败了的由联合身份验证服务处理的请求总数。 （此度量值仅在 Windows Server 2012 R2 的 AD FS 上可用）

|分组依据 | 分组意味着什么，它为什么很有用？ |
| --- | --- |
| 错误类型 | 基于预定义错误类型显示错误数。 此分组有助于了解常见类型的错误。 <ul><li>用户名或密码错误：因用户名或密码不正确而导致的错误。</li> <li>“Extranet 锁定”：收到无法访问 Extranet 的用户的请求而导致的失败 </li><li> “密码过期”：用户使用已过期密码登录而导致的失败。</li><li>“帐户已禁用”：用户使用已禁用的帐户登录导致的失败。</li><li>“设备身份验证”：用户无法使用“设备身份验证”进行身份验证导致的失败。</li><li>“用户证书身份验证”：用户因证书无效而无法进行身份验证而导致的失败。</li><li>“MFA”：用户无法使用“多重身份验证”进行身份验证导致的失败。</li><li>“其他凭据”：“颁发授权”：因授权失败而失败。</li><li>“颁发委托”：颁发委托错误导致的失败。</li><li>“令牌接受”：由于 ADFS 拒绝来自第三方标识提供者的令牌而导致的失败。</li><li>“协议”：因协议错误而失败。</li><li>“未知”：全部捕获。 任何其他不属于定义类别的失败。</li> |
| 服务器 | 基于服务器对错误分组。 这种分组有助于了解各服务器的错误分布情况。 分布不均匀可能表示服务器处于错误状态。 |
| 网络位置 | 基于请求的网络位置（Intranet 或 Extranet）对错误分组。 这种分组有助于了解失败的请求类型。 |
|  应用程序 | 基于目标应用程序（信赖方）对失败分组。 这种分组有助于了解错误数最多的目标应用程序。 |

**指标：用户计数** - 使用 AD FS 主动进行身份验证的独立用户平均数

|分组依据 | 分组意味着什么，它为什么很有用？ |
| --- | --- |
|全部 |此指标提供所选时间段内使用联合身份验证服务的用户平均数。 不对用户进行分组。 <br>平均值取决于所选的时间段。 |
| 应用程序 |基于目标应用程序（信赖方）对用户平均数分组。 这种分组有助于了解使用具体某个应用程序的用户数量。 |

## <a name="performance-monitoring-for-ad-fs"></a>AD FS 的性能监视
Azure AD Connect Health 性能监视提供有关度量值的监视信息。 选择“监视”框会打开一个边栏选项卡，其中包含有关指标的详细信息。

![Azure AD Connect Health 门户](./media/how-to-connect-health-adfs/perf1.png)

通过选择边栏选项卡顶部的“筛选器”选项，可以按服务器进行筛选，以查看单个服务器的度量值。 要更改度量值，请右键单击监视边栏选项卡下的监视图表，并选择“编辑图表”（或选择“编辑图表”按钮）。 在打开的新边栏选项卡中，可从下拉列表中选择其他度量值，并指定查看性能数据的时间范围。

## <a name="top-50-users-with-failed-usernamepassword-logins"></a>用户名/密码登录失败的前 50 个用户
AD FS 服务器上身份验证请求失败的常见原因之一就是请求所提供的凭据无效，也就是错误的用户名或密码。 这往往是用户的密码太复杂、忘记密码或打错字。

但还是有其他原因导致 AD FS 服务器所处理的请求数量超出预期，例如：可缓存用户凭据的应用程序和到期的凭据，或尝试以一系列的常见密码登录某个帐户的恶意用户。 这两个示例都是可能导致请求激增的合理原因。

Azure AD Connect Health for ADFS 提供一份报告，内容有关因为用户名或密码无效而登录尝试失败的前 50 个用户。 处理场中所有 AD FS 服务器所生成的审核事件即可完成此报告。

![Azure AD Connect Health 门户](./media/how-to-connect-health-adfs/report1a.png)

可以在这份报告中轻松获取以下信息：

* 过去 30 天内用户名/密码错误的失败请求总数
* 每天由于用户名/密码不正确而登录失败的平均用户人数。

单击此部分可转到提供其他详细信息的主要报告边栏选项卡。 此边栏选项卡包含一个提供趋势信息的图形，可帮助建立有关用户名或密码错误的请求的基准。 此外，该边栏选项卡还提供在过去一周尝试失败次数最多的 50 个用户的列表。 请注意，可以通过过去一周前 50 个用户的情况来确定错误密码尝试的峰值。  

该图形提供以下信息：

* 每天由于用户名/密码不正确而登录失败的总数。
* 每天登录失败的唯一用户总数。
* 最后一个请求的客户端 IP 地址

![Azure AD Connect Health 门户](./media/how-to-connect-health-adfs/report3a.png)

该报告提供以下信息：

| 报告项 | 描述 |
| --- | --- |
| 用户 ID |显示使用的用户 ID。 此值是用户键入的内容，在某些情况下是使用的错误用户 ID。 |
| 失败尝试次数 |显示该特定用户 ID 的尝试失败总次数。 该表已按最多失败尝试次数的降序排序。 |
| 上次失败时间 |显示上次发生失败时的时间戳。 |
| 上次失败 IP |显示最后一个错误请求中的客户端 IP 地址。 如果在此值中看到多个 IP 地址，则可能是因为其中包括了转发客户端 IP 以及用户上一次尝试的请求 IP。  |

> [!NOTE]
> 此报告每隔 12 小时以该段时间内收集的新信息自动进行更新。 因此，报告中不包括过去 12 小时内的登录尝试。

## <a name="related-links"></a>相关链接
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Azure AD Connect Health 代理安装](how-to-connect-health-agent-install.md)
* [风险 IP 报表](how-to-connect-health-adfs-risky-ip.md)

