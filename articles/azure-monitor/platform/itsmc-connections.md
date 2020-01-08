---
title: Azure Log Analytics 中与 IT Service Management Connector 的手支持连接 | Microsoft Docs
description: 本文提供了有关如何将 ITSM 产品/服务与 Azure Monitor 中的 IT 服务管理连接器 (ITSMC) 相连接，以集中监视和管理 ITSM 工作项的信息。
documentationcenter: ''
author: jyothirmaisuri
manager: riyazp
editor: ''
ms.assetid: 8231b7ce-d67f-4237-afbf-465e2e397105
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: v-jysur
ms.openlocfilehash: ffd9c4bfc934faff1664ff39c0e979a9d6c09487
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399782"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>将 ITSM 产品/服务与 IT 服务管理连接器相连接
本文介绍如何配置 ITSM 产品/服务与 Log Analytics 中的 IT 服务管理连接器 (ITSMC) 之间的连接，以便集中管理工作项。 有关 ITSMC 的详细信息，请参阅[概述](../../azure-monitor/platform/itsmc-overview.md)。

支持以下 ITSM 产品/服务。 选择产品可查看有关如何将该产品连接到 ITSMC 的详细信息。

- [System Center Service Manager](#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](#connect-provance-to-it-service-management-connector-in-azure)
- [Cherwell](#connect-cherwell-to-it-service-management-connector-in-azure)

> [!NOTE]
> 
> ITSM 连接器只能连接到基于云的 ServiceNow 实例。 当前不支持本地 ServiceNow 实例。

## <a name="connect-system-center-service-manager-to-it-service-management-connector-in-azure"></a>将 System Center Service Manager 连接到 Azure 中的 IT 服务管理连接器

以下部分提供有关如何将 System Center Service Manager 产品连接到 Azure 中的 ITSMC 的详细信息。

### <a name="prerequisites"></a>必备组件

请确保满足以下先决条件：

- 已安装 ITSMC。 详细信息：[添加 IT 服务管理连接器解决方案](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution)。
- 已部署并配置 Service Manager Web 应用程序（Web 应用）。 [此处](#create-and-deploy-service-manager-web-app-service)提供了有关 Web 应用的信息。
- 已创建并配置混合连接。 详细信息：[配置混合连接](#configure-the-hybrid-connection)。
- 支持的 Service Manager 版本：2012 R2 或 2016。
- 用户角色：[高级操作员](https://technet.microsoft.com/library/ff461054.aspx)。

### <a name="connection-procedure"></a>连接过程

按照以下过程将 System Center Service Manager 实例连接到 ITSMC：

1. 在 Azure 门户中，转到“所有资源”  ，查找“ServiceDesk(YourWorkspaceName)” 

2.  在“工作区数据源”  下，单击“ITSM 连接”  。

    ![新建连接](media/itsmc-connections/add-new-itsm-connection.png)

3. 在右窗格的顶部，单击“添加”  。

4. 提供下表中所述的信息，并单击“确定”  创建连接。

> [!NOTE]
> 
> 所有这些参数都是必需的。

| **字段** | **说明** |
| --- | --- |
| **连接名称**   | 键入要与 ITSMC 连接的 System Center Service Manager 实例的名称。  以后在此实例中配置工作项/查看详细日志分析时，需要使用此名称。 |
| **合作伙伴类型**   | 选择“System Center Service Manager”。  |
| **服务器 URL**   | 键入 Service Manager Web 应用的 URL。 [此处](#create-and-deploy-service-manager-web-app-service)提供了有关 Service Manager Web 应用的详细信息。
| **客户端 ID**   | 键入（使用自动脚本）生成的、用于对 Web 应用进行身份验证的客户端 ID。 [此处](../../azure-monitor/platform/itsmc-service-manager-script.md)提供了有关自动化脚本的详细信息。|
| **客户端机密**   | 键入为此 ID 生成的客户端机密。   |
| **同步数据**   | 选择需要通过 ITSMC 同步的 Service Manager 工作项。  这些工作项将导入到 Log Analytics。 **选项：** “事件”、“更改请求”。|
| **数据同步范围** | 键入检索数据的过去天数。 **最大限制**：120 天。 |
| **在 ITSM 解决方案中创建新的配置项** | 如果想要在 ITSM 产品中创建配置项，请选择此选项。 选择此选项后，Log Analytics 会在支持的 ITSM 系统中创建受影响的 CI 作为配置项（如果不存在 CI）。 **默认**：已禁用。 |

![Service Manager 连接](media/itsmc-connections/service-manager-connection.png)

成功连接并同步后  ：

- 选定的工作项将从 Service Manager 导入到 Azure **Log Analytics**。 可以在 IT Service Management Connector 磁贴中查看这些工作项的摘要。

- 在此 Service Manager 实例中，可以根据 Log Analytics 警报、日志记录或 Azure 警报创建事件。


了解更多：[根据 Azure 警报创建 ITSM 工作项](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts)。

### <a name="create-and-deploy-service-manager-web-app-service"></a>创建和部署 Service Manager Web 应用服务

为了将本地 Service Manager 与 Azure 中的 ITSMC 连接，Microsoft 已在 GitHub 中创建一个 Service Manager Web 应用。

若要为 Service Manager 设置 ITSM Web 应用，请执行以下操作：

- **部署 Web 应用** – 部署 Web 应用，设置属性，并在 Azure AD 上进行身份验证。 可以使用 Microsoft 提供的[自动化脚本](../../azure-monitor/platform/itsmc-service-manager-script.md)部署 Web 应用。
- 手动**配置混合连接** - [配置此连接](#configure-the-hybrid-connection)。

#### <a name="deploy-the-web-app"></a>部署 Web 应用
使用自动化[脚本](../../azure-monitor/platform/itsmc-service-manager-script.md)部署 Web 应用，设置属性，并在 Azure AD 上进行身份验证。

通过提供以下所需详细信息来运行脚本：

- Azure 订阅详细信息
- 资源组名称
- Location
- Service Manager 服务器详细信息（服务器名称、域、用户名和密码）
- Web 应用的站点名称前缀
- ServiceBus 命名空间。

该脚本将使用指定的名称（以及使该名称保持唯一的其他几个字符串）创建 Web 应用。 它将生成 **Web 应用 URL**、**客户端 ID** 和**客户端机密**。

保存这些值，因为在与 ITSMC 建立连接时需要使用。

**检查 Web 应用安装**

1. 转到“Azure 门户” > “资源”。  
2. 选择 Web 应用，并单击“设置” > “应用程序设置”。  
3. 确认有关通过脚本部署应用时提供的 Service Manager 实例的信息。

### <a name="configure-the-hybrid-connection"></a>配置混合连接

按照以下过程配置混合连接，将 Service Manager 实例与 Azure 中的 ITSMC 连接。

1. 在“Azure 资源”下面找到 Service Manager Web 应用。 
2. 单击“设置” > “网络”。  
3. 在“混合连接”下面，单击“配置混合连接终结点”。  

    ![混合连接网络](media/itsmc-connections/itsmc-hybrid-connection-networking-and-end-points.png)
4. 在“混合连接”边栏选项卡中，单击“添加混合连接”。  

    ![添加混合连接](media/itsmc-connections/itsmc-new-hybrid-connection-add.png)

5. 在“添加混合连接”边栏选项卡中，单击“创建新的混合连接”。  

    ![新建混合连接](media/itsmc-connections/itsmc-create-new-hybrid-connection.png)

6. 键入以下值：

   - **终结点名称**：指定新混合连接的名称。
   - **终结点主机**：Service Manager 管理服务器的 FQDN。
   - **终结点端口**：类型 5724
   - **服务总线命名空间**：使用现有的服务总线命名空间，或新建一个。
   - **位置**：选择位置。
   - **名称**：指定服务总线的名称（如果要创建服务总线）。

     ![混合连接值](media/itsmc-connections/itsmc-new-hybrid-connection-values.png)
6. 单击“确定”关闭“创建混合连接”边栏选项卡并开始创建混合连接。  

    创建混合连接后，它会显示在边栏选项卡下面。

7. 创建混合连接后，请选择该连接，并单击“添加所选混合连接”。 

    ![新建混合连接](media/itsmc-connections/itsmc-new-hybrid-connection-added.png)

#### <a name="configure-the-listener-setup"></a>配置侦听器设置

使用以下过程配置混合连接的侦听器设置。

1. 在“混合连接”边栏选项卡中，单击“下载连接管理器”，并将它安装在运行 System Center Service Manager 实例的计算机上。  

    安装完成后，“开始”菜单下会出现“混合连接管理器 UI”选项。  

2. 单击“混合连接管理器 UI”。系统会提示提供 Azure 凭据。 

3. 请使用 Azure 凭据登录，并选择在其中创建了混合连接的订阅。

4. 单击“ **保存**”。

现已成功连接到混合连接。

![混合连接成功](media/itsmc-connections/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]
> 
> 创建混合连接后，请通过访问部署的 Service Manager Web 应用来验证和测试该连接。 在确保连接成功后，再尝试连接到 Azure 中的 ITSMC。

以下示例图像显示了成功连接的详细信息：

![混合连接测试](media/itsmc-connections/itsmc-hybrid-connection-test.png)

## <a name="connect-servicenow-to-it-service-management-connector-in-azure"></a>将 ServiceNow 连接到 Azure 中的 IT 服务管理连接器

以下部分提供有关如何将 ServiceNow 产品连接到 Azure 中的 ITSMC 的详细信息。

### <a name="prerequisites"></a>必备组件
请确保满足以下先决条件：
- 已安装 ITSMC。 详细信息：[添加 IT 服务管理连接器解决方案](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution)。
- ServiceNow 支持的版本：马德里、 伦敦、 金斯顿、 雅加达、 伊斯坦布尔、 赫尔辛基、 日内瓦。

**ServiceNow 管理员必须在其 ServiceNow 实例中执行以下操作**：
- 生成 ServiceNow 产品的客户端 ID 和客户端密码。 有关如何生成客户端 ID 和机密的信息，请根据需要参阅以下信息：

    - [为马德里设置 OAuth](https://docs.servicenow.com/bundle/madrid-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [为伦敦设置 OAuth](https://docs.servicenow.com/bundle/london-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [为金斯顿设置 OAuth](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [为雅加达设置 OAuth](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [为伊斯坦布尔设置 OAuth](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [为赫尔辛基设置 OAuth](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [为日内瓦设置 OAuth](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)


- 安装用于 Microsoft Log Analytics 集成的用户应用（ServiceNow 应用）。 [了解详细信息](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1 )。
- 为安装的用户应用创建集成用户角色。 [此处](#create-integration-user-role-in-servicenow-app)提供了有关如何创建集成用户角色的信息。

### <a name="connection-procedure"></a>**连接过程**
使用以下过程创建 ServiceNow 连接：


1. 在 Azure 门户中，转到“所有资源”  ，查找“ServiceDesk(YourWorkspaceName)” 

2.  在“工作区数据源”  下，单击“ITSM 连接”  。
    ![新建连接](media/itsmc-connections/add-new-itsm-connection.png)

3. 在右窗格的顶部，单击“添加”  。

4. 提供下表中所述的信息，并单击“确定”  创建连接。


> [!NOTE]
> 所有这些参数都是必需的。

| **字段** | **说明** |
| --- | --- |
| **连接名称**   | 键入需要与 ITSMC 连接的 ServiceNow 实例的名称。  以后在此 ITSM 中配置工作项/查看详细日志分析时，需要在 Log Analytics 中使用此名称。 |
| **合作伙伴类型**   | 选择“ServiceNow”。  |
| **用户名**   | 键入在 ServiceNow 应用中创建的、用于支持连接到 ITSMC 的集成用户名。 详细信息：[创建 ServiceNow 应用用户角色](#create-integration-user-role-in-servicenow-app)。|
| **密码**   | 键入此用户名的关联密码。 **注意**：用户名和密码仅用于生成身份验证令牌，不会存储在 ITSMC 服务中的任何位置。  |
| **服务器 URL**   | 键入需要连接到 ITSMC 的 ServiceNow 实例的 URL。 |
| **客户端 ID**   | 键入前面生成的、用于 OAuth2 身份验证的客户端 ID。  有关生成客户端 ID 和机密的详细信息： [OAuth 设置](https://wiki.servicenow.com/index.php?title=OAuth_Setup)。 |
| **客户端机密**   | 键入为此 ID 生成的客户端机密。   |
| **数据同步范围**   | 选择要通过 ITSMC 同步到 Azure Log Analytics 的 ServiceNow 工作项。  选定的值将导入到 Log Analytics。   **选项：** “事件”和“更改请求”。|
| **同步数据** | 键入检索数据的过去天数。 **最大限制**：120 天。 |
| **在 ITSM 解决方案中创建新的配置项** | 如果想要在 ITSM 产品中创建配置项，请选择此选项。 选择此选项后，ITSMC 会在支持的 ITSM 系统中创建受影响的 CI 作为配置项（如果不存在 CI）。 **默认**：已禁用。 |

![ServiceNow 连接](media/itsmc-connections/itsm-connection-servicenow-connection-latest.png)

成功连接并同步后  ：

- 选定的工作项将从 ServiceNow 实例导入到 Azure **Log Analytics**。 可以在 IT Service Management Connector 磁贴中查看这些工作项的摘要。

- 在此 ServiceNow 实例中，可以根据 Log Analytics 警告、日志记录或 Azure 警报创建事件。

了解更多：[根据 Azure 警报创建 ITSM 工作项](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts)。

### <a name="create-integration-user-role-in-servicenow-app"></a>在 ServiceNow 应用中创建集成用户角色

使用以下过程：

1. 访问 [ServiceNow 应用商店](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1)，并在 ServiceNow 实例中安装**用于 ServiceNow 和 Microsoft OMS 集成的用户应用**。
   
   >[!NOTE]
   >作为从 Microsoft Operations Management Suite (OMS) 到 Azure Monitor 的持续过渡的一部分，OMS 现在称为 Log Analytics。     
2. 安装后，请访问 ServiceNow 实例的左侧导航栏，搜索并选择 Microsoft OMS 集成器。  
3. 单击“安装清单”。 

   如果用户角色尚未创建，状态会显示为“未完成”。 

4. 在“创建集成用户”  旁边的文本框中，输入可连接到 Azure 中的 ITSMC 的用户的用户名。
5. 输入此用户的密码，并单击“确定”。   

> [!NOTE]
> 
> 使用这些凭据在 Azure 中建立 ServiceNow 连接。

将显示新建的用户及其分配的默认角色。

默认角色  ：
- personalize_choices
- import_transformer
-   x_mioms_microsoft.user
-   itil
-   template_editor
-   view_changer

成功创建用户后，“检查安装清单”的状态将改为“已完成”，同时会列出针对应用创建的用户角色的详细信息。 

> [!NOTE]
> 
> ITSM 连接器无需在 ServiceNow 实例上安装任何其他模块即可向 ServiceNow 发送事件。 若要在 ServiceNow 实例中使用 EventManagement 模块且要使用连接器在 ServiceNow 中创建事件或警报，请将以下角色添加到集成用户：
> 
>    - evt_mgmt_integration
>    - evt_mgmt_operator  


## <a name="connect-provance-to-it-service-management-connector-in-azure"></a>将 Provance 连接到 Azure 中的 IT 服务管理连接器

以下部分提供有关如何将 Provance 产品连接到 Azure 中的 ITSMC 的详细信息。


### <a name="prerequisites"></a>必备组件

请确保满足以下先决条件：


- 已安装 ITSMC。 详细信息：[添加 IT 服务管理连接器解决方案](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution)。
- Provance 应用应已注册到 Azure AD，并且可提供客户端 ID。 有关详细信息，请参阅[如何配置 Active Directory 身份验证](../../app-service/configure-authentication-provider-aad.md)。

- 用户角色：管理员。

### <a name="connection-procedure"></a>连接过程

使用以下过程创建 Provance 连接：

1. 在 Azure 门户中，转到“所有资源”  ，查找“ServiceDesk(YourWorkspaceName)” 

2.  在“工作区数据源”  下，单击“ITSM 连接”  。
    ![新建连接](media/itsmc-connections/add-new-itsm-connection.png)

3. 在右窗格的顶部，单击“添加”  。

4. 提供下表中所述的信息，并单击“确定”  创建连接。

> [!NOTE]
> 
> 所有这些参数都是必需的。

| **字段** | **说明** |
| --- | --- |
| **连接名称**   | 键入需要与 ITSMC 连接的 Provance 实例的名称。  以后在此 ITSM 中配置工作项/查看详细日志分析时，需要使用此名称。 |
| **合作伙伴类型**   | 选择“Provance”。  |
| **用户名**   | 键入可以连接到 ITSMC 的用户名。    |
| **密码**   | 键入此用户名的关联密码。 **注意：** 用户名和密码仅用于生成身份验证令牌，不会存储在 ITSMC 服务中的任何位置。|
| **服务器 URL**   | 键入需要连接到 ITSMC 的 Provance 实例的 URL。 |
| **客户端 ID**   | 键入在 Provance 实例中生成的、用于对此连接进行身份验证的客户端 ID。  有关客户端 ID 的详细信息，请参阅[如何配置 Active Directory 身份验证](../../app-service/configure-authentication-provider-aad.md)。 |
| **数据同步范围**   | 选择需要通过 ITSMC 同步到 Azure Log Analytics 的 Provance 工作项。  这些工作项将导入到 Log Analytics。   **选项：** “事件”、“更改请求”。|
| **同步数据** | 键入检索数据的过去天数。 **最大限制**：120 天。 |
| **在 ITSM 解决方案中创建新的配置项** | 如果想要在 ITSM 产品中创建配置项，请选择此选项。 选择此选项后，ITSMC 会在支持的 ITSM 系统中创建受影响的 CI 作为配置项（如果不存在 CI）。 **默认**：已禁用。|

![Provance 连接](media/itsmc-connections/itsm-connections-provance-latest.png)

成功连接并同步后  ：

- 选定的工作项将从此 Provance 实例导入到 Azure **Log Analytics**。 可以在 IT Service Management Connector 磁贴中查看这些工作项的摘要。

- 在此 Provance 实例中，可以根据 Log Analytics 警告、日志记录或 Azure 警报创建事件。

了解更多：[根据 Azure 警报创建 ITSM 工作项](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts)。

## <a name="connect-cherwell-to-it-service-management-connector-in-azure"></a>将 Cherwell 连接到 Azure 中的 IT 服务管理连接器

以下部分提供有关如何将 Cherwell 产品连接到 Azure 中的 ITSMC 的详细信息。

### <a name="prerequisites"></a>必备组件

请确保满足以下先决条件：

- 已安装 ITSMC。 详细信息：[添加 IT 服务管理连接器解决方案](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution)。
- 已生成客户端 ID。 详细信息：[为 Cherwell 生成客户端 ID](#generate-client-id-for-cherwell)。
- 用户角色：管理员。

### <a name="connection-procedure"></a>连接过程

使用以下过程创建 Provance 连接：

1. 在 Azure 门户中，转到“所有资源”  ，查找“ServiceDesk(YourWorkspaceName)” 

2.  在“工作区数据源”  下，单击“ITSM 连接”  。
    ![新建连接](media/itsmc-connections/add-new-itsm-connection.png)

3. 在右窗格的顶部，单击“添加”  。

4. 提供下表中所述的信息，并单击“确定”  创建连接。

> [!NOTE]
> 
> 所有这些参数都是必需的。

| **字段** | **说明** |
| --- | --- |
| **连接名称**   | 键入需要连接到 ITSMC 的 Cherwell 实例名称。  以后在此 ITSM 中配置工作项/查看详细日志分析时，需要使用此名称。 |
| **合作伙伴类型**   | 选择“Cherwell”。  |
| **用户名**   | 键入可以连接到 ITSMC 的 Cherwell 用户名。 |
| **密码**   | 键入此用户名的关联密码。 **注意：** 用户名和密码仅用于生成身份验证令牌，不会存储在 ITSMC 服务中的任何位置。|
| **服务器 URL**   | 键入需要连接到 ITSMC 的 Cherwell 实例的 URL。 |
| **客户端 ID**   | 键入在 Cherwell 实例中生成的、用于对此连接进行身份验证的客户端 ID。   |
| **数据同步范围**   | 选择需要通过 ITSMC 同步的 Cherwell 工作项。  这些工作项将导入到 Log Analytics。   **选项：** “事件”、“更改请求”。 |
| **同步数据** | 键入检索数据的过去天数。 **最大限制**：120 天。 |
| **在 ITSM 解决方案中创建新的配置项** | 如果想要在 ITSM 产品中创建配置项，请选择此选项。 选择此选项后，ITSMC 会在支持的 ITSM 系统中创建受影响的 CI 作为配置项（如果不存在 CI）。 **默认**：已禁用。 |


![Provance 连接](media/itsmc-connections/itsm-connections-cherwell-latest.png)

成功连接并同步后  ：

- 选定的工作项将从此 Cherwell 实例导入到 Azure **Log Analytics**。 可以在 IT Service Management Connector 磁贴中查看这些工作项的摘要。

- 在此 Cherwell 实例中，可以根据 Log Analytics 警告、日志记录或 Azure 警报创建事件。

了解更多：[根据 Azure 警报创建 ITSM 工作项](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts)。

### <a name="generate-client-id-for-cherwell"></a>为 Cherwell 生成客户端 ID

若要为 Cherwell 生成客户端 ID/密钥，请使用以下过程：

1. 以管理员身份登录到 Cherwell 实例。
2. 单击“安全性” > “编辑 REST API 客户端设置”。  
3. 选择“创建新客户端” > “客户端机密”。  

    ![Cherwell 用户 ID](media/itsmc-connections/itsmc-cherwell-client-id.png)


## <a name="next-steps"></a>后续步骤
 - [根据 Azure 警报日志创建 ITSM 工作项](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts)
