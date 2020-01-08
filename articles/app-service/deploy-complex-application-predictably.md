---
title: 按可预见的方式预配和部署微服务 - Azure 应用服务
description: 了解如何使用 JSON 资源组模板和 PowerShell 脚本以一种可预见的方式，在 Azure 应用服务中由微服务构成的应用程序设置并部署为单个单元。
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: b13bc43595c09b3700798935f70c401c9311651c
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70070881"
---
# <a name="provision-and-deploy-microservices-predictably-in-azure"></a>按可预见的方式在 Azure 中设置和部署微服务
本教程演示如何通过使用 JSON 资源组模板和 PowerShell 脚本以一种可预见的方式，在 [Azure App Service](https://azure.microsoft.com/services/app-service/) 中将由[微服务](https://en.wikipedia.org/wiki/Microservices)构成的应用程序设置并部署为单个单元。 

在设置和部署由高度分离的微服务构成的高扩展性应用程序时，可重复性和可预见性对成功至关重要。 使用 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)，可以创建包括 Web 应用、移动后端和 API 应用在内的微服务。 使用 [Azure 资源管理器](../azure-resource-manager/resource-group-overview.md)，可以将所有微服务作为一个单元与资源依赖项（如数据库和源代码管理设置）一起进行管理。 现在，还可以使用 JSON 模板和简单的 PowerShell 脚本部署此类应用程序。 

## <a name="what-you-will-do"></a>执行的操作
在教程中，将部署的应用程序包括：

* 两个应用服务应用（即两个微服务）
* 后端 SQL 数据库
* 应用设置、连接字符串和源代码管理
* Application Insights、警报和自动缩放设置

## <a name="tools-you-will-use"></a>将使用的工具
在本教程中，将使用以下工具。 由于对工具的讨论并不全面，我将坚持使用端到端方案，并只提供每个方案的简要介绍及在哪里可找到它的详细信息。 

### <a name="azure-resource-manager-templates-json"></a>Azure 资源管理器模板 (JSON)
例如，每次在 Azure 应用服务中创建应用时，Azure 资源管理器都将使用 JSON 模板来创建具有组件资源的整个资源组。 [Azure 市场](/azure/marketplace)中的复杂模板可以包含数据库、存储帐户、应用服务计划、应用本身、警报规则、应用设置、自动缩放设置等等，可以通过 PowerShell 使用所有这些模板。 有关 Azure 资源管理器模板的详细信息，请参阅[创作 Azure 资源管理器模板](../azure-resource-manager/resource-group-authoring-templates.md)

### <a name="azure-sdk-26-for-visual-studio"></a>Azure SDK 2.6 for Visual Studio
最新的 SDK 包含对 JSON 编辑器中 Resource Manager 模板支持的改进。 可以使用它快速从头开始创建资源组模板，或打开现有 JSON 模板（例如下载的库模板）以进行修改、填充参数文件，甚至直接从 Azure 资源组解决方案部署资源组。

有关详细信息，请参阅 [Azure SDK 2.6 for Visual Studio](https://azure.microsoft.com/blog/2015/04/29/announcing-the-azure-sdk-2-6-for-net/)。

### <a name="azure-powershell-080-or-later"></a>Azure PowerShell 0.8.0 或更高版本
从版本 0.8.0 开始，Azure PowerShell 安装除了包括 Azure 模块外还包括 Azure 资源管理器模块。 使用此新模块可以编写资源组部署的脚本。

有关详细信息，请参阅[将 Azure PowerShell 与 Azure 资源管理器配合使用](../powershell-azure-resource-manager.md)

### <a name="azure-resource-explorer"></a>Azure 资源浏览器
使用此[预览工具](https://resources.azure.com)可以够浏览订阅中所有资源组的 JSON 定义和独立资源。 在工具中，可编辑资源的 JSON 定义、删除资源的整个层次结构及创建新资源。  此工具中随时可用的信息对模板创作非常有帮助，因为它会显示需要为特定类型的资源设置的属性、正确值等。甚至可在 [Azure 门户](https://portal.azure.com/)中创建资源组，并在资源管理器工具中检查其 JSON 定义以帮助模板化资源组。

### <a name="deploy-to-azure-button"></a>“部署到 Azure”按钮
如果将 GitHub 用于源代码管理，则可将一个[“部署到 Azure”按钮](https://azure.microsoft.com/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/)放入 README.MD，这会对 Azure 启用统包部署 UI。 可为任何简单的应用执行此操作，同时可扩展这一操作，通过将 azuredeploy.json 文件放入存储库根来实现对整个资源组的部署。 “部署到 Azure”按钮将使用此包含资源组模板的 JSON 文件来创建资源组。 有关示例，请参阅会在本教程中使用的 [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) 示例。

## <a name="get-the-sample-resource-group-template"></a>获取示例资源组模板
现在让我们开始吧。

1. 导航到 [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) 应用服务示例。
2. 在 readme.md 中，单击“部署到 Azure”。
3. 会转到[部署到 Azure](https://deploy.azure.com) 站点并需要输入部署参数。 请注意大多数字段将填充以存储库名称和某些随机字符串。 可以更改所有字段（如果想），但唯一一项必须输入的内容是 SQL Server 管理登录名和密码，并单击“下一步”。
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-1-deploybuttonui.png)
4. 接下来，单击“部署”启动部署进程。 进程运行至完成时，请单击 http://todoapp*XXXX*.azurewebsites.net 链接以浏览部署的应用程序。 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-2-deployprogress.png)
   
   首次浏览到 UI 时它的显示会慢些，因为应用刚刚启动，但应确信它是一个功能齐全运行正常的应用程序。
5. 返回到“部署”页，单击**管理**链接以查看 Azure 门户中的新应用程序。
6. 在“必备”下拉列表中，单击资源组链接。 还要注意，应用已连接到“外部项目”下的 GitHub 存储库。 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-3-portalresourcegroup.png)
7. 在资源组边栏选项卡中，请注意资源组中已存在两个应用和一个 SQL 数据库。
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-4-portalresourcegroupclicked.png)

刚才在几分钟内看到的全部内容就是一个经过完全部署的由两个微服务构成的应用程序，以及所有组件、依赖项、设置、数据库和连续发布，均由 Azure 资源管理器中的自动化协调所设置。 所有这一切均是通过两项内容完成：

* “部署到 Azure”按钮
* 存储库根中的 azuredeploy.json

可数十、数百或数千次地部署此同一的应用程序，并且每次都具有完全相同的配置。 这种方法的可重复性和可预见性使你能够轻松、自信地部署高扩展性应用程序。

## <a name="examine-or-edit-azuredeployjson"></a>检查（或编辑）AZUREDEPLOY.JSON
现在让我们看看如何设置 GitHub 存储库。 将使用 Azure.NET SDK 中的 JSON 编辑器，所以如果尚未安装 [Azure .NET SDK 2.6](https://azure.microsoft.com/downloads/)，请立刻安装。

1. 使用最喜欢的 git 工具克隆 [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) 存储库。 在下面的屏幕快照中，我会在 Visual Studio 2013 的团队资源管理器中执行此操作。
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-1-vsclone.png)
2. 在 Visual Studio 中从存储库根打开 azuredeploy.json。 如果没有看到“JSON 概要”窗格，则需要安装 Azure.NET SDK。
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-2-vsjsoneditor.png)

我不打算介绍 JSON 格式的每个细节，但[更多资源](#resources)部分包含可用于学习资源组模板语言的链接。 在这里，我只打算向你展示有趣的功能，可帮助你开始制作自己的自定义模板来部署应用。

### <a name="parameters"></a>Parameters
看一看参数部分，你会看到这些参数大都是“部署到 Azure” 按钮提示你输入的内容。 “部署到 Azure”按钮背后的站点使用 azuredeploy.json 中定义的参数填充输入 UI。 这些参数用于整个资源定义，例如资源名称、属性值等。

### <a name="resources"></a>资源
在资源节点中，可以看到定义了 4 个顶级资源，包括一个 SQL Server 实例、一个应用服务计划和两个应用。 

#### <a name="app-service-plan"></a>应用服务计划
让我们以 JSON 中简单的根级别资源开始。 在“JSON 概要”中，单击名为 **[hostingPlanName]** 的应用服务计划以突出显示相应的 JSON 代码。 

![](./media/app-service-deploy-complex-application-predictably/examinejson-3-appserviceplan.png)

请注意，`type` 元素指定应用服务计划（很久很久以前它被称之为服务器场）的字符串，而其他元素和属性使用 JSON 文件中定义的参数填写，并且此资源不具有任何嵌套的资源。

> [!NOTE]
> 另请注意，`apiVersion` 的值会告知 Azure 哪个版本的 REST API 将与 JSON 资源定义一起使用，并且会影响资源在 `{}` 内应采用的格式化方式。 
> 
> 

#### <a name="sql-server"></a>SQL Server
接下来，单击“JSON 概要”中名为 **SQLServer** 的 SQL Server 资源。

![](./media/app-service-deploy-complex-application-predictably/examinejson-4-sqlserver.png)

请注意以下有关突出显示的 JSON 代码的内容：

* 使用参数可确保已创建资源的命名和配置方式使其与其他资源保持一致。
* SQLServer 资源具有两个嵌套的资源，而每个具有不同的 `type` 值。
* `“resources”: […]` 内（其中定义了数据库和防火墙规则）的嵌套资源具有 `dependsOn` 元素，后者指定根级别 SQLServer 资源的资源 ID。 这会告知 Azure 资源管理器：“创建此资源之前，另一个资源必须已经存在；如果在模板中定义另一个资源，则先创建一个。”
  
  > [!NOTE]
  > 有关如何使用 `resourceId()` 函数的详细信息，请参阅 [Azure 资源管理器模板功能](../azure-resource-manager/resource-group-template-functions-resource.md#resourceid)。
  > 
  > 
* `dependsOn` 元素的影响在于让 Azure 资源管理器能够知道哪些资源可以并行创建，哪些资源必须按顺序创建。 

#### <a name="app-service-app"></a>应用服务应用
现在，让我们继续，看看实际应用本身，这更加复杂。 在“JSON 概要”中单击“[variables(‘apiSiteName’)]”应用以突出显示其 JSON 代码。 你会注意到内容正在变得更加有趣。 为此，我将一个一个地讨论功能：

##### <a name="root-resource"></a>根资源
应用依赖于两个不同的资源。 这意味着只有在创建应用服务计划和 SQL Server 实例后，Azure 资源管理器才会创建应用。

![](./media/app-service-deploy-complex-application-predictably/examinejson-5-webapproot.png)

##### <a name="app-settings"></a>应用设置
应用设置也被定义为嵌套资源。

![](./media/app-service-deploy-complex-application-predictably/examinejson-6-webappsettings.png)

在 `config/appsettings` 的 `properties` 元素中，具有两个 `"<name>" : "<value>"` 格式的应用设置。

* `PROJECT` 是 [KUDU 设置](https://github.com/projectkudu/kudu/wiki/Customizing-deployments)，它告诉 Azure 部署在多项目的 Visual Studio 解决方案中使用哪个项目。 稍后我将向你演示如何配置源代码管理，但由于 ToDoApp 代码位于多项目 Visual Studio 解决方案中，我们需要此设置。
* `clientUrl` 只是应用程序代码使用的应用设置。

##### <a name="connection-strings"></a>连接字符串
连接字符串也被定义为嵌套资源。

![](./media/app-service-deploy-complex-application-predictably/examinejson-7-webappconnstr.png)

在 `config/connectionstrings` 的 `properties` 元素中，每个连接字符串也定义为具有特定的 `"<name>" : {"value": "…", "type": "…"}` 格式的 name:value 对。 对于 `type` 元素，可能的值为 `MySql`、`SQLServer`、`SQLAzure` 和 `Custom`。

> [!TIP]
> 若要获取连接字符串类型的最终列表，请在 Azure PowerShell 中运行以下命令：\[Enum]::GetNames("Microsoft.WindowsAzure.Commands.Utilities.Websites.Services.WebEntities.DatabaseType")
> 
> 

##### <a name="source-control"></a>源代码管理
源代码管理设置也被定义为嵌套资源。 Azure 资源管理器使用此资源来配置连续发布（稍后请参阅 `IsManualIntegration` 上的注意事项），并且还可在 JSON 文件的处理过程中自动启动应用程序代码的部署。

![](./media/app-service-deploy-complex-application-predictably/examinejson-8-webappsourcecontrol.png)

`RepoUrl` 和 `branch` 应该是非常直观的，且应指向 Git 存储库和分支（从其发布）的名称。 同样，这些由输入参数定义。 

请注意，在 `dependsOn` 元素中，除应用资源本身外，`sourcecontrols/web` 还依赖于 `config/appsettings` 和 `config/connectionstrings`。 这是因为一旦配置 `sourcecontrols/web` 后，Azure 部署进程会自动尝试部署、构建和启动应用程序代码。 因此，插入此依赖项可帮助你确保在运行应用程序代码之前，应用程序有权访问所需的应用设置和连接字符串。 

> [!NOTE]
> 另请注意，`IsManualIntegration` 设置为 `true`。 此属性在本教程中是必需的，由于你实际上并不拥有 GitHub 存储库，因此不能实际授权 Azure 配置从 [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) 的连续发布（即，将自动存储库更新推送到 Azure）。 只有当之前已在 [Azure 门户](https://portal.azure.com/)中配置了所有者的 GitHub 凭据时，才可将默认值 `false` 用于指定的存储库。 换言之，如果之前已在 [Azure 门户](https://portal.azure.com/)中使用用户凭据为任何应用将源代码管理设置到 GitHub 或 BitBucket，则 Azure 将记住凭据并在将来每当从 GitHub 或 BitBucket 部署任何应用时使用这些凭据。 但是，如果还没有完成此操作，在 Azure 资源管理器尝试配置应用的源代码管理设置时，JSON 模板的部署会失败，因为它不能使用存储库所有者的凭据登录到 GitHub 或 BitBucket。
> 
> 

## <a name="compare-the-json-template-with-deployed-resource-group"></a>将 JSON 模板与已部署的资源组进行比较
在这里，可在 [Azure 门户](https://portal.azure.com/)中浏览所有应用边栏选项卡，但还有另一个同样有用的工具。 转到 [Azure 资源浏览器](https://resources.azure.com)预览工具，它将提供订阅中所有资源组的 JSON 表示形式，因为它们实际存在于 Azure 后端。 还可看到 Azure 中资源组的 JSON 层次结构如何与用于创建它的模板文件中的层次结构相对应。

例如，当我转到 [Azure 资源浏览器](https://resources.azure.com)工具并在浏览器中展开节点时，可以看到资源组和收集在其各自资源类型下的根级别资源。

![](./media/app-service-deploy-complex-application-predictably/ARM-1-treeview.png)

如果向下钻取应用，应能够看到类似于以下屏幕快照的应用配置详细信息：

![](./media/app-service-deploy-complex-application-predictably/ARM-2-jsonview.png)

再次重申，嵌套资源的层次结构应非常类似于 JSON 模板文件中的层次结构，并且应看到应用设置、连接字符串等正确反映在 JSON 窗格中。 如果此处的设置不存在，则可能指示 JSON 文件存在问题，可借此排查 JSON 模板文件的问题。

## <a name="deploy-the-resource-group-template-yourself"></a>自己部署资源组模板
“部署到 Azure”按钮太好用了，但是只有当你已将 azuredeploy.json 推送到 GitHub 时，它才允许你部署 azuredeploy.json 中的资源组模板。 Azure.NET SDK 还提供了工具，使你能够直接从本地计算机部署任何 JSON 模板文件。 为此，请执行以下步骤：

1. 在 Visual Studio 中，单击“文件” > “新建” > “项目”。
2. 单击“Visual C#” > “云” > “Azure 资源组”，并单击“确定”。
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-1-vsproject.png)
3. 在“选择 Azure 模板”中，选择“空白模板”，并单击“确定”。
4. 将 azuredeploy.json 拖动到新项目的“模板”文件夹。
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-2-copyjson.png)
5. 从解决方案资源管理器中打开复制的 azuredeploy.json。
6. 为了进行演示，让我们通过单击“添加资源”，将一些标准 Application Insight 资源添加到我们的 JSON 文件。 如果只对部署 JSON 文件感兴趣，请跳至部署步骤。
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-3-newresource.png)
7. 选择“适用于 Web 应用的 Application Insights”，确保选择了现有应用服务计划和应用，并单击“添加”。
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-4-newappinsight.png)
   
   现在即可看到在应用服务计划或应用上具有依赖项的几个新资源，具体取决于该资源及它的作用。 这些资源不由其现有定义启用，而要对此进行更改。
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-5-appinsightresources.png)
8. 在“JSON 概要”中，单击“appInsights AutoScale”以突出显示其 JSON 代码。 这是针对应用服务计划的缩放设置。
9. 在突出显示的 JSON 代码中，找到 `location` 和 `enabled` 属性并对其进行如下设置。
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-6-autoscalesettings.png)
10. 在“JSON 概要”中，单击“CPUHigh appInsights”以突出显示其 JSON 代码。 这是一个警报。
11. 找到 `location` 和 `isEnabled` 属性并对其进行如下设置。 对其他三个警报（紫色警报）执行相同的操作。
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-7-alerts.png)
12. 现在可以开始部署了。 右键单击该项目，并选择“部署” > “新部署”。
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-8-newdeployment.png)
13. 如果尚未执行该操作，则登录到 Azure 帐户。
14. 选择订阅中的现有资源组或创建一个新资源组，选择“azuredeploy.json”，并单击“编辑参数”。
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-9-deployconfig.png)
    
    现在即可在一张不错的表中编辑在模板文件中定义的所有参数。 定义默认值的参数将已具有其默认值，并且定义允许值的列表的参数会显示为下拉列表。
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-10-parametereditor.png)
15. 填写所有空参数，并使用 **repoUrl** 中的 [ToDoApp 的 GitHub 存储库地址](https://github.com/azure-appservice-samples/ToDoApp.git)。 然后，单击“保存”。
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-11-parametereditorfilled.png)
    
    > [!NOTE]
    > 自动缩放是**标准**层或更高层中提供的一项功能，而计划级别警报是**基本**层或更高层中提供的功能，需要将 **sku** 参数设置为**标准**或**高级**，使所有新 App Insights 资源亮起。
    > 
    > 
16. 单击“部署”。 如果选择了“保存密码”，密码将**以纯文本格式**保存在参数文件中。 否则，需要在部署过程中输入数据库密码。

就这么简单！ 现在只需转到 [Azure 门户](https://portal.azure.com/)和 [Azure 资源浏览器](https://resources.azure.com)工具，便可看到添加到 JSON 部署的应用程序中的新警报和自动缩放设置。

这部分中的步骤主要完成了以下内容：

1. 准备模板文件
2. 创建参数文件以搭配模板文件
3. 使用参数文件部署模板文件

最后一步通过 PowerShell cmdlet 轻松完成。 若要查看当 Visual Studio 部署应用程序时所执行的操作，请打开 Scripts\Deploy-AzureResourceGroup.ps1。 存在大量的代码，但我只突出显示使用参数文件部署模板文件所需的所有相关代码。

![](./media/app-service-deploy-complex-application-predictably/deploy-12-powershellsnippet.png)

最后一个 cmdlet，`New-AzureResourceGroup`，实际执行了该操作。 所有这一切向你展示了，借助工具可相对简单地以可预见的方式部署云应用程序。 每使用相同的参数文件在相同的模板上运行该 cmdlet 时，都会获得相同的结果。

## <a name="summary"></a>总结
在 DevOps 中，可重复性和可预见性对成功部署由微服务构成的高扩展性应用程序至关重要。 在本教程中，已经通过使用 Azure 资源管理器模板将一个由两个微服务构成的应用程序作为单个资源组部署到 Azure。 但愿这已提供所需的知识，使你能够在 Azure 中开始将应用程序转换为模板，并且能够以可预见的方式设置和部署它。 

<a name="resources"></a>

## <a name="more-resources"></a>更多资源
* [Azure 资源管理器模板语言](../azure-resource-manager/resource-group-authoring-templates.md)
* [创作 Azure Resource Manager 模板](../azure-resource-manager/resource-group-authoring-templates.md)
* [Azure 资源管理器模板功能](../azure-resource-manager/resource-group-template-functions.md)
* [使用 Azure 资源管理器模板部署应用程序](../azure-resource-manager/resource-group-template-deploy.md)
* [将 Azure PowerShell 与 Azure 资源管理器配合使用](../azure-resource-manager/powershell-azure-resource-manager.md)
* [Azure 中的资源组部署故障排除](../azure-resource-manager/resource-manager-common-deployment-errors.md)

## <a name="next-steps"></a>后续步骤

要了解本文中部署的资源类型的 JSON 语法和属性，请参阅：

* [Microsoft.Sql/servers](/azure/templates/microsoft.sql/servers)
* [Microsoft.Sql/servers/databases](/azure/templates/microsoft.sql/servers/databases)
* [Microsoft.Sql/servers/firewallRules](/azure/templates/microsoft.sql/servers/firewallrules)
* [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)
* [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)
* [Microsoft.Web/sites/slots](/azure/templates/microsoft.web/sites/slots)
* [Microsoft.Insights/autoscalesettings](/azure/templates/microsoft.insights/autoscalesettings)