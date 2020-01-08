---
title: 服务到服务身份验证：使用 Azure Active Directory 将 Java 与 Azure 数据湖存储 Gen1 |Microsoft Docs
description: 了解如何使用 Azure Active Directory 和 Java 进行 Azure Data Lake Storage Gen1 服务到服务身份验证
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: c32eada2acca73e089c2296ce8e59c529d7af665
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60194988"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-java"></a>使用 Java 进行 Azure Data Lake Storage Gen1 服务到服务身份验证
> [!div class="op_single_selector"]
> * [使用 Java](data-lake-store-service-to-service-authenticate-java.md)
> * [使用 .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [使用 Python](data-lake-store-service-to-service-authenticate-python.md)
> * [使用 REST API](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
>  

本文介绍如何使用 Java SDK 执行 Azure Data Lake Storage Gen1 服务到服务身份验证。 不支持使用 Java SDK 进行 Data Lake Storage Gen1 最终用户身份验证。

## <a name="prerequisites"></a>必备组件
* **Azure 订阅**。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

* **创建 Azure Active Directory“Web”应用程序**。 必须已完成[使用 Azure Active Directory 进行 Data Lake Storage Gen1 服务到服务身份验证](data-lake-store-service-to-service-authenticate-using-active-directory.md)中的步骤。

* [Maven](https://maven.apache.org/install.html)。 本教程使用 Maven 生成项目和项目依赖项。 尽管不使用 Maven 或 Gradle 等生成系统也能完成生成，但使用这些系统可以大大减化依赖项的管理。

* （可选）[IntelliJ IDEA](https://www.jetbrains.com/idea/download/)、[Eclipse](https://www.eclipse.org/downloads/) 或类似的 IDE。

## <a name="service-to-service-authentication"></a>服务到服务身份验证
1. 在命令行使用 [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) 或通过 IDE 创建一个 Maven 项目。 有关如何使用 IntelliJ 创建 Java 项目的说明，请参阅[此文](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html)。 有关如何使用 Eclipse 创建项目的说明，请参阅[此文](https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm)。

2. 将以下依赖项添加到 Maven pom.xml  文件。 在 \</project> 标记前添加以下代码片段  ：
   
        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.2.3</version>
          </dependency>
          <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>1.7.21</version>
          </dependency>
        </dependencies>
   
    第一个依赖项从 maven 存储库使用 Data Lake Storage Gen1 SDK (`azure-data-lake-store-sdk`)。 第二个依赖项指定此应用程序使用的日志记录框架 (`slf4j-nop`)。 Data Lake Storage Gen1 SDK 使用 [slf4j](https://www.slf4j.org/) 日志记录体系，允许用户从多种流行的记录框架（如 log4j、Java 日志记录、logback 等）中进行选择，或者不使用日志记录。 本示例禁用日志记录，因此我们使用 slf4j-nop 绑定  。 若要在应用中使用其他日志记录选项，请参阅[此文](https://www.slf4j.org/manual.html#projectDep)。

3. 将以下导入语句添加到应用程序。

        import com.microsoft.azure.datalake.store.ADLException;
        import com.microsoft.azure.datalake.store.ADLStoreClient;
        import com.microsoft.azure.datalake.store.DirectoryEntry;
        import com.microsoft.azure.datalake.store.IfExists;
        import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
        import com.microsoft.azure.datalake.store.oauth2.ClientCredsTokenProvider;

4. 使用 Java 应用程序中的以下代码片段获取以前使用 `AccessTokenProvider` 的子类之一创建的 Active Directory Web 应用程序的令牌（以下示例使用 `ClientCredsTokenProvider`）。 令牌提供程序将缓存用于获取内存中令牌的凭据，在令牌即将过期时自动续订令牌。 可以创建自己的 `AccessTokenProvider` 子类以便通过客户代码获取令牌。 我们暂时只使用 SDK 中提供的子类。

    将 **FILL-IN-HERE** 替换为 Azure Active Directory Web 应用程序的实际值。

        private static String clientId = "FILL-IN-HERE";
        private static String authTokenEndpoint = "FILL-IN-HERE";
        private static String clientKey = "FILL-IN-HERE";
    
        AccessTokenProvider provider = new ClientCredsTokenProvider(authTokenEndpoint, clientId, clientKey);   

Data Lake Storage Gen1 SDK 可让用户使用便利的方法管理所需的安全令牌来与 Data Lake Storage Gen1 帐户通信。 但是，该 SDK 并未规定只能使用这些方法。 可以使用其他任何方法获取令牌，例如，使用 [Azure Active Directory SDK](https://github.com/AzureAD/azure-activedirectory-library-for-java) 或自己的自定义代码。

## <a name="next-steps"></a>后续步骤
本文介绍如何使用 Java SDK 通过最终用户身份验证进行 Data Lake Storage Gen1 身份验证。 接下来，可以查看介绍如何使用 Java SDK 在 Data Lake Storage Gen1 中执行操作的以下文章。

* [使用 Java SDK 在 Data Lake Storage Gen1 中进行的数据操作](data-lake-store-get-started-java-sdk.md)


