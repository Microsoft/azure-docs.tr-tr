---
title: 'Hızlı başlangıç: Ruby ile Azure Service Bus kuyruklarını kullanma'
description: "Hızlı başlangıç: Azure 'da Service Bus kuyruklarını nasıl kullanacağınızı öğrenin. Ruby ile yazılan kod örnekleri."
services: service-bus-messaging
documentationcenter: ruby
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 0a11eab2-823f-4cc7-842b-fbbe0f953751
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: aschhab
ms.openlocfilehash: 09fdc58254d260b6ffeff958b6bbda50332adfac
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718763"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-ruby"></a>Hızlı başlangıç: Ruby ile Service Bus kuyruklarını kullanma

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Bu öğreticide, Service Bus kuyruğuna ileti göndermek ve ileti almak için Ruby uygulamaları oluşturmayı öğreneceksiniz. Örnekler Ruby dilinde yazılır ve Azure GED 'yi kullanır.

## <a name="prerequisites"></a>Ön koşullar
1. Azure aboneliği. Bu öğreticiyi tamamlamak için bir Azure hesabınızın olması gerekir. [MSDN abone avantajlarınızı](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) etkinleştirebilir veya [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)için kaydolabilirsiniz.
2. [Service Bus kuyruğu oluşturmak için Azure Portal kullanma](service-bus-quickstart-portal.md) adımlarını izleyin.
    1. Service Bus **kuyruklara**hızlı **genel bakış** konusunu okuyun. 
    2. Service Bus **ad alanı**oluşturun. 
    3. **Bağlantı dizesini**alın. 

        > [!NOTE]
        > Bu öğreticide Ruby kullanarak Service Bus ad alanında bir **sıra** oluşturacaksınız. 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="how-to-create-a-queue"></a>Sıra oluşturma
**Azure:: ServiceBusService** nesnesi kuyruklarla çalışmanıza olanak sağlar. Bir kuyruk oluşturmak için `create_queue()` yöntemi kullanın. Aşağıdaki örnek bir kuyruk oluşturur veya herhangi bir hata yazdırır.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

Ayrıca, canlı veya en fazla sıra boyutu gibi varsayılan sıra ayarlarını geçersiz kılmanızı sağlayan ek seçeneklerle bir **Azure:: ServiceBus:: Queue** nesnesi geçirebilirsiniz. Aşağıdaki örnek, en fazla sıra boyutunun 5 GB ve yaşam süresi olarak 1 dakikaya nasıl ayarlanacağını gösterir:

```ruby
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## <a name="how-to-send-messages-to-a-queue"></a>Bir kuyruğa ileti gönderme
Uygulamanız bir Service Bus kuyruğuna ileti göndermek için **Azure:: ServiceBusService** nesnesinde `send_queue_message()` yöntemini çağırır. Service Bus sıralara gönderilen (ve alınan) iletiler **Azure:: ServiceBus:: BrokeredMessage** nesneleri, ve uygulamaya özgü özel özellikleri tutmak için kullanılan bir dizi standart özellik (`label` ve `time_to_live`) vardır ve rastgele uygulama verileri gövdesi. Bir uygulama, ileti olarak bir dize değeri geçirerek ileti gövdesini ayarlayabilir ve gerekli standart özellikler varsayılan değerlerle doldurulur.

Aşağıdaki örnek, `send_queue_message()`kullanarak `test-queue` adlı kuyruğa test iletisinin nasıl gönderileceğini gösterir:

```ruby
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

Service Bus kuyrukları, [Standart katmanda](service-bus-premium-messaging.md) maksimum 256 KB ve [Premium katmanda](service-bus-premium-messaging.md) maksimum 1 MB ileti boyutunu destekler. Standart ve özel uygulama özelliklerini içeren üst bilginin maksimum dosya boyutu 64 KB olabilir. Kuyrukta tutulan ileti sayısına ilişkin bir sınır yoktur ancak kuyruk tarafından tutulan iletilerin toplam boyutu için uç sınır vardır. Bu kuyruk boyutu, üst sınır 5 GB olacak şekilde oluşturulma zamanında belirlenir.

## <a name="how-to-receive-messages-from-a-queue"></a>Kuyruktan ileti alma
İletiler, **Azure:: ServiceBusService** nesnesindeki `receive_queue_message()` yöntemi kullanılarak kuyruktan alınır. Varsayılan olarak, iletiler kuyruktan silinmeden, salt okunurdur ve kilitlenir. Ancak, `:peek_lock` seçeneğini **false**olarak ayarlayarak kuyruktaki iletileri okurken silebilirsiniz.

Varsayılan davranış, iki aşamalı bir işlemi okumayı ve silmeyi, ayrıca eksik iletilere izin verilmeyen uygulamaları desteklemeyi olanaklı hale getirir. Service Bus bir istek aldığında bir sonraki kullanılacak iletiyi bulur, diğer tüketicilerin bu iletiyi almasını engellemek için kilitler ve ardından uygulamaya döndürür. Uygulama iletiyi işlemeyi tamamladıktan (veya gelecekteki işlemler için güvenilir bir şekilde depolar), `delete_queue_message()` yöntemini çağırarak ve bir parametre olarak silinecek iletiyi sağlayarak alma işleminin ikinci aşamasını tamamlar. `delete_queue_message()` yöntemi iletiyi tüketildiği gibi işaretleyecek ve kuyruktan kaldıracak.

`:peek_lock` parametresi **false**olarak ayarlandıysa, iletiyi okumak ve silmek en basit model haline gelir ve bir uygulamanın hata durumunda bir iletiyi işlememesinin kabul edebildiği senaryolar için en iyi sonuç verir. Bu durumu daha iyi anlamak için müşterinin bir alma isteği bildirdiğini ve bu isteğin işlenmeden çöktüğünü varsayın. Service Bus ileti tüketildiği gibi işaretlendiğinden, uygulama yeniden başlatıldığında ve iletileri yeniden kullanmaya başladığında, kilitlenme öncesinde tüketilen ileti kaçırılmış olur.

Aşağıdaki örnek, `receive_queue_message()`kullanarak iletilerin nasıl alınacağını ve işleyeceğini gösterir. Örnek öncelikle `:peek_lock` **false**olarak ayarlanmış bir ileti alır ve siler, sonra başka bir ileti alır ve sonra `delete_queue_message()`kullanarak iletiyi siler:

```ruby
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Uygulama çökmelerini ve okunmayan iletileri giderme
Service Bus, uygulamanızda gerçekleşen hataları veya ileti işlenirken oluşan zorlukları rahat bir şekilde ortadan kaldırmanıza yardımcı olmak için işlevsellik sağlar. Bir alıcı uygulamasının iletiyi bazı nedenlerle işleyemeyeceği takdirde **Azure:: ServiceBusService** nesnesinde `unlock_queue_message()` yöntemi çağırabilir. Bu çağrı, Service Bus kuyruktaki iletinin kilidini açmasının yanı sıra, aynı uygulama ya da başka bir uygulama tarafından yeniden alınabileceğinden emin olur.

Ayrıca, kuyruk içinde kilitlenen bir iletiyle ilişkili bir zaman aşımı da vardır ve uygulamanın kilit zaman aşımı dolmadan önce iletiyi işleyemezse (örneğin, uygulama çökerse), iletinin kilidini otomatik olarak Service Bus ve bunu yapar yeniden alınmak üzere kullanılabilir.

İleti işlendikten sonra, ancak `delete_queue_message()` yöntemi çağrılmadan önce uygulamanın çöktüğü durumda, yeniden başlatıldığında ileti uygulamaya yeniden gönderilir. Bu işlem genellikle *en az bir kez işleme*olarak adlandırılır; diğer bir deyişle, her ileti en az bir kez işlenir ancak belirli durumlarda aynı ileti yeniden teslim edilebilir. Senaryo yinelenen işlemeyi kabul etmiyorsa yinelenen ileti teslimine izin vermek için uygulama geliştiricilerin uygulamaya ilave bir mantık eklemesi gerekir. Bu, genellikle iletinin `message_id` özelliği kullanılarak gerçekleştirilir ve bu durum, teslim girişimleri arasında sabitde kalır.

> [!NOTE]
> Service Bus kaynaklarını [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/)ile yönetebilirsiniz. Service Bus gezgin, kullanıcıların bir Service Bus ad alanına bağlanmasına ve mesajlaşma varlıklarını kolay bir şekilde yönetmesine olanak tanır. Araç içeri/dışarı aktarma işlevselliği gibi gelişmiş özellikler ya da konu, kuyruk, abonelik, geçiş Hizmetleri, Bildirim Hub 'ları ve Olay Hub 'larını test etme yeteneği sağlar. 

## <a name="next-steps"></a>Sonraki adımlar
Artık Service Bus kuyruklarına ilişkin temel bilgileri öğrendiğinize göre, daha fazla bilgi edinmek için aşağıdaki bağlantıları izleyin.

* [Kuyruklara, konulara ve aboneliklere](service-bus-queues-topics-subscriptions.md)genel bakış.
* GitHub 'daki [Ruby deposu Için Azure SDK 'sını](https://github.com/Azure/azure-sdk-for-ruby) ziyaret edin.

Bu makalede açıklanan Azure Service Bus kuyrukları ve [Ruby 'Den kuyruk depolama 'yı kullanma](../storage/queues/storage-ruby-how-to-use-queue-storage.md) bölümünde açıklanan Azure Kuyrukları arasında bir karşılaştırma için bkz. [azure kuyrukları ve Azure Service Bus kuyrukları-karşılaştırılan ve değişken maliyetli](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

