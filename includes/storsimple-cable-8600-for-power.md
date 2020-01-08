---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 9b9922602218280d58331a755ed0dfed7df96f40
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172986"
---
#### <a name="to-cable-your-device-for-power"></a>为设备进行电源布线
> [!NOTE]
> StorSimple 设备上的两个机箱都包括多余的 PCM。 对于每个机箱，PCM 必须安装并连接到不同电源，以确保高可用性。
> 
> 

1. 请确保所有 PCM 上的电源开关都处于“关”位置。
2. 在主机箱上，将电源线连接到两个 PCM。 在下面的电源布线图中，电源线用红色进行标识。
3. 请确保主机箱上的两个 PCM 使用单独的电源。
4. 将电源线连接到机架配电装置的电源，如电源布线图中所示。
5. 针对 EBOD 机箱重复步骤 2 到 4。
6. 通过将每个 PCM 的电源开关都切换到“开”位置，打开 EBOD 机箱。
7. 确认 EBOD 机箱已打开，方法是检查 EBOD 控制器背部的绿色 LED 是否已亮起。
8. 通过将每个 PCM 开关都切换到“开”位置，打开主机箱。
9. 确认系统已打开，方法是确保设备控制器 LED 已亮起。
10. 确保 EBOD 控制器和设备控制器之间的连接已激活，方法是确认 EBOD 控制器上 SAS 端口旁的四个 LED 为绿色。
    
    > [!IMPORTANT]
    > 要确保系统的高可用性，我们建议严格遵循下图所示的电源布线方案。
    > 
    > 
    
    ![为 4U 设备进行电源布线](./media/storsimple-cable-8600-for-power/HCSCableYour4UDeviceforPower.png)
    
    **电源布线**
    
    | Label | 描述 |
    |:--- |:--- |
    | 第 |主机箱 |
    | 2 |PCM 0 |
    | 3 |PCM 1 |
    | 4 |控制器 0 |
    | 5 |控制器 1 |
    | 6 |EBOD 控制器 0 |
    | 7 |EBOD 控制器 1 |
    | 8 |EBOD 机箱 |
    | 9 |PDU |

