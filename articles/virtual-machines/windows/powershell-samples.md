---
title: Azure 虚拟机 PowerShell 示例 | Microsoft 文档
description: Azure 虚拟机 PowerShell 示例
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: c106ee2110a8c023ab3ed4f2ec9903fc5ca146b2
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70088970"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Azure 虚拟机 PowerShell 示例

下表提供了用于创建和管理 Windows 虚拟机 (VM) 的 PowerShell 脚本示例的链接。

| | |
|---|---|
|**创建虚拟机**||
| [快速创建虚拟机](./../scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 根据简单的提示创建资源组、虚拟机和所有相关资源。|
| [创建完全配置的虚拟机](./../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 创建资源组、虚拟机和所有相关资源。|
| [创建高度可用的虚拟机](./../scripts/virtual-machines-windows-powershell-sample-create-nlb-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 使用高度可用且负载均衡的配置创建多个虚拟机。|
| [创建 VM 并运行配置脚本](./../scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 创建一个虚拟机，并使用 Azure 自定义脚本扩展安装 IIS。 |
| [创建 VM 并运行 DSC 配置](./../scripts/virtual-machines-windows-powershell-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 创建一个虚拟机，并使用 Azure Desired State Configuration (DSC) 扩展来安装 IIS。 |
| [上传 VHD 并创建 VM](./../scripts/virtual-machines-windows-powershell-upload-generalized-script.md) | 将本地 VHD 文件上传到 Azure，从 VHD 创建映像，然后通过该映像创建 VM。 |
| [从托管 OS 磁盘创建 VM](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 通过将现有托管磁盘附加为 OS 磁盘来创建虚拟机。 |
| [从快照创建 VM](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 通过先从快照创建托管磁盘，然后将新的托管磁盘附加为 OS 磁盘来从快照创建虚拟机。 |
|**管理存储**||
| [在相同或不同的订阅中从 VHD 创建托管磁盘](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 在相同或不同订阅中，从专用 VHD 将托管磁盘创建为 OS 磁盘，或从数据 VHD 将托管磁盘创建为数据磁盘。  |
| [从快照创建托管磁盘](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 从快照创建托管磁盘。 |
| [将托管磁盘复制到相同或不同的订阅](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | 将托管磁盘复制到父托管磁盘所在区域中的相同或不同订阅。
| [将快照作为 VHD 导出到存储帐户](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 将托管快照作为 VHD 导出到不同区域中的存储帐户。 |
| [将托管磁盘的 VHD 导出到存储帐户](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 将托管磁盘的基础 VHD 导出到不同区域的存储帐户。 |
| [从 VHD 创建快照](../scripts/virtual-machines-windows-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 从 VHD 创建快照，然后使用该快照快速创建多个相同的托管磁盘。  |
| [将快照复制到相同或不同的订阅](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 将快照复制到父快照所在区域中的相同或不同订阅。 |
|**保护虚拟机**||
| [加密 VM 及其数据磁盘](./../scripts/virtual-machines-windows-powershell-sample-encrypt-vm.md?toc=%2fpowershell%2fazure%2ftoc.json) | 创建 Azure Key Vault、加密密钥和服务主体，然后对 VM 进行加密。 |
|**监视虚拟机**||
| [使用 Azure Monitor 监视 VM](./../scripts/virtual-machines-windows-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 创建一个虚拟机，安装 Azure Log Analytics 代理，然后在 Log Analytics 工作区中注册该 VM。  |
| [使用 PowerShell 收集订阅中所有 VM 的详细信息](../scripts/virtual-machines-powershell-sample-collect-vm-details.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 创建一个 csv，其中包含所提供订阅中 VM 的 VM 名称、资源组名称、区域、虚拟网络、子网、专用 IP 地址、OS 类型和公共 IP 地址。
| | |
