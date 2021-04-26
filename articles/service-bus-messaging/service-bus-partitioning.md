---
title: Bölümlenmiş Azure Service Bus kuyrukları ve konuları oluşturun | Microsoft Docs
description: Birden çok ileti aracıları kullanarak Service Bus kuyrukların ve konuların nasıl bölümleneceğini açıklar.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: bc41bcf31102b19dd35f62452b956faf4f029551
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750923"
---
# <a name="partitioned-queues-and-topics"></a>Bölümlenmiş kuyruklar ve konular

İletileri depolamak için iletileri ve birden çok mesajlaşma deposunu işlemek üzere birden çok ileti aracılarını kullanır Azure Service Bus. Geleneksel bir kuyruk veya konu, tek bir ileti Aracısı tarafından işlenir ve tek bir mesajlaşma deposunda depolanır. Service Bus *bölümler* , birden çok ileti aracılarına ve mesajlaşma depolarına göre bölümlenmesi için kuyrukları ve konuları veya *mesajlaşma varlıklarını* etkinleştirir. Bölümlendirme, bölümlenmiş bir varlığın genel üretilen işinin artık tek bir ileti aracısının veya mesajlaşma deposunun performansıyla sınırlı olmadığı anlamına gelir. Ayrıca, bir mesajlaşma deposunun geçici kesilmesi bölümlenmiş bir kuyruğu veya konuyu işlemez. Bölümlenmiş kuyruklar ve konular, işlemler ve oturumlar için destek gibi tüm gelişmiş Service Bus özellikleri içerebilir.

> [!NOTE]
> Bölümlendirme, temel veya standart SKU 'lardaki tüm kuyruklar ve konular için varlık oluşturma bölümünde kullanılabilir. Premium mesajlaşma SKU 'SU için kullanılamaz, ancak Premium ad alanlarında daha önce var olan bölümlenmiş varlıklar beklendiği gibi çalışmaya devam eder.
 
Var olan herhangi bir kuyrukta veya konuda bölümleme seçeneğini değiştirmek mümkün değildir; yalnızca varlığı oluştururken seçeneğini ayarlayabilirsiniz.

## <a name="how-it-works"></a>Nasıl çalışır?

Bölümlenmiş her sıra veya konu, birden çok bölümden oluşur. Her bölüm farklı bir mesajlaşma deposunda depolanır ve farklı bir ileti Aracısı tarafından işlenir. Bölümlenmiş bir sıraya veya konuya bir ileti gönderildiğinde Service Bus, bir bölümden birine ileti atar. Seçim rastgele Service Bus veya gönderenin belirtebileceğiniz bir bölüm anahtarı kullanılarak yapılır.

İstemci, bölümlenmiş bir kuyruktan veya bir aboneliğinden bölümlenmiş bir konuya ileti almak istediğinde, Service Bus iletiler için tüm bölümleri sorgular ve ardından herhangi bir mesajlaşma mağazasından alıcıya alınan ilk iletiyi döndürür. Service Bus diğer iletileri önbelleğe alır ve ek alma istekleri aldığında onları döndürür. Alıcı istemci Bölümlendirmeyi bilmez; bölümlenmiş bir kuyruğun veya konunun istemciye yönelik davranışı (örneğin, okuma, tamamlanma, erteleme, sahipsiz, ön alma), normal bir varlığın davranışıyla aynıdır.

Bölümlendirilmemiş bir varlıktaki göz atma işlemi her zaman en eski iletiyi döndürür, ancak bölümlenmiş bir varlıkta değil. Bunun yerine, ileti aracısının ilk yanıtı olan bölümlerden birindeki en eski iletiyi döndürür. Döndürülen iletinin tüm bölümlerde en eski bir değer olduğu garantisi yoktur. 

Bölümlenmiş bir kuyruk veya konuyla bir ileti gönderilirken veya ileti alırken ek bir maliyet yoktur.

> [!NOTE]
> Göz atma işlemi, sıra numarasına göre bölümden en eski iletiyi döndürür. Bölümlenmiş varlıklar için sıra numarası bölüme göre verilir. Daha fazla bilgi için bkz. [ileti sıralaması ve zaman damgaları](../service-bus-messaging/message-sequencing.md).

## <a name="enable-partitioning"></a>Bölümlemeyi etkinleştir

Azure Service Bus ile bölümlenmiş kuyruklar ve konular kullanmak için Azure SDK 2,2 veya sonraki bir sürümünü kullanın veya `api-version=2013-10` http isteklerinizi veya sonraki bir sürümünü belirtin.

### <a name="standard"></a>Standart

Standart Mesajlaşma katmanında, 1, 2, 3, 4 veya 5 GB boyutunda Service Bus kuyruklar ve konular oluşturabilirsiniz (varsayılan değer 1 GB 'tır). Bölümlendirme etkinken, Service Bus aynı boyuttaki her biri varlığın 16 kopyasını (16 Bölüm) oluşturur. Bu nedenle, boyutu 5 GB olan ve 16 bölümlü bir sıra oluşturursanız, en büyük sıra boyutu (5 \* 16) = 80 GB olur. Bölümlenmiş kuyruğun veya konusunun en büyük boyutunu, bu varlığın **genel bakış** dikey penceresinde [Azure Portal][Azure portal]girişine bakarak görebilirsiniz.

### <a name="premium"></a>Premium

Premium katman ad alanında bölümlendirme varlıkları desteklenmez. Ancak, yine de 1, 2, 3, 4, 5, 10, 20, 40 veya 80 GB boyutunda Service Bus kuyruklar ve konular oluşturabilirsiniz (varsayılan değer 1 GB 'tır). Kuyruğunuzun veya konusunun boyutunu, bu varlığın **genel bakış** dikey penceresinde [Azure Portal][Azure portal]girişine bakarak görebilirsiniz.

### <a name="create-a-partitioned-entity"></a>Bölümlenmiş varlık oluşturma

Bölümlenmiş bir kuyruğu veya konuyu oluşturmanın birkaç yolu vardır. Uygulamanızdan kuyruğu veya konuyu oluşturduğunuzda, kuyruk [açıklaması. Enablebölümlendirme][QueueDescription.EnablePartitioning] veya [Topicdescription. enablebölümlendirme][TopicDescription.EnablePartitioning] özelliğini **true** olarak ayarlayarak sıra veya konu için bölümlemeyi etkinleştirebilirsiniz. Bu özellikler Queue veya topic oluşturulduğu sırada ayarlanmalıdır ve yalnızca eski [windowsazure. ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) kitaplığında kullanılabilir. Daha önce belirtildiği gibi, var olan bir kuyruk veya konu üzerinde bu özellikleri değiştirmek mümkün değildir. Örnek:

```csharp
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Alternatif olarak, [Azure Portal][Azure portal]bölümlenmiş bir kuyruk veya konu oluşturabilirsiniz. Portalda bir kuyruk veya konu oluşturduğunuzda, kuyruk veya konu **Oluştur** iletişim kutusunda **bölümlendirme etkinleştir** seçeneği varsayılan olarak denetlenir. Bu seçeneği yalnızca standart bir katman varlığında devre dışı bırakabilirsiniz; Premium katmanının bölümlenmesi desteklenmez ve onay kutusunun hiçbir etkisi yoktur. 

## <a name="use-of-partition-keys"></a>Bölüm anahtarlarının kullanımı

Bir ileti bölümlenmiş bir sıraya veya konuya sıralandığında, Service Bus bir bölüm anahtarı olup olmadığını denetler. Bulursa, bölümü bu anahtara göre seçer. Bölüm anahtarı bulamazsa, bölümü bir iç algoritmaya göre seçer.

### <a name="using-a-partition-key"></a>Bölüm anahtarı kullanma

Oturumlar veya işlemler gibi bazı senaryolar, iletilerin belirli bir bölümde depolanmasını gerektirir. Bu senaryoların tümü bir bölüm anahtarı kullanılmasını gerektirir. Aynı bölüm anahtarını kullanan tüm iletiler aynı bölüme atanır. Bölüm geçici olarak kullanılamıyorsa, Service Bus bir hata döndürür.

Senaryoya bağlı olarak, farklı ileti özellikleri bölüm anahtarı olarak kullanılır:

**SessionID**: bir iletide [sessionıd](/dotnet/api/microsoft.azure.servicebus.message.sessionid) özelliği ayarlandıysa Service Bus, bölüm anahtarı olarak **SessionID** 'yi kullanır. Bu şekilde, aynı oturuma ait olan tüm iletiler aynı ileti Aracısı tarafından işlenir. Oturumlar, ileti sıralamasını ve oturum durumlarının tutarlılığını sağlamak için Service Bus etkinleştirir.

**Partitionkey**: bir Ileti [partitionkey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) özelliğine sahipse ancak [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid) özelliği ayarlanmamışsa Service Bus, bölüm anahtarı olarak [partitionkey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) özellik değerini kullanır. İletide hem [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid) hem de [partitionkey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) özellikleri ayarlandıysa, her iki özellik de özdeş olmalıdır. [Partitionkey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) özelliği [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid) özelliğinden farklı bir değere ayarlanmışsa Service Bus geçersiz bir işlem özel durumu döndürür. Bir gönderici oturum kullanmayan işlem iletileri gönderiyorsa [Partitionkey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) özelliği kullanılmalıdır. Bölüm anahtarı, bir işlem içinde gönderilen tüm iletilerin aynı mesajlaşma Aracısı tarafından işlenmesini sağlar.

**MessageID**: Queue veya topic, [Requiresduplicatedetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) özelliği **true** olarak ayarlanmışsa ve [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid) ya da [partitionkey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) özellikleri ayarlanmamışsa, [MessageID](/dotnet/api/microsoft.azure.servicebus.message.messageid) özelliğinin değeri bölüm anahtarı olarak görev yapar. (Microsoft .NET ve AMQP kitaplıkları, gönderen uygulama yoksa otomatik olarak bir ileti KIMLIĞI atar.) Bu durumda, aynı iletinin tüm kopyaları aynı ileti Aracısı tarafından işlenir. Bu KIMLIK, yinelenen iletileri algılayıp ortadan kaldırmak Service Bus sağlar. [Requiresduplicatedetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) özelliği **true** olarak ayarlanmamışsa, Service Bus [MessageID](/dotnet/api/microsoft.azure.servicebus.message.messageid) özelliğini bir bölüm anahtarı olarak kabul etmez.

### <a name="not-using-a-partition-key"></a>Bölüm anahtarı kullanmıyor

Bölüm anahtarı yokluğunda Service Bus, iletileri bölümlenmiş sıranın veya konunun tüm bölümlerine hepsini bir kez daha kez dağıtır. Seçilen bölüm kullanılamıyorsa Service Bus iletiyi farklı bir bölüme atar. Bu şekilde, bir mesajlaşma deposunun geçici olarak kullanılamamasına rağmen gönderme işlemi başarılı olur. Ancak, bir bölüm anahtarının sağladığı garantili sıralamayı elde edersiniz.

Kullanılabilirlik (bölüm anahtarı olmadan) ve tutarlılık (bölüm anahtarı kullanarak) arasında zorunluluğunu getirir hakkında daha ayrıntılı bir tartışma için, [Bu makaleye](../event-hubs/event-hubs-availability-and-consistency.md)bakın. Bu bilgiler, bölümlenmiş Service Bus varlıklara eşit şekilde uygulanır.

İletiyi farklı bir bölüme sıraya almak için yeterli zaman Service Bus vermek için, iletiyi gönderen istemci tarafından belirtilen [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) değeri 15 saniyeden büyük olmalıdır. [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) özelliğini varsayılan değer olan 60 saniyeye ayarlamanız önerilir.

Bölüm anahtarı "PIN" i belirli bir bölüme bir ileti. Bu bölümü tutan mesajlaşma deposu kullanılamıyorsa Service Bus bir hata döndürür. Bölüm anahtarı yokluğunda Service Bus, farklı bir bölüm seçebilir ve işlem başarılı olur. Bu nedenle, gerekli olmadığı takdirde bir bölüm anahtarı sağlamamalısınız.

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>Gelişmiş Konular: bölümlenmiş varlıklarla işlem kullanma

Bir işlem kapsamında gönderilen iletilerin bölüm anahtarını belirtmesi gerekir. Anahtar Şu özelliklerden biri olabilir: [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid), [Partitionkey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)veya [MessageID](/dotnet/api/microsoft.azure.servicebus.message.messageid). Aynı işlemin bir parçası olarak gönderilen tüm iletiler aynı bölüm anahtarını belirtmelidir. Bir işlem içinde bölüm anahtarı olmadan bir ileti göndermeye çalışırsanız, Service Bus geçersiz bir işlem özel durumu döndürür. Farklı bölüm anahtarlarına sahip aynı işlem içinde birden çok ileti göndermeye çalışırsanız, Service Bus geçersiz bir işlem özel durumu döndürür. Örnek:

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    Message msg = new Message("This is a message");
    msg.PartitionKey = "myPartitionKey";
    await messageSender.SendAsync(msg); 
    await ts.CompleteAsync();
}
committableTransaction.Commit();
```

Bölüm anahtarı olarak görev yapan özelliklerden herhangi biri ayarlanırsa Service Bus iletiyi belirli bir bölüme sabitleyebilir. Bu davranış, bir işlemin kullanılıp kullanılmadığına bakılmaksızın oluşur. Gerekli değilse bir bölüm anahtarı belirtmemelidir.

## <a name="using-sessions-with-partitioned-entities"></a>Bölümlenmiş varlıklarla oturumları kullanma

Oturum kullanan bir konuya veya kuyruğa işlem iletisi göndermek için, iletide [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid) özelliği ayarlanmış olmalıdır. [Partitionkey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) özelliği de belirtilmişse, [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid) özelliği ile aynı olmalıdır. Farklıysa, Service Bus geçersiz bir işlem özel durumu döndürür.

Normal (bölümlenmemiş) kuyrukların veya konuların aksine, farklı oturumlara birden çok ileti göndermek için tek bir işlem kullanılması mümkün değildir. Denendiğinde Service Bus geçersiz bir işlem özel durumu döndürür. Örnek:

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    Message msg = new Message("This is a message");
    msg.SessionId = "mySession";
    await messageSender.SendAsync(msg); 
    await ts.CompleteAsync();
}
committableTransaction.Commit();
```

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>Bölümlenmiş varlıklarla otomatik ileti iletme

Service Bus bölümlenmiş varlıkların içinden, öğesinden veya arasında otomatik ileti iletmeyi destekler. Otomatik ileti iletmeyi etkinleştirmek için, kaynak kuyrukta veya abonelikte [Queuedescription. ForwardTo][QueueDescription.ForwardTo] özelliğini ayarlayın. İleti bir bölüm anahtarı ([SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid), [Partitionkey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)veya [MessageID](/dotnet/api/microsoft.azure.servicebus.message.messageid)) belirtiyorsa, bu bölüm anahtarı hedef varlık için kullanılır.

## <a name="considerations-and-guidelines"></a>Konular ve yönergeler
* **Yüksek tutarlılık özellikleri**: bir varlık, bölümleme anahtarı için oturumlar, yinelenen algılama veya açık denetim gibi özellikler kullanıyorsa, Mesajlaşma işlemleri her zaman belirli bir bölüme yönlendirilir. Bölümlerden herhangi biri yüksek trafikle veya temel alınan mağaza sağlıksız ise, bu işlemler başarısız olur ve kullanılabilirlik azalır. Genel olarak tutarlılık, bölümlenmemiş varlıklardan hala çok daha yüksektir; trafiğin yalnızca bir alt kümesi, tüm trafiğin aksine sorunlarla karşılaşıyor. Daha fazla bilgi için bu [kullanılabilirlik ve tutarlılık tartışması](../event-hubs/event-hubs-availability-and-consistency.md)bölümüne bakın.
* **Yönetim**: varlığın tüm bölümlerinde oluşturma, güncelleştirme ve silme gibi işlemler yapılmalıdır. Herhangi bir bölüm sağlıksız ise, bu işlemler için hatalara neden olabilir. Get işlemi için, her bölümden ileti sayısı gibi bilgiler toplanmalıdır. Herhangi bir bölüm sağlıksız ise, varlık kullanılabilirlik durumu sınırlı olarak bildirilir.
* **Düşük hacimli ileti senaryoları**: Bu tür senaryolarda, özellikle http protokolünü kullanırken, tüm iletileri almak için birden çok alma işlemi gerçekleştirmeniz gerekebilir. Alma isteklerinde, ön uç tüm bölümlerde bir alma gerçekleştirir ve alınan tüm yanıtları önbelleğe alır. Aynı bağlantı üzerindeki bir sonraki alma isteği bu önbelleğe alma özelliğinden faydalanır ve alma gecikmeleri daha düşük olacaktır. Ancak, birden çok bağlantınız varsa veya HTTP kullanıyorsanız, her istek için yeni bir bağlantı kurar. Bu nedenle, aynı düğüm üzerinde aratacağının garantisi yoktur. Var olan tüm iletiler kilitliyse ve başka bir ön uçta önbelleğe alınmışsa alma işlemi **null** değerini döndürür. İletilerin sonunda zaman sona erer ve bunları tekrar alabilirsiniz. HTTP etkin tutma önerilir. Düşük hacimli senaryolarda bölümlendirme kullanılırken alma işlemleri beklenenden uzun sürebilir. Bu nedenle, bu senaryolarda bölümleme kullanmanıza gerek kalmaz. Mevcut bölümlenmiş varlıkları silin ve performansı artırmak için bölümleme devre dışı olarak yeniden oluşturun.
* **Iletilere göz atma/göz atma**: yalnızca eski [windowsazure. ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) kitaplığında kullanılabilir. [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) , [messagecount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.messagecount) özelliğinde belirtilen ileti sayısını her zaman döndürmez. Bu davranışın iki yaygın nedeni vardır. Bunlardan biri, ileti koleksiyonunun toplanmış boyutunun 256 KB olan boyut üst sınırını aşmasının bir nedenidir. Başka bir nedenden dolayı, kuyruğun veya konunun [Enablebölümlendirme özelliği](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning) **true** olarak ayarlandıysa, bir bölümün istenen ileti sayısını tamamlamaya yetecek sayıda iletisi bulunmayabilir. Genel olarak, bir uygulama belirli sayıda ileti almak isterse, bu sayıda ileti alınana veya göz atabilmek için daha fazla ileti kalmayana kadar [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) öğesini tekrar çağırmalıdır. Kod örnekleri dahil daha fazla bilgi için bkz. [Queueclient. PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) veya [Subscriptionclient. PeekBatch](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.peekbatch) API belgeleri.

## <a name="latest-added-features"></a>Eklenen en son özellikler

* Kural ekleme veya kaldırma artık bölümlenmiş varlıklarda destekleniyor. Bölümlendirilmemiş varlıklardan farklı olarak, bu işlemler işlemler altında desteklenmez. 
* AMQP artık bölümlenmiş bir varlığa gelen ve olmayan iletileri göndermek ve almak için desteklenir.
* AMQP şu işlemler için destekleniyor: [Batch gönderme](/dotnet/api/microsoft.servicebus.messaging.queueclient.sendbatch), [toplu alma](/dotnet/api/microsoft.servicebus.messaging.queueclient.receivebatch), [sıra numarası ile alma](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive), [göz atma](/dotnet/api/microsoft.servicebus.messaging.queueclient.peek), [kilit yenileme](/dotnet/api/microsoft.servicebus.messaging.queueclient.renewmessagelock), [Ileti zamanlama](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync), [zamanlanan iletiyi iptal etme](/dotnet/api/microsoft.azure.servicebus.queueclient.cancelscheduledmessageasync), [kural ekleme](/dotnet/api/microsoft.azure.servicebus.ruledescription), [kural kaldırma](/dotnet/api/microsoft.azure.servicebus.ruledescription), oturum [yenileme kilidi](/dotnet/api/microsoft.servicebus.messaging.messagesession.renewlock), oturum durumunu [ayarlama](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate), oturum durumunu [alma](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate)ve [oturumları listeleme](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions).

## <a name="partitioned-entities-limitations"></a>Bölümlenmiş varlık sınırlamaları

Şu anda Service Bus bölümlenmiş kuyruklar ve konular üzerinde aşağıdaki sınırlamaları uygular:

* Bölümlenmiş kuyruklar ve konular Premium mesajlaşma katmanında desteklenmez. Oturumlar, SessionID kullanılarak Premier katmanında desteklenir. 
* Bölümlenmiş kuyruklar ve konular, tek bir işlemde farklı oturumlara ait olan iletilerin gönderilmesini desteklemez.
* Service Bus şu anda ad alanı başına en çok 100 bölümlenmiş kuyruğa veya konuya izin vermektedir. Her bölümlenmiş kuyruk veya konu, ad alanı başına 10.000 varlık kotasına (Premium katmana uygulanmaz) göre sayılır.

## <a name="next-steps"></a>Sonraki adımlar
Azure portal, PowerShell, CLı, Kaynak Yöneticisi şablonu, .NET, Java, Python ve JavaScript kullanarak bölümlemeyi etkinleştirebilirsiniz. Daha fazla bilgi için bkz. [bölümlemeyi etkinleştirme](enable-partitions.md). 

[Amqp 1,0 protokol kılavuzunda](service-bus-amqp-protocol-guide.md)amqp 1,0 mesajlaşma belirtiminin temel kavramları hakkında bilgi edinin.

[Azure portal]: https://portal.azure.com
[QueueDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablepartitioning
[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto
[AMQP 1.0 support for Service Bus partitioned queues and topics]: ./service-bus-amqp-protocol-guide.md
