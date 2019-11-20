---
title: Azure Stream Analytics çözüm desenleri
description: Parçalama, olay mesajlaşma, veri depoları, başvuru verileri zenginleştirme ve izleme gibi Azure Stream Analytics yönelik yaygın çözüm desenleri hakkında bilgi edinin.
author: zhongc
ms.author: zhongc
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 2d936a538a54edce9e3f13ea7865d57b8243c4a5
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901684"
---
# <a name="azure-stream-analytics-solution-patterns"></a>Azure Stream Analytics çözüm desenleri

Daha büyük bir uçtan uca çözüm oluşturmak için, Azure 'daki pek çok farklı hizmet gibi Stream Analytics, diğer hizmetlerle en iyi şekilde kullanılır. Bu makalede basit Azure Stream Analytics çözümleri ve çeşitli mimari desenler açıklanmaktadır. Daha karmaşık çözümler geliştirmek için bu desenleri oluşturabilirsiniz. Bu makalede açıklanan desenler çok çeşitli senaryolarda kullanılabilir. [Azure çözüm mimarilerinde](https://azure.microsoft.com/solutions/architecture/?product=stream-analytics)senaryoya özgü desenlere örnek olarak ulaşılabilir.

## <a name="create-a-stream-analytics-job-to-power-real-time-dashboarding-experience"></a>Gerçek zamanlı kesik taslak deneyimi için bir Stream Analytics işi oluşturun

Azure Stream Analytics ile, kolayca gerçek zamanlı panolar ve uyarılar oluşturabilirsiniz. Event Hubs veya IoT Hub ait olayları geri alma ve [Power BI panosunun akış veri kümesiyle beslemelerine](/power-bi/service-real-time-streaming)yönelik basit bir çözüm. Daha fazla bilgi için bkz. [Stream Analytics ile telefon araması verilerini analiz etme ve sonuçları Power BI panoda görselleştirme](stream-analytics-manage-job.md).

![ASA Power BI panosu](media/stream-analytics-solution-patterns/pbidashboard.png)

Bu çözüm, yalnızca birkaç dakika içinde Azure portal oluşturulabilir. İlgili kapsamlı bir kodlama yoktur ve iş mantığını ifade etmek için SQL dili kullanılır.

Bu çözüm modelinde, bir tarayıcıda olay kaynağından Power BI panosuna en düşük gecikme süresi sunulur. Azure Stream Analytics bu yerleşik özelliği içeren tek Azure hizmetidir.

## <a name="use-sql-for-dashboard"></a>Pano için SQL kullanma

Power BI panosu düşük gecikme süresi sunar, ancak tam kapsamlı Power BI raporları üretmek için kullanılamaz. Yaygın bir raporlama deseninin verileri, önce SQL veritabanına çıktı olarak kullanılır. Daha sonra Power BI SQL bağlayıcısını kullanarak en son veriler için SQL 'i sorgulayın.

![ASA SQL panosu](media/stream-analytics-solution-patterns/sqldashboard.png)

SQL veritabanı kullanmak, daha fazla esneklik elde etmenizi sağlar, ancak biraz daha yüksek bir gecikme olur. Bu çözüm, gecikme süresi bir saniyeden daha büyük olan işler için idealdir. Bu yöntemde, raporlar için verileri daha fazla dilimleyip zarmak ve çok daha fazla görselleştirme seçeneği için Power BI yeteneklerini en üst düzeye çıkarabilirsiniz. Ayrıca, Tableau gibi diğer Pano çözümlerini kullanma esnekliği de elde edersiniz.

SQL yüksek bir işleme veri deposu değildir. Azure Stream Analytics bir SQL veritabanı için en yüksek aktarım hızı şu anda 24 MB/sn 'nin üzerinde. Çözümünüzdeki olay kaynakları daha yüksek bir hızda veri üretemiyor, SQL 'e çıkış oranını azaltmak için Stream Analytics işlem mantığını kullanmanız gerekir. Filtreleme, pencereli toplamalar, zamana bağlı birleştirmelere göre desenler ve analitik işlevler gibi teknikler de kullanılabilir. SQL için çıkış oranı, [Azure SQL veritabanı 'na Azure Stream Analytics çıktı](stream-analytics-sql-output-perf.md)bölümünde açıklanan teknikler kullanılarak daha da iyileştirilebilir.

## <a name="incorporate-real-time-insights-into-your-application-with-event-messaging"></a>Olay mesajlaşması ile uygulamanıza gerçek zamanlı Öngörüler ekleyin

En popüler Stream Analytics kullanımı gerçek zamanlı uyarılar oluşturmak için kullanılır. Bu çözüm desenindeki Stream Analytics iş mantığı, zamana bağlı [ve uzamsal desenleri](stream-analytics-geospatial-functions.md) ve [anormallikleri](stream-analytics-machine-learning-anomaly-detection.md)algılamak için kullanılabilir ve ardından uyarı sinyalleri üretir. Ancak, Stream Analytics tercih edilen bir uç nokta olarak Power BI kullandığı Pano çözümünün aksine, bir dizi ara veri havuzları kullanılabilir. Bu havuzlar Event Hubs, Service Bus ve Azure Işlevlerini içerir. Uygulama Oluşturucu olarak, senaryolarınız için hangi veri havuzunun en iyi şekilde çalıştığına karar vermeniz gerekir.

Mevcut iş iş akışınızda uyarı oluşturmak için aşağı akış olay tüketicisi mantığı uygulanmalıdır. Azure Işlevlerinde özel mantık uygulayabilmeniz için, Azure Işlevleri Bu tümleştirmeyi gerçekleştirmenin en hızlı yoludur. Azure Işlevi 'nin bir Stream Analytics işi için çıkış olarak kullanılmasına yönelik bir öğretici, [Azure Stream Analytics ışlerden Azure Işlevleri çalıştırma](stream-analytics-with-azure-functions.md)bölümünde bulunabilir. Azure Işlevleri, metin ve e-posta gibi çeşitli bildirim türlerini de destekler. Mantıksal uygulama, Stream Analytics ve Logic App arasında Event Hubs bir tümleştirme için de kullanılabilir.

![ASA olay mesajlaşma uygulaması](media/stream-analytics-solution-patterns/eventmessagingapp.png)

Diğer yandan Event Hubs en esnek tümleştirme noktasını sunar. Azure Veri Gezgini ve Time Series Insights gibi diğer birçok hizmet Event Hubs olayları kullanabilir. Hizmetler, çözümü tamamlayabilmeniz için Azure Stream Analytics Event Hubs havuzuna doğrudan bağlanabilir. Event Hubs Ayrıca, Azure 'da bu tür tümleştirme senaryolarında bulunan en yüksek aktarım hızı ileti aracısıdır.

## <a name="dynamic-applications-and-websites"></a>Dinamik uygulamalar ve Web siteleri

Azure Stream Analytics ve Azure SignalR hizmetini kullanarak pano veya harita görselleştirmesi gibi özel gerçek zamanlı görselleştirmeler oluşturabilirsiniz. SignalR kullanarak Web istemcileri güncelleştirilemeyebilir ve dinamik içeriği gerçek zamanlı olarak gösterebilir.

![ASA dinamik uygulaması](media/stream-analytics-solution-patterns/dynamicapp.png)

## <a name="incorporate-real-time-insights-into-your-application-through-data-stores"></a>Veri depoları aracılığıyla uygulamanıza gerçek zamanlı Öngörüler ekleyin

Günümüzde çoğu Web hizmeti ve Web uygulaması, sunu katmanını sağlamak için bir istek/yanıt kalıbı kullanır. İstek/yanıt deseninin derlenmesi kolaydır ve Cosmos DB gibi durum bilgisi olmayan ön uç ve ölçeklenebilir depolar kullanılarak düşük yanıt süresi ile kolayca ölçeklendirilebilir.

Yüksek veri hacmi genellikle CRUD tabanlı bir sistemde performans sorunlarını oluşturur. Olay kaynağını belirleme [çözüm deseninin](/azure/architecture/patterns/event-sourcing) performans sorunlarını gidermek için kullanılır. Zamana bağlı desenler ve Öngörüler ayrıca geleneksel bir veri deposundan ayıklamak zor ve verimsiz bir şekilde yapılır. Modern yüksek hacimli veri odaklı uygulamalar genellikle veri akışı tabanlı bir mimari benimseyin. Hareket halindeki veriler için işlem altyapısı, bu mimarideki bir süreçlerini olarak Azure Stream Analytics.

![ASA olayı kaynak kaynağı uygulaması](media/stream-analytics-solution-patterns/eventsourcingapp.png)

Bu çözüm modelinde olaylar işlenir ve Azure Stream Analytics tarafından veri depolarında toplanır. Uygulama katmanı, geleneksel istek/yanıt modelini kullanarak veri depolarıyla etkileşime girer. Stream Analytics ' nin gerçek zamanlı çok sayıda olayı işleyebilme özelliği nedeniyle, uygulama, veri deposu katmanını toplu olarak önemli ölçüde ölçeklenebilir olur. Veri deposu katmanı aslında sistemde gerçekleştirilmiş bir görünüm olur. [Azure Cosmos db çıktı Azure Stream Analytics](stream-analytics-documentdb-output.md) Cosmos DB Stream Analytics çıkış olarak nasıl kullanıldığını açıklar.

İşlem mantığının karmaşık olduğu ve mantığın belirli kısımlarını bağımsız olarak yükseltme ihtiyacı olan gerçek uygulamalarda, birden çok Stream Analytics işi aracı olay aracısı olarak Event Hubs birlikte oluşturulabilir.

![ASA karmaşık olay kaynağını belirleme uygulaması](media/stream-analytics-solution-patterns/eventsourcingapp2.png)

Bu model sistemin esnekliğini ve yönetilebilirliğini geliştirir. Ancak, Stream Analytics tam olarak bir kez işlemeyi garanti etse de, yinelenen olayların ara Event Hubs bir şekilde aratabileceğini belirten küçük bir şansınız vardır. Aşağı akış Stream Analytics işinin, geriye doğru bir penceredeki mantıksal anahtarları kullanarak olayları kaldırmaması önemlidir. Olay teslimi hakkında daha fazla bilgi için bkz. [olay teslimi garantisi](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) başvurusu.

## <a name="use-reference-data-for-application-customization"></a>Uygulama özelleştirmesi için başvuru verilerini kullanma

Azure Stream Analytics başvuru verileri özelliği, uyarı eşiği, işleme kuralları ve [bölge dilimleri](geospatial-scenarios.md)gibi son kullanıcı özelleştirmesi için özel olarak tasarlanmıştır. Uygulama katmanı, parametre değişikliklerini kabul edebilir ve bunları bir SQL veritabanında saklayabilir. Stream Analytics işi veritabanındaki değişiklikleri düzenli aralıklarla sorgular ve özelleştirme parametrelerini bir başvuru verileri birleşimi aracılığıyla erişilebilir hale getirir. Uygulama özelleştirmesi için başvuru verilerini kullanma hakkında daha fazla bilgi için bkz. [SQL başvuru verileri](sql-reference-data.md) ve [başvuru verileri katılımı](/stream-analytics-query/reference-data-join-azure-stream-analytics).

Bu model, kural eşiklerinin başvuru verilerinden tanımlandığı bir kural altyapısını uygulamak için de kullanılabilir. Kurallar hakkında daha fazla bilgi için, bkz. [Azure Stream Analytics yapılandırılabilir eşik tabanlı kuralları işleme](stream-analytics-threshold-based-rules.md).

![ASA başvuru verileri uygulaması](media/stream-analytics-solution-patterns/refdataapp.png)

## <a name="add-machine-learning-to-your-real-time-insights"></a>Gerçek zamanlı öngörülere Machine Learning ekleyin

Azure Stream Analytics ' yerleşik [anomali algılama modeli](stream-analytics-machine-learning-anomaly-detection.md) , gerçek zamanlı uygulamanıza Machine Learning tanıtmak için kullanışlı bir yoldur. Machine Learning gereksinimlerle ilgili daha fazla bilgi için bkz. [Azure Stream Analytics Azure Machine Learning Puanlama hizmeti ile tümleşir](stream-analytics-machine-learning-integration-tutorial.md).

Çevrimiçi eğitim ve Puanlama 'yı aynı Stream Analytics işlem hattına eklemek isteyen ileri düzey kullanıcılar için, [Doğrusal gerileme](stream-analytics-high-frequency-trading.md)ile nasıl yapılacağını gösteren bu örneğe bakın.

![ASA Machine Learning uygulaması](media/stream-analytics-solution-patterns/mlapp.png)

## <a name="near-real-time-data-warehousing"></a>Neredeyse gerçek zamanlı veri depolama

Diğer bir yaygın model, akış veri ambarı olarak da adlandırılan gerçek zamanlı veri ambardır. Event Hubs IoT Hub ve uygulamanızdan gelen olaylara ek olarak, [IoT Edge üzerinde çalışan Azure Stream Analytics](stream-analytics-edge.md) veri temizleme, veri azaltma ve veri depolama ve iletme ihtiyaçlarını karşılamak için kullanılabilir. IoT Edge üzerinde çalışan Stream Analytics, sistemdeki bant genişliği sınırlamasını ve bağlantı sorunlarını düzgün şekilde işleyebilir. SQL çıkış bağdaştırıcısı SQL veri ambarı 'na çıkış yapmak için kullanılabilir; Ancak, en fazla üretilen iş 10 MB/sn ile sınırlıdır.

![ASA veri ambarı](media/stream-analytics-solution-patterns/datawarehousing.png)

Bazı gecikme süresi zorunluluğunu getirir ile üretilen işi geliştirmenin bir yolu, olayları Azure Blob depolama alanına arşivlemek ve daha sonra [PolyBase Ile SQL Data Warehouse 'a içeri aktarmaktır](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md). [Verileri zaman damgasıyla arşivleyerek](stream-analytics-custom-path-patterns-blob-storage-output.md) ve düzenli aralıklarla içeri aktararak BLOB depolama ve BLOB depolamadan SQL veri ambarı 'na giriş Stream Analytics el ile bir araya almalısınız.

Bu kullanım modelinde, Azure Stream Analytics neredeyse gerçek zamanlı ETL altyapısı olarak kullanılır. Yeni gelen olaylar sürekli olarak dönüştürülür ve aşağı akış analizi hizmeti tüketimi için depolanır.

![ASA yüksek aktarım hızı veri depolama](media/stream-analytics-solution-patterns/datawarehousing2.png)

## <a name="archiving-real-time-data-for-analytics"></a>Analiz için gerçek zamanlı verileri arşivleme

Çoğu veri bilimi ve analiz etkinliği yine de çevrimdışı gerçekleşir. Veriler, Azure Data Lake Store Gen2 Output ve Parquet çıkış biçimleri aracılığıyla Azure Stream Analytics arşivlenebilir. Bu özellik, verileri doğrudan Azure Data Lake Analytics, Azure Databricks ve Azure HDInsight 'a akışa almak için bu özelliği ortadan kaldırır. Azure Stream Analytics, bu çözümde neredeyse gerçek zamanlı bir ETL altyapısı olarak kullanılır. Data Lake, çeşitli işlem altyapılarını kullanarak arşivlenmiş verileri keşfedebilirsiniz.

![ASA çevrimdışı Analizi](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="use-reference-data-for-enrichment"></a>Zenginleştirme için başvuru verilerini kullanma

Veri zenginleştirme, genellikle ETL motorları için gereksinimdir. Azure Stream Analytics hem SQL veritabanı hem de Azure Blob depolama alanındaki [başvuru verileriyle](stream-analytics-use-reference-data.md) veri zenginleştirmesini destekler. Veri zenginleştirme, hem Azure Data Lake hem de SQL veri ambarı 'nda veri sahanlığı için yapılabilir.

![Veri zenginleştirme ile ASA çevrimdışı Analizi](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="operationalize-insights-from-archived-data"></a>Arşivlenmiş verilerden Öngörüler elde edin

Çevrimdışı analiz modelini neredeyse gerçek zamanlı uygulama düzeniyle birleştirirseniz, bir geri bildirim döngüsü oluşturabilirsiniz. Geri bildirim döngüsü, uygulamanın veride değişen desenleri otomatik olarak ayarlamasını sağlar. Bu geri bildirim döngüsü, uyarı için eşik değerini değiştirmek veya Machine Learning modelleri yeniden eğitim olarak karmaşık olabilir. Aynı çözüm mimarisi, bulutta ve IoT Edge çalışan her iki ASA işine da uygulanabilir.

![ASA öngörüleri işlemleştirme](media/stream-analytics-solution-patterns/insightsoperationalization.png)

## <a name="how-to-monitor-asa-jobs"></a>ASA işlerini izleme

Azure Stream Analytics bir iş, gelen olayları sürekli olarak gerçek zamanlı olarak işlemek için 24/7 çalıştırılabilir. Çalışma süresi garantisi, genel uygulamanın sistem durumu için önemlidir. Sektörün [% 99,9 kullanılabilirlik garantisi](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/)sunan tek akış analizi hizmeti Stream Analytics, ancak yine de bir düzey süre kullanmaya devam edebilirsiniz. Yıl boyunca Stream Analytics, işlerin sistem durumunu yansıtmak için ölçümler, Günlükler ve iş durumları getirmiştir. Bunların hepsi Azure Izleyici hizmeti üzerinden ortaya çıkmış ve OMS 'ye daha fazla aktarılabilir. Daha fazla bilgi için bkz. [Stream Analytics iş Izlemeyi anlama ve sorguları izleme](stream-analytics-monitoring.md).

![ASA izleme](media/stream-analytics-solution-patterns/monitoring.png)

İzlemeniz gereken iki önemli nokta vardır:

- [İş başarısız durumu](job-states.md)

    İlk ve daha sonra, işin çalıştığından emin olmanız gerekir. Çalışma durumunda iş olmadan, yeni ölçüm veya günlük oluşturulmaz. İşler, yüksek SU kullanım düzeyi (örneğin, kaynakları tükenme) dahil olmak üzere çeşitli nedenlerle başarısız durumuna değiştirilebilir.

- [Filigran gecikmesi ölçümleri](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)

    Bu ölçüm, işleme işlem hattının ne kadar gerisinde olduğunu yansıtır (saniye). Gecikmeden bazıları, devralınan işleme mantığına göre belirlenir. Sonuç olarak, artan eğilimi izlemek, mutlak değeri izlemeden çok daha önemlidir. Sabit durum gecikmesi, izleme veya uyarılarla değil, uygulama tasarımınız tarafından değinilmelidir.

Hata sonrasında, etkinlik günlükleri ve [tanılama günlükleri](stream-analytics-job-diagnostic-logs.md) , hata aramaya başlamak için en iyi yer lardır.

## <a name="build-resilient-and-mission-critical-applications"></a>Esnek ve görev açısından kritik uygulamalar oluşturun

Azure Stream Analytics ' SLA garantisi ve uçtan uca uygulamanızı ne kadar dikkatli olduğunuza bakılmaksızın kesintiler meydana gelir. Uygulamanız görev açısından önemliyse, düzgün bir şekilde kurtarmak için kesintiler için hazırlıklı olmanız gerekir.

Uyarı uygulamaları için en önemli şey, sonraki uyarıyı algılamasıdır. Kurtarma sırasında son uyarıları yoksayarak işi geçerli zamandan yeniden başlatmayı tercih edebilirsiniz. İş başlangıç zamanı semantiği ilk giriş saati değil, ilk çıkış zamanına göre yapılır. Giriş, belirtilen zamanda ilk çıktının tamamlandığı ve doğru olması için uygun bir süre geri gönderilir. Kısmi toplamalar almaz ve uyarılar bir sonuç olarak beklenmedik şekilde tetiklenemez.

Ayrıca, geçmişteki bir süre sonunda çıktıyı başlatmayı da tercih edebilirsiniz. Event Hubs ve IoT Hub bekletme ilkeleri, geçmişteki işleme izin vermek için makul miktarda veriyi tutar. Zorunluluğunu getirir, geçerli zamandan en hızlı şekilde yakalayabilmeniz ve zamanında yeni uyarılar oluşturmaya başlayabilir. Veriler zaman içinde hızlı bir şekilde kaybeder. bu nedenle, geçerli zamana hızlı bir şekilde yakalamak önemlidir. Hızlı bir şekilde hızla yakalayabilmenizi sağlamanın iki yolu vardır:

- Yakalama sırasında daha fazla kaynak (SU) sağlayın.
- Geçerli zamandan yeniden başlatın.

Geçerli zamandan yeniden başlatma işlemi, işlem sırasında zorunluluğunu getirir bir boşluk bırakarak basittir. Bu şekilde yeniden başlatma, uyarı senaryolarında sorunsuz olabilir, ancak Pano senaryolarında sorunlu olabilir ve arşivleme ve veri ambarı senaryolarında bir Starter olmayan bir yoldur.

Daha fazla kaynağın sağlanması işlemi hızlandırabilir, ancak işlem hızı dalgalanmasına sahip olmanın etkisi karmaşıktır.

- İşinizin daha fazla sayıda SUs ile ölçeklenebilir olduğunu test edin. Sorguların hepsi ölçeklenebilir değildir. Sorgunuzun [paralelleştirilmesine](stream-analytics-parallelization.md)sahip olduğunuzdan emin olun.

- Yukarı akış Event Hubs yeterli bölüm olduğundan emin olun veya giriş aktarım hızını ölçeklendirmek için daha fazla üretilen Iş birimi (DTU) ekleyebilmeniz IoT Hub. Her bir Event Hubs, 2 MB/sn çıkış oranıyla hızlanacağını unutmayın.

- Çıkış havuzları (örneğin, SQL veritabanı, Cosmos DB) için yeterli kaynak sağladığınızdan emin olun, bu nedenle çıkışta ani artışı azalmayın, bu da bazen sistemin kilitlenmesine neden olabilir.

En önemli şey, işleme oranı değişikliğini tahmin etmek, üretime geçmeden önce bu senaryoları test etmek ve hata kurtarma zamanı sırasında işlemeyi doğru şekilde ölçeklendirmek için hazırlanmalıdır.

Gelen olayların tamamen geciktirilen Extreme senaryosunda, işinize geç bir pencere uyguladıysanız [Tüm geciken olaylar bırakılır](stream-analytics-time-handling.md) . Olayların atılması, baştan önce herhangi bir gizdeğerli davranış gibi görünebilir; Ancak, Stream Analytics gerçek zamanlı bir işleme altyapısı olduğunu düşünürken, gelen olayların duvar saati zamanına yakın olmasını bekler. Bu kısıtlamaları ihlal eden olayları bırakması gerekmez.

### <a name="lambda-architectures-or-backfill-process"></a>Lambda mimarileri veya geri doldurma işlemi

Neyse ki, bu geç olayları düzgün bir şekilde işlemek için önceki veri arşivleme deseninin kullanılması de gerekebilir. Bu düşünce, arşivleme işinin gelen olayları varış zamanında işleme ve olayları Azure blob ' daki doğru zaman demetine ve olay zamanlarıyla Azure Data Lake Store arşivlenmesi. Bir olayın ne kadar geç ulaştığı, hiçbir şekilde bırakılmayacağı önemi yoktur. Her zaman doğru zaman sepete eklenecektir. Kurtarma sırasında, arşivlenmiş olayları yeniden işleyebilir ve sonuçları seçim deposuna geri doldurabilirsiniz. Bu, lambda desenlerinin uygulanma biçimine benzer.

![ASA geri dolgusu](media/stream-analytics-solution-patterns/backfill.png)

Geri doldurma işlemi, büyük olasılıkla Azure Stream Analytics farklı bir programlama modeline sahip olan çevrimdışı bir toplu işlem sistemiyle yapılmalıdır. Bu, tüm işlem mantığının yeniden uygulanması gerektiği anlamına gelir.

Geri doldurma için, sabit durum işleme gereksinimlerinden daha yüksek aktarım hızını işlemek üzere çıkış havuzları için en az geçici olarak daha fazla kaynak sağlanması hala önemlidir.

|Senaryolar  |Yalnızca Şimdi yeniden Başlat  |Son durdurulma zamanından yeniden Başlat |Arşivlenmiş olaylarla şimdi yeniden Başlat + geri doldur|
|---------|---------|---------|---------|
|**Kesik taslak**   |Boşluk oluşturur    |Kısa süreli kesinti için Tamam    |Uzun süreli kullanım için kullanın |
|**Uyarı**   |Kabul edilebilir |Kısa süreli kesinti için Tamam    |Gerekli değil |
|**Olay kaynağını belirleme uygulaması** |Kabul edilebilir |Kısa süreli kesinti için Tamam    |Uzun süreli kullanım için kullanın |
|**Veri depolama**   |Veri kaybı  |Kabul edilebilir |Gerekli değil |
|**Çevrimdışı analiz**  |Veri kaybı  |Kabul edilebilir |Gerekli değil|

## <a name="putting-it-all-together"></a>Hepsini bir araya getirme

Yukarıda bahsedilen tüm çözüm desenlerinin karmaşık bir uçtan uca sistemde birlikte birleştirildiğini hayal etmek zor değildir. Birleştirilmiş sistem panolar, uyarı, olay kaynağını belirleme uygulaması, veri depolama ve çevrimdışı analiz özellikleri içerebilir.

Bu anahtar, sisteminizi birleştirilebilir desenlerle tasarlayabiliyor, böylece her alt sistem bağımsız olarak oluşturulabilir, test edilebilir, yükseltilir ve kurtarılır.

## <a name="next-steps"></a>Sonraki adımlar

Artık Azure Stream Analytics kullanarak çeşitli çözüm desenleri gördünüz. Bundan sonra derinlere inerek ilk Stream Analytics işinizi oluşturabilirsiniz:

* [Azure portalını kullanarak Stream Analytics işi oluşturma](stream-analytics-quick-create-portal.md).
* [Azure PowerShell kullanarak Stream Analytics işi oluşturma](stream-analytics-quick-create-powershell.md).
* [Visual Studio 'yu kullanarak bir Stream Analytics Işi oluşturun](stream-analytics-quick-create-vs.md).
