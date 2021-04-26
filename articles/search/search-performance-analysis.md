---
title: Performansı analiz etme
titleSuffix: Azure Cognitive Search
description: TBD
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.openlocfilehash: 298508f8068b47cbfc720f1d1e8ddf370323ed28
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106582380"
---
# <a name="analyze-performance-in-azure-cognitive-search"></a>Azure Bilişsel Arama performansını çözümleme

Bu makalede, Bilişsel Arama ' de sorgu ve dizin oluşturma performansını çözümlemek için araçlar, davranışlar ve yaklaşımlar açıklanmaktadır.

## <a name="develop-baseline-numbers"></a>Taban çizgisi numaraları geliştirme

Herhangi bir büyük uygulamada, üretim ortamına almadan önce Bilişsel Arama hizmetinizin performans değerlendirmesi testini yapmak önemlidir. Hem beklediğiniz arama sorgusu yükünü hem de beklenen veri alım iş yüklerini test etmelisiniz (mümkünse, bunları eşzamanlı olarak çalıştırabilirsiniz). Kıyaslama numaralarının olması, uygun [arama katmanını](search-sku-tier.md), [hizmet yapılandırmasını](search-capacity-planning.md)ve beklenen [sorgu gecikmesini](search-performance-analysis.md#average-query-latency)doğrulamaya yardımcı olur.

Kıyaslamalar geliştirmek için, [Azure-Search-Performance-Testing (GitHub)](https://github.com/Azure-Samples/azure-search-performance-testing) aracının kullanılması önerilir.

Dağıtılmış bir hizmet mimarisinin etkilerini yalıtmak için, bir çoğaltmanın ve bir bölümün hizmet yapılandırmalarında test etmeyi deneyin.

> [!NOTE]
> Depolama için Iyileştirilmiş Katmanlar (L1 ve L2) için, standart katmanlardan daha düşük bir sorgu üretilen işi ve daha yüksek gecikme süresi beklemelisiniz.
>

## <a name="use-diagnostic-logging"></a>Tanılama günlüğünü kullanma

Yöneticinin olası performans sorunlarını Tanılamadaki en önemli araç, arama hizmetinize ilişkin işletimsel verileri ve ölçümleri toplayan [tanılama günlüğü](search-monitor-logs.md) ' dir. Tanılama günlüğü, [Azure izleyici](../azure-monitor/overview.md)aracılığıyla etkinleştirilir. Azure Izleyici kullanımı ve verileri depolama ile ilişkili maliyetler vardır, ancak bu uygulamayı hizmetiniz için etkinleştirirseniz, performans sorunlarını araştırmaya devam edebilirsiniz.

Gecikme süresinin gerçekleşmesi, bir ağ aktarımı sırasında veya uygulama hizmetleri katmanındaki içeriğin işlenmesi sırasında veya bir arama hizmetinde olması gibi birden çok fırsat vardır. Tanılama günlüğü 'nün önemli bir avantajı, etkinliklerin arama hizmeti perspektifinden günlüğe kaydedildiği, bu da günlüğün, sorgu veya dizin oluşturma ile ilgili sorunları veya başka bir hata noktası olduğunu belirlemenize yardımcı olabileceği anlamına gelir.

:::image type="content" source="media/search-performance/perf-log-event-chain.png" alt-text="Günlüğe kaydedilen olayların Zinciri" border="true":::

Tanılama günlüğü, günlüğe kaydedilen bilgileri depolamak için seçenekler sağlar. Kullanım ve performans hakkında birçok soruyu yanıtlamak için, verilerle ilgili gelişmiş kusto sorgularını yürütebilmeniz için [Log Analytics](../azure-monitor/logs/log-analytics-overview.md) kullanmanızı öneririz. 

Arama hizmeti portalı sayfalarınızda, **Tanılama ayarları** aracılığıyla günlüğe kaydetmeyi etkinleştirebilir ve ardından **Günlükler**' i seçerek Log Analytics karşı kusto sorguları verebilirsiniz. Ayarlama hakkında daha fazla bilgi için bkz. [günlük verilerini toplama ve analiz etme](search-monitor-logs.md).

:::image type="content" source="media/search-performance/perf-log-menu-options.png" alt-text="Günlük menü seçenekleri" border="true":::

## <a name="throttling-behaviors"></a>Daraltma davranışları

Arama hizmeti, bir kaynak perspektifinden işleyebileceği sınıra ulaştığında kısıtlama oluşur. Sorgular veya dizin oluşturma sırasında azaltma gerçekleştirilebilir. İstemci tarafında, bir API çağrısı, kısıtlanmışsa 503 HTTP yanıtı ile sonuçlanır. Dizin oluşturma sırasında, bir veya daha fazla öğenin dizin oluşturamayacağını belirten bir 207 HTTP yanıtı alma olasılığı da vardır. Bu hata, arama hizmetinin kapasiteye yakın olduğunu gösteren bir göstergedir. 

Thumb 'in bir kuralı olarak, gördüğünüz azaltma miktarını en iyi şekilde düzeltmek en iyisidir. Örneğin, 500.000 olmayan bir arama sorgusu kısıtlanmamışsa, sorun büyük olabilir. Ancak, bir süre boyunca büyük bir sorgu yüzdesi daraltıldı, bu daha fazla sorun olacaktır. Bir dönem boyunca azaltmayı inceleyerek, azaltma işleminin daha olası olabileceği zaman çerçevelerini ve en iyi şekilde uyum sağlayacağınıza karar vermenize yardımcı olur.

Birçok daraltma sorununa yönelik basit bir çözüm, arama hizmetinde daha fazla kaynak (genellikle sorgu tabanlı azaltma için çoğaltmalar veya dizin oluşturma tabanlı azaltma için bölümler) oluşturmaya yöneliktir. Ancak, çoğaltmaları veya bölümleri artırmak maliyet ekler. bu nedenle, azaltmasının hiç neden meydana geldiğini bilmemiz önemlidir. Bir sonraki birkaç bölümde azaltma yapılmasına neden olan koşulların araştırması açıklanmaktadır. 

Aşağıda, yük altında olan arama hizmetinden gelen HTTP yanıtlarının dökümünü belirleyebilen bir kusto sorgusunun örneği verilmiştir. 7 günlük bir dönemi sorgularken, işlenen çubuk grafiğinde, başarılı (200) yanıtların sayısı ile karşılaştırıldığında, arama sorgularının görece büyük bir yüzdesinin kısıtlandığı gösterilmektedir.

```kusto
AzureDiagnostics
| where TimeGenerated > ago(7d)
| summarize count() by resultSignature_d 
| render barchart 
```

:::image type="content" source="media/search-performance/perf-bar-chart-http-errors.png" alt-text="Http hata sayımlarının çubuk grafiği" border="true":::

Belirli bir süre boyunca azaltmayı incelemek, azaltma işleminin daha sık gerçekleşebileceği zamanları belirlemenize yardımcı olabilir. Aşağıdaki örnekte, belirli bir zaman çerçevesinde gerçekleşen kısıtlanmış sorguların sayısını göstermek için zaman serisi grafiği kullanılır. Bu durumda, performans değerlendirmesi yapılan zamanlarla bağıntılı kısıtlanmış sorgular gerçekleştirildi.

```kusto
let ['_startTime']=datetime('2021-02-25T20:45:07Z');
let ['_endTime']=datetime('2021-03-03T20:45:07Z');
let intervalsize = 1m; 
AzureDiagnostics 
| where TimeGenerated > ago(7d)
| where resultSignature_d != 403 and resultSignature_d != 404 and OperationName in ("Query.Search", "Query.Suggest", "Query.Lookup", "Query.Autocomplete")
| summarize 
  ThrottledQueriesPerMinute=bin(countif(OperationName in ("Query.Search", "Query.Suggest", "Query.Lookup", "Query.Autocomplete") and resultSignature_d == 503)/(intervalsize/1m), 0.01)
  by bin(TimeGenerated, intervalsize)
| render timechart   
```

:::image type="content" source="media/search-performance/perf-line-chart-throttled-queries.png" alt-text="Kısıtlanmış sorguların çizgi grafiği" border="true":::

## <a name="measure-individual-queries"></a>Ayrı sorguları ölçme

Bazı durumlarda, nasıl çalıştığını görmek için tek tek sorguları test etmek yararlı olabilir. Bunu yapmak için, arama hizmetinin çalışmayı ne kadar süre sürdüğünü, ne kadar sürdüğünü istemciden ve istemciye geri gidiş dönüş isteği yapmak için ne kadar süreceğine bakmak önemlidir. Tanılama günlükleri, bireysel işlemleri aramak için kullanılabilir, ancak bunu Postman gibi bir istemci aracından yapmak daha kolay olabilir.

Aşağıdaki örnekte, bir REST tabanlı arama sorgusu yürütüldü. Her yanıta Bilişsel Arama, sorgunun tamamlanma süresi ("geçen süre" içinde üstbilgiler sekmesinde görünür). Yanıtın en üstünde durum ' un yanında, gidiş dönüş süresini bulacaksınız. Bu durumda, 418 milisaniye. Sonuçlar bölümünde "üstbilgiler" sekmesi seçilmiştir. Aşağıdaki görüntüde, bu iki değerin kırmızı bir kutu kullanılarak vurgulanmış olarak, arama hizmetinin arama sorgusunun tamamlanması için 21 MS sürdüğünü ve tüm istemci gidiş dönüş isteğinin 125 MS olduğunu görüyoruz. Bu iki sayıyı çıkararak, arama sorgusunun arama hizmetine iletilmesi ve arama sonuçlarının istemciye geri aktarılması için 104 MS tarafından daha fazla zaman sürdüğünü belirleyebiliriz.

Bu, Ağ gecikmeleri veya sorgu performansını etkileyen diğer faktörler olup olmadığını belirlemede son derece yararlı olabilir.

:::image type="content" source="media/search-performance/perf-elapsed-time.png" alt-text="Sorgu süresi ölçümleri" border="true":::

## <a name="query-rates"></a>Sorgu ücretleri

Arama hizmetinizin azalmasına yönelik olası bir neden, birimin saniye başına sorgu olarak (QPS) veya dakikada (QPM) sorgu olarak yakalanmasının gerçekleştirildiği sorgu sayısıdır. Arama hizmetiniz daha fazla QPS aldığından, genellikle azaltma 503 HTTP yanıtını geri gönderebileceği sürece bu sorgulara yanıt vermeye devam eder. 

Aşağıdaki kusto sorgusu, QPM 'de ölçülen sorgu birimini, milisaniye cinsinden (AvgDurationMS) bir sorgunun ortalama süresi ve her birinde döndürülen ortalama belge sayısı (Avgdoccountdöndürülen) ile birlikte gösterir.

```kusto
AzureDiagnostics
| where OperationName == "Query.Search" and TimeGenerated > ago(1d)
| extend MinuteOfDay = substring(TimeGenerated, 0, 16) 
| project MinuteOfDay, DurationMs, Documents_d, IndexName_s
| summarize QPM=count(), AvgDuractionMs=avg(DurationMs), AvgDocCountReturned=avg(Documents_d)  by MinuteOfDay
| order by MinuteOfDay desc 
| render timechart
```

:::image type="content" source="media/search-performance/perf-line-chart-queries-per-minute.png" alt-text="Dakika başına sorgu gösteren grafik" border="true":::

> [!TIP]
> Bu grafiğin arkasındaki verileri açığa çıkarmak için satırı kaldırın `| render timechart` ve sonra sorguyu yeniden çalıştırın.

## <a name="impact-of-indexing-on-queries"></a>Sorgularda dizin oluşturmanın etkisi

Performans ' a bakarken, dizin oluşturmanın arama sorguları ile aynı kaynakları kullandığını göz önünde bulundurmanız gereken önemli bir faktör. Büyük miktarda içeriği dizinliyorsanız, hizmet her iki iş yüküne uyum sağlamaya çalıştığından gecikme süresini büyütmeyi deneyebilirsiniz.

Sorgular yavaşlarsa, sorgu düşüşüne sahip olup olmadığını görmek için dizin oluşturma etkinliğinin zamanlamasına bakın. Örneğin, bir Dizin Oluşturucu, arama sorgularının azaltılmış performansından gelen günlük veya saatlik bir işi çalıştırıyor olabilir. 

Bu bölüm, arama ve dizin oluşturma oranlarını görselleştirmenize yardımcı olabilecek bir sorgu kümesi sağlar. Bu örnekler için, zaman aralığı sorguda ayarlanır. Sorguları Azure portal içinde çalıştırırken **sorguda ayarla** ' yı belirttiğinizden emin olun.

:::image type="content" source="media/search-performance/perf-set-query-time-range.png" alt-text="Sorgu aracında zaman aralıklarını ayarlama" border="true":::

<a name="average-query-latency"></a>

### <a name="average-query-latency"></a>Ortalama sorgu gecikmesi

Aşağıdaki sorguda, arama sorgularının ortalama gecikmesini göstermek için 1 dakikalık bir Aralık boyutu kullanılır. Grafik, ortalama gecikme süresinin 5:45pm ve 5:53pm 'e kadar düşük olduğunu görebiliriz.

```kusto
let intervalsize = 1m; 
let _startTime = datetime('2021-02-23 17:40');
let _endTime = datetime('2021-02-23 18:00');
AzureDiagnostics
| where TimeGenerated between(['_startTime']..['_endTime']) // Time range filtering
| summarize AverageQueryLatency = avgif(DurationMs, OperationName in ("Query.Search", "Query.Suggest", "Query.Lookup", "Query.Autocomplete"))
    by bin(TimeGenerated, intervalsize)
| render timechart
```

:::image type="content" source="media/search-performance/perf-line-chart-average-query-latency.png" alt-text="Ortalama sorgu gecikmesini gösteren grafik" border="true":::

### <a name="average-queries-per-minute-qpm"></a>Dakika başına ortalama sorgu (QPM)

Aşağıdaki sorgu, gecikmeyi etkileyebilecek arama isteklerinde bazı ani artış olmadığından emin olmak için dakika başına ortalama sorgu sayısına bakmamızı sağlar. Grafik içinden bir varyans olduğunu görebilir, ancak istek sayısında ani bir artış belirtebiliriz.

```kusto
let intervalsize = 1m; 
let _startTime = datetime('2021-02-23 17:40');
let _endTime = datetime('2021-02-23 18:00');
AzureDiagnostics
| where TimeGenerated between(['_startTime'] .. ['_endTime']) // Time range filtering
| summarize QueriesPerMinute=bin(countif(OperationName in ("Query.Search", "Query.Suggest", "Query.Lookup", "Query.Autocomplete"))/(intervalsize/1m), 0.01)
  by bin(TimeGenerated, intervalsize)
| render timechart
```

:::image type="content" source="media/search-performance/perf-line-chart-average-queries-per-minute.png" alt-text="Dakika başına ortalama sorgu gösteren grafik" border="true":::

### <a name="indexing-operations-per-minute-opm"></a>Dakika başına dizin oluşturma Işlemi (OPM)

Burada, dakika başına dizin oluşturma işlemlerinin sayısına bakacağız. Grafik, 5:42 pm tarihinde başlayan büyük miktarda verinin dizine alınacağını ve 5:50pm tarihinde sona ermiş olduğunu görebiliriz. Bu dizin oluşturma işlemi, arama sorgularının başlamasından önce 3 dakika önce ve arama sorguları artık kalmadan önce 3 dakika sona erdi.

Bundan sonra, arama hizmetinin dizin oluşturmanın başlamasını, arama sorgusunun gecikme süresini etkilemesinden yeterince meşgul olması için 3 dakika sürdüğünü görebilirsiniz. Ayrıca, dizin oluşturma tamamlandıktan sonra arama hizmeti, arama sorgusunun artık görünmez hale gelene kadar, yeni dizinli içerikten tüm işleri tamamlaması için bir 3 dakika daha gerçekleşmelidir.

```kusto
let intervalsize = 1m; 
let _startTime = datetime('2021-02-23 17:40');
let _endTime = datetime('2021-02-23 18:00');
AzureDiagnostics
| where TimeGenerated between(['_startTime'] .. ['_endTime']) // Time range filtering
| summarize IndexingOperationsPerSecond=bin(countif(OperationName == "Indexing.Index")/ (intervalsize/1m), 0.01)
  by bin(TimeGenerated, intervalsize)
| render timechart 
```

:::image type="content" source="media/search-performance/perf-line-chart-indexing-operations.png" alt-text="Dakika başına dizin oluşturma işlemlerini gösteren grafik" border="true":::

## <a name="background-service-processing"></a>Arka plan hizmeti işleme

Sorgu veya dizin oluşturma gecikmede düzenli ani artışları görmek olağan dışı değildir. Ani artışlar, dizin oluşturma veya yüksek sorgu hızlarına yanıt olarak gerçekleşebilir, ancak birleştirme işlemleri sırasında da gerçekleşebilir. Arama dizinleri parçalar veya parçalar halinde depolanır. Sistem düzenli aralıklarla, daha küçük parçaları büyük parçalar halinde birleştirir ve bu da hizmet performansını iyileştirmenize yardımcı olabilir. Bu birleştirme işlemi, daha önce dizinden silinmek üzere işaretlenmiş belgeleri temizler ve bu da depolama alanının kurtarılmasına neden olur. 

Parçaların birleştirilmesi hızlıdır, ancak kaynakların yoğun olması ve bu nedenle hizmet performansının düşmesine neden olabilir. Bu nedenle, sorgu gecikmesi için kısa artışlarıyla görürseniz ve bu artışlarıyla, Dizin oluşturulmuş içerikte yapılan son değişikliklerle başa anıyor olabilir. Bu, büyük olasılıkla birleştirme işlemlerine yönelik gecikme süresini sorgulayabilir. 

## <a name="next-steps"></a>Sonraki adımlar

Hizmet performansını çözümleme ile ilgili bu ek makaleleri gözden geçirin.

+ [Performans ipuçları](search-performance-tips.md)
+ [Bir hizmet katmanı seçin](search-sku-tier.md)
+ [Kapasiteyi yönetme](search-capacity-planning.md)
