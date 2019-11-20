---
title: İleti aktarımlarını, kilitleri ve kapatmayı Azure Service Bus | Microsoft Docs
description: Service Bus ileti aktarımlarına ve kapatma işlemlerine genel bakış
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: aschhab
ms.openlocfilehash: 9aaada1ede8912b8b70f37c628ec918eca9be9d2
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71676273"
---
# <a name="message-transfers-locks-and-settlement"></a>İleti aktarımları, kilitler ve kapatma

Service Bus gibi bir ileti aracısının merkezi özelliği, iletileri bir kuyruğa veya konuya kabul etmek ve daha sonra alınabilmeleri için kullanılabilir tutmaktır. *Send* iletisi, ileti aracısına ileti aktarımı için yaygın olarak kullanılan terimdir. *Alma* , bir iletinin alma istemcisine aktarılması için yaygın olarak kullanılan terimdir.

Bir istemci bir ileti gönderdiğinde, genellikle iletinin aracı tarafından düzgün şekilde aktarıldığını ve kabul edilip edilmediğini veya bir hata oluştuğunu bilmek ister. Bu olumlu veya negatif bildirim, istemciyi ve aracının aktarım durumu hakkında bilgi sahibi olduğunu ve bu nedenle *kapatma*olarak adlandırılmasını sağlar.

Benzer şekilde, aracı bir istemciye bir ileti aktarırken, aracı ve istemci, iletinin başarıyla işlenip işlenmeyeceğini ve bu nedenle kaldırılıp kaldırılmadığını ya da ileti teslimi ya da işlemenin başarısız olup olmadığını anlamak ister ve bu nedenle iletinin yeniden teslim edilmesi gerekebilir.

## <a name="settling-send-operations"></a>Gönderme işlemlerini kapatma

Desteklenen Service Bus API istemcilerinden herhangi birini kullanarak, işlemleri Service Bus içine gönder, API işleminin Service Bus gelmesi için bir kabul sonucu bekleyeceğini ve sonra Gönder işlemini tamamlayarak, her zaman açıkça kapatılır.

İleti Service Bus tarafından reddedilirse, reddetme bir hata göstergesi ve içinde bir "izleme kimliği" olan metin içerir. Reddetme, işlemin başarılı olma beklentisiyle yeniden denenip denenmeyeceğini belirten bilgileri de içerir. İstemcide, bu bilgiler özel bir duruma getirilir ve gönderme işlemi çağıranına yükseltilir. İleti kabul edildiyse, işlem sessizce tamamlanır.

.NET Standard istemcisi ve Java istemcisi için özel protokol olan ve [.NET Framework istemcisi için bir seçenek](service-bus-amqp-dotnet.md)olan AMQP protokolünü kullanırken, ileti aktarımları ve kapatmalar ardışık düzen ve tamamen zaman uyumsuz olur ve zaman uyumsuz programlama modeli API türevlerini kullanmanız önerilir.

Bir gönderici, SBMP protokolünde veya HTTP 1,1 ile büyük bir süre olacağı gibi, her bir iletinin onaylanmasına gerek kalmadan, her iletiyi almak için beklemek zorunda kalmadan, hatta her ileti için bir araya daha fazla ileti yerleştirebilir. İlgili iletiler kabul edildiği ve depolandığı, bölümlenmiş varlıklarda veya farklı varlıklara gönderme işlemi çakıştığında, bu zaman uyumsuz gönderme işlemleri tamamlanır. Tamamlamalar, özgün gönderme siparişinden da oluşabilir.

Gönderme işlemlerinin sonucunu işleme stratejisi, uygulamanız için anında ve önemli performans etkisine sahip olabilir. Bu bölümdeki örnekler, içinde C# yazılmıştır ve Java Futures için equivalently uygular.

Uygulama, burada düz bir döngüyle gösterilen ve bir sonraki iletiyi, zaman uyumlu veya zaman uyumsuz API şekillerini göndermeden önce her gönderme işleminin tamamlanmasını beklemek olsaydı, 10 ' dan sonra yalnızca 10 ileti gönderilir Kapanış için sıralı tam gidiş dönüş.

Bir şirket içi siteden Service Bus ve her iletiyi kabul etmesi ve depolaması için yalnızca 10 Service Bus MS sağlayan 70 milisaniyelik bir TCP gidiş dönüş gecikme süresi ile, aşağıdaki döngü en az 8 saniye sürer, yük aktarma süresini veya potansiyelini saymaz yol tıkanıklığı etkileri:

```csharp
for (int i = 0; i < 100; i++)
{
  // creating the message omitted for brevity
  await client.SendAsync(…);
}
```

Uygulama, arka arkaya 10 zaman uyumsuz gönderme işlemini başlatır ve ilgili tamamlamayı ayrı olarak bekleirse, bu 10 gönderme işlemlerine ilişkin gidiş dönüş süresi örtüşüyor. 10 ileti hemen art arda aktarılır, hatta TCP çerçevelerini paylaşıma alabilir ve genel aktarım süresi büyük ölçüde, aracıya aktarılan iletilerin alması için gereken ağla ilgili zamana bağlıdır.

Önceki döngü için aynı varsayımlar yapılıyor, aşağıdaki döngü için toplam çakışan yürütme süresi bir saniye içinde iyi kalabilirler:

```csharp
var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  tasks.Add(client.SendAsync(…));
}
await Task.WhenAll(tasks);
```

Tüm zaman uyumsuz programlama modellerinin, bekleyen işlemleri tutan bir dizi bellek tabanlı, gizli iş kuyruğunu kullandığından emin olmak önemlidir. [Sendadsync](/dotnet/api/microsoft.azure.servicebus.queueclient.sendasync#Microsoft_Azure_ServiceBus_QueueClient_SendAsync_Microsoft_Azure_ServiceBus_Message_) (C#) veya **Send** (Java) geri geldiğinde, Gönder görevi o iş sırasında sıraya alınır ancak protokol hareketi, görevin çalışmaya dönüşü bir kez gönderilir. İletilerin ani ve güvenilir bir sorun olduğu konusunda eğilimi gösteren kod için, gönderilen tüm iletiler factually olana kadar bellek çektiğinden, her seferinde çok fazla ileti "uçuşmaya" koyduğundan dikkatli olunmalıdır.

İçindeki C#aşağıdaki kod parçacığında gösterildiği gibi Semaforlar, gerektiğinde uygulama düzeyi azaltmayı etkinleştiren eşitleme nesneleridir. Semaforun bu kullanımı, en fazla 10 iletinin bir kez Uçuşta olmasını sağlar. 10 kullanılabilir semafor kilitlerinin biri göndermeden önce alınır ve gönderme işlemi olarak serbest bırakılır. Döngü boyunca geçen 11, önceki gönderiden en az biri tamamlanana kadar bekler ve ardından kilidini kullanılabilir hale getirir:

```csharp
var semaphore = new SemaphoreSlim(10);

var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  await semaphore.WaitAsync();

  tasks.Add(client.SendAsync(…).ContinueWith((t)=>semaphore.Release()));
}
await Task.WhenAll(tasks);
```

Uygulamalar **hiçbir zaman** zaman uyumsuz gönderme işlemini işlemin sonucunu almadan bir "ateş ve unutmadan" bir biçimde başlatmamalıdır. Bunun yapılması, iç ve görünmeyen görev kuyruğunu bellek tükenmesi için yükleyebilir ve uygulamanın gönderme hatalarını algılamasını önler:

```csharp
for (int i = 0; i < 100; i++)
{

  client.SendAsync(message); // DON’T DO THIS
}
```

Alt düzey AMQP istemcisiyle, Service Bus Ayrıca "önceden kapatılan" aktarımları kabul eder. Önceden kapatılan bir aktarım, sonucun istemciye geri bildirilmediği ve iletinin gönderildiğinde kapatılan kabul edildiği bir ateş ve-unut işlemidir. İstemciye geri bildirimde bulunamaması Ayrıca, Tanılama için kullanılabilir bir kullanılabilir veri yoktur, bu da bu modun Azure desteği aracılığıyla yardım için uygun olmadığı anlamına gelir.

## <a name="settling-receive-operations"></a>Alma işlemlerini kapatma

Alma işlemleri için Service Bus API istemcileri iki farklı açık modu etkinleştirir: *al-ve-DELETE* ve *Peek-kilitle*.

### <a name="receiveanddelete"></a>ReceiveAndDelete

[Alma ve silme](/dotnet/api/microsoft.servicebus.messaging.receivemode) modu, aracıya gönderildiği sırada, alıcı istemciye gönderdiği tüm iletileri kabul etmeyi söyler. Diğer bir deyişle, aracı bunu kabloya yerleştirdiğinden ileti tüketilen kabul edilir. İleti aktarımı başarısız olursa ileti kaybedilir.

Bu modun üstü, alıcının ileti üzerinde daha fazla işlem yapması gerekmez ve ayrıca kapatmanın sonucunu beklerken yavaşlamaz. Tek tek iletilerde içerilen verilerin değeri düşük ve/veya yalnızca çok kısa bir süre için anlamlı ise, bu mod makul bir seçimdir.

### <a name="peeklock"></a>PeekLock

[Peek-kilit](/dotnet/api/microsoft.servicebus.messaging.receivemode) modu, alıcı istemcinin alınan iletileri açıkça kapatmak istediğini aracıya söyler. İleti, alıcının işlemesi için kullanılabilir hale getirilir, böylece diğer, rakip alıcıların bunu görebilmesi için hizmette özel bir kilit altında tutulur. Kilit süresi başlangıçta sırada veya abonelik düzeyinde tanımlanır ve [yenilenebilir kilit](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_) işlemi aracılığıyla kilidin sahibi olan istemci tarafından genişletilebilir.

Bir ileti kilitliyken, aynı kuyruktan veya abonelikten alınan diğer istemciler kilitleri alabilir ve etkin kilit altında olmayan bir sonraki kullanılabilir iletiyi alabilir. Bir iletideki kilit açıkça serbest bırakıldığında veya kilidin süresi dolarsa, ileti yeniden teslim edilmek üzere alma sırasının önüne veya yanına doğru bir şekilde yeniden açılır.

İleti alıcılar tarafından tekrararda serbest bırakıldığında veya kilit, belirlenen sayıda kez ([Maxdeliverycount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount#Microsoft_ServiceBus_Messaging_QueueDescription_MaxDeliveryCount)) geçerse, ileti kuyruktan veya abonelikten otomatik olarak kaldırılır ve ilişkili atılacak ileti kuyruğuna yerleştirilir.

Alıcı istemci, API düzeyinde [tamamlanma](/dotnet/api/microsoft.servicebus.messaging.queueclient.complete#Microsoft_ServiceBus_Messaging_QueueClient_Complete_System_Guid_) çağrısı yapıldığında olumlu bir bildirim ile alınan bir iletinin kapatılmasını başlatır. Bu, iletinin başarıyla işlendiği ve iletinin kuyruktan veya abonelikten kaldırıldığı aracıya işaret gösterir. Aracı, kapatmanın gerçekleştirilip gerçekleştirilmeyeceğini belirten bir Yanıt ile alıcının kapatma amacını yanıtlar.

Alıcı istemci bir iletiyi işleyemediğinde ancak iletinin yeniden teslim edilmesini istiyorsa, [Abandon](/dotnet/api/microsoft.servicebus.messaging.queueclient.abandon) 'ı çağırarak iletiyi doğrudan serbest bir şekilde ve kilidi açabilir veya herhangi bir şey yapmaz ve kilitleme geçmesini sağlar.

Alıcı istemci bir iletiyi işleyemezse ve iletiyi yeniden teslim etmeyi ve işlemi yeniden denemeyi biliyorsa, iletiyi reddedebilir ve ayrıca, özel olarak ayarlamaya olanak [tanıyan, bir](/dotnet/api/microsoft.servicebus.messaging.queueclient.deadletter)iletiyi geri alıp atılacak mektup kuyruğuna taşıyabilirsiniz. atılacak ileti sırasından iletiyle alınabilecek bir neden kodu dahil özelliği.

Bir kapanış özel durumu ertelenmiştir ve ayrı bir makalede ele alınmıştır.

**Tam** **veya kaldırılmış bir Işlem ve** **yenilenebilir kilit** işlemleri ağ sorunları nedeniyle başarısız olabilir, bu da tutulan kilidin süresi dolmuşsa veya kapatmayı önleyen başka hizmet tarafı koşulları vardır. İkinci durumlardan birinde, hizmet API istemcilerinde özel durum olarak yüzey sağlayan negatif bir bildirim gönderir. Nedeni bozuk bir ağ bağlantııysa, farklı bir bağlantıda var olan AMQP bağlantılarının kurtarılmasını desteklemediği Service Bus kilit bırakılır.

İşlem **tamamlandığında** genellikle ileti işlemenin çok sonunda ve bazı durumlarda çalışan işlemin süresi dolduktan sonra ortaya çıkan uygulama, iş durumunu korur ve teslim edildiğinde aynı iletiyi yoksayar ikinci bir zaman veya tosses, iş sonucunu verip etmediğini ve iletinin yeniden teslim edildiği şekilde yeniden denemeler yapıp denemediğini belirtir.

Yinelenen ileti teslimlerini tanımlamaya yönelik tipik mekanizma ileti kimliğini denetleyerek, büyük olasılıkla kaynak işlemden tanımlayıcı ile hizalanmış bir şekilde, gönderen tarafından benzersiz bir değere ayarlanmalıdır. Bir iş Zamanlayıcı büyük olasılıkla ileti Kimliğini verilen çalışan bir çalışana atamaya çalıştığı işin tanımlayıcısına ayarlayabilir ve bu iş zaten yapıldıysa çalışan, iş atamasının ikinci oluşumunu yoksayar.

> [!IMPORTANT]
> PeekLock 'in ileti üzerinde elde edilen kilidinin geçici olduğunu ve aşağıdaki koşullarda kaybolabileceğini unutmayın
>   * Hizmet güncelleştirmesi
>   * İşletim sistemi güncelleştirmesi
>   * Kilidi tutarken varlıktaki özellikleri değiştirme (kuyruk, konu, abonelik).
>
> Kilit kaybedildiği zaman, Azure Service Bus istemci uygulama kodu üzerinde ortaya çıkacak bir LockLostException oluşturur. Bu durumda, istemcinin varsayılan yeniden deneme mantığı otomatik olarak başlamalıdır ve işlemi yeniden dener.

## <a name="next-steps"></a>Sonraki adımlar

Service Bus mesajlaşma hakkında daha fazla bilgi edinmek için aşağıdaki konulara bakın:

* [Service Bus kuyrukları, konu başlıkları ve abonelikleri](service-bus-queues-topics-subscriptions.md)
* [Service Bus kuyrukları ile çalışmaya başlama](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus konu başlıklarını ve aboneliklerini kullanma](service-bus-dotnet-how-to-use-topics-subscriptions.md)
