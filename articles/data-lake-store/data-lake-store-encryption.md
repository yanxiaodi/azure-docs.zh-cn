---
title: Azure Data Lake Storage Gen1 中的加密 | Microsoft Docs
description: 在 Azure Data Lake Storage Gen1 中加密可以保护数据、实施企业安全策略以及满足合规性要求。 本文概述了设计，并讨论了实施过程中的某些技术方面。
services: data-lake-store
documentationcenter: ''
author: esung22
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: yagupta
ms.openlocfilehash: a009f212bd8baaa353d602dc6090aeeccddd4936
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60878346"
---
# <a name="encryption-of-data-in-azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1 中的数据加密

在 Azure Data Lake Storage Gen1 中加密可以保护数据、实施企业安全策略以及满足合规性要求。 本文概述了设计，并讨论了实施过程中的某些技术方面。

Data Lake Storage Gen1 支持对静态数据和传输数据加密。 对于静态数据，Data Lake Storage Gen1 支持“默认启用”透明加密。 下面是相关术语更详细一些的含义：

* **在默认情况下**:创建新的数据湖存储 Gen1 帐户时，默认设置启用加密。 随后会按照惯例将存储在 Data Lake Storage Gen1 中的数据加密，再将其存储到永久性介质上。 这是针对所有数据的行为，在创建帐户后不能更改。
* **透明**:数据湖存储 Gen1 自动之前加密数据，并在检索之前解密数据。 加密由管理员在 Data Lake Storage Gen1 帐户级别配置和管理。 不会对数据访问 API 进行更改， 因此不需在因加密而与 Data Lake Storage Gen1 交互的应用程序和服务中更改。

此外，还会始终在 Data Lake Storage Gen1 中对传输数据（也称移动数据）加密。 除了在存储到永久性介质之前对数据加密，还会始终通过 HTTPS 对传输数据进行保护。 HTTPS 是 Data Lake Storage Gen1 REST 接口唯一支持的协议。 下图显示了数据如何在 Data Lake Storage Gen1 中加密：

![Data Lake Storage Gen1 中的数据加密示意图](./media/data-lake-store-encryption/fig1.png)


## <a name="set-up-encryption-with-data-lake-storage-gen1"></a>设置 Data Lake Storage Gen1 的加密

Data Lake Storage Gen1 的加密是在帐户创建过程中设置的，默认情况下始终启用。 可以自行管理密钥，也可以让 Data Lake Storage Gen1 进行管理（此为默认设置）。

有关详细信息，请参阅[入门](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)。

## <a name="how-encryption-works-in-data-lake-storage-gen1"></a>Data Lake Storage Gen1 中的加密原理

以下内容说明了如何管理主加密密钥，并介绍了三种可以在 Data Lake Storage Gen1 数据加密中使用的不同类型的密钥。

### <a name="master-encryption-keys"></a>主加密密钥

Data Lake Storage Gen1 提供两种管理主加密密钥 (MEK) 的模式。 现在，请假定主加密密钥为顶级密钥。 若要解密存储在 Data Lake Storage Gen1 中的数据，必须访问主加密密钥。

管理主加密密钥的两种模式如下：

*   服务管理的密钥
*   客户管理的密钥

两种模式都是将主加密密钥存储在 Azure Key Vault 中进行保护。 Key Vault 是 Azure 上的安全度很高的完全托管式服务，可以用来保护加密密钥的安全。 有关详细信息，请参阅 [Key Vault](https://azure.microsoft.com/services/key-vault)。

下面对两种 MEK 管理模式提供的功能进行了简要比较。

|  | 服务管理的密钥 | 客户管理的密钥 |
| --- | --- | --- |
|如何存储数据？|始终在存储前加密。|始终在存储前加密。|
|在何处存储主加密密钥？|Key Vault|Key Vault|
|是否可以在 Key Vault 外部存储明文形式的加密密钥？ |否|否|
|能否通过 Key Vault 检索 MEK？|不。 存储在 Key Vault 中以后，MEK 就只能用于加密和解密。|不。 存储在 Key Vault 中以后，MEK 就只能用于加密和解密。|
|谁拥有 Key Vault 实例和 MEK？|Data Lake Storage Gen1 服务|拥有 Key Vault 实例，该实例属于自己的 Azure 订阅。 Key Vault 中的 MEK 可以通过软件或硬件进行管理。|
|能否撤消对 Data Lake Storage Gen1 服务的 MEK 的访问权限？|否|是的。 可以管理 Key Vault 中的访问控制列表，删除 Data Lake Storage Gen1 服务的服务标识的访问控制项。|
|能否永久删除 MEK？|否|是的。 如果从 Key Vault 中删除 MEK，则任何人（包括 Data Lake Storage Gen1 服务）都不能解密 Data Lake Storage Gen1 帐户中的数据。 <br><br> 如果在将 MEK 从 Key Vault 中删除以前对其进行了显式备份，则可将 MEK 还原，然后对数据进行恢复。 但是，如果在将 MEK 从 Key Vault 中删除以前未对其进行备份，则再也不能在以后解密 Data Lake Storage Gen1 帐户中的数据。|


除了该差异（即谁管理 MEK 及其所在的 Key Vault 实例），该设计的其余部分对两种模式来说都是相同的。

选择主加密密钥的模式时，请务必记住以下几点：

*   预配 Data Lake Storage Gen1 帐户时，可以选择使用客户管理的密钥或服务管理的密钥。
*   预配 Data Lake Storage Gen1 帐户以后，将无法更改模式。

### <a name="encryption-and-decryption-of-data"></a>加密和解密数据

设计数据加密时，使用三种类型的密钥。 下表进行了汇总：

| 密钥                   | 缩写 | 关联项 | 存储位置                             | Type       | 说明                                                                                                   |
|-----------------------|--------------|-----------------|----------------------------------------------|------------|---------------------------------------------------------------------------------------------------------|
| 主加密密钥 | MEK          | Data Lake Storage Gen1 帐户 | Key Vault                              | 非对称 | 可以由 Data Lake Storage Gen1 或你来管理。                                                              |
| 数据加密密钥   | DEK          | Data Lake Storage Gen1 帐户 | 永久性存储，由 Data Lake Storage Gen1 服务管理 | 对称  | DEK 由 MEK 加密。 加密的 DEK 存储在永久性介质上。 |
| 块加密密钥  | BEK          | 数据块 | 无                                         | 对称  | BEK 派生自 DEK 和数据块。                                                      |

下图演示了这些概念：

![用于数据加密的密钥](./media/data-lake-store-encryption/fig2.png)

#### <a name="pseudo-algorithm-when-a-file-is-to-be-decrypted"></a>解密文件时所使用的伪算法：
1.  检查 Data Lake Storage Gen1 帐户的 DEK 是否已缓存并可供使用。
    - 否则，请从永久性存储读取加密的 DEK，然后将其发送到 Key Vault 进行解密。 将解密的 DEK 缓存在内存中。 现可供使用。
2.  对于文件中的每个数据块，请执行以下操作：
    - 从永久性存储读取加密的数据块。
    - 根据 DEK 和加密的数据块生成 BEK。
    - 使用 BEK 解密数据。


#### <a name="pseudo-algorithm-when-a-block-of-data-is-to-be-encrypted"></a>加密数据块时使用的伪算法：
1.  检查 Data Lake Storage Gen1 帐户的 DEK 是否已缓存并可供使用。
    - 否则，请从永久性存储读取加密的 DEK，然后将其发送到 Key Vault 进行解密。 将解密的 DEK 缓存在内存中。 现可供使用。
2.  根据 DEK 为数据块生成唯一 BEK。
3.  使用 AES-256 加密通过 BEK 加密数据块。
4.  将加密的数据块存储在永久性存储上。

> [!NOTE] 
> 不管是在持久性媒体还是内存缓存中，DEK 始终由 MEK 进行存储加密。

## <a name="key-rotation"></a>密钥轮换

使用客户管理的密钥时，可以轮换 MEK。 若要了解如何使用客户管理的密钥设置 Data Lake Storage Gen1 帐户，请参阅[入门](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)。

### <a name="prerequisites"></a>必备组件

在设置 Data Lake Storage Gen1 帐户时已选择使用自己的密钥。 创建帐户后，无法更改此选项。 以下步骤假设你使用客户管理的密钥（即，已从 Key Vault 选择自己的密钥）。

请注意，如果使用默认的加密选项，则始终通过 Data Lake Storage Gen1 管理的密钥加密数据。 在该选项中，不能轮换密钥，因为密钥是 Data Lake Storage Gen1 管理的。

### <a name="how-to-rotate-the-mek-in-data-lake-storage-gen1"></a>如何在 Data Lake Storage Gen1 中轮换 MEK

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 浏览到 Key Vault 实例，该实例用于存储与 Data Lake Storage Gen1 帐户关联的密钥。 选择“密钥”。 

    ![Key Vault 的屏幕截图](./media/data-lake-store-encryption/keyvault.png)

3. 选择与 Data Lake Storage Gen1 帐户关联的密钥，并创建一个新版的该密钥。 请注意，Data Lake Storage Gen1 目前仅支持将密钥轮换成新版的密钥， 不支持轮换成其他密钥。

   ![“密钥”窗口的屏幕截图，突出显示“新版”](./media/data-lake-store-encryption/keynewversion.png)

4. 浏览到 Data Lake Storage Gen1 帐户，并选择“加密”。 

   ![Data Lake Storage Gen1 帐户窗口的屏幕截图，突出显示“加密”](./media/data-lake-store-encryption/select-encryption.png)

5. 系统会通过消息通知你，新版密钥已可使用。 单击“轮换密钥”即可将密钥更新为新版密钥。 

   ![Data Lake Storage Gen1 窗口的屏幕截图，突出显示消息和“轮换密钥”](./media/data-lake-store-encryption/rotatekey.png)

此操作应该在不到两分钟内完成，且密钥轮换不会造成停机。 完成此操作后，可以看到新版密钥处于使用状态。

> [!IMPORTANT]
> 密钥轮换操作完成后，旧版本的密钥不再活跃地用于加密数据。  但是，在数据的冗余副本甚至都受影响的意外失败的极少数情况下，可能会从仍使用旧密钥的备份还原数据。 若要确保在这些少数情况下数据可访问，请保留以前版本的加密密钥的副本。 有关灾难恢复规划的最佳做法，请参阅 [Data Lake Storage Gen1 数据灾难恢复指南](data-lake-store-disaster-recovery-guidance.md)。 