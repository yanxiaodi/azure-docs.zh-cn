---
title: 为 Azure 云服务中的角色启用远程桌面连接 | Microsoft Docs
description: 如何配置 Azure 云服务应用程序以允许远程桌面连接
services: cloud-services
documentationcenter: ''
author: mmccrory
ms.service: cloud-services
ms.topic: article
ms.date: 11/28/2016
ms.author: memccror
ms.openlocfilehash: bea4e0c43d6ae6e0ea05c43343535195a25cf3e2
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359515"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services"></a>为 Azure 云服务中的角色设置远程桌面连接

> [!div class="op_single_selector"]
> * [Azure 门户](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

可以通过远程桌面访问在 Azure 中运行的角色的桌面。 也可以使用远程桌面连接，在应用程序正在运行时排查和诊断其问题。

可以在开发过程中通过在服务定义中加入远程桌面模块来在角色中启用远程桌面连接，也可以通过远程桌面扩展选择启用远程桌面。 首选方法是使用远程桌面扩展，因为即使在部署应用程序后，也能启用远程桌面，而不必重新部署应用程序。

## <a name="configure-remote-desktop-from-the-azure-portal"></a>从 Azure 门户配置远程桌面

Azure 门户使用远程桌面扩展方法，即使在部署应用程序之后，也能启用远程桌面。 使用云服务的“远程桌面”设置，可以启用远程桌面，更改用于连接虚拟机的本地 Administrator 帐户、身份验证使用的证书，以及设置到期日期。

1. 单击“云服务”，再选择云服务的名称，然后选择“远程桌面”。

    ![云服务远程桌面](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop.png)

2. 选择希望为单个角色还是所有角色启用远程桌面，并将切换器的值更改为“已启用” 。

3. 填写用户名、密码、到期时间和证书必填字段。

    ![云服务远程桌面](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Details.png)

   > [!WARNING]
   > 当首次启用远程桌面并选择“确定”（选中标记）时，所有角色实例会重新启动。 为避免重新启动，必须对于此角色安装用于对密码进行加密的证书。 要避免重新启动，请[上载云服务的证书](cloud-services-configure-ssl-certificate-portal.md#step-3-upload-a-certificate)，并返回到此对话框。

4. 在“**角色**”中，选择要更新的角色，或选择“**全部**”以选择所有角色。

5. 完成配置更新后，选择“保存”。 可能需要一些时间角色实例才能完成接收连接的准备。

## <a name="remote-into-role-instances"></a>远程到角色实例

为角色启用远程桌面后，可以直接从 Azure 门户启动连接：

1. 单击“实例”，打开“实例”设置。
2. 选择一个配置了远程桌面的角色实例。
3. 单击“连接”，下载角色实例的 RDP 文件。

    ![云服务远程桌面](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Connect.png)

4. 依次单击“**打开**”和“**连接**”，以启动远程桌面连接。

>[!NOTE]
> 如果云服务位于 NSG 后，可能需要创建允许端口**3389** 和 **20000** 上的流量的规则。  远程桌面使用端口 **3389**。  云服务实例经过负载均衡，因此无法直接控制要连接到哪个实例。  RemoteForwarder 和 RemoteAccess 代理管理 RDP 流量，允许客户端发送 RDP cookie 和指定要连接到的单个实例。  RemoteForwarder 和 RemoteAccess 代理要求打开端口 20000*（如果具有 NSG，此端口可能已被阻止）。

## <a name="additional-resources"></a>其他资源

[如何配置云服务](cloud-services-how-to-configure-portal.md)
