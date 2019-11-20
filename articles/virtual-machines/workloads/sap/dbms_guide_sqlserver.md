---
title: SAP iş yükü için Azure sanal makineler DBMS dağıtımı SQL Server | Microsoft Docs
description: SAP iş yükü için SQL Server Azure Sanal Makineler DBMS dağıtımı
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
ms.date: 09/26/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9d0c6841e29323ceab0758f4c4d6881abd24532d
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099968"
---
# <a name="sql-server-azure-virtual-machines-dbms-deployment-for-sap-netweaver"></a>SAP NetWeaver için Azure sanal makineler DBMS dağıtımı SQL Server

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[), veritabanı yansıtmasıyla aynı şekilde kullanılması gerekir. 965908]: https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
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
[1772688]: https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide_general.md 
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f 
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b 
[dbms-guide-5.6]:dbms_guide_sqlserver.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 
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
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/eresources/templates/sql-server-2014-alwayson-existing-vnet-and-ad/
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




Bu belge, Azure IaaS 'de SAP iş yükü için SQL Server dağıtımı yaparken göz önünde bulundurmanız gereken birçok farklı alanı içerir. Bu belgenin bir önkoşulu olarak, [SAP iş yükü Için Azure sanal MAKINELERI DBMS dağıtımı](dbms_guide_general.md) ve [Azure belgelerindeki SAP iş yükündeki](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)diğer kılavuzlar hakkında belge konularını okuduğunuzdan de okumalısınız. 



> [!IMPORTANT]
> Bu belgenin kapsamı, SQL Server Windows sürümüdür. SAP, SAP yazılımıyla SQL Server Linux sürümünü desteklememektedir. Belge, Microsoft Azure platformunun hizmet olarak platform olan Microsoft Azure SQL Veritabanı tartışımamaktadır. Bu yazıda tartışma, Azure sanal makinelerinde şirket içi dağıtımlar için bilinen, Azure 'un hizmet olarak altyapı özelliğinden yararlanarak SQL Server ürünün nasıl çalıştığı konusunda çalışmaktadır. Bu iki teklif arasındaki veritabanı özellikleri ve işlevleri farklıdır ve birbirleriyle karışık olmamalıdır. Ayrıca bkz:<https://azure.microsoft.com/services/sql-database/>
> 
>

Genel olarak, Azure IaaS 'de SAP iş yükünü çalıştırmak için en son SQL Server yayınları kullanmayı göz önünde bulundurmanız gerekir. En son SQL Server sürümleri, bazı Azure hizmetleri ve işlevlerine daha iyi tümleştirme sunar. Veya bir Azure IaaS altyapısında işlemleri en iyileştiren değişiklikler vardır.

Devam etmeden önce [Bu][virtual-machines-sql-server-infrastructure-services] belgenin gözden geçirilmesi önerilir.

Aşağıdaki bölümlerde, yukarıdaki bağlantının altındaki belgelerin bölümlerinin parçaları toplanır ve bahsedilir. SAP 'nin özelliklerinin yanı sıra bazı kavramların da daha ayrıntılı olarak açıklanmasıyla bahsedilir. Ancak, SQL Server özgü belgeleri okumadan önce yukarıdaki belgelerde çalışmanız kesinlikle önerilir.

Devam etmeden önce bilmeniz gereken IaaS özel bilgilerinde bazı SQL Server vardır:

* **SQL sürümü desteği**: SAP müşterileri için, Microsoft Azure sanal makinede SQL Server 2008 R2 ve üzeri desteklenir. Önceki sürümler desteklenmez. Daha fazla bilgi için bu genel [destek bildirimine](https://support.microsoft.com/kb/956893) bakın. Genellikle SQL Server 2008, Microsoft tarafından da desteklenir. Ancak, SQL Server 2008 R2 ile sunulan SAP 'nin önemli işlevleri nedeniyle, SAP için en düşük sürüm olan SQL Server 2008 R2. Genel olarak, Azure IaaS 'de SAP iş yükünü çalıştırmak için en son SQL Server yayınları kullanmayı göz önünde bulundurmanız gerekir. En son SQL Server sürümleri, bazı Azure hizmetleri ve işlevlerine daha iyi tümleştirme sunar. Veya bir Azure IaaS altyapısında işlemleri en iyileştiren değişiklikler vardır. Bu nedenle, kağıt 2016 SQL Server ve SQL Server 2017 ile kısıtlıdır.
* **SQL performansı**: Microsoft Azure barındırılan sanal makineler, diğer genel bulut sanallaştırma teklifleri ile karşılaştırıldığında iyi sonuç verir, ancak ayrı sonuçlar farklılık gösterebilir. [Azure sanal makinelerinde SQL Server için en iyi makale performansı uygulamalarına](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance)göz atın.
* **Azure Marketi 'Ndeki görüntüleri kullanma**: Yeni bir Microsoft Azure VM dağıtmanın en hızlı yolu, Azure Marketi 'nden bir görüntü kullanmaktır. Azure Marketi 'nde en son SQL Server sürümleri içeren görüntüler vardır. SQL Server zaten yüklü olduğu görüntüler SAP NetWeaver uygulamaları için hemen kullanılamaz. Bunun nedeni, varsayılan SQL Server harmanlama bu görüntülere yüklenir ve SAP NetWeaver sistemleri için gerekli harmanlama değildir. Bu tür görüntüleri kullanmak için, [Microsoft Azure Market bir SQL Server görüntüsünü kullanarak][dbms-guide-5.6]bölümde belgelenen adımları denetleyin. 


## <a name="recommendations-on-vmvhd-structure-for-sap-related-sql-server-deployments"></a>SAP ile ilgili SQL Server dağıtımlar için VM/VHD yapısına yönelik öneriler
Genel açıklamaya uygun olarak, SQL Server yürütülebilir dosyaları, VM 'nin IŞLETIM sistemi diskinin sistem sürücüsüne yerleştirilmelidir veya yüklü olmalıdır (sürücü C:\).  Genellikle, SQL Server sistem veritabanlarının çoğu SAP NetWeaver iş yükünde yüksek düzeyde kullanılmaz. Sonuç olarak, SQL Server (Master, msdb ve model) sistem veritabanları C:\ dizininde kalabilir. aynı zamanda. SAP iş yükleri söz konusu olduğunda bir özel durum tempdb olmalıdır ve daha yüksek veri hacmi veya g/ç işlemleri birimi gerektirebilir. İşletim sistemi VHD 'sine uygulanmamalıdır, g/ç iş yükü. Bu tür sistemler için aşağıdaki adımlar gerçekleştirilmelidir:


* Tüm SAP sertifikalı VM türleriyle (bkz. SAP Note [1928533]), A serisi VM 'ler, tempdb verileri ve günlük dosyaları, kalıcı olmayan d:\ üzerine yerleştirilebilecek sürücü. 
* Bununla birlikte, birden çok tempdb veri dosyası kullanılması önerilir. Farkında olun D:\ sürücü birimleri VM türüne göre farklılık açmış. D:\ için tam boyutlar için farklı VM 'lerden oluşan sürücü, [Azure 'Da Windows sanal makinelerinin makale boyutlarını](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)kontrol edin.

Bu yapılandırma tempdb 'nin sistem sürücüsünün sağlayabileceğinden daha fazla alan kullanmasını sağlar. Kalıcı olmayan D:\ sürücü Ayrıca daha iyi g/ç gecikme süresi ve aktarım hızı (A serisi VM 'Ler hariç) sağlar. Uygun tempdb boyutunu belirleyebilmek için, mevcut sistemlerdeki tempdb boyutlarını kontrol edebilirsiniz. 

>[!NOTE]
> tempdb veri dosyalarını ve günlük dosyasını D:\ ' daki bir klasöre yerleştirmeniz durumunda oluşturduğunuz sürücü, bir VM yeniden başlatıldıktan sonra klasörün mevcut olduğundan emin olmanız gerekir. D:\ ' dan beri Sürücü, bir VM yeniden başlatıldıktan sonra, tüm dosya ve dizin yapıları temizlenmeden sonra yeniden başlatılır. D:\ üzerinde son dizin yapılarını yeniden oluşturma olanağı SQL Server hizmetinin başlangıcından önceki sürücü, [Bu makalede](https://www.sqlserver.co.uk/index.php/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/)açıklanmamıştır.

Bir SAP veritabanıyla SQL Server çalıştıran ve tempdb verilerinin ve tempdb günlük dosyasının D:\ ' A yerleştirildiği bir VM yapılandırması. sürücü şöyle görünür:

![SQL Server için basit VM disk yapılandırması diyagramı](./media/dbms_sqlserver_deployment_guide/Simple_disk_structure.PNG)

Yukarıdaki diyagramda basit bir durum görüntülenir. SAP iş yükü, numarası ve Premium Depolama disklerinin boyutu [Için Azure sanal MAKINELER DBMS dağıtımı](dbms_guide_general.md), farklı faktörlerden bağımlıdır. Ancak genel olarak şunları öneririz:

- SQL Server veri dosyalarını içeren bir veya az sayıda birim oluşturmak için depolama alanları kullanma. Bu yapılandırmanın arkasında, farklı g/ç iş yüküyle farklı boyutlardaki veritabanı dosyaları olan çok sayıda SAP veritabanı olduğu için gerçek hayatta yer vardır.
- Depolama alanlarını kullanarak yeterli ıOPS ve SQL Server işlem günlük dosyası sağlayın. Potansiyel ıOPS iş yükü genellikle işlem günlüğü biriminin boyutlandırılmasına yönelik temel satırtır ve SQL Server işlem biriminin potansiyel hacmi değildir
- Performans yeterince iyi olduğu sürece tempdb için D:\Drive kullanın. Genel iş yükü, D:\ ' da bulunan tmepdb tarafından performansı sınırlandırıldığında [Bu makalede](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance)önerilen şekilde tempdb 'Yi ayrı Premium Depolama disklerine taşımak için göz önünde bulundurmanız gereken sürücü.


### <a name="special-for-m-series-vms"></a>D serisi VM 'Ler için özel
Azure M serisi VM için, işlem günlüğüne yazma gecikmesi, Azure Yazma Hızlandırıcısı kullanılırken Azure Premium depolama performansına kıyasla faktörlerle azaltılabilir. Bu nedenle, SQL Server işlem günlüğü için birimi oluşturan VHD 'ler (ler) için Azure Yazma Hızlandırıcısı dağıtmanız gerekir. Ayrıntılar belge [yazma Hızlandırıcısı](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)okunabilir.
  

### <a name="formatting-the-disks"></a>Diskleri biçimlendirme
SQL Server için, SQL Server veri ve günlük dosyaları içeren disklerin NTFS blok boyutu 64 KB olmalıdır. D:\ biçimini biçimlendirmeye gerek yoktur. sürücü. Bu sürücü önceden biçimlendirilen şekilde gelir.

Veritabanlarının geri yüklenmesi veya oluşturulması, dosyaların içeriğini sıfırlama yoluyla veri dosyalarını başlatmadığından emin olmak için, SQL Server hizmetinin üzerinde çalıştığı Kullanıcı bağlamının belirli bir izne sahip olduğundan emin olun. Genellikle Windows Yönetici grubundaki kullanıcılar bu izinlere sahiptir. SQL Server hizmeti Windows olmayan yönetici kullanıcısının kullanıcı bağlamında çalışıyorsa, kullanıcıya **toplu bakım görevlerini gerçekleştirmesi**için bu kullanıcıya atamanız gerekir.  Bu Microsoft Bilgi Bankası makalesindeki ayrıntılara bakın:<https://support.microsoft.com/kb/2574695>

### <a name="impact-of-database-compression"></a>Veritabanı sıkıştırmasının etkisi
G/ç bant genişliğinin sınırlama faktörü olabileceği yapılandırmalarda, her ölçü, ıOPS 'yi azaltan iş yükünün Azure gibi bir IaaS senaryosunda çalıştırılabileceği şekilde uzamasına yardımcı olabilir. Bu nedenle, henüz yapılmazsa, mevcut bir SAP veritabanını Azure 'a yüklemeden önce hem SAP hem de Microsoft tarafından SQL Server sayfa sıkıştırması uygulanması önerilir.

Azure 'a yüklemeden önce veritabanı sıkıştırması gerçekleştirme önerisi iki nedenden dolayı verilmiştir:

* Karşıya yüklenecek veri miktarı düşüktür.
* Sıkıştırma yürütmesinin süresi, bir birinin daha fazla CPU veya daha yüksek g/ç bant genişliği veya şirket içi g/ç gecikme süresiyle daha güçlü donanımlar kullanabilmesi için daha kısadır.
* Daha küçük veritabanı boyutları disk ayırma için daha az maliyete yol açabilir

Veritabanı sıkıştırması, şirket içinde olduğu gibi bir Azure sanal makinelerinde da çalışmaktadır. Mevcut SAP NetWeaver SQL Server veritabanlarının nasıl sıkıştırılması hakkında daha fazla bilgi için [GELIŞMIŞ SAP sıkıştırma aracı MSSCOMPRESS](https://blogs.msdn.microsoft.com/saponsqlserver/2016/11/25/improved-sap-compression-tool-msscompress/)makalesine bakın. 

## <a name="sql-server-2014-and-more-recent---storing-database-files-directly-on-azure-blob-storage"></a>SQL Server 2014 ve daha yeni-veritabanı dosyalarını doğrudan Azure Blob depolama üzerinde depolama
SQL Server 2014 ve üzeri sürümler, veritabanı dosyalarını, çevresindeki bir VHD 'nin ' sarmalayıcı ' öğesini olmadan doğrudan Azure Blob Mağazası üzerinde depolama olasılığını açın. Özellikle standart Azure depolama veya daha küçük VM türlerini kullanarak bu tür bir dağıtım, bazı küçük sanal makine türlerine bağlanabilir sınırlı sayıda disk tarafından zorlanan ıOPS sınırlarını aşabilecebileceğiniz senaryolara olanak sağlar. Bu dağıtım yöntemi, kullanıcı veritabanları için SQL Server, ancak sistem veritabanları için de kullanılır. Ayrıca, SQL Server veri ve günlük dosyaları için de kullanılabilir. Bu şekilde bir SAP SQL Server veritabanı dağıtmak istiyorsanız, VHD 'lere ' sarmalama ' yerine bu şekilde dağıtım yapmak istiyorsanız şunları göz önünde bulundurun:

* Kullanılan depolama hesabının, SQL Server VM 'yi dağıtmak için kullanılan aynı Azure bölgesinde olması gerekir.
* VHD 'lerin farklı Azure depolama hesapları üzerinden dağıtılması hakkında daha önce listelenen konular bu dağıtım yöntemi için de geçerlidir. G/ç işlemlerinin, Azure depolama hesabı sınırlarına göre sayısı anlamına gelir.
* VM 'nin depolama g/ç kotasına karşı, SQL Server veri ve günlük dosyalarını temsil eden depolama bloblarına karşı trafik, sanal makinenin belirli VM türünün ağ bant genişliğine göre hesaba katılmaz. Belirli bir sanal makine türünün ağ ve depolama bant genişliği için, [Azure 'Da Windows sanal makinelerinin makale boyutlarına](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)başvurun.
* Dosya g/ç 'yi ağ kotasından dağıtmaya bir sonuç olarak, depolama kotasını büyük ölçüde ve ile yalnızca kısmen VM 'nin Genel bant genişliğini kullanacak şekilde kullanırsınız.
* IOP ve g/ç verimlilik performansı, Azure Premium Depolama 'nın farklı disk boyutları için sahip olduğu hedeflerse artık uygulanmaz. Oluşturduğunuz Bloblar Azure Premium depolamada bulunuyor olsa da. Hedefler, [yüksek performanslı Premium Depolama ve VM 'ler için yönetilen diskler](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)makalesine belgelenmiştir. SQL Server veri dosyalarını ve günlük dosyalarını doğrudan Azure Premium depolamada depolanan bloblara yerleştirmekten kaynaklanan performans özellikleri, Azure Premium depolamada bulunan VHD 'lere kıyasla farklı olabilir.
* Azure Premium depolama diskleri için kullanılabilir olarak ana bilgisayar tabanlı önbelleğe alma, SQL Server veri dosyalarını doğrudan Azure bloblarına yerleştirirken kullanılamaz.
* M serisi VM 'lerde, Azure Yazma Hızlandırıcısı, SQL Server işlem günlüğü dosyasına karşı alt milisaniyelik yazmaları desteklemek için kullanılamaz. 

Bu işlevselliğin ayrıntıları [Microsoft Azure içindeki veri dosyaları SQL Server](https://docs.microsoft.com/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure?view=sql-server-2017) makalesinde bulunabilir

Üretim sistemleri için öneri, bu yapılandırmayı önlemek ve Azure 'da doğrudan Azure Blob 'ları yerine Azure Premium Depolama VHD 'lerde SQL Server veri ve günlük dosyalarını seçmenizi sağlar.


## <a name="sql-server-2014-buffer-pool-extension"></a>SQL Server 2014 arabellek havuzu uzantısı
SQL Server 2014, [arabellek havuzu uzantısı](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension?view=sql-server-2017)olarak adlandırılan yeni bir özellik sunmuştur. Bu işlevsellik, bir sunucunun veya VM 'nin yerel SSD 'leri tarafından desteklenen ikinci düzey bir önbellek ile bellekte tutulan SQL Server arabellek havuzunu genişletir. Arabellek havuzu uzantısı, daha büyük bir çalışma veri kümesinin bellek içinde tutulmasını sağlar. Azure Standart depolama 'ya erişme ile karşılaştırıldığında, bir Azure VM 'nin yerel SSD 'leri üzerinde depolanan arabellek havuzunun uzantısına erişim çok daha hızlıdır. Arabellek havuzu uzantısı, SQL Server veri dosyaları için önerildiği şekilde Azure Premium Depolama okuma önbelleği ile karşılaştırılıyor, arabellek havuzu uzantıları için önemli bir avantaj beklenmez. Bunun nedeni hem önbellekler (SQL Server arabellek havuzu uzantısı ve Premium Depolama okuma önbelleği) Azure işlem düğümünün yerel disklerini kullanmaktır.

SAP iş yükü karma olan SQL Server arabellek havuzu uzantısı ile aynı zamanda kazanılan deneyimler, her durumda bunu kullanıp kullanmayacağınızı açık önerilere izin vermez. İdeal durum, SAP uygulamasının ana belleğe sığması için gereken çalışma kümesidir. 4 TB 'a kadar bellek içeren VM 'Leri sunarken Azure ile birlikte, çalışma kümesinin bellekte tutulması için ulaşılabilir olması gerekir. Bu nedenle, arabellek havuzu uzantısının kullanımı nadir bazı durumlar ile sınırlıdır ve temel bir durum olmaması gerekir.  

## <a name="backuprecovery-considerations-for-sql-server"></a>SQL Server için yedekleme/kurtarma konuları
SQL Server Azure 'a dağıttığınızda, yedekleme yönteminizin gözden geçirilmesi gerekir. Sistem bir üretim sistemi olmasa bile, SQL Server tarafından barındırılan SAP veritabanının düzenli olarak yedeklenmesi gerekir. Azure depolama üç görüntü yaptığından, bir depolama kilitlenmesiyle ilgili olarak yedekleme artık daha az önemlidir. Uygun bir yedekleme ve kurtarma planının sürdürülmesi için öncelik nedeni, zaman kurtarma özelliklerini bir noktaya sağlayarak mantıksal/el ile hatalar için telafi sağlayabilmenin daha fazla olması olabilir. Böylece hedef, veritabanını belirli bir noktaya geri yüklemek ya da mevcut veritabanını kopyalayarak başka bir sistemi temel almak için Azure 'daki yedeklemeleri kullanmak üzere yedeklemeleri kullanmaktır. 

Azure 'daki farklı SQL Server yedekleme olasılıklarına bakmak için [Azure sanal makinelerinde SQL Server Için Yedekleme ve geri yükleme](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery)makalesini okuyun. Makale birçok farklı olasılıkların ele alınmaktadır.

### <a name="manual-backups"></a>El ile yedeklemeler
' El ile ' yedeklemeler gerçekleştirmek için kullanabileceğiniz çeşitli olanaklar vardır:

1. Doğrudan bağlı Azure disklerinde geleneksel SQL Server yedeklemeleri gerçekleştirme. Bu yöntem, yedeklemelerin sistem yenilemelerinde kullanılabilir olması ve mevcut SAP sistemlerinin kopyaları olarak yeni sistemlerin oluşturulması avantajına sahiptir.
2.  SQL Server 2012 CU4 ve üzeri, veritabanlarını bir Azure Storage URL 'sine yedekleyebilir.
3.  Azure Blob depolamada veritabanı dosyaları için dosya anlık görüntü yedeklemeleri. Bu yöntem yalnızca SQL Server verileriniz ve günlük dosyalarınız Azure Blob depolama alanında bulunuyorsa işe yarar.

İlk yöntem, şirket içi dünyada pek çok durumda iyi bilinirler ve uygulanır. Bununla birlikte, daha uzun vadeli yedekleme konumunu çözümlemek için görev sizi bırakır. Yerel olarak bağlı Azure depolamada yedeklemelerinizi 30 veya daha fazla güne karşı korumak istemediğiniz için, yedeklemeleriniz için erişim ve bekletme yönetimi içeren Azure Backup hizmetlerini veya başka bir üçüncü taraf yedekleme/kurtarma aracını kullanmanız gerekir. Ya da Windows depolama alanları kullanarak Azure 'da büyük bir dosya sunucusu oluşturabilirsiniz.

İkinci yöntem, [URL 'ye yedekleme SQL Server](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-2017)makalesinde daha yakından açıklanmıştır. Farklı SQL Server sürümleri bu işlevlerde bazı çeşitlere sahiptir. Bu nedenle, belirli SQL Server sürüm denetimi için belgelere göz atın. Bu makalede çok sayıda kısıtlama listelendiğine dikkat edin. Yedeklemeyi şu şekilde gerçekleştirebilirsiniz:

- Yedekleme boyutunu 1000 GB ile sınırlayan tek bir Azure Sayfa Blobu. Bu ayrıca elde ettiğiniz aktarım hızını da sınırlandırır.
- Birden çok (64 ' e kadar) Azure blok blob 'ları, teorik olarak 12 TB 'lık yedekleme boyutunu etkinleştirir. Ancak, müşteri veritabanları ile testler maksimum yedekleme boyutunun teorik limitinden daha küçük olduğunu gösterdi. Bu durumda, yedeklemelerin bekletilmesini yönetmekten ve yedeklemeler de erişebilmekten siz sorumlusunuz.


### <a name="automated-backup-for-sql-server"></a>SQL Server için Otomatik Yedekleme
Otomatik yedekleme, Azure 'da Windows VM 'de çalışan SQL Server Standard ve Enterprise sürümleri için otomatik yedekleme hizmeti sağlar. Bu hizmet, Azure portal SQL Server Windows sanal makine görüntülerine otomatik olarak yüklenen [SQL Server IaaS Aracısı uzantısı](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension)tarafından sağlanır. SQL Server yüklü olan işletim sistemi görüntülerini dağıtırsanız, VM uzantılarını ayrı olarak yüklemeniz gerekir. Gerekli adımlar bu [makalede](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension)belgelenmiştir.

Bu yöntemin özellikleri hakkında daha fazla ayrıntı bu makalelerde bulunabilir:

- SQL Server 2014: [SQL Server 2014 sanal makineleri için otomatik yedekleme (Kaynak Yöneticisi)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup)
- SQL Server 2016/2017: [Azure sanal makineleri için otomatik yedekleme v2 (Kaynak Yöneticisi)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup-v2)

Belgelere bakarak, daha güncel SQL Server yayınları ile işlevselliğin iyileştirilen olduğunu görebilirsiniz. SQL Server otomatikleştirilmiş yedeklemelerle ilgili bazı ayrıntılar, [SQL Server yönetilen yedekleme Microsoft Azure](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure?view=sql-server-2017)makalesinde yayımlanmıştır. Teorik yedek boyut sınırı 12 TB 'tır.  Otomatik yedeklemeler, 12 TB 'a kadar yedekleme boyutları için iyi bir yöntem olabilir. Birden çok blob paralel olarak yazıldığından, 100 MB/sn 'den büyük bir verimlilik bekleyebilir. 
 

### <a name="azure-backup-for-sql-server-vms"></a>SQL Server VM 'Ler için Azure Backup
Bu yeni SQL Server yedeklemeleri yöntemi, Azure Backup Hizmetleri tarafından genel önizleme olarak Haziran 2018 ' den itibaren sunulmaktadır. SQL Server yedekleme yöntemi diğer üçüncü taraf araçları ile aynıdır; Yani, yedeklemeleri hedef konuma akışa almak için SQL Server VSS/VDı arabirimidir. Bu durumda, hedef konum Azure kurtarma hizmeti Kasası olur.

Bu yedekleme yönteminin, merkezi yedekleme yapılandırmalarının, izlemenin ve yönetimin çok sayıda avantajını ekleyen ayrıntılı açıklaması [burada](https://docs.microsoft.com/azure/backup/backup-azure-sql-database)bulunabilir. 


### <a name="third-party-backup-solutions"></a>Üçüncü taraf yedekleme çözümleri
Çok sayıda SAP müşterisi için, Azure 'da çalışan SAP yatay 'in bir bölümü için baştan başlamak ve tam yeni yedekleme çözümleri tanıtmak mümkün değildir. Sonuç olarak, mevcut yedekleme çözümlerinin kullanılması ve Azure 'da genişletilmesi gerekir. Mevcut yedekleme çözümlerini Azure 'a genişletmek, genellikle bu alandaki ana satıcıların büyük bir çoğunluğu ile sorunsuz bir şekilde çalışmıştır. 


## <a name="1b353e38-21b3-4310-aeb6-a77e7c8e81c8"></a>Microsoft Azure Market SQL Server bir görüntü kullanma
Microsoft, Azure Marketi 'nde zaten SQL Server sürümlerini içeren VM 'Leri sunmaktadır. SQL Server ve Windows için lisans gerektiren SAP müşterileri için, bu görüntüleri kullanmak, SQL Server zaten yüklü olan VM 'Leri çağırarak lisans gereksinimini kapsayan bir fırsat olabilir. Bu tür görüntüleri SAP için kullanabilmeniz için aşağıdaki noktalara dikkat edilmesi gerekir:

* Değerlendirme dışı sürümler SQL Server, Azure Marketi 'nden dağıtılan ' yalnızca Windows-yalnızca ' VM 'den daha yüksek maliyetler elde edin. Fiyatları karşılaştırmak için şu makalelere bakın: <https://azure.microsoft.com/pricing/details/virtual-machines/windows/> ve <https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/>. 
* Yalnızca SAP tarafından desteklenen SQL Server yayınları kullanabilirsiniz.
* Azure Marketi 'nde sunulan VM 'lerde yüklü olan SQL Server örneğinin harmanlaması, SAP NetWeaver 'in SQL Server örneğinin çalıştırılmasını gerektirir. Harmanlamayı, aşağıdaki bölümdeki yönleri de değiştirebilirsiniz.

### <a name="changing-the-sql-server-collation-of-a-microsoft-windowssql-server-vm"></a>Bir Microsoft Windows/SQL Server VM SQL Server harmanlamasını değiştirme
Azure Marketi 'ndeki SQL Server görüntüleri, SAP NetWeaver uygulamaları için gerekli olan harmanlamayı kullanacak şekilde kurulmadığından, dağıtımdan hemen sonra değiştirilmesi gerekir. SQL Server için, bu harmanlama değişikliği, VM dağıtıldığı anda aşağıdaki adımlarla yapılabilir ve bir yönetici dağıtılan VM 'de oturum açabiliyor:

* Yönetici olarak bir Windows komut penceresi açın.
* Dizini C:\Program Files\Microsoft SQL Server\110\Setup Bootstrap\sqlserver2012olarak değiştirin.
* Komutu yürütün: Setup. exe/quiet/Action = RebuildDatabase/InstanceName = MSSQLSERVER/sqlsysadminaccounts =`<local_admin_account_name`>/sqlharmanlama = SQL_Latin1_General_Cp850_BIN2   
  * `<local_admin_account_name`>, sanal makine Galeri aracılığıyla ilk kez dağıtıldığında yönetici hesabı olarak tanımlanan hesaptır.

İşlem yalnızca birkaç dakika sürer. Adımın doğru sonuçla sonlandırıp sonlanmadığını doğrulamak için aşağıdaki adımları gerçekleştirin:

* SQL Server Management Studio'yu açın.
* Bir sorgu penceresi açın.
* SQL Server ana veritabanında sp_helpsort komutunu yürütün.

İstenen sonuç şöyle görünmelidir:

    Latin1-General, binary code point comparison sort for Unicode Data, SQL Server Sort Order 40 on Code Page 850 for non-Unicode Data

Sonuç farklıysa SAP dağıtımı durdurun ve kurulum komutunun neden beklendiği gibi çalışmadığına ilişkin araştırma yapın. Yukarıda bahsedilen farklı SQL Server codepages ile SQL Server örneğine SAP NetWeaver uygulamalarının dağıtılması desteklenmez.

## <a name="sql-server-high-availability-for-sap-in-azure"></a>Azure 'da SAP için yüksek kullanılabilirlik SQL Server
SAP için Azure IaaS dağıtımlarında SQL Server kullanarak, DBMS katmanını yüksek oranda kullanılabilir şekilde dağıtmak için çeşitli farklı olasılıklara sahip olursunuz. [SAP iş yükü Için Azure sanal MAKINELER DBMS dağıtımına Ilişkin önemli noktalara değinildiği](dbms_guide_general.md) gibi, Azure tek bir VM ve bir Azure kullanılabilirlik kümesinde dağıtılan bir çift sanal makine için farklı bir zaman SLA 'lar sağlar. Varsayım, Azure kullanılabilirlik kümelerinde dağıtımı gerektiren üretim dağıtımlarınız için güncel SLA 'yı doğru şekilde sağlamayız. Böyle bir durumda, bu tür bir kullanılabilirlik kümesinde en az iki VM dağıtmanız gerekir. Bir VM, etkin SQL Server örneğini çalıştırır. Diğer VM pasif örneği çalıştıracak

### <a name="sql-server-clustering-using-windows-scale-out-file-server"></a>Windows genişleme dosya sunucusu kullanarak kümeleme SQL Server
Windows Server 2016 ile, Microsoft [depolama alanları doğrudan](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)sunmuştur. Depolama Alanları Doğrudan dağıtımına göre SQL Server FCı Kümelemesi desteklenir. Ayrıntılar, [Azure sanal makinelerinde SQL Server yük devretme kümesi örneğini yapılandırma](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-create-failover-cluster)makalesinde bulunabilir. Çözüm, küme kaynaklarının sanal IP adresiyle başa çıkmak için bir Azure Yük dengeleyiciyi de gerektirir. SQL Server veritabanı dosyaları depolama alanlarında depolanır. Bu nedenle, Azure Premium Depolama 'yı temel alan Windows depolama alanlarını oluşturmak için gerekli bir seçenektir. Bu çözüm çok uzun olmadığı için desteklendiğinden SAP üretim senaryolarında bu çözümü kullanan bilinen bir SAP müşterisi yoktur.  

### <a name="sql-server-log-shipping"></a>SQL Server günlük aktarımı
Yüksek kullanılabilirlik yöntemlerinden biri (HA), günlük aktarma SQL Server. HA yapılandırmasına katılan VM 'Lerin çalışma adı çözümlemesi varsa, sorun yoktur ve Azure 'daki kurulum, şirket içinde gerçekleştirilen kurulumdan farklı değildir. Günlük dağıtımını ve günlük aktarma etrafında ilkeleri ayarlamaya göre. SQL Server günlük aktarma ayrıntıları, [günlük aktarma (SQL Server)](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server?view=sql-server-2017)makalesinde bulunabilir.

Azure 'da SQL Server günlük aktarma işlevselliği, tek bir Azure bölgesinde yüksek kullanılabilirlik elde etmek için kullanılır. Bununla birlikte, aşağıdaki senaryolarda SAP müşterileri Azure ile birlikte günlük dağıtımını başarıyla kullanıyor:

- Bir Azure bölgesindeki başka bir Azure bölgesine olağanüstü durum kurtarma senaryoları
- Şirket içinden bir Azure bölgesine olağanüstü durum kurtarma yapılandırması
- Şirket içinden Azure 'a kadar kesilen senaryolar. Bu durumlarda, Azure 'daki yeni DBMS dağıtımını şirket içi üretim sistemiyle eşleştirmek için günlük aktarma kullanılır. Kesme sırasında üretim kapatılır ve son ve en son işlem günlüğü yedeklemelerinin Azure DBMS dağıtımına aktarıldığından emin olun. Ardından, Azure DBMS dağıtımı üretim için açılır.  



### <a name="database-mirroring"></a>Veritabanı yansıtma
SAP tarafından desteklenen veritabanı yansıtma (bkz. SAP Note [), veritabanı yansıtmasıyla aynı şekilde kullanılması gerekir. 965908]) SAP bağlantı dizesinde yük devretme ortağı tanımlamayı kullanır. Şirketler arası durumlarda iki VM 'nin aynı etki alanında olduğunu ve iki SQL Server örneğinin Kullanıcı bağlamının bir etki alanı kullanıcısı altında çalıştığını ve söz konusu iki SQL Server örneğinde yeterli ayrıcalıklara sahip olduğunu varsaytık. Bu nedenle, Azure 'da veritabanı yansıtma kurulumu, tipik bir şirket içi kurulum/yapılandırma arasında farklılık gösterir.

Yalnızca bulutta gerçekleştirilen dağıtımlarda, en kolay yöntem, Azure 'daki başka bir etki alanı kurulumunun, bu DBMS VM 'Leri (ve ideal SAP VM 'Leri) bir etki alanı içinde olmasını sağlar.

Bir etki alanı mümkün değilse, bir tane, burada açıklandığı gibi veritabanı yansıtma uç noktaları için sertifikalar da kullanabilir:<https://docs.microsoft.com/sql/database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql>

Azure 'da veritabanı yansıtmayı ayarlamaya yönelik bir öğretici şurada bulunabilir:<https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server> 

### <a name="sql-server-always-on"></a>SQL Server her zaman açık
Her zaman açık SAP şirket içi için desteklenir (bkz. SAP Note [1772688]), Azure 'da SAP ile birlikte desteklenir. Bu noktada Azure, şirket içinde mümkün olduğu için AD/DNS nesnesi oluşturulmasına izin vermediğinden, SQL Server kullanılabilirlik grubu dinleyicisinin (Azure kullanılabilirlik kümesiyle karıştırılmamalıdır) dağıtılmasıyla ilgili bazı özel noktalar vardır. Bu nedenle, belirli bir Azure davranışını aşmak için bazı farklı yükleme adımları gereklidir.

Kullanılabilirlik grubu dinleyicisi kullanan bazı noktalar şunlardır:

* Kullanılabilirlik grubu dinleyicisinin kullanılması yalnızca, sanal makinenin Konuk işletim sistemi olarak Windows Server 2012 veya üzeri olabilir. Windows Server 2012 için, bu düzeltme ekinin uygulandığından emin olmanız gerekir:<https://support.microsoft.com/kb/2854082> 
* Windows Server 2008 R2 için, bu düzeltme eki mevcut değildir ve her zaman açık, bağlantı dizesinde bir yük devretme ortağı belirtilerek (SAP default. PFL parametresi DBS/,/sunucu-bkz. SAP notuna [), veritabanı yansıtmasıyla aynı şekilde kullanılması gerekir. 965908]).
* Bir kullanılabilirlik grubu dinleyicisi kullanılırken, veritabanı VM 'lerinin ayrılmış bir Load Balancer bağlı olması gerekir. Azure 'un her iki VM arada kapatıldığı durumlarda yeni IP adresleri atamasını önlemek için, biri her zaman açık yapılandırmada bu sanal makinelerin ağ arabirimlerine statik IP adresleri atamalıdır (statik IP adresi tanımlama konusunda açıklanmaktadır [ Bu][virtual-networks-reserved-private-ip] Makale)
* Azure 'un geçerli işlevselliği ile aynı IP adresini kümenin oluşturulduğu düğüm ile aynı IP adresine atayacağından, kümenin özel bir IP adresi olması gereken bir WSFC küme yapılandırması oluşturulurken gerekli olan özel adımlar vardır. Bu, kümeye farklı bir IP adresi atamak için el ile yapılan bir adımın gerçekleştirilmesi gerektiği anlamına gelir.
* Kullanılabilirlik grubu dinleyicisi, Azure 'da kullanılabilirlik grubunun birincil ve ikincil çoğaltmalarını çalıştıran VM 'lere atanan TCP/IP uç noktalarıyla oluşturulacaktır.
* Bu uç noktaların ACL 'lerle güvenli hale getirilmesine gerek duyabilirsiniz.

Azure VM 'lerde aşağıdakiler gibi SQL Server her zaman açık olarak dağıtma hakkında ayrıntılı belgeler:

- [Azure sanal makinelerinde SQL Server Always on kullanılabilirlik grupları Ile tanışın](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview).
- [Azure sanal makinelerinde farklı bölgelerde her zaman açık kullanılabilirlik grubu yapılandırın](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-dr).
- [Azure 'Da Always on kullanılabilirlik grubu için bir yük dengeleyici yapılandırın](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener).

>[!NOTE]
> Kullanılabilirlik grubu dinleyicisinin sanal IP adresi için Azure Yük dengeleyiciyi yapılandırıyorsanız, DirectServerReturn öğesinin yapılandırıldığından emin olun. Bu seçeneğin yapılandırılması, SAP uygulama katmanı ve DBMS katmanı arasındaki ağ gidiş dönüş gecikmesini azaltır. 

SQL Server her zaman açık, SAP iş yükü dağıtımları için Azure 'da kullanılan en yaygın kullanılan yüksek kullanılabilirlik ve olağanüstü durum kurtarma işlevidir. Çoğu müşteri tek bir Azure bölgesinde yüksek kullanılabilirlik için her zaman açık kullanır. Dağıtım yalnızca iki düğüm ile kısıtlanmışsa bağlantı için iki seçeneğiniz vardır:

- Kullanılabilirlik grubu dinleyicisini kullanma. Kullanılabilirlik grubu dinleyicisiyle bir Azure yük dengeleyici dağıtmanız gerekir. Bu, genellikle varsayılan dağıtım yöntemidir. SAP uygulamaları, tek bir düğüme karşı değil, kullanılabilirlik grubu dinleyicisine göre bağlanacak şekilde yapılandırılır
- SQL Server veritabanı yansıtmasının bağlantı parametrelerini kullanma. Bu durumda, SAP uygulamalarının bağlantısını her iki düğüm adının de adlandırıldığı bir şekilde yapılandırmanız gerekir. Bu tür bir SAP tarafı yapılandırmasının tam ayrıntıları SAP Note [#965908](https://launchpad.support.sap.com/#/notes/965908)bölümünde belgelenmiştir. Bu seçeneği kullanarak, bir kullanılabilirlik grubu dinleyicisi yapılandırmanız gerekmez. Ve SQL Server yüksek kullanılabilirlik için Azure yük dengeleyici olmadan. Sonuç olarak, SQL Server örneğine gelen trafik Azure yük dengeleyici üzerinden yönlendirilmediğinden SAP uygulama katmanı ve DBMS katmanı arasındaki ağ gecikmesi daha düşüktür. Ancak, bu seçenek yalnızca kullanılabilirlik grubunuzu iki örneği kapsayacak şekilde kısıtladığınızda geçerlidir. 

Çok az sayıda müşteri, Azure bölgeleri arasında ek olağanüstü durum kurtarma işlevselliği için SQL Server her zaman açık işlevsellikten yararlanılarak. Birçok müşteri aynı zamanda ikincil bir çoğaltmadan yedeklemeler gerçekleştirme olanağını da kullanır. 

## <a name="sql-server-transparent-data-encryption"></a>SQL Server Saydam Veri Şifrelemesi
SAP SQL Server veritabanlarını Azure 'da dağıtmada SQL Server [Saydam veri şifrelemesi (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) kullanan birkaç müşteri vardır. SQL Server TDE işlevselliği SAP tarafından tam olarak desteklenir (bkz. SAP Note [#1380493](https://launchpad.support.sap.com/#/notes/1380493)). 

### <a name="applying-sql-server-tde"></a>SQL Server TDE uygulanıyor
Şirket içinde çalışan başka bir DBMS 'den, Azure 'da çalışan Windows/SQL Server 'e heterojen bir geçiş gerçekleştirdiğiniz durumlarda, boş hedef veritabanınızı önceden SQL Server oluşturmalısınız. Sonraki adımda SQL Server TDE işlevselliği uygularsınız. Şirket içi üretim sisteminizi hala çalıştırıyor olursunuz. Bu sırada gerçekleştirmek istediğiniz neden, boş veritabanını şifreleme işleminin biraz zaman alabilir. Ardından, SAP içeri aktarma işlemi, kapalı kalma süresi boyunca verileri şifrelenmiş veritabanına aktarır. Şifrelenmiş bir veritabanına içeri aktarma işlemi, sonraki zaman aşamasında dışarı aktarma aşamasından sonra veritabanını şifrelemeden daha düşük bir zamana sahiptir. Veritabanı üzerinde çalışan SAP iş yüküne sahip TDE ile TDE uygulama girişimi sırasında yapılan negatif deneyimler. Bu nedenle, öneri, belirli bir veritabanında SAP iş yükü olmadan gerçekleştirilmesi gereken TDE etkinliğinin dağıtımını sağlar.

SAP SQL Server veritabanlarını Şirket içinden Azure 'a taşıdığınız durumlarda, şifrelemeyi en hızlı şekilde uygulamış olduğunuz altyapıyı test etmenizi öneririz. Bunun için bu olguları aklınızda bulundurun:

- Veritabanına veri şifrelemesi uygulamak için kaç iş parçacığı kullanıldığını tanımlayamazsınız. İş parçacığı sayısı, SQL Server veri ve günlük dosyalarının dağıtıldığı disk birimi sayısına bağlıdır. Daha farklı birimler (sürücü harfleri) anlamına gelir, daha fazla iş parçacığı şifrelemeyi gerçekleştirmek için paralel olarak kullanılacaktır. Bu tür bir yapılandırma, Azure VM 'lerinde SQL Server veritabanı dosyaları için bir veya daha az sayıda depolama alanı oluşturmaya yönelik daha önceki disk yapılandırma önerimiyle bir bit ile çelişmektedir. Az sayıda birime sahip bir yapılandırma, şifrelemeyi yürüten az sayıda iş parçacığına yol açabilir. Tek bir iş parçacığı şifrelemesi, 64 KB 'LıK kapsamları okuyor, şifreler ve ardından işlem günlüğü dosyasına bir kayıt yazar ve bu da uzantının şifrelendiğini bildiriyor. Sonuç olarak, işlem günlüğü yükü orta olur.
- Daha eski SQL Server sürümlerde, yedekleme sıkıştırması, SQL Server veritabanınızı şifrelediğinde artık verimlilik almadı. Bu davranış, planınız şirket içinde SQL Server veritabanınızı şifrelemek ve sonra Azure 'da veritabanını geri yüklemek için bir yedeklemeyi Azure 'a kopyalamak üzere bir sorun halinde geliştirebilir. SQL Server yedekleme sıkıştırması genellikle 4 faktörüyle bir sıkıştırma oranına erişir.
- SQL Server 2016 ile, SQL Server şifrelenmiş veritabanlarının ve verimli bir şekilde sıkıştırılarını sağlayan yeni işlevler sunuldu. Bazı ayrıntılar için [bu bloglara](https://blogs.msdn.microsoft.com/sqlcat/2016/06/20/sqlsweet16-episode-1-backup-compression-for-tde-enabled-databases/) bakın.
 
Yalnızca çok az SAP iş yükü olmadan TDE şifreleme uygulamasını düşünerek, şirket içi SAP veritabanınıza TDE veya Azure 'da bunu yapmak için daha iyi olup olmadığını özel yapılandırmanızda test etmelisiniz. Azure 'da, aşırı sağlama altyapısı açısından çok daha fazla esneklik elde edersiniz ve TDE uygulandıktan sonra altyapıyı küçültebilirsiniz.

### <a name="using-azure-key-vault"></a>Azure Key Vault kullanma
Azure, şifreleme anahtarlarını depolamak için bir [Key Vault](https://azure.microsoft.com/services/key-vault/) hizmeti sunar. Diğer taraftan SQL Server, TDE sertifikaları için mağaza olarak Azure Key Vault yararlanmak üzere bir bağlayıcı sunar.

Aşağıdakiler gibi SQL Server TDE listelerini Azure Key Vault kullanma hakkında daha fazla bilgi:

- [Azure Key Vault (SQL Server) kullanılarak Genişletilebilir anahtar yönetimi](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server?view=sql-server-2017).
- [Azure Key Vault kurulum adımlarını kullanarak Genişletilebilir anahtar yönetimini SQL Server](https://docs.microsoft.com/sql/relational-databases/security/encryption/setup-steps-for-extensible-key-management-using-the-azure-key-vault?view=sql-server-2017).
- [SQL Server Bağlayıcısı bakım & sorun giderme](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-connector-maintenance-troubleshooting?view=sql-server-2017).
- [Müşterilerin SQL Server saydam veri şifrelemesi – TDE + Azure Key Vault hakkında daha fazla soru](https://blogs.msdn.microsoft.com/saponsqlserver/2017/04/04/more-questions-from-customers-about-sql-server-transparent-data-encryption-tde-azure-key-vault/).


>[!IMPORTANT]
>SQL Server TDE, özellikle de Azure Anahtar Kasası ile, SQL Server 2014, SQL Server 2016 ve SQL Server 2017 için en son düzeltme eklerinin kullanılması önerilir. Nedeni, müşteri geri bildirimlerine, iyileştirmeleri ve düzeltmeleri koda göre uygulanır. Örnek olarak, [KBA #4058175](https://support.microsoft.com/help/4058175/tde-enabled-backup-and-restore-slow-if-encryption-key-is-stored-in-ekm)denetleyin.
>  

## <a name="9053f720-6f3b-4483-904d-15dc54141e30"></a>Azure 'da SAP için genel SQL Server Özeti
Bu kılavuzda birçok öneri bulunur ve Azure dağıtımınızı planlamadan önce onu birden çok kez okumanızı öneririz. Genel olarak, Azure 'a özgü önerilerle ilgili en iyi genel DBMS 'yi izlediğinizden emin olun:

1. Azure 'da en fazla avantaj sunan SQL Server 2017 gibi en son DBMS sürümünü kullanın. 
2. Veri dosyası yerleşimini ve Azure kısıtlamalarını dengelemek için SAP sisteminizi Azure 'da dikkatle planlayın:
   * Çok fazla disk bulundurmayın, ancak gerekli ıOPS 'nize ulaşabildiğinizden emin olmak için yeterli.
   * Yönetilen diskler kullanmıyorsanız, ıOPS 'nin Azure depolama hesabı başına da sınırlı olduğunu ve bu depolama hesaplarının her bir Azure aboneliği ([daha fazla ayrıntı][azure-subscription-service-limits]) içinde sınırlı olduğunu unutmayın. 
   * Yalnızca daha yüksek bir verimlilik elde etmeniz gerekiyorsa diskler genelinde Stripe.
3. Hiçbir koşulda yazılım yüklemeyin veya herhangi bir dosyayı hiçbir şekilde kalıcı hale getirin. kalıcı olmayan ve bu sürücüdeki herhangi bir şey Windows yeniden başlatıldığında kaybolduğu için sürücü.
4. Azure Standart depolama için disk önbelleği kullanmayın.
5. Azure coğrafi olarak çoğaltılan Azure Standart depolama hesaplarını kullanmayın.  DBMS iş yükleri için yerel olarak yedekli ' i kullanın.
6. Veritabanı verilerini çoğaltmak için DBMS satıcınızın HA/DR çözümünü kullanın.
7. Her zaman ad çözümlemesi kullan, IP adreslerine güvenmeyin.
8. SQL Server TDE kullanarak en son SQL Server yamaları uygulayın.
9. Mümkün olan en yüksek veritabanı sıkıştırmasını kullanın. SQL Server için sayfa sıkıştırması.
10. Azure Marketi 'nden SQL Server görüntülerini kullanırken dikkatli olun. SQL Server birini kullanırsanız, herhangi bir SAP NetWeaver sistemini yüklemeden önce örnek harmanlamasını değiştirmelisiniz.
11. [Dağıtım Kılavuzu][deployment-guide]' nda açıklandığı gibi Azure Için SAP ana bilgisayar izlemeyi yükleyip yapılandırın.
