---
title: Azure Site Recovery ile olağanüstü durum kurtarma sırasında yük devretme
description: Azure Site Recovery hizmeti ile olağanüstü durum kurtarma sırasında VM 'Ler ve fiziksel sunuculardan yük devretmek hakkında bilgi edinin.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 10/29/2019
ms.author: raynew
ms.openlocfilehash: 1585c5dbdecf11bbc6ef3dad63bf4f982c70f73e
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053763"
---
# <a name="fail-over-vms-and-physical-servers"></a>Yük devretme VM 'Leri ve fiziksel sunucular 

Bu makalede, Site Recovery tarafından korunan sanal makinelerin ve fiziksel sunucuların nasıl yük devretmesinin nasıl yapılacağı açıklanır.

## <a name="prerequisites"></a>Önkoşullar
1. Yük devretme yapmadan önce, her şeyin beklendiği gibi çalıştığından emin olmak için bir [Yük devretme testi](site-recovery-test-failover-to-azure.md) yapın.
1. Yük devretme yapmadan önce ağı hedef konumda [hazırlayın](site-recovery-network-design.md) .  

Azure Site Recovery tarafından sunulan yük devretme seçenekleri hakkında bilgi edinmek için aşağıdaki tabloyu kullanın. Bu seçenekler, farklı yük devretme senaryoları için de listelenir.

| Senaryo | Uygulama kurtarma gereksinimi | Hyper-V için iş akışı | VMware için iş akışı
|---|--|--|--|
|Yaklaşan bir veri merkezi kapalı kalma süresi nedeniyle planlı yük devretme| Planlı bir etkinlik gerçekleştirildiğinde uygulama için sıfır veri kaybı| Hyper-V için ASR, verileri Kullanıcı tarafından belirtilen bir kopyalama sıklığında çoğaltır. Planlı Yük devretme, sıklığı geçersiz kılmak ve bir yük devretme başlatılmadan önce son değişiklikleri çoğaltmak için kullanılır. <br/> <br/> 1. işletmenizin değişiklik yönetimi süreci uyarınca bir bakım penceresi planlayın. <br/><br/> 2. yaklaşan kapalı kalma süresini kullanıcılara bildirin. <br/><br/> 3. kullanıcıya yönelik uygulamayı çevrimdışına alın.<br/><br/>4. ASR portalını kullanarak planlı yük devretmeyi başlatın. Şirket içi sanal makine otomatik olarak kapatılır.<br/><br/>Etkin uygulama veri kaybı = 0 <br/><br/>Kurtarma noktalarının günlüğü, daha eski bir kurtarma noktası kullanmak isteyen bir kullanıcının bekletme penceresinde de sağlanır. (Hyper-V için 24 saat bekletme). Çoğaltma, bekletme penceresinin zaman çerçevesinin ötesinde durdurulmuşsa, müşteriler hala kullanılabilir olan en son kurtarma noktalarını kullanarak yük devredebiliyor olabilir. | VMware için ASR, CDP kullanarak verileri sürekli çoğaltır. Yük devretme, kullanıcıya en son verilere yük devretme seçeneği verir (uygulama sonrası kapatma dahil)<br/><br/> 1. değişiklik yönetimi işlemine göre bir bakım penceresi planlayın <br/><br/>2. yaklaşan kapalı kalma süresini kullanıcılara bildirin <br/><br/>3. kullanıcıya yönelik uygulamayı çevrimdışına alın.<br/><br/>4. uygulama çevrimdışı olduktan sonra ASR portalını kullanarak planlanmış bir yük devretmeyi en son noktaya başlatın. Portalda "planlı yük devretme" seçeneğini kullanın ve yük devretme için en son noktayı seçin. Şirket içi sanal makine otomatik olarak kapatılır.<br/><br/>Etkin uygulama veri kaybı = 0 <br/><br/>Bir bekletme penceresindeki kurtarma noktalarının günlüğü, daha eski bir kurtarma noktası kullanmak isteyen bir müşteri için sağlanır. (VMware için 72 saat bekletme). Çoğaltma, bekletme penceresinin zaman çerçevesinin ötesinde durdurulmuşsa, müşteriler hala kullanılabilir olan en son kurtarma noktalarını kullanarak yük devredebiliyor olabilir.
|Planlanmamış bir veri merkezi kapalı kalma süresi nedeniyle yük devretme (doğal veya BT olağanüstü durum) | Uygulama için en az veri kaybı | 1. kuruluşun BCP planını başlatın <br/><br/>2. ASR portalını kullanarak planlanmamış yük devretme işlemini en son veya bir noktaya bekletme penceresinde (günlük) başlatın.| 1. kuruluşun BCP planını başlatın. <br/><br/>2. ASR portalını kullanarak planlanmamış yük devretme işlemini en son veya bir noktaya bekletme penceresinde (günlük) başlatın.


## <a name="run-a-failover"></a>Yük devretme çalıştırma
Bu yordam, bir [kurtarma planı](site-recovery-create-recovery-plans.md)için yük devretmeyi nasıl çalıştıracağınızı açıklar. Alternatif olarak, [burada](vmware-azure-tutorial-failover-failback.md#run-a-failover-to-azure)açıklandığı gibi **çoğaltılan öğeler** sayfasından tek bir sanal makine veya fiziksel sunucu için yük devretmeyi çalıştırabilirsiniz.


![Yük Devretme](./media/site-recovery-failover/Failover.png)

1. *Recoveryplan_name* > **Kurtarma planlarını** seçin. **Yük devretme** ' ye tıklayın
2. **Yük devretme** ekranında, yük devretme Için bir **Kurtarma noktası** seçin. Aşağıdaki seçeneklerden birini kullanabilirsiniz:
   1. **En son**: Bu seçenek, ilk olarak Site Recovery hizmetine gönderilen tüm verileri işleyerek işi başlatır. Verilerin işlenmesi, her sanal makine için bir kurtarma noktası oluşturur. Bu kurtarma noktası, yük devretme sırasında sanal makine tarafından kullanılır. Yük devretmeden sonra oluşturulan sanal makine, yük devretme tetiklendiğinde Site Recovery hizmetine çoğaltılan tüm verilere sahip olduğundan, bu seçenek en düşük RPO (kurtarma noktası hedefi) sağlar.
   1. **En son işlenen**: Bu seçenek, kurtarma planının tüm sanal makinelerini Site Recovery hizmeti tarafından zaten işlenmiş olan en son kurtarma noktasına devreder. Bir sanal makinenin yük devretme testini yaparken, son işlenen kurtarma noktasının zaman damgası da gösterilir. Bir kurtarma planında yük devretme yapıyorsanız, bu bilgileri almak için tek tek sanal makineye gidebilir ve **en son kurtarma noktaları** kutucuğuna bakabilirsiniz. İşlenmemiş verileri işlemek için zaman harcanmadan Bu seçenek, düşük bir RTO (kurtarma süresi hedefi) yük devretme seçeneği sağlar.
   1. **En son uygulamayla tutarlı**: Bu seçenek, kurtarma planının tüm sanal makinelerini, Site Recovery hizmeti tarafından zaten işlenmiş olan en son uygulamayla tutarlı kurtarma noktasına devreder. Bir sanal makinenin yük devretme testini yaparken, uygulamayla tutarlı en son kurtarma noktasının zaman damgası da gösterilir. Bir kurtarma planında yük devretme yapıyorsanız, bu bilgileri almak için tek tek sanal makineye gidebilir ve **en son kurtarma noktaları** kutucuğuna bakabilirsiniz.
   1. **Işlenen en son çoklu VM**: Bu seçenek yalnızca çoklu VM tutarlılığı olan en az bir sanal makineye sahip kurtarma planlarında kullanılabilir. Çoğaltma grubunun bir parçası olan sanal makineler en son ortak çoklu VM tutarlı kurtarma noktasına yük devretmeye. Diğer sanal makineler, son işlenen kurtarma noktasına yük devreder.  
   1. **En son çoklu VM ile uygulamayla tutarlı**: Bu seçenek yalnızca çoklu VM tutarlılığı olan en az bir sanal makineye sahip kurtarma planları için kullanılabilir. Çoğaltma grubunun bir parçası olan sanal makineler, en son ortak çoklu VM uygulama ile tutarlı kurtarma noktasına yük devretmeye olanak sağlar. Diğer sanal makineler, uygulamayla tutarlı en son kurtarma noktasına yük devreder.
   1. **Özel**: bir sanal makinenin yük devretme testini yapıyorsanız, belirli bir kurtarma noktasına yük devretmek için bu seçeneği kullanabilirsiniz.

      > [!NOTE]
      > Kurtarma noktası seçme seçeneği yalnızca Azure 'a yük devrettikten sonra kullanılabilir.
      >
      >


1. Kurtarma planındaki bazı sanal makineler önceki bir çalıştırmada yük devretmişse ve artık sanal makineler hem kaynak hem de hedef konumda etkinse, yük devretmenin gerçekleşmesi için yön **değiştirme** seçeneğini kullanabilirsiniz.
1. Azure 'a yük devretmek ve bulut için veri şifreleme etkinleştirilmişse (yalnızca VMM sunucusundan Hyper-v sanal makinelerini koruduysanız geçerlidir), **şifreleme anahtarı** ' nda, veri şifrelemeyi etkinleştirdiğinizde verilen sertifikayı seçin VMM sunucusunda kurulum.
1. Yük devretmeyi tetiklemeden önce Site Recovery kaynak sanal makineleri kapatmayı denemek istiyorsanız, **yük devretmeye başlamadan önce kapatma makinesi** ' ni seçin. Yük devretme, kapatma başarısız olsa bile devam eder.  

    > [!NOTE]
    > Hyper-v sanal makineleri korunuyorsa, kapatma seçeneği, yük devretmeyi tetiklemeden önce henüz hizmete gönderilmemiş olan şirket içi verileri eşitlemeye çalışır.
    >
    >

1. Yük devretme işlemini **İşler** sayfasında takip edebilirsiniz. Planlanmamış bir yük devretme sırasında hatalar gerçekleşse bile, kurtarma planı tamamlanana kadar çalışır.
1. Yük devretmeden sonra, sanal makineyi oturum açarak doğrulayın. Sanal makinenin başka bir kurtarma noktasına geçmek istiyorsanız, **kurtarma noktasını Değiştir** seçeneğini kullanabilirsiniz.
1. Yük devredilmiş sanal makineden memnun kaldığınızda, yük devretmeyi **Yürütebilirsiniz**. **Yürüt hizmeti ile kullanılabilen tüm kurtarma noktalarını siler** ve **kurtarma noktasını Değiştir** seçeneği artık kullanılamaz.

## <a name="planned-failover"></a>Planlı Yük devretme
Site Recovery kullanılarak korunan sanal makineler/fiziksel sunucular, **Planlı Yük devretmeyi**da destekler. Planlı Yük devretme, sıfır veri kaybı yük devretme seçeneğidir. Planlı bir yük devretme tetiklendiğinde, ilk olarak kaynak sanal makineler kapatılır, en son veriler eşitlenir ve ardından bir yük devretme tetiklenir.

> [!NOTE]
> Hyper-v sanal makinelerinin şirket içi bir siteden başka bir şirket içi siteye yük devretmesi sırasında, birincil şirket içi siteye geri dönmek **için öncelikle sanal** makineyi birincil siteye geri ve sonra bir yük devretme tetiklemeniz gerekir. Birincil sanal makine kullanılabilir durumda değilse, **ters çoğaltmaya** başlamadan önce, sanal makineyi bir yedekten geri yüklemeniz gerekir.   
 
 
## <a name="failover-job"></a>Yük devretme işi

![Yük Devretme](./media/site-recovery-failover/FailoverJob.png)

Yük devretme tetiklendiğinde, aşağıdaki adımları içerir:

1. Önkoşul denetimi: Bu adım, yük devretme için gereken tüm koşulların karşılanmasını sağlar
1. Yük devretme: Bu adım, verileri işler ve bir Azure sanal makinesinin oluşturulabilmesi için hazır hale getirir. **En son** kurtarma noktasını seçtiyseniz, bu adım hizmete gönderilen verilerden bir kurtarma noktası oluşturur.
1. Başlangıç: Bu adım, önceki adımda işlenen verileri kullanarak bir Azure sanal makinesi oluşturur.

> [!WARNING]
> **Devam eden yük devretme Işlemini Iptal etmeyin**: yük devretme başlatılmadan önce, sanal makine için çoğaltma durdurulur. Devam eden bir işi **iptal** ederseniz, yük devretme işlemi duraklar ancak sanal makine çoğaltılmak üzere başlamaz. Çoğaltma yeniden başlatılamaz.
>
>

## <a name="time-taken-for-failover-to-azure"></a>Azure 'a yük devretme için geçen süre

Belirli durumlarda, sanal makinelerin yük devretmesi, genellikle yaklaşık 8 ila 10 dakikalık bir adım sürer. Aşağıdaki durumlarda, yük devretme için geçen süre normalden daha yüksek olacaktır:

* 9,8 'den eski sürüm Mobility hizmetini kullanan VMware sanal makineleri
* Fiziksel sunucular
* VMware Linux sanal makineleri
* Fiziksel sunucu olarak korunan Hyper-V sanal makineleri
* Aşağıdaki sürücülerin önyükleme sürücüleri olarak bulunmadığı VMware sanal makineleri
    * storvsc
    * VMBus
    * storflt
    * intelide
    * ATAPI
* DHCP hizmeti etkinleştirilmemiş olan VMware sanal makineleri, DHCP veya statik IP adresleri mi kullandıklarından bağımsız olarak etkindir

Diğer tüm durumlarda, bu ara adım gerekli değildir ve yük devretme için geçen süre düşüktür.

## <a name="using-scripts-in-failover"></a>Yük devretmede betikleri kullanma
Yük devretme yaparken belirli eylemleri otomatikleştirmek isteyebilirsiniz. Bunu yapmak için, [Kurtarma planlarında](site-recovery-create-recovery-plans.md) betikleri veya [Azure Otomasyonu runbook 'larını](site-recovery-runbook-automation.md) kullanabilirsiniz.

## <a name="post-failover-considerations"></a>Yük devretme sonrası konuları
Yük devretme sonrası aşağıdaki önerileri göz önünde bulundurmanız gerekebilir:
### <a name="retaining-drive-letter-after-failover"></a>Yük devretmeden sonra sürücü harfini koruma
Azure Site Recovery sürücü harflerinin bekletilmesini yönetir. Bazı diskleri dışlamayı seçtiğinizde, nasıl yapıldığı hakkında [daha fazla bilgi edinin](vmware-azure-exclude-disk.md#example-1-exclude-the-sql-server-tempdb-disk) .

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Yük devretmeden sonra Azure VM'lerine bağlanmak için hazırlık yapma

Yük devretme sonrasında RDP/SSH kullanarak Azure VM'lerine bağlanmak istiyorsanız [buradaki](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) tabloda özetlenen gereksinimleri izleyin.

Yük devretme sonrasında karşılaştığınız bağlantı sorunlarını gidermek için [burada](site-recovery-failover-to-azure-troubleshoot.md) anlatılan adımları izleyin.


## <a name="next-steps"></a>Sonraki adımlar

> [!WARNING]
> Sanal makinelerin yük devretmesini ve şirket içi veri merkezini kullanıma aldıktan sonra, VMware sanal makinelerini şirket içi veri merkezine yeniden [**korumalısınız**](vmware-azure-reprotect.md) .

Hyper-v sanal makinelerini Azure 'dan şirket içine yeniden **çalışma** için [**Planlı Yük devretme**](hyper-v-azure-failback.md) seçeneğini kullanın.

Hyper-v sanal makinesini bir VMM sunucusu tarafından yönetilen başka bir şirket içi veri merkezine devretmenize ve birincil veri merkezi kullanılabilir durumda değilse çoğaltmayı birincil veri merkezine yeniden başlatmak için **ters Çoğalt** seçeneğini kullanın.
