---
title: include 文件
description: include 文件
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 08/12/2019
ms.author: v-adgera
ms.custom: include file
ms.openlocfilehash: f9b1b32775393196a77bef3aa2257f6fb66afdcb
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "69012048"
---
> [!NOTE]
> 多部分请求通常需要三个信息片段：
> * **Content-Type** 标头：
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`
> * **Content-Disposition**：
>   * `form-data; name="metadata"`
> * 要上传的文件内容
>
> **Content-Type** 和 **Content-Disposition** 将因使用方案而异。

可以通过 REST 客户端或诸如 [Postman](https://docs.microsoft.com/azure/digital-twins/how-to-configure-postman#multi) 的工具以编程方式（通过 C#）进行多部分请求。 各种 REST 客户端工具对复杂的多部分请求可能具有不同的支持级别。 不同工具的配置设置也可能略有不同。 请验证哪个工具最适合你的需求。

> [!IMPORTANT]
> 向 Azure 数字孪生管理 API 发出的多部分请求通常包含两个部分：
> * 由 **Content-Type** 和/或 **Content-Disposition** 声明的 Blob 元数据（例如关联的 MIME 类型）
> * 包括要上传的文件的非结构化内容的 Blob 内容
>
> 对于 PATCH 请求，上述两个部分都不是必需的。 对于 **POST** 请求或 create 操作，两者都是必需的。

[采用快速入门源代码](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/api/update.cs)包含完整的 C# 示例，其中展示了如何针对 Azure 数字孪生管理 API 进行多部分请求。