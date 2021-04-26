---
title: Dil desteği-Bing Haber Arama API'si
titleSuffix: Azure Cognitive Services
description: Bing Haber Arama API'si tarafından desteklenen doğal dillerin, ülkelerin ve bölgelerin listesi.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: 9c7fd03c2239cea05dc79ad4dd1965fe253a2ce9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96341606"
---
# <a name="language-and-region-support-for-the-bing-news-search-api"></a>Bing Haber Arama API'si için dil ve bölge desteği

> [!WARNING]
> Bing Arama API'leri bilişsel hizmetlerden Bing Arama hizmetlere taşınıyor. **30 ekim 2020 ' den** itibaren, [burada](/bing/search-apis/bing-web-search/create-bing-search-service-resource)belgelenen işlem sonrasında Bing arama yeni örneklerin sağlanması gerekir.
> Bilişsel hizmetler kullanılarak sağlanan Bing Arama API'leri, sonraki üç yıl boyunca veya Kurumsal Anlaşma sonuna kadar, hangisi önce gerçekleşene kadar desteklenecektir.
> Geçiş yönergeleri için bkz. [Bing arama Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Bing Haber Arama API'si, birden fazla dili olan çok sayıda ülkeyi/bölgeyi destekler. Bir ülkeyi/bölgeyi bir sorgu ile belirtmek, birincil olarak bu ülkenin/bölgedeki ilgi alanları temelinde arama sonuçlarını iyileştirmesini sağlar. Ayrıca, sonuçlar Bing bağlantıları içerebilir ve bu bağlantılar, belirtilen ülkeye/bölgeye veya dile göre Bing Kullanıcı deneyimini yerelleştiremeyebilir.

Sorgu parametresini kullanarak bir ülke/bölge belirtebilirsiniz `cc` . Bir ülke/bölge belirtirseniz, http üstbilgisini kullanarak bir veya daha fazla dil kodu da belirtmeniz gerekir `Accept-Language` . Desteklenen diller, countr/bölgeye göre farklılık gösterir. Bunlar, pazarlar tablosundaki her bir ülke/bölge için verilir.

Alternatif olarak, `mkt` sorgu parametresini ve **pazarlar** tablosundan bir kodu kullanarak pazarı de belirtebilirsiniz. Bir pazar aynı anda belirtildiğinde bir ülke/bölge ve tercih edilen dil belirtilir. `setLang`Sorgu parametresi bu durumda bir dil koduna ayarlanabilir. Bu, genellikle `mkt` Kullanıcı Bing 'i başka bir dilde görmeyi tercih etmediği müddetçe tarafından belirtilen dildir.

## <a name="supported-markets-for-news-search-endpoint"></a>Haber arama uç noktası için desteklenen pazarlar

`/news/search`Uç nokta için aşağıdaki tabloda, sorgu parametresini belirtmek için kullanabileceğiniz piyasa kodu değerleri listelenmektedir `mkt` . Bing yalnızca bu pazarlar için içerik döndürür. Liste değişebilir.  

Sorgu parametresinde belirtebileceğiniz ülke/bölge kodlarının bir listesi için `cc` bkz. [Ülke kodları](#countrycodes).  

|Ülke/bölge|Dil|Pazar kodu|  
|---------------------|--------------|-----------------|
|Danimarka|Danca|da-DK|
|Avusturya|Almanca|de|
|İsviçre|Almanca|devre dışı|
|Almanya|Almanca|de-DE|
|Avustralya|İngilizce|En-AU|
|Kanada|İngilizce|en-CA|
|Birleşik Krallık|İngilizce|en-GB|
|Endonezya|İngilizce|En-KIMLIĞI|
|İrlanda|İngilizce|en-IE|
|Hindistan|İngilizce|En-ın|
|Malezya|İngilizce|En-MY|
|Yeni Zelanda|İngilizce|En-NZ|
|Filipinler Cumhuriyeti|İngilizce|En-PH|
|Singapur|İngilizce|En-SG|
|Birleşik Devletler|İngilizce|en-US|
|İngilizce|genel|En-WW|
|İngilizce|genel|En-XA|
|Güney Afrika|İngilizce|En-ZA|
|Arjantin|İspanyolca|es-AR|
|Şili|İspanyolca|es-CL|
|İspanya|İspanyolca|es-ES|
|Meksika|İspanyolca|es-MX|
|Birleşik Devletler|İspanyolca|es-US|
|İspanyolca|genel|es-XL|
|Finlandiya|Fince|fi-FI|  
|Fransa|Fransızca|fr-of|
|Kanada|Fransızca|fr-CA|
|Belçika|Felemenkçe|nl-|
|İsviçre|Fransızca|fr-CH|
|Fransa|Fransızca|fr-FR|  
|İtalya|İtalyanca|it-IT|
|Hong Kong ÖİB|Geleneksel Çince|zh-HK|  
|Tayvan|Geleneksel Çince|zh-TW|
|Japonya|Japonca|ja-JP|  
|Güney Kore|Korece|ko-KR|  
|Hollanda|Felemenkçe|nl-NL|  
|Çin Halk Cumhuriyeti|Çince|zh-CN|  
|Brezilya|Portekizce|pt-BR|
|Rusya|Rusça|ru-RU|  
|İsveç|İsveççe|sv-SE|  
|Türkiye|Türkçe|tr-TR|  

## <a name="supported-markets-for-news-endpoint"></a>Haber uç noktası için desteklenen pazarlar
`/news`Uç nokta için aşağıdaki tabloda, sorgu parametresini belirtmek için kullanabileceğiniz piyasa kodu değerleri listelenmektedir `mkt` . Bing yalnızca bu pazarlar için içerik döndürür. Liste değişebilir.  

Sorgu parametresinde belirtebileceğiniz ülke/bölge kodlarının bir listesi için `cc` bkz. [Ülke kodları](#countrycodes).  

|Ülke/bölge|Dil|Pazar kodu|  
|---------------------|--------------|-----------------|
|Danimarka|Danca|da-DK|
|Almanya|Almanca|de-DE|
|Avustralya|İngilizce|En-AU|
|Birleşik Krallık|İngilizce|en-GB|
|Birleşik Devletler|İngilizce|en-US|
|İngilizce|genel|En-WW|
|Şili|İspanyolca|es-CL|
|Meksika|İspanyolca|es-MX|
|Birleşik Devletler|İspanyolca|es-US|
|Finlandiya|Fince|fi-FI|  
|Kanada|Fransızca|fr-CA|
|Fransa|Fransızca|fr-FR|  
|İtalya|İtalyanca|it-IT|
|Brezilya|Portekizce|pt-BR|
|Çin Halk Cumhuriyeti|Çince|zh-CN|

## <a name="supported-markets-for-news-trending-endpoint"></a>Haber eğilim uç noktası için desteklenen pazarlar
`/news/trendingtopics`Uç nokta için aşağıdaki tabloda, sorgu parametresini belirtmek için kullanabileceğiniz piyasa kodu değerleri listelenmektedir `mkt` . Bing yalnızca bu pazarlar için içerik döndürür. Liste değişebilir.  

Sorgu parametresinde belirtebileceğiniz ülke/bölge kodlarının bir listesi için `cc` bkz. [Ülke kodları](#countrycodes).  

|Ülke/bölge|Dil|Pazar kodu|  
|---------------------|--------------|-----------------|
|Almanya|Almanca|de-DE|
|Avustralya|İngilizce|En-AU|
|Birleşik Krallık|İngilizce|en-GB|
|Birleşik Devletler|İngilizce|en-US|
|Kanada|İngilizce|en-CA|
|Hindistan|İngilizce|En-ın|
|Fransa|Fransızca|fr-FR|
|Kanada|Fransızca|fr-CA|
|Brezilya|Portekizce|pt-BR|
|Çin Halk Cumhuriyeti|Çince|zh-CN|


<a name="countrycodes"></a>   
### <a name="country-codes"></a>Ülke kodları  

Sorgu parametresinde belirtebileceğiniz ülke/bölge kodları aşağıda verilmiştir `cc` . Liste değişebilir.  

|Ülke/bölge|Ülke kodu|  
|---------------------|------------------|  
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
|Hong Kong ÖİB|HK|  
|Hindistan|IN|  
|Endonezya|ID|  
|İtalya|BT|  
|Japonya|JP|  
|Güney Kore|KR|  
|Malezya|MY|  
|Meksika|MX|  
|Hollanda|NL|  
|Yeni Zelanda|NZ|  
|Norveç|NO|  
|Çin Halk Cumhuriyeti|CN|  
|Polonya|PL|  
|Portekiz|PT|  
|Filipinler Cumhuriyeti|PH|  
|Rusya|RU|  
|Suudi Arabistan|SA|  
|Güney Afrika|ZA|  
|İspanya|ES|  
|İsveç|SE|  
|İsviçre|CH|  
|Tayvan|TW|  
|Türkiye|TR|  
|Birleşik Krallık|GB|  
|Birleşik Devletler|ABD|

## <a name="next-steps"></a>Sonraki adımlar
Bing Haber Arama uç noktaları hakkında daha fazla bilgi için bkz. [Haber Arama API v7 Reference](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference).