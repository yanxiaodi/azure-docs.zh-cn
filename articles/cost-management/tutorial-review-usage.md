---
title: 教程：在 Azure 中使用 Cloudyn 查看使用情况和成本 | Microsoft Docs
description: 在本教程中，请查看使用情况和成本以跟踪趋势、检测低效情况并创建警报。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: tutorial
ms.service: cost-management
ms.custom: seodec18
manager: benshy
ms.openlocfilehash: 2b701e618bdcbd50709360c55cfb9ceb68781ebc
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2019
ms.locfileid: "65969096"
---
<!-- Intent: As a cloud-consuming user, I need to view usage and costs for my cloud resources and services.
-->

# <a name="tutorial-review-usage-and-costs"></a>教程：查看使用情况和成本

Cloudyn 显示使用情况和成本，以便能够跟踪趋势、检测低效使用情况和创建警报。 所有使用情况和成本数据都将显示在 Cloudyn 仪表板和报表中。 本教程中将举例说明如何使用仪表板和报表查看使用情况和成本。

Azure 成本管理提供了与 Cloudyn 类似的功能。 Azure 成本管理是一个本机 Azure 成本管理解决方案。 借助它，可以分析成本、创建和管理预算、导出数据，并能查看和采纳优化建议，从而节省资金。 有关详细信息，请参阅 [Azure 成本管理](overview-cost-mgt.md)。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 跟踪使用情况和成本趋势
> * 检测低效使用情况
> * 针对异常支出或超支情况创建警报
> * 导出数据

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

- 必须具有 Azure 帐户。
- 必须有 Cloudyn 的试用注册版或付费订阅版。

## <a name="open-the-cloudyn-portal"></a>打开 Cloudyn 门户

可以在 Cloudyn 门户中查看所有使用情况和成本。 通过 Azure 门户打开 Cloudyn 门户，或导航到 https://azure.cloudyn.com 并进行登录。

## <a name="track-usage-and-cost-trends"></a>跟踪使用情况和成本趋势

通过“随着时间推移”报表跟踪使用情况和成本的实际开支来确定趋势。 若要开始查看趋势，请使用“实际成本随着时间推移”报表。 在门户的左上方，单击“成本”   > “成本分析”   > “时段实际成本”  。 首次打开报表时，不会对其应用组或筛选器。

下面是一个示例报表：

![时段实际成本报表示例](./media/tutorial-review-usage/actual-cost01.png)

此报表显示过去 30 天的所有支出。 若要仅查看 Azure 服务的支出，请应用“服务”组，然后针对所有 Azure 服务进行筛选。 下图显示了筛选出的服务。

![显示筛选出的 Azure 服务的示例](./media/tutorial-review-usage/actual-cost02.png)

在前面的示例中，从 2018 年 10 月 29 日开始，支出较之前的要少。 但是，列数过多可能会使趋势不明显。 可以将报表视图更改为折线图或面积图，以查看在其他视图中显示的数据。 下图更清楚地显示了这一趋势。

![显示 Azure VM 成本趋势递减的示例](./media/tutorial-review-usage/actual-cost03.png)

继续此示例，可以看到 Azure VM 的成本下降。 当天其他 Azure 服务的成本也开始下降。 那么，是什么导致了支出缩减呢？ 在此示例中，已经完成了一个大型工作项目，因此对许多 Azure 服务的使用也下降。

若要观看有关如何跟踪使用情况和成本趋势的教程视频，请观看[使用 Cloudyn 分析云计费数据与时间](https://youtu.be/7LsVPHglM0g)。

## <a name="detect-usage-inefficiencies"></a>检测低效使用情况

优化器报表可提高效率、优化使用情况以及找到在云资源上节省开支的方式。 对于旨在帮助减少空闲或昂贵 VM 方面，它们特别有助于给出经济高效的大小调整建议。

当组织最初将资源移动到云中时，影响组织的常见问题是其虚拟化策略。 他们通常使用类似于用于本地虚拟化环境创建虚拟机的方法。 而且，他们认为通过将本地 VM 移动到云就可降低成本。 但是，这种方法不可能降低成本。

问题在于他们已为现有的基础结构支付了费用。 如果用户愿意，他们可以创建很大的 VM，并保持它们空运行或不运行而得不到有效利用。 将很大的或空闲的 VM 移到云中可能会增加  成本。 与云服务提供商达成协议时，各资源的成本分配很重要。 无论你充分使用资源还是不使用，都必须为你获得的资源支付费用。

“经济高效的大小调整建议”报表通过将 VM 实例类型容量与其 CPU 和内存使用情况的历史数据相比较，确定出每年可能节省的费用。  

在门户顶部的菜单中，单击“优化器”   > “大小优化”   > “经济高效的大小调整建议”  。 如果有用，请应用筛选器来减少结果。 下面是一个示例图像：

![Azure VM 的经济高效的大小调整建议报告](./media/tutorial-review-usage/sizing01.png)

在此示例中，按照建议更改 VM 实例类型可以节省 2,382 美元。 单击  “详细信息”下的加号 (+) 获取第一个建议。 以下是有关第一个建议的详细信息。

![显示建议详细信息的示例](./media/tutorial-review-usage/sizing02.png)

单击“候选项列表”  旁的加号可查看 VM 实例 ID。

![显示要调整大小的 VM 候选项列表的示例](./media/tutorial-review-usage/sizing03.png)

若要观看有关检测低效使用情况的教程视频，请观看[在 Cloudyn 中优化 VM 大小](https://youtu.be/1xaZBNmV704)。

Azure 成本管理也为 Azure 服务提供成本节省建议 有关详细信息，请参阅[教程：根据建议优化成本](tutorial-acm-opt-recommendations.md)。

## <a name="create-alerts-for-unusual-spending"></a>针对异常支出创建警报

出现支出异常和超支风险时，可以通过警报自动通知利益相关者。 可以使用支持基于预算和成本阈值的警报的报表创建警报。

当你在 Azure VM 上的支出接近总预算时，此示例使用“一段时间的实际成本”报表来发送通知。  在此场景中，你的总预算为 20,000 美元，你希望在成本接近预算的一半（9,000 美元）时收到通知，在成本为 10,000 美元时再次收到警报。

1. 在 Cloudyn 门户顶部的菜单中，选择“成本” > “成本分析” > “时段实际成本”    。
2. 将“组”  设置为“服务”  ，并将“按服务筛选”  设置为“Azure/VM”  。
3. 在报表的右上角，选择“操作”，然后选择“计划报表”   。
4. 若要按计划的时间间隔向自己发送有关报表的电子邮件，请在“保存或计划此报表”对话框中选择“计划”选项卡。   请务必选择“通过电子邮件发送”  。 你使用的任何标记、分组和筛选均包含在通过电子邮件发送的报表中。
5. 选择“阈值”选项卡，然后选择“实际成本与阈值”   。
   1. 在“红色警报”阈值框中，输入 10000。 
   2. 在“黄色警报”阈值框中，输入 9000。 
   3. 在“连续警报数”框中，输入要接收的连续警报的数目。  当你收到的警报总数达到指定数目时，就不会再发送任何其他警报。
6. 选择“保存”。 

![基于支出阈值显示红色和黄色警报的示例](./media/tutorial-review-usage/schedule-alert01.png)

还可以选择“成本百分比与预算”阈值指标来创建警报。  这样就可以将阈值指定为预算的百分比而不是货币值。

## <a name="export-data"></a>导出数据

可以像创建报告警告一样，从任何报告导出数据。 例如，你可能需要导出 Cloudyn 帐户或其他用户数据的列表。 若要导出任何报告，请打开该报告，然后在报告的顶部单击“操作”  。 你可能想要执行的某些操作可能包括“导出所有报告数据”，以便可以下载或打印信息  。 或者，可以选择“计划报告”，以便将报告计划为以电子邮件的形式发送  。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 跟踪使用情况和成本趋势
> * 检测低效使用情况
> * 针对异常支出或超支情况创建警报
> * 导出数据


进入下一个教程，了解如何使用历史数据预测支出。

> [!div class="nextstepaction"]
> [预测将来的支出](tutorial-forecast-spending.md)
