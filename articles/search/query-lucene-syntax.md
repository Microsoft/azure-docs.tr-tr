---
title: Lucene sorgu söz dizimi
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama joker karakter, belirsiz arama, RegEx ve diğer gelişmiş sorgu yapıları için kullanılan tam Lucene sorgu söz dizimi için başvuru.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: fc3662d8198e6ab6ab215ac1e9e8eac585f4250b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801596"
---
# <a name="lucene-query-syntax-in-azure-cognitive-search"></a>Azure Bilişsel Arama Lucene sorgu söz dizimi

Sorgu oluştururken, özel sorgu formları için [Lucene sorgu ayrıştırıcı](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) sözdizimini kabul edebilirsiniz: joker karakter, benzer arama, yakınlık arama, normal ifadeler. Lucene sorgu ayrıştırıcısı sözdiziminin çoğu, deyimler aracılığıyla oluşturulan *Aralık aramaları* dışında [Azure bilişsel arama ' de bir şekilde uygulanır](search-lucene-query-architecture.md) **`$filter`** . 

Tam Lucene sözdizimini kullanmak için, queryType öğesini "Full" olarak ayarlayacaksınız ve joker karakter, belirsiz arama veya tam sözdizimi tarafından desteklenen diğer sorgu formlarından biri için bir sorgu ifadesi desenli şekilde geçirilecek. BEKLEYEN ' de sorgu ifadeleri, **`search`** [arama belgeleri (REST API)](/rest/api/searchservice/search-documents) isteğinin parametresinde sağlanır.

## <a name="example-full-syntax"></a>Örnek (tam sözdizimi)

Aşağıdaki örnek, tam sözdizimi kullanılarak oluşturulan bir arama isteğidir. Bu belirli örnekte, alan içi arama ve terim arttırma gösterilmektedir. Kategori alanında "bütçe" teriminin bulunduğu oteller için arama yapar. "Son randevu" ifadesini içeren tüm belgeler, artırma değeri (3) sonucu olarak daha yüksektir.  

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
  "queryType": "full",
  "search": "category:budget AND \"recently renovated\"^3",
  "searchMode": "all"
}
```

Herhangi bir sorgu türüne özgü olmasa da **`searchMode`** parametresi bu örnekle ilgilidir. Her operatör sorgu üzerinde olduğunda, genellikle `searchMode=all` ölçütlerin *tümünün* eşleştiğinden emin olmak için ayarlamanız gerekir.  

Daha fazla örnek için bkz. [Lucene sorgu söz dizimi örnekleri](search-query-lucene-examples.md). SearchMode dahil olmak üzere sorgu isteği ve parametreleri hakkındaki ayrıntılar için bkz. [arama belgeleri (REST API)](/rest/api/searchservice/Search-Documents).

## <a name="syntax-fundamentals"></a><a name="bkmk_syntax"></a> Sözdizimi temelleri  

Aşağıdaki sözdizimi temelleri, Lucene sözdizimini kullanan tüm sorgular için geçerlidir.  

### <a name="operator-evaluation-in-context"></a>Bağlamda işleç değerlendirmesi

Yerleştirme, bir simgenin bir operatör ya da bir dizedeki yalnızca başka bir karakter olarak yorumlanıp yorumlanmadığını belirler.

Örneğin, Lucene Full sözdiziminde, hem belirsiz arama hem de yakınlık araması için tilde (~) kullanılır. Tırnak içine alınmış bir tümcecikden sonra yerleştirildiğinde,, yakınlık aramasını çağırır. Bir terimin sonuna yerleştirildiğinde, belirsiz aramayı çağırır.

"İş ~ analist" gibi bir dönem içinde, karakter bir işleç olarak değerlendirilmez. Bu durumda, sorgunun bir terim veya tümcecik sorgusu olduğu varsayılırsa, [sözcük temelli Analize](search-lucene-query-architecture.md#stage-2-lexical-analysis) sahip [tam metin araması](search-lucene-query-architecture.md) ,, ve "Business ~ analist" TERIMINI iki: iş veya analist olarak keser.

Yukarıdaki örnek, tilde (~), ancak aynı prensibi her operatör için geçerlidir.

### <a name="escaping-special-characters"></a>Özel karakterleri kaçış

Arama metinlerinin bir parçası olarak arama işleçlerinden herhangi birini kullanmak için, karakteri tek bir ters eğik çizgiyle () sonuna ekleyerek karakteri kaçış `\` . Örneğin, bir joker karakter araması için `https://` , burada `://` sorgu dizesinin bir parçası olan ' i belirtmeniz gerekir `search=https\:\/\/*` . Benzer şekilde, kaçan bir telefon numarası, şöyle görünebilir `\+1 \(800\) 642\-7676` .

Kaçışın gerekli olduğu özel karakterler şunlardır:  
`+ - & | ! ( ) { } [ ] ^ " ~ * ? : \ /`  

> [!NOTE]  
> Kaçış belirteçleri birlikte tutar, ancak dizin oluşturma sırasında [sözcük temelli analizler](search-lucene-query-architecture.md#stage-2-lexical-analysis) bunları açabilir. Örneğin, standart Lucene çözümleyici, sözcükleri kısa çizgilerden, boşluklardan ve diğer karakterlere göre keser. Sorgu dizesinde özel karakterlere ihtiyacınız varsa, bunları dizinde koruyan bir çözümleyici gerekebilir. Bazı seçimler, hecelenmiş kelimeleri koruyan Microsoft doğal [dil Çözümleyicileri](index-add-language-analyzers.md)veya daha karmaşık desenler için özel bir çözümleyici içerir. Daha fazla bilgi için bkz. [kısmi terimler, desenler ve özel karakterler](search-query-partial-matching.md).

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>URL 'lerde güvenli olmayan ve ayrılmış karakterleri kodlama

Lütfen tüm güvenli olmayan ve ayrılmış karakterlerin bir URL 'de kodlandığını doğrulayın. Örneğin, URL 'deki bir parça/bağlantı tanımlayıcısı olduğundan, ' # ' güvenli olmayan bir karakterdir. URL 'de kullanılıyorsa, karakterin kodlanmalıdır `%23` . ' & ' ve ' = ', parametreleri sınırlandıran ve Azure Bilişsel Arama değerlerini belirten ayrılmış karakter örnekleridir. Daha fazla ayrıntı için lütfen bkz. [rfc1738: Uniform Resource Konumlandırıcıları (URL)](https://www.ietf.org/rfc/rfc1738.txt) .

Güvenli olmayan karakterler ``" ` < > # % { } | \ ^ ~ [ ]`` . Ayrılan karakterler şunlardır `; / ? : @ = + &` .

## <a name="boolean-operators"></a><a name="bkmk_boolean"></a> Boole işleçleri

Bir eşleşmenin hassasiyetini artırmak için bir sorgu dizesine Boole işleçleri ekleyebilirsiniz. Tam sözdizimi, karakter işleçlerine ek olarak metin işleçlerini destekler. Her zaman tüm büyük harf metin Boole işleçlerini (ve veya, DEĞIL) belirtin.

|Metin işleci | Karakter | Örnek | Kullanım |
|--------------|----------- |--------|-------|
| AND | `&`, `+` | `wifi + luxury` | Bir eşleşmenin içermesi gereken terimleri belirtir. Örnekte, sorgu altyapısı hem hem de içeren belgeleri arayacaktır `wifi` `luxury` . Plus karakteri ( `+` ) gerekli şartlar için kullanılır. Örneğin, `+wifi +luxury` her iki terimi tek bir belge alanında bir yerde gözükmelidir.|
| VEYA | `|` | `wifi | luxury` | Herhangi bir terim bulunduğunda bir eşleşme bulur. Örnekte, sorgu altyapısı veya ya da her ikisini içeren belgelerde eşleşme döndürür `wifi` `luxury` . YA da varsayılan bir bağlantılı operatör olduğundan, buna eşdeğer olan gibi da bırakabilirsiniz `wifi luxury`  `wifi | luxury` .|
| NOT | `!`, `-` | `wifi –luxury` | Terimi hariç tutmak için eşleşen belgeleri döndürür. Örneğin, terimi olan, `wifi –luxury` `wifi` ancak olmayan belgeleri arar `luxury` . <br/><br/>`searchMode`Bir sorgu isteğindeki parametresi, Not işleci olan bir terimin, sorgudaki diğer koşullara sahip veya ORed olup olmadığını denetler ( `+` diğer koşullarda hiçbir veya işleci olmadığı varsayılarak `|` ). Geçerli değerler `any` veya içerir `all` .  <br/><br/>`searchMode=any` sorgu geri çekmeyi daha fazla sonuç ekleyerek artırır ve varsayılan `-` olarak "veya Not" olarak yorumlanır. Örneğin, `wifi -luxury` terimi ya da terimi içermeyen belgelerle eşleşir `wifi` `luxury` .  <br/><br/>`searchMode=all` sorguların hassasiyetini daha az sonuç ekleyerek artırır ve varsayılan olarak "ve NOT" olarak yorumlanır. Örneğin, `wifi -luxury` terimi içeren belgelerle eşleştirecektir `wifi` ve "merkezlerini" terimini içermemelidir. Bu, operatör için daha sezgisel bir davranış ile yapılır `-` . Bu nedenle, `searchMode=all` `searchMode=any` aramalarını geri çağırmak yerine duyarlık için optimize etmek istiyorsanız yerine kullanmanız gerekir *ve* kullanıcılarınız, `-` aramalardaki işleci sıklıkla kullanır.<br/><br/>Bir ayar üzerinde karar verirken `searchMode` , çeşitli uygulamalardaki sorgular için Kullanıcı etkileşimi düzenlerini göz önünde bulundurun. Bilgi arayan kullanıcıların, daha fazla yerleşik gezinti yapılarına sahip olan e-ticaret sitelerinin aksine, bir sorguya işleç ekleme olasılığı yüksektir. |

##  <a name="fielded-search"></a><a name="bkmk_fields"></a> Parçalı arama

`fieldName:searchExpression`Arama ifadesinin tek bir sözcük veya tümcecik ya da parantez içinde daha karmaşık bir ifade olabilecek, isteğe bağlı olarak Boolean işleçleriyle, bir ara değer arama işlemini sözdizimi ile tanımlayabilirsiniz. Bazı örnekler şunlardır:  

- Tarz: canot geçmiş  

- Sanatçılar:("mil Davis" "John Coltrane")

Her iki dizenin de tek bir varlık olarak değerlendirilmesini istiyorsanız, bu durumda alanda iki ayrı sanatçı aramak istiyorsanız, tırnak işaretleri içine birden çok dize yerleştirdiğinizden emin olun `artists` .  

İçinde belirtilen alanın `fieldName:searchExpression` bir alan olması gerekir `searchable` .  Dizin özniteliklerinin alan tanımlarında nasıl kullanıldığına ilişkin ayrıntılar için bkz. [Dizin oluşturma](/rest/api/searchservice/create-index) .  

> [!NOTE]
> Kullanılabilir arama ifadelerini kullanırken, `searchFields` her bir alan arama ifadesinde açık olarak belirtilmiş bir alan adı olduğundan, parametresini kullanmanız gerekmez. Ancak, `searchFields` bazı parçaların belirli bir alan kapsamında bulunduğu bir sorgu çalıştırmak istiyorsanız parametresini kullanmaya devam edebilirsiniz ve REST birçok alana uygulanabilir. Örneğin, sorgu `search=genre:jazz NOT history&searchFields=description` `jazz` yalnızca `genre` alanla eşleşirken alanla eşleşir `NOT history` `description` . Her zaman ' de belirtilen alan adı, `fieldName:searchExpression` `searchFields` Bu örnekte bu nedenle parametreye dahil etmemiz gerekmediğimiz parametreye göre öncelik alır `genre` `searchFields` .

##  <a name="fuzzy-search"></a><a name="bkmk_fuzzy"></a> Benzer arama

Benzer bir arama, benzer bir yapıya sahip hükümlerde eşleşmeleri bulur ve iki veya daha az uzaklık ölçütlerine uyan en fazla 50 terim için bir terim genişletiyor. Daha fazla bilgi için bkz. [belirsiz arama](search-query-fuzzy.md).

Benzer bir arama yapmak için, tek bir sözcüğün sonundaki tilde "~" sembolünü, isteğe bağlı bir parametreyle, 0 ile 2 (varsayılan) arasında, düzenleme uzaklığını belirten bir sayı kullanın. Örneğin, "mavi ~" veya "mavi ~ 1", "mavi", "maves" ve "tutkalla" döndürür.

Benzer arama yalnızca koşullara uygulanabilir, tümceciklere uygulanamaz, ancak her terime bir çok parçalı ad veya ifade içinde her bir terime ekleyebilirsiniz. Örneğin, "Unviersty ~ of ~" Wshington ~ "," University of Washington "ile eşleşir.
 
##  <a name="proximity-search"></a><a name="bkmk_proximity"></a> Yakınlık araması

Yakınlık aramaları, bir belgedeki birbirini yakın terimleri bulmak için kullanılır. Bir ifadenin sonuna, ardından yakınlık sınırını oluşturan sözcüklerin sayısını içeren bir tilde "~" simgesi ekleyin. Örneğin, `"hotel airport"~5` "otel" ve "Havaalanı" terimlerini bir belgedeki birbirini 5 sözcükten bulur.  

##  <a name="term-boosting"></a><a name="bkmk_termboost"></a> Terim artırma

Terim artırma, bir belgeyi, süresi içermeyen belgelere göre, daha yüksek bir dönem içeriyorsa bir belgenin derecelendirdiğini ifade eder. Bu, Puanlama profillerindeki Puanlama profillerinin belirli koşullar yerine belirli alanları arttırma açısından farklılık gösterir.  

Aşağıdaki örnek, farkları göstermeye yardımcı olur. Belirli bir alanda eşleşen bir Puanlama profili olduğunu, [müzik \ dizin örneğinde](index-add-scoring-profiles.md#bkmk_ex)de *tarzı* söylediğini varsayalım. Belirli arama terimlerini diğerlerinden daha fazla artırmak için kullanım süresi kullanılabilir. Örneğin, `rock^2 electronic` tarzdaki arama terimlerini içeren belgeleri, dizin içindeki diğer aranabilir alanlardan daha yüksek olacak şekilde artırır. Ayrıca, arama terimini içeren belgeler, diğer arama teriminden daha *yüksek olarak* *derecelendirilir (* 2).  

 Bir terimi artırmak için, aradığınız terimin sonundaki "^" giriş işaretini, bir artırma faktörü (bir sayı) ile simge kullanın. Ayrıca tümcecikleri de kullanabilirsiniz. Yükseltme faktörü arttıkça, terim diğer arama koşullarına göre daha ilgili olacaktır. Varsayılan olarak, Boost faktörü 1 ' dir. Boost faktörü pozitif olmalıdır, ancak 1 ' den az olabilir (örneğin, 0,20).  

##  <a name="regular-expression-search"></a><a name="bkmk_regex"></a> Normal ifade arama  
 Normal ifade araması, [RegExp sınıfında](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html)belgelendiği gibi Apache Lucene altında geçerli olan desenleri temel alan bir eşleşme bulur. Azure Bilişsel Arama, normal bir ifade eğik çizgiler arasında alınır `/` .

 Örneğin, "Motel" veya "otel" içeren belgeleri bulmak için, öğesini belirtin `/[mh]otel/` . Normal ifade aramaları tek sözcüklerle eşleştirilir.

Bazı araçlar ve diller, ek kaçış karakter gereksinimleri de vardır. JSON için, eğik çizgi içeren dizelerin ters eğik çizgiyle kaçışması vardır: "microsoft.com/azure/" `search=/.*microsoft.com\/azure\/.*/` `search=/.* <string-placeholder>.*/` , normal ifadenin ayarlandığı ve kaçış `microsoft.com\/azure\/` eğik çizgiyle kaçış olan dize olur.

##  <a name="wildcard-search"></a><a name="bkmk_wildcard"></a> Joker karakter arama

Birden çok ( `*` ) veya tek ( `?` ) karakterli joker karakter aramaları için genellikle tanınan sözdizimini kullanabilirsiniz. Örneğin, bir sorgu ifadesi `search=alpha*` "alfasayısal" veya "alfabetik" döndürür. Lucene sorgu ayrıştırıcısının, bu sembollerin tek bir terim ve tümcecik değil, kullanımını desteklediği unutulmamalıdır.

Full Lucene sözdizimi ön eki, infıx ve sonek eşleştirmeyi destekler. Ancak, tüm ihtiyacınız olan önek eşleme ise, basit söz dizimini kullanabilirsiniz (önek eşleme her ikisinde de desteklenir).

`*` `?` Dizenin (ın içinde olduğu gibi `search=/.*numeric./` ) veya/çözüm eşleştirmesinin kullanıldığı sonek eşleştirme, tam Lucene sözdiziminin yanı sıra normal ifade eğik çizgi `/` sınırlayıcılarını gerektirir. * Veya? kullanamazsınız bir terimin ilk karakteri olarak veya bir terim içinde, olmadan `/` . 

> [!NOTE]  
> Bir kural olarak, bir terim içindeki karakter dizileri için belirteçler oluşturan Edge n-gram simgeleştirme gibi alternatif yöntemleri araştırmak isteyebilirsiniz. Dizin daha büyük olacaktır, ancak model oluşturma ve dizininizdeki dizelerin uzunluğuna bağlı olarak sorgular daha hızlı çalışabilir.
>

### <a name="impact-of-an-analyzer-on-wildcard-queries"></a>Bir çözümleyicinin joker karakter sorguları üzerindeki etkisi

Sorgu ayrıştırma sırasında, önek, sonek, joker karakter veya normal ifadeler olarak ifade edilen sorgular, [sözcük temelli analizleri](search-lucene-query-architecture.md#stage-2-lexical-analysis)atlayarak sorgu ağacına olarak geçirilir. Eşleşmeler yalnızca, sorgunun belirttiği biçimdeki dizeleri içermesi halinde bulunur. Çoğu durumda, kısmi terim ve kalıp eşleştirme başarılı olması için dize bütünlüğünü koruyan dizin oluşturma sırasında bir çözümleyiciye ihtiyacınız olacaktır. Daha fazla bilgi için bkz. [Azure bilişsel arama sorgularda kısmi terim arama](search-query-partial-matching.md).

' Sonlandır ', ' sonlandırma ' ve ' Terminate ' gibi terimleri içeren sonuçları döndürmek için ' sonlandırma: * ' arama sorgusunun sonuçlarını almak isteyebileceğiniz bir durum düşünün.

En. Lucene (Ingilizce Lucene) Çözümleyicisi 'ni kullanacaksanız, her bir terimin agresif sözcük uygular. Örneğin, ' Terminate ', ' sonlandırma ', ' Terminate ' tümünün, dizininizdeki ' termi ' belirtecine göre simgeleştirilmiş olarak ayarlanabilir. Diğer taraftan, joker karakter veya benzer arama kullanan sorguların terimleri hiçbir şekilde çözümlenmez. bu nedenle, ' sonlandırılımı * ' sorgusuyla eşleşecek sonuç olmaz.

Diğer tarafta, Microsoft Çözümleyicileri (Bu durumda, en. Microsoft Çözümleyicisi) biraz daha ilerlemiş ve sözcük kökü ayırmayı yerine, açık bir hale getirme kullanır. Bu, oluşturulan tüm belirteçlerin geçerli Ingilizce sözcükler olması anlamına gelir. Örneğin, ' Sonlandır ', ' Terminate ' ve ' sonlandırma ' genellikle dizinde tamamen kalır ve Joker karakterlere ve benzer aramada çok büyük bir seçeneğe bağlı senaryolar için tercih edilen bir seçimdir.

## <a name="scoring-wildcard-and-regex-queries"></a>Puanlama joker karakteri ve Regex sorguları

Azure Bilişsel Arama metin sorguları için sıklık tabanlı Puanlama ([tf-ıDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)) kullanır. Bununla birlikte, koşulların kapsamını büyük olasılıkla geniş olabilecek bir joker karakter ve Regex sorguları için, derecelendirmenin, rarer terimleriyle eşleşmelerin eşleşmesini engellemek için sıklık faktörü yok sayılır. Tüm eşleşmeler joker ve Regex aramaları için eşit olarak değerlendirilir.

## <a name="special-characters"></a>Özel karakterler

Bazı durumlarda, bir ' ❤ ' emoji veya ' € ' işareti gibi özel bir karakter aramak isteyebilirsiniz. Bu gibi durumlarda, kullandığınız çözümleyicinin bu karakterleri filtrelemez olduğundan emin olun. Standart çözümleyici, dizininizden hariç olmak üzere birçok özel karakteri atlar.

Özel karakterleri simgeleştirmek için kullanılan çözümleyiciler "Whitespace" çözümleyicisini içerir. Bu, "❤" dizesi belirteç olarak kabul edilir. Ayrıca, Microsoft Ingilizce Çözümleyicisi ("en. Microsoft") gibi bir dil Çözümleyicisi, "€" dizesini belirteç olarak alır. Belirli bir sorgu için ürettiği belirteçleri görmek üzere [bir Çözümleyicisi test](/rest/api/searchservice/test-analyzer) edebilirsiniz.

Unicode karakterler kullanılırken, simgenin sorgu URL 'sinde doğru bir şekilde atlanacağından emin olun (örneğin, "❤" için çıkış sırasını kullanır `%E2%9D%A4+` ). Postman bu çeviriyi otomatik olarak yapar.  

## <a name="precedence-grouping"></a>Öncelik (gruplama)

Parantez, parantez içinde işleç dahil olmak üzere alt sorgular oluşturmak için kullanabilirsiniz. Örneğin, `motel+(wifi|luxury)` "Motel" terimini ve "WiFi" veya "merkezlerini" (ya da her ikisi) içeren belgeleri arar.

Alan gruplama benzerdir, ancak gruplamayı tek bir alanla kapsamlara sahiptir. Örneğin, " `hotelAmenities:(gym+(wifi|pool))` hotelAmenities" alanını "Gym" ve "WiFi" ya da "Gym" ve "havuz" olarak arar.  

## <a name="query-size-limits"></a>Sorgu boyutu sınırları

Azure Bilişsel Arama 'e gönderebilmeniz için sorguların boyutuyla ilgili bir sınır vardır. Özellikle, en fazla 1024 yan tümce (ve, veya ile ayrılmış ifadeler) olabilir. Ayrıca, bir sorgudaki her bir terimin boyutunda yaklaşık 32 KB 'lik bir sınır vardır. Uygulamanız program aracılığıyla arama sorguları oluşturursa, bu şekilde, sınırsız boyut sorguları oluşturmamasını sağlayan bir şekilde tasarlamayı öneririz.  

## <a name="see-also"></a>Ayrıca bkz.

+ [Basit arama için sorgu örnekleri](search-query-simple-examples.md)
+ [Tam Lucene Search için sorgu örnekleri](search-query-lucene-examples.md)
+ [Belgelerde ara](/rest/api/searchservice/Search-Documents)
+ [Filtreler ve sıralama için OData ifade sözdizimi](query-odata-filter-orderby-syntax.md)   
+ [Azure Bilişsel Arama basit sorgu söz dizimi](query-simple-syntax.md)
