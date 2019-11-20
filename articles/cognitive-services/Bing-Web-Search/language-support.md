---
title: Dil desteği-Bing Web Araması API'si
titleSuffix: Azure Cognitive Services
description: Bing Haber Arama API'si tarafından desteklenen doğal dillerin, ülkelerin ve bölgelerin listesi.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: 9425de6e75a9a46d71ff85ce49b0650c8e7a9a16
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882681"
---
# <a name="language-and-region-support-for-the-bing-web-search-api"></a>Bing Web Araması API'si için dil ve bölge desteği

Bing Web Araması API'si, üç düzine ülke veya bölgeyi destekler ve birden çok dili vardır. Bir ülkeyi veya bölgeyi bir sorgu ile belirtmek, bu ülkeye veya bölgelere ilgi alanları temelinde arama sonuçlarının iyileştirmesine yardımcı olur. Sonuçlar Bing bağlantıları içerebilir ve bu bağlantılar, belirtilen ülkeye/bölgeye veya dile göre Bing Kullanıcı deneyimini yerelleştiremeyebilir.

`cc` Sorgu parametresini kullanarak bir ülke veya bölge belirtebilirsiniz. Ülke veya bölge belirtildiğinde, [ `Accept-Language` üst](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#headers)bilgiyle bir veya daha fazla dil kodu belirtmeniz gerekir. Her pazarda desteklenen dillerin bir listesi için [pazarlar tablosunu](#markets) kullanın.

Alternatif olarak, bir Pazar, `mkt` sorgu parametresi ve **pazarlar** tablosundan bir kod ile de belirtebilirsiniz. Bir pazar aynı anda belirtildiğinde bir ülke veya bölgeyi ve tercih edilen dili belirtir. Dili `setLang` sorgu parametresiyle açıkça ayarlayabilirsiniz.

## <a name="countriesregions"></a>Ülkeler/bölgeler

|Ülke/bölge|Kod|
|-------|----|
|Arjantin|AR|
|Avustralya|Otomatik Olarak Güncelleştir|
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
|Endonezya|id|
|İtalya|IT|
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

## <a name="markets"></a>Pazar

|Ülke/bölge|Dil|Pazar kodu|
|-------|--------|-----------|
|Arjantin|İspanyolca|es-AR|
|Avustralya|Türkçe|tr-AU|
|Avusturya|Almanca|de-AT|
|Belçika|Felemenkçe|nl-|
|Belçika|Fransızca|fr-of|
|Brezilya|Portekizce|pt-BR|
|Kanada|Türkçe|CA tr|
|Kanada|Fransızca|fr-CA|
|Şili|İspanyolca|es-CL|
|Danimarka|Danca|v-DK|
|Finlandiya|Fince|FI-FI|
|Fransa|Fransızca|fr-FR|
|Almanya|Almanca|de-DE|
|Hong Kong SAR|Geleneksel Çince|zh-HK|
|Hindistan|Türkçe|tr-giriş|
|Endonezya|Türkçe|En-KIMLIĞI|
|İtalya|İtalyanca|İt-IT|
|Japonya|Japonca|ja-JP|
|Güney Kore|Korece|ko-KR|
|Malezya|Türkçe|En-MY|
|Meksika|İspanyolca|es-MX|
|Hollanda|Felemenkçe|NL-NL|
|Yeni Zelanda|Türkçe|tr NZ|
|Norveç|Norveççe|Hayır-Hayır|
|Çin|Çince|zh-CN|
|Polonya|Lehçe|pl-PL|
|Portekiz|Portekizce|pt-PT|
|Filipinler|Türkçe|En-PH|
|Rusya|Rusça|ru-RU|
|Suudi Arabistan|Arapça|ar-SA|
|Güney Afrika|Türkçe|En-ZA|
|İspanya|İspanyolca|es-ES|
|İsveç|İsveççe|sv-SE|
|İsviçre|Fransızca|FR-CH|
|İsviçre|Almanca|de-CH|
|Tayvan|Geleneksel Çince|zh-TW|
|Türkiye|Türkçe|tr-TR|
|Birleşik Krallık|Türkçe|en-GB|
|Amerika Birleşik Devletleri|Türkçe|en-US|
|Amerika Birleşik Devletleri|İspanyolca|es-US|

## <a name="next-steps"></a>Sonraki adımlar

* [Bing Resim Arama API’si başvurusu](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
