---
title: （已弃用）监视 Azure Kubernetes 群集 - 操作管理
description: 使用 Log Analytics 在 Azure 容器服务中监视 Kubernetes 群集
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: d7370fc14a5ede23744e04ac9d35140f2368e21f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60711758"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-log-analytics"></a>（已弃用）使用 Log Analytics 监视 Azure 容器服务群集

> [!TIP]
> 有关使用 Azure Kubernetes 服务的此文章的更新版本，请参阅[用于容器的 Azure Monitor](../../azure-monitor/insights/container-insights-overview.md)。

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>必备组件
本演练假定用户已[使用 Azure 容器服务创建 Kubernetes 群集](container-service-kubernetes-walkthrough.md)，

并假设已安装 `az` Azure CLI 和 `kubectl` 工具。

可以通过运行以下语句测试是否已安装 `az` 工具：

```console
$ az --version
```

如果尚未安装 `az` 工具，请参阅[此处](https://github.com/azure/azure-cli#installation)的说明。
或者，可以使用已安装 `az` Azure CLI 和 `kubectl` 工具的 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)。

可以通过运行以下语句测试是否已安装 `kubectl` 工具：

```console
$ kubectl version
```

如果尚未安装 `kubectl`，则可运行：
```console
$ az acs kubernetes install-cli
```

若要测试 kubectl 工具中是否已安装 kubernetes 密钥，可运行：
```console
$ kubectl get nodes
```

如果上述命令错误，需要将 kubernetes 群集密钥安装到 kubectl 工具中。 可使用以下命令执行该操作：
```console
RESOURCE_GROUP=my-resource-group
CLUSTER_NAME=my-acs-name
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

## <a name="monitoring-containers-with-log-analytics"></a>使用 Log Analytics 监视容器

Log Analytics 是 Microsoft 的基于云的 IT 管理解决方案，可帮助你管理和保护本地和云基础结构。 容器解决方案是 Log Analytics 中的一种解决方案，有助于查看单个位置中的容器库存、性能和日志。 通过查看集中位置中的日志，可以审核、排查容器问题，并查找主机上干扰性消耗过多的容器。

![](media/container-service-monitoring-oms/image1.png)

有关容器解决方案的详细信息，请参阅[容器解决方案 Log Analytics](../../azure-monitor/insights/containers.md)。

## <a name="installing-log-analytics-on-kubernetes"></a>在 Kubernetes 上安装 Log Analytics

### <a name="obtain-your-workspace-id-and-key"></a>获取工作区 ID 和密钥
为了使 Log Analytics 代理与服务进行通信，需要为其配置工作区 ID 和工作区密钥。 若要获取工作区 ID 和密钥，需要在 <https://mms.microsoft.com> 创建帐户。
请按照步骤创建帐户。 创建帐户后，可以通过单击“Log Analytics”  边栏选项卡，然后单击工作区名称来获取 ID 和密钥。 然后，在“高级设置”  下，依次单击“连接源”  、“Linux 服务器”  ，将发现所需的信息，如下所示。

 ![](media/container-service-monitoring-oms/image5.png)

### <a name="install-the-log-analytics-agent-using-a-daemonset"></a>使用 DaemonSet 安装 Log Analytics 代理
Kubernetes 使用 DaemonSet 在群集中的每个主机上运行一个容器实例。
DaemonSet 还特别适合用于运行监视代理。

以下是 [DaemonSet YAML 文件](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes)。 将其保存到名为 `oms-daemonset.yaml` 的文件，在文件中将 `WSID` 和 `KEY` 的占位符值分别替换为工作区 ID 和密钥。

将工作区 ID 和密钥添加到 DaemonSet 配置中后，可以使用 `kubectl` 命令行工具在群集上安装 Log Analytics 代理：

```console
$ kubectl create -f oms-daemonset.yaml
```

### <a name="installing-the-log-analytics-agent-using-a-kubernetes-secret"></a>使用 Kubernetes 机密安装 Log Analytics 代理
若要保护 Log Analytics 工作区 ID 和密钥，可以使用 Kubernetes 机密作为 DaemonSet YAML 文件的一部分。

- （从[存储库](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes)）复制脚本、机密模板文件和 DaemonSet YAML 文件，并确保它们位于同一目录中。
  - 机密生成脚本 - secret-gen.sh
  - 机密模板 - secret-template.yaml
    - DaemonSet YAML 文件 - omsagent-ds-secrets.yaml
- 运行该脚本。 该脚本需要 Log Analytics 工作区 ID 和主键。 插入该 ID 和主密钥，然后该脚本将创建一个机密 yaml 文件，以便可以运行。
  ```
  #> sudo bash ./secret-gen.sh
  ```

  - 通过运行以下命令创建机密 Pod：```kubectl create -f omsagentsecret.yaml```

  - 若要检查，请运行以下命令：

  ```
  root@ubuntu16-13db:~# kubectl get secrets
  NAME                  TYPE                                  DATA      AGE
  default-token-gvl91   kubernetes.io/service-account-token   3         50d
  omsagent-secret       Opaque                                2         1d
  root@ubuntu16-13db:~# kubectl describe secrets omsagent-secret
  Name:           omsagent-secret
  Namespace:      default
  Labels:         <none>
  Annotations:    <none>

  Type:   Opaque

  Data
  ====
  WSID:   36 bytes
  KEY:    88 bytes
  ```

  - 通过运行 ```kubectl create -f omsagent-ds-secrets.yaml``` 创建 omsagent daemon-set

### <a name="conclusion"></a>结束语
就这么简单！ 几分钟后，应该可以看到数据流向 Log Analytics 仪表板。
