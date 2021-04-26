---
title: Cosmos DB gelişmiş yapılandırma için Azure Işlevleri tetikleyicisi
description: Cosmos DB için Azure Işlevleri tetikleyicisi tarafından kullanılan günlük ve bağlantı ilkesini yapılandırma hakkında bilgi edinin
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/19/2020
ms.author: maquaran
ms.openlocfilehash: 30328db465e0d9bf8c1ce67d92e48c688c51e043
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100574611"
---
# <a name="how-to-configure-logging-and-connectivity-with-the-azure-functions-trigger-for-cosmos-db"></a>Cosmos DB için Azure Işlevleri tetikleyicisiyle günlüğe kaydetme ve bağlantı yapılandırma
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Bu makalede, Cosmos DB için Azure Işlevleri tetikleyicisini kullanırken ayarlayabileceğiniz gelişmiş yapılandırma seçenekleri açıklanmaktadır.

## <a name="enabling-trigger-specific-logs"></a>Tetikleyiciye özgü günlükleri etkinleştirme

Cosmos DB için Azure Işlevleri tetikleyicisi, [değişiklik akışı Işlemcisi kitaplığını](./change-feed-processor.md) dahili olarak kullanır ve kitaplık, [sorun giderme amacıyla](./troubleshoot-changefeed-functions.md)iç işlemleri izlemek için kullanılabilecek bir sistem durumu günlükleri kümesi oluşturur.

Sistem durumu günlükleri, Yük Dengeleme senaryoları veya başlatma sırasında işlemler denenirken Cosmos DB için Azure Işlevlerinin nasıl tetikleneceğini açıklamaktadır.

### <a name="enabling-logging"></a>Günlüğe kaydetmeyi etkinleştirme

Cosmos DB için Azure Işlevleri tetikleyicisi 'ni kullanırken günlüğe kaydetmeyi etkinleştirmek için `host.json` Azure işlevleri projenizde veya Azure Işlevleri uygulamanızda dosyayı bulun ve [gerekli günlük kaydı düzeyini yapılandırın](../azure-functions/functions-monitoring.md#log-levels-and-categories). `Host.Triggers.CosmosDB`Aşağıdaki örnekte gösterildiği gibi izlemeleri etkinleştirin:

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

Azure Işlevi güncelleştirilmiş yapılandırmayla dağıtıldıktan sonra, izlemelerinizin bir parçası olarak Cosmos DB Günlükler için Azure Işlevleri tetiklemeyi görürsünüz. Kayıtları, yapılandırılan günlük sağlayıcınızdaki *Kategori* altında görüntüleyebilirsiniz `Host.Triggers.CosmosDB` .

### <a name="query-the-logs"></a>Günlükleri sorgulama

[Azure Application Insights ' Analytics](../azure-monitor/logs/log-query-overview.md)'te Cosmos DB Için Azure işlevleri tetikleyicisi tarafından oluşturulan günlükleri sorgulamak için aşağıdaki sorguyu çalıştırın:

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="configuring-the-connection-policy"></a>Bağlantı ilkesini yapılandırma

İki bağlantı modu vardır-doğrudan mod ve ağ geçidi modu. Bu bağlantı modları hakkında daha fazla bilgi edinmek için [bağlantı modları](sql-sdk-connection-modes.md) makalesine bakın. Varsayılan olarak, **ağ geçidi** , Cosmos DB Için Azure işlevleri tetikleyicisinde tüm bağlantıları oluşturmak için kullanılır. Ancak, performans odaklı senaryolar için en iyi seçenek olmayabilir.

### <a name="changing-the-connection-mode-and-protocol"></a>Bağlantı modunu ve protokolünü değiştirme

İstemci bağlantı ilkesini yapılandırmak için kullanılabilecek iki temel yapılandırma ayarı vardır: **bağlantı modu** ve **bağlantı protokolü**. Cosmos DB için Azure Işlevleri tetikleyicisi tarafından kullanılan varsayılan bağlantı modunu ve protokolünü ve tüm [Azure Cosmos DB bağlamalarını](../azure-functions/functions-bindings-cosmosdb-v2-output.md)değiştirebilirsiniz. Varsayılan ayarları değiştirmek için `host.json` Azure işlevleri projenizde veya Azure Işlevleri uygulamanızda dosyayı bulmanız ve aşağıdaki [ek ayarı](../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings)eklemeniz gerekir:

```js
{
  "cosmosDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

Burada `connectionMode` istenen bağlantı moduna (doğrudan veya ağ geçidine) ve `protocol` istenen bağlantı protokolüne (TCP veya https) sahip olmanız gerekir. 

Azure Işlevleri projeniz Azure Işlevleri v1 çalışma zamanı ile çalışıyorsa, yapılandırmanın küçük bir ad farkı vardır; `documentDB` bunun yerine şunu kullanmalısınız `cosmosDB` :

```js
{
  "documentDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

> [!NOTE]
> İşlev uygulamanızı bir tüketim planında barındırırken, her örnek, sürdürebilmesine yönelik soket bağlantısı miktarına göre bir sınıra sahiptir. Doğrudan/TCP moduyla çalışırken, Tasarım yaparak daha fazla bağlantı oluşturulur ve [Tüketim planı sınırına](../azure-functions/manage-connections.md#connection-limit)ulaşarak, ağ geçidi modunu kullanabilir veya Işlev uygulamanızı [Premium bir planda](../azure-functions/functions-premium-plan.md) ya da [adanmış (App Service)](../azure-functions/dedicated-plan.md)bir planda barındırabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Işlevlerinde bağlantı sınırları](../azure-functions/manage-connections.md#connection-limit)
* Azure Işlevleri uygulamalarınızda [Izlemeyi etkinleştirin](../azure-functions/functions-monitoring.md) .
* Cosmos DB için Azure Işlevleri tetikleyicisini kullanırken [sık karşılaşılan sorunları tanılamaya ve gidermeye](./troubleshoot-changefeed-functions.md) nasıl bilgi alabileceğinizi öğrenin.