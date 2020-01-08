---
title: Windows VM 扩展故障排除 | Microsoft Docs
description: 了解如何进行 Azure Windows VM 扩展故障排除
services: virtual-machines-windows
documentationcenter: ''
author: kundanap
manager: gwallace
editor: ''
tags: top-support-issue,azure-resource-manager
ms.assetid: 878ab9b6-c3e6-40be-82d4-d77fecd5030f
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
ms.openlocfilehash: 6108b3c8d4f4c5c93bc2543042294bc8167d4dd9
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70084294"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Azure Windows VM 扩展故障排除
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>查看扩展状态
可以从 Azure PowerShell 执行 Azure 资源管理器模板。 一旦执行该模板，就可以从 Azure 资源浏览器或命令行工具查看扩展状态。

下面是一个示例：

Azure PowerShell：

      Get-AzVM -ResourceGroupName $RGName -Name $vmName -Status

下面是示例输出：

      Extensions:  {
      "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
      "Name": "myCustomScriptExtension",
      "SubStatuses": [
        {
          "Code": "ComponentStatus/StdOut/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
              \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
              test.txt                          \\n\\n",
                      "Time": null
          },
        {
          "Code": "ComponentStatus/StdErr/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "",
          "Time": null
        }
    }
  ]

## <a name="troubleshooting-extension-failures"></a>扩展故障排除
### <a name="rerun-the-extension-on-the-vm"></a>在 VM 上重新运行扩展
如果使用自定义脚本扩展在 VM 上运行脚本，有时可能会遇到错误：VM 已成功创建但脚本却运行失败。 在这些情况下，从此错误中恢复的建议方法是删除该扩展并再次重新运行该模板。
注意:未来此功能将得到增强，不再需要卸载该扩展。

#### <a name="remove-the-extension-from-azure-powershell"></a>从 Azure PowerShell 删除扩展
    Remove-AzVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"

删除该扩展后，可以重新执行模板在 VM 上运行脚本。

