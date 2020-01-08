---
title: Azure Active Directory Connect Health 常见问题 - Azure | Microsoft 文档
description: 此常见问题回答了关于 Azure AD Connect Health 的问题。 其中涵盖了服务使用方面的问题，包括计费模式、功能、限制和支持。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: f1b851aa-54d7-4cb4-8f5c-60680e2ce866
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec88caafa9a6168860a8e9e2ff9e2abe0cfd0e77
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "62096108"
---
# <a name="azure-ad-connect-health-frequently-asked-questions"></a>Azure AD Connect Health 常见问题
本文提供有关 Azure Active Directory (Azure AD) Connect Health 的常见问题 (FAQ) 解答。 这些常见问题涉及到服务使用方法，包括计费模式、功能、限制和支持。

## <a name="general-questions"></a>一般问题
**问：我要管理多个 Azure AD 目录。如何切换到包含 Azure Active Directory Premium 的 Azure AD 目录？**

要切换 Azure AD 租户，可以在右上角选择当前登录的**用户名**，并选择相应的帐户。 如果此处未列出该帐户，可选择“注销”  ，并使用已启用 Azure Active Directory Premium 的目录的全局管理员凭据来登录。

**问：Azure AD Connect Health 支持哪个版本的标识角色？**

下表列出了角色，以及支持的操作系统版本。

|角色| 操作系统/版本|
|--|--|
|Active Directory 联合身份验证服务 (AD FS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|
|具有 Azure AD Connect | 版本 1.0.9125 或更高版本|
|Active Directory 域服务 (AD DS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|

请注意，该服务提供的功能可能因角色和操作系统而有所不同。 换言之，并非所有功能都适用于所有操作系统版本。 有关详细信息，请参阅功能说明。

**问：需要多少许可证来监视我的基础结构？**

* 第一个 Connect Health 代理至少需要一个 Azure AD Premium 许可证。
* 每个其他注册代理需要 25 个额外的 Azure AD Premium 许可证。
* 代理计数等于在所有受监视角色（AD FS、Azure AD Connect 和/或 AD DS）中注册的总代理数。
* AAD 连接运行状况许可不需要将许可证分配给特定用户。 只需拥有必要数量的有效许可证。

许可信息还可在 [Azure AD 定价页](https://aka.ms/aadpricing)中找到。

示例：

| 注册的代理数 | 所需的许可证数 | 示例监视配置 |
| ------ | --------------- | --- |
| 第 | 第 | 1 个 Azure AD Connect 服务器 |
| 2 | 26| 1 个 Azure AD Connect 服务器和 1 个域控制器 |
| 3 | 51 | 1 个 Active Directory 联合身份验证服务 (AD FS) 服务器、1 个 AD FS 代理和 1 个域控制器 |
| 4 | 76 | 1 个 AD FS 服务器、1 个 AD FS 代理和 2 个域控制器 |
| 5 | 101 | 1 个 Azure AD Connect 服务器、1 个 AD FS 服务器、1 个 AD FS 代理和 2 个域控制器 |

**问：Azure AD Connect Health 是否支持 Azure 德国云？**

Azure AD Connect Health 不受德国云支持，但[同步错误报告功能](how-to-connect-health-sync.md#object-level-synchronization-error-report)除外。

| 角色 | 功能 | 德国云支持 |
| ------ | --------------- | --- |
| 适用于同步的 Connect Health | 监视/见解/警报/分析 | 否 |
|  | 同步错误报告 | 是 |
| 适用于 ADFS 的 Connect Health | 监视/见解/警报/分析 | 否 |
| 适用于 ADDS 的 Connect Health | 监视/见解/警报/分析 | 否 |

若要确保适用于同步的 Connect Health 的代理连接，请相应地配置[安装要求](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints)。

## <a name="installation-questions"></a>安装问题

**问：在单个服务器上安装 Azure AD Connect Health 代理有何影响？**

安装 Microsoft Azure AD Connect Health 代理、AD FS、Web 应用程序代理服务器、Azure AD Connect（同步）服务器、域控制器对 CPU、内存消耗、网络带宽和存储的影响很小。

以下数字是近似值：

* CPU 占用率：增加约 1-5%。
* 内存消耗：最多为系统总内存的 10%。

> [!NOTE]
> 如果代理无法与 Azure 通信，代理会将定义的最大限制的数据存储在本地。 代理会根据“最近获得的服务最少”这一标准覆盖“缓存的”数据。
>
>

* Azure AD Connect Health 代理的本地缓冲区存储：约 20 MB。
* 对于 AD FS 服务器，建议预配一个 1,024 MB (1 GB) 大小的磁盘空间，方便 Azure AD Connect Health 代理的 AD FS 审核通道在所有审核数据被覆盖之前将其处理掉。

**问：在 Azure AD Connect Health 代理安装期间，是否必须重启我的服务器？**

不。 安装代理时不需要重新启动服务器。 但是，安装某些先决条件步骤可能需要重新启动服务器。

例如，在 Windows Server 2008 R2 上安装 .NET 4.5 Framework 需要重新启动服务器。

**问：Azure AD Connect Health 是否通过直通型 HTTP 代理进行工作？**

是的。 对于正在进行的操作，可以将 Health 代理配置为使用 HTTP 代理转发出站 HTTP 请求。
有关详细信息，请阅读[为 Health 代理配置 HTTP 代理](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)。

如果要在代理注册过程中配置代理，可能需要事先修改 Internet Explorer 代理设置。

1. 打开“Internet Explorer”>“设置”   > “Internet 选项”   > “连接”   > “LAN 设置”  。
2. 选择“为 LAN 使用代理服务器”  。
3. 如果针对 HTTP 和 HTTPS/Secure 设置不同的代理端口，请选择“高级”  。

**问：Azure AD Connect Health 在连接到 HTTP 代理时是否支持基本身份验证？**

不。 目前不支持指定任意用户名和密码进行基本身份验证的机制。

**问：若要确保 Azure AD Connect Health 代理正常使用，需要打开哪些防火墙端口？**

有关防火墙端口列表和其他连接要求的信息，请参阅[要求部分](how-to-connect-health-agent-install.md#requirements)。

**问：Azure AD Connect Health 门户中为何有两个同名的服务器？**

从某个服务器中删除代理时，该服务器不会自动从 Azure AD Connect Health 门户中删除。 如果手动从服务器中删除代理或删除服务器本身，需要从 Azure AD Connect Health 门户中手动删除该服务器条目。

可能会使用相同的详细信息（例如计算机名称）重置服务器的映像或创建新服务器。 如果没有从 Azure AD Connect Health 门户中删除已注册服务器，而是在新服务器上安装了代理，则会看到两个服务器条目具有同一名称。

在这种情况下，请手动删除属于较旧服务器的条目。 此服务器的数据会过期。

## <a name="health-agent-registration-and-data-freshness"></a>Health 代理注册和数据刷新

**问：Health 代理注册故障的常见原因有哪些，如何排查问题？**

由于下列可能的原因，Health 代理可能无法注册：

* 该代理无法与所需的终结点通信，因为防火墙阻止流量。 这在 Web 应用程序代理服务器上尤其常见。 请确保已允许出站通信到所需终结点和端口。 有关详细信息，请参阅[要求部分](how-to-connect-health-agent-install.md#requirements)。
* 网络层会对出站通信进行 SSL 检查。 这会导致代理使用的证书被检查服务器/实体替换，并且无法执行完成代理注册所需的步骤。
* 用户没有执行代理注册的访问权限。 默认情况下，全局管理员具有访问权限。 可使用[基于角色的访问控制](how-to-connect-health-operations.md#manage-access-with-role-based-access-control)将访问权限委派给其他用户。

**问：我收到有关“Health 服务数据不是最新”的警报。如何解决此问题？**

如果过去两小时内未从服务器收到所有数据点，Azure AD Connect Health 将生成此警报。 [了解详细信息](how-to-connect-health-data-freshness.md)。

## <a name="operations-questions"></a>操作问题
**问：我需要在 Web 应用程序代理服务器上启用审核吗？**

否，不需在 Web 应用程序代理服务器上启用审核。

**问：如何解除 Azure AD Connect Health 警报？**

在成功的情况下，将解除 Azure AD Connect Health 警报。 Azure AD Connect Health 代理将定期检测并向服务报告成功的情况。 某些警报的解除取决于时间。 换句话说，如果在警报生成后的 72 小时内未观察到相同的错误条件，则警报会自动解除。

**问：我收到警报显示：“测试身份验证请求(合成事务)无法获取令牌。”如何解决此问题？**

当 AD FS 服务器上安装的 Health 代理无法获取由 Health 代理启动的作为综合事务一部分的令牌时，AD FS 的 Azure AD Connect Health 就会生成此警报。 Health 代理使用本地系统上下文，并尝试为自信赖方获取令牌。 这是一个全面测试，可确保 AD FS 处于发出令牌的状态。

此测试通常会失败，因为 Health 代理无法解析 AD FS 场名称。 如果 AD FS 服务器位于网络负载均衡器之后，并且请求从位于负载均平衡器后面的节点（而不是位于负载均衡器前面的常规客户端）启动，则可能会发生这种情况。 这可以通过更新位于“C:\Windows\System32\drivers\etc”下的“hosts”文件来修复，以包括 AD FS 服务器的 IP 地址或 AD FS 场名称（如 sts.contoso.com）的环回 IP 地址 (127.0.0.1)。 添加主机文件会使网络调用短路，从而使 Health 代理获得令牌。

**问：我收到一封电子邮件，指示我的计算机不需要进行修正的最新的勒索软件攻击。** 我为什么会收到这个电子邮件？

Azure AD Connect Health 服务会扫描其所监视的所有计算机，以确保已安装所需的修补程序。 如果至少有一台计算机未安装关键修补程序，则会向租户管理员发送此电子邮件。 以下逻辑用于做出此判断。
1. 查找计算机上安装的所有修补程序。
2. 检查定义的列表中是否至少存在一个修补程序。
3. 如果是，则计算机受保护。 如果不是，则计算机有受到攻击的风险。

以下 PowerShell 脚本可用于手动执行此检查。 它可实现上述逻辑。

```powershell
Function CheckForMS17-010 ()
{
    $hotfixes = "KB3205409", "KB3210720", "KB3210721", "KB3212646", "KB3213986", "KB4012212", "KB4012213", "KB4012214", "KB4012215", "KB4012216", "KB4012217", "KB4012218", "KB4012220", "KB4012598", "KB4012606", "KB4013198", "KB4013389", "KB4013429", "KB4015217", "KB4015438", "KB4015546", "KB4015547", "KB4015548", "KB4015549", "KB4015550", "KB4015551", "KB4015552", "KB4015553", "KB4015554", "KB4016635", "KB4019213", "KB4019214", "KB4019215", "KB4019216", "KB4019263", "KB4019264", "KB4019472", "KB4015221", "KB4019474", "KB4015219", "KB4019473"

    #checks the computer it's run on if any of the listed hotfixes are present
    $hotfix = Get-HotFix -ComputerName $env:computername | Where-Object {$hotfixes -contains $_.HotfixID} | Select-Object -property "HotFixID"

    #confirms whether hotfix is found or not
    if (Get-HotFix | Where-Object {$hotfixes -contains $_.HotfixID})
    {
        "Found HotFix: " + $hotfix.HotFixID
    } else {
        "Didn't Find HotFix"
    }
}

CheckForMS17-010

```

**问：PowerShell cmdlet <i>Get-MsolDirSyncProvisioningError</i> 显示的同步错误为什么比较少？**

<i>Get-MsolDirSyncProvisioningError</i> 只返回 DirSync 预配错误。 除了该类错误以外，Connect Health 门户还会显示其他类型的同步错误，例如导出错误。 这与 Azure AD Connect delta 结果一致。 了解有关 [Azure AD Connect 同步错误](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sync-errors)的详细信息。

**问：为什么未生成 ADFS 审核？**

请使用 PowerShell cmdlet <i>Get-AdfsProperties -AuditLevel</i> 确保审核日志未处于禁用状态。 阅读有关 [ADFS 审核日志](https://docs.microsoft.com/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server#auditing-levels-in-ad-fs-for-windows-server-2016)的更多信息。 请注意，如果有高级审核设置推送到 ADFS 服务器，则通过 auditpol.exe 进行的任何更改都将被覆盖 （即使未配置“已生成应用程序”）。 在这种情况下，请设置本地安全策略来记录“已生成应用程序”失败和成功。

**问：代理证书何时自动续订之前过期？**
代理证书，将会自动续订**6 个月**之前其到期日期。 如果未续订，请确保是稳定的网络连接的代理。 重新启动代理服务或更新到最新版本还可能会解决此问题。


## <a name="related-links"></a>相关链接
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Azure AD Connect Health 代理安装](how-to-connect-health-agent-install.md)
* [Azure AD Connect Health 操作](how-to-connect-health-operations.md)
* [在 AD FS 中使用 Azure AD Connect Health](how-to-connect-health-adfs.md)
* [使用用于同步的 Azure AD Connect Health](how-to-connect-health-sync.md)
* [在 AD DS 中使用 Azure AD Connect Health](how-to-connect-health-adds.md)
* [Azure AD Connect Health 版本历史记录](reference-connect-health-version-history.md)
