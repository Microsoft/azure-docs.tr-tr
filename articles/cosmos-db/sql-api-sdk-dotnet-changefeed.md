---
title: Azure Cosmos DB .NET değişiklik akışı İşlemci API'si ve SDK’sı sürüm notları
description: Değişiklik Akışı İşlemci API'si ve SDK'sı hakkında sürüm tarihleri, kullanımdan kaldırma tarihleri ve .NET Değişiklik Akışı İşlemci SDK'sının sürümleri arasındaki değişiklikler de dahil olmak üzere her şeyi öğrenin.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.openlocfilehash: 8f38ed175241bdb9235795d7a999a82d0799d46d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876589"
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>.NET Değişiklik Akışı İşlemci SDK'sı: İndirme ve sürüm notları
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
>
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET Değişiklik Akışı SDK'sı v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK’sı v4](sql-api-sdk-java-v4.md)
> * [Zaman uyumsuz Java SDK v2](sql-api-sdk-async-java.md)
> * [Zaman uyumlu Java SDK v2](sql-api-sdk-java.md)
> * [Spring Verileri v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Verileri v3](sql-api-sdk-java-spring-v3.md)
> * [Spark 3 OLTP Bağlayıcısı](sql-api-sdk-java-spark-v3.md)
> * [Spark 2 OLTP Bağlayıcısı](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api
> * [REST Kaynak Sağlayıcısı](/rest/api
> * [SQL](sql-api-query-reference.md)
> * [Toplu yürütücü - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Toplu yürütücü - Java](sql-api-sdk-bulk-executor-java.md)

|   | Bağlantılar  |
|---|---|
|**SDK indirmesi**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**API belgeleri**|[Değişiklik Akışı İşlemcisi kitaplığı API başvurusu belgeleri](/dotnet/api/microsoft.azure.documents.changefeedprocessor)|
|**Kullanmaya başlama**|[Değişiklik Akışı İşlemcisi .NET SDK'sını kullanmaya başlama](change-feed.md)|
|**Şu anda desteklenen çerçeve**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET Core](https://dotnet.microsoft.com/download) |

> [!NOTE]
> Değişiklik akışı işlemcisini kullanıyorsanız, değişiklik akışının SDK'da yerleşik olarak bulunduğu [.NET SDK'sının](change-feed-processor.md) en yeni sürümü 3.x'e bakın. 

## <a name="release-notes"></a>Sürüm notları

### <a name="v2-builds"></a>v2 derlemeleri

### <a name="232"></a><a id="2.3.2"></a>2.3.2
* [V3 SDK'sına dinamik geçiş yollarını etkinleştiren kira deposu uyumluluğu eklendi. Uygulama V3 SDK'sına geçirilebilir ve hiçbir durum kaybı olmadan Değişiklik Akışı işlem kitaplığına geri döndürülebilir.

### <a name="231"></a><a id="2.3.1"></a>2.3.1
* Bölüm bulunamadığında veya hedef çoğaltmanın, okuma oturumu kadar güncel olmaması durumunda `FeedProcessing.IChangeFeedObserver.CloseAsync` hedefine `FeedProcessing.ChangeFeedObserverCloseReason.Unknown` kapatma nedeninin gönderilmesi durumu düzeltildi. Artık bu durumlarda `FeedProcessing.ChangeFeedObserverCloseReason.ResourceGone` ve `FeedProcessing.ChangeFeedObserverCloseReason.ReadSessionNotAvailable` kapatma nedenleri kullanılıyor.
* Hedef çoğaltmanın okuma oturumu kadar güncel olmadığı durumlarda değişiklik akışı gözlemcisini kapatmak için gönderilen yeni `FeedProcessing.ChangeFeedObserverCloseReason.ReadSessionNotAvailable` kapatma nedeni eklendi.

### <a name="230"></a><a id="2.3.0"></a>2.3.0
* Yeni `ChangeFeedProcessorBuilder.WithCheckpointPartitionProcessorFactory` metodu ve buna karşılık gelen `ICheckpointPartitionProcessorFactory` genel arabirimi eklendi. Bu, yerleşik denetim noktası mekanizmasını kullanmak için `IPartitionProcessor` arabiriminin uygulanmasına olanak tanır. Yeni fabrika, mevcut `IPartitionProcessorFactory` ile benzerdir; tek farkı `Create` metodunun aynı zamanda `ILeaseCheckpointer` parametresini almasıdır.
* Aynı `ChangeFeedProcessorBuilder` örneği için iki metottan yalnızca biri (`ChangeFeedProcessorBuilder.WithPartitionProcessorFactory` veya `ChangeFeedProcessorBuilder.WithCheckpointPartitionProcessorFactory`) kullanılabilir.

### <a name="228"></a><a id="2.2.8"></a>2.2.8
* Kararlılık ve tanılama geliştirmeleri:
  * Uzun süren değişiklik akışı okumasının algılanması için destek eklendi. `ChangeFeedProcessorOptions.ChangeFeedTimeout` özelliği tarafından belirtilen değerden uzun sürdüğünde aşağıdaki adımlar uygulanır:
    * Sorunlu bölümde değişiklik akışını okuma işlemi durdurulur.
    * Değişiklik akışı işlemci örneği sorunlu kiralamanın sahipliğini bırakır. Bırakılan kiralama, aynı veya farklı değişiklik akışı işlemci örneği tarafından yapılacak bir sonraki kiralama alma adımı sırasında seçilir. Bu şekilde değişiklik akışını okuma işlemi baştan başlar.
    * Durum izleyicisine bir sorun bildirilir. Varsayılan durum izleyicisi bildirilen tüm sorunları izleme günlüğüne gönderir.
  * Yeni bir ortak özellik eklendi: `ChangeFeedProcessorOptions.ChangeFeedTimeout`. Bu özelliğin varsayılan değeri 10 dakikadır.
  * Yeni bir genel sabit listesi değeri eklendi: `Monitoring.MonitoredOperation.ReadChangeFeed`. `HealthMonitoringRecord.Operation` değerinin `Monitoring.MonitoredOperation.ReadChangeFeed` olarak ayarlanması, sistem durumu sorununun değişiklik akışını okuma işlemiyle ilgili olduğunu gösterir.

### <a name="227"></a><a id="2.2.7"></a>2.2.7
* Tüm kiralamaları alma işleminin kira süre sonu aralığından daha uzun sürdüğü senaryolarda (örneğin ağ sorunlarından dolayı), yük dengeleme stratejisi geliştirildi:
  * Bu senaryoda yük dengeleme algoritması hatalı olarak kira sürelerinin dolduğunu varsayıyordu ve bu da etkin kullanıcılardan kiraların alınmasına neden oluyordu. Bu durum birçok kiralamada gereksiz bir yeniden dengeleme işlemi tetikleyebiliyordu.
  * Bu sürümde bu sorun, sahibin değiştirmediği süresi dolmuş kirayı alırken oluşan çakışmada yeniden deneme önlenerek ve süresi dolan kiranın alınması bir sonraki yük dengeleme yinelemesine ertelenerek düzeltildi.

### <a name="226"></a><a id="2.2.6"></a>2.2.6
* Gözlemci özel durumlarının işlenmesi geliştirildi.
* Gözlemci hatalarıyla ilgili daha zengin bilgi sağlanıyor:
  * Gözlemci, Gözlemcinin ProcessChangesAsync öğesi tarafından oluşturulan bir özel durum nedeniyle kapatılırsa,CloseAsync artık ChangeFeedObserverCloseReason.ObserverError olarak ayarlanmış neden parametresini alıyor.
  * Gözlemcide kullanıcı kodu içindeki hataları tanımlamak için izlemeler eklendi.

### <a name="225"></a><a id="2.2.5"></a>2.2.5
* Paylaşılan veritabanı aktarım hızını kullanan koleksiyonlarda bölme işlemini işleme desteği eklendi.
  * Bu sürümde, paylaşılan veritabanı aktarım hızı kullanılarak koleksiyonlarda bölme işlemi yapıldığı sırada, bölme işlemi sonucunda iki yerine yalnızca bir alt bölüm anahtarı aralığı oluşturularak bölüm yeniden dengelemesi yapıldığında oluşabilen bir sorun düzeltildi. Bu durum oluştuğunda Değişiklik Akışı İşlemcisi, eski bölüm anahtarı aralığına yönelik kiralama silinirken kilitlenebilir ve yeni kiralamalar oluşturmayabilir. Sorun bu sürümde düzeltildi.

### <a name="224"></a><a id="2.2.4"></a>2.2.4
* Değişiklik akışının istek devamlılık belirtecinden başlatılmasını desteklemek için yeni ChangeFeedProcessorOptions.StartContinuation özelliği eklendi. Bu özellik yalnızca kira koleksiyonu boş olduğunda veya kiranın ContinuationToken öğesi ayarlanmadığında kullanılır. ContinuationToken öğesinin ayarlandığı kira koleksiyonundaki kiralar için ContinuationToken kullanılır ve ChangeFeedProcessorOptions.StartContinuation yoksayılır.

### <a name="223"></a><a id="2.2.3"></a>2.2.3
* Bölüm başına devamlılık belirtecini kalıcı hale getirmek üzere özel depo kullanma desteği eklendi.
  * Örneğin özel kiralama deposu, özel bir yolla bölümlenmiş Azure Cosmos DB kira koleksiyonu olabilir.
  * Özel kira depoları yeni ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager) genişletilebilirlik noktasını ve ILeaseStoreManager genel arabirimini kullanabilir.
  * ILeaseManager arabirimi, birden çok rol arabirimi halinde yeniden düzenlendi.
* Hataya neden olan küçük değişiklik: ChangeFeedProcessorBuilder.WithLeaseManager(ILeaseManager) genişletilebilirlik noktası kaldırıldı; bunun yerine ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager) kullanın.

### <a name="222"></a><a id="2.2.2"></a>2.2.2
* Bu sürümde, izlenen koleksiyonda bir bölme işlenirken ve bölümlenmiş kira koleksiyonu kullanılırken oluşan bir sorun düzeltildi. Bölünmüş bölümün kiralaması işlenirken, bölüme karşılık gelen kiralama silinemeyebiliyor. Sorun bu sürümde düzeltildi.

### <a name="221"></a><a id="2.2.1"></a>2.2.1
* Birden çok yazma bölgesi olan ve yeni Oturum Belirteci biçimine sahip hesaplar için Tahmin Aracı hesaplaması düzeltildi.

### <a name="220"></a><a id="2.2.0"></a>2.2.0
* Bölümlenmiş kira koleksiyonları için destek eklendi. Bölüm anahtarı /id olarak tanımlanmalıdır.
* Hataya neden olan küçük değişiklik: IChangeFeedDocumentClient arabiriminin ve ChangeFeedDocumentClient sınıfının yöntemleri RequestOptions ve CancellationToken parametrelerini içerecek şekilde değiştirildi. IChangeFeedDocumentClient gelişmiş bir genişletilebilirlik noktasıdır. Değişiklik Akışı İşlemcisiyle kullanmak üzere (örneğin DocumentClient'ı dekore etmek ve fazladan izleme, hata işleme vb. işlemler yapmak üzere ona yönelik tüm çağrıları kesmek için) Belge İstemcisinin özel uygulamasını sağlar. Bu güncelleştirmeyle IChangeFeedDocumentClient'ı uygulayan kodun uygulamada yeni parametreleri içerecek şekilde değiştirilmesi gerekir.
* Küçük tanılama geliştirmeleri.

### <a name="210"></a><a id="2.1.0"></a>2.1.0
* Yeni Task&lt;IReadOnlyList&lt;RemainingPartitionWork&gt;&gt; IRemainingWorkEstimator.GetEstimatedRemainingWorkPerPartitionAsync() API'si eklendi. Bu, her bölümün tahmini çalışmasını almak için kullanılabilir.
* Microsoft.Azure.DocumentDB SDK 2.0'ı destekler. Microsoft.Azure.DocumentDB 2.0 veya üzerini gerektirir.

### <a name="206"></a><a id="2.0.6"></a>2.0.6
* v1 ile uyumluluk için ChangeFeedEventHost.HostName ortak özelliği eklendi.

### <a name="205"></a><a id="2.0.5"></a>2.0.5
* Bölümü bölme işlemi sırasında oluşan yarış durumu düzeltildi. Yarış durumu kiranın alınmasına ve bölümü bölme işlemi sırasında hemen kaybedilmesine yol açarak çekişmeye neden olabiliyor. Yarış durumu sorunu bu sürümde düzeltildi.

### <a name="204"></a><a id="2.0.4"></a>2.0.4
* GA SDK

### <a name="203-prerelease"></a><a id="2.0.3-prerelease"></a>2.0.3 ön sürümü
* Aşağıdaki sorunlar çözülmüştür:
  * Bölümü bölme işlemi gerçekleştiğinde, bölme işlemi öncesinde değiştirilen belgelerde yinelenen işleme ortaya çıkabiliyordu.
  * Kira koleksiyonunda hiç kira olmadığında GetEstimatedRemainingWork API'si 0 döndürüyordu.

* Aşağıdaki özel durumlar genel kullanıma sunuldu. IPartitionProcessor gerçekleştirilen uzantılar bu özel durumları oluşturabilir.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.LeaseLostException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionNotFoundException.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionSplitException. 

### <a name="202-prerelease"></a><a id="2.0.2-prerelease"></a>2.0.2 ön sürümü
* Küçük API değişiklikleri:
  * Kullanımdan kaldırıldı olarak işaretlenmiş olan ChangeFeedProcessorOptions.IsAutoCheckpointEnabled kaldırıldı.

### <a name="201-prerelease"></a><a id="2.0.1-prerelease"></a>2.0.1 ön sürümü
* Kararlılık geliştirmeleri:
  * Kira deposu başlatma işlemi daha iyi işleniyor. Kira deposu boş olduğunda işlemcinin tek bir örneği depoyu başlatabilir ve diğerleri bekler.
  * Daha kararlı/verimli kira yenileme/yayınlama. Bir bölümde kirayı yenileme ve yayınlama, diğerlerini yenilemekten bağımsızdır. v1'de bu işlem tüm bölümler için sıralı olarak yapılıyordu.
* v2 API'sindeki yenilikler:
  * İşlemcinin esnek yapısı için oluşturucu deseni: ChangeFeedProcessorBuilder sınıfı.
    * Parametrelerin herhangi bir bileşimini alabilir.
    * İzleme ve/veya kira koleksiyonu için DocumentClient örneğini alabilir (v1'de kullanılamaz).
  * IChangeFeedObserver.ProcessChangesAsync şimdi CancellationToken alıyor.
  * IRemainingWorkEstimator - Kalan iş tahmin aracı işlemciden ayrı olarak kullanılabilir.
  * Yeni genişletilebilirlik noktaları:
    * IPartitionLoadBalancingStrategy - işlemcinin örnekleri arasında bölümlerin özel yük dengelemesi için.
    * ILease, ILeaseManager - özel kira yönetimi için.
    * IPartitionProcessor - bölümdeki özel işleme değişiklikleri için.
* Logging - [LibLog](https://github.com/damianh/LibLog) kitaplığını kullanır.
* Geriye dönük olarak v1 API ile %100 uyumludur.
* Yeni kod tabanı.
* [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.21.1 ve üzeri sürümleriyle uyumludur.

### <a name="v1-builds"></a>v1 derlemeleri

### <a name="133"></a><a id="1.3.3"></a>1.3.3
* Daha fazla günlük eklendi.
* Bekleyen iş tahmini birden çok kez çağrıldığında oluşan DocumentClient sızıntısı düzeltildi.

### <a name="132"></a><a id="1.3.2"></a>1.3.2
* Bekleyen iş tahminindeki düzeltmeler.

### <a name="131"></a><a id="1.3.1"></a>1.3.1
* Kararlılık geliştirmeleri.
  * Bazı bölümlerde gözlemcilerin durmasına yol açan iptal edilmiş görevleri işleme sorunu düzeltildi.
* El ile denetim noktası oluşturma desteği.
* [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.21 ve üzeri sürümleriyle uyumludur.

### <a name="120"></a><a id="1.2.0"></a>1.2.0
* .NET Standard 2.0 için destek eklendi. Paket artık `netstandard2.0` ve `net451` çerçevesi bilinen adlarını destekliyor.
* [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.17.0 ve üzeri sürümleriyle uyumludur.
* [SQL .NET Core SDK](sql-api-sdk-dotnet-core.md) 1.5.1 ve üzeri sürümleriyle uyumludur.

### <a name="111"></a><a id="1.1.1"></a>1.1.1
* Değişiklik Akışı boş olduğunda veya bekleyen iş olmadığında, kalan iş tahmini hesaplamasındaki sorun düzeltildi.
* [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.13.2 ve üzeri sürümleriyle uyumludur.

### <a name="110"></a><a id="1.1.0"></a>1.1.0
* Değişiklik Akışında işlenecek kalan iş tahminini alma yöntemi eklendi.
* [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.13.2 ve üzeri sürümleriyle uyumludur.

### <a name="100"></a><a id="1.0.0"></a>1.0.0
* GA SDK
* [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.14.1 ve öncesi sürümleriyle uyumludur.

## <a name="release--retirement-dates"></a>Yayın ve Kullanımdan Kaldırma tarihleri

Microsoft daha yeni/desteklenen bir sürüme geçişi kolaylaştırmak için bir SDK'yı kullanımdan kaldırmadan en az **12 ay** önce bildirim sağlar. Yeni özellikler, işlevler ve iyileştirmeler yalnızca geçerli SDK'ya eklendiğinden, her zaman mümkün olan en kısa zamanda, en son SDK sürümüne yükseltmeniz önerilir.

> [!WARNING]
> 31 Ağustos 2022'den sonra Azure Cosmos DB artık Azure Cosmos DB .NET veya SQL API'si için .NET Core SDK'sının 1.x sürümlerinde hata düzeltmeleri yapmayacak, yeni özellikler eklemeyecek ve destek sağlamayacaktır. Yükseltmemeyi tercih ederseniz, SDK'nın 1.x sürümünden gönderilen isteklere Azure Cosmos DB hizmeti tarafından yanıt verilmeye devam edilecektir.

<br/>

| Sürüm | Yayın Tarihi | Kullanımdan Kaldırma Tarihi |
| --- | --- | --- |
| [2.3.2](#2.3.2) |11 Ağustos 2020 |--- |
| [2.3.1](#2.3.1) |30 Temmuz 2020 |--- |
| [2.3.0](#2.3.0) |2 Nisan 2020 |--- |
| [2.2.8](#2.2.8) |28 Ekim 2019 |--- |
| [2.2.7](#2.2.7) |14 Mayıs 2019 |--- |
| [2.2.6](#2.2.6) |29 Ocak 2019 |--- |
| [2.2.5](#2.2.5) |13 Aralık 2018 |--- |
| [2.2.4](#2.2.4) |29 Kasım 2018 |--- |
| [2.2.3](#2.2.3) |19 Kasım 2018 |--- |
| [2.2.2](#2.2.2) |31 Ekim 2018 |--- |
| [2.2.1](#2.2.1) |24 Ekim 2018 |--- |
| [1.3.3](#1.3.3) |08 Mayıs 2018 |--- |
| [1.3.2](#1.3.2) |18 Nisan 2018 |--- |
| [1.3.1](#1.3.1) |13 Mart 2018 |--- |
| [1.2.0](#1.2.0) |31 Ekim 2017 |--- |
| [1.1.1](#1.1.1) |29 Ağustos 2017 |--- |
| [1.1.0](#1.1.0) |13 Ağustos 2017 |--- |
| [1.0.0](#1.0.0) |07 Temmuz 2017 |--- |

## <a name="faq"></a>SSS

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Ayrıca bkz.

Cosmos DB hakkında daha fazla bilgi edinmek için [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) hizmeti sayfasına bakın.
