---
title: İşlem ve depolama seçenekleri-MySQL için Azure veritabanı-esnek sunucu
description: Bu makalede, MySQL için Azure veritabanı-esnek sunucusu 'nda işlem ve depolama seçenekleri açıklanmaktadır.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: conceptual
ms.date: 1/28/2021
ms.openlocfilehash: 7165cdc072ffaa5b0d862e1fe17f94e35c35aeec
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105034546"
---
# <a name="compute-and-storage-options-in-azure-database-for-mysql---flexible-server-preview"></a>MySQL için Azure veritabanı 'nda işlem ve depolama seçenekleri-esnek sunucu (Önizleme)

> [!IMPORTANT] 
> MySQL için Azure veritabanı-esnek sunucu şu anda genel önizlemededir.

Üç farklı işlem katmanlarından birinde MySQL için Azure veritabanı esnek sunucusu oluşturabilirsiniz: Burstable, Genel Amaçlı ve bellek için Iyileştirilmiş. İşlem katmanları, B serisi, D serisi ve E Serisi kullanılan temel sanal makine SKU 'SU tarafından farklılaştırılabilir. İşlem katmanı ve boyut seçimi, sunucuda bulunan bellek ve sanal çekirdekleri belirler. Aynı depolama teknolojisi tüm işlem katmanlarında kullanılır. Tüm kaynaklar MySQL sunucu düzeyinde sağlanır. Sunucuda bir veya daha fazla veritabanı olabilir.

| Kaynak/katman | **Burstable** | **Genel Amaçlı** | **Bellek için Iyileştirilmiş** |
|:---|:----------|:--------------------|:---------------------|
| VM serisi| B serisi | Ddsv4 serisi | Edsv4 serisi|
| Sanal çekirdek | 1, 2 | 2, 4, 8, 16, 32, 48, 64 | 2, 4, 8, 16, 32, 48, 64 |
| Sanal çekirdek başına bellek | Değişken | 4 GiB | 8 GiB * |
| Depolama boyutu | 5 GiB ile 16 TiB | 5 GiB ile 16 TiB | 5 GiB ile 16 TiB |
| Veritabanı yedekleme saklama süresi | 1-35 gün | 1-35 gün | 1-35 gün |

\* 504 GB bellek içeren E64ds_v4 (bellek için Iyileştirilmiş) SKU 'SU ile

Bir işlem katmanını seçmek için, başlangıç noktası olarak aşağıdaki tabloyu kullanın.

| İşlem katmanı | Hedef iş yükleri |
|:-------------|:-----------------|
| Burstable | Her zaman sürekli olarak tam CPU gerektirmeyen iş yükleri için idealdir. |
| Genel Amaçlı | Ölçeklenebilir g/ç aktarım hızı ile dengeli işlem ve bellek gerektiren iş yüklerinin çoğu. Örnek olarak web uygulamalarını, mobil uygulamaları ve diğer kurumsal uygulamaları barındıran sunucular verilebilir.|
| Bellek İçin İyileştirilmiş | Daha hızlı işlem işleme ve daha yüksek eşzamanlılık için bellek içi performans gerektiren yüksek performanslı veritabanı iş yükleri. Örnek olarak gerçek zamanlı verileri işleyen ve yüksek performanslı işlem tabanlı ya da analiz uygulamalarının sunucuları verilebilir.|

Bir sunucu oluşturduktan sonra, işlem katmanı, işlem boyutu ve depolama boyutu değişmiştir. İşlem ölçekleme için yeniden başlatma gerekir ve 60-120 saniye arasında sürer, ancak depolama ölçeklendirmesinin yeniden başlatılması gerekmez. Ayrıca, yedekleme bekletme süresini yukarı veya aşağı bağımsız olarak ayarlayabilirsiniz. Daha fazla bilgi için bkz. [kaynakları ölçeklendirme](#scale-resources) bölümü.

## <a name="compute-tiers-size-and-server-types"></a>İşlem katmanları, boyutu ve sunucu türleri

İşlem kaynakları katman ve boyuta göre seçilebilir. Bu, sanal çekirdekleri ve bellek boyutunu belirler. Sanal çekirdekler, temel alınan donanımın mantıksal CPU 'sunu temsil eder.

Kullanılabilir sunucu türlerinin ayrıntılı özellikleri şunlardır:

| İşlem boyutu         | Sanal çekirdek | Bellek boyutu (GiB) | Desteklenen ıOPS üst sınırı | Desteklenen en fazla g/ç bant genişliği (MB/sn)|
|----------------------|--------|-------------------| ------------------ |-----------------------------------|
| **Burstable**        |        |                   | 
| Standard_B1s         | 1      | 1                 | 320                | 10                                | 
| Standard_B1ms        | 1      | 2                 | 640                | 10                                |
| Standard_B2s         | 2      | 4                 | 1280               | 15                                |
| **Genel Amaçlı**  |        |                   |                    |                                   |
| Standard_D2ds_v4     | 2      | 8                 | 3200               | 48                                |
| Standard_D4ds_v4     | 4      | 16                | 6400               | 96                                |
| Standard_D8ds_v4     | 8      | 32                | 12800              | 192                               |
| Standard_D16ds_v4    | 16     | 64                | 20000              | 384                               |
| Standard_D32ds_v4    | 32     | 128               | 20000              | 768                               |
| Standard_D48ds_v4    | 48     | 192               | 20000              | 1152                              |
| Standard_D64ds_v4    | 64     | 256               | 20000              | 1200                              |
| **Bellek için Iyileştirilmiş** |        |                   |                    |                                   |
| Standard_E2ds_v4     | 2      | 16                | 3200               | 48                                |
| Standard_E4ds_v4     | 4      | 32                | 6400               | 96                                |
| Standard_E8ds_v4     | 8      | 64                | 12800              | 192                               |
| Standard_E16ds_v4    | 16     | 128               | 20000              | 384                               |
| Standard_E32ds_v4    | 32     | 256               | 20000              | 768                               |
| Standard_E48ds_v4    | 48     | 384               | 20000              | 1152                              |
| Standard_E64ds_v4    | 64     | 504               | 20000              | 1200                              |

Kullanılabilir işlem serisi hakkında daha fazla bilgi edinmek için bkz. [Burstable (B-serisi)](../../virtual-machines/sizes-b-series-burstable.md), [genel amaçlı (Ddsv4-Series)](../../virtual-machines/ddv4-ddsv4-series.md)ve [bellek için iyileştirilmiş (Edsv4-Series)](../../virtual-machines/edv4-edsv4-series.md)için Azure VM belgeleri.

>[!NOTE]
>Sanal makine başlatılmış/durdurulmuş ya da yeniden başlatılırsa, [Burstable (B-serisi) işlem katmanı](../../virtual-machines/sizes-b-series-burstable.md) için krediler kaybolmuş olabilir. Daha fazla bilgi için bkz. [Burstable (B serisi) SSS](../../virtual-machines/sizes-b-series-burstable.md#q-why-is-my-remaining-credit-set-to-0-after-a-redeploy-or-a-stopstart).

## <a name="storage"></a>Depolama

Sağladığınız depolama alanı, esnek sunucunuz için kullanılabilen depolama kapasitesi miktarıdır. Depolama, veritabanı dosyaları, geçici dosyalar, işlem günlükleri ve MySQL Server günlükleri için kullanılır. Tüm işlem katmanlarında, desteklenen minimum depolama alanı 5 GiB ve maksimum değer 16 TiB 'dir. Depolama, 1 GiB artışlarına göre ölçeklendirilir ve sunucu oluşturulduktan sonra ölçeklendirilebilir.

>[!NOTE]
> Depolama yalnızca yukarı ölçeklenebilen, aşağı doğru değil.

Depolama alanı kullanımınızı, depolama sınırı, depolama yüzdesi ve depolama kullanılan ölçümleri kullanarak Azure portal (Azure Izleyici ile) izleyebilirsiniz. Ölçümler hakkında bilgi edinmek için [İzleme makalesine](./concepts-monitoring.md) bakın. 

### <a name="reaching-the-storage-limit"></a>Depolama sınırına ulaşma

Sunucuda tüketilen depolama alanı sağlanan sınıra ulaşılmaya yakın olduğunda, sunucu üzerinde kayıp yazma işlemlerini korumak için sunucu salt okuma moduna konur. 100 GiB 'tan daha az kaynak sağlanmış depolama alanı, sağlanan depolama boyutunun %5 ' inden daha az olduğunda salt okunurdur olarak işaretlenir. 100 ' den fazla GiB tarafından sağlanan depolama alanı, boş depolama 5 GiB 'den az olduğunda salt yazılır olarak işaretlenir.

Örneğin, 110 GiB depolama alanı sağladıysanız ve gerçek kullanım 105 GiB üzerine gittiğinde, sunucu salt okunurdur olarak işaretlenir. Alternatif olarak, 5 GiB depolama alanı sağladıysanız, ücretsiz depolama 256 MB 'tan az kaldığında sunucu salt okunurdur olarak işaretlenir.

Hizmet sunucuyu salt okunur duruma getirdiğinde tüm yeni yazma işlemi istekleri engellenir ve var olan etkin işlemler yürütülmeye devam eder. Sunucu salt okunur olarak ayarlandığında sonraki tüm yazma girişimleri ve işlemler başarısız olur. Okuma sorguları kesintisiz olarak çalışmaya devam eder. 

Sunucuyu salt okuma modundan almak için sunucuda sağlanan depolamayı artırmanız gerekir. Bu, Azure portal veya Azure CLı kullanılarak yapılabilir. Arttırıldıktan sonra sunucu, yazma işlemlerini yeniden kabul etmeye hazırlanacaktır.

Salt okuma durumuna ulaşmaktan kaçınmak için, sunucu depoağınızın eşiğe yaklaştığı durumlarda size bildirimde bulunan bir uyarı ayarlamanızı öneririz. Kullanılabilir ölçümler hakkında bilgi edinmek için [İzleme makalesine](./concepts-monitoring.md) bakın. 

Yapmanız önerilir <!--turn on storage auto-grow or to--> Sunucu depoağınızın eşiğe yaklaştığı durumlarda size bildirimde bulunan bir uyarı ayarlayın, böylece salt okuma durumuna ulaşabilirsiniz. Daha fazla bilgi için uyarıyı [ayarlama hakkında](how-to-alert-on-metric.md)uyarı belgeleri hakkındaki belgelere bakın.

### <a name="storage-auto-grow"></a>Depolama otomatik büyüme

Depolama otomatik büyüme, MySQL için Azure veritabanı esnek sunucusu için henüz kullanılamıyor.

## <a name="iops"></a>IOPS

MySQL için Azure veritabanı – esnek sunucu, ek ıOPS sağlama işlemini destekler. Bu özellik, tamamlayıcı ıOPS sınırının üzerinde ek ıOPS sağlamanıza olanak sağlar. Bu özelliği kullanarak, iş yükü gereksinimlerinize göre sağlanan ıOPS sayısını dilediğiniz zaman artırabilir veya azaltabilirsiniz. 

Minimum ıOPS, tüm işlem boyutlarında 100, maksimum ıOPS ise seçili işlem boyutuna göre belirlenir. Önizlemede, desteklenen maksimum ıOPS 20.000 ıOPS 'dir.

İşlem boyutu başına en fazla ıOPS hakkında daha fazla bilgi edinmek için aşağıda gösterilmektedir: 

| İşlem boyutu         | Maksimum ıOPS        | 
|----------------------|---------------------|
| **Burstable**        |                     |
| Standard_B1s         | 320                 |
| Standard_B1ms        | 640                 |
| Standard_B2s         | 1280                | 
| **Genel Amaçlı**  |                     |
| Standard_D2ds_v4     | 3200                |
| Standard_D4ds_v4     | 6400                |
| Standard_D8ds_v4     | 12800               |
| Standard_D16ds_v4    | 20000               |
| Standard_D32ds_v4    | 20000               |
| Standard_D48ds_v4    | 20000               | 
| Standard_D64ds_v4    | 20000               | 
| **Bellek için Iyileştirilmiş** |                     | 
| Standard_E2ds_v4     | 3200                | 
| Standard_E4ds_v4     | 6400                | 
| Standard_ E8ds_v4    | 12800               | 
| Standard_ E16ds_v4   | 20000               | 
| Standard_E32ds_v4    | 20000               | 
| Standard_E48ds_v4    | 20000               | 
| Standard_E64ds_v4    | 20000               |  

Maksimum ıOPS, işlem boyutu başına kullanılabilir maksimum ıOPS 'ye bağımlıdır. [B serisi](../../virtual-machines/sizes-b-series-burstable.md), [Ddsv4-Series](../../virtual-machines/ddv4-ddsv4-series.md)ve [Edsv4 serisi](../../virtual-machines/edv4-edsv4-series.md) belgelerinde *MAKSIMUM önbelleğe alınmamış disk aktarım hızı: IOPS/Mbps* sütununa bakın.

> [!Important]
> **TAMAMLAYıCı IOPS** , en düşük ("önbelleğe alınmış maksimum disk aktarım hızı: IOPS/Mbps" işlem boyutu Için, gib * 3 ' te sağlanan depolama alanı) değerine eşittir<br>
> **MINIMUM IOPS** tüm işlem boyutları boyunca 100 ' dir<br>
> **MAKSIMUM IOPS** , seçilen işlem boyutu tarafından belirlenir. Önizlemede, desteklenen maksimum ıOPS 20.000 ıOPS 'dir.

G/ç tüketiminizi, Azure portal (Azure Izleyici ile) üzerinde [GÇ yüzdesi](./concepts-monitoring.md) ölçümünü kullanarak izleyebilirsiniz. Daha fazla ıOPS 'ye ihtiyacınız varsa, en fazla ıOPS işlem temelinde, sunucunuzun işlem ölçeğini ölçeklendirmeniz gerekir.

## <a name="backup"></a>Backup

Hizmet, sunucunuzun yedeklerini otomatik olarak alır. 1 ile 35 gün arasında bir bekletme dönemi seçebilirsiniz. [Yedekleme ve geri yükleme kavramlarını yükleme makalesindeki](concepts-backup-restore.md)yedeklemeler hakkında daha fazla bilgi edinin.

## <a name="scale-resources"></a>Kaynakları ölçeklendirme

Sunucunuzu oluşturduktan sonra, işlem katmanını, işlem boyutunu (Vçekirdekler ve bellek) ve depolama miktarını ve yedekleme saklama süresini bağımsız olarak değiştirebilirsiniz. İşlem boyutu yukarı veya aşağı ölçeklendirilebilir. Yedekleme saklama süresi 1 ile 35 gün arasında ölçeklendirilebilir veya azaltılabilir. Depolama boyutu yalnızca artırılabilir. Kaynakların ölçeklendirilmesi portal veya Azure CLı aracılığıyla yapılabilir.

> [!NOTE]
> Depolama boyutu yalnızca artırılabilir. Artdıktan sonra daha küçük bir depolama boyutuna dönemezsiniz.

İşlem katmanını veya işlem boyutunu değiştirdiğinizde, yeni sunucu türünün etkili olması için sunucu yeniden başlatılır. Sistem yeni sunucuya geçerken yeni bağlantı kurulamaz ve tüm işlenmemiş işlemler geri alınır. Bu pencere farklılık gösterir, ancak çoğu durumda 60-120 saniye arasındadır. 

Depolamanın ölçeklendirilmesi ve yedekleme saklama süresinin değiştirilmesi çevrimiçi işlemlerdir ve sunucu yeniden başlatması gerektirmez.

## <a name="pricing"></a>Fiyatlandırma

En güncel fiyatlandırma bilgileri için bkz. hizmet [fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/MySQL/). İstediğiniz yapılandırmanın maliyetini görmek için [Azure Portal](https://portal.azure.com/#create/Microsoft.MySQLServer/flexibleServers) , seçtiğiniz seçeneklere göre **işlem + depolama** sekmesindeki aylık maliyeti gösterir. Azure aboneliğiniz yoksa, tahmini bir fiyat almak için Azure Fiyatlandırma hesaplayıcısı ' nı kullanabilirsiniz. [Azure Fiyatlandırma Hesaplayıcı](https://azure.microsoft.com/pricing/calculator/) Web sitesinde, **öğe Ekle**' yi seçin, **veritabanları** kategorisini genişletin, **MySQL için Azure veritabanı**' nı seçin ve seçenekleri özelleştirmek için dağıtım türü olarak **esnek sunucu** ' yı seçin.

Sunucu maliyetini iyileştirmek isterseniz, aşağıdaki ipuçlarını göz önünde bulundurun:

- İşlem az kullanılıyorsa işlem katmanınızı veya işlem boyutunu (Vçekirdekler) ölçeklendirin.
- İş yükünüz, Genel Amaçlı ve bellek için Iyileştirilmiş katmanlardan sürekli olarak tam işlem kapasitesine ihtiyaç duymazsa, Burstable işlem katmanına geçmeyi düşünün.
- Kullanımda olmadığında sunucuyu durdurun.
- Yedeklemenin daha uzun tutulması gerekmiyorsa, yedekleme saklama süresini azaltın.

## <a name="next-steps"></a>Sonraki adımlar

- [Portalda MySQL sunucusu oluşturmayı](quickstart-create-server-portal.md)öğrenin.
- [Hizmet sınırlamaları](concepts-limitations.md)hakkında bilgi edinin.
