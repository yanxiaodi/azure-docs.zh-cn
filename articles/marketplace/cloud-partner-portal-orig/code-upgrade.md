---
title: 将代码升级到最新的平台 |Azure Marketplace
description: 本主题介绍了如何将 Microsoft Dynamics 365 for Operations 平台版本升级到最新平台版本
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: aedc2c7474de0fe068a329eb2205e9bb08e62c3a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935286"
---
# <a name="upgrading-code-to-the-latest-platform"></a>将代码升级到最新平台

本文介绍了如何将 Microsoft Dynamics 365 for Operations 平台版本升级到最新平台版本。

## <a name="overview"></a>概述

Microsoft Dynamics 365 for Operations 平台由以下组件构成：

Dynamics 365 for Operations 平台二进制文件，如应用程序对象服务器 (AOS)、数据管理框架、报表和商业智能 (BI) 框架、开发工具和分析服务。 以下应用程序对象树 (AOT) 包：

1. 应用程序平台
2. 应用程序基础
3. 测试软件包

**重要**：若要移动到操作平台的最新的 Dynamics 365，操作实现在 Dynamics 365 不能具有任何自定义项 （覆盖） 的任何属于平台的 AOT 包。 为了能够顺畅地持续更新平台，平台更新 3 中引入了此限制。 如果运行的平台版本低于平台更新 3，请参阅本文结尾的“从旧版升级到平台更新 3”部分。

若要详细了解代码升级，请参阅[此处](https://docs.microsoft.com/dynamics365/operations/dev-itpro/migration-upgrade/upgrade-latest-platform-update)。