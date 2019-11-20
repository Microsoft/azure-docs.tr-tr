---
title: Azure Izleme günlükleri ile Azure Işlevlerini izleme
description: İşlev yürütmelerini izlemek için Azure Işlevleri ile Azure Izleme günlüklerini nasıl kullanacağınızı öğrenin.
author: ahmedelnably
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: aelnably
ms.openlocfilehash: a4fcf6b4dfeae3d8e53e083b2951d9d594c53d73
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/27/2019
ms.locfileid: "72966144"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>Azure Izleme günlükleri ile Azure Işlevlerini izleme

Azure Işlevleri, işlevleri izlemek için [Azure Izleyici günlükleriyle](../azure-monitor/platform/data-platform-logs.md) bir tümleştirme sunar. Bu makalede, Azure Işlevleri 'nin Azure Izleyici günlüklerine sistem tarafından oluşturulan ve Kullanıcı tarafından oluşturulan Günlükler göndermek için nasıl yapılandırılacağı gösterilmektedir.

Azure Izleyici günlükleri, toplanan verileri hızlı bir şekilde almak, birleştirmek ve analiz etmek için [sorgularla](../azure-monitor/log-query/log-query-overview.md) çözümlenebileceği aynı çalışma alanındaki farklı kaynaklardan günlükleri birleştirme olanağı sağlar.  Azure portal [Log Analytics](../azure-monitor/log-query/portals.md) kullanarak sorgular oluşturup test edebilir ve ardından bu araçları kullanarak verileri doğrudan çözümleyebilir ya da [görselleştirmeler](../azure-monitor/visualizations.md) veya [Uyarı kurallarıyla](../azure-monitor/platform/alerts-overview.md)kullanılacak sorguları kaydedebilirsiniz.

Azure Izleyici, Azure Veri Gezgini tarafından kullanılan ve basit günlük sorguları için uygun olan, ayrıca toplamalar, birleşimler ve akıllı analiz gibi gelişmiş işlevleri de içeren [kusto sorgu dilinin](/azure/kusto/query/) bir sürümünü kullanır. [Birden çok ders](../azure-monitor/log-query/get-started-queries.md)kullanarak sorgu dilini hızlıca öğrenebilirsiniz.

> [!NOTE]
> Azure Izleyici günlükleri ile tümleştirme Şu anda Windows tüketim, Premium ve adanmış barındırma planlarında çalışan işlev uygulamaları için genel önizlemededir.

## <a name="setting-up"></a>Ayarlanıyor

Izleme bölümünde **Tanılama ayarları** ' nı seçin ve ardından **Ekle**' ye tıklayın.

![Tanılama ayarı ekleme](media/functions-monitor-log-analytics/diagnostic-settings-add.png)

Ayar sayfasında **Log Analytics gönder**' i seçin ve **log** **' ın altında, bu**tablo istenen günlükleri içerir.

![Tanılama ayarı ekleme](media/functions-monitor-log-analytics/choose-table.png)

## <a name="user-generated-logs"></a>Kullanıcı tarafından oluşturulan Günlükler

Özel Günlükler oluşturmak için, dilinize bağlı olarak belirli günlük ifadesini kullanabilirsiniz, örnek kod parçacıkları aşağıda verilmiştir:

**JavaScript**

```javascript
    context.log('My app logs here.');
```

**Python**

```python
    logging.info('My app logs here.')
```

**.NET**

```csharp
    log.LogInformation("My app logs here.");
```

**Java**

```java
    context.getLogger().info("My app logs here.");
```

**PowerShell**

```powershell
    Write-Host "My app logs here."
```

## <a name="querying-the-logs"></a>Günlükleri sorgulama

Oluşturulan günlükleri sorgulamak için Log Analytics çalışma alanına gidin ve **Günlükler**' e tıklayın.

![LA çalışma alanındaki sorgu penceresi](media/functions-monitor-log-analytics/querying.png)

Azure Işlevleri tüm günlükleri **Functionapplogs** tablosuna yazar, bazı örnek sorgular aşağıda verilmiştir.

### <a name="all-logs"></a>Tüm Günlükler

```

FunctionAppLogs
| order by TimeGenerated desc

```

### <a name="a-specific-function-logs"></a>Belirli bir işlev günlükleri

```

FunctionAppLogs
| where FunctionName == "<Function name>" 

```

### <a name="exceptions"></a>Özel Durumlar

```

FunctionAppLogs
| where ExceptionDetails != ""  
| order by TimeGenerated asc

```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure işlevlerine genel bakış](functions-overview.md) konusunu inceleyin
- [Azure Izleyici günlükleri](../azure-monitor/platform/data-platform-logs.md) hakkında daha fazla bilgi edinin
- [Sorgu dili](../azure-monitor/log-query/get-started-queries.md)hakkında daha fazla bilgi edinin.
