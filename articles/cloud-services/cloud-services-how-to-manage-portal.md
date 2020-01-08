---
title: 常见的云服务管理任务 | Microsoft Docs
description: 了解如何在 Azure 门户中管理云服务。 这些示例使用 Azure 门户。
services: cloud-services
documentationcenter: ''
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 07/05/2017
ms.author: gwallace
ms.openlocfilehash: 8ec7784fb51d0fa4de2563f76444b0b5e5f34902
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359598"
---
# <a name="manage-cloud-services-in-the-azure-portal"></a>在 Azure 门户中管理云服务
在 Azure 门户的“云服务”区域中，可以：

* 更新服务角色或部署。
* 将预留部署升级到生产环境。
* 将资源链接到云服务，以便可以查看资源依赖关系和对资源进行整体缩放。
* 删除云服务或部署。

有关如何缩放云服务的详细信息，请参阅[如何在门户中为云服务配置自动缩放](cloud-services-how-to-scale-portal.md)。

## <a name="update-a-cloud-service-role-or-deployment"></a>更新云服务角色或部署
如果需要更新云服务的应用程序代码，请使用云服务边栏选项卡上的“更新”。 可以更新一个角色或所有角色。 若要进行更新，可以上传新服务包或服务配置文件。

1. 在 [Azure 门户][Azure portal]中，选择要更新的云服务。 此步骤将打开云服务实例边栏选项卡。

2. 在边栏选项卡上，选择“更新”。

    ![“更新”按钮](./media/cloud-services-how-to-manage-portal/update-button.png)

3. 使用新服务包文件 (.cspkg) 和服务配置文件 (.cscfg) 更新部署。

    ![更新部署](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. （可选）更新存储帐户和部署标签。

5. 如果任何角色只有一个角色实例，请选择“即使一个或多个角色包含单个实例也进行部署”复选框，使升级能够继续进行。

    如果每个角色至少具有两个角色实例（虚拟机），那么 Azure 在云服务更新期间只能保证 99.95% 的服务可用性。 因为有两个角色实例，其中一个虚拟机可以在另一个虚拟机更新时处理客户端请求。

6. 选中“开始部署”复选框，以便在上传包完成后应用更新。

7. 选择“确定”开始更新服务。

## <a name="swap-deployments-to-promote-a-staged-deployment-to-production"></a>交换部署以将预留部署升级到生产环境
如果决定部署云服务的新版本，可以在云服务过渡环境中暂存和测试新版本。 使用“交换”将用于寻址这两个部署的 URL 地址互换，将新版本提升为生产。

可以通过“云服务”  页面或仪表板交换部署。

1. 在 [Azure 门户][Azure portal]中，选择要更新的云服务。 此步骤将打开云服务实例边栏选项卡。

2. 在边栏选项卡上，选择“交换”。

    ![云服务“交换”按钮](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. 将打开以下确认提示：

    ![云服务交换](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. 验证部署信息后，选择“确定”交换部署。

    交换部署的速度很快，因为唯一发生更改的是部署所用的虚拟 IP 地址 (VIP)。

    为节省计算成本，可以在验证生产部署能够按预期运行后删除过渡部署。

### <a name="common-questions-about-swapping-deployments"></a>有关交换部署的常见问题

**交换部署的先决条件是什么？**

成功的部署交换有两个先决条件：

- 如果想要将静态 IP 地址用于生产槽，还必须为过渡槽保留一个静态 IP 地址。 否则，交换失败。

- 执行交换前，角色的所有实例必须都在运行。 可在 Azure 门户的“概述”边栏选项卡中检查实例的状态。 或者，可以使用 Windows PowerShell 中的 [Get-azurerole](/powershell/module/servicemanagement/azure/get-azurerole?view=azuresmps-3.7.0) 命令。

请注意，来宾 OS 更新和服务修复操作也会导致部署交换失败。 有关详细信息，请参阅[排查云服务部署问题](cloud-services-troubleshoot-deployment-problems.md)。

**应用程序的交换是否会导致停机？应如何处理它？**

如上一部分中所述，部署交换通常非常快，因为它只更改 Azure 负载均衡器中的配置。 在某些情况下，它会需要 10 秒或更长时间并导致暂时性连接故障。 若要减少对客户的影响，请考虑实施[客户端重试逻辑](../best-practices-retry-general.md)。

## <a name="delete-deployments-and-a-cloud-service"></a>删除部署和云服务
必须先删除每个现有部署，才能删除云服务。

为节省计算成本，可以在验证生产部署能够按预期运行后删除过渡部署。 停止的已部署角色实例仍会产生计算成本。

可使用以下过程删除部署或云服务。

1. 在 [Azure 门户][Azure portal]中，选择要删除的云服务。 此步骤将打开云服务实例边栏选项卡。

2. 在边栏选项卡上，选择“删除”。

    ![云服务“删除”按钮](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. 若要删除整个云服务，请选中“云服务及其部署”复选框。 或者，可以选中“生产部署”或“过渡部署”复选框。

    ![云服务删除](./media/cloud-services-how-to-manage-portal/delete-blade.png)

4. 选择底部的“删除”。

5. 若要删除云服务，请选择“删除云服务”。 然后在出现确认提示时选择“是”。

> [!NOTE]
> 删除云服务时，如果已配置详细监视，则必须从存储帐户中手动删除数据。 有关在何处查找指标表的信息，请参阅[云服务监视简介](cloud-services-how-to-monitor.md)。


## <a name="find-more-information-about-failed-deployments"></a>查找有关部署失败的详细信息
“概述”边栏选项卡顶部有一个状态栏。 选择状态栏时，会打开新的边栏选项卡并显示错误信息。 如果部署没有任何错误，则信息边栏选项卡为空白。

![云服务概述](./media/cloud-services-how-to-manage-portal/status-info.png)



[Azure portal]: https://portal.azure.com

## <a name="next-steps"></a>后续步骤
* [云服务的常规配置](cloud-services-how-to-configure-portal.md)。
* 了解如何[部署云服务](cloud-services-how-to-create-deploy-portal.md)。
* 配置[自定义域名](cloud-services-custom-domain-name-portal.md)。
* 配置 [SSL 证书](cloud-services-configure-ssl-certificate-portal.md)。
