---
title: SAP NetWeaver için Azure sanal makineleri planlama ve uygulama | Microsoft Docs
description: SAP NetWeaver için Azure sanal makineleri planlama ve uygulama
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/16/2019
ms.author: sedusch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d9b9476d8cc62585be7e7003d837607b502c8566
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71067855"
---
# <a name="azure-virtual-machines-planning-and-implementation-for-sap-netweaver"></a>SAP NetWeaver için Azure sanal makineleri planlama ve uygulama

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
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
[1909114]: https://launchpad.support.sap.com/#/notes/1909114
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

[msdn-set-Azvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:planning-guide.md  
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f
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
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/planning-monitoring-overview-2502.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-2901]:media/virtual-machines-shared-sap-planning-guide/2901-azure-ha-sap-ha-md.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-3201]:media/virtual-machines-shared-sap-planning-guide/3201-sap-ha-with-sql-md.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/install-az-ps
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
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md
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
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md
[virtual-machines-linux-agent-user-guide]:../../extensions/agent-linux.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../extensions/agent-windows.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-windows-capture-image-resource-manager]:../../windows/capture-image.md
[virtual-machines-windows-capture-image]:../../virtual-machines-windows-generalize-vhd.md
[virtual-machines-windows-capture-image-prepare-the-vm-for-image-capture]:../../virtual-machines-windows-generalize-vhd.md
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-create-upload-vhd-oracle]:../../linux/oracle-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability]:../../linux/manage-availability.md
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
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics-windows]:../../windows/multiple-nics.md
[virtual-networks-multiple-nics-linux]:../../linux/multiple-nics.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-about-vpngateways.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md
[capture-image-linux-step-2-create-vm-image]:../../linux/capture-image.md#step-2-create-vm-image

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Microsoft Azure, şirketlerin işlem ve depolama kaynaklarını uzun tedarik döngüleri olmadan en az sürede almasına olanak sağlar. Azure sanal makine hizmeti, şirketlerin SAP NetWeaver tabanlı uygulamalar gibi klasik uygulamaları Azure 'a dağıtmasını ve şirket içinde kullanılabilir kaynak olmadan güvenilirliğini ve kullanılabilirliğini genişletmelerini sağlar. Azure sanal makine Hizmetleri, şirketlerin Azure sanal makinelerini şirket içi etki alanları, özel bulutları ve SAP sistem Yatağuna etkin bir şekilde tümleştirmelerini sağlayan şirketler arası bağlantıyı da destekler.
Bu teknik incelemeye Microsoft Azure sanal makinenin temelleri açıklanmakta ve Azure 'daki SAP NetWeaver yüklemelerine yönelik planlama ve uygulama konuları ve gerçek başlatmadan önce okunan belge olması gerekir. Azure 'da SAP NetWeaver dağıtımları.
Kağıda, belirtilen platformlarda SAP yazılımının yüklemeleri ve dağıtımları için birincil kaynakları temsil eden SAP yükleme belgeleri ve SAP notları da bu şekilde tamamlar.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="summary"></a>Özet
Bulut bilgi Işlem, küçük şirketlerin büyük ve çok uluslu şirketlere kadar çok daha fazla ve BT sektörü dahilinde daha fazla önem elde eden yaygın olarak kullanılan bir terimdir.

Microsoft Azure, Microsoft 'un sunduğu, geniş bir yeni olanaklar sunan Cloud Services platformudur. Artık müşteriler, uygulamaları bulutta bir hizmet olarak hızlı bir şekilde temin edebilir ve serbest verebilir, böylece teknik veya bütçeleme kısıtlamalarıyla sınırlı değildir. Şirketler, donanım altyapısına zaman ve bütçe harcamak yerine uygulama, iş süreçlerine ve müşterilere ve kullanıcılara yönelik avantajlara odaklanabilirler.

Microsoft, Microsoft Azure Sanal Makine Hizmetleri ile kapsamlı bir Hizmet Olarak Altyapı (IaaS) platformu sunmaktadır. SAP NetWeaver tabanlı uygulamalar, Azure Sanal Makinelerinde (IaaS) desteklenmektedir. Bu Teknik İnceleme, Microsoft Azure içinde SAP NetWeaver tabanlı uygulamaların seçim platformu olarak nasıl planlanıp uygulanacağını açıklar.

Kağıdın kendisi iki ana yönüyle odaklanır:

* İlk bölümde, Azure 'da SAP NetWeaver tabanlı uygulamalar için desteklenen iki dağıtım modeli açıklanmaktadır. Ayrıca, Azure 'un SAP dağıtımlarını göz önünde bulundurarak genel işleme de açıklanmaktadır.
* İkinci bölüm, ilk bölümünde açıklanan iki farklı senaryoyu uygulayan ayrıntılar.

Ek kaynaklar için, bu belgedeki bölüm [kaynakları][planning-guide-1.2] bölümüne bakın.

### <a name="definitions-upfront"></a>Ön tanım tanımları
Belge boyunca aşağıdaki terimleri kullanırız:

* IaaS Hizmet olarak altyapı
* PaaS Hizmet olarak Platform
* SaaS Hizmet olarak yazılım
* SAP bileşeni: ECC, siyah beyaz, çözüm Yöneticisi veya S/4HANA gibi tek bir SAP uygulaması.  SAP bileşenleri geleneksel ABAP veya Java teknolojilerine veya Iş nesneleri gibi NetWeaver tabanlı olmayan bir uygulamaya dayalı olabilir.
* SAP Ortamı: geliştirme, QAS, eğitim, DR veya üretim gibi bir iş işlevi gerçekleştirmek üzere mantıksal olarak gruplandırılan bir veya daha fazla SAP bileşeni.
* SAP yatay: Bu terim, bir müşterinin BT yatay içindeki tüm SAP varlıklarını ifade eder. SAP yatay, tüm üretim ve üretim dışı ortamları içerir.
* SAP sistemi: Örneğin, bir SAP ERP geliştirme sistemi, SAP BW test sistemi, SAP CRM üretim sistemi vb. için DBMS katmanının ve uygulama katmanının birleşimi. Azure dağıtımlarında, bu iki katmanın şirket içi ve Azure arasında bölmek desteklenmez. SAP sisteminin şirket içinde dağıtıldığı ya da Azure 'da dağıtıldığı anlamına gelir. Ancak, SAP 'nin farklı sistemlerini Azure 'da veya şirket içinde dağıtabilirsiniz. Örneğin, Azure 'da SAP CRM geliştirme ve test sistemlerini, şirket içi SAP CRM üretim sistemine dağıtabilirsiniz.
* Şirketler arası veya karma: VM 'Lerin şirket içi veri merkezleri ve Azure arasında siteden siteye, çok siteli veya ExpressRoute bağlantısına sahip bir Azure aboneliğine dağıtıldığı bir senaryoyu açıklar. Yaygın Azure belgelerinde, bu tür dağıtımlar şirket içi veya hibrit senaryolar olarak da açıklanmaktadır. Bağlantının nedeni şirket içi etki alanlarını, şirket içi Active Directory/OpenLDAP ve şirket içi DNS 'yi Azure 'a genişletmenin nedenidir. Şirket içi yatay, aboneliğin Azure varlıklarına genişletilir. Bu uzantıya sahip olan VM 'Ler, şirket içi etki alanının bir parçası olabilir. Şirket içi etki alanının etki alanı kullanıcıları sunuculara erişebilir ve bu VM 'lerde (DBMS hizmetleri gibi) Hizmetleri çalıştırabilir. Şirket içinde dağıtılan ve Azure tarafından dağıtılan VM 'Ler arasındaki iletişim ve ad çözümlemesi mümkündür. Bu, SAP varlıklarını Azure 'a dağıtmanın en yaygın ve neredeyse dışlamalı durumdur. Daha fazla bilgi için bu [makaleye ve][vpn-gateway-cross-premises-options] [Bu][vpn-gateway-site-to-site-create]makaleye bakın.
* SAP için Azure Izleme uzantısı, gelişmiş Izleme ve Azure uzantısı: Bir ve aynı öğeyi açıkla. SAP konak aracısına Azure altyapısı hakkında bazı temel veriler sağlamak için, sizin tarafınızdan dağıtılması gereken bir VM uzantısını açıklar. SAP 'de SAP notları, Izleme uzantısı veya Gelişmiş izleme olarak bu şekilde ifade edebilir. Azure 'da **SAP Için Azure uzantısı**olarak buna başvuruyoruz.

> [!NOTE]
> SAP sistemleri çalıştıran Azure sanal makinelerinin, şirket içi bir etki alanının üyesi olduğu SAP sistemlerinin şirket içi veya karma dağıtımları, üretim SAP sistemlerinde desteklenir. Şirket içi veya karma Yapılandırma parçaları dağıtmak veya Azure 'a tam SAP landscapes desteği için desteklenir. Azure 'da tam SAP 'nin tamamen çalıştırılması, bu VM 'Lerin şirket içi etki alanı ve ADS/OpenLDAP kapsamında olmasını gerektirir. 
>
>



### <a name="e55d1e22-c2c8-460b-9897-64622a34fdff"></a>Kaynakların
Azure belgelerindeki SAP iş yükünün giriş noktası [burada](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)bulunur. Bu giriş noktasıyla başlayarak, şu konuları kapsayan birçok makaleyi bulabilirsiniz:

- Azure 'da SAP NetWeaver ve Business One
- Azure 'da çeşitli DBMS sistemleri için SAP DBMS Kılavuzu
- Azure 'da SAP iş yükü için yüksek kullanılabilirlik ve olağanüstü durum kurtarma
- Azure 'da SAP HANA çalıştırmak için özel kılavuz
- SAP HANA DBMS için Azure HANA büyük örneklerine özgü rehberlik 


> [!IMPORTANT]
> Başvurulan SAP yükleme kılavuzlarından veya diğer SAP belgelerinin bir bağlantısı kullanıldığında (başvuru ınstguide-01, bkz <http://service.sap.com/instguides>.). Önkoşul, yükleme işlemi veya belirli SAP işlevselliğinin ayrıntılarına geldiğinde, Microsoft belgeleri yalnızca SAP yazılımının yüklü olduğu ve bir üzerinde çalıştığı belirli görevleri kapsadığından, SAP belgelerinin ve kılavuzların her zaman dikkatle okunmalıdır. Sanal makine Microsoft Azure.
>
>

Aşağıdaki SAP notları, Azure 'daki SAP konusuyla ilgilidir:

| Dekont numarası | Başlık |
| --- | --- |
| [1928533] |Azure 'da SAP uygulamaları: Desteklenen Ürünler ve boyutlandırma |
| [2015553] |Microsoft Azure SAP: Destek önkoşulları |
| [1999351] |SAP için gelişmiş Azure Izleme sorunlarını giderme |
| [2178632] |Microsoft Azure üzerinde SAP için anahtar Izleme ölçümleri |
| [1409604] |Windows 'da sanallaştırma: Gelişmiş Izleme |
| [2191498] |Azure ile Linux üzerinde SAP: Gelişmiş Izleme |
| [2243692] |Microsoft Azure Linux (IaaS) sanal makinesi: SAP lisans sorunları |
| [1984787] |SUSE LINUX Enterprise Server 12: Yükleme notları |
| [2002167] |Red Hat Enterprise Linux 7. x: Yükleme ve yükseltme |
| [2069760] |Oracle Linux 7. x SAP yüklemesi ve yükseltmesi |
| [1597355] |Linux için takas boşluğu önerisi |

Ayrıca, Linux için tüm SAP notlarını içeren [SCN wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) 'yi de okuyun.

[Bu makalede][azure-subscription-service-limits-subscription], genel varsayılan sınırlamalar ve Azure aboneliklerinin en yüksek sınırlamaları bulunabilir.

## <a name="possible-scenarios"></a>Olası senaryolar
SAP, genellikle kuruluşlar içindeki en önemli iş uygulamalarından biri olarak görülür. Bu uygulamaların mimarisi ve işlemleri çok karmaşıktır ve kullanılabilirlik ve performans açısından gereksinimleri karşıladığınızdan emin olmanızı sağlar.

Böylece kuruluşlar, üzerinde iş açısından kritik iş süreçlerini çalıştırmak için hangi bulut sağlayıcısının seçeceğini dikkatle düşünmeleri gerekir. Azure, iş açısından kritik SAP uygulamaları ve iş süreçlerine yönelik ideal genel bulut platformudur. Çok çeşitli Azure altyapısı söz konusu olduğunda, neredeyse tüm mevcut SAP NetWeaver ve S/4HANA sistemleri, bugün Azure 'da barındırılabilir. Azure, çok sayıda terabayt belleği ve 200 ' den fazla CPU içeren VM 'Ler sağlar. Azure 'un ötesinde, 24 TB 'a varan ve en fazla 120 TB 'LıK genişleme ANA dağıtımları için genişleme HANA dağıtımlarına izin veren [Hana büyük örnekleri](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)sunmaktadır. 


SAP sistemlerini genel olarak Azure IaaS veya IaaS 'e başarıyla dağıtmak için geleneksel outsourcers veya barındırıcıların ve IaaS tekliflerinin teklifleri arasındaki önemli farklılıkları anlamak önemlidir. Geleneksel barındırıcı veya dış kaynaklar bir müşterinin barındırmak istediği iş yüküne (ağ, depolama ve sunucu türü) göre, müşterinin veya iş ortağının iş yükünü niteleyen ve doğru Azure 'u seçmesi sorumluluğunu karşılarken IaaS dağıtımları için VM 'Ler, depolama ve ağ bileşenleri.

İlk adım olarak, müşterilerin aşağıdaki öğeleri doğrulaması gerekir:

* Azure 'un desteklenen SAP VM türleri
* Azure 'da desteklenen SAP ürünleri/yayınları
* Azure 'da belirli SAP sürümleri için desteklenen işletim sistemi ve DBMS yayınları
* Farklı Azure SKU 'Ları tarafından sunulan SAPS işleme

Bu soruların yanıtları SAP Note [1928533]' de okunabilir.

İkinci bir adım olarak, Azure Kaynak ve bant genişliği sınırlarının şirket içi sistemlerin gerçek kaynak tüketimine kıyasla olması gerekir. Bu nedenle, müşterilerin alanında SAP ile desteklenen Azure türlerinin farklı yeteneklerini hakkında bilgi sahibi olmaları gerekir:

* Farklı VM türlerindeki CPU ve bellek kaynakları ve
* Farklı VM türlerindeki ıOPS bant genişliği ve
* Farklı VM türlerinin ağ özellikleri.

Bu verilerin çoğu [burada (Linux)][virtual-machines-sizes-linux] ve [burada (Windows)][virtual-machines-sizes-windows]bulunabilir.

Yukarıdaki bağlantıda listelenen limitlerin üst sınırlar olduğunu aklınızda bulundurun. Bu, kaynakların herhangi birine yönelik limitlerin tüm koşullarda sağlanabileceğine anlamına gelmez. Özel durumlar da, seçilen bir VM türünün CPU ve bellek kaynaklarıdır. SAP tarafından desteklenen VM türleri için CPU ve bellek kaynakları, sanal makine içinde tüketim için herhangi bir zamanda kullanılabilir ve bu şekilde ayrılır.

Microsoft Azure platformu çok kiracılı bir platformdur. Sonuç olarak, depolama, ağ ve diğer kaynaklar kiracılar arasında paylaşılır. Akıllı daraltma ve kota mantığı, bir kiracının başka bir kiracının (gürültülü komşu) performansını farklı bir şekilde etkileyerek engel olmak için kullanılır. Özellikle SAP HANA için Azure platformunu sertifikalamak için, Microsoft 'un, birden çok VM 'nin aynı konakta SAP 'ye düzenli olarak çalıştırılabileceği durumlarda kaynak yalıtımını kanıtlamasını sağlaması gerekir. Azure 'daki mantığın bant genişliğine karşı küçük, çok az paylaşılan platformlar, kaynakların şirket içi dağıtımlarıyla karşılaşabileceğine göre kaynak/bant genişliği kullanılabilirliği bölümünde daha büyük farklar ortaya çıkaracak şekilde çalışır. Azure üzerindeki bir SAP sisteminin, şirket içi bir sistem tarafından daha büyük farklar yaşayabileceğiniz, hesaba alınması olasılığı vardır.

Son bir adım, kullanılabilirlik gereksinimlerini değerlendirmelidir. Bu durum, temel alınan Azure altyapısının güncelleştirilmesi ve VM 'Leri çalıştıran konaklarının yeniden başlatılmasını gerektirir. Microsoft, [Azure 'daki sanal makineler Için bakımda](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)farklı durumları belgelemektedir. VM 'Lerin yeniden başlatmaya zorlanmasının, ancak Konuk işletim sistemine veya DBMS bileşenlerine yama yapmanız gereken durumlarda daha da önemli olan durumlarda, üretim SAP sistemleriniz için geçerli bir yüksek kullanılabilirlik kavramları geliştirmeniz gerekir. Bu gereksinim, şirket içi yüz yüze sahip olduğunuz gereksinimlerinden farklı değildir. Microsoft, platform değişikliklerinden kaynaklanan kapalı kalma süresini azaltmak için Azure platformunu artmasıyla. 

SAP sistemini Azure 'a başarıyla dağıtmak için, şirket içi SAP sistemi, veritabanı ve SAP uygulamaları, Azure altyapısının sağlayabileceği ve çalışabilecek kaynaklara sığacak şekilde SAP Azure destek matrisi 'nde görünmelidir Kullanılabilirlik SLA 'Ları Microsoft Azure sunar. Bu sistemler tanımlandıkları için aşağıdaki iki dağıtım senaryosundan birine karar vermeniz gerekir.





### <a name="f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10"></a>Şirket içi ağla tam olarak tümleştirildiği gereksinimle, tek veya birden çok SAP VM 'lerinin Azure 'a çapraz şirket Içi dağıtımı
![Siteden siteye bağlantısı olan VPN (Şirket içi)][planning-guide-figure-300]

Bu senaryo, olası birçok dağıtım deseni içeren bir şirket içi senaryosudur. Azure 'da SAP yatay 'ın şirket içi ve diğer bölümlerinin bazı parçalarını çalıştırıyor olarak açıklanabilir. SAP bileşenlerinin bir kısmının Azure üzerinde çalıştığı olgunun tüm yönleri, son kullanıcılar için saydam olmalıdır. Bu nedenle, SAP taşıma düzeltme sistemi (STMS), RFC Iletişimi, yazdırma, güvenlik (SSO gibi), vb. Azure üzerinde çalışan SAP sistemleri için sorunsuz bir şekilde çalışır. Ancak şirketler arası senaryo, Azure 'da müşterinin etki alanı ve DNS ile Azure 'da genişletilmiş tam SAP 'nin çalıştığı bir senaryoyu da açıklar.

> [!NOTE]
> Bu, üretken SAP sistemlerini çalıştırmak için desteklenen dağıtım senaryosudur.
>
>

Şirket içi ağınızı Microsoft Azure 'ye bağlama hakkında daha fazla bilgi için [Bu makaleyi][vpn-gateway-create-site-to-site-rm-powershell] okuyun

> [!IMPORTANT]
> Azure ile şirket içi müşteri dağıtımları arasındaki şirketler arası senaryolar hakkında konuşduğumuz zaman, tüm SAP sistemlerinin ayrıntı düzeyine bakıyoruz. Şirketler arası senaryolar için *Desteklenmeyen* senaryolar şunlardır:
>
> * Farklı dağıtım yöntemlerinde farklı SAP uygulaması katmanları çalıştırma. Örneğin, şirket içi DBMS katmanını çalıştıran, ancak VM 'lerde Azure VM 'Ler olarak dağıtılan SAP uygulama katmanı veya tam tersi de geçerlidir.
> * Azure 'da ve bazı şirket içi SAP katmanının bazı bileşenleri. Örneğin, şirket içi ve Azure VM 'Ler arasındaki SAP uygulama katmanının örneklerini bölmek.
> * Birden çok Azure bölgesi üzerinde bir sistemin SAP örneklerini çalıştıran VM 'lerin dağıtımı desteklenmez.
>
> Bu kısıtlamaların nedeni, özellikle uygulama örnekleri ve SAP sisteminin DBMS katmanı arasında bir SAP sisteminde düşük gecikmeli yüksek performanslı ağın gereksinimidir.
>
> Büyük ölçüde tümleşik olan birden çok SAP sistemi kullanılırken sistemleri ve bölgeleri özel olarak planlama gerçekleşmelidir. Ağ gecikmesini en aza indirmek için bu sistemleri birbirlerine olabildiğince yakın bir şekilde dağıttığınızdan emin olun. Son derece tümleştirilmiş SAP sistemlerine örnek olarak şunlar verilebilir:
> * ERP veya CRM ya da SRM gibi SAP OLTP sistemlerinden veri okuma SAP BW veya
> * SAP SLT, birden çok SPA sistemi arasında veya SAP ile SAP olmayan sistemler arasında veri çoğaltmak için kullanılır; veya
> * SAP ERP sistemine bağlı SAP S/4; benzerlerini.


### <a name="supported-os-and-database-releases"></a>Desteklenen işletim sistemi ve veritabanı sürümleri
* Azure sanal makine Hizmetleri için desteklenen Microsoft sunucu yazılımları bu makalede listelenmiştir: <https://support.microsoft.com/kb/2721672>.
* Desteklenen işletim sistemi sürümleri, SAP Software ile birlikte Azure sanal makine hizmetlerinde desteklenen veritabanı sistemi sürümleri, SAP Note [1928533]' de belgelenmiştir.
* Azure sanal makine hizmetlerinde desteklenen SAP uygulamaları ve sürümleri SAP Note [1928533]' de belgelenmiştir.
* SAP senaryolarında Azure 'da Konuk VM olarak çalıştırmak için yalnızca 64Bit bit görüntüleri desteklenir. Sonuç olarak, yalnızca 64 bitlik SAP uygulamaları ve veritabanları desteklenir.

## <a name="microsoft-azure-virtual-machine-services"></a>Microsoft Azure sanal makine Hizmetleri
Microsoft Azure platformu, Microsoft veri merkezlerinde barındırılan ve çalıştırılan bir İnternet ölçekli bulut hizmetleri platformudur. Platform Microsoft Azure sanal makine Hizmetleri (hizmet olarak altyapı veya IaaS) ve bir dizi zengin hizmet olarak platform (PaaS) özelliği içerir.

Azure platformu, ön teknoloji ve altyapı satın alma ihtiyacını azaltır. Web uygulamasını ve bağlı uygulamaları barındırmak, ölçeklendirmek ve yönetmek için isteğe bağlı bilgi işlem ve depolama sağlayarak uygulamaları sürdürmeyi ve işletim sistemlerini basitleştirir. Altyapı yönetimi, bir Kullandıkça Öde fiyatlandırma modeli seçeneğiyle, kullanım ihtiyaçlarını karşılamak için yüksek kullanılabilirlik ve dinamik ölçeklendirme için tasarlanan bir platform ile otomatiktir.

![Microsoft Azure sanal makine Hizmetleri 'nin konumlandırması][planning-guide-figure-400]

Microsoft, Azure sanal makine Hizmetleri ile IaaS örnekleri olarak Azure 'a özel sunucu görüntüleri dağıtmanızı sağlar (bkz. Şekil 4). Azure 'daki sanal makineler Hyper-V sanal sabit sürücülerine (VHD) dayanır ve konuk işletim sistemi olarak farklı işletim sistemlerini çalıştırabilir.

Azure sanal makine hizmeti, işlemsel bir perspektiften, şirket içinde dağıtılan sanal makineler gibi benzer deneyimler sağlar. Ancak, altyapıyı temin etmeniz, yönetmeniz ve yönetmeniz önemli ölçüde avantajlıdır. Geliştiriciler ve Yöneticiler, bu sanal makineler içindeki işletim sistemi görüntüsü üzerinde tam denetime sahiptir. Yöneticiler, yazılım dağıtım görevlerinin yanı sıra bakım ve sorun giderme görevlerini gerçekleştirmek için bu sanal makinelerde uzaktan oturum açabilir. Dağıtımın yanı sıra, Azure VM 'lerinin boyutları ve becerileri tek kısıtlamalardır. Bu boyutlar, şirket içinde yapıladıkça yapılandırmada ince bir ayrıntılı olmayabilir. Bir bileşimini temsil eden bir sanal makine türleri seçeneği vardır:

* VCPU sayısı
* Bellek
* İliştirilebilecek VHD sayısı
* Ağ ve depolama bant genişlikleri

Sunulan çeşitli farklı sanal makine boyutlarının boyut ve sınırlamaları, [Bu makaledeki (Linux)][virtual-machines-sizes-linux] ve [Bu makaledeki (Windows)][virtual-machines-sizes-windows]bir tabloda görülebilir.

Azure bölgelerinin her birinde farklı VM Serisi sunulmayabilir (Azure bölgeleri için bir sonraki bölüme bakın). Ayrıca, tüm VM 'Lerin veya VM dizilerinin SAP için sertifikalı olmadığı farkında olun.

> [!IMPORTANT]
> SAP NetWeaver tabanlı uygulamaların kullanımı için, yalnızca SAP Note [1928533] ' de listelenen VM türlerinin ve yapılandırmaların alt kümesi desteklenir.
>
>

### <a name="be80d1b9-a463-4845-bd35-f4cebdb5424a"></a>Azure bölgeleri
Sanal makineler, *Azure bölgeleri*olarak adlandırılan bir uygulamasına dağıtılır. Bir Azure bölgesi yakın bir yerde bulunan bir veya birden çok veri merkezi olabilir. Microsoft, dünyanın en az iki Azure bölgesine sahiptir. Örneğin, Avrupa 'da *Kuzey Avrupa* Azure bölgesi ve *Batı Avrupa*biridir. Bir coğrafi bölge içindeki bu iki Azure bölgesi, doğal veya teknik felaketler aynı coğrafi bölgede her iki Azure bölgesini de etkilememesi için yeterince önemli ölçüde ayrılır. Microsoft, yeni Azure bölgelerini küresel olarak farklı coğrafi bölgelerde artmasıyla bir şekilde kullanıma sunduğundan, bu bölgelerin sayısı artmasıyla artmıştır ve 2015 Aralık itibariyle daha önce duyurulan ek bölgelere sahip 20 Azure bölgesinin sayısına ulaşıldı. Bir müşteri olarak, Çin 'deki iki Azure bölgesi de dahil olmak üzere, bu bölgelere SAP sistemleri dağıtabilirsiniz. Azure bölgeleri hakkındaki güncel güncel bilgiler için şu Web sitesine bakın:<https://azure.microsoft.com/regions/>

### <a name="8d8ad4b8-6093-4b91-ac36-ea56d80dbf77"></a>Microsoft Azure sanal makine kavramı
Microsoft Azure, bir hizmet olarak altyapı (IaaS) çözümü sunarak benzer işlevlere sahip sanal makineleri şirket içi bir sanallaştırma çözümü olarak barındırır. Dağıtım ve yönetim özelliklerini de sunan Azure portal, PowerShell veya CLı içinden sanal makineler oluşturabilirsiniz.

Azure Resource Manager, uygulamalarınızı bildirim temelli bir şablon aracılığıyla sağlamanıza olanak tanır. Tek bir şablonda birden çok hizmeti bağımlılıklarıyla birlikte dağıtabilirsiniz. Uygulama yaşam döngüsünün her aşamasında uygulamanızı tekrar tekrar dağıtmak için aynı şablonu kullanırsınız.

Kaynak Yöneticisi şablonları kullanma hakkında daha fazla bilgiye buradan ulaşabilirsiniz:

* [Azure Resource Manager şablonları ve Azure CLı kullanarak sanal makineleri dağıtma ve yönetme](../../linux/create-ssh-secured-vm-from-template.md)
* [Azure Resource Manager ve PowerShell kullanarak sanal makineleri yönetme][virtual-machines-deploy-rmtemplates-powershell]
* <https://azure.microsoft.com/documentation/templates/>

Diğer ilginç bir özellik, sanal makinelerden görüntü oluşturma olanağıdır. bu sayede, gereksinimlerinizi karşılayan sanal makine örneklerini hızlı bir şekilde dağıtabileceğiniz belirli depolar hazırlanabilirsiniz.

Sanal makinelerden görüntü oluşturma hakkında daha fazla bilgi, [Bu makalede (Linux)][virtual-machines-linux-capture-image-resource-manager] ve [Bu makalede (Windows)][virtual-machines-windows-capture-image-resource-manager]bulunabilir.

#### <a name="df49dc09-141b-4f34-a4a2-990913b30358"></a>Hata etki alanları
Hata etki alanları, veri merkezlerinde bulunan fiziksel altyapıyla yakından ilişkili olan fiziksel bir hata birimini temsil eder ve bir fiziksel dikey pencere veya raf bir hata etki alanı olarak kabul edildiğinde, ikisi arasında doğrudan bire bir eşleme yoktur.

Birden çok sanal makineyi Microsoft Azure sanal makine Hizmetleri ' nde bir SAP sisteminin bir parçası olarak dağıttığınızda, uygulamanızı farklı hata etki alanlarına dağıtmak için Azure yapı denetleyicisi 'ni etkileyebilir ve böylece SLA Microsoft Azure. Ancak, bir Azure ölçek birimi (yüzlerce Işlem düğümü veya depolama düğümü ve ağ koleksiyonu) üzerinden hata etki alanlarının dağıtılması veya VM 'lerin belirli bir hata etki alanına atanması, doğrudan denetime sahip olduğunuz bir şeydir. Azure yapı denetleyicisi 'ni farklı hata etki alanları üzerinden bir VM kümesi dağıtmak üzere yönlendirmek için, dağıtım zamanında VM 'lere bir Azure kullanılabilirlik kümesi atamanız gerekir. Azure kullanılabilirlik kümeleri hakkında daha fazla bilgi için bu belgedeki bölüm [Azure kullanılabilirlik kümeleri][planning-guide-3.2.3] bölümüne bakın.

#### <a name="fc1ac8b2-e54a-487c-8581-d3cc6625e560"></a>Yükseltme etki alanları
Yükseltme etki alanları, birden çok VM 'de çalışan SAP örneklerinden oluşan SAP sistemi içindeki bir VM 'nin nasıl güncelleştirileceğini belirlemede yardımcı olan bir mantıksal birimi temsil eder. Bir yükseltme gerçekleştiğinde Microsoft Azure, bu yükseltme etki alanlarını tek tek güncelleştirme sürecindedir. VM 'Leri dağıtım zamanında farklı yükseltme etki alanları üzerinden yayarak, SAP sisteminizi kısmen olası kapalı kalma süresinden koruyabilirsiniz. Azure 'u, farklı yükseltme etki alanları üzerinden SAP sisteminin sanal makinelerini dağıtmaya zorlamak için, her bir VM 'nin dağıtım zamanında belirli bir öznitelik ayarlamanız gerekir. Hata etki alanlarına benzer şekilde, Azure ölçek birimi birden fazla yükseltme etki alanına bölünmüştür. Azure yapı denetleyicisi 'ni farklı yükseltme etki alanları üzerinden bir VM kümesi dağıtmak üzere yönlendirmek için, dağıtım zamanında VM 'lere bir Azure kullanılabilirlik kümesi atamanız gerekir. Azure kullanılabilirlik kümeleri hakkında daha fazla bilgi için aşağıdaki bölüm [Azure kullanılabilirlik kümeleri][planning-guide-3.2.3] bölümüne bakın.

#### <a name="18810088-f9be-4c97-958a-27996255c665"></a>Azure kullanılabilirlik kümeleri
Tek bir Azure kullanılabilirlik kümesindeki Azure sanal makineleri, farklı hata ve yükseltme etki alanları üzerinden Azure yapı denetleyicisi tarafından dağıtılır. Farklı hata ve yükseltme etki alanları üzerinden dağıtımın amacı, bir SAP sisteminin tüm VM 'lerinin altyapı bakımı veya bir hata etki alanındaki bir hata durumunda kapatılmasını önlemektir. Varsayılan olarak, VM 'Ler bir kullanılabilirlik kümesinin parçası değildir. Bir VM 'nin bir kullanılabilirlik kümesinde katılımı, bir VM 'nin yeniden yapılandırılması ve yeniden dağıtılması ile dağıtım sırasında veya sonrasında tanımlanır.

Azure kullanılabilirlik kümeleri kavramını ve kullanılabilirlik kümelerinin hata ve yükseltme etki alanlarıyla ilişkisini anlamak için [Bu makaleyi][virtual-machines-manage-availability] okuyun

Bir JSON şablonu aracılığıyla Azure Resource Manager kullanılabilirlik kümelerini tanımlamak için [REST API özelliklerine](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2015-06-15/swagger/compute.json) bakın ve "kullanılabilirlik" araması yapın.

### <a name="a72afa26-4bf4-4a25-8cf7-855d6032157f"></a>Depo Microsoft Azure Depolama ve veri diskleri
Microsoft Azure Sanal Makineler farklı depolama türlerini kullanır. SAP 'yi Azure sanal makine Hizmetleri 'nde uygularken, bu iki ana depolama türü arasındaki farklılıkları anlamak önemlidir:

* Kalıcı olmayan, geçici depolama.
* Kalıcı depolama alanı.

VM dağıtıldıktan sonra Azure VM 'Ler kalıcı olmayan diskler sunar. VM 'nin yeniden başlatılması durumunda, bu sürücülerdeki tüm içerikler silinir. Bu nedenle, veri dosyaları ve veritabanlarının günlük/yineleme dosyalarının, kalıcı olmayan sürücülerde hiçbir koşul olmaması gerekir. Bu kalıcı olmayan sürücülerin tempdb ve temp Tablespaces için uygun olabileceği bazı veritabanları için özel durumlar olabilir. Ancak, bu diskleri kalıcı olmayan bir VM 'Ler için kullanmaktan kaçının çünkü bu kalıcı olmayan sürücüler bu VM ailesiyle üretilen iş ile sınırlıdır. Daha fazla ayrıntı için, [Azure 'Da Windows VM 'lerinde geçici sürücüyü anlama](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/) makalesini okuyun.

---
> ![Windows][Logo_Windows] Windows
> 
> Sürücü D:\ Azure VM 'de, Azure işlem düğümündeki bazı yerel diskler tarafından desteklenen kalıcı olmayan bir sürücüdür. Kalıcı olmadığı için bu, D:\ ' deki içerikte yapılan tüm değişikliklerin yapıldığı anlamına gelir. VM yeniden başlatıldığında sürücü kaybedilir. "Tüm değişiklikler", depolanan dosyalar, oluşturulan dizinler, yüklü uygulamalar vb. gibi.
> 
> ![Linux][Logo_Linux] Linux
> 
> Linux Azure VM 'Leri, Azure işlem düğümündeki yerel disklerle desteklenen kalıcı olmayan bir sürücü olan/mnt/Resource dizininde otomatik olarak bir sürücü bağlayabilir. Kalıcı olmadığı için bu,/mnt/Resource dosyasındaki içerikte yapılan tüm değişikliklerin VM yeniden başlatıldığında kaybedildiği anlamına gelir. Depolanan dosyalar, oluşturulan dizinler, yüklü uygulamalar vb. gibi değişiklikler tarafından yapılır.
> 
> 

---

Microsoft Azure Depolama kalıcı depolama ve SAN depolamada görülen tipik koruma ve artıklık düzeyleri sağlar. Azure Storage tabanlı diskler, Azure Storage hizmetlerinde bulunan sanal sabit disktir (VHD). Yerel işletim sistemi-disk (Windows C:\, Linux/dev/sda1), Azure depolama 'da depolanır ve VM 'ye bağlanan ek birimler/diskler de burada depolanır.

Mevcut bir VHD 'yi şirket içinde karşıya yüklemek veya Azure içinden boş olanları oluşturmak ve bunları dağıtılan VM 'lere eklemek mümkündür.

Bir VHD 'YI Azure depolama 'ya oluşturduktan veya karşıya yükledikten sonra, var olan bir sanal makineye bunları bağlamak ve var olan (çıkarılmış) VHD 'YI kopyalamak mümkündür.

Bu VHD 'ler kalıcı olduğu için, bir sanal makine örneğini yeniden başlatma ve yeniden oluşturma sırasında bunlar içindeki veriler ve değişiklikler güvenlidir. Bir örnek silinmiş olsa da, bu VHD 'Ler güvende kalır ve işletim sistemi dışındaki disklerin diğer VM 'lere bağlanması durumunda yeniden dağıtılabilir.

Azure depolama hakkında daha fazla bilgiyi şurada bulabilirsiniz:

* <https://azure.microsoft.com/documentation/services/storage/>
* <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>
* <https://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview.aspx>

#### <a name="azure-standard-storage"></a>Azure Standart depolama
Azure Standart depolama, Azure IaaS yayınlandığında kullanılabilen depolama türüdür. Tek disk başına uygulanan ıOPS kotası vardı. Karşılaşılan gecikme süresi, genellikle şirket içinde barındırılan yüksek kaliteli SAP sistemleri için dağıtılan SAN/NAS cihazlarıyla aynı sınıfta değildi. Bununla birlikte, Azure Standart depolama, Azure 'da dağıtılan birçok yüzlerce SAP sistemi için yeterince uzun süre boyunca kullanıma yöneliktir.

Azure Standart depolama hesaplarında depolanan diskler, depolanan gerçek veriler, depolama işlemleri hacmi, giden veri aktarımları ve artıklık seçeneği temelinde ücretlendirilir. Birçok disk, en fazla 1 TB boyutunda oluşturulabilir, ancak boş kaldığı sürece hiçbir ücret alınmaz. Bir VHD 'yi 100 GB ile doldurursanız, VHD 'nin ile oluşturduğu Nominal boyut için değil, 100 GB depolama için ücretlendirilirsiniz.

#### <a name="ff5ad0f9-f7f4-4022-9102-af07aef3bc92"></a>Azure Premium Depolama
Azure Premium Depolama, şunları sağlamak için hedefle tanıtılmıştır:

* Daha iyi g/ç gecikmesi.
* Daha iyi üretilen iş.
* G/ç gecikmede daha az değişkenlik.

Bu amaçla, en önemli iki değişikliğin bulunduğu birçok değişiklik yapılmıştır:

* Azure depolama düğümlerinde SSD disklerinin kullanımı
* Bir Azure işlem düğümünün yerel SSD 'si tarafından desteklenen yeni bir okuma önbelleği

Yeteneklerin disk (veya VHD) boyutuna bağlı olarak değişmediği standart depolamanın tersine, Premium Depolama şu anda bu makalede gösterilen üç farklı disk kategorisine sahiptir:<https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/>

IOPS/disk ve disk işleme/disk 'in disklerin boyut kategorisine bağlı olduğunu görürsünüz

Premium Depolama söz konusu olduğunda maliyet temeli, bu tür disklerde depolanan gerçek veri hacmi değildir, ancak bu tür bir diskin disk içinde depolanan veri miktarından bağımsız olarak boyut kategorisi olur.

Ayrıca, gösterilen boyut kategorilerine doğrudan eşlenmemiş Premium depolamada diskler oluşturabilirsiniz. Özellikle, diskler standart depolamadan Premium depolamaya kopyalanırken bu durum olabilir. Bu gibi durumlarda, bir sonraki en büyük Premium Depolama diski ile eşleme yapılır.

SAP ile sertifikalı Azure VM ailelerinin birçoğu, Premium Depolama ile veya Azure Standard ile Premium Depolama arasında bir karışımta çalışabilir.

[Bu makalede (Linux)][virtual-machines-sizes-linux] ve [Bu makalede (Windows)][virtual-machines-sizes-windows]DS serisi VM 'lerinin bir bölümünü kullanıma Sundıysanız, Premium Depolama disklerinin VM düzeyinin ayrıntı düzeyi üzerinde veri hacmi sınırlamaları olduğunu fark etmiş olursunuz. Farklı DS serisi veya GS serisi VM 'lerinin Ayrıca, bağlanalabilen veri disklerinin sayısına göre farklı sınırlamaları vardır. Bu sınırlar yukarıda bahsedilen makalede da belgelenmiştir. Ancak, örneğin, 32 x P30 disklerini tek bir DS14 sanal makinesine bağlarsanız, P30 diskinin en yüksek aktarım hızını 32 x alamaz. Bunun yerine, makalede belgelendiği gibi VM düzeyindeki en yüksek aktarım hızı, veri üretimini sınırlar.

Premium depolama hakkında daha fazla bilgi için şurada bulunabilir:<https://azure.microsoft.com/blog/2015/04/16/azure-premium-storage-now-generally-available-2>

#### <a name="azure-storage-accounts"></a>Azure Depolama Hesapları

Azure 'da hizmet veya VM 'Leri dağıttığınızda, VHD 'lerin ve VM görüntülerinin dağıtımı, Azure depolama hesapları adlı birimlerde düzenlenebilir. Bir Azure dağıtımı planlarken, Azure 'un kısıtlamalarını dikkatle göz önünde bulundurmanız gerekir. Bir tarafta, Azure aboneliği başına sınırlı sayıda depolama hesabı vardır. Her Azure depolama hesabı çok sayıda VHD dosyası tutabilecek olsa da, depolama hesabı başına toplam ıOPS üzerinde sabit bir sınır vardır. DBMS sistemleri ile yüzlerce SAP VM dağıtırken, önemli GÇ çağrıları oluşturan çok sayıda Azure depolama hesabı arasında yüksek ıOPS DBMS VM 'Leri dağıtmanız önerilir. Her abonelik için Azure depolama hesaplarının geçerli limitini aşmamak için dikkatli olunması gerekir. Depolama alanı, bir SAP sistemi için veritabanı dağıtımının önemli bir parçası olduğundan, bu kavram zaten başvurulan [DBMS dağıtım kılavuzunda][dbms-guide]daha ayrıntılı bir şekilde ele alınmıştır.

[Bu makalede][storage-scalability-targets]Azure depolama hesapları hakkında daha fazla bilgi bulabilirsiniz. Bu makaleyi okuyarak, Azure Standart depolama hesapları ve Premium Depolama hesapları arasındaki sınırlamalarla ilgili farklılıklar olduğunu fark etmiş olursunuz. Önemli farklılıklar, böyle bir depolama hesabı içinde depolanabilecek veri birimidir. Standart depolamada, birim, Premium depolamayla daha büyük bir büyüklük. Diğer taraftan, standart depolama hesabı ıOPS 'de ciddi ölçüde sınırlıdır (bkz. sütun **Toplam Istek oranı**), ancak Azure Premium depolama hesabının böyle bir sınırlaması yoktur. SAP sistemlerinin, özellikle de DBMS sunucularının dağıtımlarını ele alırken bu farklılıkların ayrıntılarını ve sonuçlarını tartışacağız.

Bir depolama hesabı içinde, farklı VHD 'leri düzenlemek ve kategorilere ayırmak amacıyla farklı kapsayıcılar oluşturma olanağına sahip olursunuz. Bu kapsayıcılar, örneğin farklı VM 'lerin ayrı VHD 'Leri için kullanılır. Tek bir kapsayıcı veya birden çok kapsayıcıyı tek bir Azure depolama hesabı altında kullanmanın hiçbir performans etkileri yoktur.

Azure 'da bir VHD adı, Azure 'da VHD için benzersiz bir ad sağlaması gereken aşağıdaki adlandırma bağlantısını izler:

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

Yukarıdaki dize, Azure depolama 'da depolanan VHD 'YI benzersiz şekilde tanımlamalıdır.

#### <a name="c55b2c6e-3ca1-4476-be16-16c81927550f"></a>Yönetilen diskler

Yönetilen diskler, Azure depolama hesaplarında depolanan VHD 'ler yerine kullanılabilecek Azure Resource Manager yeni bir kaynak türüdür. Yönetilen diskler, bağlı oldukları sanal makine kullanılabilirlik kümesiyle otomatik olarak hizalanır ve bu nedenle sanal makinenizin ve sanal makinede çalışan hizmetlerin kullanılabilirliğini arttırır. Daha fazla bilgi için [genel bakış makalesini](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview)okuyun.

Sanal makinelerinizin dağıtımını ve yönetimini basitleştireceğiz için yönetilen disk kullanmanızı öneririz.
SAP Şu anda yalnızca Premium yönetilen diskleri destekler. Daha fazla bilgi için SAP Note [1928533]makalesini okuyun.

#### <a name="microsoft-azure-storage-resiliency"></a>Microsoft Azure Depolama dayanıklılık

Microsoft Azure Depolama, temel VHD 'yi (işletim sistemi) ve bağlı diskleri veya Blobları en az üç ayrı depolama düğümüne depolar. Bu olgu yerel yedekli depolama (LRS) olarak adlandırılır. LRS, Azure 'daki tüm depolama türleri için varsayılandır. 

[Azure Storage çoğaltma](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)makalesinde açıklanan, daha birçok artıklık yöntemi vardır.

> [!NOTE]
>Azure Premium Depolama itibariyle, DBMS VM 'Ler için önerilen depolama türü ve veritabanı ve günlük/yineleme dosyalarını depolayan diskler, kullanılabilir tek yöntem ise LRS 'dir. Sonuç olarak, veritabanı veri çoğaltmasını başka bir Azure bölgesine veya başka bir Azure kullanılabilirlik bölgesine etkinleştirmek için SQL Server Always on, Oracle Data Guard veya HANA sistem çoğaltması gibi veritabanı yöntemlerini yapılandırmanız gerekir.


> [!NOTE]
> DBMS dağıtımları için, coğrafi olarak yedekli depolamanın Azure Standart depolama ile kullanılabilir olarak kullanılması önerilmez çünkü bu, önemli performans etkisine sahiptir ve bir VM 'ye bağlı farklı VHD 'lerde yazma sırasını dikkate almaz. Veritabanı ve günlük/yineleme dosyaları, kaynak VM tarafında birden çok VHD 'ye yayıldığında (büyük olasılıkla durum olarak), çoğaltma hedefi tarafında, farklı VHD 'lerde yazma sırasının dayanmasının büyük bir olasılığı vardır.


### <a name="61678387-8868-435d-9f8c-450b2424f5bd"></a>Microsoft Azure ağı

Microsoft Azure, SAP yazılımıyla ilgili tüm senaryoların eşlenmesinin yapılmasına izin veren bir ağ altyapısı sağlar. Özellikleri şunlardır:

* Windows Terminal Hizmetleri veya SSH/VNC aracılığıyla dışından doğrudan VM 'lere erişim
* VM 'Ler içindeki uygulamalar tarafından kullanılan hizmetlere ve belirli bağlantı noktalarına erişim
* Azure VM 'Leri olarak dağıtılan bir VM grubu arasındaki iç Iletişim ve ad çözümlemesi
* Müşterinin Şirket içi ağı ve Azure ağı arasındaki şirketler arası bağlantı
* Azure siteleri arasında çapraz Azure bölgesi veya veri merkezi bağlantısı

Buradan daha fazla bilgi bulabilirsiniz: <https://azure.microsoft.com/documentation/services/virtual-network/>

Azure 'da ad ve IP çözümlemesini yapılandırmak için birçok farklı olasılık vardır. Ayrıca, kendi DNS sunucunuzu kurmak yerine kullanılabilecek bir Azure DNS hizmeti de vardır. [Bu makalede][virtual-networks-manage-dns-in-vnet] ve [Bu sayfada](https://azure.microsoft.com/services/dns/)daha fazla bilgi bulunabilir.

Şirketler arası veya Karma senaryolar için şirket içi AD/OpenLDAP/DNS 'in VPN veya özel Azure bağlantısı aracılığıyla genişletildiğinden emin olun. Burada belgelenen bazı senaryolar için, Azure 'da bir AD/OpenLDAP çoğaltmasının yüklü olması gerekebilir.

Ağ ve ad çözümlemesi bir SAP sistemi için veritabanı dağıtımının önemli bir parçası olduğundan, bu kavram [DBMS dağıtım kılavuzunda][dbms-guide]daha ayrıntılı olarak ele alınmıştır.

##### <a name="azure-virtual-networks"></a>Azure Sanal Ağları

Azure sanal ağını oluşturarak Azure DHCP işlevselliği tarafından ayrılan özel IP adreslerinin adres aralığını tanımlayabilirsiniz. Şirketler arası senaryolarda, tanımlanan IP adresi aralığı yine de Azure tarafından DHCP kullanılarak ayrılır. Bununla birlikte, etki alanı adı çözümlemesi şirket içinde yapılır (VM 'Lerin şirket içi etki alanının bir parçası olduğu varsayıldığında) ve bu nedenle farklı Azure Cloud Services dışındaki adresleri çözebilirler.

Azure 'daki her sanal makinenin bir sanal ağa bağlanması gerekir.

[Bu makalede][resource-groups-networking] ve [Bu sayfada](https://azure.microsoft.com/documentation/services/virtual-network/)daha fazla ayrıntı bulabilirsiniz.


> [!NOTE]
> Varsayılan olarak, bir VM dağıtıldıktan sonra sanal ağ yapılandırmasını değiştiremezsiniz. TCP/IP ayarlarının Azure DHCP sunucusuna bırakılması gerekir. Varsayılan davranış dinamik IP atamasıdır.
>
>

Sanal ağ kartının MAC adresi değişebilir, örneğin yeniden boyutlandırdıktan sonra Windows veya Linux konuk işletim sistemi yeni ağ kartını seçer ve bu durumda IP ve DNS adreslerini atamak için DHCP 'yi otomatik olarak kullanır.

##### <a name="static-ip-assignment"></a>Statik IP ataması
Bir Azure sanal ağı içindeki VM 'lere sabit veya ayrılmış IP adresleri atamak mümkündür. VM 'Leri bir Azure sanal ağında çalıştırmak, bazı senaryolar için gerektiğinde bu işlevden yararlanmak için harika bir olasılık açar. VM 'nin çalışıyor veya kapalı olmasından bağımsız olarak, IP ataması VM 'nin varlığı boyunca geçerli kalır. Sonuç olarak, sanal ağ için IP adresi aralığını tanımlarken VM 'lerin genel sayısını (çalışıyor ve durdurulmuş VM 'Ler) hesaba getirmeniz gerekir. IP adresi, VM ve ağ arabirimi silinene veya IP adresi yeniden atanana kadar atanmış olarak kalır. Daha fazla bilgi için [Bu makaleyi][virtual-networks-static-private-ip-arm-pportal]okuyun.

> [!NOTE]
> Ayrı sanal NIC 'ler için Azure aracılığıyla statik IP adresleri atamanız gerekir. Konuk işletim sistemi içinde bir vNIC 'e statik IP adresleri atamamalısınız. Azure Backup hizmeti gibi bazı Azure Hizmetleri, en azından birincil vNIC 'nin statik IP adreslerine değil, DHCP olarak ayarlandığı gerçeğini temel alır. Ayrıca bkz. belge [Azure sanal makine yedeklemesi sorunlarını giderme](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking).
>
>

##### <a name="multiple-nics-per-vm"></a>VM başına birden çok NIC

Bir Azure sanal makinesi için birden çok sanal ağ arabirim kartı (vNIC) tanımlayabilirsiniz. Birden çok vNIC 'ye sahip olmak için, örneğin, istemci trafiği bir vNIC üzerinden yönlendirildiği ve arka uç trafiği ikinci bir vNIC üzerinden yönlendirildiği ağ trafiği ayırmayı ayarlamaya başlayabilirsiniz. Sanal makine türüne bağlı olarak, vNIC sayısına göre farklılık açmış farklı sınırlamalar vardır. Aşağıdaki makalelerde tam ayrıntılar, işlevler ve kısıtlamalar bulunabilir:

* [Birden çok NIC ile Windows VM oluşturma][virtual-networks-multiple-nics-windows]
* [Birden çok NIC ile Linux VM oluşturma][virtual-networks-multiple-nics-linux]
* [Şablon kullanarak çoklu NIC VM 'Leri dağıtma][virtual-network-deploy-multinic-arm-template]
* [PowerShell kullanarak çoklu NIC VM 'Leri dağıtma][virtual-network-deploy-multinic-arm-ps]
* [Azure CLı kullanarak çoklu NIC VM 'Leri dağıtma][virtual-network-deploy-multinic-arm-cli]

#### <a name="site-to-site-connectivity"></a>Siteden siteye bağlantı

Şirketler arası, Azure VM 'Leri ve bir saydam ve kalıcı VPN bağlantısıyla bağlanmış şirket Içi bir işlemdir. Azure 'da en yaygın SAP dağıtım deseninin olması beklenmektedir. Varsayım, Azure 'daki SAP örneklerine sahip işlem yordamlarının ve işlemlerin saydam bir şekilde çalışmasını sağlamalıdır. Bu, Azure 'daki bir geliştirme sisteminden, şirket içinde dağıtılan bir test sistemine yapılan değişiklikleri aktarmak için, bu sistemlerden ve SAP aktarım yönetimi sistemi 'ni (TMS) kullanmanın yanı sıra bu sistemlerden de yazdırabilmeniz gerektiği anlamına gelir. Siteden siteye geçici olarak [Bu makalede][vpn-gateway-create-site-to-site-rm-powershell] daha fazla belge bulabilirsiniz

##### <a name="vpn-tunnel-device"></a>VPN tüneli cihazı

Siteden siteye bağlantı oluşturmak için (Azure veri merkezi 'ne şirket içi veri merkezi), bir VPN cihazı edinip yapılandırmanız ya da Windows Server 2012 ile bir yazılım bileşeni olarak tanıtılan yönlendirme ve uzaktan erişim hizmeti (RRAS) kullanmanız gerekir.

* [PowerShell kullanarak siteden siteye VPN bağlantısı olan bir sanal ağ oluşturma][vpn-gateway-create-site-to-site-rm-powershell]
* [Siteden Siteye VPN Gateway bağlantıları için VPN cihazları hakkında][vpn-gateway-about-vpn-devices]
* [VPN Gateway ile ilgili SSS][vpn-gateway-vpn-faq]

![Şirket içi ve Azure arasında siteden siteye bağlantı][planning-guide-figure-600]

Yukarıdaki şekilde iki Azure aboneliği, Azure 'daki sanal ağlarda kullanılmak üzere ayrılmış IP adresi alt aralıklarının olduğunu gösterir. Şirket içi ağdan Azure 'a bağlantı VPN aracılığıyla oluşturulur.

#### <a name="point-to-site-vpn"></a>Noktadan siteye VPN

Noktadan siteye VPN, her istemci makinenin Azure 'a kendi VPN ile bağlanmasını gerektirir. SAP senaryolarında, Noktadan siteye bağlantı, pratik değildir. Bu nedenle, Noktadan siteye VPN bağlantısına başka bir başvuru verilmez.

Daha fazla bilgiyi burada bulabilirsiniz
* [Azure portal’ı kullanarak bir sanal ağa yönelik Noktadan Siteye bağlantı yapılandırma](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [PowerShell'i kullanarak bir sanal ağa yönelik bir Noktadan Siteye bağlantı yapılandırma](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

#### <a name="multi-site-vpn"></a>Çok siteli VPN

Azure Ayrıca günümüzde, bir Azure aboneliği için çok siteli VPN bağlantısı oluşturma olanağı sunar. Daha önce tek bir abonelik, bir siteden siteye VPN bağlantısıyla sınırlandırıldı. Bu sınırlama, tek bir abonelik için çok siteli VPN bağlantılarıyla kapandı. Bu, şirket içi yapılandırmalarda belirli bir abonelik için birden fazla Azure bölgesinin kullanılmasını mümkün kılar.

Daha fazla belge için [Bu makaleye][vpn-gateway-create-site-to-site-rm-powershell] bakın

#### <a name="vnet-to-vnet-connection"></a>VNet 'ten VNet 'e bağlantı

Çok siteli VPN kullanarak, her bir bölgenin ayrı bir Azure Sanal Ağa gelen yapılandırmanız gerekir. Ancak, genellikle farklı bölgelerdeki yazılım bileşenlerinin birbirleriyle iletişim kurması gerekir. İdeal olarak, bu iletişimin bir Azure bölgesinden şirket içine ve diğer Azure bölgesine yönlendirilmemelidir. Azure 'un kısayolu, bir Azure Sanal Ağa gelen bir bölgeden başka bir bölgede barındırılan başka bir Azure sanal ağı arasında bağlantı yapılandırma olanağı sunar. Bu işlevsellik, VNet-VNet bağlantısı olarak adlandırılır. Bu işlevlerle ilgili daha fazla ayrıntı için Buradan bulunabilir: <https://azure.microsoft.com/documentation/articles/vpn-gateway-vnet-vnet-rm-ps/>.

#### <a name="private-connection-to-azure-expressroute"></a>Azure ExpressRoute 'a özel bağlantı

Microsoft Azure ExpressRoute, Azure veri merkezleri ve müşterinin Şirket içi altyapısı ile ya da bir ortak konum ortamında özel bağlantılar oluşturulmasına izin verir. ExpressRoute, çeşitli MPLS (paket anahtarlamalı) VPN sağlayıcıları veya diğer ağ hizmeti sağlayıcıları tarafından sunulur. ExpressRoute bağlantıları ortak İnternet üzerinden geçmemektedir. ExpressRoute bağlantıları, Internet üzerinden tipik bağlantılardan çok sayıda paralel devre, daha hızlı ve düşük gecikme süreleriyle daha yüksek güvenlik, daha fazla güvenilirlik sunar.

Azure ExpressRoute ve teklifleriyle ilgili daha fazla ayrıntıyı burada bulabilirsiniz:

* <https://azure.microsoft.com/documentation/services/expressroute/>
* <https://azure.microsoft.com/pricing/details/expressroute/>
* <https://azure.microsoft.com/documentation/articles/expressroute-faqs/>

Express Route, burada belgelenen bir ExpressRoute devresi üzerinden birden çok Azure aboneliğine izin verebilir

* <https://azure.microsoft.com/documentation/articles/expressroute-howto-linkvnet-arm/>
* <https://azure.microsoft.com/documentation/articles/expressroute-howto-circuit-arm/>

#### <a name="forced-tunneling-in-case-of-cross-premises"></a>Şirketler arası durumlarda Zorlamalı tünel
Şirket içi etki alanlarına siteden siteye, Noktadan siteye veya ExpressRoute aracılığıyla katılan VM 'Ler için, Internet proxy ayarlarının bu VM 'lerdeki tüm kullanıcılar için de dağıtıldığından emin olmanız gerekir. Varsayılan olarak, bu sanal makinelerde çalışan veya internet 'e erişmek için bir tarayıcı kullanan kullanıcılar şirket proxy 'sine gitmez, ancak doğrudan Azure üzerinden İnternet 'e bağlanır. Ancak, proxy ayarı, proxy 'yi denetlemek için yazılım ve hizmetlerin sorumluluğunda olduğundan, trafiği şirket proxy 'si üzerinden yönlendirmek için de% 100 çözümü değildir. VM 'de çalışan yazılım bu işlemleri yapmadığından veya bir yöneticinin ayarları yaptığı takdirde, Internet 'e giden trafik doğrudan Azure üzerinden Internet üzerinden yeniden ayarlanabilir.

Bu tür bir doğrudan internet bağlantısını önlemek için, şirket içi ve Azure arasında siteden siteye bağlantı ile Zorlamalı tünel oluşturmayı yapılandırabilirsiniz. Zorlamalı tünel özelliğinin ayrıntılı açıklaması burada yayımlanır<https://azure.microsoft.com/documentation/articles/vpn-gateway-forced-tunneling-rm/>

ExpressRoute ile Zorlamalı tünel, ExpressRoute BGP eşleme oturumları aracılığıyla varsayılan bir yol tanıtan müşteriler tarafından etkinleştirilir.

#### <a name="summary-of-azure-networking"></a>Azure ağ Özeti

Bu bölümde, Azure ağı hakkında birçok önemli nokta bulunmaktadır. Ana noktaların özeti aşağıda verilmiştir:

* Azure sanal ağları, Azure dağıtımınıza bir ağ yapısı eklemenize olanak tanır. Sanal ağlar birbirlerine karşı yalıtılabilir veya ağ güvenlik grupları yardımı ile VNET 'ler arasındaki trafik denetlenebilir.
* VM 'lere IP adres aralıkları atamak veya VM 'lere sabit IP adresleri atamak için Azure sanal ağları yararlanılabilir olabilir
* Siteden siteye veya Noktadan siteye bağlantı kurmak için önce bir Azure sanal ağı oluşturmanız gerekir
* Bir sanal makine dağıtıldıktan sonra, VM 'ye atanan sanal ağı değiştirmek artık mümkün değildir

### <a name="quotas-in-azure-virtual-machine-services"></a>Azure sanal makine hizmetlerindeki kotalar
Depolama ve ağ altyapısının Azure altyapısında çeşitli hizmetler çalıştıran VM 'Ler arasında paylaşıldığından emin olmanız gerekir. Müşterinin kendi veri merkezlerinde olduğu gibi, bazı altyapı kaynaklarından bazılarının sağlanması bir dereceye kadar gerçekleşecektir. Microsoft Azure platformu, kaynak tüketimini sınırlamak ve tutarlı ve belirleyici performansı korumak için disk, CPU, ağ ve diğer kotaları kullanır.  Farklı VM türlerinin (A5, A6, vb.) disk sayısı, CPU, RAM ve ağ için farklı kotalar vardır.

> [!NOTE]
> SAP tarafından desteklenen VM türlerinin CPU ve bellek kaynakları, konak düğümlerinde önceden ayrılır. Bu, VM dağıtıldıktan sonra konaktaki kaynakların VM türü tarafından tanımlanan şekilde kullanılabilir olduğu anlamına gelir.
>
>

Azure çözümlerinde SAP planlama ve boyutlandırma sırasında, her bir sanal makine boyutu için kotalar göz önünde bulundurulmalıdır. VM kotaları [burada (Linux)][virtual-machines-sizes-linux] ve [burada (Windows)][virtual-machines-sizes-windows]açıklanmaktadır.

Tanımlanan kotalar teorik en yüksek değerleri temsil eder.  Disk başına ıOPS sınırı, küçük IOs (8kb) ile sağlanabilir, ancak büyük olasılıkla büyük IOs (1Mb) ile birlikte ulaşılmayabilir.  IOPS sınırı, tek disk ayrıntı düzeyi üzerinde zorlanır.

Bir SAP sisteminin Azure sanal makine Hizmetleri 'ne uygun olup olmadığını veya mevcut bir sistemin Azure üzerinde sistem dağıtmak üzere farklı şekilde yapılandırılması gerekip gerekmediğini belirlemek için kaba bir karar ağacı olarak, aşağıdaki karar ağacının kullanılması gerekir:

![Azure 'da SAP dağıtma olanağı için karar ağacı][planning-guide-figure-700]

**1. adım**: İle başlamak için en önemli bilgiler, belirli bir SAP sistemine yönelik SAPS gereksinimidir. SAP sistemi 2 katmanlı bir yapılandırmada şirket içinde zaten dağıtılmış olsa bile, SAPS gereksinimlerinin DBMS bölümüne ve SAP uygulama bölümüne ayrılması gerekir. Mevcut sistemlerde, kullanılan donanımla ilgili olan SAPS 'ler, mevcut SAP kıyaslamaları temelinde belirlenebilir veya tahmin edilebilir. Sonuçlar şurada bulunabilir: <https://sap.com/about/benchmark.html>.
Yeni dağıtılan SAP sistemlerinde, sistemin SAPS gereksinimlerini belirleyecek bir boyutlandırma alıştırması yapmanız gerekir.
Ayrıca bkz. Azure 'da SAP boyutlandırma için bu blog ve ekli belge:<https://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

**2. adım**: Mevcut sistemlerde, DBMS sunucusunda g/ç birimi ve saniye başına g/ç işlemleri ölçülmelidir. Yeni planlanmış sistemlerde, yeni sistem için boyutlandırma alıştırması, DBMS tarafında g/ç gereksinimlerine ilişkin kaba fikirler de vermelidir. Emin değilseniz, sonunda bir kavram kanıtı uygulamanız gerekir.

**Adım 3**: DBMS sunucusunun SAPS gereksinimini Azure 'un sağladığı farklı VM türleri ile karşılaştırın. Farklı Azure VM türlerinin SAPS 'si ile ilgili bilgiler SAP Note [1928533]' de belgelenmiştir. Veritabanı katmanı, dağıtımların çoğunda ölçeklendirolmayan bir SAP NetWeaver sistemindeki katman olduğundan, odak DBMS VM 'sinde olmalıdır. Buna karşılık, SAP uygulama katmanı da genişletilebilir. SAP tarafından desteklenen Azure VM türlerinden hiçbiri gerekli SAPS 'leri sunabiliyorsa, planlı SAP sisteminin iş yükü Azure üzerinde çalıştırılamaz. Sistemi şirket içinde dağıtmanız ya da sistem için iş yükü birimini değiştirmeniz gerekir.

**4. adım**: [Burada (Linux)][virtual-machines-sizes-linux] ve [burada (Windows)][virtual-machines-sizes-windows]belgelendiği gibi, Azure Standart depolama veya Premium Depolama kullanıp kullanmayacağınızı bağımsız olarak disk başına bir IOPS kotası uygular. VM türüne bağlı olarak, birbirine bağlanan veri disklerinin sayısı farklılık gösterir. Sonuç olarak, her farklı VM türüyle elde edilebilir maksimum ıOPS numarasını hesaplayabilirsiniz. Veritabanı dosyası düzenine bağlı olarak, Konuk işletim sisteminde tek bir birim olacak şekilde disk şeridi oluşturabilirsiniz. Ancak, dağıtılmış bir SAP sisteminin geçerli ıOPS birimi, Azure 'un en büyük VM türüne ait hesaplanan sınırları aşarsa ve daha fazla bellekle telafi şansı yoksa, SAP sisteminin iş yükü ciddi bir şekilde etkilenebilir. Bu gibi durumlarda, Azure 'da sistemi dağıtmayan bir noktaya basabilirsiniz.

**5. adım**: Özellikle, 2 katmanlı yapılandırmalarda şirket içinde dağıtılan SAP sistemlerinde, sistemin Azure 'da 3 katmanlı bir yapılandırmada yapılandırılması gerekebilir. Bu adımda, SAP uygulama katmanında bir bileşen olup olmadığını denetlemeniz gerekir, bu, genişleme ve farklı Azure VM türleri sunan CPU ve bellek kaynaklarına sığmıyor. Böyle bir bileşen varsa, SAP sistemi ve iş yükü Azure 'a dağıtılamaz. Ancak SAP uygulama bileşenlerini birden çok Azure VM 'ye ölçeklendirebiliyorsanız, sistem Azure 'a dağıtılabilir.

**Adım 6**: DBMS ve SAP uygulama katmanı bileşenleri Azure VM 'lerinde çalıştırılabiliyorsanız, yapılandırmanın şu şekilde tanımlanması gerekir:

* Azure VM sayısı
* Bireysel bileşenler için VM türleri
* DBMS VM 'de yeterli ıOPS sağlamak için VHD sayısı

## <a name="managing-azure-assets"></a>Azure varlıklarını yönetme

### <a name="azure-portal"></a>Azure portal

Azure portal, Azure VM dağıtımlarını yönetmek için üç arabirimden biridir. Görüntülerden VM dağıtımı gibi temel yönetim görevleri, Azure portal aracılığıyla yapılabilir. Ayrıca, depolama hesapları, sanal ağlar ve diğer Azure bileşenlerinin oluşturulması de Azure portal görevlerdir. Ancak, VHD 'leri Şirket içinden Azure 'a yükleme veya Azure 'da bir VHD kopyalama gibi işlevler, üçüncü taraf araçlar ya da PowerShell veya CLı aracılığıyla yönetim gerektiren görevlerdir.

![Microsoft Azure portal-sanal makineye genel bakış][planning-guide-figure-800]


Sanal makine örneği için yönetim ve yapılandırma görevlerinin Azure portal içinden mümkündür.

Bir sanal makineyi yeniden başlatma ve kapatma yanında, sanal makine örneği için veri diskleri ekleyebilir, ayırabilir ve oluşturabilir, görüntü hazırlığı örneğini yakalayabilir ve sanal makine örneğinin boyutunu yapılandırabilirsiniz.

Azure portal, VM 'Leri ve diğer birçok Azure hizmetini dağıtmak ve yapılandırmak için temel işlevleri sağlar. Ancak, Azure portal tüm kullanılabilir işlevler kapsamında değildir. Azure portal, şunun gibi görevleri gerçekleştirmek mümkün değildir:

* VHD 'leri Azure 'a yükleme
* VM 'Leri kopyalama


### <a name="management-via-microsoft-azure-powershell-cmdlets"></a>Microsoft Azure PowerShell cmdlet 'leri aracılığıyla yönetim

Windows PowerShell, Azure 'da daha fazla sayıda sistem dağıtan müşteriler tarafından yaygın olarak benimsenen güçlü ve genişletilebilir bir çerçevedir. PowerShell cmdlet 'lerinin bir masaüstü, dizüstü bilgisayar veya adanmış Yönetim istasyonuna yüklenmesinden sonra, PowerShell cmdlet 'leri uzaktan çalıştırılabilir.

Azure PowerShell cmdlet 'lerinin kullanımı için yerel bir masaüstü/dizüstü bilgisayar etkinleştirme ve [Bu makalede][powershell-install-configure]bunların Azure abonelikleri ile kullanım için nasıl yapılandırılacağı açıklanmaktadır.

Azure PowerShell cmdlet 'lerinin nasıl yükleneceği, güncelleştirilmesi ve yapılandırılacağına ilişkin daha ayrıntılı adımlar, [Dağıtım Kılavuzu 'nun bu][deployment-guide-4.1]bölümünde bulunabilir.

Şimdiye kadar müşteri deneyimi, PowerShell 'in (PS) VM 'Leri dağıtmaya ve VM dağıtımında özel adımlar oluşturmaya yönelik daha güçlü bir araçtır. Azure 'da SAP örnekleri çalıştıran tüm müşteriler, Azure portal yönetim görevlerini tamamlamak için PS cmdlet 'lerini kullanıyor ya da Azure 'da dağıtımlarını yönetmek için yalnızca PS cmdlet 'lerini kullanıyor. Azure 'a özgü cmdlet 'ler, Windows yöneticilerinin 2000 ' den fazla cmdlet 'i ile aynı adlandırma kuralını paylaştığından, bu cmdlet 'lerin bu cmdlet 'leri kullanması kolay bir görevdir.

Bkz. örnek:<https://blogs.technet.com/b/keithmayer/archive/2015/07/07/18-steps-for-end-to-end-iaas-provisioning-in-the-cloud-with-azure-resource-manager-arm-powershell-and-desired-state-configuration-dsc.aspx>


SAP için Azure uzantısı 'nın dağıtımı (Bu belgedeki [SAP Için Azure uzantısı][planning-guide-9.1] bölümüne bakın) yalnızca POWERSHELL veya CLI aracılığıyla yapılabilir. Bu nedenle, Azure 'da SAP NetWeaver sistemi dağıtma veya yönetme sırasında PowerShell veya CLı 'yi ayarlamak ve yapılandırmak zorunludur.  

Azure daha fazla işlevsellik sağladığından cmdlet 'lerin güncelleştirilmesini gerektiren yeni PS cmdlet 'leri eklenecektir. Bu nedenle, cmdlet 'lerin yeni bir sürümü için ayda <https://azure.microsoft.com/downloads/> en az bir kez Azure indirme sitesini denetlemek mantıklı olur. Yeni sürüm eski sürümün üzerine yüklenir.

Azure ile ilgili PowerShell komutlarının genel listesi için buraya bakın: <https://docs.microsoft.com/powershell/azure/overview>.

### <a name="management-via-microsoft-azure-cli-commands"></a>Microsoft Azure CLı komutları aracılığıyla yönetim

Linux kullanan ve Azure kaynaklarını yönetmek isteyen müşteriler için bir seçenek olmayabilir. Microsoft, Azure CLı 'yi alternatif olarak sunmaktadır.
Azure CLı, Azure platformuyla çalışmaya yönelik bir dizi açık kaynak ve platformlar arası komut sunar. Azure CLı, Azure portal bulunan işlevlerin çoğunu sağlar.

Yükleme, yapılandırma ve Azure görevlerini gerçekleştirmek için CLı komutlarının nasıl kullanılacağı hakkında bilgi için bkz.

* [Azure klasik CLı 'yı yükler][xplat-cli]
* [Dağıtmak ve Azure Resource Manager şablonları ve Azure CLI kullanarak sanal makineleri yönetme] [../../linux/create-ssh-secured-vm-from-template.md]
* [Azure Resource Manager ile Mac, Linux ve Windows için klasik Azure CLı 'yi kullanma][xplat-cli-azure-resource-manager]

Ayrıca, Azure CLı 'yı kullanarak SAP için Azure uzantısı 'nı dağıtmak üzere [dağıtım kılavuzundaki][planning-guide] [Linux VM 'LERI için Azure CLI][deployment-guide-4.5.2] bölümünü okuyun.

## <a name="different-ways-to-deploy-vms-for-sap-in-azure"></a>Azure 'da SAP için VM 'Leri dağıtmanın farklı yolları

Bu bölümde, Azure 'da VM dağıtmanın farklı yollarını öğreneceksiniz. Ek hazırlık yordamları ve Azure 'da VHD 'lerin ve VM 'lerin işlenmesi bu bölümde ele alınmıştır.

### <a name="deployment-of-vms-for-sap"></a>SAP için VM dağıtımı

Microsoft Azure, VM 'Leri ve ilişkili diskleri dağıtmanın birden çok yolunu sunar. Bu nedenle, VM 'lerin hazırlıkları dağıtım yöntemine bağlı olarak farklı olabileceğinden farklılıkları anlamak önemlidir. Genel olarak, aşağıdaki senaryolara göz atalım:

#### <a name="4d175f1b-7353-4137-9d2f-817683c26e53"></a>Bir VM 'yi Şirket içinden, genelleştirilmiş olmayan bir disk ile Azure 'a taşıma

Belirli bir SAP sistemini Şirket içinden Azure 'a taşımayı planlarsınız. Bu işlem, işletim sistemini, SAP Ikililerini ve DBMS ikililerini içeren VHD 'yi, DBMS 'nin veri ve günlük dosyalarıyla Azure 'a karşıya yükleyerek yapılabilir. [Aşağıdaki senaryo #2][planning-guide-5.1.2]aksine, Azure VM 'de ana bilgisayar adı, SAP SID 'SI ve SAP Kullanıcı hesaplarını şirket içi ortamda yapılandırıldıklarında saklayın. Bu nedenle, görüntüyü genelleştirerek gerekli değildir. Şirket içi hazırlama adımları ve genelleştirilmiş VM 'Ler ya da VHD 'lerin Azure 'a yüklenmesi için, [BIR VM 'yi Şirket içinden bu belgenin Genelleştirilmiş olmayan bir disketiyle Azure 'a taşımaya yönelik bölüm hazırlama][planning-guide-5.2.1] bölümüne bakın. Bölüm [senaryosunu okuyun 3: Azure 'da böyle bir görüntüyü dağıtmanın ayrıntılı adımları için][deployment-guide-3.4] [dağıtım kılavuzunda][deployment-guide] SAP ile Genelleştirilmiş bir Azure VHD kullanarak bir VM 'yi Şirket içinden taşıma.

#### <a name="e18f7839-c0e2-4385-b1e6-4538453a285c"></a>Müşteriye özgü bir görüntüyle VM dağıtma

İşletim sistemi veya DBMS sürümünüzün belirli düzeltme eki gereksinimleri nedeniyle, Azure Marketi 'ndeki sunulan görüntüler gereksinimlerinize uygun olmayabilir. Bu nedenle, daha sonra birkaç kez dağıtılabilecek özel işletim sistemi/DBMS VM görüntünüzü kullanarak bir VM oluşturmanız gerekebilir. Bu tür özel bir görüntüyü yineleme için hazırlamak üzere aşağıdaki öğelerin göz önünde bulundurulması gerekir:

---
> ![Windows][Logo_Windows] Windows
>
> Daha fazla ayrıntı için buraya bakın: <https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed>Windows ayarları (Windows SID ve ana bilgisayar adı gibi), Sysprep komutu aracılığıyla şirket içi VM 'de soyut/Genelleştirilmiş olmalıdır.
>
>
> ![Linux][Logo_Linux] Linux
>
> Bir VHD 'yi Azure 'a yüklenecek şekilde hazırlamak için [SUSE][virtual-machines-linux-create-upload-vhd-suse], [Red Hat][virtual-machines-linux-redhat-create-upload-vhd]veya [Oracle Linux][virtual-machines-linux-create-upload-vhd-oracle]için bu makalelerde açıklanan adımları izleyin.
>
>

---
SAP içeriğini şirket içi sanal makinenize zaten yüklediyseniz (özellikle de 2 katmanlı sistemler için), SAP yazılım sağlama Yöneticisi (SAP) tarafından desteklenen örnek yeniden adlandırma yordamı aracılığıyla Azure VM dağıtımı sonrasında SAP sistem ayarlarını uyarlayabilirsiniz. Note [1619720]). Şirket içi hazırlama adımları ve genelleştirilmiş bir VM 'yi Azure 'a yüklemek için, [BIR VM 'yi müşterilere özgü bir görüntüyle dağıtmak][planning-guide-5.2.2] ve şirket [içinden bir VHD][planning-guide-5.3.2] 'yi bu belgenin Azure 'A yüklemek için bölüm hazırlığı konusuna bakın. Bölüm [senaryosunu okuyun 2: Azure 'da böyle bir görüntüyü dağıtmaya yönelik ayrıntılı adımlar][deployment-guide-3.3] için [dağıtım kılavuzunda][deployment-guide] bir VM 'yi özel bir görüntüyle dağıtma.

#### <a name="deploying-a-vm-out-of-the-azure-marketplace"></a>Azure Marketi 'Nden bir VM dağıtma

VM 'nizi dağıtmak için Azure Marketi 'nden bir Microsoft veya üçüncü taraf tarafından sağlanmış bir VM görüntüsü kullanmak istersiniz. VM 'nizi Azure 'da dağıttıktan sonra, SAP yazılımını ve/veya DBMS 'yi şirket içi bir ortamda yaptığınız gibi VM 'nizin içine yüklemek için aynı kılavuz ve araçları izleyin. Daha ayrıntılı dağıtım açıklaması için bkz. bölüm [senaryosu 1: ][deployment-guide-3.2] [Dağıtım kılavuzunda][deployment-guide]SAP için Azure Marketi 'nden bir VM dağıtma.

### <a name="6ffb9f41-a292-40bf-9e70-8204448559e7"></a>Azure için SAP ile VM 'Leri hazırlama

VM 'Leri Azure 'a yüklemeden önce VM 'Lerin ve VHD 'lerin belirli gereksinimleri karşıladıklarından emin olmanız gerekir. Kullanılan dağıtım yöntemine bağlı olarak küçük farklılıklar vardır.

#### <a name="1b287330-944b-495d-9ea7-94b83aff73ef"></a>Genelleştirilmiş olmayan bir diskle bir VM 'yi Şirket içinden Azure 'a taşımaya yönelik hazırlık

Ortak dağıtım yöntemi, şirket içi bir SAP sistemini çalıştıran mevcut bir VM 'yi Azure 'a taşımadır. VM 'deki bu VM ve SAP sisteminin aynı ana bilgisayar adını ve olasılıkla aynı SAP SID 'sini kullanarak Azure 'da çalışması gerekir. Bu durumda, VM 'nin Konuk işletim sistemi birden çok dağıtım için genelleştirilmemelidir. Şirket içi ağ Azure 'a genişletilmişse (bkz. [tek veya birden fazla sap VM 'lerinin, bu belgedeki şirket içi ağla tam olarak tümleştirildiği gereksinimle][planning-guide-2.2] ), aynı etki alanı bile hesaplar, şirket içinde daha önce kullanılan VM 'ler dahilinde kullanılabilir.

Kendi Azure VM diskinizi hazırlarken gereksinimler şunlardır:

* Başlangıçta işletim sistemini içeren VHD en fazla 127GB boyutunda olabilir. Bu sınırlama Mart 2015 ' ün sonunda ortadan kaldırıldı. Artık işletim sistemini içeren VHD, diğer Azure Storage barındırılan VHD 'ler gibi en fazla 1 TB boyutunda olabilir.
* Sabit VHD biçiminde olması gerekir. VHDx biçimindeki Dinamik VHD 'ler veya VHD 'ler henüz Azure 'da desteklenmiyor. VHD 'yi PowerShell Command, veya CLı ile karşıya yüklediğinizde Dinamik VHD 'ler statik VHD 'lere dönüştürülecek
* VM 'ye bağlanan ve VM 'nin Azure 'da yeniden bağlanması gereken VHD 'ler, aynı zamanda bir sabit VHD biçiminde olmalıdır. Veri disklerinin boyut sınırları için [Bu makaleyi (Linux)](../../linux/managed-disks-overview.md) ve [Bu makaleyi (Windows)](../../windows/managed-disks-overview.md)okuyun. VHD 'yi PowerShell Command, veya CLı ile karşıya yüklediğinizde Dinamik VHD 'ler statik VHD 'lere dönüştürülecek
* Yönetim ayrıcalıklarına sahip başka bir yerel hesap ekleyin, bu, Microsoft desteği tarafından kullanılabilen veya VM dağıtılmadan ve daha uygun kullanıcıların kullanılabilmesi için içinde çalışacağı hizmet ve uygulamaların bağlamı olarak atanabileceği bir yerel hesap ekleyin.
* Belirli dağıtım senaryosu için gerekli olabilecek diğer yerel hesapları ekleyin.

---
> ![Windows][Logo_Windows] Windows
>
> Bu senaryoda VM 'yi karşıya yüklemek ve Azure 'a dağıtmak için VM 'nin Genelleştirme (Sysprep) gerekmez.
> D:\ sürücüsünün olduğundan emin olun kullanılmaz.
> Bu belgedeki [ekli diskler için otomatik bağlama ayarı][planning-guide-5.5.3] bölümünde açıklandığı gibi ekli diskler için disk otomatik bağlama ayarlayın.
>
> ![Linux][Logo_Linux] Linux
>
> Bu senaryoda VM 'yi karşıya yüklemek ve Azure 'a dağıtmak için VM 'nin Genelleştirme (waagent-deprovision) gerekmez.
> /Mnt/Resource ' ın kullanılmadığından ve tüm disklerin UUID aracılığıyla bağlandığından emin olun. İşletim sistemi diski için, önyükleme yükleyicisi girişinin UUID tabanlı bağlama de yansıttığından emin olun.
>
>

---
#### <a name="57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3"></a>SAP için müşteriye özgü bir görüntüyle VM dağıtmaya hazırlanma

Genelleştirilmiş bir işletim sistemi içeren VHD dosyaları, Azure depolama hesaplarındaki kapsayıcılar veya yönetilen disk görüntüleri olarak depolanır. Bu tür bir görüntüden yeni bir VM 'yi, bölüm [senaryosu 2 ' de açıklandığı gibi, dağıtım şablonu dosyalarınızda bir kaynak olarak VHD veya yönetilen disk görüntüsüne başvurarak dağıtabilirsiniz: ][deployment-guide-3.3] [Dağıtım kılavuzunun][deployment-guide]SAP için özel bir görüntüyle VM dağıtma.

Kendi Azure VM görüntünüzü hazırlarken gereksinimler şunlardır:

* Başlangıçta işletim sistemini içeren VHD en fazla 127GB boyutunda olabilir. Bu sınırlama Mart 2015 ' ün sonunda ortadan kaldırıldı. Artık işletim sistemini içeren VHD, diğer Azure Storage barındırılan VHD 'ler gibi en fazla 1 TB boyutunda olabilir.
* Sabit VHD biçiminde olması gerekir. VHDx biçimindeki Dinamik VHD 'ler veya VHD 'ler henüz Azure 'da desteklenmiyor. VHD 'yi PowerShell Command, veya CLı ile karşıya yüklediğinizde Dinamik VHD 'ler statik VHD 'lere dönüştürülecek
* VM 'ye bağlanan ve VM 'nin Azure 'da yeniden bağlanması gereken VHD 'ler, aynı zamanda bir sabit VHD biçiminde olmalıdır. Veri disklerinin boyut sınırları için lütfen [Bu makaleyi (Linux)](../../windows/managed-disks-overview.md) ve [Bu makaleyi (Windows)](../../linux/managed-disks-overview.md) okuyun. VHD 'yi PowerShell Command, veya CLı ile karşıya yüklediğinizde Dinamik VHD 'ler statik VHD 'lere dönüştürülecek
* Belirli dağıtım senaryosu için gerekli olabilecek diğer yerel hesapları ekleyin.
* Görüntüde bir SAP NetWeaver yüklemesi varsa ve Azure dağıtımı noktasındaki asıl adından ana bilgisayar adının yeniden adlandırılması büyük olasılıkla, SAP yazılım sağlama Yöneticisi DVD 'sinin en son sürümlerini şablona kopyalamanız önerilir. Bu, değiştirilen ana bilgisayar adını uyarlamak ve/veya yeni bir kopya başlatıldıktan hemen sonra dağıtılan VM görüntüsü içindeki SAP sisteminin SID 'sini değiştirmek için SAP tarafından girilen yeniden adlandırma işlevselliğini kolayca kullanmanıza imkan sağlar.

---
> ![Windows][Logo_Windows] Windows
>
> D:\ sürücüsünün olduğundan emin olun , bu belgedeki [ekli diskler için otomatik bağlama ayarı][planning-guide-5.5.3] bölümünde açıklandığı gibi ekli diskler için disk otomatik bağlama ayarla kullanılmaz.
>
> ![Linux][Logo_Linux] Linux
>
> /Mnt/Resource ' ın kullanılmadığından ve tüm disklerin UUID aracılığıyla bağlandığından emin olun. İşletim sistemi diski için, önyükleme yükleyicisi girişinin UUID tabanlı bağlama de yansıttığından emin olun.
>
>

---
* SAP GUI (Yönetim ve kurulum amaçları için) bu tür bir şablonda önceden yüklenmiş olabilir.
* Bu yazılım VM 'nin yeniden adlandırılmasına çalışabilecek sürece VM 'Leri şirketler arası senaryolarda başarıyla çalıştırmak için gereken diğer yazılımlar yüklenebilir.

VM 'nin genel olması yeterince hazırlanmışsa ve son olarak, hedeflenen Azure dağıtım senaryosunda hesap/kullanıcılardan bağımsız olarak, bu tür bir görüntüyü genelleştirmeye yönelik son hazırlık adımı yürütülür.

##### <a name="generalizing-a-vm"></a>VM 'yi Genelleştirme
---
> ![Windows][Logo_Windows] Windows
>
> Son adım, bir yönetici hesabıyla bir VM 'de oturum açmak için kullanılır. *Yönetici*olarak bir Windows komut penceresi açın. %Windir%\Windows\System32\Sysprep adresine gidin ve Sysprep. exe ' yi yürütün.
> Küçük bir pencere görünür. **Genelleştir** seçeneğini denetlemek önemlidir (varsayılan olarak işaretli değildir) ve varsayılan ' reboot ' olan ' reboot ' olan kapalı seçeneğini ' kapalı ' olarak değiştirir. Bu yordam, Sysprep işleminin bir VM 'nin Konuk işletim sisteminde şirket içinde yürütüldüğünü varsayar.
> Yordamı Azure 'da zaten çalışan bir VM ile gerçekleştirmek istiyorsanız, [Bu makalede](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)açıklanan adımları izleyin.
>
> ![Linux][Logo_Linux] Linux
>
> [Kaynak Yöneticisi şablonu olarak kullanmak üzere Linux sanal makinesi yakalama][capture-image-linux-step-2-create-vm-image]
>
>

---
### <a name="transferring-vms-and-vhds-between-on-premises-to-azure"></a>VM 'Leri ve VHD 'Leri Şirket içinden Azure 'a aktarma
VM görüntülerini ve disklerini Azure 'a yükleme Azure portal aracılığıyla mümkün olmadığından, Azure PowerShell cmdlet 'lerini veya CLı 'yi kullanmanız gerekir. ' AzCopy ' aracının kullanılması başka bir olasılık. Araç, VHD 'leri şirket içi ve Azure arasında (her iki yönde) kopyalayabilir. Ayrıca, VHD 'leri Azure bölgeleri arasında kopyalayabilir. AzCopy 'in indirilmesi ve kullanımı için lütfen [Bu belgelere][storage-use-azcopy] bakın.

Üçüncü bir alternatif, çeşitli üçüncü taraf GUI odaklı araçları kullanmaktır. Ancak, bu araçların Azure sayfa Bloblarını desteklemesini sağlayın. Bizim amaçlarımız için, Azure Sayfa Blobu deposu kullanmamız gerekir (farklar burada açıklanmıştır: <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>). Ayrıca, Azure tarafından sunulan araçlar, karşıya yüklenmesi gereken VM 'Leri ve VHD 'Leri sıkıştırmak için etkilidir. Bu, sıkıştırmanın içindeki bu verimlilik karşıya yükleme süresini azalttığından (aynı zamanda şirket içi tesis ve hedeflenen Azure dağıtım bölgesinden internet 'e karşıya yükleme bağlantısına bağlı olarak farklılık gösterir) önemlidir. Avrupa konumundan ABD tabanlı Azure veri merkezlerine bir VM veya VHD 'yi karşıya yüklemek, Avrupa Azure veri merkezlerine aynı VM 'Leri/VHD 'Leri karşıya yüklemeden daha uzun sürer.

#### <a name="a43e40e6-1acc-4633-9816-8f095d5a7b6a"></a>Şirket içinden Azure 'a VHD yükleme
Mevcut bir VM veya VHD 'yi, şirket içi ağdan, bu belgenin [Genelleştirilmiş olmayan bir disketiyle BIR VM 'yi şirket Içinden Azure 'a taşımaya yönelik bölüm hazırlığı][planning-guide-5.2.1] bölümünde listelenen gereksinimlere uyması gerekir.

Bu tür bir VM 'nin genelleştirilmesi gerekmez ve şirket içi tarafında kapatıldıktan sonra sahip olduğu durum ve şekle yüklenebilir. Aynı işlem, herhangi bir işletim sistemi içermeyen ek VHD 'ler için de geçerlidir.

##### <a name="uploading-a-vhd-and-making-it-an-azure-disk"></a>Bir VHD 'YI karşıya yükleme ve bir Azure diski yapma
Bu durumda, bir VHD 'yi bir işletim sistemi ile veya olmadan karşıya yüklemek ve bir sanal makineye bir veri diski olarak bağlamak veya işletim sistemi diski olarak kullanmak istiyoruz. Bu, çok adımlı bir işlemdir

**PowerShell**

* *Connect-AzAccount* ile aboneliğinizde oturum açın
* *Set-azcontext* ve, SubscriptionID veya subscriptionName parametreleri ile bağlamınızın aboneliğini ayarlayın-bkz.<https://docs.microsoft.com/powershell/module/az.accounts/set-Azcontext>
* *Add-azvhd* ile VHD 'Yi bir Azure depolama hesabına yükleyin-bkz.<https://docs.microsoft.com/powershell/module/az.compute/add-Azvhd>
* Seçim *New-AzDisk* ile VHD 'Den yönetilen disk oluşturma-Bkz.<https://docs.microsoft.com/powershell/module/az.compute/new-Azdisk>
* Yeni bir VM yapılandırmasının işletim sistemi diskini, *set-AzVMOSDisk* ile VHD veya yönetilen diske ayarlama-Bkz.<https://docs.microsoft.com/powershell/module/az.compute/set-Azvmosdisk>
* *New-azvm* ile VM yapılandırmadan yenı bir VM oluşturma-Bkz.<https://docs.microsoft.com/powershell/module/az.compute/new-Azvm>
* *Add-AzVMDataDisk* ile yenı bir VM 'ye veri diski ekleme-Bkz.<https://docs.microsoft.com/powershell/module/az.compute/add-Azvmdatadisk>

**Azure CLI**

* *Az Login* ile aboneliğinizde oturum açın
* *Az Account set- `<subscription name or id` -Subscription >*  ile aboneliğinizi seçin
* VHD 'YI *az Storage blob upload* ile karşıya yükleme-bkz [. Azure CLI ile Azure depolama ile kullanma][storage-azure-cli]
* Seçim VHD 'den bir yönetilen disk oluşturma *az disk Create* -See https://docs.microsoft.com/cli/azure/disk
* Karşıya yüklenen VHD veya yönetilen diski işletim sistemi diski olarak belirterek *az VM Create* ve Parameter *--Attach-OS-disk* olan yeni bir VM oluşturun
* *Az VM disk Attach* ve Parameter ile yenı bir VM 'ye veri diski ekleme *--Yeni*

**Şablon**

* VHD 'YI PowerShell veya Azure CLı ile karşıya yükleme
* Seçim PowerShell, Azure CLı veya Azure portal ile VHD 'den yönetilen disk oluşturma
* VM 'yi [Bu örnek JSON](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json) şablonunda gösterildiği gıbı, VHD 'ye başvuran bir JSON şablonuyla veya [Bu örnek JSON şablonunda](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json)gösterildiği gibi yönetilen diskleri kullanarak dağıtın.

#### <a name="deployment-of-a-vm-image"></a>VM görüntüsü dağıtımı
Mevcut bir VM 'yi veya VHD 'yi, şirket içi ağdan yüklemek için, bir VM veya VHD 'nin bu belgenin [SAP için müşteriye özgü bir görüntüyle BIR VM dağıtmaya yönelik bölüm hazırlığı][planning-guide-5.2.2] bölümünde listelenen gereksinimleri karşılaması gerekir.

* VM 'nizi genelleştirmek için Windows 'ta *Sysprep* veya Linux 'ta *waagent-deprovision* komutunu kullanın-bkz. Windows için [Sysprep teknik başvurusu](https://technet.microsoft.com/library/cc766049.aspx) veya Linux Için [Kaynak Yöneticisi şablonu olarak kullanılacak bir Linux sanal makinesini yakalama][capture-image-linux-step-2-create-vm-image]
* *Connect-AzAccount* ile aboneliğinizde oturum açın
* *Set-azcontext* ve, SubscriptionID veya subscriptionName parametreleri ile bağlamınızın aboneliğini ayarlayın-bkz.<https://docs.microsoft.com/powershell/module/az.accounts/set-Azcontext>
* *Add-azvhd* ile VHD 'Yi bir Azure depolama hesabına yükleyin-bkz.<https://docs.microsoft.com/powershell/module/az.compute/add-Azvhd>
* Seçim *New-Azımage* ile VHD 'Den yönetilen disk görüntüsü oluşturma-Bkz.<https://docs.microsoft.com/powershell/module/az.compute/new-Azimage>
* Yeni bir VM yapılandırmasının işletim sistemi diskini şu şekilde ayarlayın
  * *Set-AzVMOSDisk-Sourceımageuri-CreateOption FromImage* ile VHD-bkz.<https://docs.microsoft.com/powershell/module/az.compute/set-Azvmosdisk>
  * Yönetilen disk görüntüsü *set-Azvmsourceımage* -bkz.<https://docs.microsoft.com/powershell/module/az.compute/set-Azvmsourceimage>
* *New-azvm* ile VM yapılandırmadan yenı bir VM oluşturma-Bkz.<https://docs.microsoft.com/powershell/module/az.compute/new-Azvm>

**Azure CLI**

* VM 'nizi genelleştirmek için Windows 'ta *Sysprep* veya Linux 'ta *waagent-deprovision* komutunu kullanın-bkz. Windows için [Sysprep teknik başvurusu](https://technet.microsoft.com/library/cc766049.aspx) veya Linux Için [Kaynak Yöneticisi şablonu olarak kullanılacak bir Linux sanal makinesini yakalama][capture-image-linux-step-2-create-vm-image]
* *Az Login* ile aboneliğinizde oturum açın
* *Az Account set- `<subscription name or id` -Subscription >*  ile aboneliğinizi seçin
* VHD 'YI *az Storage blob upload* ile karşıya yükleme-bkz [. Azure CLI ile Azure depolama ile kullanma][storage-azure-cli]
* Seçim *Az Image Create* -See ile VHD 'Den yönetilen disk görüntüsü oluşturma https://docs.microsoft.com/cli/azure/image
* Karşıya yüklenen VHD veya yönetilen disk görüntüsünü *az VM Create* ve Parameter *--Image* ile işletim sistemi diski olarak belirten yeni bir VM oluşturun

**Şablon**

* VM 'nizi genelleştirmek için Windows 'ta *Sysprep* veya Linux 'ta *waagent-deprovision* komutunu kullanın-bkz. Windows için [Sysprep teknik başvurusu](https://technet.microsoft.com/library/cc766049.aspx) veya Linux Için [Kaynak Yöneticisi şablonu olarak kullanılacak bir Linux sanal makinesini yakalama][capture-image-linux-step-2-create-vm-image]
* VHD 'YI PowerShell veya Azure CLı ile karşıya yükleme
* Seçim PowerShell, Azure CLı veya Azure portal ile VHD 'den yönetilen bir disk görüntüsü oluşturun
* [Bu örnek JSON](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json) şablonunda gösterildiği gibi, görüntü VHD 'sine başvuran bir JSON şablonuyla VM 'yi dağıtın veya [Bu örnek JSON şablonunda](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json)gösterildiği gibi yönetilen disk görüntüsünü kullanın.

#### <a name="downloading-vhds-or-managed-disks-to-on-premises"></a>VHD 'leri veya yönetilen diskleri şirket içine indirme
Hizmet olarak Azure altyapısı, yalnızca VHD 'leri ve SAP sistemlerini karşıya yükleyebilecekler için tek yönlü bir cadde değildir. SAP sistemlerini Azure 'dan şirket içi dünyaya geri taşıyabilirsiniz.

İndirme sırasında VHD 'ler veya yönetilen diskler etkin olamaz. VM 'lere bağlanan diskler indirilirken bile, sanal makinenin kapatılması ve serbest bırakılmasının olması gerekir. Yalnızca bir veritabanı içeriğini indirmek istiyorsanız, şirket içinde yeni bir sistem kurmak ve indirme sırasında ve Azure 'daki sistemin sistemde hala çalışır durumda olduğu kabul edilebilir olması durumunda kullanılması gerekir , bir diskte sıkıştırılmış bir veritabanı yedeklemesi gerçekleştirerek ve işletim sistemi temel VM 'yi indirmek yerine yalnızca o diski indirerek uzun bir kesinti yaşamadan kaçınabilirsiniz.

#### <a name="powershell"></a>Powershell

* Yönetilen disk indiriliyor  
  Önce yönetilen diskin temel blobuna erişim almanız gerekir. Ardından, temel alınan blobu yeni bir depolama hesabına kopyalayabilir ve blobu bu depolama hesabından indirebilirsiniz.

  ```powershell
  $access = Grant-AzDiskAccess -ResourceGroupName <resource group> -DiskName <disk name> -Access Read -DurationInSecond 3600
  $key = (Get-AzStorageAccountKey -ResourceGroupName <resource group> -Name <storage account name>)[0].Value
  $destContext = (New-AzStorageContext -StorageAccountName <storage account name -StorageAccountKey $key)
  Start-AzStorageBlobCopy -AbsoluteUri $access.AccessSAS -DestContainer <container name> -DestBlob <blob name> -DestContext $destContext
  # Wait for blob copy to finish
  Get-AzStorageBlobCopyState -Container <container name> -Blob <blob name> -Context $destContext
  Save-AzVhd -SourceUri <blob in new storage account> -LocalFilePath <local file path> -StorageKey $key
  # Wait for download to finish
  Revoke-AzDiskAccess -ResourceGroupName <resource group> -DiskName <disk name>
  ```

* VHD indirme  
  SAP sistemi durdurulduğunda ve VM kapatıldıktan sonra, VHD disklerini şirket içi dünyaya geri yüklemek için şirket içi hedefte Save-AzVhd PowerShell cmdlet 'ini kullanabilirsiniz. Bunu yapmak için, Azure portal ' depolama bölümünde bulabileceğiniz VHD 'nin URL 'SI gerekir (depolama hesabına ve VHD 'nin oluşturulduğu depolama kapsayıcısına gitmeniz gerekir) ve VHD 'nin nereye kopyalanabilmesi gerektiğini bilmeniz gerekir.

  Ardından, kaynak URI 'sini indirmek üzere VHD 'nin URL 'si olarak ve LocalFilePath öğesini VHD 'nin fiziksel konumu (adı dahil) olarak tanımlayarak komutunu kullanabilirsiniz. Komut şu şekilde görünebilir:

  ```powerhell
  Save-AzVhd -ResourceGroupName <resource group name of storage account> -SourceUri http://<storage account name>.blob.core.windows.net/<container name>/sapidedata.vhd -LocalFilePath E:\Azure_downloads\sapidesdata.vhd
  ```

  Save-AzVhd cmdlet 'i hakkında daha fazla bilgi için buraya <https://docs.microsoft.com/powershell/module/az.compute/save-Azvhd>bakın.

#### <a name="azure-cli"></a>Azure CLI
* Yönetilen disk indiriliyor  
  Önce yönetilen diskin temel blobuna erişim almanız gerekir. Ardından, temel alınan blobu yeni bir depolama hesabına kopyalayabilir ve blobu bu depolama hesabından indirebilirsiniz.
  ```
  az disk grant-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --duration-in-seconds 3600
  az storage blob download --sas-token "<sas token>" --account-name <account name> --container-name <container name> --name <blob name> --file <local file>
  az disk revoke-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>"
  ```

* VHD indirme   
  SAP sistemi durdurulduğunda ve VM kapatıldıktan sonra, VHD disklerini şirket içi dünyaya geri yüklemek için Azure CLı komutu _Azure Storage blob indirme_ 'yi şirket içi hedefte kullanabilirsiniz. Bunu yapmak için, VHD 'nin adı ve kapsayıcısına ihtiyacınız vardır. Bu, Azure portal ' depolama bölümünde ve VHD 'nin oluşturulduğu depolama kapsayıcısına gitmeniz gerekir) ve VHD 'nin COP olması gereken yeri bilmeniz gerekir. .

  Ardından, indirilecek VHD 'nin kapsayıcısını ve kapsayıcısını ve hedefi VHD 'nin fiziksel hedef konumu (adı dahil) olarak tanımlayarak komutunu kullanabilirsiniz. Komut şu şekilde görünebilir:

  ```
  az storage blob download --name <name of the VHD to download> --container-name <container of the VHD to download> --account-name <storage account name of the VHD to download> --account-key <storage account key> --file <destination of the VHD to download>
  ```

### <a name="transferring-vms-and-disks-within-azure"></a>Azure 'da VM 'Leri ve diskleri aktarma

#### <a name="copying-sap-systems-within-azure"></a>Azure 'da SAP sistemlerini kopyalama

SAP sistemi veya SAP uygulama katmanını destekleyen adanmış bir DBMS sunucusu, büyük olasılıkla, ikili dosyaları içeren işletim sistemini veya SAP veritabanının veri ve günlük dosyalarını içeren birkaç diskten oluşur. Diskleri kopyalamanın ve Azure işlevselliğinin yerel bir diske kaydedilmesi için Azure işlevselliği, birden çok diskin tutarlı bir şekilde anlık görüntüsünü veren bir eşitleme mekanizmasına sahip değildir. Bu nedenle, aynı VM 'ye karşı bağlansalar bile kopyalanmış veya kaydedilmiş disklerin durumu farklı olabilir. Diğer bir deyişle farklı disklerde farklı veri ve günlük dosyası (ler) dahil olmak üzere, sonundaki veritabanı tutarsız olur.

**İşleminin SAP sistem yapılandırmasının bir parçası olan diskleri kopyalamak veya kaydetmek için SAP sistemini durdurmanız ve ayrıca dağıtılan VM 'yi kapatmanız gerekir. Yalnızca Azure 'da veya şirket içinde SAP sisteminin bir kopyasını oluşturmak için disk kümesini kopyalayabilir veya indirebilirsiniz.**

Veri diskleri, bir Azure depolama hesabında VHD dosyaları olarak depolanabilir ve doğrudan bir sanal makineye bağlanabilir veya görüntü olarak kullanılabilir. Bu durumda, VHD sanal makineye iliştirilmeden önce başka bir konuma kopyalanır. Azure 'da VHD dosyasının tam adı Azure içinde benzersiz olmalıdır. Daha önce belirtildiği gibi, adı şu şekilde görünen üç bölümden oluşan bir ad türü olmalıdır:

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

Veri diskleri de yönetilen diskler olabilir. Bu durumda, yönetilen disk, sanal makineye iliştirilmeden önce yeni bir yönetilen disk oluşturmak için kullanılır. Yönetilen diskin adı bir kaynak grubu içinde benzersiz olmalıdır.

##### <a name="powershell"></a>Powershell

Azure PowerShell cmdlet 'lerini, [Bu makalede][storage-powershell-guide-full-copy-vhd]gösterildiği gibi bir VHD 'yi kopyalamak için kullanabilirsiniz. Yeni bir yönetilen disk oluşturmak için aşağıdaki örnekte gösterildiği gibi New-AzDiskConfig ve New-AzDisk ' i kullanın.

```powershell
$config = New-AzDiskConfig -CreateOption Copy -SourceUri "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" -Location <location>
New-AzDisk -ResourceGroupName <resource group name> -DiskName <disk name> -Disk $config
```

##### <a name="azure-cli"></a>Azure CLI

[Bu makalede][storage-azure-cli-copy-blobs]gösterildiği gibi, BIR VHD 'yi kopyalamak IÇIN Azure CLI kullanabilirsiniz. Yeni bir yönetilen disk oluşturmak için aşağıdaki örnekte gösterildiği gibi *az disk Create* kullanın.

```
az disk create --source "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --name <disk name> --resource-group <resource group name> --location <location>
```

##### <a name="azure-storage-tools"></a>Azure depolama araçları

* <https://storageexplorer.com/>

Azure depolama gezginlerine yönelik profesyonel sürümler şurada bulunabilir:

* <https://www.cerebrata.com/>
* <https://clumsyleaf.com/products/cloudxplorer>

Bir VHD 'nin bir depolama hesabı içindeki kopyası bir işlemdir. Bu işlem yalnızca birkaç saniye sürer (SAN donanımına benzer bir şekilde, yavaş kopyalama ve yazma sırasında kopyalama ile anlık görüntü oluşturma). VHD dosyasının bir kopyasına sahip olduktan sonra, VHD 'nin kopyalarını sanal makinelere eklemek için bir sanal makineye ekleyebilir veya bir görüntü olarak kullanabilirsiniz.

##### <a name="powershell"></a>Powershell

```powershell
# attach a vhd to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name newdatadisk -VhdUri <path to vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption attach
$vm | Update-AzVM

# attach a managed disk to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name newdatadisk -ManagedDiskId <managed disk id> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption attach
$vm | Update-AzVM

# attach a copy of the vhd to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name <disk name> -VhdUri <new path of vhd> -SourceImageUri <path to image vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption fromImage
$vm | Update-AzVM

# attach a copy of the managed disk to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$diskConfig = New-AzDiskConfig -Location $vm.Location -CreateOption Copy -SourceUri <source managed disk id>
$disk = New-AzDisk -DiskName <disk name> -Disk $diskConfig -ResourceGroupName <resource group name>
$vm = Add-AzVMDataDisk -VM $vm -Caching <caching option> -Lun <lun, for example 0> -CreateOption attach -ManagedDiskId $disk.Id
$vm | Update-AzVM
```
##### <a name="azure-cli"></a>Azure CLI

```

# attach a vhd to a vm
az vm unmanaged-disk attach --resource-group <resource group name> --vm-name <vm name> --vhd-uri <path to vhd>

# attach a managed disk to a vm
az vm disk attach --resource-group <resource group name> --vm-name <vm name> --disk <managed disk id>

# attach a copy of the vhd to a vm
# this scenario is currently not possible with Azure CLI. A workaround is to manually copy the vhd to the destination.

# attach a copy of a managed disk to a vm
az disk create --name <new disk name> --resource-group <resource group name> --location <location of target virtual machine> --source <source managed disk id>
az vm disk attach --disk <new disk name or managed disk id> --resource-group <resource group name> --vm-name <vm name> --caching <caching option> --lun <lun, for example 0>
```

#### <a name="9789b076-2011-4afa-b2fe-b07a8aba58a1"></a>Azure depolama hesapları arasında diskleri kopyalama
Bu görev Azure portal gerçekleştirilemiyor. Azure PowerShell cmdlet 'lerini, Azure CLı 'yi veya bir üçüncü taraf depolama tarayıcısını kullanabilirsiniz. PowerShell cmdlet 'leri veya CLı komutları, blob 'ları depolama hesapları arasında ve Azure aboneliğindeki bölgeler arasında zaman uyumsuz olarak kopyalamak için blob 'lar oluşturabilir ve yönetebilir.

##### <a name="powershell"></a>Powershell
Ayrıca, VHD 'leri abonelikler arasında kopyalayabilirsiniz. Daha fazla bilgi için [Bu makaleyi][storage-powershell-guide-full-copy-vhd]okuyun.

PS cmdlet mantığının temel akışı şöyle görünür:

* *New-AzStorageContext* ile **kaynak** depolama hesabı için depolama hesabı bağlamı oluşturma-Bkz.<https://docs.microsoft.com/powershell/module/az.storage/new-AzStoragecontext>
* *New-AzStorageContext* ile **hedef** depolama hesabı için depolama hesabı bağlamı oluşturma-Bkz.<https://docs.microsoft.com/powershell/module/az.storage/new-AzStoragecontext>
* Kopyayı ile Başlat

```powershell
Start-AzStorageBlobCopy -SrcBlob <source blob name> -SrcContainer <source container name> -SrcContext <variable containing context of source storage account> -DestBlob <target blob name> -DestContainer <target container name> -DestContext <variable containing context of target storage account>
```

* İle bir döngüde kopyanın durumunu kontrol edin

```powershell
Get-AzStorageBlobCopyState -Blob <target blob name> -Container <target container name> -Context <variable containing context of target storage account>
```

* Yukarıda açıklandığı gibi yeni VHD 'yi bir sanal makineye ekleyin.

Örnekler için [Bu makaleye][storage-powershell-guide-full-copy-vhd]bakın.

##### <a name="azure-cli"></a>Azure CLI
* Kopyayı ile Başlat

```
az storage blob copy start --source-blob <source blob name> --source-container <source container name> --source-account-name <source storage account name> --source-account-key <source storage account key> --destination-container <target container name> --destination-blob <target blob name> --account-name <target storage account name> --account-key <target storage account name>
```

* Kopyaya sahip bir döngüde hala varsa durumu denetle

```
az storage blob show --name <target blob name> --container <target container name> --account-name <target storage account name> --account-key <target storage account name>
```

* Yukarıda açıklandığı gibi yeni VHD 'yi bir sanal makineye ekleyin.

Örnekler için [Bu makaleye][storage-azure-cli-copy-blobs]bakın.

### <a name="disk-handling"></a>Disk Işleme

#### <a name="4efec401-91e0-40c0-8e64-f2dceadff646"></a>SAP dağıtımları için VM/disk yapısı

İdeal olarak, bir VM yapısının ve ilişkili disklerin işlenmesi basit olmalıdır. Şirket içi yüklemelerde, müşteriler bir sunucu yüklemesi yapılandırmak için birçok yol geliştirmiştir.

* DBMS ve/veya SAP 'nin işletim sistemini ve tüm ikililerini içeren bir temel disk. Mart 2015 ' den itibaren bu disk, en fazla 1 GB boyutunda, daha önceki kısıtlamalar yerine 127GB ile sınırlıdır.
* SAP veritabanının DBMS günlük dosyasını ve DBMS geçici depolama alanının günlük dosyasını içeren bir veya birden çok disk (DBMS bunu destekliyorsa). Veritabanı günlüğü ıOPS gereksinimleri yüksekse, gereken ıOPS hacmine ulaşmak için birden çok disk oluşturmanız gerekir.
* SAP veritabanının bir veya iki veritabanı dosyasını ve DBMS geçici veri dosyalarını (DBMS bunu destekliyorsa) içeren birçok disk.

![SAP için Azure IaaS VM 'sinin başvuru yapılandırması][planning-guide-figure-1300]


---
> ![Windows][Logo_Windows] Windows
>
> Birçok müşteri sayesinde, örneğin, SAP ve DBMS ikili dosyalarının c:\ ' da yüklü olmadığı durumlarda yapılandırma gördük. işletim sisteminin yüklendiği sürücü. Bunun çeşitli nedenleri vardı, ancak köke geri döndüğünüzde, genellikle sürücülerin küçük ve işletim sistemi yükseltmeleri gereken ek alanı 10-15 yıl önce gerekiyordu. Her iki koşul de bu günler çok sık uygulanmaz. Bugün c:\ Sürücü, büyük birim disklerinde veya VM 'lerde eşlenebilir. Dağıtımların yapısıyla basit kalmasını sağlamak için, Azure 'da SAP NetWeaver sistemleri için aşağıdaki dağıtım modelini izlemeniz önerilir
>
> Windows işletim sistemi disk belleği dosyası D: sürücüsünde (kalıcı olmayan disk) olmalıdır
>
> ![Linux][Logo_Linux] Linux
>
> Linux takas dosyasını [Bu makalede][virtual-machines-linux-agent-user-guide]açıklandığı gibi Linux üzerinde/mnt/mnt/Resource altına yerleştirin. Takas dosyası, Linux Aracısı/etc/waagent.exe yapılandırma dosyasında yapılandırılabilir. Aşağıdaki ayarları ekleyin veya değiştirin:
>
>

```
ResourceDisk.EnableSwap=y
ResourceDisk.SwapSizeMB=30720
```

Değişiklikleri etkinleştirmek için Linux aracısını ile yeniden başlatmanız gerekir

```
sudo service waagent restart
```

Önerilen takas dosyası boyutu hakkında daha fazla bilgi için lütfen SAP Note [1597355] makalesini okuyun

---
DBMS veri dosyaları için kullanılan disk sayısı ve bu disklerin üzerinde barındırıldığı Azure depolama türü, ıOPS gereksinimlerine ve gerekli gecikme süresine göre belirlenir. Tam kotalar [Bu makalede (Linux)][virtual-machines-sizes-linux] ve [Bu makalede (Windows)][virtual-machines-sizes-windows]açıklanmaktadır.

Son iki yıl içinde SAP dağıtımları deneyimi, şu şekilde özetlenebilir bazı derslere sahiptir:

* Mevcut müşteri sistemleri, SAP veritabanlarını temsil eden farklı boyutlardaki veri dosyalarına sahip olabileceğinden, farklı veri dosyalarına yönelik ıOPS trafiği her zaman aynı değildir. Sonuç olarak, veri dosyaları LUN 'larını bunların dışına koymak için birden çok disk üzerinden bir RAID yapılandırması kullanılması daha iyi hale gelir. Özellikle de bir ıOPS oranının, DBMS işlem günlüğüne göre tek bir diskin kotasına isabet ettiği Azure Standart depolama ile birlikte durumlar vardı. Bu senaryolarda, Premium depolamanın kullanılması önerilir veya bir yazılım Stripe ile birden çok standart depolama diskini daha da toplayarak bir araya getirin.

---
> ![Windows][Logo_Windows] Windows
>
> * [Azure Sanal Makineler’de SQL Server için performansa yönelik en iyi uygulamalar][virtual-machines-sql-server-performance-best-practices]
>
> ![Linux][Logo_Linux] Linux
>
> * [Linux 'ta yazılım RAID yapılandırma][virtual-machines-linux-configure-raid]
> * [Azure 'da bir Linux VM 'de LVM 'yi yapılandırma][virtual-machines-linux-configure-lvm]
> * [Azure depolama gizli dizileri ve Linux g/ç iyileştirmeleri](https://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)
>
>

---
* Premium Depolama, özellikle kritik işlem günlüğü yazmaları için önemli ölçüde daha iyi performans gösterir. Performansa benzer bir üretim sunması beklenen SAP senaryolarında, Azure Premium Depolama 'yı kullanan VM Serisi kullanımı kesinlikle önerilir.

İşletim sistemini içeren diskin ve önerdiğimiz gibi, SAP ve veritabanının (Temel VM) ikili dosyalarının de artık 127GB ile sınırlı olmadığından emin olmanız gerekir. Artık en fazla 1 TB boyutunda olabilir. Bu, örneğin SAP Batch iş günlükleri gibi tüm gerekli dosyaları tutmak için yeterli alan olmalıdır.

Özellikle de DBMS VM 'Leri için daha fazla öneri ve daha ayrıntılı bilgi için, [DBMS dağıtım kılavuzuna][dbms-guide] başvurun

#### <a name="disk-handling"></a>Disk Işleme

Çoğu senaryoda SAP veritabanını sanal makineye dağıtmak için ek diskler oluşturmanız gerekir. Bu belgenin [SAP dağıtımları için bölüm VM/disk yapısındaki][planning-guide-5.5.1] disk sayısı ile ilgili dikkat edilecek noktalar ele alındık. Azure portal, temel VM dağıtıldıktan sonra diskleri eklemek ve ayırmak için izin verir. Diskler, sanal makine çalışır duruma geldiğinde ve durdurulduğunda da eklenebilir/ayrılabilir. Bir disk iliştirilirken, Azure portal boş bir disk veya mevcut bir disk ekleme olanağı sunar. bu zaman içinde bu noktada başka bir sanal makineye bağlı değildir.

**Not**: Diskler, belirli bir zamanda yalnızca bir VM 'ye iliştirilebilir.

![Azure Standart depolama ile diskleri iliştirme/ayır][planning-guide-figure-1400]

Yeni bir sanal makinenin dağıtımı sırasında, yönetilen diskleri kullanmak isteyip istemediğinize karar verebilir veya disklerinizi Azure Storage hesaplarına yerleştirebilirsiniz. Premium Depolama kullanmak istiyorsanız, yönetilen diskleri kullanmanızı öneririz.

Ardından, yeni ve boş bir disk oluşturmak isteyip istemediğinizi ya da daha önce karşıya yüklenen mevcut bir diski seçip sanal makineye şimdi bağlı olup olmayacağını belirlemeniz gerekir.

**ÖNEMLİ**: Azure Standart depolama ile konak önbelleği **kullanmak istemezsiniz.** Ana bilgisayar önbelleği tercihini varsayılan değer olan NONE olarak bırakmanız gerekir. Azure Premium Depolama ile, g/ç özelliği genellikle veritabanı veri dosyalarında tipik g/ç trafiği gibi okunmısam, okuma önbelleğini etkinleştirmeniz gerekir. Veritabanı işlem günlük dosyası olması durumunda, önbelleğe alma önerilmez.

---
> ![Windows][Logo_Windows] Windows
>
> [Azure portal veri diski iliştirme][virtual-machines-linux-attach-disk-portal]
>
> Diskler iliştirilmişse, Windows Disk Yöneticisi 'Ni açmak için VM 'de oturum açmanız gerekir. [Ekli diskler için otomatik bağlama][planning-guide-5.5.3]bölümünde önerilen otomatik bağlama etkinleştirilmemişse, yeni eklenen birimin çevrimiçi alınması ve başlatılması gerekir.
>
> ![Linux][Logo_Linux] Linux
>
> Diskler iliştirilmişse, sanal makinede oturum açmanız ve [Bu makalede][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux] açıklandığı gibi diskleri başlatmak gerekir
>
>

---
Yeni disk boş bir diskdir, diski de biçimlendirmeniz gerekir. Biçimlendirme için özellikle DBMS verileri ve günlük dosyaları için, DBMS 'nin çıplak dağıtımlarıyla aynı öneriler geçerlidir.

[Microsoft Azure sanal makine kavramı][planning-guide-3.2]bölümünde zaten belirtildiği gibi, bir Azure Storage hesabı g/ç BIRIMI, IOPS ve veri hacmi bakımından sonsuz kaynaklar sağlamaz. Genellikle DBMS VM 'Leri bundan en çok etkilendi. Azure depolama hesabı birimi sınırının içinde kalmak için birkaç yüksek g/ç birimi sanal makinesi varsa, her VM için ayrı bir depolama hesabı kullanılması en iyi yöntem olabilir. Aksi takdirde, her bir depolama hesabının sınırına vurmadan bu VM 'Leri farklı depolama hesapları arasında nasıl dengeleyebilirsiniz. Daha fazla ayrıntı, [DBMS dağıtım kılavuzunda][dbms-guide]ele alınmıştır. Ayrıca, saf SAP uygulama sunucusu VM 'Leri veya diğer VM 'ler için bu sınırlamaları aklınızda tutmanız gerekir. bu durum sonunda ek VHD 'Ler gerektirebilir. Yönetilen disk kullanıyorsanız bu kısıtlamalar uygulanmaz. Premium Depolama kullanmayı planlıyorsanız, yönetilen disk kullanmanızı öneririz.

Depolama hesapları için uygun olan başka bir konu, bir depolama hesabındaki VHD 'Lerin coğrafi olarak çoğaltılma edilip edilmeyeceğini belirtir. Coğrafi çoğaltma, VM düzeyinde değil, depolama hesabı düzeyinde etkin veya devre dışı bırakıldı. Coğrafi çoğaltma etkinse, depolama hesabı içindeki VHD 'ler aynı bölgedeki başka bir Azure veri merkezine çoğaltılır. Buna karar vermeden önce, aşağıdaki kısıtlamayı düşünmeniz gerekir:

Azure coğrafi çoğaltma, bir VM 'deki her VHD üzerinde yerel olarak çalışarak, bir VM 'de birden çok VHD üzerinde IOs 'u kronolojik sırada çoğaltmaz. Bu nedenle, temel VM 'yi temsil eden VHD 'nin yanı sıra VM 'ye bağlı ek VHD 'ler birbirinden bağımsız olarak çoğaltılır. Bu, farklı VHD 'lerde yapılan değişiklikler arasında eşitleme olmadığı anlamına gelir. IOs 'un yazıldığı sırada bağımsız olarak çoğaltılırsa, coğrafi çoğaltmanın, veritabanlarının veritabanları birden çok VHD üzerinde dağıtılmış olan veritabanı sunucuları için değer olmadığı anlamına gelir. DBMS 'ye ek olarak, işlemlerin farklı VHD 'lerde veri yazacağı veya işlediği ve değişikliklerin sırasını korumak için önemli olduğu diğer uygulamalar da olabilir. Bu bir gereksinimle, Azure 'da coğrafi çoğaltma etkinleştirilmemelidir. Bir VM kümesi için coğrafi çoğaltma isteyip istememe ya da tercih etmeksizin, ancak başka bir küme için, VM 'Leri ve bunlarla ilgili VHD 'Leri, coğrafi çoğaltmanın etkin veya devre dışı olduğu farklı depolama hesaplarına önceden kategorilere ayırabilirsiniz.

#### <a name="17e0d543-7e8c-4160-a7da-dd7117a1ad9d"></a>İliştirilmiş diskler için otomatik bağlama ayarlama
---
> ![Windows][Logo_Windows] Windows
>
> Kendi görüntülerinden veya disklerden oluşturulan VM 'Ler için otomatik bağlama parametresini denetlemeniz ve muhtemelen ayarlamanız gerekir. Bu parametrenin ayarlanması, Azure 'daki yeniden başlatma veya yeniden dağıtım sonrasında bağlı/bağlı sürücüleri otomatik olarak yeniden bağlamak için VM 'nin izin verir.
> Parametresi, Azure Market 'te Microsoft tarafından sunulan görüntüler için ayarlanır.
>
> Otomatik bağlama ayarlamak için DiskPart. exe ' nin komut satırı yürütülebilir dosyasının belgelerine bakın:
>
> * [DiskPart komut satırı seçenekleri](https://technet.microsoft.com/library/bb490893.aspx)
> * [Otomatik bağlama](https://technet.microsoft.com/library/cc753703.aspx)
>
> Windows komut satırı penceresi yönetici olarak açılmalıdır.
>
> Diskler iliştirilmişse, Windows Disk Yöneticisi 'Ni açmak için VM 'de oturum açmanız gerekir. Otomatik bağlama, [eklenen diskler için otomatik bağlama][planning-guide-5.5.3]bölümünde önerilen şekilde etkinleştirilmemişse, yeni eklenen birimin > çevrimiçi olarak alınması ve başlatılması gerekir.
>
> ![Linux][Logo_Linux] Linux
>
> [Bu makalede][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]açıklandığı gibi yeni eklenen boş diski başlatmalısınız.
> Ayrıca,/etc/fstabna yeni diskler eklemeniz gerekir.
>
>

---
### <a name="final-deployment"></a>Son dağıtım

Son dağıtım ve özellikle SAP için Azure uzantısının dağıtımına ilişkin tam adımlar için [dağıtım kılavuzuna][deployment-guide]bakın.

## <a name="accessing-sap-systems-running-within-azure-vms"></a>Azure VM 'Leri içinde çalışan SAP sistemlerine erişme

SAP GUI 'yi kullanarak genel İnternet üzerinden bu SAP sistemlerine bağlanmak istediğiniz senaryolar için aşağıdaki yordamların uygulanması gerekir.

Bu belgede daha sonra, şirket içi sistemler ve Azure sistemleri arasında siteden siteye bağlantı (VPN tüneli) veya Azure ExpressRoute bağlantısı olan şirket içi dağıtımlardaki SAP sistemlerine bağlanarak diğer ana senaryoyu ele alınacaktır.

### <a name="remote-access-to-sap-systems"></a>SAP sistemlerine uzaktan erişim

Azure Resource Manager ile, artık eski Klasik modelde olduğu gibi varsayılan uç nokta yoktur. Bir Azure Resource Manager VM 'nin tüm bağlantı noktaları şu kadar sürede açıktır:

1. Alt ağ veya ağ arabirimi için ağ güvenlik grubu tanımlanmadı. Azure VM 'lerine yönelik ağ trafiğine, "ağ güvenlik grupları" adı verilir. Daha fazla bilgi için bkz. [Ağ Güvenlik Grubu (NSG) nedir?][virtual-networks-nsg]
2. Ağ arabirimi için tanımlı Azure Load Balancer yok   

[Bu makalede][virtual-machines-azure-resource-manager-architecture]açıklandığı gibi klasik model ve ARM arasındaki mimari farkını inceleyin.

#### <a name="configuration-of-the-sap-system-and-sap-gui-connectivity-over-the-internet"></a>Internet üzerinden SAP System ve SAP GUI bağlantısı yapılandırması

Lütfen bu konunun ayrıntılarını açıklayan bu makaleye bakın:<https://blogs.msdn.com/b/saponsqlserver/archive/2014/06/24/sap-gui-connection-closed-when-connecting-to-sap-system-in-azure.aspx>

#### <a name="changing-firewall-settings-within-vm"></a>VM 'de güvenlik duvarı ayarlarını değiştirme

SAP sisteminize gelen trafiğe izin vermek için sanal makinelerinizde güvenlik duvarının yapılandırılması gerekebilir.

---
> ![Windows][Logo_Windows] Windows
>
> Varsayılan olarak, Azure dağıtılan bir VM 'deki Windows güvenlik duvarı açıktır. Artık SAP bağlantı noktasının açılmasına izin vermeniz gerekir, aksi takdirde SAP GUI bağlantısı yapamaz.
> Bunu yapmak için:
>
> * **Gelişmiş ayarlar**için Control panel\system ve Security\windows güvenlik duvarını açın.
> * Şimdi gelen kurallara sağ tıklayıp **Yeni kural**' ı seçin.
> * Aşağıdaki sihirbazda yeni bir **bağlantı noktası** kuralı oluşturmayı tercih edin.
> * Sihirbazın bir sonraki adımında, ayarı TCP ' de bırakın ve açmak istediğiniz bağlantı noktası numarasını yazın. SAP örnek KIMLIĞINIZ 00 olduğundan, 3200 sürdü. Örneğinizin farklı bir örnek numarası varsa, daha önce örnek numarasına göre tanımladığınız bağlantı noktası açılmalıdır.
> * Sihirbazın bir sonraki bölümünde, **bağlantıya Izin ver** ' in işaretli olması gerekir.
> * Sihirbazın bir sonraki adımında, kuralın etki alanı, özel ve genel ağ için uygulanıp uygulanmadığını tanımlamanız gerekir. Gereksinimleriniz için gerekiyorsa lütfen ayarlayın. Bununla birlikte, genel ağ üzerinden SAP GUI ile bağlantı kurarak, kuralın genel ağa uygulanmış olması gerekir.
> * Sihirbazın son adımında, kuralı adlandırın ve **son**' a basarak kaydedin.
>
> Kural hemen etkili olur.
>
> ![Bağlantı noktası kuralı tanımı][planning-guide-figure-1600]
>
> ![Linux][Logo_Linux] Linux
>
> Azure Marketi 'ndeki Linux görüntüleri, Iptables güvenlik duvarını varsayılan olarak etkinleştirmez ve SAP sisteminizin bağlantısı çalışmalıdır. İptables veya başka bir güvenlik duvarını etkinleştirdiyseniz, Iptables ' ın (xx, SAP sisteminizin sistem numarasıdır) numaralı bağlantı noktasına gelen TCP trafiğine izin vermek için kullanılan güvenlik duvarının belgelerine bakın.
>
>

---
#### <a name="security-recommendations"></a>Güvenlik önerileri

SAP GUI, çalıştıran hiçbir SAP örneğine (bağlantı noktası 32xx) hemen bağlanamaz, ancak önce SAP ileti sunucusu işlemine (bağlantı noktası 36XX) açılan bağlantı noktası aracılığıyla bağlanır. Geçmişte, uygulama örneklerine yönelik iç iletişim için ileti sunucusu tarafından aynı bağlantı noktası kullanılmıştır. Şirket içi uygulama sunucularının Azure 'daki bir ileti sunucusuyla yanlışlıkla iletişim kurmasını engellemek için iç iletişim bağlantı noktaları değiştirilebilir. SAP ileti sunucusuyla uygulama örnekleri arasındaki iç iletişimin, proje testi için bir geliştirme kopyası gibi şirket içi sistemlerden kopyalanmış olan sistemlerdeki farklı bir bağlantı noktası numarasıyla değiştirilmesi kesinlikle önerilir. Bu, varsayılan profil parametresiyle yapılabilir:

> rdisp/msserv_internal
>
>

[SAP Ileti sunucusu Için güvenlik ayarlarında](https://help.sap.com/saphelp_nwpi71/helpdata/en/47/c56a6938fb2d65e10000000a42189c/content.htm) belgelendiği gibi


### <a name="3e9c3690-da67-421a-bc3f-12c520d99a30"></a>SAP NetWeaver demo/eğitim senaryosuna sahip tek VM

![Azure Cloud Services yalıtılmış olarak aynı VM adlarıyla tek VM SAP tanıtım sistemlerini çalıştırma][planning-guide-figure-1700]

Bu senaryoda, tüm eğitim/tanıtım senaryosunun tek bir sanal makinede bulunduğu tipik bir eğitim/tanıtım sistemi senaryosu uygulamamız. Dağıtımın VM görüntü şablonları aracılığıyla yapıldığını varsayıyoruz. Ayrıca, aynı ada sahip VM 'lerle birlikte bu tanıtım/seyahat sanal makinelerinin birden çok sürümünün dağıtılması gerektiğini varsaydık. Tüm eğitim sistemleri, şirket içi varlıklarınızla bağlantı sahibi değildir ve karma dağıtıma karşı bir yönlerdir.

Bu belgede, bu belgede [Azure IÇIN SAP Ile VM 'Leri hazırlama][planning-guide-5.2] başlıklı bölümün bazı bölümlerinde açıklandığı gıbı bir VM görüntüsü oluşturdunuz.

Senaryoyu uygulamak için olayların sırası şuna benzer:

##### <a name="powershell"></a>Powershell

* Her eğitim/tanıtım dünyası için yeni bir kaynak grubu oluşturun

```powershell
$rgName = "SAPERPDemo1"
New-AzResourceGroup -Name $rgName -Location "North Europe"
```
* Yönetilen diskleri kullanmak istemiyorsanız yeni bir depolama hesabı oluşturun

```powershell
$suffix = Get-Random -Minimum 100000 -Maximum 999999
$account = New-AzStorageAccount -ResourceGroupName $rgName -Name "saperpdemo$suffix" -SkuName Standard_LRS -Kind "Storage" -Location "North Europe"
```

* Aynı konak adının ve IP adreslerinin kullanımını etkinleştirmek için her eğitim/tanıtım dünyası için yeni bir sanal ağ oluşturun. Sanal ağ, yalnızca bağlantı noktası 3389 ' e giden trafiğe izin veren bir ağ güvenlik grubu tarafından korunmuştur ve SSH için bağlantı noktası 22 ' dir.

```powershell
# Create a new Virtual Network
$rdpRule = New-AzNetworkSecurityRuleConfig -Name SAPERPDemoNSGRDP -Protocol * -SourcePortRange * -DestinationPortRange 3389 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 100
$sshRule = New-AzNetworkSecurityRuleConfig -Name SAPERPDemoNSGSSH -Protocol * -SourcePortRange * -DestinationPortRange 22 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 101
$nsg = New-AzNetworkSecurityGroup -Name SAPERPDemoNSG -ResourceGroupName $rgName -Location  "North Europe" -SecurityRules $rdpRule,$sshRule

$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name Subnet1 -AddressPrefix  10.0.1.0/24 -NetworkSecurityGroup $nsg
$vnet = New-AzVirtualNetwork -Name SAPERPDemoVNet -ResourceGroupName $rgName -Location "North Europe"  -AddressPrefix 10.0.1.0/24 -Subnet $subnetConfig
```

* İnternet 'ten sanal makineye erişmek için kullanılabilecek yeni bir genel IP adresi oluşturun

```powershell
# Create a public IP address with a DNS name
$pip = New-AzPublicIpAddress -Name SAPERPDemoPIP -ResourceGroupName $rgName -Location "North Europe" -DomainNameLabel $rgName.ToLower() -AllocationMethod Dynamic
```

* Sanal makine için yeni bir ağ arabirimi oluştur

```powershell
# Create a new Network Interface
$nic = New-AzNetworkInterface -Name SAPERPDemoNIC -ResourceGroupName $rgName -Location "North Europe" -Subnet $vnet.Subnets[0] -PublicIpAddress $pip
```

* Sanal makine oluşturur. Bu senaryo için, her VM aynı ada sahip olacaktır. Bu VM 'lerdeki SAP NetWeaver örneklerinin SAP SID 'SI de aynı olacaktır. Azure Kaynak grubu ' nda, VM 'nin adının benzersiz olması gerekir, ancak farklı Azure Kaynak gruplarında aynı ada sahip VM 'Leri çalıştırabilirsiniz. Windows 'un varsayılan ' Administrator ' hesabı veya Linux için ' root ' hesabı geçerli değil. Bu nedenle, yeni bir yönetici kullanıcı adının bir parolayla birlikte tanımlanması gerekir. VM 'nin boyutunun de tanımlanması gerekir.

```powershell
#####
# Create a new virtual machine with an official image from the Azure Marketplace
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

# select image
$vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "SUSE" -Offer "SLES-SAP" -Skus "12-SP1" -Version "latest"
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "RedHat" -Offer "RHEL" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "Oracle" -Offer "Oracle-Linux" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$diskName="osfromimage"
$osDiskUri=$account.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"

$vmconfig = Set-AzVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Windows
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Linux
#$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a Managed Disk Image
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$vmconfig = Set-AzVMSourceImage -VM $vmconfig -Id <Id of Managed Disk Image>
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

* İsteğe bağlı olarak ek diskler ekleyin ve gerekli içeriği geri yükleyin. Tüm blob adları (bloblara yönelik URL 'Ler) Azure içinde benzersiz olmalıdır.

```powershell
# Optional: Attach additional VHD data disks
$vm = Get-AzVM -ResourceGroupName $rgName -Name SAPERPDemo
$dataDiskUri = $account.PrimaryEndpoints.Blob.ToString() + "vhds/datadisk.vhd"
Add-AzVMDataDisk -VM $vm -Name datadisk -VhdUri $dataDiskUri -DiskSizeInGB 1023 -CreateOption empty | Update-AzVM

# Optional: Attach additional Managed Disks
$vm = Get-AzVM -ResourceGroupName $rgName -Name SAPERPDemo
Add-AzVMDataDisk -VM $vm -Name datadisk -DiskSizeInGB 1023 -CreateOption empty -Lun 0 | Update-AzVM
```

##### <a name="cli"></a>CLI

Aşağıdaki örnek kod Linux üzerinde kullanılabilir. Windows için, yukarıda açıklandığı gibi PowerShell 'i kullanın veya örneği $rgName yerine% rgName% kullanmak üzere uyarlayın ve Windows komut *kümesini*kullanarak ortam değişkenini ayarlayın.

* Her eğitim/tanıtım dünyası için yeni bir kaynak grubu oluşturun

```
rgName=SAPERPDemo1
rgNameLower=saperpdemo1
az group create --name $rgName --location "North Europe"
```

* Yeni depolama hesabı oluştur

```
az storage account create --resource-group $rgName --location "North Europe" --kind Storage --sku Standard_LRS --name $rgNameLower
```

* Aynı konak adının ve IP adreslerinin kullanımını etkinleştirmek için her eğitim/tanıtım dünyası için yeni bir sanal ağ oluşturun. Sanal ağ, yalnızca bağlantı noktası 3389 ' e giden trafiğe izin veren bir ağ güvenlik grubu tarafından korunmuştur ve SSH için bağlantı noktası 22 ' dir.

```
az network nsg create --resource-group $rgName --location "North Europe" --name SAPERPDemoNSG
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGRDP --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 3389 --access Allow --priority 100 --direction Inbound
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGSSH --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 22 --access Allow --priority 101 --direction Inbound

az network vnet create --resource-group $rgName --name SAPERPDemoVNet --location "North Europe" --address-prefixes 10.0.1.0/24
az network vnet subnet create --resource-group $rgName --vnet-name SAPERPDemoVNet --name Subnet1 --address-prefix 10.0.1.0/24 --network-security-group SAPERPDemoNSG
```

* İnternet 'ten sanal makineye erişmek için kullanılabilecek yeni bir genel IP adresi oluşturun

```
az network public-ip create --resource-group $rgName --name SAPERPDemoPIP --location "North Europe" --dns-name $rgNameLower --allocation-method Dynamic
```

* Sanal makine için yeni bir ağ arabirimi oluştur

```
az network nic create --resource-group $rgName --location "North Europe" --name SAPERPDemoNIC --public-ip-address SAPERPDemoPIP --subnet Subnet1 --vnet-name SAPERPDemoVNet
```

* Sanal makine oluşturur. Bu senaryo için, her VM aynı ada sahip olacaktır. Bu VM 'lerdeki SAP NetWeaver örneklerinin SAP SID 'SI de aynı olacaktır. Azure Kaynak grubu ' nda, VM 'nin adının benzersiz olması gerekir, ancak farklı Azure Kaynak gruplarında aynı ada sahip VM 'Leri çalıştırabilirsiniz. Windows 'un varsayılan ' Administrator ' hesabı veya Linux için ' root ' hesabı geçerli değil. Bu nedenle, yeni bir yönetici kullanıcı adının bir parolayla birlikte tanımlanması gerekir. VM 'nin boyutunun de tanımlanması gerekir.

```
#####
# Create virtual machines using storage accounts
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image SUSE:SLES-SAP:12-SP1:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image RedHat:RHEL:7.2:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image "Oracle:Oracle-Linux:7.2:latest" --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password

#####
# Create virtual machines using Managed Disks
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image SUSE:SLES-SAP:12-SP1:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image RedHat:RHEL:7.2:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image "Oracle:Oracle-Linux:7.2:latest" --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
```

```
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --os-type Windows --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --image <path to image vhd>
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --os-type Linux --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --image <path to image vhd> --authentication-type password

#####
# Create a new virtual machine with a Managed Disk Image
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --image <managed disk image id>
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --image <managed disk image id> --authentication-type password
```

* İsteğe bağlı olarak ek diskler ekleyin ve gerekli içeriği geri yükleyin. Tüm blob adları (bloblara yönelik URL 'Ler) Azure içinde benzersiz olmalıdır.

```
# Optional: Attach additional VHD data disks
az vm unmanaged-disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --vhd-uri https://$rgNameLower.blob.core.windows.net/vhds/data.vhd  --new

# Optional: Attach additional Managed Disks
az vm disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --disk datadisk --new
```

##### <a name="template"></a>Şablon

GitHub 'daki Azure-QuickStart-Templates deposundaki örnek şablonları kullanabilirsiniz.

* [Basit Linux VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* [Basit Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
* [Görüntüden VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

### <a name="implement-a-set-of-vms-that-communicate-within-azure"></a>Azure içinde iletişim kuran bir VM kümesi uygulama

Bu karma olmayan senaryo, tanıtım/eğitim senaryosunu temsil eden yazılımın birden çok VM 'ye yayıldığı eğitim ve tanıtım amacıyla tipik bir senaryodur. Farklı VM 'lerde yüklenen farklı bileşenlerin birbirleriyle iletişim kurması gerekir. Bu senaryoda, şirket içi ağ iletişimi veya şirket içi bir senaryoya gerek yoktur.

Bu senaryo, bu belgenin [SAP NetWeaver demo/eğitim senaryosuyla bölüm tek VM][planning-guide-7.1] 'de açıklanan yükleme uzantısıdır. Bu durumda, var olan bir kaynak grubuna daha fazla sanal makine eklenecektir. Aşağıdaki örnekte, eğitim yatay bir SAP ASCS/SCS VM 'den, bir DBMS çalıştıran bir VM 'den ve SAP uygulama sunucusu örnek VM 'den oluşur.

Bu senaryoyu oluşturmadan önce, daha önce senaryoda zaten uygulanmış olan temel ayarları düşünmeniz gerekir.

#### <a name="resource-group-and-virtual-machine-naming"></a>Kaynak grubu ve sanal makine adlandırma

Tüm kaynak grubu adları benzersiz olmalıdır. Kaynaklarınızın `<rg-name`> ön eki gibi kendi adlandırma düzeninizi geliştirin.

Sanal makine adı, kaynak grubu içinde benzersiz olmalıdır.

#### <a name="set-up-network-for-communication-between-the-different-vms"></a>Farklı VM 'Ler arasında iletişim için ağ kurma

![Bir Azure sanal ağı içindeki VM 'Ler kümesi][planning-guide-figure-1900]

Aynı eğitim/tanıtım çelerine ait klonlarla adlandırma çakışmalarını engellemek için, her yatay için bir Azure sanal ağı oluşturmanız gerekir. DNS ad çözümlemesi, Azure tarafından sağlanacak veya kendi DNS sunucunuzu Azure dışında (burada açıklanmayacak şekilde) yapılandırabilirsiniz. Bu senaryoda, kendi DNS sitemizi yapılandırmayız. Bir Azure sanal ağı içindeki tüm sanal makineler için, ana bilgisayar adları aracılığıyla iletişim etkinleştirilecek.

Yalnızca kaynak grupları değil, eğitim veya tanıtım ağları 'nın sanal ağlar tarafından ayrılması nedenleri:

* SAP yatay ayarı için kendi AD/OpenLDAP gerekir ve bir etki alanı sunucusunun her bir landscapın parçası olması gerekir.  
* SAP yatay ayarı, sabit IP adresleriyle çalışması gereken bileşenlere sahiptir.

Azure sanal ağları ve bunların nasıl tanımlanacağı hakkında daha fazla ayrıntı, [Bu makalede][virtual-networks-create-vnet-arm-pportal]bulunabilir.

## <a name="deploying-sap-vms-with-corporate-network-connectivity-cross-premises"></a>SAP VM 'lerini kurumsal ağ bağlantısıyla dağıtma (Şirket Içi)

SAP Yatayı çalıştırır ve yüksek kaliteli DBMS sunucuları, uygulama katmanları için şirket içi sanallaştırılmış ortamlar ve daha küçük 2 katmanlı yapılandırılmış SAP sistemleri ve Azure IaaS arasında dağıtımı bölmek istiyorsunuz. Temel varsayım, tek bir SAP 'nin içindeki SAP sistemlerinin birbirleriyle ve şirket içinde dağıtılan diğer birçok yazılım bileşeni ile dağıtım formuyla bağımsız olarak iletişim kurması gereken bir diğer yazılım bileşenleriyle iletişim kurmasını sağlar. Ayrıca, SAP GUI veya diğer arabirimlere bağlanan son kullanıcı için dağıtım formu tarafından tanıtılan bir farklılık olmamalıdır. Bu koşullar yalnızca şirket içi Active Directory/OpenLDAP ve DNS hizmetlerinden oluşan siteden siteye/çok siteli bağlantı veya Azure ExpressRoute gibi özel bağlantılar aracılığıyla Azure sistemlerine genişletilmiş olduğunda karşılanır.



### <a name="scenario-of-an-sap-landscape"></a>SAP yatay senaryosu

Şirket içi veya hibrit senaryosu, aşağıdaki grafiklerde kabaca açıklanabilir:

![Şirket içi ve Azure varlıkları arasında siteden siteye bağlantı][planning-guide-figure-2100]

Yukarıda gösterilen senaryo, şirket içinde yer alan bir senaryoyu açıklamaktadır

Minimum gereksinim, tarayıcı erişimi veya Azure hizmetlerine sistem erişimi için VPN tabanlı bağlantılar için SSL/TLS gibi güvenli iletişim protokollerinin kullanılması. Varsayımı, şirketlerin kurumsal ağı ile Azure arasındaki VPN bağlantısını farklı şekilde işlemesinden farklıdır. Bazı şirketler tüm bağlantı noktalarını tekrar açabilir. Bazı diğer şirketler, açılması gereken bağlantı noktalarında kesin olmasını isteyebilir.

Aşağıdaki tabloda, Genel SAP iletişim bağlantı noktaları listelenir. Temel olarak SAP Gateway bağlantı noktasını açmak yeterlidir.

<!-- sapms is prefix of a SAP service name and not a spelling error -->

| Hizmet | Bağlantı noktası adı | Örnek `<nn`> = 01 | Varsayılan Aralık (en düşük) | Yorum |
| --- | --- | --- | --- | --- |
| Dağıtıcı |sapdp`<nn>` bkz. * |3201 |3200 - 3299 |Windows ve Java için SAP GUI tarafından kullanılan SAP dağıtıcısı |
| İleti sunucusu |sapms`<sid`> bkz: * * |3600 |Ücretsiz sapms`<anySID`> |SID = SAP-sistem KIMLIĞI |
| Ağ geçidi |sapgw`<nn`> bkz. * |3301 |ücretsiz |CPIC ve RFC iletişimi için kullanılan SAP Gateway |
| SAP yönlendiricisi |sapdp99 |3299 |ücretsiz |Yalnızca CI (Merkezi örnek) hizmet adları, yüklemeden sonra isteğe bağlı olarak/etc/Services 'e atanabilir. |

*) nn = SAP örnek numarası

\* *) SID = SAP-sistem KIMLIĞI

SAP ürünlerine göre farklı SAP ürünleri veya hizmetleri için gereken bağlantı noktaları hakkında daha ayrıntılı bilgi burada <https://scn.sap.com/docs/DOC-17124>bulabilirsiniz.
Bu belgeyle, belirli SAP ürünleri ve senaryoları için gereken VPN cihazında adanmış bağlantı noktalarını açabilmelisiniz.

Bu tür bir senaryoya VM 'Leri dağıtmanın diğer güvenlik önlemleri, erişim kurallarını tanımlamak için bir [ağ güvenlik grubu][virtual-networks-nsg] oluşturmak olabilir.

### <a name="dealing-with-different-virtual-machine-series"></a>Farklı sanal makine serisiyle ilgilenme

Microsoft, üzerinde çalıştığı donanımda vCPU sayısı, bellek veya daha fazla önem taşıyan birçok farklı VM türü eklemiştir. SAP ile bu VM 'Lerin tümü desteklenmez (bkz. SAP Not [1928533]IÇINDE desteklenen VM Types). Bu VM 'lerden bazıları farklı ana bilgisayar donanım nesilleri üzerinde çalışır. Bu konak donanım oluşturmaları, bir Azure ölçek birimi 'nin ayrıntı düzeyine dağıtılır. Seçtiğiniz farklı VM türlerinin aynı ölçek birimi üzerinde çalıştırılamadığı durumlar meydana gelebilir. Bir kullanılabilirlik kümesi, farklı donanımlar temelinde ölçek birimlerini yayan sınırlı olur.  Örneğin, bir kullanılabilirlik kümesindeki bir E64s_v3 VM 'de SAP DBMS katmanını çalıştırıyorsanız bir HA yapılandırmasında ikincil DBMS örneğini çalıştıran VM ile birlikte, büyük bir işlem yapmak isteyebileceğiniz için ikincil VM 'yi M serisi VM olarak durdurup yeniden başlatmanız yeterli değildir. VM 'yi taraın. Bunun nedeni, d serisi VM 'Lerin ve Ev3 serisi VM 'Lerin farklı donanımlarda ve farklı ölçek birimlerinde çalıştığı bir nedendir. Yeni bir kullanılabilirlik kümesi oluşturmanız, depolama alanını silmeden ikincil Ev3 serisi VM 'yi silmeniz ve VM 'yi yeni kullanılabilirlik kümesine d serisi VM olarak yeniden dağıtmanız gerekir.

#### <a name="printing-on-a-local-network-printer-from-sap-instance-in-azure"></a>Azure 'da SAP örneğinden yerel bir ağ yazıcısında yazdırma

##### <a name="printing-over-tcpip-in-cross-premises-scenario"></a>Şirket Içi senaryoda TCP/IP üzerinden yazdırma

Şirket içi TCP/IP tabanlı ağ yazıcılarınızı bir Azure VM 'de ayarlamak, bir VPN siteden siteye tünele veya ExpressRoute bağlantınızın kurulu olduğu varsayıldığında kurumsal ağınızdan aynı genel bir ayardır.

---
> ![Windows][Logo_Windows] Windows
>
> Bunu yapmak için:
>
> * Bazı ağ yazıcıları, yazıcınızı bir Azure VM 'de ayarlamayı kolaylaştıran bir yapılandırma sihirbazıyla gelir. Yazıcıyla dağıtılan bir sihirbaz yazılımı yoksa, yazıcıyı ayarlamaya yönelik el ile yeni bir TCP/IP yazıcı bağlantı noktası oluşturulması gerekir.
> * Denetim Masası-> cihazları ve yazıcıları açın-> bir yazıcı ekleyin
> * TCP/IP adresi veya ana bilgisayar adı kullanarak yazıcı ekle seçeneğini belirleyin
> * Yazıcının IP adresini yazın
> * Yazıcı bağlantı noktası standart 9100
> * Gerekirse uygun yazıcı sürücüsünü el ile yükleyebilirsiniz.
>
> ![Linux][Logo_Linux] Linux
>
> * Windows için olduğu gibi, yalnızca bir ağ yazıcısı yüklemek için standart yordamı izleyin
> * [SUSE](https://www.suse.com/documentation/sles-12/book_sle_deployment/data/sec_y2_hw_print.html) veya [Red Hat](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/sec-Printer_Configuration.html) için genel Linux kılavuzlarını ve yazıcı ekleme hakkında Oracle Linux izlemeniz yeterlidir.
>
>

---
![Ağ yazdırma][planning-guide-figure-2200]

##### <a name="host-based-printer-over-smb-shared-printer-in-cross-premises-scenario"></a>Şirket Içi senaryoda SMB (paylaşılan yazıcı) üzerinden ana bilgisayar tabanlı yazıcı

Ana bilgisayar tabanlı yazıcılar, tasarıma göre ağa uyumlu değildir. Ancak, yazıcı yerleşik bir bilgisayara bağlandığı sürece, ana bilgisayar tabanlı bir yazıcı, bir ağdaki bilgisayarlar arasında paylaşılabilir. Şirket ağınızı siteden siteye veya ExpressRoute 'A bağlayın ve yerel yazıcınızı paylaşabilirsiniz. SMB protokolü, ad hizmeti olarak DNS yerine NetBIOS kullanır. NetBIOS ana bilgisayar adı, DNS ana bilgisayar adından farklı olabilir. Standart durum, NetBIOS ana bilgisayar adının ve DNS ana bilgisayar adının aynı olduğu durumdur. DNS etki alanı NetBIOS ad alanında anlamlı değildir. Buna uygun olarak, DNS ana bilgisayar adı ve DNS etki alanından oluşan tam DNS ana bilgisayar adı Netbıos ad alanında kullanılmamalıdır.

Yazıcı paylaşma, ağda benzersiz bir adla tanımlanır:

* SMB konağının ana bilgisayar adı (her zaman gerekli).
* Paylaşımın adı (her zaman gerekli).
* Yazıcı paylaşımının SAP sistemiyle aynı etki alanında olmaması durumunda etki alanının adı.
* Ayrıca, yazıcı paylaşımında erişim için bir Kullanıcı adı ve parola gerekebilir.

Nasıl yapılır:

---
> ![Windows][Logo_Windows] Windows
>
> Yerel yazıcınızı paylaşabilirsiniz.
> Azure VM 'de Windows Gezgini ' ni açın ve yazıcının Share adını yazın.
> Bir yazıcı yükleme Sihirbazı, yükleme sürecinde size kılavuzluk eder.
>
> ![Linux][Logo_Linux] Linux
>
> Linux 'ta ağ yazıcılarını yapılandırmaya ilişkin bazı örnekler aşağıda verilmiştir veya Linux 'ta yazdırmayla ilgili bir bölüm dahil olmak üzere. VM bir VPN 'nin parçası olduğu sürece, Azure Linux VM 'de aynı şekilde çalışır:
>
> * SLES<https://en.opensuse.org/SDB:Printing_via_SMB_(Samba)_Share_or_Windows_Share>
> * RHEL veya Oracle Linux<https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/System_Administrators_Guide/sec-Printer_Configuration.html#s1-printing-smb-printer>
>
>

---
##### <a name="usb-printer-printer-forwarding"></a>USB yazıcı (yazıcı iletme)

Azure 'da Uzak Masaüstü Hizmetleri kullanıcılara, uzak bir oturumdaki yerel yazıcı cihazlarına erişim sağlama olanağı yoktur.

---
> ![Windows][Logo_Windows] Windows
>
> Windows ile yazdırma hakkında daha fazla ayrıntı için şu adreste bulabilirsiniz <https://technet.microsoft.com/library/jj590748.aspx>:.
>
>

---
#### <a name="integration-of-sap-azure-systems-into-correction-and-transport-system-tms-in-cross-premises"></a>SAP Azure sistemlerini şirket içinde düzeltme ve taşıma sistemi (TMS) ile tümleştirme

SAP değişikliği ve taşıma sisteminin (TMS), yatay içindeki sistemler arasında aktarım isteklerini dışarı ve içeri aktarmak üzere yapılandırılması gerekir. SAP sisteminin (DEV) geliştirme örneklerinin Azure 'da bulunduğunu, ancak kalite güvencesi (QA) ve üretken sistemleri (PRD) Şirket içi olduğunu varsayalım. Ayrıca, merkezi bir aktarım dizini olduğunu varsayalım.

##### <a name="configuring-the-transport-domain"></a>Taşıma etki alanını yapılandırma

Aktarım etki alanı denetleyicisini [yapılandırma](https://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm)bölümünde açıklandığı gibi, aktarım etki alanı denetleyicisi olarak atadığınız sistemde taşıma etki alanınızı yapılandırın. Bir sistem kullanıcısı TMSADM oluşturulur ve gerekli RFC hedefi oluşturulur. İşlem SM59 kullanarak bu RFC bağlantılarını kontrol edebilirsiniz. Ana bilgisayar adı çözümlemesi, aktarım etki alanınız genelinde etkinleştirilmelidir.

Nasıl yapılır:

* Senaryomızda, şirket içi QAS sisteminin CTS etki alanı denetleyicisi olacağını karardık. İşlem STMS çağrısı yapın. TMS iletişim kutusu görüntülenir. Taşıma etki alanını Yapılandır iletişim kutusu görüntülenir. (Bu iletişim kutusu yalnızca henüz bir taşıma etki alanını yapılandırmadıysanız görüntülenir.)
* Otomatik olarak oluşturulan tmsadm Kullanıcı (SM59-> ABAP bağlantısı-> TMSADM@E61.DOMAIN_E61 -> Ayrıntılar-> Utilities (d)-> Yetkilendirme testi) olduğundan emin olun. İşlemin ilk ekranında, bu SAP sisteminin artık burada gösterildiği gibi aktarım etki alanı denetleyicisi olarak çalıştığını göstermesi gerekir:

![Etki alanı denetleyicisindeki işlem STMS başlangıç ekranı][planning-guide-figure-2300]

#### <a name="including-sap-systems-in-the-transport-domain"></a>Aktarım etki alanına SAP sistemleri dahil

Bir aktarım etki alanında SAP sisteminin dahil edilmesi sırası şu şekilde görünür:

* Azure 'daki GELIŞTIRME sisteminde, aktarım sistemine (Istemci 000) gidin ve işlem STMS ' ı çağırın. İletişim kutusundan diğer yapılandırma ' yı seçin ve Içerme sistemiyle birlikte etki alanına devam edin. Etki alanı denetleyicisini hedef konak olarak belirtin ([Aktarım etki ALANıNDAKI SAP sistemleri dahil](https://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0c17acc11d1899e0000e829fbbd/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)). Sistem artık aktarım etki alanına dahil edilmesini bekliyor.
* Güvenlik nedenleriyle isteğinizi onaylamak için etki alanı denetleyicisine geri gitmeniz gerekir. Sisteme genel bakış ve bekleyen sistemi Onayla ' yı seçin. Sonra istemi onaylayın ve yapılandırma dağıtılır.

Bu SAP sistemi artık aktarım etki alanındaki tüm diğer SAP sistemleri hakkında gerekli bilgileri içermektedir. Aynı zamanda, yeni SAP sisteminin adres verileri diğer tüm SAP sistemlerine gönderilir ve SAP sistemi aktarım denetimi programının aktarım profiline girilir. Etki alanının aktarım dizinine yönelik RFC 'Lerin ve erişimin çalışıp çalışmadığını denetleyin.

Belge [değişikliği ve taşıma sisteminde](https://help.sap.com/saphelp_nw70ehp3/helpdata/en/48/c4300fca5d581ce10000000a42189c/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)açıklandığı gibi, aktarım sisteminizin yapılandırmasına devam edin.

Nasıl yapılır:

* Şirket içi STMS 'nizin doğru yapılandırıldığından emin olun.
* Aktarım etki alanı denetleyicisinin ana bilgisayar adının Azure 'daki sanal makineniz tarafından çözümlenebildiğinden ve tam olarak Visa olduğundan emin olun.
* Çağrı işlemi STMS-> diğer yapılandırma-> dahil etme sistemi.
* Şirket içi TMS sisteminde bağlantıyı onaylayın.
* Taşıma yollarını, grupları ve katmanları her zamanki gibi yapılandırın.

Siteden siteye bağlı çapraz şirket senaryolarında, şirket içi ve Azure arasındaki gecikme hala önemli olabilir. Geliştirme ve test sistemleri aracılığıyla nesne taşıma sırasını izliyoruz veya farklı sistemlere taşıma veya destek paketleri uygulamayı düşünürken, merkezi aktarım dizininin konumuna bağlı olduğunu fark edersiniz. sistemlerden bazıları merkezi aktarım dizinindeki yüksek gecikmeli okuma veya veri yazma ile karşılaşacaktır. Bu durum, farklı sistemlerin veri merkezleri arasında önemli mesafe ile farklı veri merkezlerinde yayıldığı SAP yatay yapılandırmalarına benzer.

Bu gecikme süresini aşmak ve sistemin aktarım dizininden okuma ya da yazma sırasında hızla çalışmasını sağlamak için, iki STMS taşıma etki alanı (bir tane şirket içi ve biri Azure 'daki sistemlerle bir tane) ayarlayabilir ve aktarım etki alanlarını bağlayabilirsiniz. SAP TMS 'de bu kavramın arkasındaki ilkeleri açıklayan lütfen bu belgeleri denetleyin: <https://help.sap.com/saphelp_me60/helpdata/en/c4/6045377b52253de10000009b38f889/content.htm?frameset=/en/57/38dd924eb711d182bf0000e829fbfe/frameset.htm>.

Nasıl yapılır:

* İşlem STMS kullanarak her konumda (Şirket içi ve Azure) bir taşıma etki alanı ayarlama<https://help.sap.com/saphelp_nw70ehp3/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm>
* Etki alanlarını bir etki alanı bağlantısıyla bağlayın ve iki etki alanı arasındaki bağlantıyı onaylayın.
  <https://help.sap.com/saphelp_nw73ehp1/helpdata/en/a3/139838280c4f18e10000009b38f8cf/content.htm>
* Yapılandırmayı bağlantılı sisteme dağıtın.

#### <a name="rfc-traffic-between-sap-instances-located-in-azure-and-on-premises-cross-premises"></a>Azure 'da ve şirket içinde bulunan SAP örnekleri arasında RFC trafiği (Şirket Içi)

Şirket içinde ve Azure 'da olan sistemler arasındaki RFC trafiği çalışmalıdır. Hedef sisteme yönelik bir RFC bağlantısı tanımlamanız gereken kaynak sistemde bir bağlantı çağrı işlemi SM59 ayarlamak için. Yapılandırma, bir RFC bağlantısının standart kurulumuna benzerdir.

Şirketler arası senaryoda, birbirleriyle iletişim kurması gereken SAP sistemlerini çalıştıran VM 'Lerin aynı etki alanında olduğunu varsayıyoruz. Bu nedenle, SAP sistemleri arasında bir RFC bağlantısı kurulumu, kurulum adımlarından ve şirket içi senaryolardaki girişlerden farklı değildir.

#### <a name="accessing-local-fileshares-from-sap-instances-located-in-azure-or-vice-versa"></a>Azure 'da bulunan SAP örneklerinden yerel dosya paylaşımlarına erişme veya bunun tersi

Azure 'da bulunan SAP örneklerinin, şirket içi Şirket içindeki dosya paylaşımlarına erişmesi gerekir. Ayrıca, şirket içi SAP örneklerinin Azure 'da bulunan dosya paylaşımlarına erişmesi gerekir. Dosya paylaşımlarını etkinleştirmek için, yerel sistemde izinleri ve paylaşım seçeneklerini yapılandırmanız gerekir. Azure ile veri merkeziniz arasındaki VPN veya ExpressRoute bağlantısı üzerindeki bağlantı noktalarını açmayı unutmayın.

## <a name="supportability"></a>Desteklenebilirlik

### <a name="6f0a47f3-a289-4090-a053-2521618a28c3"></a>SAP için Azure uzantısı

Görev açısından kritik SAP sistemlerinin bazı parçalarını, VM 'lerde yüklü olan SAP konak Aracısı örneklerine akışa almak için, SAP için bir Azure (VM) uzantısının dağıtılan VM 'Ler için yüklenmesi gerekir. SAP 'nin talepleri SAP uygulamalarına özel olduğundan, Microsoft gerekli işlevselliği Azure 'a genel olarak uygulamamaya karar vermemeye karar verdi, ancak müşterilerin gerekli VM uzantısını ve yapılandırmasını çalıştıran sanal makinelere dağıtması için bırakın Azure 'da. Bununla birlikte, SAP için Azure VM uzantısının dağıtım ve yaşam döngüsü yönetimi, Azure tarafından çoğunlukla otomatikleştirilir.

#### <a name="solution-design"></a>Çözüm tasarımı

SAP konak Aracısı 'nı etkinleştirmek için geliştirilen çözüm, Azure VM Aracısı ve uzantı çerçevesinin mimarisine dayanır. Azure VM Aracısı ve uzantı çerçevesinin fikri, bir VM içindeki Azure VM Uzantısı galerisinde bulunan yazılım uygulamalarının yüklenmesine izin vermenizde yarar vardır. Bu kavramın arkasındaki prensibi, özel işlevlerin bir VM 'ye dağıtılması ve dağıtım zamanında bu yazılımın yapılandırılması için (SAP için Azure uzantısı gibi durumlarda) izin vermenizde yarar vardır.

VM 'de belirli Azure VM uzantılarının işlenmesine izin veren ' Azure VM Aracısı ', Azure portal VM oluşturma sırasında varsayılan olarak Windows VM 'lerine eklenir. SUSE, Red hat veya Oracle Linux söz konusu olduğunda, VM Aracısı zaten Azure Marketi görüntüsünün bir parçasıdır. Bir tek durumda, bir Linux sanal makinesini Şirket içinden Azure 'a karşıya yükleyebilir ve VM aracısının el ile yüklenmesi gerekir.

Azure 'da SAP konak aracısına Azure altyapı bilgilerini sağlamak için çözümün temel yapı taşları şöyle görünür:

![Microsoft Azure uzantısı bileşenleri][planning-guide-figure-2400]

Yukarıdaki blok diyagramında gösterildiği gibi, çözümün bir parçası Azure VM görüntüsünde ve Azure uzantı galerisinde barındırılır ve bu da Azure Işlemleri tarafından yönetilen küresel olarak çoğaltılan bir depodur. SAP için Azure 'un yeni sürümlerini yayımlamak üzere SAP 'nin Azure uygulamasında çalışan SAP/MS ekibinin sorumluluğundadır.

Yeni bir Windows VM dağıtırken, Azure VM Aracısı sanal makineye otomatik olarak eklenir. Bu aracının işlevi, VM 'lerin Azure uzantılarının yükleme ve yapılandırmasını koordine etmek için kullanılır. Linux VM 'Leri için Azure VM Aracısı zaten Azure Marketi işletim sistemi görüntüsünün bir parçasıdır.

Ancak, hala müşteri tarafından yürütülmesi gereken bir adım vardır. Bu, performans koleksiyonunun etkinleştirilmesi ve yapılandırması. Yapılandırma ile ilgili işlem, bir PowerShell betiği veya CLı komutu tarafından otomatikleştirilir. PowerShell betiği, [dağıtım kılavuzunda][deployment-guide]açıklandığı gibi Microsoft Azure betik merkezi 'ne indirilebilir.

SAP için Azure uzantısının genel mimarisi şöyle görünür:

![SAP için Azure uzantısı ][planning-guide-figure-2500]

**Dağıtım sırasında bu PowerShell cmdlet 'lerini veya CLı komutunu kullanmanın ayrıntılı adımları için nasıl yapılır ve ayrıntılı adımlar için [dağıtım kılavuzunda][deployment-guide]verilen yönergeleri izleyin.**

### <a name="integration-of-azure-located-sap-instance-into-saprouter"></a>Azure bulunan SAP örneğinin, SAProuter ile tümleştirilmesi

Azure 'da çalışan SAP örneklerinin de SAProuter 'tan erişilebilir olması gerekir.

![SAP-yönlendirici ağ bağlantısı][planning-guide-figure-2600]

Bir SAProuter, doğrudan IP bağlantısı yoksa katılım sistemleri arasında TCP/IP iletişimine izin vermez. Bu, ağ düzeyinde iletişim ortakları arasında uçtan uca bir bağlantının gerekli olmadığı avantajını sağlar. SAProuter, varsayılan olarak 3299 numaralı bağlantı noktasını dinliyor.
SAP örneklerini bir SAProuter aracılığıyla bağlamak için, bir bağlantı girişimi ile SAProuter dize ve ana bilgisayar adına izin vermeniz gerekir.

## <a name="sap-netweaver-as-java"></a>Java olarak SAP NetWeaver

Bu nedenle belgenin odağının genel veya SAP NetWeaver ABAP yığınında SAP NetWeaver. Bu küçük bölümde SAP Java yığınının belirli konuları listelenmiştir. En önemli SAP NetWeaver Java özel tabanlı uygulamalardan biri SAP Enterprise Portal. SAP PI ve SAP çözüm Yöneticisi gibi diğer SAP NetWeaver tabanlı uygulamalar hem SAP NetWeaver ABAP hem de Java yığınlarını kullanır. Bu nedenle, SAP NetWeaver Java Stack ile ilgili belirli yönleri de göz önünde bulundurmanız gerekir.

### <a name="sap-enterprise-portal"></a>SAP Enterprise Portal

Şirketler arası senaryolarda dağıtım yapıyorsanız, bir Azure sanal makinesinde SAP portalının kurulumu, şirket içi bir yüklemeden farklı değildir. DNS, şirket içi tarafından yapıldığından, tek örneklerin bağlantı noktası ayarları şirket içi olarak yapılandırılmış şekilde yapılabilir. Bu belgede açıklanan öneriler ve kısıtlamalar, SAP Enterprise Portal veya genel olarak SAP NetWeaver Java Stack gibi bir uygulama için geçerlidir.

![Sunulan SAP portalı][planning-guide-figure-2700]

Bazı müşterilerin özel bir dağıtım senaryosu, sanal makine Konağı, siteden siteye VPN tüneli veya ExpressRoute aracılığıyla şirket ağına bağlıyken SAP Enterprise Portal Internet 'e doğrudan maruz kalmaktır. Böyle bir senaryoda, belirli bağlantı noktalarının açık olduğundan ve güvenlik duvarı veya ağ güvenlik grubu tarafından engellenmediğinden emin olmanız gerekir. 

İlk portal URI 'si, http (s):`<Portalserver`>: bağlantı noktasının ' de <https://help.sap.com/saphelp_nw70ehp1/helpdata/de/a2/f9d7fed2adc340ab462ae159d19509/frameset.htm>SAP tarafından belgelendiği şekilde oluşturulduğu 5xx00/ırj.

![Uç nokta yapılandırması][planning-guide-figure-2800]

SAP Enterprise Portal URL ve/veya bağlantı noktalarını özelleştirmek istiyorsanız, bu belgeleri denetleyin:

* [Portal URL 'sini Değiştir](https://wiki.scn.sap.com/wiki/display/EP/Change+Portal+URL)
* [Varsayılan bağlantı noktası numaralarını, Portal bağlantı noktası numaralarını Değiştir](https://wiki.scn.sap.com/wiki/display/NWTech/Change+Default++port+numbers%2C+Portal+port+numbers)

## <a name="high-availability-ha-and-disaster-recovery-dr-for-sap-netweaver-running-on-azure-virtual-machines"></a>Azure sanal makinelerde çalışan SAP NetWeaver için yüksek kullanılabilirlik (HA) ve olağanüstü durum kurtarma (DR)

### <a name="definition-of-terminologies"></a>Terimlerin tanımı

**Yüksek kullanılabilirlik (ha)** terimi, BT Hizmetleri 'nin **aynı** yerde, yedekli, hataya dayanıklı veya yük devretme korumalı bileşenler aracılığıyla iş sürekliliği sağlayarak bu kesintiyi en aza indiren bir teknoloji kümesiyle ilgilidir veri merkezi. Bu durumda, bir Azure bölgesi içinde.

**Olağanüstü durum kurtarma (Dr)** Ayrıca, BT Hizmetleri kesintisini ve bunların kurtarmasını, ancak genellikle yüzlerce kilometre kilometrede bulunan **farklı** veri merkezlerinde en aza indirme için de hedefleme. Bizim örneğimizde, genellikle aynı coğrafi bölge içindeki farklı Azure bölgeleri arasında veya müşteri olarak sizin tarafınızdan kurulacaktır.

### <a name="overview-of-high-availability"></a>Yüksek kullanılabilirliğe genel bakış

Azure 'da SAP yüksek kullanılabilirliği hakkındaki tartışmayı iki parçaya ayırabiliriz:

* **Azure altyapı yüksek kullanılabilirlik**, örneğin Işlem (VM), ağ, depolama vb. ve SAP uygulama kullanılabilirliğini artırma avantajları.
* SAP **Uygulama yüksek kullanılabilirliği**, ÖRNEĞIN, SAP yazılım bileşenleri IÇIN bir ha:
  * SAP uygulama sunucuları
  * SAP ASCS/SCS örneği
  * DB sunucusu

Azure altyapı HA ile nasıl birleştirilebilecek.

Azure 'da SAP yüksek kullanılabilirlik, şirket içi fiziksel veya sanal ortamda SAP yüksek kullanılabilirliğe kıyasla bazı farklılıklar içerir. SAP 'deki aşağıdaki kağıda Windows üzerinde sanallaştırılmış ortamlarda standart SAP yüksek kullanılabilirlik yapılandırmalarının açıklanmaktadır: <https://scn.sap.com/docs/DOC-44415>. Linux için Windows için mevcut olduğu gibi bir sapinst ile tümleşik SAP-HA yapılandırması yoktur. Linux için şirket içi SAP HA hakkında daha fazla bilgi edinin: <https://scn.sap.com/docs/DOC-8541>.

### <a name="azure-infrastructure-high-availability"></a>Azure altyapı yüksek kullanılabilirliği

Şu anda% 99,9 olan tek VM SLA 'Sı var. Tek bir VM 'nin kullanılabilirliğinin nasıl görünebileceğini fikir almak için, farklı kullanılabilir Azure SLA 'ların ürününü oluşturabilirsiniz: <https://azure.microsoft.com/support/legal/sla/>.

Hesaplamanın temeli ayda 30 gün veya 43200 dakikadır. Bu nedenle,% 0,05 kesinti süresi 21,6 dakikaya karşılık gelir. Her zamanki gibi, farklı hizmetlerin kullanılabilirliği aşağıdaki şekilde çarpılacaktır:

(Kullanılabilirlik hizmeti #1/100) * (kullanılabilirlik hizmeti #2/100) * (kullanılabilirlik hizmeti #3/100) 

Like

(99,95/100) * (99,9/100) * (99,9/100) = 0,9975 veya genel olarak% 99,75 kullanılabilirliği.

#### <a name="virtual-machine-vm-high-availability"></a>Sanal makine (VM) yüksek kullanılabilirlik

Sanal makinelerinizin kullanılabilirliğini etkileyebilecek iki tür Azure platform olayı vardır: planlı bakım ve planlanmamış bakım.

* Planlı bakım olayları, Microsoft tarafından, sanal makinelerinizin üzerinde çalıştığı platform altyapısının genel güvenilirlik, performans ve güvenliğini geliştirmek amacıyla temel alınan Azure platformunda yapılan düzenli güncelleştirmelerdir.
* Planlanmamış bakım olayları, sanal makinenizi temel alan donanım veya fiziksel altyapı bir şekilde hata verdi olduğunda meydana gelir. Buna yerel ağ hataları, yerel disk hataları veya raf düzeyinde diğer hatalar dahildir. Böyle bir hata algılandığında, Azure platformu sanal makinenizi sanal makinenizi barındıran uygun olmayan fiziksel sunucudan sağlıklı fiziksel bir sunucuya otomatik olarak geçirilir. Bu tür olaylar nadirdir, ancak sanal makinenizin yeniden başlatılmasına da neden olabilir.

Bu belgelerde daha fazla ayrıntı bulabilirsiniz:<https://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="azure-storage-redundancy"></a>Azure depolama artıklığı

Microsoft Azure Depolama hesabınızdaki veriler, dayanıklılık ve yüksek kullanılabilirlik sağlamak için her zaman çoğaltılır, bu da geçici donanım arızalarına karşın Azure Depolama SLA 'sını de karşılamıyor.

Azure depolama, verilerin üç görüntüsünü varsayılan olarak tutarken, birden çok Azure diski arasında RAID5 veya RAID1 gerekli değildir.

Bu makalede daha fazla ayrıntı bulabilirsiniz:<https://azure.microsoft.com/documentation/articles/storage-redundancy/>

#### <a name="utilizing-azure-infrastructure-vm-restart-to-achieve-higher-availability-of-sap-applications"></a>SAP uygulamalarında daha yüksek kullanılabilirlik elde etmek için Azure altyapı VM yeniden başlatması kullanma

Linux 'ta Windows Server Yük Devretme Kümelemesi (WSFC) veya Paceyapıcısı gibi işlevleri kullanmamaya karar verirseniz (Şu anda yalnızca SLES 12 ve üzeri için desteklenir), Azure VM yeniden başlatması, bir SAP sistemini planlanmış ve planlanmamış kapalı kalma süresine karşı korumak için kullanılır fiziksel sunucu altyapısı ve temel alınan genel Azure platformu.

> [!NOTE]
> Azure VM 'nin yeniden başlatılmasının birincil olarak uygulamaları DEĞIL, VM 'Leri koruduğu bahsetmek önemlidir. VM yeniden başlatma, SAP uygulamaları için yüksek kullanılabilirlik sunmaz, ancak belirli bir altyapı kullanılabilirliği düzeyi ve bu nedenle SAP sistemlerinin dolaylı olarak daha yüksek kullanılabilirliğini sunmaktadır. Planlı veya planlanmamış bir ana bilgisayar kesintisinden sonra VM 'yi yeniden başlatmak için gereken süre için SLA yoktur. Bu nedenle, bu yüksek kullanılabilirlik yöntemi, (A) SCS veya DBMS gibi bir SAP sisteminin kritik bileşenleri için uygun değildir.
>
>

Yüksek kullanılabilirlik için başka bir önemli altyapı öğesi depolama alanı. Örneğin Azure Storage SLA% 99,9 kullanılabilir. Biri, disklerinin bulunduğu tüm VM 'Leri tek bir Azure depolama hesabına dağıttığında, olası Azure depolama alanı, bu Azure depolama hesabına yerleştirilmiş tüm VM 'lerin ve ayrıca bu sanal makinelerin içinde çalışan tüm SAP bileşenleri üzerinde kullanılamamasına neden olur.  

Tüm VM 'Leri tek bir Azure Storage hesabına koymak yerine, her sanal makine için ayrılmış depolama hesapları da kullanabilirsiniz ve bu şekilde, birden çok bağımsız Azure depolama hesabı kullanarak genel VM ve SAP uygulamasının kullanılabilirliğini artırabilirsiniz.

Azure yönetilen diskler, eklendiği sanal makinenin hata etki alanına otomatik olarak yerleştirilir. İki sanal makineyi bir kullanılabilirlik kümesine yerleştirirseniz ve yönetilen diskler kullanıyorsanız, platform yönetilen diskleri farklı hata etki alanlarına dağıtmayı da üstlenir. Premium Depolama kullanmayı planlıyorsanız, diskleri yönetme ' nin kullanılması da önerilir.

Azure altyapı HA ve depolama hesapları kullanan bir SAP NetWeaver sisteminin örnek mimarisi şuna benzeyebilir:

![SAP uygulamasına daha yüksek kullanılabilirlik elde etmek için Azure altyapı HA kullanma][planning-guide-figure-2900]

Azure altyapı HA ve yönetilen diskler kullanan bir SAP NetWeaver sisteminin örnek mimarisi şuna benzeyebilir:

![SAP uygulamasına daha yüksek kullanılabilirlik elde etmek için Azure altyapı HA kullanma][planning-guide-figure-2901]

Kritik SAP bileşenleri için şu ana kadar şunu aldık:

* SAP uygulama sunucularının yüksek kullanılabilirliği (AS)

  SAP uygulama sunucusu örnekleri, yedekli bileşenlerdir. Her SAP örneği, farklı bir Azure hata ve yükseltme etki alanında çalışan kendi VM 'sine dağıtılır (bkz. bölümler [hata etki alanları][planning-guide-3.2.1] ve [yükseltme etki alanları][planning-guide-3.2.2]). Bu, Azure kullanılabilirlik kümeleri kullanılarak yapılır (bkz. bölüm [Azure kullanılabilirlik kümeleri][planning-guide-3.2.3]). Bir Azure hata ya da yükseltme etki alanının olası planlanmış veya planlanmamış kullanım dışı kalması, sınırlı sayıda VM 'nin SAP olarak örnekleri olarak kullanılamamasına neden olur.

  Her SAP, örnek olarak kendi Azure depolama hesabına yerleştirilir. bir Azure Storage hesabının olası kullanım dışı kalması, örnek olarak SAP olarak yalnızca bir VM 'nin kullanılamamasına neden olur. Ancak, bir Azure aboneliği içinde Azure depolama hesabı sınırının olduğunu unutmayın. VM yeniden başlatıldıktan sonra (A) bir SCS örneğinin otomatik olarak başlamasını sağlamak için, [sap örnekleri Için autostart][planning-guide-11.5]' ı kullanarak bölümünde açıklanan, (a) SCS örneği başlangıç profilinde otomatik başlatma parametresini ayarladığınızdan emin olun.
  Daha fazla ayrıntı için lütfen [SAP uygulama sunucuları için bölüm yüksek kullanılabilirliğini][planning-guide-11.4.1] okuyun.

  Yönetilen diskler kullanıyor olsanız da bu diskler bir Azure depolama hesabında depolanır ve depolama kesintisi durumunda kullanılamayabilir.

* *Daha yüksek* SAP (A) SCS örneği kullanılabilirliği

  Burada, VM 'yi yüklü SAP (A) SCS örneğiyle korumak için Azure VM yeniden başlatması kullanıyoruz. Azure 'da planlanmış veya planlanmamış kapalı kalma süresi söz konusu olduğunda, VM 'Ler kullanılabilir başka bir sunucuda yeniden başlatılır. Daha önce belirtildiği gibi, Azure VM yeniden başlatması öncelikle VM 'Leri korur, bu durumda (A) SCS örneği. VM yeniden başlatıldığında, SAP (A) SCS örneği için dolaylı olarak daha yüksek kullanılabilirliğe ulaşacağız. Sanal makine yeniden başlatıldıktan sonra (A) SCS örneğinin otomatik olarak başlamasını sağlamak için, [sap örnekleri Için autostart 'ı kullanma][planning-guide-11.5]bölümünde açıklanan, (a) SCS örneği başlangıç profilinde otomatik başlatma parametresi ayarladığınızdan emin olun. Yani, (A) SCS örneği tek bir VM 'de çalışan tek bir hata noktası (SPOF) olarak tüm SAP yatay kullanılabilirliği için belirleyici bir faktör olacaktır.

* *Daha yüksek* DBMS sunucusunun kullanılabilirliği

  Burada SAP (A) SCS örnek kullanım örneğine benzer şekilde, VM 'yi yüklü olan DBMS yazılımıyla korumak için Azure VM yeniden başlatma 'yı kullanıyoruz ve VM yeniden başlatma aracılığıyla DBMS yazılımının daha yüksek kullanılabilirliğini elde ediyoruz.
  Tek bir VM 'de çalışan DBMS de bir SOF olur ve tüm SAP yatay kullanılabilirliği için belirleyici bir etkendir.

### <a name="sap-application-high-availability-on-azure-iaas"></a>Azure IaaS 'de SAP uygulaması yüksek kullanılabilirliği

Tam SAP sistem yüksek kullanılabilirlik elde etmek için, tüm kritik SAP sistem bileşenlerini (örneğin, yedekli SAP uygulama sunucuları) ve SAP (A) SCS örneği ve DBMS gibi benzersiz bileşenleri (örneğin, tek başarısızlık noktası) korumanız gerekir.

#### <a name="5d9d36f9-9058-435d-8367-5ad05f00de77"></a>SAP uygulama sunucuları için yüksek kullanılabilirlik

SAP uygulama sunucuları/iletişim örnekleri için, belirli bir yüksek kullanılabilirlik çözümü düşünmek gerekli değildir. Yüksek kullanılabilirlik, artıklık tarafından sağlanır ve bu sayede farklı sanal makinelerde yeterince yer vardır. Bunların tümü, planlanan bakım kapalı kalma süresi boyunca VM 'Lerin aynı anda güncelleştirilebileceği kaçınmak için aynı Azure kullanılabilirlik kümesine yerleştirilmelidir. Azure ölçek birimi içindeki farklı yükseltme ve hata etki alanları üzerinde oluşturulan temel işlevsellik, bölüm [yükseltme etki alanlarında][planning-guide-3.2.2]zaten sunulmuştur. Azure kullanılabilirlik kümeleri, bu belgenin bölüm [Azure kullanılabilirlik kümelerinde][planning-guide-3.2.3] sunulmuştur.

Azure ölçek birimi içindeki bir Azure kullanılabilirlik kümesi tarafından kullanılabilen, sonsuz sayıda hata ve yükseltme etki alanı yoktur. Bu, birden çok VM 'nin bir kullanılabilirlik kümesine yerleştirilmesi, daha önce veya daha sonraki bir VM 'nin aynı hata veya yükseltme etki alanında sona ereceği anlamına gelir.

Ayrılmış VM 'lerinde birkaç SAP uygulama sunucusu örneği dağıtma ve beş yükseltme etki alanı olduğunu varsayarsak, aşağıdaki resim sonunda ortaya çıktı. Bir kullanılabilirlik kümesi içindeki en fazla hata ve güncelleştirme etki alanı sayısı gelecekte değişebilir:

![Azure 'da SAP uygulama sunucularının HA][planning-guide-figure-3000]

Bu belgelerde daha fazla ayrıntı bulabilirsiniz:<https://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="high-availability-for-sap-central-services-on-azure"></a>Azure 'da SAP Merkezi Hizmetleri için yüksek kullanılabilirlik

Azure 'daki SAP merkezi hizmetlerinin yüksek kullanılabilirlik mimarisi için, giriş bilgileri olarak [SAP NetWeaver Için yüksek kullanılabilirlik mimarisi ve senaryolar](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) makalesine bakın. Bu makale, belirli işletim sistemleri için daha ayrıntılı açıklamalara işaret eder.

#### <a name="high-availability-for-the-sap-database-instance"></a>SAP veritabanı örneği için yüksek kullanılabilirlik

Tipik SAP DBMS HA kurulumu, DBMS yüksek kullanılabilirlik işlevinin, etkin DBMS örneğinden ikinci VM 'ye verileri pasif bir DBMS örneğine çoğaltmak için kullanıldığı iki DBMS VM 'yi temel alır.

DBMS 'nin genel ve özel DBMS için yüksek kullanılabilirlik ve olağanüstü durum kurtarma işlevlerinin yanı sıra, [DBMS dağıtım kılavuzunda][dbms-guide]de açıklanmaktadır.

#### <a name="end-to-end-high-availability-for-the-complete-sap-system"></a>Tam SAP sistemi için uçtan uca yüksek kullanılabilirlik

Aşağıda, Azure 'da Windows için bir tane ve Linux için tam SAP NetWeaver HA mimarisine yönelik iki örnek verilmiştir.

Yalnızca yönetilmeyen diskler: Birçok SAP sistemini dağıtırken ve dağıtılan VM 'lerin sayısı, abonelik başına en fazla depolama hesabı sınırını aştığdığında aşağıda açıklanan kavramların tehlikeye düşmesi gerekebilir. Bu gibi durumlarda, VM 'lerin VHD 'lerinin tek bir depolama hesabında birleştirilmesi gerekir. Genellikle, farklı SAP sistemlerinin SAP uygulama katmanı VM 'lerinin VHD 'lerini birleştirerek bunu yapabilirsiniz.  Ayrıca, tek bir Azure depolama hesabında farklı SAP sistemlerinin farklı DBMS VM 'lerinin farklı VHD 'lerini de birleştirdik. Böylece, Azure depolama hesaplarının ıOPS sınırları göz önünde tutulduğunda (<https://azure.microsoft.com/documentation/articles/storage-scalability-targets>)


##### <a name="windowslogo_windows-ha-on-windows"></a>![Windows][Logo_Windows] Windows 'da HA

![Azure IaaS 'de SQL Server SAP NetWeaver Application HA mimarisi][planning-guide-figure-3200]

Aşağıdaki Azure yapıları, altyapı sorunları ve ana bilgisayar düzeltme eki uygulama ile etkisini en aza indirmek için SAP NetWeaver sistemi için kullanılır:

* Tam sistem Azure 'da dağıtılır (gerekli-DBMS katmanı, (A) SCS örneği ve tam uygulama katmanının aynı konumda çalıştırılması gerekir).
* Tüm sistem bir Azure aboneliğinde (gerekli) çalışır.
* Tüm sistem bir Azure sanal ağı içinde çalışır (gerekli).
* Aynı sanal ağa ait tüm VM 'Ler de dahil olmak üzere bir SAP sisteminin VM 'lerinin üç kullanılabilirlik kümesine ayrılması mümkündür.
* Her katmanın (örneğin, DBMS, Ass, uygulama sunucuları) adanmış bir kullanılabilirlik kümesi kullanması gerekir.
* Bir SAP sisteminin DBMS örneklerini çalıştıran tüm VM 'Ler tek bir kullanılabilirlik kümesinde bulunur. Yerel DBMS yüksek kullanılabilirlik özellikleri kullanıldığından, SQL Server AlwaysOn veya Oracle Data Guard gibi birden çok sanal makinenin sistem başına çalıştığını varsayalım.
* DBMS örnekleri çalıştıran tüm VM 'Ler kendi depolama hesabını kullanır. DBMS verileri ve günlük dosyaları, verileri eşitlerken DBMS yüksek kullanılabilirlik işlevleri kullanılarak bir depolama hesabından başka bir depolama hesabına çoğaltılır. Tek bir depolama hesabının olmaması, tek bir SQL Windows küme düğümünde kullanılamamasına neden olur, ancak tüm SQL Server hizmeti kullanılamaz.
* Bir SAP sisteminin (A) SCS örneği çalıştıran tüm VM 'Ler tek bir kullanılabilirlik kümesidir. Bir Windows Server yük devretme kümesi (WSFC), (A) SCS örneğini korumak için bu sanal makinelerin içinde yapılandırılır.
* Bir SCS örneği çalıştıran tüm VM 'Ler kendi depolama hesabını kullanır. A SCS örnek dosyaları ve SAP Genel klasörü, bir depolama hesabından, SIOS Dataman çoğaltması kullanılarak başka bir depolama hesabına çoğaltılır. Tek bir depolama hesabının olmaması, (a) bir SCS Windows küme düğümünün kullanılamamasına neden olur, ancak (A) bir SCS hizmeti değildir.
* SAP uygulama sunucusu katmanını temsil eden tüm VM 'Ler üçüncü bir kullanılabilirlik kümesidir.
* SAP uygulama sunucuları çalıştıran tüm VM 'Ler kendi depolama hesabını kullanır. Bir depolama hesabının olmaması, diğer SAP uygulama sunucularının çalışmaya devam ettiği bir SAP uygulama sunucusu üzerinde kullanılamamasına neden olur.

Aşağıdaki şekilde, yönetilen diskler kullanılarak aynı yatay gösterilmektedir.

![Azure IaaS 'de SQL Server SAP NetWeaver Application HA mimarisi][planning-guide-figure-3201]

##### <a name="linuxlogo_linux-ha-on-linux"></a>![Linux][Logo_Linux] Linux 'ta HA

Azure 'da Linux 'ta SAP HA 'nin mimarisi, yukarıda açıklanan Windows için temelde aynıdır. Desteklenen yüksek kullanılabilirliğe sahip çözümlerin listesi için SAP Note [1928533] bölümüne bakın.

### <a name="4e165b58-74ca-474f-a7f4-5e695a93204f"></a>SAP örnekleri için autostart 'ı kullanma

SAP, VM 'deki işletim sistemi başladıktan hemen sonra SAP örneklerinin başlatılması için gereken işlevselliği önerdi. SAP Bilgi Bankası makalesi [1909114]' de tam adımlar belgelenmiştir. Ancak, örnek yeniden başlatmalarının sırası üzerinde bir denetim olmadığından, SAP 'nin birden fazla VM 'den etkilendiğini veya birden çok örnek VM başına çalıştırıldığı varsayıldığında, bu ayarı kullanmayı önermez. Bir VM 'deki bir SAP uygulama sunucusu örneğinin tipik bir Azure senaryosu ve tek bir VM 'nin yeniden başlatılması durumunda, autostart önemli değildir ve bu parametre eklenerek etkinleştirilebilir:

    Autostart = 1

SAP ABAP ve/veya Java örneğinin başlangıç profiline.

> [!NOTE]
> Autostart parametresinin bir kısmı de olabilir. Daha ayrıntılı olarak, bu parametre, örneğin ilgili Windows/Linux hizmeti başlatıldığında SAP ABAP veya Java örneğinin başlangıcını tetikler. Bu durum, işletim sisteminin önyüklemesinde büyük bir durumdur. Ancak, SAP hizmetlerinin yeniden başlatılması, SUM veya diğer güncelleştirmeler ya da yükseltmeler gibi SAP yazılım yaşam döngüsü yönetimi işlevlerinin de yaygın bir şeydir. Bu işlevler, bir örneğin otomatik olarak yeniden başlatılmasını beklemiyordu. Bu nedenle, bu tür görevler çalıştırılmadan önce autostart parametresi devre dışı bırakılmalıdır. Autostart parametresi, yoks/SCS/CI gibi kümelenmiş SAP örnekleri için de kullanılmamalıdır.
>
>

SAP örnekleri için otomatik başlatma ile ilgili ek bilgilere buradan bakın:

* [UNIX sunucu başlatma/durdurma ile birlikte SAP 'yi başlatma/durdurma](https://scn.sap.com/community/unix/blog/2012/08/07/startstop-sap-along-with-your-unix-server-startstop)
* [SAP NetWeaver yönetim aracılarını başlatma ve durdurma](https://help.sap.com/saphelp_nwpi711/helpdata/en/49/9a15525b20423ee10000000a421938/content.htm)
* [HANA veritabanının otomatik başlamasını etkinleştirme](http://www.freehanatutorials.com/2012/10/how-to-enable-auto-start-of-hana.html)

### <a name="larger-3-tier-sap-systems"></a>Daha büyük 3 katmanlı SAP sistemleri
3 katmanlı SAP yapılandırmalarının yüksek kullanılabilirlik yönleri daha önceki bölümlerde ele alınmıştır. Ancak, DBMS sunucu gereksinimlerinin Azure 'da bulunması için çok büyük olduğu, ancak SAP uygulama katmanı Azure 'a dağıtılabilecek sistemler hakkında ne olur?

#### <a name="location-of-3-tier-sap-configurations"></a>3 katmanlı SAP yapılandırmalarının konumu
Uygulama katmanının kendisini veya uygulama ve DBMS katmanını şirket içi ve Azure arasında ayırmak desteklenmez. SAP sistemi, şirket içinde ya da Azure 'da tamamen dağıtılır. Ayrıca, bazı uygulama sunucularının şirket içinde ve başkalarının Azure 'da çalıştırılmasını da desteklemez. Bu, tartışmanın başlangıç noktasıdır. Ayrıca, bir SAP sisteminin ve SAP uygulama sunucusu katmanının iki farklı Azure bölgesinde dağıtılan DBMS bileşenlerine sahip olmak için de destekliyoruz. Örneğin, Orta ABD Batı ABD ve SAP uygulama katmanındaki DBMS. Bu tür yapılandırmaların desteklenmesinin nedeni SAP NetWeaver mimarisinin gecikme süresinin duyarlılığı olur.

Bununla birlikte, geçen yılın veri merkezi iş ortaklarının Azure bölgelerine birlikte ortak konumlar geliştirildiği bir kurs üzerinden. Bu ortak konumlar genellikle bir Azure bölgesindeki fiziksel Azure veri merkezlerine yakın yakındadır. Azure 'a ExpressRoute aracılığıyla ortak konumdaki varlıkların kısa uzaklığı ve bağlantısı, 2ms 'den küçük bir gecikme süresine neden olabilir. Böyle durumlarda, bu tür bir ortak konumda DBMS katmanını (depolama SAN/NAS dahil) bulmak için Azure 'daki SAP uygulama katmanı da mümkündür. [Hana büyük örnekleri](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture). 

### <a name="offline-backup-of-sap-systems"></a>SAP sistemlerinin çevrimdışı yedeklemesi
Seçilen SAP yapılandırmasına (2 katmanlı veya 3 katmanlı) bağlı olarak, yedeklemeniz gerekir. VM 'nin içeriği ve veritabanının bir yedeklemesi vardır. DBMS ile ilgili yedeklemelerin veritabanı yöntemleriyle yapılması bekleniyor. Farklı veritabanları için ayrıntılı bir açıklama, [DBMS kılavuzunda][dbms-guide]bulunabilir. Diğer yandan, SAP verileri, bu bölümde açıklandığı gibi çevrimdışı bir şekilde (veritabanı içeriği de dahil), sonraki bölümde açıklandığı gibi, çevrimiçi olarak da yedeklenebilir.

Çevrimdışı yedekleme temelde Azure portal aracılığıyla VM 'nin kapatılmasını ve temel VM diskinin bir kopyasını ve VM 'ye eklenen tüm diskleri gerektirir. Bu, sanal makinenin ve ilişkili diskinin bir zaman noktası görüntüsünü korur. Yedeklemeleri farklı bir Azure depolama hesabına kopyalamanız önerilir. Bu nedenle, bu belgenin [Azure depolama hesapları arasında diskleri kopyalama][planning-guide-5.4.2] bölümünde açıklanan yordam geçerlidir.
Azure portal birini kullanan kapatmanın yanı sıra, burada açıklandığı gibi PowerShell veya CLı aracılığıyla da yapabilir:<https://azure.microsoft.com/documentation/articles/virtual-machines-deploy-rmtemplates-powershell/>

Bu durumun geri yüklenmesi, temel VM 'nin yanı sıra temel VM 'nin orijinal disklerini ve bağlı disklerin, kaydedilen disklerin yönetilen diskler için özgün depolama hesabına veya kaynak grubuna geri kopyalanmasını ve sonra sistemin yeniden dağıtılmasını içerir.
Bu makalede, PowerShell 'de bu işlemin nasıl betikleştiribir bir örnek gösterilmektedir:<http://www.westerndevs.com/azure-snapshots/>

Yukarıda açıklanan şekilde bir VM yedeklemesini geri yüklemek için yeni bir SAP lisansı yüklediğinizden emin olun.

### <a name="online-backup-of-an-sap-system"></a>SAP sisteminin çevrimiçi yedeklemesi

DBMS yedeklemesi, DBMS [Kılavuzu][dbms-guide]' nda AÇıKLANDıĞı gibi DBMS 'ye özgü yöntemlerle gerçekleştirilir.

SAP sisteminde bulunan diğer VM 'Ler Azure sanal makine yedekleme işlevi kullanılarak yedeklenebilir. Azure sanal makine yedeklemesi, Azure 'da tüm VM 'leri yedeklemek için standart bir yöntemdir. Azure Backup yedeklemeleri Azure 'da depolar ve VM 'nin geri yüklenmesini yeniden sağlar.

> [!NOTE]
> Ara 2015 itibariyle, VM yedeklemesini kullanarak SAP lisanslama için kullanılan benzersiz VM KIMLIĞINI değiştirmeyin. Bu, bir VM yedeğinden geri yükleme işleminin, geri yüklenen VM 'nin yeni bir VM olarak kabul edildiği ve önceki bir değişikliği değiştirmediği için yeni bir SAP lisans anahtarı yüklenmesini gerektirdiği anlamına gelir.
>
> ![Windows][Logo_Windows] Windows
>
> Teorik olarak, veritabanlarını çalıştıran sanal makineler tutarlı bir şekilde yedeklenebilir ve örneğin, SQL Server gibi, DBMS sisteminin Windows VSS 'yi (birim gölge kopyası hizmeti <https://msdn.microsoft.com/library/windows/desktop/bb968832(v=vs.85).aspx>) desteklemesi de desteklenir.
> Ancak, Azure VM yedeklemeleri, veritabanlarının zaman içindeki geri yüklemelerini temel alan mümkün değildir. Bu nedenle, Azure VM yedeklemesi 'ne güvenmek yerine DBMS işlevselliğiyle veritabanlarının yedeklerini gerçekleştirme önerisi.
>
> Azure sanal makine yedeklemesi hakkında bilgi almak için Buradan başlayın: <https://docs.microsoft.com/azure/backup/backup-azure-vms>.
>
> Diğer olanaklar, bir Azure sanal makinesinde yüklü Microsoft Data Protection Manager birleşimini kullanmak ve veritabanlarını yedeklemek/geri yüklemek için Azure Backup. Daha fazla bilgiyi burada bulabilirsiniz: <https://docs.microsoft.com/azure/backup/backup-azure-dpm-introduction>.  
>
> ![Linux][Logo_Linux] Linux
>
> Linux 'ta Windows VSS ile eşdeğer değildir. Bu nedenle, yalnızca dosya ile tutarlı yedeklemeler mümkündür ancak uygulamayla tutarlı yedeklemeler mümkün değildir. SAP DBMS yedeklemesi, DBMS işlevselliği kullanılarak yapılmalıdır. SAP ile ilgili verileri içeren dosya sistemi, örneğin, burada açıklandığı gibi tar kullanılarak kaydedilebilir:<https://help.sap.com/saphelp_nw70ehp2/helpdata/en/d3/c0da3ccbb04d35b186041ba6ac301f/content.htm>
>
>

### <a name="azure-as-dr-site-for-production-sap-landscapes"></a>Üretim SAP lanlar için Azure as DR sitesi

PARÇAAL 2014 ' den itibaren, Hyper-V, System Center ve Azure 'daki çeşitli bileşenlere yönelik uzantılar, Hyper-V ' d e bağlı olarak şirket içinde çalışan VM 'Ler için DR sitesi olarak kullanılmasını sağlar.

Bu çözümün nasıl dağıtılacağını ayrıntılandıran bir blog şurada belgelenmiştir: <https://blogs.msdn.com/b/saponsqlserver/archive/2014/11/19/protecting-sap-solutions-with-azure-site-recovery.aspx>.

## <a name="summary"></a>Özet

Azure 'da SAP sistemleri için yüksek kullanılabilirliğe ilişkin önemli noktaları şunlardır:

* Bu noktada, SAP tek hata noktası, şirket içi dağıtımlarda yapılacağından tam olarak aynı şekilde güvenli hale getirilmeyebilir. Bunun nedeni, paylaşılan disk kümelerinin üçüncü taraf yazılım kullanılmadan Azure 'da henüz derlenmemiştir.
* DBMS katmanı için, paylaşılan disk kümesi teknolojisine bağlı olmayan DBMS işlevlerini kullanmanız gerekir. Ayrıntılar, [DBMS kılavuzunda][dbms-guide]belgelenmiştir.
* Azure altyapısında veya konak bakımda hata etki alanları içindeki sorunların etkilerini en aza indirmek için Azure kullanılabilirlik kümelerini kullanmanız gerekir:
  * SAP uygulama katmanı için bir kullanılabilirlik kümesi olması önerilir.
  * SAP DBMS katmanı için ayrı bir kullanılabilirlik kümesi kullanılması önerilir.
  * Farklı SAP sistemlerinin VM 'Leri için aynı Kullanılabilirlik kümesinin uygulanması önerilmez.
  * Premium yönetilen disklerin kullanılması önerilir.
* SAP DBMS katmanının yedekleme amaçları için, [DBMS kılavuzunu][dbms-guide]kontrol edin.
* SAP Iletişim kutusu örneklerinin yedeklenmesi, basit iletişim kutusu örneklerinin yeniden dağıtılması için genellikle daha hızlı olduğundan biraz daha anlamlı hale gelir.
* SAP sisteminin genel dizinini içeren VM 'yi yedekleme ve farklı örneklerin tüm profilleri ile birlikte, Windows yedekleme ile gerçekleştirilmesi veya örneğin Linux üzerinde ık olması gerekir. Windows Server 2008 (R2) ve Windows Server 2012 (R2) arasında farklar olduğundan, daha yeni Windows Server sürümlerini kullanarak yedeklemeyi daha kolay hale getirir. Windows Server 2012 (R2) 'i Windows Konuk işletim sistemi olarak çalıştırmayı öneririz.

## <a name="next-steps"></a>Sonraki adımlar
Makaleleri okuyun:

- [SAP NetWeaver için Azure sanal makineler dağıtımı](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [SAP iş yükü için Azure sanal makineler DBMS dağıtımına yönelik konular](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [Azure 'da altyapı yapılandırma ve işlemlerini SAP HANA] (https://docs.microsoft.com/
- Azure/sanal makineler/iş yükleri/SAP/HANA-VM-Operations)
