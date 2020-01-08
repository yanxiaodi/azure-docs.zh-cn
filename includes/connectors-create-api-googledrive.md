---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: fe726986b3f93ab3bb447b8973727a658ac1c706
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173212"
---
### <a name="prerequisites"></a>必备组件
* 一个 [GoogleDrive](https://www.google.com/drive/) 帐户  

可以在逻辑应用中使用 GoogleDrive 帐户之前，必须先授权该逻辑应用可以连接到 GoogleDrive 帐户。好在，可以从 Azure 门户中的逻辑应用内轻松地执行此操作。  

下面介绍授权逻辑应用连接到 GoogleDrive 帐户的步骤：  

1. 要创建与 GoogleDrive 的连接，请在逻辑应用设计器的下拉列表中选择“显示 Microsoft 托管的 API”  ，并在搜索框中输入“GoogleDrive”  。 选择要使用的触发器或操作：  
   ![GoogleDrive 连接创建步骤](./media/connectors-create-api-googledrive/googledrive-1.png)  
2. 如果以前尚未创建任何与 GoogleDrive 的连接，系统会提示提供 GoogleDrive 凭据。 这些凭据用于授权逻辑应用连接到 GoogleDrive 帐户，然后访问该帐户中的数据：  
   ![GoogleDrive 连接创建步骤](./media/connectors-create-api-googledrive/googledrive-2.png)  
3. 提供 GoogleDrive 电子邮件地址：  
   ![GoogleDrive 连接创建步骤](./media/connectors-create-api-googledrive/googledrive-3.png)  
4. 提供 GoogleDrive 密码，对逻辑应用进行授权：  
   ![GoogleDrive 连接创建步骤](./media/connectors-create-api-googledrive/googledrive-4.png)
5. 允许与 GoogleDrive 的连接  
   ![GoogleDrive 连接创建步骤](./media/connectors-create-api-googledrive/googledrive-5.png)  
6. 请注意，连接已创建，现在可以随时在逻辑应用中继续执行其他步骤：  
   ![GoogleDrive 连接创建步骤](./media/connectors-create-api-googledrive/googledrive-6.png)  

