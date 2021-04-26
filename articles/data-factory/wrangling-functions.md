---
title: Azure Data Factory veri denetimi işlevleri
description: Azure Data Factory içindeki kullanılabilir veri Wrangling işlevlerine genel bakış
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/16/2021
ms.openlocfilehash: f7a4041d87e00fa01ae5ae4dff0cade3b9755d31
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600949"
---
# <a name="transformation-functions-in-power-query-for-data-wrangling"></a>Veri denetimi için Power Query dönüştürme işlevleri

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory veri ölçeği, Power Query ```M``` betikleri veri akışı betiğine çevirerek bulut ölçeğinde kod ücretsiz çevik veri hazırlama ve denetimi kullanmanıza olanak sağlar. ADF, [Power Query çevrimiçi](/powerquery-m/power-query-m-reference) ile tümleşir ve ```M``` veri akışı Spark altyapısını kullanarak Spark yürütmesi aracılığıyla Power Query işlevlerin kullanılabilir olmasını sağlar. 

> [!NOTE]
> ADF 'deki Power Query Şu anda genel önizlemeye karşı avta

Şu anda tüm Power Query M işlevleri, yazma sırasında kullanılabilir olmasına rağmen veri denetimi için desteklenmez. Mash-ups 'larınızı oluştururken, bir işlev desteklenmiyorsa aşağıdaki hata iletisiyle karşılaşırsınız:

`UserQuery : Expression.Error: The transformation logic is not supported as it requires dynamic access to rows of data, which cannot be scaled out.`

Desteklenen Power Query d işlevlerinin listesi aşağıda verilmiştir.

## <a name="column-management"></a>Sütun yönetimi

* Seçim: [Table. SelectColumns](/powerquery-m/table-selectcolumns)
* Kaldırma: [Table. RemoveColumns](/powerquery-m/table-removecolumns)
* Yeniden adlandırma: [Table. RenameColumns](/powerquery-m/table-renamecolumns), [Table. PrefixColumns](/powerquery-m/table-prefixcolumns), [Table. transformcolumnnames](/powerquery-m/table-transformcolumnnames)
* Yeniden sıralama: [Table. ReorderColumns](/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>Satır filtreleme

Aşağıdaki koşullara göre filtrelemek için, [. SelectRows.](/powerquery-m/table-selectrows) ini kullanın:

* Eşitlik ve eşitsizlik
* Sayısal, metin ve Tarih karşılaştırmaları (DateTime değil)
* Sayı gibi sayısal bilgiler [. ıyedi](/powerquery-m/number-iseven) / [tek](/powerquery-m/number-iseven)
* [Metin. Contains](/powerquery-m/text-contains), [Text. StartsWith](/powerquery-m/text-startswith)veya [Text. EndsWith](/powerquery-m/text-endswith) kullanılarak metin kapsama
* Tüm ' ısin' [Tarih işlevlerini](/powerquery-m/date-functions)içeren tarih aralıkları) 
* Bu koşulların ve, veya değil, koşullarını kullanma

## <a name="adding-and-transforming-columns"></a>Sütun ekleme ve dönüştürme

Şu n işlevleri sütun ekler veya dönüştürür: [Table. AddColumn](/powerquery-m/table-addcolumn), [Table. transformcolumns](/powerquery-m/table-transformcolumns), [Table. Replacevalue](/powerquery-m/table-replacevalue), [Table. duplicatecolumn](/powerquery-m/table-duplicatecolumn). Desteklenen dönüştürme işlevleri aşağıda verilmiştir.

* Sayısal aritmetik
* Metin birleştirme
* Tarih ve saat aritmetiği (aritmetik işleçler, [Tarih. AddDays](/powerquery-m/date-adddays), [date. addaylar](/powerquery-m/date-addmonths), [date. Addçeyrekler](/powerquery-m/date-addquarters), [date. addhaftalar](/powerquery-m/date-addweeks), [date. addyıllar](/powerquery-m/date-addyears))
* Süreler tarih ve saat aritmetiği için kullanılabilir, ancak bir havuza yazılmadan önce başka bir türe dönüştürülmelidir (aritmetik işleçler, [#duration](/powerquery-m/sharpduration), [Duration. Days](/powerquery-m/duration-days), [Duration. hours](/powerquery-m/duration-hours), [Duration. dakika](/powerquery-m/duration-minutes), [Duration. saniye](/powerquery-m/duration-seconds), [Duration. TotalDays](/powerquery-m/duration-totaldays), Duration. [TotalHours](/powerquery-m/duration-totalhours), [Duration. TotalMinutes](/powerquery-m/duration-totalminutes), [Duration. TotalSeconds](/powerquery-m/duration-totalseconds))    
* En standart, bilimsel ve trigonometrik sayısal işlevler (sayı *hariç* [işlemler](/powerquery-m/number-functions#operations), [yuvarlama](/powerquery-m/number-functions#rounding)ve [trigonometrik metri](/powerquery-m/number-functions#trigonometry) , sayı. çarpınmalar ve sayı. birleşimler)
* Değiştirme (tekrar[cer. ReplaceText](/powerquery-m/replacer-replacetext), tekrar [cer. replacevalue](/powerquery-m/replacer-replacevalue), [Text. Replace](/powerquery-m/text-replace), [Text. Remove](/powerquery-m/text-remove))
* Konumsal metin ayıklama ([Text. PositionOf](/powerquery-m/text-positionof), [Text. length](/powerquery-m/text-length), [Text. Start](/powerquery-m/text-start), [Text. End](/powerquery-m/text-end), [Text. ortadaki](/powerquery-m/text-middle), [Text. değiştirici serange](/powerquery-m/text-replacerange), [Text. RemoveRange](/powerquery-m/text-removerange))
* Temel metin biçimlendirme ([Text. Lower](/powerquery-m/text-lower), [Text. Upper](/powerquery-m/text-upper), [Text. Trim](/powerquery-m/text-trim) / [Start](/powerquery-m/text-trimstart) / [End](/powerquery-m/text-trimend), [Text. asma start](/powerquery-m/text-padstart) / [End](/powerquery-m/text-padend), [Text. Reverse](/powerquery-m/text-reverse))
* Tarih/saat Işlevleri ([Tarih. gün](/powerquery-m/date-day), [Tarih. ay](/powerquery-m/date-month), [Tarih. yıl](/powerquery-m/date-year) [saat. saat](/powerquery-m/time-hour), [saat. dakika](/powerquery-m/time-minute), [saat. saniye](/powerquery-m/time-second), [Tarih. DayOfWeek](/powerquery-m/date-dayofweek), [Tarih. DayOfYear](/powerquery-m/date-dayofyear), [date. DaysInMonth](/powerquery-m/date-daysinmonth))
* If ifadeleri (ancak dallar eşleşen türlere sahip olmalıdır)
* Mantıksal sütun olarak satır filtreleri
* Sayı, metin, mantıksal, tarih ve tarih/saat sabitleri

<a name="mergingjoining-tables"></a>Tabloları birleştirme/birleştirme
----------------------
* Power Query iç içe bir JOIN (Table. Nestedjoın) oluşturacak ve kullanıcılar el ile [Table. AddJoinColumn](/powerquery-m/table-addjoincolumn)yazabilir.
    Kullanıcılar daha sonra iç içe JOIN sütununu iç içe olmayan bir birleşime genişletmelidir (başka bir bağlamda desteklenmez).
* M işlevi   [Table. JOIN](/powerquery-m/table-join) , ek bir genişletme adımının gereksinimini ortadan kaldırmak için doğrudan yazılabilir, ancak kullanıcının birleştirilmiş tablolar arasında yinelenen sütun adları olmadığından emin olması gerekir
* Desteklenen birleşim türleri:   [iç](/powerquery-m/joinkind-inner),   [soltouter](/powerquery-m/joinkind-leftouter),   [sağtouter](/powerquery-m/joinkind-rightouter),   [FullOuter](/powerquery-m/joinkind-fullouter)
* [Değer. Equals](/powerquery-m/value-equals) ve [Value. nullableequals](/powerquery-m/value-nullableequals) , anahtar eşitlik Karşılaştırıcılar olarak desteklenir

## <a name="group-by"></a>Gruplandırma ölçütü:

Değerleri toplamak için [Table. Group](/powerquery-m/table-group) kullanın.
* Bir toplama işleviyle birlikte kullanılması gerekir
* Desteklenen toplama işlevleri:   [List. Sum](/powerquery-m/list-sum),   [List. Count](/powerquery-m/list-count),   [List. Average](/powerquery-m/list-average),   [List. min](/powerquery-m/list-min),   [List. Max](/powerquery-m/list-max),   [List. standardsap,](/powerquery-m/list-standarddeviation)   [List. First](/powerquery-m/list-first),   [List. Last](/powerquery-m/list-last)

## <a name="sorting"></a>Sıralama

Değerleri sıralamak için [Table. Sort](/powerquery-m/table-sort) kullanın.

## <a name="reducing-rows"></a>Satırları azaltma

Bağımsız tut ve çıkar, aralığı tut (karşılık gelen k işlevleri, yalnızca destekleme sayısı, koşullar değil: [Table. FirstN](/powerquery-m/table-firstn), [Table. Skip](/powerquery-m/table-skip), [Table. Removefirstn](/powerquery-m/table-removefirstn), [Table. Range](/powerquery-m/table-range), [Table. Minn](/powerquery-m/table-minn), [Table. MAXn](/powerquery-m/table-maxn))

## <a name="known-unsupported-functions"></a>Bilinen desteklenmeyen işlevler

| İşlev | Durum |
| -- | -- |
| Table.PromoteHeaders | Desteklenmez. Aynı sonuç, veri kümesindeki "Ilk satır üstbilgi olarak" ayarlanarak elde edilebilir. |
| Table.CombineColumns | Bu, doğrudan desteklenmeyen ancak verilen iki sütunu birleştiren yeni bir sütun eklenerek elde edilen yaygın bir senaryodur.  Örneğin, Table. AddColumn (RemoveEmailColumn, "ad", her [FirstName] & "" & [LastName]) |
| Table.TransformColumnTypes | Çoğu durumda bu desteklenir. Aşağıdaki senaryolar desteklenmez: dizeyi para birimi türüne dönüştürme, dizeyi zaman türüne dönüştürme, dizeyi yüzde türüne dönüştürme. |
| Table.NestedJoin | Yalnızca bir birleşimi yapmanız doğrulama hatasına neden olur. Çalışması için sütunların genişletilmesi gerekir. |
| Table.Distinct | Yinelenen satırları kaldırma desteklenmiyor. |
| Table.RemoveLastN | Alt satırları kaldır desteklenmiyor. |
| Table.RowCount | Desteklenmez, ancak 1 değerini içeren özel bir sütun eklenerek ve sonra bu sütunu List. Sum ile toplayarak elde edilebilir. Table. Group desteklenir. | 
| Satır düzeyi hata işleme | Satır düzeyi hata işleme Şu anda desteklenmiyor. Örneğin, bir sütundan sayısal olmayan değerleri filtrelemek için, bir yaklaşım metin sütununu bir sayıya dönüştürmelidir. Dönüştürülemezse her hücre bir hata durumunda olur ve filtrelenebilir. Bu senaryo, ölçeği ölçeklendirilerek mümkün değildir. |
| Table.Transpose | Desteklenmez |
| Table.Pivot | Desteklenmez |
| Table.SplitColumn | Kısmen destekleniyor |

## <a name="m-script-workarounds"></a>Betiğe ilişkin geçici çözümler

### <a name="for-splitcolumn-there-is-an-alternate-for-split-by-length-and-by-position"></a>İçin ```SplitColumn``` , uzunluğa göre ve konuma göre bölünmüş bir alternatif vardır

* Table. AddColumn (kaynak, "Ilk karakter", her metin. Başlangıç ([e-posta], 7), tür metni)
* Table. AddColumn (# "ilk karakter ekledi", "metin aralığı", her metin. Orta ([e-posta], 4, 9), tür metni)

Bu seçeneğe Şeritteki Ayıkla seçeneğinden erişilebilir

![Power Query sütun Ekle](media/wrangling-data-flow/pq-split.png)

### <a name="for-tablecombinecolumns"></a>Bekleniyor ```Table.CombineColumns```

* Table. AddColumn (RemoveEmailColumn, "ad", her [FirstName] & "" & [LastName])


## <a name="next-steps"></a>Sonraki adımlar

[ADF 'de bir veri denetimi Power Query oluşturmayı](wrangling-tutorial.md)öğrenin.
