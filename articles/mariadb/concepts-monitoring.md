---
title: İzleme-MariaDB için Azure veritabanı
description: Bu makalede CPU, depolama ve bağlantı istatistikleri dahil olmak üzere MariaDB için Azure veritabanı izleme ve uyarma ölçümleri açıklanmaktadır.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.custom: references_regions
ms.date: 10/21/2020
ms.openlocfilehash: f63b0b523184917097c73c852a7624b366fbbb24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100595212"
---
# <a name="monitoring-in-azure-database-for-mariadb"></a>MariaDB için Azure veritabanı 'nda izleme
Sunucularınız hakkındaki izleme verileri, iş yükünüz için sorun gidermenize ve iyileştirmenize yardımcı olur. MariaDB için Azure veritabanı, sunucunuzun davranışına ilişkin Öngörüler sağlayan çeşitli ölçümler sunar.

## <a name="metrics"></a>Ölçümler
Tüm Azure ölçümlerinin bir dakikalık sıklığı vardır ve her ölçüm 30 gün geçmiş sağlar. Ölçümler üzerinde uyarılar yapılandırabilirsiniz. Diğer görevler otomatik eylemleri ayarlamayı, gelişmiş analiz gerçekleştirmeyi ve arşivleme geçmişini içerir. Daha fazla bilgi için bkz. [Azure ölçümlerine genel bakış](../azure-monitor/data-platform.md).

Adım adım yönergeler için bkz. [uyarıları ayarlama](howto-alert-metric.md).

### <a name="list-of-metrics"></a>Ölçüm listesi
Bu ölçümler, MariaDB için Azure veritabanı 'nda kullanılabilir:

|Metric|Ölçüm görünen adı|Birim|Description|
|---|---|---|---|
|cpu_percent|CPU yüzdesi|Yüzde|Kullanımdaki CPU yüzdesi.|
|memory_percent|Bellek yüzdesi|Yüzde|Kullanımdaki belleğin yüzdesi.|
|io_consumption_percent|GÇ yüzdesi|Yüzde|Kullanımdaki GÇ yüzdesi. (Temel katman sunucuları için geçerli değildir)|
|storage_percent|Depolama yüzdesi|Yüzde|Sunucunun en yüksek sınırının dışında kullanılan depolama alanı yüzdesi.|
|storage_used|Kullanılan depolama alanı|Bayt|Kullanımdaki depolama miktarı. Hizmet tarafından kullanılan depolama alanı, veritabanı dosyalarını, işlem günlüklerini ve sunucu günlüklerini içerebilir.|
|serverlog_storage_percent|Sunucu günlüğü depolama yüzdesi|Yüzde|Sunucunun en yüksek sunucu günlük depolama alanı dışında kullanılan sunucu günlük depolama alanı yüzdesi.|
|serverlog_storage_usage|Kullanılan sunucu günlüğü depolaması|Bayt|Kullanımdaki sunucu günlüğü depolama miktarı.|
|serverlog_storage_limit|Sunucu günlüğü depolama sınırı|Bayt|Bu sunucu için en fazla sunucu günlük depolama alanı.|
|storage_limit|Depolama sınırı|Bayt|Bu sunucu için en fazla depolama alanı.|
|active_connections|Etkin Bağlantılar|Count|Sunucuya etkin bağlantı sayısı.|
|connections_failed|Başarısız Bağlantılar|Count|Sunucuya yönelik başarısız bağlantı sayısı.|
|seconds_behind_master|Saniye cinsinden çoğaltma gecikmesi|Count|Çoğaltma sunucusunun kaynak sunucuya karşı gecikme saniye sayısı. (Temel katman sunucuları için geçerli değildir)|
|network_bytes_egress|Ağ Çıkışı|Bayt|Etkin bağlantılar arasında ağ çıkışı.|
|network_bytes_ingress|Ağ Girişi|Bayt|Etkin bağlantılar genelinde ağ.|
|backup_storage_used|Kullanılan yedekleme depolama alanı|Bayt|Kullanılan yedekleme depolama alanı miktarı. Bu ölçüm, tüm tam veritabanı yedeklemeleri, fark yedeklemeleri ve sunucu için ayarlanan yedekleme Bekletme dönemi temel alınarak korunan depolama alanının toplamını temsil eder. Yedeklemelerin sıklığı hizmet olarak yönetilir ve [Kavramlar makalesinde](concepts-backup.md)açıklanmıştır. Coğrafi olarak yedekli depolama için, yedekleme depolama alanı kullanımı yerel olarak yedekli depolama alanının iki katından oluşur.|

## <a name="server-logs"></a>Sunucu günlükleri

Sunucunuzda yavaş sorgu günlüğünü etkinleştirebilirsiniz. Bu Günlükler Azure Izleyici günlükleri, Event Hubs ve depolama hesabı 'ndaki Azure tanılama günlükleri aracılığıyla da kullanılabilir. Günlüğe kaydetme hakkında daha fazla bilgi için [sunucu günlükleri](concepts-server-logs.md) sayfasını ziyaret edin.

## <a name="query-store"></a>Sorgu Deposu

Sorgu [deposu](concepts-query-store.md) sorgu çalışma zamanı istatistikleri ve bekleme olayları dahil olmak üzere zaman içinde sorgu performansını izler. Özellik, **MySQL** şemasında çalışma zamanı performans bilgilerini sorgu ile devam ettirir. Verilerin toplanmasını ve depolanmasını çeşitli yapılandırma düğmelerini kullanarak denetleyebilirsiniz.

## <a name="query-performance-insight"></a>Sorgu Performansı İçgörüleri

[Sorgu performansı içgörüleri](concepts-query-performance-insight.md) , Azure Portal erişilebilir görselleştirmeler sağlamak üzere sorgu deposuyla birlikte çalışarak. Bu grafikler, performansı etkileyen anahtar sorgularını tanımlamanızı sağlar. Sorgu Performansı İçgörüleri, MariaDB sunucunuzun portal sayfası için Azure veritabanı 'nın **akıllı performans** bölümünde erişilebilir.

## <a name="performance-recommendations"></a>Performans Önerileri

[Performans önerileri](concepts-performance-recommendations.md) özelliği, iş yükü performansını artırmaya yönelik fırsatları tanımlar. Performans önerileri, iş yüklerinizin performansını geliştirmek için potansiyel olarak yeni dizinler oluşturmaya yönelik öneriler sağlar. Dizin önerileri oluşturmak için, özelliği şema ve sorgu deposu tarafından bildirilen iş yükü dahil çeşitli veritabanı özelliklerini dikkate alır. Herhangi bir performans önerisi uygulandıktan sonra, müşteriler bu değişikliklerin etkisini değerlendirmek için performansı test etmelidir.

## <a name="planned-maintenance-notification"></a>Planlı bakım bildirimi

[Planlı bakım bildirimleri](./concepts-planned-maintenance-notification.md) , MariaDB Için Azure veritabanınıza yaklaşan planlı bakım için uyarı almanızı sağlar. Bu bildirimler [hizmet durumunun](../service-health/overview.md) planlanmış bakımıyla tümleşiktir ve abonelikleriniz için tüm zamanlanmış bakımı tek bir yerden görüntülemenize olanak sağlar. Ayrıca, farklı kaynaklardan sorumlu farklı kişileriniz olabileceğinden farklı kaynak grupları için bildirimin doğru kitlelere ölçeklendirilmesine de yardımcı olur. Olaydan önce yaklaşan bakım 72 saati hakkında bildirim alacaksınız.

[Planlı bakım bildirimleri](./concepts-planned-maintenance-notification.md) belgesinde bildirimleri ayarlama hakkında daha fazla bilgi edinin.

## <a name="next-steps"></a>Sonraki adımlar

- Azure portal, REST API veya CLı kullanarak ölçümlere erişme ve dışarı aktarma hakkında daha fazla bilgi için bkz. [Azure ölçümleri 'Ne genel bakış](../azure-monitor/data-platform.md).
- Bir ölçüm üzerinde uyarı oluşturma konusunda rehberlik için [uyarıları ayarlama](howto-alert-metric.md) bölümüne bakın.
- MariaDB için Azure veritabanı 'nda [Planlı bakım bildirimleri](./concepts-planned-maintenance-notification.md) hakkında daha fazla bilgi edinin.