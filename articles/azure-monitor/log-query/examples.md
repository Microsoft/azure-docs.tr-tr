---
title: Azure Izleyici günlüğü sorgu örnekleri | Microsoft Docs
description: Azure Izleyici 'de kusto sorgu dilini kullanan günlük sorgularının örnekleri.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/01/2019
ms.openlocfilehash: 8850aef8b5d45f236385551a1455e6fe7b540340
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614453"
---
# <a name="azure-monitor-log-query-examples"></a>Azure Izleyici günlüğü sorgu örnekleri
Bu makalede, Azure Izleyici 'den farklı günlük verisi türlerini almak için [kusto sorgu dilini](/azure/kusto/query/) kullanan çeşitli [sorgu](log-query-overview.md) örnekleri yer almaktadır. Verileri birleştirmek ve analiz etmek için farklı yöntemler kullanılır. bu sayede, kendi gereksinimleriniz için kullanabileceğiniz farklı stratejileri belirlemek için bu örnekleri kullanabilirsiniz.  

Bu örneklerde kullanılan farklı anahtar sözcüklerle ilgili ayrıntılı bilgi için [kusto dil başvurusuna](https://docs.microsoft.com/azure/kusto/query/) bakın. Azure Izleyici 'de yeni başladıysanız [sorgu oluşturma konusunda bir derste](get-started-queries.md) ilerleyin.

## <a name="events"></a>Olaylar

### <a name="search-application-level-events-described-as-cryptographic"></a>"Şifreleme" olarak açıklanan uygulama düzeyi olayları ara
Bu örnek, **olay** tablosunda **EventLog** 'ın _uygulama_ ve **rendereddescription** 'un _şifreleme_içerdiği kayıtlar için arama yapar. Son 24 saat içindeki kayıtları içerir.

```Kusto
Event
| where EventLog == "Application" 
| where TimeGenerated > ago(24h) 
| where RenderedDescription contains "cryptographic"
```

### <a name="search-events-related-to-unmarshaling"></a>Sıralama geri alma ile ilgili olayları ara
_Sıralama geri alma_ile ilgili kayıtlar için **olayları** ve **securityevents** ' i arayın.

```Kusto
search in (Event, SecurityEvent) "unmarshaling"
```

## <a name="heartbeat"></a>Sinyal

### <a name="chart-a-week-over-week-view-of-the-number-of-computers-sending-data"></a>Veri gönderen bilgisayar sayısını haftalık olarak göster

Aşağıdaki örnek, her hafta sinyal gönderen ayrı bilgisayarların sayısını grafikler.

```Kusto
Heartbeat
| where TimeGenerated >= startofweek(ago(21d))
| summarize dcount(Computer) by endofweek(TimeGenerated) | render barchart kind=default
```

### <a name="find-stale-computers"></a>Eski bilgisayarları bul

Aşağıdaki örnek, son gün içinde etkin olan ancak son bir saat içinde sinyal göndermeyen bilgisayarları bulur.

```Kusto
Heartbeat
| where TimeGenerated > ago(1d)
| summarize LastHeartbeat = max(TimeGenerated) by Computer
| where isnotempty(Computer)
| where LastHeartbeat < ago(1h)
```

### <a name="get-the-latest-heartbeat-record-per-computer-ip"></a>Bilgisayar IP 'si başına en son sinyal kaydını al

Bu örnek her bilgisayar IP 'si için son sinyal kaydını döndürür.
```Kusto
Heartbeat
| summarize arg_max(TimeGenerated, *) by ComputerIP
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>Korumalı durum kayıtlarını sinyal kayıtlarıyla Eşleştir

Bu örnek, hem bilgisayar hem de zaman ile eşleşen ilgili koruma durum kayıtlarını ve sinyal kayıtlarını bulur.
Zaman alanı en yakın dakikaya yuvarlanır. Bunu yapmak için çalışma zamanı bölme hesaplamasını kullandık: `round_time=bin(TimeGenerated, 1m)`.

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```

### <a name="server-availability-rate"></a>Sunucu kullanılabilirlik oranı

Sinyal kayıtlarına göre sunucu kullanılabilirlik oranını hesaplayın. Kullanılabilirlik, "saat başına en az 1 sinyal" olarak tanımlanır.
Bu nedenle, bir sunucu 100 saat 98 ile kullanılabilirse, kullanılabilirlik oranı %98.

```Kusto
let start_time=startofday(datetime("2018-03-01"));
let end_time=now();
Heartbeat
| where TimeGenerated > start_time and TimeGenerated < end_time
| summarize heartbeat_per_hour=count() by bin_at(TimeGenerated, 1h, start_time), Computer
| extend available_per_hour=iff(heartbeat_per_hour>0, true, false)
| summarize total_available_hours=countif(available_per_hour==true) by Computer 
| extend total_number_of_buckets=round((end_time-start_time)/1h)+1
| extend availability_rate=total_available_hours*100/total_number_of_buckets
```


## <a name="multiple-data-types"></a>Birden çok veri türü

### <a name="chart-the-record-count-per-table"></a>Kayıt sayısını tablo başına grafik olarak
Aşağıdaki örnek, son beş saatten tüm tabloların tüm kayıtlarını toplar ve her tabloda kaç kayıt olduğunu sayar. Sonuçlar bir timechart içinde gösterilir.

```Kusto
union withsource=sourceTable *
| where TimeGenerated > ago(5h) 
| summarize count() by bin(TimeGenerated,10m), sourceTable
| render timechart
```

### <a name="count-all-logs-collected-over-the-last-hour-by-type"></a>Son saate göre toplanan tüm günlükleri türe göre say
Aşağıdaki örnek, son saat içinde raporlanan her şeyi arar ve her bir tablonun kayıtlarını **türe**göre sayar. Sonuçlar bir çubuk grafiğinde görüntülenir.

```Kusto
search *
| where TimeGenerated > ago(1h) 
| summarize CountOfRecords = count() by Type
| render barchart
```

## <a name="azurediagnostics"></a>AzureDiagnostics

### <a name="count-azure-diagnostics-records-per-category"></a>Kategori başına Azure tanılama kayıtlarını say
Bu örnek, her benzersiz kategori için tüm Azure tanılama kayıtlarını sayar.

```Kusto
AzureDiagnostics 
| where TimeGenerated > ago(1d)
| summarize count() by Category
```

### <a name="get-a-random-record-for-each-unique-category"></a>Her benzersiz kategori için rastgele bir kayıt alın
Bu örnek, her benzersiz kategori için tek bir rastgele Azure tanılama kaydı alır.

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize any(*) by Category
```

### <a name="get-the-latest-record-per-category"></a>Kategori başına en son kaydı al
Bu örnek, her benzersiz kategoride en son Azure tanılama kaydını alır.

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize arg_max(TimeGenerated, *) by Category
```

## <a name="network-monitoring"></a>Ağ izleme

### <a name="computers-with-unhealthy-latency"></a>Sağlıksız gecikme süresi olan bilgisayarlar
Bu örnek, sağlıksız gecikme süresi olan ayrı bilgisayarların bir listesini oluşturur.

```Kusto
NetworkMonitoring 
| where LatencyHealthState <> "Healthy" 
| where Computer != "" 
| distinct Computer
```

## <a name="performance"></a>Performans

### <a name="join-computer-perf-records-to-correlate-memory-and-cpu"></a>Bellek ve CPU ile bağıntılı bilgisayar performans kayıtlarını birleştirin
Bu örnek, belirli bir bilgisayarın **performans** kayıtlarını ilişkilendirir ve ortalama CPU ve maksimum bellek olmak üzere iki zaman grafiği oluşturur.

```Kusto
let StartTime = now()-5d;
let EndTime = now()-4d;
Perf
| where CounterName == "% Processor Time"  
| where TimeGenerated > StartTime and TimeGenerated < EndTime
| project TimeGenerated, Computer, cpu=CounterValue 
| join kind= inner (
   Perf
    | where CounterName == "% Used Memory"  
    | where TimeGenerated > StartTime and TimeGenerated < EndTime
    | project TimeGenerated , Computer, mem=CounterValue 
) on TimeGenerated, Computer
| summarize avgCpu=avg(cpu), maxMem=max(mem) by TimeGenerated bin=30m  
| render timechart
```

### <a name="perf-cpu-utilization-graph-per-computer"></a>Bilgisayar başına performans CPU kullanımı grafiği
Bu örnek, _contoso_ile başlayan bilgisayarların CPU kullanımını hesaplar ve grafiklerini yapar.

```Kusto
Perf
| where TimeGenerated > ago(4h)
| where Computer startswith "Contoso" 
| where CounterName == @"% Processor Time"
| summarize avg(CounterValue) by Computer, bin(TimeGenerated, 15m) 
| render timechart
```

## <a name="protection-status"></a>Koruma durumu

### <a name="computers-with-non-reporting-protection-status-duration"></a>Raporlama dışı koruma durum süresi olan bilgisayarlar
Bu örnekte, _Raporlama değil_ koruma durumuna sahip bilgisayarlar ve bu durumda oldukları süre listelenir.

```Kusto
ProtectionStatus
| where ProtectionStatus == "Not Reporting"
| summarize count(), startNotReporting = min(TimeGenerated), endNotReporting = max(TimeGenerated) by Computer, ProtectionStatusDetails
| join ProtectionStatus on Computer
| summarize lastReporting = max(TimeGenerated), startNotReporting = any(startNotReporting), endNotReporting = any(endNotReporting) by Computer
| extend durationNotReporting = endNotReporting - startNotReporting
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>Korumalı durum kayıtlarını sinyal kayıtlarıyla Eşleştir
Bu örnek, hem bilgisayar hem de zaman ile eşleşen ilgili koruma durumu kayıtlarını ve sinyal kayıtlarını bulur.
Zaman alanı, **bin**kullanılarak en yakın dakikaya yuvarlanır.

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```


## <a name="security-records"></a>Güvenlik kayıtları

### <a name="count-security-events-by-activity-id"></a>Etkinlik KIMLIĞINE göre güvenlik olaylarını say


Bu örnek, **etkinlik** sütununun sabit yapısına bağımlıdır: \<ıd\>-\<Name\>.
**Etkinlik** değerini iki yeni sütuna ayrıştırır ve her bir **ActivityId**'nin oluşma sayısını sayar.

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| project Activity 
| parse Activity with activityID " - " activityDesc
| summarize count() by activityID
```

### <a name="count-security-events-related-to-permissions"></a>İzinlerle ilgili güvenlik olaylarını say
Bu örnek, **etkinlik** sütununda tüm terim _Izinlerini_içeren **securityevent** kayıtlarının sayısını gösterir. Sorgu, son 30 dakika içinde oluşturulan kayıtlar için geçerlidir.

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m)
| summarize EventCount = countif(Activity has "Permissions")
```

### <a name="find-accounts-that-failed-to-log-in-from-computers-with-a-security-detection"></a>Güvenlik algılaması olan bilgisayarlardan oturum açma başarısız hesapları bulma
Bu örnek, bir güvenlik algılamayı tespit ettiğimiz bilgisayarlardan oturum açma başarısız olan hesapları bulur ve sayar.

```Kusto
let detections = toscalar(SecurityDetection
| summarize makeset(Computer));
SecurityEvent
| where Computer in (detections) and EventID == 4624
| summarize count() by Account
```

### <a name="is-my-security-data-available"></a>Güvenlik verilerim kullanılabilir mi?
Veri araştırmasının başlaması genellikle veri kullanılabilirliği denetimi ile başlar. Bu örnek, son 30 dakika içinde **Securityevent** kayıtlarının sayısını gösterir.

```Kusto
SecurityEvent 
| where TimeGenerated  > ago(30m) 
| count
```

### <a name="parse-activity-name-and-id"></a>Ayrıştırma etkinliği adı ve KIMLIĞI
Aşağıdaki iki örnek, **etkinlik** sütununun sabit yapısına dayanır: \<kimliği\>-\<adı\>. İlk örnek, iki yeni sütuna değer atamak için **Parse** işlecini kullanır: **ActivityId** ve **activitydesc**.

```Kusto
SecurityEvent
| take 100
| project Activity 
| parse Activity with activityID " - " activityDesc
```

Bu örnek, ayrı değerlerden oluşan bir dizi oluşturmak için **split** işlecini kullanır
```Kusto
SecurityEvent
| take 100
| project Activity 
| extend activityArr=split(Activity, " - ") 
| project Activity , activityArr, activityId=activityArr[0]
```

### <a name="explicit-credentials-processes"></a>Açık kimlik bilgileri işlemi
Aşağıdaki örnek, geçen hafta açık kimlik bilgilerini kullanan bir işlem Pasta grafiğini gösterir

```Kusto
SecurityEvent
| where TimeGenerated > ago(7d)
// filter by id 4648 ("A logon was attempted using explicit credentials")
| where EventID == 4648
| summarize count() by Process
| render piechart 
```

### <a name="top-running-processes"></a>En iyi çalışan süreçler

Aşağıdaki örnek, son üç gün içinde en yaygın beş işlem için etkinlik zaman satırını gösterir.

```Kusto
// Find all processes that started in the last three days. ID 4688: A new process has been created.
let RunProcesses = 
    SecurityEvent
    | where TimeGenerated > ago(3d)
    | where EventID == "4688";
// Find the 5 processes that were run the most
let Top5Processes =
RunProcesses
| summarize count() by Process
| top 5 by count_;
// Create a time chart of these 5 processes - hour by hour
RunProcesses 
| where Process in (Top5Processes) 
| summarize count() by bin (TimeGenerated, 1h), Process
| render timechart
```


### <a name="find-repeating-failed-login-attempts-by-the-same-account-from-different-ips"></a>Farklı IP 'lerden aynı hesaba sahip başarısız oturum açma girişimlerini bulma

Aşağıdaki örnek, son altı saatte beş ' den fazla farklı IP 'den aynı hesap tarafından başarısız oturum açma girişimlerini bulur.

```Kusto
SecurityEvent 
| where AccountType == "User" and EventID == 4625 and TimeGenerated > ago(6h) 
| summarize IPCount = dcount(IpAddress), makeset(IpAddress)  by Account
| where IPCount > 5
| sort by IPCount desc
```

### <a name="find-user-accounts-that-failed-to-log-in"></a>Oturum açma başarısız Kullanıcı hesaplarını bulma 
Aşağıdaki örnek, son gün içinde beş kereden fazla oturum açma başarısız olan kullanıcı hesaplarını ve en son oturum açma zamanlarını tanımlar.

```Kusto
let timeframe = 1d;
SecurityEvent
| where TimeGenerated > ago(1d)
| where AccountType == 'User' and EventID == 4625 // 4625 - failed log in
| summarize failed_login_attempts=count(), latest_failed_login=arg_max(TimeGenerated, Account) by Account 
| where failed_login_attempts > 5
| project-away Account1
```

**JOIN**ve **Let** deyimlerini kullanarak aynı şüpheli hesapların daha sonra başarıyla oturum açabiliyor olup olmadığını kontrol edebiliyoruz.

```Kusto
let timeframe = 1d;
let suspicious_users = 
    SecurityEvent
    | where TimeGenerated > ago(timeframe)
    | where AccountType == 'User' and EventID == 4625 // 4625 - failed login
    | summarize failed_login_attempts=count(), latest_failed_login=arg_max(TimeGenerated, Account) by Account 
    | where failed_login_attempts > 5
    | project-away Account1;
let suspicious_users_that_later_logged_in = 
    suspicious_users 
    | join kind=innerunique (
        SecurityEvent
        | where TimeGenerated > ago(timeframe)
        | where AccountType == 'User' and EventID == 4624 // 4624 - successful login,
        | summarize latest_successful_login=arg_max(TimeGenerated, Account) by Account
    ) on Account
    | extend was_login_after_failures = iif(latest_successful_login>latest_failed_login, 1, 0)
    | where was_login_after_failures == 1
;
suspicious_users_that_later_logged_in
```

## <a name="usage"></a>Kullanım

### <a name="calculate-the-average-size-of-perf-usage-reports-per-computer"></a>Bilgisayar başına performans kullanımı raporlarının ortalama boyutunu hesapla

Bu örnek, son 3 saat içinde bilgisayar başına performans kullanımı raporlarının ortalama boyutunu hesaplar.
Sonuçlar bir çubuk grafiğinde gösterilir.
```Kusto
Usage 
| where TimeGenerated > ago(3h)
| where DataType == "Perf" 
| where QuantityUnit == "MBytes" 
| summarize avg(Quantity) by Computer
| sort by avg_Quantity desc nulls last
| render barchart
```

### <a name="timechart-latency-percentiles-50-and-95"></a>Timechart gecikme süresi yüzdebirlik değeri 50 ve 95

Bu örnek, son 24 saat içinde, saat bazında bildirilen **Avglatency** 'nin 50. ve 95. yüzdebirlik değeri ' ni hesaplar ve grafiğini hesaplıyor.

```Kusto
Usage
| where TimeGenerated > ago(24h)
| summarize percentiles(AvgLatencyInSeconds, 50, 95) by bin(TimeGenerated, 1h) 
| render timechart
```

### <a name="usage-of-specific-computers-today"></a>Bugün belirli bilgisayarların kullanımı
Bu örnek, _ContosoFile_dizesini içeren bilgisayar adları için son günden **kullanım** verilerini alır. Sonuçlar **TimeGenerated**'a göre sıralanır.

```Kusto
Usage
| where TimeGenerated > ago(1d)
| where  Computer contains "ContosoFile" 
| sort by TimeGenerated desc nulls last
```

## <a name="updates"></a>Güncelleştirmeler

### <a name="computers-still-missing-updates"></a>Hala güncelleştirmeleri eksik olan bilgisayarlar
Bu örnek, birkaç gün önce bir veya daha fazla kritik güncelleştirmenin eksik olduğu ve hala güncelleştirmeleri eksik olan bilgisayarların listesini gösterir.

```Kusto
let ComputersMissingUpdates3DaysAgo = Update
| where TimeGenerated between (ago(30d)..ago(1h))
| where Classification !has "Critical" and UpdateState =~ "Needed"
| summarize makeset(Computer);
Update
| where TimeGenerated > ago(1d)
| where Classification has "Critical" and UpdateState =~ "Needed"
| where Computer in (ComputersMissingUpdates3DaysAgo)
| summarize UniqueUpdatesCount = dcount(Product) by Computer, OSType
```


## <a name="next-steps"></a>Sonraki adımlar

- Dil hakkındaki ayrıntılar için [kusto dil başvurusuna](/azure/kusto/query) bakın.
- [Azure izleyici 'de günlük sorguları yazma konusunda bir dersi](get-started-queries.md)adım adım inceleyin.
