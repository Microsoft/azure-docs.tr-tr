---
title: İleti oturumlarını Azure Service Bus | Microsoft Docs
description: Oturumlardaki Azure Service Bus ileti dizilerini işleyin.
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
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 7264b8e5a536c90d106b3bf4a5e26093744327d6
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091821"
---
# <a name="message-sessions-first-in-first-out-fifo"></a>İleti oturumları: ilk gelen, ilk çıkar (FıFO) 

Microsoft Azure Service Bus oturumlar, sınırsız sayıda ilgili ileti dizisinin birleşme ve sıralı işlenmesini sağlar. Service Bus bir FıFO garantisi sağlamak için oturumları kullanın. Service Bus, iletiler arasındaki ilişkinin doğası hakkında görünmez ve ayrıca bir ileti sırasının nerede başlatıldığını veya bittiğini belirlemek için belirli bir model tanımlamaz.

> [!NOTE]
> Service Bus temel katmanı oturumları desteklemez. Standart ve Premium katmanlar oturumları destekler. Daha fazla bilgi için bkz. [Service Bus fiyatlandırması](https://azure.microsoft.com/pricing/details/service-bus/).

Herhangi bir gönderen, [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) özelliğini oturum için benzersiz olan uygulama tanımlı bazı tanımlayıcılarla ayarlayarak bir konuya veya kuyruğa ileti gönderirken bir oturum oluşturabilir. AMQP 1,0 protokol düzeyinde, bu değer *Grup Kimliği* özelliğine eşlenir.

Oturum kullanan kuyruklarda veya aboneliklerde oturum, oturumun [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId)'sini içeren en az bir ileti olduğunda, oturumlar varlığına gelir. Bir oturum varsa, oturumun süresinin dolması veya kaybolması durumunda için tanımlı bir zaman veya API yoktur. Teorik olarak, bugün bir oturum için bir ileti alınabilir, bir yılda bir sonraki ileti ve **SessionID** eşleşiyorsa, oturum Service Bus perspektifinden aynı olur.

Bununla birlikte, genellikle bir uygulama, ilgili bir ileti kümesinin başladığı ve bittiği yerde açık bir kavram içerir. Belirli bir kural Service Bus.

Bir dosyanın aktarılması için bir diziyi belirleme örneği, ilk iletinin **Başlangıç**olarak, ara iletilerin **içeriğe**ve son Iletinin **sona erdirmek**için **Label** özelliğini ayarlamaya yönelik bir örnektir. İçerik iletilerinin göreli konumu, *SequenceNumber* **Başlangıç** iletisinden Delta *SequenceNumber*geçerli ileti olarak hesaplanabilir.

Service Bus oturum özelliği, C# ve Java API 'Lerinde [messagesession](/dotnet/api/microsoft.servicebus.messaging.messagesession) biçiminde belirli bir alma işlemini mümkün bir şekilde sunar. Azure Resource Manager aracılığıyla ya da portalda bayrağını ayarlayarak ya da abonelik veya abonelikte [RequiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) özelliğini ayarlayarak özelliği etkinleştirebilirsiniz. İlgili API işlemlerini kullanmayı denemeden önce bu gereklidir.

Portalda bayrağı aşağıdaki onay kutusuyla ayarlayın:

![][2]

> [!NOTE]
> Bir kuyrukta veya abonelikte Oturumlar etkinleştirildiğinde, istemci uygulamalar ***artık*** normal iletileri gönderemez/alamaz. Tüm iletilerin bir oturumun parçası olarak gönderilmesi (oturum kimliği ayarlanarak) ve oturum alarak alınmalıdır.

Kuyruk ve abonelik istemcilerinde oturum için API 'Ler mevcuttur. Oturum ve mesajların ne zaman alındığını denetleyen bir zorunlu model ve alma döngüsünü yönetmenin karmaşıklığını gizleyen bir işleyici tabanlı model de *OnMessage*ile benzerdir.

## <a name="session-features"></a>Oturum özellikleri

Oturumlar, sıralı teslimi korurken ve garanti edilirken, araya eklemeli ileti akışlarının eşzamanlı olarak birden çok kullanımını sağlar.

![][1]

İstemci tarafından oturum kabul eden bir [Messagesession](/dotnet/api/microsoft.servicebus.messaging.messagesession) alıcısı oluşturulur. İstemci [queueclient. acceptmessagesession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) veya [Queueclient. AcceptMessageSessionAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync) ' C#i çağırır. Reaktif geri çağırma modelinde bir oturum işleyicisi kaydeder.

[Messagesession](/dotnet/api/microsoft.servicebus.messaging.messagesession) nesnesi kabul edildiğinde ve bir istemci tarafından tutulurken, bu istemci kuyrukta veya abonelikte var olan [SessionID](/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid#Microsoft_ServiceBus_Messaging_MessageSession_SessionId) 'ye sahip tüm Iletilerde ve ayrıca bu SessionID 'ye sahip tüm iletilerde özel bir kilit barındırırbu, oturum tutulurken yine de ulaşır.

**Close** veya **CloseAsync** çağrıldığında kilit serbest bırakılır veya kilit, uygulamanın kapatma işlemini gerçekleştiremedik durumlarda zaman aşımına uğrar. Oturum kilidi bir dosya üzerinde özel bir kilit gibi değerlendirilmelidir, yani uygulamanın oturumu artık gerektirmez ve/veya başka ileti beklemediğinde oturumu kapatması gerekir.

Kuyruktan birden çok eşzamanlı alıcı geldiğinde, belirli bir oturuma ait iletiler, o oturum için kilidi Şu anda tutan belirli bir alıcıya dağıtılır. Bu işlemle, bir kuyrukta veya abonelikte bulunan bir araya eklemeli ileti akışı, farklı alıcılar için düzgün şekilde çoğullanmış ve bu alıcılar farklı istemci makinelerde da kullanılabilir, çünkü kilit Yönetimi hizmet tarafı Service Bus.

Önceki çizimde üç eşzamanlı oturum alıcısı gösterilmektedir. = 4 olan `SessionId` bir oturumda etkin, sahip olmayan bir istemci yoktur ve bu belirli oturumdan hiçbir ileti teslim edimediği anlamına gelir. Bir oturum, alt sıra gibi birçok şekilde davranır.

Oturum alıcısı tarafından tutulan oturum kilidi, *Peek-kilit* kapatma modu tarafından kullanılan ileti kilitleri için bir şemsiye. Alıcının aynı anda "uçuş" olarak iki mesajı olamaz, ancak iletilerin sırayla işlenmesi gerekir. Yeni bir ileti yalnızca önceki ileti tamamlandığında veya kullanımdan alınamadığı zaman elde edilebilir. Bir iletiyi terk etmek, bir sonraki alma işlemiyle aynı iletinin yeniden sunulmasını sağlar.

## <a name="message-session-state"></a>İleti oturumu durumu

İş akışları yüksek ölçekli, yüksek kullanılabilirliğe sahip bulut sistemlerinde işlendiğinde, belirli bir oturumla ilişkili iş akışı işleyicisi beklenmedik hatalardan kurtulabilmelidir ve ayrıca kısmen tamamlanmış çalışmayı farklı bir şekilde sürdürüyor olabilir İşin başladığı bir işlem veya makine.

Oturum durumu özelliği, bu oturuma göre kaydedilen işleme durumunun, oturum yeni bir işlemci tarafından edinildiği zaman anında kullanılabilir hale gelmesi için, aracı içindeki bir ileti oturumunun uygulama tanımlı ek açıklamasına olanak sağlar.

Service Bus perspektifinden ileti oturumu durumu, Service Bus standart için 256 KB ve Service Bus Premium için 1 MB olan bir ileti boyutunun verilerini tutan donuk bir ikili nesnedir. Bir oturuma göre işleme durumu oturum durumu içinde tutulabilir veya oturum durumu, bu tür bilgileri tutan bir depolama konumunu veya veritabanı kaydını işaret edebilir.

Oturum durumunu yönetmeye yönelik API 'Ler, [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) ve [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState), hem C# hem de Java API 'lerinde [messagesession](/dotnet/api/microsoft.servicebus.messaging.messagesession) nesnesinde bulunabilir. Daha önce oturum durumu kümesi olmayan bir oturum, **GetState**için **null** bir başvuru döndürür. Daha önce ayarlanan oturum durumunu Temizleme işlemi [setstate (null)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_)ile yapılır.

Oturum durumunun, bir oturumdaki tüm iletiler tüketilse bile temizlenmediğinden ( **null**döndürüldüğüne) kaldığı sürece kaldığını unutmayın.

Bir kuyruktaki veya abonelikteki tüm mevcut oturumlar, Java API 'sinde **Sessionbrowser** yöntemiyle ve .net Istemcisinde [Queueclient](/dotnet/api/microsoft.azure.servicebus.queueclient) ve [Subscriptionclient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) üzerinde [getmessagesessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions) ile listelenebilir.

Bir kuyrukta veya abonelikte tutulan oturum durumu, söz konusu varlığın depolama kotasına doğru sayılır. Uygulama bir oturumla bittiğinde, bu nedenle uygulamanın, dış yönetim maliyetinden kaçınmak için korunan durumunu temizlemesi önerilir.

## <a name="impact-of-delivery-count"></a>Teslimat sayısının etkisi

Oturum bağlamındaki ileti başına teslim sayısı tanımı, bağlanılabilirliğinin olmaması ' deki tanımdan biraz farklılık gösterir. Burada, teslim sayısı arttırılır.

| Senaryo | İletinin teslim sayısı arttırılır |
|----------|---------------------------------------------|
| Oturum kabul edildi, ancak oturum kilidinin süresi dolduğunda (zaman aşımı nedeniyle) | Evet |
| Oturum kabul edildi, oturumdaki iletiler (kilitli olsalar bile) tamamlanmaz ve oturum kapalı | Hayır |
| Oturum kabul edildi, iletiler tamamlandı, sonra oturum açık olarak kapalı | Yok (Standart akışdır. Buradan iletiler oturumdan kaldırılır) |

## <a name="next-steps"></a>Sonraki adımlar

- Oturum kullanan iletileri işlemek için .NET Framework istemcisini kullanan bir örnek için [Microsoft. Azure. ServiceBus örnekleri](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/Sessions) veya [Microsoft. ServiceBus. Messaging örneklerine](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions) bakın. 

Service Bus mesajlaşma hakkında daha fazla bilgi edinmek için aşağıdaki konulara bakın:

* [Service Bus kuyrukları, konu başlıkları ve abonelikleri](service-bus-queues-topics-subscriptions.md)
* [Service Bus kuyrukları ile çalışmaya başlama](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus konu başlıklarını ve aboneliklerini kullanma](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png
