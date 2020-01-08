---
title: 使用 Azure 容器注册表从 Azure Kubernetes 服务进行身份验证
description: 了解如何使用 Azure Active Directory 服务主体从 Azure Kubernetes 服务访问专用容器注册表中的映像。
services: container-service
author: dlepow
manager: gwallace
ms.service: container-service
ms.topic: article
ms.date: 08/27/2019
ms.author: danlep
ms.openlocfilehash: f80956ec401737766f7a85540e90be70b9d621e7
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114706"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>使用 Azure 容器注册表从 Azure Kubernetes 服务进行身份验证

结合使用 Azure 容器注册表 (ACR) 和 Azure Kubernetes 服务 (AKS) 时，需要建立身份验证机制。 本文详细介绍这两种 Azure 服务之间的建议身份验证配置。

只需配置其中一种身份验证方法。 最常见的方法是[使用 AKS 服务主体授予访问权限](#grant-aks-access-to-acr)。 如果有特定需求, 可以选择[使用 Kubernetes 机密授予访问权限](#access-with-kubernetes-secret)。

本文假定已创建 AKS 群集，并且可以使用 `kubectl` 命令行客户端访问群集。 如果要在创建群集时创建群集并配置对容器注册表的访问, 请参阅[教程:](../aks/tutorial-kubernetes-deploy-cluster.md) [使用 azure Kubernetes Service (预览版) 部署 AKS 群集或使用 azure 容器注册表进行身份验证](../aks/cluster-container-registry-integration.md)。

## <a name="grant-aks-access-to-acr"></a>向 ACR 授予 AKS 访问权限

创建 AKS 群集时，Azure 还会创建一个服务主体以支持与其他 Azure 资源的群集可操作性。 可以使用此自动生成的服务主体向 ACR 注册表进行身份验证。 若要执行此操作，需要创建一个 Azure AD [角色分配](../role-based-access-control/overview.md#role-assignments)，它会授予群集的服务主体访问容器注册表的权限。

使用以下脚本授予 AKS 生成的服务主体请求 Azure 容器注册表的访问权限。 在运行脚本前，修改环境的 `AKS_*` 和 `ACR_*` 变量。

```bash
#!/bin/bash

AKS_RESOURCE_GROUP=myAKSResourceGroup
AKS_CLUSTER_NAME=myAKSCluster
ACR_RESOURCE_GROUP=myACRResourceGroup
ACR_NAME=myACRRegistry

# Get the id of the service principal configured for AKS
CLIENT_ID=$(az aks show --resource-group $AKS_RESOURCE_GROUP --name $AKS_CLUSTER_NAME --query "servicePrincipalProfile.clientId" --output tsv)

# Get the ACR registry resource id
ACR_ID=$(az acr show --name $ACR_NAME --resource-group $ACR_RESOURCE_GROUP --query "id" --output tsv)

# Create role assignment
az role assignment create --assignee $CLIENT_ID --role acrpull --scope $ACR_ID
```

## <a name="access-with-kubernetes-secret"></a>使用 Kubernetes 机密访问

在某些情况下，可能无法向自动生成的 AKS 服务主体分配授予其访问 ACR 权限所需的角色。 例如，由于组织的安全模型，你可能没有足够的权限在 Azure Active Directory 租户中为 AKS 生成的服务主体分配角色。 将角色分配给服务主体要求 Azure AD 帐户具有对 Azure AD 租户的写入权限。 如果没有权限，则可以创建新的服务主体，然后使用 Kubernetes 映像请求机密向其授予容器注册表的访问权限。

使用以下脚本创建新的服务主体（你将使用其凭据来获取 Kubernetes 映像请求机密）。 在运行脚本之前，修改环境的 `ACR_NAME` 变量。

```bash
#!/bin/bash

ACR_NAME=myacrinstance
SERVICE_PRINCIPAL_NAME=acr-service-principal

# Populate the ACR login server and resource id.
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --query loginServer --output tsv)
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Create acrpull role assignment with a scope of the ACR resource.
SP_PASSWD=$(az ad sp create-for-rbac --name http://$SERVICE_PRINCIPAL_NAME --role acrpull --scopes $ACR_REGISTRY_ID --query password --output tsv)

# Get the service principal client id.
CLIENT_ID=$(az ad sp show --id http://$SERVICE_PRINCIPAL_NAME --query appId --output tsv)

# Output used when creating Kubernetes secret.
echo "Service principal ID: $CLIENT_ID"
echo "Service principal password: $SP_PASSWD"
```

你现在可以将服务主体的凭据存储在 Kubernetes[映像请求机密][image-pull-secret]中, AKS 群集在运行容器时将引用该密钥。

使用以下 kubectl 命令创建 Kubernetes 机密。 将 `<acr-login-server>` 替换为 Azure 容器注册表的完全限定名称（它的格式为“acrname.azurecr.io”）。 将 `<service-principal-ID>` 和 `<service-principal-password>` 替换为运行之前的脚本所获得的值。 将 `<email-address>` 替换为格式正确的任何电子邮件地址。

```bash
kubectl create secret docker-registry acr-auth --docker-server <acr-login-server> --docker-username <service-principal-ID> --docker-password <service-principal-password> --docker-email <email-address>
```

现在可以通过在 `imagePullSecrets` 参数中指定 Kubernetes 机密的名称（在这种情况下，名称为“acr-auth”），以在 pod 部署中使用此机密：

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: acr-auth-example
spec:
  template:
    metadata:
      labels:
        app: acr-auth-example
    spec:
      containers:
      - name: acr-auth-example
        image: myacrregistry.azurecr.io/acr-auth-example
      imagePullSecrets:
      - name: acr-auth
```

<!-- LINKS - external -->
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[image-pull-secret]: https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets
