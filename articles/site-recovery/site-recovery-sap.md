---
title: Azure Site Recovery ile SAP NetWeaver olağanüstü durum kurtarmayı ayarlama
description: Bu makalede, Azure Site Recovery kullanarak SAP NetWeaver uygulama dağıtımları için olağanüstü durum kurtarmanın nasıl ayarlanacağı açıklanır.
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: ca3126c983d62cb28c543215b86ab9709a4736d8
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083782"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sap-netweaver-app-deployment"></a>Çok katmanlı SAP NetWeaver uygulama dağıtımı için olağanüstü durum kurtarmayı ayarlama

Büyük boyutlu ve orta ölçekli SAP dağıtımları, bir dizi olağanüstü durum kurtarma çözümünü kullanır. Daha fazla temel iş işlemi SAP gibi uygulamalara taşınabildiğinden, sağlam ve test edilebilir olağanüstü durum kurtarma çözümlerinin önemi artar. Azure Site Recovery, SAP uygulamalarıyla sınanmış ve tümleşiktir. Site Recovery, çoğu şirket içi olağanüstü durum kurtarma çözümünün ve daha düşük bir toplam sahip olma maliyeti (TCO) ile rekabet eden çözümlerin yeteneklerini aşmaktadır.

Site Recovery, şunları yapabilirsiniz:
* Bileşenleri Azure 'a çoğaltarak **Şirket içinde çalışan SAP NetWeaver ve NetWeaver olmayan üretim uygulamalarının korumasını etkinleştirin** .
* **Azure üzerinde çalışan SAP NetWeaver ve NetWeaver olmayan üretim uygulamalarının korunmasını,** bileşenleri başka bir Azure veri merkezine çoğaltarak etkinleştirin.
* SAP dağıtımınızı Azure 'a geçirmek için Site Recovery kullanarak **bulut geçişini kolaylaştırın** .
* SAP uygulamalarını test etmek için isteğe bağlı bir üretim kopyası oluşturarak **SAP Proje yükseltmeleri, test ve prototip oluşturma Işlemlerini kolaylaştırın** .

Bu makalede, [Azure Site Recovery](site-recovery-overview.md)kullanarak SAP NetWeaver uygulama dağıtımlarını nasıl koruyabileceğiniz açıklanır. Makale, Site Recovery kullanarak başka bir Azure veri merkezine çoğaltarak Azure 'da üç katmanlı SAP NetWeaver dağıtımını korumaya yönelik en iyi yöntemleri içerir. Desteklenen senaryoları ve konfigürasyonları ve test yük devretmeleri (olağanüstü durum kurtarma detayları) ve gerçek yük devretme işlemleri gerçekleştirmeyi açıklar.

## <a name="prerequisites"></a>Ön koşullar
Başlamadan önce, aşağıdaki görevleri nasıl yapabileceğinizi öğrendiğinizden emin olun:

* [Bir sanal makineyi Azure 'a çoğaltma](azure-to-azure-walkthrough-enable-replication.md)
* [Kurtarma ağını tasarlama](site-recovery-azure-to-azure-networking-guidance.md)
* [Azure 'a yük devretme testi yapın](azure-to-azure-walkthrough-test-failover.md)
* [Azure 'a yük devretme](site-recovery-failover.md)
* [Bir etki alanı denetleyicisini çoğaltma](site-recovery-active-directory.md)
* [SQL Server çoğaltma](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Desteklenen senaryolar
Aşağıdaki senaryolarda bir olağanüstü durum kurtarma çözümü uygulamak için Site Recovery kullanabilirsiniz:
* Başka bir Azure veri merkezine (Azure 'dan Azure 'a olağanüstü durum kurtarma) çoğaltılan bir Azure veri merkezinde çalışan SAP sistemleri. Daha fazla bilgi için bkz. [Azure 'Dan Azure 'a çoğaltma mimarisi](https://aka.ms/asr-a2a-architecture).
* Şirket içinde, bir Azure veri merkezinde olağanüstü durum kurtarma sitesine çoğaltılan (VMware 'den Azure 'a olağanüstü durum kurtarma), VMware (veya fiziksel) sunucularda çalışan SAP sistemleri. Bu senaryo için bazı ek bileşenler gereklidir. Daha fazla bilgi için bkz. [VMware 'Den Azure 'a çoğaltma mimarisi](https://aka.ms/asr-v2a-architecture).
* Şirket içi Hyper-V ' d e çalışan, bir Azure veri merkezinde olağanüstü durum kurtarma sitesine çoğaltılan SAP sistemleri (Hyper-V-Azure olağanüstü durum kurtarma). Bu senaryo için bazı ek bileşenler gereklidir. Daha fazla bilgi için bkz. [Hyper-V-Azure çoğaltma mimarisi](https://aka.ms/asr-h2a-architecture).

Bu makalede, Site Recovery SAP olağanüstü durum kurtarma yeteneklerini göstermek için **Azure 'Dan Azure 'a** olağanüstü durum kurtarma senaryosunu kullanırız. Site Recovery çoğaltma uygulamaya özgü olmadığından, açıklanan işlemin diğer senaryolar için de uygulanması beklenir.

### <a name="required-foundation-services"></a>Gerekli temel hizmetler
Bu makalede tartıştığımız senaryoda, aşağıdaki Foundation Hizmetleri dağıtılır:
* Azure ExpressRoute veya Azure VPN Gateway
* Azure 'da çalışan en az bir Active Directory etki alanı denetleyicisi ve DNS sunucusu

Site Recovery dağıtmadan önce bu altyapıyı oluşturmanız önerilir.

## <a name="reference-sap-application-deployment"></a>Başvuru SAP uygulama dağıtımı

Bu başvuru mimarisi, Azure 'da yüksek kullanılabilirliğe sahip bir Windows ortamında SAP NetWeaver 'ın çalıştığını gösterir.  Bu mimari, kuruluşunuzun ihtiyaçlarına uyacak şekilde değiştirilebilen belirli bir sanal makine (VM) boyutlarıyla dağıtılır.

![Tipik bir SAP dağıtım deseninin diyagramı](./media/site-recovery-sap/sap-netweaver_latest.png)

## <a name="disaster-recovery-considerations"></a>Olağanüstü durum kurtarma konuları

Olağanüstü Durum Kurtarma (DR), ikincil bir bölgeye yük devretme mümkün olması gerekir. Olağanüstü durum kurtarma (DR) koruması sağlamak için her katman farklı bir strateji kullanır.

#### <a name="vms-running-sap-web-dispatcher-pool"></a>SAP web Dağıtıcı havuzu çalıştıran VM 'Ler 
Web Dispatcher bileşen SAP trafiği SAP uygulama sunucuları arasında bir yük dengeleyici olarak kullanılır. Web dağıtıcısı bileşeni için yüksek kullanılabilirlik elde etmek üzere Azure Load Balancer, paralel Web dağıtıcısı kurulumunu, dengeleyici havuzundaki kullanılabilir Web dengeleyicileri 'ler arasında HTTP (S) trafik dağıtımı için hepsini bir kez deneme yapılandırmasında uygulamak üzere kullanılır. Bu, Azure Site Recovery (ASR) kullanılarak çoğaltılır ve otomasyon betikleri, olağanüstü durum kurtarma bölgesinde yük dengeleyiciyi yapılandırmak için kullanılacaktır. 

#### <a name="vms-running-application-servers-pool"></a>Uygulama sunucuları havuzunu çalıştıran VM 'Ler
SMLG işlem ABAP uygulama sunucuları için oturum açma grupları yönetmek için kullanılır. Yük Dengeleme ileti sunucusu merkezi Hizmetleri işlevindeki SAPGUIs ve RFC için SAP uygulama sunucuları havuzu arasındaki iş yükünü dağıtmak için kullandığı trafiği. Bu, Azure Site Recovery kullanılarak çoğaltılır 

#### <a name="vms-running-sap-central-services-cluster"></a>SAP merkezi hizmetler kümesi çalıştıran VM 'Ler
Bu başvuru mimarisi, uygulama katmanında Vm'lerde merkezi hizmetleri çalıştırır. Merkezi Hizmetleri için tek bir VM dağıtılırken hata (SPOF) bir olası tek noktası olan — yüksek kullanılabilirlik gereksinimi olmadığı durumlarda tipik dağıtım.<br>

Yüksek oranda kullanılabilir bir çözüm uygulamak için, paylaşılan bir disk kümesi veya bir dosya paylaşım kümesi kullanılabilir. VM 'Leri paylaşılan bir disk kümesi için yapılandırmak için Windows Server yük devretme kümesi ' ni kullanın. Bulut tanığı çekirdek tanığı olarak önerilir. 
 > [!NOTE]
 > Azure Site Recovery bulut tanığını çoğaltmadığından, bulut tanığını olağanüstü durum kurtarma bölgesinde dağıtmanız önerilir.

Yük devretme küme ortamında desteklemek için [SIOS DataKeeper Cluster Edition](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8) küme düğümlerinin sahip olduğu bağımsız diskleri çoğaltarak Küme Paylaşılan Birimi işlevi gerçekleştirir. Azure Paylaşılan diskleri yerel olarak desteklemez ve bu nedenle SIOS tarafından sağlanan çözümleri gerektirir. 

Kümeleme işlemenin başka bir yolu da dosya paylaşma kümesi uygulamaktır. [SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) /sapmnt genel dizinleri UNC yolu üzerinden erişmek için Yönetim Hizmetleri dağıtım modeli yakın zamanda değiştirilmiş. Ancak,/sapmnt UNC paylaşımının yüksek oranda kullanılabilir olduğundan emin olmak yine de önerilir. Bu işlem, Windows Server 2016 ' deki genişleme dosya sunucusu (SOFS) ve Depolama Alanları Doğrudan (S2D) özelliği ile Windows Server yük devretme kümesi kullanılarak Merkezi Hizmetler örneğinde yapılabilir. 
 > [!NOTE]
 > Şu anda Azure Site Recovery, yalnızca depolama alanları doğrudan ve yalnızca SIOS veri Man 'ın pasif düğümü kullanılarak sanal makinelerin kilitlenme tutarlı noktası çoğaltmasını destekler


## <a name="disaster-recovery-considerations"></a>Olağanüstü durum kurtarmayla konusunda dikkat edilmesi gerekenler

Azure bölgelerinde tam SAP dağıtımı yükünü yönetmek için Azure Site Recovery kullanabilirsiniz.
Olağanüstü durum kurtarmayı ayarlamaya yönelik adımlar aşağıda verilmiştir 

1. Sanal makineleri çoğaltma 
2. Kurtarma ağını tasarlama
3.  Bir etki alanı denetleyicisini çoğaltma
4.  Veri tabanı katmanını Çoğalt 
5.  Yük devretme testi gerçekleştirin 
6.  Yük devretme gerçekleştirin 

Bu örnekte kullanılan her bir katmanın olağanüstü durum kurtarma önerisi aşağıda verilmiştir. 

 **SAP katmanları** | **Öneri**
 --- | ---
**SAP web Dağıtıcı havuzu** |  Site Recovery kullanarak çoğaltma 
**SAP uygulama sunucusu havuzu** |  Site Recovery kullanarak çoğaltma 
**SAP merkezi hizmetler kümesi** |  Site Recovery kullanarak çoğaltma 
**Active Directory sanal makineleri** |  Active Directory çoğaltma 
**SQL veritabanı sunucuları** |  SQL Always on çoğaltma

## <a name="replicate-virtual-machines"></a>Sanal makineleri çoğaltma

Tüm SAP uygulaması sanal makinelerini Azure olağanüstü durum kurtarma veri merkezine Çoğaltmaya başlamak için, [bir sanal makineyi Azure 'A çoğaltma](azure-to-azure-walkthrough-enable-replication.md)' daki yönergeleri izleyin.


* Active Directory ve DNS 'yi koruma hakkında yönergeler için bkz. [koruma Active Directory ve DNS](site-recovery-active-directory.md) belgesi.

* SQL Server üzerinde çalışan veritabanı katmanını koruma hakkında yönergeler için bkz. [koruma SQL Server](site-recovery-active-directory.md) belgesi.

## <a name="networking-configuration"></a>Ağ yapılandırması

Statik bir IP adresi kullanırsanız, sanal makinenin kullanmasını istediğiniz IP adresini belirtebilirsiniz. IP adresini ayarlamak için, **işlem ve ağ ayarları** > **ağ arabirimi kartına**gidin.

![Site Recovery ağ arabirimi kartı bölmesinde özel IP adresi ayarlamayı gösteren ekran görüntüsü](./media/site-recovery-sap/sap-static-ip.png)


## <a name="creating-a-recovery-plan"></a>Kurtarma planı oluşturma
Kurtarma planı, yük devretme sırasında çok katmanlı bir uygulamadaki çeşitli katmanların sıralamasını destekler. Sıralama, uygulama tutarlılığını sürdürmenize yardımcı olur. Çok katmanlı bir Web uygulaması için bir kurtarma planı oluşturduğunuzda [Site Recovery kullanarak kurtarma planı oluşturma](site-recovery-create-recovery-plans.md)bölümünde açıklanan adımları uygulayın.

### <a name="adding-virtual-machines-to-failover-groups"></a>Yük devretme gruplarına sanal makineler ekleme

1.  Uygulama sunucusu, Web dağıtıcısı ve SAP Merkezi Hizmetleri VM 'Leri ekleyerek bir kurtarma planı oluşturun.
2.  VM 'Leri gruplandırmak için ' Özelleştir 'e tıklayın. Varsayılan olarak, tüm VM 'Ler ' Grup 1 ' in bir parçasıdır.



### <a name="add-scripts-to-the-recovery-plan"></a>Kurtarma planına betikler ekleme
Uygulamalarınızın düzgün çalışması için, yük devretmeden sonra veya yük devretme testi sırasında Azure sanal makinelerinde bazı işlemler yapmanız gerekebilir. Bazı yük devretme sonrası işlemleri otomatik hale getirebilirsiniz. Örneğin, kurtarma planına ilgili betikler ekleyerek DNS girişini güncelleştirebilir ve bağlamaları ve bağlantıları değiştirebilirsiniz.


En yaygın olarak kullanılan Azure Site Recovery betikleri aşağıdaki ' Azure 'a Dağıt ' düğmesine tıklayarak Otomasyon hesabınıza dağıtabilirsiniz. Yayımlanmış herhangi bir betiği kullanırken, betikteki yönergeleri izlediğinizden emin olun.

[![Azure’a dağıtma](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. SQL kullanılabilirlik grubu yük devretmesi için ' Group 1 ' öğesine bir ön eylem betiği ekleyin. Örnek betikte yayımlanan ' ASR-SQL-FailoverAG ' betiğini kullanın. Betikteki yönergeleri izlediğinizden ve betikteki gerekli değişiklikleri uygun şekilde seçtiğinizden emin olun.
2. Yük dengeleyiciyi web katmanının yükü devredildiği sanal makinelere (Grup 1) eklemek için bir post eylem betiği ekleyin. Örnek betikte yayımlanan ' ASR-AddSingleLoadBalancer ' betiğini kullanın. Betikteki yönergeleri izlediğinizden ve betikteki gerekli değişiklikleri uygun şekilde seçtiğinizden emin olun.

![SAP kurtarma planı](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>Yük devretme testi çalıştırma

1.  Azure portal, kurtarma hizmetleri kasanızı seçin.
2.  SAP uygulamaları için oluşturduğunuz kurtarma planını seçin.
3.  **Yük Devretme Testi**'ni seçin.
4.  Yük devretme testi işlemini başlatmak için kurtarma noktasını ve Azure sanal ağını seçin.
5.  İkincil ortam çalışır duruma geldiğinde doğrulama gerçekleştirin.
6.  Doğrulamalar tamamlandığında, yük devretme ortamını temizlemek için **Test yük devretmesini**Temizle ' yi seçin.

Daha fazla bilgi için bkz. [Azure 'a yük devretmeyi test etme Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Yük devretme çalıştırma

1.  Azure portal, kurtarma hizmetleri kasanızı seçin.
2.  SAP uygulamaları için oluşturduğunuz kurtarma planını seçin.
3.  **Yük devretme**'yi seçin.
4.  Yük devretme işlemini başlatmak için kurtarma noktasını seçin.

Daha fazla bilgi için bkz. [Site Recovery 'de yük devretme](site-recovery-failover.md).

## <a name="next-steps"></a>Sonraki adımlar
* Site Recovery kullanarak SAP NetWeaver dağıtımları için olağanüstü durum kurtarma çözümü oluşturma hakkında daha fazla bilgi için bkz. indirilebilir beyaz kağıt [SAP NetWeaver: Azure Site Recovery Ile olağanüstü durum kurtarma çözümü oluşturma](https://aka.ms/asr_sap). Teknik İnceleme, çeşitli SAP mimarilerine yönelik öneriler ele alınmaktadır, Azure 'da SAP için desteklenen uygulamaları ve VM türlerini listeler ve olağanüstü durum kurtarma çözümünüz için test planı seçeneklerini açıklar.
* Site Recovery kullanarak [diğer iş yüklerini çoğaltma](site-recovery-workload.md) hakkında daha fazla bilgi edinin.
