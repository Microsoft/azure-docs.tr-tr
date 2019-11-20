---
title: Azure Stream Analytics içindeki ölçek Machine Learning işlevleri
description: Bu makalede bölümlendirme ve akış birimleri yapılandırarak Machine Learning işlevleri kullanan Stream Analytics işlerin nasıl ölçeklendiriyapılacağı açıklanır.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 28734e5eaa693ca4ee31603863b69605a1d92c88
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467864"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-classic-functions"></a>Stream Analytics işinizi Azure Machine Learning Studio (klasik) işlevlerle ölçeklendirin

Bu makalede Azure Machine Learning işlevleri kullanan Azure Stream Analytics işlerin verimli bir şekilde ölçeklendirilmesi açıklanmaktadır. Genel olarak Stream Analytics işlerinin ölçeklendirilmesi hakkında daha fazla bilgi için bkz. [Ölçek işleri](stream-analytics-scale-jobs.md).

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>Stream Analytics Azure Machine Learning işlev nedir?

Stream Analytics bir Machine Learning işlevi Stream Analytics sorgu dilinde normal işlev çağrısı gibi kullanılabilir. Ancak, arka planda bu işlev çağrıları aslında Web hizmeti istekleri Azure Machine Learning.

Birden çok satırı aynı Web hizmeti API çağrısında birlikte "toplu olarak" izleyerek Machine Learning Web hizmeti isteklerinin verimini geliştirebilirsiniz. Bu gruplandırma bir mini toplu iş olarak adlandırılır. Daha fazla bilgi için bkz. [Azure Machine Learning Studio (klasik) Web Hizmetleri](../machine-learning/studio/consume-web-services.md). Stream Analytics Azure Machine Learning Studio (klasik) için destek önizleme aşamasındadır.

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Machine Learning işlevlerle Stream Analytics işi yapılandırma

Stream Analytics işiniz tarafından kullanılan Machine Learning işlevini yapılandırmak için iki parametre vardır:

* Machine Learning işlev çağrılarının toplu iş boyutu.
* Stream Analytics işi için sağlanan akış birimlerinin (SUs) sayısı.

SUs için uygun değerleri belirlemek için Stream Analytics işin gecikmesini veya her SU verimini iyileştirmek isteyip istemediğinize karar verin. İyi bölümlenen bir Stream Analytics sorgusunun aktarım hızını artırmak için, SUs her zaman bir işe eklenebilir. Ek SUs işi çalıştırma maliyetini artırır.

Stream Analytics işiniz için gecikme süresi *toleransını* belirleme. Toplu iş boyutunu artırmak, Azure Machine Learning isteklerinizin gecikmesini ve Stream Analytics işinin gecikmesini arttırır.

Toplu iş boyutunu artırmak, Stream Analytics işinin **aynı sayıda** Machine Learning Web hizmeti isteği ile **daha fazla olay** işlemesini sağlar. Machine Learning Web hizmeti gecikmesi artışı genellikle toplu iş boyutu artışına göre daha doğrusal olur. 

Belirli bir durumda Machine Learning Web hizmeti için en uygun maliyetli toplu iş boyutunu göz önünde bulundurmanız önemlidir. Web hizmeti istekleri için varsayılan toplu iş boyutu 1000 ' dir. Bu varsayılan boyutu, Stream Analytics için [Stream Analytics REST API](https://docs.microsoft.com/previous-versions/azure/mt653706(v=azure.100) "Akış Analizi REST API") veya [PowerShell istemcisini](stream-analytics-monitor-and-manage-jobs-use-powershell.md)kullanarak değiştirebilirsiniz.

Bir toplu iş boyutuna karar verdikten sonra, işlevin saniyede işlemesi gereken olay sayısına göre akış birimlerinin (SUs) sayısını ayarlayabilirsiniz. Akış birimleri hakkında daha fazla bilgi için bkz. [Stream Analytics ölçek işleri](stream-analytics-scale-jobs.md).

Her 6 SUs Machine Learning Web hizmetine yönelik 20 eşzamanlı bağlantı alır. Ancak, 1 SU işi ve 3 SU işi 20 eşzamanlı bağlantı alır.  

Uygulamanız saniyede 200.000 olay oluşturursa ve toplu iş boyutu 1000 ise, sonuçta elde edilen Web hizmeti gecikmesi 200 MS 'dir. Bu hız, her bağlantının her saniye Machine Learning Web hizmetinde beş istek yapabileceği anlamına gelir. Stream Analytics işi, 20 bağlantıyla birlikte 200 MS ve 100.000 olaylarında 20.000 olayı saniye içinde işleyebilir.

Saniyede 200.000 olay işlemek için, Stream Analytics işi 12 ' ye kadar olan 40 eşzamanlı bağlantı gerektirir. Aşağıdaki diyagramda Stream Analytics işinden Machine Learning Web hizmeti uç noktasına yapılan istekler gösterilmektedir – her 6 SUs, Web hizmeti için en fazla 20 eşzamanlı bağlantı Machine Learning.

![Machine Learning Işlevleri ile Stream Analytics ölçek iki iş örneği](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Machine Learning Işlevleri ile Stream Analytics ölçek iki iş örneği")

Genel olarak ***b*** toplu iş boyutu için, b toplu iş boyutunda Web hizmeti gecikmesi için ***L*** , ***N*** SUs ile bir Stream Analytics işinin aktarım hızı:

![Machine Learning Işlevleri formülüyle ölçek Stream Analytics](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Machine Learning Işlevleri formülüyle ölçek Stream Analytics")

Ayrıca, Machine Learning Web hizmetinde ' maksimum eşzamanlı çağrılar ' yapılandırabilirsiniz. Bu parametreyi en büyük değere ayarlamanız önerilir (Şu anda 200).

Bu ayar hakkında daha fazla bilgi için, [Machine Learning Web Hizmetleri Için ölçeklendirme makalesini](../machine-learning/studio/scaling-webservice.md)inceleyin.

## <a name="example--sentiment-analysis"></a>Örnek: Yaklaşım Analizi
Aşağıdaki örnek, [Stream Analytics Machine Learning tümleştirme öğreticisinde](stream-analytics-machine-learning-integration-tutorial.md)açıklandığı gibi, yaklaşım Analizi Machine Learning işleviyle Stream Analytics bir işi içerir.

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

1 SU kullanılarak bu Stream Analytics iş trafiği işleyebilir mi? İş, 1000 varsayılan toplu iş boyutunu kullanarak giriş ile devam edebilir. Yaklaşım Analizi Machine Learning Web hizmetinin varsayılan gecikmesi (varsayılan toplu iş boyutu 1000 ile), bir saniyeden daha fazla gecikme süresi üretir.

Stream Analytics işin **genel** veya uçtan uca gecikmesi genellikle birkaç saniye sürer. *Özellikle* Machine Learning işlevi çağrılarındaki bu Stream Analytics işe daha ayrıntılı bir bakış uygulayın. Toplu iş boyutu 1000 ile, 10.000 olaylarının bir verimi Web hizmetinde 10 istek alır. Tek bir SU ile, bu giriş trafiğini barındırmak için yeterli eşzamanlı bağlantı vardır.

Giriş olayı hızı, 100x tarafından artıyorsa, Stream Analytics işin saniye başına 1.000.000 kata kadar işlemesi gerekir. Artan ölçeği gerçekleştirmenin iki seçeneği vardır:

1. Toplu iş boyutunu artırın.
2. Olayları paralel olarak işlemek için giriş akışını bölümleyin.

İlk seçenekle iş **gecikmesi** artar.

İkinci seçenekle, daha fazla eşzamanlı Machine Learning Web hizmeti isteğinde bulunan daha fazla SUs sağlamanız gerekir. Bu daha fazla sayıda SUs, iş **maliyetini**artırır.

Her toplu iş boyutu için aşağıdaki gecikme ölçümlerini kullanarak ölçeklendirmeyi inceleyelim:

| Gecikme süresi | Toplu iş boyutu |
| --- | --- |
| 200 MS | 1000-olay toplu işleri veya altı |
| 250 MS | 5\.000-olay toplu işleri |
| 300 MS | 10.000-olay toplu işleri |
| 500 ms | 25.000-olay toplu işleri |

1. İlk seçenek (daha fazla SUs**sağlamama** ) kullanılıyor. Toplu iş boyutu **25.000**olarak artırılabilir. Toplu iş boyutunu bu şekilde artırmak, işin 1.000.000 olay işlemesini, Machine Learning Web hizmetiyle 20 eş zamanlı bağlantıyla (çağrı başına 500 ms gecikme süresi ile) işlemesini sağlayacaktır. Bu nedenle, Machine Learning Web hizmeti isteklerine yönelik yaklaşım işlev istekleri nedeniyle Stream Analytics işinin ek gecikmesi **200 MS** 'den **500 MS**'ye artırılabilir. Ancak, Machine Learning Web Hizmetleri bir isteğin yük boyutunun 4 MB veya daha küçük olmasını gerektirdiğinden ve 100 saniyelik işlemden sonra Web hizmeti isteklerinin zaman aşımına uğramasıyla, toplu **iş boyutu sonsuz bir şekilde** artamamıştır.
1. İkinci seçenek olarak, toplu iş boyutu 1000 ' de kalır, 200-MS Web hizmeti gecikme süresiyle, Web hizmetine yönelik her 20 eş zamanlı bağlantı, dakikada * 20 * 5 olay = 100.000 ' i 1000 işleyebilir. Bu nedenle, saniye başına 1.000.000 olay işlemek için, işin 60 SUs olması gerekir. İlk seçenekle karşılaştırıldığında Stream Analytics iş daha fazla Web hizmeti toplu istek oluşturacak ve bu da daha yüksek maliyetli bir maliyet oluşturuyor.

Aşağıda, farklı SUs ve Batch boyutları (saniye başına olay sayısı) için Stream Analytics işi verimlilik için bir tablo verilmiştir.

| toplu iş boyutu (ML gecikmesi) | 500 (200 ms) | 1\.000 (200 ms) | 5\.000 (250 MS) | 10.000 (300 ms) | 25.000 (500 MS) |
| --- | --- | --- | --- | --- | --- |
| **1 SU** |2\.500 |5\.000 |20.000 |30.000 |50.000 |
| **3 SUs** |2\.500 |5\.000 |20.000 |30.000 |50.000 |
| **6 SUs** |2\.500 |5\.000 |20.000 |30.000 |50.000 |
| **12 SUs** |5\.000 |10,000 |40.000 |60.000 |100.000 |
| **18 SUs** |7\.500 |15.000 |60.000 |90.000 |150.000 |
| **24 SUs** |10,000 |20.000 |80.000 |120.000 |200,000 |
| **...** |... |... |... |... |... |
| **60 SUs** |25.000 |50.000 |200,000 |300.000 |500.000 |

Şimdi, Stream Analytics çalışma Machine Learning işlevlerinin nasıl çalıştığını daha iyi anlayabilirsiniz. Ayrıca, veri kaynaklarından alınan Stream Analytics işlerin "çekme" verilerini ve her "çekme" Stream Analytics işinin işlemesi için bir olay toplu işi döndürdüğünü de anlamış olabilirsiniz. Bu çekme modeli Machine Learning Web hizmeti isteklerini nasıl etkiler?

Normalde, Machine Learning işlevleri için belirlediğimiz toplu iş boyutu, her Stream Analytics iş "pull" tarafından döndürülen olay sayısıyla tamamen bölünemez. Bu gerçekleştiğinde, Machine Learning Web hizmeti "kısmi" toplu işlerle çağırılır. Kısmi toplu işlerin kullanılması, olayları çekme işleminden çekmeye kadar birleştirme sırasında ek iş gecikmesi yükünü ortadan kaldırır.

## <a name="new-function-related-monitoring-metrics"></a>İşlevle ilgili yeni izleme ölçümleri
Stream Analytics işinin Izleyici alanında, işlevle ilgili üç ek ölçüm eklenmiştir. Bunlar, aşağıdaki grafikte gösterildiği gibi **Işlev isteklerdir**, **Işlev OLAYLARı** ve **başarısız işlev isteklerdir**.

![Machine Learning Işlevleri ölçümleriyle ölçek Stream Analytics](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Machine Learning Işlevleri ölçümleriyle ölçek Stream Analytics")

, Aşağıdaki gibi tanımlanır:

**Işlev istekleri**: işlev isteklerinin sayısı.

**Işlev olayları**: işlev isteklerindeki olay sayısı.

**Başarısız Işlev istekleri**: başarısız işlev isteklerinin sayısı.

## <a name="key-takeaways"></a>Anahtar koymalar

Machine Learning işlevlerle bir Stream Analytics işini ölçeklendirmek için aşağıdaki faktörleri göz önünde bulundurun:

1. Giriş olayı oranı.
2. Çalışan Stream Analytics işi için toleranslı gecikme süresi (ve bu nedenle Machine Learning Web hizmeti isteklerinin toplu iş boyutu).
3. Sağlanan Stream Analytics SUs ve Machine Learning Web hizmeti isteklerinin sayısı (işlevle ilgili ek maliyetler).

Örnek olarak, tamamen bölümlenmiş bir Stream Analytics sorgusu kullanılmıştır. Daha karmaşık bir sorgu gerekliyse, [Azure Stream Analytics forumu](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics) Stream Analytics takımdan ek yardım almak için harika bir kaynaktır.

## <a name="next-steps"></a>Sonraki adımlar
Stream Analytics hakkında daha fazla bilgi edinmek için bkz.:

* [Azure Akış Analizi'ni kullanmaya başlama](stream-analytics-real-time-fraud-detection.md)
* [Azure Akış Analizi işlerini ölçeklendirme](stream-analytics-scale-jobs.md)
* [Azure Akış Analizi Sorgu Dili Başvurusu](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Akış Analizi Yönetimi REST API'si Başvurusu](https://msdn.microsoft.com/library/azure/dn835031.aspx)
