---
title: 检查自定义语音语音服务的数据质量
titleSuffix: Azure Cognitive Services
description: 自定义语音提供的工具可让你通过将音频数据与相应的识别结果进行比较，直观地检查模型的识别质量。 在自定义语音门户中，可以播放上传的音频，并确定所提供的识别结果是否正确。  使用此工具，可以快速检查基线语音到文本模型或定型自定义模型的质量，无需转录任何音频数据。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: 0b5f064204c5bca35ccb1a628cb06978180c318c
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/08/2019
ms.locfileid: "70801457"
---
# <a name="inspect-custom-speech-data"></a>检查自定义语音数据

> [!NOTE]
> 此页假定您已阅读[自定义语音的准备测试数据](how-to-custom-speech-test-data.md)，并已上传用于检查的数据集。

自定义语音提供的工具可让你通过将音频数据与相应的识别结果进行比较，直观地检查模型的识别质量。 在[自定义语音门户](https://speech.microsoft.com/customspeech)中，可以播放上传的音频，并确定所提供的识别结果是否正确。 使用此工具，可以快速检查 Microsoft 的基线语音到文本模型或定型自定义模型的质量，无需转录任何音频数据。

在本文档中，你将学习如何使用之前上传的定型数据以视觉方式检查模型的质量。

在此页上，你将学习如何以视觉方式检查 Microsoft 的基线语音到文本模型和/或你训练的自定义模型的质量。 你将使用上载到 "**数据**" 选项卡的数据进行测试。

## <a name="create-a-test"></a>创建测试

按照以下说明创建测试：

1. 登录到[自定义语音门户](https://speech.microsoft.com/customspeech)。
2. 导航到 "**语音到文本" > 自定义语音 > 测试**。
3. 单击 "**添加测试**"。
4. 选择 "**检查质量" （仅音频数据）** 。 为测试指定名称、说明，并选择音频数据集。
5. 最多选择两个想要测试的模型。
6. 单击“创建”。

成功创建测试后，可以并排比较模型。

## <a name="side-by-side-model-comparisons"></a>并行模型比较

当测试状态为 "*成功*" 时，单击测试项名称以查看测试的详细信息。 此详细信息页列出了数据集中的所有最谈话，指明了两种模型的识别结果以及提交的数据集的脚本。

若要帮助检查并排比较，可以切换各种错误类型，包括插入、删除和替换。 通过倾听音频，并比较每个列中的识别结果（显示人为标记的脚本和两个语音到文本模型的结果），您可以决定哪种模型满足您的需求，以及需要改进的地方。

检查质量测试有助于验证语音识别终结点的质量是否足以满足应用程序的需要。  要获得精确的目标度量值，需要转录的音频，请按照[评估准确性](how-to-custom-speech-evaluate-data.md)中的说明进行操作。

## <a name="next-steps"></a>后续步骤

* [评估数据](how-to-custom-speech-evaluate-data.md)
* [训练模型](how-to-custom-speech-train-model.md)
* [部署模型](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>其他资源

* [准备自定义语音的测试数据](how-to-custom-speech-test-data.md)
