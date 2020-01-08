---
title: Azure PowerShell 示例 - 应用服务 | Microsoft 文档
description: Azure PowerShell 示例 - 应用服务
services: app-service
documentationcenter: app-service
author: syntaxc4
manager: erikre
editor: ggailey777
tags: azure-service-management
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.service: app-service
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 03/08/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 322820976f7f693ff09c071fc28f1ef3d1d472cf
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074086"
---
# <a name="powershell-samples-for-azure-app-service"></a>Azure 应用服务的 PowerShell 示例

下表包含指向使用 Azure PowerShell 生成的 PowerShell 脚本的链接。

| | |
|-|-|
|**创建应用**||
| [从 GitHub 使用部署创建应用](./scripts/powershell-deploy-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 创建从 GitHub 提取代码的应用服务应用。 |
| [从 GitHub 使用连续部署创建应用](./scripts/powershell-continuous-deployment-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 创建从 GitHub 持续部署代码的应用服务应用。 |
| [使用 FTP 创建应用并部署代码](./scripts/powershell-deploy-ftp.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 使用 FTP 从本地目录创建应用服务应用并上传文件。 |
| [从本地 Git 存储库创建应用并部署代码](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 创建应用服务应用并配置从本地 Git 存储库进行的代码推送。 |
| [创建应用并将代码部署到过渡环境](./scripts/powershell-deploy-staging-environment.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 创建应用服务应用并为其配置用于暂存代码更改的部署槽位。 |
|**配置应用**||
| [将自定义域映射到应用](./scripts/powershell-configure-custom-domain.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 创建应用服务应用并将自定义域名映射到它。 |
| [将自定义 SSL 证书绑定到应用](./scripts/powershell-configure-ssl-certificate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 创建应用服务应用并将自定义域名的 SSL 证书绑定到它。 |
|**缩放应用**||
| [手动缩放应用](./scripts/powershell-scale-manual.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 创建应用服务应用并将其在 2 个实例之间进行缩放。 |
| [缩放具有高可用性体系结构的全球应用](./scripts/powershell-scale-high-availability.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 在两个不同地理区域中创建两个应用服务应用，并使用 Azure 流量管理器通过单个终结点使其可用。 |
|**将应用连接到资源**||
| [将应用连接到 SQL 数据库](./scripts/powershell-connect-to-sql.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 创建应用服务应用和 SQL 数据库，然后将数据库连接字符串添加到应用设置。 |
| [将应用连接到存储帐户](./scripts/powershell-connect-to-storage.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 创建应用服务应用和存储帐户，然后将存储连接字符串添加到应用设置。 |
|**备份和还原应用**||
| [备份应用](./scripts/powershell-backup-onetime.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 创建应用服务应用，并为其创建一次性备份。 |
| [为应用创建计划备份](./scripts/powershell-backup-scheduled.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 创建应用服务应用，并为其创建计划的备份。 |
| [删除应用的备份](./scripts/powershell-backup-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 删除应用的现有备份。 |
| [从备份中还原应用](./scripts/powershell-backup-restore.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 从以前完成的备份中还原应用。 |
| [跨订阅还原备份](./scripts/powershell-backup-restore-diff-sub.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 从另一订阅的备份中还原 Web 应用。 |
|**监视应用**||
| [使用 Web 服务器日志监视应用](./scripts/powershell-monitor.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 创建应用服务应用，为其启用日志记录，并将日志下载到本地计算机。 |
| | |
