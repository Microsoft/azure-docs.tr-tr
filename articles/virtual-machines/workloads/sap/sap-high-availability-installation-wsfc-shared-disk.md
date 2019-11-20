---
title: Azure 'da SAP ASCS/SCS örneği için Windows Yük devretme kümesine ve paylaşılan diske SAP NetWeaver HA 'yi yüklemeyin | Microsoft Docs
description: SAP NetWeaver HA 'yi bir Windows Yük devretme kümesine ve paylaşılan diske bir SAP ASCS/SCS örneği için nasıl yükleyeceğinizi öğrenin.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 6209bcb3-5b20-4845-aa10-1475c576659f
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a22d77de80c7440fc120d2c48f9e73e606388848
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70078164"
---
# <a name="install-sap-netweaver-ha-on-a-windows-failover-cluster-and-shared-disk-for-an-sap-ascsscs-instance-in-azure"></a>Azure 'da SAP ASCS/SCS örneği için Windows Yük devretme kümesine ve paylaşılan diske SAP NetWeaver HA 'yi yüklemeyin

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

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

Bu makalede, bir SAP ASCS/SCS örneğini Kümelendirmek için bir Windows Server yük devretme kümesi ve Küme Paylaşılan diski kullanılarak Azure 'da yüksek kullanılabilirliğe sahip bir SAP sisteminin nasıl yükleneceği ve yapılandırılacağı açıklanmaktadır.

## <a name="prerequisites"></a>Önkoşullar

Yüklemeye başlamadan önce şu belgeleri gözden geçirin:

* [Mimari Kılavuzu: Küme Paylaşılan diski kullanarak bir Windows Yük devretme kümesinde SAP ASCS/SCS örneği oluşturma][sap-high-availability-guide-wsfc-shared-disk]

* [SAP Ass/SCS örneği için Windows Yük devretme kümesi ve paylaşılan disk kullanarak SAP HA için Azure altyapısını hazırlama][sap-high-availability-infrastructure-wsfc-shared-disk]

Bu makaledeki DBMS kurulumunu açıklamamız, çünkü ayarlar kullandığınız DBMS sistemine bağlı olarak değişir. DBMS ile ilgili yüksek kullanılabilirliğe sahip olan sorunların, Azure için farklı DBMS satıcılarının desteklediği işlevlere değinilmesi gerektiğini varsaydık. Oracle veritabanları için SQL Server ve Oracle Data Guard için AlwaysOn veya veritabanı yansıtma örnekleri verilmiştir. Bu makalede kullandığımız senaryoda, DBMS 'ye daha fazla koruma ekliyoruz.

Farklı DBMS Hizmetleri, Azure 'daki kümelenmiş SAP Ass veya SCS yapılandırmasıyla etkileşen özel önemli noktalar yoktur.

> [!NOTE]
> SAP NetWeaver ABAP Systems, Java sistemleri ve ABAP + Java sistemlerinin yükleme yordamları neredeyse aynıdır. En önemli fark, SAP ABAP sisteminin bir ASCS örneğine sahip olması gerektiğidir. SAP Java sisteminde bir SCS örneği vardır. SAP ABAP + Java sisteminde bir ASCS örneği ve aynı Microsoft yük devretme kümesi grubunda çalışan bir SCS örneği vardır. Her SAP NetWeaver yükleme yığını için herhangi bir yükleme farkı açıkça bahsedilir. Diğer tüm parçaların aynı olduğunu varsayabilirsiniz.  
>
>

## <a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>Yüksek kullanılabilirliğe sahip bir ASCS/SCS örneğiyle SAP 'yi yükler

> [!IMPORTANT]
> Sayfa dosyanızı, SIOS veri akışı yansıtılmış birimlerine yerleştirdiğinizden emin olun. Dataman yansıtılmış birimleri desteklemez. Sayfa dosyanızı, varsayılan olan bir Azure sanal makinesinin geçici sürücüsünde bırakabilirsiniz. Henüz orada yoksa, Windows sayfa dosyasını Azure sanal makinenizin D sürücüsüne taşıyın.
>
>

SAP 'yi yüksek kullanılabilirliğe sahip bir ASCS/SCS örneğiyle yüklemek şu görevleri içerir:

* Kümelenmiş SAP ASCS/SCS örneği için bir sanal ana bilgisayar adı oluşturun.
* SAP ilk küme düğümünü yükler.
* ASCS/SCS örneğinin SAP profilini değiştirin.
* Araştırma bağlantı noktası ekleyin.
* Windows Güvenlik Duvarı araştırma bağlantı noktasını açın.

### <a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>Kümelenmiş SAP Ass/SCS örneği için sanal ana bilgisayar adı oluşturma

1. Windows DNS Yöneticisi 'nde, yoks/SCS örneğinin sanal ana bilgisayar adı için bir DNS girişi oluşturun.

   > [!IMPORTANT]
   > Yoks/SCS örneğinin sanal ana bilgisayar adına atadığınız IP adresi, Azure Load Balancer atadığınız IP adresiyle aynı olmalıdır (\<SID\>-lb-ascs).  
   >
   >

   Sanal SAP ASCS/SCS ana bilgisayar adının (PR1-ascs-SAP) IP adresi Azure Load Balancer (PR1-lb-ascs) IP adresiyle aynıdır.

   ![Şekil 1: SAP ASCS/SCS kümesi sanal adı ve TCP/IP adresi için DNS girişi tanımlayın][sap-ha-guide-figure-3046]

   _**Şekil 1:** SAP ASCS/SCS kümesi sanal adı ve TCP/IP adresi için DNS girişi tanımlayın_

2. Sanal ana bilgisayar adına atanan IP adresini tanımlamak için **DNS Yöneticisi** > **etki alanı**' nı seçin.

   ![Şekil 2: SAP Ass/SCS küme yapılandırması için yeni sanal ad ve TCP/IP adresi][sap-ha-guide-figure-3047]

   _**Şekil 2:** SAP Ass/SCS küme yapılandırması için yeni sanal ad ve TCP/IP adresi_

### <a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a>SAP ilk küme düğümünü yükler

1. Küme düğümü A 'da ilk küme düğümü seçeneğini yürütün. Örneğin, PR1-ascs-0 * ana bilgisayarında.
2. Azure iç yük dengeleyici için varsayılan bağlantı noktalarını tutmak üzere şunları seçin:

   * **ABAP sistemi**: **Yoks** örnek numarası **00**
   * **Java sistemi**: **SCS** örnek numarası **01**
   * **ABAP + Java sistemi**: **Ascs** örnek numarası **00** ve **SCS** örnek numarası **01**

   ABAP Ass örneği için 00 dışındaki örnek numaralarını ve Java SCS örneği için 01 ' i kullanmak için, önce Azure iç yük dengeleyici varsayılan Yük Dengeleme kurallarını değiştirin. Daha fazla bilgi için bkz. [Azure iç yük dengeleyici IÇIN Ass/SCS varsayılan Yük Dengeleme kurallarını değiştirme][sap-ha-guide-8.9].

Sonraki birkaç görev standart SAP yükleme belgelerinde açıklanmamıştır.

> [!NOTE]
> SAP yükleme belgeleri, ilk ASCS/SCS küme düğümünün nasıl yükleneceğini açıklar.
>
>

### <a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a>ASCS/SCS örneğinin SAP profilini değiştirme

İlk olarak, yeni bir profil parametresi ekleyin. Profil parametresi, SAP iş işlemleriyle sıraya alma sunucusu arasındaki bağlantıları çok uzun süre boşta kaldığında kapatmadan önler. [SAP ASCS/SCS örneğinin küme düğümlerine kayıt defteri girişleri ekleme][sap-ha-guide-8.11]' deki sorun senaryosundan bahsedin. Bu bölümde, bazı temel TCP/IP bağlantı parametrelerinde iki değişiklik de sunuyoruz. İkinci bir adımda, bağlantıların Azure iç yük dengeleyicinin boşta eşiğine ulaşmaması için sıraya `keep_alive` alma sunucusunu bir sinyal gönderecek şekilde ayarlamanız gerekir.

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

### <a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a>Araştırma bağlantı noktası ekle

Tüm küme yapılandırmasının Azure Load Balancer ile çalışmasını sağlamak için iç yük dengeleyicinin araştırma işlevini kullanın. Azure iç yük dengeleyici, genellikle katılan sanal makineler arasında eşit olarak gelen iş yükünü dağıtır.

 Ancak, yalnızca bir örnek etkin olduğundan, bazı küme yapılandırmalarında bu çalışmaz. Diğer örnek pasif ve iş yükünün hiçbirini kabul edemiyor. Araştırma işlevselliği, Azure iç yük dengeleyicinin yalnızca etkin bir örneğe iş atarken çalışmasına yardımcı olur. Araştırma işlevselliğiyle, iç yük dengeleyici hangi örneklerin etkin olduğunu algılayabilir ve sonra yalnızca örneği iş yüküne hedefleyebilir.

Araştırma bağlantı noktası eklemek için:

1. Aşağıdaki PowerShell komutunu çalıştırarak geçerli **Probeport** değerini kontrol edin:

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
   ```

   Komutu küme yapılandırmasındaki sanal makinelerden biri içinden yürütün.

2. Bir yoklama bağlantı noktası tanımlayın. Varsayılan yoklama bağlantı noktası numarası 0 ' dır. Örneğimizde araştırma bağlantı noktası 62000 ' i kullanıyoruz.

   ![Şekil 3: Küme yapılandırma araştırması bağlantı noktası varsayılan olarak 0 ' dır][sap-ha-guide-figure-3048]

   _**Şekil 3:** Varsayılan küme yapılandırma araştırması bağlantı noktası 0 ' dır_

   Bağlantı noktası numarası SAP Azure Resource Manager şablonlarında tanımlanmıştır. Bağlantı noktası numarasını PowerShell 'e atayabilirsiniz.

   SAP \<SID\> IP kümesi kaynağı için yeni bir probeport değeri ayarlamak için, ortamınız için PowerShell değişkenlerini güncelleştirmek üzere aşağıdaki PowerShell betiğini çalıştırın:

   ```powershell
   $SAPSID = "PR1"      # SAP <SID>
   $ProbePort = 62000   # ProbePort of the Azure internal load balancer

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

   SAP \<SID\> küme rolünü çevrimiçine geçirdikten sonra, **probeport** değerinin yeni değere ayarlandığını doğrulayın.

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

   ```
   Betik çalıştıktan sonra, değişiklikleri etkinleştirmek için SAP küme grubunu yeniden başlatmanız istenir.

   ![Şekil 4: Yeni değeri ayarladıktan sonra küme bağlantı noktasını araştırma][sap-ha-guide-figure-3049]

   _**Şekil 4:** Yeni değeri ayarladıktan sonra küme bağlantı noktasını araştırma_

### <a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a>Windows Güvenlik Duvarı araştırma bağlantı noktasını açın

Her iki küme düğümünde bir Windows Güvenlik Duvarı araştırması bağlantı noktası açın. Windows Güvenlik Duvarı araştırması bağlantı noktasını açmak için aşağıdaki betiği kullanın. Ortamınız için PowerShell değişkenlerini güncelleştirin.

  ```powershell
  $ProbePort = 62000   # ProbePort of the Azure internal load balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

**Probeport** **62000**olarak ayarlanır. Şimdi, \ascsha-clsap\sapmnt dosya paylaşımında \\, assha-DBAS gibi diğer konaklardan erişebilirsiniz.

## <a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a>Veritabanı örneğini yükler

Veritabanı örneğini yüklemek için SAP yükleme belgelerinde açıklanan işlemi izleyin.

## <a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a>İkinci küme düğümünü yükler

İkinci kümeyi yüklemek için, SAP yükleme kılavuzunda açıklanan adımları izleyin.

## <a name="094bc895-31d4-4471-91cc-1513b64e406a"></a>SAP ERS Windows hizmeti örneğinin başlangıç türünü değiştirme

SAP 'nin Windows hizmeti başlangıç türünü, her iki küme düğümünde da **Otomatik (Gecikmeli başlatma)** olarak değiştirin.

![Şekil 5: SAP ERS örneği için hizmet türünü Gecikmeli otomatik olarak değiştirme][sap-ha-guide-figure-3050]

_**Şekil 5:** SAP ERS örneği için hizmet türünü Gecikmeli otomatik olarak değiştirme_

## <a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a>SAP birincil uygulama sunucusunu yükler

Pas 'yi barındırmak için belirlediğiniz sanal makineye birincil uygulama \<sunucusu\>(pas) örneği SID-dı-0 ' yı yükler. Azure üzerinde hiçbir bağımlılık yoktur. Veri ve özel ayar yok.

## <a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a>SAP ek uygulama sunucusunu yükler

SAP uygulama sunucusu örneğini barındırmak için belirlediğiniz tüm sanal makinelere bir SAP ek uygulama sunucusu (AAS) yükleyebilirsiniz. Örneğin \<,&gt;SID\>-dı-1 ile \<SID\>-dı-&lt;n arası.

> [!NOTE]
> Bu, yüksek kullanılabilirliğe sahip SAP NetWeaver sisteminin yüklenmesini sonlandırır. Sonra, yük devretme testi ile devam edin.
>


## <a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a>SAP ASCS/SCS örneği yük devretme ve SIOS çoğaltmasını test etme
Yük Devretme Kümesi Yöneticisi ve SIOS Dataman yönetimi ve yapılandırma aracını kullanarak SAP yoks/SCS örneği yük devretmesini ve SIOS disk çoğaltmasını test etmek ve izlemek kolaydır.

### <a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a>SAP ASCS/SCS örneği, küme düğümü A 'da çalışıyor

SAP PR1 küme grubu, A küme düğümü üzerinde çalışıyor. Örneğin, PR1-ascs-0 üzerinde. SAP PR1 küme grubunun bir parçası olan paylaşılan disk sürücüsüne göre ' yi küme düğümüne atayın. YOKS/SCS örneği de disk sürücü S kullanır. 

![Şekil 6: Yük Devretme Kümesi Yöneticisi: SAP \<SID\> küme grubu, A kümesi düğümünde çalışıyor][sap-ha-guide-figure-5000]

_**Şekil 6:** Yük Devretme Kümesi Yöneticisi: SAP \<SID\> küme grubu, A kümesi düğümünde çalışıyor_

SIOS Verilerlik yönetimi ve yapılandırma aracında, paylaşılan disk verilerinin, A küme düğümü üzerindeki kaynak birim sürücüsünden, B küme düğümü üzerindeki hedef birim sürücüsüne zaman uyumlu olarak çoğaltıldığından emin olabilirsiniz. Örneğin, PR1-ascs-0 [10.0.0.40] öğesinden PR1-ascs-1 [10.0.0.41] arasında çoğaltılır.

![Şekil 7: Jımdataman 'da yerel birimi küme düğümü A 'yı küme düğümüne çoğaltma B][sap-ha-guide-figure-5001]

_**Şekil 7:** Jımdataman 'da yerel birimi küme düğümü A 'yı küme düğümüne çoğaltma B_

### <a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a>A düğümünden B düğümüne yük devretme

1. A kümesi düğümünden B kümesine SAP \<SID\> küme grubunun yük devretmesini başlatmak için şu seçeneklerden birini seçin:
   - Yük Devretme Kümesi Yöneticisi  
   - Yük devretme kümesi PowerShell

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPClusterGroup = "SAP $SAPSID"
   Move-ClusterGroup -Name $SAPClusterGroup

   ```
2. Windows Konuk işletim sisteminin içindeki küme düğümünü yeniden başlatın. Bu, A düğümünden B düğümüne SAP \<SID\> küme grubunun otomatik yük devretmesini başlatır.  
3. Azure portal küme düğümünü yeniden başlatın. Bu, A düğümünden B düğümüne SAP \<SID\> küme grubunun otomatik yük devretmesini başlatır.  
4. Azure PowerShell kullanarak küme düğümünü yeniden başlatın. Bu, A düğümünden B düğümüne SAP \<SID\> küme grubunun otomatik yük devretmesini başlatır.

   Yük devretmeden sonra, \<SAP\> SID kümesi grubu B kümesi düğümünde çalışır. Örneğin, PR1-ascs-1 üzerinde çalışıyor.

   ![Şekil 8: Yük devretme kümesi Yöneticisi, SAP \<SID\> kümesi grubu B kümesi düğümünde çalışıyor][sap-ha-guide-figure-5002]

   _**Şekil 8**: Yük devretme kümesi Yöneticisi, SAP \<SID\> kümesi grubu B kümesi düğümünde çalışıyor_

   Paylaşılan disk artık B küme düğümüne takıyor. SIOS Verilerman, küme düğümü B 'deki kaynak birim sürücüsünden verileri, A küme düğümü üzerindeki hedef birim sürücüsüne çoğaltmakta. Örneğin, PR1-ascs-1 [10.0.0.41] öğesinden PR1-ascs-0 [10.0.0.40] arasında çoğaltılıyor.

   ![Şekil 9: SIOS Dataman, yerel birimi B küme düğümünden A kümesine çoğaltır][sap-ha-guide-figure-5003]

   _**Şekil 9:** SIOS Dataman, yerel birimi B küme düğümünden A kümesine çoğaltır_
