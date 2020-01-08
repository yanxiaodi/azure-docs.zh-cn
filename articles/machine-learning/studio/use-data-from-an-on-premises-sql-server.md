---
title: 本地 SQL Server
titleSuffix: Azure Machine Learning Studio
description: 在 Azure 机器学习工作室中使用本地 SQL Server 数据库中的数据执行高级分析。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 03/13/2017
ms.openlocfilehash: 9590728cec663b36c889dc26a6216c3d474244e4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60735275"
---
# <a name="perform-analytics-with-azure-machine-learning-studio-using-an-on-premises-sql-server-database"></a>在 Azure 机器学习工作室中使用本地 SQL Server 数据库执行分析

通常，使用本地数据的企业希望利用云的规模和灵活性来平衡其机器学习工作负荷。 但他们并不希望在将企业的本地数据移动到云时中断其当前业务处理和工作流。 Azure 机器学习工作室现在支持从本地 SQL Server 数据库读取数据，然后训练和评分与此数据模型。 再也不必在云和本地服务器之间手动复制并同步数据。 相反，Azure 机器学习工作室中的**导入数据**模块现在可以为训练和评分作业直接从本地 SQL Server 数据库中读取。

本文概述了如何将本地 SQL Server 数据引入到 Azure 机器学习工作室中。 它假定您熟悉 Studio 概念，如工作区、 模块、 数据集、 试验*等*。

> [!NOTE]
> 此功能不适用于免费工作区。 有关机器学习定价和层级的详细信息，请参阅 [Azure 机器学习定价](https://azure.microsoft.com/pricing/details/machine-learning/)。
>
>

<!-- -->



## <a name="install-the-data-factory-self-hosted-integration-runtime"></a>安装数据工厂自承载集成运行时
若要访问 Azure 机器学习工作室中的本地 SQL Server 数据库，您需要下载并安装数据工厂自承载集成运行时，以前称为数据管理网关。 在机器学习工作室中配置连接时，将有机会使用如下所述的“下载并注册数据网关”对话框下载并安装集成运行时 (IR)  。


还可以通过从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=39717)下载并运行 MSI 安装包来提前安装 IR。MSI 也可用于将现有 IR 升级至最新版本，并会保留所有设置。

下面是数据工厂自承载运行时的先决条件：

* 数据工厂自承载运行时需要带有 .NET Framework 4.6.1 或更高版本的 64 位操作系统。
* 支持的 Windows 操作系统版本有 Windows 10、Windows Server 2012、Windows Server 2012 R2 和 Windows Server 2016。 
* IR 计算机的推荐配置至少为：2 GHz、4 核 CPU、8 GB RAM 和 80 GB 磁盘。
* 如果主机计算机进入休眠状态，则 IR 不会响应数据请求。 因此，安装 IR 之前，请在计算机上配置相应的电源计划。 如果计算机配置为休眠，则 IR 安装会显示一条消息。
* 由于复制活动按特定频率发生，因此计算机上的资源使用率（CPU、内存）也遵循相同的高峰期和空闲期模式。 资源利用率很大程度上还取决于正在移动的数据量。 进行多个复制作业时，将观察到资源使用率在高峰期上升。 尽管以上所列最低配置从技术上讲足够，但你可能希望具有更多资源的配置（相对于最低配置），具体取决于数据移动的特定负载。

在设置并使用数据工厂自承载集成运行时的时候，请注意以下几点：

* 一台计算机上只能安装一个 IR 实例。
* 可以将单个 IR 用于多个本地数据源。
* 可以将不同计算机上的多个 IR 连接到同一个本地数据源。
* 一次配置只能有一个工作区 IRs。 目前不能跨工作区共享 IR。
* 可以为单个工作区配置多个 IR。 例如，你可能想要使用连接到测试数据源在开发和生产红外线 （ir） 期间如果你已准备好实施红外线 （ir）。
* IR 不需要位于数据源所在的计算机上。 但是，如果离数据源较近，可以减少网关连接到数据源的时间。 建议不要在托管本地数据源的计算机上安装 IR，从而避免 IR 和数据源之间的资源争用。
* 如果已为 Power BI 或 Azure 数据工厂方案提供服务在计算机上安装的 IR，请安装 Azure 机器学习工作室单独红外线 （ir） 在另一台计算机上。

  > [!NOTE]
  > 数据工厂自承载集成运行时和 Power BI Gateway 不能在同一台计算机上运行。
  >
  >
* 您需要为 Azure 机器学习工作室中使用数据工厂自承载集成运行时，即使针对其他数据使用 Azure ExpressRoute。 即使使用 ExpressRoute，也应将数据源视为本地数据源（位于防火墙之后）。 使用数据工厂自承载集成运行时建立机器学习和数据源之间的连接性。

若要详细了解安装先决条件、安装步骤和故障排除提示，请参阅[数据工厂中的集成运行时](../../data-factory/concepts-integration-runtime.md)一文。

## <a name="span-idusing-the-data-gateway-step-by-step-walk-classanchorspan-idtoc450838866-classanchorspanspaningress-data-from-your-on-premises-sql-server-database-into-azure-machine-learning"></a><span id="using-the-data-gateway-step-by-step-walk" class="anchor"><span id="_Toc450838866" class="anchor"></span></span>将本地 SQL Server 数据库中的数据导入 Azure 机器学习
在本演练中，将在 Azure 机器学习工作区中设置 Azure 数据工厂集成运行时、 其进行配置，然后从本地 SQL Server 数据库读取数据。

> [!TIP]
> 开始之前，请在浏览器中禁用 `studio.azureml.net` 的弹出窗口阻止程序。 如果使用的是 Google Chrome 浏览器，请下载并安装 Google Chrome WebStore [ClickOnce 应用扩展](https://chrome.google.com/webstore/search/clickonce?_category=extensions)中提供的几个插件中的一个。
>
> [!NOTE]
> Azure 数据工厂自承载集成运行时之前名为“数据管理网关”。 此分步教程将继续称其为网关。  

### <a name="step-1-create-a-gateway"></a>步骤 1：创建网关
第一步是创建并设置用于访问本地 SQL 数据库的网关。

1. 登录到 [Azure 机器学习工作室](https://studio.azureml.net/Home/)，并选择要在其中工作的工作区。
2. 单击左侧的“设置”  边栏选项卡，并单击顶部的“数据网关”  选项卡。
3. 单击屏幕底部的“新建数据网关”  按钮。

    ![新数据网关](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-button.png)
4. 在“新建数据网关”  对话框中，输入“网关名称”  ，还可添加“说明”  。 单击右下角的箭头可转到配置的下一步。

    ![输入网关名称和说明](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-dialog-enter-name.png)
5. 在“下载并注册数据网关”对话框中，将网关注册密钥复制到剪贴板。

    ![下载并注册数据网关](./media/use-data-from-an-on-premises-sql-server/download-and-register-data-gateway.png)
6. <span id="note-1" class="anchor"></span>如果尚未下载并安装 Microsoft 数据管理网关，则单击“下载数据管理网关”  。 这会你将转到 Microsoft 下载中心，可以在其中选择所需网关版本、下载并安装它。 有关安装先决条件、安装步骤和故障排除提示的详细信息，请参阅[使用数据管理网关在本地资源和云之间移动数据](../../data-factory/tutorial-hybrid-copy-portal.md)一文的开头部分。
7. 网关安装完成后，数据管理网关配置管理器将打开，还会显示“注册网关”  对话框。 粘贴已复制到剪贴板的“网关注册密钥”  ，并单击“注册”  。
8. 如果已安装网关，请运行数据管理网关配置管理器。 单击 **更改密钥** 、粘贴在上一步中已复制到剪贴板的 **网关注册密钥** ，并单击 **确定** 。
9. 安装完成后，会显示 Microsoft 数据管理网关配置管理器的“注册网关”  对话框。 粘贴在上一步中已复制到剪贴板的“网关注册密钥”，并单击 **注册**。

    ![注册网关](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-register-gateway.png)
10. 在 Microsoft 数据管理网关配置管理器的“主页”  选项卡上设置了以下值后，网关配置即完成：

    * “网关名称”  和“实例名称”  设置为网关的名称。
    * “注册”  设置为“已注册”  。
    * “状态”  设置为“已启动”  。
    * 底部状态栏会显示“已连接到数据管理网关云服务”  以及绿色核选标记。

      ![数据管理网关管理器](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-registered.png)

      Azure 机器学习工作室也会在注册完成时进行更新。

    ![网关注册成功](./media/use-data-from-an-on-premises-sql-server/gateway-registered.png)
11. 在“下载并注册数据网关”  对话框中，单击核选标记以完成设置。 “设置”  页将网关状态显示为“联机”。 在右侧窗格中，可查看状态和其他有用信息。

    ![网关设置](./media/use-data-from-an-on-premises-sql-server/gateway-status.png)
12. 在 Microsoft 数据管理网关配置管理器中，切换到“证书”  选项卡。此选项卡上指定的证书可用于加密/解密在门户上指定的本地数据存储的凭据。 此证书是默认证书。 Microsoft 建议将此证书更改为在证书管理系统中备份的自己的证书。 单击“更改”  以改为使用自己的证书。

    ![更改网关证书](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-certificate.png)
13. （可选）如果想要启用用于排查网关问题的详细日志记录，请在 Microsoft 数据管理网关配置管理中，切换到“诊断”  选项卡，并选中“启用详细日志记录以进行疑难解答”  选项。 可在“应用程序和服务日志”   -&gt;“数据管理网关”  节点下的“Windows 事件查看器”中找到该日志记录信息。 还可以使用“诊断”  选项卡对使用网关连接本地数据源进行测试。

    ![启用详细日志记录](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-verbose-logging.png)

这将完成 Azure 机器学习工作室中的网关安装过程。
现在，已可以使用本地数据。

可以在工作室中为每个工作区创建并设置多个网关。 例如，可能希望开发期间某个网关与测试数据源连接，而其他网关用于生产数据源。 Azure 机器学习工作室可以灵活地设置取决于您的企业环境的多个网关。 目前，不能在工作区之间共享网关，一台计算机上只能安装一个网关。 有关详细信息，请参阅[使用数据管理网关在本地源与云之间移动数据](../../data-factory/tutorial-hybrid-copy-portal.md)。

### <a name="step-2-use-the-gateway-to-read-data-from-an-on-premises-data-source"></a>步骤 2：使用网关从本地数据源读取数据
在完成设置网关后，可以将“导入数据”  模块添加到从本地 SQL Server 数据库输入数据的实验。

1. 在机器学习工作室中，选择“实验”  选项卡、单击左下角的“+新建”  ，并选择“空白实验”  （或选择提供的多个实验示例中的一个）。
2. 找到“导入数据”  模块并将其拖动到试验画布上。
3. 单击画布下方的“另存为”  。 输入"Azure 机器学习 Studio 在本地 SQL Server 教程"作为实验名称，选择工作区，并单击**确定**复选标记。

   ![使用新名称保存实验](./media/use-data-from-an-on-premises-sql-server/experiment-save-as.png)
4. 单击要选取的“导入数据”  模块，并在画布右侧的“属性”  窗格中，选择“数据源”  下拉列表中的“本地 SQL 数据库”。
5. 选择已安装并注册的“数据网关”  。 可以通过选择“(添加新的数据网关…)”来设置其他网关。

   ![为导入数据模块选择数据网关](./media/use-data-from-an-on-premises-sql-server/import-data-select-on-premises-data-source.png)
6. 输入 SQL“数据库服务器名称”  和“数据库名称”  以及要执行的 SQL“数据库查询”  。
7. 单击“用户名和密码”  下的“输入值”  ，并输入你的数据库凭据。 可以使用 Windows 集成身份验证或 SQL Server 身份验证，具体取决于配置本地 SQL Server 的方式。

   ![输入数据库凭据](./media/use-data-from-an-on-premises-sql-server/database-credentials.png)

   消息“必填值”将更改为带有绿色复选标记的“值已设置”。 只需输入凭据一次，除非数据库信息或密码发生更改。 Azure 机器学习工作室使用安装网关来加密在云中的凭据时所提供的证书。 Azure 从不存储未加密的本地凭据。

   ![导入数据模块属性](./media/use-data-from-an-on-premises-sql-server/import-data-properties-entered.png)
8. 若要运行实验，请单击“运行”  。

实验运行结束后，可以可视化从数据库中导入的数据，方法是单击“导入数据”  模块的输出端口并选择“可视化”  。

完成实验开发后，即可部署并操作模型。 通过使用 Batch 执行服务，可读取在“导入数据”  模块中配置的本地 SQL Server 数据库中的数据，并将该数据用于评分。 尽管可以将请求响应服务用于评分本地数据，但 Microsoft 还是建议使用 [Excel 加载项](excel-add-in-for-web-services.md)。 目前，通过“导出数据”  写入本地 SQL Server 数据库在实验和已发布的 Web 服务中都不受支持。
