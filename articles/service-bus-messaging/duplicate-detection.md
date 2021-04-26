---
title: Yinelenen ileti algılamayı Azure Service Bus | Microsoft Docs
description: Bu makalede Azure Service Bus iletilerinde yinelenenleri nasıl Algılayabileceğiniz açıklanır. Yinelenen ileti yoksayılabilir ve bırakılabilir.
ms.topic: article
ms.date: 04/19/2021
ms.openlocfilehash: baeda3509cb5646c658f79fb11610ecfdd1ffd3d
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751283"
---
# <a name="duplicate-detection"></a>Yineleme algılama

Bir uygulama, bir ileti gönderdikten hemen sonra önemli bir hata nedeniyle başarısız olursa ve yeniden başlatılan uygulama örneği, önceki ileti tesliminin gerçekleşmediği görüldüğü takdirde, sonraki bir gönderme aynı iletinin iki kez sistemde görünmesine neden olur.

Ayrıca, istemci veya ağ düzeyindeki bir hata daha önce gerçekleşmesi ve gönderilmiş bir iletinin sıraya teslim edilmesiyle, bildirim istemciye başarıyla döndürülmediği için de mümkündür. Bu senaryo, istemciyi gönderme işleminin sonucu hakkında şüpheli halde bırakır.

Yinelenen algılama, gönderenin aynı iletiyi yeniden göndermesini sağlayarak bu durumlardan yararlanır ve kuyruk veya konu, yinelenen kopyaları atar.

> [!NOTE]
> Service Bus temel katmanı yinelenen saptamayı desteklemez. Standart ve Premium katmanlar yinelenen saptamayı destekler. Bu katmanlar arasındaki farklar için bkz. [Service Bus fiyatlandırması](https://azure.microsoft.com/pricing/details/service-bus/).

## <a name="how-it-works"></a>Nasıl çalışıyor? 
Yinelenen saptamayı etkinleştirmek, belirli bir zaman penceresi sırasında bir sıraya veya konuya gönderilen tüm iletilerin uygulama denetimli *MessageID* ' i izlemeye devam eder. Zaman penceresi sırasında günlüğe kaydedilen *MessageID* ile yeni bir ileti gönderilirse, ileti kabul edildi olarak bildirilir (gönderme işlemi başarılı olur), ancak yeni gönderilen ileti anında yoksayılır ve bırakılır. İletinin *MessageID* dışında başka hiçbir bölümü göz önünde bulundurulmamasıdır.

Yalnızca *uygulamanın, bir* hata oluştuğunda öngörülebilir olarak yeniden oluşturulduğu bir iş süreci bağlamına erişmesini sağladığından, tanımlayıcının uygulama denetimi gereklidir.

Bazı uygulama bağlamını işleme sürecinde birden fazla iletinin gönderildiği bir iş süreci için, *MessageID* , bir satın alma siparişi numarası gibi uygulama düzeyi bağlam tanımlayıcısının bir bileşimi olabilir ve örneğin, **12345.2017/ödeme** gibi ileti konusu olabilir.

*MessageID* her zaman bir GUID olabilir, ancak tanımlayıcıyı iş işlemine sabitleme, yinelenen algılama özelliğinin etkin bir şekilde kullanılması için istenen tahmin edilebilir yinelenebilirlik verir.

> [!IMPORTANT]
>- **Bölümlendirme** **etkin** olduğunda, `MessageId+PartitionKey` benzersizliği belirlemede kullanılır. Oturumlar etkinleştirildiğinde, bölüm anahtarı ve oturum KIMLIĞI aynı olmalıdır. 
>- **Bölümlendirme** **devre dışı** bırakıldığında (varsayılan), yalnızca `MessageId` benzersizliği belirlemede kullanılır.
>- SessionID, PartitionKey ve MessageID hakkında daha fazla bilgi için bkz. [bölüm anahtarlarının kullanımı](service-bus-partitioning.md#use-of-partition-keys).
>- [Premier katmanı](service-bus-premium-messaging.md) bölümlemeyi desteklemez, bu nedenle uygulamalarınızda benzersiz ileti kimlikleri kullanmanızı ve yinelenen algılama için bölüm anahtarlarına güvenmenizi öneririz. 


## <a name="duplicate-detection-window-size"></a>Yinelenen algılama penceresi boyutu

Yalnızca yinelenen saptamayı etkinleştirmenin yanı sıra, ileti kimliklerinin saklanacağı yinelenen algılama geçmişi zaman penceresinin boyutunu da yapılandırabilirsiniz.
Bu değer, en az 7 günlük değer olacak şekilde, kuyruklar ve konular için varsayılan olarak 10 dakikadır.

Tüm kayıtlı ileti kimliklerinin yeni gönderilen ileti tanımlayıcısına karşı eşleşmesi gerektiğinden, yinelenen saptamayı etkinleştirmek ve pencerenin boyutu doğrudan sırayı (ve konu) verimini etkiler.

Pencerenin küçük tutulması, daha az ileti kimliği tutulması ve eşleştirilmesi gerektiği ve verimlilik daha az etkilenildiği anlamına gelir. Yinelenen saptamayı gerektiren yüksek aktarım hızı varlıkları için, pencereyi mümkün olduğunca küçük tutmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar
Azure portal, PowerShell, CLı, Kaynak Yöneticisi şablonu, .NET, Java, Python ve JavaScript kullanarak yinelenen ileti algılamayı etkinleştirebilirsiniz. Daha fazla bilgi için bkz. [yinelenen ileti algılamayı etkinleştirme](enable-duplicate-detection.md). 

İstemci kodunun daha önceki bir *MessageID* ile bir iletiyi yeniden gönderemediği senaryolarda, güvenli bir şekilde işlenebilecekleri iletiler tasarlamak önemlidir. [Eşkuvvetlilik hakkında bu blog gönderisi](https://particular.net/blog/what-does-idempotent-mean) , bunun nasıl yapılacağı hakkında çeşitli teknikler açıklamaktadır.

Azure Service Bus özellikleri araştırmak için istediğiniz dildeki örnekleri deneyin. 

- [Java için Azure Service Bus istemci kitaplığı örnekleri](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Python için Azure Service Bus istemci kitaplığı örnekleri](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [JavaScript için Azure Service Bus istemci kitaplığı örnekleri](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [TypeScript için Azure Service Bus istemci kitaplığı örnekleri](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [.NET için Azure. Messaging. ServiceBus örnekleri](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)

Aşağıdaki eski .NET ve Java istemci kitaplıkları için örnekler bulun:
- [.NET için Microsoft. Azure. ServiceBus örnekleri](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [Java için Azure-ServiceBus örnekleri](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)

