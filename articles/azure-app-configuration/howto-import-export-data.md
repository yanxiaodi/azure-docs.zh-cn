---
title: 导入或导出数据与 Azure 应用程序配置 |Microsoft Docs
description: 了解如何导入或导出数据到或从 Azure 应用程序配置
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 377c5088d39821e87412c517540b3190b0a14a00
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66393275"
---
# <a name="import-or-export-configuration-data"></a>导入或导出配置数据

Azure 应用程序配置支持的数据导入和导出操作。 使用这些操作可以使用你的应用程序配置存储区和代码之间的大容量和交换数据中的配置数据项目。 例如，您可以设置用于测试的一个应用配置存储区和另一个用于生产。 因此，无需两次输入数据，然后可以复制它们通过的文件之间的应用程序设置。

本文提供导入和导出数据与应用程序配置的指南。

## <a name="import-data"></a>导入数据

导入提供的配置从现有源，而不是手动输入存储在应用程序配置数据。 使用导入函数将数据迁移到应用程序配置存储区或从多个源聚合数据。 应用配置支持从 JSON、 YAML 或属性文件导入。

通过使用导入数据[Azure 门户](https://portal.azure.com)或[Azure CLI](./scripts/cli-import.md)。 在 Azure 门户中执行以下步骤：

1. 浏览到您的应用程序的配置存储区，然后选择**导入/导出**。

2. 上**导入**选项卡上，选择**源服务** > **配置文件**。

3. 选择**语言** > **文件类型**。

4. 选择**文件夹**图标，并浏览到要导入的文件。

    ![导入文件](./media/import-file.png)

5. 选择**分隔符**，并根据需要输入**前缀**要用于导入的密钥名称。

6. （可选） 选择**标签**。

7. 选择**应用**来完成导入。

    ![完成导入文件](./media/import-file-complete.png)

## <a name="export-data"></a>导出数据

导出将写入到另一个目标应用程序配置中存储的配置数据。 例如，使用导出函数，以将数据保存到使用应用程序代码嵌入在部署过程中的文件的应用程序配置存储区中。

通过使用导出数据[Azure 门户](https://portal.azure.com)或[Azure CLI](./scripts/cli-export.md)。 在 Azure 门户中执行以下步骤：

1. 浏览到您的应用程序的配置存储区，然后选择**导入/导出**。

2. 上**导出**选项卡上，选择**服务为目标** > **配置文件**。

3. （可选） 输入**前缀**，然后选择**标签**和中的时间点要导出的密钥。

4. 选择**文件类型** > **分隔符**。

5. 选择**应用**完成导出。

    ![导出文件已完成](./media/export-file-complete.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建一个 ASP.NET Core Web 应用](./quickstart-aspnet-core-app.md)  
