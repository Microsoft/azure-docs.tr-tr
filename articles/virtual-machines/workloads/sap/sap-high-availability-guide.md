---
title: Azure sanal makineler SAP NetWeaver için yüksek kullanılabilirlik | Microsoft Docs
description: Azure sanal makinelerde SAP NetWeaver için yüksek kullanılabilirlik Kılavuzu
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
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bebdd8beacb3b5fc91802fb41055df7e24b93218
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70078961"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver"></a>Azure sanal makineler SAP NetWeaver için yüksek kullanılabilirlik

[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2243692]: https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

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



Azure sanal makineleri, işlem, depolama ve ağ kaynakları, en kısa sürede ve uzun tedarik döngüleri olmadan ihtiyaç duyulan kuruluşların çözümüdür. SAP NetWeaver tabanlı ABAP, Java ve bir ABAP + Java yığını gibi klasik uygulamaları dağıtmak için Azure sanal makinelerini kullanabilirsiniz. Ek şirket içi kaynaklar olmadan güvenilirliği ve kullanılabilirliği genişletin. Azure sanal makineleri, şirket içi etki alanları, özel bulutlar ve SAP sistem yataya Azure sanal makinelerini tümleştirmenize olanak sağlamak için şirketler arası bağlantıyı destekler.

Bu makalede, Azure Resource Manager dağıtım modelini kullanarak Azure 'da yüksek kullanılabilirliğe sahip SAP sistemlerini dağıtmak için uygulayabileceğiniz adımları ele aldık. Şu ana görevlerde size kılavuzluk ederiz:

* [Kaynaklar][sap-ha-guide-2] bölümünde LISTELENEN doğru SAP notlarını ve yükleme kılavuzlarını bulun. Bu makalede, belirli platformlarda SAP yazılımını yüklemenize ve dağıtmanıza yardımcı olabilecek birincil kaynaklar olan SAP yükleme belgeleri ve SAP notları açıklanır.
* Azure Resource Manager dağıtım modeliyle klasik Azure dağıtım modeli arasındaki farkları öğrenin.
* Windows Server Yük Devretme Kümelemesi çekirdek modları hakkında bilgi edinmek için, Azure dağıtımınız için uygun olan modeli seçebilirsiniz.
* Azure hizmetlerinde Windows Server Yük Devretme Kümelemesi paylaşılan depolama hakkında bilgi edinin.
* Gelişmiş Iş uygulaması programlama (ABAP) SAP Merkezi Hizmetleri (ASCS)/SAP Merkezi Hizmetleri (SCS) ve veritabanı yönetim sistemleri (DBMS) ve SAP uygulaması gibi yedekli bileşenler gibi tek noktalı hata bileşenlerinin korunmasına nasıl yardımcı olabileceğinizi öğrenin Sunucu, Azure 'da.
* Azure Resource Manager kullanarak Azure 'da bir Windows Server Yük Devretme Kümelemesi kümesinde yüksek kullanılabilirliğe sahip bir SAP sisteminin yükleme ve yapılandırma hakkında adım adım bir örnek izleyin.
* Azure 'da Windows Server Yük Devretme Kümelemesi 'ni kullanmak için gereken ek adımlar hakkında bilgi edinin, ancak bu, şirket içi dağıtımda gerekli değildir.

Dağıtım ve yapılandırmayı basitleştirmek için, bu makalede SAP üç katmanlı yüksek kullanılabilirliğe sahip Kaynak Yöneticisi şablonları kullanırız. Şablonlar, yüksek kullanılabilirliğe sahip bir SAP sistemi için ihtiyaç duyduğunuz tüm altyapının dağıtımını otomatik hale getirir. Altyapı Ayrıca SAP sisteminizin performans standardı (SAPS) boyutunu destekler.

## <a name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a> Önkoşullar
Başlamadan önce, aşağıdaki bölümlerde açıklanan önkoşulları karşıladığınızdan emin olun. Ayrıca, [kaynaklar][sap-ha-guide-2] bölümünde listelenen tüm kaynakları denetlediğinizden emin olun.

Bu makalede, [yönetilen diskleri kullanarak üç KATMANLı SAP NetWeaver](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md/)için Azure Resource Manager şablonları kullanırız. Şablonlara faydalı bir genel bakış için bkz. [SAP Azure Resource Manager şablonları](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/).

## <a name="42b8f600-7ba3-4606-b8a5-53c4f026da08"></a>Kaynakların
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
| [2015553] |Microsoft Azure SAP: Destek önkoşulları |
| [1999351] |SAP için geliştirilmiş Azure Izleme |
| [2178632] |Microsoft Azure üzerinde SAP için anahtar Izleme ölçümleri |
| [1999351] |Windows 'da sanallaştırma: Gelişmiş Izleme |
| [2243692] |SAP DBMS örneği için Azure Premium SSD depolama kullanımı |

Genel varsayılan sınırlamalar ve en yüksek sınırlamalar dahil olmak üzere [Azure aboneliklerinin sınırlamaları][azure-subscription-service-limits-subscription]hakkında daha fazla bilgi edinin.

## <a name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a>Azure klasik dağıtım modeli Azure Resource Manager ile yüksek kullanılabilirliğe sahip SAP
Azure Resource Manager ve klasik Azure dağıtım modelleri aşağıdaki alanlarda farklıdır:

- Kaynak grupları
- Azure Kaynak grubunda Azure iç yük dengeleyici bağımlılığı
- SAP çoklu SID senaryoları desteği

### <a name="f76af273-1993-4d83-b12d-65deeae23686"></a>Kaynak grupları
Azure Resource Manager, Azure aboneliğinizdeki tüm uygulama kaynaklarını yönetmek için kaynak gruplarını kullanabilirsiniz. Tümleşik bir yaklaşım olan bir kaynak grubunda, tüm kaynaklar aynı yaşam döngüsüne sahiptir. Örneğin, tüm kaynaklar aynı anda oluşturulur ve aynı zamanda silinir. [Kaynak grupları](../../../azure-resource-manager/resource-group-overview.md#resource-groups) hakkında daha fazla bilgi edinin.

### <a name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a>Azure Kaynak grubunda Azure iç yük dengeleyici bağımlılığı

Klasik Azure dağıtım modelinde, Azure iç yük dengeleyici (Azure Load Balancer hizmeti) ve bulut hizmeti arasında bir bağımlılık vardır. Her iç yük dengeleyicinin bir bulut hizmeti olması gerekir.

Azure Resource Manager, her Azure kaynağının bir Azure Kaynak grubuna yerleştirilmesi gerekir ve bu, Azure Load Balancer için de geçerlidir. Ancak, Azure yük dengeleyici başına bir Azure Kaynak grubuna sahip olmanız gerekmez, örneğin, bir Azure Kaynak grubu birden çok Azure Yük dengeleyiciyi içerebilir. Ortam daha basit ve daha esnektir. 

### <a name="support-for-sap-multi-sid-scenarios"></a>SAP çoklu SID senaryoları desteği

Azure Resource Manager, bir kümede birden fazla SAP sistem tanımlayıcısı (SID) ASCS/SCS örneği yükleyebilirsiniz. Her bir Azure iç yük dengeleyici için birden çok IP adresi desteği nedeniyle çoklu SID örnekleri mümkündür.

Klasik Azure dağıtım modelini kullanmak için, Azure 'da SAP NetWeaver 'de [açıklanan yordamları izleyin: Azure 'daki Windows Server Yük Devretme Kümelemesi 'ni, SIOS Dataman](https://go.microsoft.com/fwlink/?LinkId=613056)ile kullanarak SAP ascs/SCS örneklerini kümeleme.

> [!IMPORTANT]
> SAP yüklemelerinizin Azure Resource Manager dağıtım modelini kullanmanızı önemle öneririz. Klasik dağıtım modelinde kullanılamayan birçok avantaj sunar. Azure [dağıtım modelleri][virtual-machines-azure-resource-manager-architecture-benefits-arm] hakkında daha fazla bilgi edinin.   
>
>

## <a name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a>Windows Server Yük Devretme Kümelemesi
Windows Server Yük Devretme Kümelemesi, Windows 'da yüksek kullanılabilirliğe sahip SAP Ass/SCS yüklemesi ve DBMS 'nin temelidir.

Yük devretme kümesi, uygulamaların ve hizmetlerin kullanılabilirliğini artırmak için birlikte çalışan 1 + n bağımsız sunucu (düğüm) grubudur. Bir düğüm hatası oluşursa, Windows Server Yük Devretme Kümelemesi, uygulamalar ve hizmetler sağlamak üzere sağlıklı bir kümeyi sürdürirken oluşabilecek hata sayısını hesaplar. Yük Devretme Kümelemesi elde etmek için farklı çekirdek modlarında seçim yapabilirsiniz.

### <a name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a>Çekirdek modları
Windows Server Yük Devretme Kümelemesi kullandığınızda dört çekirdek modu arasından seçim yapabilirsiniz:

* **Düğüm çoğunluğu**. Kümenin her bir düğümü oylayabilir. Küme yalnızca oyları olan, yani oyından fazla olacak şekilde çalışır. Düzensiz sayıda düğüme sahip kümeler için bu seçeneği öneririz. Örneğin, yedi düğümlü bir küme içindeki üç düğüm başarısız olabilir ve küme stilleri büyük bir çoğunluğuna ulaşır ve çalışmaya devam eder.  
* **Düğüm ve disk çoğunluğu**. Küme depolamadaki her düğüm ve belirlenen bir disk (bir disk tanığı), kullanılabilir olduklarında ve iletişim kutusunda oy verebilir. Küme yalnızca oylarının büyük bir çoğunluğuna sahip olur, yani oyları yarıya kadar büyüktür. Bu mod, çift sayıda düğüm içeren bir küme ortamında mantıklı olur. Düğümlerin ve diskin yarısı çevrimiçiyse, küme sağlıklı durumda kalır.
* **Düğüm ve dosya paylaşımının çoğunluğu**. Her düğüm ve yönetici tarafından oluşturulan bir dosya paylaşımının (bir dosya paylaşma tanığı), düğümlerin ve dosya paylaşımının kullanılabilir ve iletişim içinde olup olmamasına bakılmaksızın, bu, belirlenen bir dosya paylaşma (dosya paylaşma tanığı). Küme yalnızca oylarının büyük bir çoğunluğuna sahip olur, yani oyları yarıya kadar büyüktür. Bu mod, çift sayıda düğüm içeren bir küme ortamında mantıklı olur. Düğüm ve disk çoğunluğu moduna benzerdir, ancak tanık disk yerine bir tanık dosya paylaşma kullanır. Bu modun uygulanması kolaydır, ancak dosya paylaşımının kendisi yüksek oranda kullanılabilir değilse, tek hata noktası olabilir.
* **Çoğunluk yok: Yalnızca**disk. Bir düğüm varsa ve küme depolamadaki belirli bir diskle iletişim kutusunda kümenin bir çekirdeği vardır. Yalnızca bu diskle iletişim de olan düğümler kümeye katılabilir. Bu modu kullanmanızı öneririz.
 

## <a name="fdfee875-6e66-483a-a343-14bbaee33275"></a>Şirket içi Windows Server Yük Devretme Kümelemesi
Şekil 1 ' de iki düğümün kümesi gösterilmektedir. Düğümler arasındaki ağ bağlantısı başarısız olursa ve her iki düğüm de çalışır duruma çıkarsa, bir çekirdek disk veya dosya paylaşımında kümenin uygulama ve hizmetlerini sağlamaya devam edecek olan düğüm belirlenir. Çekirdek diskine veya dosya paylaşıma erişimi olan düğüm, hizmetlerin devam etmesini sağlayan düğümdür.

Bu örnek iki düğümlü bir küme kullandığından, düğüm ve dosya paylaşma çoğunluğu çekirdek modunu kullanırız. Düğüm ve disk çoğunluğu de geçerli bir seçenektir. Bir üretim ortamında, bir çekirdek disk kullanmanızı öneririz. Ağ ve depolama sistemi teknolojisini kullanarak yüksek oranda kullanılabilir hale getirebilirsiniz.

![Şekil 1: Azure 'da SAP yoks/SCS için Windows Server Yük Devretme Kümelemesi yapılandırması örneği][sap-ha-guide-figure-1000]

_**Şekil 1:** Azure 'da SAP yoks/SCS için Windows Server Yük Devretme Kümelemesi yapılandırması örneği_

### <a name="be21cf3e-fb01-402b-9955-54fbecf66592"></a>Paylaşılan depolama alanı
Şekil 1 ' de iki düğümlü bir paylaşılan depolama kümesi de gösterilmektedir. Şirket içi paylaşılan depolama kümesinde, kümedeki tüm düğümler paylaşılan depolamayı algılar. Kilitleme mekanizması, verilerin bozulmasını önler. Tüm düğümler, başka bir düğümün başarısız olup olmadığını algılayabilir. Bir düğüm başarısız olursa, kalan düğüm depolama kaynaklarının sahipliğini alır ve hizmetlerin kullanılabilirliğini sağlar.

> [!NOTE]
> SQL Server gibi bazı DBMS uygulamalarıyla yüksek kullanılabilirlik için paylaşılan disklere ihtiyacınız yoktur. Her zaman SQL Server, tek bir küme düğümünün yerel diskinden DBMS verilerini ve günlük dosyalarını başka bir küme düğümünün yerel diskine çoğaltır. Bu durumda, Windows küme yapılandırmasına paylaşılan bir disk gerekmez.
>
>

### <a name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a>Ağ ve ad çözümlemesi
İstemci bilgisayarlar, kümeye bir sanal IP adresi ve DNS sunucusunun sağladığı bir sanal ana bilgisayar adı üzerinden ulaşabilirler. Şirket içi düğümler ve DNS sunucusu birden çok IP adresini işleyebilir.

Tipik bir kurulumda iki veya daha fazla ağ bağlantısı kullanırsınız:

* Depolamaya adanmış bir bağlantı
* Sinyal için bir küme iç ağ bağlantısı
* İstemcilerin kümeye bağlanmak için kullandığı ortak ağ

## <a name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a>Azure 'da Windows Server Yük Devretme Kümelemesi
Çıplak veya özel bulut dağıtımlarıyla karşılaştırıldığında, Azure sanal makineleri, Windows Server Yük Devretme Kümelemesi 'ni yapılandırmak için ek adımlar gerektirir. Paylaşılan bir küme diski oluştururken, SAP ASCS/SCS örneği için birkaç IP adresi ve sanal konak adı ayarlamanız gerekir.

Bu makalede, Azure 'da SAP yüksek kullanılabilirlik Merkezi Hizmetleri kümesi oluşturmak için gereken temel kavramları ve ek adımları tartıştık. Üçüncü taraf aracı SIOS veri ve Azure iç yük dengeleyicinin nasıl yapılandırılacağını göstereceğiz. Bu araçları, Azure 'da dosya paylaşma tanığı olan bir Windows Yük devretme kümesi oluşturmak için kullanabilirsiniz.

![Şekil 2: Azure 'da paylaşılan disk olmadan Windows Server Yük Devretme Kümelemesi yapılandırması][sap-ha-guide-figure-1001]

_**Şekil 2:** Azure 'da paylaşılan disk olmadan Windows Server Yük Devretme Kümelemesi yapılandırması_

### <a name="1a464091-922b-48d7-9d08-7cecf757f341"></a>Azure 'da SIOS Dataman ile paylaşılan disk
Yüksek kullanılabilirliğe sahip SAP yoks/SCS örneği için küme paylaşılan depolama gerekir. Azure, 2016 Eylül itibariyle paylaşılan depolama kümesi oluşturmak için kullanabileceğiniz paylaşılan depolama alanı sunmaz. Küme paylaşılan depolama alanını taklit eden yansıtılmış bir depolama alanı oluşturmak için üçüncü taraf yazılım SIOS Dataman kümesi sürümünü kullanabilirsiniz. SIOS çözümü gerçek zamanlı zaman uyumlu veri çoğaltması sağlar. Bir küme için paylaşılan disk kaynağı oluşturma işlemi şu şekilde yapılır:

1. Bir Windows küme yapılandırmasındaki sanal makinelerin (VM) her birine ek bir disk ekleyin.
2. Her iki sanal makine düğümünde de SIOS Dataman küme sürümünü çalıştırın.
3. Disk bağlantılı ek birimin içeriğini kaynak sanal makineden hedef sanal makinenin ek disk ekli birimine yansıtan şekilde, SIOS Dataman küme sürümünü yapılandırın. SIOS Dataman, kaynak ve hedef yerel birimleri soyutlar ve sonra bunları bir paylaşılan disk olarak Windows Server Yük Devretme Kümelemesi 'ne gösterir.

[SIOS Verilerman](https://us.sios.com/products/datakeeper-cluster/)hakkında daha fazla bilgi alın.

![Şekil 3: Azure 'da SIOS Dataman ile Windows Server Yük Devretme Kümelemesi yapılandırması][sap-ha-guide-figure-1002]

_**Şekil 3:** Azure 'da SIOS Dataman ile Windows Server Yük Devretme Kümelemesi yapılandırması_

> [!NOTE]
> SQL Server gibi bazı DBMS ürünleriyle yüksek kullanılabilirlik için paylaşılan disklere ihtiyacınız yoktur. Her zaman SQL Server, tek bir küme düğümünün yerel diskinden DBMS verilerini ve günlük dosyalarını başka bir küme düğümünün yerel diskine çoğaltır. Bu durumda, Windows küme yapılandırmasına paylaşılan bir disk gerekmez.
>
>

### <a name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a>Azure 'da ad çözümlemesi
Azure bulut platformu, kayan IP adresleri gibi sanal IP adreslerini yapılandırma seçeneği sunmaz. Bulutta küme kaynağına ulaşmak üzere bir sanal IP adresi ayarlamak için alternatif bir çözüme ihtiyacınız vardır.
Azure 'da Azure Load Balancer hizmetinde bir iç yük dengeleyici vardır. İç yük dengeleyici ile istemciler küme sanal IP adresi üzerinden kümeye ulaşabilirler.
İç yük dengeleyiciyi, küme düğümlerini içeren kaynak grubunda dağıtmanız gerekir. Ardından, tüm gerekli bağlantı noktası iletme kurallarını iç yük dengeleyicinin araştırma bağlantı noktalarıyla yapılandırın.
İstemciler sanal ana bilgisayar adı aracılığıyla bağlanabilir. DNS sunucusu, küme IP adresini çözümler ve iç yük dengeleyici, kümenin etkin düğümüne bağlantı noktası iletmeyi işler.

## <a name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a>Azure hizmet olarak altyapı (IaaS) içinde SAP NetWeaver yüksek kullanılabilirliği
SAP yazılım bileşenleri gibi SAP uygulaması yüksek kullanılabilirlik elde etmek için aşağıdaki bileşenleri korumanız gerekir:

* SAP uygulama sunucusu örneği
* SAP ASCS/SCS örneği
* DBMS sunucusu

Yüksek kullanılabilirlik senaryolarında SAP bileşenlerini koruma hakkında daha fazla bilgi için bkz. [SAP NetWeaver Için Azure sanal makineleri planlama ve uygulama][planning-guide-11].

### <a name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a>Yüksek kullanılabilirliğe sahip SAP uygulama sunucusu
SAP uygulama sunucusu ve iletişim örnekleri için genellikle belirli bir yüksek kullanılabilirlik çözümüne ihtiyacınız yoktur. Yedeklilik ile yüksek kullanılabilirlik elde edersiniz ve Azure sanal makinelerinin farklı örneklerinde birden fazla iletişim kutusu örneği yapılandıracaksınız. İki Azure sanal makine örneğinde yüklü en az iki SAP uygulaması örneği olmalıdır.

![Şekil 4: Yüksek kullanılabilirliğe sahip SAP uygulama sunucusu][sap-ha-guide-figure-2000]

_**Şekil 4:** Yüksek kullanılabilirliğe sahip SAP uygulama sunucusu_

SAP uygulama sunucusu örneklerini barındıran tüm sanal makineleri aynı Azure kullanılabilirlik kümesine yerleştirmeniz gerekir. Azure kullanılabilirlik kümesi şunları sağlar:

* Tüm sanal makineler aynı yükseltme etki alanının bir parçasıdır. Örneğin, bir yükseltme etki alanı, planlı bakım kapalı kalma süresi boyunca sanal makinelerin aynı anda güncel olmadığından emin olur.
* Tüm sanal makineler aynı hata etki alanının bir parçasıdır. Örneğin, bir hata etki alanı, sanal makinelerin dağıtıldığından emin olur, böylece tek bir hata noktası tüm sanal makinelerin kullanılabilirliğini etkilemektedir.

[Sanal makinelerin kullanılabilirliğini yönetme][virtual-machines-manage-availability]hakkında daha fazla bilgi edinin.

Yalnızca yönetilmeyen disk: Azure depolama hesabı olası bir tek hata noktası olduğundan, en az iki sanal makinenin dağıtıldığı en az iki Azure depolama hesabı olması önemlidir. İdeal bir kurulumda, SAP iletişim kutusu örneği çalıştıran her sanal makinenin diskleri farklı bir depolama hesabına dağıtılır.

### <a name="f559c285-ee68-4eec-add1-f60fe7b978db"></a>Yüksek kullanılabilirliğe sahip SAP yoks/SCS örneği
Şekil 5, yüksek kullanılabilirliğe sahip SAP yoks/SCS örneğine bir örnektir.

![Şekil 5: Yüksek kullanılabilirliğe sahip SAP yoks/SCS örneği][sap-ha-guide-figure-2001]

_**Şekil 5:** Yüksek kullanılabilirliğe sahip SAP yoks/SCS örneği_

#### <a name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a>Azure 'da Windows Server Yük Devretme Kümelemesi ile SAP ASCS/SCS örneği yüksek kullanılabilirliği
Çıplak veya özel bulut dağıtımlarıyla karşılaştırıldığında, Azure sanal makineleri, Windows Server Yük Devretme Kümelemesi 'ni yapılandırmak için ek adımlar gerektirir. Bir Windows Yük devretme kümesi oluşturmak için, bir SAP ASCS/SCS örneği Kümelemeye yönelik bir paylaşılan küme diskine, birkaç IP adresine, birkaç sanal konak adına ve bir Azure iç yük dengeleyicisine sahip olmanız gerekir. Bu konuda makalenin ilerleyen bölümlerinde daha ayrıntılı olarak ele alınmaktadır.

![Şekil 6: Azure 'da bir SAP ASCS/SCS yapılandırması için, SIOS Dataman kullanarak Windows Server Yük Devretme Kümelemesi][sap-ha-guide-figure-1002]

_**Şekil 6:** Azure 'da, SIOS Dataman ile SAP ASCS/SCS yapılandırması için Windows Server Yük Devretme Kümelemesi_

### <a name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a>Yüksek kullanılabilirlik DBMS örneği
DBMS Ayrıca bir SAP sisteminde tek bir iletişim noktasıdır. Yüksek kullanılabilirlik çözümü kullanarak korumanız gerekir. Şekil 7 ' de, Windows Server Yük Devretme Kümelemesi ve Azure iç yük dengeleyici ile Azure 'da SQL Server her zaman yüksek kullanılabilirliğe sahip bir çözüm gösterilmektedir. Her zaman SQL Server, kendi DBMS çoğaltmasını kullanarak DBMS verilerini ve günlük dosyalarını çoğaltır. Bu durumda, tüm kurulumun basitleşerek küme paylaşılan disklere ihtiyacınız yoktur.

![Şekil 7: SQL Server her zaman açık olan yüksek kullanılabilirliğe sahip SAP DBMS örneği][sap-ha-guide-figure-2003]

_**Şekil 7:** SQL Server her zaman açık olan yüksek kullanılabilirliğe sahip SAP DBMS örneği_

Azure 'da Azure Resource Manager dağıtım modelini kullanarak kümeleme SQL Server hakkında daha fazla bilgi için şu makalelere bakın:

* [Kaynak Yöneticisi kullanarak Azure sanal makinelerinde Always on kullanılabilirlik grubunu el ile yapılandırma [sanal makineler-Windows-Portal-SQL-AlwaysOn-kullanılabilirlik-gruplar-Manual]
* [Azure 'da Always on kullanılabilirlik grubu için Azure iç yük dengeleyici yapılandırma] [sanal makineler-Windows-Portal-SQL-AlwaysOn-int-Listener]

## <a name="045252ed-0277-4fc8-8f46-c5a29694a816"></a>Uçtan uca yüksek kullanılabilirliğe sahip dağıtım senaryoları

### <a name="deployment-scenario-using-architectural-template-1"></a>Mimari Şablon 1 kullanarak dağıtım senaryosu

Şekil 8 ' de, **bir** SAP sistemi için Azure 'Da SAP NetWeaver yüksek kullanılabilirlik mimarisi örneği gösterilmektedir. Bu senaryo aşağıdaki şekilde ayarlanır:

- SAP ASCS/SCS örneği için ayrılmış bir küme kullanılır.
- DBMS örneği için ayrılmış bir küme kullanılır.
- SAP uygulama sunucusu örnekleri kendi adanmış VM 'lerine dağıtılır.

![Şekil 8: Ass/SCS ve DBMS için adanmış küme ile SAP yüksek kullanılabilirliğe sahip mimari Şablon 1][sap-ha-guide-figure-2004]

_**Şekil 8:** SAP yüksek kullanılabilirliğe sahip mimari Şablon 1, yoks/SCS ve DBMS için adanmış kümeler_

### <a name="deployment-scenario-using-architectural-template-2"></a>Mimari şablonu kullanarak dağıtım senaryosu 2

Şekil 9 ' da, **bir** SAP sistemi için Azure 'Da SAP NetWeaver yüksek kullanılabilirliğe sahip bir mimari örneği gösterilmektedir. Bu senaryo aşağıdaki şekilde ayarlanır:

- SAP ASCS/SCS örneği ve DBMS için bir adanmış küme kullanılır.
- SAP uygulama sunucusu örnekleri, kendi adanmış VM 'lerde dağıtılır.

![Şekil 9: SAP yüksek kullanılabilirliğe sahip mimari Şablon 2, yoks/SCS için adanmış bir küme ve DBMS için adanmış bir küme ile][sap-ha-guide-figure-2005]

_**Şekil 9:** SAP yüksek kullanılabilirliğe sahip mimari Şablon 2, yoks/SCS için adanmış bir küme ve DBMS için adanmış bir küme ile_

### <a name="deployment-scenario-using-architectural-template-3"></a>Mimari şablonu kullanarak dağıtım senaryosu 3

Şekil 10 ' da, SID1&gt; ve &lt;SID2&gt;ile &lt; **iki** SAP sistemi için Azure 'da SAP NetWeaver yüksek kullanılabilirlik mimarisine bir örnek gösterilmektedir. Bu senaryo aşağıdaki şekilde ayarlanır:

- SAP ASCS/SCS SID1 ÖRNEĞI *ve* SAP ascs/SCS SID2 örneği (bir küme) için ayrılmış bir küme kullanılır.
- DBMS SID1 için bir adanmış küme kullanılır ve DBMS SID2 (iki küme) için başka bir adanmış küme kullanılır.
- SAP System SID1 için SAP uygulama sunucusu örneklerinin kendi ayrılmış sanal makineleri vardır.
- SAP System SID2 için SAP uygulama sunucusu örneklerinin kendi ayrılmış sanal makineleri vardır.

![Şekil 10: Farklı yoks/SCS örnekleri için adanmış bir küme ile SAP yüksek kullanılabilirliğe sahip mimari Şablon 3][sap-ha-guide-figure-6003]

_**Şekil 10:** Farklı yoks/SCS örnekleri için adanmış bir küme ile SAP yüksek kullanılabilirliğe sahip mimari Şablon 3_

## <a name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a>Altyapıyı hazırlama

### <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Altyapıyı mimari Şablon 1 için hazırlama
SAP için Azure Resource Manager şablonları, gerekli kaynakların dağıtımını basitleştirmeye yardımcı olur.

Azure Resource Manager içindeki üç katmanlı şablonlar, iki kümeye sahip mimari Şablon 1 gibi yüksek kullanılabilirlik senaryolarını de destekler. Her küme SAP ASCS/SCS ve DBMS için SAP tek hata noktasıdır.

Burada, bu makalede anladığımız örnek senaryo için Azure Resource Manager şablonları edinebilirsiniz:

* [Azure Market görüntüsü](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Yönetilen diskleri kullanarak Azure Marketi görüntüsü](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md)  
* [Özel görüntü](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)
* [Yönetilen diskleri kullanan özel görüntü](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-md)

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

  * **Azure depolama hesapları (yalnızca yönetilmeyen diskler)**

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
> Ağ kartlarının ve Azure iç yük dengeleyicileri 'nin tüm IP adresleri varsayılan olarak dinamiktir. Onları **statik** IP adresleriyle değiştirin. Bunun nasıl yapılacağını makalenin ilerleyen bölümlerinde anlatmaktadır.
>
>

### <a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a>Üretimde kullanmak üzere kurumsal ağ bağlantısı (şirketler arası) ile sanal makineler dağıtma
Üretim SAP sistemleri için, Azure siteden siteye VPN veya Azure ExpressRoute kullanarak Azure sanal makinelerini [Kurumsal ağ bağlantısı (Şirket içi)][planning-guide-2.2] ile dağıtın.

> [!NOTE]
> Azure sanal ağ örneğinizi kullanabilirsiniz. Sanal ağ ve alt ağ zaten oluşturulup hazırlandı.
>
>

1. Azure portal, **Parametreler** dikey penceresinde, **Neworexistingsubnet** kutusunda **var**' ı seçin.
2. **SubnetID** kutusunda, Azure sanal makinelerinizi dağıtmayı planladığınız, hazırlanmış Azure Network SubnetID 'nizin tam dizesini ekleyin.
3. Tüm Azure ağ alt ağlarının listesini almak için şu PowerShell komutunu çalıştırın:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
   ```

   **Kimlik** alanı, **SubnetID**'yi gösterir.
4. Tüm **SubnetID** değerlerinin listesini almak Için Şu PowerShell komutunu çalıştırın:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
   ```

   **SubnetID** şuna benzer:

   ```
   /subscriptions/<SubscriptionId>/resourceGroups/<VPNName>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<SubnetName>
   ```

### <a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a>Test ve tanıtım için yalnızca bulutta SAP örnekleri dağıtın
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

* [Azure Market görüntüsü](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Yönetilen diskleri kullanarak Azure Marketi görüntüsü](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged-md)  
* [Özel görüntü](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)
* [Yönetilen diskleri kullanan özel görüntü](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged-md)


### <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Altyapıyı mimari Şablon 3 için hazırlama

Altyapıyı hazırlayabilir ve **Çoklu SID**için SAP 'yi yapılandırabilirsiniz. Örneğin, *var olan* bir küme yapılandırmasına ek BIR SAP ascs/SCS örneği ekleyebilirsiniz. Daha fazla bilgi için, [Azure Resource Manager ' de SAP çoklu SID yapılandırması oluşturmak üzere mevcut bir küme yapılandırmasına ek SAP ASCS/SCS örneği yapılandırma][sap-ha-multi-sid-guide]konusuna bakın.

Yeni bir çoklu SID kümesi oluşturmak istiyorsanız [GitHub 'da çoklu SID hızlı başlangıç şablonlarını](https://github.com/Azure/azure-quickstart-templates)kullanabilirsiniz.
Yeni bir çoklu SID kümesi oluşturmak için aşağıdaki üç şablonu dağıtmanız gerekir:

* [YOKS/SCS şablonu](#ASCS-SCS-template)
* [Veritabanı şablonu](#database-template)
* [Uygulama sunucuları şablonu](#application-servers-template)

Aşağıdaki bölümlerde, şablonlarda sağlamanız gereken şablonlar ve parametreler hakkında daha fazla ayrıntı verilmektedir.

#### <a name="ASCS-SCS-template"></a>YOKS/SCS şablonu

YOKS/SCS şablonu, birden fazla ASCS/SCS örneğini barındıran bir Windows Server yük devretme kümesi oluşturmak için kullanabileceğiniz iki sanal makine dağıtır.

Ascs/SCS çok düzeyli şablonunu ayarlamak için, [Ass/SCS çok düzeyli şablonda][sap-templates-3-tier-multisid-xscs-marketplace-image] veya [yönetilen diskleri kullanan ascs/SCS çoklu SID][sap-templates-3-tier-multisid-xscs-marketplace-image-md]şablonunda aşağıdaki parametrelerin değerlerini girin:

  - **Kaynak ön eki**.  Dağıtım sırasında oluşturulan tüm kaynakların ön eki için kullanılan kaynak önekini ayarlayın. Kaynaklar yalnızca bir SAP sistemine ait olmadığından, kaynağın ön eki bir SAP sisteminin SID 'SI değildir.  Ön ek **üç ve altı karakter**arasında olmalıdır.
  - **Yığın türü**. SAP sisteminin yığın türünü seçin. Yığın türüne bağlı olarak, Azure Load Balancer SAP sistemine göre yalnızca bir (ABAP veya Java) veya iki (ABAP + Java) özel IP adresi vardır.
  -  **Işletim sistemi türü**. Sanal makinelerin işletim sistemini seçin.
  -  **SAP sistem sayısı**. Bu kümeye yüklemek istediğiniz SAP sistemi sayısını seçin.
  -  **Sistem kullanılabilirliği**. **Ha**'yi seçin.
  -  **Yönetici Kullanıcı adı ve yönetici parolası**. Makinede oturum açmak için kullanılabilecek yeni bir kullanıcı oluşturun.
  -  **Yeni veya var olan alt ağ**. Yeni bir sanal ağın ve alt ağın oluşturulması gerekip gerekmediğini veya mevcut bir alt ağın kullanılıp kullanılmayacağını ayarlayın. Şirket içi ağınıza bağlı bir sanal ağınız zaten varsa, **mevcut**' ı seçin.
  -  **Alt ağ kimliği**. VM 'yi tanımlanmış VM 'ye atanmış bir alt ağa sahip olduğunuz mevcut bir VNet 'e dağıtmak istiyorsanız, söz konusu alt ağın KIMLIĞINI adlandırın. KIMLIK genellikle şöyle görünür:/Subscriptions/<*abonelik kimliği*>/resourceGroups/<*kaynak grubu adı*>/Providers/Microsoft.Network/virtualNetworks/<*sanal ağ adı*>/Subnets/<*alt ağı ad*>

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
- Linux 'ta yoks bağlantı noktaları (örnek numarası x2): 33x2, 5x213, 5x214, 5x216
- Linux 'ta SCS 'ler bağlantı noktaları (örnek numarası x3): 33x3, 5x313, 5x314, 5x316

Yük dengeleyici, aşağıdaki araştırma bağlantı noktalarını kullanacak şekilde yapılandırılır (burada x, SAP sisteminin numarasıdır, örneğin, 1, 2, 3...):
- YOKS/SCS iç yük dengeleyici araştırma bağlantı noktası: 620x0
- ERS iç yük dengeleyici yoklama bağlantı noktası (yalnızca Linux): 621x2

#### <a name="database-template"></a>Veritabanı şablonu

Veritabanı şablonu, bir SAP sistemine yönelik ilişkisel veritabanı yönetim sistemini (RDBMS) yüklemek için kullanabileceğiniz bir veya iki sanal makine dağıtır. Örneğin, beş SAP sistemi için bir ASCS/SCS şablonu dağıtırsanız, bu şablonu beş kez dağıtmanız gerekir.

Veritabanı çoklu SID şablonunu ayarlamak için, [veritabanı çok][sap-templates-3-tier-multisid-db-marketplace-image] düzeyli şablonunda veya [yönetilen diskleri kullanarak veritabanı çoklu SID şablonunda][sap-templates-3-tier-multisid-db-marketplace-image-md], aşağıdaki parametrelerin değerlerini girin:

- **SAP sistem kimliği**. Yüklemek istediğiniz SAP sisteminin SAP sistem KIMLIĞINI girin. KIMLIK, dağıtılan kaynaklar için bir ön ek olarak kullanılacaktır.
- **Işletim sistemi türü**. Sanal makinelerin işletim sistemini seçin.
- **DbType**. Kümeye yüklemek istediğiniz veritabanının türünü seçin. Microsoft SQL Server yüklemek istiyorsanız **SQL** ' i seçin. SAP HANA sanal makinelere yüklemeyi planlıyorsanız **Hana** ' yı seçin. Doğru işletim sistemi türünü seçtiğinizden emin olun: SQL için **Windows** ' u SEÇIN ve Hana Için bir Linux dağıtımı seçin. Sanal makinelere bağlı Azure Load Balancer seçili veritabanı türünü destekleyecek şekilde yapılandırılacak:
  * **SQL**. Yük dengeleyici 1433 numaralı bağlantı noktasını yük dengelemeye çalışır. SQL Server her zaman kurulum üzerinde bu bağlantı noktasını kullandığınızdan emin olun.
  * **HANA**. Yük dengeleyici 35015 ve 35017 bağlantı noktalarını yük dengelemeye çalışır. **50**örnek numarasıyla SAP HANA yüklediğinizden emin olun.
  Yük dengeleyici, 62550 araştırma bağlantı noktasını kullanır.
- **SAP sistem boyutu**. Yeni sistemin sunmayacak olan SAPS sayısını ayarlayın. Sisteme kaç tane SAPS isteyeceğiz emin değilseniz, SAP Technology Iş ortağınızdan veya sistem tümleştirmenize sorun.
- **Sistem kullanılabilirliği**. **Ha**'yi seçin.
- **Yönetici Kullanıcı adı ve yönetici parolası**. Makinede oturum açmak için kullanılabilecek yeni bir kullanıcı oluşturun.
- **Alt ağ kimliği**. ASCS/SCS şablonunun dağıtımı sırasında kullandığınız alt ağın KIMLIĞINI veya Ass/SCS şablon dağıtımının bir parçası olarak oluşturulan alt ağın KIMLIĞINI girin.

#### <a name="application-servers-template"></a>Uygulama sunucuları şablonu

Uygulama sunucuları şablonu, bir SAP sistemi için SAP uygulama sunucusu örnekleri olarak kullanılabilecek iki veya daha fazla sanal makine dağıtır. Örneğin, beş SAP sistemi için bir ASCS/SCS şablonu dağıtırsanız, bu şablonu beş kez dağıtmanız gerekir.

Uygulama sunucuları çoklu SID şablonu ayarlamak için, [uygulama sunucuları çok düzeyli şablon][sap-templates-3-tier-multisid-apps-marketplace-image] veya [uygulama SUNUCULARı birden çok SID şablonu yönetilen diskleri kullanarak][sap-templates-3-tier-multisid-apps-marketplace-image-md], aşağıdaki parametreler için değerler girin:

  -  **SAP sistem kimliği**. Yüklemek istediğiniz SAP sisteminin SAP sistem KIMLIĞINI girin. KIMLIK, dağıtılan kaynaklar için bir ön ek olarak kullanılacaktır.
  -  **Işletim sistemi türü**. Sanal makinelerin işletim sistemini seçin.
  -  **SAP sistem boyutu**. Yeni sistemin sunacaktır SAPS sayısı. Sisteme kaç tane SAPS isteyeceğiz emin değilseniz, SAP Technology Iş ortağınızdan veya sistem tümleştirmenize sorun.
  -  **Sistem kullanılabilirliği**. **Ha**'yi seçin.
  -  **Yönetici Kullanıcı adı ve yönetici parolası**. Makinede oturum açmak için kullanılabilecek yeni bir kullanıcı oluşturun.
  -  **Alt ağ kimliği**. ASCS/SCS şablonunun dağıtımı sırasında kullandığınız alt ağın KIMLIĞINI veya Ass/SCS şablon dağıtımının bir parçası olarak oluşturulan alt ağın KIMLIĞINI girin.


### <a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a>Azure sanal ağı
Örneğimizde, Azure sanal ağının adres alanı 10.0.0.0/16 ' dır. Adres aralığı 10.0.0.0/24 olan **alt ağ**adlı bir alt ağ vardır. Tüm sanal makineler ve iç yük dengeleyiciler bu sanal ağda dağıtılır.

> [!IMPORTANT]
> Konuk işletim sistemi içindeki ağ ayarlarında herhangi bir değişiklik yapmayın. Buna IP adresleri, DNS sunucuları ve alt ağ dahildir. Tüm ağ ayarlarınızı Azure 'da yapılandırın. Dinamik ana bilgisayar Yapılandırma Protokolü (DHCP) hizmeti, ayarlarınızı yayar.
>
>

### <a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a>DNS IP adresleri

Gerekli DNS IP adreslerini ayarlamak için aşağıdaki adımları uygulayın.

1. Azure portal, **DNS sunucuları** dikey penceresinde, sanal ağ **DNS SUNUCULARıNıZ** seçeneğinin **özel DNS**olarak ayarlandığından emin olun.
2. Sahip olduğunuz ağ türüne göre ayarlarınızı seçin. Daha fazla bilgi için aşağıdaki kaynaklara bakın:
   * [Şirket ağı bağlantısı (Şirket içi)][planning-guide-2.2]: Şirket içi DNS sunucularının IP adreslerini ekleyin.  
   Şirket içi DNS sunucularını Azure 'da çalışan sanal makinelere genişletebilirsiniz. Bu senaryoda, DNS hizmetini çalıştırdığınız Azure sanal makinelerinin IP adreslerini ekleyebilirsiniz.
   * Azure 'da yalıtılmış VM dağıtımları için: Aynı sanal ağ örneğine bir DNS sunucusu olarak hizmet veren ek bir sanal makine dağıtın. DNS hizmetini çalıştırmak için ayarladığınız Azure sanal makinelerinin IP adreslerini ekleyin.

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

### <a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a>SAP ASCS/SCS kümelenmiş örneği ve DBMS kümelenmiş örneği için ana bilgisayar adları ve statik IP adresleri

Şirket içi dağıtım için, bu ayrılmış ana bilgisayar adlarına ve IP adreslerine ihtiyacınız vardır:

| Sanal konak adı rolü | Sanal konak adı | Sanal statik IP adresi |
| --- | --- | --- |
| SAP ASCS/SCS ilk küme sanal ana bilgisayar adı (küme yönetimi için) |PR1-ascs-Vir |10.0.0.42 |
| SAP ASCS/SCS örneği sanal ana bilgisayar adı |PR1-ascs-SAP |10.0.0.43 |
| SAP DBMS ikinci küme sanal ana bilgisayar adı (küme yönetimi) |pr1-dbms-vir |10.0.0.32 |

Kümeyi oluşturduğunuzda, **PR1-ascs-Vir** ve **PR1-DBMS-Vir** sanal ana bilgisayar adlarını ve KÜMENIN kendisini yöneten ilişkili IP adreslerini oluşturun. Bunun nasıl yapılacağı hakkında bilgi için bkz. küme [düğümlerini bir küme yapılandırmasında toplama][sap-ha-guide-8.12.1].

Diğer iki sanal ana bilgisayar adını, **PR1-ascs-SAP** ve **PR1-DBMS-SAP**ve DNS sunucusunda ilişkili IP adreslerini el ile oluşturabilirsiniz. Kümelenmiş SAP ASCS/SCS örneği ve kümelenmiş DBMS örneği bu kaynakları kullanır. Bunun nasıl yapılacağı hakkında bilgi için bkz. [KÜMELENMIŞ SAP yoks/SCS örneği için sanal konak adı oluşturma][sap-ha-guide-9.1.1].

### <a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a>SAP sanal makineleri için statik IP adresleri ayarlama
Kümenizde kullanmak üzere sanal makineleri dağıttıktan sonra, tüm sanal makineler için statik IP adresleri ayarlamanız gerekir. Bunu, Konuk işletim sisteminde değil, Azure sanal ağ yapılandırmasında yapın.

1. Azure Portal, **kaynak grubu** > **ağ kartı** > **ayarları** > **IP adresi**' ni seçin.
2. **IP adresleri** dikey penceresinde **atama**altında **statik**' ı seçin. **IP adresi** kutusuna, kullanmak istediğiniz IP adresini girin.

   > [!NOTE]
   > Ağ kartının IP adresini değiştirirseniz, değişikliği uygulamak için Azure sanal makinelerini yeniden başlatmanız gerekir.  
   >
   >

   ![Şekil 13: Her bir sanal makinenin ağ kartı için statik IP adresleri ayarla][sap-ha-guide-figure-3002]

   _**Şekil 13:** Her bir sanal makinenin ağ kartı için statik IP adresleri ayarla_

   Active Directory/DNS hizmetiniz için kullanmak istediğiniz sanal makineler de dahil olmak üzere tüm ağ arabirimleri için bu adımı tekrarlayın.

Örneğimizde şu sanal makineler ve statik IP adresleri vardır:

| Sanal makine rolü | Sanal makine konak adı | Ağ kartı adı | Statik IP adresi |
| --- | --- | --- | --- |
| İlk SAP uygulama sunucusu örneği |PR1-dı-0 |PR1-NIC-dı-0 |10.0.0.50 |
| İkinci SAP uygulama sunucusu örneği |PR1-dı-1 |PR1-NIC-dı-1 |10.0.0.51 |
| ... |... |... |... |
| Son SAP uygulama sunucusu örneği |PR1-dı-5 |pr1-nic-di-5 |10.0.0.55 |
| ASCS/SCS örneği için ilk küme düğümü |PR1-ascs-0 |PR1-NIC-ascs-0 |10.0.0.40 |
| ASCS/SCS örneği için ikinci küme düğümü |PR1-ascs-1 |PR1-NIC-ascs-1 |10.0.0.41 |
| DBMS örneği için ilk küme düğümü |PR1-DB-0 |PR1-NIC-DB-0 |10.0.0.30 |
| DBMS örneği için ikinci küme düğümü |PR1-DB-1 |PR1-NIC-DB-1 |10.0.0.31 |

### <a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a>Azure iç yük dengeleyici için statik IP adresi ayarlama

SAP Azure Resource Manager şablonu, SAP ASCS/SCS örneği kümesi ve DBMS kümesi için kullanılan bir Azure iç yük dengeleyici oluşturur.

> [!IMPORTANT]
> SAP ASCS/SCS 'nin sanal ana bilgisayar adının IP adresi, SAP ASCS/SCS iç yük dengeleyicinin IP adresiyle aynıdır: **PR1-lb-ascs**.
> DBMS 'nin sanal adının IP adresi, DBMS iç yük dengeleyicinin IP adresiyle aynıdır: **PR1-lb-DBMS**.
>
>

Azure iç yük dengeleyici için bir statik IP adresi ayarlamak için:

1. İlk dağıtım, iç yük dengeleyici IP adresini **dinamik**olarak ayarlar. Azure portal, **IP adresleri** dikey penceresindeki **atama**altında **statik**' ı seçin.
2. **PR1-lb-ascs** iç yük dengeleyicinin IP adresini SAP ascs/SCS örneğinin sanal ana BILGISAYAR adının IP adresine ayarlayın.
3. İç yük dengeleyici **PR1-lb-DBMS** IP ADRESINI, DBMS örneğinin sanal ana BILGISAYAR adının IP adresine ayarlayın.

   ![Şekil 14: SAP ASCS/SCS örneği için iç yük dengeleyici için statik IP adresleri ayarlama][sap-ha-guide-figure-3003]

   _**Şekil 14:** SAP ASCS/SCS örneği için iç yük dengeleyici için statik IP adresleri ayarlama_

Örneğimizde, şu statik IP adreslerine sahip iki Azure iç yük dengeliyoruz:

| Azure iç yük dengeleyici rolü | Azure iç yük dengeleyici adı | Statik IP adresi |
| --- | --- | --- |
| SAP ASCS/SCS örneği iç yük dengeleyici |PR1-lb-yoks |10.0.0.43 |
| SAP DBMS iç yük dengeleyici |PR1-lb-DBMS |10.0.0.33 |


### <a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a>Azure iç yük dengeleyici için varsayılan yoks/SCS Yük Dengeleme kuralları

SAP Azure Resource Manager şablonu, ihtiyacınız olan bağlantı noktalarını oluşturur:
* Varsayılan örnek numarası **00** olan BIR ABAP Ass örneği
* Varsayılan örnek numarası **01** olan BIR Java SCS örneği

SAP ASCS/SCS örneğinizi yüklerken, ABAP ASCS örneğiniz için **00** varsayılan örnek numarasını ve Java SCS örneğiniz için varsayılan örnek numarasını ( **01** ) kullanmanız gerekir.

Ardından, SAP NetWeaver bağlantı noktaları için gerekli iç Yük Dengeleme uç noktaları oluşturun.

Gerekli iç Yük Dengeleme uç noktalarını oluşturmak için, SAP NetWeaver ABAP ASCS bağlantı noktalarında bu yük dengeleme uç noktalarını oluşturun:

| Hizmet/Yük Dengeleme kuralı adı | Varsayılan bağlantı noktası numaraları | Somut bağlantı noktaları (örnek numarası 00 olan ASCS örneği) (10 ile) |
| --- | --- | --- |
| Sıraya alma sunucusu/ *lbrule3200* |32 <*ınstancenumarası*> |3200 |
| ABAP Message sunucusu/ *lbrule3600* |36 <*ınstancenumarası*> |3600 |
| İç ABAP Iletisi/ *lbrule3900* |39 <*ınstancenumarası*> |3900 |
| İleti sunucusu HTTP/ *Lbrule8100* |81 <*ınstancenumarası*> |8100 |
| SAP başlatma hizmeti yoks HTTP/ *Lbrule50013* |5 <*ınstancenumarası*> 13 |50013 |
| SAP başlangıç hizmeti yoks HTTPS/ *Lbrule50014* |5 <*ınstancenumarası*> 14 |50014 |
| Sıraya alma çoğaltması/ *Lbrule50016* |5 <*ınstancenumarası*> 16 |50016 |
| SAP Start Service ERS HTTP *Lbrule51013* |5 <*ınstancenumarası*> 13 |51013 |
| SAP Start Service ERS HTTP *Lbrule51014* |5 <*ınstancenumarası*> 14 |51014 |
| Win RM *Lbrule5985* | |5985 |
| Dosya paylaşma *Lbrule445* | |445 |

_**Tablo 1:** SAP NetWeaver ABAP Ass örneklerinin bağlantı noktası numaraları_

Daha sonra, SAP NetWeaver Java SCS bağlantı noktaları için bu yük dengeleme uç noktalarını oluşturun:

| Hizmet/Yük Dengeleme kuralı adı | Varsayılan bağlantı noktası numaraları | Somut bağlantı noktaları (örneğin, örnek numarası 01 olan SCS örneği) (11 ile ÇÖZÜMLEYICILER) |
| --- | --- | --- |
| Sıraya alma sunucusu/ *lbrule3201* |32 <*ınstancenumarası*> |3201 |
| Ağ Geçidi sunucusu/ *lbrule3301* |33 <*ınstancenumarası*> |3301 |
| Java Ileti sunucusu/ *lbrule3900* |39 <*ınstancenumarası*> |3901 |
| İleti sunucusu HTTP/ *Lbrule8101* |81 <*ınstancenumarası*> |8101 |
| SAP başlangıç hizmeti SCS HTTP/ *Lbrule50113* |5 <*ınstancenumarası*> 13 |50113 |
| SAP başlangıç hizmeti SCS HTTPS/ *Lbrule50114* |5 <*ınstancenumarası*> 14 |50114 |
| Sıraya alma çoğaltması/ *Lbrule50116* |5 <*ınstancenumarası*> 16 |50116 |
| SAP Start Service ERS HTTP *Lbrule51113* |5 <*ınstancenumarası*> 13 |51113 |
| SAP Start Service ERS HTTP *Lbrule51114* |5 <*ınstancenumarası*> 14 |51114 |
| Win RM *Lbrule5985* | |5985 |
| Dosya paylaşma *Lbrule445* | |445 |

_**Tablo 2:** SAP NetWeaver Java SCS örneklerinin bağlantı noktası numaraları_

![Şekil 15: Azure iç yük dengeleyici için varsayılan yoks/SCS Yük Dengeleme kuralları][sap-ha-guide-figure-3004]

_**Şekil 15:** Azure iç yük dengeleyici için varsayılan yoks/SCS Yük Dengeleme kuralları_

Yük dengeleyici **PR1-lb-DBMS** IP ADRESINI, DBMS örneğinin sanal ana BILGISAYAR adının IP adresine ayarlayın.

### <a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a>Azure iç yük dengeleyici için ASCS/SCS varsayılan Yük Dengeleme kurallarını değiştirme

SAP ASCS veya SCS örnekleri için farklı numaralar kullanmak istiyorsanız, bağlantı noktalarının adlarını ve değerlerini varsayılan değerlerle değiştirmeniz gerekir.

1. Azure Portal  **< *SID*>-lb-ascs yük dengeleyici** > **Yük Dengeleme kuralları**' nı seçin.
2. SAP ASCS veya SCS örneğine ait olan tüm yük dengeleme kuralları için şu değerleri değiştirin:

   * Name
   * Port
   * Arka uç bağlantı noktası

   Örneğin, varsayılan ASCS örnek numarasını 00 ' dan 31 ' e değiştirmek istiyorsanız, Tablo 1 ' de listelenen tüm bağlantı noktaları için değişiklikleri yapmanız gerekir.

   Bağlantı noktası *lbrule3200*için bir güncelleştirme örneği aşağıda verilmiştir.

   ![Şekil 16: Azure iç yük dengeleyici için ASCS/SCS varsayılan Yük Dengeleme kurallarını değiştirme][sap-ha-guide-figure-3005]

   _**Şekil 16:** Azure iç yük dengeleyici için ASCS/SCS varsayılan Yük Dengeleme kurallarını değiştirme_

### <a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a>Windows sanal makinelerini etki alanına ekleme

Sanal makinelere statik bir IP adresi atadıktan sonra, sanal makineleri etki alanına ekleyin.

![Şekil 17: Bir etki alanına sanal makine ekleme][sap-ha-guide-figure-3006]

_**Şekil 17:** Bir etki alanına sanal makine ekleme_

### <a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a>SAP ASCS/SCS örneğinin küme düğümlerine kayıt defteri girişleri ekleme

Azure Load Balancer, bağlantılar, belirlenen süre boyunca boşta kaldığında bağlantıları kapatan iç yük dengeleyiciye sahiptir (boşta zaman aşımı). İletişim örneklerinde SAP iş işlemleri, ilk sıraya alma/sıradan çıkarma isteğinin gönderilmesi gerektiği anda SAP sıraya alma işlemine bağlantılar açar. Bu bağlantılar genellikle iş süreci veya sıraya alma işlemi yeniden başlatılana kadar kurulmaya devam eder. Ancak, bağlantı ayarlanan süre boyunca boşta kalırsa, Azure iç yük dengeleyici bağlantıları kapatır. Bu bir sorun değildir çünkü SAP iş işlemi, artık mevcut değilse sıraya alma işlemiyle bağlantıyı yeniden oluşturur. Bu etkinlikler, SAP işlemlerinin geliştirici izlemelerinde belgelenmiştir, ancak bu izlemelerde büyük miktarda ekstra içerik oluşturur. TCP/IP `KeepAliveTime` ve `KeepAliveInterval` her iki küme düğümünde de değişiklik yapmak iyi bir fikirdir. Bu değişiklikleri, makalenin ilerleyen kısımlarında açıklanan SAP profili parametreleriyle TCP/IP parametrelerinde birleştirin.

Yalnızca SAP ASCS/SCS örneğinin küme düğümlerine kayıt defteri girişleri eklemek için, bu Windows kayıt defteri girdilerini SAP Ass/SCS için her iki Windows küme düğümüne de ekleyin:

| `Path` | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Değişken adı |`KeepAliveTime` |
| Değişken türü |REG_DWORD (ondalık) |
| Value |120000 |
| Belge bağlantısı |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

_**Tablo 3:** İlk TCP/IP parametresini değiştirme_

Daha sonra, bu Windows kayıt defteri girdilerini SAP yoks/SCS için Windows küme düğümlerine ekleyin:

| `Path` | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Değişken adı |`KeepAliveInterval` |
| Değişken türü |REG_DWORD (ondalık) |
| Value |120000 |
| Belge bağlantısı |[https://technet.microsoft.com/library/cc957548.aspx](https://technet.microsoft.com/library/cc957548.aspx) |

_**Tablo 4:** İkinci TCP/IP parametresini değiştirme_

**Değişiklikleri uygulamak için her iki küme düğümünü yeniden başlatın**.

### <a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a>SAP ASCS/SCS örneği için Windows Server Yük Devretme Kümelemesi kümesi ayarlama

SAP yoks/SCS örneği için Windows Server Yük Devretme Kümelemesi kümesi ayarlamak şu görevleri içerir:

- Küme düğümlerini bir küme yapılandırmasında toplama
- Küme dosya paylaşma tanığını yapılandırma

#### <a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a>Küme düğümlerini bir küme yapılandırmasında toplayın

1. Rol ve Özellik Ekleme Sihirbazı 'nda, her iki küme düğümüne yük devretme kümelemesi ekleyin.
2. Yük Devretme Kümesi Yöneticisi kullanarak yük devretme kümesini ayarlayın. Yük Devretme Kümesi Yöneticisi, **küme oluştur**' u seçin ve ardından yalnızca ilk kümenin adını, A düğümünü ekleyin. İkinci düğümü henüz eklemeyin; ikinci düğümü sonraki bir adımda ekleyeceksiniz.

   ![Şekil 18: İlk küme düğümünün sunucusunu veya sanal makine adını ekleyin][sap-ha-guide-figure-3007]

   _**Şekil 18:** İlk küme düğümünün sunucusunu veya sanal makine adını ekleyin_

3. Kümenin ağ adını (sanal ana bilgisayar adı) girin.

   ![Şekil 19: Küme adını girin][sap-ha-guide-figure-3008]

   _**Şekil 19:** Küme adını girin_

4. Kümeyi oluşturduktan sonra bir küme doğrulama testi çalıştırın.

   ![Şekil 20: Küme doğrulama denetimini çalıştırın][sap-ha-guide-figure-3009]

   _**Şekil 20:** Küme doğrulama denetimini çalıştırın_

   İşlemdeki bu noktada disklerle ilgili tüm uyarıları yoksayabilirsiniz. Daha sonra bir dosya paylaşım tanığı ve SIOS Paylaşılan diskleri ekleyeceksiniz. Bu aşamada, bir çekirdeğe sahip olmak için endişelenmeniz gerekmez.

   ![Şekil 21: Hiçbir çekirdek disk bulunamadı][sap-ha-guide-figure-3010]

   _**Şekil 21:** Hiçbir çekirdek disk bulunamadı_

   ![Şekil 22: Çekirdek küme kaynağı için yeni bir IP adresi gerekiyor][sap-ha-guide-figure-3011]

   _**Şekil 22:** Çekirdek küme kaynağı için yeni bir IP adresi gerekiyor_

5. Çekirdek küme hizmetinin IP adresini değiştirin. Sunucu IP adresi sanal makine düğümlerinden birine işaret ettiğinden, küme, çekirdek küme hizmetinin IP adresini değiştirene kadar başlayamaz. Bunu, çekirdek küme hizmetinin IP kaynağının **Özellikler** sayfasında yapın.

   Örneğin, **PR1-ascs-Vir**küme sanal ana bilgisayar adı IÇIN bir IP adresi (örneğimizde, **10.0.0.42**) atanmamız gerekiyor.

   ![Şekil 23: Özellikler iletişim kutusunda, IP adresini değiştirme][sap-ha-guide-figure-3012]

   _**Şekil 23:** **Özellikler** iletişim kutusunda, IP adresini değiştirme_

   ![Şekil 24: Küme için ayrılan IP adresini atama][sap-ha-guide-figure-3013]

   _**Şekil 24:** Küme için ayrılan IP adresini atama_

6. Küme sanal ana bilgisayar adını çevrimiçi duruma getirin.

   ![Şekil 25: Küme çekirdeği hizmeti çalışır duruma sahip ve doğru IP adresi ile][sap-ha-guide-figure-3014]

   _**Şekil 25:** Küme çekirdeği hizmeti çalışır duruma sahip ve doğru IP adresi ile_

7. İkinci küme düğümünü ekleyin.

   Artık çekirdek küme hizmeti çalışır durumda olduğundan, ikinci küme düğümünü ekleyebilirsiniz.

   ![Şekil 26: İkinci küme düğümünü ekleme][sap-ha-guide-figure-3015]

   _**Şekil 26:** İkinci küme düğümünü ekleme_

8. İkinci küme düğümü konağı için bir ad girin.

   ![Şekil 27: İkinci küme düğümü ana bilgisayar adını girin][sap-ha-guide-figure-3016]

   _**Şekil 27:** İkinci küme düğümü ana bilgisayar adını girin_

   > [!IMPORTANT]
   > **Kümeye uygun tüm depolamayı Ekle** onay kutusunun seçili **olmadığından** emin olun.  
   >
   >

   ![Şekil 28: Onay kutusunu seçmeyin][sap-ha-guide-figure-3017]

   _**Şekil 28:** Onay kutusunu seçmeyin_

   Çekirdek ve disklerle ilgili uyarıları yoksayabilirsiniz. [SAP yoks/SCS küme paylaşma diski IÇIN SIOS Dataman küme sürümü][sap-ha-guide-8.12.3]' nde açıklandığı gibi çekirdeği ayarlar ve diski daha sonra paylaşabilirsiniz.

   ![Şekil 29: Disk çekirdeği hakkındaki uyarıları yoksay][sap-ha-guide-figure-3018]

   _**Şekil 29:** Disk çekirdeği hakkındaki uyarıları yoksay_


#### <a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a>Küme dosyası paylaşma tanığını yapılandırma

Küme dosya paylaşımının tanığını yapılandırmak şu görevleri içerir:

- Dosya paylaşma oluşturma
- Yük Devretme Kümesi Yöneticisi 'de dosya paylaşma tanık çekirdeğini ayarlama

##### <a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a>Dosya paylaşma oluşturma

1. Çekirdek disk yerine bir dosya paylaşma tanığı seçin. SIOS Dataman bu seçeneği destekler.

   Bu makaledeki örneklerde, dosya paylaşma tanığı Azure 'da çalışan Active Directory/DNS sunucusudur. Dosya paylaşımının tanığı, **domcontenr-0**olarak adlandırılır. Azure 'a bir VPN bağlantısı yapılandırdığınıza (siteden siteye VPN veya Azure ExpressRoute aracılığıyla), Active Directory/DNS hizmetiniz şirket içi ve dosya paylaşma tanığı çalıştırmak için uygun değildir.

   > [!NOTE]
   > Active Directory/DNS hizmetiniz yalnızca şirket içi çalıştırıyorsa, dosya paylaşma tanığını şirket içinde çalışan Active Directory/DNS Windows işletim sisteminde yapılandırmayın. Azure 'da çalışan küme düğümleri ve şirket içi Active Directory/DNS arasındaki ağ gecikmesi çok büyük olabilir ve bağlantı sorunlarına yol açabilir. Dosya paylaşımının tanığını, küme düğümüne kapat çalıştıran bir Azure sanal makinesinde yapılandırmayı unutmayın.  
   >
   >

   Çekirdek sürücüde en az 1.024 MB boş alan gerekir. Çekirdek sürücü için 2.048 MB boş alan öneririz.

2. Küme adı nesnesini ekleyin.

   ![Şekil 30: Küme adı nesnesi için paylaşımdaki izinleri atama][sap-ha-guide-figure-3019]

   _**Şekil 30:** Küme adı nesnesi için paylaşımdaki izinleri atama_

   İzinlerin küme adı nesnesi için paylaşımdaki verileri değiştirme yetkisini (bizim örneğimizde, **PR1-ascs-Vir $** ) içerdiğinden emin olun.

3. Küme adı nesnesini listeye eklemek için **Ekle**' yi seçin. Şekil 31 ' de gösterilenler 'in yanı sıra, bilgisayar nesnelerini denetlemek için filtreyi değiştirin.

   ![Şekil 31: Nesne türlerini bilgisayarları içerecek şekilde değiştirme][sap-ha-guide-figure-3020]

   _**Şekil 31:** Nesne türlerini bilgisayarları içerecek şekilde değiştirme_

   ![Şekil 32: Bilgisayarlar onay kutusunu seçin][sap-ha-guide-figure-3021]

   _**Şekil 32:** **Bilgisayarlar** onay kutusunu seçin_

4. Şekil 31 ' de gösterildiği gibi küme adı nesnesini girin. Kayıt zaten oluşturulduğundan, izinleri Şekil 30 ' da gösterildiği gibi değiştirebilirsiniz.

5. Paylaşımın **güvenlik** sekmesini seçin ve ardından küme adı nesnesi için daha ayrıntılı izinler ayarlayın.

   ![Şekil 33: Dosya paylaşımında küme adı nesnesinin güvenlik özniteliklerini ayarlama][sap-ha-guide-figure-3022]

   _**Şekil 33:** Dosya paylaşımında küme adı nesnesinin güvenlik özniteliklerini ayarlama_

##### <a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a>Yük Devretme Kümesi Yöneticisi 'de dosya paylaşma tanık çekirdeğini ayarlama

1. Çekirdek ayarlarını yapılandırma Sihirbazı 'Nı açın.

   ![Şekil 34: Küme çekirdek ayarlarını yapılandırma Sihirbazı 'Nı başlatma][sap-ha-guide-figure-3023]

   _**Şekil 34:** Küme çekirdek ayarlarını yapılandırma Sihirbazı 'Nı başlatma_

2. **Çekirdek yapılandırması seçin** sayfasında, **çekirdek tanığını Seç**' i seçin.

   ![Şekil 35: Aralarından seçim yapabileceğiniz çekirdek yapılandırma][sap-ha-guide-figure-3024]

   _**Şekil 35:** Aralarından seçim yapabileceğiniz çekirdek yapılandırma_

3. **Çekirdek tanığı seçin** sayfasında, **dosya paylaşma tanığını Yapılandır**' ı seçin.

   ![Şekil 36: Dosya paylaşma tanığını seçin][sap-ha-guide-figure-3025]

   _**Şekil 36:** Dosya paylaşma tanığını seçin_

4. Dosya paylaşımının UNC yolunu (örneğimizde, \\domcontr-0\FSW) girin. Yapabileceğiniz değişikliklerin listesini görmek için **İleri**' yi seçin.

   ![Şekil 37: Tanık paylaşımının dosya paylaşma konumunu tanımlayın][sap-ha-guide-figure-3026]

   _**Şekil 37:** Tanık paylaşımının dosya paylaşma konumunu tanımlayın_

5. İstediğiniz değişiklikleri seçin ve ardından **İleri**' yi seçin. Şekil 38 ' de gösterildiği gibi küme yapılandırmasını başarıyla yeniden yapılandırmanız gerekir.  

   ![Şekil 38: Kümeyi yeniden yapılandırdığınızı onaylama][sap-ha-guide-figure-3027]

   _**Şekil 38:** Kümeyi yeniden yapılandırdığınızı onaylama_

Windows Yük devretme kümesini başarılı bir şekilde yükledikten sonra, yük devretme algılamayı Azure 'daki koşullara uyarlamak için bazı eşiklere değişiklik yapılması gerekir. Değiştirilecek Parametreler bu blogda belgelenmiştir: https://blogs.msdn.microsoft.com/clustering/2012/11/21/tuning-failover-cluster-network-thresholds/ . ASCS/SCS için Windows kümesi yapılandırmasını oluşturan iki VM 'nizin aynı alt ağda olduğunu varsayarsak, aşağıdaki parametrelerin bu değerlere değiştirilmesi gerekir:
- SameSubNetDelay = 2
- SameSubNetThreshold = 15

Bu ayarlar müşterilerle test edilmiştir ve bir tarafta yeterince dayanıklı olması için iyi bir uzlaşma sağlanması sağladı. Diğer taraftan, bu ayarlar SAP yazılımı veya düğüm/VM hatasında gerçek hata koşullarında yeterince hızlı yük devretme sağlamamıştı. 

### <a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a>SAP ASCS/SCS küme paylaşma diski için SIOS Dataman küme sürümünü yükleyin

Artık Azure 'da çalışan bir Windows Server Yük Devretme Kümelemesi yapılandırmasına sahipsiniz. Ancak, bir SAP ASCS/SCS örneği yüklemek için, paylaşılan bir disk kaynağınız olması gerekir. Azure 'da ihtiyacınız olan paylaşılan disk kaynaklarını oluşturamazsınız. SIOS Dataman küme sürümü, paylaşılan disk kaynakları oluşturmak için kullanabileceğiniz bir üçüncü taraf çözümüdür.

SAP ASCS/SCS küme paylaşma diski için SIOS Dataman küme sürümü yüklemesi şu görevleri içerir:

- 3,5 .NET Framework ekleniyor
- SIOS veri Man 'ı yükleme
- SIOS veri Man 'ı ayarlama

#### <a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a>3,5 .NET Framework ekleyin
Microsoft .NET Framework 3,5, Windows Server 2012 R2 'de otomatik olarak etkinleştirilmez veya yüklenmez. , SIOS veri Man 'ı, .NET Framework veri sahibi yüklediğiniz tüm düğümlerde olmasını gerektirdiğinden, kümedeki tüm sanal makinelerin Konuk işletim sistemine 3,5 .NET Framework yüklenmesi gerekir.

3,5 .NET Framework eklemenin iki yolu vardır:

- Şekil 39 ' de gösterildiği gibi Windows 'daki rol ve Özellik Ekleme Sihirbazı ' nı kullanın.

  ![Şekil 39: Rol ve Özellik Ekleme Sihirbazı 'Nı kullanarak .NET Framework 3,5 ' ü yükler][sap-ha-guide-figure-3028]

  _**Şekil 39:** Rol ve Özellik Ekleme Sihirbazı 'Nı kullanarak .NET Framework 3,5 ' ü yükler_

  ![Şekil 40: Rol ve Özellik Ekleme Sihirbazı 'Nı kullanarak .NET Framework 3,5 yüklediğinizde yükleme ilerleme çubuğu][sap-ha-guide-figure-3029]

  _**Şekil 40:** Rol ve Özellik Ekleme Sihirbazı 'Nı kullanarak .NET Framework 3,5 yüklediğinizde yükleme ilerleme çubuğu_

- Dism. exe komut satırı aracını kullanın. Bu yükleme türü için, Windows yükleme medyasında SxS dizinine erişmeniz gerekir. Yükseltilmiş bir komut isteminde şunu yazın:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

#### <a name="dd41d5a2-8083-415b-9878-839652812102"></a>SIOS Dataman 'ı yükler

Kümedeki her düğüme SIOS Dataman küme sürümü yükleyin. Bir sanal paylaşılan depolama alanını, SIOS \ veri Man ile oluşturmak için eşitlenmiş bir yansıtma oluşturun ve küme paylaşılan depolama benzetimi yapın.

SIOS yazılımını yüklemeden önce, Kullanıcı **veri birimi Persvc**etki alanını oluşturun.

> [!NOTE]
> Data, **Persvc** kullanıcısını her iki küme düğümünde **yerel yönetici** grubuna ekleyin.
>
>

SIOS Dataman 'ı yüklemek için:

1. Her iki küme düğümüne da SIOS yazılımını yükler.

   ![SIOS yükleyicisi][sap-ha-guide-figure-3030]

   ![Şekil 41: SIOS Dataman yüklemesinin ilk sayfası][sap-ha-guide-figure-3031]

   _**Şekil 41:** SIOS Dataman yüklemesinin ilk sayfası_

2. Şekil 42 ' de gösterilen iletişim kutusunda **Evet**' i seçin.

   ![Şekil 42: Dataman, bir hizmetin devre dışı bırakılacağını bildirir][sap-ha-guide-figure-3032]

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

#### <a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a>SIOS Dataman 'ı ayarlama

Her iki düğüme de SIOS veri Man 'ı yükledikten sonra yapılandırmayı başlatmanız gerekir. Yapılandırmanın amacı, sanal makinelerin her birine eklenen ek diskler arasında zaman uyumlu veri çoğaltmasına sahip olmaktır.

1. Dataman yönetimi ve yapılandırma aracını başlatın ve ardından **sunucuyu bağla**' yı seçin. (Şekil 46 ' de bu seçenek, daire içinde kırmızı renkte olur.)

   ![Şekil 46: SIOS Dataman yönetimi ve yapılandırma aracı][sap-ha-guide-figure-3036]

   _**Şekil 46:** SIOS Dataman yönetimi ve yapılandırma aracı_

2. Yönetim ve yapılandırma aracının bağlanması gereken ilk düğümün adını veya TCP/IP adresini ve ikinci bir adımda ikinci düğümü girin.

   ![Şekil 47: Yönetim ve yapılandırma aracının bağlanması gereken ilk düğümün adını veya TCP/IP adresini, ikinci bir adımda ise ikinci düğümü ekleyin][sap-ha-guide-figure-3037]

   _**Şekil 47:** Yönetim ve yapılandırma aracının bağlanması gereken ilk düğümün adını veya TCP/IP adresini, ikinci bir adımda ise ikinci düğümü ekleyin_

3. İki düğüm arasında çoğaltma işi oluşturun.

   ![Şekil 48: Çoğaltma işi oluşturma][sap-ha-guide-figure-3038]

   _**Şekil 48:** Çoğaltma işi oluşturma_

   Bir sihirbaz, çoğaltma işi oluşturma sürecinde size rehberlik eder.
4. Kaynak düğümün adını, TCP/IP adresini ve disk birimini tanımlayın.

   ![Şekil 49: Çoğaltma işinin adını tanımlayın][sap-ha-guide-figure-3039]

   _**Şekil 49:** Çoğaltma işinin adını tanımlayın_

   ![Şekil 50: Düğüm için, geçerli kaynak düğümü olması gereken temel verileri tanımlayın][sap-ha-guide-figure-3040]

   _**Şekil 50:** Düğüm için, geçerli kaynak düğümü olması gereken temel verileri tanımlayın_

5. Hedef düğümün adını, TCP/IP adresini ve disk birimini tanımlayın.

   ![Şekil 51: Düğüm için, geçerli hedef düğüm olması gereken temel verileri tanımlayın][sap-ha-guide-figure-3041]

   _**Şekil 51:** Düğüm için, geçerli hedef düğüm olması gereken temel verileri tanımlayın_

6. Sıkıştırma algoritmalarını tanımlayın. Örneğimizde, çoğaltma akışını sıkıştırmanız önerilir. Özellikle yeniden eşitleme durumlarında, çoğaltma akışının sıkıştırılması, yeniden eşitleme süresini önemli ölçüde azaltır. Sıkıştırmanın bir sanal makinenin CPU ve RAM kaynaklarını kullandığını unutmayın. Sıkıştırma hızı arttıkça, CPU kaynakları birimi kullanılır. Bu ayarı daha sonra da ayarlayabilirsiniz.

7. Kopyalamanın zaman uyumsuz olarak mı yoksa zaman uyumlu mı oluştuğunu denetlemeniz gereken başka bir ayar. *SAP ASCS/SCS yapılandırmasını koruduğunuzda, zaman uyumlu çoğaltma kullanmanız gerekir*.  

   ![Şekil 52: Çoğaltma ayrıntılarını tanımlama][sap-ha-guide-figure-3042]

   _**Şekil 52:** Çoğaltma ayrıntılarını tanımlama_

8. Çoğaltma işi tarafından çoğaltılan birimin paylaşılan disk olarak bir Windows Server Yük Devretme Kümelemesi kümesi yapılandırmasına temsil edilip edilmeyeceğini tanımlayın. SAP ASCS/SCS yapılandırması için, Windows kümesinin çoğaltılan birimi küme birimi olarak kullanabileceği paylaşılan bir disk olarak görmesi için **Evet** ' i seçin.

   ![Şekil 53: Çoğaltılan birimi bir küme birimi olarak ayarlamak için Evet ' i seçin][sap-ha-guide-figure-3043]

   _**Şekil 53:** Çoğaltılan birimi bir küme birimi olarak ayarlamak için **Evet** ' i seçin_

   Birim oluşturulduktan sonra, veri Man Yönetimi ve yapılandırma aracı, çoğaltma işinin etkin olduğunu gösterir.

   ![Şekil 54: SAP yoks/SCS paylaşılan diski için Dataman zaman uyumlu yansıtma etkin][sap-ha-guide-figure-3044]

   _**Şekil 54:** SAP yoks/SCS paylaşılan diski için Dataman zaman uyumlu yansıtma etkin_

   Yük Devretme Kümesi Yöneticisi Şekil 55 ' de gösterildiği gibi artık diski bir Dataman diski olarak gösterir.

   ![Şekil 55: Yük Devretme Kümesi Yöneticisi, Dataman 'ın çoğaltılan diski gösterir][sap-ha-guide-figure-3045]

   _**Şekil 55:** Yük Devretme Kümesi Yöneticisi, Dataman 'ın çoğaltılan diski gösterir_

## <a name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a>SAP NetWeaver sistemini yükler

Kurulum, kullandığınız DBMS sistemine bağlı olarak farklılık gösterdiğinden, DBMS kurulumunu açıklamayamayacağız. Bununla birlikte, DBMS ile ilgili yüksek kullanılabilirliğe sahip olan kaygıları Azure için farklı DBMS satıcıları desteğinin işlevleri ile ilgilendiririz. Örneğin, SQL Server için her zaman açık veya veritabanı yansıtma ve Oracle veritabanları için Oracle Data Guard. Bu makalede kullandığımız senaryoda, DBMS 'ye daha fazla koruma ekleyeceğiz.

Farklı DBMS Hizmetleri, Azure 'da bu tür bir kümelenmiş SAP ASCS/SCS yapılandırmasıyla etkileşen özel bir dikkat edin.

> [!NOTE]
> SAP NetWeaver ABAP Systems, Java sistemleri ve ABAP + Java sistemlerinin yükleme yordamları neredeyse aynıdır. En önemli fark, SAP ABAP sisteminin bir ASCS örneğine sahip olması gerektiğidir. SAP Java sisteminde bir SCS örneği vardır. SAP ABAP + Java sisteminde bir ASCS örneği ve aynı Microsoft yük devretme kümesi grubunda çalışan bir SCS örneği vardır. Her SAP NetWeaver yükleme yığını için herhangi bir yükleme farkı açıkça bahsedilir. Diğer tüm parçaların aynı olduğunu varsayabilirsiniz.  
>
>

### <a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>Yüksek kullanılabilirliğe sahip bir ASCS/SCS örneğiyle SAP 'yi yükler

> [!IMPORTANT]
> Sayfa dosyanızı veri ve yansıtmalı birimlere yerleştirdiğinizden emin olun. Dataman yansıtılmış birimleri desteklemez. Sayfa dosyanızı, varsayılan olan bir Azure sanal makinesinin geçici sürücüsünde bırakabilirsiniz. Henüz orada yoksa, Windows sayfa dosyasını Azure sanal makinenizin D: sürücüsüne taşıyın.
>
>

SAP 'yi yüksek kullanılabilirliğe sahip bir ASCS/SCS örneğiyle yüklemek şu görevleri içerir:

- Kümelenmiş SAP yoks/SCS örneği için sanal ana bilgisayar adı oluşturma
- SAP ilk küme düğümünü yükleme
- ASCS/SCS örneğinin SAP profilini değiştirme
- Araştırma bağlantı noktası ekleme
- Windows Güvenlik Duvarı araştırma bağlantı noktasını açma

#### <a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>Kümelenmiş SAP Ass/SCS örneği için sanal ana bilgisayar adı oluşturma

1. Windows DNS Yöneticisi 'nde, yoks/SCS örneğinin sanal ana bilgisayar adı için bir DNS girişi oluşturun.

   > [!IMPORTANT]
   > Ascs/SCS örneğinin sanal ana bilgisayar adına atadığınız IP adresi, Azure Load Balancer atadığınız IP adresiyle aynı olmalıdır ( **<*SID*>-lb-ascs**).  
   >
   >

   Sanal SAP ASCS/SCS ana bilgisayar adının (**PR1-ascs-SAP**) ıp adresi Azure Load Balancer (**PR1-lb-ascs**) IP adresiyle aynıdır.

   ![Şekil 56: SAP ASCS/SCS kümesi sanal adı ve TCP/IP adresi için DNS girişi tanımlayın][sap-ha-guide-figure-3046]

   _**Şekil 56:** SAP ASCS/SCS kümesi sanal adı ve TCP/IP adresi için DNS girişi tanımlayın_

2. Sanal ana bilgisayar adına atanan IP adresini tanımlamak için **DNS Yöneticisi** > **etki alanı**' nı seçin.

   ![Şekil 57: SAP Ass/SCS küme yapılandırması için yeni sanal ad ve TCP/IP adresi][sap-ha-guide-figure-3047]

   _**Şekil 57:** SAP Ass/SCS küme yapılandırması için yeni sanal ad ve TCP/IP adresi_

#### <a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a>SAP ilk küme düğümünü yükler

1. Küme düğümü A 'da ilk küme düğümü seçeneğini yürütün. Örneğin, **PR1-ascs-0** ana bilgisayarında.
2. Azure iç yük dengeleyici için varsayılan bağlantı noktalarını tutmak üzere şunları seçin:

   * **ABAP sistemi**: **Yoks** örnek numarası **00**
   * **Java sistemi**: **SCS** örnek numarası **01**
   * **ABAP + Java sistemi**: **Ascs** örnek numarası **00** ve **SCS** örnek numarası **01**

   , ABAP Ass örneği ve Java SCS örneği için 01 dışındaki örnek numaralarını kullanmak için, önce Azure 'un [ASG/SCS varsayılan Yük Dengeleme kurallarını değiştirme bölümünde açıklanan Azure iç yük dengeleyici varsayılan Yük Dengeleme kurallarını değiştirmeniz gerekir İç yük dengeleyici][sap-ha-guide-8.9].

Sonraki birkaç görev standart SAP yükleme belgelerinde açıklanmamıştır.

> [!NOTE]
> SAP yükleme belgeleri, ilk ASCS/SCS küme düğümünün nasıl yükleneceğini açıklar.
>
>

#### <a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a>ASCS/SCS örneğinin SAP profilini değiştirme

Yeni bir profil parametresi eklemeniz gerekiyor. Profil parametresi, SAP iş işlemleriyle sıraya alma sunucusu arasındaki bağlantıları çok uzun süre boşta kaldığında kapatmadan önler. [SAP ASCS/SCS örneğinin küme düğümlerine kayıt defteri girişleri ekleme][sap-ha-guide-8.11]' de sorun senaryosunu belirttik. Bu bölümde, bazı temel TCP/IP bağlantı parametrelerinde da iki değişiklik yapılmıştır. İkinci bir adımda, bağlantıların Azure iç yük dengeleyicinin boşta eşiğine ulaşmaması için sıraya `keep_alive` alma sunucusunu bir sinyal gönderecek şekilde ayarlamanız gerekir.

YOKS/SCS örneğinin SAP profilini değiştirmek için:

1. Bu profil parametresini SAP ASCS/SCS örnek profiline ekleyin:

   ```
   enque/encni/set_so_keepalive = true
   ```
   Örneğimizde yol şu şekilde olur:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

   Örneğin, SAP SCS örnek profiline ve karşılık gelen yola:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2. Değişiklikleri uygulamak için SAP ASCS/SCS örneğini yeniden başlatın.

#### <a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a>Araştırma bağlantı noktası ekle

Tüm küme yapılandırmasının Azure Load Balancer ile çalışmasını sağlamak için iç yük dengeleyicinin araştırma işlevini kullanın. Azure iç yük dengeleyici, genellikle katılan sanal makineler arasında eşit olarak gelen iş yükünü dağıtır. Ancak, yalnızca bir örnek etkin olduğundan, bazı küme yapılandırmalarında bu çalışmaz. Diğer örnek pasif ve iş yükünün hiçbirini kabul edemiyor. Araştırma işlevselliği, Azure iç yük dengeleyicinin yalnızca etkin bir örneğe iş atarken çalışmasına yardımcı olur. Araştırma işlevselliğiyle, iç yük dengeleyici hangi örneklerin etkin olduğunu algılayabilir ve sonra yalnızca örneği iş yüküne hedefleyebilir.

Araştırma bağlantı noktası eklemek için:

1. Aşağıdaki PowerShell komutunu çalıştırarak geçerli **Probeport** ayarını denetleyin. Küme yapılandırmasındaki sanal makinelerden birini içinden yürütün.

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
   ```

2. Bir yoklama bağlantı noktası tanımlayın. Varsayılan yoklama bağlantı noktası numarası **0**' dır. Örneğimizde araştırma bağlantı noktası **62000**' i kullanıyoruz.

   ![Şekil 58: Küme yapılandırma araştırması bağlantı noktası varsayılan olarak 0 ' dır][sap-ha-guide-figure-3048]

   _**Şekil 58:** Varsayılan küme yapılandırma araştırması bağlantı noktası 0 ' dır_

   Bağlantı noktası numarası SAP Azure Resource Manager şablonlarında tanımlanmıştır. Bağlantı noktası numarasını PowerShell 'e atayabilirsiniz.

   **SAP <*SID*> IP** kümesi kaynağı Için yeni bir probeport değeri ayarlamak için aşağıdaki PowerShell betiğini çalıştırın. Ortamınız için PowerShell değişkenlerini güncelleştirin. Betik çalıştıktan sonra, değişiklikleri etkinleştirmek için SAP küme grubunu yeniden başlatmanız istenir.

   ```powershell
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

   Sonra Getir **SAP <*SID*>** doğrulayın, küme rolünü **ProbePort** yeni değere ayarlanır.

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

   ```

   ![Şekil 59: Yeni değeri ayarladıktan sonra küme bağlantı noktasını araştırma][sap-ha-guide-figure-3049]

   _**Şekil 59:** Yeni değeri ayarladıktan sonra küme bağlantı noktasını araştırma_

#### <a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a>Windows Güvenlik Duvarı araştırma bağlantı noktasını açın

Her iki küme düğümünde bir Windows Güvenlik Duvarı araştırma bağlantı noktası açmanız gerekir. Windows Güvenlik Duvarı araştırması bağlantı noktasını açmak için aşağıdaki betiği kullanın. Ortamınız için PowerShell değişkenlerini güncelleştirin.

  ```powershell
  $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

**Probeport** **62000**olarak ayarlanır. Artık  **\\\ascsha-clsap\sapmnt** dosya paylaşımıyla, **assha-DBAS**gibi diğer konaklardan erişebilirsiniz.

### <a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a>Veritabanı örneğini yükler

Veritabanı örneğini yüklemek için SAP yükleme belgelerinde açıklanan süreci izleyin.

### <a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a>İkinci küme düğümünü yükler

İkinci kümeyi yüklemek için SAP yükleme kılavuzundaki adımları izleyin.

### <a name="094bc895-31d4-4471-91cc-1513b64e406a"></a>SAP ERS Windows hizmeti örneğinin başlangıç türünü değiştirme

SAP 'nin Windows hizmeti başlangıç türünü, her iki küme düğümünde da **Otomatik (Gecikmeli başlatma)** olarak değiştirin.

![Şekil 60: SAP ERS örneği için hizmet türünü Gecikmeli otomatik olarak değiştirme][sap-ha-guide-figure-3050]

_**Şekil 60:** SAP ERS örneği için hizmet türünü Gecikmeli otomatik olarak değiştirme_

### <a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a>SAP birincil uygulama sunucusunu yükler

PAS 'yi barındırmak için belirlediğiniz sanal makinede*sıd*>-dı-0 < birincil uygulama sunucusu (pas) örneğini yükleyebilirsiniz. Azure veya Dataman 'a özgü ayarlar üzerinde hiçbir bağımlılık yoktur.

### <a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a>SAP ek uygulama sunucusunu yükler

SAP uygulama sunucusu örneğini barındırmak için belirlediğiniz tüm sanal makinelere bir SAP ek uygulama sunucusu (AAS) yükleyebilirsiniz. Örneğin, <*SID*>-dı-1 ile <*SID*>-dı-&lt;n.&gt;

> [!NOTE]
> Bu, yüksek kullanılabilirliğe sahip SAP NetWeaver sisteminin yüklenmesini sonlandırır. Sonra, yük devretme testi ile devam edin.
>


## <a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a>SAP ASCS/SCS örneği yük devretme ve SIOS çoğaltmasını test etme
Yük Devretme Kümesi Yöneticisi ve SIOS Dataman yönetimi ve yapılandırma aracını kullanarak SAP yoks/SCS örneği yük devretmesini ve SIOS disk çoğaltmasını test etmek ve izlemek kolaydır.

### <a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a>SAP ASCS/SCS örneği, küme düğümü A 'da çalışıyor

**SAP PR1** küme grubu, A küme düğümü üzerinde çalışıyor. Örneğin, **PR1-ascs-0**üzerinde. **SAP PR1** küme grubunun bir parçası olan ve ascs/SCS örneğinin kullandığı paylaşılan disk sürücüsünü, A düğümünü Kümelendirmek için atayın.

![Şekil 61: Yük Devretme Kümesi Yöneticisi: SAP < SID > küme grubu A kümesi düğümünde çalışıyor][sap-ha-guide-figure-5000]

_**Şekil 61:** Yük Devretme Kümesi Yöneticisi: SAP <*sıd*> küme grubu A kümesi düğümünde çalışıyor_

SIOS Verilerlik yönetimi ve yapılandırma aracında, paylaşılan disk verilerinin, A küme düğümü üzerindeki kaynak birim sürücüsünden, B küme düğümü üzerindeki hedef birim sürücüsüne zaman uyumlu olarak çoğaltıldığından emin olabilirsiniz. Örneğin, **PR1-ascs-0 [10.0.0.40]** öğesinden **PR1-ascs-1 [10.0.0.41]** arasında çoğaltılır.

![Şekil 62: Jımdataman 'da yerel birimi küme düğümü A 'yı küme düğümüne çoğaltma B][sap-ha-guide-figure-5001]

_**Şekil 62:** Jımdataman 'da yerel birimi küme düğümü A 'yı küme düğümüne çoğaltma B_

### <a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a>A düğümünden B düğümüne yük devretme

1. A küme düğümünden B kümesine SAP <*sıd*> küme grubunun yük devretmesini başlatmak için şu seçeneklerden birini seçin:
   - Yük Devretme Kümesi Yöneticisi kullan  
   - Yük devretme kümesi PowerShell 'i kullanma

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPClusterGroup = "SAP $SAPSID"
   Move-ClusterGroup -Name $SAPClusterGroup

   ```
2. Windows Konuk işletim sisteminde küme düğümünü bir yeniden başlatın (Bu, A düğümünden B düğümüne) SAP <*sıd*> küme grubunun otomatik yük devretmesini başlatır).  
3. Azure portal küme düğümünü A 'dan yeniden başlatın (Bu, A düğümünden B düğümüne) SAP <*sıd*> küme grubunun otomatik yük devretmesini başlatır).  
4. Azure PowerShell kullanarak küme düğümünü yeniden başlatma (Bu, A düğümünden B düğümüne, SAP <*sıd*> küme grubunun otomatik yük devretmesini başlatır).

   Yük devretmeden sonra SAP <*sıd*> küme grubu B küme düğümünde çalışıyor. Örneğin, **PR1-ascs-1**üzerinde çalışıyor.

   ![Şekil 63: Yük Devretme Kümesi Yöneticisi, SAP < SID > kümesi grubu B kümesi düğümünde çalışıyor][sap-ha-guide-figure-5002]

   _**Şekil 63**: Yük Devretme Kümesi Yöneticisi, SAP <*sıd*> kümesi grubu B kümesi düğümünde çalışıyor_

   Paylaşılan disk artık B küme düğümüne takıyor. SIOS Verilerman, küme düğümü B 'deki kaynak birim sürücüsünden verileri, A küme düğümü üzerindeki hedef birim sürücüsüne çoğaltmakta. Örneğin, **PR1-ascs-1 [10.0.0.41]** öğesinden **PR1-ascs-0 [10.0.0.40]** arasında çoğaltılıyor.

   ![Şekil 64: SIOS Dataman, yerel birimi B küme düğümünden A kümesine çoğaltır][sap-ha-guide-figure-5003]

   _**Şekil 64:** SIOS Dataman, yerel birimi B küme düğümünden A kümesine çoğaltır_
