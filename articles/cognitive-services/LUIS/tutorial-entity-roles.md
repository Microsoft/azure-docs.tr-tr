---
title: 'Öğretici: rollerle bağlama verisi-LUSıS'
titleSuffix: Azure Cognitive Services
description: Bağlama göre ilgili verileri bulun. Örneğin, bir bina ya da ofisten başka bir bina ya da ofise fiziksel olarak taşınmada çıkış ve varış konumları.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 3e85067439fe412822ac34a065753e9a13c7a506
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488736"
---
# <a name="tutorial-extract-contextually-related-data-from-an-utterance"></a>Öğretici: bağlamsal olarak ilgili verileri bir söylenişi 'ten ayıklayın

Bu öğreticide bağlama göre ilgili veri parçalarını bulacaksınız. Örneğin, bir şehirden diğerine aktarım için kaynak ve hedef konumlar. Her iki veri parçası de gerekli olabilir ve birbirleriyle ilişkilidir.  

Bir rol, önceden oluşturulmuş veya özel varlık türü ile kullanılabilir ve her iki örnek de ve desenlerinde kullanılabilir. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:**

> [!div class="checklist"]
> * Yeni uygulama oluşturma
> * Amaç ekleme 
> * Rolleri kullanarak kaynak ve hedef bilgilerini al
> * Eğitim
> * Yayımlama
> * Uç noktadan amaçları ve varlık rollerini al

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="related-data"></a>İlgili veriler

Bu uygulama, bir çalışanın kaynak şehirden hedef şehre nereye taşınacağını belirler. Şehir adlarını belirlemek için GeographyV2 önceden oluşturulmuş bir varlık kullanır ve konum türlerini (Origin ve Destination) söylik içinde belirlemek için rolleri kullanır.

Ayıklanacak varlık verileri için bir rol kullanılmalıdır:

* , Utterance bağlamında birbirleriyle ilişkilidir.
* Her bir rolü göstermek için belirli bir sözcük seçimi kullanır. Bu sözcüklere örnekler şunlardır: from/to, leaving/headed to, away from/toward (çıkış/varış, ayrılıyor/gidiyor, kaynaktan/hedefe doğru)
* Her iki rol de genellikle aynı şekilde, LUSıS 'in bu sık kullanılan bağlamsal kullanımdan öğrenmesine izin verir.
* İstemci uygulama tarafından bir bilgi birimi olarak gruplanmaları ve işlenmeleri gerekir.

## <a name="create-a-new-app"></a>Yeni bir uygulama oluşturma

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-an-intent-to-move-employees-between-cities"></a>Çalışanları şehir arasında taşımak için bir amaç oluşturun

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. **Create new intent** (Yeni amaç oluştur) öğesini seçin. 

1. Açılan iletişim kutusuna `MoveEmployeeToCity` girip **Done** (Bitti) öğesini seçin. 

    ![Create new intent (Yeni amaç oluştur) iletişim kutusunun ekran görüntüsü](./media/tutorial-entity-roles/create-new-intent-move-employee-to-city.png)

1. Amaca örnek konuşmalar ekleyin.

    |Örnek konuşmalar|
    |--|
    |John W. Smith 'i Seattle uçlu, Orlando 'ya taşı|
    |Jill Jones Seattle 'dan Cairo 'ya aktar|
    |John Jackson yerine, Atlanta 'e yaklaşan |
    |KAG Chicago 'ten Tulsa 'ya taşı|
    |MV Jill Jones, Cairo uçlu|
    |Ali Anderson 'dan Oakland 'e kaydır|
    |San Francisco 'dan Redmond 'a Carl Chamerlin|
    |San Diego 'dan Bellevue 'e doğru Steve Stançe aktarma |
    |SSAS şehrinde Tanner Thompson 'yı kaldırın ve Chicago 'ye SHIFT 'e gidin|

    [MOVEIN 'ın, MoveEmployee amacına yönelik yeni dıklarla birlikte ![ekran görüntüsü](./media/tutorial-entity-roles/hr-enter-utterances.png)](./media/tutorial-entity-roles/hr-enter-utterances.png#lightbox)

## <a name="add-prebuilt-entity-geographyv2"></a>Önceden oluşturulmuş varlık geographyV2 Ekle

Önceden oluşturulmuş varlık olan geographyV2, şehir adları dahil konum bilgilerini ayıklar. Söyleyenlerde iki şehir adı bulunduğundan, bağlamdaki birbirleriyle ilgili olarak bu bağlamı ayıklamak için roller kullanın.

1. Sol taraftaki gezinmede **varlıklar** ' ı seçin.

1. **Önceden oluşturulmuş varlık Ekle**' yi seçin ve ardından önceden oluşturulmuş varlıkları filtrelemek için arama çubuğunda `geo` ' ı seçin. 

    ![Uygulamaya geographyV2 önceden oluşturulmuş varlık ekleme](media/tutorial-entity-roles/add-geographyV2-prebuilt-entity.png)
1. Onay kutusunu seçin ve **bitti**' yi seçin.
1. **Varlıklar** listesinde, yeni varlığı açmak için **geographyV2** seçin. 
1. İki rol ekleyin, `Origin`ve `Destination`. 

    ![Önceden oluşturulmuş varlığa roller ekleme](media/tutorial-entity-roles/add-roles-to-prebuilt-entity.png)
1. Sol taraftaki gezinmede **amaçları** seçin, sonra **Moveemployeetocity** hedefini seçin. Şehir adlarının **geographyV2**önceden oluşturulmuş varlıkla etiketlendirildiğine dikkat edin.
1. Listenin ilk bölümünde, kaynak konumunu seçin. Açılan menü görüntülenir. Listeden **geographyV2** öğesini seçin ve ardından **kaynak**Seç ' in yanındaki menüyü izleyin.
1. Tüm yerlerdeki konumların tüm rollerini işaretlemek için önceki adımdaki yöntemi kullanın. 


## <a name="add-example-utterances-to-the-none-intent"></a>Hiçbiri amacına örnek ekleme 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Uygulamaya yapılan değişikliklerin test edilebilir olması için uygulamayı eğitme 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Eğitilen modelin uç noktadan sorgulanabilir olması için uygulamayı yayımlayın

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Uç noktadan amacı ve varlık tahminini alın

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]


1. Adres çubuğundaki URL'nin sonuna gidip `Please move Carl Chamerlin from Tampa to Portland` yazın. Son sorgu dizesi parametresi konuşma `q`s**orgusu olan**  öğesidir. Bu söylenişi, etiketlendirmelerdeki herhangi biriyle aynı değildir ve bu nedenle iyi bir test olur ve ayıklanan varlıkla `MoveEmployee` hedefini döndürmelidir.

    ```json
    {
      "query": "Please move Carl Chamerlin from Tampa to Portland",
      "topScoringIntent": {
        "intent": "MoveEmployeeToCity",
        "score": 0.9706451
      },
      "intents": [
        {
          "intent": "MoveEmployeeToCity",
          "score": 0.9706451
        },
        {
          "intent": "None",
          "score": 0.0307451729
        }
      ],
      "entities": [
        {
          "entity": "tampa",
          "type": "builtin.geographyV2.city",
          "startIndex": 32,
          "endIndex": 36,
          "role": "Origin"
        },
        {
          "entity": "portland",
          "type": "builtin.geographyV2.city",
          "startIndex": 41,
          "endIndex": 48,
          "role": "Destination"
        }
      ]
    }
    ```
    
    Doğru amaç tahmin edilir ve varlıklar dizisinin karşılık gelen **varlıklar** özelliğinde hem kaynak hem de hedef rolleri vardır.
    
## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>İlgili bilgiler

* [Varlık kavramları](luis-concept-entity-types.md)
* [Rol kavramları](luis-concept-roles.md)
* [Önceden oluşturulmuş varlıklar listesi](luis-reference-prebuilt-entities.md)
* [Eğitme](luis-how-to-train.md)
* [Yayımlama nasıl yapılır?](luis-how-to-publish-app.md)
* [LUSıS portalında test etme](luis-interactive-test.md)
* [Roller](luis-concept-roles.md)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğretici, kaynak ve hedef konumların bağlamsal olarak öğrenilen verileri için yeni bir amaç ve ek bir örnek açıklaması oluşturdu. Uygulama eğitilip yayımlandıktan sonra bir istemci uygulama bu bilgileri ilgili bilgilerle bir taşınma bileti oluşturmak için kullanabilir.

> [!div class="nextstepaction"] 
> [Bileşik varlık eklemeyi öğrenin](luis-tutorial-composite-entity.md) 
