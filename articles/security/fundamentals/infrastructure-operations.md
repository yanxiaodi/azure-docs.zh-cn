---
title: Azure 生产网络管理-Microsoft Azure
description: 本文介绍 Microsoft 如何管理和操作 Azure 生产网络来保护 Azure 数据中心。
services: security
documentationcenter: n
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2019
ms.author: terrylan
ms.openlocfilehash: d41fe409b4a44a4c2af3670d76dd3a83a300feae
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727116"
---
# <a name="management-and-operation-of-the-azure-production-network"></a>Azure 生产网络的管理和操作    
本文介绍 Microsoft 如何管理和操作 Azure 生产网络来保护 Azure 数据中心。

## <a name="monitor-log-and-report"></a>监视器、日志和报表

Azure 生产网络的管理和操作需要在 Azure 运营团队与 Azure SQL 数据库之间做出协调。 团队在环境中使用了多个系统和应用程序性能监视工具。 他们使用适当的工具来监视网络设备、服务器、服务和应用程序进程。

为确保安全执行 Azure 环境中运行的服务，运营团队实施多种级别的监视、日志记录和报告，包括以下操作：

- 首先，Microsoft Monitoring Agent (MMA) 从多个位置（包括结构控制器 (FC) 和根操作系统 (OS)）收集监视和诊断日志信息，并将其写入日志文件中。 该代理最终会将一部分摘要信息推送到预配置的 Azure 存储帐户中。 此外，独立监视和诊断服务会读取各种监视和诊断日志数据并汇总信息。 监视和诊断服务将信息写入集成日志。 Azure 使用定制的 Azure 安全监视，这是 Azure 监视系统的一个扩展。 ASM 中的组件可以从平台中的各个位置观察、分析和报告安全相关的事件。

- Azure SQL 数据库 Windows Fabric 平台为 Azure SQL 数据库提供管理、部署、开发和操作监督服务。 该平台提供分布式多步骤部署服务、运行状况监视、自动修复和服务版本符合性。 它提供以下服务：

   - 服务建模功能和高保真开发环境（数据中心群集的成本高昂且能力不足）。
   - 一键式部署和升级工作流，用于执行服务启动和维护。
   - 运行状况报告和自动化修复工作流，可实现自我修复。
   - 跨分布式系统节点的实时监视、警报和调试工具。
   - 集中收集操作数据和指标，以提供分散式的根本原因分析和服务见解。
   - 用于部署、变更管理和监视的操作工具。
   - Azure SQL 数据库 Windows Fabric 平台和监视器脚本持续实时运行并监视。

如果出现任何异常，将会激活事件响应过程，Azure 事件会审团需遵循此过程。 相应的 Azure 支持人员会收到响应事件的通知。 在集中式票证系统中阐述和管理问题跟踪与解决方法。 根据保密协议 (NDA) 和客户请求提供系统正常运行时间指标。

## <a name="corporate-network-and-multi-factor-access-to-production"></a>通过企业网络和多重身份验证访问生产环境
企业网络用户群包括 Azure 支持人员。 企业网络支持内部企业职能，并提供 Azure 客户支持人员所用的内部应用程序的访问权限。 企业网络在物理上和逻辑上与 Azure 生产网络分离。 Azure 人员使用 Azure 工作站和笔记本电脑访问企业网络。 所有用户必须有一个 Azure Active Directory (Azure AD) 帐户（包括用户名和密码）才能访问企业网络资源。 企业网络访问使用 Azure AD 帐户，该帐户发布给所有 Microsoft 人员、承包商和供应商，并由 Microsoft 信息技术管理。 唯一的用户标识符根据用户在 Microsoft 的雇佣关系状态来区分用户。

通过 Active Directory 联合身份验证服务 (AD FS) 进行身份验证来控制对内部 Azure 应用程序的访问。 AD FS 是由 Microsoft 信息技术托管的服务，它通过应用安全令牌和用户声明来提供企业网络用户的身份验证。 AD FS 使内部 Azure 应用程序能够针对 Microsoft 公司 Active Directory 域对用户进行身份验证。 若要从公司网络环境访问生产网络，用户必须使用多重身份验证进行身份验证。

## <a name="next-steps"></a>后续步骤
若要详细了解 Microsoft 如何保护 Azure 基础结构，请参阅：

- [Azure 设施、场地和物理安全性](physical-security.md)
- [Azure 基础结构可用性](infrastructure-availability.md)
- [Azure 信息系统的组件和边界](infrastructure-components.md)
- [Azure 网络体系结构](infrastructure-network.md)
- [Azure 生产网络](production-network.md)
- [Azure SQL 数据库安全功能](infrastructure-sql.md)
- [Azure 基础结构监视](infrastructure-monitoring.md)
- [Azure 基础结构完整性](infrastructure-integrity.md)
- [Azure 客户数据保护](protection-customer-data.md)
