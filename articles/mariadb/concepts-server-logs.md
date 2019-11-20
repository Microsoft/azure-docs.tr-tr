---
title: MariaDB için Azure veritabanı için sunucu günlükleri
description: MariaDB için Azure veritabanı 'nda kullanılabilen günlükleri ve farklı günlük düzeylerini etkinleştirmek için kullanılabilen parametreleri açıklar.
author: rachel-msft
ms.author: raagyema
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: 10dbd4d7fa838ee7f8a3f70b3caadb570877d685
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71259965"
---
# <a name="slow-query-logs-in-azure-database-for-mariadb"></a>MariaDB için Azure veritabanı 'nda yavaş sorgu günlükleri
MariaDB için Azure veritabanı 'nda yavaş sorgu günlüğü kullanıcılar tarafından kullanılabilir. İşlem günlüğüne erişim desteklenmez. Yavaş sorgu günlüğü, sorun giderme için performans sorunlarını belirlemek için kullanılabilir.

Yavaş sorgu günlüğü hakkında daha fazla bilgi için, [yavaş sorgu günlüğü](https://mariadb.com/kb/en/library/slow-query-log-overview/)Için MariaDB belgelerine bakın.

## <a name="access-slow-query-logs"></a>Yavaş sorgu günlüklerine erişin
Azure portal ve Azure CLı kullanarak MariaDB yavaş sorgu günlükleri için Azure veritabanı 'nı listeleyebilir ve indirebilirsiniz.

Azure portal, MariaDB sunucusu için Azure veritabanınızı seçin. **İzleme** başlığı altında, **sunucu günlükleri** sayfasını seçin.

Azure CLı hakkında daha fazla bilgi için bkz. [Azure CLI kullanarak sunucu günlüklerini yapılandırma ve erişme](howto-configure-server-logs-cli.md).

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

Yavaş sorgu günlüğü parametrelerinin tam açıklamaları için MariaDB [yavaş sorgu günlüğü belgelerine](https://mariadb.com/kb/en/library/slow-query-log-overview/) bakın.

## <a name="diagnostic-logs"></a>Tanılama günlükleri
MariaDB için Azure veritabanı, Azure Izleyici tanılama günlükleriyle tümleşiktir. MariaDB sunucunuzda yavaş sorgu günlüklerini etkinleştirdikten sonra, bunların Azure Izleyici günlüklerine, Event Hubs veya Azure depolama 'ya yayılmasını tercih edebilirsiniz. Tanılama günlüklerinin nasıl etkinleştirileceği hakkında daha fazla bilgi edinmek için [tanılama günlükleri belgelerinin](../azure-monitor/platform/resource-logs-overview.md)nasıl yapılır bölümüne bakın.

> [!IMPORTANT]
> Sunucu günlükleri için bu tanılama özelliği yalnızca Genel Amaçlı ve bellek için Iyileştirilmiş [fiyatlandırma katmanlarında](concepts-pricing-tiers.md)kullanılabilir.

Aşağıdaki tabloda her günlükte neler olduğu açıklanmaktadır. Çıkış yöntemine bağlı olarak, dahil edilen alanlar ve göründükleri sıralama farklılık gösterebilir.

| **Özellik** | **Açıklama** |
|---|---|
| `TenantId` | Kiracı KIMLIĞINIZ |
| `SourceSystem` | `Azure` |
| `TimeGenerated`UTC | Günlük kaydedildiği zaman damgası (UTC) |
| `Type` | Günlüğün türü. Her zaman `AzureDiagnostics` |
| `SubscriptionId` | Sunucunun ait olduğu abonelik için GUID |
| `ResourceGroup` | Sunucunun ait olduğu kaynak grubunun adı |
| `ResourceProvider` | Kaynak sağlayıcının adı. Her zaman `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Kaynak URI 'SI |
| `Resource` | Sunucunun adı |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | Sunucunun adı |
| `start_time_t`UTC | Sorgunun başladığı zaman |
| `query_time_s` | Sorgunun yürütülmesi için geçen toplam süre |
| `lock_time_s` | Sorgunun kilitlenme toplam süre |
| `user_host_s` | Kullanıcı Adı |
| `rows_sent_s` | Gönderilen satır sayısı |
| `rows_examined_s` | İncelenen satır sayısı |
| `last_insert_id_s` | [last_insert_id](https://mariadb.com/kb/en/library/last_insert_id/) |
| `insert_id_s` | KIMLIĞI Ekle |
| `sql_text_s` | Tam sorgu |
| `server_id_s` | Sunucu Kimliği |
| `thread_id_s` | İş parçacığı KIMLIĞI |
| `\_ResourceId` | Kaynak URI 'SI |

## <a name="next-steps"></a>Sonraki adımlar
- [Azure Portal sunucu günlüklerini yapılandırma ve erişme](howto-configure-server-logs-portal.md).
