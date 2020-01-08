---
title: Microsoft Azure Data Box Edge 概述 | Microsoft Docs
description: 介绍 Azure Data Box Edge，这是一种使用物理设备进行 Azure 网络传输的存储解决方案。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 07/17/2019
ms.author: alkohli
ms.openlocfilehash: 69580f956b603423ef302353953a45ad5d00391e
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305423"
---
# <a name="what-is-azure-data-box-edge"></a>什么是 Azure Data Box Edge？ 

Azure Data Box Edge 是具有网络数据传输功能的支持 AI 的边缘计算设备。 本文概述 Data Box Edge 解决方案、其优势、重要功能以及可以部署此设备的场合。 

Data Box Edge 是一种硬件即服务解决方案。 Microsoft 为客户提供了一个具有内置现场可编程门阵列 (FPGA) 的云托管设备，该阵列可加速 AI 推理并具有存储网关的所有功能。 

## <a name="use-cases"></a>用例

下面介绍各种方案，其中 Data Box Edge 可用于加快边缘的机器学习 (ML) 推理并对数据进行预处理，然后再将该数据发送到 Azure。

- **使用 Azure 机器学习进行推理** - 借助 Data Box Edge，可以运行 ML 模型以获得可在数据发送到云之前执行的快速结果。 可以选择传输完整的数据集以继续重新训练并改进 ML 模型。 有关如何在 Data Box Edge 设备上使用 Azure ML 硬件加速模型的详细信息，请参阅[在 Data Box Edge 上部署 Azure ML 硬件加速模型](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-fpga-web-service#deploy-to-a-local-edge-server)。

- **预处理数据** - 转换数据，然后将数据发送到 Azure，以创建更具操作的数据集。 使用预处理可以： 

    - 聚合数据。
    - 修改数据，例如删除个人数据。
    - 用于优化存储和带宽，或用于进一步分析的子集数据。
    - 分析和应对 IoT 事件。 

- **通过网络将数据传输到 Azure** - 使用 Data Box Edge 可以快速轻松地将数据传输到 Azure，以实现其他计算和分析或存档目的。 


## <a name="key-capabilities"></a>关键功能

Data Box Edge 具有以下功能：

|功能 |说明  |
|---------|---------|
|加速 AI 推断| 通过内置 FPGA 实现。|
|计算       |允许分析、处理、筛选数据。|
|高性能 | 高性能计算和数据传输。|
|数据访问     | 使用云 API 从 Azure 存储 Blob 和 Azure 文件中直接访问数据，以便在云中进行其他数据处理。 设备带有本地缓存，以便快速访问最近使用的文件。|
|云托管     |设备和服务通过 Azure 门户进行管理。  |
|离线上传     | 离线模式支持离线上传方案。|
|支持的协议     | 支持用于数据引入的标准 SMB 和 NFS 协议。 <br> 有关支持的版本的详细信息，请转到 [Data Box Edge 系统要求](data-box-edge-system-requirements.md)。|
|数据刷新     | 可以使用云中的最新内容刷新本地文件。|
|加密    | BitLocker 支持本地加密数据，并通过 *http* 安全地将数据传输到云中。|
|宽带限制| 中止以限制在高峰时段使用带宽。|


## <a name="components"></a>组件

Data Box Edge 解决方案包括 Data Box Edge 资源、Data Box Edge 物理设备和本地 Web UI。

* **Data Box Edge 物理设备** - 可将 Microsoft 提供的 1U 机架安装式服务器配置为向 Azure 发送数据。 
    
* **Data Box Edge 资源** - Azure 门户中的一个资源，使用该资源可以通过 Web 界面（可从不同的地理位置访问该界面）管理 Data Box Edge 设备。 使用 Data Box Edge 资源可以创建和管理资源、查看和管理设备与警报，以及管理共享。  

    <!--![The Data Box Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

    有关详细信息，请转到[为 Data Box Edge 设备创建订单](data-box-edge-deploy-prep.md#create-a-new-resource)。

* **Data Box 本地 Web UI** - 使用本地 Web UI 可以运行诊断、关闭和重启 Data Box Edge 设备、查看复制日志，并联系 Microsoft 支持部门来提出服务请求。

    <!--![The Data Box Edge local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    有关使用基于 Web 的 UI 的详细信息，请转到[使用基于 Web 的 UI 管理 Data Box](data-box-edge-manage-access-power-connectivity-mode.md)。


## <a name="region-availability"></a>上市区域

将数据传输到的 Data Box Edge 物理设备、Azure 资源和目标存储帐户不一定非要位于同一区域。

- **资源可用性** - 有关 Data Box Edge 资源可用的所有区域的列表，请转到[可用的 Azure 产品(按区域)](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)。 Data Box Edge 也可以部署在 Azure 政府云中。 有关详细信息，请参阅[什么是 Azure 政府？](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)。
    
- **目标存储帐户** - 存储数据的存储帐户可在所有 Azure 区域中获得。 存储帐户存储 Data Box Edge 数据的区域应靠近设备所在位置，以便获得最佳性能。 远离设备的存储帐户会导致长时间的延迟和性能下降。 


## <a name="next-steps"></a>后续步骤

- 查看 [Data Box Edge 系统要求](data-box-edge-system-requirements.md)。
- 了解 [Data Box Edge 限制](data-box-edge-limits.md)。
- 在 Azure 门户中部署 [Azure Data Box Edge](data-box-edge-deploy-prep.md)。




