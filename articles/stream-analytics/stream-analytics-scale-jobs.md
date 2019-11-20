---
title: Azure Stream Analytics işlerinde ve ölçeklendirme
description: Bu makalede, Stream Analytics işi giriş verileri bölümleme sorguyu ayarlamayı ve ayarlama iş akış birimleri ölçeklendirme açıklar.
services: stream-analytics
author: JSeb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/22/2017
ms.openlocfilehash: fe4d37563af159f566bc3fb03a3cfe136e7cb734
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621722"
---
# <a name="scale-an-azure-stream-analytics-job-to-increase-throughput"></a>Azure Stream Analytics işi verimliliğini artırmak için ölçeklendirme
Bu makalede, Streaming Analytics işlerini verimliliğini artırmak için bir Stream Analytics sorgu nasıl ayarlanacağını gösterir. İşinizi daha yüksek bir yükü işlemek ve daha fazla sistem kaynakları (örneğin, daha fazla bant genişliği, daha fazla CPU kaynaklarının, daha fazla bellek) yararlanmak için ölçeklendirmek için aşağıdaki kılavuzu kullanabilirsiniz.
Bir önkoşul olarak bu makaleleri okuyun gerekebilir:
-   [Akış Birimlerini anlama ve ayarlama](stream-analytics-streaming-unit-consumption.md)
-   [Paralelleştirilebilir işleri oluşturma](stream-analytics-parallelization.md)

## <a name="case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions"></a>1 – sorgunuzu giriş bölümler arasında tam olarak kendiliğinden paralelleştirilebilir olduğu
Sorgunuzu giriş bölümler arasında tam olarak kendiliğinden paralelleştirilebilir ise, aşağıdaki adımları izleyebilirsiniz:
1.  Utandırıcı derecede paralel olarak kullanarak sorgunuzu yazma **PARTITION BY** anahtar sözcüğü. Daha fazla bilgi utandırıcı derecede Paralel işler bölümünde [bu sayfadaki](stream-analytics-parallelization.md).
2.  Sorguda kullanılan çıkış türlerine bağlı olarak, bazı ya da olmayabilir paralelleştirilebilir, çıkış veya daha fazla utandırıcı derecede paralel olarak yapılandırması gerekir. Örneğin, SQL ve SQL DW Powerbı çıkışları paralelleştirilebilir değildir. Çıktı, çıkış havuzuna göndermeden önce her zaman birleştirilir. Bloblar, tablolar, ADLS, Service Bus ve Azure işlevi otomatik olarak paralelleştirildi. CosmosDB ve olay hub'ı PartitionKey yapılandırması ile eşleşecek şekilde ayarlanmış olması gerekiyor **PARTITION BY** alan (genellikle PartitionID). Olay hub'ı için aynı zamanda tüm girişleri ve bölümler arasında çapraz üzerinden önlemek için tüm çıktılar için bölüm sayısı eşleşecek şekilde ek dikkat edin. 
3.  Sorgunuzu ile çalıştırmak **6 SU** (tek bir bilgi işlem düğümü tam kapasitesini olmayan) en fazla ulaşılabilir aktarım hızını ölçmek için ve kullanıyorsanız **GROUP BY**, kaç grupları (kardinalite) iş için ölçün tanıtıcı. Sistem kaynak sınırlarını ulaşma iş genel belirtileri aşağıda verilmiştir.
    - SU % utilization ölçümünü üzerinde % 80'idir. Bu, bellek kullanımı yüksek olduğunu gösterir. Bu ölçüm bir artış için katkıda bulunan faktörleri açıklanan [burada](stream-analytics-streaming-unit-consumption.md). 
    -   Çıkış zaman damgası duvar saati süresi açısından dönülüyor. Sorgu mantığınızı bağlı olarak çıkış zaman damgası duvar saati süresi mantıksal uzaklığı olabilir. Ancak, yaklaşık aynı fiyattan ilerleme. Çıkış zaman damgası başka ve bunun arkasında geride kalıyor sistem overworking bir gösterge yoktur. Bu, aşağı akış çıkış havuzu kısıtlama veya yüksek CPU kullanımı bir sonucu olabilir. İki ayırt etmek zor olabilir, böylece CPU kullanım ölçümü şu anda sunmaz.
        - Havuz kapasitesi azaltıldığı sorunu mevcutsa, çıkış bölüm sayısını artırmak (ve aynı zamanda işin tam olarak paralelleştirilebilir tutmak bölümler giriş), gerekebilir veya kaynak, havuz (örneğin CosmosDB için istek birimi sayısı) miktarını artırın.
    - İş diyagramında var. bir bölüm biriktirme listesi olay ölçüm her bir giriş için. Biriktirme listesi olay ölçümü artmaya devam ederse, sistem kaynağı (veya çıkış havuzu kısıtlama veya yüksek CPU nedeniyle) kısıtlı bir göstergesi de olabilir.
4.  6 SU iş ulaşıp ulaşamadığını sınırları belirledikten sonra belirli bölüm "sıcak" yapan, herhangi bir veri dengesizliği yoksa varsayılarak daha fazla SUs ekledikçe, doğrusal olarak işin işlem kapasitesini tahmin

> [!NOTE]
> Doğru sayıda akış birimi seçin: Stream Analytics, bir işlem düğümü, eklenen her 6 SU oluşturduğundan, bölümleri düğümler arasında eşit olarak dağıtılabilir bir bölen giriş bölüm sayısı, düğüm sayısını olmak en iyisidir.
> Örneğin, 6 ölçülen SU iş elde edebileceğiniz 4 MB/s işleme hızı ve, giriş bölüm sayısı olan 4. Yaklaşık 8 MB/sn işleme hızını elde etmek için 12 SU veya 16 MB/sn elde etmek için 24 SU işinizi çalıştırmayı seçebilirsiniz. Ardından işin hangi değere giriş oranınız işlevi olarak SU sayıyı artırmak ne zaman karar verebilirsiniz.


## <a name="case-2---if-your-query-is-not-embarrassingly-parallel"></a>2 - sorgunuzu utandırıcı derecede paralel değilse durumu.
Sorgunuzu utandırıcı derecede paralel değilse, aşağıdaki adımları izleyebilirsiniz.
1.  Başlangıç içermeyen bir sorgu ile **PARTITION BY** ilk karmaşıklığı bölümlenmesini önlemeye ve en fazla yükü olarak ölçmek için 6 SU ile sorguyu çalıştırmak için [vaka 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions).
2.  Beklenen yük, üretilen iş terimini elde edebileceğiniz ise gerçekleştirilir. Alternatif olarak, ölçü 3 SU ve 1 SU, en az senaryonuz için uygun SU sayısını öğrenmek için çalışan aynı iş tercih edebilirsiniz.
3.  İstenen aktarım hızı elde etmek, olmayan birden çok adım zaten varsa ve her adımında sorgu en fazla 6 SU ayırma sorgunuzu Mümkünse, birden çok adımı ayırmak deneyin. Örneğin "Ölçeklendir" seçeneğini 18 SU ayırma 3 adımda varsa.
4.  Böyle bir iş çalışırken, Stream Analytics her adım adanmış 6 SU kaynaklarla kendi düğüme yerleştirir. 
5.  Hala yük hedef elde yapmadıysanız, kullanmayı deneyebilir **PARTITION BY** girişi yakın adımlardan başlatılıyor. İçin **GROUP BY** doğal olarak bölümlenebilir olmayabilir işleci, bir bölümlenmiş gerçekleştirmek için yerel/genel toplama düzeni kullanabilirsiniz **GROUP BY** bir bölümlenmemiş tarafından izlenen **Gruplandır** . Saymak istiyorsanız, örneğin, her Ücretli standına 3 dakikada bir ve veri hacmi giderek kaç otomobiller beklenenin ötesine olduğu 6 SU tarafından işlenebilir.

Sorgu:

 ```SQL
 WITH Step1 AS (
 SELECT COUNT(*) AS Count, TollBoothId, PartitionId
 FROM Input1 Partition By PartitionId
 GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
 )
 SELECT SUM(Count) AS Count, TollBoothId
 FROM Step1
 GROUP BY TumblingWindow(minute, 3), TollBoothId
 ```
Yukarıdaki sorguda, bölüm başına Ücretli standına başına otomobiller sayım ve sayı tüm bölümleri birbirine ekleme.

En fazla 6 SU, 6 sahip her bölüm, adımın her bölüm için bölümlenmiş sonra tahsis SU her bölüm kendi işlem düğümünde yerleştirilebilir en olduğundan.

> [!Note]
> Sorgunuzu bölümlenemez, ek SU çok adımları sorguda ekleme her zaman aktarım hızı artırabilir değil. Performans elde etmek için bir yol yukarıda 5. adımda açıklandığı gibi yerel/genel toplama desenini kullanarak ilk adımlarında birimde azaltmaktır.

## <a name="case-3---you-are-running-lots-of-independent-queries-in-a-job"></a>Durum 3 - bağımsız sorgular çok sayıda bir işi çalışıyor.
Belirli bir ISV kullanmak için olduğu daha tek bir işlemde birden çok kiracıdan gelen veriyi işlemek için ekonomik, servis talepleri, her bir kiracı için ayrı giriş ve çıkışları kullanarak, (örneğin 20) oldukça az sayıda çalışan sonlandırabiliriz tek bir İşte bağımsız sorgular. Her tür alt sorgu ait yük göreceli olarak küçüktür varsayılır. Bu durumda, aşağıdaki adımları izleyebilirsiniz.
1.  Bu durumda, kullanmayın **PARTITION BY** sorgu
2.  Olay hub'ı kullanıyorsanız giriş bölüm sayısı 2 olası en düşük değerini azaltın.
3.  Sorgu 6 SU ile çalıştırın. İş, sistem kaynak sınırlarını ulaşma kadar her alt sorgu için beklenen yükü ile mümkün olduğu kadar alt ekleyin. Başvurmak [vaka 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions) için böyle bir durumda belirtileri.
4.  Yukarıda ölçülen alt sınırı karşılaştınız demektir sonra alt sorgu yeni bir projeye ekleme başlatın. İşlev bağımsız sorgularının sayısı olarak çalıştırılacak işlerin sayısı eğme herhangi bir yüke sahip değilseniz varsayılarak oldukça doğrusal olmalıdır. 6 kaç SU işleri kiracılara hizmet vermek için istediğiniz sayıda bir işlev çalıştırmak için gereken tahmini.
5.  Başvuru veri JOIN sorgularını ile kullanırken girişleri birlikte aynı ile birleştirilmeden önce başvuru verileri birleşim. Ardından, gerekirse olaylarını bölün. Aksi takdirde, her başvuru veri birleştirme, büyük olasılıkla bellek kullanımını serbest gereksiz yere blowing bellekte başvuru verilerinin bir kopyasını tutar.

> [!Note] 
> Her bir iş koymak için kaç adet kiracıyı?
> Bu sorgu deseni genellikle çok sayıda alt sorgular sahiptir ve çok büyük ve karmaşık topolojisinde sonuçlanır. Denetleyici işin büyük bir topoloji işlemek mümkün olmayabilir. Bir kural karşısında, altında 40 kiracıları 1 SU işi için kalın ve SU 3 ve 6 SU işleri için 60 Kiracı. Denetleyici kapasitesi aşıldığında işi başarıyla başlatılmaz.



## <a name="get-help"></a>Yardım alın
Daha fazla yardım için deneyin bizim [Azure Stream Analytics forumumuzu](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Sonraki adımlar
* [Azure Stream analytics'e giriş](stream-analytics-introduction.md)
* [Azure Akış Analizi'ni kullanmaya başlama](stream-analytics-real-time-fraud-detection.md)
* [Azure Akış Analizi Sorgu Dili Başvurusu](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Akış Analizi Yönetimi REST API'si Başvurusu](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

<!--Link references-->

[microsoft.support]: https://support.microsoft.com
[azure.event.hubs.developer.guide]: https://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301

