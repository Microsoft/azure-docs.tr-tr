---
title: Dil ve metin işleme Çözümleyicileri
titleSuffix: Azure Cognitive Search
description: Varsayılan standart Lucene öğesini özel, önceden tanımlanmış veya dile özgü alternatifler ile değiştirmek için bir dizinde aranabilir metin alanlarına çözümleyiciler atayın.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/17/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: d40dd0b91f9dcfb7bf5b6e8f084f25ee4f90d780
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104596561"
---
# <a name="analyzers-for-text-processing-in-azure-cognitive-search"></a>Azure Bilişsel Arama metin işleme için çözümleyiciler

*Çözümleyici* , sorgu dizelerinde ve dizini oluşturulmuş belgelerde metin işlemeden sorumlu [tam metin aramasının](search-lucene-query-architecture.md) bir bileşenidir. Metin işleme (sözlü analiz olarak da bilinir), bir sorgu dizesini bunlar gibi eylemler aracılığıyla değiştirerek dönüştürülebilir:

+ Gerekli olmayan kelimeleri (stopwords) ve noktalama işaretlerini kaldır
+ Tümcecikleri bölme ve sözcükleri bileşen bölümlerine ayırma
+ Büyük/küçük harf sözcükleri küçük harfe
+ Bir depolama verimliliği için kelimeleri basit kök formlara küçültün ve bu nedenle eşleşmelerin zaman hali ne olursa olsun bulunabilir

Analiz `Edm.String` , tam metin aramasını gösteren "aranabilir" olarak işaretlenen alanlar için geçerlidir. 

Bu yapılandırmaya sahip alanlar için, belirteçler oluşturulduğunda dizin oluşturma sırasında ve sorgular ayrıştırıldığında ve motor eşleşen belirteçleri taradığında sorgu yürütme sırasında gerçekleşir. Aynı çözümleyici hem dizin oluşturma hem de sorgular için kullanıldığında, bir eşleşme oluşma olasılığını daha yüksektir, ancak gereksinimlerinize bağlı olarak her iş yükü için çözümleyici 'yi bağımsız olarak ayarlayabilirsiniz.

Filtre veya benzer arama gibi tam metin araması *olmayan* sorgu türleri, sorgu tarafındaki çözümleme aşamasına gitmez. Bunun yerine, ayrıştırıcı bu dizeleri eşleşme için temel olarak sağladığınız kalıbı kullanarak doğrudan arama altyapısına gönderir. Genellikle, bu sorgu formları, model eşleme çalışması yapmak için tam dize belirteçleri gerektirir. Dizin oluşturma sırasında tüm terim belirteçlerini sağlamak için [özel çözümleyiciler](index-add-custom-analyzers.md)gerekebilir. Sorgu koşullarının ne zaman ve neden çözümlenme hakkında daha fazla bilgi için bkz. [Azure bilişsel arama 'de tam metin arama](search-lucene-query-architecture.md).

Sözcük temelli analize yönelik daha fazla arka plan için, kısa bir açıklama için aşağıdaki video klibini dinleyin.

> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=132&end=189]

## <a name="default-analyzer"></a>Varsayılan çözümleyici  

Azure Bilişsel Arama sorgularında, bir çözümleyici, aranabilir olarak işaretlenen tüm dize alanlarında otomatik olarak çağrılır. 

Varsayılan olarak, Azure Bilişsel Arama [Apache Lucene standart Çözümleyicisi 'ni (Standart Lucene)](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html)kullanır ve bu, metni ["Unicode metin segmentleme"](https://unicode.org/reports/tr29/) kurallarından sonra öğelere ayırır. Ayrıca, standart çözümleyici tüm karakterleri küçük harf biçimine dönüştürür. Dizini oluşturulmuş belgeler ve arama terimleri, dizin oluşturma ve sorgu işleme sırasında Analize gider.  

Alan temelinde varsayılan ayarı geçersiz kılabilirsiniz. Alternatif çözümleyiciler, dil işleme için [dil Çözümleyicisi](index-add-language-analyzers.md) , [özel bir çözümleyici](index-add-custom-analyzers.md)veya [kullanılabilir çözümleyiciler listesinden](index-add-custom-analyzers.md#built-in-analyzers)yerleşik bir çözümleyici olabilir.

## <a name="types-of-analyzers"></a>Çözümleyiciler türleri

Aşağıdaki listede Azure Bilişsel Arama 'de hangi çözümleyiciler kullanılabildiği açıklanmaktadır.

| Kategori | Açıklama |
|----------|-------------|
| [Standart Lucene Çözümleyicisi](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Varsayılan. Belirtim veya yapılandırma gerekli değildir. Bu genel amaçlı çözümleyici birçok dil ve senaryo için iyi bir performans uygular.|
| Yerleşik çözümleyiciler | Olduğu gibi tüketilen ve adına göre başvuruluyor. İki tür vardır: Language ve Language-agstik. </br></br>[Özelleştirilmiş (dilden bağımsız) çözümleyiciler](index-add-custom-analyzers.md#built-in-analyzers) , metin girdileri özel işlem veya minimum işleme gerektirdiğinde kullanılır. Bu kategorideki çözümleyiciler örnekleri, **Asciifolding**, **anahtar sözcük**, **model**, **Simple**, **stop**, **Whitespace** içerir. </br></br>[Dil Çözümleyicileri](index-add-language-analyzers.md) , tek tek diller için zengin dil desteği gerektiğinde kullanılır. Azure Bilişsel Arama, 35 Lucene dil Çözümleyicileri ve 50 Microsoft doğal dil işleme Çözümleyicileri destekler. |
|[Özel çözümleyiciler](/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | , Bir Simgeleştirici (zorunlu) ve isteğe bağlı filtrelerden (char veya token) oluşan, mevcut öğelerin bir birleşiminin Kullanıcı tanımlı yapılandırmasını ifade eder.|

**Desenler** veya **durdurma** gibi bazı yerleşik çözümleyiciler, sınırlı sayıda yapılandırma seçeneğini destekler. Bu seçenekleri ayarlamak için yerleşik [çözümleyicilerin ve yerleşik çözümleyiciler](index-add-custom-analyzers.md#built-in-analyzers)' de belgelenen alternatif seçeneklerden birini içeren özel bir çözümleyici oluşturun. Tüm özel yapılandırmada olduğu gibi, yeni yapılandırmanızı Lucene model çözümleyicisinden ayırt etmek için *Mypatternanalyzer* gibi bir adla birlikte belirtin.

## <a name="how-to-specify-analyzers"></a>Çözümleyiciler belirtme

Çözümleyici ayarlama isteğe bağlıdır. Genel bir kural olarak, nasıl çalıştığını görmek için önce varsayılan standart Lucene Analyzer 'ı kullanmayı deneyin. Sorgular beklenen sonuçları döndürmiyorsa, farklı bir çözümleyiciye geçiş genellikle doğru çözümdür.

1. [Dizinde](/rest/api/searchservice/create-index)bir alan tanımı oluştururken, "çözümleyici" özelliğini aşağıdakilerden birine ayarlayın: **anahtar sözcük**, gibi bir [dil Çözümleyicisi](index-add-language-analyzers.md) [](index-add-custom-analyzers.md#built-in-analyzers) `en.microsoft` veya özel bir çözümleyici (aynı dizin şemasında tanımlanır) gibi yerleşik bir çözümleyici.  
 
   ```json
     "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "en.microsoft",
      "indexAnalyzer": null,
      "searchAnalyzer": null
    },
   ```

   [Dil Çözümleyicisi](index-add-language-analyzers.md)kullanıyorsanız, bunu belirtmek için "Analyzer" özelliğini kullanmanız gerekir. "SearchAnalyzer" ve "ındexanalyzer" özellikleri dil Çözümleyicileri için geçerlidir.

1. Alternatif olarak, her iş yükü için çözümleyici 'yi değiştirmek üzere "ındexanalyzer" ve "searchAnalyzer" ayarlayın. Bu özellikler birlikte ayarlanır ve null olması gereken "Analyzer" özelliğini değiştirir. Bu etkinliklerden biri başka bir dönüşüme ihtiyaç duymadığında, dizin oluşturma ve sorgular için farklı çözümleyiciler kullanabilirsiniz.

   ```json
     "fields": [
    {
      "name": "ProductGroup",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": null,
      "indexAnalyzer": "keyword",
      "searchAnalyzer": "standard"
    },
   ```

1. Yalnızca özel çözümleyiciler için, dizinin **[çözümleyiciler]** bölümünde bir giriş oluşturun ve ardından özel çözümleyicinizi önceki iki adımdan herhangi biri başına alan tanımına atayın. Daha fazla bilgi için bkz. [Dizin oluşturma](/rest/api/searchservice/create-index) ve ayrıca [özel çözümleyiciler ekleme](index-add-custom-analyzers.md).

## <a name="when-to-add-analyzers"></a>Çözümleyiciler ne zaman eklenir

Çözümleyiciler eklemek ve atamak için en iyi süre, etkin geliştirme sırasında dizinleri bırakma ve yeniden oluşturma işlemi sırasında yapılır.

Çözümleyiciler terimleri simgeleştirmek için kullanıldığından, alan oluşturulduğunda bir çözümleyici atamanız gerekir. Aslında, zaten fiziksel olarak oluşturulmuş bir alana çözümleyici veya ındexanalyzer atamaya izin verilmez (ancak, searchAnalyzer özelliğini herhangi bir zamanda dizine hiçbir etki olmadan değiştirebilirsiniz).

Mevcut bir alanın çözümleyicisini değiştirmek için, [dizini tamamen yeniden oluşturmanız](search-howto-reindex.md) gerekecektir (tek tek alanları yeniden oluşturamazsınız). Üretimdeki dizinler için, yeni çözümleyici atamasıyla yeni bir alan oluşturarak yeniden derlemeyi erteleyebilirsiniz ve eskisini yerine kullanmaya başlayabilirsiniz. Yeni alanı birleştirmek için [güncelleştirme dizinini](/rest/api/searchservice/update-index) kullanın ve doldurmak Için [mergeorupload](/rest/api/searchservice/addupdate-or-delete-documents) kullanın. Daha sonra, planlı dizin hizmeti 'nin bir parçası olarak, eski alanları kaldırmak için dizini temizleyebilirsiniz.

Varolan bir dizine yeni bir alan eklemek için, alanı eklemek üzere [güncelleştirme dizinini](/rest/api/searchservice/update-index) çağırın ve onu doldurmak Için [mergeorupload](/rest/api/searchservice/addupdate-or-delete-documents) .

Varolan bir dizine özel bir çözümleyici eklemek için, bu hatadan kaçınmak istiyorsanız [güncelleştirme dizininde](/rest/api/searchservice/update-index) "Allowındexkesinti" bayrağını geçirin:

*"Dizin güncelleştirmesine izin verilmiyor, çünkü kapalı kalma süresine yol açacaktı. Varolan bir dizine yeni çözümleyiciler, belirteçler, belirteç filtreleri veya karakter filtreleri eklemek için, Dizin güncelleştirme isteğinde ' Allowwındexını ' sorgu parametresini ' true ' olarak ayarlayın. Bu işlemin dizininizi en az birkaç saniyede çevrimdışına koyacağına, dizin oluşturma ve sorgu isteklerinizin başarısız olmasına neden olduğunu unutmayın. Dizinin performansı ve yazma kullanılabilirliği, Dizin güncelleştirildikten sonra çok fazla dakika boyunca veya çok büyük dizinler için daha uzun olabilir. "*

## <a name="recommendations-for-working-with-analyzers"></a>Çözümleyiciler ile çalışmaya yönelik öneriler

Bu bölümde, çözümleyiciler ile nasıl çalışılacağı hakkında öneriler sunulmaktadır.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>Belirli gereksinimleriniz yoksa okuma-yazma için bir çözümleyici

Azure Bilişsel Arama, ek ındexanalyzer ve searchAnalyzer alan özellikleri aracılığıyla Dizin oluşturma ve arama için farklı çözümleyiciler belirtmenize olanak tanır. Belirtilmemişse, çözümleyici özelliği ile ayarlanan çözümleyici, hem dizin oluşturma hem de arama için kullanılır. Çözümleyici belirtilmemişse, varsayılan standart Lucene Çözümleyicisi kullanılır.

Genel bir kural, aynı çözümleyici 'yi hem dizin oluşturma hem de sorgulama için, belirli gereksinimler aksini belirtmedikçe kullanır. Kapsamlı olarak test ettiğinizden emin olun. Metin işleme, arama ve dizin oluşturma sırasında farklılık gösterdiğinde, arama ve dizin oluşturma Çözümleyicisi yapılandırmalarının hizalanmamış olması durumunda sorgu terimleri ve dizine alınmış terimler arasında uyumsuzluk riskini çalıştırırsınız.

### <a name="test-during-active-development"></a>Etkin geliştirme sırasında test etme

Standart çözümleyici 'nin geçersiz kılınması, dizin yeniden oluşturmayı gerektirir. Mümkünse, üretime bir dizin vermeden önce, etkin geliştirme sırasında hangi çözümleyicilerin kullanılacağını belirleyin.

### <a name="inspect-tokenized-terms"></a>Simgeleştirilmiş terimleri İncele

Arama beklenen sonuçları döndürmediğinde, en olası senaryo sorgudaki terim girişleri ile dizinde simgeleştirilmiş terimler arasındaki belirteç tutarsızlıklardan oluşur. Belirteçler aynı değilse, Eşleşmeler bir hata ile başarısız olur. Belirteç ayırıcı çıkışını denetlemek için, [API 'yi](/rest/api/searchservice/test-analyzer) araştırma aracı olarak Çözümle ' yi kullanmanızı öneririz. Yanıt, belirli bir çözümleyici tarafından oluşturulan belirteçlerden oluşur.

<a name="examples"></a>

## <a name="rest-examples"></a>REST örnekleri

Aşağıdaki örneklerde birkaç anahtar senaryo için çözümleyici tanımları gösterilmektedir.

+ [Özel çözümleyici örneği](#Custom-analyzer-example)
+ [Bir alan örneğine çözümleyiciler atama](#Per-field-analyzer-assignment-example)
+ [Dizin oluşturma ve arama için Çözümleyicileri karıştırma](#Mixing-analyzers-for-indexing-and-search-operations)
+ [Dil Çözümleyicisi örneği](#Language-analyzer-example)

<a name="Custom-analyzer-example"></a>

### <a name="custom-analyzer-example"></a>Özel çözümleyici örneği

Bu örnek, özel seçeneklerle bir çözümleyici tanımını gösterir. Karakter filtreleri, simgeler ve belirteç filtreleri için özel seçenekler adlandırılmış yapılar olarak ayrı olarak belirtilir ve ardından çözümleyici tanımında başvurulur. Önceden tanımlanmış öğeler, olduğu gibi kullanılır ve yalnızca adı ile başvurulur.

Bu örnekte izlenecek:

+ Çözümleyiciler, aranabilir bir alan için alan sınıfının bir özelliğidir.

+ Özel çözümleyici, Dizin tanımının bir parçasıdır. Bu, hafif bir şekilde özelleştirilmiş olabilir (örneğin, tek bir filtrenin tek bir seçeneğini özelleştirme) veya birden çok yerde özelleştirilebilir.

+ Bu durumda, özel çözümleyici "my_analyzer" olur ve bu da "my_standard_tokenizer" özelleştirilmiş standart belirteç ayırıcı ve iki belirteç filtresi kullanır: küçük ve özelleştirilmiş asciifolding filtresi "my_asciifolding".

+ Ayrıca, "map_dash" ve "remove_whitespace" 2 özel char filtrelerini de tanımlar. İlki tüm tireleri alt çizgi ile değiştirir, ikinci bir tane tüm boşlukları kaldırır. Eşleme kurallarında boşluklar UTF-8 olarak kodlanmalıdır. Char filtreleri simgeleştirme öncesinde uygulanır ve sonuçta elde edilen belirteçleri etkiler (çizgi ve boşluklar üzerinde standart belirteç ayırıcı işaretleri ve alt çizgi üzerinde değil).

```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"my_analyzer"
        }
     ],
     "analyzers":[
        {
           "name":"my_analyzer",
           "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
           "charFilters":[
              "map_dash",
              "remove_whitespace"
           ],
           "tokenizer":"my_standard_tokenizer",
           "tokenFilters":[
              "my_asciifolding",
              "lowercase"
           ]
        }
     ],
     "charFilters":[
        {
           "name":"map_dash",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["-=>_"]
        },
        {
           "name":"remove_whitespace",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["\\u0020=>"]
        }
     ],
     "tokenizers":[
        {
           "name":"my_standard_tokenizer",
           "@odata.type":"#Microsoft.Azure.Search.StandardTokenizerV2",
           "maxTokenLength":20
        }
     ],
     "tokenFilters":[
        {
           "name":"my_asciifolding",
           "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
           "preserveOriginal":true
        }
     ]
  }
```

<a name="Per-field-analyzer-assignment-example"></a>

### <a name="per-field-analyzer-assignment-example"></a>Alan başına çözümleyici atama örneği

Standart çözümleyici varsayılandır. Varsayılan değerini, model Çözümleyicisi gibi farklı bir önceden tanımlı çözümleyici ile değiştirmek istediğinizi varsayalım. Özel seçenekleri ayarlamadıysanız, yalnızca alan tanımında adıyla belirtmeniz gerekir.

"Analyzer" öğesi, alan temelinde standart çözümleyici 'yi geçersiz kılar. Genel geçersiz kılma yok. Bu örnekte, `text1` model Çözümleyicisi 'ni kullanır ve `text2` bir çözümleyici belirtmeyen, Varsayılanı kullanır.

```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text1",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"pattern"
        },
        {
           "name":"text2",
           "type":"Edm.String",
           "searchable":true
        }
     ]
  }
```

<a name="Mixing-analyzers-for-indexing-and-search-operations"></a>

### <a name="mixing-analyzers-for-indexing-and-search-operations"></a>Dizin oluşturma ve arama işlemleri için Çözümleyicileri karıştırma

API 'Ler, dizin oluşturma ve arama için farklı çözümleyiciler belirtmeye yönelik ek dizin öznitelikleri içerir. SearchAnalyzer ve ındexanalyzer öznitelikleri bir çift olarak belirtilmelidir ve tek çözümleyici özniteliği değiştiriliyor.


```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
     ],
  }
```

<a name="Language-analyzer-example"></a>

### <a name="language-analyzer-example"></a>Dil Çözümleyicisi örneği

Farklı dillerdeki dizeleri içeren alanlar dil Çözümleyicisi kullanabilir, diğer alanlar da varsayılan olarak korunur (veya başka bir önceden tanımlanmış veya özel çözümleyici kullanır). Dil Çözümleyicisi kullanıyorsanız, hem dizin oluşturma hem de arama işlemleri için kullanılması gerekir. Dil Çözümleyicisi kullanan alanlarda dizin oluşturma ve arama için farklı çözümleyiciler bulunamaz.

```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
        {
           "name":"text_fr",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"fr.lucene"
        }
     ],
  }
```

## <a name="c-examples"></a>C# örnekleri

.NET SDK kod örneklerini kullanıyorsanız, bu örnekleri kullanmak veya çözümleyiciler yapılandırmak için ekleyebilirsiniz.

+ [Yerleşik çözümleyici atama](#Assign-a-language-analyzer)
+ [Çözümleyici yapılandırma](#Define-a-custom-analyzer)

<a name="Assign-a-language-analyzer"></a>

### <a name="assign-a-language-analyzer"></a>Dil Çözümleyicisi atama

Olduğu gibi kullanılan ve yapılandırma olmadan kullanılan çözümleyici, bir alan tanımında belirtilmiştir. Dizinin **[çözümleyiciler]** bölümünde giriş oluşturmak için bir gereksinim yoktur. 

Dil Çözümleyicileri olduğu gibi kullanılır. Bunları kullanmak için, Azure Bilişsel Arama 'nda desteklenen bir metin Çözümleyicisi sağlayan [LexicalAnalyzerName](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzername) türünü belirterek [LexicalAnalyzer](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzer)çağırın.

Özel çözümleyiciler, alan tanımında benzer şekilde belirtilmiştir, ancak bunun çalışması için, sonraki bölümde açıklandığı gibi, Dizin tanımında çözümleyici belirtmeniz gerekir.

```csharp
    public partial class Hotel
    {
       . . . 
        [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
        public string Description { get; set; }

        [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrLucene)]
        [JsonPropertyName("Description_fr")]
        public string DescriptionFr { get; set; }

        [SearchableField(AnalyzerName = "url-analyze")]
        public string Url { get; set; }
      . . .
    }
```

<a name="Define-a-custom-analyzer"></a>

### <a name="define-a-custom-analyzer"></a>Özel çözümleyici tanımlama

Özelleştirme veya yapılandırma gerektiğinde, bir dizine çözümleyici yapısı ekleyin. Bunu tanımladıktan sonra, bir önceki örnekte gösterildiği gibi alan tanımını ekleyebilirsiniz.

Bir [CustomAnalyzer](/dotnet/api/azure.search.documents.indexes.models.customanalyzer) nesnesi oluşturun. Özel çözümleyici, bilinen bir Simgeleştirici, sıfır veya daha fazla belirteç filtresinin ve sıfır ya da daha fazla karakter filtresi adının Kullanıcı tanımlı birleşimidir:

+ [CustomAnalyzer. Tokenizer](/dotnet/api/microsoft.azure.search.models.customanalyzer.tokenizer)
+ [CustomAnalyzer. TokenFilters](/dotnet/api/microsoft.azure.search.models.customanalyzer.tokenfilters)
+ [CustomAnalyzer. CharFilters](/dotnet/api/microsoft.azure.search.models.customanalyzer.charfilters)

Aşağıdaki örnek, [uax_url_email belirteç ayırıcı](/dotnet/api/microsoft.azure.search.models.customanalyzer.tokenizer) ve [küçük harfli belirteç filtresi](/dotnet/api/microsoft.azure.search.models.tokenfiltername.lowercase)kullanan "URL-çözümle" adlı özel bir çözümleyici oluşturur.

```csharp
private static void CreateIndex(string indexName, SearchIndexClient adminClient)
{
   FieldBuilder fieldBuilder = new FieldBuilder();
   var searchFields = fieldBuilder.Build(typeof(Hotel));

   var analyzer = new CustomAnalyzer("url-analyze", "uax_url_email")
   {
         TokenFilters = { TokenFilterName.Lowercase }
   };

   var definition = new SearchIndex(indexName, searchFields);

   definition.Analyzers.Add(analyzer);

   adminClient.CreateOrUpdateIndex(definition);
}
```

Daha fazla örnek için bkz. [CustomAnalyzerTests. cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Microsoft.Azure.Search/tests/Tests/CustomAnalyzerTests.cs).

## <a name="next-steps"></a>Sonraki adımlar

[Azure bilişsel arama 'de tam metin aramasında](search-lucene-query-architecture.md)sorgu yürütmeye ilişkin ayrıntılı bir açıklama bulabilirsiniz. Makale, yüzeyde sayaç kullanımı kolay olabilecek davranışları açıklamak için örnekleri kullanır.

Çözümleyiciler hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

+ [Dil çözümleyicileri](index-add-language-analyzers.md)
+ [Özel çözümleyiciler](index-add-custom-analyzers.md)
+ [Arama dizini oluşturma](search-what-is-an-index.md)
+ [Çok dilli bir dizin oluşturma](search-language-support.md)