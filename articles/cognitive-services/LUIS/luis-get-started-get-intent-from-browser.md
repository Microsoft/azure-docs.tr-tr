---
title: Bir tarayıcı kullanarak tahminleri sorgulama-LUSıS
description: Bu makalede, kullanıcının bir tarayıcıda konuşma metinleriyle aynı olduğunu öğrenmek için kullanılabilir bir genel LUL uygulaması kullanın.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/26/2021
ms.openlocfilehash: 4542f85ab562e65777fb8570873305523c76aee6
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106276833"
---
# <a name="how-to-query-the-prediction-runtime-with-user-text"></a>Tahmin çalışma zamanını Kullanıcı metniyle sorgulama

Bir LUIS tahmin uç noktasının ne döndüğünü anlamak için bir tahmin sonucunu bir Web tarayıcısında görüntüleyin.

## <a name="prerequisites"></a>Önkoşullar

Bir ortak uygulamayı sorgulamak için şunlar gerekir:

* Language Understanding (LUSıS) kaynak bilgileriniz:
    * [Luo portalından](https://www.luis.ai/)elde edilebilir **tahmin anahtarı** . Zaten anahtar oluşturmak için bir aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/cognitive-services)için kaydolabilirsiniz.
    * **Tahmin uç noktası** alt etki alanı-aynı zamanda lusıs kaynağınızın **adıdır** .
* Bir LUSıS uygulama KIMLIĞI-genel IoT uygulama KIMLIĞI ' ni kullanın `df67dcdb-c37d-46af-88e1-8b97951ca1c2` . Hızlı başlangıç kodunda kullanılan Kullanıcı sorgusu bu uygulamaya özeldir.

## <a name="use-the-browser-to-see-predictions"></a>Tahminleri görmek için tarayıcıyı kullanın

1. Bir web tarayıcısı açın.
1. `YOUR-KEY`Kendı Lusıs tahmin anahtarınızla değiştirerek aşağıdaki URL 'leri kullanın. İstekler GET isteklerdir ve bir sorgu dizesi parametresi olarak LUSıS tahmin anahtarınızla yetkilendirmeyi içerir.

    #### <a name="v3-prediction-request"></a>[V3 tahmin isteği](#tab/V3-1-1)


    Bir **Get** Endpoint (yuvalara göre) Isteği IÇIN v3 URL 'sinin biçimi:

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY
    `

    #### <a name="v2-prediction-request"></a>[V2 tahmin isteği](#tab/V2-1-2)

    Bir **Get** Endpoint Isteği IÇIN v2 URL 'sinin biçimi:

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=YOUR-LUIS-PREDICTION-KEY&q=turn on all lights
    `

1. URL'yi bir tarayıcı penceresine yapıştırıp Enter tuşuna basın. Tarayıcıda LUIS'in `HomeAutomation.TurnOn` amacını en önemli amaç olarak ve `HomeAutomation.Operation` varlığını `on` değeriyle algıladığını gösteren bir JSON sonucu görüntülenir.

    #### <a name="v3-prediction-response"></a>[V3 tahmin yanıtı](#tab/V3-2-1)

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    #### <a name="v2-prediction-response"></a>[V2 tahmin yanıtı](#tab/V2-2-2)

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```

    * * *

1. Tüm hedefleri görmek için uygun sorgu dizesi parametresini ekleyin.

    #### <a name="v3-prediction-endpoint"></a>[V3 tahmin uç noktası](#tab/V3-3-1)

    `show-all-intents=true` **Tüm amaçları göstermek** için QueryString 'in sonuna ekleyin:

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/predict/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY&show-all-intents=true
    `

    ```JSON
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "None": {
                    "score": 0.08687421
                },
                "HomeAutomation.TurnOff": {
                    "score": 0.0207554
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    #### <a name="v2-prediction-endpoint"></a>[V2 tahmin uç noktası](#tab/V2)

    `verbose=true` **Tüm amaçları göstermek** için QueryString 'in sonuna ekleyin:

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?q=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY&verbose=true
    `

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "intents": [
            {
                "intent": "HomeAutomation.TurnOn",
                "score": 0.5375382
            },
            {
                "intent": "None",
                "score": 0.08687421
            },
            {
                "intent": "HomeAutomation.TurnOff",
                "score": 0.0207554
            }
        ],
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdakiler hakkında daha fazla bilgi edinin:
* [V3 tahmin uç noktası](luis-migration-api-v3.md)
* [Özel alt etki alanları](../cognitive-services-custom-subdomains.md)

> [!div class="nextstepaction"]
> [İstemci kitaplıklarını veya REST API kullanın](client-libraries-rest-api.md)
