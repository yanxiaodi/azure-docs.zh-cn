---
title: 远程桌面网关与 Azure MFA NPS 扩展的集成-Azure Active Directory
description: 使用 Microsoft Azure 的网络策略服务器扩展将远程桌面网关基础结构与 Azure MFA 进行集成
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf9188502dd2b17bcd898e2655138b06cfe5cebf
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70898556"
---
# <a name="integrate-your-remote-desktop-gateway-infrastructure-using-the-network-policy-server-nps-extension-and-azure-ad"></a>使用网络策略服务器 (NPS) 扩展和 Azure AD 集成远程桌面网关基础结构

本文将详细介绍如何使用 Microsoft Azure 的网络策略服务器 (NPS) 扩展集成远程桌面网关基础结构与 Azure 多重身份验证 (MFA)。

Azure 网络策略服务 (NPS) 扩展允许客户使用 Azure 基于云的[多重身份验证 (MFA)](multi-factor-authentication.md) 来保护远程身份验证拨入用户服务 (RADIUS) 客户端身份验证。 该解决方案提供双重验证，用于将第二层安全性添加到用户的登录和事务。

本文将逐步说明如何使用 Azure 的 NPS 扩展集成 NPS 基础结构与 Azure MFA。 这使得尝试登录到远程桌面网关的用户能够进行安全验证。

> [!NOTE]
> 本文不适用于 MFA 服务器部署，仅适用于 Azure MFA（基于云）部署。

网络策略和访问服务 (NPS) 使组织能够执行以下操作：

* 通过指定执行连接操作的人员、允许连接的时间、连接持续时间，以及客户端必须用于连接的安全级别等来定义网络请求的管理和控制中心位置。 这些策略可以集中在一个位置一次性指定，而不是在每个 VPN 或远程桌面 (RD) 网关服务器上进行指定。 RADIUS 协议提供集中身份验证、授权和计帐 (AAA)。
* 建立和强制执行网络访问保护 (NAP) 客户端健康策略，可确定是授予设备对网络资源的无限制还是受限制的访问权限。
* 提供一种强制进行身份验证和授权，以访问支持 802.1x 无线访问点和以太网交换机的方式。

通常，组织使用 NPS （RADIUS）来简化和集中 VPN 策略的管理。 然而，许多组织也会使用 NPS 简化和集中 RD 桌面连接授权策略 (RD CAP) 的管理。

另外，组织还可以将 NPS 与 Azure MFA 进行集成，以增强安全性并提供高级别的符合性。 这将有助于确保用户建立双重验证以登录到远程桌面网关。 已授予访问权限的用户，他们必须提供其用户名/密码组合与用户已有的信息。 此信息必须受信任且不容易复制，例如手机号码、座机号码、移动设备上的应用程序等等。 RDG 当前支持用于2FA 的 Microsoft 验证器应用方法的电话呼叫和推送通知。 有关支持的身份验证方法的详细信息，请参阅[决定你的用户可以使用哪些身份验证方法](howto-mfa-nps-extension.md#determine-which-authentication-methods-your-users-can-use)部分。

在为 Azure 提供 NPS 扩展之前，希望对集成的 NPS 和 Azure MFA 环境实施双重验证的客户，必须在本地环境中配置和维护单独的 MFA 服务器，如[使用 RADIUS 的远程桌面网关和 Azure 多重身份验证服务器](howto-mfaserver-nps-rdg.md)中所述。

现在提供了 Azure 的 NPS 扩展之后，可使组织选择部署基于内部部署的 MFA 解决方案或基于云的 MFA 解决方案来保护 RADIUS 客户端身份验证。

## <a name="authentication-flow"></a>身份验证流

通过远程桌面网关授予网络资源访问权限的用户，必须满足一个 RD 连接授权策略 (RD CAP) 和一个 RD 资源授权策略 (RD RAP) 中指定的条件。 RD CAP 指定授权连接到 RD 网关的人员。 RD RAP 指定允许用户通过 RD 网关连接到的网络资源，如远程桌面或远程应用程序。

可以将 RD 网关配置为使用 RD CAP 的中央策略存储。 RD RAP 无法使用中央策略，因为在 RD 网关上处理它们。 将 RD 网关配置为使用 RD CAP 中央策略的示例是：用作中央策略存储的另一个 NPS 服务器的 RADIUS 客户端。

当 Azure 的 NPS 扩展与 NPS 和远程桌面网关集成时，成功的身份验证流如下：

1. 远程桌面网关服务器从远程桌面用户接收身份验证请求，用于连接到资源，如远程桌面会话。 作为 RADIUS 客户端，远程桌面网关服务器将请求转换为 RADIUS 访问请求消息，并将此消息发送到安装了 NPS 扩展的 RADIUS (NPS) 服务器。
1. 用户名和密码组合在 Active Directory 中进行验证，同时进行用户身份验证。
1. 如果满足 NPS 连接请求和网络策略中指定的所有条件（例如，时间或组成员资格限制），NPS 扩展将触发对 Azure MFA 进行辅助身份验证的请求。
1. Azure MFA 与 Azure AD 通信，检索用户的详细信息并使用受支持的方法执行辅助身份验证。
1. 在 MFA 质询成功之后，Azure MFA 将结果传递给 NPS 扩展。
1. 安装此扩展的 NPS 服务器向远程桌面网关服务器发送 RD CAP 策略的 RADIUS 访问接受消息。
1. 通过 RD 网关授予用户访问所请求的网络资源的权限。

## <a name="prerequisites"></a>先决条件

本部分将详细介绍将 Azure MFA 与远程桌面网关集成之前的必备条件。 开始集成之前，必须具备以下先决条件：  

* 远程桌面服务 (RDS) 基础结构
* Azure MFA 许可证
* Windows Server 软件
* 网络策略和访问服务 (NPS) 角色
* 将 Azure Active Directory 与本地 Active Directory 同步
* Azure Active Directory GUID ID

### <a name="remote-desktop-services-rds-infrastructure"></a>远程桌面服务 (RDS) 基础结构

必须具有有效的远程桌面服务 (RDS) 基础结构。 否则，可以使用以下快速入门模板在 Azure 中快速创建此基础结构：[创建远程桌面会话集合部署](https://github.com/Azure/azure-quickstart-templates/tree/ad20c78b36d8e1246f96bb0e7a8741db481f957f/rds-deployment)。

如果希望快速手动创建一个测试用的本地 RDS 基础结构，请按照以下步骤进行部署。
**了解详情**：[通过 Azure 快速入门](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-in-azure)和[基本 RDS 基础结构部署](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)部署 RDS。

### <a name="azure-mfa-license"></a>Azure MFA 许可证

需要一个 Azure MFA 许可证，Azure AD Premium 或包括它的其他捆绑包中提供了该许可证。 Azure MFA 的基于使用量的许可证（例如，按用户或按身份验证许可证）与 NPS 扩展不兼容。 有关详细信息，请参阅[如何获取 Azure 多重身份验证](concept-mfa-licensing.md)。 出于测试目的，你可以使用一个试用订阅。

### <a name="windows-server-software"></a>Windows Server 软件

NPS 扩展需要安装了 NPS 角色服务的 Windows Server 2008 R2 SP1 或更高版本。 本部分中的所有步骤均使用 Windows Server 2016 执行。

### <a name="network-policy-and-access-services-nps-role"></a>网络策略和访问服务 (NPS) 角色

NPS 角色服务提供 RADIUS 服务器和客户端功能，以及网络访问策略健康服务。 必须在基础设施中的至少两台计算机上安装此角色：远程桌面网关与另一台成员服务器或域控制器。 默认情况下，此角色已经存在于配置为远程桌面网关的计算机上。  你还必须至少在另一台计算机上安装 NPS 角色，例如域控制器或成员服务器。

有关安装 NPS 角色服务 Windows Server 2012或更早版本的信息，请参阅[安装 NAP 健康策略服务器](https://technet.microsoft.com/library/dd296890.aspx)。 有关 NPS 最佳做法的说明，包括在域控制器上安装 NPS 的建议，请参阅 [NPS 最佳做法](https://technet.microsoft.com/library/cc771746)。

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>将 Azure Active Directory 与本地 Active Directory 同步

要使用 NPS 扩展，本地用户必须与 Azure AD 同步并启用 MFA。 本部分内容假设内部部署用户使用 AD Connect 与 Azure AD 同步。 有关 Azure AD 连接的信息，请参阅[将本地目录与 Azure Active Directory 进行集成](../hybrid/whatis-hybrid-identity.md)。

### <a name="azure-active-directory-guid-id"></a>Azure Active Directory GUID ID

若要安装 NPS 扩展，需要知道 Azure AD 的 GUID。 下面提供了用于查找 Azure AD 的 GUID 的说明。

## <a name="configure-multi-factor-authentication"></a>配置多重身份验证

本部分说明如何将 Azure MFA 与远程桌面网关进行集成。 作为管理员，你必须先配置 Azure MFA 服务，然后用户才能自行注册其多重设备或应用程序。

按照[云中的 Azure 多重身份验证入门](howto-mfa-getstarted.md)中的步骤，为 Azure AD 用户启用 MFA。

### <a name="configure-accounts-for-two-step-verification"></a>配置帐户进行双重验证

为 MFA 启用帐户后，在成功配置用于二次身份验证的受信任的设备并且使用双重验证进行身份验证前，你将无法登录 MFA 策略管理的资源。

按照[Azure 多重身份验证对我而言有什么用途？](../user-help/multi-factor-authentication-end-user.md)中的步骤，使用你的用户帐户了解并正确配置 MFA 的设备。

## <a name="install-and-configure-nps-extension"></a>安装和配置 NPS 扩展

本部分将说明如何配置 RDS 基础结构，以通过远程桌面网关使用 Azure MFA 进行客户端身份验证。

### <a name="acquire-azure-active-directory-guid-id"></a>获得 Azure Active Directory GUID ID

作为 NPS 扩展配置的一部分，你需要为 Azure AD 租户提供管理员凭据和 Azure AD ID。 以下步骤演示如何获取租户 ID。

1. 以 Azure 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航栏中，选择“Azure Active Directory”图标。
1. 选择“属性”。
1. 在“属性”边栏选项卡的“目录ID”旁边，单击“复制”图标，如下所示，将 ID 复制到剪贴板。

   ![正在从 Azure 门户获取目录 ID](./media/howto-mfa-nps-extension-rdg/image1.png)

### <a name="install-the-nps-extension"></a>安装 NPS 扩展

在安装了网络策略和访问服务 (NPS) 角色的服务器上安装 NPS 扩展。 这作为为你设计的 RADIUS 服务器。

> [!Important]
> 确保你未在远程桌面网关服务器上安装 NPS 扩展。
>

1. 下载 [NPS 扩展](https://aka.ms/npsmfa)。
1. 将安装程序可执行文件 (NpsExtnForAzureMfaInstaller.exe) 复制到 NPS 服务器。
1. 在 NPS 服务器上，双击“NpsExtnForAzureMfaInstaller.exe”。 系统提示后，单击“运行”。
1. 在“适用于 Azure MFA 设置的 NPS 扩展”对话框中，查看软件许可条款，勾选“我同意许可条款和条件”，然后单击“安装”。
1. 在“适用于 Azure MFA 设置的 NPS 扩展”对话框中，单击“关闭”。

### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>使用 PowerShell 脚本配置用于 NPS 扩展的证书

接下来，你需要配置供 NPS 扩展使用的证书，以确保安全通信并提供保证。 NPS 组件包括配置自签名证书以用于 NPS 的 Windows PowerShell 脚本。

此脚本可执行以下操作：

* 创建自签名证书
* 将证书的公钥关联到 Azure AD 上的服务主体
* 将证书存储在本地计算机存储中
* 向网络用户授予对证书私钥的访问权限
* 重新启动网络策略服务器服务

如果要使用自己的证书，则需要将证书的公钥关联到 Azure AD 上的服务主体等等。

要使用该脚本，请提供 Azure AD 管理员凭据和之前复制的 Azure AD 租户 ID 的扩展。 在安装 NPS 扩展的每个 NPS 服务器上运行脚本。 然后执行以下操作：

1. 打开管理 Windows PowerShell 提示符。
1. 在 PowerShell 命令提示符处，键入 `cd ‘c:\Program Files\Microsoft\AzureMfa\Config’` 并按 ENTER。
1. 键入 `.\AzureMfaNpsExtnConfigSetup.ps1`，然后按 ENTER。 该脚本将检查是否已安装 Azure Active Directory PowerShell 模块。 如果未安装，该脚本将为你安装此模块。

   ![在 Azure AD PowerShell 中运行 AzureMfaNpsExtnConfigSetup](./media/howto-mfa-nps-extension-rdg/image4.png)
  
1. 脚本验证 PowerShell 模块的安装情况后，它将显示 Azure Active Directory PowerShell 模块对话框。 在对话框中，输入 Azure AD 管理员凭据和密码，然后单击“登录”。

   ![在 PowerShell 中对 Azure AD 进行身份验证](./media/howto-mfa-nps-extension-rdg/image5.png)

1. 出现提示时，请先将复制的目录 ID 粘贴到剪贴板，然后按**enter**。

   ![在 PowerShell 中输入目录 ID](./media/howto-mfa-nps-extension-rdg/image6.png)

1. 此脚本创建一个自签名证书并执行其他配置更改。 输出应如下图所示。

   ![显示自签名证书的 PowerShell 输出](./media/howto-mfa-nps-extension-rdg/image7.png)

## <a name="configure-nps-components-on-remote-desktop-gateway"></a>在远程桌面网关配置 NPS 组件

在本节中，你将配置远程桌面网关连接授权策略和其他 RADIUS 设置。

身份验证流需要在安装了 NPS 扩展的远程桌面网关和 NPS 服务器之间交换 RADIUS 消息。 这意味着你必须在远程桌面网关和安装了 NPS 扩展的 NPS 服务器上配置 RADIUS 客户端设置。

### <a name="configure-remote-desktop-gateway-connection-authorization-policies-to-use-central-store"></a>配置远程桌面网关连接授权策略以使用中央存储

远程桌面连接授权策略 (RD CAP) 指定连接到远程桌面网关服务器的要求。 可以本地存储 RD CAP（默认），也可以存储在运行 NPS 的中央 RD CAP 存储中。 要配置 Azure MFA 与 RDS 的集成，需要指定使用中央存储。

1. 在 RD 网关服务器上，打开“服务器管理器”。
1. 在此菜单上，单击“工具”，指向“远程桌面服务”，然后单击“远程桌面网关管理器”。
1. 在 RD 网关管理器中，右键单击 **\[\]** “服务器名称(本地)”，然后单击“属性”。
1. 在“属性”对话框中，选择“RD CAP 存储”选项卡。
1. 在“RD CAP 存储”选项卡上，选择“运行 NPS 的中心服务器”。 
1. 在“输入正在运行 NPS 的服务器的名称或 IP 地址”字段中，键入安装了 NPS 扩展的服务器的 IP 地址或服务器名称。

   ![输入 NPS 服务器的名称或 IP 地址](./media/howto-mfa-nps-extension-rdg/image10.png)
  
1. 单击“添加”。
1. 在“共享密钥”对话框中，输入一个共享密钥，然后单击“确定”。 确保记录此共享密钥并安全地存储该记录。

   >[!NOTE]
   >共享密钥用于在 RADIUS 服务器和客户端之间建立信任关系。 创建长且复杂的密码。
   >

   ![创建共享机密来建立信任](./media/howto-mfa-nps-extension-rdg/image11.png)

1. 单击“确定”以关闭该对话框。

### <a name="configure-radius-timeout-value-on-remote-desktop-gateway-nps"></a>在远程桌面网关 NPS 上配置 RADIUS 超时值

为确保有时间验证用户凭据、执行双重验证、接收响应和响应 RADIUS 消息，必须调整 RADIUS 超时值。

1. 在 RD 网关服务器上，打开“服务器管理器”。 在菜单中，单击“工具”，然后单击“网络策略服务器”。
1. 在“NPS (本地)”控制台中，展开“RADIUS 客户端和服务器”，然后选择“远程 RADIUS 服务器”。

   ![显示远程 RADIUS 服务器的网络策略服务器管理控制台](./media/howto-mfa-nps-extension-rdg/image12.png)

1. 在详细信息窗格中，双击“TS 网关服务器组”。

   >[!NOTE]
   >当为 NPS 策略配置中央服务器时，创建了此 RADIUS 服务器组。 RD 网关将 RADIUS 消息转发到该服务器或服务器组（如果组中有多台服务器）。
   >

1. 在“TS 网关服务器组属性”对话框中，选择配置为存储 RD CAP 的 NPS 服务器的 IP 地址或名称，然后单击“编辑”。

   ![选择前面配置的 NPS 服务器的 IP 或名称](./media/howto-mfa-nps-extension-rdg/image13.png)

1. 在“编辑 RADIUS 服务器”对话框中，选择“负载平衡”选项卡。
1. 在“负载平衡”选项卡的“考虑放弃请求之前的不响应秒数”字段中，将默认值从 3 更改为 30 到 60 秒之间的值。
1. 在“将服务器标识为不可用时请求之间的秒数”字段中，将默认值 30 秒更改为等于或大于上一步中指定的值。

   ![在 "负载平衡" 选项卡上编辑 Radius 服务器超时设置](./media/howto-mfa-nps-extension-rdg/image14.png)

1. 单击“确定”两次，以关闭该对话框。

### <a name="verify-connection-request-policies"></a>验证连接请求策略

默认情况下，当将 RD 网关配置为使用中央策略存储进行授权策略连接时，RD 网关配置为将 CAP 请求转发到 NPS 服务器。 安装了 Azure MFA 扩展的 NPS 服务器处理 RADIUS 访问请求。 以下步骤显示如何验证默认连接请求策略。

1. 在 RD 网关的“NPS (本地)”控制台中，展开“策略”，然后选择“连接请求策略”。
1. 双击“TS 网关授权策略”。
1. 在“TS 网关授权策略”属性对话框中，单击“设置”选项卡。
1. 在“设置”选项卡的“转发连接请求”下，单击“身份验证”。 RADIUS 客户端配置为转发请求进行身份验证。

   ![配置指定服务器组的身份验证设置](./media/howto-mfa-nps-extension-rdg/image15.png)

1. 单击“取消”。

## <a name="configure-nps-on-the-server-where-the-nps-extension-is-installed"></a>在安装了 NPS 扩展的服务器上配置 NPS

安装了 NPS 扩展的 NPS 服务器需要能够与远程桌面网关上的 NPS 服务器交换 RADIUS 消息。 要启用此消息交换，需要在安装了 NPS 扩展服务的服务器上配置 NPS 组件。

### <a name="register-server-in-active-directory"></a>在 Active Directory 中注册服务器

要在这种情况下正常工作，需要在 Active Directory 中注册 NPS 服务器。

1. 在 NPS 服务器上，打开“服务器管理器”。
1. 在“服务器管理器”中，单击“工具”，然后单击“网络策略服务器”。
1. 在“网络策略服务器”控制台中，右键单击“NPS(本地)”，然后单击“在 Active Directory 中注册服务器”。
1. 单击“确定”两次。

   ![在 Active Directory 中注册 NPS 服务器](./media/howto-mfa-nps-extension-rdg/image16.png)

1. 使控制台保持打开状态以进行下一个过程。

### <a name="create-and-configure-radius-client"></a>创建和配置 RADIUS 客户端

远程桌面网关需要配置为 NPS 服务器的 RADIUS 客户端。

1. 在安装了 NPS 扩展的 NPS 服务器上，在“ NPS (本地)”控制台中，右键单击“RADIUS 客户端”，然后单击“新建”。

   ![在 NPS 控制台中创建新的 RADIUS 客户端](./media/howto-mfa-nps-extension-rdg/image17.png)

1. 在“新建 RADIUS 客户端”对话框中，提供一个友好名称，如“网关”，以及远程桌面网关服务器的 IP 地址或 DNS 名称。
1. 在“共享密钥”和“确认共享密钥”字段中，输入与之前使用的相同的密码。

   ![配置友好名称和 IP 或 DNS 地址](./media/howto-mfa-nps-extension-rdg/image18.png)

1. 单击“确定”，以关闭“新建 RADIUS 客户端”对话框。

### <a name="configure-network-policy"></a>配置网络策略

撤回 NPS 服务器与 Azure MFA 扩展是连接授权策略 (CAP) 的指定中央策略存储。 因此，需要在 NPS 服务器上实现 CAP，以授权有效的连接请求。  

1. 在 NPS 服务器上，打开“NPS (本地)”控制台，展开“策略”，然后单击“网络策略”。
1. 右键单击“到其他访问服务器的连接”，然后单击“重复策略”。

   ![复制与其他访问服务器策略的连接](./media/howto-mfa-nps-extension-rdg/image19.png)

1. 右键单击“复制到其他访问服务器的连接”，然后单击“属性”。
1. 在“复制到其他访问服务器的连接”对话框中，在“策略名称”中输入合适的名称，如“RDG_CAP”。 勾选“策略已启用”，然后选择“授权访问”。 （可选）在“网络访问服务器类型”中，选择“远程桌面网关”，也可以将其保留为“未指定”。

   ![命名策略，启用并授予访问权限](./media/howto-mfa-nps-extension-rdg/image21.png)

1. 单击“约束”选项卡，然后选中“允许客户端连接时不必协商身份验证方法”。

   ![修改身份验证方法，以允许客户端连接](./media/howto-mfa-nps-extension-rdg/image22.png)

1. （可选）单击“条件”选项卡，并添加授权连接必须满足的条件，例如特定 Windows 组中的成员资格。

   ![选择性地指定连接条件](./media/howto-mfa-nps-extension-rdg/image23.png)

1. 单击 **“确定”** 。 当系统提示查看相应帮助主题时，请单击“否”。
1. 确保新策略位于列表的顶部、该策略已启用，并且它授予访问权限。

   ![将策略移至列表顶部](./media/howto-mfa-nps-extension-rdg/image24.png)

## <a name="verify-configuration"></a>验证配置

若要验证配置，需要使用合适的 RDP 客户端登录远程桌面网关。 确保使用连接授权策略允许的帐户，并为 Azure MFA 启用了此帐户。

如下图所示，你可以使用“远程桌面 Web 访问”页面。

![在远程桌面 Web 访问中测试](./media/howto-mfa-nps-extension-rdg/image25.png)

成功输入凭证进行主要身份验证后，“远程桌面连接”对话框将显示“正在启动远程连接”的状态，如下所示。 

如果使用先前在 Azure MFA 中配置的辅助身份验证方法成功进行身份验证，则将连接到此资源。 但是，如果辅助身份验证不成功，你将无法访问资源。 

![远程桌面连接启动远程连接](./media/howto-mfa-nps-extension-rdg/image26.png)

在下面的示例中，Windows 手机上的 Authenticator 应用用于提供辅助身份验证。

![显示验证的 Windows Phone 验证器应用示例](./media/howto-mfa-nps-extension-rdg/image27.png)

使用辅助身份验证方法成功进行身份验证后，你就可以正常登录到远程桌面网关。 但是，由于你需要在受信任的设备上使用移动应用的辅助身份验证方法，因此，登录过程比其他情况更安全。

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>查看事件查看器日志以了解成功登录事件

要查看 Windows 事件查看器日志中的成功登录事件，可以发出以下 Windows PowerShell 命令来查询 Windows 终端服务和 Windows 安全日志。

要在网关操作日志（Event Viewer\应用程序和服务日志\Microsoft\Windows\TerminalServices-Gateway\Operational）中查询成功登录事件，请使用以下 PowerShell 命令：

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '300'} | FL`
* 此命令显示 Windows 事件，这些事件可显示符合资源授权策略要求 (RD RAP) 的用户并向其授予访问权限。

![使用 PowerShell 查看事件](./media/howto-mfa-nps-extension-rdg/image28.png)

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '200'} | FL`
* 此命令显示可展示用户满足连接授权策略要求时间的事件。

![使用 PowerShell 查看连接授权策略](./media/howto-mfa-nps-extension-rdg/image29.png)

另外，你还可以查看此日志并按事件 ID、300 和 200 进行筛选。 要在安全性事件查看器日志中查询成功登录事件，请使用以下命令：

* `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`
* 此命令可以在中央 NPS 或 RD 网关服务器上运行。

![成功登录事件示例](./media/howto-mfa-nps-extension-rdg/image30.png)

此外，你还可以查看“安全日志”或“网络策略和访问服务”自定义视图，如下所示：

![网络策略和访问服务事件查看器](./media/howto-mfa-nps-extension-rdg/image31.png)

在安装了适用于 Azure MFA 的 NPS 扩展的服务器上，可以在 Application and Services Logs\Microsoft\AzureMfa 位置找到特定于此扩展的事件查看器应用程序日志。

![事件查看器 AuthZ 应用程序日志](./media/howto-mfa-nps-extension-rdg/image32.png)

## <a name="troubleshoot-guide"></a>故障排除指南

如果配置无法正常工作，首先要排查的问题是验证是否将用户配置为使用 Azure MFA。 将用户连接到 [Azure 门户](https://portal.azure.com)。 如果提示用户进行辅助身份验证并且身份验证成功，则可以消除 Azure MFA 的不正确配置。

如果用户的 Azure MFA 正常工作，则应查看相关的事件日志。 其中包括上一节中讨论的安全事件、网关操作和 Azure MFA 日志。

以下是安全日志的输出示例，显示失败的登录事件（事件 ID 6273）。

![失败登录事件的示例](./media/howto-mfa-nps-extension-rdg/image33.png)

下面是 AzureMFA 日志中的相关事件：

![示例 Azure MFA 日志事件查看器](./media/howto-mfa-nps-extension-rdg/image34.png)

要执行高级故障排除选项，请参阅安装了 NPS 服务的 NPS 数据库格式日志文件。 这些日志文件以逗号分隔的文本文件形式在 %SystemRoot%\System32\Logs 文件夹中创建。

有关这些日志文件的说明，请参阅[解释 NPS 数据库格式日志文件](https://technet.microsoft.com/library/cc771748.aspx)。 如果不将这些日志文件中的条目导入电子表格或数据库，可能难以解释这些条目。 你可以在线查找几个 IAS 分析程序，以帮助你解释日志文件。

下图显示了一个可下载的[共享性应用程序](https://www.deepsoftware.com/iasviewer)的输出。

![示例共享应用程序 IAS 分析器](./media/howto-mfa-nps-extension-rdg/image35.png)

最后，对于其他故障排除选项，你可以使用协议分析器，例如 [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx)。

来自 Microsoft Message Analyzer 的以下图像显示了根据包含用户名 **CONTOSO\AliceC** 的 RADIUS 协议进行筛选的网络流量。

![显示筛选的流量的 Microsoft Message Analyzer](./media/howto-mfa-nps-extension-rdg/image36.png)

## <a name="next-steps"></a>后续步骤

[如何获取 Azure 多重身份验证](concept-mfa-licensing.md)

[使用 RADIUS 的远程桌面网关和 Azure 多重身份验证服务器](howto-mfaserver-nps-rdg.md)

[将本地目录与 Azure Active Directory 进行集成](../hybrid/whatis-hybrid-identity.md)
