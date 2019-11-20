---
title: Azure 'da bir dosya paylaşma kullanarak bir Windows Yük devretme kümesinde SAP ASCS/SCS örneği oluşturma | Microsoft Docs
description: Azure 'da bir dosya paylaşma kullanarak bir Windows Yük devretme kümesinde SAP ASCS/SCS örneğini nasıl kümeleyeceğinizi öğrenin.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/24/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8db3baf9fa4c0d054e743d0b52964847b37ec281
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70078288"
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[kb4025334]:https://support.microsoft.com/help/4025334/windows-10-update-kb4025334

[dv2-series]:https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dv2-series
[ds-series]:https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general

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
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-volumes-s2d-choosing-filesystem]:https://docs.microsoft.com/windows-server/storage/storage-spaces/plan-volumes#choosing-the-filesystem
[choosing-the-size-of-volumes-s2d]:https://docs.microsoft.com/windows-server/storage/storage-spaces/plan-volumes#choosing-the-size-of-volumes
[deploy-sofs-s2d-in-azure]:https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment
[s2d-in-win-2016]:https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview
[deep-dive-volumes-in-s2d]:https://blogs.technet.microsoft.com/filecab/2016/08/29/deep-dive-volumes-in-spaces-direct/

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

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

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png


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

[1869038]:https://launchpad.support.sap.com/#/notes/1869038 

# <a name="cluster-an-sap-ascsscs-instance-on-a-windows-failover-cluster-by-using-a-file-share-in-azure"></a>Azure 'da bir dosya paylaşma kullanarak bir Windows Yük devretme kümesinde SAP yoks/SCS örneği oluşturma

> ![Windows][Logo_Windows] Windows
>

Windows Server Yük Devretme Kümelemesi, Windows 'da yüksek kullanılabilirliğe sahip SAP Ass/SCS yüklemesi ve DBMS 'nin temelidir.

Yük devretme kümesi, uygulamaların ve hizmetlerin kullanılabilirliğini artırmak için birlikte çalışan 1 + n bağımsız sunucu (düğüm) grubudur. Bir düğüm hatası oluşursa, Windows Server Yük Devretme Kümelemesi oluşabilecek hata sayısını hesaplar ve uygulamalar ve hizmetler sağlamak için sağlıklı bir kümeyi sürdürür. Yük Devretme Kümelemesi elde etmek için farklı çekirdek modlarında seçim yapabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar
Bu makalede açıklanan görevlere başlamadan önce şu makaleyi gözden geçirin:

* [SAP NetWeaver için Azure sanal makineler yüksek kullanılabilirliğe sahip mimari ve senaryolar][sap-high-availability-architecture-scenarios]

> [!IMPORTANT]
> SAP ASCS/SCS örneklerinin bir dosya paylaşımının kullanılarak kümelenmesi, SAP çekirdek 7,49 (ve üzeri) ile SAP NetWeaver 7,40 (ve üzeri) için desteklenir.
>


## <a name="windows-server-failover-clustering-in-azure"></a>Azure 'da Windows Server Yük Devretme Kümelemesi

Çıplak veya özel bulut dağıtımlarıyla karşılaştırıldığında, Azure sanal makineleri, Windows Server Yük Devretme Kümelemesi 'ni yapılandırmak için ek adımlar gerektirir. Bir küme oluşturduğunuzda, SAP ASCS/SCS örneği için birkaç IP adresi ve sanal konak adı ayarlamanız gerekir.

### <a name="name-resolution-in-azure-and-the-cluster-virtual-host-name"></a>Azure 'da ad çözümlemesi ve küme sanal ana bilgisayar adı

Azure bulut platformu, kayan IP adresleri gibi sanal IP adreslerini yapılandırma seçeneği sunmaz. Bulutta küme kaynağına ulaşmak üzere bir sanal IP adresi ayarlamak için alternatif bir çözüme ihtiyacınız vardır. 

Azure Load Balancer hizmeti, Azure için bir *iç yük dengeleyici* sağlar. İç yük dengeleyici ile istemciler küme sanal IP adresi üzerinden kümeye ulaşabilirler. 

İç yük dengeleyiciyi küme düğümlerini içeren kaynak grubuna dağıtın. Ardından, iç yük dengeleyicinin araştırma bağlantı noktalarını kullanarak tüm gerekli bağlantı noktası iletme kurallarını yapılandırın. İstemciler sanal ana bilgisayar adı aracılığıyla bağlanabilir. DNS sunucusu, küme IP adresini çözer. İç yük dengeleyici, kümenin etkin düğümüne bağlantı noktası iletmeyi işler.

![Şekil 1: Azure 'da paylaşılan disk olmadan Windows Server Yük Devretme Kümelemesi yapılandırması][sap-ha-guide-figure-1001]

_**Şekil 1:** Azure 'da paylaşılan disk olmadan Windows Server Yük Devretme Kümelemesi yapılandırması_

## <a name="sap-ascsscs-ha-with-file-share"></a>Dosya paylaşımıyla SAP yoks/SCS HA

SAP, bir Windows Yük devretme kümesindeki SAP ASCS/SCS örneğini Kümelendirmek için yeni bir yaklaşım ve küme paylaşılan disklerine bir alternatifi geliştirmiştir. Küme paylaşılan disklerini kullanmak yerine, SAP Küresel Ana bilgisayar dosyalarını dağıtmak için bir SMB dosya paylaşımından yararlanabilirsiniz.

> [!NOTE]
> SMB dosya paylaşma, SAP Ass/SCS örneklerinin kümelenmesi için Küme Paylaşılan diskleri kullanmanın bir alternatifidir.  
>

Bu mimari aşağıdaki yollarla özeldir:

* SAP Merkezi Hizmetler (kendi dosya yapısı ve ileti ve sıraya alma işlemleriyle birlikte) SAP Küresel Ana bilgisayar dosyalarından ayrıdır.
* SAP yönetim hizmetleri SAP ASCS/SCS örneği altında çalışır.
* SAP ascs/SCS örneği kümelenir ve \<yoks/SCS sanal ana bilgisayar adı\> sanal ana bilgisayar adı kullanılarak erişilebilir.
* SAP genel dosyaları, SMB dosya paylaşımında yerleştirilir ve \<SAP Küresel Ana\> bilgisayar ana bilgisayar adı kullanılarak erişilir: \\\\&lt;SAP Küresel Ana&gt;bilgisayar \ sapmnt&gt;\\&lt;SID \sys\...
* SAP ASCS/SCS örneği her iki küme düğümünde yerel bir diske yüklenir.
* Ascs/SCS sanal konak adı\> &lt;ağ adı SAP Küresel Ana&gt;bilgisayardan farklı. \<

![Şekil 2: SMB dosya paylaşımıyla SAP ASCS/SCS HA mimarisi][sap-ha-guide-figure-8004]

_**Şekil 2:** Bir SMB dosya paylaşımıyla yeni SAP ASCS/SCS HA mimarisi_

SMB dosya paylaşımının önkoşulları:

* SMB 3,0 (veya üzeri) protokolü.
* Active Directory Kullanıcı grupları ve `computer$` bilgisayar nesnesi için Active Directory erişim denetim listeleri (ACL 'ler) ayarlama yeteneği.
* Dosya paylaşımının HA etkin olması gerekir:
    * Dosyaları depolamak için kullanılan disklerin tek bir hata noktası olmaması gerekir.
    * Sunucu veya VM kapalı kalma süresi dosya paylaşımında kesinti oluşmasına neden olmaz.

SAP \<SID\> kümesi rolü Küme Paylaşılan diskleri veya bir genel dosya paylaşım kümesi kaynağı içermez.


![Şekil 3: Dosya \<paylaşımının\> kullanımı için SAP SID kümesi rol kaynakları][sap-ha-guide-figure-8005]

_**Şekil 3:** Dosya &lt;paylaşımının&gt; kullanımı için SAP SID kümesi rol kaynakları_


## <a name="scale-out-file-shares-with-storage-spaces-direct-in-azure-as-an-sapmnt-file-share"></a>Azure 'da bir SAPMNT dosya paylaşımı olarak Depolama Alanları Doğrudan genişleme dosya paylaşımları

SAP Küresel Ana bilgisayar dosyalarını barındırmak ve korumak için bir genişleme dosya paylaşımından yararlanabilirsiniz. Genişleme dosya paylaşma, yüksek oranda kullanılabilir bir SAPMNT dosya paylaşma hizmeti de sunar.

![Şekil 4: SAP Küresel Ana bilgisayar dosyalarını korumak için kullanılan genişleme dosya paylaşma][sap-ha-guide-figure-8006]

_**Şekil 4:** SAP Küresel Ana bilgisayar dosyalarını korumak için kullanılan bir genişleme dosya paylaşma_

> [!IMPORTANT]
> Genişleme dosya paylaşımları, Microsoft Azure bulutta ve şirket içi ortamlarda tam olarak desteklenmektedir.
>

Genişleme dosya paylaşımında yüksek düzeyde kullanılabilir ve yatay olarak ölçeklenebilir SAPMNT dosya paylaşma sunulmaktadır.

Depolama Alanları Doğrudan, genişleme dosya paylaşımında paylaşılan disk olarak kullanılır. Yerel depolama ile sunucuları kullanarak yüksek oranda kullanılabilir ve ölçeklenebilir depolama oluşturmak için Depolama Alanları Doğrudan kullanabilirsiniz. SAP Küresel Ana bilgisayar dosyaları gibi bir genişleme dosya paylaşımında kullanılan paylaşılan depolama alanı, tek bir başarısızlık noktası değildir.

Depolama Alanları Doğrudan seçerken şu kullanım örneklerini göz önünde bulundurun:

- Depolama Alanları Doğrudan kümesini oluşturmak için kullanılan sanal makinelerin bir Azure kullanılabilirlik kümesinde dağıtılması gerekir.
- Depolama Alanları Doğrudan kümesinin olağanüstü durum kurtarması için [Azure Site Recovery Hizmetleri](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#replicated-machines---storage)' ni kullanabilirsiniz.
- Farklı Azure Kullanılabilirlik Alanları depolama alanı doğrudan kümesini uzatmak desteklenmez.

### <a name="sap-prerequisites-for-scale-out-file-shares-in-azure"></a>Azure 'da genişleme dosya paylaşımları için SAP önkoşulları

Genişleme dosya paylaşımının kullanılması için sisteminizin aşağıdaki gereksinimleri karşılaması gerekir:

* Genişleme dosya paylaşımının en az iki küme düğümü.
* Her düğüm için en az iki yerel disk olmalıdır.
* Performans nedeniyle, *yansıtma dayanıklılığı*kullanmanız gerekir:
    * İki küme düğümü olan bir genişleme dosya paylaşımında iki yönlü yansıtma.
    * Üç (veya daha fazla) küme düğümüne sahip bir genişleme dosya paylaşımında üç yönlü yansıtma.
* Üç yönlü yansıtmayla genişleme dosya paylaşımında üç (veya daha fazla) küme düğümü öneririz.
    Bu kurulum, iki küme düğümü ve iki yönlü yansıtma ile genişleme dosya paylaşma kurulumundan daha fazla ölçeklenebilirlik ve daha fazla depolama esnekliği sunar.
* Azure Premium disklerini kullanmanız gerekir.
* Azure yönetilen diskleri kullanmanızı öneririz.
* Birimleri dayanıklı dosya sistemi (ReFS) kullanarak biçimlendirmeniz önerilir.
    * Daha fazla bilgi için bkz. [SAP Note 1869038-ReFs dosya sistemi IÇIN sap desteği][1869038] ve depolama alanları doğrudan 'de planlama birimlerinin bulunduğu makalenin [dosya sistemini seçme][planning-volumes-s2d-choosing-filesystem] bölümü.
    * [MICROSOFT KB4025334 toplu güncelleştirme][kb4025334]'yi yüklediğinizden emin olun.
* DS serisi veya DSv2 serisi Azure VM boyutlarını kullanabilirsiniz.
* Depolama Alanları Doğrudan disk eşitleme için gerekli olan VM 'Ler arasında iyi ağ performansı için, en az "yüksek" ağ bant genişliğine sahip bir VM türü kullanın.
    Daha fazla bilgi için bkz. [DSv2-Series][dv2-series] ve [DS serisi][ds-series] belirtimleri.
* Depolama havuzunda ayrılmamış kapasiteyi ayırmanızı öneririz. Depolama havuzundaki ayrılmamış kapasiteden ayrıldığınızda, bir sürücü başarısız olursa "yerinde" onarım için birim alanı verilir. Bu, veri güvenliğini ve performansını geliştirir.  Daha fazla bilgi için bkz. [birim boyutunu seçme][choosing-the-size-of-volumes-s2d].
* Azure iç yük dengeleyiciyi, \<SAP Küresel Ana bilgisayar\>gibi genişleme dosya paylaşımının ağ adı için yapılandırmanız gerekmez. Bu, SAP ascs \</SCS örneğinin veya DBMS 'nin yoks/SCS sanal ana bilgisayar adı\> için yapılır. Genişleme dosya paylaşma, tüm küme düğümlerinde yükü ölçeklendirir. \<SAP Küresel Ana\> bilgisayar tüm küme düğümleri için yerel IP adresini kullanır.


> [!IMPORTANT]
> \<SAP Küresel ana bilgisayarına\>işaret eden sapmnt dosya paylaşımının adını değiştiremezsiniz. SAP yalnızca "sapmnt" adlı paylaşımın adını destekler.
>
> Daha fazla bilgi için bkz. [SAP Note 2492395-sapmnt paylaşma adı değişebilir mi?][2492395]

### <a name="configure-sap-ascsscs-instances-and-a-scale-out-file-share-in-two-clusters"></a>SAP ASCS/SCS örnekleri ve iki kümede genişleme dosya paylaşma yapılandırma

SAP ascs/SCS örneklerini tek bir kümede, kendi sap \<SID\> kümesi rolüyle dağıtabilirsiniz. Bu durumda, farklı bir küme rolüyle genişleme dosya paylaşımından başka bir kümede yapılandırırsınız.

> [!IMPORTANT]
>Bu senaryoda SAP ascs/SCS örneği, SAP Küresel ana bilgisayarına UNC yolu \\ \\ &lt;ile erişim için yapılandırılmış SAP Küresel Ana&gt;bilgisayar\\\sapmnt&lt;SID&gt;\sys\.
>

![Şekil 5: SAP ASCS/SCS örneği ve iki kümede dağıtılan bir genişleme dosya paylaşma][sap-ha-guide-figure-8007]

_**Şekil 5:** İki kümede dağıtılan bir SAP ASCS/SCS örneği ve genişleme dosya paylaşma_

> [!IMPORTANT]
> Azure bulutunda, SAP ve genişleme dosya paylaşımları için kullanılan her kümenin kendi Azure kullanılabilirlik kümesinde veya Azure Kullanılabilirlik Alanları arasında dağıtılması gerekir. Bu, temel alınan Azure altyapısında küme VM 'lerinin dağıtılmış yerleştirmesini sağlar. Kullanılabilirlik bölgesi dağıtımları bu teknolojiyle desteklenir.
>

## <a name="generic-file-share-with-sios-datakeeper-as-cluster-shared-disks"></a>Küme Paylaşılan diskleri olarak SIOS veri Man ile genel dosya paylaşma


Genel bir dosya paylaşma, yüksek oranda kullanılabilir bir dosya paylaşımının elde etmek için başka bir seçenektir.

Bu durumda, bir üçüncü taraf SIOS çözümünü küme paylaşılan diski olarak kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [SAP Ass/SCS örneği için Windows Yük devretme kümesi ve dosya paylaşma kullanarak SAP HA için Azure altyapısını hazırlama][sap-high-availability-infrastructure-wsfc-file-share]
* [SAP NetWeaver HA 'yi bir Windows Yük devretme kümesine ve bir SAP ASCS/SCS örneği için dosya paylaşımında yüklemesi][sap-high-availability-installation-wsfc-shared-disk]
* [Azure 'da UPD depolaması için iki düğümlü Depolama Alanları Doğrudan genişleme dosya sunucusu dağıtma][deploy-sofs-s2d-in-azure]
* [Windows Server 2016 ' de Depolama Alanları Doğrudan][s2d-in-win-2016]
* [Derin bakış: Depolama Alanları Doğrudan birimler][deep-dive-volumes-in-s2d]
