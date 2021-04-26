---
title: Azure Stream Analytics içindeki ölçek Machine Learning Studio (klasik) işlevleri
description: Bu makalede, bölümleme ve akış birimlerini yapılandırarak Machine Learning Studio (klasik) işlevlerini kullanan Stream Analytics işlerin nasıl ölçeklendiriyapılacağı açıklanır.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/15/2021
ms.openlocfilehash: 1ee1411aba7724d76ed8626de9b8b038d02339dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103574263"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-classic-functions"></a>Stream Analytics işinizi Azure Machine Learning Studio (klasik) işlevlerle ölçeklendirin

> [!TIP]
> Daha iyi performans ve güvenilirlik için Azure Machine Learning Studio (klasik) UDF yerine [Azure Machine Learning UDF 'leri](machine-learning-udf.md) kullanmanız önemle önerilir.

Bu makalede Azure Machine Learning Studio (klasik) işlevleri kullanan Azure Stream Analytics işlerin verimli bir şekilde ölçeklendirilmesi açıklanmaktadır. Genel olarak Stream Analytics işlerinin ölçeklendirilmesi hakkında daha fazla bilgi için bkz. [Ölçek işleri](stream-analytics-scale-jobs.md).

## <a name="what-is-a-studio-classic-function-in-stream-analytics"></a>Stream Analytics Studio (klasik) işlevi nedir?

Stream Analytics bir Machine Learning Studio (klasik) işlevi Stream Analytics sorgu dilinde normal işlev çağrısı gibi kullanılabilir. Ancak, arka planda bu işlev çağrıları aslında Studio (klasik) Web hizmeti isteklerdir.

Studio (klasik) Web hizmeti isteklerinin performansını "toplu olarak" birden çok satırı aynı Web hizmeti API çağrısıyla birlikte artırabilirsiniz. Bu gruplandırma bir mini toplu iş olarak adlandırılır. Daha fazla bilgi için bkz. [Azure Machine Learning Studio (klasik) Web Hizmetleri](../machine-learning/classic/consume-web-services.md). Stream Analytics 'de Studio (klasik) için destek.

## <a name="configure-a-stream-analytics-job-with-studio-classic-functions"></a>Studio (klasik) işlevleriyle Stream Analytics işi yapılandırma

Stream Analytics işiniz tarafından kullanılan Studio (klasik) işlevini yapılandırmak için iki parametre vardır:

* Studio (klasik) işlev çağrılarının toplu iş boyutu.
* Stream Analytics işi için sağlanan akış birimlerinin (SUs) sayısı.

SUs için uygun değerleri belirlemek için Stream Analytics işin gecikmesini veya her SU verimini iyileştirmek isteyip istemediğinize karar verin. İyi bölümlenen bir Stream Analytics sorgusunun aktarım hızını artırmak için, SUs her zaman bir işe eklenebilir. Ek SUs işi çalıştırma maliyetini artırır.

Stream Analytics işiniz için gecikme süresi *toleransını* belirleme. Toplu iş boyutunu artırmak, Studio (klasik) isteklerinizin gecikmesini ve Stream Analytics işinin gecikme süresini artırır.

Toplu iş boyutunu artırmak, Stream Analytics işinin **aynı sayıda** Studio (klasik) Web hizmeti isteği ile **daha fazla olay** işlemesini sağlar. Studio (klasik) Web hizmeti gecikmesi, genellikle toplu iş boyutu artışına göre daha doğrusal bir yoldur. 

Belirli bir durumda bir Studio (klasik) Web hizmeti için en uygun maliyetli toplu iş boyutunu göz önünde bulundurmanız önemlidir. Web hizmeti istekleri için varsayılan toplu iş boyutu 1000 ' dir. Bu varsayılan boyutu, Stream Analytics için [Stream Analytics REST API](/previous-versions/azure/mt653706(v=azure.100) "Akış Analizi REST API") veya [PowerShell istemcisini](stream-analytics-monitor-and-manage-jobs-use-powershell.md)kullanarak değiştirebilirsiniz.

Bir toplu iş boyutuna karar verdikten sonra, işlevin saniyede işlemesi gereken olay sayısına göre akış birimlerinin (SUs) sayısını ayarlayabilirsiniz. Akış birimleri hakkında daha fazla bilgi için bkz. [Stream Analytics ölçek işleri](stream-analytics-scale-jobs.md).

Her 6 SUs, Studio (klasik) Web hizmeti ile 20 eşzamanlı bağlantı alır. Ancak, 1 SU işi ve 3 SU işi 20 eşzamanlı bağlantı alır.  

Uygulamanız saniyede 200.000 olay oluşturursa ve toplu iş boyutu 1000 ise, sonuçta elde edilen Web hizmeti gecikmesi 200 MS 'dir. Bu hız, her bağlantının her saniye Studio (klasik) Web hizmetinde beş istek yapabileceği anlamına gelir. Stream Analytics işi, 20 bağlantıyla birlikte 200 MS ve 100.000 olaylarında 20.000 olayı saniye içinde işleyebilir.

Saniyede 200.000 olay işlemek için, Stream Analytics işi 12 ' ye kadar olan 40 eşzamanlı bağlantı gerektirir. Aşağıdaki diyagramda Stream Analytics işinden Studio (klasik) Web hizmeti uç noktasına yapılan istekler gösterilmektedir. her 6 SUs, en fazla Studio (klasik) Web hizmeti için en fazla 20 eşzamanlı bağlantı sağlar.

![Studio (klasik) Işlevleri ile Stream Analytics ölçeklendirme iki iş örneği](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Studio (klasik) Işlevleri ile Stream Analytics ölçeklendirme iki iş örneği")

Genel olarak, toplu iş boyutu için ***b** _, toplu Iş boyutu b 'de Web hizmeti gecikmesi için _*_L_*_ , _ *_N_** SUs ile bir Stream Analytics işinin aktarım hızı:

![Studio (klasik) Işlevleri formülüyle Stream Analytics ölçeklendirme](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Studio (klasik) Işlevleri formülüyle Stream Analytics ölçeklendirme")

Ayrıca, Studio (klasik) Web hizmetinde ' maksimum eşzamanlı çağrılar ' yapılandırabilirsiniz. Bu parametreyi en büyük değere ayarlamanız önerilir (Şu anda 200).

Bu ayar hakkında daha fazla bilgi için, [Machine Learning Studio (klasik) Web Hizmetleri Için ölçeklendirme makalesini](../machine-learning/classic/create-endpoint.md)inceleyin.

## <a name="example--sentiment-analysis"></a>Örnek: Yaklaşım Analizi
Aşağıdaki örnek, [Stream Analytics Machine Learning Studio (klasik) tümleştirme öğreticisinde](stream-analytics-machine-learning-integration-tutorial.md)açıklandığı gibi, yaklaşım Analizi Studio (klasik) işleviyle bir Stream Analytics işi içerir.

Sorgu, aşağıdaki örnekte gösterildiği **gibi, sonra yaklaşım işlevinin gösterdiği** basit bir tamamen bölümlenmiş sorgudur:

```SQL
    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery
```

Bir Stream Analytics işi oluşturmak için gereken yapılandırmayı incelemektir. Bu, saniye başına 10.000/sn cinsinden en fazla fiyata izin veren bir ücret analizi yapar.

1 SU kullanılarak bu Stream Analytics iş trafiği işleyebilir mi? İş, 1000 varsayılan toplu iş boyutunu kullanarak giriş ile devam edebilir. Yaklaşım Analizi Studio (klasik) Web hizmeti 'nin (varsayılan toplu iş boyutu 1000) varsayılan gecikmesi, bir saniyeden daha fazla gecikme süresi oluşturur.

Stream Analytics işin **genel** veya uçtan uca gecikmesi genellikle birkaç saniye sürer. *Özellikle de* Studio (klasik) işlevi çağrılarındaki bu Stream Analytics işe daha ayrıntılı bir bakış alın. Toplu iş boyutu 1000 ile, 10.000 olaylarının bir verimi Web hizmetinde 10 istek alır. Tek bir SU ile, bu giriş trafiğini barındırmak için yeterli eşzamanlı bağlantı vardır.

Giriş olayı hızı, 100x tarafından artıyorsa, Stream Analytics işin saniye başına 1.000.000 kata kadar işlemesi gerekir. Artan ölçeği gerçekleştirmenin iki seçeneği vardır:

1. Toplu iş boyutunu artırın.
2. Olayları paralel olarak işlemek için giriş akışını bölümleyin.

İlk seçenekle iş **gecikmesi** artar.

İkinci seçenekle, daha fazla eşzamanlı Studio (klasik) Web hizmeti isteğine sahip olmak için daha fazla SUs sağlamanız gerekir. Bu daha fazla sayıda SUs, iş **maliyetini** artırır.

Her toplu iş boyutu için aşağıdaki gecikme ölçümlerini kullanarak ölçeklendirmeyi inceleyelim:

| Gecikme süresi | Toplu iş boyutu |
| --- | --- |
| 200 MS | 1000-olay toplu işleri veya altı |
| 250 MS | 5.000-olay toplu işleri |
| 300 MS | 10.000-olay toplu işleri |
| 500 ms | 25.000-olay toplu işleri |

1. İlk seçenek (daha fazla SUs **sağlamama** ) kullanılıyor. Toplu iş boyutu **25.000** olarak artırılabilir. Toplu iş boyutunu bu şekilde artırmak, işin 1.000.000 olaylarını Studio (klasik) Web hizmeti ile 20 eşzamanlı bağlantıyla işleme (çağrı başına 500 ms gecikme süresi ile) sağlar. Bu nedenle, işle (klasik) Web hizmeti isteklerine yönelik yaklaşım işlevi istekleri nedeniyle Stream Analytics işinin ek gecikmesi **200 MS** 'den **500 MS**'ye artırılabilir. Ancak, Studio (klasik) Web Hizmetleri bir isteğin yük boyutunun 4 MB veya daha küçük olmasını gerektirdiğinden ve 100 saniyelik işlemden sonra Web hizmeti isteklerinin zaman aşımına uğramasıyla, toplu **iş boyutu sonsuz bir şekilde** artmıştır.
1. İkinci seçenek olarak, toplu iş boyutu 1000 ' de kalır, 200-MS Web hizmeti gecikme süresiyle, Web hizmetine yönelik her 20 eş zamanlı bağlantı, dakikada * 20 * 5 olay = 100.000 ' i 1000 işleyebilir. Bu nedenle, saniye başına 1.000.000 olay işlemek için, işin 60 SUs olması gerekir. İlk seçenekle karşılaştırıldığında Stream Analytics iş daha fazla Web hizmeti toplu istek oluşturacak ve bu da daha yüksek maliyetli bir maliyet oluşturuyor.

Aşağıda, farklı SUs ve Batch boyutları (saniye başına olay sayısı) için Stream Analytics işi verimlilik için bir tablo verilmiştir.

| toplu iş boyutu (ML gecikmesi) | 500 (200 ms) | 1.000 (200 ms) | 5.000 (250 MS) | 10.000 (300 ms) | 25.000 (500 MS) |
| --- | --- | --- | --- | --- | --- |
| **1 SU** |2,500 |5.000 |20.000 |30.000 |50,000 |
| **3 SUs** |2,500 |5.000 |20.000 |30.000 |50,000 |
| **6 SUs** |2,500 |5.000 |20.000 |30.000 |50,000 |
| **12 SUs** |5.000 |10,000 |40,000 |60.000 |100.000 |
| **18 SUs** |7.500 |15.000 |60.000 |90,000 |150.000 |
| **24 SUs** |10,000 |20.000 |80,000 |120.000 |200,000 |
| **…** |… |… |… |… |… |
| **60 SUs** |25,000 |50,000 |200,000 |300.000 |500.000 |

Şu anda, Studio (klasik) işlevlerinin Stream Analytics nasıl çalıştığını daha iyi anlayabilirsiniz. Ayrıca, veri kaynaklarından alınan Stream Analytics işlerin "çekme" verilerini ve her "çekme" Stream Analytics işinin işlemesi için bir olay toplu işi döndürdüğünü de anlamış olabilirsiniz. Bu çekme modeli, Studio (klasik) Web hizmeti isteklerini nasıl etkiler?

Normalde, Studio (klasik) işlevleri için belirlediğimiz toplu iş boyutu, her bir Stream Analytics işi "pull" tarafından döndürülen olay sayısına göre bölünemez. Bu gerçekleştiğinde, Studio (klasik) Web hizmeti "kısmi" toplu işlerle çağırılır. Kısmi toplu işlerin kullanılması, olayları çekme işleminden çekmeye kadar birleştirme sırasında ek iş gecikmesi yükünü ortadan kaldırır.

## <a name="new-function-related-monitoring-metrics"></a>İşlevle ilgili yeni izleme ölçümleri
Stream Analytics işinin Izleyici alanında, işlevle ilgili üç ek ölçüm eklenmiştir. Bunlar, aşağıdaki grafikte gösterildiği gibi **Işlev isteklerdir**, **Işlev OLAYLARı** ve **başarısız işlev isteklerdir**.

![Studio (klasik) Işlevleri ölçümleriyle Stream Analytics ölçeklendirme](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Studio (klasik) Işlevleri ölçümleriyle Stream Analytics ölçeklendirme")

, Aşağıdaki gibi tanımlanır:

**Işlev istekleri**: işlev isteklerinin sayısı.

**Işlev olayları**: işlev isteklerindeki olay sayısı.

**Başarısız Işlev istekleri**: başarısız işlev isteklerinin sayısı.

## <a name="key-takeaways"></a>Anahtar koymalar

Stream Analytics işini Studio (klasik) işlevleriyle ölçeklendirmek için aşağıdaki faktörleri göz önünde bulundurun:

1. Giriş olayı oranı.
2. Çalışan Stream Analytics işi için toleranslı gecikme süresi (ve bu nedenle Studio (klasik) Web hizmeti isteklerinin toplu iş boyutu).
3. Sağlanan Stream Analytics SUs ve Studio (klasik) Web hizmeti isteklerinin sayısı (işlevle ilgili ek maliyetler).

Örnek olarak, tamamen bölümlenmiş bir Stream Analytics sorgusu kullanılmıştır. Daha karmaşık bir sorgu gerekliyse, [Azure Stream Analytics Için Microsoft Q&soru sayfası](/answers/topics/azure-stream-analytics.html) Stream Analytics takımdan ek yardım almak için harika bir kaynaktır.

## <a name="next-steps"></a>Sonraki adımlar
Stream Analytics hakkında daha fazla bilgi edinmek için bkz.:

* [Azure Akış Analizi'ni kullanmaya başlama](stream-analytics-real-time-fraud-detection.md)
* [Azure Akış Analizi işlerini ölçeklendirme](stream-analytics-scale-jobs.md)
* [Azure Akış Analizi Sorgu Dili Başvurusu](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Akış Analizi Yönetimi REST API'si Başvurusu](/rest/api/streamanalytics/)
