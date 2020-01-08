---
title: （已弃用）Azure 容器服务的 DC/OS 代理池
description: 公共和专用代理池如何使用 Azure 容器服务的 DC/OS 群集
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 01/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 03cacda1aa405cb2d0ded579c8ddb5f6011ce3bb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60478440"
---
# <a name="deprecated-dcos-agent-pools-for-azure-container-service"></a>（已弃用）Azure 容器服务的 DC/OS 代理池

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Azure 容器服务中的 DC/OS 群集包含两个池（公共池和专用池）中的代理节点。 可将应用程序部署到任一个池，从而影响容器服务中计算机之间的可访问性。 计算机可以向 Internet 公开（公共）或保持在内部（专用）。 本文简要概述了使用公共池和专用池的原因。


* **专用代理**：专用代理节点通过不可路由网络运行。 只有从管理区域或通过公共区域边缘路由器才可访问此网络。 默认情况下，DC/OS 在专用代理节点启动应用。 

* **公共代理**：公共代理节点通过可公共访问的网络运行 DC/OS 应用和服务。 

有关 DC/OS 网络安全性的详细信息，请参阅 [DC/OS 文档](https://docs.mesosphere.com/)。

## <a name="deploy-agent-pools"></a>部署代理池

按如下方式创建 Azure 容器服务中的 DC/OS 代理池：

* **专用池**包含一定数量的代理节点，此数量在[部署 DC/OS 群集](container-service-deployment.md)时指定。 

* **公共池**最初包含预先设定的一定数量的代理节点。 预配 DC/OS 群集时自动添加此池。

专用池和公共池均为 Azure 虚拟机规模集。 可以在部署后调整这些池的大小。

## <a name="use-agent-pools"></a>使用代理池
默认情况下，**Marathon** 将所有新的应用程序部署到“专用”  代理节点。 必须在应用程序创建过程中，将应用程序显式部署到“公共”  节点。 选择“可选”  选项卡，并输入 **slave_public**作为“已接受的资源角色”  值。 此过程记录在[此处](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container)和 [DC/OS](https://docs.mesosphere.com/1.7/administration/installing/oss/custom/create-public-agent/) 文档中。

## <a name="next-steps"></a>后续步骤
* 阅读有关[管理 DC/OS 容器](container-service-mesos-marathon-ui.md)的详细信息。

* 了解如何[打开由 Azure 提供的防火墙](container-service-enable-public-access.md)，允许对 DC/OS 容器进行公共访问。

