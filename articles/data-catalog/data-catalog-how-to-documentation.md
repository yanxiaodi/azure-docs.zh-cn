---
title: 如何在 Azure 数据目录中记录数据源
description: 操作指南文章重点说明如何在 Azure 数据目录中记录数据资产。
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: e9e9013d354585d04f205feb93a84d94c0f05905
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950190"
---
# <a name="how-to-document-data-sources-in-azure-data-catalog"></a>如何在 Azure 数据目录中记录数据源

## <a name="introduction"></a>简介
**Microsoft Azure 数据目录**是一个完全托管的云服务，充当企业数据源的注册系统和发现系统。 换而言之，**Azure 数据目录**旨在帮助人们发现、了解,和使用数据源，并帮助组织从其现有数据中获取更多价值。

数据源向 **Azure 数据目录**注册时，该服务将复制其元数据并为其建立索引，但并不仅仅只是这样。 **Azure 数据目录**还允许用户提供自己的完整文档，以描述数据源的使用情况和常见方案。

在 [如何对数据源进行批注](data-catalog-how-to-annotate.md)中，了解到知道数据源的专家可以使用标记和说明对其进行批注。 **Azure 数据目录**门户包含 RTF 编辑器，使用户可以完整地记录数据资产和容器。 编辑器包括段落格式设置（如标题）、文本格式设置、项目符号列表、编号列表和表。

标记和说明非常适合简单批注。 但是，为了帮助数据使用者更好地了解数据源的用法和数据源的业务方案，专家可以提供完整且详尽的文档。 记录数据源很简单。 选择数据资产或容器，并选择“文档”。

![数据目录中的文档选项卡](media/data-catalog-documentation/data-catalog-documentation.png)

## <a name="documenting-data-assets"></a>记录数据资产
**Azure 数据目录**文档的优点让可以使用数据目录作为内容存储库，创建完整的数据资产叙述。 可以浏览描述容器和表的详细内容。 如果其他内容存储库中已有内容（例如 SharePoint 或文件共享），可以添加资产文档链接来参考现有内容。 此功能使现有文档更容易被发现。

> [!NOTE]
> 文档不包括在搜索索引中。
>

![文档选项卡和到 web 链接的超链接](media/data-catalog-documentation/data-catalog-documentation2.png)

文档级别范围可以从描述数据资产的特性和值到容器中表架构的详细说明。 应根据业务需求提供相应的文档级别。 但通常情况下，记录数据资产存在以下几种利弊：

* 仅记录容器:所有内容都在一个位置, 但可能没有必要的详细信息供用户作出明智的决策。
* 仅记录表:内容特定于该对象, 但您的用户有多个文档位置。
* 文档容器和表:最全面的方法, 但可能会引入更多文档维护。

## <a name="summary"></a>总结
使用 **Azure 数据目录**记录数据源可以根据需要尽可能详细地叙述数据资产。  通过使用链接，可以链接到现有内容存储库中存储的内容，将现有文档和数据资产结合起来。 用户发现合适的数据资产后，就可以获取一组完整的文档。
