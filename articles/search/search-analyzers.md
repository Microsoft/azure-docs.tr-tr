---
title: Dil ve metin işleme Çözümleyicileri
titleSuffix: Azure Cognitive Search
description: Varsayılan standart Lucene öğesini özel, önceden tanımlanmış veya dile özgü alternatifler ile değiştirmek için bir dizinde aranabilir metin alanlarına çözümleyiciler atayın.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 32ac91df042eb29c39cc54b738dbb96aff3104f3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496497"
---
# <a name="analyzers-for-text-processing-in-azure-cognitive-search"></a>Azure Bilişsel Arama metin işleme için çözümleyiciler

*Çözümleyici* , sorgu dizelerinde ve dizini oluşturulmuş belgelerde metin işlemeden sorumlu [tam metin arama altyapısının](search-lucene-query-architecture.md) bir bileşenidir. Farklı çözümleyiciler, senaryoya bağlı olarak metni farklı şekillerde işleyebilir. Dil Çözümleyicileri, arama kalitesini artırmak için dil kurallarını kullanarak metin işler, diğer çözümleyiciler ise, örneğin, daha küçük harfe dönüştürme gibi daha temel görevler gerçekleştirir. 

Dil Çözümleyicileri en sık kullanılan ve bir Azure Bilişsel Arama dizininde her aranabilir alana atanan varsayılan dil Çözümleyicisi vardır. Aşağıdaki dil dönüştürmeleri, metin analizi sırasında tipik bir şekilde yapılır:

+ Önemli olmayan kelimeler (stopwords) ve noktalama işaretleri kaldırılır.
+ Tümcecikler ve hecelenmiş sözcükler bileşen bölümlerine bölünür.
+ Büyük harfli kelimeler küçük harfle kaldırılır.
+ Sözcükler, zaman hali ne olursa olsun bir eşleşme bulunabilmesi için kök formlara düşürülür.

Dil Çözümleyicileri, bir metin girişini, bilgi depolama ve alma açısından verimli bir şekilde temel veya kök formlara dönüştürür. Dizin oluşturma sırasında, Dizin oluşturulduğunda ve sonra arama sırasında, Dizin okunarak bir kez dönüştürme gerçekleşir. Her iki işlem için de aynı çözümleyici 'yi kullanıyorsanız, bekleeceğiniz arama sonuçlarını elde etmeniz daha olasıdır.

## <a name="default-analyzer"></a>Varsayılan çözümleyici  

Azure Bilişsel Arama, varsayılan olarak [Apache Lucene standart Çözümleyicisi 'ni (Standart Lucene)](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) kullanır ve bu, metni ["Unicode metin segmentleme"](https://unicode.org/reports/tr29/) kurallarından sonra öğelere ayırır. Ayrıca, standart çözümleyici tüm karakterleri küçük harf biçimine dönüştürür. Dizini oluşturulmuş belgeler ve arama terimleri, dizin oluşturma ve sorgu işleme sırasında Analize gider.  

Bu, aranabilir her alan için otomatik olarak kullanılır. Alan temelinde varsayılan ayarı geçersiz kılabilirsiniz. Alternatif çözümleyiciler, [kullanılabilir çözümleyiciler listesinden](index-add-custom-analyzers.md#AnalyzerTable)bir [dil Çözümleyicisi](index-add-language-analyzers.md), [özel çözümleyici](index-add-custom-analyzers.md)veya önceden tanımlanmış bir çözümleyici olabilir.


## <a name="types-of-analyzers"></a>Çözümleyiciler türleri

Aşağıdaki listede Azure Bilişsel Arama 'de hangi çözümleyiciler kullanılabildiği açıklanmaktadır.

| Kategori | Açıklama |
|----------|-------------|
| [Standart Lucene Çözümleyicisi](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Varsayılan. Belirtim veya yapılandırma gerekli değildir. Bu genel amaçlı çözümleyici, çoğu dil ve senaryo için iyi bir performans uygular.|
| Önceden tanımlanmış çözümleyiciler | Olduğu gibi kullanılması amaçlanan, tamamlanmış bir ürün olarak sunulur. <br/>İki tür vardır: özelleştirilmiş ve dil. Bunları "önceden tanımlanmış" olarak belirlemek, yapılandırma veya özelleştirme olmadan bunlara ada göre başvurmanıza neden olur. <br/><br/>[Özelleştirilmiş (dilden bağımsız) çözümleyiciler](index-add-custom-analyzers.md#AnalyzerTable) , metin girdileri özel işlem veya minimum işleme gerektirdiğinde kullanılır. Dili olmayan önceden tanımlı çözümleyiciler, **Asciifolding**, **anahtar sözcük**, **model**, **Simple**, **stop**, **Whitespace**içerir.<br/><br/>[Dil Çözümleyicileri](index-add-language-analyzers.md) , tek tek diller için zengin dil desteği gerektiğinde kullanılır. Azure Bilişsel Arama, 35 Lucene dil Çözümleyicileri ve 50 Microsoft doğal dil işleme Çözümleyicileri destekler. |
|[Özel çözümleyiciler](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | , Bir Simgeleştirici (zorunlu) ve isteğe bağlı filtrelerden (char veya token) oluşan, mevcut öğelerin bir birleşiminin Kullanıcı tanımlı yapılandırmasını ifade eder.|

**Model** veya **durdurma**gibi önceden tanımlanmış birkaç çözümleyici, sınırlı bir yapılandırma seçenekleri kümesini destekler. Bu seçenekleri ayarlamak için, önceden tanımlanmış çözümleyicisinden ve [önceden tanımlanmış çözümleyici başvurusunda](index-add-custom-analyzers.md#AnalyzerTable)belgelenen alternatif seçeneklerden birine sahip olan özel bir çözümleyici 'yi verimli bir şekilde oluşturursunuz. Tüm özel yapılandırmada olduğu gibi, yeni yapılandırmanızı Lucene model çözümleyicisinden ayırt etmek için *Mypatternanalyzer* gibi bir adla birlikte belirtin.

## <a name="how-to-specify-analyzers"></a>Çözümleyiciler belirtme

1. (yalnızca özel çözümleyiciler için) Dizin tanımında adlandırılmış bir **çözümleyici** bölümü oluşturun. Daha fazla bilgi için bkz. [Dizin oluşturma](https://docs.microsoft.com/rest/api/searchservice/create-index) ve ayrıca [özel çözümleyiciler ekleme](index-add-custom-analyzers.md).

2. Dizindeki bir [alan tanımında](https://docs.microsoft.com/rest/api/searchservice/create-index) , alanın **çözümleyici** özelliğini bir hedef Çözümleyicisinin adı (örneğin, `"analyzer" = "keyword"`olarak ayarlayın. Geçerli değerler, dizin şemasında tanımlanmış, önceden tanımlanmış bir çözümleyici, dil Çözümleyicisi veya özel çözümleyici adı içerir. Dizin, hizmette oluşturulmadan önce Dizin tanımı aşamasında Çözümleyicisi atamaya planlayın.

3. İsteğe bağlı olarak, bir **çözümleyici** özelliği yerine, dizin oluşturma ve sorgulama Için **ındexanalyzer** ve **searchAnalyzer** alan parametrelerini kullanarak farklı çözümleyiciler ayarlayabilirsiniz. Veri hazırlama ve alma işlemleri için farklı çözümleyiciler kullanacaksınız ve bu etkinliklerden biri başka bir dönüşüm için gerekli değildir.

Zaten fiziksel olarak oluşturulmuş bir alana **çözümleyici** veya **ındexanalyzer** atamaya izin verilmez. Bunlardan herhangi biri belirsiz ise, hangi eylemlerin yeniden derleme gerektirdiğini ve nedenini belirten bir döküm için aşağıdaki tabloyu gözden geçirin.
 
 | Senaryo | Etki | Adımlar |
 |----------|--------|-------|
 | Yeni alan ekle | En az | Alan henüz şemada yoksa, bu alan, dizinde henüz bir fiziksel varlığı olmadığından, yapılacak bir alan düzeltmesi yoktur. Var olan bir dizine yeni bir alan eklemek için [güncelleştirme dizinini](https://docs.microsoft.com/rest/api/searchservice/update-index) kullanabilir ve onu doldurmak Için [mergeorupload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) kullanabilirsiniz.|
 | Var olan bir dizinli alana **çözümleyici** veya **ındexanalyzer** ekleyin. | [derlemesine](search-howto-reindex.md) | Bu alan için ters çevrilen Dizin baştan sona yeniden oluşturulmalıdır ve bu alanların içeriğinin yeniden oluşturulması gerekir. <br/> <br/>Etkin geliştirme altındaki dizinler için, yeni alan tanımını seçmek üzere dizini [silin](https://docs.microsoft.com/rest/api/searchservice/delete-index) ve [oluşturun](https://docs.microsoft.com/rest/api/searchservice/create-index) . <br/> <br/>Üretimde dizinler için, düzeltilen tanımı sağlamak üzere yeni bir alan oluşturarak yeniden derlemeyi erteleyebilirsiniz ve eskisini yerine kullanmaya başlayabilirsiniz. Yeni alanı birleştirmek için [güncelleştirme dizinini](https://docs.microsoft.com/rest/api/searchservice/update-index) kullanın ve doldurmak Için [mergeorupload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) kullanın. Daha sonra, planlı dizin hizmeti 'nin bir parçası olarak, eski alanları kaldırmak için dizini temizleyebilirsiniz. |

## <a name="when-to-add-analyzers"></a>Çözümleyiciler ne zaman eklenir

Çözümleyiciler eklemek ve atamak için en iyi süre, etkin geliştirme sırasında dizinleri bırakma ve yeniden oluşturma işlemi sırasında yapılır.

Dizin tanımı, bir dizine yeni analiz yapıları ekleyebilirsiniz, ancak bu hatadan kaçınmak istiyorsanız [dizini güncelleştirmek](https://docs.microsoft.com/rest/api/searchservice/update-index) Için **Allowındexkesinti** bayrağını geçirmeniz gerekir:

*"Dizin güncelleştirmesine izin verilmiyor, çünkü kapalı kalma süresine yol açacaktı. Varolan bir dizine yeni çözümleyiciler, belirteçler, belirteç filtreleri veya karakter filtreleri eklemek için, Dizin güncelleştirme isteğinde ' Allowwındexını ' sorgu parametresini ' true ' olarak ayarlayın. Bu işlemin dizininizi en az birkaç saniyede çevrimdışına koyacağına, dizin oluşturma ve sorgu isteklerinizin başarısız olmasına neden olduğunu unutmayın. Dizinin performansı ve yazma kullanılabilirliği, Dizin güncelleştirildikten sonra çok fazla dakika boyunca veya çok büyük dizinler için daha uzun olabilir. "*

Bir alana çözümleyici atarken aynı durum geçerlidir. Çözümleyici, alan tanımının ayrılmaz bir parçasıdır, bu nedenle yalnızca alan oluşturulduğunda eklenebilir. Mevcut alanlara çözümleyiciler eklemek istiyorsanız, dizini [bırakıp yeniden oluşturmanız](search-howto-reindex.md) veya çözümleyiciyle istediğiniz yeni bir alan eklemeniz gerekir.

Belirtildiği gibi, **searchAnalyzer** varyantı bir istisnadır. Çözümleyiciler (**çözümleyici**, **ındexanalyzer**, **searchAnalyzer**) belirtmek için üç yol, var olan bir alanda yalnızca **searchAnalyzer** özniteliği değiştirilebilir.

## <a name="recommendations-for-working-with-analyzers"></a>Çözümleyiciler ile çalışmaya yönelik öneriler

Bu bölümde, çözümleyiciler ile nasıl çalışılacağı hakkında öneriler sunulmaktadır.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>Belirli gereksinimleriniz yoksa okuma-yazma için bir çözümleyici

Azure Bilişsel Arama, dizin oluşturma ve ek **ındexanalyzer** ve **searchAnalyzer** alan parametreleri aracılığıyla arama için farklı çözümleyiciler belirtmenize olanak tanır. Belirtilmemişse, **çözümleyici** özelliği ile ayarlanan çözümleyici, hem dizin oluşturma hem de arama için kullanılır. `analyzer` belirtilmemişse, varsayılan standart Lucene Çözümleyicisi kullanılır.

Genel bir kural, aynı çözümleyici 'yi hem dizin oluşturma hem de sorgulama için, belirli gereksinimler aksini belirtmedikçe kullanır. Kapsamlı olarak test ettiğinizden emin olun. Metin işleme, arama ve dizin oluşturma sırasında farklılık gösterdiğinde, arama ve dizin oluşturma Çözümleyicisi yapılandırmalarının hizalanmamış olması durumunda sorgu terimleri ve dizine alınmış terimler arasında uyumsuzluk riskini çalıştırırsınız.

### <a name="test-during-active-development"></a>Etkin geliştirme sırasında test etme

Standart çözümleyici 'nin geçersiz kılınması, dizin yeniden oluşturmayı gerektirir. Mümkünse, üretime bir dizin vermeden önce, etkin geliştirme sırasında hangi çözümleyicilerin kullanılacağını belirleyin.

### <a name="inspect-tokenized-terms"></a>Simgeleştirilmiş terimleri İncele

Arama beklenen sonuçları döndürmediğinde, en olası senaryo sorgudaki terim girişleri ile dizinde simgeleştirilmiş terimler arasındaki belirteç tutarsızlıklardan oluşur. Belirteçler aynı değilse, Eşleşmeler bir hata ile başarısız olur. Belirteç ayırıcı çıkışını denetlemek için, [API 'yi](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) araştırma aracı olarak Çözümle ' yi kullanmanızı öneririz. Yanıt, belirli bir çözümleyici tarafından oluşturulan belirteçlerden oluşur.

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

* Çözümleyiciler, aranabilir bir alan için alan sınıfının bir özelliğidir.
* Özel çözümleyici, Dizin tanımının bir parçasıdır. Bu, hafif bir şekilde özelleştirilmiş olabilir (örneğin, tek bir filtrenin tek bir seçeneğini özelleştirme) veya birden çok yerde özelleştirilebilir.
* Bu durumda, özel çözümleyici "my_analyzer" olur ve bu, sırasıyla özelleştirilmiş bir standart belirteç ayırıcı "my_standard_tokenizer" ve iki belirteç filtresi kullanır: küçük ve özelleştirilmiş asciifolding filtresi "my_asciifolding".
* Ayrıca, "map_dash" ve "remove_whitespace" 2 özel char filtrelerini de tanımlar. İlki tüm tireleri alt çizgi ile değiştirir, ikinci bir tane tüm boşlukları kaldırır. Eşleme kurallarında boşluklar UTF-8 olarak kodlanmalıdır. Char filtreleri simgeleştirme öncesinde uygulanır ve sonuçta elde edilen belirteçleri etkiler (çizgi ve boşluklar üzerinde standart belirteç ayırıcı işaretleri ve alt çizgi üzerinde değil).

~~~~
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
~~~~

<a name="Per-field-analyzer-assignment-example"></a>

### <a name="per-field-analyzer-assignment-example"></a>Alan başına çözümleyici atama örneği

Standart çözümleyici varsayılandır. Varsayılan değerini, model Çözümleyicisi gibi farklı bir önceden tanımlı çözümleyici ile değiştirmek istediğinizi varsayalım. Özel seçenekleri ayarlamadıysanız, yalnızca alan tanımında adıyla belirtmeniz gerekir.

"Analyzer" öğesi, alan temelinde standart çözümleyici 'yi geçersiz kılar. Genel geçersiz kılma yok. Bu örnekte `text1`, bir çözümleyici belirtmeyen `text2`model Çözümleyicisi ve kullanır.

~~~~
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
~~~~

<a name="Mixing-analyzers-for-indexing-and-search-operations"></a>

### <a name="mixing-analyzers-for-indexing-and-search-operations"></a>Dizin oluşturma ve arama işlemleri için Çözümleyicileri karıştırma

API 'Ler, dizin oluşturma ve arama için farklı çözümleyiciler belirtmeye yönelik ek dizin öznitelikleri içerir. **SearchAnalyzer** ve **ındexanalyzer** öznitelikleri bir çift olarak belirtilmelidir ve tek **çözümleyici** özniteliği değiştiriliyor.


~~~~
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
~~~~

<a name="Language-analyzer-example"></a>

### <a name="language-analyzer-example"></a>Dil Çözümleyicisi örneği

Farklı dillerdeki dizeleri içeren alanlar dil Çözümleyicisi kullanabilir, diğer alanlar da varsayılan olarak korunur (veya başka bir önceden tanımlanmış veya özel çözümleyici kullanır). Dil Çözümleyicisi kullanıyorsanız, hem dizin oluşturma hem de arama işlemleri için kullanılması gerekir. Dil Çözümleyicisi kullanan alanlarda dizin oluşturma ve arama için farklı çözümleyiciler bulunamaz.

~~~~
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
~~~~

## <a name="c-examples"></a>C#örnekler

.NET SDK kod örneklerini kullanıyorsanız, bu örnekleri kullanmak veya çözümleyiciler yapılandırmak için ekleyebilirsiniz.

+ [Yerleşik çözümleyici atama](#Assign-a-language-analyzer)
+ [Çözümleyici yapılandırma](#Define-a-custom-analyzer)

<a name="Assign-a-language-analyzer"></a>

### <a name="assign-a-language-analyzer"></a>Dil Çözümleyicisi atama

Olduğu gibi kullanılan ve yapılandırma olmadan kullanılan çözümleyici, bir alan tanımında belirtilmiştir. Çözümleyici yapısı oluşturma gereksinimi yoktur. 

Bu örnek, Açıklama alanlarına Microsoft Ingilizce ve Fransızca Çözümleyicileri atar. Bu bir kod parçacığı, otel dizininin daha büyük bir tanımından alınmış ve [Dotnethowto](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) örneğinin Hotels.cs dosyasındaki otel sınıfını kullanarak oluşturuluyor.

Azure Bilişsel Arama 'de desteklenen bir metin Çözümleyicisi sağlayan [analiz Zeradı](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) türünü belirterek [çözümleyici](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet)'yi çağırın.

```csharp
    public partial class Hotel
    {
       . . . 

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
        [JsonProperty("description")]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrLucene)]
        [JsonProperty("description_fr")]
        public string DescriptionFr { get; set; }

      . . .
    }
```
<a name="Define-a-custom-analyzer"></a>

### <a name="define-a-custom-analyzer"></a>Özel çözümleyici tanımlama

Özelleştirme veya yapılandırma gerektiğinde, bir dizine çözümleyici yapısı eklemeniz gerekir. Bunu tanımladıktan sonra, bir önceki örnekte gösterildiği gibi alan tanımını ekleyebilirsiniz.

Bir [CustomAnalyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.customanalyzer?view=azure-dotnet) nesnesi oluşturun. Daha fazla örnek için bkz. [CustomAnalyzerTests.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Microsoft.Azure.Search/tests/Tests/CustomAnalyzerTests.cs).

```csharp
{
   var definition = new Index()
   {
         Name = "hotels",
         Fields = FieldBuilder.BuildForType<Hotel>(),
         Analyzers = new[]
            {
               new CustomAnalyzer()
               {
                     Name = "url-analyze",
                     Tokenizer = TokenizerName.UaxUrlEmail,
                     TokenFilters = new[] { TokenFilterName.Lowercase }
               }
            },
   };

   serviceClient.Indexes.Create(definition);
```

## <a name="next-steps"></a>Sonraki adımlar

+ [Tam metin aramasının Azure bilişsel arama 'de nasıl çalıştığına](search-lucene-query-architecture.md)ilişkin kapsamlı bir açıklama inceleyin. Bu makalede, yüzeyde sayaç kullanımı kolay olabilecek davranışları açıklamak için örnekleri kullanılmaktadır.

+ [Belgeleri ara](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) örnek bölümünde veya portalda arama Gezgini 'ndeki [basit sorgu söz diziminde](query-simple-syntax.md) ek sorgu söz dizimini deneyin.

+ [Dile özgü sözcük Çözümleyicileri çözümleyicilerinin](index-add-language-analyzers.md)nasıl uygulanacağını öğrenin.

+ Tek tek alanlarda en az işlem veya özel işleme için [özel Çözümleyicileri yapılandırın](index-add-custom-analyzers.md) .

## <a name="see-also"></a>Ayrıca bkz.

 [Belgelerde ara REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

 [Basit sorgu söz dizimi](query-simple-syntax.md) 

 [Tam Lucene sorgu söz dizimi](query-lucene-syntax.md) 
 
 [Arama sonuçlarını işleme](search-pagination-page-layout.md)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
