---
title: 'Hızlı başlangıç: tarayıcı için amaç al-LUSıS'
titleSuffix: Azure Cognitive Services
description: Bu hızlı başlangıçta, kullanıcının bir tarayıcıda konuşma metinleriyle aynı olduğunu öğrenmek için kullanılabilir bir genel LUL uygulaması kullanın.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 10/17/2019
ms.author: diberry
ms.openlocfilehash: a99dbc594b53d00ae02b2581d149fe7b4573ab7d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495232"
---
# <a name="quickstart-get-intent-with-a-browser"></a>Hızlı başlangıç: bir tarayıcıyla amaç alın

Bir LUIS tahmin uç noktasının ne döndüğünü anlamak için bir tahmin sonucunu bir Web tarayıcısında görüntüleyin. 

## <a name="prerequisites"></a>Önkoşullar

Bir ortak uygulamayı sorgulamak için şunlar gerekir:

* Kendi Language Understanding (LUSıS) anahtarınız. Zaten anahtar oluşturmak için bir aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)için kaydolabilirsiniz. LUSıS yazma anahtarı çalışmayacak. 
* Ortak uygulamanın KIMLIĞI: `df67dcdb-c37d-46af-88e1-8b97951ca1c2`. 

## <a name="use-the-browser-to-see-predictions"></a>Tahminleri görmek için tarayıcıyı kullanın

1. Bir web tarayıcısı açın. 
1. Aşağıdaki URL 'Leri kullanın ve `YOUR-KEY` kendi LUSıS anahtarınızla değiştirin. İstekler GET isteklerdir ve bir sorgu dizesi parametresi olarak LUSıS anahtarınızla yetkilendirmeyi içerir.

    #### <a name="v3-prediction-requesttabv3-1-1"></a>[V3 tahmin isteği](#tab/V3-1-1)
    
    
    Bir **Get** Endpoint (yuvalara göre) Isteği IÇIN v3 URL 'sinin biçimi:
    
    `
    https://westus.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-KEY
    `

    #### <a name="v2-prediction-requesttabv2-1-2"></a>[V2 tahmin isteği](#tab/V2-1-2)
    
    Bir **Get** Endpoint Isteği IÇIN v2 URL 'sinin biçimi:
    
    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=YOUR-KEY&q=turn on all lights
    `

1. URL'yi bir tarayıcı penceresine yapıştırıp Enter tuşuna basın. Tarayıcıda LUIS'in `HomeAutomation.TurnOn` amacını en önemli amaç olarak ve `HomeAutomation.Operation` varlığını `on` değeriyle algıladığını gösteren bir JSON sonucu görüntülenir.

    #### <a name="v3-prediction-responsetabv3-2-1"></a>[V3 tahmin yanıtı](#tab/V3-2-1)

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

    #### <a name="v2-prediction-responsetabv2-2-2"></a>[V2 tahmin yanıtı](#tab/V2-2-2)

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

    #### <a name="v3-prediction-endpointtabv3-3-1"></a>[V3 tahmin uç noktası](#tab/V3-3-1)

    **Tüm amaçları göstermek**için QueryString 'in sonuna `show-all-intents=true` ekleyin:

    `
    https://westus.api.cognitive.microsoft.com/luis/predict/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-KEY&show-all-intents=true
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

    #### <a name="v2-prediction-endpointtabv2"></a>[V2 tahmin uç noktası](#tab/V2)

    **Tüm amaçları göstermek**için QueryString 'in sonuna `verbose=true` ekleyin:

    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?q=turn on all lights&subscription-key={your-key}&verbose=true
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


<!-- FIX - is the public app getting updated for the new prebuilt domain with entities? -->   

## <a name="next-steps"></a>Sonraki adımlar

[V3 tahmin uç noktası](luis-migration-api-v3.md)hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"]
> [LUSıS portalında uygulama oluşturma](get-started-portal-build-app.md)