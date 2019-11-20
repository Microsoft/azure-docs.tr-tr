---
title: 'Hızlı Başlangıç: Proje yanıtı arama olgu sorgusu'
titlesuffix: Azure Cognitive Services
description: Yanıt Arama Projesindeki olgu sorguları
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: quickstart
ms.date: 04/16/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: 05b870fb0d836a89be2a7212436c5c6f91982837
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705445"
---
# <a name="quickstart-query-for-facts"></a>Hızlı Başlangıç: Olgu sorgusu

Tarih veya tanımlanabilir bilgi gibi bir olgu sorgusunda yanıt `facts` öğelerini içerebilir. Olgu yanıtları, web belgelerindeki paragraflardan alınan ilgili sorguları içerir.  Bu sorgular her zaman web sayfası döndürür ve [olgular](fact-queries.md) ve/veya [varlıklar](entity-queries.md) sorguya bağlı olarak değişir.

Sevgililer+2016 veya ramazan+başlangıcı gibi sorgular, tarihle ilgili sorgular olarak kabul edilir. Bing, sorgunun tarihle ilgili olduğunu belirlediğinde yanıtta bir `facts` öğesi bulunur.

Aşağıdaki örnekte tarihle ilgili bir `facts` yanıtı gösterilmiştir.

**Sorgu:**
```
https://labsportalppe.azure-api.net/answerSearch/v7.0/search?q=valentines+2016

```

**Yanıtıyla** `subjectName` Alan, olgu görüntülenirken etiket olarak kullanabileceğiniz Kullanıcı sorgusunun görüntü sürümünü içerir. Sorgu dizesi Sevgililer+2016 şeklindeyse Bing bunu Sevgililer Günü 2016 şeklinde değiştirebilir. Açıklama alanı olguyu içerir.

```
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "valentines 2016"
    },
    "facts" : {
        "id" : "https:\/\/www.bingapis.com\/api\/v7\/#Facts",
        "value" : [{
            "description" : "Valentine's Day was on Sunday, February 14, 2016.",
            "subjectName" : "Valentine's Day 2016"
        }]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "Facts",
                "value" : {
                    "id" : "https:\/\/www.bingapis.com\/api\/v7\/knowledge\/#Facts"
                }
            }]
        }
    }
}

```

"Gökyüzü neden mavi?" sorgusu bilgiyle ilgili bir yanıt döndürür.

**Sorgu:**

```
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=why+is+the+sky+blue

```

**Yanıtıyla** `value/description` Alan, sorgu tarafından istenen bilgi veya bilgileri içerir.

```
  "facts": {
    "id": "https://www.bingapis.com/api/v7/#Facts",
    "contractualRules": [
      {
        "_type": "ContractualRules/LinkAttribution",
        "text": "en.wikipedia.org/wiki/Diffuse_sky_radiation",
        "url": "https://en.wikipedia.org/wiki/Diffuse_sky_radiation"
      },
      {
        "_type": "ContractualRules/LinkAttribution",
        "text": "spaceplace.nasa.gov/blue-sky/en/",
        "url": "https://spaceplace.nasa.gov/blue-sky/en/"
      }
    ],
    "attributions": [
      {
        "providerDisplayName": "en.wikipedia.org/wiki/Diffuse_sky_radiation",
        "seeMoreUrl": "https://en.wikipedia.org/wiki/Diffuse_sky_radiation"
      },
      {
        "providerDisplayName": "spaceplace.nasa.gov/blue-sky/en/",
        "seeMoreUrl": "https://spaceplace.nasa.gov/blue-sky/en/"
      }
    ],
    "value": [
      {
        "image": {
          "webSearchUrl": "https://www.bing.com/images/search?view=detailv2&FORM=OIIRPO&q=&id=B632272C4E934D7F0B18790300B2DE34E7676C7A&simid=608045681196075791&iss=eqna",
          "name": "Why is the sky blue in the sky?",
          "thumbnailUrl": "https://www.bing.com/th?id=ODE.858093005&w=140&h=140&c=8&rs=1&qlt=100&pid=3.1",
          "isFamilyFriendly": true
        },
        "description": "When sunlight reaches Earth's atmosphere, it is scattered in all directions by all the gases and particles in the air. Blue is scattered by air molecules more than other colors because it travels as shorter, smaller waves. This is why we see a blue sky most of the time.\n\nCloser to the horizon, the sky fades to a lighter blue or white. The sunlight reaching us from low in the sky has passed through even more air than the sunlight reaching us from overhead. As the sunlight has passed through all this air, the air molecules have scattered and rescattered the blue light many times in many directions. Also, the surface of Earth has reflected and scattered the light. All this scattering mixes the colors together again so we see more white and less blue.",
        "subjectName": "Why is the sky blue in the sky?",
        "primaryData": [
          "The atmosphere scatters more blue light"
        ]
      }
    ]
  },

```

## <a name="tabular-data"></a>Tablo verileri
Bazı durumlarda olgular `_type: StructuredValue/TabularData` olarak döndürülebilir. Aşağıdaki sorgu çay ve kahve hakkındaki karşılaştırma bilgilerini içeren tablo verilerini alır.

```
https://labsportalppe.azure-api.net/answerSearch/v7.0/search?q=coffee+vs+tea&mkt=en-us

```
`facts` sonuçlarında aşağıdaki satırlar ve hücreler bulunur:
```
    "value": [
      {
        "subjectName": "Coffee vs. Tea",
        "richCaption": {
          "_type": "StructuredValue/TabularData",
          "header": [
            "",
            "Coffee",
            "Tea"
          ],
          "rows": [
            {
              "cells": [
                {
                  "text": "Part of plant used"
                },
                {
                  "text": "Bean"
                },
                {
                  "text": "Leaf"
                }
              ]
            },
            {
              "cells": [
                {
                  "text": "Caffeine Content"
                },
                {
                  "text": "80-185 mg per 8 ounce cup depending upon the brew and the..."
                },
                {
                  "text": "15 - 70 mg per cup"
                }
              ]
            },
            {
              "cells": [
                {
                  "text": "Types of Consumption"
                },
                {
                  "text": "Drip Coffee"
                },
                {
                  "text": "White Tea"
                }
              ]
            },
            {
              "cells": [
                {
                  "text": "Additions"
                },
                {
                  "text": "Sugar"
                },
                {
                  "text": "Milk"
                }
              ]
            }
          ],
          "seeMoreUrl": {
            "text": "8 more rows",
            "url": "https://www.diffen.com/difference/Coffee_vs_Tea"
          }
        }
      }
    ]
  },

```

## <a name="next-steps"></a>Sonraki adımlar
- [C# hızlı başlangıcı](c-sharp-quickstart.md)
- [Java hızlı başlangıcı](java-quickstart.md)
- [Node hızlı başlangıcı](node-quickstart.md)
- [Python hızlı başlangıcı](python-quickstart.md)
