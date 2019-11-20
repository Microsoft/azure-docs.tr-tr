---
title: 'Öğretici: tam metin eşleşmesi-LUSıS'
titleSuffix: Azure Cognitive Services
description: Bir listedeki önceden tanımlanmış öğelerle eşleşen verileri alma. Listedeki her öğenin tam olarak eşleşen eş anlamlıları da olabilir
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: a8021885bf51ab6d44bc8576b9fdd69f1bdd270a
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953679"
---
# <a name="tutorial-get-exact-text-matched-data-from-an-utterance"></a>Öğretici: tam metin ile eşleşen verileri bir utterde alın

Bu öğreticide, önceden tanımlanmış bir öğe listesiyle eşleşen varlık verilerinin nasıl alınacağını anlayın. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Uygulama oluştur
> * Amaç ekleme
> * Liste varlığı ekleme 
> * Eğitim 
> * Yayımlama
> * Uç noktasındaki amaçları ve varlıkları alma

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="what-is-a-list-entity"></a>Liste varlığı nedir?

Liste varlığı, söylenme içindeki sözcüklerle tam metin eşleşmelidir. 

Listeden her öğenin bir eşanlamlı sözcükler listesi olabilir. İnsan kaynakları uygulaması için, bir şirket departmanı resmi ad, ortak kısaltmalar ve fatura departmanı kodları gibi çeşitli önemli bilgiler ile tanımlanabilir. 

Insan kaynakları uygulamasının bir çalışanın aktarabilmesi için gereken departmanı belirlemesi gerekir. 

Liste varlığı bu veri türü için iyi bir seçimdir:

* Veri değerleri bilinen bir kümedir.
* Küme, bu varlık türü için maksimum LUIS [sınırlarını](luis-boundaries.md) aşmaz.
* Konuşmadaki metin bir eşanlamlı sözcük veya kurallı ad ile tam olarak eşleşiyor. LUSıS, tam metin eşleştirmelerinin ötesinde listeyi kullanmaz. Sözcük kökü, plurals ve diğer çeşitlemeler yalnızca bir liste varlığıyla çözümlenmez. Çeşitlemeleri yönetmek için, isteğe bağlı metin söz dizimine sahip bir [model](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance) kullanmayı düşünün. 

## <a name="create-a-new-app"></a>Yeni bir uygulama oluşturma

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-an-intent-to-transfer-employees-to-a-different-department"></a>Çalışanları farklı bir departmana aktarmaya yönelik bir amaç oluşturun

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. **Create new intent** (Yeni amaç oluştur) öğesini seçin. 

3. Açılan iletişim kutusuna `TransferEmployeeToDepartment` girip **Done** (Bitti) öğesini seçin. 

    ![Create new intent (Yeni amaç oluştur) iletişim kutusunun ekran görüntüsü](./media/luis-quickstart-intent-and-list-entity/hr-create-new-intent-ddl.png)

4. Amaca örnek konuşmalar ekleyin.

    |Örnek konuşmalar|
    |--|
    |John W. Smith 'i muhasebe departmanına taşı|
    |Jill Jones 'ten R & D 'ye aktar|
    |Bölüm 1234, Bill Bradstreet adlı yeni bir üyeye sahiptir|
    |John Jackson 'ı mühendisliğe yerleştir |
    |Deköşeli Doughtery 'yi satış Içinde taşı|
    |MV Jill Jones|
    |Gamze Anderson to DevOps 'a kaydır|
    |Carl Chamerlin finans|
    |Steve Stançe 1234|
    |Tanner Thompson, 3456|

    [![Örnek utbotları ile amaç ekran görüntüsü](media/luis-quickstart-intent-and-list-entity/intent-transfer-employee-to-department.png "Örnek utbotları ile amaç ekran görüntüsü")](media/luis-quickstart-intent-and-list-entity/intent-transfer-employee-to-department.png#lightbox)

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="department-list-entity"></a>Departman listesi varlığı

Şimdi **Transferemployeetodepartment** amacına örnek olarak sahip olduğuna göre, lusıs 'in departmanı ne olduğunu anlaması gerekir. 

Her öğenin birincil, _kurallı_adı departman adıdır. Her bir kurallı adın eş anlamlıları örnekleri şunlardır: 

|Kurallı ad|Eş anlamlılar|
|--|--|
|Muhasebe|acct<br>aci<br>3456|
|Geliştirme Işlemleri|DevOps<br>4949|
|Mühendislik|ing<br>altyapısındaki<br>4567|
|Finans|ın<br>2020|
|Bilgi teknolojisi|BT<br>2323|
|Satış iç|IaLe<br>ınsatılık<br>1414|
|Araştırma ve geliştirme|R & D<br>1234|

1. Sol panelde **Entities** (Varlıklar) öğesini seçin.

1. **Create new entity** (Yeni varlık oluştur) öğesini seçin.

1. Açılan varlık iletişim kutusunda varlık adı olarak `Department`, varlık türü olarak da **List** (Liste) seçin. **Done** (Bitti) öğesini seçin.  

    [![Yeni varlık açılan menüsü oluşturma iletişim kutusu](media/luis-quickstart-intent-and-list-entity/create-new-list-entity-named-department.png "Yeni varlık açılan menüsü oluşturma iletişim kutusu")](media/luis-quickstart-intent-and-list-entity/create-new-list-entity-named-department.png#lightbox)

1. Departman varlığı sayfasında, yeni değer olarak `Accounting` girin.

1. Eş anlamlıları için önceki tablodan eş anlamlıları ekleyin.

1. Tüm kurallı adları ve bunların eş anlamlılarını eklemeye devam edin. 

## <a name="add-example-utterances-to-the-none-intent"></a>Hiçbiri amacına örnek ekleme 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Uygulamaya yapılan değişikliklerin test edilebilir olması için uygulamayı eğitme 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Eğitilen modelin uç noktadan sorgulanabilir olması için uygulamayı yayımlayın

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Uç noktadan amacı ve varlık tahminini alın

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

1. Adres çubuğundaki URL'nin sonuna gidip `shift Joe Smith to IT` yazın. Son sorgu dizesi parametresi konuşma `q`s**orgusu olan**  öğesidir. Bu konuşma, etiketlenmiş olan konuşmalarla aynı olmadığından iyi bir testtir ve `TransferEmployeeToDepartment` amacını `Department` ayıklanmış şekilde döndürmelidir.

   ```json
    {
      "query": "shift Joe Smith to IT",
      "topScoringIntent": {
        "intent": "TransferEmployeeToDepartment",
        "score": 0.9775754
      },
      "intents": [
        {
          "intent": "TransferEmployeeToDepartment",
          "score": 0.9775754
        },
        {
          "intent": "None",
          "score": 0.0154493852
        }
      ],
      "entities": [
        {
          "entity": "it",
          "type": "Department",
          "startIndex": 19,
          "endIndex": 20,
          "resolution": {
            "values": [
              "Information Technology"
            ]
          }
        }
      ]
    }
   ```

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>İlgili bilgiler

* [Liste varlığı](luis-concept-entity-types.md#list-entity) kavramsal bilgileri
* [Eğitme](luis-how-to-train.md)
* [Yayımlama nasıl yapılır?](luis-how-to-publish-app.md)
* [LUSıS portalında test etme](luis-interactive-test.md)


## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide yeni bir amaç oluşturuldu, örnek konuşmalar eklendi, ardından konuşmalardan tam metin eşleşmeleri ayıklamak için bir liste varlığı oluşturuldu. Uygulama eğitildikten ve yayımlandıktan sonra uç noktaya gönderilen bir sorgu amacı tanımladı ve ayıklanan verileri döndürdü.

[Bileşik bir varlık ekleyerek](luis-tutorial-composite-entity.md)bu uygulamayla devam edin.

> [!div class="nextstepaction"]
> [Uygulamaya bir rol ile önceden oluşturulmuş varlık ekleyin](tutorial-entity-roles.md)

