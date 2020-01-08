---
title: 使用 Azure Site Recovery 为 Azure VM 设置到 Azure 次要区域的灾难恢复
description: 了解如何使用 Azure Site Recovery 服务为 Azure VM 设置到其他 Azure 区域的灾难恢复。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 08/05/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 6987c6f1191b0dfc7b78b14e77a5d6a0ab369f57
ms.sourcegitcommit: f7998db5e6ba35cbf2a133174027dc8ccf8ce957
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/05/2019
ms.locfileid: "68782608"
---
# <a name="set-up-disaster-recovery-for-azure-vms"></a>为 Azure VM 设置灾难恢复

[Azure Site Recovery](site-recovery-overview.md) 服务可管理和协调本地计算机和 Azure 虚拟机 (VM) 的复制、故障转移和故障回复，因而有利于灾难恢复策略。

本教程介绍如何为 Azure VM 设置灾难恢复：将 VM 从一个 Azure 区域复制到另一个区域。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建恢复服务保管库
> * 验证目标资源设置
> * 设置 VM 的出站网络连接
> * 为虚拟机启用复制

> [!NOTE]
> 本文说明了如何使用最简单的设置来部署灾难恢复。 若要了解自定义的设置，请查看[“操作方法”部分](azure-to-azure-how-to-enable-replication.md)的文章。

## <a name="prerequisites"></a>先决条件

完成本教程：

- 查看[方案体系结构和组件](concepts-azure-to-azure-architecture.md)。
- 在开始之前，请查看[支持要求](site-recovery-support-matrix-azure-to-azure.md)。

## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

在除了源区域之外的任意区域中创建保管库。

1. 登录到 [Azure 门户](https://portal.azure.com) > **恢复服务**。
2. 单击“创建资源” > “管理工具” > “备份和 Site Recovery”    。
3. 在“名称”  中，指定一个友好名称以标识该保管库。 如果有多个订阅，请选择合适的一个。
4. 创建一个资源组或选择一个现有的资源组。 指定 Azure 区域。 若要查看受支持的区域，请参阅 [Azure Site Recovery 定价详细信息](https://azure.microsoft.com/pricing/details/site-recovery/)中的“地域可用性”。
5. 若要从仪表板快速访问保管库，请单击“固定到仪表板”，然后单击“创建”。  

   ![新保管库](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

   新保管库将添加到“仪表板”中的“所有资源”下，以及“恢复服务保管库”主页面上。   

## <a name="verify-target-resource-settings"></a>验证目标资源设置

1. 验证 Azure 订阅是否允许在目标区域中创建 VM。 请联系支持部门，启用所需配额。
2. 确保订阅中有足够的资源，能够支持与源 VM 匹配的 VM 大小。 Site Recovery 会为目标 VM 选择相同的大小或尽可能接近的大小。

## <a name="set-up-outbound-network-connectivity-for-vms"></a>设置 VM 的出站网络连接

若要使 Site Recovery 按预期工作，需在要复制的 VM 中对出站网络连接进行修改。

> [!NOTE]
> Site Recovery 不支持使用身份验证代理来控制网络连接。


### <a name="outbound-connectivity-for-urls"></a>URL 的出站连接

如果使用基于 URL 的防火墙代理来控制出站连接，请允许访问以下 URL。

| **URL** | **详细信息** |
| ------- | ----------- |
| \* .blob.core.windows.net | 允许将数据从 VM 写入源区域中的缓存存储帐户。 |
| login.microsoftonline.com | 向 Site Recovery 服务 URL 提供授权和身份验证。 |
| *.hypervrecoverymanager.windowsazure.com | 允许 VM 与 Site Recovery 服务进行通信。 |
| *.servicebus.windows.net | 允许 VM 写入 Site Recovery 监视和诊断数据。 |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>IP 地址范围的出站连接

如果想要使用 IP 地址而不是 URL 来控制出站连接，请允许将这些地址用于基于 IP 的防火墙、代理或 NSG 规则。

  - [Microsoft Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)
  - [德国的 Windows Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=54770)
  - [中国的 Windows Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=42064)
  - [Office 365 URL 和 IP 地址范围](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)
  - [Site Recovery 服务终结点 IP 地址](https://aka.ms/site-recovery-public-ips)

如果你正在使用 NSG，则可以为源区域创建存储服务标记 NSG 规则。 [了解详细信息](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges)。

## <a name="verify-azure-vm-certificates"></a>验证 Azure VM 证书

检查要复制的 VM 是否有最新的根证书。 如果没有，则 VM 会由于安全约束而无法注册到 Site Recovery。

- 对于 Windows VM，请在 VM 上安装所有最新的 Windows 更新，使所有受信任的根证书位于该计算机上。 在未联网的环境中，请按照你的组织的标准 Windows 更新和证书更新过程执行操作。
- 对于 Linux VM，请遵循 Linux 分销商提供的指导，在 VM 上获取最新的受信任根证书和证书吊销列表。

## <a name="set-permissions-on-the-account"></a>设置帐户权限

Azure Site Recovery 提供了三个用于控制 Site Recovery 管理操作的内置角色。

- **Site Recovery 参与者** - 此角色拥有管理恢复服务保管库中 Azure Site Recovery 操作所需的全部权限。 不过，拥有此角色的用户既无法创建或删除恢复服务保管库，也无法向其他用户分配访问权限。 此角色最适合分配给灾难恢复管理员，这样他们就可以为应用程序或整个组织启用和管理灾难恢复。

- **Site Recovery 操作员** - 此角色有权执行和管理故障转移和故障回复操作。 拥有此角色的用户无法启用或禁用复制、无法创建或删除保管库，也无法注册新的基础结构或向其他用户分配访问权限。 此角色最适合分配给灾难恢复操作员，这样他们就可以遵循应用程序所有者或 IT 管理员的指示，对虚拟机或应用程序进行故障转移。 解决灾难后，DR 操作员可以对虚拟机进行重新保护和故障回复。

- **Site Recovery 读者** - 此角色有权查看所有 Site Recovery 管理操作。 此角色最适合分配给 IT 监视主管，这样他们就可以监视当前保护状态并创建支持票证。

详细了解 [Azure RBAC 内置角色](../role-based-access-control/built-in-roles.md)。

## <a name="enable-replication-for-a-vm"></a>为虚拟机启用复制

### <a name="select-the-source"></a>选择源

1. 在“恢复服务保管库”中，单击保管库名称 >“+复制”  。
2. 在“源”中，选择“Azure”。  
3. 在“源位置”中，选择当前运行 VM 的 Azure 源区域。 
4. 选择运行虚拟机的**源订阅**。 这可以是存在恢复服务保管库的同一 Azure Active Directory 租户中的任何订阅。
5. 选择“源资源组”，然后单击“确定”保存设置。  

    ![设置源](./media/azure-to-azure-tutorial-enable-replication/source.png)

### <a name="select-the-vms"></a>选择 VM

Site Recovery 检索与订阅和资源组/云服务关联的 VM 列表。

1. 在“虚拟机”  中，选择要复制的 VM。
2. 单击“确定”。 

### <a name="configure-replication-settings"></a>配置复制设置

Site Recovery 会针对目标区域创建默认设置和复制策略。 可以根据需要更改设置。

1. 单击“设置”查看目标设置和复制设置  。
2. 若要重写默认目标设置，请单击“资源组、网络、存储和可用性”旁边的“自定义”   。

   ![配置设置](./media/azure-to-azure-tutorial-enable-replication/settings.png)


3. 根据下表中的摘要内容自定义目标设置。

    **设置** | **详细信息**
    --- | ---
    **目标订阅** | 默认情况下，目标订阅与源订阅相同。 单击“自定义”以在同一 Azure Active Directory 租户中选择其他目标订阅。
    **目标位置** | 用于灾难恢复的目标区域。<br/><br/> 建议选择与 Site Recovery 保管库位置匹配的目标位置。
    **目标资源组** | 故障转移后，目标区域中用于容纳 Azure VM 的资源组。<br/><br/> 默认情况下，Site Recovery 会在目标位置中创建一个带有“asr”后缀的新资源组。 目标资源组的位置可以是除托管源虚拟机区域以外的任何区域。
    **目标虚拟网络** | 故障转移后，目标区域中 VM 所位于的网络。<br/><br/> 默认情况下，Site Recovery 会在目标位置中创建一个带有“asr”后缀的新虚拟网络（以及子网）。
    **缓存存储帐户** | Site Recovery 使用源区域中的一个存储帐户。 复制到目标位置之前，对源 VM 的更改将发送到此帐户。<br/><br/> 如果使用支持防火墙的缓存存储帐户，请确保启用“允许受信任的 Microsoft 服务”。  [了解详细信息。](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)
    **目标存储帐户(源 VM 使用非托管磁盘)** | 默认情况下，Site Recovery 会在目标区域中创建新存储帐户，从而形成源 VM 存储帐户的镜像。<br/><br/> 如果使用支持防火墙的缓存存储帐户，请启用“允许受信任的 Microsoft 服务”。 
    **副本托管磁盘(如果源 VM 使用托管磁盘)** | 默认情况下，Site Recovery 在目标区域中创建副本托管磁盘，以生成和源 VM 的托管磁盘存储类型一致（标准或高级）的镜像磁盘。 只能自定义磁盘类型 
    **目标可用性集** | 默认情况下，Azure Site Recovery 会在目标区域中创建一个名称带有“asr”后缀（针对源区域中可用性集的 VM 部分）的新可用性集。 如果 Azure Site Recovery 创建的可用性集已存在，则会重复使用。
    **目标可用性区域** | 默认情况下，Site Recovery 会在目标区域中分配与源区域相同的区域编号，前提是目标区域支持可用性区域。<br/><br/> 如果目标区域不支持可用性区域，则会将目标 VM 默认配置为单一实例。<br/><br/> 单击“自定义”，将 VM 配置为目标区域中可用性集的一部分。 <br/><br/> 启用复制后，无法更改可用性类型（单一实例、可用性集或可用性区域）。 若要更改可用性类型，需要先禁用复制，然后再启用复制。

4. 若要自定义复制策略设置，请单击“复制策略”旁边的“自定义”，然后根据需要修改设置。  

    **设置** | **详细信息**
    --- | ---
    **复制策略名称** | 策略名称。
    **恢复点保留期** | 默认情况下，Site Recovery 会将恢复点保留 24 小时。 可将此值配置为 1 - 72 小时。
    **应用一致性快照频率** | 默认情况下，Site Recovery 每隔 4 小时拍摄 1 次“应用一致”快照。 可将此值配置为 1 - 12 小时之间的任何值。<br/><br/> 应用一致的快照是 VM 内应用程序数据的时间点快照。 卷影复制服务 (VSS) 确保 VM 上的应用在拍摄快照时处于一致状态。
    **复制组** | 如果应用程序需要跨 VM 的多 VM 一致性，可以为这些 VM 创建一个复制组。 默认情况下，所选的 VM 不属于任何复制组。

5. 若要将 VM 添加到新的或现有的复制组，请在“自定义”中选择“是”以确保多 VM 一致性。   然后单击“确定”  。 

    >[!NOTE]
    >- 故障转移时，复制组中的所有计算机将获得共享的崩溃一致性恢复点和应用程序一致性恢复点。
    >- 启用（CPU 密集型的）多 VM 一致性会影响工作负荷的性能。 仅当计算机运行相同的工作负荷并且你需要在多个计算机之间保持一致时，才使用此功能。
    >- 在一个复制组中最多可以包含 16 个 VM。
    >- 如果启用了多 VM 一致性，则复制组中的计算机将通过端口 20004 相互通信。 确保没有防火墙阻止 VM 通过此端口进行内部通信。
    >- 对于复制组中的 Linux VM，请确保根据适用于 Linux 版本的指导手动打开端口 20004 上的出站流量。



### <a name="configure-encryption-settings"></a>配置加密设置

如果源 VM 上已启用 Azure 磁盘加密 (ADE)，请检查设置。

1. 验证设置：
    - **磁盘加密密钥保管库**：默认情况下，Site Recovery 会在源 VM 磁盘加密密钥中创建新的 Key Vault，其名称带有“asr”后缀。 如果该 Key Vault 已存在，则会重复使用它。
    - **密钥加密密钥保管库**：默认情况下，Site Recovery 会在目标区域中创建新的 Key Vault， 其名称带有“asr”后缀，并且基于源 VM 的密钥加密密钥。 如果 Site recovery 创建的 Key Vault 已存在，则会重复使用它。

2. 单击“自定义”，选择自定义密钥保管库。 

>[!NOTE]
>Azure Site Recovery 目前仅支持运行 Windows 操作系统且[已使用 Azure AD 应用启用加密](https://aka.ms/ade-aad-app)的 Azure VM。
>

### <a name="track-replication-status"></a>跟踪复制状态

1. 在“设置”中，单击“刷新”以获取最新状态。  
2. 跟踪进度和状态，如下所示：
    - 在“设置” > “作业” > “Site Recovery 作业”中，跟踪“启用保护”作业的进度。    
    - 在“设置” > “复制的项”中，可以查看 VM 的状态和初始复制进度。   单击 VM，向下钻取其设置。

## <a name="next-steps"></a>后续步骤

在本教程中，已经为 Azure VM 配置了灾难恢复。 现在可以启动一个灾难恢复演练，检查故障转移是否按预期工作。

> [!div class="nextstepaction"]
> [运行灾难恢复演练](azure-to-azure-tutorial-dr-drill.md)
