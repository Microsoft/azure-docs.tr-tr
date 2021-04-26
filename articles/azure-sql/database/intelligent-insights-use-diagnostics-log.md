---
title: Akıllı İçgörüler performans tanılama günlüğü
description: Akıllı İçgörüler Azure SQL veritabanı ve Azure SQL yönetilen örnek performans sorunları için tanılama günlüğü sağlar
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 06/12/2020
ms.openlocfilehash: b03c21eea18c966616154b5cfc5df5d8924fd335
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100589317"
---
# <a name="use-the-intelligent-insights-performance-diagnostics-log-of-azure-sql-database-and-azure-sql-managed-instance-performance-issues"></a>Azure SQL veritabanı 'nın Akıllı İçgörüler Performans Tanılama günlüğünü ve Azure SQL yönetilen örnek performans sorunlarını kullanın
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Bu sayfa, Azure SQL veritabanı 'nın [akıllı içgörüler](intelligent-insights-overview.md) ve Azure SQL yönetilen örnek performans sorunları, biçimi ve özel geliştirme gereksinimleriniz için içerdiği veriler tarafından oluşturulan Performans Tanılama günlüğünü kullanma hakkında bilgi sağlar. Bu tanılama günlüğünü, özel DevOps uyarısı ve raporlama özellikleri için [Azure izleyici günlüklerine](../../azure-monitor/insights/azure-sql.md), [Azure Event Hubs](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs), [Azure depolama](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#stream-into-azure-storage)'ya veya üçüncü taraf bir çözüme gönderebilirsiniz.

> [!NOTE]
> Akıllı Öngörüler şu bölgelerde kullanılamayan bir önizleme özelliğidir: Batı Avrupa, Kuzey Avrupa, Batı ABD 1 ve Doğu ABD 1.

## <a name="log-header"></a>Günlük üst bilgisi

Tanılama günlüğü Akıllı İçgörüler bulguları çıkarmak için JSON standart biçimini kullanır. Akıllı İçgörüler günlüğüne erişmek için tam kategori özelliği, "Sqlinsıghts" sabit değeridir.

Günlüğün üst bilgisi ortaktır ve bir girdinin oluşturulduğu zamanı gösteren zaman damgasından (TimeGenerated) oluşur. Ayrıca, girişin ilişkili olduğu belirli bir veritabanına başvuran bir kaynak KIMLIĞI (RESOURCEID) içerir. Kategori (kategori), düzey (düzey) ve işlem adı (OperationName), değerleri değişmeyen sabit özelliklerdir. Günlük girişinin bilgilendirici olduğunu ve Akıllı İçgörüler (Sqlinsıghts) geldiğini gösterir.

```json
"TimeGenerated" : "2017-9-25 11:00:00", // time stamp of the log entry
"ResourceId" : "database identifier", // value points to a database resource
"Category": "SQLInsights", // fixed property
"Level" : "Informational", // fixed property
"OperationName" : "Insight", // fixed property
```

## <a name="issue-id-and-database-affected"></a>Sorun KIMLIĞI ve veritabanı etkilendi

Sorun tanımlama özelliği (issueId_d), çözümlenene kadar performans sorunlarını benzersiz bir şekilde izlemenin bir yolunu sağlar. Aynı sorunun günlük raporlama durumundaki birden çok olay kaydı aynı sorun KIMLIĞINI paylaşır.

Tanılama günlüğü, sorun KIMLIĞIYLE birlikte, tanılama günlüğünde bildirilen bir sorunla ilgili belirli bir olayın başlangıç (intervalStartTime_t) ve bitiş (intervalEndTme_t) zaman damgalarını raporlar.

Elastik havuz (elasticPoolName_s) özelliği, hangi elastik havuzun bir sorun olduğunu gösterir. Veritabanı elastik havuzun bir parçası değilse, bu özelliğin değeri yoktur. Bir sorunun algılandığı veritabanı, veritabanı adı (databaseName_s) özelliğinde açıklanmamıştır.

```json
"intervalStartTime_t": "2017-9-25 11:00", // start of the issue reported time stamp
"intervalEndTme_t":"2017-9-25 12:00", // end of the issue reported time stamp
"elasticPoolName_s" : "", // resource elastic pool (if applicable)
"databaseName_s" : "db_name", // database name
"issueId_d" : 1525, // unique ID of the issue detected
"status_s" : "Active" // status of the issue – possible values: "Active", "Verifying", and "Complete"
```

## <a name="detected-issues"></a>Algılanan sorunlar

Akıllı İçgörüler performans günlüğünün sonraki bölümü, yerleşik yapay zeka aracılığıyla algılanan performans sorunlarını içerir. Algılamalar, JSON tanılama günlüğü içindeki özelliklerde duyurulmuştur. Bu algılamalar bir sorun kategorisinden, sorunun etkilerine, etkilenen sorgulara ve ölçülerden oluşur. Algılamalar özellikleri, algılanan birden fazla performans sorunu içerebilir.

Algılanan performans sorunları aşağıdaki algılama özelliği yapısıyla raporlanır:

```json
"detections_s" : [{
"impact" : 1 to 3, // impact of the issue detected, possible values 1-3 (1 low, 2 moderate, 3 high impact)
"category" : "Detectable performance pattern", // performance issue detected, see the table
"details": <Details outputted> // details of an issue (see the table)
}]
```

Algılanabilir performans desenleri ve tanılama günlüğüne Çıktılanan Ayrıntılar aşağıdaki tabloda verilmiştir.

### <a name="detection-category"></a>Algılama kategorisi

Category (kategori) özelliği, algılanabilir performans desenlerinin kategorisini açıklar. Algılanabilir performans desenlerinin tüm olası kategorileri için aşağıdaki tabloya bakın. Daha fazla bilgi için bkz. [akıllı içgörüler veritabanı performans sorunlarını giderme](intelligent-insights-troubleshoot-performance.md).

Algılanan performans sorununa bağlı olarak, tanılama günlük dosyasında oluşan ayrıntılar farklı şekilde farklılık gösterir.

| Algılanabilir performans desenleri | Ayrıntılar çıktıladı |
| :------------------- | ------------------- |
| Kaynak sınırlarına ulaşma | <li>Etkilenen kaynaklar</li><li>Sorgu karmaları</li><li>Kaynak tüketim yüzdesi</li> |
| İş yükü artışı | <li>Yürütmesi arttığı sorgu sayısı</li><li>İş yükü artışına en büyük katkısıyla sorguların sorgu karmaları</li> |
| Bellek Baskısı | <li>Bellek memuru</li> |
| Kilitleme | <li>Etkilenen sorgu karmaları</li><li>Sorgu karmalarını engelleme</li> |
| Artan MAXDOP | <li>Sorgu karmaları</li><li>CXP bekleme süreleri</li><li>Bekleme süreleri</li> |
| Pagemandal çekişmesi | <li>Çekişmeye neden olan sorguların karmalarını sorgulama</li> |
| Eksik dizin | <li>Sorgu karmaları</li> |
| Yeni Sorgu | <li>Yeni sorguların sorgu karması</li> |
| Olağan dışı bekleme Istatistiği | <li>Olağan dışı bekleme türleri</li><li>Sorgu karmaları</li><li>Sorgu bekleme süreleri</li> |
| TempDB çekişmesi | <li>Çekişmeye neden olan sorguların karmalarını sorgulama</li><li>Genel veritabanı pagemanatısyonu bekleme süresi [%] ile sorgulama</li> |
| Elastik havuz DTU eksik | <li>Elastik havuz</li><li>En yüksek DTU kullanan veritabanı</li><li>Üst tüketici tarafından kullanılan havuz DTU yüzdesi</li> |
| Gerileme planı | <li>Sorgu karmaları</li><li>İyi plan kimlikleri</li><li>Hatalı plan kimlikleri</li> |
| Database-Scoped yapılandırma değeri değişikliği | <li>Veritabanı kapsamlı yapılandırma değişiklikleri varsayılan değerlerle karşılaştırılır</li> |
| Yavaş Istemci | <li>Sorgu karmaları</li><li>Bekleme süreleri</li> |
| Fiyatlandırma Katmanı düşürme | <li>Metin bildirimi</li> |

### <a name="impact"></a>Etki

Etki (etki) özelliği, algılanan bir davranışın bir veritabanının sahip olduğu soruna ne kadar katkıda bulunduğunu açıklar. 1 ile 3 arasında bir değer, en yüksek katkı olarak 3, orta ve en düşük katkı olarak 1 ' i etkiler. Özel gereksinimlerinize bağlı olarak, etki değeri özel uyarı otomasyonu için giriş olarak kullanılabilir. Etkilenen Özellik sorguları (Querykarmaları), belirli bir algılamanın etkilediği sorgu karmalarının bir listesini sağlar.

### <a name="impacted-queries"></a>Etkilenen sorgular

Akıllı İçgörüler günlüğünün sonraki bölümü, algılanan performans sorunlarından etkilenen belirli sorgular hakkında bilgiler sağlar. Bu bilgiler, impact_s özelliğine katıştırılmış bir nesne dizisi olarak duyurulmuştur. Impact özelliği varlıklardan ve ölçülerden oluşur. Varlıklar belirli bir sorguya başvurur (tür: sorgu). Benzersiz sorgu karması değer (değer) özelliği altında duyurulmuştur. Ayrıca, duyurulan her bir sorgu, algılanan bir performans sorununu gösteren bir ölçüm ve bir değer izler.

Aşağıdaki günlük örneğinde, 0x9102EXZ4 karmasından oluşan sorgu, daha fazla yürütme süresine sahip olacak şekilde algılandı (ölçüm: DurationIncreaseSeconds). 110 saniyelik değeri, bu belirli sorgunun yürütülmesi için 110 saniye daha uzun sürdüğünü gösterir. Birden çok sorgu algılanabileceğinden, bu günlük bölümünde birden çok sorgu girişi bulunabilir.

```json
"impact" : [{
"entity" : {
"Type" : "Query", // type of entity - query
"Value" : "query hash value", // for example "0x9102EXZ4" query hash value },
"Metric" : "DurationIncreaseSeconds", // measured metric and the measurement unit (in this case seconds)
"Value" : 110 // value of the measured metric (in this case seconds)
}]
```

### <a name="metrics"></a>Ölçümler

Bildirilen her ölçüm için ölçü birimi, ölçüm (ölçüm) özelliği altında, olası saniye, sayı ve yüzde değerleri ile sağlanır. Ölçülen ölçüm değeri değer (değer) özelliğinde raporlanır.

DurationIncreaseSeconds özelliği, ölçü birimini saniye cinsinden sağlar. CriticalHandle değeri bir hata sayısını temsil eden bir sayıdır.

```json
"metric" : "DurationIncreaseSeconds", // issue metric type – possible values: DurationIncreaseSeconds, CriticalErrorCount, WaitingSeconds
"value" : 102 // value of the measured metric (in this case seconds)
```

## <a name="root-cause-analysis-and-improvement-recommendations"></a>Kök neden analizi ve geliştirme önerileri

Akıllı İçgörüler performans günlüğünün son kısmı, belirlenen performans düşüşü sorununa yönelik otomatik kök neden analizine aittir. Bu bilgiler, kök neden analizi (rootCauseAnalysis_s) özelliğinde insan kullanımı kolay bir şekilde görünür. Geliştirme önerileri, mümkün olduğunda günlüğe dahildir.

```json
// example of reported root cause analysis of the detected performance issue, in a human-readable format

"rootCauseAnalysis_s" : "High data IO caused performance to degrade. It seems that this database is missing some indexes that could help."
```

Özel DevOps uyarı ve raporlama özellikleri için [Azure izleyici günlükleri]( https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql) veya bir üçüncü taraf çözümü ile akıllı içgörüler performans günlüğünü kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Akıllı içgörüler](intelligent-insights-overview.md) kavramları hakkında bilgi edinin.
- [Akıllı içgörüler performans sorunlarını giderme](intelligent-insights-troubleshoot-performance.md)hakkında bilgi edinin.
- [Azure SQL Analytics kullanarak performans sorunlarını nasıl izleyeceğinizi](../../azure-monitor/insights/azure-sql.md)öğrenin.
- [Azure kaynaklarınızdan günlük verilerini nasıl toplayacağınızı ve](../../azure-monitor/essentials/platform-logs-overview.md)kullanacağınızı öğrenin.