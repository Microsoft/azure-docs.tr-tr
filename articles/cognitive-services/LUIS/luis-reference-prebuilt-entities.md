---
title: Önceden oluşturulmuş tüm varlıklar-LUSıS
titleSuffix: Azure Cognitive Services
description: Bu makale, Language Understanding (LUO) ' de bulunan önceden oluşturulmuş varlıkların listesini içerir.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/13/2021
ms.openlocfilehash: 7155a829655645e13e0485ed7d51305ec50e5b0a
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502768"
---
# <a name="entities-per-culture-in-your-luis-model"></a>LUSıS modelinizde kültür başına varlık

Language Understanding (LUSıS) önceden oluşturulmuş varlıklar sağlar.

## <a name="entity-resolution"></a>Varlık çözümleme
Uygulamanıza önceden oluşturulmuş bir varlık dahil edildiğinde, Lud, uç nokta yanıtında karşılık gelen varlık çözünürlüğünü içerir. Tüm örnek söyleymalar da varlıkla etiketlidir.

Önceden oluşturulmuş varlıkların davranışı değiştirilemez, ancak [önceden oluşturulmuş varlığı bir makine öğrenimi varlığına veya alt varlığa bir özellik olarak ekleyerek](luis-concept-entity-types.md#prebuilt-entity)çözümü geliştirebilirsiniz.

## <a name="availability"></a>Kullanılabilirlik
Aksi belirtilmediği takdirde, önceden oluşturulmuş varlıklar tüm LUıN uygulama yerel ayarları 'nda (kültürler) kullanılabilir. Aşağıdaki tabloda her kültür için desteklenen önceden oluşturulmuş varlıklar gösterilmektedir.

|Kültür|Alt kültürler|Notlar|
|--|--|--|
|Çince|[zh-CN](#chinese-entity-support)||
|Felemenkçe|[NL-NL](#dutch-entity-support)||
|İngilizce|[en-US (Amerikan)](#english-american-entity-support)||
|Fransızca|[fr-CA (Kanada)](#french-canadian-entity-support), [fr-fr (Fransa)](#french-france-entity-support), ||
|Almanca|[de-DE](#german-entity-support)||
|İtalyanca|[BT BT](#italian-entity-support)||
|Japonca|[ja-JP](#japanese-entity-support)||
|Korece|[ko-KR](#korean-entity-support)||
|Portekizce|[PT-BR (Brezilya)](#portuguese-brazil-entity-support)||
|İspanyolca|[es-es (İspanya)](#spanish-spain-entity-support), [es-MX (Meksika)](#spanish-mexico-entity-support)||
|Türkçe|[Türkçe](#turkish-entity-support)||

## <a name="prediction-endpoint-runtime"></a>Tahmin uç noktası çalışma zamanı

Önceden oluşturulmuş bir varlığın belirli bir dilde kullanılabilirliği, tahmin uç noktası çalışma zamanı sürümü tarafından belirlenir.

## <a name="chinese-entity-support"></a>Çince varlık desteği

Aşağıdaki varlıklar desteklenir:

| Önceden oluşturulmuş varlık | zh-CN |
| --------------- | :---: |
[Yaş](luis-reference-prebuilt-age.md):<br>yıl<br>ay<br>hafta<br>gün   |    V2, V3   |
[Para birimi (para)](luis-reference-prebuilt-currency.md):<br>Doları<br>kesir birimi (örn: kuruş)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>dateRange<br>time<br>timerange   |    V2, V3   |
[Boyut](luis-reference-prebuilt-dimension.md):<br>Ses düzeyi<br>alan<br>ağırlık<br>bilgi (örn: bit/bayt)<br>Uzunluk (örn: ölçüm)<br>Hız (örn. mil/saat)  |    V2, V3   |
[E-posta](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   |
[Sayı](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Sıralı](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Yüzde](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Kişi adı](luis-reference-prebuilt-person.md)   |    V2, V3   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Sıcaklık](luis-reference-prebuilt-temperature.md):<br>Fahrenhayt<br>Kelvin<br>Rankine dili<br>Delisle<br>çevirip   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="dutch-entity-support"></a>Felemenkçe varlık desteği

Aşağıdaki varlıklar desteklenir:

| Önceden oluşturulmuş varlık | nl-NL |
| --------------- | :---: |
[Yaş](luis-reference-prebuilt-age.md):<br>yıl<br>ay<br>hafta<br>gün   |    V2, V3   |
[Para birimi (para)](luis-reference-prebuilt-currency.md):<br>Doları<br>kesir birimi (örn: kuruş)  |    V2, V3   |
[Hem](luis-reference-prebuilt-deprecated.md)   |    -   |
[Boyut](luis-reference-prebuilt-dimension.md):<br>Ses düzeyi<br>alan<br>ağırlık<br>bilgi (örn: bit/bayt)<br>Uzunluk (örn: ölçüm)<br>Hız (örn. mil/saat)  |    V2, V3   |
[E-posta](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Sayı](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Sıralı](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Yüzde](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Kişi adı](luis-reference-prebuilt-person.md)   |    -   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Sıcaklık](luis-reference-prebuilt-temperature.md):<br>Fahrenhayt<br>Kelvin<br>Rankine dili<br>Delisle<br>çevirip   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="english-american-entity-support"></a>İngilizce (Amerikan) varlık desteği

Aşağıdaki varlıklar desteklenir:

| Önceden oluşturulmuş varlık | en-US |
| --------------- | :---: |
[Yaş](luis-reference-prebuilt-age.md):<br>yıl<br>ay<br>hafta<br>gün   |    V2, V3   |
[Para birimi (para)](luis-reference-prebuilt-currency.md):<br>Doları<br>kesir birimi (örn: kuruş)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>dateRange<br>time<br>timerange   |    V2, V3   |
[Boyut](luis-reference-prebuilt-dimension.md):<br>Ses düzeyi<br>alan<br>ağırlık<br>bilgi (örn: bit/bayt)<br>Uzunluk (örn: ölçüm)<br>Hız (örn. mil/saat)  |    V2, V3   |
[E-posta](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    V2, V3   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Sayı](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Sıralı](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    V2, V3   |
[Yüzde](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Kişi adı](luis-reference-prebuilt-person.md)   |    V2, V3   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Sıcaklık](luis-reference-prebuilt-temperature.md):<br>Fahrenhayt<br>Kelvin<br>Rankine dili<br>Delisle<br>çevirip   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-france-entity-support"></a>Fransızca (Fransa) varlık desteği

Aşağıdaki varlıklar desteklenir:

| Önceden oluşturulmuş varlık | fr-FR |
| --------------- | :---: |
[Yaş](luis-reference-prebuilt-age.md):<br>yıl<br>ay<br>hafta<br>gün   |    V2, V3   |
[Para birimi (para)](luis-reference-prebuilt-currency.md):<br>Doları<br>kesir birimi (örn: kuruş)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>dateRange<br>time<br>timerange   |    V2, V3   |
[Boyut](luis-reference-prebuilt-dimension.md):<br>Ses düzeyi<br>alan<br>ağırlık<br>bilgi (örn: bit/bayt)<br>Uzunluk (örn: ölçüm)<br>Hız (örn. mil/saat)  |    V2, V3   |
[E-posta](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Sayı](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Sıralı](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Yüzde](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Kişi adı](luis-reference-prebuilt-person.md)   |   -   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Sıcaklık](luis-reference-prebuilt-temperature.md):<br>Fahrenhayt<br>Kelvin<br>Rankine dili<br>Delisle<br>çevirip   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-canadian-entity-support"></a>Fransızca (Kanada) varlık desteği

Aşağıdaki varlıklar desteklenir:

| Önceden oluşturulmuş varlık | fr-CA |
| --------------- | :---: |
[Yaş](luis-reference-prebuilt-age.md):<br>yıl<br>ay<br>hafta<br>gün   |    V2, V3   |
[Para birimi (para)](luis-reference-prebuilt-currency.md):<br>Doları<br>kesir birimi (örn: kuruş)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>dateRange<br>time<br>timerange   |    V2, V3   |
[Boyut](luis-reference-prebuilt-dimension.md):<br>Ses düzeyi<br>alan<br>ağırlık<br>bilgi (örn: bit/bayt)<br>Uzunluk (örn: ölçüm)<br>Hız (örn. mil/saat)  |    V2, V3   |
[E-posta](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Sayı](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Sıralı](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Yüzde](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Kişi adı](luis-reference-prebuilt-person.md)   |    -   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Sıcaklık](luis-reference-prebuilt-temperature.md):<br>Fahrenhayt<br>Kelvin<br>Rankine dili<br>Delisle<br>çevirip   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="german-entity-support"></a>Almanya varlık desteği

Aşağıdaki varlıklar desteklenir:

|Önceden oluşturulmuş varlık | de-DE |
| -------------- | :---: |
[Yaş](luis-reference-prebuilt-age.md):<br>yıl<br>ay<br>hafta<br>gün   |    V2, V3   |
[Para birimi (para)](luis-reference-prebuilt-currency.md):<br>Doları<br>kesir birimi (örn: kuruş)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>dateRange<br>time<br>timerange   |    V2, V3   |
[Boyut](luis-reference-prebuilt-dimension.md):<br>Ses düzeyi<br>alan<br>ağırlık<br>bilgi (örn: bit/bayt)<br>Uzunluk (örn: ölçüm)<br>Hız (örn. mil/saat)  |    V2, V3   |
[E-posta](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Sayı](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Sıralı](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Yüzde](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Kişi adı](luis-reference-prebuilt-person.md)   |    -   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Sıcaklık](luis-reference-prebuilt-temperature.md):<br>Fahrenhayt<br>Kelvin<br>Rankine dili<br>Delisle<br>çevirip   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="italian-entity-support"></a>İtalyanca varlık desteği

İtalyan ön derlenmiş yaş, para birimi, boyut, sayı, yüzde _çözünürlüğü_ v2 ve v3 önizlemesinden değişti.

Aşağıdaki varlıklar desteklenir:

| Önceden oluşturulmuş varlık | it-IT |
| --------------- | :---: |
[Yaş](luis-reference-prebuilt-age.md):<br>yıl<br>ay<br>hafta<br>gün   |    V2, V3   |
[Para birimi (para)](luis-reference-prebuilt-currency.md):<br>Doları<br>kesir birimi (örn: kuruş)  |    V2, V3   |
[Hem](luis-reference-prebuilt-deprecated.md)   |    -   |
[Boyut](luis-reference-prebuilt-dimension.md):<br>Ses düzeyi<br>alan<br>ağırlık<br>bilgi (örn: bit/bayt)<br>Uzunluk (örn: ölçüm)<br>Hız (örn. mil/saat)  |    V2, V3   |
[E-posta](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Sayı](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Sıralı](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Yüzde](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Kişi adı](luis-reference-prebuilt-person.md)   |    -   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Sıcaklık](luis-reference-prebuilt-temperature.md):<br>Fahrenhayt<br>Kelvin<br>Rankine dili<br>Delisle<br>çevirip   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="japanese-entity-support"></a>Japonca varlık desteği

Aşağıdaki varlıklar desteklenir:

|Önceden oluşturulmuş varlık | ja-JP |
| -------------- | :---: |
[Yaş](luis-reference-prebuilt-age.md):<br>yıl<br>ay<br>hafta<br>gün   |    V2,-   |
[Para birimi (para)](luis-reference-prebuilt-currency.md):<br>Doları<br>kesir birimi (örn: kuruş)  |    V2,-   |
[Hem](luis-reference-prebuilt-deprecated.md)   |    -   |
[Boyut](luis-reference-prebuilt-dimension.md):<br>Ses düzeyi<br>alan<br>ağırlık<br>bilgi (örn: bit/bayt)<br>Uzunluk (örn: ölçüm)<br>Hız (örn. mil/saat)  |    V2,-   |
[E-posta](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Sayı](luis-reference-prebuilt-number.md)   |    V2,-   |
[Sıralı](luis-reference-prebuilt-ordinal.md)   |    V2,-   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Yüzde](luis-reference-prebuilt-percentage.md)   |    V2,-   |
[Kişi adı](luis-reference-prebuilt-person.md)   |    -   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Sıcaklık](luis-reference-prebuilt-temperature.md):<br>Fahrenhayt<br>Kelvin<br>Rankine dili<br>Delisle<br>çevirip   |    V2,-   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="korean-entity-support"></a>Korece varlık desteği

Aşağıdaki varlıklar desteklenir:

| Önceden oluşturulmuş varlık | ko-KR |
| --------------- | :---: |
[Yaş](luis-reference-prebuilt-age.md):<br>yıl<br>ay<br>hafta<br>gün   |    -   |
[Para birimi (para)](luis-reference-prebuilt-currency.md):<br>Doları<br>kesir birimi (örn: kuruş)  |    -   |
[Hem](luis-reference-prebuilt-deprecated.md)   |    -   |
[Boyut](luis-reference-prebuilt-dimension.md):<br>Ses düzeyi<br>alan<br>ağırlık<br>bilgi (örn: bit/bayt)<br>Uzunluk (örn: ölçüm)<br>Hız (örn. mil/saat)  |    -   |
[E-posta](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Sayı](luis-reference-prebuilt-number.md)   |    -   |
[Sıralı](luis-reference-prebuilt-ordinal.md)   |    -   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Yüzde](luis-reference-prebuilt-percentage.md)   |    -   |
[Kişi adı](luis-reference-prebuilt-person.md)   |    -   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Sıcaklık](luis-reference-prebuilt-temperature.md):<br>Fahrenhayt<br>Kelvin<br>Rankine dili<br>Delisle<br>çevirip   |    -   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="portuguese-brazil-entity-support"></a>Portekizce (Brezilya) varlık desteği

Aşağıdaki varlıklar desteklenir:

| Önceden oluşturulmuş varlık | pt-BR |
| --------------- | :---: |
[Yaş](luis-reference-prebuilt-age.md):<br>yıl<br>ay<br>hafta<br>gün   |    V2, V3   |
[Para birimi (para)](luis-reference-prebuilt-currency.md):<br>Doları<br>kesir birimi (örn: kuruş)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>dateRange<br>time<br>timerange   |    V2, V3   |
[Boyut](luis-reference-prebuilt-dimension.md):<br>Ses düzeyi<br>alan<br>ağırlık<br>bilgi (örn: bit/bayt)<br>Uzunluk (örn: ölçüm)<br>Hız (örn. mil/saat)  |    V2, V3   |
[E-posta](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Sayı](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Sıralı](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Yüzde](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Kişi adı](luis-reference-prebuilt-person.md)   |    -   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Sıcaklık](luis-reference-prebuilt-temperature.md):<br>Fahrenhayt<br>Kelvin<br>Rankine dili<br>Delisle<br>çevirip   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

KeyPhrase, Portekizce (Brezilya)-tüm alt kültürleri için kullanılamaz ```pt-BR``` .

## <a name="spanish-spain-entity-support"></a>İspanyolca (Ispanya) varlık desteği

Aşağıdaki varlıklar desteklenir:

| Önceden oluşturulmuş varlık | es-ES |
| --------------- | :---: |
[Yaş](luis-reference-prebuilt-age.md):<br>yıl<br>ay<br>hafta<br>gün   |    V2, V3   |
[Para birimi (para)](luis-reference-prebuilt-currency.md):<br>Doları<br>kesir birimi (örn: kuruş)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>dateRange<br>time<br>timerange   |    V2, V3   |
[Boyut](luis-reference-prebuilt-dimension.md):<br>Ses düzeyi<br>alan<br>ağırlık<br>bilgi (örn: bit/bayt)<br>Uzunluk (örn: ölçüm)<br>Hız (örn. mil/saat)  |    V2, V3   |
[E-posta](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Sayı](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Sıralı](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Yüzde](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Kişi adı](luis-reference-prebuilt-person.md)   |    -   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Sıcaklık](luis-reference-prebuilt-temperature.md):<br>Fahrenhayt<br>Kelvin<br>Rankine dili<br>Delisle<br>çevirip   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="spanish-mexico-entity-support"></a>İspanyolca (Meksika) varlık desteği

Aşağıdaki varlıklar desteklenir:

| Önceden oluşturulmuş varlık | es-MX |
| --------------- | :---: |
[Yaş](luis-reference-prebuilt-age.md):<br>yıl<br>ay<br>hafta<br>gün   |    -   |
[Para birimi (para)](luis-reference-prebuilt-currency.md):<br>Doları<br>kesir birimi (örn: kuruş)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>dateRange<br>time<br>timerange   |    -   |
[Boyut](luis-reference-prebuilt-dimension.md):<br>Ses düzeyi<br>alan<br>ağırlık<br>bilgi (örn: bit/bayt)<br>Uzunluk (örn: ölçüm)<br>Hız (örn. mil/saat)  |    -   |
[E-posta](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Sayı](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Sıralı](luis-reference-prebuilt-ordinal.md)   |    -   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Yüzde](luis-reference-prebuilt-percentage.md)   |    -   |
[Kişi adı](luis-reference-prebuilt-person.md)   |    -   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Sıcaklık](luis-reference-prebuilt-temperature.md):<br>Fahrenhayt<br>Kelvin<br>Rankine dili<br>Delisle<br>çevirip   |    -   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

[Kullanım dışı bırakılan varlıklarda](luis-reference-prebuilt-deprecated.md) notlara bakın

## <a name="turkish-entity-support"></a>Türkçe varlık desteği

| Önceden oluşturulmuş varlık | tr-tr |
| --------------- | :---: |
[Yaş](luis-reference-prebuilt-age.md):<br>yıl<br>ay<br>hafta<br>gün   |    -   |
[Para birimi (para)](luis-reference-prebuilt-currency.md):<br>Doları<br>kesir birimi (örn: kuruş)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>dateRange<br>time<br>timerange   |    -   |
[Boyut](luis-reference-prebuilt-dimension.md):<br>Ses düzeyi<br>alan<br>ağırlık<br>bilgi (örn: bit/bayt)<br>Uzunluk (örn: ölçüm)<br>Hız (örn. mil/saat)  |    -   |
[E-posta](luis-reference-prebuilt-email.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   |
[Sayı](luis-reference-prebuilt-number.md)   |    -   |
[Sıralı](luis-reference-prebuilt-ordinal.md)   |    -   |
[Yüzde](luis-reference-prebuilt-percentage.md)   |    -   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    -   |
[Sıcaklık](luis-reference-prebuilt-temperature.md):<br>Fahrenhayt<br>Kelvin<br>Rankine dili<br>Delisle<br>çevirip   |    -   |
[URL](luis-reference-prebuilt-url.md)   |    -   |

<!---
See notes on [Deprecated prebuilt entities](luis-reference-prebuilt-deprecated.md)
KeyPhrase is not available.
-->

## <a name="contribute-to-prebuilt-entity-cultures"></a>Önceden oluşturulmuş varlık külbilgilerine katkıda bulunun
Önceden oluşturulmuş varlıklar Recognizers-Text açık kaynak projede geliştirilir. Projeye [katkıda bulunun](https://github.com/Microsoft/Recognizers-Text) . Bu proje, kültür başına para birimi örnekleri içerir.

GeographyV2 ve PersonName Recognizers-Text projesine dahil değildir. Önceden oluşturulmuş bu varlıklarla ilgili sorunlar için lütfen bir [destek isteği](../../azure-portal/supportability/how-to-create-azure-support-request.md)açın.

## <a name="next-steps"></a>Sonraki adımlar

[Sayı](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md)ve [para birimi](luis-reference-prebuilt-currency.md) varlıkları hakkında bilgi edinin.
