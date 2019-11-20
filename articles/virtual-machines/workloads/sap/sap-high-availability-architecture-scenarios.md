---
title: Azure sanal makineler SAP NetWeaver için yüksek kullanılabilirliğe sahip mimari ve senaryolar | Microsoft Docs
description: Azure sanal makinelerde SAP NetWeaver için yüksek kullanılabilirliğe sahip mimari ve senaryolar
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/21/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f4352fbf71b23aedc1dddd3e454b58196d4f5a6e
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70078465"
---
# <a name="high-availability-architecture-and-scenarios-for-sap-netweaver"></a>SAP NetWeaver için yüksek kullanılabilirliğe sahip mimari ve senaryolar

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[sap-installation-guides]:http://service.sap.com/instguides

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-ascs-ha-multi-sid-wsfc-file-share]:sap-ascs-ha-multi-sid-wsfc-file-share.md
[sap-ascs-ha-multi-sid-wsfc-shared-disk]:sap-ascs-ha-multi-sid-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md
[sap-suse-ascs-ha-anf]:high-availability-guide-suse-netapp-files.md
[sap-higher-availability]:sap-higher-availability-architecture-scenarios.md

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

[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md

[sap-ha-bc-virtual-env-hyperv-vmware-white-paper]:https://scn.sap.com/docs/DOC-44415
[sap-ha-partner-information]:https://scn.sap.com/docs/DOC-8541
[azure-sla]:https://azure.microsoft.com/support/legal/sla/
[azure-virtual-machines-manage-availability]:https://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability
[azure-storage-redundancy]:https://azure.microsoft.com/documentation/articles/storage-redundancy/
[azure-storage-managed-disks-overview]:https://docs.microsoft.com/azure/storage/storage-managed-disks-overview

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

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP çoklu SID yüksek kullanılabilirlik yapılandırması)


[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


## <a name="terminology-definitions"></a>Terminoloji tanımları

**Yüksek kullanılabilirlik**: *Aynı* veri merkezinde yer alan yedekli, hataya dayanıklı veya yük devretme korumalı BILEŞENLER aracılığıyla BT hizmetlerinin iş sürekliliği sunarak, bu kesintileri en aza indirecek bir teknoloji kümesini ifade eder. Bu durumda, veri merkezi bir Azure bölgesi içinde bulunur.

**Olağanüstü durum kurtarma**: Ayrıca, BT Hizmetleri kesintisini ve bunların kurtarmasının en aza indirmesine, ancak bir diğerinden yüzlerce mil olabilecek *çeşitli* veri merkezlerinde da ifade eder. Bu durumda, veri merkezleri aynı geopolitik bölgede veya bir müşteri olarak sizin tarafınızdan belirlenen konumlarda yer alan çeşitli Azure bölgelerinde yer alabilir.


## <a name="overview-of-high-availability"></a>Yüksek kullanılabilirliğe genel bakış
Azure 'da SAP yüksek kullanılabilirlik üç tür için ayrılabilir:

* **Azure altyapı yüksek kullanılabilirliği**: 

    Örneğin, yüksek kullanılabilirlik işlem (VM 'Ler), ağ veya depolama alanı ve SAP uygulamalarının kullanılabilirliğini arttırmaya yönelik avantajları içerebilir.

* **SAP uygulamalarında *daha yüksek kullanılabilirlik* elde etmek IÇIN Azure altyapı VM yeniden başlatması**kullanma: 

    Linux üzerinde Windows Server Yük Devretme Kümelemesi (WSFC) veya pacemaker gibi işlevleri kullanmamaya karar verirseniz, Azure VM yeniden başlatması kullanılır. Azure fiziksel sunucu altyapısının ve temel alınan Azure platformunun planlanmış ve planlanmamış kapalı kalma süresine karşı SAP sistemlerini korur.

* **SAP uygulaması yüksek kullanılabilirliği**: 

    Tam SAP sistem yüksek kullanılabilirlik elde etmek için tüm kritik SAP sistem bileşenlerini korumanız gerekir. Örneğin:
    * Yedekli SAP uygulama sunucuları.
    * Benzersiz bileşenler. Bir örnek, SAP ASCS/SCS örneği veya bir veritabanı yönetim sistemi (DBMS) gibi tek bir hata noktası (SPOF) bileşeni olabilir.

Azure 'da SAP yüksek kullanılabilirlik, şirket içi fiziksel veya sanal ortamda SAP yüksek kullanılabilirliğinden farklıdır. [Microsoft Windows üzerinde VMware ve Hyper-V ' d e sanal ortamlarda yüksek kullanılabilirlik ve iş sürekliliği için aşağıdaki kağıda sahip SAP NetWeaver][sap-ha-bc-virtual-env-hyperv-vmware-white-paper] , Windows üzerinde sanallaştırılmış ortamlarda standart sap yüksek kullanılabilirliğe sahip yapılandırmalara sahiptir.

Windows için olduğu gibi Linux için sapinst ile tümleşik SAP yüksek kullanılabilirlik yapılandırması yoktur. Linux için şirket içi SAP yüksek kullanılabilirlik hakkında daha fazla bilgi için bkz. [yüksek kullanılabilirlik iş ortağı bilgileri][sap-ha-partner-information].

## <a name="azure-infrastructure-high-availability"></a>Azure altyapı yüksek kullanılabilirliği

### <a name="sla-for-single-instance-virtual-machines"></a>Tek örnekli sanal makineler için SLA

Şu anda Premium Depolama ile% 99,9 oranında tek VM SLA 'Sı vardır. Tek bir VM 'nin kullanılabilirliğinin ne olabileceği hakkında fikir almak için, çeşitli kullanılabilir [Azure hizmet düzeyi sözleşmelerinin][azure-sla]ürününü oluşturabilirsiniz.

Hesaplamanın temeli ayda 30 gün veya 43.200 dakikadır. Örneğin,% 0,05 kesinti süresi 21,6 dakikaya karşılık gelir. Her zamanki gibi çeşitli hizmetlerin kullanılabilirliği aşağıdaki şekilde hesaplanır:

(Kullanılabilirlik hizmeti #1/100) * (kullanılabilirlik hizmeti #2/100) * (kullanılabilirlik hizmeti #3/100) \*...

Örneğin:

(99,95/100) * (99,9/100) * (99,9/100) = 0,9975 veya genel olarak% 99,75 kullanılabilirliği.

### <a name="multiple-instances-of-virtual-machines-in-the-same-availability-set"></a>Aynı Kullanılabilirlik kümesindeki birden fazla sanal makine örneği
Aynı *kullanılabilirlik kümesinde*dağıtılan iki veya daha fazla örneğe sahip tüm sanal makinelerde, en az bir örneğe yönelik sanal makine bağlantınızın en az% 99,95 olacağını garanti ediyoruz.

İki veya daha fazla VM aynı Kullanılabilirlik kümesinin parçası olduğunda, kullanılabilirlik kümesindeki her bir sanal makineye, temel alınan Azure platformu tarafından bir *güncelleştirme etki alanı* ve bir *hata etki alanı* atanır.

* **Güncelleştirme etki alanları** , bir Azure altyapısının planlı Bakımı sırasında birden çok VM 'nin aynı anda yeniden başlatılmasının garanti edilir. Tek seferde yalnızca bir VM yeniden başlatılır.

* **Hata etki alanları** , VM 'lerin ortak bir güç kaynağı ve ağ anahtarı paylaşmayan donanım bileşenlerine dağıtılmasını güvence altına alır. Sunucular, ağ anahtarı veya güç kaynağı planlanmamış kapalı kalma süresi olmadığında yalnızca bir VM etkilenir.

Daha fazla bilgi için bkz. [Azure 'Da Windows sanal makinelerinin kullanılabilirliğini yönetme][azure-virtual-machines-manage-availability].

Kullanılabilirlik kümesi, yüksek kullanılabilirlik sağlamak için kullanılır:

* Yedekli SAP uygulama sunucuları.  
* SAP ASCS/SCS örneği veya bir DBMS gibi SPOFs 'ları koruyan iki veya daha fazla düğüm (VM) içeren kümeler.


### <a name="azure-availability-zones"></a>Azure Kullanılabilirlik Alanları
Azure, farklı [Azure bölgelerinin](https://azure.microsoft.com/global-infrastructure/regions/)tamamında [Azure kullanılabilirlik alanları](https://docs.microsoft.com/azure/availability-zones/az-overview) kavramlarını kullanıma sunmaya yönelik bir işlemdir. Kullanılabilirlik Alanları sunulan Azure bölgelerinde, Azure bölgelerinin, güç kaynağı, soğutma ve ağ sağlama ile bağımsız birden çok veri merkezi vardır. Tek bir Azure bölgesi içinde farklı bölgeler sunma nedeni, sunulan iki veya üç Kullanılabilirlik Alanları üzerinde uygulama dağıtmanızı olanaklı hale maktır. Güç kaynaklarında ve/veya ağda yer alan sorunların yalnızca bir kullanılabilirlik alanı altyapısını etkileyeceğini varsayarsak, bir Azure bölgesindeki uygulama dağıtımınız hala tamamen işlevseldir. Bir bölgedeki bazı VM 'Ler kaybedildiğinden, sonunda bazı düşük kapasiteye sahip. Ancak, diğer iki bölgede bulunan VM 'Ler hala çalışır durumda kalır. Bölgeleri sunan Azure bölgeleri [Azure kullanılabilirlik alanları](https://docs.microsoft.com/azure/availability-zones/az-overview)listelenmiştir.

Kullanılabilirlik Alanları kullanarak göz önünde bulundurmanız gereken bazı noktalar vardır. Şöyle dikkat edilecek noktalar listesi:

- Azure kullanılabilirlik kümelerini bir kullanılabilirlik bölgesi içinde dağıtamazsınız. Bir VM için dağıtım çerçevesi olarak bir kullanılabilirlik alanı veya kullanılabilirlik kümesi seçmeniz gerekir.
- Windows Yük devretme kümesi Hizmetleri veya Linux pacemaker tabanlı yük devretme kümesi çözümleri oluşturmak için [temel Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) kullanamazsınız. Bunun yerine [Azure Standart Load Balancer SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones) 'sunu kullanmanız gerekir
- Azure Kullanılabilirlik Alanları, bir bölgedeki farklı bölgeler arasında belirli bir mesafe garantisi vermez
- Farklı Azure bölgelerinde bulunan farklı Azure Kullanılabilirlik Alanları arasındaki ağ gecikmesi Azure bölgesinden bölgeye farklı olabilir. Bir müşteri olarak, bir bölgeden etkin DBMS VM 'sine olan ağ gecikmesi hala bir iş süreci etkisinden kabul edilebilir olduğundan, müşteri olarak, farklı bölgelerde dağıtılan SAP uygulama katmanını makul bir şekilde çalıştırabileceği durumlar olacaktır. Bu durumda, bir bölgedeki etkin DBMS sanal makinesi ile başka bir bölgedeki bir VM 'deki SAP uygulama örneği arasındaki gecikme süresinin çok zorlenebileceği ve SAP iş işlemlerinde kabul edilemez olduğu durumlarda Müşteri senaryoları olacaktır. Sonuç olarak, gecikme çok yüksekse, dağıtım mimarilerinin uygulama veya etkin/Pasif mimari için etkin/etkin bir mimariyle farklı olması gerekir.
- [Azure yönetilen diskleri](https://azure.microsoft.com/services/managed-disks/) kullanmak Azure kullanılabilirlik alanları dağıtmak için zorunludur 


### <a name="planned-and-unplanned-maintenance-of-virtual-machines"></a>Sanal makinelerin planlanmış ve planlanmamış Bakımı

İki tür Azure platform olayı, sanal makinelerinizin kullanılabilirliğini etkileyebilir:

* **Planlı bakım** olayları, Microsoft tarafından temel alınan Azure platformunda yapılan düzenli güncelleştirmelerdir. Güncelleştirmeler, sanal makinelerinizin üzerinde çalıştığı platform altyapısının genel güvenilirlik, performans ve güvenliğini artırır.

* **Planlanmamış bakım** olayları, sanal makinenizi temel alan donanım veya fiziksel altyapı bir şekilde başarısız olduğunda meydana gelir. Yerel ağ arızalarını, yerel disk başarısızlıklarını veya diğer raf düzeyi başarısızlıklarını içerebilir. Böyle bir hata algılandığında, Azure platformu sanal makinenizi sanal makinenizi barındıran sağlıksız fiziksel sunucudan sağlıklı fiziksel bir sunucuya otomatik olarak geçirir. Bu tür olaylar nadir bir durumdur, ancak sanal makinenizin yeniden başlatılmasına de neden olabilir.

Daha fazla bilgi için bkz. [Azure 'Da Windows sanal makinelerinin kullanılabilirliğini yönetme][azure-virtual-machines-manage-availability].

### <a name="azure-storage-redundancy"></a>Azure depolama artıklığı
Depolama hesabınızdaki veriler, dayanıklılık ve yüksek kullanılabilirlik sağlamak için her zaman çoğaltılır ve geçici donanım arızalarının yanında bile Azure Storage SLA 'sını ister.

Azure depolama, varsayılan olarak verilerin üç görüntüsünü sakladığı için, RAID 5 veya RAID 1 ' in birden çok Azure diskinde kullanılması gereksizdir.

Daha fazla bilgi için bkz. [Azure Storage çoğaltma][azure-storage-redundancy].

### <a name="azure-managed-disks"></a>Azure Yönetilen Diskler
Yönetilen diskler, Azure depolama hesaplarında depolanan sanal sabit diskler (VHD) yerine kullanılması önerilen Azure Resource Manager bir kaynak türüdür. Yönetilen diskler, bağlı oldukları sanal makine Azure kullanılabilirlik kümesiyle otomatik olarak hizalanır. Sanal makinenizin ve üzerinde çalışan hizmetlerin kullanılabilirliğini arttırır.

Daha fazla bilgi için bkz. [Azure yönetilen disklere genel bakış][azure-storage-managed-disks-overview].

Sanal makinelerinizin dağıtımını ve yönetimini basitleştiğinden yönetilen diskleri kullanmanızı öneririz.



## <a name="utilizing-azure-infrastructure-high-availability-to-achieve-higher-availability-of-sap-applications"></a>SAP uygulamalarının yüksek *oranda kullanılabilirliğini* elde etmek için Azure altyapı yüksek kullanılabilirliği kullanma

Linux üzerinde WSFC veya Paceyapıcısı gibi işlevleri kullanmamaya karar verirseniz (Şu anda yalnızca SUSE Linux Enterprise Server [SLES] 12 ve üzeri için desteklenmektedir), Azure VM yeniden başlatması kullanılır. Azure fiziksel sunucu altyapısının ve temel alınan Azure platformunun planlanmış ve planlanmamış kapalı kalma süresine karşı SAP sistemlerini korur.

Bu yaklaşım hakkında daha fazla bilgi için bkz. [SAP sisteminin daha yüksek kullanılabilirlik elde etmek Için Azure ALTYAPı VM yeniden başlatma][sap-higher-availability]'yı kullanma.

## <a name="baed0eb3-c662-4405-b114-24c10a62954e"></a>Azure IaaS 'de SAP uygulamalarının yüksek kullanılabilirliği

Tam SAP sistem yüksek kullanılabilirlik elde etmek için tüm kritik SAP sistem bileşenlerini korumanız gerekir. Örneğin:
  * Yedekli SAP uygulama sunucuları.
  * Benzersiz bileşenler. Bir örnek, SAP ASCS/SCS örneği veya bir veritabanı yönetim sistemi (DBMS) gibi tek bir hata noktası (SPOF) bileşeni olabilir.

Sonraki bölümlerde, üç kritik SAP sistem bileşeni için yüksek kullanılabilirlik elde etme konusu ele alınmaktadır.

### <a name="high-availability-architecture-for-sap-application-servers"></a>SAP uygulama sunucuları için yüksek kullanılabilirlik mimarisi

> Bu bölüm için geçerlidir:
>
> ![Windows][Logo_Windows] Windows ve ![Linux][Logo_Linux] Linux
>

SAP uygulama sunucusu ve iletişim örnekleri için genellikle belirli bir yüksek kullanılabilirlik çözümüne ihtiyacınız yoktur. Yedeklilik ile yüksek kullanılabilirlik elde edersiniz ve çeşitli Azure sanal makine örneklerinde birden çok iletişim kutusu örneği yapılandırırsınız. İki Azure sanal makine örneğinde yüklü en az iki SAP uygulaması örneği olmalıdır.

![Şekil 1: Yüksek kullanılabilirliğe sahip SAP uygulama sunucusu][sap-ha-guide-figure-2000]

_**Şekil 1:** Yüksek kullanılabilirliğe sahip SAP uygulama sunucusu_

SAP uygulama sunucusu örneklerini barındıran tüm sanal makineleri aynı Azure kullanılabilirlik kümesine yerleştirmeniz gerekir. Azure kullanılabilirlik kümesi şunları sağlar:

* Tüm sanal makineler aynı güncelleştirme etki alanının bir parçasıdır.  
    Güncelleştirme etki alanı, planlı bakım kapalı kalma süresi boyunca sanal makinelerin aynı anda güncelleştirilmesini sağlar.

    Azure ölçek birimi içindeki farklı güncelleştirme ve hata etki alanları üzerinde oluşturulan temel işlevler, [etki alanları güncelleştirme][planning-guide-3.2.2] bölümünde zaten sunulmuştur.

* Tüm sanal makineler aynı hata etki alanının bir parçasıdır.  
    Bir hata etki alanı, sanal makinelerin dağıtılmasını sağlar, böylece tek bir hata noktası tüm sanal makinelerin kullanılabilirliğini etkiler.

Azure ölçek birimi içindeki bir Azure kullanılabilirlik kümesi tarafından kullanılabilen güncelleştirme ve hata etki alanlarının sayısı sınırlıdır. VM 'Leri tek bir kullanılabilirlik kümesine eklemeye devam ederseniz, iki veya daha fazla VM sonunda aynı hata veya güncelleştirme etki alanında sona bırakılır.

Ayrılmış VM 'lerinde birkaç SAP uygulama sunucusu örneği dağıtırsanız, beş güncelleştirme etki alanı olduğunu varsayarsak, aşağıdaki resim ortaya çıktı. Bir kullanılabilirlik kümesindeki gerçek en fazla güncelleştirme ve hata etki alanı sayısı gelecekte değişebilir:

![Şekil 2: Azure kullanılabilirlik kümesi][planning-guide-figure-3000]
 _'nde SAP uygulama sunucularının yüksek kullanılabilirliği**Şekil 2:** Azure kullanılabilirlik kümesindeki SAP uygulama sunucularının yüksek kullanılabilirliği_

Daha fazla bilgi için bkz. [Azure 'Da Windows sanal makinelerinin kullanılabilirliğini yönetme][azure-virtual-machines-manage-availability].

Daha fazla bilgi için bkz. SAP NetWeaver için Azure sanal makineleri planlama ve uygulama için [Azure kullanılabilirlik kümeleri][planning-guide-3.2.3] bölümü.

**Yalnızca yönetilmeyen diskler:** Azure depolama hesabı olası bir tek hata noktası olduğundan, en az iki sanal makinenin dağıtıldığı en az iki Azure depolama hesabı olması önemlidir. İdeal bir kurulumda, SAP iletişim kutusu örneği çalıştıran her sanal makinenin diskleri farklı bir depolama hesabına dağıtılır.

> [!IMPORTANT]
> SAP yüksek kullanılabilirlik yüklemelerinizde Azure yönetilen diskleri kullanmanızı önemle tavsiye ederiz. Yönetilen diskler, bağlı oldukları sanal makine kullanılabilirlik kümesiyle otomatik olarak hizalandığından, sanal makinenizin ve üzerinde çalışan hizmetlerin kullanılabilirliğini arttırır.  
>

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-windows"></a>Windows üzerinde SAP ASCS/SCS örneği için yüksek kullanılabilirlik mimarisi

> ![Windows][Logo_Windows] Windows
>

SAP ASCS/SCS örneğini korumak için bir WSFC çözümünü kullanabilirsiniz. Çözüm iki çeşitte sahiptir:

* **Kümelenmiş paylaşılan diskler kullanarak SAP ASCS/SCS örneğini kümeleyerek**: Bu mimari hakkında daha fazla bilgi için bkz. [Küme Paylaşılan diski kullanarak bir Windows Yük devretme KÜMESINDE SAP yoks/SCS örneği][sap-high-availability-guide-wsfc-shared-disk]oluşturma.   

* **SAP ASCS/SCS örneğini dosya paylaşımından kullanarak kümeleyerek**: Bu mimari hakkında daha fazla bilgi için, bkz. [dosya paylaşma kullanarak bir Windows Yük devretme KÜMESINDE SAP ASCS/SCS örneği kümesi][sap-high-availability-guide-wsfc-file-share].

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-linux"></a>Linux 'ta SAP ASCS/SCS örneği için yüksek kullanılabilirlik mimarisi

> ![Linux][Logo_Linux] Linux
> 
> SLES küme çerçevesini kullanarak SAP ASCS/SCS örneğini kümeleme hakkında daha fazla bilgi için bkz. [SAP NetWeaver için SUSE Linux Enterprise Server Azure VM 'Lerinde yüksek kullanılabilirlik][sap-suse-ascs-ha]. Yüksek oranda kullanılabilir NFS gerektirmeyen SLES üzerinde alternatif HA mimarisi için, [SAP NetWeaver için Azure NetApp Files ile SuSE Linux Enterprise Server Için yüksek kullanılabilirlik Kılavuzu][sap-suse-ascs-ha-anf]' na bakın.

Red Hat kümesi çerçevesini kullanarak SAP ASCS/SCS örneğini kümeleme hakkında daha fazla bilgi için, bkz. [Azure sanal makineler IÇIN SAP NetWeaver için yüksek kullanılabilirlik Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)


### <a name="sap-netweaver-multi-sid-configuration-for-a-clustered-sap-ascsscs-instance"></a>Kümelenmiş SAP yoks/SCS örneği için SAP NetWeaver çok SID yapılandırması

> ![Windows][Logo_Windows] Windows
> 
> Şu anda, çoklu SID yalnızca WSFC ile desteklenir. Çoklu SID, dosya paylaşma ve paylaşılan disk kullanılarak desteklenir.
> 
> Çoklu SID yüksek kullanılabilirlik mimarisi hakkında daha fazla bilgi için bkz.:

* [SAP ASCS/SCS örneği Windows Server Yük Devretme Kümelemesi ve dosya paylaşma için çok SID yüksek kullanılabilirlik][sap-ascs-ha-multi-sid-wsfc-file-share]

* [SAP ASCS/SCS örneği Windows Server Yük Devretme Kümelemesi ve paylaşılan disk için çok SID yüksek kullanılabilirlik][sap-ascs-ha-multi-sid-wsfc-shared-disk]

### <a name="high-availability-dbms-instance"></a>Yüksek kullanılabilirlik DBMS örneği

DBMS Ayrıca bir SAP sisteminde tek bir iletişim noktasıdır. Yüksek kullanılabilirlik çözümü kullanarak korumanız gerekir. Aşağıdaki şekilde, Windows Server Yük Devretme Kümelemesi ve Azure iç yük dengeleyici ile Azure 'da SQL Server AlwaysOn yüksek kullanılabilirlik çözümü gösterilmektedir. SQL Server AlwaysOn, DBMS verilerini ve günlük dosyalarını kendi DBMS çoğaltmasını kullanarak çoğaltır. Bu durumda, küme paylaşılan diskine ihtiyacınız yoktur, bu da kurulumun tamamını basitleştirir.

![Şekil 3: SQL Server AlwaysOn ile yüksek kullanılabilirliğe sahip SAP DBMS örneği][sap-ha-guide-figure-2003]

_**Şekil 3:** SQL Server AlwaysOn ile yüksek kullanılabilirliğe sahip SAP DBMS örneği_

Azure Resource Manager dağıtım modelini kullanarak Azure 'da kümeleme SQL Server DBMS hakkında daha fazla bilgi için şu makalelere bakın:

* [Kaynak Yöneticisi kullanarak Azure sanal makinelerinde el ile bir AlwaysOn kullanılabilirlik grubu yapılandırın][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]

* [Azure 'da bir AlwaysOn kullanılabilirlik grubu için Azure iç yük dengeleyici yapılandırma][virtual-machines-windows-portal-sql-alwayson-int-listener]

Azure Resource Manager dağıtım modelini kullanarak Azure 'da kümeleme SAP HANA DBMS hakkında daha fazla bilgi için bkz. [Azure sanal makinelerinde (VM) SAP HANA yüksek kullanılabilirliği][sap-hana-ha].
