---
title: 从 Azure 数据工厂复制活动调用存储过程 | Microsoft Docs
description: 了解如何从 Azure 数据工厂复制活动调用 Azure SQL 数据库或 SQL Server 中的存储过程。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 35e9347039a7b9939ab4d2719f9738429dec168c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60824246"
---
# <a name="invoke-stored-procedure-from-copy-activity-in-azure-data-factory"></a>从 Azure 数据工厂中的复制活动调用存储过程
> [!NOTE]
> 本文适用于数据工厂版本 1。 如果使用当前版本数据工厂服务，请参阅[在数据工厂中使用存储过程活动转换数据](../transform-data-using-stored-procedure.md)。


将数据复制到 [SQL Server](data-factory-sqlserver-connector.md) 或 [Azure SQL 数据库](data-factory-azure-sql-connector.md)中时，可以将复制活动中的 **SqlSink** 配置为调用存储过程。 将数据插入到目标表前，可能需要使用存储过程执行任何其他处理（合并列、查找值、插入到多个表，等等）。 此功能利用[表值参数](https://msdn.microsoft.com/library/bb675163.aspx)。 

以下示例演示如何从数据工厂管道（复制活动）调用 SQL Server 数据库中的存储过程：  

## <a name="output-dataset-json"></a>输出数据集 JSON
在输出数据集 JSON 中，将 **type** 设置为：**SqlServerTable**。 将它设置为 **AzureSqlTable** 可配合 Azure SQL 数据库使用。 **tableName** 属性的值必须与存储过程的第一个参数的名称匹配。  

```json
{
  "name": "SqlOutput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlLinkedService",
    "typeProperties": {
      "tableName": "Marketing"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

## <a name="sqlsink-section-in-copy-activity-json"></a>复制活动 JSON 中的 SqlSink 节
如下所示，在复制活动 JSON 中定义 **SqlSink** 节。 要在将数据插入接收器/目标数据库时调用存储过程，请为 **SqlWriterStoredProcedureName** 和 **SqlWriterTableType** 属性指定值。 有关这些属性的说明，请参阅[“SQL Server 连接器”一文中的 SqlSink 部分](data-factory-sqlserver-connector.md#sqlsink)。

```json
"sink":
{
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing", 
    "storedProcedureParameters":
            {
                "stringData": 
                {
                    "value": "str1"     
                }
            }
}
```

## <a name="stored-procedure-definition"></a>存储过程定义 
在数据库中，使用与 **SqlWriterStoredProcedureName** 相同的名称定义存储过程。 该存储过程处理来自源数据存储的输入数据，并将数据插入到目标数据库的表中。 存储过程的第一个参数的名称必须与数据集 JSON（市场营销部）中定义的 tableName 匹配。

```sql
CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
AS
BEGIN
    DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
    INSERT [dbo].[Marketing](ProfileID, State)
    SELECT * FROM @Marketing
END
```

## <a name="table-type-definition"></a>表类型定义
在数据库中，使用与 **SqlWriterTableType** 相同的名称定义表类型。 表类型的架构必须与输入数据集的架构匹配。

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL
)
```

## <a name="next-steps"></a>后续步骤
有关完整的 JSON 示例，请查看以下连接器文章： 

- [Azure SQL 数据库](data-factory-azure-sql-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
