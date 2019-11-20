---
title: 'Hızlı başlangıç: uygulama oluşturma-LUSıS'
titleSuffix: Azure Cognitive Services
description: Işıkları ve cihazları açıp kapatmak için önceden oluşturulmuş `HomeAutomation` etki alanını kullanan bir LUIS uygulaması oluşturun. Önceden oluşturulmuş olan bu etki alanı amaçlara, varlıklara ve örnek konuşmalara sahiptir. İşlemi tamamladığınızda bulut üzerinde çalışan bir LUIS uç noktasına sahip olacaksınız.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 11/04/2019
ms.author: diberry
ms.openlocfilehash: 22a37dbd0b76710a14183aec1795639614207d16
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73613675"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Hızlı başlangıç: Önceden oluşturulmuş ev otomasyonu uygulamasını kullanma

[!INCLUDE [Uses preview portal](./includes/uses-portal-preview.md)]

Bu hızlı başlangıçta ışıkları ve cihazları açıp kapatmak için önceden oluşturulmuş `HomeAutomation` etki alanını kullanan bir LUIS uygulaması oluşturacaksınız. Önceden oluşturulmuş olan bu etki alanı amaçlara, varlıklara ve örnek konuşmalara sahiptir. İşlemi tamamladığınızda bulut üzerinde çalışan bir LUIS uç noktasına sahip olacaksınız.

## <a name="prerequisites"></a>Ön koşullar

Bu makale için [https://www.luis.ai](https://www.luis.ai) adresindeki LUIS portalından oluşturulan ücretsiz bir LUIS hesabına ihtiyacınız olacak. 

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

[!INCLUDE [Select authoring resource](./includes/select-authoring-resource.md)]

## <a name="create-a-new-app"></a>Yeni bir uygulama oluşturma
**Uygulamalarım** sayfasından uygulama oluşturabilir ve yönetebilirsiniz. 

1. LUU portalında, Uygulamalarım listesinden **+ Oluştur**' u seçin.

    ![LUU portalında, Uygulamalarım listesinde ' + oluştur ' u seçin.](./media/create-app-in-portal.png)

1. İletişim kutusunda `Home Automation` uygulamanızı adlandırın, sonra **bitti**' yi seçin. LUO uygulamayı oluşturur. Açıklama isteğe bağlıdır ve yazma veya tahmin için kullanılmaz. Bir LUSıS uygulaması oluştururken tahmin kaynağı da isteğe bağlıdır. Uygulamanızı üretime yayımladığınızda, uygulamanızın birçok isteği işleyebilmesi için bir tahmin kaynağı atamanız gerekir.  

    ![İletişim kutusunda, uygulamanızın giriş Otomasyonu ' nu adlandırın](./media/create-new-app-details.png)

    >[!NOTE]
    >Uygulama oluşturduktan sonra kültür değiştirilemez. 

## <a name="add-prebuilt-domain"></a>Önceden oluşturulmuş etki alanını ekleme

**Önceden oluşturulmuş etki alanlarını** seçin ve ardından **homeautomation**' ı arayın. HomeAutomation kartında **etki alanı Ekle** ' yi seçin.

![' Önceden oluşturulmuş etki alanları ' ' nı seçin ve ' HomeAutomation ' araması yapın. HomeAutomation kartında ' etki alanı Ekle ' seçeneğini belirleyin.](media/luis-quickstart-new-app/home-automation.png)

Etki alanı başarıyla eklendiğinde önceden oluşturulmuş etki alanı kutusunda **Etki alanını kaldır** düğmesi görüntülenir.

## <a name="intents-and-entities"></a>Amaçlar ve varlıklar

HomeAutomation etki alanı amaçlarını gözden geçirmek için **amaçlar** ' ı seçin. Önceden oluşturulmuş etki alanı amaçları, örnek söylenebilir.

![HomeAutomation amaçları listesinin ekran görüntüsü](media/luis-quickstart-new-app/home-automation-intents.png "HomeAutomation amaçları listesinin ekran görüntüsü")

> [!NOTE]
> **Hiçbiri**, tüm LUIS uygulamaları tarafından sağlanan bir amaçtır. Uygulamanızın sağladığı işlevleri karşılamayan konuşmaların işlenmesi için bunu seçersiniz. 

**HomeAutomation.TurnOff** amacını seçin. Amaçta varlıklarla etiketlenmiş olan konuşmaların bir listesini görebilirsiniz.

[![HomeAutomation. kapatma amaçlı ekran görüntüsü](media/luis-quickstart-new-app/home-automation-turnoff.png "HomeAutomation. kapatma amaçlı ekran görüntüsü")](media/luis-quickstart-new-app/home-automation-turnoff.png)

## <a name="train-the-luis-app"></a>LUIS uygulamasını eğitme

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="test-your-app"></a>Uygulamanızı test etme
Uygulamanızı eğittikten sonra test edebilirsiniz. **Test**' i seçin. Etkileşimli test bölmesine `Turn off the lights` benzer bir test yazın ve ENTER tuşuna basın. 

```
Turn off the lights
```

En yüksek puana sahip olan amacın test konuşması için beklediğiniz amaca karşılık geldiğinden emin olun.

Bu örnekte `Turn off the lights`, **Homeautomation. TurnOff**'un en üst Puanlama hedefi olarak doğru şekilde tanımlanır.

![Söylenişi vurgulanmış şekilde test paneli ekran görüntüsü](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

Tahmin hakkında daha fazla **bilgi için İnceleme** ' yi seçin.

![İnceleme bilgileriyle test bölmesinin ekran görüntüsü](media/luis-quickstart-new-app/test.png)

Test bölmesini daraltmak için yeniden **Test**'i seçin. 

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Uç nokta URL'sini almak için uygulamayı yayımlama

[!INCLUDE [LUIS How to Publish steps](./includes/howto-publish.md)]

<a name="query-the-v2-api-prediction-endpoint"></a>

## <a name="query-the-v3-api-prediction-endpoint"></a>V3 API tahmini uç noktasını sorgulama

[!INCLUDE [LUIS How to get endpoint first step](./includes/v3-prediction-endpoint.md)] 

1. Tarayıcı adres çubuğunda, sorgu dizesi için aşağıdaki ad ve değer çubuklarının URL 'de olduğundan emin olun. Sorgu dizesinde yoksa, bunları ekleyin:

    |Ad/değer çifti|
    |--|
    |`verbose=true`|
    |`show-all-intents=true`|

1. Tarayıcının adres çubuğunda URL 'nin sonuna gidin ve _sorgu_ değeri için `turn off the living room light` girin ve ardından ENTER tuşuna basın.

    ```json
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOff",
            "intents": {
                "HomeAutomation.TurnOff": {
                    "score": 0.984315455
                },
                "HomeAutomation.QueryState": {
                    "score": 0.009912962
                },
                "HomeAutomation.TurnDown": {
                    "score": 0.00626645749
                },
                "HomeAutomation.TurnUp": {
                    "score": 0.00572059769
                },
                "HomeAutomation.SetDevice": {
                    "score": 0.00379381469
                },
                "HomeAutomation.TurnOn": {
                    "score": 0.00366983772
                },
                "None": {
                    "score": 0.000623856
                }
            },
            "entities": {
                "HomeAutomation.Location": [
                    "living room"
                ],
                "HomeAutomation.DeviceName": [
                    [
                        "living room light"
                    ]
                ],
                "HomeAutomation.DeviceType": [
                    [
                        "light"
                    ]
                ],
                "$instance": {
                    "HomeAutomation.Location": [
                        {
                            "type": "HomeAutomation.Location",
                            "text": "living room",
                            "startIndex": 13,
                            "length": 11,
                            "score": 0.907323956,
                            "modelTypeId": 1,
                            "modelType": "Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceName": [
                        {
                            "type": "HomeAutomation.DeviceName",
                            "text": "living room light",
                            "startIndex": 13,
                            "length": 17,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceType": [
                        {
                            "type": "HomeAutomation.DeviceType",
                            "text": "light",
                            "startIndex": 25,
                            "length": 5,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

    [V3 tahmin uç noktası](luis-migration-api-v3.md)hakkında daha fazla bilgi edinin.
    

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Uç noktayı kod ile çağırabilirsiniz:

> [!div class="nextstepaction"]
> [Kod kullanarak bir LUIS uç noktasını çağırma](luis-get-started-cs-get-intent.md)
