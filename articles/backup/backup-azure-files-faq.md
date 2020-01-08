---
title: 备份 Azure 文件常见问题解答
description: 本文详述如何保护 Azure 文件共享。
author: dcurwin
ms.author: dacurwin
ms.date: 07/29/2019
ms.topic: tutorial
ms.service: backup
manager: carmonm
ms.openlocfilehash: 05b591137a53e60b3197feb7f57564a8d4af7a44
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624283"
---
# <a name="questions-about-backing-up-azure-files"></a>有关如何备份 Azure 文件的问题
本文回答了有关如何备份 Azure 文件的常见问题。 某些答案提供内含全面信息的文章的链接。 也可以在 [论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup)中发布有关 Azure 备份服务的问题。

若要快速浏览本文的各个部分，请使用右侧“本文内容”  下的链接。

## <a name="configuring-the-backup-job-for-azure-files"></a>为 Azure 文件配置备份作业

### <a name="why-cant-i-see-some-of-my-storage-accounts-i-want-to-protect-that-contain-valid-azure-file-shares-br"></a>为什么我看不到需要保护的部分存储帐户？这些帐户中包含的 Azure 文件共享都是有效的。 <br/>
在预览期间，Azure 文件共享的备份并不支持所有类型的存储帐户。 请参阅[此处](troubleshoot-azure-files.md#limitations-for-azure-file-share-backup-during-preview)的列表，了解一系列受支持的存储帐户。 还有一种可能是，要查找的存储帐户已受保护或已注册到另一保管库中。 从保管库中[注销](troubleshoot-azure-files.md#configuring-backup)可发现其他保管库中要保护的存储帐户。

### <a name="why-cant-i-see-some-of-my-azure-file-shares-in-the-storage-account-when-im-trying-to-configure-backup-br"></a>尝试配置备份时，为什么看不到存储帐户中的部分 Azure 文件共享？ <br/>
检查是否已在同一恢复服务保管库中对 Azure 文件共享进行保护，或者已在最近将其删除。

### <a name="can-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync-br"></a>是否可以保护已连接到 Azure 文件同步中的某个同步组的文件共享？ <br/>
是的。 保护连接到同步组的 Azure 文件共享这一功能已启用，在公共预览版中提供。

### <a name="when-trying-to-back-up-file-shares-i-clicked-on-a-storage-account-for-discovering-the-file-shares-in-it-however-i-did-not-protect-them-how-do-i-protect-these-file-shares-with-any-other-vault"></a>我在尝试备份文件共享时，单击了某个存储帐户，看能否发现其中的文件共享。 但是，我发现自己无法对其进行保护。 如何使用其他保管库来保护这些文件共享？
尝试进行备份时，如果选择一个要发现其中的文件共享的存储帐户，则会将该存储帐户注册到在其中执行此操作的保管库。 如果选择使用其他保管库来保护文件共享，则请从该保管库[注销](troubleshoot-azure-files.md#configuring-backup)所选存储帐户。

### <a name="can-i-change-the-vault-to-which-i-back-up-my-file-shares"></a>是否可以更改将文件共享备份到的保管库？
是的。 但是，需要先在连接的保管库中[停止保护](backup-azure-files.md#stop-protecting-an-azure-file-share)，[注销](troubleshoot-azure-files.md#configuring-backup)此存储帐户，然后在另一保管库中对其进行保护。

### <a name="in-which-geos-can-i-back-up-azure-file-shares-br"></a>可以在哪些地理区域备份 Azure 文件共享？ <br/>
Azure 文件共享备份目前为预览版，只在以下地理区域提供：
- 澳大利亚东部 (AE)
- 澳大利亚东南部 (ASE)
- 巴西南部 (BRS)
- 加拿大中部 (CNC)
- 加拿大东部 (CE)
- 美国中部 (CUS)
- 东亚 (EA)
- 美国东部 (EUS)
- 美国东部 2 (EUS2)
- 日本东部 (JPE)
- 日本西部 (JPW)
- 印度中部 (INC)
- 印度南部 (INS)
- 韩国中部 (KRC)
- 韩国南部 (KRS)
- 美国中北部 (NCUS)
- 北欧 (NE)
- 美国中南部 (SCUS)
- 东南亚 (SEA)
- 英国南部 (UKS)
- 英国西部 (UKW)
- 西欧 (WE)
- 美国西部 (WUS)
- 美国中西部 (WCUS)
- 美国西部 2 (WUS 2)
- US Gov 亚利桑那州 (UGA)
- US Gov 德克萨斯州 (UGT)
- US Gov 弗吉尼亚州 (UGV)

如果需要在上面没有列出的特定地理区域使用，请向 [AskAzureBackupTeam@microsoft.com](email:askazurebackupteam@microsoft.com) 发送邮件。

### <a name="how-many-azure-file-shares-can-i-protect-in-a-vaultbr"></a>可以在保管库中保护多少 Azure 文件共享？<br/>
在预览期，一个保管库中最多可以保护 50 个存储帐户的 Azure 文件共享。 也可在单个保管库中保护多达 200 个 Azure 文件共享。

### <a name="can-i-protect-two-different-file-shares-from-the-same-storage-account-to-different-vaults"></a>是否可以在不同的保管库中对同一存储帐户中的两个不同的文件共享进行保护？
不是。 只能由同一保管库对某个存储帐户中的所有文件共享进行保护。

## <a name="backup"></a>备份

### <a name="how-many-scheduled-backups-can-i-configure-per-file-share"></a>针对每个文件共享，可以配置多少个计划备份？
Azure 备份当前支持对 Azure 文件共享配置计划的每日一次备份。 

### <a name="how-many-on-demand-backups-can-i-take-per-file-share-br"></a>每个文件共享可以进行多少个按需备份？ <br/>
在任何时间点，最多可以有一个文件共享的 200 个快照。 此限制包括由 Azure 备份根据策略的定义创建的快照。 如果在达到此限制后无法进行备份，请删除按需还原点，以便将来能够成功地进行备份。


## <a name="restore"></a>还原

### <a name="can-i-recover-from-a-deleted-azure-file-share-br"></a>能否从已删除的 Azure 文件共享进行恢复？ <br/>
删除 Azure 文件共享时，系统会显示会被删除的备份的列表，并会要求你进行确认。 无法还原已删除的 Azure 文件共享。

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-an-azure-file-share-br"></a>在停止对 Azure 文件共享进行保护的情况下，是否能从备份还原？ <br/>
是的。 如果在停止保护时选择了“保留备份数据”，则可从所有现有的还原点还原。 

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>如果取消正在进行的还原作业，会发生什么情况？
如果取消正在进行的还原作业，则还原过程将停止，并且在取消之前还原的所有文件将保留在已配置的目标位置（原始位置或备用位置），而不进行任何回退。 


## <a name="manage-backup"></a>管理备份

### <a name="can-i-use-powershell-to-configuremanagerestore-backups-of-azure-file-shares-br"></a>是否可以使用 PowerShell 配置/管理/还原 Azure 文件共享的备份？ <br/>
是的。 请参阅[此处](backup-azure-afs-automation.md)的详细文档

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-it-br"></a>能否访问 Azure 备份生成的快照并将其装载？ <br/>
可以访问 Azure 备份生成的所有快照，只需在门户、PowerShell 或 CLI 中查看快照即可。 若要详细了解 Azure 文件共享快照，请参阅 [Azure 文件的共享快照（预览版）概述](../storage/files/storage-snapshots-files.md)。

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups-br"></a>可以为备份配置的最长保留期是多长？ <br/>
Azure 文件共享的备份提供了配置保留期最多为 180 天的策略的功能。 但是，使用 [PowerShell 中的“按需备份”选项](backup-azure-afs-automation.md#trigger-an-on-demand-backup)，你甚至可以将恢复点保留 10 年。

### <a name="what-happens-when-i-change-the-backup-policy-for-an-azure-file-share-br"></a>更改 Azure 文件共享的备份策略时，会发生什么情况？ <br/>
在文件共享上应用新策略时，将遵循新策略的计划和保留期。 如果延长保留期，则会对现有的恢复点进行标记，按新策略要求保留它们。 如果缩短保留期，则会将其标记为在下一清理作业中删除，然后就会将其删除。

## <a name="see-also"></a>另请参阅
本文的信息只是讲述如何备份 Azure 文件，若要详细了解 Azure 备份的其他领域，请选择性地参阅下面的其他备份常见问题解答：
-  [恢复服务保管库常见问题解答](backup-azure-backup-faq.md)
-  [Azure VM 备份常见问题解答](backup-azure-vm-backup-faq.md)
-  [Azure 备份代理常见问题解答](backup-azure-file-folder-backup-faq.md)