---
title: "Azure SQL yönetilen örneği 'ne SQL Server: performans temeli"
description: SQL Server veritabanlarınızı Azure SQL yönetilen örneği 'ne geçirirken bir performans temeli oluşturmayı ve karşılaştırmayı öğrenin.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: mokabiru
ms.date: 11/06/2020
ms.openlocfilehash: a47684bf29f1f34b8c9c59c04b7d33d234505cc2
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389716"
---
# <a name="migration-performance-sql-server-to--azure-sql-managed-instance-performance-baseline"></a>Geçiş performansı: SQL Server Azure SQL yönetilen örnek performans temeline
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

Bir SQL yönetilen örneğindeki iş yükünüzün performansını SQL Server üzerinde çalışan orijinal iş yükünüze göre karşılaştırmak için bir performans temeli oluşturun. 

## <a name="create-a-baseline"></a>Taban çizgisi oluşturma

İdeal olarak, geçişten sonra performans benzerdir veya daha iyidir, bu nedenle temel performans değerlerinin kaynak üzerinde ölçülmesi ve kaydetmeniz ve ardından hedef ortamla karşılaştırılabilmesi önemlidir. Performans temeli, kaynak üzerinde ortalama iş yükünüzü tanımlayan bir parametre kümesidir. 

İçin önemli bir sorgu kümesi ve iş yükünüzün temsilcisi seçin. Kaynak sunucuda, ortalama/en fazla CPU kullanımı, ortalama/en fazla disk GÇ gecikmesi, üretilen iş, ıOPS, Ortalama/maksimum sayfa ömrü erkeklerin ve en fazla tempdb boyutu gibi performans ölçümlerinin yanı sıra bu sorgular için min/Average/Max süresini ve CPU kullanımını ölçün ve belgeleyin. 

Aşağıdaki kaynaklar bir performans taban çizgisi tanımlamaya yardımcı olabilir: 

   - [CPU kullanımını izle ](https://techcommunity.microsoft.com/t5/azure-sql-database/monitor-cpu-usage-on-sql-server-and-azure-sql/ba-p/680777#M131)
   - [Bellek kullanımını izleme](/sql/relational-databases/performance-monitor/monitor-memory-usage)   ve arabellek havuzu, plan önbelleği, sütun deposu havuzu, [bellek ıçı OLTP](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage)vb. gibi farklı bileşenler tarafından kullanılan bellek miktarını tespit edin. Ayrıca, sayfa ömrü erkeklerin bellek performans sayacının ortalama ve en yüksek değerlerini de bulmanız gerekir. 
   - Kaynak SQL Server örneğinde, [sys.dm_io_virtual_file_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)   görünümü veya [performans sayaçlarını](/sql/relational-databases/performance-monitor/monitor-disk-usage)kullanarak disk GÇ kullanımını izleyin. 
   - Dinamik yönetim görünümlerini inceleyerek iş yükünü ve sorgu performansını izleyin (veya SQL Server 2016 ve üzeri sürümlerde geçiş yapıyorsanız sorgu deposu). İş yükünüzün en önemli sorgularının ortalama süresini ve CPU kullanımını belirler. 

Kaynak SQL Server yönelik tüm performans sorunları, geçişten önce ele alınmalıdır. Bilinen sorunların herhangi bir yeni sisteme geçirilmesi beklenmeyen sonuçlara neden olabilir ve herhangi bir performans karşılaştırmayı geçersiz kılabilir. 


## <a name="compare-performance"></a>Performansı karşılaştırın 

Bir taban çizgisi tanımladıktan sonra, hedef SQL yönetilen örneği üzerinde benzer iş yükü performansını karşılaştırın. Doğruluk açısından, SQL yönetilen örnek ortamının SQL Server ortamla mümkün olduğunca benzer olması önemlidir. 

Eşleşen performansı tam olarak olası olmayan SQL yönetilen örnek altyapısı farklılıkları vardır. Bazı sorgular beklenenden daha hızlı çalışabilir, diğerleri daha yavaş olabilir. Bu karşılaştırmanın amacı, yönetilen örnekteki iş yükü performansının SQL Server (Ortalama) performans ile eşleştiğini doğrulamak ve performans ile asıl performansınınkiyle eşleşmeyen tüm kritik sorguları belirlemek için kullanılır. 

Performans karşılaştırması aşağıdaki sonuçlar oluşmasına neden olabilir: 

- Yönetilen örnekteki iş yükü performansı, kaynak SQL Server iş yükü performansından daha iyi hizalı veya daha iyidir. Bu durumda, geçişin başarılı olduğunu başarıyla onaylamışınızı tamamladınız. 

- Performans parametrelerinin ve iş yükündeki sorguların çoğunluğu, bazı özel durumlarla beklenen performansa neden olur. Bu durumda, farkları ve bunların önemini tespit edin. Performans düşüklüğü olan bazı önemli sorgular varsa, temel alınan SQL planlarının değişip değişmediğini veya sorguların kaynak limitlerini vurmasını araştırın. Bu sorunu, kritik sorgulara (örneğin, uyumluluk düzeyini değiştirme, eski kardinalite Estimator) doğrudan veya plan kılavuzlarını kullanarak bazı ipuçları uygulayarak azaltabilirsiniz. İstatistiklerin ve dizinlerin her iki ortamda da güncel ve eşit olduğundan emin olun. 

- Kaynak SQL Server Örneğiniz ile karşılaştırıldığında, çoğu sorgu yönetilen bir örnekte daha yavaştır. Bu durumda, örneğin GÇ, bellek veya örnek günlüğü hız sınırları gibi [bazı kaynak sınırlarına ulaşmak](../../managed-instance/resource-limits.md#service-tier-characteristics) gibi farkın kök nedenlerini belirlemeyi deneyin. Farka neden olan bir kaynak sınırı yoksa, veritabanının uyumluluk düzeyini değiştirmeyi deneyin veya eski kardinalite tahmini gibi veritabanı ayarlarını değiştirin ve testi yeniden çalıştırın. Gerileme performansına sahip sorguları belirlemek için yönetilen örnek veya sorgu deposu görünümleri tarafından sunulan önerileri gözden geçirin. 

SQL yönetilen örneği, varsayılan olarak etkinleştirilen yerleşik bir otomatik plan düzeltme özelliğine sahiptir. Bu özellik, geçmişte daha fazla çalışan sorguların gelecekte azalmamasını sağlar. Bu özellik etkinleştirilmemişse, SQL yönetilen örneği 'nin performans temelini öğrenebilmesi için, eski ayarlarla iş yükünü çalıştırın. Ardından, özelliği etkinleştirin ve iş yükünü yeni ayarlarla yeniden çalıştırın. 

İhtiyaçlarınıza uygun iş yükü performansının en iyi yapılandırmasına ulaşmak için, testinizin parametrelerinde değişiklik yapın veya daha yüksek hizmet katmanlarına yükseltin. 

## <a name="monitor-performance"></a>Performansı izleme 

SQL yönetilen örneği, izleme ve sorun giderme için gelişmiş araçlar sağlar ve bunları örneğinizin performansını izlemek için kullanmanız gerekir. İzlenecek bazı anahtar ölçümleri şunlardır: 

- Sağladığınız sanal çekirdek sayısının iş yükünüz için doğru eşleşme olup olmadığını belirleme örneği üzerinde CPU kullanımı. 
- Daha [fazla bellek](https://techcommunity.microsoft.com/t5/azure-sql-database/do-you-need-more-memory-on-azure-sql-managed-instance/ba-p/563444)gerekip gerekmediğini öğrenmek için yönetilen Örneğinizde sayfa ömrü erkeklerin.
-  Daha iyi GÇ performansı almak için dosyaları önceden ayırmanız gerekebilecek, özellikle Genel Amaçlı katmanında depolama GÇ sorunlarını tanımlayan INSTANCE_LOG_GOVERNOR veya PAGEIOLATCH gibi istatistikler. 


## <a name="considerations"></a>Dikkat edilmesi gerekenler  

Performansı karşılaştırırken şunları göz önünde bulundurun: 

- Kaynak ve hedef arasında ayarlar eşleşiyor. Çeşitli örnek, veritabanı ve tempdb ayarlarının iki ortam arasında eşit olduğunu doğrulayın. Yapılandırma, uyumluluk düzeyleri, şifreleme ayarları, izleme bayrakları gibi farklılıklar, performansı eğebilir. 

- Depolama [en iyi yöntemlere](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525)göre yapılandırılır. Örneğin, Genel Amaçlı için, performansı artırmak üzere dosyaların boyutunu önceden ayırmanız gerekebilir. 

- Yönetilen bir örnek ve SQL Server arasındaki performans farklılıklarına neden olabilecek [önemli ortam farklılıkları](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) vardır. Ortamla ilgili bir performans sorununa katkıda bulunan riskleri belirler. 

- İş yükü performansını ölçmenize ve olası performans sorunlarını otomatik olarak azaltmanıza yardımcı olduklarından, SQL yönetilen Örneğinizde sorgu deposu ve otomatik ayarlama etkinleştirilmelidir. 



## <a name="next-steps"></a>Sonraki adımlar

Yeni Azure SQL yönetilen örnek ortamınızı iyileştirme hakkında daha fazla bilgi için aşağıdaki kaynaklara bakın: 

- [Azure SQL yönetilen örneği üzerinde iş yükü performansının neden SQL Server farklı olduğunu belirleme](https://medium.com/azure-sqldb-managed-instance/what-to-do-when-azure-sql-managed-instance-is-slower-than-sql-server-dd39942aaadd)
- [SQL yönetilen örneği ve SQL Server arasındaki performans farklılıklarının önemli nedenleri](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)
- [Azure SQL yönetilen örneği (Genel Amaçlı) için depolama performansı en iyi uygulamaları ve konuları](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525)
- [Azure SQL yönetilen örneği için gerçek zamanlı performans izleme (Bu arşivlendi, hedeflenen hedef mi?)](/archive/blogs/sqlcat/real-time-performance-monitoring-for-azure-sql-database-managed-instance)