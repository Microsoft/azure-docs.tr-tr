---
title: Dil desteği-Metin Analizi API'si
titleSuffix: Azure Cognitive Services
description: Metin Analizi API'si tarafından desteklenen doğal dillerin bir listesi. Bu makalede, her bir işlem için hangi dillerin desteklendiği açıklanmaktadır.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: aahi
ms.openlocfilehash: c0d91f803822e018f4363bb78d9138e2efe16f8a
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531429"
---
# <a name="text-analytics-api-v3-language-support"></a>Metin Analizi API'si v3 dil desteği 

#### <a name="sentiment-analysis"></a>[Yaklaşım Analizi](#tab/sentiment-analysis)

| Dil              | Dil kodu | v3 desteği | V3 model sürümü başlatılıyor: |              Notlar |
|:----------------------|:-------------:|:----------:|:--------------------------:|-------------------:|
| Basitleştirilmiş Çince    |   `zh-hans`   |     ✓      |         2019-10-01         | `zh` Ayrıca kabul edildi |
| Geleneksel Çince   |   `zh-hant`   |    ✓      |         2019-10-01         |                    |
| Felemenkçe                 |     `nl`      |     ✓      |         2019-10-01        |                    |
| İngilizce               |     `en`      |     ✓      |         2019-10-01         |                    |
| Fransızca                |     `fr`      |     ✓      |         2019-10-01         |                    |
| Almanca                |     `de`      |     ✓      |         2019-10-01         |                    |
| Hintçe                 |    `hi`       |     ✓      |         2020-04-01         |                    |
| İtalyanca               |     `it`      |     ✓      |         2019-10-01         |                    |
| Japonca              |     `ja`      |     ✓      |         2019-10-01         |                    |
| Korece                |     `ko`      |    ✓      |         2019-10-01         |                    |
| Norveççe (Bokmål)   |     `no`      |     ✓      |         2020-07-01         |                    |
| Portekizce (Brezilya)   |    `pt-BR`    |     ✓      |         2020-04-01         |                    |
| Portekizce (Portekiz) |    `pt-PT`    |     ✓      |         2019-10-01         | `pt` Ayrıca kabul edildi |
| İspanyolca               |     `es`      |     ✓      |         2019-10-01         |                    |
| Türkçe               |     `tr`      |     ✓       |         2020-07-01        |                    |

### <a name="opinion-mining-v31-preview-only"></a>Görüşme madenciliği (v 3.1-yalnızca Önizleme)

| Dil              | Dil kodu | V3 modeli sürümünden itibaren: |              Notlar |
|:----------------------|:-------------:|:------------------------------------:|-------------------:|
| İngilizce               |     `en`      |              2020-04-01              |                    |


#### <a name="named-entity-recognition-ner"></a>[Adlandırılmış varlık tanıma (NER)](#tab/named-entity-recognition)

> [!NOTE]
> * * İle işaretlenmiş diller için yalnızca "kişi", "konum" ve "kuruluş" varlıkları döndürülür.

| Dil               | Dil kodu | v3 desteği | V3 modeli sürümünden itibaren: |       Notlar        |
|:-----------------------|:-------------:|:----------:|:-------------------------------:|:------------------:|
| Arapça                 |     `ar`      |      ✓*    |               2019-10-01        |                    |
| Basitleştirilmiş Çince     |   `zh-hans`   |     ✓      |               2021-01-15        | `zh` Ayrıca kabul edildi |
| Geleneksel Çince   |   `zh-hant`   |     ✓*      |               2019-10-01        |                    |
| Çekçe                 |     `cs`      |     ✓*      |               2019-10-01        |                    |
| Danca                |     `da`      |     ✓*      |               2019-10-01        |                    |
| Felemenkçe                 |     `nl`      |     ✓*      |               2019-10-01        |                    |
| İngilizce                |     `en`      |     ✓      |               2019-10-01        |                    |
| Fince               |     `fi`      |     ✓*      |               2019-10-01        |                    |
| Fransızca                 |     `fr`      |     ✓      |               2021-01-15        |                    |
| Almanca                 |     `de`      |     ✓      |               2021-01-15        |                    |
| İbranice                |     `he`      |     ✓*      |               2019-10-01        |                    |
| Macarca             |     `hu`      |     ✓*      |               2019-10-01        |                    |
| İtalyanca               |     `it`      |     ✓       |               2021-01-15        |                    |
| Japonca              |     `ja`      |     ✓       |               2021-01-15        |                    |
| Korece                |     `ko`      |     ✓       |               2021-01-15        |                    |
| Norveççe (Bokmål)   |     `no`      |     ✓*      |               2019-10-01        | `nb` Ayrıca kabul edildi |
| Lehçe                |     `pl`      |     ✓*      |               2019-10-01        |                    |
| Portekizce (Brezilya)   |    `pt-BR`    |     ✓       |               2021-01-15        |                    |
| Portekizce (Portekiz) |    `pt-PT`    |     ✓       |               2021-01-15        | `pt` Ayrıca kabul edildi |
| Rusça              |     `ru`      |     ✓*       |               2019-10-01        |                    |
| İspanyolca               |     `es`      |     ✓       |               2020-04-01        |                    |
| İsveççe               |     `sv`      |     ✓*      |               2019-10-01        |                    |
| Türkçe               |     `tr`      |     ✓*      |               2019-10-01        |                    |

#### <a name="key-phrase-extraction"></a>[Anahtar tümceciği ayıklama](#tab/key-phrase-extraction)

| Dil              | Dil kodu |  v3 desteği | V3 model sürümü ile başlayarak kullanılabilir: |       Notlar        |
|:----------------------|:-------------:|:----------:|:-----------------------------------------:|:------------------:|
| Danca                |     `da`      |     ✓     |                2019-10-01                 |                    |
| Felemenkçe                 |     `nl`      |     ✓      |                2019-10-01                 |                    |
| İngilizce               |     `en`      |     ✓      |                2019-10-01                 |                    |
| Fince               |     `fi`      |     ✓      |                2019-10-01                 |                    |
| Fransızca                |     `fr`      |     ✓      |                2019-10-01                 |                    |
| Almanca                |     `de`      |     ✓      |                2019-10-01                 |                    |
| İtalyanca               |     `it`      |     ✓      |                2019-10-01                 |                    |
| Japonca              |     `ja`      |     ✓      |                2019-10-01                 |                    |
| Korece                |     `ko`      |     ✓      |                2019-10-01                 |                    |
| Norveççe (Bokmål)   |     `no`      |     ✓      |                2020-07-01                 | `nb` Ayrıca kabul edildi |
| Lehçe                |     `pl`      |    ✓      |                2019-10-01                 |                    |
| Portekizce (Brezilya)   |    `pt-BR`    |     ✓      |                2019-10-01                 |                    |
| Portekizce (Portekiz) |    `pt-PT`    |    ✓      |                2019-10-01                 | `pt` Ayrıca kabul edildi |
| Rusça               |     `ru`      |     ✓      |                2019-10-01                 |                    |
| İspanyolca               |     `es`      |     ✓      |                2019-10-01                 |                    |
| İsveççe               |     `sv`      |     ✓      |                2019-10-01                 |                    |

#### <a name="entity-linking"></a>[Varlık bağlama](#tab/entity-linking)

| Dil | Dil kodu |  v3 desteği | V3 model sürümü ile başlayarak kullanılabilir: | Notlar |
|:---------|:-------------:|:----------:|:-----------------------------------------:|:-----:|
| İngilizce  |     `en`      |     ✓      |                2019-10-01                 |       |
| İspanyolca  |     `es`      |    ✓      |                2019-10-01                 |       |

#### <a name="personally-identifiable-information-pii"></a>[Kişisel olarak tanımlanabilen bilgiler (PII)](#tab/pii)

| Dil               | Dil kodu | v3 desteği | V3 modeli sürümünden itibaren: |       Notlar        |
|:-----------------------|:-------------:|:----------:|:-------------------------------:|:------------------:|
| Basitleştirilmiş Çince     |   `zh-hans`   |     ✓      |               2021-01-15        | `zh` Ayrıca kabul edildi |
| İngilizce                |     `en`      |     ✓      |               2020-07-01        |                    |
| Fransızca                 |     `fr`      |     ✓      |               2021-01-15        |                    |
| Almanca                 |     `de`      |     ✓      |               2021-01-15        |                    |
| İtalyanca               |     `it`      |     ✓       |               2021-01-15        |                    |
| Japonca              |     `ja`      |     ✓       |               2021-01-15        |                    |
| Korece                |     `ko`      |     ✓       |               2021-01-15        |                    |
| Portekizce (Brezilya)   |    `pt-BR`    |     ✓       |               2021-01-15        |                    |
| Portekizce (Portekiz) |    `pt-PT`    |     ✓       |               2021-01-15        | `pt` Ayrıca kabul edildi |
| İspanyolca               |     `es`      |     ✓       |               2020-04-01        |                    |

#### <a name="language-detection"></a>[Dil Algılama](#tab/language-detection)

Metin Analizi API'si, çok çeşitli diller, çeşitler, diapacts ve bazı bölgesel/kültürel dillerini algılayabilir ve algılanan dilleri ad ve kodla döndürebilir. Metin Analizi Dil Algılama dil kodu parametreleri [ISO-639-1](https://www.iso.org/iso-639-language-codes.html) tanımlayıcılarıyla uyumlu olan [bcp-47](https://tools.ietf.org/html/bcp47) standardına uygundur. 

Daha az sıklıkta kullanılan bir dilde ifade ettiğiniz bir içeriğiniz varsa, bir kodu döndürüp döndürdüğünü görmek için Dil Algılama deneyebilirsiniz. Tespit edilemez dillerin yanıtı `unknown` .

| Dil | Dil Kodu | v3 desteği | V3 model sürümü ile başlayarak kullanılabilir: |
|:-|:-:|:-:|:-:|
|Afrikaner|`af`|✓|    |
|Arnavutça|`sq`|✓|    |
|Amhara Dili|`am`|✓|2021-01-05|
|Arapça|`ar`|✓|    |
|Ermenice|`hy`|✓|    |
|Assam|`as`|✓|2021-01-05|
|Dili|`az`|✓|2021-01-05|
|Baskça|`eu`|✓|    |
|Beyaz Rusça|`be`|✓|    |
|Bengali|`bn`|✓|    |
|Boşnakça|`bs`|✓|2020-09-01|
|Bulgarca|`bg`|✓|    |
|Birman dili|`my`|✓|    |
|Katalanca|`ca`|✓|    |
|Orta Khmer Dili|`km`|✓|    |
|Çince|`zh`|✓|    |
|Basitleştirilmiş Çince|`zh_chs`|✓|    |
|Geleneksel Çince|`zh_cht`|✓|    |
|Korsika dili|`co`|✓|2021-01-05|
|Hırvatça|`hr`|✓|    |
|Çekçe|`cs`|✓|    |
|Danca|`da`|✓|    |
|Dili|`prs`|✓|2020-09-01|
|Divehi|`dv`|✓|    |
|Felemenkçe|`nl`|✓|    |
|İngilizce|`en`|✓|    |
|Esperanto|`eo`|✓|    |
|Estonya Dili|`et`|✓|    |
|Fiji Adaları dili|`fj`|✓|2020-09-01|
|Fince|`fi`|✓|    |
|Fransızca|`fr`|✓|    |
|Galiçya Dili|`gl`|✓|    |
|Gürcüce|`ka`|✓|    |
|Almanca|`de`|✓|    |
|Yunanca|`el`|✓|    |
|Gucerat dili|`gu`|✓|    |
|Haiti|`ht`|✓|    |
|Hausa Dili|`ha`|✓|2021-01-05|
|İbranice|`he`|✓|    |
|Hintçe|`hi`|✓|    |
|Hmong DAW|`mww`|✓|2020-09-01|
|Macarca|`hu`|✓|    |
|İzlandaca|`is`|✓|    |
|İgbo Dili|`ig`|✓|2021-01-05|
|Endonezce|`id`|✓|    |
|Dili|`iu`|✓|    |
|İrlandaca|`ga`|✓|    |
|İtalyanca|`it`|✓|    |
|Japonca|`ja`|✓|    |
|Cava Dili|`jv`|✓|2021-01-05|
|Kannada dili|`kn`|✓|    |
|Kazakça|`kk`|✓|2020-09-01|
|Kinyarvanda dili|`rw`|✓|2021-01-05|
|Kirghiz|`ky`|✓|2021-01-05|
|Korece|`ko`|✓|    |
|Kürtçe|`ku`|✓|    |
|Laos|`lo`|✓|    |
|Tin|`la`|✓|    |
|Letonca|`lv`|✓|    |
|Litvanca|`lt`|✓|    |
|Lüksemburgca|`lb`|✓|2021-01-05|
|CA|`mk`|✓|    |
|Malgaşça|`mg`|✓|2020-09-01|
|Malayca|`ms`|✓|    |
|Malayalam dili|`ml`|✓|    |
|Maltaca|`mt`|✓|    |
|Maori dili|`mi`|✓|2020-09-01|
|Marathi|`mr`|✓|2020-09-01|
|Moğolca|`mn`|✓|2021-01-05|
|Nepal Dili|`ne`|✓|2021-01-05|
|Norveççe|`no`|✓|    |
|Norveççe Nynorsk|`nn`|✓|    |
|Oriya|`or`|✓|    |
|Pasht|`ps`|✓|    |
|Farsça|`fa`|✓|    |
|Lehçe|`pl`|✓|    |
|Portekizce|`pt`|✓|    |
|Pencap dili|`pa`|✓|    |
|Queretaro Otomi|`otq`|✓|2020-09-01|
|Rumence|`ro`|✓|    |
|Rusça|`ru`|✓|    |
|Samoan|`sm`|✓|2020-09-01|
|Sırpça|`sr`|✓|    |
|Shona dili|`sn`|✓|2021-01-05|
|Di|`sd`|✓|2021-01-05|
|Sinhali|`si`|✓|    |
|Slovakça|`sk`|✓|    |
|Slovence|`sl`|✓|    |
|Dili|`so`|✓|    |
|İspanyolca|`es`|✓|    |
|Sunda|`su`|✓|2021-01-05|
|Svahili dili|`sw`|✓|    |
|İsveççe|`sv`|✓|    |
|Tagalog|`tl`|✓|    |
|Tahiti dili|`ty`|✓|2020-09-01|
|İK|`tg`|✓|2021-01-05|
|Tamil dili|`ta`|✓|    |
|Tatarca|`tt`|✓|2021-01-05|
|Telugu dili|`te`|✓|    |
|Tayca|`th`|✓|    |
|Dili|`bo`|✓|2021-01-05|
|Tigrinya dili|`ti`|✓|2021-01-05|
|Tonga dili|`to`|✓|2020-09-01|
|Türkçe|`tr`|✓|2021-01-05|
|Türkmence|`tk`|✓|2021-01-05|
|Zosa dili|`xh`|✓|2021-01-05|
|Dili|`yo`|✓|2021-01-05|
|Zulu dili|`zu`|✓|2021-01-05|

---

## <a name="see-also"></a>Ayrıca bkz.

* [Metin Analizi API'si nedir?](overview.md)   
