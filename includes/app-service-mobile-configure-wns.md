---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 79459be30a5a2018dc82486a84895b1a954941bc
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173449"
---
1. 在 [Azure 门户](https://portal.azure.com/)中，选择“浏览全部”   > “应用程序服务”  。 然后选择“移动应用”后端。 在“设置”下  ，选择“应用服务推送”  。 然后选择通知中心名称。
2. 转到“Windows (WNS)”  。 输入**安全密钥**（客户端密码）和从 Live 服务站点获取的**包 SID**。 接下来，选择“保存”  。

    ![设置门户中的 WNS 密钥](./media/app-service-mobile-configure-wns/mobile-push-wns-credentials.png)

后端现已配置为使用 WNS 发送推送通知。
