---
title: 将工作或学校帐户添加到 Microsoft Authenticator 应用 - Azure Active Directory | Microsoft Docs
description: 如何将工作或学校帐户添加到 Microsoft Authenticator 应用以进行双重验证。
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3be2ee662a061cdcb6acc58e47eda5feda3b9eee
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880798"
---
# <a name="add-your-work-or-school-account"></a>添加工作或学校帐户

如果组织使用双重验证，可以设置工作或学校帐户，以使用 Microsoft Authenticator 应用作为验证方法之一。

>[!Important]
>必须先下载并安装 Microsoft Authenticator 应用，然后才能添加帐户。 如果尚未这样做，请按照[下载并安装应用](user-help-auth-app-download-install.md)一文中的步骤操作。

## <a name="add-your-work-or-school-account"></a>添加工作或学校帐户

1. 在计算机上，转到[其他安全性验证](https://aka.ms/mfasetup)页。

    >[!Note]
    >如果看不到“其他安全性验证”页，可能是管理员启用了安全信息（预览）体验。 如果是这种情况，应按照[设置安全信息以使用 Authenticator 应用](security-info-setup-auth-app.md)部分中的说明操作。 否则，请联系组织的技术支持来获得帮助。 有关安全信息的详细信息, 请参阅[安全信息 (预览版) 概述](user-help-security-info-overview.md)。

2. 选中“Authenticator 应用”旁边的复选框，然后选择“配置”。

    此时会显示“配置移动应用”页。

    ![提供 QR 码的屏幕](./media/user-help-auth-app-download-install/auth-app-barcode.png)

3. 打开 Microsoft Authenticator 应用，从右上方的“自定义和控制”图标中选择“添加帐户”，然后选择“工作或学校帐户”。

    >[!Note]
    >如果这是你第一次设置 Microsoft Authenticator 应用程序, 你可能会收到询问是允许应用程序访问你的相机 (iOS) 还是允许应用拍摄图片并录制视频 (Android) 的提示。 你必须选择 "**允许**", 以便验证器应用可以访问你的相机, 以便在下一步中对 QR 代码进行图片。 如果不允许相机, 仍可以设置验证器应用, 但需要手动添加代码信息。 有关如何手动添加代码的信息, 请参阅请参阅[手动将帐户添加到应用](user-help-auth-app-add-account-manual.md)。

4. 在计算机上使用设备的摄像头扫描“配置移动应用”屏幕中的 QR 码，然后选择“完成”。

    >[!Note]
    >如果摄像头无法捕获 QR 码，可以手动将帐户信息添加到 Microsoft Authenticator 应用中，以进行双重验证。 有关详细信息以及操作方法，请参阅[手动添加帐户](user-help-auth-app-add-account-manual.md)。

5. 检查设备上应用的“帐户”屏幕，以确保帐户正确，并有关联的六位数验证码。 为了提高安全性，验证码每 30 秒更改一次，以防有人多次使用一个代码。

    ![帐户屏幕](./media/user-help-auth-app-download-install/auth-app-accounts.png)

## <a name="next-steps"></a>后续步骤

- 将帐户添加到应用后，可以在设备上使用 Authenticator 应用登录。 有关详细信息，请参阅[使用应用登录](user-help-auth-app-sign-in.md)。

- 对于运行 iOS 的设备，还可以将帐户凭据和相关的应用设置（如帐户顺序）备份到云中。 有关详细信息，请参阅 [Microsoft Authenticator 应用备份和恢复](user-help-auth-app-backup-recovery.md)。
