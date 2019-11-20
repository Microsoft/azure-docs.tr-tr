---
title: MySQL için Azure veritabanı için sunucu günlükleri
description: MySQL için Azure veritabanı 'nda kullanılabilen yavaş sorgu günlüklerini ve farklı günlük düzeylerini etkinleştirmek için kullanılabilen parametreleri açıklar.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: 90f3e80c92cd4409a77d4661462ae027c535eaf7
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72434291"
---
# <a name="slow-query-logs-in-azure-database-for-mysql"></a>MySQL için Azure veritabanı 'nda yavaş sorgu günlükleri
MySQL için Azure veritabanı 'nda, yavaş sorgu günlüğü kullanıcılar tarafından kullanılabilir. İşlem günlüğüne erişim desteklenmez. Yavaş sorgu günlüğü, sorun giderme için performans sorunlarını belirlemek için kullanılabilir.

MySQL yavaş sorgu günlüğü hakkında daha fazla bilgi için MySQL Reference el ile [yavaş sorgu günlüğü bölümüne](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)bakın.

## <a name="access-slow-query-logs"></a>Yavaş sorgu günlüklerine erişin
MySQL için Azure veritabanı 'nı, Azure portal ve Azure CLı kullanarak yavaş sorgu günlüklerini listeleyebilir ve indirebilirsiniz.

Azure portal, MySQL için Azure veritabanı sunucunuzu seçin. **İzleme** başlığı altında, **sunucu günlükleri** sayfasını seçin.

Azure CLı hakkında daha fazla bilgi için bkz. [Azure CLI kullanarak sunucu günlüklerini yapılandırma ve erişme](howto-configure-server-logs-in-cli.md).

## <a name="log-retention"></a>Günlük tutma
Günlükler, oluşumlarından yedi güne kadar kullanılabilir. Kullanılabilir günlüklerin toplam boyutu 7 GB 'yi aşarsa, alan kullanılabilir olana kadar en eski dosyalar silinir. 

Günlükler her 24 saatte bir veya 7 GB döndürülür, hangisi önce gelir.

## <a name="configure-slow-query-logging"></a>Yavaş sorgu günlüğünü yapılandırma 
Varsayılan olarak, yavaş sorgu günlüğü devre dışıdır. Etkinleştirmek için slow_query_log olarak ayarlayın.

Ayarlayabileceğiniz diğer parametreler şunlardır:

- **long_query_time**: sorgu günlüğe kaydedilir (saniye cinsinden). Varsayılan değer 10 saniyedir.
- **log_slow_admin_statements**: varsa, slow_query_log 'e YAZıLAN DEYIMLERDE ALTER_TABLE ve ANALYZE_TABLE gibi yönetim deyimlerini içerir.
- **log_queries_not_using_indexes**: dizinleri kullanmayan sorguların slow_query_log 'e kaydedilip kaydedilmeyeceğini belirler
- **log_throttle_queries_not_using_indexes**: Bu parametre, yavaş sorgu günlüğüne yazılabilen Dizin dışı sorguların sayısını sınırlar. Bu parametre, log_queries_not_using_indexes açık olarak ayarlandığında devreye girer.

> [!Note]
> @No__t-0 için günlük, 2048 karakteri aşarsa kesilir.

Yavaş sorgu günlüğü parametrelerinin tam açıklamaları için MySQL 'in [yavaş sorgu günlüğü belgelerini](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) inceleyin.

## <a name="diagnostic-logs"></a>Tanılama günlükleri
MySQL için Azure veritabanı, Azure Izleyici tanılama günlükleriyle tümleşiktir. MySQL sunucunuzda yavaş sorgu günlüklerini etkinleştirdikten sonra Azure Izleyici günlüklerine, Event Hubs veya Azure depolama 'ya yayılmasını seçebilirsiniz. Tanılama günlüklerinin nasıl etkinleştirileceği hakkında daha fazla bilgi edinmek için [tanılama günlükleri belgelerinin](../azure-monitor/platform/resource-logs-overview.md)nasıl yapılır bölümüne bakın.

> [!IMPORTANT]
> Sunucu günlükleri için bu tanılama özelliği yalnızca Genel Amaçlı ve bellek için Iyileştirilmiş [fiyatlandırma katmanlarında](concepts-pricing-tiers.md)kullanılabilir.

Aşağıdaki tabloda her günlükte neler olduğu açıklanmaktadır. Çıkış yöntemine bağlı olarak, dahil edilen alanlar ve göründükleri sıralama farklılık gösterebilir.

| **Özellik** | **Açıklama** |
|---|---|
| `TenantId` | Kiracı KIMLIĞINIZ |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | Günlük kaydedildiği zaman damgası (UTC) |
| `Type` | Günlüğün türü. Her zaman `AzureDiagnostics` |
| `SubscriptionId` | Sunucunun ait olduğu abonelik için GUID |
| `ResourceGroup` | Sunucunun ait olduğu kaynak grubunun adı |
| `ResourceProvider` | Kaynak sağlayıcının adı. Her zaman `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Kaynak URI 'SI |
| `Resource` | Sunucunun adı |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | Sunucunun adı |
| `start_time_t` [UTC] | Sorgunun başladığı zaman |
| `query_time_s` | Sorgunun yürütülmesi için geçen saniye cinsinden toplam süre |
| `lock_time_s` | Sorgunun kilitlenme saniye cinsinden toplam süre |
| `user_host_s` | Kullanıcı adı |
| `rows_sent_s` | Gönderilen satır sayısı |
| `rows_examined_s` | İncelenen satır sayısı |
| `last_insert_id_s` | [last_insert_id](https://dev.mysql.com/doc/refman/8.0/en/information-functions.html#function_last-insert-id) |
| `insert_id_s` | KIMLIĞI Ekle |
| `sql_text_s` | Tam sorgu |
| `server_id_s` | Sunucunun KIMLIĞI |
| `thread_id_s` | İş parçacığı KIMLIĞI |
| `\_ResourceId` | Kaynak URI 'SI |

## <a name="next-steps"></a>Sonraki Adımlar
- [Azure CLI 'dan sunucu günlüklerini yapılandırma ve erişme](howto-configure-server-logs-in-cli.md).
