---
title: include 文件
description: include 文件
services: active-directory
author: rolyon
ms.service: active-directory
ms.topic: include
ms.date: 07/31/2019
ms.author: rolyon
ms.custom: include file
ms.openlocfilehash: 154d71c9cbc109834a5854b46c3e6584dcefa7eb
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68968829"
---
### <a name="policy-for-users-in-your-directory"></a>策略:适用于目录中的用户

如果你希望在你的目录中为可以请求此访问包的用户提供策略, 请执行以下步骤。  **目录中的用户**是指内部用户和外部用户, 这些用户以前已被邀请到目录, 无论是通过他们请求使用其他访问包的权利管理, 还是受 Azure AD B2B 邀请。 定义策略时, 可以指定单个用户或更常见的用户组。 例如, 你的组织可能已有一个组, 例如 "**所有员工**"。  如果为可请求访问的用户在策略中添加该组, 则该组的任何成员都可以请求访问。

1. 在 "**可请求访问的用户**" 部分中,**为目录中的用户**选择。

    请注意, "目录" 设置中的 "用户" 包含已添加到目录**中**的成员用户和来宾用户。 如果只想包括成员用户而不包含 guest 用户, 请**在目录中**选择 "用户", 然后选择一个成员用户组。 如有必要, 可以创建成员用户的动态组 (userType-eq "成员")。 有关详细信息, 请参阅[Azure Active Directory 中组的动态成员身份规则](../articles/active-directory/users-groups-roles/groups-dynamic-membership.md)。

1. 在 "**选择用户和组**" 部分中, 单击 "**添加用户和组**"。

1. 在 "选择用户和组" 窗格中, 选择要添加的用户和组。

    ![访问包-策略-选择用户和组](./media/active-directory-entitlement-management-policy/policy-select-users-groups.png)

1. 单击 "**选择**" 添加用户和组。

1. 向下跳到[策略:Request](#policy-request)部分。

### <a name="policy-for-users-not-in-your-directory"></a>策略:对于不在你的目录中的用户

如果你希望在你的目录中的用户不能请求此访问包, 请执行以下步骤。 **不在你的目录中的用户**指的是另一个 Azure AD 目录中的用户, 可能尚未邀请到你的目录。 目前只能从组织中添加具有 Azure AD 的用户。 必须将目录配置为允许在**组织关系协作限制**设置中使用。

> [!NOTE]
> 将为你的请求已获批准或自动批准的用户创建来宾外部用户帐户。 将邀请来宾, 但不会收到邀请电子邮件。 相反, 在传递其访问包分配时, 他们将收到一封电子邮件。 默认情况下, 当来宾用户不再具有任何访问包分配时, 因为他们的上次分配已过期或已被取消, 则将阻止该来宾用户帐户登录并随后将其删除。 如果希望来宾用户无限期地保留在目录中, 你可以更改你的授权管理配置的设置。

1. 在 "**可请求访问的用户**" 部分中, 选择 "**对于不在你的目录中的用户**"。

1. 在 "**选择外部 Azure AD 目录**" 部分中, 单击 "**添加目录**"。

1. 输入域名, 并使用该域名搜索 Azure AD 目录。

1. 按照提供的目录名称和初始域验证它是否是正确的目录。

    > [!NOTE]
    > 目录中的所有用户都能够请求此访问包。 这包括与目录关联的所有子域中的用户, 而不仅仅是搜索中使用的域。

    ![访问包-策略-选择目录](./media/active-directory-entitlement-management-policy/policy-select-directories.png)

1. 单击 "**添加**" 以添加目录。

1. 重复此步骤以添加更多目录。

1. 添加要包括在策略中的所有目录后, 请单击 "**选择**"。

1. 向下跳到[策略:Request](#policy-request)部分。

### <a name="policy-none-administrator-direct-assignments-only"></a>策略:无 (仅限管理员直接分配)

如果希望策略绕过访问请求并允许管理员直接将特定用户分配到访问包, 请执行以下步骤。 用户无需请求访问包。 你仍可以设置过期设置, 但没有请求设置。

1. 在 "**可请求访问的用户**" 部分中, 选择 "**无 (仅限管理员直接分配**"。

    创建访问包后, 可以直接将特定的内部和外部用户分配到访问包。 如果指定外部用户, 则将在目录中创建来宾用户帐户。

1. 向下跳到[策略:过期](#policy-expiration)时间部分。

### <a name="policy-request"></a>策略:请求

在 "请求" 部分中, 在用户请求访问包时指定批准设置。

1. 若要要求从所选用户批准请求, 请将 "**需要审批**" 切换设置为 **"是"** 。 若要自动批准请求, 请将切换设置为 "**否**"。

1. 如果需要批准, 请在 "**选择审批者**" 部分中单击 "**添加审批者**"。

1. 在 "选择审批者" 窗格中, 选择一个或多个要成为审批者的用户和/或组。

    只有一个选定的审批者需要批准请求。 不需要所有审批者的批准。 批准决策基于每个审批者首先查看请求。

    ![访问包-策略-选择审批者](./media/active-directory-entitlement-management-policy/policy-select-approvers.png)

1. 单击 "**选择**" 添加审批者。

1. 单击 "**显示高级请求设置**" 以显示其他设置。

    ![访问包-策略-选择目录](./media/active-directory-entitlement-management-policy/policy-advanced-request.png)

1. 若要要求用户提供要求访问包的理由, 请将 "**要求理由**" 设置为 **"是"** 。

1. 若要要求审批者提供批准访问包请求的理由, 请将 "**需要审批者理由**" 设置为 **"是"** 。

1. 在 "**批准请求超时 (天)** " 框中, 指定审批者必须查看请求的时间长度。 如果没有任何审批者在此天数内查看此请求, 则请求将过期, 并且用户必须提交访问包的另一个请求。

### <a name="policy-expiration"></a>策略:有效期限

在 "过期" 部分中, 指定用户对访问包的分配何时过期。

1. 在 "**过期**" 部分中, 将 "**访问包过期**时间" 设置为 **"日期**"、"**天**" 或 "**从不**"。

    对于 "**日期**", 选择将来的到期日期。

    对于 "**天**", 请指定一个介于0到3660天之间的数字。

    根据你的选择, 对访问包的用户分配将在特定日期过期, 在批准后特定天数过期, 或从不过期。

1. 单击 "**显示高级过期设置**" 以显示其他设置。

1. 若要允许用户扩展其分配, 请将 "**允许用户扩展" 权限**设置为 **"是"** 。

    如果策略中允许使用扩展, 则用户将收到电子邮件14天, 并且在访问包分配设置为过期之前的1天内, 会提示他们扩展分配。

    ![访问包-策略过期设置](./media/active-directory-entitlement-management-policy/policy-expiration.png)

### <a name="policy-enable-policy"></a>策略:启用策略

1. 如果希望访问包立即可用于策略中的用户, 请单击 **"是"** 以启用策略。

    在完成创建访问包后, 你始终可以启用它。

    ![访问包-策略-启用策略设置](./media/active-directory-entitlement-management-policy/policy-enable.png)

1. 单击 "**下一步**" 或 "**创建**"。
