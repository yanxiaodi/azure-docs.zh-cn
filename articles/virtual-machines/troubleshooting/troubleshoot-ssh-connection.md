---
title: 排查 Azure VM 的 SSH 连接问题 | Microsoft Docs
description: 如何排查运行 Linux 的 Azure VM 上发生的“SSH 连接失败”或“SSH 连接被拒绝”等问题。
keywords: ssh 连接被拒绝, ssh 错误, azure ssh, SSH 连接失败
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: dcb82e19-29b2-47bb-99f2-900d4cfb5bbb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 05/30/2017
ms.author: genli
ms.openlocfilehash: 006dbbe1b7472982a894691d019eb88ef2041dac
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71088267"
---
# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>针对通过 SSH 连接到 Azure Linux VM 时发生的失败、错误或被拒绝问题进行故障排除
尝试连接到 Linux 虚拟机 (VM) 时，可能会由于安全外壳 (SSH) 错误、SSH 连接失败或 SSH 被拒绝而发生问题，本文可帮助你查找并更正这些问题。 可以使用 Azure 门户、Azure CLI 或适用于 Linux 的 VM 访问扩展来排查和解决连接问题。

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

如果对本文中的任何内容需要更多帮助，可以联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/support/forums/)上的 Azure 专家。 或者，你也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。 有关使用 Azure 支持的信息，请阅读 [Microsoft Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。

## <a name="quick-troubleshooting-steps"></a>快速故障排除步骤
执行每个故障排除步骤后，请尝试重新连接到 VM。

1. [重置 SSH 配置](#reset-config)。
2. [重置用户的凭据](#reset-credentials)。
3. 验证[网络安全组](../../virtual-network/security-overview.md)规则是否允许 SSH 流量。
   * 确保有一条[网络安全组规则](#security-rules)允许 SSH 流量（默认为 TCP 端口 22）。
   * 在不使用 Azure 负载均衡器的情况下无法使用端口重定向/映射。
4. 查看 [VM 资源运行状况](../../resource-health/resource-health-overview.md)。
   * 确保 VM 报告为正常。
   * 如果[已启用启动诊断](boot-diagnostics.md)，请验证 VM 是否在日志中报告了启动错误。
5. [重启 VM](#restart-vm)。
6. [重新部署 VM](#redeploy-vm)。

继续阅读余下的内容，获取更详细的故障排除步骤和说明。

## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>排查 SSH 连接问题的可用方法
可以使用以下方法之一重置凭据或 SSH 配置：

* [Azure 门户](#use-the-azure-portal) - 如果需要快速重置 SSH 配置或 SSH 密钥，并且没有安装 Azure 工具，则很适合使用此方法。
* [AZURE Vm 串行控制台](https://aka.ms/serialconsolelinux)-无论 SSH 配置如何，vm 串行控制台都可正常工作，并向你的 vm 提供交互式控制台。 事实上，"不能 SSH" 情况是串行控制台旨在帮助解决的具体情况。 详情见下文。
* [Azure CLI](#use-the-azure-cli) - 如果已打开命令行，则可以快速重置 SSH 配置或凭据。 如果要处理经典 VM，则可以使用 [Azure 经典 CLI](#use-the-azure-classic-cli)。
* [Azure VMAccessForLinux 扩展](#use-the-vmaccess-extension) - 创建和重复使用 json 定义文件来重置 SSH 配置或用户凭据。

在执行每个故障排除步骤之后，请尝试再次连接到 VM。 如果仍然无法连接，请尝试下一步。

## <a name="use-the-azure-portal"></a>使用 Azure 门户
在 Azure 门户中，可以快速重置 SSH 配置或用户凭据，无需在本地计算机上安装任何工具。

若要开始，请在 Azure 门户中选择你的 VM。 向下滚动到“支持 + 故障排除”部分并选择“重置密码”，如以下示例中所示：

![在 Azure 门户中重置 SSH 配置或凭据](./media/troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="a-idreset-config-reset-the-ssh-configuration"></a><a id="reset-config" />重置 SSH 配置
若要重置 SSH 配置，请如上面的屏幕截图所示在“模式”部分中选择“`Reset configuration only`”，然后选择“更新”。 完成此操作后，再次尝试访问 VM。

### <a name="a-idreset-credentials-reset-ssh-credentials-for-a-user"></a><a id="reset-credentials" />重置用户的 SSH 凭据
若要重置现有用户的凭据，请在“模式”部分中选择“`Reset SSH public key`”或“`Reset password`”，如上面的屏幕截图中所示。 指定用户名和 SSH 密钥或新密码，然后选择“更新”。

还可以通过此菜单在 VM 上创建具有 sudo 权限的用户。 输入新用户名和关联的密码或 SSH 密钥，然后选择“更新”。

### <a name="a-idsecurity-rules-check-security-rules"></a><a id="security-rules" />检查安全规则

使用 [IP 流验证](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md)来确认网络安全组中的规则是否阻止了传入或传出虚拟机的流量。 还可以查看有效的安全组规则，确保入站“允许”NSG 规则存在并已针对 SSH 端口（默认值 22）进行优化。 有关详细信息，请参阅[使用有效的安全规则排查 VM 流量流问题](../../virtual-network/diagnose-network-traffic-filter-problem.md)。

### <a name="check-routing"></a>检查路由

使用网络观察程序的[下一跃点](../../network-watcher/network-watcher-check-next-hop-portal.md)功能确认路由未阻止将流量路由到虚拟机或从虚拟机路由流量。 还可以查看有效路由，以了解网络接口的所有有效路由。 有关详细信息，请参阅[使用有效路由排查 VM 流量流问题](../../virtual-network/diagnose-network-routing-problem.md)。

## <a name="use-the-azure-vm-serial-console"></a>使用 Azure VM 串行控制台
[AZURE VM 串行控制台](./serial-console-linux.md)提供对 Linux 虚拟机的基于文本的控制台的访问。 可以使用控制台对交互式 shell 中的 SSH 连接进行故障排除。 确保满足使用串行控制台的[先决条件](./serial-console-linux.md#prerequisites)，并尝试以下命令，进一步排查 SSH 连接问题。

### <a name="check-that-ssh-is-running"></a>检查 SSH 是否正在运行
你可以使用以下命令来验证 SSH 是否在 VM 上运行：
```
$ ps -aux | grep ssh
```
如果有任何输出，SSH 就会启动并运行。

### <a name="check-which-port-ssh-is-running-on"></a>检查正在运行 SSH 的端口
你可以使用以下命令来检查运行 SSH 的端口：
```
$ sudo grep Port /etc/ssh/sshd_config
```
你的输出将如下所示：
```
Port 22
```

## <a name="use-the-azure-cli"></a>使用 Azure CLI
安装最新的 [Azure CLI](/cli/azure/install-az-cli2) 并使用 [az login](/cli/azure/reference-index) 登录到 Azure 帐户（如果尚未这样做）。

如果创建并上传了自定义 Linux 磁盘映像，请确保已安装 [Microsoft Azure Linux](../extensions/agent-linux.md) 代理 2.0.5 版或更高版本。 在使用库映像创建的 VM 上，系统已自动安装并配置了此访问扩展。

### <a name="reset-ssh-configuration"></a>重置 SSH 配置
最初可尝试将 SSH 配置重置为默认值，然后重新启动 VM 上的 SSH 服务器。 这不会更改用户帐户名、密码或 SSH 密钥。
以下示例使用 [az vm user reset-ssh](/cli/azure/vm/user)，在 `myResourceGroup` 中名为 `myVM` 的 VM 上重置 SSH 配置。 请如下所示使用自己的值：

```azurecli
az vm user reset-ssh --resource-group myResourceGroup --name myVM
```

### <a name="reset-ssh-credentials-for-a-user"></a>重置用户的 SSH 凭据
以下示例使用 [az vm user update](/cli/azure/vm/user)，在 `myResourceGroup` 中名为 `myVM` 的 VM 上， 将 `myUsername` 的凭据重置为 `myPassword` 中指定的值。 请如下所示使用自己的值：

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
     --username myUsername --password myPassword
```

如果使用 SSH 密钥身份验证，可以重置给定用户的 SSH 密钥。 以下示例在 `myResourceGroup` 中名为 `myVM` 的 VM 上，使用 **az vm access set-linux-user** 更新存储在 `~/.ssh/id_rsa.pub` 中的用户名为 `myUsername` 的 SSH 密钥。 请如下所示使用自己的值：

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
    --username myUsername --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="use-the-vmaccess-extension"></a>使用 VMAccess 扩展
适用于 Linux 的 VM 访问扩展可以读入用于定义待执行操作的 json 文件。这些操作包括重置 SSHD、重置 SSH 密钥或添加用户。 仍要使用 Azure CLI 调用 VMAccess 扩展，但可以根据需要在多个 VM 上重复使用该 json 文件。 使用这种方法可以创建 json 文件存储库，在给定的方案中可调用这些存储库。

### <a name="reset-sshd"></a>重置 SSHD
创建包含以下内容的名为 `settings.json` 的文件：

```json
{
    "reset_ssh":"True"
}
```

使用 Azure CLI，并调用 `VMAccessForLinux` 扩展并指定 json 文件来重置 SSHD 连接。 以下示例使用 [az vm extension set](/cli/azure/vm/extension)，在 `myResourceGroup` 中名为 `myVM` 的 VM 上重置 SSHD。 请如下所示使用自己的值：

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

### <a name="reset-ssh-credentials-for-a-user"></a>重置用户的 SSH 凭据
如果 SSHD 看上去运行正常，可以重置给定用户的凭据。 若要重置用户的密码，请创建名为 `settings.json` 的文件。 以下示例将 `myUsername` 的凭据重置为 `myPassword` 中指定的值。 在 `settings.json` 文件中使用自己的值输入以下行：

```json
{
    "username":"myUsername", "password":"myPassword"
}
```

若要重置用户的 SSH 密钥，请先创建名为 `settings.json` 的文件。 以下示例在 `myResourceGroup` 中名为 `myVM` 的 VM 上，将 `myUsername` 的凭据重置为 `myPassword` 中指定的值。 在 `settings.json` 文件中使用自己的值输入以下行：

```json
{
    "username":"myUsername", "ssh_key":"mySSHKey"
}
```

创建 json 文件之后，使用 Azure CLI 调用 `VMAccessForLinux` 扩展并指定 json 文件来重置 SSH 用户凭据。 以下示例重置 `myResourceGroup` 中名为 `myVM` 的 VM 上的凭据。 请如下所示使用自己的值：

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

## <a name="use-the-azure-classic-cli"></a>使用 Azure 经典 CLI
[安装 Azure 经典 CLI 并连接到 Azure 订阅](../../cli-install-nodejs.md)（如果尚未这样做）。 确保按如下所示使用 Resource Manager 模式：

```azurecli
azure config mode arm
```

如果创建并上传了自定义 Linux 磁盘映像，请确保已安装 [Microsoft Azure Linux](../extensions/agent-linux.md) 代理 2.0.5 版或更高版本。 在使用库映像创建的 VM 上，系统已自动安装并配置了此访问扩展。

### <a name="reset-ssh-configuration"></a>重置 SSH 配置
SSHD 配置本身可能有误或服务遇到错误。 可以重置 SSHD 以确保 SSH 配置本身是有效的。 要执行的第一个故障排除步骤应该是重置 SSHD。

以下示例重置 `myResourceGroup` 资源组中名为 `myVM` 的 VM 上的 SSHD。 请使用自己的 VM 和资源组名称，如下所示：

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --reset-ssh
```

### <a name="reset-ssh-credentials-for-a-user"></a>重置用户的 SSH 凭据
如果 SSHD 看上去运行正常，可以重置给定用户的密码。 以下示例在 `myResourceGroup` 中名为 `myVM` 的 VM 上，将 `myUsername` 的凭据重置为 `myPassword` 中指定的值。 请如下所示使用自己的值：

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
     --user-name myUsername --password myPassword
```

如果使用 SSH 密钥身份验证，可以重置给定用户的 SSH 密钥。 以下示例在 `myResourceGroup` 中名为 `myVM` 的 VM 上，更新 `~/.ssh/id_rsa.pub` 中为用户 `myUsername` 存储的 SSH 密钥。 请如下所示使用自己的值：

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --user-name myUsername --ssh-key-file ~/.ssh/id_rsa.pub
```

## <a name="a-idrestart-vm-restart-a-vm"></a><a id="restart-vm" />重启 VM
如果已重置 SSH 配置和用户凭据，或者在执行此操作期间遇到错误，可以尝试重新启动 VM 来解决基本的计算问题。

### <a name="azure-portal"></a>Azure 门户
若要使用 Azure 门户重启 VM，请选择你的 VM，然后单击“重启”，如以下示例中所示：

![在 Azure 门户中重新启动 VM](./media/troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-cli"></a>Azure CLI
以下示例使用 [az vm restart](/cli/azure/vm) 重新启动名为 `myResourceGroup` 的资源组中名为 `myVM` 的 VM。 请如下所示使用自己的值：

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>Azure 经典 CLI
以下示例重新启动 `myResourceGroup` 资源组中名为 `myVM` 的 VM。 请如下所示使用自己的值：

```azurecli
azure vm restart --resource-group myResourceGroup --name myVM
```

## <a name="a-idredeploy-vm-redeploy-a-vm"></a><a id="redeploy-vm" />重新部署 VM
可以将 VM 重新部署到 Azure 中的另一个节点，这可能可以更正任何潜在的网络问题。 有关重新部署 VM 的信息，请参阅[将虚拟机重新部署到新的 Azure 节点](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

> [!NOTE]
> 完成此操作后，临时磁盘数据将丢失，系统将更新与虚拟机关联的动态 IP 地址。
>
>

### <a name="azure-portal"></a>Azure 门户
要使用 Azure 门户重新部署 VM，请选择 VM，并向下滚动到“支持 + 故障排除”部分。 选择“重新部署”，如以下示例中所示：

![在 Azure 门户中重新部署 VM](./media/troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-cli"></a>Azure CLI
以下示例使用 [az vm redeploy](/cli/azure/vm) 重新部署名为 `myResourceGroup` 的资源组中名为 `myVM` 的 VM。 请如下所示使用自己的值：

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>Azure 经典 CLI
以下示例重新部署 `myResourceGroup` 资源组中名为 `myVM` 的 VM。 请如下所示使用自己的值：

```azurecli
azure vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>使用经典部署模型创建的 VM
若要解决使用经典部署模型创建的 VM 中最常见的 SSH 连接失败问题，请尝试以下步骤。 在执行每个步骤之后，请尝试重新连接到 VM。

* 从 [Azure 门户](https://portal.azure.com)重置远程访问。 在 Azure 门户中，选择你的 VM，然后选择“重置远程...”。
* 重启 VM。 在 [Azure 门户](https://portal.azure.com)中，选择你的 VM，然后选择“重启”。

* 将 VM 重新部署到新的 Azure 节点。 有关如何重新部署 VM 的信息，请参阅[将虚拟机重新部署到新的 Azure 节点](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

    完成此操作后，临时磁盘数据会丢失，并且系统会更新与虚拟机关联的动态 IP 地址。
* 按照[如何为基于 Linux 的虚拟机重置密码或 SSH](../linux/classic/reset-access-classic.md) 中的说明执行以下操作：

  * 重置密码或 SSH 密钥。
  * 创建 *sudo* 用户帐户。
  * 重置 SSH 配置。
* 检查 VM 的资源运行状况，了解是否存在任何平台问题。<br>
     选择 VM 并向下滚动到“设置” > “检查运行状况”。

## <a name="additional-resources"></a>其他资源
* 如果在执行后续步骤之后仍然无法通过 SSH 连接到 VM，请参阅[更详细的故障排除步骤](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)，查看其他可以解决问题的步骤。
* 有关对应用程序访问进行故障排除的详细信息，请参阅[对在 Azure 虚拟机上运行的应用程序的访问进行故障排除](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* 有关对使用经典部署模型创建的虚拟机进行故障排除的详细信息，请参阅[如何为基于 Linux 的虚拟机重置密码或 SSH](../linux/classic/reset-access-classic.md)。
