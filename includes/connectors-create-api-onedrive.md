---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: 7cfce34cb2d6002dba5ec570bf859ec47e894c65
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173206"
---
#### <a name="prerequisites"></a>必备组件
* Azure 帐户；可以创建[免费帐户](https://azure.microsoft.com/free)
* 一个 [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) 帐户 

可以在逻辑应用中使用 OneDrive 帐户之前，请先授权该逻辑应用可以连接到 OneDrive 帐户。  可以在 Azure 门户中的逻辑应用内轻松地执行此操作。 

按照以下步骤操作，即可授权逻辑应用连接到 OneDrive 帐户：

1. 创建逻辑应用。 在逻辑应用设计器的下拉列表中选择“显示 Microsoft 托管的 API”  ，并在搜索框中输入“onedrive”。 选择其中一个触发器或操作：  
   ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. 如果以前尚未创建任何与 OneDrive 的连接，系统会提示使用 OneDrive 凭据进行登录：  
   ![](./media/connectors-create-api-onedrive/onedrive-2.png)
3. 选择“登录”  ，并输入用户名和密码。 选择“登录”  ：  
   ![](./media/connectors-create-api-onedrive/onedrive-3.png)   
   
    这些凭据将用于授权逻辑应用连接到你的 OneDrive 帐户，并访问该帐户中的数据。 
4. 选择“是”  即可授权逻辑应用使用 OneDrive 帐户：  
   ![](./media/connectors-create-api-onedrive/onedrive-4.png)   
5. 请注意，连接已创建。 现在，继续在逻辑应用中执行其他步骤：  
   ![](./media/connectors-create-api-onedrive/onedrive-5.png)

