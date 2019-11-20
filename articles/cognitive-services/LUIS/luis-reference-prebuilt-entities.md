---
title: Önceden oluşturulmuş tüm varlıklar-LUSıS
titleSuffix: Azure Cognitive Services
description: Bu makale, Language Understanding (LUO) ' de bulunan önceden oluşturulmuş varlıkların listesini içerir.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: diberry
ms.openlocfilehash: a5fcccc18a453101bd1166f2c85b0c3cdc747c3a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464952"
---
# <a name="entities-per-culture-in-your-luis-model"></a>LUSıS modelinizde kültür başına varlık

Language Understanding (LUSıS) önceden oluşturulmuş varlıklar sağlar. Uygulamanıza önceden oluşturulmuş bir varlık dahil edildiğinde, Lud, uç nokta yanıtında karşılık gelen varlık tahminini içerir. Tüm örnek söyleymalar da varlıkla etiketlidir. Önceden **oluşturulmuş varlıkların davranışı** değiştirilemez. Aksi belirtilmediği takdirde, önceden oluşturulmuş varlıklar tüm LUıN uygulama yerel ayarları 'nda (kültürler) kullanılabilir. Aşağıdaki tabloda her kültür için desteklenen önceden oluşturulmuş varlıklar gösterilmektedir.

|Kültür|Alt kültürler|Notlar|
|--|--|--|
|Çince|[zh-CN](#chinese-entity-support)||
|Hollanda dili|[NL-NL](#dutch-entity-support)||
|Türkçe|[en-US (Amerikan)](#english-american-entity-support)||
|Fransızca|[fr-CA (Kanada)](#french-canadian-entity-support), [fr-fr (Fransa)](#french-france-entity-support), ||
|Almanca|[de-DE](#german-entity-support)||
|İtalyanca|[BT BT](#italian-entity-support)||
|Japonca|[ja-JP](#japanese-entity-support)||
|Korece|[ko-KR](#korean-entity-support)||
|Portekizce|[PT-BR (Brezilya)](#portuguese-brazil-entity-support)||
|İspanyolca|[es-es (İspanya)](#spanish-spain-entity-support), [es-MX (Meksika)](#spanish-mexico-entity-support)||
|Türkçe|[Türkçe](#turkish-entity-support)|Türkçe 'da önceden oluşturulmuş varlık desteklenmez|

## <a name="prediction-endpoint-runtime"></a>Tahmin uç noktası çalışma zamanı

Önceden oluşturulmuş bir varlığın belirli bir dilde kullanılabilirliği, tahmin uç noktası çalışma zamanı sürümü tarafından belirlenir. 

## <a name="chinese-entity-support"></a>Çince varlık desteği

Aşağıdaki varlıklar desteklenir:

|Önceden oluşturulmuş varlık|```zh-CN``` |
------|:------:|
[Yaş](luis-reference-prebuilt-age.md):<br>yıl<br>Başından<br>hafta<br>günündeki   |    V2, V3   |
[Para birimi (para)](luis-reference-prebuilt-currency.md):<br>Doları<br>kesir birimi (örn: kuruş)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>dateRange<br>time<br>timerange   |    V2, V3   | 
[Boyut](luis-reference-prebuilt-dimension.md):<br>birimindeki<br>alandır<br>Lık<br>bilgi (örn: bit/bayt)<br>Uzunluk (örn: ölçüm)<br>Hız (örn. mil/saat)  |    V2, V3   | 
[E-posta](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   | 
[Sayısından](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Numarasını](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Den](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[Kişi adı](luis-reference-prebuilt-person.md)   |    V2, V3   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Sıcaklık](luis-reference-prebuilt-temperature.md):<br>Fahrenhayt<br>Kelvin<br>Rankine dili<br>Delisle<br>çevirip   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="dutch-entity-support"></a>Felemenkçe varlık desteği

Aşağıdaki varlıklar desteklenir:

|Önceden oluşturulmuş varlık|```nl-NL``` |
------|:------:|
[Yaş](luis-reference-prebuilt-age.md):<br>yıl<br>Başından<br>hafta<br>günündeki   |    V2, V3   |
[Para birimi (para)](luis-reference-prebuilt-currency.md):<br>Doları<br>kesir birimi (örn: kuruş)  |    V2, V3   |
[Hem](luis-reference-prebuilt-deprecated.md)   |    -   | 
[Boyut](luis-reference-prebuilt-dimension.md):<br>birimindeki<br>alandır<br>Lık<br>bilgi (örn: bit/bayt)<br>Uzunluk (örn: ölçüm)<br>Hız (örn. mil/saat)  |    V2, V3   | 
[E-posta](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Sayısından](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Numarasını](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Den](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[Kişi adı](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Sıcaklık](luis-reference-prebuilt-temperature.md):<br>Fahrenhayt<br>Kelvin<br>Rankine dili<br>Delisle<br>çevirip   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="english-american-entity-support"></a>İngilizce (Amerikan) varlık desteği

Aşağıdaki varlıklar desteklenir:

|Önceden oluşturulmuş varlık|```en-US``` |
------|:------:|
[Yaş](luis-reference-prebuilt-age.md):<br>yıl<br>Başından<br>hafta<br>günündeki   |    V2, V3   |
[Para birimi (para)](luis-reference-prebuilt-currency.md):<br>Doları<br>kesir birimi (örn: kuruş)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>dateRange<br>time<br>timerange   |    V2, V3   | 
[Boyut](luis-reference-prebuilt-dimension.md):<br>birimindeki<br>alandır<br>Lık<br>bilgi (örn: bit/bayt)<br>Uzunluk (örn: ölçüm)<br>Hız (örn. mil/saat)  |    V2, V3   | 
[E-posta](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    V2, V3   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Sayısından](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Numarasını](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    V2, V3   |
[Den](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[Kişi adı](luis-reference-prebuilt-person.md)   |    V2, V3   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Sıcaklık](luis-reference-prebuilt-temperature.md):<br>Fahrenhayt<br>Kelvin<br>Rankine dili<br>Delisle<br>çevirip   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-france-entity-support"></a>Fransızca (Fransa) varlık desteği

Aşağıdaki varlıklar desteklenir:

|Önceden oluşturulmuş varlık|```fr-FR``` |
------|:------:|
[Yaş](luis-reference-prebuilt-age.md):<br>yıl<br>Başından<br>hafta<br>günündeki   |    V2, V3   |
[Para birimi (para)](luis-reference-prebuilt-currency.md):<br>Doları<br>kesir birimi (örn: kuruş)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>dateRange<br>time<br>timerange   |    V2, V3   | 
[Boyut](luis-reference-prebuilt-dimension.md):<br>birimindeki<br>alandır<br>Lık<br>bilgi (örn: bit/bayt)<br>Uzunluk (örn: ölçüm)<br>Hız (örn. mil/saat)  |    V2, V3   | 
[E-posta](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Sayısından](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Numarasını](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |  
[Den](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[Kişi adı](luis-reference-prebuilt-person.md)   |   -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Sıcaklık](luis-reference-prebuilt-temperature.md):<br>Fahrenhayt<br>Kelvin<br>Rankine dili<br>Delisle<br>çevirip   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-canadian-entity-support"></a>Fransızca (Kanada) varlık desteği

Aşağıdaki varlıklar desteklenir:

|Önceden oluşturulmuş varlık|```fr-CA``` |
------|:------:|
[Yaş](luis-reference-prebuilt-age.md):<br>yıl<br>Başından<br>hafta<br>günündeki   |    V2, V3   |
[Para birimi (para)](luis-reference-prebuilt-currency.md):<br>Doları<br>kesir birimi (örn: kuruş)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>dateRange<br>time<br>timerange   |    V2, V3   | 
[Boyut](luis-reference-prebuilt-dimension.md):<br>birimindeki<br>alandır<br>Lık<br>bilgi (örn: bit/bayt)<br>Uzunluk (örn: ölçüm)<br>Hız (örn. mil/saat)  |    V2, V3   | 
[E-posta](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Sayısından](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Numarasını](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Den](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[Kişi adı](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Sıcaklık](luis-reference-prebuilt-temperature.md):<br>Fahrenhayt<br>Kelvin<br>Rankine dili<br>Delisle<br>çevirip   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="german-entity-support"></a>Almanya varlık desteği

Aşağıdaki varlıklar desteklenir:

|Önceden oluşturulmuş varlık|```de-DE``` |
------|:------:|
[Yaş](luis-reference-prebuilt-age.md):<br>yıl<br>Başından<br>hafta<br>günündeki   |    V2, V3   |
[Para birimi (para)](luis-reference-prebuilt-currency.md):<br>Doları<br>kesir birimi (örn: kuruş)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>dateRange<br>time<br>timerange   |    V2, V3   | 
[Boyut](luis-reference-prebuilt-dimension.md):<br>birimindeki<br>alandır<br>Lık<br>bilgi (örn: bit/bayt)<br>Uzunluk (örn: ölçüm)<br>Hız (örn. mil/saat)  |    V2, V3   | 
[E-posta](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Sayısından](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Numarasını](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |  
[Den](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[Kişi adı](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Sıcaklık](luis-reference-prebuilt-temperature.md):<br>Fahrenhayt<br>Kelvin<br>Rankine dili<br>Delisle<br>çevirip   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="italian-entity-support"></a>İtalyanca varlık desteği

İtalyan ön derlenmiş yaş, para birimi, boyut, sayı, yüzde _çözünürlüğü_ v2 ve v3 önizlemesinden değişti.

Aşağıdaki varlıklar desteklenir:

|Önceden oluşturulmuş varlık|```it-IT``` |
------|:------:|
[Yaş](luis-reference-prebuilt-age.md):<br>yıl<br>Başından<br>hafta<br>günündeki   |    V2, V3   |
[Para birimi (para)](luis-reference-prebuilt-currency.md):<br>Doları<br>kesir birimi (örn: kuruş)  |    V2, V3   |
[Hem](luis-reference-prebuilt-deprecated.md)   |    -   | 
[Boyut](luis-reference-prebuilt-dimension.md):<br>birimindeki<br>alandır<br>Lık<br>bilgi (örn: bit/bayt)<br>Uzunluk (örn: ölçüm)<br>Hız (örn. mil/saat)  |    V2, V3   | 
[E-posta](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Sayısından](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Numarasını](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Den](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[Kişi adı](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Sıcaklık](luis-reference-prebuilt-temperature.md):<br>Fahrenhayt<br>Kelvin<br>Rankine dili<br>Delisle<br>çevirip   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="japanese-entity-support"></a>Japonca varlık desteği

Aşağıdaki varlıklar desteklenir:

|Önceden oluşturulmuş varlık|```ja-JP``` |
------|:------:|
[Yaş](luis-reference-prebuilt-age.md):<br>yıl<br>Başından<br>hafta<br>günündeki   |    V2,-   |
[Para birimi (para)](luis-reference-prebuilt-currency.md):<br>Doları<br>kesir birimi (örn: kuruş)  |    V2,-   |
[Hem](luis-reference-prebuilt-deprecated.md)   |    -   | 
[Boyut](luis-reference-prebuilt-dimension.md):<br>birimindeki<br>alandır<br>Lık<br>bilgi (örn: bit/bayt)<br>Uzunluk (örn: ölçüm)<br>Hız (örn. mil/saat)  |    V2,-   | 
[E-posta](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Sayısından](luis-reference-prebuilt-number.md)   |    V2,-   |  
[Numarasını](luis-reference-prebuilt-ordinal.md)   |    V2,-   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Den](luis-reference-prebuilt-percentage.md)   |    V2,-   | 
[Kişi adı](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Sıcaklık](luis-reference-prebuilt-temperature.md):<br>Fahrenhayt<br>Kelvin<br>Rankine dili<br>Delisle<br>çevirip   |    V2,-   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="korean-entity-support"></a>Korece varlık desteği

Aşağıdaki varlıklar desteklenir:

|Önceden oluşturulmuş varlık|```ko-KR``` |
------|:------:|
[Yaş](luis-reference-prebuilt-age.md):<br>yıl<br>Başından<br>hafta<br>günündeki   |    -   |
[Para birimi (para)](luis-reference-prebuilt-currency.md):<br>Doları<br>kesir birimi (örn: kuruş)  |    -   |
[Hem](luis-reference-prebuilt-deprecated.md)   |    -   | 
[Boyut](luis-reference-prebuilt-dimension.md):<br>birimindeki<br>alandır<br>Lık<br>bilgi (örn: bit/bayt)<br>Uzunluk (örn: ölçüm)<br>Hız (örn. mil/saat)  |    -   | 
[E-posta](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Sayısından](luis-reference-prebuilt-number.md)   |    -   |  
[Numarasını](luis-reference-prebuilt-ordinal.md)   |    -   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Den](luis-reference-prebuilt-percentage.md)   |    -   | 
[Kişi adı](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Sıcaklık](luis-reference-prebuilt-temperature.md):<br>Fahrenhayt<br>Kelvin<br>Rankine dili<br>Delisle<br>çevirip   |    -   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="portuguese-brazil-entity-support"></a>Portekizce (Brezilya) varlık desteği

Aşağıdaki varlıklar desteklenir:

|Önceden oluşturulmuş varlık|```pt-BR``` |
------|:------:|
[Yaş](luis-reference-prebuilt-age.md):<br>yıl<br>Başından<br>hafta<br>günündeki   |    V2, V3   |
[Para birimi (para)](luis-reference-prebuilt-currency.md):<br>Doları<br>kesir birimi (örn: kuruş)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>dateRange<br>time<br>timerange   |    V2, V3   | 
[Boyut](luis-reference-prebuilt-dimension.md):<br>birimindeki<br>alandır<br>Lık<br>bilgi (örn: bit/bayt)<br>Uzunluk (örn: ölçüm)<br>Hız (örn. mil/saat)  |    V2, V3   | 
[E-posta](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Sayısından](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Numarasını](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Den](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[Kişi adı](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Sıcaklık](luis-reference-prebuilt-temperature.md):<br>Fahrenhayt<br>Kelvin<br>Rankine dili<br>Delisle<br>çevirip   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="spanish-spain-entity-support"></a>İspanyolca (Ispanya) varlık desteği

Aşağıdaki varlıklar desteklenir:

|Önceden oluşturulmuş varlık|```es-ES``` |
------|:------:|
[Yaş](luis-reference-prebuilt-age.md):<br>yıl<br>Başından<br>hafta<br>günündeki   |    V2, V3   |
[Para birimi (para)](luis-reference-prebuilt-currency.md):<br>Doları<br>kesir birimi (örn: kuruş)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>dateRange<br>time<br>timerange   |    V2, V3   | 
[Boyut](luis-reference-prebuilt-dimension.md):<br>birimindeki<br>alandır<br>Lık<br>bilgi (örn: bit/bayt)<br>Uzunluk (örn: ölçüm)<br>Hız (örn. mil/saat)  |    V2, V3   | 
[E-posta](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Sayısından](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Numarasını](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Den](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[Kişi adı](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Sıcaklık](luis-reference-prebuilt-temperature.md):<br>Fahrenhayt<br>Kelvin<br>Rankine dili<br>Delisle<br>çevirip   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="spanish-mexico-entity-support"></a>İspanyolca (Meksika) varlık desteği

Aşağıdaki varlıklar desteklenir:

|Önceden oluşturulmuş varlık|```es-MX``` |
------|:------:|
[Yaş](luis-reference-prebuilt-age.md):<br>yıl<br>Başından<br>hafta<br>günündeki   |    -   |
[Para birimi (para)](luis-reference-prebuilt-currency.md):<br>Doları<br>kesir birimi (örn: kuruş)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>dateRange<br>time<br>timerange   |    -   | 
[Boyut](luis-reference-prebuilt-dimension.md):<br>birimindeki<br>alandır<br>Lık<br>bilgi (örn: bit/bayt)<br>Uzunluk (örn: ölçüm)<br>Hız (örn. mil/saat)  |    -   | 
[E-posta](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Sayısından](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Numarasını](luis-reference-prebuilt-ordinal.md)   |    -   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Den](luis-reference-prebuilt-percentage.md)   |    -   | 
[Kişi adı](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Sıcaklık](luis-reference-prebuilt-temperature.md):<br>Fahrenhayt<br>Kelvin<br>Rankine dili<br>Delisle<br>çevirip   |    -   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

[Kullanım dışı bırakılan varlıklarda](luis-reference-prebuilt-deprecated.md) notlara bakın

KeyPhrase, Portekizce (Brezilya)-```pt-BR```tüm alt kültürleri için kullanılamaz.

## <a name="turkish-entity-support"></a>Türkçe varlık desteği

**Türkçe sürümünde desteklenen önceden oluşturulmuş varlık yok.** 

<!--

|Prebuilt entity|```tr-tr``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    -   |
[Currency (money)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (ex: penny)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    -   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>volume<br>area<br>weight<br>information (ex: bit/byte)<br>length (ex: meter)<br>speed (ex: mile per hour)  |    -   | 
[Email](luis-reference-prebuilt-email.md)   |    -   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   | 
[Number](luis-reference-prebuilt-number.md)   |    -   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    -   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    -   | 
[URL](luis-reference-prebuilt-url.md)   |    -   |

See notes on [Deprecated prebuilt entities](luis-reference-prebuilt-deprecated.md)


KeyPhrase is not available.
-->

## <a name="contribute-to-prebuilt-entity-cultures"></a>Önceden oluşturulmuş varlık külbilgilerine katkıda bulunun
Önceden oluşturulmuş varlıklar, Tanıyıcılar-metin açık kaynaklı projede geliştirilir. Projeye [katkıda bulunun](https://github.com/Microsoft/Recognizers-Text) . Bu proje, kültür başına para birimi örnekleri içerir. 

GeographyV2 ve PersonName, Tanıyıcılar-metin projesine dahil değildir. Önceden oluşturulmuş bu varlıklarla ilgili sorunlar için lütfen bir [destek isteği](../../azure-supportability/how-to-create-azure-support-request.md)açın. 

## <a name="next-steps"></a>Sonraki adımlar

[Sayı](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md)ve [para birimi](luis-reference-prebuilt-currency.md) varlıkları hakkında bilgi edinin. 
