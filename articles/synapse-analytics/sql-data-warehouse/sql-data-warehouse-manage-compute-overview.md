---
title: Adanmış SQL havuzu için işlem kaynağını Yönet (eski adıyla SQL DW)
description: Azure SYNAPSE Analytics 'te adanmış SQL Havuzu (eski adıyla SQL DW) için performans ölçeği genişletme özellikleri hakkında bilgi edinin. Özel SQL havuzunu (eski adıyla SQL DW) duraklatarak DWUs 'ı ayarlayarak veya daha düşük maliyetlerle ölçeği azaltın.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/12/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 8c44e0570b08bba852d57fbb25da05d778ea8a0d
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105960121"
---
# <a name="manage-compute-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te adanmış SQL Havuzu (eski adıyla SQL DW) için işlem yönetme

Azure SYNAPSE Analytics 'te işlem kaynaklarını adanmış SQL Havuzu (eski adıyla SQL DW) yönetme hakkında bilgi edinin. Adanmış SQL havuzunu duraklatarak ve performans taleplerini karşılamak için adanmış SQL havuzunu ölçeklendirerek maliyetleri düşürün.

## <a name="what-is-compute-management"></a>İşlem yönetimi nedir?

Adanmış SQL havuzunun mimarisi (eski adıyla SQL DW), depolama ve işlem ayırır ve her birinin bağımsız olarak ölçeklendirilmesine olanak tanır. Sonuçta, performans taleplerini karşılamak için işlemi veri depolamasından bağımsız olarak ölçeklendirebilirsiniz. Ayrıca işlem kaynaklarını duraklatabilir ve sürdürebilirsiniz. Bu mimarinin doğal bir sonucu, işlem ve depolama için [faturalandırmaya](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) yöneliktir. Özel SQL havuzunuzu (eski adıyla SQL DW) bir süre kullanmanız gerekmiyorsa, işlem maliyetlerini duraklaleyerek işlem maliyetlerini kaydedebilirsiniz.

## <a name="scaling-compute"></a>Ölçeklendirme işlem

Adanmış SQL havuzunuz (eski adıyla SQL DW) için [veri ambarı birimleri](what-is-a-data-warehouse-unit-dwu-cdwu.md) ayarını ayarlayarak, işlem ölçeğini ölçeklendirebilir veya ölçeklendirebilirsiniz. Daha fazla veri ambarı birimi eklendikçe yükleme ve sorgu performansında doğrusal bir artış olur.

Genişleme adımları için [Azure Portal](quickstart-scale-compute-portal.md), [PowerShell](quickstart-scale-compute-powershell.md)veya [T-SQL](quickstart-scale-compute-tsql.md) quickbaşlangıçlara bakın. Ayrıca, bir [REST API](sql-data-warehouse-manage-compute-rest-api.md#scale-compute)genişleme işlemleri gerçekleştirebilirsiniz.

Bir ölçeklendirme işlemi gerçekleştirmek için, adanmış SQL Havuzu (eski adıyla SQL DW) ilk olarak tüm gelen sorguları çıkarır ve sonra tutarlı bir durum sağlamak için işlemleri geri kaydeder. Ölçeklendirme ancak işlemlerin geri alınması tamamlandıktan sonra gerçekleşir. Bir ölçeklendirme işlemi için sistem, depolama katmanını işlem düğümlerinden ayırır, işlem düğümlerini ekler ve ardından depolama katmanını Işlem katmanına yeniden ekler. Her adanmış SQL Havuzu (eski adıyla SQL DW), işlem düğümlerine eşit olarak dağıtılan 60 dağıtımları olarak depolanır. Daha fazla işlem düğümü eklemek daha fazla işlem gücü sağlar. İşlem düğümlerinin sayısı arttıkça, sorgular için daha fazla işlem gücü sağlayan işlem düğümü başına dağıtım sayısı azalır. Benzer şekilde, veri ambarı birimlerini düşürmek, sorguların işlem kaynaklarını azaltan işlem düğümü sayısını azaltır.

Aşağıdaki tabloda, veri ambarı birimleri değiştiğinde Işlem düğümü başına dağıtım sayısının nasıl değiştiği gösterilmektedir.  DW30000c 60 Işlem düğümleri sağlar ve DW100c ' den çok daha yüksek sorgu performansına erişir.

| Veri ambarı birimleri  | \# işlem düğümlerinin | \# düğüm başına dağıtım sayısı |
| -------- | ---------------- | -------------------------- |
| DW100c   | 1                | 60                         |
| DW200c   | 1                | 60                         |
| DW300c   | 1                | 60                         |
| DW400c   | 1                | 60                         |
| DW500c'yi seçin   | 1                | 60                         |
| DW1000c  | 2                | 30                         |
| DW1500c  | 3                | 20                         |
| DW2000c  | 4                | 15                         |
| DW2500c  | 5                | 12                         |
| DW3000c  | 6                | 10                         |
| DW5000c  | 10               | 6                          |
| DW6000c  | 12               | 5                          |
| DW7500c  | 15               | 4                          |
| DW10000c | 20               | 3                          |
| DW15000c | 30               | 2                          |
| DW30000c | 60               | 1                          |

## <a name="finding-the-right-size-of-data-warehouse-units"></a>Veri ambarı birimlerinin doğru boyutunu bulma

Özellikle büyük veri ambarı birimleri için, ölçeklendirmenin performans avantajlarını görmek için, en az 1 TB veri kümesi kullanmak istersiniz. Adanmış SQL havuzunuzun (eski adıyla SQL DW) en iyi veri ambarı birimi sayısını bulmak için ölçeği artırma ve azaltma seçeneğini deneyin. Verilerinizi yükledikten sonra farklı sayıda veri ambarı birimiyle birkaç sorgu çalıştırın. Ölçeklendirmenin hızlı olduğu için, çeşitli performans düzeylerini bir saat veya daha kısa sürede deneyebilirsiniz.

En iyi veri ambarı birimi sayısını bulmaya yönelik öneriler:

- Geliştirme sırasında adanmış bir SQL Havuzu (eski adıyla SQL DW) için, daha az sayıda veri ambarı birimi seçerek başlayın.  İyi bir başlangıç noktası DW400c veya DW200c.
- Uygulama performansınızı izleyip, gözlemlediğiniz performansa göre seçilen veri ambarı birimlerinin sayısını gözlemleyin.
- Doğrusal bir ölçek varsayın ve veri ambarı birimlerini ne kadar artırmanız veya azaltmanız gerektiğini belirleyebilirsiniz.
- İş gereksinimleriniz için en iyi performans düzeyine ulaşana kadar ayarlamalar yapmaya devam edin.

## <a name="when-to-scale-out"></a>Ne zaman Ölçeklendirilecek

Veri ambarı birimlerinin ölçeklendirilmesi, bu performans yönlerini etkiler:

- Linerken, taramalar, Toplamalar ve CTAS deyimlerinin sistem performansını geliştirir.
- Verileri yüklemek için okuyucu ve yazıcı sayısını artırır.
- En fazla eşzamanlı sorgu ve eşzamanlılık yuvası sayısı.

Veri ambarı birimlerinin ne zaman ölçeklenebilmesini sağlayacak öneriler:

- Yoğun veri yükleme veya dönüştürme işlemi gerçekleştirmeden önce, verileri daha hızlı kullanılabilir hale getirmek için ölçeği ölçeklendirin.
- Yoğun iş saatlerinde daha fazla sayıda eşzamanlı sorgu sağlamak için ölçeği ölçeklendirin.

## <a name="what-if-scaling-out-does-not-improve-performance"></a>Ölçeklendirmenin performansı artırmaz ne olursa?

Paralellik arttırılarak veri ambarı birimleri ekleme. İş, Işlem düğümleri arasında eşit olarak bölündüğünde, ek paralellik sorgu performansını geliştirir. Ölçeği genişletme, performansınızı değiştirmeiyorsa, bu nedenle oluşabilecek bazı nedenler vardır. Verileriniz dağıtımlar genelinde çarpıtılmış olabilir veya sorgular büyük miktarda veri hareketine giriş gösterebilir. Sorgu performans sorunlarını araştırmak için bkz. [Performans sorun giderme](sql-data-warehouse-troubleshoot.md#performance).

## <a name="pausing-and-resuming-compute"></a>İşlemi duraklatma ve sürdürme

İşlem duraklatma, depolama katmanının Işlem düğümlerinden ayrılmasına neden olur. İşlem kaynakları hesabınızdan serbest bırakılır. İşlem duraklatıldığında işlem için ücretlendirilirsiniz. İşlemi sürdürmek işlem düğümlerine yeniden iliştirmeye devam eder ve Işlem için ücretleri sürdürür.
Adanmış bir SQL havuzunu duraklattığınızda (eski adıyla SQL DW):

- İşlem ve bellek kaynakları, veri merkezindeki kullanılabilir kaynak havuzuna döndürülür
- Duraklama süresi için veri ambarı birim maliyetleri sıfırdır.
- Veri depolama etkilenmez ve verileriniz bozulmadan kalır.
- Tüm çalışan veya sıraya alınmış işlemler iptal edilir.
- DMV sayaçları sıfırlanır.

Ayrılmış bir SQL havuzunu (eski adıyla SQL DW) sürdürürseniz:

- Adanmış SQL Havuzu (eski adıyla SQL DW), veri ambarı birimleriniz ayarınız için işlem ve bellek kaynaklarını alır.
- Veri ambarı birimleriniz için işlem ücretleri sürdürülür.
- Verileriniz kullanılabilir hale gelir.
- Adanmış SQL Havuzu (eski adıyla SQL DW) çevrimiçi olduktan sonra iş yükü sorgularınızı yeniden başlatmanız gerekir.

Adanmış SQL havuzunuzun (eski adıyla SQL DW) erişilebilir olmasını istiyorsanız duraklatma yerine en küçük boyuta ölçeklendirebilirsiniz.

Duraklatma ve devam adımları için [Azure Portal](pause-and-resume-compute-portal.md)veya [PowerShell](pause-and-resume-compute-powershell.md) hızlı başlangıçlarını inceleyin. [Duraklatma REST API](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) veya [Resume REST API](sql-data-warehouse-manage-compute-rest-api.md#resume-compute)de kullanabilirsiniz.

## <a name="drain-transactions-before-pausing-or-scaling"></a>Duraklatma veya ölçeklendirme öncesinde işlemleri boşaltın

Duraklatma veya ölçeklendirme işlemi başlamadan önce mevcut işlemlerin bitmesini öneririz.

Adanmış SQL havuzunuzu (eski adıyla SQL DW) duraklattığınızda veya ölçeklendirirseniz, duraklatma veya ölçeklendirme isteğini başlattığınızda, yaptığınız arka planda sorgular iptal edilir. Basit bir SELECT sorgusunu hızlıca ve örnek duraklatma veya ölçeklendirme süresini neredeyse hiç etkilemeden iptal edebilirsiniz.  Ancak, verilerinizi veya verilerinizin yapısını değiştiren işlem sorguları o kadar hızlı durdurulamayabilir. **Bir işlem sorgusunun tamamlanması veya yaptığı değişiklikleri geri alması gerekir.** Bir işlem sorgusunun tamamladığı işi geri almak, sorgunun değişiklik yapmak için harcadığı süre kadar, hatta bazen daha fazla zaman alabilir. Örneğin, bir saattir çalışan ve satır silen bir sorguyu iptal etmeniz halinde sistemin silinmiş olan satırları geri eklemesi bir saat sürebilir. Duraklatma veya ölçeklendirme isteklerini işlemler devam ederken çalıştırmanız halinde, devam etmek için geri alma işleminin tamamlanmasını bekleyeceğinden ilgili duraklatma veya ölçeklendirme işleminin tamamlanması uzun sürebilir.

Ayrıca bkz. [Işlemleri anlama](sql-data-warehouse-develop-transactions.md)ve [işlemleri iyileştirme](sql-data-warehouse-develop-best-practices-transactions.md).

## <a name="automating-compute-management"></a>İşlem yönetimini otomatikleştirme

İşlem yönetimi işlemlerini otomatikleştirmek için bkz. [Azure işlevleri ile Işlem yönetme](manage-compute-with-azure-functions.md).

Ölçek Genişletme, duraklatma ve devam eden işlemlerin her birinin tamamlanması birkaç dakika sürebilir. Otomatik olarak ölçeklendirme yaptıysanız, durakladıysanız veya devam ettiriyorsanız, başka bir eyleme geçmeden önce belirli işlemlerin tamamlanmasını sağlamak için mantık uygulamanız önerilir. Adanmış SQL Havuzu (eski adıyla SQL DW) durumunun çeşitli uç noktalar aracılığıyla denetlenmesi, bu tür işlemlerin otomatikleştirilmesini doğru bir şekilde uygulamanıza olanak sağlar.

Adanmış SQL Havuzu (eski adıyla SQL DW) durumunu denetlemek için bkz. [PowerShell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) veya [T-SQL](quickstart-scale-compute-tsql.md#check-dedicated-sql-pool-formerly-sql-dw-state) hızlı başlangıç. Ayrıca, adanmış SQL Havuzu (eski adıyla SQL DW) durumunu bir [REST API](sql-data-warehouse-manage-compute-rest-api.md#check-database-state)kontrol edebilirsiniz.

## <a name="permissions"></a>İzinler

Adanmış SQL havuzunun ölçeklendirilmesi (eski adıyla SQL DW), [alter database](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)bölümünde açıklanan izinleri gerektirir.  Duraklatma ve devam etmeyi, özellikle Microsoft. SQL/Servers/Database/Action [SQL DB katılımcısı](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#sql-db-contributor) iznini gerektirir.

## <a name="next-steps"></a>Sonraki adımlar

[Yönetim](manage-compute-with-azure-functions.md) işlemi için nasıl yapılır Kılavuzu ' na bakın işlem kaynaklarını yönetmenin başka bir yönü de tekil sorgular için farklı işlem kaynakları ayırıyor. Daha fazla bilgi için bkz. [iş yükü yönetimi Için kaynak sınıfları](resource-classes-for-workload-management.md).
