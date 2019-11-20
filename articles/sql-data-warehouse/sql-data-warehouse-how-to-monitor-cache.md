---
title: Gen2 önbelleğinizi iyileştirin
description: Azure portal kullanarak Gen2 önbelleğinizi izlemeyi öğrenin.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: performance
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: b33f7cedca4ef130eefa28c1dbaaedd82d11a9e4
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73645762"
---
# <a name="how-to-monitor-the-gen2-cache"></a>Gen2 önbelleğini izleme
Gen2 depolama mimarisi, en sık sorgulanan columnstore segmentlerinizi, Gen2 veri ambarları için tasarlanan NVMe tabanlı SSD 'lerde bulunan bir önbellekte otomatik olarak katmanlar. Sorgularınızı önbellekte bulunan segmentleri aldığında daha fazla performans yapılır. Bu makalede, iş yükünüzün Gen2 önbelleğini en iyi şekilde kullanıp kullanmadığını belirleyerek yavaş sorgu performansının nasıl izleneceği ve giderileceği açıklanır.  
## <a name="troubleshoot-using-the-azure-portal"></a>Azure portal kullanarak sorun giderme
Sorgu performansının sorunlarını gidermek için Azure Izleyici 'yi Gen2 önbellek ölçümlerini görüntülemek için kullanabilirsiniz. Önce Azure portal gidin ve Izleyiciyi tıklatın:

![Azure İzleyici](./media/sql-data-warehouse-cache-portal/cache_0.png)

Ölçümler düğmesini seçin ve **abonelik**, **kaynak** **grubu**, **kaynak türü**ve veri ambarınızın **kaynak adını** girin.

Gen2 önbelleğinde sorun gidermeye yönelik temel ölçümler, **önbellek isabet yüzdesi** ve **kullanılan önbellek yüzdesidir**. Bu iki ölçümü göstermek için Azure ölçüm grafiğini yapılandırın.

![Önbellek ölçümleri](./media/sql-data-warehouse-cache-portal/cache_1.png)


## <a name="cache-hit-and-used-percentage"></a>Önbellek isabeti ve kullanılan yüzde
Aşağıdaki matris, önbellek ölçümlerinin değerlerine göre senaryolar anlatmaktadır:

|                                | **Yüksek önbellek isabet yüzdesi** | **Düşük önbellek isabet yüzdesi** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **Yüksek önbellek kullanılan yüzde** |          Senaryo 1           |          Senaryo 2          |
| **Düşük önbellek kullanılan yüzde**  |          Senaryo 3           |          Senaryo 4          |

**Senaryo 1:** Önbelleğinizi en iyi şekilde kullanıyorsunuz. Sorgularınızın yavaşlatılabilen diğer alanlarla [Ilgili sorunları giderin](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor) .

**Senaryo 2:** Geçerli çalışma verileri kümesi, fiziksel okumalar nedeniyle düşük önbellek okuması yüzdesine neden olan önbelleğe sığmayacak. Performans düzeyinizi ölçeklendirmenizi ve önbelleği doldurmak için iş yükünüzü yeniden çalıştırmayı göz önünde bulundurun.

**Senaryo 3:** Önbelleğiniz, önbellekte ilgisiz olan nedenlerden dolayı yavaş çalışıyor olabilir. Sorgularınızın yavaşlatılabilen diğer alanlarla [Ilgili sorunları giderin](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor) . Ayrıca, maliyetleri kaydetmek için önbelleğin boyutunu azaltmak üzere [örneğinizi ölçeklendirmeye](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor) de göz önünde bulundurmanız gerekir. 

**Senaryo 4:** Sorgunuzun yavaşlamasına neden olabilecek bir soğuk önbelleğiniz vardı. Çalışma Veri kümeniz şimdi önbelleğe alınmış olması gerektiği için sorgunuzu yeniden çalıştırmayı deneyin. 

**Önemli: iş yükünüzü yeniden çalıştırdıktan sonra önbellek isabet yüzdesi veya kullanılan önbellek yüzdesi güncelleştirilse, çalışma kümesi bellekte zaten bulunabilir. Note yalnızca kümelenmiş columnstore tabloları önbelleğe alınır.**

## <a name="next-steps"></a>Sonraki adımlar
Genel sorgu performansı ayarlama hakkında daha fazla bilgi için bkz. [izleyici sorgu yürütme](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor#monitor-query-execution).


<!--Image references-->

<!--Article references-->
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[System views]: ./sql-data-warehouse-reference-tsql-system-views.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Investigating queries waiting for resources]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm_pdw_dms_workers]: https://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_exec_sessions]: https://msdn.microsoft.com/library/mt203883.aspx
[sys.dm_pdw_request_steps]: https://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: https://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: https://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: https://msdn.microsoft.com/library/mt204028.aspx
[LABEL]: https://msdn.microsoft.com/library/ms190322.aspx
