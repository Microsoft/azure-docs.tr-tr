---
title: Dil desteği - Bing özel arama API'si
titleSuffix: Azure Cognitive Services
description: Bing özel arama API'si için desteklenen diller ve bölgeler listesi.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: aahi
ms.openlocfilehash: 85326ae9166f7ea15ec2f45c01755b8f9ef03aff
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66388574"
---
# <a name="language-and-region-support-for-the-bing-custom-search-api"></a>Bing özel arama API'si için dil ve bölge desteği

Bing özel arama API'si, birden fazla üç düzine ülkeler/bölgeler, çoğu birden fazla dili destekler.

İsteğe bağlı olsa da, istek belirtmelidir [mkt](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#mkt) gelen sonuçların istediğiniz Pazar tanımlayan sorgu parametresi. İsteğe bağlı sorgu parametreleri listesi için bkz. [sorgu parametreleri](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters)

Kullanarak bir ülke/bölge belirtin `cc` sorgu parametresi. Bir ülke/bölge belirtirseniz, kullanarak bir veya daha fazla dil kodunu da belirtmeniz gerekir `Accept-Language` başlığı. Desteklenen diller, ülke/bölgeye göre değişiklik gösterir; Her ülkesinde/bölgesinde için verilen **pazarlara** tablo.

`Accept-Language` Üstbilgi ve `setLang` sorgu parametresi karşılıklı olarak birbirini dışlar — her ikisini birden belirtmeyin. Ayrıntılar için bkz [Accept-Language](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#acceptlanguage).

## <a name="countriesregions"></a>Ülkeler/bölgeler

|Ülke/bölge|Kod|
|-------|----|
|Arjantin|AR|
|Avustralya|AU|
|Avusturya|AT|
|Belçika|BE|
|Brezilya|BR|
|Kanada|CA|
|Şili|CL|
|Danimarka|DK|
|Finlandiya|FI|
|Fransa|GS|
|Almanya|DE|
|Hong Kong SAR|HK|
|Hindistan|IN|
|Endonezya|Kimlik|
|İtalya|BT|
|Japonya|JP|
|Güney Kore|KR|
|Malezya|MY|
|Meksika|MX|
|Hollanda|NL|
|Yeni Zelanda|NZ|
|Norveç|NO|
|Çin|CN|
|Polonya|PL|
|Portekiz|PT|
|Filipinler|PH|
|Rusya|RU|
|Suudi Arabistan|SA|
|Güney Afrika|ZA|
|İspanya|ES|
|İsveç|SE|
|İsviçre|CH|
|Tayvan|TW|
|Türkiye|TR|
|Birleşik Krallık|GB|
|Amerika Birleşik Devletleri|ABD|


## <a name="markets"></a>Pazarlar

|Ülke/bölge|Dil|Pazar kod|
|-------|--------|-----------|
|Arjantin|İspanyolca|es-AR|
|Avustralya|Türkçe|tr-AU|
|Avusturya|Almanca|de-AT|
|Belçika|Felemenkçe|NL-olabilir|
|Belçika|Fransızca|FR-olabilir|
|Brezilya|Portekizce|pt-BR|
|Kanada|Türkçe|CA tr|
|Kanada|Fransızca|fr-CA|
|Şili|İspanyolca|ES-CL|
|Danimarka|Danca|v-DK|
|Finlandiya|Fince|FI-FI|
|Fransa|Fransızca|fr-FR|
|Almanya|Almanca|de-DE|
|Hong Kong ÖİB|Geleneksel Çince|zh-HK|
|Hindistan|Türkçe|tr-giriş|
|Endonezya|Türkçe|tr kimliği|
|İtalya|İtalyanca|İt-IT|
|Japonya|Japonca|ja-JP|
|Güney Kore|Korece|ko-KR|
|Malezya|Türkçe|MY tr|
|Meksika|İspanyolca|es-MX|
|Hollanda|Felemenkçe|NL-NL|
|Yeni Zelanda|Türkçe|tr NZ|
|Norveç|Norveççe|no-NO|
|Çin|Çince|zh-CN|
|Polonya|Lehçe|pl-PL|
|Portekiz|Portekizce|pt-PT|
|Filipinler|Türkçe|tr PH|
|Rusya|Rusça|ru-RU|
|Suudi Arabistan|Arapça|ar-SA|
|Güney Afrika|Türkçe|tr-ZA|
|İspanya|İspanyolca|es-ES|
|İsveç|İsveççe|sv-SE|
|İsviçre|Fransızca|FR-CH|
|İsviçre|Almanca|de-CH|
|Tayvan|Geleneksel Çince|zh-TW|
|Türkiye|Türkçe|tr-TR|
|Birleşik Krallık|Türkçe|en-GB|
|Amerika Birleşik Devletleri|Türkçe|en-US|
|Amerika Birleşik Devletleri|İspanyolca|ES-ABD|
