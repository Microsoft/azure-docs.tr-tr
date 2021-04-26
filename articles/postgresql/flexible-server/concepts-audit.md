---
title: Denetim günlüğü-PostgreSQL için Azure veritabanı-esnek sunucu
description: PostgreSQL için Azure veritabanı 'nda pgAudit denetim günlüğü için kavramlar-esnek sunucu.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: b344e2a845a9da8333860599bd4ff9041108202f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100588257"
---
# <a name="audit-logging-in-azure-database-for-postgresql---flexible-server"></a>PostgreSQL için Azure veritabanı 'nda denetim günlüğü-esnek sunucu

PostgreSQL için Azure veritabanı 'nda veritabanı etkinliklerini denetleme-esnek sunucu, PostgreSQL Denetim Uzantısı aracılığıyla kullanılabilir: [pgaudit](https://www.pgaudit.org/). pgAudit ayrıntılı oturum ve/veya nesne denetim günlüğü sağlar.

> [!IMPORTANT]
> PostgreSQL için Azure veritabanı-esnek sunucu önizlemededir

İşlem ve depolama Ölçeklendirmesi gibi işlemler için Azure Kaynak düzeyinde Günlükler istiyorsanız bkz. [Azure etkinlik günlüğü](../../azure-monitor/essentials/platform-logs-overview.md).

## <a name="usage-considerations"></a>Kullanım konuları
Varsayılan olarak, Postgres'in standart günlük özelliği kullanılarak pgAudit günlük deyimleri normal günlük deyimlerinizle birlikte yayılır. PostgreSQL için Azure veritabanı-esnek sunucu ' da, tüm günlüklerin Log Analytics daha sonra analiz için Azure Izleyici günlük deposuna gönderilmesini sağlayabilirsiniz. Azure Izleyici kaynak günlüğünü etkinleştirirseniz, günlüklerinizin seçiminize bağlı olarak Azure depolama, Event Hubs ve/veya Azure Izleyici günlüklerine otomatik olarak (JSON biçiminde) gönderilmesi gerekir.

Azure depolama, Event Hubs veya Azure Izleyici günlüklerine günlük kaydı ayarlamayı öğrenmek için [sunucu günlükleri makalesinin](concepts-logging.md)kaynak günlükleri bölümünü ziyaret edin.

## <a name="enabling-pgaudit"></a>PgAudit etkinleştiriliyor

PgAudit ' i etkinleştirmek için, bir istemciyi (psql gibi) kullanarak sunucunuza bağlanmanız ve aşağıdaki komutu çalıştırarak pgAudit uzantısını etkinleştirmeniz gerekir:
```SQL
CREATE EXTENSION pgaudit;
```

## <a name="pgaudit-settings"></a>pgAudit ayarları

pgAudit oturum veya nesne denetim günlüğünü yapılandırmanızı sağlar. [Oturum denetim günlüğü](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) , yürütülen deyimlerin ayrıntılı günlüklerini yayar. [Nesne denetim günlüğü](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) , belirli ilişkilerin kapsama alınmış bir denetim. Günlük kayıt türlerinin birini veya ikisini birden ayarlamayı seçebilirsiniz. 

> [!NOTE]
> pgAudit ayarları genel olarak belirtilir ve bir veritabanı veya rol düzeyinde belirtilemez.

[PgAudit](#enabling-pgaudit)etkinleştirildikten sonra, günlük kaydını başlatacak şekilde parametrelerini yapılandırabilirsiniz. [Pgaudit belgeleri](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) her parametrenin tanımını sağlar. Önce parametreleri test edin ve beklenen davranışı aldığınızı onaylayın.

> [!NOTE]
> `pgaudit.log_client`Olarak ayarlandığında, günlükleri dosyaya yazılması yerine bir istemci işlemine (psql gibi) yönlendirir. Bu ayar genellikle devre dışı bırakılmalıdır. <br> <br>
> `pgaudit.log_level` parametresi ancak `pgaudit.log_client` açık olduğunda etkinleştirilir.

> [!NOTE]
> PostgreSQL için Azure veritabanı-esnek sunucu, `pgaudit.log` `-` pgaudit belgelerinde açıklandığı şekilde (eksi) işareti kısayolu kullanılarak ayarlanamaz. Tüm gerekli ifade sınıfları (okuma, yazma, vb.) tek tek belirtilmelidir.

## <a name="audit-log-format"></a>Denetim günlüğü biçimi
Her denetim girişi, `AUDIT:` günlük satırının başlangıcında yakınında gösterilir. Girişin geri kalanının biçimi [Pgaudit belgelerinde](https://github.com/pgaudit/pgaudit/blob/master/README.md#format)ayrıntılıdır.

## <a name="getting-started"></a>Kullanmaya başlama
Hızlıca kullanmaya başlamak için, `pgaudit.log` olarak ayarlayın `WRITE` ve çıktıyı gözden geçirmek için sunucu günlüklerinizi açın. 

## <a name="viewing-audit-logs"></a>Denetim günlüklerini görüntüleme
Günlüklere erişmenin yolu, seçtiğiniz uç noktaya bağlıdır. Azure depolama için [günlük depolama hesabı](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) makalesine bakın. Event Hubs için bkz. [Azure günlükleri akışı](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs) makalesi.

Azure Izleyici günlükleri için Günlükler seçtiğiniz çalışma alanına gönderilir. Postgres günlükleri **AzureDiagnostics** Collection modunu kullanır, bu nedenle AzureDiagnostics tablosundan sorgulanırlar. Tablodaki alanlar aşağıda açıklanmıştır. [Azure Izleyici günlükleri sorgusuna](../../azure-monitor/logs/log-query-overview.md) genel bakış bölümünde sorgulama ve uyarı alma hakkında daha fazla bilgi edinin.

Başlamak için bu sorguyu kullanabilirsiniz. Sorgular temelinde uyarıları yapılandırabilirsiniz.

Son gündeki belirli bir sunucu için Postgres günlüklerinde tüm pgAudit girdilerini arayın
```kusto
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>Sonraki adımlar
- [PostgreSQL için Azure veritabanı 'nda oturum açma-esnek sunucu hakkında bilgi edinin](concepts-logging.md)
- [PostgreSQL için Azure veritabanı 'nda günlüğü ayarlamayı öğrenin-esnek sunucu ve günlüklere erişme](howto-configure-and-access-logs.md)