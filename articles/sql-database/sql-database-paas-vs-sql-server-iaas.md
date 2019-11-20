---
title: Doğru dağıtım seçeneğini belirleyin
description: SQL veritabanları, SQL yönetilen örnekler ve Azure sanal makinelerinde SQL Server arasında Azure SQL içindeki dağıtım seçenekleri arasından seçim yapmayı öğrenin.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
keywords: SQL Server bulut, bulutta SQL Server, PaaS veritabanı, bulut SQL Server, DBaaS
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/22/2019
ms.openlocfilehash: e642454807511e8e0bc0b6b6ca7af837e03de2cf
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821345"
---
# <a name="choose-the-right-deployment-option-in-azure-sql"></a>Azure SQL 'de doğru dağıtım seçeneğini belirleyin

Her dağıtım seçeneğinin Microsoft 'un Azure SQL veri platformuna nasıl uyduğunu öğrenin ve iş gereksinimleriniz için doğru seçenekle eşleşen yardım alın. Maliyet tasarruflarının veya en düşük yönetimin önceliklendirmenize bakılmaksızın, bu makale, en çok ilgilendiğiniz iş gereksinimlerine göre hangi yaklaşımın elde eteceğine karar vermenize yardımcı olabilir.

## <a name="microsofts-azure-sql-data-platform"></a>Microsoft 'un Azure SQL veri platformu

Yükseltme ve kaydırma geçişlerden, var olan uygulamaların basitleştirilmesini sağlamak için modern bulut hizmetleri oluşturmaya yönelik Azure SQL, sektör lideri Microsoft SQL Server altyapısı tarafından desteklenen çeşitli dağıtım seçenekleri sunan modern bir SQL platformudur. Azure SQL, en zorlu geçiş ve modernleştirme gereksinimlerini karşılamak için temel alınan platform üzerinde farklı denetim düzeylerine sahip çok çeşitli uygulama düzenlerini desteklemek üzere tasarlanmıştır. Azure SQL, tek ve birleştirilmiş bir yönetim deneyimi sunarak farklı SQL Server tabanlı uygulamaların farklı koleksiyonlarını yönetmenin karmaşıklığını ortadan kaldırır.

Azure ile şirket içi SQL Server veritabanlarının karşılaştırmasına yönelik herhangi bir tartışmada anlaşılması gereken ilk unsur, bunların tümünü kullanabileceğinizdir. Microsoft 'un veri platformu SQL Server teknolojiden yararlanır ve fiziksel şirket içi makineler, özel bulut ortamları, üçüncü taraf barındırılan özel bulut ortamları ve genel bulut genelinde kullanılabilir hale getirir. Azure sanal makinelerinde SQL Server (SQL sanal makineler), aynı sunucu ürünleri, geliştirme araçları ve uzmanlık alanı kullanılırken, şirket içi ve bulut üzerinde barındırılan dağıtımların bir birleşimi aracılığıyla benzersiz ve farklı iş ihtiyaçlarını karşılamanıza olanak sağlar Bu ortamlar genelinde.

   ![Bulut SQL Server seçenekleri: IaaS üzerinde SQL sunucusu veya bulutta SaaS veritabanı.](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Diyagramda görüldüğü gibi, her teklif, altyapının üzerinde sahip olduğunuz yönetim düzeyiyle ve maliyet verimliliği derecesine göre ayırdedilir.

Azure 'da, SQL Server iş yüklerinizin barındırılan bir hizmet ([PaaS](https://azure.microsoft.com/overview/what-is-paas/)) veya barındırılan bir altyapı ([IaaS](https://azure.microsoft.com/overview/what-is-iaas/)) olarak çalışmasını sağlayabilirsiniz. PaaS 'de, her dağıtım seçeneğinde birden çok dağıtım seçeneğiniz ve hizmet katmanınız vardır. PaaS veya IaaS arasında seçim yaparken yapmanız gereken önemli soru, veritabanınızı yönetmek, düzeltme eki uygulamak ve yedeklemeleri gerçekleştirmek istiyor musunuz ya da bu işlemleri Azure 'a atamak istiyor musunuz?

Yanıta bağlı olarak aşağıdaki seçenekleriniz vardır:

- [**SQL veritabanları**](sql-database-technical-overview.md): en son kararlı SQL Server özelliklerini kullanmak ve geliştirme ve pazarlama sırasında zaman kısıtlamalarına sahip olmak isteyen modern bulut uygulamaları için en iyi seçenektir. SQL Server en son kararlı kurumsal sürümünü temel alan, tam olarak yönetilen bir SQL veritabanı altyapısı. Bu, *bir hizmet olarak platform (PaaS)* endüstri kategorisine denk gelen Azure bulutu 'nda barındırılan ilişkisel bir hizmet olarak veritabanıdır (DBaaS). SQL veritabanı 'nda, her biri Microsoft tarafından sahip olunan, barındırılan ve korunan, standartlaştırılmış donanım ve yazılım üzerinde oluşturulmuş birden çok dağıtım seçeneği vardır. SQL Server ile, kapsamlı yapılandırma gerektiren yerleşik özellikleri ve işlevleri (Şirket içi veya bir Azure sanal makinesinde) kullanabilirsiniz. SQL Database'i kullandığınızda, kesinti olmadan daha fazla güç için ölçeğinizi artırmaya veya genişletmeye yönelik seçeneklerle kullandıkça ödersiniz. SQL veritabanı, yerleşik yüksek kullanılabilirlik, zeka ve yönetim gibi SQL Server bulunmayan bazı ek özelliklere sahiptir.


  Veritabanları aşağıdaki dağıtım seçeneklerini sunar:
  - Tek bir [***veritabanı***](sql-database-single-database.md) olarak bir veritabanı sunucusu aracılığıyla yönetilen kaynak kümesi olarak. Tek bir veritabanı SQL Server [Kapsanan bir veritabanına](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) benzerdir. Bu seçenek, yeni bulut kaynaklı uygulamaların modern uygulama geliştirmesi için iyileştirilmiştir. [Hiper ölçek](sql-database-service-tier-hyperscale.md) ve [sunucusuz](sql-database-serverless.md) seçenekler kullanılabilir.
  - Bir veritabanı sunucusu aracılığıyla yönetilen ve paylaşılan kaynak kümesi olan veritabanlarının toplanması olan [***elastik havuz***](sql-database-elastic-pool.md). Tek veritabanları, elastik bir havuzun içine ve dışına taşınabilir. Bu seçenek, çok kiracılı SaaS uygulaması modelini kullanarak yeni bulut kaynaklı uygulamaların modern uygulama geliştirmesi için iyileştirilmiştir. Elastik havuzlar, değişken kullanım desenlerine sahip birden çok veritabanının performansını yönetmeye yönelik uygun maliyetli bir çözüm sunar.
  - Tek veritabanı ve elastik havuzların gruplarını yönetmek için kullanılan bir [***veritabanı sunucusu***](sql-database-servers.md). Veritabanı sunucuları, birden çok tek veya havuza alınmış veritabanları, [oturum açmalar](sql-database-manage-logins.md), [güvenlik duvarı kuralları](sql-database-firewall-configure.md), [denetim kuralları](sql-database-auditing.md), [tehdit algılama ilkeleri](sql-database-threat-detection.md)ve [Yük devretme grupları](sql-database-auto-failover-group.md)için merkezi bir yönetim noktası işlevi görür.

- [**SQL yönetilen örnekler**](sql-database-managed-instance.md): en iyi buluta geçiş için idealdir. Yönetilen örnek, kaldırma ve değiştirme özellikli bir dizi paylaşılan kaynak içeren sistem ve kullanıcı veritabanlarının bir koleksiyonudur. En iyi tutarlı SQL Server özelliklerini kullanmak isteyen ve en az değişiklikle buluta geçirilen yeni uygulamalar veya mevcut şirket içi uygulamalar için idealdir. Yönetilen bir örnek, veritabanları ve diğer örnek kapsamlı özellikler için paylaşılan kaynaklar sunan [Microsoft SQL Server veritabanı altyapısının](https://docs.microsoft.com/sql/database-engine/sql-server-database-engine-overview) bir örneğine benzer. Yönetilen örnek, veritabanı değişikliğine sahip olmayan en az ile şirket içi veritabanı geçişini destekler. Bu seçenek, Azure SQL veritabanı 'nın tüm PaaS avantajlarını sağlar, ancak daha önce yalnızca SQL VM 'lerinde bulunan özellikleri ekler. Bu, yerel bir sanal ağ (VNet) ve şirket içi SQL Server uyumluluk %100 ' ını içerir. Yönetilen örnekler, SQL Server 'ı Azure 'a geçirmek için tam SQL Server erişim ve özellik uyumluluğu sağlar.


- [**SQL sanal makineleri**](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md): işletim sistemi düzeyinde erişim gerektiren geçişler ve uygulamalar için idealdir. SQL sanal makineleri, en az değişiklikle veya hiçbir değişikliğe gerek olmadan buluta hızlı geçiş yapılmasını gerektiren mevcut uygulamalar için, kaldırma ve kaydırma sağlar. SQL sanal makineleri, Azure 'a geçiş için SQL Server örneği ve temel alınan işletim sistemi üzerinde tam yönetim denetimi sunar. Şirket içi üretim dışı SQL Server donanımı satın almak istemediğinizde hızlı geliştirme ve test senaryoları. SQL sanal makineleri, sektör kategorisi *hizmet olarak altyapı (IaaS) olarak* çalışır ve Azure bulutundaki tam olarak yönetilen bir sanal makıne (VM) içinde SQL Server çalıştırmanızı sağlar. SQL sanal makineleri, Microsoft tarafından sahip olunan, barındırılan ve korunan standartlaştırılmış donanımlar üzerinde de çalışır. SQL sanal makinelerini kullanırken, zaten bir SQL Server görüntüsüne dahil olan bir SQL Server Lisansı için ödeme yapabilir veya mevcut bir lisansı kolayca kullanabilirsiniz. Ayrıca, gerektiğinde VM 'yi durdurabilir veya sürdürebilirsiniz. Bulutta yüklü ve barındırılan SQL Server, hizmet olarak altyapı (IaaS) olarak da bilinen, Azure üzerinde çalışan Windows Server veya Linux sanal makinelerinde çalışır. SQL sanal makineleri, herhangi bir veritabanı değişikliği olmadan şirket içi SQL Server veritabanlarını ve uygulamaları geçirmek için iyi bir seçenektir. SQL Server tüm yeni sürümleri ve sürümleri IaaS sanal makinesinde yüklenebilir. SQL veritabanları ve SQL yönetilen örneklerinden en önemli fark, SQL Server VM 'Lerin veritabanı altyapısı üzerinde tam denetime izin vereceğidir. Bakım/düzeltme eki uygulamayı ne zaman başlatacağınızı, kurtarma modelini basit veya toplu Günlüksüz olarak değiştirmeyi, gerektiğinde hizmeti duraklatmayı veya başlatmayı seçebilirsiniz ve SQL Server veritabanı altyapısını tam olarak özelleştirebilirsiniz. Bu ek denetimle, sanal makineyi yönetmek için eklenen sorumluluğa yer verilir.

  Mevcut uygulamaları Azure 'a geçirmek veya mevcut şirket içi uygulamaları karma dağıtımlarda buluta genişletmek için iyileştirilmiştir. Ayrıca, SQL Server’ı sanal bir makinede kullanarak geleneksel SQL Server uygulamaları geliştirip test edebilirsiniz. SQL sanal makineler ile adanmış bir SQL Server örneği ve bulut tabanlı VM üzerinde tam yönetici haklarına sahip olursunuz. Bu, bir kuruluşun halihazırda sanal makinelerin bakımını yapmak için kullanılabilir BT kaynaklarına sahip olması durumunda ideal bir seçenektir. Bu özellikler uygulamanızın belirli performans ve kullanılabilirlik gereksinimlerini karşılamak üzere yüksek düzeyde özelleştirilmiş bir sistem oluşturmanıza olanak sağlar.


Aşağıdaki tabloda ek farklılıklar listelenmiştir, ancak ***her iki veritabanı ve yönetilen örnek, birçok veritabanını sağlamak ve yönetmek için genel yönetim maliyetlerini en düşük düzeye düşürmek üzere iyileştirilmiştir.*** Herhangi bir sanal makineyi, işletim sistemini veya veritabanı yazılımını yönetmeniz gerekmediğinden, bu, devam eden yönetim maliyetlerini azaltır. Yükseltme, yüksek kullanılabilirlik veya [yedeklemeleri](sql-database-automated-backups.md) yönetmeniz gerekli değildir. Genellikle, Azure SQL Veritabanı tek bir BT veya geliştirme kaynağı tarafından yönetilen veritabanlarının sayısını önemli ölçüde artırabilir. [Elastik havuzlar](sql-database-elastic-pool.md) , kiracı yalıtımı ve veritabanları arasında kaynakları paylaşarak maliyetleri azaltmak için ölçekleyebilme dahil özelliklerle çok kiracılı uygulama mimarilerini de destekler. [Yönetilen örnek](sql-database-managed-instance.md) , mevcut uygulamaların kolay geçişini sağlayan örnek kapsamlı özellikler için destek sağlar ve veritabanları arasında kaynakları destekler.

| SQL veritabanları | SQL yönetilen örnekleri | SQL sanal makineleri |
| :--- | :--- | :--- |
|, Çoğu şirket içi veritabanı düzeyi özelliği destekler. En yaygın olarak kullanılan SQL Server özellikleri mevcuttur.<br/>% 99,995 kullanılabilirlik garanti edilir.<br/>Yerleşik yedeklemeler, düzeltme eki uygulama.<br/>En son kararlı veritabanı altyapısı sürümü.<br/>Gerekli kaynakları (CPU/depolama) ayrı veritabanlarına atama özelliği.<br/>Yerleşik Gelişmiş zeka ve güvenlik.<br/>Kaynakların çevrimiçi değişikliği (CPU/depolama).| Neredeyse tüm şirket içi örnek düzeyinde ve veritabanı düzeyinde özellikleri destekler. Şirket içi SQL Server yüksek uyumluluk.<br/>% 99,99 kullanılabilirlik garanti edilir.<br/>Yerleşik yedeklemeler, düzeltme eki uygulama.<br/>En son kararlı veritabanı altyapısı sürümü.<br/>SQL Server 'den kolay geçiş.<br/>Azure sanal ağı içindeki özel IP adresi.<br/>Yerleşik Gelişmiş zeka ve güvenlik.<br/>Kaynakların çevrimiçi değişikliği (CPU/depolama).| SQL Server altyapısı üzerinde tam denetiminiz vardır. Tüm şirket içi özellikleri destekler.<br/>% 99,99 ' e kadar kullanılabilirlik.<br/>Şirket içi SQL Server eşleşen sürümüyle tam eşlik.<br/>Sabit, iyi bilinen Veritabanı Altyapısı sürümü.<br/>Şirket içi SQL Server kolayca geçiş.<br/>Azure sanal ağı içindeki özel IP adresi.<br/>SQL Server yerleştirildiği konakta uygulama veya hizmet dağıtmanıza olanak tanır.|
|SQL Server geçiş zor olabilir.<br/>Bazı SQL Server özellikleri kullanılamaz.<br/>Garanti eden tam bakım süresi (ancak neredeyse saydam).<br/>SQL Server sürümle uyumluluk, yalnızca veritabanı uyumluluk düzeyleri kullanılarak sağlanabilir.<br/>Özel IP adresi atanamıyor (güvenlik duvarı kurallarını kullanarak erişimi sınırlayabilirsiniz).|Kullanılabilir olmayan en az sayıda SQL Server özelliği hala vardır.<br/>Garanti eden tam bakım süresi (ancak neredeyse saydam).<br/>SQL Server sürümle uyumluluk, yalnızca veritabanı uyumluluk düzeyleri kullanılarak sağlanabilir.|Yedeklemelerinizi ve düzeltme eklerini yönetmeniz gerekir.<br>Kendi yüksek kullanılabilirlik çözümünüzü uygulamanız gerekir.<br/>Kaynakları değiştirirken kapalı kalma süresi var (CPU/depolama)|
| 100 TB 'a kadar veritabanları. | 8 TB 'a kadar. | 256 TB depolama alanına kadar örnek SQL Server. Örnek gereken sayıda veritabanını destekleyebilir. |
| Şirket içi uygulama, Azure SQL veritabanı 'ndaki verilere erişebilir. | Azure Express Route veya VPN Gateway kullanarak şirket içi ortamınıza [yerel sanal ağ uygulama](sql-database-managed-instance-vnet-configuration.md) ve bağlantı. | SQL sanal makineler ile kısmen bulutta ve kısmen şirket içinde çalışan uygulamalarınız olabilir. Örneğin, şirket içi ağınızı ve Active Directory Etki Alanı'nı [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) üzerinden buluta genişletebilirsiniz. Karma bulut çözümleri hakkında daha fazla bilgi için bkz. [Şirket içi veri çözümlerini buluta genişletme](https://docs.microsoft.com/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud). |




## <a name="business-motivations-for-choosing-databases-managed-instances-or-sql-virtual-machines"></a>Veritabanları, yönetilen örnekler veya SQL sanal makineleri seçmek için iş kesintileri

Farklı veri teklifleri arasında seçim yapmak için kararlarınızı etkileyebilecek birkaç etken vardır:

- [](#cost) Her Iki PaaS ve IaaS seçeneği, temel altyapıyı ve lisanslamayı kapsayan temel fiyatı içerir. Bununla birlikte, IaaS seçeneği ile veritabanınızı yönetmek için ek saat ve kaynakları yatırım yapmanız gerekir. PaaS 'de, fiyata dahil olan bu yönetim özellikleri elde edersiniz. IaaS seçeneği, kaynakları, gerekli olduğunda kaynakları bırakıp yeniden oluşturmadığınız sürece, kaynakları kapatmanıza karşın, bu kaynakları, bu işlemi her zaman çalışır durumdayken kapatmanızı sağlar.
- [Yönetim](#administration) -PaaS seçenekleri, veritabanını yönetmek için yatırım yapmanız gereken süre miktarını azaltır. Ancak, gerçekleştirebileceğiniz veya çalıştırabileceğiniz özel yönetim görevlerinin ve betiklerin aralığını da sınırlandırır. Örneğin, CLR tek veya havuza alınmış veritabanlarıyla desteklenmez, ancak yönetilen bir örnek için desteklenir. Ayrıca PaaS 'deki dağıtım seçenekleri, izleme bayraklarının kullanımını desteklemez.
- [Hizmet düzeyi sözleşmesi](#service-level-agreement-sla) -IaaS ve PaaS, yüksek ENDÜSTRI standardı SLA sağlar. PaaS seçeneği% 99,99 SLA 'sını garanti ederken IaaS, altyapı için% 99,95 SLA 'sını garanti ederken, veritabanlarının kullanılabilirliğini sağlamak için ek mekanizmalar uygulamanız gerekir. VM 'de ek bir SQL Server oluşturup AlwaysOn kullanılabilirlik gruplarını yapılandırabilmeniz için% 99,99 ' de yüksek kullanılabilirlik çözümü uygulayabilirsiniz.
- Azure VM 'de SQL Server [Azure 'a geçme süresi](#market) ortamınızın tam eşleşmesinden itibaren, şirket Içi SUNUCUDAN Azure SQL VM 'sine geçiş, veritabanlarını şirket içi bir sunucudan diğerine taşımadan farklı değildir. Yönetilen örnek, son derece kolay geçişe de izin verebilir; Ancak, yönetilen bir örneğe geçirmeden önce uygulamanız gereken bazı değişiklikler olabilir.

Bu faktörler aşağıdaki bölümlerde daha ayrıntılı olarak ele alınacaktır.

### <a name="cost"></a>Maliyet

İster nakit ihtiyacında olan yeni bir girişim ister sıkı bütçe kısıtlamaları altında işletilen yerleşik bir şirket bünyesindeki bir ekip olun, sınırlı fon genellikle veritabanlarınızın barındırılmasına yönelik kararların verilmesinde en önemli etmendir. Bu bölümde, şu iki ilişkisel veritabanı seçeneğine göre Azure 'da faturalandırma ve lisans temelleri hakkında bilgi edineceksiniz: SQL veritabanı ve SQL sanal makineleri. Ayrıca toplam uygulama maliyetini hesaplama hakkında bilgi edineceksiniz.

#### <a name="billing-and-licensing-basics"></a>Faturalama ve lisanslama ile ilgili temel bilgiler

Şu anda **SQL veritabanı** bir hizmet olarak satılır ve birkaç dağıtım seçeneği ve çeşitli hizmet katmanlarında kullanılabilir ve bu işlemler, her biri, seçtiğiniz hizmet katmanına ve işlem boyutuna bağlı olarak sabit bir fiyata faturalandırılır. . Desteklenen geçerli hizmet katmanları, işlem boyutları ve depolama miktarları hakkında en son bilgiler için bkz. [DTU tabanlı satın alma modeli](sql-database-service-tiers-dtu.md) ve [sanal çekirdek tabanlı satın alma modeli](sql-database-service-tiers-vcore.md).

- SQL veritabanı ile, temel katman için 5 $/aydan itibaren çok çeşitli fiyatlarla ihtiyaçlarınıza uyan bir hizmet katmanını seçebilirsiniz.
- Maliyetleri azaltmak ve kullanım artışlarını karşılamak için, veritabanı örnekleri arasında kaynak paylaşmak üzere [elastik havuzlar](sql-database-elastic-pool.md) oluşturabilirsiniz.
- SQL yönetilen örneği ile kendi lisansınızı da getirebilirsiniz. Kendi lisansını getir hakkında daha fazla bilgi için bkz. [Azure 'da Yazılım Güvencesiyle Lisans Taşınabilirliği](https://azure.microsoft.com/pricing/license-mobility/) veya [Azure hibrit avantajı hesaplayıcıyı](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database) kullanarak **%40**' a nasıl tasarruf etmek gerektiğini öğrenin.

Buna ek olarak, giden Internet trafiği için normal [veri aktarımı ücretleriyle](https://azure.microsoft.com/pricing/details/data-transfers/) faturalandırılırsınız. Hizmet katmanlarını ve işlem boyutlarını, uygulamanızın değişen aktarım hızı gereksinimleriyle eşleşecek şekilde dinamik olarak ayarlayabilirsiniz.

**SQL veritabanları ve SQL yönetilen örneklerle**, veritabanı yazılımı Microsoft tarafından otomatik olarak yapılandırılır, düzeltme eki uygulanmış ve yükseltilir ve bu da yönetim maliyetlerinizi azaltır. Ayrıca, [yerleşik yedekleme](sql-database-automated-backups.md) özellikleri, özellikle çok sayıda veritabanınız olduğunda önemli maliyet tasarrufları sağlamanıza yardımcı olur.

**SQL sanal makineler**ile, platform tarafından sağlanmış SQL Server görüntülerden birini (lisans içerir) kullanabilir veya SQL Server lisansınızı getirebilirsiniz. Desteklenen tüm SQL Server sürümleri (2008R2, 2012, 2014, 2016) (Developer, Express, Web, Standard, Enterprise) kullanılabilir. Ayrıca, görüntülerin Kendi Lisansını Getir sürümleri (KLG) mevcuttur. Azure tarafından sağlanan görüntüler kullanıldığında, işletim maliyeti, VM boyutunun yanı sıra seçtiğiniz SQL Server sürümüne bağlıdır. VM boyutundan veya SQL Server sürümünden bağımsız olarak, VM diskleri için Azure depolama maliyetiyle birlikte, SQL Server ve Windows veya Linux sunucusu için dakikalık lisanslama ücreti ödersiniz. Dakika başına faturalandırma seçeneği, SQL Server'ı ek SQL Server lisansları satın almadan istediğiniz süreyle kullanmanıza olanak sağlar. Azure 'a kendi SQL Server lisansınızı getirdiğiniz takdirde, yalnızca sunucu ve depolama maliyetleri için ücretlendirilirsiniz. Kendi lisansını getir seçeneği hakkında daha fazla bilgi için bkz. [Azure'da Yazılım Güvencesi ile Lisans Mobilitesi](https://azure.microsoft.com/pricing/license-mobility/). Buna ek olarak, giden Internet trafiği için normal [veri aktarımı ücretleriyle](https://azure.microsoft.com/pricing/details/data-transfers/) faturalandırılırsınız.

#### <a name="calculating-the-total-application-cost"></a>Toplam uygulama maliyetini hesaplama

Bulut platformunu kullanmaya başladığınızda, uygulamanızı çalıştırmanın maliyeti yeni geliştirme ve devam eden yönetim maliyetlerinin yanı sıra genel bulut platformu hizmet maliyetlerine dahil olmak üzere maliyeti içerir.

Fiyatlandırma konusunda daha fazla bilgi için aşağıdaki kaynaklara bakın:

- [SQL veritabanı fiyatlandırması](https://azure.microsoft.com/pricing/details/sql-database/)
- [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) ve [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows) için [sanal makine fiyatlandırması](https://azure.microsoft.com/pricing/details/virtual-machines/)
- [Azure Fiyatlandırma Hesaplayıcısı](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Yönetim

Birçok işletme için, bir bulut hizmetine geçiş yapma kararı, maliyetle ilgili olduğu kadar, yönetim karmaşıklığı yükünü gidermekle de ilgilidir. IaaS ve PaaS ile, Microsoft temel altyapıyı yönetir ve olağanüstü durum kurtarma sağlamak, veritabanı yazılımını yapılandırır ve yükseltir, yük dengelemeyi yönetir ve bir bir veri merkezinde sunucu hatası.

- **SQL veritabanları ve SQL yönetilen örneklerle**, veritabanınızı yönetmeye devam edebilirsiniz, ancak artık veritabanı altyapısını, işletim sistemini veya donanımı yönetmeniz gerekmez. Yönetmeye devam edebileceğiniz öğelere örnek olarak veritabanları ve oturum açma bilgileri, dizin ve sorgu ayarları ile denetim ve güvenlik verilebilir. Ayrıca, yüksek kullanılabilirliği başka bir veri merkezine yapılandırmak, en az yapılandırma ve yönetim gerektirir.
- **SQL sanal makineler**ile, işletim sistemi ve SQL Server örneği yapılandırması üzerinde tam denetime sahip olursunuz. Bir VM ile, işletim sisteminin ve veritabanı yazılımının ne zaman güncelleştirileceğine/yükseltileceğine ve virüsten koruma araçları gibi herhangi bir ek yazılımın ne zaman yükleneceğine ilişkin karar size aittir. Düzeltme eki uygulama, yedekleme ve yüksek kullanılabilirliği önemli ölçüde kolaylaştırmak amacıyla bazı otomatik özellikler sağlanır. Ayrıca, VM'nin boyutunu, disk sayısını ve disklerin depolama yapılandırmalarını denetleyebilirsiniz. Azure gerektiğinde bir VM’nin boyutunu değiştirmenize izin verir. Bilgi için bkz. [Azure için Sanal Makine ve Bulut Hizmeti Boyutları](../virtual-machines/windows/sizes.md).

### <a name="service-level-agreement-sla"></a>Hizmet Düzeyi Sözleşmesi (SLA)

Birçok BT departmanı için, bir Hizmet Düzeyi Sözleşmesi'nin çalışma süresi yükümlülüklerinin yerine getirilmesi birincil önceliğe sahiptir. Bu bölümde, her bir veritabanı barındırma seçeneği için geçerli olan SLA'yı ele alacağız.

**SQL veritabanı**için Microsoft,% 99,99 oranında bir kullanılabilirlik SLA 'sı sağlar. En son bilgiler için bkz. [Hizmet Düzeyi Sözleşmesi](https://azure.microsoft.com/support/legal/sla/sql-database/).

Microsoft, **SQL sanal makineler**Için yalnızca sanal makineyi içeren% 99,95 oranında bir kullanılabilirlik SLA 'sı sağlar. Bu SLA, VM'de çalıştırılan işlemleri (SQL Server gibi) kapsamaz ve bir kullanılabilirlik kümesinde en az iki VM örneğini barındırmanızı gerektirir. En son bilgiler için bkz. [VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/). VM 'Ler içindeki veritabanı yüksek kullanılabilirlik (HA) için, [her zaman açık kullanılabilirlik grupları](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)gibi SQL Server desteklenen yüksek kullanılabilirlik seçeneklerinden birini yapılandırmalısınız. Desteklenen bir yüksek kullanılabilirlik seçeneğinin kullanılması ek bir SLA sağlamaz, ancak %99,99’un üzerinde veritabanı kullanılabilirliği elde etmenize imkan tanır.

### <a name="market"></a>Azure 'a geçiş süresi

**SQL veritabanı** (tek veritabanları veya elastik havuzlar), yeni çözümlere yönelik geliştirici üretkenliği ve hızla pazara sunma süresi önemli olduğunda bulutta tasarlanan uygulamalar için doğru çözümdür. Programlama DBA benzeri işlevsellik ile, altta yatan işletim sistemini ve veritabanını yönetmeye yönelik ihtiyacı azalttığından, bulut mimarları ve geliştiricileri için idealdir.

**SQL yönetilen örnek** , mevcut UYGULAMALARıN Azure SQL 'e geçirilmesini büyük ölçüde basitleştirir. böylece, geçirilmiş veritabanı uygulamalarını Azure 'da hızla pazara sunmanıza olanak tanır.

Mevcut veya yeni uygulamalarınızın büyük veritabanları gerektirmesi veya SQL Server ya da Windows/Linux 'taki tüm özelliklere erişimi olması halinde **SQL sanal makineleri** idealdir ve yeni şirket içi donanım alma süresini ve harcamasını önlemek istiyorsanız. Ayrıca, mevcut şirket içi uygulamaları ve veritabanlarını Azure SQL veritabanı yönetilen örneği iyi bir uygun olmadığı durumlarda Azure 'a geçirmek istediğinizde de iyi bir uyum sağlar. Sunu, uygulama ve veri katmanlarını değiştirmenize gerek olmadığından, mevcut çözümünüzü yeniden mimarmaya göre zaman ve bütçe tasarrufu yapabilirsiniz. Bunun yerine, tüm çözümlerinizin Azure'a geçişini sağlamaya ve Azure platformu tarafından gerekli kılınabilen bazı performans iyileştirmelerini gerçekleştirmeye odaklanabilirsiniz. Daha fazla bilgi için bkz. [Azure Virtual Machines'de SQL Server için En İyi Performans Uygulamaları](../virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md).

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="next-steps"></a>Sonraki adımlar

- SQL veritabanı 'nı kullanmaya başlamak için [Ilk Azure SQL veritabanınıza](sql-database-single-database-get-started.md) bakın.
- Bkz. [SQL Veritabanı Fiyatlandırması](https://azure.microsoft.com/pricing/details/sql-database/).
- Azure VM'lerinde SQL Server'ı kullanmaya başlamak için bkz. [Azure'da SQL Server sanal makinesi sağlama](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md).
- Şirket [içi veritabanınız için doğru SQL veritabanını veya SQL yönetilen örnek SKU 'Sunu belirler](/sql/dma/dma-sku-recommend-sql-db/).
