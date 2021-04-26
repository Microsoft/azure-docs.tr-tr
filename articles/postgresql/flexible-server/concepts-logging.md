---
title: Günlükler-PostgreSQL için Azure veritabanı-esnek sunucu
description: PostgreSQL için Azure veritabanı 'nda günlük yapılandırma, depolama ve çözümlemeyi açıklar-esnek sunucu
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: e09c01fcfb9c4725ac169151e85c8b030d8bb18c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606394"
---
# <a name="logs-in-azure-database-for-postgresql---flexible-server"></a>PostgreSQL için Azure veritabanı 'ndaki Günlükler-esnek sunucu

> [!IMPORTANT]
> PostgreSQL için Azure veritabanı-esnek sunucu önizlemededir

PostgreSQL için Azure veritabanı, Postgres 'e yönelik standart günlüklere yapılandırma ve erişme olanağı sağlar. Günlükler, yapılandırma hatalarını belirlemek, sorunlarını gidermek ve onarmak ve performans performansını düzeltmek için kullanılabilir. Yapılandırabileceğiniz ve erişebileceğiniz günlüğe kaydetme bilgileri hatalar, sorgu bilgileri, oto vakum kayıtları, bağlantılar ve kontrol noktaları içerir. (İşlem günlüklerine erişim kullanılamaz).

Denetim günlüğü, bir Postgres uzantısı aracılığıyla kullanılabilir hale getirilir `pgaudit` . Daha fazla bilgi edinmek için [Denetim kavramları](concepts-audit.md) makalesini ziyaret edin.

## <a name="configure-logging"></a>Günlüğe kaydetmeyi yapılandırma

Günlük sunucusu parametrelerini kullanarak sunucunuzda Postgres standart günlüğünü yapılandırabilirsiniz. Postgres günlük parametreleri hakkında daha fazla bilgi edinmek için, ne [zaman günlüğe kaydedilir](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHEN) ve Postgres belgelerinin [günlük](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHAT) bölümlerini ziyaret edin. PostgreSQL için Azure veritabanı 'nda, tüm Postgres günlük oluşturma parametreleri de yapılandırılabilir.

PostgreSQL için Azure veritabanı 'nda parametrelerin nasıl yapılandırılacağını öğrenmek için [Portal belgelerine](howto-configure-server-parameters-using-portal.md) veya [CLI belgelerine](howto-configure-server-parameters-using-cli.md)bakın.

> [!NOTE]
> Yüksek hacimli Günlükler yapılandırma (örneğin, bildirim günlüğü), önemli performans yükü ekleyebilir. 

## <a name="accessing-logs"></a>Günlüklere erişme

PostgreSQL için Azure veritabanı, Azure Izleyici tanılama ayarlarıyla tümleşiktir. Tanılama ayarları, Azure Izleyici günlüklerine analiz ve uyarı, akış için Event Hubs ve arşivlenmek üzere Azure Storage günlüklerini JSON biçiminde göndermenizi sağlar. 

### <a name="log-format"></a>Günlük biçimi

Aşağıdaki tablo, **Postgrestablogs** türü için alanları açıklar. Seçtiğiniz çıkış uç noktasına bağlı olarak, dahil edilen alanlar ve göründükleri sıralama farklılık gösterebilir. 

|**Alan** | **Açıklama** |
|---|---|
| TenantId | Kiracı KIMLIĞINIZ |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | Günlük kaydedildiği zaman damgası (UTC) |
| Tür | Günlüğün türü. Her `AzureDiagnostics` |
| SubscriptionId | Sunucunun ait olduğu abonelik için GUID |
| adlı yönetilen örnek, | Sunucunun ait olduğu kaynak grubunun adı |
| ResourceProvider | Kaynak sağlayıcının adı. Her `MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| ResourceId | Kaynak URI 'SI |
| Kaynak | Sunucunun adı |
| Kategori | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| Düzeyi | Günlüğe kaydetme düzeyi, örnek: günlük, hata, BILDIRIM |
| İleti | Birincil günlük iletisi | 
| Etki alanı | Sunucu sürümü, örnek: Postgres-10 |
| Ayrıntı | İkincil günlük iletisi (varsa) |
| ColumnName | Sütunun adı (varsa) |
| SchemaName | Şemanın adı (varsa) |
| DatatypeName | Veri türünün adı (varsa) |
| LogicalServerName | Sunucunun adı | 
| _ResourceId | Kaynak URI 'SI |
| Ön ek | Günlük satırının öneki |


## <a name="next-steps"></a>Sonraki adımlar

- [Günlükleri yapılandırma ve erişme](howto-configure-and-access-logs.md)hakkında daha fazla bilgi edinin.
- [Azure izleyici fiyatlandırması](https://azure.microsoft.com/pricing/details/monitor/)hakkında daha fazla bilgi edinin.
- [Denetim günlükleri](concepts-audit.md) hakkında daha fazla bilgi edinin
