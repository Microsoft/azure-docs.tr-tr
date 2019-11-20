---
title: Tam Lucene sorgu söz dizimini kullan
titleSuffix: Azure Cognitive Search
description: Bir Azure Bilişsel Arama hizmetinde, benzer arama, yakınlık araması, terim artırma, normal ifade arama ve joker karakter aramaları için Lucene sorgu söz dizimi.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: Lucene query analyzer syntax
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 745be21c2a7a09a09fdbbfd57a305d09a4fac3ed
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793439"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>"Full" Lucene arama sözdizimini kullanın (Azure Bilişsel Arama Gelişmiş sorgular)

Azure Bilişsel Arama için sorgular oluştururken, özelleştirilmiş ve Gelişmiş sorgu tanımlarını formülleştirmek için varsayılan [basit sorgu ayrıştırıcısını](query-simple-syntax.md) [Azure bilişsel arama daha Expanleyici Lucene sorgu ayrıştırıcısıyla](query-lucene-syntax.md) değiştirebilirsiniz. 

Lucene ayrıştırıcısı alan kapsamlı sorgular, benzer ve ön ek joker karakter araması, yakınlık araması, terim artırma ve normal ifade arama gibi karmaşık sorgu yapılarını destekler. Ek güç ek işlem gereksinimleriyle birlikte gelir, böylece biraz daha uzun bir yürütme süresi beklemelisiniz. Bu makalede, tam sözdizimini kullanırken kullanılabilir sorgu işlemlerini gösteren örneklere ileredebilirsiniz.

> [!Note]
> Tam Lucene sorgu söz dizimi aracılığıyla etkinleştirilen özelleştirilmiş sorgu kurulumlarını birçoğu [metin çözümlenmez](search-lucene-query-architecture.md#stage-2-lexical-analysis)ve bu da, sözcük olarak ayırmayı veya kasaymayı düşünüyorsanız ortaya çıkmış olabilir. Sözcük temelli analiz yalnızca tüm koşullarda (bir terim sorgusu veya tümcecik sorgusu) gerçekleştirilir. Eksik koşullara sahip sorgu türleri (ön ek sorgusu, joker karakter sorgusu, Regex sorgusu, benzer sorgu) doğrudan sorgu ağacına eklenir, analiz aşaması atlanıyor. Tamamlanmamış sorgu koşullarında gerçekleştirilen tek dönüşüm küçük harfe göre yapılır. 
>

## <a name="formulate-requests-in-postman"></a>İstekleri Postman 'da formülleştirmek

Aşağıdaki örnekler, [New York OpenData girişiminin City](https://opendata.cityofnewyork.us/) tarafından sağlanan bir veri kümesine dayalı olarak bulunan işleri içeren bir NYC işleri arama dizininden yararlanır. Bu verilerin geçerli veya tamamlanmış olarak kabul edilmemelidir. Dizin, Microsoft tarafından sağlanmış bir korumalı alan hizmetidir ve bu sorguları denemek için bir Azure aboneliğine veya Azure Bilişsel Arama ihtiyaç duymayın.

Ne yapmanız gerekir Postman veya GET 'te HTTP isteği vermek için eşdeğer bir araçtır. Daha fazla bilgi için bkz. [rest Istemcilerinde araştırma](search-get-started-postman.md).

### <a name="set-the-request-header"></a>İstek üst bilgisini ayarlama

1. İstek üstbilgisinde, **Içerik türünü** `application/json`olarak ayarlayın.

2. Bir **API anahtarı**ekleyin ve bu dizeye ayarlayın: `252044BE3886FE4A8E3BAA4F595114BB`. Bu, NYC Işleri dizinini barındıran korumalı alan arama hizmeti için bir sorgu anahtarıdır.

İstek üst bilgisini belirttikten sonra, bu makaledeki tüm sorgular için onu yeniden kullanabilirsiniz ve yalnızca **Search =** dizesini takas edebilirsiniz. 

  ![Postman isteği üst bilgisi](media/search-query-lucene-examples/postman-header.png)

### <a name="set-the-request-url"></a>İstek URL 'sini ayarla

İstek, Azure Bilişsel Arama uç noktası ve arama dizesini içeren bir URL ile eşleştirilmiş bir GET komutu.

  ![Postman isteği üst bilgisi](media/search-query-lucene-examples/postman-basic-url-request-elements.png)

URL kompozisyonu aşağıdaki öğelere sahiptir:

+ **`https://azs-playground.search.windows.net/`** , Azure bilişsel arama geliştirme ekibi tarafından tutulan bir korumalı alan arama hizmetidir. 
+ **`indexes/nycjobs/`** , bu hizmetin dizinler koleksiyonundaki NYC işleri dizinidir. İstekte hem hizmet adı hem de dizin gereklidir.
+ **`docs`** , tüm aranabilir içeriği içeren belge koleksiyonudur. İstek üstbilgisinde belirtilen sorgu api anahtarı yalnızca belge koleksiyonunu hedefleyen okuma işlemlerinde kullanılabilir.
+ **`api-version=2019-05-06`** , her istekte gerekli bir parametre olan api sürümünü ayarlar.
+ **`search=*`** , ilk sorgu null olduğunda ilk 50 sonucunu döndüren sorgu dizesidir (varsayılan olarak).

## <a name="send-your-first-query"></a>İlk sorgunuzu gönderin

Doğrulama adımı olarak, aşağıdaki isteği al öğesine yapıştırın ve **Gönder**' e tıklayın. Sonuçlar ayrıntılı JSON belgeleri olarak döndürülür. Tüm belgeler döndürülür, bu, tüm alanları ve tüm değerleri görmenizi sağlar.

Bu URL 'YI bir REST istemcisine doğrulama adımı olarak yapıştırın ve belge yapısını görüntüleyin.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=*
  ```

Sorgu dizesi **`search=*`** , null veya boş aramaya bir belirtilmemiş arama eşdeğerdir. Bu, yapabileceğiniz en basit arama.

İsteğe bağlı olarak, arama ölçütleriyle eşleşen belgelerin sayısını döndürmek için URL 'ye **`$count=true`** ekleyebilirsiniz. Boş bir arama dizesinde bu, dizindeki tüm belgelerdir (NYC Işleri durumunda 2800 hakkında).

## <a name="how-to-invoke-full-lucene-parsing"></a>Tam Lucene ayrıştırma çağırma

Tam sorgu söz dizimini çağırmak için **queryType = Full** ekleyerek varsayılan basit sorgu söz dizimini geçersiz kılar. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&search=*
```

Bu makaledeki örneklerin hepsi, tam sözdiziminin Lucene sorgu ayrıştırıcısı tarafından işlendiğini belirten **queryType = Full** Search parametresini belirtir. 

## <a name="example-1-query-scoped-to-a-list-of-fields"></a>Örnek 1: bir alan listesi kapsamındaki sorgu

Bu ilk örnek, Lucene 'e özgü değildir, ancak ilk temel sorgu kavramını tanıtmaya neden olur: alan kapsamı. Bu örnek, tüm sorguyu ve yanıtı yalnızca birkaç belirli alana kapsamlar. Araç Postman veya arama Gezgini olduğunda okunabilir bir JSON yanıtının nasıl ayarlanacağını bilmek önemlidir. 

Breçekimi için sorgu yalnızca *business_title* alanını hedefler ve yalnızca iş başlıklarının döndürüldüğünü belirtir. **Searchfields** parametresi sorgu yürütmeyi yalnızca business_title alanıyla **kısıtlar ve yanıta** hangi alanların ekleneceğini belirtir.

### <a name="partial-query-string"></a>Kısmi sorgu dizesi

```http
&search=*&searchFields=business_title&$select=business_title
```

Virgülle ayrılmış bir listede birden çok alan ile aynı sorgu aşağıda verilmiştir.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

Virgüllerden sonraki boşluklar isteğe bağlıdır.

> [!Tip]
> Uygulama kodunuzda REST API kullanırken, `$select` ve `searchFields`gibi URL kodlaması parametrelerini unutmayın.

### <a name="full-url"></a>Tam URL

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&search=*&searchFields=business_title&$select=business_title
```

Bu sorgu için yanıt aşağıdaki ekran görüntüsüne benzer görünmelidir.

  ![Postman örnek yanıtı](media/search-query-lucene-examples/postman-sample-results.png)

Yanıtta arama puanı olduğunu fark etmiş olabilirsiniz. Arama tam metin araması olmadığı veya hiçbir ölçüt uygulanmadığı için, tek bir derecelendirme olmadığında 1 Tekdüzen puanları oluşur. Ölçüt olmadan null arama için, satırlar rastgele sırada geri gelir. Gerçek arama ölçütlerini dahil ettiğinizde, arama puanları anlamlı değerlere geliştikçe görüntülenir.

## <a name="example-2-fielded-search"></a>Örnek 2: Alanlu arama

Full Lucene sözdizimi, tek tek arama ifadelerinin belirli bir alana kapsamını destekler. Bu örnek, iş unvanlarını arar, ancak Junior değildir.

### <a name="partial-query-string"></a>Kısmi sorgu dizesi

```http
$select=business_title&search=business_title:(senior NOT junior)
```

Birden çok alan ile aynı sorgu aşağıda verilmiştir.

```http
$select=business_title, posting_type&search=business_title:(senior NOT junior) AND posting_type:external
```

### <a name="full-url"></a>Tam URL

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&$select=business_title&search=business_title:(senior NOT junior)
```

  ![Postman örnek yanıtı](media/search-query-lucene-examples/intrafieldfilter.png)

**FieldName: SearchExpression** söz dizimi ile bir ara değer arama işlemi tanımlayabilirsiniz. burada, arama ifadesi tek bir sözcük veya tümcecik ya da parantez içinde daha karmaşık bir ifade olabilir ve isteğe bağlı olarak Boolean işleçleriyle. Bazı örnekler şunlardır:

- `business_title:(senior NOT junior)`
- `state:("New York" OR "New Jersey")`
- `business_title:(senior NOT junior) AND posting_type:external`

Her iki dizenin de tek bir varlık olarak değerlendirilmesini istiyorsanız, bu örnekte `state` alanında iki ayrı konum aramak için birden çok dizeyi tırnak işareti içine aldığınızdan emin olun. Ayrıca, ve ve gibi gördüğünüz gibi işlecin büyük harfli olduğundan emin olun.

**FieldName: searchExpression** 'da belirtilen alan aranabilir bir alan olmalıdır. Dizin özniteliklerinin alan tanımlarında nasıl kullanıldığına ilişkin ayrıntılar için bkz. [Dizin oluşturma (Azure Bilişsel Arama REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) .

> [!NOTE]
> Yukarıdaki örnekte, sorgunun her bölümü açıkça belirtilmiş bir alan adına sahip olduğundan `searchFields` parametresini kullanmak zorunda değilsiniz. Ancak, bazı parçaların belirli bir alan kapsamında bulunduğu bir sorgu çalıştırmak istiyorsanız `searchFields` parametresini kullanmaya devam edebilirsiniz ve REST çeşitli alanlara uygulanabilir. Örneğin, sorgu `search=business_title:(senior NOT junior) AND external&searchFields=posting_type` yalnızca `business_title` alanı ile `senior NOT junior` eşleşirken, `posting_type` alanla "External" ile eşleşecekti. **FieldName: searchExpression** 'da belirtilen alan adı her zaman `searchFields` parametresine göre önceliklidir. Bu örnekte, `searchFields` parametresine `business_title` eklemesi gerekmez.

## <a name="example-3-fuzzy-search"></a>Örnek 3: benzer arama

Tam Lucene sözdizimi, benzer bir yapıya sahip koşullarla eşleşen belirsiz aramayı da destekler. Benzer bir arama yapmak için, tek bir sözcüğün sonundaki tilde `~` sembolünü, isteğe bağlı bir parametre, 0 ile 2 arasında, düzenleme uzaklığını belirten bir değer olacak şekilde ekleyin. Örneğin, `blue~` veya `blue~1` mavi, maves ve tutkalla döndürecek.

### <a name="partial-query-string"></a>Kısmi sorgu dizesi

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~
```

Tümcecikler doğrudan desteklenmez, ancak bir tümceciğin bileşen bölümlerinde benzer bir eşleşme belirtebilirsiniz.

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~ AND comm~ 
```


### <a name="full-url"></a>Tam URL

Bu sorgu, "ilişkilendir" terimiyle (kasıtlı olarak yanlış yazılmış) işleri arar:

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:asosiate~
```
  ![Benzer arama yanıtı](media/search-query-lucene-examples/fuzzysearch.png)


> [!Note]
> Benzer sorgular [çözümlenmez](search-lucene-query-architecture.md#stage-2-lexical-analysis). Eksik koşullara sahip sorgu türleri (ön ek sorgusu, joker karakter sorgusu, Regex sorgusu, benzer sorgu) doğrudan sorgu ağacına eklenir, analiz aşaması atlanıyor. Tamamlanmamış sorgu koşullarında gerçekleştirilen tek dönüşüm küçük harfe göre yapılır.
>

## <a name="example-4-proximity-search"></a>Örnek 4: yakınlık araması
Yakınlık aramaları, bir belgedeki birbirini yakın terimleri bulmak için kullanılır. Bir ifadenin sonuna, ardından yakınlık sınırını oluşturan sözcüklerin sayısını içeren bir tilde "~" simgesi ekleyin. Örneğin, "otel Havaalanı" ~ 5, bir belgede her birinin 5 sözcüklerinde otel ve Havaalanı terimlerini bulur.

### <a name="partial-query-string"></a>Kısmi sorgu dizesi

```http
searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```

### <a name="full-url"></a>Tam URL

Bu sorguda, "kıdemli analist" terimine sahip işler için birden fazla sözcükten ayrıldığından:

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```
  ![Yakınlık sorgusu](media/search-query-lucene-examples/proximity-before.png)

"Baş analist" terimi arasındaki sözcükleri kaldırmayı yeniden deneyin. Bu sorgu için, önceki sorgu için 10 ' dan farklı olarak 8 belge döndürüldüğünden emin olun.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~0
```

## <a name="example-5-term-boosting"></a>Örnek 5: terim artırma
Terim artırma, bir belgeyi, süresi içermeyen belgelere göre, daha yüksek bir dönem içeriyorsa bir belgenin derecelendirdiğini ifade eder. Bir dönemi artırmak için, aradığınız terimin sonundaki "^" (bir sayı) simgesini bir artırma faktörü (bir sayı) ile birlikte kullanın. 

### <a name="full-urls"></a>Tam URL 'Ler

Bu "önce" sorgusunda, *bilgisayar analistine* sahip işleri arayın ve hem *bilgisayar* hem de *analist*ile bir sonuç olmadığını, ancak *bilgisayar* işlerinin sonuçların en üstünde olduğunu unutmayın.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst
```
  ![Önce dönem artırma](media/search-query-lucene-examples/termboostingbefore.png)

"Sonra" sorgusunda, bu kez, her iki kelime de yoksa, aramayı tekrarlayarak *bilgisayar* terimi üzerinde *analist* terimini elde edin. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst%5e2
```
Yukarıdaki sorgunun daha okunabilir bir sürümü `search=business_title:computer analyst^2`. `^2` bir sorgu için, daha zor olan `%5E2`olarak kodlanır.

  ![Sonrasında dönem artırma](media/search-query-lucene-examples/termboostingafter.png)

Kullanım süresi, Puanlama profillerindeki Puanlama profillerinden farklıdır ve belirli koşullar yerine belirli alanları artırır. Aşağıdaki örnek, farkları göstermeye yardımcı olur.

Musicstoreındex örneğinde **tarz** gibi belirli bir alanda eşleşen bir Puanlama profili düşünün. Belirli arama terimlerini diğerlerinden daha fazla artırmak için kullanım süresi kullanılabilir. Örneğin, "Rock ^ 2 elektronik", **tarzdaki** arama terimlerini, dizinde bulunan diğer aranabilir alanlardan daha yüksek olan belgeyi artırır. Ayrıca, "Rock" arama terimini içeren belgeler, yükseltme değeri (2) sonucunda "elektronik" diğer arama teriminden daha yüksek olarak derecelendirilir.

Faktör düzeyi ayarlanırken, yükseltme faktörü arttıkça, bu terim diğer arama koşullarına göre daha uygun olacaktır. Varsayılan olarak, Boost faktörü 1 ' dir. Boost faktörü pozitif olmalıdır, ancak 1 ' den az olabilir (örneğin, 0,2).


## <a name="example-6-regex"></a>Örnek 6: Regex

Normal ifade araması, [RegExp sınıfında](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html)belgelendiği gibi eğik çizgi "/" arasındaki içeriğe dayalı bir eşleşme bulur.

### <a name="partial-query-string"></a>Kısmi sorgu dizesi

```http
searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

### <a name="full-url"></a>Tam URL

Bu sorguda, kıdemli veya Junior: `search=business_title:/(Sen|Jun)ior/`teriminin bulunduğu işleri arayın.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

  ![Regex sorgusu](media/search-query-lucene-examples/regex.png)

> [!Note]
> Regex sorguları [çözümlenmez](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis). Tamamlanmamış sorgu koşullarında gerçekleştirilen tek dönüşüm küçük harfe göre yapılır.
>

## <a name="example-7-wildcard-search"></a>Örnek 7: joker karakter arama
Birden çok (\*) veya tek (?) karakterli joker karakter aramaları için genellikle tanınan sözdizimini kullanabilirsiniz. Lucene sorgu ayrıştırıcısının, bu sembollerin tek bir terim ve tümcecik değil, kullanımını desteklediği unutulmamalıdır.

### <a name="partial-query-string"></a>Kısmi sorgu dizesi

```http
searchFields=business_title&$select=business_title&search=business_title:prog*
```

### <a name="full-url"></a>Tam URL

Bu sorguda, programlama ve programlayıcı koşullarına sahip iş başlıklarını içeren ' prog ' önekini içeren işleri arayın. \* Veya? kullanamazsınız bir aramanın ilk karakteri olarak sembol.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:prog*
```
  ![Joker karakter sorgusu](media/search-query-lucene-examples/wildcard.png)

> [!Note]
> Joker karakter sorguları [çözümlenmez](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis). Tamamlanmamış sorgu koşullarında gerçekleştirilen tek dönüşüm küçük harfe göre yapılır.
>

## <a name="next-steps"></a>Sonraki adımlar
Kodunuzda Lucene sorgu ayrıştırıcısını belirtmeyi deneyin. Aşağıdaki bağlantılarda hem .NET hem de REST API için arama sorgularının nasıl ayarlanacağı açıklanmaktadır. Bağlantılar varsayılan basit söz dizimini kullanır, bu nedenle, bu makaleden öğrendiklerinizi belirtmek için **queryType**'i belirtmeniz gerekir.

* [.NET SDK kullanarak dizininizi sorgulama](search-query-dotnet.md)
* [REST API kullanarak dizininizi sorgulayın](search-create-index-rest-api.md)

Ek sözdizimi başvurusu, sorgu mimarisi ve örnekler aşağıdaki bağlantılarda bulunabilir:

+ [Basit sözdizimi sorgu örnekleri](search-query-simple-examples.md)
+ [Tam metin aramasının Azure 'da nasıl çalıştığı Bilişsel Arama](search-lucene-query-architecture.md)
+ [Basit sorgu söz dizimi](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Tam Lucene sorgu söz dizimi](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)