---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: d1ca6d37d6133786aff7ad3156fea2a0c22dfb97
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173014"
---
#### <a name="to-create-a-cloud-appliance"></a>创建云设备

1. 在 Azure 门户中，转到“StorSimple Device Manager”  服务。
2. 转到“设备”边栏选项卡。  从“服务摘要”边栏选项卡中的命令栏中，单击“创建云设备”。 
    ![StorSimple 创建云设备](./media/storsimple-8000-create-cloud-appliance-u2/sca-create1.png)
3. 在“创建云设备”边栏选项卡中，指定以下详细信息。 
   
    ![StorSimple 创建云设备](./media/storsimple-8000-create-cloud-appliance-u2/sca-create2m.png)
   
   1. **名称** – 云设备的唯一名称。
   2. **型号** – 选择云设备的型号。 8010 设备提供 30 TB 的标准存储，而 8020 提供 64 TB 的高级存储。 指定 8010 来从备份部署项目级别检索方案。 选择 8020 来部署高性能、低延迟工作负荷，或用作灾难恢复的辅助设备。
   3. **版本** - 选择云设备的版本。 该版本对应于用于创建云设备的虚拟磁盘映像的版本。 由于云设备的版本决定了可从其进行故障转移或克隆的物理设备，因此必须创建适当的云设备版本。
   4. **虚拟网络** – 指定要用于此云设备的虚拟网络。 如果使用高级存储，则必须选择支持高级存储帐户的虚拟网络。 不受支持的虚拟网络在下拉列表中呈灰显。 如果选择了不受支持的虚拟网络，则会收到警告。
   5. **子网** - 下拉列表会根据所选的虚拟网络显示关联的子网。 将一个子网分配到云设备。
   6. **存储帐户** – 选择一个存储帐户用于在预配期间保存云设备的映像。 此存储帐户应与云设备和虚拟网络位于同一区域中。 此帐户不应由物理或云设备用于数据存储。 默认情况下会创建一个新的存储帐户来用于数据存储。 但是，如果知道已有适用于存储设备数据的存储帐户，可以从列表中选择。 如果创建高级云设备，则下拉列表中仅显示高级存储帐户。
      
      > [!NOTE]
      > 云设备仅适用于 Azure 存储帐户。
    
   7. 选中表示你已了解云设备中存储的数据将托管在 Microsoft 数据中心的复选框。
       * 仅使用物理设备时，加密密钥将保存在设备中，因此 Microsoft 无法解密。

       * 使用云设备时，加密密钥和解密密钥都存储在 Microsoft Azure 中。 有关详细信息，请参阅[使用云设备的安全注意事项](../articles/storsimple/storsimple-security.md)。
   8. 单击“创建”  以预配云设备。 设备可能需要 30 分钟左右的时间来预配。 成功创建云设备后，将收到通知。 转到“设备”边栏选项卡，设备列表将刷新以显示云设备。 设备状态为“就绪可设置”。 
      
      ![StorSimple 云设备就绪可设置](./media/storsimple-8000-create-cloud-appliance-u2/sca-create3.png)

