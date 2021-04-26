---
title: Azure 'da standart sütunlar günlük kayıtlarında Izleme | Microsoft Docs
description: Azure Izleyici günlüklerinde birden çok veri türü için ortak olan sütunları açıklar.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/25/2021
ms.openlocfilehash: 5b906bdbd07d59d2acc88f6b30f0db6b6cbc961a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103562255"
---
# <a name="standard-columns-in-azure-monitor-logs"></a>Azure Izleyici günlüklerinde standart sütunlar
Azure Izleyici günlüklerindeki veriler, her biri benzersiz bir sütun kümesine sahip belirli bir veri türüne sahip bir [Log Analytics çalışma alanında veya Application Insights uygulamasında bir kayıt kümesi olarak depolanır](../logs/data-platform-logs.md). Birçok veri türü, birden çok tür genelinde ortak olan standart sütunlara sahip olacaktır. Bu makale, bu sütunları açıklar ve bunları sorgularda nasıl kullanabileceğinizi gösteren örnekler sağlar.

Application Insights içindeki çalışma alanı tabanlı uygulamalar, verilerini bir Log Analytics çalışma alanında depolar ve çalışma alanındaki diğer tablolarla aynı standart sütunları kullanır. Klasik uygulamalar, verilerini ayrı olarak depolar ve bu makalede belirtilen şekilde farklı standart sütunlara sahiptir.

> [!NOTE]
> Standart sütunlardan bazıları Log Analytics şema görünümünde veya IntelliSense 'de gösterilmez ve çıktıda açıkça bir sütun belirtmediğiniz takdirde sorgu sonuçlarında gösterilmez.
> 

## <a name="tenantid"></a>TenantId
**Tenantıd** sütunu, Log Analytics çalışma alanı için çalışma alanı kimliğini barındırır.

## <a name="timegenerated-and-timestamp"></a>TimeGenerated ve zaman damgası
**TimeGenerated** (Log Analytics çalışma alanı) ve **zaman damgası** (Application Insights uygulaması) sütunları, kaydın veri kaynağı tarafından oluşturulduğu tarih ve saati içerir. Daha fazla ayrıntı için bkz. [Azure izleyici 'de günlük verisi alma süresi](../logs/data-ingestion-time.md) .

**TimeGenerated** ve **timestamp** zamana göre filtreleme veya özetleme için kullanılacak ortak bir sütun sağlar. Azure portal bir görünüm veya Pano için zaman aralığı seçtiğinizde, sonuçları filtrelemek için TimeGenerated veya timestamp kullanılır. 

### <a name="examples"></a>Örnekler

Aşağıdaki sorgu, önceki haftanın her günü için oluşturulan hata olaylarının sayısını döndürür.

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```

Aşağıdaki sorgu, önceki hafta içinde her gün için oluşturulan özel durumların sayısını döndürür.

```Kusto
exceptions
| where timestamp between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by timestamp asc 
```

## <a name="_timereceived"></a>\_Timerecelmiş
**\_ Timerectitim** sütunu, Azure bulutundaki Azure izleyici alma noktası tarafından kaydın alındığı tarih ve saati içerir. Bu, veri kaynağı ve bulut arasındaki gecikme sorunlarını belirlemek için yararlı olabilir. Bir örneğin, bir aracıdan alınan verilerle gecikmeye neden olan bir ağ sorunu olabilir. Daha fazla ayrıntı için bkz. [Azure izleyici 'de günlük verisi alma süresi](../logs/data-ingestion-time.md) .

> [!NOTE]
> Zaman **\_ damgası** sütunu her kullanıldığında hesaplama yapılır. Bu işlem kaynak açısından yoğun bir işlemdir. Çok sayıda kaydı filtrelemek için bu uygulamayı kullanmayı daraltın. Bu işlevin kullanılması, daha fazla sorgu yürütme süresine yol açabilir.


Aşağıdaki sorgu, bir aracıdan gelen olay kayıtları için saate göre ortalama gecikme süresini verir. Bu, aracıdan buluta kadar olan süreyi ve kayıt sorgularının günlük sorguları için kullanılabilir olması için toplam süreyi içerir.

```Kusto
Event
| where TimeGenerated > ago(1d) 
| project TimeGenerated, TimeReceived = _TimeReceived, IngestionTime = ingestion_time() 
| extend AgentLatency = toreal(datetime_diff('Millisecond',TimeReceived,TimeGenerated)) / 1000
| extend TotalLatency = toreal(datetime_diff('Millisecond',IngestionTime,TimeGenerated)) / 1000
| summarize avg(AgentLatency), avg(TotalLatency) by bin(TimeGenerated,1hr)
``` 

## <a name="type-and-itemtype"></a>Tür ve ItemType
**Tür** (Log Analytics çalışma alanı) ve **ıtemtype** (Application Insights Application) sütunları, kaydın alındığı tablonun adını, kayıt türü olarak da düşünülebilir. Bu sütun, `search` farklı türlerin kayıtlarını ayırt etmek için işleç kullananlar gibi birden çok tablodan kayıtları birleştiren sorgularda yararlıdır. **$Table** , bazı yerlerde **tür** yerine kullanılabilir.

### <a name="examples"></a>Örnekler
Aşağıdaki sorgu, son saate göre toplanan kayıt sayısını döndürür.

```Kusto
search * 
| where TimeGenerated > ago(1h)
| summarize count() by Type

```
## <a name="_itemid"></a>\_ID
**\_ ItemId** sütunu, kayıt için benzersiz bir tanımlayıcı tutar.


## <a name="_resourceid"></a>\_ResourceId
**\_ RESOURCEID** sütunu, kaydın ilişkilendirildiği kaynak için benzersiz bir tanımlayıcı tutar. Bu, sorgunuzu yalnızca belirli bir kaynaktaki kayıtlarla birleştirmek veya ilgili verileri birden çok tablo genelinde birleştirmek için kullanabileceğiniz standart bir sütun sağlar.

Azure kaynakları için **_ResourceId** değeri [Azure kaynak kimliği URL 'sidir](../../azure-resource-manager/templates/template-functions-resource.md). Sütun, [Azure Arc](../../azure-arc/overview.md) kaynakları dahil olmak üzere Azure kaynaklarıyla veya alma SıRASıNDA kaynak kimliğini belirten özel günlüklere sınırlıdır.

> [!NOTE]
> Bazı veri türlerinde zaten Azure Kaynak KIMLIĞI veya abonelik KIMLIĞI gibi en az parçalar içeren alanlar var. Bu alanlar geriye dönük uyumluluk için tutulurken, daha tutarlı olacağı için _ResourceId çapraz bağıntı gerçekleştirmek üzere kullanılması önerilir.

### <a name="examples"></a>Örnekler
Aşağıdaki sorgu, her bilgisayar için performans ve olay verilerini birleştirir. KIMLIĞI _101_ olan tüm olayları ve %50 üzerinde işlemci kullanımını gösterir.

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

Aşağıdaki sorgu, _AzureActivity_ kayıtlarını _securityevent_ kayıtlarıyla birleştirir. Bu makinede oturum açmış kullanıcılarla tüm etkinlik işlemleri gösterilir.

```Kusto
AzureActivity 
| where  
    OperationName in ("Restart Virtual Machine", "Create or Update Virtual Machine", "Delete Virtual Machine")  
    and ActivityStatus == "Succeeded"  
| join kind= leftouter (    
   SecurityEvent 
   | where EventID == 4624  
   | summarize LoggedOnAccounts = makeset(Account) by _ResourceId 
) on _ResourceId  
```

Aşağıdaki sorgu **_ResourceId** ayrıştırır ve Azure Kaynak grubu başına faturalanan veri birimlerini toplar.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by resourceGroup | sort by Bytes nulls last 
```

`union withsource = tt *`Veri türlerindeki taramaların yürütülmesi pahalı olduğundan bu sorguları dikkatli bir şekilde kullanın.

\_ResourceID sütununu ayrıştırarak SubscriptionID sütununun ayıklanması her zaman daha etkilidir \_ .

## <a name="_subscriptionid"></a>\_SubscriptionID
**\_ SubscriptionID** sütunu, kaydın ILIŞKILENDIRILDIĞI kaynağın abonelik kimliğini içerir. Bu, sorgunuzu yalnızca belirli bir aboneliğin kayıtlarına veya farklı aboneliklerin karşılaştırılacağı şekilde kapsamını atamak için kullanabileceğiniz standart bir sütun sağlar.

Azure kaynakları için **__SubscriptionId** değeri, [Azure kaynak kimliği URL](../../azure-resource-manager/templates/template-functions-resource.md)'sinin abonelik bölümüdür. Sütun, [Azure Arc](../../azure-arc/overview.md) kaynakları dahil olmak üzere Azure kaynaklarıyla veya alma SıRASıNDA kaynak kimliğini belirten özel günlüklere sınırlıdır.

> [!NOTE]
> Bazı veri türlerinde Azure abonelik KIMLIĞI içeren alanlar zaten var. Bu alanlar geriye dönük uyumluluk için tutulurken, \_ daha tutarlı olacağı için SubscriptionID sütununun, çapraz bağıntı gerçekleştirmek üzere kullanılması önerilir.
### <a name="examples"></a>Örnekler
Aşağıdaki sorgu belirli bir aboneliğin bilgisayarları için performans verilerini inceler. 

```Kusto
Perf 
| where TimeGenerated > ago(24h) and CounterName == "memoryAllocatableBytes"
| where _SubscriptionId == "57366bcb3-7fde-4caf-8629-41dc15e3b352"
| summarize avgMemoryAllocatableBytes = avg(CounterValue) by Computer
```

Aşağıdaki sorgu **_ResourceId** ayrıştırır ve Azure aboneliği başına faturalanan veri birimlerini toplar.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by _SubscriptionId | sort by Bytes nulls last 
```

`union withsource = tt *`Veri türlerindeki taramaların yürütülmesi pahalı olduğundan bu sorguları dikkatli bir şekilde kullanın.


## <a name="_isbillable"></a>\_Miktarbirimi
**\_ Ifaturalanabilir** sütun, alınan verilerin faturalandırılabilir olup olmadığını belirtir. **\_ Ifaturalanabilir** değerine eşit olan veriler `false` ücretsiz olarak toplanır ve Azure hesabınıza faturalandırılmaz.

### <a name="examples"></a>Örnekler
Faturalanan veri türlerini gönderen bilgisayarların listesini almak için aşağıdaki sorguyu kullanın:

> [!NOTE]
> `union withsource = tt *`Veri türlerinde taramaların yürütülmesi pahalı olan sorguları gelişigüzel şekilde kullanın. 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

Bu, faturalanan veri türlerini Gönderen saat başına bilgisayar sayısını döndürmek için Genişletilebilir:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="_billedsize"></a>\_BilledSize
**\_ Billedsize** sütunu, **\_ ısfaturalandırılabilir** değeri true ise Azure hesabınıza faturalandırılacak verilerin bayt cinsinden boyutunu belirtir.


### <a name="examples"></a>Örnekler
Bilgisayar başına alınan faturalandırılabilir olayların boyutunu görmek için `_BilledSize` bayt cinsinden boyutu sağlayan sütunu kullanın:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

Abonelik başına alınan faturalandırılabilir olayların boyutunu görmek için aşağıdaki sorguyu kullanın:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  _SubscriptionId | sort by Bytes nulls last 
```

Kaynak grubu başına alınan faturalandırılabilir olayların boyutunu görmek için aşağıdaki sorguyu kullanın:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/resourcegroups/" ResourceGroupName "/" *
| summarize Bytes=sum(_BilledSize) by  _SubscriptionId, ResourceGroupName | sort by Bytes nulls last 

```


Bilgisayar başına alınan olayların sayısını görmek için aşağıdaki sorguyu kullanın:

```Kusto
union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last
```

Bilgisayar başına alınan faturalandırılabilir olayların sayısını görmek için aşağıdaki sorguyu kullanın: 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last
```

Belirli bir bilgisayardan faturalanabilir veri türleri sayısını görmek için aşağıdaki sorguyu kullanın:

```Kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last 
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure izleyici günlük verilerinin nasıl depolandığı](./log-query-overview.md)hakkında daha fazla bilgi edinin.
- [Günlük sorgularını yazarken](./get-started-queries.md)bir ders alın.
- [Günlük sorgularında tabloları birleştirme](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#joins)konusunda bir ders alın.
