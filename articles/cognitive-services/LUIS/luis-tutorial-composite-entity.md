---
title: 'Öğretici: Bileşik varlık öğreticisi-LUSıS'
titleSuffix: Azure Cognitive Services
description: Çeşitli türlerin ayıklanan verilerini tek bir kapsayan varlığa dönüştürmek için bileşik bir varlık ekleyin. İstemci uygulaması, verileri paketleyerek ilgili verileri farklı veri türlerinde kolayca ayıklayabilir.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: adb8941fd60a955a44a04717958c5203b721639a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498989"
---
# <a name="tutorial-group-and-extract-related-data"></a>Öğretici: ilgili verileri gruplandırın ve ayıklayın
Bu öğreticide, çeşitli türlerin ayıklanan verilerini tek bir kapsayan varlığa dönüştürmek için bir bileşik varlık ekleyin. İstemci uygulaması, verileri paketleyerek ilgili verileri farklı veri türlerinde kolayca ayıklayabilir.

Bileşik varlığın amacı, ilgili varlıkların bir üst kategori varlığına gruplandırılmalarıdır. Bilgiler, bir bileşik oluşturulmadan önce ayrı varlıklar olarak mevcuttur. 

Bileşik varlık bu veri türü için uygun bir değer olduğundan veri:

* Birbirleriyle ilişkilidir. 
* Çeşitli varlık türlerini kullanın.
* İstemci uygulama tarafından bir bilgi birimi olarak gruplanmaları ve işlenmeleri gerekir.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Örnek uygulamayı içeri aktar
> * Amaç oluşturma
> * Bileşik varlık ekleme 
> * Eğitim
> * Yayımlama
> * Uç noktasındaki amaçları ve varlıkları alma

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Örnek uygulamayı içeri aktar

1.  [Uygulama json dosyasını](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/build-app/tutorial_list.json) liste varlık öğreticiden indirip kaydedin.

2. JSON'ı yeni bir uygulamaya içeri aktarın.

3. **Yönet** bölümünde **Sürümler** sekmesinde sürümü kopyalayın ve `composite` olarak adlandırın. Kopyalama, özgün sürümünüzü etkilemeden farklı LUIS özelliklerini deneyebileceğiniz ideal bir yol sunar. Sürüm adı URL rotasının bir parçası olarak kullanıldığından ad bir URL'de geçerli olmayan hiçbir karakter içeremez.

## <a name="composite-entity"></a>Bileşik varlık

Bu uygulamada, departman adı **Departman** listesi varlığında tanımlanmıştır ve eş anlamlılar içerir. 

**Transferemployeetodepartment** amacı, bir çalışanın yeni bir departmana taşınmasını istemek için örnek bir örnektir. 

Bu amaca yönelik örnek bildirimler şunlardır:

|Örnek konuşmalar|
|--|
|John W. Smith 'i muhasebe departmanına taşı|
|Jill Jones 'ten R & D 'ye aktar|
 
Taşıma isteği, Bölüm adı ve çalışan adı ' nı içermelidir. 

## <a name="add-the-personname-prebuilt-entity-to-help-with-common-data-type-extraction"></a>Ortak veri türü ayıklamasıyla ilgili yardım için PersonName önceden oluşturulmuş varlığını ekleyin

LUIS, ortak veri ayıklama işlemi için önceden oluşturulmuş birkaç varlık sunar. 

1. Üstteki gezinmede **Oluştur** ' u seçin ve ardından sol gezinti menüsünden **varlıklar** ' ı seçin.

1. **Önceden oluşturulmuş varlıkları yönet** düğmesini seçin.

1. Önceden oluşturulmuş varlıklar listesinden **[PersonName](luis-reference-prebuilt-person.md)** ' i seçin ve **bitti**' yi seçin.

    ![Önceden oluşturulmuş varlıklar iletişim kutusunda sayının seçildiğini gösteren ekran görüntüsü](./media/luis-tutorial-composite-entity/add-personname-prebuilt-entity.png)

    Bu varlık, istemci uygulamanıza ad tanıma eklemenize yardımcı olur.

## <a name="create-composite-entity-from-example-utterances"></a>Örnek utbotlardan Bileşik varlık oluşturma

1. Sol gezinti bölmesinden **Intents** (Amaçlar) öğesini seçin.

1. Amaçlar listesinden **Transferemployeetodepartment** öğesini seçin.

1. Söylenişi `place John Jackson in engineering`, kişi adı varlığını seçin, `John Jackson`ve ardından açılan menü listesinden aşağıdaki söylik için **bileşik varlıkta Wrap** ' ı seçin. 

    ![Açılan kutudan kaydırmayı kaydır iletişim kutusunun ekran görüntüsü](./media/luis-tutorial-composite-entity/hr-create-composite-entity-1.png)

1. Ardından, en son varlığı hemen seçin, `engineering`. Bileşik bir varlık gösteren seçili sözcüklerin altına yeşil bir çubuk çizilir. Açılır menüde, bileşik adı `TransferEmployeeInfo` girin ve ardından ENTER ' u seçin. 

    ![Açılan liste iletişim kutusunda bileşik ad girme ekran görüntüsü](./media/luis-tutorial-composite-entity/hr-create-composite-entity-2.png)

1. **Ne tür bir varlık oluşturmak istiyorsunuz?** , gereken tüm alanlar listede: `personName` ve `Department`. **Done** (Bitti) öğesini seçin. Önceden oluşturulmuş varlığın, personName, bileşik varlığa eklendiğini unutmayın. Bileşik bir varlığın başlangıç ve bitiş belirteçleri arasında önceden oluşturulmuş bir varlığınız varsa, bileşik varlık bu önceden oluşturulmuş varlıkları içermelidir. Önceden oluşturulmuş varlıklar dahil edilmezlerse, bileşik varlık doğru şekilde tahmin edilmez, ancak her ayrı öğe.

    ![Açılan liste iletişim kutusunda bileşik ad girme ekran görüntüsü](./media/luis-tutorial-composite-entity/hr-create-composite-entity-3.png)

## <a name="label-example-utterances-with-composite-entity"></a>Bileşik varlık ile etiket örneği

1. Her örnekte, bileşik içinde olması gereken en soldaki varlığı seçin. Ardından **bileşik varlıkta Wrap '** ı seçin.

1. Bileşik varlıktaki son sözcüğü seçin ve ardından açılan menüden **Transferemployeeınfo** öğesini seçin. 

1. Amaç içindeki tüm söyleyeni, bileşik varlıkla etiketlenmiş olduğunu doğrulayın. 

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Uygulamaya yapılan değişikliklerin test edilebilir olması için uygulamayı eğitme 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Eğitilen modelin uç noktadan sorgulanabilir olması için uygulamayı yayımlayın

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Uç noktadan amacı ve varlık tahminini alın 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Adres çubuğundaki URL'nin sonuna gidip `Move Jill Jones to DevOps` yazın. Son QueryString parametresi, söylenişi sorgusu `q`. 

    Bu test, Birleşik öğenin doğru bir şekilde ayıklandığını doğrulamak olduğundan, bir test var olan bir örnek, ya da yeni bir söylenişi içerebilir. Birleşik varlıktaki tüm alt varlıkları eklemek iyi bir test.

    ```json
    {
      "query": "Move Jill Jones to DevOps",
      "topScoringIntent": {
        "intent": "TransferEmployeeToDepartment",
        "score": 0.9882747
      },
      "intents": [
        {
          "intent": "TransferEmployeeToDepartment",
          "score": 0.9882747
        },
        {
          "intent": "None",
          "score": 0.00925369747
        }
      ],
      "entities": [
        {
          "entity": "jill jones",
          "type": "builtin.personName",
          "startIndex": 5,
          "endIndex": 14
        },
        {
          "entity": "devops",
          "type": "Department",
          "startIndex": 19,
          "endIndex": 24,
          "resolution": {
            "values": [
              "Development Operations"
            ]
          }
        },
        {
          "entity": "jill jones to devops",
          "type": "TransferEmployeeInfo",
          "startIndex": 5,
          "endIndex": 24,
          "score": 0.9607566
        }
      ],
      "compositeEntities": [
        {
          "parentType": "TransferEmployeeInfo",
          "value": "jill jones to devops",
          "children": [
            {
              "type": "builtin.personName",
              "value": "jill jones"
            },
            {
              "type": "Department",
              "value": "devops"
            }
          ]
        }
      ]
    }
    ```

   Bu söylenişi bir bileşik varlıklar dizisi döndürüyor. Her varlığa bir tür ve değer verilir. Her bir alt varlığa ilişkin daha fazla duyarlık bulmak için, varlıklar dizisinde karşılık gelen öğeyi bulmak için bileşik dizi öğesinden tür ve değer birleşimini kullanın.  

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>İlgili bilgiler

* [Varlık öğreticisini listeleme](luis-quickstart-intents-only.md)
* [Bileşik varlık](luis-concept-entity-types.md) kavramsal bilgileri
* [Eğitme](luis-how-to-train.md)
* [Yayımlama nasıl yapılır?](luis-how-to-publish-app.md)
* [LUSıS portalında test etme](luis-interactive-test.md)


## <a name="next-steps"></a>Sonraki adımlar

Bu öğretici, mevcut varlıkları kapsüllemek için bir bileşik varlık oluşturdu. Bu, istemci uygulamanın konuşmaya devam etmek için farklı veri türlerine ilişkin bir grup veri bulmasına olanak tanır. Bu Insan kaynakları uygulaması için bir istemci uygulaması, taşımanın başlaması gereken gün ve saati sorabilir. Ayrıca, fiziksel telefon gibi diğer bir taşıma için başka bir lojisinden da sorabilir. 

> [!div class="nextstepaction"] 
> [Tümcecik listesi ile basit bir varlık eklemeyi öğrenin](luis-quickstart-primary-and-secondary-data.md)  
