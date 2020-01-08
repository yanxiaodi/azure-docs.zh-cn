---
title: 在使用 Azure Site Recovery 的 VMware Vm 和物理服务器灾难恢复过程中设置横向扩展进程服务器 |Microsoft Docs
description: 本文介绍如何在 VMware VM 和物理服务器的灾难恢复期间设置横向扩展进程服务器。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/23/2019
ms.author: ramamill
ms.openlocfilehash: 1b6084b4e93f3dc17f633f1b8496f9c26e7f576f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64925476"
---
# <a name="scale-with-additional-process-servers"></a>使用额外的进程服务器进行扩展

默认情况下，当使用 [Site Recovery](site-recovery-overview.md) 将 VMware VM 或物理服务器复制到 Azure 时，进程服务器将安装在配置服务器计算机上，并且将用于协调 Site Recovery 和本地基础结构之间的数据传输。 若要增加容量并横向扩展复制部署，可以添加额外的独立进程服务器。 本文介绍如何设置横向扩展进程服务器。

## <a name="before-you-start"></a>开始之前

### <a name="capacity-planning"></a>容量计划

请确保已执行[容量规划](site-recovery-plan-capacity-vmware.md)以进行 VMware 复制。 这可帮助你确定如何以及何时应部署额外的进程服务器。

从 9.24 版本开始，在选择用于新复制的进程服务器时添加了指导。 根据特定条件，进程服务器将标记为“正常”、“警告”和“严重”。 若要了解可能影响进程服务器状态的不同场景，请查看[进程服务器警报](vmware-physical-azure-monitor-process-server.md#process-server-alerts)。

> [!NOTE]
> 不支持使用克隆的进程服务器组件。 按照本文中的步骤横向扩展每个 PS。

### <a name="sizing-requirements"></a>调整大小要求 

验证该表中汇总的调整大小要求。 通常，如果必须将部署扩大到 200 台以上的源计算机，或者每日总改动率超过 2 TB，则需要额外的进程服务器来处理流量。

| **额外的进程服务器** | **缓存磁盘大小** | **数据更改率** | **受保护的计算机** |
| --- | --- | --- | --- |
|4 个 vCPU（2 个插槽 * 2 个核心 \@ 2.5 GHz），8 GB 内存 |300 GB |250 GB 或更少 |复制 85 台或更少的计算机。 |
|8 个 vCPU（2 个插槽 * 4 个核心 \@ 2.5 GHz），12 GB 内存 |600 GB |250 GB 到 1 TB |复制 85-150 台计算机。 |
|12 个 vCPU（2 个插槽 * 6 个核心 \@ 2.5 GHz），24 GB 内存 |1 TB |1 TB 到 2 TB |复制 150-225 台计算机。 |

其中，每台受保护的源计算机配置有 3 个磁盘，每个磁盘 100 GB。

### <a name="prerequisites"></a>必备组件

下表中汇总了额外进程服务器的先决条件。

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="download-installation-file"></a>下载安装文件

下载进程服务器的安装文件，如下所示：

1. 登录到 Azure 门户，并浏览到恢复服务保管库。
2. 打开“Site Recovery 基础结构”   > “VMWare 和物理计算机”   > “配置服务器”  （在“针对 VMware 和物理计算机”下面）。
3. 选择配置服务器以向下钻取到配置服务器详细信息。 然后单击“+ 进程服务器”  。
4. 在“添加进程服务器”   >  “选择要部署进程服务器的位置”  中，选择“在本地部署横向扩展进程服务器”  。

   ![添加服务器页](./media/vmware-azure-set-up-process-server-scale/add-process-server.png)
1. 单击“下载 Microsoft Azure Site Recovery 统一安装程序”  。 这会下载最新版本的安装文件。

   > [!WARNING]
   > 进程服务器安装版本应低于所运行的配置服务器版本或与之相同。 确保版本兼容性的一种简单方法是使用最近用来安装或更新配置服务器的同一安装程序。

## <a name="install-from-the-ui"></a>从 UI 安装

如下所示进行安装。 设置服务器后，可以迁移源计算机来使用它。

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-add-process-server.md)]


## <a name="install-from-the-command-line"></a>从命令行安装

运行以下命令进行安装：

```
UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
```

其中，命令行参数如下所示：

[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]

例如：

```
MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
cd C:\Temp\Extracted
UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "PS" /InstallLocation "D:\" /EnvType "VMWare" /CSIP "10.150.24.119" /PassphraseFilePath "C:\Users\Administrator\Desktop\Passphrase.txt" /DataTransferSecurePort 443
```
### <a name="create-a-proxy-settings-file"></a>创建代理设置文件

如果需要设置代理，请使用 ProxySettingsFilePath 参数指定某个文件作为输入。 可以如下所述创建文件并将其作为输入 ProxySettingsFilePath 参数进行传递。

```
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address"
* ProxyPort = "Port"
* ProxyUserName="UserName"
* ProxyPassword="Password"
```

## <a name="next-steps"></a>后续步骤
了解如何[管理进程服务器设置](vmware-azure-manage-process-server.md)
