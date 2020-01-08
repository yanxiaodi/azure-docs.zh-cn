---
title: 使用 Azure Data Lake Analytics 查询 Avro 数据 |Microsoft Docs
description: 使用消息正文属性将设备遥测数据路由到 Blob 存储并查询写入到 Blob 存储的 Avro 格式数据。
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.openlocfilehash: 84e1dd77c6e873dc2facb5126bbddf795192b60d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66257747"
---
# <a name="query-avro-data-by-using-azure-data-lake-analytics"></a>使用 Azure Data Lake Analytics 查询 Avro 数据

本文讨论了如何查询 Avro 数据，以高效地将消息从 Azure IoT 中心路由到 Azure 服务。 [消息路由](iot-hub-devguide-messages-d2c.md)允许使用基于消息属性、消息正文、设备孪生标记和设备孪生属性的丰富查询来筛选数据。 若要了解有关在消息路由的查询功能的详细信息，请参阅文章[消息路由查询语法](iot-hub-devguide-routing-query-syntax.md)。

面临的挑战已，当 Azure IoT 中心将消息路由到 Azure Blob 存储，默认情况下 IoT 中心写入内容以 Avro 格式，具有消息正文属性和消息属性。 Avro 格式不用于任何其他终结点。 尽管 Avro 格式可用于保存数据和消息，但将其用于查询数据将是一项挑战。 比较而言，JSON 或 CSV 格式更容易用来查询数据。 IoT 中心现在支持将数据写入 Blob 存储在 JSON 和 AVRO。

有关详细信息，请参阅[使用 Azure Blob 存储作为路由终结点](iot-hub-devguide-messages-d2c.md#azure-blob-storage)。

为了解决非关系大数据需求和格式并应对这一挑战，可以使用许多大数据模式来对数据进行转换和缩放。 其中的一个模式“按查询付费”是 Azure Data Lake Analytics，它是本文重点要讨论的内容。 虽然可以在 Hadoop 或其他解决方案中轻松执行查询，但 Data Lake Analytics 通常更适合“按查询付费”方式。

U-SQL 中有一个适用于 Avro 的“提取程序”。 有关详细信息，请参阅 [U-SQL Avro 示例](https://github.com/Azure/usql/tree/master/Examples/AvroExamples)。

## <a name="query-and-export-avro-data-to-a-csv-file"></a>查询 Avro 数据并将其导出到 CSV 文件

在本部分，你将查询 Avro 数据并将其导出到 Azure Blob 存储中的一个 CSV 文件，但也可以轻松将数据放置在其他存储库或数据存储中。

1. 将 Azure IoT 中心设置为通过使用消息正文中的属性来选择消息来将数据路由到 Azure Blob 存储终结点。

   ![“自定义终结点”部分](./media/iot-hub-query-avro-data/query-avro-data-1a.png)

   ![传递规则](./media/iot-hub-query-avro-data/query-avro-data-1b.png)

   有关设置路由和自定义终结点的详细信息，请参阅 [IoT 中心的消息路由](iot-hub-create-through-portal.md#message-routing-for-an-iot-hub)。

2. 确保你的设备有属性或消息正文中的编码、内容类型和所需的数据，如产品文档中所述。 当在 Device Explorer 中查看这些属性时（如此处所示），可以验证这些属性是否已正确设置。

   ![“事件中心数据”窗格](./media/iot-hub-query-avro-data/query-avro-data-2.png)

3. 设置 Azure Data Lake Store 实例和 Data Lake Analytics 实例。 Azure IoT 中心不会路由到 Data Lake Store 实例，但 Data Lake Analytics 实例需要它。

   ![Data Lake Store 和 Data Lake Analytics 实例](./media/iot-hub-query-avro-data/query-avro-data-3.png)

4. 在 Data Lake Analytics 中，将 Azure Blob 存储配置为一个附加存储（Azure IoT 中心将数据路由到的同一 Blob 存储）。

   ![“数据源”窗格](./media/iot-hub-query-avro-data/query-avro-data-4.png)

5. 如 [U-SQL Avro 示例](https://github.com/Azure/usql/tree/master/Examples/AvroExamples)中讨论的那样，需要四个 DLL 文件。 将这些文件上载到 Data Lake Store 实例中的某个位置。

   ![四个已上载的 DLL 文件](./media/iot-hub-query-avro-data/query-avro-data-5.png)

6. 在 Visual Studio 中，创建一个 U-SQL 项目。

   !创建 U-SQL 项目](./media/iot-hub-query-avro-data/query-avro-data-6.png)

7. 将以下脚本的内容粘贴到新创建的文件中。 修改三个突出显示的部分：Data Lake Analytics 帐户、关联的 DLL 文件路径，以及存储帐户的正确路径。

   ![要修改的三个部分](./media/iot-hub-query-avro-data/query-avro-data-7a.png)

   用于简单输出到 CSV 文件的实际 U-SQL 脚本：

    ```sql
        DROP ASSEMBLY IF EXISTS [Avro];
        CREATE ASSEMBLY [Avro] FROM @"/Assemblies/Avro/Avro.dll";
        DROP ASSEMBLY IF EXISTS [Microsoft.Analytics.Samples.Formats];
        CREATE ASSEMBLY [Microsoft.Analytics.Samples.Formats] FROM @"/Assemblies/Avro/Microsoft.Analytics.Samples.Formats.dll";
        DROP ASSEMBLY IF EXISTS [Newtonsoft.Json];
        CREATE ASSEMBLY [Newtonsoft.Json] FROM @"/Assemblies/Avro/Newtonsoft.Json.dll";
        DROP ASSEMBLY IF EXISTS [log4net];
        CREATE ASSEMBLY [log4net] FROM @"/Assemblies/Avro/log4net.dll";

        REFERENCE ASSEMBLY [Newtonsoft.Json];
        REFERENCE ASSEMBLY [log4net];
        REFERENCE ASSEMBLY [Avro];
        REFERENCE ASSEMBLY [Microsoft.Analytics.Samples.Formats];

        // Blob container storage account filenames, with any path
        DECLARE @input_file string = @"wasb://hottubrawdata@kevinsayazstorage/kevinsayIoT/{*}/{*}/{*}/{*}/{*}/{*}";
        DECLARE @output_file string = @"/output/output.csv";

        @rs =
        EXTRACT
        EnqueuedTimeUtc string,
        Body byte[]
        FROM @input_file

        USING new Microsoft.Analytics.Samples.Formats.ApacheAvro.AvroExtractor(@"
        {
            ""type"":""record"",
            ""name"":""Message"",
            ""namespace"":""Microsoft.Azure.Devices"",
            ""fields"":
           [{
                ""name"":""EnqueuedTimeUtc"",
                ""type"":""string""
            },
            {
                ""name"":""Properties"",
                ""type"":
                {
                    ""type"":""map"",
                    ""values"":""string""
                }
            },
            {
                ""name"":""SystemProperties"",
                ""type"":
                {
                    ""type"":""map"",
                    ""values"":""string""
                }
            },
            {
                ""name"":""Body"",
                ""type"":[""null"",""bytes""]
            }]
        }"
        );

        @cnt =
        SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
        FROM @rs;

        OUTPUT @cnt TO @output_file USING Outputters.Text(); 
    ```

    Data Lake Analytics 需要五分钟的时间来运行以下脚本，该脚本被限制为 10 个分析单元和处理 177 个文件。 结果显示在以下图像中显示的 CSV 文件输出中：

    ![输出到 CSV 文件的结果](./media/iot-hub-query-avro-data/query-avro-data-7b.png)

    ![转换为 CSV 文件的输出](./media/iot-hub-query-avro-data/query-avro-data-7c.png)

    若要解析 JSON，请继续执行步骤 8。

8. 大多数 IoT 消息采用 JSON 文件格式。 通过添加下列行，可以将消息解析为 JSON 文件，这使你能够添加 WHERE 子句并仅输出所需的数据。

    ```sql
       @jsonify =
         SELECT Microsoft.Analytics.Samples.Formats.Json.JsonFunctions.JsonTuple(Encoding.UTF8.GetString(Body))
           AS message FROM @rs;
    
        /*
        @cnt =
            SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
            FROM @rs;
        
        OUTPUT @cnt TO @output_file USING Outputters.Text();
        */
        
        @cnt =
            SELECT message["message"] AS iotmessage,
                   message["event"] AS msgevent,
                   message["object"] AS msgobject,
                   message["status"] AS msgstatus,
                   message["host"] AS msghost
            FROM @jsonify;
            
        OUTPUT @cnt TO @output_file USING Outputters.Text();
    ```

    输出显示 `SELECT` 命令中的每个项的列。

    ![显示每个项的列的输出](./media/iot-hub-query-avro-data/query-avro-data-8.png)

## <a name="next-steps"></a>后续步骤

在本教程中，你已学习了如何查询 Avro 数据以高效地将消息从 Azure IoT 中心路由到 Azure 服务。

有关使用 IoT 中心完成端到端解决方案的示例，请参阅 [Azure IoT 解决方案加速器文档](/azure/iot-accelerators)。

若要了解有关使用 IoT 中心开发解决方案的详细信息，请参阅 [IoT 中心开发人员指南](iot-hub-devguide.md)。

若要详细了解 IoT 中心的消息路由，请参阅[使用 IoT 中心发送和接收消息](iot-hub-devguide-messaging.md)。
