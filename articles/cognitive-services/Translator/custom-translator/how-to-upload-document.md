---
title: 如何上传文档 - 自定义翻译
titleSuffix: Azure Cognitive Services
description: 使用文档上传功能可以上传用于训练的并行文档。 并行文档是配对的文档，其中的一个文档是另一个文档的翻译。 该对中的一个文档包含采用源语言的句子，另一个文档包含这些句子的目标语言翻译。
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: f2bd6103c27d455265ee967554fb27513f78a472
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595646"
---
# <a name="upload-a-document"></a>上传文档

在[自定义翻译](https://portal.customtranslator.azure.ai)中，可以上传并行文档来训练翻译模型。 [并行文档](what-are-parallel-documents.md)是配对的文档，其中的一个文档是另一个文档的翻译。 该对中的一个文档包含采用源语言的句子，另一个文档包含这些句子的目标语言翻译。

在上传文档之前，请查看[文档格式和命名约定指南](document-formats-naming-convention.md)，以确保自定义翻译支持你的文件格式。

## <a name="how-to-upload-document"></a>如何上传文档？

在[自定义翻译](https://portal.customtranslator.azure.ai)门户中，单击“文档”选项卡转到文档页。

![文档上传链接](media/how-to/how-to-upload-1.png)


1.  单击文档页上的“上传文件”按钮。

    ![上传文档的页面](media/how-to/how-to-upload-2.png)

2.  在对话框中填充以下信息：

    a.  文档类型：

    -  训练：这些文档将用于训练集。
    -  优化：这些文档将用于优化集。
    -  测试：这些文档将用于测试集。
    -  短语字典：这些文档将用于短语字典。
    -  句子字典：这些文档将用于句子字典

    b.  语言对

    c.  替代文档(如果存在)：若要覆盖同名的所有现有文档，请选中此复选框。

    d.  填写并行数据或组合数据的相关部分。

    -  并行数据：
        -  源文件：从本地计算机中选择源语言文件。
        -  目标文件：从本地计算机中选择目标语言文件。
        -  文档名：仅当上传并行文件时才使用。

    - 组合数据：
        -  组合文件：从本地计算机中选择组合文件。 组合文件同时包含源语言句子和目标语言句子。 [命名约定](document-formats-naming-convention.md)对于组合文件而言非常重要。

    e.  单击“上传”

    ![上传文档的对话框](media/how-to/how-to-upload-dialog.png)

3.  目前，我们正在处理你的文档并尝试提取句子。 你可以单击“查看上传进度”来查看文档的处理状态。

    ![显示正在处理上传文档的对话框](media/how-to/how-to-upload-processing-dialog.png)

4.  此页将显示上传的每个文件的处理状态以及任何错误。 随时可以单击“上传历史记录”选项卡查看以往的上传状态。

    ![上传文档历史记录对话框](media/how-to/how-to-upload-document-history.png)


## <a name="view-upload-history"></a>查看上传历史记录

在上传历史记录页中，可以查看所有上传文档的历史记录详细信息，例如文档类型、语言对、上传状态，等等。

1. 在[自定义翻译](https://portal.customtranslator.azure.ai)门户中，单击“上传历史记录”选项卡查看历史记录。

    ![“上传历史记录”选项卡](media/how-to/how-to-upload-history-1.png)

2. 此页显示以往所有上传内容的状态。 其中按最近到最早的时间顺序显示上传内容。 对于每个上传内容，此页会显示文档名称、上传状态、上传日期、上传的文件数、上传的文件类型，以及文件的语言对。

    ![上传历史记录页](media/how-to/how-to-document-history-2.png)

3. 单击任一上传历史记录。 在上传历史记录详细信息页中，可以查看上传过程中上传的文件、上传文件的状态、文件的语言和错误消息（如果上传内容包含任何错误）。

## <a name="next-steps"></a>后续步骤

- 使用[文档详细信息页](how-to-view-document-details.md)查看提取的句子列表。
- [如何训练模型](how-to-train-model.md)。
