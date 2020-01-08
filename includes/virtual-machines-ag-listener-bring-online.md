---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 760bb5b62e9bba9b7a83f99760f7fe5d8c399dfb
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172901"
---
1. 在故障转移群集管理器中，展开“角色”  ，并突出显示可用性组。  

2. 在“资源”选项卡上，右键单击侦听程序名称，然后单击“属性”。  

3. 选择“依赖项”选项卡。  如果列出了多个资源，请验证 IP 地址具有 OR 而不是 AND 依赖项。  

4. 单击“确定”。 

5. 右键单击侦听器名称，并单击“联机”。 

6. 侦听程序处于联机状态后，请在“资源”选项卡上右键单击可用性组，然后单击“属性”。  
   
    ![配置可用性组资源](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678772.gif)

7. 在侦听器名称资源（而不是 IP 地址资源名称）上创建依赖项，并单击“确定”。 
   
    ![在侦听器名称上添加依赖项](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678773.gif)

8. 启动 SQL Server Management Studio，然后连接到主副本。

9. 转到“AlwaysOn 高可用性”   > “可用性组”   >  **\<AvailabilityGroupName\>**  >   “可用性组侦听器”。  
    此时会显示在故障转移群集管理器中创建的侦听器名称。

10. 右键单击侦听程序名称，然后单击“属性”。 

11. 在“端口”框中，通过使用先前使用过的 $EndpointPort 为可用性组侦听器指定端口号（在本教程中，默认值是 1433），并单击“确定”。  

