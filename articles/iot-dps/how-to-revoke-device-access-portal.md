---
title: 如何从 Azure IoT 中心设备预配服务取消注册设备
description: 如何从 Azure IoT 中心设备预配服务取消注册设备以阻止预配
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 0dadf0ec248dac01e5cc65779004477bf4afc823
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "62113565"
---
# <a name="how-to-disenroll-a-device-from-azure-iot-hub-device-provisioning-service"></a>如何从 Azure IoT 中心设备预配服务取消注册设备

适当管理设备凭据对于高可见性 IoT 解决方案等系统至关重要。 此类系统的最佳做法是指定明确的计划，规定在设备凭据（共享访问签名 (SAS) 令牌或 X.509 证书）可能被盗用时应如何撤消设备的访问权限。 

设备预配服务中的注册使设备能够[自动预配](concepts-auto-provisioning.md)。 已预配的设备是已向 IoT 中心进行了注册的设备，这允许设备接收其初始[设备孪生](~/articles/iot-hub/iot-hub-devguide-device-twins.md)状态并开始报告遥测数据。 本文介绍了如何从预配服务实例取消注册设备，阻止设备在将来再次预配。

> [!NOTE] 
> 请注意要撤消访问权限的设备的重试策略。 例如，具有无限的重试策略的设备可能持续尝试注册到预配服务。 这种情况会消耗服务资源并可能影响性能。

## <a name="blacklist-devices-by-using-an-individual-enrollment-entry"></a>使用单独登记项将设备加入方块列表

单独登记适用于单个设备，可使用 X.509 证书或 SAS 令牌（在真实或虚拟 TPM 中）作为证明机制。 （使用 SAS 令牌作为证明机制的设备通过单独登记进行预配。）若要将具有单独登记的设备加入方块列表，可禁用或删除其登记项。 

若要暂时将设备加入方块列表，可禁用其登记项： 

1. 登录 Azure 门户，选择左侧菜单中的“所有资源”。 
2. 从资源列表中，单击要阻止设备访问的预配服务。
3. 在预配服务中，选择“管理登记”，然后选择“单独登记”选项卡   。
4. 选择要加入方块列表的设备的登记项。 

    ![选择单个注册](./media/how-to-revoke-device-access-portal/select-individual-enrollment.png)

5. 在注册页上，滚动到底部，为“启用项”开关选择“禁用”，然后选择“保存”    。  

   ![在门户中禁用单独注册](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)

若要永久将设备加入方块列表，可删除其登记项。

1. 登录 Azure 门户，选择左侧菜单中的“所有资源”。 
2. 从资源列表中，单击要阻止设备访问的预配服务。
3. 在预配服务中，选择“管理登记”，然后选择“单独登记”选项卡   。
4. 选中要加入方块列表的设备的登记项旁边的复选框。 
5. 选择窗口顶部的“删除”，然后选择“是”以确认删除登记。   

   ![在门户中删除单独注册](./media/how-to-revoke-device-access-portal/delete-individual-enrollment.png)


完成该过程后，会看到该项已从单独登记列表中删除。  

## <a name="blacklist-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group"></a>使用登记组将 X.509 中间或根 CA 证书加入方块列表

X.509 证书通常排列在证书信任链中。 如果链中任何阶段的证书被盗用，则信任受损。 必须将该证书加入方块列表，以防设备预配服务预配包含该证书的任何链中的设备下游。 若要详细了解 X.509 证书和及如何用其预配服务，请参阅 [X.509 证书](./concepts-security.md#x509-certificates)。 

注册组是共享 X.509 证书的常见证明机制的设备的项，由同一个中间或根 CA 签名。 登记组项配置了与中间或根 CA 相关联的 X.509 证书。 由证书链中包含该证书的设备共享的任何配置值（如孪生状态和 IoT 中心连接）。 若要将证书加入方块列表，可禁用或删除其登记组。

若要暂时将证书加入方块列表，可禁用其登记组： 

1. 登录 Azure 门户，选择左侧菜单中的“所有资源”。 
2. 在资源列表中，选择要阻止签名证书访问的预配服务。
3. 在预配服务中，选择“管理登记”，然后选择“登记组”选项卡   。
4. 选择使用了要将其加入方块列表的证书的注册组。
5. 在“启用项”开关中选择“禁用”，然后选择“保存”    。  

   ![在门户中禁用注册组项](./media/how-to-revoke-device-access-portal/disable-enrollment-group.png)

    
若要永久将证书加入方块列表，可删除其登记组：

1. 登录 Azure 门户，选择左侧菜单中的“所有资源”。 
2. 从资源列表中，单击要阻止设备访问的预配服务。
3. 在预配服务中，选择“管理登记”，然后选择“登记组”选项卡   。
4. 选中要加入方块列表的证书的登记组旁边的复选框。 
5. 选择窗口顶部的“删除”，然后选择“是”以确认删除登记组。   

   ![在门户中删除注册组项](./media/how-to-revoke-device-access-portal/delete-enrollment-group.png)

完成该过程后，会看到该项已从登记组列表中删除。  

> [!NOTE]
> 删除证书登记组后，如果根证书或证书链中上游位置的其他中间证书存在已启用的登记组，则证书链中包含该证书的设备可能仍将能够登记。

## <a name="blacklist-specific-devices-in-an-enrollment-group"></a>注册组中阻止特定设备

实现 X.509 证明机制的设备使用设备的证书链和私钥进行身份验证。 如果设备通过设备预配服务进行连接和身份验证，服务将首先查找与设备凭据匹配的单个登记。 然后，服务搜索登记组以确定是否可以预配设备。 如果服务发现设备对应了已禁用的单个登记，它将阻止设备连接。 即使设备的证书链中存在对应于中间或根 CA 的已启用登记组，服务也会阻止连接。 

若要阻止注册组中的单个设备，请执行以下步骤：

1. 登录 Azure 门户，选择左侧菜单中的“所有资源”。 
2. 从资源列表中，选择包含要加入方块列表的设备所对应登记组的预配服务。
3. 在预配服务中，选择“管理登记”，然后选择“单独登记”选项卡   。
4. 选择顶部的“添加单个注册”  按钮。 
5. 在“添加注册”  页上，选择“X.509”  作为设备的证明机制  。

    上传设备证书，并输入要列入阻止名单的设备 ID。 对于证书，要使用设备上安装的已签名最终实体证书。 设备使用已签名的最终实体证书进行身份验证。

    ![设置已列入阻止名单的设备的设备属性](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group-1.png)

6. 滚动到“添加注册”  页底部，对“启用项”  开关选择“禁用”  ，然后选择“保存”  。 

    [![在门户中使用禁用的个人注册项来从组注册禁用设备](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png)](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png#lightbox)

成功创建注册后，你应该会在“个人注册”选项卡上看到列出的已禁用的设备注册  。 

## <a name="next-steps"></a>后续步骤

取消注册也是更大的取消预配流程的一部分。 取消预配设备包括从预配服务取消注册以及从 IoT 中心取消注册。 若要了解完整流程，请参阅[如何取消预配以前自动预配的设备](how-to-unprovision-devices.md) 

