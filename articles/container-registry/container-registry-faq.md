---
title: Azure 容器注册表-常见问题
description: 与 Azure 容器注册表服务相关的常见问题的解答
services: container-registry
author: sajayantony
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/02/2019
ms.author: sajaya
ms.openlocfilehash: 293f2a704fecb04bc6b65e49743ea80905f2394f
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142672"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>有关 Azure 容器注册表的常见问题

本文介绍有关 Azure 容器注册表的常见问题和已知问题。

## <a name="resource-management"></a>资源管理

- [能否使用资源管理器模板创建 Azure 容器注册表？](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [对于 ACR 中的图像, 是否存在安全漏洞扫描？](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [如何实现将 Kubernetes 配置为 Azure 容器注册表？](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [如何实现获取容器注册表的管理员凭据？](#how-do-i-get-admin-credentials-for-a-container-registry)
- [如何实现获取资源管理器模板中的管理员凭据？](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [即使使用 Azure CLI 或 Azure PowerShell 删除复制, 复制的删除操作也会失败并已禁用。](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)
- [防火墙规则已成功更新, 但不生效](#firewall-rules-are-updated-successfully-but-they-do-not-take-effect)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>能否使用资源管理器模板创建 Azure 容器注册表？

是的。 下面是[一个](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry)可用于创建注册表的模板。

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>对于 ACR 中的图像, 是否存在安全漏洞扫描？

是的。 请参阅[Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/)和[浅绿](https://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry)中的文档。

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>如何实现将 Kubernetes 配置为 Azure 容器注册表？

请参阅[Kubernetes](https://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr)的文档和[Azure Kubernetes 服务](container-registry-auth-aks.md)的步骤。

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>如何实现获取容器注册表的管理员凭据？

> [!IMPORTANT]
> 管理员用户帐户旨在使单个用户访问注册表, 主要用于测试目的。 建议不要与多个用户共享管理员帐户凭据。 建议用户和服务主体在无外设方案中使用单个标识。 请参阅[身份验证概述](container-registry-authentication.md)。

在获取管理员凭据之前, 请确保已启用注册表的管理员用户。

使用 Azure CLI 获取凭据:

```azurecli
az acr credential show -n myRegistry
```

使用 Azure Powershell:

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>如何实现获取资源管理器模板中的管理员凭据？

> [!IMPORTANT]
> 管理员用户帐户旨在使单个用户访问注册表, 主要用于测试目的。 建议不要与多个用户共享管理员帐户凭据。 建议用户和服务主体在无外设方案中使用单个标识。 请参阅[身份验证概述](container-registry-authentication.md)。

在获取管理员凭据之前, 请确保已启用注册表的管理员用户。

获取第一个密码:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[0].value]"
}
```

获取第二个密码:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[1].value]"
}
```

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>即使使用 Azure CLI 或 Azure PowerShell 删除复制, 复制的删除操作也会失败并已禁用。

如果用户具有对注册表的权限, 但没有对订阅的读取者级别权限, 则会出现此错误。 若要解决此问题, 请向用户分配对订阅的读取者权限:


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

### <a name="firewall-rules-are-updated-successfully-but-they-do-not-take-effect"></a>防火墙规则已成功更新, 但不生效

传播防火墙规则更改需要一些时间。 更改防火墙设置后, 请等待几分钟, 然后再验证此更改。


## <a name="registry-operations"></a>注册表操作

- [如何实现访问 Docker 注册表 HTTP API V2？](#how-do-i-access-docker-registry-http-api-v2)
- [如何实现删除存储库中的任何标记未引用的所有清单？](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [为什么删除映像后, 注册表配额使用不会减少？](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [如何实现验证存储配额更改？](#how-do-i-validate-storage-quota-changes)
- [在容器中运行 CLI 时, 如何实现使用注册表进行身份验证吗？](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [Azure 容器注册表是否提供仅 TLS 1.2 版的配置以及如何启用 TLS 1.2 版？](#does-azure-container-registry-offer-tls-v12-only-configuration-and-how-to-enable-tls-v12)
- [Azure 容器注册表是否支持内容信任？](#does-azure-container-registry-support-content-trust)
- [如何实现在没有管理注册表资源的权限的情况下授予请求或推送映像的访问权限吗？](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [如何实现为注册表启用自动映像隔离](#how-do-i-enable-automatic-image-quarantine-for-a-registry)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>如何实现访问 Docker 注册表 HTTP API V2？

ACR 支持 Docker 注册表 HTTP API V2。 可以访问`https://<your registry login server>/v2/`api。 示例： `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>如何实现删除存储库中的任何标记未引用的所有清单？

如果你在 bash 上:

```bash
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

对于 Powershell:

```powershell
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

注意:可以在 delete `-y`命令中添加, 以跳过确认。

有关详细信息, 请参阅[在 Azure 容器注册表中删除容器映像](container-registry-delete.md)。

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>为什么删除映像后, 注册表配额使用不会减少？

如果其他容器映像仍在引用基础层, 则会发生这种情况。 如果删除没有引用的映像, 则会在几分钟内更新注册表使用情况。

### <a name="how-do-i-validate-storage-quota-changes"></a>如何实现验证存储配额更改？

使用以下 docker 文件创建包含1GB 层的映像。 这可确保映像的层不会被注册表中的任何其他图像共享。

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

使用 docker CLI 生成映像并将其推送到注册表。

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

你应该能够看到存储使用量在 Azure 门户中已增加, 或者你可以使用 CLI 查询使用情况。

```bash
az acr show-usage -n myregistry
```

使用 Azure CLI 或门户删除映像, 并在几分钟内检查更新的使用情况。

```bash
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>在容器中运行 CLI 时, 如何实现使用注册表进行身份验证吗？

需要通过装载 Docker 套接字来运行 Azure CLI 容器:

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

在容器中, 安装`docker`:

```bash
apk --update add docker
```

然后, 在注册表中进行身份验证:

```azurecli
az acr login -n MyRegistry
```

### <a name="does-azure-container-registry-offer-tls-v12-only-configuration-and-how-to-enable-tls-v12"></a>Azure 容器注册表是否提供仅 TLS 1.2 版的配置以及如何启用 TLS 1.2 版？

是的。 使用任何最近的 docker 客户端 (版本18.03.0 及更高版本) 启用 TLS。 

### <a name="does-azure-container-registry-support-content-trust"></a>Azure 容器注册表是否支持内容信任？

是的, 你可以使用 Azure 容器注册表中的受信任映像, 因为[Docker 公证人](https://docs.docker.com/notary/getting_started/)已集成并可启用。 有关详细信息, 请参阅[Azure 容器注册表中的内容信任](container-registry-content-trust.md)。


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>指纹位于何处？

在`~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata`:

* 所有角色 (委托角色除外) 的公钥和证书都存储在中`root.json`。
* 委托角色的公钥和证书存储在其父角色的 JSON 文件中 ( `targets.json`例如`targets/releases` , 角色)。

建议在 Docker 和公证人客户端完成总体 TUF 验证之后验证这些公钥和证书。

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>如何实现在没有管理注册表资源的权限的情况下授予请求或推送映像的访问权限吗？

ACR 支持提供不同级别的权限的[自定义角色](container-registry-roles.md)。 具体而言`AcrPull` , `AcrPush`和角色允许用户在 Azure 中没有管理注册表资源的权限的情况下请求和/或推送映像。

* Azure 门户：注册表 > 访问控制 (IAM)-> 添加 (为角色选择`AcrPull`或`AcrPush` )。
* Azure CLI：通过运行以下命令查找注册表的资源 ID:

  ```azurecli
  az acr show -n myRegistry
  ```
  
  然后, 你可以将`AcrPull`或`AcrPush`角色分配给用户 (以下示例使用`AcrPull`):

  ```azurecli
    az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
    ```

  或者, 将角色分配给由其应用程序 ID 标识的服务主体:

  ```
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

然后, 工作负责人可以在注册表中对映像进行身份验证和访问。

* 若要对注册表进行身份验证:
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* 列出存储库:

  ```azurecli
  az acr repository list -n myRegistry
  ```

 提取映像:
    
  ```azurecli
  docker pull myregistry.azurecr.io/hello-world
  ```

如果只`AcrPull`使用或`AcrPush`角色, 则工作负责人无权管理 Azure 中的注册表资源。 例如, `az acr list`或`az acr show -n myRegistry`不会显示注册表。

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>如何实现为注册表启用自动映像隔离？

图像隔离目前是 ACR 的预览功能。 您可以启用注册表的隔离模式, 以便普通用户看不到成功通过安全扫描的映像。 有关详细信息, 请参阅[ACR GitHub](https://github.com/Azure/acr/tree/master/docs/preview/quarantine)存储库。

## <a name="diagnostics-and-health-checks"></a>诊断和运行状况检查

- [检查运行状况`az acr check-health`](#check-health-with-az-acr-check-health)
- [docker pull 失败, 出现错误: net/http: 请求在等待连接时取消 (客户端在等待标头时超时)](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [docker 推送成功但 docker pull 失败, 出现错误: 未授权: 需要进行身份验证](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [启用并获取 docker 后台程序的调试日志](#enable-and-get-the-debug-logs-of-the-docker-daemon) 
- [新用户权限在更新后可能不会立即生效](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [直接 REST API 调用的身份验证信息的格式不正确](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [为什么 Azure 门户不列出我的所有存储库或标记？](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [如何实现在 Windows 上收集 http 跟踪？](#how-do-i-collect-http-traces-on-windows)

### <a name="check-health-with-az-acr-check-health"></a>检查运行状况`az acr check-health`

若要解决常见环境和注册表问题, 请参阅[检查 Azure 容器注册表的运行状况](container-registry-check-health.md)。

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>docker pull 失败, 出现错误: net/http: 请求在等待连接时取消 (客户端在等待标头时超时)

 - 如果此错误是暂时性问题, 则重试将成功。
 - 如果`docker pull`连续失败, 则可能是 Docker 守护程序出现问题。 通常可以通过重新启动 Docker 守护程序来缓解此问题。 
 - 如果在重新启动 Docker 守护程序后仍然看到此问题, 则问题可能是计算机的某些网络连接问题。 若要检查计算机上的常规网络是否正常, 请运行以下命令来测试终结点连接。 包含此`az acr`连接性检查命令的最低版本为2.2.9。 如果使用的是较旧版本, 请升级 Azure CLI。
 
   ```azurecli
    az acr check-health -n myRegistry
    ```
 - 应始终对所有 Docker 客户端操作使用重试机制。

### <a name="docker-pull-is-slow"></a>Docker 拉取速度缓慢
使用[此](http://www.azurespeed.com/Azure/Download)工具可测试计算机网络下载速度。 如果计算机网络速度较慢, 请考虑在注册表所在的同一区域中使用 Azure VM。 这通常提供更快的网络速度。

### <a name="docker-push-is-slow"></a>Docker 推送速度缓慢
使用[此](http://www.azurespeed.com/Azure/Upload)工具可测试计算机网络上传速度。 如果计算机网络速度较慢, 请考虑在注册表所在的同一区域中使用 Azure VM。 这通常提供更快的网络速度。

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>Docker 推送成功但 docker pull 失败, 出现错误: 未授权: 需要进行身份验证

在默认情况下启用的 Docker 守护`--signature-verification`程序的 Red Hat 版本中会出现此错误。 可以通过运行以下命令, 检查 Red Hat Enterprise Linux (RHEL) 或 Fedora 的 Docker 守护程序选项:

```bash
grep OPTIONS /etc/sysconfig/docker
```

例如, Fedora 28 服务器具有以下 docker 守护程序选项:

```
OPTIONS='--selinux-enabled --log-driver=journald --live-restore'
```

如果缺少, `docker pull`将失败并出现类似于以下的错误: `--signature-verification=false`

```bash
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

解决该错误：
1. 将选项`--signature-verification=false`添加到 Docker 守护程序配置文件`/etc/sysconfig/docker`。 例如：

  ```
  OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'
  ```
2. 通过运行以下命令重启 Docker 后台程序服务:

  ```bash
  sudo systemctl restart docker.service
  ```

可以通过`--signature-verification`运行`man dockerd`来找到的详细信息。

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>启用并获取 Docker 后台程序的调试日志  

从选项开始`dockerd` `debug` 。 首先, 创建 Docker 守护程序配置文件 (`/etc/docker/daemon.json`) (如果不存在), 并`debug`添加选项:

```json
{   
    "debug": true   
}
```

然后重新启动该守护程序。 例如, 对于 Ubuntu 14.04:

```bash
sudo service docker restart
```

可在[Docker 文档](https://docs.docker.com/engine/admin/#enable-debugging)中找到详细信息。 

 * 日志可能会在不同的位置生成, 具体取决于您的系统。 例如, 对于 Ubuntu 14.04, 它是`/var/log/upstart/docker.log`。   
有关详细信息, 请参阅[Docker 文档](https://docs.docker.com/engine/admin/#read-the-logs)。    

 * 对于用于 Windows 的 Docker, 将在% LOCALAPPDATA%/docker/. 下生成日志 但它可能不包含所有调试信息。   

   若要访问完整的守护程序日志, 可能需要执行一些额外的步骤:

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    现在, 你可以访问运行`dockerd`中的 VM 的所有文件了。 日志为`/var/log/docker.log`。

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>新用户权限在更新后可能不会立即生效

向服务主体授予新权限时, 更改可能不会立即生效。 有两个可能的原因:

* Azure Active Directory 角色分配延迟。 通常, 这是快速的, 但由于传播延迟, 这可能需要几分钟的时间。
* ACR 标记服务器上的权限延迟。 这可能需要长达10分钟的时间。 若要缓解此情况`docker logout` , 可以在1分钟后再次向同一用户进行身份验证:

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

目前, ACR 不支持用户删除主复制。 解决方法是在模板中包括主复制创建, 但通过添加`"condition": false`来跳过其创建, 如下所示:

```json
{
    "name": "[concat(parameters('acrName'), '/', parameters('location'))]",
    "condition": false,
    "type": "Microsoft.ContainerRegistry/registries/replications",
    "apiVersion": "2017-10-01",
    "location": "[parameters('location')]",
    "properties": {},
    "dependsOn": [
        "[concat('Microsoft.ContainerRegistry/registries/', parameters('acrName'))]"
     ]
},
```

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>直接 REST API 调用的身份验证信息的格式不正确

你可能会遇到`InvalidAuthenticationInfo`错误, 尤其是将`curl`该`--location`工具与选项`-L`一起使用 (以跟踪重定向)。
例如, 使用`curl` with `-L`选项和基本身份验证获取 blob:

```bash
curl -L -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest
```

可能会导致以下响应:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Error><Code>InvalidAuthenticationInfo</Code><Message>Authentication information is not given in the correct format. Check the value of Authorization header.
RequestId:00000000-0000-0000-0000-000000000000
Time:2019-01-01T00:00:00.0000000Z</Message></Error>
```

根本原因是某些`curl`实现遵循从原始请求中的标头进行重定向。

若要解决此问题, 需要在不带标头的情况下手动跟踪重定向。 打印包含`-D -`选项的`curl`响应标头`Location` , 然后提取: 标头:

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>为什么 Azure 门户不列出我的所有存储库或标记？ 

如果使用的是 Microsoft Edge/IE 浏览器, 最多可以查看100个存储库或标记。 如果你的注册表中的存储库或标记超过100个, 我们建议你使用 Firefox 或 Chrome 浏览器来列出它们。

### <a name="how-do-i-collect-http-traces-on-windows"></a>如何实现在 Windows 上收集 http 跟踪？

#### <a name="prerequisites"></a>先决条件

- 在 fiddler 中启用解密 https:<https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>
- 通过 Docker ui 使 Docker 能够使用代理:<https://docs.docker.com/docker-for-windows/#proxies>
- 完成后, 请务必还原。  Docker 不会使用启用的, 并且 fiddler 不会运行。

#### <a name="windows-containers"></a>Windows 容器

将 Docker 代理配置为 127.0.0.1: 8888

#### <a name="linux-containers"></a>Linux 容器

查找 Docker vm 虚拟交换机的 ip:

```powershell
(Get-NetIPAddress -InterfaceAlias "*Docker*" -AddressFamily IPv4).IPAddress
```

将 Docker 代理配置为前一个命令的输出和端口 8888 (例如 10.0.75.1: 8888)

## <a name="tasks"></a>任务

- [如何实现批取消运行？](#how-do-i-batch-cancel-runs)
- [如何实现在 az acr build 命令中包含 git 文件夹？](#how-do-i-include-the-git-folder-in-az-acr-build-command)

### <a name="how-do-i-batch-cancel-runs"></a>如何实现批取消运行？

以下命令将取消指定注册表中所有正在运行的任务。

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>如何实现在 az acr build 命令中包含 git 文件夹？

如果将本地源文件夹传递到`az acr build`命令, 则默认情况下从已上载的包中排除该`.git`文件夹。 您可以使用以下`.dockerignore`设置创建文件。 它通知命令还原已上载包中下`.git`的所有文件。 

```
!.git/**
```

此设置还适用`az acr run`于命令。

## <a name="cicd-integration"></a>CI/CD 集成

- [CircleCI](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [GitHub 操作](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)

## <a name="next-steps"></a>后续步骤

* [了解](container-registry-intro.md)有关 Azure 容器注册表的详细信息。
