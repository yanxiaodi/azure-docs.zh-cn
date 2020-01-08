---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: dc871b29cdafa57d337f9be6cf01e76212f31b67
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172926"
---
## <a name="migrate-iaas-resources-from-the-classic-deployment-model-to-azure-resource-manager"></a>将 IaaS 资源从经典部署模型迁移到 Azure 资源管理器
首先，必须了解在基础结构即服务 (IaaS) 资源上进行的数据平面操作和管理平面操作的差异。

* “管理/控制平面”  描述进入管理/控制平面或 API 来修改资源的调用。 例如，创建 VM、重启 VM 以及将虚拟网络更新成新子网等操作均可管理正在运行的资源。 它们并不直接影响到 VM 的连接。
* “数据平面”  （应用程序）描述应用程序本身的运行时，并涉及与不通过 Azure API 的实例的交互。 例如，访问网站或从运行中的 SQL Server 实例或 MongoDB 服务器拉取数据属于数据平面或应用程序交互。 其他示例包括：从存储帐户复制 Blob，以及访问公共 IP 地址，以便使用远程桌面协议 (RDP) 或安全外壳 (SSH) 连接到虚拟机。 这些操作可让应用程序继续跨计算、网络和存储运行。

经典部署模型和资源管理器堆栈之间的数据平面是相同的。 区别在于，在迁移过程中，Microsoft 会将资源的表示方式从经典部署模型转换为资源管理器堆栈中的相应模型。 因此，需在资源管理器堆栈中使用新的工具、API 和 SDK 来管理资源。

![显示管理/控制平面和数据平面之间差异的图](../articles/virtual-machines/media/virtual-machines-windows-migration-classic-resource-manager/data-control-plane.png)


> [!NOTE]
> 在某些迁移方案中，Azure 平台会停止、释放和重新启动虚拟机。 这会导致短暂的数据平面停机。
>

## <a name="the-migration-experience"></a>迁移体验
开始迁移之前：

* 确保要迁移的资源未使用任何不受支持的功能或配置。 通常平台会检测到这些问题并生成错误。
* 如果有不在虚拟网络中的 VM，平台会在准备操作期间停止这些 VM 并解除其分配。 如果不想丢失公共 IP 地址，可考虑先保留 IP 地址再触发准备操作。 如果 VM 位于虚拟网络中，则不会将其停止和解除分配。
* 规划在非工作时间迁移，以便应对迁移期间可能发生的任何意外失败。
* 使用 PowerShell、命令行接口 (CLI) 命令或 REST API 下载当前的 VM 配置，以便能够在完成准备步骤之后轻松进行验证。
* 先更新用于处理资源管理器部署模型的自动化和操作化脚本，再开始迁移。 也可以选择在资源处于准备就绪状态时执行 GET 操作。
* 在迁移完成之后，评估经典部署模型中 IaaS 资源上配置的基于角色的访问控制 (RBAC) 策略并准备好计划。

迁移工作流如下所示：

![显示迁移工作流的图](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-workflow.png)

> [!NOTE]
> 以下部分描述的操作全都是幂等的。 如果遇到功能不受支持或配置错误以外的问题，请重试准备、中止或提交操作。 Azure 会再次尝试此操作。
>
>

### <a name="validate"></a>验证
验证操作是迁移过程中的第一个步骤。 此步骤的目的是分析要在经典部署模型中迁移的资源的状态。 操作会评估资源能否进行迁移（成功或失败）。

可选择需要进行迁移验证的虚拟网络或云服务（如果不在虚拟网络中）。 如果资源不能迁移，Azure 会列出原因。

#### <a name="checks-not-done-in-the-validate-operation"></a>不在验证操作中进行的检查

验证操作仅分析经典部署模型中资源的状态。 它可以查看因经典部署模型中配置的不同而导致的所有失败方案和不支持的方案。 它不能检查 Azure 资源管理器堆栈对迁移过程中的资源造成的所有问题。 仅当资源在下一步的迁移过程（准备操作）中进行转换时，才会检查是否存在这些问题。 下表列出了不在验证操作中检查的所有问题：


|不在验证操作中进行的网络检查|
|-|
|虚拟网络同时具有 ER 和 VPN 网关|
|虚拟网关连接处于断开状态。|
|所有 ER 线路都预先迁移到 Azure 资源管理器堆栈。|
|Azure 资源管理器配额检查网络资源。 例如：静态公共 IP、动态公共 IP、负载均衡器、网络安全组、路由表、网络接口。 |
| 在整个部署和虚拟网络中，所有负载均衡器规则都有效。 |
| 同一 VNET 中处于“已停止-已取消分配”状态的 VM 之间存在专用 IP 冲突。 |

### <a name="prepare"></a>准备
准备操作是迁移过程中的第二个步骤。 此步骤的目的是模拟将 IaaS 资源从经典部署模型资源转换为资源管理器资源的过程。 此外，准备操作还以并排方式让此转换过程直观可见。

> [!NOTE] 
> 不会在这一步修改经典部署模型中的资源。 若要尝试迁移，这是可以运行的安全步骤。 

可选择需要进行迁移准备的虚拟网络或云服务（如果不是虚拟网络）。

* 如果资源无法迁移，Azure 会停止迁移过程，并列出准备操作失败的原因。
* 如果资源可迁移，Azure 会锁定进行迁移的资源的管理平面操作。 例如，无法将数据磁盘添加到进行迁移的 VM。

然后，Azure 就会开始将迁移中资源的元数据从经典部署模型迁移到资源管理器模型。

准备操作完成之后，可以选择在经典部署模型和资源管理器模型中将资源可视化。 对于经典部署模型中的每项云服务，Azure 平台都会创建模式为 `cloud-service-name>-Migrated` 的资源组名称。

> [!NOTE]
> 不能选择针对已迁移资源（即标有“-Migrated”字样的资源）创建的资源组的名称。 但是，可以在迁移完成后，使用 Azure 资源管理器的移动功能将资源移至所需的任何资源组。 有关详细信息，请参阅[将资源移到新资源组或订阅](../articles/resource-group-move-resources.md)。

下面的两个屏幕截图显示了准备操作成功后的结果。 第一个屏幕截图显示包含原始云服务的资源组。 第二个屏幕截图显示包含 Azure 资源管理器等效资源的新“-Migrated”资源组。

![显示原始云服务的屏幕截图](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-classic.png)

![显示准备操作中的 Azure 资源管理器资源的屏幕截图](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-arm.png)

下面是在完成准备阶段以后，在幕后查看资源的情形。 请注意，数据平面中的资源是相同的。 它同时在管理平面（经典部署模型）和控制平面（资源管理器）中呈现。

![准备阶段图](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-prepare.png)

> [!NOTE]
> 不属于经典部署模型中的虚拟网络的 VM 在此迁移阶段停止和解除分配。
>

### <a name="check-manual-or-scripted"></a>检查（手动或通过脚本）
在检查步骤中，可以选择使用前面下载的配置来验证迁移是否正常。 也可以登录到门户并抽查属性和资源，来验证元数据迁移是否正常。

如果迁移的是虚拟网络，大多数虚拟机配置不会重新启动。 对于这些 VM 上的应用程序，可以验证应用程序是否仍在运行。

可以测试监视和操作脚本，以查看 VM 是否按预期运行，以及更新后的脚本是否正常运行。 仅当资源处于准备就绪状态时，才支持 GET 操作。

可以慢慢考虑是否要提交迁移，因为系统对此并没有时间限制。 可以在此状态下停留任何时间。 但是，这些资源的管理平面会遭到锁定，直到中止或提交为止。

如果发现任何问题，始终可以中止迁移，并返回到经典部署模型。 在你返回后，Azure 会打开资源的管理平面操作，使你可以继续在经典部署模型中对这些 VM 执行正常操作。

### <a name="abort"></a>中止
这是可选步骤，用于还原对经典部署模型所做的更改，并停止迁移。 对于资源，此操作会删除资源管理器元数据（在准备步骤中创建）。 

![中止步骤图](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-abort.png)


> [!NOTE]
> 触发提交操作后，就无法执行此操作。     
>

### <a name="commit"></a>提交
完成验证之后，就可以提交迁移。 资源不再出现在经典部署模型中，只在资源管理器部署模型中提供。 只能在新门户中管理迁移的资源。

> [!NOTE]
> 这是幂等操作。 如果失败，请重试操作。 如果仍失败，请创建支持票证，或在 [VM 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesforWindows)上创建标记为“ClassicIaaSMigration”的论坛帖子。
>
>

![提交步骤图](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-commit.png)

## <a name="migration-flowchart"></a>迁移流程图

下面是一个流程图，说明了如何进行迁移：

![Screenshot that shows the migration steps](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="translation-of-the-classic-deployment-model-to-resource-manager-resources"></a>从经典部署模型资源转换为资源管理器资源
可以在下表中找到资源的经典部署模型与资源管理器表示形式。 目前不支持其他功能和资源。

| 经典表示形式 | Resource Manager 表示形式 | 说明 |
| --- | --- | --- |
| 云服务名称 |DNS 名称 |在迁移期间，以命名模式 `<cloudservicename>-migrated` 为每个云服务创建新的资源组。 此资源组包含用户的所有资源。 云服务名称会成为与公共 IP 地址关联的 DNS 名称。 |
| 虚拟机 |虚拟机 |VM 特定属性将原封不动地进行迁移。 某些 osProfile 信息（例如计算机名称）不会存储在经典部署模型中，因此迁移后会保留空白。 |
| 附加到 VM 的磁盘资源 |附加到 VM 的隐式磁盘 |在 Resource Manager 部署模型中，磁盘不会建模为顶级资源。 这些磁盘将作为 VM 下的隐式磁盘进行迁移。 目前只支持附加到 VM 的磁盘。 资源管理器 VM 现在可以使用经典部署模型中的存储帐户轻松地迁移磁盘，不需任何更新。 |
| VM 扩展 |VM 扩展 |除 XML 扩展以外的所有资源扩展都会从经典部署模型中迁移。 |
| 虚拟机证书 |Azure 密钥保管库中的证书 |如果云服务包含服务证书，迁移会为每个云服务创建新的 Azure Key Vault，并将证书移到该 Key Vault。 VM 将更新为引用该密钥保管库中的证书。 <br><br> 请勿删除该 Key Vault。 这可能导致 VM 进入故障状态。 |
| WinRM 配置 |osProfile 下的 WinRM 配置 |Windows 远程管理配置在迁移过程中会原封不动地进行转移。 |
| 可用性集属性 |可用性集资源 | 可用性集规范是经典部署模型中 VM 上的属性。 在迁移过程中，可用性集将成为顶级资源。 以下配置不受支持：每个云服务包含多个可用性集，或者在一个云服务中有一个或多个可用性集以及不在任何可用性集中的 VM。 |
| VM 上的网络配置 |主网络接口 |在迁移后，VM 上的网络配置会表示为主网络接口资源。 对于不在虚拟网络中的 VM，内部 IP 地址在迁移期间会更改。 |
| VM 上的多个网络接口 |网络接口 |如果 VM 有多个关联的网络接口，则在迁移过程中，每个网络接口以及所有属性都会成为顶级资源。 |
| 负载均衡的终结点集 |负载均衡 |在经典部署模型中，平台已经为每个云服务分配一个隐式负载均衡器。 在迁移期间，将创建新的负载均衡器资源，负载均衡终结点集将成为负载均衡器规则。 |
| 入站 NAT 规则 |入站 NAT 规则 |在迁移期间，VM 上定义的输入终结点将转换成负载均衡器下的入站网络地址转换规则。 |
| VIP 地址 |具有 DNS 名称的公共 IP 地址 |虚拟 IP 地址会变成公共 IP 地址并与负载均衡器关联。 虚拟机 IP 仅在已向其分配了输入终结点的情况下才能迁移。 |
| 虚拟网络 |虚拟网络 |虚拟网络将连同其所有属性一起迁移到 Resource Manager 部署模型。 将创建名为 `-migrated` 的新资源组。 |
| 保留 IP |具有静态分配方法的公共 IP 地址 |与负载均衡器关联的保留 IP 会在迁移云服务或虚拟机的过程中一起迁移。 目前不支持进行未关联的保留 IP 迁移。 |
| 每个 VM 的公共 IP 地址 |具有动态分配方法的公共 IP 地址 |与 VM 关联的公共 IP 地址将转换为公共 IP 地址资源，分配方法将设置为静态。 |
| NSG |NSG |在迁移到 Resource Manager 部署模型的过程中，将克隆与子网关联的网络安全组。 在迁移期间不会删除经典部署模型中的 NSG。 但是，当迁移正在进行时，会阻止 NSG 的管理平面操作。 |
| DNS 服务器 |DNS 服务器 |与虚拟网络或 VM 关联的 DNS 服务器会在迁移相应资源的过程中连同所有属性一起迁移。 |
| UDR |UDR |在迁移到 Resource Manager 部署模型的过程中，将克隆与子网关联的用户定义路由。 在迁移期间不会删除经典部署模型中的 UDR。 但是，当迁移正在进行时，会阻止 UDR 的管理平面操作。 |
| VM 网络配置中的 IP 转发属性 |NIC 中的 IP 转发属性 |VM 上的 IP 转发属性在迁移期间将转换为网络接口上的属性。 |
| 具有多个 IP 的负载均衡器 |具有多个公共 IP 资源的负载均衡器 |与负载均衡器关联的每个公共 IP 都会转换为公共 IP 资源，并在迁移后与负载均衡器关联。 |
| VM 上的内部 DNS 名称 |NIC 上的内部 DNS 名称 |在迁移期间，VM 的内部 DNS 后缀将迁移到 NIC 上名为“InternalDomainNameSuffix”的只读属性。 在迁移后，该后缀会保持不变，并且 VM 解决方案会继续像以前一样正常工作。 |
| 虚拟网络网关 |虚拟网络网关 |虚拟网关属性会原封不动地进行迁移。 与网关关联的 VIP 也不会更改。 |
| 本地网络站点 |本地网络网关 |本地网络站点属性将原封不动地迁移到名为“本地网关”的新资源。 这表示本地地址前缀和远程网关 IP。 |
| 连接引用 |连接 |网络配置中网关和本地网络站点之间的连接引用由名为“连接”的新资源表示。 网络配置文件中连接引用的所有属性会原封不动地复制到“连接”资源。 创建两个到本地网络站点（代表虚拟网络）的 IPsec 隧道即可在经典部署模型中的虚拟网络之间进行连接。 无需本地网关，即可将此连接转换为资源管理器模型中的“虚拟网络到虚拟网络”连接类型。 |

## <a name="changes-to-your-automation-and-tooling-after-migration"></a>自动化与工具在迁移之后的变化
在将资源从经典部署模型迁移到资源管理器部署模型的过程中，必须更新现有的自动化或工具，确保其在迁移之后仍可继续运行。
