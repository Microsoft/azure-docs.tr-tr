---
title: Denetim günlüğü-PostgreSQL için Azure veritabanı-tek sunucu
description: PostgreSQL için Azure veritabanı 'nda pgAudit denetim günlüğü için kavramlar-tek sunucu.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 33fa6420f52cae9c869cc75a04ea82de0ec48262
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100596291"
---
# <a name="audit-logging-in-azure-database-for-postgresql---single-server"></a>PostgreSQL için Azure veritabanı 'nda denetim günlüğü-tek sunucu

PostgreSQL için Azure veritabanı 'nda veritabanı etkinliklerini denetleme-tek sunucu, PostgreSQL Denetim Uzantısı aracılığıyla kullanılabilir: [pgaudit](https://www.pgaudit.org/). pgAudit ayrıntılı oturum ve/veya nesne denetim günlüğü sağlar.

> [!NOTE]
> pgAudit, PostgreSQL için Azure veritabanı 'nda önizlemededir.
> Uzantı yalnızca Genel Amaçlı ve bellek için Iyileştirilmiş sunucularda etkinleştirilebilir.

İşlem ve depolama Ölçeklendirmesi gibi işlemler için Azure Kaynak düzeyinde Günlükler istiyorsanız bkz. [Azure etkinlik günlüğü](../azure-monitor/essentials/platform-logs-overview.md).

## <a name="usage-considerations"></a>Kullanım konuları
Varsayılan olarak, Postgres'in standart günlük özelliği kullanılarak pgAudit günlük deyimleri normal günlük deyimlerinizle birlikte yayılır. PostgreSQL için Azure Veritabanı'nda bu .log dosyaları Azure portalı veya CLI aracılığıyla indirilebilir. Dosya koleksiyonu için maksimum depolama alanı 1 GB ve her dosya en fazla yedi gün için kullanılabilir (varsayılan değer üç gündür). Bu hizmet, kısa süreli bir depolama seçeneğidir.

Alternatif olarak, Log Analytics ' de daha sonra analiz için Azure Izleyici günlük deposuna gönderilecek tüm günlükleri yapılandırabilirsiniz. Azure Izleyici kaynak günlüğünü etkinleştirirseniz, günlüklerinizin seçiminize bağlı olarak Azure depolama, Event Hubs ve/veya Azure Izleyici günlüklerine otomatik olarak (JSON biçiminde) gönderilmesi gerekir.

pgAudit'in etkinleştirilmesi sunucuda çok büyük hacimli bir günlük oluşturur, bu da performansı ve günlük depolamasını etkiler. Daha uzun vadeli depolama seçenekleri sunan Azure Izleyici günlüklerini ve analiz ve uyarı özelliklerini kullanmanızı öneririz. Ek günlüğün performans üzerindeki etkisini azaltmak için standart günlüğü kapatmanızı öneririz:

   1. Parametreyi `logging_collector` kapalı olarak ayarlayın. 
   2. Bu değişikliği uygulamak için sunucuyu yeniden başlatın.

Azure depolama, Event Hubs veya Azure Izleyici günlüklerine günlük kaydı ayarlamayı öğrenmek için [sunucu günlükleri makalesinin](concepts-server-logs.md)kaynak günlükleri bölümünü ziyaret edin.

## <a name="installing-pgaudit"></a>PgAudit yükleniyor

PgAudit 'ı yüklemek için sunucunun paylaşılan önyükleme kitaplıklarına dahil etmeniz gerekir. Postgres 'nin parametresinin bir değişikliği, `shared_preload_libraries` sunucu yeniden başlatmanın etkili olmasını gerektirir. [Azure Portal](howto-configure-server-parameters-using-portal.md), [Azure CLI](howto-configure-server-parameters-using-cli.md)veya [REST API](/rest/api/postgresql/configurations/createorupdate)kullanarak parametreleri değiştirebilirsiniz.

[Azure Portal](https://portal.azure.com)kullanarak:

   1. PostgreSQL için Azure Veritabanı sunucunuzu seçin.
   2. Kenar çubuğunda **sunucu parametreleri**' ni seçin.
   3. Parametresi için arama yapın `shared_preload_libraries` .
   4. **Pgaudit** öğesini seçin.
   5. Değişikliği uygulamak için sunucuyu yeniden başlatın.

   6. Bir istemciyi (psql gibi) kullanarak sunucunuza bağlanın ve pgAudit uzantısını etkinleştirin
      ```SQL
      CREATE EXTENSION pgaudit;
      ```

> [!TIP]
> Bir hata görürseniz, kaydettikten sonra sunucunuzu yeniden başlattığınızdan emin olun `shared_preload_libraries` .

## <a name="pgaudit-settings"></a>pgAudit ayarları

pgAudit oturum veya nesne denetim günlüğünü yapılandırmanızı sağlar. [Oturum denetim günlüğü](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) , yürütülen deyimlerin ayrıntılı günlüklerini yayar. [Nesne denetim günlüğü](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) , belirli ilişkilerin kapsama alınmış bir denetim. Günlük kayıt türlerinin birini veya ikisini birden ayarlamayı seçebilirsiniz. 

> [!NOTE]
> pgAudit ayarları, genel olarak belirtilmiştir ve bir veritabanı veya rol düzeyinde belirtilemez.

[PgAudit](#installing-pgaudit)' i yükledikten sonra, parametrelerini günlüğe kaydetmeyi başlatacak şekilde yapılandırabilirsiniz. [Pgaudit belgeleri](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) her parametrenin tanımını sağlar. Önce parametreleri test edin ve beklenen davranışı aldığınızı onaylayın.

> [!NOTE]
> `pgaudit.log_client`Olarak ayarlandığında, günlükleri dosyaya yazılması yerine bir istemci işlemine (psql gibi) yönlendirir. Bu ayar genellikle devre dışı bırakılmalıdır. <br> <br>
> `pgaudit.log_level` parametresi ancak `pgaudit.log_client` açık olduğunda etkinleştirilir.

> [!NOTE]
> PostgreSQL için Azure veritabanı 'nda, `pgaudit.log` `-` pgaudit belgelerinde açıklandığı şekilde (eksi) işareti kısayolu kullanılarak ayarlanamaz. Tüm gereken deyim sınıfları (READ, WRITE vb.) tek tek belirtilmelidir.

### <a name="audit-log-format"></a>Denetim günlüğü biçimi
Her denetim girişi, `AUDIT:` günlük satırının başlangıcında yakınında gösterilir. Girişin geri kalanının biçimi [Pgaudit belgelerinde](https://github.com/pgaudit/pgaudit/blob/master/README.md#format)ayrıntılıdır.

Denetim gereksinimlerinizi karşılamak için başka bir alana ihtiyacınız varsa, Postgres parametresini kullanın `log_line_prefix` . `log_line_prefix` Her Postgres günlük satırının başlangıcında çıktı olan bir dizedir. Örneğin, aşağıdaki `log_line_prefix` ayar zaman damgası, Kullanıcı adı, veritabanı adı ve Işlem kimliği sağlar:

```
t=%m u=%u db=%d pid=[%p]:
```

Hakkında daha fazla bilgi edinmek için `log_line_prefix` [PostgreSQL belgelerini](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LINE-PREFIX)ziyaret edin.

### <a name="getting-started"></a>Kullanmaya başlama
Hızlıca kullanmaya başlamak için, `pgaudit.log` olarak ayarlayın `WRITE` ve çıktıyı gözden geçirmek için günlüklerinizi açın. 

## <a name="viewing-audit-logs"></a>Denetim günlüklerini görüntüleme
. Log dosyalarını kullanıyorsanız, denetim günlüklerinizin PostgreSQL hata günlüklerinizin bulunduğu dosyaya dahil edilir. Günlük dosyalarını Azure [portalından](howto-configure-server-logs-in-portal.md) veya [CLI](howto-configure-server-logs-using-cli.md)'dan indirebilirsiniz. 

Azure kaynak günlüğü 'nü kullanıyorsanız, günlüklere erişme yönteminiz seçtiğiniz uç noktaya göre değişir. Azure depolama için [günlük depolama hesabı](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) makalesine bakın. Event Hubs için bkz. [Azure günlükleri akışı](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs) makalesi.

Azure Izleyici günlükleri için Günlükler seçtiğiniz çalışma alanına gönderilir. Postgres günlükleri **AzureDiagnostics** Collection modunu kullanır, bu nedenle AzureDiagnostics tablosundan sorgulanırlar. Tablodaki alanlar aşağıda açıklanmıştır. [Azure Izleyici günlükleri sorgusuna](../azure-monitor/logs/log-query-overview.md) genel bakış bölümünde sorgulama ve uyarı alma hakkında daha fazla bilgi edinin.

Başlamak için bu sorguyu kullanabilirsiniz. Sorgular temelinde uyarıları yapılandırabilirsiniz.

Son gün içinde belirli bir sunucu için tüm Postgres günlüklerini arayın
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>Sonraki adımlar
- [PostgreSQL için Azure veritabanı 'nda günlüğe kaydetme hakkında bilgi edinin](concepts-server-logs.md)
- [Azure Portal](howto-configure-server-parameters-using-portal.md), [Azure CLI](howto-configure-server-parameters-using-cli.md)veya [REST API](/rest/api/postgresql/configurations/createorupdate)kullanarak parametrelerin nasıl ayarlanacağını öğrenin.