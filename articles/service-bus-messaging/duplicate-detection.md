---
title: Yinelenen ileti algılamayı Azure Service Bus | Microsoft Docs
description: Yinelenen Service Bus iletilerini Algıla
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
ms.openlocfilehash: bee8c1d2a1cd313c7fe59d8e53379dc57554e98c
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68618577"
---
# <a name="duplicate-detection"></a>Yineleme algılama

Bir uygulama, bir ileti gönderdikten hemen sonra önemli bir hata nedeniyle başarısız olursa ve yeniden başlatılan uygulama örneği, önceki ileti tesliminin gerçekleşmediği görüldüğü takdirde, sonraki bir gönderme aynı iletinin iki kez sistemde görünmesine neden olur.

Ayrıca, istemci veya ağ düzeyindeki bir hata daha önce gerçekleşmesi ve gönderilmiş bir iletinin sıraya teslim edilmesiyle, bildirim istemciye başarıyla döndürülmediği için de mümkündür. Bu senaryo, istemciyi gönderme işleminin sonucu hakkında şüpheli halde bırakır.

Yinelenen algılama, gönderenin aynı iletiyi yeniden göndermesini sağlayarak bu durumlardan yararlanır ve kuyruk veya konu, yinelenen kopyaları atar.

Yinelenen saptamayı etkinleştirmek, belirli bir zaman penceresi sırasında bir sıraya veya konuya gönderilen tüm iletilerin uygulama denetimli *MessageID* ' i izlemeye devam eder. Zaman penceresi sırasında günlüğe kaydedilen *MessageID* ile yeni bir ileti gönderilirse, ileti kabul edildi olarak bildirilir (gönderme işlemi başarılı olur), ancak yeni gönderilen ileti anında yoksayılır ve bırakılır. İletinin *MessageID* dışında başka hiçbir bölümü göz önünde bulundurulmamasıdır.

Yalnızca uygulamanın, bir hata oluştuğunda öngörülebilir olarak yeniden oluşturulduğu bir iş süreci bağlamına erişmesini sağladığından, tanımlayıcının uygulama denetimi gereklidir.

Bazı uygulama bağlamını işleme sürecinde birden fazla iletinin gönderildiği bir iş süreci için, *MessageID* , bir satın alma siparişi numarası gibi uygulama düzeyi bağlam tanımlayıcısının bir bileşimi olabilir ve bu ileti konusu örnek, **12345.2017/ödeme**.

*MessageID* her zaman bir GUID olabilir, ancak tanımlayıcıyı iş işlemine tutturma, yinelenen algılama özelliğini etkin bir şekilde kullanmak için istenen tahmin edilebilir yinelenebilirlik verir.

> [!NOTE]
> Yinelenen algılama etkinse ve oturum KIMLIĞI ya da bölüm anahtarı ayarlanmamışsa, bölüm anahtarı olarak ileti KIMLIĞI kullanılır. İleti KIMLIĞI de ayarlanmamışsa, .NET ve AMQP kitaplıkları ileti için otomatik olarak bir ileti KIMLIĞI oluşturur. Daha fazla bilgi için bkz. [bölüm anahtarlarının kullanımı](service-bus-partitioning.md#use-of-partition-keys).

## <a name="enable-duplicate-detection"></a>Yinelenen algılamayı etkinleştir

Portalda, özellik varsayılan olarak kapalı olan **yinelenen saptamayı etkinleştir** onay kutusu ile varlık oluşturma sırasında açıktır. Yeni konu başlıkları oluşturma ayarı eşdeğerdir.

![][1]

> [!IMPORTANT]
> Sıra oluşturulduktan sonra yinelenen saptamayı etkinleştiremez/devre dışı bırakabilirsiniz. Bunu yalnızca kuyruğu oluşturma sırasında yapabilirsiniz. 

Programlama yoluyla, bayrağı Full Framework .NET API 'sindeki [Queuedescription. requiresDuplicateDetection](/dotnet/api/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection) özelliği ile ayarlarsınız. Azure Resource Manager API 'SI ile, değeri [Queueproperties. requiresDuplicateDetection](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) özelliği ile ayarlanır.

Yinelenen algılama zaman geçmişi, kuyruklar ve konular için en fazla yedi gün olacak şekilde 30 saniye olur. Bu ayarı, Azure portal sıra ve konu özellikleri penceresinde değiştirebilirsiniz.

![][2]

Programlama yoluyla, tam .NET Framework API 'SI ile [Queuedescription. DuplicateDetectionHistoryTimeWindow](/dotnet/api/microsoft.servicebus.messaging.queuedescription.duplicatedetectionhistorytimewindow#Microsoft_ServiceBus_Messaging_QueueDescription_DuplicateDetectionHistoryTimeWindow) özelliğini kullanarak ileti kimliklerinin saklanacağı yinelenen saptama penceresinin boyutunu yapılandırabilirsiniz. Azure Resource Manager API 'SI ile, değeri [Queueproperties. duplicateDetectionHistoryTimeWindow](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) özelliği ile ayarlanır.

Tüm kayıtlı ileti kimliklerinin yeni gönderilen ileti tanımlayıcısına karşı eşleşmesi gerektiğinden, yinelenen saptamayı etkinleştirmek ve pencerenin boyutu doğrudan sırayı (ve konu) verimini etkiler.

Pencerenin küçük tutulması, daha az ileti kimliği tutulması ve eşleştirilmesi gerektiği ve verimlilik daha az etkilenildiği anlamına gelir. Yinelenen saptamayı gerektiren yüksek aktarım hızı varlıkları için, pencereyi mümkün olduğunca küçük tutmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Service Bus mesajlaşma hakkında daha fazla bilgi edinmek için aşağıdaki konulara bakın:

* [Service Bus kuyrukları, konu başlıkları ve abonelikleri](service-bus-queues-topics-subscriptions.md)
* [Service Bus kuyrukları ile çalışmaya başlama](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus konu başlıklarını ve aboneliklerini kullanma](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png
