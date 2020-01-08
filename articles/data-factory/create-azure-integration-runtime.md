---
title: 在 Azure 数据工厂中创建 Azure 集成运行时 | Microsoft Docs
description: 了解如何在 Azure 数据工厂中创建 Azure 集成运行时，用于复制数据并调度转换活动。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/15/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 4b166ded3dcef4a89951eb81f7f1b321f89a0e67
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66153402"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>如何创建和配置 Azure 集成运行时
集成运行时 (IR) 是 Azure 数据工厂用于在不同的网络环境之间提供数据集成功能的计算基础结构。 有关 IR 的更多信息，请参阅[集成运行时](concepts-integration-runtime.md)。

Azure IR 提供完全托管的计算，以本机执行数据移动和调度数据转换活动来计算 HDInsight 这样的服务。 它在 Azure 环境中托管，并支持连接到具有公共可访问端点的公共网络环境中的资源。

本文档介绍了如何创建和配置 Azure 集成运行时。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-azure-ir"></a>默认 Azure IR
默认情况下，每个数据工厂在后端都有 Azure IR，支持云数据存储上的操作和公共网络中的计算服务。 该 Azure IR 的位置自动解析。 如果链接服务定义中未指定 **connectVia** 属性，则会使用默认的 Azure IR。 如果想要显式定义 IR 的位置，或对不同 IR 上的活动执行进行虚拟分组以进行管理，只需显式创建 Azure IR。 

## <a name="create-azure-ir"></a>创建 Azure IR
可以使用 **Set-AzDataFactoryV2IntegrationRuntime** PowerShell cmdlet 创建集成运行时。 若要创建 Azure IR，请指定命令的名称、位置和类型。 下面的示例命令创建了位置设为“西欧”的 Azure IR。

```powershell
Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
Azure IR 的类型必须设置为**托管**。 无需指定计算详细信息，因为它在云中是完全弹性托管的。 如果要创建 Azure-SSIS IR，请指定计算详细信息，如节点大小和节点数量。 有关详细信息，请参阅[创建和配置 Azure-SSIS IR](create-azure-ssis-integration-runtime.md)。

可以使用 Set-AzDataFactoryV2IntegrationRuntime PowerShell cmdlet 来配置现有 Azure IR 以更改其位置。 有关 Azure IR 的位置的详细信息，请参阅[集成运行时简介](concepts-integration-runtime.md)。

## <a name="use-azure-ir"></a>使用 Azure IR

创建 Azure IR 后，您可以在链接的服务定义中引用它。 以下示例说明如何从 Azure 存储链接服务引用上面创建的 Azure 集成运行时：  

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": {
          "value": "DefaultEndpointsProtocol=https;AccountName=myaccountname;AccountKey=...",
          "type": "SecureString"
        }
      },
      "connectVia": {
        "referenceName": "MySampleAzureIR",
        "type": "IntegrationRuntimeReference"
      }   
    }
}

```

## <a name="next-steps"></a>后续步骤
有关如何创建其他类型的集成运行时，请参阅以下文章：

- [创建自承载集成运行时](create-self-hosted-integration-runtime.md)
- [创建 Azure-SSIS 集成运行时](create-azure-ssis-integration-runtime.md)
 
