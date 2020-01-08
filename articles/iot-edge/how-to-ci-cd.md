---
title: 持续集成和持续部署 - Azure IoT Edge | Microsoft Docs
description: 设置持续集成和持续部署 - Azure IoT Edge 以及 Azure DevOps、Azure Pipelines
author: shizn
manager: philmea
ms.author: xshi
ms.date: 08/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: e14025a5a7a3e81404498638d6f6f9c5ff18ed58
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650794"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>向 Azure IoT Edge 进行持续集成和持续部署

借助 Azure Pipelines 中的内置 Azure IoT Edge 任务，可以轻松地在 Azure IoT Edge 应用程序中采用 DevOps。 本文演示了如何使用 Azure Pipelines 的持续集成和持续部署功能，快速高效地生成和测试应用程序并将其部署到 Azure IoT Edge。 

![示意图 - 用于开发和生产的 CI 和 CD 分支](./media/how-to-ci-cd/cd.png)

在本文中，你将学习如何使用 Azure Pipelines 的内置 Azure IoT Edge 任务为 IoT Edge 解决方案创建两个管道。 Azure IoT Edge 任务可以使用四种操作。
   - **Azure IoT Edge 生成模块映像**采用 IoT Edge 解决方案代码, 并生成容器映像。
   - **Azure IoT Edge 推送模块映像**将模块映像推送到指定的容器注册表。
   - **Azure IoT Edge 生成部署清单**使用一个部署. 模板 json 文件和变量, 然后生成最终的 IoT Edge 部署清单文件。
   - **Azure IoT Edge 部署到 IoT Edge 设备**有助于创建 IoT Edge 为单/多 IoT Edge 设备的部署。

## <a name="prerequisites"></a>先决条件

* Azure Repos 存储库。 如果没有存储库，可[在项目中创建一个新的 Git 存储库](https://docs.microsoft.com/azure/devops/repos/git/create-new-repo?view=vsts&tabs=new-nav)。
* 提交 IoT Edge 解决方案并将其推送到存储库。 如果要为测试本文创建新的示例解决方案，请按照[在 Visual Studio Code 中开发和调试模块](how-to-vs-code-develop-module.md)或[在 Visual Studio 中开发和调试 C# 模块](how-to-visual-studio-develop-csharp-module.md)中的步骤进行操作。
   * 对于本文，你只需要 Visual Studio Code 或 Visual Studio 中的 IoT Edge 模板创建的解决方案文件夹。 在继续操作之前，无需生成、推送、部署或调试此代码。 你将在 Azure Pipelines 中设置这些进程。 
   * 如果要创建新解决方案，请首先在本地克隆存储库。 然后，在创建解决方案时，可以选择直接在存储库文件夹中创建它。 可以轻松从中提交和推送新文件。 
* 容器注册表，你可以在其中推送模块图像。 可使用 [Azure 容器注册表](https://docs.microsoft.com/azure/container-registry/)或第三方注册表。 
* 具有至少 IoT Edge 设备的活动 [IoT 集线器](../iot-hub/iot-hub-create-through-portal.md)，用于测试单独的测试和生产部署阶段。 可按照快速入门文章在 [Linux](quickstart-linux.md) 或 [Windows](quickstart.md) 上创建 IoT Edge 设备


要详细了解如何使用 Azure 存储库，请参阅 [Share your code with Visual Studio and Azure Repos](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts)（与 Visual Studio 和 Azure 存储库共享代码）

## <a name="configure-continuous-integration"></a>配置持续集成
在本部分中，将创建新的生成管道。 管道配置，使其在用户向 IoT Edge 解决方案签入任何更改时自动运行，并发布生成日志。

>[!NOTE]
>本文使用 Azure DevOps 可视化设计器。 在按照本部分中的步骤操作之前，请关闭新 YAML 管道创建体验的预览功能。 
>1. 在 Azure DevOps 中，选择个人资料图标，然后选择“预览功能”。
>2. 关闭“新的 YAML 管道创建体验”。 
>
>有关详细信息，请参阅[创建生成管道](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav#create-a-build-pipeline)。

1. 登录 Azure DevOps 组织 (**https:\//dev.azure.com/{your organization}/** ) 并打开包含 IoT Edge 解决方案存储库的项目。

   在本文中，我们创建了名为“IoTEdgeRepo”的存储库。 该存储库包含“IoTEdgeSolution”，其中包含名为“filtermodule”的模块代码。 

   ![打开 DevOps 项目](./media/how-to-ci-cd/init-project.png)

2. 导航到项目中的 Azure Pipelines。 打开“生成”选项卡，然后选择“新建管道”。 或者，如果已有生成管道，则选择“新建”按钮。 然后选择“新建生成管道”。

    ![创建新的生成管道](./media/how-to-ci-cd/add-new-build.png)

3. 按照提示创建管道。 

   1. 提供新生成管道的源信息。 选择“Azure Repos Git”作为源，然后选择 IoT Edge 解决方案代码所在的项目、存储库和分支。 然后选择“继续”。 

      ![选择管道源](./media/how-to-ci-cd/pipeline-source.png)

   2. 选择“空作业”而不是模板。 

      ![从空进程开始](./media/how-to-ci-cd/start-with-empty.png)

4. 创建管道后，将转到管道编辑器。 在管道描述中，根据目标平台选择正确的代理池： 
    
   * 若要在用于 Linux 容器的 amd64 平台中生成模块，请选择“托管 Ubuntu 1604”

   * 如果想在平台 amd64 中为 Windows 1809 容器生成模块，则需要[在 Windows 上设置自托管代理](https://docs.microsoft.com/azure/devops/pipelines/agents/v2-windows?view=vsts)。

   * 如果要在平台 arm32v7 或 arm64 for Linux 容器中生成模块, 需在[linux 上设置自托管代理](https://blogs.msdn.microsoft.com/iotdev/2018/11/13/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent/)。
    
     ![配置生成代理池](./media/how-to-ci-cd/configure-env.png)

5. 管道预先配置了名为“代理作业 1”的作业。 选择加号 (+)，向作业添加三个任务：**Azure IoT Edge**两次, 请**将文件复制**一次并发布一次**生成项目**。 （将鼠标悬停在每个任务的名称上，以查看“添加”按钮。）

   ![添加 Azure IoT Edge 任务](./media/how-to-ci-cd/add-iot-edge-task.png)

   添加四个任务后, 代理作业如以下示例所示:
    
   ![生成管道中的三个任务](./media/how-to-ci-cd/add-tasks.png)

6. 选择第一个“Azure IoT Edge”任务进行编辑。 此任务用指定的目标平台生成解决方案中的所有模块。

   * **显示名称**：接受默认的“Azure IoT Edge - 生成模块图像”。
   * **操作**：接受默认的“生成模块图像”。 
   * **.template.json 文件**：选择省略号 (...) 并导航到包含 IoT Edge 解决方案的存储库中的“deployment.template.json”文件。 
   * **默认平台**：根据目标 IoT Edge 设备为模块选择相应的平台。 
   * **输出变量**：输出变量包含引用名称，可使用该名称配置生成 deployment.json 文件的文件路径。 将引用名称设置为令人难忘的内容，如“边缘”。 

7. 选择第二个“Azure IoT Edge”任务进行编辑。 此任务会把所有模块图像推送到所选容器注册表。

   * **显示名称**：操作字段更改时，显示名称会自动更新。 
   * **操作**：使用下拉列表选择“推送模块图像”。 
   * **容器注册表类型**：选择用于存储模块图像的容器注册表类型。 窗体会根据选择的注册表类型进行更改。 如果选择“Azure 容器注册表”，请使用下拉列表选择 Azure 订阅和容器注册表的名称。 如果选择“通用容器注册表”，请选择“新建”以创建注册表服务连接。 
   * **.template.json 文件**：选择省略号 (...) 并导航到包含 IoT Edge 解决方案的存储库中的“deployment.template.json”文件。 
   * **默认平台**：选择与生成的模块图像相同的平台。

   如果有多个用于托管模块映像的容器注册表，则需重复此任务，选择其他容器注册表，并使用高级设置中的“绕过模块”来绕过不适用于此特定注册表的映像。

8. 选择 "**复制文件**" 任务进行编辑。 使用此任务可将文件复制到项目暂存目录。

   * **显示名称**：将文件复制到:放置文件夹。
   * **内容**：在此部分中放置两行`deployment.template.json` , `**/module.json`和。 这两种类型的文件是 IoT Edge 部署清单生成的输入。 需要复制到项目暂存文件夹并为发布管道发布。
   * **目标文件夹**:放置变量`$(Build.ArtifactStagingDirectory)`。 有关说明, 请参阅[生成变量](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables)。

9. 选择“发布生成项目”任务进行编辑。 提供任务的项目暂存目录路径, 以便可以将路径发布到发布管道。
   
   * **显示名称**：发布项目: 删除。
   * **要发布的路径**:放置变量`$(Build.ArtifactStagingDirectory)`。 有关说明, 请参阅[生成变量](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables)。
   * **项目名称**: drop。
   * **项目发布位置**:Azure Pipelines。


10. 打开“触发器”标签，然后选中“启用持续集成”框。 确保包含代码的分支已包括在内。

    ![开启“持续集成”触发器](./media/how-to-ci-cd/configure-trigger.png)

11. 使用“保存”按钮保存新的生成管道。

此管道现配置为在将新代码推送到存储库时自动运行。 发布管道项目的最后一项任务是触发发布管道。 继续下一部分以生成发布管道。 

## <a name="configure-continuous-deployment"></a>配置持续部署
在本部分中，会创建一个发布管道，此管道配置为在生成管道放置项目时自动运行，并且它将在 Azure Pipelines 中显示部署日志。

创建新管道并添加新阶段 

1. 在“发布”选项卡上，选择“+ 新键管道”。 或者，如果已有发布管道，则选择“+ 新建”按钮并选择“+ 新建发布管道”。  

    ![添加发布管道](./media/how-to-ci-cd/add-release-pipeline.png)

2. 提示选择模板时，选择以“空作业”开头。

    ![从空作业开始](./media/how-to-ci-cd/start-with-empty-job.png)

3. 新版本管道初始化为一个阶段，称为“阶段 1”。 将阶段1重命名为 "**开发**", 并将其视为测试环境。 通常, 连续部署管道有多个阶段, 包括**开发**、**过渡**和**生产**。可以根据 DevOps 实践创建更多内容。 重命名后关闭阶段详细信息窗口。 

4. 将发布链接到生成管道发布的生成项目。 在项目区域中，单击“添加”。

   ![添加项目](./media/how-to-ci-cd/add-artifacts.png)  
    
5. 在“添加项目”页中，选择源类型“生成”。 然后选择项目和创建的生成管道。 然后选择“添加”。

   ![添加生成项目](./media/how-to-ci-cd/add-an-artifact.png)

6. 打开项目触发器并选择切换以启用持续部署触发器。 现在，每次有新的生成时，都会创建新版本。

   ![配置“持续部署”触发器](./media/how-to-ci-cd/add-a-trigger.png)

7. **开发**阶段预配置了一个作业和零个任务。 从 "管道" 菜单中选择 "**任务**", 然后选择 "**开发**阶段"。  选择作业和任务计数以在此阶段配置任务。

    ![配置开发任务](./media/how-to-ci-cd/view-stage-tasks.png)

8. 在**开发**阶段, 应会看到默认**代理作业**。 可配置有关代理作业的详细信息，但部署任务对平台不敏感，因此可在“代理池”（或你自己管理的任何其他代理）中使用“托管 VS2017”或“托管 Ubuntu 1604”。 

9. 选择加号 ( **+** ) 以添加两个任务。 搜索并添加两次**Azure IoT Edge** 。

    ![为开发人员添加任务](./media/how-to-ci-cd/add-task-qa.png)

10. 选择第一个**Azure IoT Edge**任务并对其配置以下值:

    * **显示名称**：操作字段更改时，显示名称会自动更新。 
    * **操作**：使用下拉列表选择 "**生成部署清单**"。 更改操作值还会更新要匹配的任务显示名称。
    * **.template.json 文件**：放置路径`$(System.DefaultWorkingDirectory)/Drop/drop/deployment.template.json`。 该路径从生成管道发布。
    * **默认平台**：生成模块映像时, 请选择相同的值。
    * **输出路径**:放置路径`$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json`。 此路径是最后一个 IoT Edge 部署清单文件。

    这些配置有助于替换`deployment.template.json`文件中的模块图像 url。 **生成部署清单**还有助于将变量替换为在`deployment.template.json`文件中定义的准确值。 在 VS/VS Code 中, 将在`.env`文件中指定实际值。 在 Azure Pipelines 中, 可以在 "释放管道变量" 选项卡中设置值。转到 "变量" 选项卡, 并按如下所示配置名称和值。

    * **ACR_ADDRESS**:Azure 容器注册表地址。 
    * **ACR_PASSWORD**:Azure 容器注册表密码。
    * **ACR_USER**:Azure 容器注册表用户名。

    如果项目中有其他变量, 则可以在此选项卡中指定名称和值。**生成部署清单**只能识别这些变量在`${VARIABLE}`风格上, 请确保在`*.template.json`文件中使用此文件。

    ![配置发布管道的变量](./media/how-to-ci-cd/configure-variables.png)

10. 选择第二个**Azure IoT Edge**任务并对其配置以下值:

    * **显示名称**：操作字段更改时，显示名称会自动更新。 
    * **操作**：使用下拉列表选择 "**部署到 IoT Edge 设备**"。 更改操作值还会更新要匹配的任务显示名称。
    * **Azure 订阅**：选择包含 IoT 中心的订阅。
    * **IoT 中心名称**：选择 IoT 中心。 
    * **选择单个/多个设备**：选择是否要将发布管道部署到一个设备或多个设备。 
      * 如果部署到单个设备，请输入“IoT Edge设备 ID”。 
      * 如果要部署到多个设备，请指定设备“目标条件”。 目标条件是一个筛选器, 用于匹配 IoT 中心内一组 IoT Edge 设备。 若想将设备标记用作条件，则需要使用 IoT 中心设备孪生更新对应的设备标记。 在高级设置中更新“IoT Edge 部署 ID”和“IoT Edge 部署优先级”。 有关为多个设备创建部署的详细信息，请参阅[了解 IoT Edge 自动部署](module-deployment-monitoring.md)。
    * 展开 "高级设置", 选择 " **IoT Edge 部署 ID**" `$(System.TeamProject)-$(Release.EnvironmentName)`, 然后输入变量。 这会将项目和发布名称映射为你的 IoT Edge 部署 ID。

11. 选择“保存”，将更改保存到新发布管道。 从菜单中选择“管道”，返回管道视图。 
    
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>使用生成和发布管道验证 IoT Edge CI/CD

要触发生成作业，可以将提交推送到源代码存储库或手动触发该作业。 在本部分中，手动触发 CI/CD 管道以测试它是否正常工作。 然后验证部署是否成功。

1. 导航到在本文开头创建的生成管道。 

2. 通过选择“队列”按钮，可在生成管道中触发生成作业（如以下屏幕截图所示）。

    ![手动触发器](./media/how-to-ci-cd/manual-trigger.png)

3. 选择生成作业以查看其进度。 如果生成管道已成功完成, 则会触发发布到**开发**阶段。 

    ![生成日志](./media/how-to-ci-cd/build-logs.png)

4. 成功的**开发**版本创建了 IoT Edge 部署到目标 IoT Edge 设备。

    ![发布到开发](./media/how-to-ci-cd/pending-approval.png)

5. 单击 "**开发**阶段" 以查看发布日志。

    ![发布日志](./media/how-to-ci-cd/release-logs.png)



## <a name="next-steps"></a>后续步骤
* IoT Edge [Azure DevOps 项目中的](how-to-devops-project.md)DevOps 最佳实践示例 IoT Edge
* 在[了解单设备 IoT Edge 部署或大规模 IoT Edge 部署](module-deployment-monitoring.md)中了解 IoT Edge 部署
* 阅读[大规模地部署和监视 IoT Edge 模块](how-to-deploy-monitor.md)，详细了解创建、更新或删除部署的步骤。
