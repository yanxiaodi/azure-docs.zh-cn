---
author: zhangmanling
ms.service: azure-cdn
ms.topic: include
ms.date: 11/21/2018
ms.author: mazha
ms.openlocfilehash: f21a768733456a6c00e5a87612f3055dd76d416c
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594115"
---
## <a name="prerequisites"></a>系统必备
在编写 CDN 管理代码之前，需要做一些准备工作，使代码能够与 Azure 资源管理器进行交互。 若要执行此准备工作，你需要：

* 创建一个资源组，用来包含本教程中创建的 CDN 配置文件
* 配置 Azure Active Directory 来为应用程序提供身份验证
* 向该资源组应用权限，使得只有来自 Azure AD 租户的已授权用户可与 CDN 配置文件进行交互

### <a name="creating-the-resource-group"></a>创建资源组
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 单击“创建资源”  。
3. 搜索“资源组”  ，在“资源组”窗格中，单击“创建”  。

    ![创建新资源组](./media/cdn-app-dev-prep/cdn-new-rg-1-include.png)
3. 将资源组命名为 *CdnConsoleTutorial*。  选择订阅，并选择离你最近的位置。  如果需要，可以单击“固定到仪表板”复选框，将资源组固定到门户中的仪表板。   这便于以后查找该资源组。  完成选择后，单击“创建”。 

    ![为资源组命名](./media/cdn-app-dev-prep/cdn-new-rg-2-include.png)
4. 创建资源组之后，如果未将它固定到仪表板，可以依次单击“浏览”和“资源组”找到它。    要打开该资源组，请单击它。  记下**订阅 ID**。 稍后需要用到此信息。

    ![为资源组命名](./media/cdn-app-dev-prep/cdn-subscription-id-include.png)

### <a name="creating-the-azure-ad-application-and-applying-permissions"></a>创建 Azure AD 应用程序并应用权限
有两种方法可以使用 Azure Active Directory 应用身份验证：单个用户或服务主体。 服务主体类似于 Windows 中的服务帐户。  不是向特定用户授予与 CDN 配置文件进行交互的权限，权限将授予给服务主体。  服务主体通常用于自动化的非交互式流程。  尽管本教程涉及到编写交互式控制台应用，但重点介绍服务主体身份验证方式。

创建服务主体的过程由多个步骤构成，其中包括创建 Azure Active Directory 应用程序。  要创建它，请[遵循此教程](../articles/active-directory/develop/howto-create-service-principal-portal.md)。

> [!IMPORTANT]
> 请务必遵循[链接的教程](../articles/active-directory/develop/howto-create-service-principal-portal.md)中的所有步骤。  必须完全按照说明完成操作，这*很重要*。  请务必记下**租户 ID**、**租户域名**（通常是 *.onmicrosoft.com* 域，除非指定了自定义域）、**客户端 ID** 和**客户端身份验证密钥**，因为稍后需要用到这些信息。  请妥善保护**客户端 ID** 和**客户端身份验证密钥**，因为任何人都可以使用这些凭据以服务主体的身份执行操作。
>
> 执行“Configure multi-tenant application”（配置多租户应用程序）步骤时，请选择“No”（否）。 
>
> 执行 [Assign the application to a role](../articles/active-directory/develop/howto-create-service-principal-portal.md#assign-the-application-to-a-role)（将应用程序分配到角色）步骤时，请使用前面创建的资源组 *CdnConsoleTutorial* 而不是“读者”角色来分配“CDN 配置文件参与者”角色。    在资源组中为应用程序分配“CDN 配置文件参与者”角色之后，请返回本教程。  
>
>

创建服务主体并分配“CDN 配置文件参与者”角色之后，资源组的“用户”边栏选项卡看起来应类似于以下图像。  

![“用户”边栏选项卡](./media/cdn-app-dev-prep/cdn-service-principal-include.png)

### <a name="interactive-user-authentication"></a>交互式用户身份验证
如果想要使用交互式个人用户身份验证而不是服务主体身份验证，操作过程与适用于服务主体的过程类似。  事实上，需要遵循的过程基本相同，只是要做一些轻微的更改。

> [!IMPORTANT]
> 仅当选择使用个人用户身份验证而不是服务主体身份验证时，才要执行后续步骤。
>
>

1. 创建应用程序时，请选择“本机应用程序”而不是“Web 应用程序”。  

    ![本机应用程序](./media/cdn-app-dev-prep/cdn-native-application-include.png)
2. 在下一页上，系统会提示输入**重定向 URI**。  系统不会验证 URI，但请记住输入的 URI。 稍后需要用到此信息。
3. 不需要创建**客户端身份验证密钥**。
4. 我们不会向“CDN 配置文件参与者”角色分配服务主体，而是分配个人用户或组。   在本示例中，可以看到已将“CDN 演示用户”分配到“CDN 配置文件参与者”角色。    

    ![个人用户访问权限](./media/cdn-app-dev-prep/cdn-aad-user-include.png)
