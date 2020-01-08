---
title: 在门户中针对 Azure IoT 中心设备预配服务设置云 | Microsoft Docs
description: Azure 门户中的 IoT 中心自动设备预配
author: wesmc7777
ms.author: wesmc
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 8f06d3f033a2bf5907dc2ee324359bef0eb247d0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58170728"
---
# <a name="configure-cloud-resources-for-device-provisioning-with-the-iot-hub-device-provisioning-service"></a>通过 IoT 中心设备预配服务配置云资源以进行设备预配

本教程演示如何利用 IoT 中心设备预配服务来设置云资源以进行自动设备预配。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 使用 Azure 门户创建 IoT 中心设备预配服务并获取 ID 范围
> * 创建 IoT 中心
> * 将 IoT 中心链接到设备预配服务
> * 设置设备预配服务上的分配策略

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="create-a-device-provisioning-service-instance-and-get-the-id-scope"></a>创建设备预配服务实例并获取 ID 范围

按照下列步骤创建新的设备预配服务实例。

1. 在 Azure 门户的左上角单击“创建资源”。

2. 在搜索框中键入“设备预配”。 

3. 单击“IoT 中心设备预配服务”。

4. 用以下信息填写“IoT 中心设备预配服务”表：
    
   | 设置       | 建议的值 | 说明 | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **名称** | 任何唯一名称 | -- | 
   | **订阅** | 你的订阅  | 有关订阅的详细信息，请参阅[订阅](https://account.windowsazure.com/Subscriptions)。 |
   | **资源组** | myResourceGroup | 如需有效的资源组名称，请参阅 [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)（命名规则和限制）。 |
   | **位置** | 任何有效的位置 | 有关区域的信息，请参阅 [Azure 区域](https://azure.microsoft.com/regions/)。 |   

   ![在门户中输入有关设备预配服务的基本信息](./media/tutorial-set-up-cloud/create-iot-dps-portal.png)

5. 单击“创建”。 片刻之后，将创建设备预配服务实例并显示“概述”页面。

6. 在新服务实例的“概述”页面上，复制“ID 范围”的值供稍后使用。 该值用于标识注册 ID，并保证注册 ID 的唯一性。

7. 另外，复制“服务终结点”值供稍后使用。 

## <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>检索 IoT 中心的连接字符串

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

现已创建 IoT 中心，因此已获得完成本教程的其余部分所需的主机名和 IoT 中心连接字符串。

## <a name="link-the-device-provisioning-service-to-an-iot-hub"></a>将设备预配服务链接到 IoT 中心

下一步，将设备预配服务链接到 IoT 中心，以便 IoT 中心设备预配服务能向该中心注册设备。 该服务只能将设备预配到已链接至设备预配服务的 IoT 中心。 执行以下步骤。

1. 在“所有资源”页上，单击之前创建的设备预配服务实例。

2. 在“设备预配服务”页上，单击“链接 IoT 中心”。

3. 单击“添加”。

4. 在“添加 IoT 中心的链接”页面上，提供以下信息，并单击“保存”：

    * **订阅：** 确保选择包含 IoT 中心的订阅。 可以链接到位于其他订阅中的 IoT 中心。

    * **IoT 中心：** 选择要与此设备预配服务实例进行链接的 IoT 中心的名称。

    * **访问策略：** 选择 iothubowner 作为用于与 IoT 中心建立链接的凭据。

   ![链接中心名称，以便链接到门户中的设备预配服务](./media/tutorial-set-up-cloud/link-iot-hub-to-dps-portal.png)

## <a name="set-the-allocation-policy-on-the-device-provisioning-service"></a>设置设备预配服务上的分配策略

分配策略是一项 IoT 中心设备预配服务设置，用于确定将设备分配到 IoT 中心的方式。 支持三种分配策略： 

1. **最低延迟**：将设备预配到具有最低延迟的 IoT 中心。

2. **均衡加权分布**（默认）：链接的 IoT 中心等可能地获得预配到它们的设备。 此设置为默认设置。 如果只将设备预配到一个 IoT 中心，则可以保留此设置。 

3. **通过注册列表进行静态注册**：注册列表中所需 IoT 中心的规范优先于设备预配服务级别的分配策略。

若要设置分配策略，请单击“设备预配服务”页上的“管理分配策略”。 确保将分配策略设置为“均匀加权分发”（默认）。 如果进行了任何更改，请在完成后单击“保存”。

![管理分配策略](./media/tutorial-set-up-cloud/iot-dps-manage-allocation.png)

## <a name="clean-up-resources"></a>清理资源

本教程系列中的其他教程是在本教程的基础上制作的。 如果计划继续使用后续的快速入门或相关教程，请勿清除在本教程中创建的资源。 如果不打算继续，请在 Azure 门户中执行以下步骤来删除此教程创建的所有资源。

1. 在 Azure 门户的左侧菜单中单击“所有资源”，然后选择 IoT 中心设备预配服务实例。 在“所有资源”页的顶部单击“删除”。  

2. 在 Azure 门户的左侧菜单中单击“所有资源”，然后选择 IoT 中心。 在“所有资源”页的顶部单击“删除”。
 
## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 使用 Azure 门户创建 IoT 中心设备预配服务并获取 ID 范围
> * 创建 IoT 中心
> * 将 IoT 中心链接到设备预配服务
> * 设置设备预配服务上的分配策略

前往下一教程，了解如何设置设备以进行预配。

> [!div class="nextstepaction"]
> [设置设备以进行预配](tutorial-set-up-device.md)
