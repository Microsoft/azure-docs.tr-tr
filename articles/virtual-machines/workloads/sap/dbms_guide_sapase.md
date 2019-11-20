---
title: SAP Ao Azure sanal makineleri SAP iş yükü için DBMS dağıtımı | Microsoft Docs
description: SAP iş yükü için SAP ASE Azure Sanal Makineler DBMS dağıtımı
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
ms.date: 07/1/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 51e97089b1de88ccf9f45b1a0f429abc0cfac9f3
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101335"
---
# <a name="sap-ase-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>SAP iş yükü için SAP ASE Azure Sanal Makineler DBMS dağıtımı

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]: https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]: https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]: https://launchpad.support.sap.com/#/notes/1558958
[1585981]: https://launchpad.support.sap.com/#/notes/1585981
[1588316]: https://launchpad.support.sap.com/#/notes/1588316
[1590719]: https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]: https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]: https://launchpad.support.sap.com/#/notes/1619967
[1750510]: https://launchpad.support.sap.com/#/notes/1750510
[1752266]: https://launchpad.support.sap.com/#/notes/1752266
[1757924]: https://launchpad.support.sap.com/#/notes/1757924
[1757928]: https://launchpad.support.sap.com/#/notes/1757928
[1758182]: https://launchpad.support.sap.com/#/notes/1758182
[1758496]: https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]: https://launchpad.support.sap.com/#/notes/1814258
[1882376]: https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]: https://launchpad.support.sap.com/#/notes/1922555
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[1941500]: https://launchpad.support.sap.com/#/notes/1941500
[1956005]: https://launchpad.support.sap.com/#/notes/1956005
[1973241]: https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]: https://launchpad.support.sap.com/#/notes/2121797
[2134316]: https://launchpad.support.sap.com/#/notes/2134316
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



Bu belgede, Azure IaaS 'de SAP aşırı dağıtıldığında göz önünde bulundurmanız gereken birkaç farklı alanı ele alır. Bu belgeye yönelik bir önkoşul olarak, Azure [sanal makineler Için Azure sanal MAKINELERI DBMS dağıtımı, SAP iş yükü](dbms_guide_general.md) ve diğer kılavuzlar için [Azure belgelerindeki](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)belge konularını okuduğunuzdan önce göz önünde bulundurmanız gerekir. 

## <a name="specifics-to-sap-ase-on-windows"></a>Windows üzerinde SAP Ao 'nun özellikleri
Microsoft Azure başlayarak, mevcut SAP Ao uygulamalarınızı Azure sanal makinelerine geçirebilirsiniz. Bir Azure sanal makinesinde SAP ASE, bu uygulamaları kolayca Microsoft Azure bir şekilde geçirerek kurumsal ayırt edilen uygulamaların dağıtım, yönetim ve bakımının toplam maliyetini düşürmenizi sağlar. Bir Azure sanal makinesinde SAP Ao ile yöneticiler ve geliştiriciler, şirket içinde bulunan aynı geliştirme ve yönetim araçlarını kullanmaya devam edebilir.

Azure sanal makineler için SLA 'Lar şurada bulunabilir:<https://azure.microsoft.com/support/legal/sla/virtual-machines>

Microsoft Azure, en küçük SAP sistemlerini ve ıdscapes 'yi binlerce kullanıcıyla büyük SAP sistemlerine ve Langes 'ye kadar olan çok sayıda farklı sanal makine türü sunar. SAP Note [1928533]' de farklı SAP sertifikalı VM SKU 'larının SAP boyutlandırma SAPS numaraları sunulmaktadır.

Azure depolama 'nın kullanımıyla ilgili deyimler ve öneriler, SAP VM 'lerinin dağıtımı veya SAP Izleme için [Azure sanal MAKINELER DBMS dağıtımı](dbms_guide_general.md)

### <a name="sap-ase-version-support"></a>SAP ATıCı sürüm desteği
SAP, SAP Business Suite ürünleriyle kullanılmak üzere şu anda SAP ASE sürüm 16,0 ' i desteklemektedir. SAP AıO Server için tüm güncelleştirmeler veya SAP Business Suite ürünleriyle kullanılacak olan JDBC ve ODBC sürücüleri yalnızca şu adreste bulunan SAP hizmeti Marketi aracılığıyla sunulmaktadır: <https://support.sap.com/swdc>.

SAP Ao sunucusu için güncelleştirmeleri veya JDBC ve ODBC sürücülerini doğrudan Sybase Web sitelerinden indirmeyin. Şirket içi ve Azure sanal makinelerinde SAP ürünleri ile kullanım için desteklenen düzeltme ekleri hakkında ayrıntılı bilgi için aşağıdaki SAP notlarına bakın:

* [1590719]
* [1973241]

SAP Business Suite 'i SAP AıS üzerinde çalıştırmaya ilişkin genel bilgiler [SCN](https://www.sap.com/community/topic/ase.html) 'de bulunabilir

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>Azure VM 'lerinde SAP ile ilgili SAP as yüklemeleri için SAP Ao yapılandırma yönergeleri
#### <a name="structure-of-the-sap-ase-deployment"></a>SAP ATıCı dağıtımının yapısı
SAP Ao yürütülebilirleri, VM 'nin IŞLETIM sistemi diskinin sistem sürücüsüne yerleştirilmelidir veya yüklü olmalıdır (sürücü c:\). Genellikle, SAP ASE sistemi ve araçları veritabanlarının çoğu yüksek iş yüküyle karşılaşmaz. Bu nedenle, sistem ve araçlar veritabanları (Master, model, saptools, sybmgmtdb, sybsystemdb) C:\ dizininde kalabilir. sürücü. 

Bir özel durum, bazı SAP ERP ve tüm beyaz iş yüklerinin daha yüksek veri hacmi veya g/ç işlemleri birimi gerektirebileceği geçici veritabanı olabilir. VM 'nin işletim sistemi diski tarafından sağlansağlanmayan birimler veya ıOPS (sürücü C:\).

Yüklemek için kullanılan SAPInst/SWPM sürümüne bağlı olarak SAP Ao örneği yapılandırması şu şekilde görünebilir:

* SAP aşiri yüklenirken oluşturulan tek bir SAP Ao tempdb
* SAP AX ve SAP yükleme yordamı tarafından oluşturulan ek saptempdb 'yi yükleyerek oluşturulan bir SAP Ao tempdb
* SAP AX ve el ile oluşturulan ek bir tempdb (örneğin, SAP Note [1752266]), ERP/siyah ve belirli tempdb gereksinimlerini karşılayacak şekilde oluşturulan BIR SAP Ao tempdb

Belirli ERP veya tüm bant genişliği iş yüklerinin performans nedenleriyle, ek olarak oluşturulan tempdb 'nin tempdb cihazlarını C:\Bir sürücüde depolamak mantıklı olabilir. Ek bir tempdb yoksa, bir tane oluşturmanız önerilir (SAP Note [1752266]).

Bu tür sistemler için, ek olarak oluşturulan tempdb için aşağıdaki adımlar yürütülmelidir:

* İlk tempdb cihazını SAP veritabanının ilk cihazına taşı
* SAP veritabanının bir cihazını içeren VHD 'lere tempdb cihazları ekleme

Bu yapılandırma, tempdb 'nin sistem sürücüsünün sağlayabileceğinden daha fazla alan kullanmasına izin verir. Bir başvuru olarak, var olan sistemlerdeki tempdb cihaz boyutlarını, şirket içinde çalışan bir şekilde kontrol edebilir. Ya da bu tür bir yapılandırma, sistem sürücüsüyle sağlanamaz olan tempdb 'ye karşı ıOPS numaralarına izin vermez. Şirket içinde çalışan sistemler, tempdb 'ye karşı g/ç iş yükünü izlemek için kullanılabilir.

Herhangi bir SAP ATıCı cihazını hiçbir zaman D:\ öğesine yerleştirmeyin VM 'nin sürücüsü. SAP ASE için, tempdb 'de tutulan nesneler yalnızca geçicidir olsa da bu öneri tempdb için de geçerlidir.

Veri ve işlem günlüğü dosya dağıtımları için, [SAP iş yükü Için Azure sanal MAKINELER DBMS dağıtımı konusunda dikkate alınması gereken](dbms_guide_general.md)deyimler ve öneriler. Windows tabanlı bir durumda, yeterli ıOPS, üretilen iş ve birimle dizili kümeler oluşturmak için Windows depolama alanları kullanımının kullanılması önerilir.  

#### <a name="impact-of-database-compression"></a>Veritabanı sıkıştırmasının etkisi
G/ç bant genişliğinin sınırlama faktörü olabileceği yapılandırmalarda, her ölçü, ıOPS 'yi azaltan iş yükünün Azure gibi bir IaaS senaryosunda çalıştırılabileceği şekilde uzamasına yardımcı olabilir. Bu nedenle, mevcut bir SAP veritabanını Azure 'a yüklemeden önce SAP ASE sıkıştırması 'nın kullanıldığından emin olmanız önerilir.

Azure 'a yüklemeden önce sıkıştırmayı uygulama önerisi çeşitli nedenlerden dolayı verilmiştir:

* Azure 'a yüklenecek veri miktarı düşüktür
* Sıkıştırma yürütmesinin süresi, bir birinin daha fazla CPU veya daha yüksek g/ç bant genişliği veya şirket içi g/ç gecikme süresiyle daha güçlü donanımlar kullanmasına olanak daha kısadır
* Daha küçük veritabanı boyutları disk ayırma için daha az maliyete yol açabilir

Veri ve LOB sıkıştırma, şirket içinde çalıştığı gibi Azure sanal makinelerinde barındırılan bir VM 'de çalışır. Sıkıştırmanın mevcut bir SAP Ao veritabanında zaten kullanımda olup olmadığını denetleme hakkında daha fazla bilgi için bkz. SAP Note [1750510].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Veritabanı örneklerini izlemek için Dbakokpit kullanma
Veritabanı platformu olarak SAP AAS kullanan SAP sistemleri için Dbakokpit, işlem Dbakokpit veya WebDynpro olarak katıştırılmış tarayıcı pencereleri olarak erişilebilir. Ancak, veritabanını izlemeye ve yönetmeye yönelik tüm işlevler yalnızca Dbakokpit 'ın WebDynpro uygulamasında kullanılabilir.

Şirket içi sistemlerde olduğu gibi, Dbakokpitinin WebDynpro uygulamasının kullandığı tüm SAP NetWeaver işlevlerini etkinleştirmek için birkaç adım gerekir. Web dynprofesyonelleri kullanımını etkinleştirmek ve gerekli olanları oluşturmak için SAP Note [1245200] ' i izleyin. Yukarıdaki notlardaki yönergeleri izleyerek, Internet Iletişim Yöneticisi 'Ni (ICM) http ve HTTPS bağlantıları için kullanılacak bağlantı noktalarıyla birlikte da yapılandırırsınız. Http için varsayılan ayar şöyle görünür:

> ICM/server_port_0 = PROT = HTTP, bağlantı noktası = 8000, PROCTIMEOUT = 600, zaman AŞıMı = 600
> 
> ICM/server_port_1 = PROT = HTTPS, bağlantı noktası = 443 $ $, PROCTIMEOUT = 600, TIMEOUT = 600
> 
> 

ve işlem Dbakokpit içinde oluşturulan bağlantılar şuna benzer:

> https:\//fullyqualifiedhostname>:44300/SAP/BC/WebDynpro/SAP/dba_cockpit\<
> 
> http:\//fullyqualifiedhostname>:8000/SAP/BC/WebDynpro/SAP/dba_cockpit\<
> 
> 

SAP sistemini barındıran Azure sanal makinesinin AD ve DNS 'nize nasıl bağlı olduğuna bağlı olarak, ICM 'nin Dbakokpit 'yi açtığınız makinede çözümlenebileceğiniz tam bir ana bilgisayar adı kullandığını doğrulayın. ICM 'nin profil parametrelerine bağlı olarak tam ana bilgisayar adını nasıl belirlediğini ve gerekirse ICM/host_name_full parametresini ayarlama hakkında bilgi edinmek için bkz. SAP Note [773830] .

VM 'yi şirket içi ve Azure arasında şirketler arası bağlantı olmadan yalnızca bulut senaryosunda dağıttıysanız, bir genel IP adresi ve domainlabel tanımlamanız gerekir. VM 'nin Genel DNS adının biçimi şöyle görünür:

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

DNS adıyla ilgili daha fazla ayrıntıyı [burada][virtual-machines-azurerm-versus-azuresm]bulabilirsiniz.

SAP profili parametresini ICM/host_name_full olarak ayarlamak, bağlantının şuna benzeyebilir:

> https:\//mydomainlabel.westeurope.cloudapp.net:44300/SAP/BC/WebDynpro/SAP/dba_cockpit
> 
> http:\//mydomainlabel.westeurope.cloudapp.net:8000/SAP/BC/WebDynpro/SAP/dba_cockpit

Bu durumda şunları yapmanız gerekir:

* ICM ile iletişim kurmak için kullanılan TCP/IP bağlantı noktaları için Azure portal ağ güvenlik grubuna gelen kuralları ekleyin
* ICM ile iletişim kurmak için kullanılan TCP/IP bağlantı noktaları için Windows Güvenlik Duvarı yapılandırmasına gelen kuralları ekleyin

Tüm düzeltmelerin otomatik olarak içeri aktarılması için, SAP sürümünüze uygun olan düzeltme toplama SAP notunun düzenli olarak uygulanması önerilir:

* [1558958]
* [1619967]
* [1882376]

SAP ATıCı için DBA kokpiti hakkında daha fazla bilgi aşağıdaki SAP notlarında bulunabilir:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>SAP ATıCı için yedekleme/kurtarma konuları
SAP ate 'yi Azure 'a dağıttığınızda, yedekleme yönteminize göz atmak gerekir. Üretim dışı sistemler için bile SAP veritabanlarının düzenli olarak yedeklenmesi gerekir. Azure depolama üç görüntü sakladığı için bir yedekleme, bir depolama kilitlenmesiyle karşılaştırıldığında daha az önemli olabilir. Doğru bir yedekleme ve geri yükleme planının korunmasının birincil nedeni, zaman kurtarma özelliklerini bir noktadan ayırarak mantıksal/el ile hatalar için telafi sağlayabilmenin daha fazla nedenidir. Böylece hedef, veritabanını belirli bir noktaya geri yüklemek ya da mevcut veritabanını kopyalayarak başka bir sistemi temel almak için Azure 'daki yedeklemeleri kullanmak üzere yedeklemeleri kullanmaktır. 

Azure 'da bir veritabanını yedekleme ve geri yükleme, şirket içi BT ile aynı şekilde çalışır. Bkz. SAP notları:

* [1588316]
* [1585981]

döküm yapılandırması ve zamanlama yedeklemeleri oluşturma hakkında ayrıntılı bilgi için. Stratejinize ve gereksinimlerinize bağlı olarak, var olan disklerden birine yönelik veritabanı ve günlük dökümlerini bir diske yapılandırabilir veya yedekleme için ek bir disk ekleyebilirsiniz. Bir hata durumunda veri kaybını çözmeyi azaltmak için veritabanı dosyalarının yer aldığı bir disk kullanılması önerilir.

Veri ve LOB sıkıştırmasının yanı sıra SAP Ao Ayrıca yedekleme sıkıştırması da sunar. Veritabanı ve günlük dökümlerinde daha az alan kullanmak için yedekleme sıkıştırması kullanılması önerilir. Daha fazla bilgi için bkz. SAP Note [1588316]. Yedeklemenin sıkıştırılması, Azure sanal makinesinden şirket içi yedekleme dökümlerini içeren yedeklemeleri veya VHD 'leri indirmeyi planlıyorsanız, aktarılacak veri miktarını azaltmak için de önemlidir.

D:\ sürücüsünü kullanma veritabanı veya günlük dökümü hedefi olarak.

#### <a name="performance-considerations-for-backupsrestores"></a>Yedeklemeler/geri yüklemeler için performans değerlendirmeleri
Çıplak dağıtımlarda olduğu gibi, yedekleme/geri yükleme performansı, kaç birimin paralel olarak okunmayacağına ve bu birimlerin üretilen işinin ne kadar olabileceğini bağımlıdır. Yedek sıkıştırmasının CPU kaynaklarını tükettiğini aklınızda bulundurun. Yedekleme sıkıştırması 'nın bu CPU tüketimi, az sayıda CPU iş parçacığı olan VM 'lerde önemli bir rol oynayabilir. Bu nedenle, şunları varsayabilirsiniz:

* Veritabanı cihazlarını depolamak için kullanılan disk sayısı daha az olduğunda, okuma sırasında genel üretilen iş miktarı küçüktür
* VM 'deki CPU iş parçacıklarının sayısı ne kadar küçükse, yedek sıkıştırmasının etkisi daha ciddi olur
* Yedeklemenin yazılacağı daha az hedef (dizili dizinler, diskler), üretilen iş miktarı küçüktür

Yazılacak hedef sayısını artırmak için, gereksinimlerinize bağlı olarak kullanılabilecek/birleştirilebilir iki seçenek vardır:

* Bu şeritli birimde ıOPS verimini artırmak için, birden çok bağlı disk üzerinde yedekleme hedefi birimini şeritli hale
* Döküm yazmak için birden fazla hedef dizin kullanan SAP Ao düzeyinde bir döküm yapılandırması oluşturma

Birden çok bağlı disk üzerinde disk birimi şeridi oluşturma, [SAP iş yükü Için Azure sanal MAKINELER DBMS dağıtımı ile Ilgili konular](dbms_guide_general.md)açıklanmaktadır. SAP asa döküm yapılandırmasında birden çok dizin kullanma hakkında daha fazla bilgi için, [Sybase bilgi merkezi](http://infocenter.sybase.com/help/index.jsp)'nde döküm yapılandırması oluşturmak Için kullanılan saklı yordam sp_config_dump hakkındaki belgelere bakın.

### <a name="disaster-recovery-with-azure-vms"></a>Azure VM 'lerinde olağanüstü durum kurtarma
#### <a name="data-replication-with-sap-sybase-replication-server"></a>SAP Sybase çoğaltma sunucusu ile veri çoğaltma
SAP Sybase çoğaltma sunucusu (SRS) ile SAP ATıCı, veritabanı işlemlerini uzaktaki bir konuma zaman uyumsuz olarak aktarmaya yönelik bir yarı etkin bekleme çözümü sağlar. 

SRS 'nin yüklenmesi ve işleyişi, şirket içinde çalıştığı gibi Azure sanal makine Hizmetleri 'nde barındırılan bir VM 'de de işlevsel olarak çalışmaktadır.

SAP Ao HADR, bir Azure Iç Load Balancer gerektirmez ve işletim sistemi düzeyinde Kümelemeye hiç bağımlılığı yoktur. Azure Windows ve Linux VM 'lerinde çalışmaktadır. SAP Ao HADR hakkında ayrıntılar için [SAP Ao HADR kullanıcıları kılavuzunu](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.3/en-US/a6645e28bc2b1014b54b8815a64b87ba.html)okuyun.

## <a name="specifics-to-sap-ase-on-linux"></a>Linux üzerinde SAP Ao 'nun özellikleri
Microsoft Azure başlayarak, mevcut SAP Ao uygulamalarınızı Azure sanal makinelerine kolayca geçirebilirsiniz. Bir sanal makinedeki SAP ASE, bu uygulamaları kolayca Microsoft Azure bir şekilde geçirerek Kurumsal tek uygulamaların dağıtım, yönetim ve bakımının toplam maliyetini düşürmenizi sağlar. Bir Azure sanal makinesinde SAP Ao ile yöneticiler ve geliştiriciler, şirket içinde bulunan aynı geliştirme ve yönetim araçlarını kullanmaya devam edebilir.

Azure VM 'Leri dağıtmak için burada bulunan resmi SLA 'Ları bilmemiz önemlidir:<https://azure.microsoft.com/support/legal/sla>

SAP Note [1928533]' de SAP boyutlandırma BILGILERI ve SAP sertifikalı VM SKU 'larının listesi sunulmaktadır. Azure sanal makineleri için ek SAP boyutlandırma belgeleri burada <https://blogs.msdn.com/b/saponsqlserver/archive/2015/06/19/how-to-size-sap-systems-running-on-azure-vms.aspx> ve burada bulunabilir<https://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

Azure depolama 'nın kullanımıyla ilgili deyimler ve öneriler, SAP VM 'lerinin dağıtımı veya SAP Izleme, bu belgenin ilk dört bölümünde belirtilen SAP uygulamalarıyla birlikte SAP Ao 'nun dağıtımları için geçerlidir.

Aşağıdaki iki SAP notu, Linux üzerinde Ao ve bulutta Ao hakkında genel bilgiler içerir:

* [2134316]
* [1941500]

### <a name="sap-ase-version-support"></a>SAP ATıCı sürüm desteği
SAP, SAP Business Suite ürünleriyle kullanılmak üzere şu anda SAP ASE sürüm 16,0 ' i desteklemektedir. SAP AıO Server için tüm güncelleştirmeler veya SAP Business Suite ürünleriyle kullanılacak olan JDBC ve ODBC sürücüleri yalnızca şu adreste bulunan SAP hizmeti Marketi aracılığıyla sunulmaktadır: <https://support.sap.com/swdc>.

Şirket içi yüklemeler için, SAP ALSERVER için güncelleştirmeleri veya JDBC ve ODBC sürücülerini doğrudan Sybase Web sitelerinden indirmeyin. Şirket içi ve Azure sanal makinelerinde SAP Business Suite ürünleri ile kullanım için desteklenen düzeltme ekleri hakkında ayrıntılı bilgi için aşağıdaki SAP notlarına bakın:

* [1590719]
* [1973241]

SAP Business Suite 'i SAP AıS üzerinde çalıştırmaya ilişkin genel bilgiler [SCN](https://www.sap.com/community/topic/ase.html) 'de bulunabilir

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>Azure VM 'lerinde SAP ile ilgili SAP as yüklemeleri için SAP Ao yapılandırma yönergeleri
#### <a name="structure-of-the-sap-ase-deployment"></a>SAP ATıCı dağıtımının yapısı
SAP Ao yürütülebilirleri, VM 'nin (/Sybase) kök dosya sistemine yerleştirilmelidir veya yüklenmelidir. Genellikle, SAP ASE sistemi ve araçları veritabanlarının çoğu yüksek iş yüküyle karşılaşmaz. Bu nedenle, sistem ve araçlar veritabanları (Master, model, saptools, sybmgmtdb, sybsystemdb) kök dosya sisteminde depolanabilir. 

Bir özel durum, bazı SAP ERP ve tüm beyaz iş yüklerinin daha yüksek veri hacmi veya g/ç işlemleri birimi gerektirebileceği geçici veritabanı olabilir. VM 'nin işletim sistemi diski tarafından sağlansağlanmayan birimler veya ıOPS 

Sistemi yüklemek için kullanılan SAPInst/SWPM sürümüne bağlı olarak, veritabanı şunları içerebilir:

* SAP aşırı yüklenirken oluşturulan tek bir SAP Ao tempdb
* SAP AX ve SAP yükleme yordamı tarafından oluşturulan ek saptempdb 'yi yükleyerek oluşturulan bir SAP Ao tempdb
* SAP AX ve el ile oluşturulan ek bir tempdb (örneğin, SAP Note [1752266]), ERP/siyah ve belirli tempdb gereksinimlerini karşılayacak şekilde oluşturulan BIR SAP Ao tempdb

Belirli ERP veya tüm bant genişliği iş yüklerinin performans nedenleriyle, ayrıca oluşturulan tempdb 'nin (SWPM veya el ile) tempdb cihazlarını, tek bir Azure veri diski veya bir Linux RAID ile birlikte temsil edilebilir ayrı bir dosya sisteminde depolamak mantıklı olabilir. Azure veri disklerini u. Ek bir tempdb yoksa, bir tane oluşturmanız önerilir (SAP Note [1752266]).

Bu tür sistemler için, ayrıca oluşturulan tempdb için aşağıdaki adımlar gerçekleştirilmelidir:

* İlk tempdb dizinini SAP veritabanının ilk dosya sistemine taşıyın
* SAP veritabanının bir dosya sistemini içeren her diske tempdb dizinleri ekleyin

Bu yapılandırma, tempdb 'nin sistem sürücüsünün sağlayabileceğinden daha fazla alan kullanmasına izin verir. Bir başvuru olarak, var olan sistemlerdeki tempdb cihaz boyutlarını, şirket içinde çalışan bir şekilde kontrol edebilir. Ya da bu tür bir yapılandırma, sistem sürücüsüyle sağlanamaz olan tempdb 'ye karşı ıOPS numaralarına izin vermez. Şirket içinde çalışan sistemler, tempdb 'ye karşı g/ç iş yükünü izlemek için kullanılabilir.

Bir SAP Ao dizinini hiçbir zaman VM 'nin/MNT veya/mnt/Resource öğesine yerleştirmeyin. SAP ASE için, tempdb 'de tutulan nesneler yalnızca geçicidir olsa da bu öneri tempdb için de geçerlidir. /MNT veya/mnt/Resource varsayılan bir Azure VM geçici alanı olduğundan, bu kalıcı değildir. [Bu makalede][virtual-machines-linux-how-to-attach-disk] , Azure VM geçici alanı hakkında daha fazla ayrıntı bulabilirsiniz

Veri ve işlem günlüğü dosya dağıtımları için, [SAP iş yükü Için Azure sanal MAKINELER DBMS dağıtımı konusunda dikkate alınması gereken](dbms_guide_general.md)deyimler ve öneriler. Linux tabanlı dağıtımlarda, yeterli ıOPS, üretilen iş ve birimle dizili kümeler oluşturmak için LVM veya MDADDM kullanımının kullanılması önerilir. 

#### <a name="impact-of-database-compression"></a>Veritabanı sıkıştırmasının etkisi
G/ç bant genişliğinin sınırlama faktörü olabileceği yapılandırmalarda, her ölçü, ıOPS 'yi azaltan iş yükünün Azure gibi bir IaaS senaryosunda çalıştırılabileceği şekilde uzamasına yardımcı olabilir. Bu nedenle, mevcut bir SAP veritabanını Azure 'a yüklemeden önce SAP ASE sıkıştırması 'nın kullanıldığından emin olmanız önerilir.

Azure 'a yüklemeden önce sıkıştırmayı uygulama önerisi çeşitli nedenlerden dolayı verilmiştir:

* Azure 'a yüklenecek veri miktarı düşüktür
* Sıkıştırma yürütmesinin süresi, bir birinin daha fazla CPU veya daha yüksek g/ç bant genişliği veya şirket içi g/ç gecikme süresiyle daha güçlü donanımlar kullanmasına olanak daha kısadır
* Daha küçük veritabanı boyutları disk ayırma için daha az maliyete yol açabilir

Veri ve LOB sıkıştırma, şirket içinde çalıştığı gibi Azure sanal makinelerinde barındırılan bir VM 'de çalışır. Sıkıştırmanın mevcut bir SAP Ao veritabanında zaten kullanımda olup olmadığını denetleme hakkında daha fazla bilgi için bkz. SAP Note [1750510]. Veritabanı sıkıştırması hakkında daha fazla bilgi için bkz. SAP Note [2121797].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Veritabanı örneklerini izlemek için Dbakokpit kullanma
Veritabanı platformu olarak SAP AAS kullanan SAP sistemleri için Dbakokpit, işlem Dbakokpit veya WebDynpro olarak katıştırılmış tarayıcı pencereleri olarak erişilebilir. Ancak, veritabanını izlemeye ve yönetmeye yönelik tüm işlevler yalnızca Dbakokpit 'ın WebDynpro uygulamasında kullanılabilir.

Şirket içi sistemlerde olduğu gibi, Dbakokpitinin WebDynpro uygulamasının kullandığı tüm SAP NetWeaver işlevlerini etkinleştirmek için birkaç adım gerekir. Web dynprofesyonelleri kullanımını etkinleştirmek ve gerekli olanları oluşturmak için SAP Note [1245200] ' i izleyin. Yukarıdaki notlardaki yönergeleri izleyerek, Internet Iletişim Yöneticisi 'Ni (ICM) http ve HTTPS bağlantıları için kullanılacak bağlantı noktalarıyla birlikte da yapılandırırsınız. Http için varsayılan ayar şöyle görünür:

> ICM/server_port_0 = PROT = HTTP, bağlantı noktası = 8000, PROCTIMEOUT = 600, zaman AŞıMı = 600
> 
> ICM/server_port_1 = PROT = HTTPS, bağlantı noktası = 443 $ $, PROCTIMEOUT = 600, TIMEOUT = 600
> 
> 

ve işlem Dbakokpitinde oluşturulan bağlantılar şuna benzer olacaktır:

> https:\//fullyqualifiedhostname>:44300/SAP/BC/WebDynpro/SAP/dba_cockpit\<
> 
> http:\//fullyqualifiedhostname>:8000/SAP/BC/WebDynpro/SAP/dba_cockpit\<
> 
> 

SAP sistemini barındıran Azure sanal makinesinin AD ve DNS 'nize nasıl bağlı olduğuna bağlı olarak, ICM 'nin Dbakokpit 'yi açtığınız makinede çözümlenebileceğiniz tam bir ana bilgisayar adı kullandığını doğrulayın. Bkz. SAP Note [773830] , profil parametrelerine bağlı olarak tam konak adını nasıl belirlediğini ve gerekirse ICM/host_name_full parametresini nasıl ayarlayabileceğinizi anlamak için.

VM 'yi şirket içi ve Azure arasında şirketler arası bağlantı olmadan yalnızca bulut senaryosunda dağıttıysanız, bir genel IP adresi ve domainlabel tanımlamanız gerekir. VM 'nin Genel DNS adının biçimi şöyle görünür:

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

DNS adıyla ilgili daha fazla ayrıntıyı [burada][virtual-machines-azurerm-versus-azuresm]bulabilirsiniz.

SAP profili parametresini ICM/host_name_full olarak ayarlamak, bağlantının şuna benzeyebilir:

> https:\//mydomainlabel.westeurope.cloudapp.net:44300/SAP/BC/WebDynpro/SAP/dba_cockpit
> 
> http:\//mydomainlabel.westeurope.cloudapp.net:8000/SAP/BC/WebDynpro/SAP/dba_cockpit

Bu durumda şunları yapmanız gerekir:

* ICM ile iletişim kurmak için kullanılan TCP/IP bağlantı noktaları için Azure portal ağ güvenlik grubuna gelen kuralları ekleyin
* ICM ile iletişim kurmak için kullanılan TCP/IP bağlantı noktaları için Windows Güvenlik Duvarı yapılandırmasına gelen kuralları ekleyin

Tüm düzeltmelerin otomatik olarak içeri aktarılması için, SAP sürümünüze uygun olan düzeltme toplama SAP notunun düzenli olarak uygulanması önerilir:

* [1558958]
* [1619967]
* [1882376]

SAP ATıCı için DBA kokpiti hakkında daha fazla bilgi aşağıdaki SAP notlarında bulunabilir:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>SAP ATıCı için yedekleme/kurtarma konuları
SAP ate 'yi Azure 'a dağıttığınızda, yedekleme yönteminize göz atmak gerekir. Üretim dışı sistemler için bile SAP veritabanlarının düzenli olarak yedeklenmesi gerekir. Azure depolama üç görüntü sakladığı için bir yedekleme, bir depolama kilitlenmesiyle karşılaştırıldığında daha az önemli olabilir. Doğru bir yedekleme ve geri yükleme planının korunmasının birincil nedeni, zaman kurtarma özelliklerini bir noktadan ayırarak mantıksal/el ile hatalar için telafi sağlayabilmenin daha fazla nedenidir. Böylece hedef, veritabanını belirli bir noktaya geri yüklemek ya da mevcut veritabanını kopyalayarak başka bir sistemi temel almak için Azure 'daki yedeklemeleri kullanmak üzere yedeklemeleri kullanmaktır. 

Azure 'da bir veritabanını yedekleme ve geri yükleme, şirket içi BT ile aynı şekilde çalışır. Bkz. SAP notları:

* [1588316]
* [1585981]

döküm yapılandırması ve zamanlama yedeklemeleri oluşturma hakkında ayrıntılı bilgi için. Stratejinize ve gereksinimlerinize bağlı olarak, veritabanı ve günlük dökümlerini mevcut disklerden birine göre ve yedekleme için ek bir disk ekleyebilmeniz gerekir. Bir hata durumunda veri kaybını çözmeyi azaltmak için veritabanı dizini/dosyası bulunmayan bir disk kullanılması önerilir.

Veri ve LOB sıkıştırmasının yanı sıra SAP Ao Ayrıca yedekleme sıkıştırması da sunar. Veritabanı ve günlük dökümlerinde daha az alan kullanmak için yedekleme sıkıştırması kullanılması önerilir. Daha fazla bilgi için bkz. SAP Note [1588316]. Yedeklemenin sıkıştırılması, Azure sanal makinesinden şirket içi yedekleme dökümlerini içeren yedeklemeleri veya VHD 'leri indirmeyi planlıyorsanız, aktarılacak veri miktarını azaltmak için de önemlidir.

Azure VM Temp Space/MNT veya/mnt/Resource ' i veritabanı veya günlük dökümü hedefi olarak kullanmayın.

#### <a name="performance-considerations-for-backupsrestores"></a>Yedeklemeler/geri yüklemeler için performans değerlendirmeleri
Çıplak dağıtımlarda olduğu gibi, yedekleme/geri yükleme performansı, kaç birimin paralel olarak okunmayacağına ve bu birimlerin üretilen işinin ne kadar olabileceğini bağımlıdır. Yedek sıkıştırmasının CPU kaynaklarını tükettiğini aklınızda bulundurun. Yedekleme sıkıştırması 'nın bu CPU tüketimi, az sayıda CPU iş parçacığı olan VM 'lerde önemli bir rol oynayabilir.  Bu nedenle, şunları varsayabilirsiniz:

* Veritabanı cihazlarını depolamak için kullanılan disk sayısı daha az olduğunda, okuma sırasında genel üretilen iş miktarı küçüktür
* VM 'deki CPU iş parçacıklarının sayısı ne kadar küçükse, yedek sıkıştırmasının etkisi daha ciddi olur
* Yedeklemenin yazılacağı daha az hedef (Linux yazılım RAID, disk), aktarım hızı daha az

Yazılacak hedef sayısını artırmak için, gereksinimlerinize bağlı olarak kullanılabilecek/birleştirilebilir iki seçenek vardır:

* Bu şeritli birimde ıOPS verimini artırmak için, birden çok bağlı disk üzerinde yedekleme hedefi birimini şeritli hale
* Döküm yazmak için birden fazla hedef dizin kullanan SAP Ao düzeyinde bir döküm yapılandırması oluşturma

Birden çok bağlı disk üzerinde disk birimi şeridi oluşturma, [SAP iş yükü Için Azure sanal MAKINELER DBMS dağıtımı ile Ilgili konular](dbms_guide_general.md)açıklanmaktadır. SAP asa döküm yapılandırmasında birden çok dizin kullanma hakkında daha fazla bilgi için, [Sybase bilgi merkezi](http://infocenter.sybase.com/help/index.jsp)'nde döküm yapılandırması oluşturmak Için kullanılan saklı yordam sp_config_dump hakkındaki belgelere bakın.

### <a name="disaster-recovery-with-azure-vms"></a>Azure VM 'lerinde olağanüstü durum kurtarma
#### <a name="data-replication-with-sap-sybase-replication-server"></a>SAP Sybase çoğaltma sunucusu ile veri çoğaltma
SAP Sybase çoğaltma sunucusu (SRS) ile SAP ATıCı, veritabanı işlemlerini uzaktaki bir konuma zaman uyumsuz olarak aktarmaya yönelik bir yarı etkin bekleme çözümü sağlar. 

SRS 'nin yüklenmesi ve işleyişi, şirket içinde çalıştığı gibi Azure sanal makine Hizmetleri 'nde barındırılan bir VM 'de de işlevsel olarak çalışmaktadır.

SAP çoğaltma sunucusu aracılığıyla Ao HADR desteklenir. Bu tür bir yapılandırmayı denemek için SAP ASE 16,03 kullanılması önemle önerilir. Bu tür konfigürasyonları yüklemeye yönelik daha ayrıntılı yönergeler, bu blogda ayrıntılı olarak [](https://blogs.msdn.microsoft.com/saponsqlserver/2018/06/18/installation-procedure-for-sybase-16-3-patch-level-3-always-on-dr-on-suse-12-3-recent-customer-proof-of-concept/)bulunabilir.
