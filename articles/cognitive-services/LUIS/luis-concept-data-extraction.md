---
title: Veri ayıklama-LUSıS
titleSuffix: Azure Cognitive Services
description: Amaç ve varlıklar ile söylenişi metinlerdeki verileri ayıklayın. Language Understanding (LUSıS) öğesinden ne tür verilerin ayıklanabileceği hakkında bilgi edinin.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: ff0a9838d1fcc9db3b6cc25b47c840e01056e6cd
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703146"
---
# <a name="extract-data-from-utterance-text-with-intents-and-entities"></a>Amaç ve varlıklar ile söylenişi metinlerinde veri ayıklama
LUO, kullanıcının doğal dil dıklarından bilgi almanıza olanak tanır. Bilgiler, bir program, uygulama veya sohbet bot tarafından işlem gerçekleştirmek üzere kullanılabilecek şekilde ayıklanır. Aşağıdaki bölümlerde, veri ve varlıklardan JSON örnekleri ile hangi verilerin döndürüldüğünü öğrenin.

Bir tam metin eşleşmesi olmadığından Ayıklanacak en zor veriler makine tarafından öğrenilen veri. Makine tarafından öğrenilen [varlıkların](luis-concept-entity-types.md) veri ayıklamasıyla, beklediğiniz verileri elde edinceye kadar, [yazma döngüsünün](luis-concept-app-iteration.md) bir parçası olması gerekir.

## <a name="data-location-and-key-usage"></a>Veri konumu ve anahtar kullanımı
LUO, yayımlanan [uç noktadan](luis-glossary.md#endpoint)verileri sağlar. **Https isteği** (Post veya Get), hazırlama veya üretim ortamları gibi bazı isteğe bağlı yapılandırmalara sahiptir.

#### <a name="v2-prediction-endpoint-requesttabv2"></a>[V2 tahmin uç noktası isteği](#tab/V2)

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

#### <a name="v3-prediction-endpoint-requesttabv3"></a>[V3 tahmin uç noktası isteği](#tab/V3)

`https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/<appID>/slots/<slot-type>/predict?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&query=book 2 tickets to paris`

[V3 tahmin uç noktası](luis-migration-api-v3.md)hakkında daha fazla bilgi edinin.

* * * 

@No__t-0, lusıs uygulamasının **Ayarlar** sayfasında (`/apps/` ' den sonra), bu Luo uygulamayı düzenlediğinizde URL 'nin bir parçası olarak kullanılabilir. @No__t-0, uygulamanızı sorgulamak için kullanılan uç nokta anahtarıdır. LUSıS öğrenirken ücretsiz yazma/başlangıç anahtarınızı kullanabilirsiniz, ancak Endpoint tuşunun [beklenen lusıs kullanımınızı](luis-boundaries.md#key-limits)destekleyen bir anahtarla değiştirilmesi önemlidir. @No__t-0 birimi dakikadır.

**Https yanıtı** , her türlü amaç ve varlık bilgisini içerir. Bu, hazırlama veya üretim uç noktasının geçerli yayımlanmış modeline göre belirlenir. Uç nokta URL 'SI, bu web sitesinde, **anahtarlar ve uç noktalar** sayfasındaki **Yönet** bölümünde bulunan [halsıs](luis-reference-regions.md) Web sitesinde bulunur.

## <a name="data-from-intents"></a>Amaçlardan alınan veriler
Birincil veri, en üst Puanlama **hedefi adıdır**. Uç nokta yanıtı:

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 tahmin uç noktası yanıtı](#tab/V2)

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 tahmin uç noktası yanıtı](#tab/V3)

```JSON
{
  "query": "when do you open next?",
  "prediction": {
    "normalizedQuery": "when do you open next?",
    "topIntent": "GetStoreInfo",
    "intents": {
        "GetStoreInfo": {
            "score": 0.984749258
        }
    }
  },
  "entities": []
}
```

[V3 tahmin uç noktası](luis-migration-api-v3.md)hakkında daha fazla bilgi edinin.

* * * 

|Veri nesnesi|Veri Türü|Veri konumu|Değer|
|--|--|--|--|
|Varsayılandır|Dize|topScoringIntent. amaç|"GetStoreInfo"|

Sohbet botu veya lu, çağırma uygulamanız birden fazla amaç puanı temelinde bir karar yapıyorsa, tüm amaç puanlarını döndürün.


#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 tahmin uç noktası yanıtı](#tab/V2)

QueryString parametresini `verbose=true` olarak ayarlayın. Uç nokta yanıtı:

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "intents": [
    {
      "intent": "GetStoreInfo",
      "score": 0.984749258
    },
    {
      "intent": "None",
      "score": 0.2040639
    }
  ],
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 tahmin uç noktası yanıtı](#tab/V3)

QueryString parametresini `show-all-intents=true` olarak ayarlayın. Uç nokta yanıtı:

```JSON
{
    "query": "when do you open next?",
    "prediction": {
        "normalizedQuery": "when do you open next?",
        "topIntent": "GetStoreInfo",
        "intents": {
            "GetStoreInfo": {
                "score": 0.984749258
            },
            "None": {
                 "score": 0.2040639
            }
        },
        "entities": {
        }
    }
}
```

[V3 tahmin uç noktası](luis-migration-api-v3.md)hakkında daha fazla bilgi edinin.

* * * 

Amaçlar, en yüksekten en düşük puanla sıralanır.

|Veri nesnesi|Veri Türü|Veri konumu|Değer|ınızı|
|--|--|--|--|:--|
|Varsayılandır|Dize|amaçlar [0]. amaç|"GetStoreInfo"|0,984749258|
|Varsayılandır|Dize|amaçlar [1]. amaç|Seçim|0,0168218873|

Önceden oluşturulmuş etki alanları eklerseniz, amaç adı etki alanını belirtir (örneğin, `Utilties` veya `Communication`) ve Amaç:

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 tahmin uç noktası yanıtı](#tab/V2)

```JSON
{
  "query": "Turn on the lights next monday at 9am",
  "topScoringIntent": {
    "intent": "Utilities.ShowNext",
    "score": 0.07842206
  },
  "intents": [
    {
      "intent": "Utilities.ShowNext",
      "score": 0.07842206
    },
    {
      "intent": "Communication.StartOver",
      "score": 0.0239675418
    },
    {
      "intent": "None",
      "score": 0.0168218873
    }],
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 tahmin uç noktası yanıtı](#tab/V3)

```JSON
{
    "query": "Turn on the lights next monday at 9am",
    "prediction": {
        "normalizedQuery": "Turn on the lights next monday at 9am",
        "topIntent": "Utilities.ShowNext",
        "intents": {
            "Utilities.ShowNext": {
                "score": 0.07842206
            },
            "Communication.StartOver": {
                "score": 0.0239675418
            },
            "None": {
                "score": 0.00085447653
            }
        },
        "entities": []
    }
}
```

[V3 tahmin uç noktası](luis-migration-api-v3.md)hakkında daha fazla bilgi edinin.

* * * 

|Etki Alanı|Veri nesnesi|Veri Türü|Veri konumu|Değer|
|--|--|--|--|--|
|Yardımcı Programlar|Varsayılandır|Dize|amaçlar [0]. amaç|"<b>Yardımcı programları</b>. ShowNext "|
|Kurulan|Varsayılandır|Dize|amaçlar [1]. amaç|<b>İletişim</b>. StartOver "|
||Varsayılandır|Dize|amaçlar [2]. amaç|Seçim|


## <a name="data-from-entities"></a>Varlıklardan veriler
Çoğu chatbots ve uygulamanın, amaç adından daha fazla ihtiyacı vardır. Bu ek, isteğe bağlı veriler, utterance 'te bulunan varlıklardan gelir. Her varlık türü, eşleşme hakkındaki farklı bilgileri döndürür.

Söylenişi 'teki tek bir sözcük veya tümcecik birden fazla varlıkla eşleşiyor olabilir. Bu durumda, eşleşen her varlık puanı ile döndürülür.

Tüm varlıklar uç noktadan yanıtın **varlıklar** dizisinde döndürülür:

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 tahmin uç noktası yanıtı](#tab/V2)

```JSON
"entities": [
  {
    "entity": "bob jones",
    "type": "Name",
    "startIndex": 0,
    "endIndex": 8,
    "score": 0.473899543
  },
  {
    "entity": "3",
    "type": "builtin.number",
    "startIndex": 16,
    "endIndex": 16,
    "resolution": {
      "value": "3"
    }
  }
]
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 tahmin uç noktası yanıtı](#tab/V3)

```JSON
"entities": {
    "name":["bob jones"],
    "number": [3]
}
```
[V3 tahmin uç noktası](luis-migration-api-v3.md)hakkında daha fazla bilgi edinin.

* * * 

## <a name="tokenized-entity-returned"></a>Simgeleştirilmiş varlık döndürüldü
Çeşitli [kültürler](luis-language-support.md#tokenization) , `entity` değeri [simgeleştirilmiş](luis-glossary.md#token)varlık nesnesini döndürür. ISIS tarafından döndürülen startIndex ve endIndex, varlık nesnesinde yeni ve simgeleştirilmiş değere eşlenmiyor, ancak ham varlığı programlı bir şekilde ayıklamanız için özgün sorguya değil. 

Örneğin, Almanya 'da, `das Bauernbrot` kelimesi `das bauern brot` ' e ayarlanır. @No__t-0 olan simgeleştirilmiş değer döndürülür ve özgün değer özgün sorgunun startIndex ve endIndex öğesinden programlı bir şekilde belirlenebilir ve bu da `das Bauernbrot` ' i verir.

## <a name="simple-entity-data"></a>Basit varlık verileri

[Basit bir varlık](reference-entity-simple.md) , makine tarafından öğrenilen bir değerdir. Bu bir sözcük veya tümcecik olabilir.

## <a name="composite-entity-data"></a>Bileşik varlık verileri

[Bileşik bir varlık](reference-entity-composite.md) , önceden oluşturulmuş varlıklar, basit, normal ifade ve liste varlıkları gibi diğer varlıklardan oluşur. Ayrı varlıklar bir bütün varlığı oluşturur. 

## <a name="list-entity-data"></a>Varlık verilerini listeleme

[Liste varlıkları](reference-entity-list.md) , sabit ve kapalı bir ilgili sözcük kümesini eş anlamlılarıyla birlikte temsil eder. LUSıS, liste varlıkları için ek değerler bulamaz. Geçerli listeye göre yeni kelimelerin önerilerini görmek için **öner** özelliğini kullanın. Aynı değere sahip birden fazla liste varlığı varsa, her bir varlık uç nokta sorgusunda döndürülür. 

## <a name="prebuilt-entity-data"></a>Önceden oluşturulmuş varlık verileri
[Önceden oluşturulmuş](luis-concept-entity-types.md) varlıklar, açık kaynaklı [Tanıyıcılar-metin](https://github.com/Microsoft/Recognizers-Text) projesi kullanılarak normal ifade eşleşmesi temel alınarak bulunur. Önceden oluşturulmuş varlıklar, varlıklar dizisinde döndürülür ve `builtin::` ' dan önekli tür adını kullanır. Aşağıdaki metin, döndürülen önceden oluşturulmuş varlıklar ile bir örnektir:

`Dec 5th send to +1 360-555-1212`

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 tahmin uç noktası yanıtı](#tab/V2)

```JSON
"entities": [
    {
      "entity": "dec 5th",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-12-05",
            "type": "date",
            "value": "2017-12-05"
          },
          {
            "timex": "XXXX-12-05",
            "type": "date",
            "value": "2018-12-05"
          }
        ]
      }
    },
    {
      "entity": "1",
      "type": "builtin.number",
      "startIndex": 18,
      "endIndex": 18,
      "resolution": {
        "value": "1"
      }
    },
    {
      "entity": "360",
      "type": "builtin.number",
      "startIndex": 20,
      "endIndex": 22,
      "resolution": {
        "value": "360"
      }
    },
    {
      "entity": "555",
      "type": "builtin.number",
      "startIndex": 26,
      "endIndex": 28,
      "resolution": {
        "value": "555"
      }
    },
    {
      "entity": "1212",
      "type": "builtin.number",
      "startIndex": 32,
      "endIndex": 35,
      "resolution": {
        "value": "1212"
      }
    },
    {
      "entity": "5th",
      "type": "builtin.ordinal",
      "startIndex": 4,
      "endIndex": 6,
      "resolution": {
        "value": "5"
      }
    },
    {
      "entity": "1 360 - 555 - 1212",
      "type": "builtin.phonenumber",
      "startIndex": 18,
      "endIndex": 35,
      "resolution": {
        "value": "1 360 - 555 - 1212"
      }
    }
  ]
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 tahmin uç noktası yanıtı](#tab/V3)

QueryString parametresi olmadan, `verbose=true`:

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-12-05",
                    "value": "2018-12-05"
                },
                {
                    "timex": "XXXX-12-05",
                    "value": "2019-12-05"
                }
            ]
        }
    ],
    "ordinal": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "ordinalV2": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "number": [
        1360,
        555,
        1212
    ],
    "phonenumber": [
        "1 360-555-1212"
    ]
}
```

QueryString parametresiyle `verbose=true`:

```json

"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-12-05",
                    "value": "2018-12-05"
                },
                {
                    "timex": "XXXX-12-05",
                    "value": "2019-12-05"
                }
            ]
        }
    ],
    "ordinal": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "ordinalV2": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "number": [
        1360,
        555,
        1212
    ],
    "phonenumber": [
        "1 360-555-1212"
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "Dec 5th",
                "startIndex": 0,
                "length": 7,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "ordinal": [
            {
                "type": "builtin.ordinal",
                "text": "5th",
                "startIndex": 4,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "ordinalV2": [
            {
                "type": "builtin.ordinalV2",
                "text": "5th",
                "startIndex": 4,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "number": [
            {
                "type": "builtin.number",
                "text": "1 360",
                "startIndex": 17,
                "length": 5,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.number",
                "text": "555",
                "startIndex": 23,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.number",
                "text": "1212",
                "startIndex": 27,
                "length": 4,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "phonenumber": [
            {
                "type": "builtin.phonenumber",
                "text": "1 360-555-1212",
                "startIndex": 17,
                "length": 14,
                "score": 1.0,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

[V3 tahmin uç noktası](luis-migration-api-v3.md)hakkında daha fazla bilgi edinin.

* * * 
## <a name="regular-expression-entity-data"></a>Normal ifade varlık verileri

[Normal ifade varlığı](reference-entity-regular-expression.md) , sağladığınız normal ifade düzenine göre bir varlığı ayıklar.

## <a name="extracting-names"></a>Adları ayıklama
Bir ad, neredeyse tüm harflerin ve sözcüklerin bir birleşimi olabileceğinden, bir tanınaradan adların alınması zordur. Hangi tür adı ayıkladığınıza bağlı olarak, birkaç seçeneğiniz vardır. Aşağıdaki öneriler kurallar değildir ancak daha fazla yönerge değildir.

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>Önceden oluşturulmuş PersonName ve GeographyV2 varlıklarını ekleyin

[PersonName](luis-reference-prebuilt-person.md) ve [GeographyV2](luis-reference-prebuilt-geographyV2.md) varlıkları bazı [dil kültürleri](luis-reference-prebuilt-entities.md)içinde kullanılabilir. 

### <a name="names-of-people"></a>Kişilerin adları

Kişilerin adı, dile ve kültüre bağlı olarak bazı hafif biçimlendirmeye sahip olabilir. Önceden oluşturulmuş bir **[PersonName](luis-reference-prebuilt-person.md)** varlığı veya ad ve soyadı [rollerine](luis-concept-roles.md) sahip **[basit bir varlık](luis-concept-entity-types.md#simple-entity)** kullanın. 

Basit varlığı kullanırsanız, farklı uzunluklarda Farklı uzunluklardaki ilk ve son adı kullanan örneklere ve hiçbir türlü amaç dahil olmak üzere tüm amaçlar genelinde dikkat edilecek örneklere sahip olun. Doğru tahmin edilemeyen adların etiketlenmesi için düzenli olarak uç nokta utslarını [gözden geçirin](luis-how-to-review-endoint-utt.md) .

### <a name="names-of-places"></a>Yerlerin adları

Konum adları, şehir, ilçe, eyalet, eyalet ve ülke/bölge gibi bir şekilde ayarlanır ve bilinmektedir. Konum bilgilerini ayıklamak için önceden oluşturulan **[geographyV2](luis-reference-prebuilt-geographyv2.md)** varlığını kullanın.

### <a name="new-and-emerging-names"></a>Yeni ve gelişen adlar

Bazı uygulamaların, ürünler veya şirketler gibi yeni ve gelişmekte olan adları bulabilmeleri gerekir. Bu tür adlar, veri ayıklamanın en zor türüdür. **[Basit bir varlıkla](luis-concept-entity-types.md#simple-entity)** başlayın ve [tümcecik listesi](luis-concept-feature.md)ekleyin. Doğru tahmin edilemeyen adların etiketlenmesi için düzenli olarak uç nokta utslarını [gözden geçirin](luis-how-to-review-endoint-utt.md) .

## <a name="pattern-roles-data"></a>Model rolü verileri
Roller, varlıkların bağlamsal farklarıdır.


#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 tahmin uç noktası yanıtı](#tab/V2)

Varlık adı, iki rol içeren `Location`, `Origin` ve `Destination` ' dir.

```JSON
"entities": [
  {
    "entity": "bob jones",
    "type": "Employee",
    "startIndex": 5,
    "endIndex": 13,
    "score": 0.922820568,
    "role": ""
  },
  {
    "entity": "seattle",
    "type": "Location",
    "startIndex": 20,
    "endIndex": 26,
    "score": 0.948008537,
    "role": "Origin"
  },
  {
    "entity": "redmond",
    "type": "Location",
    "startIndex": 31,
    "endIndex": 37,
    "score": 0.7047979,
    "role": "Destination"
  }
]
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 tahmin uç noktası yanıtı](#tab/V3)

V3 'de **rol adı** nesnenin birincil adıdır. 

Varlık adı, iki rol içeren `Location`, `Origin` ve `Destination` ' dir.

QueryString parametresi olmadan, `verbose=true`:

```json
"entities": {
    "Employee": [
        "bob jones"
    ],
    "Origin": [
        "seattle"
    ],
    "Destination": [
        "redmond"
    ]
}
```

QueryString parametresiyle `verbose=true`:

```json
"entities": {
    "Employee": [
        "bob jones"
    ],
    "LocationOrigin": [
        "seattle"
    ],
    "LocationDestination": [
        "redmond"
    ],
    "$instance": {
        "Employee": [
            {
                "type": "Employee",
                "text": "bob jones",
                "startIndex": 5,
                "length": 9,
                "score": 0.982873261,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Origin": [
            {
                "role": "Origin",
                "type": "Location",
                "text": "seattle",
                "startIndex": 20,
                "length": 7,
                "score": 0.9913306,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Destination": [
            {
                "role": "Destination",
                "type": "Location",
                "text": "redmond",
                "startIndex": 31,
                "length": 7,
                "score": 0.898179531,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]

}
```

[V3 tahmin uç noktası](luis-migration-api-v3.md)hakkında daha fazla bilgi edinin.

* * *

## <a name="patternany-entity-data"></a>Model. tüm varlık verileri

[Desen. any](reference-entity-pattern-any.md) , varlığın nerede başladığını ve bittiğini işaretlemek için yalnızca bir desen şablonunda kullanılan değişken uzunluklu bir yer tutucudur.  

## <a name="sentiment-analysis"></a>Yaklaşım Analizi
Yaklaşım Analizi yapılandırılırsa, LUSıS JSON yanıtı yaklaşım analizini içerir. [Metin analizi](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) belgelerindeki yaklaşım analizi hakkında daha fazla bilgi edinin.

### <a name="sentiment-data"></a>Yaklaşım verileri
Yaklaşım verileri, 1 ile 0 arasında pozitif (1 ' e yakın) veya negatif (0 ' a yakın) verilerin yaklaşımını belirten bir puandır.

Kültür `en-us` olduğunda yanıt şu şekilde olur:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

Diğer tüm kültürler için yanıt şu şekilde yapılır:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```


### <a name="key-phrase-extraction-entity-data"></a>Anahtar tümceciği ayıklama varlığı verileri
Anahtar tümceciği ayıklama varlığı, [metin analizi](https://docs.microsoft.com/azure/cognitive-services/text-analytics/)tarafından sunulan, utterance 'teki anahtar tümceleri döndürür.


#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 tahmin uç noktası yanıtı](#tab/V2)

```JSON
{
  "query": "Is there a map of places with beautiful views on a favorite trail?",
  "topScoringIntent": {
    "intent": "GetJobInformation",
    "score": 0.764368951
  },
  "intents": [
    ...
  ],
  "entities": [
    {
      "entity": "beautiful views",
      "type": "builtin.keyPhrase",
      "startIndex": 30,
      "endIndex": 44
    },
    {
      "entity": "map of places",
      "type": "builtin.keyPhrase",
      "startIndex": 11,
      "endIndex": 23
    },
    {
      "entity": "favorite trail",
      "type": "builtin.keyPhrase",
      "startIndex": 51,
      "endIndex": 64
    }
  ]
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 tahmin uç noktası yanıtı](#tab/V3)

[V3 tahmin uç noktası](luis-migration-api-v3.md)hakkında daha fazla bilgi edinin.

QueryString parametresi olmadan, `verbose=true`:

```json
"entities": {
    "keyPhrase": [
        "map of places",
        "beautiful views",
        "favorite trail"
    ]
}
```

QueryString parametresiyle `verbose=true`:

```json
"entities": {
    "keyPhrase": [
        "map of places",
        "beautiful views",
        "favorite trail"
    ],
    "$instance": {
        "keyPhrase": [
            {
                "type": "builtin.keyPhrase",
                "text": "map of places",
                "startIndex": 11,
                "length": 13,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.keyPhrase",
                "text": "beautiful views",
                "startIndex": 30,
                "length": 15,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.keyPhrase",
                "text": "favorite trail",
                "startIndex": 51,
                "length": 14,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

[V3 tahmin uç noktası](luis-migration-api-v3.md)hakkında daha fazla bilgi edinin.

* * *


## <a name="data-matching-multiple-entities"></a>Birden çok varlıkla eşleşen veriler

LUSıS, utterance 'te bulunan tüm varlıkları döndürür. Sonuç olarak, sohbet botu için sonuçlara göre karar vermeniz gerekebilir. Söylenişi, bir utterde birçok varlığa sahip olabilir:

`book me 2 adult business tickets to paris tomorrow on air france`

LUO uç noktası, farklı varlıklarda aynı verileri bulabilir.

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 tahmin uç noktası yanıtı](#tab/V2)

```JSON
{
  "query": "book me 2 adult business tickets to paris tomorrow on air france",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 1.0
  },
  "intents": [
    {
      "intent": "BookFlight",
      "score": 1.0
    },
    {
      "intent": "Concierge",
      "score": 0.04216196
    },
    {
      "intent": "None",
      "score": 0.03610297
    }
  ],
  "entities": [
    {
      "entity": "air france",
      "type": "Airline",
      "startIndex": 54,
      "endIndex": 63,
      "score": 0.8291798
    },
    {
      "entity": "adult",
      "type": "Category",
      "startIndex": 10,
      "endIndex": 14,
      "resolution": {
        "values": [
          "adult"
        ]
      }
    },
    {
      "entity": "paris",
      "type": "Cities",
      "startIndex": 36,
      "endIndex": 40,
      "resolution": {
        "values": [
          "Paris"
        ]
      }
    },
    {
      "entity": "tomorrow",
      "type": "builtin.datetimeV2.date",
      "startIndex": 42,
      "endIndex": 49,
      "resolution": {
        "values": [
          {
            "timex": "2018-02-21",
            "type": "date",
            "value": "2018-02-21"
          }
        ]
      }
    },
    {
      "entity": "paris",
      "type": "Location::ToLocation",
      "startIndex": 36,
      "endIndex": 40,
      "score": 0.9730773
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 8,
      "endIndex": 8,
      "resolution": {
        "value": "2"
      }
    },
    {
      "entity": "business",
      "type": "Seat",
      "startIndex": 16,
      "endIndex": 23,
      "resolution": {
        "values": [
          "business"
        ]
      }
    },
    {
      "entity": "2 adult business",
      "type": "TicketSeatOrder",
      "startIndex": 8,
      "endIndex": 23,
      "score": 0.8784727
    }
  ],
  "compositeEntities": [
    {
      "parentType": "TicketSeatOrder",
      "value": "2 adult business",
      "children": [
        {
          "type": "Category",
          "value": "adult"
        },
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "Seat",
          "value": "business"
        }
      ]
    }
  ]
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 tahmin uç noktası yanıtı](#tab/V3)

QueryString parametresi olarak `verbose=true` olmadan.

```json
"entities": {
    "TicketsOrder": [
        {
            "number": [
                2
            ],
            "PassengerCategory": [
                "adult"
            ],
            "TravelClass": [
                "business"
            ]
        }
    ],
    "Location::LocationTo": [
        "paris"
    ],
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "2019-09-28",
                    "value": "2019-09-28"
                }
            ]
        }
    ],
    "Airline": [
        "air france"
    ]
}
```

@No__t-0 ile QueryString parametresi olarak.


```json
"entities": {
    "TicketsOrder": [
        {
            "number": [
                2
            ],
            "PassengerCategory": [
                "adult"
            ],
            "TravelClass": [
                "business"
            ],
            "$instance": {
                "number": [
                    {
                        "type": "builtin.number",
                        "text": "2",
                        "startIndex": 8,
                        "length": 1,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "PassengerCategory": [
                    {
                        "type": "PassengerCategory",
                        "text": "adult",
                        "startIndex": 10,
                        "length": 5,
                        "score": 0.9503733,
                        "modelTypeId": 3,
                        "modelType": "Hierarchical Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "TravelClass": [
                    {
                        "type": "TravelClass",
                        "text": "business",
                        "startIndex": 16,
                        "length": 8,
                        "score": 0.950095,
                        "modelTypeId": 3,
                        "modelType": "Hierarchical Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    ],
    "Location::LocationTo": [
        "paris"
    ],
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "2019-09-28",
                    "value": "2019-09-28"
                }
            ]
        }
    ],
    "Airline": [
        "air france"
    ],
    "$instance": {
        "TicketsOrder": [
            {
                "type": "TicketsOrder",
                "text": "2 adult business",
                "startIndex": 8,
                "length": 16,
                "score": 0.942183256,
                "modelTypeId": 4,
                "modelType": "Composite Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Location::LocationTo": [
            {
                "type": "Location::LocationTo",
                "text": "paris",
                "startIndex": 36,
                "length": 5,
                "score": 0.9905354,
                "modelTypeId": 3,
                "modelType": "Hierarchical Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "tomorrow",
                "startIndex": 42,
                "length": 8,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Airline": [
            {
                "type": "Airline",
                "text": "air france",
                "startIndex": 54,
                "length": 10,
                "score": 0.9455415,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

[V3 tahmin uç noktası](luis-migration-api-v3.md)hakkında daha fazla bilgi edinin.

* * *

## <a name="data-matching-multiple-list-entities"></a>Birden çok liste varlığı ile eşleşen veriler

Bir sözcük veya tümcecik birden fazla liste varlığıyla eşleşiyorsa, uç nokta sorgusu her bir liste varlığını döndürür.

@No__t-0 sorgusu için ve uygulamanın birden fazla listede `red` kelimesi varsa, LUSıS tüm varlıkları tanır ve JSON uç noktası yanıtının bir parçası olarak bir varlık dizisi döndürür: 

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 tahmin uç noktası yanıtı](#tab/V2)

```JSON
{
  "query": "when is the best time to go to red rock?",
  "topScoringIntent": {
    "intent": "Calendar.Find",
    "score": 0.06701678
  },
  "entities": [
    {
      "entity": "red",
      "type": "Colors",
      "startIndex": 31,
      "endIndex": 33,
      "resolution": {
        "values": [
          "Red"
        ]
      }
    },
    {
      "entity": "red rock",
      "type": "Cities",
      "startIndex": 31,
      "endIndex": 38,
      "resolution": {
        "values": [
          "Destinations"
        ]
      }
    }
  ]
}
```



#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 tahmin uç noktası yanıtı](#tab/V3)

Sorgu dizesinde `verbose=true` olmadan:

```JSON
{
    "query": "when is the best time to go to red rock",
    "prediction": {
        "normalizedQuery": "when is the best time to go to red rock",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "Colors": [
                [
                    "red"
                ]
            ],
            "Cities": [
                [
                    "Destinations"
                ]
            ]
        }
    }
}
```


Sorgu dizesinde `verbose=true` ile:

```JSON
{
    "query": "when is the best time to go to red rock",
    "prediction": {
        "normalizedQuery": "when is the best time to go to red rock",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "Colors": [
                [
                    "red"
                ]
            ],
            "Cities": [
                [
                    "Destinations"
                ]
            ],
            "$instance": {
                "Colors": [
                    {
                        "type": "Colors",
                        "text": "red",
                        "startIndex": 31,
                        "length": 3,
                        "modelTypeId": 5,
                        "modelType": "List Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "Cities": [
                    {
                        "type": "Cities",
                        "text": "red rock",
                        "startIndex": 31,
                        "length": 8,
                        "modelTypeId": 5,
                        "modelType": "List Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    }
}
```

[V3 tahmin uç noktası](luis-migration-api-v3.md)hakkında daha fazla bilgi edinin.

* * *

## <a name="next-steps"></a>Sonraki adımlar

LUSıS uygulamanıza varlık ekleme hakkında daha fazla bilgi edinmek için bkz. [varlık ekleme](luis-how-to-add-entities.md) .
