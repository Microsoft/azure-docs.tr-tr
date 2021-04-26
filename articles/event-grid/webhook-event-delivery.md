---
title: Web kancası olay teslimi
description: Bu makalede Web kancaları kullanılırken Web kancası olay teslimi ve uç nokta doğrulaması açıklanmaktadır.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 42ba36a21d307ca85d9cdae850c0c9a991e4f30e
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968008"
---
# <a name="webhook-event-delivery"></a>Web kancası olay teslimi
Web kancaları Azure Event Grid olayların alınacağı birçok yol vardır. Yeni bir etkinlik hazırsanız Event Grid hizmet, istek gövdesinde olay ile yapılandırılmış uç noktaya bir HTTP isteği gönderir.

Web kancalarını destekleyen birçok farklı hizmet gibi Event Grid, bu uç noktaya yönelik olayları sunmaya başlamadan önce Web kancası uç noktanızın sahipliğini kanıtlamanızı gerektirir. Bu gereksinim, kötü niyetli bir kullanıcının uç noktanızı etkinliklerle taşmasını önler. Aşağıda listelenen üç Azure hizmetinden birini kullandığınızda, Azure altyapısı bu doğrulamayı otomatik olarak işler:

- [Event Grid Bağlayıcısı](/connectors/azureeventgrid/) ile Azure Logic Apps
- [Web kancası](../event-grid/ensure-tags-exists-on-new-virtual-machines.md) aracılığıyla Azure Otomasyonu
- [Event Grid tetikleyicisiyle](../azure-functions/functions-bindings-event-grid.md) Azure işlevleri

## <a name="endpoint-validation-with-event-grid-events"></a>Event Grid olaylar ile uç nokta doğrulaması
HTTP tetikleyici tabanlı bir Azure işlevi gibi başka herhangi bir uç nokta türü kullanıyorsanız, uç nokta kodunuzun Event Grid bir doğrulama el sıkışmasına katılması gerekir. Event Grid, aboneliği doğrulamak için iki yolu destekler.

1. **Zaman uyumlu el sıkışma**: olay aboneliği oluşturma sırasında Event Grid, uç noktanıza bir abonelik doğrulama olayı gönderir. Bu olayın şeması, diğer tüm Event Grid olayına benzerdir. Bu olayın veri bölümü bir `validationCode` özelliği içerir. Uygulamanız, doğrulama isteğinin beklenen bir olay aboneliği olduğunu doğrular ve yanıt içindeki doğrulama kodunu zaman uyumlu olarak döndürür. Bu el sıkışma mekanizması tüm Event Grid sürümlerinde desteklenir.

2. **Zaman uyumsuz el sıkışma**: belirli durumlarda, yanıt olarak yanıt olarak doğrulama kodunu geri dönemiyoruz. Örneğin, üçüncü taraf bir hizmet kullanıyorsanız ( [`Zapier`](https://zapier.com) veya [IFTTT](https://ifttt.com/)gibi), doğrulama koduyla program aracılığıyla yanıt veremez.

   Sürüm 2018-05-01-önizleme ile başlayarak, Event Grid el ile doğrulama anlaşmasını destekler. API sürümü 2018-05-01-Preview veya sonraki bir sürümünü kullanan bir SDK veya araçla bir olay aboneliği oluşturuyorsanız, Event Grid `validationUrl` Abonelik doğrulama olayının veri bölümünde bir özellik gönderir. El sıkışmasını gerçekleştirmek için, bu URL 'YI olay verilerinde bulun ve bir GET isteği yapın. Bir REST istemcisi ya da Web tarayıcınızı kullanabilirsiniz.

   Belirtilen URL **5 dakika** için geçerlidir. Bu süre boyunca, olay aboneliğinin sağlama durumu olur `AwaitingManualAction` . El ile doğrulamayı 5 dakika içinde tamamlamazsanız, sağlama durumu olarak ayarlanır `Failed` . El ile doğrulamayı başlatmadan önce olay aboneliği oluşturmanız gerekir.

   Bu kimlik doğrulama mekanizması Ayrıca, el ile doğrulama moduna alınmadan önce doğrulama olayının GÖNDERISINI kabul ettiğini bilmesi için Web kancası uç noktasının 200 HTTP durum kodunu döndürmesini gerektirir. Diğer bir deyişle, uç nokta 200 döndürürse ancak bir doğrulama yanıtını zaman uyumlu olarak geri döndürmezse mod el ile doğrulama moduna geçirilir. Doğrulama URL 'sinde 5 dakika içinde bir GET varsa, doğrulama el sıkışması başarılı olarak kabul edilir.

> [!NOTE]
> Doğrulama için otomatik olarak imzalanan sertifikalar kullanılması desteklenmez. Bunun yerine ticari bir sertifika yetkilisinden (CA) imzalı bir sertifika kullanın.

### <a name="validation-details"></a>Doğrulama ayrıntıları

- Olay aboneliği oluşturma/güncelleştirme sırasında, Event Grid bir abonelik doğrulama olayını hedef uç noktaya gönderir.
- Olay, "AEG-Event-Type: SubscriptionValidation" başlık değerini içerir.
- Olay gövdesi diğer Event Grid olaylarıyla aynı şemaya sahip.
- Olayın eventType özelliği `Microsoft.EventGrid.SubscriptionValidationEvent` .
- Olayın Data özelliği `validationCode` rastgele oluşturulmuş bir dizeye sahip bir özelliği içerir. Örneğin, "validationCode: acb13...".
- Olay verileri, `validationUrl` aboneliği el ile doğrulamak IÇIN URL içeren bir özellik de içerir.
- Dizi yalnızca doğrulama olayını içerir. Diğer olaylar, doğrulama kodunu yankıladıktan sonra ayrı bir istekte gönderilir.
- EventGrid veri düzlemi SDK 'larının, abonelik doğrulama olay verileri ve abonelik doğrulama yanıtına karşılık gelen sınıfları vardır.

Aşağıdaki örnekte bir SubscriptionValidationEvent örneği gösterilmektedir:

```json
[
  {
    "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
    "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "subject": "",
    "data": {
      "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
      "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/estest/validate?id=512d38b6-c7b8-40c8-89fe-f46f9e9622b6&t=2018-04-26T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1A1A1A1A"
    },
    "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
    "eventTime": "2018-01-25T22:12:19.4556811Z",
    "metadataVersion": "1",
    "dataVersion": "1"
  }
]
```

Uç nokta sahipliğini kanıtlamak için, aşağıdaki örnekte gösterildiği gibi validationResponse özelliğindeki doğrulama kodunu geri yankılayın:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Bir HTTP 200 Tamam yanıt durum kodu döndürmelidir. Kabul edilen HTTP 202, geçerli bir Event Grid Abonelik doğrulama yanıtı olarak tanınmıyor. HTTP isteğinin 30 saniye içinde tamamlaması gerekir. İşlem 30 saniye içinde sonlanmazsa, işlem iptal edilir ve 5 saniye sonra yeniden denenecek. Tüm denemeler başarısız olursa, doğrulama el sıkışma hatası olarak kabul edilir.

Ya da, doğrulama URL 'sine bir GET isteği göndererek aboneliği el ile doğrulayabilirsiniz. Olay aboneliği, onaylanana kadar bekleyen bir durumda kalır. Doğrulama URL 'Si 553 numaralı bağlantı noktasını kullanır. Güvenlik Duvarı kurallarınız bağlantı noktası 553 ' i engellerseniz, başarılı el ile el sıkışma için kuralların güncellenmesi gerekebilir.

Abonelik doğrulama anlaşmasını işleme bir örnek için bkz. bir [C# örneği](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs).

## <a name="endpoint-validation-with-cloudevents-v10"></a>CloudEvents v 1.0 ile uç nokta doğrulaması
Zaten Event Grid biliyorsanız, uygunsuz kullanımı önlemek için Event Grid uç nokta doğrulama elsıkışmasının farkında olabilirsiniz. CloudEvents v 1.0, HTTP SEÇENEKLERI metodunu kullanarak kendi [uygunsuz kullanım koruma semantiğini](webhook-event-delivery.md) uygular. [Buradan](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection)daha fazla bilgi edinebilirsiniz. Çıkış için CloudEvents şeması kullanılırken, Event Grid Event Grid doğrulama olay mekanizması yerine CloudEvents v 1.0 kötüye kullanımı koruması ile birlikte kullanılır.

## <a name="next-steps"></a>Sonraki adımlar
Olay aboneliği doğrulamaları sorunlarını giderme hakkında bilgi edinmek için aşağıdaki makaleye bakın: 

[Olay aboneliği doğrulamaları sorunlarını giderme](troubleshoot-subscription-validation.md)
