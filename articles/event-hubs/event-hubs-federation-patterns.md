---
title: Olay çoğaltma görev desenleri-Azure Event Hubs | Microsoft Docs
description: Bu makale, belirli olay çoğaltma görevi desenlerini uygulamaya yönelik ayrıntılı kılavuz sağlar
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: 438964c228f060dede93abf582c9504b698db8b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97934620"
---
# <a name="event-replication-tasks-patterns"></a>Olay çoğaltma görevleri desenleri

[Federasyona genel bakış](event-hubs-federation-overview.md) ve [çoğaltıcı işlevleri genel bakış](event-hubs-federation-replicator-functions.md) ve çoğaltma görevlerinin temel öğeleri hakkında genel bakış ve bu makaleye devam etmeden önce bunları öğrenmeniz önerilir.

Bu makalede genel bakış bölümünde vurgulanan desenlerin birkaçı için uygulama kılavuzumuzu inceleyeceğiz.

## <a name="replication"></a>Çoğaltma

Çoğaltma deseninin olayları bir olay hub 'ından sonrakine veya bir olay hub 'ından Service Bus kuyruğu gibi başka bir hedefe kopyalar. Olaylar, olay yükünde herhangi bir değişiklik yapılmadan iletilir.

Bu düzenin uygulanması, [Event Hubs ve Service Bus örnekleri arasındaki Event Hubs ve olay çoğaltması](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus) [arasında olay çoğaltma](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy) ve Event Hubs bir aracıdan Apache Kafka ' a veri çoğaltmanın belirli bir durumu için Event Hubs [öğreticisiyle birlikte Apache Kafka mirrormaker](event-hubs-kafka-mirror-maker-tutorial.md) 'ın kapsamına giren bir durumdur.

### <a name="streams-and-order-preservation"></a>Akışlar ve sipariş koruması

Çoğaltma, Azure Işlevleri veya Azure Stream Analytics aracılığıyla bir kaynak olay hub 'ının tam 1:1 klonlarının bir hedef olay hub 'ına oluşturulmasını güvence altına almaz, ancak uygulamanın gerektirdiği olayların göreli sırasını korumak için odaklanır. Uygulama, aynı bölüm anahtarıyla ilgili olayları gruplandırarak ve aynı bölüm [anahtarına sahip iletileri aynı bölümde sırasıyla event Hubs](event-hubs-features.md#partitions), bu uygulamayı iletişim kurar.

> [!IMPORTANT] 
> "Uzaklık" bilgileri her bir olay hub 'ı için benzersizdir ve aynı olaylara yönelik uzaklıklar, Olay Hub 'ı örnekleri arasında farklılık gösterir. Kopyalanmış bir olay akışındaki konumu bulmak için zaman tabanlı uzaklıklar kullanın ve [yayılmış hizmet tarafından atanan meta verilere](#service-assigned-metadata)başvurun.
>
> Zamana dayalı uzaklıklar, alıcıyı belirli bir zaman noktasında başlatır:
> - *Eventposition. FromStart ()* -tüm tutulan verileri yeniden okuyun.
> - *Eventposition. FromEnd ()* -bağlantı zamanından itibaren tüm yeni verileri oku.
> - *Eventposition. FromEnqueuedTime (DateTime)* -belirli bir tarih ve saatten başlayan tüm veriler.
>
> EventProcessor 'da, EventProcessorOptions üzerinde InitialOffsetProvider ile konumu ayarlarsınız. Diğer alıcı API 'Leri ile, konum Oluşturucu aracılığıyla geçirilir. 


Azure Işlevleri 'ni temel alan [örnek olarak sağlanan](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) önceden oluşturulmuş çoğaltma işlev yardımcıları, kaynak bölümden alınan aynı bölüm anahtarına sahip olay akışlarının, özgün akışta ve aynı bölüm anahtarıyla bir toplu iş olarak hedef olay hub 'ına gönderilmesini sağlamaktır.

Kaynak ve hedef olay hub 'ının bölüm sayısı aynıysa, hedefteki tüm akışlar kaynaktaki gibi aynı bölümlerle eşlenir.
Bölüm sayısı farklıysa, aşağıda açıklanan bazı diğer desenlerde önemli olan, eşleme farklı olur, ancak akışlar her zaman bir arada ve sırayla saklanır.

Hedef bölümde bir bölüm anahtarı olmadan farklı akışlara veya bağımsız olaylara ait olayların göreli sırası, her zaman kaynak bölümden farklı olabilir.

### <a name="service-assigned-metadata"></a>Hizmet tarafından atanan meta veriler

Kaynak olay hub 'ından alınan bir olayın hizmet tarafından atanan meta verileri, özgün sıraya alma süresi, sıra numarası ve konum, hedef olay hub 'ında yeni hizmet tarafından atanan değerlerle değiştirilmiştir, ancak örneklerimizde sağlanan [yardımcı işlevlerle](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication), özgün değerler kullanıcı özelliklerinde saklanır: `repl-enqueue-time` (ISO8601 String), `repl-sequence` , `repl-offset` .

Bu özellikler dize türündedir ve ilgili özgün özelliklerin strıngıonvalue değerini içerir. Olay birden çok kez iletilirse, anlık kaynağın hizmet tarafından atanan meta verileri, noktalı virgülle ayrılmış değerler ile zaten mevcut olan özelliklere eklenir.

### <a name="failover"></a>Yük devretme

Olağanüstü durum kurtarma amaçları için çoğaltma kullanıyorsanız, Event Hubs hizmetindeki bölgesel kullanılabilirlik olaylarına karşı koruma sağlamak için veya ağ kesintilerine karşı, bu tür bir hata senaryosunda bir olay hub 'ından sonrakine yük devretme gerçekleştirilmesi gerekir. Bu, üreticileri ve/veya tüketicilerinin ikincil uç noktasını kullanmasını sağlar.

Tüm yük devretme senaryolarında, ad alanları için gerekli öğelerin yapısal olarak aynı olduğu varsayılır; yani Event Hubs ve tüketici grupları aynı şekilde adlandırılır ve paylaşılan erişim imza kuralları ve/veya rol tabanlı erişim denetimi kuralları aynı şekilde ayarlanır. [Ad alanlarını taşıma](move-across-regions.md) ve Temizleme adımını atlama kılavuzunu izleyerek ikincil bir ad alanı oluşturabilir (ve güncelleştirebilirsiniz).

Üreticileri ve tüketicilerinin geçiş yapmasını zorlamak için, kullanılacak ad alanı ile ilgili, erişim ve güncelleştirme kolay bir konumda arama için kullanılabilecek bilgileri yapmanız gerekir. Üreticileri veya tüketiciler sık sık veya kalıcı hatalarla karşılaştığında, bu konuma başvurması ve yapılandırmalarını ayarlamamız gerekir. Bu yapılandırmayı paylaşmanın çok çeşitli yolları vardır, ancak aşağıdakileri yapın: DNS ve dosya paylaşımları.

#### <a name="dns-based-failover-configuration"></a>DNS tabanlı yük devretme yapılandırması

Tek bir aday yaklaşım, kontrol ettiğiniz ve ilgili olay hub uç noktalarına işaret eden DNS SRV kayıtlarında bilgileri tutmak. 

> [!IMPORTANT] 
> Event Hubs, uç noktalarının CNAME kayıtlarıyla doğrudan diğer ad kullanmasına izin vermeyeceğini unutmayın. Bu, DNS 'yi, uç nokta adresleri için esnek bir arama mekanizması olarak kullanacaksınız ve IP adresi bilgilerini doğrudan çözümleyemeyecek anlamına gelir.

Etki alanının `example.com` ve uygulamanız için bir bölgenin olduğunu varsayalım `test.example.com` . İki alternatif Event Hubs için artık iki adet daha fazla iç içe bölge ve her birinde bir SRV kaydı oluşturacaksınız.

SRV kayıtları, ön eki olan ve 5671 numaralı bağlantı noktası için bir tane olmak üzere iki uç nokta kaydı içeren ve ' ın bağlantı noktası Event Hubs 443 ' deki AMQP-Over-WebSockets için bir tane olmak üzere, ortak kuralları izler `_azure_eventhubs._amqp` .

| Bölge                   | SRV kaydı                                                                                                                                                            |
| ---------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `eh1.test.example.com` | **`_azure_servicebus._amqp.eh1.test.example.com`**<br>`1 1 5671 eh1-test-example-com.servicebus.windows.net`<br>`2 2 443 eh1-test-example-com.servicebus.windows.net` |
| `eh2.test.example.com` | **`_azure_servicebus._amqp.eh2.test.example.com`**<br>`1 1 5671 eh2-test-example-com.servicebus.windows.net`<br>`2 2 443 eh2-test-example-com.servicebus.windows.net` |

Uygulamanızın bölgesinde, birincil olay hub 'ınıza karşılık gelen alt bölgeyi işaret eden bir CNAME girişi oluşturacaksınız:

| CNAME kaydı                | Diğer ad                    |
| --------------------------- | ------------------------ |
| `eventhub.test.example.com` | `test1.test.example.com` |

CNAME ve SRV kayıtlarını açıkça sorgulamaya imkan tanıyan bir DNS istemcisi kullanarak (Java ve .NET 'in yerleşik istemcileri yalnızca adların IP adreslerine basit olarak çözümlenmesine izin verir), istediğiniz uç noktayı çözebilirsiniz. Örneğin, [DnsClient.net](https://dnsclient.michaco.net/), arama işlevi şu şekilde olur:

```C#
static string GetEventHubName(string aliasName)
{
    const string SrvRecordPrefix = "_azure_eventhub._amqp.";
    LookupClient lookup = new LookupClient();

    return (from CNameRecord alias in (lookup.Query(aliasName, QueryType.CNAME).Answers)
            from SrvRecord srv in lookup.Query(SrvRecordPrefix + alias.CanonicalName, QueryType.SRV).Answers
            where srv.Port == 5671
            select srv.Target).FirstOrDefault()?.Value.TrimEnd('.');
}
```

İşlev, yukarıda gösterildiği gibi CNAME ile Şu anda diğer ad alanı 5671 bağlantı noktası için kaydedilen hedef ana bilgisayar adını döndürür.

Yük devretme gerçekleştirmek için CNAME kaydının düzenlemesi ve alternatif bölgeye işaret olması gerekir.

DNS kullanmanın avantajı ve özellikle [Azure DNS](../dns/dns-overview.md), Azure DNS bilgilerinin genel olarak çoğaltılıp tek bölge kesintilerine karşı dayanıklı olduğundan emin olur.

Bu yordam, [Event Hubs COĞRAFI Dr](event-hubs-geo-dr.md) 'nin çalışmasına benzer, ancak kendi denetiinkini tam olarak ve ayrıca etkin/etkin senaryolarla birlikte çalışıyor.

#### <a name="file-share-based-failover-configuration"></a>Dosya paylaşma tabanlı yük devretme yapılandırması

Uç nokta bilgilerini paylaşmak için DNS kullanmanın en basit alternatifi, birincil uç noktanın adını düz metin dosyasına koymaktan ve bu dosyayı kesintilere karşı dayanıklı bir altyapıdan sunmaya ve yine de güncelleştirmelere izin vermeye yöneliktir.

Genel kullanılabilirlik ve içerik çoğaltmasıyla yüksek düzeyde kullanılabilir bir Web sitesi altyapısı çalıştırırsanız, böyle bir dosya eklemek ve bir anahtar gerekliyse dosyayı yeniden yayımlamanız gerekir.

> [!CAUTION]
> Gizli dizileri de içeren tam bir bağlantı dizesi değil, yalnızca uç nokta adını bu şekilde yayımlamanız gerekir.

#### <a name="extra-considerations-for-failing-over-consumers"></a>Tüketicilerin yük devretmeyle ilgili ek konular

Olay Hub 'ı tüketicileri için yük devretme stratejisine ilişkin daha fazla dikkat edilecek konular olay işlemcisinin ihtiyaçlarına bağlıdır.

Veritabanları da dahil olmak üzere bir sistemin yeniden oluşturulmasını gerektiren bir olağanüstü durum varsa ve veritabanları doğrudan veya Olay Hub 'ında bulunan olaylardan ara işlem yoluyla, yedekleme işlemini geri yükler ve ardından, veritabanı yedeklemesinin oluşturulduğu andan itibaren sistem içindeki olayları yeniden başlatmaya başlamak istediğinizde, özgün sistem yok edilir.

Bir hata yalnızca sistemin bir dilimini veya yalnızca tek bir olay hub 'ını etkiliyorsa, büyük olasılıkla işlemenin kesintiye uğratılmasını gerektiren aynı konumdan olayları işlemeye devam etmek isteyeceksiniz.

Her iki senaryoyu da gerçekleştirmek ve ilgili Azure SDK 'sının olay işlemcisini kullanmak için, [Yeni bir denetim noktası deposu oluşturacaksınız](event-processor-balance-partition-load.md#checkpointing) ve işlemeyi sürdürmek istediğiniz _zaman damgasına_ göre bir ilk bölüm konumu sağlarsınız.

Geçiş yaptığınız Olay Hub 'ının denetim noktası deposuna hala erişiminiz varsa, yukarıda açıklanan [yayılmış meta veriler](#service-assigned-metadata) , zaten işlenmiş ve en son kaldığınız yerden tam olarak devam eden olayları atlayabilmenize yardımcı olur.

## <a name="merge"></a>Birleştir

Birleştirme deseninin tek bir hedefe işaret eden bir veya daha fazla çoğaltma görevi vardır. Bu, muhtemelen düzenli üreticileri aynı hedefe olay gönderiyor.

Bu patlamaların çeşitlemeleri şunlardır:

- İki veya daha fazla çoğaltma işlevi aynı anda ayrı kaynaklardan olay alıyor ve bunları aynı hedefe gönderiyor.
- Bir çoğaltma işlevi bir kaynaktan olay alıyor, ancak hedef doğrudan üreticileri tarafından da kullanılıyor.
- Önceki model, ancak iki veya daha fazla Event Hubs arasında yansıtılmalıdır, ancak olayların nerede üretildiğine bakılmaksızın, bu Event Hubs aynı akışları içermesiyle sonuçlanır.

İlk iki model varyasyonları önemsiz ve düz çoğaltma görevlerinden farklı değildir.

Son senaryo, zaten çoğaltılan olayların dışlanmasını yeniden çoğaltılmasını gerektirir. Teknik, [Eventhubtoeventhubmerge](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/code/EventHubMerge) örneğinde gösterilmiştir ve açıklanır.

## <a name="editor"></a>Düzenleyici

Düzenleyici deseninin [çoğaltma](#replication) düzeninde derlemeleri oluşturulur, ancak iletiler iletilmeden önce değiştirilir. 

Bu değişikliklere yönelik örnekler şunlardır:

- **_Kodlama kodlaması_** -olay içeriği ("Body" veya "payload" olarak da bilinir) _Apache avro_ biçimi veya bazı özel serileştirme biçimi kullanılarak kaynak kodlamadan alınırsa, ancak hedefin sahibi olan sistemin beklentisi, içeriğin _JSON_ kodlamalı olması için, bir dönüştürme çoğaltma görevinin, ilk olarak _Apache avro_ 'den bir bellek içi nesne grafiğine yük Serisi kaldırılır ve bu grafiği, iletilecek olayın _JSON_ biçimine serileştirirsiniz. Dönüştürme, **içerik sıkıştırma** ve açma görevlerini de içerir.
- **_Dönüştürme_** -yapılandırılmış veriler içeren olaylar, aşağı akış tüketicilerine göre daha kolay tükemek üzere bu verilerin yeniden şekillendirmesini gerektirebilir. Bu, iç içe yapıları düzleştirme, fazlalık veri öğelerini ayıklama veya yükü belirli bir şemaya tam olarak sığacak şekilde yeniden şekillendirme gibi bir iş içerebilir.
- Toplu **_işleme_** olayları, bir kaynaktan (tek bir Aktarımdaki birden çok olay) toplu olarak alınabilir, ancak listedir bir hedefe iletilebilmesi veya bunun tersini almış olması gerekir. Bu nedenle, bir görev tek bir giriş olay aktarımına göre birden çok olayı iletebilir veya daha sonra birlikte aktarılan bir olay kümesini toplayabilir.
- **_Doğrulama_** -dış kaynaklardaki olay verilerinin, iletilmeden önce bir kural kümesiyle uyumlu olup olmadığı denetlenir. Kurallar, şemalar veya kod kullanılarak ifade edilebilir. Uyumlu olmayan bulunan olaylar, günlüklerde belirtilen sorun nedeniyle bırakılabilir veya daha fazla işlem yapmak için özel bir hedef hedefe iletilebilir.
- **_Zenginleştirme_** -bazı kaynaklardan gelen olay verileri, hedef sistemlerde kullanılabilir olması için daha fazla bağlam ile zenginleştirme gerektirebilir. Bu, başvuru verileri aramak ve bu verileri olayla birlikte eklemeye ya da çoğaltma göreviyle bilinen, ancak olaylarda bulunmayan kaynakla ilgili bilgiler eklemeye dahil olabilir.
- **_Filtreleme_** -bir kaynaktan gelen bazı olayların, bazı kurala göre hedeften kısılan olması gerekebilir. Filtre, olayı kurala göre sınar ve olay kuralla eşleşmezse olayı bırakır. Belirli ölçütleri gözlemleyerek ve sonraki olayları aynı değerlerle bırakarak yinelenen olayları filtreleme bir filtreleme biçimidir.
- **_Şifreleme_** -bir çoğaltma görevinin, kaynaktan gelen içeriğin şifresini çözmeniz ve/veya içeriği bir hedefe iletmesinin yanı sıra, veya bu tür bir imzaya göre içerik ve meta verilerin bütünlüğünü doğrulamak zorunda kalabilir.
- **_Kanıtlama_** -bir çoğaltma görevi, büyük olasılıkla dijital bir imzayla korunan meta verileri, olayın belirli bir kanaldan veya belirli bir zamanda alındığını test eden bir olaya ekleyebilir.
- **_Zincirleme_** -bir çoğaltma görevi, olayların bütünlüğünden korunmasının ve eksik olayların algılanabilmesi gibi olay akışlarına imza uygulayabilir.

Dönüştürme, toplu işlem ve zenginleştirme desenleri genellikle [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) işlerle en iyi şekilde uygulanır. 

Tüm bu desenler, olayları almak için [Event Hubs tetikleyicisi](../azure-functions/functions-bindings-event-hubs-trigger.md) ve bunları teslim etmek Için [Olay Hub 'ı çıkış bağlaması](../azure-functions/functions-bindings-event-hubs-output.md) kullanılarak Azure işlevleri kullanılarak uygulanabilir.

## <a name="routing"></a>Yönlendirme

Yönlendirme deseninin [çoğaltma](#replication) düzeninde derlemeleri vardır, ancak bir kaynak ve bir hedef olması yerine, çoğaltma görevinin C# dilinde gösterildiği birden çok hedefi vardır:

```csharp
[FunctionName("EH2EH")]
public static async Task Run(
    [EventHubTrigger("source", Connection = "EventHubConnectionAppSetting")] EventData[] events,
    [EventHub("dest1", Connection = "EventHubConnectionAppSetting")] EventHubClient output1,
    [EventHub("dest2", Connection = "EventHubConnectionAppSetting")] EventHubClient output2,
    ILogger log)
{
    foreach (EventData eventData in events)
    {
        // send to output1 and/or output2 based on criteria
        EventHubReplicationTasks.ConditionalForwardToEventHub(input, output1, log, (eventData) => {
            return ( inputEvent.SystemProperties.SequenceNumber%2==0 ) ? inputEvent : null;
        });
        EventHubReplicationTasks.ConditionalForwardToEventHub(input, output2, log, (eventData) => {
            return ( inputEvent.SystemProperties.SequenceNumber%2!=0 ) ? inputEvent : null;
        });
    }
}
```

Yönlendirme işlevi ileti meta verilerini ve/veya ileti yükünü göz önünde bulunduracaktır ve sonra gönderilecek mevcut hedeften birini seçer.

Azure Stream Analytics, birden fazla çıkışı tanımlayarak ve sonra çıktı başına bir sorgu çalıştırırken aynı elde edebilirsiniz.

```sql
select * into dest1Output from inputSource where Info = 1
select * into dest2Output from inputSource where Info = 2
```


## <a name="log-projection"></a>Günlük projeksiyonu

Günlük projeksiyonu, olay akışını veritabanında kayıtlar haline gelmesine sahip bir dizinli veritabanı üzerinde düzleştirir. Genellikle, olaylar aynı koleksiyona veya tabloya eklenir ve Olay Hub 'ı bölüm anahtarı, kaydın benzersiz olmasını arayan birincil anahtarın bir tarafı olur.

Günlük projeksiyonu, her bölüm anahtarı için yalnızca en son olayın korunduğu, olay verilerinize veya düzenlenmiş bir görünüme sahip bir zaman serisi historu oluşturabilir. Hedef veritabanının şekli, sonunda sizin ve uygulamanızın ihtiyaçlarına göre belirlenir. Bu model "olay kaynağı belirleme" olarak da adlandırılır.

> [!TIP]
> [Azure SQL veritabanı](../stream-analytics/sql-database-output.md) 'nda günlük projeksiyonları kolayca oluşturabilir ve Azure Stream Analytics [Azure Cosmos DB](../stream-analytics/azure-cosmos-db-output.md) , bu seçeneği tercih etmeniz gerekir.

Aşağıdaki Azure Işlevi, bir Azure CosmosDB koleksiyonuna sıkıştırılan bir olay hub 'ının içeriğini projeler.

```C#
[FunctionName("Eh1ToCosmosDb1Json")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static async Task Eh1ToCosmosDb1Json(
    [EventHubTrigger("eh1", ConsumerGroup = "Eh1ToCosmosDb1", Connection = "Eh1ToCosmosDb1-source-connection")] EventData[] input,
    [CosmosDB(databaseName: "SampleDb", collectionName: "foo", ConnectionStringSetting = "CosmosDBConnection")] IAsyncCollector<object> output,
    ILogger log)
{
    foreach (var ev in input)
    {
        if (!string.IsNullOrEmpty(ev.SystemProperties.PartitionKey))
        {
            var record = new
            {
                id = ev.SystemProperties.PartitionKey,
                data = JsonDocument.Parse(ev.Body),
                properties = ev.Properties
            };
            await output.AddAsync(record);
        }
    }
}
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Işlevlerinde olay çoğaltıcı uygulamaları][1]
- [Olayları Event Hubs arasında çoğaltma][2]
- [Olayları Azure Service Bus çoğaltma][3]

[1]: event-hubs-federation-replicator-functions.md
[2]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy
[3]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus
