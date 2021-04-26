---
title: Kısmi terimler, desenler ve özel karakterler
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama sorgu isteğindeki tüm veya kısmi terimlerle eşleştirmek için joker karakter, Regex ve önek sorguları kullanın. Özel karakterler içeren donanımdan eşleşme desenleri, tam sorgu sözdizimi ve özel çözümleyiciler kullanılarak çözülebilir.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/03/2020
ms.openlocfilehash: 2e2625fff802e71f797bf6970e763f2bf11c393e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104584185"
---
# <a name="partial-term-search-and-patterns-with-special-characters-hyphens-wildcard-regex-patterns"></a>Kısmi terim arama ve özel karakterlerle desenler (tireler, joker karakter, Regex, desenler)

*Kısmi bir terim arama* , dönem parçalarından oluşan sorguları ifade eder; burada, tam bir dönem yerine yalnızca başlangıç, orta veya terim (ön ek, ınfıx veya sonek sorguları olarak adlandırılır) olabilir. Kısmi bir terim arama, genellikle kısa çizgi, kısa çizgi veya sorgu dizesinin parçası olan eğik çizgi gibi özel karakterler içeren parçaların bir birleşimini içerebilir. Yaygın kullanım durumları, telefon numarası, URL, kod veya hecelenmiş Birleşik sözcüklerin parçalarını içerir.

Dizinin beklenen biçimde belirteçleri yoksa, özel karakterler içeren kısmi terim arama ve sorgu dizeleri sorunlu olabilir. Dizin oluşturma işleminin (varsayılan standart çözümleyicisini varsayarak) [sözcük analizi aşamasında](search-lucene-query-architecture.md#stage-2-lexical-analysis) , özel karakterler atılır, bileşik sözcükler bölünür ve boşluk silinir; tümü, hiçbir eşleşme bulunamadığında sorguların başarısız olmasına neden olabilir. Örneğin, (yani,,,,) gibi bir telefon numarası, `+1 (425) 703-6214` `"1"` `"425"` `"703"` `"6214"` `"3-62"` içerik gerçekten dizinde bulunmadığından bir sorguda gösterilmez. 

Çözüm, gerekirse boşluk ve özel karakterler dahil olmak üzere, dizin oluşturma sırasında bir çözümleyici çağırmak ve bu sayede, sorgu dizinizdeki boşlukları ve karakterleri dahil edebilmeniz için gereklidir. Benzer şekilde, daha küçük parçalara simgeleştirilmiş olmayan bir dizenin olması, "ile başlar" veya "ile biter" sorguları için model eşleştirmeyi sağlar, burada sağladığınız model, sözlü Analize göre dönüştürülmemiş bir terime göre değerlendirilemez. Bozulmadan bir dize için ek bir alan oluşturma ve tam terim belirteçleri veren bir içerik koruma Çözümleyicisi kullanma, her iki düzende eşleşen ve özel karakterler içeren sorgu dizeleriyle eşleşen çözüm olan çözümdür.

> [!TIP]
> Postman ve REST API 'Leri hakkında bilgi sahibiyseniz, bu makalede açıklanan kısmi terimleri ve özel karakterleri sorgulamak için [sorgu örnekleri koleksiyonunu indirin](https://github.com/Azure-Samples/azure-search-postman-samples/) .

## <a name="about-partial-term-search"></a>Kısmi terim arama hakkında

Azure Bilişsel Arama, dizinde simgeleştirilmiş terimlerin tamamını tarar ve joker karakter yer tutucusu işleçlerini ( `*` ve `?` ) eklemediğiniz veya sorguyu normal bir ifade olarak biçimlendirmediğiniz sürece kısmi bir dönemde eşleşme bulmayacaktır. Kısmi terimler şu teknikler kullanılarak belirtilir:

+ [Normal ifade sorguları](query-lucene-syntax.md#bkmk_regex) , Apache Lucene altında geçerli olan herhangi bir normal ifade olabilir. 

+ [Ön ek eşleştirmesi olan joker karakter işleçleri](query-simple-syntax.md#prefix-search) , bir dönemin başlangıcını, ardından gelen `*` veya `?` sonek işleçlerini ( `search=cap*` "Capyah 's sulu ön Inn" veya "Gacc sermaye" gibi) içeren genel olarak tanınan bir düzeni ifade eder. Önek eşleme, hem basit hem de tam Lucene sorgu sözdiziminde desteklenir.

+ [Indüzeltilme ve sonek eşleştirme Içeren joker karakter](query-lucene-syntax.md#bkmk_wildcard) , `*` ve `?` işleçlerini bir terimin başına ya da başlangıcına koyar ve normal ifade söz dizimini gerektirir (ifade eğik çizgi ile çevrelenmiş olan). Örneğin, sorgu dizesi (), `search=/.*numeric*./` sonek ve geri dönüş eşleşmeleri olarak "alfasayısal" ve "alfasayısal" sonuçları döndürür.

Kısmi terim veya kalıp arama için ve benzer arama gibi diğer birkaç sorgu formu, çözümleyiciler sorgu zamanında kullanılmaz. Bu sorgu formlarında, ayrıştırıcıların işleçler ve sınırlayıcılar varlığı tarafından algıladığı sorgu dizesi, sözcük temelli analiz olmadan altyapıya geçirilir. Bu sorgu formlarında, alanında belirtilen çözümleyici yok sayılır.

> [!NOTE]
> Kısmi bir sorgu dizesi, URL parçasındaki eğik çizgiler gibi karakterler içerdiğinde kaçış karakterleri eklemeniz gerekebilir. JSON 'da eğik çizgi, `/` ters eğik çizgiyle çıkış olur `\` . Bu nedenle, `search=/.*microsoft.com\/azure\/.*/` "Microsoft.com/Azure/" URL parçasına yönelik sözdizimidir.

## <a name="solving-partialpattern-search-problems"></a>Kısmi/kalıp arama sorunlarını çözme

Parçalar veya desenler ya da özel karakterler üzerinde arama yapmanız gerektiğinde, varsayılan çözümleyici 'yi daha basit simgeleştirme kuralları altında çalışan özel bir çözümleyici ile geçersiz kılabilirsiniz, bu da tüm dizeyi dizinde tutarak. Bir adım geri alınarak yaklaşım şuna benzer:

1. Dizenin bozulmadan bir sürümünü depolamak için bir alan tanımlayın (sorgu sırasında çözümlenmeyen ve çözümlenmemiş metni istediğiniz varsayılarak)
1. Belirteçleri doğru ayrıntı düzeyi düzeyine yayan çeşitli çözümleyiciler arasından değerlendirin ve seçin
1. Çözümleyici 'yi alana atama
1. Dizin oluşturma ve test etme

> [!TIP]
> Çözümleyiciler değerlendirmek, sık sık dizin yeniden oluşturma gerektiren yinelemeli bir işlemdir. Bu adımı Postman, [Dizin oluşturma](/rest/api/searchservice/create-index), [Dizin silme](/rest/api/searchservice/delete-index),[belge yükleme](/rest/api/searchservice/addupdate-or-delete-documents)ve [belge arama](/rest/api/searchservice/search-documents)için REST API 'leri kullanarak daha kolay hale getirebilirsiniz. Yükleme belgeleri için, istek gövdesi test etmek istediğiniz küçük bir temsili veri kümesi (örneğin, telefon numarası veya ürün kodu içeren bir alan) içermelidir. Aynı Postman koleksiyonundaki bu API 'lerle, bu adımları hızla geçebilirsiniz.

## <a name="1---create-a-dedicated-field"></a>1-adanmış alan oluşturma

Çözümleyiciler, koşulların bir dizinde nasıl simgeleştirilmiş olduğunu tespit ediyor. Çözümleyiciler alan temelinde atandıklarından, farklı senaryolar için iyileştirmek üzere dizininizdeki alanları oluşturabilirsiniz. Örneğin, ilk ve ikinci üzerinde normal tam metin aramasını desteklemek için "featureCode" ve "featureCodeRegex" tanımlayabilir. Her bir alana atanan çözümleyiciler, her bir alanın içeriğinin dizin içinde nasıl simgeleştirilmiş olduğunu belirtir.  

```json
{
  "name": "featureCode",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": null
},
{
  "name": "featureCodeRegex",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": "my_custom_analyzer"
},
```

<a name="set-an-analyzer"></a>

## <a name="2---set-an-analyzer"></a>2-bir çözümleyici ayarlama

Tam terim belirteçleri üreten bir çözümleyici seçerken, aşağıdaki çözümleyiciler yaygın seçimlerdir:

| Analyzer | Davranışlar |
|----------|-----------|
| [Dil Çözümleyicileri](index-add-language-analyzers.md) | Birleşik kelimeler veya dizelerde, sesli harfler ve fiil formlarında kısa çizgileri korur. Sorgu desenleri tire içeriyorsa, dil Çözümleyicisi kullanmak yeterli olabilir. |
| [sözcükle](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | Tüm alanın içeriği tek bir terim olarak simgeleştirilir. |
| [boşlu](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | Yalnızca boşluklardan ayrılır. Tireler veya diğer karakterleri içeren koşullar tek bir belirteç olarak değerlendirilir. |
| [özel çözümleyici](index-add-custom-analyzers.md) | Önerilen Özel bir çözümleyici oluşturma, hem belirteç ayırıcı hem de Token filtresi belirtmenize olanak tanır. Önceki çözümleyiciler olduğu gibi kullanılmalıdır. Özel çözümleyici, hangi simgeleyiciler ve belirteç filtrelerini kullanacağınızı seçmenizi sağlar. <br><br>Önerilen birleşim, [küçük harfli bir belirteç filtresiyle](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html) [Simgeleştirici anahtar kelimedir](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) . Yerleşik [anahtar sözcük Çözümleyicisi](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) , büyük/küçük harfli metinleri küçük harfe düşürmez ve bu da sorguların başarısız olmasına neden olabilir. Özel çözümleyici, size küçük harfli belirteç filtresi eklemek için bir mekanizma sağlar. |

Postman gibi bir Web API test aracı kullanıyorsanız, simgeleştirilmiş çıktıyı incelemek için [Test ÇÖZÜMLEYICISI Rest çağrısını](/rest/api/searchservice/test-analyzer) ekleyebilirsiniz.

Birlikte çalışmak için doldurulmuş bir dizininiz olmalıdır. Var olan bir dizin ve tire ya da kısmi terim içeren bir alan verildiğinde, hangi belirteçlerin yayınlandığını görmek için belirli koşullara göre çeşitli çözümleyiciler deneyebilirsiniz.  

1. İlk olarak, koşulların varsayılan olarak nasıl simgeleştirilmiş olduğunu görmek için standart çözümleyici 'yi denetleyin.

   ```json
   {
   "text": "SVP10-NOR-00",
   "analyzer": "standard"
   }
    ```

1. Metnin dizin içinde nasıl simgeleştirilmiş olduğunu görmek için yanıtı değerlendirin. Her bir terimin ne kadar küçük, kısa çizgilerden kaldırıldığı ve tek tek belirteçlere ayrılmış alt dizelerin olduğunu fark edin. Bu belgede yalnızca bu belirteçlerle eşleşen sorgular döndürülür. "10-veya" içeren bir sorgu başarısız olur.

    ```json
    {
        "tokens": [
            {
                "token": "svp10",
                "startOffset": 0,
                "endOffset": 5,
                "position": 0
            },
            {
                "token": "nor",
                "startOffset": 6,
                "endOffset": 9,
                "position": 1
            },
            {
                "token": "00",
                "startOffset": 10,
                "endOffset": 12,
                "position": 2
            }
        ]
    }
    ```
1. Şimdi `whitespace` veya çözümleyicisini kullanma isteğini değiştirin `keyword` :

    ```json
    {
    "text": "SVP10-NOR-00",
    "analyzer": "keyword"
    }
    ```

1. Artık yanıt, dizenin bir parçası olarak bir üst üste çizgilerden oluşan tek bir belirteçle oluşur. "10-or" gibi bir düzende veya kısmi bir dönemde aramanız gerekiyorsa, sorgu altyapısı artık eşleşme bulma temelini oluşturur.


    ```json
    {

        "tokens": [
            {
                "token": "SVP10-NOR-00",
                "startOffset": 0,
                "endOffset": 12,
                "position": 0
            }
        ]
    }
    ```
> [!Important]
> Sorgunun, sorgu ağacını oluştururken bir arama ifadesinde genellikle küçük harf koşullarına duyarlı olduğunu unutmayın. Dizin oluşturma sırasında büyük/küçük harf metin girişi olmayan bir çözümleyici kullanıyorsanız ve beklenen sonuçları alamıyorsanız, bunun nedeni bu olabilir. Çözüm, aşağıdaki "özel Çözümleyicileri kullanma" bölümünde açıklandığı gibi küçük harfli bir belirteç filtresi eklemektir.

## <a name="3---configure-an-analyzer"></a>3-çözümleyici yapılandırma
 
Çözümleyici 'yi değerlendiriyor veya belirli bir yapılandırma ile ileriye doğru hareket etmekle birlikte, alan tanımında Çözümleyicisi belirtmeniz ve bir yerleşik çözümleyici kullanmıyorsanız, büyük olasılıkla çözümleyici 'yi yapılandırmanız gerekir. Çözümleyiciler takas edildiğinde, genellikle dizini yeniden oluşturmanız gerekir (drop, yeniden oluştur ve yeniden yükle). 

### <a name="use-built-in-analyzers"></a>Yerleşik çözümleyiciler kullanın

Yerleşik çözümleyiciler `analyzer` , dizinde ek yapılandırma gerekmeden, bir alan tanımının bir özelliğinde ad ile belirtilebilir. Aşağıdaki örnek, `whitespace` bir alanı üzerinde çözümleyiciyi nasıl ayarlayabileceğinizi gösterir. 

Diğer senaryolar ve diğer yerleşik çözümleyiciler hakkında daha fazla bilgi edinmek için bkz. [yerleşik çözümleyiciler](./index-add-custom-analyzers.md#built-in-analyzers). 

```json
    {
      "name": "phoneNumber",
      "type": "Edm.String",
      "key": false,
      "retrievable": true,
      "searchable": true,
      "analyzer": "whitespace"
    }
```

### <a name="use-custom-analyzers"></a>Özel çözümleyiciler kullanma

[Özel bir çözümleyici](index-add-custom-analyzers.md)kullanıyorsanız, bu dosyayı, Kullanıcı tanımlı Simgeleştirici, belirteç filtresi, olası yapılandırma ayarları ile birlikte dizinde tanımlayın. Daha sonra, bir alan tanımında, tıpkı yerleşik çözümleyici gibi, ona başvurun.

Amaç bir bütün olarak simgeleştirmeye ayarlandığında, **simgeleştirme anahtar sözcüğünden** ve **küçük harfli belirteç filtresinden** oluşan özel bir çözümleyici önerilir.

+ Belirteç ayırıcı anahtar sözcüğü, bir alanın tüm içeriği için tek bir belirteç oluşturur.
+ Küçük harfli belirteç filtresi, büyük harfli harfleri küçük harfli metne dönüştürür. Sorgu Çözümleyicileri genellikle küçük harfli bir metin girişi yapar. Büyük küçük harf, girdileri simgeleştirilmiş koşullara göre sıralar.

Aşağıdaki örnek, Simgeleştirici ve küçük harfli bir belirteç filtresi anahtar sözcüğünü sağlayan özel bir çözümleyici gösterir.

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
],

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": []
```

> [!NOTE]
> `keyword_v2`Belirteç ayırıcı ve `lowercase` token filtresi, sistem tarafından bilinir ve varsayılan yapılandırmalarının kullanılması gerekir. bu nedenle, bunları önce tanımlamak zorunda kalmadan ada göre başvurabileceğiniz anlamına gelir.

## <a name="4---build-and-test"></a>4-derleme ve test

Çözümleyicinizi destekleyen çözümleyiciler ve alan tanımlarına sahip bir dizin tanımladıktan sonra, kısmi dize sorgularını test edebilmeniz için temsili dizeleri olan belgeleri yükleyin. 

Önceki bölümlerde Logic açıklanmaktadır. Bu bölüm, çözümünüzü sınarken çağırmanız gereken her API aracılığıyla yapmanız gereken adımları uygulayın. Daha önce belirtildiği gibi, Postman gibi etkileşimli bir Web testi aracı kullanırsanız, bu görevleri hızlıca izleyebilirsiniz.

+ [Dizini Sil](/rest/api/searchservice/delete-index) , yeniden oluşturabilmeniz için aynı ada sahip var olan bir dizini kaldırır.

+ [Create INDEX](/rest/api/searchservice/create-index) , çözümleyici tanımları ve çözümleyici belirtimine sahip alanlar da dahil olmak üzere arama hizmetinizde dizin yapısı oluşturur.

+ [Yükleme belgeleri](/rest/api/searchservice/addupdate-or-delete-documents) , dizininizdeki ve aranabilir içeriğin yanı sıra aynı yapıya sahip belgeleri içeri aktarır. Bu adımdan sonra, dizininiz sorgu veya test için hazırlanıyor.

+ [Test Çözümleyicisi](/rest/api/searchservice/test-analyzer) , [bir çözümleyici ayarla](#set-an-analyzer)' da tanıtılmıştır. Koşulların nasıl simgeleştirilmiş olduğunu anlamak için çeşitli çözümleyiciler kullanarak dizininizdeki dizelerin bazılarını test edin.

+ [Arama belgeleri](/rest/api/searchservice/search-documents) , joker ve normal ifadeler için [basit söz dizimi](query-simple-syntax.md) veya [tam Lucene sözdizimini](query-lucene-syntax.md) kullanarak bir sorgu isteğinin nasıl oluşturulacağını açıklar.

  "+ 1 (425) 703-6214" üzerinde bir eşleşme bulmak için "3-6214" sorgulaması gibi kısmi terim sorguları için, basit söz dizimini kullanabilirsiniz: `search=3-6214&queryType=simple` .

  "Num" veya "" alfasayısal "ile eşleşen bir eşleşme bulmak için sayısal olarak sorgu ve sonek sorguları için, tam Lucene söz dizimini ve bir normal ifadeyi kullanın: `search=/.*num.*/&queryType=full`

## <a name="tune-query-performance"></a>Sorgu performansını ayarlama

Keyword_v2 belirteç ayırıcı ve küçük harfli belirteç filtresini içeren önerilen yapılandırmayı uygularsanız, dizininizdeki mevcut belirteçler üzerinde ek belirteç filtresi işleme nedeniyle sorgu performansında azalmayı fark edebilirsiniz. 

Aşağıdaki örnek, öneki daha hızlı eşleşmekte olmak için bir [Edgengramtokenfilter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html) ekler. Şu karakterleri içeren 2-25 karakter birleşimlerinde ek belirteçler oluşturulur: (yalnızca MS, MSF, MSFT, MSFT/, MSFT/S, MSFT/SQ, MSFT/SQL). 

Imagine de, ek simgeleştirme daha büyük bir dizin ile sonuçlanır. Daha büyük dizine uyum sağlamak için yeterli kapasiteye sahipseniz, bu yaklaşım daha hızlı yanıt süresi daha iyi bir çözüm olabilir.

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
],

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase", "my_edgeNGram"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": [
  {
  "@odata.type":"#Microsoft.Azure.Search.EdgeNGramTokenFilterV2",
  "name":"my_edgeNGram",
  "minGram": 2,
  "maxGram": 25,
  "side": "front"
  }
]
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, çözümleyicilerin her ikisi de sorgu sorunlarına katkıda bulunma ve sorgu sorunlarını çözme işlemleri açıklanmaktadır. Bir sonraki adımda, dizin oluşturma ve sorgu işleme konusunda çözümleyici etkisi konusuna daha yakından göz atın. Özellikle, dizininiz için bir çözümleyici 'nin nasıl olduğunu tam olarak görebilmeniz için simgeleştirilmiş çıktıyı döndürmek için metin analizi API 'sini kullanmayı göz önünde bulundurun.

+ [Dil çözümleyicileri](search-language-support.md)
+ [Azure Bilişsel Arama metin işleme için çözümleyiciler](search-analyzers.md)
+ [Metin API 'sini çözümleme (REST)](/rest/api/searchservice/test-analyzer)
+ [Tam metin aramasının nasıl çalıştığı (sorgu mimarisi)](search-lucene-query-architecture.md)