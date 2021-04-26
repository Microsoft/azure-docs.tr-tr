---
title: Dize alanlarına özel çözümleyiciler ekleme
titleSuffix: Azure Cognitive Search
description: Dizin oluşturma ve sorgular sırasında dizelerde metin analizi gerçekleştirmek için metin simgeleyicileri ve karakter filtreleri yapılandırın.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 831e57a68c79c245b96baec0fc3d062c4c9112c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104604449"
---
# <a name="add-custom-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Azure Bilişsel Arama dizinindeki dize alanlarına özel çözümleyiciler ekleme

*Özel çözümleyici* , belirteç ayırıcı, bir veya daha fazla belirteç filtresi ve arama dizininde tanımladığınız bir veya daha fazla karakter filtresinin birleşimidir ve ardından özel analiz gerektiren alan tanımlarına başvurur. Belirteç ayırıcı, belirteçlere son metin ve Simgeleştirici tarafından yayılan belirteçleri değiştirmeye yönelik belirteç filtrelerinden sorumludur. Karakter filtreleri, giriş metnini Simgeleştirici tarafından işlenmeden önce hazırlar. 

Özel bir çözümleyici, hangi analiz veya filtreleme türlerini ve bunların oluşma sırasını seçmenize olanak tanıyarak, metni, dizin haline dönüştürülebilir ve aranabilir belirteçlere dönüştürme sürecini denetlemenizi sağlar. Standart olarak maxTokenLength değiştirme gibi özel seçeneklerle yerleşik bir çözümleyici kullanmak istiyorsanız, bu seçenekleri ayarlamak için Kullanıcı tanımlı bir ada sahip özel bir çözümleyici oluşturursunuz.

Özel çözümleyiciler yararlı olabilecek durumlar şunlardır:

- Metin girişlerinin simgeleştirmesinden önce HTML işaretlemesini kaldırmak için karakter filtreleri kullanma veya belirli karakterleri ya da sembolleri değiştirme.

- Fonetik arama. Bir sözcüğün nasıl seslendiğinden ve nasıl yazıldığını temel alarak aramayı etkinleştirmek için bir fonetik filtresi ekleyin.  

- Sözcük analizini devre dışı bırakın. Çözümlenmemiş aranabilir alanlar oluşturmak için anahtar sözcük Çözümleyicisi ' ni kullanın.  

- Hızlı önek/sonek araması. Hızlı önek eşleştirmeyi etkinleştirmek için sözcük ön eklerini dizine eklemek için Edge N-gram belirteç filtresini ekleyin. Sonek eşleştirmeyi yapmak için ters belirteç filtresiyle birleştirin.  

- Özel simgeleştirme. Örneğin, boşlukları bir sınırlayıcı olarak kullanarak belirteçlere bölmek için Simgeleştirici boşluk kullanın  

- ASCII katlama. Ö veya ê gibi aksanları, arama terimlerinde normalleştirmek için standart ASCII katlama filtresini ekleyin.  

Özel bir çözümleyici oluşturmak için, tasarım zamanında bir dizinin "çözümleyiciler" bölümünde belirtin ve ardından "Analyzer" özelliğini ya da "ındexanalyzer" ve "searchAnalyzer" çiftini kullanarak aranabilir, Edm. String alanlarında başvuru yapın.

> [!NOTE]  
> Oluşturduğunuz özel çözümleyiciler Azure portal gösterilmez. Özel çözümleyici eklemenin tek yolu, bir dizini tanımlayan koddur. 

## <a name="create-a-custom-analyzer"></a>Özel çözümleyici oluşturma

Çözümleyici tanımı bir ad, tür, bir veya daha fazla karakter filtresi, en fazla bir Simgeleştirici ve simgeleştirme sonrası işleme için bir veya daha fazla belirteç filtresi içerir. Karakter filtreleri simgeleştirme öncesinde uygulanır. Belirteç filtreleri ve karakter filtreleri soldan sağa uygulanır.

- Özel bir Çözümleyicisindeki adların benzersiz olması ve yerleşik çözümleyiciler, belirteçlerin, belirteç filtreleri veya karakter filtreleriyle aynı olamaz. Yalnızca harf, rakam, boşluk, tire veya alt çizgi içermelidir, yalnızca alfasayısal karakterlerle başlayıp bitebilirler ve 128 karakterle sınırlıdır. 

- Tür #Microsoft. Azure. Search. CustomAnalyzer olmalıdır.

- "charFilters" bir veya daha fazla [karakter filtresinden](#CharFilter)bir veya daha fazla filtre olabilir ve bu, belirteçten önce, belirtilen sıraya göre işlenir. Bazı karakter filtreleri, "bir" charFilter bölümünde ayarlanabilir seçeneklere sahiptir. Karakter filtreleri isteğe bağlıdır.

- "Simgeleştirici" tam olarak bir [Simgeleştirici](#tokenizers). Değer gereklidir. Birden fazla belirteç Oluşturucu gerekirse, birden çok özel çözümleyici oluşturabilir ve bunları Dizin şemanızda alan temelinde atayabilirsiniz.

- "tokenFilters", [belirteç filtrelerinden](#TokenFilters)bir veya daha fazla filtre olabilir ve bu, belirteçlerin ardından, belirtilen sırayla işlenir. Seçenekleri olan belirteç filtreleri için yapılandırmayı belirtmek üzere bir "tokenFilter" bölümü ekleyin. Belirteç filtreleri isteğe bağlıdır.

Çözümleyiciler 300 karakterden daha uzun belirteçler üretmemelidir, yoksa dizin oluşturma başarısız olur. Uzun belirteci kırpmak veya hariç tutmak için, sırasıyla **truncatetokenfilter** ve **lengthtokenfilter** kullanın. Başvuru için [**belirteç filtrelerine**](#TokenFilters) bakın.

```json
"analyzers":(optional)[
   {
      "name":"name of analyzer",
      "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
      "charFilters":[
         "char_filter_name_1",
         "char_filter_name_2"
      ],
      "tokenizer":"tokenizer_name",
      "tokenFilters":[
         "token_filter_name_1",
         "token_filter_name_2"
      ]
   },
   {
      "name":"name of analyzer",
      "@odata.type":"#analyzer_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"charFilters":(optional)[
   {
      "name":"char_filter_name",
      "@odata.type":"#char_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenizers":(optional)[
   {
      "name":"tokenizer_name",
      "@odata.type":"#tokenizer_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenFilters":(optional)[
   {
      "name":"token_filter_name",
      "@odata.type":"#token_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
]
```

Bir dizin tanımı içinde, bu bölümü bir oluştur dizin isteği gövdesinde herhangi bir yere yerleştirebilirsiniz, ancak genellikle sona geçer:  

```json
{
  "name": "name_of_index",
  "fields": [ ],
  "suggesters": [ ],
  "scoringProfiles": [ ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) { },
  "analyzers":(optional)[ ],
  "charFilters":(optional)[ ],
  "tokenizers":(optional)[ ],
  "tokenFilters":(optional)[ ]
}
```

Çözümleyici tanımı, daha büyük dizinin bir parçasıdır. Karakter filtreleri, simgeler ve belirteç filtreleri için tanımlar, yalnızca özel seçenekler ayarlıyorsanız dizine eklenir. Var olan bir filtreyi veya belirteç ayırıcı 'ı olduğu gibi kullanmak için, bunu çözümleyici tanımında adıyla belirtin. Daha fazla bilgi için bkz. [Dizin oluşturma (REST)](/rest/api/searchservice/create-index). Daha fazla örnek için bkz. [Azure bilişsel arama çözümleyiciler ekleme](search-analyzers.md#examples).

## <a name="test-custom-analyzers"></a>Özel Çözümleyicileri test et

Bir çözümleyici 'nin metin olarak belirteçlere nasıl bölüneceğini görmek için [Test çözümleyici 'yi (REST)](/rest/api/searchservice/test-analyzer) kullanabilirsiniz.

**İstek**

```http
  POST https://[search service name].search.windows.net/indexes/[index name]/analyze?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

  {
     "analyzer":"my_analyzer",
     "text": "Vis-à-vis means Opposite"
  }
```

**Response**

```http
  {
    "tokens": [
      {
        "token": "vis_a_vis",
        "startOffset": 0,
        "endOffset": 9,
        "position": 0
      },
      {
        "token": "vis_à_vis",
        "startOffset": 0,
        "endOffset": 9,
        "position": 0
      },
      {
        "token": "means",
        "startOffset": 10,
        "endOffset": 15,
        "position": 1
      },
      {
        "token": "opposite",
        "startOffset": 16,
        "endOffset": 24,
        "position": 2
      }
    ]
  }
```

## <a name="update-custom-analyzers"></a>Özel Çözümleyicileri Güncelleştir

Çözümleyici, belirteç ayırıcı, belirteç filtresi veya karakter filtresi tanımlandıktan sonra değiştirilemez. Var olan bir dizine yalnızca `allowIndexDowntime` bayrak, Dizin güncelleştirme isteğinde true olarak ayarlanırsa yeni olanlar eklenebilir:

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```

Bu işlem, dizin oluşturma ve sorgu isteklerinizin başarısız olmasına neden olacak şekilde dizininizi en az birkaç saniyede çevrimdışı hale getirir. Dizinin performansı ve yazma kullanılabilirliği, Dizin güncelleştirildikten sonra birkaç dakika boyunca veya çok büyük dizinler için daha uzun olabilir, ancak bu etkileri geçicidir ve sonunda kendi kendine çözümlenir.

<a name="built-in-analyzers"></a>

## <a name="built-in-analyzers"></a>Yerleşik çözümleyiciler

Özel seçeneklerle yerleşik bir çözümleyici kullanmak istiyorsanız, özel çözümleyici oluşturmak, bu seçenekleri belirttiğiniz mekanizmadır. Buna karşılık, bir yerleşik çözümleyici 'yi olduğu gibi kullanmak için, yalnızca alan tanımında [ada göre başvuru](search-analyzers.md#how-to-specify-analyzers) yapmanız gerekir.

|**analyzer_name**|**analyzer_type**  <sup>1</sup>|**Açıklama ve Seçenekler**|  
|-----------------|-------------------------------|---------------------------|  
|[sözcükle](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)| (tür yalnızca seçenekler kullanılabilir olduğunda geçerlidir) |Bir alanın tüm içeriğini tek bir belirteç olarak değerlendirir. Bu, ZIP kodları, kimlikler ve bazı ürün adları gibi veriler için yararlıdır.|  
|[kalıp](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/PatternAnalyzer.html)|PatternAnalyzer|Bir normal ifade örüntüsünün ardından metni esnek olarak koşullara ayırır. </br></br>**Seçenekler** </br></br>küçük harf (tür: bool)-koşulların küçük olup olmadığını belirler. Varsayılan değer true 'dur. </br></br>[model](https://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html?is-external=true) (tür: dize)-belirteç ayırıcılarını eşleştirmek için bir normal ifade deseninin. `\W+`Sözcük olmayan karakterlerle eşleşen varsayılan değer varsayılandır. </br></br>[bayraklar](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (tür: dize)-normal ifade bayrakları. Varsayılan değer boş bir dizedir. İzin verilen değerler: CANON_EQ, CASE_INSENSITIVE, AÇıKLAMALAR, DOTALL, DEĞIŞMEZ değer, çok SATıRLı, UNICODE_CASE, UNIX_LINES </br></br>stopword (tür: dize dizisi)-stopword listesi. Varsayılan değer boş bir liste.|  
|[MPLE](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/SimpleAnalyzer.html)|(tür yalnızca seçenekler kullanılabilir olduğunda geçerlidir) |Metni mektupsız böler ve küçük harfe dönüştürür. |  
|[Stand](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) </br>(Standart. Lucene olarak da adlandırılır)|Standartçözümleyici|Standart Simgeleştirici, küçük harfli filtre ve durdurma filtresinden oluşan standart Lucene Çözümleyicisi. </br></br>**Seçenekler** </br></br>maxTokenLength (tür: int)-en büyük belirteç uzunluğu. Varsayılan değer 255 ' dir. Uzunluk üst sınırından daha uzun belirteçler bölünür. Kullanılabilecek maksimum belirteç uzunluğu 300 karakterdir. </br></br>stopword (tür: dize dizisi)-stopword listesi. Varsayılan değer boş bir liste.|  
|standardavbilimifolding. Lucene|(tür yalnızca seçenekler kullanılabilir olduğunda geçerlidir) |ASCII katlama filtresiyle standart çözümleyici. |  
|[durdurulması](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/StopAnalyzer.html)|StopAnalyzer|Metni mektupsız böler, küçük harfli ve durma sözcüğü belirteç filtrelerini uygular. </br></br>**Seçenekler** </br></br>stopword (tür: dize dizisi)-stopword listesi. Varsayılan değer Ingilizce için önceden tanımlanmış bir listesidir. |  
|[boşlu](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html)|(tür yalnızca seçenekler kullanılabilir olduğunda geçerlidir) |Tokenizer boşluk kullanan bir çözümleyici. 255 karakterden uzun belirteçler bölünür.|  

 <sup>1</sup> çözümleyici türlerine her zaman "#Microsoft. Azure. Search" ile kod ön eki uygulanır ve "patternanalyzer" aslında "#Microsoft. Azure. Search. PatternAnalyzer" olarak belirtilir. Kısaltma için öneki kaldırdık, ancak kodunuzda önek gereklidir.

Analyzer_type yalnızca özelleştirilebilecek çözümleyiciler için sağlanır. Hiçbir seçenek yoksa, anahtar sözcük Çözümleyicisi 'nde olduğu gibi, ilişkili #Microsoft. Azure. Search türü yoktur.

<a name="CharFilter"></a>

## <a name="character-filters"></a>Karakter filtreleri

Aşağıdaki tabloda, Apache Lucene kullanılarak uygulanan karakter filtreleri, Lucene API belgelerine bağlanır.

|**char_filter_name**|**char_filter_type** <sup>1</sup>|**Açıklama ve Seçenekler**|  
|--------------------|---------------------------------|---------------------------|
|[html_strip](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/HTMLStripCharFilter.html)|(tür yalnızca seçenekler kullanılabilir olduğunda geçerlidir)  |HTML yapılarını sökme girişiminde bulunan bir Char filtresi.|  
|[eşleme](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/MappingCharFilter.html)|MappingCharFilter|Eşlemeler seçeneği ile tanımlanan eşlemeler uygulayan bir Char filtresi. Eşleşme doyumsuz (verili bir noktada en uzun model eşleştirme). Değiştirme işleminin boş dize olmasına izin verilir.  </br></br>**Seçenekler**  </br></br> eşlemeler (tür: dize dizisi)-Şu biçimdeki eşlemelerin listesi: "a =>b" ("a" karakterinin tüm oluşumları "b" karakteriyle değiştirilmiştir). Gereklidir.|  
|[pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceCharFilter.html)|PatternReplaceCharFilter|Giriş dizesindeki karakterlerin yerini alan bir Char filtresi. Korunacak karakter dizilerini belirlemek için normal bir ifade ve değiştirilecek karakterleri belirlemek için de bir değiştirme kalıbı kullanır. Örneğin, Input Text = "aa bb aa bb", model = "(AA) \\ \s + (BB)" değiştirme = "$ 1 # $2", Result = "aa # bb aa # bb".  </br></br>**Seçenekler**  </br></br>Model (tür: dize)-gerekli.  </br></br>değiştirme (tür: dize)-gerekli.|  

 <sup>1</sup> char filtre türlerine her zaman "#Microsoft. Azure. Search" ile birlikte "mappingcharfilter" adlı kod, gerçekten "#Microsoft. Azure. Search. mappingcharfilter olarak belirtilir". Tablonun genişliğini azaltmak için ön eki kaldırdık, ancak kodunuza dahil etmeyi unutmayın. Char_filter_type yalnızca özelleştirilebilecek filtreler için sağlandığını fark edin. Hiçbir seçenek yoksa html_strip olduğu gibi, ilişkili bir #Microsoft. Azure. Search türü yoktur.

<a name="tokenizers"></a>

## <a name="tokenizers"></a>Fark oluşturma denenmeden

Belirteç ayırıcı, sürekli metni bir tümceyi sözcüklere bölmek gibi bir belirteç dizisine böler. Aşağıdaki tabloda, Apache Lucene kullanılarak uygulanan simgeleyiciler, Lucene API belgelerine bağlanır.

|**tokenizer_name**|**tokenizer_type** <sup>1</sup>|**Açıklama ve Seçenekler**|  
|------------------|-------------------------------|---------------------------|  
|[klasik](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/ClassicTokenizer.html)|ClassicTokenizer|Çoğu Avrupa dilinde belgeyi işlemeye uygun, dilbilgisi tabanlı Simgeleştirici.  </br></br>**Seçenekler**  </br></br>maxTokenLength (tür: int)-en büyük belirteç uzunluğu. Varsayılan: 255, en fazla: 300. Uzunluk üst sınırından daha uzun belirteçler bölünür.|  
|[edgeNGram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html)|EdgeNGramTokenizer|Bir kenara ait girişi, verilen boyut (lar) ın n-gram sayısına göre simgeleştirir.  </br></br> **Seçenekler**  </br></br>minGram (tür: int)-varsayılan: 1, maksimum: 300.  </br></br>maxGram (tür: int)-varsayılan: 2, maksimum: 300. MinGram 'dan büyük olmalıdır.  </br></br>belirteçlerde saklanacak tokenChars (tür: dize dizisi)-karakter sınıfları. İzin verilen değerler: </br>"Letter", "digit", "Whitespace", "noktalama", "Symbol". Varsayılan olarak boş bir dizi-tüm karakterleri tutar. |  
|[keyword_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)|KeywordTokenizerV2|Tüm girişi tek bir belirteç olarak yayar.  </br></br>**Seçenekler**  </br></br>maxTokenLength (tür: int)-en büyük belirteç uzunluğu. Varsayılan: 256, en fazla: 300. Uzunluk üst sınırından daha uzun belirteçler bölünür.|  
|[harfin](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LetterTokenizer.html)|(tür yalnızca seçenekler kullanılabilir olduğunda geçerlidir)  |Metni mektupsız böler. 255 karakterden uzun belirteçler bölünür.|  
|[No](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseTokenizer.html)|(tür yalnızca seçenekler kullanılabilir olduğunda geçerlidir)  |Metni mektupsız böler ve küçük harfe dönüştürür. 255 karakterden uzun belirteçler bölünür.|  
| microsoft_language_tokenizer| MicrosoftLanguageTokenizer| Dile özgü kuralları kullanarak metni böler.  </br></br>**Seçenekler**  </br></br>maxTokenLength (tür: int)-en büyük belirteç uzunluğu, varsayılan: 255, en fazla: 300. Uzunluk üst sınırından daha uzun belirteçler bölünür. 300 karakterden daha uzun belirteçler, ilk olarak 300 uzunluklu belirteçlere bölünür ve bu belirteçlerden her biri maxTokenLength kümesine göre bölünür.  </br></br>ısearchtokenizer (tür: bool)-arama Tokenizer olarak kullanılıyorsa true olarak ayarlanır, dizin oluşturma Simgeleştirici olarak kullanılıyorsa false olarak ayarlanır. </br></br>Dil (tür: dize)-kullanılacak dil, varsayılan "İngilizce". İzin verilen değerler şunlardır: </br>"Bangla", "Bulgarca", "Katalanca", "Chinesebasiti", "Çinesetradi,", "Hırvatça", "Çekçe", "Danca", "Felemenkçe", "İngilizce", "Fransızca", "Almanca", "Yunanca", "Gucerat dili", "Hintçe", "İzlanda", "Kore dili", "İtalyanca", "Japonca" Malay dili "," Malayalam "," Marathi "," norwegianBokmaal "," Lehçe "," Portekizce "," portugueseBrazilian "," Pencap dili "," Rumence "," Rusça "," serbianCyrillic "," serbianLatin "," Slovence "," İspanyolca "," İsveççe "," Tamil dili "," Telugu "," Tay "," Ukraynaca "," Urduca "," Vietnam " |
| microsoft_language_stemming_tokenizer | MicrosoftLanguageStemmingTokenizer| Dile özgü kuralları kullanarak metni böler ve kelimeleri temel formlarına düşürür </br></br>**Seçenekler** </br></br>maxTokenLength (tür: int)-en büyük belirteç uzunluğu, varsayılan: 255, en fazla: 300. Uzunluk üst sınırından daha uzun belirteçler bölünür. 300 karakterden daha uzun belirteçler, ilk olarak 300 uzunluklu belirteçlere bölünür ve bu belirteçlerden her biri maxTokenLength kümesine göre bölünür. </br></br> ısearchtokenizer (tür: bool)-arama Tokenizer olarak kullanılıyorsa true olarak ayarlanır, dizin oluşturma Simgeleştirici olarak kullanılıyorsa false olarak ayarlanır. </br></br>Dil (tür: dize)-kullanılacak dil, varsayılan "İngilizce". İzin verilen değerler şunlardır: </br>"Arapça", "Bangla", "Bulgarca", "Katalanca", "Hırvatça", "Çekçe", "Danca", "Felemenkçe", "İngilizce", "Estonya", "Fince", "Fransızca", "Almanca", "Yunanca", "Gucerat dili", "İbranice", "Hintçe", "Macarca", "İzlanda", "Letonca", "Litvanca", "Malay", "Malayalam", "Marathi", "norwegianBokmaal", "Lehçe", "Portekizce", "portugueseBrazilian", "Pencap dili", "Rumence", "Rusça", "serbianCyrillic", "serbianLatin", "Slovakça", "Slovence", "İspanyolca", "İsveççe", "Tamil dili", "Telugu", "Türkçe", "Ukrayna", "Urduca" |
|[nGram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenizer.html)|NGramTokenizer|Girişi, verilen boyut (ler) i n-gram olarak simgeleştirir. </br></br>**Seçenekler** </br></br>minGram (tür: int)-varsayılan: 1, maksimum: 300. </br></br>maxGram (tür: int)-varsayılan: 2, maksimum: 300. MinGram 'dan büyük olmalıdır. </br></br>belirteçlerde saklanacak tokenChars (tür: dize dizisi)-karakter sınıfları. İzin verilen değerler: "Letter", "digit", "Whitespace", "noktalama", "Symbol". Varsayılan olarak boş bir dizi-tüm karakterleri tutar. |  
|[path_hierarchy_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/path/PathHierarchyTokenizer.html)|PathHierarchyTokenizerV2|Yol benzeri Hiyerarşiler için Simgeleştirici. **Seçenekler** </br></br>sınırlayıcı (tür: dize)-varsayılan: '/. </br></br>değiştirme (Type: String)-ayarlandıysa, sınırlayıcı karakteri değiştirir. Varsayılan değer sınırlayıcı değeriyle aynıdır. </br></br>maxTokenLength (tür: int)-en büyük belirteç uzunluğu. Varsayılan: 300, en fazla: 300. MaxTokenLength 'den uzun olan yollar yoksayıldı. </br></br>ters (Type: bool)-true Ise, belirteç ters sırada oluşturulur. Varsayılan: false. </br></br>Skip (tür: bool)-atlanacak başlangıç belirteçleri. Varsayılan değer, 0'dur.|  
|[kalıp](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternTokenizer.html)|PatternTokenizer|Bu belirteç ayırıcı, farklı belirteçler oluşturmak için Regex düzeniyle eşleştirmeyi kullanır. </br></br>**Seçenekler** </br></br> [model](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html) (tür: dize)-belirteç ayırıcılarını eşleştirmek için normal ifade deseninin. `\W+`Sözcük olmayan karakterlerle eşleşen varsayılan değer varsayılandır. </br></br>[bayraklar](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (tür: dize)-normal ifade bayrakları. Varsayılan değer boş bir dizedir. İzin verilen değerler: CANON_EQ, CASE_INSENSITIVE, AÇıKLAMALAR, DOTALL, DEĞIŞMEZ değer, çok SATıRLı, UNICODE_CASE, UNIX_LINES </br></br>Grup (tür: int)-belirteçlere Ayıklanacak grup. Varsayılan değer-1 ' dir (bölünmüş).|
|[standard_v2](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardTokenizer.html)|StandardTokenizerV2|[Unicode metin segmentleme kurallarından](https://unicode.org/reports/tr29/)sonraki metni keser. </br></br>**Seçenekler** </br></br>maxTokenLength (tür: int)-en büyük belirteç uzunluğu. Varsayılan: 255, en fazla: 300. Uzunluk üst sınırından daha uzun belirteçler bölünür.|  
|[uax_url_email](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/UAX29URLEmailTokenizer.html)|Uıaxurtamailtokenizer|URL 'leri ve e-postaları tek bir belirteç olarak simgeleştirir. </br></br>**Seçenekler** </br></br> maxTokenLength (tür: int)-en büyük belirteç uzunluğu. Varsayılan: 255, en fazla: 300. Uzunluk üst sınırından daha uzun belirteçler bölünür.|  
|[boşlu](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceTokenizer.html)|(tür yalnızca seçenekler kullanılabilir olduğunda geçerlidir) |Metni boşluk olarak böler. 255 karakterden uzun belirteçler bölünür.|  

 <sup>1</sup> Tokenizer türlerine her zaman "#Microsoft. Azure. Search" ile kod ön eki uygulanır ve "ClassicTokenizer" aslında "#Microsoft. Azure. Search. ClassicTokenizer" olarak belirtilir. Tablonun genişliğini azaltmak için ön eki kaldırdık, ancak kodunuza dahil etmeyi unutmayın. Tokenizer_type yalnızca özelleştirilebilecek simgeleyiciler için sağlandığını unutmayın. Hiçbir seçenek yoksa, Simgeleştirici harfle aynı olduğu gibi, ilişkili #Microsoft. Azure. Search türü yoktur.

<a name="TokenFilters"></a>

## <a name="token-filters"></a>Belirteç filtreleri

Belirteç filtresi, bir Simgeleştirici tarafından oluşturulan belirteçleri filtrelemek veya değiştirmek için kullanılır. Örneğin, tüm karakterleri küçük harfe dönüştüren küçük harfli bir filtre belirtebilirsiniz. Özel bir çözümleyici 'de birden çok belirteç filtresi kullanabilirsiniz. Belirteç filtreleri listelendikleri sırada çalışır. 

Aşağıdaki tabloda, Apache Lucene kullanılarak uygulanan belirteç filtreleri, Lucene API belgelerine bağlanır.

|**token_filter_name**|**token_filter_type** <sup>1</sup>|**Açıklama ve Seçenekler**|  
|-|-|-|  
|[arabic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)|(tür yalnızca seçenekler kullanılabilir olduğunda geçerlidir)  |Diklerinin normalleştirmek için Arapça normalleştirici uygulayan bir belirteç filtresi.|  
|[kesme işareti](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/tr/ApostropheFilter.html)|(tür yalnızca seçenekler kullanılabilir olduğunda geçerlidir)  |Kesme işareti (kesme işareti dahil) sonrasında tüm karakterleri şeritler. |  
|[yokıifolding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)|AsciiFoldingTokenFilter|İlk 127 ASCII karakterden ("temel Latin" Unicode bloğu) olmayan alfabetik, sayısal ve sembolik Unicode karakterlerini, varsa ASCII eşdeğerlerine dönüştürür.<br /><br /> **Seçenekler**<br /><br /> Preserveorijinali (tür: bool)-true Ise, özgün belirteç tutulur. Varsayılan değer false.|  
|[cjk_bigram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKBigramFilter.html)|CjkBigramTokenFilter|StandardTokenizer 'dan oluşturulan CJK terimlerin bıgram sayısını oluşturur.<br /><br /> **Seçenekler**<br /><br /> ignoreScripts (tür: dize dizisi)-yoksayılacak betikler. İzin verilen değerler şunlardır: "Han", "Hiragana", "Katakana", "Hangul". Varsayılan değer boş bir liste.<br /><br /> Outputunigram (Type: bool)-hem unigram hem de bigram için her zaman çıkış yapmak istiyorsanız true olarak ayarlayın. Varsayılan değer false.|  
|[cjk_width](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)|(tür yalnızca seçenekler kullanılabilir olduğunda geçerlidir)  |CJK genişlik farklarını normalleştirir. Tam genişlik ASCII varyantlarını eşdeğer temel Latin ve yarı genişlikte katakana çeşitlerine eşit kana ile katlayın. |  
|[klasik](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/ClassicFilter.html)|(tür yalnızca seçenekler kullanılabilir olduğunda geçerlidir)  |Ingilizce sahip olan ve kısaltmalardan oluşan noktaları ortadan kaldırır. |  
|[common_grams](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/commongrams/CommonGramsFilter.html)|CommonGramTokenFilter|Dizin oluşturma sırasında sık karşılaşılan koşullar için bigram oluşturun. Tek terimler hala dizine eklenmiş bir şekilde dizinlenir.<br /><br /> **Seçenekler**<br /><br /> commonWords (tür: dize dizisi)-ortak sözcükler kümesi. Varsayılan değer boş bir liste. Gereklidir.<br /><br /> IgnoreCase (tür: bool)-true Ise, eşleşme büyük/küçük harfe duyarsızdır. Varsayılan değer false.<br /><br /> queryMode (tür: bool)-çoklu gram oluşturur ve ardından ortak bir sözcük gelen ortak kelimeleri ve tek terimleri kaldırır. Varsayılan değer false.|  
|[dictionary_decompounder](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/compound/DictionaryCompoundWordTokenFilter.html)|DictionaryDecompounderTokenFilter|Birçok Germanic dilinde bulunan Bileşik sözcüklerin çıkarılması.<br /><br /> **Seçenekler**<br /><br /> Sözcük türü (tür: dize dizisi)-eşleştirilecek sözcüklerin listesi. Varsayılan değer boş bir liste. Gereklidir.<br /><br /> minWordSize (tür: int)-bu alma sonrasında yalnızca daha uzun sözcükler işlenir. Varsayılan değer 5 ' tir.<br /><br /> minSubwordSize (tür: int)-yalnızca bundan daha uzun alt sözcükler silinir. Varsayılan değer 2 ' dir.<br /><br /> maxSubwordSize (tür: int)-yalnızca bundan kısa olan alt sözcükler silinir. Varsayılan değer 15’tir.<br /><br /> onlyLongestMatch (Type: bool)-çıktıyı yalnızca en uzun eşleşen alt sözcük ekleyin. Varsayılan değer false.|  
|[edgeNGram_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenFilter.html)|EdgeNGramTokenFilterV2|Verilen boyut (ler) in önüne veya bir giriş belirtecinin arkasına kadar olan n-gram üretir.<br /><br /> **Seçenekler**<br /><br /> minGram (tür: int)-varsayılan: 1, maksimum: 300.<br /><br /> maxGram (tür: int)-varsayılan: 2, maksimum 300. MinGram 'dan büyük olmalıdır.<br /><br /> SIDE (tür: dize)-n-gram öğesinin hangi tarafına oluşturulacağını belirtir. İzin verilen değerler: "ön", "geri" |  
|[Eleme gerçekleştirilemedi](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)|ElisionTokenFilter|Kaldırmalar kaldırılır. Örneğin, "liavion" (düzlem) "Avion" (uçak) olarak dönüştürülür.<br /><br /> **Seçenekler**<br /><br /> Makaleler (tür: dize dizisi)-kaldırılacak makale kümesi. Varsayılan değer boş bir liste. Makale kümesi yoksa, varsayılan olarak tüm Fransızca makaleler kaldırılır.|  
|[german_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)|(tür yalnızca seçenekler kullanılabilir olduğunda geçerlidir)  |Almanya karakterlerini [German2 kar topu algoritmasındaki](https://snowballstem.org/algorithms/german2/stemmer.html) buluşsal yöntemler doğrultusunda normalleştirir.|  
|[hindi_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)|(tür yalnızca seçenekler kullanılabilir olduğunda geçerlidir)  |Yazım çeşitlerindeki bazı farklılıkları kaldırmak için Hintçe içindeki metni normalleştirir. |  
|[indic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)|Indicnormalizationtokenfilter|Hintçe dillerdeki metnin Unicode gösterimini normalleştirir.
|[tutmanıza](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeepWordFilter.html)|KeepTokenFilter|Yalnızca belirtilen sözcük listesinde bulunan metinle birlikte belirteçleri tutan belirteç filtresi.<br /><br /> **Seçenekler**<br /><br /> keepWords (tür: dize dizisi)-saklanacak sözcüklerin bir listesi. Varsayılan değer boş bir liste. Gereklidir.<br /><br /> keepWordsCase (tür: bool)-true Ise, küçük harf tüm sözcükleri önce. Varsayılan değer false.|  
|[keyword_marker](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordMarkerFilter.html)|KeywordMarkerTokenFilter|Terimleri anahtar sözcük olarak işaretler.<br /><br /> **Seçenekler**<br /><br /> anahtar sözcükler (tür: dize dizisi)-anahtar sözcük olarak işaretlemek için sözcüklerin listesi. Varsayılan değer boş bir liste. Gereklidir.<br /><br /> IgnoreCase (tür: bool)-true Ise, küçük harf tüm sözcükleri önce. Varsayılan değer false.|  
|[keyword_repeat](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordRepeatFilter.html)|(tür yalnızca seçenekler kullanılabilir olduğunda geçerlidir)  |Her gelen belirteci, anahtar sözcük olmayan bir defa anahtar sözcük ve bir kez bir kez yayar. |  
|[kstem](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/en/KStemFilter.html)|(tür yalnızca seçenekler kullanılabilir olduğunda geçerlidir)  |Ingilizce için yüksek performanslı bir kstem filtresi. |  
|[length](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/LengthFilter.html)|LengthTokenFilter|Çok uzun veya çok kısa olan kelimeleri kaldırır.<br /><br /> **Seçenekler**<br /><br /> Min (Type: int)-minimum sayı. Varsayılan: 0, maksimum: 300.<br /><br /> Max (Type: int)-maksimum sayı. Varsayılan: 300, en fazla: 300.|  
|[limit](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/LimitTokenCountFilter.html)|Microsoft. Azure. Search. Limitsimgefilter|Dizin oluşturma sırasında belirteçlerin sayısını sınırlar.<br /><br /> **Seçenekler**<br /><br /> maxTokenCount (tür: int)-üretilecek en fazla belirteç sayısı. Varsayılan değer 1'dir.<br /><br /> Tüketimealltokens (tür: bool)-maxTokenCount değerine ulaşılsa bile, girdiden tüm belirteçlerin tüketilmesi gerekip gerekmediğini belirtir. Varsayılan değer false.|  
|[No](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)|(tür yalnızca seçenekler kullanılabilir olduğunda geçerlidir)  |Belirteç metnini küçük harfe normalleştirir. |  
|[nGram_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html)|NGramTokenFilterV2|Verilen boyut (ler) i için n-gram üretir.<br /><br /> **Seçenekler**<br /><br /> minGram (tür: int)-varsayılan: 1, maksimum: 300.<br /><br /> maxGram (tür: int)-varsayılan: 2, maksimum 300. MinGram 'dan büyük olmalıdır.|  
|[pattern_capture](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternCaptureGroupTokenFilter.html)|PatternCaptureTokenFilter|, Bir veya daha fazla desendeki her yakalama grubu için bir tane olmak üzere birden çok belirteç göstermek için Java normal ifadeler kullanır.<br /><br /> **Seçenekler**<br /><br /> desenler (tür: dize dizisi)-her belirteçle eşleştirilecek desenlerin bir listesi. Gereklidir.<br /><br /> Preserveorijinali (Type: bool)-desenlerden biri eşlense bile özgün belirteci döndürmek için true olarak ayarlanır, varsayılan: true |  
|[pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceFilter.html)|PatternReplaceTokenFilter|Belirtilen değiştirme dizesiyle eşleşen tekrarlarla değiştirerek akıştaki her belirtece bir desenler uygulayan bir belirteç filtresi.<br /><br /> **Seçenekler**<br /><br /> Model (tür: dize)-gerekli.<br /><br /> değiştirme (tür: dize)-gerekli.|  
|[persian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)|(tür yalnızca seçenekler kullanılabilir olduğunda geçerlidir) |Farsça için normalleştirme uygular. |  
|[İl](https://lucene.apache.org/core/6_6_1/analyzers-phonetic/org/apache/lucene/analysis/phonetic/package-tree.html)|PhoneticTokenFilter|Fonetik eşleşmeler için belirteçler oluşturun.<br /><br /> **Seçenekler**<br /><br /> kodlayıcı (tür: dize)-kullanılacak fonetik kodlayıcı. İzin verilen değerler şunlardır: "metaphone", "Beltattaphone", "Soundex", "Iyileşmedisoundex", "caverphone1", "caverphone2", "Cologne", "nysıs", "koelnerPhonetik", "haasePhonetik", "Beidermoro". Varsayılan: "metaphone". Metaphone varsayılandır.<br /><br /> Daha fazla bilgi için bkz. [kodlayıcı](https://commons.apache.org/proper/commons-codec/archives/1.10/apidocs/org/apache/commons/codec/language/package-frame.html) .<br /><br /> Replace (tür: bool)-kodlanmış belirteçlerin orijinal belirteçleri değiştirmesini istiyorsanız true, eş anlamlılar olarak eklenmeleri gerekiyorsa false. Varsayılan değer true 'dur.|  
|[porter_stem](https://lucene.apache.org/core/6_6_1/analyzers-common/org/tartarus/snowball/ext/PorterStemmer.html)|(tür yalnızca seçenekler kullanılabilir olduğunda geçerlidir)  |Belirteç akışını, bağlantı [noktası sözcük algoritması algoritmasına](https://tartarus.org/~martin/PorterStemmer/)göre dönüştürür. |  
|[tersini](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/reverse/ReverseStringFilter.html)|(tür yalnızca seçenekler kullanılabilir olduğunda geçerlidir)  |Belirteç dizesini tersine çevirir. |  
|[scandinavian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)|(tür yalnızca seçenekler kullanılabilir olduğunda geçerlidir)  |Değiştirilebilir Scandinavian karakterlerinin normalleştirir. |  
|[scandinavian_folding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)|(tür yalnızca seçenekler kullanılabilir olduğunda geçerlidir)  |Scandinavian karakterleri åÅäæÄÆ->a ve öÖøØ->o. Ayrıca, yalnızca birinciden ayrıldığınızda, çift sesli aa, AE, Ao, OE ve Oo kullanımına göre de ayrım yapar. |  
|[kımi](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/shingle/ShingleFilter.html)|Shingtatokenfilter|Belirteç birleşimlerini tek bir belirteç olarak oluşturur.<br /><br /> **Seçenekler**<br /><br /> Maxshingtasize (tür: int)-varsayılan olarak 2 ' dir.<br /><br /> Minshingtasize (tür: int)-varsayılan olarak 2 ' dir.<br /><br /> Outputunigram (Type: bool)-true ise, çıkış akışı giriş belirteçlerini (unigram) ve kıingles 'yi içerir. Varsayılan değer true 'dur.<br /><br /> Outputunigramsıfnoshingles (Type: bool)-true Ise, kullanılabilir bir kınaller olmadığında bu süreler için Outputunigram = = false davranışını geçersiz kılın. Varsayılan değer false.<br /><br /> tokenSeparator (tür: String): bitişik belirteçlerin bir parça oluşturacak şekilde birleştirilirken kullanılacak dize. Varsayılan değer "" dir.<br /><br /> filterToken (tür: dize)-belirteç olmayan her bir konum için eklenecek dize. Varsayılan değer "_" dır.|  
|[kar topu](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/snowball/SnowballFilter.html)|SnowballTokenFilter|Kar topu belirteç filtresi.<br /><br /> **Seçenekler**<br /><br /> Dil (tür: dize)-Izin verilen değerler şunlardır: "Ermenice", "Bask", "Katalanca", "Danca", "Hollanda dili", "İngilizce", "Fince", "Fransızca", "Almanca", "german2", "Macarca", "İtalyanca", "KP", "lovins", "Norveççe", "," Rusça "," İspanyolca "," İsveç "," Türkçe "|  
|[sorani_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)|Soranınormalizationtokenfilter|Soranı metninin Unicode gösterimini normalleştirir.<br /><br /> **Seçenekler**<br /><br /> Yok.|  
|sözcük kökü ayırıcı|StemmerTokenFilter|Dile özgü sözcük kökü ayırıcı filtresi.<br /><br /> **Seçenekler**<br /><br /> Dil (Type: String)-Izin verilen değerler şunlardır: <br /> -   [rakam](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicStemmer.html)<br />-   [Ermenistan](https://snowballstem.org/algorithms/armenian/stemmer.html)<br />-   [Bask dili](https://snowballstem.org/algorithms/basque/stemmer.html)<br />-   [[](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/br/BrazilianStemmer.html)<br />-"Bulgarca"<br />-   [kata](https://snowballstem.org/algorithms/catalan/stemmer.html)<br />-   [Çekçe](https://portal.acm.org/citation.cfm?id=1598600)<br />-   [Danca](https://snowballstem.org/algorithms/danish/stemmer.html)<br />-   [Felemenkçe](https://snowballstem.org/algorithms/dutch/stemmer.html)<br />-   ["dutchKp"](https://snowballstem.org/algorithms/kraaij_pohlmann/stemmer.html)<br />-   [İngilizce](https://snowballstem.org/algorithms/porter/stemmer.html)<br />-   ["açık hayalet"](https://ciir.cs.umass.edu/pubfiles/ir-35.pdf)<br />-   ["minimalEnglish"](https://www.researchgate.net/publication/220433848_How_effective_is_suffixing)<br />-   ["possessiveEnglish"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/en/EnglishPossessiveFilter.html)<br />-   ["porter2"](https://snowballstem.org/algorithms/english/stemmer.html)<br />-   ["lovins"](https://snowballstem.org/algorithms/lovins/stemmer.html)<br />-   [Fince](https://snowballstem.org/algorithms/finnish/stemmer.html)<br />-"Lightfince"<br />-   [Fransız](https://snowballstem.org/algorithms/french/stemmer.html)<br />-   ["hafif"](https://dl.acm.org/citation.cfm?id=1141523)<br />-   ["Minimalfransız"](https://dl.acm.org/citation.cfm?id=318984)<br />-"Galicia"<br />-"En düşük düzeyde"<br />-   ['sı](https://snowballstem.org/algorithms/german/stemmer.html)<br />-   ["german2"](https://snowballstem.org/algorithms/german2/stemmer.html)<br />-   ["Lightalmanca"](https://dl.acm.org/citation.cfm?id=1141523)<br />-"Minimalalmanca"<br />-   [dalgalı](https://sais.se/mthprize/2007/ntais2007.pdf)<br />-"Hintçe"<br />-   [Macarca](https://snowballstem.org/algorithms/hungarian/stemmer.html)<br />-   ["lightHungarian"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   [Endonezya dili](https://eprints.illc.uva.nl/741/2/MoL-2003-03.text.pdf)<br />-   [landaca](https://snowballstem.org/otherapps/oregan/)<br />-   [İtalyanca](https://snowballstem.org/algorithms/italian/stemmer.html)<br />-   ["lightItalian"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   ["Sorani"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniStemmer.html)<br />-   [Le](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/lv/LatvianStemmer.html)<br />-   [İspanyolca](https://snowballstem.org/algorithms/norwegian/stemmer.html)<br />-   ["açık Norveç"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["en az Alnorveç"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   ["lightNynorsk"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimalNynorsk"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   [Portekizce](https://snowballstem.org/algorithms/portuguese/stemmer.html)<br />-   ["açık Portekizce"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   ["Minimalportekizcesi"](https://www.inf.ufrgs.br/~buriol/papers/Orengo_CLEF07.pdf)<br />-   ["portugueseRslp"](https://www.inf.ufrgs.br//~viviane/rslp/index.htm)<br />-   [Rumence](https://snowballstem.org/otherapps/romanian/)<br />-   [Rus](https://snowballstem.org/algorithms/russian/stemmer.html)<br />-   ["Işıklı"](https://doc.rero.ch/lm.php?url=1000%2C43%2C4%2C20091209094227-CA%2FDolamic_Ljiljana_-_Indexing_and_Searching_Strategies_for_the_Russian_20091209.pdf)<br />-   [Norveççe](https://snowballstem.org/algorithms/spanish/stemmer.html)<br />-   ["açık Ispanyolca"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   [İsveççe](https://snowballstem.org/algorithms/swedish/stemmer.html)<br />-"açık Isveççe"<br />-   [ına](https://snowballstem.org/algorithms/turkish/stemmer.html)|  
|[stemmer_override](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/StemmerOverrideFilter.html)|StemmerOverrideTokenFilter|Tüm sözlüklerde bulunan terimler anahtar sözcük olarak işaretlenir ve bu da zinciri yavaşlatımayı önler. Herhangi bir sözcük kökü olmadan önce yerleştirilmelidir.<br /><br /> **Seçenekler**<br /><br /> kurallar (tür: dize dizisi)-aşağıdaki biçimdeki "kelime => gövdesi" ve "ran => Run" biçimindeki kuralları etkinleştir. Varsayılan değer boş bir liste.  Gereklidir.|  
|[stopwords](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/StopFilter.html)|StopwordsTokenFilter|Bir belirteç akışından durdurma sözcüklerini kaldırır. Varsayılan olarak, filtre Ingilizce için önceden tanımlanmış bir Durdur kelime listesini kullanır.<br /><br /> **Seçenekler**<br /><br /> stopword (tür: dize dizisi)-stopword listesi. Bir stopwordsList belirtilmişse belirtilemiyor.<br /><br /> stopwordsList (tür: dize)-stopwords için önceden tanımlanmış bir liste. Stopword belirtilmişse belirtilemiyor. İzin verilen değerler şunlardır: "Arapça", "Ermenice", "Bask", "Brezilya", "Bulgarca", "Katalanca", "Çekçe", "Danca", "Felemenkçe", "İngilizce", "Fince", "Fransızca", "Galicia", "Almanca", "Yunanca", "Hintçe", "Macarca", "Endonezya", "İrlanda", "İtalyanca", "Letonya", "Norveççe", "Farsça", "Portekizce", "Rumence", "Rusça", "" Stopword belirtilmişse belirtilemiyor. <br /><br /> IgnoreCase (tür: bool)-Eğer true Ise, tüm kelimeler ilk olarak daha az kullanılır. Varsayılan değer false.<br /><br /> Uzaktan yazma (tür: bool)-Eğer true Ise, bir Durdur sözcükse, son arama terimini yoksayın. Varsayılan değer true 'dur.
|[eşanlamlı](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/synonym/SynonymFilter.html)|Eşanlamlı ve Mtokenfilter|Bir belirteç akışındaki tek veya birden çok sözcük eş anlamlısını eşleştirir.<br /><br /> **Seçenekler**<br /><br /> eş anlamlılar (tür: dize dizisi)-gerekli. Aşağıdaki iki biçimden birindeki eş anlamlıların listesi:<br /><br /> -inanılmaz, inanılmaz, fabulou => harika-=> sembolünün sol tarafındaki tüm terimler, sağ tarafındaki tüm koşullara göre değiştirilmiştir.<br /><br /> -inanılmaz, inanılmaz, fabulou, şaşırtıcı, eşdeğer sözcüklerin virgülle ayrılmış bir listesi. Bu listenin nasıl yorumlandığını değiştirmek için Genişlet seçeneğini ayarlayın.<br /><br /> IgnoreCase (tür: bool)-eşleştirme için büyük/küçük harf-katds girişi. Varsayılan değer false.<br /><br /> Expand (tür: bool)-true Ise, eş anlamlı (Eğer => gösterimi kullanılmayan) tüm sözcükler birbiriyle eşlenir. <br />Aşağıdaki liste: inanılmaz, inanılmaz, fabulou, harika bir değere sahip: inanılmaz, inanılmaz, fabulou, harika => inanılmaz, inanılmaz, fabulou, harika<br /><br />-False ise, şu liste: inanılmaz, inanılmaz, fabulou, harika bir şekilde eşdeğerdir: inanılmaz, inanılmaz, faonou, harika => inanılmaz.|  
|[kırpma](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/TrimFilter.html)|(tür yalnızca seçenekler kullanılabilir olduğunda geçerlidir)  |Belirteçlerden öndeki ve sondaki boşlukları kırpar. |  
|[kesilemedi](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/TruncateTokenFilter.html)|TruncateTokenFilter|Terimleri belirli bir uzunluğa kırpar.<br /><br /> **Seçenekler**<br /><br /> Uzunluk (tür: int)-varsayılan: 300, maksimum: 300. Gereklidir.|  
|[eşi](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/RemoveDuplicatesTokenFilter.html)|UniqueTokenFilter|Önceki belirteçle aynı metin ile belirteçleri filtreler.<br /><br /> **Seçenekler**<br /><br /> onlyOnSamePosition (tür: bool)-ayarlandıysa, yinelenenleri yalnızca aynı konumda kaldırın. Varsayılan değer true 'dur.|  
|[İngiliz](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)|(tür yalnızca seçenekler kullanılabilir olduğunda geçerlidir)  |Belirteç metnini büyük harfe normalleştirir. |  
|[word_delimiter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/WordDelimiterFilter.html)|Wordsınırlandıran Tertokenfilter|Sözcükleri alt sözcüklere böler ve alt sözcük grupları üzerinde isteğe bağlı dönüşümler gerçekleştirir.<br /><br /> **Seçenekler**<br /><br /> generateWordParts (tür: bool)-sözcük bölümlerinin oluşturulmasına neden olur, örneğin "AzureSearch", "Azure" "Search" olur. Varsayılan değer true 'dur.<br /><br /> generateNumberParts (tür: bool)-Sayı alt sözcüklerinin oluşturulmasına neden olur. Varsayılan değer true 'dur.<br /><br /> catenateWords (tür: bool)-sözcük bölümlerinin en fazla çalıştırılışlara eşit olmasına neden olur, örneğin "Azure-Search", "AzureSearch" olur. Varsayılan değer false.<br /><br /> catenateNumbers (tür: bool)-sayı bölümlerinin en fazla çalışmasına izin vermez; Örneğin, "1-2", "12" olur. Varsayılan değer false.<br /><br /> catenateAll (tür: bool)-tüm alt sözcük bölümlerinin eşit olmasına neden olur, örneğin "Azure-Search-1", "AzureSearch1" olur. Varsayılan değer false.<br /><br /> Sptoncasechange (tür: bool)-true Ise, sözcüğü caseChange üzerinde böler, örneğin "AzureSearch", "Azure" "Search" olur. Varsayılan değer true 'dur.<br /><br /> Preserveorijinal-özgün sözcüklerin korunmasına ve alt sözcük listesine eklenmesine neden olur. Varsayılan değer false.<br /><br /> Sptonnumerics (tür: bool)-true Ise, "Azure1Search" gibi "Azure" "1" "arama" olur. Varsayılan değer true 'dur.<br /><br /> Stemenglishiyine (tür: bool)-her alt sözcük için sondaki "'s" öğesinin kaldırılmasına neden olur. Varsayılan değer true 'dur.<br /><br /> protectedWords (tür: dize dizisi)-sınırlandırılyana korunacak belirteçler. Varsayılan değer boş bir liste.|  

 <sup>1</sup> belirteç filtresi türlerine her zaman, "ArabicNormalizationTokenFilter. Azure. Search. ArabicNormalizationTokenFilter" #Microsoft olarak belirtilebileceğinden "#Microsoft. Azure. Search" ile kod ön eki atanır.  Tablonun genişliğini azaltmak için ön eki kaldırdık, ancak kodunuza dahil etmeyi unutmayın.  

## <a name="see-also"></a>Ayrıca bkz.

- [Azure Bilişsel Arama REST API 'Leri](/rest/api/searchservice/)
- [Azure Bilişsel Arama Çözümleyicileri (örnekler)](search-analyzers.md#examples)
- [Dizin Oluşturma (REST)](/rest/api/searchservice/create-index)