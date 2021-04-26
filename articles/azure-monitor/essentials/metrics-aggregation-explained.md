---
title: Azure Izleyici ölçümleri ölçüm toplamı ve görüntüleme açıklanmaktadır
description: Azure Izleyici 'de ölçümlerin nasıl toplandığından ilgili ayrıntılı bilgi
author: rboucher
ms.author: robb
services: azure-monitor
ms.topic: conceptual
ms.date: 03/10/2021
ms.openlocfilehash: c89b352954f114ec9da22cad6751bb57ef59899b
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106381806"
---
# <a name="azure-monitor-metrics-metrics-aggregation-and-display-explained"></a>Azure Izleyici ölçümleri ölçüm toplamı ve görüntüleme açıklanmaktadır

Bu makalede, Azure Izleyici [platformu ölçümlerini](../data-platform.md) ve [özel ölçümleri](../essentials/metrics-custom-overview.md)geri yükleyen Azure izleyici zaman serisi veritabanında ölçümlerin toplamı açıklanmaktadır. Bu makale ayrıca standart [Application Insights ölçümleri](../app/app-insights-overview.md)için de geçerlidir. 

Bu karmaşık bir konudur ve Azure Izleyici ölçümlerini etkin bir şekilde kullanmak için bu makaledeki tüm bilgileri anlamak için gerekli değildir.

## <a name="overview-and-terms"></a>Genel bakış ve koşullar

Bir grafiğe ölçüm eklediğinizde, Ölçüm Gezgini varsayılan toplamayı otomatik olarak önceden seçer. Varsayılan değer temel senaryolarda anlamlı hale gelir, ancak ölçüm hakkında daha fazla öngörü kazanmak için farklı toplamalar kullanabilirsiniz. Bir grafikteki farklı toplamaları görüntülemek için ölçüm Gezgini 'nin bunları nasıl işlediğini anlamanız gerekir.

Daha önce az sayıda terim tanımlayalim:

- **Ölçüm değeri** : belirli bir kaynak için toplanan tek bir ölçüm değeri.
- **Zaman serisi veritabanı** -bir değer ve karşılık gelen zaman damgası içeren veri noktalarının depolanması ve alınması için iyileştirilmiş bir veritabanı. 
- **Zaman aralığı** : genel bir zaman dilimi.
- **Zaman aralığı** : iki ölçüm değerinin toplanması arasındaki süre. 
- **Zaman aralığı** : bir grafikte görünen zaman dilimi. Tipik varsayılan değer 24 saattir. Yalnızca belirli aralıklar kullanılabilir. 
- **Zaman ayrıntı düzeyi** veya **zaman dilimi** : bir grafik üzerinde görüntülenmek üzere değerleri bir araya getirmek için kullanılan zaman aralığı. Yalnızca belirli aralıklar kullanılabilir. Geçerli en az 1 dakikadır. Zaman ayrıntı düzeyi değeri seçilen zaman aralığından daha küçük olmalıdır; Aksi takdirde, tüm grafik için yalnızca bir değer gösterilir. 
- **Toplama türü** : birden çok ölçüm değerinden hesaplanan bir istatistik türü.  
- **Toplama** : birden fazla giriş değeri alma ve sonra toplama türü tarafından tanımlanan kurallar aracılığıyla tek bir çıkış değeri oluşturmak için bunları kullanma işlemi. Örneğin, birden çok değerin ortalamasını alma.  

## <a name="summary-of-process"></a>İşlemin Özeti

Ölçümler, zaman damgasıyla depolanan bir değer dizisidir. Azure 'da, çoğu ölçüm Azure ölçümleri zaman serisi veritabanında depolanır. Bir grafiği çizerseniz seçili ölçümlerin değerleri veritabanından alınır ve ardından seçilen zaman ayrıntı düzeyi (zaman dilimi olarak da bilinir) temelinde ayrı olarak toplanır. Zaman ayrıntı düzeyinin boyutunu [Ölçüm Gezgini saat seçici panelini](../essentials/metrics-getting-started.md#select-a-time-range)kullanarak seçersiniz. Açık bir seçim yapmazsanız zaman ayrıntı düzeyi, o anda seçili zaman aralığına göre otomatik olarak seçilir. Seçildiğinde, her zaman ayrıntı düzeyi aralığı boyunca yakalanan ölçüm değerleri toplanır ve her Aralık için tek bir DataPoint şemasına yerleştirilir.

## <a name="aggregation-types"></a>Toplama türleri 

Ölçüm Gezgininde beş temel toplama türü mevcuttur. Ölçüm Gezgini, ilgisiz olan ve belirli bir ölçüm için kullanılamayan toplamaları gizler. 

- **Sum** : toplama aralığı boyunca yakalanan tüm değerlerin toplamı. Bazen toplam toplama olarak adlandırılır.
- **Count** : toplama aralığı boyunca yakalanan ölçüm sayısı. Count, ölçümün değerine ve yalnızca kayıt sayısına göre görünmüyor. 
- **Average** : toplama aralığı boyunca yakalanan ölçüm değerlerinin ortalaması. Çoğu ölçüm için bu değer Sum/Count değeridir. 
- **Min** : toplama aralığı boyunca yakalanan en küçük değer.
- **Max** : toplama aralığı boyunca yakalanan en büyük değer.

Örneğin, bir grafiğin, son 24 saatlik zaman aralığında **Toplam** toplamayı kullanarak bir VM Için **ağ çıkış toplamı ölçüsünü gösterdiğini** varsayalım. Aşağıdaki ekran görüntüsünde görüldüğü gibi, zaman aralığı ve ayrıntı düzeyi grafiğin sağ üst kısmından değiştirilebilir.

:::image type="content" source="media/metrics-aggregation-explained/time-range-granularity-picker.png" alt-text="Zaman aralığı ve zaman ayrıntı düzeyi seçiciyi gösteren ekran görüntüsü" border="true":::

Zaman ayrıntı düzeyi = 30 dakika ve zaman aralığı = 24 saat için:

- Grafik, 48 veri noktalarından çizilir. Bu, saatte 24 saat x 2 veri noktası (60min/30dk) toplanmış 1 dakikalık veri noktaları. 
- Çizgi grafik, grafik çizim alanında 48 nokta bağlar. 
- Her DataPoint, ilgili 30 dakikalık zaman döneminin her biri sırasında gönderilen tüm ağ çıkış baytlarının toplamını temsil eder. 


 :::image type="content" source="media/metrics-aggregation-explained/24-hour-30-min-gran.png" alt-text="Bir çizgi grafiğindeki verileri 24 saat aralığında ve 30 dakikalık zaman parçalı olarak gösteren ekran görüntüsü" border="true" lightbox="media/metrics-aggregation-explained/24-hour-30-min-gran.png":::

*Daha büyük sürümleri görmek için bu bölümdeki görüntülere tıklayın.*

Zaman parçalı yapısını 15 dakikaya geçerseniz, grafik 96 Birleşik veri noktalarından çizilir. Yani, 60min/15min = 4 veri noktası/saat x 24 saat.

:::image type="content" source="media/metrics-aggregation-explained/24-hour-15-min-gran.png" alt-text="Bir çizgi grafiğindeki verileri 24 saatlik zaman aralığı ve 15 dakikalık zaman parçalı olarak gösteren ekran görüntüsü" border="true" lightbox="media/metrics-aggregation-explained/24-hour-15-min-gran.png":::

5 dakikalık zaman ayrıntı düzeyi için 24 x (60/5) = 288 noktası alırsınız. 

:::image type="content" source="media/metrics-aggregation-explained/24-hour-5-min-gran.png" alt-text="Bir çizgi grafiğindeki verileri 24 saatlik zaman aralığı ve 5 dakikalık zaman ayrıntı düzeyi olarak gösteren ekran görüntüsü" border="true" lightbox="media/metrics-aggregation-explained/24-hour-15-min-gran.png":::

1 dakikalık zaman ayrıntı düzeyi (grafikte olabilecek en küçük) için 24 x 60/1 = 1440 noktası elde edersiniz. 

:::image type="content" source="media/metrics-aggregation-explained/24-hour-1-min-gran.png" alt-text="Bir çizgi grafiğindeki verileri 24 saatlik zaman aralığı ve 1 dakikalık zaman ayrıntı düzeyi olarak gösteren ekran görüntüsü" border="true" lightbox="media/metrics-aggregation-explained/24-hour-1-min-gran.png":::

Grafikler, önceki ekran görüntülerinde gösterildiği gibi bu toplamalar için farklı şekilde görünür.  Bu sanal makinenin, zaman penceresi geri kalanı ile ilgili küçük bir zaman diliminde çok fazla çıktı olduğunu fark edin.  

Zaman yapısı, bir grafikteki "sinyal-gürültü" oranını ayarlamanıza olanak sağlar. Daha yüksek toplamalar paraziti ve kesintisiz artışları kaldırır.  En alttaki 1 dakikalık grafikteki değişikliklere ve daha yüksek ayrıntı düzeyi değerlerine giderek bunları nasıl düzgünleştirdiğine dikkat edin. 

Bu düzgünleştirme davranışı, bu verileri diğer sistemlere gönderdiğinizde önemlidir. Örneğin, uyarılar. Genellikle, genellikle %90 üzerinden CPU süresi içinde çok kısa ani artışlar için uyarı almak istemezsiniz. Ancak CPU, 5 dakika boyunca %90 ' de kalırsa, bu durum büyük olasılıkla önemlidir. CPU (veya herhangi bir ölçüm) üzerinde bir uyarı kuralı ayarlarsanız, zaman ayrıntı düzeyi daha büyük hale getirmek aldığınız yanlış uyarı sayısını azaltabilir. 

İş yükünüz için hangi zaman aralığının en iyi olduğunu öğrenmesi için "normal" ne olduğunu belirlemek önemlidir. Bu, burada kapsanmayan farklı bir konu olan [dinamik uyarıların](../alerts/alerts-dynamic-thresholds.md)avantajlarından biridir.  

## <a name="how-the-system-collects-metrics"></a>Sistemin ölçümleri nasıl toplar

Veri toplama, ölçüme göre farklılık gösterir. 

### <a name="measurement-collection-frequency"></a>Ölçüm toplama sıklığı 

İki tür koleksiyon dönemi vardır.

- **Normal** -ölçüm, farklılık gösteren tutarlı bir zaman aralığı içinde toplanır.

- **Etkinlik tabanlı** -belirli bir türden bir işlem gerçekleştiğinde ölçüm toplanır. Her işlemin bir ölçüm girişi ve zaman damgası vardır. Belirli bir süre boyunca değişen sayıda kayıt olması için düzenli aralıklarla toplanmazlar. 

### <a name="granularity"></a>Ayrıntı düzeyi

En kısa zaman aralığı 1 dakikadır, ancak temel alınan sistem, ölçüme göre verileri daha hızlı yakalayabilir. Örneğin, CPU yüzdesi düzenli bir aralıkta her 15 saniyede bir izlenir. HTTP sorunları işlem olarak izlendiğinden, bunlar bir dakikadan çok daha fazla bir dakika daha uzun olabilir. SQL depolama gibi diğer ölçümler 20 dakikada bir yakalanır. Bu seçenek, bireysel kaynak sağlayıcısına ve türüne sahiptir. Mümkün olan en küçük aralığı sağlamaya çalışır.

### <a name="dimensions-splitting-and-filtering"></a>Boyutlar, bölme ve filtreleme

Ölçümler her bir kaynak için yakalanır. Ancak, ölçümlerin toplandığı, depolandığı ve gösterebileceği düzey farklılık gösterebilir. Bu düzey, **ölçüm boyutlarında** bulunan ek ölçümler tarafından temsil edilir. Her tek kaynak sağlayıcı, topladıkları verilerin ne kadar ayrıntılı olduğunu tanımlar. Azure Izleyici yalnızca bu ayrıntıların nasıl sunulduğunu ve depolanacağını tanımlar. 

Ölçüm Gezgini 'nde bir ölçümü grafikleyerek, grafiği bir boyuta göre "bölme" seçeneğiniz vardır.  Bir grafiği bölmek, daha fazla ayrıntı için temel alınan verilere baktığınız ve Ölçüm Gezgini 'nde verilerin görülendiğini veya filtrelendiğini görmenizi sağlayan anlamına gelir.

Örneğin, [Microsoft. Apimanayönetimi/hizmeti](./metrics-supported.md#microsoftapimanagementservice) birçok ölçüm için bir boyut olarak *konum* içerir. 

- **Kapasite** , bu tür bir ölçümdür. *Konum* boyutunun olması, temeldeki sistemin toplam miktar için değil, her bir konumun kapasitesi için bir ölçüm kaydı depoladığını gösterir. Daha sonra bu bilgileri bir ölçüm grafiğinde alabilir veya ayırabilirsiniz.  

- **Ağ geçidi Isteklerinin genel süresine** bakarak, 2 boyut *konumu* ve *ana bilgisayar adı* vardır ve bir sürenin konumunu ve hangi ana bilgisayar adının geldiğini bilmenizi sağlar.  

- Daha esnek **ölçülerden** biri olan istekler 7 farklı boyuta sahiptir. 
 
Her ölçüm ve kullanılabilir boyutlar hakkındaki ayrıntılar için Azure Izleyici [ölçümleri destekleniyor](./metrics-supported.md) makalesine bakın. Bunlara ek olarak, her bir kaynak sağlayıcısı ve türü için belgeler, Boyutlar ve ölçüledikleri özellikler hakkında ek bilgiler sağlayabilir.

Bir sorunu gidermek için bölme ve filtreleme işlemlerini birlikte kullanabilirsiniz. Aşağıda, bir kaynak grubundaki bir VM grubu için *Ortalama Disk yazma baytlarının* gösterildiği bir grafik örneği verilmiştir. Bu ölçüme sahip tüm VM 'lerin topladığımızda, ancak 6:00'DA ilgili en yüksek dünyada gerçekten sorumlu olduğunu görmek istiyoruz. Aynı makineli mı? Kaç makine katıldı?  

:::image type="content" source="media/metrics-aggregation-explained/total-disk write-bytes-all-VMs.png" alt-text="Contoso oteller kaynak grubundaki tüm sanal makineler için toplam disk yazma baytlarını gösteren ekran görüntüsü" border="true" lightbox="media/metrics-aggregation-explained/total-disk write-bytes-all-VMs.png":::

*Daha büyük sürümleri görmek için bu bölümdeki görüntülere tıklayın.*

Bölmeyi uyguladığımızda, temel alınan veriler görüyoruz, ancak bu, bir Mess 'nin bir bitidir. Yukarıdaki grafikte toplanan 20 VM 'yi etkinleştirir. Bu durumda, fare 'umuzu, CH-DCVM11 neden olduğunu belirten 6:00'DA büyük tepeli üzerine gelme için kullandık. Ancak, diğer VM 'Ler grafik üzerinde bulunduğundan, bu VM ile ilişkili verilerin geri kalanını görmek zordur. 

:::image type="content" source="media/metrics-aggregation-explained/split-total-disk write-bytes-all-VMs.png" alt-text="Contoso oteller kaynak grubundaki tüm sanal makineler için sanal makine adına göre bölünmüş disk yazma baytlarını gösteren ekran görüntüsü" border="true" lightbox="media/metrics-aggregation-explained/split-total-disk write-bytes-all-VMs.png":::

Filtreleme kullanmak, gerçekten ne olduğunu görmek için grafiği temizleyebilmemize olanak sağlar. Görmek istediğiniz VM 'Leri denetleyebilir veya işaretini kaldırabilirsiniz. Noktalı çizgilere dikkat edin. Bunlar sonraki bir bölümde bahsedilir.

:::image type="content" source="media/metrics-aggregation-explained/split-filter-total-disk write-bytes-all-VMs.png" alt-text="Contoso oteller kaynak grubundaki tüm sanal makineler için sanal makine adına göre bölünmüş ve filtrelenmiş disk yazma baytlarını gösteren ekran görüntüsü" border="true" lightbox="media/metrics-aggregation-explained/split-filter-total-disk write-bytes-all-VMs.png":::

Ölçüm Gezgini grafiğinde bölünmüş boyut verilerinin nasıl gösterileceği hakkında daha fazla bilgi için bkz. [Ölçüm Gezgini 'Nin gelişmiş özellikleri-filtreler ve bölme](../essentials/metrics-charts.md#filters).

### <a name="null-and-zero-values"></a>NULL ve sıfır değerleri

Sistem bir kaynaktan ölçüm verileri beklerken ancak onu almadığınızda, NULL bir değer kaydeder.  NULL değeri, Toplamalar ve grafik hesaplamasında önemli olacak şekilde sıfır değerinden farklıdır. NULL değerler geçerli ölçümler olarak sayılmaz. 

Null değerler farklı grafiklerde farklı şekilde görünür. Dağılım çizimleri grafikteki bir noktayı gösteren atlama. Çubuk grafikler çubuğu göstermeyi atlar. Çizgi grafiklerde, NULL, önceki bölümde yer alan ekran görüntüsünde gösterilenler gibi [noktalı veya kesikli çizgiler](../essentials/metrics-troubleshoot.md#chart-shows-dashed-line) gösterebilir. Null değerleri içeren ortalamalar hesaplanırken, ortalamasını almak için daha az veri noktası vardır.  Bu davranış bazen bir grafikteki değerlerde beklenmedik bir şekilde bir bırakmaya neden olabilir, ancak değer sıfıra dönüştürülüp geçerli bir DataPoint olarak kullanılıyorsa, genellikle küçüktür.  

[Özel ölçümler](../essentials/metrics-custom-overview.md) , hiçbir veri alınmadığında her zaman null değerleri kullanır. [Platform ölçümleri](../data-platform.md)ile her kaynak sağlayıcı, belirli bir ölçüm için en mantıklı hale göre ne kadar mantıklı veya null değerleri kullanacağınızı belirler.

Azure Izleyici uyarıları, kaynak sağlayıcının ölçüm veritabanına yazdığı değerleri kullanır. bu nedenle, önce verileri görüntüleyerek kaynak sağlayıcının null değerleri nasıl işleyeceğini bilmek önemlidir.

## <a name="how-aggregation-works"></a>Toplama nasıl kullanılır?

Önceki sistemdeki ölçüm grafikleri, farklı türlerde toplanan verileri gösterir. Sistem, istenen grafiklerin çok sayıda yinelenen hesaplama olmadan daha hızlı görünmesi için verileri önceden toplar.  

Bu örnekte:

- **Http hatalarıyla** bilinen **kurgusal** bir işlem ölçümü topluyor 
- *Sunucu* , **http arızaları** ölçümü için bir boyuttur.
- 3 sunucuumuz var-sunucu A, B ve C.

Açıklamayı basitleştirmek için yalnızca SUM toplama türü ile başlayacağız. 

### <a name="sub-minute-to-1-minute-aggregation"></a>Alt dakikayı 1 dakikalık toplama

İlk ham ölçüm verileri, Azure Izleyici ölçümleri veritabanında toplanır ve depolanır. Bu durumda, *sunucu* bir boyut olduğundan, her sunucuda işlem zaman damgasıyla depolanan işlem kayıtları vardır. Müşteri olarak görüntüleyebileceğiniz en küçük dönem 1 dakikadır, bu zaman damgaları ilk olarak her bir sunucu için 1 dakikalık ölçüm değerlerine toplanır.  Sunucu B için toplama işlemi aşağıdaki grafikte gösterilmiştir. A ve C sunucuları aynı şekilde yapılır ve farklı verilere sahiptir.  

:::image type="content" source="media/metrics-aggregation-explained/sub-minute-transaction.png" alt-text="Alt dakikalık işlem girdilerini 1 dakikalık toplamalara gösteren ekran görüntüsü. " border="false":::

Sonuç 1 dakikalık toplanmış değerler, daha sonra hesaplamalar için toplanabilmeleri için ölçüm veritabanında yeni girişler olarak depolanır. 

:::image type="content" source="media/metrics-aggregation-explained/sub-minute-transaction-dimension-aggregated.png" alt-text="Sunucu boyutu genelinde birden fazla 1 dakikalık toplanmış girişi gösteren ekran görüntüsü. Tek tek gösterilen sunucu A, B ve C" border="false":::

### <a name="dimension-aggregation"></a>Boyut toplama

1 dakikalık hesaplamalar daha sonra boyut tarafından daraltılır ve ayrı kayıtlar olarak depolanır.   Bu durumda, tüm bireysel sunuculardaki tüm veriler 1 dakikalık bir aralıkta toplanır ve sonraki toplamalarında kullanılmak üzere ölçüm veritabanında depolanır.

:::image type="content" source="media/metrics-aggregation-explained/1-minute-transaction-dimension-flattened-aggregated.png" alt-text="A, B ve C Server A 'nın 1 dakikalık toplam toplanmış giriş sayısını gösteren ekran görüntüsü 1 dakikalık tüm sunucular varlık" border="false":::

Açıklık için aşağıdaki tabloda toplama yöntemi gösterilmektedir.

| Dönem   | Sunucu A | Sunucu B | Sunucu C | Sum (A + B + C)|
| -------- | -------- | -------- | -------- | --------   |  
| Dakika 1 | 1        | 1        | 1        | 3          |
| Dakika 2 | 0        | 5        | 1        | 6          |
| Dakika 3 | 0        | 5        | 1        | 6          |
| Dakika 4 | 2        | 3        | 4        | 9          |
| Dakika 5 | 1        | 0        | 3        | 4          |
| Dakika 6 | 1        | 0        | 4        | 5          |
| Dakika 7 | 1        | 2        | 4        | 7          |
| Dakika 8 | 0        | 1        | 0        | 1          |
| Dakika 9 | 1        | 1        | 4        | 6          |
| Dakika 10| 2        | 1        | 0        | 3          |

Yukarıda yalnızca bir boyut gösteriliyor, ancak bir ölçümün desteklediği **tüm boyutlar** için aynı toplama ve depolama süreci oluşur.

- Değerleri bu boyut tarafından 1 dakikalık toplanmış küme olarak toplayın. Bu değerleri depolayın. 
- Boyutu 1 dakikalık toplam toplama olarak daraltın. Bu değerleri depolayın. 

NetworkAdapter adlı bir HTTP arızasından oluşan bir boyut tanıtalım. Sunucu başına farklı sayıda bağdaştırıcıya sahip olduğumuz için diyelim.

- Sunucu A 'nın 1 bağdaştırıcısı vardır
- Sunucu B 'nin 2 bağdaştırıcısı vardır
- Sunucu C 'nin 3 bağdaştırıcısı vardır

Aşağıdaki işlemlere yönelik verileri ayrı olarak topladık. Bunlar ile işaretlenir:

- Bir saat
- Bir değer
- İşlemin geldiği sunucu
- İşlemin geldiği bağdaştırıcı

Bu alt dakika akışlarının her biri, 1 dakikalık zaman serisi değerlerinde toplanır ve Azure Izleyici ölçüm veritabanında depolanır:

- Sunucu A, Bağdaştırıcı 1
- Sunucu B, Bağdaştırıcı 1
- Sunucu B, Bağdaştırıcı 2
- Sunucu C, Bağdaştırıcı 1
- Sunucu C, Bağdaştırıcı 2
- Sunucu C, bağdaştırıcı 3

Ayrıca, aşağıdaki daraltılmış toplamalar da depolanacak:

- Sunucu A, Bağdaştırıcı 1 (daraltılacak bir şey olmadığından, yeniden depolanacak)
- Sunucu B, Bağdaştırıcı 1 + 2
- Sunucu C, Bağdaştırıcı 1 + 2 + 3
- Tüm sunucular, bağdaştırıcılar

Bu, çok sayıda boyuta sahip ölçümlerin daha fazla toplama sayısına sahip olduğunu gösterir. Tüm permütasyonların anlaşılması önemli değildir; bu da anlamayı anlamak yeterlidir. Sistem, herhangi bir grafiğe erişim için hızlı bir şekilde veri ve toplanan verilerin her ikisine de depolanmasını istiyor. Sistem, görüntüleme yaptığınız seçeneğe bağlı olarak en uygun saklı toplamayı veya temel alınan ham verileri seçer. 

### <a name="aggregation-with-no-dimensions"></a>Boyut olmadan toplama

Bu ölçüm bir boyut *sunucusuna* sahip olduğu için, bu makalede daha önce anlatıldığı gibi bölme ve filtreleme aracılığıyla yukarıdaki sunucu a, B ve C için temel verilere ulaşabilirsiniz. Metriğin bir boyut olarak *sunucusu* yoksa, bir müşteri olarak yalnızca diyagramda siyah olarak gösterilen toplanmış 1 dakikalık toplamları erişebilir. Diğer bir deyişle, 3, 6, 6, 9 vb. değerleri. Sistem Ayrıca, bölünmüş değerleri toplamak için temel alınan çalışmayı, Ölçüm Gezgini 'nde kullanmaz veya ölçüm REST API aracılığıyla hiçbir şekilde göndermez. 

## <a name="viewing-time-granularities-above-1-minute"></a>1 dakikalık bir süre içinde zaman granluğunu görüntüleme

Daha büyük bir ayrıntı düzeyinde ölçümler için sorun yaparsanız, sistem, daha büyük zaman kapsamı toplamlarını hesaplamak için 1 dakikalık toplanmış toplamları kullanır.  Aşağıda, noktalı çizgiler 2 dakikalık ve 5 dakikalık zaman uyumlu olmayan toplam yöntemini gösterir. Yine basitlik için yalnızca SUM toplama türünü gösteriyoruz.

:::image type="content" source="media/metrics-aggregation-explained/1-minute-to-2-min-5-min.png" alt-text="2-dak ve 5 dakikalık zaman aralıklarında toplanan sunucu boyutunda birden çok 1 dakikalık toplanmış girişi gösteren ekran görüntüsü." border="false":::

2 dakikalık zaman ayrıntı düzeyi için.

| Dönem       | Toplamları       
| -------------|-------------|  
| 1 & dakikası 2 | (3 + 6) = 9 |
| Dakika 3 & 4 | (6 + 9) = 15|
| Dakika 4 & 5 | (4 + 5) = 9 |
| Dakika 6 & 7 | (7 + 1) = 8 |
| Dakika 8 & 9 | (6 + 3) = 9 |

5 dakikalık zaman ayrıntı düzeyi için.

| Dönem              |            Toplamları        |
|---------------------|------------------------|  
| Dakika 1 ila 5  | 3 + 6 + 6 + 9 + 4 = 28 |
| Dakika 6 ila 10 | 5 + 7 + 1 + 6 + 3 = 22 |

Sistem, en iyi performansı sağlayan depolanan toplanmış verileri kullanır. 

Aşağıda, yukarıdaki 1 dakikalık toplama süreci için daha büyük diyagram bulunur

:::image type="content" source="media/metrics-aggregation-explained/sum-aggregation-full.png" alt-text="Önceki 3 ekran görüntülerinin birleştirilmesi gösteren ekran görüntüsü. 1 dakikalık, 2 dakikalık ve 5 dakikalık aralıklarda toplanan sunucu boyutu boyunca birden çok 1 dakikalık toplanan giriş. Tek tek gösterilen sunucu A, B ve C" border="false":::

## <a name="more-complex-example"></a>Daha karmaşık örnek

Aşağıda, milisaniye cinsinden HTTP yanıt süresi adlı kurgusal bir metrikteki değerler kullanılarak daha büyük bir örnek verilmiştir.  Burada ek karmaşıklık düzeyleri tanıtıldık.

1. Sum, Count, min ve Max için toplama, ortalama için hesaplama gösteririz.
2. NULL değerler ve bunların hesaplamaları nasıl etkilediği gösterilmektedir. 

Aşağıdaki örneği inceleyin. Kutular ve oklar, değerlerin nasıl toplandığını ve hesaplanmadığını örnekler gösterir. 

Önceki bölümde açıklandığı gibi aynı 1 dakikalık ön kimlik doğrulama süreci toplamlar, say, minimum ve maksimum için gerçekleşir.  Ancak, ortalama önceden toplanmaz. Hesaplama hatalarından kaçınmak için toplanan veriler kullanılarak yeniden hesaplanır.
 
:::image type="content" source="media/metrics-aggregation-explained/full-aggregation-example-all-types.png" alt-text="Sum, Count, min, Max ve Average 'ın 1 dakikadan 10 dakikaya kadar karmaşık toplama ve hesaplama örneğini gösteren ekran görüntüsü." border="false" lightbox="media/metrics-aggregation-explained/full-aggregation-example-all-types.png":::

Yukarıda vurgulanan 1 dakikalık toplama için dakika 6 ' yı göz önünde bulundurun. Bu dakika, büyük olasılıkla yeniden başlatma nedeniyle sunucu B 'nin çevrimdışı ve raporlama verilerinin durdurulduğu bir noktasıdır. 

Yukarıdaki dakika 6 ' dan, hesaplanan 1 dakikalık toplama türleri şunlardır: 

| Toplama türü | Değer        | Notlar |
|------------------|--------------|-------|
| Sum              | 53 + 20 = 73 | |
| Count            | 2            | Null değerleri etkisini gösterir.  Sunucu çevrimiçi olsaydı değer 3 olur.  |
| Minimum          | 20           | |
| Maksimum          | 53           | |
| Ortalama          | 73/2       | Her zaman toplamı sayı olarak bölünür. Bu ayrıntı düzeyi için toplanan sayılar kullanılarak hiçbir zaman ayrıntı düzeyi için her zaman bir şekilde depolanmaz ve her zaman yeniden hesaplanır. Yukarıda vurgulanan 5 dakikalık ve 10 dakikalık zaman uyumlu olmayan bir şekilde yeniden hesaplamaya dikkat edin. |

Kırmızı metin rengi, normal aralığın dışında kabul edilebilir olan değerleri gösterir ve ayrıntı düzeyi zaman içinde olduğu gibi, nasıl yayıldığını (veya başarısız olduğunu) gösterir. *En düşük* ve *en yüksek* bir sorun olduğunu nasıl belirttiğine dikkat edin.    

Null değerleri, bunun yerine sıfırlardan daha iyi bir hesaplama olduğunu da görebilirsiniz.  

> [!NOTE] 
> Bu örnekte durum olmasa da, bir ölçümün her zaman 1 değeriyle yakalanması durumunda *Count* değeri *Sum* değerine eşittir. Bu, bir ölçüm bir işlem olayının oluşumunu izliyorsa yaygındır. Örneğin, bu makaledeki önceki örnekte bahsedilen HTTP hatalarının sayısı.

## <a name="next-steps"></a>Sonraki adımlar

- [Ölçüm Gezgini 'ni kullanmaya başlama](../essentials/metrics-getting-started.md)
- [Gelişmiş Ölçüm Gezgini](../essentials/metrics-charts.md)
