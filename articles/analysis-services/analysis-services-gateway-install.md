---
title: 为 Azure Analysis Services 安装本地数据网关 |Microsoft Docs
description: 了解如何安装并配置本地数据网关。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: e87a8221a4db4dfab132a91a31a9ba5b5602a3db
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2019
ms.locfileid: "68678432"
---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>安装并配置本地数据网关

同一区域中的一个或多个 Azure Analysis Services 服务器连接到本地数据源时，需要具备本地数据网关。  尽管安装的网关与其他服务 (如 Power BI、电源应用和逻辑应用) 所使用的网关相同, 但是, 在为 Azure Analysis Services 进行安装时, 需要完成一些额外的步骤。 本安装文章特定于**Azure Analysis Services**。

若要详细了解网关及其使用方式 Azure Analysis Services, 请参阅[连接到本地数据源](analysis-services-gateway.md)。

## <a name="prerequisites"></a>先决条件

**最低要求：**

* .NET 4.5 Framework
* 64 位版本的 Windows 7/Windows Server 2008 R2（或更高版本）

**推荐：**

* 8 核 CPU
* 8 GB 内存
* 64 位版本的 Windows 2012 R2（或更高版本）

**重要注意事项：**

* 安装过程中，向 Azure 注册你的网关时，会选中你订阅的默认区域。 你也可以选择其他区域。 如果你在多个区域有服务器，则必须为每个区域都安装一个网关。 
* 不能在域控制器上安装网关。
* 一台计算机上只能安装一个网关。
* 在计算机处于开启但未处于休眠状态下安装网关。
* 不要在使用无线网络连接的计算机上安装网关。 否则，可能会降低性能。
* 安装网关时，你用来登录到计算机的用户帐户必须具有“作为服务登录”权限。 安装完成后，本地数据网关服务使用 NT SERVICE\PBIEgwService 帐户作为服务登录。 可以在安装期间指定一个不同的帐户，也可以在安装完成后在“服务”中指定一个不同的帐户。 请确保组策略设置同时允许你在安装时登录的帐户以及你选择的具有“作为服务登录”权限的服务帐户。
* 在 Azure AD 中使用与要在其中注册网关的订阅相同[租户](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant)的帐户登录到 Azure。 安装和注册网关时不支持 Azure B2B（来宾）帐户。
* 如果数据源位于 Azure 虚拟网络 (VNet) 上，则必须配置 [AlwaysUseGateway](analysis-services-vnet-gateway.md) 服务器属性。
* Azure 德国区域不支持此处所述的（统一）网关。 请改用从门户中服务器的快速入门安装的“用于 Azure Analysis Services 的专用本地网关”。 


## <a name="download"></a>下载

 [下载网关](https://go.microsoft.com/fwlink/?LinkId=820925&clcid=0x409)

## <a name="install"></a>安装

1. 运行安装程序。

2. 选择 **"本地数据网关"** 。

   ![选择](media/analysis-services-gateway-install/aas-gateway-installer-select.png)

2. 选择一个位置，接受条款，然后单击“安装”。

   ![安装位置和许可条款](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. 登录 Azure。 该帐户必须位于你租户的 Azure Active Directory 中。 该帐户用于网关管理员。 安装和注册网关时不支持 Azure B2B（来宾）帐户。

   ![登录  Azure](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > 如果使用域帐户登录，它将映射到你在 Azure AD 中的组织帐户。 你的组织帐户将用作网关管理员。

## <a name="register"></a>注册

若要在 Azure 中创建网关资源，则必须在网关云服务中注册你安装的本地实例。 

1.  选择“在此计算机上注册新网关”。

    ![注册](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. 为你的网关键入名称和恢复密钥。 默认情况下，网关使用你订阅的默认区域。 如果你需要选择其他区域，请选择“更改区域”。

    > [!IMPORTANT]
    > 将恢复密钥保存在安全位置。 接管、迁移或还原网关时需要使用恢复密钥。 

   ![注册](media/analysis-services-gateway-install/aas-gateway-register-name.png)


## <a name="create-resource"></a>创建 Azure 网关资源

在安装并注册了网关后，需要在 Azure 订阅中创建网关资源。 使用注册网关时使用的帐户登录到 Azure。

1. 在 Azure 门户中, 单击 "**创建资源**", 然后搜索 **"本地数据网关**", 然后单击 "**创建**"。

   ![创建网关资源](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. 在“创建连接网关”中，输入以下设置：

   * **名称**：输入网关资源的名称。 

   * **订阅**：选择要与网关资源关联的 Azure 订阅。 
   
     默认订阅取决于用来登录的 Azure 帐户。

   * **资源组**：创建资源组，或选择现有资源组。

   * **位置**：选择网关的注册区域。

   * **安装名称**：如果尚未选择网关安装, 请选择您的计算机上安装的网关并注册。 

     完成后，单击“创建”。

## <a name="connect-servers"></a>将服务器连接到网关资源

1. 在 Azure Analysis Services 服务器概述中，单击“本地数据网关”。

   ![将服务器连接到网关](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. 在“选取要连接的本地数据网关”，选择你的网关资源，然后单击“连接所选网关”。

   ![将服务器连接到网关资源](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > 如果列表中不显示你的网关，很可能是你的服务器与你注册网关时指定的区域不在同一个区域。

    如果服务器和网关资源之间的连接成功, 则状态将显示 "**已连接**"。


    ![将服务器连接到网关资源成功](media/analysis-services-gateway-install/aas-gateway-connect-success.png)

就这么简单。 如果你需要打开端口或执行任何故障排除时，一定要签出[本地数据网关](analysis-services-gateway.md)。

## <a name="next-steps"></a>后续步骤

* [管理 Analysis Services](analysis-services-manage.md)   
* [从 Azure Analysis Services 获取数据](analysis-services-connect.md)   
* [对 Azure 虚拟网络上的数据源使用网关](analysis-services-vnet-gateway.md)
