---
title: Çalışma zamanında uygulamayı Genişlet-LUSıS
description: Önceden yayımlanmış bir tahmin uç noktasını yeni bilgi geçirmek için genişletmeyi öğrenin.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 1d94e9f59062e4d730b8f3b71022442e81e6eeda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98953030"
---
# <a name="extend-app-at-prediction-runtime"></a>Uygulamayı tahmin çalışma zamanında genişletme

Uygulamanın şeması (modeller ve Özellikler) eğitilir ve tahmin uç noktasına yayımlandı. Bu yayımlanmış model, tahmin çalışma zamanında kullanılır. Tahmine karşı tahmin etmek için kullanıcının utterkiyle birlikte yeni bilgileri tahmin çalışma zamanına geçirebilirsiniz.

İki tahmin çalışma zamanı şeması değişikliği şunları içerir:
* [Dış varlıklar](#external-entities)
* [Dinamik listeler](#dynamic-lists)

<a name="external-entities-passed-in-at-prediction-time"></a>

## <a name="external-entities"></a>Dış varlıklar

Dış varlıklar, LUSıS uygulamasına çalışma zamanı sırasında varlıkları tanımlayabilir ve etiketleyebilir, bu da mevcut varlıkların özellikleri olarak kullanılabilir. Bu, sorguları tahmin uç noktanıza göndermeden önce kendi ayrı ve özel varlıklarınızı kullanmanıza olanak sağlar ayıklayıcıları. Bu sorgu tahmini uç noktasında yapıldığından, modelinizi yeniden eğitmeniz ve yayımlamanız gerekmez.

İstemci uygulaması, varlık eşleşmesini yönetip, bu eşleşen varlığın içindeki konumu belirleyerek ve sonra bu bilgileri istekle göndererek kendi varlık ayıklayıcısı 'nı sağlıyor.

Dış varlıklar, hala diğer modellere sinyal olarak kullanılmakta olan herhangi bir varlık türünü genişletmeye yönelik mekanizmadır.

Bu, yalnızca sorgu tahmini çalışma zamanında kullanılabilir verileri olan bir varlık için yararlıdır. Bu tür verilerin örnekleri, sürekli olarak verileri değiştirme veya Kullanıcı başına belirli bir örnektir. Bir LUSıS iletişim varlığını, bir kullanıcının kişi listesindeki dış bilgilerle genişletebilirsiniz.

Dış varlıklar v3 yazma API 'sinin bir parçasıdır. Bu sürüme [geçme](luis-migration-api-v3.md) hakkında daha fazla bilgi edinin.

### <a name="entity-already-exists-in-app"></a>Varlık uygulamada zaten var

`entityName`Uç nokta Istek gönderi gövdesinde geçirilen dış varlığın değeri, istek yapıldığı sırada eğitilen ve yayımlanmış uygulamada zaten mevcut olmalıdır. Varlık türü, tüm türler desteklenir.

### <a name="first-turn-in-conversation"></a>İlk olarak konuşmayı açın

Bir kullanıcının aşağıdaki eksik bilgileri girdiği bir sohbet bot görüşmesinde ilk göz önünde bulundurun:

`Send Hazem a new message`

Sohbet bot 'tan LUSıS 'e gelen istek, `Hazem` Kullanıcı kişilerinin biri olarak doğrudan eşleştirildiğinden, ılgılı posta gövdesinde bilgi gönderebilir.

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 5,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

Tahmin yanıtı, istek içinde tanımlandığından, diğer tüm öngörülen varlıklar ile bu dış varlığı içerir.

### <a name="second-turn-in-conversation"></a>İkinci konuşmayı aç

Sohbet bot 'ta bir sonraki Kullanıcı, daha fazla dönemi kullanır:

`Send him a calendar reminder for the party.`

Konuşmayı bu şekilde yaptığınızda, söylenişi `him` öğesine başvuru olarak kullanılır `Hazem` . GÖNDERI gövdesinde konuşma sohbeti bot, `him` ilk utterden ayıklanan varlık değeriyle eşlenir `Hazem` .

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 3,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

Tahmin yanıtı, istek içinde tanımlandığından, diğer tüm öngörülen varlıklar ile bu dış varlığı içerir.

### <a name="override-existing-model-predictions"></a>Mevcut model tahminlerini geçersiz kıl

`preferExternalEntities`Options özelliği, Kullanıcı aynı ada sahip bir tahmin edilen varlıkla çakışan bir dış varlık gönderiyorsa, luya geçirilen varlığı veya modelde var olan varlığı seçer.

Örneğin, sorguyu göz önünde bulundurun `today I'm free` . `today`Luo, aşağıdaki Yanıt ile bir datetimeV2 algılar:

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

Kullanıcı dış varlığı gönderirse:

```JSON
{
    "entityName": "datetimeV2",
    "startIndex": 0,
    "entityLength": 5,
    "resolution": {
        "date": "2019-06-21"
    }
}
```

, Olarak `preferExternalEntities` ayarlanırsa `false` , dış varlık gönderilmediği gibi lusıs bir yanıt döndürür.

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

, `preferExternalEntities` Olarak ayarlandıysa `true` , lusıs aşağıdakiler dahil bir yanıt döndürür:

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>Çözüm

_İsteğe bağlı_ `resolution` özelliği, tahmine yanıt olarak döner ve dış varlıkla ilişkili meta verileri geçirmenize olanak tanır ve ardından yanıtta geri alabilirsiniz.

Birincil amaç, önceden oluşturulmuş varlıkların genişletilmesine karşın bu varlık türüyle sınırlı değildir.

`resolution`Özelliği bir sayı, dize, nesne veya dizi olabilir:

* Şubesi
* {"metin": "değer"}
* 12345
* ["a", "b", "c"]

<a name="dynamic-lists-passed-in-at-prediction-time"></a>

## <a name="dynamic-lists"></a>Dinamik listeler

Dinamik listeler, zaten LUSıS uygulamasında var olan eğitilen ve yayımlanmış bir liste varlığını genişletmenizi sağlar.

Liste varlık değerlerinizin düzenli aralıklarla değiştirilmesi gerektiğinde bu özelliği kullanın. Bu özellik, zaten eğitilen ve yayımlanmış bir liste varlığını genişletmenizi sağlar:

* Sorgu tahmin uç noktası isteği sırasında.
* Tek bir istek için.

Liste varlığı, LUSıS uygulamasında boş olabilir, ancak var olması gerekiyor. LUSıS uygulamasındaki liste varlığı değiştirilmez, ancak uç noktasındaki tahmin yeteneği, yaklaşık 1.000 öğe içeren en fazla 2 liste içerecek şekilde genişletilir.

### <a name="dynamic-list-json-request-body"></a>Dinamik liste JSON istek gövdesi

Aşağıdaki JSON gövdesine göndererek, listeye eş anlamlı olan yeni bir alt liste ekleyin ve `LUIS` sorgu tahmini isteğiyle metin için liste varlığını tahmin edin `POST` :

```JSON
{
    "query": "Send Hazem a message to add an item to the meeting agenda about LUIS.",
    "options":{
        "timezoneOffset": "-8:00"
    },
    "dynamicLists": [
        {
            "listEntity*":"ProductList",
            "requestLists":[
                {
                    "name": "Azure Cognitive Services",
                    "canonicalForm": "Azure-Cognitive-Services",
                    "synonyms":[
                        "language understanding",
                        "luis",
                        "qna maker"
                    ]
                }
            ]
        }
    ]
}
```

Tahmin yanıtı, istekte tanımlandığından, diğer tüm öngörülen varlıkların bulunduğu liste varlığını içerir.

## <a name="next-steps"></a>Sonraki adımlar

* [Tahmin puanı](luis-concept-prediction-score.md)
* [API v3 değişikliklerini yazma](luis-migration-api-v3.md)
