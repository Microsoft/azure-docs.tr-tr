---
title: İş Açısından Kritik hizmet katmanı
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Azure SQL veritabanı ve Azure SQL yönetilen örneği için iş açısından kritik hizmet katmanı hakkında bilgi edinin.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: 830ecc44d0def13e51cb06704bef429bb8860cd6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92780232"
---
# <a name="business-critical-tier---azure-sql-database-and-azure-sql-managed-instance"></a>İş Açısından Kritik katmanı-Azure SQL veritabanı ve Azure SQL yönetilen örneği 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> İş Açısından Kritik katmana DTU satın alma modelinde Premium adı verilir. DTU tabanlı satın alma modeliyle sanal çekirdek tabanlı satın alma modeli karşılaştırması için bkz. [Azure SQL veritabanı satın alma modelleri ve kaynakları](purchasing-models.md).

Azure SQL veritabanı ve Azure SQL yönetilen örneği, altyapı hatalarının durumlarında bile% 99,99 kullanılabilirlik sağlamak için bulut ortamı için ayarlanmış SQL Server veritabanı altyapısı mimarisine dayalıdır. Kullanılan üç mimari modeli vardır:
- Genel Amaçlı/standart 
- İş Açısından Kritik/Premium
- Hiper Ölçek

Premium/İş Açısından Kritik hizmet katmanı modeli, bir veritabanı altyapısı işlemlerinin kümesini temel alır. Bu mimari model, kullanılabilir veritabanı altyapısı düğümlerinin her zaman bir çekirdeği olduğunu ve bakım etkinlikleri sırasında bile iş yükünüzde en az performans etkisi olduğunu bir olgusuna bağımlıdır. Hiper ölçekli hizmet katmanı Şu anda yalnızca Azure SQL veritabanı 'nda (SQL yönetilen örneği değil) kullanılabilir ve Azure SQL veritabanı 'ndaki bir veritabanının depolama ve işlem kaynaklarını, genel amaçlı ve iş açısından kritik hizmet katmanları için kullanılabilir limitlerin büyük ölçüde ötesinde ölçeğini genişletmek için Azure mimarisinden yararlanan yüksek düzeyde ölçeklenebilir bir depolama ve işlem performansı katmandır.

Azure, son kullanıcılar için en düşük düzeyde çalışan işletim sistemi, sürücü ve SQL Server veritabanı altyapısını şeffaf bir şekilde yükseltir ve düzeltme eklerini ekler. 

Premium kullanılabilirlik, Premium ve İş Açısından Kritik hizmet katmanlarında etkinleştirilir ve devam eden bakım işlemleri nedeniyle herhangi bir performans etkisi için bir performans etkisine neden olmayan yoğun iş yükleri için tasarlanmıştır.

İşlem ve depolama, Premium modeldeki tek düğüm üzerinde tümleşiktir. Bu mimari modelde yüksek kullanılabilirlik, [her zaman açık kullanılabilirlik grupları](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)SQL Server benzer teknolojiyi kullanarak, işlem (SQL Server veritabanı altyapısı işlemi) ve depolama (yerel olarak bağlı SSD) ile dört düğüm kümesine dağıtılan depolama (yerel olarak eklenmiş SSD) tarafından sağlanır.

![Veritabanı altyapısı düğümlerinin kümesi](./media/service-tier-business-critical/business-critical-service-tier.png)

Hem SQL Server veritabanı altyapısı işlemi hem de temel alınan. mdf/. ldf dosyaları, iş yükünüze düşük gecikme süresi sağlayan yerel olarak eklenmiş SSD depolama ile aynı düğüme yerleştirilir. Yüksek kullanılabilirlik, [her zaman açık kullanılabilirlik grupları](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)SQL Server benzer teknolojiler kullanılarak uygulanır. Her veritabanı, müşteri iş yükleri için erişilebilen tek bir birincil veritabanına sahip bir veritabanı düğümleri kümesi ve verilerin kopyalarını içeren üç ikincil işlem. Birincil düğüm, birincil düğümün herhangi bir nedenden dolayı başarısız olması durumunda, verilerin ikincil çoğaltmalarda kullanılabilir olmasını sağlamak için, değişiklikleri sürekli olarak ikincil düğümlere iter. Yük devretme SQL Server veritabanı altyapısı tarafından işlenir – bir ikincil çoğaltma birincil düğüm haline gelir ve kümede yeterli düğüm olduğundan emin olmak için yeni bir ikincil çoğaltma oluşturulur. İş yükü otomatik olarak yeni birincil düğüme yönlendirilir.

Ayrıca, İş Açısından Kritik kümede, birincil iş yükünüzün performansını etkilememesi gereken salt okuma sorgularını (örneğin, raporlar) çalıştırmak için kullanılabilecek, ücretsiz, yerleşik salt okuma düğümü sağlayan yerleşik [okuma ölçeği](read-scale-out.md) özelliği vardır.

## <a name="when-to-choose-this-service-tier"></a>Bu hizmet katmanını seçme

İş Açısından Kritik hizmet katmanı, temel alınan SSD depolamadan (ortalama 1-2 ms) düşük Gecikmeli yanıt gerektiren uygulamalar için tasarlanmıştır, temel alınan altyapı başarısız olursa hızlı kurtarma, birincil veritabanının ücretsiz olarak okunabilir ikincil çoğaltmasındaki, raporların ve analizlerin ve salt okunur sorguların yük boşaltma yapması gerekir.

Genel Amaçlı katmanı yerine İş Açısından Kritik hizmet katmanını seçmeniz gereken önemli nedenler şunlardır:
-   **Düşük g/ç gecikme süresi gereksinimleri** – depolama katmanından (ortalama 1-2 milisaniye) hızlı bir yanıt gerektiren iş yükleri iş açısından kritik katmanını kullanmalıdır. 
-   **Uygulama ve veritabanı arasındaki sık kullanılan iletişim**. Uygulama katmanı önbelleğe alma veya [istek toplu işleme](../performance-improve-use-batching.md) özelliğinden yararlanamaz uygulamalar ve hızlı bir şekilde işlenmesi gereken pek çok SQL sorgusu gönderilmesi iş açısından kritik katmanı için iyi adaylardır.
-   **Çok sayıda güncelleştirme** – INSERT, Update ve DELETE işlemleri, işlem ile veri dosyalarına kaydedilmesi gereken bellekteki veri sayfalarını (kirli sayfa) değiştirir `CHECKPOINT` . Olası veritabanı altyapısı işlem kilitlenmesi veya çok sayıda kirli sayfayla veritabanının yük devretmesi Genel Amaçlı katmanındaki kurtarma süresini artırabilir. Birçok bellek içi değişikliğe neden olan bir iş yükünüz varsa İş Açısından Kritik katmanını kullanın. 
-   **Verileri değiştiren uzun süre çalışan işlemler**. Daha uzun bir süre açılan işlemler günlük dosyasının kesilmesine engel olur, bu da günlük boyutunu ve [sanal günlük dosyası sayısını (VLF)](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide#physical_arch)artırabilir. Yüksek sayıda VLFs, yük devretmeden sonra veritabanının kurtarılmasını yavaşlatabilir.
-   **Raporlama ve analitik sorguları olan Iş yükü** , ücretsiz, ikincil salt okuma çoğaltmasına yeniden yönlendirilebilir.
- **Daha yüksek dayanıklılık ve hatalardan daha hızlı kurtarma**. Sistem arızası durumunda, birincil örnekteki veritabanı devre dışı bırakılır ve ikincil çoğaltmalardan biri, sorguları işlemeye yönelik yeni okuma-yazma birincil veritabanına anında gönderilir. Veritabanı altyapısının, günlük dosyasındaki işlemleri çözümlemek ve yinelemek ve tüm verileri bellek arabelleğine yüklemesi gerekmez.
- **Gelişmiş veri bozulması koruması**. İş Açısından Kritik katmanı, iş sürekliliği açısından arka planda veritabanı Çoğaltmalarından yararlanır ve bu nedenle hizmet, SQL Server veritabanı [yansıtma ve kullanılabilirlik grupları](/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring)için kullanılan teknolojiden de otomatik sayfa onarmasını kullanır. Bir veri bütünlüğü sorunu nedeniyle bir çoğaltmanın bir sayfayı okuyamadığında, sayfanın yeni bir kopyası başka bir yinelemeden alınır ve bu da veri kaybı veya müşteri kapalı kalma süresi olmadan okunamaz sayfa değişir. Bu işlev, veritabanının coğrafi ikincil çoğaltma özelliği varsa Genel Amaçlı katmanında geçerlidir.
- Multi-AZ Configuration içindeki **daha yüksek kullanılabilirlik** -iş açısından kritik katmanı, genel amaçlı katmanının% 99,99 ' i ile karşılaştırıldığında% 99,995 kullanılabilirliği garanti eder.
- Coğrafi çoğaltma ile yapılandırılan **hızlı coğrafi kurtarma** -iş açısından kritik katmanı, dağıtılan saatlerin %100 ' i için 5 sn ve kurtarma süresi hedefi (RTO) ile 30 sn arasında bir garantili kurtarma noktası hedefı (RPO) sağlar.

## <a name="next-steps"></a>Sonraki adımlar

- [SQL yönetilen örneği](../managed-instance/resource-limits.md#service-tier-characteristics)'nde iş açısından kritik katmanının kaynak özelliklerini (çekirdek sayısı, g/ç, bellek), [Vcore modeli](resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen4) veya [DTU modelinde](resource-limits-dtu-single-databases.md#premium-service-tier)tek veritabanı ya da [Vcore modeli](resource-limits-vcore-elastic-pools.md#business-critical---provisioned-compute---gen4) ve [DTU modelinde](resource-limits-dtu-elastic-pools.md#premium-elastic-pool-limits)esnek havuz bulun.
- [Genel amaçlı](service-tier-general-purpose.md) ve [hiper ölçek](service-tier-hyperscale.md) katmanları hakkında bilgi edinin.
- [Service Fabric](../../service-fabric/service-fabric-overview.md)hakkında bilgi edinin.
- Yüksek kullanılabilirlik ve olağanüstü durum kurtarma için daha fazla seçenek için bkz. [Iş sürekliliği](business-continuity-high-availability-disaster-recover-hadr-overview.md).