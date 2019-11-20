---
title: Azure Izleyici günlük sorgularındaki tarih saat değerleriyle çalışma | Microsoft Docs
description: Azure Izleyici günlük sorgularında tarih ve saat verileriyle nasıl çalışabileceğinizi açıklar.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 6ff095d674a11d95ed4fd2d008c3e664dd595fef
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72894209"
---
# <a name="working-with-date-time-values-in-azure-monitor-log-queries"></a>Azure Izleyici günlük sorgularındaki tarih saat değerleriyle çalışma

> [!NOTE]
> [Analiz portalını kullanmaya başlama](get-started-portal.md) ve bu dersi tamamlamadan önce [sorguları](get-started-queries.md) kullanmaya başlama işlemini tamamlamanız gerekir.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Bu makalede, Azure Izleyici günlük sorgularında tarih ve saat verileriyle nasıl çalışılacağı açıklanır.


## <a name="date-time-basics"></a>Tarih saat temelleri
Kusto sorgu dili, tarih ve saatlerle ilişkili iki ana veri türüne sahiptir: DateTime ve TimeSpan. Tüm tarihler UTC olarak ifade edilir. Birden çok DateTime biçimi desteklenirken, ıSO8601 biçimi tercih edilir. 

Timespans bir Decimal ve ardından bir zaman birimi tarafından ifade edilir:

|tirme   | zaman birimi    |
|:---|:---|
|TID           | günündeki          |
|h           | saat         |
|m           | dakikaya       |
|s           | İkincisi       |
|SWM          | milisaniy  |
|mikrosaniye ölçeğinde | mikrosaniye ölçeğinde  |
|sayaç        | nanosaniyelik   |

DateTimeS, `todatetime` işleci kullanılarak bir dize atama ile oluşturulabilir. Örneğin, belirli bir zaman diliminde gönderilen VM sinyallerini gözden geçirmek için `between` işlecini kullanarak bir zaman aralığı belirtin.

```Kusto
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

Diğer bir yaygın senaryo, bir tarih saat değeri mevcut ile karşılaştırılıyor. Örneğin, son iki dakika boyunca tüm sinyalleri görmek için, `now` işlecini iki dakikayı temsil eden bir TimeSpan ile birlikte kullanabilirsiniz:

```Kusto
Heartbeat
| where TimeGenerated > now() - 2m
```

Bu işlev için bir kısayol da kullanılabilir:
```Kusto
Heartbeat
| where TimeGenerated > now(-2m)
```

En kısa ve en okunabilir yöntem de `ago` işlecini kullanıyor:
```Kusto
Heartbeat
| where TimeGenerated > ago(2m)
```

Başlangıç ve bitiş zamanını bilmenin yanı sıra başlangıç saatini ve süreyi bildiğinizi varsayalım. Sorguyu aşağıdaki gibi yeniden yazabilirsiniz:

```Kusto
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

## <a name="converting-time-units"></a>Zaman birimlerini dönüştürme
Varsayılan değer dışında bir zaman biriminde bir tarih saat veya TimeSpan değeri ifade etmek isteyebilirsiniz. Örneğin, son 30 dakikadan oluşan hata olaylarını gözden geçiriyorsanız ve olayın ne kadar önce oluştuğunu gösteren hesaplanmış bir sütuna ihtiyacınız varsa:

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

`timeAgo` sütunu şöyle bir değer barındırır: "00:09:31.5118992", yani hh: mm: ss. fffffff olarak biçimlendirilir. Bu değerleri başlangıç zamanından bu yana dakika `numver` biçimlendirmek istiyorsanız, bu değeri "1 dakika" olarak bölün:

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```


## <a name="aggregations-and-bucketing-by-time-intervals"></a>Zaman aralıklarına göre toplamalar ve demetlenmesidir
Diğer bir yaygın senaryo, belirli bir zaman dilimi içinde belirli bir zaman diliminde istatistik alma gereksinimdir. Bu senaryo için bir `bin` işleci, özetleme yan tümcesinin bir parçası olarak kullanılabilir.

Son yarım saat boyunca 5 dakikada bir gerçekleşen olay sayısını almak için aşağıdaki sorguyu kullanın:

```Kusto
Event
| where TimeGenerated > ago(30m)
| summarize events_count=count() by bin(TimeGenerated, 5m) 
```

Bu sorgu aşağıdaki tabloyu üretir:  

|TimeGenerated (UTC)|events_count|
|--|--|
|2018-08-01T09:30:00.000|54|
|2018-08-01T09:35:00.000|41|
|2018-08-01T09:40:00.000|42|
|2018-08-01T09:45:00.000|41|
|2018-08-01T09:50:00.000|41|
|2018-08-01T09:55:00.000|16|

Sonuç demetlerini oluşturmanın başka bir yolu da `startofday`gibi işlevleri kullanmaktır:

```Kusto
Event
| where TimeGenerated > ago(4d)
| summarize events_count=count() by startofday(TimeGenerated) 
```

Bu sorgu aşağıdaki sonuçları üretir:

|timestamp|biriktirme|
|--|--|
|2018-07-28T00:00:00.000|7\.136|
|2018-07-29T00:00:00.000|12.315|
|2018-07-30T00:00:00.000|16.847|
|2018-07-31T00:00:00.000|12.616|
|2018-08-01T00:00:00.000|5\.416|


## <a name="time-zones"></a>Saat dilimleri
Tüm DateTime değerleri UTC 'de gösterildiği için, bu değerleri genellikle yerel saat dilimine dönüştürmek yararlı olur. Örneğin, UTC 'yi PST saatlerine dönüştürmek için bu hesaplamayı kullanın:

```Kusto
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="related-functions"></a>İlgili işlevler

| Kategori | İşlev |
|:---|:---|
| Veri türlerini Dönüştür | [totarihsaat](/azure/kusto/query/todatetimefunction)  [ToTimeSpan](/azure/kusto/query/totimespanfunction)  |
| Değerin boyutunu bin boyutuna yuvarla | [bölme](/azure/kusto/query/binfunction) |
| Belirli bir tarih veya saat alın | [](/azure/kusto/query/agofunction) [hemen](/azure/kusto/query/nowfunction) önce   |
| Değerin bir kısmını al | [datetime_part](/azure/kusto/query/datetime-partfunction) [GetMonth](/azure/kusto/query/getmonthfunction) [monthofyear](/azure/kusto/query/monthofyearfunction) [GetYear](/azure/kusto/query/getyearfunction) [dayofmonth](/azure/kusto/query/dayofmonthfunction) [DayOfWeek](/azure/kusto/query/dayofweekfunction) [DayOfYear](/azure/kusto/query/dayofyearfunction) [WeekOfYear](/azure/kusto/query/weekofyearfunction) |
| Göreli tarih değeri alın  | [endofday](/azure/kusto/query/endofdayfunction) [endofweek](/azure/kusto/query/endofweekfunction) [ENDOFYEAR](/azure/kusto/query/endofmonthfunction) [](/azure/kusto/query/endofyearfunction) [startofday](/azure/kusto/query/startofdayfunction) [startofweek](/azure/kusto/query/startofweekfunction) [STARTOFMONTH](/azure/kusto/query/startofmonthfunction) [STARTOFYEAR](/azure/kusto/query/startofyearfunction) |

## <a name="next-steps"></a>Sonraki adımlar
Azure Izleyici günlük verileriyle [kusto sorgu dilini](/azure/kusto/query/) kullanmaya yönelik diğer derslere bakın:

- [Dize işlemleri](string-operations.md)
- [Toplama işlevleri](aggregations.md)
- [Gelişmiş toplamalar](advanced-aggregations.md)
- [JSON ve veri yapıları](json-data-structures.md)
- [Gelişmiş sorgu yazma](advanced-query-writing.md)
- [Birleştirmeler](joins.md)
- [Grafik](charts.md)