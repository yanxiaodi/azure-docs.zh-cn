---
title: 教程：使用 Azure DevOps Projects 将 ASP.NET 应用部署到 Azure Functions
description: 可以通过 Azure DevOps Projects 轻松地在 Azure 上开始操作。 使用 DevOps Projects，可通过几个快速步骤将 ASP.NET 应用部署到 Azure Functions。
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 06/20/2019
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 6894f9bc6c803e2692afb54d7459adf6b0e6dbd6
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67828152"
---
# <a name="continuously-deploy-to-azure-functions-with-devops-projects"></a>使用 DevOps Projects 持续部署到 Azure Functions

Azure DevOps Projects 提供一种简化的体验，你在其中既可使用现有的代码和 Git 存储库，也可选择一个示例应用程序，以便创建连接到 Azure 的持续集成 (CI) 和持续交付 (CD) 管道。

DevOps Projects 还可以：

* 自动创建 Azure 资源，例如 Azure Functions。

* 在 Azure DevOps 中为 CI/CD 创建并配置一个发布管道

在本教程中，将：

> [!div class="checklist"]
>* 使用 DevOps Projects 将 ASP.NET 应用部署到 Azure Function
>* 配置 Azure DevOps 和 Azure 订阅
>* 检查 Azure Function
>* 检查 CI 管道
>* 检查 CD 管道
>* 提交对 Git 所做的更改并将其自动部署到 Azure
>* 清理资源

目前，函数支持的运行时为 **.NET** 和 **Node.js**。 我们在本教程中使用 .NET 运行时来部署到 Azure Functions。 

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 可以通过 [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) 免费获取一个

## <a name="use-devops-projects-to-deploy-an-aspnet-app-to-azure-functions"></a>使用 DevOps Projects 将 ASP.NET 应用部署到 Azure Functions

DevOps Projects 在 Azure Pipelines 中创建 CI/CD 管道。 可以创建新的 Azure DevOps 组织，或使用现有的组织。 DevOps Projects 还会在所选的 Azure 订阅中创建 Azure 资源，例如 IoTHub。

1. 登录到 [Azure 门户](https://portal.azure.com)

1. 在左窗格中，选择“创建资源”。

1. 在搜索框中键入 **DevOps Projects**，然后单击“添加”。

   ![DevOps 项目](_img/azure-devops-project-functions/devops-project.png)

1. 依次选择“.NET”、“下一步”。 在“选择应用程序框架”下，选择“ASP.NET”，然后单击“下一步”。

1. 选择 **Function App**，然后选择“下一步”。

## <a name="configure-azure-devops-and-azure-subscription"></a>配置 Azure DevOps 和 Azure 订阅

1. 输入 Azure DevOps 项目的名称。

1. 创建新的 Azure DevOps 组织，或选择现有的组织。

1. 选择 Azure 订阅。

1. 若要查看其他 Azure 配置设置并确定定价层和位置，请单击“其他设置”。 此窗格显示了各种用于配置 Azure 服务定价层和位置的选项。

1. 退出 Azure 配置区域，然后选择“完成”。

1. 配置过程在几分钟后完成。 此过程会在你的 Azure DevOps 组织的 Git 存储库中设置一个示例 ASP.NET 应用（Function App），创建 Application Insights，执行 CI/CD 管道，并将应用部署到 Azure。

   完成所有操作后，Azure DevOps Projects 仪表板会显示在 Azure 门户中。 也可直接从 Azure 门户中的“所有资源”直接转到 DevOps Projects 仪表板。

   在此仪表板中可以查看 Azure DevOps 代码存储库、CI/CD 管道和 Azure Function。 可在 Azure DevOps 管道中配置其他 CI/CD 选项。 在右侧，选择要查看的“Function App”。

## <a name="examine-the-function-app"></a>检查 Function App

DevOps Projects 会自动配置函数应用，你可以浏览和自定义此应用。 若要了解函数应用，请执行以下操作：

1. 转到 DevOps Projects 仪表板。

    ![DevOps Projects 仪表板](_img/azure-devops-project-functions/devops-projects-dashboard.png)

1. 在右侧选择函数应用。 此时会打开函数应用的窗格。 可在此视图中执行各种操作，例如监视、搜索日志。

    ![Function App](_img/azure-devops-project-functions/function-app.png)

## <a name="examine-the-ci-pipeline"></a>检查 CI 管道

DevOps Projects 自动在你的 Azure DevOps 组织中配置一个 CI/CD 管道。 可以浏览和自定义此管道。 若要了解此管道，请执行以下操作：

1. 转到 DevOps Projects 仪表板。

1. 单击“生成”下的超链接。 浏览器标签页会显示新项目的生成管道。

    ![构建](_img/azure-devops-project-functions/build.png)

1. 选择“编辑”。 在此窗格中，可以检查生成管道的各种任务。 该生成会执行各种任务，例如，从 Git 存储库提取源代码、生成应用程序、运行单元测试，以及发布用于部署的输出。

1. 选择“触发器”。 DevOps Projects 会自动创建一个 CI 触发器，每次向存储库提交内容都会启动新的生成。 （可选）可以选择在 CI 过程中包括或排除分库。

1. 选择“保留期”。 可以根据方案指定策略，以保留或删除特定数目的生成。

1. 在生成管道的顶部，选择生成管道名称。

1. 将生成管道的名称更改为更具描述性的名称，然后从“保存并排队”下拉列表中选择“保存”。

1. 在生成管道名称下，选择“历史记录”。 此窗格显示最近针对生成所做的更改的审核线索。 Azure DevOps 会跟踪对生成管道所做的任何更改，并允许进行版本比较。

## <a name="examine-the-cd-release-pipeline"></a>检查 CD 发布管道

DevOps Projects 会自动创建并配置从 Azure DevOps 组织部署到 Azure 订阅所要执行的步骤。 这些步骤包括配置 Azure 服务连接，以便在 Azure 订阅中进行 Azure DevOps 身份验证。 自动化还会创建一个发布管道，该管道提供到 Azure 的 CD。 若要详细了解发布管道，请执行以下操作：

1. 导航到“管道|发布”。

1. 单击“编辑”。

1. 在“项目”下选择“删除”。 在前述步骤中检查过的生成管道会生成用于项目的输出。

1. 在“删除”图标的右侧，选择“持续部署触发器”。 此发布管道有一个已启用的 CD 触发器，每次有新的生成项目可用时，此触发器就会执行部署。 （可选）可以禁用此触发器，这样就需要手动执行部署。

1. 在右侧选择“查看发布”，以显示发布历史记录。

1. 单击发布，以便显示管道。 单击任何环境即可查看版本“摘要”、“提交”、关联的“工作项”。

1. 选择“提交”。 此视图显示与此部署关联的代码提交。 请对发布进行比较，查看不同部署之间的提交差异。

1. 选择“查看日志”。 日志包含有关部署过程的有用信息。 可以在部署期间和之后查看日志。

## <a name="commit-code-changes-and-execute-cicd"></a>提交代码更改并执行 CI/CD

> [!NOTE]
> 以下过程通过进行简单的文本更改来测试 CI/CD 管道。

现在，可以使用可将最新工作部署到 Azure Function 的 CI/CD 过程，与某个团队展开应用协作。 每次对 Git 存储库进行更改都会在 Azure DevOps 中启动一个生成，CD 管道可以执行到 Azure 的部署。 遵循本部分所述的过程，或使用其他方法提交对存储库所做的更改。 例如，可以在常用工具或 IDE 中克隆 Git 存储库，然后将更改推送到此存储库。

1. 在 Azure DevOps 菜单中选择“存储库”|“文件”，然后转到你的存储库。

1. 基于在创建过程中选择的应用程序语言，存储库已包含名为 SampleFunctionApp 的代码。 打开 **Application/SampleFunctionApp/Function1.cs** 文件。

1. 选择“编辑”，然后对**第 31 行**进行更改。 例如，可以将其更新为“你好！欢迎体验使用 DevOps Projects 的 Azure Functions”

1. 在右上角选择“提交”，然后再次选择“提交”以推送更改。

1. 打开 **Application/SampleFunctionApp.Test/Function1TestRunner.cs** 文件。 

1. 选择“编辑”，然后对**第 21 行**进行更改。 例如，可以将其更新为“你好！欢迎体验使用 Azure DevOps Projects 的 Azure Functions”。

     片刻之后，某个生成会在 Azure DevOps 中启动，并且某个发布将会执行以部署更改。 使用 Azure DevOps 组织通过 DevOps Projects 仪表板或浏览器监视生成状态。

## <a name="clean-up-resources"></a>清理资源

不再需要创建的相关资源时，可将其删除。 请使用 DevOps Projects 仪表板上的“删除”功能。

## <a name="next-steps"></a>后续步骤

可以选择根据所在团队的需求修改这些生成和发布管道。 也可以将此 CI/CD 模式用作其他管道的模板。 本教程介绍了如何：

> [!div class="checklist"]
> * 使用 DevOps Projects 将 ASP.NET Core 应用部署到 Azure Function
> * 配置 Azure DevOps 和 Azure 订阅 
> * 检查 Azure Function
> * 检查 CI 管道
> * 检查 CD 管道
> * 提交对 Git 所做的更改并将其自动部署到 Azure
> * 清理资源

