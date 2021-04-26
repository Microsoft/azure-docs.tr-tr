---
title: Yönetilebilirlik ve izleme-sorgu etkinliği, kaynak kullanımı
description: Azure SYNAPSE Analytics 'i yönetmek ve izlemek için kullanabileceğiniz özellikleri öğrenin. Sorgu etkinliğini ve veri ambarınızın kaynak kullanımını anlamak için Azure portal ve dinamik yönetim görünümlerini (DMVs) kullanın.
services: synapse-analytics
author: julieMSFT
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/09/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 7b103991e22ffab8ed5bd2b3c10400330e1d09b3
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568412"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te kaynak kullanımını ve sorgu etkinliğini izleme

Azure SYNAPSE Analytics, veri ambarı iş yükünüz ile ilgili öngörülere yönelik Azure portal içinde zengin bir izleme deneyimi sağlar. Azure portal, veri Ambarınızı izlerken, ölçüm ve Günlükler için yapılandırılabilir bekletme dönemleri, uyarılar, öneriler ve özelleştirilebilir grafikler ve panolar sağladığından önerilen araçtır. Portal Ayrıca, yalnızca veri Ambarınızla değil, tümleşik bir izleme deneyimi için tüm Azure Analytics platformunuzu değil, bütünsel bir izleme deneyimi sağlamak amacıyla Log Analytics ile Azure Izleyici (Günlükler) gibi diğer Azure izleme hizmetleriyle tümleştirmenize olanak sağlar. Bu belgelerde, SYNAPSE SQL ile analiz platformunuzu iyileştirmek ve yönetmek için kullanabileceğiniz izleme özellikleri açıklanmaktadır.

## <a name="resource-utilization"></a>Kaynak kullanımı

Aşağıdaki ölçümler, SYNAPSE SQL için Azure portal kullanılabilir. Bu ölçümler [Azure izleyici](../../azure-monitor/data-platform.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json#metrics)aracılığıyla ortaya çıkmış.

| Ölçüm Adı             | Description                                                  | Toplama Türü |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| CPU yüzdesi          | Veri ambarı için tüm düğümlerde CPU kullanımı      | Ortalama, en düşük, en fazla    |
| Veri G/Ç yüzdesi      | Veri ambarı için tüm düğümlerde GÇ kullanımı       | Ortalama, en düşük, en fazla    |
| Bellek yüzdesi       | Veri ambarı için tüm düğümlerde bellek kullanımı (SQL Server) | Ortalama, en düşük, en fazla   |
| Etkin sorgular          | Sistemde yürütülen etkin sorguların sayısı             | Sum              |
| Kuyruğa alınmış sorgular          | Yürütülmeye başlamak için bekleyen sıraya alınmış sorgu sayısı          | Sum              |
| Başarılı bağlantılar  | Veritabanına yönelik başarılı bağlantı sayısı (oturum açma) | Toplam, sayı       |
| Başarısız Bağlantılar      | Veritabanına karşı başarısız bağlantı sayısı (oturum açma) | Toplam, sayı       |
| Güvenlik Duvarı Tarafından Engellendi     | Engellenen veri ambarına ait oturum açma sayısı     | Toplam, sayı       |
| DWU sınırı               | Veri ambarının hizmet düzeyi hedefi                | Ortalama, en düşük, en fazla    |
| DWU yüzdesi          | CPU yüzdesi ile veri GÇ yüzdesi arasındaki en fazla        | Ortalama, en düşük, en fazla    |
| Kullanılan DWU                | DWU sınırı * DWU yüzdesi                                   | Ortalama, en düşük, en fazla    |
| İsabetli önbellek okuması yüzdesi    | (önbellek isabet sayısı/önbellek isabetsizliği) * 100, önbellek isabetlerinin yerel SSD önbelleğindeki tüm columnstore segmentlerinin toplamı olduğu ve önbellek isabetsizlik, tüm düğümlerde toplanan yerel SSD önbelleğinde bulunan columnstore kesimlerinin isabetsizliği | Ortalama, en düşük, en fazla    |
| Önbellek kullanılan yüzde   | (kullanılan önbellek/önbellek kapasitesi) * 100, kullanılan önbellek tüm düğümlerde yerel SSD önbelleğindeki tüm baytların toplamı ve önbellek kapasitesi tüm düğümlerde yerel SSD önbelleğinin depolama kapasitesinin toplamıdır | Ortalama, en düşük, en fazla    |
| Yerel tempdb yüzdesi | Tüm işlem düğümlerinde yerel tempdb kullanımı-değerler her beş dakikada bir dağıtılır | Ortalama, en düşük, en fazla    |

Ölçümleri görüntülerken ve uyarıları ayarlarken dikkate alınması gereken noktalar:

- Kullanılan DWU, SQL havuzundaki **kullanım için yalnızca üst düzey bir temsili** temsil eder ve kapsamlı bir kullanım göstergesi olması anlamına gelir. Ölçeği artırma veya azaltma yapılıp yapılmayacağını anlamak için eşzamanlılık, bellek, tempdb ve Uyarlamalı önbellek kapasitesi gibi DWU tarafından etkilenen tüm faktörleri göz önünde bulundurun. İş hedeflerinizi karşılayacak en iyi şeyi belirlemek için iş [yükünüzü farklı DWU ayarlarında çalıştırmayı](sql-data-warehouse-manage-compute-overview.md#finding-the-right-size-of-data-warehouse-units) öneririz.
- Başarısız ve başarılı bağlantılar, belirli bir veri ambarı için (sunucunun kendisi için değil) raporlanır.
- Veri ambarı boşta durumunda olsa bile bellek yüzdesi kullanımı yansıtır; etkin iş yükü bellek tüketimini yansıtmaz. Ek önbellek kapasitesi için ölçeklendirmenin, gereksinimlerinizi karşılamak üzere iş yükü performansını artırabilmesi için bu ölçümü diğer kullanıcılarla birlikte (tempdb, Gen2 Cache) kullanın ve izleyin.

## <a name="query-activity"></a>Sorgu etkinliği

T-SQL aracılığıyla SYNAPSE SQL 'i izlerken bir programlama deneyimi için, hizmet bir dizi dinamik yönetim görünümü (DMVs) sağlar. Bu görünümler, iş yükünüzün performans sorunlarını etkin bir şekilde gidermeye ve tanımlamaya yönelik olarak faydalıdır.

SYNAPSE SQL 'e uygulanan DMVs listesini görüntülemek için bu [belgeye](../sql/reference-tsql-system-views.md#dedicated-sql-pool-dynamic-management-views-dmvs)bakın. 

## <a name="metrics-and-diagnostics-logging"></a>Ölçümler ve tanılama günlükleri 

Hem ölçümler hem de Günlükler Azure izleyici 'ye, özellikle de [Azure izleyici günlükleri](../../azure-monitor/logs/log-query-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) bileşenine aktarılabilir ve [günlük sorgularıyla](../../azure-monitor/logs/log-analytics-tutorial.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json)programlı olarak erişilebilir. SYNAPSE SQL için günlük gecikme süresi yaklaşık 10-15 dakikadır. Gecikme süresini etkileyen faktörler hakkında daha fazla bilgi için aşağıdaki belgeleri ziyaret edin.

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki nasıl yapılır kılavuzunda, veri Ambarınızı izlerken ve yönetirken yaygın senaryolar ve kullanım örnekleri açıklanmaktadır:

- [DMV 'ler ile veri ambarı iş yükünüzü izleme](sql-data-warehouse-manage-monitor.md)