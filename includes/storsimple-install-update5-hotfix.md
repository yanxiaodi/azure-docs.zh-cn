---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 19d2dedc2ccf7015696504a94f5ef7c43a90d3be
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173147"
---
#### <a name="to-download-hotfixes"></a>下载修补程序

执行以下步骤，从 Microsoft 更新目录下载软件更新。

1. 启动 Internet Explorer，并转到 [http://catalog.update.microsoft.com](https://catalog.update.microsoft.com)。
2. 如果这是你在此计算机上首次使用 Microsoft 更新目录，请在系统提示是否安装 Microsoft 更新目录外接程序时单击“安装”。 

    ![安装目录](./media/storsimple-install-update2-hotfix/HCS_InstallCatalog-include.png)

3. 在 Microsoft 更新目录的搜索框中，输入要下载的修补程序的知识库 (KB) 编号（例如 **4037264**），并单击“搜索”。 
   
    随后会显示修补程序列表，例如“适用于 StorSimple 8000 系列的累积软件捆绑包更新 5.0”。 
   
    ![搜索目录](./media/storsimple-install-update5-hotfix/update-catalog-search.png)

4. 单击“下载”  。 指定或**浏览**到下载项要保存到的本地位置。 单击要下载到指定位置和文件夹的文件。 也可以将该文件夹复制到可通过设备访问的网络共享位置。
5. 搜索上表中列出的任何其他修补程序 (**4037266**)，将相应的文件下载到上表中列出的特定文件夹。

> [!NOTE]
> 必须能够同时从两个控制器访问修补程序，以便检测来自对等控制器的任何潜在错误消息。
>
> 必须在 3 个不同的文件夹中复制修补程序。 例如，设备软件/Cis/MDS 代理更新可以在 _FirstOrderUpdate_ 文件夹中复制，所有其他非中断性更新可以在 _SecondOrderUpdate_ 文件夹中复制，维护模式更新可以在 _ThirdOrderUpdate_ 文件夹中复制。

#### <a name="to-install-and-verify-regular-mode-hotfixes"></a>安装和验证常规模式修补程序

执行以下步骤安装和验证常规模式修补程序。 如果已使用 Azure 门户安装这些修补程序，请直接跳到[安装和验证维护模式修补程序](#to-install-and-verify-maintenance-mode-hotfixes)。

1. 若要安装修补程序，请访问 StorSimple 设备串行控制台上的 Windows PowerShell 界面。 遵循 [Use PuTTy to connect to the serial console](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console)（使用 PuTTy 连接到串行控制台）中的详细说明。 在命令提示符下，按 **Enter**。
2. 选择选项 1“以完全访问权限登录”  。 建议先在被动控制器中安装修补程序。
3. 若要安装修补程序，请在命令提示符下键入：
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    请在上述命令的共享路径中使用 IP 而不是 DNS。 仅当要访问经过身份验证的共享时，才使用凭据参数。
   
    建议使用凭据参数来访问共享。 即使是向“任何人”开放的共享，通常也不会向未经身份验证的用户开放。
   
4. 根据提示提供密码。 安装第一级更新的示例输出显示在下面。 对于第一级更新，需要指向特定文件。

    >[!NOTE] 
    > 应先安装 _HcsSoftwareUpdate.exe_。 此安装完成后，然后安装 _CisMdsAgentUpdate.exe_。
   
        ```
        Controller0>Start-HcsHotfix -Path \\10.100.100.100\share
        \FirstOrderUpdate\HcsSoftwareUpdate.exe -Credential contoso\John
   
        Confirm
   
        This operation starts the hotfix installation and could reboot one or
        both of the controllers. If the device is serving I/Os, these will not
        be disrupted. Are you sure you want to continue?
        [Y] Yes [N] No [?] Help (default is "Y"): Y
   
        ```
5. 出现确认安装修补程序的提示时，请键入 **Y**。
6. 使用 `Get-HcsUpdateStatus` 监视更新。 先在被动控制器上完成更新。 更新被动控制器之后，将发生故障转移，然后，更新将应用到另一个控制器。 两个控制器都更新后，更新即告完成。
   
    以下示例输出显示更新正在进行。 更新正在进行时，`RunInprogress` 为 `True`。

    ```
    Controller0>Get-HcsUpdateStatus
    RunInprogress       : True
    LastHotfixTimestamp :
    LastUpdateTimestamp : 07/28/2017 2:04:02 AM
    Controller0Events   :
    Controller1Events   :
    ```
   
     以下示例输出指示更新已完成。 更新完成时，`RunInProgress` 为 `False`。
   
    ```
    Controller0>Get-HcsUpdateStatus
    RunInprogress       : False
    LastHotfixTimestamp : 07/28/2017 9:15:55 AM
    LastUpdateTimestamp : 07/28/2017 9:06:07 AM
    Controller0Events   :
    Controller1Events   :
    ```

    > [!NOTE]
    > 当更新仍在进行时，cmdlet 偶尔会报告 `False`。 为了确保完成修补程序更新，请等待几分钟再重新运行此命令，并检查 `RunInProgress` 是否为 `False`。 如果是，则表示修补程序更新完成。

7. 完成软件更新后，请检查系统软件版本。 键入：
   
    `Get-HcsSystem`
   
    应该会看到以下版本：
   
   * `FriendlySoftwareVersion: StorSimple 8000 Series Update 5.0`
   * `HcsSoftwareVersion: 6.3.9600.17845`
   
     如果在应用更新后版本号并未更改，则表示此修补程序未成功应用。 如果出现这种情况，请联系 [Microsoft 支持](../articles/storsimple/storsimple-8000-contact-microsoft-support.md)获取进一步的帮助。
     
     > [!IMPORTANT]
     > 必须先通过 `Restart-HcsController` cmdlet 重启主动控制器，然后应用下一更新。
     
8. 重复步骤 3-6，安装下载到 _FirstOrderUpdate_ 文件夹的 _CisMDSAgentupdate.exe_ 代理。
8. 重复步骤 3-6，安装第二级更新。 

    > [!NOTE] 
    > 对于第二级更新，只需运行 `Start-HcsHotfix cmdlet` 并指向第二级更新所在的文件夹，即可安装多个更新。 该 cmdlet 将执行文件夹中所有可用的更新。 如果更新已安装，则更新逻辑会删除该更新，不应用该更新。

    安装所有修补程序后，请使用 `Get-HcsSystem` cmdlet。 版本应为：
    
    * `CisAgentVersion:  1.0.9724.0`
    * `MdsAgentVersion: 35.2.2.0`
    * `Lsisas2Version: 2.0.78.00`


#### <a name="to-install-and-verify-maintenance-mode-hotfixes"></a>安装和验证维护模式修补程序

使用 KB4037263 安装磁盘固件更新。 这是一些干扰性的更新，大约需要 30 分钟才能完成。 可以选择在计划好的维护时段内，通过连接到设备串行控制台安装这些更新。

> [!NOTE] 
> 如果磁盘固件已是最新版本，则不需要安装这些更新。 从设备串行控制台运行 `Get-HcsUpdateAvailability` cmdlet 检查是否有可用的更新，以及更新是干扰性（维护模式）还是非干扰性（常规模式）更新。

若要安装磁盘固件更新，请遵循以下说明。

1. 将设备置于维护模式。 

    > [!NOTE] 
    > 连接到处于维护模式的设备时，请勿使用 Windows PowerShell 远程功能， 而应该在通过设备串行控制台连接时，在设备控制器上运行此 cmdlet。

    若要将控制器置于维护模式，请键入：
   
    `Enter-HcsMaintenanceMode`
   
    下面显示了示例输出。
   
        Controller0>Enter-HcsMaintenanceMode
        Checking device state...
   
        In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
        [Y] Yes [N] No (Default is "Y"): Y
   
        -----------------------MAINTENANCE MODE------------------------
        Microsoft Azure StorSimple Appliance Model 8600
        Name: Update4-8600-mystorsimple
        Copyright (C) 2014 Microsoft Corporation. All rights reserved.
        You are connected to Controller0 - Passive
        ---------------------------------------------------------------
   
        Serial Console Menu
        [1] Log in with full access
        [2] Log into peer controller with full access
        [3] Connect with limited access
        [4] Change language
        Please enter your choice>
   
    然后，两个控制器将重新启动并进入维护模式。
2. 若要安装磁盘固件更新，请键入：
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    下面显示了示例输出。
   
        Controller1>Start-HcsHotfix -Path \\10.100.100.100\share\ThirdOrderUpdates\ -Credential contoso\john
        Enter Password:
        WARNING: In maintenance mode, hotfixes should be installed on each controller sequentially. After the hotfix is installed on this controller, install it on the peer controller.
        Confirm
        This operation starts a hotfix installation and could reboot one or both of the controllers. By installing new updates you agree to, and accept any additional terms associated with, the new functionality listed in the release notes (https://go.microsoft.com/fwLink/?LinkID=613790). Are you sure you want to continue?
        [Y] Yes [N] No (Default is "Y"): Y
        WARNING: Installation is currently in progress. This operation can take several minutes to complete.
3. 使用 `Get-HcsUpdateStatus` 命令监视安装进度。 当 `RunInProgress` 更改为 `False` 时，即表示更新完成。
4. 安装完成后，安装维护模式修补程序的控制器将重新启动。 使用选项 1“以完全访问权限登录”  登录，并验证磁盘固件版本。 键入：
   
   `Get-HcsFirmwareVersion`
   
   预期的磁盘固件版本为：
   
   `XMGJ, XGEG, KZ50, F6C2, VR08, N003, 0107`
   
   下面显示了示例输出。
   
       -----------------------MAINTENANCE MODE------------------------
       Microsoft Azure StorSimple Appliance Model 8600
       Name: Update4-8600-mystorsimple
       Software Version: 6.3.9600.17845
       Copyright (C) 2014 Microsoft Corporation. All rights reserved.
       You are connected to Controller1
       ---------------------------------------------------------------
   
       Controller1>Get-HcsFirmwareVersion
   
       Controller0 : TalladegaFirmware
           ActiveBIOS:0.45.0010
              BackupBIOS:0.45.0006
              MainCPLD:17.0.000b
              ActiveBMCRoot:2.0.001F
              BackupBMCRoot:2.0.001F
              BMCBoot:2.0.0002
              LsiFirmware:20.00.04.00
              LsiBios:07.37.00.00
              Battery1Firmware:06.2C
              Battery2Firmware:06.2C
              DomFirmware:X231600
              CanisterFirmware:3.5.0.56
              CanisterBootloader:5.03
              CanisterConfigCRC:0x9134777A
              CanisterVPDStructure:0x06
              CanisterGEMCPLD:0x19
              CanisterVPDCRC:0x142F7DC2
              MidplaneVPDStructure:0x0C
              MidplaneVPDCRC:0xA6BD4F64
              MidplaneCPLD:0x10
              PCM1Firmware:1.00|1.05
              PCM1VPDStructure:0x05
              PCM1VPDCRC:0x41BEF99C
              PCM2Firmware:1.00|1.05
              PCM2VPDStructure:0x05
              PCM2VPDCRC:0x41BEF99C

           EbodFirmware
              CanisterFirmware:3.5.0.56
              CanisterBootloader:5.03
              CanisterConfigCRC:0xB23150F8
              CanisterVPDStructure:0x06
              CanisterGEMCPLD:0x14
              CanisterVPDCRC:0xBAA55828
              MidplaneVPDStructure:0x0C
              MidplaneVPDCRC:0xA6BD4F64
              MidplaneCPLD:0x10
              PCM1Firmware:3.11
              PCM1VPDStructure:0x03
              PCM1VPDCRC:0x6B58AD13
              PCM2Firmware:3.11
              PCM2VPDStructure:0x03
              PCM2VPDCRC:0x6B58AD13

           DisksFirmware
              SmrtStor:TXA2D20800GA6XYR:KZ50
              SmrtStor:TXA2D20800GA6XYR:KZ50
              SmrtStor:TXA2D20800GA6XYR:KZ50
              SmrtStor:TXA2D20800GA6XYR:KZ50
              SmrtStor:TXA2D20800GA6XYR:KZ50
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
   
    在第二个控制器上运行 `Get-HcsFirmwareVersion` 命令，验证软件版本是否已更新。 然后即可退出维护模式。 为此，请针对每个设备控制器键入以下命令：
   
   `Exit-HcsMaintenanceMode`

5. 退出维护模式时，控制器会重新启动。 成功应用磁盘固件更新并且设备退出维护模式后，将返回 Azure 门户。 请注意，门户在 24 小时内可能不会显示已安装维护模式更新。

