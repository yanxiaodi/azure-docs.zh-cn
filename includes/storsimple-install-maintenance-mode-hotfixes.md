---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 2d0fd904dd704c704662192e1e92fe403f0971c5
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172980"
---
#### <a name="to-install-maintenance-mode-hotfixes-via-windows-powershell-for-storsimple"></a>通过 Windows PowerShell for StorSimple 安装维护模式修补程序
> [!IMPORTANT]
> 在维护模式下，需要依次对两个控制器应用修补程序。
> 
> 

1. 将设备置于维护模式下。 请参阅[步骤 2：进入维护模式](../articles/storsimple/storsimple-update-device.md#step2)，获取有关如何进入维护模式的说明。
2. 若要应用修补程序，请键入：
   
     `Start-HcsHotfix` 
3. 出现提示时，提供包含修补程序文件的网络共享文件夹的路径。
4. 系统会提示进行确认。 键入 **Y** 继续进行修补程序安装。
5. 在其中一个控制器上应用修补程序之后，登录到另一个控制器。 像对之前的控制器那样应用修补程序。
6. 在应用修补程序后，退出维护模式。 请参阅[步骤 4：退出维护模式](../articles/storsimple/storsimple-update-device.md#step4)，获取有关说明。

