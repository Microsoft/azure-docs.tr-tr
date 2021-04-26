---
title: Yazım denetimi Ekle
titleSuffix: Azure Cognitive Search
description: Sorguyu yürütmeden önce sorgu koşullarında yazım hatalarını giderecek şekilde sorgu işlem hattına yazım denetimi ekleyin.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/26/2021
ms.custom: references_regions
ms.openlocfilehash: 52ac3ee4ea2f71e285d21c7b6d082e84fa090da1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105625917"
---
# <a name="add-spell-check-to-queries-in-cognitive-search"></a>Bilişsel Arama sorgulara yazım denetimi ekleme

> [!IMPORTANT]
> Yazım denetimi, yalnızca önizleme REST API aracılığıyla kullanılabilen genel önizlemededir. Önizleme özellikleri, olduğu gibi, [ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)altında sunulur. İlk önizleme başlatma sırasında, yazım denetleyicisi için ücret alınmaz. Daha fazla bilgi için bkz. [kullanılabilirlik ve fiyatlandırma](semantic-search-overview.md#availability-and-pricing).

Arama altyapısına ulaşmadan önce tek tek arama sorgu koşullarını yazım içinde düzelterek geri çekmeyi geliştirebilirsiniz. **Yazım denetleyicisi** parametresi tüm sorgu türleri için desteklenir: [basit](query-simple-syntax.md), [tam](query-lucene-syntax.md)ve şu anda genel önizlemede olan yeni [anlam](semantic-how-to-query-request.md) seçeneği.

## <a name="prerequisites"></a>Önkoşullar

+ Ingilizce içerik içeren mevcut bir arama dizini. Şu anda yazım denetimi, [eş anlamlılar](search-synonyms.md)ile çalışmaz. Herhangi bir alan tanımında bir eş anlamlı eşleme belirten dizinlerde kullanmaktan kaçının.

+ Sorgu göndermek için bir arama istemcisi

  Arama istemcisinin, sorgu isteğinde önizleme REST API 'Leri desteklemesi gerekir. Önizleme API 'Lerine GERI çağrı yapmak için değiştirdiğiniz [Postman](search-get-started-rest.md), [Visual Studio Code](search-get-started-vs-code.md)veya kodu kullanabilirsiniz.

+ Yazım düzeltmesini kullanan [bir sorgu isteğinde](/rest/api/searchservice/preview-api/search-documents) "api-Version = 2020-06 -30-Preview", "Yazım denetleyicisi = sözlük" ve "QueryLanguage = en-US" bulunur.

  Yazım denetleyicisi için queryLanguage gereklidir ve şu anda "en-US" geçerli değerdir.

> [!Note]
> Yazım denetleyicisi parametresi, semantik arama sağlayan aynı bölgelerdeki tüm katmanlarda kullanılabilir. Bu Önizleme özelliğine erişim için kaydolmanız gerekmez. Daha fazla bilgi için bkz. [kullanılabilirlik ve fiyatlandırma](semantic-search-overview.md#availability-and-pricing).

## <a name="spell-correction-with-simple-search"></a>Basit aramayla yazım denetimi yapma

Aşağıdaki örnek, basit bir ücretsiz form metin sorgusunda yazım düzeltmesini göstermek için yerleşik oteller-örnek dizinini kullanır. Yazım denetimi olmadan sorgu sıfır sonuç döndürür. Düzeltme ile sorgu, Johnson 'un aile odaklı için bir sonuç döndürür.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "famly acitvites",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "queryType": "simple",
    "select": "HotelId,HotelName,Description,Category,Tags",
    "count": true
}
```

## <a name="spell-correction-with-full-lucene"></a>Tam Lucene ile yazım denetimi

Yazım denetimi, analiz eden tek tek sorgu terimlerinde oluşur. bu nedenle, yazım denetleyicisi parametresini bazı Lucene sorgularıyla kullanabilirsiniz, ancak diğerleri değildir.

+ Metin analizini atlayan uyumsuz sorgu formları şunları içerir: joker karakter, Regex, bulanık
+ Uyumlu sorgu formları şunları içerir: alanlu arama, yakınlık, terim artırma

Bu örnek kategori alanı üzerinde, tam Lucene söz dizimi ve yanlış yazılmış bir sorgu terimi ile alanları oluşturulmuş aramayı kullanır. Yazım denetleyicisi dahil ederek, "Suiite" içindeki yazım hatası düzeltilir ve sorgu başarılı olur.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "Category:(Resort and Spa) OR Category:Suiite",
    "queryType": "full",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "select": "Category",
    "count": true
}
```

## <a name="spell-correction-with-semantic-search"></a>Anlamsal arama ile yazım denetimi

Bu sorgu, bunlardan biri dışında her dönemde yazım hatası, ilgili sonuçları döndürmek için de yazım denetimi düzeltmeleri sağlar. Daha fazla bilgi için bkz. [anlamsal sorgu oluşturma](semantic-how-to-query-request.md).

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview     
{
    "search": "hisotoric hotell wiht great restrant nad wiifi",
    "queryType": "semantic",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "searchFields": "HotelName,Tags,Description",
    "select": "HotelId,HotelName,Description,Category,Tags",
    "count": true
}
```

## <a name="language-considerations"></a>Dil konuları

Yazım denetleyicisi için gereken queryLanguage parametresi, dizin şemasında alan tanımlarına atanan [dil Çözümleyicileri](index-add-language-analyzers.md) ile tutarlı olmalıdır. 

+ queryLanguage, yazım denetimi için hangi nacons kullanıldığını belirler ve "queryType = anlam" kullanıyorsanız [anlamsal derecelendirme algoritmasına](semantic-answers.md) giriş olarak da kullanılır.

+ Dil Çözümleyicileri, arama dizininde eşleşen belgeleri bulmak için dizin oluşturma ve sorgu yürütme sırasında kullanılır. Dil Çözümleyicisi kullanan alan tanımına bir örnek `"name": "Description", "type": "Edm.String", "analyzer": "en.microsoft"` .

Yazım denetleyicisi kullanırken en iyi sonuçlar için, queryLanguage "en-US" ise, herhangi bir dil Çözümleyicileri de Ingilizce bir varyant ("en. Microsoft" veya "en. Lucene") olmalıdır.

> [!NOTE]
> Dilden bağımsız çözümleyiciler (anahtar sözcük, basit, standart, durdurma, boşluk veya `standardasciifolding.lucene` ) QueryLanguage ayarları ile çakışmaz.

Bir sorgu isteğinde, ayarladığınız queryLanguage, yazım denetleyicisi, yanıtlar ve açıklamalı alt yazılar ile aynı şekilde uygulanır. Ayrı parçalar için geçersiz kılma yoktur.

Bir arama dizinindeki içerik birden çok dilde birleştirileken, sorgu girişi büyük olasılıkla birdir. Arama altyapısı, queryLanguage, Language Analyzer ve içeriğin oluştuğu dilin uyumluluğunu kontrol etmez, bu nedenle yanlış sonuçlar üretmemek için sorguları uygun şekilde kapsamdığınızdan emin olun.

## <a name="next-steps"></a>Sonraki adımlar

+ [Anlamsal sorgu oluşturma](semantic-how-to-query-request.md)
+ [Temel bir sorgu oluşturma](search-query-create.md)
+ [Tam Lucene sorgu söz dizimini kullan](query-Lucene-syntax.md)
+ [Basit sorgu söz dizimi kullan](query-simple-syntax.md)
+ [Anlamsal aramaya genel bakış](semantic-search-overview.md)