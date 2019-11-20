---
title: 'Öğretici: amaçları tahmin etme-LUO'
titleSuffix: Azure Cognitive Services
description: Bu öğreticide, kullanıcının amaç 'ı tahmin eden özel bir uygulama oluşturun. E-posta adresleri veya tarihler gibi konuşma metinlerinden çeşitli veri öğeleri ayıklamadığından bu uygulama en basit LUIS uygulaması türüdür.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: diberry
ms.openlocfilehash: 1e39126324de486d118f808b37672c9fd08af711
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822753"
---
# <a name="tutorial-build-luis-app-to-determine-user-intentions"></a>Öğretici: Kullanıcı amaçları 'nı öğrenmek için LUSıS uygulaması oluşturma

Bu öğreticide, bir kullanıcının, söylenişi (metin) temelinde bir amaç tahmin eden özel bir uygulama oluşturacaksınız. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

**Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:**

> [!div class="checklist"]
> * Yeni bir uygulama oluşturma 
> * Amaç oluşturma
> * Örnek konuşmalar ekleme
> * Uygulamayı eğitme
> * Uygulama yayımlama
> * Uç noktadan amaç tahminini al


[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]

## <a name="user-intentions-as-intents"></a>Amaç olarak Kullanıcı amaçları

Uygulamanın amacı, konuşma, doğal dil metinleriyle bir amaç belirlemektir: 

`I'd like to order a veggie pizza with a salad on the side.`

Bunlar **Amaçlar** şeklinde kategorilere ayrılır. 

|Amaç|Amaç|
|--|--|
|`ModifyOrder`|Kullanıcının pizza sırasını belirleme.|
|`Greeting`|Bot konuşmasına başla.|
|`ConfirmOrder`|Pizza sırasını onaylayın.|
|`None`|Kullanıcının uygulamanın yanıt vermesi beklenen bir şeyi isteyip istemediğini sorar. Bu amaç, uygulama oluşturmanın bir parçası olarak sağlanmışsa ve silinemezler. |

## <a name="create-a-new-app"></a>Yeni bir uygulama oluşturma

[!INCLUDE [Follow these steps to create a new LUIS app](includes/create-pizza-app.md)]

## <a name="create-a-new-intent"></a>Yeni amaç oluşturma 

1. [ÖNIZLEME Luu portalında](https://preview.luis.ai), uygulamanın **Build** bölümünde **+ Oluştur**' u seçin. Aşağıda listelenen yeni amaç adını girip **bitti**' yi seçin.

    `OrderPizza` amacı tahmin edilir: bir Kullanıcı bir pizza siparişi ister. 

1. Bir kullanıcının sormasını beklediğinizi bu amaca birkaç örnek ekleyin:

    |`OrderPizza` örnek söylenme|
    |--|
    |`can i get a pepperoni pizza and a can of coke please`|
    |`can i get a small pizza with onions peppers and olives`|
    |`delivery for a small pepperoni pizza`|
    |`pickup a cheddar cheese pizza large with extra anchovies`|
    |`i need 2 large cheese pizzas 6 large pepperoni pizzas and 1 large supreme pizza`|

    ![Örnek konuşmalar ekleme](media/tutorial-intents-only/add-example-utterances-for-pizza-order.png)

    _Örnek_bir amaç sunarak, bu amaçla ne tür bir anlamı tahmin etmeniz gerektiğini eğitme. 

    [!INCLUDE [Do not use too few utterances](includes/do-not-use-too-few-utterances.md)]    

## <a name="create-remaining-intents"></a>Kalan amaçları oluştur

1. `Greeting` hedefini oluşturun ve aşağıdaki örnek söyleyeni ekleyin. Bu, bir kullanıcının yeni bir pizza siparişi konuşmasıyla başlayıp kullanmadığını belirleme amacınızı belirlemektir.

    |`Greeting` örnek söylenme|
    |--|
    |`Hi`|
    |`Hello`|
    |`Hey`|
    |`Start`|
    |`Begin`|

1. `Confirm` hedefini oluşturun ve aşağıdaki örnek söyleyeni ekleyin. Bu, bir kullanıcının sıralamayı ve sipariş ayrıntılarını kabul edip etmeyeceğini belirleme amacınızı belirlemektir. 

    |`Confirm` örnek söylenme|
    |--|
    |`Go ahead`|
    |`ok`|
    |`Yes`|
    |`Sure`|


## <a name="none-intent-example-utterances"></a>Hiçbiri amacı örnek söyliği

[!INCLUDE [Follow these steps to add the None intent to the app](includes/add-example-utterances-none-intent.md)]

## <a name="train-the-app"></a>Uygulamayı eğitme 

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app"></a>Uygulamayı yayımlama 

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)] 

## <a name="get-intent-prediction"></a>Amaç tahminini al

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Adres çubuğunda URL 'nin sonuna gidin ve şunu girin:

    `get a medium vegetarian pizza for delivery` 

    Bu, bir örnek ile tam olarak aynı değildir. bu nedenle, LUYA 'nın bu amaçla ne tahmin edilebilir olduğunu öğreniyor olup olmadığını görmek için iyi bir test olması gerekir.

    Son sorgu dizesi parametresi konuşma `q`sorgusu**olan** öğesidir. Bu konuşma, örnek konuşmalarından hiçbiriyle aynı değil. İyi bir test olduğundan `OrderPizza` amacını en yüksek puanlı amaç olarak döndürmelidir. 

    ```JSON
    {
        "query": "get a medium vegetarian pizza for delivery",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.6488959
                },
                "None": {
                    "score": 0.139966831
                },
                "Confirm": {
                    "score": 0.00736504374
                },
                "Greeting": {
                    "score": 0.003970454
                }
            },
            "entities": {}
        }
    }
    ```

    Bu uygulamanın şu anda bir varlığı olmadığından varlıklar dizisi boştur. 

    JSON sonucu, en yüksek puanlı amacı **`prediction.topIntent`** özelliği olarak tanımlar. Tüm puanlar 1 ile 0 arasındadır ve 1 ' e daha iyi puan elde edilir. 

1. **Selamlama** hedefini HEDEFLEMEK için URL **sorgu** parametresini değiştirin:

    `Howdy`

    Bu, bir örnek ile tam olarak aynı değildir. bu nedenle, LUYA 'nın bu amaçla ne tahmin edilebilir olduğunu öğreniyor olup olmadığını görmek için iyi bir test olması gerekir. 

    ```json
    {
        "query": "howdy",
        "prediction": {
            "topIntent": "Greeting",
            "intents": {
                "Greeting": {
                    "score": 0.446016937
                },
                "Confirm": {
                    "score": 0.2390079
                },
                "None": {
                    "score": 0.09119555
                },
                "OrderPizza": {
                    "score": 0.00109590159
                }
            },
            "entities": {}
        }
    }    
    ```
 
    Bu tahminde %44 Güvenirlik puanı vardır. Güven Puanını artırmak için, 15 ila 30 örnek Aralık ekleyin.  

## <a name="client-application-next-steps"></a>İstemci-uygulama sonraki adımları

JSON yanıtı döndürdükten sonra LUIS’in istekle işi biter. LUIS kullanıcı konuşmalarını yanıtlamaz, yalnızca doğal dilde sorulan bilgi türünü tanımlar. Konuşma izleme, Azure bot gibi istemci uygulaması tarafından sağlanır. 


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>İlgili bilgiler

* [Varlık türleri](luis-concept-entity-types.md)
* [Eğitme](luis-how-to-train.md)
* [Yayımlama nasıl yapılır?](luis-how-to-publish-app.md)
* [LUSıS portalında test etme](luis-interactive-test.md)
* [Azure bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)


## <a name="next-steps"></a>Sonraki adımlar

Bu öğretici, bir LUSıS uygulaması, oluşturma amaçları, her amaca yönelik eklenen örnek, eklenen örnek ve son noktadaki yok etme, eğitilen, yayımlanan ve test edilen bir örnek ile oluşturulmuştur. Bunlar, LUIS modeli oluşturmanın temel adımlarıdır. 

> [!div class="nextstepaction"]
> [Bu uygulamaya önceden derlenmiş amaçlar ve varlıklar ekleme](tutorial-machine-learned-entity.md)
