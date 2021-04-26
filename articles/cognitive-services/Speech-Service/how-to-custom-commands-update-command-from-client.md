---
title: Bir komutu bir istemci uygulamasından güncelleştirme
titleSuffix: Azure Cognitive Services
description: Bir komutu istemci uygulamasından güncelleştirmeyi öğrenin.
services: cognitive-services
author: nitinme
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: nitinme
ms.openlocfilehash: 08c674a7a7ec060a4273836064cb1c21e979e725
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "97560296"
---
# <a name="update-a-command-from-a-client-app"></a>Bir komutu bir istemci uygulamasından güncelleştirme

Bu makalede, bir istemci uygulamasından devam eden bir komutun nasıl güncelleştireceğinizi öğreneceksiniz.

## <a name="prerequisites"></a>Önkoşullar
> [!div class = "checklist"]
> * Önceden oluşturulmuş [Özel Komutlar uygulaması](quickstart-custom-commands-application.md)

## <a name="update-the-state-of-a-command"></a>Komutun durumunu güncelleştirme

İstemci uygulamanız, sesli giriş olmadan devam eden bir komutun durumunu güncelleştirmenizi gerektiriyorsa, komutu güncelleştirmek için bir olay gönderebilirsiniz.

Bu senaryoyu göstermek için, devam eden bir komutun durumunu güncelleştirmek üzere aşağıdaki olay etkinliğini gönderin ( `TurnOnOff` ): 

```json
{
  "type": "event",
  "name": "RemoteUpdate",
  "value": {
    "updatedCommand": {
      "name": "TurnOnOff",
      "updatedParameters": {
        "OnOff": "on"
      },
      "cancel": false
    },
    "updatedGlobalParameters": {},
    "processTurn": true
  }
}
```

Bu etkinliğin anahtar özniteliklerini gözden geçirelim:

| Öznitelik | Açıklama |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **türüyle** | Etkinlik türündedir `"event"` ve olayın adı olması gerekir `"RemoteUpdate"` . |
| **değer** | Özniteliği, `"value"` geçerli komutu güncelleştirmek için gerekli olan öznitelikleri içerir. |
| **updatedCommand** | Öznitelik, `"updatedCommand"` komutun adını içerir. Bu öznitelik içinde, `"updatedParameters"` parametrelerin adlarını ve güncelleştirilmiş değerlerini içeren bir haritadır. |
| **İptal** | Devam eden komutun iptal edilmesi gerekiyorsa özniteliğini `"cancel"` olarak ayarlayın `true` . |
| **updatedGlobalParameters** | Özniteliği `"updatedGlobalParameters"` tıpkı bir eşlemedir `"updatedParameters"` , ancak genel parametreler için kullanılır. |
| **processTurn** | Etkinliğin, etkinlik gönderildikten sonra işlenmesi gerekiyorsa, özniteliğini `"processTurn"` olarak ayarlayın `true` . |

Bu senaryoyu özel komutlar portalında test edebilirsiniz:

1. Daha önce oluşturduğunuz özel komutlar uygulamasını açın. 
1. **Eğitme** ve **Test**' i seçin.
1. Gönderin `turn` .
1. Yan paneli açın ve **etkinlik Düzenleyicisi**' ni seçin.
1. `RemoteCommand`Önceki bölümde belirtilen olayı yazın ve gönderin.
    > [!div class="mx-imgBorder"]
    > ![Uzak bir komutun olayını gösteren ekran görüntüsü.](media/custom-commands/send-remote-command-activity.png)

Parametresinin değeri, `"OnOff"` `"on"` konuşma veya metin yerine istemciden bir etkinlik aracılığıyla nasıl ayarlandığına ilişkin bir açıklama.

## <a name="update-the-catalog-of-the-parameter-for-a-command"></a>Bir komutun parametresinin kataloğunu güncelleştirme

Bir parametre için geçerli seçeneklerin listesini yapılandırdığınızda, parametre değerleri uygulama için genel olarak tanımlanır. 

Örneğimizde, bu, `SubjectDevice` konuşmadan bağımsız olarak desteklenen değerlerin sabit bir listesine sahip olacaktır.

Her konuşma için parametrenin kataloğuna yeni girişler eklemek istiyorsanız, aşağıdaki etkinliği gönderebilirsiniz:

```json
{
  "type": "event",
  "name": "RemoteUpdate",
  "value": {
    "catalogUpdate": {
      "commandParameterCatalogs": {
        "TurnOnOff": [
          {
            "name": "SubjectDevice",
            "values": {
              "stereo": [
                "cd player"
              ]
            }
          }
        ]
      }
    },
    "processTurn": false
  }
}
```
Bu etkinlikle, komutunda parametresinin kataloğuna bir giriş eklediniz `"stereo"` `"SubjectDevice"` `"TurnOnOff"` .

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-commands/update-catalog-with-remote-activity.png" alt-text="Bir katalog güncelleştirmesini gösteren ekran görüntüsü.":::

Birkaç şeyi göz önünde bulundurmanız gerekenler:
- Bu etkinliği yalnızca bir kez göndermeniz gerekir (ideal, bir bağlantıyı başlattıktan hemen sonra).
- Bu etkinliği gönderdikten sonra, olayın `ParameterCatalogsUpdated` istemciye geri gönderilmesini beklemeniz gerekir.

## <a name="add-more-context-from-the-client-application"></a>İstemci uygulamasından daha fazla bağlam ekleyin

İstemci uygulamasından, daha sonra özel komutlar uygulamanızda kullanılabilecek bir konuşma başına ek bağlam ayarlayabilirsiniz. 

Örneğin, özel komutlar uygulamasına bağlı olan cihazın KIMLIĞINI ve adını göndermek istediğiniz senaryoyu düşünün.

Bu senaryoyu test etmek için geçerli uygulamada yeni bir komut oluşturalım:
1. Adlı yeni bir komut oluşturun `GetDeviceInfo` .
1. Örnek tümce ekleyin `get device info` .
1. Tamamlama **kuralı tamamlandığında**, özniteliklerini içeren bir **konuşma yanıtı gönder** eylemi ekleyin `clientContext` .
   ![Bağlama sahip bir konuşmayı göndermek için bir yanıt gösteren ekran görüntüsü.](media/custom-commands/send-speech-response-context.png)
1. Uygulamanızı kaydedin, eğitme ve test edin.
1. Sınama penceresinde, istemci bağlamını güncelleştirmek için bir etkinlik gönderin.

    ```json
    {
       "type": "event",
       "name": "RemoteUpdate",
       "value": {
         "clientContext": {
           "deviceId": "12345",
           "deviceName": "My device"
         },
         "processTurn": false
       }
    }
    ```
1. Metni gönderin `get device info` .
   ![İstemci bağlamını göndermek için bir etkinlik gösteren ekran görüntüsü.](media/custom-commands/send-client-context-activity.png)

Birkaç şeyi göz önünde bulundurmanız gerekenler:
- Bu etkinliği yalnızca bir kez göndermeniz gerekir (ideal, bir bağlantıyı başlattıktan hemen sonra).
- İçin karmaşık nesneleri kullanabilirsiniz `clientContext` .
- `clientContext`Konuşma yanıtlarında, etkinlikleri göndermek ve Web uç noktalarını çağırmak için kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Web uç noktasından bir komutu güncelleştirme](./how-to-custom-commands-update-command-from-web-endpoint.md)
