---
title: 排查 Azure 数据工厂连接器问题 |Microsoft Docs
description: 了解如何排查 Azure 数据工厂中的连接器问题。
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 08/26/2019
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: bea5191063cf673f6b1395d46a15536e80b0aa30
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2019
ms.locfileid: "70143500"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>排查 Azure 数据工厂连接器问题

本文探讨了 Azure 数据工厂中连接器的常见故障排除方法。

## <a name="azure-data-lake-storage"></a>Azure Data Lake Storage

### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>错误消息：远程服务器返回错误：(403) 禁止访问

- **症状**：复制活动失败, 出现以下错误: 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **原因**：一个可能的原因是你使用的服务主体或托管标识无权访问某些文件夹/文件。

- **解决方法**：授予需要复制的所有文件夹和子文件夹的相应权限。 请参阅[此文档](connector-azure-data-lake-store.md#linked-service-properties)。

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>错误消息：无法使用服务主体获取访问令牌。 ADAL 错误: service_unavailable

- **症状**: 复制活动失败, 出现以下错误:

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **原因**：如果 Azure Active Directory 拥有的服务令牌服务器 (STS) 不可用, 例如, 太忙而无法处理请求, 则返回 HTTP 错误503。 

- **解决方法**：几分钟后重新运行复制活动。

## <a name="azure-sql-data-warehouse"></a>Azure SQL 数据仓库

### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>错误消息：将字符串转换为 uniqueidentifier 时转换失败

- **症状**：使用暂存复制和 PolyBase 将数据从表格数据源 (例如 SQL Server) 复制到 Azure SQL 数据仓库时, 遇到以下错误:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **原因**：Azure SQL 数据仓库 PolyBase 无法将空字符串转换为 GUID。

- **解决方法**：在复制活动接收器的 "Polybase 设置" 下, 将 "**使用类型默认值**" 选项设置为 "false"。

### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>错误消息：预期的数据类型:DECIMAL (x, x), 有问题的值

- **症状**：使用暂存复制和 PolyBase 将数据从表格数据源 (例如 SQL Server) 复制到 SQL DW 时, 遇到以下错误:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **原因**：Azure SQL 数据仓库 Polybase 不能将空字符串 (null 值) 插入 decimal 列中。

- **解决方法**：在复制活动接收器的 "Polybase 设置" 下, 将 "**使用类型默认值**" 选项设置为 "false"。

### <a name="error-message-java-exception-messagehdfsbridgecreaterecordreader"></a>错误消息：Java 异常消息: HdfsBridge:: CreateRecordReader

- **症状**：使用 PolyBase 将数据复制到 Azure SQL 数据仓库, 并遇到以下错误:

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **原因**：可能的原因是架构 (总列宽宽度) 太大 (大于 1 MB)。 通过添加所有列的大小来检查目标 SQL DW 表的架构:

    - Int-> 4 个字节
    - Bigint-> 8 字节
    - Varchar (n)、char (n)、binary (n)、varbinary (n)-> n 个字节
    - Nvarchar (n)、nchar (n)-> n * 2 字节
    - Date-> 6 个字节
    - Datetime/(2)、smalldatetime-> 16 字节
    - Datetimeoffset-> 20 个字节
    - 小数-> 19 个字节
    - Float-> 8 个字节
    - Money-> 8 个字节
    - Smallmoney-> 4 个字节
    - 实 > 4 个字节
    - Smallint-> 2 个字节
    - 时间-> 12 个字节
    - Tinyint-> 1 个字节

- **解决方法**：将列宽缩小为小于 1 MB

- 或通过禁用 Polybase 来使用 bulk insert 方法

### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>错误消息：不满足使用 HTTP 条件标头指定的条件

- **症状**：使用 SQL 查询从 Azure SQL 数据仓库拉取数据, 并遇到以下错误:

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **原因**：在 Azure 存储中查询外部表时, azure SQL 数据仓库遇到问题。

- **解决方法**：在 SSMS 中运行相同的查询, 并检查是否看到相同的结果。 如果是, 请为 Azure SQL 数据仓库提供支持票证, 并提供你的 SQL DW 服务器和数据库名称以便进一步排查问题。

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>错误消息：请求太大

- **症状**：将数据复制到具有默认写入批大小的 Azure Cosmos DB, 并命中错误 *"**请求大小太大**"* 。

- **原因**：Cosmos DB 限制单个请求的大小为 2 MB。 公式为, 请求大小 = 单文档大小 * 写入批大小。 如果文档大小太大, 则默认行为会导致太大的请求大小。 可以优化写入批大小。

- **解决方法**：在复制活动接收器中, 减少 "写入批大小" 值 (默认值为 10000)。

### <a name="error-message-unique-index-constraint-violation"></a>错误消息：唯一索引约束冲突

- **症状**：将数据复制到 Cosmos DB 时遇到以下错误:

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception &#61; Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **原因**：有两个可能的原因：

    - 如果使用**Insert** as 写入行为, 此错误意味着源数据具有具有相同 ID 的行/对象。

    - 如果使用**Upsert**作为写入行为, 并将另一个唯一键设置到容器, 此错误意味着源数据的行/对象具有不同的 id, 但定义的唯一键的值相同。

- **解决方法**： 

    - 对于 cause1, 将**Upsert**设置为写入行为。
    - 对于原因 2, 请确保每个文档的定义唯一键的值不同。

### <a name="error-message-request-rate-is-large"></a>错误消息：请求速率大

- **症状**：将数据复制到 Cosmos DB 时遇到以下错误:

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **原因**：使用的请求单位大于在 Cosmos DB 中配置的可用 RU。 了解 Cosmos DB 从[此处](../cosmos-db/request-units.md#request-unit-considerations)计算 RU 的方式。

- **解决方法**：下面是两个解决方案:

    1. 在 Cosmos DB 中将**容器 RU 增加**到更大的值, 这将提高复制活动的性能, 但在 Cosmos DB 会导致更高的成本。 

    2. 将**writeBatchSize**减少到较小的值 (例如 1000), 并将**parallelCopies**设置为较小的值 (例如 1), 这会使复制运行性能比当前更糟, 但不会在 Cosmos DB 中产生更多费用。

### <a name="column-missing-in-column-mapping"></a>列映射中缺少列

- **症状**：导入用于列映射的 Cosmos DB 的架构时, 缺少某些列。 

- **原因**：ADF 从前10个 Cosmos DB 文档中推断架构。 如果某些列/属性不具有这些文档中的值, 则 ADF 不会检测到它们, 因此不会显示。

- **解决方法**：你可以按以下方式优化查询, 以强制列在结果集中显示空值: (假定: 前10个文档中缺少 "不可能" 列)。 或者, 您可以手动添加用于映射的列。

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>错误消息：读取器的 GuidRepresentation 是 CSharpLegacy

- **症状**：从 Cosmos DB MongoAPI/MongoDB 和 UUID 字段复制数据时, 遇到以下错误:

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **原因**：可以通过两种方法来表示 BSON-UuidStardard 和 UuidLegacy 中的 UUID。 默认情况下, UuidLegacy 用于读取数据。 如果 MongoDB 中的 UUID 数据是 UuidStandard, 则会遇到错误。

- **解决方法**：在 MongoDB 连接字符串中, 添加选项 "**uuidRepresentation = standard**"。 有关详细信息, 请参阅[MongoDB 连接字符串](connector-mongodb.md#linked-service-properties)。

## <a name="sftp"></a>SFTP

### <a name="error-message-invalid-sftp-credential-provided-for-sshpublickey-authentication-type"></a>错误消息：为 "SshPublicKey" 身份验证类型提供的 SFTP 凭据无效

- **症状**：使用`SshPublicKey`身份验证并遇到以下错误:

    ```
    Invalid Sftp credential provided for 'SshPublicKey' authentication type
    ```

- **原因**：有3种可能的原因:

    1. 如果你使用 ADF 创作 UI 创作 SFTP 链接服务, 此错误意味着你选择使用的私钥的格式不正确。 您可以使用 SSH 私钥的 PKCS # 8 格式, 同时请注意, ADF 仅支持传统 SSH 密钥格式。 更具体地说, PKCS # 8 格式和传统密钥格式的差异在于 PKCS # 8 密钥内容以 " *-----开始加密私钥-----* " 开头, 而传统密钥格式则以 " *-----BEGIN RSA PRIVATE key-----* " 开头。
    2. 如果使用 Azure Key Vault 来存储私钥内容, 或使用 programmatical 方法创作 SFTP 链接服务, 此错误表示私钥内容不正确, 很可能不是 base64 编码的内容。
    3. 凭据或私钥内容无效。

- **解决方法**： 

    - 出于 #1 的原因, 请运行以下命令, 将密钥转换为传统密钥格式, 然后在 ADF 创作 UI 中使用它。

        ```
        # Decrypt the pkcs8 key and convert the format to traditional key format
        openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file

        chmod 600 traditional_format_key_file

        # Re-encrypte the key file using passphrase
        ssh-keygen -f traditional_format_key_file -p
        ```

    - 为了 #2, 若要生成此类字符串, 客户可以使用以下两种方法:
    - 使用第三方 base64 转换工具: 将整个私钥内容粘贴到[Base64 编码和解码](https://www.base64encode.org/)的工具中, 将其编码为 base64 格式字符串, 然后将此字符串粘贴到密钥保管库, 或使用此值以编程方式创作 SFTP 链接服务。
    - 使用C#代码:

        ```c#
        byte[] keyContentBytes = File.ReadAllBytes(privateKeyPath);
        string keyContent = Convert.ToBase64String(keyContentBytes, Base64FormattingOptions.None);
        ```

    - 出于 #3 的原因, 请使用其他工具来确认密钥文件或密码是否正确, 以验证是否可以使用它来正确访问 SFTP 服务器。

## <a name="next-steps"></a>后续步骤

有关故障排除的详细信息, 请尝试以下资源:

*  [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [数据工厂功能请求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN 论坛](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [用于数据工厂的 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [有关数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)



