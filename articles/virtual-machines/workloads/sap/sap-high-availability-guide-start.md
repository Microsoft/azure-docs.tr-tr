---
title: Azure sanal makineler SAP NetWeaver için yüksek kullanılabilirlik | Microsoft Docs
description: Azure sanal makinelerde SAP NetWeaver için yüksek kullanılabilirlik Kılavuzu
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 1cfcc14a-6795-4cfd-a740-aa09d6d2b817
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8a4e4cb8330f6b02d721f2228fee17a91cd44499
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "70078352"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver"></a>Azure sanal makineler SAP NetWeaver için yüksek kullanılabilirlik

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-file-share]:sap-high-availability-installation-wsfc-file-share.md

[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md
[sap-suse-ascs-ha-setting-ha-nfs]:high-availability-guide-suse.md#setting-up-a-highly-available-nfs-server
[sap-suse-ascs-ha-setting-ha-anf]:high-availability-guide-suse-netapp-files.md#setting-up-the-azure-netapp-files-infrastructure
[sap-suse-ascs-ha-sap-installation]:high-availability-guide-suse.md#prepare-for-sap-netweaver-installation
[sap-suse-ascs-ha-sap-installation-anf]:high-availability-guide-suse-netapp-files.md
[sap-rhel-ascs-ha]:high-availability-guide-rhel.md
[sap-rhel-ascs-ha-sap-installation-anf]:high-availability-guide-rhel-netapp-files.md
[high-availability-guide-rhel-glusterfs]:high-availability-guide-rhel-glusterfs.md
[high-availability-guide-rhel-pacemaker]:high-availability-guide-rhel-pacemaker.md

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

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

Azure sanal makineleri, işlem, depolama ve ağ kaynakları, en kısa sürede ve uzun tedarik döngüleri olmadan ihtiyaç duyulan kuruluşların çözümüdür. SAP NetWeaver tabanlı ABAP, Java ve bir ABAP + Java yığını gibi klasik uygulamaları dağıtmak için Azure sanal makinelerini kullanabilirsiniz. Ek şirket içi kaynaklar olmadan güvenilirliği ve kullanılabilirliği genişletin. Azure sanal makineleri, şirket içi etki alanları, özel bulutlar ve SAP sistem yataya Azure sanal makinelerini tümleştirmenize olanak sağlamak için şirketler arası bağlantıyı destekler.

Bu makale serisi şunları içerir:

* Mimari ve senaryolar.
* Altyapı hazırlığı.
* Azure Resource Manager dağıtım modelini kullanarak Azure 'da yüksek kullanılabilirliğe sahip SAP sistemleri dağıtmaya yönelik SAP yükleme adımları.

    > [!IMPORTANT]
    > SAP yüklemelerinizin Azure Resource Manager dağıtım modelini kullanmanızı önemle öneririz. Klasik dağıtım modelinde kullanılamayan birçok avantaj sunar. Azure [dağıtım modelleri][virtual-machines-azure-resource-manager-architecture-benefits-arm] hakkında daha fazla bilgi edinin.   
    >
* SAP yüksek kullanılabilirlik:
  * Windows **Server yük devretme kümesi (wsfc)** kullanarak ![Windows ][Logo_Windows]**Windows**
  * **Linux Cluster Framework** kullanarak ![Linux ][Logo_Linux] **Linux**

Bu makalelerde, SAP Merkezi Hizmetleri (yoks/SCS) ve veritabanı yönetim sistemleri (DBMS) gibi tek hata noktası (SPI) bileşenlerini korumaya nasıl yardımcı olabileceğinizi öğreneceksiniz. Ayrıca, Azure 'da SAP uygulama sunucusu gibi yedekli bileşenler hakkında bilgi edinebilirsiniz.

## <a name="high-availability-architecture-and-scenarios-for-sap-netweaver"></a>SAP NetWeaver için yüksek kullanılabilirliğe sahip mimari ve senaryolar

**Özet:** Bu makalede, Azure 'da SAP sisteminin yüksek kullanılabilirlik mimarisini tartıştık. SAP tek hata noktası (SPOF) ve yedekli bileşenlerin yüksek kullanılabilirlik düzeyini ve Azure altyapı yüksek kullanılabilirlik özelliklerini çözmeyi tartıştık. Ayrıca, bu parçaların SAP sistem bileşenleriyle ilişkisini de ele aldık. Ayrıca, tartışma Windows ve Linux özellikleri için kırılır. Çeşitli SAP yüksek kullanılabilirlik senaryoları da ele alınmıştır.

**Güncelleştirilme tarihi:** 2017 Ekim

* [SAP NetWeaver için Azure sanal makineler yüksek kullanılabilirliğe sahip mimari ve senaryolar][sap-high-availability-architecture-scenarios]

Makalede hem ![Windows ][Logo_Windows] **Windows** hem de ![Linux ][Logo_Linux] **Linux**ele alınmaktadır.


## <a name="azure-infrastructure-preparation-for-sap-netweaver-high-availability-deployment"></a>SAP NetWeaver yüksek kullanılabilirlik dağıtımı için Azure altyapı hazırlığı

**Özet:** Burada listelenen makalelerde, SAP yüklemesine hazırlanmaya yönelik Azure altyapısını dağıtmak için gerçekleştirebileceğiniz adımlar ele alınmaktadır. Azure altyapı dağıtımını basitleştirmek için SAP Azure Resource Manager şablonları tüm işlemi otomatikleştirmek için kullanılır.

**Güncelleştirilme tarihi:** Mart 2019

* [SAP Ass/SCS örnekleri için bir Windows Yük devretme kümesi ve **paylaşılan DISK** kullanarak SAP yüksek kullanılabilirlik için Azure altyapısını][sap-high-availability-infrastructure-wsfc-shared-disk] ![Windows ][Logo_Windows] hazırlama

* [SAP Ass/SCS örnekleri için bir Windows Yük devretme kümesi ve **dosya paylaşma** kullanarak SAP yüksek kullanılabilirlik Için Azure altyapısını hazırlama][sap-high-availability-infrastructure-wsfc-file-share] ][Logo_Windows] ![Windows

* [SAP Ass/SCS örnekleri için SUSE Linux Enterprise Server kümesi çerçevesini kullanarak SAP yüksek kullanılabilirlik Için Azure altyapısını][sap-suse-ascs-ha-setting-ha-nfs] ![Linux ][Logo_Linux] hazırlama

* ![Linux ][Logo_Linux] Azure [NetApp dosyaları Ile SAP Ass/SCS örnekleri için bir SUSE Linux Enterprise Server kümesi çerçevesi kullanarak SAP yüksek kullanılabilirlik Için Azure altyapısını hazırlama][sap-suse-ascs-ha-setting-ha-anf]

* ![RHEL ][Logo_Linux] [SAP yoks/SCS yüksek kullanılabilirlik Için Azure altyapısını hazırlama-RHEL 'de GlusterFS ayarlama][high-availability-guide-rhel-glusterfs]

* ![RHEL ][Logo_Linux] [SAP yoks/SCS yüksek kullanılabilirlik Için Azure altyapısını hazırlama-RHEL üzerinde Paceyapıcısı ayarlama][high-availability-guide-rhel-pacemaker]

## <a name="installation-of-an-sap-netweaver-high-availability-system-in-azure"></a>Azure 'da SAP NetWeaver yüksek kullanılabilirlik sistemi yüklemesi

**Özet:** Burada listelenen makaleler, Azure 'da bir Windows Server Yük Devretme Kümelemesi kümesi ve Linux kümesi çerçevesinde yüksek kullanılabilirliğe sahip bir SAP sisteminin yüklenmesi ve yapılandırılmasına ilişkin adım adım örnekler sunar.

**Güncelleştirilme tarihi:** Mart 2019

* ![Windows ][Logo_Windows] SAP [ASCS/SCS örnekleri için bir Windows Yük devretme kümesi ve **paylaşılan DISK** kullanarak SAP NetWeaver yüksek kullanılabilirliği yüklemeyi][sap-high-availability-installation-wsfc-shared-disk]

* ![Windows ][Logo_Windows] SAP [ASCS/SCS örnekleri için bir Windows Yük devretme kümesi ve **dosya paylaşma** kullanarak SAP NetWeaver yüksek kullanılabilirliği yüklemeyi][sap-high-availability-installation-wsfc-file-share]

* [SAP ASCS/SCS örnekleri için SUSE Linux Enterprise Server kümesi çerçevesini kullanarak SAP NetWeaver yüksek kullanılabilirlik][sap-suse-ascs-ha-sap-installation] ![Linux ][Logo_Linux] yüklemesi

* ![Linux ][Logo_Linux] SAP [ASCS/SCS örnekleri için bir SUSE Linux Enterprise Server kümesi çerçevesi kullanarak SAP NetWeaver yüksek kullanılabilirliği 'nı kullanın Azure NetApp Files][sap-suse-ascs-ha-sap-installation-anf]

* ![RHEL ][Logo_Linux] [SAP NetWeaver yoks/SCS 'yi, RHEL üzerinde yüksek kullanılabilirlik yapılandırmasında yüklemesi][sap-rhel-ascs-ha]

* ![RHEL ][Logo_Linux] [SAP NetWeaver yoks/SCS 'yi Azure NetApp Files Ile RHEL üzerinde yüksek kullanılabilirlik yapılandırmasında yüklemesi][sap-rhel-ascs-ha-sap-installation-anf]

