---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/13/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: afd4836229c60ebef1536d4fa1ca4206a492e56d
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172826"
---
创建自签名根证书之后，导出根证书公用密钥 .cer 文件（不导出私钥）。 稍后要将此文件上传到 Azure。 以下步骤可帮助你导出自签名根证书的 .cer 文件：

1. 若要获取证书 .cer 文件，请打开“管理用户证书”  。 找到自签名根证书（通常位于“Certificates - Current User\Personal\Certificates”中），并右键单击。 单击“所有任务”  ，并单击“导出”  。 此操作将打开“证书导出向导”  。 如果在 Current User\Personal\Certificates 下找不到证书，可能会意外地打开“Certificates - Local Computer”而不是“Certificates - Current User”）。 如果想要使用 PowerShell 在当前用户范围内打开证书管理程序，请在控制台窗口中键入“certmgr”  。

   ![导出](./media/vpn-gateway-certificates-export-public-key-include/export.png)
2. 在向导中，单击“下一步”  。

   ![导出证书](./media/vpn-gateway-certificates-export-public-key-include/exportwizard.png)
3. 选择“否，不导出私钥”  ，并单击“下一步”  。

   ![不要导出私钥](./media/vpn-gateway-certificates-export-public-key-include/notprivatekey.png)
4. 在“导出文件格式”  页上，选择“Base-64 编码的 X.509 (.CER)”  ，并单击“下一步”  。

   ![Base-64 编码](./media/vpn-gateway-certificates-export-public-key-include/base64.png)
5. 对于“要导出的文件”  ，“浏览”  到要将证书导出的目标位置。 在“文件名”  中，为证书文件命名。 然后单击“下一步”。 

   ![浏览](./media/vpn-gateway-certificates-export-public-key-include/browse.png)
6. 单击“完成”  导出证书。

   ![完成](./media/vpn-gateway-certificates-export-public-key-include/finish.png)
7. 证书已成功导出。

   ![Success](./media/vpn-gateway-certificates-export-public-key-include/success.png)
8. 导出的证书类似于以下内容：

   ![已导出](./media/vpn-gateway-certificates-export-public-key-include/exported.png)
9. 如果使用记事本打开导出的证书，则会看到类似于此示例的一些内容。 蓝色的部分包含上传到 Azure 的信息。 如果使用记事本打开证书，并且内容不与此类似，则这通常意味着你没有使用 Base-64 编码的 X.509(.CER) 格式将其导出。 此外，如果希望使用其他文本编辑器，请注意，某些编辑器可能会在后台引入意外的格式设置。 将此证书中的文本上传到 Azure 时，这可能会产生问题。

   ![使用记事本打开](./media/vpn-gateway-certificates-export-public-key-include/notepad.png)
