---
title: Denetim günlüğü-PostgreSQL için Azure veritabanı-hiper ölçek (Citus)
description: PostgreSQL için Azure veritabanı 'nda, Pgscale denetim günlüğü için kavramlar-hiper ölçek (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 8a36062a2d29bcec10279d73211526a0dcba619e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101702122"
---
# <a name="audit-logging-in-azure-database-for-postgresql---hyperscale-citus"></a>PostgreSQL için Azure veritabanı 'nda denetim günlüğü-hiper ölçek (Citus)

PostgreSQL için Azure veritabanı 'nda veritabanı etkinliklerinin denetim günlüğü-hiper ölçek (Citus), PostgreSQL Denetim Uzantısı: [pgaudit](https://www.pgaudit.org/)ile kullanılabilir. pgAudit ayrıntılı oturum ve/veya nesne denetim günlüğü sağlar.

> [!IMPORTANT]
> pgAudit, PostgreSQL için Azure veritabanı 'nın önizlemededir-hiper ölçek (Citus)

İşlem ve depolama Ölçeklendirmesi gibi işlemler için Azure Kaynak düzeyinde Günlükler istiyorsanız bkz. [Azure etkinlik günlüğü](../azure-monitor/essentials/platform-logs-overview.md).

## <a name="usage-considerations"></a>Kullanım konuları
Varsayılan olarak, Postgres'in standart günlük özelliği kullanılarak pgAudit günlük deyimleri normal günlük deyimlerinizle birlikte yayılır. PostgreSQL için Azure veritabanı-hiper ölçek (Citus) içinde, Log Analytics ' de daha sonra analiz için Azure Izleyici günlük deposuna gönderilecek tüm günlükleri yapılandırabilirsiniz. Azure Izleyici kaynak günlüğünü etkinleştirirseniz, günlüklerinizin seçiminize bağlı olarak Azure depolama, Event Hubs ve/veya Azure Izleyici günlüklerine otomatik olarak (JSON biçiminde) gönderilmesi gerekir.

## <a name="enabling-pgaudit"></a>PgAudit etkinleştiriliyor

PgAudit uzantısı, tüm Hiperscale (Citus) sunucu grubu düğümlerinde önceden yüklenmiş ve etkinleştirilmiştir. Etkinleştirmek için herhangi bir eylem gerekmez.

## <a name="pgaudit-settings"></a>pgAudit ayarları

pgAudit oturum veya nesne denetim günlüğünü yapılandırmanızı sağlar. [Oturum denetim günlüğü](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) , yürütülen deyimlerin ayrıntılı günlüklerini yayar. [Nesne denetim günlüğü](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) , belirli ilişkilerin kapsama alınmış bir denetim. Günlük kayıt türlerinin birini veya ikisini birden ayarlamayı seçebilirsiniz. 

> [!NOTE]
> pgAudit ayarları genel olarak belirtilir ve bir veritabanı veya rol düzeyinde belirtilemez.
>
> Ayrıca, pgAudit ayarları bir sunucu grubunda düğüm başına belirtilir. Tüm düğümlerde değişiklik yapmak için, her bir düğüme tek tek uygulamanız gerekir.

Günlüğe kaydetmeyi başlatmak için pgAudit parametrelerini yapılandırmanız gerekir. [Pgaudit belgeleri](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) her parametrenin tanımını sağlar. Önce parametreleri test edin ve beklenen davranışı aldığınızı onaylayın.

> [!NOTE]
> `pgaudit.log_client`Olarak ayarlandığında, günlükleri dosyaya yazılması yerine bir istemci işlemine (psql gibi) yönlendirir. Bu ayar genellikle devre dışı bırakılmalıdır. <br> <br>
> `pgaudit.log_level` parametresi ancak `pgaudit.log_client` açık olduğunda etkinleştirilir.

> [!NOTE]
> PostgreSQL için Azure veritabanı-hiper ölçek (Citus), `pgaudit.log` `-` pgaudit belgelerinde açıklandığı şekilde (eksi) işareti kısayolu kullanılarak ayarlanamaz. Tüm gerekli ifade sınıfları (okuma, yazma, vb.) tek tek belirtilmelidir.

## <a name="audit-log-format"></a>Denetim günlüğü biçimi
Her denetim girişi, `AUDIT:` günlük satırının başlangıcında yakınında gösterilir. Girişin geri kalanının biçimi [Pgaudit belgelerinde](https://github.com/pgaudit/pgaudit/blob/master/README.md#format)ayrıntılıdır.

## <a name="getting-started"></a>Kullanmaya başlama
Hızlıca kullanmaya başlamak için, `pgaudit.log` olarak ayarlayın `WRITE` ve çıktıyı gözden geçirmek için sunucu günlüklerinizi açın. 

## <a name="viewing-audit-logs"></a>Denetim günlüklerini görüntüleme
Günlüklere erişmenin yolu, seçtiğiniz uç noktaya bağlıdır. Azure depolama için [günlük depolama hesabı](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) makalesine bakın. Event Hubs için bkz. [Azure günlükleri akışı](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs) makalesi.

Azure Izleyici günlükleri için Günlükler seçtiğiniz çalışma alanına gönderilir. Postgres günlükleri **AzureDiagnostics** Collection modunu kullanır, bu nedenle AzureDiagnostics tablosundan sorgulanırlar. Tablodaki alanlar aşağıda açıklanmıştır. [Azure Izleyici günlükleri sorgusuna](../azure-monitor/logs/log-query-overview.md) genel bakış bölümünde sorgulama ve uyarı alma hakkında daha fazla bilgi edinin.

Başlamak için bu sorguyu kullanabilirsiniz. Sorgular temelinde uyarıları yapılandırabilirsiniz.

Son gündeki belirli bir sunucu için Postgres günlüklerinde tüm pgAudit girdilerini arayın
```kusto
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>Sonraki adımlar

- [PostgreSQL için Azure veritabanı-hiper ölçek (Citus) ve günlüklere erişme hakkında bilgi edinin](howto-hyperscale-logging.md)