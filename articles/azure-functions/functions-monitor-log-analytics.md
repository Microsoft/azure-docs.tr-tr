---
title: Azure Izleme günlükleri ile Azure Işlevlerini izleme
description: İşlev yürütmelerini izlemek için Azure Işlevleri ile Azure Izleme günlüklerini nasıl kullanacağınızı öğrenin.
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: b6ce9e77421df0563810fd7f8255720c1fd2d0c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100591070"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>Azure Izleme günlükleri ile Azure Işlevlerini izleme

Azure Işlevleri, işlevleri izlemek için [Azure Izleyici günlükleriyle](../azure-monitor/logs/data-platform-logs.md) bir tümleştirme sunar. Bu makalede, Azure Işlevleri 'nin Azure Izleyici günlüklerine sistem tarafından oluşturulan ve Kullanıcı tarafından oluşturulan Günlükler göndermek için nasıl yapılandırılacağı gösterilmektedir.

Azure Izleyici günlükleri, toplanan verileri hızlı bir şekilde almak, birleştirmek ve analiz etmek için [sorgularla](../azure-monitor/logs/log-query-overview.md) çözümlenebileceği aynı çalışma alanındaki farklı kaynaklardan günlükleri birleştirme olanağı sağlar.  Azure portal [Log Analytics](../azure-monitor/logs/log-query-overview.md) kullanarak sorgular oluşturup test edebilir ve ardından bu araçları kullanarak verileri doğrudan çözümleyebilir ya da [görselleştirmeler](../azure-monitor/visualizations.md) veya [Uyarı kurallarıyla](../azure-monitor/alerts/alerts-overview.md)kullanılacak sorguları kaydedebilirsiniz.

Azure Izleyici, Azure Veri Gezgini tarafından kullanılan ve basit günlük sorguları için uygun olan, ayrıca toplamalar, birleşimler ve akıllı analiz gibi gelişmiş işlevleri de içeren [kusto sorgu dilinin](/azure/kusto/query/) bir sürümünü kullanır. [Birden çok ders](../azure-monitor/logs/get-started-queries.md)kullanarak sorgu dilini hızlıca öğrenebilirsiniz.

> [!NOTE]
> Azure Izleyici günlükleri ile tümleştirme Şu anda Windows tüketim, Premium ve adanmış barındırma planlarında çalışan v2 ve v3 işlev uygulamaları için genel önizlemededir.

## <a name="setting-up"></a>Ayarlanıyor

1. [Azure Portal](https://portal.azure.com)Işlev uygulamanızın **izleme** bölümünden **Tanılama ayarları**' nı ve ardından **Tanılama ayarı Ekle**' yi seçin.

   :::image type="content" source="media/functions-monitor-log-analytics/diagnostic-settings-add.png" alt-text="Tanılama ayarlarını seçin":::

1. **Tanılama ayarları** sayfasında, **Kategori ayrıntıları** ve **günlüğü** altında **functionapplogs**' u seçin.

   **Functionapplogs** tablosu, istenen günlükleri içerir.

1. **Hedef ayrıntıları** altında **Log Analytics gönder**' i seçin ve sonra **Log Analytics çalışma alanınızı** seçin. 

1. Bir **Tanılama ayarları adı** girin ve ardından **Kaydet**' i seçin.

   :::image type="content" source="media/functions-monitor-log-analytics/choose-table.png" alt-text="Tanılama ayarı ekleme":::

## <a name="user-generated-logs"></a>Kullanıcı tarafından oluşturulan Günlükler

Özel Günlükler oluşturmak için, dilinize özgü günlüğe kaydetme ifadesini kullanın. Örnek kod parçacıkları şunlardır:


# <a name="c"></a>[C#](#tab/csharp)

```csharp
log.LogInformation("My app logs here.");
```

# <a name="java"></a>[Java](#tab/java)

```java
context.getLogger().info("My app logs here.");
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
context.log('My app logs here.');
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Write-Host "My app logs here."
```

# <a name="python"></a>[Python](#tab/python)

```python
logging.info('My app logs here.')
```

---

## <a name="querying-the-logs"></a>Günlükleri sorgulama

Oluşturulan günlükleri sorgulamak için:
 
1. İşlev uygulamanızdan **Tanılama ayarları**' nı seçin. 

1. **Tanılama ayarları** listesinden, işlev günlüklerini göndermek için yapılandırdığınız Log Analytics çalışma alanını seçin. 

1. **Log Analytics çalışma alanı** sayfasında **Günlükler**' i seçin.

   Azure Işlevleri, tüm günlükleri **Logmanagement** altındaki **functionapplogs** tablosuna yazar. 

   :::image type="content" source="media/functions-monitor-log-analytics/querying.png" alt-text="Log Analytics çalışma alanındaki sorgu penceresi":::

Bazı örnek sorgular aşağıda verilmiştir:

### <a name="all-logs"></a>Tüm Günlükler

```

FunctionAppLogs
| order by TimeGenerated desc

```

### <a name="specific-function-logs"></a>Belirli işlev günlükleri

```

FunctionAppLogs
| where FunctionName == "<Function name>" 

```

### <a name="exceptions"></a>Özel durumlar

```

FunctionAppLogs
| where ExceptionDetails != ""  
| order by TimeGenerated asc

```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure işlevlerine genel bakış](functions-overview.md)konusunu gözden geçirin.
- [Azure Izleyici günlükleri](../azure-monitor/logs/data-platform-logs.md)hakkında daha fazla bilgi edinin.
- [Sorgu dili](../azure-monitor/logs/get-started-queries.md)hakkında daha fazla bilgi edinin.
