---
title: 适用于 SAP 工作负荷的 Oracle Azure 虚拟机 DBMS 部署 | Microsoft Docs
description: 适用于 SAP 工作负荷的 Oracle Azure 虚拟机 DBMS 部署
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/14/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b912743c758f33173b568944341fab4e815300ed
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099988"
---
# <a name="azure-virtual-machines-dbms-deployment-for-sap-workload"></a>适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 部署

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1114181]: https://launchpad.support.sap.com/#/notes/1114181
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]: https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
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
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2039619]: https://launchpad.support.sap.com/#/notes/2039619
[2069760]: https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2171857]: https://launchpad.support.sap.com/#/notes/2171857
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]: https://launchpad.support.sap.com/#/notes/2243692

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md 
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f 
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b 
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d 
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c 
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8
[dbms-guide-managed-disks]:dbms-guide.md#f42c6cb5-d563-484d-9667-b07ae51bce29

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:deployment-guide.md 
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab 
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e 
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e 
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc 
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d 
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f 
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca 
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b 
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d 
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
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b

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

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:planning-guide.md 
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff 
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f 
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a 
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c 
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef 
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a 
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d 
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 

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
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd 
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f 

[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam 
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
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
[virtual-machines-azurerm-versus-azuresm]:../../../resource-manager-deployment-model.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md 
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md 
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
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
[virtual-machines-manage-availability-linux]:../../linux/manage-availability.md
[virtual-machines-manage-availability-windows]:../../windows/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-create-powershell.md
[virtual-machines-sizes-linux]:../../linux/sizes.md
[virtual-machines-sizes-windows]:../../windows/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:./../../windows/sql/virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:./../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:./../../windows/sql/virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/resources/templates/sql-server-2014-alwayson-existing-vnet-and-ad/
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


本文档介绍在 Azure IaaS 中部署适用于 SAP 工作负荷的 Oracle Database 时要考虑的多个不同领域。 在阅读本文档之前，我们建议阅读[适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 部署注意事项](dbms_guide_general.md)。 此外，我们建议阅读 [Azure 上的 SAP 工作负荷文档](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)中的其他指南。 

有关支持在 Azure 上的 Oracle 中运行 SAP 的 Oracle 版本及相应 OS 版本，可参阅 SAP 说明 [2039619]。

有关在 Oracle 上运行 SAP Business Suite 的常规信息可在 [Oracle 上的 SAP](https://www.sap.com/community/topic/oracle.html) 中找到。
Oracle 支持在 Microsoft Azure 上运行 Oracle 软件。 有关 Windows Hyper-V 和 Azure 常规支持的详细信息，请查看 [Oracle 和 Microsoft Azure 常见问题解答](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)。 

## <a name="sap-notes-relevant-for-oracle-sap-and-azure"></a>与 Oracle、SAP 和 Azure 相关的 SAP 说明 

以下 SAP 说明与 Azure 上的 SAP 相关。

| 说明文档编号 | 标题 |
| --- | --- |
| [1928533] |Azure 上的 SAP 应用程序：支持的产品和 Azure VM 类型 |
| [2015553] |Microsoft Azure 上的 SAP：支持先决条件 |
| [1999351] |适用于 SAP 的增强型 Azure 监视故障排除 |
| [2178632] |Microsoft Azure 上的 SAP 关键监视指标 |
| [2191498] |Azure 的 Linux 上的 SAP：增强型监视 |
| [2039619] |Microsoft Azure 上使用 Oracle Database 的 SAP 应用程序：支持的产品和版本 |
| [2243692] |Microsoft Azure (IaaS) VM 上的 Linux：SAP 许可证问题 |
| [2069760] |Oracle Linux 7.x SAP 安装和升级 |
| [1597355] |适用于 Linux 的交换空间建议 |
| [2171857] |Oracle Database 12c - Linux 上的文件系统支持 |
| [1114181] |Oracle Database 11g - Linux 上的文件系统支持 |

Oracle 和 Azure 上的 SAP 支持确切的配置和功能，如 SAP 说明 [#2039619](https://launchpad.support.sap.com/#/notes/2039619) 所述。

Windows 和 Oracle Linux 是 Oracle 和 Azure 上的 SAP 唯一支持的操作系统。 不支持将广泛使用的 SLES 和 RHEL Linux 分发版用于在 Azure 中部署 Oracle 组件。 Oracle 组件包含 Oracle Database 客户端，SAP 应用程序使用该客户端针对 Oracle DBMS 进行连接。 

根据 SAP Note [#2039619](https://launchpad.support.sap.com/#/notes/2039619) 的例外情况是 SAP 组件，它们不使用 Oracle Database 客户端。 此类 SAP 组件是 SAP 的独立排队、消息服务器、排队复制服务、WebDispatcher 和 SAP 网关。  

即使在 Oracle Linux 上运行 Oracle DBMS 和 SAP 应用程序实例，也可以在 SLES 或 RHEL 上运行 SAP Central Services，并使用基于群集的 Pacemaker 对其进行保护。 Oracle Linux 不支持 Pacemaker 作为高可用性框架。

## <a name="specifics-for-oracle-database-on-windows"></a>有关 Windows 上的 Oracle Database 的具体信息

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms-on-windows"></a>在 Windows 上的 Azure VM 中安装 SAP 的 Oracle 配置准则

根据 SAP 安装手册，所有与 Oracle 相关的文件都不应安装到或位于 VM 的 OS 磁盘（驱动器 c：）的系统驱动程序中。 不同大小的虚拟机支持附加不同数量的磁盘。 较小的虚拟机类型支持附加较少数量的磁盘。 

如果 VM 较小，我们建议在 OS 磁盘中安装/定位 Oracle home、stage、“saptrace”、“saparch”、“sapbackup”、“sapcheck”或“sapreorg”。 Oracle DBMS 组件的这些部分在 I/O 和 I/O 吞吐量方面并不密集。 这意味着，OS 磁盘可以处理 I/O 要求。 OS 磁盘的默认大小为 127 GB。 

如果可用空间不足，则可以将磁盘[调整大小](https://docs.microsoft.com/azure/virtual-machines/windows/expand-os-disk)到 2048 GB。 Oracle Database 和重做日志文件需要存储在单独的数据磁盘上。 Oracle 临时表空间会发生异常。 可以在 D:/（非持久驱动器）上创建临时文件。 非持久性驱动器 D:\ 还提供更大的 I/O 延迟和吞吐量（除 A 系列 VM 外）。 

若要确定正确的临时文件空间，可在现有系统上检查临时文件的大小。

### <a name="storage-configuration"></a>存储配置
仅支持一个使用 NTFS 格式化磁盘的 Oracle 实例。 所有数据库文件都必须存储在托管磁盘（推荐）或 VHD 的 NTFS 文件系统上。 这些磁盘装载到 Azure VM，基于 [Azure 页 Blob 存储](https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs)或 [Azure 托管磁盘](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview)。 

我们强烈建议使用 [Azure 托管磁盘](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview)。 另外，我们强烈建议使用[高级 SSD](../../windows/disks-types.md) 进行 Oracle Database 部署。

Azure 文件服务等网络驱动器或远程共享不支持 Oracle Database 文件。 有关详细信息，请参阅：

- [Microsoft Azure 文件服务简介](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)

- [将连接保存到 Microsoft Azure 文件中](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)


如果使用基于 Azure 页 Blob 存储或托管磁盘的磁盘时，[适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 部署的注意事项](dbms_guide_general.md)中的表述也适用于利用 Oracle Database 进行的部署。

存在 Azure 磁盘的 IOPS 吞吐量的配额。 [适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 部署注意事项](dbms_guide_general.md)中解释了此概念。 确切的配额因所用 VM 类型而异。 可以在[Azure 中的 Windows 虚拟机的大小][virtual-machines-sizes-windows]找到 VM 类型及其配额的列表。

若要确定支持的 Azure VM 类型，请参阅 SAP 说明 [1928533]。

最低配置如下： 

| 组件 | 磁盘 | 正在缓存 | 存储池 |
| --- | ---| --- | --- |
| \oracle\<SID>\origlogaA & mirrlogB | 高级 | 无 | 无需 |
| \oracle\<SID>\origlogaB & mirrlogA | 高级 | 无 | 无需 |
| \oracle\<SID>\sapdata1...n | 高级 | 只读 | 可使用 |
| \oracle\<SID>\oraarch | 标准 | 无 | 无需 |
| Oracle 主页, saptrace, ... | 操作系统磁盘 | | 无需 |


托管联机重做日志的磁盘选择应由 IOP 要求驱动。 只要大小、IOPS 和吞吐量满足要求，就可以将所有 sapdata1...n（表空间）存储在一个已装载的磁盘上。 

性能配置如下：

| 组件 | 磁盘 | 正在缓存 | 存储池 |
| --- | ---| --- | --- |
| \oracle\<SID>\origlogaA | 高级 | 无 | 可使用  |
| \oracle\<SID>\origlogaB | 高级 | 无 | 可使用 |
| \oracle\<SID>\mirrlogAB | 高级 | 无 | 可使用 |
| \oracle\<SID>\mirrlogBA | 高级 | 无 | 可使用 |
| \oracle\<SID>\sapdata1...n | 高级 | 只读 | 建议  |
| \oracle\SID\sapdata(n+1)* | 高级 | 无 | 可使用 |
| \oracle\<SID>\oraarch* | 高级 | 无 | 无需 |
| Oracle 主页, saptrace, ... | 操作系统磁盘 | 无需 |

*(n+1)：托管 SYSTEM、TEMP 和 UNDO 表空间。 系统和撤消表空间的 I/O 模式与托管应用程序数据的其他表空间不同。 无缓存是系统和撤消表空间性能的最佳选择。

*oraarch：性能视图中不需要存储池。 它可用于获取更多空间。

如果需要更多 IOPS，建议使用 Windows 存储池（仅适用于 Windows Server 2012 和更高版本），基于已装载的多个磁盘来创建一个大型逻辑设备。 这种方法可以简化磁盘空间的管理开销，帮助避免跨已装载的多个磁盘手动分发文件。


#### <a name="write-accelerator"></a>写入加速器
与 Azure 高级存储相比，Azure M 系列 VM 可通过多种因素减少写入联机重做日志的延迟。 基于 Azure 高级存储（用于联机重做日志文件）为磁盘 (VHD) 启用 Azure 写入加速器。 有关详细信息，请参阅[写入加速器](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)。


### <a name="backuprestore"></a>备份/还原
支持通过适用于 Oracle 的 SAP BR* 工具提供备份/还原功能，其方式与在标准 Windows Server 操作系统上一样。 Oracle 恢复管理器 (RMAN) 也支持备份到磁盘以及从磁盘还原。

还可以使用 Azure 备份来运行应用程序一致性 VM 备份。 [在 Azure 中规划 VM 备份基础结构](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)指出，Azure 备份使用 Windows VSS 功能来执行应用程序一致性备份。 Azure 上 SAP 支持的 Oracle DBMS 版本可以利用 VSS 功能进行备份。 有关详细信息，请阅读 Oracle 文档 [使用 VSS 进行数据库备份和还原的基本概念](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/ntqrf/basic-concepts-of-database-backup-and-recovery-with-vss.html#GUID-C085101B-237F-4773-A2BF-1C8FD040C701)。


### <a name="high-availability"></a>高可用性
支持通过 Oracle Data Guard 实现高可用性和灾难恢复。 要在 Data Guard 中实现自动故障转移，需要使用快速启动故障转移 (FSFA)。 观察者 (FSFA) 触发故障转移。 如果不使用 FSFA，则只能使用手动故障转移配置。

有关 Azure 中 Oracle Database 灾难恢复的详细信息，请参阅 [Azure 环境中 Oracle Database 12c 数据库的灾难恢复](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-disaster-recovery)。

### <a name="accelerated-networking"></a>更快的网络连接
对于 Window 上的 Oracle 部署，我们强烈建议根据 [Azure 加速网络](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)中所述使用加速网络。 也可以考虑[适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 部署的注意事项](dbms_guide_general.md)中的建议。 
### <a name="other"></a>其他
[适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 部署注意事项](dbms_guide_general.md)中介绍了使用 Oracle Database 的 VM 部署相关的其他重要概念，包括 Azure 可用性集和 SAP 监视。

## <a name="specifics-for-oracle-database-on-oracle-linux"></a>有关 Oracle Linux 上的 Oracle Database 的具体信息
Oracle 支持在 Oracle Linux 作为来宾 OS 的 Microsoft Azure 上运行 Oracle 软件。 有关 Windows Hyper-V 和 Azure 常规支持的详细信息，请参阅 [Azure 和 Oracle 常见问题解答](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)。 

SAP 应用程序使用 Oracle Database 的特定方案也受支持。 详细信息将在本文档的下一部分中讨论。

### <a name="oracle-version-support"></a>Oracle 版本支持
有关支持在 Azure 虚拟机上的 Oracle 中运行 SAP 的 Oracle 版本及相应 OS 版本的信息，请参阅 SAP 说明 [2039619]。

有关在 Oracle 上运行 SAP Business Suite 的常规信息，请参阅 [Oracle 上的 SAP 社区页](https://www.sap.com/community/topic/oracle.html)。

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms-on-linux"></a>在 Linux 上的 Azure VM 中安装 SAP 的 Oracle 配置准则

根据 SAP 安装手册，与 Oracle 相关的文件不应安装到或位于 VM 的启动盘的系统驱动程序中。 不同大小的虚拟机支持附加不同数量的磁盘。 较小的虚拟机类型支持附加较少数量的磁盘。 

在这种情况下，建议在启动磁盘中安装/定位 Oracle home、stage、saptrace、saparch、sapbackup、sapcheck 或 sapreorg。 Oracle DBMS 组件的这些部分在 I/O 和 I/O 吞吐量方面并不密集。 这意味着，OS 磁盘可以处理 I/O 要求。 OS 磁盘的默认大小为 30 GB。 可以使用 Azure 门户、PowerShell 或 CLI 来扩展启动盘。 扩展启动盘后，可为 Oracle 二进制文件添加其他分区。


### <a name="storage-configuration"></a>存储配置

Azure 上的 Oracle Database 文件支持 ext4、xfs 或 Oracle ASM 的文件系统。 所有数据库文件都必须存储在基于 VHD 或托管磁盘的这些文件系统上。 这些磁盘装载到 Azure VM，基于 [Azure 页 Blob 存储](<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>)或 [Azure 托管磁盘](../../windows/managed-disks-overview.md)。

对于 Oracle Linux UEK 内核，支持 [Azure 高级 SSD](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-caching) 至少需要 UEK 版本 4。

强烈建议使用 [Azure 托管磁盘](../../windows/managed-disks-overview.md)。 另外，还强烈建议使用 [Azure 高级 SSD](../../windows/disks-types.md) 进行 Oracle Database 部署。

Azure 文件服务等网络驱动器或远程共享不支持 Oracle Database 文件。 有关详细信息，请参阅以下主题： 

- [Microsoft Azure 文件服务简介](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)

- [将连接保存到 Microsoft Azure 文件中](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)

如果使用基于 Azure 页 Blob 存储或托管磁盘的磁盘，[适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 部署的注意事项](dbms_guide_general.md)中的表述也适用于利用 Oracle Database 进行的部署。

 存在 Azure 磁盘的 IOPS 吞吐量的配额。 [适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 部署注意事项](dbms_guide_general.md)中解释了此概念。确切的配额因所用 VM 类型而异。 有关其配额的 VM 类型的列表, 请参阅[Azure 中 Linux 虚拟机的大小][virtual-machines-sizes-linux]。

若要确定支持的 Azure VM 类型，请参阅 SAP 说明 [1928533]。

最低配置：

| 组件 | 磁盘 | 正在缓存 | 撤消* |
| --- | ---| --- | --- |
| /oracle/\<SID >/origlogaA & mirrlogB | 高级 | None | 无需 |
| /oracle/\<SID >/origlogaB & mirrlogA | 高级 | None | 无需 |
| /oracle/\<SID >/sapdata1...北 | 高级 | 只读 | 可使用 |
| /oracle/\<SID >/oraarch | 标准 | None | 无需 |
| Oracle 主页, saptrace, ... | 操作系统磁盘 | | 无需 |

*撤消：使用 RAID0 的 LVM 带状线或 MDADM

托管 Oracle 的联机重做日志的磁盘选择应由 IOP 要求驱动。 只要卷、IOPS 和吞吐量满足要求，就可以将所有 sapdata1...n（表空间）存储在一个已装载的磁盘上。 

性能配置：

| 组件 | 磁盘 | 正在缓存 | 撤消* |
| --- | ---| --- | --- |
| /oracle/\<SID >/origlogaA | 高级 | 无 | 可使用  |
| /oracle/\<SID >/origlogaB | 高级 | None | 可使用 |
| /oracle/\<SID >/mirrlogAB | 高级 | 无 | 可使用 |
| /oracle/\<SID>/mirrlogBA | 高级 | 无 | 可使用 |
| /oracle/\<SID >/sapdata1...北 | 高级 | 只读 | 建议  |
| /oracle/\<SID >/sapdata (n + 1) * | 高级 | 无 | 可使用 |
| /oracle/\<SID >/oraarch * | 高级 | 无 | 无需 |
| Oracle 主页, saptrace, ... | 操作系统磁盘 | 无需 |

*撤消：使用 RAID0 的 LVM 带状线或 MDADM

*(n+1)：托管 SYSTEM、TEMP 和 UNDO 表空间。系统和撤消表空间的 I/O 模式与托管应用程序数据的其他表空间不同。 无缓存是系统和撤消表空间性能的最佳选择。

*oraarch：性能视图中不需要存储池。


如果需要更多 IOPS，我们建议使用 LVM（逻辑卷管理器）或 MDADM 跨多个已装载磁盘创建一个大型的逻辑卷。 有关如何利用 LVM 或 MDADM 的准则和指针的详细信息，请参阅[适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 部署注意事项](dbms_guide_general.md)。 这种方法可以简化磁盘空间的管理开销，帮助避免跨已装载的多个磁盘手动分发文件。


#### <a name="write-accelerator"></a>写入加速器
对于 Azure M 系列 VM，使用 Azure 写入加速器时，与 Azure 高级存储性能相比，可通过多种因素减少写入联机重做日志的延迟。 基于 Azure 高级存储（用于联机重做日志文件）为磁盘 (VHD) 启用 Azure 写入加速器。 有关详细信息，请参阅[写入加速器](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)。


### <a name="backuprestore"></a>备份/还原
支持通过适用于 Oracle 的 SAP BR* 工具提供备份/还原功能，其方式与在裸机和 Hyper-V 上一样。 Oracle 恢复管理器 (RMAN) 也支持备份到磁盘以及从磁盘还原。

有关如何使用 Azure 备份和恢复服务进行备份和恢复 Oracle Database 的详细信息，请参阅[在 Azure Linux 虚拟机上备份和恢复 Oracle Database 12c 数据库](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-backup-recovery)。

### <a name="high-availability"></a>高可用性
支持通过 Oracle Data Guard 实现高可用性和灾难恢复。 若要在 Data Guard 中实现自动故障转移，需要使用快速启动故障转移 (FSFA)。 观察者功能 (FSFA) 触发故障转移。 如果不使用 FSFA，则只能使用手动故障转移配置。 有关详细信息，请参阅[在 Azure Linux 虚拟机上实施 Oracle Data Guard](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)。


有关在 Azure 环境下的 Oracle Database 数据库灾难恢复方面的信息，请参阅[在 Azure 环境下的 Oracle Database 12c 数据库灾难恢复](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-disaster-recovery)。

### <a name="accelerated-networking"></a>更快的网络连接
Oracle Linux 7 更新 5 (Oracle Linux 7.5) 提供对 Oracle Linux 中 Azure 加速网络的支持。 如果无法升级到最新的 Oracle Linux 7.5 版本，可能的解决方法是，使用 RedHat 兼容内核 (RHCK) 而不是 Oracle UEK 内核。 

根据 SAP 说明 [#1565179](https://launchpad.support.sap.com/#/notes/1565179)，支持在 Oracle Linux 内使用 RHEL 内核。 对于 Azure 加速网络，最小 RHCKL 内核版本必须是 3.10.0-862.13.1.el7。 如果在 Oracle Linux 中结合 [Azure 加速网络](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)使用 UEK 内核，需要使用 Oracle UEK 内核版本 5。

如果从不基于 Azure 市场的映像部署 VM，则需要运行以下代码，将其他配置文件复制到 VM： 
<pre><code># Copy settings from GitHub to the correct place in the VM
sudo curl -so /etc/udev/rules.d/68-azure-sriov-nm-unmanaged.rules https://raw.githubusercontent.com/LIS/lis-next/master/hv-rhel7.x/hv/tools/68-azure-sriov-nm-unmanaged.rules 
</code></pre>


### <a name="other"></a>其他
[适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 部署注意事项](dbms_guide_general.md)中介绍了使用 Oracle Database 的 VM 部署相关的其他重要概念，包括 Azure 可用性集和 SAP 监视。
