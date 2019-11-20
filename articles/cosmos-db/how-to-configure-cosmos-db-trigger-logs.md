---
title: Cosmos DB için Azure Işlevleri tetikleyicisi kullanırken günlükleri yapılandırma ve okuma
description: Cosmos DB için Azure Işlevleri 'ni kullanırken günlüklerin Azure Işlevleri günlüğe kaydetme işlem hattında nasıl açığa alınacağını öğrenin
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 17b2d4854e3222e5bdb607c6c80b0ec5ed6177d3
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093591"
---
# <a name="how-to-configure-and-read-the-logs-when-using-azure-functions-trigger-for-cosmos-db"></a>Cosmos DB için Azure Işlevleri tetikleyicisi kullanırken günlükleri yapılandırma ve okuma

Bu makalede, Azure Işlevleri ortamınızı, Cosmos DB günlükleri için Azure Işlevleri tetikleyicisini yapılandırılmış [izleme çözümünüze](../azure-functions/functions-monitoring.md)göndermek üzere nasıl yapılandırabileceğiniz açıklanmaktadır.

## <a name="included-logs"></a>Dahil edilen Günlükler

Cosmos DB için Azure Işlevleri tetikleyicisi, [değişiklik akışı Işlemcisi kitaplığını](./change-feed-processor.md) dahili olarak kullanır ve kitaplık, [sorun giderme amacıyla](./troubleshoot-changefeed-functions.md)iç işlemleri izlemek için kullanılabilecek bir sistem durumu günlükleri kümesi oluşturur.

Sistem durumu günlükleri, Yük Dengeleme senaryoları veya başlatma sırasında işlemler denenirken Cosmos DB için Azure Işlevlerinin nasıl tetikleneceğini açıklamaktadır.

## <a name="enabling-logging"></a>Günlüğe kaydetmeyi etkinleştirme

Cosmos DB için Azure işlevleri tetikleyicisi 'ni kullanırken günlüğe kaydetmeyi etkinleştirmek için Azure işlevleri `host.json` projenizde veya Azure işlevleri uygulamanızda dosyayı bulun ve [gerekli günlük kaydı düzeyini yapılandırın](../azure-functions/functions-monitoring.md#log-configuration-in-hostjson). Aşağıdaki örnekte gösterildiği `Host.Triggers.CosmosDB` gibi izlemeleri etkinleştirmeniz gerekir:

```js
{
  "version": "2.0",
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "Host.Triggers.CosmosDB": "Trace"
    }
  }
}
```

Azure Işlevi güncelleştirilmiş yapılandırmayla dağıtıldıktan sonra, izlemelerinizin bir parçası olarak Cosmos DB Günlükler için Azure Işlevleri tetiklemeyi görürsünüz. Kayıtları, yapılandırılan günlük sağlayıcınızdaki *Kategori* `Host.Triggers.CosmosDB`altında görüntüleyebilirsiniz.

## <a name="query-the-logs"></a>Günlükleri sorgulama

[Azure Application Insights ' Analytics](../azure-monitor/app/analytics.md)'te Cosmos DB Için Azure işlevleri tetikleyicisi tarafından oluşturulan günlükleri sorgulamak için aşağıdaki sorguyu çalıştırın:

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="next-steps"></a>Sonraki adımlar

* Azure Işlevleri uygulamalarınızda [Izlemeyi etkinleştirin](../azure-functions/functions-monitoring.md) .
* Cosmos DB için Azure Işlevleri tetikleyicisini kullanırken [sık karşılaşılan sorunları tanılamaya ve gidermeye](./troubleshoot-changefeed-functions.md) nasıl bilgi alabileceğinizi öğrenin.