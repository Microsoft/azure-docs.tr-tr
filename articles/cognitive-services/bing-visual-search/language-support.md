---
title: Dil desteği-Bing Görsel Arama API'si
titleSuffix: Azure Cognitive Services
description: Bing Görsel Arama API'si tarafından desteklenen doğal dillerin, ülkelerin ve bölgelerin listesi. Bing Görsel Arama API'si, birden fazla dilden çok sayıda ülkeyi/bölgeyi destekler.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: scottwhi
ms.openlocfilehash: b17341bc234ff3dfecc2c6dcd84ef77116a95d61
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883542"
---
# <a name="language-and-region-support-for-the-bing-visual-search-api"></a>Bing Görsel Arama API'si için dil ve bölge desteği

Bing Görsel Arama API'si, birden fazla dilden çok sayıda ülkeyi/bölgeyi destekler. Her istek, kullanıcının ülke/bölge ve tercih edilen dilini içermelidir. Kullanıcının pazarını bilmek, Bing 'in uygun sonuçlara dönüşmesini sağlar. Bir ülke/bölge ve dil belirtmezseniz, Bing kullanıcının ülke/bölge ve dilini belirlemek için en iyi çabayı sağlar. Sonuçlar Bing bağlantıları içerebileceğinden, Kullanıcı Bing bağlantılarına tıkladığında ülke/bölge ve dil tercih edilen yerelleştirilmiş Bing Kullanıcı deneyimi sağlayabilir.

Ülke/bölge ve dil belirtmek için, `mkt` (Pazar) sorgu parametresini aşağıdaki **pazarlar** tablosundan bir koda ayarlayın. Pazar, hem ülke/bölge hem de dili belirtir. Kullanıcı farklı bir dilde görüntüleme metnini görmeyi tercih ediyorsa, sorgu parametresini uygun dil `setLang` koduna ayarlayın.

Alternatif olarak, ülke/bölge ' yi `cc` sorgu parametresini kullanarak belirtebilirsiniz. Bir ülke/bölge belirtirseniz, `Accept-Language` http üstbilgisini kullanarak bir veya daha fazla dil kodu da belirtmeniz gerekir. Desteklenen diller ülkeye/bölgeye göre farklılık gösterir. Bunlar, pazarlar tablosundaki her bir ülke için verilir.



> [!NOTE]
> Aşağıdaki Pazar kısıtlamaları geçerlidir:
>
> - Görüntü tanıma ek açıklamaları yalnızca Ingilizce olarak kullanılabilir.
> - İçgörüler, alışveriş ve sayfalar dahil Öngörüler yalnızca en-US pazarında sunulmaktadır.


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
