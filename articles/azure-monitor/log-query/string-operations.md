---
title: Azure Izleyici günlük sorgularındaki dizelerle çalışma | Microsoft Docs
description: Azure Izleyici günlük sorgularındaki dizeler üzerinde nasıl düzenleme, karşılaştırma, arama ve çeşitli işlemler gerçekleştirme işlemlerini açıklar.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 0d7bf025b414df819887192bb59f7fd8da64b5d9
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932926"
---
# <a name="work-with-strings-in-azure-monitor-log-queries"></a>Azure Izleyici günlük sorgularındaki dizelerle çalışma


> [!NOTE]
> Bu öğreticiyi tamamlamadan önce [Azure izleyici Log Analytics kullanmaya başlama](get-started-portal.md) ve [Azure izleyici günlük sorgularını](get-started-queries.md) kullanmaya başlama işlemini tamamlamanız gerekir.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Bu makalede, dizeler üzerinde nasıl düzenleme, karşılaştırma, arama yapmak ve çeşitli işlemler gerçekleştirme işlemleri açıklanmaktadır.

Bir dizedeki her karakterin konumuna göre bir dizin numarası vardır. İlk karakter dizin 0 ' dır, sonraki karakter 1 ' dir ve bu şekilde devam eder. Farklı dize işlevleri, aşağıdaki bölümlerde gösterildiği gibi dizin numaralarını kullanır. Aşağıdaki örneklerden birçoğu, belirli bir veri kaynağı kullanmadan dize işlemeyi göstermek için **Print** komutunu kullanır.


## <a name="strings-and-escaping-them"></a>Dizeler ve kaçış
Dize değerleri, tek veya çift tırnak karakterleriyle paketlenir. Ters eğik çizgi (\\), sekme için \t, yeni satır için \n, ve tırnak karakterinin \" gibi karakterleri izleyen karaktere atlamak için kullanılır.

```Kusto
print "this is a 'string' literal in double \" quotes"
```

```Kusto
print 'this is a "string" literal in single \' quotes'
```

"\\" ın kaçış karakteri olarak davranmasını engellemek için, dizeye önek olarak "\@" ekleyin:

```Kusto
print @"C:\backslash\not\escaped\with @ prefix"
```


## <a name="string-comparisons"></a>Dize karşılaştırmaları

İşleç       |Açıklama                         |Büyük/küçük harfe duyarlı|Örnek (`true`verir)
---------------|------------------------------------|--------------|-----------------------
`==`           |Eşittir                              |Yes           |`"aBc" == "aBc"`
`!=`           |Eşit değildir                          |Yes           |`"abc" != "ABC"`
`=~`           |Eşittir                              |Hayır            |`"abc" =~ "ABC"`
`!~`           |Eşit değildir                          |Hayır            |`"aBc" !~ "xyz"`
`has`          |Sağ taraftaki bir bütün terim, sol taraftaki bir terimdir |Hayır|`"North America" has "america"`
`!has`         |Sağ taraftaki, sol taraftaki bir tam terim değildir       |Hayır            |`"North America" !has "amer"` 
`has_cs`       |Sağ taraftaki bir bütün terim, sol taraftaki bir terimdir |Yes|`"North America" has_cs "America"`
`!has_cs`      |Sağ taraftaki, sol taraftaki bir tam terim değildir       |Yes            |`"North America" !has_cs "amer"` 
`hasprefix`    |Sağ taraftaki, sol taraftaki bir terim ön eki         |Hayır            |`"North America" hasprefix "ame"`
`!hasprefix`   |Sağ taraftaki bir terim, sol taraftaki bir terim ön eki değildir     |Hayır            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`    |Sağ taraftaki, sol taraftaki bir terim ön eki         |Yes            |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs`   |Sağ taraftaki bir terim, sol taraftaki bir terim ön eki değildir     |Yes            |`"North America" !hasprefix_cs "CA"` 
`hassuffix`    |Sağ taraftaki, sol taraftaki bir dönem sonekidir         |Hayır            |`"North America" hassuffix "ica"`
`!hassuffix`   |Sağ taraftaki, sol taraftaki bir terim son eki değildir     |Hayır            |`"North America" !hassuffix "americ"`
`hassuffix_cs`    |Sağ taraftaki, sol taraftaki bir dönem sonekidir         |Yes            |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs`   |Sağ taraftaki, sol taraftaki bir terim son eki değildir     |Yes            |`"North America" !hassuffix_cs "icA"`
`contains`     |Sağ taraftaki bir sol taraftaki alt dizi olarak gerçekleşir  |Hayır            |`"FabriKam" contains "BRik"`
`!contains`    |Sol tarafta sağ taraftaki bir durum oluşmaz           |Hayır            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |Sağ taraftaki bir sol taraftaki alt dizi olarak gerçekleşir  |Yes           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |Sol tarafta sağ taraftaki bir durum oluşmaz           |Yes           |`"Fabrikam" !contains_cs "Kam"`
`startswith`   |Sağ taraftaki, sol taraftaki bir ilk alt dizidir|Hayır            |`"Fabrikam" startswith "fab"`
`!startswith`  |Sağ taraftaki, sol taraftaki bir başlangıç alt dizisi değildir|Hayır        |`"Fabrikam" !startswith "kam"`
`startswith_cs`   |Sağ taraftaki, sol taraftaki bir ilk alt dizidir|Yes            |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`  |Sağ taraftaki, sol taraftaki bir başlangıç alt dizisi değildir|Yes        |`"Fabrikam" !startswith_cs "fab"`
`endswith`     |Sağ taraftaki sol taraftaki bir kapanış alt dizisi|Hayır             |`"Fabrikam" endswith "Kam"`
`!endswith`    |Sağ taraftaki, sol taraftaki bir kapanış alt dizisi değildir|Hayır         |`"Fabrikam" !endswith "brik"`
`endswith_cs`     |Sağ taraftaki sol taraftaki bir kapanış alt dizisi|Yes             |`"Fabrikam" endswith "Kam"`
`!endswith_cs`    |Sağ taraftaki, sol taraftaki bir kapanış alt dizisi değildir|Yes         |`"Fabrikam" !endswith "brik"`
`matches regex`|Sol taraftaki, sağ taraftaki bir eşleşme içeriyor        |Yes           |`"Fabrikam" matches regex "b.*k"`
`in`           |Öğelerin birine eşittir       |Yes           |`"abc" in ("123", "345", "abc")`
`!in`          |Öğelerin hiçbirine eşit değildir   |Yes           |`"bca" !in ("123", "345", "abc")`


## <a name="countof"></a>CountOf

Dizedeki alt dizenin tekrarlamalarını sayar. Düz dizeler ile eşleştirebilir veya normal ifade kullanabilir. Regex eşleştirirken düz dize eşleşmeleri çakışmayabilir.

### <a name="syntax"></a>Sözdizimi
```
countof(text, search [, kind])
```

### <a name="arguments"></a>Değişkenlerinden
- `text`-giriş dizesi 
- metnin içinde eşleşmesi için `search` düz dize veya normal ifade.
-  - _normal_ | _Regex_ `kind`(varsayılan: normal).

### <a name="returns"></a>Döndürdüğü

Arama dizesinin kapsayıcıda eşleştirileme sayısı. Regex eşleştirirken düz dize eşleşmeleri çakışmayabilir.

### <a name="examples"></a>Örnekler

#### <a name="plain-string-matches"></a>Düz dize eşleşmeleri

```Kusto
print countof("The cat sat on the mat", "at");  //result: 3
print countof("aaa", "a");  //result: 3
print countof("aaaa", "aa");  //result: 3 (not 2!)
print countof("ababa", "ab", "normal");  //result: 2
print countof("ababa", "aba");  //result: 2
```

#### <a name="regex-matches"></a>Regex eşleşiyor

```Kusto
print countof("The cat sat on the mat", @"\b.at\b", "regex");  //result: 3
print countof("ababa", "aba", "regex");  //result: 1
print countof("abcabc", "a.c", "regex");  // result: 2
```


## <a name="extract"></a>süzmek

Belirli bir dizeden normal ifade için bir eşleşme alır. İsteğe bağlı olarak, ayıklanan alt dizeyi belirtilen türe dönüştürür.

### <a name="syntax"></a>Sözdizimi

```Kusto
extract(regex, captureGroup, text [, typeLiteral])
```

### <a name="arguments"></a>Bağımsız Değişkenler

- `regex`-normal ifade.
- `captureGroup`-Ayıklanacak yakalama grubunu gösteren pozitif bir tamsayı sabiti. Tüm eşleşme için 0, normal ifadede ilk ' (' parantez ') ' ile eşleştirilen değer için 1, sonraki parantezler için 2 veya daha fazla.
- `text`-Aranacak dize.
- `typeLiteral`-isteğe bağlı bir tür değişmez değeri (örneğin, typeof (Long)). Sağlanmışsa ayıklanan alt dize bu türe dönüştürülür.

### <a name="returns"></a>Döndürdüğü
Belirtilen yakalama grubu captureGroup ile eşleşen alt dize, isteğe bağlı olarak typeLiteral 'a dönüştürüldü.
Eşleşme yoksa veya tür dönüştürme başarısız olursa, null değeri döndürün.

### <a name="examples"></a>Örnekler

Aşağıdaki örnek, *bilgisayar IP* 'nin son sekizlinin bir sinyal kaydından ayıklar:
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| project ComputerIP, last_octet=extract("([0-9]*$)", 1, ComputerIP) 
```

Aşağıdaki örnek, son sekizlinin ayıklar ve *gerçek* bir türe (sayı) çevirir ve sonraki IP değerini hesaplar
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| extend last_octet=extract("([0-9]*$)", 1, ComputerIP, typeof(real)) 
| extend next_ip=(last_octet+1)%255
| project ComputerIP, last_octet, next_ip
```

Aşağıdaki örnekte, dize *izlemesi* "Duration" tanımı için aranır. Eşleşme, *gerçek* olarak ayarlanır ve zaman *aralığı için süreyi veren*bir zaman sabiti (1 s) ile çarpılır.
```Kusto
let Trace="A=12, B=34, Duration=567, ...";
print Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real));  //result: 567
print Duration_seconds =  extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s);  //result: 00:09:27
```


## <a name="isempty-isnotempty-notempty"></a>IsEmpty, isnotempty, notEmpty

- *IsEmpty* , bağımsız değişken boş bir dize veya null ise true döndürür (Ayrıca bkz. *IsNull*).
- bağımsız değişken boş bir dize veya null değilse (Ayrıca bkz. *IsNotNull*), *IsNotEmpty* doğru döndürür. diğer ad: *notEmpty*.

### <a name="syntax"></a>Sözdizimi

```Kusto
isempty(value)
isnotempty(value)
```

### <a name="examples"></a>Örnekler

```Kusto
print isempty("");  // result: true

print isempty("0");  // result: false

print isempty(0);  // result: false

print isempty(5);  // result: false

Heartbeat | where isnotempty(ComputerIP) | take 1  // return 1 Heartbeat record in which ComputerIP isn't empty
```


## <a name="parseurl"></a>parseURL

Bir URL 'YI bölümlerine böler (protokol, ana bilgisayar, bağlantı noktası, vb.) ve dizeler olarak bölümleri içeren bir sözlük nesnesi döndürür.

### <a name="syntax"></a>Sözdizimi

```
parseurl(urlstring)
```

### <a name="examples"></a>Örnekler

```Kusto
print parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")
```

Sonuç şu şekilde olacaktır:
```
{
    "Scheme" : "http",
    "Host" : "contoso.com",
    "Port" : "80",
    "Path" : "/icecream/buy.aspx",
    "Username" : "user",
    "Password" : "pass",
    "Query Parameters" : {"a":"1","b":"2"},
    "Fragment" : "tag"
}
```


## <a name="replace"></a>değiştirin

Tüm Regex eşleşmelerini başka bir dizeyle değiştirir. 

### <a name="syntax"></a>Sözdizimi

```
replace(regex, rewrite, input_text)
```

### <a name="arguments"></a>Bağımsız Değişkenler

- `regex`-eşleştirilecek normal ifade. ' (' Parantezler ') ' içinde yakalama grupları içerebilir.
- `rewrite`-Regex ile eşleşen herhangi bir eşleşmenin yerini alır. Tüm eşleştirmeye başvurmak için \ 0, sonraki yakalama grupları için \ 2 vb. ilk yakalama grubu için \ 1 kullanın.
- `input_text`-arama yapılacak giriş dizesi.

### <a name="returns"></a>Döndürdüğü
Yeniden yazma değerlendirmelerinde Regex tüm eşleşmelerini değiştirdikten sonraki metin. Eşleşmeler çakışmıyor.

### <a name="examples"></a>Örnekler

```Kusto
SecurityEvent
| take 1
| project Activity 
| extend replaced = replace(@"(\d+) -", @"Activity ID \1: ", Activity) 
```

Aşağıdaki sonuçlara sahip olabilir:

Etkinlik                                        |silinmeyecek
------------------------------------------------|----------------------------------------------------------
4663-bir nesneye erişme girişiminde bulunuldu  |Etkinlik KIMLIĞI 4663: bir nesneye erişme girişiminde bulunuldu.


## <a name="split"></a>split

Verilen bir dizeyi belirtilen sınırlayıcıya göre böler ve sonuçta elde edilen alt dizelerin dizisini döndürür.

### <a name="syntax"></a>Sözdizimi
```
split(source, delimiter [, requestedIndex])
```

### <a name="arguments"></a>Değişkenlerinden

- `source`-belirtilen sınırlayıcıya göre bölünecek dize.
- `delimiter`-kaynak dizenin bölünebilmesi için kullanılacak sınırlayıcı.
- `requestedIndex`-isteğe bağlı sıfır tabanlı dizin. Sağlanmışsa, döndürülen dize dizisi yalnızca o öğeyi (varsa) tutar.


### <a name="examples"></a>Örnekler

```Kusto
print split("aaa_bbb_ccc", "_");    // result: ["aaa","bbb","ccc"]
print split("aa_bb", "_");          // result: ["aa","bb"]
print split("aaa_bbb_ccc", "_", 1); // result: ["bbb"]
print split("", "_");               // result: [""]
print split("a__b", "_");           // result: ["a","","b"]
print split("aabbcc", "bb");        // result: ["aa","cc"]
```

## <a name="strcat"></a>strcat

Dize bağımsız değişkenlerini art arda ekler (1-16 bağımsız değişkenlerini destekler).

### <a name="syntax"></a>Sözdizimi
```
strcat("string1", "string2", "string3")
```

### <a name="examples"></a>Örnekler
```Kusto
print strcat("hello", " ", "world") // result: "hello world"
```


## <a name="strlen"></a>strlen

Bir dizenin uzunluğunu döndürür.

### <a name="syntax"></a>Sözdizimi
```
strlen("text_to_evaluate")
```

### <a name="examples"></a>Örnekler
```Kusto
print strlen("hello")   // result: 5
```


## <a name="substring"></a>dizeden

Belirtilen dizinden başlayarak, belirli bir kaynak dizeden bir alt dize ayıklar. İsteğe bağlı olarak, istenen alt dizenin uzunluğu belirtilebilir.

### <a name="syntax"></a>Sözdizimi
```
substring(source, startingIndex [, length])
```

### <a name="arguments"></a>Değişkenlerinden

- `source`-alt dizenin alınacağı kaynak dize.
- `startingIndex`-istenen alt dizenin sıfır tabanlı başlangıç karakteri konumu.
- `length`-döndürülen alt dizenin istenen uzunluğunu belirtmek için kullanılabilen isteğe bağlı bir parametre.

### <a name="examples"></a>Örnekler
```Kusto
print substring("abcdefg", 1, 2);   // result: "bc"
print substring("123456", 1);       // result: "23456"
print substring("123456", 2, 2);    // result: "34"
print substring("ABCD", 0, 2);  // result: "AB"
```


## <a name="tolower-toupper"></a>ToLower, ToUpper

Verilen bir dizeyi tüm küçük veya büyük harflere dönüştürür.

### <a name="syntax"></a>Sözdizimi
```
tolower("value")
toupper("value")
```

### <a name="examples"></a>Örnekler
```Kusto
print tolower("HELLO"); // result: "hello"
print toupper("hello"); // result: "HELLO"
```



## <a name="next-steps"></a>Sonraki adımlar
Gelişmiş öğreticilerle devam edin:
* [Toplama işlevleri](aggregations.md)
* [Gelişmiş toplamalar](advanced-aggregations.md)
* [Grafikler ve diyagramlar](charts.md)
* [JSON ve veri yapıları ile çalışma](json-data-structures.md)
* [Gelişmiş sorgu yazma](advanced-query-writing.md)
* [Birleşimler çapraz analiz](joins.md)
