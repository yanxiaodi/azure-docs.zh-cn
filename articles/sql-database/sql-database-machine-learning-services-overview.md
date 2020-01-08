---
title: 使用 R （预览版） 概述的 azure SQL 数据库机器学习服务
description: 本文介绍 Azure SQL 数据库机器学习服务 （使用 R) 并说明其工作原理。
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 03/01/2019
ms.openlocfilehash: 186b986fe1931365ee34efab2e04e58908402cc0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67427945"
---
# <a name="azure-sql-database-machine-learning-services-with-r-preview"></a>使用 R （预览版） 的 azure SQL 数据库机器学习服务

机器学习服务是 Azure SQL 数据库的一项功能，用于执行数据库中的 R 脚本。 该功能包括用于高性能预测分析和机器学习的 Microsoft R 包。 关系数据可以通过存储过程、包含 R 语句的 T-SQL 脚本或包含 T-SQL 的 R 代码在 R 脚本中使用。

> [!IMPORTANT]
> （使用 R) 的 azure SQL 数据库机器学习服务当前处于公共预览状态。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>
> 公共预览版是可用于单一数据库和使用基于 vCore 的购买模型中的弹性池**常规用途**并**业务关键**服务层。 此初始公共预览版不支持**超大规模**服务层级和**托管实例**部署选项。 目前，R 是唯一受支持的语言。 目前不支持 Python。
>
> 在预览目前已在以下区域：欧洲西部、 欧洲北部、 美国西部 2、 美国东部、 美国中南部、 美国中北部、 加拿大中部、 亚洲东南部、 印度南部和澳大利亚东南部。
>
> [注册预览版](#signup)（见下）。

## <a name="what-you-can-do-with-r"></a>R 的功能

使用 R 语言的强大功能在数据库中提供高级分析和机器学习。 该功能将计算和处理功能带到了数据所在的位置，不需要通过网络拉取数据。 此外，您可以利用企业 R 包的强大功能，若要大规模提供高级分析功能。

机器学习服务包括了 R 的基本发行版，其中包含 Microsoft 提供的企业 R 包。 Microsoft 的 R 函数和算法是为规模和实用性而设计的，提供预测分析、统计建模、数据可视化以及先进的机器学习算法。

### <a name="r-packages"></a>R 包

最常见的开放源代码 R 包会预安装在机器学习服务。 此外还包括 Microsoft 提供的下述 R 包：

| R 程序包 | 描述|
|-|-|
| [Microsoft R Open](https://mran.microsoft.com/rro) | Microsoft R Open 是 Microsoft 提供的增强型发行版 R。 它是用于统计分析和数据科学的完整的开源平台。 它基于 R 且 100% 兼容 R，并且包括其他可以增强性能和可再现性的功能。 |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | RevoScaleR 是可缩放 R 的主库。该库中的函数属于使用最广泛的函数。 这些库中包含的函数可用于数据转换和操作、统计汇总、可视化以及许多形式的建模和分析。 另外，这些库中的函数可以自动地跨可用核心分发工作负荷，以便对其进行并行分析，并且可以处理由计算引擎协调和管理的大块数据。 |
| [MicrosoftML (R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | MicrosoftML 增加了机器学习算法，可以创建进行文本分析、图像分析和情绪分析的自定义模型。 |

除了预安装的包，你可以[安装其他包](sql-database-machine-learning-services-add-r-packages.md)。

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>注册预览版

若要注册获取公共预览版，请执行以下步骤：

1. 如果还没有 Azure 订阅，可以在开始前[创建一个帐户](https://azure.microsoft.com/free/)。

2. 若要注册获取公共预览版，请向 Microsoft 的 [sqldbml@microsoft.com](mailto:sqldbml@microsoft.com) 发送电子邮件。 SQL 数据库中的机器学习服务（使用 R）的公共预览版不默认启用。

一旦你注册的程序中，Microsoft 也将陆续采用您的公共预览版和启用 R 你为现有或新数据库。

使用 R 的机器学习服务是在公共预览期间不建议生产工作负荷。

## <a name="next-steps"></a>后续步骤

- 请参阅[主要从 SQL Server 机器学习服务的差别](sql-database-machine-learning-services-differences.md)。
- 若要了解如何使用 R 来查询 Azure SQL 数据库机器学习服务 （预览版），请参阅[快速入门指南](sql-database-connect-query-r.md)。
- 若要开始使用一些简单的 R 脚本，请参阅[创建并运行的 Azure SQL 数据库机器学习服务 （预览版） 中的简单 R 脚本](sql-database-quickstart-r-create-script.md)。
