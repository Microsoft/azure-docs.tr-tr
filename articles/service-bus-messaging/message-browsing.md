---
title: Azure Service Bus-iletiye göz atma
description: Göz atma ve göz atma Service Bus iletileri, bir Azure Service Bus istemcisinin bir kuyruktaki veya abonelikteki tüm iletileri numaralandırmasını sağlar.
ms.topic: article
ms.date: 03/29/2021
ms.openlocfilehash: deafe9e6ddeeebf233922aade36823ddaaede864
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520131"
---
# <a name="message-browsing"></a>İletilere göz atma
İletiye göz atma veya gözatma, bir Service Bus istemcisinin tanılama ve hata ayıklama amacıyla bir kuyruktaki veya abonelikteki tüm iletileri numaralandırmasını sağlar.

Bir kuyrukta veya abonelikte bulunan göz atma işlemi istenen sayıda ileti döndürür. Aşağıdaki tabloda, Peek işleminin döndürdüğü ileti türleri gösterilmektedir. 

| İleti türü | Verilen? | 
| ---------------- | ----- | 
| Etkin iletiler | Yes |
| Kullanılmayan iletiler | Hayır | 
| Kilitli iletiler | Yes |
| Süre dolmuþ iletiler |  Olabilir (Bunlar, atılacak bir işlem yapılmadan önce) |
| Zamanlanmış iletiler | Kuyruklar için Evet. Abonelikler için Hayır |

## <a name="dead-lettered-messages"></a>Kullanılmayan iletiler
Bir kuyruğun veya aboneliğin **kullanılmayan** iletilerine gözatmak için, göz atma işlemi kuyruk veya abonelikle ilişkili atılacak ileti sırası üzerinde çalıştırılmalıdır. Daha fazla bilgi için bkz. [atılacak ileti sıralarına erişme](service-bus-dead-letter-queues.md#path-to-the-dead-letter-queue).

## <a name="expired-messages"></a>Süre dolmuþ iletiler
Süre biten iletiler, göz atma işleminin döndürdüğü sonuçlara dahil edilebilir. Tüketilen ve biten iletiler, zaman uyumsuz bir "çöp toplama" çalıştırması tarafından temizlenir. Bu adım, iletilerin süre dolduktan hemen sonra gerçekleşmeyebilir. Bu nedenle, bir göz atma işleminin zaten kullanım süreniz geçen iletileri döndürebiliriz. Bu iletiler, bir sonraki sefer sırada veya abonelikte bir alma işlemi çağrıldığında kaldırılacak veya atılacak duruma getirilir. Ertelenmiş iletileri kuyruktan kurtarmaya çalışırken bu davranışı aklınızda bulundurun. 

Süresi biten bir ileti, göz atma tarafından döndürülse bile diğer yollarla düzenli alma için uygun değildir. Bu iletilerin döndürülmesi tasarıma göre yapılır çünkü Özet, günlüğün geçerli durumunu yansıtan bir tanılama aracıdır.

## <a name="locked-messages"></a>Kilitli iletiler
Göz atma Ayrıca **kilitli** olan ve diğer alıcılar tarafından işlenmekte olan iletileri de döndürür. Ancak, Peek bağlantısı kesik bir anlık görüntü döndürdüğünden, iletinin kilit durumu atılamıyor iletilerinde gözlemlenemez.

## <a name="peek-apis"></a>API 'Leri göz atma
Kuyruklar, abonelikler ve atılacak ileti kuyruklarındaki çalışmalarda göz atın. 

Tekrar tekrar çağrıldığında, göz atma işlemi kuyruktaki veya abonelikteki tüm iletileri sırasıyla en düşük kullanılabilir sıra numarasından en yükseğe sıralar. İletilerin son sırada alınamayacağını sıraya göre değil, mesajların sıraya alınma sırası vardır.

Ayrıca, bir SequenceNumber de bir göz atma işlemine geçirebilirsiniz. Nereden göz atma başlangıcını belirlemekte kullanılacak. Daha fazla Numaralandırılacak parametreyi belirtmeden göz atma işlemine sonraki çağrıları yapabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
Göz atmayı veya ileti taramayı araştırmak için istediğiniz dildeki örnekleri deneyin:

- Java için Azure Service Bus [istemci kitaplığı örnekleri](/samples/azure/azure-sdk-for-java/servicebus-samples/)  -  **Bir ileti örneğine göz atın**
- Python için Azure Service Bus [istemci kitaplığı örnekleri](/samples/azure/azure-sdk-for-python/servicebus-samples/)  -  **receive_peek. Kopyala** örneği
- JavaScript için Azure Service Bus [istemci kitaplığı örnekleri](/samples/azure/azure-sdk-for-js/service-bus-javascript/)  -  **browseMessages.js** örneği
- TypeScript için Azure Service Bus [istemci kitaplığı örnekleri](/samples/azure/azure-sdk-for-js/service-bus-typescript/)  -  **browseMessages. TS** örneği
- [.Net Için Azure. Messaging. ServiceBus örnekleri](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/) - [başvuru belgelerindeki](/dotnet/api/azure.messaging.servicebus)alıcı sınıflarında bulunan yöntemlere göz atın.

Aşağıdaki eski .NET ve Java istemci kitaplıkları için örnekler bulun:
- [.Net Için Microsoft. Azure. ServiceBus örnekleri](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)  -  **Iletiye göz atma (Peek)** örneği 
- Java için Azure [-ServiceBus örnekleri](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)  -  **İleti, örneğe gözatamıyorum** . 
