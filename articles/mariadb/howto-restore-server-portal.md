---
title: 如何在 Azure Database for MariaDB 中还原服务器
description: 本文介绍如何使用 Azure 门户在 Azure Database for MariaDB 中还原服务器。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 23d683fea494ad0509af359d6e49519f2bc6aa99
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60746543"
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-mariadb-using-the-azure-portal"></a>如何使用 Azure 门户在 Azure Database for MariaDB 中备份和还原服务器

## <a name="backup-happens-automatically"></a>自动进行备份
Azure Database for MariaDB 服务器定期进行备份以便启用还原功能。 通过此功能，用户可将服务器及其所有数据库还原到新服务器上的某个较早时间点。

## <a name="prerequisites"></a>必备组件
若要完成本操作指南，需要：
- [Azure Database for MariaDB 服务器和数据库](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="set-backup-configuration"></a>设置备份配置

创建服务器时，可以在“定价层”  窗口中选择将服务器配置为进行本地冗余备份或异地冗余备份。

> [!NOTE]
> 创建服务器后，无法在异地冗余或本地冗余之间切换服务器冗余类型。
>

通过 Azure 门户创建服务器时，在“定价层”  窗口中为服务器选择是进行**本地冗余**备份还是**异地冗余**备份。 也在此窗口中选择“备份保留期”  - 希望存储服务器备份多长时间（天数）。

   ![定价层 - 选择备份冗余](./media/howto-restore-server-portal/pricing-tier.png)

有关在创建过程中设置这些值的详细信息，请参阅 [Azure Database for MariaDB 服务器快速入门](quickstart-create-mariadb-server-database-using-azure-portal.md)。

可以通过以下步骤更改服务器上的备份保留期：
1. 登录到 [Azure 门户](https://portal.azure.com/)。

2. 选择 Azure Database for MariaDB 服务器。 此操作将打开“概述”  页。

3. 在“设置”  下，从菜单中选择“定价层”  。 使用滑块可以根据需要更改**备份保留期**（7 天到 35 天）。
在下面的屏幕截图中，该项已增加到 35 天。
![增加的备份保留期](./media/howto-restore-server-portal/3-increase-backup-days.png)

4. 单击“确定”  确认更改。

备份保留期控制可以往回检索多长时间的时间点还原，因为它基于可用备份。 以下部分进一步说明了时间点还原。 

## <a name="point-in-time-restore"></a>时间点还原
使用 Azure Database for MariaDB 可以将服务器还原到某个时间点，并还原到服务器的新副本。 可以使用此新服务器恢复数据，或将客户端应用程序指向此新服务器。

例如，如果今天中午不小心删除了一张表，则可以将该表还原到中午之前的状态，并从服务器的新副本中检索丢失的表和数据。 时间点还原在服务器级别（而不是在数据库级别）进行。

以下步骤演示将示例服务器还原到某个时间点：
1. 在 Azure 门户中，选择 Azure Database for MariaDB 服务器。 

2. 在服务器“概述”页的工具栏中，选择“还原”   。

   ![Azure Database for MariaDB - 概述 -“还原”按钮](./media/howto-restore-server-portal/2-server.png)

3. 使用必需信息填写“还原”窗体：

   ![Azure Database for MariaDB - 还原信息](./media/howto-restore-server-portal/3-restore.png)
   - **还原点**：选择要还原到的时间点。
   - **目标服务器**：提供新服务器的名称。
   - **位置**：不可选择区域。 默认情况下，此值与源服务器相同。
   - **定价层**：执行时间点还原时，无法更改这些参数。 此值与源服务器相同。 

4. 单击“确定”  ，将服务器还原到某个时间点。 

5. 还原完成后，找到创建的新服务器，以验证数据是否已按预期还原。

>[!Note]
>请注意，通过时间点还原创建的新服务器具有在所选时间点对现有服务器有效的相同服务器管理员登录名和密码。 可以从新服务器的“概述”  页更改密码。

## <a name="geo-restore"></a>异地还原
如果为服务器配置了异地冗余备份，则可以从该现有服务器的备份创建新服务器。 可以在 Azure Database for MariaDB 可用的任何区域中创建此新服务器。  

1. 选择“数据库” > “Azure Database for MariaDB”   。 还可以在搜索框中键入“MariaDB”以查找该服务  。

   ![“Azure Database for MariaDB”选项](./media/howto-restore-server-portal/2_navigate-to-mariadb.png)

2. 在窗体的“选择源”  下拉列表中，选择“备份”  。 此操作将加载已启用异地冗余备份的服务器列表。 选择这些备份之一作为新服务器的源。
   ![选择源：备份和异地冗余备份的列表](./media/howto-restore-server-portal/2-georestore.png)

   > [!NOTE]
   > 首次创建服务器时，该服务器可能不会立即可用于异地还原。 填充必需的元数据可能需要几个小时。
   >

3. 根据需要填写窗体的其余部分。 可以选择任意**位置**。 选择位置后，可以选择**定价层**。 默认情况下将显示要从中还原的现有服务器的参数。 可以单击“确定”  ，以不进行任何更改继承这些设置。 也可以更改**计算的代**（如果在所选区域中可用）、**vCore** 数、**备份保留期**和**备份冗余选项**。 不支持在还原过程中更改**定价层**（“基本”、“常规用途”或“内存优化”）或**存储**大小。

>[!Note]
>通过异地还原创建的新服务器具有在启动还原时对现有服务器有效的相同服务器管理员登录名和密码。 可以从新服务器的“概述”  页更改密码。

## <a name="next-steps"></a>后续步骤
- 详细了解服务的[备份](concepts-backup.md)。
- 详细了解[业务连续性](concepts-business-continuity.md)选项。
