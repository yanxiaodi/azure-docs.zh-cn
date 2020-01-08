---
title: （已弃用）通过 Sysdig 监视 Azure 容器服务群集
description: 通过 Sysdig 监视 Azure 容器服务群集。
services: container-service
author: sauryadas
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 08/08/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 39cd5a3fe7db0c442608e7943e7cbd6e3198edfb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60509486"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-sysdig"></a>（已弃用）通过 Sysdig 监视 Azure 容器服务群集

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

在本文中，我们将 Sysdig 代理部署到 Azure 容器服务群集中的所有代理节点。 此配置需要具有 Sysdig 的帐户。 

## <a name="prerequisites"></a>必备组件
[部署](container-service-deployment.md)和[连接](../container-service-connect.md)由 Azure 容器服务配置的群集。 探究 [Marathon UI](container-service-mesos-marathon-ui.md)。 转到 [https://app.sysdigcloud.com](https://app.sysdigcloud.com) 设置 Sysdig 云帐户。 

## <a name="sysdig"></a>Sysdig
Sysdig 是允许监视群集中容器的监视服务。 大家都知道 Sysdig 有助于进行故障排除，但它也具有基本的 CPU、网络、内存和 I/O 监控指标。 Sysdig 便于查看哪些容器的工作最为繁重、哪些容器实质上占用的内存和 CPU 最多。 此视图位于“概述”部分中，当前处于测试阶段。 

![Sysdig UI](./media/container-service-monitoring-sysdig/sysdig6.png) 

## <a name="configure-a-sysdig-deployment-with-marathon"></a>通过 Marathon 配置 Sysdig 部署
这些步骤将演示如何通过 Marathon 将 Sysdig 应用程序配置和部署到群集中。 

通过 [http://localhost:80/](http://localhost:80/) 访问 DC/OS UI，然后从此处导航到左下角的“Universe”，再搜索“Sysdig”。

![DC/OS Universe 中的 Sysdig](./media/container-service-monitoring-sysdig/sysdig1.png)

现在，要完成该配置，需要一个 Sysdig 云帐户或免费试用帐户。 登录到 Sysdig 云网站后，请单击用户名，在该页上你应看到自己的“访问密钥”。 

![Sysdig API 密钥](./media/container-service-monitoring-sysdig/sysdig2.png) 

接下来将访问密钥输入到 DC/OS Universe 内的 Sysdig 配置中。 

![DC/OS Universe 中的 Sysdig 配置](./media/container-service-monitoring-sysdig/sysdig3.png)

现在将实例设置为 10000000，这样每当在群集中添加新节点时，Sysdig 都会将代理部署到这个新节点。 这是一个临时解决方案，确保 Sysdig 会将所有新代理都部署到群集内。 

![DC/OS Universe 实例中的 Sysdig 配置](./media/container-service-monitoring-sysdig/sysdig4.png)

安装该程序包后，请导航回 Sysdig UI，然后便可以浏览容器在群集中的不同使用情况指标。 

还可以通过[新仪表板向导](https://app.sysdigcloud.com/#/dashboards/new)安装 Mesos 和 Marathon 特定的仪表板。
