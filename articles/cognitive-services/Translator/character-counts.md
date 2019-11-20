---
title: Karakter sayıları-Translator Metin Çevirisi API'si
titleSuffix: Azure Cognitive Services
description: Bu makalede, Azure bilişsel hizmetler 'in içeriği nasıl saydığını anlayabilmeniz için karakterlerin kaç tanesinin Translator Metin Çevirisi API'si olduğu açıklanmaktadır.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: cb70b8624ac0d909511032622948f14f8764b153
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888156"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Translator Metin Çevirisi API'si karakterlerin sayısı

Translator Metin Çevirisi API'si, giriş metninin her Unicode kod noktasını bir karakter olarak sayar. Bir metnin bir dile her çevirisi, isteğin tek bir API çağrısında yapılmış olsa bile birden çok dile çevrilirken ayrı bir çeviri olarak sayılır. Yanıtın uzunluğu önemi değildir.

Hangi sayımlar şunlardır:

* İsteğin gövdesinde Translator Metin Çevirisi API'si geçirilen metin
   * Çeviri, alfabe ve sözlük arama yöntemlerini kullanırken `Text`
   * Sözlük örnekleri metodunu kullanırken `Text` ve `Translation`
* Tüm biçimlendirme: HTML, XML etiketleri, vb. istek gövdesinin metin alanı içinde. İsteği oluşturmak için kullanılan JSON gösterimi (örneğin, "metin:") sayılmaz.
* Tek bir harf
* edilen
* Boşluk, sekme, biçimlendirme ve her türlü boşluk karakteri
* Unicode 'da tanımlanan her kod noktası
* Daha önce aynı metni çevirseniz bile yinelenen çeviri

Çince ve Japonca Kanji gibi ideograms tabanlı betikler için Translator Metin Çevirisi API'si, İdeogram başına bir karakter olan Unicode kod noktalarının sayısını yine de sayacaktır. Özel durum: Unicode yedeklerin kapıları iki karakter olarak sayılır.

İstek, sözcük, bayt veya cümle sayısı karakter sayısında ilgisiz değildir.

Algılama ve Breakcümlesi yöntemlerine yapılan çağrılar, karakter tüketimine göre sayılmaz. Ancak, Algıla ve Breakcümlesi yöntemlerine yapılan çağrıların, sayılan diğer işlevlerin kullanımına yönelik makul bir ORANTA olmasını umuz. Yaptığınız algılama veya Breakcümle çağrılarının sayısı 100 kez diğer sayılan yöntemlerin sayısını aşarsa, Microsoft, Algıla ve Breakcümlesi yöntemlerinin kullanımını kısıtlama hakkını saklı tutar.


Karakter sayıları hakkında daha fazla bilgi için [Microsoft Translator SSS](https://www.microsoft.com/en-us/translator/faq.aspx)' de yer alır.
