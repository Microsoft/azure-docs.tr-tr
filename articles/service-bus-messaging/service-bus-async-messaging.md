---
title: Zaman uyumsuz mesajlaşma Service Bus | Microsoft Docs
description: Kuyruklar, konular ve aboneliklerle bir mağaza ve ileriye yönelik bir mekanizma aracılığıyla Azure Service Bus nasıl destekleyeceğinizi öğrenin.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: e37c18b95bca7ef1e6e8f0d74976bb73b214624a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102500640"
---
# <a name="asynchronous-messaging-patterns-and-high-availability"></a>Zaman uyumsuz mesajlaşma desenleri ve yüksek kullanılabilirlik

Zaman uyumsuz mesajlaşma birçok farklı yolla uygulanabilir. Kuyruklar, konular ve abonelikler sayesinde, Azure Service Bus bir mağaza ve ileriye doğru mekanizma aracılığıyla zaman eşitlemesini destekler. Normal (zaman uyumlu) işleminde, sıralara ve konulara iletiler gönderir ve kuyruklar ve aboneliklerden iletiler alırsınız. Yazdığınız uygulamalar, her zaman kullanılabilir olan bu varlıklara bağlıdır. Çeşitli koşullar nedeniyle varlık durumu değiştiğinde, çoğu ihtiyacı karşılayabilen daha düşük bir yetenek varlığı sağlamak için bir yol gerekir.

Uygulamalar genellikle zaman uyumsuz mesajlaşma düzenlerini kullanarak bir dizi iletişim senaryosunu etkinleştirir. Hizmetin çalışmıyor olsa bile, istemcilerin hizmetlere ileti gönderebileceği uygulamalar oluşturabilirsiniz. Bir kuyruk, iletişimin ani bir kısmını yaşayan uygulamalar için arabellek iletişimleri için bir yer sağlayarak yükün sağlanmasına yardımcı olabilir. Son olarak, birden çok makineye ileti dağıtmak için basit ancak etkin bir yük dengeleyici edinebilirsiniz.

Bu varlıkların herhangi birinin kullanılabilirliğini sürdürmek için, bu varlıkların dayanıklı bir mesajlaşma sistemi için kullanılamaz şekilde görünebileceği birçok farklı yolu göz önünde bulundurun. Genellikle, aşağıdaki farklı yollarla yazdığımız uygulamalar için varlık kullanılamaz hale geldiğini görüyoruz:

* Mesajlar gönderilemiyor.
* İleti alınamıyor.
* Varlıklar yönetimedi (varlık oluşturma, alma, güncelleştirme veya silme).
* Hizmetle iletişim kurulamıyor.

Bu hatalardan her biri için, bir uygulamanın, daha düşük bir özellik düzeyinde iş gerçekleştirmeye devam etmesine olanak tanıyan farklı hata modları vardır. Örneğin, iletileri gönderebilen ancak almamayan bir sistem yine de müşterilerden sipariş alabilir, ancak bu siparişleri işleyemez. Bu konu, gerçekleşebileceği olası sorunları ve bu sorunların nasıl azaltıldığı hakkında ele alınmaktadır. Service Bus, kabul etmeniz gereken bir dizi azaltmayı sunmuştur ve bu konuda Ayrıca bu katılım azaltmaları kullanımını düzenleyen kurallar ele alınmaktadır.

## <a name="reliability-in-service-bus"></a>Service Bus güvenilirlik
İleti ve varlık sorunlarını ele almanın birkaç yolu vardır ve bu azaltıcı etkenlerin uygun kullanımını düzenleyen yönergeler vardır. Yönergeleri anlamak için öncelikle Service Bus başarısız olduğunu anlamanız gerekir. Azure sistemlerinin tasarımı nedeniyle, tüm bu sorunlar kısa süreli olarak eğilimlidir. Yüksek düzeyde, kullanılamaın farklı nedenleri şu şekilde görünür:

* Service Bus bağlı bir dış sistemden azaltma. Depolama ve işlem kaynaklarıyla etkileşimi, kısıtlama oluşur.
* Service Bus bağımlı olan bir sistem için sorun. Örneğin, belirli bir depolama bölümünde sorunlar oluşabilir.
* Tek alt sistemde Service Bus hatası. Bu durumda, bir işlem düğümü tutarsız duruma alabilir ve kendisini yeniden başlatması gerekir, bu da diğer düğümlere yük dengelemeye hizmet verdiği tüm varlıklara neden olur. Bu işlem, kısa bir süre yavaş ileti işlemeye neden olabilir.
* Bir Azure veri merkezi içinde Service Bus hatası. Bu, sistemin birçok dakika veya birkaç saat boyunca ulaşılamaz olduğu bir "yıkıcı hatası" olur.

> [!NOTE]
> **Depolama** terimi hem Azure Storage hem de SQL Azure anlamına gelebilir.
> 
> 

Service Bus, bu sorunlar için bir dizi azaltmaları içerir. Aşağıdaki bölümlerde her bir sorun ve ilgili azaltmaları ele alınmaktadır.

### <a name="throttling"></a>Azaltma
Service Bus, azaltma, birlikte çalışmayan ileti hızı yönetimine izin vermez. Her bireysel Service Bus düğümü birçok varlık barındırır. Bu varlıkların her biri, sistem üzerinde CPU, bellek, depolama ve diğer modellerle ilgili talepler sağlar. Bu modellerden herhangi biri, tanımlanan eşikleri aşan kullanımı algıladığında, Service Bus belirli bir isteği reddedebilirler. Çağıran bir [Serverbusi özel durumu][ServerBusyException] alır ve 10 saniye sonra yeniden dener.

Risk azaltma olarak, kodun hatayı okuması ve iletiyi en az 10 saniye için yeniden deneme süresini durdurmalıdır. Hata müşteri uygulamasının parçaları arasında gerçekleşediğinden, her parçanın yeniden deneme mantığını bağımsız olarak yürütmesi beklenmektedir. Kod, bir kuyruk veya konu üzerinde bölümlemeyi etkinleştirerek daraltameme olasılığını azaltabilir.

### <a name="issue-for-an-azure-dependency"></a>Azure bağımlılığı sorunu
Azure 'daki diğer bileşenlere bazen hizmet sorunları olabilir. Örneğin, Service Bus tarafından kullanılan bir sistem yükseltildiğinde, bu sistem geçici olarak daha az yetenek yaşayabilir. Bu tür sorunları geçici olarak çözmek için Service Bus düzenli olarak araştırır ve azaltmaları uygular. Bu azaltmaları yan etkileri görüntülenir. Örneğin, depolamada geçici sorunları işlemek için, Service Bus ileti gönderme işlemlerinin sürekli olarak çalışmasına izin veren bir sistem uygular. Risk azaltma doğası gereği, gönderilen bir ileti etkilenen kuyrukta veya abonelikte görünmesi ve bir alma işlemi için hazırlanabileceği 15 dakikaya kadar sürebilir. Genellikle, çoğu varlık bu sorunla karşılaşmaz. Ancak, Azure 'daki Service Bus varlıkların sayısı verildiğinde, bu hafifletme bazı durumlarda Service Bus müşterilerinin küçük bir alt kümesi için gerekir.

### <a name="service-bus-failure-on-a-single-subsystem"></a>Tek bir alt sistemde hata Service Bus
Herhangi bir uygulamayla, koşullar Service Bus iç bileşeninin tutarsız hale gelmesine neden olabilir. Service Bus bunu algıladığında, ne olduğunu tanılamaya yardımcı olmak için uygulamadan veri toplar. Veriler toplandıktan sonra, uygulamayı tutarlı bir duruma döndürme girişiminde yeniden başlatılır. Bu işlem oldukça hızlı gerçekleşir ve bir varlığın birkaç dakikaya kadar kullanılamaz şekilde görünmesine neden olur, ancak tipik aşağı doğru süreler çok daha kısadır.

Bu durumlarda, istemci uygulama bir [System. TimeoutException][System.TimeoutException] veya [messagingexception][MessagingException] özel durumu oluşturur. Service Bus, bu soruna yönelik otomatik istemci yeniden deneme mantığı biçiminde bir azaltma işlemi içerir. Yeniden deneme süresi tükendikten ve ileti teslim edilmeden, [kesintileri ve olağanüstü durumları işleme][handling outages and disasters]makalesinde bahsedilen diğer kullanımı inceleyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
Artık Service Bus zaman uyumsuz mesajlaşma hakkında temel bilgileri öğrendiğinize göre, [kesintileri ve olağanüstü durumları işleme][handling outages and disasters]hakkında daha fazla bilgi edinin.

[ServerBusyException]: /dotnet/api/microsoft.servicebus.messaging.serverbusyexception
[System.TimeoutException]: /dotnet/api/system.timeoutexception
[MessagingException]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Microsoft.ServiceBus.Messaging.MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[MessageReceiver]: /dotnet/api/microsoft.servicebus.messaging.messagereceiver
[QueueClient]: /dotnet/api/microsoft.servicebus.messaging.queueclient
[TopicClient]: /dotnet/api/microsoft.servicebus.messaging.topicclient
[Microsoft.ServiceBus.Messaging.PairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.pairednamespaceoptions
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[SendAvailabilityPairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[PairNamespaceAsync]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[EnableSyphon]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[System.TimeSpan.Zero]: /dotnet/api/system.timespan.zero
[IsTransient]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[UnauthorizedAccessException]: /dotnet/api/system.unauthorizedaccessexception
[BacklogQueueCount]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[handling outages and disasters]: service-bus-outages-disasters.md