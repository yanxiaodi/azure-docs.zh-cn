---
title: Ethereum 权威证明联盟 - Azure
description: 使用 Ethereum 权威证明联盟解决方案部署和配置多成员联盟 Ethereum 网络
services: azure-blockchain
keywords: ''
author: CodyBorn
ms.author: coborn
ms.date: 04/08/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: vamelech
ms.openlocfilehash: 01b9f7f74077737ea95a56bbe81f440db425bf0c
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698455"
---
# <a name="ethereum-proof-of-authority-consortium"></a>Ethereum 权威证明联盟

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>概述
[此解决方案](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium)旨在利用最少的 Azure 和 Ethereum 知识使部署、配置和管理多成员联盟权威证明 Ethereum 网络变得更加轻松。

通过 Azure 门户中的少量用户输入和一键式部署，每个成员都可以使用全球范围内的 Microsoft Azure 计算、网络和存储服务来预配网络内存占用。 每个成员的网络内存占用由一组负载均衡验证程序节点组成，应用程序或用户可以通过与这些节点交互来提交 Ethereum 事务。

## <a name="concepts"></a>概念

### <a name="terminology"></a>术语

-   **共识** - 通过块验证和创建同步分布式网络中的数据的行为。

-   **联盟成员** - 参与区块链网络上的共识的实体。

-   **管理员** - 用于管理给定联盟成员的参与行为的 Ethereum 帐户。

-   **验证程序** - 与代表管理员参与共识的 Ethereum 帐户相关的计算机。

### <a name="proof-of-authority"></a>权威证明

对于那些刚刚进入区块链社区的人来说，该解决方案的发布是在 Azure 上以简单的可配置方式了解该技术的绝佳机会。 工作量证明是一种抗 Sybil 机制，利用计算成本对网络进行自我调节并允许公平参与。 这非常适合开放的匿名区块链网络，其中加密货币的竞争提升了网络安全性。 但是，在专用/联盟网络中，基础 Ether 没有值。 证书颁发机构是一种替代协议, 更适用于所有共识参与者已知且可信的允许网络。 无需进行挖矿，权威证明更有效，同时仍保留拜占庭容错。

### <a name="consortium-governance"></a>联盟治理

由于证书颁发机构依赖于允许的网络颁发机构列表来使网络保持正常运行, 因此提供一种公平机制来对此权限列表进行修改非常重要。 每个部署都附带一组智能协定和门户, 适用于此允许列表的链间管理。 一旦提议的更改获得联盟成员的多数票，就会执行更改。 这允许以一种激励诚信网络的透明方式添加新的共识参与者或删除受到损害的参与者。

### <a name="admin-account"></a>管理帐户

在部署证书颁发机构节点时, 系统会要求你提供管理员以太坊地址。 可使用多种不同的机制来生成 Ethereum 帐户并确保其安全。 将此地址添加为网络上的权威后，即可使用此帐户参与治理。 此管理员帐户还可用于将共识参与委托给作为此部署的一部分创建的验证程序节点。 由于仅使用公用以太坊地址, 因此每个管理员都可以灵活地以遵循其所需安全模式的方式保护其私钥。

### <a name="validator-node"></a>验证程序节点

在权威证明协议中，验证程序节点取代了传统的挖掘程序节点。 每个验证程序都有一个添加到智能合同权限列表中的唯一 Ethereum 标识。 验证程序包含在此列表中之后，即可参与块创建过程。 有关此过程的详细信息，请参阅关于[权威系列共识](https://wiki.parity.io/Aura)的奇偶校验文档。 每个联盟成员可以跨五个区域预配两个或更多验证程序节点，用于提供异地冗余。 验证程序节点与其他验证程序节点进行通信以就基础分布式账本的状态达成共识。
为确保网络上的公平参与，每个联盟成员使用的验证程序数不得超过网络上第一个成员使用的验证程序数（如果第一个成员部署三个验证程序，则每个成员最多只能有三个验证程序）。

### <a name="identity-store"></a>标识存储区

由于每个成员都有多个同时运行的验证程序节点, 并且每个节点都必须具有允许的标识, 因此, 验证程序可以安全地获取网络上的唯一活动标识, 这一点很重要。 为了使此过程更容易, 我们构建了一个标识存储, 该存储将部署在每个成员的订阅中, 以安全地保存生成的以太坊标识。 部署时, 业务流程容器将为每个验证程序生成以太坊私钥, 并将其存储在 Azure Key Vault 中。 在奇偶校验节点启动之前，它首先获取对未使用标识的租用，以确保该标识不会由另一个节点获取。 标识提供给为其授予开始创建块的权限的客户端。 如果托管 VM 发生故障，将释放标识租用，允许替换节点在将来恢复其标识。

### <a name="bootnode-registrar"></a>引导节点注册机构

为了便于连接，每个成员都可在[数据 API 终结点](#data-api)上托管一组连接信息。 此数据包括作为联接成员的对等节点提供的 bootnodes 的列表。 作为此数据 API 的一部分，我们将此引导节点列表保持最新状态

### <a name="bring-your-own-operator"></a>自带运营商

联盟成员通常希望参与网络治理，但不希望运营和维护其基础结构。 与传统系统不同，整个网络中只有一个运营商与区块链系统的分散模型是背道而驰的。 每个联盟成员都可以将基础结构管理委托给他们选择的运营商，而不是雇用同一中介来运营网络。 这允许使用混合模型, 其中每个成员都可以选择操作自己的基础结构或将操作委托给不同的合作伙伴。 委托运营工作流的工作方式如下：

1.  联盟成员生成 Ethereum 地址（保存私钥）

2.  联盟成员向运营商提供公共 Ethereum 地址

3.  操作员使用 Azure 资源管理器解决方案部署和配置 PoA 验证程序节点

4.  运营商向联盟成员提供 RPC 和管理终结点

5.  联盟成员使用私钥对请求进行签名，接受运营商已部署并代表他们参与的验证程序节点

### <a name="azure-monitor"></a>Azure Monitor

此解决方案还附带 Azure Monitor，用于跟踪节点和网络统计信息。 对于应用程序开发人员，这提供了对基础区块链的可见性，用于跟踪块生成统计信息。 网络运营商可以使用 Azure Monitor 通过基础结构统计信息和可查询日志快速检测并防止网络中断。 有关详细信息, 请参阅[服务监视](#service-monitoring)。

### <a name="deployment-architecture"></a>部署体系结构

#### <a name="description"></a>描述

此解决方案可以部署基于单个或多个区域的多成员 Ethereum 联盟网络。 默认情况下，可以通过公共 IP 访问 RPC 和对等互连终结点，以简化订阅和云之间的连接。 建议利用[奇偶校验权限协议](https://wiki.parity.io/Permissioning)控制应用程序级访问。 我们还支持在 VPN 后方部署网络，利用 VNet 网关实现跨订阅连接。 这些部署更复杂，因此建议先从公共 IP 模型开始。

#### <a name="consortium-member-overview"></a>联盟成员概述

每个联盟成员部署包括：

-   用于运行 PoA 验证程序的虚拟机

-   用于分配 RPC、对等互连和 Governance DApp 请求的 Azure 负载均衡器

-   用于确保验证程序标识安全的 Azure Key Vault

-   用于托管持久性网络信息并协调租用的 Azure 存储

-   用于聚合日志和性能统计信息的 Azure Monitor

-   VNet 网关（可选），允许跨专用 VNet 的 VPN 连接

![部署体系结构](./media/ethereum-poa-deployment/deployment-architecture.png)

我们利用 Docker 容器提供可靠性和模块性。 我们使用 Azure 容器注册表托管每个部署中包含的版本控制映像，并为其提供服务。 容器映像包括：

-   业务流程协调程序

    -   在部署期间运行一次

    -   生成标识和治理合同

    -   在标识存储区中存储标识

-   奇偶校验客户端

    -   来自标识存储区的租用标识

    -   发现并连接到对等机

-   EthStats 代理

    -   通过 RPC 收集本地日志和统计信息并推送到 Azure Monitor

-   Governance DApp

    -   用于与治理合同进行交互的 Web 界面

## <a name="how-to-guides"></a>操作指南
### <a name="governance-dapp"></a>Governance DApp

权威证明的核心是分散式治理。 Governance DApp 是一组预部署的[智能合同](https://github.com/Azure-Samples/blockchain/tree/master/ethereum-on-azure/)和 Web 应用程序，用于治理网络上的授权机构。
授权机构分为管理员标识和验证程序节点。
管理员有权将共识参与委托给一组验证程序节点。 管理员还可以将投票给网络内外的其他管理员。

![Governance dapp](./media/ethereum-poa-deployment/governance-dapp.png)

-   **分散式治理 -** 网络授权机构的更改由选定的管理员通过链式投票进行管理。

-   **验证程序委派 -** 授权机构可以管理在每个 PoA 部署中设置的验证程序节点。

-   **可审核的更改历史记录 -** 每次更改都记录在区块链上，提供了透明度和可审核性。

#### <a name="getting-started-with-governance"></a>Governance 入门
若要通过管理 DApp 执行任何类型的事务, 需要利用以太坊钱包。  最简单的方法是使用 [MetaMask](https://metamask.io) 等浏览器内电子钱包；但是，由于它们是网络上部署的智能合同，因此还可以自动化与 Governance 合同的交互。

安装 MetaMask 后，在浏览器中导航到 Governance DApp。  可以在部署确认电子邮件中或通过部署输出中的 Azure 门户查找 URL。  如果未安装浏览器内钱包, 将无法执行任何操作;但是, 您仍然可以读取管理员状态。  

#### <a name="becoming-an-admin"></a>成为管理员
如果你是在网络上部署的第一个成员, 则你将自动成为管理员, 而你的奇偶校验节点将作为验证程序列出。  如果加入网络, 你将需要以管理员身份 (超过 50%) 进行投票现有管理员集。  如果你选择不成为管理员，则节点仍将同步并验证区块链；但是，它们不会参与块创建过程。 若要启动投票过程以成为管理员，请单击“提名”并输入 Ethereum 地址和别名。

![提名](./media/ethereum-poa-deployment/governance-dapp-nominate.png)

#### <a name="candidates"></a>候选人
选择“候选人”选项卡将向你显示当前候选管理员集。  一旦候选人的票数超过当前管理员的票数，候选人就会被提升为管理员。若要对候选人进行投票，请选择该行，然后单击顶部的“投票”。  如果要更改投票，则可以选择候选人，然后单击“撤销投票”。

![候选人](./media/ethereum-poa-deployment/governance-dapp-candidates.png)


#### <a name="admins"></a>管理员
“管理员”选项卡将显示当前管理员集并允许你投反对票。  管理员丢失超过 50% 的支持后, 将作为网络上的管理员删除。  此管理员拥有的任何验证程序节点将丢失验证程序状态并成为网络上的事务节点。  可能出于多种原因删除管理员;不过, 它会提前对策略达成一致。

![管理员](./media/ethereum-poa-deployment/governance-dapp-admins.png)

#### <a name="validators"></a>验证程序
选择左侧菜单中的“验证程序”选项卡将显示此实例的当前已部署的奇偶校验节点及其当前状态（节点类型）。  由于此视图表示当前已部署的联合会成员, 因此每个联合会成员将在此列表中具有一组不同的验证器。  如果这是新部署的实例, 并且尚未添加验证程序, 则会显示 "添加验证程序" 选项。  选择此设置将自动选择突破均衡的奇偶校验节点集, 并将其分配给验证器集。  如果已部署的节点数大于允许的容量，则剩余节点将成为网络上的事务节点。

每个验证程序的地址将通过 Azure 中的[标识存储区](#identity-store)自动分配。  如果某个节点出现故障，则将放弃其标识，允许部署中的其他节点来取代它。  这可确保一致参与高度可用。

![验证程序](./media/ethereum-poa-deployment/governance-dapp-validators.png)

#### <a name="consortium-name"></a>联盟名称
任何管理员都可以更新页面顶部显示的联盟名称。  选择左上角的齿轮图标可更新联盟名称。

#### <a name="account-menu"></a>帐户菜单
右上角是 Ethereum 帐户别名和 identicon。  如果你是管理员, 则可以更新别名。

![帐户](./media/ethereum-poa-deployment/governance-dapp-account.png)

### <a name="deploy-ethereum-proof-of-authority"></a>部署 Ethereum 权威证明

下面是多方部署流的示例：

1.  三个成员分别使用 MetaMask 生成 Ethereum 帐户

2.  成员 A 部署 Ethereum PoA，提供 Ethereum 公用地址

3.  成员 A 向成员 B 和成员 C 提供联盟 URL

4.  成员 B 和成员 C 部署 Ethereum PoA，提供其 Ethereum 公用地址和成员 A 的联盟 URL

5.  成员 A 投票赞成成员 B 作为管理员

6.  成员 A 和成员 B 都投票赞成成员 C 作为管理员

此过程需要支持部署多个虚拟机和托管磁盘的 Azure 订阅。 如有必要，请首先[创建一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

获得订阅后，请转到 Azure 门户。 选择“+”、“市场(查看全部)”，并搜索 Ethereum PoA 联盟。

以下部分介绍如何在网络中配置第一个成员的内存占用。 部署流程分为五个步骤：基础、部署区域、网络规模和性能、Ethereum 设置、Azure Monitor。

#### <a name="basics"></a>基本

在“基础”部分，为全部部署指定标准参数的值，例如订阅、资源组和基本虚拟机属性。

每个参数的详细说明如下：

参数名称|描述|允许值|默认值
---|---|---|---
新建网络还是加入现有网络？|新建网络或加入预先存在的联盟网络|新建 加入现有|新建
电子邮件地址（可选）|部署完成后，可收到包含相关部署信息的电子邮件通知。|有效的电子邮件地址|不可用
VM 用户名|部署的每个 VM 的管理员用户名（仅限字母数字字符）|1-64 个字符|不可用
身份验证类型|对虚拟机进行身份验证的方法。|密码或 SSH 公钥|密码
密码（身份验证类型 = 密码）|部署的每个虚拟机的管理员帐户密码。  密码必须包含以下 3 项：1 个大写字符、1 个小写字符、1 个数字和 1 个特殊字符。 虽然所有 VM 最初都有相同的密码，但可以在预配后更改密码。|12-72 个字符|不可用
SSH 密钥（身份验证类型 = 公钥）|用于远程登录的安全 shell 密钥。||不可用
订阅|部署联盟网络的订阅||不可用
资源组|部署联盟网络的资源组。||不可用
Location|资源组的 Azure 区域。||不可用

示例部署如下所示：![基本边栏选项卡](./media/ethereum-poa-deployment/basic-blade.png)

#### <a name="deployment-regions"></a>部署区域

接下来，在“部署区域”下，为“区域数量”指定输入，以根据给定的区域数量部署联盟网络及选择 Azure 区域。 用户最多可在 5 个区域中进行部署。 建议选择第一个区域以匹配“基础”部分中的资源组位置。 对于开发或测试网络，建议每个成员使用一个区域。 对于生产，建议跨两个或更多区域进行部署以实现高可用性。

每个参数的详细说明如下：

  参数名称|描述|允许值|默认值
  ---|---|---|---
  区域数量|部署联盟网络的区域数量|1、2、3、4、5|1
  第一个区域|部署联盟网络的第一个区域|所有允许的 Azure 区域|不可用
  第二个区域|部署联盟网络的第二个区域（仅在选择的区域数量为 2 时可见）|所有允许的 Azure 区域|不可用
  第三个区域|部署联盟网络的第三个区域（仅在选择的区域数量为 3 时可见）|所有允许的 Azure 区域|不可用
  第四个区域|部署联盟网络的第四个区域（仅在选择的区域数量为 4 时可见）|所有允许的 Azure 区域|不可用
  第五个区域|部署联盟网络的第五个区域（仅在选择的区域数量为 5 时可见）|所有允许的 Azure 区域|不可用

示例部署如下所示：![部署区域](./media/ethereum-poa-deployment/deployment-regions.png)

#### <a name="network-size-and-performance"></a>网络规模和性能

接下来，在“网络规模和性能”下指定联盟网络规模的输入值，例如验证程序节点的数量和大小。
验证程序节点存储大小可决定区块链的潜在大小。 这可以在部署后进行更改。

每个参数的详细说明如下：

  参数名称|描述|允许值|默认值
  ---|---|---|---
  负载均衡验证程序节点数|要预配为网络组成部分的验证程序节点数|2-15|2
  验证程序节点存储性能|支持每个已部署验证程序节点的托管磁盘的类型。|标准 SSD 或高级|标准 SSD
  验证程序节点虚拟机大小|用于验证程序节点的虚拟机大小。|标准 A、标准 D、标准 D-v2、标准 F 系列、标准 DS 和标准 FS|标准 D1 v2

[存储定价详细信息](https://azure.microsoft.com/pricing/details/managed-disks/)

[虚拟机定价详细信息](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)

虚拟机和存储层会影响网络性能。  根据所需的成本效益，我们建议使用以下 SKU：

  虚拟机 SKU|存储层|价格|吞吐量|延迟
  ---|---|---|---|---
  F1|标准 SSD|低|低|高
  D2_v3|标准 SSD|中|中|中
  F16s|高级 SSD|高|高|低

示例部署如下所示：![网络大小和性能](./media/ethereum-poa-deployment/network-size-and-performance.png)

#### <a name="ethereum-settings"></a>Ethereum 设置

接下来，在“Ethereum 设置”下，指定 Ethereum 相关配置设置，例如网络 ID、Ethereum 帐户密码或起源块。

每个参数的详细说明如下：

  参数名称|描述|允许值|默认值
  ---|---|---|---
联盟成员 ID|与参与联盟网络的每个成员相关联的 ID，用于配置 IP 地址空间以避免冲突。 在专用网络中，成员 ID 在同一网络中的不同组织之间应该是唯一的。  即使同一个组织部署到多个区域，也需要一个唯一的成员 ID。 请记下此参数的值, 因为你需要与其他联接成员共享它, 以确保不会发生冲突。|0-255|不可用
网络 ID|要部署的 Ethereum 联盟网络的网络 ID。  每个 Ethereum 网络都有自己的网络 ID，其中 1 是公共网络的 ID。|5 - 999,999,999|10101010
管理员 Ethereum 地址|用于参与 PoA 治理的 Ethereum 帐户地址。  建议使用 MetaMask 生成 Ethereum 地址。|以 0x 开头的 42 个字母数字字符|不可用
高级选项|用于 Ethereum 设置的高级选项|启用或禁用|禁用
公共 IP（高级选项 = 启用）|在 VNet 网关后方部署网络，并删除对等互连访问权限。 如果选择了此选项，则所有成员都必须使用 VNet 网关进行连接，以确保兼容性。|公共 IP 专用 VNet|公共 IP
区块燃料限制（高级选项 = 启用）|网络的起始区块燃料限制|任意数字|50000000
区块重新封装时间段（秒）|网络上没有事务时创建空区块的频率。 较高的频率将加快结束，但会增加存储成本。|任意数字|15
事务权限协定（高级选项 = 启用）|事务权限协定的字节码。 将智能协定部署和执行限制到以太坊帐户的允许列表。|协定字节码|不可用

示例部署如下所示：![ethereum 设置](./media/ethereum-poa-deployment/ethereum-settings.png)

#### <a name="monitoring"></a>监视

"监视" 边栏选项卡允许你为网络配置 Azure Monitor 日志资源。 监视代理从网络收集并显示有用的指标和日志，从而提供快速检查网络运行状况或调试问题的功能。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

  参数名称|描述|允许值|默认值
  ---|---|---|---
监视|启用监视的选项|启用或禁用|启用
连接到现有 Azure Monitor 日志|创建新 Azure Monitor 日志实例或加入现有实例|新建或加入现有|新建
监视位置 (连接到现有 Azure Monitor 日志 = 新建)|将在其中部署新 Azure Monitor 日志实例的区域|所有 Azure Monitor 日志区域|不可用
现有 log analytics 工作区 ID (连接到现有 Azure Monitor 日志 = 加入现有的)|现有 Azure Monitor 日志实例的工作区 ID||不可用
现有 log analytics 主密钥 (连接到现有 Azure Monitor 日志 = 加入现有的日志)|用于连接到现有 Azure Monitor 日志实例的主键||不可用


示例部署如下所示：![azure monitor](./media/ethereum-poa-deployment/azure-monitor.png)

#### <a name="summary"></a>总结

单击“摘要”边栏选项卡查看指定的输入并运行基本的部署前验证。 在部署之前，可以下载模板和参数。

查看法律和隐私条款，然后单击“购买”进行部署。 如果部署包括 VNet 网关，则需要 45 至 50 分钟时间进行部署。

#### <a name="post-deployment"></a>后期部署

##### <a name="deployment-output"></a>部署输出

部署完成后, 可以通过确认电子邮件或 Azure 门户访问所需的参数。 这些参数中，可看到：

-   Ethereum RPC 终结点

-   管理仪表板 URL

-   Azure Monitor URL

-   数据 URL

-   VNet 网关资源 ID（可选）

##### <a name="confirmation-email"></a>确认电子邮件

如果提供了电子邮件地址（[基础部分](#basics)），则会向该电子邮件地址发送包含部署输出信息的电子邮件。

![部署电子邮件](./media/ethereum-poa-deployment/deployment-email.png)

##### <a name="portal"></a>门户

成功完成部署并设置所有资源后, 你可以查看资源组中的输出参数。

1.  在门户中找到自己的资源组

2.  导航到“部署”

3.  选择与资源组同名的顶级部署

4.  选择“输出”

### <a name="growing-the-consortium"></a>扩大联盟

要扩展联盟，首先必须连接物理网络。
此首要步骤使用基于公共 IP 的部署，可顺畅完成。 如果在 VPN 后面部署, 请参阅[连接 VNet 网关](#connecting-vnet-gateways)以在新成员部署过程中执行网络连接一节。  完成部署后，请使用 [Governance DApp](#governance-dapp) 成为网络管理员。

#### <a name="new-member-deployment"></a>新成员部署

1.  与加入的成员共享以下信息。 可以在部署后的电子邮件或门户部署输出中找到此信息。

    -  联盟数据 URL

    -  已部署的节点数

    -  VNet 网关资源 ID（若使用 VPN）

2.  部署成员在部署其网络状态时应使用[相同的解决方案](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium)，并记住以下内容：

    -  选择“加入现有”

    -  选择与网络上其他成员相同的验证程序节点数，以确保公平表示

    -  使用上一步中提供的同一 Ethereum 地址

    -  传入“Ethereum 设置”选项卡上提供的“联盟数据 URL”

    -  如果网络的其余部分位于 VPN 后方，请在高级部分选择“专用 VNet”

#### <a name="connecting-vnet-gateways"></a>连接 VNet 网关

如果已使用默认的公共 IP 设置进行部署，则可以忽略此步骤。 在专用网络中，不同的成员通过 VNet 网关连接进行连接。 在成员可以加入网络并查看事务流量之前, 现有成员必须在其 VPN 网关上执行最终配置, 以接受连接。 这意味着在建立连接之前, 联接成员的以太坊节点将不会运行。 建议在协会中创建冗余网络连接 (网格), 以减少单点故障的可能性。

部署新成员后，现有成员必须通过设置与新成员的 VNet 网关连接来完成双向连接。 为此，现有成员需要：

1.  连接成员的 VNet 网关 ResourceID（请参阅部署输出）

2.  共享的连接密钥

现有成员必须运行以下 PowerShell 脚本来完成连接。 建议使用门户中右上方导航栏中的 Azure Cloud Shell。

![Cloud Shell](./media/ethereum-poa-deployment/cloud-shell.png)

```Powershell
$MyGatewayResourceId = "<EXISTING_MEMBER_RESOURCEID>"
$OtherGatewayResourceId = "<NEW_MEMBER_RESOURCEID]"
$ConnectionName = "Leader2Member"
$SharedKey = "<NEW_MEMBER_KEY>"

## $myGatewayResourceId tells me what subscription I am in, what ResourceGroup and the VNetGatewayName
$splitValue = $MyGatewayResourceId.Split('/')
$MySubscriptionid = $splitValue[2]
$MyResourceGroup = $splitValue[4]
$MyGatewayName = $splitValue[8]

## $otherGatewayResourceid tells me what the subscription and VNet GatewayName are
$OtherGatewayName = $OtherGatewayResourceId.Split('/')[8]
$Subscription=Select-AzSubscription -SubscriptionId $MySubscriptionid

## create a PSVirtualNetworkGateway instance for the gateway I want to connect to
$OtherGateway=New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
$OtherGateway.Name = $OtherGatewayName
$OtherGateway.Id = $OtherGatewayResourceId
$OtherGateway.GatewayType = "Vpn"
$OtherGateway.VpnType = "RouteBased"

## get a PSVirtualNetworkGateway instance for my gateway
$MyGateway = Get-AzVirtualNetworkGateway -Name $MyGatewayName -ResourceGroupName $MyResourceGroup

## create the connection
New-AzVirtualNetworkGatewayConnection -Name $ConnectionName -ResourceGroupName $MyResourceGroup -VirtualNetworkGateway1 $MyGateway -VirtualNetworkGateway2 $OtherGateway -Location $MyGateway.Location -ConnectionType Vnet2Vnet -SharedKey $SharedKey -EnableBgp $True
```

### <a name="service-monitoring"></a>服务监视

可以通过访问部署电子邮件中的链接或在部署输出 \[OMS\_PORTAL\_URL\] 中查找参数来查找 Azure Monitor 门户。

门户首先显示高级网络统计信息和节点概述。

![监视类别](./media/ethereum-poa-deployment/monitor-categories.png)

选择“节点概述”可定向到门户，以查看每个节点的基础结构统计信息。

![节点统计信息](./media/ethereum-poa-deployment/node-stats.png)

选择“网络统计信息”可定向到查看 Ethereum 网络统计信息的位置。

![网络统计信息](./media/ethereum-poa-deployment/network-stats.png)

#### <a name="sample-kusto-queries"></a>示例 Kusto 查询

这些仪表板之后是一组可查询的原始日志。 可以使用这些原始日志自定义仪表板、调查失败或设置阈值警报。 下方是一组可在日志搜索工具中运行的示例查询：

##### <a name="lists-blocks-that-have-been-reported-by-more-than-one-validator-useful-to-help-find-chain-forks"></a>列出由多个验证程序报告的块。 有助于查找分支。

```sql
MinedBlock_CL
| summarize DistinctMiners = dcount(BlockMiner_s) by BlockNumber_d, BlockMiner_s
| where DistinctMiners > 1
```

##### <a name="get-average-peer-count-for-a-specified-validator-node-averaged-over-5-minute-buckets"></a>获取指定验证程序节点平均超过 5 分钟 Bucket 的平均对等计数。

```sql
let PeerCountRegex = @"Syncing with peers: (\d+) active, (\d+) confirmed, (\d+)";
ParityLog_CL
| where Computer == "vl-devn3lgdm-reg1000001"
| project RawData, TimeGenerated
| where RawData matches regex PeerCountRegex
| extend ActivePeers = extract(PeerCountRegex, 1, RawData, typeof(int))
| summarize avg(ActivePeers) by bin(TimeGenerated, 5m)
```

### <a name="ssh-access"></a>SSH 访问权限

出于安全因素，默认情况下，网络组安全规则拒绝 SSH 端口访问。 若要访问 PoA 网络中的虚拟机实例, 需要将此规则更改为\""允许"\"

1.  从 Azure 门户中已部署资源组的“概述”部分开始。

    ![ssh 概述](./media/ethereum-poa-deployment/ssh-overview.png)

2.  为要访问的 VM 所在的区域选择网络安全组

    ![ssh nsg](./media/ethereum-poa-deployment/ssh-nsg.png)

3.  选择 allow-ssh 规则\"\"

    ![ssh-allow](./media/ethereum-poa-deployment/ssh-allow.png)

4.  将“操作”更改为“允许”\"\"

    ![ssh 启用允许](./media/ethereum-poa-deployment/ssh-enable-allow.png)

5.  单击“保存”（更改可能在几分钟之后生效）\"\"

现在可以使用所提供的管理员用户名和密码/SSH 密钥，通过 SSH 密钥远程连接到验证程序节点的虚拟机。
为访问第一个验证程序节点而运行的 SSH 命令在模板部署输出参数中列为“SSH\_TO\_FIRST\_VL\_NODE\_REGION1”（对于示例部署：ssh -p 4000 poaadmin\@leader4vb.eastus.cloudapp.azure.com）。 要获得其他事务节点，请将端口号增加 1（例如，第一个事务节点位于端口 4000 上）。

如果部署到多个区域，请将上述命令更改为该区域中负载均衡器的 DNS 名称或 IP 地址。 要查找其他区域的 DNS 名称或 IP 地址，请查找具有命名约定 \* \*\*\*\*-lbpip-reg\# 的资源，并查看其 DNS 名称和 IP 地址属性。

### <a name="azure-traffic-manager-load-balancing"></a>Azure 流量管理器负载均衡

Azure 流量管理器可通过路由不同区域中多个部署间的传入流量，帮助减少故障时间并加快 PoA 网络的响应速度。 内置运行状况检查和自动重新路由功能有助于确保 RPC 终结点和 Governance DApp 的高可用性。 如果已部署到多个区域并且已准备好投入生产，则此功能非常有用。

通过使用流量管理器，可以：

-   借助自动故障转移改进 PoA 网络可用性。

-   通过将最终用户路由到网络延迟最低的 Azure 位置，加快网络响应速度。

如果决定创建流量管理器配置文件，可以使用配置文件的 DNS 名称来访问网络。 将其他联盟成员添加到网络中之后，流量管理器也可用于跨其部署的验证程序进行负载均衡。

#### <a name="creating-a-traffic-manager-profile"></a>创建流量管理器配置文件

在 Azure 门户中单击“创建资源”按钮后，搜索并选择“流量管理器配置文件”\"\"\"\"。

![搜索 Azure 流量管理器](./media/ethereum-poa-deployment/traffic-manager-search.png)

为配置文件指定唯一名称，然后选择在 PoA 部署期间创建的资源组。 单击“创建”按钮进行部署。

![创建流量管理器](./media/ethereum-poa-deployment/traffic-manager-create.png)

部署后, 选择资源组中的实例。 可以在“概述”选项卡中找到用于访问流量管理器的 DNS 名称

![查找流量管理器 DNS](./media/ethereum-poa-deployment/traffic-manager-dns.png)

选择“终结点”选项卡，并单击“添加”按钮。 为终结点提供唯一名称。 将“目标资源类型”更改为“公共 IP 地址”。 然后选择第一个区域的负载均衡器的公共 IP \' 地址。

![路由流量管理器](./media/ethereum-poa-deployment/traffic-manager-routing.png)

对已部署网络中的每个区域重复此操作。 终结点处于 "已启用\"\" " 状态后, 它们将自动加载并在流量管理器的 DNS 名称中进行区域平衡。 现在，可以在文档的其他步骤中使用此 DNS 名称替代 \[CONSORTIUM\_DATA\_URL\] 参数。

### <a name="data-api"></a>数据 API

每个联盟成员都拥有其他成员连接到网络所需的信息。 现有成员将在该成员的部署之前提供 [CONSORTIUM_DATA_URL]。 部署后，加入的成员可从以下终结点的 JSON 接口检索信息：

`<CONSORTIUM_DATA_URL>/networkinfo`

响应将包含有助于联接成员 (Genesis 块、验证程序集协定 ABI、bootnodes) 的信息, 以及对现有成员有用的信息 (验证程序地址)。 我们鼓励使用此标准化来跨云提供程序扩展联盟。 此 API 返回具有以下结构的 JSON 格式的响应：
```json
{
  "$id": "",
  "type": "object",
  "definitions": {},
  "$schema": "https://json-schema.org/draft-07/schema#",
  "properties": {
    "majorVersion": {
      "$id": "/properties/majorVersion",
      "type": "integer",
      "title": "This schema’s major version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "minorVersion": {
      "$id": "/properties/minorVersion",
      "type": "integer",
      "title": "This schema’s minor version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "bootnodes": {
      "$id": "/properties/bootnodes",
      "type": "array",
      "items": {
        "$id": "/properties/bootnodes/items",
        "type": "string",
        "title": "This member’s bootnodes",
        "default": "",
        "examples": [
          "enode://a348586f0fb0516c19de75bf54ca930a08f1594b7202020810b72c5f8d90635189d72d8b96f306f08761d576836a6bfce112cfb6ae6a3330588260f79a3d0ecb@10.1.17.5:30300",
          "enode://2d8474289af0bb38e3600a7a481734b2ab19d4eaf719f698fe885fb239f5d33faf217a860b170e2763b67c2f18d91c41272de37ac67386f80d1de57a3d58ddf2@10.1.17.4:30300"
        ]
      }
    },
    "valSetContract": {
      "$id": "/properties/valSetContract",
      "type": "string",
      "title": "The ValidatorSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\n\nimport \"./SafeMath.sol\";\nimport \"./Utils.sol\";\n\ncontract ValidatorSet …"
      ]
    },
    "adminContract": {
      "$id": "/properties/adminContract",
      "type": "string",
      "title": "The AdminSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\nimport \"./SafeMath.sol\";\nimport \"./SimpleValidatorSet.sol\";\nimport \"./Admin.sol\";\n\ncontract AdminValidatorSet is SimpleValidatorSet { …"
      ]
    },
    "adminContractABI": {
      "$id": "/properties/adminContractABI",
      "type": "string",
      "title": "The Admin Contract ABI",
      "default": "",
      "examples": [
        "[{\"constant\":false,\"inputs\":[{\"name\":\"proposedAdminAddress\",\"type\":\"address\"},…"
      ]
    },
    "paritySpec": {
      "$id": "/properties/paritySpec",
      "type": "string",
      "title": "The Parity client spec file",
      "default": "",
      "examples": [
        "\n{\n \"name\": \"PoA\",\n \"engine\": {\n \"authorityRound\": {\n \"params\": {\n \"stepDuration\": \"2\",\n \"validators\" : {\n \"safeContract\": \"0x0000000000000000000000000000000000000006\"\n },\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\"\n }\n }\n },\n \"params\": {\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\",\n \"wasmActivationTransition\": \"0x0\"\n },\n \"genesis\": {\n \"seal\": {\n \"authorityRound\": {\n \"step\": \"0x0\",\n \"signature\": \"0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000\"\n }\n },\n \"difficulty\": \"0x20000\",\n \"gasLimit\": \"0x2FAF080\"\n },\n \"accounts\": {\n \"0x0000000000000000000000000000000000000001\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ecrecover\", \"pricing\": { \"linear\": { \"base\": 3000, \"word\": 0 } } } },\n \"0x0000000000000000000000000000000000000002\": { \"balance\": \"1\", \"builtin\": { \"name\": \"sha256\", \"pricing\": { \"linear\": { \"base\": 60, \"word\": 12 } } } },\n \"0x0000000000000000000000000000000000000003\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ripemd160\", \"pricing\": { \"linear\": { \"base\": 600, \"word\": 120 } } } },\n \"0x0000000000000000000000000000000000000004\": { \"balance\": \"1\", \"builtin\": { \"name\": \"identity\", \"pricing\": { \"linear\": { \"base\": 15, \"word\": 3 } } } },\n \"0x0000000000000000000000000000000000000006\": { \"balance\": \"0\", \"constructor\" : \"…\" }\n }\n}"
      ]
    },
    "errorMessage": {
      "$id": "/properties/errorMessage",
      "type": "string",
      "title": "Error message",
      "default": "",
      "examples": [
        ""
      ]
    },
    "addressList": {
      "$id": "/properties/addressList",
      "type": "object",
      "properties": {
        "addresses": {
          "$id": "/properties/addressList/properties/addresses",
          "type": "array",
          "items": {
            "$id": "/properties/addressList/properties/addresses/items",
            "type": "string",
            "title": "This member’s validator addresses",
            "default": "",
            "examples": [
              "0x00a3cff0dccc0ecb6ae0461045e0e467cff4805f",
              "0x009ce13a7b2532cbd89b2d28cecd75f7cc8c0727"
            ]
          }
        }
      }
    }
  }
}

```
## <a name="tutorials"></a>教程

### <a name="programmatically-interacting-with-a-smart-contract"></a>以编程方式与智能合同交互

> [!WARNING]
> 切勿通过网络发送 Ethereum 私钥！ 首先确保在本地对每个事务进行签名，然后通过网络发送已签名的事务。

以下示例使用 ethereumjs-wallet 生成 Ethereum 地址、使用 ethereumjs-tx 在本地进行签名，并且使用 web3 将原始事务发送到 Ethereum RPC 终结点。

此示例中使用简单的 Hello-World 智能合同：

```javascript
pragma solidity ^0.4.11;
contract postBox {
    string message;
    function postMsg(string text) public {
        message = text;
    }
    function getMsg() public view returns (string) {
        return message;
    }
}
```

此示例假定已部署合同。 可以使用 solc 和 web3 以编程方式部署合同。 首先安装以下节点模块：
```
sudo npm install web3@0.20.2
sudo npm install ethereumjs-tx@1.3.6
sudo npm install ethereumjs-wallet@0.6.1
```
此 nodeJS 脚本可执行以下操作：

-   构造原始事务：postMsg

-   使用生成的私钥对事务进行签名

-   将已签名的事务提交到 Ethereum 网络

```javascript
var ethereumjs = require('ethereumjs-tx')
var wallet = require('ethereumjs-wallet')
var Web3 = require('web3')

// TODO Replace with your contract address
var address = "0xfe53559f5f7a77125039a993e8d5d9c2901edc58";
var abi = [{"constant": false,"inputs": [{"name": "text","type": "string"}],"name": "postMsg","outputs": [],"payable": false,"stateMutability": "nonpayable","type": "function"},{"constant": true,"inputs": [],"name": "getMsg","outputs": [{"name": "","type": "string"}],"payable": false,"stateMutability": "view","type": "function"}];

// Generate a new Ethereum account
var account = wallet.generate();
var accountAddress = account.getAddressString()
var privateKey = account.getPrivateKey();

// TODO Replace with your RPC endpoint
var web3 = new Web3(new Web3.providers.HttpProvider(
    "http://testzvdky-dns-reg1.eastus.cloudapp.azure.com:8545"));

// Get the current nonce of the account
web3.eth.getTransactionCount(accountAddress, function (err, nonce) {
   var data = web3.eth.contract(abi).at(address).postMsg.getData("Hello World");
   var rawTx = {
     nonce: nonce,
     gasPrice: '0x00',
     gasLimit: '0x2FAF080',
     to: address,
     value: '0x00',
     data: data
   }
   var tx = new ethereumjs(rawTx);

   tx.sign(privateKey);

   var raw = '0x' + tx.serialize().toString('hex');
   web3.eth.sendRawTransaction(raw, function (txErr, transactionHash) {
     console.log("TX Hash: " + transactionHash);
     console.log("Error: " + txErr);
   });
 });
```

### <a name="deploy-smart-contract-with-truffle"></a>使用 Truffle 部署智能合同

-   安装所需的库

```javascript
npm init

npm install truffle-hdwallet-provider --save
```
-   在 truffle.js 中，添加以下代码以解锁 MetaMask 帐户并通过提供助记键短语（MetaMask/Settings/Reveal Seed 等词）将 PoA 节点配置为入口点

```javascript
var HDWalletProvider = require("truffle-hdwallet-provider");

var rpc_endpoint = "XXXXXX";
var mnemonic = "twelve words you can find in metamask/settings/reveal seed words";

module.exports = {
  networks: {
    development: {
      host: "localhost",
      port: 8545,
      network_id: "*" // Match any network id
    },
    poa: {
      provider: new HDWalletProvider(mnemonic, rpc_endpoint),
      network_id: 3,
      gasPrice : 0
    }
  }
};

```

-   部署到 PoA 网络

```javascript
$ truffle migrate --network poa
```

### <a name="debug-smart-contract-with-truffle"></a>使用 Truffle 调试智能合同

Truffle 具有可用于调试智能合同的本地开发网络。 可在[此处](https://truffleframework.com/tutorials/debugging-a-smart-contract)找到完整教程。

### <a name="webassembly-wasm-support"></a>WebAssembly (WASM) 支持

新部署的 PoA 网络上已启用了 WebAssembly 支持。 它允许以任何转换为 Web-Assembly (Rust、C、C++) 的语言进行智能合同开发。 有关详细信息，请访问以下链接

-   WebAssembly 的奇偶校验概述 - <https://wiki.parity.io/WebAssembly-Home>

-   奇偶校验技术教程 - <https://github.com/paritytech/pwasm-tutorial>

## <a name="reference"></a>参考

### <a name="faq"></a>常见问题

#### <a name="i-notice-there-are-many-transactions-on-the-network-that-i-didnt-send-where-are-these-coming-from"></a>我在网络上看到许多未发送的事务。 这些事务来自哪里？

解锁[个人 API](https://web3js.readthedocs.io/en/v1.2.0/web3-eth-personal.html) 是不安全的操作。 机器人将侦听解锁的 Ethereum 帐户并尝试耗尽资金。 机器人假设这些帐户包含真实的以太，并试图第一个转走余额。 请勿在网络上启用个人 API。 而是对事务进行预签名，可以使用 MetaMask 之类的电子钱包进行手动签名，也可以使用[以编程方式与智能合同交互](#programmatically-interacting-with-a-smart-contract)部分中所述的方式以编程方式签名。

#### <a name="how-to-ssh-onto-a-vm"></a>如何在 VM 上启用 SSH？

出于安全因素，未公开 SSH 端口。 请按照[本指南启用 SSH 端口](#ssh-access)。

#### <a name="how-do-i-set-up-an-audit-member-or-transaction-nodes"></a>如何设置审核成员或事务节点？

事务节点是一组与网络对等互连但未参与共识的奇偶校验客户端。 这些节点仍可用于提交 Ethereum 事务并读取智能合同状态。
这是一个很好的机制，为网络上的非权威联盟成员提供可审核性。 要实现这一点，只需遵循“扩大联盟”的步骤 2。

#### <a name="why-are-metamask-transactions-taking-a-long-time"></a>为什么 MetaMask 事务需要花费很长时间？

为了确保以正确的顺序接收事务，每个 Ethereum 事务都带有递增的 nonce。 如果在不同网络上的 MetaMask 中使用了帐户，需要重置 nonce 值。 依次单击设置图标（3 个条形）、“设置”、“重置帐户”。 事务历史记录随即清除，现在可以重新提交事务。

#### <a name="do-i-need-to-specify-gas-fee-in-metamask"></a>是否需要在 MetaMask 中指定燃料费用？

Ether 在权威证明联盟中没有用处。 因此，在 MetaMask 中提交事务时无需指定燃料费用。

#### <a name="what-should-i-do-if-my-deployment-fails-due-to-failure-to-provision-azure-oms"></a>如果因无法预配 Azure OMS 而使我的部署失败，我该怎么办？

监视是一个可选功能。 在因无法成功预配 Azure Monitor 资源而使你的部署失败的极少数情况下，无需 Azure Monitor 即可进行重新部署。

#### <a name="are-public-ip-deployments-compatible-with-private-network-deployments"></a>公共 IP 部署是否与专用网络部署兼容？

否，对等互连需要双向通信，因此整个网络必须是公共或私有的。

#### <a name="what-is-the-expected-transaction-throughput-of-proof-of-authority"></a>权威证明的预期事务吞吐量是多少？

事务吞吐量高度依赖于事务类型和网络拓扑。  使用简单的事务，我们已通过跨多个区域部署的网络，以平均每秒 400 个事务为基准进行评估。

#### <a name="how-do-i-subscribe-to-smart-contract-events"></a>如何订阅智能合同事件？

Ethereum 权威证明现在支持 Web 套接字。  检查部署电子邮件或部署输出以查找 Web 套接字 URL 和端口。

## <a name="next-steps"></a>后续步骤

通过使用 [Ethereum 权威证明联盟](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium)解决方案开始使用。
