---
title: 'Öğretici: özel çözümleyici oluşturma'
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama 'de arama sonuçlarının kalitesini artırmak için özel bir çözümleyici oluşturmayı öğrenin.
manager: luisca
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/29/2021
ms.openlocfilehash: f4bde98cfc772f5a80bb52c2e4bc2f5a9c28c78d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99097665"
---
# <a name="tutorial-create-a-custom-analyzer-for-phone-numbers"></a>Öğretici: telefon numaraları için özel çözümleyici oluşturma

[Çözümleyiciler](search-analyzers.md) , herhangi bir arama çözümünde önemli bir bileşendir. Arama sonuçlarının kalitesini artırmak için, çözümleyicilerin nasıl çalıştığını ve bu sonuçları nasıl etkilediğini anlamak önemlidir.

Serbest bir metin alanı gibi bazı durumlarda, yalnızca doğru [dil Çözümleyicisi](index-add-language-analyzers.md) ' ni seçerek arama sonuçları iyileştirecektir. Ancak telefon numaralarını, URL 'Leri veya e-postaları doğru şekilde aramak gibi bazı senaryolar özel çözümleyiciler kullanımını gerektirebilir.

Bu öğretici, Postman ve Azure Bilişsel Arama [REST API 'lerini](/rest/api/searchservice/) kullanarak şunları sağlar:

> [!div class="checklist"]
> * Çözümleyicilerin nasıl çalıştığını açıklayın
> * Telefon numaralarını aramak için özel bir çözümleyici tanımlayın
> * Özel çözümleyici 'nin metin olarak Simgeleştir işlemini test etme
> * Sonuçları daha fazla geliştirmek için dizin oluşturmak ve aramak üzere ayrı çözümleyiciler oluşturun

## <a name="prerequisites"></a>Önkoşullar

Bu öğretici için aşağıdaki hizmetler ve araçlar gereklidir.

+ [Postman masaüstü uygulaması](https://www.getpostman.com/)
+ [Mevcut bir arama hizmeti](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) [oluşturun](search-create-service-portal.md) veya bulun

## <a name="download-files"></a>Dosyaları indirme

Bu öğreticinin kaynak kodu, [Azure-Samples/Azure-Search-Postman-Samples](https://github.com/Azure-Samples/azure-search-postman-samples) GitHub deposundaki [özel çözümleyiciler](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/custom-analyzers) klasöründedir.

## <a name="1---create-azure-cognitive-search-service"></a>1-Azure Bilişsel Arama hizmeti oluşturma

Bu öğreticiyi tamamlayabilmeniz için [portalda oluşturabileceğiniz](search-create-service-portal.md)bir Azure bilişsel arama hizmetine ihtiyacınız vardır. Bu izlenecek yolu tamamlamak için ücretsiz katmanı kullanabilirsiniz.

Bir sonraki adımda, arama hizmetinizin adını ve API anahtarını bilmeniz gerekir. Bu öğeleri bulma konusunda emin değilseniz, bu [hızlı](search-create-service-portal.md#get-a-key-and-url-endpoint)başlangıca göz atın.


## <a name="2---set-up-postman"></a>2-Postman 'ı ayarlama

Sonra Postman 'ı başlatın ve [Azure-Samples/Azure-Search-Postman-Samples](https://github.com/Azure-Samples/azure-search-postman-samples)' den indirdiğiniz koleksiyonu içeri aktarın.

Koleksiyonu içeri aktarmak için, **dosyalar**  >  **içeri aktar**' a gidin ve ardından içeri aktarmak istediğiniz koleksiyon dosyasını seçin.

Her istek için şunları yapmanız gerekir:

1. `<YOUR-SEARCH-SERVICE>` değerini, arama hizmetinizin adıyla değiştirin.

1. `<YOUR-ADMIN-API-KEY>`Arama hizmetinizin birincil veya ikincil anahtarıyla değiştirin.

  :::image type="content" source="media/search-get-started-rest/postman-url.png" alt-text="Postman istek URL 'SI ve üstbilgisi" border="false":::

Postman hakkında bilgi sahibi değilseniz bkz. [Azure BILIŞSEL arama REST API 'Lerini araştırma](search-get-started-rest.md).

## <a name="3---create-an-initial-index"></a>3-ilk dizin oluşturma

Bu adımda, bir ilk dizin oluşturacak, dizine belge yükleyecek ve ardından ilk Aramalarımızın nasıl gerçekleştirileceğini görmek için belgeleri sorgularız.

### <a name="create-index"></a>Dizin Oluştur

İki alan ile çağrılan basit bir dizin oluşturarak başlayacağız `tutorial-basic-index` : `id` ve `phone_number` . Henüz çözümleyici tanımlamadık, `standard.lucene` Bu nedenle çözümleyici varsayılan olarak kullanılacak.

Dizini oluşturmak için aşağıdaki isteği göndereceğiz:

```http
PUT https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>

  {
    "fields": [
      {
        "name": "id",
        "type": "Edm.String",
        "key": true,
        "searchable": true,
        "filterable": false,
        "facetable": false,
        "sortable": true
      },
      {
        "name": "phone_number",
        "type": "Edm.String",
        "sortable": false,
        "searchable": true,
        "filterable": false,
        "facetable": false
      }
    ]
  }
```

### <a name="load-data"></a>Veri yükleme

Daha sonra, verileri dizine yükleyeceğiz. Bazı durumlarda, alınan telefon numaralarının biçimi üzerinde denetiminiz olmayabilir, bu nedenle farklı biçim türlerine karşı test edeceğiz. İdeal olarak, bir arama çözümü, biçiminden bağımsız olarak tüm eşleşen telefon numaralarını döndürür.

Veriler, aşağıdaki istek kullanılarak dizine yüklenir: 

```http
POST https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/docs/index?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>

  {
    "value": [
      {
        "@search.action": "upload",  
        "id": "1",
        "phone_number": "425-555-0100"
      },
      {
        "@search.action": "upload",  
        "id": "2",
        "phone_number": "(321) 555-0199"
      },
      {  
        "@search.action": "upload",  
        "id": "3",
        "phone_number": "+1 425-555-0100"
      },
      {  
        "@search.action": "upload",  
        "id": "4",  
        "phone_number": "+1 (321) 555-0199"
      },
      {
        "@search.action": "upload",  
        "id": "5",
        "phone_number": "4255550100"
      },
      {
        "@search.action": "upload",  
        "id": "6",
        "phone_number": "13215550199"
      },
      {
        "@search.action": "upload",  
        "id": "7",
        "phone_number": "425 555 0100"
      },
      {
        "@search.action": "upload",  
        "id": "8",
        "phone_number": "321.555.0199"
      }
    ]  
  }
```

Dizindeki verilerle aramaya başlamak için hazırız.

### <a name="search"></a>Arayın

Aramayı sezgisel hale getirmek için, kullanıcıların sorguları belirli bir şekilde biçimlendirmeniz beklenmez. Bir Kullanıcı `(425) 555-0100` yukarıda gösterilen biçimlerden herhangi birinde arama yapmak için sonuçlar döndürülmeye devam edecektir. Bu adımda, nasıl çalıştığını görmek için birkaç örnek sorgu test edeceğiz.

Şunu arayarak başladık `(425) 555-0100` :

```http
GET https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/docs?api-version=2019-05-06&search=(425) 555-0100
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>  
```

Bu sorgu **, dört beklenen üç sonuç döndürür,** ancak aynı zamanda **iki beklenmeyen sonuç** döndürür:

```json
{
    "value": [
        {
            "@search.score": 0.05634898,
            "phone_number": "+1 425-555-0100"
        },
        {
            "@search.score": 0.05634898,
            "phone_number": "425 555 0100"
        },
        {
            "@search.score": 0.05634898,
            "phone_number": "425-555-0100"
        },
        {
            "@search.score": 0.020766128,
            "phone_number": "(321) 555-0199"
        },
        {
            "@search.score": 0.020766128,
            "phone_number": "+1 (321) 555-0199"
        }
    ]
}
```

Daha sonra, herhangi bir biçimlendirme olmadan bir numara arayalım `4255550100`

```http
GET https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/docs?api-version=2019-05-06&search=4255550100
  api-key: <YOUR-ADMIN-API-KEY>
```

Bu sorgu, daha da **kötüleşirken yalnızca dört doğru eşleştirmeden birini** döndürür.

```json
{
    "value": [
        {
            "@search.score": 0.6015292,
            "phone_number": "4255550100"
        }
    ]
}
```

Bu sonuçların kafa karıştırıcı olduğunu fark ederseniz, tek başına siz olursunuz. Sonraki bölümde, bu sonuçları neden alıyoruz.

## <a name="4---debug-search-results"></a>4-hata ayıklama arama sonuçları

Bu arama sonuçlarını anlamak için, çözümleyicilerin nasıl çalıştığını anlamak önemlidir. Buradan, [metin ANALIZI API](/rest/api/searchservice/test-analyzer) 'sini kullanarak varsayılan çözümleyici 'yi test edebilir ve ardından ihtiyaçlarınızı karşılayan bir çözümleyici oluşturabilirsiniz.

### <a name="how-analyzers-work"></a>Çözümleyiciler nasıl çalışır?

[Çözümleyici](search-analyzers.md) , sorgu dizelerinde ve dizini oluşturulmuş belgelerde metin işlemeden sorumlu [tam metin arama altyapısının](search-lucene-query-architecture.md) bir bileşenidir. Farklı çözümleyiciler, senaryoya bağlı olarak metni farklı şekillerde işleyebilir. Bu senaryo için telefon numaralarına uyarlanmış bir çözümleyici oluşturacağız.

Çözümleyiciler üç bileşenden oluşur:

+ Giriş metinlerinden bağımsız karakterleri kaldırmak veya değiştirmek için kullanılan [**karakter filtreleri**](#CharFilters) .
+ Giriş metnini, arama dizininde anahtarlar haline gelen belirteçlere kesen bir [**Simgeleştirici**](#Tokenizers) .
+ Tokenizer tarafından üretilen belirteçleri düzenleyen [**belirteç filtreleri**](#TokenFilters) .

Aşağıdaki diyagramda, bu üç bileşenin bir tümceyi simgeleştirmek için birlikte nasıl çalıştığını görebilirsiniz:

  :::image type="content" source="media/tutorial-create-custom-analyzer/analyzers-explained.png" alt-text="Bir tümceyi simgeleştirme için çözümleyici işleminin diyagramı":::

Bu belirteçler daha sonra, hızlı, tam metin aramalarına izin veren ters bir dizinde depolanır.  Ters bir dizin, sözcük temelli analiz sırasında ayıklanan tüm benzersiz terimleri, bulundukları belgelere eşleyerek tam metin aramasını sağlar. Aşağıdaki diyagramda bir örnek görebilirsiniz:

  :::image type="content" source="media/tutorial-create-custom-analyzer/inverted-index-explained.png" alt-text="Örnek tersine çevrilmiş Dizin":::

Tüm arama, ters çevrilen dizinde depolanan terimleri aramaya yönelik olarak gelir. Bir Kullanıcı bir sorgu yayınlar:

1. Sorgu ayrıştırılır ve sorgu terimleri çözümlenir.
1. Tersine çevrilmiş Dizin, eşleşen koşullara sahip belgeler için taranır.
1. Son olarak, alınan belgeler [benzerlik algoritmasına](index-ranking-similarity.md)göre sıralanır.

  :::image type="content" source="media/tutorial-create-custom-analyzer/query-architecture-explained.png" alt-text="Çözümleyici işlem derecelendirmesi benzerliği diyagramı":::

Sorgu terimleri, ters dizininizdeki koşullara eşleşmezse sonuçlar döndürülmez. Sorguların nasıl çalıştığı hakkında daha fazla bilgi edinmek için [tam metin aramasında](search-lucene-query-architecture.md)bu makaleye bakın.

> [!Note]
> [Kısmi terim sorguları](search-query-partial-matching.md) , bu kural için önemli bir istisnadır. Bu sorgular (önek sorgusu, joker karakter sorgusu, Regex sorgusu) normal terim sorgularının aksine sözcük temelli analiz sürecini atlar. Kısmi şartlar yalnızca dizindeki koşullara göre eşleştirilmeden önce küçük harfli. Bir çözümleyici bu sorgu türlerini destekleyecek şekilde yapılandırılmamışsa, eşleşen terimler dizinde bulunmadığından genellikle beklenmeyen sonuçlar alırsınız.

### <a name="test-analyzer-using-the-analyze-text-api"></a>Metin analizi API 'sini kullanarak test Çözümleyicisi

Azure Bilişsel Arama, metinleri nasıl işleyeceğini anlamak için çözümleyicilerin test etmenizi sağlayan bir [metin analizi API 'si](/rest/api/searchservice/test-analyzer) sağlar.

Aşağıdaki istek kullanılarak metin analizi API 'SI çağrılır:

```http
POST https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/analyze?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>

  {
    "text": "(425) 555-0100",
    "analyzer": "standard.lucene"
  }
```

Ardından API, metinden ayıklanan belirteçlerin bir listesini döndürür. Standart Lucene Çözümleyicisi 'nin telefon numarasını üç ayrı belirtece böler.

```json
{
    "tokens": [
        {
            "token": "425",
            "startOffset": 1,
            "endOffset": 4,
            "position": 0
        },
        {
            "token": "555",
            "startOffset": 6,
            "endOffset": 9,
            "position": 1
        },
        {
            "token": "0100",
            "startOffset": 10,
            "endOffset": 14,
            "position": 2
        }
    ]
}
```

Buna karşılık, `4255550100` herhangi bir noktalama olmadan biçimlendirilen telefon numarası tek bir belirteçte simgeleştirilir.

```json
{
  "text": "4255550100",
  "analyzer": "standard.lucene"
}
```

```json
{
    "tokens": [
        {
            "token": "4255550100",
            "startOffset": 0,
            "endOffset": 10,
            "position": 0
        }
    ]
}
```

Hem sorgu terimlerinin hem de dizinli belgelerin çözümlendiğini aklınızda bulundurun. Önceki adımdan gelen arama sonuçlarına geri dönüyoruz, bu sonuçların neden döndürüldüğünü görmemiz için başlayabiliriz.

İlk sorguda, koşullarınızdan biri `555` aradığımız şartlarla eşleştiğinden yanlış telefon numaraları döndürüldü. İkinci sorguda yalnızca bir sayı, eşleşen bir terim olan tek kayıt olduğundan döndürüldü `4255550100` .

## <a name="5---build-a-custom-analyzer"></a>5-özel çözümleyici oluşturma

Görtiğimiz sonuçları anladığımıza göre, simgeleştirme mantığını geliştirmek için özel bir çözümleyici oluşturalım.

Amaç, sorgu veya dizini oluşturulmuş dizenin biçim ne olduğuna bakılmaksızın telefon numaralarına karşı sezgisel bir arama sağlamaktır. Bu sonucu elde etmek için bir [karakter filtresi](#CharFilters), bir [Simgeleştirici](#Tokenizers)ve bir [belirteç filtresi](#TokenFilters)belirteceğiz.

<a name="CharFilters"></a>

### <a name="character-filters"></a>Karakter filtreleri

Karakter filtreleri, belirteç Oluşturucu 'ya gönderilmeden önce metni işlemek için kullanılır. Karakter filtrelerinin yaygın kullanımları, HTML öğelerinin filtrelenmesini veya özel karakterlerin yerini içerir.

Telefon numaraları için, tüm telefon numarası biçimlerinin aynı özel karakterleri ve boşlukları içermediği için boşluk ve özel karakterleri kaldırmak istiyoruz.

```json
"charFilters": [
    {
      "@odata.type": "#Microsoft.Azure.Search.MappingCharFilter",
      "name": "phone_char_mapping",
      "mappings": [
        "-=>",
        "(=>",
        ")=>",
        "+=>",
        ".=>",
        "\\u0020=>"
      ]
    }
  ]
```

Yukarıdaki filtre, girdiden kaldırılacak `-` `(` `)` `+` `.` ve boşluktan boşluk olacak.

|Girdi|Çıktı|  
|-|-|  
|`(321) 555-0199`|`3215550199`|  
|`321.555.0199`|`3215550199`|

<a name="Tokenizers"></a>

### <a name="tokenizers"></a>Fark oluşturma denenmeden

Simgeler, metni belirteçlere ayırır ve noktalama gibi bazı karakterleri, bu şekilde atar. Çoğu durumda, simgeleştirme hedefi bir tümceyi tek sözcüklere bölemedir.

Bu senaryoda, `keyword_v2` telefon numarasını tek bir terim olarak yakalamak istediğimiz için Simgeleştirici anahtar sözcüğünü kullanacağız. Bu sorunu çözmenin tek yolu olmadığını unutmayın. Aşağıdaki [alternatif yaklaşımlar](#Alternate) bölümüne bakın.

Anahtar sözcük simgeleyicileri her zaman tek bir terim olarak verilen metin olarak çıktı.

|Girdi|Çıktı|  
|-|-|  
|`The dog swims.`|`[The dog swims.]`|  
|`3215550199`|`[3215550199]`|

<a name="TokenFilters"></a>

### <a name="token-filters"></a>Belirteç filtreleri

Belirteç filtreleri, Simgeleştirici tarafından oluşturulan belirteçleri filtreleyecek veya değiştirecek. Belirteç filtresinin yaygın bir kullanımı, küçük harfli bir belirteç filtresi kullanan tüm karakterlere küçük harfle eklenir. Diğer bir yaygın kullanım,, veya gibi stopsözcüklerini filtrelemedir `the` `and` `is` .

Bu senaryo için bu filtrelerden birini kullanmaya gerek duymuz, ancak telefon numaralarının kısmi aramalarına izin vermek için bir nGram belirteç filtresi kullanacağız.

```json
"tokenFilters": [
  {
    "@odata.type": "#Microsoft.Azure.Search.NGramTokenFilterV2",
    "name": "custom_ngram_filter",
    "minGram": 3,
    "maxGram": 20
  }
]
```

#### <a name="ngramtokenfilterv2"></a>NGramTokenFilterV2

[NGram_v2 belirteç filtresi](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html) belirteçleri, ve parametrelerine göre belirli bir boyutun n-gram ' lerine böler `minGram` `maxGram` .

Telefon çözümleyici için, `minGram` `3` kullanıcıların aramasını beklediğimiz en kısa alt dize olduğundan, olarak ayarlandık. `maxGram` , uzantılar da `20` dahil olmak üzere tüm telefon numaralarının tek bir n-gram içine sığması için olarak ayarlanır.

 N-gram talihsiz yan etkisi, bazı yanlış pozitif sonuçlar döndürülmeyecektir. 7. adımda, n-gram belirteç filtresi içermeyen aramalar için ayrı bir çözümleyici oluşturarak bu adımı düzeltireceğiz.

|Girdi|Çıktı|  
|-|-|  
|`[12345]`|`[123, 1234, 12345, 234, 2345, 345]`|  
|`[3215550199]`|`[321, 3215, 32155, 321555, 3215550, 32155501, 321555019, 3215550199, 215, 2155, 21555, 215550, ... ]`|

### <a name="analyzer"></a>Analyzer

Karakter filtrelerimiz, Simgeleştirici ve belirteç filtrelerimizle, çözümümüzü tanımlamaya hazırız.

```json
"analyzers": [
  {
    "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer",
    "name": "phone_analyzer",
    "tokenizer": "custom_tokenizer_phone",
    "tokenFilters": [
      "custom_ngram_filter"
    ],
    "charFilters": [
      "phone_char_mapping"
    ]
  }
]
```

|Girdi|Çıktı|  
|-|-|  
|`12345`|`[123, 1234, 12345, 234, 2345, 345]`|  
|`(321) 555-0199`|`[321, 3215, 32155, 321555, 3215550, 32155501, 321555019, 3215550199, 215, 2155, 21555, 215550, ... ]`|

Çıkışdaki belirteçlerin herhangi birinin artık arandığına dikkat edin. Sorgumız bu belirteçlerden herhangi birini içeriyorsa telefon numarası döndürülür.

Özel çözümleyici tanımlı olarak, özel çözümleyici 'nin bir sonraki adımda test için kullanılabilmesi için dizini yeniden oluşturun. Basitlik sağlamak için Postman koleksiyonu, tanımladığımız çözümleyici ile adlı yeni bir dizin oluşturur `tutorial-first-analyzer` .

## <a name="6---test-the-custom-analyzer"></a>6-özel çözümleyici 'yi test etme

Dizini oluşturduktan sonra, aşağıdaki isteği kullanarak oluşturduğum çözümleyici 'yi şimdi test edebilirsiniz:

```http
POST https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-first-analyzer/analyze?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>  

  {
    "text": "+1 (321) 555-0199",
    "analyzer": "phone_analyzer"
  }
```

Ardından, telefon numarasından kaynaklanan belirteçlerin koleksiyonunu görebileceksiniz:

```json
{
    "tokens": [
        {
            "token": "132",
            "startOffset": 1,
            "endOffset": 17,
            "position": 0
        },
        {
            "token": "1321",
            "startOffset": 1,
            "endOffset": 17,
            "position": 0
        },
        {
            "token": "13215",
            "startOffset": 1,
            "endOffset": 17,
            "position": 0
        },
        ...
        ...
        ...
    ]
}
```

## <a name="7---build-a-custom-analyzer-for-queries"></a>7-sorgular için özel çözümleyici oluşturma

Özel çözümleyici ile dizinde bazı örnek sorgular yaptıktan sonra, geri çekmenin iyileştirildiğini ve eşleşen tüm telefon numaralarının artık döndürüldüğünü fark edeceksiniz. Ancak, n-gram belirteç filtresi bazı yanlış pozitif sonuçlar verilmesini sağlar. Bu, n-gram belirteç filtresinin yaygın bir yan etkıdır.

Yanlış pozitifleri engellemek için, sorgulamak için ayrı bir çözümleyici oluşturacağız. Bu **çözümleyici, zaten** oluşturduğumuz çözümleyici ile aynı olacaktır `custom_ngram_filter` .

```json
    {
      "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer",
      "name": "phone_analyzer_search",
      "tokenizer": "custom_tokenizer_phone",
      "tokenFilters": [],
      "charFilters": [
        "phone_char_mapping"
      ]
    }
```

Dizin tanımında, ve ' ı belirtin `indexAnalyzer` `searchAnalyzer` .

```json
    {
      "name": "phone_number",
      "type": "Edm.String",
      "sortable": false,
      "searchable": true,
      "filterable": false,
      "facetable": false,
      "indexAnalyzer": "phone_analyzer",
      "searchAnalyzer": "phone_analyzer_search"
    }
```

Bu değişiklik ile tüm hazırsınız. Aramanın beklendiği gibi çalıştığını doğrulamak için dizini yeniden oluşturun, verileri dizine oluşturun ve sorguları tekrar test edin. Postman koleksiyonunu kullanıyorsanız, adlı üçüncü bir dizin oluşturur `tutorial-second-analyzer` .

<a name="Alternate"></a>

## <a name="alternate-approaches"></a>Alternatif yaklaşımlar

Yukarıdaki çözümleyici, arama esnekliğini en üst düzeye çıkarmak için tasarlanmıştır. Ancak, bu durumda çok önemli olmayan birçok terimi dizinde depolamanın maliyeti de vardır.

Aşağıdaki örnekte, bu görev için de kullanılabilecek farklı bir çözümleyici gösterilmektedir. 

Çözümleyici, bu gibi giriş verileri dışında çalışarak `14255550100` telefon numarasını mantıksal olarak öbeklendirir. Örneğin, çözümleyici ülke kodunu, alan kodundan ayırabilmeyecektir `1` `425` . Bu tutarsızlık, bir Kullanıcı aramasına bir ülke kodu içermiyorsa, yukarıdaki sayının döndürülmemesine neden olur.

```json
"analyzers": [
  {
    "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer",
    "name": "phone_analyzer_shingles",
    "tokenizer": "custom_tokenizer_phone",
    "tokenFilters": [
      "custom_shingle_filter"
    ]
  }
],
"tokenizers": [
  {
    "@odata.type": "#Microsoft.Azure.Search.StandardTokenizerV2",
    "name": "custom_tokenizer_phone",
    "maxTokenLength": 4
  }
],
"tokenFilters": [
  {
    "@odata.type": "#Microsoft.Azure.Search.ShingleTokenFilter",
    "name": "custom_shingle_filter",
    "minShingleSize": 2,
    "maxShingleSize": 6,
    "tokenSeparator": ""
  }
]
```

Aşağıdaki örnekte telefon numarasının, normalde bir kullanıcının aramasını bekledikleri parçalara bölüneceği aşağıda görebilirsiniz.

|Girdi|Çıktı|  
|-|-|  
|`(321) 555-0199`|`[321, 555, 0199, 321555, 5550199, 3215550199]`|

Gereksinimlerinize bağlı olarak, bu sorun için daha verimli bir yaklaşım olabilir.

## <a name="reset-and-rerun"></a>Sıfırlama ve yeniden çalıştırma

Kolaylık olması için bu öğreticide üç yeni dizin oluşturmanız gerekiyordu. Ancak, ilk geliştirme aşamaları sırasında dizinleri silmek ve yeniden oluşturmak yaygındır. Azure portal bir dizini veya aşağıdaki API çağrısını kullanarak silebilirsiniz:

```http
DELETE https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index?api-version=2019-05-06
  api-key: <YOUR-ADMIN-API-KEY>
```

## <a name="takeaways"></a>Paketler

Bu öğreticide, özel bir çözümleyici oluşturma ve test etme işlemi gösterilmiştir. Bir dizin, dizinli veri oluşturdunuz ve sonra hangi arama sonuçlarının döndürüleceğini görmek için dizine göre sorgulandınız. Buradan, işlem içinde sözlü analiz sürecini görmek için metin analizi API 'sini kullandınız.

Bu öğreticide tanımlanan çözümleyici, telefon numaralarına karşı arama yapmak için kolay bir çözüm sunarak, aynı işlem, sahip olduğunuz herhangi bir senaryo için özel bir telefon Çözümleyicisi oluşturmak için de kullanılabilir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kendi aboneliğinizde çalışırken, projenin sonunda artık ihtiyaç duyulmadığınızı kaynakları kaldırmak iyi bir fikirdir. Çalışır durumda bırakılan kaynaklar maliyetlerin artmasına neden olabilir. Kaynakları teker teker silebilir veya tüm kaynak grubunu silerek kaynak kümesinin tamamını kaldırabilirsiniz.

Sol gezinti bölmesindeki tüm kaynaklar veya kaynak grupları bağlantısını kullanarak portalda kaynakları bulabilir ve yönetebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Artık özel bir çözümleyici oluşturma hakkında bilgi sahibi olduğunuza göre, zengin bir arama deneyimi oluşturmanız için kullanabileceğiniz farklı filtrelerin, belirteçlerin ve çözümleyicilerin tümüne göz atalım.

> [!div class="nextstepaction"]
> [Azure Bilişsel Arama özel çözümleyiciler](index-add-custom-analyzers.md)