---
title: Sorgu türleri ve oluşturma
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama 'de arama sorgusu oluşturmaya yönelik temel bilgiler, sonuçları filtrelemek, seçmek ve sıralamak için parametreler kullanmaktır.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 902f3628235cc8a4524ddc4dd8a5327592fe47e7
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793211"
---
# <a name="query-types-and-composition-in-azure-cognitive-search"></a>Azure Bilişsel Arama 'de sorgu türleri ve bileşimi

Azure Bilişsel Arama, bir sorgu gidiş dönüş işleminin tam belirtimidir. İstekteki parametreler, dizinde belge bulmak için eşleşme ölçütü sağlar, hangi alanları dahil etmek veya hariç tutmak, altyapıya geçilen yürütme yönergeleri ve yanıtı şekillendirmeye yönelik yönergeler sağlar. Belirtilmemiş (`search=*`) bir sorgu, tüm aranabilir alanlara karşı tam metin arama işlemi olarak çalışarak, rastgele bir sonuç kümesi rastgele sırada döner.

Aşağıdaki örnek, [REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)oluşturulan temsili bir sorgudur. Bu örnek, [otel demo dizinini](search-get-started-portal.md) hedefler ve ortak parametreleri içerir.

```
{
    "queryType": "simple" 
    "search": "+New York +restaurant",
    "searchFields": "Description, Address/City, Tags",
    "select": "HotelId, HotelName, Description, Rating, Address/City, Tags",
    "top": "10",
    "count": "true",
    "orderby": "Rating desc"
}
```

+ **`queryType`** , [varsayılan basit sorgu ayrıştırıcısı](search-query-simple-examples.md) (tam metin araması için en uygun) veya normal ifadeler, yakınlık araması, bulanık ve gibi gelişmiş sorgu yapıları için kullanılan [tam Lucene sorgu ayrıştırıcısı](search-query-lucene-examples.md) olan ayrıştırıcısı ayarlar. birkaç tane olmak üzere joker karakter arama.

+ **`search`** , genellikle metin ve genellikle Boolean işleçlere eşlik eden, eşleşme ölçütlerini sağlar. Tek başına terimler, *terim* sorgulardır. Tırnak içine alınmış çok parçalı sorgular *anahtar tümceciği* sorgulardır. Arama, **`search=*`** gibi tanımsız olabilir, ancak büyük olasılıkla örnekte gösterilene benzer hüküm, tümcecik ve işleçlerden oluşur.

+ **`searchFields`** sorgu yürütmeyi belirli alanlara kısıtlar. Dizin şemasında *aranabilir* olarak öznitelikli tüm alanlar, bu parametre için bir adaydır.

Yanıtlar Ayrıca sorguya dahil ettiğiniz parametrelere göre şekillendirilir. Örnekte, sonuç kümesi **`select`** bildiriminde listelenen alanlardan oluşur. $Select bildiriminde yalnızca *alınabilir* olarak işaretlenen alanlar kullanılabilir. Ayrıca, bu sorguda yalnızca **`top`** 10 isabet döndürülür, **`count`** , bu, döndürülenden daha fazla sayıda belge hakkında daha fazla bilgi olabilir. Bu sorguda, satırlar derecelendirmeye göre azalan düzende sıralanır.

Azure Bilişsel Arama 'de sorgu yürütme her zaman bir dizine göre yapılır ve istekte belirtilen bir API anahtarı kullanılarak kimlik doğrulaması yapılır. REST 'de, her ikisi de istek üst bilgilerinde sağlanır.

### <a name="how-to-run-this-query"></a>Bu sorgu nasıl çalıştırılır

Bu sorguyu yürütmek için [Arama Gezgini ve oteller tanıtım dizini](search-get-started-portal.md)' ni kullanın. 

Bu sorgu dizesini gezgin 'in arama çubuğuna yapıştırabilirsiniz: `search=+"New York" +restaurant&searchFields=Description, Address/City, Tags&$select=HotelId, HotelName, Description, Rating, Address/City, Tags&$top=10&$orderby=Rating desc&$count=true`

## <a name="how-query-operations-are-enabled-by-the-index"></a>Sorgu işlemleri dizin tarafından nasıl etkinleştirilir

Dizin tasarımı ve sorgu tasarımı Azure Bilişsel Arama sıkı bir şekilde bağlanmış. En baştan haberdar olmak için önemli bir olgu, *dizin şemasının*her bir alanda bulunan özniteliklerle, oluşturabileceğiniz sorgu türünü belirler. 

Bir alandaki dizin öznitelikleri, izin verilen işlemleri ayarlar; bir alanın dizinde *aranabilir* olup olmadığı, sonuçlarda *alınabilir* , *sıralanabilir*, *filtrelenebilir*ve benzeri. Örnek sorgu dizesinde yalnızca `"$orderby": "Rating"` geçerlidir çünkü derecelendirme alanı dizin şemasında *sıralanabilir* olarak işaretlenir. 

![Otel örneği için Dizin tanımı](./media/search-query-overview/hotel-sample-index-definition.png "Otel örneği için Dizin tanımı")

Yukarıdaki ekran görüntüsü, otel örneği için dizin özniteliklerinin kısmi bir listesidir. Tüm Dizin şemasını portalda görüntüleyebilirsiniz. Dizin öznitelikleri hakkında daha fazla bilgi için bkz. [Create ındex REST API](https://docs.microsoft.com/rest/api/searchservice/create-index).

> [!Note]
> Bazı sorgu işlevleri, her alan temelinde değil, Dizin genelinde etkindir. Bu yetenekler şunlardır: [eş anlamlı haritalar](search-synonyms.md), [özel çözümleyiciler](index-add-custom-analyzers.md), [öneri aracı yapıları (otomatik tamamlama ve önerilen sorgular için)](index-add-suggesters.md), [sıralama sonuçları için Puanlama mantığı](index-add-scoring-profiles.md).

## <a name="elements-of-a-query-request"></a>Bir sorgu isteği öğeleri

Sorgular her zaman tek bir dizine yönlendirilir. Bir sorgu hedefi olarak dizinlere katılamaz veya özel veya geçici veri yapıları oluşturamazsınız. 

Bir sorgu isteğindeki gerekli öğeler aşağıdaki bileşenleri içerir:

+ Sabit ve Kullanıcı tanımlı bileşenleri içeren bir URL olarak ifade edilen hizmet uç noktası ve Dizin belgeleri koleksiyonu: **`https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`**
+ her zaman API 'nin birden fazla sürümü kullanılabildiği için **`api-version`** (yalnızca REST) gereklidir. 
+ bir sorgu ya da yönetici API anahtarı **`api-key`** hizmetinize yönelik isteğin kimliğini doğrular.
+ **`queryType`** basit veya tam, yerleşik varsayılan basit sözdizimi kullanıyorsanız atlanabilir.
+ **`search`** veya **`filter`** , boş bir arama gerçekleştirmek istiyorsanız bu parametre belirtilmeyen bir eşleşme ölçütü sağlar. Her iki sorgu türü de basit ayrıştırıcı açısından ele alınmıştır ancak gelişmiş sorgular bile karmaşık sorgu ifadelerini geçirmek için arama parametresi gerektirir.

Diğer tüm arama parametreleri isteğe bağlıdır. Özniteliklerin tam listesi için bkz. [Dizin oluşturma (REST)](https://docs.microsoft.com/rest/api/searchservice/create-index). İşlemler sırasında parametrelerin nasıl kullanıldığına daha yakından bakmak için, [tam metin aramasının Azure bilişsel arama 'de nasıl çalıştığını](search-lucene-query-architecture.md)görün.

## <a name="choose-apis-and-tools"></a>API 'Leri ve araçları seçin

Aşağıdaki tabloda sorguları göndermek için API 'Ler ve araç tabanlı yaklaşımlar listelenmektedir.

| Yöntem | Açıklama |
|-------------|-------------|
| [Arama Gezgini (portal)](search-explorer.md) | Dizin ve API-sürüm seçimleri için bir arama çubuğu ve seçenekler sağlar. Sonuçlar JSON belgeleri olarak döndürülür. Araştırma, test ve doğrulama için önerilir. <br/>[Daha fazla bilgi edinin.](search-get-started-portal.md#query-index) | 
| [Postman veya diğer REST araçları](search-get-started-postman.md) | Web test araçları, REST çağrılarını formülletmenin çok iyi bir seçimdir. REST API Azure Bilişsel Arama tüm olası işlemleri destekler. Bu makalede, Azure Bilişsel Arama istek göndermek için HTTP istek üst bilgisini ve gövdesini ayarlamayı öğrenin.  |
| [Searchındexclient (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) | Azure Bilişsel Arama dizinini sorgulamak için kullanılabilen istemci.  <br/>[Daha fazla bilgi edinin.](search-howto-dotnet-sdk.md#core-scenarios)  |
| [Belgelerde ara (REST API)](https://docs.microsoft.com/rest/api/searchservice/search-documents) | Ek giriş için sorgu parametrelerini kullanarak bir dizinde GET veya POST yöntemleri.  |

## <a name="choose-a-parser-simple--full"></a>Ayrıştırıcı seçin: basit | tümünü

Azure Bilişsel Arama Apache Lucene üzerinde bulunur ve tipik ve özelleştirilmiş sorguları işlemek için iki sorgu ayrıştırıcıları arasında seçim sağlar. Basit ayrıştırıcı kullanan istekler [basit sorgu söz dizimi](query-simple-syntax.md)kullanılarak formüle sahiptir ve ücretsiz form metin sorgularında kendi hızı ve verimliliği için varsayılan olarak seçilidir. Bu sözdizimi, AND, OR, NOT, tümcecik, sonek ve öncelik işleçlerini içeren bir dizi yaygın arama işlecini destekler.

İsteğe `queryType=full` eklediğinizde etkinleştirilen [tam Lucene sorgu söz dizimi](query-Lucene-syntax.md#bkmk_syntax), [Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)'in bir parçası olarak geliştirilen, yaygın olarak benimsenen ve ifade eden sorgu dilini kullanıma sunar. Tam sözdizimi basit sözdizimini genişletir. Basit sözdizimi için yazdığınız herhangi bir sorgu, tam Lucene ayrıştırıcısı altında çalışır. 

Aşağıdaki örneklerde nokta gösterilmektedir: aynı sorgu, ancak farklı queryType ayarları ile farklı sonuçlar elde edin. İlk sorguda, `historic` sonrasında `^3`, arama teriminin bir parçası olarak kabul edilir. Bu sorgu için en üst dereceye sahip sonuç, açıklamasında *okyanus* olan "Marquis plaza & paketleriniz" dır

```
queryType=simple&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

Full Lucene Parser ile aynı sorgu `^3` alan terimi rampa olarak yorumlar. Çözümleyicileri değiştirmek, en üste *geçen dönemi içeren* sonuçlarla birlikte derecelendirmeyi değiştirir.

```
queryType=full&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>Sorgu türleri

Azure Bilişsel Arama, çok çeşitli sorgu türlerini destekler. 

| Sorgu türü | Kullanım | Örnekler ve daha fazla bilgi |
|------------|--------|-------------------------------|
| Serbest form metin araması | Arama parametresi ve ayrıştırıcı| Tam metin arama, dizininizdeki tüm *aranabilir* alanlarda bir veya daha fazla terimi tarar ve Google veya Bing gibi bir arama altyapısının çalışmasını istediğiniz şekilde çalışır. Giriş bölümündeki örnek tam metin aramadır.<br/><br/>Tam metin araması, standart Lucene Çözümleyicisi (varsayılan olarak), tüm terimleri düşürmek için "The" gibi durdurma sözcüklerini kullanarak metin analizinden daha az gider. Varsayılan ayarı, [İngilizce olmayan çözümleyiciler](index-add-language-analyzers.md#language-analyzer-list) veya metin analizini değiştiren [özel dilden bağımsız çözümleyiciler](index-add-custom-analyzers.md#AnalyzerTable) ile geçersiz kılabilirsiniz. Bir alanın tüm içeriğini tek bir belirteç olarak ele alan bir [anahtar sözcük](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) örneğidir. Bu, ZIP kodları, kimlikler ve bazı ürün adları gibi veriler için yararlıdır. | 
| Filtrelenmiş arama | [OData filtre ifadesi](query-odata-filter-orderby-syntax.md) ve her iki ayrıştırıcı | Filtre sorguları bir dizin içindeki tüm *filtrelenebilir* alanlar üzerinde bir Boole ifadesi değerlendirir. Aramanın aksine, bir filtre sorgusu, dize alanlarında büyük/küçük harf duyarlılığı dahil olmak üzere bir alanın tam içeriğiyle eşleşir. Farklı bir farklılık, filtre sorgularının OData sözdiziminde ifade edildiği bir farktır. <br/>[Filtre ifadesi örneği](search-query-simple-examples.md#example-3-filter-queries) |
| Coğrafi arama | Alan, filtre ifadesi ve herhangi bir ayrıştırıcıda [Edm. Geographon noktası türü](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) | EDM. Geographi noktası bulunan bir alanda depolanan koordinatlar "yakın ben bul" veya harita tabanlı arama denetimleri için kullanılır. <br/>[Coğrafi arama örneği](search-query-simple-examples.md#example-5-geo-search)|
| Aralık araması | Filtre ifadesi ve basit ayrıştırıcı | Azure Bilişsel Arama 'de, Aralık sorguları filtre parametresi kullanılarak oluşturulur. <br/>[Aralık filtresi örneği](search-query-simple-examples.md#example-4-range-filters) | 
| [Parçalı arama](query-lucene-syntax.md#bkmk_fields) | Arama parametresi ve tam ayrıştırıcı | Tek bir alanı hedefleyen bir bileşik sorgu ifadesi oluşturun. <br/>[Parçalı arama örneği](search-query-lucene-examples.md#example-2-fielded-search) |
| [benzer arama](query-lucene-syntax.md#bkmk_fuzzy) | Arama parametresi ve tam ayrıştırıcı | Benzer bir yapım veya yazım denetimi olan koşullara göre eşleşir. <br/>[Benzer arama örneği](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [yakınlık araması](query-lucene-syntax.md#bkmk_proximity) | Arama parametresi ve tam ayrıştırıcı | Belgedeki birbirini yakın terimleri bulur. <br/>[Yakınlık arama örneği](search-query-lucene-examples.md#example-4-proximity-search) |
| [terim artırma](query-lucene-syntax.md#bkmk_termboost) | Arama parametresi ve tam ayrıştırıcı | Bir belgeyi, daha fazla olmayan diğerlerine göre, daha yüksek bir dönem içeriyorsa, daha yüksek bir şekilde derecelendirir. <br/>[Terim artırma örneği](search-query-lucene-examples.md#example-5-term-boosting) |
| [normal ifade arama](query-lucene-syntax.md#bkmk_regex) | Arama parametresi ve tam ayrıştırıcı | Normal bir ifadenin içeriğine göre eşleşir. <br/>[Normal ifade örneği](search-query-lucene-examples.md#example-6-regex) |
|  [joker karakter veya ön ek arama](query-lucene-syntax.md#bkmk_wildcard) | Arama parametresi ve tam ayrıştırıcı | Bir önek ve tilde (`~`) veya tek karakter (`?`) temelinde eşleşir. <br/>[Joker karakter arama örneği](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="manage-search-results"></a>Arama sonuçlarını Yönet 

Sorgu sonuçları REST API JSON belgeleri olarak akışlardır, ancak .NET API 'Lerini kullanıyor olsanız da serileştirme yerleşiktir. Sorgudaki parametreleri ayarlayarak, yanıt için belirli alanlar seçerek sonuçları şekillendirebilirsiniz.

Sorgudaki parametreler, sonuç kümesini aşağıdaki yollarla yapılandırmak için kullanılabilir:

+ Sonuçlarda belge sayısını sınırlama veya toplu işleme (varsayılan olarak 50)
+ Sonuçlara dahil edilecek alanları seçme
+ Sıralama düzeni ayarlama
+ Arama sonuçlarının gövdesinde, eşleşen koşullara dikkat çekmek için isabet vurgulamaları ekleme

### <a name="tips-for-unexpected-results"></a>Beklenmeyen sonuçlara yönelik ipuçları

Kimi zaman, sonuçların yapısını değil, her zaman için. Sorgu sonuçları görmeyi beklediğiniz gibi değilse sonuçların iyileştirediğini görmek için bu sorgu değişikliklerini deneyebilirsiniz:

+ Her ölçüt yerine tüm ölçütlerde eşleşme gerektirmek için **`searchMode=any`** (varsayılan) **`searchMode=all`** olarak değiştirin. Bu, Boolean işleçleri sorguyu dahil edildiğinde özellikle doğrudur.

+ Metin veya sözcük temelli analiz gerekliyse sorgu tekniğini değiştirin, ancak sorgu türü, dil işlemesini önceden halizler. Tam metin aramasında, yazım hataları, tekil çoğul sözcük formları ve hatta düzensiz fiiller ya da isimler için metin veya sözlü analizler oto düzeltir. Benzer veya joker karakter araması gibi bazı sorgular için, metin Analizi sorgu ayrıştırma işlem hattının bir parçası değildir. Bazı senaryolarda, normal ifadeler geçici bir çözüm olarak kullanılmıştır. 

### <a name="paging-results"></a>Disk belleği sonuçları
Azure Bilişsel Arama, arama sonuçlarının sayfalama uygulanmasını kolaylaştırır. **@No__t_1** ve **`skip`** parametrelerini kullanarak, iyi arama kullanıcı arabirimi uygulamalarını kolayca etkinleştiren, yönetilebilir, sıralı alt kümeler halinde toplam arama sonucu kümesini almanızı sağlayan arama isteklerini sorunsuz bir şekilde verebilirsiniz. Bu daha küçük sonuç alt kümelerini alırken, tüm arama sonuçları kümesindeki belge sayısını da alabilirsiniz.

Sayfalama arama sonuçları hakkında daha fazla bilgi edinmek [Için bkz. Azure bilişsel arama arama sonuçları sayfası](search-pagination-page-layout.md).

### <a name="ordering-results"></a>Sonuçları sıralama
Bir arama sorgusu için sonuçları alırken, Azure Bilişsel Arama belirli bir alandaki değerlere göre sıralanmış sonuçlara hizmet verebilir. Azure Bilişsel Arama, varsayılan olarak, arama sonuçlarını [tf-ıDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)' den türetilen her bir belgenin arama puanı derecesine göre sıralar.

Azure Bilişsel Arama 'nin sonuçlarınızı arama puanı dışında bir değere göre sıralanmış olarak döndürmesini istiyorsanız **`orderby`** arama parametresini kullanabilirsiniz. Alan adlarını ve jeo-uzamsal değerler için [ **`geo.distance()` işlevine**](query-odata-filter-orderby-syntax.md) çağrıları dahil etmek için **`orderby`** parametresinin değerini belirtebilirsiniz. Her ifadenin, sonuçların artan sırada istendiğini belirtmek için `asc` ve sonuçların azalan sırada istendiğini belirtmek için **`desc`** . Artan sıralama varsayılandır.


### <a name="hit-highlighting"></a>İsabet vurgulama
Azure Bilişsel Arama 'de arama sorgusuyla eşleşen arama sonuçlarının tam bölümünü vurgulayarak **`highlight`** , **`highlightPreTag`** ve **`highlightPostTag`** parametreleri kullanılarak kolayca yapılır. Hangi *aranabilir* alanların eşleşen metnine sahip olması gerektiğini ve Azure bilişsel arama döndürdüğü eşleşen metnin başlangıcına ve sonuna eklemek için tam dize etiketlerini belirterek belirtebilirsiniz.

## <a name="see-also"></a>Ayrıca bkz.

+ [Tam metin aramasının Azure Bilişsel Arama 'da nasıl çalıştığı (sorgu ayrıştırma mimarisi)](search-lucene-query-architecture.md)
+ [Arama Gezgini](search-explorer.md)
+ [.NET 'te sorgulama](search-query-dotnet.md)
+ [REST 'te sorgulama](search-create-index-rest-api.md)
