---
title: İleti çoğaltma görev desenleri-Azure Service Bus | Microsoft Docs
description: Bu makale, belirli ileti çoğaltma görevi desenlerini uygulamaya yönelik ayrıntılı kılavuz sağlar
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: d823ee7ccd4f53bfc3e10211a4f44908273a110d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97657596"
---
# <a name="message-replication-tasks-patterns"></a>İleti çoğaltma görevleri desenleri

[Federasyona genel bakış](service-bus-federation-overview.md) ve [çoğaltıcı işlevleri genel bakış](service-bus-federation-replicator-functions.md) ve çoğaltma görevlerinin temel öğeleri hakkında genel bakış ve bu makaleye devam etmeden önce bunları öğrenmeniz önerilir.

Bu makalede genel bakış bölümünde vurgulanan desenlerin birkaçı için uygulama kılavuzumuzu inceleyeceğiz. 

## <a name="replication"></a>Çoğaltma 

Çoğaltma deseninin iletileri bir kuyruktan veya konudan diğerine, bir kuyruktan veya konudan bir olay hub 'ı gibi başka bir hedefe kopyalar. İleti yükünde herhangi bir değişiklik yapılmadan iletiler iletilir. 

Bu düzenin uygulanması, [Azure Service Bus örneğine gelen ve olmayan ileti çoğaltmasının](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy) kapsamına alınmıştır.

### <a name="sequences-and-order-preservation"></a>Sıralar ve sipariş koruması

Çoğaltma modeli, bir kaynak sırasının veya konusunun iletilerin mutlak sırasını bir hedef sıraya veya konuya korumayı hedeflemez, ancak her gerektiğinde, uygulamanın gerektirdiği iletilerin göreli sırasını korumak için odaklanır. Uygulama, kaynak varlık için oturum desteğini etkinleştirerek ve ilgili iletileri aynı [oturum anahtarıyla](message-sessions.md)gruplandırarak bunu etkinleştirir.

Oturum kullanan önceden oluşturulmuş çoğaltma işlevleri, bir kaynak varlıktan aynı oturum kimliğine sahip ileti sıralarının, özgün sırada ve aynı oturum kimliğiyle bir toplu iş olarak hedef sıraya veya konuya gönderilmesini güvence altına almaktadır. 

### <a name="service-assigned-metadata"></a>Hizmet tarafından atanan meta veriler 

Kaynak sırasından veya konudan alınan bir iletinin hizmet tarafından atanan meta verileri, özgün sıraya alma süresi ve sıra numarası, hedef kuyruktaki veya konudaki yeni hizmet tarafından atanan değerlerle değiştirilmiştir, ancak örneklerimizde sağlanan varsayılan çoğaltma görevleriyle birlikte, özgün değerler kullanıcı özelliklerinde saklanır: `repl-enqueue-time` (ISO8601 String) ve `repl-sequence` .

Bu özellikler dize türündedir ve ilgili özgün özelliklerin strıngıonvalue değerini içerir.  İleti birden çok kez iletilirse, anlık kaynağın hizmet tarafından atanan meta verileri, noktalı virgülle ayrılmış değerler ile zaten mevcut olan özelliklere eklenir. 

### <a name="failover"></a>Yük devretme

Olağanüstü durum kurtarma amaçları için çoğaltma kullanıyorsanız, Service Bus hizmetindeki bölgesel kullanılabilirlik iletilerine karşı koruma için veya ağ kesintilerine karşı, bu tür bir senaryo bir kuyruktan veya konudan sonraki bir yük devretme gerçekleştirmeyi gerektirir, bu da üreticileri ve/veya tüketicileri ikincil uç noktayı kullanır.

Tüm yük devretme senaryolarında, ad alanları için gerekli öğelerin yapısal olarak aynı olduğu varsayılır, yani kuyruklar ve konular aynı şekilde adlandırılır ve paylaşılan erişim imza kuralları ve/veya rol tabanlı erişim denetimi kuralları aynı şekilde ayarlanır. [Ad alanlarını taşıma](move-across-regions.md) ve Temizleme adımını atlama kılavuzunu izleyerek ikincil bir ad alanı oluşturabilir (ve güncelleştirebilirsiniz).

Üreticileri ve tüketicilerinin geçiş yapmasını zorlamak için, kullanılacak ad alanı ile ilgili, erişim ve güncelleştirme kolay bir konumda arama için kullanılabilecek bilgileri yapmanız gerekir. Üreticileri veya tüketiciler sık sık veya kalıcı hatalarla karşılaştığında, bu konuma başvurması ve yapılandırmalarını ayarlamamız gerekir. Bu yapılandırmayı paylaşmanın çok çeşitli yolları vardır, ancak aşağıdakileri yapın: DNS ve dosya paylaşımları.

#### <a name="dns-based-failover-configuration"></a>DNS tabanlı yük devretme yapılandırması

Tek bir aday yaklaşım, kontrol ettiğiniz bir DNS SRV kayıtlarındaki bilgileri tutmak ve ilgili kuyruğu veya konu bitiş noktalarını işaret etmek için kullanılır. İleti hub 'Larının uç noktalarının CNAME kayıtlarıyla doğrudan diğer ad kullanmasına izin vermeyeceğini unutmayın. Bu, DNS 'yi, uç nokta adresleri için dayanıklı bir arama mekanizması olarak ve IP adresi bilgilerini doğrudan çözemeyeceğiniz anlamına gelir. 

Etki alanının `example.com` ve uygulamanız için bir bölgenin olduğunu varsayalım `test.example.com` . İki alternatif Service Bus için artık iki adet daha fazla iç içe bölge ve her birinde bir SRV kaydı oluşturacaksınız. 

SRV kayıtları, ön eki olan ve 5671 numaralı bağlantı noktası için bir tane olmak üzere iki uç nokta kaydı içeren ve ' ın bağlantı noktası Service Bus 443 ' deki AMQP-Over-WebSockets için bir tane olmak üzere, ortak kuralları izler `_azure_servicebus._amqp` .

| Bölge                 | SRV kaydı
|----------------------|-------------------------------------------------------------
| `sb1.test.example.com` | **`_azure_servicebus._amqp.sb1.test.example.com`**<br>`1 1 5671 sb1-test-example-com.servicebus.windows.net`<br>`2 2 443 sb1-test-example-com.servicebus.windows.net`
| `sb2.test.example.com` | **`_azure_servicebus._amqp.sb1.test.example.com`**<br>`1 1 5671 sb2-test-example-com.servicebus.windows.net`<br>`2 2 443 sb2-test-example-com.servicebus.windows.net`

Uygulamanızın bölgesinde, birincil kuyruğunuza veya konuya karşılık gelen alt bölgeyi işaret eden bir CNAME girişi oluşturacaksınız:

| CNAME kaydı                 | Diğer ad
|------------------------------|-------------------------------------------------------------
| `servicebus.test.example.com`  | `test1.test.example.com`

CNAME ve SRV kayıtlarını açıkça sorgulamaya imkan tanıyan bir DNS istemcisi kullanarak (Java ve .NET 'in yerleşik istemcileri yalnızca adların IP adreslerine basit olarak çözümlenmesine izin verir), istediğiniz uç noktayı çözebilirsiniz. Örneğin, [DnsClient.net](https://dnsclient.michaco.net/), arama işlevi şu şekilde olur:

``` C#
static string GetServiceBusName(string aliasName)
{
    const string SrvRecordPrefix = "_azure_servicebus._amqp.";
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

Bu yordam, [Service Bus COĞRAFI Dr](service-bus-geo-dr.md) 'nin çalışmasına benzer, ancak kendi denetiinkini tam olarak ve ayrıca etkin/etkin senaryolarla birlikte çalışıyor.

#### <a name="file-share-based-failover-configuration"></a>Dosya paylaşma tabanlı yük devretme yapılandırması

Uç nokta bilgilerini paylaşmak için DNS kullanmanın en basit alternatifi, birincil uç noktanın adını düz metin dosyasına koymaktan ve bu dosyayı kesintilere karşı dayanıklı bir altyapıdan sunmaya ve yine de güncelleştirmelere izin vermeye yöneliktir. 

Genel kullanılabilirlik ve içerik çoğaltmasıyla yüksek düzeyde kullanılabilir bir Web sitesi altyapısı çalıştırırsanız, böyle bir dosya eklemek ve bir anahtar gerekliyse dosyayı yeniden yayımlamanız gerekir.

## <a name="merge"></a>Birleştir

Birleştirme deseninin tek bir hedefe işaret eden bir veya daha fazla çoğaltma görevi vardır. Bu, muhtemelen normal üreticileri aynı hedefe ileti gönderiyor. 

Bu düzenin çeşitlemeleri şunlardır:
- İki veya daha fazla çoğaltma işlevi, farklı kaynaklardan iletileri eşzamanlı olarak alıyor ve aynı hedefe gönderiyor.
- Bir veya daha fazla çoğaltma işlevi, hedef doğrudan üreticileri tarafından da kullanıldığı sürece bir kaynaktan ileti alıyor. 
- Önceki model, ancak iki veya daha fazla konu arasında yansıtılan iletiler, iletilerin nerede üretildiğine bakılmaksızın aynı iletileri içeren konulara neden olur.

İlk iki model varyasyonları önemsiz ve düz çoğaltma görevlerinden farklı değildir.

Son senaryo, zaten çoğaltılan iletilerin dışlanmasını yeniden çoğaltılmasını gerektirir. Teknik, etkin/etkin örnekte gösterilmiştir ve açıklanmaktadır.

## <a name="editor"></a>Düzenleyici

Düzenleyici deseninin [çoğaltma](#replication) düzeninde derlemeleri oluşturulur, ancak iletiler iletilmeden önce değiştirilir. Bu değişikliklere yönelik örnekler şunlardır:

- ***Kodlama kodlaması*** -ileti içeriği ("Body" veya "payload" olarak da bilinir) *Apache avro* biçimi veya bazı özel serileştirme biçimi kullanılarak kaynak kodlamadan alınırsa, ancak hedefin sahibi olan sistemin beklentisi, içeriğin *JSON* kodlamalı olması için, bir dönüştürme çoğaltma görevinin, ilk olarak *Apache avro* 'den bir bellek içi nesne grafiğine yük Serisi kaldırılır ve bu grafiği, iletilecek ileti için *JSON* biçimine serileştirirsiniz. Dönüştürme, **içerik sıkıştırma** ve açma görevlerini de içerir.
- ***Dönüştürme*** -yapılandırılmış veriler içeren iletiler, aşağı akış tüketicilerine göre daha kolay tüketim için bu verilerin yeniden şekillendirmesini gerektirebilir. Bu, iç içe yapıları düzleştirme, fazlalık veri öğelerini ayıklama veya yükü belirli bir şemaya tam olarak sığacak şekilde yeniden şekillendirme gibi bir iş içerebilir.
- Toplu ***işleme*** -iletiler bir kaynaktan toplu işlerle (tek bir Aktarımdaki birden çok ileti) alınabilir, ancak listedir bir hedefe iletilebilmesi veya bunun tersini almış olması gerekir. Bu nedenle bir görev, birden fazla iletiyi tek bir giriş iletisi aktarımına göre iletebilir veya daha sonra birlikte aktarılan bir ileti kümesini toplayabilir. 
- ***Doğrulama*** -dış kaynaklardan gelen ileti verilerinin, iletilmeden önce bir kural kümesiyle uyumlu olup olmadığı denetlenir. Kurallar, şemalar veya kod kullanılarak ifade edilebilir. uyumlu olmayan bulunan iletiler, günlüklerde belirtilen sorun ile bırakılmış olabilir veya daha fazla işlem yapmak için özel bir hedef hedefe iletilebilir.   
- ***Zenginleştirme*** -bazı kaynaklardan gelen ileti verileri, hedef sistemlerde kullanılabilir olması için daha fazla bağlam ile zenginleştirme gerektirebilir. Bu, başvuru verilerini aramak ve bu verileri iletiyle birlikte eklemeye ya da çoğaltma göreviyle bilinen, ancak iletilerde bulunmayan kaynakla ilgili bilgiler eklemeye dahil olabilir. 
- ***Filtreleme*** -bir kaynaktan gelen bazı iletiler, bazı kurala göre hedeften kısılan edilmiş olabilir. Filtre, iletiyi bir kurala göre sınar ve ileti kuralla eşleşmezse iletiyi bırakır. Belirli ölçütleri gözlemleyerek ve sonraki iletileri aynı değerlerle bırakarak yinelenen iletilerin filtrelenmesi, filtreleme biçimidir.
- ***Yönlendirme ve bölümleme*** -bazı çoğaltma görevleri iki veya daha fazla alternatif hedefe izin verebilir ve iletinin meta verilerine veya içeriğine göre belirli bir ileti için hangi çoğaltma hedefinin seçili olduğunu belirten kurallar tanımlayabilir. Özel bir yönlendirme formu, görevin kurallara göre bir çoğaltma hedefinde açıkça bölüm atadığı bölümleniyor.
- ***Şifreleme*** -bir çoğaltma görevinin, kaynaktan gelen içeriğin şifresini çözmeniz ve/veya içeriği bir hedefe iletmesinin yanı sıra, veya bu tür bir imzaya göre içerik ve meta verilerin bütünlüğünü doğrulamak zorunda kalabilir. 
- ***Kanıtlama*** -bir çoğaltma görevi, büyük olasılıkla dijital imzayla korunan meta verileri, iletinin belirli bir kanaldan veya belirli bir zamanda alındığını test eden bir iletiye ekleyebilir.     
- ***Zincirleme*** -çoğaltma görevi, dizi bütünlüğünden korunmasının ve eksik iletilerin algılanabilmesi gibi İleti dizilerine imza uygulayabilir.

Tüm bu desenler, iletileri almak için [Ileti hub 'ı tetikleyicisi](../azure-functions/functions-bindings-service-bus-trigger.md) ve bunları teslim etmek için [kuyruk veya konu çıkış bağlaması](../azure-functions/functions-bindings-service-bus-output.md) kullanılarak Azure işlevleri kullanılarak uygulanabilir. 

## <a name="routing"></a>Yönlendirme

Yönlendirme deseninin [çoğaltma](#replication) düzeninde derlemeleri vardır, ancak bir kaynak ve bir hedef olması yerine, çoğaltma görevinin C# dilinde gösterildiği birden çok hedefi vardır:

``` csharp
[FunctionName("SBRouter")]
public static async Task Run(
    [ServiceBusTrigger("source", Connection = "serviceBusConnectionAppSetting")] Message[] messages,
    [ServiceBus("dest1", Connection = "serviceBusConnectionAppSetting")] QueueClient output1,
    [ServiceBus("dest2", Connection = "serviceBusConnectionAppSetting")] QueueClient output2,
    ILogger log)
{
    foreach (Message messageData in messages)
    {
        // send to output1 or output2 based on criteria 
    }
}
```

Yönlendirme işlevi ileti meta verilerini ve/veya ileti yükünü göz önünde bulunduracaktır ve sonra gönderilecek mevcut hedeften birini seçer.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Işlevlerinde ileti çoğaltıcı uygulamaları][1]
- [Service Bus arasında ileti çoğaltma][2]
- [İletileri Azure Event Hubs çoğaltma][3]

[1]: service-bus-federation-replicator-functions.md
[2]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy
[3]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopyToEventHub