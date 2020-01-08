---
title: 使用 Azure MFA 与 AD FS 的 Azure Active Directory 保护云资源
description: 这是与 Azure Multi-Factor Authentication 相关的页面，介绍如何将 Azure MFA 与云中的 AD FS 配合使用。
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
ms.openlocfilehash: a5b1838007e1be7fc1d9872516ede14c208b1f57
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67113464"
---
# <a name="securing-cloud-resources-with-azure-multi-factor-authentication-and-ad-fs"></a>将 Azure 多重身份验证与 AD FS 配合使用来保护云资源

如果组织已与 Azure Active Directory 联合，则使用 Azure 多重身份验证或 Active Directory 联合身份验证服务 (AD FS) 来保护通过 Azure AD 访问的资源。 使用以下过程可通过 Azure 多重身份验证或 Active Directory 联合身份验证服务保护 Azure Active Directory 资源。

## <a name="secure-azure-ad-resources-using-ad-fs"></a>使用 AD FS 保护 Azure AD 资源

若要保护云资源，请设置声明规则，以便在用户成功执行双重验证之后，Active Directory 联合身份验证服务能够发出多重身份验证声明。 此声明将传递到 Azure AD。 按照以下过程完成各步骤：

1. 打开“AD FS 管理”。
2. 在左侧选择“信赖方信任”  。
3. 右键单击“Microsoft Office 365 标识平台”  ，然后选择“编辑声明规则”  。

   ![ADFS 控制台中的信赖方信任](./media/howto-mfa-adfs/trustedip1.png)

4. 在“颁发转换规则”上，单击 **添加规则**。

   ![编辑颁发转换规则](./media/howto-mfa-adfs/trustedip2.png)

5. 在“添加转换声明规则向导”上，从下拉列表中选择“传递或筛选传入声明”  ，并单击“下一步”  。

   ![添加转换声明规则向导](./media/howto-mfa-adfs/trustedip3.png)

6. 为规则提供一个名称。 
7. 选择“身份验证方法引用”作为传入声明类型。 
8. 选择“传递所有声明值”  。
    ![添加转换声明规则向导](./media/howto-mfa-adfs/configurewizard.png)
9. 单击 **“完成”** 。 关闭 AD FS 管理控制台。

## <a name="trusted-ips-for-federated-users"></a>联合用户的受信任 IP

受信任的 IP 可让管理员针对特定的 IP 地址或针对从他们自己的 Intranet 发出请求的联合用户，跳过两步验证。 以下部分介绍当请求是来自联合用户的 Intranet 时，如何配置联合用户的 Azure 多重身份验证受信任 IP，以及跳过两步验证。 这是通过将 AD FS 配置为使用“传递或筛选传入声明”模板与“公司网络内部”声明类别来实现的。

此示例使用 Office 365 作为信赖方信任。

### <a name="configure-the-ad-fs-claims-rules"></a>配置 AD FS 声明规则

我们要做的第一件事是配置 AD FS 声明。 创建两个声明规则，一个用于“公司网络内部”声明类型，另一个用于保持用户登录。

1. 打开“AD FS 管理”。
2. 在左侧选择“信赖方信任”  。
3. 右键单击**Microsoft Office 365 标识平台**，然后选择**编辑声明规则...** 
    ![ADFS 控制台-编辑声明规则](./media/howto-mfa-adfs/trustedip1.png)
4. 在颁发转换规则，单击**添加规则。** 
   ![添加声明规则](./media/howto-mfa-adfs/trustedip2.png)
5. 在“添加转换声明规则向导”上，从下拉列表中选择“传递或筛选传入声明”  ，并单击“下一步”  。
   ![添加转换声明规则向导](./media/howto-mfa-adfs/trustedip3.png)
6. 在“声明规则名称”旁边的框中，为规则指定名称。 例如：InsideCorpNet。
7. 从“传入声明类型”旁边的下拉列表中，选择“公司网络内部”  。
   ![公司网络内部添加声明](./media/howto-mfa-adfs/trustedip4.png)
8. 单击 **“完成”** 。
9. 在“颁发转换规则”上，单击“添加规则”  。
10. 在“添加转换声明规则向导”上，从下拉列表中选择“使用自定义规则发送声明”  ，并单击“下一步”  。
11. 在“声明规则名称”下的框中：输入“保持用户登录状态”  。
12. 在“自定义规则”框中，输入：

        c:[Type == "http://schemas.microsoft.com/2014/03/psso"]
            => issue(claim = c);
    ![创建自定义声明，以确保登录的用户](./media/howto-mfa-adfs/trustedip5.png)
13. 单击 **“完成”** 。
14. 单击“应用”  。
15. 单击“确定”  。
16. 关闭“AD FS 管理”。

### <a name="configure-azure-multi-factor-authentication-trusted-ips-with-federated-users"></a>配置联合用户的 Azure 多重身份验证受信任 IP

创建声明后，可以开始配置受信任的 IP。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 选择**Azure Active Directory** > **条件性访问** > **命名位置**。
3. 从**条件访问-命名位置**边栏选项卡，选择**配置 MFA 受信任的 Ip**

   ![Azure AD 条件性访问命名位置配置 MFA 受信任的 Ip](./media/howto-mfa-adfs/trustedip6.png)

4. 在“服务设置”页的“受信任的 IP”下，选择“跳过对于 Intranet 上联合用户的请求的多重身份验证”   。  
5. 单击“保存”  。

就这么简单！ 现在，仅当声明来自公司 Intranet 外部时，Office 365 联合用户才需要使用 MFA。
