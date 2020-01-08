---
title: 处理暂时性错误 - Azure SQL 数据库 | Microsoft Docs
description: 了解如何排查、诊断和防止 Azure SQL 数据库中的 SQL 连接错误或暂时性错误。
keywords: SQL 连接, 连接字符串, 连接问题, 暂时性错误, 连接错误
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dalechen
manager: dcscontentpm
ms.author: ninarn
ms.reviewer: carlrab
ms.date: 06/14/2019
ms.openlocfilehash: eb34395e0a9ec881c2f5e303383555fa6544369d
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71090897"
---
# <a name="working-with-sql-database-connection-issues-and-transient-errors"></a>处理 SQL 数据库连接问题和暂时性错误

本文介绍如何防止、排查、诊断和减少客户端应用程序在与 Azure SQL 数据库交互时发生的连接错误和暂时性错误。 了解如何配置重试逻辑、生成连接字符串以及调整其他连接设置。

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>暂时性错误（暂时性故障）

暂时性错误（也称为暂时性故障）的根本原因很快就能自行解决。 当 Azure 系统快速地将硬件资源转移到负载均衡更好的各种工作负荷时，偶尔会发生暂时性错误。 其中的大多数重新配置事件在 60 秒内就能完成。 在进行这种重新配置的过程中，可能会遇到 SQL 数据库的连接问题。 连接到 Azure SQL 数据库的应用程序应当构建为能预见这些暂时性错误。 为了处理这些错误，可应用程序代码中实现重试逻辑，而不是以应用程序错误的形式呈现给用户。

如果客户端程序使用 ADO.NET，系统会引发 **SqlException**，使程序知道已发生暂时性错误。 将 **Number** 属性与 [SQL 数据库客户端应用程序的 SQL 错误代码](sql-database-develop-error-messages.md)一文顶部附近的暂时性错误列表进行比较。

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-vs-command"></a>连接与命令

重试 SQL 连接或重新建立连接，具体取决于以下各项：

- **尝试连接期间发生暂时性错误**

延迟几秒钟后，重试连接。

- **在 SQL 查询命令执行期间发生暂时性错误**

不要立即重试该命令， 应在一定的延迟之后建立新的连接。 然后重试命令。

<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

## <a name="retry-logic-for-transient-errors"></a>针对暂时性错误的重试逻辑

在偶尔会遇到暂时性错误的客户端程序中包含重试逻辑可以让它变得更稳健。 如果程序通过第三方中间件与 SQL 数据库通信，请咨询供应商该中间件是否包含暂时性错误的重试逻辑。

<a id="principles-for-retry" name="principles-for-retry"></a>

### <a name="principles-for-retry"></a>重试原则

- 如果错误是暂时性的，请重试打开连接。
- 不要直接重试由于暂时性错误而失败的 SQL `SELECT` 语句。 而应建立新的连接，然后重试 `SELECT`。
- SQL `UPDATE` 语句由于暂时性错误而失败时，请先建立新的连接，然后再重试 UPDATE。 重试逻辑必须确保整个数据库事务完成，或整个事务已回滚。

### <a name="other-considerations-for-retry"></a>其他重试注意事项

- 下班后自动启动的批处理程序以及在凌晨之前完成的批处理程序在每次重试前经过较长的时间间隔。
- 用户界面程序应该解释用户会在长时间等待后放弃操作的倾向。 解决方案不得每隔几秒钟重试，因为该策略可能会使系统填满请求。

### <a name="interval-increase-between-retries"></a>增大重试间隔

我们建议在第一次重试前等待 5 秒。 如果在少于 5 秒的延迟后重试，云服务有超载的风险。 对于后续的每次重试，延迟应以指数级增大，最大值为 60 秒。

有关使用 ADO.NET 的客户端的阻塞期的介绍，请参阅 [SQL Server 连接池 (ADO.NET)](https://msdn.microsoft.com/library/8xx3tyca.aspx)。

还可以设置程序在自行终止之前的重试次数上限。

### <a name="code-samples-with-retry-logic"></a>重试逻辑代码示例

以下文档提供了有关重试逻辑的代码示例：

- [使用 ADO.NET 弹性连接到 SQL][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
- [使用 PHP 弹性连接到 SQL][step-4-connect-resiliently-to-sql-with-php-p42h]

<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

### <a name="test-your-retry-logic"></a>测试重试逻辑

若要测试重试逻辑，必须模拟或生成程序仍在运行时可更正的错误。

#### <a name="test-by-disconnecting-from-the-network"></a>通过断开网络连接进行测试

可以测试重试逻辑的一种方法是在程序运行时断开客户端计算机与网络的连接。 错误为：

- **SqlException.Number** = 11001
- 消息：“此主机不存在”

第一次重试时，可以将客户端计算机重新连接到网络，然后尝试连接。

要使此测试可行，请从网络中断开计算机的连接，再启动程序。 然后，程序将识别促使它执行以下操作的运行时参数：

- 暂时将 11001 添加到视为暂时性故障的错误列表。
- 像往常一样尝试首次连接。
- 在捕获该错误后，从列表中删除 11001。
- 显示一条消息，告知用户要将计算机接入网络。
- 通过使用 **Console.ReadLine** 方法或具有“确定”按钮的对话框暂停进一步执行。 将计算机接入网络后，用户按 Enter 键。
- 重新尝试连接，预期会成功。

#### <a name="test-by-misspelling-the-database-name-when-connecting"></a>通过在连接时拼错数据库名称进行测试

在首次连接尝试之前，程序可以故意拼错用户名。 错误为：

- **SqlException.Number** = 18456
- 消息：“用户 'WRONG_MyUserName' 的登录失败。”

在首次重试过程中，程序可以更正拼写错误，然后尝试连接。

要使此测试可行，程序需识别促使它执行以下操作的运行时参数：

- 暂时将 18456 添加到视为暂时性故障的错误列表。
- 故意将“WRONG_”添加到用户名。
- 在捕获该错误后，从列表中删除 18456。
- 从用户名中删除“WRONG_”。
- 重新尝试连接，预期会成功。

<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

## <a name="net-sqlconnection-parameters-for-connection-retry"></a>连接重试的 .NET SqlConnection 参数

如果客户端程序使用 .NET Framework 类 **System.Data.SqlClient.SqlConnection** 连接到 SQL 数据库，请使用 .NET 4.6.1 或更高版本（或 .NET Core），以便利用其连接重试功能。 有关此功能的详细信息，请参阅[此网页](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection)。

<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->

为 **SqlConnection** 对象生成[连接字符串](https://msdn.microsoft.com/library/System.Data.SqlClient.SqlConnection.connectionstring.aspx)时，请在以下参数之间协调值：

- **ConnectRetryCount**：&nbsp;&nbsp;默认值为 1。 范围为 0 到 255。
- **ConnectRetryInterval**：&nbsp;&nbsp;默认值为 10 秒。 范围为 1 到 60。
- **Connection Timeout**：&nbsp;&nbsp;默认值为 15 秒。 范围为 0 到 2147483647。

具体而言，所选的值应使以下等式成立：连接超时值 = ConnectRetryCount * ConnectionRetryInterval

例如，如果计数等于 3 且间隔等于 10 秒，超时值仅为 29 秒未给系统足够的时间进行其第三次也是最后一次连接重试，因为 29 < 3 * 10。

<a id="connection-versus-command" name="connection-versus-command"></a>

## <a name="connection-vs-command"></a>连接与命令

**ConnectRetryCount** 和 **ConnectRetryInterval** 参数使 **SqlConnection** 对象在重试连接操作时不用通知或麻烦程序（例如，将控制权返还给程序）。 在以下情况下可能会进行重试：

- mySqlConnection.Open 方法调用
- mySqlConnection.Execute 方法调用

有个很微妙的地方。 如果正在执行查询时发生暂时性错误，**SqlConnection** 对象不会重试连接操作。 肯定不会重试查询。 但是，**SqlConnection** 在发送要执行的查询前会非常快速地检查连接。 如果快速检查检测到连接问题，**SqlConnection** 会重试连接操作。 如果重试成功，则会发送查询以执行。

### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>ConnectRetryCount 是否应结合应用程序重试逻辑？

假设应用程序具有功能强大的自定义重试逻辑。 它可能会重试连接操作四次。 如果将 **ConnectRetryInterval** 和 **ConnectRetryCount** =3 添加到连接字符串，则将重试计数提高到 4 * 3 = 12 次重试。 可能未打算重试这么多次。

<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-sql-database"></a>与 SQL 数据库的连接

<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>连接: 连接字符串

连接到 SQL 数据库所需的连接字符串与连接到 SQL Server 所需的字符串稍有不同。 可以通过 [Azure 门户](https://portal.azure.com/)复制数据库的连接字符串。

[!INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]

<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>连接: IP 地址

必须将 SQL 数据库服务器配置为接受来自托管客户端程序的计算机 IP 地址的通信。 若要设置此配置，可以通过 [Azure 门户](https://portal.azure.com/)编辑防火墙设置。

如果忘记了配置 IP 地址，程序会失败，并显示简单的错误消息，指出所需的 IP 地址。

[!INCLUDE [sql-database-include-ip-address-22-portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]

有关详细信息，请参阅[在 SQL 数据库上配置防火墙设置](sql-database-configure-firewall-settings.md)。
<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>连接: 端口

通常，只需确保在托管客户端程序的计算机上已打开端口 1433 进行出站通信。

例如，当客户端程序托管在 Windows 计算机上时，则可以使用主机上的 Windows 防火墙打开端口 1433。

1. 打开控制面板。
2. 选择“所有控制面板项” > “Windows 防火墙” > “高级设置” > “出站规则” > “操作” > “新建规则”。

如果客户端程序托管在 Azure 虚拟机 (VM) 上，请阅读[用于 ADO.NET 4.5 和 SQL 数据库的非 1433 端口](sql-database-develop-direct-route-ports-adonet-v12.md)。

有关配置端口和 IP 地址的背景信息，请参阅 [Azure SQL 数据库防火墙](sql-database-firewall-configure.md)。

<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-462-or-later"></a>连接: ADO.NET 4.6.2 或更高版本

如果程序使用 **System.Data.SqlClient.SqlConnection** 等 ADO.NET 类来连接到 SQL 数据库，我们建议使用 .NET Framework 4.6.2 或更高版本。

#### <a name="starting-with-adonet-462"></a>从 ADO.NET 4.6.2 开始

- 将立即为 Azure SQL 数据库重试连接打开尝试，从而改进启用了云的应用的性能。

#### <a name="starting-with-adonet-461"></a>从 ADO.NET 4.6.1 开始

- 对于 SQL 数据库，使用 **SqlConnection.Open** 方法打开连接可以获得更高的可靠性。 此 **Open** 方法现在结合了应对暂时性故障的最佳效果重试机制，用于处理连接超时期间发生的特定错误。
- 支持连接池，其中包括有效验证提供给程序的连接对象是否正常运行的功能。

若要从连接池使用连接对象，我们建议，如果程序未立即使用连接，应暂时关闭连接。 重新打开连接的开销并不高，但要创建新连接。

如果使用 ADO.NET 4.0 或更低版本，我们建议升级到最新的 ADO.NET。 从 2018 年 8 月开始，可以[下载 ADO.NET 4.6.2](https://blogs.msdn.microsoft.com/dotnet/20../../announcing-the-net-framework-4-7-2/)。

<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>诊断

<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>诊断：测试实用程序是否可以连接

如果程序无法连接到 SQL 数据库，可通过一个诊断选项尝试使用某个实用工具进行连接。 理想情况下，该实用工具将使用程序所用的同一个库进行连接。

可以在任何 Windows 计算机上尝试以下实用程序：

- SQL Server Management Studio (ssms.exe)，它使用 ADO.NET 进行连接
- `sqlcmd.exe`，它使用 [ODBC](https://msdn.microsoft.com/library/jj730308.aspx) 进行连接

连接程序后，测试一个简短的 SQL SELECT 查询是否可以正常工作。

<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>诊断：检查打开的端口

如果你怀疑连接尝试由于端口问题而失败，可以在计算机上运行报告端口配置的实用工具。

在 Linux 上，以下实用工具可能很有用：

- `netstat -nap`
- `nmap -sS -O 127.0.0.1`：请将示例值更改为你的 IP 地址。

在 Windows 上，[PortQry.exe](https://www.microsoft.com/download/details.aspx?id=17148) 实用工具可能很有用。 以下是在 SQL 数据库服务器上查询端口情况，以及在便携式计算机上运行了哪个端口的示例执行：

```cmd
[C:\Users\johndoe\]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called: johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe\]
>>
```

<a id="g-diagnostics-log-your-errors" name="g-diagnostics-log-your-errors"></a>

### <a name="diagnostics-log-your-errors"></a>诊断：记录错误

有时，诊断间歇性问题的最好方式是每隔数天或数周检测常规模式。

客户端可以通过记录其所遇到的所有错误来帮助你进行诊断。 可将日志条目与 SQL 数据库本身内部记录的错误数据相关联。

Enterprise Library 6 (EntLib60) 提供了 .NET 托管类来帮助进行日志记录。 有关详细信息，请参阅：[5 - 与写入日志一样简单：使用日志记录应用程序块](https://msdn.microsoft.com/library/dn440731.aspx)。

<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>诊断：在系统日志中检查错误

下面是查询错误日志和其他信息的一些 Transact-SQL SELECT 语句。

| 日志查询 | 描述 |
|:--- |:--- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` |[sys.event_log](https://msdn.microsoft.com/library/dn270018.aspx) 视图提供有关各个事件的信息，包括一些可能导致暂时性错误或连接故障的事件。<br/><br/>理想情况下，可以将 **start_time** 或 **end_time** 值与有关客户端程序遇到问题时的信息相关联。<br/><br/>必须连接到 *master* 数据库才能运行此查询。 |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` |[sys.database_connection_stats](https://msdn.microsoft.com/library/dn269986.aspx) 视图针对其他诊断提供事件类型的聚合计数。<br/><br/>必须连接到 *master* 数据库才能运行此查询。 |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>诊断：在 SQL 数据库日志中搜索问题事件

可以在 SQL 数据库日志中搜索有关问题事件的条目。 在 *master* 数据库中尝试运行以下 Transact-SQL SELECT 语句：

```sql
SELECT
   object_name
  ,CAST(f.event_data as XML).value
      ('(/event/@timestamp)[1]', 'datetime2')                      AS [timestamp]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="error"]/value)[1]', 'int')             AS [error]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="state"]/value)[1]', 'int')             AS [state]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="is_success"]/value)[1]', 'bit')        AS [is_success]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="database_name"]/value)[1]', 'sysname') AS [database_name]
FROM
  sys.fn_xe_telemetry_blob_target_read_file('el', null, null, null) AS f
WHERE
  object_name != 'login_event'  -- Login events are numerous.
  and
  '2015-06-21' < CAST(f.event_data as XML).value
        ('(/event/@timestamp)[1]', 'datetime2')
ORDER BY
  [timestamp] DESC
;
```

#### <a name="a-few-returned-rows-from-sysfn_xe_telemetry_blob_target_read_file"></a>将返回 sys.fn_xe_telemetry_blob_target_read_file 中的若干行

以下示例显示返回的行的类似内容。 显示的 null 值在其他行中通常不是 null。

```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```

<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>Enterprise Library 6

Enterprise Library 6 (EntLib60) 是 .NET 类的框架，可帮助你实施云服务（包括 SQL 数据库服务）的可靠客户端。 若要查找 EntLib60 可以提供帮助的各个领域的相关专题，请参阅 [Enterprise Library 6 - 2013 年 4 月](https://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx)。

用于处理暂时性错误的重试逻辑是 EntLib60 可以提供帮助的一个领域。 有关详细信息，请参阅 [4 - 锲而不舍是一切成功的秘密：使用暂时性故障处理应用程序块](https://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx)。

> [!NOTE]
> EntLib60 的源代码可从[下载中心](https://go.microsoft.com/fwlink/p/?LinkID=290898)公开下载。 Microsoft 不打算对 EntLib 做进一步的功能更新或维护更新。

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>用于暂时性错误和重试的 EntLib60 类

以下 EntLib60 类对重试逻辑特别有用。 可以在 **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling** 命名空间或其子级中找到所有这些类。

在命名空间 **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling** 中：

- **RetryPolicy** 类
  - **ExecuteAction** 方法
- **ExponentialBackoff** 类
- **SqlDatabaseTransientErrorDetectionStrategy** 类
- **ReliableSqlConnection** 类
  - **ExecuteCommand** 方法

在命名空间 **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport** 中：

- **AlwaysTransientErrorDetectionStrategy** 类
- **NeverTransientErrorDetectionStrategy** 类

以下是 EntLib60 相关信息的某些链接：

- 免费书籍下载：[Microsoft Enterprise Library 版本 2 开发人员指南](https://www.microsoft.com/download/details.aspx?id=41145)
- 最佳做法：[重试常规指南](../best-practices-retry-general.md)深入探讨了重试逻辑。
- NuGet 下载：[Enterprise Library - 暂时性故障处理应用程序块 6.0](https://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/)。

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60：日志记录块

- 日志记录块是极其灵活且可配置的解决方案，可用于：
  - 创建日志消息并将其存储在各种不同的位置。
  - 分类与筛选消息。
  - 收集有助于调试和跟踪的上下文信息，以及用于满足审核和一般日志记录要求的上下文信息。
- 日志记录块可以从日志目标抽象化日志记录功能，使应用程序代码保持一致，无论目标日志记录存储的位置和类型为何。

有关详细信息，请参阅：[5 - 与写入日志一样简单：使用日志记录应用程序块](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx)。

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>EntLib60 IsTransient 方法的源代码

接下来，**SqlDatabaseTransientErrorDetectionStrategy** 类包含 **IsTransient** 方法的 C# 源代码。 该源代码阐明了哪些错误被视为暂时性错误并值得重试（截止 2013 年 4 月）。

```csharp
public bool IsTransient(Exception ex)
{
  if (ex != null)
  {
    SqlException sqlException;
    if ((sqlException = ex as SqlException) != null)
    {
      // Enumerate through all errors found in the exception.
      foreach (SqlError err in sqlException.Errors)
      {
        switch (err.Number)
        {
            // SQL Error Code: 40501
            // The service is currently busy. Retry the request after 10 seconds.
            // Code: (reason code to be decoded).
          case ThrottlingCondition.ThrottlingErrorNumber:
            // Decode the reason code from the error message to
            // determine the grounds for throttling.
            var condition = ThrottlingCondition.FromError(err);

            // Attach the decoded values as additional attributes to
            // the original SQL exception.
            sqlException.Data[condition.ThrottlingMode.GetType().Name] =
              condition.ThrottlingMode.ToString();
            sqlException.Data[condition.GetType().Name] = condition;

            return true;

          case 10928:
          case 10929:
          case 10053:
          case 10054:
          case 10060:
          case 40197:
          case 40540:
          case 40613:
          case 40143:
          case 233:
          case 64:
            // DBNETLIB Error Code: 20
            // The instance of SQL Server you attempted to connect to
            // does not support encryption.
          case (int)ProcessNetLibErrorCode.EncryptionNotSupported:
            return true;
        }
      }
    }
    else if (ex is TimeoutException)
    {
      return true;
    }
    else
    {
      EntityException entityException;
      if ((entityException = ex as EntityException) != null)
      {
        return this.IsTransient(entityException.InnerException);
      }
    }
  }

  return false;
}
```

## <a name="next-steps"></a>后续步骤

- 有关其他常见 SQL 数据库连接问题的故障排除信息，请参阅[排查 Azure SQL 数据库的连接问题](sql-database-troubleshoot-common-connection-issues.md)。
- [用于 SQL 数据库和 SQL Server 的连接库](sql-database-libraries.md)
- [SQL Server 连接池 (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)
- [*重试*是 Apache 2.0 授权的通用重试库，它以 Python](https://pypi.python.org/pypi/retrying) 编写，可以简化向几乎任何程序添加重试行为的任务。

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php
