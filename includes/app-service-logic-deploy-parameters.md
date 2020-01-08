---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: f188f2c7bea511f1109d37ef49563e0f745a770e
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2019
ms.locfileid: "68385746"
---
使用 Azure 资源管理器，可以在部署模板时为要使用的值定义参数。 模板包括 `parameters` 节，其中包含所有参数值。 模板使用每个参数值定义要部署的资源。

> [!NOTE]
> 不要为始终保持不变的值定义参数。 仅为随着要部署的项目或要部署到的环境而变化的值定义参数。

定义参数时：

* 若要指定用户可以在部署过程中提供的允许值，请使用 **allowedValues** 字段。

* 若要在部署过程中未提供值的情况下为参数分配默认值，请使用 **defaultValue** 字段。 
