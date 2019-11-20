---
title: Azure Stream Analytics akış birimleri
description: Bu makalede, akış birimleri ayarı ve Azure Stream Analytics performansını etkileyen diğer faktörler açıklanmaktadır.
services: stream-analytics
author: JSeb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2019
ms.openlocfilehash: 25105847b7134b7119252a66ac7e8502771ce5db
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961271"
---
# <a name="understand-and-adjust-streaming-units"></a>Akış birimlerini anlama ve ayarlama

Akış birimleri (SUs) Stream Analytics işini yürütmek için ayrılan bilgi işlem kaynaklarını temsil eder. SU sayısı ne kadar büyük olursa işe ayrılan CPU ve bellek kaynakları o kadar fazla olur. Bu kapasite, sorgu mantığına odaklanmanıza ve Stream Analytics işinizi zamanında çalıştırmak için donanımı yönetme ihtiyacını özetlemenizi sağlar.

Düşük gecikme süreli akış işlemeyi başarabilmek için, Azure Stream Analytics işleri tüm işlemi bellekte gerçekleştirir. Bellek tükençalışırken, akış işi başarısız olur. Sonuç olarak, bir üretim işi için bir akış işinin kaynak kullanımını izlemek önemlidir ve 24/7 çalıştıran işleri tutmak için yeterli kaynak bulunduğundan emin olun.

%0 ile %100 arasında değişen% SU kullanım ölçümü, iş yükünüzün bellek tüketimini açıklamaktadır. Minimum parmak izine sahip bir akış işi için bu ölçüm genellikle %10 ila %20 arasındadır. % SU kullanımı düşükse ve giriş olayları geri yazılır duruma gelir, iş yükünüz muhtemelen daha fazla bilgi işlem kaynağı gerektirir, bu da SUs sayısını artırmanız gerekir. SU ölçüsünü zaman zaman ani artışlar için %80 oranında hesaba tutmanız en iyisidir. Microsoft, kaynak tükenmesi 'ni engellemek için %80 SU kullanım ölçümü üzerinde bir uyarı ayarlanmasını öneriyor. Daha fazla bilgi için bkz. [öğretici: Azure Stream Analytics işleri için uyarıları ayarlama](stream-analytics-set-up-alerts.md).

## <a name="configure-stream-analytics-streaming-units-sus"></a>Stream Analytics akış birimlerini (SUs) yapılandırma
1. [Azure portalda](https://portal.azure.com/) oturum açın.

2. Kaynak listesinde, ölçeklendirmek istediğiniz Stream Analytics işini bulun ve sonra açın. 

3. İş sayfasında, **Yapılandır** başlığı altında, **Ölçek**' i seçin. 

    ![Azure portal Stream Analytics iş yapılandırması][img.stream.analytics.preview.portal.settings.scale]
    
4. İşi için SUs ayarlamak üzere kaydırıcıyı kullanın. Belirli SU ayarlarına sınırlı olduğunu unutmayın. 
5. Çalışma sırasında bile işinize atanan SUs sayısını değiştirebilirsiniz. İşiniz [bölümlenmiş olmayan bir çıkış](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#query-using-non-partitioned-output) kullanıyorsa veya [DEĞERLERI ile farklı bölüm içeren çok adımlı bir sorgu](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#multi-step-query-with-different-partition-by-values)içeriyorsa bu mümkün değildir. İş çalışırken bu ayarı değiştirmek için işin en az 6 SUs 'e sahip olması gerekir. İş çalışırken bir SU değeri kümesinden seçim yapmak zorunda olabilirsiniz. 

## <a name="monitor-job-performance"></a>İş performansını izleme
Azure portal kullanarak, bir işin verimini izleyebilirsiniz:

![Azure Stream Analytics izleme işleri][img.stream.analytics.monitor.job]

İş yükünün beklenen iş verimini hesaplayın. Aktarım hızı beklenenden küçükse, giriş bölümünü ayarlayın, sorguyu ayarlayın ve projenize SUs ekleyin.

## <a name="how-many-sus-are-required-for-a-job"></a>Bir iş için kaç tane SUs gerekiyor?

Belirli bir iş için gerekli SUs sayısının seçilmesi, giriş ve iş içinde tanımlanan sorgu için bölüm yapılandırmasına bağlıdır. **Ölçek** sayfası, doğru sayıda SUs ayarlamanıza olanak sağlar. Gerekenden daha fazla sayıda SUs ayırmak en iyi uygulamadır. Stream Analytics işleme altyapısı, ek bellek ayırma maliyetiyle gecikme süresi ve aktarım hızı için optimize eder.

Genel olarak en iyi yöntem, **bölüm tarafından**kullanmayan sorgular Için 6 SUs ile başlamadır. Daha sonra, temsili veri miktarını geçtikten sonra ve% su kullanım ölçümünü inceleyerek, SUs sayısını değiştirdiğiniz bir deneme ve hata yöntemi kullanarak SWE Bir Stream Analytics işi tarafından kullanılabilen en fazla akış birimi sayısı, iş için tanımlanan sorgudaki adım sayısına ve her adımdaki bölüm sayısına bağlıdır. Sınırlamalar hakkında [buradan](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#calculate-the-maximum-streaming-units-of-a-job)daha fazla bilgi edinebilirsiniz.

Doğru sayıda SUs seçme hakkında daha fazla bilgi için şu sayfaya bakın: [iş üretimini artırmak için Azure Stream Analytics Işleri ölçeklendirin](stream-analytics-scale-jobs.md)

> [!Note]
> Belirli bir iş için kaç tane SUs gerektiğini seçme, girişlerin bölüm yapılandırmasına ve iş için tanımlanan sorguya bağlıdır. Bir iş için SUs 'de kotayı seçebilirsiniz. Varsayılan olarak, her Azure aboneliğinin belirli bir bölgedeki tüm analiz işleri için 500 adede kadar SUs kotası vardır. Bu kotanın ötesinde abonelikleriniz için SUs 'i artırmak üzere [Microsoft desteği](https://support.microsoft.com)başvurun. İş başına SUs için geçerli değerler 1, 3, 6 ve 6 ' luk artışlarla artar.

## <a name="factors-that-increase-su-utilization"></a>% SU kullanımını artıran faktörler 

Zamana bağlı (zamana dayalı) sorgu öğeleri, Stream Analytics tarafından sağlanmış olan temel durum bilgisi işleçleri kümesidir. Stream Analytics, bu işlemlerin durumunu Kullanıcı adına, bellek tüketimini yöneterek, dayanıklılık için denetim ve hizmet yükseltmeleri sırasında durum kurtarma gibi bir şekilde yönetir. Durumları tam olarak yönetStream Analytics se de, kullanıcıların göz önünde bulundurmanız gereken bir dizi en iyi uygulama önerisi vardır.

Karmaşık sorgu mantığı olan bir işin, sürekli olarak giriş olayları almasa bile yüksek SU kullanımına sahip olabileceğini unutmayın. Bu, giriş ve çıkış olaylardaki ani bir ani artış sonrasında gerçekleşebilir. Sorgu karmaşıksa, iş bellekte durumu sürdürmek için devam edebilir.

% SU kullanımı, beklenen düzeylere geri dönebilmeniz için aniden kısa bir süre boyunca 0 ' a düşürüyordur. Bu, geçici hatalar veya sistem tarafından başlatılan yükseltmeler nedeniyle oluşur. Sorgu [tam olarak paralel](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization)değilse, bir iş için akış birimlerinin sayısının ARTıRıLMASı% su kullanımını azaltmayabilir.

## <a name="stateful-query-logicin-temporal-elements"></a>Durum bilgisi olan sorgu mantığının zamana bağlı öğeleri
Azure Stream Analytics işi benzersiz yeteneğinin, pencereli toplamlar, zamana bağlı birleştirmeler ve geçici analiz işlevleri gibi durum bilgisi olan işlem gerçekleştirmektir. Bu işleçlerin her biri durum bilgilerini tutar. Bu sorgu öğeleri için en büyük pencere boyutu yedi gündür. 

Geçici pencere kavramı çeşitli Stream Analytics sorgu öğelerin görünür:
1. Pencereli toplamalar: Içe geçmiş, atlamalı ve kayan pencereler tarafından gruplama

2. Zamana bağlı birleşimler: DATEDıFF işleviyle BIrLEŞTIr

3. Zamana bağlı analitik işlevler: ıSFIRST, LAST ve LIMIT SÜRESI ile GECIKME

Aşağıdaki etmenler, Stream Analytics işlere göre kullanılan belleği (akış birimi ölçümünün bir parçası) etkiler:

## <a name="windowed-aggregates"></a>Pencereli toplamalar
Bir pencereli toplama için tüketilen bellek (durum boyutu), pencere boyutuyla her zaman doğrudan orantılı değildir. Bunun yerine, tüketilen bellek, verilerin kardinalitesiyle veya her zaman penceredeki grup sayısından orantılıdır.


Örneğin, aşağıdaki sorguda `clusterid` ile ilişkili sayı sorgunun kardinalitedir. 

   ```sql
   SELECT count(*)
   FROM input 
   GROUP BY  clusterid, tumblingwindow (minutes, 5)
   ```

Önceki sorgudaki yüksek kardinalite nedeniyle oluşan herhangi bir sorunu azaltmak için, `clusterid`tarafından bölümlenen Olay Hub 'ına olay gönderebilir ve aşağıdaki örnekte gösterildiği gibi sistemin her giriş bölümünü **bölüm** kullanarak ayrı olarak işlemesini sağlayarak sorguyu ölçeklendirebilirsiniz:

   ```sql
   SELECT count(*) 
   FROM input PARTITION BY PartitionId
   GROUP BY PartitionId, clusterid, tumblingwindow (minutes, 5)
   ```

Sorgu bölümlere ayrıldıktan sonra birden çok düğüme dağıtılır. Sonuç olarak, her düğüme gelen `clusterid` değerlerinin sayısı azaltılır ve Group By işlecinin kardinalitesini azaltır. 

Azaltma adımının gereksinimini ortadan kaldırmak için, Olay Hub 'ı bölümlerinin gruplandırma anahtarına göre bölümlenmesi gerekir. Daha fazla bilgi için bkz. [Event Hubs genel bakış](../event-hubs/event-hubs-what-is-event-hubs.md). 

## <a name="temporal-joins"></a>Zamana bağlı birleşimler
Zamana bağlı bir birleştirmenin tüketilen bellek (durum boyutu), birleştirmenin, bir olay girişi oranının, Won Oda boyutuyla çarpıldığı, birleştirmenin zamana bağlı olarak, bu, bir girdinin, bu şekilde olan, bu durumda bulunan olayların sayısıyla orantılıdır. Diğer bir deyişle, birleştirmeler tarafından tüketilen bellek, DATEDIFF zaman aralığı ortalama olay oranıyla çarpılarak orantılıdır.

Birleşimdeki eşleşmeyen olay sayısı sorgunun bellek kullanımını etkiler. Aşağıdaki sorgu, tıklama oluşturan reklam görüntüleme sayısını bulmayı amaçlamaktadır:

   ```sql
   SELECT clicks.id
   FROM clicks 
   INNER JOIN impressions ON impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10.
   ```

Bu örnekte, çok sayıda reklam gösterilmesi ve birkaç kişinin üzerine tıkladığından ve tüm olayların zaman penceresinde tutulması gerekir. Tüketilen bellek miktarı pencere boyutu ve olay hızıyla doğru orantılıdır. 

Bunu düzeltmek için, Olay Hub 'ına JOIN anahtarlarına (Bu durumda KIMLIK) göre bölümlenmiş olayları gönderin ve aşağıdaki gibi, sistem her giriş bölümünü **bölüm** kullanarak ayrı olarak işlemesini sağlayarak sorguyu ölçeklendirin:

   ```sql
   SELECT clicks.id
   FROM clicks PARTITION BY PartitionId
   INNER JOIN impressions PARTITION BY PartitionId 
   ON impression.PartitionId = clicks.PartitionId AND impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10 
   ```

Sorgu bölümlere ayrıldıktan sonra birden çok düğüme dağıtılır. Sonuç olarak, her düğüme gelen olay sayısı azaltılır ve bu sayede, JOIN penceresinde tutulan durum boyutunu azaltmaktadır. 

## <a name="temporal-analytic-functions"></a>Zamana bağlı analitik işlevler
Zamana bağlı bir analitik işlevin tüketilen bellek (durum boyutu), Duration ile çarpılacak olay oranıyla orantılıdır. Analitik işlevler tarafından tüketilen bellek pencere boyutuyla orantılı değildir, ancak her zaman penceresinde bölüm sayısı olarak belirlenir.

Düzeltme, zamana bağlı birleşime benzerdir. **Bölüm**kullanarak sorguyu ölçeklendirebilirsiniz. 

## <a name="out-of-order-buffer"></a>Sıra dışı arabellek 
Kullanıcı, olay sıralaması Yapılandırma bölmesinde sıra dışı arabellek boyutunu yapılandırabilir. Arabellek, pencerenin süresi boyunca girişleri tutmak için kullanılır ve yeniden sıralayın. Arabelleğin boyutu, olay giriş oranı ile orantılıdır ve bu boyut, sırası pencere boyutu kadar çarpçıkar. Varsayılan pencere boyutu 0 ' dır. 

Sıra dışı arabelleğin taşmasını düzeltmek için, **bölüm tarafından**kullanılan sorguyu ölçeklendirin. Sorgu bölümlere ayrıldıktan sonra birden çok düğüme dağıtılır. Sonuç olarak, her düğüme gelen olay sayısı azaltılır ve bu sayede her bir yeniden sipariş arabelleğinin olay sayısını azaltır. 

## <a name="input-partition-count"></a>Giriş bölümü sayısı 
Bir iş girişinin her giriş bölümünün bir arabelleği vardır. Daha fazla sayıda giriş bölümü, işin tükettiği kaynak daha fazla. Her akış birimi için Azure Stream Analytics kabaca 1 MB/s girişi işleyebilir. Bu nedenle, Olay Hub 'ınızdaki bölüm sayısıyla Stream Analytics akış birimi sayısını eşleştirerek iyileştirebilirsiniz. 

Genellikle, bir akış birimi ile yapılandırılan bir iş, iki bölümden oluşan bir olay hub 'ı (Event Hub için en düşük değer) için yeterlidir. Olay Hub 'ında daha fazla bölüm varsa Stream Analytics işiniz daha fazla kaynak tüketir, ancak olay hub 'ı tarafından belirtilen ek aktarım hızını kullanmaz. 

6 akış birimi olan bir iş için Olay Hub 'ında 4 veya 8 Bölüm gerekebilir. Ancak, çok fazla sayıda gereksiz bölümden kaçının, bu yana aşırı kaynak kullanımına neden olur. Örneğin, 1 akış birimi olan Stream Analytics işinde 16 bölümlü veya daha büyük bir olay hub 'ı. 

## <a name="reference-data"></a>Başvuru verileri 
ASA içindeki başvuru verileri hızlı arama için belleğe yüklenir. Geçerli uygulamayla, başvuru verileriyle birlikte her bir JOIN işlemi, aynı başvuru verileriyle birden çok kez katılırsanız bile başvuru verilerinin bir kopyasını bellekte tutar. **Bölüm tarafından**oluşturulan sorgularda, her bölüm başvuru verilerinin bir kopyasına sahiptir, bu nedenle bölümler tamamen ayrılmış hale ulaşır. Birden çok bölümden oluşan başvuru verileriyle birden çok kez katılırsanız, çarpan etkisi sayesinde bellek kullanımı hızla çok yüksek bir şekilde alınabilir.  

### <a name="use-of-udf-functions"></a>UDF işlevlerinin kullanımı
Bir UDF işlevi eklediğinizde Azure Stream Analytics JavaScript çalışma zamanını belleğe yükler. Bu işlem% ' i etkiler.

## <a name="next-steps"></a>Sonraki adımlar
* [Azure Stream Analytics paralelleştirilebilir sorgular oluşturma](stream-analytics-parallelization.md)
* [İş üretimini artırmak için Azure Stream Analytics işleri ölçeklendirin](stream-analytics-scale-jobs.md)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   
