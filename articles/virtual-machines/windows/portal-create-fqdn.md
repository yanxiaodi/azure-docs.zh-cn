---
title: 在 Azure 门户中为 Windows VM 创建 FQDN | Microsoft 文档
description: 了解如何在 Azure 门户中为基于 Resource Manager 的虚拟机创建完全限定域名或 FQDN。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: a2ae5887-76df-485e-ae19-0efd96df8600
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c86e2739efd951ad9274f2fa0829b9fd7e13e3e5
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102595"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-windows-vm"></a>在 Azure 门户中为 Windows VM 创建完全限定的域名

在 [Azure 门户](https://portal.azure.com)中创建虚拟机 (VM) 时，此门户会自动为虚拟机创建公共 IP 资源。 可以使用此 IP 地址远程访问 VM。 虽然门户不会创建[完全限定的域名](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)（简称 FQDN），但可以在创建 VM 后创建一个完全限定的域名。 本文演示了创建 DNS 名称或 FQDN 的步骤。

## <a name="create-a-fqdn"></a>创建 FQDN
阅读本文的前提是已创建 VM。 如果需要，可以[在门户中创建 VM](quick-create-portal.md)或[通过 Azure PowerShell 创建 VM](quick-create-powershell.md)。 在启动和运行 VM 后，请执行下列步骤：

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

现在可以使用此 DNS 名称（例如远程桌面协议 (RDP)）远程连接至 VM。

## <a name="next-steps"></a>后续步骤
你的 VM 已经有公共 IP 和 DNS 名称，现在可以部署常用应用程序框架或服务，例如 IIS、SQL 或 SharePoint。

也可以深入了解如何[使用 Resource Manager](../../azure-resource-manager/resource-group-overview.md)，以获取生成 Azure 部署的相关提示。

