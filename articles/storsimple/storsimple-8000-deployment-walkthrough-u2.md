---
title: 在 Azure 门户中部署 StorSimple 8000 系列设备 | Microsoft Docs
description: 介绍了部署 StorSimple 8000 系列设备（运行 Update 3 及更高版本）和 StorSimple 设备管理器服务的步骤和最佳做法。
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/23/2018
ms.author: alkohli
ms.openlocfilehash: 1f44690de1f38e3d337072cc7c974887eb0e31cc
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68965905"
---
# <a name="deploy-your-on-premises-storsimple-device-update-3-and-later"></a>部署本地 StorSimple 设备（Update 3 及更高版本）

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>概述
欢迎使用 Microsoft Azure StorSimple 设备部署。 这些部署教程适用于 StorSimple 8000 系列 Update 3 或更高版本。 本系列教程包括 StorSimple 设备的配置清单、配置先决条件和详细配置步骤。

这些教程中的信息均假定已阅读安全预防措施，并已打开 StorSimple 设备包、装入机架，并连接好电缆。 如果仍然需要执行这些任务，请从阅读 [安全预防措施](storsimple-8000-safety.md)开始。 按照设备具体说明，将设备解包、装入机架并连接好电缆。

* [解包、装载机架，并将电缆接到 8100](storsimple-8100-hardware-installation.md)
* [解包、安装机架，将电缆连接到 8600](storsimple-8600-hardware-installation.md)

需要有管理员权限才能完成安装和配置过程。 建议在开始之前查看配置清单。 部署和配置过程可能需要一些时间才能完成。

> [!NOTE]
> Microsoft Azure 网站上发布的 StorSimple 部署信息仅适用于 StorSimple 8000 系列设备。 如需 7000 系列设备的完整信息，请转到：[http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com)。 如需 7000 系列的部署信息，请参阅 [StorSimple 系统快速入门指南](http://onlinehelp.storsimple.com/111_Appliance/)。 


## <a name="deployment-steps"></a>部署步骤
执行这些必需的步骤来配置 StorSimple 设备，并将其连接到 StorSimple Device Manager 服务。 除了这些所需的步骤外，在部署过程中可能还需要完成一些可选步骤和过程。 逐步部署说明将指示何时应执行每个可选步骤。

| 步骤 | 描述 |
| --- | --- |
| **先决条件** |在为即将进行的部署执行准备工作时必须完成这些事项。 |
| [部署配置清单](#deployment-configuration-checklist) |在部署之前或在部署期间使用此清单来收集和记录信息。 |
| [部署先决条件](#deployment-prerequisites) |这些项会验证环境是否已准备就绪以进行部署。 |
|  | |
| **逐步部署** |需要完成这些步骤，以在生产中部署 StorSimple 设备。 |
| [步骤 1：创建新服务](#step-1-create-a-new-service) |设置 StorSimple 设备的云管理和存储。 *如果其他 StorSimple 设备有现有服务，请跳过此步骤*。 |
| [步骤 2：获取服务注册密钥](#step-2-get-the-service-registration-key) |使用此密钥来注册 StorSimple 设备，并将其连接到管理服务。 |
| [步骤 3：通过用于 StorSimple 的 Windows PowerShell 配置和注册设备](#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |要使用管理服务完成设置，请将设备连接到网络并将其注册到 Azure。 |
| [步骤 4：完成最小设备设置](#step-4-complete-minimum-device-setup)</br>[最佳做法:更新 StorSimple 设备](#scan-for-and-apply-updates) |使用管理服务来完成设备安装，并启用以使其能够提供存储。 |
| [步骤 5：创建卷容器](#step-5-create-a-volume-container) |创建容以预配卷。 卷容器具有其中所包含的所有卷的存储帐户、带宽和加密设置。 |
| [步骤 6：创建卷](#step-6-create-a-volume) |在服务器的 StorSimple 设备上预配存储卷。 |
| [步骤 8：装载、初始化和格式化卷](#step-7-mount-initialize-and-format-a-volume)</br>[可选：配置 MPIO](storsimple-8000-configure-mpio-windows-server.md) |将服务器连接到设备提供的 iSCSI 存储。 根据情况配置 MPIO，以确保服务器可以容许链接、网络和接口故障。 |
| [步骤 8：执行备份](#step-8-take-a-backup) |设置备份策略以保护数据 |
|  | |
| **其他过程** |在部署解决方案时可能需要参阅这些过程。 |
| [针对服务配置新的存储帐户](#configure-a-new-storage-account-for-the-service) | |
| [使用 PuTTY 连接到设备串行控制台](#use-putty-to-connect-to-the-device-serial-console) | |
| [获取 Windows Server 主机的 IQN](#get-the-iqn-of-a-windows-server-host) | |
| [创建手动备份](#create-a-manual-backup) | |


## <a name="deployment-configuration-checklist"></a>部署配置清单
在部署设备之前，需要收集信息来配置 StorSimple 设备上的软件。 提前准备其中的一些信息有助于简化在环境中部署 StorSimple 设备的过程。 下载并使用此清单，以记下部署设备时的配置详细信息。

* [下载 StorSimple 部署配置清单](https://www.microsoft.com/download/details.aspx?id=49159)

## <a name="deployment-prerequisites"></a>部署先决条件
以下各部分介绍了 StorSimple Device Manager 服务和 StorSimple 设备的配置先决条件。

### <a name="for-the-storsimple-device-manager-service"></a>对于 StorSimple Device Manager 服务
在开始之前，请确保：

* 具有 Microsoft 帐户和访问凭据。
* 具有 Microsoft Azure 存储帐户和访问凭据。
* Microsoft Azure 订阅已启用了 StorSimple Device Manager 服务。 应通过 [企业协议](https://azure.microsoft.com/pricing/enterprise-agreement/)购买订阅。
* 有权访问终端模拟软件，如 PuTTY。

### <a name="for-the-device-in-the-datacenter"></a>对于数据中心中的设备
在配置设备前，请确保设备已完全解包、装载在机架上，并已连接所有电源、网络和串行访问线缆，如下所述：

* [解包、装载机架，并将电缆接到 8100 设备](storsimple-8100-hardware-installation.md)
* [解包、装载机架，并电缆接到 8600 设备](storsimple-8600-hardware-installation.md)

### <a name="for-the-network-in-the-datacenter"></a>对于数据中心中的网络
在开始之前，请确保：

* 数据中心防火墙的端口已开放，以允许 iSCSI 和云流量，如 [StorSimple 设备的网络要求](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device)中所述。

## <a name="step-by-step-deployment"></a>逐步部署
使用下面的分步说明在数据中心部署 StorSimple 设备。

## <a name="step-1-create-a-new-service"></a>步骤 1：创建新服务
StorSimple Device Manager 服务可以管理多个 StorSimple 设备。 执行以下步骤创建 StorSimple Device Manager 服务实例。

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-8000-create-new-service.md)]

> [!IMPORTANT]
> 如果未启用服务的自动创建存储帐户，则在成功创建服务后，需要创建至少一个存储帐户。 在创建卷容器时会使用此存储帐户。
>
> * 如果未自动创建存储帐户，请转到 [针对服务配置新的存储帐户](#configure-a-new-storage-account-for-the-service) 了解详细说明。
> * 如果启用了自动创建存储帐户，请转到 [步骤 2：获取服务注册密钥](#step-2-get-the-service-registration-key)。


## <a name="step-2-get-the-service-registration-key"></a>步骤 2：获取服务注册密钥
在 StorSimple Device Manager 服务启动并运行以后，将需要获取服务注册密钥。 此密钥可用来注册 StorSimple 设备，并将其连接到服务。

在 Azure 门户中执行以下步骤。

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

## <a name="step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple"></a>步骤 3：通过用于 StorSimple 的 Windows PowerShell 配置和注册设备
使用 Windows PowerShell for StorSimple 来完成 StorSimple 设备的初始设置，如以下过程所述。 需要使用终端模拟软件来完成此步骤。 有关详细信息，请参阅 [使用 PuTTY 连接到设备串行控制台](#use-putty-to-connect-to-the-device-serial-console)。

[!INCLUDE [storsimple-8000-configure-and-register-device-u2](../../includes/storsimple-8000-configure-and-register-device-u2.md)]

## <a name="step-4-complete-minimum-device-setup"></a>步骤 4：完成最低要求的设备设置
若要完成 StorSimple 设备的最起码设备配置，需要： 

* 为设备提供一个友好名称。
* 设置设备时区。
* 将固定的 IP 地址分配给两个控制器。

在 Azure 门户中执行以下步骤，完成最基本的设备设置。

[!INCLUDE [storsimple-8000-complete-minimum-device-setup-u2](../../includes/storsimple-8000-complete-minimum-device-setup-u2.md)]

完成最低要求的设备设置以后，最好能够[扫描并应用最新更新](#scan-for-and-apply-updates)。

## <a name="step-5-create-a-volume-container"></a>步骤 5：创建卷容器
卷容器具有其中所包含的所有卷的存储帐户、带宽和加密设置。 需要创建卷容器后才可以开始在 StorSimple 设备上设置卷。

在 Azure 门户中执行以下步骤来创建卷容器。

[!INCLUDE [storsimple-8000-create-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="step-6-create-a-volume"></a>步骤 6：创建卷
创建卷容器后，就可以为服务器在 StorSimple 设备上设置存储卷。 在 Azure 门户中执行以下步骤来创建卷。

> [!IMPORTANT]
> StorSimple Device Manager 可以创建精简预配和完全预配的卷。 但是不能创建部分预配的卷。


[!INCLUDE [storsimple-8000-create-volume](../../includes/storsimple-8000-create-volume-u2.md)]

## <a name="step-7-mount-initialize-and-format-a-volume"></a>步骤 7：装载、初始化和格式化卷
请在 Windows Server 主机上执行以下步骤。

> [!IMPORTANT]
> * 为了获得 StorSimple 解决方案的高可用性，建议在配置 iSCSI 之前先在主机服务器（可选）上配置 MPIO。 主机服务器上的 MPIO 配置将确保服务器可以容许链接、网络或接口故障。
> * 如需 Windows Server 主机上的 MPIO 和 iSCSI 安装和配置说明，请转到 [为 StorSimple 设备配置 MPIO](storsimple-8000-configure-mpio-windows-server.md)。 这些内容还包括装载、初始化和格式化 StorSimple 卷的步骤。
> * 如需 Linux 主机上的 MPIO 和 iSCSI 安装和配置说明，请转到 [为 StorSimple Linux 主机配置 MPIO](storsimple-configure-mpio-on-linux.md)

如果决定不配置 MPIO，请执行以下步骤来装载、初始化和格式化 Windows Server 主机上的 StorSimple 卷。

[!INCLUDE [storsimple-8000-mount-initialize-format-volume](../../includes/storsimple-8000-mount-initialize-format-volume.md)]

## <a name="step-8-take-a-backup"></a>步骤 8：执行备份
备份可提供卷的时间点保护，并可提高可恢复性，同时最大限度地减少恢复时间。 可以在 StorSimple 设备上执行两种类型的备份：本地快照和云快照。 上述每种备份类型都可以是**计划**或**手动**的。

在 Azure 门户中执行以下步骤来创建计划备份。

[!INCLUDE [storsimple-8000-take-backup](../../includes/storsimple-8000-take-backup.md)]

可以随时进行手动备份。 如需相关过程，请转到 [创建手动备份](#create-a-manual-backup)。

设备配置已完成。

## <a name="configure-a-new-storage-account-for-the-service"></a>针对服务配置新的存储帐户
这是一个可选步骤，只有当未启用服务自动创建存储帐户时，才需要执行。 必须要具有 Microsoft Azure 存储帐户才可以创建 StorSimple 卷容器。

如果需要在不同的区域创建 Azure 存储帐户，请参阅 [关于 Azure 存储帐户](../storage/common/storage-create-storage-account.md) 了解逐步说明。

在 Azure 门户中的“StorSimple Device Manager 服务”页上执行以下步骤。

[!INCLUDE [storsimple-8000-configure-new-storage-account-u2](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

## <a name="use-putty-to-connect-to-the-device-serial-console"></a>使用 PuTTY 连接到设备串行控制台
若要连接到 Windows PowerShell for StorSimple，需要使用终端模拟软件，如 PuTTY。 可以在访问设备时，直接通过串行控制台或从远程计算机上打开 telnet 会话来使用 PuTTY。

[!INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]

## <a name="scan-for-and-apply-updates"></a>扫描并应用更新
更新设备可能需要几个小时。 有关如何安装最新更新的详细步骤，请访问[安装 Update 5](storsimple-8000-install-update-5.md)。


## <a name="get-the-iqn-of-a-windows-server-host"></a>获取 Windows Server 主机的 IQN
请执行以下步骤，获取正在运行 Windows Server® 2012 的 Windows 主机的 iSCSI 限定名称 (IQN)。

[!INCLUDE [Create a manual backup](../../includes/storsimple-get-iqn.md)]

## <a name="create-a-manual-backup"></a>创建手动备份
在 Azure 门户中执行以下步骤，在 StorSimple 设备上为单个卷创建按需手动备份。

[!INCLUDE [Create a manual backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="view-the-pinout-diagram-for-serial-cable-for-storsimple"></a>查看 StorSimple 串行电缆的引出线图
以下引出线图可用于 StorSimple 串行控制台电缆。

在此图中，DB9 插孔连接器为 P1，3.5 mm 连接器为 P2。

![StorSimple 串行控制台电缆的引出线图 1](./media/storsimple-8000-deployment-walkthrough-u2/pinout-diagram1.png)

立体声插孔的尖端被认为是 PIN 3 RX，中间是 PIN 2 TX，底座是 PIN 1 GND，如下图所示。

![StorSimple 串行控制台电缆的引出线图 2](./media/storsimple-8000-deployment-walkthrough-u2/pinout-diagram2.png)



## <a name="next-steps"></a>后续步骤
* [配置 StorSimple 云设备](storsimple-8000-cloud-appliance-u2.md)。
* [使用 StorSimple 设备管理器服务管理 StorSimple 设备](storsimple-8000-manager-service-administration.md)。

