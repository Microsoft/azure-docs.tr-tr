---
title: Azure Stream Analytics işlerinde ölçeği artırma ve genişletme
description: Bu makalede, giriş verilerini bölümleyerek, sorguyu ayarlayarak ve iş akışı birimlerinin ayarlanmasıyla bir Stream Analytics işinin nasıl ölçeklendiriyapılacağı açıklanır.
author: JSeb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/22/2017
ms.openlocfilehash: e3d4fd6b6b83681284278d10409a1c16394db31f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98018692"
---
# <a name="scale-an-azure-stream-analytics-job-to-increase-throughput"></a>Azure Stream Analytics işini, aktarım hızını artırmak için ölçeklendirin
Bu makalede, Akış Analizi işlerinin aktarım hızını artırmak için bir Stream Analytics sorgusunun nasıl ayarlanacağı gösterilmektedir. Daha yüksek yükü işleyecek ve daha fazla sistem kaynaklarından (daha fazla bant genişliği, daha fazla CPU kaynağı, daha fazla bellek gibi) yararlanmak üzere işinizi ölçeklendirmek için aşağıdaki kılavuzu kullanabilirsiniz.
Bir önkoşul olarak, aşağıdaki makaleleri okumanız gerekebilir:
-   [Akış Birimlerini anlama ve ayarlama](stream-analytics-streaming-unit-consumption.md)
-   [Paralelleştirilebilir işler oluşturma](stream-analytics-parallelization.md)

## <a name="case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions"></a>Durum 1 – sorgunuz doğal olarak giriş bölümleri genelinde tamamen paralelleştirilebilir
Sorgunuz doğal olarak giriş bölümlerine tam olarak paralelleştirmemişse aşağıdaki adımları izleyebilirsiniz:
1.  Anahtar sözcüğe **göre Bölüm** kullanarak sorgunuzu beyana yazın. [Bu sayfanın Embarson](stream-analytics-parallelization.md)'un paralel işleri bölümünde daha fazla ayrıntı görüntüleyin.
2.  Sorgunuzda kullanılan çıkış türlerine bağlı olarak, bazı çıktılar paralelleştirilebilir veya daha fazla yapılandırmanın emson paralel olması gerekebilir. Örneğin, PowerBI çıkışı paralelleştirilebilir. Çıktılar, çıkış havuzuna gönderilmeden önce her zaman birleştirilir. Blob 'lar, tablolar, ADLS, Service Bus ve Azure Işlevi otomatik olarak paraleldir. SQL ve Azure SYNAPSE Analytics çıkışları paralelleştirme için bir seçeneğe sahiptir. Olay Hub 'ının PartitionKey yapılandırması, **bölümleme ölçütü** alanı (genellikle PartitionID) ile eşleşecek şekilde ayarlanmalıdır. Olay Hub 'ı için Ayrıca, bölümler arasında çapraz geçiş yapmaktan kaçınmak üzere tüm girişler ve tüm çıktılar için bölüm sayısıyla eşleşen ek dikkat ödeyin. 
3.  En yüksek ulaşılabilir üretilen iş miktarını ölçmek için sorgunuzu **6 su** (tek bir bilgi işlem düğümünün tam kapasitesi olan) ile çalıştırın ve **gruplama ölçütü** kullanıyorsanız, işin işleyebileceği grup sayısını (kardinalite) ölçebilir. İş, sistem kaynağı sınırlarına vurarak yapılacak işin genel belirtileri aşağıda verilmiştir.
    - % SU kullanım ölçümü %80 üzerinde. Bu, bellek kullanımının yüksek olduğunu gösterir. Bu ölçümün artışına katkıda bulunan faktörler [burada](stream-analytics-streaming-unit-consumption.md)açıklanmıştır. 
    -   Çıkış zaman damgası, duvar saati zamanına göre geride olur. Sorgu mantığınıza bağlı olarak, çıkış zaman damgası, duvar saati saatinden bir mantık uzaklığa sahip olabilir. Ancak, kabaca aynı hızda ilerlemelidir. Çıktı zaman damgası daha fazla ve daha sonra geri düşolursa sistemin fazla çalıştığını gösteren bir göstergedir. Bu, akış çıkış havuzunun azalmasını veya yüksek CPU kullanımının bir sonucu olabilir. Şu anda CPU kullanım ölçümü sağlamadık, bu yüzden ikisini ayırt etmek zor olabilir.
        - Sorun havuz azaltmasından kaynaklanıyorsa, çıkış bölümlerinin sayısını artırmanız (ve ayrıca işi tamamen paralelleştirilebilir) veya havuzun kaynak miktarını (örneğin, CosmosDB için Istek birimi sayısı) artırmanız gerekebilir.
    - İş Diyagramı ' nda her giriş için bölüm başına biriktirme listesi olay ölçümü vardır. Biriktirme listesi olay ölçümü artmaya devam ederse, sistem kaynağının kısıtlandığı bir göstergedir (çıkış havuzu azaltma veya yüksek CPU nedeniyle).
4.  6 SU işinin ne kadar hızlı bir şekilde ulaşabileceği hakkında daha fazla bilgi sahibi olduktan sonra, daha fazla SUs eklediğinizde işin işlem kapasitesini tahmin edebilir, ancak belirli bir bölümün "etkin" olmasını sağlayan herhangi bir veri eğimini olmadığı varsayılır.

> [!NOTE]
> Akış birimlerinin doğru sayısını seçin: Stream Analytics her 6 SU için bir işleme düğümü oluşturduğundan, düğümlerin sayısını giriş bölümlerinin sayısının böleni olarak oluşturmak en iyisidir, böylece bölümler düğümler arasında eşit olarak dağıtılabilir.
> Örneğin, 6 SU işinizin 4 MB/sn işleme hızına ulaşabilmesini ve giriş bölümü sayınızı 4 ' ün üzerinde olduğunu ölçtüğünüz. İşinizi yaklaşık 8 MB/sn işlem hızına veya 16 MB/s elde etmek için 24 SU elde etmek üzere 12 SU ile çalıştırmayı tercih edebilirsiniz. Daha sonra, giriş hızlarınızın bir işlevi olarak iş için SU sayısının ne zaman artacağına karar verebilirsiniz.


## <a name="case-2---if-your-query-is-not-embarrassingly-parallel"></a>Durum 2-sorgunuz emsanki paralel değilse.
Sorgunuz emsönce paralel değilse aşağıdaki adımları izleyebilirsiniz.
1.  Bölümleme karmaşıklığına kaçınmak için öncelikle **bölüm** içermeyen bir sorgu ile başlayın ve büyük [/küçük harf 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions)' de en fazla yükü ölçmek için sorgunuzu 6 su ile çalıştırın.
2.  İşlem sırasında tahmini yükün elde edilebilmesi için işiniz bitti demektir. Alternatif olarak, senaryolarınız için çalışan minimum SU sayısını öğrenmek üzere 3 SU ve 1 SU ile aynı işi ölçmeyi tercih edebilirsiniz.
3.  İstenen aktarım hızını elde ediyorsanız, mümkünse sorgunuzu birden çok adıma kesmeyi deneyin, zaten birden çok adım yoksa ve sorgudaki her adım için 6 SU ayırın. Örneğin 3 adım varsa, "ölçek" seçeneğinde 18 SU ayırın.
4.  Böyle bir işi çalıştırırken Stream Analytics, her adımı adanmış 6 SU kaynaklarıyla kendi düğümüne koyar. 
5.  Yükleme hedefini hala almadıysanız, girişe daha yakın adımlardan başlayarak **bölüm** kullanmayı deneyebilirsiniz. Doğal olarak bölümlenebilir **Grup ölçütü** operatörü için, yerel/genel toplama modelini kullanarak bölümlenmiş bir **grubu** , sonra bölümlenmemiş olmayan bir **Grup** tarafından gerçekleştirebilirsiniz. Örneğin, her 5 dakikada bir, her biri için bir kaç araba olduğunu saymak istiyorsanız ve verilerin hacmi 6 SU tarafından işlenebilecekleri kadar fazla.

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
Yukarıdaki sorguda, bölüm başına her bir stand için otomobilleri saymanız ve sonra sayıyı tüm bölümlerden birlikte eklemeniz gerekir.

Bölümlenmiş olduktan sonra, adımın her bölümü için 6 SU ayırın. her bölüm, en fazla 6 SU olacak şekilde, her bölüm kendi işleme düğümüne yerleştirilebilir.

> [!Note]
> Sorgunuz bölümlenemez, çok adımlardan oluşan bir sorguda ek SU eklemek, her zaman üretimi iyileştiremeyebilir. Performansı elde etmenin bir yolu, yukarıdaki 5. adımda açıklandığı gibi yerel/genel toplama modelini kullanarak ilk adımlarda hacmi azaltmaktır.

## <a name="case-3---you-are-running-lots-of-independent-queries-in-a-job"></a>Durum 3-bir işte çok sayıda bağımsız sorgu çalıştırıyorsunuz.
Her kiracı için ayrı girişler ve çıktılar kullanarak tek bir iş içindeki birden fazla kiracıdan verileri işlemek için daha uygun maliyetli olan bazı ISV kullanım durumları için, tek bir işte çok az sayıda (örneğin, 20) bağımsız sorgu çalıştırmayı seçebilirsiniz. Varsayım her bir alt sorgunun yükü nispeten küçüktür. Bu durumda, aşağıdaki adımları izleyebilirsiniz.
1.  Bu durumda, sorgu içinde **bölüm** kullanmayın
2.  Olay Hub 'ı kullanıyorsanız, giriş bölümünün sayısını en düşük olası 2 değerine küçültün.
3.  Sorguyu 6 SU ile çalıştırın. Her bir alt sorgu için beklenen yük ile, iş sistem kaynağı sınırlarına ulaşana kadar mümkün olduğunca çok sayıda alt sorgu ekleyin. Bu gerçekleştiğinde belirtiler için [durum 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions) ' e bakın.
4.  Yukarıda ölçülen alt sorgu sınırına vurduktan sonra, alt sorguyu yeni bir işe eklemeye başlayın. Bağımsız sorgu sayısının bir işlevi olarak çalıştırılacak iş sayısı oldukça doğrusal olmalıdır, ancak herhangi bir yük eğiniz olmadığı varsayılır. Daha sonra, çalıştırmak istediğiniz kiracı sayısı işlevi olarak kaç 6 SU işi çalıştırmanız gerektiğini tahmin edebilirsiniz.
5.  Bu tür sorgularla başvuru verileri birleştirme kullanılırken, aynı başvuru verileriyle katılmadan önce girişleri birlikte toplayın. Ardından, gerekirse olayları ayırın. Aksi halde, her başvuru verisi birleştirme, bellek kullanımını gereksiz bir şekilde belleğe alan başvuru verilerinin bir kopyasını tutar.

> [!Note] 
> Her işinde kaç kiracı yerleştirilecek?
> Bu sorgu deseninin genellikle çok sayıda alt sorgusu vardır ve çok büyük ve karmaşık topolojiye neden olur. İşin denetleyicisi bu tür bir büyük topolojiyi işleyemeyebilir. Thumb kuralı olarak 1 SU işi için 40 kiracılar ve 3 SU ve 6 SU işi için 60 kiracılar ' ın altında kalır. Denetleyicinin kapasitesini aşdığınızda, iş başarıyla başlatılmaz.



## <a name="get-help"></a>Yardım alın
Daha fazla yardım için, [Azure Stream Analytics Için Microsoft Q&soru sayfasını](/answers/topics/azure-stream-analytics.html)deneyin.

## <a name="next-steps"></a>Sonraki adımlar
* [Azure Stream Analytics giriş](stream-analytics-introduction.md)
* [Azure Akış Analizi'ni kullanmaya başlama](stream-analytics-real-time-fraud-detection.md)
* [Azure Akış Analizi Sorgu Dili Başvurusu](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Akış Analizi Yönetimi REST API'si Başvurusu](/rest/api/streamanalytics/)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

<!--Link references-->

[microsoft.support]: https://support.microsoft.com
[azure.event.hubs.developer.guide]: /previous-versions/azure/dn789972(v=azure.100)

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/