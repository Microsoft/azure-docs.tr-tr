---
title: Azure Sentinel sorgularını ve etkinliklerini denetleme | Microsoft Docs
description: Bu makalede, Azure Sentinel 'de gerçekleştirilen sorguların ve etkinliklerin nasıl denetleneceği açıklanır.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/03/2021
ms.author: bagol
ms.openlocfilehash: a02be0938b1ab925fb0343351ce1c414cc59c615
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105044847"
---
# <a name="audit-azure-sentinel-queries-and-activities"></a>Azure Sentinel sorgularını ve etkinliklerini denetleme

Bu makalede, Azure Sentinel çalışma alanınızda gerçekleştirilen ve güvenlik Işlemleri (SOC) çalışma alanınızdaki iç ve dış uyumluluk gereksinimleri gibi işlemler için denetim verilerini nasıl görüntüleyebileceğiniz açıklanır.

Azure Sentinel şu erişimi sağlar:

- Uyarı kurallarını Düzenle gibi Azure Sentinel 'de gerçekleştirilen tüm eylemlerle ilgili ayrıntıları sağlayan **AzureActivity** tablosu. **AzureActivity** tablosu, belirli sorgu verilerini günlüğe eklemez. Daha fazla bilgi için bkz. [Azure etkinlik günlükleri Ile denetleme](#auditing-with-azure-activity-logs).

- Azure Sentinel 'ten çalıştırılan sorgular da dahil olmak üzere Log Analytics ' de çalıştırılan sorgularla ilgili ayrıntıları sağlayan **Laquerylogs** tablosu. Daha fazla bilgi için bkz. [LAQueryLogs Ile denetleme](#auditing-with-laquerylogs).

> [!TIP]
> Azure Sentinel, bu makalede açıklanan el ile sorgulara ek olarak, SOC ortamınızdaki etkinlikleri denetlemeye yardımcı olmak için yerleşik bir çalışma kitabı sağlar.
>
> Azure Sentinel **çalışma kitapları** alanında, **çalışma alanı denetim** çalışma kitabını arayın.



## <a name="auditing-with-azure-activity-logs"></a>Azure etkinlik günlükleri ile denetleme

Azure Sentinel 'in denetim günlükleri, **AzureActivity** tablosunun Azure Sentinel çalışma alanınızda gerçekleştirilen tüm eylemleri Içerdiği [Azure etkinlik günlüklerinde](../azure-monitor/essentials/platform-logs-overview.md)tutulur.

Azure Sentinel ile SOC ortamınızda etkinlik denetimi yaparken **AzureActivity** tablosunu kullanabilirsiniz.

**AzureActivity tablosunu sorgulamak için**:

1. Denetim olaylarını AzureActivity adlı **Günlükler** ekranında yeni bir tabloya akışı başlatmak Için [Azure etkinlik](connect-azure-activity.md) veri kaynağını bağlayın.

1. Ardından, diğer tüm tablolar gibi KQL kullanarak verileri sorgulayın.

    **AzureActivity** tablosu, Azure Sentinel dahil olmak üzere birçok hizmetten alınan verileri içerir. Yalnızca Azure Sentinel 'deki verilere filtre uygulamak için sorgunuzu aşağıdaki kodla başlatın:

    ```kql
     AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS"
    ```

    Örneğin, belirli bir analiz kuralını kimin düzenleyen Son Kullanıcı olduğunu öğrenmek için aşağıdaki sorguyu kullanın ( `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` denetlemek istediğiniz kuralın kural kimliğiyle değiştirin):

    ```kusto
    AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS/ALERTRULES/WRITE"
    | where Properties contains "alertRules/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    | project Caller , TimeGenerated , Properties
    ```

**AzureActivities** tablosunu daha fazla incelemek için sorgunuza daha fazla parametre ekleyin ve rapor almanız gerekenleri göz atın. Aşağıdaki bölümlerde, **AzureActivity** tablo verileriyle Denetim yaparken kullanılacak diğer örnek sorgular sağlanmaktadır. 

Daha fazla bilgi için bkz. [Azure etkinlik günlüklerine eklenen Azure Sentinel verileri](#azure-sentinel-data-included-in-azure-activity-logs).

### <a name="find-all-actions-taken-by-a-specific-user-in-the-last-24-hours"></a>Son 24 saat içindeki belirli bir kullanıcı tarafından gerçekleştirilen tüm eylemleri bul

Aşağıdaki **AzureActivity** tablo sorgusu, son 24 saat içinde belirli BIR Azure AD kullanıcısı tarafından gerçekleştirilen tüm eylemleri listeler.

```kql
AzureActivity
| where OperationNameValue contains "SecurityInsights"
| where Caller == "[AzureAD username]"
| where TimeGenerated > ago(1d)
```

### <a name="find-all-delete-operations"></a>Tüm silme işlemlerini bul

Aşağıdaki **AzureActivity** tablo sorgusu, Azure Sentinel çalışma alanınızda gerçekleştirilen tüm silme işlemlerini listeler.

```kql
AzureActivity
| where OperationNameValue contains "SecurityInsights"
| where OperationName contains "Delete"
| where ActivityStatusValue contains "Succeeded"
| project TimeGenerated, Caller, OperationName
``` 


### <a name="azure-sentinel-data-included-in-azure-activity-logs"></a>Azure etkinlik günlüklerine eklenen Azure Sentinel verileri
 
Azure Sentinel 'in denetim günlükleri, [Azure etkinlik günlüklerinde](../azure-monitor/essentials/platform-logs-overview.md)tutulur ve aşağıdaki bilgi türlerini içerir:

|İşlem  |Bilgi türleri  |
|---------|---------|
|**Oluşturulan**     |Uyarı kuralları <br> Örnek açıklama <br>Olay açıklamaları <br>Kayıtlı aramalar<br>Watchlists    <br>Çalışma Kitapları     |
|**Silindi**     |Uyarı kuralları <br>Yer işaretleri <br>Veri bağlayıcıları <br>Olaylar <br>Kayıtlı aramalar <br>Ayarlar <br>Tehdit bilgileri raporları <br>Watchlists      <br>Çalışma Kitapları <br>İş akışı  |
|**Güncellendi**     |  Uyarı kuralları<br>Yer işaretleri <br> Çalışmaların <br> Veri bağlayıcıları <br>Olaylar <br>Olay açıklamaları <br>Tehdit bilgileri raporları <br> Çalışma Kitapları <br>İş akışı       |
|     |         |

Ayrıca, Kullanıcı yetkilendirmelerini ve lisanslarını denetlemek için Azure etkinlik günlüklerini de kullanabilirsiniz. 

Örneğin, aşağıdaki tabloda günlük verilerinin çektiği belirli kaynakla Azure etkinlik günlüklerinde bulunan seçili işlemler listelenmiştir.

|İşlem adı|    Kaynak türü|
|----|----|
|Çalışma kitabı oluştur veya güncelleştir  |Microsoft. Insights/çalışma kitapları|
|Çalışma kitabını Sil    |Microsoft. Insights/çalışma kitapları|
|İş akışını ayarla   |Microsoft. Logic/iş akışları|
|İş akışını Sil    |Microsoft. Logic/iş akışları|
|Kayıtlı arama oluştur    |Microsoft. Operationalınsights/çalışma alanları/Savedaramalar|
|Kayıtlı aramayı Sil    |Microsoft. Operationalınsights/çalışma alanları/Savedaramalar|
|Uyarı kurallarını Güncelleştir |Microsoft. Securityınsights/alertRules|
|Uyarı kurallarını Sil |Microsoft. Securityınsights/alertRules|
|Uyarı kuralı yanıt eylemlerini Güncelleştir |Microsoft. Securityınsights/alertRules/Actions|
|Uyarı kuralı yanıt eylemlerini Sil |Microsoft. Securityınsights/alertRules/Actions|
|Yer imlerini güncelleştirme   |Microsoft. Securityınsights/yer işaretleri|
|Yer imlerini sil   |Microsoft. Securityınsights/yer işaretleri|
|Güncelleştirme çalışmaları   |Microsoft. Securityınsights/durumlar|
|Durum araştırmasını Güncelleştir  |Microsoft. Securityınsights/durumlar/araştırmalar|
|Case açıklamaları oluşturma   |Microsoft. Securityınsights/Cases/Comments|
|Veri bağlayıcılarını güncelleştirme |Microsoft. Securityınsights/veri bağlayıcıları|
|Veri bağlayıcıları silme |Microsoft. Securityınsights/veri bağlayıcıları|
|Ayarları güncelleştirme    |Microsoft. Securityınsights/ayarlar|
| | |

Daha fazla bilgi için bkz. [Azure etkinlik günlüğü olay şeması](../azure-monitor/essentials/activity-log-schema.md).


## <a name="auditing-with-laquerylogs"></a>LAQueryLogs ile denetim

**Laquerylogs** tablosu, Log Analytics ' de çalıştırılan günlük sorguları hakkında ayrıntılar sağlar. Log Analytics Azure Sentinel 'in temel alınan veri deposu olarak kullanıldığından, sisteminizi Azure Sentinel çalışma alanınızda LAQueryLogs verileri toplayacak şekilde yapılandırabilirsiniz.

LAQueryLogs verileri aşağıdakiler gibi bilgiler içerir:

- Sorgular çalıştırıldığında
- Log Analytics sorguları çalıştıran kişiler
- Azure Sentinel gibi Log Analytics sorguları çalıştırmak için hangi araç kullanılmıştır
- Sorgu metinleri kendileri
- Her sorgu çalıştırmasında performans verileri

> [!NOTE]
> - **Laquerylogs** tablosu yalnızca Azure Sentinel 'in Günlükler dikey penceresinde çalıştırılmış sorguları içerir. Zamanlanan analiz kuralları tarafından çalıştırılan sorguları, **araştırma grafı** kullanılarak veya **Azure Sentinel arama** sayfasında bulundurmaz.
> - Sorgunun çalıştırıldığı zaman ve verilerin **Laquerylogs** tablosunda doldurulması arasında kısa bir gecikme olabilir. Denetim verileri için **Laquerylogs** tablosunun sorgulanmasını yaklaşık 5 dakika beklememiz önerilir.
>


**LAQueryLogs tablosunu sorgulamak için**:

1. **Laquerylogs** tablosu, Log Analytics çalışma alanınızda varsayılan olarak etkinleştirilmemiştir. Azure Sentinel 'de Denetim yaparken **laquerylogs** verilerini kullanmak için, önce Log Analytics çalışma alanınızın **Tanılama ayarları** alanında yer alan **laquerylogs** ' u etkinleştirin.

    Daha fazla bilgi için bkz. [Azure izleyici günlüklerinde denetim sorguları](../azure-monitor/logs/query-audit.md).


1. Ardından, diğer tüm tablolar gibi KQL kullanarak verileri sorgulayın.

    Örneğin, aşağıdaki sorgu, son hafta içinde kaç sorgunun gün içinde çalıştırıldığını gösterir:

    ```kql
    LAQueryLogs
    | where TimeGenerated > ago(7d)
    | summarize events_count=count() by bin(TimeGenerated, 1d)
    ```

Aşağıdaki bölümlerde, Azure Sentinel kullanarak SOC ortamınızdaki etkinlikleri denetlerken **Laquerylogs** tablosunda çalıştırılacak daha fazla örnek sorgu gösterilmektedir.

### <a name="the-number-of-queries-run-where-the-response-wasnt-ok"></a>Yanıtın "Tamam" olmadığı yerde çalıştırılan sorgu sayısı

Aşağıdaki **Laquerylogs** tablosu sorgusu, çalışan **200** http yanıtı dışında herhangi bir şeyin alındığı sorgu sayısını gösterir. Örneğin, bu numara çalıştırmayacak olan sorguları içerir.

```kql
LAQueryLogs
| where ResponseCode != 200 
| count 
```

### <a name="show-users-for-cpu-intensive-queries"></a>CPU yoğun sorgulara yönelik kullanıcıları göster

Aşağıdaki **Laquerylogs** tablosu sorgusu, CPU kullanımı en yüksek sorguları çalıştıran kullanıcıları ve sorgu süresinin uzunluğunu temel alarak listeler.

```kql
LAQueryLogs
|summarize arg_max(StatsCPUTimeMs, *) by AADClientId
| extend User = AADEmail, QueryRunTime = StatsCPUTimeMs
| project User, QueryRunTime, QueryText
| order by QueryRunTime desc
```

### <a name="show-users-who-ran-the-most-queries-in-the-past-week"></a>Son hafta içinde en çok sorguyu çalıştıran kullanıcıları göster

Aşağıdaki **Laquerylogs** tablosu sorgusu, geçen hafta içinde en çok sorguyu çalıştıran kullanıcıları listeler.

```kql
LAQueryLogs
| where TimeGenerated > ago(7d)
| summarize events_count=count() by AADEmail
| extend UserPrincipalName = AADEmail, Queries = events_count
| join kind= leftouter (
    SigninLogs)
    on UserPrincipalName
| project UserDisplayName, UserPrincipalName, Queries
| summarize arg_max(Queries, *) by UserPrincipalName
| sort by Queries desc
```

## <a name="configuring-alerts-for-azure-sentinel-activities"></a>Azure Sentinel etkinlikleri için uyarıları yapılandırma

Proaktif uyarılar oluşturmak için Azure Sentinel denetim kaynaklarını kullanmak isteyebilirsiniz.

Örneğin, Azure Sentinel çalışma alanınızda hassas tablolar varsa, bu tabloların her sorgulanışında size bildirimde bulunan aşağıdaki sorguyu kullanın:

```kql
LAQueryLogs
| where QueryText contains "[Name of sensitive table]"
| where TimeGenerated > ago(1d)
| extend User = AADEmail, Query = QueryText
| project User, Query
```


## <a name="next-steps"></a>Sonraki adımlar

Azure Sentinel 'de, SOC ortamınızdaki etkinlikleri denetlemek için **çalışma alanı denetim** çalışma kitabını kullanın.

Daha fazla bilgi için bkz. [öğretici: verilerinizi görselleştirme ve izleme](tutorial-monitor-your-data.md).