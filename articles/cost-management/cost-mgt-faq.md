---
title: 有关 Azure 中 Cloudyn 的常见问题解答 | Microsoft Docs
description: 本文提供有关 Cloudyn 的一些常见问题的解答。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: troubleshooting
ms.service: cost-management
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 02a03adb128c140343032075ec334cbd6d88729b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66002029"
---
# <a name="frequently-asked-questions-for-cloudyn"></a>有关 Cloudyn 的常见问题解答

本文解答有关 Cloudyn 的一些常见问题。 如果你对 Cloudyn 有疑问，可以在[有关 Cloudyn 的常见问题解答](https://social.msdn.microsoft.com/Forums/en-US/231bf072-2c71-4121-8339-ac9d868137b9/faqs-for-cloudyn-cost-management?forum=Cloudyn)中提出问题。

## <a name="how-can-i-resolve-common-indirect-enterprise-setup-problems"></a>如何解决常见的间接企业设置问题？

首次使用 Cloudyn 门户时，企业协议或云解决方案提供商 (CSP) 用户可能会看到以下消息：

- “设置 Cloudyn”向导中显示“指定的 API 密钥不是顶级登记密钥”  。
- 企业协议门户中显示“直接登记 - 否”。
- Cloudyn 门户中显示“找不到过去 30 天的使用情况数据。 请与分发商联系，确保你的 Azure 帐户已启用标记”。

上面的错误消息表示 Azure 企业协议是通过经销商或 CSP 购买的。 经销商或 CSP 需为你的 Azure 帐户启用标记，这样，你才能在 Cloudyn 中查看自己的数据。 

下面是修复问题的方法：

1. 经销商需为你的帐户启用标记。  有关说明，请参阅[间接客户加入指南](https://ea.azure.com/api/v3Help/v2IndirectCustomerOnboardingGuide)。

2. 生成可在 Cloudyn 中使用的 Azure 企业协议密钥。 有关说明，请参阅[添加 Azure EA](quick-register-ea.md#register-with-cloudyn) 或[如何查找 EA 登记 ID 和 API 密钥](https://youtu.be/u_phLs_udig)。

只有 Azure 服务管理员可以启用 Cloudyn。 协同管理员的权限不足。

在生成用于设置 Cloudyn 的 Azure 企业协议 API 密钥之前，必须遵照以下主题中的说明启用 Azure 计费 API：

- [面向企业客户的报告 API 概述](../billing/billing-enterprise-api.md)
- **实现对 API 的数据访问**中的 [Microsoft Azure Enterprise Portal 报告 API](https://ea.azure.com/helpdocs/reportingAPI)


可能还需要向部门管理员、帐户所有者和企业管理员授予使用计费 API 查看费用的权限。 

## <a name="why-dont-i-see-optimizer-recommendations"></a>我为何看不到优化器建议？

只对已激活的帐户显示建议信息。 对于未激活的帐户，“优化器”报告类别中不会显示如下所述的任何建议信息：  

- 优化管理器
- 大小优化
- 低效率

如果无法查看任何优化器建议数据，则很有可能是你的帐户未激活。 若要激活帐户，需要使用 Azure 凭据将其注册。

激活帐户：

1.  在 Cloudyn 门户中，单击右上方的“设置”  ，然后“云帐户”  。
2.  在“Microsoft Azure 帐户”选项卡上，找到包含**未激活**订阅的帐户。
3.  在未激活帐户的右侧，单击“编辑”铅笔图标。 
4.  系统会自动检测租户 ID 和费率 ID。 单击“下一步”。 
5.  随后将重定向到 Azure 门户。 登录到门户并授权 Cloudyn 收集器访问你的 Azure 数据。
6.  接下来，将会重定向到 Cloudyn 帐户管理页，订阅中的帐户状态已更新为“活动”。  该订阅显示了一个绿色的勾选标记符号。
7.  如果有一个或多个订阅未显示绿色勾选标记符号，则表示你无权为订阅创建读取器应用 (CloudynCollector)。 具有更高订阅权限的用户需要重复步骤 3 和 4。  

完成前面的步骤后，在一到两天内即可查看优化器建议。 但是，最长可能需要在五天之后才能显示完整的优化数据。


## <a name="how-do-i-enable-suspended-or-locked-out-users"></a>如何启用已暂停或已锁定的用户？

首先，让我们来看一下导致用户帐户初始挂起  的常见应用场景。

> Admin1 可能是 Microsoft 云解决方案提供商或企业协议用户。 其组织已准备好开始使用 Cloudyn。  他通过 Azure 门户注册并登录到 Cloudyn 门户。 将 Cloudyn 服务和符号注册到 Cloudyn 门户中的人员，Admin1 变得*主管理员*。 管理员 1 不创建任何用户帐户。 但是，使用 Cloudyn 门户，它们执行创建 Azure 帐户并设置的实体层次结构。 Admin1 通知管理员 2，租户管理员，他们需要向 Cloudyn 注册并登录到 Cloudyn 门户。
>
> 管理员 2 通过 Azure 门户进行注册。 但是当他们尝试登录到 Cloudyn 门户，会得到一个错误，指示用户的帐户**挂起**。 主要管理员（管理员 1）将收到帐户挂起的通知。 管理员 1 需要激活管理员 2 的帐户并针对相应的实体授予管理员实体访问权限  ，并允许用户管理访问和激活用户帐户。


如果收到请求允许某个用户进行访问的警报，需要激活该用户帐户。

若要激活用户帐户，请执行以下操作：

1. 使用设置 Cloudyn 时所用的 Azure 管理用户帐户登录到 Cloudyn。 或者，使用拥有管理员访问权限的用户帐户登录。
2. 选择右上角的齿轮符号，再选择“用户管理”  。
3. 找到该用户，然后选择铅笔符号并编辑该用户。
4. 在“用户状态”下，将状态从“已暂停”更改为“活动”。   

Cloudyn 用户帐户使用单一登录从 Azure 建立连接。 如果用户错误地键入了密码，他们可能被锁定在 Cloudyn 之外，即使他们仍可访问 Azure。

如果在 Cloudyn 中更改电子邮件地址，使之与 Azure 中的默认地址不同，则可能会导致帐户被锁定。此时可能会显示“状态 initiallySuspended”。 如果用户帐户已锁定，请联系另一管理员重置帐户。

我们建议至少创建两个 Cloudyn 管理员帐户，以防其中一个帐户被锁定。

如果无法登录到 Cloudyn 门户，请确保使用正确的 URL 登录到 Cloudyn。 使用 [https://azure.cloudyn.com](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/CloudynMainBlade)。

避免使用 Cloudyn 直接 URL https://app.cloudyn.com 。

## <a name="how-do-i-activate-unactivated-accounts-with-azure-credentials"></a>如何使用 Azure 凭据激活尚未激活的帐户？

Cloudyn 发现你的 Azure 帐户后，会立即在基于成本的报告中提供成本数据。 但是，要让 Cloudyn 提供使用情况和性能数据，需要注册帐户的 Azure 凭据。 有关说明，请参阅[添加帐户或更新订阅](activate-subs-accounts.md#add-an-account-or-update-a-subscription)。

若要添加帐户的 Azure 凭据，请在 Cloudyn 门户中，选择帐户名（而不是订阅）右侧的编辑符号。

在将 Azure 凭据添加到 Cloudyn 之前，该帐户会一直显示为“未激活”。 

## <a name="how-do-i-add-multiple-accounts-and-entities-to-an-existing-subscription"></a>如何将多个帐户和实体添加到现有订阅？

使用更多的实体可将更多的企业协议添加到 Cloudyn 订阅。 有关详细信息，请参阅[创建和管理实体](tutorial-user-access.md#create-and-manage-entities)。

对于 CSP：

若要将更多 CSP 帐户添加到某个实体，请在创建新实体时选择“MSP 访问”而不是“企业”。   如果已将帐户注册为企业协议并想要添加 CSP 凭据，Cloudyn 支持人员可能需要修改帐户设置。 付费的 Azure 订阅者可在 Azure 门户中创建新的支持请求。 依次选择“帮助 + 支持”、“新建支持请求”。  

## <a name="currency-symbols-in-cloudyn-reports"></a>Cloudyn 报表中的货币符号

可能有多个使用不同货币的 Azure 帐户。 但在 Cloudyn 中，每个成本报表不显示超过一种货币类型。

如果有多个使用不同货币的订阅，则父实体及其子实体货币会以美元 ($) 显示  。 建议的最佳做法是避免在同一实体层次结构中使用不同的货币。 换而言之，实体结构中组织的所有订阅应使用同一货币。

Cloudyn 会自动检测企业协议订阅货币并在报表中正确显示。  但是，Cloudyn 仅对 CSP 和 Web 直通 Azure 帐户显示美元 ($)  。

## <a name="what-are-cloudyn-data-refresh-timelines"></a>Cloudyn 数据刷新时间线是什么？

Cloudyn 提供以下数据刷新时间线：

- **初始**：设置后，最长可能需要 24 小时才能查看 Cloudyn 中的成本数据。 另外，Cloudyn 最长可能需要花费 10 天时间来收集足够的数据，以显示大小调整建议。
- **每日**：在每个月的 10 号到月底，下一日的 UTC 时间加上 3 小时之后，Cloudyn 应会显示前一日的最新数据。
- **每月**：在每个月的 1 至 10 号，Cloudyn 只能显示截止前一个月月底的数据。

提供前一日的完整数据后，Cloudyn 会处理前一日的数据。 通常，在每日的 UTC 时间加上 3 小时后，前一日的数据就会在 Cloudyn 中提供。 某些数据（例如标记）可能需要额外的 24 小时才能得到处理。

在每个月份开始时，当前月份的数据不可收集。 在此期间，服务提供商会结算前一月份的帐单。 在每个月份开始后的 5 到 10 天内，前一月份的数据会显示在 Cloudyn 中。 在此期间，可能只会看到前一月份的摊余成本， 而看不到每日帐单或使用情况数据。 提供数据后，Cloudyn 会以回溯方式处理这些数据。 处理后，会显示每个月 5 到 10 号的所有数据。

如果将数据从 Azure 发送到 Cloudyn 时出现延迟，数据仍会记录在 Azure 中。 恢复连接后，数据会传输到 Cloudyn。

## <a name="cost-fluctuations-in-cloudyn-cost-reports"></a>Cloudyn 成本报告中的成本波动

只要云服务提供商发送更新的计费文件，成本报告就会显示成本波动。 在通常的每日或每月报告计划之外从云服务提供商收到新文件时，会发生波动的成本。 Cloudyn 重新计算不会导致成本变化。

在整个月中，云服务提供商发送的所有计费文件都是你每日费用的估算值。 有时数据会经常更新 - 有时每天会多次更新。 AWS 的更新比 Azure 更频繁。 当前一个月的计费计算完成并收到最终计费文件时，成本总计应保持稳定。 通常是在每月的 10 号前。

当你从云服务提供商处收到成本调整时，会发生变化。 接收信用额度是一个例子。 相关月份结束后的几个月可能会发生变化。 只要云服务提供商重新计算，就会显示更改。 Cloudyn 会更新其历史数据以确保重新计算所有调整。 它还会验证成本是否在报告中准确显示。

## <a name="how-can-a-direct-csp-configure-cloudyn-access-for-indirect-csp-customers-or-partners"></a>直接 CSP 如何为间接 CSP 客户或合作伙伴配置 Cloudyn 访问权限？

有关说明，请参阅[在 Cloudyn 中配置间接 CSP 访问权限](quick-register-csp.md#configure-indirect-csp-access-in-cloudyn)。

## <a name="what-causes-the-optimizer-menu-item-to-appear"></a>导致“优化器”菜单项显示的原因是什么？

添加 Azure 资源管理器访问权限并收集数据后，应该会看到“优化器”  选项。 若要激活 Azure 资源管理器访问权限，请参阅[我如何使用 Azure 凭据激活未激活的帐户？](#how-do-i-activate-unactivated-accounts-with-azure-credentials)

## <a name="is-cloudyn-agent-based"></a>Cloudyn 是否基于代理？

不。 不使用代理。 VM 的 Azure 虚拟机指标数据是通过 Microsoft Insights API 进行收集。 若要收集 Azure VM 中的指标数据，需要为 VM 启用诊断设置。

## <a name="do-cloudyn-reports-show-more-than-one-ad-tenant-per-report"></a>Cloudyn 报表是否每报表显示多个 AD 租户？

是的。 可以为拥有的每个 AD 租户[创建相应的云帐户实体](tutorial-user-access.md#create-and-manage-entities)。 然后，可以查看所有 Azure AD 租户数据和其他云平台提供程序（包括 Amazon Web Services 和 Google Cloud Platform）。
