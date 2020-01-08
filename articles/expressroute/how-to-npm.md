---
title: 为 ExpressRoute 线路配置网络性能监视器 - Azure | Microsoft Docs
description: 为 Azure ExpressRoute 线路配置基于云的网络监视 (NPM)。 这包括通过 ExpressRoute 专用对等互连和 Microsoft 对等互连进行监视。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 01/25/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 180075f13be2cc2507a78e3d10a67a49a0c0cb12
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60839957"
---
# <a name="configure-network-performance-monitor-for-expressroute"></a>为 ExpressRoute 配置网络性能监视器

本文可帮助配置网络性能监视器扩展以监视 ExpressRoute。 网络性能监视器 (NPM) 是基于云的网络监视解决方案，用于监视 Azure 云部署和本地位置（分支机构等）之间的连接。 NPM 是 Azure Monitor 日志的一部分。 NPM 可为 ExpressRoute 提供扩展，使你能通过配置为使用专用对等互连或 Microsoft 对等互连的 ExpressRoute 线路监视网络性能。 为 ExpressRoute 配置 NPM 后，可以检测到需要识别和消除的网络问题。 此服务也是适用于 Azure 政府云。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

可以：

* 跨多种 VNet 监视数据丢失和延迟情况并设置警报

* 监视网络上的所有路径（包括冗余路径）

* 对难以复制且在特定时间点出现的暂时性网络问题进行故障排除

* 帮助确定网络上导致性能下降的特定分段

* 获取每个虚拟网络的吞吐量（如果已在每个 VNet 中安装代理）

* 查看之前某一时间的 ExpressRoute 系统状态

## <a name="workflow"></a>工作流

监视本地和 Azure 中的多个服务器上安装的代理。 代理相互通信，但不会发送数据，而是发送 TCP 握手数据包。 通过代理间的通信，Azure 可以映射流量可能经过的网络拓扑和路径。

1. 创建 NPM 工作区。 这与 Log Analytics 工作区相同。
2. 安装和配置软件代理。 （如果只想通过 Microsoft 对等互连进行监视，则无需安装和配置软件代理。）： 
    * 在本地服务器和 Azure VM 上安装监视代理（用于专用对等互连）。
    * 在监视代理服务器上配置设置，允许监视代理进行通信。 （打开防火墙端口等）
3. 配置网络安全组 (NSG) 规则，允许 Azure VM 上安装的监视代理与本地监视代理进行通信。
4. 设置监视：自动发现和管理在 NPM 中可见的网络。

如果已使用网络性能监视器监视其他对象或服务，并且在一个受支持区域已有工作区，则可以跳过步骤 1 和步骤 2，从步骤 3 开始配置。

## <a name="configure"></a>步骤 1：创建工作区

在具有连接到 ExpressRoute 线路的 VNet 链路的订阅中创建一个工作区。

1. 在[Azure 门户](https://portal.azure.com)，选择 Vnet 的订阅到 ExpressRoute 线路对等。 然后从市场服务列表中搜索“网络性能监视器”  。 在返回结果中，单击打开“网络性能监视器”页面  。

   >[!NOTE]
   >可以创建新的工作区或使用现有的工作区。 如果想要使用现有工作区，则必须确保工作区已迁移到新的查询语言。 [详细信息...](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-upgrade)
   >

   ![portal](./media/how-to-npm/3.png)<br><br>
2. 在“网络性能监视器”主页底部，单击“创建”打开“网络性能监视器 - 创建新解决方案”页面    。 单击“Log Analytics 工作区 - 选择工作区”  打开“工作区”页面。 单击“+ 创建新工作区”打开“工作区”页面  。
3. 上**Log Analytics 工作区**页上，选择**创建新**，然后配置以下设置：

   * Log Analytics 工作区 - 键入工作区的名称。
   * “订阅”- 若有多个订阅，请选择要与新工作区相关联的订阅。
   * “资源组”- 创建一个资源组或使用现有资源组。
   * 位置 - 此位置用于指定代理连接日志所用的存储帐户的位置。
   * 定价层 - 选择定价层。
  
     >[!NOTE]
     >ExpressRoute 线路可以位于世界的任何位置。 它不一定要在工作区所在的同一区域。
     >
  
     ![工作区](./media/how-to-npm/4.png)<br><br>
4. 单击“确定”保存和部署设置模板  。 模板验证后，单击“创建”以部署工作区  。
5. 部署工作区后，导航到创建的“NetworkMonitoring(名称)”资源  。 验证设置，然后单击“解决方案需要其他配置”  。

   ![其他配置](./media/how-to-npm/5.png)

## <a name="agents"></a>步骤 2：安装并配置代理

### <a name="download"></a>2.1：下载代理安装程序文件

1. 转到资源的“网络性能监视器配置”页面的“通用设置”选项卡。   从“安装 Log Analytics 代理”  部分中选择与你的服务器的处理器对应的代理，并下载安装文件。
2. 接下来，将“工作区 ID”和“主密钥”复制到记事本   。
3. 从“将 Log Analytics 代理配置为使用 TCP 协议进行监视”  部分中，下载 Powershell 脚本。 PowerShell 脚本可帮助你打开与 TCP 事务相关的防火墙端口。

   ![PowerShell 脚本](./media/how-to-npm/7.png)

### <a name="installagent"></a>2.2：在每个监视服务器上（在要监视的每个 VNET 上）安装监视代理

我们建议在 ExpressRoute 连接的每一端至少安装两个代理来实现冗余（例如，本地或 Azure VNET）。 必须在 Windows Server（2008 SP1 或更高版本）上安装代理。 不支持使用 Windows 桌面 OS 和 Linux OS 监视 ExpressRoute 线路。 使用以下步骤安装代理：
   
  >[!NOTE]
  >如果 SCOM 推送的代理（包括 [MMA](https://technet.microsoft.com/library/dn465154(v=sc.12).aspx)）在 Azure 中托管，可能无法持续检测其位置。 建议不要在 Azure VNET 中使用这些代理来监视 ExpressRoute。
  >

1. 运行安装程序，在要用于监视 ExpressRoute 的每个服务器上安装代理  。 用于监视的服务器可以是 VM 或本地服务器，并且必须连接 Internet。 需要至少在本地安装一个代理，并在 Azure 中在要监视的每个网络段上安装一个代理。
2. 在“欢迎”页面上，单击“下一步”。  
3. 在“许可条款”页面上阅读许可协议，然后单击“我接受”   。
4. 在“目标文件夹”页面上更改或保留默认安装文件夹，然后单击“下一步”   。
5. 上**代理安装选项**页上，你可以选择将代理连接到 Azure Monitor 日志或 Operations Manager。 或者，如果希望稍后配置代理，也可以将选项留空。 完成选择后，单击“下一步”  。

   * 如果选择连接到 Azure Log Analytics，请粘贴在前一部分中复制到记事本的“工作区 ID”和“工作区密钥”（主密钥）    。 然后单击“下一步”。 

     ![ID 和密钥](./media/how-to-npm/8.png)
   * 如果选择连接到 Operations Manager，请在“管理组配置”页面键入“管理组名称”、“管理服务器”和“管理服务器端口”      。 然后单击“下一步”。 

     ![Operations Manager](./media/how-to-npm/9.png)
   * 在“代理操作帐户”页面，选择“本地系统”帐户或“域或本地计算机帐户”    。 然后单击“下一步”。 

     ![帐户](./media/how-to-npm/10.png)
6. 在“准备安装”页上检查所做的选择，并单击“安装”   。
7. 在“配置已成功完成”页上，单击“完成”。  
8. 完成后，Microsoft Monitoring Agent 将显示在“控制面板”中。 您可以查看你的配置，并验证代理连接到 Azure Monitor 日志。 连接后，代理将显示一条消息，声明：“Microsoft Monitoring Agent 已成功连接到 Microsoft Operations Management Suite 服务”  。

9. 针对需要监视的每个 VNET 重复上述过程。

### <a name="proxy"></a>2.3：配置代理设置（可选）

如果要使用 Web 代理访问 Internet，请执行以下步骤为 Microsoft Monitoring Agent 配置代理设置。 针对每个服务器执行这些步骤。 如果需要配置多台服务器，使用脚本自动执行此过程可能更加轻松。 如果是此情况，请参阅[使用脚本为 Microsoft Monitoring Agent 配置代理设置](../log-analytics/log-analytics-windows-agent.md)。

使用控制面板为 Microsoft Monitoring Agent 配置代理设置：

1. 打开“控制面板”  。
2. 打开“Microsoft Monitoring Agent” 
3. 单击“代理设置”  选项卡。
4. 选择“使用代理服务器”并键入 URL 和端口号（如果需要）  。 如果代理服务器要求身份验证，请键入用户名和密码以访问代理服务器。

   ![proxy](./media/how-to-npm/11.png)

### <a name="verifyagent"></a>2.4：验证代理连接性

可以轻松验证代理是否正在通信。

1. 在具有监视代理的服务器上，打开“控制面板”  。
2. 打开“Microsoft Monitoring Agent”  。
3. 单击“Azure Log Analytics”选项卡  。
4. 在中**状态**列中，你应看到代理成功连接到 Azure Monitor 日志。

   ![status](./media/how-to-npm/12.png)

### <a name="firewall"></a>2.5：打开监视代理服务器上的防火墙端口

若要使用 TCP 协议，必须打开防火墙端口以确保监视代理可以通信。

可以运行 PowerShell 脚本创建网络性能监视器所需的注册表项。 此脚本还会创建 Windows 防火墙规则，允许监视代理创建彼此之间的 TCP 连接。 该脚本创建的注册表项指定是否记录调试日志和该日志文件的路径。 还会定义用于通信的代理 TCP 端口。 该脚本会自动设置这些注册表项的值。 不要手动更改这些注册表项。

8084 端口默认打开。 通过向该脚本提供参数“portNumber”即可使用自定义端口。 但是，如果这样做，则必须为运行脚本的所有服务器指定同一端口。

>[!NOTE]
>“EnableRules”PowerShell 脚本仅在运行该脚本的服务器上配置 Windows 防火墙规则。 如果有网络防火墙，应确保该防火墙允许流量去往网络性能监视器正在使用的 TCP 端口。
>
>

在代理服务器上，使用管理权限打开 PowerShell 窗口。 运行 [EnableRules](https://aka.ms/npmpowershellscript) PowerShell 脚本（之前已下载）。 不要使用任何参数。

![PowerShell_Script](./media/how-to-npm/script.png)

## <a name="opennsg"></a>步骤 3：配置网络安全组规则

若要监视 Azure 中的代理服务器，必须配置网络安全组 (NSG) 规则，允许将 NPM 在端口上使用的 TCP 流量用于综合事务。 默认端口为 8084。 通过此操作，Azure VM 上安装的监视代理将可与本地监视代理进行通信。

有关 NSG 的详细信息，请参阅[网络安全组](../virtual-network/virtual-networks-create-nsg-arm-portal.md)。

>[!NOTE]
>请确保已安装代理（包括本地服务器代理和 Azure 服务器代理），并且在执行此步骤前已运行 PowerShell 脚本。
>

## <a name="setupmonitor"></a>步骤 4：发现对等互连

1. 转到“所有资源”页面，单击已加入允许列表的 NPM 工作区，导航到“网络性能监视器”概述磁贴  。

   ![npm 工作区](./media/how-to-npm/npm.png)
2. 单击“网络性能监视器概述”磁贴，调出仪表板  。 仪表板包含一个 ExpressRoute 页面，其中显示 ExpressRoute 处于“未配置状态”。 单击“功能设置”，打开“网络性能监视器”配置页  。

   ![功能设置](./media/how-to-npm/npm2.png)
3. 在配置页面，导航到左侧面板上的“ExpressRoute 对等互连”选项卡。 接下来，单击“立即发现”  。

   ![发现](./media/how-to-npm/13.png)
4. 完成发现后，会看到包含以下项的列表：
   * ExpressRoute 线路中与此订阅关联的所有 Microsoft 对等互连。
   * 连接到与此订阅关联的 VNet 的所有专用对等互连。
            
## <a name="configmonitor"></a>步骤 5：配置监视器

在本部分配置监视器。 根据想要监视的对等互连类型遵循相应的步骤：**专用对等互连**或 **Microsoft 对等互连**。

### <a name="private-peering"></a>专用对等互连

对于专用对等互连，在完成发现后，会看到唯一“线路名称”和“VNet 名称”的规则。   这些规则起初是禁用的。

![规则](./media/how-to-npm/14.png)

1. 选中“监视此对等互连”复选框。 
2. 选中“为此对等互连启用运行状况监视”复选框。 
3. 选择监视条件。 可以通过键入阈值，设置自定义阈值来生成运行状况事件。 只要条件值超出其针对所选网络/子网对选择的阈值，就会生成运行状况事件。
4. 单击“本地代理”>“添加代理”按钮，添加要从中监视专用对等互连的本地服务器。  确保只选择已与本部分步骤 2 中指定的 Microsoft 服务终结点建立了连接的代理。 本地代理必须能够使用 ExpressRoute 连接访问该终结点。
5. 保存设置。
6. 启用规则并选择要监视的的值和代理后，需要等待约 30-60 分钟后值才会开始填充，“ExpressRoute 监视”磁贴也将变为可用  。

### <a name="microsoft-peering"></a>Microsoft 对等互连

对于 Microsoft 对等互连，请单击要监视的 Microsoft 对等互连，并配置设置。

1. 选中“监视此对等互连”复选框。  
2. （可选）可以更改目标 Microsoft 服务终结点。 默认情况下，NPM 会选择一个 Microsoft 服务终结点作为目标。 NPM 会监视通过 ExpressRoute 建立的从本地服务器到此目标终结点的连接。 
    * 若要更改此目标终结点，请单击“目标:”下的“(编辑)”链接，然后从 URL 列表中选择另一个 Microsoft 服务目标终结点。  
      ![编辑目标](./media/how-to-npm/edit_target.png)<br>

    * 可以使用自定义 URL 或 IP 地址。 如果使用 Microsoft 对等互连来与公共 IP 地址上提供的 Azure PaaS 服务（例如 Azure 存储、SQL 数据库和网站）建立连接，则此选项特别有用。 为此，请单击 URL 列表底部的“(改用自定义 URL 或 IP 地址)”链接，然后输入通过 ExpressRoute Microsoft 对等互连连接的 Azure PaaS 服务的公共终结点。 
    ![自定义 URL](./media/how-to-npm/custom_url.png)<br>

    * 如果使用这些可选设置，请确保只在此处选择 Microsoft 服务终结点。 该终结点必须连接到 ExpressRoute，并且可由本地代理访问。
3. 选中“为此对等互连启用运行状况监视”复选框。 
4. 选择监视条件。 可以通过键入阈值，设置自定义阈值来生成运行状况事件。 只要条件值超出其针对所选网络/子网对选择的阈值，就会生成运行状况事件。
5. 单击“本地代理”>“添加代理”按钮，添加要从中监视 Microsoft 对等互连的本地服务器。  确保只选择已与本部分步骤 2 中指定的 Microsoft 服务终结点建立了连接的代理。 本地代理必须能够使用 ExpressRoute 连接访问该终结点。
6. 保存设置。
7. 启用规则并选择要监视的的值和代理后，需要等待约 30-60 分钟后值才会开始填充，“ExpressRoute 监视”磁贴也将变为可用  。

## <a name="explore"></a>步骤 6：查看监视磁贴

看到监视磁铁后，NPM 也将开始监视 ExpressRoute 线路和连接资源。 可以单击“Microsoft 对等互连”磁贴来深入了解 Microsoft 对等互连的运行状况。

![监视磁贴](./media/how-to-npm/15.png)

### <a name="dashboard"></a>网络性能监视器页面

NPM 页面包含一个 ExpressRoute 页面，其中显示 ExpressRoute 线路和对等互连的运行状况。

![仪表板](./media/how-to-npm/dashboard.png)

### <a name="circuits"></a>线路的列表

若要查看所有受监视的 ExpressRoute 线路的列表，请单击“ExpressRoute 线路”磁贴。  可以选择一条线路并查看其运行状态以及数据包丢失、带宽使用率和延迟的趋势图表。 这些图表是交互式的。 可以选择自定义时间段来绘制图表。 可以在图表上方区域拖动鼠标来放大图表，详细查看数据点。

![circuit_list](./media/how-to-npm/circuits.png)

#### <a name="trend"></a>丢失、延迟和吞吐量的趋势

带宽、延迟和丢失图表是交互式的。 可以使用鼠标控件放大这些图表的任何部分。 单击左上角“操作”按钮下的“日期/时间”，还可以查看其它时间间隔内的带宽、延迟和数据丢失趋势  。

![趋势](./media/how-to-npm/16.png)

### <a name="peerings"></a>对等互连列表

若要查看通过专用对等互连建立的到虚拟网络的所有连接的列表，请单击仪表板上的“专用对等互连”磁贴。  可以在此处选择一个虚拟网络连接，并查看其运行状态以及数据包丢失、带宽使用率和延迟的趋势图表。

![线路列表](./media/how-to-npm/peerings.png)

### <a name="nodes"></a>节点视图

若要查看所选 ExpressRoute 对等互连的在本地节点与 Azure VM/Microsoft 服务终结点之间的所有链接列表，请单击“查看节点链接”。  可以查看每个链接的运行状况，及其关联的丢包和延迟趋势。

![节点视图](./media/how-to-npm/nodes.png)

### <a name="topology"></a>线路拓扑

若要查看线路拓扑，请单击“拓扑”磁贴  。 你将转到所选线路或对等互连的拓扑视图。 拓扑图提供网络中每个网段的延迟。 每个第 3 层跃点由图中的某个节点表示。 单击跃点即可查看该跃点的详细信息。

通过移动“筛选器”下的滚动条，可以增加可见级别以包含本地跃点  。 向左/右移动滚动条即可增加/减少拓扑图中的跃点数。 还将显示每个分段的延迟情况，据此可以更快地隔离网络中的高延迟分段。

![筛选器](./media/how-to-npm/topology.png)

#### <a name="detailed-topology-view-of-a-circuit"></a>线路的详细拓扑图视图

此视图显示 VNet 连接。
![详细拓扑图](./media/how-to-npm/17.png)
