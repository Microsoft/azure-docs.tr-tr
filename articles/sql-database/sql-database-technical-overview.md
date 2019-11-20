---
title: Azure SQL Veritabanı hizmeti nedir?
description: "SQL veritabanı 'na giriş: bulutta Microsoft ilişkisel veritabanı yönetim sistemi 'nin (RDBMS) teknik ayrıntıları ve özellikleri."
keywords: sql'e giriş,sql veritabanı nedir
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 04/08/2019
ms.openlocfilehash: 3932c22ff003a343e4c32aee117a7ddea922fbdb
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820737"
---
# <a name="what-is-the-azure-sql-database-service"></a>Azure SQL Veritabanı hizmeti nedir?

Azure SQL veritabanı, yönetilen bir hizmet olarak sağlanmış olan genel amaçlı ilişkisel bir veritabanıdır. Bununla birlikte, Azure 'daki uygulamalar ve çözümler için yüksek düzeyde kullanılabilir ve yüksek performanslı bir veri depolama katmanı oluşturabilirsiniz. SQL veritabanı, hem ilişkisel verileri hem de [ilişkisel olmayan yapıları](sql-database-multi-model-features.md)(GRAFIKLER, JSON, uzamsal ve XML gibi) işleyebilmenizi sağladığından, çeşitli modern bulut uygulamaları için doğru seçim yapılabilir.

[Microsoft SQL Server veritabanı altyapısının](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json)en son kararlı sürümünü temel alır. [Yüksek performanslı bellek içi teknolojiler](sql-database-in-memory.md) ve [akıllı sorgu işleme](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing?toc=/azure/sql-database/toc.json)gibi gelişmiş sorgu işleme özelliklerini kullanabilirsiniz. Aslında SQL Server 'nin en yeni özellikleri önce SQL veritabanı 'na, sonra da SQL Server. En yeni SQL Server yeteneklerini, düzeltme eki uygulama veya yükseltme için ek yük olmadan, milyonlarca veritabanı üzerinden test etmeniz gerekir. 

SQL veritabanı iki farklı satın alma modelinde performansı kolayca tanımlamanızı ve ölçeklendirmenizi sağlar: [sanal çekirdek tabanlı satın alma modeli](sql-database-service-tiers-vcore.md) ve [DTU tabanlı satın alma modeli](sql-database-service-tiers-dtu.md). SQL veritabanı, yerleşik yüksek kullanılabilirlik, yedeklemeler ve diğer yaygın bakım işlemlerine sahip olan tam olarak yönetilen bir hizmettir. Microsoft, SQL ve işletim sistemi kodunun tüm düzeltme ekini ve güncelleştirilmesini işler. Temel altyapıyı yönetmeniz gerekmez.

> [!NOTE]
> İlgili hüküm ve tanımları için bkz. [SQL veritabanı terimleri sözlüğü](sql-database-glossary-terms.md).

## <a name="deployment-models"></a>Dağıtım modelleri

Azure SQL Veritabanı, bir Azure SQL veritabanı için aşağıdaki dağıtım seçeneklerini sunar:

![Dağıtım seçenekleri diyagramı](./media/sql-database-technical-overview/deployment-options.png)

- [Tek veritabanı](sql-database-single-database.md) , tam olarak yönetilen, yalıtılmış bir veritabanını temsil eder. Tek bir güvenilir veri kaynağı gerektiren modern bulut Uygulamalarınız ve mikro hizmetlerinize sahipseniz bu seçeneği kullanabilirsiniz. Tek bir veritabanı, [Microsoft SQL Server veritabanı altyapısında](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json) [Kapsanan veritabanına](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases?toc=/azure/sql-database/toc.json) benzerdir.
- [Yönetilen örnek](sql-database-managed-instance.md) , [Microsoft SQL Server veritabanı altyapısının](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json)tam olarak yönetilen bir örneğidir. Birlikte kullanılabilecek bir veritabanı kümesi içerir. Şirket içi SQL Server veritabanlarının Azure bulutuna kolayca geçirilmesi ve SQL Server veritabanı altyapısının sağladığı veritabanı özelliklerini kullanması gereken uygulamalar için bu seçeneği kullanın.
- [Elastik havuz](sql-database-elastic-pool.md) , CPU veya bellek gibi paylaşılan bir kaynak kümesiyle [tek bir veritabanı](sql-database-single-database.md) koleksiyonudur. Tek veritabanları, elastik bir havuzun içine ve dışına taşınabilir.

> [!IMPORTANT]
> SQL veritabanı ile SQL Server arasındaki özellik farklarını ve farklı Azure SQL veritabanı dağıtım seçenekleri arasındaki farkları anlamak için bkz. [SQL veritabanı özellikleri](sql-database-features.md).

SQL veritabanı, birden çok kaynak türü, hizmet katmanı ve işlem boyutları ile öngörülebilir performans sunar. Kapalı kalma süresi olmadan dinamik ölçeklenebilirlik, yerleşik akıllı iyileştirme, küresel ölçeklenebilirlik ve kullanılabilirlik ve gelişmiş güvenlik seçenekleri sağlar. Bu yetenekler, sanal makineleri ve altyapıyı yönetmek yerine hızlı uygulama geliştirmeye odaklanmanıza ve pazara ulaşma süresini hızlandırmanıza olanak tanır. SQL veritabanı hizmeti şu anda dünyanın dört bir yanındaki 38 veri merkezinde bulunur, bu sayede veritabanınızı yakınınızdaki bir veri merkezinde çalıştırabilirsiniz.

## <a name="scalable-performance-and-pools"></a>Ölçeklenebilir performans ve havuzlar

Atanan kaynak miktarını belirleyebilirsiniz. 
- Tek veritabanları ile, her veritabanı diğerlerinden yalıtılmıştır ve taşınabilir. Her birinin garantili işlem, bellek ve depolama kaynağı miktarına sahip olması gerekir. Veritabanına atanan kaynakların miktarı söz konusu veritabanına ayrılmıştır ve Azure 'daki diğer veritabanlarıyla paylaşılmaz. [Tek veritabanı kaynaklarını](sql-database-single-database-scale.md) dinamik olarak yukarı ve aşağı ölçekleyebilirsiniz. Tek veritabanı seçeneği, farklı gereksinimlere göre farklı işlem, bellek ve depolama kaynakları sağlar. Örneğin, 1 ila 80 sanal çekirdek veya 32 GB ila 4 TB alabilir. Tek veritabanı için [hiperscale hizmet katmanı](sql-database-service-tier-hyperscale.md) , hızlı yedekleme ve geri yükleme özellikleri Ile 100 TB 'ye ölçeklenmenize olanak sağlar.
- Elastik havuzlarla, havuzdaki tüm veritabanları tarafından paylaşılan kaynaklar atayabilirsiniz. Kaynak kullanımını en üst düzeye çıkarmak ve tasarruf etmek için yeni bir veritabanı oluşturabilir veya var olan tek veritabanlarını bir kaynak havuzuna taşıyabilirsiniz. Bu seçenek ayrıca [Esnek havuz kaynaklarını](sql-database-elastic-pool-scale.md) yukarı ve aşağı dinamik olarak ölçeklendirmenize olanak tanır.
- Yönetilen örneklerle, her yönetilen örnek garantili kaynaklarla diğer örneklerden yalıtılmıştır. Yönetilen bir örnek içinde, örnek veritabanları bir kaynak kümesini paylaşır. [Yönetilen örnek kaynaklarını](sql-database-managed-instance-resource-limits.md) dinamik olarak yukarı ve aşağı ölçeklendirebilirsiniz.

İlk uygulamanızı küçük, tek bir veritabanında, genel amaçlı hizmet katmanında aylık düşük maliyetli bir şekilde oluşturabilirsiniz. Daha sonra, çözümünüzün ihtiyaçlarını karşılamak üzere iş açısından kritik hizmet katmanına hizmet katmanını el ile veya programlama yoluyla dilediğiniz zaman değiştirebilirsiniz. Performansı uygulamanız veya müşterileriniz kesinti yaşamadan ayarlayabilirsiniz. Dinamik ölçeklendirebilirlik, veritabanınızın hızla değişen kaynak gereksinimlerine saydam bir şekilde yanıt verebilmesini sağlar. Yalnızca ihtiyacınız olan kaynaklar için ödeme yaparsınız.

*Dinamik ölçeklenebilirlik* , *Otomatik ölçeklendirmeyi*farklıdır. Bir hizmet ölçütlere dayalı olarak otomatik şekilde ölçeklendirildiğinde otomatik ölçeklendirme oluşurken, dinamik ölçeklenebilirlik ise kesinti süresi olmadan el ile ölçeklendirmeye olanak sağlar. Tek veritabanı seçeneği el ile dinamik ölçeklenebilirliği destekler, ancak otomatik ölçeklendirme gerektirmez. Daha otomatik bir deneyim için, veritabanlarının bir havuzdaki kaynakları tek tek veritabanı ihtiyaçlarına göre paylaşmasına izin veren elastik havuzlar kullanmayı düşünün. Diğer bir seçenek de tek bir veritabanı için ölçeklenebilirliği otomatik hale getirmeye yardımcı olabilecek betikleri kullanmaktır. Bir örnek için bkz. [tek bir veritabanını izlemek ve ölçeklendirmek Için PowerShell 'ı kullanma](scripts/sql-database-monitor-and-scale-database-powershell.md).

### <a name="purchasing-models"></a>Satın alma modelleri

SQL veritabanı aşağıdaki satın alma modellerini sunmaktadır:
- [Sanal çekirdek tabanlı satın alma modeli](sql-database-service-tiers-vcore.md) , sanal çekirdek sayısını, bellek miktarını ve depolama alanı miktarını ve hızını seçmenize olanak sağlar. Sanal çekirdek tabanlı satın alma modeli, maliyet tasarrufu kazanmak için [SQL Server Azure hibrit avantajı](https://azure.microsoft.com/pricing/hybrid-benefit/) kullanmanıza da olanak tanır. Azure Hibrit Avantajı hakkında daha fazla bilgi için, bu makalenin devamındaki "sık sorulan sorular" bölümüne bakın.
- [DTU tabanlı satın alma modeli](sql-database-service-tiers-dtu.md) , hafif veritabanı iş yüklerini desteklemek için üç hizmet katmanında işlem, bellek ve g/ç kaynaklarının bir karışımını sunar. Her katmandaki işlem boyutları bu kaynakların farklı bir karışımını sağlar ve bu da ek depolama kaynakları ekleyebilirsiniz.
- [Sunucusuz model](sql-database-serverless.md) , iş yükü talebine göre işlem sayısını otomatik olarak ölçeklendirir ve saniye başına kullanılan işlem miktarına göre faturalandırılır. Sunucusuz işlem katmanı Ayrıca, yalnızca depolama faturalandırılırken, etkin olmayan dönemler sırasında veritabanlarını otomatik olarak duraklatır ve etkinlik döndüğünde veritabanlarını otomatik olarak sürdürür.

### <a name="service-tiers"></a>Hizmet katmanları

Azure SQL veritabanı, farklı türlerde uygulamalar için tasarlanan üç hizmet katmanı sunar:
- Ortak iş yükleri için tasarlanan [genel amaçlı/standart](sql-database-service-tier-general-purpose.md) hizmet katmanı. Bütçeye dayalı dengeli işlem ve depolama seçenekleri sunar.
- Yüksek işlem oranı ve en düşük gecikmeli g/ç özellikli OLTP uygulamaları için tasarlanan [iş açısından kritik/Premium](sql-database-service-tier-business-critical.md) hizmet katmanı. Birkaç yalıtılmış çoğaltma kullanarak hatalara en yüksek esnekliği sağlar.
- Çok büyük OLTP veritabanı için tasarlanan [hiper ölçekli](sql-database-service-tier-hyperscale.md) hizmet katmanı ve depolamayı otomatik olarak ölçeklendirme ve işlem akıcı bir şekilde ölçeklendirme özelliği. 

### <a name="elastic-pools-to-maximize-resource-utilization"></a>Kaynak kullanımını en verimli hale getirmek için elastik havuzlar

Tek veritabanı oluşturabilmek ve veritabanı performansını isteğe göre yükseltip düşürebilmek, özellikle kullanım biçimlerinin nispeten tahmin edilebilir olduğu durumlarda birçok işletme ve uygulama için yeterlidir. Öngörülemeyen kullanım desenleri, maliyetleri ve iş modelinizi yönetmeyi zorlaştırır. [Elastik havuzlar](sql-database-elastic-pool.md) bu sorunu çözmek için tasarlanmıştır. Performans kaynaklarını ayrı bir veritabanı yerine bir havuza ayırabilirsiniz. Tek veritabanı performansı yerine havuzun toplu performans kaynakları için ödeme yaparsınız.

   ![Temel, standart ve Premium sürümlerde elastik havuzlar gösteren grafik](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Elastik havuzlar sayesinde kaynak talebindeki dalgalanmalara ayak uydurmak için veritabanı performansında sürekli ayarlama yapmanız gerekmez. Havuza alınan veritabanları, gerektiğinde elastik havuzun performans kaynaklarını tüketir. Havuza alınan veritabanları havuzu kullanır ancak havuz sınırlarını aşmaz, böylece veritabanı kullanımınız tahmin edilebilir olmasa bile maliyetleriniz için durum tam tersidir.

[Havuza veritabanları ekleyip kaldırabilir](sql-database-elastic-pool-manage-portal.md), uygulamanızı, sizin denetlediğiniz bir bütçeyle binlerce veritabanına ölçeklendirebilirsiniz. Havuzdaki veritabanları için kullanılabilen minimum ve maksimum kaynakları denetleyebilir, havuzdaki hiçbir veritabanının havuz kaynaklarını kullandığından ve havuza alınan her veritabanının garantili minimum kaynak miktarına sahip olduğundan emin olmanız gerekir. Elastik havuzlar kullanan hizmet olarak yazılım (SaaS) uygulamaları için tasarım desenleri hakkında daha fazla bilgi edinmek için bkz. [SQL veritabanı ile çok kiracılı SaaS uygulamaları Için tasarım desenleri](sql-database-design-patterns-multi-tenancy-saas-applications.md).

Betikler, elastik havuzların izlenmesine ve ölçeklendirilmesine yardımcı olabilir. Bir örnek için bkz. [Azure SQL veritabanında BIR SQL elastik havuzunu izlemek ve ölçeklendirmek Için PowerShell 'ı kullanma](scripts/sql-database-monitor-and-scale-pool-powershell.md).

> [!IMPORTANT]
> Yönetilen bir örnek elastik havuzları desteklemez. Bunun yerine, yönetilen örnek, yönetilen örnek kaynaklarını paylaşan örnek veritabanlarının bir koleksiyonudur.

### <a name="blend-single-databases-with-pooled-databases"></a>Tek veritabanlarını havuza alınan veritabanlarıyla karıştırma

Tek veritabanlarını elastik havuzlarla karıştırabilirsiniz ve tek veritabanlarının ve elastik havuzların hizmet katmanlarını, durumunuza uyum sağlamak için değiştirebilirsiniz. Ayrıca, benzersiz uygulama tasarımı ihtiyaçlarınızı karşılamak, maliyet ve kaynak verimliliği sağlamak ve yeni iş fırsatlarının kilidini açmak için diğer Azure hizmetlerini SQL veritabanı ile karıştırabilir ve eşleştirebilirsiniz.

## <a name="extensive-monitoring-and-alerting-capabilities"></a>Kapsamlı izleme ve uyarı özellikleri

Azure SQL veritabanı, iş yükü özelliklerine daha derin Öngörüler elde etmenize yardımcı olan gelişmiş izleme ve sorun giderme özellikleri sağlar. Bu özellikler ve araçlar şunları içerir:
 - SQL Server veritabanı altyapısının en son sürümü tarafından sunulan yerleşik izleme özellikleri. Gerçek zamanlı performans öngörülerini bulmanıza olanak tanır. 
 - Azure tarafından sunulan PaaS izleme özellikleri, çok sayıda veritabanı örneğini izlemenize ve gidermenize olanak sağlar.

[Sorgu deposu](sql-database-operate-query-store.md), yerleşik bir SQL Server izleme özelliği, Sorgularınızın performansını gerçek zamanlı olarak kaydeder ve olası performans sorunlarını ve en üst kaynak tüketicilerini tanımlamanızı sağlar. Otomatik ayarlama ve öneriler, gerileme performansına ve eksik veya yinelenen dizinlere sahip sorgularla ilgili öneriler sağlar. SQL veritabanı 'ndaki otomatik ayarlama, sorunları giderebilen betikleri el ile uygulamanızı veya SQL veritabanının bu sorunu çözmesine izin verir. SQL veritabanı Ayrıca, düzeltmesinin bazı avantajlardan yararlandığına da test edebilir ve sonuca bağlı olarak değişikliği korur veya döndürmenizi sağlar. Sorgu deposu ve otomatik ayarlama özelliklerine ek olarak, iş yükü performansını izlemek için standart [DMVs ve XEvent](sql-database-monitoring-with-dmvs.md) ' yi de kullanabilirsiniz.

Azure, binlerce veritabanının durumunu izlemenize olanak sağlayan, performans derecelendirmeleri ile birleştirilmiş [yerleşik performans izleme](sql-database-performance.md) ve [Uyarı](sql-database-insights-alerts-portal.md) araçları sağlar. Bu araçları kullanarak, geçerli veya tahmini performans ihtiyaçlarınıza göre ölçeği artırma veya azaltma etkilerini hızla değerlendirebilirsiniz. SQL Veritabanı ayrıca izlemeyi kolaylaştırmak için [ölçümler ve tanılama günlükleri oluşturabilir](sql-database-metrics-diag-logging.md). SQL Veritabanını kaynak kullanımını, çalışanları, oturumları ve bu Azure kaynaklarından birine yapılan bağlantıları kaydedecek şekilde yapılandırabilirsiniz:

- **Azure Depolama**: Küçük maliyetlerle çok sayıda telemetri arşivleme için.
- **Azure Event Hubs**: SQL veritabanı telemetrisini özel izleme çözümünüz veya etkin işlem hatlarınız ile tümleştirmek için.
- **Azure izleyici günlükleri**: raporlama, uyarı ve azaltıcı yetenekler içeren yerleşik bir izleme çözümü için.

![Azure izleme mimarisi diyagramı](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="availability-capabilities"></a>Kullanılabilirlik özellikleri

Geleneksel bir SQL Server ortamında, genellikle en az iki makineniz yerel olarak ayarlanır. Bu makineler, tek bir makine veya bileşen hatasına karşı korumak için verilerin tam, zaman uyumlu olarak korunmasını sağlar. Bu ortam yüksek kullanılabilirlik sağlar, ancak veri merkezinizi yok etmek doğal bir olağanüstü duruma karşı koruma sağlamaz.

Olağanüstü durum kurtarma, çok zararlı bir olayın, verilerinizin bir kopyasına sahip başka bir makineye veya makine kümesine sahip olacak kadar coğrafi olarak yerelleştirilmiş olduğunu varsayar. SQL Server, bu özelliği almak için zaman uyumsuz modda çalışan her zaman açık kullanılabilirlik gruplarını kullanabilirsiniz. Kullanıcılar genellikle çoğaltma işlemini bir işlem gerçekleştirmeden önce uzakta olmasını beklemek istemeiyorsunuzdur, bu nedenle planlanmamış yük devretme işlemleri gerçekleştirdiğinizde veri kaybı meydana gelir.

Premium ve iş açısından kritik hizmet katmanlarındaki veritabanları, bir kullanılabilirlik grubunun eşitlemesine [çok benzer bir şey zaten yapılır](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) . Daha düşük hizmet katmanlarındaki veritabanları, [farklı ancak eşdeğer bir mekanizma](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability)kullanarak depolama aracılığıyla artıklık sağlar. Yerleşik mantık, tek bir makine hatasına karşı korunmaya yardımcı olur. Etkin coğrafi çoğaltma özelliği, tüm bir bölgenin yok edileceği olağanüstü duruma karşı koruma olanağı sağlar.

Azure Kullanılabilirlik Alanları, tek bir bölge içinde tek bir veri merkezi oluşturma kesintisinden korunmanıza çalışır. Bir binadan güç veya ağ kaybına karşı korumaya yardımcı olur. SQL veritabanı 'nda farklı çoğaltmaları farklı kullanılabilirlik bölgelerine (farklı binalar, etkin) yerleştirebilirsiniz.

Aslında, Microsoft tarafından yönetilen küresel bir veri merkezi ağı tarafından desteklenen Azure 'un hizmet düzeyi sözleşmesi [(SLA)](https://azure.microsoft.com/support/legal/sla/) , uygulamanızın 24/7 çalışmasına yardımcı olur. Azure platformu her veritabanını tamamen yönetir ve veri kaybı ve yüksek oranda veri kullanılabilirliği garantisi vermez. Azure, düzeltme eki uygulama, yedeklemeler, çoğaltma, hata algılama, temel alınan olası donanım, yazılım veya ağ hataları, hata düzeltmelerini dağıtma, yük devretme, veritabanı yükseltmeleri ve diğer bakım görevlerini otomatik olarak işler. Standart kullanılabilirlik düzeyine ulaşmak için işlem ve depolama katmanları ayrılır. Premium kullanılabilirlik, performans için işlem ve depolamayı tek bir düğümde tümleştirerek ve sonra her zaman açık kullanılabilirlik gruplarıyla benzer bir teknoloji uygulayarak elde edilir. Azure SQL veritabanı 'nın yüksek kullanılabilirlik özelliklerine ilişkin tam bir tartışma için bkz. [SQL veritabanı kullanılabilirliği](sql-database-high-availability.md). 

Ayrıca, SQL veritabanı yerleşik [iş sürekliliği ve küresel ölçeklenebilirlik](sql-database-business-continuity.md) özellikleri sağlar. Bunlar:

- [Otomatik yedeklemeler](sql-database-automated-backups.md):

  SQL veritabanı, zaman içinde herhangi bir noktaya geri yükleme olanağı sağlamak için SQL veritabanlarının tam, değişiklik ve işlem günlüğü yedeklemelerini otomatik olarak gerçekleştirir. Tek veritabanları ve havuza alınmış veritabanları için SQL veritabanını, uzun süreli yedek saklama için Azure depolama 'ya tam veritabanı yedeklerini depolayacak şekilde yapılandırabilirsiniz. Yönetilen örnekler için, uzun süreli yedek saklama için yalnızca kopya yedeklemeler de gerçekleştirebilirsiniz.

- [Zaman içinde bir noktaya geri yükleme](sql-database-recovery-using-backups.md):

  Tüm SQL veritabanı dağıtım seçenekleri herhangi bir SQL veritabanı için otomatik yedekleme saklama süresi içinde herhangi bir zaman noktasına kurtarmayı destekler.
- [Etkin coğrafi çoğaltma](sql-database-active-geo-replication.md):

  Tek veritabanı ve havuza alınmış veritabanları seçenekleri, aynı veya küresel olarak dağıtılmış Azure veri merkezlerinde dört adede kadar okunabilir ikincil veritabanını yapılandırmanıza olanak tanır. Örneğin, bir katalog veritabanına sahip ve yüksek miktarda eşzamanlı salt okuma işlemlerine sahip bir SaaS uygulamanız varsa, genel okuma ölçeğini etkinleştirmek için etkin coğrafi çoğaltma ' yı kullanın. Bu, okuma iş yüklerinden kaynaklanan birincil üzerinde performans sorunlarını ortadan kaldırır. Yönetilen örnekler için otomatik yük devretme grupları kullanın.
- [Otomatik yük devretme grupları](sql-database-auto-failover-group.md):

  Tüm SQL veritabanı dağıtım seçenekleri, küresel ölçekte yüksek kullanılabilirlik ve yük dengelemeyi etkinleştirmek için yük devretme gruplarını kullanmanıza izin verir. Buna saydam coğrafi çoğaltma ve büyük veritabanı kümelerinin, elastik havuzların ve yönetilen örneklerin yük devretmesi dahildir. Yük devretme grupları, genel olarak dağıtılmış SaaS uygulamalarının oluşturulmasını, en az yönetim yüküyle etkinleştirir. Bu, tüm karmaşık izleme, Yönlendirme ve yük devretme düzenlemesini SQL veritabanı 'na bırakır.
- Bölgesel olarak [yedekli veritabanları](sql-database-high-availability.md):

  SQL veritabanı, birden çok kullanılabilirlik alanında Premium veya iş açısından kritik veritabanları ya da elastik havuzlar sağlamanıza olanak tanır. Bu veritabanları ve elastik havuzlar yüksek kullanılabilirlik için birden fazla yedekli çoğaltma içerdiğinden, bu çoğaltmaların birden fazla kullanılabilirlik bölgesine yerleştirilmesi daha yüksek esnekliği sağlar. Bu, veri kaybı olmadan veri merkezi ölçek hatalarından otomatik olarak kurtarma özelliği içerir.

## <a name="built-in-intelligence"></a>Yerleşik zeka

SQL veritabanı ile, veritabanlarını çalıştırmanın ve yönetmenin maliyetlerini önemli ölçüde düşürmenize ve uygulamanızın hem performansını hem de güvenliğini en üst düzeye çıkaran yerleşik zeka alırsınız. Saat içinde milyonlarca müşteri iş yükü çalıştırıldığında, SQL veritabanı büyük miktarda telemetri verilerini toplayıp işler ve ayrıca müşteri gizliliğini tamamen de tam olarak alır. Çeşitli algoritmalar, hizmetin uygulamanızı öğrenmesine ve uyarlanabilmesi için telemetri verilerini sürekli olarak değerlendirir.

### <a name="automatic-performance-monitoring-and-tuning"></a>Otomatik performans izleme ve ayarlama

SQL Veritabanı izlemeniz gereken sorgularla ilgili ayrıntılı öngörüler sunar. SQL veritabanı, veritabanı desenlerinizi öğrenir ve veritabanı şemanızı iş yükünüze uyarlamanızı sağlar. SQL Veritabanı, [performans ayarlama önerilerinde](sql-database-advisor.md) bulunur. Siz de bu ayarları gözden geçirebilir ve uygulayabilirsiniz.

Ancak, bir veritabanını sürekli olarak izlemek, özellikle birçok veritabanı ile ilgilenirken zor ve sıkıcı bir görevdir. [Akıllı içgörüler](sql-database-intelligent-insights.md) , SQL veritabanı performansını ölçekte otomatik olarak izleyerek sizin için bu işi yapar. Performans düşüşü sorunları hakkında sizi bilgilendirir, her sorunun kök nedenini tanımlar ve mümkün olduğunda performans iyileştirme önerileri sağlar.

SQL veritabanı ve Azure 'un sağladığı tüm kullanılabilir araçlar ve raporlardan çok fazla sayıda veritabanını yönetmek imkansız olabilir. Veritabanınızı el ile izlemek ve ayarlamak yerine, [otomatik ayarlama](sql-database-automatic-tuning.md)kullanarak SQL veritabanı 'na bazı izleme ve ayarlama eylemlerinin yetkisini aktarmaya yarar sağlayabilirsiniz. SQL veritabanı, performansın sürekli olarak iyileştirildiğinden emin olmak için öneri, test ve ayarlama eylemlerinin her birini doğrular. Bu şekilde, SQL veritabanı otomatik olarak denetimli ve güvenli bir şekilde iş yükünüze uyum sağlar. Otomatik ayarlama, veritabanınızın performansının dikkatle izlenen ve her ayarlama eyleminden önce ve sonra karşılaştırıldığı anlamına gelir. Performans geliştirmezse ayarlama eylemi geri döndürülür.

SQL veritabanı üzerinde [SaaS çok kiracılı uygulamalar](sql-database-design-patterns-multi-tenancy-saas-applications.md) çalıştıran iş ortaklarımızın birçoğu, uygulamalarının sürekli ve öngörülebilir performansa sahip olduğundan emin olmak için otomatik performans ayarlamaya bağlıdır. İş ortaklarımız bu özelliğin gecenin ortasında performans sorunu yaşama riskini önemli ölçüde azalttığını söylüyor. Ayrıca, müşteri tabanlarına ait bir parçası SQL Server kullandığından, SQL Server müşterilerine yardımcı olmak için SQL veritabanı tarafından sağlanan dizin oluşturma önerilerini de kullanıyor.

[SQL veritabanında](sql-database-automatic-tuning.md)iki otomatik ayarlama yönü mevcuttur:

- **Otomatik dizin yönetimi**: Veritabanınıza eklenmesi ve veritabanınızdan kaldırılması gereken dizinleri tanımlar.
- **Otomatik plan düzeltme**: sorunlu planları TANıMLAR ve SQL planı performans sorunlarını düzeltir.

### <a name="adaptive-query-processing"></a>Uyarlamalı sorgu işleme

Çok deyimli tablo değerli işlevler, toplu iş modu bellek verme geri bildirimi ve toplu iş modu Uyarlamalı birleşimler için araya eklemeli yürütme dahil olmak üzere [Uyarlamalı sorgu işleme](/sql/relational-databases/performance/intelligent-query-processing)kullanabilirsiniz. Bu uyarlamalı sorgu işleme özelliklerinin her biri benzer "öğren ve uyarla" tekniklerini uygulayarak geçmişe dönük zorlu sorgu iyileştirme sorunlarıyla ilgili performans sorunlarında yardımcı olmaktadır.

## <a name="advanced-security-and-compliance"></a>Gelişmiş koruma ve uyumluluk

SQL Veritabanı, uygulamanızın çeşitli güvenlik ve uyumluluk gereksinimlerine uygun olmasına yardımcı olmak için bir dizi [yerleşik güvenlik ve uyum özelliğine](sql-database-security-overview.md) sahiptir.

> [!IMPORTANT]
> Microsoft, bir dizi uyumluluk standartlarına karşı Azure SQL veritabanı 'na (tüm dağıtım seçenekleri) sahiptir. Daha fazla bilgi için, SQL veritabanı uyumluluk sertifikalarının en güncel listesini bulabileceğiniz [Microsoft Azure Güven Merkezi](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)' ne bakın.

### <a name="advance-threat-protection"></a>Tehdit korumasını ilerlet

Gelişmiş veri güvenliği, gelişmiş SQL güvenlik özelliklerine yönelik Birleşik bir pakettir. Hassas verileri bulma ve sınıflandırma, veritabanınızın güvenlik açıklarını yönetme ve veritabanınıza yönelik bir tehdit oluşturabilecek anormal etkinlikleri algılama işlevlerini içerir. Bu özellikleri etkinleştirmek ve yönetmek için tek bir konum sağlar.

- [Veri bulma ve sınıflandırma](sql-database-data-discovery-and-classification.md):

  Bu özellik, veritabanlarınızdaki hassas verileri bulmak, sınıflandırmak, etiketlemek ve korumak için Azure SQL veritabanı 'nda yerleşik olarak bulunan yetenekler sağlar. Veritabanı sınıflandırma durumlarınızın görünürlüğünü sağlar ve veritabanı içindeki hassas verilere erişimi izler ve sınırlarının ötesine dönüştürür.
- [Güvenlik açığı değerlendirmesi](sql-vulnerability-assessment.md):

  Bu hizmet, olası veritabanı güvenlik açıklarını düzeltmenizi, izleyebilir ve yardımcı olabilir. Güvenlik durumunuz hakkında görünürlük sağlamasının yanı sıra güvenlik sorunlarınızı çözmek ve veritabanı güçlendirmelerinizi geliştirmek için eyleme dönüştürülebilir adımlar sunar.
- [Tehdit algılama](sql-database-threat-detection.md):

  Bu özellik, veritabanınıza erişmek veya veritabanına yararlanmak için olağan dışı ve zararlı olabilecek girişimleri gösteren anormal etkinlikleri algılar. Veritabanınızı şüpheli etkinliklere karşı sürekli izler ve olası güvenlik açıkları, SQL ekleme saldırıları ve anormal veritabanı erişim modelleri hakkında anında güvenlik uyarıları sunar. Tehdit algılama uyarıları şüpheli etkinliğin ayrıntılarını sağlar ve tehdidi araştırmak ve hafifletmek için eyleme önerilir.

### <a name="auditing-for-compliance-and-security"></a>Uyumluluk ve güvenlik denetimi

[Denetim](sql-database-auditing.md) , veritabanı olaylarını izler ve bunları Azure Depolama hesabınızdaki bir denetim günlüğüne yazar. Denetim, yasal uyumluluğu korumanıza, veritabanı etkinliklerini anlamanıza ve işle ilgili endişeleri veya şüpheli güvenlik ihlallerini gösteren tutarsızlıklar ve bozukluklar elde etmenize yardımcı olabilir.

### <a name="data-encryption"></a>Veri şifrelemesi

SQL veritabanı, şifreleme sağlayarak verilerinizin güvenliğinin sağlanmasına yardımcı olur. Hareket halindeki veriler için [Aktarım Katmanı Güvenliği](https://support.microsoft.com/kb/3135244)kullanır. Bekleyen veriler için [Saydam veri şifrelemesi](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)kullanır. Kullanılan veriler için [her zaman şifreli](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine)' i kullanır.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory tümleştirmesi ve çok faktörlü kimlik doğrulaması

SQL Veritabanı, [Azure Active Directory tümleştirmesi](sql-database-aad-authentication.md) ile veritabanı kullanıcısı ve diğer Microsoft hizmetleri kimliklerini bir merkezden yönetmenizi sağlar. Bu özellik, izin yönetimini kolaylaştırırken güvenliği artırır. Azure Active Directory, çoklu bir oturum açma işlemini desteklerken, veri ve uygulama güvenliğini artırmak için [Multi-Factor Authentication](sql-database-ssms-mfa-authentication.md) 'ı destekler.

## <a name="easy-to-use-tools"></a>Kullanımı kolay araçlar

SQL Veritabanı uygulama oluşturma ve uygulamaların bakımını yapma işlemlerinin daha kolay ve daha verimli şekilde yapılmasını sağlar. SQL Veritabanı size, en iyi yaptığınız işe; mükemmel uygulamalar oluşturmaya odaklanma seçeneği sunar. Zaten sahip olduğunuz araçları ve becerileri kullanarak SQL veritabanı 'nda yönetebilir ve geliştirebilirsiniz.

- [Azure Portal](https://portal.azure.com/):

  Tüm Azure hizmetlerini yönetmek için Web tabanlı bir uygulama.
- [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms):

  SQL Server SQL veritabanı 'na kadar herhangi bir SQL altyapısını yönetmeye yönelik ücretsiz, indirilebilir bir istemci uygulaması.
- [Visual Studio 'da SQL Server veri araçları](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt):

  SQL Server ilişkisel veritabanları, SQL veritabanları, Tümleştirme Hizmetleri paketleri, Analysis Services veri modelleri ve Raporlama Hizmetleri raporları geliştirmeye yönelik ücretsiz, indirilebilir bir istemci uygulaması.
- [Visual Studio Code](https://code.visualstudio.com/docs):

  Windows, macOS ve Linux için ücretsiz, indirilebilir, açık kaynaklı bir kod Düzenleyicisi. Microsoft SQL Server, Azure SQL veritabanı ve Azure SQL veri ambarı 'nı sorgulamak için [MSSQL uzantısı](https://aka.ms/mssql-marketplace) dahil olmak üzere uzantıları destekler.

SQL veritabanı, macOS, Linux ve Windows 'da Python, Java, Node. js, PHP, Ruby ve .NET ile uygulama oluşturmayı destekler. SQL Veritabanı, SQL Server ile aynı [bağlantı kitaplıklarını destekler](sql-database-libraries.md).

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="sql-database-frequently-asked-questions"></a>SQL veritabanı sık sorulan sorular

### <a name="what-is-the-current-version-of-sql-database"></a>SQL veritabanı 'nın geçerli sürümü nedir?

SQL veritabanının geçerli sürümü v12. Sürüm V11 kullanımdan kaldırıldı.

### <a name="can-i-control-when-patching-downtime-occurs"></a>Düzeltme eki uygulama süresinin ne zaman gerçekleşeceğini denetleyebilir miyim?

Hayır. Uygulamanızda [yeniden deneme mantığı](sql-database-develop-overview.md#resiliency) kullanırsanız, düzeltme eki uygulamanın etkisi genellikle fark etmez. Daha fazla bilgi için bkz. [Azure SQL veritabanı 'Nda Azure bakım olaylarını planlama](sql-database-planned-maintenance.md).

### <a name="azure-hybrid-benefit-questions"></a>Azure Hibrit Avantajı sorular

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>SQL Server için Azure Hibrit Avantajı birlikte çift kullanım hakları var mı?

Geçişlerin sorunsuz çalıştığından emin olmak için lisansın 180 gün süreyle çift kullanım haklarına sahip olursunuz. Bu 180 gün sonra, SQL veritabanı 'nda yalnızca bulutta SQL Server lisansını kullanabilirsiniz. Artık şirket içinde ve bulutta ikili kullanım haklarına sahip olmanız gerekmez.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>SQL Server için Azure Hibrit Avantajı, lisans taşınabilirliği 'nden farklı midir?

Yazılım Güvencesi kapsamındaki müşterilere SQL Server için lisans taşınabilirliği avantajları sunuyoruz. Bu, lisanslarının iş ortağının paylaşılan sunucularına yeniden atanmasını sağlar. Bu avantajı Azure IaaS ve AWS EC2 üzerinde kullanabilirsiniz.

SQL Server için Azure Hibrit Avantajı, iki anahtar alanındaki lisans taşınabilirliğine göre farklılık gösterir:

- Yüksek oranda sanallaştırılmış iş yüklerini Azure 'a taşımak için ekonomik avantajlar sağlar. SQL Server Enterprise Edition müşterileri, Azure 'da, yüksek oranda sanallaştırılmış uygulamalar için şirket içinde sahip oldukları her çekirdeğe yönelik Genel Amaçlı SKU 'sunda dört çekirdek alabilir. Lisans Taşınabilirliği, sanallaştırılmış iş yüklerini buluta taşımak için özel maliyet avantajlarına izin vermez.
- Şirket içi SQL Server ile yüksek oranda uyumlu Azure 'da (SQL veritabanı yönetilen örneği) bir PaaS hedefi sağlar.

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>SQL Server için Azure Hibrit Avantajı belirli hakları nelerdir?

SQL veritabanı müşterileri, SQL Server için Azure Hibrit Avantajı ilişkili aşağıdaki haklara sahiptir:

|Lisans ayak izi|SQL Server için ne Azure Hibrit Avantajı?|
|---|---|
|SA ile SQL Server Enterprise sürümü çekirdek müşterileri|<li>Genel Amaçlı veya İş Açısından Kritik SKU 'sunda temel ücret ödeyebilir</li><br><li>1 çekirdek şirket içi = Genel Amaçlı SKU 'da 4 çekirdek</li><br><li>1 çekirdek şirket içi = 1 çekirdek İş Açısından Kritik SKU 'da</li>|
|SA ile SQL Server Standard sürümü çekirdek müşterileri|<li>Temel fiyat yalnızca Genel Amaçlı SKU üzerinde ödeme yapabilir</li><br><li>1 çekirdek şirket içi = 1 çekirdek Genel Amaçlı SKU 'da</li>|
|||

## <a name="engage-with-the-sql-server-engineering-team"></a>SQL Server mühendislik ekibi ile iletişime geçme

- [DBA Stack Exchange](https://dba.stackexchange.com/questions/tagged/sql-server): veritabanı yönetim sorularına sorun.
- [Stack Overflow](https://stackoverflow.com/questions/tagged/sql-server): geliştirme sorularını sorun.
- [MSDN forumları](https://social.msdn.microsoft.com/Forums/home?category=sqlserver): Teknik sorular sorun.
- [Geri bildirim](https://aka.ms/sqlfeedback): hataları ve istek özelliğini bildir.
- [Reddit](https://www.reddit.com/r/SQLServer/): SQL Server tartışın.

## <a name="next-steps"></a>Sonraki adımlar

- Tek veritabanları ve elastik havuzlarla ilgili maliyet karşılaştırmaları ve hesaplayıcıları için [fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/sql-database/) bakın.
- Kullanmaya başlamak için şu hızlı başlangıçlara bakın:

  - [Azure portalda SQL veritabanı oluşturma](sql-database-single-database-get-started.md)  
  - [Azure CLI ile SQL veritabanı oluşturma](sql-database-get-started-cli.md)
  - [PowerShell ile SQL veritabanı oluşturma](sql-database-get-started-powershell.md)

- Azure CLI ve PowerShell örnekleri için bkz.:
  - [SQL Veritabanı için Azure CLI örnekleri](sql-database-cli-samples.md)
  - [SQL Veritabanı için Azure PowerShell örnekleri](sql-database-powershell-samples.md)

- Duyurdukları yeni yetenekler hakkında daha fazla bilgi için bkz. [SQL veritabanı Için Azure yol haritası](https://azure.microsoft.com/roadmap/?category=databases).
- SQL Server ürün ekibi üyelerinin SQL veritabanı haberleri ve özellikleri hakkında blogdaki [Azure SQL veritabanı blogu](https://azure.microsoft.com/blog/topics/database)' na bakın.

