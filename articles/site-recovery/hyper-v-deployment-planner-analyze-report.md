---
title: Azure Site Recovery 'de Hyper-V Dağıtım Planlayıcısı raporunu analiz etme
description: Bu makalede, Hyper-V VM 'lerinin Azure 'a olağanüstü durum kurtarması için Azure Site Recovery Dağıtım Planlayıcısı tarafından oluşturulan bir raporun nasıl analiz edileceği açıklanır.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/21/2019
ms.author: mayg
ms.openlocfilehash: 0d39f763d3cdc90f89e0bcd17d0facc67551ffc0
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084956"
---
# <a name="analyze-the-azure-site-recovery-deployment-planner-report"></a>Azure Site Recovery Dağıtım Planlayıcısı raporunu analiz etme
Bu makalede, Hyper-V’den Azure’a dağıtım senaryosu için Azure Site Recovery Dağıtım Planlayıcısı tarafından oluşturulan Excel raporunda bulunan sayfalar ele alınmaktadır.

## <a name="on-premises-summary"></a>Şirket içi özeti
Şirket içi özeti çalışma sayfası, profili oluşturulmuş Hyper-V ortamına genel bir bakış sağlar.

![Şirket içi özeti](media/hyper-v-deployment-planner-analyze-report/on-premises-summary-h2a.png)

**Başlangıç tarihi** ve **Bitiş tarihi**: Rapor oluşturma için göz önünde bulundurulan profil oluşturma verilerinin başlangıç ve bitiş tarihleri. Varsayılan olarak, başlangıç tarihi profil oluşturmanın başladığı, bitiş tarihi ise profil oluşturmanın durdurulduğu tarihtir. Rapor bu parametrelerle oluşturulduysa bu bilgiler "StartDate" ve "EndDate" değerleri olabilir.

**Profil oluşturulan toplam gün sayısı**: Raporun oluşturulduğu başlangıç ile bitiş tarihleri arasında profil oluşturulan toplam gün sayısı.

**Uyumlu sanal makine sayısı**: Gerekli ağ bant genişliği, gerekli depolama hesabı sayısı ve Azure çekirdek sayısının hesaplandığı uyumlu sanal makinelerin toplam sayısı.

**Tüm uyumlu sanal makinelerdeki toplam disk sayısı**: Tüm uyumlu sanal makinelerdeki toplam disk sayısıdır.

**Bir uyumlu sanal makinedeki ortalama disk sayısı**: Tüm uyumlu sanal makinelerde hesaplanan ortalama disk sayısıdır.

**Ortalama disk boyutu (GB)** : Tüm uyumlu sanal makinelerde hesaplanan ortalama disk boyutudur.

**İstenen RPO (dakika)** : Varsayılan kurtarma noktası hedefi veya rapor oluşturma sırasında gerekli bant genişliğini tahmin etmek üzere ‘DesiredRPO’ parametresi için geçirilen değer.

**İstenen bant genişliği (Mb/sn)** : Rapor oluşturma sırasında ulaşılabilir kurtarma noktası hedefini (RPO) tahmin etmek üzere "Bandwidth" parametresi için geçirdiğiniz değer.

**Bir günde gözlemlenen tipik veri değişim sıklığı (GB)** : Profil oluşturulan tüm günlerde gözlemlenen ortalama veri değişim sıklığıdır.

## <a name="recommendations"></a>Öneriler 
Hyper-V'den Azure'a dağıtım raporunun öneriler sayfasında, seçilen ve istenen RPO'ya göre aşağıdaki ayrıntılar yer alır:

![Hyper-V'den Azure'a dağıtım raporu için öneriler](media/hyper-v-deployment-planner-analyze-report/Recommendations-h2a.png)

### <a name="profile-data"></a>Profil verileri
![Profil verileri](media/hyper-v-deployment-planner-analyze-report/profile-data-h2a.png)

**Profili oluşturulmuş veri süresi**: Profil oluşturma işleminin gerçekleştirildiği süre. Varsayılan olarak, araç hesaplamadaki tüm profil verilerini içerir. Rapor oluşturma sırasında StartDate ve EndDate seçeneğini kullandıysanız belirli bir dönem için rapor oluşturulur. 

**Profili oluşturulan Hyper-V sunucularının sayısı**: İçerdikleri sanal makinelerin raporu oluşturulan Hyper-V sunucularının sayısı. Hyper-V sunucularının adını görüntülemek için sayıyı seçin. Şirket İçi Depolama Gereksinimi sayfası açılır ve depolama gereksinimleriyle birlikte tüm sunucuları gösterir. 

**İstenen RPO**: Dağıtımınıza yönelik kurtarma noktası hedefi. Varsayılan olarak, gerekli ağ bant genişliği 15, 30 ve 60 dakikalık RPO değerleri için hesaplanır. Seçim temel alınarak, etkilenen değerler sayfada güncelleştirilir. Raporu oluştururken DesiredRPOinMin parametresini kullandıysanız bu değer İstenen RPO sonucunda gösterilir.

### <a name="profiling-overview"></a>Profil oluşturmaya genel bakış
![Profil oluşturmaya genel bakış](media/hyper-v-deployment-planner-analyze-report/profiling-overview-h2a.png)

**Profili Oluşturulan Toplam Sanal Makine Sayısı**: Profili oluşturulan verileri bulunan sanal makinelerin toplam sayısı. VMListFile dosyasında profili oluşturulmamış sanal makineler varsa, bu sanal makineler rapor oluşturma işleminde göz önünde bulundurulmaz ve profili oluşturulan toplam sanal makine sayısının dışında tutulur.

**Uyumlu Sanal Makineler**: Azure Site Recovery kullanılarak Azure’da korunabilen sanal makine sayısı. Bu sayı, gerekli ağ bant genişliği, depolama hesabı sayısı ve Azure çekirdek sayısının hesaplandığı uyumlu sanal makinelerin toplam sayısıdır. Her uyumlu VM’nin ayrıntıları "Uyumlu VM’ler" bölümünde bulunabilir.

**Uyumsuz Sanal Makineler**: Site Recovery ile koruma için uygun olmayan, profili oluşturulmuş sanal makine sayısı. Uyumsuzluğun nedenleri, “Uyumsuz VM’ler” bölümünde belirtilmiştir. VMListFile içinde profili oluşturulmamış bir sanal makinenin adı varsa, bu sanal makineler uyumsuz sanal makine sayısının dışında bırakılır. Bu sanal makineler, “Uyumsuz VM’ler” bölümünün sonunda “Veri bulunamadı” olarak listelenir.

**İstenen RPO**: Dakika cinsinden istediğiniz kurtarma noktası hedefi. Rapor üç RPO değeri için oluşturulur: 15 (varsayılan), 30 ve 60 dakika. Rapordaki bant genişliği önerisi, sayfanın sağ üst köşesinde bulunan **İstenen RPO** açılır listesindeki seçiminize göre değişir. Özel bir değer ile -DesiredRPO parametresini kullanarak raporu oluşturduysanız bu özel değer, **İstenen RPO** açılır listesinde varsayılan olarak gösterilir.

### <a name="required-network-bandwidth-mbps"></a>Gerekli ağ bant genişliği (Mb/sn)
![Gerekli ağ bant genişliği](media/hyper-v-deployment-planner-analyze-report/required-network-bandwidth-h2a.png)

**%100 RPO süresini karşılamak için**: İstediğiniz yüzde 100 RPO süresini karşılamak için Mb/sn cinsinden ayrılacak önerilen bant genişliği. Bu bant genişliği miktarı, herhangi bir RPO ihlalini önlemek üzere tüm uyumlu sanal makinelerinizin kararlı durum delta çoğaltması için ayrılmalıdır.

**%90 RPO süresini karşılamak için**: Geniş bant fiyatlandırması nedeniyle veya başka bir nedenle istediğiniz %100 RPO süresini karşılamak için gerekli bant genişliğini ayarlayamayabilirsiniz. Bu durumda, istediğiniz yüzde 90 RPO süresini karşılayabilecek daha düşük bir bant genişliği ayarı kullanabilirsiniz. Daha düşük olan bu bant genişliğini ayarlamanın etkilerini anlamak için, raporda beklenen RPO ihlallerinin sayısı ve süresine ilişkin bir ne yapmalı analizi sağlar.

**Elde Edilen Aktarım Hızı**: Depolama hesabının bulunduğu Azure bölgesine GetThroughput komutunu çalıştırdığınız sunucudan aktarım hızı. Bu aktarım hızı sayısı, Site Recovery kullanarak uyumlu sanal makineleri koruduğunuzda elde edebileceğiniz tahmini düzeyi belirtir. Hyper-V sunucusunun depolama ve ağ özellikleri, aracı çalıştırdığınız sunucunun depolama ve ağ özellikleriyle aynı kalmalıdır.

Tüm kurumsal Site Recovery dağıtımları için [ExpressRoute](https://aka.ms/expressroute) kullanılması önerilir.

### <a name="required-storage-accounts"></a>Gerekli depolama hesapları
Aşağıdaki grafikte tüm uyumlu sanal makineleri korumak için gereken depolama hesaplarının (standart ve premium) toplam sayısı gösterilmektedir. Her bir VM için kullanılacak depolama hesabını öğrenmek için "VM depolama yerleşimi" bölümüne bakın.

![Gerekli Azure depolama hesapları](media/hyper-v-deployment-planner-analyze-report/required-storage-accounts-h2a.png)

### <a name="required-number-of-azure-cores"></a>Gerekli Azure çekirdek sayısı
Bu sonuç, tüm uyumlu sanal makinelerin yük devretme işlemi ya da yük devretme testi öncesinde ayarlanması gereken toplam çekirdek sayısıdır. Abonelikte çok az sayıda çekirdek varsa, yük devretme testi veya yük devretme sırasında Azure Site Recovery, sanal makineleri oluşturamaz.

![Gerekli Azure çekirdek sayısı](media/hyper-v-deployment-planner-analyze-report/required-number-of-azure-cores-h2a.png)


### <a name="additional-on-premises-storage-requirement"></a>Ek şirket içi depolama gereksinimleri

Sanal makine çoğaltmasının üretim uygulamalarınızda istenmeyen kapalı kalma durumlarına neden olmayacağından emin olacak şekilde başarılı bir ilk çoğaltma ve değişiklik çoğaltması sağlamak için Hyper-V sunucularında gereken toplam boş depolama alanı. Her birim gereksinimiyle ilgili daha fazla bilgi, [şirket içi depolama gereksinimleri](#on-premises-storage-requirement) bölümünde sağlanmıştır. 

Çoğaltma için neden boş alan gerektiğini anlamak için, [Şirket için depolama gereksinimleri](#why-do-i-need-free-space-on-the-hyper-v-server-for-the-replication) bölümüne bakın.

![Şirket içi depolama gereksinimleri ve kopyalama sıklığı](media/hyper-v-deployment-planner-analyze-report/on-premises-storage-and-copy-frequency-h2a.png)

### <a name="maximum-copy-frequency"></a>En yüksek kopyalama sıklığı
İstenen RPO'yu elde etmek üzere çoğaltma için önerilen en yüksek kopyalama sıklığı ayarlanmalıdır. Varsayılan değer beş dakikadır. Daha iyi bir RPO elde etmek için kopyalama sıklığını 30 saniyeye ayarlayabilirsiniz.

### <a name="what-if-analysis"></a>Benzetim analiz
![Benzetim analizi](media/hyper-v-deployment-planner-analyze-report/what-if-analysis-h2a.png) Bu analiz, sürenin yalnızca yüzde 90’ını karşılamasını istediğiniz RPO için daha düşük bir bant genişliği ayarladığınızda profil oluşturma sırasında kaç tane ihlal oluşabileceğini özetler. Belirli bir günde bir veya daha fazla RPO ihlali ortaya çıkabilir. Graf, günün yoğun RPO değerini gösterir. Bu analizi temel alarak, belirtilen düşük bant genişliği ile tüm günlerdeki RPO ihlali sayısının ve bir gündeki en yoğun RPO gerçekleşme zamanının kabul edilebilir olup olmadığına karar verebilirsiniz. Kabul edilebilir ise, çoğaltma için daha düşük bant genişliği ayırabilirsiniz. Kabul edilebilir değilse, istenen yüzde 100 RPO süresini karşılamak için önerildiği şekilde daha yüksek bant genişliği ayırın. 

### <a name="recommendation-for-successful-initial-replication"></a>Başarılı ilk çoğaltma önerisi
Bu bölümde, sanal makinelerin korunacağı toplu iş sayısı ve ilk çoğaltmayı (IR) başarıyla tamamlamak için gereken en düşük bant genişliği ele alınmaktadır. 

![IR toplu işlemesi](media/hyper-v-deployment-planner-analyze-report/ir-batching-h2a.png)

Sanal makineler belirtilen toplu iş sırasıyla korunmalıdır. Her toplu işin belirli bir sanal makine listesi vardır. Toplu İş 1 sanal makineleri, Toplu İş 2 sanal makinelerinden önce korunmalıdır. Toplu İş 2 sanal makineleri, Toplu İş 3 sanal makinelerinden önce korunmalıdır ve bu şekilde devam edilmelidir. Toplu İş 1 sanal makinelerinin ilk çoğaltması tamamlandıktan sonra, Toplu İş 2 sanal makineleri için çoğaltmayı etkinleştirebilirsiniz. Benzer şekilde, Toplu İş 2 sanal makinelerinin ilk çoğaltması tamamlandıktan sonra, Toplu İş 3 sanal makineleri için çoğaltmayı etkinleştirebilirsiniz ve bu şekilde devam edilmelidir. 

Toplu iş sırasına uyulmazsa, daha sonra korunan sanal makineler için ilk çoğaltmaya yönelik yeterli bant genişliği kalmayabilir. Sonuçta ya sanal makineler ilk çoğaltmayı hiçbir zaman tamamlamaz ya da birkaç korumalı sanal makine yeniden eşitleme moduna geçebilir. Seçilen RPO için IR toplu işleme sayfasında, her toplu işe hangi sanal makinelerin dahil edilmesi gerektiğine ilişkin ayrıntılı bilgi bulunur.

Buradaki grafta, belirlenen toplu iş sırasına göre toplu işlerde ilk çoğaltma ve değişiklik çoğaltması için bant genişliği dağılımı gösterilir. İlk sanal makine toplu işini koruduğunuzda ilk çoğaltma için bant genişliğinin tamamı kullanılabilir. İlk toplu iş için ilk çoğaltma tamamlandıktan sonra, değişiklik çoğaltması için bant genişliğinin bir kısmı gerekir. Kalan bant genişliği, sanal makinelerin ikinci toplu işinin ilk çoğaltmasında kullanılabilir. 

Toplu İş 2 çubuğu, Toplu İş 1 sanal makineleri için gereken değişiklik çoğaltması bant genişliğini ve Toplu İş 2 sanal makinelerinin ilk çoğaltması için kullanılabilir bant genişliğini gösterir. Benzer biçimde, Toplu İş 3 çubuğu önceki toplu işlerin (Toplu İş 1 ve Toplu İş 2 sanal makineleri) değişiklik çoğaltması için gereken bant genişliğini ve Toplu İş 3’ün ilk çoğaltması için kullanılabilir bant genişliğini gösterir ve bu şekilde devam eder. Tüm toplu işlerin ilk çoğaltması tamamlandıktan sonra son çubuk tüm korumalı sanal makinelerin değişiklik çoğaltması için gereken bant genişliğini gösterir.

**Neden ilk çoğaltma toplu işlemesine ihtiyacım var?**
İlk çoğaltmayı tamamlama süresi VM disk boyutuna, kullanılan disk alanına ve kullanılabilir ağ aktarım hızına bağlıdır. Seçilen RPO için IR toplu işlemesi sayfasında ayrıntılar sağlanır.

### <a name="cost-estimation"></a>Maliyet tahmini
Grafta, seçtiğiniz hedef bölgede ve rapor oluşturma için belirttiğiniz para biriminde Azure’a tahmini toplam olağanüstü durum kurtarma (DR) maliyetinin özet görünümü gösterilir.

![Maliyet tahmini özeti](media/hyper-v-deployment-planner-analyze-report/cost-estimation-summary-h2a.png)

Bu özet, Site Recovery kullanarak tüm uyumlu sanal makinelerinizi Azure’da koruduğunuzda ödemeniz gereken depolama, bilgi işlem, ağ ve lisans maliyetini anlamanıza yardımcı olur. Maliyet, tüm profili oluşturulan sanal makineler için değil uyumlu sanal makineler için hesaplanır. 
 
Aylık veya yıllık maliyeti görüntüleyebilirsiniz. [Desteklenen hedef bölgeler](./hyper-v-deployment-planner-cost-estimation.md#supported-target-regions) ve [desteklenen para birimleri](./hyper-v-deployment-planner-cost-estimation.md#supported-currencies) hakkında daha fazla bilgi edinin.

**Bileşenlere göre maliyet**: Toplam DR maliyeti dört bileşene bölünür: Bilgi işlem, depolama, ağ ve Site Recovery lisansı maliyeti. Maliyet, çoğaltma sırasında ve DR tatbikatı anında oluşan tüketime dayalı olarak hesaplanır. Hesaplamalar için bilgi işlem, depolama (premium ve standart), şirket içi site ve Azure arasında yapılandırılan ExpressRoute/VPN ve Site Recovery lisansı kullanılır.

**Durumlara göre maliyet**: Toplam olağanüstü durum kurtarma maliyeti, iki farklı duruma göre kategorilere ayrılır: çoğaltma ve DR tatbikatı. 

**Çoğaltma maliyeti**: Çoğaltma sırasında tahakkuk ettirilen maliyet. Depolama, ağ ve Site Recovery lisansı maliyetini kapsar. 

**DR Tatbikatı maliyeti**: Yük devretme testi sırasında tahakkuk ettirilen maliyet. Site Recovery, yük devretme testi sırasında sanal makineleri çalıştırır. DR tatbikatı maliyeti, çalıştırılan sanal makinelerin bilgi işlem ve depolama maliyetini kapsar. 

**Ay/Yıl başına Azure Depolama Maliyeti**: Çubuk grafik, çoğaltma ve DR tatbikatının premium ve standart depolaması için tahakkuk ettirilen toplam depolama maliyetini gösterir. [Maliyetini Tahmini](hyper-v-deployment-planner-cost-estimation.md) sayfasında VM başına ayrıntılı maliyet analizini görüntüleyebilirsiniz.

### <a name="growth-factor-and-percentile-values-used"></a>Kullanılan büyüme faktörü ve yüzdelik değerler
Sayfanın alt kısmındaki bu bölümde, profili oluşturulan sanal makinelerin tüm performans sayaçları için kullanılan yüzdelik dilim değeri (varsayılan değer yüzde 95’lik dilim) gösterilmektedir. Ayrıca tüm hesaplamalarda kullanılan büyüme faktörünü de gösterir (varsayılan değer yüzde 30).

![Kullanılan büyüme faktörü ve yüzdelik değerler](media/hyper-v-deployment-planner-run/growth-factor-max-percentile-value.png)

## <a name="recommendations-with-available-bandwidth-as-input"></a>Girdi olarak kullanılabilir bant genişliği ile ilgili öneriler
![Bant genişliği girdisiyle profil oluşturmaya genel bakış](media/hyper-v-deployment-planner-analyze-report/profiling-overview-bandwidth-input-h2a.png)

Site Recovery çoğaltması için x MB/sn’den fazla bant genişliği ayarlayamayacağınızı bildiğiniz bir durumla karşılaşabilirsiniz. Kullanılabilir bant genişliğini girmek (rapor oluşturma sırasında -Bandwidth parametresini kullanarak) ve dakika cinsinden ulaşılabilir RPO değerini almak için aracı kullanabilirsiniz. Bu ulaşılabilir RPO değeriyle ek bant genişliği sağlamanızın gerekli olup olmadığına veya bu RPO ile bir olağanüstü durum kurtarma çözümünden memnun olup olmadığınıza karar verebilirsiniz.

![Ulaşılabilir RPO](media/hyper-v-deployment-planner-analyze-report/achivable-rpo-h2a.PNG)

## <a name="vm-storage-placement-recommendation"></a>VM-depolama yerleştirme önerisi 
![VM-depolama yerleşimi](media/hyper-v-deployment-planner-analyze-report/vm-storage-placement-h2a.png)

**Disk Depolama Türü**: **Yerleştirilecek VM’ler** sütununda bahsedilen tüm ilgili sanal makineleri çoğaltmak için kullanılan standart veya premium depolama hesabıdır.

**Önerilen Ön Ek**: depolama hesabını adlandırmak için kullanılabilecek, üç karakterli bir önerilen ön ektir. Kendi ön ekinizi kullanabilirsiniz, ancak aracın önerisi [depolama hesapları için bölüm adlandırma kuralına](https://aka.ms/storage-performance-checklist) uygundur.

**Önerilen Hesap Adı**: Önerilen ön eki ekledikten sonra depolama hesabı adı. Köşeli ayraç (< ve >) içindeki adı özel girdinizle değiştirin.

**Günlük Depolama Hesabı:** Tüm çoğaltma günlükleri standart bir depolama hesabında depolanır. Premium depolama hesabına çoğaltılan sanal makineler için günlük depolamaya yönelik ek bir standart depolama hesabı oluşturun. Tek bir standart kayıt depolama hesabı, birden fazla premium çoğaltma depolama hesabı tarafından kullanılabilir. Standart depolama hesaplarına çoğaltılan sanal makineler, günlüklerle aynı depolama hesabını kullanır.

**Önerilen Günlük Hesabı Adı**: Önerilen ön eki ekledikten sonra depolama günlük hesabı adı. Köşeli ayraç (< ve >) içindeki adı özel girdinizle değiştirin.

**Yerleştirme Özeti**: Çoğaltma ve yük devretme testi veya yük devretme sırasında depolama hesabındaki toplam sanal makine yükünün özeti. Özet şunları içerir:

* Depolama hesabına eşlenen toplam sanal makine sayısı. 
* Bu depolama hesabına yerleştirilen tüm sanal makineler arasındaki toplam okuma/yazma IOPS değeri.
* Toplam yazma (çoğaltma) IOPS değeri.
* Tüm diskler arasındaki toplam kurulum boyutu.
* Toplam disk sayısı.

**Yerleştirilecek Sanal Makineler**: En iyi performans ve kullanım için belirli bir depolama hesabına yerleştirilmesi gereken tüm sanal makinelerin listesi.

## <a name="compatible-vms"></a>Uyumlu VM’ler
Site Recovery Dağıtım Planlayıcısı tarafından oluşturulan Excel raporu, "Uyumlu Sanal Makineler" sayfasında tüm uyumlu sanal makinelerin ayrıntılarını sağlar.

![Uyumlu VM’ler](media/hyper-v-deployment-planner-analyze-report/compatible-vms-h2a.png)

**VM Adı**: Rapor oluşturulurken VMListFile içinde kullanılan VM adı. Bu sütunda ayrıca sanal makinelere bağlanan diskler de (VHD) listelenir. Adlar, profil oluşturma sırasında aracın sanal makineleri bulduğu Hyper-V konak adlarını içerir.

**VM Uyumluluğu**: Değerler **Evet** ve **Evet**\* şeklindedir. **Evet**\*, VM 'Nin [Premium SSD 'ler](../virtual-machines/windows/disks-types.md)için uygun olduğu örneklere yöneliktir. Burada, profili oluşturulan yüksek değişim sıklığı veya IOPS diski, diske eşlenen boyuttan daha büyük bir premium disk boyutuna sığar. Depolama hesabı, bir diskin boyutuna göre hangi premium depolama disk türüne eşleneceğine karar verir: 
* <128 GB bir P10’dur.
* 128 GB ile 256 GB arası P15’tir.
* 256 GB ile 512 GB arası P20'dir.
* 512 GB ile 1.024 GB arası P30’dur.
* 1\.025 GB ile 2.048 GB arası P40’tır.
* 2\.049 GB ile 4.095 GB arası P50’dir.

Bir diskin iş yükü özellikleri diski P20 veya P30 kategorisine koyarken boyutu nedeniyle daha düşük bir premium depolama disk türüne eşleniyorsa, araç bu VM’yi **Evet**\* olarak işaretler. Araç ayrıca kaynak disk boyutunu önerilen premium depolama disk türüne uyacak şekilde değiştirmenizi veya hedef disk türünü yük devretme sonrasını değiştirmenizi önerir.

**Depolama Türü**: Standart veya Premium.

**Önerilen Ön Ek**: Üç karakterli depolama hesabı ön ekidir.

**Depolama Hesabı**: Önerilen depolama hesabı ön ekini kullanan ad.

**En Yoğun Okuma/Yazma IOPS (Büyüme Faktörü ile)** : Gelecekteki büyüme faktörü (varsayılan değer yüzde 30’dur) ile birlikte disk üzerindeki en yoğun iş yükü okuma/yazma IOPS değeri (varsayılan değer yüzde 95’lik dilimdir). Sanal makinenin toplam okuma/yazma IOPS değeri her zaman sanal makinenin tek tek disklerinin okuma/yazma IOPS toplamı değildir. Sanal makinenin en yoğun okuma/yazma IOPS değeri, profil oluşturma döneminin her dakikasındaki tek tek disklerinin okuma/yazma IOPS değerinin en yüksek toplamıdır.

**MB/sn Cinsinden En Yoğun Veri Değişim Sıklığı (Büyüme Faktörü ile)** : Gelecekteki büyüme faktörü (varsayılan değer yüzde 30’dur) ile birlikte disk üzerindeki en yoğun veri değişim sıklığı (varsayılan değer yüzde 95’lik dilimdir). Sanal makinenin en yoğun veri değişim sıklığı her zaman sanal makinenin tek tek disklerinin veri değişim sıklığı toplamı değildir. Sanal makinenin en yoğun okuma/yazma IOPS değeri, profil oluşturma döneminin her dakikasındaki tek tek disklerinin okuma/yazma IOPS değerinin en yüksek toplamıdır.

**Azure VM Boyutu**: Bu şirket içi sanal makine için eşlenen ideal Azure Cloud Services sanal makine boyutudur. Eşleme, şirket içi sanal makinenin belleğine, disk/çekirdek/ağ arabirimi sayısına ve okuma/yazma IOPS değerine bağlıdır. Her zaman şirket içi VM özelliklerinin tümüyle eşleşen en düşük Azure VM boyutunun kullanılması önerilir.

**Disk Sayısı**: Sanal makine üzerindeki disklerin (VHD) toplam sayısı.

**Disk Boyutu (GB)** : Sanal makinenin tüm disklerinin toplam boyutu. Araç ayrıca sanal makinedeki ayrı diskler için disk boyutunu gösterir.

**Çekirdek**: Sanal makine üzerindeki CPU çekirdeği sayısı.

**Bellek (MB)** : VM üzerindeki RAM.

**NIC**: VM üzerindeki NIC sayısı.

**Önyükleme Türü**: Sanal makinenin önyükleme türü. BIOS veya EFI olabilir.

## <a name="incompatible-vms"></a>Uyumsuz VM’ler
Site Recovery Dağıtım Planlayıcısı tarafından oluşturulan Excel raporu, "Uyumsuz Sanal Makineler" sayfasında tüm uyumsuz sanal makinelerin ayrıntılarını sağlar.

![Uyumsuz VM’ler](media/hyper-v-deployment-planner-analyze-report/incompatible-vms-h2a.png)

**VM Adı**: Rapor oluşturulurken VMListFile içinde kullanılan VM adı. Bu sütunda ayrıca sanal makinelere bağlanan diskler de (VHD) listelenir. Adlar, profil oluşturma sırasında aracın sanal makineleri bulduğu Hyper-V konak adlarını içerir.

**VM Uyumluluğu**: Belirli bir sanal makinenin, Site Recovery ile kullanım için neden uyumlu olmadığını gösterir. Sanal makinenin her uyumsuz diski için, yayımlanan [depolama sınırlarına](https://aka.ms/azure-storage-scalbility-performance) göre nedenler aşağıdakilerden biri olabilir:

* Disk boyutu, 4.095 GB’tan büyüktür. Azure Depolama şu anda 4.095 GB’tan büyük veri diski boyutlarını desteklememektedir.

* 1\. nesil (BIOS önyükleme türü) sanal makine için işletim sistemi diski, 2.047 GB’tan büyüktür. Site Recovery, 1. nesil sanal makinelerde 2.047 GB’tan büyük işletim sistemi disk boyutunu desteklememektedir.

* 2\. nesil (EFI önyükleme türü) sanal makine için işletim sistemi diski, 300 GB’tan büyüktür. Site Recovery, 2. nesil sanal makinelerde 300 GB’tan büyük işletim sistemi disk boyutunu desteklememektedir.

* Şu karakterlerden herhangi birini içeren sanal makine adları desteklenmemektedir: “” [] `. Araç, adlarında bu karakterlerden biri bulunan sanal makineler için profil verilerini alamaz. 

* VHD, iki veya daha çok sanal makine tarafından paylaşılıyordur. Azure, VHD’si paylaşılan sanal makineleri desteklememektedir.

* Sanal Fiber Kanalı olan sanal makineler desteklenmemektedir. Site Recovery, Sanal Fiber Kanalı olan sanal makineleri desteklememektedir.

* Hyper-V kümesi, çoğaltma aracısı içermez. Site Recovery, küme için Hyper-V Çoğaltma Aracısı yapılandırılmadıysa Hyper-V kümesindeki sanal makineyi desteklememektedir.

* Sanal makine yüksek kullanılabilirliğe sahip değildir. Site Recovery, VHD’leri küme diski yerine yerel diskte depolanmış olan Hyper-V küme düğümünün sanal makinesini desteklememektedir. 

* Toplam VM boyutu (çoğaltma + yük devretme testi), desteklenen premium depolama hesabı boyut sınırını (35 TB) aşıyordur. Bu uyumsuzluk genellikle sanal makine içindeki tek bir diskin standart depolama için desteklenen Azure veya Site Recovery sınırlarını aşan bir performans özelliği olduğunda gerçekleşir. Bu tür bir örnek, sanal makineyi premium depolama bölgesine iter. Ancak premium depolama hesabının en yüksek desteklenen boyutu 35 TB’dir. Tek bir korumalı sanal makine, birden çok depolama hesabında korunamaz. 

    Korunan bir sanal makine üzerinde yük devretme testi yürütüldüğünde ve yük devretme testi için yönetilmeyen bir disk yapılandırıldıysa test, çoğaltmanın yürütüldüğü aynı depolama hesabında çalıştırılır. Bu örnekte, çoğaltmayla aynı miktarda ek depolama alanı gerekir. Bu sayede, çoğaltmanın ilerlemesiyle yük devretme testinin başarıya ulaşması paralel gider. Yük devretme testi için yönetilen disk yapılandırıldığında yük devretme testi sanal makinesi için ek alanın hesaba katılması gerekmez.

* Kaynak IOPS, depolama IOPS için disk başına desteklenen 7.500 sınırını aşıyor.

* Kaynak IOPS, depolama IOPS için sanal makine başına desteklenen 80.000 sınırını aşıyor.

* Kaynak VM ortalama veri karmaşası, ortalama g/ç boyutu için desteklenen Site Recovery veri değişim sınırını (20 MB/sn) aşıyor.

* Kaynak VM ortalama etkili yazma IOPS değeri, 840 olan desteklenen Site Recovery IOPS sınırını aşıyor.

* Hesaplanan anlık görüntü depolama alanı, 10 TB’lik desteklenen anlık görüntü depolama limitini aşıyor.

**En Yoğun Okuma/Yazma IOPS (Büyüme Faktörü ile)** : Disk üzerinde gelecekteki büyüme faktörü (varsayılan değer yüzde 30’dur) ile birlikte en yoğun iş yükü IOPS değeridir (varsayılan değer yüzde 95’lik dilimdir). Sanal makinenin toplam okuma/yazma IOPS değeri her zaman sanal makinenin tek tek disklerinin okuma/yazma IOPS toplamı değildir. Sanal makinenin en yoğun okuma/yazma IOPS değeri, profil oluşturma döneminin her dakikasındaki tek tek disklerinin okuma/yazma IOPS değerinin en yüksek toplamıdır.

**En Yoğun Veri Değişim Sıklığı (MB/sn) (Büyüme Faktörü ile)** : Gelecekteki büyüme faktörü (varsayılan değer yüzde 30’dur) ile birlikte disk üzerindeki en yoğun veri değişim sıklığı (varsayılan değer yüzde 95’lik dilimdir). Sanal makinenin toplam veri değişim sıklığı her zaman sanal makinenin tek tek disklerinin veri değişim sıklığı toplamı değildir. Sanal makinenin en yoğun okuma/yazma IOPS değeri, profil oluşturma döneminin her dakikasındaki tek tek disklerinin okuma/yazma IOPS değerinin en yüksek toplamıdır.

**Disk Sayısı**: Sanal makine üzerindeki toplam VHD sayısı.

**Disk Boyutu (GB)** : Sanal makinenin tüm disklerinin toplam kurulum boyutu. Araç ayrıca sanal makinedeki ayrı diskler için disk boyutunu gösterir.

**Çekirdek**: Sanal makine üzerindeki CPU çekirdeği sayısı.

**Bellek (MB)** : VM üzerindeki RAM miktarı.

**NIC**: VM üzerindeki NIC sayısı.

**Önyükleme Türü**: Sanal makinenin önyükleme türü. BIOS veya EFI olabilir.

## <a name="azure-site-recovery-limits"></a>Azure Site Recovery limitleri
Aşağıdaki tablo, Site Recovery sınırlarını sağlar. Bu sınırlar, testleri temel alsa da mümkün olan tüm uygulama G/Ç birleşimlerini kapsayamaz. Gerçek sonuçlar, uygulamanızın G/Ç karışımına göre değişebilir. En iyi sonuçlar için, uygulamanın gerçek performans görüntüsünü elde etmek üzere, dağıtım planlamasından sonra bile yük devretme testi düzenleyerek kapsamlı uygulama testleri gerçekleştirin.
 
**Çoğaltma depolama hedefi** | **Kaynak VM ortalama G/Ç boyutu** |**Kaynak VM ortalama veri değişim sıklığı** | **Günlük toplam kaynak VM veri değişim sıklığı**
---|---|---|---
Standart depolama | 8 KB | VM başına 2 MB/sn | VM başına 168 GB
Premium depolama | 8 KB  | VM başına 5 MB/sn | VM başına 421 GB
Premium depolama | 16 KB veya daha yüksek| VM başına 20 MB/s | VM başına 1684 GB

Bu sınırlar yüzde 30 G/Ç çakışmasını varsayan ortalama sayılardır. Site Recovery; çakışma oranı, büyük yazma boyutları ve gerçek iş yükü G/Ç davranışına göre daha yüksek aktarım hızını işleyebilir. Yukarıdaki sayılar yaklaşık beş dakikalık tipik bir kapsamı varsayar. Diğer bir deyişle, veriler karşıya yüklendikten sonra işlenir ve beş dakika içinde bir kurtarma noktası oluşturulur.

## <a name="on-premises-storage-requirement"></a>Şirket içi depolama gereksinimi

Çalışma sayfasında başarılı bir ilk çoğaltma ve değişiklik çoğaltması için Hyper-V sunucularının (VHD'lerin durduğu) her birimindeki toplam boş depolama alanı gereksinimi sağlanır. Çoğaltmayı etkinleştirmeden önce, çoğaltmanın üretim uygulamalarınızda istenmeyen kapalı kalma durumlarına neden olmaması için birimlere gerekli depolama alanını ekleyin. 

  Site Recovery Dağıtım Planlayıcısı, çoğaltmada kullanılan VHD’lerin boyutu ve ağ bant genişliği temelinde en uygun depolama alanı gereksinimini belirler.

![Şirket içi depolama gereksinimi](media/hyper-v-deployment-planner-analyze-report/on-premises-storage-requirement-h2a.png)

### <a name="why-do-i-need-free-space-on-the-hyper-v-server-for-the-replication"></a>Çoğaltma için neden Hyper-V sunucusunda boş alana ihtiyacım var?
* Sanal makinenin çoğaltmasını etkinleştirdiğinizde Site Recovery, ilk çoğaltma için sanal makinenin her VHD’sinin anlık görüntüsünü alır. İlk çoğaltma devam ederken, uygulama tarafından disklere yeni değişiklikler yazılır. Site Recovery bu değişiklikleri günlük dosyalarında izler ve bu da ek depolama alanı gerektirir. İlk çoğaltma tamamlanıncaya kadar günlük dosyaları yerel olarak depolanır. 

    Günlük dosyaları ve anlık görüntü (AVHDX) için yeterli alan yoksa çoğaltma yeniden eşitleme moduna geçer ve hiçbir zaman tamamlanmaz. En kötü durumda, ilk çoğaltma için VHD boyutunun yüzde 100’ü kadar ek boş alana ihtiyacınız olur.
* İlk çoğaltma tamamlandıktan sonra değişiklik çoğaltması başlatılır. Site Recovery bu değişiklikleri günlük dosyalarında izler; bu dosyalar sanal makinenin VHD’lerinin durduğu birimde depolanır. Bu günlük dosyaları, yapılandırılan kopyalama sıklığında Azure’a çoğaltılır. Kullanılabilir ağ bant genişliğine göre, günlük dosyalarının Azure'a çoğaltılması biraz zaman alır. 

    Günlük dosyalarını depolamak için yeterli boş alan yoksa çoğaltma duraklatılır. Daha sonra sanal makinenin çoğaltma durumu "yeniden eşitleme gerekiyor" durumuna geçer.
* Günlük dosyalarını Azure’a göndermek için ağ bant genişliği yeterli değilse günlük dosyaları birimde birikir. En kötü durum senaryosunda günlük dosyalarının boyutu, VHD boyutunun yüzde 50’sine yükseltildiğinde, sanal makinenin çoğaltması "yeniden eşitleme gerekiyor" durumuna geçer. En kötü durumda, değişiklik çoğaltması için VHD boyutunun yüzde 50’si kadar ek boş alana ihtiyacınız olur.

**Hyper-V konağı**: Profili oluşturulan Hyper-V sunucularının listesi. Sunucu bir Hyper-V kümesinin parçasıysa, tüm küme düğümleri birlikte gruplandırılır.

**Birim (VHD yolu)** : VHD/VHDX'lerin bulunduğu Hyper-V konağının her birimi. 

**Kullanılabilir boş alan (GB)** : Birimde kullanılabilir olan boş alan.

**Birimde gereken toplam depolama alanı (GB)** : Başarılı bir ilk çoğaltma ve değişiklik çoğaltması için birimde bulunması gereken toplam boş depolama alanı. 

**Başarılı bir çoğaltma için birimde sağlanacak toplam ek depolama alanı (GB)** : Başarılı bir ilk çoğaltma ve değişiklik çoğaltması için birimde sağlanması gereken toplam ek alan için öneride bulunur.

## <a name="initial-replication-batching"></a>İlk çoğaltma toplu işlemesi 

### <a name="why-do-i-need-initial-replication-batching"></a>Neden ilk çoğaltma toplu işlemesine ihtiyacım var?
Tüm sanal makineler aynı anda korunursa boş depolama gereksinimi çok daha yüksek olur. Yeterli depolama alanı yoksa sanal makinelerin çoğaltması, yeniden eşitleme moduna geçer. Aynı zamanda, tüm sanal makinelerin ilk çoğaltmasını başarıyla tamamlamak için gereken ağ bant genişliği çok daha yüksektir. 

### <a name="initial-replication-batching-for-a-selected-rpo"></a>Seçilen RPO için ilk çoğaltma toplu işlemesi
Bu çalışma sayfası, IR için her toplu işin ayrıntılı görünümünü sağlar. Her RPO için, ayrı bir IR toplu işleme sayfası oluşturulur. 

Her birim için şirket içi depolama gereksinimleri önerisine uyduktan sonra, çoğaltmanız gereken ana bilgiler, paralel olarak korunabilecek sanal makinelerin listesidir. Bu sanal makineler bir toplu işte birlikte gruplanır ve birden çok toplu iş olabilir. Sanal makineleri, verilen toplu iş sırasına göre koruyun. İlk olarak Toplu İş 1 Sanal Makinelerini koruyun. İlk çoğaltma tamamlandıktan sonra Toplu İş 2 Sanal Makinelerini koruyun ve bu şekilde devam edin. Bu sayfada toplu iş listesini ve bunlara karşılık gelen sanal makineleri görebilirsiniz. 

![IR toplu işleme ayrıntıları](media/hyper-v-deployment-planner-analyze-report/ir-batching-for-rpo-h2a.png)

![Ek IR toplu işleme ayrıntıları](media/hyper-v-deployment-planner-analyze-report/ir-batching-for-rpo2-h2a.png)

### <a name="each-batch-provides-the-following-information"></a>Her toplu iş şu bilgileri sağlar 
**Hyper-V konağı**: Korunacak sanal makinenin Hyper-V konağı.

**Sanal Makine**: Korunacak olan sanal makine. 

**Açıklamalar**: Sanal makinenin herhangi bir birimi için herhangi bir eylem gerekiyorsa, burada açıklama sağlanır. Örneğin, bir birimde yeterli boş alan yoksa açıklamada "Bu sanal makineyi korumak için depolama alanı ekleyin" ifadesi yer alır.

**Birim (VHD yolu)** : Sanal makinenin VHD’lerinin durduğu birimin adı. 

**Birimdeki kullanılabilir boş alan (GB)** : Birimde sanal makine için kullanılabilen boş disk alanı. Birimlerdeki kullanılabilir boş alan hesaplanırken, VHD'leri aynı birimde yer alan önceki toplu işlerin sanal makineleri tarafından değişiklik çoğaltması için kullanılan disk alanını hesaba katar. 

Örneğin, VM1, VM2 ve VM3 sanal makinelerinin E:\VHDyolu yolunda bulunduğunu varsayalım. Çoğaltma öncesinde, birimdeki boş alan 500 GB'dir. VM1, Toplu İş 1’in; VM2, Toplu İş 2’nin ve VM3 de Toplu İş 3’ün parçasıdır. VM1 için, kullanılabilir boş alan 500 GB olur. VM2 için, kullanılabilir boş alan 500 olur; bu, VM1’in değişiklik çoğaltması için gereken disk alanıdır. VM1, değişiklik çoğaltması için 300 GB gerektiriyorsa, VM2 için kullanılabilir boş alan 500 GB – 300 GB = 200 GB olur. Benzer biçimde, VM2'ye değişiklik çoğaltması için 300 GB gerektiğini varsayalım. VM3 için kullanılabilir boş alan 200 GB - 300 GB = -100 GB olur.

**İlk çoğaltma için birimde gereken depolama alanı (GB)** : Sanal makinenin ilk çoğaltması için birimde gereken boş depolama alanı.

**Değişiklik çoğaltması için birimde gereken depolama alanı (GB)** : Değişiklik çoğaltması için sanal makineye ilişkin birimde gereken boş depolama alanı.

**Çoğaltmanın başarısız olmasını önlemek için eksiklik temelinde gereken ek depolama alanı (GB)** : Sanal makine için birimde gereken ek depolama alanı. Bu, ilk çoğaltma ile değişiklik çoğaltmasının en yüksek depolama alanı gereksiniminden, birimdeki kullanılabilir boş alanın çıkarılmasına eşittir.

**İlk çoğaltma için gereken en düşük bant değişliği (Mb/sn)** : Sanal makinenin ilk çoğaltması için gereken en düşük bant genişliği.

**Değişiklik çoğaltması için gereken en düşük bant genişliği (Mb/sn)** : Sanal makinenin değişiklik çoğaltması için gereken en düşük bant genişliği.

### <a name="network-utilization-details-for-each-batch"></a>Her toplu iş için ağ kullanım ayrıntıları 
Her toplu iş tablosunda, toplu işin ağ kullanımının özeti sağlanır.

**Toplu iş için kullanılabilir bant genişliği**: Önceki toplu işin değişiklik çoğaltması bant genişliği dikkate alındıktan sonra toplu iş için kullanılabilir olan bant genişliği.

**Toplu işin ilk çoğaltması için kullanılabilir yaklaşık bant genişliği**: Toplu işteki sanal makinelerin ilk çoğaltması için kullanılabilir bant genişliği. 

**Toplu işin değişiklik çoğaltması için kullanılan yaklaşık bant genişliği**: Toplu işteki sanal makinelerin değişiklik çoğaltması için gereken bant genişliği. 

**Toplu iş için tahmini ilk çoğaltma süresi (SS:DD)** : Saat:Dakika cinsinden tahmini ilk çoğaltma süresi.



## <a name="next-steps"></a>Sonraki adımlar
[Maliyet tahmini](hyper-v-deployment-planner-cost-estimation.md) hakkında daha fazla bilgi edinin.
