---
title: 排查常见的 Azure Kubernetes 服务问题
description: 了解如何排查和解决在使用 Azure Kubernetes 服务 (AKS) 时遇到的常见问题
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: 6ff273236f9f8465de9ec0cda89ed3ff8996ecec
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70932667"
---
# <a name="aks-troubleshooting"></a>AKS 疑难解答

当创建或管理 Azure Kubernetes 服务 (AKS) 群集时，可能偶尔会遇到问题。 本文详细介绍了一些常见问题及其排查步骤。

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>通常在何处查看 Kubernetes 问题调试的相关信息？

请尝试 [Kubernetes 群集故障排除的官方指南](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/)。
还可尝试由 Microsoft 工程师发布的[故障排除指南](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md)，用于对 Pod、节点、群集和其他功能进行故障排除。

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>在创建或升级期间遇到“超出配额”的错误。 我该怎么办？ 

需要[请求内核](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)。

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>对 AKS 而言，每个节点设置的最大 Pod 是多少？

如果在 Azure 门户中部署 AKS 群集，则每个节点的最大 Pod 均默认设置为 30。
如果在 Azure CLI 中部署 AKS 群集，则每个节点的最大 Pod 均默认设置为 110。 （确保使用最新版本的 Azure CLI）。 可以使用 `az aks create` 命令中的 `–-max-pods` 标记来更改此默认设置。

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>在使用高级网络部署 AKS 群集时收到 insufficientSubnetSize 错误。 我该怎么办？

如果使用 Azure CNI（高级网络），AKS 会根据配置的每个节点的“最大 Pod 数”预分配 IP 地址。 AKS 群集中的节点数可介于 1 和 110 之间的任意位置。 根据配置的每个节点的最大 Pod 数，子网大小应大于“节点数和每个节点的最大 Pod 数的乘积”。 以下基本等式对此进行了概要介绍：

子网大小 > 群集中的节点数（考虑到未来的缩放要求）* 每个节点的最大 Pod 数。

有关详细信息，请参阅[规划群集的 IP 地址](configure-azure-cni.md#plan-ip-addressing-for-your-cluster)。

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>我的 Pod 停滞在 CrashLoopBackOff 模式。 我该怎么办？

可能有多种原因导致 Pod 停滞在该模式。 可能通过以下方式查看：

* 使用 `kubectl describe pod <pod-name>` 查看 Pod 本身。
* 使用 `kubectl log <pod-name>` 查看日志。

有关如何对 Pod 的问题进行故障排除的详细信息，请参阅[调试应用程序](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods)。

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>尝试在现有群集上启用 RBAC。 该如何操作？

遗憾的是，目前不支持在现有群集上启用基于角色的访问控制 (RBAC)。 必须显式创建新群集。 如果使用 CLI，则默认启用 RBAC。 如果使用 AKS 门户，则在创建工作流时可使用切换按钮来启用 RBAC。

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>使用带有默认值的 Azure CLI 或 Azure 门户创建了一个启用了 RBAC 的集群，现在 Kubernetes 仪表板上出现了许多警告。 仪表板以前在没有任何警告的情况下工作。 我该怎么办？

仪表板上收到警告的原因是群集现在启用了 RBAC，但已默认禁用了对它的访问。 一般来说，此方法比较棒，因为仪表板默认公开给群集的所有用户可能会导致安全威胁。 如果仍想要启用仪表板，请遵循此[博客文章](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/)中的步骤进行操作。

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>我无法连接到仪表板。 我该怎么办？

要访问群集外的服务，最简单的方法是运行 `kubectl proxy`，它将代理对 Kubernetes API 服务器使用 localhost 端口 8001 的请求。 在此，API 服务器可以代理服务：`http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`。

如果看不到 Kubernetes 仪表板，请检查 `kube-proxy` Pod 是否在 `kube-system` 命名空间中运行。 如果未处于运行状态，请删除 Pod，它会重启。

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>无法使用 Kubectl 日志获取日志或无法连接到 API 服务器。 我收到“来自服务器的错误：拨号后端时出错: 拨打 tcp...”。 我该怎么办？

请确保默认网络安全组未被修改并且端口 22 和 9000 已打开以连接到 API 服务器。 使用 `kubectl get pods --namespace kube-system` 命令检查 `tunnelfront` Pod是否在 *kube-system* 命名空间中运行。 如果没有，请强制删除 Pod，它会重启。

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>我在尝试进行升级或缩放，并收到“消息：不允许更改属性‘imageReference’”错误。 如何修复此问题？

收到此错误的原因可能是，你修改了 AKS 群集内代理节点中的标记。 如果修改和删除 MC_* 资源组中资源的标记和其他属性，可能会导致意外结果。 修改 AKS 群集中 MC_ * 组下的资源会中断服务级别目标 (SLO)。

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>有错误指出，我的群集处于故障状态，在解决此解决之前无法进行升级或缩放

*此故障排除帮助是从 https://aka.ms/aks-cluster-failed*

如果群集出于多种原因进入故障状态，则会发生此错误。 请遵循以下步骤解决群集故障状态，然后重试先前失败的操作：

1. 除非群集摆脱 `failed` 状态，否则 `upgrade` 和 `scale` 操作不会成功。 常见的根本问题和解决方法包括：
    * 使用**不足的计算 (CRP) 配额**进行缩放。 若要解决此问题，请先将群集缩放回到配额内的稳定目标状态。 遵循[这些步骤请求提高计算配额](../azure-supportability/resource-manager-core-quotas-request.md)，然后尝试扩展到超出初始配额限制。
    * 使用高级网络和**不足的子网（网络）资源**缩放群集。 若要解决此问题，请先将群集缩放回到配额内的稳定目标状态。 遵循[这些步骤请求提高资源配额](../azure-resource-manager/resource-manager-quota-errors.md#solution)，然后尝试扩展到超出初始配额限制。
2. 解决升级失败的根本原因后，群集应会进入成功状态。 确认处于成功状态后，重试原始操作。

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>尝试升级或缩放群集时，有错误指出我的群集当前正在升级或升级失败

*此故障排除帮助是从 https://aka.ms/aks-pending-upgrade*

具有单个节点池的群集或具有[多个节点池](use-multiple-node-pools.md)的群集的升级和缩放操作是互斥的。 不能让群集或节点池同时升级和缩放， 而只能先在目标资源上完成一个操作类型，然后再在同一资源上执行下一个请求。 因此，如果当前正在执行升级或缩放操作，或者曾经尝试过这些操作，但随后失败，则其他操作会受到限制。 

若要诊断此问题，请运行 `az aks show -g myResourceGroup -n myAKSCluster -o table` 检索群集上的详细状态。 根据结果：

* 如果群集正在升级，请等到该操作终止。 如果升级成功，请再次重试先前失败的操作。
* 如果群集升级失败，请按前面部分所述的步骤操作。

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>是否可以将我的群集移动到其他订阅，或者说，是否可以将包含我的群集的订阅移动到新租户？

如果你已将 AKS 群集移动到其他订阅，或者将拥有订阅的群集移动到新租户，则群集将会由于失去角色分配和服务主体权限而丢失功能。 由于此约束，**AKS 不支持在订阅或租户之间移动群集**。

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>尝试使用需要虚拟机规模集的功能时收到错误

*此故障排除帮助是从 aka.ms/aks-vmss-enablement*

可能会收到指示 AKS 群集不在虚拟机规模集上的错误，例如以下示例：

**AgentPool ' AgentPool ' 已启用自动缩放，但它不在虚拟机规模集上**

若要使用群集自动缩放程序或多节点池等功能，必须创建使用虚拟机规模集的 AKS 群集。 如果尝试使用依赖于虚拟机规模集的功能，并以常规的非虚拟机规模集 AKS 群集为目标，则会返回错误。 虚拟机规模集支持目前在 AKS 中以预览版提供。

按照相应文档中*开始步骤之前*的步骤，正确注册虚拟机规模集功能预览并创建 AKS 群集：

* [使用群集自动缩放程序](cluster-autoscaler.md)
* [创建和使用多个节点池](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>针对 AKS 资源和参数强制实施了什么命名限制？

*此故障排除帮助来自 aka.ms/aks-naming-rules*

Azure 平台和 AKS 都实施了命名限制。 如果资源名称或参数违反了这些限制之一，则会返回一个错误，要求你提供不同的输入。 将应用以下通用的命名准则：

* AKS *MC_* 资源组名称组合了资源组名称和资源名称。 自动生成的语法 `MC_resourceGroupName_resourceName_AzureRegion` 不能超过 80 个字符。 如果需要，请缩短你的资源组名称或 AKS 群集名称的长度。
* *dnsPrefix* 的开头和结尾必须是字母数字值。 有效字符包括字母数字值和连字符 (-)。 *dnsPrefix* 不能包含特殊字符，例如句点 (.)。

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>尝试创建、更新、缩放、删除或升级群集时收到错误，不允许执行该操作，因为正在执行其他操作。

*此故障排除帮助是从 aka.ms/aks-pending-operation*

当上一个操作仍在进行时，群集操作会受限。 若要检索群集的详细状态，请使用 `az aks show -g myResourceGroup -n myAKSCluster -o table` 命令。 根据需要使用自己的资源组和 AKS 群集名称。

根据群集状态的输出：

* 如果群集的预配状态不是“成功”或“失败”，请等待操作（升级/更新/创建/缩放/删除/迁移）终止。 当上一操作完成后，请重试最新的群集操作。

* 如果群集的升级失败，请按[有错误指出，我的群集处于故障状态，在解决此解决之前无法进行升级或缩放](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed)中概述的步骤操作。

## <a name="im-receiving-errors-that-my-service-principal-was-not-found-when-i-try-to-create-a-new-cluster-without-passing-in-an-existing-one"></a>我在尝试创建新群集时找不到服务主体，但未传入现有群集时，出现错误。

创建 AKS 群集时，需要使用服务主体来代表你创建资源。 AKS 提供在创建群集时创建新的功能，但这需要 Azure Active Directory 在合理的时间内完全传播新的服务主体，以便群集成功创建。 如果此传播时间太长，则群集将无法进行验证，因为它找不到可用的服务主体来执行此操作。 

为此，请使用以下解决方法：
1. 使用已在区域中传播的现有服务主体，并在创建群集时将其传递到 AKS。
2. 如果使用自动化脚本，请在创建服务主体和创建 AKS 群集之间添加时间延迟。
3. 如果使用 Azure 门户，请在创建过程中返回到群集设置，然后在几分钟后重试验证页面。

## <a name="im-receiving-errors-after-restricting-my-egress-traffic"></a>限制出站流量后收到错误

在限制来自 AKS 群集的出口流量时，需要为 AKS[提供必需和可选](limit-egress-traffic.md)的出站端口/网络规则和 FQDN/应用程序规则。 如果你的设置与任何这些规则冲突，你可能无法运行某些`kubectl`命令。 创建 AKS 群集时，还可能会看到错误。

验证你的设置是否不与任何必需或可选的建议出站端口/网络规则和 FQDN/应用程序规则冲突。
