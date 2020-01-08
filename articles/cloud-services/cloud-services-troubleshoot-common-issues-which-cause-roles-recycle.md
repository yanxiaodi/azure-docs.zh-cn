---
title: 云服务角色回收的常见原因 | Microsoft Docs
description: 突然回收的云服务角色可能会导致严重停机。 以下是导致角色回收的一些常见问题，解决这些问题将有助于减少停机。
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 533930d1-8035-4402-b16a-cf887b2c4f85
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 554508b1bf784e306cd12a4a601f908e06320933
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2019
ms.locfileid: "71154977"
---
# <a name="common-issues-that-cause-roles-to-recycle"></a>导致角色回收的常见问题
本文讨论部署问题的一些常见原因，并提供故障排除技巧以帮助你解决这些问题。 当角色实例无法启动，或者在“正在初始化”、“忙”和“正在停止”状态之间循环时，则指示应用程序存在问题。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-runtime-dependencies"></a>缺少运行时依赖项
如果应用程序中的某个角色依赖于某个程序集，而该程序集不属 .NET Framework 或 Azure 托管库的一部分，则必须在应用程序包中显式包括该程序集。 请记住，默认情况下，其他 Microsoft 框架在 Azure 上不可用。 如果角色依赖于此类框架，则必须将这些程序集添加到应用程序包。

生成和打包应用程序之前，请验证以下各项：

* 如果使用 Visual Studio，请确保针对项目中每个不属于 Azure SDK 或 .NET Framework 的引用程序集，将其“复制本地”属性设置为“True”。
* 请确保 web.config 文件在 compilation 元素中未引用任何未使用的程序集。
* 每个 .cshtml 文件的“生成操作”将设置为“内容”。 这可确保这些文件会正确显示在包中，并允许其他引用的文件显示在包中。

## <a name="assembly-targets-wrong-platform"></a>程序集针对的平台错误
Azure 是一个 64 位的环境。 因此，针对 32 位目标编译的 .NET 程序集不会在 Azure 上运行。

## <a name="role-throws-unhandled-exceptions-while-initializing-or-stopping"></a>角色在初始化或停止时引发未处理异常
任何通过 [RoleEntryPoint] 类的方法（包括 [OnStart]、[OnStop] 和 [Run] 方法）引发的异常均属未处理异常。 如果这些方法中的某个方法出现未处理异常，则会对角色进行回收。 如果角色处于反复回收的状态，则每次该角色尝试启动时，都可能会引发未处理异常。

## <a name="role-returns-from-run-method"></a>角色从 Run 方法返回
[Run] 方法会不限次数地运行。 如果代码重写了 [Run] 方法，该方法会无限地休眠下去。 如果 [Run] 方法返回，则角色会回收。

## <a name="incorrect-diagnosticsconnectionstring-setting"></a>不正确的 DiagnosticsConnectionString 设置
如果应用程序使用 Azure 诊断，则服务配置文件必须指定 `DiagnosticsConnectionString` 配置设置。 此设置应在 Azure 中指定一个连接到存储帐户的 HTTPS 连接。

将应用程序包部署到 Azure 之前，要确保 `DiagnosticsConnectionString` 设置正确，请验证以下内容：  

* `DiagnosticsConnectionString` 设置指向 Azure 中的有效存储帐户。  
  默认情况下，此设置指向模拟的存储帐户中，因此必须在部署应用程序包之前显式更改此设置。 如果不更改此设置，则角色实例尝试启动诊断监视器时，会引发异常。 这可能导致角色实例无限期回收。
* 连接字符串是使用以下[格式](../storage/common/storage-configure-connection-string.md)指定的。 （协议必须指定为 HTTPS。）将 *MyAccountName* 替换为存储帐户名称，将 *MyAccountKey* 替换为访问密钥：    

        DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey

  如果要使用 Azure Tools for Microsoft Visual Studio 来开发应用程序，则可使用属性页设置此值。

## <a name="exported-certificate-does-not-include-private-key"></a>导出的证书不含私钥
若要在 SSL 下运行 Web 角色，必须确保导出的管理证书包含私钥。 如果使用 *Windows 证书管理器*来导出证书，请务必对“**导出私钥**”选项选择“**是**”。 该证书必须以 PFX 格式导出，这是当前支持的唯一格式。

## <a name="next-steps"></a>后续步骤
查看更多针对云服务的[故障排除文章](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services)。

在 [Kevin Williamson 博客系列](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx)中查看更多角色回收方案。

[RoleEntryPoint]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx
[OnStart]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx
[OnStop]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[Run]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx
