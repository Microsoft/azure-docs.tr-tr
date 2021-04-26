---
title: Azure VM 'lerinde SAP NetWeaver için yüksek kullanılabilirlik
description: Azure sanal makinelerde SAP NetWeaver için yüksek kullanılabilirlik Kılavuzu
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/24/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6aedaf829df941a225cd0684318f28bb06ba89d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101674600"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms"></a>Azure VM 'lerinde SAP NetWeaver için yüksek kullanılabilirlik

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
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
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:https://docs.microsoft.com/powershell/azure/
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:dbms_guide_general.md
[dbms-guide-2.1]:dbms_guide_general.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f
[dbms-guide-2.2]:dbms_guide_general.md#c8e566f9-21b7-4457-9f7f-126036971a91
[dbms-guide-2.3]:dbms_guide_general.md#10b041ef-c177-498a-93ed-44b3441ab152
[dbms-guide-2]:dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64
[dbms-guide-3]:dbms_guide_general.md#871dfc27-e509-4222-9370-ab1de77021c3
[dbms-guide-5.5.1]:dbms_guide_general.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268
[dbms-guide-5.5.2]:dbms_guide_general.md#f9071eff-9d72-4f47-9da4-1852d782087b
[dbms-guide-5.6]:dbms_guide_general.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8
[dbms-guide-5.8]:dbms_guide_general.md#9053f720-6f3b-4483-904d-15dc54141e30
[dbms-guide-5]:dbms_guide_general.md#3264829e-075e-4d25-966e-a49dad878737
[dbms-guide-8.4.1]:dbms_guide_general.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573
[dbms-guide-8.4.2]:dbms_guide_general.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d
[dbms-guide-8.4.3]:dbms_guide_general.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c
[dbms-guide-8.4.4]:dbms_guide_general.md#f77c1436-9ad8-44fb-a331-8671342de818
[dbms-guide-900-sap-cache-server-on-premises]:dbms_guide_general.md#642f746c-e4d4-489d-bf63-73e80177a0a8

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

[deploy-template-cli]:../../../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows
[deploy-template-portal]:../../../resource-group-template-deploy.md#deploy-with-the-preview-portal
[deploy-template-powershell]:../../../resource-group-template-deploy.md#deploy-with-powershell

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:../../virtual-machines-windows-sap-get-started.md
[getting-started-dbms]:../../virtual-machines-windows-sap-get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:../../virtual-machines-windows-sap-get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:../../virtual-machines-windows-sap-get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:https://go.microsoft.com/fwlink/?LinkId=613056

[ha-guide]:high-availability-guide.md

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

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

[sap-ha-guide]:high-availability-guide.md
[sap-ha-guide-1]:high-availability-guide.md#217c5479-5595-4cd8-870d-15ab00d4f84c
[sap-ha-guide-2]:high-availability-guide.md#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-3]:high-availability-guide.md#42156640c6-01cf-45a9-b225-4baa678b24f1
[sap-ha-guide-3.1]:high-availability-guide.md#f76af273-1993-4d83-b12d-65deeae23686
[sap-ha-guide-3.2]:high-availability-guide.md#3e85fbe0-84b1-4892-87af-d9b65ff91860
[sap-ha-guide-4]:high-availability-guide.md#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-4.1]:high-availability-guide.md#1a3c5408-b168-46d6-99f5-4219ad1b1ff2
[sap-ha-guide-5]:high-availability-guide.md#fdfee875-6e66-483a-a343-14bbaee33275
[sap-ha-guide-5.1]:high-availability-guide.md#be21cf3e-fb01-402b-9955-54fbecf66592
[sap-ha-guide-5.2]:high-availability-guide.md#ff7a9a06-2bc5-4b20-860a-46cdb44669cd
[sap-ha-guide-6]:high-availability-guide.md#2ddba413-a7f5-4e4e-9a51-87908879c10a
[sap-ha-guide-6.1]:high-availability-guide.md#1a464091-922b-48d7-9d08-7cecf757f341
[sap-ha-guide-6.2]:high-availability-guide.md#44641e18-a94e-431f-95ff-303ab65e0bcb
[sap-ha-guide-7]:high-availability-guide.md#2e3fec50-241e-441b-8708-0b1864f66dfa
[sap-ha-guide-7.1]:high-availability-guide.md#93faa747-907e-440a-b00a-1ae0a89b1c0e
[sap-ha-guide-7.2]:high-availability-guide.md#f559c285-ee68-4eec-add1-f60fe7b978db
[sap-ha-guide-7.2.1]:high-availability-guide.md#b5b1fd0b-1db4-4d49-9162-de07a0132a51
[sap-ha-guide-7.3]:high-availability-guide.md#ddd878a0-9c2f-4b8e-8968-26ce60be1027
[sap-ha-guide-7.4]:high-availability-guide.md#045252ed-0277-4fc8-8f46-c5a29694a816
[sap-ha-guide-8]:high-availability-guide.md#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:high-availability-guide.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.2]:high-availability-guide.md#7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310
[sap-ha-guide-8.3]:high-availability-guide.md#47d5300a-a830-41d4-83dd-1a0d1ffdbe6a
[sap-ha-guide-8.4]:high-availability-guide.md#b22d7b3b-4343-40ff-a319-097e13f62f9e
[sap-ha-guide-8.5]:high-availability-guide.md#9fbd43c0-5850-4965-9726-2a921d85d73f
[sap-ha-guide-8.6]:high-availability-guide.md#84c019fe-8c58-4dac-9e54-173efd4b2c30
[sap-ha-guide-8.7]:high-availability-guide.md#7a8f3e9b-0624-4051-9e41-b73fff816a9e
[sap-ha-guide-8.8]:high-availability-guide.md#f19bd997-154d-4583-a46e-7f5a69d0153c
[sap-ha-guide-8.9]:high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.10]:high-availability-guide.md#e69e9a34-4601-47a3-a41c-d2e11c626c0c
[sap-ha-guide-8.11]:high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:high-availability-guide.md#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:high-availability-guide.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.2]:high-availability-guide.md#e49a4529-50c9-4dcf-bde7-15a0c21d21ca
[sap-ha-guide-8.12.2.1]:high-availability-guide.md#06260b30-d697-4c4d-b1c9-d22c0bd64855
[sap-ha-guide-8.12.2.2]:high-availability-guide.md#4c08c387-78a0-46b1-9d27-b497b08cac3d
[sap-ha-guide-8.12.3]:high-availability-guide.md#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:high-availability-guide.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:high-availability-guide.md#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:high-availability-guide.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:high-availability-guide.md#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:high-availability-guide.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097
[sap-ha-guide-9.1.2]:high-availability-guide.md#eb5af918-b42f-4803-bb50-eff41f84b0b0
[sap-ha-guide-9.1.3]:high-availability-guide.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556
[sap-ha-guide-9.1.4]:high-availability-guide.md#10822f4f-32e7-4871-b63a-9b86c76ce761
[sap-ha-guide-9.1.5]:high-availability-guide.md#4498c707-86c0-4cde-9c69-058a7ab8c3ac
[sap-ha-guide-9.2]:high-availability-guide.md#85d78414-b21d-4097-92b6-34d8bcb724b7
[sap-ha-guide-9.3]:high-availability-guide.md#8a276e16-f507-4071-b829-cdc0a4d36748
[sap-ha-guide-9.4]:high-availability-guide.md#094bc895-31d4-4471-91cc-1513b64e406a
[sap-ha-guide-9.5]:high-availability-guide.md#2477e58f-c5a7-4a5d-9ae3-7b91022cafb5
[sap-ha-guide-9.6]:high-availability-guide.md#0ba4a6c1-cc37-4bcf-a8dc-025de4263772
[sap-ha-guide-10]:high-availability-guide.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9
[sap-ha-guide-10.1]:high-availability-guide.md#65fdef0f-9f94-41f9-b314-ea45bbfea445
[sap-ha-guide-10.2]:high-availability-guide.md#5e959fa9-8fcd-49e5-a12c-37f6ba07b916
[sap-ha-guide-10.3]:high-availability-guide.md#755a6b93-0099-4533-9f6d-5c9a613878b5

[sap-ha-multi-sid-guide]:high-availability-multi-sid.md (SAP çoklu SID yüksek kullanılabilirlik yapılandırması)


[sap-ha-guide-figure-1000]:media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png

[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../../../azure-resource-manager/management/overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam (SAP ürün kullanılabilirliği matrisi)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/scalability-targets-standard-accounts.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-windows-attach-disk-portal]:../../virtual-machines-windows-attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../azure-resource-manager/management/overview.md
[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-windows-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-capture]:../../linux/capture-image.md#capture-the-vm
[virtual-machines-windows-capture-image]:../../virtual-machines-windows-capture-image.md
[virtual-machines-windows-capture-image-capture]:../../virtual-machines-windows-capture-image.md#capture-the-vm
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability]:../../windows/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:../../virtual-machines-windows-ps-create.md
[virtual-machines-sizes]:../../virtual-machines-windows-sizes.md
[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]:../../../azure-sql/virtual-machines/windows/availability-group-load-balancer-portal-configure.md
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
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md


Azure sanal makineleri, işlem, depolama ve ağ kaynakları, en kısa sürede ve uzun tedarik döngüleri olmadan ihtiyaç duyulan kuruluşların çözümüdür. SAP NetWeaver tabanlı ABAP, Java ve bir ABAP + Java yığını gibi klasik uygulamaları dağıtmak için Azure sanal makinelerini kullanabilirsiniz. Ek şirket içi kaynaklar olmadan güvenilirliği ve kullanılabilirliği genişletin. Azure sanal makineleri, şirket içi etki alanları, özel bulutlar ve SAP sistem yataya Azure sanal makinelerini tümleştirmenize olanak sağlamak için şirketler arası bağlantıyı destekler.

Bu makalede, Azure Resource Manager dağıtım modelini kullanarak Azure 'da yüksek kullanılabilirliğe sahip SAP sistemlerini dağıtmak için uygulayabileceğiniz adımları ele aldık. Şu ana görevlerde size kılavuzluk ederiz:

* [Kaynaklar][sap-ha-guide-2] bölümünde LISTELENEN doğru SAP notlarını ve yükleme kılavuzlarını bulun. Bu makalede, belirli platformlarda SAP yazılımını yüklemenize ve dağıtmanıza yardımcı olabilecek birincil kaynaklar olan SAP yükleme belgeleri ve SAP notları açıklanır.
* Azure Resource Manager dağıtım modeliyle klasik Azure dağıtım modeli arasındaki farkları öğrenin.
* Windows Server Yük Devretme Kümelemesi çekirdek modları hakkında bilgi edinmek için, Azure dağıtımınız için uygun olan modeli seçebilirsiniz.
* Azure hizmetlerinde Windows Server Yük Devretme Kümelemesi paylaşılan depolama hakkında bilgi edinin.
* Gelişmiş Iş uygulaması programlama (ABAP) SAP Merkezi Hizmetleri (ASCS)/SAP Merkezi Hizmetleri (SCS) ve veritabanı yönetim sistemleri (DBMS) gibi tek nokta tabanlı bileşenleri ve Azure 'da SAP uygulama sunucusu gibi yedekli bileşenleri korumaya nasıl yardımcı olabileceğinizi öğrenin.
* Azure Resource Manager kullanarak Azure 'da bir Windows Server Yük Devretme Kümelemesi kümesinde yüksek kullanılabilirliğe sahip bir SAP sisteminin yükleme ve yapılandırma hakkında adım adım bir örnek izleyin.
* Azure 'da Windows Server Yük Devretme Kümelemesi 'ni kullanmak için gereken ek adımlar hakkında bilgi edinin, ancak bu, şirket içi dağıtımda gerekli değildir.

Dağıtım ve yapılandırmayı basitleştirmek için, bu makalede SAP üç katmanlı yüksek kullanılabilirliğe sahip Kaynak Yöneticisi şablonları kullanırız. Şablonlar, yüksek kullanılabilirliğe sahip bir SAP sistemi için ihtiyaç duyduğunuz tüm altyapının dağıtımını otomatik hale getirir. Altyapı Ayrıca SAP sisteminizin performans standardı (SAPS) boyutunu destekler.

## <a name="prerequisites"></a><a name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a> Kaynakları
Başlamadan önce, aşağıdaki bölümlerde açıklanan önkoşulları karşıladığınızdan emin olun. Ayrıca, [kaynaklar][sap-ha-guide-2] bölümünde listelenen tüm kaynakları denetlediğinizden emin olun.

Bu makalede, [üç KATMANLı SAP NetWeaver](https://github.com/Azure/azure-quickstart-templates/)için Azure Resource Manager şablonlar kullanırız. Şablonlara faydalı bir genel bakış için bkz. [SAP Azure Resource Manager şablonları](/archive/blogs/saponsqlserver/azure-quickstart-templates-for-sap).

## <a name="resources"></a><a name="42b8f600-7ba3-4606-b8a5-53c4f026da08"></a> Kaynakların
Bu makaleler Azure 'da SAP dağıtımlarını kapsar:

* [SAP NetWeaver için Azure sanal makineleri planlama ve uygulama][planning-guide]
* [SAP NetWeaver için Azure sanal makineler dağıtımı][deployment-guide]
* [SAP NetWeaver için Azure sanal makineler DBMS dağıtımı][dbms-guide]
* [Azure sanal makineler SAP NetWeaver için yüksek kullanılabilirlik (Bu kılavuz)][sap-ha-guide]

> [!NOTE]
> Mümkün olduğunda, başvurulan SAP yükleme kılavuzunun bağlantısını sunuyoruz (bkz. [SAP yükleme kılavuzlarını][sap-installation-guides]). Yükleme işlemiyle ilgili Önkoşullar ve bilgiler için, SAP NetWeaver yükleme kılavuzlarını dikkatle okumak iyi bir fikirdir. Bu makalede, yalnızca Azure sanal makinelerle kullanabileceğiniz SAP NetWeaver tabanlı sistemler için özel görevler ele alınmaktadır.
>
>

Bu SAP notları, Azure 'daki SAP konusuyla ilgilidir:

| Dekont numarası | Başlık |
| --- | --- |
| [1928533] |Azure 'da SAP uygulamaları: Desteklenen Ürünler ve boyutlandırma |
| [2015553] |Microsoft Azure SAP: destek önkoşulları |
| [1999351] |SAP için geliştirilmiş Azure Izleme |
| [2178632] |Microsoft Azure üzerinde SAP için anahtar Izleme ölçümleri |
| [1999351] |Windows 'da sanallaştırma: Gelişmiş Izleme |
| [2243692] |SAP DBMS örneği için Azure Premium SSD depolama kullanımı |

Genel varsayılan sınırlamalar ve en yüksek sınırlamalar dahil olmak üzere [Azure aboneliklerinin sınırlamaları][azure-resource-manager/management/azure-subscription-service-limits-subscription]hakkında daha fazla bilgi edinin.

## <a name="high-availability-sap-with-azure-resource-manager-vs-the-azure-classic-deployment-model"></a><a name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a>Azure klasik dağıtım modeli Azure Resource Manager ile yüksek kullanılabilirliğe sahip SAP
Azure Resource Manager ve klasik Azure dağıtım modelleri aşağıdaki alanlarda farklıdır:

- Kaynak grupları
- Azure Kaynak grubunda Azure iç yük dengeleyici bağımlılığı
- SAP çoklu SID senaryoları desteği

### <a name="resource-groups"></a><a name="f76af273-1993-4d83-b12d-65deeae23686"></a> Kaynak grupları
Azure Resource Manager, Azure aboneliğinizdeki tüm uygulama kaynaklarını yönetmek için kaynak gruplarını kullanabilirsiniz. Tümleşik bir yaklaşım olan bir kaynak grubunda, tüm kaynaklar aynı yaşam döngüsüne sahiptir. Örneğin, tüm kaynaklar aynı anda oluşturulur ve aynı zamanda silinir. [Kaynak grupları](../../../azure-resource-manager/management/overview.md#resource-groups) hakkında daha fazla bilgi edinin.

### <a name="azure-internal-load-balancer-dependency-on-the-azure-resource-group"></a><a name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a> Azure Kaynak grubunda Azure iç yük dengeleyici bağımlılığı

Klasik Azure dağıtım modelinde, Azure iç yük dengeleyici (Azure Load Balancer hizmeti) ve bulut hizmeti grubu arasında bir bağımlılık vardır. Her iç yük dengeleyicinin bir bulut hizmeti grubu olması gerekir.

Azure Resource Manager, Azure Load Balancer kullanabilmeniz için bir Azure Kaynak grubuna ihtiyacınız yoktur. Ortam daha basit ve daha esnektir.

### <a name="support-for-sap-multi-sid-scenarios"></a>SAP çoklu SID senaryoları desteği

Azure Resource Manager, bir kümede birden fazla SAP sistem tanımlayıcısı (SID) ASCS/SCS örneği yükleyebilirsiniz. Her bir Azure iç yük dengeleyici için birden çok IP adresi desteği nedeniyle çoklu SID örnekleri mümkündür.

Klasik Azure dağıtım modelini kullanmak için Azure 'da SAP NetWeaver 'de açıklanan yordamları izleyin [: Azure 'Da Windows Server Yük Devretme Kümelemesi 'ni, SIOS Dataman ile birlikte kullanarak SAP Ass/SCS örneklerini kümeleme](https://go.microsoft.com/fwlink/?LinkId=613056).

> [!IMPORTANT]
> SAP yüklemelerinizin Azure Resource Manager dağıtım modelini kullanmanızı önemle öneririz. Klasik dağıtım modelinde kullanılamayan birçok avantaj sunar. Azure [dağıtım modelleri][virtual-machines-azure-resource-manager-architecture-benefits-arm] hakkında daha fazla bilgi edinin.   
>
>

## <a name="windows-server-failover-clustering"></a><a name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a> Windows Server Yük Devretme Kümelemesi
Windows Server Yük Devretme Kümelemesi, Windows 'da yüksek kullanılabilirliğe sahip SAP Ass/SCS yüklemesi ve DBMS 'nin temelidir.

Yük devretme kümesi, uygulamaların ve hizmetlerin kullanılabilirliğini artırmak için birlikte çalışan 1 + n bağımsız sunucu (düğüm) grubudur. Bir düğüm hatası oluşursa, Windows Server Yük Devretme Kümelemesi, uygulamalar ve hizmetler sağlamak üzere sağlıklı bir kümeyi sürdürirken oluşabilecek hata sayısını hesaplar. Yük Devretme Kümelemesi elde etmek için farklı çekirdek modlarında seçim yapabilirsiniz.

### <a name="quorum-modes"></a><a name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a> Çekirdek modları
Windows Server Yük Devretme Kümelemesi kullandığınızda dört çekirdek modu arasından seçim yapabilirsiniz:

* **Düğüm çoğunluğu**. Kümenin her bir düğümü oylayabilir. Küme yalnızca oyları olan, yani oyından fazla olacak şekilde çalışır. Düzensiz sayıda düğüme sahip kümeler için bu seçeneği öneririz. Örneğin, yedi düğümlü bir küme içindeki üç düğüm başarısız olabilir ve küme stilleri büyük bir çoğunluğuna ulaşır ve çalışmaya devam eder.  
* **Düğüm ve disk çoğunluğu**. Küme depolamadaki her düğüm ve belirlenen bir disk (bir disk tanığı), kullanılabilir olduklarında ve iletişim kutusunda oy verebilir. Küme yalnızca oylarının büyük bir çoğunluğuna sahip olur, yani oyları yarıya kadar büyüktür. Bu mod, çift sayıda düğüm içeren bir küme ortamında mantıklı olur. Düğümlerin ve diskin yarısı çevrimiçiyse, küme sağlıklı durumda kalır.
* **Düğüm ve dosya paylaşımının çoğunluğu**. Her düğüm ve yönetici tarafından oluşturulan bir dosya paylaşımının (bir dosya paylaşma tanığı), düğümlerin ve dosya paylaşımının kullanılabilir ve iletişim içinde olup olmamasına bakılmaksızın, bu, belirlenen bir dosya paylaşma (dosya paylaşma tanığı). Küme yalnızca oylarının büyük bir çoğunluğuna sahip olur, yani oyları yarıya kadar büyüktür. Bu mod, çift sayıda düğüm içeren bir küme ortamında mantıklı olur. Düğüm ve disk çoğunluğu moduna benzerdir, ancak tanık disk yerine bir tanık dosya paylaşma kullanır. Bu modun uygulanması kolaydır, ancak dosya paylaşımının kendisi yüksek oranda kullanılabilir değilse, tek hata noktası olabilir.
* **Çoğunluk yok: yalnızca disk**. Bir düğüm varsa ve küme depolamadaki belirli bir diskle iletişim kutusunda kümenin bir çekirdeği vardır. Yalnızca bu diskle iletişim de olan düğümler kümeye katılabilir. Bu modu kullanmanızı öneririz.

## <a name="windows-server-failover-clustering-on-premises"></a><a name="fdfee875-6e66-483a-a343-14bbaee33275"></a> Şirket içi Windows Server Yük Devretme Kümelemesi
Şekil 1 ' de iki düğümün kümesi gösterilmektedir. Düğümler arasındaki ağ bağlantısı başarısız olursa ve her iki düğüm de çalışır duruma çıkarsa, bir çekirdek disk veya dosya paylaşımında kümenin uygulama ve hizmetlerini sağlamaya devam edecek olan düğüm belirlenir. Çekirdek diskine veya dosya paylaşıma erişimi olan düğüm, hizmetlerin devam etmesini sağlayan düğümdür.

Bu örnek iki düğümlü bir küme kullandığından, düğüm ve dosya paylaşma çoğunluğu çekirdek modunu kullanırız. Düğüm ve disk çoğunluğu de geçerli bir seçenektir. Bir üretim ortamında, bir çekirdek disk kullanmanızı öneririz. Ağ ve depolama sistemi teknolojisini kullanarak yüksek oranda kullanılabilir hale getirebilirsiniz.

![Şekil 1: Azure 'da SAP yoks/SCS için Windows Server Yük Devretme Kümelemesi yapılandırması örneği][sap-ha-guide-figure-1000]

_**Şekil 1:** Azure 'da SAP yoks/SCS için Windows Server Yük Devretme Kümelemesi yapılandırması örneği_

### <a name="shared-storage"></a><a name="be21cf3e-fb01-402b-9955-54fbecf66592"></a> Paylaşılan depolama alanı
Şekil 1 ' de iki düğümlü bir paylaşılan depolama kümesi de gösterilmektedir. Şirket içi paylaşılan depolama kümesinde, kümedeki tüm düğümler paylaşılan depolamayı algılar. Kilitleme mekanizması, verilerin bozulmasını önler. Tüm düğümler, başka bir düğümün başarısız olup olmadığını algılayabilir. Bir düğüm başarısız olursa, kalan düğüm depolama kaynaklarının sahipliğini alır ve hizmetlerin kullanılabilirliğini sağlar.

> [!NOTE]
> SQL Server gibi bazı DBMS uygulamalarıyla yüksek kullanılabilirlik için paylaşılan disklere ihtiyacınız yoktur. Her zaman SQL Server, tek bir küme düğümünün yerel diskinden DBMS verilerini ve günlük dosyalarını başka bir küme düğümünün yerel diskine çoğaltır. Bu durumda, Windows küme yapılandırmasına paylaşılan bir disk gerekmez.
>
>

### <a name="networking-and-name-resolution"></a><a name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a> Ağ ve ad çözümlemesi
İstemci bilgisayarlar, kümeye bir sanal IP adresi ve DNS sunucusunun sağladığı bir sanal ana bilgisayar adı üzerinden ulaşabilirler. Şirket içi düğümler ve DNS sunucusu birden çok IP adresini işleyebilir.

Tipik bir kurulumda iki veya daha fazla ağ bağlantısı kullanırsınız:

* Depolamaya adanmış bir bağlantı
* Sinyal için bir küme iç ağ bağlantısı
* İstemcilerin kümeye bağlanmak için kullandığı ortak ağ

## <a name="windows-server-failover-clustering-in-azure"></a><a name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a> Azure 'da Windows Server Yük Devretme Kümelemesi
Çıplak veya özel bulut dağıtımlarıyla karşılaştırıldığında, Azure sanal makineleri, Windows Server Yük Devretme Kümelemesi 'ni yapılandırmak için ek adımlar gerektirir. Paylaşılan bir küme diski oluştururken, SAP ASCS/SCS örneği için birkaç IP adresi ve sanal konak adı ayarlamanız gerekir.

Bu makalede, Azure 'da SAP yüksek kullanılabilirlik Merkezi Hizmetleri kümesi oluşturmak için gereken temel kavramları ve ek adımları tartıştık. Üçüncü taraf aracı SIOS veri ve Azure iç yük dengeleyicinin nasıl yapılandırılacağını göstereceğiz. Bu araçları, Azure 'da dosya paylaşma tanığı olan bir Windows Yük devretme kümesi oluşturmak için kullanabilirsiniz.

![Şekil 2: Azure 'da paylaşılan disk olmadan Windows Server Yük Devretme Kümelemesi yapılandırması][sap-ha-guide-figure-1001]

_**Şekil 2:** Azure 'da paylaşılan disk olmadan Windows Server Yük Devretme Kümelemesi yapılandırması_

### <a name="shared-disk-in-azure-with-sios-datakeeper"></a><a name="1a464091-922b-48d7-9d08-7cecf757f341"></a> Azure 'da SIOS Dataman ile paylaşılan disk
Yüksek kullanılabilirliğe sahip SAP yoks/SCS örneği için küme paylaşılan depolama gerekir. Azure, 2016 Eylül itibariyle paylaşılan depolama kümesi oluşturmak için kullanabileceğiniz paylaşılan depolama alanı sunmaz. Küme paylaşılan depolama alanını taklit eden yansıtılmış bir depolama alanı oluşturmak için üçüncü taraf yazılım SIOS Dataman kümesi sürümünü kullanabilirsiniz. SIOS çözümü gerçek zamanlı zaman uyumlu veri çoğaltması sağlar. Bir küme için paylaşılan disk kaynağı oluşturma işlemi şu şekilde yapılır:

1. Bir Windows küme yapılandırmasındaki sanal makinelerin (VM) her birine ek bir Azure sanal sabit diski (VHD) ekleyin.
2. Her iki sanal makine düğümünde de SIOS Dataman küme sürümünü çalıştırın.
3. SIOS Dataman küme sürümünü, kaynak sanal makineden ek VHD 'ye bağlı birimin içeriğini hedef sanal makinenin ek VHD bağlı birimine yansıtmayacak şekilde yapılandırın. SIOS Dataman, kaynak ve hedef yerel birimleri soyutlar ve sonra bunları bir paylaşılan disk olarak Windows Server Yük Devretme Kümelemesi 'ne gösterir.

[SIOS Verilerman](https://us.sios.com/products/datakeeper-cluster/)hakkında daha fazla bilgi alın.

![Şekil 3: Azure 'da SIOS Dataman ile Windows Server Yük Devretme Kümelemesi yapılandırması][sap-ha-guide-figure-1002]

_**Şekil 3:** Azure 'da SIOS Dataman ile Windows Server Yük Devretme Kümelemesi yapılandırması_

> [!NOTE]
> SQL Server gibi bazı DBMS ürünleriyle yüksek kullanılabilirlik için paylaşılan disklere ihtiyacınız yoktur. Her zaman SQL Server, tek bir küme düğümünün yerel diskinden DBMS verilerini ve günlük dosyalarını başka bir küme düğümünün yerel diskine çoğaltır. Bu durumda, Windows küme yapılandırmasına paylaşılan bir disk gerekmez.
>
>

### <a name="name-resolution-in-azure"></a><a name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a> Azure 'da ad çözümlemesi
Azure bulut platformu, kayan IP adresleri gibi sanal IP adreslerini yapılandırma seçeneği sunmaz. Bulutta küme kaynağına ulaşmak üzere bir sanal IP adresi ayarlamak için alternatif bir çözüme ihtiyacınız vardır.
Azure 'da Azure Load Balancer hizmetinde bir iç yük dengeleyici vardır. İç yük dengeleyici ile istemciler küme sanal IP adresi üzerinden kümeye ulaşabilirler.
İç yük dengeleyiciyi, küme düğümlerini içeren kaynak grubunda dağıtmanız gerekir. Ardından, tüm gerekli bağlantı noktası iletme kurallarını iç yük dengeleyicinin araştırma bağlantı noktalarıyla yapılandırın.
İstemciler sanal ana bilgisayar adı aracılığıyla bağlanabilir. DNS sunucusu, küme IP adresini çözümler ve iç yük dengeleyici, kümenin etkin düğümüne bağlantı noktası iletmeyi işler.

## <a name="sap-netweaver-high-availability-in-azure-infrastructure-as-a-service-iaas"></a><a name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a> Azure hizmet olarak altyapı (IaaS) içinde SAP NetWeaver yüksek kullanılabilirliği
SAP yazılım bileşenleri gibi SAP uygulaması yüksek kullanılabilirlik elde etmek için aşağıdaki bileşenleri korumanız gerekir:

* SAP uygulama sunucusu örneği
* SAP ASCS/SCS örneği
* DBMS sunucusu

Yüksek kullanılabilirlik senaryolarında SAP bileşenlerini koruma hakkında daha fazla bilgi için bkz. [SAP NetWeaver Için Azure sanal makineleri planlama ve uygulama](planning-guide.md).

### <a name="high-availability-sap-application-server"></a><a name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a> Yüksek kullanılabilirliğe sahip SAP uygulama sunucusu
SAP uygulama sunucusu ve iletişim örnekleri için genellikle belirli bir yüksek kullanılabilirlik çözümüne ihtiyacınız yoktur. Yedeklilik ile yüksek kullanılabilirlik elde edersiniz ve Azure sanal makinelerinin farklı örneklerinde birden fazla iletişim kutusu örneği yapılandıracaksınız. İki Azure sanal makine örneğinde yüklü en az iki SAP uygulaması örneği olmalıdır.

![Şekil 4: yüksek kullanılabilirliğe sahip SAP uygulama sunucusu][sap-ha-guide-figure-2000]

_**Şekil 4:** Yüksek kullanılabilirliğe sahip SAP uygulama sunucusu_

SAP uygulama sunucusu örneklerini barındıran tüm sanal makineleri aynı Azure kullanılabilirlik kümesine yerleştirmeniz gerekir. Azure kullanılabilirlik kümesi şunları sağlar:

* Tüm sanal makineler aynı yükseltme etki alanının bir parçasıdır. Örneğin, bir yükseltme etki alanı, planlı bakım kapalı kalma süresi boyunca sanal makinelerin aynı anda güncel olmadığından emin olur.
* Tüm sanal makineler aynı hata etki alanının bir parçasıdır. Örneğin, bir hata etki alanı, sanal makinelerin dağıtıldığından emin olur, böylece tek bir hata noktası tüm sanal makinelerin kullanılabilirliğini etkilemektedir.

[Sanal makinelerin kullanılabilirliğini yönetme][virtual-machines-manage-availability]hakkında daha fazla bilgi edinin.

Azure depolama hesabı olası bir tek hata noktası olduğundan, en az iki sanal makinenin dağıtıldığı en az iki Azure depolama hesabı olması önemlidir. İdeal bir kurulumda, SAP iletişim kutusu örneği çalıştıran her sanal makinenin diskleri farklı bir depolama hesabına dağıtılır.

### <a name="high-availability-sap-ascsscs-instance"></a><a name="f559c285-ee68-4eec-add1-f60fe7b978db"></a> Yüksek kullanılabilirliğe sahip SAP yoks/SCS örneği
Şekil 5, yüksek kullanılabilirliğe sahip SAP yoks/SCS örneğine bir örnektir.

![Şekil 5: yüksek kullanılabilirliğe sahip SAP yoks/SCS örneği][sap-ha-guide-figure-2001]

_**Şekil 5:** Yüksek kullanılabilirliğe sahip SAP yoks/SCS örneği_

#### <a name="sap-ascsscs-instance-high-availability-with-windows-server-failover-clustering-in-azure"></a><a name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a> Azure 'da Windows Server Yük Devretme Kümelemesi ile SAP ASCS/SCS örneği yüksek kullanılabilirliği
Çıplak veya özel bulut dağıtımlarıyla karşılaştırıldığında, Azure sanal makineleri, Windows Server Yük Devretme Kümelemesi 'ni yapılandırmak için ek adımlar gerektirir. Bir Windows Yük devretme kümesi oluşturmak için, bir SAP ASCS/SCS örneği Kümelemeye yönelik bir paylaşılan küme diskine, birkaç IP adresine, birkaç sanal konak adına ve bir Azure iç yük dengeleyicisine sahip olmanız gerekir. Bu konuda makalenin ilerleyen bölümlerinde daha ayrıntılı olarak ele alınmaktadır.

![Şekil 6: Azure 'da, SIOS Dataman kullanarak SAP yoks/SCS yapılandırması için Windows Server Yük Devretme Kümelemesi][sap-ha-guide-figure-1002]

_**Şekil 6:** Azure 'da, SIOS Dataman ile SAP ASCS/SCS yapılandırması için Windows Server Yük Devretme Kümelemesi_

### <a name="high-availability-dbms-instance"></a><a name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a>Yüksek kullanılabilirlik DBMS örneği
DBMS Ayrıca bir SAP sisteminde tek bir iletişim noktasıdır. Yüksek kullanılabilirlik çözümü kullanarak korumanız gerekir. Şekil 7 ' de, Windows Server Yük Devretme Kümelemesi ve Azure iç yük dengeleyici ile Azure 'da SQL Server her zaman yüksek kullanılabilirliğe sahip bir çözüm gösterilmektedir. Her zaman SQL Server, kendi DBMS çoğaltmasını kullanarak DBMS verilerini ve günlük dosyalarını çoğaltır. Bu durumda, tüm kurulumun basitleşerek küme paylaşılan disklere ihtiyacınız yoktur.

![Şekil 7: SQL Server her zaman açık olan yüksek kullanılabilirliğe sahip SAP DBMS örneği][sap-ha-guide-figure-2003]

_**Şekil 7:** SQL Server her zaman açık olan yüksek kullanılabilirliğe sahip SAP DBMS örneği_

Azure 'da Azure Resource Manager dağıtım modelini kullanarak kümeleme SQL Server hakkında daha fazla bilgi için şu makalelere bakın:

* [Kaynak Yöneticisi kullanarak Azure sanal makinelerinde Always on kullanılabilirlik grubunu el ile yapılandırma][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]
* [Azure 'da Always on kullanılabilirlik grubu için bir Azure iç yük dengeleyici yapılandırma][virtual-machines-windows-portal-sql-alwayson-int-listener]

## <a name="end-to-end-high-availability-deployment-scenarios"></a><a name="045252ed-0277-4fc8-8f46-c5a29694a816"></a> Uçtan uca yüksek kullanılabilirliğe sahip dağıtım senaryoları

### <a name="deployment-scenario-using-architectural-template-1"></a>Mimari Şablon 1 kullanarak dağıtım senaryosu

Şekil 8 ' de, **bir** SAP sistemi için Azure 'Da SAP NetWeaver yüksek kullanılabilirlik mimarisi örneği gösterilmektedir. Bu senaryo aşağıdaki şekilde ayarlanır:

- SAP ASCS/SCS örneği için ayrılmış bir küme kullanılır.
- DBMS örneği için ayrılmış bir küme kullanılır.
- SAP uygulama sunucusu örnekleri kendi adanmış VM 'lerine dağıtılır.

![Şekil 8: ASCS/SCS ve DBMS için adanmış küme ile SAP yüksek kullanılabilirliğe sahip mimari Şablon 1][sap-ha-guide-figure-2004]

_**Şekil 8:** SAP yüksek kullanılabilirliğe sahip mimari Şablon 1, yoks/SCS ve DBMS için adanmış kümeler_

### <a name="deployment-scenario-using-architectural-template-2"></a>Mimari şablonu kullanarak dağıtım senaryosu 2

Şekil 9 ' da, **bir** SAP sistemi için Azure 'Da SAP NetWeaver yüksek kullanılabilirliğe sahip bir mimari örneği gösterilmektedir. Bu senaryo aşağıdaki şekilde ayarlanır:

- SAP ASCS/SCS örneği ve **DBMS için bir** adanmış küme kullanılır.
- SAP uygulama sunucusu örnekleri, kendi adanmış VM 'lerde dağıtılır.

![Şekil 9: ass/SCS için adanmış bir küme ve DBMS için adanmış bir küme ile SAP yüksek kullanılabilirliğe sahip mimari Şablon 2][sap-ha-guide-figure-2005]

_**Şekil 9:** SAP yüksek kullanılabilirliğe sahip mimari Şablon 2, yoks/SCS için adanmış bir küme ve DBMS için adanmış bir küme ile_

### <a name="deployment-scenario-using-architectural-template-3"></a>Mimari şablonu kullanarak dağıtım senaryosu 3

Şekil 10 ' da,  &lt; SID1 &gt; ve SID2 ile Iki SAP SISTEMI Için Azure 'da SAP NetWeaver yüksek kullanılabilirlik mimarisine bir örnek gösterilmektedir &lt; &gt; . Bu senaryo aşağıdaki şekilde ayarlanır:

- SAP ASCS/SCS SID1 örneği *ve* SAP ascs/SCS SID2 örneği (bir küme **) için ayrılmış** bir küme kullanılır.
- DBMS SID1 için bir adanmış küme kullanılır ve DBMS SID2 (iki küme) için başka bir adanmış küme kullanılır.
- SAP System SID1 için SAP uygulama sunucusu örneklerinin kendi ayrılmış sanal makineleri vardır.
- SAP System SID2 için SAP uygulama sunucusu örneklerinin kendi ayrılmış sanal makineleri vardır.

![Şekil 10: farklı yoks/SCS örnekleri için adanmış bir küme ile SAP yüksek kullanılabilirliğe sahip mimari Şablon 3][sap-ha-guide-figure-6003]

_**Şekil 10:** Farklı yoks/SCS örnekleri için adanmış bir küme ile SAP yüksek kullanılabilirliğe sahip mimari Şablon 3_

## <a name="prepare-the-infrastructure"></a><a name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a> Altyapıyı hazırlama

### <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Altyapıyı mimari Şablon 1 için hazırlama
SAP için Azure Resource Manager şablonları, gerekli kaynakların dağıtımını basitleştirmeye yardımcı olur.

Azure Resource Manager içindeki üç katmanlı şablonlar, iki kümeye sahip mimari Şablon 1 gibi yüksek kullanılabilirlik senaryolarını de destekler. Her küme SAP ASCS/SCS ve DBMS için SAP tek hata noktasıdır.

Burada, bu makalede anladığımız örnek senaryo için Azure Resource Manager şablonları edinebilirsiniz:

* [Azure Market görüntüsü](https://github.com/Azure/azure-quickstart-templates/)  
* [Özel görüntü](https://github.com/Azure/azure-quickstart-templates/)

Altyapıyı mimari Şablon 1 için hazırlamak için:

- Azure portal, **Parametreler** dikey penceresinde, **Systemavailability** kutusunda **ha**' yi seçin.

  ![Şekil 11: SAP yüksek kullanılabilirlik Azure Resource Manager parametrelerini ayarlama][sap-ha-guide-figure-3000]

_**Şekil 11:** SAP yüksek kullanılabilirlik Azure Resource Manager parametrelerini ayarlama_


  Şablonlar şunları oluşturur:

  * **Sanal makineler**:
    * SAP uygulama sunucusu sanal makineleri: <*Sapsystemsıd*>-dı-<*numarası*>
    * YOKS/SCS kümesi sanal makineleri: <*Sapsystemsıd*>-ascs-<*numarası*>
    * DBMS kümesi: <*Sapsystemsıd*>-db-<*numarası*>

  * **Tüm sanal makineler için, ILIŞKILI IP adresleriyle ağ kartları**:
    * <*Sapsystemsıd*>-NIC-dı-<*numarası*>
    * <*Sapsystemsıd*>-NIC-ascs-<*numarası*>
    * <*Sapsystemsıd*>-NIC-db-<*numarası*>

  * **Azure depolama hesapları**

  * **Kullanılabilirlik grupları** :
    * SAP uygulama sunucusu sanal makineleri: <*Sapsystemsıd*>-avset-dı
    * SAP ASCS/SCS kümesi sanal makineleri: <*Sapsystemsıd*>-avset-ascs
    * DBMS kümesi sanal makineleri: <*Sapsystemsıd*>-avset-DB

  * **Azure iç yük dengeleyici**:
    * YOKS/SCS örneği ve IP adresi için tüm bağlantı noktalarıyla <*Sapsystemsıd*>-lb-ascs
    * SQL Server DBMS ve IP adresi için tüm bağlantı noktalarıyla <*Sapsystemsıd*>-lb-DB

  * **Ağ güvenlik grubu**: <*sapsystemsıd*>-NSG-ascs-0  
    * <*Sapsystemsıd*>-ascs-0 sanal makinesine açık bir dış Uzak Masaüstü Protokolü (RDP) bağlantı noktası ile

> [!NOTE]
> Ağ kartlarının ve Azure iç yük dengeleyicileri 'nin tüm IP adresleri varsayılan olarak **dinamiktir** . Onları **statik** IP adresleriyle değiştirin. Bunun nasıl yapılacağını makalenin ilerleyen bölümlerinde anlatmaktadır.
>
>

### <a name="deploy-virtual-machines-with-corporate-network-connectivity-cross-premises-to-use-in-production"></a><a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> Üretimde kullanmak üzere kurumsal ağ bağlantısı (şirketler arası) ile sanal makineler dağıtma
Üretim SAP sistemlerinde Azure sanal makinelerini Azure siteden siteye VPN veya Azure ExpressRoute kullanarak kurumsal ağ bağlantısı ile dağıtın.

> [!NOTE]
> Azure sanal ağ örneğinizi kullanabilirsiniz. Sanal ağ ve alt ağ zaten oluşturulup hazırlandı.
>
>

1. Azure portal, **Parametreler** dikey penceresinde, **Neworexistingsubnet** kutusunda **var**' ı seçin.
2. **SubnetID** kutusunda, Azure sanal makinelerinizi dağıtmayı planladığınız, hazırlanmış Azure Network SubnetID 'nizin tam dizesini ekleyin.
3. Tüm Azure ağ alt ağlarının listesini almak için şu PowerShell komutunu çalıştırın:

   ```PowerShell
   (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
   ```

   **Kimlik** alanı, **SubnetID**'yi gösterir.
4. Tüm **SubnetID** değerlerinin listesini almak Için Şu PowerShell komutunu çalıştırın:

   ```PowerShell
   (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
   ```

   **SubnetID** şuna benzer:

   ```
   /subscriptions/<SubscriptionId>/resourceGroups/<VPNName>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<SubnetName>
   ```

### <a name="deploy-cloud-only-sap-instances-for-test-and-demo"></a><a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> Test ve tanıtım için yalnızca bulutta SAP örnekleri dağıtın
Yüksek kullanılabilirlik SAP sisteminizi yalnızca bulut dağıtım modelinde dağıtabilirsiniz. Bu tür bir dağıtım öncelikle tanıtım ve test kullanım durumları için yararlıdır. Üretim kullanım örnekleri için uygun değildir.

- Azure portal, **Parametreler** dikey penceresinde, **Neworexistingsubnet** kutusunda **Yeni**' yi seçin. **SubnetID** alanını boş bırakın.

  SAP Azure Resource Manager şablonu, Azure sanal ağını ve alt ağını otomatik olarak oluşturur.

> [!NOTE]
> Aynı Azure sanal ağ örneğindeki Active Directory ve DNS için en az bir ayrılmış sanal makineyi de dağıtmanız gerekir. Şablon bu sanal makineleri oluşturmaz.
>
>


### <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Altyapıyı mimari Şablon 2 için hazırlama

SAP mimari şablonu 2 için gerekli altyapı kaynaklarının dağıtımını basitleştirmeye yardımcı olmak üzere SAP için bu Azure Resource Manager şablonunu kullanabilirsiniz.

Burada, bu dağıtım senaryosu için Azure Resource Manager şablonları edinebilirsiniz:

* [Azure Market görüntüsü](https://github.com/Azure/azure-quickstart-templates/)  
* [Özel görüntü](https://github.com/Azure/azure-quickstart-templates/)


### <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Altyapıyı mimari Şablon 3 için hazırlama

Altyapıyı hazırlayabilir ve **Çoklu SID** için SAP 'yi yapılandırabilirsiniz. Örneğin, *var olan* bir küme yapılandırmasına ek BIR SAP ascs/SCS örneği ekleyebilirsiniz. Daha fazla bilgi için, [Azure Resource Manager ' de SAP çoklu SID yapılandırması oluşturmak üzere mevcut bir küme yapılandırmasına ek SAP ASCS/SCS örneği yapılandırma][sap-ha-multi-sid-guide]konusuna bakın.

Yeni bir çoklu SID kümesi oluşturmak istiyorsanız [GitHub 'da çoklu SID hızlı başlangıç şablonlarını](https://github.com/Azure/azure-quickstart-templates)kullanabilirsiniz.
Yeni bir çoklu SID kümesi oluşturmak için aşağıdaki üç şablonu dağıtmanız gerekir:

* [YOKS/SCS şablonu](#ASCS-SCS-template)
* [Veritabanı şablonu](#database-template)
* [Uygulama sunucuları şablonu](#application-servers-template)

Aşağıdaki bölümlerde, şablonlarda sağlamanız gereken şablonlar ve parametreler hakkında daha fazla ayrıntı verilmektedir.

#### <a name="ascsscs-template"></a><a name="ASCS-SCS-template"></a> YOKS/SCS şablonu

YOKS/SCS şablonu, birden fazla ASCS/SCS örneğini barındıran bir Windows Server yük devretme kümesi oluşturmak için kullanabileceğiniz iki sanal makine dağıtır.

ASCS/SCS çok düzeyli şablonunu ayarlamak için, [yoks/SCS çok düzeyli şablonunda][sap-templates-3-tier-multisid-xscs-marketplace-image]aşağıdaki parametrelerin değerlerini girin:

  - **Kaynak ön eki**.  Dağıtım sırasında oluşturulan tüm kaynakların ön eki için kullanılan kaynak önekini ayarlayın. Kaynaklar yalnızca bir SAP sistemine ait olmadığından, kaynağın ön eki bir SAP sisteminin SID 'SI değildir.  Ön ek **üç ve altı karakter** arasında olmalıdır.
  - **Yığın türü**. SAP sisteminin yığın türünü seçin. Yığın türüne bağlı olarak, Azure Load Balancer SAP sistemine göre yalnızca bir (ABAP veya Java) veya iki (ABAP + Java) özel IP adresi vardır.
  -  **Işletim sistemi türü**. Sanal makinelerin işletim sistemini seçin.
  -  **SAP sistem sayısı**. Bu kümeye yüklemek istediğiniz SAP sistemi sayısını seçin.
  -  **Sistem kullanılabilirliği**. **Ha**'yi seçin.
  -  **Yönetici Kullanıcı adı ve yönetici parolası**. Makinede oturum açmak için kullanılabilecek yeni bir kullanıcı oluşturun.
  -  **Yeni veya var olan alt ağ**. Yeni bir sanal ağın ve alt ağın oluşturulması gerekip gerekmediğini veya mevcut bir alt ağın kullanılıp kullanılmayacağını ayarlayın. Şirket içi ağınıza bağlı bir sanal ağınız zaten varsa, **mevcut**' ı seçin.
  -  **Alt ağ kimliği**. VM 'yi tanımlanmış VM 'ye atanmış bir alt ağa sahip olduğunuz mevcut bir VNet 'e dağıtmak istiyorsanız, söz konusu alt ağın KIMLIĞINI adlandırın. KIMLIK genellikle şöyle görünür:/Subscriptions/<*abonelik kimliği*>/resourceGroups/<*kaynak grubu adı*>/Providers/Microsoft.Network/virtualNetworks/<*sanal ağ adı*>/Subnets/<*alt ağ adı*>

Şablon, birden çok SAP sistemini destekleyen bir Azure Load Balancer örneğini dağıtır.

- ASCS örnekleri, 00, 10, 20 örnek numarası için yapılandırılır...
- SCS örnekleri 01, 11, 21 örnek numarası için yapılandırılır...
- ASCS sıraya alma çoğaltma sunucusu (ERS) (yalnızca Linux) örnekleri, 02, 12, 22 örnek numarası için yapılandırılır...
- SCS (yalnızca Linux) örnekleri, 03, 13, 23 örnek numarası için yapılandırılır...

Yük dengeleyici, 1 (Linux için 2) VIP, ASCS/SCS için 1x VIP ve ERS için 1x VIP (yalnızca Linux) içerir.

Aşağıdaki liste, tüm yük dengeleme kurallarını içerir (burada x, SAP sisteminin sayısıdır, örneğin, 1, 2, 3...):
- Her SAP sistemi için Windows 'a özel bağlantı noktaları: 445, 5985
- YOKS bağlantı noktaları (örnek numarası x0): 32x0, 36x0, 39x0, 81x0, 5x013, 5x014, 5x016
- SCS bağlantı noktaları (örnek numarası x1): 32x1, 33x1, 39x1, 81x1, 5x113, 5x114, 5x116
- Linux 'ta YOKLER bağlantı noktaları (örnek numarası x2): 33x2, 5x213, 5x214, 5x216
- Linux 'ta SCS 'ler bağlantı noktaları (örnek numarası x3): 33x3, 5x313, 5x314, 5x316

Yük dengeleyici, aşağıdaki araştırma bağlantı noktalarını kullanacak şekilde yapılandırılır (burada x, SAP sisteminin numarasıdır, örneğin, 1, 2, 3...):
- YOKS/SCS iç yük dengeleyici araştırma bağlantı noktası: 620x0
- ERS iç yük dengeleyici araştırma bağlantı noktası (yalnızca Linux): 621x2

#### <a name="database-template"></a><a name="database-template"></a> Veritabanı şablonu

Veritabanı şablonu, bir SAP sistemine yönelik ilişkisel veritabanı yönetim sistemini (RDBMS) yüklemek için kullanabileceğiniz bir veya iki sanal makine dağıtır. Örneğin, beş SAP sistemi için bir ASCS/SCS şablonu dağıtırsanız, bu şablonu beş kez dağıtmanız gerekir.

Veritabanı çoklu SID şablonunu ayarlamak için, [veritabanı çoklu SID şablonunda][sap-templates-3-tier-multisid-db-marketplace-image]aşağıdaki parametreler için değerler girin:

- **SAP sistem kimliği**. Yüklemek istediğiniz SAP sisteminin SAP sistem KIMLIĞINI girin. KIMLIK, dağıtılan kaynaklar için bir ön ek olarak kullanılacaktır.
- **Işletim sistemi türü**. Sanal makinelerin işletim sistemini seçin.
- **DbType**. Kümeye yüklemek istediğiniz veritabanının türünü seçin. Microsoft SQL Server yüklemek istiyorsanız **SQL** ' i seçin. SAP HANA sanal makinelere yüklemeyi planlıyorsanız **Hana** ' yı seçin. Doğru işletim sistemi türünü seçtiğinizden emin olun: SQL için **Windows** ' u SEÇIN ve Hana Için bir Linux dağıtımı seçin. Sanal makinelere bağlı Azure Load Balancer seçili veritabanı türünü destekleyecek şekilde yapılandırılacak:
  * **SQL**. Yük dengeleyici 1433 numaralı bağlantı noktasını yük dengelemeye çalışır. SQL Server her zaman kurulum üzerinde bu bağlantı noktasını kullandığınızdan emin olun.
  * **Hana**. Yük dengeleyici 35015 ve 35017 bağlantı noktalarını yük dengelemeye çalışır. **50** örnek numarasıyla SAP HANA yüklediğinizden emin olun.
  Yük dengeleyici, 62550 araştırma bağlantı noktasını kullanır.
- **SAP sistem boyutu**. Yeni sistemin sunmayacak olan SAPS sayısını ayarlayın. Sisteme kaç tane SAPS isteyeceğiz emin değilseniz, SAP Technology Iş ortağınızdan veya sistem tümleştirmenize sorun.
- **Sistem kullanılabilirliği**. **Ha**'yi seçin.
- **Yönetici Kullanıcı adı ve yönetici parolası**. Makinede oturum açmak için kullanılabilecek yeni bir kullanıcı oluşturun.
- **Alt ağ kimliği**. ASCS/SCS şablonunun dağıtımı sırasında kullandığınız alt ağın KIMLIĞINI veya Ass/SCS şablon dağıtımının bir parçası olarak oluşturulan alt ağın KIMLIĞINI girin.

#### <a name="application-servers-template"></a><a name="application-servers-template"></a> Uygulama sunucuları şablonu

Uygulama sunucuları şablonu, bir SAP sistemi için SAP uygulama sunucusu örnekleri olarak kullanılabilecek iki veya daha fazla sanal makine dağıtır. Örneğin, beş SAP sistemi için bir ASCS/SCS şablonu dağıtırsanız, bu şablonu beş kez dağıtmanız gerekir.

Uygulama sunucuları Multi-SID şablonunu ayarlamak için, [uygulama sunucuları çoklu SID şablonu][sap-templates-3-tier-multisid-apps-marketplace-image]' nda, aşağıdaki parametreler için değerler girin:

  -  **SAP sistem kimliği**. Yüklemek istediğiniz SAP sisteminin SAP sistem KIMLIĞINI girin. KIMLIK, dağıtılan kaynaklar için bir ön ek olarak kullanılacaktır.
  -  **Işletim sistemi türü**. Sanal makinelerin işletim sistemini seçin.
  -  **SAP sistem boyutu**. Yeni sistemin sunacaktır SAPS sayısı. Sisteme kaç tane SAPS isteyeceğiz emin değilseniz, SAP Technology Iş ortağınızdan veya sistem tümleştirmenize sorun.
  -  **Sistem kullanılabilirliği**. **Ha**'yi seçin.
  -  **Yönetici Kullanıcı adı ve yönetici parolası**. Makinede oturum açmak için kullanılabilecek yeni bir kullanıcı oluşturun.
  -  **Alt ağ kimliği**. ASCS/SCS şablonunun dağıtımı sırasında kullandığınız alt ağın KIMLIĞINI veya Ass/SCS şablon dağıtımının bir parçası olarak oluşturulan alt ağın KIMLIĞINI girin.


### <a name="azure-virtual-network"></a><a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Azure sanal ağı
Örneğimizde, Azure sanal ağının adres alanı 10.0.0.0/16 ' dır. Adres aralığı 10.0.0.0/24 olan **alt ağ** adlı bir alt ağ vardır. Tüm sanal makineler ve iç yük dengeleyiciler bu sanal ağda dağıtılır.

> [!IMPORTANT]
> Konuk işletim sistemi içindeki ağ ayarlarında herhangi bir değişiklik yapmayın. Buna IP adresleri, DNS sunucuları ve alt ağ dahildir. Tüm ağ ayarlarınızı Azure 'da yapılandırın. Dinamik ana bilgisayar Yapılandırma Protokolü (DHCP) hizmeti, ayarlarınızı yayar.
>
>

### <a name="dns-ip-addresses"></a><a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a> DNS IP adresleri

Gerekli DNS IP adreslerini ayarlamak için aşağıdaki adımları uygulayın.

1. Azure portal, **DNS sunucuları** dikey penceresinde, sanal ağ **DNS SUNUCULARıNıZ** seçeneğinin **özel DNS** olarak ayarlandığından emin olun.
2. Sahip olduğunuz ağ türüne göre ayarlarınızı seçin. Daha fazla bilgi için aşağıdaki kaynaklara bakın:
   * Şirket içi DNS sunucularının IP adreslerini ekleyin.  
   Şirket içi DNS sunucularını Azure 'da çalışan sanal makinelere genişletebilirsiniz. Bu senaryoda, DNS hizmetini çalıştırdığınız Azure sanal makinelerinin IP adreslerini ekleyebilirsiniz.
   * Azure 'da yalıtılmış dağıtımlar için: DNS sunucusu olarak hizmet veren aynı sanal ağ örneğine ek bir sanal makine dağıtın. DNS hizmetini çalıştırmak için ayarladığınız Azure sanal makinelerinin IP adreslerini ekleyin.

   ![Şekil 12: Azure sanal ağı için DNS sunucularını yapılandırma][sap-ha-guide-figure-3001]

   _**Şekil 12:** Azure sanal ağı için DNS sunucularını yapılandırma_

   > [!NOTE]
   > DNS sunucularının IP adreslerini değiştirirseniz, değişikliği uygulamak ve yeni DNS sunucularını yaymak için Azure sanal makinelerini yeniden başlatmanız gerekir.
   >
   >

Örneğimizde, DNS hizmeti bu Windows sanal makinelerine yüklenir ve yapılandırılır:

| Sanal makine rolü | Sanal makine konak adı | Ağ kartı adı | Statik IP adresi |
| --- | --- | --- | --- |
| İlk DNS sunucusu |domcontenr-0 |PR1-NIC-domcontenr-0 |10.0.0.10 |
| İkinci DNS sunucusu |domcontenr-1 |PR1-NIC-domcontenr-1 |10.0.0.11 |

### <a name="host-names-and-static-ip-addresses-for-the-sap-ascsscs-clustered-instance-and-dbms-clustered-instance"></a><a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> SAP ASCS/SCS kümelenmiş örneği ve DBMS kümelenmiş örneği için ana bilgisayar adları ve statik IP adresleri

Şirket içi dağıtım için, bu ayrılmış ana bilgisayar adlarına ve IP adreslerine ihtiyacınız vardır:

| Sanal konak adı rolü | Sanal konak adı | Sanal statik IP adresi |
| --- | --- | --- |
| SAP ASCS/SCS ilk küme sanal ana bilgisayar adı (küme yönetimi için) |PR1-ascs-Vir |10.0.0.42 |
| SAP ASCS/SCS örneği sanal ana bilgisayar adı |PR1-ascs-SAP |10.0.0.43 |
| SAP DBMS ikinci küme sanal ana bilgisayar adı (küme yönetimi) |PR1-DBMS-Vir |10.0.0.32 |

Kümeyi oluşturduğunuzda, **PR1-ascs-Vir** ve **PR1-DBMS-Vir** sanal ana bilgisayar adlarını ve KÜMENIN kendisini yöneten ilişkili IP adreslerini oluşturun. Bunun nasıl yapılacağı hakkında bilgi için bkz. küme [düğümlerini bir küme yapılandırmasında toplama][sap-ha-guide-8.12.1].

Diğer iki sanal ana bilgisayar adını, **PR1-ascs-SAP** ve **PR1-DBMS-SAP** ve DNS sunucusunda ilişkili IP adreslerini el ile oluşturabilirsiniz. Kümelenmiş SAP ASCS/SCS örneği ve kümelenmiş DBMS örneği bu kaynakları kullanır. Bunun nasıl yapılacağı hakkında bilgi için bkz. [KÜMELENMIŞ SAP yoks/SCS örneği için sanal konak adı oluşturma][sap-ha-guide-9.1.1].

### <a name="set-static-ip-addresses-for-the-sap-virtual-machines"></a><a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a> SAP sanal makineleri için statik IP adresleri ayarlama
Kümenizde kullanmak üzere sanal makineleri dağıttıktan sonra, tüm sanal makineler için statik IP adresleri ayarlamanız gerekir. Bunu, Konuk işletim sisteminde değil, Azure sanal ağ yapılandırmasında yapın.

1. Azure Portal, **kaynak grubu**  >  **ağ kartı**  >  **ayarları**  >  **IP adresi**' ni seçin.
2. **IP adresleri** dikey penceresinde **atama** altında **statik**' ı seçin. **IP adresi** kutusuna, kullanmak istediğiniz IP adresini girin.

   > [!NOTE]
   > Ağ kartının IP adresini değiştirirseniz, değişikliği uygulamak için Azure sanal makinelerini yeniden başlatmanız gerekir.  
   >
   >

   ![Şekil 13: her bir sanal makinenin ağ kartı için statik IP adresleri ayarla][sap-ha-guide-figure-3002]

   _**Şekil 13:** Her bir sanal makinenin ağ kartı için statik IP adresleri ayarla_

   Active Directory/DNS hizmetiniz için kullanmak istediğiniz sanal makineler de dahil olmak üzere tüm ağ arabirimleri için bu adımı tekrarlayın.

Örneğimizde şu sanal makineler ve statik IP adresleri vardır:

| Sanal makine rolü | Sanal makine konak adı | Ağ kartı adı | Statik IP adresi |
| --- | --- | --- | --- |
| İlk SAP uygulama sunucusu örneği |PR1-dı-0 |PR1-NIC-dı-0 |10.0.0.50 |
| İkinci SAP uygulama sunucusu örneği |PR1-dı-1 |PR1-NIC-dı-1 |10.0.0.51 |
| ... |... |... |... |
| Son SAP uygulama sunucusu örneği |PR1-dı-5 |PR1-NIC-dı-5 |10.0.0.55 |
| ASCS/SCS örneği için ilk küme düğümü |PR1-ascs-0 |PR1-NIC-ascs-0 |10.0.0.40 |
| ASCS/SCS örneği için ikinci küme düğümü |PR1-ascs-1 |PR1-NIC-ascs-1 |10.0.0.41 |
| DBMS örneği için ilk küme düğümü |PR1-DB-0 |PR1-NIC-DB-0 |10.0.0.30 |
| DBMS örneği için ikinci küme düğümü |PR1-DB-1 |PR1-NIC-DB-1 |10.0.0.31 |

### <a name="set-a-static-ip-address-for-the-azure-internal-load-balancer"></a><a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> Azure iç yük dengeleyici için statik IP adresi ayarlama

SAP Azure Resource Manager şablonu, SAP ASCS/SCS örneği kümesi ve DBMS kümesi için kullanılan bir Azure iç yük dengeleyici oluşturur.

> [!IMPORTANT]
> SAP ASCS/SCS 'nin sanal ana bilgisayar adının IP adresi, SAP ASCS/SCS iç yük dengeleyicinin IP adresiyle aynıdır: **PR1-lb-ascs**.
> DBMS 'nin sanal adının IP adresi, DBMS iç yük dengeleyicinin IP adresiyle aynıdır: **PR1-lb-DBMS**.
>
>

Azure iç yük dengeleyici için bir statik IP adresi ayarlamak için:

1. İlk dağıtım, iç yük dengeleyici IP adresini **dinamik** olarak ayarlar. Azure portal, **IP adresleri** dikey penceresindeki **atama** altında **statik**' ı seçin.
2. **PR1-lb-ascs** iç yük dengeleyicinin IP adresini SAP ascs/SCS örneğinin sanal ana BILGISAYAR adının IP adresine ayarlayın.
3. İç yük dengeleyici **PR1-lb-DBMS** IP ADRESINI, DBMS örneğinin sanal ana BILGISAYAR adının IP adresine ayarlayın.

   ![Şekil 14: SAP ASCS/SCS örneği için iç yük dengeleyici için statik IP adresleri ayarlama][sap-ha-guide-figure-3003]

   _**Şekil 14:** SAP ASCS/SCS örneği için iç yük dengeleyici için statik IP adresleri ayarlama_

Örneğimizde, şu statik IP adreslerine sahip iki Azure iç yük dengeliyoruz:

| Azure iç yük dengeleyici rolü | Azure iç yük dengeleyici adı | Statik IP adresi |
| --- | --- | --- |
| SAP ASCS/SCS örneği iç yük dengeleyici |PR1-lb-yoks |10.0.0.43 |
| SAP DBMS iç yük dengeleyici |PR1-lb-DBMS |10.0.0.33 |


### <a name="default-ascsscs-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Azure iç yük dengeleyici için varsayılan yoks/SCS Yük Dengeleme kuralları

SAP Azure Resource Manager şablonu, ihtiyacınız olan bağlantı noktalarını oluşturur:
* Varsayılan örnek numarası **00** olan BIR ABAP Ass örneği
* Varsayılan örnek numarası **01** olan BIR Java SCS örneği

SAP ASCS/SCS örneğinizi yüklerken, ABAP ASCS örneğiniz için **00** varsayılan örnek numarasını ve Java SCS örneğiniz için varsayılan örnek numarasını ( **01** ) kullanmanız gerekir.

Ardından, SAP NetWeaver bağlantı noktaları için gerekli iç Yük Dengeleme uç noktaları oluşturun.

Gerekli iç Yük Dengeleme uç noktalarını oluşturmak için, SAP NetWeaver ABAP ASCS bağlantı noktalarında bu yük dengeleme uç noktalarını oluşturun:

| Hizmet/Yük Dengeleme kuralı adı | Varsayılan bağlantı noktası numaraları | Somut bağlantı noktaları (örnek numarası 00 olan ASCS örneği) (10 ile) |
| --- | --- | --- |
| Sıraya alma sunucusu/ *lbrule3200* |32<*ınstancenumarası*> |3200 |
| ABAP Message sunucusu/ *lbrule3600* |36<*ınstancenumarası*> |3600 |
| İç ABAP Iletisi/ *lbrule3900* |39<*ınstancenumarası*> |3900 |
| İleti sunucusu HTTP/ *Lbrule8100* |81<*ınstancenumarası*> |8100 |
| SAP başlatma hizmeti yoks HTTP/ *Lbrule50013* |5<*ınstancenumarası*>13 |50013 |
| SAP başlangıç hizmeti yoks HTTPS/ *Lbrule50014* |5<*ınstancenumarası*>14 |50014 |
| Sıraya alma çoğaltması/ *Lbrule50016* |5<*ınstancenumarası*>16 |50016 |
| SAP Start Service ERS HTTP *Lbrule51013* |5<*ınstancenumarası*>13 |51013 |
| SAP Start Service ERS HTTP *Lbrule51014* |5<*ınstancenumarası*>14 |51014 |
| Win RM *Lbrule5985* | |5985 |
| Dosya paylaşma *Lbrule445* | |445 |

_**Tablo 1:** SAP NetWeaver ABAP Ass örneklerinin bağlantı noktası numaraları_

Daha sonra, SAP NetWeaver Java SCS bağlantı noktaları için bu yük dengeleme uç noktalarını oluşturun:

| Hizmet/Yük Dengeleme kuralı adı | Varsayılan bağlantı noktası numaraları | Somut bağlantı noktaları (örneğin, örnek numarası 01 olan SCS örneği) (11 ile ÇÖZÜMLEYICILER) |
| --- | --- | --- |
| Sıraya alma sunucusu/ *lbrule3201* |32<*ınstancenumarası*> |3201 |
| Ağ Geçidi sunucusu/ *lbrule3301* |33<*ınstancenumarası*> |3301 |
| Java Ileti sunucusu/ *lbrule3900* |39<*ınstancenumarası*> |3901 |
| İleti sunucusu HTTP/ *Lbrule8101* |81<*ınstancenumarası*> |8101 |
| SAP başlangıç hizmeti SCS HTTP/ *Lbrule50113* |5<*ınstancenumarası*>13 |50113 |
| SAP başlangıç hizmeti SCS HTTPS/ *Lbrule50114* |5<*ınstancenumarası*>14 |50114 |
| Sıraya alma çoğaltması/ *Lbrule50116* |5<*ınstancenumarası*>16 |50116 |
| SAP Start Service ERS HTTP *Lbrule51113* |5<*ınstancenumarası*>13 |51113 |
| SAP Start Service ERS HTTP *Lbrule51114* |5<*ınstancenumarası*>14 |51114 |
| Win RM *Lbrule5985* | |5985 |
| Dosya paylaşma *Lbrule445* | |445 |

_**Tablo 2:** SAP NetWeaver Java SCS örneklerinin bağlantı noktası numaraları_

![Şekil 15: Azure iç yük dengeleyici için varsayılan yoks/SCS Yük Dengeleme kuralları][sap-ha-guide-figure-3004]

_**Şekil 15:** Azure iç yük dengeleyici için varsayılan yoks/SCS Yük Dengeleme kuralları_

Yük dengeleyici **PR1-lb-DBMS** IP ADRESINI, DBMS örneğinin sanal ana BILGISAYAR adının IP adresine ayarlayın.

### <a name="change-the-ascsscs-default-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Azure iç yük dengeleyici için ASCS/SCS varsayılan Yük Dengeleme kurallarını değiştirme

SAP ASCS veya SCS örnekleri için farklı numaralar kullanmak istiyorsanız, bağlantı noktalarının adlarını ve değerlerini varsayılan değerlerle değiştirmeniz gerekir.

1. Azure Portal **< *SID*>-lb-ascs yük dengeleyici**  >  **Yük Dengeleme kuralları**' nı seçin.
2. SAP ASCS veya SCS örneğine ait olan tüm yük dengeleme kuralları için şu değerleri değiştirin:

   * Name
   * Bağlantı noktası
   * Arka uç bağlantı noktası

   Örneğin, varsayılan ASCS örnek numarasını 00 ' dan 31 ' e değiştirmek istiyorsanız, Tablo 1 ' de listelenen tüm bağlantı noktaları için değişiklikleri yapmanız gerekir.

   Bağlantı noktası *lbrule3200* için bir güncelleştirme örneği aşağıda verilmiştir.

   ![Şekil 16: Azure iç yük dengeleyici için Ass/SCS varsayılan Yük Dengeleme kurallarını değiştirme][sap-ha-guide-figure-3005]

   _**Şekil 16:** Azure iç yük dengeleyici için ASCS/SCS varsayılan Yük Dengeleme kurallarını değiştirme_

### <a name="add-windows-virtual-machines-to-the-domain"></a><a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Windows sanal makinelerini etki alanına ekleme

Sanal makinelere statik bir IP adresi atadıktan sonra, sanal makineleri etki alanına ekleyin.

![Şekil 17: etki alanına sanal makine ekleme][sap-ha-guide-figure-3006]

_**Şekil 17:** Bir etki alanına sanal makine ekleme_

### <a name="add-registry-entries-on-both-cluster-nodes-of-the-sap-ascsscs-instance"></a><a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> SAP ASCS/SCS örneğinin küme düğümlerine kayıt defteri girişleri ekleme

Azure Load Balancer, bağlantılar, belirlenen süre boyunca boşta kaldığında bağlantıları kapatan iç yük dengeleyiciye sahiptir (boşta zaman aşımı). İletişim örneklerinde SAP iş işlemleri, ilk sıraya alma/sıradan çıkarma isteğinin gönderilmesi gerektiği anda SAP sıraya alma işlemine bağlantılar açar. Bu bağlantılar genellikle iş süreci veya sıraya alma işlemi yeniden başlatılana kadar kurulmaya devam eder. Ancak, bağlantı ayarlanan süre boyunca boşta kalırsa, Azure iç yük dengeleyici bağlantıları kapatır. Bu bir sorun değildir çünkü SAP iş işlemi, artık mevcut değilse sıraya alma işlemiyle bağlantıyı yeniden oluşturur. Bu etkinlikler, SAP işlemlerinin geliştirici izlemelerinde belgelenmiştir, ancak bu izlemelerde büyük miktarda ekstra içerik oluşturur. TCP/IP `KeepAliveTime` ve `KeepAliveInterval` her iki küme düğümünde de değişiklik yapmak iyi bir fikirdir. Bu değişiklikleri, makalenin ilerleyen kısımlarında açıklanan SAP profili parametreleriyle TCP/IP parametrelerinde birleştirin.

Yalnızca SAP ASCS/SCS örneğinin küme düğümlerine kayıt defteri girişleri eklemek için, bu Windows kayıt defteri girdilerini SAP Ass/SCS için her iki Windows küme düğümüne de ekleyin:

| Yol | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Değişken adı |`KeepAliveTime` |
| Değişken türü |REG_DWORD (ondalık) |
| Değer |120000 |
| Belge bağlantısı |[https://technet.microsoft.com/library/cc957549.aspx](/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10)) |

_**Tablo 3:** İlk TCP/IP parametresini değiştirme_

Daha sonra, bu Windows kayıt defteri girdilerini SAP yoks/SCS için Windows küme düğümlerine ekleyin:

| Yol | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Değişken adı |`KeepAliveInterval` |
| Değişken türü |REG_DWORD (ondalık) |
| Değer |120000 |
| Belge bağlantısı |[https://technet.microsoft.com/library/cc957548.aspx](/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)) |

_**Tablo 4:** İkinci TCP/IP parametresini değiştirme_

**Değişiklikleri uygulamak için her iki küme düğümünü yeniden başlatın**.

### <a name="set-up-a-windows-server-failover-clustering-cluster-for-an-sap-ascsscs-instance"></a><a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> SAP ASCS/SCS örneği için Windows Server Yük Devretme Kümelemesi kümesi ayarlama

SAP yoks/SCS örneği için Windows Server Yük Devretme Kümelemesi kümesi ayarlamak şu görevleri içerir:

- Küme düğümlerini bir küme yapılandırmasında toplama
- Küme dosya paylaşma tanığını yapılandırma

#### <a name="collect-the-cluster-nodes-in-a-cluster-configuration"></a><a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> Küme düğümlerini bir küme yapılandırmasında toplayın

1. Rol ve Özellik Ekleme Sihirbazı 'nda, her iki küme düğümüne yük devretme kümelemesi ekleyin.
2. Yük Devretme Kümesi Yöneticisi kullanarak yük devretme kümesini ayarlayın. Yük Devretme Kümesi Yöneticisi, **küme oluştur**' u seçin ve ardından yalnızca ilk kümenin adını, A düğümünü ekleyin. İkinci düğümü henüz eklemeyin; ikinci düğümü sonraki bir adımda ekleyeceksiniz.

   ![Şekil 18: ilk küme düğümünün sunucusunu veya sanal makine adını ekleyin][sap-ha-guide-figure-3007]

   _**Şekil 18:** İlk küme düğümünün sunucusunu veya sanal makine adını ekleyin_

3. Kümenin ağ adını (sanal ana bilgisayar adı) girin.

   ![Şekil 19: küme adını girin][sap-ha-guide-figure-3008]

   _**Şekil 19:** Küme adını girin_

4. Kümeyi oluşturduktan sonra bir küme doğrulama testi çalıştırın.

   ![Şekil 20: küme doğrulama denetimini çalıştırın][sap-ha-guide-figure-3009]

   _**Şekil 20:** Küme doğrulama denetimini çalıştırın_

   İşlemdeki bu noktada disklerle ilgili tüm uyarıları yoksayabilirsiniz. Daha sonra bir dosya paylaşım tanığı ve SIOS Paylaşılan diskleri ekleyeceksiniz. Bu aşamada, bir çekirdeğe sahip olmak için endişelenmeniz gerekmez.

   ![Şekil 21: hiçbir çekirdek disk bulunamadı][sap-ha-guide-figure-3010]

   _**Şekil 21:** Hiçbir çekirdek disk bulunamadı_

   ![Şekil 22: çekirdek küme kaynağı için yeni bir IP adresi gerekiyor][sap-ha-guide-figure-3011]

   _**Şekil 22:** Çekirdek küme kaynağı için yeni bir IP adresi gerekiyor_

5. Çekirdek küme hizmetinin IP adresini değiştirin. Sunucu IP adresi sanal makine düğümlerinden birine işaret ettiğinden, küme, çekirdek küme hizmetinin IP adresini değiştirene kadar başlayamaz. Bunu, çekirdek küme hizmetinin IP kaynağının **Özellikler** sayfasında yapın.

   Örneğin, **PR1-ascs-Vir** küme sanal ana bilgisayar adı IÇIN bir IP adresi (örneğimizde, **10.0.0.42**) atanmamız gerekiyor.

   ![Şekil 23: Özellikler iletişim kutusunda IP adresini değiştirme][sap-ha-guide-figure-3012]

   _**Şekil 23:** **Özellikler** iletişim kutusunda, IP adresini değiştirme_

   ![Şekil 24: küme için ayrılan IP adresini atama][sap-ha-guide-figure-3013]

   _**Şekil 24:** Küme için ayrılan IP adresini atama_

6. Küme sanal ana bilgisayar adını çevrimiçi duruma getirin.

   ![Şekil 25: küme çekirdek hizmeti çalışır duruma ve doğru IP adresiyle çalışır][sap-ha-guide-figure-3014]

   _**Şekil 25:** Küme çekirdeği hizmeti çalışır duruma sahip ve doğru IP adresi ile_

7. İkinci küme düğümünü ekleyin.

   Artık çekirdek küme hizmeti çalışır durumda olduğundan, ikinci küme düğümünü ekleyebilirsiniz.

   ![Şekil 26: ikinci küme düğümünü ekleme][sap-ha-guide-figure-3015]

   _**Şekil 26:** İkinci küme düğümünü ekleme_

8. İkinci küme düğümü konağı için bir ad girin.

   ![Şekil 27: ikinci küme düğümü ana bilgisayar adını girin][sap-ha-guide-figure-3016]

   _**Şekil 27:** İkinci küme düğümü ana bilgisayar adını girin_

   > [!IMPORTANT]
   > **Kümeye uygun tüm depolamayı Ekle** onay kutusunun seçili **olmadığından** emin olun.  
   >
   >

   ![Şekil 28: onay kutusunu seçmeyin][sap-ha-guide-figure-3017]

   _**Şekil 28:** Onay **kutusunu seçmeyin**_

   Çekirdek ve disklerle ilgili uyarıları yoksayabilirsiniz. [SAP yoks/SCS küme paylaşma diski IÇIN SIOS Dataman küme sürümü][sap-ha-guide-8.12.3]' nde açıklandığı gibi çekirdeği ayarlar ve diski daha sonra paylaşabilirsiniz.

   ![Şekil 29: disk çekirdeği hakkındaki uyarıları yoksayın][sap-ha-guide-figure-3018]

   _**Şekil 29:** Disk çekirdeği hakkındaki uyarıları yoksay_


#### <a name="configure-a-cluster-file-share-witness"></a><a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> Küme dosyası paylaşma tanığını yapılandırma

Küme dosya paylaşımının tanığını yapılandırmak şu görevleri içerir:

- Dosya paylaşma oluşturma
- Yük Devretme Kümesi Yöneticisi 'de dosya paylaşma tanık çekirdeğini ayarlama

##### <a name="create-a-file-share"></a><a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> Dosya paylaşma oluşturma

1. Çekirdek disk yerine bir dosya paylaşma tanığı seçin. SIOS Dataman bu seçeneği destekler.

   Bu makaledeki örneklerde, dosya paylaşma tanığı Azure 'da çalışan Active Directory/DNS sunucusudur. Dosya paylaşımının tanığı, **domcontenr-0** olarak adlandırılır. Azure 'a bir VPN bağlantısı yapılandırdığınıza (siteden siteye VPN veya Azure ExpressRoute aracılığıyla), Active Directory/DNS hizmetiniz şirket içi ve dosya paylaşma tanığı çalıştırmak için uygun değildir.

   > [!NOTE]
   > Active Directory/DNS hizmetiniz yalnızca şirket içi çalıştırıyorsa, dosya paylaşma tanığını şirket içinde çalışan Active Directory/DNS Windows işletim sisteminde yapılandırmayın. Azure 'da çalışan küme düğümleri ve şirket içi Active Directory/DNS arasındaki ağ gecikmesi çok büyük olabilir ve bağlantı sorunlarına yol açabilir. Dosya paylaşımının tanığını, küme düğümüne kapat çalıştıran bir Azure sanal makinesinde yapılandırmayı unutmayın.  
   >
   >

   Çekirdek sürücüde en az 1.024 MB boş alan gerekir. Çekirdek sürücü için 2.048 MB boş alan öneririz.

2. Küme adı nesnesini ekleyin.

   ![Şekil 30: küme adı nesnesi için paylaşımdaki izinleri atama][sap-ha-guide-figure-3019]

   _**Şekil 30:** Küme adı nesnesi için paylaşımdaki izinleri atama_

   İzinlerin küme adı nesnesi için paylaşımdaki verileri değiştirme yetkisini (bizim örneğimizde, **PR1-ascs-Vir $**) içerdiğinden emin olun.

3. Küme adı nesnesini listeye eklemek için **Ekle**' yi seçin. Şekil 31 ' de gösterilenler 'in yanı sıra, bilgisayar nesnelerini denetlemek için filtreyi değiştirin.

   ![Şekil 31: nesne türlerini bilgisayarları içerecek şekilde değiştirme][sap-ha-guide-figure-3020]

   _**Şekil 31:** Nesne türlerini bilgisayarları içerecek şekilde değiştirme_

   ![Şekil 32: bilgisayarlar onay kutusunu seçin][sap-ha-guide-figure-3021]

   _**Şekil 32:** **Bilgisayarlar** onay kutusunu seçin_

4. Şekil 31 ' de gösterildiği gibi küme adı nesnesini girin. Kayıt zaten oluşturulduğundan, izinleri Şekil 30 ' da gösterildiği gibi değiştirebilirsiniz.

5. Paylaşımın **güvenlik** sekmesini seçin ve ardından küme adı nesnesi için daha ayrıntılı izinler ayarlayın.

   ![Şekil 33: dosya paylaşımında çekirdek üzerinde küme adı nesnesinin güvenlik özniteliklerini ayarlama][sap-ha-guide-figure-3022]

   _**Şekil 33:** Dosya paylaşımında küme adı nesnesinin güvenlik özniteliklerini ayarlama_

##### <a name="set-the-file-share-witness-quorum-in-failover-cluster-manager"></a><a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> Yük Devretme Kümesi Yöneticisi 'de dosya paylaşma tanık çekirdeğini ayarlama

1. Çekirdek ayarlarını yapılandırma Sihirbazı 'Nı açın.

   ![Şekil 34: küme çekirdek ayarlarını yapılandırma Sihirbazı 'Nı başlatma][sap-ha-guide-figure-3023]

   _**Şekil 34:** Küme çekirdek ayarlarını yapılandırma Sihirbazı 'Nı başlatma_

2. **Çekirdek yapılandırması seçin** sayfasında, **çekirdek tanığını Seç**' i seçin.

   ![Şekil 35: aralarından seçim yapabileceğiniz çekirdek yapılandırmalarında][sap-ha-guide-figure-3024]

   _**Şekil 35:** Aralarından seçim yapabileceğiniz çekirdek yapılandırma_

3. **Çekirdek tanığı seçin** sayfasında, **dosya paylaşma tanığını Yapılandır**' ı seçin.

   ![Şekil 36: dosya paylaşma tanığını seçin][sap-ha-guide-figure-3025]

   _**Şekil 36:** Dosya paylaşma tanığını seçin_

4. Dosya paylaşımının UNC yolunu (örneğimizde, \\ domcontr-0\FSW) girin. Yapabileceğiniz değişikliklerin listesini görmek için **İleri**' yi seçin.

   ![Şekil 37: tanık paylaşımının dosya paylaşma konumunu tanımlayın][sap-ha-guide-figure-3026]

   _**Şekil 37:** Tanık paylaşımının dosya paylaşma konumunu tanımlayın_

5. İstediğiniz değişiklikleri seçin ve ardından **İleri**' yi seçin. Şekil 38 ' de gösterildiği gibi küme yapılandırmasını başarıyla yeniden yapılandırmanız gerekir.  

   ![Şekil 38: kümeyi yeniden yapılandırdığınızı onaylama][sap-ha-guide-figure-3027]

   _**Şekil 38:** Kümeyi yeniden yapılandırdığınızı onaylama_

Windows Yük devretme kümesini başarılı bir şekilde yükledikten sonra, yük devretme algılamayı Azure 'daki koşullara uyarlamak için bazı eşiklere değişiklik yapılması gerekir. Değiştirilecek Parametreler bu blogda belgelenmiştir: [https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834) . ASCS/SCS için Windows kümesi yapılandırmasını oluşturan iki VM 'nizin aynı alt ağda olduğunu varsayarsak, aşağıdaki parametrelerin bu değerlere değiştirilmesi gerekir:  
- SameSubNetDelay = 2000  
- SameSubNetThreshold = 15  
- Routinggeçmişini = 30  

Bu ayarlar müşterilerle test edilmiştir ve bir tarafta yeterince dayanıklı olması için iyi bir uzlaşma sağlanması sağladı. Diğer taraftan, bu ayarlar SAP yazılımı veya düğüm/VM hatasında gerçek hata koşullarında yeterince hızlı yük devretme sağlamamıştı. 

### <a name="install-sios-datakeeper-cluster-edition-for-the-sap-ascsscs-cluster-share-disk"></a><a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> SAP ASCS/SCS küme paylaşma diski için SIOS Dataman küme sürümünü yükleyin

Artık Azure 'da çalışan bir Windows Server Yük Devretme Kümelemesi yapılandırmasına sahipsiniz. Ancak, bir SAP ASCS/SCS örneği yüklemek için, paylaşılan bir disk kaynağınız olması gerekir. Azure 'da ihtiyacınız olan paylaşılan disk kaynaklarını oluşturamazsınız. SIOS Dataman küme sürümü, paylaşılan disk kaynakları oluşturmak için kullanabileceğiniz bir üçüncü taraf çözümüdür.

SAP ASCS/SCS küme paylaşma diski için SIOS Dataman küme sürümü yüklemesi şu görevleri içerir:

- 3,5 .NET Framework ekleniyor
- SIOS veri Man 'ı yükleme
- SIOS veri Man 'ı ayarlama

#### <a name="add-the-net-framework-35"></a><a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> 3,5 .NET Framework ekleyin
Microsoft .NET Framework 3,5, Windows Server 2012 R2 'de otomatik olarak etkinleştirilmez veya yüklenmez. , SIOS veri Man 'ı, .NET Framework veri sahibi yüklediğiniz tüm düğümlerde olmasını gerektirdiğinden, kümedeki tüm sanal makinelerin Konuk işletim sistemine 3,5 .NET Framework yüklenmesi gerekir.

3,5 .NET Framework eklemenin iki yolu vardır:

- Şekil 39 ' de gösterildiği gibi Windows 'daki rol ve Özellik Ekleme Sihirbazı ' nı kullanın.

  ![Şekil 39: rol ve Özellik Ekleme Sihirbazı 'Nı kullanarak 3,5 .NET Framework 'Yi yükler][sap-ha-guide-figure-3028]

  _**Şekil 39:** Rol ve Özellik Ekleme Sihirbazı 'Nı kullanarak .NET Framework 3,5 ' ü yükler_

  ![Şekil 40: rol ve Özellik Ekleme Sihirbazı 'Nı kullanarak .NET Framework 3,5 yüklediğinizde yükleme ilerleme çubuğu][sap-ha-guide-figure-3029]

  _**Şekil 40:** Rol ve Özellik Ekleme Sihirbazı 'Nı kullanarak .NET Framework 3,5 yüklediğinizde yükleme ilerleme çubuğu_

- dism.exe komut satırı aracını kullanın. Bu yükleme türü için, Windows yükleme medyasında SxS dizinine erişmeniz gerekir. Yükseltilmiş bir komut isteminde şunu yazın:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

#### <a name="install-sios-datakeeper"></a><a name="dd41d5a2-8083-415b-9878-839652812102"></a> SIOS Dataman 'ı yükler

Kümedeki her düğüme SIOS Dataman küme sürümü yükleyin. Bir sanal paylaşılan depolama alanını, SIOS \ veri Man ile oluşturmak için eşitlenmiş bir yansıtma oluşturun ve küme paylaşılan depolama benzetimi yapın.

SIOS yazılımını yüklemeden önce, Kullanıcı **veri birimi Persvc** etki alanını oluşturun.

> [!NOTE]
> **Data, Persvc** kullanıcısını her iki küme düğümünde **yerel yönetici** grubuna ekleyin.
>
>

SIOS Dataman 'ı yüklemek için:

1. Her iki küme düğümüne da SIOS yazılımını yükler.

   ![SIOS yükleyicisi][sap-ha-guide-figure-3030]

   ![Şekil 41: SIOS Dataman yüklemesinin Ilk sayfası][sap-ha-guide-figure-3031]

   _**Şekil 41:** SIOS Dataman yüklemesinin ilk sayfası_

2. Şekil 42 ' de gösterilen iletişim kutusunda **Evet**' i seçin.

   ![Şekil 42: Dataman bir hizmetin devre dışı bırakıldığını bildirir][sap-ha-guide-figure-3032]

   _**Şekil 42:** Dataman, bir hizmetin devre dışı bırakılacağını bildirir_

3. Şekil 43 ' de gösterilen iletişim kutusunda **etki alanı veya sunucu hesabı**' nı seçmenizi öneririz.

   ![Şekil 43: SIOS veri Man için Kullanıcı seçimi][sap-ha-guide-figure-3033]

   _**Şekil 43:** SIOS veri Man için Kullanıcı seçimi_

4. Bir etki alanı hesabı Kullanıcı adı ve SIOS veri Man için oluşturduğunuz parolaları girin.

   ![Şekil 44: SIOS Dataman yüklemesi için etki alanı Kullanıcı adı ve parolasını girin][sap-ha-guide-figure-3034]

   _**Şekil 44:** SIOS Dataman yüklemesi için etki alanı Kullanıcı adı ve parolasını girin_

5. Şekil 45 ' de gösterildiği gibi, SIOS Dataman örneğiniz için lisans anahtarını yükler.

   ![Şekil 45: SIOS Dataman lisans anahtarınızı girin][sap-ha-guide-figure-3035]

   _**Şekil 45:** SIOS Dataman lisans anahtarınızı girin_

6. İstendiğinde, sanal makineyi yeniden başlatın.

#### <a name="set-up-sios-datakeeper"></a><a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> SIOS Dataman 'ı ayarlama

Her iki düğüme de SIOS veri Man 'ı yükledikten sonra yapılandırmayı başlatmanız gerekir. Yapılandırmanın amacı, sanal makinelerin her birine bağlı ek VHD 'ler arasında zaman uyumlu veri çoğaltmasına sahip olmaktır.

1. Dataman yönetimi ve yapılandırma aracını başlatın ve ardından **sunucuyu bağla**' yı seçin. (Şekil 46 ' de bu seçenek, daire içinde kırmızı renkte olur.)

   ![Şekil 46: SIOS Dataman yönetimi ve yapılandırma aracı][sap-ha-guide-figure-3036]

   _**Şekil 46:** SIOS Dataman yönetimi ve yapılandırma aracı_

2. Yönetim ve yapılandırma aracının bağlanması gereken ilk düğümün adını veya TCP/IP adresini ve ikinci bir adımda ikinci düğümü girin.

   ![Şekil 47: yönetim ve yapılandırma aracının bağlanması gereken ilk düğümün adını veya TCP/IP adresini veya ikinci bir adımda ikinci bir adımda ekleyin][sap-ha-guide-figure-3037]

   _**Şekil 47:** Yönetim ve yapılandırma aracının bağlanması gereken ilk düğümün adını veya TCP/IP adresini, ikinci bir adımda ise ikinci düğümü ekleyin_

3. İki düğüm arasında çoğaltma işi oluşturun.

   ![Şekil 48: çoğaltma işi oluşturma][sap-ha-guide-figure-3038]

   _**Şekil 48:** Çoğaltma işi oluşturma_

   Bir sihirbaz, çoğaltma işi oluşturma sürecinde size rehberlik eder.
4. Kaynak düğümün adını, TCP/IP adresini ve disk birimini tanımlayın.

   ![Şekil 49: çoğaltma işinin adını tanımlayın][sap-ha-guide-figure-3039]

   _**Şekil 49:** Çoğaltma işinin adını tanımlayın_

   ![Şekil 50: düğüm için, geçerli kaynak düğümü olması gereken temel verileri tanımlayın][sap-ha-guide-figure-3040]

   _**Şekil 50:** Düğüm için, geçerli kaynak düğümü olması gereken temel verileri tanımlayın_

5. Hedef düğümün adını, TCP/IP adresini ve disk birimini tanımlayın.

   ![Şekil 51: düğüm için, geçerli hedef düğüm olması gereken temel verileri tanımlayın][sap-ha-guide-figure-3041]

   _**Şekil 51:** Düğüm için, geçerli hedef düğüm olması gereken temel verileri tanımlayın_

6. Sıkıştırma algoritmalarını tanımlayın. Örneğimizde, çoğaltma akışını sıkıştırmanız önerilir. Özellikle yeniden eşitleme durumlarında, çoğaltma akışının sıkıştırılması, yeniden eşitleme süresini önemli ölçüde azaltır. Sıkıştırmanın bir sanal makinenin CPU ve RAM kaynaklarını kullandığını unutmayın. Sıkıştırma hızı arttıkça, CPU kaynakları birimi kullanılır. Bu ayarı daha sonra da ayarlayabilirsiniz.

7. Kopyalamanın zaman uyumsuz olarak mı yoksa zaman uyumlu mı oluştuğunu denetlemeniz gereken başka bir ayar. *SAP ASCS/SCS yapılandırmasını koruduğunuzda, zaman uyumlu çoğaltma kullanmanız gerekir*.  

   ![Şekil 52: çoğaltma ayrıntılarını tanımlama][sap-ha-guide-figure-3042]

   _**Şekil 52:** Çoğaltma ayrıntılarını tanımlama_

8. Çoğaltma işi tarafından çoğaltılan birimin paylaşılan disk olarak bir Windows Server Yük Devretme Kümelemesi kümesi yapılandırmasına temsil edilip edilmeyeceğini tanımlayın. SAP ASCS/SCS yapılandırması için, Windows kümesinin çoğaltılan birimi küme birimi olarak kullanabileceği paylaşılan bir disk olarak görmesi için **Evet** ' i seçin.

   ![Şekil 53: çoğaltılan birimi bir küme birimi olarak ayarlamak için Evet ' i seçin][sap-ha-guide-figure-3043]

   _**Şekil 53:** Çoğaltılan birimi bir küme birimi olarak ayarlamak için **Evet** ' i seçin_

   Birim oluşturulduktan sonra, veri Man Yönetimi ve yapılandırma aracı, çoğaltma işinin etkin olduğunu gösterir.

   ![Şekil 54: SAP ASCS/SCS paylaşılan diski için Dataman zaman uyumlu yansıtma etkin][sap-ha-guide-figure-3044]

   _**Şekil 54:** SAP yoks/SCS paylaşılan diski için Dataman zaman uyumlu yansıtma etkin_

   Yük Devretme Kümesi Yöneticisi Şekil 55 ' de gösterildiği gibi artık diski bir Dataman diski olarak gösterir.

   ![Şekil 55: Yük Devretme Kümesi Yöneticisi, Dataman 'ın çoğaltılan diski gösterir][sap-ha-guide-figure-3045]

   _**Şekil 55:** Yük Devretme Kümesi Yöneticisi, Dataman 'ın çoğaltılan diski gösterir_

## <a name="install-the-sap-netweaver-system"></a><a name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a> SAP NetWeaver sistemini yükler

Kurulum, kullandığınız DBMS sistemine bağlı olarak farklılık gösterdiğinden, DBMS kurulumunu açıklamayamayacağız. Bununla birlikte, DBMS ile ilgili yüksek kullanılabilirliğe sahip olan kaygıları Azure için farklı DBMS satıcıları desteğinin işlevleri ile ilgilendiririz. Örneğin, SQL Server için her zaman açık veya veritabanı yansıtma ve Oracle veritabanları için Oracle Data Guard. Bu makalede kullandığımız senaryoda, DBMS 'ye daha fazla koruma ekleyeceğiz.

Farklı DBMS Hizmetleri, Azure 'da bu tür bir kümelenmiş SAP ASCS/SCS yapılandırmasıyla etkileşen özel bir dikkat edin.

> [!NOTE]
> SAP NetWeaver ABAP Systems, Java sistemleri ve ABAP + Java sistemlerinin yükleme yordamları neredeyse aynıdır. En önemli fark, SAP ABAP sisteminin bir ASCS örneğine sahip olması gerektiğidir. SAP Java sisteminde bir SCS örneği vardır. SAP ABAP + Java sisteminde bir ASCS örneği ve aynı Microsoft yük devretme kümesi grubunda çalışan bir SCS örneği vardır. Her SAP NetWeaver yükleme yığını için herhangi bir yükleme farkı açıkça bahsedilir. Diğer tüm parçaların aynı olduğunu varsayabilirsiniz.  
>
>

### <a name="install-sap-with-a-high-availability-ascsscs-instance"></a><a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a> Yüksek kullanılabilirliğe sahip bir ASCS/SCS örneğiyle SAP 'yi yükler

> [!IMPORTANT]
> Sayfa dosyanızı veri ve yansıtmalı birimlere yerleştirdiğinizden emin olun. Dataman yansıtılmış birimleri desteklemez. Sayfa dosyanızı, varsayılan olan bir Azure sanal makinesinin geçici sürücüsünde bırakabilirsiniz. Henüz orada yoksa, Windows sayfa dosyasını Azure sanal makinenizin D sürücüsüne taşıyın.
>
>

SAP 'yi yüksek kullanılabilirliğe sahip bir ASCS/SCS örneğiyle yüklemek şu görevleri içerir:

- Kümelenmiş SAP yoks/SCS örneği için sanal ana bilgisayar adı oluşturma
- SAP ilk küme düğümünü yükleme
- ASCS/SCS örneğinin SAP profilini değiştirme
- Araştırma bağlantı noktası ekleme
- Windows Güvenlik Duvarı araştırma bağlantı noktasını açma

#### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a><a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a> Kümelenmiş SAP Ass/SCS örneği için sanal ana bilgisayar adı oluşturma

1. Windows DNS Yöneticisi 'nde, yoks/SCS örneğinin sanal ana bilgisayar adı için bir DNS girişi oluşturun.

   > [!IMPORTANT]
   > Ascs/SCS örneğinin sanal ana bilgisayar adına atadığınız IP adresi, Azure Load Balancer atadığınız IP adresiyle aynı olmalıdır (**< *SID*>-lb-ascs**).  
   >
   >

   Sanal SAP ASCS/SCS ana bilgisayar adının (**PR1-ascs-SAP**) ıp adresi Azure Load Balancer (**PR1-lb-ascs**) IP adresiyle aynıdır.

   ![Şekil 56: SAP ASCS/SCS kümesi sanal adı ve TCP/IP adresi için DNS girişi tanımlama][sap-ha-guide-figure-3046]

   _**Şekil 56:** SAP ASCS/SCS kümesi sanal adı ve TCP/IP adresi için DNS girişi tanımlayın_

2. Sanal ana bilgisayar adına atanan IP adresini tanımlamak için **DNS Yöneticisi**  >  **etki alanı**' nı seçin.

   ![Şekil 57: SAP Ass/SCS küme yapılandırması için yeni sanal ad ve TCP/IP adresi][sap-ha-guide-figure-3047]

   _**Şekil 57:** SAP Ass/SCS küme yapılandırması için yeni sanal ad ve TCP/IP adresi_

#### <a name="install-the-sap-first-cluster-node"></a><a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> SAP ilk küme düğümünü yükler

1. Küme düğümü A 'da ilk küme düğümü seçeneğini yürütün. Örneğin, **PR1-ascs-0** ana bilgisayarında.
2. Azure iç yük dengeleyici için varsayılan bağlantı noktalarını tutmak üzere şunları seçin:

   * **ABAP sistemi**: **ascs** örnek numarası **00**
   * **Java sistemi**: **SCS** örnek numarası **01**
   * **ABAP + Java sistemi**: **Ass** örnek numarası **00** ve **SCS** örnek numarası **01**

   ABAP Ass örneği için 00 dışındaki örnek numaralarını ve Java SCS örneği için 01 ' i kullanmak için, ilk olarak Azure [iç yük dengeleyici IÇIN ascs/SCS varsayılan Yük Dengeleme kurallarını değiştirme][sap-ha-guide-8.9]bölümünde açıklanan Azure iç yük dengeleyici varsayılan Yük Dengeleme kurallarını değiştirmeniz gerekir.

Sonraki birkaç görev standart SAP yükleme belgelerinde açıklanmamıştır.

> [!NOTE]
> SAP yükleme belgeleri, ilk ASCS/SCS küme düğümünün nasıl yükleneceğini açıklar.
>
>

#### <a name="modify-the-sap-profile-of-the-ascsscs-instance"></a><a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> ASCS/SCS örneğinin SAP profilini değiştirme

Yeni bir profil parametresi eklemeniz gerekiyor. Profil parametresi, SAP iş işlemleriyle sıraya alma sunucusu arasındaki bağlantıları çok uzun süre boşta kaldığında kapatmadan önler. [SAP ASCS/SCS örneğinin küme düğümlerine kayıt defteri girişleri ekleme][sap-ha-guide-8.11]' de sorun senaryosunu belirttik. Bu bölümde, bazı temel TCP/IP bağlantı parametrelerinde da iki değişiklik yapılmıştır. İkinci bir adımda, `keep_alive` bağlantıların Azure iç yük dengeleyicinin boşta eşiğine ulaşmaması için sıraya alma sunucusunu bir sinyal gönderecek şekilde ayarlamanız gerekir.

YOKS/SCS örneğinin SAP profilini değiştirmek için:

1. ENSA1 kullanıyorsanız, bu profile parametresini SAP ASCS/SCS örnek profiline ekleyin:

   ```
   enque/encni/set_so_keepalive = true
   ```
   Örneğimizde yol şu şekilde olur:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

   Örneğin, SAP SCS örnek profiline ve karşılık gelen yola:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

   Hem ENSA1 hem de ENSA2 için, `keepalive` işletim sistemi PARAMETRELERININ SAP note [1410736](https://launchpad.support.sap.com/#/notes/1410736)' de açıklandığı gibi ayarlandığından emin olun.  

2. Değişiklikleri uygulamak için SAP ASCS/SCS örneğini yeniden başlatın.

#### <a name="add-a-probe-port"></a><a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> Araştırma bağlantı noktası ekle

Tüm küme yapılandırmasının Azure Load Balancer ile çalışmasını sağlamak için iç yük dengeleyicinin araştırma işlevini kullanın. Azure iç yük dengeleyici, genellikle katılan sanal makineler arasında eşit olarak gelen iş yükünü dağıtır. Ancak, yalnızca bir örnek etkin olduğundan, bazı küme yapılandırmalarında bu çalışmaz. Diğer örnek pasif ve iş yükünün hiçbirini kabul edemiyor. Araştırma işlevselliği, Azure iç yük dengeleyicinin yalnızca etkin bir örneğe iş atarken çalışmasına yardımcı olur. Araştırma işlevselliğiyle, iç yük dengeleyici hangi örneklerin etkin olduğunu algılayabilir ve sonra yalnızca örneği iş yüküne hedefleyebilir.

Araştırma bağlantı noktası eklemek için:

1. Aşağıdaki PowerShell komutunu çalıştırarak geçerli **Probeport** ayarını denetleyin. Küme yapılandırmasındaki sanal makinelerden birini içinden yürütün.

   ```PowerShell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
   ```

2. Bir yoklama bağlantı noktası tanımlayın. Varsayılan yoklama bağlantı noktası numarası **0**' dır. Örneğimizde araştırma bağlantı noktası **62000**' i kullanıyoruz.

   ![Şekil 58: küme yapılandırma araştırması bağlantı noktası varsayılan olarak 0 ' dır][sap-ha-guide-figure-3048]

   _**Şekil 58:** Varsayılan küme yapılandırma araştırması bağlantı noktası 0 ' dır_

   Bağlantı noktası numarası SAP Azure Resource Manager şablonlarında tanımlanmıştır. Bağlantı noktası numarasını PowerShell 'e atayabilirsiniz.

   **SAP <*SID*> IP** kümesi kaynağı Için yeni bir probeport değeri ayarlamak için aşağıdaki PowerShell betiğini çalıştırın. Ortamınız için PowerShell değişkenlerini güncelleştirin. Betik çalıştıktan sonra, değişiklikleri etkinleştirmek için SAP küme grubunu yeniden başlatmanız istenir.

   ```PowerShell
   $SAPSID = "PR1"      # SAP <SID>
   $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

   Clear-Host
   $SAPClusterRoleName = "SAP $SAPSID"
   $SAPIPresourceName = "SAP $SAPSID IP"
   $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
   $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
   $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
   $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
   $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
   $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
   $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value

   $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }

   Write-Host "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" -ForegroundColor Cyan
   Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter

   Write-Host
   Write-Host "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." -ForegroundColor Cyan
   Write-Host
   Write-Host "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." -ForegroundColor Cyan
   Write-Host

   $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}

   Write-Host

   $ActivateChanges = Read-Host "Do you want to take restart SAP cluster role '$SAPClusterRoleName', to activate the changes (yes/no)?"

   if($ActivateChanges -eq "yes"){
   Write-Host
   Write-Host "Activating changes..." -ForegroundColor Cyan

   Write-Host
   write-host "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..." -ForegroundColor Cyan
   Stop-ClusterResource -Name $SAPIPresourceName
   sleep 5

   Write-Host "Starting SAP cluster role '$SAPClusterRoleName' ..." -ForegroundColor Cyan
   Start-ClusterGroup -Name $SAPClusterRoleName

   Write-Host "New ProbePort parameter is active." -ForegroundColor Green
   Write-Host

   Write-Host "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" -ForegroundColor Cyan
   Write-Host
   Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
   }else
   {
   Write-Host "Changes are not activated."
   }
   ```

   **SAP <*SID* >** küme rolünü çevrimiçine geçirdikten sonra, **probeport** değerinin yeni değere ayarlandığını doğrulayın.

   ```PowerShell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

   ```

   ![Şekil 59: yeni değeri ayarladıktan sonra küme bağlantı noktasını araştırma][sap-ha-guide-figure-3049]

   _**Şekil 59:** Yeni değeri ayarladıktan sonra küme bağlantı noktasını araştırma_

#### <a name="open-the-windows-firewall-probe-port"></a><a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a> Windows Güvenlik Duvarı araştırma bağlantı noktasını açın

Her iki küme düğümünde bir Windows Güvenlik Duvarı araştırma bağlantı noktası açmanız gerekir. Windows Güvenlik Duvarı araştırması bağlantı noktasını açmak için aşağıdaki betiği kullanın. Ortamınız için PowerShell değişkenlerini güncelleştirin.

  ```PowerShell
  $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

**Probeport** **62000** olarak ayarlanır. Artık **\\ \ascsha-clsap\sapmnt** dosya paylaşımıyla, **assha-DBAS** gibi diğer konaklardan erişebilirsiniz.

### <a name="install-the-database-instance"></a><a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> Veritabanı örneğini yükler

Veritabanı örneğini yüklemek için SAP yükleme belgelerinde açıklanan süreci izleyin.

### <a name="install-the-second-cluster-node"></a><a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> İkinci küme düğümünü yükler

İkinci kümeyi yüklemek için SAP yükleme kılavuzundaki adımları izleyin.

### <a name="change-the-start-type-of-the-sap-ers-windows-service-instance"></a><a name="094bc895-31d4-4471-91cc-1513b64e406a"></a> SAP ERS Windows hizmeti örneğinin başlangıç türünü değiştirme

SAP 'nin Windows hizmeti başlangıç türünü, her iki küme düğümünde da **Otomatik (Gecikmeli başlatma)** olarak değiştirin.

![Şekil 60: SAP ERS örneği için hizmet türünü Gecikmeli otomatik olarak değiştirme][sap-ha-guide-figure-3050]

_**Şekil 60:** SAP ERS örneği için hizmet türünü Gecikmeli otomatik olarak değiştirme_

### <a name="install-the-sap-primary-application-server"></a><a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> SAP birincil uygulama sunucusunu yükler

PAS 'yi barındırmak için belirlediğiniz sanal makinede *sıd*>-dı-0 <birincil uygulama sunucusu (pas) örneğini yükleyebilirsiniz. Azure veya Dataman 'a özgü ayarlar üzerinde hiçbir bağımlılık yoktur.

### <a name="install-the-sap-additional-application-server"></a><a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> SAP ek uygulama sunucusunu yükler

SAP uygulama sunucusu örneğini barındırmak için belirlediğiniz tüm sanal makinelere bir SAP ek uygulama sunucusu (AAS) yükleyebilirsiniz. Örneğin, <*sıd*>-dı-1 Ile <*SID*>-dı- &lt; n &gt; .

> [!NOTE]
> Bu, yüksek kullanılabilirliğe sahip SAP NetWeaver sisteminin yüklenmesini sonlandırır. Sonra, yük devretme testi ile devam edin.
>


## <a name="test-the-sap-ascsscs-instance-failover-and-sios-replication"></a><a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> SAP ASCS/SCS örneği yük devretme ve SIOS çoğaltmasını test etme
Yük Devretme Kümesi Yöneticisi ve SIOS Dataman yönetimi ve yapılandırma aracını kullanarak SAP yoks/SCS örneği yük devretmesini ve SIOS disk çoğaltmasını test etmek ve izlemek kolaydır.

### <a name="sap-ascsscs-instance-is-running-on-cluster-node-a"></a><a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a> SAP ASCS/SCS örneği, küme düğümü A 'da çalışıyor

**SAP PR1** küme grubu, A küme düğümü üzerinde çalışıyor. Örneğin, **PR1-ascs-0** üzerinde. **SAP PR1** küme grubunun bir parçası olan ve ascs/SCS örneğinin kullandığı paylaşılan disk sürücüsünü, A düğümünü Kümelendirmek için atayın.

![Şekil 61: Yük Devretme Kümesi Yöneticisi: SAP <SID> cluster grubu A kümesi düğümünde çalışıyor][sap-ha-guide-figure-5000]

_**Şekil 61:** Yük Devretme Kümesi Yöneticisi: SAP <*sıd*> kümesi grubu A kümesi düğümünde çalışıyor_

SIOS Verilerlik yönetimi ve yapılandırma aracında, paylaşılan disk verilerinin, A küme düğümü üzerindeki kaynak birim sürücüsünden, B küme düğümü üzerindeki hedef birim sürücüsüne zaman uyumlu olarak çoğaltıldığından emin olabilirsiniz. Örneğin, **PR1-ascs-0 [10.0.0.40]** öğesinden **PR1-ascs-1 [10.0.0.41]** arasında çoğaltılır.

![Şekil 62: SIOS Dataman 'Da, yerel birimi A küme düğümünden A 'yı küme düğümüne çoğaltma B][sap-ha-guide-figure-5001]

_**Şekil 62:** Jımdataman 'da yerel birimi küme düğümü A 'yı küme düğümüne çoğaltma B_

### <a name="failover-from-node-a-to-node-b"></a><a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a> A düğümünden B düğümüne yük devretme

1. A küme düğümünden B kümesine SAP <*sıd*> küme grubunun yük devretmesini başlatmak için şu seçeneklerden birini seçin:
   - Yük Devretme Kümesi Yöneticisi kullan  
   - Yük devretme kümesi PowerShell 'i kullanma

   ```PowerShell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPClusterGroup = "SAP $SAPSID"
   Move-ClusterGroup -Name $SAPClusterGroup

   ```
2. Windows Konuk işletim sisteminde küme düğümünü bir yeniden başlatın (Bu, A düğümünden B düğümüne) SAP <*sıd*> küme grubunun otomatik yük devretmesini başlatır).  
3. Azure portal küme düğümünü A 'dan yeniden başlatın (Bu, A düğümünden B düğümüne) SAP <*sıd*> küme grubunun otomatik yük devretmesini başlatır).  
4. Azure PowerShell kullanarak küme düğümünü yeniden başlatma (Bu, A düğümünden B düğümüne, SAP <*sıd*> küme grubunun otomatik yük devretmesini başlatır).

   Yük devretmeden sonra SAP <*sıd*> küme grubu B küme düğümünde çalışıyor. Örneğin, **PR1-ascs-1** üzerinde çalışıyor.

   ![Şekil 63: Yük Devretme Kümesi Yöneticisi ' de, SAP <SID> küme düğümü B üzerinde çalışıyor][sap-ha-guide-figure-5002]

   _**Şekil 63**: yük devretme kümesi Yöneticisi ' de, SAP <*SID*> küme düğümü B üzerinde çalışıyor_

   Paylaşılan disk artık B küme düğümüne takıyor. SIOS Verilerman, küme düğümü B 'deki kaynak birim sürücüsünden verileri, A küme düğümü üzerindeki hedef birim sürücüsüne çoğaltmakta. Örneğin, **PR1-ascs-1 [10.0.0.41]** öğesinden **PR1-ascs-0 [10.0.0.40]** arasında çoğaltılıyor.

   ![Şekil 64: SIOS Dataman yerel birimi B küme düğümünden A kümesine çoğaltır][sap-ha-guide-figure-5003]

   _**Şekil 64:** SIOS Dataman, yerel birimi B küme düğümünden A kümesine çoğaltır_
