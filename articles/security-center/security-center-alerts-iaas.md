---
title: Azure 安全中心 Vm 和服务器的威胁检测 |Microsoft Docs
description: 本主题介绍 Azure 安全中心提供的 VM 和服务器警报。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: dd2eb069-4c76-4154-96bb-6e6ae553ef46
ms.service: security-center
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: memildin
ms.openlocfilehash: 50d4023b09eb14fcfafe752ca60b8e888acc1fef
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71273886"
---
# <a name="threat-detection-for-vms-and-servers-in-azure-security-center"></a>Azure 安全中心 Vm 和服务器的威胁检测

本主题介绍可用于具有以下操作系统的 Vm 和服务器的不同类型的检测方法和警报。 有关支持的版本的列表, 请参阅[Azure 安全中心支持的平台和功能](https://docs.microsoft.com/azure/security-center/security-center-os-coverage)。

* [Windows](#windows-machines)
* [Linux](#linux-machines)

## Windows<a name="windows-machines"></a>

Azure 安全中心与 Azure 服务集成，以监视和保护基于 Windows 的计算机。 安全中心以一种易于使用的格式从所有这些服务提供警报和修正建议。

### Microsoft Defender ATP<a nanme="windows-atp"></a>

安全中心通过与 Microsoft Defender 高级威胁防护（ATP）集成来扩展其云工作负荷保护平台。 这提供了全面的端点检测和响应（EDR）功能。

> [!NOTE]
> 使用安全中心的 Windows 服务器上会自动启用 Microsoft Defender ATP 传感器。

当 Microsoft Defender ATP 检测到威胁时，会触发警报。 警报显示在安全中心仪表板上。 在仪表板中，可以透视到 Microsoft Defender ATP 控制台，并执行详细的调查以发现攻击范围。 有关 Microsoft Defender ATP 的详细信息，请参阅将[服务器载入 Microsoft DEFENDER atp 服务](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)。

### 故障转储分析<a nanme="windows-dump"></a>

当软件故障时，故障转储可捕获故障时的部分内存。

故障可能是恶意软件或包含恶意软件造成的。 为避免被安全产品检测到, 各种形式的恶意软件使用 fileless 攻击, 这可避免写入磁盘或加密写入磁盘的软件组件。 使用传统的基于磁盘的方法很难检测到这种类型的攻击。

但是，通过使用内存分析，可以检测到这种攻击。 通过分析故障转储中的内存，安全中心可以检测到攻击所使用的技术。 例如，攻击可能试图利用软件中的漏洞、访问机密数据以及偷偷在受损的计算机中保持不变。 安全中心执行此操作时，对主机性能的影响最小。

> [!div class="mx-tableFixed"]

|警报|描述|
|---|---|
|**已发现代码注入**|代码注入是将可执行模块插入到正在运行的进程或线程中。 恶意软件使用此方法来访问数据, 同时成功地隐藏自身以防止找到和删除。 <br/>此警报指示故障转储中存在注入模块。 为了区分恶意和非恶意注入的模块, 安全中心会检查注入的模块是否符合可疑行为的配置文件。|
|**发现可疑代码段**|指示已使用非标准方法（如反射注入和进程替换所）分配了代码段。 该警报提供代码段的其他特征, 这些特征已经过处理, 可为所报告代码段的功能和行为提供上下文。|
|**已发现外壳代码**|Shellcode 是在恶意软件利用软件漏洞之后运行的有效负载。<br/>此警报指示故障转储分析检测到可执行代码, 该代码展示了恶意有效负载通常会执行的行为。 尽管非恶意软件也可以执行此行为，但它不是一般的软件开发实践。|

### Fileless 攻击检测<a nanme="windows-fileless"></a>

针对终结点的 Fileless 攻击很常见。 为了避免检测, fileless 攻击会将恶意有效负载注入内存。 攻击者负载会在遭到入侵的进程中保持不变，并执行各种恶意活动。

通过 fileless 攻击检测, 自动内存鉴证技术识别 fileless 攻击工具包、技术和行为。 此解决方案会定期在运行时扫描计算机，并直接从安全关键进程的内存中提取见解。

它找到了利用、代码注入和恶意负载执行的证据。 Fileless 攻击检测生成详细的安全警报，以加速警报会审、相关和下游响应时间。 此方法补充基于事件的 EDR 解决方案，提供更高的检测范围。

> [!NOTE]
> 可以通过下载[Azure 安全中心操作手册来模拟 Windows 警报:安全警报](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)。

> [!div class="mx-tableFixed"]

|警报|描述|
|---|---|
|**检测到的 Fileless 攻击方法**|指定进程的内存包含 fileless 攻击工具包：Meterpreter. Fileless 攻击工具包通常不会在文件系统上存在，这使得传统的防病毒软件难以检测。|

### <a name="further-reading"></a>延伸阅读

有关安全中心检测的示例和详细信息，请参阅：

* [Azure 安全中心如何自动检测网络攻击](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [Azure 安全中心如何使用管理工具检测漏洞](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

## Linux<a name="linux-machines"></a>

安全中心使用**审核**（一个最常见的 Linux 审核框架）从 linux 计算机收集审核记录。 审核位于主线内核中。 

### Linux 审核警报和 Microsoft Monitoring Agent (MMA) 集成<a name="linux-auditd"></a>

审核系统包含一个内核级别子系统，该子系统负责监视系统调用。 它按指定的规则集筛选它们，并将其消息写入套接字。 安全中心在 Microsoft Monitoring Agent (MMA) 内集成审核包中的功能。 通过这种集成，可在所有受支持的 Linux 发行版中收集审核事件，无需任何先决条件。  

使用 Linux MMA 代理收集、充实并聚合审核记录到事件中。 安全中心会持续添加使用 Linux 信号检测云和本地 Linux 计算机上的恶意行为的新分析。 类似于 Windows 功能，这些分析跨越可疑进程、可疑登录尝试、内核模块加载和其他活动。 这些活动可以指示计算机受到攻击或已被破坏。  

以下是跨攻击生命周期的各个阶段的分析的几个示例。

> [!div class="mx-tableFixed"]

|警报|描述|
|---|---|
|**以异常方式访问 SSH 授权密钥文件的进程**|已在类似于已知恶意软件活动的方法中访问了 SSH 授权密钥文件。 此访问可能表明攻击者正尝试获取计算机的持久访问权限。|
|**检测到的永久性尝试**|主机数据分析检测到已安装了单用户模式的启动脚本。 <br/>由于在此模式下运行时，很少需要合法的进程，这可能表明攻击者已向每个运行级别添加了恶意进程以保证持久性。|
|**检测到计划任务的操作**|主机数据分析检测到计划任务的可能操作。 攻击者通常会将计划的任务添加到其泄露的计算机上以获得持久性。|
|**可疑文件时间戳修改**|主机数据分析检测到可疑的时间戳修改。 攻击者经常将时间戳从现有的合法文件复制到新工具，以避免检测到这些新删除的文件。|
|**已将新用户添加到 sudoers 组**|主机数据分析检测到已将用户添加到 sudoers 组, 从而使其成员能够运行具有高权限的命令。|
|**可能会在 dhcp 客户端中利用 DynoRoot 漏洞**|主机数据分析检测到执行异常命令，其中包含 dhclient 脚本的父进程。|
|**检测到可疑的内核模块**|主机数据分析检测到作为内核模块加载的共享对象文件。 这可能是合法活动，或者表明某个计算机已经泄露。|
|**检测到与数字货币挖掘关联的进程**|主机数据分析检测到通常与数字货币挖掘关联的进程的执行。|
|**可能端口转发到外部 IP 地址**|主机数据分析检测到启动了到外部 IP 地址的端口转发。|

> [!NOTE]
> 可以通过下载[Azure 安全中心操作手册来模拟 Windows 警报:Linux 检测](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef)。


有关详细信息，请参阅：  

* [利用 Azure 安全中心来检测遭受的 Linux 计算机遭受攻击](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)

* [Azure 安全中心可以检测到 Linux 中的新兴漏洞](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

 