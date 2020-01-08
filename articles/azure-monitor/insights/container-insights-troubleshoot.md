---
title: 如何对用于容器的 Azure Monitor 进行故障排除 | Microsoft Docs
description: 本文介绍如何排查和解决用于容器的 Azure Monitor 存在的问题。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2018
ms.author: magoedte
ms.openlocfilehash: b6c245142eea12bcec5ed642ec9bd91a58e10eb0
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68813765"
---
# <a name="troubleshooting-azure-monitor-for-containers"></a>对用于容器的 Azure Monitor 进行故障排除

使用用于容器的 Azure Monitor 配置 Azure Kubernetes 服务 (AKS) 群集的监视时，可能会遇到阻止数据收集或报告状态的问题。 本文详细介绍了一些常见问题及其排查步骤。

## <a name="authorization-error-during-onboarding-or-update-operation"></a>在执行载入或更新操作期间出现授权错误
为容器启用 Azure Monitor 或更新群集以支持收集度量值时, 可能会收到类似于以下的错误:*客户端 < 用户的标识 > "对象 id" < 用户的 objectId > "无权作用域执行 "Microsoft Authorization/roleAssignments/write" 操作*

在载入或更新过程中，将对群集资源尝试授予“监视指标发布服务器”角色分配。 如果用户要启动为容器启用 Azure Monitor 的过程或用于支持收集指标的更新，则该用户必须可以访问 AKS 群集资源作用域上的 **Microsoft.Authorization/roleAssignments/write** 权限。 只有**所有者**和**用户访问管理员**内置角色的成员才被授权访问此权限。 如果安全策略需要分配粒度级别的权限，我们建议查看[自定义角色](../../role-based-access-control/custom-roles.md)，并将其分配给需要它的用户。 

此外，还可以通过执行以下步骤，在 Azure 门户中手动授予此角色：

1. 登录到 [Azure 门户](https://portal.azure.com)。 
2. 在 Azure 门户中，单击左上角的“所有服务”。 在资源列表中，键入 **Kubernetes**。 开始键入时，会根据输入筛选该列表。 选择“Azure Kubernetes”。
3. 从 Kubernetes 群集列表中选择一个群集。
2. 在左侧菜单中，单击“访问控制 (IAM)”。
3. 选择“+ 添加”以添加角色分配，并选择“监视指标发布服务器”角色，然后在“选择”框下键入 **AKS**，以仅根据订阅中定义的群集服务主体筛选结果。 从列表中选择特定于该群集的角色。
4. 选择“保存”完成角色分配。 

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>用于容器的 Azure Monitor 已启用，但未报告任何信息
如果用于容器的 Azure Monitor 已成功启用和配置，但你无法查看状态信息或日志查询未返回任何结果，你可以按照以下步骤诊断问题： 

1. 通过运行以下命令检查代理状态： 

    `kubectl get ds omsagent --namespace=kube-system`

    输出应如下所示，指明其已正确部署：

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. 使用以下命令检查代理版本 *06072018* 或更高版本的部署状态：

    `kubectl get deployment omsagent-rs -n=kube-system`

    输出应类似于以下示例，指示已正确部署：

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. 使用以下命令，检查 Pod 的状态，验证它是否正在运行：`kubectl get pods --namespace=kube-system`

    输出应类似于以下示例，omsagent 状态为“正在运行”：

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

4. 查看代理日志。 部署容器化代理时，它通过运行 OMI 命令执行快速检查，并显示代理和提供程序的版本。 

5. 若要验证代理是否已成功部署，请运行以下命令：`kubectl logs omsagent-484hw --namespace=kube-system`

    状态应类似于以下示例：

    ```
    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.2
    omsagent 1.6.0.23
    docker-cimprov 1.0.0.31
    ```

## <a name="error-messages"></a>错误消息

下表汇总了使用适用于容器的 Azure Monitor 时可能会遇到的已知错误。

| 错误消息  | Action |  
| ---- | --- |  
| 错误消息 `No data for selected filters`  | 为新创建的群集建立监视数据流可能需要花费一些时间。 群集的数据至少需要 10 到 15 分钟才能显示。 |   
| 错误消息 `Error retrieving data` | 为 Azure Kubenetes 服务群集设置运行状况和性能监视时，会在群集与 Azure Log Analytics 工作区之间建立连接。 Log Analytics 工作区用于存储你的群集的所有监视数据。 删除 Log Analytics 工作区时, 可能会发生此错误。 检查工作区是否已删除, 如果已删除, 你将需要使用容器 Azure Monitor 重新启用对群集的监视, 并指定现有工作区或创建新的工作区。 若要重新启用，将需要对该群集[禁用](container-insights-optout.md)监视，然后再次[启用](container-insights-enable-new-cluster.md)用于容器的 Azure Monitor。 |  
| 通过 az aks cli 添加适用于容器的 Azure Monitor 后出现 `Error retrieving data` | 当使用 `az aks cli` 启用监视时，可能无法正确部署用于容器的 Azure Monitor。 请检查是否部署了该解决方案。 若要执行此操作，请转到你的 Log Analytics 工作区，并从左侧的面板中选择“解决方案”来查看该解决方案是否可用。 若要解决此问题，需要按照[如何部署适用于容器的 Azure Monitor](container-insights-onboard.md) 中的说明重新部署该解决方案。 |  

为了帮助诊断问题，我们在[此处](https://github.com/Microsoft/OMS-docker/tree/ci_feature_prod/Troubleshoot#troubleshooting-script)提供了一个可用的故障排除脚本。  

## <a name="next-steps"></a>后续步骤

启用监视来捕获 AKS 群集节点和 Pod 的运行状况指标后，可在 Azure 门户中找到这些运行状况指标。 要了解如何将 Azure Monitor 用于容器，请参阅[查看 Azure Kubernetes 服务运行状况](container-insights-analyze.md)。