---
title: PostgreSQL için Azure veritabanı 'nda pgAudit kullanarak günlüğü denetleme-tek sunucu
description: PostgreSQL için Azure veritabanı 'nda pgAudit denetim günlüğü için kavramlar-tek sunucu.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/14/2019
ms.openlocfilehash: 49ad7334c418e29c821320608be729e060b4a8ae
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331324"
---
# <a name="audit-logging-in-azure-database-for-postgresql---single-server"></a>PostgreSQL için Azure veritabanı 'nda denetim günlüğü-tek sunucu

PostgreSQL için Azure veritabanı 'nda veritabanı etkinliklerini denetleme-tek sunucu, PostgreSQL Denetim Uzantısı aracılığıyla kullanılabilir: [pgaudit](https://www.pgaudit.org/). pgAudit ayrıntılı oturum ve/veya nesne denetim günlüğü sağlar.

> [!NOTE]
> pgAudit, PostgreSQL için Azure veritabanı 'nda önizlemededir.
> Uzantı yalnızca Genel Amaçlı ve bellek için Iyileştirilmiş sunucularda etkinleştirilebilir.

İşlem ve depolama Ölçeklendirmesi gibi işlemler için Azure Kaynak düzeyinde Günlükler istiyorsanız bkz. [Azure etkinlik günlüğü](../azure-monitor/platform/activity-logs-overview.md).

## <a name="usage-considerations"></a>Kullanım konuları
Varsayılan olarak, Postgres'in standart günlük özelliği kullanılarak pgAudit günlük deyimleri normal günlük deyimlerinizle birlikte yayılır. PostgreSQL için Azure Veritabanı'nda bu .log dosyaları Azure portalı veya CLI aracılığıyla indirilebilir. Dosya koleksiyonu için maksimum depolama alanı 1 GB ve her dosya en fazla yedi gün için kullanılabilir (varsayılan değer üç gündür). Bu hizmet, kısa süreli bir depolama seçeneğidir.

Alternatif olarak, tüm günlüklerin Azure Izleyici 'nin tanılama günlüğü hizmetine yayınlanacağını yapılandırabilirsiniz. Azure Izleyici tanılama günlüğünü etkinleştirirseniz, sizin seçiminize bağlı olarak günlüklerinizi Azure depolama, Event Hubs ve/veya Azure Izleyici günlüklerine otomatik olarak (JSON biçiminde) gönderilir.

pgAudit'in etkinleştirilmesi sunucuda çok büyük hacimli bir günlük oluşturur, bu da performansı ve günlük depolamasını etkiler. Hem daha uzun vadeli depolama seçenekleri hem de analiz ve uyarı özellikleri sunan Azure tanılama günlüğü hizmetini kullanmanızı öneririz. Ek günlüğün performans üzerindeki etkisini azaltmak için standart günlüğü kapatmanızı öneririz:

   1. @No__t-0 parametresini OFF olarak ayarlayın. 
   2. Bu değişikliği uygulamak için sunucuyu yeniden başlatın.

Azure depolama, Event Hubs veya Azure Izleyici günlüklerine günlük kaydı ayarlamayı öğrenmek için [sunucu günlükleri makalesinin](concepts-server-logs.md)tanılama günlükleri bölümünü ziyaret edin.

## <a name="installing-pgaudit"></a>PgAudit yükleniyor

PgAudit 'ı yüklemek için sunucunun paylaşılan önyükleme kitaplıklarına dahil etmeniz gerekir. Postgres 'nin `shared_preload_libraries` parametresinin değişikliği, sunucu yeniden başlatmanın etkili olmasını gerektirir. [Azure Portal](howto-configure-server-parameters-using-portal.md), [Azure CLI](howto-configure-server-parameters-using-cli.md)veya [REST API](/rest/api/postgresql/configurations/createorupdate)kullanarak parametreleri değiştirebilirsiniz.

[Azure Portal](https://portal.azure.com)kullanarak:

   1. PostgreSQL için Azure Veritabanı sunucunuzu seçin.
   2. Kenar çubuğunda **sunucu parametreleri**' ni seçin.
   3. @No__t-0 parametresini arayın.
   4. **Pgaudit**öğesini seçin.
   5. Değişikliği uygulamak için sunucuyu yeniden başlatın.

   6. Bir istemciyi (psql gibi) kullanarak sunucunuza bağlanın ve pgAudit uzantısını etkinleştirin
      ```SQL
      CREATE EXTENSION pgaudit;
      ```

> [!TIP]
> Bir hata görürseniz, `shared_preload_libraries` ' ı kaydettikten sonra sunucunuzu yeniden başlattığınızdan emin olun.

## <a name="pgaudit-settings"></a>pgAudit ayarları

pgAudit oturum veya nesne denetim günlüğünü yapılandırmanızı sağlar. [Oturum denetim günlüğü](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) , yürütülen deyimlerin ayrıntılı günlüklerini yayar. [Nesne denetim günlüğü](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) , belirli ilişkilerin kapsama alınmış bir denetim. Günlük kayıt türlerinin birini veya ikisini birden ayarlamayı seçebilirsiniz. 

> [!NOTE]
> pgAudit ayarları, genel olarak belirtilmiştir ve bir veritabanı veya rol düzeyinde belirtilemez.

[PgAudit](#installing-pgaudit)' i yükledikten sonra, parametrelerini günlüğe kaydetmeyi başlatacak şekilde yapılandırabilirsiniz. [Pgaudit belgeleri](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) her parametrenin tanımını sağlar. Önce parametreleri test edin ve beklenen davranışı aldığınızı onaylayın.

> [!NOTE]
> @No__t-0 olarak ayarlanması, günlükleri dosyaya yazılması yerine bir istemci işlemine (psql gibi) yönlendirir. Bu ayar genellikle devre dışı bırakılmalıdır.

> [!NOTE]
> `pgaudit.log_level` yalnızca `pgaudit.log_client` olduğunda etkinleştirilir. Ayrıca, Azure portal Şu anda `pgaudit.log_level` ile bir hata vardır: Birleşik giriş kutusu, birden çok düzeyin seçilebilecek şekilde görünür. Ama tek bir düzey seçilmelidir. 

> [!NOTE]
> PostgreSQL için Azure veritabanı 'nda, `pgaudit.log`, pgAudit belgelerinde açıklandığı şekilde `-` (eksi) işareti kısayolu kullanılarak ayarlanamaz. Tüm gereken deyim sınıfları (READ, WRITE vb.) tek tek belirtilmelidir.

### <a name="audit-log-format"></a>Denetim günlüğü biçimi
Her denetim girişi, günlük satırının başlangıcına yakın `AUDIT:` ile belirtilir. Girişin geri kalanının biçimi [Pgaudit belgelerinde](https://github.com/pgaudit/pgaudit/blob/master/README.md#format)ayrıntılıdır.

Denetim gereksinimlerinizi karşılamak için başka bir alana ihtiyaç duyuyorsanız, Postgres parametresini `log_line_prefix` ' ı kullanın. `log_line_prefix`, her Postgres günlük satırının başlangıcında çıktı olan bir dizedir. Örneğin, aşağıdaki `log_line_prefix` ayarı zaman damgası, Kullanıcı adı, veritabanı adı ve işlem KIMLIĞI sağlar:

```
t=%m u=%u db=%d pid=[%p]:
```

@No__t-0 hakkında daha fazla bilgi edinmek için [PostgreSQL belgelerini](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LINE-PREFIX)ziyaret edin.

### <a name="getting-started"></a>Başlangıç
Hızlı bir şekilde başlamak için `pgaudit.log` ' ı `WRITE` olarak ayarlayın ve çıktıyı gözden geçirmek için günlüklerinizi açın. 


## <a name="next-steps"></a>Sonraki adımlar
- [PostgreSQL için Azure veritabanı 'nda günlüğe kaydetme hakkında bilgi edinin](concepts-server-logs.md)
- [Azure Portal](howto-configure-server-parameters-using-portal.md), [Azure CLI](howto-configure-server-parameters-using-cli.md)veya [REST API](/rest/api/postgresql/configurations/createorupdate)kullanarak parametrelerin nasıl ayarlanacağını öğrenin.
