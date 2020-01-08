---
title: 适用于 SAP NetWeaver 的 Azure 虚拟机部署 | Microsoft Docs
description: 了解如何将 SAP 软件部署到 Azure 中的 Linux 虚拟机。
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 1c4f1951-3613-4a5a-a0af-36b85750c84e
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/16/2019
ms.author: sedusch
ms.openlocfilehash: 549fd8f4cb770d472eefd1c504e42837fa8230dd
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71066866"
---
# <a name="azure-virtual-machines-deployment-for-sap-netweaver"></a>适用于 SAP NetWeaver 的 Azure 虚拟机部署

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]: https://launchpad.support.sap.com/#/notes/1031096
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]: https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]: https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]: https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]:https://launchpad.support.sap.com/#/notes/1619967
[1750510]:https://launchpad.support.sap.com/#/notes/1750510
[1752266]:https://launchpad.support.sap.com/#/notes/1752266
[1757924]:https://launchpad.support.sap.com/#/notes/1757924
[1757928]:https://launchpad.support.sap.com/#/notes/1757928
[1758182]:https://launchpad.support.sap.com/#/notes/1758182
[1758496]:https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]: https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]: https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[2367194]:https://launchpad.support.sap.com/#/notes/2367194

[azure-cli]:../../../cli-install-nodejs.md
[azure-cli-2]:https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md (适用于 SAP 的 Azure 虚拟机 DBMS 部署)
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (VM 和 VHD 的缓存)
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (软件 RAID)
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Microsoft Azure 存储)
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (RDBMS 部署的结构)
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (Azure Vm 的高可用性和灾难恢复)
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 及更高版本)
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 及更低版本)
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (使用来自 Azure 市场的 SQL Server 映像)
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (适用于 Azure 上的 SAP 的 SQL Server 总体摘要)
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (SQL Server RDBMS 的详细信息)
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (存储配置)
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (备份和还原)
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (备份和还原的性能注意事项)
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (其他)
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:deployment-guide.md (适用于 SAP 的 Azure 虚拟机部署)
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (SAP 资源)
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (通过使用自定义映像部署 VM)
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (方案 1：为 SAP 部署来自 Azure 市场的 VM)
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (方案 2：使用自定义映像为 SAP 部署 VM)
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (方案 3：使用包含 SAP 的非通用化 Azure VHD 从本地移动 VM)
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Microsoft Azure 上 SAP 的 VM 部署方案)
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (部署 Azure PowerShell cmdlet)
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (下载并导入 SAP 相关的 PowerShell cmdletst)
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (将 VM 加入本地域（仅限 Windows）)
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux)
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (下载、安装并启用 Azure VM 代理)
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell)
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Azure CLI)
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (配置适用于 SAP 的 Azure 扩展)
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (适用于 SAP 的 Azure 扩展的就绪状态检查)
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (适用于 SAP 的 Azure 扩展配置的运行状况检查)
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (适用于 SAP 的 Azure 扩展故障排除)

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (配置 VM 扩展)
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (配置代理)
[deployment-guide-figure-100]:media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:deployment-guide.md#figure-11
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:deployment-guide.md#figure-14
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:deployment-guide.md#figure-5
[deployment-guide-figure-50]:media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:deployment-guide.md#figure-6
[deployment-guide-figure-600]:media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:deployment-guide.md#figure-7
[deployment-guide-figure-700]:media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (SAP 主机代理的端到端数据收集的检查和故障排除)

[deploy-template-cli]:../../../resource-group-template-deploy-cli.md
[deploy-template-portal]:../../../resource-group-template-deploy-portal.md
[deploy-template-powershell]:../../../resource-group-template-deploy.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[getting-started-dbms]:get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:https://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-Azvmaemextension]:https://docs.microsoft.com/powershell/module/az.compute/set-azvmaemextension

[planning-guide]:planning-guide.md (适用于 SAP 的 Azure 虚拟机规划和实施)
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (资源)
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (Azure 虚拟机上运行的 SAP NetWeaver 的高可用性和灾难恢复)
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (SAP 应用程序服务器的高可用性)
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (对 SAP 实例使用 Autostart)
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (仅限云 - 在不将依赖项部署到本地客户网络的情况下将虚拟机部署到 Azure 中)
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (跨界 - 在 Azure 中部署的单个或多个 SAP VM 完全集成到本地网络)
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Azure 区域)
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (容错域)
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (升级域)
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Azure 可用性集)
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (Microsoft Azure 虚拟机概念)
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure 高级存储)
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (使用非通用化磁盘将 VM 从本地移至 Azure)
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (使用特定于客户的映像部署虚拟机)
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (准备使用非通用化磁盘将虚拟机从本地移到 Azure)
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (准备使用特定于客户的映像为 SAP 部署虚拟机)
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (为 Azure 准备包含 SAP 的虚拟机)
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Azure 磁盘和 Azure 映像之间的差异)
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (将 VHD 从本地上载到 Azure)
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (在 Azure 存储帐户之间复制磁盘)
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (SAP 部署的 VM/VHD 结构)
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (为附加的磁盘设置自动装载)
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (用于 SAP NetWeaver 演示/培训的单一 VM 方案)
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (SAP 实例的仅限云部署的概念)
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (适用于 SAP 的 Azure 监视解决方案)
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure 高级存储)
[planning-guide-managed-disks]:planning-guide.md#c55b2c6e-3ca1-4476-be16-16c81927550f (托管磁盘)
[planning-guide-figure-100]:media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Microsoft Azure 网络)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (存储：Microsoft Azure 存储和数据磁盘)

[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../../../networking/network-overview.md
[sap-pam]: https://support.sap.com/pam (SAP 产品可用性对照表)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image-md%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-os-disk-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk-md%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-2-tier-user-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image-md%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-md%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image-md%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../windows/disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/storage-scalability-targets.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-Az-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md (使用 Azure 资源管理器模板和 Azure CLI 部署和管理虚拟机)
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md (使用 Azure Resource Manager 和 PowerShell 管理虚拟机)
[virtual-machines-windows-agent-user-guide]:../../extensions/agent-windows.md
[virtual-machines-linux-agent-user-guide]:../../extensions/agent-linux.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../extensions/agent-linux.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability]:../../linux/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:../../virtual-machines-windows-ps-create.md
[virtual-machines-sizes]:../../linux/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:./../../windows/sql/virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:./../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:./../../windows/sql/virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../../../virtual-network/virtual-network-deploy-multinic-classic-ps.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

对于需要在最短时间内获得计算和存储资源的组织，Azure 虚拟机是合适的解决方案，它没有冗长的采购周期。 可以使用 Azure 虚拟机在 Azure 中部署经典应用程序，例如基于 SAP NetWeaver 的应用程序。 无需额外的本地资源即可扩展应用程序的可靠性和可用性。 Azure 虚拟机支持跨界连接，因此，可将 Azure 虚拟机集成到组织的本地域、私有云和 SAP 系统布局中。

本文介绍在 Azure 中的虚拟机 (VM) 上部署 SAP 应用程序的步骤，包括备用部署选项和故障排除。 本文基于[适用于 SAP NetWeaver 的 Azure 虚拟机规划和实施][planning-guide]中的信息。 它还对 SAP 安装文档和 SAP 说明（指导安装和部署 SAP 软件的主要资源）进行了补充。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

为 SAP 软件部署设置 Azure 虚拟机涉及多个步骤和资源。 在开始之前，请确保满足在 Azure 中虚拟机上安装 SAP 软件的先决条件。

### <a name="local-computer"></a>本地计算机

若要管理 Windows VM 或 Linux VM，可以使用 PowerShell 脚本和 Azure 门户。 对于这两种工具，都需要具有运行 Windows 7 或更高 Windows 版本的本地计算机。 如果希望仅管理 Linux VM 并且希望使用 Linux 计算机执行此任务，则可以使用 Azure CLI。

### <a name="internet-connection"></a>Internet 连接

若要下载并运行 SAP 软件部署所需的工具和脚本，必须连接到 Internet。 运行适用于 SAP 的 Azure 扩展的 Azure VM 也需要访问 Internet。 如果 Azure VM 是 Azure 虚拟网络或本地域的一部分，请确保设置相关代理设置，如[配置代理][deployment-guide-configure-proxy]中所述。

### <a name="microsoft-azure-subscription"></a>Microsoft Azure 订阅

需要一个有效的 Azure 帐户。

### <a name="topology-and-networking"></a>拓扑和网络

需要在 Azure 中定义 SAP 部署的拓扑和体系结构：

* 要使用的 Azure 存储帐户
* 要在其中部署 SAP 系统的虚拟网络
* 要将 SAP 系统部署到的资源组
* 要在其中部署 SAP 系统的 Azure 区域
* SAP 配置（两层或三层）
* VM 大小和要装载到 VM 的额外数据磁盘的数目
* SAP 更正和传输系统 (CTS) 配置

在开始 SAP 软件部署流程之前，请创建并配置 Azure 存储帐户（如果需要）或 Azure 虚拟网络。 有关如何创建和配置这些资源的信息，请参阅[适用于 SAP NetWeaver 的 Azure 虚拟机规划和实施][planning-guide]。

### <a name="sap-sizing"></a>SAP 大小调整

要进行 SAP 大小调整，需知道以下信息：

* 预计的 SAP 工作负荷（例如，通过使用 SAP Quick Sizer 工具）和 SAP 应用程序性能标准 (SAPS) 数量
* SAP 系统所需的 CPU 资源和内存消耗
* 所需的每秒输入/输出 (I/O) 操作数
* Azure 中不同 VM 之间最终通信时所需的网络带宽
* 本地资产与 Azure 部署的 SAP 系统之间所需的网络带宽

### <a name="resource-groups"></a>资源组

在 Azure 资源管理器中，可使用资源组管理 Azure 订阅中的所有应用程序资源。 有关详细信息，请参阅 [Azure 资源管理器概述][resource-group-overview]。

## <a name="resources"></a>资源

### <a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>SAP 资源

设置 SAP 软件部署时，需要以下 SAP 资源：

* SAP 说明 [1928533]，其中包含：
  * SAP 软件部署支持的 Azure VM 大小的列表
  * Azure VM 大小的重要容量信息
  * 支持的 SAP 软件、操作系统 (OS) 和数据库组合
  * Microsoft Azure 上 Windows 和 Linux 所需的 SAP 内核版本

* SAP 说明 [2015553] 列出了在 Azure 中 SAP 支持的 SAP 软件部署的先决条件。
* SAP 说明 [2178632] 包含为 Azure 中的 SAP 报告的所有监控指标的详细信息。
* SAP 说明 [1409604] 包含 Azure 中的 Windows 所需的 SAP 主机代理版本。
* SAP 说明 [2191498] 包含 Azure 中的 Linux 所需的 SAP 主机代理版本。
* SAP 说明 [2243692] 包含 Azure 中的 Linux 上的 SAP 许可的相关信息。
* SAP 说明 [1984787] 包含有关 SUSE Linux Enterprise Server 12 的一般信息。
* SAP 说明 [2002167] 包含有关 Red Hat Enterprise Linux 7.x 的一般信息。
* SAP 说明 [2069760] 包含有关 Oracle Linux 7.x 的一般信息。
* SAP 说明 [1999351] 包含适用于 SAP 的 Azure 增强型监视扩展的其他故障排除信息。
* SAP 说明 [1597355] 包含有关 Linux 交换空间的一般信息。
* [Azure 上的 SAP SCN 页](https://wiki.scn.sap.com/wiki/x/Pia7Gg)包含新闻和有用资源的集合。
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) 包含适用于 Linux 的所有必需 SAP 说明。
* [Azure PowerShell][azure-ps]中包含的特定于 SAP 的 PowerShell cmdlet。
* [Azure CLI][azure-cli]中包含的特定于 SAP 的 Azure CLI 命令。

### <a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Windows 资源

以下文章介绍了 Azure 中的 SAP 部署：

* [SAP NetWeaver 的 Azure 虚拟机规划和实施指南][planning-guide]
* [适用于 SAP NetWeaver 的 Azure 虚拟机部署（本文）][deployment-guide]
* [SAP NetWeaver 的 Azure 虚拟机 DBMS 部署][dbms-guide]

## <a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Azure VM 上 SAP 软件的部署方案

有多个选项可用于在 Azure 中部署 VM 和关联的磁盘。 了解这些部署选项之间的区别非常重要，你可能需要根据所选的部署类型采取不同的步骤为部署准备 VM。

### <a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>场景 1：为 SAP 部署来自 Azure 市场的 VM

可以使用 Azure 市场中由 Microsoft 或第三方提供的映像来部署 VM。 市场提供了 Windows Server 和各种 Linux 分发的一些标准 OS 映像。 还可以部署包括数据库管理系统 (DBMS) SKU（例如 Microsoft SQL Server）的映像。 有关使用带有 DBMS Sku 的映像的详细信息，请参阅[适用于 SAP NetWeaver 的 Azure 虚拟机 DBMS 部署][dbms-guide]。

下面的流程图显示了从 Azure 市场部署 VM 时特定于 SAP 的步骤序列：

![使用 Azure 市场中的 VM 映像为 SAP 系统部署 VM 的流程图][deployment-guide-figure-100]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>使用 Azure 门户创建虚拟机

使用 Azure 市场中的映像创建新虚拟机的最简单方式是使用 Azure 门户。

1.  转到  <https://portal.azure.com/#create/hub> 。  或者，在 Azure 门户菜单中，选择“+ 新建”。
1.  选择“计算”，并选择要部署的操作系统的类型。 例如，Windows Server 2012 R2、SUSE Linux Enterprise Server 12 (SLES 12)、Red Hat Enterprise Linux 7.2 (RHEL 7.2) 或 Oracle Linux 7.2。 默认列表视图并未显示所有受支持的操作系统。 若要查看完整列表，请选择“查看所有”。 有关 SAP 软件部署支持的操作系统的详细信息，请参阅 SAP 说明 [1928533]。
1.  在下一页上，查看条款和条件。
1.  在“选择部署模型”框中，选择“资源管理器”。
1.  选择“创建”。

向导将引导完成创建虚拟机以及所有必需资源（例如网络接口和存储帐户）时所需的参数的设置。 其中一些参数包括：

1. **基本信息**：
   * **名称**：资源的名称（虚拟机名称）。
   * **VM 磁盘类型**：选择 OS 磁盘的磁盘类型。 若要对数据磁盘使用高级存储，我们建议也对 OS 磁盘使用高级存储。
   * 用户名和密码或 SSH 公钥：输入在预配期间创建的用户的用户名和密码。 对于 Linux 虚拟机，可以输入用来登录计算机的公用安全外壳 (SSH) 密钥。
   * **订阅**：选择要用于预配新虚拟机的订阅。
   * **资源组**：VM 的资源组的名称。 可以输入一个新资源组的名称或已存在的资源组的名称。
   * **位置**：要将新虚拟机部署到的地方。 如果想要将虚拟机连接到本地网络，请确保选择将 Azure 连接到本地网络的虚拟网络的位置。 有关详细信息，请参阅[SAP NetWeaver 的 Azure 虚拟机规划和实施][planning-guide]中的[Microsoft Azure 网络][planning-guide-microsoft-azure-networking]。
1. **大小**：

     有关支持的 VM 类型的列表，请查看 SAP 说明 [1928533]。 如果想要使用 Azure 高级存储，请确保选择正确的 VM 类型。 并非所有 VM 类型都支持高级存储。 有关详细信息，请参阅[存储：][planning-guide-storage-microsoft-azure-storage-and-data-disks] [SAP NetWeaver 的 azure 虚拟机规划和实施][planning-guide]中的 Microsoft Azure 存储和数据磁盘和[azure 高级存储][planning-guide-azure-premium-storage]。

1. **设置**：
   * **存储**
     * **磁盘类型**：选择 OS 磁盘的磁盘类型。 若要对数据磁盘使用高级存储，我们建议也对 OS 磁盘使用高级存储。
     * **使用托管磁盘**：若要使用托管磁盘，请选择“是”。 有关托管磁盘的详细信息，请参阅规划指南中的[托管磁盘][planning-guide-managed-disks]一章。
     * **存储帐户**：选择现有存储帐户，或者新建存储帐户。 并未所有存储类型都适合用来运行 SAP 应用程序。 有关存储类型的详细信息，请参阅[用于 RDBMS 部署的 VM 的存储结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64)。
   * **网络**
     * 虚拟网络和子网：要将虚拟机与内部网络相集成，请选择连接到本地网络的虚拟网络。
     * **公共 IP 地址**：选择想要使用的公用 IP 地址，或输入参数来创建新的公用 IP 地址。 可以使用公用 IP 地址通过 Internet 访问虚拟机。 请确保同时创建网络安全组来帮助保护对虚拟机的访问。
     * **网络安全组**：有关详细信息，请参阅[利用网络安全组控制网络流量][virtual-networks-nsg]。
   * **扩展**：可以通过将虚拟机扩展添加到部署来安装这些扩展。 不需要在此步骤中添加扩展。 稍后会安装 SAP 支持所需的扩展。 请参阅本指南中[的配置适用于 SAP 的 Azure 扩展][deployment-guide-4.5]章节。
   * **高可用性**：选择一个可用性集，或输入参数来创建新的可用性集。 有关详细信息，请参阅[Azure 可用性集][planning-guide-3.2.3]。
   * **监视**
     * **启动诊断**：可以选择“禁用”启动诊断。
     * **来宾 OS 诊断**：可以选择“禁用”监视诊断。

1. **汇总**：

   复查选择，然后选择“确定”。

虚拟机将部署在选择的资源组中。

#### <a name="create-a-virtual-machine-by-using-a-template"></a>使用模板创建虚拟机

可以使用[azure 快速入门模板 GitHub 存储库][azure-quickstart-templates-github]中发布的某个 SAP 模板来创建虚拟机。 你还可以使用[Azure 门户][virtual-machines-windows-tutorial]、 [PowerShell][virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]或[Azure CLI][virtual-machines-linux-tutorial]来手动创建虚拟机。

* [**双层配置（仅一个虚拟机）模板**（sap-第2层-市场映像）][sap-templates-2-tier-marketplace-image]

  若要仅使用一台虚拟机创建一个两层系统，请使用此模板。
* [**双层配置（仅一个虚拟机）模板托管磁盘**（sap-2 层-市场-映像-md）][sap-templates-2-tier-marketplace-image-md]

  若要仅使用一个虚拟机和托管磁盘创建一个两层系统，请使用此模板。
* [**三层配置（多个虚拟机）模板**（sap-第3层-市场映像）][sap-templates-3-tier-marketplace-image]

  若要使用多台虚拟机创建一个三层系统，请使用此模板。
* [**三层配置（多个虚拟机）模板托管磁盘**（sap-3 层-市场-映像-md）][sap-templates-3-tier-marketplace-image-md]

  若要使用多个虚拟机和托管磁盘创建一个三层系统，请使用此模板。

在 Azure 门户中，为模板输入以下参数：

1. **基本信息**：
   * **订阅**：要用来部署模板的订阅。
   * **资源组**：要用来部署模板的资源组。 可以创建一个新的资源组，也可以选择订阅中的一个现有资源组。
   * **位置**：部署模板的位置。 如果选择了一个现有资源组，则会使用该资源组的位置。

1. **设置**：
   * **SAP 系统 ID**：SAP 系统 ID (SID)。
   * **OS 类型**：要部署的操作系统，例如 Windows Server 2012 R2、SUSE Linux Enterprise Server 12 (SLES 12)、Red Hat Enterprise Linux 7.2 (RHEL 7.2) 或 Oracle Linux 7.2。

     列表视图并未显示所有受支持的操作系统。 有关 SAP 软件部署支持的操作系统的详细信息，请参阅 SAP 说明 [1928533]。
   * **SAP 系统大小**：SAP 系统的大小。

     新系统提供的 SAPS 的数量。 如果不确定系统需要多少 SAPS，请咨询 SAP 技术合作伙伴或系统集成商。
   * **系统可用性**（仅限三层模板）：系统可用性。

     对于适用于高可用性安装的配置，请选择“HA”。 将创建两个数据库服务器和两个用于 ABAP SAP 中心服务 (ASCS) 的服务器。
   * **存储类型**（仅限双层模板）：要使用的存储类型。

     对于较大的系统，我们强烈建议使用 Azure 高级存储。 有关存储类型的详细信息，请参阅以下资源：
      * [将 Azure 高级 SSD 存储用于 SAP DBMS 实例][2367194]
      * [用于 RDBMS 部署的 VM 的存储结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [高级存储：适用于 Azure 虚拟机工作负荷的高性能存储][storage-premium-storage-preview-portal]
      * [Microsoft Azure 存储简介][storage-introduction]
   * 管理员用户名和管理员密码：用户名和密码。
     将创建一个新用户，用于登录到虚拟机。
   * **新子网或现有子网**：确定是要创建新的虚拟网络和子网，还是使用现有子网。 如果已有连接到本地网络的虚拟网络，请选择“现有”。
   * **子网 ID**：如果要将 VM 部署到现有 VNet 中，并且该 VNet 中已定义了 VM 应分配到的子网，请指定该特定子网的 ID。 ID 通常如下所示：/subscriptions/&lt;订阅 id>/resourceGroups/&lt;资源组名称>/providers/Microsoft.Network/virtualNetworks/&lt;虚拟网络名称>/subnets/&lt;子网名称>

1. **条款和条件**：  
    查看并接受法律条款。

1. 选择“购买”。

使用 Azure 市场中的映像时，默认情况下会部署 Azure VM 代理。

#### <a name="configure-proxy-settings"></a>配置代理设置

根据本地网络的配置情况，可能需要在 VM 上设置代理。 如果 VM 通过 VPN 或 ExpressRoute 连接到本地网络，则 VM 可能无法访问 Internet，并且无法下载所需的 VM 扩展，也无法通过 SAP 扩展为 SAP 主机代理收集 Azure 基础结构信息适用于 Azure。 有关详细信息，请参阅[配置代理][deployment-guide-configure-proxy]。

#### <a name="join-a-domain-windows-only"></a>加入域（仅限 Windows）

如果 Azure 部署通过 Azure 站点到站点 VPN 连接或 ExpressRoute 连接到本地 Active Directory 或 DNS 实例（这在 [适用于 SAP 的 Azure 虚拟机规划和实施中称为跨界）NetWeaver][planning-guide]），应为 VM 加入本地域。 有关此任务的注意事项的详细信息，请参阅将[VM 加入本地域（仅限 Windows）][deployment-guide-4.3]。

#### <a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>配置 VM 扩展

若要确保 SAP 支持你的环境，请按照[配置适用于 sap 的 Azure 扩展][deployment-guide-4.5]中所述设置适用于 Sap 的 azure 扩展。 查看 sap 的先决条件，以及 sap[资源][deployment-guide-2.2]中列出的资源中的 sap 内核和 Sap 主机代理所需的最低版本。

#### <a name="vm-extension-for-sap-check"></a>适用于 SAP 的 VM 扩展检查

查看适用于 sap[主机代理的端到端数据收集的检查和故障排除][deployment-guide-troubleshooting-chapter]中所述的适用于 SAP 的 VM 扩展是否正常工作。

#### <a name="post-deployment-steps"></a>部署后步骤

在创建并部署 VM 后，需要在 VM 中安装所需的软件组件。 由于此类型的 VM 部署中的部署/软件安装顺序，要安装的软件必须已经可用（位于 Azure 中、在另一 VM 上或者作为可以附加的磁盘）。 或者，考虑使用跨界方案，在此方案中，将提供到本地资产（安装共享）的连接。

将 VM 部署到 Azure 中之后，需要像在本地环境中一样，遵照相同的准则并使用相同的工具在 VM 上安装 SAP 软件。 若要在 Azure VM 上安装 SAP 软件，SAP 和 Microsoft 都建议将 SAP 安装媒体上传并存储到 Azure VHD 或托管磁盘中，或者创建一个充当文件服务器并包含所有必需 SAP 安装媒体的 Azure VM。

### <a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>场景 2：使用自定义映像为 SAP 部署 VM

因为不同版本的操作系统或 DBMS 具有不同的修补程序要求，因此，在 Azure 市场中找到的映像不一定能满足需求。 可能需要使用自己的 OS/DBMS VM 映像创建一个 VM，以后可以再次部署该 VM。
为 Linux 创建专用映像时使用的步骤不同于为 Windows 创建专用映像时使用的步骤。

---
> ![Windows][Logo_Windows] Windows
>
> 若要准备可用来部署多台虚拟机的 Windows 映像，必须在本地 VM 上抽象化或通用化 Windows 设置（例如 Windows SID 和主机名）。 可以使用 [sysprep](https://msdn.microsoft.com/library/hh825084.aspx) 来执行此操作。
>
> ![Linux][Logo_Linux] Linux
>
> 若要准备可用来部署多台虚拟机的 Linux 映像，必须在本地 VM 上抽象化或通用化某些 Linux 设置。 可以使用 `waagent -deprovision` 来执行此操作。 有关详细信息，请参阅[捕获在 azure 上运行的 Linux 虚拟机][virtual-machines-linux-capture-image]和[azure Linux 代理用户指南][virtual-machines-linux-agent-user-guide-command-line-options]。
>
>

---
可以准备并创建自定义映像，使用它创建多个新的 VM。 [SAP NetWeaver 的 Azure 虚拟机规划和实施中对][planning-guide]此进行了介绍。 设置数据库内容的方式是使用 SAP Software Provisioning Manager 安装新的 SAP 系统（从附加到虚拟机的磁盘还原数据库备份）或直接从 Azure 存储还原数据库备份（如果 DBMS 支持此操作）。 有关详细信息，请参阅[适用于 SAP NetWeaver 的 Azure 虚拟机 DBMS 部署][dbms-guide]。 如果已在本地 VM 中安装了 SAP 系统（尤其是对于两层系统），则在部署 Azure VM 后，可以使用 SAP Software Provisioning Manager 支持的系统重命名过程来修改 SAP 系统设置（SAP 说明 [1619720]）。 否则，可以在部署 Azure VM 之后安装 SAP 软件。

下面的流程图显示了从自定义映像部署 VM 时特定于 SAP 的步骤序列：

![使用专用市场中的 VM 映像为 SAP 系统部署 VM 的流程图][deployment-guide-figure-300]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>使用 Azure 门户创建虚拟机

通过托管磁盘映像创建新虚拟机的最简单方式是使用 Azure 门户。 有关如何创建托管磁盘映像的详细信息，请阅读[在 Azure 中捕获通用 VM 的托管映像](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)

1.  转到  <https://ms.portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Compute%2Fimages> 。 或者，在 Azure 门户菜单中，选择“映像”。
1.  选择想要部署的托管磁盘映像并单击“创建 VM”

向导将引导完成创建虚拟机以及所有必需资源（例如网络接口和存储帐户）时所需的参数的设置。 其中一些参数包括：

1. **基本信息**：
   * **名称**：资源的名称（虚拟机名称）。
   * **VM 磁盘类型**：选择 OS 磁盘的磁盘类型。 若要对数据磁盘使用高级存储，我们建议也对 OS 磁盘使用高级存储。
   * 用户名和密码或 SSH 公钥：输入在预配期间创建的用户的用户名和密码。 对于 Linux 虚拟机，可以输入用来登录计算机的公用安全外壳 (SSH) 密钥。
   * **订阅**：选择要用于预配新虚拟机的订阅。
   * **资源组**：VM 的资源组的名称。 可以输入一个新资源组的名称或已存在的资源组的名称。
   * **位置**：要将新虚拟机部署到的地方。 如果想要将虚拟机连接到本地网络，请确保选择将 Azure 连接到本地网络的虚拟网络的位置。 有关详细信息，请参阅[SAP NetWeaver 的 Azure 虚拟机规划和实施][planning-guide]中的[Microsoft Azure 网络][planning-guide-microsoft-azure-networking]。
1. **大小**：

     有关支持的 VM 类型的列表，请查看 SAP 说明 [1928533]。 如果想要使用 Azure 高级存储，请确保选择正确的 VM 类型。 并非所有 VM 类型都支持高级存储。 有关详细信息，请参阅[存储：][planning-guide-storage-microsoft-azure-storage-and-data-disks] [SAP NetWeaver 的 azure 虚拟机规划和实施][planning-guide]中的 Microsoft Azure 存储和数据磁盘和[azure 高级存储][planning-guide-azure-premium-storage]。

1. **设置**：
   * **存储**
     * **磁盘类型**：选择 OS 磁盘的磁盘类型。 若要对数据磁盘使用高级存储，我们建议也对 OS 磁盘使用高级存储。
     * **使用托管磁盘**：若要使用托管磁盘，请选择“是”。 有关托管磁盘的详细信息，请参阅规划指南中的[托管磁盘][planning-guide-managed-disks]一章。
   * **网络**
     * 虚拟网络和子网：要将虚拟机与内部网络相集成，请选择连接到本地网络的虚拟网络。
     * **公共 IP 地址**：选择想要使用的公用 IP 地址，或输入参数来创建新的公用 IP 地址。 可以使用公用 IP 地址通过 Internet 访问虚拟机。 请确保同时创建网络安全组来帮助保护对虚拟机的访问。
     * **网络安全组**：有关详细信息，请参阅[利用网络安全组控制网络流量][virtual-networks-nsg]。
   * **扩展**：可以通过将虚拟机扩展添加到部署来安装这些扩展。 不需要在此步骤中添加扩展。 稍后会安装 SAP 支持所需的扩展。 请参阅本指南中[的配置适用于 SAP 的 Azure 扩展][deployment-guide-4.5]章节。
   * **高可用性**：选择一个可用性集，或输入参数来创建新的可用性集。 有关详细信息，请参阅[Azure 可用性集][planning-guide-3.2.3]。
   * **监视**
     * **启动诊断**：可以选择“禁用”启动诊断。
     * **来宾 OS 诊断**：可以选择“禁用”监视诊断。

1. **汇总**：

   复查选择，然后选择“确定”。

虚拟机将部署在选择的资源组中。

#### <a name="create-a-virtual-machine-by-using-a-template"></a>使用模板创建虚拟机

若要从 Azure 门户使用某个专用 OS 映像来创建部署，请使用下列 SAP 模板之一。 这些模板在[azure 快速入门模板 GitHub 存储库][azure-quickstart-templates-github]中发布。 还可以使用[PowerShell][virtual-machines-upload-image-windows-resource-manager]手动创建虚拟机。

* [**双层配置（仅一个虚拟机）模板**（sap-第2层-用户映像）][sap-templates-2-tier-user-image]

  若要仅使用一台虚拟机创建一个两层系统，请使用此模板。
* [**双层配置（仅一个虚拟机）模板托管磁盘映像**（sap-2 层-用户映像-md）][sap-templates-2-tier-user-image-md]

  若要仅使用一个虚拟机和托管磁盘映像创建一个两层系统，请使用此模板。
* [**三层配置（多个虚拟机）模板**（sap-第3层-用户映像）][sap-templates-3-tier-user-image]

  要使用多台虚拟机或使用自己的 OS 映像创建一个三层系统，请使用此模板。
* [**三层配置（多个虚拟机）模板托管磁盘映像**（sap-3 层-用户映像-md）][sap-templates-3-tier-user-image-md]

  若要使用多台虚拟机或使用自己的 OS 映像和托管磁盘映像创建一个三层系统，请使用此模板。

在 Azure 门户中，为模板输入以下参数：

1. **基本信息**：
   * **订阅**：要用来部署模板的订阅。
   * **资源组**：要用来部署模板的资源组。 可以创建一个新的资源组，也可以选择订阅中的一个现有资源组。
   * **位置**：部署模板的位置。 如果选择了一个现有资源组，则会使用该资源组的位置。
1. **设置**：
   * **SAP 系统 ID**：SAP 系统 ID。
   * **OS 类型**：要部署的操作系统类型（Windows 或 Linux）。
   * **SAP 系统大小**：SAP 系统的大小。

     新系统提供的 SAPS 的数量。 如果不确定系统需要多少 SAPS，请咨询 SAP 技术合作伙伴或系统集成商。
   * **系统可用性**（仅限三层模板）：系统可用性。

     对于适用于高可用性安装的配置，请选择“HA”。 将创建两个数据库服务器和两个用于 ASCS 的服务器。
   * **存储类型**（仅限双层模板）：要使用的存储类型。

     对于较大的系统，我们强烈建议使用 Azure 高级存储。 有关存储类型的详细信息，请参阅以下资源：
      * [将 Azure 高级 SSD 存储用于 SAP DBMS 实例][2367194]
      * [用于 RDBMS 部署的 VM 的存储结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [高级存储：适用于 Azure 虚拟机工作负载的高性能存储][storage-premium-storage-preview-portal]
      * [Microsoft Azure 存储简介][storage-introduction]
   * **用户映像 VHD URI**（仅限非托管磁盘映像模板）：专用 OS 映像 VHD 的 URI，例如 https://&lt;accountname>.blob.core.windows.net/vhds/userimage.vhd。
   * **用户映像存储帐户**（仅限非托管磁盘映像模板）：存储着专用 OS 映像的存储帐户的名称，例如 &lt;accountname> in https://&lt;accountname>.blob.core.windows.net/vhds/userimage.vhd。
   * **userImageId**（仅限托管磁盘映像模板）：要使用的托管磁盘映像的 ID
   * 管理员用户名和管理员密码：用户名和密码。

     将创建一个新用户，用于登录到虚拟机。
   * **新子网或现有子网**：确定是要创建新的虚拟网络和子网，还是使用现有子网。 如果已有连接到本地网络的虚拟网络，请选择“现有”。
   * **子网 ID**：如果要将 VM 部署到现有 VNet 中，并且该 VNet 中已定义了 VM 应分配到的子网，请指定该特定子网的 ID。 ID 通常如下所示：/subscriptions/&lt;订阅 id>/resourceGroups/&lt;资源组名称>/providers/Microsoft.Network/virtualNetworks/&lt;虚拟网络名称>/subnets/&lt;子网名称>

1. **条款和条件**：  
    查看并接受法律条款。

1. 选择“购买”。

#### <a name="install-the-vm-agent-linux-only"></a>安装 VM 代理（仅限 Linux）

要使用前面的部分中描述的模板，必须已经在用户映像中安装了 Linux 代理，否则，部署会失败。 如[下载、安装并启用 AZURE VM 代理][deployment-guide-4.4]中所述，在用户映像中下载并安装 VM 代理。 如果不使用模板，也可以在以后安装 VM 代理。

#### <a name="join-a-domain-windows-only"></a>加入域（仅限 Windows）

如果 Azure 部署通过 Azure 站点到站点 VPN 连接或 Azure ExpressRoute 连接到本地 Active Directory 或 DNS 实例（这在 [适用于 SAP 的 azure 虚拟机规划和实施中称为跨界）NetWeaver][planning-guide]），应为 VM 加入本地域。 有关此步骤的注意事项的详细信息，请参阅将[VM 加入本地域（仅限 Windows）][deployment-guide-4.3]。

#### <a name="configure-proxy-settings"></a>配置代理设置

根据本地网络的配置情况，可能需要在 VM 上设置代理。 如果 VM 通过 VPN 或 ExpressRoute 连接到本地网络，则 VM 可能无法访问 Internet，并且无法下载所需的 VM 扩展，也无法通过 SAP 扩展为 SAP 主机代理收集 Azure 基础结构信息对于 Azure，请参阅[配置代理][deployment-guide-configure-proxy]。

#### <a name="configure-azure-vm-extension-for-sap"></a>配置适用于 SAP 的 Azure VM 扩展

若要确保 SAP 支持你的环境，请按照[配置适用于 sap 的 Azure 扩展][deployment-guide-4.5]中所述设置适用于 Sap 的 azure 扩展。 查看 sap 的先决条件，以及 sap[资源][deployment-guide-2.2]中列出的资源中的 sap 内核和 Sap 主机代理所需的最低版本。

#### <a name="sap-vm-extension-check"></a>SAP VM 扩展检查

查看适用于 sap[主机代理的端到端数据收集的检查和故障排除][deployment-guide-troubleshooting-chapter]中所述的适用于 SAP 的 VM 扩展是否正常工作。


### <a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>场景 3：使用包含 SAP 的非通用化 Azure VHD 移动本地 VM

在此方案中，打算将特定的 SAP 系统从本地环境移动到 Azure。 可以通过将包含 OS、SAP 二进制文件并最终包含 DBMS 二进制文件的 VHD，以及包含 DBMS 数据和日志文件的 VHD 上传到 Azure 来实现此目的。 与[方案 2：使用适用于 SAP][deployment-guide-3.3]的自定义映像部署 VM，在这种情况下，你可以在 Azure VM 中保留主机名、sap SID 和 sap 用户帐户，因为它们是在本地环境中配置的。 不需要对 OS 进行通用化。 此方案通常应用于跨界情况，在这种情况下，SAP 布局有一部分在本地运行，另一部分在 Azure 上运行。

在此方案中，在部署期间**不会**自动安装 VM 代理。 由于在 Azure 上运行 SAP NetWeaver 需要 VM 代理和适用于 SAP 的 Azure 扩展，因此，在创建虚拟机后，需要手动下载、安装并启用这两个组件。

有关 Azure VM 代理的详细信息，请参阅以下资源。

---
> ![Windows][Logo_Windows] Windows
>
> [Azure 虚拟机代理概述][virtual-machines-windows-agent-user-guide]
>
> ![Linux][Logo_Linux] Linux
>
> [Azure Linux 代理用户指南][virtual-machines-linux-agent-user-guide]
>
>

---

下面的流程图显示了使用非通用化 Azure VHD 移动本地 VM 时的步骤序列：

![使用 VM 磁盘为 SAP 系统部署 VM 的流程图][deployment-guide-figure-400]

如果已在 Azure 中上传并定义磁盘（请参阅[适用于 SAP NetWeaver 的 Azure 虚拟机规划和实施][planning-guide]），请执行以下几节中所述的任务。

#### <a name="create-a-virtual-machine"></a>创建虚拟机

若要通过 Azure 门户使用专用 OS 磁盘创建部署，请使用在[Azure 快速入门模板][azure-quickstart-templates-github]中发布的 SAP 模板 GitHub 存储库。 还可以使用 PowerShell 手动创建虚拟机。

* [**双层配置（仅一个虚拟机）模板**（sap-2 层-用户-磁盘）][sap-templates-2-tier-os-disk]

  若要仅使用一台虚拟机创建一个两层系统，请使用此模板。
* [**双层配置（仅一个虚拟机）模板托管磁盘**（sap-2 层-用户-磁盘-md）][sap-templates-2-tier-os-disk-md]

  若要仅使用一个虚拟机和托管磁盘创建一个两层系统，请使用此模板。

在 Azure 门户中，为模板输入以下参数：

1. **基本信息**：
   * **订阅**：要用来部署模板的订阅。
   * **资源组**：要用来部署模板的资源组。 可以创建一个新的资源组，也可以选择订阅中的一个现有资源组。
   * **位置**：部署模板的位置。 如果选择了一个现有资源组，则会使用该资源组的位置。
1. **设置**：
   * **SAP 系统 ID**：SAP 系统 ID。
   * **OS 类型**：要部署的操作系统类型（Windows 或 Linux）。
   * **SAP 系统大小**：SAP 系统的大小。

     新系统提供的 SAPS 的数量。 如果不确定系统需要多少 SAPS，请咨询 SAP 技术合作伙伴或系统集成商。
   * **存储类型**（仅限双层模板）：要使用的存储类型。

     对于较大的系统，我们强烈建议使用 Azure 高级存储。 有关存储类型的详细信息，请参阅以下资源：
      * [将 Azure 高级 SSD 存储用于 SAP DBMS 实例][2367194]
      * [用于 RDBMS 部署的 VM 的存储结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [高级存储：适用于 Azure 虚拟机工作负荷的高性能存储][storage-premium-storage-preview-portal]
      * [Microsoft Azure 存储简介][storage-introduction]
   * **OS 磁盘 VHD URI**（仅限非托管磁盘模板）：专用 OS 磁盘的 URI，例如 https://&lt;accountname>.blob.core.windows.net/vhds/osdisk.vhd。
   * **OS 磁盘托管磁盘 ID**（仅限托管磁盘模板）：托管磁盘 OS 磁盘的 ID，例如 /subscriptions/92d102f7-81a5-4df7-9877-54987ba97dd9/resourceGroups/group/providers/Microsoft.Compute/disks/WIN
   * **新子网或现有子网**：确定是要创建新的虚拟网络和子网，还是使用现有子网。 如果已有连接到本地网络的虚拟网络，请选择“现有”。
   * **子网 ID**：如果要将 VM 部署到现有 VNet 中，并且该 VNet 中已定义了 VM 应分配到的子网，请指定该特定子网的 ID。 ID 通常如下所示：/subscriptions/&lt;订阅 id>/resourceGroups/&lt;资源组名称>/providers/Microsoft.Network/virtualNetworks/&lt;虚拟网络名称>/subnets/&lt;子网名称>

1. **条款和条件**：  
    查看并接受法律条款。

1. 选择“购买”。

#### <a name="install-the-vm-agent"></a>安装 VM 代理

要使用前面的部分中描述的模板，必须已经在 OS 磁盘中安装了 VM 代理，否则，部署会失败。 如[下载、安装并启用 AZURE Vm 代理][deployment-guide-4.4]中所述，在 vm 中下载并安装 vm 代理。

如果不使用前面部分中描述的模板，也可以在以后安装 VM 代理。

#### <a name="join-a-domain-windows-only"></a>加入域（仅限 Windows）

如果 Azure 部署通过 Azure 站点到站点 VPN 连接或 ExpressRoute 连接到本地 Active Directory 或 DNS 实例（这在 [适用于 SAP 的 Azure 虚拟机规划和实施中称为跨界）NetWeaver][planning-guide]），应为 VM 加入本地域。 有关此任务的注意事项的详细信息，请参阅将[VM 加入本地域（仅限 Windows）][deployment-guide-4.3]。

#### <a name="configure-proxy-settings"></a>配置代理设置

根据本地网络的配置情况，可能需要在 VM 上设置代理。 如果 VM 通过 VPN 或 ExpressRoute 连接到本地网络，则 VM 可能无法访问 Internet，并且无法下载所需的 VM 扩展，也无法通过 SAP 扩展为 SAP 主机代理收集 Azure 基础结构信息对于 Azure，请参阅[配置代理][deployment-guide-configure-proxy]。

#### <a name="configure-azure-vm-extension-for-sap"></a>配置适用于 SAP 的 Azure VM 扩展

若要确保 SAP 支持你的环境，请按照[配置适用于 sap 的 Azure 扩展][deployment-guide-4.5]中所述设置适用于 Sap 的 azure 扩展。 查看 sap 的先决条件，以及 sap[资源][deployment-guide-2.2]中列出的资源中的 sap 内核和 Sap 主机代理所需的最低版本。

#### <a name="sap-vm-check"></a>SAP VM 检查

查看适用于 sap[主机代理的端到端数据收集的检查和故障排除][deployment-guide-troubleshooting-chapter]中所述的适用于 SAP 的 VM 扩展是否正常工作。

## <a name="update-the-configuration-of-azure-extension-for-sap"></a>更新适用于 SAP 的 Azure 扩展的配置

在以下任一情况下，更新适用于 SAP 的 Azure 扩展的配置：
* 联合 Microsoft/SAP 团队扩展了 VM 扩展的功能，并请求更多或更少的计数器。
* Microsoft 引入了新版本的底层 Azure 基础结构，可提供数据，并且需要对 SAP 的 Azure 扩展进行相应的更改。
* 向 Azure VM 装载更多数据磁盘，或删除数据磁盘。 在此情况下，需要更新与存储相关的数据的集合。 通过添加或删除终结点或者通过向 VM 分配 IP 地址来更改配置不会影响扩展配置。
* 更改了 Azure VM 的大小，例如，从 A5 更改为任何其他 VM 大小。
* 在 Azure VM 中添加了新的网络接口。

若要更新设置，请遵循[配置适用于 sap 的 Azure 扩展][deployment-guide-4.5]中的步骤来更新适用于 Sap 的 azure 扩展的配置。

## <a name="detailed-tasks-for-sap-software-deployment"></a>SAP 软件部署的详细任务

本部分介绍了在配置和部署流程中用于执行特定任务的详细步骤。

### <a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>部署 Azure PowerShell cmdlet

1. 转到 [Microsoft Azure 下载](https://azure.microsoft.com/downloads/)。
1. 在“命令行工具”下，在“PowerShell”下选择“Windows 安装”。
1. 在 Microsoft 下载管理器对话框中，针对已下载的文件（例如 WindowsAzurePowershellGet.3f.3f.3fnew.exe）选择“运行”。
1. 若要运行 Microsoft Web 平台安装程序 (Microsoft Web PI)，请选择“是”。
1. 将显示一个如下所示的页面：

   ![Azure PowerShell cmdlet 的安装页面][deployment-guide-figure-500]<a name="figure-5"></a>

1. 选择“安装”，并接受 Microsoft 软件许可条款。
1. PowerShell 安装完成。 选择“完成”以关闭安装向导。

请经常检查 PowerShell cmdlet 的更新，通常每月都会更新。 检查更新的最简单方法是执行上述安装步骤，直到出现步骤 5 中显示的安装页面。 步骤 5 中显示的页面上包括了 cmdlet 的发布日期和发行版号。 除非 SAP 说明 [1928533] 或 SAP 说明 [2015553] 中另有规定，否则建议使用最新版本的 Azure PowerShell cmdlet。

要检查计算机上安装的 Azure PowerShell cmdlet 的版本，请运行以下 PowerShell 命令：
```powershell
(Get-Module Az.Compute).Version
```
结果如下所示：

![Azure PowerShell cmdlet 版本检查结果][deployment-guide-figure-600]
<a name="figure-6"></a>

如果计算机上安装的 Azure cmdlet 版本是当前版本，则安装向导的第一个页面会通过将 **(已安装)** 添加到产品标题中来指明这一情况（请参阅下面的屏幕截图）。 PowerShell Azure cmdlet 是最新的。 若要关闭安装向导，请选择“退出”。

![Azure PowerShell cmdlet 的安装页面，其中指明已安装了 Azure PowerShell cmdlet 的最新版本][deployment-guide-figure-700]
<a name="figure-7"></a>

### <a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>部署 Azure CLI

1. 转到 [Microsoft Azure 下载](https://azure.microsoft.com/downloads/)。
1. 在“命令行工具”下，在“Azure 命令行接口”下选择适用于操作系统的**安装**链接。
1. 在 Microsoft 下载管理器对话框中，针对已下载的文件（例如 WindowsAzureXPlatCLI.3f.3f.3fnew.exe）选择“运行”。
1. 若要运行 Microsoft Web 平台安装程序 (Microsoft Web PI)，请选择“是”。
1. 将显示一个如下所示的页面：

   ![Azure PowerShell cmdlet 的安装页面][deployment-guide-figure-500]<a name="figure-5"></a>

1. 选择“安装”，并接受 Microsoft 软件许可条款。
1. Azure CLI 安装完成。 选择“完成”关闭安装向导。

请经常检查 Azure CLI 的更新，通常每月都会更新。 检查更新的最简单方法是执行上述安装步骤，直到出现步骤 5 中显示的安装页面。

若要检查计算机上安装的 Azure CLI 的版本，请运行以下命令：
```
azure --version
```

结果如下所示：

![Azure CLI 版本检查结果][deployment-guide-figure-760]
<a name="0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda"></a>

### <a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>将 VM 加入本地域（仅限 Windows）

如果在跨界方案（在此方案中，本地 Active Directory 和 DNS 已扩展到 Azure）中部署 SAP VM，则这些 VM 应当加入本地域。 将 VM 加入本地域需要执行的详细步骤以及成为本地域成员所需的其他软件因客户而异。 通常，要将 VM 加入本地域，需要安装其他软件，例如，反恶意软件以及备份和监视软件。

在此方案中，还需要确保当 VM 加入环境中的域时强制实施 Internet 代理设置，来宾 VM 中的 Windows 本地系统帐户 (S-1-5-18) 具有相同的代理设置。 强制实施代理的最简单选项是使用域组策略，该策略将应用于域中的所有系统。

### <a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>下载、安装并启用 Azure VM 代理

对于从非通用化的 OS 映像（例如，非来自 Windows 系统准备（或 sysprep）工具的映像）部署的虚拟机，需要手动下载、安装并启用 Azure VM 代理。

如果从 Azure 市场部署 VM，则此步骤不是必需的。 来自 Azure 市场的映像已经包含 Azure VM 代理。

#### <a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows

1. 下载 Azure VM 代理：
   1.  下载 [Azure VM 代理安装程序包](https://go.microsoft.com/fwlink/?LinkId=394789)。
   1.  将 VM 代理 MSI 包存储在个人计算机或服务器本地。
1. 安装 Azure VM 代理：
   1.  使用远程桌面协议 (RDP) 连接到已部署的 Azure VM。
   1.  在 VM 上打开一个 Windows 资源管理器窗口，并选择 VM 代理的 MSI 文件的目标目录。
   1.  将 Azure VM 代理安装程序 MSI 文件从本地计算机/服务器拖放到 VM 上 VM 代理的目标目录中。
   1.  在 VM 上双击该 MSI 文件。
1. 对于加入到本地域的 Vm，请确保最终的 Internet 代理设置也适用于 VM 中的 Windows 本地系统帐户（S-1-5-18），如[配置代理][deployment-guide-configure-proxy]中所述。 VM 代理在此上下文中运行，并且需要能够连接到 Azure。

更新 Azure VM 代理时不需要用户参与。 VM 代理会自动更新，并且不要求 VM 重新启动。

#### <a name="6889ff12-eaaf-4f3c-97e1-7c9edc7f7542"></a>Linux

可以使用以下命令来安装适用于 Linux 的 VM 代理：

* **SUSE Linux Enterprise Server (SLES)**

  ```
  sudo zypper install WALinuxAgent
  ```

* **Red Hat Enterprise Linux (RHEL) 或 Oracle Linux**

  ```
  sudo yum install WALinuxAgent
  ```

如果已安装代理，若要更新 Azure Linux 代理，请执行将[VM 上的 Azure Linux 代理更新为 GitHub 中的最新版本][virtual-machines-linux-update-agent]中所述的步骤。

### <a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>配置代理

在 Windows 中配置代理时采取的步骤不同于在 Linux 中配置代理时采取的步骤。

#### <a name="windows"></a>Windows

必须正确设置代理设置，本地系统帐户才能访问 Internet。 如果没有通过组策略设置代理设置，则可以为本地系统帐户配置这些设置。

1. 转到“开始”，输入 **gpedit.msc**，并选择 **Enter**。
1. 选择“计算机配置” > “管理模板” > “Windows 组件” > “Internet Explorer”。 确保“按计算机进行代理设置(而不是按用户)”设置已禁用或者未配置。
1. 在**控制面板**中，转到“网络和共享中心” > “Internet 选项”。
1. 在“连接”选项卡上，选择“局域网设置”按钮。
1. 清除**自动检测设置**复选框。
1. 选中“为 LAN 使用代理服务器”复选框，并输入代理地址和端口。
1. 选择“高级”按钮。
1. 在“例外”框中，输入 IP 地址 **168.63.129.16**。 选择“确定”。

#### <a name="linux"></a>Linux

在 Microsoft Azure 来宾代理的配置文件（位于 \\etc\\waagent.conf 中）中配置正确的代理。

设置以下参数：

1. **HTTP 代理主机**。 例如，设置为 **proxy.corp.local**。
   ```
   HttpProxy.Host=<proxy host>

   ```
1. **HTTP 代理端口**。 例如，将其设置为 **80**。
   ```
   HttpProxy.Port=<port of the proxy host>

   ```
1. 重新启动该代理。

   ```
   sudo service waagent restart
   ```

\\etc\\waagent.conf 中的代理设置也应用于所需的 VM 扩展。 如果想要使用 Azure 存储库，请确保这些存储库的流量不会流经本地 Intranet。 如果已创建用户定义的路由来启用强制隧道，请确保添加路由，将发往存储库的流量直接路由到 Internet，而不是通过点对点 VPN 连接进行路由。

* **SLES**

  此外，还需要为 \\etc\\regionserverclnt.cfg 中列出的 IP 地址添加路由。 以下代码展示了一个示例：

  ![强制隧道][deployment-guide-figure-50]


* **RHEL**

  还需要为 \\etc\\yum.repos.d\\rhui-load-balancers 中列出的主机的 IP 地址添加路由。 有关示例，请参阅上图。

* **Oracle Linux**

  Azure 上的 Oracle Linux 没有存储库。 需要配置自己的 Oracle Linux 存储库，或使用公共存储库。

有关用户定义路由的详细信息，请参阅[用户定义的路由和 IP 转发][virtual-networks-udr-overview]。

### <a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>配置适用于 SAP 的 Azure 扩展

如果已根据[azure 上的 SAP 的 Vm 部署方案][deployment-guide-3]中所述准备好 VM，则会在虚拟机上安装 Azure VM 代理。 下一步是部署适用于 SAP 的 Azure 扩展，该扩展可在全球 Azure 数据中心内的 Azure 扩展存储库中找到。 有关详细信息，请参阅[适用于 SAP NetWeaver 的 Azure 虚拟机规划和实施][planning-guide-9.1]。

可以使用 PowerShell 或 Azure CLI 来安装和配置适用于 SAP 的 Azure 扩展。 若要使用 Windows 计算机在 Windows 或 Linux VM 上安装扩展，请参阅[Azure PowerShell][deployment-guide-4.5.1]。 若要使用 Linux 台式机在 Linux VM 上安装扩展，请参阅[Azure CLI][deployment-guide-4.5.2]。

#### <a name="987cf279-d713-4b4c-8143-6b11589bb9d4"></a>适用于 Linux 和 Windows VM 的 Azure PowerShell

使用 PowerShell 安装适用于 SAP 的 Azure 扩展：

1. 确保已安装最新版本的 Azure PowerShell cmdlet。 有关详细信息，请参阅[部署 Azure PowerShell cmdlet][deployment-guide-4.1]。  
1. 运行以下 Azure PowerShell cmdlet。
    若要获得可用环境的列表，请运行 `commandlet Get-AzEnvironment`。 如果想要使用全局 Azure，则环境是 **AzureCloud**。 对于中国区 Azure，请选择 **AzureChinaCloud**。

    ```powershell
    $env = Get-AzEnvironment -Name <name of the environment>
    Connect-AzAccount -Environment $env
    Set-AzContext -SubscriptionName <subscription name>

    Set-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
    ```

在输入帐户数据并标识 Azure 虚拟机名，该脚本将部署所需的扩展，并启用所需的功能。 这可能需要几分钟。
有关的详细信息`Set-AzVMAEMExtension`，请参阅[AzVMAEMExtension][msdn-set-Azvmaemextension]。

![成功执行特定于 SAP 的 Azure cmdlet AzVMAEMExtension][deployment-guide-figure-900]

`Set-AzVMAEMExtension`配置执行所有步骤来为 SAP 配置主机数据收集。

脚本输出包括以下信息：

* 确认已配置 OS 磁盘和所有其他数据磁盘的数据收集。
* 接下来的两条消息确认已配置了特定存储帐户的存储度量值。
* 一行输出为 SAP 配置提供 VM 扩展的实际更新状态。
* 另一行输出确认配置已部署或已更新。
* 最后一行输出是信息性的。 其中显示了用于测试用于 SAP 配置的 VM 扩展的选项。
* 若要检查是否已成功执行适用于 SAP 的 Azure VM 扩展配置的所有步骤，以及 Azure 基础结构是否提供必要的数据，请继续检查适用于 SAP 的 Azure 扩展的就绪状态，如就绪状态中所述[适用于 SAP 的 Azure 扩展][deployment-guide-5.1]。
* 等待 15-30 分钟以便 Azure 诊断以收集相关数据。

#### <a name="408f3779-f422-4413-82f8-c57a23b4fc2f"></a>适用于 Linux VM 的 Azure CLI

使用 Azure CLI 安装适用于 SAP 的 Azure 扩展：

   1. 安装 azure 经典 CLI，如[安装 azure 经典 cli][azure-cli]中所述。
   1. 使用 Azure 帐户进行登录：

      ```
      azure login
      ```

   1. 切换到 Azure 资源管理器模式：

      ```
      azure config mode arm
      ```

   1. 启用适用于 SAP 的 Azure 扩展：

      ```
      azure vm enable-aem <resource-group-name> <vm-name>
      ```

1. 使用 Azure CLI 2.0 进行安装

   1. 安装 Azure CLI 2.0，如[安装 Azure CLI 2.0][azure-cli-2]中所述。
   1. 使用 Azure 帐户进行登录：

      ```
      az login
      ```

   1. 安装 Azure CLI AEM 扩展
  
      ```
      az extension add --name aem
      ```
  
   1. 通过以下命令安装扩展
  
      ```
      az vm aem set -g <resource-group-name> -n <vm name>
      ```

1. 验证 azure Extension for SAP 是否在 Azure Linux VM 上处于活动状态。 检查 \\var\\lib\\AzureEnhancedMonitor\\PerfCounters 文件是否存在。 如果它存在，请在命令提示符下运行以下命令，以显示 Azure 扩展 for SAP 收集的信息：

   ```
   cat /var/lib/AzureEnhancedMonitor/PerfCounters
   ```

   输出如下所示：
   ```
   ...
   2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
   2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
   ...
   ```

## <a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>SAP 主机代理的端到端数据收集的检查和故障排除

部署 Azure VM 并设置适用于 SAP 的相关 Azure 扩展后，请检查扩展的所有组件是否按预期方式工作。

针对适用于[sap 的 Azure 扩展的就绪状态检查][deployment-guide-5.1]中所述，对适用于 Sap 的 azure 扩展运行就绪状态检查。 如果所有就绪状态检查结果都是肯定的并且所有相关的性能计数器都显示为 "正常"，则已成功设置适用于 SAP 的 Azure 扩展。 可以按照 [sap 说明][deployment-guide-2.2]中的 sap 说明中所述, 继续安装 SAP 主机代理。 如果就绪状态检查指出缺少计数器，请根据适用于[sap 的 Azure 扩展配置的运行状况检查][deployment-guide-5.2]中所述，对适用于 Sap 的 azure 扩展运行运行状况检查。 有关更多故障排除选项，请参阅[Azure Extension FOR SAP 的故障排除][deployment-guide-5.3]。

### <a name="bb61ce92-8c5c-461f-8c53-39f5e5ed91f2"></a>适用于 SAP 的 Azure 扩展的就绪状态检查

此检查可确保 sap 应用程序中显示的所有性能度量值都由适用于 SAP 的底层 Azure 扩展提供。

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>在 Windows VM 上运行就绪状态检查

1. 登录到 Azure 虚拟机（不需要使用管理员帐户）。
1. 打开命令提示符窗口。
1. 在命令提示符下，将目录更改为适用于 SAP 的 Azure 扩展的安装文件夹：C:\\Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;version>\\drop

   扩展路径中的*版本*可能会有所不同。 如果在安装文件夹中看到扩展的多个版本的文件夹，请检查 AzureEnhancedMonitoring Windows 服务的配置，并切换到 "*可执行文件的路径*" 指示的文件夹。

   ![运行适用于 SAP 的 Azure 扩展的服务的属性][deployment-guide-figure-1000]

1. 在命令提示符下，在不使用任何参数的情况下运行 **azperflib.exe**。

   > [!NOTE]
   > Azperflib.exe 以循环方式运行，并且每隔 60 秒更新一次所收集的计数器。 若要结束循环，请关闭命令提示符窗口。
   >
   >

如果未安装适用于 SAP 的 Azure 扩展，或者 AzureEnhancedMonitoring 服务未运行，则表示未正确配置该扩展。 有关如何部署该扩展的详细信息，请参阅对[适用于 SAP 的 Azure 扩展进行故障排除][deployment-guide-5.3]。

> [!NOTE]
> Azperflib.exe 是一个不能用于自身的组件。 它是一个组件，它以独占方式为 SAP 主机代理提供与 VM 相关的 Azure 基础结构数据。
> 

##### <a name="check-the-output-of-azperflibexe"></a>检查 azperflib.exe 的输出

Azperflib.exe 输出会显示针对 SAP 的所有已填充的 Azure 性能计数器。 在收集的计数器列表的底部，摘要和运行状况指示器显示适用于 SAP 的 Azure 扩展的状态。

![通过执行 azperflib.exe 完成的运行状况检查的输出，该输出表明不存在任何问题][deployment-guide-figure-1100]
<a name="figure-11"></a>

检查针对 **Counters total** 输出返回的结果中报告为空的结果，并检查针对 **Health status** 返回的结果，如上图所示。

结果值解释如下：

| Azperflib.exe 结果值 | 适用于 SAP 运行状态的 Azure 扩展 |
| --- | --- |
| **API 调用 - 不可用** | 不可用的计数器可能不适用于虚拟机配置，也可能是错误。 请参阅 **Health status**。 |
| **Counters total - empty** |以下两个 Azure 存储计数器可以为空： <ul><li>Storage Read Op Latency Server msec</li><li>Storage Read Op Latency E2E msec</li></ul>所有其他计数器都必须具有值。 |
| **Health status** |仅当返回状态显示为 **OK** 时才表示正常。 |
| **诊断** |有关运行状况的详细信息。 |

如果 "**健康**状况" 值不是 **"正常**"，请按照[适用于 SAP 配置的 Azure 扩展运行状况检查][deployment-guide-5.2]中的说明进行操作。

#### <a name="run-the-readiness-check-on-a-linux-vm"></a>在 Linux VM 上运行就绪状态检查

1. 使用 SSH 连接到 Azure 虚拟机。

1. 查看适用于 SAP 的 Azure 扩展的输出。

   a.  运行 `more /var/lib/AzureEnhancedMonitor/PerfCounters`

   **预期结果**：返回性能计数器的列表。 文件不应为空。

   b. 运行 `cat /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error`

   **预期结果**：返回一个行，其中，错误为“none”，例如“3;config;Error;;0;0;none;0;1456416792;tst-servercs;”

   c. 运行 `more /var/lib/AzureEnhancedMonitor/LatestErrorRecord`

   **预期结果**：返回为空或不存在。

如果前面的检查未成功，请运行以下额外的检查：

1. 确保已安装并启用了 waagent。

   a.  运行 `sudo ls -al /var/lib/waagent/`

     **预期结果**：列出 waagent 目录的内容。

   b.  运行 `ps -ax | grep waagent`

   **预期结果**：显示一个类似于以下内容的条目：`python /usr/sbin/waagent -daemon`

1. 请确保已安装并运行适用于 SAP 的 Azure 扩展。

   a.  运行 `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/'`

   **预期结果**：列出了适用于 SAP 的 Azure 扩展目录的内容。

   b. 运行 `ps -ax | grep AzureEnhanced`

   **预期结果**：显示一个类似于以下内容的条目：`python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon`

1. 如 SAP 说明 [1031096] 中所述安装 SAP 主机代理，并检查 `saposcol` 的输出。

   a.  运行 `/usr/sap/hostctrl/exe/saposcol -d`

   b.  运行 `dump ccm`

   c.  检查 **Virtualization_Configuration\Enhanced Monitoring Access** 度量值是否为 **true**。

如果已安装 SAP NetWeaver ABAP 应用程序服务器，请打开事务 ST06，并检查是否已启用增强型监视。

如果这些检查中有任何一个失败，以及有关如何重新部署该扩展的详细信息，请参阅对[适用于 SAP 的 Azure 扩展进行故障排除][deployment-guide-5.3]。

### <a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>适用于 SAP 的 Azure 扩展配置的运行状况检查

如果未按照[针对 sap 的 azure 扩展准备情况检查][deployment-guide-5.1]中所述的测试正确传递某些基础结构数据，请运行`Test-AzVMAEMExtension` cmdlet 来检查 azure 基础结构和适用于 sap 的 azure 扩展是否配置正确。

1. 请确保已安装最新版本的 Azure PowerShell cmdlet，如[部署 Azure PowerShell cmdlet][deployment-guide-4.1]中所述。
1. 运行以下 Azure PowerShell cmdlet。 若要获得可用环境的列表，请运行 cmdlet `Get-AzEnvironment`。 若要使用全局 Azure，请选择 **AzureCloud** 环境。 对于中国区 Azure，请选择 **AzureChinaCloud**。
   ```powershell
   $env = Get-AzEnvironment -Name <name of the environment>
   Connect-AzAccount -Environment $env
   Set-AzContext -SubscriptionName <subscription name>
   Test-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
   ```

1. 输入帐户数据并标识 Azure 虚拟机。

   ![特定于 SAP 的 Azure cmdlet Test-VMConfigForSAP_GUI 的输入页面][deployment-guide-figure-1200]

1. 脚本将测试你选择的虚拟机的配置。

   ![成功测试适用于 SAP 的 Azure 扩展的输出][deployment-guide-figure-1300]

请确保每个运行状况检查结果都是 **OK**。 如果某些检查没有显示 **"正常"** ，请根据[配置适用于 SAP 的 Azure 扩展][deployment-guide-4.5]中所述运行 update cmdlet。 等待15分钟，并重复检查[适用于 sap 的 Azure 扩展的就绪状态检查][deployment-guide-5.1]和适用于 Sap 的[Azure 扩展的运行状况检查][deployment-guide-5.2]中所述的检查。 如果检查仍然指出部分或所有计数器存在问题，请参阅[对适用于 SAP 的 Azure 扩展进行故障排除][deployment-guide-5.3]。

> [!Note]
> 如果使用的是托管的标准 Azure 磁盘，则可能会收到一些警告。 测试将显示警告而非返回“正常”。 在使用该磁盘类型的情况下，这是正常的并且是预料中的行为。 另请参阅[疑难解答 Azure 扩展 FOR SAP][deployment-guide-5.3]
> 

### <a name="fe25a7da-4e4e-4388-8907-8abc2d33cfd8"></a>适用于 SAP 的 Azure 扩展故障排除

#### <a name="windowslogo_windows-azure-performance-counters-do-not-show-up-at-all"></a>![Windows][Logo_Windows] Azure 性能计数器根本未显示

AzureEnhancedMonitoring Windows 服务在 Azure 中收集性能度量值。 如果该服务未正确安装或者未在 VM 中运行，则无法收集任何性能度量值。

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>适用于 SAP 的 Azure 扩展的安装目录为空

###### <a name="issue"></a>问题

安装目录 C:\\Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;版本>\\drop 为空。

###### <a name="solution"></a>解决方案

未安装该扩展。 确定这是否为代理问题（如前文所述）。 可能需要重新启动计算机或重新运行 `Set-AzVMAEMExtension` 配置脚本。

##### <a name="service-for-azure-extension-for-sap-does-not-exist"></a>适用于 SAP 的 Azure 扩展的服务不存在

###### <a name="issue"></a>问题

Windows 服务 AzureEnhancedMonitoring 不存在。

Azperflib.exe 输出引发了一个错误：

![执行 azperflib.exe 表示适用于 SAP 的 Azure 扩展的服务未运行][deployment-guide-figure-1400]
<a name="figure-14"></a>

###### <a name="solution"></a>解决方案

如果该服务不存在，则表示未正确安装适用于 SAP 的 Azure 扩展。 使用[Azure 中适用于 SAP 的 Vm 部署方案][deployment-guide-3]中所述的步骤，重新部署扩展。

部署该扩展后，在一小时后重新检查 Azure VM 中是否提供了 Azure 性能计数器。

##### <a name="service-for-azure-extension-for-sap-exists-but-fails-to-start"></a>适用于 SAP 的 Azure 扩展服务存在，但无法启动

###### <a name="issue"></a>问题

Windows 服务 AzureEnhancedMonitoring 存在并已启用，但无法启动。 有关详细信息，请检查应用程序事件日志。

###### <a name="solution"></a>解决方案

配置不正确。 如[配置适用于 sap 的 Azure 扩展][deployment-guide-4.5]中所述，在 VM 中重启适用于 Sap 的 azure 扩展。

#### <a name="windowslogo_windows-some-azure-performance-counters-are-missing"></a>![Windows][Logo_Windows] 缺少某些 Azure 性能计数器

AzureEnhancedMonitoring Windows 服务在 Azure 中收集性能指标。 该服务从多个来源获取数据。 某些配置数据是从本地收集的，某些性能指标是从 Azure 诊断读取的。 存储计数器通过日志记录在存储订阅级别使用。

如果使用 SAP 说明 [1999351] 进行故障排除没有解决问题，请重新运行 `Set-AzVMAEMExtension` 配置脚本。 可能必须要等待一小时，因为在启用存储分析或诊断计数器后可能不会立即创建这些计数器。 如果问题依然存在，请为 Windows 虚拟机组件 BC-OP-NT-AZR 或 Linux 虚拟机组件 BC-OP-LNX-AZR 创建一条 SAP 客户支持消息。

#### <a name="linuxlogo_linux-azure-performance-counters-do-not-show-up-at-all"></a>![Linux][Logo_Linux] Azure 性能计数器根本未显示

Azure 中的性能度量值是由某个守护程序收集的。 如果该守护程序未运行，则无法收集任何性能度量值。

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>适用于 SAP 的 Azure 扩展的安装目录为空

###### <a name="issue"></a>问题

目录\\var\\lib\\waagent没有用于SAP的Azure扩展的子目录。\\

###### <a name="solution"></a>解决方案

未安装该扩展。 确定这是否为代理问题（如前所述）。 可能需要重新启动计算机并/或重新运行 `Set-AzVMAEMExtension` 配置脚本。

##### <a name="the-execution-of-set-azvmaemextension-and-test-azvmaemextension-show-warning-messages-stating-that-standard-managed-disks-are-not-supported"></a>AzVMAEMExtension 和 AzVMAEMExtension 的执行将显示警告消息，指出不支持标准托管磁盘

###### <a name="issue"></a>问题

执行 AzVMAEMExtension 或 AzVMAEMExtension 消息时，将显示如下内容：

<pre><code>
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
</code></pre>

如上文所述执行 azperfli.exe 可能会得到指示非正常状态的结果。 

###### <a name="solution"></a>解决方案

这种情况的原因是标准托管磁盘不提供 sap 扩展 for SAP 使用的 Api 来检查标准 Azure 存储帐户的统计信息。 这不是一个值得关注的问题。 引入标准磁盘存储帐户的收集数据的原因是发生频繁发生的 i/o 限制。 托管磁盘会通过限制存储帐户中的磁盘数避免这类限流。 因此，这种类型的数据不是至关重要的。


#### <a name="linuxlogo_linux-some-azure-performance-counters-are-missing"></a>![Linux][Logo_Linux] 缺少某些 Azure 性能计数器

Azure 中的性能度量值是由某个守护程序收集的，该守护程序从多个来源获取数据。 某些配置数据是从本地收集的，某些性能度量值是从 Azure 诊断读取的。 存储计数器来自存储订阅中的日志。

有关已知问题的完整最新列表，请参阅 SAP 说明[1999351]，其中包含适用于 SAP 的 Azure 扩展的其他疑难解答信息。

如果使用 SAP 说明[1999351]进行故障排除不能解决此问题，请`Set-AzVMAEMExtension`根据[配置适用于 SAP 的 Azure 扩展][deployment-guide-4.5]中所述，重新运行配置脚本。 可能必须要等待一小时，因为在启用存储分析或诊断计数器后可能不会立即创建这些计数器。 如果问题依然存在，请为 Windows 虚拟机组件 BC-OP-NT-AZR 或 Linux 虚拟机组件 BC-OP-LNX-AZR 创建一条 SAP 客户支持消息。
