---
title: Kesintileri ve olağanüstü durumlara karşı yalıtılmış Azure Service Bus uygulamalar
description: Bu makaleler, uygulamaları potansiyel bir Azure Service Bus kesintiye karşı korumak için teknikler sağlar.
ms.topic: article
ms.date: 02/10/2021
ms.openlocfilehash: b9090a54cd58788dbd13f528af4dda4aa96005b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100374601"
---
# <a name="best-practices-for-insulating-applications-against-service-bus-outages-and-disasters"></a>Uygulamaları Service Bus kesintilerine ve olağanüstü durumlarına karşı dayanıklı hale getirmek için en iyi yöntemler

Görev açısından kritik uygulamalar, planlanmamış kesintiler veya olağanüstü durumlar da dahil olmak üzere sürekli olarak çalışmalıdır. Bu makalede, Service Bus uygulamalarını olası bir hizmet kesintisi veya olağanüstü duruma karşı korumak için kullanabileceğiniz teknikler açıklanmaktadır.

Kesinti, Azure Service Bus geçici olarak kullanım dışı olarak tanımlanır. Kesinti, bir mesajlaşma deposu gibi bazı Service Bus bileşenlerini veya hatta tüm veri merkezini etkileyebilir. Sorun giderildikten sonra Service Bus yeniden kullanılabilir hale gelir. Genellikle kesinti, ileti veya diğer verilerin kaybedilmesine neden olmaz. Bir bileşen hatasına bir örnek, belirli bir mesajlaşma deposunun kullanım dışı olmamasından oluşur. Veri merkezi 'nin bir kesinti örneği, veri merkezinde veya hatalı bir veri merkezi ağ anahtarının güç hatasıdır. Bir kesinti en son birkaç dakikadan birkaç güne kadar sürebilir.

Olağanüstü durum, Service Bus ölçek birimi veya veri merkezinin kalıcı kaybı olarak tanımlanır. Veri merkezi bir kez daha kullanılabilir duruma gelebilir veya kullanılamıyor olabilir. Genellikle bir olağanüstü durum, iletilerin veya diğer verilerin kaybedilmesine neden olur. Olağanüstü durumlar örnekleri ateş, taşması veya deprem.

## <a name="protecting-against-outages-and-disasters---service-bus-premium"></a>Kesintilere ve olağanüstü durumlara karşı koruma-Service Bus Premium
Yüksek kullanılabilirlik ve olağanüstü durum kurtarma kavramları, her ikisi de aynı bölgedeki (Kullanılabilirlik Alanları aracılığıyla) ve farklı bölgelerde (Geo-Disaster kurtarma aracılığıyla) Azure Service Bus Premium katmanda yerleşiktir.

### <a name="geo-disaster-recovery"></a>Geo-Disaster kurtarma

Service Bus Premium, ad alanı düzeyinde coğrafi olağanüstü durum kurtarmayı destekler. Daha fazla bilgi için bkz. [Azure Service Bus coğrafi olağanüstü durum kurtarma](service-bus-geo-dr.md). Yalnızca [PREMIUM SKU](service-bus-premium-messaging.md) için kullanılabilen olağanüstü durum kurtarma özelliği, meta veri olağanüstü durum kurtarma uygular ve birincil ve ikincil olağanüstü durum kurtarma ad alanlarını kullanır. Geo-Disaster kurtarmayla, birincil ve ikincil ad alanları arasında yalnızca varlık meta verileri çoğaltılır.  

### <a name="availability-zones"></a>Kullanılabilirlik Alanları

Service Bus Premium SKU, aynı Azure bölgesi içinde hata yalıtılmış konumlar sağlayarak [kullanılabilirlik alanları](../availability-zones/az-overview.md)destekler. Service Bus ileti deposunun üç kopyasını yönetir (1 birincil ve 2 ikincil). Service Bus veri ve yönetim işlemleri için eşitlerdeki tüm üç kopyayı tutar. Birincil kopya başarısız olursa, ikincil kopyalardan biri, algılanan kapalı kalma süresi olmadan birinciye yükseltilir. Uygulamalar Service Bus geçici kesilen bağlantıları görüyorsanız, SDK 'daki yeniden deneme mantığı Service Bus otomatik olarak yeniden bağlanır. 

Kullanılabilirlik bölgelerini kullandığınızda, hem meta veriler hem de veriler (iletiler) kullanılabilirlik bölgesindeki veri merkezlerinde çoğaltılır. 

> [!NOTE]
> Azure Service Bus Premium için Kullanılabilirlik Alanları desteği yalnızca kullanılabilirlik alanlarının bulunduğu [Azure bölgelerinde](../availability-zones/az-region.md) kullanılabilir.

Azure portal kullanarak yalnızca yeni ad alanlarında Kullanılabilirlik Alanları etkinleştirebilirsiniz. Service Bus var olan ad alanlarının geçişini desteklemez. Ad alanınız üzerinde etkinleştirildikten sonra bölge yedekliliği devre dışı bırakılamaz.

![1][]


## <a name="protecting-against-outages-and-disasters---service-bus-standard"></a>Kesintilere ve olağanüstü durumlara karşı koruma-Service Bus standart
Standart Mesajlaşma fiyatlandırma katmanını kullanırken veri merkezi kesintilerine karşı esnekliği ulaşmak için Service Bus iki yaklaşımdan birini destekler: *etkin* ve *Pasif* çoğaltma. Her yaklaşım için, belirli bir kuyruk veya konu, bir veri merkezi kesintisi olması durumunda erişilebilir durumda kalırsa, her iki ad alanında da oluşturabilirsiniz. Her iki varlık de aynı ada sahip olabilir. Örneğin, **contosoPrimary.ServiceBus.Windows.net/myQueue** altında birincil bir sıraya ulaşılırsa, ikincil karşılığı **contosoSecondary.ServiceBus.Windows.net/myQueue** altına ulaşılmış olabilir.

>[!NOTE]
> **Etkin çoğaltma** ve **Pasif çoğaltma** kurulumu, Service Bus belirli özellikleri değil genel amaçlı çözümlerdir. Çoğaltma mantığı (2 farklı ad alanına gönderme) Gönderici uygulamalarında bulunur ve alıcının yinelenen algılama için özel bir mantığı olması gerekir.

Uygulama kalıcı gönderici alıcıya iletişim gerektirmiyorsa, uygulama ileti kaybını engellemek ve göndereni geçici Service Bus hatalarından korumak için dayanıklı bir istemci tarafı kuyruğu uygulayabilir.

### <a name="active-replication"></a>Etkin çoğaltma
Etkin çoğaltma, her bir işlem için her iki ad alanındaki varlıkları kullanır. İleti gönderen tüm istemciler aynı iletinin iki kopyasını gönderir. İlk kopya birincil varlığa gönderilir (örneğin, **contosoPrimary.ServiceBus.Windows.net/Sales**) ve iletinin ikinci kopyası ikincil varlığa gönderilir (örneğin, **contosoSecondary.ServiceBus.Windows.net/Sales**).

İstemci her iki kuyruktan de ileti alır. Alıcı bir iletinin ilk kopyasını işler ve ikinci kopya bastırılır. Yinelenen iletileri gizlemek için, gönderenin her iletiyi benzersiz bir tanımlayıcıyla etiketmaları gerekir. İletinin her iki kopyasının de aynı tanımlayıcıyla etiketlenmesi gerekir. İletiyi etiketlemek için [brokeredmessage. MessageId][BrokeredMessage.MessageId] veya [Brokeredmessage. Label][BrokeredMessage.Label] özelliklerini ya da özel bir özelliği kullanabilirsiniz. Alıcı, zaten aldığı iletilerin bir listesini korumalıdır.

[Service Bus Standart katman örneği Ile coğrafi çoğaltma][Geo-replication with Service Bus Standard Tier] , mesajlaşma varlıklarının etkin çoğaltmasını gösterir.

> [!NOTE]
> Etkin çoğaltma yaklaşımı işlem sayısını iki katına çıkarır, bu nedenle bu yaklaşım daha yüksek maliyetli olabilir.
> 
> 

### <a name="passive-replication"></a>Pasif çoğaltma
Hata serbest durumda, pasif çoğaltma iki mesajlaşma varlıklarından yalnızca birini kullanır. İstemci, iletiyi etkin varlığa gönderir. Etkin varlıktaki işlem, etkin varlığı barındıran veri merkezinin devre dışı bırakıldığını belirten bir hata koduyla başarısız olursa, istemci, iletinin bir kopyasını yedekleme varlığına gönderir. Bu noktada, etkin ve yedekleme varlıkları anahtar rolleri: gönderen istemci eski etkin varlığı yeni yedekleme varlığı olarak değerlendirir ve eski yedekleme varlığı yeni etkin varlıktır. Gönderme işlemlerinin her ikisi de başarısız olursa, iki varlığın rolleri değişmeden kalır ve bir hata döndürülür.

İstemci her iki kuyruktan de ileti alır. Alıcının aynı iletinin iki kopyasını alması olasılığı olduğu için alıcı, yinelenen iletileri göstermelidir. Yinelenenleri, etkin çoğaltma için açıklananla aynı şekilde gizleyebilirsiniz.

Genel olarak, pasif çoğaltma, çoğu durumda yalnızca bir işlem gerçekleştirildiğinden etkin çoğaltmadan daha ekonomik olur. Gecikme süresi, aktarım hızı ve parasal maliyet, çoğaltılmayan senaryoyla aynıdır.

Pasif çoğaltma kullanılırken, aşağıdaki senaryolarda iletiler kaybolabilir veya iki kez alınabilir:

* **İleti gecikmesi veya kaybı**: gönderenin, birincil kuyruğa M1 iletisini başarıyla gönderdiğini ve alıcı M1 'i almadan önce sıranın kullanılamaz hale geldiğini varsayın. Gönderen, ikincil sıraya bir sonraki ileti m2 iletisi gönderir. Birincil sıra geçici olarak kullanılamıyorsa, kuyruk yeniden kullanılabilir duruma geldikten sonra alıcı M1 'yi alır. Bir olağanüstü durum durumunda alıcı hiçbir şekilde M1 'yi alamayabilir.
* **Yinelenen Alım**: gönderenin birincil kuyruğa m iletisi göndereceğini varsayın. Service Bus başarılı bir şekilde işleme, ancak yanıt gönderemediğinde. Gönderme işlemi zaman aşımına uğrarsa, gönderici aynı m kopyasını ikincil kuyruğa gönderir. Alıcı, birincil sıra kullanılamaz hale gelmeden önce ilk m kopyasını alabilirse, alıcı her iki kopyayı da yaklaşık olarak aynı anda alır. Alıcı, birincil sıra kullanılamaz hale gelmeden önce ilk m kopyasını alabilirse, alıcı başlangıçta yalnızca ikinci bir m kopyasını alır, ancak birincil sıra kullanılabilir olduğunda ikinci bir m kopyası alır.

[Service Bus Standart katman örneği Ile coğrafi çoğaltma][Geo-replication with Service Bus Standard Tier] , mesajlaşma varlıklarının pasif çoğaltmasını gösterir.

## <a name="protecting-relay-endpoints-against-datacenter-outages-or-disasters"></a>Geçiş uç noktalarını veri merkezi kesintileri veya olağanüstü durumlara karşı koruma
[Azure Relay](../azure-relay/relay-what-is-it.md) uç noktaların coğrafi çoğaltma, bir geçiş uç noktasının Service Bus kesintileri olması durumunda ulaşılabilir olmasını sağlayan bir hizmete izin verir. Coğrafi çoğaltmanın elde edilebilmesi için hizmetin farklı ad alanlarında iki geçiş uç noktası oluşturması gerekir. Ad alanları farklı veri merkezlerinde bulunmalı ve iki uç noktanın farklı adlara sahip olması gerekir. Örneğin, **contosoPrimary.ServiceBus.Windows.net/myPrimaryService** altında birincil bir uç noktaya ulaşılırsa, ikincil karşılığı **contosoSecondary.ServiceBus.Windows.net/mySecondaryService** altına ulaşılmış olabilir.

Daha sonra hizmet her iki uç nokta üzerinde dinler ve bir istemci, hizmeti uç noktası aracılığıyla çağırabilir. İstemci uygulaması, geçişleri birincil uç nokta olarak rastgele seçer ve isteği etkin uç noktaya gönderir. İşlem hata kodu ile başarısız olursa, bu hata geçiş uç noktasının kullanılabilir olmadığını gösterir. Uygulama, yedekleme uç noktasına bir kanal açar ve isteği yeniden yayınlar. Bu noktada, etkin ve yedekleme uç noktaları, rol Değiştir: istemci uygulaması eski etkin uç noktayı yeni yedekleme uç noktası olarak ve eski yedekleme uç noktasını yeni etkin uç nokta olacak şekilde değerlendirir. Gönderme işlemlerinin her ikisi de başarısız olursa, iki varlığın rolleri değişmeden kalır ve bir hata döndürülür.

## <a name="next-steps"></a>Sonraki adımlar
Olağanüstü durum kurtarma hakkında daha fazla bilgi için şu makalelere bakın:

* [Azure Service Bus coğrafi olağanüstü durum kurtarma](service-bus-geo-dr.md)
* [Azure SQL veritabanı Iş sürekliliği][Azure SQL Database Business Continuity]
* [Azure için dayanıklı uygulamalar tasarlama][Azure resiliency technical guidance]

[Service Bus Authentication]: service-bus-authentication-and-authorization.md
[Partitioned messaging entities]: service-bus-partitioning.md
[Asynchronous messaging patterns and high availability]: service-bus-async-messaging.md#failure-of-service-bus-within-an-azure-datacenter
[BrokeredMessage.MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[BrokeredMessage.Label]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Geo-replication with Service Bus Standard Tier]: https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoReplication
[Azure SQL Database Business Continuity]:../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md
[Azure resiliency technical guidance]: /azure/architecture/resiliency

[1]: ./media/service-bus-outages-disasters/az.png