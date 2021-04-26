---
title: Sorgu türleri
titleSuffix: Azure Cognitive Search
description: Serbest metin, filtre, otomatik tamamlama ve öneriler, coğrafi arama, sistem sorguları ve belge arama gibi Bilişsel Arama desteklenen sorgu türleri hakkında bilgi edinin.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 21012848ba3624df6110eaea182beccc4646d234
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105609284"
---
# <a name="querying-in-azure-cognitive-search"></a>Azure Bilişsel Arama sorgulama

Azure Bilişsel Arama, ücretsiz metin aramasından yüksek düzeyde belirtilen sorgu desenlerine kadar çok sayıda senaryoyu desteklemek için zengin bir sorgu dili sunar. Bu makalede sorgu istekleri ve oluşturabileceğiniz sorgu türleri açıklanmaktadır.

Bilişsel Arama bir sorgu, **`search`** her ikisinin de sorgu yürütmesini bilgilendirmesini ve yanıtı şekillendirip geri geldiğini bildiren bir gidiş dönüş işleminin tam belirtimidir. Parametreler ve Çözümleyicileri sorgu isteği türünü tespit. Aşağıdaki sorgu örneği,, [otel-örnek-dizin](search-get-started-portal.md) belgeleri koleksiyonunu hedefleyen [arama belgelerini (REST API)](/rest/api/searchservice/search-documents)kullanarak Boole operatörü olan bir boş metin sorgusudur.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "queryType": "simple",
    "searchMode": "all",
    "search": "restaurant +view",
    "searchFields": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "select": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "top": "10",
    "count": "true",
    "orderby": "Rating desc"
}
```

Sorgu yürütme sırasında kullanılan parametreler şunları içerir:

+ **`queryType`**[varsayılan basit sorgu ayrıştırıcı](search-query-simple-examples.md) (tam metin araması için en uygun) veya normal ifadeler, yakınlık araması, belirsiz ve joker karakter arama gibi gelişmiş sorgu yapıları için kullanılan [tam Lucene sorgu ayrıştırıcısının](search-query-lucene-examples.md) , birkaç kez ad vermek üzere Ayrıştırıcıyı ayarlar.

+ **`searchMode`** eşleşmelerin ifadede "All" kriterine mi yoksa "Any" ölçütlerine göre mi olduğunu belirtir. Varsayılan değer.

+ **`search`** eşleştirme ölçütünü, genellikle tüm terimleri veya tümceleri, işleçlere sahip veya olmayan bir şekilde sağlar. Dizin şemasında *aranabilir* olarak öznitelikli tüm alanlar, bu parametre için bir adaydır.

+ **`searchFields`** sorgu yürütmesini belirli aranabilir alanlarla kısıtlar. Geliştirme sırasında, seçme ve arama için aynı alan listesinin kullanılması yararlı olur. Aksi takdirde, bir eşleşme, sonuçların neden döndürüldüğünden emin olmak için sonuçlarda görmediğiniz alan değerlerine dayalı olabilir.

Yanıtı şekillendirmek için kullanılan parametreler:

+ **`select`** yanıtta döndürülecek alanları belirtir. Yalnızca dizinde *alınabilir* olarak işaretlenen alanlar, Select ifadesinde kullanılabilir.

+ **`top`** belirtilen en iyi eşleşen belge sayısını döndürür. Bu örnekte, yalnızca 10 isabetler döndürülür. Sonuçları göstermek için top ve Skip (gösterilmez) ' i kullanabilirsiniz.

+ **`count`** Tüm dizindeki birçok belge, döndürülenden daha fazla olabilecek şekilde tamamen eşleşir. 

+ **`orderby`** sonuçları derecelendirme veya konum gibi bir değere göre sıralamak istiyorsanız kullanılır. Aksi takdirde, varsayılan değer, sonuçları derecelendirmek için yakınlık puanı kullanmaktır. Bu parametre için bir aday olması için bir alanın *sıralanabilir* olması gerekir.

Yukarıdaki liste temsilcisidir ancak ayrıntılı değildir. Bir sorgu isteğindeki parametrelerin tam listesi için bkz. [arama belgeleri (REST API)](/rest/api/searchservice/search-documents).

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>Sorgu türleri

Birkaç önemli özel durumu ile, bir sorgu isteği, her türlü arama belgesi içinde potansiyel olarak herhangi bir alanda bulunabilecek hızlı taramalar için yapılandırılmış ters dizinler üzerinde yinelenir. Bilişsel Arama, eşleşmeleri bulmaya yönelik birincil metodoloji tam metin arama veya filtreleridir, ancak otomatik tamamlama veya coğrafi konum arama gibi iyi bilinen diğer arama deneyimlerini de uygulayabilirsiniz. Bu makalenin geri kalanı Bilişsel Arama sorguları özetler ve daha fazla bilgi ve örnek için bağlantılar sağlar.

## <a name="full-text-search"></a>Tam metin araması

Arama uygulamanız, terim girişlerini toplayan bir arama kutusu içeriyorsa, tam metin araması büyük olasılıkla bu deneyimi yedekleyen sorgu işlemidir. Tam metin arama, **`search`** dizininizdeki tüm *aranabilir* alanlardaki bir parametreye geçirilen terimleri veya tümceleri kabul eder. Sorgu dizesindeki isteğe bağlı Boolean işleçleri içerme veya dışlama ölçütlerini belirtebilir. Hem basit ayrıştırıcı hem de tam Ayrıştırıcı tam metin aramasını destekler.

Bilişsel Arama, tam metin araması Apache Lucene sorgu altyapısında oluşturulmuştur. Tam metin aramasında sorgu dizeleri, taramaları daha verimli hale getirmek için sözlü Analize sahiptir. Analiz, tüm terimleri azaltır, "The" gibi durdurma sözcüklerini kaldırır ve temel kök formlara yönelik terimleri azaltır. Varsayılan çözümleyici standart Lucene ' dir.

Eşleşen terimler bulunduğunda sorgu altyapısı, alan değerlerini birleştirmek için belge anahtarını veya KIMLIĞINI kullanarak eşleştirmeyi içeren bir arama belgesi reconstitutes, belgeleri ilgi sırasına göre derecelendirir ve belirtilmişse en üstteki 50 (varsayılan olarak) değerini döndürür **`top`** .

Tam metin araması gerçekleştiriyorsanız, içeriğinizin nasıl simgeleştirilmiş olduğunu anlamak, tüm sorgu anormallarını hata ayıklamanıza yardımcı olur. Hecelenmiş dizeler veya özel karakterler üzerinde sorgular, dizinin doğru belirteçleri içerdiğinden emin olmak için varsayılan standart Lucene dışında bir çözümleyici kullanmayı gerekli olabilir. Varsayılan ayarı, [dil Çözümleyicileri](index-add-language-analyzers.md#language-analyzer-list) veya sözcük temelli analizleri değiştiren [özelleştirilmiş çözümleyiciler](index-add-custom-analyzers.md#built-in-analyzers) ile geçersiz kılabilirsiniz. Bir örnek, bir alanın tüm içeriğini tek bir belirteç olarak ele alan [anahtar sözcüktür](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) . Bu, ZIP kodları, kimlikler ve bazı ürün adları gibi veriler için yararlıdır. Daha fazla bilgi için bkz. [özel karakterlerle kısmi terim arama ve desenleri](search-query-partial-matching.md).

Büyük metin blokları (bir içerik alanı veya uzun açıklamalar) içeren dizinlerde daha büyük olasılıkla, Boolean operatörlerinin ağır kullanımını düşünüyorsanız, **`searchMode=Any|All`** Bu ayarın Boole aramasında etkisini değerlendirmek için sorguları parametresiyle test ettiğinizden emin olun.

## <a name="autocomplete-and-suggested-queries"></a>Otomatik tamamlama ve önerilen sorgular

[Otomatik tamamlama veya önerilen sonuçlar](search-add-autocomplete-suggestions.md) , **`search`** bir arama türü deneyimindeki kısmi dize girdilerine (her bir karakterden sonra) göre art arda yapılan sorgu isteklerinin tetikleneceği alternatiflerdir. **`autocomplete`** **`suggestions`** [Bu öğreticide](tutorial-csharp-type-ahead-and-suggestions.md)açıklandığı gibi, ve parametresini birlikte veya ayrı ayrı kullanabilirsiniz, ancak ile kullanamazsınız **`search`** . Hem tamamlanan terimler hem de önerilen sorgular, Dizin içeriğinden türetilir. Motor, dizininizdeki mevcut olmayan bir dizeyi veya öneriyi hiçbir şekilde döndürmez. Daha fazla bilgi için bkz. [AutoComplete (REST API)](/rest/api/searchservice/autocomplete) ve [öneriler (REST API)](/rest/api/searchservice/suggestions).

## <a name="filter-search"></a>Arama filtrele

Filtreler Bilişsel Arama içeren uygulamalarda yaygın olarak kullanılır. Uygulama sayfalarında, filtreler genellikle kullanıcı tabanlı filtreleme için bağlantı gezinti yapılarında modeller olarak görselleştirilir. Filtreler, dizinlenmiş içeriğin dilimlerini göstermek için dahili olarak da kullanılır. Örneğin, bir ürün kategorisinde filtre kullanarak bir arama sayfası başlatabilir veya bir dizinde hem Ingilizce hem de Fransızca alanları varsa bir dil kullanabilirsiniz.

Ayrıca, aşağıdaki tabloda açıklandığı gibi özelleştirilmiş bir sorgu formunu çağırmak için filtreler de gerekebilir. Belirtilmemiş bir aramayla ( **`search=*`** ) veya terimleri, tümceleri, işleçleri ve desenleri içeren bir sorgu dizesiyle bir filtre kullanabilirsiniz.

| Filtre senaryosu | Description |
|-----------------|-------------|
| Aralık filtreleri | Azure Bilişsel Arama 'de, Aralık sorguları filtre parametresi kullanılarak oluşturulur. Daha fazla bilgi ve örnek için bkz. [Aralık filtresi örneği](search-query-simple-examples.md#example-5-range-filters). |
| Coğrafi konum arama | Aranabilir bir alan [Edm. Geographyıpoint türünde](/rest/api/searchservice/supported-data-types)ise, "yakın beni bul" veya harita tabanlı arama denetimleri için bir filtre ifadesi oluşturabilirsiniz. Coğrafi arama 'nın bulunduğu alanlar koordinatları içerir. Daha fazla bilgi ve örnek için bkz. [coğrafi arama örneği](search-query-simple-examples.md#example-6-geo-search). |
| Çok yönlü gezinme | Bir model gezinti yapısı, bir model üzerindeki bir olaya yanıt olarak bir filtre çağırdığınızda Kullanıcı tarafından yönlendirilen gezinmede de alet haline gelir `onclick` . Bu nedenle, modeller ve filtreler el ile devam ediyor. Model gezintisi eklerseniz, deneyimi tamamlamaya yönelik filtrelere ihtiyacınız olacaktır. Daha fazla bilgi için bkz. [model filtresi oluşturma](search-filters-facets.md). |

> [!NOTE]
> Filtre ifadesinde kullanılan metin, sorgu işleme sırasında çözümlenmez. Metin girişi, eşleşme üzerinde başarılı veya başarısız olan, büyük/küçük harfe duyarlı bir karakter deseninin olduğu varsayılır. Filtre ifadeleri [OData sözdizimi](query-odata-filter-orderby-syntax.md) kullanılarak oluşturulur ve **`filter`** dizininizdeki tüm *filtrelenebilir* alanlara bir parametreye geçirilir. Daha fazla bilgi için bkz. [Azure bilişsel arama filtreler](search-filters.md).

## <a name="document-look-up"></a>Belge arama

Daha önce açıklanan sorgu formlarının aksine, bu, karşılık gelen dizin arama veya tarama olmadan tek bir [arama BELGESINI kimliğe göre](/rest/api/searchservice/lookup-document)alır. Yalnızca bir belge istenir ve döndürülür. Bir Kullanıcı arama sonuçlarında bir öğe seçtiğinde, belgeyi almak ve bir ayrıntı sayfasını alanlarla doldurmak tipik bir yanıt olur ve bir belge görünümü onu destekleyen bir işlemdir.

## <a name="advanced-search-fuzzy-wildcard-proximity-regex"></a>Gelişmiş arama: benzer, joker karakter, yakınlık, Regex

Gelişmiş sorgu formu, belirli bir sorgu davranışını tetikleyen tam Lucene ayrıştırıcısına ve işleçlere bağlıdır.

| Sorgu türü | Kullanım | Örnekler ve daha fazla bilgi |
|------------|--------|------------------------------|
| [Parçalı arama](query-lucene-syntax.md#bkmk_fields) | **`search`**  parametresinin **`queryType=full`**  | Tek bir alanı hedefleyen bir bileşik sorgu ifadesi oluşturun. <br/>[Parçalı arama örneği](search-query-lucene-examples.md#example-1-fielded-search) |
| [benzer arama](query-lucene-syntax.md#bkmk_fuzzy) | **`search`** parametresinin **`queryType=full`** | Benzer bir yapım veya yazım denetimi olan koşullara göre eşleşir. <br/>[Benzer arama örneği](search-query-lucene-examples.md#example-2-fuzzy-search) |
| [yakınlık araması](query-lucene-syntax.md#bkmk_proximity) | **`search`** parametresinin **`queryType=full`** | Belgedeki birbirini yakın terimleri bulur. <br/>[Yakınlık arama örneği](search-query-lucene-examples.md#example-3-proximity-search) |
| [terim artırma](query-lucene-syntax.md#bkmk_termboost) | **`search`** parametresinin **`queryType=full`** | Bir belgeyi, daha fazla olmayan diğerlerine göre, daha yüksek bir dönem içeriyorsa, daha yüksek bir şekilde derecelendirir. <br/>[Terim artırma örneği](search-query-lucene-examples.md#example-4-term-boosting) |
| [normal ifade arama](query-lucene-syntax.md#bkmk_regex) | **`search`** parametresinin **`queryType=full`** | Normal bir ifadenin içeriğine göre eşleşir. <br/>[Normal ifade örneği](search-query-lucene-examples.md#example-5-regex) |
|  [joker karakter veya ön ek arama](query-lucene-syntax.md#bkmk_wildcard) | **`search`*** veya içeren *_`~`_* parametre **`?`****`queryType=full`**| Bir önek ve tilde ( `~` ) veya tek karakter () temelinde eşleşir `?` . <br/>[Joker karakter arama örneği](search-query-lucene-examples.md#example-6-wildcard-search) |

## <a name="next-steps"></a>Sonraki adımlar

Sorgu uygulamasına daha yakından bakmak için, her bir sözdizimi için örnekleri gözden geçirin. Tam metin arama için yeni olduğunuzda, sorgu altyapısının eşit derecede iyi bir seçim yapabileceğini daha yakından inceleyelim.

+ [Basit sorgu örnekleri](search-query-simple-examples.md)
+ [Lucene sözdizimi sorgu gelişmiş sorgular oluşturmaya yönelik örnekler](search-query-lucene-examples.md)
+ [Tam metin aramasının Azure bilişsel arama git 'Te nasıl çalıştığı](search-lucene-query-architecture.md)