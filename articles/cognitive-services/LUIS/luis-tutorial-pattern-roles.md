---
title: 'Öğretici: model rolleri-LUSıS'
titleSuffix: Azure Cognitive Services
description: Desenler, iyi biçimlendirilmiş şablon dıklarından verileri ayıklar. Konuşma şablonu basit bir varlığın yanı sıra kaynak konum ve hedef konum gibi ilgili verileri ayıklamak için roller kullanır.
ms.custom: seodec18
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 13a1589a6cc6ed48f159f361ff69a5a3ba8f0f80
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499443"
---
# <a name="tutorial-extract-contextually-related-patterns-using-roles"></a>Öğretici: rolleri kullanarak bağlamsal olarak ile ilgili desenleri Ayıkla

Bu öğreticide iyi biçimlendirilmiş konuşma şablonundan veri ayıklamak için desen kullanacaksınız. Şablon, kaynak konumu ve hedef konum gibi ilgili verileri ayıklamak için [basit bir varlık](luis-concept-entity-types.md#simple-entity) ve [Roller](luis-concept-roles.md) kullanır.  Desen kullandığınızda amaç için daha az sayıda örnek konuşmaya ihtiyacınız vardır.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:**

> [!div class="checklist"]
> * Örnek uygulamayı içeri aktar
> * Yeni varlıklar oluşturma
> * Yeni amaç oluşturma
> * Eğitim
> * Yayımlama
> * Uç noktasındaki amaçları ve varlıkları alma
> * Role sahip desen oluşturma
> * Şehirleri içeren tümcecik listesi oluşturma
> * Uç noktasındaki amaçları ve varlıkları alma

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="using-roles-in-patterns"></a>Düzenleri desenlerde kullanma

Rollerin amacı, bağlamsal olarak ilişkili varlıkları bir utterance içinde ayıklamaktan oluşur. `Move new employee Robert Williams from Sacramento and San Francisco` konuşmasının içindeki kaynak şehir ve hedef şehir değerleri birbirleriyle ilişkilidir ve her konumun belirtilmesi için yaygın bir dil kullanılmaktadır. 


Yeni çalışanın adı Billy Patterson henüz **Employee** liste varlığının bir bölümü değildir. Yeni çalışan adı şirket kimlik bilgilerini oluşturmak üzere dışarıdaki bir sisteme gönderilmesi için ilk olarak ayıklanır. Şirket kimlik bilgileri oluşturulduktan sonra çalışan kimlik bilgileri **Employee** liste varlığına eklenir.

Yeni çalışanın ve ailesinin bulundukları şehirden hayali bir şirketin bulunduğu başka bir şehre taşınmaları gerekmektedir. Yeni bir çalışan herhangi bir şehirde bulunabileceği için konumların bulunması gerekir. Yalnızca listedeki şehirlerin ayıklanmasına neden olacağından liste varlığı gibi sabit bir liste işe yaramayacaktır.

Kaynak ve hedef şehirlerle ilişkilendirilmiş rol adlarının tüm varlıklarda benzersiz olması gerekir. Rollerin benzersiz olduğundan emin olmanın kolay yollarından biri, adlandırma stratejisi ile içeren varlığa bağlamaktır. **NewEmployeeRelocation** varlığı iki role sahip olan basit bir varlıktır: **NewEmployeeReloOrigin** ve **NewEmployeeReloDestination**. Relo, "relocation" (konum değiştirme) teriminin kısaltmasıdır.

`Move new employee Robert Williams from Sacramento and San Francisco` örnek konuşmasında yalnızca makine öğrenimi varlıkları bulunduğundan varlıkların tespit edilmesi için amaca yeterli sayıda örnek konuşmanın sağlanması önemlidir.  

**Desenler daha az örnek konuşma sağlamanızı mümkün kılsa da varlıkların algılanmaması durumunda desen eşleşmez.**

Ad veya şehir olması nedeniyle basit varlık algılama konusunda zorluk yaşıyorsanız benzer değerlerin bulunduğu bir tümcecik listesi eklemeyi deneyebilirsiniz. Bu liste LUIS'e kelime veya tümcecik türü hakkında ek bilgi vererek şehir adının algılanmasına yardımcı olur. Tümcecik listeleri yalnızca desenin eşleşmesi için gerekli varlık algılama konusunda desene yardımcı olur. 

## <a name="import-example-app"></a>Örnek uygulamayı içeri aktar
Son öğreticide oluşturulan **HumanResources** adlı uygulamayla devam edin. 

Aşağıdaki adımları kullanın:

1.  [Uygulama JSON dosyasını](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-patterns-HumanResources-v2.json) indirip kaydedin.

2. JSON'ı yeni bir uygulamaya içeri aktarın.

3. **Yönet** bölümünde **Sürümler** sekmesinde sürümü kopyalayın ve `roles` olarak adlandırın. Kopyalama, özgün sürümünüzü etkilemeden farklı LUIS özelliklerini deneyebileceğiniz ideal bir yol sunar. Sürüm adı URL rotasının bir parçası olarak kullanıldığından ad bir URL'de geçerli olmayan hiçbir karakter içeremez.

## <a name="create-new-entities"></a>Yeni varlıklar oluşturma

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Sol gezinti panelinden **Entities** (Varlıklar) öğesini seçin. 

3. **Create new entity** (Yeni varlık oluştur) öğesini seçin.

4. Açılan pencereye `NewEmployee`Simple **(Basit) varlık olarak** girin.

5. **Create new entity** (Yeni varlık oluştur) öğesini seçin.

6. Açılan pencereye `NewEmployeeRelocation`Simple **(Basit) varlık olarak** girin.

7. Varlık listesinden **NewEmployeeRelocation** öğesini seçin. 

8. İlk rol olarak `NewEmployeeReloOrigin` girin ve Enter tuşuna basın.

9. İkinci rol olarak `NewEmployeeReloDestination` girin ve Enter tuşuna basın.

## <a name="create-new-intent"></a>Yeni amaç oluşturma
Bu bölümdeki adımlara başlamadan önceden oluşturulmuş keyPhrase varlığını kaldırıp adımları tamamladıktan sonra geri eklemeniz bu adımdaki varlıkların etiketlenmesini kolaylaştırır. 

1. Sol gezinti bölmesinden **Intents** (Amaçlar) öğesini seçin.

2. **Create new intent** (Yeni amaç oluştur) öğesini seçin. 

3. Açılan iletişim kutusunda varlık adı olarak `NewEmployeeRelocationProcess` girin.

4. Aşağıdaki örnek konuşmaları girin ve yeni varlıkları etiketleyin. Varlık ve rol değerleri kalın olarak biçimlendirilmiştir. Metin etiketleme konusunda kolaylık sunduğunu düşünüyorsanız **Tokens View** (Belirteç) görünümünü kullanın. 

    Varlığın rolü amacı etiketleme sırasında belirtilmez. Bu işlem desen oluşturma sırasında gerçekleştirilir. 

    |İfade|NewEmployee|NewEmployeeRelocation|
    |--|--|--|
    |Move **Bob Jones** from **Seattle** to **Los Colinas** (Bob Jones'u Seattle'dan Los Colinas'a taşı)|Bob Jones|Seattle, Los Colinas|
    |Move **Dave C. Cooper** from **Redmond** to **New York City**|Dave C. Cooper|Redmond, New York City|
    |Move **Jim Paul Smith** from **Toronto** to **West Vancouver**|Jim Paul Smith|Toronto, West Vancouver|
    |Move **J. Benson** from **Boston** to **Staines-upon-Thames**|J. Benson|Boston, Staines-upon-Thames|
    |Move **Travis "Trav" Hinton** from **Castelo Branco** to **Orlando**|Travis "Trav" Hinton|Castelo Branco, Orlando|
    |Move **Trevor Nottington III** from **Aranda de Duero** to **Boise**|Trevor Nottington III|Aranda de Duero, Boise|
    |**Dr. Greg Williams** 'yi **Orlando** 'dan **Ellicott City** 'e taşıyın|Dr. Greg Williams|Orlando, Ellicott City|
    |Move **Robert "Bobby" Gregson** from **Kansas City** to **San Juan Capistrano**|Robert "Bobby" Gregson|Kansas City, San Juan Capistrano|
    |Move **Patti Owens** from **Bellevue** to **Rockford**|Patti Owens|Bellevue, Rockford|
    |Move **Janet Bartlet** from **Tuscan** to **Santa Fe**|Janet Bartlet|Tuscan, Santa Fe|

    Çalışan adının ön ek, kelime sayısı, söz dizimi ve son ek kullanımı farklıdır. Bu durum LUIS'in yeni çalışan adındaki değişiklikleri anlaması açısından önemlidir. Şehir adlarının da kelime sayısı ve söz dizimleri farklıdır. Bu farklılık LUIS'e bu varlıkların kullanıcının konuşmasında nasıl görünebileceğini gösterme açısından önemlidir. 
    
    Varlıklardan biri aynı kelime sayısına sahip ve başka bir farklılığa sahip değilse LUIS'e bu varlığın yalnızca bu kelime sayısına sahip olduğunu ve başka bir farklılığa sahip olmadığını öğretmiş olursunuz. LUIS gösterilmediği için daha geniş farklılıkları doğru şekilde tahmin edemeyebilir. 

    keyPhrase varlığını kaldırdıysanız bu adımda uygulamaya geri ekleyin.

## <a name="train"></a>Eğitim

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Yayımlama

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Uç noktadan amacı ve varlıkları alma

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

2. Adres çubuğundaki URL'nin sonuna gidip `Move Wayne Berry from Miami to Mount Vernon` yazın. Son sorgu dizesi parametresi konuşma `q`s**orgusu olan**  öğesidir. 

    ```json
    {
      "query": "Move Wayne Berry from Newark to Columbus",
      "topScoringIntent": {
        "intent": "NewEmployeeRelocationProcess",
        "score": 0.514479756
      },
      "intents": [
        {
          "intent": "NewEmployeeRelocationProcess",
          "score": 0.514479756
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.017118983
        },
        {
          "intent": "MoveEmployee",
          "score": 0.009982505
        },
        {
          "intent": "GetJobInformation",
          "score": 0.008637771
        },
        {
          "intent": "ApplyForJob",
          "score": 0.007115978
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.006120186
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.00452428637
        },
        {
          "intent": "None",
          "score": 0.00400899537
        },
        {
          "intent": "OrgChart-Reports",
          "score": 0.00240071164
        },
        {
          "intent": "Utilities.Help",
          "score": 0.001770991
        },
        {
          "intent": "EmployeeFeedback",
          "score": 0.001697356
        },
        {
          "intent": "OrgChart-Manager",
          "score": 0.00168116146
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00163952739
        },
        {
          "intent": "FindForm",
          "score": 0.00112958835
        }
      ],
      "entities": [
        {
          "entity": "wayne berry",
          "type": "NewEmployee",
          "startIndex": 5,
          "endIndex": 15,
          "score": 0.629158735
        },
        {
          "entity": "newark",
          "type": "NewEmployeeRelocation",
          "startIndex": 22,
          "endIndex": 27,
          "score": 0.638941
        }
      ]
    }  
    ```

Amaç tahmini puanı yalnızca %50 seviyesindedir. İstemci uygulamanızda daha yüksek bir sayı gerekiyorsa bu durumun düzeltilmesi gerekir. Varlıklar da tahmin edilmemiştir.

Konumlardan biri ayıklanmış ancak diğer konum ayıklanmamıştır. 

Desenler tahmin puanına katkıda bulunur ancak desenin konuşmayla eşleştirilebilmesi için varlıkların doğru şekilde tahmin edilmesi gerekir. 

## <a name="pattern-with-roles"></a>Role sahip desen

1. Üst gezinti bölmesinde **Build** (Derle) öğesini seçin.

2. Sol gezinti bölmesinden **Patterns** (Desenler) öğesini seçin.

3. **Select an intent** (Amaç seçin) açılan listesinden **NewEmployeeRelocationProcess** öğesini seçin. 

4. Şu deseni girin: `move {NewEmployee} from {NewEmployeeRelocation:NewEmployeeReloOrigin} to {NewEmployeeRelocation:NewEmployeeReloDestination}[.]`

    Uç noktayı eğittikten, yayımladıktan ve sorguladıktan sonra varlıkların bulunmadığını gördüğünüzde hayal kırıklığına uğrayabilirsiniz. Bu da desenin eşleşmediğini ve tahminin geliştirilmediğini gösterir. Bu, yeterli sayıda etiketlenmiş varlığa sahip örnek konuşma olmamasından kaynaklanır. Bu sorunu gidermek için daha fazla örnek eklemek yerine tümcecik listesi ekleyebilirsiniz.

## <a name="cities-phrase-list"></a>Şehir tümcecik listesi
Kişi adları gibi şehir adları da farklı sözcükler ve noktalama işaretleri içerebildiğinden karmaşık olabilir. Dünyadaki şehirler ve bölgeler bilindiği için LUIS şehirlerden oluşan bir tümcecik listesini kullanarak öğrenmeye başlayabilir. 

1. Soldaki menünün **Improve app performance** (Uygulamanın performansını artırın) bölümünden **Phrase list** (Tümcecik listesi) girişini seçin. 

2. Listeye `Cities` adını verin ve aşağıdaki `values` öğelerini ekleyin:

    |Tümcecik listesinin değerleri|
    |--|
    |Seattle|
    |San Diego|
    |New York City|
    |Los Angeles|
    |Portland|
    |Philadephia|
    |Miami|
    |Dallas|

    Dünyadaki tüm şehirleri veya bölgedeki tüm şehirleri eklemeyin. LUIS'in listeyi kullanarak şehir kavramını genelleştirmesi gerekir. **These values are interchangeable** (Bu değerler birbirleriyle değiştirilebilir) seçeneğinin işaretini kaldırmayın. Bu ayar listedeki kelimelerin eş anlamlı olarak kabul edilmesini sağlar. 

3. Uygulamayı eğitin ve yayımlayın.

## <a name="get-intent-and-entities-from-endpoint"></a>Uç noktadan amacı ve varlıkları alma

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Adres çubuğundaki URL'nin sonuna gidip `Move wayne berry from miami to mount vernon` yazın. Son sorgu dizesi parametresi konuşma `q`s**orgusu olan**  öğesidir. 

    ```json
    {
      "query": "Move Wayne Berry from Miami to Mount Vernon",
      "topScoringIntent": {
        "intent": "NewEmployeeRelocationProcess",
        "score": 0.9999999
      },
      "intents": [
        {
          "intent": "NewEmployeeRelocationProcess",
          "score": 0.9999999
        },
        {
          "intent": "Utilities.Confirm",
          "score": 1.49678385E-06
        },
        {
          "intent": "MoveEmployee",
          "score": 8.240291E-07
        },
        {
          "intent": "GetJobInformation",
          "score": 6.3131273E-07
        },
        {
          "intent": "None",
          "score": 4.25E-09
        },
        {
          "intent": "OrgChart-Manager",
          "score": 2.8E-09
        },
        {
          "intent": "OrgChart-Reports",
          "score": 2.8E-09
        },
        {
          "intent": "EmployeeFeedback",
          "score": 1.64E-09
        },
        {
          "intent": "Utilities.StartOver",
          "score": 1.64E-09
        },
        {
          "intent": "Utilities.Help",
          "score": 1.48181822E-09
        },
        {
          "intent": "Utilities.Stop",
          "score": 1.48181822E-09
        },
        {
          "intent": "Utilities.Cancel",
          "score": 1.35E-09
        },
        {
          "intent": "FindForm",
          "score": 1.23846156E-09
        },
        {
          "intent": "ApplyForJob",
          "score": 5.692308E-10
        }
      ],
      "entities": [
        {
          "entity": "wayne berry",
          "type": "builtin.keyPhrase",
          "startIndex": 5,
          "endIndex": 15
        },
        {
          "entity": "miami",
          "type": "builtin.keyPhrase",
          "startIndex": 22,
          "endIndex": 26
        },
        {
          "entity": "wayne berry",
          "type": "NewEmployee",
          "startIndex": 5,
          "endIndex": 15,
          "score": 0.9410646,
          "role": ""
        },
        {
          "entity": "miami",
          "type": "NewEmployeeRelocation",
          "startIndex": 22,
          "endIndex": 26,
          "score": 0.9853915,
          "role": "NewEmployeeReloOrigin"
        },
        {
          "entity": "mount vernon",
          "type": "NewEmployeeRelocation",
          "startIndex": 31,
          "endIndex": 42,
          "score": 0.986044347,
          "role": "NewEmployeeReloDestination"
        }
      ],
      "sentimentAnalysis": {
        "label": "neutral",
        "score": 0.5
      }
   }
    ```

Amaç puanı artık çok daha yüksektir ve rol adları varlık yanıtının parçasıdır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide rollere sahip bir varlık ve örnek konuşmalar içeren bir amaç ekledik. Varlık kullanılarak gerçekleştirilen ilk uç nokta tahmini amacı doğru şekilde tahmin etti ancak güvenilirlik puanı düşüktü. İki varlıktan yalnızca biri algılandı. Öğreticide daha sonra varlık rollerini kullanan bir desen ve konuşmalardaki şehir adlarının değerini artırmak için bir tümcecik listesi eklendi. İkinci uç nokta tahmini daha yüksek bir güvenilirlik puanı döndürdü ve iki varlık rolünü de buldu. 

> [!div class="nextstepaction"]
> [LUIS uygulamaları için en iyi yöntemleri öğrenin](luis-concept-best-practices.md)
