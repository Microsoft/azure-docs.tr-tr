---
title: Azure Izleyici günlük sorgularındaki yararlı işleçler | Microsoft Docs
description: Azure Izleyici günlük sorgularında farklı senaryolar için kullanılacak ortak işlevler.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: 022a9f638b3a7d8ae4ebeff8062f258ada7a14f8
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932875"
---
# <a name="useful-operators-in-azure-monitor-log-queries"></a>Azure Izleyici günlük sorgularında yararlı işleçler

Aşağıdaki tabloda, Azure Izleyici günlük sorgularında farklı senaryolar için kullanabileceğiniz bazı genel işlevler verilmiştir.

## <a name="useful-operators"></a>Yararlı işleçler

Kategori                                |İlgili analiz Işlevi
----------------------------------------|----------------------------------------
Seçim ve sütun diğer adları            |`project`, `project-away`, `extend`
Geçici tablolar ve sabitler          |`let scalar_alias_name = …;` <br> `let table_alias_name =  …  …  … ;`| 
Karşılaştırma ve dize Işleçleri         |`startswith`, `!startswith`, `has`, `!has` <br> `contains`, `!contains`, `containscs` <br> `hasprefix`, `!hasprefix`, `hassuffix`, `!hassuffix`, `in`, `!in` <br> `matches regex` <br> `==`, `=~`, `!=`, `!~`
Ortak dize işlevleri                 |`strcat()`, `replace()`, `tolower()`, `toupper()`, `substring()`, `strlen()`
Ortak matematik işlevleri                   |`sqrt()`, `abs()` <br> `exp()`, `exp2()`, `exp10()`, `log()`, `log2()`, `log10()`, `pow()` <br> `gamma()`, `gammaln()`
Metin ayrıştırma                            |`extract()`, `extractjson()`, `parse`, `split()`
Çıktıyı sınırlandırma                         |`take`, `limit`, `top`, `sample`
Tarih işlevleri                          |`now()`, `ago()` <br> `datetime()`, `datepart()`, `timespan` <br> `startofday()`, `startofweek()`, `startofmonth()`, `startofyear()` <br> `endofday()`, `endofweek()`, `endofmonth()`, `endofyear()` <br> `dayofweek()`, `dayofmonth()`, `dayofyear()` <br> `getmonth()`, `getyear()`, `weekofyear()`, `monthofyear()`
Gruplandırma ve toplama                |`summarize by` <br> `max()`, `min()`, `count()`, `dcount()`, `avg()`, `sum()` <br> `stddev()`, `countif()`, `dcountif()`, `argmax()`, `argmin()` <br> `percentiles()`, `percentile_array()`
Birleşimler ve birleşimler                        |`join kind=leftouter`, `inner`, `rightouter`, `fullouter`, `leftanti` <br> `union`
Sıralama, sıralama                             |`sort`, `order` 
Dinamik nesne (JSON ve dizi)         |`parsejson()` <br> `makeset()`, `makelist()` <br> `split()`, `arraylength()` <br> `zip()`, `pack()`
Mantıksal işleçler                       |`and`, `or`, `iff(condition, value_t, value_f)` <br> `binary_and()`, `binary_or()`, `binary_not()`, `binary_xor()`
Makine öğrenme                        |`evaluate autocluster`, `basket`, `diffpatterns`, `extractcolumns`


## <a name="next-steps"></a>Sonraki adımlar

- [Azure izleyici 'de yazma günlüğü sorgularıyla](get-started-queries.md)bir derste ilerleyin.
