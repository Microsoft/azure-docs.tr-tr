---
title: 'Hızlı başlangıç: Service Bus konuları kullanma (Ruby)'
description: "Hızlı başlangıç: Azure 'da Service Bus konuları ve abonelikleri nasıl kullanacağınızı öğrenin. Ruby uygulamaları için kod örnekleri yazılmıştır."
services: service-bus-messaging
documentationcenter: ruby
author: axisc
manager: timlt
editor: ''
ms.assetid: 3ef2295e-7c5f-4c54-a13b-a69c8045d4b6
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: aschhab
ms.openlocfilehash: b5401eae844ed2113a9fbc07c8b3ad8601709d43
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718937"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-ruby"></a>Hızlı başlangıç: Ruby ile Service Bus konuları ve abonelikleri kullanma
 
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Bu makalede, Ruby uygulamalarından Service Bus konularının ve aboneliklerinin nasıl kullanılacağı açıklanır. Kapsanan senaryolar şunlardır:

- Konular ve abonelikler oluşturma 
- Abonelik filtreleri oluşturma 
- Konuya ileti gönderme 
- Bir abonelikten ileti alma
- Konuları ve abonelikleri silme


## <a name="prerequisites"></a>Ön koşullar
1. Azure aboneliği. Bu öğreticiyi tamamlamak için bir Azure hesabınızın olması gerekir. [Visual Studio veya MSDN abonesi avantajlarınızı](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) etkinleştirebilir veya [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)için kaydolabilirsiniz.
2. Hızlı başlangıç içindeki adımları izleyin: bir Service Bus **ad alanı** oluşturmak ve **bağlantı dizesini**almak için [Azure Portal Service Bus konu başlığı ve abonelikleri oluşturmak üzere kullanın](service-bus-quickstart-topics-subscriptions-portal.md) . 

    > [!NOTE]
    > Bu hızlı başlangıçta **Ruby** kullanarak konu için bir **Konu** ve bir **abonelik** oluşturacaksınız. 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="create-a-topic"></a>Konu başlığı oluşturma
**Azure:: ServiceBusService** nesnesi konularda çalışmanıza olanak sağlar. Aşağıdaki kod bir **Azure:: ServiceBusService** nesnesi oluşturur. Bir konu oluşturmak için `create_topic()` yöntemi kullanın. Aşağıdaki örnek bir konu oluşturur veya hataları yazdırır.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  topic = azure_service_bus_service.create_topic("test-topic")
rescue
  puts $!
end
```

Ayrıca, ileti Süresi canlı veya en yüksek sıra boyutu gibi varsayılan konu ayarlarını geçersiz kılmanızı sağlayan ek seçeneklerle bir **Azure:: ServiceBus:: topic** nesnesi geçirebilirsiniz. Aşağıdaki örnek, en büyük sıra boyutu olarak 5 GB ve 1 dakikalık yaşam süresi ayarlanmasını gösterir:

```ruby
topic = Azure::ServiceBus::Topic.new("test-topic")
topic.max_size_in_megabytes = 5120
topic.default_message_time_to_live = "PT1M"

topic = azure_service_bus_service.create_topic(topic)
```

## <a name="create-subscriptions"></a>Abonelik oluşturma
**Azure:: ServiceBusService** nesnesiyle birlikte konu abonelikleri de oluşturulur. Abonelikler adlandırılır ve aboneliğin sanal kuyruğuna teslim edilen ileti kümesini kısıtlayan isteğe bağlı bir filtreye sahip olabilir.

Abonelikler, varsayılan olarak kalıcıdır. Bunlar, ya da ilişkili oldukları konu silinene kadar var olmaya devam eder. Uygulamanız bir abonelik oluşturmak için mantık içeriyorsa, öncelikle getSubscription yöntemi kullanılarak aboneliğin zaten mevcut olup olmadığını denetlemelidir.

Otomatik [Deleteonıdle özelliği](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.autodeleteonidle)ayarlanarak aboneliklerin otomatik olarak silinmesini sağlayabilirsiniz.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Varsayılan (MatchAll) filtreyle abonelik oluşturma
Yeni bir abonelik oluşturulduğunda bir filtre belirtilmemişse, **Matchall** filtresi (varsayılan) kullanılır. **Matchall** filtresi kullanıldığında, konuya yayınlanan tüm iletiler aboneliğin sanal kuyruğuna yerleştirilir. Aşağıdaki örnek, "All-Messages" adlı bir abonelik oluşturur ve varsayılan **Matchall** filtresini kullanır.

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "all-messages")
```

### <a name="create-subscriptions-with-filters"></a>Filtre içeren abonelik oluşturma
Ayrıca, bir konuya gönderilen iletilerin belirli bir abonelik içinde gösterilmesi gerektiğini belirtmenize imkan tanıyan filtreler de tanımlayabilirsiniz.

Abonelikler tarafından desteklenen en esnek filtre türü, SQL92 alt kümesini uygulayan **Azure:: ServiceBus:: SqlFilter**' dir. SQL filtreleri, konu başlığında yayımlanan iletilerin özelliklerinde çalışır. Bir SQL filtresiyle kullanılabilecek ifadeler hakkında daha fazla ayrıntı için [sqlfilter](service-bus-messaging-sql-filter.md) sözdizimini gözden geçirin.

**Azure:: ServiceBusService** nesnesinin `create_rule()` yöntemini kullanarak bir aboneliğe filtre ekleyebilirsiniz. Bu yöntem, mevcut bir aboneliğe yeni filtreler eklemenize olanak sağlar.

Varsayılan filtre tüm yeni aboneliklere otomatik olarak uygulandığından, önce varsayılan filtreyi kaldırmanız gerekir, yoksa **Matchall** , belirtebileceğiniz diğer filtreleri geçersiz kılar. **Azure:: ServiceBusService** nesnesindeki `delete_rule()` yöntemini kullanarak varsayılan kuralı kaldırabilirsiniz.

Aşağıdaki örnekte, yalnızca 3 ' ten büyük özel bir `message_number` özelliğine sahip iletileri seçen bir **Azure:: ServiceBus:: SqlFilter** ile "yüksek iletiler" adlı bir abonelik oluşturulur:

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "high-messages")
azure_service_bus_service.delete_rule("test-topic", "high-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("high-messages-rule")
rule.topic = "test-topic"
rule.subscription = "high-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number > 3" })
rule = azure_service_bus_service.create_rule(rule)
```

Benzer şekilde, aşağıdaki örnekte, yalnızca 3 ' ten küçük veya buna eşit bir `message_number` özelliğine sahip iletileri seçen **Azure:: ServiceBus:: SqlFilter** ile `low-messages` adlı bir abonelik oluşturulur:

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "low-messages")
azure_service_bus_service.delete_rule("test-topic", "low-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("low-messages-rule")
rule.topic = "test-topic"
rule.subscription = "low-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number <= 3" })
rule = azure_service_bus_service.create_rule(rule)
```

`test-topic`bir ileti gönderildiğinde, her zaman `all-messages` konu aboneliğine abone olan alıcılar için dağıtılır ve `high-messages` ve `low-messages` konu aboneliklerine abone olan alıcılar için seçmeli olarak dağıtılır (iletiye bağlı olarak) içerik).

## <a name="send-messages-to-a-topic"></a>Konu başlığına ileti gönderme
Service Bus bir konuya ileti göndermek için uygulamanızın **Azure:: ServiceBusService** nesnesinde `send_topic_message()` metodunu kullanması gerekir. Service Bus konularına gönderilen iletiler **Azure:: ServiceBus:: BrokeredMessage** nesnelerinin örnekleridir. **Azure:: ServiceBus:: BrokeredMessage** nesneleri, bir dizi standart özelliğe sahiptir (`label` ve `time_to_live`gibi), uygulamaya özgü özel özellikleri tutmak için kullanılan bir sözlük ve dize verileri gövdesi. Bir uygulama, `send_topic_message()` yöntemine bir dize değeri geçirerek ileti gövdesini ayarlayabilir ve gerekli tüm standart özellikler varsayılan değerlere göre doldurulur.

Aşağıdaki örnek, `test-topic`beş test iletisinin nasıl gönderileceğini gösterir. Her iletinin `message_number` özel özellik değeri, döngünün yinelemesinde değişiklik gösterir (hangi aboneliğin bunu alacağını belirler):

```ruby
5.times do |i|
  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
    { :message_number => i })
  azure_service_bus_service.send_topic_message("test-topic", message)
end
```

Service Bus konu başlıkları, [Standart katmanda](service-bus-premium-messaging.md) maksimum 256 KB ve [Premium katmanda](service-bus-premium-messaging.md) maksimum 1 MB ileti boyutunu destekler. Standart ve özel uygulama özelliklerini içeren üst bilginin maksimum dosya boyutu 64 KB olabilir. Konu başlığında tutulan ileti sayısına ilişkin bir sınır yoktur ancak konu başlığı tarafından tutulan iletilerin toplam boyutu için uç sınır vardır. Bu konu başlığı boyutu, üst sınır 5 GB olacak şekilde oluşturulma zamanında belirlenir.

## <a name="receive-messages-from-a-subscription"></a>Bir abonelikten ileti alma
**Azure:: ServiceBusService** nesnesindeki `receive_subscription_message()` yöntemi kullanılarak bir abonelikten iletiler alınır. Varsayılan olarak, iletiler, abonelikten silinmeksizin salt okunurdur (yoğun) ve kilitlidir. `peek_lock` seçeneğini **yanlış**olarak ayarlayarak iletiyi aboneliğden okuyabilir ve silebilirsiniz.

Varsayılan davranış, iki aşamalı bir işlemi okumayı ve silmeyi, ayrıca eksik iletilere izin verilmeyen uygulamaları desteklemeyi olanaklı hale getirir. Service Bus bir istek aldığında bir sonraki kullanılacak iletiyi bulur, diğer tüketicilerin bu iletiyi almasını engellemek için kilitler ve ardından uygulamaya döndürür. Uygulama iletiyi işlemeyi tamamladıktan (veya gelecekteki işlemler için güvenilir bir şekilde depolar), `delete_subscription_message()` yöntemini çağırarak ve bir parametre olarak silinecek iletiyi sağlayarak alma işleminin ikinci aşamasını tamamlar. `delete_subscription_message()` yöntemi iletiyi tüketildiği gibi işaretler ve abonelikten kaldırır.

`:peek_lock` parametresi **false**olarak ayarlandıysa, iletiyi okumak ve silmek en basit model haline gelir ve bir hata oluştuğunda bir uygulamanın bir iletiyi işlememesinin kabul edebildiği senaryolar için en iyi sonucu verir. Tüketicinin alma isteğini yaptığı ve işlemeden önce çöktüğü bir senaryo düşünün. Service Bus ileti tüketildiği gibi işaretlendiğinden, uygulama yeniden başlatıldığında ve iletileri yeniden kullanmaya başladığında, kilitlenme öncesinde tüketilen iletiyi kaçırmıştır.

Aşağıdaki örnek, iletilerin `receive_subscription_message()`kullanılarak nasıl alınacağını ve işlenebileceğinizi gösterir. Örnek, `low-messages` aboneliğinden önce `:peek_lock` **false**olarak ayarlanmış bir ileti alır ve siler, ardından `high-messages` başka bir ileti alır ve `delete_subscription_message()`kullanarak iletiyi siler:

```ruby
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "low-messages", { :peek_lock => false })
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "high-messages")
azure_service_bus_service.delete_subscription_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Uygulama çökmelerini ve okunmayan iletileri giderme
Service Bus, uygulamanızda gerçekleşen hataları veya ileti işlenirken oluşan zorlukları rahat bir şekilde ortadan kaldırmanıza yardımcı olmak için işlevsellik sağlar. Bir alıcı uygulamasının iletiyi bazı nedenlerle işleyemeyeceği takdirde **Azure:: ServiceBusService** nesnesinde `unlock_subscription_message()` yöntemi çağırabilir. Service Bus, aboneliğin içindeki iletinin kilidini açmasının yanı sıra, aynı uygulama ya da başka bir Kullanıcı uygulama tarafından tekrar alınabilir hale gelmesine neden olur.

Ayrıca, abonelik içinde kilitlenen bir iletiyle ilişkili bir zaman aşımı vardır. uygulama, kilit zaman aşımı dolmadan önce iletiyi işleyemezse (örneğin, uygulama çökerse), Service Bus otomatik olarak iletinin kilidini açar ve yeniden alınmak üzere kullanılabilir hale getirin.

İleti işlendikten sonra, ancak `delete_subscription_message()` yöntemi çağrılmadan önce uygulamanın çöktüğü durumda, yeniden başlatıldığında ileti uygulamaya yeniden gönderilir. Genellikle *en az bir kez işleme*olarak adlandırılır; diğer bir deyişle, her ileti en az bir kez işlenir ancak belirli durumlarda aynı ileti yeniden teslim edilebilir. Senaryo yinelenen işlemeyi kabul etmiyorsa yinelenen ileti teslimine izin vermek için uygulama geliştiricilerin uygulamaya ilave bir mantık eklemesi gerekir. Bu mantık genellikle iletinin `message_id` özelliği kullanılarak elde edilir ve bu da teslim girişimleri arasında sabit kalır.

## <a name="delete-topics-and-subscriptions"></a>Konu başlıklarını ve abonelikleri silme
[Oto Deleteonıdle özelliği](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.autodeleteonidle) ayarlanmadığı için konular ve abonelikler kalıcıdır. [Azure Portal][Azure portal] veya program aracılığıyla silinebilirler. Aşağıdaki örnek, `test-topic`adlı konunun nasıl silineceğini gösterir.

```ruby
azure_service_bus_service.delete_topic("test-topic")
```

Bir konu başlığı silindiğinde bu konu başlığıyla kaydedilen tüm abonelikler de silinir. Ayrıca, abonelikler bağımsız olarak da silinebilir. Aşağıdaki kod, `test-topic` konusunun `high-messages` adlı aboneliğin nasıl silineceğini göstermektedir:

```ruby
azure_service_bus_service.delete_subscription("test-topic", "high-messages")
```

> [!NOTE]
> Service Bus kaynaklarını [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/)ile yönetebilirsiniz. Service Bus gezgin, kullanıcıların bir Service Bus ad alanına bağlanmasına ve mesajlaşma varlıklarını kolay bir şekilde yönetmesine olanak tanır. Araç içeri/dışarı aktarma işlevselliği gibi gelişmiş özellikler ya da konu, kuyruk, abonelik, geçiş Hizmetleri, Bildirim Hub 'ları ve Olay Hub 'larını test etme yeteneği sağlar. 

## <a name="next-steps"></a>Sonraki adımlar
Service Bus konuların temellerini öğrendiğinize göre, daha fazla bilgi edinmek için bu bağlantıları izleyin.

* Bkz. [Kuyruklar, konular ve abonelikler](service-bus-queues-topics-subscriptions.md).
* [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) için API başvurusu
* GitHub 'daki [Ruby deposu Için Azure SDK 'sını](https://github.com/Azure/azure-sdk-for-ruby) ziyaret edin.

[Azure portal]: https://portal.azure.com
