---
title: Azure Izleyici 'de günlük sorgularına genel bakış | Microsoft Docs
description: Günlük sorgularıyla ilgili yaygın soruların yanıtlarını verir ve bunları kullanmaya başlamanızı sağlar.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/19/2019
ms.openlocfilehash: 08af0ff3121d6721c9f5c1457655c345f50f91e0
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900265"
---
# <a name="overview-of-log-queries-in-azure-monitor"></a>Azure Izleyici 'de günlük sorgularına genel bakış
Günlük sorguları, [Azure Izleyici günlüklerinde](../platform/data-platform-logs.md)toplanan verilerin değerini tamamen kullanmanıza yardımcı olur. Güçlü bir sorgu dili, birden çok tablodan veri birleştirme, büyük veri kümelerini toplama ve en az kodla karmaşık işlemler gerçekleştirmenize olanak tanır. Neredeyse her soru yanıtlanarak, destekleyici veriler toplandıkça ve analiz, doğru sorgunun nasıl oluşturulacağını anlamış olabilir.

Azure Izleyici 'deki [Öngörüler](../insights/insights-overview.md) ve [çözümler](../insights/solutions-inventory.md) gibi bazı özellikler, sizi temel sorgulara açmadan günlük verilerini işler. Azure Izleyici 'nin diğer özelliklerinden tamamen yararlanmak için sorguların nasıl oluşturulduğunu ve Azure Izleyici günlüklerinde verileri etkileşimli olarak analiz etmek için bunları nasıl kullanabileceğinizi anlamanız gerekir.

Azure Izleyici 'de günlük sorguları hakkında bilgi edinmek için bu makaleyi başlangıç noktası olarak kullanın. Ortak soruları yanıtlar ve daha fazla ayrıntı ve ders sağlayan diğer belgelere bağlantılar sağlar.

## <a name="how-can-i-learn-how-to-write-queries"></a>Sorguların nasıl yazılacağını nasıl öğrenirim?
Herhangi bir öğeye doğrudan gitmek istiyorsanız aşağıdaki öğreticilerle başlayabilirsiniz:

- [Azure izleyici 'de Log Analytics kullanmaya](get-started-portal.md)başlayın.
- [Azure izleyici 'de günlük sorgularını kullanmaya](get-started-queries.md)başlayın.

Temel bilgileri aldıktan sonra, tanıtım ortamımızdan başlayarak kendi verilerinizi veya verilerinizi kullanarak birden çok dersi adım adım inceleyin: 

- [Azure Izleyici günlük sorgularındaki dizelerle çalışma](string-operations.md)
 
## <a name="what-language-do-log-queries-use"></a>Günlük sorgularının hangi dillerde kullanıldığı.
Azure Izleyici günlükleri [azure Veri Gezgini](/azure/data-explorer)tabanlıdır ve günlük sorguları aynı kusto sorgu DILI (KQL) kullanılarak yazılır. Bu, okunması ve yazarı kolay bir şekilde tasarlanan ve en az kılavuzla kullanmaya başlayabilmeniz gereken zengin bir dildir.

KQL hakkındaki tüm belgeler ve kullanılabilen farklı işlevlerde başvuru için bkz. [Azure Veri Gezgini KQL belgeleri](/azure/kusto/query) .<br>
Azure Izleyici günlüklerinden verileri kullanarak dile yönelik hızlı bir anlatım için bkz. [Azure izleyici 'de günlük sorgularıyla çalışmaya başlama](get-started-queries.md) .
Azure Izleyici tarafından kullanılan KQL sürümündeki küçük farklılıklar için bkz. [Azure izleyici günlük sorgu dili farklılıkları](data-explorer-difference.md) .

## <a name="what-data-is-available-to-log-queries"></a>Günlük sorgularının hangi verileri kullanılabilir?
Azure Izleyici günlüklerinde toplanan tüm veriler, günlük sorgularını almak ve analiz etmek için kullanılabilir. Farklı veri kaynakları verileri farklı tablolara yazacak, ancak birden çok kaynak genelinde verileri çözümlemek için tek bir sorguya birden fazla tablo ekleyebilirsiniz. Bir sorgu oluşturduğunuzda, hangi tabloların Aradığınız verilere sahip olduğunu belirleyerek başlar, bu nedenle Azure Izleyici günlüklerindeki verilerin nasıl yapılandırıldığı hakkında en az bir temel bilgiye sahip olmanız gerekir.

Azure Izleyici günlüklerini dolduran farklı veri kaynaklarının listesi için bkz. [Azure Izleyici günlüklerinin kaynakları](../platform/data-platform-logs.md#sources-of-azure-monitor-logs).<br>
Verilerin nasıl yapılandırıldığını gösteren bir açıklama için bkz. [Azure Izleyici günlüklerinin yapısı](logs-structure.md) .

## <a name="what-does-a-log-query-look-like"></a>Günlük sorgusu nasıl görünür?
Bir sorgu, tablodaki tüm kayıtları almak için tek bir tablo adı kadar basit olabilir:

```Kusto
Syslog
```

Ya da belirli kayıtları filtreleyebilir, özetleyebilir ve sonuçları bir grafik halinde görselleştirebilir:

```
SecurityEvent
| where TimeGenerated > ago(7d)
| where EventID == 4625
| summarize count() by Computer, bin(TimeGenerated, 1h)
| render timechart 
```

Daha karmaşık analizler için, bir birleştirmeyi kullanarak birden çok tablodan veri alabilir ve sonuçları analiz edebilirsiniz.

```Kusto
app("ContosoRetailWeb").requests
| summarize count() by bin(timestamp,1hr)
| join kind= inner (Perf
    | summarize avg(CounterValue) 
      by bin(TimeGenerated,1hr))
on $left.timestamp == $right.TimeGenerated
```
KQL hakkında bilgi sahibi olmadığınız halde, bu sorgular tarafından kullanılan temel mantığı en az şekilde belirleyebilmelisiniz. Bunlar bir tablonun adıyla başlar ve bu verileri filtrelemek ve işlemek için birden çok komut eklemektir. Bir sorgu herhangi bir sayıda komutu kullanabilir ve kullanılabilir farklı KQL komutlarına alıştıkça daha karmaşık sorgular yazabilirsiniz.

Dili ve ortak işlevleri tanıtan günlük sorguları hakkında bir öğretici için bkz. [Azure izleyici 'de günlük sorgularıyla çalışmaya başlama](get-started-queries.md) .<br>


## <a name="what-is-log-analytics"></a>Log Analytics nedir?
Log Analytics, günlük sorgularını yazmak ve bunların sonuçlarını etkileşimli olarak çözümlemek için Azure portal birincil araçtır. Günlük sorgusu Azure Izleyici 'de başka bir yerde kullanılsa bile, genellikle Log Analytics kullanarak sorguyu yazın ve test edersiniz.

Azure portal çeşitli yerlerden Log Analytics başlatabilirsiniz. Log Analytics için kullanılabilir olan verilerin kapsamı, nasıl başladiğinize göre belirlenir. Daha fazla ayrıntı için bkz. [sorgu kapsamı](scope.md) .

- **Azure izleyici** menüsünde veya **Log Analytics çalışma alanları** menüsünden **Günlükler** ' i seçin.
- Application Insights uygulamasının **genel bakış** sayfasından **analiz** ' ı seçin.
- Azure kaynağı menüsündeki **günlükleri** seçin.

![Log Analytics](media/log-query-overview/log-analytics.png)

Özelliklerinden birkaçını tanıtan bir Log Analytics Öğretici Kılavuzu için bkz. [Azure izleyici Log Analytics kullanmaya başlama](get-started-portal.md) .

## <a name="where-else-are-log-queries-used"></a>Günlük sorgularının kullanıldığı yer nedir?
Günlük sorgularıyla etkileşimli olarak çalışmaya ek olarak, Azure Izleyici 'deki sorguları kullanacağınız Log Analytics, Azure Izleyici 'de bulunan ve bunlara ilişkin sonuçlarla birlikte şunları içerir:

- **Uyarı kuralları.** [Uyarı kuralları](../platform/alerts-overview.md) çalışma alanınızdaki verilerden sorunları önceden belirler.  Her uyarı kuralı, düzenli aralıklarla otomatik olarak çalıştırılan bir günlük aramasını temel alır.  Sonuçlar, bir uyarının oluşturulup oluşturulmadığını belirlemede denetlenir.
- **Panoların.** Herhangi bir sorgunun sonucunu, günlük ve ölçüm verilerini birlikte görselleştirmenize ve isteğe bağlı olarak diğer Azure kullanıcılarıyla paylaşmanıza olanak sağlayan bir [Azure panosuna](../learn/tutorial-logs-dashboards.md) sabitleyebilirsiniz.
- **Görünümler.**  [Görünüm Tasarımcısı](../platform/view-designer.md)ile Kullanıcı panolarına dahil edilecek veri görselleştirmeleri oluşturabilirsiniz.  Günlük sorguları, her görünümdeki [Kutucuklar](../platform/view-designer-tiles.md) ve [görselleştirme parçaları](../platform/view-designer-parts.md) tarafından kullanılan verileri sağlar.  
- **Dışarı aktarın.**  Azure Izleyici 'den Excel 'e veya [Power BI](../platform/powerbi.md)günlük verilerini içeri aktardığınızda, dışarı aktarılacak verileri tanımlamak için bir günlük sorgusu oluşturun.
- **PowerShell.** Azure Izleyici 'den günlük verilerini almak için [Get-Azoperationalınsightssearchresults](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult) kullanan bir komut satırından veya bir Azure Otomasyonu runbook 'tan PowerShell betiği çalıştırabilirsiniz.  Bu cmdlet alınacak verileri belirlemede bir sorgu gerektirir.
- **Azure Izleyici günlükleri API 'SI.**  [Azure Izleyici günlükleri API 'si](https://dev.loganalytics.io) , herhangi bir REST API istemcisinin, çalışma alanından günlük verilerini almasına izin verir.  API isteği alınacak verileri belirlemede Azure Izleyici 'ye karşı çalıştırılan bir sorgu içerir.


## <a name="next-steps"></a>Sonraki adımlar
- [Azure portal Log Analytics kullanma hakkında öğreticiyi](get-started-portal.md)adım adım inceleyin.
- [Sorgu yazma hakkında öğreticiyi](get-started-queries.md)adım adım inceleyin.
