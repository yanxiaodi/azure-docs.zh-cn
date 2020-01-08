---
title: Microsoft Azure Data Box 磁盘快速入门 | Microsoft Docs
description: 使用本快速入门在 Azure 门户中快速部署 Azure Data Box 磁盘
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: quickstart
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
Customer intent: As an IT admin, I need to quickly deploy Data Box Disk so as to import data into Azure.
ms.openlocfilehash: b07adc955b7904c6e6a3278480cec5a2be91ec35
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/05/2019
ms.locfileid: "70232909"
---
::: zone target="docs"

# <a name="quickstart-deploy-azure-data-box-disk-using-the-azure-portal"></a>快速入门：使用 Azure 门户部署 Azure Data Box Disk

::: zone-end

::: zone target="chromeless"

## <a name="get-started-with-azure-data-box-disk"></a>Azure Data Box Disk 入门 

::: zone-end

::: zone target="docs"

本快速入门介绍如何使用 Azure 门户部署 Azure Data Box 磁盘。 这些步骤包括快速创建订单、接收磁盘、打开包装、建立连接以及将数据复制到磁盘，以便上传到 Azure。

有关详细的分步部署和跟踪说明，请转到[教程：订购 Azure Data Box Disk](data-box-disk-deploy-ordered.md)。 

如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

::: zone-end

::: zone target="chromeless"

本指南将指导你完成在 Azure 门户中使用 Azure Data Box Disk 的步骤。 本指南可帮助解答以下问题。

::: zone-end

::: zone target="docs"

## <a name="prerequisites"></a>先决条件

开始之前：

- 确保为 Azure Data Box 服务启用了订阅。 若要为此服务启用订阅，请[注册服务](https://aka.ms/azuredataboxfromdiskdocs)。

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://aka.ms/azuredataboxfromdiskdocs](https://aka.ms/azuredataboxfromdiskdocs) 中登录 Azure 门户。

::: zone-end

::: zone target="chromeless"

> [!div class="checklist"]
>
> - **检查先决条件**：检查磁盘和电缆的数量、操作系统和其他软件。
> - **连接和解锁**：连接设备并解锁磁盘以复制数据。
> - **将数据复制到磁盘并进行验证**：将数据复制到磁盘上预先创建的文件夹中。
> - **退回设备**：将磁盘退回 Azure 数据中心，数据将那里上传到你的存储帐户。
> - **在 Azure 中验证数据**：验证数据是否已上传到存储帐户，然后再将其从源数据服务器中删除。

::: zone-end


::: zone target="docs"

## <a name="order"></a>顺序

此步骤大约需要 5 分钟。

1. 在 Azure 门户中创建新的 Azure Data Box 资源。 
2. 选择为此服务启用的订阅，然后选择“导入”作为传输类型。  提供数据所在的**来源国家/地区**，以及数据要传输到的 **Azure 目标区域**。
3. 选择“Data Box 磁盘”。  解决方案的最大容量为 35 TB；如果数据大小更大，你可以创建多个磁盘订单。  
4. 输入订单详细信息和发货信息。 如果该服务在你所在的区域中可用，请提供通知电子邮件地址、查看摘要，然后创建订单。

创建订单后，Microsoft 会准备磁盘发货。

## <a name="unpack"></a>打开包装

此步骤大约需要 5 分钟。

Data Box 磁盘通过 UPS Express Box 寄送。 请打开包装，检查其中是否包括：

- 1 到 5 个以气泡膜包装的 USB 磁盘。
- 每个磁盘有一根连接线。
- 用于退件的发货标签。

## <a name="connect-and-unlock"></a>连接和解锁

此步骤大约需要 5 分钟。

1. 使用随附的电缆将磁盘连接到运行受支持版本的 Windows/Linux 计算机。 有关支持的 OS 版本的详细信息，请转到 [Azure Data Box 磁盘系统要求](data-box-disk-system-requirements.md)。 
2. 解锁磁盘：

    1. 在 Azure 门户中，转到“常规”>“设备详细信息”并获取支持密钥。 
    2. 在计算机上下载并提取用于将数据复制到磁盘的特定于操作系统的 Data Box Disk 解锁工具。 
    3. 运行 Data Box Disk 解锁工具并提供密钥。 每次重新插入磁盘时，请再次运行解锁工具并提供密钥。 **请勿使用 BitLocker 对话框或 BitLocker 密钥来解锁磁盘。** 有关如何解锁磁盘的更多信息，请访问[在 Windows 客户端上解锁磁盘](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client)或[在 Linux 客户端上解锁磁盘](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client)。
    4. 工具会显示分配给磁盘的驱动器号。 请记下磁盘驱动器号， 后续步骤中需要用到。

## <a name="copy-data-and-validate"></a>复制数据并进行验证

完成此操作所需的时间取决于数据大小。

1. 驱动器包含 *PageBlob*、*BlockBlob*、*AzureFile*、*ManagedDisk* 和 *DataBoxDiskImport* 文件夹。 通过拖放操作，将需要作为块 Blob 导入的数据复制到 *BlockBlob* 文件夹中。 同理，将 VHD/VHDX 等数据拖放到 *PageBlob* 文件夹中，将相应的数据拖放到 *AzureFile* 文件夹中。 将需要作为托管磁盘上传的 VHD 复制到 *ManagedDisk* 下的一个文件夹中。

    在 Azure 存储帐户中，为 *BlockBlob* 和 *PageBlob* 文件夹下的每个子文件夹创建一个容器。 此时会为 *AzureFile* 下的一个子文件夹创建文件共享。

    *BlockBlob* 和 *PageBlob* 文件夹下的所有文件将复制到 Azure 存储帐户下的默认容器 `$root` 中。 将文件复制到 *AzureFile* 中的一个文件夹。 任何直接复制到 *AzureFile* 文件夹的文件都会失败，会作为块 Blob 上传。

    > [!NOTE]
    > - 所有容器、Blob 和文件都应符合 [Azure 命名约定](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)。 如果不遵循这些规则，则无法将数据上传到 Azure。
    > - 确保块 Blob 的文件不超过大约 4.75 TiB，页 Blob 的文件不超过大约 8 TiB，Azure 文件不超过大约 1 TiB。

2. **（可选但建议选择）** 复制完成后，强烈建议你至少运行 *DataBoxDiskImport* 文件夹中提供的 `DataBoxDiskValidation.cmd` 并选择选项 1 来验证文件。 我们还建议在时间允许的情况下使用选项 2，这样也可以生成用于验证的校验和（可能需要一定的时间，具体取决于数据大小）。 这些步骤可以最大程度降低将数据上传到 Azure 时发生故障的可能性。
3. 安全地移除驱动器。

## <a name="ship-to-azure"></a>寄送到 Azure

此步骤大约需要 5-7 分钟才能完成。

1. 将所有磁盘一起放入原始包装中。 使用随附的发货标签。 如果该标签损坏或丢失，请从门户下载。 转到“概述”，然后在命令栏中单击“下载发货标签”。  
2. 在寄件处投递密封的包裹。  

Data Box 磁盘服务将发送一封电子邮件通知，并在 Azure 门户上更新订单状态。

## <a name="verify-your-data"></a>验证数据

完成此操作所需的时间取决于数据大小。

1. 将 Data Box 磁盘连接到 Azure 数据中心网络后，数据会自动开始上传到 Azure。
2. Azure Data Box 服务会通过 Azure 门户通知数据复制已完成。
    
    1. 检查错误日志中是否包含任何错误，并采取相应的措施。
    2. 从源中删除数据之前，请确认数据已存储在存储帐户中。

## <a name="clean-up-resources"></a>清理资源

此步骤需要 2-3 分钟才能完成。

若要清理资源，可以取消 Data Box 订单并将其删除。

- 在订单尚未处理的情况下，可以在 Azure 门户中取消 Data Box 订单。 订单处理后，无法取消订单。 订单处理会不断进行，直到完成。

    若要取消订单，请转到“概况”，然后在命令栏中单击“取消”。    

- 在 Azure 门户中的状态显示为“已完成”或“已取消”后，即可删除订单。  

    若要删除订单，请转到“概况”，然后在命令栏中单击“删除”。  

## <a name="next-steps"></a>后续步骤

在本快速入门中，我们已部署 Azure Data Box 磁盘来帮助将数据导入 Azure。 若要详细了解 Azure Data Box 磁盘的管理，请转到以下教程：

> [!div class="nextstepaction"]
> [使用 Azure 门户管理 Data Box 磁盘](data-box-portal-ui-admin.md)

::: zone-end
