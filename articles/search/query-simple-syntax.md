---
title: Basit sorgu söz dizimi
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama 'de tam metin arama sorguları için kullanılan basit sorgu söz dizimi başvurusu.
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
ms.openlocfilehash: fb98be9975de38ec9f65e723e078a1db8755b4ed
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792548"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Azure Bilişsel Arama basit sorgu söz dizimi

Azure Bilişsel Arama iki adet Lucene tabanlı sorgu dili uygular: [basit sorgu ayrıştırıcısı](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) ve [Lucene sorgu ayrıştırıcısı](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). Azure Bilişsel Arama 'de basit sorgu söz dizimi, benzer/slop seçeneklerini dışlar.  

> [!NOTE]  
>  Azure Bilişsel Arama, daha karmaşık sorgular için alternatif bir [Lucene sorgu söz dizimi](query-lucene-syntax.md) sağlar. Her bir sözdiziminin sorgu ayrıştırma mimarisi ve avantajları hakkında daha fazla bilgi edinmek için bkz. [tam metin aramasının Azure bilişsel arama nasıl çalıştığı](search-lucene-query-architecture.md).

## <a name="how-to-invoke-simple-parsing"></a>Basit ayrıştırma çağırma

Basit sözdizimi varsayılandır. Çağırma yalnızca sözdizimini tam değerinden basit olarak sıfırlarsanız gereklidir. Sözdizimini açıkça ayarlamak için `queryType` Search parametresini kullanın. Geçerli değerler, varsayılan olarak `simple` ve Lucene için `full` `simple|full`içerir. 

## <a name="query-behavior-anomalies"></a>Sorgu davranışı bozuklukları

Bir veya daha fazla terim içeren herhangi bir metin, sorgu yürütmesi için geçerli bir başlangıç noktası olarak kabul edilir. Azure Bilişsel Arama, metnin çözümlenmesi sırasında bulunan tüm Çeşitlemeler dahil olmak üzere, koşulların herhangi birini veya tümünü içeren belgelerle eşleşir. 

Bu seslerin yanı sıra, Azure Bilişsel Arama sorgu yürütmesinin bir yönü vardır. Bu işlem, giriş dizesine daha fazla terim ve işleç eklendikçe, arama sonuçlarını azaltmak yerine artan sonuçlara neden *olabilir* . Bu genişletmenin gerçekten gerçekleşmediği, bir NOT işlecinin eklenmesine bağlıdır, AND veya OR davranışları açısından nasıl yorumlanmadığını belirleyen bir `searchMode` parametresi ayarıyla birleştirilir. Varsayılan, `searchMode=Any`ve NOT işleci verildiğinde, işlem bir veya eylem olarak hesaplanır; Örneğin, `"New York" NOT Seattle` Seattle olmayan tüm şehirleri döndürür.  

Genellikle, kullanıcıların daha fazla yerleşik gezinti yapılarına sahip olan e-ticaret sitelerinin aksine, içerik üzerinde arama yapan uygulamalar için Kullanıcı etkileşimi desenlerinde bu davranışları görmeniz daha olasıdır. Daha fazla bilgi için, bkz. [işleç](#not-operator). 

## <a name="boolean-operators-and-or-not"></a>Boole işleçleri (ve, veya DEĞIL) 

Eşleşen belgelerin bulunduğu bir dizi zengin ölçüt kümesi oluşturmak için işleçleri bir sorgu dizesine ekleyebilirsiniz. 

### <a name="and-operator-"></a>AND işleci `+`

AND işleci bir artı işareti. Örneğin, `wifi+luxury` hem `wifi` hem de `luxury`içeren belgeler için arama yapılır.

### <a name="or-operator-"></a>OR işleci `|`

OR işleci dikey bir çubuk veya boru karakterdir. Örneğin, `wifi | luxury` `wifi` veya `luxury` ya da her ikisini de içeren belgeleri arayacak.

<a name="not-operator"></a>

### <a name="not-operator--"></a>İşleç `-` DEĞIL

NOT işleci eksi işareti. Örneğin, `wifi –luxury` `wifi` terimi olan ve/veya `luxury` olmayan (ve/veya `searchMode`tarafından denetlenen) belgeleri arar.

> [!NOTE]  
>  `searchMode` seçeneği, NOT işleci olan bir terimin bir `+` veya `|` işleci yokluğunda, sorgudaki diğer koşullara göre mi yoksa ORed mi olduğunu denetler. `searchMode` `any` (varsayılan) veya `all`olarak ayarlan, hatırlayın. `any`kullanıyorsanız, daha fazla sonuç ekleyerek sorguların geri çağrılması artar ve varsayılan olarak `-` "veya NOT" olarak yorumlanır. Örneğin, `wifi -luxury` `wifi` terimi veya `luxury`terimi içermeyen belgelerle eşleşir. `all`kullanırsanız, daha az sonuç ekleyerek sorguların duyarlığını artırır ve varsayılan olarak "ve NOT" olarak yorumlanır. Örneğin, `wifi -luxury` `wifi` terimi içeren belgelerle eşleşir ve "merkezlerini" terimini içermez. Bu, `-` işleci için daha sezgisel bir davranıştır. Bu nedenle, aramaları geri çağırmak yerine duyarlık için iyileştirmek istiyorsanız `searchMode=any` yerine `searchMode=all` kullanmayı düşünmelisiniz *ve* kullanıcılarınız aramalardaki `-` işlecini kullanır.

## <a name="suffix-operator"></a>Sonek işleci

Sonek işleci bir yıldız `*`. Örneğin, `lux*`, `lux`ile başlayan, büyük/küçük harf yok sayılarak bir terim içeren belgeler için arama yapılır.  

## <a name="phrase-search-operator"></a>Tümcecik arama işleci

Tümcecik işleci, tırnak işaretleri `" "`bir tümceciği barındırır. Örneğin, `Roach Motel` (tırnak işareti olmadan) herhangi bir sırada `Roach` ve/veya `Motel` içeren belgeleri aradığında, `"Roach Motel"` (tırnak işaretleriyle) yalnızca bu tümceciği içeren belgelerle ve bu sırayla eşleşir (metin analizi devam ediyor geçerlidir).

## <a name="precedence-operator"></a>Öncelik operatörü

Öncelik işleci, dizeyi parantez `( )`içine alır. Örneğin `motel+(wifi | luxury)`, Motel dönemi ve `wifi` veya `luxury` (ya da her ikisi) içeren belgeleri arayacak.  

## <a name="escaping-search-operators"></a>Kaçış arama işleçleri  

 Yukarıdaki sembolleri, arama metninin gerçek parçası olarak kullanmak için, bir ters eğik çizgiyle önek olarak önüne alınmalıdır. Örneğin, `luxury\+hotel` `luxury+hotel`terimi sonucunu elde eder. Daha tipik durumlarda bu kurala göre basit hale getirmek için, bu kuralın kaçışın gerekli olmadığı iki özel durum vardır:  

- NOT işleci `-`, bir dönemin ortasında değil, boşluktan sonra gelen ilk karakter olması durumunda yalnızca kaçışlı olması gerekir. Örneğin, `wi-fi` tek bir terimdir; Yani, GUID 'Ler (`3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`gibi) tek bir belirteç olarak değerlendirilir.
- Sonek işlecinin `*`, yalnızca boşluktan önceki son karakter olması, bir terimin ortasında olmaması durumunda değil, bunun önüne alınması gerekir. Örneğin, `wi*fi` tek bir belirteç olarak değerlendirilir.

> [!NOTE]  
>  Kaçış belirteçleri birlikte tutar, ancak çözümleme moduna bağlı olarak metin analizi bunları bölebilir. Ayrıntılar için bkz. [Azure &#40;bilişsel arama&#41; REST API dil desteği](index-add-language-analyzers.md) .  

## <a name="see-also"></a>Ayrıca bkz.  

+ [Belgeleri &#40;Azure Bilişsel Arama ara REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Lucene sorgu söz dizimi](query-lucene-syntax.md)
+ [OData ifadesi söz dizimi](query-odata-filter-orderby-syntax.md) 
