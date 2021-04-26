---
title: Dil desteği-çevirici
titleSuffix: Azure Cognitive Services
description: Bilişsel hizmetler çevirmeni, sinir makine çevirisi (NMT) kullanarak metin çevirisi için aşağıdaki dilleri destekler.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 06/10/2020
ms.author: lajanuar
ms.openlocfilehash: a195567117378a118b9bea8d15a7cb0a661d284a
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107303534"
---
# <a name="language-and-region-support-for-text-and-speech-translation"></a>Metin ve konuşma çevirisi için dil ve bölge desteği

90 metin çeviri dili ve diatilerinden herhangi birine dönüştürmek için çeviriciyi kullanın. Sinir makine çevirisi (NMT), yüksek kaliteli AI destekli makine çevirileri için yeni standarttır ve bir sinir sistemi kullanılabilir olduğunda varsayılan olarak v3 of Translator kullanılarak kullanılabilir.

Ayrıca, kendi iş ve sektöründe kullanılan terminolojiyi anlayan sinir çeviri sistemleri oluşturmak ve uygulamanıza konuşma çevirisi eklemek için Microsoft konuşma hizmeti ile birlikte çeviriciyi de kullanabilirsiniz.

[Makine çevirisinin nasıl çalıştığı hakkında daha fazla bilgi edinin](https://www.microsoft.com/translator/mt.aspx)

## <a name="text-translation"></a>Metin çevirisi
Metin çevirisi, Translator 'da kullanılabilen dillerden veya çeviri işlemi kullanılarak kullanılabilir. Ayrıca API, Algıla işlemini kullanarak dil algılama, alfabede kullanılan bir işlem ve sözlük arama ve sözlük örnekleri işlemlerini kullanarak iki dilli sözlük de sunar. Bu işlemlerin her biri için kullanılabilen diller aşağıda listelenmiştir. 

### <a name="translate"></a>Çeviri

Translator metin çevirisi için aşağıdaki dilleri destekler. 

[Çeviri işlemi başvuru belgelerini görüntüle](reference/v3-0-translate.md)

| Dil | Dil kodu |
|:-|:-:|
| Afrikaner | `af` |
| Arnavutça | `sq` |
| Amhara Dili | `am` |
| Arapça | `ar` |
| Ermenice | `hy` |
| Assam | `as` |
| Dili | `az` |
| Bangla | `bn` |
| Boşnakça (Latin) | `bs` |
| Bulgarca | `bg` |
| Cantonetıcı (Geleneksel) | `yue` |
| Katalanca | `ca` |
| Basitleştirilmiş Çince | `zh-Hans` |
| Geleneksel Çince | `zh-Hant` |
| Hırvatça | `hr` |
| Çekçe | `cs` |
| Danca | `da` |
| Dili | `prs` |
| Felemenkçe | `nl` |
| İngilizce | `en` |
| Estonya Dili | `et` |
| Fiji Adaları dili | `fj` |
| Filipino | `fil` |
| Fince | `fi` |
| Fransızca | `fr` |
| Fransızca (Kanada) | `fr-ca` |
| Almanca | `de` |
| Yunanca | `el` |
| Gucerat dili | `gu` |
| Haian Creole | `ht` |
| İbranice | `he` |
| Hintçe | `hi` |
| Hmong DAW | `mww` |
| Macarca | `hu` |
| İzlandaca | `is` |
| Endonezce | `id` |
| Dili | `iu` |
| İrlandaca | `ga` |
| İtalyanca | `it` |
| Japonca | `ja` |
| Kannada dili | `kn` |
| Kazakça | `kk` |
| Khmer | `km` |
| Klingon | `tlh-Latn` |
| Klingon (plqaD) | `tlh-Piqd` |
| Korece | `ko` |
| Kürtçe (orta) | `ku` |
| Kürtçe (Kuzey) | `kmr` |
| Laos | `lo` |
| Letonca | `lv` |
| Litvanca | `lt` |
| Malgaşça | `mg` |
| Malayca | `ms` |
| Malayalam dili | `ml` |
| Maltaca | `mt` |
| Maori dili | `mi` |
| Marathi | `mr` |
| Myanmar | `my` |
| Nepal Dili | `ne` |
| Norveççe | `nb` |
| Odia | `or` |
| Peştuca | `ps` |
| Farsça | `fa` |
| Lehçe | `pl` |
| Portekizce (Brezilya) | `pt` |
| Portekizce (Portekiz) | `pt-pt` |
| Pencap dili | `pa` |
| Queretaro Otomi | `otq` |
| Rumence | `ro` |
| Rusça | `ru` |
| Samoan | `sm` |
| Sırpça (Kiril) | `sr-Cyrl` |
| Sırpça (Latin) | `sr-Latn` |
| Slovakça | `sk` |
| Slovence | `sl` |
| İspanyolca | `es` |
| Svahili dili | `sw` |
| İsveççe | `sv` |
| Tahiti dili | `ty` |
| Tamil dili | `ta` |
| Telugu dili | `te` |
| Tayca | `th` |
| Tigrinya dili | `ti` |
| Tonga dili | `to` |
| Türkçe | `tr` |
| Ukraynaca | `uk` |
| Urduca | `ur` |
| Vietnamca | `vi` |
| Galce | `cy` |
| Yucatec Maya | `yua` |

> [!NOTE]
> Dil kodu `pt` Varsayılan olarak `pt-br` , Portekizce (Brezilya) olur.

### <a name="detect"></a>Algılama

Çevirmen, çeviri ve alfabe için aşağıdaki dilleri algılar.

[İşlem başvurusunu Algıla belgelerini görüntüle](reference/v3-0-detect.md)

| Dil | Dil kodu |
|:-|:-:|
| Afrikaner | `af` |
| Arnavutça | `sq` |
| Arapça | `ar` |
| Bulgarca | `bg` |
| Katalanca | `ca` |
| Basitleştirilmiş Çince | `zh-Hans` |
| Geleneksel Çince | `zh-Hant` |
| Hırvatça | `hr` |
| Çekçe | `cs` |
| Danca | `da` |
| Felemenkçe | `nl` |
| İngilizce | `en` |
| Estonya Dili | `et` |
| Fince | `fi` |
| Fransızca | `fr` |
| Almanca | `de` |
| Yunanca | `el` |
| Gucerat dili | `gu` |
| Haian Creole | `ht` |
| İbranice | `he` |
| Hintçe | `hi` |
| Macarca | `hu` |
| İzlandaca | `is` |
| Endonezce | `id` |
| Dili | `iu` |
| İrlandaca | `ga` |
| İtalyanca | `it` |
| Japonca | `ja` |
| Klingon | `tlh-Latn` |
| Korece | `ko` |
| Kürtçe (orta) | `ku-Arab` |
| Letonca | `lv` |
| Litvanca | `lt` |
| Malayca | `ms` |
| Maltaca | `mt` |
| Norveççe | `nb` |
| Peştuca | `ps` |
| Farsça | `fa` |
| Lehçe | `pl` |
| Portekizce | `pt` |
| Rumence | `ro` |
| Rusça | `ru` |
| Sırpça (Kiril) | `sr-Cyrl` |
| Sırpça (Latin) | `sr-Latn` |
| Slovakça | `sk` |
| Slovence | `sl` |
| İspanyolca | `es` |
| Svahili dili | `sw` |
| İsveççe | `sv` |
| Tahiti dili | `ty` |
| Tayca | `th` |
| Türkçe | `tr` |
| Ukraynaca | `uk` |
| Urduca | `ur` |
| Vietnamca | `vi` |
| Galce | `cy` |
| Yucatec Maya | `yua` |

### <a name="transliterate"></a>Karakter Dönüştürme

Alfabede bulunan yöntem aşağıdaki dilleri destekler. "To/from", "<-->", dilin listelenen betiklerden veya bunlardan herhangi birine dönüştürülmüş olduğunu gösterir. "-->", dilin yalnızca bir betikten diğerine dönüştürülmüş olduğunu gösterir.

[Alfabede işlem başvurusu belgelerini görüntüle](reference/v3-0-translate.md)


| Dil    | Dil kodu | Komut Dosyası | Hedef/Kaynak | Komut Dosyası|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Arapça | `ar` | Arapça `Arab` | <--> | Tin `Latn` |
| Assam | `as` | Bengali `Beng` | <--> | Tin `Latn` |
| Bangla  | `bn` | Bengali `Beng` | <--> | Tin `Latn` |
|Beyaz Rusça| `be` | V `Cyrl`  | <--> | Tin `Latn` |
|Bulgarca| `bg` | V `Cyrl`  | <--> | Tin `Latn` |
| Basitleştirilmiş Çince | `zh-Hans` | Basitleştirilmiş Çince `Hans`| <--> | Tin `Latn` |
| Basitleştirilmiş Çince | `zh-Hans` | Basitleştirilmiş Çince `Hans`| <--> | Geleneksel Çince `Hant`|
| Geleneksel Çince | `zh-Hant` | Geleneksel Çince `Hant`| <--> | Tin `Latn` |
| Geleneksel Çince | `zh-Hant` | Geleneksel Çince `Hant`| <--> | Basitleştirilmiş Çince `Hans` |
|Yunanca| `el` | Yunanca `Grek`  | <--> | Tin `Latn` |
| Gucerat dili | `gu`  | Gucerat dili `Gujr` | <--> | Tin `Latn` |
| İbranice | `he` | İbranice `Hebr` | <--> | Tin `Latn` |
| Hintçe | `hi` | Devanagari `Deva` | <--> | Tin `Latn` |
| Japonca | `ja` | Japonca `Jpan` | <--> | Tin `Latn` |
| Kannada dili | `kn` | Kannada dili `Knda` | <--> | Tin `Latn` |
|Kazakça| `kk` | V `Cyrl`  | <--> | Tin `Latn` |
|Korece| `ko` | Kore dili `Kore`  | <--> | Tin `Latn` |
|Kırgızca| `ky` | V `Cyrl`  | <--> | Tin `Latn` |
|CA| `mk` | V `Cyrl`  | <--> | Tin `Latn` |
| Malayalam dili | `ml` | Malayalam dili `Mlym` | <--> | Tin `Latn` |
| Marathi | `mr` | Devanagari `Deva` | <--> | Tin `Latn` |
|Moğolca| `mn` | V `Cyrl`  | <--> | Tin `Latn` |
| Odia | `or` | Oriya `Orya` | <--> | Tin `Latn` |
|Farsça| `fa` | Arapça `Arab`  | <--> | Tin `Latn` |
| Pencap dili | `pa` | Gurmukhi `Guru`  | <--> | Tin `Latn`  |
|Rusça| `ru` | V `Cyrl`  | <--> | Tin `Latn` |
| Sırpça (Kiril) | `sr-Cyrl` | V `Cyrl`  | --> | Tin `Latn` |
| Sırpça (Latin) | `sr-Latn` | Tin `Latn` | --> | V `Cyrl`|
|Di| `sd` | Arapça `Arab`  | <--> | Tin `Latn` |
|Sinhali| `si` | Sinhali `Sinh`  | <--> | Tin `Latn` |
|İK| `tg` | V `Cyrl`  | <--> | Tin `Latn` |
| Tamil dili | `ta` | Tamil dili `Taml` | <--> | Tin `Latn` |
|Tatarca| `tt` | V `Cyrl`  | <--> | Tin `Latn` |
| Telugu dili | `te` | Telugu dili `Telu` | <--> | Tin `Latn` |
| Tayca | `th` | Tay dili `Thai` | --> | Tin `Latn` |
|Ukraynaca| `uk` | V `Cyrl`  | <--> | Tin `Latn` |
|Urduca| `ur` | Arapça `Arab`  | <--> | Tin `Latn` |

### <a name="dictionary"></a>Sözlük

Sözlük, arama ve örnekler yöntemlerini kullanarak Ingilizce 'ye veya Ingilizce 'ye kadar aşağıdaki dilleri destekler.

[Sözlük araması](reference/v3-0-dictionary-lookup.md) ve [Sözlük örnekleri](reference/v3-0-dictionary-examples.md) işlemleri için başvuru belgelerini görüntüleyin.

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
| Felemenkçe      | `nl`          |
| Estonya Dili      | `et`          |
| Fince      | `fi`          |
| Fransızca      | `fr`          |
| Almanca      | `de`          |
| Yunanca      | `el`          |
| Haian Creole      | `ht`          |
| İbranice      | `he`          |
| Hintçe      | `hi`          |
| Hmong DAW      | `mww`          |
| Macarca      | `hu`          |
| İzlandaca    | `is`  |
| Endonezce      | `id`          |
| İtalyanca      | `it`          |
| Japonca      | `ja`          |
| Klingon      | `tlh`          |
| Korece      | `ko`          |
| Letonca      | `lv`          |
| Litvanca      | `lt`          |
| Malayca      | `ms`          |
| Maltaca      | `mt`          |
| Norveççe      | `nb`          |
| Farsça      | `fa`          |
| Lehçe      | `pl`          |
| Portekizce (Brezilya)     | `pt`          |
| Rumence      | `ro`          |
| Rusça      | `ru`          |
| Sırpça (Latin)      | `sr-Latn`          |
| Slovakça     | `sk`          |
| Slovence      | `sl`          |
| İspanyolca      | `es`          |
| Svahili dili      | `sw`          |
| İsveççe      | `sv`          |
| Tamil dili      | `ta`          |
| Tayca      | `th`          |
| Türkçe      | `tr`          |
| Ukraynaca      | `uk`          |
| Urduca      | `ur`          |
| Vietnamca      | `vi`          |
| Galce      | `cy`          |

### <a name="access-the-translator-language-list-programmatically"></a>Çeviri dili listesine programlı olarak erişin

Diller metodunu kullanarak, çevirmen için desteklenen dillerin bir listesini alabilirsiniz. Liste özelliği, dil kodu ve dil adı ile Ingilizce veya desteklenen başka bir dilde görünüm görüntüleyebilirsiniz. Yeni diller kullanılabilir hale getirildiğinden bu liste Microsoft Translator hizmeti tarafından otomatik olarak güncelleştirilir.

[Dilleri görüntüle işlem başvurusu belgeleri](reference/v3-0-languages.md)

## <a name="customization"></a>Özelleştirme

Aşağıdaki diller, [özel çevirici](https://aka.ms/CustomTranslator)kullanılarak Ingilizce veya İngilizce arasında özelleştirme için kullanılabilir.

| Dil    | Dil kodu |
|:----------- |:-------------:|
|Afrikaner| `af`|
| Arapça       | `ar`          |
| Bangla      | `bn`          |
| Boşnakça (Latin)      | `bs`          |
| Bulgarca      | `bg`          |
|Katalanca|   `ca`    |
| Basitleştirilmiş Çince      | `zh-Hans`          |
|Geleneksel Çince|   `zh-Hant`   |
| Hırvatça      | `hr`          |
| Çekçe      | `cs`          |
| Danca      | `da`          |
| Felemenkçe      | `nl`          |
| İngilizce    | `en`     |
| Estonya Dili      | `et`          |
|Fiji Adaları dili|    `fj`    |
|Filipino|  `fil`   |
| Fince      | `fi`          |
| Fransızca      | `fr`          |
| Almanca      | `de`          |
| Yunanca      | `el`          |
| Gucerat dili| `gu`    |
| İbranice      | `he`          |
| Hintçe      | `hi`          |
| Macarca      | `hu`          |
| İzlandaca | `is` |
| Endonezce|   `id`    |
| Dili|    `iu`    |
| İrlandaca | `ga`  |
| İtalyanca      | `it`          |
| Japonca      | `ja`          |
|Kannada dili|`kn`|
| Korece      | `ko`          |
| Letonca      | `lv`          |
| Litvanca      | `lt`          |
| Malgaşça| `mg`    |
| Malayca|    `ms` |
|Maltaca|   `mt`    |
| Maori dili| `mi`  |
| Marathi| `mr`  |
| Norveççe      | `nb`          |
| Farsça      | `fa`          |
| Lehçe      | `pl`          |
| Portekizce (Brezilya) | `pt` |
| Pencap dili|`pa`|
| Rumence      | `ro`          |
| Rusça      | `ru`          |
| Samoan|   `sm`    |
| Sırpça (Latin)      | `sr-Latn`          |
| Slovakça     | `sk`          |
| Slovence      | `sl`          |
| İspanyolca      | `es`          |
| Svahili dili|  `sw`    |
| İsveççe      | `sv`          |
|Tahiti dili|  `ty`    |
| Tayca      | `th`          |
|Tonga dili|    `to`    |
| Türkçe      | `tr`          |
| Ukraynaca      | `uk`          |
| Urduca| `ur`    |
| Vietnamca      | `vi`          |
| Galce | `cy` |

## <a name="speech-translation"></a>Konuşma Çevirisi
Konuşma çevirisi, bilişsel hizmetler konuşma hizmeti ile Translator kullanılarak kullanılabilir. Konuşma çevirisi kullanma hakkında daha fazla bilgi edinmek ve [kullanılabilir tüm dil seçeneklerini](../speech-service/language-support.md)görüntülemek Için [konuşma hizmeti belgelerini](../speech-service/index.yml) görüntüleyin.

### <a name="speech-to-text"></a>Konuşmayı metne dönüştürme
İstediğiniz metin diline çevirmek için konuşmayı metne dönüştürün. Konuşmayı metne dönüştürme özelliği, konuşma senşiyle birlikte kullanıldığında konuşma için konuşma çevirisi veya konuşma çevirisi için kullanılır.

| Dil    |
|:----------- |
|Arapça|
|Cantonetıcı (Geleneksel)|
|Katalanca|
|Basitleştirilmiş Çince|
|Geleneksel Çince|
|Danca|
|Felemenkçe|
|İngilizce|
|Fince|
|Fransızca|
|Fransızca (Kanada)|
|Almanca|
|Gucerat dili|
|Hintçe|
|İtalyanca|
|Japonca|
|Korece|
|Marathi|
|Norveççe|
|Lehçe|
|Portekizce (Brezilya)|
|Portekizce (Portekiz)|
|Rusça|
|İspanyolca|
|İsveççe|
|Tamil dili|
|Telugu dili|
|Tayca|
|Türkçe|

### <a name="text-to-speech"></a>Metin okuma
Metni konuşmaya dönüştürün. Metin okuma, çeviri sonuçlarının duyulabilir çıktısını eklemek veya konuşmayı metne yönelik olarak kullanıldığında konuşma tanıma çevirisi için kullanılır. 

| Dil |
|:-|
| Arapça |
| Bulgarca |
| Cantonetıcı (Geleneksel) |
| Katalanca |
| Basitleştirilmiş Çince |
| Geleneksel Çince |
| Hırvatça |
| Çekçe |
| Danca |
| Felemenkçe |
| İngilizce |
| Fince |
| Fransızca |
| Fransızca (Kanada) |
| Almanca |
| Yunanca |
| İbranice |
| Hintçe |
| Macarca |
| Endonezce |
| İtalyanca |
| Japonca |
| Korece |
| Malayca |
| Norveççe |
| Lehçe |
| Portekizce (Brezilya) |
| Portekizce (Portekiz) |
| Rumence |
| Rusça |
| Slovakça |
| Slovence |
| İspanyolca |
| İsveççe |
| Tamil dili |
| Telugu dili |
| Tayca |
| Türkçe |
| Vietnamca |

## <a name="view-the-language-list-on-the-microsoft-translator-website"></a>Microsoft Translator Web sitesinde dil listesini görüntüleyin

Microsoft Translator Web sitesinde, dillere hızlı bir bakış için, konuşma çevirisi için metin çevirisi ve konuşma hizmeti çevirmeni tarafından desteklenen tüm diller gösterilir. Bu liste, dil kodları gibi geliştiriciye özgü bilgiler içermez.

[Dillerin listesine bakın](https://www.microsoft.com/translator/languages.aspx)
