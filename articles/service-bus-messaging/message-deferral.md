---
title: Azure Service Bus-ileti erteleme
description: Bu makalede Azure Service Bus iletilerinin teslimini erteleme açıklanır. İleti kuyrukta veya abonelikte kalır, ancak bu şekilde ayarlanır.
ms.topic: conceptual
ms.date: 04/21/2021
ms.openlocfilehash: 171fc6e1a3c779802747d34ac631d265e8c8926f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869500"
---
# <a name="message-deferral"></a>İleti erteleme
Bir kuyruk veya abonelik istemcisi işlemek isteyen bir ileti aldığında, ancak özel koşullar nedeniyle işleme Şu anda mümkün değilse, iletiyi sonraki bir noktaya alma seçeneği vardır. İleti kuyrukta veya abonelikte kalır, ancak bu şekilde ayarlanır.

> [!NOTE]
> Ertelenmiş iletiler, [süreleri dolduktan sonra](./service-bus-dead-letter-queues.md#time-to-live)otomatik olarak atılacak ileti kuyruğuna taşınamaz. Bu davranış tasarıma göre yapılır.

## <a name="sample-scenarios"></a>Örnek senaryolar
Deferral, özel olarak iş akışı işleme senaryolarında oluşturulan bir özelliktir. İş akışı çerçeveleri belirli işlemler için belirli bir sırada işlenmesini gerektirebilir. Diğer iletiler tarafından bilgilendirilmiş önceki çalışmalarla birlikte, bazı alınan iletilerin işlenmesini erteleyebilirler.

Basit bir tanım örneği, eşleşen satın alma siparişi mağazadan öne çıkan sisteme yayılmadan önce bir sistemde, bir dış ödeme sağlayıcısından ödeme bildiriminin bir sistemde göründüğü bir sipariş işleme sırasıdır. Bu durumda, karşılama sistemi, ilişkilendirilecek bir sipariş olana kadar ödeme bildirimini işlemeye erteleyebilirsiniz. Farklı kaynaklardaki iletilerin bir iş akışını ileriye doğru bir şekilde iletmekte olduğu buluşma senaryolarında gerçek zamanlı yürütme sırası gerçekten doğru olabilir, ancak sonuçları yansıtan iletiler sıra dışına çıkabilir.

Sonuç olarak, alma sırasındaki iletileri, işleme için bir ileti deposunda güvenli durumda bırakarak, bu iletileri işlenmek üzere yeniden sıralama konusunda yardımcı olur.

Bu iletiyi işlemeye yönelik belirli bir kaynak geçici olarak kullanılamadığından bir ileti işlenemezse, ileti işleme summarily askıya alınmamalıdır, bu iletiyi birkaç dakika için tarafa koymak için bir yol, [zamanlanan bir iletideki](message-sequencing.md) sıra numarasını birkaç dakika içinde yeniden almak ve zamanlanan ileti geldiğinde ertelenmiş iletiyi yeniden almak olacaktır. Bir ileti işleyicisi tüm işlemler için bir veritabanına bağımlıysa ve bu veritabanı geçici olarak kullanılamıyorsa, ertelemeal kullanılmamalıdır, ancak veritabanı yeniden kullanılabilir olana kadar iletileri almayı askıya almamayı tercih etmemelidir. 

## <a name="retrieving-deferred-messages"></a>Ertelenmiş iletileri alma
Ertelenmiş iletiler, diğer tüm etkin iletilerle birlikte ana sırada kalır (bir alt sırada bulunan atılacak ileti iletilerinin aksine), ancak artık normal alma işlemleri kullanılarak alınamazlar. Ertelenmiş iletiler, bir uygulama tarafından izlenmeleri kaybolursa [ileti göz atma](message-browsing.md) aracılığıyla bulunabilir.

Ertelenmiş bir ileti almak için, sahip olduğu sırada sıra numarasını hatırmaktan sorumlu olur. Ertelenmiş bir iletinin sıra numarasını bilen herhangi bir alıcı, daha sonra dizi numarasını parametre olarak alan alma yöntemlerini kullanarak iletiyi alabilir. Sıra numaraları hakkında daha fazla bilgi için bkz. [ileti sıralaması ve zaman damgaları](message-sequencing.md).

## <a name="next-steps"></a>Sonraki adımlar
Azure Service Bus özellikleri araştırmak için istediğiniz dildeki örnekleri deneyin. 

- [Java için Azure Service Bus istemci kitaplığı örnekleri](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Python için istemci kitaplığı örneklerini Azure Service Bus](/samples/azure/azure-sdk-for-python/servicebus-samples/) -bkz. **receive_deferred_message_queue. Kopyala** örneği. 
- [JavaScript için istemci kitaplığı örneklerini Azure Service Bus](/samples/azure/azure-sdk-for-js/service-bus-javascript/) - **Gelişmiş/deferral.js** örneğe bakın. 
- [TypeScript için istemci kitaplığı örneklerini Azure Service Bus](/samples/azure/azure-sdk-for-js/service-bus-typescript/) -bkz. **Advanced/deferral. TS** örneği. 
- [.Net Için Azure. Messaging. ServiceBus örnekleri](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/) -bkz. **kapatma iletileri** örneği. 

Aşağıdaki eski .NET ve Java istemci kitaplıkları için örnekler bulun:
- [.Net Için Microsoft. Azure. ServiceBus örnekleri](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/) - **deferral** örneğine bakın. 
- [Java için Azure-ServiceBus örnekleri](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)
