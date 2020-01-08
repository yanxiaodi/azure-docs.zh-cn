---
title: Azure Service Fabric 修补业务流程应用程序 | Microsoft Docs
description: 用于在 Service Fabric 群集中自动修补操作系统的应用程序。
services: service-fabric
documentationcenter: .net
author: khandelwalbrijeshiitr
manager: chackdan
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/01/2019
ms.author: brkhande
ms.openlocfilehash: 2aa2dd8373a9568478a02691ca5e6a43e80cd408
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2019
ms.locfileid: "71289421"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>在 Service Fabric 群集中修补 Windows 操作系统

> 
> [!IMPORTANT]
> 应用程序版本 1.2.* 将在 2019 年 4 月 30 日停止支持。 请升级到最新版本。


[Azure 虚拟机规模集自动 OS 映像升级](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade)是使操作系统保持在 Azure 中进行修补的最佳做法，而修补业务流程应用程序 (POA) 是 Service Fabrics RepairManager Systems 服务的包装器，它可为非 Azure 托管群集启用基于配置的 OS 修补计划。 非 Azure 托管群集不需要 POA，但需要按升级域计划修补程序安装，以便在不停机的情况下修补 Service Fabric 群集主机。

POA 是一个 Azure Service Fabric 应用程序，可在 Service Fabric 群集中自动修补操作系统，而无需停机。

修补业务流程应用提供以下功能：

- 自动的操作系统更新安装。 自动下载并安装操作系统更新。 可根据需要重新启动群集节点，且无需让群集停机。

- 群集感知修补和运行状况集成。 在应用更新时，修补业务流程应用会监视群集节点的运行状况。 群集节点的升级方式为一次一个节点，或一次一个升级域。 如果群集的运行状况由于修补进程而恶化，此时修补将停止以防止问题加重。

## <a name="internal-details-of-the-app"></a>应用的内部详细信息

修补业务流程应用由以下子组件组成：

- **协调器服务**：此有状态服务负责：
    - 协调整个群集上的 Windows 更新作业。
    - 存储已完成的 Windows 更新操作的结果。
- **节点代理服务**：此无状态服务在所有 Service Fabric 群集节点上运行。 该服务负责：
    - 启动节点代理 NTService。
    - 监视节点代理 NTService。
- **节点代理 NTService**：此 Windows NT 服务以更高级别的特权 (SYSTEM) 运行。 相比之下，节点代理服务和协调器服务以较低级别的特权 (NETWORK SERVICE) 运行。 该服务负责在所有群集节点上执行以下 Windows 更新作业：
    - 在节点上禁用自动 Windows 更新。
    - 根据用户提供的策略下载并安装 Windows 更新。
    - 安装 Windows 更新后重新启动计算机。
    - 将 Windows 更新的结果上传到协调器服务。
    - 在某个操作用完所有重试次数后失败时报告运行状况。

> [!NOTE]
> 修补业务流程应用使用 Service Fabric“修复管理器系统服务”来禁用/启用节点和执行运行状况检查。 修补业务流程应用创建的修复任务跟踪每个节点的 Windows 更新进度。

## <a name="prerequisites"></a>先决条件

> [!NOTE]
> 所需的最低 .NET Framework 版本为 4.6。

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>启用修复管理器服务（如果尚未运行）

修补业务流程应用需要在群集上启用修复管理器系统服务。

#### <a name="azure-clusters"></a>Azure 群集

银级持久层中的 Azure 群集默认启用修复管理器服务。 黄金级耐久层中的 Azure 群集可能启用或不启用修复管理器服务，具体取决于这些群集的创建时间。 铜级持久层中的 Azure 群集默认不启用修复管理器服务。 如果已启用该服务，可以看到它在 Service Fabric Explorer 的系统服务部分中运行。

##### <a name="azure-portal"></a>Azure 门户
在设置群集时，可以从 Azure 门户启用修复管理器。 在配置群集时选择“附加功能”下的“包含修复管理器”选项。
![从 Azure 门户启用修复管理器的映像](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="azure-resource-manager-deployment-model"></a>Azure 资源管理器部署模型
另外，也可以使用 [Azure 资源管理器部署模型](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)在新的或现有 Service Fabric 群集上启用修复管理器服务。 获取要部署的群集的模板。 可以使用示例模板，或者创建自定义 Azure 资源管理器部署模型模板。 

若要使用 [Azure 资源管理器部署模型模板](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)启用修复管理器服务，请执行以下操作：

1. 首先检查 `Microsoft.ServiceFabric/clusters` 资源的 `apiversion` 是否设置为 `2017-07-01-preview`。 如果不是，则需要将 `apiVersion` 更新为值 `2017-07-01-preview` 或更高的值：

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. 现在，通过在 `fabricSettings` 节后面添加以下 `addonFeatures` 节来启用修复管理器服务：

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. 通过这些更改更新群集模板后，应用更改并等待升级完成。 现在可以看到修复管理器系统服务在群集中运行。 它在 Service Fabric Explorer 中的系统服务部分中被称为 `fabric:/System/RepairManagerService`。 

### <a name="standalone-on-premises-clusters"></a>独立本地群集

可以使用[独立 Windows 群集的配置设置](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest)在新的和现有的 Service Fabric 群集上启用修复管理器服务。

启用修复管理器服务：

1. 首先需要检查[常规群集配置](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest#general-cluster-configurations)中的 `apiversion` 是否设置为 `04-2017` 或更高：

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

2. 现在，通过在 `fabricSettings` 节后面添加以下 `addonFeatures` 节来启用修复管理器服务，如下所示：

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. 通过这些更改更新群集清单后，使用已更新的群集清单[创建新群集](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-for-windows-server)或[升级群集配置](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-windows-server)。 群集使用已更新的群集清单运行后，就可以看到修复管理器系统服务在群集中运行，该服务在 Service Fabric Explorer 中的系统服务部分下被称为 `fabric:/System/RepairManagerService`。

### <a name="configure-windows-updates-for-all-nodes"></a>为所有节点配置 Windows 更新

自动 Windows 更新可能会导致可用性丢失，因为多个群集节点可能同时重启。 修补业务流程应用默认会尝试在每个群集节点上禁用自动 Windows 更新。 但是，如果设置由管理员或组策略管理，我们建议显式将 Windows 更新策略设置为“下载之前发出通知”。

## <a name="download-the-app-package"></a>下载应用包

若要下载应用程序包，请访问修补业务流程应用程序的 GitHub 发行[页面](https://github.com/microsoft/Service-Fabric-POA/releases/latest/)。

## <a name="configure-the-app"></a>配置应用

可配置修补业务流程应用的行为来满足需求。 在创建或更新应用程序的过程中，通过传入应用程序参数来替代默认值。 可以通过在 cmdlet `Start-ServiceFabricApplicationUpgrade` 或 `New-ServiceFabricApplication` 中指定 `ApplicationParameter` 来提供应用程序参数。

|**Parameter**        |类型                          | **详细信息**|
|:-|-|-|
|MaxResultsToCache    |Long                              | 应缓存的 Windows 更新结果的最大数。 <br>在假定以下情况时，默认值为 3000： <br> - 节点数为 20。 <br> - 节点上每月发生的更新次数为 5。 <br> - 每个操作的结果数可为 10。 <br> - 过去三个月的结果应已存储。 |
|TaskApprovalPolicy   |Enum <br> { NodeWise, UpgradeDomainWise }                          |TaskApprovalPolicy 指示协调器服务用于跨 Service Fabric 群集节点安装 Windows 更新的策略。<br>                         允许值包括： <br>                                                           <b>NodeWise</b>。 每次在一个节点上安装 Windows 更新。 <br>                                                           <b>UpgradeDomainWise</b>。 每次在一个升级域上安装 Windows 更新。 （在最大程度情况下，属于升级域的所有节点都可进行 Windows 更新。）<br> 请参阅[常见问题解答](#frequently-asked-questions)部分，了解如何确定最适合你的群集的策略。
|LogsDiskQuotaInMB   |长  <br> （默认值：1024）               |可在节点本地持久保存的修补业务流程应用日志的最大大小，以 MB 为单位。
| WUQuery               | string<br>（默认值："IsInstalled=0"）                | 用于获取 Windows 更新的查询。 有关详细信息，请参阅 [WuQuery](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)。
| InstallWindowsOSOnlyUpdates | 布尔 <br> （默认值：false）                 | 使用此标志来控制应当下载并安装哪些更新。 允许以下值 <br>true - 仅安装 Windows 操作系统更新。<br>false - 在计算机上安装所有可用的更新。          |
| WUOperationTimeOutInMinutes | Int <br>（默认值：90%）                   | 指示任何 Windows 更新操作（搜索、下载或安装）的超时。 在指定的超时内未完成的操作将被中止。       |
| WURescheduleCount     | Int <br> （默认值：5）                  | 在操作持续失败的情况下，服务重新计划 Windows 更新的最大次数。          |
| WURescheduleTimeInMinutes | Int <br>（默认值：30） | 在持续失败的情况下，服务重新计划 Windows 更新的间隔。 |
| WUFrequency           | 逗号分隔的字符串（默认值："Weekly, Wednesday, 7:00:00"）     | 安装 Windows 更新的频率。 其格式和可能的值包括： <br>-   Monthly, DD, HH:MM:SS，例如：Monthly, 5,12:22:32。<br>字段 DD（天）允许的值为范围 1-28 中的数字和“last”。 <br> -   Weekly, DAY, HH:MM:SS，例如：Weekly, Tuesday, 12:22:32。  <br> -   Daily, HH:MM:SS，例如：Daily, 12:22:32。  <br> - None 表示不应执行 Windows 更新。  <br><br> 请注意，时间采用 UTC。|
| AcceptWindowsUpdateEula | 布尔 <br>（默认值：True） | 通过设置此标志，该应用程序将代表计算机所有者接受 Windows 更新的最终用户许可协议。              |

> [!TIP]
> 若要立即进行 Windows 更新，请依据应用程序部署时间设置 `WUFrequency`。 例如，假设拥有一个 5 节点测试群集，并计划在大约 UTC 下午 5:00 部署应用。 如果假定应用程序升级或部署最多需要 30 分钟，请将 WUFrequency 设置为“Daily, 17:30:00”

## <a name="deploy-the-app"></a>部署应用

1. 完成所有先决条件步骤来准备群集。
2. 像部署任何其他 Service Fabric 应用那样部署修补业务流程应用。 可以使用 PowerShell 部署应用。 请按照[使用 PowerShell 部署和删除应用程序](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)中的步骤操作。
3. 若要在部署时配置应用程序，将 `ApplicationParameter` 传递至 `New-ServiceFabricApplication` cmdlet。 为方便起见，我们随应用程序一同提供了脚本 Deploy.ps1。 使用脚本：

    - 使用 `Connect-ServiceFabricCluster` 连接到 Service Fabric 群集。
    - 结合相应的 `ApplicationParameter` 值执行 PowerShell 脚本 Deploy.ps1。

> [!NOTE]
> 保持脚本和应用程序文件夹 PatchOrchestrationApplication 位于同一目录中。

## <a name="upgrade-the-app"></a>升级应用

若要使用 PowerShell 升级现有的修补业务流程应用，请按照[使用 PowerShell 进行 Service Fabric 应用程序升级](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell)中的步骤操作。

## <a name="remove-the-app"></a>删除应用

若要删除应用，请按照[使用 PowerShell 部署和删除应用程序](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)中的步骤操作。

为方便起见，我们随应用程序一同提供了脚本 Undeploy.ps1。 使用脚本：

  - 使用 ```Connect-ServiceFabricCluster``` 连接到 Service Fabric 群集。

  - 执行 PowerShell 脚本 Undeploy.ps1。

> [!NOTE]
> 保持脚本和应用程序文件夹 PatchOrchestrationApplication 位于同一目录中。

## <a name="view-the-windows-update-results"></a>查看 Windows 更新结果

修补业务流程应用公开了 REST API，以向用户显示历史结果。 JSON 结果的示例：
```json
[
  {
    "NodeName": "_stg1vm_1",
    "WindowsUpdateOperationResults": [
      {
        "OperationResult": 0,
        "NodeName": "_stg1vm_1",
        "OperationTime": "2019-05-13T08:44:56.4836889Z",
        "OperationStartTime": "2019-05-13T08:44:33.5285601Z",
        "UpdateDetails": [
          {
            "UpdateId": "7392acaf-6a85-427c-8a8d-058c25beb0d6",
            "Title": "Cumulative Security Update for Internet Explorer 11 for Windows Server 2012 R2 (KB3185319)",
            "Description": "A security issue has been identified in a Microsoft software product that could affect your system. You can help protect your system by installing this update from Microsoft. For a complete listing of the issues that are included in this update, see the associated Microsoft Knowledge Base article. After you install this update, you may have to restart your system.",
            "ResultCode": 0,
            "HResult": 0
          }
        ],
        "OperationType": 1,
        "WindowsUpdateQuery": "IsInstalled=0",
        "WindowsUpdateFrequency": "Daily,10:00:00",
        "RebootRequired": false
      }
    ]
  },
  ...
]
```

JSON 的字段如下所述。

字段 | 值 | 详细信息
-- | -- | --
OperationResult | 0 - Succeeded（成功）<br> 1 - Succeeded With Errors（已成功但存在错误）<br> 2 - Failed（失败）<br> 3 - Aborted（已中止）<br> 4 - Aborted With Timeout（因超时已中止） | 指示整个操作（通常涉及安装一个或多个更新）的结果。
ResultCode | 与 OperationResult 相同 | 此字段指示单个更新的安装操作的结果。
OperationType | 1 - Installation（安装）<br> 0 - Search and Download（搜索并下载）。| Installation 是默认情况下结果中将显示的唯一 OperationType。
WindowsUpdateQuery | 默认值是“IsInstalled=0” |用来搜索更新的 Windows 更新查询。 有关详细信息，请参阅 [WuQuery](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)。
RebootRequired | true - 需要重新启动<br> false - 无需重新启动 | 指示是否需要重新启动才能完成安装更新。
OperationStartTime | DateTime | 指示启动操作（下载/安装）的时间。
OperationTime | DateTime | 指示完成操作（下载/安装）的时间。
HResult | 0 - 成功<br> 其他 - 失败| 指示 Windows 更新失败并出现 updateID“7392acaf-6a85-427c-8a8d-058c25beb0d6”的原因。

如果尚未计划更新，JSON 结果将为空。

请登录到群集以查询 Windows 更新结果。 然后找出协调器服务的主副本地址，并在浏览器中点击此 URL： http://&lt;REPLICA-IP&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1/GetWindowsUpdateResults。

协调器服务的 REST 终结点有一个动态端口。 若要检查确切的 URL，请参考 Service Fabric Explorer。 例如，可在 `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults` 处获取结果。

![REST 终结点的图像](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)


如果在群集上启用了反向代理，则也可以从群集外部访问该 URL。
需要访问的终结点： http://&lt;SERVERURL&gt;:&lt;REVERSEPROXYPORT&gt;/PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults。

若要在群集上启用反向代理，请按照 [Azure Service Fabric 中的反向代理](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy)中的步骤操作。 

> 
> [!WARNING]
> 配置反向代理后，公开 HTTP 终结点的群集中的所有微服务都可从群集外部进行访问。

## <a name="diagnosticshealth-events"></a>诊断/运行状况事件

以下部分介绍如何通过 Service Fabric 群集上的修补业务流程应用程序调试/诊断修补程序更新问题。

> [!NOTE]
> 应安装 POA v1.4.0 才能获得下面所述的许多自助诊断改进。

NodeAgentNTService 将创建[修复任务](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtask?view=azure-dotnet)用于在节点上安装更新。 然后，CoordinatorService 根据任务审批策略准备每个任务。 准备好的任务最终由修复管理器审批，如果群集处于不正常状态，修复管理器不会批准任何任务。 让我们逐步了解如何在节点上进行更新。

1. 在每个节点上运行的 NodeAgentNTService 按计划的时间查找可用的 Windows 更新。 如果有可用的更新，它会继续将更新下载到节点上。
2. 下载更新后，NodeAgentNTService 将为节点创建名为 POS___<唯一 ID> 的相应修复任务。 可以使用 cmdlet [Get-ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps) 或节点详细信息部分所述的 SFX 查看这些修复任务。 创建修复任务后，请立即转到[声明的状态](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtaskstate?view=azure-dotnet)。
3. Coordinator 服务定期查找处于已声明状态的修复任务，并继续根据 TaskApprovalPolicy 将这些任务更新到 Preparing 状态。 如果 TaskApprovalPolicy 配置为 NodeWise，仅当没有任何其他修复任务当前处于 Preparing/Approved/Executing/Restoring 状态时，才会准备对应于节点的修复任务。 同理，如果 TaskApprovalPolicy 配置为 UpgradeWise，可以确保在任意时间，只有属于同一个升级域的节点才具有处于上述状态的任务。 在修复任务转到 Preparing 状态后，相应的 Service Fabric 节点将会[禁用](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps)，其意图为“Restart”。

   POA（v1.4.0 和更高版本）使用 CoordinaterService 上的属性“ClusterPatchingStatus”发布事件，以显示正在修补的节点。 下图显示正在 _poanode_0 上安装更新：

    [![群集修补状态的插图](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png)](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png#lightbox)

4. 禁用该节点后，修复任务将转到 Executing 状态。
   
   >[!NOTE]
   > 处于禁用状态的节点会阻止新的修复任务，这将暂停群集上的修补操作。

5. 修复任务进入 executing 状态后，将开始在该节点上安装修补程序。 从现在开始，安装修补程序后，节点不一定重启，具体取决于安装的修补程序。 然后，修复任务将转到 restoring 状态，这会重新启用节点，并将其标记为 completed。

   在 v1.4.0 及更高版本的应用程序中，可以通过查看 NodeAgentService 上包含属性“WUOperationStatus-[NodeName]”的运行状况事件，来查找更新状态。 下图中的突出显示部分显示了节点“poanode_0”和“poanode_2”上的 Windows 更新状态：

   [![Windows 更新操作状态插图](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png#lightbox)

   [![Windows 更新操作状态插图](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png#lightbox)

   还可以使用 PowerShell 获取详细信息，方法是连接到群集，然后使用 [Get-ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps) 提取修复任务的状态。 以下示例显示“POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15”任务处于 DownloadComplete 状态。 这表示更新下载到“poanode_2”节点，一旦任务转到 Executing 状态，就会尝试安装这些更新。

   ``` powershell
    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k = Get-ServiceFabricRepairTask -TaskId "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15"

    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k.ExecutorData
    {"ExecutorSubState":2,"ExecutorTimeoutInMinutes":90,"RestartRequestedTime":"0001-01-01T00:00:00"}
    ```

   如果需要查找更多信息，请登录到特定的 VM，使用 Windows 事件日志了解有关问题的详细信息。 上述修复任务只能处于这些执行器子状态：

      ExecutorSubState | Detail
    -- | -- 
      None=1 |  表示节点上没有正在进行的操作。 可能的状态转换。
      DownloadCompleted=2 | 表示下载操作已完成，状态为成功、部分失败或失败。
      InstallationApproved=3 | 表示下载操作已提前完成，修复管理器已批准安装。
      InstallationInProgress=4 | 对应于修复任务的执行状态。
      InstallationCompleted=5 | 表示安装已完成，状态为成功、部分成功或失败。
      RestartRequested=6 | 表示修补程序安装已完成，节点上有一个挂起的重启操作。
      RestartNotNeeded=7 |  表示修补程序安装完成后不需要重启。
      RestartCompleted=8 | 表示重启已成功完成。
      OperationCompleted=9 | Windows 更新操作已成功完成。
      OperationAborted=10 | 表示 Windows 更新操作已中止。

6. 在 v1.4.0 及更高版本的应用程序中，当节点上的更新尝试完成后，将在 NodeAgentService 上发布一个包含属性“WUOperationStatus-[NodeName]”的事件，以通知下一次要在何时尝试下载并安装更新以及启动。 参阅下图：

     [![Windows 更新操作状态插图](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png#lightbox)

### <a name="diagnostic-logs"></a>诊断日志

修补业务流程应用日志是作为 Service Fabric 运行时日志的一部分进行收集的。

在想要通过所选的诊断工具/管道捕获日志的情况下使用。 修补业务流程应用程序使用以下固定的提供程序 Id 通过[事件源](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netframework-4.5.1)记录事件

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>运行状况报告

对于以下情况，修补业务流程应用还会针对协调器服务或节点代理服务发布运行状况报告：

#### <a name="the-node-agent-ntservice-is-down"></a>节点代理 NTService 关闭

如果某个节点上的节点代理 NTService 关闭，将会针对节点代理服务生成警告级别的运行状况报告。

#### <a name="the-repair-manager-service-is-not-enabled"></a>未启用修复管理器服务

如果在群集上找不到修复管理器服务，将会针对协调器服务生成警告级别的运行状况报告。

## <a name="frequently-asked-questions"></a>常见问题

问： 为什么在修补业务流程应用运行时，我发现群集处于错误状态？

A. 在安装过程中，修补业务流程应用会禁用或重新启动节点，这可能会暂时导致群集的运行状况变差。

根据应用程序的策略，执行修补操作期间可以有一个节点关闭，或者整个升级域同时关闭。

在 Windows 更新安装结束时，重新启动后节点将会重新启用。

在以下示例中，由于两个节点关闭且违反了 MaxPercentageUnhealthyNodes 策略，群集暂时进入了错误状态。 这是一个暂时性的错误，在修补操作继续后即可恢复。

![不正常群集的图像](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

如果问题持续出现，请参阅“故障排除”部分。

问： 修补业务流程应用处于警告状态

A. 检查针对应用程序发布的运行状况报告是否是根本原因。 通常，警告中会包含问题的详细信息。 如果该问题是暂时性的，则应用程序应该会自动从此状态中恢复。

问： 如果群集运行不正常，而我需要进行紧急的操作系统更新，该怎么办？

A. 群集运行不正常时，修补业务流程应用不会安装更新。 请尝试将群集恢复正常状态，消除修补业务流程应用工作流的阻碍。

问： **对于我的群集，应将 TaskApprovalPolicy 设置为“NodeWise”还是“UpgradeDomainWise”？**

A. “UpgradeDomainWise”通过并行修补属于升级域的所有节点，使整个群集修补速度更快。 这意味着在修补过程中，属于整个升级域的节点将不可用（处于[已禁用](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled)状态）。

相比之下，“NodeWise”策略一次只修补一个节点，这意味着整个群集修补需要更长时间。 但是，在修补过程中最多只有一个节点不可用（处于[已禁用](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled)状态）。

如果你的群集在修补周期内可以容忍在 N-1 个升级域上运行（其中 N 是群集上升级域的总数），那么你可以将策略设置为“UpgradeDomainWise”，否则将其设置为“NodeWise”。

问： **修补一个节点需要多长时间？**

A. 修补节点可能需要花费数分钟（例如：[Windows Defender 定义更新](https://www.microsoft.com/en-us/wdsi/definitions)）到数小时（例如：[Windows 累积更新](https://www.catalog.update.microsoft.com/Search.aspx?q=windows%20server%20cumulative%20update)）。 修补一个节点所需的时间主要取决于 
 - 更新的大小
 - 必须在修补窗口中应用的更新数
 - 安装更新、重新启动节点（如果需要）以及完成重新启动后安装步骤所需的时间。
 - VM/计算机的性能和网络条件。

问： **修补整个群集需要多长时间？**

A. 修补整个群集所需的时间取决于以下因素：

- 修补一个节点所需的时间。
- 协调器服务的策略。 - 默认策略 `NodeWise` 导致一次仅修补一个节点，这将慢于 `UpgradeDomainWise`。 例如：如果修补一个节点需要约 1 小时，想要修补 5 个升级域（每个升级域包含 4 个节点）的 20 个节点（相同类型的节点）群集。
    - 如果策略为 `NodeWise`，则应需要大约 20 个小时来修补整个群集
    - 如果策略为 `UpgradeDomainWise`，则应需要大约 5 个小时
- 群集负载 - 每个修补操作都需要将客户工作负载重新分配到群集中的其他可用节点。 正在进行修补的节点将在此期间处于[禁用](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabling)状态。 如果群集正在运行接近峰值负载，则禁用过程将需要更长时间。 因此，在这种重压条件下，整个修补过程可能会看起来很慢。
- 修补期间的群集运行状况故障 - [群集运行状况](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction)中的任何[降级](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet#System_Fabric_Health_HealthState_Error)都会中断修补过程。 这将增加修补整个群集所需的总时间。

问： **为什么某些更新会出现在通过 REST API 获得的 Windows 更新结果中，而不是在计算机的 Windows 更新历史记录下？**

A. 某些产品更新仅会显示在其各自的更新/修补历史记录中。 例如，Windows Defender 更新不一定会显示在 Windows Server 2016 的 Windows 更新历史记录中。

问： **修补业务流程应用是否可用来修补开发群集（单节点群集）？**

A. 否，修补业务流程应用不能用来修补单节点群集。 此限制是设计使然，因为 [Service Fabric 系统服务](https://docs.microsoft.com/azure/service-fabric/service-fabric-technical-overview#system-services)或者任意客户应用将面临停机时间，因此修复管理器不会批准任何修复工作进行修补。

问： **如何实现 Linux 上的修补群集节点？**

A. 请参阅[Azure 虚拟机规模集自动 OS 映像升级](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade)，适用于 linux 上的协调更新。

问：**为何更新周期需要花费这么长时间？**

A. 可以在结果 JSON 中查询所有节点的更新周期对应的条目，然后，可以尝试使用 OperationStartTime 和 OperationTime(OperationCompletionTime) 来了解在每个节点上安装更新所花费的时间。 如果在某个较长时间段内未进行更新，原因可能是群集处于错误状态，因此，修复管理器未批准任何其他 POA 修复任务。 如果任一节点上的更新安装花费了较长时间，原因可能是该节点长时间未更新，并且有大量的更新等待安装，因此花费了较长时间。 也可能是阻止了节点上的修补，原因是节点处于 disabling 状态，这种情况往往是禁用节点导致仲裁/数据丢失造成的。

问： **POA 修补节点时为何需要禁用该节点？**

A. 修补业务流程应用程序使用“restart”意图禁用节点，这会停止/重新分配节点上运行的所有 Service Fabric 服务。 目的是确保应用程序最终不会混用新的和旧的 DLL，因此，我们不建议在未禁用节点的情况下对其进行修补。

## <a name="disclaimers"></a>免责声明

- 修补业务流程应用代表用户接受 Windows 更新的最终用户许可协议。 可以在应用程序的配置中选择性地关闭该设置。

- 修补业务流程应用会收集遥测来跟踪使用情况和性能。 应用程序遥测遵循 Service Fabric 运行时的遥测设置（默认设置）。

## <a name="troubleshooting"></a>疑难解答

### <a name="a-node-is-not-coming-back-to-up-state"></a>节点无法恢复启动状态

节点可能会卡在“正在禁用”状态，因为：

安全检查处于挂起中。 若要纠正此情况，请确保有足够多的节点处于正常状态。

节点可能会卡在“已禁用”状态，因为：

- 节点已被手动禁用。
- 某个正在进行的 Azure 基础结构作业导致节点被禁用。
- 修补节点的修补业务流程应用暂时禁用了节点。

节点可能会卡在关闭状态，因为：

- 已手动将节点置于关闭状态。
- 节点正在重新启动（可能由修补业务流程应用触发）。
- VM 或计算机故障、网络连接问题导致节点关闭。

### <a name="updates-were-skipped-on-some-nodes"></a>在某些节点上跳过了更新

修补业务流程应用根据重新计划策略尝试安装 Windows 更新。 服务根据应用程序策略尝试恢复节点并跳过更新。

在这种情况下，将针对节点代理服务生成警告级别的运行状况报告。 Windows 更新结果也包含可能的失败原因。

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-installs"></a>安装更新时群集运行状况出错

发生故障的 Windows 更新会使特定节点或升级域上的应用程序或群集的运行状况恶化。 修补业务流程应用会终止任何后续的 Windows 更新操作，直到群集再次正常运行。

管理员必须介入，并判断为何 Windows 更新会导致应用程序或群集运行不正常。

## <a name="release-notes"></a>发行说明

>[!NOTE]
> 从版本1.4.0 开始，可在 GitHub 版本[页](https://github.com/microsoft/Service-Fabric-POA/releases/)找到发行说明和版本。

### <a name="version-110"></a>版本 1.1.0
- 公开发布的版本

### <a name="version-111"></a>版本 1.1.1
- 修复了 NodeAgentService 的 SetupEntryPoint 中的 bug，可阻止安装 NodeAgentNTService。

### <a name="version-120"></a>版本 1.2.0

- 系统重启工作流中的 Bug 修复。
- 由于修复任务准备过程中的运行状况检查，RM 任务创建过程中的 Bug 修复未能按预期方式进行。
- 将窗口服务 POANodeSvc 的启动模式从自动更改为延时自动。

### <a name="version-121"></a>版本 1.2.1

- 群集缩减工作流中的 Bug 修复。 引入了针对不存在节点中 POA 修复任务的垃圾回收逻辑。

### <a name="version-122"></a>版本 1.2.2

- 其他 Bug 修复。
- 二进制文件现已签名。
- 为应用程序添加了 sfpkg 链接。

### <a name="version-130"></a>版本 1.3.0

- 将 InstallWindowsOSOnlyUpdates 设置为 false 现在会安装所有可用的更新。
- 更改了禁用自动更新的逻辑。 这修复了在 Server 2016 及更高版本上不会禁用自动更新的 bug。
- 用于高级用例的 POA 的微服务的参数化放置约束。

### <a name="version-131"></a>版本 1.3.1
- 修复了由于禁用自动更新失败而导致 POA 1.3.0 无法在 Windows Server 2012 R2 或更低版本上运行的回归。 
- 修复了 InstallWindowsOSOnlyUpdates 配置总是被选为 True 的 bug。
- 将 InstallWindowsOSOnlyUpdates 的默认值更改为 False。

### <a name="version-132"></a>版本 1.3.2
- 解决某个问题，此问题会影响节点上的修补生命周期，以防出现名称为当前节点名称子集的节点。 对于此类节点，可能会出现修补缺失或重启操作挂起的情况。 
