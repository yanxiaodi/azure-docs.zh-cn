---
title: 使用 Azure PowerShell 配置文件上传 | Microsoft Docs
description: 如何使用 Azure PowerShell cmdlet 配置 IoT 中心，以便从连接的设备上传文件。 包括有关配置目标 Azure 存储帐户的信息。
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robinsh
ms.openlocfilehash: c8fc0393e0961b46fbb8031d735f27e9ad785031
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60318425"
---
# <a name="configure-iot-hub-file-uploads-using-powershell"></a>使用 PowerShell 配置 IoT 中心文件上传

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

要使用 [IoT 中心的文件上传功能](iot-hub-devguide-file-upload.md)，必须先将 Azure 存储帐户与 IoT 中心关联。 可以使用现有存储帐户，也可以创建新的存储帐户。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

要完成本教程，需要以下各项：

* 有效的 Azure 帐户。 如果没有帐户，只需花费几分钟就能创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

* [Azure PowerShell cmdlet](https://docs.microsoft.com/powershell/azure/install-Az-ps)。

* Azure IoT 中心。 如果没有 IoT 中心，可以使用 [New-AzIoTHub cmdlet](https://docs.microsoft.com/powershell/module/az.iothub/new-aziothub) 创建一个，或者使用门户[创建一个 IoT 中心](iot-hub-create-through-portal.md)。

* 一个 Azure 存储帐户。 如果没有 Azure 存储帐户，可以使用 [Azure 存储 PowerShell cmdlet](https://docs.microsoft.com/powershell/module/az.storage/) 创建一个，或使用门户[创建存储帐户](../storage/common/storage-create-storage-account.md)

## <a name="sign-in-and-set-your-azure-account"></a>登录并设置 Azure 帐户

登录到 Azure 帐户，并选择订阅。

1. 在 PowerShell 提示符下，运行 **Connect-AzAccount**：

    ```powershell
    Connect-AzAccount
    ```

2. 如果有多个 Azure 订阅，则访问 Azure 即有权访问与凭据关联的所有 Azure 订阅。 使用以下命令，列出可供使用的 Azure 订阅：

    ```powershell
    Get-AzSubscription
    ```

    使用以下命令，选择想要用于运行命令以管理 IoT 中心的订阅。 可使用上一命令输出中的订阅名称或 ID：

    ```powershell
    Select-AzSubscription `
        -SubscriptionName "{your subscription name}"
    ```

## <a name="retrieve-your-storage-account-details"></a>检索存储帐户详细信息

以下步骤假设已使用 **Resource Manager** 部署模型而不**经典**部署模型创建了存储帐户。

若要从设备配置文件上传，需要 Azure 存储帐户的连接字符串。 存储帐户必须与 IoT 中心位于同一订阅中。 还需要存储帐户中 Blob 容器的名称。 使用以下命令检索存储帐户密钥：

```powershell
Get-AzStorageAccountKey `
  -Name {your storage account name} `
  -ResourceGroupName {your storage account resource group}
```

记下 **key1** 存储帐户密钥值。 在后续步骤中需要用到它。

可将现有的 Blob 容器用于文件上传，或新建一个容器：

* 若要列出存储帐户中的现有 Blob 容器，请使用以下命令：

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    Get-AzStorageContainer -Context $ctx
    ```

* 若要在存储帐户中创建 Blob 容器，请使用以下命令：

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    New-AzStorageContainer `
        -Name {your new container name} `
        -Permission Off `
        -Context $ctx
    ```

## <a name="configure-your-iot-hub"></a>配置 IoT 中心

现在可以使用存储帐户详细信息配置 IoT 中心以[将文件上传到 IoT 中心](iot-hub-devguide-file-upload.md)。

配置需要以下值：

* **存储容器**：当前 Azure 订阅中要与 IoT 中心关联的 Azure 存储帐户中的 Blob 容器。 检索在上一部分中必要的存储帐户信息。 IoT 中心会自动生成对此 Blob 容器具有写入权限的 SAS URI，以供设备上传文件时使用。

* **接收已上传文件的通知**：启用或禁用文件上传通知。

* **SAS TTL**：此设置是 IoT 中心返回给设备的 SAS URI 生存时间。 默认设置为一小时。

* **文件通知设置默认 TTL**：文件上传通知到期前的生存时间。 默认设置为一天。

* **文件通知最大传送数**：IoT 中心将尝试传送文件上传通知的次数。 默认设置为 10。

使用以下 PowerShell cmdlet 在 IoT 中心内配置上传文件设置：

```powershell
Set-AzIotHub `
    -ResourceGroupName "{your iot hub resource group}" `
    -Name "{your iot hub name}" `
    -FileUploadNotificationTtl "01:00:00" `
    -FileUploadSasUriTtl "01:00:00" `
    -EnableFileUploadNotifications $true `
    -FileUploadStorageConnectionString "DefaultEndpointsProtocol=https;AccountName={your storage account name};AccountKey={your storage account key};EndpointSuffix=core.windows.net" `
    -FileUploadContainerName "{your blob container name}" `
    -FileUploadNotificationMaxDeliveryCount 10
```

## <a name="next-steps"></a>后续步骤

有关 IoT 中心文件上传功能的详细信息，请参阅[从设备上传文件](iot-hub-devguide-file-upload.md)。

若要了解有关如何管理 Azure IoT 中心的详细信息，请参阅以下链接：

* [批量管理 IoT 设备](iot-hub-bulk-identity-mgmt.md)
* [IoT 中心指标](iot-hub-metrics.md)
* [操作监视](iot-hub-operations-monitoring.md)

若要进一步探索 IoT 中心的功能，请参阅：

* [IoT 中心开发人员指南](iot-hub-devguide.md)
* [使用 Azure IoT Edge 将 AI 部署到边缘设备](../iot-edge/tutorial-simulate-device-linux.md)
* [从根本上保护 IoT 解决方案](../iot-fundamentals/iot-security-ground-up.md)
