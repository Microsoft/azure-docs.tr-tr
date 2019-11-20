---
title: Azure Event Hubs olayları Azure 'da günlüğe kaydetme | API Management | Microsoft Docs
description: Azure API Management 'da olayları Azure Event Hubs günlüğe kaydetme hakkında bilgi edinin.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 88f6507d-7460-4eb2-bffd-76025b73f8c4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/29/2018
ms.author: apimpm
ms.openlocfilehash: 646d9206ec82d5f35ccab9365e76276ff779d225
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073480"
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Azure Event Hubs olayları Azure 'da günlüğe kaydetme API Management
Azure Event Hubs, bağlı cihazlarınız ve uygulamalarınız tarafından üretilen oldukça büyük miktardaki verileri işleyip analiz edebilmeniz için saniye başına milyonlarca olayı işleyebilen ileri düzeyde ölçeklenebilir bir veri alım sistemidir. Event Hubs, bir olay ardışık düzeni için "ön kapı" olarak görev yapar ve veriler bir olay hub 'ına toplandıktan sonra herhangi bir gerçek zamanlı analiz sağlayıcısı veya toplu işlem/depolama bağdaştırıcısı kullanılarak dönüştürülebilir ve depolanabilir. Event Hubs olay akışı üretimlerini bu olayların tüketilmesinden ayırır, böylece olay tüketicileri olaylara kendi zamanlamalarında erişebilir.

Bu makale, [azure API Management Event Hubs videoyla tümleştirmenize](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) yardımcı olur ve Azure Event Hubs kullanarak API Management olaylarının nasıl günlüğe alınacağını açıklar.

## <a name="create-an-azure-event-hub"></a>Azure Olay Hub’ı oluşturma

Olay Hub 'ı oluşturma ve Olay Hub 'ından olayları göndermeniz ve almanız gereken bağlantı dizelerini alma hakkında ayrıntılı adımlar için, bkz. [Azure Portal kullanarak bir Event Hubs ad alanı ve bir olay hub 'ı oluşturma](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="create-an-api-management-logger"></a>API Management günlükçüsü oluşturma
Artık bir olay hub 'ına sahip olduğunuza göre, bir sonraki adım, olayları Olay Hub 'ına kaydetmek için API Management hizmetinizde bir [günlükçü](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) yapılandırmaktır.

API Management Günlükçüler [API Management REST API](https://aka.ms/smapi)kullanılarak yapılandırılır. REST API ilk kez kullanmadan önce [önkoşulları](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest) gözden geçirin ve [REST API erişimi etkinleştirmiş](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#EnableRESTAPI)olduğunuzdan emin olun.

Bir günlükçü oluşturmak için aşağıdaki URL şablonunu kullanarak bir HTTP PUT isteği yapın:

`https://{your service}.management.azure-api.net/loggers/{new logger name}?api-version=2017-03-01`

* API Management `{your service}` hizmet örneğinizin adıyla değiştirin.
* Yeni `{new logger name}` günlükçü için istenen ad ile değiştirin. Bu adı,- [eventhub oturumunu](/azure/api-management/api-management-advanced-policies#log-to-eventhub) yapılandırma

İsteğe aşağıdaki üstbilgileri ekleyin:

* Content-Type: Application/JSON
* Yetkisi SharedAccessSignature 58...
  * `SharedAccessSignature` [Azure API Management REST API kimlik doğrulaması](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-authentication)' nı oluşturma hakkında yönergeler için bkz.

Aşağıdaki şablonu kullanarak istek gövdesini belirtin:

```json
{
  "loggerType" : "AzureEventHub",
  "description" : "Sample logger description",
  "credentials" : {
    "name" : "Name of the Event Hub from the portal",
    "connectionString" : "Endpoint=Event Hub Sender connection string"
    }
}
```

* `loggerType`olarak `AzureEventHub`ayarlanmalıdır.
* `description`, günlükçü için isteğe bağlı bir açıklama sağlar ve isterseniz sıfır uzunluğunda bir dize olabilir.
* `credentials`Azure Olay Hub `connectionString` 'ınızın vesürümleriniiçerir.`name`

İsteği yaptığınızda, günlükçü oluşturulduysa bir durum kodu `201 Created` döndürülür. Yukarıdaki örnek isteği temel alan örnek bir yanıt aşağıda gösterilmiştir.

```json
{
    "id": "/loggers/{new logger name}",
    "loggerType": "azureEventHub",
    "description": "Sample logger description",
    "credentials": {
        "name": "Name of the Event Hub from the Portal",
        "connectionString": "{{Logger-Credentials-xxxxxxxxxxxxxxx}}"
    },
    "isBuffered": true,
    "resourceId": null
}
```

> [!NOTE]
> Diğer olası dönüş kodları ve nedenleri için bkz. [günlükçü oluşturma](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity#PUT). Liste, güncelleştirme ve silme gibi diğer işlemlerin nasıl gerçekleştirileceğini görmek için, [günlükçü](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) varlık belgelerine bakın.
>
>

## <a name="configure-log-to-eventhubs-policies"></a>Günlük-eventhubs ilkelerini yapılandırma

Günlükçü API Management ' de yapılandırıldıktan sonra, günlük-eventhubs ilkelerinizi istenen olayları günlüğe kaydetmek için yapılandırabilirsiniz. Günlük-eventhubs ilkesi, gelen ilke bölümünde veya giden ilke bölümünde kullanılabilir.

1. APIM örneğinize göz atın.
2. API sekmesini seçin.
3. İlkeyi eklemek istediğiniz API 'yi seçin. Bu örnekte, **sınırsız** ürüne **yankı API** 'sine bir ilke ekliyoruz.
4. **Tüm işlemler**’i seçin.
5. Ekranın üst kısmında Tasarım sekmesini seçin.
6. Gelen veya giden işleme penceresinde üçgeni (kurşun kalem ' ın yanında) tıklatın.
7. Kod düzenleyicisini seçin. Daha fazla bilgi için bkz. [ilkeleri ayarlama veya düzenleme](set-edit-policies.md).
8. İmlecinizi `inbound` veya`outbound` ilke bölümünde konumlandırın.
9. Sağdaki pencerede,**EventHub için** **Gelişmiş İlkeler** > günlüğü ' nü seçin. Bu, `log-to-eventhub` ilke ekstresi şablonunu ekler.

```xml
<log-to-eventhub logger-id ='logger-id'>
  @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
</log-to-eventhub>
```
Önceki `logger-id` adımda günlükçü oluşturmak için URL 'de `{new logger name}` için kullandığınız değerle değiştirin.

`log-to-eventhub` Öğesi için değer olarak bir dize döndüren herhangi bir ifadeyi kullanabilirsiniz. Bu örnekte, tarih ve saat, hizmet adı, istek kimliği, istek IP adresi ve işlem adı içeren bir dize günlüğe kaydedilir.

Güncelleştirilmiş ilke yapılandırmasını kaydetmek için **Kaydet** ' e tıklayın. Kaydedilen ilke etkin olur ve olaylar belirlenen Olay Hub 'ına kaydedilir.

## <a name="next-steps"></a>Sonraki adımlar
* Azure Event Hubs hakkında daha fazla bilgi
  * [Azure Event Hubs kullanmaya başlama](../event-hubs/event-hubs-c-getstarted-send.md)
  * [EventProcessorHost ile ileti alma](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Event Hubs programlama kılavuzu](../event-hubs/event-hubs-programming-guide.md)
* API Management ve Event Hubs tümleştirmesi hakkında daha fazla bilgi edinin
  * [Günlükçü varlık başvurusu](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity)
  * [günlük-eventhub ilke başvurusu](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#log-to-eventhub)
  * [Azure API Management, Event Hubs ve Moesif ile API 'lerinizi izleme](api-management-log-to-eventhub-sample.md)  
* [Azure Application Insights ile tümleştirme](api-management-howto-app-insights.md) hakkında daha fazla bilgi edinin

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png
