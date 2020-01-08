---
title: 设备组的自动部署 - Azure IoT Edge | Microsoft Docs
description: 使用 Azure IoT Edge 中的自动部署来管理基于共享标记的设备组
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 09/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 376ee74732daf526b31129fa8c93cbaa32350eae
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60318200"
---
# <a name="understand-iot-edge-automatic-deployments-for-single-devices-or-at-scale"></a>了解单设备或大规模的 IoT Edge 自动部署

Azure IoT Edge 设备遵循的[设备生命周期](../iot-hub/iot-hub-device-management-overview.md)类似于其他类型的 IoT 设备：

1. 预配新的 IoT Edge 设备，这包括使用 OS 创建设备映像以及安装 [IoT Edge 运行时](iot-edge-runtime.md)。
2. 配置设备来运行 [IoT Edge 模块](iot-edge-modules.md)，然后监视其运行状况。 
3. 最后，如果设备需要更换或变得过时，则停用设备。  

Azure IoT Edge 提供两种方法来配置在 IoT Edge 设备上运行的模块：一种方法用于在单个设备上进行开发和快速迭代（在 Azure IoT Edge [教程](tutorial-deploy-function.md)中使用此方法），另一种方法用于管理大群 IoT Edge 设备。 可以通过 Azure 门户和编程方式使用这两种方法。 若是针对组或大量设备，可以使用设备孪生中的[标记](../iot-edge/how-to-deploy-monitor.md#identify-devices-using-tags)指定要将模块部署到哪些设备。 以下步骤步骤讨论如何部署到通过标记属性标识的华盛顿州设备组。 

本文重点介绍设备群的配置和监视阶段，统称为 IoT Edge 自动部署。 整个部署步骤如下所示： 

1. 由操作员来定义部署，描述一组模块和目标设备。 每个部署都有一个反映此信息的部署清单。 
2. IoT 中心服务与所有目标设备通信，为其配置所需模块。 
3. IoT 中心服务从 IoT Edge 设备检索状态，然后将这些状态提供给操作员。  例如，如果某个 Edge 设备配置不成功，或者某个模块在运行时发生故障，操作员就会看到。 
4. 随时对新的符合目标条件的 IoT Edge 设备进行部署配置。 例如，如果某个部署的目标是华盛顿州的所有 IoT Edge 设备，则当某个新的 IoT Edge 设备完成预配并添加到华盛顿州设备组时，该部署会自动配置此设备。 
 
本文将介绍配置和监视部署过程中涉及的每个组件。 如需创建和更新部署的详细介绍，请参阅[大规模部署和监视 IoT Edge 模块](how-to-deploy-monitor.md)。

## <a name="deployment"></a>部署

loT Edge 自动部署会分配 IoT Edge 模块映像，这些映像在一组 IoT Edge 目标设备上作为实例运行。 操作方式是：配置一个 IoT Edge 部署清单，其中包括一系列模块和相应的初始化参数。 部署可以分配给单个设备（根据设备 ID 分配），也可以分配给一组设备（根据标记）。 IoT Edge 设备在收到部署清单以后，就会从各个容器存储库下载并安装容器映像，并对其进行相应的配置。 创建部署以后，操作员可以监视部署状态，看目标设备是否已正确配置。

只能通过部署配置 IoT Edge 设备。 设备在接收部署之前，必须具备以下先决条件：

* 基础操作系统
* 容器管理系统，如 Moby 或 Docker
* 预配 IoT Edge 运行时 

### <a name="deployment-manifest"></a>部署清单

部署清单是一个 JSON 文档，用于描述要在目标 IoT Edge 设备上配置的模块。 它包含所有模块的配置元数据，其中包括必需的系统模块（具体说来，就是 IoT Edge 代理和 IoT Edge 中心）。  

每个模块的配置元数据包括： 

* Version 
* Type 
* 状态（例如，正在运行或已停止） 
* 重启策略 
* 映像和容器注册表
* 数据输入和输出的路由 

如果模块映像存储在专用容器注册表中，则 IoT Edge 代理会保留注册表凭据。 

### <a name="target-condition"></a>目标条件

在部署的整个生存期内会持续对目标条件进行评估。 将包括满足要求的任何新设备，并删除不再满足要求的任何现有设备。 如果服务检测到任何目标条件更改，则会重新激活部署。 

例如，部署 A 具有目标条件 tags.environment = 'prod'。 启动该部署时，共有 10 个生产设备。 这 10 个设备都成功安装了模块。 IoT Edge 代理状态显示为总共 10 个设备，10 个成功响应，0 个失败响应，以及 0 个挂起响应。 现在，又添加 5 个 tags.environment = 'prod' 的设备。 服务检测到更改，当它尝试部署到这 5 个新设备时，IoT Edge 代理状态变为总共 15 个设备，10 个成功响应，0 个失败响应，以及 5 个挂起响应。

使用设备孪生标记或 deviceId 上的任意布尔条件来选择目标设备。 如果想将条件与标记结合使用，则需在设备孪生中与属性相同的级别下添加 "tags":{} 节。 [深入了解设备孪生中的标记](../iot-hub/iot-hub-devguide-device-twins.md)

目标条件示例：

* deviceId ='linuxprod1'
* tags.environment ='prod'
* tags.environment = 'prod' AND tags.location = 'westus'
* tags.environment = 'prod' OR tags.location = 'westus'
* tags.operator = 'John' AND tags.environment = 'prod' NOT deviceId = 'linuxprod1'

下面是构造目标条件时的一些限制：

* 在设备孪生中，只能使用标记或 deviceId 生成目标条件。
* 目标条件的任何部分都不允许用双引号引起来。 请使用单引号。
* 单引号表示目标条件的值。 因此，如果某个单引号是设备名称的一部分，则必须使用另一个单引号对其转义。 若要以名为 `operator'sDevice` 的设备为目标，请编写 `deviceId='operator''sDevice'`。
* 目标条件值中允许使用数字、字母和以下字符：`-:.+%_#*?!(),=@;$`。

### <a name="priority"></a>优先度

优先级定义相对于其他部署，是否更应将某个部署应用到目标设备。 部署优先级是一个正整数，数字越大表示优先级越高。 如果多个部署均以某个 IoT Edge 设备为目标，则应用优先级最高的部署。  不会应用或合并优先级较低的部署。  如果两个或两个以上优先级相同的部署以某个设备为目标，则应用最近创建的部署（取决于创建时间戳）。

### <a name="labels"></a>标签 

标签是字符串键值对，可以用于部署的筛选和分组。 一个部署可能有多个标签。 标签是可选的，不影响 IoT Edge 设备的实际配置。 

### <a name="deployment-status"></a>部署状态

可以监视任何目标 IoT Edge 设备的部署，确定其是否成功应用。  目标 Edge 设备会在下述一个或多个状态类别中显示： 

* **目标**：显示与部署目标条件匹配的 IoT Edge 设备。
* **实际**：显示的目标 IoT Edge 设备尚未成为另一优先级更高的部署的目标。
* **正常**：显示的 IoT Edge 设备已回头向服务报告模块已成功部署。 
* **不正常**：显示的 IoT Edge 设备已回头向服务报告一个或多个模块未成功部署。 若要进一步调查此错误，请通过远程方式连接到这些设备并查看日志文件。
* **未知**：显示的 IoT Edge 设备未报告与此部署相关的任何状态。 若要进一步进行调查，请查看服务信息和日志文件。

## <a name="phased-rollout"></a>分阶段推出 

分阶段推出是指操作员将更改逐渐部署到更大范围内的 IoT Edge 设备这一整个过程。 这样做的目的是逐渐进行更改，降低进行大规模重大更改的风险。  

分阶段推出按以下阶段和步骤执行： 

1. 建立一个 IoT Edge 设备的测试环境，方法是对设备进行预配，并设置类似 `tag.environment='test'` 的设备孪生标记。 该测试环境应镜像最终会成为部署目标的生产环境。 
2. 创建包含所需模块和配置的部署。 目标条件应针对测试型 IoT Edge 设备环境。   
3. 在测试环境中验证新的模块配置。
4. 更新部署，使之包括部分生产型 IoT Edge 设备，方法是向目标条件添加新标记。 另请确保部署的优先级高于其他目前以这些设备为目标的部署。 
5. 通过查看部署状态，验证部署是否已在目标 IoT 设备上成功完成。
6. 更新部署，使之以所有剩余的生产型 IoT Edge 设备为目标。

## <a name="rollback"></a>回退

在出现错误或配置不当的情况下，可以回退部署。  由于部署为 IoT Edge 设备定义绝对的模块配置，因此即使目的是删除所有模块，也必须有另一优先级较低的部署以该设备为目标。  

请按以下顺序执行回退： 

1. 确认另一部署也以同一设备集为目标。 如果回退的目的是删除所有模块，则再次部署时不应包括任何模块。 
2. 修改或删除要回退的部署的目标条件表达式，使设备不再符合目标条件。
3. 通过查看部署状态，验证回退是否成功。
   * 回退的部署不应再显示已回退设备的状态。
   * 现在，再次进行的部署应包含已回退设备的部署状态。


## <a name="next-steps"></a>后续步骤

* 阅读[大规模地部署和监视 IoT Edge 模块](how-to-deploy-monitor.md)，详细了解创建、更新或删除部署的步骤。
* 详细了解其他 IoT Edge 概念，例如 [IoT Edge 运行时](iot-edge-runtime.md)和 [IoT Edge 模块](iot-edge-modules.md)。

