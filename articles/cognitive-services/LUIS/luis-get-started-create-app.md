---
title: 'Hızlı başlangıç: uygulama oluşturma-LUSıS'
description: Bu hızlı `HomeAutomation` Başlangıçta, ışıkları ve gereçleri açmak ve kapatmak için önceden oluşturulmuş etki alanını kullanan BIR Luo uygulamasının nasıl oluşturulacağı gösterilmektedir. Önceden oluşturulmuş olan bu etki alanı amaçlara, varlıklara ve örnek konuşmalara sahiptir. İşlemi tamamladığınızda bulut üzerinde çalışan bir LUIS uç noktasına sahip olacaksınız.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/29/2021
ms.openlocfilehash: e0972998fad647c1c70be814cb7c13f14a6e1141
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277037"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Hızlı başlangıç: Önceden oluşturulmuş ev otomasyonu uygulamasını kullanma

Bu hızlı başlangıçta ışıkları ve cihazları açıp kapatmak için önceden oluşturulmuş `HomeAutomation` etki alanını kullanan bir LUIS uygulaması oluşturacaksınız. Önceden oluşturulmuş olan bu etki alanı amaçlara, varlıklara ve örnek konuşmalara sahiptir. İşlemi tamamladığınızda bulut üzerinde çalışan bir LUIS uç noktasına sahip olacaksınız.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

[!INCLUDE [Select authoring resource](./includes/select-authoring-resource.md)]

## <a name="create-a-new-app"></a>Yeni uygulama oluşturma
**Uygulamalarım** sayfasından uygulama oluşturabilir ve yönetebilirsiniz.

### <a name="create-an-application"></a>Uygulama oluşturma

Bir uygulama oluşturmak için  **+ Yeni uygulama**' ya tıklayın. 

Görüntülenen pencerede, aşağıdaki bilgileri girin:

|Ad  |Açıklama  |
|---------|---------|
|AName     | Uygulamanız için bir ad. Örneğin, "ana Otomasyon".        |
|Kültür     | Uygulamanızın anladığı ve kulakça dili.   |
|Description | Uygulamanız için bir açıklama.
|Tahmin kaynağı | Sorgu alacak tahmin kaynağı. |

**Bitti** seçeneğini belirleyin.

>[!NOTE]
>Uygulama oluşturduktan sonra kültür değiştirilemez.

## <a name="add-prebuilt-domain"></a>Önceden oluşturulmuş etki alanını ekleme

1. Sol gezinti bölmesinde, **önceden oluşturulmuş etki alanları**' nı seçin.
1. **Homeautomation** için arama yapın.
1. HomeAutomation kartında **etki alanı Ekle** ' yi seçin.

    > [!div class="mx-imgBorder"]
    > ![' Önceden oluşturulmuş etki alanları ' ' nı seçin ve ' HomeAutomation ' araması yapın. HomeAutomation kartında ' etki alanı Ekle ' seçeneğini belirleyin.](media/luis-quickstart-new-app/home-automation.png)

    Etki alanı başarıyla eklendiğinde önceden oluşturulmuş etki alanı kutusunda **Etki alanını kaldır** düğmesi görüntülenir.

## <a name="intents-and-entities&quot;></a>Amaçlar ve varlıklar

1. HomeAutomation etki alanı amaçlarını görmek için sol gezinti menüsünde **amaçlar** ' ı seçin. Ve gibi örnek bir örnek vardır `HomeAutomation.QueryState`     `HomeAutomation.SetDevice` .

    > [!NOTE]
    > **Hiçbiri**, tüm LUIS uygulamaları tarafından sağlanan bir amaçtır. Uygulamanızın sağladığı işlevleri karşılamayan konuşmaların işlenmesi için bunu seçersiniz.

1. **HomeAutomation.TurnOff** amacını seçin. Amaç, varlıklarla etiketlenmiş örnek bir listesini içerir.

    > [!div class=&quot;mx-imgBorder&quot;]
    > [![HomeAutomation.TurnOff amacının ekran görüntüsü](media/luis-quickstart-new-app/home-automation-turnoff.png &quot;HomeAutomation.TurnOff amacının ekran görüntüsü")](media/luis-quickstart-new-app/home-automation-turnoff.png)

1. Uygulamanın varlıklarını görüntülemek istiyorsanız, **varlıklar**' ı seçin. Örneğin, **Homeautomation. aygıtadı** gibi varlıklardan birine tıklarsanız, onunla ilişkili değerlerin bir listesini görürsünüz. 
 
    :::image type="content" source="media/luis-quickstart-new-app/entities-page.png" alt-text="Görüntü alternatif metni" lightbox="media/luis-quickstart-new-app/entities-page.png":::

## <a name="train-the-luis-app"></a>LUIS uygulamasını eğitme

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="test-your-app"></a>Uygulamanızı test etme
Uygulamanızı eğittikten sonra test edebilirsiniz.

1. Sağ üst gezinmede **Test** ' i seçin.

1. Etkileşimli test bölmesine benzer bir test söyliği yazın `Turn off the lights` ve ENTER tuşuna basın. Örneğin *ışıkları devre dışı bırakın*.

    Bu örnekte, `Turn off the lights` **Homeautomation. turnoff**'un en üst Puanlama hedefi olarak doğru şekilde tanımlanır.

    ![Konuşmanın vurgulandığı Test paneli ekran görüntüsü](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

1. Tahmin hakkında daha fazla bilgi görüntülemek için **İnceleme** ' yi seçin.

    > [!div class="mx-imgBorder"]
    > ![İnceleme bilgileriyle test bölmesinin ekran görüntüsü](media/luis-quickstart-new-app/test.png)

1. Test bölmesini kapatın.

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Uç nokta URL'sini almak için uygulamayı yayımlama

Bir sohbet bot veya diğer istemci uygulamasında bir LUO tahmini almak için, uygulamayı tahmin uç noktasına yayımlamanız gerekir.

1. Pencerenin sağ üst köşesinde **Yayımla** ' yı seçin.

1. **Üretim** yuvasını seçin ve **bitti**' yi seçin.

    > [!div class="mx-imgBorder"]
    > ![Uç noktada bir HALSıS yayımlama ekran görüntüsü](media/howto-publish/publish-app-popup.png)

1. **Azure kaynakları** sayfasına gitmek için bildirimde **uç nokta URL 'lerine erişin** bağlantısına tıklayın. Uç nokta URL 'Leri **örnek sorgu** olarak listelenir.

<!-- [!INCLUDE [LUIS How to Publish steps](./includes/howto-publish.md)] -->

<a name="query-the-v2-api-prediction-endpoint"></a>

## <a name="query-the-v3-api-prediction-endpoint"></a>V3 API tahmini uç noktasını sorgulama

[!INCLUDE [LUIS How to get endpoint first step](./includes/v3-prediction-endpoint.md)]

2. Tarayıcı adres çubuğunda, sorgu dizesi için aşağıdaki ad ve değer çubuklarının URL 'de olduğundan emin olun. Sorgu dizesinde yoksa, bunları ekleyin:

    |Ad/değer çifti|
    |--|
    |`verbose=true`|
    |`show-all-intents=true`|

3. Tarayıcının adres çubuğunda URL 'nin sonuna gidin ve `turn off the living room light` _sorgu_ değeri Için yazın ve ENTER tuşuna basın.

    ```json
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOff",
            "intents": {
                "HomeAutomation.TurnOff": {
                    "score": 0.969448864
                },
                "HomeAutomation.QueryState": {
                    "score": 0.0122336326
                },
                "HomeAutomation.TurnUp": {
                    "score": 0.006547436
                },
                "HomeAutomation.TurnDown": {
                    "score": 0.0050634006
                },
                "HomeAutomation.SetDevice": {
                    "score": 0.004951761
                },
                "HomeAutomation.TurnOn": {
                    "score": 0.00312553928
                },
                "None": {
                    "score": 0.000552945654
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
                            "score": 0.902181149,
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

> [!div class="nextstepaction"]
> [LUSıS için yinelemeli uygulama geliştirme](./luis-concept-app-iteration.md)