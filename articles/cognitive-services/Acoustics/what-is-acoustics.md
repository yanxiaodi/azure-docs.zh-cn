---
title: 项目音响效果概述
titlesuffix: Azure Cognitive Services
description: 项目音响效果是 3D 交互式体验的音效引擎，将烘焙生成的波物理模拟与交互式设计控件相集成。
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: overview
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 51bfcc47961e870fb7fb87b26a78aea0f1564d46
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390309"
---
# <a name="what-is-project-acoustics"></a>什么是项目音响效果？
项目音响效果是 3D 交互式体验的波形音效引擎。 它可对复杂场景中的声障、声笼、传送门和混响效果进行建模而无需手动区域标记或 CPU 密集型光线跟踪。 它还包括游戏引擎和音频中间件集成。 项目音响效果的原理类似于静态照明：脱机烘焙出具体的物理数据以提供物理基线，并将轻型运行时与表达式设计控件结合使用来满足虚拟世界音响效果的艺术需要。

![显示音效体素的《战争机器 4》的屏幕截图](media/gears-with-voxels.jpg)

## <a name="using-wave-physics-for-interactive-acoustics"></a>使用交互式音响效果的波物理数据
基于射线的音响效果方法可使用单个“源到侦听器”光线投射来检查闭合，或使用一些射线来估算本地场景音量，从而驱动混响效果。 但这些技术可能不可靠，因为小颗砾石产生的闭合与巨石无异。 射线不影响声音在对象周围的弯散方式，该方式称为衍射。 项目音响效果模拟使用基于波的模拟来捕获这些效果。 这些音响效果更易于预测、准确且无缝。

项目音响效果的关键创新在于将基于实际声波的音效模拟与传统声音设计概念相结合。 它将传统模拟结果转换为衍射、传送门和混响效果的音频 DSP 参数。 设计器对此转换过程使用控件。 有关项目音响效果背后核心技术的更多详细信息，请访问[研究项目页](https://www.microsoft.com/en-us/research/project/project-triton/)。

![此动画显示通过场景的波传播的水平 2D 切片](media/wave-simulation.gif)

## <a name="video-presentation-from-gdc-2019-30-min"></a>GDC 2019 的视频演示（大约 30 分钟）
[![项目音响效果视频](https://img.youtube.com/vi/uY4G-GUAQIE/0.jpg)](https://www.youtube.com/watch?v=uY4G-GUAQIE "单击以播放视频")

## <a name="setup"></a>设置
[项目音响效果 Unity 集成](unity-integration.md)是拖放式过程，包括一个 Unity 音频引擎插件。 通过将项目音响效果 C# 控件组件附加到每个音频对象来扩充 Unity 音频源控件。

[项目音响效果 Unreal 集成](unreal-integration.md)包括编辑器和 Unreal 的游戏插件，以及一个 Wwise 混音器插件。 自定义音频组件利用实时音效设计控件来扩展 Unreal 中的常见 Wwise 功能。 设计控件还在 Wwise 中的混音器插件上公开。

## <a name="workflow"></a>工作流
* **预先烘焙：** 先设置烘焙，方法是选择哪个几何图形会对音效进行响应（如忽略光轴）。 然后编辑自动材料分配和选择导航区域以指导侦听器进行采样。 不存在混响/传送门/空间区域的手动标记。
* **烘培：** 本地运行一个分析步骤，会基于上面所做的选择对场景执行体素化操作和其他几何分析。 会在编辑器中直观显示结果来验证场景设置。 在提交烘焙时，会向 Azure 发送 voxel 数据，并取回音效游戏资产。
* **运行时：** 将资产加载到自己的级别，且已准备好侦听所在级别中的音效。 使用基于源的精细化控件在编辑器中实时设计音效。 还可以从级别脚本驱动控件。

## <a name="runtime-platforms"></a>运行时平台
当前可以将项目音响效果运行时插件部署到以下平台：
* Windows
* MacOS
* Android
* Xbox One

## <a name="editor-platforms"></a>编辑器平台
“项目音响效果”编辑器插件适用于以下平台：
* Windows
* MacOS（仅限 Unity）

## <a name="download"></a>下载
* [项目音响效果 Unity 插件和示例](https://www.microsoft.com/en-us/download/details.aspx?id=57346)
* [项目音响效果 Unreal 与 Wwise 插件和示例](https://www.microsoft.com/download/details.aspx?id=58090)
  * 如需 Xbox 二进制文件和相关支持，请通过下面的注册表单与我们联系

## <a name="contact-us"></a>联系我们
* [项目音响效果讨论和问题报告](https://github.com/microsoft/ProjectAcoustics/issues)
* [注册以接收项目音响效果更新](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u)

## <a name="next-steps"></a>后续步骤
* 试用 [Unity 项目音响效果快速入门](unity-quickstart.md)或 [Unreal](unreal-quickstart.md) 项目音响效果快速入门
* 了解[项目音响效果的声音设计理念](design-process.md)

