---
title: Yönetilebilirlik ve izleme-sorgu etkinliği, kaynak kullanımı
description: Azure SQL veri ambarı 'nı yönetmek ve izlemek için kullanabileceğiniz özellikleri öğrenin. Sorgu etkinliğini ve veri ambarınızın kaynak kullanımını anlamak için Azure portal ve dinamik yönetim görünümlerini (DMVs) kullanın.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/09/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 1a210e2622212ed59dfa12f9f9a108c6ffe08714
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692905"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-sql-data-warehouse"></a>Azure SQL veri ambarı 'nda kaynak kullanımını ve sorgu etkinliğini izleme
Azure SQL veri ambarı, veri ambarı iş yükünüze yönelik içgörüler için Azure portal içinde zengin bir izleme deneyimi sağlar. Azure portal, veri Ambarınızı izlerken, ölçüm ve Günlükler için yapılandırılabilir bekletme dönemleri, uyarılar, öneriler ve özelleştirilebilir grafikler ve panolar sağladığından önerilen araçtır. Portal ayrıca Operations Management Suite (OMS) ve Azure Izleyici (Günlükler) gibi diğer Azure izleme hizmetleri ile tümleştirmenize olanak tanıdığından, yalnızca veri Ambarınızla değil, tüm Azure analizlerinizi değil de bir bütünsel izleme deneyimi sağlar tümleşik izleme deneyimi için platform. Bu belgelerde, SQL veri ambarı ile analiz platformunuzu iyileştirmek ve yönetmek için kullanabileceğiniz izleme özellikleri açıklanmaktadır. 

## <a name="resource-utilization"></a>Kaynak kullanımı 
SQL veri ambarı için Azure portal aşağıdaki ölçümler mevcuttur. Bu ölçümler [Azure izleyici](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection#metrics)aracılığıyla ortaya çıkmış.


| Ölçüm adı             | Açıklama                                                  | Toplama türü |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| CPU yüzdesi          | Veri ambarı için tüm düğümlerde CPU kullanımı      | Maksimum          |
| Veri G/Ç yüzdesi      | Veri ambarı için tüm düğümlerde GÇ kullanımı       | Maksimum          |
| Bellek yüzdesi       | Veri ambarı için tüm düğümlerde bellek kullanımı (SQL Server) | Maksimum          |
| Başarılı bağlantılar  | Verilere yönelik başarılı bağlantı sayısı                 | Toplam            |
| Başarısız Bağlantılar      | Veri ambarına yönelik başarısız bağlantı sayısı           | Toplam            |
| Güvenlik duvarı tarafından engellendi     | Engellenen veri ambarına ait oturum açma sayısı     | Toplam            |
| DWU sınırı               | Veri ambarının hizmet düzeyi hedefi                | Maksimum          |
| DWU yüzdesi          | CPU yüzdesi ile veri GÇ yüzdesi arasındaki en fazla        | Maksimum          |
| Kullanılan DWU                | DWU sınırı * DWU yüzdesi                                   | Maksimum          |
| İsabetli önbellek okuması yüzdesi    | (önbellek isabet sayısı/önbellek isabetsizliği) * 100, önbellek isabetlerinin yerel SSD önbelleğindeki tüm columnstore segmentlerinin toplamı olduğu ve önbellek isabetsizlik, tüm düğümlerde toplanan yerel SSD önbelleğinde bulunan columnstore kesimlerinin isabetsizliği | Maksimum          |
| Önbellek kullanılan yüzde   | (kullanılan önbellek/önbellek kapasitesi) * 100, kullanılan önbellek tüm düğümlerde yerel SSD önbelleğindeki tüm baytların toplamı ve önbellek kapasitesi tüm düğümlerde yerel SSD önbelleğinin depolama kapasitesinin toplamıdır | Maksimum          |
| Yerel tempdb yüzdesi | Tüm işlem düğümlerinde yerel tempdb kullanımı-değerler her beş dakikada bir dağıtılır | Maksimum          |

> Ölçümleri görüntülerken ve uyarıları ayarlarken dikkate alınması gereken noktalar:
>
> - Başarısız ve başarılı bağlantılar, belirli bir veri ambarı için (mantıksal sunucu için değil) raporlanır
> - Veri ambarı boşta durumunda olsa bile bellek yüzdesi kullanımı yansıtır; etkin iş yükü bellek tüketimini yansıtmaz. Ek önbellek kapasitesi için ölçeklendirmenin, gereksinimlerinizi karşılamak üzere iş yükü performansını artırabilmesi için bu ölçümü diğer kullanıcılarla birlikte (tempdb, Gen2 Cache) kullanın ve izleyin.


## <a name="query-activity"></a>Sorgu etkinliği
SQL veri ambarı 'nı T-SQL aracılığıyla izlerken programlama deneyimi için, hizmet bir dizi dinamik yönetim görünümü (DMVs) sağlar. Bu görünümler, iş yükünüzün performans sorunlarını etkin bir şekilde gidermeye ve tanımlamaya yönelik olarak faydalıdır.

SQL veri ambarı 'nın sağladığı DMVs listesini görüntülemek için bu [belgeye](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-system-views#sql-data-warehouse-dynamic-management-views-dmvs)bakın. 

## <a name="metrics-and-diagnostics-logging"></a>Ölçümler ve tanılama günlüğü
Hem ölçümler hem de Günlükler Azure izleyici 'ye, özellikle de [Azure izleyici günlükleri](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) bileşenine aktarılabilir ve [günlük sorgularıyla](https://docs.microsoft.com/azure/log-analytics/log-analytics-tutorial-viewdata)programlı olarak erişilebilir. SQL veri ambarı için günlük gecikme süresi yaklaşık 10-15 dakikadır. Gecikme süresini etkileyen faktörler hakkında daha fazla bilgi için aşağıdaki belgeleri ziyaret edin.


## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki nasıl yapılır kılavuzlarında, veri Ambarınızı izlerken ve yönetirken yaygın senaryolar ve kullanım örnekleri açıklanır:

- [DMV 'ler ile veri ambarı iş yükünüzü izleme](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor)
