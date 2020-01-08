---
title: Microsoft Azure 静态数据加密 | Microsoft Docs
description: 本文概述了 Microsoft Azure 静态数据加密及其整体功能和一般注意事项。
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2019
ms.author: barclayn
ms.openlocfilehash: 3b60a6da1e7961c7709bb0b19e91dc6f15a51a1c
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316778"
---
# <a name="azure-data-encryption-at-rest"></a>Azure 静态数据加密

Microsoft Azure 提供了许多工具，可以使用它们根据你公司的安全性和符合性需求来保护数据。 本白皮书重点介绍：

- 如何在 Microsoft Azure 上对数据进行静态保护
- 讨论参与数据保护实现的各个组件
- 查看不同密钥管理保护方法的优点和缺点。

静态加密是常见的安全要求。 在 Azure 中，组织可以加密静态数据，而不会造成自定义密钥管理解决方案的风险或成本。 组织可以选择让 Azure 来全权管理静态加密。 另外，组织还可以通过各种选择来严格管理加密或加密密钥。

## <a name="what-is-encryption-at-rest"></a>什么是静态加密？

静态加密是指在持久保存数据时对数据进行编码（加密）。 Azure 中的静态加密设计使用对称加密根据简单的概念模型来快速加密和解密大量数据：

- 将使用对称加密密钥在将数据写入到存储时对数据进行加密。
- 当数据在内存中就绪可供使用时，将会使用同一加密密钥来解密该数据。
- 可以将数据分区，并可对每个分区使用不同的密钥。
- 必须将密钥存储在实施了基于标识的访问控制和审核策略的安全位置。 数据加密密钥通常使用 Azure Key Vault 中的密钥加密密钥进行加密，以进一步限制访问。

在实践中，密钥管理和控制方案以及规模和可用性保证都需要其他构造。 下面描述的是 Microsoft Azure 静态加密概念和组件。

## <a name="the-purpose-of-encryption-at-rest"></a>静态加密的目的

静态加密为已存储的数据（静止的）提供数据保护。 对静态数据进行的攻击包括：试图获得存储数据的硬件的物理访问机会，然后盗用其中包含的数据。 发生此类攻击可能是由于服务器的硬盘驱动器在维护过程中处理不当，导致攻击者有机会拆除硬盘驱动器。 攻击者随后会将该硬盘驱动器置于受其控制的计算机中，尝试访问相关数据。

静态加密旨在防止攻击者访问未加密的数据，其方法是确保这些数据在磁盘上时是加密的。 如果攻击者获取了包含加密数据的硬盘驱动器但未获取加密密钥，则攻击者必须破解加密才能读取数据。 这种攻击比访问硬盘驱动器上的未加密数据要复杂得多，且消耗的资源也多得多。 因此，强烈建议使用静态加密。对于许多组织来说，这是需要完成的高优先级事项。

当组织需要进行数据治理并确保符合性时，可能也需要使用静态加密。 行业和政府法规（例如 HIPAA、PCI 和 FedRAMP）就数据保护和加密要求制定了具体的保障措施。 要符合这其中的许多法规，静态加密是一种必需的强制措施。

除了满足合规要求以外，静态加密还能提供深层防御保护。 Microsoft Azure 为服务、应用程序和数据提供合规的平台。 此外，它还提供综合性的设施和物理安全性、数据访问控制和审核。 但是，必须提供额外的“重叠性”安全措施，以免出现其他某个安全措施失效的情况，而静态加密正好提供这样一道安全措施

Microsoft 致力于提供跨云服务的静态加密选项，可让客户控制加密密钥和密钥使用日志。 另外，Microsoft 正在努力实现默认加密所有客户静态数据。

## <a name="azure-encryption-at-rest-components"></a>Azure 静态加密组件

如前所述，静态加密的目标是使用机密加密密钥来加密持久保存在磁盘上的数据。 若要实现该目标，必须为加密密钥提供安全的密钥创建、存储、访问控制和管理措施。 可以使用下图中介绍的术语来描述 Azure 服务静态加密实现，虽然细节可能有所不同。

![组件](./media/encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>Azure Key Vault

对于静态加密模型来说，最重要的是加密密钥的存储位置以及对这些密钥的访问控制。 密钥需要严格的保护，但同时又要能够由指定的用户进行管理，并可供特定的服务使用。 对于 Azure 服务，建议使用 Azure Key Vault 作为密钥存储解决方案，它可以跨服务提供通常的管理体验。 密钥在密钥保管库中存储和管理，对密钥保管库的访问权限可以提供给用户或服务。 Azure Key Vault 支持客户创建密钥，也支持将导入的客户密钥用于客户管理的加密密钥方案。

### <a name="azure-active-directory"></a>Azure Active Directory

可以为 Azure Active Directory 帐户提供存储在 Azure Key Vault 中的密钥的使用权限，以便通过管理或访问这些密钥来完成静态加密的加密和解密操作。

### <a name="key-hierarchy"></a>密钥层次结构

在实施静态加密时，使用多个加密密钥。 在 Azure Key Vault 中存储加密密钥可确保密钥的安全密钥访问和集中管理。 但是，对于与每个数据操作的 Key Vault 进行交互相比，对加密密钥的本地访问与与进行交互相比，对加密密钥的本地访问更高效，从而实现更强的加密，提高性能。 限制单个加密密钥的使用降低了密钥被盗用的风险，也降低了必须更换密钥时的重新加密成本。 Azure 加密 rest 模型使用由以下密钥类型组成的密钥层次结构来满足所有这些需求：

- 数据加密密钥 (DEK) – 对称 AES256 密钥，用于加密数据分区或块。  单个资源可能有多个分区和多个数据加密密钥。 使用不同的密钥加密每个数据块可以增加加密分析攻击的难度。 资源提供程序或应用程序实例需要 DEK 访问权限才能加密和解密特定的块。 将 DEK 替换为新密钥时，仅其关联的块中的数据需要使用新密钥重新加密。
- **密钥加密密钥（KEK）** –用于对数据加密密钥进行加密的加密密钥。 使用永远不会离开 Key Vault 的密钥加密密钥，可以加密和控制数据加密密钥。 具有 KEK 访问权限的实体可能不同于需要 DEK 的实体。 实体可能会代理对 DEK 的访问以将每个 DEK 的访问限制到特定分区。 由于解密 DEK 需要 KEK，因此 KEK 实际上构成了一个单点机制：删除 KEK 即可删除 DEK。

使用密钥加密密钥加密的数据加密密钥单独进行存储，只有有权访问密钥加密密钥的实体才能解密这些数据加密密钥。 支持各种不同的密钥存储模型。 我们会在后面（下一部分）更详细地讨论每个模型。

## <a name="data-encryption-models"></a>数据加密模型

了解各种加密模型及其优缺点很重要，有助于了解 Azure 中的各个资源提供程序是如何实施静态加密的。 这些定义在 Azure 的所有资源提供程序中是共享的，目的是确保共同的语言和分类。

服务器端加密有三种方案：

- 使用服务托管密钥进行服务器端加密
  - Azure 资源提供程序执行加密和解密操作
  - Microsoft 管理密钥
  - 完整云功能

- 使用 Azure Key Vault 中客户托管密钥的服务器端加密
  - Azure 资源提供程序执行加密和解密操作
  - 客户通过 Azure Key Vault 控制密钥
  - 完整云功能

- 使用客户所控制硬件上的客户托管密钥的服务器端加密
  - Azure 资源提供程序执行加密和解密操作
  - 客户控制其所控制的硬件上的密钥
  - 完整云功能

对于客户端加密，请注意以下事项：

- Azure 服务无法看到已解密的数据
- 客户在本地（或其他安全存储中）管理和存储密钥。 Azure 服务无法使用密钥
- 精简云功能

Azure 中支持的加密模型分为两大类：如前所述，“客户端加密”和“服务器端加密”。 Azure 服务始终建议使用独立于所用静态加密模型的安全传输（例如 TLS 或 HTTPS）。 因此，传输过程中的加密应由传输协议来处理，不应成为决定要使用的静态加密模型的主要因素。

### <a name="client-encryption-model"></a>客户端加密模型

客户端加密模型是指由服务或调用应用程序在资源提供程序或 Azure 外部执行的加密。 加密可以由 Azure 中的服务应用程序执行，也可以由在客户数据中心运行的应用程序执行。 不管哪种情况，在采用此加密模型时，Azure 资源提供程序都会收到加密的数据 blob，但却无法以任何方式解密数据，也无法访问加密密钥。 在此模型中，密钥管理由调用服务/应用程序执行，对 Azure 服务来说是不透明的。

![客户端](./media/encryption-atrest/azure-security-encryption-atrest-fig2.png)

### <a name="server-side-encryption-model"></a>服务器端加密模型

服务器端加密模型是指由 Azure 服务执行的加密。 在该模型中，资源提供程序执行加密和解密操作。 例如，Azure 存储可能会以纯文本操作方式接收数据，并且会在内部进行加密和解密。 资源提供程序可能使用由 Microsoft 或客户管理的加密密钥，具体取决于提供的配置。

![服务器](./media/encryption-atrest/azure-security-encryption-atrest-fig3.png)

### <a name="server-side-encryption-key-management-models"></a>服务器端加密密钥管理模型

每个服务器端静态加密模型都暗含密钥管理的独特特征。 其中包括：加密密钥的创建和存储位置和方式，以及访问模型和密钥轮换过程。

#### <a name="server-side-encryption-using-service-managed-keys"></a>使用服务托管密钥的服务器端加密

对许多客户来说，基本要求就是确保数据在静态时能够获得加密。 使用服务托管密钥的服务器端加密实现该模型的方式是：让客户标出适用于加密的特定资源（存储帐户、SQL DB 等），将所有密钥管理事项（例如密钥的颁发、轮换和备份）留给 Microsoft。 大多数支持静态加密的 Azure 服务通常支持这种将加密密钥管理任务留给 Azure 的模型。 Azure 资源提供程序创建密钥，将其置于安全的存储中，然后根据需要对其进行检索。 这意味着，服务具有密钥的完全访问权限，且服务可以全权控制凭据生命周期管理。

![托管式](./media/encryption-atrest/azure-security-encryption-atrest-fig4.png)

因此，使用服务托管密钥的服务器端加密可以快速满足进行静态加密并降低客户开销的需求。 在可用的情况下，客户通常会打开适用于目标订阅和资源提供程序的 Azure 门户，选中一个表明其希望数据加密的复选框。 在某些资源管理器中，使用服务托管密钥的服务器端加密默认处于启用状态。

使用 Microsoft 托管密钥的服务器端加密意味着服务对存储具有完全访问权限，并且可以管理密钥。 某些客户可能希望对密钥进行管理，因为他们觉得自己可以获得更高的安全性，但在评估此模型时，应该考虑与自定义密钥存储解决方案相关联的成本和风险。 在许多情况下，组织可能觉得本地解决方案的资源约束或风险会大于对静态加密密钥进行云管理的风险。  但是，此模型可能满足不了某些组织的需求，这些组织需要控制加密密钥的创建或生命周期，或者需要安排某个人来管理服务，安排另一个人来管理该服务的加密密钥（也就是说，这是一种将密钥管理与总体管理分开的服务管理模型）。

##### <a name="key-access"></a>密钥访问权限

使用服务托管密钥的服务器端加密在使用时，密钥的创建、存储和服务访问均由服务管理。 通常情况下，基础 Azure 资源提供程序会将数据加密密钥存储在靠近数据且能快速使用和访问的存储中，而将密钥加密密钥存储在安全的内部存储中。

**优点**

- 安装简单
- Microsoft 管理密钥轮换、备份和冗余
- 客户没有与实施相关联的成本，也没有自定义密钥管理方案的风险。

**缺点**

- 客户无法控制加密密钥（密钥规范、生命周期、吊销等）
- 此服务的管理模型无法将密钥管理与总体管理分开

#### <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>使用 Azure Key Vault 中客户托管密钥的服务器端加密

对于需要加密静态数据并控制加密密钥的情况，客户可以选择使用 Key Vault 中客户托管密钥的服务器端加密。 某些服务可能仅将根密钥加密密钥存储在 Azure Key Vault 中，而将加密的数据加密密钥存储在更靠近数据的内部位置。 在这种情况下，客户可以将自己的密钥带到 Key Vault 中（BYOK – 自带密钥），或者生成新的密钥，以便加密所需资源。 资源提供程序执行加密和解密操作时，会使用配置的密钥加密密钥作为所有加密操作的根密钥。

密钥加密密钥丢失意味着数据丢失。 出于此原因，不应删除密钥。 每次创建或旋转时都应备份密钥。 应在存储密钥加密密钥的任何保管库上启用[软删除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)。 不是删除密钥、将 "启用" 设置为 "false" 或 "设置到期日期"。

##### <a name="key-access"></a>密钥访问权限

将客户托管密钥置于 Azure Key Vault 中的服务器端加密模型涉及到的操作是，服务会根据需要访问用于加密和解密的密钥。 可以通过访问控制策略来允许服务访问静态加密密钥。 此策略授予服务标识接收密钥所需的访问权限。 可以为代表关联的订阅运行的 Azure 服务配置一个该订阅中的标识。 该服务可以执行 Azure Active Directory 身份验证，然后会收到一个身份验证令牌，将服务本身标识为代表该订阅的服务。 然后，该服务可以将该令牌出示给 Key Vault，以便获取有权访问的密钥。

对于使用加密密钥的操作，可以为服务标识授予以下任何操作的访问权限：decrypt、encrypt、unwrapKey、wrapKey、verify、sign、get、list、update、create、import、delete、backup、restore。

若要获取用于加密或解密静态数据的密钥，服务标识（将由资源管理器服务实例在运行时充当）必须使用 UnwrapKey 来获取解密用的密钥，并在创建新密钥时使用 WrapKey 将密钥插入密钥保管库中。

>[!NOTE]
>有关 Key Vault 授权的更多详细信息，请参阅 [Azure Key Vault 文档](../../key-vault/key-vault-secure-your-key-vault.md)中的“保护密钥保管库”页。

**优点**

- 全权控制所用密钥 – 加密密钥在客户的 Key Vault 中管理，受客户的控制。
- 能够通过加密将多个服务转换成一个主服务
- 此服务的管理模型可以将密钥管理与总体管理分开
- 可以跨区域定义服务和密钥位置

**缺点**

- 客户全权负责密钥访问管理
- 客户全权负责密钥生命周期管理
- 额外的安装和配置开销

#### <a name="server-side-encryption-using-customer-managed-keys-in-customer-controlled-hardware"></a>在客户控制的硬件中使用客户托管密钥的服务器端加密

某些 Azure 服务启用了“托管自己的密钥 (HYOK)”密钥管理模型。 当需要对静止的数据进行加密并在不受 Microsoft 控制的专有存储库中管理密钥时，此管理模式非常有用。 在此模型中，服务必须从外部站点检索密钥。 性能和可用性担保会受影响，并且配置更复杂。 另外，由于服务可以在加密和解密操作过程中访问 DEK，此模型的总体安全保证类似于密钥在 Azure Key Vault 中由客户托管的情况。  因此，此模型不适合大多数组织，除非该组织有特定的密钥管理要求。 由于这些限制，大多数 Azure 服务不支持使用客户所控制硬件中的服务托管密钥的服务器端加密。

##### <a name="key-access"></a>密钥访问权限

选择使用客户所控制硬件中的服务托管密钥的服务器端加密时，密钥保留在客户配置的系统上。 支持此模型的 Azure 服务提供了一种建立安全连接的方法，用于连接到客户提供的密钥存储。

**优点**

- 全权控制所用的根密钥 – 加密密钥由客户提供的存储托管
- 能够通过加密将多个服务转换成一个主服务
- 此服务的管理模型可以将密钥管理与总体管理分开
- 可以跨区域定义服务和密钥位置

**缺点**

- 全权负责密钥的存储、安全、性能和可用性
- 全权负责密钥访问管理
- 全权负责密钥生命周期管理
- 极高的安装、配置和持续维护成本
- 增强了对客户数据中心和 Azure 数据中心之间网络可用性的依赖。

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Microsoft 云服务中的静态加密

Microsoft 云服务用于下述所有三个云模型：IaaS、PaaS、SaaS。 下面是在每个模型上使用该服务的示例：

- 软件服务，也称软件即服务（简称 SaaS），包含云提供的应用程序，例如 Office 365。
- 平台服务，方便客户在其应用程序中利用云，将云用于存储、分析和服务总线功能等。
- 基础结构服务，也称基础结构即服务 (IaaS)，方便客户部署托管在云中的操作系统和应用程序，并尽可能利用其他云服务。

### <a name="encryption-at-rest-for-saas-customers"></a>适合 SaaS 客户的静态加密

软件即服务 (SaaS) 客户通常会在每个服务中启用或提供静态加密。 Office 365 为客户提供多个验证或启用静态加密的选项。 有关 Office 365 服务的信息，请参阅 [Office 365 中的加密](https://docs.microsoft.com/office365/securitycompliance/encryption)。

### <a name="encryption-at-rest-for-paas-customers"></a>适合 PaaS 客户的静态加密

平台即服务（PaaS）客户的数据通常驻留在存储服务（如 Blob 存储）中，但也可以缓存或存储在应用程序执行环境（例如虚拟机）中。 若要查看适用的静态加密选项，请检查下表中是否存在所用的存储和应用程序平台。

### <a name="encryption-at-rest-for-iaas-customers"></a>适合 IaaS 客户的静态加密

基础结构即服务 (IaaS) 客户可以使用各种服务和应用程序。 IaaS 服务可以在其 Azure 托管的虚拟机和 VHD 中通过 Azure 磁盘加密来启用静态加密。

#### <a name="encrypted-storage"></a>加密的存储

与 PaaS 一样，IaaS 解决方案可以利用其他存储静态加密数据的 Azure 服务。 在此类情况下，可以启用每个所用 Azure 服务提供的静态加密支持。 下表枚举了主要的存储、服务和应用程序平台以及所支持的静态加密模型。 

#### <a name="encrypted-compute"></a>加密的计算

所有托管磁盘、快照和映像都使用存储服务加密使用服务托管密钥进行加密。 更完整的静态加密解决方案确保数据永远不会以未加密形式持久保存。 在处理虚拟机上的数据时，可以将数据保存到 Windows 页面文件或 Linux 交换文件、崩溃转储或应用程序日志。 为了确保对该数据进行静态加密，IaaS 应用程序可以在 Azure IaaS 虚拟机（Windows 或 Linux）和虚拟磁盘上使用 Azure 磁盘加密。

#### <a name="custom-encryption-at-rest"></a>自定义静态加密

建议让 IaaS 应用程序尽可能利用 Azure 磁盘加密以及任何所用 Azure 服务提供的静态加密选项。 在某些情况下（例如加密要求异乎寻常，或者存储不是基于 Azure 的），IaaS 应用程序开发人员可能需要自行实施静态加密。 IaaS 解决方案开发人员可以利用某些 Azure 组件，改进与 Azure 管理的集成并更好地满足客户期望。 具体说来，开发人员应该使用 Azure Key Vault 服务为其客户提供安全的密钥存储，以及提供与大多数 Azure 平台服务一致的密钥管理选项。 另外，自定义解决方案应通过 Azure 托管服务标识来允许服务帐户访问加密密钥。 有关 Azure Key Vault 和托管服务标识的开发人员信息，请参阅各自的 SDK。

## <a name="azure-resource-providers-encryption-model-support"></a>Azure 资源提供程序加密模型支持

每个 Microsoft Azure 服务都支持一个或多个静态加密模型。 但是，对于某些服务来说，其中的一个或多个加密模型可能并不适用。 对于支持客户管理的密钥方案的服务，它们可能只支持 Azure Key Vault 支持用于密钥加密密钥的密钥类型的一个子集。 另外，服务可能会按不同的计划发布对这些方案和密钥类型的支持。 此部分介绍的静态加密支持在撰写本文时仍适用于每个主要的 Azure 数据存储服务。

### <a name="azure-disk-encryption"></a>Azure 磁盘加密

任何使用 Azure 基础结构即服务 (IaaS) 功能的客户都可以通过 Azure 磁盘加密为其 IaaS VM 和磁盘实施静态加密。 有关 Azure 磁盘加密的详细信息，请参阅 [Azure 磁盘加密文档](../azure-security-disk-encryption-overview.md)。

#### <a name="azure-storage"></a>Azure 存储

所有 Azure 存储服务（Blob 存储、队列存储、表存储和 Azure 文件）都支持服务器端加密;某些服务还支持客户管理的密钥和客户端加密。 

- 服务器端：默认情况下，所有 Azure 存储服务都使用服务托管的密钥来启用服务器端加密（对应用程序而言是透明的）。 有关详细信息，请参阅[静态数据的 Azure 存储服务加密](../../storage/common/storage-service-encryption.md)。 Azure Blob 存储和 Azure 文件也支持 Azure Key Vault 中客户托管的 RSA 2048 位密钥。 有关详细信息，请参阅 [Azure Key Vault 中使用客户托管密钥的存储服务加密](../../storage/common/storage-encryption-keys-portal.md)。
- 客户端：Azure Blob、表和队列支持客户端加密。 使用客户端加密时，客户会加密数据并将数据作为加密的 blob 上传。 密钥管理由客户执行。 有关详细信息，请参阅 [Microsoft Azure 存储的客户端加密和 Azure Key Vault](../../storage/common/storage-client-side-encryption.md)。

#### <a name="azure-sql-database"></a>Azure SQL 数据库

Azure SQL 数据库目前支持将静态加密用于 Microsoft 托管的服务器端和客户端加密方案。

对服务器加密的支持目前通过名为“透明数据加密”的 SQL 功能来提供。 在 Azure SQL 数据库客户启用 TDE 后，系统会自动为其创建和管理密钥。 可以在数据库和服务器级别启用静态加密。 从 2017 年 6 月开始，会在新创建的数据库上默认启用[透明数据加密 (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)。 Azure SQL 数据库支持 Azure Key Vault 中客户管理的 RSA 2048 位密钥。 有关详细信息，请参阅[使用 Azure SQL 数据库和数据仓库的“创建自己的密钥”支持进行透明数据加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql?view=azuresqldb-current)。

可以通过 [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) 功能启用对 Azure SQL 数据库数据的客户端加密。 Always Encrypted 使用由客户端创建和存储的密钥。 客户可以将主密钥存储在 Windows 证书存储、Azure Key Vault 或本地硬件安全模块中。 使用 SQL Server Management Studio 时，SQL 用户可以选择想要使用什么密钥来加密哪个列。

#### <a name="encryption-model-and-key-management-table"></a>加密模型和密钥管理表

|                                  |                    | **加密模型和密钥管理** |                    |
|----------------------------------|--------------------|-----------------------------------------|--------------------|
|                                  | **使用服务托管密钥的服务器端**     | **使用客户托管密钥的服务器端**             | **使用客户端托管密钥的客户端**      |
| **AI 和机器学习**      |                    |                    |                    |
| Azure 搜索                     | 是                | 预览            | -                  |
| Azure 机器学习服务   | 是                | -                  | -                  |
| Azure 机器学习工作室    | 是                | 预览，RSA 2048 位 | -               |
| Power BI                         | 是                | 预览，RSA 2048 位 | -                  |
| **分析**                    |                    |                    |                    |
| Azure 流分析           | 是                | -                  | -                  |
| 事件中心                       | 是                | 预览，所有 RSA 长度。 | -                  |
| Azure Analysis Services          | 是                | -                  | -                  |
| Azure 数据目录               | 是                | -                  | -                  |
| Azure HDInsight 上的 Apache Kafka  | 是                | 所有 RSA 长度。   | -                  |
| Azure 数据资源管理器              | 是                | -                  | -                  |
| Azure 数据工厂               | 是                | 是                | -                  |
| Azure Data Lake Store            | 是                | 是，RSA 2048 位  | -                  |
| **容器**                   |                    |                    |                    |
| Azure Kubernetes 服务         | 是                | -                  | -                  |
| 容器注册表               | 是                | -                  | -                  |
| **计算**                      |                    |                    |                    |
| 虚拟机                 | 是                | 是，RSA 2048 位  | -                  |
| 虚拟机规模集        | 是                | 是，RSA 2048 位  | -                  |
| SAP HANA                         | 是                | 是，RSA 2048 位  | -                  |
| **数据库**                    |                    |                    |                    |
| 虚拟机上的 SQL Server   | 是                | 是，RSA 2048 位  | 是                |
| Azure SQL 数据库               | 是                | 是，RSA 2048 位  | 是                |
| 用于 MariaDB 的 Azure SQL 数据库   | 是                | -                  | -                  |
| 用于 MySQL 的 Azure SQL 数据库     | 是                | -                  | -                  |
| 用于 PostgreSQL 的 Azure SQL 数据库 | 是                | -                  | -                  |
| Azure SQL 数据仓库         | 是                | 是，RSA 2048 位  | 是                |
| SQL Server Stretch Database      | 是                | 是，RSA 2048 位  | 是                |
| 表存储                    | 是                | -                  | 是                |
| Azure Cosmos DB                  | 是                | -                  | -                  |
| **DevOps**                       |                    |                    |                    |
| Azure DevOps                     | 是                | -                  | 是                |
| Azure Repos                      | 是                | -                  | 是                |
| **标识**                     |                    |                    |                    |
| Azure Active Directory           | 是                | -                  | -                  |
| Azure Active Directory 域服务 | 是          | 是，RSA 2048 位  | -                  |
| **集成**                  |                    |                    |                    |
| 服务总线                      | 是                | -                  | 是                |
| 事件网格                       | 是                | -                  | -                  |
| API 管理                   | 是                | -                  | -                  |
| IoT 服务                 |                    |                    |                    |
| IoT 中心                          | 是                | -                  | 是                |
| **管理和管理**    |                    |                    |                    |
| Azure 站点恢复              | 是                | 是，RSA 2048 位  | 是                |
| **许可证**                        |                    |                    |                    |
| 媒体服务                   | 是                | -                  | 是                |
| **存储**                      |                    |                    |                    |
| Blob 存储                     | 是                | 是，RSA 2048 位  | 是                |
| 磁盘存储                     | 是                | -                  | -                  |
| 托管磁盘存储             | 是                | -                  | -                  |
| 文件存储                     | 是                | 是，RSA 2048 位  | -                  |
| 队列存储                    | 是                | -                  | 是                |
| Avere vFXT                       | 是                | -                  | -                  |
| Azure NetApp 文件               | 是                | -                  | -                  |
| 存档存储                  | 是                | 是，RSA 2048 位  | -                  |
| StorSimple                       | 是                | 是，RSA 2048 位  | 是                |
| Azure 备份                     | 是                | -                  | 是                |
| Data Box                         | 是                | -                  | 是                |

## <a name="conclusion"></a>结束语

保护存储在 Azure 服务中的客户数据对于 Microsoft 来说至关重要。 所有 Azure 托管服务都会始终提供静态加密选项。 基础服务（例如 Azure 存储、Azure SQL 数据库以及密钥分析和智能服务）已经提供静态加密选项。 其中的某些服务既支持客户控制的密钥和客户端加密，又支持服务托管的密钥和加密。 Microsoft Azure 服务正在大范围地增强静态加密的可用性，计划在未来数月中推出新功能的预览版和公开发行版。
