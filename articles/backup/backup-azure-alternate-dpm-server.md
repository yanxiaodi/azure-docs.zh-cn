---
title: 从 Azure 备份服务器恢复数据
description: 将所保护的数据从任意 Azure 备份服务器恢复到恢复服务保管库，前提是服务器已注册到该保管库。
ms.reviewer: kasinh
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: dacurwin
ms.openlocfilehash: 0a6d1fd73d99cf15137e937dbfe2336d49a63d90
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955046"
---
# <a name="recover-data-from-azure-backup-server"></a>从 Azure 备份服务器恢复数据
可使用 Azure 备份服务器恢复已备份到恢复服务保管库的数据。 用于执行此操作的过程已集成到 Azure 备份服务器管理控制台中，且与其他 Azure 备份组件的恢复工作流类似。

> [!NOTE]
> 本文适用于搭载了[最新 Azure 备份代理](https://aka.ms/azurebackup_agent)的包含 UR7 的 [System Center Data Protection Manager 2012 R2 或更高版本](https://support.microsoft.com/en-us/kb/3065246)。
>
>

若要从 Azure 备份服务器恢复数据，请执行以下操作：

1. 在 Azure 备份服务器管理控制台的“恢复”选项卡中，单击“添加外部 DPM”（位于屏幕左上角）。   
    ![添加外部 DPM](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)
2. 从与要恢复数据的“Azure 备份服务器”关联的保管库下载新的“保管库凭据”，从注册到恢复服务保管库的 Azure 备份服务器列表中选择 Azure 备份服务器，并提供与要恢复数据的服务器关联的“加密密码”。

    ![外部 DPM 凭据](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

   > [!NOTE]
   > 仅与同一注册保管库关联的 Azure 备份服务器可以恢复彼此的数据。
   >
   >

    成功添加外部 Azure 备份服务器以后，可以从“恢复”选项卡浏览外部服务器和本地 Azure 备份服务器的数据。
3. 浏览受外部 Azure 备份服务器保护的生产服务器的可用列表，并选择适当的数据源。

    ![浏览外部 DPM 服务器](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)
4. 从“**恢复点**”下拉列表中选择“**年份和月份**”，并选择所需的“**恢复日期**”（对应于恢复点的创建时间），再选择“**恢复时间**”。

    将在底部窗格中显示文件和文件夹的列表，可以浏览这些文件和文件夹并将其恢复到任何位置。

    ![外部 DPM 服务器恢复点](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)
5. 右键单击相应的项目, 然后单击 "**恢复**"。

    ![外部 DPM 恢复](./media/backup-azure-alternate-dpm-server/recover.png)
6. 查看“**恢复所选内容**”。 验证要恢复的备份副本的数据和时间，以及创建备份副本时所依据的源。 如果所选内容不正确，请单击“**取消**”，导航回恢复选项卡，并选择适当的恢复点。 如果所选内容正确无误，请单击“**下一步**”。

    ![外部 DPM 恢复摘要](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)
7. 选择“**恢复到另一个位置**”。 **浏览**到进行恢复的正确位置。

    ![外部 DPM 恢复备用位置](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)
8. 选择与“**创建副本**”、“**跳过**”或“**覆盖**”相关的选项。

   * **创建副本**- 在存在名称冲突时创建文件副本。
   * **Skip** -如果存在名称冲突, 则不恢复保留原始文件的文件。
   * **覆盖**- 存在名称冲突时，覆盖文件的现有副本。

     选择与“**还原安全**”相对应的选项。 可以应用进行数据恢复的目标计算机的安全设置，也可以应用在创建恢复点时适用于产品的安全设置。

     确定是否在恢复成功完成后发送“通知”。

     ![外部 DPM 恢复通知](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)
9. “**摘要**”屏幕列出了目前为止的所选选项。 单击“恢复”时，数据将恢复到相应的本地位置。

    ![外部 DPM 恢复选项摘要](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

   > [!NOTE]
   > 可以在 Azure 备份服务器的“监视”选项卡中监视恢复作业。
   >
   >

    ![监视恢复](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)
10. 单击 DPM 服务器的“**恢复**”选项卡上的“**清除外部 DPM**”，即可删除外部 DPM 服务器的视图。

    ![清除外部 DPM](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## <a name="troubleshooting-error-messages"></a>错误消息疑难解答
| 没有。 | 错误消息 | 疑难解答步骤 |
|:---:|:--- |:--- |
| 1. |此服务器未注册到保管库凭据所指定的保管库。 |原因：当所选保管库凭据文件不属于与 Azure 备份服务器（在其上进行恢复尝试）关联的恢复服务保管库时，会出现此错误。 <br> **解决方法：** 从 Azure 备份服务器所注册到的恢复服务保管库中下载保管库凭据文件。 |
| 2. |可恢复的数据不可用，或所选服务器不是 DPM 服务器。 |原因：没有其他 Azure 备份服务器注册到恢复服务保管库或服务器尚未上传元数据，或者所选服务器不是 Azure 备份服务器（使用 Windows Server 或 Windows Client）。 <br> **解决方法：** 如果有其他 Azure 备份服务器注册到了恢复服务保管库，请确保安装了最新 Azure 备份代理。 <br>如果有其他 Azure 备份服务器注册到了恢复服务保管库，请等到安装之后的某一天来启动恢复过程。 每夜执行的作业会将所有受保护的备份的元数据上传到云。 数据将可用于恢复。 |
| 3. |没有其他 DPM 服务器注册到此保管库中。 |原因：没有其他 Azure 备份服务器注册到了正在从其尝试恢复的保管库。<br>**解决方法：** 如果有其他 Azure 备份服务器注册到了恢复服务保管库，请确保安装了最新 Azure 备份代理。<br>如果有其他 Azure 备份服务器注册到了恢复服务保管库，请等到安装之后的某一天来启动恢复过程。 每夜执行的作业会将所有受保护的备份的元数据上传到云。 数据将可用于恢复。 |
| 4. |提供的加密密码与以下服务器的关联密码不匹配： **\<server name>** |原因：用于 Azure 备份服务器的恢复数据加密流程的加密密码与所提供的加密密码不匹配。 代理不能对数据进行解密。 因此恢复失败。<br>**解决方法：** 请确保提供的加密密码与要进行数据恢复的 Azure 备份服务器的相关加密密码相同。 |

## <a name="next-steps"></a>后续步骤

阅读其他常见问题：

- 有关 Azure VM 备份的[常见问题](backup-azure-vm-backup-faq.md)
- 有关 Azure 备份代理的[常见问题](backup-azure-file-folder-backup-faq.md)
