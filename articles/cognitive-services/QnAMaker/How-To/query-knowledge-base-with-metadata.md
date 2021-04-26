---
title: Meta verileri kullanarak filtre bağlamsal olarak
titleSuffix: Azure Cognitive Services
description: Soru-Cevap Oluşturma, meta verilere göre QnA çiftlerini filtreler.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 8f65ca9386963824f0cb740f587de83c9dec7f78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103022145"
---
# <a name="filter-responses-with-metadata"></a>Meta verileri olan yanıtları filtrele

Soru-Cevap Oluşturma, anahtar ve değer çiftleri biçiminde meta verileri, soru ve yanıt çiftlerine eklemenizi sağlar. Daha sonra bu bilgileri Kullanıcı sorgularıyla sonuçları filtrelemek ve izleme konuşmalarında kullanılabilecek ek bilgileri depolamak için kullanabilirsiniz.

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Soru ve yanıtları bir QnA varlığıyla depolayın

Soru-Cevap Oluşturma soruyu nasıl depolayacağınızı ve verileri nasıl yanıtlayabileceğinizi anlamak önemlidir. Aşağıdaki çizim bir QnA varlığını göstermektedir:

![Bir QnA varlığının çizimi](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Her QnA varlığının benzersiz ve kalıcı bir KIMLIĞI vardır. Belirli bir QnA varlığında güncelleştirme yapmak için KIMLIĞINI kullanabilirsiniz.

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>Özel meta veri etiketlerine göre yanıtları filtrelemek için meta verileri kullanma

Meta veri eklemek, yanıtları bu meta veri etiketlerine göre filtrelemenize izin verir. **Görünüm seçenekleri** menüsünden meta veri sütununu ekleyin. Meta veri simgesini seçerek, meta veri çifti eklemek için bilgi bankasından meta veriler ekleyin **+** . Bu çift bir anahtar ve bir değer içerir.

![Meta veri ekleme ekran görüntüsü](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Meta veri etiketleri için en strictFilters ile Sonuçları filtrele

"Bu otel ne zaman kapatıldığında?" Kullanıcı sorusunu göz önünde bulundurun.

Sonuçlar yalnızca Restoran "PARADISE" için gerekli olduğundan, "restoran adı" meta verilerinde GenerateAnswer çağrısında bir filtre ayarlayabilirsiniz. Aşağıdaki örnek şunu gösterir:

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [ { "name": "restaurant", "value": "paradise"}]
}
```

### <a name="logical-and-by-default"></a>Mantıksal ve varsayılan olarak

Sorgudaki çeşitli meta veri filtrelerini birleştirmek için, özelliğin dizisine ek meta veri filtreleri ekleyin `strictFilters` . Varsayılan olarak, değerler mantıksal olarak birleştirilir (ve). Bir mantıksal birleşim, çiftin yanıt içinde döndürülmesi için tüm filtrelerin QnA çiftleriyle eşleşmesini gerektirir.

Bu, `strictFiltersCompoundOperationType` özelliğinin değeri ile birlikte kullanılmasına eşdeğerdir `AND` .

### <a name="logical-or-using-strictfilterscompoundoperationtype-property"></a>StrictFiltersCompoundOperationType özelliğini kullanarak mantıksal veya

Birden çok meta veri filtresi birleştirilirken, yalnızca bir veya birkaç filtre eşleştirme ile ilgileniyorlarsa, `strictFiltersCompoundOperationType` özelliği değerini kullanın `OR` .

Bu, herhangi bir filtre eşleştiğinde, ancak meta verisi olmayan yanıtlar döndürmeyeceği bilgi Bankalarınızın yanıtları döndürmesini sağlar.

```json
{
    "question": "When do facilities in this hotel close?",
    "top": 1,
    "strictFilters": [
      { "name": "type","value": "restaurant"},
      { "name": "type", "value": "bar"},
      { "name": "type", "value": "poolbar"}
    ],
    "strictFiltersCompoundOperationType": "OR"
}
```

### <a name="metadata-examples-in-quickstarts"></a>Hızlı başlangıçlarda meta veri örnekleri

Meta veriler hakkında daha fazla bilgi için Soru-Cevap Oluşturma Portal Hızlı başlangıcı:
* [Yazma-meta verileri QnA çiftine ekleyin](../quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)
* [Sorgu tahmini-meta verilere göre filtre yanıtları](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md)

<a name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Konuşma bağlamını tutmak için soru ve yanıt sonuçlarını kullanın

GenerateAnswer yanıtı, eşleşen soru ve yanıt çiftinin karşılık gelen meta veri bilgilerini içerir. Bu bilgileri, daha sonraki konuşmalarda kullanılmak üzere önceki görüşmenin bağlamını depolamak için istemci uygulamanızda kullanabilirsiniz.

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Bilgi Bankası 'nizi çözümleyin](../How-to/get-analytics-knowledge-base.md)
