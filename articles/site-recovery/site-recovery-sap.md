---
title: Azure Site Recovery ile SAP NetWeaver olağanüstü durum kurtarmayı ayarlama
description: Azure Site Recovery ile SAP NetWeaver için olağanüstü durum kurtarmayı ayarlamayı öğrenin.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 11/27/2018
ms.openlocfilehash: e639762cd1adb7bbbc3fb2ec31f4ce52710e46f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101711952"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sap-netweaver-app-deployment"></a>Çok katmanlı SAP NetWeaver uygulama dağıtımı için olağanüstü durum kurtarmayı ayarlama

Büyük boyutlu ve orta ölçekli SAP dağıtımları, bir dizi olağanüstü durum kurtarma çözümünü kullanır. Daha fazla temel iş işlemi SAP gibi uygulamalara taşınabildiğinden, sağlam ve test edilebilir olağanüstü durum kurtarma çözümlerinin önemi artar. Azure Site Recovery, SAP uygulamalarıyla sınanmış ve tümleşiktir. Site Recovery, çoğu şirket içi olağanüstü durum kurtarma çözümünün ve daha düşük bir toplam sahip olma maliyetinden rekabet eden çözümlerin yeteneklerini aşmaktadır.

Site Recovery, şunları yapabilirsiniz:
* Bileşenleri Azure 'a çoğaltarak şirket içinde çalışan SAP NetWeaver ve NetWeaver olmayan üretim uygulamalarının korumasını etkinleştirin.
* Azure üzerinde çalışan SAP NetWeaver ve NetWeaver olmayan üretim uygulamalarının korunmasını, bileşenleri başka bir Azure veri merkezine çoğaltarak etkinleştirin.
* SAP dağıtımınızı Azure'a geçirmek için Site Recovery'yi kullanarak bulut geçişini basitleştirin.
* SAP uygulamalarını test etmek için isteğe bağlı bir üretim kopyası oluşturarak SAP Proje yükseltmeleri, test ve prototip oluşturma işlemlerini kolaylaştırın.

[Azure Site Recovery](site-recovery-overview.md)kullanarak SAP NetWeaver uygulama dağıtımlarını koruyabilirsiniz. Bu makalede, Site Recovery kullanarak başka bir Azure veri merkezine çoğalttığınızda Azure 'da üç katmanlı SAP NetWeaver dağıtımını korumaya yönelik en iyi uygulamalar ele alınmaktadır. Makalesinde desteklenen senaryolar ve Konfigürasyonlar ve yük devretme testi (olağanüstü durum kurtarma detayları) ve gerçek yük devretme işlemleri açıklanmaktadır.

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce, aşağıdaki görevleri nasıl yapabileceğinizi öğrendiğinizden emin olun:

* [Bir sanal makineyi Azure 'a çoğaltma](./azure-to-azure-tutorial-enable-replication.md)
* [Kurtarma ağını tasarlama](./azure-to-azure-about-networking.md)
* [Azure 'a yük devretme testi yapın](./azure-to-azure-tutorial-dr-drill.md)
* [Azure 'a yük devretme](site-recovery-failover.md)
* [Bir etki alanı denetleyicisini çoğaltma](site-recovery-active-directory.md)
* [SQL Server örneğini çoğaltma](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Desteklenen senaryolar

Aşağıdaki senaryolarda bir olağanüstü durum kurtarma çözümü uygulamak için Site Recovery kullanabilirsiniz:
* Bir Azure veri merkezinde çalışan SAP sistemleriniz var ve bunları başka bir Azure veri merkezine (Azure 'dan Azure 'a olağanüstü durum kurtarma) çoğaltıyoruz. 
   Daha fazla bilgi için bkz. [Azure 'Dan Azure 'a çoğaltma mimarisi](./azure-to-azure-architecture.md).
* Şirket içinde VMware (veya fiziksel) sunucularda çalışan SAP sistemleri vardır. Ayrıca SAP sistemlerini, bir Azure veri merkezinde (VMware 'den Azure 'a olağanüstü durum kurtarma) bir olağanüstü durum kurtarma sitesine çoğaltmakta olursunuz. 
   Bu senaryo için bazı ek bileşenler gereklidir. Daha fazla bilgi için bkz. [VMware 'Den Azure 'a çoğaltma mimarisi](./vmware-azure-architecture.md).
* Şirket içi Hyper-V üzerinde çalışan SAP sistemlerine sahipsiniz. Ayrıca SAP sistemlerini, bir Azure veri merkezinde (Hyper-V-Azure olağanüstü durum kurtarma) bir olağanüstü durum kurtarma sitesine çoğaltmakta olursunuz.
   Bu senaryo için bazı ek bileşenler gereklidir. Daha fazla bilgi için bkz. [Hyper-V-Azure çoğaltma mimarisi](./hyper-v-azure-architecture.md).

Bu makalede, **Azure 'Dan Azure 'a** olağanüstü durum kurtarma senaryosu kullanıyoruz. Senaryo, Site Recovery SAP olağanüstü durum kurtarma özelliklerini gösterir. Site Recovery çoğaltma uygulamaya özgü olmadığından, açıklanan işlemin diğer senaryolar için de uygulanması beklenir.

### <a name="required-foundation-services"></a>Gerekli temel hizmetler
Bu makalede tartıştığımız senaryoda, aşağıdaki Foundation Hizmetleri dağıtılır:
* Azure ExpressRoute veya Azure VPN Gateway
* Azure 'da çalışan en az bir Azure Active Directory etki alanı denetleyicisi ve DNS sunucusu

Site Recovery dağıtmadan önce bu altyapıyı oluşturmanız önerilir.

## <a name="reference-sap-application-deployment"></a>Başvuru SAP uygulama dağıtımı

Bu başvuru mimarisi, Azure 'da yüksek kullanılabilirliğe sahip bir Windows ortamında SAP NetWeaver çalıştırıyor. Bu mimari, kuruluşunuzun ihtiyaçlarına uyum sağlayacak şekilde değiştirebileceğiniz belirli bir sanal makine (VM) boyutlarıyla dağıtılır.

![Tipik bir SAP dağıtım deseninin diyagramı](./media/site-recovery-sap/sap-netweaver_latest.png)

## <a name="disaster-recovery-considerations"></a>Olağanüstü durum kurtarmayla konusunda dikkat edilmesi gerekenler

Olağanüstü durum kurtarma için, ikincil bir bölgeye yük devredebilmelisiniz. Her katman olağanüstü durum kurtarma koruması sağlamak için farklı bir strateji kullanır.

#### <a name="vms-running-sap-web-dispatcher-pools"></a>SAP web dağıtıcı havuzları çalıştıran VM 'Ler

Web dağıtıcısı bileşeni, SAP uygulama sunucuları arasında SAP trafiği için yük dengeleyici olarak çalışmaktadır. Web dağıtıcısı bileşeni için yüksek kullanılabilirlik elde etmek üzere Azure Load Balancer paralel Web dağıtıcısı kurulumunu uygular. Web dağıtıcısı, dengeleyiciler havuzundaki kullanılabilir Web dağıtımcıları arasında HTTP (S) trafik dağıtımı için hepsini bir kez deneme yapılandırması kullanır.

#### <a name="vms-running-application-servers-pools"></a>Uygulama sunucuları havuzlarını çalıştıran VM 'Ler
SMLG işlemi, ABAP uygulama sunucuları için oturum açma gruplarını yönetir. SAP uygulama sunucusu havuzları ve RFC trafiği arasında iş yükünü dağıtmak için merkezi hizmetlerin ileti sunucusu içinde Yük Dengeleme işlevini kullanır. Site Recovery kullanarak bu yönetimi çoğaltabilirsiniz.

#### <a name="vms-running-sap-central-services-clusters"></a>SAP Merkezi Hizmetleri kümelerini çalıştıran VM 'Ler
Bu başvuru mimarisi, uygulama katmanındaki VM 'lerde Merkezi Hizmetler çalıştırır. Merkezi Hizmetler, tek bir VM 'de olduğunda olası bir tek hata noktasıdır. Tipik dağıtım ve yüksek kullanılabilirlik gerekli değildir.

Yüksek oranda kullanılabilir bir çözüm uygulamak için, paylaşılan bir disk kümesi veya bir dosya paylaşım kümesi kullanabilirsiniz. VM 'Leri paylaşılan bir disk kümesi için yapılandırmak için Windows Server yük devretme kümesi ' ni kullanın. Bulut tanığını bir çekirdek tanığı olarak kullanmanızı öneririz.

 > [!NOTE]
 > Site Recovery bulut tanığını çoğaltmadığından, bulut tanığını olağanüstü durum kurtarma bölgesinde dağıtmanızı öneririz.

Bir yük devretme kümesi ortamını desteklemek için, [SIOS Dataman küme sürümü](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8) küme paylaşılan birimi işlevini yapar. İşlevinde, SIOS Dataman kümesi, küme düğümlerinin sahip olduğu bağımsız diskleri çoğaltır. Azure, Paylaşılan diskleri yerel olarak desteklemediğinden, SIOS tarafından sunulan çözümler gerektirir.

Ayrıca, bir dosya paylaşma kümesi uygulayarak kümeleme işleyebilirsiniz. [SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) kısa süre önce,/sapmnt genel DIZINLERINE bir UNC yolu aracılığıyla erişmek Için Merkezi Hizmetler dağıtım modelini değiştirdi. Yine de/sapmnt UNC paylaşımının yüksek oranda kullanılabilir olmasını öneririz. Merkezi Hizmetler örneğinizi kontrol edebilirsiniz. Windows Server 2016 ' de genişleme dosya sunucusu (SOFS) ve Depolama Alanları Doğrudan (S2D) özelliği ile Windows Server yük devretme kümesi kullanın.

 > [!NOTE]
 > Site Recovery Şu anda yalnızca depolama alanları doğrudan kullanan sanal makinelerin yalnızca kilitlenme tutarlı noktası çoğaltmasını ve yalnızca SIOS veri Man 'ın pasif düğümünü desteklemektedir.


## <a name="more-disaster-recovery-considerations"></a>Daha fazla olağanüstü durum kurtarma konuları

Azure bölgelerinde tam SAP dağıtımının yük devretmesini yönetmek için Site Recovery kullanabilirsiniz.
Olağanüstü durum kurtarmayı ayarlamaya yönelik adımlar aşağıda verilmiştir:

1. Sanal makineleri çoğaltma
1. Kurtarma ağını tasarlama
1. Bir etki alanı denetleyicisini çoğaltma
1. Veri tabanı katmanını Çoğalt
1. Yük devretme testi gerçekleştirin
1. Yük devretme gerçekleştirin

Bu örnekte kullanılan her bir katmanın olağanüstü durum kurtarma önerisi aşağıda verilmiştir.

 **SAP katmanları** | **Öneri**
 --- | ---
**SAP web Dağıtıcı havuzu** |  Site Recovery kullanarak çoğaltma 
**SAP uygulama sunucusu havuzu** |  Site Recovery kullanarak çoğaltma 
**SAP merkezi hizmetler kümesi** |  Site Recovery kullanarak çoğaltma 
**Active Directory sanal makineleri** |  Active Directory çoğaltmasını kullanma 
**SQL Veritabanı sunucuları** |  SQL Server her zaman çoğaltma üzerinde kullanın

## <a name="replicate-virtual-machines"></a>Sanal makineleri çoğaltma

Tüm SAP uygulaması sanal makinelerini Azure olağanüstü durum kurtarma veri merkezine Çoğaltmaya başlamak için, [bir sanal makineyi Azure 'A çoğaltma](./azure-to-azure-tutorial-enable-replication.md)' daki yönergeleri izleyin.

* Active Directory ve DNS 'yi koruma hakkında yönergeler için [Active Directory ve DNS 'yi nasıl koruyacağınızı](site-recovery-active-directory.md)öğrenin.

* SQL Server çalışan veritabanı katmanını koruma hakkında rehberlik için [SQL Server nasıl koruyacağınızı](site-recovery-sql.md)öğrenin.

## <a name="networking-configuration"></a>Ağ yapılandırması

Statik bir IP adresi kullanırsanız, sanal makinenin kullanmasını istediğiniz IP adresini belirtebilirsiniz. IP adresini ayarlamak için **işlem ve ağ ayarları**  >  **ağ arabirimi kartına** gidin.

![Site Recovery ağ arabirimi kartı bölmesinde özel IP adresi ayarlamayı gösteren ekran görüntüsü](./media/site-recovery-sap/sap-static-ip.png)


## <a name="create-a-recovery-plan"></a>Kurtarma planı oluşturma

Kurtarma planı, yük devretme sırasında çok katmanlı bir uygulamadaki çeşitli katmanların sıralamasını destekler. Sıralama, uygulama tutarlılığını sürdürmenize yardımcı olur. Çok katmanlı bir Web uygulaması için bir kurtarma planı oluşturduğunuzda [Site Recovery kullanarak kurtarma planı oluşturma](site-recovery-create-recovery-plans.md)bölümünde açıklanan adımları uygulayın.

### <a name="add-virtual-machines-to-failover-groups"></a>Yük devretme gruplarına sanal makineler ekleme

1. Uygulama sunucusu, Web dağıtıcısı ve SAP Merkezi Hizmetler VM 'Leri ekleyerek bir kurtarma planı oluşturun.
1. VM 'Leri gruplamak için **Özelleştir** ' i seçin. Varsayılan olarak, tüm VM 'Ler Grup 1 ' in bir parçasıdır.

### <a name="add-scripts-to-the-recovery-plan"></a>Kurtarma planına betikler ekleme
Uygulamalarınızın düzgün çalışması için, Azure sanal makinelerinde bazı işlemler yapmanız gerekebilir. Yük devretmenin ardından veya yük devretme testi sırasında bu işlemleri yapın. Ayrıca, bazı yük devretme sonrası işlemleri otomatikleştirebilir. Örneğin, DNS girişini güncelleştirin ve kurtarma planına ilgili betikler ekleyerek bağlamaları ve bağlantıları değiştirin.

**Azure 'A dağıt**' a tıklayarak en çok kullanılan Site Recovery betikleri Azure Otomasyonu hesabınıza dağıtabilirsiniz. Yayımlanmış herhangi bir betiği kullandığınızda, betikteki yönergeleri izleyin.

[![Azure’a dağıtın](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. SQL Server kullanılabilirlik grubunun yükünü devretmek için Grup 1 ' e bir ön eylem betiği ekleyin. Örnek betiklerinizde yayınlanan ASR-SQL-FailoverAG betiğini kullanın. Betikteki yönergeleri izleyin ve betikte gerekli değişiklikleri uygun şekilde yapın.
1. Web katmanının (Grup 1) yükü devredilen sanal makinelerine yük dengeleyici eklemek için bir eylem sonrası betiği ekleyin. Örnek betiklerinizde yayınlanan ASR-AddSingleLoadBalancer betiğini kullanın. Betikteki yönergeleri izleyin ve gerekli değişiklikleri betikte gerekli şekilde yapın.

![SAP kurtarma planı](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>Yük devretme testi çalıştırma

1. Azure portal, kurtarma hizmetleri kasanızı seçin.
1. SAP uygulamaları için oluşturduğunuz kurtarma planını seçin.
1. **Yük Devretme Testi**'ni seçin.
1. Yük devretme testi işlemini başlatmak için kurtarma noktasını ve Azure sanal ağını seçin.
1. İkincil ortam çalışır duruma geldiğinde doğrulama gerçekleştirin.
1. Doğrulamalar tamamlandığında, yük **devretme testini temizle**' yi seçerek yük devretme ortamını temizleyin.

Daha fazla bilgi için bkz. [Azure 'a yük devretmeyi test etme Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Yük devretme çalıştırma

1. Azure portal, kurtarma hizmetleri kasanızı seçin.
1. SAP uygulamaları için oluşturduğunuz kurtarma planını seçin.
1. **Yük devretme**'yi seçin.
1. Yük devretme işlemini başlatmak için kurtarma noktasını seçin.

Daha fazla bilgi için bkz. [Site Recovery 'de yük devretme](site-recovery-failover.md).

## <a name="next-steps"></a>Sonraki adımlar
* Site Recovery kullanarak SAP NetWeaver dağıtımları için olağanüstü durum kurtarma çözümü oluşturma hakkında daha fazla bilgi edinin. Bkz. indirilebilir Teknik İnceleme [SAP NetWeaver: Site Recovery Ile olağanüstü durum kurtarma çözümü oluşturma](/samples/browse/?redirectedfrom=TechNet-Gallery). Teknik İnceleme, çeşitli SAP mimarilerine yönelik öneriler ele alınmaktadır. Azure 'da SAP için desteklenen uygulamaları ve VM türlerini görebilirsiniz. Ayrıca olağanüstü durum kurtarma çözümünüzü test etmek için plan seçenekleri de vardır.
* Site Recovery kullanarak [diğer iş yüklerini çoğaltma](site-recovery-workload.md) hakkında daha fazla bilgi edinin.