---
title: azcopy |Microsoft Docs
description: 本文提供 azcopy 命令的参考信息。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 4da9206f4500941179d781a0fe2a57ad15d7393d
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195888"
---
# <a name="azcopy"></a>azcopy

AzCopy 是一个命令行工具, 用于将数据移入和移出 Azure 存储。

## <a name="synopsis"></a>概要

命令的常规格式为: `azcopy [command] [arguments] --[flag-name]=[flag-value]`。

若要报告问题或了解有关该工具的详细信息[https://github.com/Azure/azure-storage-azcopy](https://github.com/Azure/azure-storage-azcopy), 请参阅。

## <a name="options"></a>选项

|选项|描述|
|---|---|
|--cap-mbps uint32|以兆位/秒为单位限制传输速率。 每分钟的吞吐量可能与 cap 略有不同。 如果将此选项设置为零, 或省略此选项, 则不会限制吞吐量。|
|-h、--help|显示 azcopy 的帮助内容。|
|--output 类型字符串|命令输出的格式。 选项包括: 文本、json。 默认值为 "text"。|

## <a name="see-also"></a>请参阅

- [AzCopy 入门](storage-use-azcopy-v10.md)
- [azcopy 副本](storage-ref-azcopy-copy.md)
- [azcopy 文档](storage-ref-azcopy-doc.md)
- [azcopy 环境](storage-ref-azcopy-env.md)
- [azcopy 作业](storage-ref-azcopy-jobs.md)
- [azcopy 列表](storage-ref-azcopy-list.md)
- [azcopy 登录](storage-ref-azcopy-login.md)
- [azcopy 注销](storage-ref-azcopy-logout.md)
- [azcopy](storage-ref-azcopy-make.md)
- [azcopy 删除](storage-ref-azcopy-remove.md)
- [azcopy 同步](storage-ref-azcopy-sync.md)
