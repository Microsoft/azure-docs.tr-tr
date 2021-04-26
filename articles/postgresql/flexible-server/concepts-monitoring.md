---
title: İzleme ve ölçümler-PostgreSQL için Azure veritabanı-esnek sunucu
description: Bu makalede PostgreSQL için Azure veritabanı 'nın (esnek sunucu) izleme ve ölçüm özellikleri açıklanmaktadır.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 3d26c585593161ccf4f8ec33f913cc7163531f89
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606666"
---
# <a name="monitor-metrics-on-azure-database-for-postgresql---flexible-server"></a>PostgreSQL için Azure veritabanı 'nda ölçümleri izleme-esnek sunucu

> [!IMPORTANT]
> PostgreSQL için Azure veritabanı-esnek sunucu önizlemededir

Sunucularınız hakkındaki izleme verileri, iş yükünüz için sorun gidermenize ve iyileştirmenize yardımcı olur. PostgreSQL için Azure veritabanı, sunucunuzun davranışına ilişkin Öngörüler sağlamak üzere çeşitli izleme seçenekleri sunar.

## <a name="metrics"></a>Ölçümler
PostgreSQL için Azure veritabanı, PostgreSQL sunucusunu destekleyen kaynakların davranışına fikir veren çeşitli ölçümler sağlar. Her ölçüm tek dakikalık bir sıklıkta yayılır ve [93 güne kadar geçmiş](../../azure-monitor/essentials/data-platform-metrics.md#retention-of-metrics)olur. Ölçümler üzerinde uyarılar yapılandırabilirsiniz. Diğer seçenekler otomatik eylemleri ayarlamayı, gelişmiş analiz gerçekleştirmeyi ve arşivleme geçmişini içerir. Daha fazla bilgi için bkz. [Azure ölçümlerine genel bakış](../../azure-monitor/essentials/data-platform-metrics.md).

### <a name="list-of-metrics"></a>Ölçüm listesi
PostgreSQL esnek sunucusu için aşağıdaki ölçümler mevcuttur:


|Metric|Ölçüm görünen adı|Birim|Description|
|---|---|---|---|
| active_connections | Etkin Bağlantılar | Count | Sunucunuza bağlantı sayısı. | 
| backup_storage_used | Kullanılan yedekleme depolama alanı | Bayt | Kullanılan yedekleme depolama miktarı. Bu ölçüm, tüm tam veritabanı yedeklemeleri, fark yedeklemeleri ve sunucu için ayarlanan yedekleme Bekletme dönemi temel alınarak korunan depolama alanının toplamını temsil eder. Yedeklemelerin sıklığı hizmet tarafından yönetilmektedir. Coğrafi olarak yedekli depolama için, yedekleme depolama alanı kullanımı yerel olarak yedekli depolama alanının iki katından oluşur. |
| connections_failed | Başarısız Bağlantılar | Count | Bağlantı başarısız oldu. |
| connections_succeeded | Başarılı bağlantılar | Count | Bağlantı başarılı oldu. |
| cpu_credits_consumed | Tüketilen CPU kredileri | Count | Esnek sunucu tarafından kullanılan kredi sayısı. Burstable katmana uygulanabilir. |
| cpu_credits_remaining | Kalan CPU kredileri | Count | Patlama için kullanılabilir kredi sayısı. Burstable katmana uygulanabilir. |
| cpu_percent | CPU yüzdesi | Yüzde | Kullanımdaki CPU yüzdesi. | 
| disk_queue_depth | Disk kuyruğu derinliği | Count | Veri diskine yönelik bekleyen g/ç işlemlerinin sayısı. |
| 'ye | IOPS | Count | Diske saniye başına g/ç işlemlerinin sayısı. |
| maximum_used_transactionIDs | Kullanılan en fazla Işlem kimliği | Count | Kullanımdaki işlem KIMLIĞI üst sınırı. |
| memory_percent | Bellek yüzdesi | Yüzde | Kullanımdaki belleğin yüzdesi. |
| network_bytes_egress | Ağ Çıkışı | Bayt | Giden ağ trafiği miktarı. |
| network_bytes_ingress | Ağ Girişi | Bayt | Gelen ağ trafiği miktarı. |
| read_iops | IOPS 'yi oku | Count | Saniye başına veri disk g/ç okuma işlemi sayısı. |
| read_throughput | Aktarım hızını oku | Bayt | Diskten saniye başına okunan bayt sayısı. |
| storage_free | Depolama alanı boş | Bayt | Kullanılabilir depolama alanı miktarı. |
| storage_percent | Depolama alanı yüzdesi | Yüzde | Kullanılan depolama alanı yüzdesi. Hizmet tarafından kullanılan depolama alanı, veritabanı dosyalarını, işlem günlüklerini ve sunucu günlüklerini içerebilir.|
| storage_used | Kullanılan depolama alanı | Bayt | Kullanılan depolama alanı yüzdesi. Hizmet tarafından kullanılan depolama alanı, veritabanı dosyalarını, işlem günlüklerini ve sunucu günlüklerini içerebilir. |
| txlogs_storage_used | Kullanılan işlem günlüğü depolaması | Bayt | İşlem günlükleri tarafından kullanılan depolama alanı miktarı. | 
| write_throughput | Yazma performansı | Bayt | Diske saniyede yazılan bayt sayısı. |
| write_iops | IOPS yaz | Count | Saniye başına veri disk g/ç yazma işlemi sayısı. |

## <a name="server-logs"></a>Sunucu günlükleri
PostgreSQL için Azure veritabanı, Postgres 'e yönelik standart günlüklere yapılandırma ve erişme olanağı sağlar. Günlükler hakkında daha fazla bilgi edinmek için [günlük kavramlarını](concepts-logging.md)ziyaret edin.
