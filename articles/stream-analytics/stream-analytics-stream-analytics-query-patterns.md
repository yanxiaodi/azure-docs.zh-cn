---
title: Azure 流分析中的常见查询模式
description: 本文介绍了在 Azure 流分析作业中很有用的多个常见查询模式和设计。
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/16/2019
ms.openlocfilehash: 729385a2ce9feb6e69f9be29c2175b403093be3f
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2019
ms.locfileid: "68413361"
---
# <a name="query-examples-for-common-stream-analytics-usage-patterns"></a>常用流分析使用模式的查询示例

Azure 流分析中的查询以类似 SQL 的查询语言表示。 这些语言构造记录在[流分析查询语言参考](/stream-analytics-query/stream-analytics-query-language-reference)指南中。 

查询设计可以表达简单的传递逻辑, 以将事件数据从一个输入流移动到一个输出数据存储, 或者可以执行丰富的模式匹配和临时分析, 以便在不同时间窗口上计算聚合,[如使用使用流分析](stream-analytics-build-an-iot-solution-using-stream-analytics.md)指南。 您可以联接多个输入中的数据以合并流式处理事件, 并且可以对静态引用数据执行查找以丰富事件值。 你还可以将数据写入多个输出。

本文概述了基于实际方案的多个常见查询模式的解决方案。

## <a name="work-with-complex-data-types-in-json-and-avro"></a>使用 JSON 和 AVRO 中的复杂数据类型

Azure 流分析支持处理采用 CSV、JSON 和 Avro 数据格式的事件。

JSON 和 Avro 都可能包含嵌套对象（记录）或数组等复杂类型。 有关使用这些复杂数据类型的详细信息, 请参阅[分析 JSON 和 AVRO 数据](stream-analytics-parsing-json.md)一文。

## <a name="query-example-convert-data-types"></a>查询示例：转换数据类型

**说明**：定义输入流中的属性类型。 例如, 汽车权重作为字符串传入输入流, 需要转换为**INT**才能执行**SUM**。

**输入**：

| 制造商 | Time | 权重 |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |"1000" |
| Honda |2015-01-01T00:00:02.0000000Z |"2000" |

**输出**：

| 制造商 | 重量 |
| --- | --- |
| Honda |3000 |

**解决方案**；

```SQL
    SELECT
        Make,
        SUM(CAST(Weight AS BIGINT)) AS Weight
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
```

**说明**：在“重量”字段中使用 CAST 语句来指定它的数据类型。 请参阅[数据类型（Azure 流分析）](/stream-analytics-query/data-types-azure-stream-analytics)中支持的数据类型列表。

## <a name="query-example-use-likenot-like-to-do-pattern-matching"></a>查询示例：使用 LIKE/NOT LIKE 进行模式匹配

**说明**：检查事件上的字段值是否与特定的模式相匹配。
例如，检查返回以 A 开头并以 9 结尾的车牌的结果。

**输入**：

| 制造商 | LicensePlate | Time |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**输出**：

| 制造商 | LicensePlate | Time |
| --- | --- | --- |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**解决方案**；

```SQL
    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LicensePlate LIKE 'A%9'
```

**说明**：使用 LIKE 语句检查 LicensePlate 字段的值。 它应以字母 A 开头, 并具有零个或多个字符的任何字符串, 然后以数字9结尾。 

## <a name="query-example-specify-logic-for-different-casesvalues-case-statements"></a>查询示例：指定不同案例/值的逻辑（CASE 语句）

**说明**：根据某些特定条件对字段进行各种计算。 例如，同一制造商的汽车通过数量为 1 时，为这种特殊情况提供一个字符串说明。

**输入**：

| 制造商 | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**输出**：

| CarsPassed | Time |
| --- | --- |
| 1 辆 Honda |2015-01-01T00:00:10.0000000Z |
| 2 辆 Toyota |2015-01-01T00:00:10.0000000Z |

**解决方案**；

```SQL
    SELECT
        CASE
            WHEN COUNT(*) = 1 THEN CONCAT('1 ', Make)
            ELSE CONCAT(CAST(COUNT(*) AS NVARCHAR(MAX)), ' ', Make, 's')
        END AS CarsPassed,
        System.TimeStamp() AS AsaTime
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
```

**说明**：CASE 表达式将表达式与一组简单表达式进行比较以确定结果。 在此示例中，计数为 1 的车返回的是与计数不为 1 的车不同的字符串说明。

## <a name="query-example-send-data-to-multiple-outputs"></a>查询示例：将数据发送到多个输出

**说明**：从单个作业中将数据发送到多个输出目标。 例如，分析基于阈值的警报数据，并将所有事件保存到 blob 存储。

**输入**：

| 制造商 | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**输出 1**：

| 制造商 | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**输出 2**：

| 制造商 | Time | Count |
| --- | --- | --- |
| Toyota |2015-01-01T00:00:10.0000000Z |3 |

**解决方案**；

```SQL
    SELECT
        *
    INTO
        ArchiveOutput
    FROM
        Input TIMESTAMP BY Time

    SELECT
        Make,
        System.TimeStamp() AS AsaTime,
        COUNT(*) AS [Count]
    INTO
        AlertOutput
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
    HAVING
        [Count] >= 3
```

**说明**：INTO 子句告知流分析哪一个输出可通过此语句写入数据。 第一个查询是收到的数据传递到名为**ArchiveOutput**的输出。 第二个查询执行一些简单的聚合和筛选, 并将结果发送到下游警报系统**AlertOutput**。

请注意，还可重复使用多个输出语句中的公用表表达式 (CTE) 结果（例如 WITH 语句）。 此选项可提供额外权益，即在输入源打开较少的读取器。

例如： 

```SQL
    WITH AllRedCars AS (
        SELECT
            *
        FROM
            Input TIMESTAMP BY Time
        WHERE
            Color = 'red'
    )
    SELECT * INTO HondaOutput FROM AllRedCars WHERE Make = 'Honda'
    SELECT * INTO ToyotaOutput FROM AllRedCars WHERE Make = 'Toyota'
```

## <a name="query-example-count-unique-values"></a>查询示例：对唯一值进行计数

**说明**：计算时间范围内流中显示的唯一字段值数。 例如，在 2 秒的时间范围内，通过收费站的同一制造商的汽车数量是多少？

**输入**：

| 制造商 | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**输出：**

| CountMake | Time |
| --- | --- |
| 2 |2015-01-01T00:00:02.000Z |
| 1 |2015-01-01T00:00:04.000Z |

**解决方案：**

```SQL
SELECT
     COUNT(DISTINCT Make) AS CountMake,
     System.TIMESTAMP() AS AsaTIME
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
```


**说明：** 
COUNT(DISTINCT Make) 返回时间范围内的“制造商”列的非重复值数目。

## <a name="query-example-determine-if-a-value-has-changed"></a>查询示例：确定某个值是否已更改

**说明**：可通过查看前一个值来确定它与当前的值是否相同。 例如：在收费路段前一辆汽车与当前汽车的制造商是否相同？

**输入**：

| 制造商 | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |

**输出**：

| 制造商 | 时间 |
| --- | --- |
| Toyota |2015-01-01T00:00:02.0000000Z |

**解决方案**；

```SQL
    SELECT
        Make,
        Time
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make
```

**说明**：使用 LAG 来查看后退一个事件之后的输入流，并获得“制造商”字段的值。 然后，将它与当前事件的“制造商”字段进行比较，如果二者不同，则输出该事件。

## <a name="query-example-find-the-first-event-in-a-window"></a>查询示例：查找时间范围内的第一个事件

**说明**：查找每 10 分钟时间间隔内的第一辆汽车。

**输入**：

| LicensePlate | 制造商 | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**输出**：

| LicensePlate | 制造商 | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |

**解决方案**；

```SQL
    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) = 1
```

现在，我们来变一下这个问题，查找每 10 分钟时间间隔内特定制造商的第一辆汽车。

| LicensePlate | 制造商 | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**解决方案**；

```SQL
    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) OVER (PARTITION BY Make) = 1
```

## <a name="query-example-find-the-last-event-in-a-window"></a>查询示例：查找时间范围内的最后一个事件

**说明**：查找每 10 分钟时间间隔内的最后一辆汽车。

**输入**：

| LicensePlate | 制造商 | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**输出**：

| LicensePlate | 制造商 | Time |
| --- | --- | --- |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**解决方案**；

```SQL
    WITH LastInWindow AS
    (
        SELECT 
            MAX(Time) AS LastEventTime
        FROM 
            Input TIMESTAMP BY Time
        GROUP BY 
            TumblingWindow(minute, 10)
    )
    SELECT 
        Input.LicensePlate,
        Input.Make,
        Input.Time
    FROM
        Input TIMESTAMP BY Time 
        INNER JOIN LastInWindow
        ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
        AND Input.Time = LastInWindow.LastEventTime
```

**说明**：查询中包含两个步骤。 第一个步骤是在 10 分钟的时间范围内查找最新的时间戳。 第二个步骤是将第一个查询的结果与原始流联接，查找每个时间范围内与最后一个时间戳相匹配的事件。 

## <a name="query-example-locate-correlated-events-in-a-stream"></a>查询示例：在流中找到相关事件

**说明**：查找流中的相关事件。 例如，两辆同一制造商的汽车是否在 90 秒内先后进入收费路段？

**输入**：

| 制造商 | LicensePlate | Time |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Honda |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Toyota |DEF-987 |2015-01-01T00:00:03.0000000Z |
| Honda |GHI-345 |2015-01-01T00:00:04.0000000Z |

**输出**：

| 制造商 | Time | CurrentCarLicensePlate | FirstCarLicensePlate | FirstCarTime |
| --- | --- | --- | --- | --- |
| Honda |2015-01-01T00:00:02.0000000Z |AAA-999 |ABC-123 |2015-01-01T00:00:01.0000000Z |

**解决方案**；

```SQL
    SELECT
        Make,
        Time,
        LicensePlate AS CurrentCarLicensePlate,
        LAG(LicensePlate, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarLicensePlate,
        LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarTime
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make
```

**说明**：使用 LAG 来查看后退一个事件之后的输入流，并获得“制造商”字段的值。 将它与当前事件的“制造商”字段进行比较，如果二者相同，则输出该事件。 还可使用 LAG 获取前一辆汽车的数据。

## <a name="query-example-detect-the-duration-between-events"></a>查询示例：检测事件之间的持续时间

**说明**：查找给定事件的持续时间。 例如：给定一个 Web 点击流，确定某项功能花费的时间。

**输入**：  

| 用户 | 功能 | 事件 | Time |
| --- | --- | --- | --- |
| user@location.com |RightMenu |Start |2015-01-01T00:00:01.0000000Z |
| user@location.com |RightMenu |结束 |2015-01-01T00:00:08.0000000Z |

**输出**：  

| 用户 | 功能 | Duration |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**解决方案**；

```SQL
    SELECT
        [user],
    feature,
    DATEDIFF(
        second,
        LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'),
        Time) as duration
    FROM input TIMESTAMP BY Time
    WHERE
        Event = 'end'
```

**说明**：使用 LAST 函数检索上次事件类型为“开始”时的时间值。 LAST 函数使用 PARTITION BY [user] 指示结果应按唯一用户计算。 该查询在“开始”和“停止”事件之间有 1 小时的最大时差阈值，但也可按需配置 (LIMIT DURATION(hour, 1)。

## <a name="query-example-detect-the-duration-of-a-condition"></a>查询示例：检测某个条件的持续时间
**说明**：查看某个条件的持续时间。
例如，假设某个 Bug 导致所有车的重量不正确（超出 20000 磅），因此必须计算该 Bug 的持续时间。

**输入**：

| 制造商 | Time | 权重 |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |2000 |
| Toyota |2015-01-01T00:00:02.0000000Z |25000 |
| Honda |2015-01-01T00:00:03.0000000Z |26000 |
| Toyota |2015-01-01T00:00:04.0000000Z |25000 |
| Honda |2015-01-01T00:00:05.0000000Z |26000 |
| Toyota |2015-01-01T00:00:06.0000000Z |25000 |
| Honda |2015-01-01T00:00:07.0000000Z |26000 |
| Toyota |2015-01-01T00:00:08.0000000Z |2000 |

**输出**：

| StartFault | EndFault |
| --- | --- |
| 2015-01-01T00:00:02.000Z |2015-01-01T00:00:07.000Z |

**解决方案**；

```SQL
    WITH SelectPreviousEvent AS
    (
    SELECT
    *,
        LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previousTime,
        LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previousWeight
    FROM input TIMESTAMP BY [time]
    )

    SELECT 
        LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previousWeight < 20000 ) [StartFault],
        previousTime [EndFault]
    FROM SelectPreviousEvent
    WHERE
        [weight] < 20000
        AND previousWeight > 20000
```

**说明**：使用 LAG 查看 24 小时内的输入流并查找因重量 < 20000 而持续的 StartFault 和 StopFault 实例。

## <a name="query-example-fill-missing-values"></a>查询示例：填充缺失值

**说明**：对于值缺失的事件流，以固定的间隔生成事件流。 例如，每间隔 5 秒生成一个事件，报告最新发现的数据点。

**输入**：

| t | value |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**输出（前 10 行）** ：

| windowend | lastevent.t | lastevent.value |
| --- | --- | --- |
| 2014-01-01T14:01:00.000Z |2014-01-01T14:01:00.000Z |1 |
| 2014-01-01T14:01:05.000Z |2014-01-01T14:01:05.000Z |2 |
| 2014-01-01T14:01:10.000Z |2014-01-01T14:01:10.000Z |3 |
| 2014-01-01T14:01:15.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:20.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:25.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:30.000Z |2014-01-01T14:01:30.000Z |5 |
| 2014-01-01T14:01:35.000Z |2014-01-01T14:01:35.000Z |6 |
| 2014-01-01T14:01:40.000Z |2014-01-01T14:01:35.000Z |6 |
| 2014-01-01T14:01:45.000Z |2014-01-01T14:01:35.000Z |6 |

**解决方案**；

```SQL
    SELECT
        System.Timestamp() AS windowEnd,
        TopOne() OVER (ORDER BY t DESC) AS lastEvent
    FROM
        input TIMESTAMP BY t
    GROUP BY HOPPINGWINDOW(second, 300, 5)
```

**说明**：此查询每隔 5 秒生成一个事件，并输出上次收到的最后一个事件。 [跳跃窗口](/stream-analytics-query/hopping-window-azure-stream-analytics)持续时间决定了查询在查找最新事件时会回溯多久（在本例中为 300 秒）。


## <a name="query-example-correlate-two-event-types-within-the-same-stream"></a>查询示例：在同一流中关联两个事件类型

**说明**：有时需要基于某个特定时间范围内发生的多个事件类型生成警报。 例如，在家用烤箱的 IoT 方案中，必须在风扇温度小于 40 且在过去 3 分钟内最大功率小于 10 时生成警报。

**输入**：

| 时间 | deviceId | sensorName | value |
| --- | --- | --- | --- |
| "2018-01-01T16:01:00" | "Oven1" | "temp" |120 |
| "2018-01-01T16:01:00" | "Oven1" | "power" |15 |
| "2018-01-01T16:02:00" | "Oven1" | "temp" |100 |
| "2018-01-01T16:02:00" | "Oven1" | "power" |15 |
| "2018-01-01T16:03:00" | "Oven1" | "temp" |70 |
| "2018-01-01T16:03:00" | "Oven1" | "power" |15 |
| "2018-01-01T16:04:00" | "Oven1" | "temp" |50 |
| "2018-01-01T16:04:00" | "Oven1" | "power" |15 |
| "2018-01-01T16:05:00" | "Oven1" | "temp" |30 |
| "2018-01-01T16:05:00" | "Oven1" | "power" |8 |
| "2018-01-01T16:06:00" | "Oven1" | "temp" |20 |
| "2018-01-01T16:06:00" | "Oven1" | "power" |8 |
| "2018-01-01T16:07:00" | "Oven1" | "temp" |20 |
| "2018-01-01T16:07:00" | "Oven1" | "power" |8 |
| "2018-01-01T16:08:00" | "Oven1" | "temp" |20 |
| "2018-01-01T16:08:00" | "Oven1" | "power" |8 |

**输出**：

| eventTime | deviceId | temp | alertMessage | maxPowerDuringLast3mins |
| --- | --- | --- | --- | --- | 
| "2018-01-01T16:05:00" | "Oven1" |30 | “加热元件短路” |15 |
| "2018-01-01T16:06:00" | "Oven1" |20 | “加热元件短路” |15 |
| "2018-01-01T16:07:00" | "Oven1" |20 | “加热元件短路” |15 |

**解决方案**；

```SQL
WITH max_power_during_last_3_mins AS (
    SELECT 
        System.TimeStamp() AS windowTime,
        deviceId,
        max(value) as maxPower
    FROM
        input TIMESTAMP BY t
    WHERE 
        sensorName = 'power' 
    GROUP BY 
        deviceId, 
        SlidingWindow(minute, 3) 
)

SELECT 
    t1.t AS eventTime,
    t1.deviceId, 
    t1.value AS temp,
    'Short circuit heating elements' as alertMessage,
    t2.maxPower AS maxPowerDuringLast3mins
    
INTO resultsr

FROM input t1 TIMESTAMP BY t
JOIN max_power_during_last_3_mins t2
    ON t1.deviceId = t2.deviceId 
    AND t1.t = t2.windowTime
    AND DATEDIFF(minute,t1,t2) between 0 and 3
    
WHERE
    t1.sensorName = 'temp'
    AND t1.value <= 40
    AND t2.maxPower > 10
```

**说明**：第一个查询 `max_power_during_last_3_mins` 使用[滑动窗口](/stream-analytics-query/sliding-window-azure-stream-analytics)查找在过去 3 分钟内每个设备的功率传感器最大值。 将第二个查询联接到第一个查询，以便在与当前事件有关的最近窗口中查找功率值。 然后，假如满足条件，将为设备生成警报。

## <a name="query-example-process-events-independent-of-device-clock-skew-substreams"></a>查询示例：处理与设备时钟偏差无关的事件（子流）

**说明**：由于事件生成器之间的时钟偏差、分区之间的时钟偏差或网络延迟，事件可能会迟到或不按顺序到达。 在以下示例中, TollID 2 的设备时钟在 TollID 1 后5秒, TollID 3 的设备时钟为 TollID 1 后面的十秒。 

**输入**：

| LicensePlate | 制造商 | Time | TollID |
| --- | --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:01.0000000Z | 1 |
| YHN 6970 |Toyota |2015-07-27T00:00:05.0000000Z | 1 |
| QYF 9358 |Honda |2015-07-27T00:00:01.0000000Z | 2 |
| GXF 9462 |BMW |2015-07-27T00:00:04.0000000Z | 2 |
| VFE 1616 |Toyota |2015-07-27T00:00:10.0000000Z | 1 |
| RMV 8282 |Honda |2015-07-27T00:00:03.0000000Z | 3 |
| MDR 6128 |BMW |2015-07-27T00:00:11.0000000Z | 2 |
| YZK 5704 |Ford |2015-07-27T00:00:07.0000000Z | 3 |

**输出**：

| TollID | Count |
| --- | --- |
| 1 | 2 |
| 2 | 2 |
| 1 | 1 |
| 3 | 1 |
| 2 | 1 |
| 3 | 1 |

**解决方案**；

```SQL
SELECT
      TollId,
      COUNT(*) AS Count
FROM input
      TIMESTAMP BY Time OVER TollId
GROUP BY TUMBLINGWINDOW(second, 5), TollId
```

**说明**：[TIMESTAMP BY OVER](/stream-analytics-query/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering) 子句分别使用子流来查看每个设备时间线。 每个 TollID 的输出事件都是在计算时生成的，这意味着事件按照每个 TollID 的顺序排列，而不是像所有设备都在同一个时钟上那样重新排序。

## <a name="query-example-remove-duplicate-events-in-a-window"></a>查询示例：删除时间范围内的重复事件

**说明**：执行某项操作（例如计算给定时间范围内事件的平均值）时，应筛选出重复事件。 在下面的示例中, 第二个事件是第一个事件的副本。

**输入**：  

| DeviceId | Time | 特性 | 值 |
| --- | --- | --- | --- |
| 1 |2018-07-27T00:00:01.0000000Z |温度 |50 |
| 1 |2018-07-27T00:00:01.0000000Z |温度 |50 |
| 2 |2018-07-27T00:00:01.0000000Z |温度 |40 |
| 1 |2018-07-27T00:00:05.0000000Z |温度 |60 |
| 2 |2018-07-27T00:00:05.0000000Z |温度 |50 |
| 1 |2018-07-27T00:00:10.0000000Z |温度 |100 |

**输出**：  

| AverageValue | DeviceId |
| --- | --- |
| 70 | 1 |
|45 | 2 |

**解决方案**；

```SQL
With Temp AS (
    SELECT
        COUNT(DISTINCT Time) AS CountTime,
        Value,
        DeviceId
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Value,
        DeviceId,
        SYSTEM.TIMESTAMP()
)

SELECT
    AVG(Value) AS AverageValue, DeviceId
INTO Output
FROM Temp
GROUP BY DeviceId,TumblingWindow(minute, 5)
```

**说明**：[COUNT(DISTINCT Time)](/stream-analytics-query/count-azure-stream-analytics) 返回时间范围内的“时间”列的非重复值数目。 然后，你可以使用此步骤的输出按设备计算平均值，只需去掉重复值即可。

## <a name="geofencing-and-geospatial-queries"></a>地理围栏和地理空间查询
Azure 流分析提供内置的地理空间功能, 这些功能可用于实现各种方案, 如汽油管理、联系、共享、连接汽车和资产跟踪。 地理空间数据可引入为 GeoJSON 或 WKT 格式, 作为事件流或引用数据的一部分。 有关详细信息, 请参阅[地理围栏和地理空间聚合方案和 Azure 流分析](geospatial-scenarios.md)一文。

## <a name="language-extensibility-through-javascript-and-c"></a>通过 JavaScript 和C#
可以用 JavaScript 或C#语言编写的自定义函数扩展 Azure Stream Ananlytics query langugae。 有关详细信息, 请参阅 foolowing 文章:
* [Azure 流分析 JavaScript 用户定义的函数](stream-analytics-javascript-user-defined-functions.md)
* [Azure 流分析 JavaScript 用户定义的聚合](stream-analytics-javascript-user-defined-aggregates.md)
* [为 Azure 流分析边缘作业开发 .NET Standard 用户定义函数](stream-analytics-edge-csharp-udf-methods.md)

## <a name="get-help"></a>获取帮助

如需进一步的帮助，请尝试我们的 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>后续步骤
* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-real-time-fraud-detection.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

