---
title: Lucene sorgu söz dizimi
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama joker karakter, belirsiz arama, RegEx ve diğer gelişmiş sorgu yapıları için kullanılan tam Lucene sorgu söz dizimi için başvuru.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 0bb8474b30c05e21a62ded1fa2cb8a6df8e4e321
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112178"
---
# <a name="lucene-query-syntax-in-azure-cognitive-search"></a>Azure Bilişsel Arama Lucene sorgu söz dizimi

Özelleştirilmiş sorgu formları için zengin [Lucene sorgu ayrıştırıcı](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) sözdizimine göre Azure bilişsel arama karşı sorgular yazabilirsiniz: joker karakter, benzer arama, yakınlık araması, normal ifadeler birkaç örnektir. Lucene sorgu ayrıştırıcısı sözdiziminin büyük bir bölümü [azure bilişsel arama 'da](search-lucene-query-architecture.md), Azure Bilişsel Arama tarafından `$filter` ifadelerle oluşturulan *Aralık aramaları* dışında bir şekilde uygulanır. 

## <a name="how-to-invoke-full-parsing"></a>Tam ayrıştırma çağırma

Hangi ayrıştırıcısının kullanılacağını belirtmek için `queryType` Search parametresini ayarlayın. Geçerli değerler, varsayılan olarak `simple` ve Lucene için `full` `simple|full`içerir. 

<a name="bkmk_example"></a> 

### <a name="example-showing-full-syntax"></a>Tam sözdizimini gösteren örnek

Aşağıdaki örnek, Lucene sorgu söz dizimini kullanarak dizindeki belgeleri bulur, `queryType=full` parametresinde yok edilecek. Bu sorgu, Kategori alanının "bütçe" terimini ve "son randevu" ifadesini içeren tüm aranabilir alanları içermesi halinde oteller döndürür. "Son randevu" ifadesini içeren belgeler, artırma değeri (3) sonucu olarak daha yüksektir.  

`searchMode=all` parametresi bu örnekle ilgilidir. Her operatör sorgu üzerinde olduğunda, ölçütlerin *tümünün* eşleştiğinden emin olmak için genellikle `searchMode=all` ayarlamanız gerekir.

```
GET /indexes/hotels/docs?search=category:budget AND \"recently renovated\"^3&searchMode=all&api-version=2019-05-06&querytype=full
```

 Alternatif olarak, POST 'u kullanın:  

```
POST /indexes/hotels/docs/search?api-version=2019-05-06
{
  "search": "category:budget AND \"recently renovated\"^3",
  "queryType": "full",
  "searchMode": "all"
}
```

Daha fazla örnek için bkz. [Lucene sorgu söz dizimi örnekleri Azure bilişsel arama 'de sorgu oluşturma](search-query-lucene-examples.md). Sorgu parametrelerinin tam olarak belirlenmesi hakkında daha fazla bilgi için bkz. [arama belgeleri &#40;Azure bilişsel arama REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

> [!NOTE]  
>  Azure Bilişsel Arama [basit sorgu söz dizimini](query-simple-syntax.md)da destekler, basit ve güçlü bir sorgu dili, doğrudan anahtar sözcük araması için kullanılabilir.  

##  <a name="bkmk_syntax"></a>Sözdizimi temelleri  
 Aşağıdaki sözdizimi temelleri, Lucene sözdizimini kullanan tüm sorgular için geçerlidir.  

### <a name="operator-evaluation-in-context"></a>Bağlamda işleç değerlendirmesi

Yerleştirme, bir simgenin bir operatör ya da bir dizedeki yalnızca başka bir karakter olarak yorumlanıp yorumlanmadığını belirler.

Örneğin, Lucene Full sözdiziminde, hem belirsiz arama hem de yakınlık araması için tilde (~) kullanılır. Tırnak içine alınmış bir tümcecikden sonra yerleştirildiğinde,, yakınlık aramasını çağırır. Bir terimin sonuna yerleştirildiğinde, belirsiz aramayı çağırır.

"İş ~ analist" gibi bir dönem içinde, karakter bir işleç olarak değerlendirilmez. Bu durumda, sorgunun bir terim veya tümcecik sorgusu olduğu varsayılırsa, [sözcük temelli Analize](search-lucene-query-architecture.md#stage-2-lexical-analysis) sahip [tam metin araması](search-lucene-query-architecture.md) ,, ve "Business ~ analist" TERIMINI iki: iş veya analist olarak keser.

Yukarıdaki örnek, tilde (~), ancak aynı prensibi her operatör için geçerlidir.

### <a name="escaping-special-characters"></a>Özel karakterleri kaçış

 Arama metninin bir parçası olarak kullanılması için özel karakterlerin kaçışılması gerekir. Üzerlerine ters eğik çizgiyle (\\) ekleyerek bunları silebilirsiniz. Kaçışılması gereken özel karakterler şunlardır:  
`+ - && || ! ( ) { } [ ] ^ " ~ * ? : \ /`  

 Örneğin, bir joker karakteri atlamak için \\\*kullanın.

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>URL 'lerde güvenli olmayan ve ayrılmış karakterleri kodlama

 Lütfen tüm güvenli olmayan ve ayrılmış karakterlerin bir URL 'de kodlandığını doğrulayın. Örneğin, URL 'deki bir değer/bağlayıcı tanımlayıcısı olduğundan, ' # ' güvenli olmayan bir karakterdir. URL 'de kullanılıyorsa, karakterin `%23` kodlanmalıdır. ' & ' ve ' = ', parametreleri sınırlandıran ve Azure Bilişsel Arama değerlerini belirten ayrılmış karakter örnekleridir. Daha fazla ayrıntı için lütfen bkz. [rfc1738: Uniform Resource Konumlandırıcıları (URL)](https://www.ietf.org/rfc/rfc1738.txt) .

 Güvenli olmayan karakterler ``" ` < > # % { } | \ ^ ~ [ ]``. Ayrılan karakterler `; / ? : @ = + &`.

### <a name="precedence-operators-grouping-and-field-grouping"></a>Öncelik işleçleri: gruplandırma ve alan gruplama  
 Parantez, parantez içinde işleç dahil olmak üzere alt sorgular oluşturmak için kullanabilirsiniz. Örneğin, `motel+(wifi||luxury)` "Motel" terimini ve "WiFi" veya "merkezlerini" (ya da her ikisi) içeren belgeleri arayacak.

Alan gruplama benzerdir, ancak gruplamayı tek bir alanla kapsamlara sahiptir. Örneğin, `hotelAmenities:(gym+(wifi||pool))` "Gym" ve "WiFi" veya "Gym" ve "havuz" için "hotelAmenities" alanını arar.  

### <a name="searchmode-parameter-considerations"></a>SearchMode parametresi konuları  
 [Azure bilişsel arama basit sorgu söz dizimi](query-simple-syntax.md)bölümünde açıklandığı gibi sorgularda `searchMode` etkisi, Lucene sorgu söz dizimine eşit olarak uygulanır. Yani, NOT işleci ile birlikte `searchMode`, parametresini nasıl ayarlayacaı üzerinde hiçbir görünmemeye açık olmadığınız takdirde alışılmadık görünebilir olabilecek sorgu sonuçları elde edebilir. Varsayılan, `searchMode=any`ve bir NOT işleci kullanıyorsanız, işlem bir veya eylem olarak hesaplanır, yani "New York" NOT "Seattle", Seattle olmayan tüm şehirleri döndürür.  

##  <a name="bkmk_boolean"></a>Boole işleçleri (ve, veya DEĞIL) 
 Her zaman tüm büyük harf metin Boole işleçlerini (ve veya, DEĞIL) belirtin.  

### <a name="or-operator-or-or-"></a>OR işleci `OR` veya `||`

OR işleci dikey bir çubuk veya boru karakterdir. Örneğin: `wifi || luxury`, "WiFi" veya "merkezlerini" ya da her ikisini de içeren belgeleri arayacak. YA da varsayılan bir bağlantılı operatör olduğundan, `wifi luxury` `wifi || luxuery`eşdeğerdir.

### <a name="and-operator-and--or-"></a>AND işleci `AND`, `&&` veya `+`

AND işleci bir ve işareti ya da artı işareti. Örneğin: `wifi && luxury` hem "WiFi" hem de "merkezlerini" içeren belgeler için arama yapılır. Plus karakteri (+) gerekli şartlar için kullanılır. Örneğin, `+wifi +luxury` her iki terim tek bir belge alanında bir yerde gözükmelidir.


### <a name="not-operator-not--or--"></a>İşleç `NOT`, `!` veya `-` DEĞIL

NOT işleci bir ünlem işareti veya eksi işareti. Örneğin: `wifi !luxury`, "WiFi" terimine sahip olan ve/veya "merkezlerini" olmayan belgeleri arayacak. `searchMode` seçeneği, bir + veya | | yokluğunda, NOT işleci olan bir terimin, sorgudaki diğer koşullara göre mi yoksa ORed mi olduğunu denetler. işlecinde. `searchMode` `any`(varsayılan) veya `all`olarak ayarlan, hatırlayın.

`searchMode=any` kullanmak, daha fazla sonuç ekleyerek sorguların geri çekmeyi artırır ve varsayılan olarak "veya NOT" olarak yorumlanır. Örneğin, `wifi -luxury`, *WiFi* terimini içeren veya merkezlerini terimini içermeyen belgelerle eşleşir *.*

`searchMode=all` kullanmak, sorguların kesinliğini daha az sonuç ekleyerek artırır ve varsayılan olarak "ve NOT" olarak yorumlanır. Örneğin `wifi -luxury`, `wifi` terimini içeren belgelerle eşleşir ve `luxury`terimini içermez. Bu,-işleci için daha sezgisel bir davranıştır. Bu nedenle, aramaları geri çağırmak yerine duyarlık için optimize etmek isterseniz *ve* kullanıcılarınız aramalardaki `-` işlecini kullanıyorsa, `searchMode=any` üzerinden `searchMode=all` seçmeyi düşünmelisiniz.

##  <a name="bkmk_querysizelimits"></a>Sorgu boyutu sınırlamaları  
 Azure Bilişsel Arama 'e gönderebilmeniz için sorguların boyutuyla ilgili bir sınır vardır. Özellikle, en fazla 1024 yan tümce (ve, veya ile ayrılmış ifadeler) olabilir. Ayrıca, bir sorgudaki her bir terimin boyutunda yaklaşık 32 KB 'lik bir sınır vardır. Uygulamanız program aracılığıyla arama sorguları oluşturursa, bu şekilde, sınırsız boyut sorguları oluşturmamasını sağlayan bir şekilde tasarlamayı öneririz.  

##  <a name="bkmk_searchscoreforwildcardandregexqueries"></a>Puanlama joker karakteri ve Regex sorguları
 Azure Bilişsel Arama metin sorguları için sıklık tabanlı Puanlama ([tf-ıDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)) kullanır. Bununla birlikte, koşulların kapsamını büyük olasılıkla geniş olabilecek bir joker karakter ve Regex sorguları için, derecelendirmenin, rarer terimleriyle eşleşmelerin eşleşmesini engellemek için sıklık faktörü yok sayılır. Tüm eşleşmeler joker ve Regex aramaları için eşit olarak değerlendirilir.

##  <a name="bkmk_fields"></a>Parçalı arama  
Arama ifadesinin tek bir sözcük veya tümcecik ya da parantez içinde daha karmaşık bir ifade ya da isteğe bağlı olarak Boolean işleçleriyle, `fieldName:searchExpression` sözdizimiyle bir ara değer arama işlemi tanımlayabilirsiniz. Bazı örnekler şunlardır:  

- Tarz: canot geçmiş  

- Sanatçılar:("mil Davis" "John Coltrane")

Her iki dizenin tek bir varlık olarak değerlendirilmesini istiyorsanız, bu örnekte `artists` alanında iki ayrı sanatçıın aranmasına izin vermek için birden çok dizeyi tırnak işareti içine aldığınızdan emin olun.  

`fieldName:searchExpression` belirtilen alan bir `searchable` alanı olmalıdır.  Dizin özniteliklerinin alan tanımlarında nasıl kullanıldığına ilişkin ayrıntılar için bkz. [Dizin oluşturma](https://docs.microsoft.com/rest/api/searchservice/create-index) .  

> [!NOTE]
> Kullanılabilir arama ifadelerini kullanırken, her bir alan arama ifadesinde açık olarak belirtilmiş bir alan adı olduğundan `searchFields` parametresini kullanmanız gerekmez. Ancak, bazı parçaların belirli bir alan kapsamında bulunduğu bir sorgu çalıştırmak istiyorsanız `searchFields` parametresini kullanmaya devam edebilirsiniz ve REST çeşitli alanlara uygulanabilir. Örneğin, sorgu `search=genre:jazz NOT history&searchFields=description` `jazz` yalnızca `genre` alanı ile eşleşirken, `description` alanla `NOT history` eşleşecekti. `fieldName:searchExpression` ' de belirtilen alan adı her zaman `searchFields` parametresine göre önceliklidir. Bu örnekte, `searchFields` parametresine `genre` eklemesi gerekmez.

##  <a name="bkmk_fuzzy"></a>Benzer arama  
 Benzer bir arama, benzer bir yapıya sahip olan hükümlerle eşleşmeleri bulur. Her [Lucene belgesi](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)için, belirsiz aramalar, [dumerau-Pavenshtein mesafesini](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance)temel alır. Benzer aramalar, uzaklık ölçütlerine uyan en fazla 50 terim için bir dönemi genişletebilir. 

 Benzer bir arama yapmak için, tek bir sözcüğün sonundaki tilde "~" sembolünü, isteğe bağlı bir parametreyle, 0 ile 2 (varsayılan) arasında, düzenleme uzaklığını belirten bir sayı kullanın. Örneğin, "mavi ~" veya "mavi ~ 1", "mavi", "maves" ve "tutkalla" döndürür.

 Benzer arama yalnızca koşullara uygulanabilir, tümceciklere uygulanamaz, ancak her terime bir çok parçalı ad veya ifade içinde her bir terime ekleyebilirsiniz. Örneğin, "Unviersty ~ of ~" Wshington ~ "," University of Washington "ile eşleşir.
 

##  <a name="bkmk_proximity"></a>Yakınlık araması  
 Yakınlık aramaları, bir belgedeki birbirini yakın terimleri bulmak için kullanılır. Bir ifadenin sonuna, ardından yakınlık sınırını oluşturan sözcüklerin sayısını içeren bir tilde "~" simgesi ekleyin. Örneğin `"hotel airport"~5`, bir belgede "otel" ve "Havaalanı" terimlerini bir belgedeki 5 sözcükten sonra bulur.  


##  <a name="bkmk_termboost"></a>Terim artırma  
 Terim artırma, bir belgeyi, süresi içermeyen belgelere göre, daha yüksek bir dönem içeriyorsa bir belgenin derecelendirdiğini ifade eder. Bu, Puanlama profillerindeki Puanlama profillerinin belirli koşullar yerine belirli alanları arttırma açısından farklılık gösterir.  

Aşağıdaki örnek, farkları göstermeye yardımcı olur. Belirli bir alanda eşleşen bir Puanlama profili olduğunu, [müzik \ dizin örneğinde](index-add-scoring-profiles.md#bkmk_ex)de *tarzı* söylediğini varsayalım. Belirli arama terimlerini diğerlerinden daha fazla artırmak için kullanım süresi kullanılabilir. Örneğin, `rock^2 electronic`, tarzdaki arama terimlerini, dizindeki diğer aranabilir alanlardan daha yüksek olan belgeleri artırır. Ayrıca, arama terimini içeren belgeler, diğer arama teriminden daha *yüksek olarak* *derecelendirilir (* 2).  

 Bir terimi artırmak için, aradığınız terimin sonundaki "^" giriş işaretini, bir artırma faktörü (bir sayı) ile simge kullanın. Ayrıca tümcecikleri de kullanabilirsiniz. Yükseltme faktörü arttıkça, terim diğer arama koşullarına göre daha ilgili olacaktır. Varsayılan olarak, Boost faktörü 1 ' dir. Boost faktörü pozitif olmalıdır, ancak 1 ' den az olabilir (örneğin, 0,20).  

##  <a name="bkmk_regex"></a>Normal ifade arama  
 Normal ifade araması, [RegExp sınıfında](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html)belgelendiği gibi eğik çizgi "/" arasındaki içeriğe dayalı bir eşleşme bulur.  

 Örneğin, "Motel" veya "otel" içeren belgeleri bulmak için `/[mh]otel/`belirtin.  Normal ifade aramaları tek sözcüklerle eşleştirilir.   

##  <a name="bkmk_wildcard"></a>Joker karakter arama  
 Birden çok (*) veya tek (?) karakterli joker karakter aramaları için genellikle tanınan sözdizimini kullanabilirsiniz. Lucene sorgu ayrıştırıcısının, bu sembollerin tek bir terim ve tümcecik değil, kullanımını desteklediği unutulmamalıdır.  

 Örneğin, "Not" ("Not defteri" veya "Not defteri" gibi "Not" önekiyle birlikte kelimeleri içeren belgeleri bulmak için "Not *" değerini belirtin.  

> [!NOTE]  
>  \* Veya? kullanamazsınız bir aramanın ilk karakteri olarak sembol.  
>  Joker karakter arama sorgularında metin analizi yapılmaz. Sorgu zamanında, joker karakter sorgu terimleri arama dizininde analiz edilen koşullara göre karşılaştırılır ve genişletilir.

## <a name="see-also"></a>Ayrıca bkz.  

+ [Belgelerde ara](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [Filtreler ve sıralama için OData ifade sözdizimi](query-odata-filter-orderby-syntax.md)   
+ [Azure Bilişsel Arama basit sorgu söz dizimi](query-simple-syntax.md)   
