---
title: 数据工厂函数和系统变量 | Microsoft Docs
description: 提供 Azure 数据工厂函数和系统变量的列表
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 243923fba5b81ef68d6e4e560182d228e3b8ad1a
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2019
ms.locfileid: "70139746"
---
# <a name="azure-data-factory---functions-and-system-variables"></a>Azure 数据工厂 - 函数和系统变量
> [!NOTE]
> 本文适用于数据工厂版本 1。 如果使用当前版本数据工厂服务，请参阅[数据工厂中的系统变量](../control-flow-system-variables.md)。

本文提供 Azure 数据工厂支持的函数和变量的相关信息。

## <a name="data-factory-system-variables"></a>数据工厂系统变量

| 变量名 | 描述 | 对象作用域 | JSON 作用域和用例 |
| --- | --- | --- | --- |
| WindowStart |当前活动运行窗口的开始时间间隔 |活动 |<ol><li>指定数据选择查询。 请参阅[数据移动活动](data-factory-data-movement-activities.md)一文中引用的连接器文章。</li> |
| WindowEnd |当前活动运行窗口的结束时间间隔 |activity |与 WindowStart 相同。 |
| SliceStart |生成数据切片的开始时间间隔 |活动<br/>dataset |<ol><li>使用 [Azure Blob](data-factory-azure-blob-connector.md) 和[文件系统数据集](data-factory-onprem-file-system-connector.md)时指定动态文件夹路径和文件名。</li><li>指定活动输入集合中含数据工厂函数的输入依赖项。</li></ol> |
| SliceEnd |当前数据切片的时间间隔结束。 |活动<br/>dataset |与 SliceStart 相同。 |

> [!NOTE]
> 目前，数据工厂要求活动中指定的计划与输出数据集可用性中指定的计划精确匹配。 因此，WindowStart、WindowEnd、SliceStart 和 SliceEnd 始终映射到同一时间段和单个输出切片。
> 

### <a name="example-for-using-a-system-variable"></a>使用系统变量的示例
在下面的示中，SliceStart 的年、月、日和时间已提取到“folderPath”和“fileName”属性使用的不同变量中。

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```

## <a name="data-factory-functions"></a>数据工厂函数
可将数据工厂中的函数和系统变量用于以下目的：

1. 指定数据选择查询（请参阅[数据移动活动](data-factory-data-movement-activities.md)一文引用的连接器文章）。
   
   调用数据工厂函数的语法是:  **$$ \<函数 >** 用于数据选择查询以及活动和数据集中的其他属性。  
2. 指定活动输入集合中含数据工厂函数的输入依赖项。
   
    指定输入依赖项表达式不需要 $$。     

在以下示例中，JSON 文件的 **sqlReaderQuery** 属性分配到 `Text.Format` 函数返回的值。 此示例还使用名为 **WindowStart** 的系统变量表示活动运行窗口的开始时间。

```json
{
    "Type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
}
```

请参阅[自定义日期和时间格式字符串](https://msdn.microsoft.com/library/8kb3ddd4.aspx)主题，其中介绍了可用的不同格式设置选项（例如：ay 和 yyyy）。 

### <a name="functions"></a>函数
下表列出了 Azure 数据工厂中的所有函数：

| 类别 | 函数 | Parameters | 描述 |
| --- | --- | --- | --- |
| Time |AddHours(X,Y) |X：DateTime <br/><br/>Y: int |向给定时间 X 加 Y 小时。 <br/><br/>示例： `9/5/2013 12:00:00 PM + 2 hours = 9/5/2013 2:00:00 PM` |
| Time |AddMinutes(X,Y) |X：DateTime <br/><br/>Y: int |向 X 加 Y 分钟。<br/><br/>示例： `9/15/2013 12: 00:00 PM + 15 minutes = 9/15/2013 12: 15:00 PM` |
| Time |StartOfHour(X) |X：Datetime |获取由 X 小时部分表示的小时起始时间。 <br/><br/>示例： `StartOfHour of 9/15/2013 05: 10:23 PM is 9/15/2013 05: 00:00 PM` |
| Date |AddDays(X,Y) |X：DateTime<br/><br/>Y: int |向 X 加 Y 天。 <br/><br/>例如：9/15/2013 12:00:00 PM + 2 天 = 9/17/2013 12:00:00 PM。<br/><br/>也可指定 Y 为负数来减去天数。<br/><br/>示例：`9/15/2013 12:00:00 PM - 2 days = 9/13/2013 12:00:00 PM`。 |
| Date |AddMonths(X,Y) |X：DateTime<br/><br/>Y: int |向 X 加 Y 个月。<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 month = 10/15/2013 12:00:00 PM`。<br/><br/>也可指定 Y 为负数来减去月数。<br/><br/>示例：`9/15/2013 12:00:00 PM - 1 month = 8/15/2013 12:00:00 PM`。|
| Date |AddQuarters(X,Y) |X：DateTime <br/><br/>Y: int |向 X 加 Y * 3 个月。<br/><br/>示例： `9/15/2013 12:00:00 PM + 1 quarter = 12/15/2013 12:00:00 PM` |
| Date |AddWeeks(X,Y) |X：DateTime<br/><br/>Y: int |向 X 加 Y * 7 天<br/><br/>例如：9/15/2013 12:00:00 PM + 1 周 = 9/22/2013 12:00:00 PM<br/><br/>也可指定 Y 为负数来减去周数。<br/><br/>示例：`9/15/2013 12:00:00 PM - 1 week = 9/7/2013 12:00:00 PM`。 |
| Date |AddYears(X,Y) |X：DateTime<br/><br/>Y: int |向 X 加 Y 年。<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 year = 9/15/2014 12:00:00 PM`<br/><br/>也可指定 Y 为负数来减去年数。<br/><br/>示例：`9/15/2013 12:00:00 PM - 1 year = 9/15/2012 12:00:00 PM`。 |
| 日期 |Day(X) |X：DateTime |获取 X 的日期号数部分。<br/><br/>示例：`Day of 9/15/2013 12:00:00 PM is 9`。 |
| Date |DayOfWeek(X) |X：DateTime |获取 X 的星期部分。<br/><br/>示例：`DayOfWeek of 9/15/2013 12:00:00 PM is Sunday`。 |
| Date |DayOfYear(X) |X：DateTime |获取由 X 的年份部分表示的当年第几天。<br/><br/>例如：<br/>`12/1/2015: day 335 of 2015`<br/>`12/31/2015: day 365 of 2015`<br/>`12/31/2016: day 366 of 2016 (Leap Year)` |
| Date |DaysInMonth(X) |X：DateTime |获取由参数 X 的月份部分表示的当月天数。<br/><br/>示例：`DaysInMonth of 9/15/2013 are 30 since there are 30 days in the September month`。 |
| Date |EndOfDay(X) |X：DateTime |获取表示 X 的当天（日期号数部分）结束时的日期时间。<br/><br/>示例：`EndOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 11:59:59 PM`。 |
| Date |EndOfMonth(X) |X：DateTime |获取由参数 X 的月份部分表示的当月结束时间。 <br/><br/>示例： `EndOfMonth of 9/15/2013 05:10:23 PM is 9/30/2013 11:59:59 PM`（表示 9 月结束的日期时间） |
| Date |StartOfDay(X) |X：DateTime |获取由参数 X 的日期号数部分表示的当天开始时间。<br/><br/>示例：`StartOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 12:00:00 AM`。 |
| DateTime |From(X) |X：String |将字符串 X 解析成日期时间。 |
| DateTime |Ticks(X) |X：DateTime |获取参数 X 的刻度属性。一刻度等于 100 纳秒。 此属性的值表示自 0001 年 1 月 1 日午夜 12:00:00 以来已经过的刻度数。 |
| 文本 |Format(X) |X：字符串变量 |设置文本格式（使用 `\\'` 组合转义 `'` 字符）。|

> [!IMPORTANT]
> 在一个函数中使用另一函数时，无需对内部函数使用 **$$** 前缀。 例如：$$Text.Format('PartitionKey eq \\'my_pkey_filter_value\\' and RowKey ge \\'{0: yyyy-MM-dd HH:mm:ss}\\''、Time.AddHours(SliceStart, -6))。 请注意，本例未对 **Time.AddHours** 函数使用 **$$** 前缀。 

#### <a name="example"></a>示例
在以下示例中，使用 `Text.Format` 函数和 SliceStart 系统变量决定 Hive 活动的输入和输出参数。 

```json  
{
    "name": "HiveActivitySamplePipeline",
        "properties": {
    "activities": [
            {
            "name": "HiveActivitySample",
            "type": "HDInsightHive",
            "inputs": [
                    {
                    "name": "HiveSampleIn"
                    }
            ],
            "outputs": [
                    {
                    "name": "HiveSampleOut"
                }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
                    "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                    "scriptLinkedService": "StorageLinkedService",
                    "defines": {
                        "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)",
                        "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                }
            }
            }
    ]
    }
}
```

### <a name="example-2"></a>示例 2

在以下示例中，使用文本决定存储程序活动的 DateTime 参数。 Format 函数和 SliceStart 变量。 

```json
{
    "name": "SprocActivitySamplePipeline",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "usp_sample",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                    }
                },
                "outputs": [
                    {
                        "name": "sprocsampleout"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SprocActivitySample"
            }
        ],
            "start": "2016-08-02T00:00:00Z",
            "end": "2016-08-02T05:00:00Z",
        "isPaused": false
    }
}
```

### <a name="example-3"></a>示例 3
若要读取前一天（而非 SliceStart 表示的那天）的数据，请如以下示例所示使用 AddDays 函数： 

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-01-01T08:00:00",
        "end": "2017-01-01T11:00:00",
        "description": "hive activity",
        "activities": [
            {
                "name": "SampleHiveActivity",
                "inputs": [
                    {
                        "name": "MyAzureBlobInput",
                        "startTime": "Date.AddDays(SliceStart, -1)",
                        "endTime": "Date.AddDays(SliceEnd, -1)"
                    }
                ],
                "outputs": [
                    {
                        "name": "MyAzureBlobOutput"
                    }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adftutorial\\hivequery.hql",
                    "scriptLinkedService": "StorageLinkedService",
                    "defines": {
                        "Year": "$$Text.Format('{0:yyyy}',WindowsStart)",
                        "Month": "$$Text.Format('{0:MM}',WindowStart)",
                        "Day": "$$Text.Format('{0:dd}',WindowStart)"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 2,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

请参阅[自定义日期和时间格式字符串](https://msdn.microsoft.com/library/8kb3ddd4.aspx)主题，其中介绍了可用的不同格式设置选项（例如：yy 和 yyyy）。 

