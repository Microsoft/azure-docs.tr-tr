---
title: PostgreSQL için Azure veritabanı 'nda izleme ve ayarlama-hiper ölçek (Citus)
description: Bu makalede PostgreSQL için Azure veritabanı-hiper ölçek (Citus) içindeki izleme ve ayarlama özellikleri açıklanmaktadır
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 6c9bf7a427c0d3d26c3accd1d0d7098413916993
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512697"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---hyperscale-citus"></a>PostgreSQL için Azure veritabanı 'nı izleme ve ayarlama-hiper ölçek (Citus)

Sunucularınız hakkındaki izleme verileri, iş yükünüz için sorun gidermenize ve iyileştirmenize yardımcı olur. Hiper ölçek (Citus), bir sunucu grubundaki düğümlerin davranışına ilişkin Öngörüler sağlamak için çeşitli izleme seçenekleri sağlar.

## <a name="metrics"></a>Ölçümler

Hiper ölçek (Citus), bir sunucu grubundaki her bir düğüm için ölçümler sağlar. Ölçümler, destekleyici kaynakların davranışına ilişkin öngörüler sunar. Her ölçüm bir dakikalık sıklıkta yayılır ve 30 güne kadar geçmiş olur.

Ölçümlerin grafiklerini görüntülemenin yanı sıra, uyarıları da yapılandırabilirsiniz. Adım adım yönergeler için bkz. [uyarıları ayarlama](howto-hyperscale-alert-on-metric.md).  Diğer görevler otomatik eylemleri ayarlamayı, gelişmiş analizler çalıştırmayı ve arşivleme geçmişini içerir. Daha fazla bilgi için bkz. [Azure ölçümlerine genel bakış](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Ölçüm listesi

Bu ölçümler hiper ölçek (Citus) düğümleri için kullanılabilir:

|Ölçüm|Ölçüm görünen adı|Birim|Açıklama|
|---|---|---|---|
|active_connections|Etkin bağlantılar|Sayı|Sunucuya etkin bağlantı sayısı.|
|cpu_percent|CPU yüzdesi|Yüzde|Kullanımdaki CPU yüzdesi.|
|'ye|IOPS|Sayı|[IOPS tanımına](../virtual-machines/linux/premium-storage-performance.md#iops) ve [hiper ölçekli aktarım](concepts-hyperscale-configuration-options.md) hızına bakın|
|memory_percent|Bellek yüzdesi|Yüzde|Kullanımdaki belleğin yüzdesi.|
|network_bytes_ingress|Ağ Girişi|Sayacının|Etkin bağlantılar genelinde ağ.|
|network_bytes_egress|Ağ Çıkışı|Sayacının|Etkin bağlantılar arasında ağ çıkışı.|
|storage_percent|Depolama yüzdesi|Yüzde|Sunucunun en yüksek sınırının dışında kullanılan depolama alanı yüzdesi.|
|storage_used|Kullanılan depolama|Sayacının|Kullanımdaki depolama miktarı. Hizmet tarafından kullanılan depolama alanı, veritabanı dosyalarını, işlem günlüklerini ve sunucu günlüklerini içerebilir.|

Azure, küme için bir bütün olarak toplam ölçüm sağlar, ancak birden çok düğüm için ölçümler aynı grafiğe yerleştirilebilir.

## <a name="next-steps"></a>Sonraki adımlar

- Bir ölçüm üzerinde uyarı oluşturma konusunda rehberlik için [uyarıları ayarlama](howto-hyperscale-alert-on-metric.md) bölümüne bakın.
