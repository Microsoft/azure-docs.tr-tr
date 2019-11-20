---
title: Azure Service Bus mesajlaşmasına genel bakış | Microsoft Belgeleri
description: Service Bus mesajlaşmasının açıklaması
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: overview
ms.date: 11/04/2019
ms.custom: mvc
ms.author: aschhab
ms.openlocfilehash: e2460ab760811a3db39058eac74d519ca09046c6
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889825"
---
# <a name="what-is-azure-service-bus"></a>Azure Service Bus nedir?

Microsoft Azure Service Bus tam olarak yönetilen bir kurumsal tümleştirme ileti aracısıdır. Service Bus, uygulamaları ve Hizmetleri ayrışırlar. Service Bus, zaman uyumsuz veriler ve durum aktarımı için güvenilir ve güvenli bir platform sunar.

Veriler, farklı uygulamalar ve hizmetler arasında *iletiler* kullanılarak aktarılır. Bir ileti ikili biçimdedir ve JSON, XML veya yalnızca metin içerebilir. Daha fazla bilgi için bkz. [Integration Services](https://azure.com/integration).

Yaygın olarak kullanılan bazı mesajlaşma senaryoları:

* *Mesajlaşma*. Satış veya satın alma siparişleri, Günlükler veya stok hareketleri gibi iş verilerini aktarın.
* *Uygulamaları ayrıştı*. Uygulama ve hizmetlerin güvenilirliğini ve ölçeklenebilirliğini geliştirme. İstemci ve hizmetin aynı anda çevrimiçi olması gerekmez.
* *Konular ve abonelikler*. Yayımcılar ve aboneler arasındaki 1:*n* ilişkilerini etkinleştirin.
* *İleti oturumları*. İleti sıralaması veya ileti ertelemeyi gerektiren iş akışlarını uygulayın.

## <a name="namespaces"></a>Ad Alanları

Ad alanı, tüm mesajlaşma bileşenleri için bir kapsayıcıdır. Tek bir ad alanında birden fazla kuyruk ve konu bulunabilir ve ad alanları genellikle uygulama kapsayıcıları olarak görev yapar.

## <a name="queues"></a>Kuyruklar

İletiler *kuyruklara* gönderilir ve bunlardan alınır. Kuyruklar, alıcı uygulama tarafından alınması ve işlenmesi için kullanılabilir olana kadar iletileri depolar.

![Kuyruk](./media/service-bus-messaging-overview/about-service-bus-queue.png)

Kuyruklarda bulunan iletiler, varış noktasında sıralanır ve zaman damgalanır. Kabul edildikten sonra ileti yedekli depolama alanında güvenli bir şekilde tutulur. İletiler *çekme* moduna alınır ve yalnızca istendiğinde iletileri teslim edilir.

## <a name="topics"></a>Konu başlıkları

İleti göndermek ve almak için *konu başlıklarını* da kullanabilirsiniz. Bir kuyruk genellikle noktadan noktaya iletişim için kullanılır, buna karşın konu başlıkları yayımlama/abone olma senaryolarında yararlıdır.

![Konu](./media/service-bus-messaging-overview/about-service-bus-topic.png)

Konuların birden fazla ve bağımsız abonesi olabilir. Bir konu başlığının bir abonesi konu başlığına gönderilen her iletinin bir kopyasını alabilir. Abonelikler, varlıklar olarak adlandırılır. Abonelikler devam edebilir, ancak süreleri dolacak veya oto silebilir.

Tek tek aboneliklerin, bir konuya gönderilen tüm iletileri almasını istemeyebilirsiniz. Bu durumda, isteğe bağlı *eylemleri*tetikleyen koşulları tanımlamak için *kurallar* ve *Filtreler* kullanabilirsiniz. Belirtilen iletileri filtreleyebilir ve ileti özelliklerini ayarlayabilir ya da değiştirebilirsiniz. Daha fazla bilgi için bkz. [Konu filtreleri ve eylemleri](topic-filters.md).

## <a name="advanced-features"></a>Gelişmiş özellikler

Service Bus, daha karmaşık Mesajlaşma sorunlarını çözmenize olanak sağlayan gelişmiş özellikler içerir. Aşağıdaki bölümlerde bu özelliklerden bazıları açıklanır.

### <a name="message-sessions"></a>İleti oturumları

Service Bus ' de ilk kez ilk çıkar (FıFO) garantisi oluşturmak için oturumları kullanın. İleti oturumları, sınırsız sayıda ilgili ileti dizisinin birleşme ve sıralı işlenmesini sağlar. Daha fazla bilgi için bkz. [ileti oturumları: ilk gelen, ilk çıkar (FIFO)](message-sessions.md).

### <a name="autoforwarding"></a>Oto iletme

Oto iletme özelliği bir kuyruk veya aboneliği başka bir kuyruğa veya konuya zincirler. Aynı ad alanının parçası olmaları gerekir. Otomatik iletme ile, Service Bus iletileri kuyruktan veya abonelikten otomatik olarak kaldırır ve farklı bir kuyruğa veya konuya koyar. Daha fazla bilgi için bkz. [Service Bus varlıklarını oto iletme Ile zincirleme](service-bus-auto-forwarding.md).

### <a name="dead-letter-queue"></a>Atılacak ileti sırası

Service Bus, atılacak bir sırayı (DLQ) destekler. Bir DLCı, hiçbir alıcıya teslim edilemediğinden iletileri barındırır. İşlenemeyen iletileri barındırır. Service Bus, DLCı 'lerden gelen iletileri kaldırmanızı ve bunları incelemenizi sağlar. Daha fazla bilgi için bkz. [Service Bus atılacak ileti sıralarına genel bakış](service-bus-dead-letter-queues.md).

### <a name="scheduled-delivery"></a>Zamanlanmış teslim

Gecikmeli işleme için bir kuyruğa veya konuya iletiler gönderebilirsiniz. Bir işi belirli bir zamanda sistem tarafından işlenmek üzere kullanılabilir hale gelecek şekilde zamanlayabilirsiniz. Daha fazla bilgi için bkz. [zamanlanmış iletiler](message-sequencing.md#scheduled-messages).

### <a name="message-deferral"></a>İleti erteleme

Bir kuyruk veya abonelik istemcisi daha sonra bir ileti almayı erteleyebilirsiniz. Bu erteleme, uygulamadaki özel koşullardan kaynaklanabilir. İleti kuyrukta veya abonelikte kalır, ancak bu şekilde ayarlanır. Daha fazla bilgi için bkz. [ileti erteleme](message-deferral.md).

### <a name="batching"></a>Toplu İşleme

İstemci tarafı toplu işleme, bir kuyruk veya konu istemcisinin belirli bir süre için ileti göndermeyi geciktirmesini sağlar. İstemci bu süre içinde başka iletiler gönderirse, iletileri tek bir toplu iş olarak gönderir. Daha fazla bilgi için bkz. [istemci tarafı toplu işleme](service-bus-performance-improvements.md#client-side-batching).

### <a name="transactions"></a>İşlemler

İşlem iki veya daha fazla işlemi bir *yürütme kapsamında*gruplandırır. Service Bus, tek bir işlemin kapsamındaki tek bir mesajlaşma varlığına karşı işlemleri gruplamayı destekler. İleti varlığı bir kuyruk, konu veya abonelik olabilir. Daha fazla bilgi için bkz. [Service Bus işlem Işlemeye genel bakış](service-bus-transactions.md).

### <a name="filtering-and-actions"></a>Filtreleme ve eylemler

Aboneler, bir konu başlığından hangi iletileri almak istediklerini tanımlayabilir. Bu iletiler bir veya daha fazla adlandırılmış abonelik kuralı biçiminde belirtilir. Her eşleşen kural koşulu için abonelik, her eşleşen kural için farklı şekilde açıklanabileceği iletinin bir kopyasını oluşturur. Daha fazla bilgi için bkz. [Konu filtreleri ve eylemleri](topic-filters.md).

### <a name="autodelete-on-idle"></a>Boşta üzerinde oto silme

Boştayken otomatik silme, bir kuyruğun otomatik olarak silineceği bir boşta kalma aralığı belirtmenize olanak sağlar. En düşük süre 5 dakikadır. Daha fazla bilgi için, bkz. [Queuedescription. oto Deleteonıdle özelliği](/dotnet/api/microsoft.servicebus.messaging.queuedescription.autodeleteonidle).

### <a name="duplicate-detection"></a>Yineleme algılama

Bir hata, istemcinin bir gönderme işleminin sonucu hakkında şüpheli olmasına neden olabilir. Yinelenen algılama, gönderenin aynı iletiyi yeniden göndermesini sağlar. Başka bir seçenek de sıranın veya konunun yinelenen kopyaları atmaları için kullanılır. Daha fazla bilgi için bkz. [yinelenen algılama](duplicate-detection.md).

### <a name="security-protocols"></a>Güvenlik protokolleri
<a name="sas-rbac-and-managed-identities-for-azure-resources"></a>

Service Bus, [Paylaşılan Erişim İmzaları](service-bus-sas.md) (SAS), [Rol Tabanlı Erişim Denetimi](authenticate-application.md) (RBAC) ve [Azure kaynakları için Yönetilen kimlikler](service-bus-managed-service-identity.md) gibi güvenlik protokollerini destekler.

### <a name="geo-disaster-recovery"></a>Coğrafi olağanüstü durum kurtarma

Azure bölgeleri veya veri merkezleri kapalı kalma süresi yaşdığında, coğrafi olağanüstü durum kurtarma, veri işlemenin farklı bir bölgede veya veri merkezinde çalışmaya devam etmesine olanak sağlar. Daha fazla bilgi için bkz. [Azure Service Bus coğrafi olağanüstü durum kurtarma](service-bus-geo-dr.md).

### <a name="security"></a>Güvenlik

Service Bus, standart [AMQP 1.0](service-bus-amqp-overview.md) ve [HTTP/REST](/rest/api/servicebus/) protokollerini destekler.

## <a name="client-libraries"></a>İstemci kitaplıkları

Service Bus [.net](https://github.com/Azure/azure-service-bus-dotnet/tree/master), [Java](https://github.com/Azure/azure-service-bus-java/tree/master)ve [JMS](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client)için istemci kitaplıklarını destekler.

## <a name="integration"></a>Tümleştirme

Service Bus, aşağıdaki Azure hizmetleriyle tam olarak tümleşiktir:

* [Event Grid](https://azure.microsoft.com/services/event-grid/)
* [Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure İşlevleri](https://azure.microsoft.com/services/functions/)
* [Dynamics 365](https://dynamics.microsoft.com)
* [Azure Akış Analizi](https://azure.microsoft.com/services/stream-analytics/)

## <a name="next-steps"></a>Sonraki adımlar

Service Bus mesajlaşmasını kullanmaya başlamak için aşağıdaki makalelere bakın:

* Azure mesajlaşma hizmetlerini karşılaştırmak için bkz. [Hizmetleri karşılaştırma](../event-grid/compare-messaging-services.md?toc=%2fazure%2fservice-bus-messaging%2ftoc.json&bc=%2fazure%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json).
* [.Net](service-bus-dotnet-get-started-with-queues.md), [Java](service-bus-java-how-to-use-queues.md)veya [JMS](service-bus-java-how-to-use-jms-api-amqp.md)için hızlı başlangıç yapmayı deneyin.
* Service Bus kaynaklarını yönetmek için, bkz. [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases).
* Standart ve Premium katmanları ve fiyatları hakkında daha fazla bilgi edinmek için bkz. [Service Bus fiyatlandırması](https://azure.microsoft.com/pricing/details/service-bus/).
* Premium katmanı için performans ve gecikme hakkında bilgi edinmek için bkz. [Premium mesajlaşma](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Premium-Messaging-How-fast-is-it/ba-p/370722).
