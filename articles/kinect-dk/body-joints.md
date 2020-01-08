---
title: Azure Kinect 人体跟踪关节
description: 使用人体帧和关节
author: qm13
ms.author: quentinm
ms.reviewer: cedmonds, abalan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, 移植, 人体, 跟踪, 关节, 层次结构, 骨骼, 连接
ms.openlocfilehash: 286252bbddc5b02e18d2d06ecb18b8876f9d6434
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232456"
---
# <a name="azure-kinect-body-tracking-joints"></a>Azure Kinect 人体跟踪关节

Azure Kinect 人体跟踪可以同时跟踪多个人体。 每个人体包括帧与运动骨架之间的时态关联的 ID。 可以使用 `k4abt_frame_get_num_bodies()` 获取在每个帧中检测到的人体数。

## <a name="joints"></a>关节

关节位置和方向是相对于全局深度传感器参考帧的估算值。 位置以毫米为单位指定。 方向以规范化四元数表示。

## <a name="joint-coordinates"></a>关节坐标

每个关节的位置和方向构成了其自身的关节坐标系。 所有关节坐标系是相对于深度相机 3D 坐标系的绝对坐标系。

![关节坐标](./media/concepts/joint-coordinates.png)

## <a name="joint-hierarchy"></a>关节层次结构

骨架包括 26 个关节，层次结构按照从人体中心向四肢的流向分布。 每个连接（骨骼）将父关节与子关节链接起来。 该图演示了关节位置以及相对于人体的连接。

![关节层次结构](./media/concepts/joint-hierarchy.png)

下表列举了标准的关节连接。

|索引 |关节名称     | 父关节   |
|------|---------------|----------------|
| 0    |PELVIS         | -              |
| 1    |SPINE_NAVAL    | PELVIS         |
| 2    |SPINE_CHEST    | SPINE_NAVAL    |
| 3    |NECK           | SPINE_CHEST    |
| 4    |CLAVICLE_LEFT  | SPINE_CHEST    |
| 5    |SHOULDER_LEFT  | CLAVICLE_LEFT  |
| 6    |ELBOW_LEFT     | SHOULDER_LEFT  |
| 7    |WRIST_LEFT     | ELBOW_LEFT     |
| 8    |CLAVICLE_RIGHT | SPINE_CHEST    |
| 9    |SHOULDER_RIGHT | CLAVICLE_RIGHT |
| 10   |ELBOW_RIGHT    | SHOULDER_RIGHT |
| 11   |WRIST_RIGHT    | ELBOW_RIGHT    |
| 12   |HIP_LEFT       | PELVIS         |
| 13   |KNEE_LEFT      | HIP_LEFT       |
| 14   |ANKLE_LEFT     | KNEE_LEFT      |
| 15   |FOOT_LEFT      | ANKLE_LEFT     |
| 16   |HIP_RIGHT      | PELVIS         |
| 17   |KNEE_RIGHT     | HIP_RIGHT      |
| 18   |ANKLE_RIGHT    | KNEE_RIGHT     |
| 19   |FOOT_RIGHT     | ANKLE_RIGHT    |
| 20   |HEAD           | NECK           |
| 21   |NOSE           | HEAD           |
| 22   |EYE_LEFT       | HEAD           |
| 23   |EAR_LEFT       | HEAD           |
| 24   |EYE_RIGHT      | HEAD           |
| 25   |EAR_RIGHT      | HEAD           |

## <a name="next-steps"></a>后续步骤

[人体跟踪索引映射](body-index-map.md)
