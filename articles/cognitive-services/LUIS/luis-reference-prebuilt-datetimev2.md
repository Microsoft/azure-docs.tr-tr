---
title: DatetimeV2 önceden oluşturulmuş varlıklar-LUSıS
titleSuffix: Azure Cognitive Services
description: Bu makalede, Language Understanding (LUSıS) içinde datetimeV2 önceden oluşturulmuş varlık bilgileri bulunur.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/13/2020
ms.openlocfilehash: 83522de9c00056a3808b002b3103f45c72553399
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96013080"
---
# <a name="datetimev2-prebuilt-entity-for-a-luis-app"></a>LUSıS uygulaması için DatetimeV2 önceden oluşturulmuş varlık

**DatetimeV2** önceden oluşturulmuş varlık tarih ve saat değerlerini ayıklar. Bu değerler, istemci programlarının kullanması için standartlaştırılmış bir biçimde çözümlenir. Bir söylenişi 'nin tamamlanmamış bir tarih veya saati olduğunda, Luo, uç nokta yanıtında _hem geçmiş hem de gelecekteki değerleri_ içerir. Bu varlık zaten eğitiltiğinden, uygulama amaçlarını datetimeV2 içeren örnek bir değer eklemeniz gerekmez.

## <a name="types-of-datetimev2"></a>DatetimeV2 türleri
DatetimeV2, [Tanıyıcılar-metin](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-DateTime.yaml) GitHub deposundan yönetilir.

## <a name="example-json"></a>Örnek JSON

Aşağıdaki söylenişi ve bunun kısmen JSON yanıtı aşağıda gösterilmiştir.

`8am on may 2nd 2019`

#### <a name="v3-response"></a>[V3 yanıtı](#tab/1-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "datetime",
            "values": [
                {
                    "timex": "2019-05-02T08",
                    "resolution": [
                        {
                            "value": "2019-05-02 08:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[V3 ayrıntılı yanıt](#tab/1-2)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "datetime",
            "values": [
                {
                    "timex": "2019-05-02T08",
                    "resolution": [
                        {
                            "value": "2019-05-02 08:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.datetime",
                "text": "8am on may 2nd 2019",
                "startIndex": 0,
                "length": 19,
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

#### <a name="v2-response"></a>[V2 yanıtı](#tab/1-3)

```json
"entities": [
  {
    "entity": "8am on may 2nd 2019",
    "type": "builtin.datetimeV2.datetime",
    "startIndex": 0,
    "endIndex": 18,
    "resolution": {
      "values": [
        {
          "timex": "2019-05-02T08",
          "type": "datetime",
          "value": "2019-05-02 08:00:00"
        }
      ]
    }
  }
]
 ```

|Özellik adı |Özellik türü ve açıklaması|
|---|---|
|Varlık|Tarih, saat, tarih aralığı veya zaman aralığı türü ile utterden çıkarılan **dize** metni.|
|tür|**dize** - [datetimeV2 alt türlerinden](#subtypes-of-datetimev2) biri
|startIndex|**int** -varlığın başladığı noktada dizin.|
|endIndex|**int** -varlığın bittiği noktada dizin.|
|çözüm|`values`, Bir, iki veya dört [Çözümleme değerine](#values-of-resolution)sahip bir diziye sahiptir.|
|end|Bir saatin veya tarih aralığının bitiş değeri ile aynı biçimde `value` . Yalnızca,, `type` `daterange` veya ise `timerange` kullanılır `datetimerange`|

* * *

## <a name="subtypes-of-datetimev2"></a>DatetimeV2 alt türleri

**DatetimeV2** önceden oluşturulmuş varlık aşağıdaki alt türleri içerir ve aşağıdaki tabloda her birine örnek verilmiştir:
* `date`
* `time`
* `daterange`
* `timerange`
* `datetimerange`


## <a name="values-of-resolution"></a>Çözüm değerleri
* Dizideki tarih veya saat tam olarak belirtilmişse ve belirsiz ise dizide bir öğesi vardır.
* DatetimeV2 değeri belirsiz ise dizide iki öğe vardır. Belirsizlik belirli yıl, saat veya zaman aralığı eksikliğinden oluşur. Örnekler için [belirsiz tarihlere](#ambiguous-dates) bakın. Saat A.M. için belirsiz olduğunda ya da P.M., her iki değer de dahildir.
* Söylenişi belirsizliğe sahip iki öğe içeriyorsa dizide dört öğe vardır. Bu belirsizlik, aşağıdaki öğeleri içerir:
  * Yıl olarak belirsiz bir tarih veya tarih aralığı
  * Saat veya saat aralığı, saat olarak belirsiz görüntülenir. Örneğin, 3:00 Nisan 3.

Dizinin her öğesi `values` aşağıdaki alanlara sahip olabilir:

|Özellik adı|Özellik açıklaması|
|--|--|
|Timex|saat, tarih veya tarih aralığı, TIMEX3 için [ıso 8601 standardını](https://en.wikipedia.org/wiki/ISO_8601) Izleyen ve timeml dilini kullanarak ek açıklama için ÖZNITELIKLERINI izleyen TIMEX biçiminde ifade edilir.|
|alma|, gibi değerin nasıl kullanılacağını betimleyen dönem `before` `after` .|
|tür|Aşağıdaki öğelerden biri olabilecek alt tür: `datetime` , `date` ,, `time` `daterange` , `timerange` , `datetimerange` , `duration` , `set` .|
|değer|**Seçim.** Yyyy-aa-gg (Tarih), ss: DD: SS (saat) YYYY-AA-GG SS: DD: SS (DateTime) biçiminde bir DateTime nesnesi. `type`İse `duration` , değer saniye sayısıdır (süre) <br/> Yalnızca `type` `datetime` veya `date` , `time` veya ' Duration ise kullanılır.|

## <a name="valid-date-values"></a>Geçerli tarih değerleri

**DatetimeV2** aşağıdaki aralıklar arasındaki tarihleri destekler:

| Min | En yüksek değer |
|----------|-------------|
| 1 Ocak 1900   | 31 Aralık 2099 |

## <a name="ambiguous-dates"></a>Belirsiz tarihler

Tarih geçmişte veya gelecekte olabilir, LUYA her iki değer de sağlar. Yıl, yılı olmayan ayı ve tarihi içeren bir örnektir.

Örneğin, aşağıdaki utterance verildiğinde:

`May 2nd`

* Bugünün tarihi 3 Mayıs 2017 ise, Lu, değer olarak hem "2017-05-02" hem de "2018-05-02" sağlar.
* Bugünün tarihi 1 Mayıs 2017 olduğunda, Lu, değer olarak hem "2016-05-02" hem de "2017-05-02" sağlar.

Aşağıdaki örnek "Mayıs 2" varlığının çözünürlüğünü gösterir. Bu çözüm bugünün tarihinin 1 Mayıs 2017 arasında bir tarih olduğunu varsayar ve 1 Mayıs 2018 olabilir.
Alanında bulunan alanlar, `X` `timex` utterance 'te açıkça belirtilmeyen tarihin parçalarından oluşur.

## <a name="date-resolution-example"></a>Tarih çözümleme örneği


Aşağıdaki söylenişi ve bunun kısmen JSON yanıtı aşağıda gösterilmiştir.

`May 2nd`

#### <a name="v3-response"></a>[V3 yanıtı](#tab/2-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-05-02",
                    "resolution": [
                        {
                            "value": "2019-05-02"
                        },
                        {
                            "value": "2020-05-02"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[V3 ayrıntılı yanıt](#tab/2-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-05-02",
                    "resolution": [
                        {
                            "value": "2019-05-02"
                        },
                        {
                            "value": "2020-05-02"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "May 2nd",
                "startIndex": 0,
                "length": 7,
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

#### <a name="v2-response"></a>[V2 yanıtı](#tab/2-3)

```json
  "entities": [
    {
      "entity": "may 2nd",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2019-05-02"
          },
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2020-05-02"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="date-range-resolution-examples-for-numeric-date"></a>Sayısal Tarih için tarih aralığı çözümleme örnekleri

`datetimeV2`Varlık tarih ve saat aralıklarını ayıklar. `start`Ve `end` alanları aralığın başlangıcını ve sonunu belirtir. Söylenişi için `May 2nd to May 5th` , Lua, hem geçerli yıl hem de bir sonraki yıl için **terterange** değerleri sağlar. `timex`Alanda, `XXXX` değerler yılın belirsizliğin olduğunu gösterir. `P3D` zaman döneminin üç gün uzunluğunda olduğunu gösterir.

Aşağıdaki söylenişi ve bunun kısmen JSON yanıtı aşağıda gösterilmiştir.

`May 2nd to May 5th`

#### <a name="v3-response"></a>[V3 yanıtı](#tab/3-1)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
                    "resolution": [
                        {
                            "start": "2019-05-02",
                            "end": "2019-05-05"
                        },
                        {
                            "start": "2020-05-02",
                            "end": "2020-05-05"
                        }
                    ]
                }
            ]
        }
    ]
}
```


#### <a name="v3-verbose-response"></a>[V3 ayrıntılı yanıt](#tab/3-2)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
                    "resolution": [
                        {
                            "start": "2019-05-02",
                            "end": "2019-05-05"
                        },
                        {
                            "start": "2020-05-02",
                            "end": "2020-05-05"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.daterange",
                "text": "May 2nd to May 5th",
                "startIndex": 0,
                "length": 18,
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

#### <a name="v2-response"></a>[V2 yanıtı](#tab/3-3)

```json
"entities": [
    {
      "entity": "may 2nd to may 5th",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 17,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
            "type": "daterange",
            "start": "2019-05-02",
            "end": "2019-05-05"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="date-range-resolution-examples-for-day-of-week"></a>Tarih aralığı çözümleme için haftanın günü örnekleri

Aşağıdaki örnek, luın, söylenişi 'i çözümlemek için **datetimeV2** nasıl kullandığını gösterir `Tuesday to Thursday` . Bu örnekte, geçerli tarih 19 Haziran. LUO, ' dan önce gelen tarih aralıklarının her ikisi için de **meterange** değerleri içerir ve geçerli tarihi izler.

Aşağıdaki söylenişi ve bunun kısmen JSON yanıtı aşağıda gösterilmiştir.

`Tuesday to Thursday`

#### <a name="v3-response"></a>[V3 yanıtı](#tab/4-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
                    "resolution": [
                        {
                            "start": "2019-10-08",
                            "end": "2019-10-10"
                        },
                        {
                            "start": "2019-10-15",
                            "end": "2019-10-17"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[V3 ayrıntılı yanıt](#tab/4-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
                    "resolution": [
                        {
                            "start": "2019-10-08",
                            "end": "2019-10-10"
                        },
                        {
                            "start": "2019-10-15",
                            "end": "2019-10-17"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.daterange",
                "text": "Tuesday to Thursday",
                "startIndex": 0,
                "length": 19,
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

#### <a name="v2-response"></a>[V2 yanıtı](#tab/4-3)

```json
  "entities": [
    {
      "entity": "tuesday to thursday",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 19,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
            "type": "daterange",
            "start": "2019-04-30",
            "end": "2019-05-02"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="ambiguous-time"></a>Belirsiz saat
Değer dizisinde, saat veya zaman aralığı belirsiz ise iki zaman öğesi vardır. Belirsiz bir zaman olduğunda, değerlerin her ikisi de ve P.M. sayısıdır.

## <a name="time-range-resolution-example"></a>Zaman aralığı çözümleme örneği

DatetimeV2 JSON yanıtı API v3 'de değiştirildi. Aşağıdaki örnek, bir zaman aralığına sahip olan utterliği çözümlemek için LUIN **datetimeV2** 'in nasıl kullandığını gösterir.

API v2 'deki değişiklikler:
* `datetimeV2.timex.type` özellik üst düzeyde döndürüldüğünden artık döndürülmüyor `datetimev2.type` .
* `datetimeV2.value`Özelliği olarak yeniden adlandırıldı `datetimeV2.timex` .

Aşağıdaki söylenişi ve bunun kısmen JSON yanıtı aşağıda gösterilmiştir.

`from 6pm to 7pm`

#### <a name="v3-response"></a>[V3 yanıtı](#tab/5-1)

Aşağıdaki JSON `verbose` parametresi olarak ayarlanmıştır `false` :

```JSON

"entities": {
    "datetimeV2": [
        {
            "type": "timerange",
            "values": [
                {
                    "timex": "(T18,T19,PT1H)",
                    "resolution": [
                        {
                            "start": "18:00:00",
                            "end": "19:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 ayrıntılı yanıt](#tab/5-2)

Aşağıdaki JSON `verbose` parametresi olarak ayarlanmıştır `true` :

```json

"entities": {
    "datetimeV2": [
        {
            "type": "timerange",
            "values": [
                {
                    "timex": "(T18,T19,PT1H)",
                    "resolution": [
                        {
                            "start": "18:00:00",
                            "end": "19:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.timerange",
                "text": "from 6pm to 7pm",
                "startIndex": 0,
                "length": 15,
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
#### <a name="v2-response"></a>[V2 yanıtı](#tab/5-3)

```json
  "entities": [
    {
      "entity": "6pm to 7pm",
      "type": "builtin.datetimeV2.timerange",
      "startIndex": 0,
      "endIndex": 9,
      "resolution": {
        "values": [
          {
            "timex": "(T18,T19,PT1H)",
            "type": "timerange",
            "start": "18:00:00",
            "end": "19:00:00"
          }
        ]
      }
    }
  ]
```

* * *

## <a name="time-resolution-example"></a>Zaman çözümleme örneği

Aşağıdaki söylenişi ve bunun kısmen JSON yanıtı aşağıda gösterilmiştir.

`8am`

#### <a name="v3-response"></a>[V3 yanıtı](#tab/6-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "time",
            "values": [
                {
                    "timex": "T08",
                    "resolution": [
                        {
                            "value": "08:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 ayrıntılı yanıt](#tab/6-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "time",
            "values": [
                {
                    "timex": "T08",
                    "resolution": [
                        {
                            "value": "08:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.time",
                "text": "8am",
                "startIndex": 0,
                "length": 3,
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
#### <a name="v2-response"></a>[V2 yanıtı](#tab/6-3)

```json
"entities": [
  {
    "entity": "8am",
    "type": "builtin.datetimeV2.time",
    "startIndex": 0,
    "endIndex": 2,
    "resolution": {
      "values": [
        {
          "timex": "T08",
          "type": "time",
          "value": "08:00:00"
        }
      ]
    }
  }
]
```

* * *

## <a name="deprecated-prebuilt-datetime"></a>Kullanım dışı önceden oluşturulmuş tarih saat

`datetime`Önceden oluşturulmuş varlık kullanım dışıdır ve **datetimeV2** ile değiştirilmiştir.

İle değiştirmek `datetime` için `datetimeV2` aşağıdaki adımları izleyin:

1. LUSıS Web arabiriminin **varlıklar** bölmesini açın.
2. **Tarih saat** önceden oluşturulmuş varlığı silin.
3. **Önceden oluşturulmuş varlık Ekle** ' ye tıklayın
4. **DatetimeV2** öğesini seçin ve **Kaydet**' e tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

[V3 tahmin uç noktası](luis-migration-api-v3.md)hakkında daha fazla bilgi edinin.

[Boyut](luis-reference-prebuilt-dimension.md), [e-posta](luis-reference-prebuilt-email.md) varlıkları ve [sayı](luis-reference-prebuilt-number.md)hakkında bilgi edinin.

