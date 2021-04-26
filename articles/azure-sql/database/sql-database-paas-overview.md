---
title: Azure SQL Veritabanı hizmeti nedir?
description: "SQL veritabanı 'na giriş: bulutta Microsoft ilişkisel veritabanı yönetim sistemi 'nin (RDBMS) teknik ayrıntıları ve özellikleri."
keywords: sql'e giriş,sql veritabanı nedir
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=3
ms.devlang: ''
ms.topic: overview
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/21/2020
ms.openlocfilehash: 65710ae40a971d214068f0e2686f78fb994a967e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104601440"
---
# <a name="what-is-azure-sql-database"></a>Azure SQL Veritabanı nedir?
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Veritabanı yükseltme, düzeltme eki uygulama, yedekleme ve izleme gibi veritabanı işlevlerinin çoğunu kullanıcı müdahalesi olmadan gerçekleştiren tam olarak yönetilen hizmet olarak platform (PaaS) veritabanı altyapısıdır. Azure SQL Veritabanı her zaman en güncel kararlı SQL Server veritabanı altyapısı ve düzeltme eki uygulanmış işletim sistemi sürümü üzerinde çalışır ve %99,99 kullanılabilirlik sunar. Azure SQL Veritabanı'nda bulunan yerleşik PaaS özellikleri, işletmeniz için kritik öneme sahip olan etki alanına özgü veritabanı yönetim ve iyileştirme etkinliklerine odaklanmanızı sağlar.

Azure SQL Veritabanı ile Azure'da çalışan uygulamalar ve çözümler için yüksek oranda kullanılabilir ve yüksek performanslı bir veri depolama katmanı oluşturabilirsiniz. SQL veritabanı, hem ilişkisel verileri hem de [ilişkisel olmayan yapıları](../multi-model-features.md)(GRAFIKLER, JSON, uzamsal ve XML gibi) işleyebilmenizi sağladığından, çeşitli modern bulut uygulamaları için doğru seçim yapılabilir.

Azure SQL veritabanı, [Microsoft SQL Server veritabanı altyapısının](/sql/sql-server/sql-server-technical-documentation?toc=%2fazure%2fsql-database%2ftoc.json)en son kararlı sürümünü temel alır. [Yüksek performanslı bellek içi teknolojiler](../in-memory-oltp-overview.md) ve [akıllı sorgu işleme](/sql/relational-databases/performance/intelligent-query-processing?toc=%2fazure%2fsql-database%2ftoc.json)gibi gelişmiş sorgu işleme özelliklerini kullanabilirsiniz. Hatta en yeni SQL Server özellikleri önce SQL Veritabanı'na, daha sonra SQL Server'a uygulanır. Düzeltme eki uygulama veya yükseltme konusunda endişelenmeden milyonlarca veritabanıyla test edilmiş olan en güncel SQL Server özelliklerine sahip olursunuz. 

SQL veritabanı iki farklı satın alma modelinde performansı kolayca tanımlamanızı ve ölçeklendirmenizi sağlar: [sanal çekirdek tabanlı satın alma modeli](service-tiers-vcore.md) ve [DTU tabanlı satın alma modeli](service-tiers-dtu.md). SQL Veritabanı yerleşik yüksek kullanılabilirlik, yedekleme ve diğer yaygın bakım işlemlerine sahip olan tam olarak yönetilen bir hizmettir. Microsoft, SQL ve işletim sistemi kodunun tüm düzeltme ekini ve güncelleştirilmesini işler. Temel alınan altyapıyı yönetmeniz gerekmez.

Azure SQL veritabanı 'na yeni başladıysanız, Azure SQL *veritabanı 'Na genel bakış* videosunu, derınlemesıne [Azure SQL video serimize](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner)göre inceleyin:
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/Azure-SQL-Database-Overview-7-of-61/player]

> [!TIP]
> Azure SQL nasıl daha iyi hale getirebiliriz? [Ankete katılın](https://microsoft.qualtrics.com/jfe/form/SV_ePOznHhP4gDKfGu?channel=456).

## <a name="deployment-models"></a>Dağıtım modelleri

Azure SQL veritabanı, bir veritabanı için aşağıdaki dağıtım seçeneklerini sağlar:

- [Tek veritabanı](single-database-overview.md) , tam olarak yönetilen, yalıtılmış bir veritabanını temsil eder. Tek bir güvenilir veri kaynağı gerektiren modern bulut Uygulamalarınız ve mikro hizmetlerinize sahipseniz bu seçeneği kullanabilirsiniz. Tek bir veritabanı, [SQL Server veritabanı altyapısındaki](/sql/sql-server/sql-server-technical-documentation?toc=%2fazure%2fsql-database%2ftoc.json) [Kapsanan veritabanına](/sql/relational-databases/databases/contained-databases?toc=%2fazure%2fsql-database%2ftoc.json) benzerdir.
- [Elastik havuz](elastic-pool-overview.md) , CPU veya bellek gibi paylaşılan bir kaynak kümesiyle [tek bir veritabanı](single-database-overview.md) koleksiyonudur. Tek veritabanları, elastik bir havuzun içine ve dışına taşınabilir.

> [!IMPORTANT]
> SQL veritabanı ile SQL Server arasındaki özellik farklarını ve farklı Azure SQL veritabanı seçenekleri arasındaki farkları anlamak için bkz. [SQL veritabanı özellikleri](features-comparison.md).

SQL veritabanı, birden çok kaynak türü, hizmet katmanı ve işlem boyutları ile öngörülebilir performans sunar. Kapalı kalma süresi olmadan dinamik ölçeklenebilirlik, yerleşik akıllı iyileştirme, küresel ölçeklenebilirlik ve kullanılabilirlik ve gelişmiş güvenlik seçenekleri sağlar. Bu yetenekler, sanal makineleri ve altyapıyı yönetmek yerine hızlı uygulama geliştirmeye odaklanmanıza ve pazara ulaşma süresini hızlandırmanıza olanak tanır. SQL veritabanı şu anda dünyanın dört bir yanındaki 38 veri merkezinde bulunur, bu nedenle veritabanınızı yakınınızdaki bir veri merkezinde çalıştırabilirsiniz.

## <a name="scalable-performance-and-pools"></a>Ölçeklenebilir performans ve havuzlar

Atanan kaynak miktarını belirleyebilirsiniz. 
- Tek veritabanları ile, her veritabanı diğerlerinden yalıtılmıştır ve taşınabilir. Her birinin garantili işlem, bellek ve depolama kaynağı miktarına sahip olması gerekir. Veritabanına atanan kaynakların miktarı söz konusu veritabanına ayrılmıştır ve Azure 'daki diğer veritabanlarıyla paylaşılmaz. [Tek veritabanı kaynaklarını](single-database-scale.md) dinamik olarak yukarı ve aşağı ölçekleyebilirsiniz. Tek veritabanı seçeneği, farklı gereksinimlere göre farklı işlem, bellek ve depolama kaynakları sağlar. Örneğin, 1 ila 80 sanal çekirdek veya 32 GB ila 4 TB alabilir. Tek veritabanları için [hiperscale hizmet katmanı](service-tier-hyperscale.md) , hızlı yedekleme ve geri yükleme özellikleri Ile 100 TB 'a ölçeklenmenize olanak sağlar.
- Elastik havuzlarla, havuzdaki tüm veritabanları tarafından paylaşılan kaynaklar atayabilirsiniz. Kaynak kullanımını en üst düzeye çıkarmak ve tasarruf etmek için yeni bir veritabanı oluşturabilir veya var olan tek veritabanlarını bir kaynak havuzuna taşıyabilirsiniz. Bu seçenek ayrıca [Esnek havuz kaynaklarını](elastic-pool-scale.md) yukarı ve aşağı dinamik olarak ölçeklendirmenize olanak tanır.

İlk uygulamanızı küçük, tek bir veritabanında, genel amaçlı hizmet katmanında aylık düşük maliyetli bir şekilde oluşturabilirsiniz. Daha sonra, çözümünüzün ihtiyaçlarını karşılamak üzere iş açısından kritik hizmet katmanına hizmet katmanını el ile veya programlama yoluyla dilediğiniz zaman değiştirebilirsiniz. Performansı uygulamanız veya müşterileriniz kesinti yaşamadan ayarlayabilirsiniz. Dinamik ölçeklendirebilirlik, veritabanınızın hızla değişen kaynak gereksinimlerine saydam bir şekilde yanıt verebilmesini sağlar. Yalnızca ihtiyacınız olan kaynaklar için ödeme yaparsınız.

*Dinamik ölçeklenebilirlik* , *Otomatik ölçeklendirmeyi* farklıdır. Bir hizmet ölçütlere dayalı olarak otomatik şekilde ölçeklendirildiğinde otomatik ölçeklendirme oluşurken, dinamik ölçeklenebilirlik ise kesinti süresi olmadan el ile ölçeklendirmeye olanak sağlar. Tek veritabanı seçeneği el ile dinamik ölçeklenebilirliği destekler, ancak otomatik ölçeklendirme gerektirmez. Daha otomatik bir deneyim için, veritabanlarının tek tek veritabanı gereksinimlerine göre bir havuzdaki kaynakları paylaşmasına olanak sağlayan elastik havuzları kullanın. Diğer bir seçenek de tek bir veritabanı için ölçeklenebilirliği otomatik hale getirmeye yardımcı olabilecek betikleri kullanmaktır. Bir örnek için bkz. [tek bir veritabanını izlemek ve ölçeklendirmek Için PowerShell 'ı kullanma](scripts/monitor-and-scale-database-powershell.md).

### <a name="purchasing-models"></a>Satın alma modelleri

SQL veritabanı aşağıdaki satın alma modellerini sunmaktadır:
- [Sanal çekirdek tabanlı satın alma modeli](service-tiers-vcore.md) , sanal çekirdek sayısını, bellek miktarını ve depolama alanı miktarını ve hızını seçmenize olanak sağlar. Sanal çekirdek tabanlı satın alma modeli, maliyet tasarrufu kazanmak için [SQL Server Azure hibrit avantajı](https://azure.microsoft.com/pricing/hybrid-benefit/) kullanmanıza da olanak tanır. Azure Hibrit Avantajı hakkında daha fazla bilgi için, bu makalenin devamındaki "sık sorulan sorular" bölümüne bakın.
- [DTU tabanlı satın alma modeli](service-tiers-dtu.md) , hafif veritabanı iş yüklerini desteklemek için üç hizmet katmanında işlem, bellek ve g/ç kaynaklarının bir karışımını sunar. Her katmandaki işlem boyutları bu kaynakların farklı bir karışımını sağlar ve bu da ek depolama kaynakları ekleyebilirsiniz.
- [Sunucusuz model](serverless-tier-overview.md) , iş yükü talebine göre işlem sayısını otomatik olarak ölçeklendirir ve saniye başına kullanılan işlem miktarına göre faturalandırılır. Sunucusuz işlem katmanı Ayrıca, yalnızca depolama faturalandırılırken, etkin olmayan dönemler sırasında veritabanlarını otomatik olarak duraklatır ve etkinlik döndüğünde veritabanlarını otomatik olarak sürdürür.

### <a name="service-tiers"></a>Hizmet katmanları

Azure SQL veritabanı, farklı türlerde uygulamalar için tasarlanan üç hizmet katmanı sunar:
- Ortak iş yükleri için tasarlanan [genel amaçlı/standart](service-tier-general-purpose.md) hizmet katmanı. Bütçeye dayalı dengeli işlem ve depolama seçenekleri sunar.
- Yüksek işlem oranı ve en düşük gecikmeli g/ç özellikli OLTP uygulamaları için tasarlanan [iş açısından kritik/Premium](service-tier-business-critical.md) hizmet katmanı. Birkaç yalıtılmış çoğaltma kullanarak hatalara en yüksek esnekliği sağlar.
- Çok büyük OLTP veritabanı için tasarlanan [hiper ölçekli](service-tier-hyperscale.md) hizmet katmanı ve depolamayı otomatik olarak ölçeklendirme ve işlem akıcı bir şekilde ölçeklendirme özelliği.    

### <a name="elastic-pools-to-maximize-resource-utilization"></a>Kaynak kullanımını en verimli hale getirmek için elastik havuzlar

Tek veritabanı oluşturabilmek ve veritabanı performansını isteğe göre yükseltip düşürebilmek, özellikle kullanım biçimlerinin nispeten tahmin edilebilir olduğu durumlarda birçok işletme ve uygulama için yeterlidir. Öngörülemeyen kullanım desenleri, maliyetleri ve iş modelinizi yönetmeyi zorlaştırır. [Elastik havuzlar](elastic-pool-overview.md) bu sorunu çözmek için tasarlanmıştır. Performans kaynaklarını ayrı bir veritabanı yerine bir havuza ayırabilirsiniz. Tek veritabanı performansı yerine havuzun toplu performans kaynakları için ödeme yaparsınız.

   ![Temel, standart ve Premium sürümlerde elastik havuzlar gösteren grafik](./media/sql-database-paas-overview/sqldb_elastic_pools.png)

Elastik havuzlarla, kaynakların ölçeği dalgalanarak bir şekilde arama yapmak için veritabanı performansını artırma ve azaltma konusuna odaklanmanız gerekmez. Havuza alınan veritabanları, gerektiğinde elastik havuzun performans kaynaklarını tüketir. Havuza alınmış veritabanları, havuzun sınırlarını aşmaz, ancak tek veritabanı kullanımı olmasa bile maliyetleriniz tahmin edilebilir olmaya devam eder.

[Havuza veritabanları ekleyip kaldırabilir](elastic-pool-overview.md), uygulamanızı, sizin denetlediğiniz bir bütçeyle binlerce veritabanına ölçeklendirebilirsiniz. Havuzdaki veritabanları için kullanılabilen minimum ve maksimum kaynakları denetleyebilir, havuzdaki hiçbir veritabanının havuz kaynaklarını kullandığından ve havuza alınan her veritabanının garantili minimum kaynak miktarına sahip olduğundan emin olmanız gerekir. Elastik havuzlar kullanan hizmet olarak yazılım (SaaS) uygulamaları için tasarım desenleri hakkında daha fazla bilgi edinmek için bkz. [SQL veritabanı ile çok kiracılı SaaS uygulamaları Için tasarım desenleri](saas-tenancy-app-design-patterns.md).

Betikler, elastik havuzların izlenmesine ve ölçeklendirilmesine yardımcı olabilir. Bir örnek için bkz. [Azure SQL veritabanında esnek havuzu izlemek ve ölçeklendirmek Için PowerShell 'ı kullanma](scripts/monitor-and-scale-pool-powershell.md).


### <a name="blend-single-databases-with-pooled-databases"></a>Tek veritabanlarını havuza alınan veritabanlarıyla karıştırma

Tek veritabanlarını elastik havuzlarla karıştırabilirsiniz ve tek veritabanlarının ve elastik havuzların hizmet katmanlarını, durumunuza uyum sağlamak için değiştirebilirsiniz. Ayrıca, benzersiz uygulama tasarımı ihtiyaçlarınızı karşılamak, maliyet ve kaynak verimliliği sağlamak ve yeni iş fırsatlarının kilidini açmak için diğer Azure hizmetlerini SQL veritabanı ile karıştırabilir ve eşleştirebilirsiniz.

## <a name="extensive-monitoring-and-alerting-capabilities"></a>Kapsamlı izleme ve uyarı özellikleri

Azure SQL veritabanı, iş yükü özelliklerine daha derin Öngörüler elde etmenize yardımcı olan gelişmiş izleme ve sorun giderme özellikleri sağlar. Bu özellikler ve araçlar şunları içerir:
 - SQL Server veritabanı altyapısının en son sürümü tarafından sunulan yerleşik izleme özellikleri. Gerçek zamanlı performans öngörülerini bulmanıza olanak tanır. 
 - Azure tarafından sunulan PaaS izleme özellikleri, çok sayıda veritabanı örneğini izlemenize ve gidermenize olanak sağlar.

[Sorgu deposu](/sql/relational-databases/performance/best-practice-with-the-query-store), yerleşik bir SQL Server izleme özelliği, Sorgularınızın performansını gerçek zamanlı olarak kaydeder ve olası performans sorunlarını ve en üst kaynak tüketicilerini tanımlamanızı sağlar. Otomatik ayarlama ve öneriler, gerileme performansına ve eksik veya yinelenen dizinlere sahip sorgularla ilgili öneriler sağlar. SQL veritabanı 'ndaki otomatik ayarlama, sorunları giderebilen betikleri el ile uygulamanızı veya SQL veritabanının bu sorunu çözmesine izin verir. SQL veritabanı Ayrıca, düzeltmesinin bazı avantajlardan yararlandığına da test edebilir ve sonuca bağlı olarak değişikliği korur veya döndürmenizi sağlar. Sorgu deposu ve otomatik ayarlama özelliklerine ek olarak, iş yükü performansını izlemek için standart [DMVs ve XEvent](monitoring-with-dmvs.md) ' yi de kullanabilirsiniz.

Azure, binlerce veritabanının durumunu izlemenize olanak sağlayan, performans derecelendirmeleri ile birleştirilmiş [yerleşik performans izleme](performance-guidance.md) ve [Uyarı](alerts-insights-configure-portal.md) araçları sağlar. Bu araçları kullanarak, geçerli veya tahmini performans ihtiyaçlarınıza göre ölçeği artırma veya azaltma etkilerini hızla değerlendirebilirsiniz. Ayrıca, SQL veritabanı daha kolay izleme için [ölçümleri ve kaynak günlüklerini yayabilir](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) . SQL Veritabanını kaynak kullanımını, çalışanları, oturumları ve bu Azure kaynaklarından birine yapılan bağlantıları kaydedecek şekilde yapılandırabilirsiniz:

- **Azure Depolama**: Küçük maliyetlerle çok sayıda telemetri arşivleme için.
- **Azure Event Hubs**: SQL veritabanı telemetrisini özel izleme çözümünüz veya etkin işlem hatlarınız ile tümleştirmek için.
- **Azure izleyici günlükleri**: raporlama, uyarı ve azaltıcı yetenekler içeren yerleşik bir izleme çözümü için.

![Azure izleme mimarisi diyagramı](./media/sql-database-paas-overview/architecture.png)

## <a name="availability-capabilities"></a>Kullanılabilirlik özellikleri

Azure SQL veritabanı, işinizin kesintiler sırasında çalışmaya devam etmesini sağlar. Geleneksel bir SQL Server ortamında, genellikle en az iki makineniz yerel olarak ayarlanır. Bu makineler, tek bir makine veya bileşen hatasına karşı korumak için verilerin tam, zaman uyumlu olarak korunmasını sağlar. Bu ortam yüksek kullanılabilirlik sağlar, ancak veri merkezinizi yok etmek doğal bir olağanüstü duruma karşı koruma sağlamaz.

Olağanüstü durum kurtarma, çok zararlı bir olayın, verilerinizin bir kopyasına sahip başka bir makineye veya makine kümesine sahip olacak kadar coğrafi olarak yerelleştirilmiş olduğunu varsayar. SQL Server, bu özelliği almak için zaman uyumsuz modda çalışan her zaman açık kullanılabilirlik gruplarını kullanabilirsiniz. Kullanıcılar genellikle çoğaltma işlemini bir işlem gerçekleştirmeden önce uzakta olmasını beklemek istemeiyorsunuzdur, bu nedenle planlanmamış yük devretme işlemleri gerçekleştirdiğinizde veri kaybı meydana gelir.

Premium ve İş Açısından Kritik hizmet katmanlarındaki veritabanları, bir kullanılabilirlik grubunun eşitlemesine [benzer bir şey zaten yapılır](high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) . Daha düşük hizmet katmanlarındaki veritabanları, [farklı ancak eşdeğer bir mekanizma](high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability)kullanarak depolama aracılığıyla artıklık sağlar. Yerleşik mantık, tek bir makine hatasına karşı korunmaya yardımcı olur. Etkin coğrafi çoğaltma özelliği, tüm bir bölgenin yok edileceği olağanüstü duruma karşı koruma olanağı sağlar.

Azure Kullanılabilirlik Alanları, tek bir bölge içinde tek bir veri merkezi oluşturma kesintisinden korunmanıza çalışır. Bir binadan güç veya ağ kaybına karşı korumaya yardımcı olur. SQL veritabanı 'nda farklı çoğaltmaları farklı kullanılabilirlik bölgelerine (farklı binalar, etkin) yerleştirebilirsiniz.

Aslında, Microsoft tarafından yönetilen küresel bir veri merkezi ağı tarafından desteklenen Azure 'un hizmet düzeyi sözleşmesi [(SLA)](https://azure.microsoft.com/support/legal/sla/) , uygulamanızın 24/7 çalışmasına yardımcı olur. Azure platformu her veritabanını tamamen yönetir ve veri kaybı ve yüksek oranda veri kullanılabilirliği garantisi vermez. Azure, düzeltme eki uygulama, yedeklemeler, çoğaltma, hata algılama, temel alınan olası donanım, yazılım veya ağ hataları, hata düzeltmelerini dağıtma, yük devretme, veritabanı yükseltmeleri ve diğer bakım görevlerini otomatik olarak işler. Standart kullanılabilirlik düzeyine ulaşmak için işlem ve depolama katmanları ayrılır. Premium kullanılabilirlik, performans için işlem ve depolamayı tek bir düğümde tümleştirerek ve sonra her zaman açık kullanılabilirlik gruplarıyla benzer bir teknoloji uygulayarak elde edilir. Azure SQL veritabanı 'nın yüksek kullanılabilirlik özelliklerine ilişkin tam bir tartışma için bkz. [SQL veritabanı kullanılabilirliği](high-availability-sla.md). 

Ayrıca, SQL veritabanı yerleşik [iş sürekliliği ve küresel ölçeklenebilirlik](business-continuity-high-availability-disaster-recover-hadr-overview.md) özellikleri sağlar. Bu modüller şunlardır:

- [Otomatik yedeklemeler](automated-backups-overview.md):

  SQL veritabanı, zaman içinde herhangi bir noktaya geri yükleme olanağı sağlamak için veritabanlarının tam, değişiklik ve işlem günlüğü yedeklerini otomatik olarak gerçekleştirir. Tek veritabanları ve havuza alınmış veritabanları için SQL veritabanını, uzun süreli yedek saklama için Azure depolama 'ya tam veritabanı yedeklerini depolayacak şekilde yapılandırabilirsiniz. Yönetilen örnekler için, uzun süreli yedek saklama için yalnızca kopya yedeklemeler de gerçekleştirebilirsiniz.

- [Zaman içinde bir noktaya geri yükleme](recovery-using-backups.md):

  Tüm SQL veritabanı dağıtım seçenekleri herhangi bir veritabanı için otomatik yedekleme saklama süresi içinde herhangi bir zamanda kurtarmayı destekler.
- [Etkin coğrafi çoğaltma](active-geo-replication-overview.md):

  Tek veritabanı ve havuza alınmış veritabanları seçenekleri, aynı veya küresel olarak dağıtılmış Azure veri merkezlerinde dört adede kadar okunabilir ikincil veritabanını yapılandırmanıza olanak tanır. Örneğin, bir katalog veritabanına sahip ve yüksek miktarda eşzamanlı salt okuma işlemlerine sahip bir SaaS uygulamanız varsa, genel okuma ölçeğini etkinleştirmek için etkin coğrafi çoğaltma ' yı kullanın. Bu, okuma iş yüklerinden kaynaklanan birincil üzerinde performans sorunlarını ortadan kaldırır. Yönetilen örnekler için otomatik yük devretme grupları kullanın.
- [Otomatik yük devretme grupları](auto-failover-group-overview.md):

  Tüm SQL veritabanı dağıtım seçenekleri, küresel ölçekte yüksek kullanılabilirlik ve yük dengelemeyi etkinleştirmek için yük devretme gruplarını kullanmanıza izin verir. Buna saydam coğrafi çoğaltma ve büyük veritabanı kümelerinin, elastik havuzların ve yönetilen örneklerin yük devretmesi dahildir. Yük devretme grupları, genel olarak dağıtılmış SaaS uygulamalarının oluşturulmasını, en az yönetim yüküyle etkinleştirir. Bu, tüm karmaşık izleme, Yönlendirme ve yük devretme düzenlemesini SQL veritabanı 'na bırakır.
- Bölgesel olarak [yedekli veritabanları](high-availability-sla.md):

  SQL veritabanı, birden çok kullanılabilirlik alanında Premium veya İş Açısından Kritik veritabanları ya da elastik havuzlar sağlamanıza olanak tanır. Bu veritabanları ve elastik havuzlar yüksek kullanılabilirlik için birden fazla yedekli çoğaltma içerdiğinden, bu çoğaltmaların birden fazla kullanılabilirlik bölgesine yerleştirilmesi daha yüksek esnekliği sağlar. Bu, veri kaybı olmadan veri merkezi ölçek hatalarından otomatik olarak kurtarma özelliği içerir.

## <a name="built-in-intelligence"></a>Yerleşik zeka

SQL veritabanı ile, veritabanlarını çalıştırmanın ve yönetmenin maliyetlerini önemli ölçüde düşürmenize ve uygulamanızın hem performansını hem de güvenliğini en üst düzeye çıkaran yerleşik zeka alırsınız. Saat içinde milyonlarca müşteri iş yükü çalıştırıldığında, SQL veritabanı büyük miktarda telemetri verilerini toplayıp işler ve ayrıca müşteri gizliliğini tamamen de tam olarak alır. Çeşitli algoritmalar, hizmetin uygulamanızı öğrenmesine ve uyarlanabilmesi için telemetri verilerini sürekli olarak değerlendirir.

### <a name="automatic-performance-monitoring-and-tuning"></a>Otomatik performans izleme ve ayarlama

SQL Veritabanı izlemeniz gereken sorgularla ilgili ayrıntılı öngörüler sunar. SQL veritabanı, veritabanı desenlerinizi öğrenir ve veritabanı şemanızı iş yükünüze uyarlamanızı sağlar. SQL Veritabanı, [performans ayarlama önerilerinde](database-advisor-implement-performance-recommendations.md) bulunur. Siz de bu ayarları gözden geçirebilir ve uygulayabilirsiniz.

Ancak, bir veritabanını sürekli olarak izlemek, özellikle birçok veritabanı ile ilgileniyorsanız zor ve sıkıcı bir görevdir. [Akıllı içgörüler](intelligent-insights-overview.md) , SQL veritabanı performansını ölçekte otomatik olarak izleyerek sizin için bu işi yapar. Performans düşüşü sorunları hakkında sizi bilgilendirir, her sorunun kök nedenini tanımlar ve mümkün olduğunda performans iyileştirme önerileri sağlar.

SQL veritabanı ve Azure 'un sağladığı tüm kullanılabilir araçlar ve raporlardan çok fazla sayıda veritabanını yönetmek imkansız olabilir. Veritabanınızı el ile izlemek ve ayarlamak yerine, [otomatik ayarlama](automatic-tuning-overview.md)kullanarak SQL veritabanı 'na bazı izleme ve ayarlama eylemlerinin yetkisini aktarmaya yarar sağlayabilirsiniz. SQL veritabanı, performansın sürekli olarak iyileştirildiğinden emin olmak için öneri, test ve ayarlama eylemlerinin her birini doğrular. Bu şekilde, SQL veritabanı otomatik olarak denetimli ve güvenli bir şekilde iş yükünüze uyum sağlar. Otomatik ayarlama, veritabanınızın performansının dikkatle izlenen ve her ayarlama eyleminden önce ve sonra karşılaştırıldığı anlamına gelir. Performans geliştirmezse ayarlama eylemi geri döndürülür.

SQL veritabanı üzerinde [SaaS çok kiracılı uygulamalar](saas-tenancy-app-design-patterns.md) çalıştıran iş ortaklarımızın birçoğu, uygulamalarının sürekli ve öngörülebilir performansa sahip olduğundan emin olmak için otomatik performans ayarlamaya bağlıdır. İş ortaklarımız bu özelliğin gecenin ortasında performans sorunu yaşama riskini önemli ölçüde azalttığını söylüyor. Ayrıca, müşteri tabanlarına ait bir parçası SQL Server kullandığından, SQL Server müşterilerine yardımcı olmak için SQL veritabanı tarafından sağlanan dizin oluşturma önerilerini de kullanıyor.

[SQL veritabanında](automatic-tuning-overview.md)iki otomatik ayarlama yönü mevcuttur:

- **Otomatik dizin yönetimi**: Veritabanınıza eklenmesi ve veritabanınızdan kaldırılması gereken dizinleri tanımlar.
- **Otomatik plan düzeltme**: sorunlu planları TANıMLAR ve SQL planı performans sorunlarını düzeltir.

### <a name="adaptive-query-processing"></a>Uyarlamalı sorgu işleme

Çok deyimli tablo değerli işlevler, toplu iş modu bellek verme geri bildirimi ve toplu iş modu Uyarlamalı birleşimler için araya eklemeli yürütme dahil olmak üzere [Uyarlamalı sorgu işleme](/sql/relational-databases/performance/intelligent-query-processing)kullanabilirsiniz. Bu Uyarlamalı sorgu işleme özelliklerinin her biri benzer "öğrenme ve uyarma" teknikleri uygulayarak, tarihsel olmayan bir sorgu iyileştirme sorunlarıyla ilgili performans sorunlarının daha da sağlanmasına yardımcı olur.

## <a name="advanced-security-and-compliance"></a>Gelişmiş koruma ve uyumluluk

SQL Veritabanı, uygulamanızın çeşitli güvenlik ve uyumluluk gereksinimlerine uygun olmasına yardımcı olmak için bir dizi [yerleşik güvenlik ve uyum özelliğine](../../active-directory/identity-protection/concept-identity-protection-security-overview.md) sahiptir.

> [!IMPORTANT]
> Microsoft, bir dizi uyumluluk standartlarına karşı Azure SQL veritabanı 'na (tüm dağıtım seçenekleri) sahiptir. Daha fazla bilgi için, SQL veritabanı uyumluluk sertifikalarının en güncel listesini bulabileceğiniz [Microsoft Azure Güven Merkezi](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)' ne bakın.

### <a name="advance-threat-protection"></a>Tehdit korumasını ilerlet

SQL için Azure Defender, gelişmiş SQL güvenlik özelliklerine yönelik Birleşik bir pakettir. Veritabanı güvenlik açıklarını yönetme işlevlerini ve veritabanınıza yönelik bir tehdidi gösterebilen anormal etkinlikleri algılamamızı içerir. Bu özellikleri etkinleştirmek ve yönetmek için tek bir konum sağlar.

- [Güvenlik açığı değerlendirmesi](sql-vulnerability-assessment.md):

  Bu hizmet, olası veritabanı güvenlik açıklarını düzeltmenizi, izleyebilir ve yardımcı olabilir. Güvenlik durumunuz hakkında görünürlük sağlamasının yanı sıra güvenlik sorunlarınızı çözmek ve veritabanı güçlendirmelerinizi geliştirmek için eyleme dönüştürülebilir adımlar sunar.
- [Tehdit algılama](threat-detection-configure.md):

  Bu özellik, veritabanınıza erişmek veya veritabanına yararlanmak için olağan dışı ve zararlı olabilecek girişimleri gösteren anormal etkinlikleri algılar. Veritabanınızı şüpheli etkinliklere karşı sürekli izler ve olası güvenlik açıkları, SQL ekleme saldırıları ve anormal veritabanı erişim modelleri hakkında anında güvenlik uyarıları sunar. Tehdit koruması uyarıları, şüpheli etkinliğin ayrıntılarının yanı sıra tehdidi araştırmak ve ortadan kaldırmak için önerilen eylemleri de içerir.

### <a name="auditing-for-compliance-and-security"></a>Uyumluluk ve güvenlik denetimi

[Denetim](../../azure-sql/database/auditing-overview.md) , veritabanı olaylarını izler ve bunları Azure Depolama hesabınızdaki bir denetim günlüğüne yazar. Denetim; mevzuatla uyumluluk, veritabanı etkinliğini anlama ve işletme sorunlarını veya şüpheli güvenlik ihlallerini işaret edebilecek farklılıklar ve anormal durumlar hakkında içgörü sahip olmanıza yardımcı olabilir.

### <a name="data-encryption"></a>Veri şifrelemesi

SQL Veritabanı, şifreleme özelliği sayesinde verilerinizin güvenliğinin sağlanmasına yardımcı olur. Hareket halindeki veriler için [Aktarım Katmanı Güvenliği](https://support.microsoft.com/kb/3135244)kullanır. Bekleyen veriler için [Saydam veri şifrelemesi](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)kullanır. Kullanılan veriler için [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)kullanır.

### <a name="data-discovery-and-classification"></a>Veri bulma ve sınıflandırma

[Veri bulma ve sınıflandırma](data-discovery-and-classification-overview.md) , veritabanlarınızdaki hassas verileri bulmak, sınıflandırmak, etiketlemek ve korumak IÇIN Azure SQL veritabanı 'nda yerleşik olarak bulunan yetenekler sağlar. Veritabanı sınıflandırma durumlarınızın görünürlüğünü sağlar ve veritabanı içindeki hassas verilere erişimi izler ve sınırlarının ötesine dönüştürür.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory tümleştirmesi ve çok faktörlü kimlik doğrulaması

SQL Veritabanı, [Azure Active Directory tümleştirmesi](authentication-aad-overview.md) ile veritabanı kullanıcısı ve diğer Microsoft hizmetleri kimliklerini bir merkezden yönetmenizi sağlar. Bu özellik, izin yönetimini kolaylaştırırken güvenliği artırır. Azure Active Directory, çoklu bir oturum açma işlemini desteklerken, veri ve uygulama güvenliğini artırmak için [Multi-Factor Authentication](authentication-mfa-ssms-overview.md) 'ı destekler.

## <a name="easy-to-use-tools"></a>Kullanımı kolay araçlar

SQL Veritabanı uygulama oluşturma ve uygulamaların bakımını yapma işlemlerinin daha kolay ve daha verimli şekilde yapılmasını sağlar. SQL Veritabanı size, en iyi yaptığınız işe; mükemmel uygulamalar oluşturmaya odaklanma seçeneği sunar. Zaten sahip olduğunuz araçları ve becerileri kullanarak SQL veritabanı 'nda yönetebilir ve geliştirebilirsiniz.

|Araç|Açıklama|
|:---|:---|
|[Azure portal](https://portal.azure.com/)|Tüm Azure hizmetlerini yönetmek için Web tabanlı bir uygulama.|
|[Azure Data Studio](/sql/azure-data-studio/)|Windows, macOS ve Linux üzerinde çalışan platformlar arası bir veritabanı aracı.|
|[SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)|SQL Server SQL veritabanı 'na kadar herhangi bir SQL altyapısını yönetmeye yönelik ücretsiz, indirilebilir bir istemci uygulaması.|
|[Visual Studio 'da SQL Server Veri Araçları](/sql/ssdt/download-sql-server-data-tools-ssdt)|SQL Server ilişkisel veritabanları, Azure SQL veritabanı 'ndaki veritabanları, Tümleştirme Hizmetleri paketleri, Analysis Services veri modelleri ve Raporlama Hizmetleri raporları geliştirmeye yönelik ücretsiz, indirilebilir bir istemci uygulaması.|
|[Visual Studio Code](https://code.visualstudio.com/docs)|Windows, macOS ve Linux için ücretsiz, indirilebilir, açık kaynaklı bir kod Düzenleyicisi. Microsoft SQL Server, Azure SQL veritabanı ve Azure Azure SYNAPSE Analytics 'i sorgulamak için [MSSQL uzantısı](https://aka.ms/mssql-marketplace) dahil olmak üzere uzantıları destekler.|

SQL veritabanı, macOS, Linux ve Windows 'da Python, Java, Node.js, PHP, Ruby ve .NET uygulamaları oluşturmayı destekler. SQL Veritabanı, SQL Server ile aynı [bağlantı kitaplıklarını destekler](connect-query-content-reference-guide.md#libraries).

[!INCLUDE [sql-database-create-manage-portal](../includes/sql-database-create-manage-portal.md)]

## <a name="sql-database-frequently-asked-questions"></a>SQL veritabanı sık sorulan sorular

### <a name="can-i-control-when-patching-downtime-occurs"></a>Düzeltme eki uygulama süresinin ne zaman gerçekleşeceğini denetleyebilir miyim?

Hayır. Uygulamanızda [yeniden deneme mantığı](develop-overview.md#resiliency) kullanırsanız, düzeltme eki uygulamanın etkisi genellikle fark etmez. Daha fazla bilgi için bkz. [Azure SQL veritabanı 'Nda Azure bakım olaylarını planlama](planned-maintenance.md).



## <a name="engage-with-the-sql-server-engineering-team"></a>SQL Server mühendislik ekibi ile iletişime geçme

- [DBA Stack Exchange](https://dba.stackexchange.com/questions/tagged/sql-server): veritabanı yönetim sorularına sorun.
- [Stack Overflow](https://stackoverflow.com/questions/tagged/sql-server): geliştirme sorularını sorun.
- [Microsoft Q&soru sayfası](/answers/topics/azure-synapse-analytics.html): Teknik sorular sorun.
- [Geri bildirim](https://aka.ms/sqlfeedback): hataları ve istek özelliklerini bildirin.
- [Reddit](https://www.reddit.com/r/SQLServer/): SQL Server tartışın.

## <a name="next-steps"></a>Sonraki adımlar

- Tek veritabanları ve elastik havuzlarla ilgili maliyet karşılaştırmaları ve hesaplayıcıları için [fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/sql-database/) bakın.
- Kullanmaya başlamak için şu hızlı başlangıçlara bakın:

  - [Azure portal veritabanı oluşturma](single-database-create-quickstart.md)  
  - [Azure CLı ile veritabanı oluşturma](az-cli-script-samples-content-guide.md)
  - [PowerShell kullanarak veritabanı oluşturma](powershell-script-content-guide.md)

- Azure CLI ve PowerShell örnekleri için bkz.:
  - [SQL Veritabanı için Azure CLI örnekleri](az-cli-script-samples-content-guide.md)
  - [SQL Veritabanı için Azure PowerShell örnekleri](powershell-script-content-guide.md)

- Duyurdukları yeni yetenekler hakkında daha fazla bilgi için bkz. [SQL veritabanı Için Azure yol haritası](https://azure.microsoft.com/roadmap/?category=databases).
- SQL Server ürün ekibi üyelerinin SQL veritabanı haberleri ve özellikleri hakkında blogdaki [Azure SQL veritabanı blogu](https://azure.microsoft.com/blog/topics/database)' na bakın.