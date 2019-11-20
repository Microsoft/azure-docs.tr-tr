---
title: SAP ASCS/SCS örneği Windows Server Yük Devretme Kümelemesi ve Azure 'da paylaşılan disk ile çok düzeyli yüksek kullanılabilirlik | Microsoft Docs
description: Azure 'da Windows Server Yük Devretme Kümelemesi ve paylaşılan disk ile SAP ASCS/SCS örneği için çoklu SID yüksek kullanılabilirlik
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: cbf18abe-41cb-44f7-bdec-966f32c89325
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fada16b3ca5307a28eebca4dfe97dc96ba389212
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098688"
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1869038]:https://launchpad.support.sap.com/#/notes/1869038
[2287140]:https://launchpad.support.sap.com/#/notes/2287140
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[sap-installation-guides]:http://service.sap.com/instguides

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md
[networking-limits-azure-resource-manager]:../../../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits
[load-balancer-multivip-overview]:../../../load-balancer/load-balancer-multivip-overview.md


[sap-net-weaver-ports]:https://help.sap.com/viewer/ports
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md
[sap-net-weaver-ports-ascs-scs-ports]:sap-high-availability-infrastructure-wsfc-shared-disk.md#0f3ee255-b31e-4b8a-a95a-d9ed6200468b

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md

[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk-install-ascs]:sap-high-availability-installation-wsfc-shared-disk.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-high-availability-installation-wsfc-shared-disk-modify-ascs-profile]:sap-high-availability-installation-wsfc-shared-disk.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556
[sap-high-availability-installation-wsfc-shared-disk-add-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761
[sap-high-availability-installation-wsfc-shared-disk-win-firewall-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#4498c707-86c0-4cde-9c69-058a7ab8c3ac
[sap-high-availability-installation-wsfc-shared-disk-change-ers-service-startup-type]:sap-high-availability-installation-wsfc-shared-disk.md#094bc895-31d4-4471-91cc-1513b64e406a
[sap-high-availability-installation-wsfc-shared-disk-test-ascs-failover-and-sios-repl]:sap-high-availability-installation-wsfc-shared-disk.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9


[sap-high-availability-installation-wsfc-file-share]:sap-high-availability-installation-wsfc-file-share.md
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

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

[sap-ha-guide-figure-6001]:media/virtual-machines-shared-sap-high-availability-guide/6001-sap-multi-sid-ascs-scs-sid1.png
[sap-ha-guide-figure-6002]:media/virtual-machines-shared-sap-high-availability-guide/6002-sap-multi-sid-ascs-scs.png
[sap-ha-guide-figure-6003]:media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png
[sap-ha-guide-figure-6004]:media/virtual-machines-shared-sap-high-availability-guide/6004-sap-multi-sid-dns.png
[sap-ha-guide-figure-6005]:media/virtual-machines-shared-sap-high-availability-guide/6005-sap-multi-sid-azure-portal.png
[sap-ha-guide-figure-6006]:media/virtual-machines-shared-sap-high-availability-guide/6006-sap-multi-sid-sios-replication.png



[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png


[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md

# <a name="sap-ascsscs-instance-multi-sid-high-availability-with-windows-server-failover-clustering-and-shared-disk-on-azure"></a>SAP ASCS/SCS örneği Windows Server Yük Devretme Kümelemesi ve paylaşılan disk ile Azure üzerinde çok düzeyli yüksek kullanılabilirlik

> ![Windows][Logo_Windows] Windows
>

Microsoft, Eylül 2016 ' de bir [Azure iç yük dengeleyici][load-balancer-multivip-overview]kullanarak birden çok sanal IP adresini yönetebileceğiniz bir özellik yayımladı. Bu işlevsellik Azure dış yük dengeleyicisinde zaten var. 

SAP dağıtımınız varsa, SAP Merkezi Hizmetleri (yoks/SCS) örnekleri için bir Windows küme yapılandırması oluşturmak üzere iç yük dengeleyici kullanmanız gerekir.

Bu makalede, paylaşılan disk ile var olan bir Windows Server Yük Devretme Kümelemesi (WSFC) kümesine ek SAP ASCS/SCS kümelenmiş örnekleri yükleyerek tek bir ASCS/SCS yüklemesinden SAP çoklu SID yapılandırmasına nasıl geçiş yapılacağı ele alınmaktadır. Bu işlem tamamlandığında, SAP çoklu SID kümesi yapılandırdınız.

> [!NOTE]
> Bu özellik yalnızca Azure Resource Manager dağıtım modelinde kullanılabilir.
>
>Her bir Azure iç yük dengeleyici için özel ön uç IP sayısı sınırı vardır.
>
>Bir WSFC kümesindeki en fazla SAP ASCS/SCS örneği sayısı, her bir Azure iç yük dengeleyici için en fazla özel ön uç IP sayısına eşittir.
>

Yük dengeleyici sınırları hakkında daha fazla bilgi için ağ sınırları içindeki ["yük dengeleyici başına özel ön uç IP" bölümüne bakın: Azure Resource Manager][networking-limits-azure-resource-manager].

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu diyagramda gösterildiği gibi, **dosya paylaşma**kullanarak BIR SAP ascs/SCS örneği için kullanılacak bir wsfc kümesini zaten yapılandırdınız.

![Yüksek kullanılabilirliğe sahip SAP yoks/SCS örneği][sap-ha-guide-figure-6001]

> [!IMPORTANT]
> Kurulumun aşağıdaki koşullara uyması gerekir:
> * SAP ASCS/SCS örnekleri aynı WSFC kümesini paylaşmalıdır.
> * Her veritabanı yönetim sistemi (DBMS) SID 'sinin kendi adanmış WSFC kümesi olmalıdır.
> * Bir SAP sistem SID 'sine ait SAP uygulama sunucularının kendi ayrılmış VM 'lerine sahip olması gerekir.

## <a name="sap-ascsscs-multi-sid-architecture-with-shared-disk"></a>Paylaşılan disk ile SAP ASCS/SCS çoklu SID mimarisi

Amaç, burada gösterildiği gibi birden çok SAP ABAP yoks veya SAP Java SCS kümelenmiş örneğini aynı WSFC kümesine yüklemektir:

![Azure 'da birden çok SAP ASCS/SCS kümelenmiş örneği][sap-ha-guide-figure-6002]

Yük dengeleyici sınırları hakkında daha fazla bilgi için ağ sınırları içindeki ["yük dengeleyici başına özel ön uç IP" bölümüne bakın: Azure Resource Manager][networking-limits-azure-resource-manager].

İki yüksek kullanılabilirliğe sahip SAP sistemiyle tam yatay şöyle görünür:

![İki SAP sistem SID ile SAP yüksek kullanılabilirliğe sahip çok düzeyli kurulum][sap-ha-guide-figure-6003]

## <a name="25e358f8-92e5-4e8d-a1e5-df7580a39cb0"></a>Altyapıyı SAP çoklu SID senaryosu için hazırlama

Altyapınızı hazırlamak için aşağıdaki parametrelerle ek bir SAP ASCS/SCS örneği yükleyebilirsiniz:

| Parametre adı | Value |
| --- | --- |
| SAP YOKS/SCS SID 'SI |PR1-lb-yoks |
| SAP DBMS iç yük dengeleyici | PR5 |
| SAP sanal ana bilgisayar adı | PR5-SAP-CL |
| SAP ASCS/SCS sanal ana bilgisayar IP adresi (ek Azure yük dengeleyici IP adresi) | 10.0.0.50 |
| SAP ASCS/SCS örnek numarası | 50 |
| Ek SAP yoks/SCS örneği için ıLB araştırma bağlantı noktası | 62350 |

> [!NOTE]
> SAP ASCS/SCS küme örnekleri için her IP adresi için benzersiz bir yoklama bağlantı noktası gerekir. Örneğin, bir Azure iç yük dengeleyicideki bir IP adresi araştırma bağlantı noktası 62300 kullanıyorsa, bu yük dengeleyicide başka bir IP adresi araştırma bağlantı noktası 62300 ' i kullanabilir.
>
>Araştırma bağlantı noktası 62300 zaten ayrıldığından, bizim için araştırma bağlantı noktası 62350 ' i kullanıyoruz.

Mevcut WSFC kümesine iki düğüm ile ek SAP yoks/SCS örneği yükleyebilirsiniz:

| Sanal makine rolü | Sanal makine konak adı | Statik IP adresi |
| --- | --- | --- |
| ASCS/SCS örneği için ilk küme düğümü |PR1-ascs-0 |10.0.0.10 |
| ASCS/SCS örneği için ikinci küme düğümü |PR1-ascs-1 |10.0.0.9 |

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance-on-the-dns-server"></a>DNS sunucusunda kümelenmiş SAP Ass/SCS örneği için bir sanal konak adı oluşturun

Aşağıdaki parametreleri kullanarak, ASCS/SCS örneğinin sanal ana bilgisayar adı için bir DNS girişi oluşturabilirsiniz:

| Yeni SAP yoks/SCS sanal ana bilgisayar adı | İlişkili IP adresi |
| --- | --- |
|PR5-SAP-CL |10.0.0.50 |

Yeni ana bilgisayar adı ve IP adresi, aşağıdaki ekran görüntüsünde gösterildiği gibi DNS Yöneticisi 'nde görüntülenir:

![Yeni SAP yoks/SCS kümesi sanal adı ve TCP/IP adresi için tanımlı DNS girişini vurgulayan DNS Yöneticisi listesi][sap-ha-guide-figure-6004]

> [!NOTE]
> Ek ASCS/SCS örneğinin sanal ana bilgisayar adına atadığınız yeni IP adresi, SAP Azure yük dengeleyicisine atadığınız yeni IP adresiyle aynı olmalıdır.
>
>Senaryomızda, IP adresi 10.0.0.50 ' dir.

### <a name="add-an-ip-address-to-an-existing-azure-internal-load-balancer-by-using-powershell"></a>PowerShell kullanarak var olan bir Azure iç yük dengeleyicisine bir IP adresi ekleme

Aynı WSFC kümesinde birden fazla SAP ASCS/SCS örneği oluşturmak için, PowerShell kullanarak var olan bir Azure iç yük dengeleyicisine bir IP adresi ekleyin. Her IP adresi kendi yük dengeleme kurallarını, araştırma bağlantı noktasını, ön uç IP havuzunu ve arka uç havuzunu gerektirir.

Aşağıdaki betik var olan bir yük dengeleyiciye yeni bir IP adresi ekler. Ortamınız için PowerShell değişkenlerini güncelleştirin. Betik tüm SAP yoks/SCS bağlantı noktaları için gereken tüm yük dengeleme kurallarını oluşturur.

```powershell

# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>
Clear-Host
$ResourceGroupName = "SAP-MULTI-SID-Landscape"      # Existing resource group name
$VNetName = "pr2-vnet"                        # Existing virtual network name
$SubnetName = "Subnet"                        # Existing subnet name
$ILBName = "pr2-lb-ascs"                      # Existing ILB name                      
$ILBIP = "10.0.0.50"                          # New IP address
$VMNames = "pr2-ascs-0","pr2-ascs-1"          # Existing cluster virtual machine names
$SAPInstanceNumber = 50                       # SAP ASCS/SCS instance number: must be a unique value for each cluster
[int]$ProbePort = "623$SAPInstanceNumber"     # Probe port: must be a unique value for each IP and load balancer

$ILB = Get-AzLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName

$count = $ILB.FrontendIpConfigurations.Count + 1
$FrontEndConfigurationName ="lbFrontendASCS$count"
$LBProbeName = "lbProbeASCS$count"

# Get the Azure virtual network and subnet
$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName
$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

# Add a second front-end and probe configuration
Write-Host "Adding new front end IP Pool '$FrontEndConfigurationName' ..." -ForegroundColor Green
$ILB | Add-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id
$ILB | Add-AzLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 10  | Set-AzLoadBalancer

# Get a new updated configuration
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

# Get an updated LP FrontendIpConfig
$FEConfig = Get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

# Add a back-end configuration into an existing ILB
$BackEndConfigurationName  = "backendPoolASCS$count"
Write-Host "Adding new backend Pool '$BackEndConfigurationName' ..." -ForegroundColor Green
$BEConfig = Add-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB | Set-AzLoadBalancer

# Get an updated config
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

# Assign VM NICs to the back-end pool
$BEPool = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
foreach($VMName in $VMNames){
        $VM = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $VMName
        $NICName = ($VM.NetworkInterfaceIDs[0].Split('/') | select -last 1)        
        $NIC = Get-AzNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName                
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools += $BEPool
        Write-Host "Assigning network card '$NICName' of the '$VMName' VM to the backend pool '$BackEndConfigurationName' ..." -ForegroundColor Green
        Set-AzNetworkInterface -NetworkInterface $NIC
        #start-AzVM -ResourceGroupName $ResourceGroupName -Name $VM.Name
}


# Create load-balancing rules
$Ports = "445","32$SAPInstanceNumber","33$SAPInstanceNumber","36$SAPInstanceNumber","39$SAPInstanceNumber","5985","81$SAPInstanceNumber","5$SAPInstanceNumber`13","5$SAPInstanceNumber`14","5$SAPInstanceNumber`16"
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
$FEConfig = get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$BEConfig = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

Write-Host "Creating load balancing rules for the ports: '$Ports' ... " -ForegroundColor Green

foreach ($Port in $Ports) {

        $LBConfigrulename = "lbrule$Port" + "_$count"
        Write-Host "Creating load balancing rule '$LBConfigrulename' for the port '$Port' ..." -ForegroundColor Green

        $ILB | Add-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $HealthProbe -Protocol tcp -FrontendPort  $Port -BackendPort $Port -IdleTimeoutInMinutes 30 -LoadDistribution Default -EnableFloatingIP   
}

$ILB | Set-AzLoadBalancer

Write-Host "Successfully added new IP '$ILBIP' to the internal load balancer '$ILBName'!" -ForegroundColor Green

```
Komut dosyası çalıştırıldıktan sonra, aşağıdaki ekran görüntüsünde gösterildiği gibi sonuçlar Azure portal görüntülenir:

![Azure portal yeni ön uç IP Havuzu][sap-ha-guide-figure-6005]

### <a name="add-disks-to-cluster-machines-and-configure-the-sios-cluster-share-disk"></a>Küme makinelerine disk ekleme ve SIOS küme paylaşma diskini yapılandırma

Her ek SAP ASCS/SCS örneği için yeni bir küme paylaşma diski eklemeniz gerekir. Windows Server 2012 R2 için şu anda kullanılmakta olan WSFC küme paylaşma diski, SIOS Verilerman yazılım çözümüdür.

Şunları yapın:
1. Küme düğümlerinin her birine aynı boyuttaki (Stripe için gereklidir) ek bir disk veya disk ekleyin ve bunları biçimlendirin.
2. SIOS Dataman ile depolama çoğaltmasını yapılandırın.

Bu yordamda, WSFC küme makinelerine zaten SIOS Dataman yüklediğinizi varsayılmaktadır. Uygulamayı yüklediyseniz, artık makineler arasında çoğaltmayı yapılandırmanız gerekir. İşlem [SAP ASCS/SCS küme paylaşma diski IÇIN SIOS Dataman küme sürümü][sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]' nde ayrıntılı olarak açıklanır.  

![Yeni SAP Ass/SCS paylaşma diski için Dataman zaman uyumlu yansıtma][sap-ha-guide-figure-6006]

### <a name="deploy-vms-for-sap-application-servers-and-the-dbms-cluster"></a>SAP uygulama sunucuları ve DBMS kümesi için VM 'Leri dağıtma

İkinci SAP sistemine yönelik altyapı hazırlığını gerçekleştirmek için aşağıdakileri yapın:

1. SAP uygulama sunucuları için adanmış VM 'Ler dağıtın ve her birinin kendi adanmış kullanılabilirlik grubuna yerleştirin.
2. DBMS kümesi için adanmış VM 'Ler dağıtın ve her bir kendisini kendi adanmış kullanılabilirlik grubuna yerleştirin.

## <a name="install-an-sap-netweaver-multi-sid-system"></a>SAP NetWeaver çoklu SID sistemi yüklemesi

İkinci bir SAP SID2 sistemi yükleme işleminin bir açıklaması için, bkz. sap [NETWEAVER ha yüklemesi Windows Yük devretme kümesi ve paylaşılan disk, SAP ASCS/SCS örneği için][sap-high-availability-installation-wsfc-shared-disk].

Üst düzey yordam aşağıdaki gibidir:

1. [Yüksek kullanılabilirliğe sahip BIR ASCS/SCS ÖRNEĞIYLE SAP 'Yi yükler][sap-high-availability-installation-wsfc-shared-disk-install-ascs].  
 Bu adımda, mevcut WSFC küme düğümü 1 üzerinde yüksek kullanılabilirliğe sahip bir ASCS/SCS örneğiyle SAP 'yi yüklüyorsunuz.

2. [ASCS/SCS ÖRNEĞININ SAP profilini değiştirin][sap-high-availability-installation-wsfc-shared-disk-modify-ascs-profile].

3. [Bir araştırma bağlantı noktası yapılandırın][sap-high-availability-installation-wsfc-shared-disk-add-probe-port].  
 Bu adımda, PowerShell kullanarak SAP küme kaynağı SAP-SID2-IP araştırma bağlantı noktasını yapılandırıyorsunuz. Bu yapılandırmayı SAP ASCS/SCS küme düğümlerinden birinde yürütün.

4. Veritabanı örneğini yükler.  
 İkinci kümeyi yüklemek için SAP yükleme kılavuzundaki adımları izleyin.

5. İkinci küme düğümünü yükler.  
 Bu adımda, mevcut WSFC küme düğümü 2 ' de yüksek kullanılabilirliğe sahip bir ASCS/SCS örneği ile SAP 'yi yüklüyorsunuz. İkinci kümeyi yüklemek için SAP yükleme kılavuzundaki adımları izleyin.

6. SAP ASCS/SCS örneği ve yoklama bağlantı noktası için Windows Güvenlik Duvarı bağlantı noktalarını açın.  
    SAP ASCS/SCS örnekleri için kullanılan her iki küme düğümünde de SAP Ass/SCS tarafından kullanılan tüm Windows Güvenlik Duvarı bağlantı noktalarını açıyor olursunuz. Bu SAP yoks/SCS örnek bağlantı noktaları, bölüm [SAP yoks/SCS bağlantı][sap-net-weaver-ports-ascs-scs-ports]noktalarında listelenmiştir.

    Diğer tüm SAP bağlantı noktalarının listesi için bkz. [Tüm sap ürünlerinin TCP/IP bağlantı noktaları][sap-net-weaver-ports].  

    Ayrıca, Senaryomuzda 62350 olan Azure iç yük dengeleyici araştırma bağlantı noktasını açın. [Bu makalede][sap-high-availability-installation-wsfc-shared-disk-win-firewall-probe-port]açıklanmaktadır.

7. [SAP tarafından değerlendirilen giriş kapatması (ERS) Windows hizmeti örneğinin başlangıç türünü değiştirin][sap-high-availability-installation-wsfc-shared-disk-change-ers-service-startup-type].

8. SAP birincil uygulama sunucusunu, SAP yükleme kılavuzunda açıklandığı şekilde, yeni adanmış VM 'ye yükler.  

9. SAP yükleme kılavuzunda açıklandığı şekilde SAP ek uygulama sunucusunu yeni adanmış VM 'ye yükleme.

10. [SAP ASCS/SCS örneği yük devretme ve SIOS çoğaltmasını test][sap-high-availability-installation-wsfc-shared-disk-test-ascs-failover-and-sios-repl]edin.

## <a name="next-steps"></a>Sonraki adımlar

- [Ağ sınırları: Azure Resource Manager][networking-limits-azure-resource-manager]
- [Azure Load Balancer için birden çok VIP][load-balancer-multivip-overview]
