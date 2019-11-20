---
title: Azure Service Bus kullanarak performansı iyileştirmeye yönelik en iyi uygulamalar | Microsoft Docs
description: Aracılı iletileri değiş tokuşu yaparken performansı iyileştirmek için Service Bus nasıl kullanılacağını açıklar.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 09/14/2018
ms.author: aschhab
ms.openlocfilehash: 3d2d26e8cb8a3b1ee7720424aea701ca063ecc9f
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596452"
---
# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>Service Bus mesajlaşma kullanarak performans iyileştirmeleri için en iyi uygulamalar

Bu makalede, aracılı iletileri değiş tokuşu yaparken performansı iyileştirmek için Azure Service Bus nasıl kullanılacağı açıklanır. Bu makalenin ilk bölümünde, performansı artırmaya yardımcı olmak için sunulan farklı mekanizmalar açıklanmaktadır. İkinci bölüm Service Bus, belirli bir senaryoda en iyi performansı sunabileceği şekilde nasıl kullanılacağına ilişkin yönergeler sağlar.

Bu makale boyunca, "Client" terimi Service Bus erişen herhangi bir varlığa başvurur. Bir istemci, gönderenin veya alıcının rolünü alabilir. "Gönderici" terimi, bir Service Bus kuyruğuna veya konu aboneliğine ileti gönderen Service Bus kuyruğu veya konu istemcisi için kullanılır. "Alıcı" terimi, bir Service Bus kuyruğundan veya aboneliğinden iletiler alan bir Service Bus kuyruğu veya abonelik istemcisine başvurur.

Bu bölümlerde, Service Bus performansı artırmak için tarafından kullanılan çeşitli kavramlar tanıtılmaktadır.

## <a name="protocols"></a>Protokoller

Service Bus, istemcilerin üç protokolden birini kullanarak ileti göndermesini ve almasını sağlar:

1. Gelişmiş İleti Sıraya Alma Protokolü (AMQP)
2. Service Bus mesajlaşma Protokolü (SBMP)
3. HTTP

AMQP ve SBMP daha verimlidir çünkü ileti fabrikası, mevcut olduğu sürece Service Bus bağlantısını koruduklarında. Toplu işleme ve önceden getirme de uygular. Açıkça belirtilmedikçe, bu makaledeki tüm içerikler AMQP veya SBMP kullanımını varsayar.

## <a name="reusing-factories-and-clients"></a>Fabrikaları ve istemcileri yeniden kullanma

[Queueclient][QueueClient] veya [iletiör][MessageSender]gibi istemci nesneleri Service Bus, ayrıca bağlantıların Iç yönetimini sağlayan bir [messagingfactory][MessagingFactory] nesnesi aracılığıyla oluşturulur. İleti gönderdikten sonra ileti fabrikalarını veya kuyruğu, konuyu ve abonelik istemcilerini kapatmamalıdır ve sonraki iletiyi gönderdiğinizde yeniden oluşturmanız önerilir. Bir mesajlaşma fabrikasının kapatılması Service Bus hizmetine olan bağlantıyı siler ve fabrikası yeniden oluştururken yeni bir bağlantı oluşturulur. Bir bağlantı kurmak, birden çok işlem için aynı fabrika ve istemci nesnelerini yeniden kullandığınızda kaçınmanın pahalı bir işlemdir. Bu istemci nesnelerini, eşzamanlı zaman uyumsuz işlemler ve birden çok iş parçacığından güvenle kullanabilirsiniz. 

## <a name="concurrent-operations"></a>Eş zamanlı işlemler

Bir işlem (gönderme, alma, silme, vb.) gerçekleştirmek biraz zaman alır. Bu süre, isteğin ve yanıtın gecikme süresinin yanı sıra Service Bus hizmeti tarafından işlemin işlenmesini içerir. Zaman başına işlem sayısını artırmak için, işlemlerin eşzamanlı olarak yürütülmesi gerekir. 

İstemci zaman uyumsuz işlemler gerçekleştirerek eşzamanlı işlemleri zamanlar. Sonraki istek, önceki istek tamamlanmadan önce başlatılır. Aşağıdaki kod parçacığı, zaman uyumsuz gönderme işlemine bir örnektir:
  
 ```csharp
  Message m1 = new BrokeredMessage(body);
  Message m2 = new BrokeredMessage(body);
  
  Task send1 = queueClient.SendAsync(m1).ContinueWith((t) => 
    {
      Console.WriteLine("Sent message #1");
    });
  Task send2 = queueClient.SendAsync(m2).ContinueWith((t) => 
    {
      Console.WriteLine("Sent message #2");
    });
  Task.WaitAll(send1, send2);
  Console.WriteLine("All messages sent");
  ```
  
  Aşağıdaki kod, zaman uyumsuz alma işlemine bir örnektir. Tam programı [buradan](https://github.com/Azure/azure-service-bus/blob/master/samples/DotNet/Microsoft.Azure.ServiceBus/SendersReceiversWithQueues)görebilirsiniz:
  
  ```csharp
  var receiver = new MessageReceiver(connectionString, queueName, ReceiveMode.PeekLock);
  var doneReceiving = new TaskCompletionSource<bool>();

  receiver.RegisterMessageHandler(...);
  ```

## <a name="receive-mode"></a>Alma modu

Bir kuyruk veya abonelik istemcisi oluştururken, bir alma modu belirtebilirsiniz: *Peek-kilitle* veya *al ve Sil*. Varsayılan alma modu [PeekLock][PeekLock]' dir. Bu modda çalışırken, istemci Service Bus ileti alma isteği gönderir. İstemci iletiyi aldıktan sonra iletiyi tamamlamaya yönelik bir istek gönderir.

Alma modunu [Receiveanddelete][ReceiveAndDelete]olarak ayarlarken her iki adım tek bir istekte birleştirilir. Bu adımlar genel işlem sayısını azaltır ve genel ileti işleme hızını iyileştirebilir. Bu performans kazancı, iletileri kaybetme riskiyle gelir.

Service Bus, alma ve silme işlemlerine yönelik işlemleri desteklemez. Buna ek olarak, istemcinin bir iletiyi erteleme veya [atılacak](service-bus-dead-letter-queues.md) bir ileti almak istediği her senaryo için Peek kilit semantiği gerekir.

## <a name="client-side-batching"></a>İstemci tarafı toplu işleme

İstemci tarafı toplu işleme, bir kuyruk veya konu istemcisinin belirli bir süre için ileti gönderilmesini geciktirmesini sağlar. İstemci bu süre içinde başka iletiler gönderirse, iletileri tek bir toplu iş olarak gönderir. İstemci tarafı toplu işleme Ayrıca bir kuyruk veya abonelik istemcisinin birden çok **tamamlanmış** isteği tek bir istek halinde toplu olarak yığın oluşturmasına neden olur. Toplu işleme yalnızca zaman uyumsuz **gönderme** ve **Tamamlanan** işlemler için kullanılabilir. Zaman uyumlu işlemler hemen Service Bus hizmetine gönderilir. Toplu işleme, göz atma veya alma işlemleri veya istemciler arasında toplu işlem gerçekleşmiyor.

Varsayılan olarak, bir istemci 20 MS toplu iş aralığı kullanır. İleti fabrikası oluşturmadan önce [Batchflushınterval][BatchFlushInterval] özelliğini ayarlayarak Batch aralığını değiştirebilirsiniz. Bu ayar, bu fabrika tarafından oluşturulan tüm istemcileri etkiler.

Toplu işlemeyi devre dışı bırakmak için [Batchflushınterval][BatchFlushInterval] özelliğini **TimeSpan. Zero**olarak ayarlayın. Örnek:

```csharp
MessagingFactorySettings mfs = new MessagingFactorySettings();
mfs.TokenProvider = tokenProvider;
mfs.NetMessagingTransportSettings.BatchFlushInterval = TimeSpan.FromSeconds(0.05);
MessagingFactory messagingFactory = MessagingFactory.Create(namespaceUri, mfs);
```

Toplu işleme, faturalandırılabilir mesajlaşma işlemlerinin sayısını etkilemez ve yalnızca [Microsoft. ServiceBus. Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) kitaplığını kullanan Service Bus istemci protokolü için kullanılabilir. HTTP protokolü toplu işlemeyi desteklemez.

> [!NOTE]
> Batchflushınterval ayarı, toplu işleme uygulamanın perspektifinden örtük olmasını sağlar. Yani, uygulama Sendadsync () ve tamamlanabilen eşitleme () çağrılarını yapar ve belirli toplu çağrı yapmaz.
>
> Açık istemci tarafı toplu işleme, aşağıdaki yöntem çağrısının kullanılmasıyla uygulanabilir- 
> ```csharp
> Task SendBatchAsync (IEnumerable<BrokeredMessage> messages);
> ```
> Burada iletilerin Birleşik boyutunun fiyatlandırma katmanının desteklediği en büyük boyuttan küçük olması gerekir.

## <a name="batching-store-access"></a>Depo erişimini toplu işleme

Bir kuyruğun, konunun veya aboneliğin aktarım hızını artırmak için Service Bus, iç deposuna yazarken birden çok ileti işler. Bir kuyruk veya konu üzerinde etkinleştirilirse, depoya ileti yazmak toplu olarak oluşturulur. Bir kuyrukta veya abonelikte etkinleştirilirse, depodan ileti silme toplu olur. Bir varlık için toplu depolama erişimi etkinleştirilmişse Service Bus, bu varlıkla ilgili bir mağaza yazma işlemini 20 MS 'ye kadar geciktirir. 

> [!NOTE]
> 20 MS toplu işlem aralığının sonunda Service Bus hatası olsa bile, toplu işleme ile iletileri kaybetme riski yoktur. 

Bu Aralık süresince gerçekleşen ek mağaza işlemleri toplu işe eklenir. Toplu depolama erişimi yalnızca **gönderme** ve **tamamlanma** işlemlerini etkiler; alma işlemleri etkilenmez. Toplu depo erişimi, bir varlık üzerindeki bir özelliktir. Toplu işleme, toplu depo erişimini etkinleştiren tüm varlıklarda oluşur.

Yeni bir kuyruk, konu veya abonelik oluştururken, toplu mağaza erişimi varsayılan olarak etkindir. Toplu depo erişimini devre dışı bırakmak için, varlığı oluşturmadan önce [Enablebatchedoperations][EnableBatchedOperations] özelliğini **false** olarak ayarlayın. Örnek:

```csharp
QueueDescription qd = new QueueDescription();
qd.EnableBatchedOperations = false;
Queue q = namespaceManager.CreateQueue(qd);
```

Toplu depolama erişimi, faturalandırılabilir mesajlaşma işlemlerinin sayısını etkilemez ve bir kuyruk, konu veya abonelik özelliğidir. Alma modundan ve bir istemci ile Service Bus hizmeti arasında kullanılan protokolden bağımsızdır.

## <a name="prefetching"></a>Önceden getiriliyor

[Önceden getirme](service-bus-prefetch.md) , kuyruk veya abonelik istemcisinin alma işlemi gerçekleştirdiğinde hizmetten ek iletiler yüklemesine olanak sağlar. İstemci bu iletileri yerel bir önbellekte depolar. Önbelleğin boyutu [Queueclient. prefetchcount][QueueClient.PrefetchCount] veya [Subscriptionclient. prefetchcount][SubscriptionClient.PrefetchCount] özellikleri tarafından belirlenir. Ön getirmeyi sağlayan her istemci kendi önbelleğini korur. Bir önbellek istemciler arasında paylaşılmaz. İstemci bir alma işlemi başlatırsa ve önbelleği boşsa, hizmet bir toplu ileti iletir. Toplu iş boyutu, Önbelleğin boyutuna veya 256 KB değerine eşitse, hangisi daha küçüktür. İstemci bir alma işlemi başlatırsa ve önbellek bir ileti içeriyorsa, ileti önbellekten alınır.

Bir ileti önceden getirilme durumunda hizmet, önceden getirilen iletiyi kilitler. Kilit ile, önceden getirilen ileti farklı bir alıcı tarafından alınamaz. Alıcı, kilidin süresi dolmadan önce iletiyi tamamlayamadığında ileti diğer alıcılar tarafından kullanılabilir hale gelir. İletinin önceden getirilen kopyası önbellekte kalır. Önbelleğe alınmış önbelleğe alınmış kopyayı tüketen alıcı, iletiyi tamamlamaya çalıştığında bir özel durum alır. Varsayılan olarak, ileti kilidinin 60 saniye sonra süresi dolar. Bu değer 5 dakikaya genişletilebilir. Süresi dolma iletilerinin kullanımını engellemek için, önbellek boyutu her zaman bir istemci tarafından kilit zaman aşımı aralığı içinde tüketilen ileti sayısından daha küçük olmalıdır.

60 saniyelik varsayılan kilit süre sonunu kullanırken, [Prefetchcount][SubscriptionClient.PrefetchCount] için iyi bir değer, fabrika 'nin tüm alıcılarının maksimum işlem hızlarının 20 katı olur. Örneğin, bir fabrika üç alıcı oluşturuyor ve her alıcı saniyede en fazla 10 ileti işleyebilir. Önceden getirme sayısı 20 X 3 X 10 = 600 değerini aşmamalıdır. Varsayılan olarak, [Prefetchcount][QueueClient.PrefetchCount] 0 olarak ayarlanır; Bu, hizmetten başka hiçbir ileti getirilmediği anlamına gelir.

İletileri önceden getirme, ileti işlemlerinin genel sayısını veya gidiş dönüş sayısını azalttığından bir sıranın veya aboneliğin genel aktarım hızını artırır. Ancak ilk ileti getirilirken, daha uzun sürer (artan ileti boyutu nedeniyle). Bu iletiler istemci tarafından zaten indirildiğinden, önceden getirilen iletilerin alınması daha hızlı olacaktır.

Bir iletinin yaşam süresi (TTL) özelliği, sunucunun istemciye ileti göndermesi sırasında sunucu tarafından denetlenir. İleti alındığında istemci iletinin TTL özelliğini denetlemez. Bunun yerine ileti, ileti istemci tarafından önbelleğe alındıktan sonra iletinin TTL 'SI geçtiğinde bile alınabilir.

Önceden getirme, faturalandırılabilir mesajlaşma işlemlerinin sayısını etkilemez ve yalnızca Service Bus istemci protokolü için kullanılabilir. HTTP protokolü, ön getirmeyi desteklemez. Önceden getirme, hem zaman uyumlu hem de zaman uyumsuz alma işlemleri için kullanılabilir.

## <a name="prefetching-and-receivebatch"></a>Ön getirme ve ReceiveBatch

Birden çok iletiyi önceden getirme kavramlarının bir toplu işte (ReceiveBatch) iletileri işlemeye benzer semantiklerine sahip olsa da, bunları birlikte kullanırken göz önünde bulundurmanız gereken bazı küçük farklılıklar vardır.

Önceden getirme, istemcideki (QueueClient ve SubscriptionClient) bir yapılandırmadır (veya mod) ve ReceiveBatch bir işlemdir (istek-yanıt semantiğini içeren).

Bunları birlikte kullanırken, aşağıdaki durumları göz önünde bulundurun-

* Önceden getirme, ReceiveBatch 'ten almayı beklediğiniz ileti sayısından büyük veya buna eşit olmalıdır.
* Önceden getirme, saniye başına işlenen ileti sayısının en fazla n/3 katı olabilir, burada n varsayılan kilit süresi olur.

Bir doyumsuz yaklaşımı (yani, önceden getirme sayısının çok yüksek tutulması) sayesinde, iletinin belirli bir alıcıya kilitli olduğunu gösterdiği için bazı sorunlar vardır. Bu öneri, yukarıda bahsedilen eşikler arasında önceden getirme değerlerini denemek ve neyin uygun olduğunu belirlemektir.

## <a name="multiple-queues"></a>Birden çok kuyruk

Beklenen yükleme tek bir sıra veya konu tarafından işlenemiyorsa, birden çok mesajlaşma varlığı kullanmanız gerekir. Birden çok varlık kullanırken, tüm varlıklar için aynı istemciyi kullanmak yerine, her varlık için ayrılmış bir istemci oluşturun.

## <a name="development-and-testing-features"></a>Geliştirme ve test özellikleri

Service Bus, özellikle geliştirme için kullanılan ve **Üretim yapılandırmalarında hiçbir daha kullanılmamalıdır**: [topicdescription. Enablefilteringiletibefoyeniden yayımlama][]için kullanılan bir özelliğe sahiptir.

Konuya yeni kurallar veya filtreler eklendiğinde, yeni filtre ifadesinin beklendiği gibi çalıştığını doğrulamak için [Topicdescription. Enablefilteringiletibefoyeniden yayımlama][] kullanabilirsiniz.

## <a name="scenarios"></a>Senaryolar

Aşağıdaki bölümlerde tipik mesajlaşma senaryoları açıklanır ve tercih edilen Service Bus ayarları ana hatlarıyla verilmiştir. Aktarım hızı ücretleri küçük (1 ileti/saniye), Orta (1 ileti/saniye veya daha fazla, 100 ileti/saniye) ve yüksek (100 mesaj/saniye veya daha büyük) olarak sınıflandırılır. İstemci sayısı küçük (5 veya daha az), Orta (5 ' ten fazla veya daha az) ve büyük (20 ' den fazla) olarak sınıflandırılır.

### <a name="high-throughput-queue"></a>Yüksek aktarım hızı kuyruğu

Hedef: tek bir sıranın verimini en üst düzeye çıkarın. Gönderenlerin ve alıcıların sayısı küçüktür.

* Genel gönderme oranını sıraya göre artırmak için, göndericiler oluşturmak üzere birden çok ileti fabrikası kullanın. Her gönderici için zaman uyumsuz işlemler veya birden çok iş parçacığı kullanın.
* Kuyruktaki genel alma hızını artırmak için, alıcı oluşturmak üzere birden çok ileti fabrikası kullanın.
* İstemci tarafı toplu işleme avantajlarından yararlanmak için zaman uyumsuz işlemler kullanın.
* Service Bus istemci protokol iletimi sayısını azaltmak için toplu işlem aralığını 50 ms olarak ayarlayın. Birden çok gönderici kullanılıyorsa, toplu işlem aralığını 100 ms olarak artırın.
* Toplu mağaza erişimini devre dışı bırakın. Bu erişim, iletilerin sıraya yazılabileceği genel oranı artırır.
* Önceden getirme sayısını, bir fabrika 'nin tüm alıcılarının maksimum işlem hızlarına 20 kez ayarlayın. Bu sayı, Service Bus istemci protokol iletimi sayısını azaltır.

### <a name="multiple-high-throughput-queues"></a>Birden çok yüksek işleme kuyruğu

Hedef: birden çok sıranın genel verimini en üst düzeye çıkarın. Tek bir kuyruğun üretilen işi orta veya yüksek.

Birden çok sırada en yüksek aktarım hızını elde etmek için, tek bir sıranın verimini en üst düzeye çıkarmak üzere özetlenen ayarları kullanın. Ayrıca, farklı kuyruklardan gönderen veya alan istemcileri oluşturmak için farklı fabrikalar kullanın.

### <a name="low-latency-queue"></a>Düşük gecikme süresi kuyruğu

Hedef: bir kuyruğun veya konunun uçtan uca gecikmesini en aza Indirir. Gönderenlerin ve alıcıların sayısı küçüktür. Kuyruğun üretilen işi küçük veya orta.

* İstemci tarafı toplu işlemeyi devre dışı bırakın. İstemci hemen bir ileti gönderir.
* Toplu depo erişimini devre dışı bırakın. Hizmet iletiyi depoya hemen yazar.
* Tek bir istemci kullanıyorsanız, önceden getirme sayısını alıcının işleme hızının 20 katı olarak ayarlayın. Aynı anda sıraya birden çok ileti ulaştığında, Service Bus istemci protokolü bunları aynı anda iletir. İstemci bir sonraki iletiyi aldığında, bu ileti zaten yerel önbellekte bulunur. Önbelleğin küçük olması gerekir.
* Birden çok istemci kullanılıyorsa, önceden getirme sayısını 0 olarak ayarlayın. Sayıyı ayarlayarak ikinci istemci ilk iletiyi hala işlerken ikinci iletiyi alabilir.

### <a name="queue-with-a-large-number-of-senders"></a>Çok sayıda gönderici içeren kuyruk

Hedef: bir kuyruğun veya konunun iş verimini çok sayıda gönderen ile en üst düzeye çıkarın. Her gönderici, bir orta oranına sahip iletiler gönderir. Alıcı sayısı küçüktür.

Service Bus, bir mesajlaşma varlığına en fazla 1000 eşzamanlı bağlantı sağlar (veya AMQP kullanarak 5000). Bu sınır ad alanı düzeyinde zorlanır ve kuyruklar/konular/abonelikler ad alanı başına eşzamanlı bağlantı sınırına göre belirlenir. Kuyruklar için bu sayı, Gönderenler ve alıcılar arasında paylaşılır. Göndericiler için tüm 1000 bağlantıları gerekliyse, kuyruğu bir konuyla ve tek bir abonelikle değiştirin. Bir konu, gönderenlerden en fazla 1000 eşzamanlı bağlantı kabul eder, ancak abonelik alıcıların ek 1000 eşzamanlı bağlantılarını kabul eder. 1000 'den fazla eşzamanlı gönderici gerekliyse, göndericiler HTTP aracılığıyla Service Bus protokolüne ileti göndermelidir.

Aktarım hızını en üst düzeye çıkarmak için aşağıdaki adımları gerçekleştirin:

* Her Gönderici farklı bir işlemde yer alıyorsa, işlem başına yalnızca tek bir fabrika kullanın.
* İstemci tarafı toplu işleme avantajlarından yararlanmak için zaman uyumsuz işlemler kullanın.
* Service Bus istemci protokol iletimi sayısını azaltmak için 20 MS varsayılan toplu işlem aralığını kullanın.
* Toplu mağaza erişimini devre dışı bırakın. Bu erişim, iletilerin kuyruğa veya konuya yazıtırabileceği genel oranı artırır.
* Önceden getirme sayısını, bir fabrika 'nin tüm alıcılarının maksimum işlem hızlarına 20 kez ayarlayın. Bu sayı, Service Bus istemci protokol iletimi sayısını azaltır.

### <a name="queue-with-a-large-number-of-receivers"></a>Çok sayıda alıcı içeren kuyruk

Hedef: çok sayıda alıcıya sahip bir kuyruğun veya aboneliğin alma oranını en üst düzeye çıkarın. Her alıcı, iletileri orta bir hızda alır. Gönderenlerin sayısı küçüktür.

Service Bus, bir varlığa en fazla 1000 eşzamanlı bağlantı sağlar. Bir kuyruk 1000 'den fazla alıcı gerektiriyorsa, kuyruğu bir konuyla ve birden çok aboneliğe göre değiştirin. Her abonelik 1000 adede kadar eşzamanlı bağlantıyı destekleyebilir. Alternatif olarak, alıcılar kuyruğa HTTP protokolü aracılığıyla erişebilir.

Aktarım hızını en üst düzeye çıkarmak için şunları yapın:

* Her alıcı farklı bir işlemde yer alıyorsa, işlem başına yalnızca tek bir fabrika kullanın.
* Alıcılar, zaman uyumlu veya zaman uyumsuz işlemler kullanabilir. Tek bir alıcının orta alım oranı verildiğinde, tam bir isteğin istemci tarafında toplu işleme alıcı aktarım hızını etkilemez.
* Toplu mağaza erişimini devre dışı bırakın. Bu erişim, varlığın genel yükünü azaltır. Ayrıca, iletilerin kuyruğa veya konuya yazılma hızının genel oranını azaltır.
* Önceden getirme sayısını küçük bir değere ayarlayın (örneğin, PrefetchCount = 10). Bu sayı, diğer alıcıların önbelleğe alınmış çok sayıda iletisi olduğunda alıcıların boşta kalmasını engeller.

### <a name="topic-with-a-small-number-of-subscriptions"></a>Az sayıda aboneliğe sahip konu

Hedef: az sayıda aboneliğe sahip bir konunun verimini en üst düzeye çıkarın. Birçok abonelik tarafından bir ileti alınır; bu, tüm aboneliklerdeki Birleşik Alım oranının gönderme hızından büyük olduğu anlamına gelir. Gönderenlerin sayısı küçüktür. Abonelik başına alıcı sayısı küçüktür.

Aktarım hızını en üst düzeye çıkarmak için şunları yapın:

* Genel gönderme oranını konuya yükseltmek için, gönderici oluşturmak üzere birden çok ileti fabrikası kullanın. Her gönderici için zaman uyumsuz işlemler veya birden çok iş parçacığı kullanın.
* Bir abonelikteki genel alma hızını artırmak için, alıcılar oluşturmak üzere birden çok ileti fabrikası kullanın. Her alıcı için zaman uyumsuz işlemler veya birden çok iş parçacığı kullanın.
* İstemci tarafı toplu işleme avantajlarından yararlanmak için zaman uyumsuz işlemler kullanın.
* Service Bus istemci protokol iletimi sayısını azaltmak için 20 MS varsayılan toplu işlem aralığını kullanın.
* Toplu mağaza erişimini devre dışı bırakın. Bu erişim, iletilerin konuya yazıtırabileceği genel oranı artırır.
* Önceden getirme sayısını, bir fabrika 'nin tüm alıcılarının maksimum işlem hızlarına 20 kez ayarlayın. Bu sayı, Service Bus istemci protokol iletimi sayısını azaltır.

### <a name="topic-with-a-large-number-of-subscriptions"></a>Çok sayıda aboneliğe sahip konu

Hedef: çok sayıda aboneliğe sahip bir konunun verimini en üst düzeye çıkarın. Birçok abonelik tarafından bir ileti alınır. Bu, tüm aboneliklerdeki Birleşik Alım oranının gönderme hızından çok daha büyük olduğu anlamına gelir. Gönderenlerin sayısı küçüktür. Abonelik başına alıcı sayısı küçüktür.

Çok sayıda aboneliğe sahip olan konular genellikle tüm iletiler tüm aboneliklere yönlendirilse genel olarak düşük bir verimlilik sunar. Bu düşük aktarım hızı, her bir iletinin birçok kez alınması nedeniyle oluşur ve bir konu başlığında bulunan tüm iletiler ve tüm abonelikleri aynı depoda depolanır. Abonelik başına gönderen ve alıcı sayısının küçük olduğu varsayılır. Service Bus, konu başına en fazla 2.000 aboneliği destekler.

Aktarım hızını en üst düzeye çıkarmak için aşağıdaki adımları deneyin:

* İstemci tarafı toplu işleme avantajlarından yararlanmak için zaman uyumsuz işlemler kullanın.
* Service Bus istemci protokol iletimi sayısını azaltmak için 20 MS varsayılan toplu işlem aralığını kullanın.
* Toplu mağaza erişimini devre dışı bırakın. Bu erişim, iletilerin konuya yazıtırabileceği genel oranı artırır.
* Ön hazırlık sayısını saniye cinsinden beklenen alma hızının 20 katı olarak ayarlayın. Bu sayı, Service Bus istemci protokol iletimi sayısını azaltır.

[QueueClient]: /dotnet/api/microsoft.azure.servicebus.queueclient
[MessageSender]: /dotnet/api/microsoft.azure.servicebus.core.messagesender
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[PeekLock]: /dotnet/api/microsoft.azure.servicebus.receivemode
[ReceiveAndDelete]: /dotnet/api/microsoft.azure.servicebus.receivemode
[BatchFlushInterval]: /dotnet/api/microsoft.servicebus.messaging.messagesender.batchflushinterval
[EnableBatchedOperations]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablebatchedoperations
[QueueClient.PrefetchCount]: /dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount
[SubscriptionClient.PrefetchCount]: /dotnet/api/microsoft.azure.servicebus.subscriptionclient.prefetchcount
[ForcePersistence]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage.forcepersistence
[EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[Partitioned messaging entities]: service-bus-partitioning.md
[TopicDescription. Enablefilteringiletibefoyeniden yayımlama]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablefilteringmessagesbeforepublishing
