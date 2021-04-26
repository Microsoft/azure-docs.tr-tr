---
title: Azure SQL nedir?
description: "Azure SQL Services ailesi Hizmetleri içindeki farklı seçenekler hakkında bilgi edinin: Azure SQL veritabanı, Azure SQL yönetilen örneği ve Azure VM 'de SQL Server."
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: overview
keywords: SQL Server bulut, bulutta SQL Server, PaaS veritabanı, bulut SQL Server, DBaaS, IaaS
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 07/27/2020
ms.openlocfilehash: 394b3390386c60e2a64f52dd944dfcdb0d33951e
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727033"
---
# <a name="what-is-azure-sql"></a>Azure SQL nedir? 
[!INCLUDE[appliesto-asf](includes/appliesto-asf.md)]

Azure SQL, Azure bulutundaki SQL Server veritabanı altyapısını kullanan, yönetilen, güvenli ve akıllı ürünlerin bir ailesidir.

- **Azure SQL veritabanı**: sunucusuz işlem içeren, akıllı, yönetilen bir veritabanı hizmetinde modern bulut uygulamalarını destekler. 
- **Azure SQL yönetilen örneği**: SQL Server veritabanı altyapısı ile neredeyse %100 Özellik eşliği sayesinde mevcut SQL Server uygulamalarınızı, bir hizmet olarak, akıllı tam yönetilen bir örnek olarak ölçeklendirin. Buluta birçok geçiş için en iyi seçenektir.
- **Azure VM 'lerinde SQL Server**: SQL Server iş yüklerinizi kolayca kaldırın ve %100 uyumluluk ve işletim sistemi düzeyinde erişim SQL Server koruyun. 
 
Azure SQL tanıdık SQL Server altyapısından oluşturulmuştur. böylece uygulamaları kolayca geçirebilir ve öğrenolduğunuz araçları, dilleri ve kaynakları kullanmaya devam edebilirsiniz. Becerileriniz ve deneyiminiz buluta aktarılmalıdır, bu sayede zaten sahip olduğunuz değişikliklerle daha da fazlasını yapabilirsiniz. 

Her bir ürünün, iş gereksinimleriniz için doğru seçeneği karşılamak üzere Microsoft 'un Azure SQL veri platformuna nasıl uyduğunu öğrenin. Maliyet tasarruflarının veya en düşük yönetimin önceliklendirmenize bakılmaksızın, bu makale, en çok ilgilendiğiniz iş gereksinimlerine göre hangi yaklaşımın elde eteceğine karar vermenize yardımcı olabilir.

Azure SQL 'e yeni başladıysanız, derinlemesine Azure SQL [video serimizin](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner) *Azure SQL* videosunu inceleyin:
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/What-is-Azure-SQL-3-of-61/player]

> [!TIP]
> Azure SQL nasıl daha iyi hale getirebiliriz? [Ankete katılın](https://microsoft.qualtrics.com/jfe/form/SV_ePOznHhP4gDKfGu?channel=456).

## <a name="overview"></a>Genel Bakış

Bugünün veri odaklı dünyasında, dijital dönüştürmeyi giderek gittikçe çok büyük miktarlarda veriyi yönetme ve potansiyel olarak kullanım imkanına bağlıdır. Ancak bugünün veri türleri, şirket içinde, bulutta veya ağın kenarında barındırılan verilerle giderek daha fazla karmaşıktır. Akıllı ve modern uygulamalar oluşturan geliştiriciler, kendi deneyimlerini etkileyebilecek sınırlamalara göre kısıtlanmış olarak bulunabilir. Uyumsuz platformlardan kaynaklanan sınırlamalar, yetersiz veri güvenliği, yetersiz kaynak ve fiyat performansı engelleri, uygulama modernleştirme ve geliştirmeyi ortadan kaldırabilecek karmaşıklıklar oluşturur. 

Azure ile şirket içi SQL Server veritabanlarının karşılaştırmasına yönelik herhangi bir tartışmada anlaşılması gereken ilk unsur, bunların tümünü kullanabileceğinizdir. Microsoft 'un veri platformu SQL Server teknolojiden yararlanır ve fiziksel şirket içi makineler, özel bulut ortamları, üçüncü taraf barındırılan özel bulut ortamları ve genel bulut genelinde kullanılabilir hale getirir. 


### <a name="fully-managed-and-always-up-to-date"></a>Tam olarak yönetilen ve her zaman güncel 

Veritabanlarınızı düzeltme, güncelleştirme ve yedekleme için daha fazla zaman sürmek ve daha az zaman harcaın. Azure, en son güncelleştirmeleri ve düzeltme eklerini otomatik olarak uygulayan tek bulut bulutudur. böylece, veritabanlarının her zaman güncel olmasını sağlar ve destek sonu sorunlarını ortadan kaldırır. Performans ayarlama, yüksek kullanılabilirlik, olağanüstü durum kurtarma ve yedeklemeler gibi karmaşık görevler de otomatik hale getirilebilir ve bu da uygulamalara odaklanmaya olanak sağlar.  

### <a name="protect-your-data-with-built-in-intelligent-security"></a>Yerleşik akıllı güvenlik ile verilerinizi koruyun 

Azure, verilerinizi tehditler açısından sürekli izler. Azure SQL ile şunları yapabilirsiniz:

- Akıllı [Gelişmiş tehdit algılama](../security/fundamentals/threat-detection.md#threat-protection-features-other-azure-services) ve proaktif güvenlik açığı değerlendirme uyarıları sayesinde olası tehditleri gerçek zamanlı olarak düzeltin. 
- T-SQL, kimlik doğrulama, ağ ve anahtar yönetimi dahil [yerleşik güvenlik denetimleriyle](https://azure.microsoft.com/overview/security/) sektör lideri, çok katmanlı koruma alın. 
- Herhangi bir bulut veritabanı hizmetinin en kapsamlı [Uyumluluk](https://azure.microsoft.com/overview/trusted-cloud/compliance/) kapsamından yararlanın. 


### <a name="business-motivations"></a>İş motive

Farklı veri teklifleri arasında seçim yapmak için kararlarınızı etkileyebilecek birkaç etken vardır:

- [Maliyet](#cost): her Iki PaaS ve IaaS seçeneği, temel altyapıyı ve lisanslamayı kapsayan temel fiyatı içerir. Bununla birlikte, IaaS seçeneği ile veritabanınızı yönetmek için ek saat ve kaynakları yatırım yapmanız gerekir. PaaS 'de, fiyata dahil olan bu yönetim özelliklerini edinirsiniz. IaaS, kaynakları, gerekli olduğunda kaynakları bırakıp yeniden oluşturmadığınız sürece her zaman çalışır durumdayken, maliyetleri azaltmak için kullanmadığınız sırada, kaynakları kapatmanızı sağlar.
- [Yönetim](#administration): PaaS seçenekleri veritabanını yönetmek için yatırım yapmanız gereken süre miktarını azaltır. Ancak, gerçekleştirebileceğiniz veya çalıştırabileceğiniz özel yönetim görevlerinin ve betiklerin aralığını da sınırlandırır. Örneğin, CLR SQL veritabanı ile desteklenmez, ancak SQL yönetilen örneği örneği için desteklenir. Ayrıca PaaS 'deki dağıtım seçenekleri, izleme bayraklarının kullanımını desteklemez.
- [Hizmet düzeyi sözleşmesi](#service-level-agreement-sla): IaaS ve PaaS, yüksek endüstri standardı SLA 'sı sağlar. PaaS seçeneği% 99,99 SLA 'sını garanti ederken IaaS, altyapı için% 99,95 SLA 'sını garanti ederken, veritabanlarının kullanılabilirliğini sağlamak için ek mekanizmalar uygulamanız gerekir. % 99,99 SLA 'yı ek bir SQL sanal makinesi oluşturarak ve Always on kullanılabilirlik grubu yüksek kullanılabilirlik çözümü SQL Server uygulayarak sağlayabilirsiniz. 
- [Azure 'a geçiş süresi](#market): Azure vm 'deki SQL Server ortamınızın tam eşleşmesinden itibaren şirket Içi SUNUCUDAN Azure VM 'sine geçiş, veritabanlarını bir şirket içi sunucudan diğerine taşımaktan farklı değildir. SQL yönetilen örneği de kolay geçişe izin verebilir; Ancak, geçişten önce uygulamanız gereken bazı değişiklikler olabilir. 


## <a name="service-comparison"></a>Hizmetlerin karşılaştırması

   ![Cloud SQL Server seçenekleri: IaaS veya bulutta SaaS SQL veritabanı SQL Server.](./media/azure-sql-iaas-vs-paas-what-is-overview/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Diyagramda görüldüğü gibi, her hizmet teklifi, altyapının üzerinde sahip olduğunuz yönetim düzeyiyle ve maliyet verimliliği derecesine göre ayırdedilir.

Azure 'da, SQL Server iş yüklerinizin barındırılan bir hizmet ([PaaS](https://azure.microsoft.com/overview/what-is-paas/)) veya barındırılan bir altyapı ([IaaS](https://azure.microsoft.com/overview/what-is-iaas/)) olarak çalışmasını sağlayabilirsiniz. PaaS 'de, her bir seçenekte birden çok ürün seçeneğiniz ve hizmet katmanları vardır. PaaS veya IaaS arasında seçim yaparken yapmanız gereken önemli soru, veritabanınızı yönetmek, düzeltme eki uygulamak ve yedeklemeleri gerçekleştirmek istiyor musunuz ya da bu işlemleri Azure 'a atamak istiyor musunuz?

### <a name="azure-sql-database"></a>Azure SQL Veritabanı

[Azure SQL veritabanı](database/sql-database-paas-overview.md) , Azure 'da barındırılan, *hizmet olarak platform (PaaS)* endüstri kategorisine denk gelen bir ilişkisel hizmet olarak veritabanıdır (DBaaS). 
- Geliştirme ve pazarlama için en son kararlı SQL Server özelliklerini kullanmak ve zaman kısıtlamalarına sahip olmak isteyen modern bulut uygulamaları için idealdir. 
- SQL Server en son kararlı Enterprise sürümüne bağlı olarak, tam olarak yönetilen bir SQL Server veritabanı altyapısı. SQL veritabanı, Microsoft tarafından sahip olunan, barındırılan ve korunan standartlaştırılmış donanım ve yazılım üzerinde oluşturulmuş iki dağıtım seçeneğine sahiptir. 

SQL Server ile, kapsamlı yapılandırma gerektiren yerleşik özellikleri ve işlevleri (Şirket içi veya bir Azure sanal makinesinde) kullanabilirsiniz. SQL Database'i kullandığınızda, kesinti olmadan daha fazla güç için ölçeğinizi artırmaya veya genişletmeye yönelik seçeneklerle kullandıkça ödersiniz. SQL veritabanı, yerleşik yüksek kullanılabilirlik, zeka ve yönetim gibi SQL Server bulunmayan bazı ek özelliklere sahiptir.


Azure SQL veritabanı aşağıdaki dağıtım seçeneklerini sunar:
  - [Mantıksal BIR SQL Server](database/logical-servers.md)aracılığıyla yönetilen kendi kaynakları kümesine sahip [*tek bir veritabanı*](database/single-database-overview.md) olarak. Tek bir veritabanı SQL Server [Kapsanan bir veritabanına](/sql/relational-databases/databases/contained-databases) benzerdir. Bu seçenek, yeni bulut kaynaklı uygulamaların modern uygulama geliştirmesi için iyileştirilmiştir. [Hiper ölçek](database/service-tier-hyperscale.md) ve [sunucusuz](database/serverless-tier-overview.md) seçenekler kullanılabilir.
  - [Mantıksal BIR SQL Server](database/logical-servers.md)aracılığıyla yönetilen, paylaşılan bir kaynak kümesiyle veritabanlarının toplanması olan [*elastik havuz*](database/elastic-pool-overview.md). Tek veritabanları, elastik bir havuzun içine ve dışına taşınabilir. Bu seçenek, çok kiracılı SaaS uygulaması modelini kullanarak yeni bulut kaynaklı uygulamaların modern uygulama geliştirmesi için iyileştirilmiştir. Elastik havuzlar, değişken kullanım desenlerine sahip birden çok veritabanının performansını yönetmeye yönelik uygun maliyetli bir çözüm sunar.

### <a name="azure-sql-managed-instance"></a>Azure SQL Yönetilen Örnek

[Azure SQL yönetilen örneği](managed-instance/sql-managed-instance-paas-overview.md) , *hizmet olarak platform (PaaS)* endüstri kategorisine denk gelir ve buluta geçiş için en iyi seçenektir. SQL yönetilen örneği, kaldırma ve değiştirme özellikli bir dizi paylaşılan kaynak içeren sistem ve kullanıcı veritabanlarının bir koleksiyonudur.  
- En iyi tutarlı SQL Server özelliklerini kullanmak isteyen ve en az değişiklikle buluta geçirilen yeni uygulamalar veya mevcut şirket içi uygulamalar için idealdir. SQL yönetilen örneğinin bir örneği, veritabanları ve diğer örnek kapsamlı özellikler için paylaşılan kaynaklar sunan [Microsoft SQL Server veritabanı altyapısının](/sql/database-engine/sql-server-database-engine-overview) bir örneğine benzer. 
- SQL yönetilen örneği, veritabanı değişikliğine sahip olmayan en az ile şirket içi veritabanı geçişini destekler. Bu seçenek, Azure SQL veritabanı 'nın tüm PaaS avantajlarını sağlar, ancak daha önce yalnızca SQL Server VM 'lerde kullanılabilen özellikleri ekler. Bu, yerel bir sanal ağ ve şirket içi SQL Server ile %100 uyumluluğun yakınında bulunur. SQL yönetilen örneği örnekleri, SQL Server 'ı Azure 'a geçirmek için tam SQL Server erişim ve özellik uyumluluğu sağlar.

### <a name="sql-server-on-azure-vm"></a>Azure Sanal Makinesi üzerinde SQL Server

[Azure VM SQL Server](virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) , sektör kategorisi *hizmet olarak altyapı (IaaS)* ile sonuçlanır ve Azure 'da tam olarak yönetilen BIR sanal makine (VM) içinde SQL Server çalıştırmanızı sağlar. 
- Bulutta yüklü ve barındırılan SQL Server, hizmet olarak altyapı (IaaS) olarak da bilinen, Azure üzerinde çalışan Windows Server veya Linux sanal makinelerinde çalışır. SQL sanal makineleri, herhangi bir veritabanı değişikliği olmadan şirket içi SQL Server veritabanlarını ve uygulamaları geçirmek için iyi bir seçenektir. SQL Server tüm yeni sürümleri ve sürümleri IaaS sanal makinesinde yüklenebilir. 
- İşletim sistemi düzeyinde erişim gerektiren geçişler ve uygulamalar için idealdir. Azure 'daki SQL sanal makineleri, en az değişiklikle veya hiçbir değişikliğe gerek olmadan buluta hızlı geçiş gerektiren mevcut uygulamalar için yükseltme ve kaydırma. SQL sanal makineleri, Azure 'a geçiş için SQL Server örneği ve temel alınan işletim sistemi üzerinde tam yönetim denetimi sunar. 
- SQL veritabanı ve SQL yönetilen örneğinden en önemli fark, Azure sanal makinelerinde SQL Server, veritabanı altyapısı üzerinde tam denetim sağlar. Bakım/düzeltme eki uygulamayı ne zaman başlatacağınızı, kurtarma modelini basit veya toplu Günlüksüz olarak değiştirmeyi, gerektiğinde hizmeti duraklatmayı veya başlatmayı seçebilirsiniz ve SQL Server veritabanı altyapısını tam olarak özelleştirebilirsiniz. Bu ek denetimle, sanal makineyi yönetmek için eklenen sorumluluğa yer verilir.
- Şirket içi üretim dışı SQL Server donanımı satın almak istemediğinizde hızlı geliştirme ve test senaryoları. SQL sanal makineleri, Microsoft tarafından sahip olunan, barındırılan ve korunan standartlaştırılmış donanımlar üzerinde de çalışır. SQL sanal makinelerini kullanırken, zaten bir SQL Server görüntüsüne dahil olan bir SQL Server Lisansı için ödeme yapabilir veya mevcut bir lisansı kolayca kullanabilirsiniz. Ayrıca, gerektiğinde VM 'yi durdurabilir veya sürdürebilirsiniz. 
- Mevcut uygulamaları Azure 'a geçirmek veya mevcut şirket içi uygulamaları karma dağıtımlarda buluta genişletmek için iyileştirilmiştir. Ayrıca, SQL Server’ı sanal bir makinede kullanarak geleneksel SQL Server uygulamaları geliştirip test edebilirsiniz. SQL sanal makineler ile adanmış bir SQL Server örneği ve bulut tabanlı VM üzerinde tam yönetici haklarına sahip olursunuz. Bu, bir kuruluşun halihazırda sanal makinelerin bakımını yapmak için kullanılabilir BT kaynaklarına sahip olması durumunda ideal bir seçenektir. Bu özellikler uygulamanızın belirli performans ve kullanılabilirlik gereksinimlerini karşılamak üzere yüksek düzeyde özelleştirilmiş bir sistem oluşturmanıza olanak sağlar.


### <a name="comparison-table"></a>Karşılaştırma tablosu

Aşağıdaki tabloda ek farklılıklar listelenmiştir, ancak *hem SQL veritabanı hem de SQL yönetilen örneği, birçok veritabanını sağlamak ve yönetmek için genel yönetim maliyetlerini en düşük düzeye düşürmek üzere iyileştirilmiştir.* Herhangi bir sanal makineyi, işletim sistemini veya veritabanı yazılımını yönetmeniz gerektiğinden devam eden yönetim maliyetleri azalır. Yükseltme, yüksek kullanılabilirlik veya [yedeklemeleri](database/automated-backups-overview.md) yönetmeniz gerekli değildir. 

Genel olarak, SQL veritabanı ve SQL yönetilen örneği, tek bir BT veya geliştirme kaynağıyla yönetilen veritabanlarının sayısını ciddi ölçüde artırabilir. [Elastik havuzlar](database/elastic-pool-overview.md) , kiracı yalıtımı ve veritabanları arasında kaynakları paylaşarak maliyetleri azaltmak için ölçekleyebilme dahil özelliklerle çok kiracılı uygulama mimarilerini de destekler. [SQL yönetilen örneği](managed-instance/sql-managed-instance-paas-overview.md) , mevcut uygulamaların kolayca geçirilmesini sağlayan örnek kapsamlı özellikler için destek sağlar ve kaynakları veritabanları arasında paylaşıyor. Ancak, [Azure VM 'lerinde SQL Server](virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) , öğrendikleri şirket içi ortama benzer bir deneyimle DBAS sağlar. 


| Azure SQL Veritabanı | Azure SQL Yönetilen Örnek | Azure Sanal Makinesi üzerinde SQL Server |
| :--- | :--- | :--- |
|, Çoğu şirket içi veritabanı düzeyi özelliği destekler. En yaygın olarak kullanılan SQL Server özellikleri mevcuttur.<br/>% 99,995 kullanılabilirlik garanti edilir.<br/>Yerleşik yedeklemeler, düzeltme eki uygulama.<br/>En son kararlı veritabanı altyapısı sürümü.<br/>Gerekli kaynakları (CPU/depolama) ayrı veritabanlarına atama özelliği.<br/>Yerleşik Gelişmiş zeka ve güvenlik.<br/>Kaynakların çevrimiçi değişikliği (CPU/depolama).| Neredeyse tüm şirket içi örnek düzeyinde ve veritabanı düzeyinde özellikleri destekler. SQL Server ile yüksek uyumluluk.<br/>% 99,99 kullanılabilirlik garanti edilir.<br/>Yerleşik yedeklemeler, düzeltme eki uygulama.<br/>En son kararlı veritabanı altyapısı sürümü.<br/>SQL Server 'den kolay geçiş.<br/>Azure sanal ağı içindeki özel IP adresi.<br/>Yerleşik Gelişmiş zeka ve güvenlik.<br/>Kaynakların çevrimiçi değişikliği (CPU/depolama).| SQL Server altyapısı üzerinde tam denetiminiz vardır. Tüm şirket içi özellikleri destekler.<br/>% 99,99 ' e kadar kullanılabilirlik.<br/>Şirket içi SQL Server eşleşen sürümüyle tam eşlik.<br/>Sabit, iyi bilinen Veritabanı Altyapısı sürümü.<br/>SQL Server 'den kolay geçiş.<br/>Azure sanal ağı içindeki özel IP adresi.<br/>SQL Server yerleştirildiği konakta uygulama veya hizmet dağıtma olanağınız vardır.|
|SQL Server geçiş zor olabilir.<br/>Bazı SQL Server özellikleri kullanılamaz.<br/>Garanti eden tam bakım süresi (ancak neredeyse saydam).<br/>SQL Server sürümle uyumluluk, yalnızca veritabanı uyumluluk düzeyleri kullanılarak sağlanabilir.<br/>[Azure özel bağlantısı](database/private-endpoint-overview.md)Ile özel IP adresi desteği.|Kullanılabilir olmayan en az sayıda SQL Server özelliği hala vardır.<br/>Garanti eden tam bakım süresi (ancak neredeyse saydam).<br/>SQL Server sürümle uyumluluk, yalnızca veritabanı uyumluluk düzeyleri kullanılarak sağlanabilir.|Yedeklemelerinizi ve düzeltme eklerini yönetmeniz gerekir.<br>Kendi High-Availability çözümünüzü uygulamanız gerekir.<br/>Kaynakları değiştirirken kapalı kalma süresi var (CPU/depolama)|
| 100 TB 'a kadar veritabanları. | 8 TB 'a kadar. | 256 TB depolama alanına kadar örnek SQL Server. Örnek gereken sayıda veritabanını destekleyebilir. |
| Şirket içi uygulama, Azure SQL veritabanı 'ndaki verilere erişebilir. | Azure Express Route veya VPN Gateway kullanarak şirket içi ortamınıza [yerel sanal ağ uygulama](managed-instance/vnet-existing-add-subnet.md) ve bağlantı. | SQL sanal makineler ile kısmen bulutta ve kısmen şirket içinde çalışan uygulamalarınız olabilir. Örneğin, şirket içi ağınızı ve Active Directory Etki Alanı'nı [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) üzerinden buluta genişletebilirsiniz. Karma bulut çözümleri hakkında daha fazla bilgi için bkz. [Şirket içi veri çözümlerini buluta genişletme](/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud). |


## <a name="cost"></a>Maliyet

İster nakit için, ister sıkı bütçe kısıtlamaları altında çalışan kurulu bir şirketteki bir ekip olsun, sınırlı komik, genellikle veritabanlarınızı barındırmaya karar verirken birincil sürücü olur. Bu bölümde, Azure 'da Azure SQL hizmet ailesiyle ilişkili faturalandırma ve lisans temel bilgileri hakkında bilgi edineceksiniz.  Ayrıca toplam uygulama maliyetini hesaplama hakkında bilgi edineceksiniz.

### <a name="billing-and-licensing-basics"></a>Faturalama ve lisanslama ile ilgili temel bilgiler

Şu anda, hem **SQL veritabanı** hem de **SQL yönetilen örneği** bir hizmet olarak satılır ve çeşitli seçeneklerle ve çeşitli hizmet katmanlarında kullanılabilir ve her biri, seçtiğiniz hizmet katmanına ve işlem boyutuna bağlı olarak sabit bir fiyata faturalandırılır. Desteklenen geçerli hizmet katmanları, işlem boyutları ve depolama miktarları hakkında en son bilgiler için bkz. SQL [veritabanı Için DTU tabanlı satın alma modeli](database/service-tiers-dtu.md) ve [hem SQL VERITABANı hem de SQL yönetilen örneği için sanal çekirdek tabanlı satın alma modeli](database/service-tiers-vcore.md).

- SQL veritabanı ile, temel katman için 5 $/aydan itibaren çok çeşitli fiyatlarla ihtiyaçlarınıza uyan bir hizmet katmanını seçebilirsiniz ve maliyetleri azaltmak ve kullanım artışlarını karşılamak için veritabanları arasında kaynak paylaşmak üzere [elastik havuzlar](database/elastic-pool-overview.md) oluşturabilirsiniz.
- SQL yönetilen örneği ile kendi lisansınızı da getirebilirsiniz. Kendi lisansını getir hakkında daha fazla bilgi için bkz. [Azure 'da Yazılım Güvencesiyle Lisans Taşınabilirliği](https://azure.microsoft.com/pricing/license-mobility/) veya [Azure hibrit avantajı hesaplayıcıyı](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database) kullanarak **%40**' a nasıl tasarruf etmek gerektiğini öğrenin.

Buna ek olarak, giden Internet trafiği için normal [veri aktarımı ücretleriyle](https://azure.microsoft.com/pricing/details/data-transfers/) faturalandırılırsınız. Hizmet katmanlarını ve işlem boyutlarını, uygulamanızın değişen aktarım hızı gereksinimleriyle eşleşecek şekilde dinamik olarak ayarlayabilirsiniz.

**SQL veritabanı** ve **SQL yönetilen örneği** ile, veritabanı yazılımı otomatik olarak yapılandırılır, düzeltme eki uygulanmış ve Azure tarafından yükseltilir ve bu da yönetim maliyetlerinizi azaltır. Ayrıca, [yerleşik yedekleme](database/automated-backups-overview.md) özellikleri, özellikle çok sayıda veritabanınız olduğunda önemli maliyet tasarrufları sağlamanıza yardımcı olur.

**Azure VM 'Lerde SQL** ile, platform tarafından sağlanmış SQL Server görüntülerden birini (lisans içerir) kullanabilir veya SQL Server lisansınızı getirebilirsiniz. Desteklenen tüm SQL Server sürümleri (2008R2, 2012, 2014, 2016, 2017, 2019) ve sürümleri (Geliştirici, Express, Web, Standard, Enterprise) kullanılabilir. Ayrıca, görüntülerin Kendi Lisansını Getir sürümleri (KLG) mevcuttur. Azure tarafından sağlanan görüntüler kullanıldığında, işletim maliyeti, VM boyutunun yanı sıra seçtiğiniz SQL Server sürümüne bağlıdır. VM boyutundan veya SQL Server sürümünden bağımsız olarak, VM diskleri için Azure depolama maliyetiyle birlikte, SQL Server ve Windows veya Linux sunucusu için dakikalık lisanslama ücreti ödersiniz. Dakika başına faturalandırma seçeneği, SQL Server'ı ek SQL Server lisansları satın almadan istediğiniz süreyle kullanmanıza olanak sağlar. Azure 'a kendi SQL Server lisansınızı getirdiğiniz takdirde, yalnızca sunucu ve depolama maliyetleri için ücretlendirilirsiniz. Kendi lisansını getir seçeneği hakkında daha fazla bilgi için bkz. [Azure'da Yazılım Güvencesi ile Lisans Mobilitesi](https://azure.microsoft.com/pricing/license-mobility/). Buna ek olarak, giden Internet trafiği için normal [veri aktarımı ücretleriyle](https://azure.microsoft.com/pricing/details/data-transfers/) faturalandırılırsınız.

#### <a name="calculating-the-total-application-cost"></a>Toplam uygulama maliyetini hesaplama

Bulut platformunu kullanmaya başladığınızda, uygulamanızı çalıştırmanın maliyeti yeni geliştirme ve devam eden yönetim maliyetlerinin yanı sıra genel bulut platformu hizmet maliyetlerine dahil olmak üzere maliyeti içerir.

Fiyatlandırma konusunda daha fazla bilgi için aşağıdaki kaynaklara bakın:

- [SQL veritabanı & SQL yönetilen örnek fiyatlandırması](https://azure.microsoft.com/pricing/details/sql-database/)
- [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) ve [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows) için [sanal makine fiyatlandırması](https://azure.microsoft.com/pricing/details/virtual-machines/)
- [Azure Fiyatlandırma Hesaplayıcısı](https://azure.microsoft.com/pricing/calculator/)

## <a name="administration"></a>Yönetim

Birçok işletme için, bir bulut hizmetine geçiş yapma kararı, maliyetle ilgili olduğu kadar, yönetim karmaşıklığı yükünü gidermekle de ilgilidir. IaaS ve PaaS ile Azure temel altyapıyı yönetir ve olağanüstü durum kurtarma sağlamak, veritabanı yazılımını yapılandırır ve yükseltir, yük dengelemeyi yönetir ve bir veri merkezinde sunucu arızası olması durumunda saydam yük devretme işlemi yapar.

- **SQL veritabanı** ve **SQL yönetilen örneği** sayesinde, veritabanınızı yönetmeye devam edebilirsiniz, ancak artık veritabanı altyapısını, işletim sistemini veya donanımı yönetmeniz gerekmez. Yönetmeye devam edebileceğiniz öğelere örnek olarak veritabanları ve oturum açma bilgileri, dizin ve sorgu ayarları ile denetim ve güvenlik verilebilir. Ayrıca, yüksek kullanılabilirliği başka bir veri merkezine yapılandırmak, en az yapılandırma ve yönetim gerektirir.
- **Azure VM 'de SQL** ile, işletim sistemi ve SQL Server örneği yapılandırması üzerinde tam denetime sahip olursunuz. Bir VM ile, işletim sisteminin ve veritabanı yazılımının ne zaman güncelleştirilmesi/yükseltileceğine ve virüsten koruma gibi herhangi bir ek yazılımın ne zaman yükleneceğine karar vermeniz gerekir. Düzeltme eki uygulama, yedekleme ve yüksek kullanılabilirliği önemli ölçüde kolaylaştırmak amacıyla bazı otomatik özellikler sağlanır. Ayrıca, VM'nin boyutunu, disk sayısını ve disklerin depolama yapılandırmalarını denetleyebilirsiniz. Azure gerektiğinde bir VM’nin boyutunu değiştirmenize izin verir. Bilgi için bkz. [Azure için Sanal Makine ve Bulut Hizmeti Boyutları](../virtual-machines/sizes.md).

## <a name="service-level-agreement-sla"></a>Hizmet düzeyi sözleşmesi (SLA)

Birçok BT bölümünde, bir hizmet düzeyi sözleşmesi (SLA) için en güncel yükümlülüklerin toplantısı, en önemli önceliktir. Bu bölümde, her bir veritabanı barındırma seçeneği için geçerli olan SLA'yı ele alacağız.

**Azure SQL veritabanı** ve **Azure SQL yönetilen örneği** için, MICROSOFT% 99,99 kullanılabilirlik SLA 'sı sağlar. En son bilgiler için bkz. [hizmet düzeyi sözleşmesi](https://azure.microsoft.com/support/legal/sla/sql-database/).

Microsoft, **Azure VM 'de SQL** için yalnızca sanal makineyi içeren% 99,95 oranında bir kullanılabilirlik SLA 'sı sağlar. Bu SLA, VM'de çalıştırılan işlemleri (SQL Server gibi) kapsamaz ve bir kullanılabilirlik kümesinde en az iki VM örneğini barındırmanızı gerektirir. En son bilgiler için bkz. [VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/). VM 'Ler içindeki veritabanı yüksek kullanılabilirlik (HA) için, [her zaman açık kullanılabilirlik grupları](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)gibi SQL Server desteklenen yüksek kullanılabilirlik seçeneklerinden birini yapılandırmalısınız. Desteklenen bir yüksek kullanılabilirlik seçeneğinin kullanılması ek bir SLA sağlamaz, ancak %99,99’un üzerinde veritabanı kullanılabilirliği elde etmenize imkan tanır.

## <a name="time-to-move-to-azure"></a><a name="market"></a>Azure 'a geçiş süresi

**Azure SQL veritabanı** , yeni çözümlere yönelik geliştirici üretkenliği ve hızla pazara sunma süresi kritik olduğunda bulutta tasarlanan uygulamalar için doğru çözümdür. Programlama DBA benzeri işlevsellik ile, altta yatan işletim sistemini ve veritabanını yönetmeye yönelik ihtiyacı azalttığından, bulut mimarları ve geliştiricileri için idealdir.

**Azure SQL yönetilen örneği** , mevcut uygulamaların Azure 'a geçişini büyük ölçüde basitleştirir, böylece geçirilmiş veritabanı uygulamalarını Azure 'da hızla pazara sunmanıza olanak tanır.

**Azure VM 'Deki SQL** , mevcut veya yeni uygulamalarınız büyük veritabanları gerektiriyorsa veya SQL Server ya da Windows/Linux 'taki tüm özelliklere erişebiliyorlarsa ve yeni şirket içi donanım alma süresinin ve masrafından kaçınmak istediğinizde idealdir. Ayrıca, mevcut şirket içi uygulamaları ve veritabanlarını SQL veritabanı ya da SQL yönetilen örneği iyi bir uygun olmadığı durumlarda Azure 'a geçirmek istediğinizde de iyi bir uyum sağlar. Sunu, uygulama ve veri katmanlarını değiştirmenize gerek olmadığından, mevcut çözümünüzü yeniden mimarmaya göre zaman ve bütçe tasarrufu yapabilirsiniz. Bunun yerine, tüm çözümlerinizin Azure'a geçişini sağlamaya ve Azure platformu tarafından gerekli kılınabilen bazı performans iyileştirmelerini gerçekleştirmeye odaklanabilirsiniz. Daha fazla bilgi için bkz. [Azure Virtual Machines'de SQL Server için En İyi Performans Uygulamaları](virtual-machines/windows/performance-guidelines-best-practices.md).

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="next-steps"></a>Sonraki adımlar

- SQL Veritabanı’nı kullanmaya başlamak için bkz. [İlk Azure SQL Veritabanınız](database/single-database-create-quickstart.md).
- SQL yönetilen örneği ile çalışmaya başlamak için [Ilk Azure SQL yönetilen örneğinizi](managed-instance/instance-create-quickstart.md) inceleyin. 
- Bkz. [SQL Veritabanı Fiyatlandırması](https://azure.microsoft.com/pricing/details/sql-database/).
- Azure VM'lerinde SQL Server'ı kullanmaya başlamak için bkz. [Azure'da SQL Server sanal makinesi sağlama](virtual-machines/windows/create-sql-vm-portal.md).
- Şirket [içi veritabanınız için doğru SQL veritabanını veya SQL yönetilen örnek SKU 'Sunu belirler](/sql/dma/dma-sku-recommend-sql-db/).
