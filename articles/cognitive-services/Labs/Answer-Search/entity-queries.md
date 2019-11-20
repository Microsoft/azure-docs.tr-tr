---
title: 'Hızlı Başlangıç: Proje yanıtı arama varlık sorgusu'
titlesuffix: Azure Cognitive Services
description: Yanıt Arama Projesindeki Varlık sorguları
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: quickstart
ms.date: 04/16/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: b1dbfcfe85a7847b593553bd06cbc22cc62d2e2e
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705461"
---
# <a name="quickstart-query-for-entities"></a>Hızlı Başlangıç: Varlıklar için sorgu

Sorguda bir kişi, yer veya nesne bilgisi istenmesi durumunda yanıtta `entities` bulunabilir.  Sorgular her zaman web sayfası döndürür ve [olgular](fact-queries.md) ve/veya [varlıklar](entity-queries.md) sorguya bağlı olarak değişir.

Varlıklar üç sorgu senaryosunu destekler: 
-   DominantEntity: Kullanıcının sorgusu ve amacıyla eşleşen tek bir varlık vardır. Örneğin Space Needle sorgusu bir DominantEntity senaryosudur. 
-   Disambiguation: Kullanıcının sorgusu ve amacıyla eşleşen birden fazla varlık vardır ve doğru varlığı kullanıcının seçmesi gerekir. Örneğin Game of Thrones sorgusu bir Disambiguation senaryosudur ve hem dizi hem de kitap döndürülür. 
-   List: Kullanıcının sorgusu ve amacıyla eşleşen birçok varlık vardır. Örneğin "List of endangered species" sorgusu bir List senaryosudur, satırlar ve hücreler halinde görüntülenmesi için biçimlendirilmiş tablosal değerler döndürür. 
 
Sorgu senaryosunu belirlemek için `entities` nesnesinin `queryScenario` alanını kullanın. Varlığın içerdiği veriler varlık türüne göre değişir. Varlıklar aynı temel bilgileri içeriyor olsa da turistik yerler veya kitaplar gibi bazı varlıklarda ek özellikler bulunabilir. Ek özellik içeren varlıklarda seri hale getirici tarafından kullanılan bir ipucu içeren `_type` alanı bulunur. Aşağıdaki varlıklar ek özelliklere sahiptir: 
-   Book 
-   MusicRecording 
-   Kişi 
-   Attraction 
 
Yanıtın içeriği varlık türünü belirlemek için Bill Gates sorgusunda gösterildiği gibi `entityTypeHints` alanını kullanın.
```
        },
        "description": "Bill Gates is an American business man and philanthropist, co-founder of Microsoft",
        "entityPresentationInfo": {
          "entityScenario": "DominantEntity",
          "entityTypeHints": [
            "Person"
          ]
        },
        "bingId": "6d7d66a7-2cb8-0ae9-637c-f81fd749dc9a"
      }
```
Aşağıda Space Needle sorgusu gösterilmiştir:
```
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=space+needle&mkt=en-us
```
Yanıt `entities` bilgisini içerir. `entityScenario` ve `entityTypeHints` alanlarına dikkat edin. 
```
  "entities": {
    "value": [
      {
        "id": "https://www.bingapis.com/api/v7/#Entities.0",
        "contractualRules": [
          {
            "_type": "ContractualRules/LicenseAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "license": {
              "name": "CC-BY-SA",
              "url": "https://creativecommons.org/licenses/by-sa/3.0/"
            },
            "licenseNotice": "Text under CC-BY-SA license"
          },
          {
            "_type": "ContractualRules/LinkAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "text": "Wikipedia",
            "url": "https://en.wikipedia.org/wiki/Space_Needle"
          },
          {
            "_type": "ContractualRules/MediaAttribution",
            "targetPropertyName": "image",
            "mustBeCloseToContent": true,
            "url": "https://en.wikipedia.org/wiki/Space_Needle"
          }
        ],
        "webSearchUrl": "https://www.bing.com/entityexplore?q\u003dSpace+Needle\u0026filters\u003dsid:%22f8dd5b08-206d-2554-6e4a-893f51f4de7e%22\u0026elv\u003dAXXfrEiqqD9r3GuelwApulpmymQx!ODfuQu*veOQHkvP0!Zbvi5F5tVcMSDJvDEWiQWwrdueYTtIszgj03oFQHykYYLYgq3q5!Sf00QxXGIS",
        "name": "Space Needle",
        "image": {
          "name": "Space Needle",
          "thumbnailUrl": "https://www.bing.com/th?id\u003dA15d336cf119b9b5c7e0ab37e271421d3\u0026w\u003d110\u0026h\u003d110\u0026c\u003d7\u0026rs\u003d1\u0026qlt\u003d80\u0026cdv\u003d1\u0026pid\u003d16.1",
          "provider": [
            {
              "_type": "Organization",
              "url": "https://en.wikipedia.org/wiki/Space_Needle"
            }
          ],
          "hostPageUrl": "https://upload.wikimedia.org/wikipedia/commons/2/23/Space_Needle_2011-07-04.jpg",
          "width": 110,
          "height": 110,
          "sourceWidth": 152,
          "sourceHeight": 300
        },
        "description": "The Space Needle is an observation tower in Seattle, Washington, a landmark of the Pacific Northwest, and an icon of Seattle. It was built in the Seattle Center for the 1962 World\u0027s Fair, which drew over 2.3 million visitors, when nearly 20,000 people a day used its elevators.",
        "entityPresentationInfo": {
          "entityScenario": "DominantEntity",
          "entityTypeHints": [
            "Attraction"
          ]
        },
        "bingId": "f8dd5b08-206d-2554-6e4a-893f51f4de7e"
      }
    ]
  },
```

Sorgu ilgiliyse bir liste döndürebilir.

**Sorgu:** Aşağıdaki sorgu, bir Endangered türler listesi bulur:

```
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=list+of+endangered+species

```

**Yanıtıyla** Yanıt, tablo değerleri olarak görüntülenmek üzere biçimlendirilen bir liste içerir:
```
  "facts": {
    "id": "https://www.bingapis.com/api/v7/#Facts",
    "contractualRules": [
      {
        "_type": "ContractualRules/LinkAttribution",
        "text": "www.worldwildlife.org/species/directory?direction=desc&sort=e…",
        "url": "https://www.worldwildlife.org/species/directory?direction=desc&sort=extinction_status"
      }
    ],
    "attributions": [
      {
        "providerDisplayName": "www.worldwildlife.org/species/directory?direction=desc&sort=e…",
        "seeMoreUrl": "https://www.worldwildlife.org/species/directory?direction=desc&sort=extinction_status"
      }
    ],
    "value": [
      {
        "subjectName": "Species Directory",
        "richCaption": {
          "_type": "StructuredValue/TabularData",
          "header": [
            "Common name",
            "Scientific name",
            "Conservation status ↓"
          ],
          "rows": [
            {
              "cells": [
                {
                  "_type": "Properties/Link",
                  "text": "Amur Leopard",
                  "url": "https://www.bing.com/https//www.worldwildlife.org/species/amur-leopard"
                },
                {
                  "text": "Panthera pardus orientalis"
                },
                {
                  "text": "Critically Endangered"
                }
              ]
            },
            {
              "cells": [
                {
                  "_type": "Properties/Link",
                  "text": "Black Rhino",
                  "url": "https://www.bing.com/https//www.worldwildlife.org/species/black-rhino"
                },
                {
                  "text": "Diceros bicornis"
                },
                {
                  "text": "Critically Endangered"
                }
              ]
            },
            {
              "cells": [
                {
                  "_type": "Properties/Link",
                  "text": "Bornean Orangutan",
                  "url": "https://www.bing.com/https//www.worldwildlife.org/species/bornean-orangutan"
                },
                {
                  "text": "Pongo pygmaeus"
                },
                {
                  "text": "Critically Endangered"
                }
              ]
            },
            {
              "cells": [
                {
                  "_type": "Properties/Link",
                  "text": "Cross River Gorilla",
                  "url": "https://www.bing.com/https//www.worldwildlife.org/species/cross-river-gorilla"
                },
                {
                  "text": "Gorilla gorilla diehli"
                },
                {
                  "text": "Critically Endangered"
                }
              ]
            }
          ],
          "seeMoreUrl": {
            "text": "46 more rows",
            "url": "https://www.worldwildlife.org/species/directory?direction=desc&sort=extinction_status"
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
