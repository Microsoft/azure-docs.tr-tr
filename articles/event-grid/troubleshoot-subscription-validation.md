---
title: Azure Event Grid-Abonelik doğrulama sorunlarını giderme
description: Bu makalede, abonelik doğrulamalarında nasıl sorun giderebileceğiniz gösterilmektedir.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 857760182675d5673a3b09495c2faaf7372a4164
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94592949"
---
# <a name="troubleshoot-azure-event-grid-subscription-validations"></a>Azure Event Grid abonelik doğrulamaları sorunlarını giderme
Olay aboneliği oluşturma sırasında, gibi bir hata mesajı görüyorsanız `The attempt to validate the provided endpoint https://your-endpoint-here failed. For more details, visit https://aka.ms/esvalidation` , doğrulama el sıkışmasının bir hata olduğunu gösterir. Bu hatayı çözmek için aşağıdaki noktaları doğrulayın:

- Postman veya kıvrık ya da benzer bir aracı kullanarak [örnek bir SubscriptionValidationEvent](webhook-event-delivery.md#validation-details) istek gövdesi ile Web kancası URL 'niz IÇIN BIR http gönderisi yapın.
- Web kancası, zaman uyumlu doğrulama el sıkışma mekanizması uygualıyorsa, yanıtın bir parçası olarak ValidationCode 'un döndürüldüğünden emin olun.
- Web kancası, zaman uyumsuz doğrulama el sıkışma mekanizmasını uygulıyorsanız, HTTP POST 'un 200 doğru olduğunu doğrulayın.
- Web kancasına yanıt verildiğinde `403 (Forbidden)` , Web kancasının bir Azure Application Gateway veya Web uygulaması güvenlik duvarının arkasında olup olmadığını kontrol edin. Bu durumda, bu güvenlik duvarı kurallarını devre dışı bırakıp bir HTTP POST işlemini yeniden gerçekleştirmeniz gerekir:
    - 920300 (istek bir Accept üst bilgisini içermiyor)
    - 942430 (kısıtlanan SQL karakter anomali algılama (args): özel karakter sayısı aşıldı (12))
    - 920230 (birden çok URL kodlaması algılandı)
    - 942130 (SQL ekleme saldırısı: SQL tautology algılandı.)
    - 931130 (olası uzaktan dosya ekleme (RFı) saldırısı = kapalı etki alanı başvurusu/bağlantı)

> [!IMPORTANT]
> Web kancaları için uç nokta doğrulaması hakkında ayrıntılı bilgi için bkz. [Web kancası olay teslimi](webhook-event-delivery.md).

Aşağıdaki bölümlerde, Postman ve kıvrımlı kullanarak bir olay aboneliklerinin nasıl doğrulanacağı gösterilmektedir.  

## <a name="validate-event-grid-event-subscription-using-postman"></a>Postman kullanarak olay aboneliğini Event Grid doğrulama
Aşağıda, bir Event Grid olayının Web kancası aboneliğini doğrulamak için Postman kullanmanın bir örneği verilmiştir: 

![Postman kullanarak olay Kılavuzu olay aboneliği doğrulaması](./media/troubleshoot-subscription-validation/event-subscription-validation-postman.png)

Örnek bir **Subscriptionvalidationevent** JSON 'si aşağıda verilmiştir:

```json
[
  {
    "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
    "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "subject": "",
    "data": {
      "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
    },
    "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
    "eventTime": "2018-01-25T22:12:19.4556811Z",
    "metadataVersion": "1",
    "dataVersion": "1"
  }
]
```

Örnek başarılı yanıt aşağıda verilmiştir:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Web kancaları için Event Grid olay doğrulama hakkında daha fazla bilgi edinmek için bkz. [Event Grid olayları Ile Endpoint Validation](webhook-event-delivery.md#endpoint-validation-with-event-grid-events).


## <a name="validate-event-grid-event-subscription-using-curl"></a>Kıvrımlı kullanarak olay aboneliğini Event Grid doğrulama 
Aşağıda, bir Event Grid olayının Web kancası aboneliğini doğrulamaya yönelik örnek kıvrımlı komut verilmiştir: 

```bash
curl -X POST -d '[{"id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66","topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx","subject": "","data": {"validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"},"eventType": "Microsoft.EventGrid.SubscriptionValidationEvent","eventTime": "2018-01-25T22:12:19.4556811Z", "metadataVersion": "1","dataVersion": "1"}]' -H 'Content-Type: application/json' https://{your-webhook-url.com}
```

## <a name="validate-cloud-event-subscription-using-postman"></a>Postman kullanarak bulut olay aboneliğini doğrulama
Bir bulut olayının Web kancası aboneliğini doğrulamak için Postman kullanmanın bir örneği aşağıda verilmiştir: 

![Postman kullanarak bulut olay aboneliği doğrulaması](./media/troubleshoot-subscription-validation/cloud-event-subscription-validation-postman.png)

Bulut olaylarıyla doğrulama için **http seçenekleri** yöntemini kullanın. Web kancaları için bulut olay doğrulama hakkında daha fazla bilgi edinmek için bkz. [Cloud Events Ile Endpoint Validation](webhook-event-delivery.md#endpoint-validation-with-event-grid-events).

## <a name="troubleshoot-event-subscription-validation"></a>Olay aboneliği doğrulama sorunlarını giderme

## <a name="next-steps"></a>Sonraki adımlar
Daha fazla yardıma ihtiyacınız varsa sorununuzu [Stack Overflow forumuna](https://stackoverflow.com/questions/tagged/azure-eventgrid) gönderin veya bir [destek bileti](https://azure.microsoft.com/support/options/)açın. 
