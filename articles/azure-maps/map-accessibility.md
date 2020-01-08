---
title: 使用 Azure Maps 构建可访问的应用程序 | Microsoft Docs
description: 如何使用 Azure Maps 构建可访问的应用程序
services: azure-maps
author: chgennar
ms.author: chgennar
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
manager: timlt
ms.openlocfilehash: 8865027c895be09150797608e43184f1fdefb267
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638788"
---
# <a name="building-an-accessible-application"></a>构建可访问的应用程序

本文介绍如何构建可由屏幕阅读器使用的地图应用程序。

## <a name="understand-the-code"></a>了解代码

<iframe height='500' scrolling='no' title='构建可访问的应用程序' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>构建可访问的应用程序</a>。
</iframe>

地图是使用某些辅助功能预构建的。 用户可以使用键盘导航地图。 如果屏幕阅读器正在运行，地图会将其状态的更改通知用户。
例如，在平移或缩放地图时，系统将地图的更改告知用户。 基本地图上的任何附加信息应具有适用于屏幕阅读器用户的相应文本信息。

使用[弹出项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)是实现此目标的一种方法。 在上述搜索示例中，具有文本信息的弹出项将添加到地图上每个 PIN 对应的地图。 使用[弹出项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)的[附加](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#attach)方法可通过屏幕阅读器查看弹出项，而不直观显示地图上的弹出项。

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的弹出项类及其方法：

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

查看更多代码示例：

> [!div class="nextstepaction"]
> [代码示例页](https://aka.ms/AzureMapsSamples)
