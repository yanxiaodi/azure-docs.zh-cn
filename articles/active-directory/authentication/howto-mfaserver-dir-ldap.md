---
title: LDAP 身份验证和 Azure MFA 服务器-Azure Active Directory
description: 部署 LDAP 身份验证和 Azure 多重身份验证服务器。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc67fb216451bd19fe8cfb327c101aa989228b01
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536919"
---
# <a name="ldap-authentication-and-azure-multi-factor-authentication-server"></a>LDAP 身份验证和 Azure 多重身份验证服务器

默认情况下，Azure 多重身份验证服务器配置为从 Active Directory 导入或同步用户。 但是，可以将它配置为绑定到不同 LDAP 目录，如 ADAM 目录或特定的 Active Directory 域控制器。 通过 LDAP 连接到目录后，Azure 多重身份验证服务器可以充当 LDAP 代理来执行身份验证。 它还允许将 LDAP 绑定用作 RADIUS 目标，使用 IIS 身份验证对用户进行预身份验证，或者在 Azure MFA 用户门户中进行主要身份验证。

若要使用 Azure 多重身份验证作为 LDAP 代理，请在 LDAP 客户端（例如 VPN 设备、应用程序）与 LDAP 目录服务器之间插入 Azure 多重身份验证服务器。 必须将 Azure 多重身份验证服务器配置为同时与客户端服务器和 LDAP 目录进行通信。 在此配置中，Azure 多重身份验证服务器接受来自客户端服务器和应用程序的 LDAP 请求，并将这些请求转发到目标 LDAP 目录服务器以验证主要凭据。 如果 LDAP 目录验证主要凭据有效，Azure 多重身份验证将执行双重身份验证，并将响应发送回 LDAP 客户端。 仅当 LDAP 服务器身份验证和双重验证都成功时，整个身份验证才成功。

> [!IMPORTANT]
> 截至 2019 年 7 月 1 日，Microsoft 将 MFA 服务器不再提供对新的部署。 想要要求从其用户的多重身份验证的新客户应使用基于云的 Azure 多重身份验证。 已激活在 7 月 1 日之前的 MFA 服务器的现有客户将能够下载最新版本，将来的更新并照常生成激活凭据。

## <a name="configure-ldap-authentication"></a>配置 LDAP 身份验证

若要配置 LDAP 身份验证，请在 Windows Server 上安装 Azure 多重身份验证服务器。 请按以下过程操作：

### <a name="add-an-ldap-client"></a>添加 LDAP 客户端

1. 在 Azure 多重身份验证服务器中，选择左侧菜单中的“LDAP 身份验证”图标。
2. 选中“启用 LDAP 身份验证”复选框。 

   ![在 MFA 服务器的 LDAP 身份验证](./media/howto-mfaserver-dir-ldap/ldap2.png)

3. 如果 Azure 多重身份验证 LDAP 服务应绑定到非标准端口来侦听 LDAP 请求，请在“客户端”选项卡上更改 TCP 端口和 SSL 端口。
4. 如果计划从客户端到 Azure 多重身份验证服务器使用 LDAPS，则必须在与 MFA 服务器相同的服务器上安装 SSL 证书。 单击 SSL 证书框旁边的“浏览”  ，并选择要用于安全连接的证书。
5. 单击“添加”  。
6. 在“添加 LDAP 客户端”对话框中，输入向该服务器进行身份验证的设备、服务器或应用程序的 IP 地址以及应用程序名称（可选）。 应用程序名称将出现在 Azure 多重身份验证报告中，并可能会显示在短信或移动应用身份验证消息中。
7. 如果所有用户均已导入或将导入到该服务器并接受双重验证，请选中“需要 Azure 多重身份验证用户匹配”  框。 如果大量用户尚未导入到该服务器并且/或者将免除进行双重验证，请使该框处于未选中状态。 请参阅 MFA 服务器帮助文件，以了解有关此功能的其他信息。

重复上述步骤添加其他 LDAP 客户端。

### <a name="configure-the-ldap-directory-connection"></a>配置 LDAP 目录连接

当 Azure 多重身份验证配置为接收 LDAP 身份验证时，它必须将这些身份验证通过代理发送到 LDAP 目录。 因此，“目标”选项卡只显示一个使用 LDAP 目标的灰显选项。

> [!NOTE]
> 目录集成不保证能够使用 Active Directory 域服务以外的目录。

1. 若要配置 LDAP 目录连接，请单击“目录集成”图标。 
2. 在“设置”选项卡上，选择“使用特定 LDAP 配置”  单选按钮。
3. 选择“编辑...” 
4. 在“编辑 LDAP 配置”对话框中，使用连接到 LDAP 目录时所需的信息填充字段。 Azure 多重身份验证服务器帮助文件中提供了字段说明。

    ![目录集成 LDAP 配置](./media/howto-mfaserver-dir-ldap/ldap.png)

5. 通过单击“测试”  按钮来测试 LDAP 连接。
6. 如果 LDAP 连接测试成功，请单击“确定”  按钮。
7. 单击“筛选器”选项卡。  该服务器已预配置为从 Active Directory 加载容器、安全组和用户。 若要绑定到其他 LDAP 目录，可能需要编辑显示的筛选器。 有关筛选器的详细信息，请单击“帮助”链接。 
8. 单击“属性”选项卡  。该服务器已预配置为从 Active Directory 映射属性。
9. 若要绑定到其他 LDAP 目录或更改预配置的属性映射，请单击“编辑...” 
10. 在“编辑属性”对话框中，修改目录的 LDAP 属性映射。 可以通过单击每个字段旁边的“...”  按钮来键入或选择属性名称。 有关属性的详细信息，请单击“帮助”链接。 
11. 单击“确定”  按钮。
12. 单击“公司设置”图标，并选择“用户名解析”选项卡。  
13. 如果从已加入域的服务器连接到 Active Directory，请将“使用 Windows 安全标识符 (SID) 匹配用户名”单选按钮保持选中状态。  否则，请选中“使用 LDAP 唯一标识符属性匹配用户名”单选按钮。 

“使用 LDAP 唯一标识符属性匹配用户名”单选按钮处于选中状态时，Azure 多重身份验证服务器会尝试将每个用户名解析为 LDAP 目录中的唯一标识符。  将对“目录集成 -> 属性”选项卡中定义的用户名属性执行 LDAP 搜索。当用户进行身份验证时，将用户名解析为 LDAP 目录中的唯一标识符。 该唯一标识符用于匹配 Azure 多重身份验证数据文件中的用户。 这允许不区分大小写的比较以及长、短用户名格式。

完成上述步骤后，MFA 服务器会在配置的端口上侦听来自配置的客户端的 LDAP 访问请求，并充当代理将这些请求发送到 LDAP 目录进行身份验证。

## <a name="configure-ldap-client"></a>配置 LDAP 客户端

若要配置 LDAP 客户端，请遵循以下准则：

* 将设备、服务器或应用程序配置为通过 LDAP 向 Azure 多重身份验证服务器进行身份验证，就如同它是 LDAP 目录一样。 使用通常用于直接连接到 LDAP 目录的设置（服务器名称或 IP 地址除外，应使用 Azure 多重身份验证服务器的服务器名称或 IP 地址）。
* 将 LDAP 超时配置为 30-60 秒，以便有时间使用 LDAP 目录验证用户的凭据、执行双重验证、接收其响应，然后对 LDAP 访问请求做出响应。
* 如果使用 LDAPS，进行 LDAP 查询的设备或服务器必须信任 Azure 多重身份验证服务器上安装的 SSL 证书。
