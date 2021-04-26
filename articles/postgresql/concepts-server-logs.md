---
title: Günlükler-PostgreSQL için Azure veritabanı-tek sunucu
description: PostgreSQL için Azure veritabanı 'nda günlük yapılandırma, depolama ve çözümlemeyi açıklar-tek sunucu
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/25/2020
ms.openlocfilehash: 31b3c0a1894280e90f696f6c107b67a3af7b1880
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605357"
---
# <a name="logs-in-azure-database-for-postgresql---single-server"></a>PostgreSQL için Azure veritabanı 'ndaki Günlükler-tek sunucu

PostgreSQL için Azure veritabanı, Postgres 'nin standart günlüklerine yapılandırma ve erişme olanağı sağlar. Günlükler, yapılandırma hatalarını belirlemek, sorunlarını gidermek ve onarmak ve performans performansını düzeltmek için kullanılabilir. Yapılandırabileceğiniz ve erişebileceğiniz günlüğe kaydetme bilgileri hatalar, sorgu bilgileri, oto vakum kayıtları, bağlantılar ve kontrol noktaları içerir. (İşlem günlüklerine erişim kullanılamaz).

Denetim günlüğü, bir Postgres uzantısı, pgaudit aracılığıyla kullanılabilir hale getirilir. Daha fazla bilgi edinmek için [Denetim kavramları](concepts-audit.md) makalesini ziyaret edin.


## <a name="configure-logging"></a>Günlüğe kaydetmeyi yapılandırma 
Günlük sunucusu parametrelerini kullanarak sunucunuzda Postgres standart günlüğünü yapılandırabilirsiniz. Her PostgreSQL için Azure veritabanı sunucusunda `log_checkpoints` ve `log_connections` Varsayılan olarak üzerinde bulunur. Günlük gereksinimlerinize uyacak şekilde ayarlayabileceğiniz ek parametreler vardır: 

:::image type="content" source="./media/concepts-server-logs/log-parameters.png" alt-text="PostgreSQL için Azure veritabanı-günlük parametreleri":::

Postgres günlük parametreleri hakkında daha fazla bilgi edinmek için, ne [zaman günlüğe kaydedilir](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHEN) ve Postgres belgelerinin [günlük](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHAT) bölümlerini ziyaret edin. PostgreSQL için Azure veritabanı 'nda, tüm Postgres günlük oluşturma parametreleri de yapılandırılabilir.

PostgreSQL için Azure veritabanı 'nda parametrelerin nasıl yapılandırılacağını öğrenmek için [Portal belgelerine](howto-configure-server-parameters-using-portal.md) veya [CLI belgelerine](howto-configure-server-parameters-using-cli.md)bakın. 

> [!NOTE]
> Yüksek hacimli Günlükler yapılandırma (örneğin, bildirim günlüğü), önemli performans yükü ekleyebilir. 

## <a name="access-log-files"></a>. Log dosyalarına erişin
PostgreSQL için Azure veritabanı 'nda varsayılan günlük biçimi. log ' dır. Bu günlüğün örnek bir satırı şöyle görünür:

```
2019-10-14 17:00:03 UTC-5d773cc3.3c-LOG: connection received: host=101.0.0.6 port=34331 pid=16216
```

PostgreSQL için Azure veritabanı,. log dosyaları için kısa vadeli depolama konumu sağlar. Yeni bir dosya 1 saat veya 100 MB başlar, hangisi önce gelir. Günlükler, Postgres 'lerden yayıldıkları için geçerli dosyaya eklenir.  

Parametresini kullanarak bu kısa vadeli günlük depolama için saklama süresini ayarlayabilirsiniz `log_retention_period` . Varsayılan değer 3 gün, en büyük değer ise 7 gündür. Kısa vadeli depolama konumu 1 GB 'a kadar günlük dosyası tutabilir. 1 GB 'den sonra, saklama süresinden bağımsız olarak en eski dosyalar yeni günlüklere yer açmak için silinir. 

Günlüklerin ve günlük analizinin daha uzun süreli tutulması için,. log dosyalarını indirebilir ve bunları bir üçüncü taraf hizmetine taşıyabilirsiniz. [Azure clı](howto-configure-server-logs-using-cli.md) [Azure Portal](howto-configure-server-logs-in-portal.md)kullanarak dosyaları indirebilirsiniz. Alternatif olarak, Azure Izleyici tanılama ayarlarını, günlüklerinizi (JSON biçiminde) uzun dönem konumlarına otomatik olarak yayan bir şekilde yapılandırabilirsiniz. Aşağıdaki bölümde bu seçenek hakkında daha fazla bilgi edinin. 

Parametresini kapalı olarak ayarlayarak. log dosyalarının oluşturulmasını durdurabilirsiniz `logging_collector` . Kapatılıyor. günlük dosyası oluşturma, Azure Izleyici tanılama ayarlarını kullanıyorsanız önerilir. Bu yapılandırma, ek günlüğe kaydetme işleminin performans etkisini azaltacaktır.

## <a name="resource-logs"></a>Kaynak günlükleri

PostgreSQL için Azure veritabanı, Azure Izleyici tanılama ayarlarıyla tümleşiktir. Tanılama ayarları, Azure Izleyici günlüklerine analiz ve uyarı, akış için Event Hubs ve arşivlenmek üzere Azure Storage günlüklerini JSON biçiminde göndermenizi sağlar. 

> [!IMPORTANT]
> Sunucu günlükleri için bu tanılama özelliği yalnızca Genel Amaçlı ve bellek için Iyileştirilmiş [fiyatlandırma katmanlarında](concepts-pricing-tiers.md)kullanılabilir.


### <a name="configure-diagnostic-settings"></a>Tanılama ayarlarını yapılandırma

Azure portal, CLı, REST API ve PowerShell 'i kullanarak Postgres sunucunuz için tanılama ayarlarını etkinleştirebilirsiniz. Seçilecek günlük kategorisi **Postgressqllogs**' dır. ( [Sorgu deposu](concepts-query-store.md)kullanıyorsanız yapılandırabileceğiniz başka Günlükler vardır.)

Azure portal kullanarak kaynak günlüklerini etkinleştirmek için:

   1. Portalda, Postgres sunucunuzun gezinti menüsünde *Tanılama ayarları* ' na gidin.
   2. *Tanılama ayarı Ekle*' yi seçin.
   3. Bu ayarı adlandırın. 
   4. Tercih ettiğiniz uç noktayı (depolama hesabı, Olay Hub 'ı, Log Analytics) seçin. 
   5. **Postgressqllogs** günlük türünü seçin.
   7. Ayarınızı kaydedin.

PowerShell, CLı veya REST API kullanarak kaynak günlüklerini etkinleştirmek için [Tanılama ayarları](../azure-monitor/essentials/diagnostic-settings.md) makalesini ziyaret edin.

### <a name="access-resource-logs"></a>Kaynak günlüklerine erişin

Günlüklere erişmenin yolu, seçtiğiniz uç noktaya bağlıdır. Azure depolama için [günlük depolama hesabı](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) makalesine bakın. Event Hubs için bkz. [Azure günlükleri akışı](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs) makalesi.

Azure Izleyici günlükleri için Günlükler seçtiğiniz çalışma alanına gönderilir. Postgres günlükleri **AzureDiagnostics** Collection modunu kullanır, bu nedenle AzureDiagnostics tablosundan sorgulanırlar. Tablodaki alanlar aşağıda açıklanmıştır. [Azure Izleyici günlükleri sorgusuna](../azure-monitor/logs/log-query-overview.md) genel bakış bölümünde sorgulama ve uyarı alma hakkında daha fazla bilgi edinin.

Kullanmaya başlamak için kullanabileceğiniz sorgular aşağıda verilmiştir. Sorgular temelinde uyarıları yapılandırabilirsiniz.

Son gün içinde belirli bir sunucu için tüm Postgres günlüklerini arayın
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where Category == "PostgreSQLLogs"
| where TimeGenerated > ago(1d) 
```

Localhost olmayan tüm bağlantı girişimlerini ara
```
AzureDiagnostics
| where Message contains "connection received" and Message !contains "host=127.0.0.1"
| where Category == "PostgreSQLLogs" and TimeGenerated > ago(6h)
```
Yukarıdaki sorguda, bu çalışma alanındaki tüm Postgres sunucusu günlüğü için son 6 saat içindeki sonuçlar gösterilecektir.

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
- [Azure Portal](howto-configure-server-logs-in-portal.md) veya [Azure CLI](howto-configure-server-logs-using-cli.md)'dan günlüklere erişme hakkında daha fazla bilgi edinin.
- [Azure izleyici fiyatlandırması](https://azure.microsoft.com/pricing/details/monitor/)hakkında daha fazla bilgi edinin.
- [Denetim günlükleri](concepts-audit.md) hakkında daha fazla bilgi edinin