---
title: Azure 'da SAP ASCS/SCS örneği için Windows Yük devretme kümesine ve paylaşılan diske SAP NetWeaver HA 'yi yüklemeyin | Microsoft Docs
description: SAP NetWeaver HA 'yi bir Windows Yük devretme kümesine ve paylaşılan diske bir SAP ASCS/SCS örneği için nasıl yükleyeceğinizi öğrenin.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 6209bcb3-5b20-4845-aa10-1475c576659f
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/12/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 12cfd39cf1358dcef79c3843627f0b45dc688c9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101667943"
---
# <a name="install-sap-netweaver-ha-on-a-windows-failover-cluster-and-shared-disk-for-an-sap-ascsscs-instance-in-azure"></a>Azure 'da SAP ASCS/SCS örneği için Windows Yük devretme kümesine ve paylaşılan diske SAP NetWeaver HA 'yi yüklemeyin

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

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
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-ha-guide-8.9]:high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md

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
[sap-ha-guide-figure-3046-ers2]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ers2-virtual-name-ip.png
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

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md

Bu makalede, bir SAP ASCS/SCS örneğini Kümelendirmek için bir Windows Server yük devretme kümesi ve Küme Paylaşılan diski kullanılarak Azure 'da yüksek kullanılabilirliğe sahip bir SAP sisteminin nasıl yükleneceği ve yapılandırılacağı açıklanmaktadır. [Mimari kılavuzunda açıklandığı gibi: Küme Paylaşılan diski kullanarak bir Windows Yük devretme KÜMESINDE SAP ASCS/SCS örneği kümeleme][sap-high-availability-guide-wsfc-shared-disk]küme paylaşılan *diski* için iki alternatif vardır:

- [Azure paylaşılan diskleri](../../disks-shared.md)
- Kümelenmiş paylaşılan diskin benzetimini yapan yansıtılmış depolama alanı oluşturmak için [SIOS Dataman küme sürümünü](https://us.sios.com/products/datakeeper-cluster/) kullanma 

## <a name="prerequisites"></a>Önkoşullar

Yüklemeye başlamadan önce şu belgeleri gözden geçirin:

* [Mimari Kılavuzu: Küme Paylaşılan diski kullanarak bir Windows Yük devretme kümesinde SAP yoks/SCS örneği oluşturma][sap-high-availability-guide-wsfc-shared-disk]

* [SAP Ass/SCS örneği için Windows Yük devretme kümesi ve paylaşılan disk kullanarak SAP HA için Azure altyapısını hazırlama][sap-high-availability-infrastructure-wsfc-shared-disk]

Bu makaledeki DBMS kurulumunu açıklamamız, çünkü ayarlar kullandığınız DBMS sistemine bağlı olarak değişir. DBMS ile ilgili yüksek kullanılabilirliğe sahip olan sorunların, Azure için farklı DBMS satıcılarının desteklediği işlevlere değinilmesi gerektiğini varsaydık. Oracle veritabanları için SQL Server ve Oracle Data Guard için AlwaysOn veya veritabanı yansıtma örnekleri verilmiştir. DBMS için yüksek kullanılabilirlik senaryoları Bu makalede ele alınmıyor.

Farklı DBMS Hizmetleri, Azure 'daki kümelenmiş SAP Ass veya SCS yapılandırmasıyla etkileşen özel önemli noktalar yoktur.

> [!NOTE]
> SAP NetWeaver ABAP Systems, Java sistemleri ve ABAP + Java sistemlerinin yükleme yordamları neredeyse aynıdır. En önemli fark, SAP ABAP sisteminin bir ASCS örneğine sahip olması gerektiğidir. SAP Java sisteminde bir SCS örneği vardır. SAP ABAP + Java sisteminde bir ASCS örneği ve aynı Microsoft yük devretme kümesi grubunda çalışan bir SCS örneği vardır. Her SAP NetWeaver yükleme yığını için herhangi bir yükleme farkı açıkça bahsedilir. Adımların geri kalanının aynı olduğunu varsayabilirsiniz.  

## <a name="install-sap-with-a-high-availability-ascsscs-instance"></a><a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>Yüksek kullanılabilirliğe sahip bir ASCS/SCS örneğiyle SAP 'yi yükler

> [!IMPORTANT]
> Paylaşılan disk sunmak için SIOS kullanırsanız, sayfa dosyanızı SIOS veri kullanımı yansıtılmış birimlerine yerleştirmeyin. Sayfa dosyanızı, varsayılan olan bir Azure sanal makinesinin geçici sürücüsünde bırakabilirsiniz. Henüz orada yoksa, Windows sayfa dosyasını Azure sanal makinenizin D sürücüsüne taşıyın.  

SAP 'yi yüksek kullanılabilirliğe sahip bir ASCS/SCS örneğiyle yüklemek şu görevleri içerir:

* Kümelenmiş SAP ASCS/SCS örneği için bir sanal ana bilgisayar adı oluşturun.
* İlk küme düğümüne SAP 'yi yükler.
* ASCS/SCS örneğinin SAP profilini değiştirin.
* Araştırma bağlantı noktası ekleyin.
* Windows Güvenlik Duvarı araştırma bağlantı noktasını açın.

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a><a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>Kümelenmiş SAP Ass/SCS örneği için sanal ana bilgisayar adı oluşturma

1. Windows DNS Yöneticisi 'nde, yoks/SCS örneğinin sanal ana bilgisayar adı için bir DNS girişi oluşturun.

   > [!IMPORTANT]
   > ASCS/SCS örneğinin sanal ana bilgisayar adına atadığınız IP adresi, Azure Load Balancer atadığınız IP adresiyle aynı olmalıdır.  

   ![Şekil 1: SAP ASCS/SCS kümesi sanal adı ve TCP/IP adresi için DNS girişi tanımlama][sap-ha-guide-figure-3046]

   _SAP ASCS/SCS kümesi sanal adı ve TCP/IP adresi için DNS girişi tanımlayın_

2. Ayrıca kümelenmiş örnek olan yeni SAP sıraya alma çoğaltma sunucusu 2 ' yi kullanıyorsa, DNS 'de ERS2 için bir sanal ana bilgisayar adı da ayırmanız gerekir. 

   > [!IMPORTANT]
   > ERS2 örneğinin sanal ana bilgisayar adına atadığınız IP adresi, Azure Load Balancer atadığınız ikinci IP adresi olmalıdır.    

   ![Şekil 1A: SAP ASCS/SCS kümesi sanal adı ve TCP/IP adresi için DNS girişi tanımlama][sap-ha-guide-figure-3046-ers2]

   _SAP ERS2 kümesi sanal adı ve TCP/IP adresi için DNS girişi tanımlama_

3. Sanal ana bilgisayar adına atanan IP adresini tanımlamak için **DNS Yöneticisi**  >  **etki alanı**' nı seçin.

   ![Şekil 2: SAP Ass/SCS küme yapılandırması için yeni sanal ad ve TCP/IP adresi][sap-ha-guide-figure-3047]

   _SAP Ass/SCS küme yapılandırması için yeni sanal ad ve TCP/IP adresi_

### <a name="install-the-sap-first-cluster-node"></a><a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> SAP ilk küme düğümünü yükler

1. Küme düğümü A 'da ilk küme düğümü seçeneğini yürütün. şunu seçin:

   * **ABAP sistemi**: **ascs** örnek numarası **00**
   * **Java sistemi**: **SCS** örnek numarası **01**
   * **ABAP + Java sistemi**: **Ass** örnek numarası **00** ve **SCS** örnek numarası **01**

   > [!IMPORTANT]
   > Azure iç yük dengeleyici Yük Dengeleme kurallarında (temel SKU kullanılıyorsa) yapılandırmanın ve seçili SAP örnek numaralarının eşleşmesi gerektiğini unutmayın.

2. Tanımlanan SAP yükleme yordamını izleyin. "Ilk küme düğümü" yükleme başlatma seçeneğinde, "küme paylaşılan disk" öğesini yapılandırma seçeneği olarak seçmek için emin olun.

> [!TIP]
> SAP yükleme belgeleri, ilk ASCS/SCS küme düğümünün nasıl yükleneceğini açıklar.

### <a name="modify-the-sap-profile-of-the-ascsscs-instance"></a><a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> ASCS/SCS örneğinin SAP profilini değiştirme

Sıraya alma çoğaltma sunucusu 1 varsa, aşağıda açıklandığı gibi SAP profili parametresi ekleyin `enque/encni/set_so_keepalive` . Profil parametresi, SAP iş işlemleriyle sıraya alma sunucusu arasındaki bağlantıları çok uzun süre boşta kaldığında kapatmadan önler. SAP parametresi ERS2 için gerekli değildir. 

1. ERS1 kullanılıyorsa, bu profile parametresini SAP ASCS/SCS örnek profiline ekleyin.

   ```
   enque/encni/set_so_keepalive = true
   ```

   Hem ERS1 hem de ERS2 için, `keepalive` işletim sistemi PARAMETRELERININ SAP note [1410736](https://launchpad.support.sap.com/#/notes/1410736)' de açıklandığı gibi ayarlandığından emin olun.   

2. SAP profili parametre değişikliklerini uygulamak için SAP ASCS/SCS örneğini yeniden başlatın.

### <a name="add-a-probe-port"></a><a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> Araştırma bağlantı noktası ekle

Tüm küme yapılandırmasının Azure Load Balancer ile çalışmasını sağlamak için iç yük dengeleyicinin araştırma işlevini kullanın. Azure iç yük dengeleyici, genellikle katılan sanal makineler arasında eşit olarak gelen iş yükünü dağıtır.

Ancak, yalnızca bir örnek etkin olduğundan, bazı küme yapılandırmalarında bu çalışmaz. Diğer örnek pasif ve iş yükünün hiçbirini kabul edemiyor. Bir araştırma işlevselliği, Azure iç yük dengeleyicinin hangi Örneğin etkin olduğunu algılayarak ve yalnızca etkin örneği hedeflemeye yardımcı olur.  

> [!IMPORTANT]
> Bu örnek yapılandırmada, **Probeport** 620 **NR** olarak ayarlanır. **00** NUMARALı BIR SAP ascs örneği için 620 **00**' dır. Yapılandırmayı SAP örnek numaralarınız ve SAP SID 'niz ile eşleşecek şekilde ayarlamanız gerekecektir.

Bir araştırma bağlantı noktası eklemek için, bu PowerShell modülünü küme VM 'lerinden birinde çalıştırın:

- SAP ASC/SCS örneği söz konusu olduğunda 
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID SID -ProbePort 62000
   ```

- ERS2 kullanıyorsanız, kümelenmiş. Kümelenmemiş olduğundan, ERS1 için araştırma bağlantı noktasını yapılandırmaya gerek yoktur.  
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID SID -ProbePort 62001 -IsSAPERSClusteredInstance $True
   ```

 İşlevin kodu `Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource` şöyle görünür:
   ```powershell
    function Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource {

    <#
    .SYNOPSIS 
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.

    .DESCRIPTION
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.
    It will also restart SAP Cluster group (default behavior), to activate the changes. 

    You need to run it on one of the SAP ASCS/SCS Windows cluster nodes.

    Expectation is that SAP group is installed with official SWPM installation tool, which will set default expected naming convention for:
    - SAP Cluster Group:               'SAP $SAPSID'
    - SAP Cluster IP Address Resource: 'SAP $SAPSID IP' 

    .PARAMETER SAPSID 
    SAP SID - 3 characters staring with letter.

    .PARAMETER ProbePort 
    Azure Load Balancer Health Check Probe Port.

    .PARAMETER RestartSAPClusterGroup 
    Optional parameter. Default value is '$True', so SAP cluster group will be restarted to activate the changes.

    .PARAMETER IsSAPERSClusteredInstance 
    Optional parameter.Default value is '$False'.
    If set to $True , then handle clsutered new SAP ERS2 instance.

    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP', and restart the SAP cluster group 'SAP AB1', to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 

    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP'. SAP cluster group 'SAP AB1' IS NOT restarted, therefore changes are NOT active.
    # To activate the changes you need to manualy restart 'SAP AB1' cluster group.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -RestartSAPClusterGroup $False

    .EXAMPLE 
    # Set probe port to 62001, on SAP cluster resource 'SAP AB1 ERS IP'. SAP cluster group 'SAP AB1 ERS' IS restarted, to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -IsSAPERSClusteredInstance $True

    #> 

        [CmdletBinding()]
        param(

            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]  
            [ValidateLength(3,3)]      
            [string]$SAPSID,

            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]        
            [int] $ProbePort,

            [Parameter(Mandatory=$False)] 
            [bool] $RestartSAPClusterGroup = $True,

            [Parameter(Mandatory=$False)] 
            [bool] $IsSAPERSClusteredInstance = $False
        )

        BEGIN{}

        PROCESS{
            try{                                      

                if($IsSAPERSClusteredInstance){
                    #Handle clustered SAP ERS Instance
                    $SAPClusterRoleName = "SAP $SAPSID ERS"
                    $SAPIPresourceName = "SAP $SAPSID ERS IP"            
                }else{
                    #Handle clustered SAP ASCS/SCS Instance
                    $SAPClusterRoleName = "SAP $SAPSID"
                    $SAPIPresourceName = "SAP $SAPSID IP"
                }

                $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
                $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
                $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
                $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
                $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
                $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
                $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value

                $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }
                Write-Output "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" 

                Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter

                Write-Output " "
                Write-Output "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." 
                Write-Output " "
                Write-Output "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." 
                Write-Output " "

                $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}

                Write-Output " "

                if($RestartSAPClusterGroup){
                    Write-Output ""
                    Write-Output "Activating changes..." 

                    Write-Output " "
                    Write-Output "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..."
                    Stop-ClusterResource -Name $SAPIPresourceName
                    sleep 5

                    Write-Output "Starting SAP cluster role '$SAPClusterRoleName' ..."
                    Start-ClusterGroup -Name $SAPClusterRoleName

                    Write-Output "New ProbePort parameter is active." 
                    Write-Output " "

                    Write-Output "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" 
                    Write-Output " " 
                    Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
                }else
                {
                    Write-Output "SAP cluster role '$SAPClusterRoleName' is not restarted, therefore changes are not activated."
                }
            }
            catch{
               Write-Error  $_.Exception.Message
           }
        }
        END {}
    }

   ```

### <a name="open-the-windows-firewall-probe-port"></a><a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a> Windows Güvenlik Duvarı araştırma bağlantı noktasını açın

Her iki küme düğümünde bir Windows Güvenlik Duvarı araştırması bağlantı noktası açın. Windows Güvenlik Duvarı araştırması bağlantı noktasını açmak için aşağıdaki betiği kullanın. Ortamınız için PowerShell değişkenlerini güncelleştirin.  
ERS2 kullanıyorsanız, ERS2 araştırma bağlantı noktası için güvenlik duvarı bağlantı noktasını da açmanız gerekir.  

  ```powershell
    $ProbePort = 62000   # ProbePort of the Azure internal load balancer
    New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

## <a name="install-the-database-instance"></a><a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> Veritabanı örneğini yükler

Veritabanı örneğini yüklemek için SAP yükleme belgelerinde açıklanan işlemi izleyin.

## <a name="install-the-second-cluster-node"></a><a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> İkinci küme düğümünü yükler

İkinci kümeyi yüklemek için, SAP yükleme kılavuzunda açıklanan adımları izleyin.

## <a name="install-the-sap-primary-application-server"></a><a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> SAP birincil uygulama sunucusunu yükler

PAS 'yi barındırmak için belirlediğiniz sanal makinede birincil uygulama sunucusu (PAS) örneği \<SID\> -dı-0 ' yı yükler. Azure üzerinde hiçbir bağımlılık yoktur. SIOS kullanıyorsanız, hiçbir veri ve özel ayar yoktur.

## <a name="install-the-sap-additional-application-server"></a><a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> SAP ek uygulama sunucusunu yükler

SAP uygulama sunucusu örneğini barındırmak için belirlediğiniz tüm sanal makinelere bir SAP ek uygulama sunucusu (AAS) yükleyebilirsiniz. 

## <a name="test-the-sap-ascsscs-instance-failover"></a><a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> SAP ASCS/SCS örneği yük devretmesini test etme

Seviyelendirilmiş yük devretme testleri için SAP Ass 'nin A düğümünde etkin olduğunu varsaytık.  

1. SAP sisteminin A düğümünden A düğümüne başarılı bir şekilde yük devredebildiğini doğrulayın \<SID\> a küme düğümünden b KÜMESINE SAP kümesi grubu yük devretmesi başlatmak için şu seçeneklerden birini seçin:
    - Yük Devretme Kümesi Yöneticisi  
    - Yük devretme kümesi PowerShell

    ```powershell
    $SAPSID = "PR1"     # SAP <SID>

    $SAPClusterGroup = "SAP $SAPSID"
    Move-ClusterGroup -Name $SAPClusterGroup

    ```

2. Windows Konuk işletim sisteminin içindeki küme düğümünü yeniden başlatın. Bu, \<SID\> A düğümünden B DÜĞÜMÜNE SAP küme grubunun otomatik yük devretmesini başlatır.  
3. Azure portal küme düğümünü yeniden başlatın. Bu, \<SID\> A düğümünden B DÜĞÜMÜNE SAP küme grubunun otomatik yük devretmesini başlatır.  
4. Azure PowerShell kullanarak küme düğümünü yeniden başlatın. Bu, \<SID\> A düğümünden B DÜĞÜMÜNE SAP küme grubunun otomatik yük devretmesini başlatır.

5. Doğrulama
   - Yük devretmeden sonra, SAP \<SID\> küme grubunun B küme düğümünde çalıştığını doğrulayın. 

      ![Şekil 8: Yük Devretme Kümesi Yöneticisi, SAP \< SID \> kümesi grubu B kümesi düğümünde çalışıyor][sap-ha-guide-figure-5002]

      _Yük Devretme Kümesi Yöneticisi, SAP \<SID\> kümesi grubu B kümesi düğümünde çalışıyor_

   - Yük devretmeden sonra paylaşılan diskin B küme düğümüne takılı olduğundan emin olun. 
   - Yük devretmenin ardından, SIOS kullanıyorsanız, SIOS veri tutan özelliğinin, küme düğümü B 'deki kaynak birim sürücüsünden gelen verileri, A küme düğümü üzerinde hedef birim sürücüleri olarak çoğaltmakta olduğunu doğrulayın. 

      ![Şekil 9: SIOS Dataman yerel birimi B küme düğümünden A 'ya çoğaltır][sap-ha-guide-figure-5003]

      _SIOS Dataman, yerel birimi B küme düğümünden A kümesine çoğaltır_