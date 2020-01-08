---
title: 视频已编制索引，Azure SaaS SQL 应用 | Microsoft Docs
description: 本文对一个视频中的各个时间点进行了索引。这是 2017 年 10 月 11 日举行的 Ignite 会议上录制的关于 SaaS DB 租户应用设计的视频，时长 81 分钟。 可直接跳到感兴趣的部分。 至少描述了 3 种模式。 描述了简化开发和管理的 Azure 功能。
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, sstein
ms.date: 12/18/2018
ms.openlocfilehash: 7c1f93bb7cfe1e088aa88d9ff194c8fbce9ea3c6
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68570228"
---
# <a name="video-indexed-and-annotated-for-multi-tenant-saas-app-using-azure-sql-database"></a>关于使用 Azure SQL 数据库的多租户 SaaS 应用的视频，包含索引和批注

本文是有关 SaaS 租户模型或模式的 81 分钟视频的时间位置批注索引。 可以在本文中向后跳转或向前跳转至视频中感兴趣的部分。 视频介绍 Azure SQL 数据库上多租户数据库应用程序的主要设计选项。 该视频包括演示和管理代码演练，有时甚至可以通过实际操作提供比书面文档更加详细的信息。

该视频扩充了以下书面文档中的信息： 
- *概念：* [多租户 SaaS 数据库租户模式][saas-concept-design-patterns-563e]
- *教程：* [Wingtip 票证 SaaS 应用程序][saas-how-welcome-wingtip-app-679t]

视频和文章描述在云中的 Azure SQL 数据库上创建多租户应用程序的多个阶段。 Azure SQL 数据库的特殊功能使多租户应用更易于开发、实施和管理且性能可靠。

我们会定期更新书面文档。 由于视频未被编辑或更新，因此最终许多详细信息可能会过时。



## <a name="sequence-of-38-time-indexed-screenshots"></a>38 个时间索引的屏幕截图序列

本节将在 81 分钟的视频中对 38 个讨论的时间位置进行索引。 每个时间索引都带有视频中屏幕截图的批注，有时还有其他信息。

每个时间索引采用的格式均为 h:mm: ss。 例如，标记为“会话目标”的第二个索引时间位置大概始于 0:03:11。


### <a name="compact-links-to-video-indexed-time-locations"></a>到视频索引时间位置的紧凑型链接

以下标题是到本文后面相应批注部分的链接：

- [1.（开始）欢迎使用幻灯片，0:00:03](#anchor-image-wtip-min00001)
- [2.会话目标，0:03:11](#anchor-image-wtip-min00311)
- [3.议程，0:04:17](#anchor-image-wtip-min00417)
- [4.多租户 Web 应用，0:05:05](#anchor-image-wtip-min00505)
- [5.操作中的应用 Web 窗体，0:05:55](#anchor-image-wtip-min00555)
- [6.每个租户的成本（缩放、隔离、恢复），0:09:31](#anchor-image-wtip-min00931)
- [7.多租户数据库模型：优点和缺点，0:11:59](#anchor-image-wtip-min01159)
- [8.混合模型混合 MT/ST 的优点，0:13:01](#anchor-image-wtip-min01301)
- [9.单租户与多租户：优点和缺点，0:16:44](#anchor-image-wtip-min01644)
- [10.池对于不可预知的工作负荷来说是经济高效的，0:19:36](#anchor-image-wtip-min01936)
- [11.每个租户一个数据库和混合 ST/MT 的演示，0:20:08](#anchor-image-wtip-min02008)
- [12.显示 Dojo 的实时应用窗体，0:20:29](#anchor-image-wtip-min02029)
- [13.看到的是 MYOB 而不是 DBA，0:28:54](#anchor-image-wtip-min02854)
- [14.MYOB 弹性池使用情况示例，0:29:40](#anchor-image-wtip-min02940)
- [15.借鉴 MYOB 和其他 ISV，0:31:36](#anchor-image-wtip-min03136)
- [16.模式组合到 E2E SaaS 方案中，0:43:15](#anchor-image-wtip-min04315)
- [17.Canonical 混合多租户 SaaS 应用，0:47:33](#anchor-image-wtip-min04733)
- [18.Wingtip SaaS 示例应用，0:48:10](#anchor-image-wtip-min04810)
- [19.教程中介绍的方案和模式，0:49:10](#anchor-image-wtip-min04910)
- [20.教程和 GitHub 存储库演示，0:50:18](#anchor-image-wtip-min05018)
- [21.GitHub 存储库 Microsoft/WingtipSaaS，0:50:38](#anchor-image-wtip-min05038)
- [22.浏览模式，0:56:20](#anchor-image-wtip-min05620)
- [23.预配租户和载入，0:57:44](#anchor-image-wtip-min05744)
- [24.预配租户和应用程序连接，0:58:58](#anchor-image-wtip-min05858)
- [25.预配单个租户的管理脚本演示，0:59:43](#anchor-image-wtip-min05943)
- [26.PowerShell 预配和编录，1:00:02](#anchor-image-wtip-min10002)
- [27.T-SQL SELECT * FROM TenantsExtended，1:03:30](#anchor-image-wtip-min10330)
- [28.管理不可预知的租户工作负荷，1:04:36](#anchor-image-wtip-min10436)
- [29.弹性池监视，1:06:39](#anchor-image-wtip-min10639)
- [30.负载生成和性能监视，1:09:42](#anchor-image-wtip-min10942)
- [31.大规模架构管理，1:10:33](#anchor-image-wtip-min11033)
- [32.跨租户数据库的分布式查询，1:12:21](#anchor-image-wtip-min11221)
- [33.票证生成演示，1:12:32](#anchor-image-wtip-min11232)
- [34.SSMS 临时分析，1:12:46](#anchor-image-wtip-min11246)
- [35.将租户数据提取到 SQL DW 中，1:16:32](#anchor-image-wtip-min11632)
- [36.日销售分配图，1:16:48](#anchor-image-wtip-min11648)
- [37.总结和行动号召，1:19:52](#anchor-image-wtip-min11952)
- [38.有关详细信息的资源，1:20:42](#anchor-image-wtip-min12042)


&nbsp;

### <a name="annotated-index-time-locations-in-the-video"></a>视频中带批注的索引时间位置

点击任意屏幕截图即可转到视频中的确切时间位置。


&nbsp; <a name="anchor-image-wtip-min00001"/>
#### <a name="1-start-welcome-slide-00001"></a>1.（开始）欢迎使用幻灯片，0:00:01

借鉴 MYOB：设计 Azure SQL 数据库上的 SaaS 应用程序模式 - BRK3120

[![欢迎使用幻灯片][image-wtip-min00003-brk3120-whole-welcome]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1)

- 标题：借鉴 MYOB：设计 Azure SQL 数据库上的 SaaS 应用程序模式
- Bill.Gibson@microsoft.com
- Azure SQL 数据库首席计划经理
- Microsoft Ignite 专场 BRK3120，美国佛罗里达州奥兰多，2017 年 10 月 11 日


&nbsp; <a name="anchor-image-wtip-min00311"/>
#### <a name="2-session-objectives-00153"></a>2.专场目标，0:01:53
[![会话目标][image-wtip-min00311-session]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=113)

- 多租户应用的替代模式，及优缺点。
- SaaS 模式可以降低开发、管理和资源成本。
- 应用+脚本示例。
- PaaS 功能+ SaaS 模式可让 SQL 数据库成为多租户 SaaS 的高度可缩放、经济高效的数据平台。


&nbsp; <a name="anchor-image-wtip-min00417"/>
#### <a name="3-agenda-00409"></a>3.议程，0:04:09
[![议程][image-wtip-min00417-agenda]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=249)


&nbsp; <a name="anchor-image-wtip-min00505"/>
#### <a name="4-multi-tenant-web-app-00500"></a>4.多租户 Web 应用，0:05:00
[![Wingtip SaaS 应用：多租户 Web 应用][image-wtip-min00505-web-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=300)


&nbsp; <a name="anchor-image-wtip-min00555"/>
#### <a name="5-app-web-form-in-action-00539"></a>5.操作中的应用 Web 窗体，0:05:39
[![操作中的应用 Web 窗体][image-wtip-min00555-app-web-form]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=339)


&nbsp; <a name="anchor-image-wtip-min00931"/>
#### <a name="6-per-tenant-cost-scale-isolation-recovery-00658"></a>6.每个租户的成本（缩放、隔离、恢复），0:06:58
[![每个租户的成本、缩放、隔离、恢复][image-wtip-min00931-per-tenant-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=418)


&nbsp; <a name="anchor-image-wtip-min01159"/>
#### <a name="7-database-models-for-multi-tenant-pros-and-cons-00952"></a>7.多租户数据库模型：优点和缺点，0:09:52
[![多租户数据库模型：优点和缺点][image-wtip-min01159-db-models-pros-cons]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=592)


&nbsp; <a name="anchor-image-wtip-min01301"/>
#### <a name="8-hybrid-model-blends-benefits-of-mtst-01229"></a>8.混合模型混合 MT/ST 的优点，0:12:29
[![混合模型混合 MT/ST 的优点][image-wtip-min01301-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=749)


&nbsp; <a name="anchor-image-wtip-min01644"/>
#### <a name="9-single-tenant-vs-multi-tenant-pros-and-cons-01311"></a>9.单租户与多租户：优点和缺点，0:13:11
[![单租户与多租户：优点和缺点][image-wtip-min01644-st-vs-mt]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=791)


&nbsp; <a name="anchor-image-wtip-min01936"/>
#### <a name="10-pools-are-cost-effective-for-unpredictable-workloads-01749"></a>10.池对于不可预知的工作负荷来说是经济高效的，0:17:49
[![池对于不可预知的工作负荷来说是经济高效的][image-wtip-min01936-pools-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1069)


&nbsp; <a name="anchor-image-wtip-min02008"/>
#### <a name="11-demo-of-database-per-tenant-and-hybrid-stmt-01959"></a>11.每个租户一个数据库和混合 ST/MT 的演示，0:19:59
[![每个租户一个数据库和混合 ST/MT 的演示][image-wtip-min02008-demo-st-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1199)


&nbsp; <a name="anchor-image-wtip-min02029"/>
#### <a name="12-live-app-form-showing-dojo-02010"></a>12.显示 Dojo 的实时应用窗体，0:20:10
[![显示 Dojo 的实时应用窗体][image-wtip-min02029-live-app-form-dojo]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1210)

&nbsp; <a name="anchor-image-wtip-min02854"/>
#### <a name="13-myob-and-not-a-dba-in-sight-02506"></a>13.看到的是 MYOB 而不是 DBA，0:25:06
[![看到的是 MYOB 而不是 DBA][image-wtip-min02854-myob-no-dba]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1506)


&nbsp; <a name="anchor-image-wtip-min02940"/>
#### <a name="14-myob-elastic-pool-usage-example-02930"></a>14.MYOB 弹性池使用情况示例，0:29:30
[![MYOB 弹性池使用情况示例][image-wtip-min02940-myob-elastic]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1770)


&nbsp; <a name="anchor-image-wtip-min03136"/>
#### <a name="15-learning-from-myob-and-other-isvs-03125"></a>15.借鉴 MYOB 和其他 ISV，0:31:25
[![借鉴 MYOB 和其他 ISV][image-wtip-min03136-learning-isvs]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1885)


&nbsp; <a name="anchor-image-wtip-min04315"/>
#### <a name="16-patterns-compose-into-e2e-saas-scenario-03142"></a>16.模式组合到 E2E SaaS 方案中，0:31:42
[![模式组合到 E2E SaaS 方案中][image-wtip-min04315-patterns-compose]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1902)


&nbsp; <a name="anchor-image-wtip-min04733"/>
#### <a name="17-canonical-hybrid-multi-tenant-saas-app-04604"></a>17.Canonical 混合多租户 SaaS 应用，0:46:04
[![Canonical 混合多租户 SaaS 应用][image-wtip-min04733-canonical-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2764)


&nbsp; <a name="anchor-image-wtip-min04810"/>
#### <a name="18-wingtip-saas-sample-app-04801"></a>18.Wingtip SaaS 示例应用，0:48:01
[![Wingtip SaaS 示例应用][image-wtip-min04810-wingtip-saas-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2881)


&nbsp; <a name="anchor-image-wtip-min04910"/>
#### <a name="19-scenarios-and-patterns-explored-in-the-tutorials-04900"></a>19.教程中介绍的方案和模式，0:49:00
[![教程中介绍的方案和模式][image-wtip-min04910-scenarios-tutorials]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2940)


&nbsp; <a name="anchor-image-wtip-min05018"/>
#### <a name="20-demo-of-tutorials-and-github-repository-05012"></a>20.教程和 GitHub 存储库演示，0:50:12
[![教程和 GitHub 存储库演示][image-wtip-min05018-demo-tutorials-github]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3012)


&nbsp; <a name="anchor-image-wtip-min05038"/>
#### <a name="21-github-repo-microsoftwingtipsaas-05032"></a>21.GitHub 存储库 Microsoft/WingtipSaaS，0:50:32
[![GitHub 存储库 Microsoft/WingtipSaaS][image-wtip-min05038-github-wingtipsaas]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3032)


&nbsp; <a name="anchor-image-wtip-min05620"/>
#### <a name="22-exploring-the-patterns-05615"></a>22.浏览模式，0:56:15
[![浏览模式][image-wtip-min05620-exploring-patterns]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3375)


&nbsp; <a name="anchor-image-wtip-min05744"/>
#### <a name="23-provisioning-tenants-and-onboarding-05619"></a>23.预配租户和载入，0:56:19
[![预配租户和载入][image-wtip-min05744-provisioning-tenants-onboarding-1]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3379)


&nbsp; <a name="anchor-image-wtip-min05858"/>
#### <a name="24-provisioning-tenants-and-application-connection-05752"></a>24.预配租户和应用程序连接，0:57:52
[![预配租户和应用程序连接][image-wtip-min05858-provisioning-tenants-app-connection-2]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3472)


&nbsp; <a name="anchor-image-wtip-min05943"/>
#### <a name="25-demo-of-management-scripts-provisioning-a-single-tenant-05936"></a>25.预配单个租户的管理脚本演示，0:59:36
[![预配单个租户的管理脚本演示][image-wtip-min05943-demo-management-scripts-st]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3576)


&nbsp; <a name="anchor-image-wtip-min10002"/>
#### <a name="26-powershell-to-provision-and-catalog-05956"></a>26.PowerShell 预配和编录，0:59:56
[![PowerShell 预配和编录][image-wtip-min10002-powershell-provision-catalog]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3596)


&nbsp; <a name="anchor-image-wtip-min10330"/>
#### <a name="27-t-sql-select--from-tenantsextended-10325"></a>27.T-SQL SELECT * FROM TenantsExtended，1:03:25
[![T-SQL SELECT * FROM TenantsExtended][image-wtip-min10330-sql-select-tenantsextended]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3805)


&nbsp; <a name="anchor-image-wtip-min10436"/>
#### <a name="28-managing-unpredictable-tenant-workloads-10334"></a>28.管理不可预知的租户工作负荷，1:03:34
[![管理不可预知的租户工作负荷][image-wtip-min10436-managing-unpredictable-workloads]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3814)


&nbsp; <a name="anchor-image-wtip-min10639"/>
#### <a name="29-elastic-pool-monitoring-10632"></a>29.弹性池监视，1:06:32
[![弹性池监视][image-wtip-min10639-elastic-pool-monitoring]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3992)


&nbsp; <a name="anchor-image-wtip-min10942"/>
#### <a name="30-load-generation-and-performance-monitoring-10937"></a>30.负载生成和性能监视，1:09:37
[![负载生成和性能监视][image-wtip-min10942-load-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4117)


&nbsp; <a name="anchor-image-wtip-min11033"/>
#### <a name="31-schema-management-at-scale-10940"></a>31.大规模架构管理，1:09:40
[![大规模架构管理][image-wtip-min11033-schema-management-scale]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=34120)


&nbsp; <a name="anchor-image-wtip-min11221"/>
#### <a name="32-distributed-query-across-tenant-databases-11118"></a>32.跨租户数据库的分布式查询，1:11:18
[![跨租户数据库的分布式查询][image-wtip-min11221-distributed-query]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4278)


&nbsp; <a name="anchor-image-wtip-min11232"/>
#### <a name="33-demo-of-ticket-generation-11228"></a>33.票证生成演示，1:12:28
[![票证生成演示][image-wtip-min11232-demo-ticket-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4348)


&nbsp; <a name="anchor-image-wtip-min11246"/>
#### <a name="34-ssms-adhoc-analytics-11235"></a>34.SSMS 临时分析，1:12:35
[![SSMS 临时分析][image-wtip-min11246-ssms-adhoc-analytics]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4355)


&nbsp; <a name="anchor-image-wtip-min11632"/>
#### <a name="35-extract-tenant-data-into-sql-dw-11546"></a>35.将租户数据提取到 SQL DW 中，1:15:46
[![将租户数据提取到 SQL DW 中][image-wtip-min11632-extract-tenant-data-sql-dw]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4546)


&nbsp; <a name="anchor-image-wtip-min11648"/>
#### <a name="36-graph-of-daily-sale-distribution-11638"></a>36.日销售分配图，1:16:38
[![日销售分配图][image-wtip-min11648-graph-daily-sale-distribution]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4598)


&nbsp; <a name="anchor-image-wtip-min11952"/>
#### <a name="37-wrap-up-and-call-to-action-11743"></a>37.总结和行动号召，1:17:43
[![总结和行动号召][image-wtip-min11952-wrap-up-call-action]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4663)


&nbsp; <a name="anchor-image-wtip-min12042"/>
#### <a name="38-resources-for-more-information-12035"></a>38.有关详细信息的资源，1:20:35
[![有关详细信息的资源][image-wtip-min12042-resources-more-info]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4835)

- [博客文章, 5 月22日2017][resource-blog-saas-patterns-app-dev-sql-db-768h]

- *概念：* [多租户 SaaS 数据库租户模式][saas-concept-design-patterns-563e]

- *教程：* [Wingtip 票证 SaaS 应用程序][saas-how-welcome-wingtip-app-679t]

- Wingtip Tickets SaaS 租户应用程序风格的 GitHub 存储库：
    - [适用于-独立应用程序模型的 GitHub][github-wingtip-standaloneapp]存储库。
    - [每个租户的 GitHub][github-wingtip-dbpertenant]存储库模型。
    - [用于-多租户数据库模型的 GitHub][github-wingtip-multitenantdb]存储库。





## <a name="next-steps"></a>后续步骤

- [第一篇教程文章][saas-how-welcome-wingtip-app-679t]




<!-- Image link reference IDs. -->

[image-wtip-min00003-brk3120-whole-welcome]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00003-brk3120-welcome-myob-design-saas-app-sql-db.png "欢迎使用幻灯片"

[image-wtip-min00311-session]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00311-session-objectives-takeaway.png "会话目标。"

[image-wtip-min00417-agenda]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00417-agenda-app-management-models-patterns.png "议程。"

[image-wtip-min00505-web-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00505-wingtip-saas-app-mt-web.png "Wingtip SaaS 应用：多租户 Web 应用"

[image-wtip-min00555-app-web-form]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00555-app-form-contoso-concert-hall-night-opera.png "操作中的应用 Web 窗体"

[image-wtip-min00931-per-tenant-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00931-saas-data-management-concerns.png "每个租户的成本（缩放、隔离、恢复）"

[image-wtip-min01159-db-models-pros-cons]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01159-db-models-multi-tenant-saas-apps.png "多租户数据库模型：优点和缺点"

[image-wtip-min01301-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01301-hybrib-model-blends-benefits-mt-st.png "混合模型混合 MT/ST 的优点"

[image-wtip-min01644-st-vs-mt]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01644-st-mt-pros-cons.png "单租户与多租户：优点和缺点"

[image-wtip-min01936-pools-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01936-pools-cost-effective-unpredictable-workloads.png "池对于不可预知的工作负荷来说是经济高效的"

[image-wtip-min02008-demo-st-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02008-demo-st-hybrid.png "每个租户一个数据库和混合 ST/MT 的演示"

[image-wtip-min02029-live-app-form-dojo]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02029-app-form-dogwwod-dojo.png "显示 Dojo 的实时应用窗体"

[image-wtip-min02854-myob-no-dba]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02854-myob-no-dba.png "看到的是 MYOB 而不是 DBA"

[image-wtip-min02940-myob-elastic]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02940-myob-elastic-pool-usage-example.png "MYOB 弹性池使用情况示例"

[image-wtip-min03136-learning-isvs]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min03136-myob-isv-saas-patterns-design-scale.png "借鉴 MYOB 和其他 ISV"

[image-wtip-min04315-patterns-compose]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04315-patterns-compose-into-e2e-saas-scenario-st-mt.png "模式组合到 E2E SaaS 方案中"

[image-wtip-min04733-canonical-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04733-canonical-hybrid-mt-saas-app.png "Canonical 混合多租户 SaaS 应用"

[image-wtip-min04810-wingtip-saas-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04810-saas-sample-app-descr-of-modules-links.png "Wingtip SaaS 示例应用"

[image-wtip-min04910-scenarios-tutorials]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04910-scenarios-patterns-explored-tutorials.png "教程中介绍的方案和模式"

[image-wtip-min05018-demo-tutorials-github]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05018-demo-saas-tutorials-github-repo.png "教程和 GitHub 存储库演示"

[image-wtip-min05038-github-wingtipsaas]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05038-github-repo-wingtipsaas.png "GitHub 存储库 Microsoft/WingtipSaaS"

[image-wtip-min05620-exploring-patterns]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05620-exploring-patterns-tutorials.png "浏览模式"

[image-wtip-min05744-provisioning-tenants-onboarding-1]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05744-provisioning-tenants-connecting-run-time-1.png "预配租户和载入"

[image-wtip-min05858-provisioning-tenants-app-connection-2]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05858-provisioning-tenants-connecting-run-time-2.png "预配租户和应用程序连接"

[image-wtip-min05943-demo-management-scripts-st]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05943-demo-management-scripts-provisioning-st.png "预配单个租户的管理脚本演示"

[image-wtip-min10002-powershell-provision-catalog]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10002-powershell-code.png "PowerShell 预配和编录"

[image-wtip-min10330-sql-select-tenantsextended]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10330-ssms-tenantcatalog.png "T-SQL SELECT * FROM TenantsExtended"

[image-wtip-min10436-managing-unpredictable-workloads]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10436-managing-unpredictable-tenant-workloads.png "管理不可预知的租户工作负荷"

[image-wtip-min10639-elastic-pool-monitoring]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10639-elastic-pool-monitoring.png "弹性池监视"

[image-wtip-min10942-load-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10942-schema-management-scale.png "负载生成和性能监视"

[image-wtip-min11033-schema-management-scale]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11033-schema-manage-1000s-dbs-one.png "大规模架构管理"

[image-wtip-min11221-distributed-query]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11221-distributed-query-all-tenants-asif-single-db.png "跨租户数据库的分布式查询"

[image-wtip-min11232-demo-ticket-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11232-demo-ticket-generation-distributed-query.png "票证生成演示"

[image-wtip-min11246-ssms-adhoc-analytics]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11246-tsql-adhoc-analystics-db-elastic-query.png "SSMS 临时分析"

[image-wtip-min11632-extract-tenant-data-sql-dw]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11632-extract-tenant-data-analytics-db-dw.png "将租户数据提取到 SQL DW 中"

[image-wtip-min11648-graph-daily-sale-distribution]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11648-graph-daily-sale-contoso-concert-hall.png "日销售分配图"

[image-wtip-min11952-wrap-up-call-action]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11952-wrap-call-action-saasfeedback.png "总结和行动号召"

[image-wtip-min12042-resources-more-info]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min12042-resources-blog-github-tutorials-get-started.png "有关详细信息的资源"




<!-- Article link reference IDs. -->

[saas-concept-design-patterns-563e]: saas-tenancy-app-design-patterns.md

[saas-how-welcome-wingtip-app-679t]: saas-tenancy-welcome-wingtip-tickets-app.md


[video-on-youtube-com-478y]: https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1

[video-on-channel9-479c]: https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK3120


[resource-blog-saas-patterns-app-dev-sql-db-768h]: https://azure.microsoft.com/blog/saas-patterns-accelerate-saas-application-development-on-sql-database/


[github-wingtip-standaloneapp]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp/

[github-wingtip-dbpertenant]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/

[github-wingtip-multitenantdb]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/

