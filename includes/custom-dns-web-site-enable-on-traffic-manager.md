---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/09/2018
ms.author: cephalin
ms.openlocfilehash: ddeb46a2c7bc7f24f55c22f446926529cee7b598
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059593"
---
在传播域名记录后，应可使用浏览器验证自定义域名能否用于访问 Azure 应用服务中的 Web 应用。

> [!NOTE]
> CNAME 通过 DNS 系统向外传播可能需要一段时间。 可使用 <a href="https://www.digwebinterface.com/">https://www.digwebinterface.com/</a> 等服务验证该 CNAME 是否可用。
> 
> 

如果尚未将 Web 应用添加为流量管理器终结点，必须在解析名称前执行此操作，因为自定义域名将路由到流量管理器。 然后，流量管理器将路由到 Web 应用。 根据[添加或删除终结点](../articles/traffic-manager/traffic-manager-endpoints.md)中的信息，在流量管理器配置文件中将 Web 应用添加为终结点。

> [!NOTE]
> 如果在添加终结点时 Web 应用未列出，请验证是否已将其配置为**标准**应用服务计划模式。 必须将 Web 应用设为**标准**模式才可使用流量管理器。
> 
> 

1. 在浏览器中，打开 [Azure 门户](https://portal.azure.com)。
2. 在“Web 应用”选项卡中，单击 Web 应用的名称，选择“设置”，并选择“自定义域”
   
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)
3. 在“自定义域”边栏选项卡上，单击“添加主机名”。
4. 使用 "**主机名**" 文本框输入要与此 web 应用关联的自定义域名。
   
    ![](./media/custom-dns-web-site/dncmntask-cname-8.png)
5. 单击“验证”保存域名配置。
6. 单击“验证”时，Azure 将启动域验证工作流。 这会检查域的所有权和主机名的可用性，并报告成功或错误详情（附带如何解决错误的说明性指南）。    
7. 验证成功后，“添加主机名”按钮变为激活状态，就可以分配主机名了。 导航到浏览器中的自定义域名。 应会看到应用正在使用自定义域名运行。 
   
   完成配置后，自定义域名会在 Web 应用的“域名”部分中列出。

此时，应可在浏览器中输入流量管理器域名，并查看它是否成功转至 Web 应用。

