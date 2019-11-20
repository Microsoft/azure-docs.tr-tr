---
title: Azure Izleyici günlük sorgularıyla toplamalar | Microsoft Docs
description: Verilerinizi analiz etmenin yararlı yollarını sunan Azure Izleyici günlük sorgularının toplama işlevlerini açıklar.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 86b84e76b4716c1fddda23a6d52c65c0700c5663
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900421"
---
# <a name="aggregations-in-azure-monitor-log-queries"></a>Azure Izleyici günlük sorgularındaki toplamalar

> [!NOTE]
> [Analiz portalını kullanmaya başlama](get-started-portal.md) ve bu dersi tamamlamadan önce [sorguları](get-started-queries.md) kullanmaya başlama işlemini tamamlamanız gerekir.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Bu makalede, verilerinizi analiz etmenin yararlı yollarını sunan Azure Izleyici günlük sorgularının toplama işlevleri açıklanmaktadır. Bu işlevler, giriş tablosunun toplanmış sonuçlarını içeren bir tablo üreten `summarize` işleciyle çalışır.

## <a name="counts"></a>Kapsam

### <a name="count"></a>count
Herhangi bir filtre uygulandıktan sonra sonuç kümesindeki satır sayısını say. Aşağıdaki örnek, son 30 dakikadan itibaren _performans_ tablosundaki toplam satır sayısını döndürür. Sonuç, özel bir ad atamadıkça *count_* adlı bir sütunda döndürülür:


```Kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize count()
```

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize num_of_records=count() 
```

Zaman içinde bir eğilim görmek için bir timechart görselleştirmesi yararlı olabilir:

```Kusto
Perf 
| where TimeGenerated > ago(30m) 
| summarize count() by bin(TimeGenerated, 5m)
| render timechart
```

Bu örnekteki çıktı, performans kayıt sayısı eğilim çizgisini 5 dakika olarak gösterir:

![Sayı eğilimi](media/aggregations/count-trend.png)


### <a name="dcount-dcountif"></a>DCount, değersay
Belirli bir sütundaki farklı değerleri saymak için `dcount` ve `dcountif` kullanın. Aşağıdaki sorgu, son bir saat içinde kaç farklı bilgisayarın sinyal gönderdiğini değerlendirir:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcount(Computer)
```

Yalnızca sinyal gönderen Linux bilgisayarları saymak için `dcountif`kullanın:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcountif(Computer, OSType=="Linux")
```

### <a name="evaluating-subgroups"></a>Alt gruplar değerlendiriliyor
Verilerinizde alt gruplar üzerinde bir sayı veya diğer toplamalar gerçekleştirmek için `by` anahtar sözcüğünü kullanın. Örneğin, her ülkede/bölgede sinyal gönderen ayrı Linux bilgisayar sayısını saymak için:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry
```

|RemoteIPCountry  | distinct_computers  |
------------------|---------------------|
|Birleşik Devletler    | 19                  |
|Kanada           | 3                   |
|İrlanda          | 0                   |
|Birleşik Krallık   | 0                   |
|Hollanda      | 2                   |


Verilerinizin daha küçük alt grupları çözümlemek için `by` bölümüne ek sütun adları ekleyin. Örneğin, her bir ülke/bölgeden her OSType için ayrı bilgisayarları saymak isteyebilirsiniz:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry, OSType
```

## <a name="percentiles-and-variance"></a>Yüzdebirlik değeri ve varyans
Sayısal değerleri değerlendirirken, yaygın bir uygulama `summarize avg(expression)`kullanarak bunları ortalama olarak kullanmaktır. Ortalamalar yalnızca birkaç durumu niteleyen Extreme değerlerinden etkilenir. Bu sorunu gidermek için `median` veya `variance`gibi daha az hassas işlevler kullanabilirsiniz.

### <a name="percentile"></a>Özelliğindeki
Ortanca değerini bulmak için, `percentile` işlevini bir değerle kullanın ve bu yüzdebirlik değerini belirtin:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 50) by Computer
```

Ayrıca, her biri için toplanan bir sonuç almak için farklı yüzdebirlik değeri belirtebilirsiniz:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 25, 50, 75, 90) by Computer
```

Bu, bazı bilgisayar CPU 'ların benzer ortanca değerleri olduğunu gösterebilir, ancak bazıları ortanca etrafında kararlı olmakla kalmaz, diğer bilgisayarlar ani artışlar yaştıkları anlamına gelir.

### <a name="variance"></a>Denetlemesini
Bir değerin varyansını doğrudan değerlendirmek için standart sapma ve varyans yöntemlerini kullanın:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), variance(CounterValue) by Computer
```

CPU kullanımının kararlılığını çözümlemenin iyi bir yolu, STDSAPMA değerini ortanca hesaplamayla birleştirmekte.

```Kusto
Perf
| where TimeGenerated > ago(130m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), percentiles(CounterValue, 50) by Computer
```

Azure Izleyici günlük verileriyle [kusto sorgu dilini](/azure/kusto/query/) kullanmaya yönelik diğer derslere bakın:

- [Dize işlemleri](string-operations.md)
- [Tarih ve saat işlemleri](datetime-operations.md)
- [Gelişmiş toplamalar](advanced-aggregations.md)
- [JSON ve veri yapıları](json-data-structures.md)
- [Gelişmiş sorgu yazma](advanced-query-writing.md)
- [Birleştirmeler](joins.md)
- [Grafik](charts.md)
