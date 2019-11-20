---
title: Azure Izleyici 'de gelişmiş sorgular | Microsoft Docs
description: Bu makalede, Azure Izleyici 'de sorgu yazmak için analiz portalını kullanmaya yönelik bir öğretici sunulmaktadır.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/15/2018
ms.openlocfilehash: 8895224bef037c8c3f8b28a6085359837478d924
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72894499"
---
# <a name="writing-advanced-queries-in-azure-monitor"></a>Azure Izleyici 'de gelişmiş sorgular yazma

> [!NOTE]
> [Azure izleyici Log Analytics kullanmaya başlama](get-started-portal.md) ve bu dersi tamamlamadan önce [sorguları](get-started-queries.md) kullanmaya başlama işlemini tamamlamanız gerekir.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

## <a name="reusing-code-with-let"></a>Kodu izin ile yeniden kullanma
Bir değişkene sonuçlar atamak ve bu sorguya daha sonra başvurmak için `let` kullanın:

```Kusto
// get all events that have level 2 (indicates warning level)
let warning_events=
Event
| where EventLevel == 2;
// count the number of warning events per computer
warning_events
| summarize count() by Computer 
```

Değişkenlere sabit değerler de atayabilirsiniz. Bu, sorguyu her çalıştırdığınızda değiştirmeniz gereken alanların parametrelerini ayarlamak için bir yöntemi destekler. Bu parametreleri gerektiği şekilde değiştirin. Örneğin, boş disk alanını ve boş belleği (yüzdebirlik değeri), belirli bir zaman penceresinde hesaplamak için:

```Kusto
let startDate = datetime(2018-08-01T12:55:02);
let endDate = datetime(2018-08-02T13:21:35);
let FreeDiskSpace =
Perf
| where TimeGenerated between (startDate .. endDate)
| where ObjectName=="Logical Disk" and CounterName=="Free Megabytes"
| summarize percentiles(CounterValue, 50, 75, 90, 99);
let FreeMemory =
Perf
| where TimeGenerated between (startDate .. endDate)
| where ObjectName=="Memory" and CounterName=="Available MBytes Memory"
| summarize percentiles(CounterValue, 50, 75, 90, 99);
union FreeDiskSpace, FreeMemory
```

Bu, sorguyu bir sonraki çalıştırışınızda bitiş zamanının başlangıcını değiştirmeyi kolaylaştırır.

### <a name="local-functions-and-parameters"></a>Yerel işlevler ve parametreler
Aynı Sorguda kullanılabilecek işlevler oluşturmak için `let` deyimlerini kullanın. Örneğin, bir DateTime alanını alan (UTC biçiminde) bir işlev tanımlayın ve bunu standart ABD biçimine dönüştürür. 

```Kusto
let utc_to_us_date_format = (t:datetime)
{
  strcat(getmonth(t), "/", dayofmonth(t),"/", getyear(t), " ",
  bin((t-1h)%12h+1h,1s), iff(t%24h<12h, "AM", "PM"))
};
Event 
| where TimeGenerated > ago(1h) 
| extend USTimeGenerated = utc_to_us_date_format(TimeGenerated)
| project TimeGenerated, USTimeGenerated, Source, Computer, EventLevel, EventData 
```

## <a name="print"></a>yazdırdığımda
`print`, bir hesaplamanın sonucunu gösteren tek bir sütun ve tek bir satır içeren bir tablo döndürür. Bu, genellikle basit bir hesaplamanın gerektiği durumlarda kullanılır. Örneğin, PST 'deki geçerli saati bulmak ve EST ile bir sütun eklemek için:

```Kusto
print nowPst = now()-8h
| extend nowEst = nowPst+3h
```

## <a name="datatable"></a>DataRow
`datatable` bir veri kümesi tanımlamanızı sağlar. Bir şema ve bir değerler kümesi girip tabloyu diğer herhangi bir sorgu öğesine yönelolursunuz. Örneğin, bir RAM kullanımı tablosu oluşturmak ve Ortalama değerlerini saat başına hesaplamak için:

```Kusto
datatable (TimeGenerated: datetime, usage_percent: double)
[
  "2018-06-02T15:15:46.3418323Z", 15.5,
  "2018-06-02T15:45:43.1561235Z", 20.2,
  "2018-06-02T16:16:49.2354895Z", 17.3,
  "2018-06-02T16:46:44.9813459Z", 45.7,
  "2018-06-02T17:15:41.7895423Z", 10.9,
  "2018-06-02T17:44:23.9813459Z", 24.7,
  "2018-06-02T18:14:59.7283023Z", 22.3,
  "2018-06-02T18:45:12.1895483Z", 25.4
]
| summarize avg(usage_percent) by bin(TimeGenerated, 1h)
```

Bir arama tablosu oluştururken DataTable yapıları de çok yararlı olur. Örneğin, _securityevent_ tablosundan olay kimlikleri gibi tablo verilerini başka bir yerde listelenen olay türlerine eşlemek için, `datatable` kullanarak olay türleriyle bir arama tablosu oluşturun ve bu DataTable 'ı _securityevent_ verileriyle birleştirin:

```Kusto
let eventCodes = datatable (EventID: int, EventType:string)
[
    4625, "Account activity",
    4688, "Process action",
    4634, "Account activity",
    4672, "Access",
    4624, "Account activity",
    4799, "Access management",
    4798, "Access management",
    5059, "Key operation",
    4648, "A logon was attempted using explicit credentials",
    4768, "Access management",
    4662, "Other",
    8002, "Process action",
    4904, "Security event management",
    4905, "Security event management",
];
SecurityEvent
| where TimeGenerated > ago(1h) 
| join kind=leftouter (
  eventCodes
) on EventID
| project TimeGenerated, Account, AccountType, Computer, EventType
```

## <a name="next-steps"></a>Sonraki adımlar
Azure Izleyici günlük verileriyle [kusto sorgu dilini](/azure/kusto/query/) kullanmaya yönelik diğer derslere bakın:

- [Dize işlemleri](string-operations.md)
- [Tarih ve saat işlemleri](datetime-operations.md)
- [Toplama işlevleri](aggregations.md)
- [Gelişmiş toplamalar](advanced-aggregations.md)
- [JSON ve veri yapıları](json-data-structures.md)
- [Birleştirmeler](joins.md)
- [Grafik](charts.md)
