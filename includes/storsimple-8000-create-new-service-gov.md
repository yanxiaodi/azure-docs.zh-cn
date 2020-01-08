---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 6939553206fe223d8166368d2963f03b32d2629e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173004"
---
#### <a name="to-create-a-new-service"></a>创建新服务
1. 使用 Microsoft 帐户凭据登录 [Microsoft Azure 政府门户](https://portal.azure.us/)。
2. 在政府门户中，单击 **+** ，然后在市场中单击“查看所有”。  搜索“StorSimple 物理”。  选择并单击“StorSimple 物理设备系列”，单击“创建”。   或者，在政府门户中，单击 **+** ，然后在“存储”下单击“StorSimple 物理设备系列”。  
3. 在“StorSimple 设备管理器”  边栏选项卡中，执行以下步骤：
   
   1. 为服务提供唯一“资源名称”。  此名称是可用来标识服务的友好名称。 名称可以为 2 到 50 个字符，包括字母、数字和连字符。 名称必须以字母或数字开头和结尾。
   2. 从下拉列表中选择一个“订阅”  。 该订阅将链接到计费帐户。 如果只有一个订阅，此字段不存在。
   3. 对于“资源组”，请使用现有组或创建新组。    有关详细信息，请参阅 [Azure 资源组](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/)。
   4. 为服务提供“位置”  。 位置是指要部署设备的地理区域。 选择“USGov Iowa”或“USGov Virginia”   。
   5. 选择“新建存储帐户”  自动通过该服务创建存储帐户。 为此存储帐户指定名称。 如果需要将数据存储在不同位置，请取消选中此框。
   6. 如果希望在仪表板上显示此服务的快速链接，请选中“固定到仪表板”。 
   7. 单击“创建”以创建 StorSimple 设备管理器。  创建服务需要几分钟时间。 在服务成功创建后，会发出一条通知并且会打开新的服务边栏选项卡。


