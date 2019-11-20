---
title: Azure Data Factory 'de wrangling veri akışı dönüştürme işlevleri
description: Azure Data Factory içindeki kullanılabilir denetimi veri akışı işlevlerine genel bakış
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 3274641f7b118e13b3ed727f609ce7471fd66b54
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682299"
---
# <a name="transformation-functions-in-wrangling-data-flow"></a>Denetimi veri akışındaki dönüştürme işlevleri

Azure Data Factory 'de denetimi veri akışı, bulut ölçeğinde kod ücretsiz çevik veri hazırlama ve denetimi kullanmanıza olanak sağlar. Denetimi veri akışı, [çevrimiçi Power Query](https://docs.microsoft.com/powerquery-m/power-query-m-reference) ile tümleşir ve Spark yürütmesi aracılığıyla veriler için Power Query M işlevlerini kullanılabilir hale getirir. 

Şu anda tüm Power Query M işlevleri, yazma sırasında kullanılabilir olmasına rağmen veri denetimi için desteklenmez. Denetimi veri akışlarınızı oluştururken, bir işlev desteklenmiyorsa aşağıdaki hata iletisiyle karşılaşırsınız:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

Desteklenen Power Query d işlevlerinin listesi aşağıda verilmiştir.

## <a name="column-management"></a>Sütun yönetimi

* Seçim: [Table. SelectColumns](https://docs.microsoft.com/powerquery-m/table-selectcolumns)
* Kaldırma: [Table. RemoveColumns](https://docs.microsoft.com/powerquery-m/table-removecolumns)
* Yeniden adlandırma: [Table. RenameColumns](https://docs.microsoft.com/powerquery-m/table-renamecolumns), [Table. PrefixColumns](https://docs.microsoft.com/powerquery-m/table-prefixcolumns), [Table. transformcolumnnames](https://docs.microsoft.com/powerquery-m/table-transformcolumnnames)
* Yeniden sıralama: [Table. ReorderColumns](https://docs.microsoft.com/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>Satır filtreleme

Aşağıdaki koşullara göre filtrelemek için, [. SelectRows.](https://docs.microsoft.com/powerquery-m/table-selectrows) ini kullanın:

* Eşitlik ve eşitsizlik
* Sayısal, metin ve Tarih karşılaştırmaları (DateTime değil)
* Sayı gibi sayısal bilgiler [. ıyedi](https://docs.microsoft.com/powerquery-m/number-iseven)/[tek](https://docs.microsoft.com/powerquery-m/number-iseven)
* [Metin. Contains](https://docs.microsoft.com/powerquery-m/text-contains), [Text. StartsWith](https://docs.microsoft.com/powerquery-m/text-startswith)veya [Text. EndsWith](https://docs.microsoft.com/powerquery-m/text-endswith) kullanılarak metin kapsama
* Tüm ' ısin' [Tarih işlevlerini](https://docs.microsoft.com/powerquery-m/date-functions)içeren tarih aralıkları) 
* Bu koşulların ve, veya değil, koşullarını kullanma

## <a name="adding-and-transforming-columns"></a>Sütun ekleme ve dönüştürme

Şu n işlevleri sütun ekler veya dönüştürür: [Table. AddColumn](https://docs.microsoft.com/powerquery-m/table-addcolumn), [Table. transformcolumns](https://docs.microsoft.com/powerquery-m/table-transformcolumns), [Table. Replacevalue](https://docs.microsoft.com/powerquery-m/table-replacevalue), [Table. duplicatecolumn](https://docs.microsoft.com/powerquery-m/table-duplicatecolumn). Desteklenen dönüştürme işlevleri aşağıda verilmiştir.

* Sayısal aritmetik
* Metin birleştirme
* Tarih ve saat aritmetiği (aritmetik işleçler, [Tarih. AddDays](https://docs.microsoft.com/powerquery-m/date-adddays), [date. addaylar](https://docs.microsoft.com/powerquery-m/date-addmonths), [date. Addçeyrekler](https://docs.microsoft.com/powerquery-m/date-addquarters), [date. addhaftalar](https://docs.microsoft.com/powerquery-m/date-addweeks), [date. addyıllar](https://docs.microsoft.com/powerquery-m/date-addyears))
* Süreler tarih ve saat aritmetiği için kullanılabilir, ancak bir havuza yazılmadan önce başka bir türe dönüştürülmesi gerekir (aritmetik işleçler, [#duration](https://docs.microsoft.com/powerquery-m/sharpduration), [Duration. Days](https://docs.microsoft.com/powerquery-m/duration-days), [Duration. hours](https://docs.microsoft.com/powerquery-m/duration-hours), [Duration. minutes](https://docs.microsoft.com/powerquery-m/duration-minutes), [ Süre. saniye](https://docs.microsoft.com/powerquery-m/duration-seconds), [süre. TotalDays](https://docs.microsoft.com/powerquery-m/duration-totaldays), [Duration. TotalHours](https://docs.microsoft.com/powerquery-m/duration-totalhours), [Duration. TotalMinutes](https://docs.microsoft.com/powerquery-m/duration-totalminutes), [Duration. TotalSeconds](https://docs.microsoft.com/powerquery-m/duration-totalseconds))    
* En standart, bilimsel ve trigonometrik sayısal işlevler (sayı *hariç* [işlemler](https://docs.microsoft.com/powerquery-m/number-functions#operations), [yuvarlama](https://docs.microsoft.com/powerquery-m/number-functions#rounding)ve [trigonometrik metri](https://docs.microsoft.com/powerquery-m/number-functions#trigonometry) , sayı. çarpınmalar ve sayı. birleşimler)
* Değiştirme (tekrar[cer. ReplaceText](https://docs.microsoft.com/powerquery-m/replacer-replacetext), tekrar [cer. replacevalue](https://docs.microsoft.com/powerquery-m/replacer-replacevalue), [Text. Replace](https://docs.microsoft.com/powerquery-m/text-replace), [Text. Remove](https://docs.microsoft.com/powerquery-m/text-remove))
* Konumsal metin ayıklama ([Text. PositionOf](https://docs.microsoft.com/powerquery-m/text-positionof), [Text. length](https://docs.microsoft.com/powerquery-m/text-length), [Text. Start](https://docs.microsoft.com/powerquery-m/text-start), [Text. End](https://docs.microsoft.com/powerquery-m/text-end), [Text. ortadaki](https://docs.microsoft.com/powerquery-m/text-middle), [Text. değiştirici serange](https://docs.microsoft.com/powerquery-m/text-replacerange), [Text. RemoveRange](https://docs.microsoft.com/powerquery-m/text-removerange))
* Temel metin biçimlendirme ([Text. Lower](https://docs.microsoft.com/powerquery-m/text-lower), [Text. Upper](https://docs.microsoft.com/powerquery-m/text-upper), [text. Trim](https://docs.microsoft.com/powerquery-m/text-trim)/[Başlangıç](https://docs.microsoft.com/powerquery-m/text-trimstart)/[End](https://docs.microsoft.com/powerquery-m/text-trimend), [Text. asma start](https://docs.microsoft.com/powerquery-m/text-padstart)/[End](https://docs.microsoft.com/powerquery-m/text-padend), [Text. Reverse](https://docs.microsoft.com/powerquery-m/text-reverse))
* Tarih/saat Işlevleri ([Tarih. gün](https://docs.microsoft.com/powerquery-m/date-day), [Tarih. ay](https://docs.microsoft.com/powerquery-m/date-month), [Tarih. yıl](https://docs.microsoft.com/powerquery-m/date-year) [saat. saat](https://docs.microsoft.com/powerquery-m/time-hour), [saat. dakika](https://docs.microsoft.com/powerquery-m/time-minute), [saat. saniye](https://docs.microsoft.com/powerquery-m/time-second), [Tarih. DayOfWeek](https://docs.microsoft.com/powerquery-m/date-dayofweek), [Tarih. DayOfYear](https://docs.microsoft.com/powerquery-m/date-dayofyear), [date. DaysInMonth](https://docs.microsoft.com/powerquery-m/date-daysinmonth))
* If ifadeleri (ancak dallar eşleşen türlere sahip olmalıdır)
* Mantıksal sütun olarak satır filtreleri
* Sayı, metin, mantıksal, tarih ve tarih/saat sabitleri

<a name="mergingjoining-tables"></a>Tabloları birleştirme/birleştirme
----------------------
* Power Query iç içe bir JOIN (Table. Nestedjoın) oluşturacak ve kullanıcılar el ile [Table. AddJoinColumn](https://docs.microsoft.com/powerquery-m/table-addjoincolumn)yazabilir.
    Kullanıcılar daha sonra iç içe JOIN sütununu iç içe olmayan bir birleşime genişletmelidir (başka bir bağlamda desteklenmez).
* M işlevi [Table. JOIN](https://docs.microsoft.com/powerquery-m/table-join) , ek bir genişletme adımının gereksinimini ortadan kaldırmak için doğrudan yazılabilir, ancak kullanıcının birleştirilmiş tablolar arasında yinelenen sütun adları olmadığından emin olması gerekir
* Desteklenen birleşim türleri: [iç](https://docs.microsoft.com/powerquery-m/joinkind-inner), [soltouter](https://docs.microsoft.com/powerquery-m/joinkind-leftouter), [sağtouter](https://docs.microsoft.com/powerquery-m/joinkind-rightouter), [FullOuter](https://docs.microsoft.com/powerquery-m/joinkind-fullouter)
* [Değer. Equals](https://docs.microsoft.com/powerquery-m/value-equals) ve [Value. nullableequals](https://docs.microsoft.com/powerquery-m/value-nullableequals) , anahtar eşitlik Karşılaştırıcılar olarak desteklenir

## <a name="group-by"></a>Gruplandırma ölçütü

Değerleri toplamak için [Table. Group](https://docs.microsoft.com/powerquery-m/table-group) kullanın.
* Bir toplama işleviyle birlikte kullanılması gerekir
* Desteklenen toplama işlevleri: [Table. RowCount](https://docs.microsoft.com/powerquery-m/table-rowcount), [List. Sum](https://docs.microsoft.com/powerquery-m/list-sum), [List. Count](https://docs.microsoft.com/powerquery-m/list-count), [List. Average](https://docs.microsoft.com/powerquery-m/list-average), [List. min](https://docs.microsoft.com/powerquery-m/list-min), [List. Max](https://docs.microsoft.com/powerquery-m/list-max), [List. standardsapması](https://docs.microsoft.com/powerquery-m/list-standarddeviation), [List. First](https://docs.microsoft.com/powerquery-m/list-first), [List. Last](https://docs.microsoft.com/powerquery-m/list-last)

## <a name="sorting"></a>Sıralama

Değerleri sıralamak için [Table. Sort](https://docs.microsoft.com/powerquery-m/table-sort) kullanın.

## <a name="reducing-rows"></a>Satırları azaltma

Bağımsız tut ve çıkar, aralığı tut (karşılık gelen k işlevleri, yalnızca destekleme sayısı, koşullar değil: [Table. FirstN](https://docs.microsoft.com/powerquery-m/table-firstn), [Table. Skip](https://docs.microsoft.com/powerquery-m/table-skip), [Table. Removefirstn](https://docs.microsoft.com/powerquery-m/table-removefirstn), [Table. Range](https://docs.microsoft.com/powerquery-m/table-range), [Table. Minn](https://docs.microsoft.com/powerquery-m/table-minn), [Table. MAXn](https://docs.microsoft.com/powerquery-m/table-maxn))

## <a name="known-unsupported-functionality"></a>Bilinen desteklenmeyen işlevsellik

Desteklenmeyen işlevler aşağıda verilmiştir. Bu liste ayrıntılı değildir ve değişikliğe tabidir.
* Sütunları birleştir (AddColumn ile elde edilebilir)
* Sütunu Böl
* Sorgu Ekle
* ' İlk satırı üst bilgi olarak kullan ' ve ' üst bilgileri ilk satır olarak kullan '

## <a name="next-steps"></a>Sonraki adımlar
