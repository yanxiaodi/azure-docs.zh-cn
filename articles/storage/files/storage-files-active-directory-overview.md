---
title: Azure 文件的 SMB Azure Active Directory authentication 概述-Azure 存储
description: Azure 文件通过 SMB (服务器消息块) 通过 Azure Active Directory (Azure AD) 域服务支持基于身份的身份验证。 然后，已加入域的 Windows 虚拟机 (VM) 可使用 Azure AD 凭据访问 Azure 文件共享。
author: roygara
ms.service: storage
ms.topic: article
ms.date: 08/07/2019
ms.author: rogarana
ms.openlocfilehash: 6cdee8f1ad59962822e9e0394547c395c13e4bd8
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69611776"
---
# <a name="overview-of-azure-files-azure-active-directory-domain-service-azure-ad-ds-authentication-support-for-smb-access"></a>Azure 文件概述 Azure Active Directory 域服务 (Azure AD DS) 身份验证支持 SMB 访问
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

若要了解如何为 Azure 文件启用 Azure AD DS 身份验证, 请参阅[通过 SMB 为 Azure 文件启用 Azure Active Directory 域服务身份验证](storage-files-active-directory-enable.md)。

## <a name="glossary"></a>术语表 
了解与 Azure 文件的 SMB 的 Azure AD 域服务身份验证相关的一些关键术语, 这会很有帮助:

-   **Azure Active Directory (Azure AD)**  
    Azure Active Directory (Azure AD) 是 Microsoft 提供的多租户、基于云的目录和标识管理服务。 Azure AD 将核心目录服务、应用程序访问管理和标识保护融入一个解决方案中。 有关详细信息，请参阅[什么是 Azure Active Directory？](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Azure AD 域服务**  
    Azure AD 域服务提供托管域服务，例如域加入、组策略、LDAP、Kerberos/NTLM 身份验证。 这些服务与 Windows Server Active Directory 完全兼容。 有关详细信息，请参阅 [Azure Active Directory (AD) 域服务](../../active-directory-domain-services/overview.md)。

-   **Azure 基于角色的访问控制 (RBAC)**  
    Azure 基于角色的访问控制 (RBAC) 可用于对 Azure 进行细致的访问管理。 使用 RBAC，可通过向用户授予执行其作业所需的最少权限来管理对资源的访问权限。 有关 RBAC 的详细信息，请参阅[什么是 Azure 中基于角色的访问控制 (RBAC)？](../../role-based-access-control/overview.md)

-   **Kerberos 身份验证**

    Kerberos 是一种身份验证协议，用于验证用户或主机的标识。 有关 Kerberos 的详细信息，请参阅 [Kerberos 身份验证概述](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview)。

-  **服务器消息块 (SMB) 协议**  
    SMB 是行业标准网络文件共享协议。 也称为通用 Internet 文件系统或 CIFS。 有关 SMB 的详细信息，请参阅 [Microsoft SMB 协议和 CIFS协议概述](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview)。

## <a name="advantages-of-azure-ad-domain-service-authentication"></a>Azure AD 域服务身份验证的优点
与使用共享密钥身份验证相比, Azure 文件 Azure AD 域服务身份验证具有多项优势:

-   **使用 Azure AD 和 Azure AD 域服务将传统的基于标识的文件共享访问体验扩展到云中**  
    如果你计划将应用程序 "直接迁移" 到云, 并将传统文件服务器替换为 Azure 文件, 则可能希望你的应用程序使用 Azure AD 凭据进行身份验证, 以访问文件数据。 Azure 文件支持使用 Azure AD 凭据从已加入域的 Azure AD DS Windows Vm 中通过 SMB 访问 Azure 文件。 还可选择将所有本地 Active Directory 对象同步到 Azure AD，以保留用户名、密码和其他组分配。

-   **对 Azure 文件共享强制实施粒度访问控制**  
    你可以在共享、目录或文件级别向特定标识授予权限。 例如，假设多个团队使用一个 Azure 文件共享进行项目协作。 你可以向所有团队授予访问非敏感目录的权限，而仅授权财务团队访问含敏感财务数据的目录。 

-   **备份 ACL 以及你的数据**  
    可使用 Azure 文件备份现有的本地文件共享。 将文件共享通过 SMB 备份到 Azure 文件时，Azure 文件会保留 ACL 和你的数据。

## <a name="how-it-works"></a>工作原理
Azure 文件使用 Azure AD 域服务，支持通过已加入域的 VM 中的 Azure AD 凭据进行 Kerberos 身份验证。 在将 Azure AD 用于 Azure 文件之前，必须先启用 Azure AD 域服务，再从计划访问文件数据的 VM 加入域。 已加入域的 VM 必须与 Azure AD 域服务位于同一虚拟网络 (VNET) 中。 

当与 VM 上运行的应用程序相关联的标识尝试访问 Azure 文件中的数据时，该请求将发送到 Azure AD 域服务以验证标识。 如果身份验证成功，Azure AD 域服务会返回一个 Kerberos 令牌。 应用程序发送一个包含该 Kerberos 令牌的请求，Azure 文件使用该令牌对该请求授权​​。 Azure 文件仅接收令牌，不保存 Azure AD 凭据。

![屏幕截图显示了通过 SMB 进行 Azure AD 身份验证的关系图](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-azure-ad-domain-service-authentication-for-smb-access"></a>启用 SMB 访问 Azure AD 域服务身份验证
你可以在2018年9月24日之后创建的新的和现有存储帐户上为 Azure 文件启用 Azure AD 域服务身份验证。 

在启用此功能之前, 请验证是否已为你的存储帐户关联的主 Azure AD 租户部署 Azure AD 域服务。 如果尚未设置 Azure AD 域服务，请按照[使用 Azure 门户启用 Azure Active Directory 域服务](../../active-directory-domain-services/tutorial-create-instance.md)中提供的分步指导进行操作。

部署 Azure AD 域服务通常需要 10 至 15 分钟。 部署 Azure AD 域服务后，即可启用通过 SMB 为 Azure 文件进行 Azure AD 身份验证这一功能。 有关详细信息, 请参阅[启用 Azure 文件的通过 SMB Azure Active Directory 域服务身份验证](storage-files-active-directory-enable.md)。 

### <a name="configure-share-level-permissions-for-azure-files"></a>为 Azure 文件配置共享级别权限
启用 Azure AD 域服务身份验证后, 你可以为 Azure AD 标识配置自定义 RBAC 角色, 并将访问权限分配给存储帐户中的任何文件共享。

在已加入域的 VM 上运行的应用程序尝试装载 Azure 文件共享或者访问目录或文件时，系统会验证应用程序的 Azure AD 凭据以确保共享级别权限和 NTFS 权限正确。 有关配置共享级权限的信息, 请参阅[通过 SMB 启用 Azure Active Directory 域服务身份验证](storage-files-active-directory-enable.md)"。

### <a name="configure-directory--or-file-level-permissions-for-azure-files"></a>为 Azure 文件配置目录级别或文件级别权限 
Azure 文件在目录级别和文件级别（包括根目录）强制实施标准 NTFS 文件权限。 仅支持通过 SMB 配置目录级别或文件级权限。 使用 Windows 文件资源管理器、Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)或[Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl)命令从 VM 装载目标文件共享并配置权限。 

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>使用存储帐户密钥获取超级用户权限 
拥有存储帐户密钥的用户可使用超级用户权限访问 Azure 文件。 超级用户权限超越使用 RBAC 在共享级别配置并由 Azure AD 强制实施的所有访问控制限制。 装载 Azure 文件共享需要超级用户权限。 

> [!IMPORTANT]
> 作为安全最佳做法的一部分，请避免共享存储帐户密钥，并尽可能利用 Azure AD 权限。

### <a name="preserve-directory-and-file-acls-for-data-import-to-azure-file-shares"></a>保留目录和文件 ACL，以便将数据导入 Azure 文件共享
Azure 文件现在支持在将数据复制到 Azure 文件共享时保留目录或文件 Acl。 可以将目录或文件上的 Acl 复制到 Azure 文件。 例如，可使用带 `/copy:s` 标志的 [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) 将数据和 ACL 复制到 Azure 文件共享。 默认情况下启用 ACL 保留, 并且不需要在存储帐户上显式启用 Azure AD 域服务身份验证功能。 

## <a name="pricing"></a>定价
对存储帐户启用通过 SMB 进行 Azure AD 身份验证无需额外的服务费。 有关定价的详细信息，请参阅 [Azure 文件定价](https://azure.microsoft.com/pricing/details/storage/files/)和 [Azure AD 域服务定价](https://azure.microsoft.com/pricing/details/active-directory-ds/)页面。

## <a name="next-steps"></a>后续步骤
有关 Azure 文件和通过 SMB 进行 Azure AD 身份验证的详细信息，请参阅以下资源：

- [Azure 文件简介](storage-files-introduction.md)
- [启用 Azure 文件的通过 SMB 进行 Azure Active Directory 身份验证](storage-files-active-directory-enable.md)
- [常见问题](storage-files-faq.md)
