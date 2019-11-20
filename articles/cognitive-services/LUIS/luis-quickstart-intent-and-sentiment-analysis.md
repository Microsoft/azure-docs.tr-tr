---
title: 'Öğretici: yaklaşım Analizi-LUSıS'
titleSuffix: Azure Cognitive Services
description: Bu öğreticide, dıklardan olumlu, olumsuz ve nötr yaklaşımı nasıl alacağınızı gösteren bir uygulama oluşturun. Yaklaşım konuşmanın tamamından belirlenir.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 07afd197e514adb0f2fc65c11e9fec552aa05b99
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492661"
---
# <a name="tutorial--get-sentiment-of-utterance"></a>Öğretici: söylenişi 'in duygu kazanın

Bu öğreticide, senslerden olumlu, olumsuz ve nötr yaklaşımı belirlemeyi gösteren bir uygulama oluşturun. Yaklaşım konuşmanın tamamından belirlenir.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Yeni bir uygulama oluşturma
> * Yaklaşım analizini yayımlama ayarı olarak ekleyin
> * Uygulamayı eğitme
> * Uygulama yayımlama
> * Konuşmanın yaklaşımını uç noktasından alma

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="sentiment-analysis-is-a-publish-setting"></a>Yaklaşım Analizi bir yayımlama ayarıdır

Aşağıdaki konuşmalarda yaklaşım örnekleri gösterilmektedir:

|Yaklaşım|Puan|İfade|
|:--|:--|:--|
|pozitif|0,91 |John W. Smith did a great job on the presentation in Paris. (John W. Smith, Paris'teki sunumda harika bir iş çıkardı.)|
|pozitif|0,84 |Seattle mühendisleri, Parker satış aralığı üzerinde çalışır.|

Yaklaşım analizi, tüm konuşmalar için geçerli olan bir yayımlama ayarıdır. Duygu içinde duygu belirten kelimeleri bulmanız ve bunları işaretlemenize gerek yoktur. 

Yayımlama ayarı olduğu için amaçlar veya varlıklar sayfalarında görmezsiniz. [Etkileşimli test](luis-interactive-test.md#view-sentiment-results) bölmesinde veya uç nokta URL'sinde test yaparken görebilirsiniz. 


## <a name="create-a-new-app"></a>Yeni bir uygulama oluşturma

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="add-personname-prebuilt-entity"></a>Kişilik adı önceden oluşturulmuş varlık Ekle 

1. Gezinti menüsünden **Oluştur** ' u seçin.

1. Sol gezinti menüsünden **Entities** (Varlıklar) öğesini seçin.

1. **Add prebuilt entity** (Önceden oluşturulan varlık ekle) düğmesini seçin.

1. Önceden oluşturulmuş varlıklar listesinden aşağıdaki varlığı seçin ve **bitti**' yi seçin:

   * **[Kişi adı](luis-reference-prebuilt-person.md)** 

     ![Önceden oluşturulmuş varlıklar iletişim kutusunda sayının seçildiğini gösteren ekran görüntüsü](./media/luis-quickstart-intent-and-sentiment-analysis/add-personname-prebuilt-entity.png)

## <a name="create-an-intent-to-determine-employee-feedback"></a>Çalışan geri bildirimini belirleme amacını oluşturma

Şirket üyelerinin çalışanlar hakkındaki geri bildirimini yakalamak için yeni bir amaç ekleyin. 

1. Sol panelden **Intents** (Amaçlar) öğesini seçin.

1. **Create new intent** (Yeni amaç oluştur) öğesini seçin.

1. Yeni amaca `EmployeeFeedback` adını verin.

    ![EmployeeFeedback adı görünen Create new intent (Yeni amaç oluştur) iletişim kutusu](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent-ddl.png)

4. Bir çalışanın iyi yaptığı bir işi veya geliştirilmesi gereken bir alanı belirten birden fazla konuşma ekleyin:

    |Konuşmalar|
    |--|
    |John Smith, Maternity Leave 'tan bir ortak çalışan geri geldiğini iyi bir şekilde gerçekleştirmedi|
    |Jill Jones, grief sırasında ortak çalışan Comforting harika bir iş.|
    |Bob Barnes, PaperWork için gereken faturaların tümüne sahip değil.|
    |Gerekli formlarda, imza olmadan en geç bir ayda Todd olarak açıldı|
    |Katherine Kelly, bunu önemli bir pazarlama yerinde toplantısı haline geçirmez.|
    |Larıse Dilm, Haziran gözden geçirmeleri için toplantıyı kaçırdı.|
    |Matematiksel WS 'yi işaret eden satış aralığını Harvard olarak işaretle|
    |Walter Williams, Stanford 'da sunum üzerinde harika bir iş oldu|

    **Görünüm seçeneklerini**belirleyin, adları görmek için **varlık değerlerini göster** ' i seçin.

    [EmployeeFeedback amacına örnek olarak YAĞıN uygulamasının ekran görüntüsünü ![](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png#lightbox)

## <a name="add-example-utterances-to-the-none-intent"></a>Hiçbiri amacına örnek ekleme 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Uygulamaya yapılan değişikliklerin test edilebilir olması için uygulamayı eğitme 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>Uygulamayı yaklaşım analizi içerecek şekilde yapılandırma

1. Sağ üst gezinti bölmesinden **Yönet**'i seçin, sonra sol menüden **Yayımlama ayarları**'nı seçin.

1. **Kullanıcının utterinin pozitif, negatif veya nötr olduğunu belirlemek için yaklaşım analizini kullan** ' ı seçin. Bu ayarı etkinleştirmek için. 

    ![Yayımlama ayarı olarak Yaklaşım Analizi aç](./media/luis-quickstart-intent-and-sentiment-analysis/turn-on-sentiment-analysis-as-publish-setting.png)

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Eğitilen modelin uç noktadan sorgulanabilir olması için uygulamayı yayımlayın

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-the-sentiment-of-an-utterance-from-the-endpoint"></a>Uç noktadan bir duygu duygunluyararlanın

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Adresteki URL 'nin sonuna gidin ve aşağıdaki deterance 'i girin:

    `Jill Jones work with the media team on the public portal was amazing` 

    Son sorgu dizesi parametresi konuşma `q`s**orgusu olan**  öğesidir. Bu konuşma, etiketlenmiş olan konuşmalarla aynı olmadığından iyi bir testtir ve `EmployeeFeedback` amacını yaklaşım analizi ayıklanmış şekilde döndürmelidir.
    
    ```json
    {
      "query": "Jill Jones work with the media team on the public portal was amazing",
      "topScoringIntent": {
        "intent": "EmployeeFeedback",
        "score": 0.9616192
      },
      "intents": [
        {
          "intent": "EmployeeFeedback",
          "score": 0.9616192
        },
        {
          "intent": "None",
          "score": 0.09347677
        }
      ],
      "entities": [
        {
          "entity": "jill jones",
          "type": "builtin.personName",
          "startIndex": 0,
          "endIndex": 9
        }
      ],
      "sentimentAnalysis": {
        "label": "positive",
        "score": 0.8694164
      }
    }
    ```

    SentimentAnalysis %86 puanı ile pozitif bir değer. 

    Tarayıcının adres çubuğunda `q` değerini kaldırarak başka bir yaklaşım deneyin: `William Jones did a terrible job presenting his ideas.` yaklaşım puanı, düşük puan `0.18597582`döndürerek negatif bir yaklaşım gösterir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>İlgili bilgiler

* Yaklaşım analizi, bilişsel hizmet [metin analizi](../Text-Analytics/index.yml)tarafından sağlanır. Özellik [desteklenen diller](luis-language-support.md##languages-supported)metin analizi kısıtlıdır.
* [Eğitme](luis-how-to-train.md)
* [Yayımlama nasıl yapılır?](luis-how-to-publish-app.md)
* [LUSıS portalında test etme](luis-interactive-test.md)


## <a name="next-steps"></a>Sonraki adımlar
Bu öğretici, bir bütün olarak konuşmadan yaklaşım değerleri ayıklamak için bir yayımlama ayarı olarak yaklaşım analizi ekler.

> [!div class="nextstepaction"] 
> [İK uygulamasında uç nokta konuşmasını gözden geçirme](luis-tutorial-review-endpoint-utterances.md) 

