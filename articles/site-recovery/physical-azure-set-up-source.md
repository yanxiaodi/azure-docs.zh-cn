---
title: 使用 Azure Site Recovery 设置配置服务器，以便将物理服务器灾难恢复到 Azure | Microsoft Docs
description: 本文介绍如何设置本地配置服务器，以便将本地物理服务器灾难恢复到 Azure。
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: ramamill
ms.openlocfilehash: 902c14211e91a1500c6b50cd790b347e337c4f70
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67589057"
---
# <a name="set-up-the-configuration-server-for-disaster-recovery-of-physical-servers-to-azure"></a>设置配置服务器，以便将物理服务器灾难恢复到 Azure

本文介绍如何设置本地环境，以便开始将运行 Windows 或 Linux 的物理服务器复制到 Azure。

## <a name="prerequisites"></a>先决条件

本文假设已具有：
- [Azure 门户](https://portal.azure.com "Azure 门户")中的恢复服务保管库。
- 安装配置服务器的物理计算机。
- 如果要在其安装配置服务器计算机上，已禁用 TLS 1.0，请确保启用了 TLs 1.2，并在计算机上安装.NET Framework 4.6 或更高版本，（与启用强加密）。 [了解详细信息](https://support.microsoft.com/help/4033999/how-to-resolve-azure-site-recovery-agent-issues-after-disabling-tls-1)。

### <a name="configuration-server-minimum-requirements"></a>配置服务器的最低要求
下表列出了配置服务器的最低硬件、软件和网络要求。
[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> 配置服务器不支持基于 HTTPS 的代理服务器。

## <a name="choose-your-protection-goals"></a>选择保护目标

1. 在 Azure 门户中，转到“恢复服务保管库”  边栏选项卡，然后选择保管库。
2. 在保管库的“资源”  菜单中，单击“开始使用”   > “Site Recovery”   > “步骤 1: 准备基础结构”   > “保护目标”  。

    ![选择目标](./media/physical-azure-set-up-source/choose-goals.png)
3. 在“保护目标”  中，依次选择“到 Azure”  和“未虚拟化/其他”  ，并单击“确定”  。

    ![选择目标](./media/physical-azure-set-up-source/physical-protection-goal.png)

## <a name="set-up-the-source-environment"></a>设置源环境

1. 如果没有配置服务器，请在“准备源”  中单击“+配置服务器”  添加一个。

   ![设置源](./media/physical-azure-set-up-source/plus-config-srv.png)
2. 在“添加服务器”边栏选项卡中，检查“配置服务器”是否已出现在“服务器类型”中。   
4. 下载站点恢复统一安装程序安装文件。
5. 下载保管库注册密钥。 运行统一安装程序时，需要注册密钥。 生成的密钥有效期为 5 天。

    ![设置源](./media/physical-azure-set-up-source/set-source2.png)
6. 在用作配置服务器的计算机上，运行 **Azure Site Recovery 统一安装程序**以安装配置服务器、进程服务器和主目标服务器。

#### <a name="run-azure-site-recovery-unified-setup"></a>运行 Azure Site Recovery 统一安装程序

> [!TIP]
> 如果计算机上的系统时钟时间与本地时间相差 5 分钟以上，则配置服务器注册会失败。 在开始安装之前，请将系统时钟与[时间服务器](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service)同步。

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> 可通过命令行安装配置服务器。 [了解详细信息](physical-manage-configuration-server.md#install-from-the-command-line)。


## <a name="common-issues"></a>常见问题

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>后续步骤

下一步涉及在 Azure 中[设置目标环境](physical-azure-set-up-target.md)。
