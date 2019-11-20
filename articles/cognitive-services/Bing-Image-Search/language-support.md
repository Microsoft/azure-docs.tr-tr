---
title: Dil desteği-Bing Resim Arama API'si
titleSuffix: Azure Cognitive Services
description: Bing Resim Arama API'si hangi ülkelerin/bölgelerin ve dillerin desteklendiğini öğrenin.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: ca3821b6088e45730334d1b0971e270b1d86dfce
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881915"
---
# <a name="language-and-region-support-for-the-bing-image-search-api"></a>Bing Resim Arama API'si için dil ve bölge desteği

Bing Resim Arama API'si, birden fazla dilden çok sayıda ülkeyi/bölgeyi destekler. Bir ülkeyi/bölgeyi bir sorgu ile belirtmek, birincil olarak bu ülkenin/bölgedeki ilgi alanları temelinde arama sonuçlarını iyileştirmesini sağlar. Ayrıca, sonuçlar Bing bağlantıları içerebilir ve bu bağlantılar, belirtilen ülkeye/bölgelere veya dile göre Bing Kullanıcı deneyimini yerelleştiremeyebilir.

Ülke/bölge ve dil belirtmek için, `mkt` (Pazar) sorgu parametresini aşağıdaki **pazarlar** tablosundan bir koda ayarlayın. Pazar, hem ülke/bölge hem de dili belirtir. Kullanıcı farklı bir dilde görüntüleme metnini görmeyi tercih ediyorsa, sorgu parametresini uygun dil `setLang` koduna ayarlayın.

Alternatif olarak, ülke/bölge ' yi `cc` sorgu parametresini kullanarak belirtebilirsiniz. Bir ülke/bölge belirtirseniz, `Accept-Language` http üstbilgisini kullanarak bir veya daha fazla dil kodu da belirtmeniz gerekir. Desteklenen diller ülkeye/bölgeye göre farklılık gösterir. Bunlar, pazarlar tablosundaki her bir ülke/bölge için verilir.

> [!NOTE]
> Popüler resimler API 'SI Şu anda yalnızca aşağıdaki pazarları desteklemektedir:
> - en-US (Ingilizce, Birleşik Devletler)
> - en-CA (Ingilizce, Kanada)
> - En-AU (Ingilizce, Avustralya)
> - zh-CN (Çince, Çin)

## <a name="countriesregions"></a>Ülkeler/Bölgeler

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
Bing Haber Arama uç noktaları hakkında daha fazla bilgi için bkz. [News resım arama API v7 Reference](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference).
