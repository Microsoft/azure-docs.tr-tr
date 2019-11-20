---
title: 'Öğretici: Normal ifade varlığı-LUSıS'
titleSuffix: Azure Cognitive Services
description: Normal İfade varlığını kullanarak bir konuşmadaki tutarlı olarak biçimlendirilmiş verileri ayıklayın.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: diberry
ms.openlocfilehash: db60d87b441836f38309166318dd1778ce828483
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307594"
---
# <a name="tutorial-get-well-formatted-data-from-the-utterance"></a>Öğretici: Utterlik 'ten iyi biçimlendirilmiş veriler alın
Bu öğreticide, **normal ifade** varlığını kullanarak, düzenli olarak biçimlendirilen verileri bir noktadan ayıklamak için bir uygulama oluşturun.

**Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Yeni bir uygulama oluşturma 
> * Amaç ekleme
> * Normal ifade varlığı ekleme 
> * Eğitim
> * Yayımlama
> * Uç noktadan amaçları ve varlıkları alma

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="regular-expression-entities"></a>Normal ifade varlıkları

Bu uygulamanın normal ifade varlığı kullanımı, iyi biçimlendirilmiş Insan kaynakları (HR) form numaralarını bir noktadan çekmekdir. Konuşmanın amacı her zaman makine öğrenimi ile belirlenirse de bu özel varlık türü makine öğrenimli değildir. 

**Basit konuşma örnekleri:**

|Örnek konuşmalar|
|--|
|HRF-123456 nerede?|
|HRF-123234 yazan kim?|
|HRF-456098 Fransızca 'da yayımlandı mı?|
|HRF-456098|
|HRF-456098 tarihi?|
 
Normal bir ifade, şu durumlarda bu tür veri için iyi bir seçimdir:

* veriler düzgün biçimlendirilmiş olduğunda.


## <a name="create-a-new-app"></a>Yeni bir uygulama oluşturma

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-intent-for-finding-form"></a>Form bulmak için amaç oluştur

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. **Create new intent** (Yeni amaç oluştur) öğesini seçin. 

1. Açılan iletişim kutusuna `FindForm` girip **Done** (Bitti) öğesini seçin. 

    ![Arama kutusunda Utilities (Yardımcı Programlar) yazan yeni amaç oluşturma iletişim kutusunun ekran görüntüsü](./media/luis-quickstart-intents-regex-entity/create-new-intent-ddl.png)

1. Amaca örnek konuşmalar ekleyin.

    |Örnek konuşmalar|
    |--|
    |What is the URL for hrf-123456? (hrf-123456 numaralı formun URL'si nedir?)|
    |Where is hrf-345678? (hrf-345678 nerede?)|
    |When was hrf-456098 updated? (hrf-456098 ne zaman güncelleştirildi?)|
    |Did John Smith update hrf-234639 last week? (John Smith, hrf-234639 numaralı formu geçen hafta güncelleştirdi mi?)|
    |HRF-345123 ' nin kaç sürümü var?|
    |Who needs to authorize form hrf-123456? (hrf-123456 numaralı formun yetkilisi kim?)|
    |How many people need to sign off on hrf-345678? (hrf-345678 numaralı formu kaç kişinin onaylaması gerekiyor?)|
    |hrf-234123 date? (hrf-234123 numaralı formun tarihi nedir?)|
    |author of hrf-546234? (hrf-546234 numaralı formun yazarı kim?)|
    |title of hrf-456234? (hrf-456234 numaralı formun başlığı nedir?)|

    [![Yeni utlaslar vurgulanacak amaç sayfasının ekran görüntüsü](./media/luis-quickstart-intents-regex-entity/findform-intent.png)](./media/luis-quickstart-intents-regex-entity/findform-intent.png#lightbox)

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="use-the-regular-expression-entity-for-well-formatted-data"></a>İyi biçimlendirilmiş veriler için normal ifade varlığını kullanın
Form numarasıyla eşleştirilecek normal ifade varlığı: `hrf-[0-9]{6}`. Bu normal ifade, `hrf-` değişmez karakterleriyle eşleşir ancak büyük/küçük harf ve kültür farklarını yoksayar. Tam olarak 6 basamak için 0-9 basamaklarını eşleştirir.

HRF `human resources form` anlamına gelir.

LUIS, bir amaca eklendiğinde konuşmayı belirteçlerine ayırır. Bu konuşmalar için belirteçlere ayırma işlemi, kısa çizginin önüne ve arkasına boşluk ekler, `Where is HRF - 123456?` Normal ifade, konuşmanın belirteçlere ayrılmamış ham biçimine uygulanır. Normal ifade _ham_ biçimine uygulandığından sözcük sınırlarıyla ilgilenmesi gerekmez. 

Aşağıdaki adımları izleyerek LUIS uygulamasına HRF-numara biçimini bildiren bir normal ifade varlığı oluşturun:

1. Sol panelde **Entities** (Varlıklar) öğesini seçin.

1. Entities (Varlıklar) sayfasında **Create new entity** (Yeni varlık oluştur) düğmesini seçin. 

1. Açılan iletişim kutusuna yeni varlık adı olarak `HRF-number` girin, varlık türü olarak **RegEx**'i seçin, **Normal İfade** değeri olarak `hrf-[0-9]{6}` girin ve ardından **Bitti** düğmesini seçin.

    ![Yeni varlık özelliklerinin ayarlandığı açılan iletişim kutusunun ekran görüntüsü](./media/luis-quickstart-intents-regex-entity/create-regex-entity.png)

1. Sol menüden **Amaçlar**'ı, ardından **FindForm** amacını seçerek konuşmalarda etiketlenmiş olan normal ifadeleri görebilirsiniz. 

    [![Konuşmayı var olan varlıkla ve normal ifade düzeniyle etiketleme işleminin ekran görüntüsü](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png)](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png#lightbox)

    Varlık, makine tarafından öğrenilen bir varlık olmadığından, varlık, söz konusu bir şekilde uygulanır ve, her oluşturulduktan sonra LUO Web sitesinde görüntülenir.

## <a name="add-example-utterances-to-the-none-intent"></a>Hiçbiri amacına örnek ekleme 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-before-testing-or-publishing"></a>Test veya yayımlamadan önce uygulamayı eğitme

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Uygulamayı bitiş noktasından sorgulamak üzere yayımlayın

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Uç noktadan amacı ve varlık tahminini alın

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Adresteki URL 'nin sonuna gidin ve aşağıdaki deterance 'i girin:

    `When were HRF-123456 and hrf-234567 published in the last year?`

    Son sorgu dizesi parametresi ifade **s**orgusu olan `q` öğesidir. Bu konuşma, etiketlenmiş olan konuşmalarla aynı olmadığından iyi bir testtir ve `FindForm` amacını `HRF-123456` ile `hrf-234567` olmak üzere iki form numarasını döndürmelidir.

    ```json
    {
      "query": "When were HRF-123456 and hrf-234567 published in the last year?",
      "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9988884
      },
      "intents": [
        {
          "intent": "FindForm",
          "score": 0.9988884
        },
        {
          "intent": "None",
          "score": 0.00204812363
        }
      ],
      "entities": [
        {
          "entity": "hrf-123456",
          "type": "HRF-number",
          "startIndex": 10,
          "endIndex": 19
        },
        {
          "entity": "hrf-234567",
          "type": "HRF-number",
          "startIndex": 25,
          "endIndex": 34
        }
      ]
    }
    ```

    LUIS, normal ifade varlığı kullanarak adlandırılmış verileri ayıklar ve bu durum, JSON yanıtını alan istemci yazılımı için program açısından daha faydalıdır.


## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>İlgili bilgiler

* [Normal ifade](luis-concept-entity-types.md#regular-expression-entity) varlık kavramları
* [Eğitme](luis-how-to-train.md)
* [Yayımlama nasıl yapılır?](luis-how-to-publish-app.md)
* [LUSıS portalında test etme](luis-interactive-test.md)

## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide yeni bir amaç oluşturuldu, örnek konuşmalar eklendi ve ardından konuşmalardan düzgün biçimlendirilmiş veriler ayıklamak için normal ifade varlığı eklendi. Uygulama eğitildikten ve yayımlandıktan sonra uç noktaya gönderilen bir sorgu amacı tanımladı ve ayıklanan verileri döndürdü.

> [!div class="nextstepaction"]
> [Liste varlığı hakkında bilgi edinin](luis-quickstart-intent-and-list-entity.md)

