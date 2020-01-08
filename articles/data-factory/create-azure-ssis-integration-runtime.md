---
title: 在 Azure 数据工厂中创建 Azure-SSIS 集成运行时 | Microsoft Docs
description: 了解如何在 Azure 数据工厂中创建 Azure-SSIS 集成运行时，以便能够在 Azure 中部署和运行 SSIS 包。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/15/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 36cb4d306cd74dcde09fa55fc582a043bc324f65
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2019
ms.locfileid: "71010020"
---
# <a name="create-azure-ssis-integration-runtime-in-azure-data-factory"></a>在 Azure 数据工厂中创建 Azure-SSIS 集成运行时

本教程提供了在 Azure 数据工厂 (ADF) 中预配 Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) 的步骤。 Azure-SSIS IR 支持运行部署到由 Azure SQL 数据库服务器/托管实例托管的 SSIS 目录 (SSISDB) 中的包（项目部署模型），以及部署到文件系统/文件共享/Azure 文件存储中的包（包部署模型）。 预配 Azure-SSIS IR 后，就可以使用熟悉的工具（如 SQL Server Data Tools (SSDT)/SQL Server Management Studio (SSMS) 和命令行实用工具（如 `dtinstall`/`dtutil`/`dtexec`））在 Azure 中部署和运行包。

[教程：预配 Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md) 介绍如何通过 Azure 门户/ADF 应用创建 Azure-SSIS IR，以及如何选择性地使用 Azure SQL 数据库服务器/托管实例来托管 SSISDB。 本文对该教程的内容做了延伸，介绍了如何执行以下操作：

- 选择性地将 Azure SQL 数据库服务器与虚拟网络服务终结点/托管实例结合使用，以承载 SSISDB。 需配置虚拟网络权限/设置（先决条件），然后 Azure-SSIS IR 才能加入虚拟网络。

- （可选）对 ADF 的托管标识使用 Azure Active Directory (AAD) 身份验证，以连接到 Azure SQL 数据库服务器/托管实例。 需将 ADF 的托管标识添加为能够创建 SSISDB 的数据库用户，这是先决条件。

- （可选）将 Azure-SSIS IR 加入虚拟网络/将自承载 IR 配置为你的 Azure-SSIS IR 的代理以访问本地数据。

## <a name="overview"></a>概述

本文演示预配 Azure-SSIS IR 的不同方法：

- [Azure 门户](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Azure Resource Manager 模板](#azure-resource-manager-template)

## <a name="prerequisites"></a>先决条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure 订阅**。 如果没有订阅，可以创建一个[免费试用](https://azure.microsoft.com/pricing/free-trial/)帐户。
- **Azure SQL 数据库服务器/托管实例（可选）** 。 如果还没有数据库服务器，请在开始操作之前在 Azure 门户中创建一个。 ADF 进而会在此数据库服务器上创建 SSISDB。 建议在集成运行时所在的同一 Azure 区域中创建数据库服务器。 此配置允许集成运行时将执行日志写入 SSISDB 而无需跨 Azure 区域。 
    - 根据所选数据库服务器的不同，SSISDB 的创建方式也不相同：可以代表你作为单个数据库创建、可以充当弹性池的一部分创建，也可以在托管实例中创建，并可在公用网络中访问或者通过加入虚拟网络来访问。 有关如何选择用于托管 SSISDB 的数据库服务器类型的指导，请参阅[比较 Azure SQL 数据库单一数据库/弹性池/托管实例](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance)。 如果将 Azure SQL 数据库服务器与虚拟网络服务终结点/托管实例结合使用专用终结点来托管 SSISDB，或需要访问本地数据而无需配置自承载 IR，则需要将 Azure-SSIS IR 加入虚拟网络，请参阅[将 Azure-SSIS IR 加入虚拟网络](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。
    - 确认为数据库服务器启用了“允许访问 Azure 服务”设置。 将 Azure SQL 数据库服务器与虚拟网络服务终结点/托管实例与专用终结点一起使用来托管 SSISDB 时，这不适用。 有关详细信息，请参阅[保护 Azure SQL 数据库](../sql-database/sql-database-security-tutorial.md#create-firewall-rules)。 若要通过 PowerShell 来启用此设置，请参阅 [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)。
    - 将客户端计算机的 IP 地址或一系列包括客户端计算机 IP 地址的 IP 地址添加到数据库服务器的防火墙设置中的客户端 IP 地址列表。 有关详细信息，请参阅 [Azure SQL 数据库服务器级和数据库级防火墙规则](../sql-database/sql-database-firewall-configure.md)。
    - 若要连接到数据库服务器，可以将 SQL 身份验证与服务器管理员凭据配合使用，也可以将 Azure Active Directory (AAD) 身份验证与 ADF 托管标识配合使用。  对于后者，需将 ADF 的托管标识添加到有权访问数据库服务器的 AAD 组中，具体请参阅[为 Azure-SSIS IR 启用 AAD 身份验证](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir)。
    - 确认你的数据库服务器还没有 SSISDB。 预配 Azure-SSIS IR 时不支持使用现有 SSISDB。
- **Azure 资源管理器虚拟网络（可选）** 。 如果下列条件中至少有一个属实，则必须配置 Azure 资源管理器虚拟网络：
    - 使用具有专用终结点的虚拟网络服务终结点/托管实例在 Azure SQL 数据库服务器上托管 SSISDB。
    - 要从 Azure-SSIS IR 上运行的 SSIS 包连接到本地数据存储，而无需配置自承载 IR。
- **Azure PowerShell（可选）** 。 若要运行 PowerShell 脚本来预配 Azure-SSIS IR，请按有关[如何安装和配置 Azure PowerShell](/powershell/azure/install-az-ps) 的说明进行操作。

### <a name="region-support"></a>区域支持

有关目前提供 ADF 和 Azure-SSIS IR 的 Azure 区域的列表，请参阅 [ADF + SSIS IR 可用性（按区域）](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all)。

### <a name="compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance"></a>比较 SQL 数据库单一数据库/弹性池和 SQL 数据库托管实例

下表比较了 Azure SQL 数据库服务器和托管实例与 Azure-SSIR IR 相关的某些功能：

| 功能 | 单一数据库/弹性池| 托管实例 |
|---------|--------------|------------------|
| **计划** | SQL Server 代理不可用。<br/><br/>请参阅[在 ADF 管道中计划包执行](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity)。| 可以使用托管实例代理。 |
| **身份验证** | 可以使用包含的数据库用户创建 SSISDB。该用户充当 **db_owner** 角色，代表具有 ADF 托管标识的任意 AAD 组。<br/><br/>请参阅[在 Azure SQL 数据库服务器中启用 Azure AD 身份验证以创建 SSISDB](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database)。 | 可以使用代表 ADF 托管标识的包含的数据库用户创建 SSISDB。 <br/><br/>请参阅[在 Azure SQL 数据库托管实例中启用 Azure AD 身份验证以创建 SSISDB](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance)。 |
| **服务层** | 在 Azure SQL 数据库服务器上创建 Azure-SSIS IR 时，可以选择 SSISDB 的服务层级。 有多个服务层级。 | 在托管实例上创建 Azure-SSIS IR 时，无法选择 SSISDB 的服务层级。 托管实例上的所有数据库共享分配给该实例的相同资源。 |
| **虚拟网络** | 仅支持适用于你的 Azure-SSIS IR 的 Azure 资源管理器虚拟网络，以便在你将 Azure SQL 数据库服务器与虚拟网络服务终结点一起使用时，或需要访问本地数据存储而无需配置自承载 IR。 | 仅支持 Azure-SSIS IR 可加入的 Azure 资源管理器虚拟网络。 如果没有为托管实例启用公共终结点，则需要虚拟网络。<br/><br/>如果将 Azure-SSIS IR 加入与托管实例相同的虚拟网络，请确保 Azure-SSIS IR 位于与托管实例不同的子网中。 如果将 Azure-SSIS IR 加入与托管实例不同的虚拟网络，我们建议使用虚拟网络对等互连或虚拟网络间的连接。 请参阅[将应用程序连接到 Azure SQL 数据库托管实例](../sql-database/sql-database-managed-instance-connect-app.md)。 |
| **分布式事务** | 通过弹性事务支持。 不支持 Microsoft 分布式事务处理协调器 (MSDTC) 事务。 如果 SSIS 包使用 MSDTC 协调分布式事务，请考虑迁移到 Azure SQL 数据库弹性事务。 有关详细信息，请参阅[跨云数据库的分布式事务](../sql-database/sql-database-elastic-transactions-overview.md)。 | 不受支持。 |
| | | |

## <a name="azure-portal"></a>Azure 门户

在本部分，我们将使用 Azure 门户（具体的说是 ADF 用户界面 (UI)/应用）创建 Azure-SSIS IR。

### <a name="create-a-data-factory"></a>创建数据工厂

若要通过 Azure 门户创建 ADF，请按照通过[UI 创建 adf](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)一文中的分步说明进行操作，并选择 "**固定到仪表板**"，以便在创建后允许快速访问。 

创建 ADF 后，在 Azure 门户上打开其 "概述" 页，然后单击 "**作者 & 监视器**" 磁贴，在单独的选项卡上启动其 "**入门**" 页，你可以在其中继续创建 Azure-SSIS IR。   

### <a name="provision-an-azure-ssis-integration-runtime"></a>预配 Azure SSIS 集成运行时

1. 在 "**开始**" 页上，单击 "**配置 SSIS Integration Runtime** " 磁贴。

   ![“配置 Azure SSIS 集成运行时”磁贴](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

2. 在“集成运行时安装”的“常规设置”页中完成以下步骤：

   ![常规设置](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    a. 对于“名称”，请输入集成运行时的名称。

    b. 对于“说明”，请输入集成运行时的说明。

    c. 对于“位置”，请选择集成运行时的位置。 界面上仅显示支持的位置。 建议选择承载 SSISDB 所需的数据库服务器的位置。

    d. 对于“节点大小”，请选择集成运行时群集中的节点大小。 仅显示支持的节点大小。 如果需要运行多个计算/内存密集型包，请选择较大的节点大小（纵向扩展）。

    e. 对于“节点数”，请选择集成运行时群集中的节点数。 仅显示支持的节点数。 如果需要并行运行多个包，请选择包含许多节点的大型群集（横向扩展）。

    f. 对于“版本/许可证”，请选择集成运行时的 SQL Server 版本/许可证：“标准”或“企业”。 如果需要在集成运行时上使用高级功能，请选择“Enterprise”。

    g. 对于“节省资金”，请选择适用于集成运行时的 Azure 混合权益 (AHB) 选项：“是”或“否”。 如果需要自带具有软件保障的 SQL Server 许可证，以便充分利用使用混合权益带来的成本节省，请选择“是”。

    h. 单击“下一步”。

3. 在“SQL 设置”页上，完成以下步骤：

   ![SQL 设置](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    a. 在“创建 SSIS 目录...”对话框中，选择要在 Azure-SSIS IR 中运行的包的部署模型：项目部署模型，其中的包将部署到数据库服务器托管的 SSISDB；或包部署模型，其中的包将部署到文件系统/文件共享/Azure 文件。 如果选中此项，需要提供自己的数据库服务器来托管我们将代表你创建和管理的 SSISDB。
   
    b. 对于“订阅”，请选择使用数据库服务器来托管 SSISDB 的 Azure 订阅。 

    c. 对于“位置”，请选择用于托管 SSISDB 的数据库服务器的位置。 建议选择集成运行时的位置。 

    d. 对于“目录数据库服务器终结点”，请选择用于承载 SSISDB 的数据库服务器的终结点。 根据所选数据库服务器的不同，SSISDB 的创建方式也不相同：可以代表你作为单个数据库创建、可以充当弹性池的一部分创建，也可以在托管实例中创建，并可在公用网络中访问或者通过加入虚拟网络来访问。 有关如何选择用于托管 SSISDB 的数据库服务器类型的指导，请参阅[比较 Azure SQL 数据库单一数据库/弹性池/托管实例](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance)。 如果选择 "Azure SQL 数据库服务器"，其中包含带虚拟网络服务终结点的虚拟网络服务终结点/托管实例，并使用专用终结点来托管 SSISDB，或者需要访问本地数据而无需配置自承载 IR，则需要将 Azure-SSIS IR 加入虚拟网络，请参阅[将 Azure-SSIS IR 加入到虚拟网络](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。 

    e. 在“使用 AAD 身份验证...”复选框中，选择数据库服务器用来承载 SSISDB 的身份验证方法：使用 ADF 的托管标识进行 SQL 身份验证或 AAD 身份验证。 如果勾选它，则需将 ADF 的托管标识添加到有权访问数据库服务器的 AAD 组中，具体请参阅[为 Azure-SSIS IR 启用 AAD 身份验证](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir)。 

    f. 对于“管理员用户名”，请输入用于承载 SSISDB 的数据库服务器的 SQL 身份验证用户名。 

    g. 对于“管理员密码”，请输入用于承载 SSISDB 的数据库服务器的 SQL 身份验证密码。 

    h. 对于“目录数据库服务层级”，请选择用于承载 SSISDB 的数据库服务器的服务层级：基本/标准/高级层或弹性池名称。 

    i. 单击“测试连接”，如果成功，则单击“下一步”。 

4. 在“高级设置”页上，完成以下步骤：

    ![高级设置](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

    a. 对于“每个节点的最大并行执行数”，请选择要在集成运行时群集中并发执行的最大包数（按节点）。 仅显示支持的包数。 如果需要使用多个核心来运行单个属于计算/内存密集型的大型/重型包，则请选择较低的数字。 如果需要在单个核心中运行一个或多个小型/轻型包，则请选择较高的数字。

    b. 对于“自定义安装容器 SAS URI”，可以选择输入 Azure 存储 Blob 容器（在其中存储了安装脚本及其关联的文件）的共享访问签名 (SAS) 统一资源标识符 (URI)，具体请参阅 [Azure-SSIS IR 的自定义安装](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)。

5. 在“选择虚拟网络...”复选框中，选择是否要将集成运行时加入虚拟网络。 如果你将 Azure SQL 数据库服务器与虚拟网络服务终结点/托管实例结合使用专用终结点来托管 SSISDB，或者需要访问本地数据（即，你的 SSIS 包中有本地数据源/目标），请选中该选项，无需配置自承载 IR，请参阅[将 Azure-SSIS IR 加入虚拟网络](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。 如果选中此项，请完成以下步骤：

   ![虚拟网络的高级设置](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

    a. 对于“订阅”，请选择包含你的虚拟网络的 Azure 订阅。

    b. 对于“位置”，系统已选择集成运行时所在的位置。

    c. 对于“类型”，请选择虚拟网络的类型：“经典”或“Azure 资源管理器”。 我们建议选择 Azure 资源管理器虚拟网络，因为经典虚拟网络在不久后将被弃用。

    d. 对于“VNet 名称”，请选择虚拟网络的名称。 此虚拟网络应是用于 Azure SQL 数据库服务器（包含虚拟网络中用于托管 SSISDB 的虚拟网络服务终结点/托管实例）的同一虚拟网络，或者是连接到本地网络的虚拟网络。

    e. 对于“子网名称”，请选择虚拟网络的子网名称。 这应该是与虚拟网络中的托管实例用来承载 SSISDB 的子网不同的子网。

6. 在 "**设置自承载 ...** " 复选框中，选择是否要将自承载 ir 配置为 Azure-SSIS IR 的代理，请参阅[将自承载 ir 设置为代理](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)。 如果选中此项，请完成以下步骤：

   ![带有自承载 IR 的高级设置](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

    a. 对于**自承载 Integration Runtime**，选择现有的自承载 IR 作为 Azure-SSIS IR 的代理。

    b. 对于 "**暂存存储链接服务**"，请选择现有的 Azure Blob 存储链接服务，或创建一个新的临时存储。

    c. 对于**暂存路径**，请在所选的 Azure blob 存储中指定一个 blob 容器，或将其留空以使用默认值进行暂存。

7. 单击 " **VNet 验证**" 和 "**下一步**"。 

8. 在 "**摘要**" 页上，查看所有设置设置，将建议的文档链接做成书签，并单击 "**完成**" 开始创建集成运行时。

    > [!NOTE]
    > 此过程应在 5 分钟内完成（不包括任何自定义设置时间），但对于加入虚拟网络的 Azure-SSIS IR，此过程可能需要大约 20 - 30 分钟。
    >
    > 如果使用 SSISDB，ADF 服务将连接到数据库服务器以准备 SSISDB。 它还会配置虚拟网络的权限和设置（如果已指定），并将 Azure-SSIS IR 加入虚拟网络中。
    > 
    > 预配 Azure-SSIS IR 时，还会安装 Access Redistributable 和 Azure Feature Pack for SSIS。 除了内置组件已支持的数据源外，这些组件还提供与 Excel/Access 文件和各种 Azure 数据源的连接。 还可以安装其他组件，请参阅 [Azure-SSIS IR 的自定义安装](how-to-configure-azure-ssis-ir-custom-setup.md)。

7. 在“连接”窗口中，根据需要切换到“集成运行时”。 单击“刷新”以刷新状态。

   ![创建状态](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

8. 使用“操作”列下面的链接可以停止/启动、编辑或删除集成运行时。 使用最后一个链接可以查看集成运行时的 JSON 代码。 仅当 IR 已停止时，才会启用编辑和删除按钮。

   ![Azure SSIS IR - 操作](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>门户中的 Azure SSIS 集成运行时

1. 在 Azure 数据工厂 UI 中切换到“编辑”选项卡，单击“连接”，然后切换到“集成运行时”选项卡以查看数据工厂中的现有集成运行时。

   ![查看现有 IR](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

2. 单击“新建”创建新的 Azure-SSIS IR。

   ![通过菜单创建集成运行时](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

3. 若要创建 Azure-SSIS 集成运行时，请单击“新建”，如图所示。

4. 在“集成运行时安装”窗口中选择“直接迁移现有的 SSIS 包以在 Azure 中执行”，然后单击“下一步”。

   ![指定集成运行时的类型](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

5. 请参阅[预配 Azure SSIS 集成运行时](#provision-an-azure-ssis-integration-runtime)部分，了解用于设置 Azure-SSIS IR 的剩余步骤。

## <a name="azure-powershell"></a>Azure PowerShell

在本部分中，将使用 Azure PowerShell 来创建 Azure-SSIS IR。

### <a name="create-variables"></a>创建变量

复制并粘贴以下脚本 - 指定变量的值。 

```powershell
### Azure Data Factory information
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database server with virtual network service endpoints/Managed Instance with a private endpoint/on-premises data without configuring Self-Hosted IR, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints or a different subnet than the one used for your Managed Instance with a private endpoint

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.DNS prefix.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"
```

### <a name="sign-in-and-select-subscription"></a>登录并选择订阅

在脚本中添加以下代码，以登录并选择 Azure 订阅：

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database-server"></a>验证与数据库服务器的连接

添加以下脚本来验证 Azure SQL 数据库服务器/托管实例。

```powershell
# Validate only if you use SSISDB and do not use VNet or AAD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
        {
            $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword
            $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
            Try
            {
                $sqlConnection.Open();
            }
            Catch [System.Data.SqlClient.SqlException]
            {
                Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
                Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
                $yn = Read-Host
                if(!($yn -ieq "Y"))
                {
                    Return;
                }
            }
        }
    }
}
```

### <a name="configure-virtual-network"></a>配置虚拟网络

添加以下脚本以自动配置加入 Azure-SSIS 集成运行时的虚拟网络权限/设置。

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-a-resource-group"></a>创建资源组

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 命令创建 [Azure 资源组](../azure-resource-manager/resource-group-overview.md)。 资源组是在其中以组的形式部署和管理 Azure 资源的逻辑容器。

如果资源组已存在，请不要将此代码复制到脚本中。 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>创建数据工厂

运行以下命令创建数据工厂。

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>创建集成运行时

运行以下命令，在 Azure 中创建运行 SSIS 包的 Azure-SSIS 集成运行时：

如果不使用 SSISDB，则可以省略 CatalogServerEndpoint、CatalogPricingTier 和 CatalogAdminCredential 参数。

如果未使用带有专用终结点的虚拟网络服务终结点/托管实例的 Azure SQL 数据库服务器来托管 SSISDB 或需要访问本地数据，则可以忽略 VNetId 和子网参数或为它们传递空值。 如果将自承载 IR 配置为用于访问本地数据的 Azure-SSIS IR 代理，则也可以省略它们。 否则不能省略这些参数，而必须传递虚拟网络配置中的有效值。具体请参阅[将 Azure-SSIS IR 加入虚拟网络](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。

如果使用托管实例来托管 SSISDB，则可以省略 CatalogPricingTier 参数，或传递该参数的空值。 否则不能省略此参数，而必须传递 Azure SQL 数据库定价层列表中的有效值。具体请参阅 [SQL 数据库资源限制](../sql-database/sql-database-resource-limits.md)。

如果对 ADF 的托管标识使用 Azure Active Directory (AAD) 身份验证来连接到数据库服务器，则可以省略 CatalogAdminCredential 参数，但必须将 ADF 的托管标识添加到有权访问数据库服务器的 AAD 组，具体请参阅[为 Azure-SSIS IR 启用 AAD 身份验证](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir)。 否则，不能忽略它，且必须传递由 SQL 身份验证的服务器管理员用户名和密码构成的有效对象。

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
       
# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add CatalogAdminCredential parameter if you do not use AAD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                   -DataFactoryName $DataFactoryName `
                                                   -Name $AzureSSISName `
                                                   -CatalogAdminCredential $serverCreds
    }
}

# Add SetupScriptContainerSasUri parameter if you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
```

### <a name="start-integration-runtime"></a>启动集成运行时

运行以下命令以启动 Azure-SSIS Integration Runtime。

```powershell
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

> [!NOTE]
> 此过程应在 5 分钟内完成（不包括任何自定义设置时间），但对于加入虚拟网络的 Azure-SSIS IR，此过程可能需要大约 20 - 30 分钟。
>
> 如果使用 SSISDB，ADF 服务将连接到数据库服务器以准备 SSISDB。 它还会配置虚拟网络的权限和设置（如果已指定），并将 Azure-SSIS IR 加入虚拟网络中。
> 
> 预配 Azure-SSIS IR 时，还会安装 Access Redistributable 和 Azure Feature Pack for SSIS。 除了内置组件已支持的数据源外，这些组件还提供与 Excel/Access 文件和各种 Azure 数据源的连接。 还可以安装其他组件，请参阅 [Azure-SSIS IR 的自定义安装](how-to-configure-azure-ssis-ir-custom-setup.md)。

### <a name="full-script"></a>完整脚本

下面是创建 Azure-SSIS 集成运行时的完整脚本。

```powershell
### Azure Data Factory information
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database server with virtual network service endpoints/Managed Instance with a private endpoint/on-premises data without configuring Self-Hosted IR, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database server with virtual network service endpoints or a different subnet than the one used for your Managed Instance with a private endpoint

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.DNS prefix.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"

### Sign in and select subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to database server
# Validate only if you use SSISDB and do not use VNet or AAD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
        {
            $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword
            $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
            Try
            {
                $sqlConnection.Open();
            }
            Catch [System.Data.SqlClient.SqlException]
            {
                Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
                Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
                $yn = Read-Host
                if(!($yn -ieq "Y"))
                {
                    Return;
                }
            }
        }
    }
}

### Configure virtual network
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}

### Create a data factory
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName

### Create an integration runtime
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
       
# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add CatalogAdminCredential parameter if you do not use AAD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                   -DataFactoryName $DataFactoryName `
                                                   -Name $AzureSSISName `
                                                   -CatalogAdminCredential $serverCreds
    }
}

# Add SetupScriptContainerSasUri parameter when you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}

### Start integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="azure-resource-manager-template"></a>Azure 资源管理器模板

在本部分，我们将使用 Azure 资源管理器模板创建 Azure-SSIS 集成运行时。 下面是示例演练：

1. 使用以下 Azure 资源管理器模板创建一个 JSON 文件。 将尖括号中的值（占位符）替换为你自己的值。

    ```json
    {
      "contentVersion": "1.0.0.0",
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2018-06-01",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for your Azure-SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2018-06-01",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D8_v3",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 8
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL Database server name>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL Database server admin username>",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Database server admin password>"
                                },
                                "catalogPricingTier": "Basic"
                            }
                        }
                    }
                }
            }]
        }]
    }
    ```

2. 若要部署 Azure 资源管理器模板，请运行 New-AzResourceGroupDeployment 命令，如以下示例所示。其中，ADFTutorialResourceGroup 是资源组的名称。 ADFTutorialARM.json 是包含数据工厂和 Azure-SSIS IR 的 JSON 定义的文件。

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    此命令将创建数据工厂并在其中创建 Azure-SSIS IR，但不会启动 IR。

3. 若要启动 Azure-SSIS IR，请运行 Start-AzDataFactoryV2IntegrationRuntime 命令：

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
                                                 -DataFactoryName "<Data Factory Name>" `
                                                 -Name "<Azure SSIS IR Name>" `
                                                 -Force
    ```

> [!NOTE]
> 此过程应在 5 分钟内完成（不包括任何自定义设置时间），但对于加入虚拟网络的 Azure-SSIS IR，此过程可能需要大约 20 - 30 分钟。
>
> 如果使用 SSISDB，ADF 服务将连接到数据库服务器以准备 SSISDB。 它还会配置虚拟网络的权限和设置（如果已指定），并将 Azure-SSIS IR 加入虚拟网络中。
> 
> 预配 Azure-SSIS IR 时，还会安装 Access Redistributable 和 Azure Feature Pack for SSIS。 除了内置组件已支持的数据源外，这些组件还提供与 Excel/Access 文件和各种 Azure 数据源的连接。 还可以安装其他组件，请参阅 [Azure-SSIS IR 的自定义安装](how-to-configure-azure-ssis-ir-custom-setup.md)。

## <a name="deploy-ssis-packages"></a>部署 SSIS 包

如果使用 SSISDB，则可以将包部署到其中，并使用通过服务器终结点连接到数据库服务器的 SSDT/SSMS 工具在 Azure-SSIS IR 上运行它们。  对于包含具有公共终结点的专用终结点/托管实例的中的 Azure SQL 数据库服务器/托管实例，服务器终结`<server name>.database.windows.net`点格式是分别是`<server name>.public.<dns prefix>.database.windows.net,3342` /。 `<server name>.<dns prefix>.database.windows.net` / 如果不使用 SSISDB，则可以将包部署到文件系统/文件共享/Azure 文件存储中，并使用 `dtinstall`/`dtutil`/`dtexec` 命令行实用工具在 Azure-SSIS IR 上运行它们。 有关详细信息，请参阅[部署 SSIS 包](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server)。 在这两种情况下，还可以使用 ADF 管道中的“执行 SSIS 包”活动在 Azure-SSIS IR 上运行已部署的包，请参阅[将 SSIS 包执行作为一级 ADF 活动调用](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)。

## <a name="next-steps"></a>后续步骤

另请参阅本文档中的其他 Azure-SSIS IR 主题：

- [Azure-SSIS 集成运行时](concepts-integration-runtime.md#azure-ssis-integration-runtime)。 此文提供有关集成运行时（包括 Azure-SSIS IR）的一般信息。
- [监视 Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime)。 此文介绍如何检索并了解有关 Azure-SSIS IR 的信息。
- [管理 Azure-SSIS IR](manage-azure-ssis-integration-runtime.md)。 此文介绍如何停止、启动或删除 Azure-SSIS IR，同时还介绍如何通过添加更多节点来横向扩展 Azure-SSIS IR。
- [将 Azure-SSIS IR 加入虚拟网络](join-azure-ssis-integration-runtime-virtual-network.md)。 此文介绍如何将 Azure-SSIS IR 加入虚拟网络。