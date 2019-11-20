---
title: 'Öğretici: basit varlık, tümcecik listesi-LUSıS'
titleSuffix: Azure Cognitive Services
description: Bu öğreticide, basit varlığı kullanarak bir noktadan itibaren iş işi adının makineye öğrenilen verilerini ayıklayın. Ayıklama doğruluğunu artırmak için, basit varlığa özgü terimlerin tümcecik listesini ekleyin.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: a917176cb06e833745996326520341c1f819c5bf
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73465409"
---
# <a name="tutorial-extract-names-with-simple-entity-and-a-phrase-list"></a>Öğretici: basit varlık ve tümcecik listesi olan adları Ayıkla

Bu öğreticide, **Basit** varlığını kullanarak bir ifadeden iş adının makine öğrenmesi verilerini ayıklayın. Ayıklama doğruluğunu artırmak için, basit varlığa özgü terimlerin tümcecik listesini ekleyin.

Basit varlık, sözcükler veya tümceciklerde bulunan tek bir veri kavramını algılar.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Örnek uygulamayı içeri aktar
> * Basit varlık Ekle 
> * Sinyal sözcüklerini artırmak için tümcecik listesi ekleme
> * Eğitim 
> * Yayımlama 
> * Uç noktasındaki amaçları ve varlıkları alma

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]


## <a name="simple-entity"></a>Basit varlık

Bu öğretici, iş adını ayıklamak için yeni bir basit varlık ekler. Bu LUIS uygulamasındaki basit varlığın amacı, LUIS uygulamasına iş adının ne olduğunu ve konuşmanın hangi bölümünde bulunabileceğini öğretmektir. İfadenin iş adı olan bölümü, sözcük seçimine ve ifade uzunluğuna göre ifadeden ifadeye değişiklik gösterebilen iletidir. LUIS için, iş adlarını kullanan tüm amaçlar genelinde iş adlarının örnekleri gerekir.  

Aşağıdaki durumlarda bu veri tipi için basit varlık idealdir:

* Veri, tek bir kavram olduğunda.
* Veri, normal ifade gibi düzün biçimlendirilmediğinde.
* Veri, önceden derlenmiş telefon numarası veya veri varlığı gibi genel olmadığında.
* Veri, liste varlığı gibi bilinen sözcükler listesiyle tam olarak eşleşmediğinde.
* Veriler, bileşik varlık veya bağlamsal roller gibi diğer veri öğelerini içermez.

Bir sohbet botundan alınmış olan aşağıdaki ifadelere göz atın:

|İfade|Ayıklanabilir iş adı|
|:--|:--|
|I want to apply for the new accounting job. (Yeni muhasebe işine başvurmak istiyorum.)|accounting (muhasebe)|
|Submit my resume for the engineering position. (Özgeçmişimi mühendislik pozisyonu için değerlendirmek üzere gönderin.)|engineering (mühendislik)|
|Fill out application for job 123456 (123456 numaralı iş için başvuru yapın)|123456|

İş adı isim, fiil veya birden fazla kelimeden oluşan bir tümcecik olabileceğinden belirlemek zordur. Örneğin:

|İş|
|--|
|engineer (mühendis)|
|software engineer (yazılım mühendisi)|
|senior software engineer (kıdemli yazılım mühendisi)|
|engineering team lead (mühendislik ekibi lideri) |
|air traffic controller (hava trafik kontrolörü)|
|motor vehicle operator (motorlu araç operatörü)|
|ambulance driver (ambulans şoförü)|
|tender (ihale sorumlusu)|
|extruder (ekstrüder)|
|millwright (değirmenci)|

Bu LUIS uygulamasında birden fazla amaçta iş adları bulunmaktadır. LUIS, bu sözcükleri tüm amaçların ifadelerinde etiketleyerek iş adının ne olduğu ve ifadelerin hangi bölümünde yer aldığı konusunda daha fazla bilgi edinir.

Varlıklar, örnek ifadelerde işaretlendikten sonra, basit varlığın sinyalini güçlendirmek için tümcecik listesi eklenmesi önemlidir. Tümcecik listesi, tam eşleşme olarak **kullanılmaz** ve beklediğiniz her olası değer olması gerekmez. 

## <a name="import-example-app"></a>Örnek uygulamayı içeri aktar

1.  [Uygulama json dosyasını](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/build-app/intentonly.json) amaçlar öğreticiden indirip kaydedin.

2. JSON'ı yeni bir uygulamaya içeri aktarın.

3. **Yönet** bölümünde **Sürümler** sekmesinde sürümü kopyalayın ve `simple` olarak adlandırın. Kopyalama, özgün sürümünüzü etkilemeden farklı LUIS özelliklerini deneyebileceğiniz ideal bir yol sunar. Sürüm adı URL rotasının bir parçası olarak kullanıldığından ad bir URL'de geçerli olmayan hiçbir karakter içeremez.

## <a name="mark-entities-in-example-utterances-of-an-intent"></a>Varlıkları örnek bir amaç halinde işaretleyin

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. **Intents** (Amaçlar) sayfasında **ApplyForJob** amacını seçin. 

1. `I want to apply for the new accounting job` konuşmasında `accounting` öğesini seçin, açılır menünün en üst kısmına `Job` yazın ve ardından açılır menüden **Create new entity** (Yeni varlık oluştur) girişini seçin. 

    [![Varlık oluşturma adımları vurgulanmış şekilde ' ApplyForJob ' amacına sahip LUSıS ekran görüntüsü](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png "Varlık oluşturma adımları vurgulanmış şekilde ' ApplyForJob ' amacına sahip LUSıS ekran görüntüsü")](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png#lightbox)

1. Açılır pencerede varlığın adını ve türünü doğrulayıp **Done** (Bitti) öğesini seçin.

    ![İş adı ve basit türü görünen basit varlık oluşturma açılır iletişim kutusu](media/luis-quickstart-primary-and-secondary-data/hr-create-simple-entity-popup.png)

1. Kalan söylerde, **iş varlığıyla işle** ilgili kelimeleri, sözcüğü veya tümceciği seçip açılır menüden **iş** öğesini seçerek işaretleyin. 

    [![LUSıS etiketleme iş varlığının vurgulandığı ekran görüntüsü](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png "LUSıS etiketleme iş varlığının vurgulandığı ekran görüntüsü")](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png#lightbox)


## <a name="add-more-example-utterances-and-mark-entity"></a>Daha fazla örnek ekleme ve işaret varlığı ekleme

Basit varlıkların, yüksek tahmine sahip olması için çok sayıda örnek gerekir. 
 
1. Daha fazla konuşma ekleyin ve iş sözcüklerini veya tümceciklerini **Job** (İş) varlığı olarak etiketleyin. 

    |İfade|İş varlığı|
    |:--|:--|
    |I'm applying for the Program Manager desk in R&D (Ar-Ge bölümündeki Program Yöneticisi pozisyonu için başvuru yapıyorum)|Program Manager (Program Yöneticisi)|
    |Here is my line cook application. (Şef başvurumu gönderiyorum.)|şef|
    |My resume for camp counselor is attached. (Kamp Danışmanı özgeçmişim ektedir.)|camp counselor (kamp danışmanı)|
    |This is my c.v. for administrative assistant. (Yönetici asistanı özgeçmişim budur.)|administrative assistant (yönetici asistanı)|
    |I want to apply for the management job in sales. (Satış departmanındaki yönetim işi için başvuru yapmak istiyorum.)|management, sales (yönetim, satış)|
    |This is my resume for the new accounting position. (Yeni muhasebe pozisyonu için özgeçmişimi ilginize sunarım.)|accounting (muhasebe)|
    |My application for barback is included. (Barmenlik başvurum ektedir.)|barback (barmenlik)|
    |I'm submitting my application for roofer and framer. (Çatı ve doğrama işleri için başvurumu gönderiyorum.)|roofer, framer (çatı, doğrama)|
    |My c.v. for bus driver is here. (Otobüs şoförü başvurumu iletiyorum.)|bus driver (otobüs şoförü)|
    |I'm a registered nurse. (Sertifikalı bir hemşireyim.) Here is my resume. (Özgeçmişim burada.)|registered nurse (sertifikalı hemşire)|
    |I would like to submit my paperwork for the teaching position I saw in the paper. (Gazetede gördüğüm öğretmenlik pozisyonu için gerekli belgelerimi göndermek istiyorum.)|teaching (öğretmenlik)|
    |This is my c.v. for the stocker post in fruits and vegetables. (Meyve ve sebze stok görevlisi ilanı için özgeçmişimi iletiyorum.)|stocker (stok görevlisi)|
    |Apply for tile work. (Döşeme işleri için başvuruyorum.)|tile (döşeme)|
    |Attached resume for landscape architect. (Peyzaj mimarı ilanı için özgeçmişimi ekte gönderiyorum.)|landscape architect (peyzaj mimarı)|
    |My curriculum vitae for professor of biology is enclosed. (Biyoloji öğretmenliği için özgeçmişimi ekte bulabilirsiniz.)|professor of biology (biyoloji öğretmenliği)|
    |I would like to apply for the position in photography. (Fotoğrafçılık alanındaki pozisyon için başvuruda bulunmak istiyorum.)|photography (fotoğrafçılık)|

## <a name="mark-job-entity-in-other-intents"></a>İş varlığını diğer amaçlar halinde işaretle

1. Sol menüden **Intents** (Amaçlar) öğesini seçin.

1. Amaç listesinden **GetJobInformation** girişini seçin. 

1. Örnek söyleylerdeki işleri etiketleme

    Başka bir amaca göre bir amaç için daha fazla örnek elde edildiğinde, bu amaç en yüksek tahmini amaç olma olasılığının daha yüksektir. 

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Uygulamaya yapılan değişikliklerin test edilebilir olması için uygulamayı eğitme 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Eğitilen modelin uç noktadan sorgulanabilir olması için uygulamayı yayımlayın

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Uç noktadan amacı ve varlık tahminini alın 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Adres çubuğundaki URL'nin sonuna gidip `Here is my c.v. for the engineering job` yazın. Son sorgu dizesi parametresi konuşma `q`s**orgusu olan**  öğesidir. Bu konuşma, etiketlenmiş olan konuşmalarla aynı olmadığından iyi bir testtir ve `ApplyForJob` konuşmaları döndürmelidir.

    ```json
    {
      "query": "Here is my c.v. for the engineering job",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.98052007
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.98052007
        },
        {
          "intent": "GetJobInformation",
          "score": 0.03424581
        },
        {
          "intent": "None",
          "score": 0.0015820954
        }
      ],
      "entities": [
        {
          "entity": "engineering",
          "type": "Job",
          "startIndex": 24,
          "endIndex": 34,
          "score": 0.668959737
        }
      ]
    }
    ```
    
    LUIS, doğru amacı (**ApplyForJob**) buldu ve **değeriyle doğru**İş`engineering` varlığını ayıkladı.


## <a name="names-are-tricky"></a>Adlar kafa karıştırıcı olabilir
LUIS uygulaması yüksek güven derecesiyle doğru amacı buldu ve iş adını ayıkladı ancak adlar kafa karıştırıcı olabilir. `This is the lead welder paperwork` konuşmasını deneyin.  

Aşağıdaki JSON kodunda LUIS doğru amaç olan `ApplyForJob` yanıt vermektedir ancak `lead welder` iş adını ayıklamamıştır. 

```json
{
  "query": "This is the lead welder paperwork",
  "topScoringIntent": {
    "intent": "ApplyForJob",
    "score": 0.860295951
  },
  "intents": [
    {
      "intent": "ApplyForJob",
      "score": 0.860295951
    },
    {
      "intent": "GetJobInformation",
      "score": 0.07265678
    },
    {
      "intent": "None",
      "score": 0.00482481951
    }
  ],
  "entities": []
}
```

Ad herhangi bir şey olabileceğinden LUIS, sinyali güçlendirecek bir tümcecik listesi olması halinde varlıkları daha doğru bir şekilde tahmin edebilir.

## <a name="to-boost-signal-of-the-job-related-words-add-a-phrase-list-of-job-related-words"></a>İşle ilgili sözcüklerin sinyalini artırmak için işle ilgili sözcüklerin bir tümcecik listesini ekleyin

Azure-Samples GitHub deposundan [Jobs-phrase-List. csv](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/job-phrase-list.csv) ' i açın. Listede 1.000 iş kelimeleri ve tümceleri bulunur. Listede size anlamlı gelen iş sözcüklerini bulun. İstediğiniz sözcükler ve tümcecikler listede değilse ekleyin.

1. LUIS uygulamasının **Build** (Derleme) bölümünde **Improve app performance** (Uygulama performansını geliştir) kısmındaki **Phrase lists** (Tümcecik listeleri) girişini seçin.

1. **Create new phrase list** (Yeni tümcecik listesi oluştur) öğesini seçin. 

1. Yeni tümcecik listesine `JobNames` adını verin ve jobs-phrase-list.csv dosyasındaki listeyi kopyalayıp **Values** (Değerler) metin kutusuna yapıştırın.

    [![Yeni tümcecik listesi oluştur iletişim kutusu açılır penceresi](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png "Yeni tümcecik listesi oluştur iletişim kutusu açılır penceresi")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png#lightbox)

    Tümcecik listesine daha fazla sözcük eklemek istiyorsanız, **yeniden komut** ' yi seçin ve ardından yeni **ilgili değerleri** gözden geçirin ve ilgili olanları ekleyin. 

    Bu değerlerin tümü işler için eş anlamlı olarak değerlendirildiğinden, **Bu değerlerin değiştirilebilir** olduğundan emin olun. Değiştirilebilir ve değiştirilemez [ifade listesi kavramları](luis-concept-feature.md#how-to-use-phrase-lists)hakkında daha fazla bilgi edinin.

1. Tümcecik listesini etkinleştirmek için **bitti** ' yi seçin.

    [![Tümcecik liste değerleri kutusunda kelimeyle yeni tümcecik listesi oluştur iletişim kutusu açılır ekran görüntüsü](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png "Tümcecik liste değerleri kutusunda kelimeyle yeni tümcecik listesi oluştur iletişim kutusu açılır ekran görüntüsü")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png#lightbox)

1. Tümcecik listesini kullanmak için uygulamayı eğitin ve yayımlayın.

1. Uç noktayı aynı konuşmayla yeniden sorgulayın: `This is the lead welder paperwork.`

    JSON yanıtı ayıklanan varlığı içerir:

    ```json
      {
      "query": "This is the lead welder paperwork.",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.983076453
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.983076453
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0120766377
        },
        {
          "intent": "None",
          "score": 0.00248388131
        }
      ],
      "entities": [
        {
          "entity": "lead welder",
          "type": "Job",
          "startIndex": 12,
          "endIndex": 22,
          "score": 0.8373154
        }
      ]
    }
    ```

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>İlgili bilgiler

* [Varlıkları olmayan amaçlar öğreticisi](luis-quickstart-intents-only.md)
* [Basit varlık](luis-concept-entity-types.md) kavramsal bilgileri
* [İfade listesi](luis-concept-feature.md) kavramsal bilgileri
* [Eğitme](luis-how-to-train.md)
* [Yayımlama nasıl yapılır?](luis-how-to-publish-app.md)
* [LUSıS portalında test etme](luis-interactive-test.md)


## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide İnsan Kaynakları uygulaması, ifadelerdeki iş adlarını bulmak için makine öğrenmesi basit varlığı kullanır. İş adları çok çeşitli sözcükler veya tümcecikler olabileceğinden, uygulamaya iş adı sözcüklerini artırmak için bir tümcecik listesi gerekti. 

> [!div class="nextstepaction"]
> [Önceden oluşturulmuş keyphrase varlığı ekleme](luis-quickstart-intent-and-key-phrase.md)
