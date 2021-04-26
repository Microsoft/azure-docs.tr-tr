---
title: Filtreler, modeller, sıralama için metin normalleştirme
titleSuffix: Azure Cognitive Search
description: Filtre uygulama, Faks ve sıralama sırasında katı anahtar sözcük eşleştirme davranışını özelleştirmek için bir dizindeki metin alanlarına normalonları belirtin.
author: IshanSrivastava
manager: jlembicz
ms.author: ishansri
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/23/2021
ms.openlocfilehash: 3e7a33d9213d7af44d2cfc50baa847534618f7e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104609986"
---
# <a name="text-normalization-for-case-insensitive-filtering-faceting-and-sorting"></a>Büyük/küçük harfe duyarsız filtreleme, Faks ve sıralama için metin normalleştirme

 > [!IMPORTANT]
 > Normalleştirici, **2020-06-30-preview REST API** aracılığıyla erişilebilen genel önizlemededir. Önizleme özellikleri, olduğu gibi, ek kullanım koşulları altında sunulur.

Azure Bilişsel Arama dizininden belge arama ve alma, sorgunun belge içeriğiyle eşleşmesini gerektirir. İçerik, eşleşme için belirteçler oluşturmak üzere analiz edilebilir `search` veya parametresi kullanıldığında durum, ve ile görüldüğü gibi katı anahtar sözcük eşleştirme için olduğu gibi kullanılabilir `$filter` `facets` `$orderby` . Bu tek veya-olmayan yaklaşımda çoğu senaryo ele alınmaktadır, ancak büyük/küçük harf, aksan kaldırma, asciifolding gibi basit ön işleme ve bu nedenle tüm analiz zincirinin tamamına geçmeksizin gereklidir.

Aşağıdaki örnekleri inceleyin:

+ `$filter=City eq 'Las Vegas'` yalnızca "Las Vegas" metnini içeren belgeleri döndürecek ve "Las Vegas" ve "Las Vegas" ile belgeleri dışarıda bırakacak ve kullanım örneği büyük küçük harfe bakılmaksızın tüm belgelere ihtiyaç duyacaktır.

+ `search=*&facet=City,count:5` aynı şehirde olmasına rağmen "Las Vegas", "LAS VEGAS" ve "Las Vegas" değerlerini ayrı değerler olarak döndürür.

+ `search=usa&$orderby=City` , "Las Vegas", "Seattle", "Las Vegas", "İstanbul", "İstanbul", "Las 

## <a name="normalizers"></a>Normalleyiciler

*Normalleştirici* , anahtar sözcük eşleştirme için önceden işleme metinden sorumlu olan arama altyapısının bir bileşenidir. Normaller, sorguyu simgeleştirme haricinde çözümleyiciler ile benzerdir. Normalleyiciler kullanılarak erişilebilecek bazı dönüşümler şunlardır:

+ Küçük harfe veya büyük harfe Dönüştür.
+ Ö veya ê gibi vurguları ve aksanları, "o" ve "e" ASCII denk karakterleriyle normalleştirin.
+ `-`Ve boşluk gibi karakterleri Kullanıcı tarafından belirtilen bir karaktere eşleyin.

Normalleyiciler, dizindeki metin alanlarında belirtilebilir ve hem dizin oluşturma hem de sorgu yürütme sırasında uygulanır.

## <a name="predefined-and-custom-normalizers"></a>Önceden tanımlanmış ve özel normalleyiciler 

Azure Bilişsel Arama, yaygın kullanım örnekleri için önceden tanımlanmış normalleştiriciler destekler, ancak gerekli şekilde özelleştirme özelliği ile birlikte.

| Kategori | Açıklama |
|----------|-------------|
| [Önceden tanımlanmış normalleyiciler](#predefined-normalizers) | Kullanıma hazır olarak sunulur ve herhangi bir yapılandırma olmadan kullanılabilir. |
|[Özel normalleyiciler](#add-custom-normalizers) | Gelişmiş senaryolar için. , Char ve Token filtrelerinden oluşan mevcut öğelerin bir birleşiminin Kullanıcı tanımlı yapılandırmasını gerektirir. <sup>1</sup>|

<sup>(1)</sup> normalleştiriciler her zaman tek bir belirteç oluşturduğundan, özel normalleyiciler simgeleyiciler belirtmez.

## <a name="how-to-specify-normalizers"></a>Normalleyiciler belirtme

Normalleyiciler `Edm.String` `Collection(Edm.String)` , en az bir `filterable` , `sortable` veya `facetable` özellikleri true olarak ayarlanmış metin alanları (ve) üzerinde alan başına belirtilebilir. Normalleştirici ayarlama isteğe bağlıdır ve `null` Varsayılan olarak ayarlanır. Kullanım kolaylığı için özel bir tane yapılandırmadan önce önceden tanımlanmış normalleştiricilerin değerlendirilmesi önerilir. Sonuçlar beklenmiyorsa, farklı bir normalleştirici deneyin.

Normalleyiciler yalnızca dizine yeni bir alan eklendiğinde belirtilebilir. Normalleştirme ihtiyaçlarını önünden değerlendirmek ve dizinleri bırakıp yeniden oluştururken geliştirme aşamalarını ilk aşamalarda normalleştiriciler atamak önerilir. Zaten oluşturulmuş bir alanda normalleyiciler belirtilemez.

1. [Dizinde](/rest/api/searchservice/create-index)bir alan tanımı oluştururken, **normalleştirici** özelliğini aşağıdakilerden birine ayarlayın: gibi [önceden tanımlanmış bir normalleştirici](#predefined-normalizers) `lowercase` veya bir özel normalleştirici (aynı dizin şemasında tanımlanır).  
 
   ```json
   "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "filterable": true,
      "analyzer": "en.microsoft",
      "normalizer": "lowercase"
      ...
    },
   ```

2. Özel normalleyiciler, önce dizinin **[normalde]** bölümünde tanımlanmalıdır ve ardından önceki adımda gösterildiği gibi alan tanımına atanır. Daha fazla bilgi için bkz. [Dizin oluşturma](/rest/api/searchservice/create-index) ve ayrıca [özel normalleyiciler ekleme](#add-custom-normalizers).


   ```json
   "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": null,
      "normalizer": "my_custom_normalizer"
    },
   ```

 
> [!NOTE]
> Varolan bir alanın normalleştirici boyutunu değiştirmek için, dizini tamamen yeniden oluşturmanız gerekecektir (tek tek alanları yeniden oluşturamazsınız).

, Dizinleri yeniden oluşturma işlemi maliyetli olan üretim dizinleri için iyi bir geçici çözüm olan, eskisiyle aynı ancak yeni normalleştirici ile özdeş olan yeni bir alan oluşturmak ve bunu eski bir yerde kullanmak. Yeni alanı birleştirmek için [güncelleştirme dizinini](/rest/api/searchservice/update-index) kullanın ve doldurmak Için [mergeorupload](/rest/api/searchservice/addupdate-or-delete-documents) kullanın. Daha sonra, planlı dizin hizmeti 'nin bir parçası olarak, eski alanları kaldırmak için dizini temizleyebilirsiniz.

## <a name="add-custom-normalizers"></a>Özel normalleyiciler Ekle

Özel normalleyiciler Dizin şeması içinde tanımlanır ve Field özelliği kullanılarak belirtilebilir. Özel normalleştirici tanımı bir ad, tür, bir veya daha fazla char filtresi ve belirteç filtresi içerir. Karakter filtreleri ve belirteç filtreleri, bir özel normalleştirici ve metin işlemeden sorumlu yapı taşlarıdır. Bu filtreler soldan sağa uygulanır.

 , `token_filter_name_1` Belirteç filtresinin adıdır ve `char_filter_name_1` `char_filter_name_2` karakter filtrelerinin adlarıdır (bkz. geçerli değerler için aşağıdaki [desteklenen belirteç filtreleri](#supported-token-filters) ve karakter filtreleri tabloları).

Normalleştirici tanımı, daha büyük dizinin bir parçasıdır. Dizinin geri kalanı hakkında bilgi için bkz. [Dizin oluşturma API 'si](/rest/api/searchservice/create-index) .

```
"normalizers":(optional)[
   {
      "name":"name of normalizer",
      "@odata.type":"#Microsoft.Azure.Search.CustomNormalizer",
      "charFilters":[
         "char_filter_name_1",
         "char_filter_name_2"
      ],
      "tokenFilters":[
         "token_filter_name_1
      ]
   }
],
"charFilters":(optional)[
   {
      "name":"char_filter_name_1",
      "@odata.type":"#char_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenFilters":(optional)[
   {
      "name":"token_filter_name_1",
      "@odata.type":"#token_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
]
```

Özel normalleyiciler, dizin oluşturma sırasında veya daha sonra mevcut bir tane güncelleştirilerek eklenebilir. Var olan bir dizine özel bir normalleştirici eklenmesi için, [güncelleştirme dizininde](/rest/api/searchservice/update-index) **allowındexını** bayrağının belirtilmesi gerekir ve dizinin birkaç saniye boyunca kullanılamaz hale gelmesine neden olur.

## <a name="normalizers-reference"></a>Normalleyiciler başvurusu

### <a name="predefined-normalizers"></a>Önceden tanımlanmış normalleyiciler
|**Ad**|**Açıklama ve Seçenekler**|  
|-|-|  
|Stand| Küçük harf, metnin ardından asciifolding izler.|  
|No| Karakterleri küçük harfe dönüştürür.|
|İngiliz| Karakterleri büyük harfe dönüştürür.|
|yokıifolding| Temel Latin Unicode bloğunda olmayan karakterleri, varsa ASCII eşdeğerine dönüştürür. Örneğin, à öğesini bir olarak değiştirme.|
|Eleme gerçekleştirilemedi| Belirteçlerin başından sonuna kadar ortadan kaldırır.|

### <a name="supported-char-filters"></a>Desteklenen karakter filtreleri
Karakter filtreleri hakkında daha fazla bilgi için, [char Filters Reference](index-add-custom-analyzers.md#CharFilter)bölümüne bakın.
+ [eşleme](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/MappingCharFilter.html)  
+ [pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceCharFilter.html)

### <a name="supported-token-filters"></a>Desteklenen belirteç filtreleri
Aşağıdaki liste, normalleştiriciler için desteklenen belirteç filtrelerini gösterir ve sözcük temelli Analize dahil olan genel belirteç filtrelerinin bir alt kümesidir. Filtreler hakkında daha fazla bilgi için, [belirteç filtreleri başvurusuna](index-add-custom-analyzers.md#TokenFilters)bakın.

+ [arabic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)
+ [yokıifolding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)
+ [cjk_width](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)  
+ [Eleme gerçekleştirilemedi](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)  
+ [german_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)
+ [hindi_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)  
+ [indic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)
+ [persian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)
+ [scandinavian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)  
+ [scandinavian_folding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)
+ [sorani_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)  
+ [No](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)
+ [İngiliz](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)


## <a name="custom-normalizer-example"></a>Özel normalleştirici örneği

Aşağıdaki örnekte, karşılık gelen char filtrelerini ve belirteç filtrelerini içeren özel bir normalleştirici tanımı gösterilmektedir. Karakter filtreleri ve belirteç filtreleri için özel seçenekler, adlandırılmış yapılar olarak ayrı olarak belirtilir ve ardından aşağıda gösterildiği gibi normalleştirici tanımında başvurulur.

* Dizin tanımının bölümünde özel bir normalleştirici "my_custom_normalizer" tanımlanmıştır `normalizers` .
* Normalleştirici iki Char filtresinden ve üç belirteç filtresinden oluşur: ıkarma, küçük harf ve özelleştirilmiş asciifolding filtresi "my_asciifolding".
* İkinci bir "remove_whitespace" tüm boşlukları kaldırdığında, "map_dash" ilk char filtresi tüm tireleri alt çizgi ile değiştirir.

```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false,
        },
        {
           "name":"city",
           "type":"Edm.String",
           "filterable": true,
           "facetable": true,
           "normalizer": "my_custom_normalizer"
        }
     ],
     "normalizers":[
        {
           "name":"my_custom_normalizer",
           "@odata.type":"#Microsoft.Azure.Search.CustomNormalizer",
           "charFilters":[
              "map_dash",
              "remove_whitespace"
           ],,
           "tokenFilters":[              
              "my_asciifolding",
              "elision",
              "lowercase",
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
     "tokenFilters":[
        {
           "name":"my_asciifolding",
           "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
           "preserveOriginal":true
        }
     ]
  }
```

## <a name="see-also"></a>Ayrıca bkz.
+ [Dil ve metin işleme Çözümleyicileri](search-analyzers.md)

+ [Belgelerde Arama REST API'si](/rest/api/searchservice/search-documents) 
