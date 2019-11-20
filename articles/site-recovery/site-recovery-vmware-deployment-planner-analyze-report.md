---
title: VMware olağanüstü durum kurtarma Dağıtım Planlayıcısı raporunu Azure Site Recovery ile çözümleyin
description: Bu makalede, Azure Site Recovery kullanılarak Azure 'da VMware olağanüstü durum kurtarma için kurtarma Dağıtım Planlayıcısı tarafından oluşturulan raporun nasıl çözümleneceği açıklanır.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: mayg
ms.openlocfilehash: b6ac10b47a8bbc987eb1e338991100ee17eacd61
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961372"
---
# <a name="analyze-the-deployment-planner-report-for-vmware-disaster-recovery-to-azure"></a>Azure 'da VMware olağanüstü durum kurtarma için Dağıtım Planlayıcısı raporunu analiz etme

Oluşturulan Microsoft Excel raporu şu sayfaları içerir:
## <a name="on-premises-summary"></a>Şirket içi özeti
Şirket içi özeti çalışma sayfası, profili oluşturulmuş VMware ortamına genel bir bakış sağlar.

![VMware ortamının şirket içi özeti](media/site-recovery-vmware-deployment-planner-analyze-report/on-premises-summary-v2a.png)

**Başlangıç Tarihi** ve **Bitiş Tarihi**: Rapor oluşturma için göz önünde bulundurulan profil oluşturma verilerinin başlangıç ve bitiş tarihleri. Varsayılan olarak, başlangıç tarihi profil oluşturmanın başladığı, bitiş tarihi ise profil oluşturmanın durdurulduğu tarihtir. Rapor bu parametrelerle oluşturulduysa bunlar ‘StartDate’ ve ‘EndDate’ değerleri olabilir.

**Profil oluşturulan toplam gün sayısı**: Raporun oluşturulduğu başlangıç ile bitiş tarihleri arasında profil oluşturulan toplam gün sayısı.

**Uyumlu sanal makine sayısı**: Gerekli ağ bant genişliği, gerekli depolama hesabı sayısı, Microsoft Azure çekirdek sayısı ve yapılandırma sunucusu ile ek işlem sunucularının sayısının hesaplandığı uyumlu sanal makinelerin toplamı.

**Tüm uyumlu sanal makinelerdeki toplam disk sayısı**: Bu sayı, dağıtımda kullanılacak yapılandırma sunucusu ve ek işlem sunucusu sayısına karar vermeye yönelik girdilerden biri olarak kullanılır.

**Bir uyumlu sanal makinedeki ortalama disk sayısı**: Tüm uyumlu sanal makinelerde hesaplanan ortalama disk sayısıdır.

**Ortalama disk boyutu (GB)** : Tüm uyumlu sanal makinelerde hesaplanan ortalama disk boyutudur.

**İstenen RPO (dakika)** : Varsayılan kurtarma noktası hedefi veya rapor oluşturma sırasında gerekli bant genişliğini tahmin etmek üzere ‘DesiredRPO’ parametresi için geçirilen değer.

**İstenen bant genişliği (Mb/sn)** : Rapor oluşturma sırasında ulaşılabilir RPO’yu tahmin etmek üzere ‘Bandwidth’ parametresi için geçirdiğiniz değerdir.

**Bir günde gözlemlenen tipik veri değişim sıklığı (GB)** : Profil oluşturulan tüm günlerde gözlemlenen ortalama veri değişim sıklığıdır. Bu sayı, dağıtımda kullanılacak yapılandırma sunucusu ve ek işlem sunucusu sayısına karar vermeye yönelik girdilerden biri olarak kullanılır.

## <a name="recommendations"></a>Öneriler

VMware’den Azure'a dağıtım raporunun öneriler sayfasında, seçilen ve istenen RPO'ya göre aşağıdaki ayrıntılar yer alır:

![VMware'den Azure'a dağıtım raporu için öneriler](media/site-recovery-vmware-deployment-planner-analyze-report/Recommendations-v2a.png)

### <a name="profiled-data"></a>Profili oluşturulan veriler
![Dağıtım planlayıcısında profili oluşturulmuş veriler görünümü](media/site-recovery-vmware-deployment-planner-analyze-report/profiled-data-v2a.png)

**Profili oluşturulmuş veri süresi**: Profil oluşturma işleminin gerçekleştirildiği süre. Varsayılan olarak, rapor oluşturma sırasında StartDate ve EndDate seçenekleri kullanılarak rapor belirli bir süre için oluşturulmadıkça araç, profili oluşturulmuş tüm verileri hesaplamaya dahil eder.

**Sunucu Adı**: sanal makinelerinin raporu oluşturulan VMware vCenter veya ESXi ana bilgisayarının adı ya da IP adresi.

**İstenen RPO**: Dağıtımınıza yönelik kurtarma noktası hedefi. Varsayılan olarak, gerekli ağ bant genişliği 15, 30 ve 60 dakikalık RPO değerleri için hesaplanır. Seçim temel alınarak, etkilenen değerler sayfada güncelleştirilir. Raporu oluştururken *DesiredRPOinMin* parametresini kullandıysanız, değer İstenen RPO sonucunda gösterilir.

### <a name="profiling-overview"></a>Profil oluşturmaya genel bakış

![Dağıtım planlayıcısında profil oluşturma sonuçları](media/site-recovery-vmware-deployment-planner-analyze-report/profiling-overview-v2a.png)

**Profili Oluşturulan Toplam Sanal Makine Sayısı**: Profili oluşturulan verileri bulunan sanal makinelerin toplam sayısı. VMListFile dosyasında profili oluşturulmamış sanal makineler varsa, bu sanal makineler rapor oluşturma işleminde göz önünde bulundurulmaz ve profili oluşturulan toplam sanal makine sayısının dışında tutulur.

**Uyumlu Sanal Makineler**: Site Recovery kullanılarak Azure’da korunabilen sanal makine sayısı. Bu sayı, gerekli ağ bant genişliği, depolama hesabı sayısı, Azure çekirdek sayısı ve yapılandırma sunucusu ile ek işlem sunucularının sayısının hesaplandığı uyumlu sanal makinelerin toplamıdır. Her uyumlu VM’nin ayrıntıları "Uyumlu VM’ler" bölümünde bulunabilir.

**Uyumsuz Sanal Makineler**: Site Recovery ile koruma için uygun olmayan, profili oluşturulmuş sanal makine sayısı. Uyumsuzluğun nedenleri, “Uyumsuz VM’ler” bölümünde belirtilmiştir. VMListFile içinde profili oluşturulmamış bir sanal makinenin adı varsa, bu sanal makineler uyumsuz sanal makine sayısının dışında bırakılır. Bu sanal makineler, “Uyumsuz VM’ler” bölümünün sonunda “Veri bulunamadı” olarak listelenir.

**İstenen RPO**: Dakika cinsinden istediğiniz kurtarma noktası hedefi. Rapor üç RPO değeri için oluşturulur: 15 (varsayılan), 30 ve 60 dakika. Rapordaki bant genişliği önerisi, tablonun sağ üst köşesinde bulunan İstenen RPO açılır listesindeki seçiminize göre değişir. Raporu özel bir değer ile *-DesiredRPO* parametresini kullanarak oluşturduysanız, bu özel değer İstenen RPO açılır listesinde varsayılan olarak gösterilir.

### <a name="required-network-bandwidth-mbps"></a>Gerekli ağ bant genişliği (Mb/sn)

![Dağıtım planlayıcısında gerekli ağ bant genişliği](media/site-recovery-vmware-deployment-planner-analyze-report/required-network-bandwidth-v2a.png)

**Yüzde 100 RPO süresini karşılamak için:** İstediğiniz yüzde 100 RPO süresini karşılamak için Mb/sn cinsinden ayrılacak önerilen bant genişliğidir. Bu bant genişliği miktarı, herhangi bir RPO ihlalini önlemek üzere tüm uyumlu sanal makinelerinizin kararlı durum delta çoğaltması için ayrılmalıdır.

**Yüzde 90 RPO süresini karşılamak için**: Geniş bant fiyatlandırması veya istediğiniz yüzde 100 RPO süresini karşılamak için gereken bant genişliğini ayarlayamamanız durumunda başka bir nedenle, istediğiniz yüzde 90 RPO süresini karşılayabilen daha düşük bir bant genişliği ayarlamayı seçebilirsiniz. Daha düşük olan bu bant genişliğini ayarlamanın etkilerini anlamak için, raporda beklenen RPO ihlallerinin sayısı ve süresine ilişkin bir ne yapmalı analizi sağlar.

**Elde Edilen Aktarım Hızı**: Depolama hesabının bulunduğu Microsoft Azure bölgesine GetThroughput komutunu gönderdiğiniz sunucudan aktarım hızıdır. Bu aktarım hızı sayısı, yapılandırma sunucusu veya işlem sunucusu depolama ve ağ özelliklerinin, aracı çalıştırdığınız sunucudaki özelliklerle aynı kalması şartıyla, Site Recovery kullanarak uyumlu VM’leri korurken elde edebileceğiniz tahmini düzeyi gösterir.

Çoğaltma için, önerilen bant genişliğini sürenin yüzde 100’ünü karşılayacak şekilde ayarlamanız gerekir. Bant genişliğini ayarladıktan sonra araç tarafından ulaşıldığı bildirilen aktarım hızında herhangi bir artış görmezseniz aşağıdakileri yapın:

1. Site Recovery aktarım hızını sınırlayan herhangi bir ağ Hizmet Kalitesi (QoS) olup olmadığını denetleyin.

2. Ağ gecikme süresini en aza indirmek için, Site Recovery kasanızın desteklenen en yakın fiziksel Microsoft Azure bölgesinde olup olmadığını denetleyin.

3. Donanımı iyileştirip iyileştiremeyeceğinizi (örneğin, HDD’den SSD’ye) belirlemek için yerel depolama özelliklerini denetleyin.

4. [Çoğaltma için kullanılan ağ bant genişliği miktarını artırmak](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth) üzere işlem sunucusundaki Site Recovery ayarlarını değiştirin.

Aracı korunan sanal makinelere zaten sahip olan bir yapılandırma sunucusu veya işlem sunucusu üzerinde çalıştırıyorsanız, aracı birkaç kez çalıştırın. Elde edilen aktarım hızı sayısı, zamanın o noktasında işlenen değişim hızı miktarına bağlı olarak değişir.

Tüm kurumsal Site Recovery dağıtımları için [ExpressRoute](https://aka.ms/expressroute) kullanılması önerilir.

### <a name="required-storage-accounts"></a>Gerekli depolama hesapları
Aşağıdaki grafikte tüm uyumlu sanal makineleri korumak için gereken depolama hesaplarının (standart ve premium) toplam sayısı gösterilmektedir. Her bir VM için kullanılacak depolama hesabını öğrenmek için "VM depolama yerleşimi" bölümüne bakın. V 2.5 for Dağıtım Planlayıcısı kullanıyorsanız, bu öneri yalnızca veriler doğrudan yönetilen disklere yazıldığı için çoğaltma için gereken standart önbellek depolama hesaplarının sayısını gösterir.

![Dağıtım planlayıcısında gerekli depolama hesapları](media/site-recovery-vmware-deployment-planner-analyze-report/required-storage-accounts-v2a.png)

### <a name="required-number-of-azure-cores"></a>Gerekli Azure çekirdek sayısı
Bu sonuç, tüm uyumlu sanal makinelerin yük devretme işlemi ya da yük devretme testi öncesinde ayarlanması gereken toplam çekirdek sayısıdır. Abonelikte çok az sayıda çekirdek varsa, yük devretme testi veya yük devretme sırasında Azure Site Recovery, sanal makineleri oluşturamaz.

![Dağıtım planlayıcısında gereken Azure çekirdek sayısı](media/site-recovery-vmware-deployment-planner-analyze-report/required-cores-v2a.png)

### <a name="required-on-premises-infrastructure"></a>Gerekli şirket içi altyapısı
Bu sayı, tüm uyumlu sanal makineleri korumak için yapılandırılması gereken yapılandırma sunucusu ve ek işlem sunucularının toplam sayısıdır. [Yapılandırma sunucusu için desteklenen boyut önerilerine](https://aka.ms/asr-v2a-on-prem-components) bağlı olarak, araç ek sunucular önerebilir. Öneri, günlük değişim sıklığı veya en fazla korunan VM sayısından (VM başına ortalama üç disk olduğu varsayılarak) yapılandırma sunucusu veya ek işlem sunucusunda ilk gerçekleşen olaya göre yapılır. Günlük toplam değişim sıklığı ve toplam korunan disk sayısına ilişkin ayrıntıları “Şirket içi özeti” bölümünde bulabilirsiniz.

![Dağıtım planlayıcısında gerekli şirket içi altyapı](media/site-recovery-vmware-deployment-planner-analyze-report/required-on-premises-components-v2a.png)

### <a name="what-if-analysis"></a>Benzetim analiz
Bu analiz, sürenin yalnızca yüzde 90’ını karşılamasını istediğiniz RPO için daha düşük bir bant genişliği ayarladığınızda profil oluşturma sırasında kaç tane ihlal oluşabileceğini özetler. Belirli bir günde bir veya daha fazla RPO ihlali ortaya çıkabilir. Graf, günün yoğun RPO değerini gösterir.
Bu analizi temel alarak, belirtilen düşük bant genişliği ile tüm günlerdeki RPO ihlali sayısının ve bir gündeki en yoğun RPO gerçekleşme zamanının kabul edilebilir olup olmadığına karar verebilirsiniz. Değer kabul edilebilir düzeydeyse, çoğaltma için düşük bant genişliği ayırabilirsiniz; aksi takdirde, istenilen yüzde 100 RPO süresini karşılamak üzere önerilen yüksek bant genişliğini ayırmanız gerekir.

![Dağıtım planlayıcısında benzetim analizi](media/site-recovery-vmware-deployment-planner-analyze-report/what-if-analysis-v2a.png)

### <a name="recommended-vm-batch-size-for-initial-replication"></a>İlk çoğaltma için önerilen VM toplu iş boyutu
Bu bölümde, ayarlanmakta olan sürenin %100 RPO değerini karşılamak amacıyla ilk çoğaltmayı önerilen bant genişliği ile 72 saat içinde tamamlamak için paralel olarak korunması gereken sanal makine sayısı önerilmektedir. Bu değer yapılandırılabilir bir değerdir. Rapor oluşturma zamanında bu değeri değiştirmek için *GoalToCompleteIR* parametresini kullanın.

Buradaki grafikte, tüm uyumlu makinelerde algılanan ortalama sanal makine boyutuna göre 72 saat içinde ilk çoğaltmayı tamamlamak için bir bant genişliği değer aralığı ve hesaplanmış sanal makine toplu iş boyutu sayısı gösterilmektedir.

Genel önizleme sürümünde rapor, bir toplu işe hangi sanal makinelerin dahil edilmesi gerektiğini belirtmez. Her bir sanal makinenin boyutunu bulmak ve bir toplu işe yönelik sanal makinelerinizi seçmek ya da bilinen iş yükü özelliklerine göre seçim yapmak için, “Uyumlu VM’ler” bölümünde gösterilen disk boyutunu kullanabilirsiniz. İlk çoğaltmayı tamamlama süresi, gerçek VM disk boyutu, kullanılan disk alanı ve kullanılabilir ağ aktarım hızı ile doğru orantılı bir şekilde değişir.

![Önerilen VM toplu iş boyutu](media/site-recovery-vmware-deployment-planner-analyze-report/ir-batching-v2a.png)

### <a name="cost-estimation"></a>Maliyet tahmini
Grafta, seçtiğiniz hedef bölgede ve rapor oluşturma için belirttiğiniz para biriminde Azure'a tahmini toplam olağanüstü durum kurtarma (DR) maliyetinin özet görünümü gösterilir.

![Maliyet tahmini özeti](media/site-recovery-vmware-deployment-planner-analyze-report/cost-estimation-summary-v2a.png)

Bu özet Azure Site Recovery kullanarak tüm uyumlu sanal makinelerinizi Azure'da koruduğunuzda ödemeniz gereken depolama, bilgi işlem, ağ ve lisansın maliyetini anlamanıza yardımcı olur. Maliyet, tüm profili oluşturulan sanal makineler için değil uyumlu sanal makineler için hesaplanır.  

Aylık veya yıllık maliyeti görüntüleyebilirsiniz. [Desteklenen hedef bölgeler](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-target-regions) ve [desteklenen para birimleri](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-currencies) hakkında daha fazla bilgi edinin.

**Bileşenlere göre maliyet** Toplam DR dört bileşene bölünür: İşlem, Depolama, Ağ ve Azure Site Recovery lisansı maliyeti. Maliyet, şirket içi siteyle Azure arasında ve yapılandırılan işlem, depolama (premium ve standart) ExpressRoute/VPN ve Azure Site Recovery lisansı için çoğaltma sırasında ve DR tatbikatında tahakkuk ettirilecek tüketim temelinde hesaplanır.

**Durumlara göre maliyet** Toplam olağanüstü durum kurtarma (DR) maliyeti, iki farklı duruma göre kategorilere ayrılır: Çoğaltma ve DR tatbikatı.

**Çoğaltma maliyeti**: Çoğaltma sırasında tahakkuk ettirilen maliyet. Depolama, ağ ve Azure Site Recovery lisansı maliyetini kapsar.

**DR Tatbikatı maliyeti**: Yük devretme testi sırasında tahakkuk ettirilen maliyet. Azure Site Recovery, yük devretme testi sırasında sanal makineleri çalıştırır. DR tatbikatı maliyeti, çalıştırılan sanal makinelerin işlem ve depolama maliyetini kapsar.

**Ay/Yıl başına Azure depolama maliyeti** Çoğaltma ve DR tatbikatının premium ve standart depolaması için tahakkuk ettirilecek toplam depolama maliyetini gösterir.
[Maliyetini Tahmini](site-recovery-vmware-deployment-planner-cost-estimation.md) sayfasında VM başına ayrıntılı maliyet analizini görüntüleyebilirsiniz.

### <a name="growth-factor-and-percentile-values-used"></a>Kullanılan büyüme faktörü ve yüzdelik değerler
Tablonun alt kısmındaki bu bölümde, profili oluşturulan sanal makinelerin tüm performans sayaçları için kullanılan yüzdelik dilim değeri (varsayılan değer yüzde 95’lik dilim) ve tüm hesaplamalarda kullanılan büyüme faktörü (varsayılan değer yüzde 30) gösterilmektedir.

![Kullanılan büyüme faktörü ve yüzdelik değerler](media/site-recovery-vmware-deployment-planner-analyze-report/growth-factor-v2a.png)

## <a name="recommendations-with-available-bandwidth-as-input"></a>Girdi olarak kullanılabilir bant genişliği ile ilgili öneriler

![Girdi olarak kullanılabilir bant genişliği ile ilgili öneriler](media/site-recovery-vmware-deployment-planner-analyze-report/profiling-overview-bandwidth-input-v2a.png)

Site Recovery çoğaltması için x MB/sn’den fazla bant genişliği ayarlayamayacağınızı bildiğiniz bir durumla karşılaşabilirsiniz. Araç, kullanılabilir bant genişliğini girmenize (rapor oluşturma sırasında -Bandwidth parametresini kullanarak) ve dakika cinsinden ulaşılabilir RPO değerini almanıza olanak tanır. Bu ulaşılabilir RPO değeriyle ek bant genişliği ayarlamanızın gerekli olup olmadığına veya bu RPO ile bir olağanüstü durum kurtarma çözümüne sahip olmak isteyip istemediğinize karar verebilirsiniz.

![500 MB/sn bant genişliği için elde edilebilen RPO](media/site-recovery-vmware-deployment-planner-analyze-report/achievable-rpo-v2a.png)

## <a name="vm-storage-placement"></a>VM-depolama yerleşimi

>[!Note]
>Dağıtım Planlayıcısı v 2.5 onmalları, doğrudan yönetilen disklere çoğaltılacak makineler için depolama yerleşimini önerir.

![VM-depolama yerleşimi](media/site-recovery-vmware-deployment-planner-analyze-report/vm-storage-placement-v2a.png)

**Çoğaltma depolama türü**: sütun **yerleştirilecek VM** 'Lerde bahsedilen tüm ilgili sanal makineleri çoğaltmak için kullanılan standart veya Premium yönetilen bir disk.

**Günlük depolama hesabı türü**: tüm çoğaltma günlükleri standart depolama hesabında depolanır.

**Depolama hesabı Için önerilen önek**: önbellek depolama hesabını adlandırırken kullanılabilecek üç karakterlik önerilen ön ek. Kendi ön ekinizi kullanabilirsiniz, ancak aracın önerisi [depolama hesapları için bölüm adlandırma kuralına](https://aka.ms/storage-performance-checklist) uygundur.

**Önerilen günlük hesabı adı**: önerilen ön eki ekledikten sonra depolama hesabı adı. Köşeli ayraç (< ve >) içindeki adı özel girdinizle değiştirin.

**Yerleştirme Özeti**: depolama türüne göre korunan VM 'lere gereken disklerin bir özeti. Toplam sanal makine sayısı, tüm disklerde toplam sağlanan boyut ve toplam disk sayısı dahildir.

**Yerleştirilecek Sanal Makineler**: En iyi performans ve kullanım için belirli bir depolama hesabına yerleştirilmesi gereken tüm sanal makinelerin listesi.

## <a name="compatible-vms"></a>Uyumlu VM’ler
![Uyumlu VM'lerin Excel elektronik tablosu](media/site-recovery-vmware-deployment-planner-analyze-report/compatible-vms-v2a.png)

**VM Adı**: Bir rapor oluşturulurken VMListFile içinde kullanılan VM adı veya IP adresi. Bu sütunda ayrıca sanal makinelere bağlanan diskler (VMDK) listelenir. Yinelenen adlara veya IP adreslerine sahip vCenter sanal makinelerini birbirinden ayırt etmek için, adlar ESXi ana bilgisayar adını içerir. Listelenen ESXi ana bilgisayarı, profil oluşturma sırasında araç keşfettiğinde VM’in yerleştirildiği yerdir.

**VM Uyumluluğu**: Değerler **Evet** ve **Evet**\* şeklindedir. **Evet**\*, VM 'Nin [Premium SSD 'ler](../virtual-machines/windows/disks-types.md)için uygun olduğu örneklere yöneliktir. Burada, profili oluşturulmuş yüksek değişim sıklığı veya IOPS diski P20 ya da P30 kategorisine uyar, ancak diskin boyutu diskin bir P10 veya P20 ile eşlenmesine neden olur. Depolama hesabı, bir diskin boyutuna göre hangi premium depolama disk türüne eşleneceğine karar verir. Örneğin:
* <128 GB bir P10’dur.
* 128 GB ile 256 GB arası P15'tir
* 256 GB ile 512 GB arası P20'dir.
* 512 GB ile 1024 GB arası P30'dur.
* 1025 GB ile 2048 GB arası P40'dır.
* 2049 GB ile 4095 GB arası P50'dir.

Bir diskin iş yükü özellikleri diski P20 veya P30 kategorisine koyarken boyutu nedeniyle daha düşük bir premium depolama disk türüne eşleniyorsa, araç bu VM’yi **Evet**\* olarak işaretler. Araç ayrıca kaynak disk boyutunu önerilen premium depolama disk türüne uyacak şekilde değiştirmenizi veya hedef disk türünü yük devretme sonrasını değiştirmenizi önerir.

**Depolama Türü**: Standart veya Premium.

**Çoğaltma için oluşturulan Asrseeddisk (yönetilen disk)** : çoğaltmayı etkinleştirdiğinizde oluşturulan diskin adı. Verileri ve anlık görüntülerini Azure 'da depolar.

**En Yüksek Okuma/Yazma IOPS (Büyüme Faktörü ile)** : Disk üzerinde gelecekteki büyüme faktörünü de (varsayılan değer yüzde 30) içeren en yoğun iş yükü okuma/yazma IOPS değeridir (varsayılan değer yüzde 95’lik dilim). Sanal makinenin en yoğun okuma/yazma IOPS değeri profil oluşturma işleminin her dakikası boyunca tek disklerin okuma/yazma IOPS değerinin toplamı olduğundan, bir sanal makinenin toplam okuma/yazma IOPS değeri her zaman sanal makinenin ayrı disklerinin okuma/yazma IOPS değerine eşit değildir.

**Mb/sn cinsinden En Yoğun Veri Değişim Sıklığı (Büyüme Faktörü ile)** : Disk üzerinde gelecekteki büyüme faktörünü de (varsayılan değer yüzde 30) içeren en yüksek erime oranıdır (varsayılan değer yüzde 95’lik dilim). Sanal makinenin en yoğun veri değişim sıklığı, profil oluşturma döneminin her dakikasında içindeki ayrı disklerin veri değişim sıklığı toplamının tepe noktası olduğundan, sanal makinenin toplam veri değişim sıklığı her zaman sanal makinedeki ayrı disklerin veri değişim sıklığının toplamı olmayacaktır.

**Azure VM Boyutu**: Bu şirket içi sanal makine için eşlenen ideal Azure Cloud Services makine boyutudur. Eşleme, şirket içi sanal makinenin belleğine, disk/çekirdek/ağ arabirimi sayısına ve okuma/yazma IOPS değerine bağlıdır. Her zaman şirket içi VM özelliklerinin tümüyle eşleşen en düşük Azure VM boyutunun kullanılması önerilir.

**Disk Sayısı**: Sanal makine üzerindeki toplam disk sayısı (VMDK).

**Disk boyutu (GB)** : Sanal makinenin tüm disklerinin toplam kurulum boyutu. Araç ayrıca sanal makinedeki ayrı diskler için disk boyutunu gösterir.

**Çekirdek**: Sanal makine üzerindeki CPU çekirdeği sayısı.

**Bellek (MB)** : VM üzerindeki RAM.

**NIC**: VM üzerindeki NIC sayısı.

**Önyükleme Türü**: Sanal makinenin önyükleme türü. BIOS veya EFI olabilir.  Azure Site Recovery şu anda önyükleme diskindeki bölüm sayısı 4’ten az ve önyükleme kesimi boyutu 512 bayt olmak şartıyla Windows Server EFI VM’lerini (Windows Server 2012, 2012 R2 ve 2016) desteklemektedir. EFI VM'lerini korumak için Azure Site Recovery hareketlilik hizmeti sürümü 9.13 veya üstü olmalıdır. EFI VM'ler için yalnızca yük devretme desteklenir. Yeniden çalışma desteklenmez.  

**İşletim sistemi türü**: VM’nin işletim sistemi türüdür. VM oluşturulurken VMware vSphere’den seçilen şablona bağlı olarak Windows veya Linux ya da başka bir işletim sistemi olabilir.  

## <a name="incompatible-vms"></a>Uyumsuz VM’ler

![Uyumsuz VM'lerin Excel elektronik tablosu
](media/site-recovery-vmware-deployment-planner-analyze-report/incompatible-vms-v2a.png)

**VM Adı**: Bir rapor oluşturulurken VMListFile içinde kullanılan VM adı veya IP adresi. Bu sütunda ayrıca sanal makinelere bağlanan VMDK’lar listelenir. Yinelenen adlara veya IP adreslerine sahip vCenter sanal makinelerini birbirinden ayırt etmek için, adlar ESXi ana bilgisayar adını içerir. Listelenen ESXi ana bilgisayarı, profil oluşturma sırasında araç keşfettiğinde VM’in yerleştirildiği yerdir.

**VM Uyumluluğu**: Belirli bir sanal makinenin, Site Recovery ile kullanım için neden uyumlu olmadığını gösterir. Sanal makinenin her uyumsuz diski için, yayımlanan [depolama sınırlarına](https://aka.ms/azure-storage-scalbility-performance) göre nedenler aşağıdakilerden biri olabilir:

* Disk boyutu > 4095 GB’dir. Azure Depolama şu anda 4095 GB’den büyük veri diski boyutlarını desteklememektedir.

* İşletim sistemi diski >2048 GB'dir. Azure Depolama şu anda 2048 GB’den büyük işletim sistemi diski boyutunu desteklememektedir.

* Toplam VM boyutu (çoğaltma + TFO), desteklenen depolama hesabı boyut sınırını (35 TB) aşıyor. Bu uyumsuzluk genellikle sanal makine içindeki tek bir diskin standart depolama için desteklenen Azure veya Site Recovery sınırlarını aşan bir performans özelliği olduğunda gerçekleşir. Bu tür bir örnek, sanal makineyi premium depolama bölgesine iter. Ancak, bir premium depolama hesabı için desteklenen en büyük boyut 35 TB’dir ve tek bir korunan sanal makine birden fazla depolama hesabında korunamaz. Ayrıca, korunan bir sanal makine üzerinde yük devretme testi yürütüldüğünde, test ile çoğaltma aynı depolama hesabında devam eder. Bu örnekte, çoğaltmanın ilerlemesi ve yük devretme testinin paralel olarak başarılı olması için disk boyutunun 2 katını ayarlayın.

* Kaynak IOPS, depolama IOPS için disk başına desteklenen 7500 sınırını aşıyor.

* Kaynak IOPS, depolama IOPS için VM başına desteklenen 80.000 limitini aşıyor.

* Ortalama veri dalgalanması, disk için Ortalama g/ç boyutu için desteklenen Site Recovery veri değişim sınırı olan 20 MB/sn 'yi aşıyor.

* Sanal makine üzerindeki tüm disklerde bulunan en yüksek veri değişim sıklığı, VM başına 54 MB/sn’lik desteklenen Site Recovery en yüksek veri değişim sıklığı sınırını aşıyor.

* Algılanan ortalama yazma IOPS değeri, disk için 840 olan desteklenen Site Recovery IOPS limitini aşıyor.

* Hesaplanan anlık görüntü depolama alanı, 10 TB’lik desteklenen anlık görüntü depolama limitini aşıyor.

* Günlük toplam veri değişim sıklığı, bir İşlem Sunucusu tarafından desteklenen günlük 2 TB sınırını aşıyor.


**En Yüksek Okuma/Yazma IOPS (Büyüme Faktörü ile)** : Disk üzerinde gelecekteki büyüme faktörünü de (varsayılan değer yüzde 30) içeren en yoğun iş yükü IOPS değeridir (varsayılan değer yüzde 95’lik dilim). Sanal makinenin en yoğun okuma/yazma IOPS değeri profil oluşturma işleminin her dakikası boyunca tek disklerin okuma/yazma IOPS değerinin toplamı olduğundan, bir sanal makinenin toplam okuma/yazma IOPS değeri her zaman sanal makinenin ayrı disklerinin okuma/yazma IOPS değerine eşit değildir.

**Mb/sn cinsinden En Yüksek Veri Değişim Sıklığı (Büyüme Faktörü ile)** : disk üzerinde gelecekteki büyüme faktörünü de (varsayılan değer yüzde 30) içeren en yüksek erime oranıdır (varsayılan değer yüzde 95’lik dilim). Sanal makinenin en yoğun veri değişim sıklığı, profil oluşturma döneminin her dakikasında içindeki ayrı disklerin veri değişim sıklığı toplamının tepe noktası olduğundan, sanal makinenin toplam veri değişim sıklığı her zaman sanal makinedeki ayrı disklerin veri değişim sıklığının toplamı olmayacaktır.

**Disk Sayısı**: Sanal makine üzerindeki toplam VMDK sayısı.

**Disk boyutu (GB)** : Sanal makinenin tüm disklerinin toplam kurulum boyutu. Araç ayrıca sanal makinedeki ayrı diskler için disk boyutunu gösterir.

**Çekirdek**: Sanal makine üzerindeki CPU çekirdeği sayısı.

**Bellek (MB)** : VM üzerindeki RAM miktarı.

**NIC**: VM üzerindeki NIC sayısı.

**Önyükleme Türü**: Sanal makinenin önyükleme türü. BIOS veya EFI olabilir.  Azure Site Recovery şu anda önyükleme diskindeki bölüm sayısı 4’ten az ve önyükleme kesimi boyutu 512 bayt olmak şartıyla Windows Server EFI VM’lerini (Windows Server 2012, 2012 R2 ve 2016) desteklemektedir. EFI VM'lerini korumak için Azure Site Recovery hareketlilik hizmeti sürümü 9.13 veya üstü olmalıdır. EFI VM'ler için yalnızca yük devretme desteklenir. Yeniden çalışma desteklenmez.

**İşletim sistemi türü**: VM’nin işletim sistemi türüdür. VM oluşturulurken VMware vSphere’den seçilen şablona bağlı olarak Windows veya Linux ya da başka bir işletim sistemi olabilir.

## <a name="azure-site-recovery-limits"></a>Azure Site Recovery limitleri
Şu tablo, Azure Site Recovery sınırlarını sağlar. Bu limitler yaptığımız testleri temel alsa da mümkün olan tüm uygulama G/Ç birleşimlerini kapsamamaktadır. Gerçek sonuçlar, uygulamanızın G/Ç karışımına göre değişebilir. En iyi sonuçlar için, uygulamanın gerçek performans görüntüsünü elde etmek üzere, dağıtım planlamasından sonra bile yük devretme testi kullanılarak her zaman kapsamlı uygulama testleri gerçekleştirilmesi önerilir.

**Çoğaltma depolama hedefi** | **Ortalama kaynak disk G/Ç boyutu** |**Ortalama kaynak disk veri değişim sıklığı** | **Günlük toplam kaynak disk veri değişim sıklığı**
---|---|---|---
Standart depolama | 8 KB | 2 MB/sn | Disk başına 168 GB
Premium P10 veya P15 disk | 8 KB  | 2 MB/sn | Disk başına 168 GB
Premium P10 veya P15 disk | 16 KB | 4 MB/sn |  Disk başına 336 GB
Premium P10 veya P15 disk | 32 KB veya daha büyük | 8 MB/sn | Disk başına 672 GB
Premium P20 veya P30 veya P40 veya P50 disk | 8 KB    | 5 MB/sn | Disk başına 421 GB
Premium P20 veya P30 veya P40 veya P50 disk | 16 KB veya daha büyük | 20 MB/sn | disk başına 1684 GB

**Kaynak veri değişim sıklığı** | **Üst Sınır**
---|---
VM üzerindeki tüm disklerde en yüksek veri değişim sıklığı | 54 MB/sn
İşlem Sunucusu tarafından desteklenen günlük en fazla veri değişim sıklığı | 2 TB

Bunlar yüzde 30 G/Ç çakışmasını varsayan ortalama sayılardır. Site Recovery; çakışma oranı, büyük yazma boyutları ve gerçek iş yükü G/Ç davranışına göre daha yüksek aktarım hızını işleyebilir. Yukarıdaki sayılar yaklaşık beş dakikalık tipik bir kapsamı varsayar. Diğer bir deyişle, veriler karşıya yüklendikten sonra işlenir ve beş dakika içinde kurtarma noktası oluşturulur.


## <a name="cost-estimation"></a>Maliyet tahmini
[Maliyet tahmini](site-recovery-vmware-deployment-planner-cost-estimation.md) hakkında daha fazla bilgi edinin.


## <a name="next-steps"></a>Sonraki adımlar
[Maliyet tahmini](site-recovery-vmware-deployment-planner-cost-estimation.md) hakkında daha fazla bilgi edinin.
