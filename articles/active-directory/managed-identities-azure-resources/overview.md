---
title: Azure 资源的托管标识
description: Azure 资源托管标识概述。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: overview
ms.custom: mvc
ms.date: 09/26/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 596da9cfe0e914183bd3b2603ffa1047f1d9352b
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310007"
---
# <a name="what-is-managed-identities-for-azure-resources"></a>什么是 Azure 资源的托管标识？

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

生成云应用程序时需要应对的常见挑战是，如何管理代码中用于云服务身份验证的凭据。 保护这些凭据是一项重要任务。 理想情况下，这些凭据永远不会出现在开发者工作站上，也不会被签入源代码管理系统中。 虽然 Azure Key Vault 可用于安全存储凭据、机密以及其他密钥，但代码需要通过 Key Vault 的身份验证才能检索它们。 

Azure Active Directory (Azure AD) 中的 Azure 资源托管标识功能可以解决此问题。 此功能为 Azure 服务提供了 Azure AD 中的自动托管标识。 可以使用此标识向支持 Azure AD 身份验证的任何服务（包括 Key Vault）证明身份，无需在代码中放入任何凭据。

如果有 Azure 订阅，Azure AD 中的 Azure 资源托管标识功能是免费的。 不需额外付费。

> [!NOTE]
> Azure 资源托管标识是以前称为托管服务标识 (MSI) 的服务的新名称。

## <a name="terminology"></a>术语

以下术语用于 Azure 资源文档集的托管标识：

- **客户端 ID** - Azure AD 生成的唯一标识符，在其初始预配期间与应用程序和服务主体绑定。
- **主体 ID** - 托管标识的服务主体对象的对象 ID，用于授予对 Azure 资源的基于角色的访问权限。
- **Azure 实例元数据服务 (IMDS)** - 一个 REST 终结点，可供通过 Azure 资源管理器创建的所有 IaaS VM 使用。 该终结点位于已知不可路由的 IP 地址 (169.254.169.254)，该地址只能从 VM 中访问。

## Azure 资源托管标识的工作原理<a name="how-does-it-work"></a>

托管标识分为两种类型：

- **系统分配托管标识**直接在 Azure 服务实例上启用。 启用标识后，Azure 将在实例的订阅信任的 Azure AD 租户中创建实例的标识。 创建标识后，系统会将凭据预配到实例。 系统分配标识的生命周期直接绑定到启用它的 Azure 服务实例。 如果实例遭删除，Azure 会自动清理 Azure AD 中的凭据和标识。
- **用户分配托管标识**是作为独立的 Azure 资源创建的。 在创建过程中，Azure 会在由所用订阅信任的 Azure AD 租户中创建一个标识。 在创建标识后，可以将标识分配到一个或多个 Azure 服务实例。 用户分配标识的生命周期与它所分配到的 Azure 服务实例的生命周期是分开管理的。

在内部，托管标识是特殊类型的服务主体，它们已锁定，只能与 Azure 资源配合使用。 删除托管标识时，相应的服务主体也会自动删除。 

代码可以使用托管标识来请求支持 Azure AD 身份验证的服务的访问令牌。 Azure 负责滚动更新服务实例使用的凭据。 

下图演示了托管服务标识如何与 Azure 虚拟机 (VM) 协同工作：

![托管服务标识和 Azure VM](media/overview/msi-vm-vmextension-imds-example.png)

|  属性    | 系统分配的托管标识 | 用户分配的托管标识 |
|------|----------------------------------|--------------------------------|
| 创建 |  作为 Azure 资源（例如 Azure 虚拟机或 Azure 应用服务）的一部分创建 | 作为独立 Azure 资源创建 |
| 生命周期 | 与用于创建托管标识的 Azure 资源共享生命周期。 <br/> 删除父资源时，也会删除托管标识。 | 独立生命周期。 <br/> 必须显式删除。 |
| 在 Azure 资源之间共享 | 无法共享。 <br/> 只能与单个 Azure 资源相关联。 | 可以共享 <br/> 用户分配的同一个托管标识可以关联到多个 Azure 资源。 |
| 常见用例 | 包含在单个 Azure 资源中的工作负荷 <br/> 需要独立标识的工作负荷。 <br/> 例如，在单个虚拟机上运行的应用程序 | 在多个资源上运行的并可以共享单个标识的工作负荷。 <br/> 需要在预配流程中预先对安全资源授权的工作负荷。 <br/> 其资源经常回收，但权限应保持一致的工作负荷。 <br/> 例如，其中的多个虚拟机需要访问同一资源的工作负荷 | 

### <a name="how-a-system-assigned-managed-identity-works-with-an-azure-vm"></a>系统分配托管标识如何与 Azure VM 协同工作

1. Azure 资源管理器收到请求，要求在 VM 上启用系统分配托管标识。

2. Azure 资源管理器在 Azure AD 中创建与 VM 标识相对应的服务主体。 服务主体在此订阅信任的 Azure AD 租户中创建。

3. Azure 资源管理器通过使用服务主体客户端 ID 和证书更新 Azure 实例元数据服务标识终结点来配置 VM 上的标识。

4. VM 有了标识以后，请根据服务主体信息向 VM 授予对 Azure 资源的访问权限。 若要调用 Azure 资源管理器，请在 Azure AD 中使用基于角色的访问控制 (RBAC) 向 VM 服务主体分配相应的角色。 若要调用 Key Vault，请授予代码对 Key Vault 中特定机密或密钥的访问权限。

5. 在 VM 上运行的代码可以从只能从 VM 中访问的 Azure 实例元数据服务终结点请求令牌：`http://169.254.169.254/metadata/identity/oauth2/token`
    - resource 参数指定了要向其发送令牌的服务。 若要向 Azure 资源管理器进行身份验证，请使用 `resource=https://management.azure.com/`。
    - API 版本参数指定 IMDS 版本，请使用 api-version=2018-02-01 或更高版本。

6. 调用了 Azure AD，以便使用在步骤 3 中配置的客户端 ID 和证书请求访问令牌（在步骤 5 中指定）。 Azure AD 返回 JSON Web 令牌 (JWT) 访问令牌。

7. 代码在调用支持 Azure AD 身份验证的服务时发送访问令牌。

### <a name="how-a-user-assigned-managed-identity-works-with-an-azure-vm"></a>用户分配托管标识如何与 Azure VM 协同工作

1. Azure 资源管理器收到请求，要求创建用户分配托管标识。

2. Azure 资源管理器在 Azure AD 中创建与用户分配托管标识相对应的服务主体。 服务主体在此订阅信任的 Azure AD 租户中创建。

3. Azure 资源管理器收到在 VM 上配置用户分配的托管标识的请求，并使用用户分配的托管标识服务主体客户端 ID 和证书更新 Azure 实例元数据服务标识终结点。

4. 创建用户分配托管标识以后，请根据服务主体信息向标识授予对 Azure 资源的访问权限。 若要调用 Azure 资源管理器，请在 Azure AD 中使用 RBAC 向用户分配标识的服务主体分配相应的角色。 若要调用 Key Vault，请授予代码对 Key Vault 中特定机密或密钥的访问权限。

   > [!Note]
   > 也可在步骤 3 之前执行此步骤。

5. 在 VM 上运行的代码可以从只能从 VM 中访问的 Azure 实例元数据服务标识终结点请求令牌：`http://169.254.169.254/metadata/identity/oauth2/token`
    - resource 参数指定了要向其发送令牌的服务。 若要向 Azure 资源管理器进行身份验证，请使用 `resource=https://management.azure.com/`。
    - 客户端 ID 参数指定为其请求令牌的标识。 当单台 VM 上有多个用户分配的标识时，此值是消除歧义所必需的。
    - API 版本参数指定 Azure 实例元数据服务版本。 请使用 `api-version=2018-02-01` 或指定更高的版本。

6. 调用了 Azure AD，以便使用在步骤 3 中配置的客户端 ID 和证书请求访问令牌（在步骤 5 中指定）。 Azure AD 返回 JSON Web 令牌 (JWT) 访问令牌。
7. 代码在调用支持 Azure AD 身份验证的服务时发送访问令牌。

## <a name="how-can-i-use-managed-identities-for-azure-resources"></a>如何使用 Azure 资源的托管标识？

若要了解如何使用托管标识来访问不同的 Azure 资源，请尝试以下教程。

> [!NOTE]
> 请查看[为 Microsoft Azure 资源实施托管标识](https://www.pluralsight.com/courses/microsoft-azure-resources-managed-identities-implementing)来了解有关托管标识的详细信息，包括多个受支持方案的详细视频演练。

了解如何将托管标识与 Windows VM 配合使用：

* [访问 Azure Data Lake Store](tutorial-windows-vm-access-datalake.md)
* [访问 Azure 资源管理器](tutorial-windows-vm-access-arm.md)
* [访问 Azure SQL](tutorial-windows-vm-access-sql.md)
* [使用访问密钥访问 Azure 存储](tutorial-windows-vm-access-storage.md)
* [使用共享访问签名访问 Azure 存储](tutorial-windows-vm-access-storage-sas.md)
* [使用 Azure Key Vault 访问非 Azure AD 资源](tutorial-windows-vm-access-nonaad.md)

了解如何将托管标识与 Linux VM 配合使用：

* [访问 Azure Data Lake Store](tutorial-linux-vm-access-datalake.md)
* [访问 Azure 资源管理器](tutorial-linux-vm-access-arm.md)
* [使用访问密钥访问 Azure 存储](tutorial-linux-vm-access-storage.md)
* [使用共享访问签名访问 Azure 存储](tutorial-linux-vm-access-storage-sas.md)
* [使用 Azure Key Vault 访问非 Azure AD 资源](tutorial-linux-vm-access-nonaad.md)
* [访问 Azure 容器注册表](../../container-registry/container-registry-authentication-managed-identity.md)

了解如何将托管标识与其他 Azure 服务配合使用：

* [Azure 应用服务](/azure/app-service/overview-managed-identity)
* [Azure Functions](/azure/app-service/overview-managed-identity)
* [Azure 逻辑应用](/azure/logic-apps/create-managed-service-identity)
* [Azure 服务总线](../../service-bus-messaging/service-bus-managed-service-identity.md)
* [Azure 事件中心](../../event-hubs/event-hubs-managed-service-identity.md)
* [Azure API 管理](../../api-management/api-management-howto-use-managed-service-identity.md)
* [Azure 容器实例](../../container-instances/container-instances-managed-identity.md)
* [Azure 容器注册表任务](../../container-registry/container-registry-tasks-authentication-managed-identity.md)

## 哪些 Azure 服务支持此功能？<a name="which-azure-services-support-managed-identity"></a>

Azure 资源的托管标识可以用来向支持 Azure AD 身份验证的服务证明身份。 如需支持 Azure 资源托管标识功能的 Azure 服务的列表，请参阅[支持 Azure 资源托管标识的服务](services-support-msi.md)。

## <a name="next-steps"></a>后续步骤

请参阅以下快速入门，开始使用 Azure 资源托管标识功能：

* [使用 Windows VM 系统分配托管标识访问资源管理器](tutorial-windows-vm-access-arm.md)
* [使用 Linux VM 系统分配托管标识访问资源管理器](tutorial-linux-vm-access-arm.md)
