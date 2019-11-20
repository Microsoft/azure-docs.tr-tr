---
title: Dil desteği-Translator Metin Çevirisi API'si
titleSuffix: Azure Cognitive Services
description: Translator Metin Çevirisi API'si, sinir makine çevirisi (NMT) kullanarak metin çevirisi için aşağıdaki dilleri destekler.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 5e96ced1bd87a0ed8be19bf4e07f7b0d9b319aad
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837323"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>Translator Metin Çevirisi API'si için dil ve bölge desteği

Translator Metin Çevirisi API'si metin çevirisi için aşağıdaki dilleri destekler. Sinir makine çevirisi (NMT), yüksek kaliteli AI destekli makine çevirileri için yeni standarttır ve bir sinir sistemi kullanılabilir olduğunda Translator Metin Çevirisi API'si v3 kullanılarak varsayılan olarak kullanılabilir.

[Makine çevirisinin nasıl çalıştığı hakkında daha fazla bilgi edinin](https://www.microsoft.com/translator/mt.aspx)

## <a name="translation"></a>Çeviri

**V2 Translator API 'SI**

> [!NOTE]
> V2, 30 Nisan 2018 tarihinde kullanımdan kaldırılmıştır. Yalnızca v3 'de bulunan yeni işlevsellikten yararlanmak için uygulamalarınızı v3 'e geçirin.

* Yalnızca istatistiksel: Bu dil için kullanılabilir sinir sistemi yok.
* Sinir kullanılabilir: bir sinir sistemi kullanılabilir. Sinir sistemine erişmek için `category=generalnn` parametresini kullanın.
* Sinir varsayılan: sinir, varsayılan çeviri sistemidir. Microsoft Translator hub ile kullanmak üzere istatistiksel sisteme erişmek için `category=smt` parametresini kullanın.
* Yalnızca sinir: yalnızca sinir çeviri kullanılabilir.

**V3 Translator API 'si** V3 Translator API 'SI varsayılan olarak sinir ve istatistiksel sistemler yalnızca sinir sistemi yoksa kullanılabilir.

> [!NOTE]
> Şu anda, sinir dillerinin bir alt kümesi özel çevirmende bulunabilir ve bunlar yavaş yavaş bir şekilde ekliyoruz. [Özel çevirmende mevcut olan dilleri görüntüleyin](#customization).

|Dil|  Dil kodu|  V2 APı 'SI| V3 APı 'SI|
|:-----|:-----:|:-----|:-----|
|Afrikaner| `af`    |Yalnızca istatistiksel|  Nöral|
|Arapça|    `ar`    |Sinir kullanılabilir|  Nöral|
|Bangla|    `bn`    |Sinir kullanılabilir|  Nöral|
|Boşnakça (Latin)|   `bs`    |Sinir kullanılabilir|  Nöral|
|Bulgarca| `bg`    |Sinir kullanılabilir|  Nöral|
|Cantonetıcı (Geleneksel)|   `yue`   |Yalnızca istatistiksel|  Ki|
|Katalanca|   `ca`    |Yalnızca istatistiksel|  Ki|
|Basitleştirilmiş Çince|    `zh-Hans`   |Sinir varsayılan |Nöral|
|Geleneksel Çince|   `zh-Hant`   |Sinir varsayılan |Nöral|
|Hırvatça|  `hr`    |Sinir kullanılabilir|  Nöral|
|Çekçe| `cs`    |Sinir kullanılabilir|  Nöral|
|Danca|    `da`    |Sinir kullanılabilir   |Nöral|
|Hollanda dili| `nl`    |Sinir kullanılabilir|  Nöral|
|Türkçe|   `en`    |Sinir kullanılabilir|  Nöral|
|Estonca|  `et`    |Sinir kullanılabilir|  Nöral|
|Fiji Adaları dili|    `fj`    |Yalnızca istatistiksel|  Ki|
|Filipin dili|  `fil`   |Yalnızca istatistiksel|  Ki|
|Fince|   `fi`    |Sinir kullanılabilir|  Nöral|
|Fransızca|    `fr`    |Sinir kullanılabilir|  Nöral|
|Almanca|    `de`    |Sinir kullanılabilir|  Nöral|
|Yunanca| `el`    |Sinir kullanılabilir|  Nöral|
|Haian Creole|    `ht`    |Yalnızca istatistiksel   |Ki|
|İbranice |`he`   |Sinir kullanılabilir   |Nöral|
|Hintçe| `hi`    |Sinir varsayılan|    Nöral|
|Hmong DAW| `mww`   |Yalnızca istatistiksel|  Ki|
|Macarca| `hu`    |Sinir kullanılabilir|  Nöral|
|İzlanda dili| `is`    |Yalnızca sinir|   Nöral|
|Endonezya dili|    `id`    |Yalnızca istatistiksel|  Ki|
|İtalyanca|   `it`    |Sinir kullanılabilir|  Nöral|
|Japonca|  `ja`    |Sinir kullanılabilir|  Nöral|
|Svahili dili| `sw`    |Yalnızca istatistiksel|  Ki|
|Klingon|   `tlh`   |Yalnızca istatistiksel|  Ki|
|Klingon (plqaD)|   `tlh-Qaak`  |Yalnızca istatistiksel|  Ki|
|Korece |`ko`   |Sinir kullanılabilir|  Nöral|
|Letonca|   `lv`    |Sinir kullanılabilir|  Nöral|
|Litvanca|    `lt`    |Sinir kullanılabilir|  Nöral|
|Malgaşça|  `mg`    |Yalnızca istatistiksel|  Ki|
|Malay dili| `ms`    |Yalnızca istatistiksel   |Ki|
|Malta dili|   `mt`    |Yalnızca istatistiksel|  Ki|
|Norveççe| `nb`    |Sinir kullanılabilir|  Nöral|
|Farsça|   `fa`    |Sinir kullanılabilir|  Nöral|
|Lehçe|    `pl`    |Sinir kullanılabilir|  Nöral|
|Portekizce|    `pt`    |Sinir kullanılabilir|  Nöral|
|Queretaro Otomi|   `otq`   |Yalnızca istatistiksel|  Ki|
|Rumence|  `ro`    |Sinir kullanılabilir|  Nöral|
|Rusça|   `ru`    |Sinir kullanılabilir|  Nöral|
|Samoan|    `sm`    |Yalnızca istatistiksel|  Ki|
|Sırpça (Kiril)|    `sr-Cyrl`   |Yalnızca istatistiksel|  Ki|
|Sırpça (Latin)|   `sr-Latn`   |Yalnızca istatistiksel   |Ki|
|Slovakça|    `sk`    |Sinir kullanılabilir|  Nöral|
|Slovence| `sl`    |Sinir kullanılabilir|  Nöral|
|İspanyolca|   `es`    |Sinir kullanılabilir|  Nöral|
|İsveççe|   `sv`    |Sinir kullanılabilir   |Nöral|
|Tahiti dili|  `ty`    |Yalnızca istatistiksel|  Ki|
|Tamil dili| `ta`    |Yalnızca istatistiksel|  Ki|
|Telugu dili|    `te`    |Yalnızca sinir|   Nöral|
|Tay dili|  `th`    |Sinir kullanılabilir|  Nöral|
|Tonga dili|    `to`    |Yalnızca istatistiksel|  Ki|
|Türkçe|   `tr`    |Sinir kullanılabilir   |Nöral|
|Ukrayna dili| `uk`    |Sinir kullanılabilir|  Nöral|
|Urduca|  `ur`    |Yalnızca istatistiksel|  Ki|
|Vietnam dili|    `vi`    |Sinir kullanılabilir|  Nöral|
|Galce| `cy`    |Sinir kullanılabilir|  Nöral|
|Yucatec Maya|  `yua`   |Yalnızca istatistiksel|  Ki|

## <a name="transliteration"></a>Alfabe çevirisi

Alfabede bulunan yöntem aşağıdaki dilleri destekler. "To/from", "<-->", dilin listelenen betiklerden veya bunlardan herhangi birine dönüştürülmüş olduğunu gösterir. "-->", Dilin yalnızca bir betikten diğerine dönüştürülmüş olduğunu gösterir.

| Dil    | Dil kodu | Betik | /Kimden | Betik|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Arapça | `ar` | Arapça `Arab` | <--> | Latin `Latn` |
|Bangla  | `bn` | Bangla `Beng` | <--> | Latin `Latn` |
| Çince (Basitleştirilmiş) | `zh-Hans` | Basitleştirilmiş Çince `Hans`| <--> | Latin `Latn` |
| Çince (Basitleştirilmiş) | `zh-Hans` | Basitleştirilmiş Çince `Hans`| <--> | Geleneksel Çince `Hant`|
| Çince (Geleneksel) | `zh-Hant` | Geleneksel Çince `Hant`| <--> | Latin `Latn` |
| Çince (Geleneksel) | `zh-Hant` | Geleneksel Çince `Hant`| <--> | Basitleştirilmiş Çince `Hans` |
| Gucerat dili | `gu`  | Gucerat dili `Gujr` | --> | Latin `Latn` |
| İbranice | `he` | İbranice `Hebr` | <--> | Latin `Latn` |
| Hintçe | `hi` | Devanagari `Deva` | <--> | Latin `Latn` |
| Japonca | `ja` | Japonca `Jpan` | <--> | Latin `Latn` |
| Kannada dili | `kn` | Kannada dili `Knda` | --> | Latin `Latn` |
| Malayalam dili | `ml` | Malayalam dili `Mlym` | --> | Latin `Latn` |
| Marathi | `mr` | Devanagari `Deva` | --> | Latin `Latn` |
| Oriya | `or` | Oriya dili `Orya` | <--> | Latin `Latn` |
| Pencap dili | `pa` | Gurmukhi `Guru`  | <--> | Latin `Latn`  |
| Sırpça (Kiril) | `sr-Cyrl` | Kiril `Cyrl`  | --> | Latin `Latn` |
| Sırpça (Latin) | `sr-Latn` | Latin `Latn` | --> | Kiril `Cyrl`|
| Tamil dili | `ta` | Tamil dili `Taml` | --> | Latin `Latn` |
| Telugu dili | `te` | Telugu dili `Telu` | --> | Latin `Latn` |
| Tay dili | `th` | Tay dili `Thai` | <--> | Latin `Latn` |

## <a name="dictionary"></a>Sözlük

Sözlük, arama ve örnekler yöntemlerini kullanarak Ingilizce 'ye veya Ingilizce 'ye kadar aşağıdaki dilleri destekler.

| Dil    | Dil kodu |
|:----------- |:-------------:|
| Afrikaner      | `af`          |
| Arapça       | `ar`          |
| Bangla      | `bn`          |
| Boşnakça (Latin)      | `bs`          |
| Bulgarca      | `bg`          |
| Katalanca      | `ca`          |
| Basitleştirilmiş Çince      | `zh-Hans`          |
| Hırvatça      | `hr`          |
| Çekçe      | `cs`          |
| Danca      | `da`          |
| Hollanda dili      | `nl`          |
| Estonca      | `et`          |
| Fince      | `fi`          |
| Fransızca      | `fr`          |
| Almanca      | `de`          |
| Yunanca      | `el`          |
| Haian Creole      | `ht`          |
| İbranice      | `he`          |
| Hintçe      | `hi`          |
| Hmong DAW      | `mww`          |
| Macarca      | `hu`          |
| İzlanda dili    | `is`  |
| Endonezya dili      | `id`          |
| İtalyanca      | `it`          |
| Japonca      | `ja`          |
| Svahili dili      | `sw`          |
| Klingon      | `tlh`          |
| Korece      | `ko`          |
| Letonca      | `lv`          |
| Litvanca      | `lt`          |
| Malay dili      | `ms`          |
| Malta dili      | `mt`          |
| Norveççe      | `nb`          |
| Farsça      | `fa`          |
| Lehçe      | `pl`          |
| Portekizce      | `pt`          |
| Rumence      | `ro`          |
| Rusça      | `ru`          |
| Sırpça (Latin)      | `sr-Latn`          |
| Slovakça     | `sk`          |
| Slovence      | `sl`          |
| İspanyolca      | `es`          |
| İsveççe      | `sv`          |
| Tamil dili      | `ta`          |
| Tay dili      | `th`          |
| Türkçe      | `tr`          |
| Ukrayna dili      | `uk`          |
| Urduca      | `ur`          |
| Vietnam dili      | `vi`          |
| Galce      | `cy`          |

## <a name="detect"></a>Algılama

Translator Metin Çevirisi API'si, çeviri ve alfabede kullanılabilen tüm dilleri algılar.


## <a name="access-the-translator-text-api-language-list-programmatically"></a>Translator Metin Çevirisi API'si dil listesine programlı olarak erişin

Diller metodunu kullanarak Translator Metin Çevirisi API'si v 3.0 için desteklenen dillerin bir listesini alabilirsiniz. Liste özelliği, dil kodu ve dil adı ile Ingilizce veya desteklenen başka bir dilde görünüm görüntüleyebilirsiniz. Yeni diller kullanılabilir hale getirildiğinden bu liste Microsoft Translator hizmeti tarafından otomatik olarak güncelleştirilir.

[Dilleri görüntüle işlem başvurusu belgeleri](reference/v3-0-languages.md)

## <a name="customization"></a>Özelleştirme

Aşağıdaki diller, [özel çevirici](https://aka.ms/CustomTranslator)kullanılarak Ingilizce veya İngilizce arasında özelleştirme için kullanılabilir.

| Dil    | Dil kodu |
|:----------- |:-------------:|
| Arapça       | `ar`          |
| Bangla      | `bn`          |
| Boşnakça (Latin)      | `bs`          |
| Bulgarca      | `bg`          |
| Basitleştirilmiş Çince      | `zh-Hans`          |
|Geleneksel Çince|   `zh-Hant`   |
| Hırvatça      | `hr`          |
| Çekçe      | `cs`          |
| Danca      | `da`          |
| Hollanda dili      | `nl`          |
| Türkçe    | `en`     |
| Estonca      | `et`          |
| Fince      | `fi`          |
| Fransızca      | `fr`          |
| Almanca      | `de`          |
| Yunanca      | `el`          |
| İbranice      | `he`          |
| Hintçe      | `hi`          |
| Macarca      | `hu`          |
| İzlanda dili | `is` |
| Endonezya dili|   `id`    |
| İrlanda dili | `ga`  |
| İtalyanca      | `it`          |
| Japonca      | `ja`          |
|Svahili dili| `sw`    |
| Korece      | `ko`          |
| Letonca      | `lv`          |
| Litvanca      | `lt`          |
|Malgaşça|  `mg`    |
| Norveççe      | `nb`          |
| Farsça      | `fa`          |
| Lehçe      | `pl`          |
| Portekizce      | `pt`          |
| Rumence      | `ro`          |
| Rusça      | `ru`          |
|Samoan|    `sm`    |
| Sırpça (Latin)      | `sr-Latn`          |
| Slovakça     | `sk`          |
| Slovence      | `sl`          |
| İspanyolca      | `es`          |
| İsveççe      | `sv`          |
| Tay dili      | `th`          |
| Türkçe      | `tr`          |
| Ukrayna dili      | `uk`          |
| Vietnam dili      | `vi`          |
| Galce | `cy` |

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Microsoft Translator Web sitesindeki listeye erişin

Microsoft Translator Web sitesinde, dillere hızlı bir bakış için Translator Metin Çevirisi ve konuşma API 'Leri tarafından desteklenen tüm diller gösterilmektedir. Bu liste, dil kodları gibi geliştiriciye özgü bilgiler içermez.

[Dillerin listesine bakın](https://www.microsoft.com/translator/languages.aspx)
