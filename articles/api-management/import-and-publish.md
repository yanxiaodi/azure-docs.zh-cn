---
title: 在 Azure API 管理中导入和发布第一个 API | Microsoft Docs
description: 了解如何在 API 管理中导入和发布第一个 API。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: apimpm
ms.openlocfilehash: 6a1ae2966e8d5535a5fd9aeffb5ddc3a788f85ee
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072102"
---
# <a name="import-and-publish-your-first-api"></a>导入和发布第一个 API 

本教程介绍如何导入 https://conferenceapi.azurewebsites.net?format=json 中的“OpenAPI 规范”后端 API。 此后端 API 由 Microsoft 提供并托管在 Azure 上。 

后端 API 导入到 API 管理 (APIM) 之后，APIM API 即成为后端 API 的外观。 在导入后端 API 时，源 API 和 APIM API 均相同。 通过 APIM，无需触摸后端 API 即可根据需要自定义外观。 有关详细信息，请参阅[转换和保护 API](transform-api.md)。 

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 导入第一个 API
> * 在 Azure 门户中测试 API
> * 在开发人员门户中测试 API

![新的 API](./media/api-management-get-started/created-api.png)

## <a name="prerequisites"></a>先决条件

+ 了解 [Azure API 管理术语](api-management-terminology.md)。
+ 完成以下快速入门：[创建一个 Azure API 管理实例](get-started-create-service-instance.md)。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>导入和发布后端 API

本部分演示如何导入和发布 OpenAPI 规范后端 API。
 
1. 在“API 管理”下面选择“API”。  
2. 从该列表中选择“OpenAPI 规范”，然后在弹出窗口中单击“完整”   。

    ![创建 API](./media/api-management-get-started/create-api.png)

    可在创建时设置 API 或稍后转到“设置”选项卡进行设置  。带有红色星号的字段是必填的。

    使用下表中的值来创建第一个 API。

    | 设置                   | 值                                              | 说明                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
    |---------------------------|----------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **OpenAPI 规范** | https://conferenceapi.azurewebsites.net?format=json | 引用实现 API 的服务。 API 管理将请求转发到此地址。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
    | **显示名称**          | 演示会议 API                               | 如果在输入服务 URL 后按 Tab 键，APIM 将根据 json 中的内容填充此字段。 <br/>此名称显示在开发人员门户中。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
    | **名称**                  | *demo-conference-api*                              | 提供 API 的唯一名称。 <br/>如果在输入服务 URL 后按 Tab 键，APIM 将根据 json 中的内容填充此字段。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
    | **说明**           | 提供 API 的可选说明。        | 如果在输入服务 URL 后按 Tab 键，APIM 将根据 json 中的内容填充此字段。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
    | **URL 方案**            | *HTTPS*                                            | 确定可用于访问 API 的协议。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
    | **API URL 后缀**        | 会议                                        | 此后缀附加到 API 管理服务的基础 URL。 API 管理通过其后缀区分 API，因此后缀对给定发布者上的每个 API 必须唯一。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
    | **产品**              | *不受限制*                                        | 产品是一个或多个 API 的关联。 可在一个产品中包含多个 API，并通过开发人员门户将其提供给开发人员。 <br/>通过将 API 关联到某个产品（在本示例中为“无限制”）来发布该 API。  若要将此新 API 添加到产品，请键入产品名称（也可以稍后通过“设置”页执行此操作）。  多次重复此步骤以将 API 添加到多个产品。<br/>开发人员必须先订阅产品才能访问 API。 订阅时，他们会得到一个订阅密钥，此密钥对该产品中的任何 API 都有效。 <br/> 如果你创建了 APIM 实例，那么你已是管理员，因此订阅了每个产品。<br/> 默认情况下，每个 API 管理实例附带两个示例产品：**初学者**和**无限**。 |
    | **标记**                  |                                                    | 用于组织 API 的标记。 标记可用于搜索、分组或筛选。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
    | **对此 API 进行版本控制？**     |                                                    | 有关版本控制的详细信息，请参阅[发布 API 的多个版本](api-management-get-started-publish-versions.md)。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |

    >[!NOTE]
    > 若要发布 API，必须将其与某个产品相关联。 可以从“设置”页执行此操作。 

3. 选择“创建”  。

> [!TIP]
> 如果在导入自己的 API 定义时遇到问题，请[查看已知问题和限制的列表](api-management-api-import-restrictions.md)。

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>在 Azure 门户中测试新的 APIM API

![测试 API 映射](./media/api-management-get-started/01-import-first-api-01.png)

可直接从 Azure 门户调用操作，这样可以方便地查看和测试 API 的操作。

1. 从“API”选项卡选择在上一步骤中创建的 API。 
2. 按“测试”选项卡  。
3. 单击“GetSpeakers”  。 该页显示查询参数的字段（在此示例中为“无”），以及标头。 其中一个标头是“Ocp-Apim-Subscription-Key”，适用于和此 API 关联的产品订阅密钥。 将自动填充该密钥。
4. 按“发送”。 

    后端以“200 正常”和某些数据做出响应  。

## <a name="call-operation"> </a>从开发人员门户调用操作

此外，也可以从**开发人员门户**调用操作来测试 API。

1. 导航到**开发人员门户**。

    ![开发人员门户](./media/api-management-get-started/developer-portal.png)

2. 选择“API”，单击“演示会议 API”，然后单击“GetSpeakers”。   

    该页显示查询参数的字段（在此示例中为“无”），以及标头。 其中一个标头是“Ocp-Apim-Subscription-Key”，适用于和此 API 关联的产品订阅密钥。 如果创建了 APIM 实例，那么你已是管理员，因此会自动填充该密钥。

3. 按“试用”  。
4. 按“发送”。 

    调用操作后，开发人员门户将显示响应。  

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 导入第一个 API
> * 在 Azure 门户中测试 API
> * 在开发人员门户中测试 API

转到下一教程：

> [!div class="nextstepaction"]
> [创建和发布产品](api-management-howto-add-products.md)
