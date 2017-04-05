---
title: "SQL (PaaS) Veritabanı ile VM&quot;lerdeki bulutta bulunan SQL Server (IaaS) karşılaştırması | Microsoft Docs"
description: "Hangi bulut SQL Server seçeneğinin uygulamanıza uygun olduğunu öğrenin: Azure SQL (PaaS) Veritabanı veya Azure Virtual Machines&quot;deki bulutta bulunan SQL Server."
services: sql-database, virtual-machines
keywords: "SQL Server bulut, bulutta SQL Server, PaaS veritabanı, bulut SQL Server, DBaaS"
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: cjgronlund
ms.assetid: 7467f422-b77d-4b60-9cb5-0f1ec17ec565
ms.service: sql-database
ms.custom: overview
ms.workload: data-management
ms.tgt_pltfrm: vm-windows-sql-server
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/01/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 28edde3e70bca833d888cdf9831c1544d8cdd4bb
ms.lasthandoff: 03/31/2017


---
# <a name="choose-a-cloud-sql-server-option-azure-sql-paas-database-or-sql-server-on-azure-vms-iaas"></a>Bir bulut SQL Server seçeneği seçin: Azure SQL (PaaS) Veritabanı ya da Azure VM'lerde SQL Server (IaaS)
Azure, SQL Server iş yüklerini Microsoft Azure’da barındırmaya yönelik iki seçenek içerir:

* [Azure SQL Database](https://azure.microsoft.com/services/sql-database/): Hizmet olarak yazılım (SaaS) uygulama geliştirme işlemi için iyileştirilmiş olan, hizmet olarak platform (PaaS) veritabanı veya hizmet olarak veritabanı (DBaaS) olarak da bilinen, bulut için yerel olan bir SQL database. SQL Server özelliklerinin çoğunluğu ile uyumluluk sağlar. PaaS hakkında daha fazla bilgi için bkz. [PaaS nedir](https://azure.microsoft.com/overview/what-is-paas/).
* [Azure Virtual Machines'de SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/): Azure üzerinde çalışan Windows Server Virtual Machines'deki (VM'ler) bulutta yüklü olan ve barındırılan, aynı zamanda hizmet olarak altyapı (IaaS) olarak da bilinen SQL Server.
  Azure sanal makinelerinde SQL Server, var olan SQL Server uygulamalarını geçirme için optimize edilmiştir. SQL Server’ın tüm sürümleri kullanılabilir durumdadır. Gereken sayıda veritabanını barındırmanıza ve veritabanları arası işlemleri yürütmenize olanak sağlayarak SQL Server ile % 100 uyumluluk sunar. SQL Server ve Windows üzerinde tam denetim sağlar.

Her bir seçeneğin Microsoft veri platformuna nasıl uyduğunu öğrenin ve iş gereksinimleriniz için doğru seçeneği bulma konusunda yardım alın. Sizin için maliyet tasarrufu ve minimum yönetim tüm diğer unsurlardan önce geliyorsa bu makale, hangi yaklaşımın en fazla önem verdiğiniz iş gereksinimleri açısından en iyi sonucu verdiği konusunda karar vermenize yardımcı olabilir.

## <a name="microsofts-data-platform"></a>Microsoft'un veri platformu
Azure ile şirket içi SQL Server veritabanlarının karşılaştırmasına yönelik herhangi bir tartışmada anlaşılması gereken ilk unsur, bunların tümünü kullanabileceğinizdir. Microsoft'un veri platformu, SQL Server teknolojisini kullanır ve fiziksel şirket içi makineler, özel bulut ortamları, üçüncü taraflarca barındırılan özel bulut ortamları ve genel bulutta kullanılabilir kılar. Azure sanal makineleri üzerinde SQL Server, söz konusu ortamlar genelinde aynı sunucu ürünleri, geliştirme araçları ve uzmanlık kümesin kullanırken, şirket içi ve bulut üzerinde barındırılan dağıtımların bir birleşimi yoluyla benzersiz ve çeşitli iş gereksinimlerini karşılamanıza olanak sağlar.

   ![Bulut SQL Server seçenekleri: IaaS üzerinde SQL sunucusu veya bulutta SaaS veritabanı.](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Diyagramda görüldüğü gibi, her bir teklif, altyapı üzerinde sahip olduğunuz yönetim düzeyine (X ekseni üzerinde) ve veritabanı düzeyi birleştirme ve otomasyon işlemleri ile elde edilen maliyet verimliliği (Y ekseni üzerinde) düzeyine göre belirlenebilir.

Bir uygulama tasarlarken, uygulamanın SQL Server kısmını barındırmak için dört temel seçenek mevcuttur:

* Sanallaştırılmamış fiziksel makinelerde SQL Server
* Şirket içi sanallaştırılmış makinelerde SQL Server (özel bulut)
* Azure Sanal Makine'de SQL Server (Microsoft genel bulut)
* Azure SQL Database (Microsoft genel bulut)

Aşağıdaki bölümlerde, Microsoft genel bulutta SQL Server ile ilgili bilgi edineceksiniz: Azure SQL Veritabanı ve Azure VM’lerinde SQL Server. Ayrıca, hangi seçeneğin uygulamanız için en uygun olduğunu belirlemek üzere genel iş teşviklerini inceleyeceksiniz.

## <a name="a-closer-look-at-azure-sql-database-and-sql-server-on-azure-vms"></a>Azure SQL Database ve Azure VM'lerinde SQL Server'a daha ayrıntılı bir bakış
**Azure SQL Database**, Azure bulutunda barındırılan, *Hizmet olarak Yazılım (SaaS)* ve *Hizmet olarak Platform (PaaS)* endüstri kategorileri kapsamında bulunan hizmet olarak ilişkisel veritabanıdır (DBaaS). [SQL veritabanı](sql-database-technical-overview.md), Microsoft'un sahibi olduğu, barındırdığı ve bakımını yaptığı standartlaştırılmış donanım ve yazılım üzerinde geliştirilmiştir. SQL Database ile yerleşik özellikleri ve işlevleri kullanarak doğrudan hizmet üzerinde geliştirme yapabilirsiniz. SQL Database'i kullandığınızda, kesinti olmadan daha fazla güç için ölçeğinizi artırmaya veya genişletmeye yönelik seçeneklerle kullandıkça ödersiniz.

**Azure Virtual Machines'de (VM'ler) SQL Server**, *Hizmet olarak Altyapı (IaaS)* endüstri kategorisine girer ve buluttaki bir sanal makine içinde SQL Server'ı çalıştırmanıza olanak sağlar. SQL Database'e benzer şekilde,bu da Microsoft'un sahibi olduğu, barındırdığı ve bakımını yaptığı standartlaştırılmış donanım üzerinde geliştirilmiştir. Bir VM’de SQL Server kullanırken SQL Server görüntüsüne zaten dahil edilmiş bir SQL Server lisansı için kullandıkça ödeyebilir veya var olan bir lisansı kolayca kullanabilirsiniz. Ayrıca, gerektiğinde VM’nin ölçeğini kolayca artırabilir/azaltabilir ve VM’yi duraklatabilir/sürdürebilirsiniz.

Genellikle, bu iki SQL seçeneği farklı amaçlar için en iyi hale getirilmiştir:

* **Azure SQL Veritabanı**, çok sayıda veritabanının hazırlanması ve yönetilmesi için maliyetleri minimum düzeye indirmek üzere en iyi hale getirilmiştir. Herhangi bir sanal makineyi, işletim sistemini veya veritabanı yazılımını yönetmeniz gerekmediğinden, bu, devam eden yönetim maliyetlerini azaltır. Yükseltme, yüksek kullanılabilirlik veya [yedeklemeleri](sql-database-automated-backups.md) yönetmeniz gerekli değildir. Genellikle, Azure SQL Database tek bir BT veya geliştirme kaynağı tarafından yönetilen veritabanlarının sayısını önemli ölçüde artırabilir.
* **Azure VM’ler üzerinde çalışan SQL Server**, var olan uygulamaları Azure’a geçirmek veya var olan şirket içi uygulamaları karma dağıtımlarda buluta genişletmek için en iyi duruma getirilmiştir. Ayrıca, SQL Server’ı sanal bir makinede kullanarak geleneksel SQL Server uygulamaları geliştirip test edebilirsiniz. Azure VM'lerinde SQL Server ile, adanmış bir SQL Server örneği ve bulut tabanlı bir VM üzerinde tam yönetici haklarına sahip olursunuz. Bu, bir kuruluşun halihazırda sanal makinelerin bakımını yapmak için kullanılabilir BT kaynaklarına sahip olması durumunda ideal bir seçenektir. Bu özellikler uygulamanızın belirli performans ve kullanılabilirlik gereksinimlerini karşılamak üzere yüksek düzeyde özelleştirilmiş bir sistem oluşturmanıza olanak sağlar.

Aşağıdaki tabloda, SQL Database ve Azure VM'lerinde SQL Server'ın temel özellikleri özetlenmektedir:

| **En iyi kullanım alanı:** | **Azure SQL Veritabanı** | **Azure Sanal Makine'de SQL Server** |
| --- | --- | --- |
|  |Geliştirme ve pazarlama alanında zaman kısıtlamaları olan yeni bulut tasarımlı uygulamalar. |Minimum değişiklikle buluta hızlı geçiş gerektiren var olan uygulamalar. Şirket içi üretim dışı SQL Server donanımı satın almak istemediğinizde hızlı geliştirme ve test senaryoları. |
|  | Veritabanı için yerleşik yüksek kullanılabilirlik, olağanüstü durum kurtarma ve yükseltme mekanizmalarına gereksinim duyan ekipler. |SQL Server için yüksek kullanılabilirlik, olağanüstü durum kurtarma ve düzeltme eki uygulamayı yapılandırıp yönetebilen ekipler. Sağlanan bazı otomatik özellikler bunu önemli ölçüde basitleştirir. | |
|  | Altta yatan işletim sistemi ve yapılandırma ayarlarını yönetmek istemeyen ekipler. |Tam yönetici haklarına sahip özelleştirilmiş bir ortama ihtiyaç duyuyorsanız. | |
|  | 1 TB’ye kadar olan veritabanları veya ölçek genişletme düzeni kullanılarak [yatay ya da dikey yönde bölümlenebilen](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling) daha büyük veritabanları. |64 TB’ye varan depolama alanına sahip SQL Server örnekleri. Örnek gereken sayıda veritabanını destekleyebilir. | |
|  | [Hizmet olarak Yazılım (SaaS) uygulamaları oluşturma](sql-database-design-patterns-multi-tenancy-saas-applications.md). |Kuruluş uygulamalarını ve karma uygulamaları geçirme ve derleme. | |
|  | | |
| **Kaynaklar:** |Temel alınan altyapının yapılandırma ve yönetimi için BT kaynakları kullanmak istemiyorsunuz, ancak uygulama katmanına odaklanmak istiyorsunuz. |Yapılandırma ve yönetim için bazı BT kaynaklarına sahipsiniz. Sağlanan bazı otomatik özellikler bunu önemli ölçüde basitleştirir. |
| **Toplam sahip olma maliyeti:** |Donanım maliyetlerini ortadan kaldırır ve yönetim maliyetlerini azaltır. |Donanım maliyetlerini ortadan kaldırır. |
| **İş sürekliliği:** |Azure SQL Database, yerleşik hata toleransı altyapı özelliklerine ek olarak, iş sürekliliğini artırmak üzere [otomatik yedeklemeler](sql-database-automated-backups.md), [Belirli Bir Noktaya Geri Yükleme](sql-database-recovery-using-backups.md#point-in-time-restore), [Coğrafi Geri Yükleme](sql-database-recovery-using-backups.md#geo-restore) ve [Etkin Coğrafi Çoğaltma](sql-database-geo-replication-overview.md) gibi özellikler içerir. Daha fazla bilgi için bkz. [SQL Database iş sürekliliğine genel bakış](sql-database-business-continuity.md). |Azure VM’lerde SQL Server, veritabanınızın belirli gereksinimleri için bir yüksek kullanılabilirlik ve olağanüstü durum kurtarma çözümü ayarlamanıza olanak sağlar. Böylece, uygulamanız için en iyi hale getirilmiş bir sisteme sahip olabilirsiniz. Yük devretme işlemlerini ihtiyaç duyulduğunda kendi kendinize test edebilir ve çalıştırabilirsiniz. Daha fazla bilgi için bkz. [Azure Virtual Machines'de SQL Server için Yüksek Kullanılabilirlik ve Olağanüstü Durum Kurtarma](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md). |
| **Karma bulut:** |Şirket içi uygulamanız, Azure SQL Database'deki verilere erişebilir. |Azure VM'lerinde SQL Server ile kısmen bulutta ve kısmen şirket içinde çalıştırılan uygulamalara sahip olabilirsiniz. Örneğin, şirket içi ağınızı ve Active Directory Etki Alanı'nı [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) üzerinden buluta genişletebilirsiniz. Ek olarak, [Azure'da SQL Server Veri Dosyaları](http://msdn.microsoft.com/library/dn385720.aspx)'nı kullanarak şirket içi veri dosyalarını Azure Storage'da depolayabilirsiniz. Daha fazla bilgi için bkz. [SQL Server 2014 Karma Bulutu'na giriş](http://msdn.microsoft.com/library/dn606154.aspx). |
|  | Verileri çoğaltmak için abone olarak [SQL Server işlem çoğaltmayı](https://msdn.microsoft.com/library/mt589530.aspx) destekler. |Verileri çoğaltmak için [SQL Server işlem çoğaltma](https://msdn.microsoft.com/library/mt589530.aspx), [AlwaysOn Kullanılabilirlik Grupları](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md), Integration Services ve Günlük aktarmayı tam destekler. Ayrıca, geleneksel SQL Server yedeklemeleri tam olarak desteklenir | |
|  | | |

## <a name="business-motivations-for-choosing-azure-sql-database-or-sql-server-on-azure-vms"></a>Azure VM'lerinde Azure SQL Database'in veya SQL Server'ın seçilmesine yönelik iş faydaları
### <a name="cost"></a>Maliyet
İster nakit ihtiyacında olan yeni bir girişim ister sıkı bütçe kısıtlamaları altında işletilen yerleşik bir şirket bünyesindeki bir ekip olun, sınırlı fon genellikle veritabanlarınızın barındırılmasına yönelik kararların verilmesinde en önemli etmendir. Bu bölümde, Azure'da faturalama ve lisanslama ile ilgili temel bilgileri şu iki ilişkisel veritabanı seçeneğine istinaden öğreneceksiniz: SQL Veritabanı ve Azure VM’lerde SQL Server. Ayrıca toplam uygulama maliyetini hesaplama hakkında bilgi edineceksiniz.

#### <a name="billing-and-licensing-basics"></a>Faturalama ve lisanslama ile ilgili temel bilgiler
**SQL Veritabanı**, müşterilere bir lisans ile değil hizmet olarak satılır.  [Azure VM’lerde SQL Server](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) ise dakika başına ödediğiniz bir lisans ile birlikte satılır. Mevcut bir lisansınız varsa onu da kullanabilirsiniz.  

Şu anda **SQL Database** birkaç hizmet katmanında sunulmaktadır ve bu katmanların her biri, seçtiğiniz hizmet katmanında ve performans düzeyi temelinde sabit bir ücretle saatlik olarak faturalandırılır. Buna ek olarak, giden Internet trafiği için normal [veri aktarımı ücretleriyle](https://azure.microsoft.com/pricing/details/data-transfers/) faturalandırılırsınız. Temel, Standart ve Premium hizmet katmanları, uygulamanızın en üst sıradaki gereksinimlerini karşılamak üzere birden çok performans düzeyine sahip tahmin edilebilir bir performans sağlamak üzere tasarlanmıştır. Uygulamanızın değişken verimlilik ihtiyaçlarını karşılamak üzere, hizmet katmanları ve performans düzeyleri arasında geçiş yapabilirsiniz. Veritabanınızın işlem hacmi yüksekse ve çok sayıda eşzamanlı kullanıcıyı desteklemesi gerekiyorsa Premium hizmet katmanını öneririz. Desteklenen geçerli hizmet katmanlarına yönelik en son bilgiler için, bkz. [Azure SQL Database Hizmet Katmanları](sql-database-service-tiers.md). Veritabanı örnekleri arasında performans kaynaklarını paylaşmak için [elastik havuzlar](sql-database-elastic-pool.md) da oluşturabilirsiniz.

**SQL Database** ile, veritabanı yazılımına Microsoft tarafından otomatik olarak yapılandırma, düzeltme eki ve yükseltme uygulanır; bu da yönetim maliyetlerinizi azaltır. Ayrıca, [yerleşik yedekleme](sql-database-automated-backups.md) özellikleri, özellikle çok sayıda veritabanınız olduğunda önemli maliyet tasarrufları sağlamanıza yardımcı olur.

**Azure VM’lerde SQL Server** ile platform tarafından sağlanan SQL Server görüntülerinin herhangi birini (lisans içerir) kullanabilir veya kendi SQL Server lisansınızı getirebilirsiniz. Desteklenen tüm SQL Server sürümleri (2008R2, 2012, 2014, 2016) (Developer, Express, Web, Standard, Enterprise) kullanılabilir. Ayrıca, görüntülerin Kendi Lisansını Getir sürümleri (KLG) mevcuttur. Azure tarafından sağlanan görüntüler kullanıldığında, işletim maliyeti, VM boyutunun yanı sıra seçtiğiniz SQL Server sürümüne bağlıdır. VM boyutundan veya SQL Server sürümünden bağımsız olarak, VM diskleri için Azure Storage maliyetinin yanı sıra, SQL Server ve Windows Server için dakika başına lisanslama ücreti ödersiniz. Dakika başına faturalandırma seçeneği, SQL Server'ı ek SQL Server lisansları satın almadan istediğiniz süreyle kullanmanıza olanak sağlar. Azure'a kendi SQL Server lisansınızı getirirseniz yalnızca Windows Server ve depolama maliyetleri için sizden ücret tahsil edilir. Kendi lisansını getir seçeneği hakkında daha fazla bilgi için bkz. [Azure'da Yazılım Güvencesi ile Lisans Mobilitesi](https://azure.microsoft.com/pricing/license-mobility/).

#### <a name="calculating-the-total-application-cost"></a>Toplam uygulama maliyetini hesaplama
Bir bulut platformunu kullanmaya başladığınızda, uygulamanızı çalıştırmanın maliyeti geliştirme ve yönetim maliyetlerinin yanı sıra genel bulut platformu hizmet maliyetlerini içerir.

SQL Database ve Azure VM'lerinde SQL Server çalıştıran uygulamanız için ayrıntılı maliyeti hesaplaması şu şekildedir:

**Azure SQL Veritabanı kullanıldığında:**

*Toplam uygulama maliyeti = Yüksek oranda azaltılmış yönetim maliyetleri + yazılım geliştirme maliyetleri + SQL Veritabanı hizmet maliyetleri*

**Azure VM'lerinde SQL Server kullanıldığında:**

*Toplam uygulama maliyeti = Yüksek oranda azaltılmış yazılım geliştirme/yönetim maliyetleri + SQL Server ve Windows Server lisanslama maliyetleri + Azure Depolama maliyetleri*

Fiyatlandırma konusunda daha fazla bilgi için aşağıdaki kaynaklara bakın:

* [SQL Veritabanı Fiyatlandırması](https://azure.microsoft.com/pricing/details/sql-database/)
* [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) ve [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows) için [Virtual Machine Fiyatlandırması](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Azure Fiyatlandırma Hesaplayıcısı](https://azure.microsoft.com/pricing/calculator/)

> [!NOTE]
> SQL Server'daki özelliklerin küçük bir kısmı, SQL Database için geçerli veya kullanılabilir değildir. Daha fazla bilgi için bkz. [SQL Database Özellikleri](sql-database-features.md) ve [SQL Database Transact-SQL bilgisi](sql-database-transact-sql-information.md). Var olan bir SQL Server çözümünü buluta taşıyorsanız bkz. [Bir SQL Server veritabanının Azure SQL Database'e geçişini sağlama](sql-database-cloud-migrate.md). Var olan bir şirket içi SQL Server uygulamasının SQL Database'e geçişini sağladığınızda, bulut hizmetlerinin sunduğu özelliklerden faydalanmak üzere uygulamayı güncelleştirme seçeneğini değerlendirin. Örneğin, maliyet avantajlarını artırmak için uygulama katmanınızı barındırmak üzere [Azure Web App Service](https://azure.microsoft.com/services/app-service/web/)'i veya [Azure Cloud Services](https://azure.microsoft.com/services/cloud-services/)'i kullanma fikrini değerlendirebilirsiniz.
> 
> 

### <a name="administration"></a>Yönetim
Birçok işletme için, bir bulut hizmetine geçiş yapma kararı, maliyetle ilgili olduğu kadar, yönetim karmaşıklığı yükünü gidermekle de ilgilidir. **SQL Veritabanı** ile Microsoft temel alınan donanımı yönetir. Microsoft tüm verileri yüksek kullanılabilirlik sağlamak üzere otomatik olarak çoğaltır, veritabanı yazılımını yapılandırır ve yükseltir, yük dengelemeyi yönetir ve bir sunucu hatası olması durumunda saydam yük devretme işlemi gerçekleştirir. Veritabanınızı yönetmeye devam edebilirsiniz, ancak artık veritabanı altyapısını, sunucu işletim sistemini veya donanımı yönetmeniz gerekmez.  Yönetmeye devam edebileceğiniz öğelere örnek olarak veritabanları ve oturum açma bilgileri, dizin ve sorgu ayarları ile denetim ve güvenlik verilebilir.

**Azure VM’lerde SQL Server** ile işletim sistemi ve SQL Server örneği yapılandırması üzerinde tam denetime sahip olursunuz. Bir VM ile, işletim sisteminin ve veritabanı yazılımının ne zaman güncelleştirileceğine/yükseltileceğine ve virüsten koruma araçları gibi herhangi bir ek yazılımın ne zaman yükleneceğine ilişkin karar size aittir. Düzeltme eki uygulama, yedekleme ve yüksek kullanılabilirliği önemli ölçüde kolaylaştırmak amacıyla bazı otomatik özellikler sağlanır. Ayrıca, VM'nin boyutunu, disk sayısını ve disklerin depolama yapılandırmalarını denetleyebilirsiniz. Azure gerektiğinde bir VM’nin boyutunu değiştirmenize izin verir. Bilgi için bkz. [Azure için Sanal Makine ve Bulut Hizmeti Boyutları](../virtual-machines/windows/sizes.md). 

### <a name="service-level-agreement-sla"></a>Hizmet Düzeyi Sözleşmesi (SLA)
Birçok BT departmanı için, bir Hizmet Düzeyi Sözleşmesi'nin çalışma süresi yükümlülüklerinin yerine getirilmesi birincil önceliğe sahiptir. Bu bölümde, her bir veritabanı barındırma seçeneği için geçerli olan SLA'yı ele alacağız.

**SQL Database** Temel, Standart ve Premium hizmet katmanları için Microsoft, %99,99 oranında kullanılabilirlik SLA'sı sağlar. En son bilgiler için bkz. [Hizmet Düzeyi Sözleşmesi](https://azure.microsoft.com/support/legal/sla/sql-database/). SQL Database hizmet katmanlarına ve desteklenen iş sürekliliği planlarına yönelik en son bilgiler için bkz. [Hizmet Katmanları](sql-database-service-tiers.md).

**Azure VM'lerinde çalıştırılan SQL Server** için Microsoft, yalnızca Sanal Makine'yi kapsayan %99,95 oranında bir kullanılabilirlik SLA'sı sağlar. Bu SLA, VM'de çalıştırılan işlemleri (SQL Server gibi) kapsamaz ve bir kullanılabilirlik kümesinde en az iki VM örneğini barındırmanızı gerektirir. En son bilgiler için bkz. [VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/). VM'lerde veritabanı yüksek kullanılabilirliği (HA) için, [AlwaysOn Kullanılabilirlik Grupları](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx) gibi, SQL Server'da desteklenen yüksek kullanılabilirlik seçeneklerinden birini gibi yapılandırmanız gerekir. Desteklenen bir yüksek kullanılabilirlik seçeneğinin kullanılması ek bir SLA sağlamaz, ancak %99,99’un üzerinde veritabanı kullanılabilirliği elde etmenize imkan tanır.

### <a name="market"></a>Pazarlama süresi
**SQL Database**, geliştirici üretkenliği ve hızlı pazarlama süresi kritik önem taşıdığında bulut tasarımlı uygulamalar için doğru çözümdür. Programlama DBA benzeri işlevsellik ile, altta yatan işletim sistemini ve veritabanını yönetmeye yönelik ihtiyacı azalttığından, bulut mimarları ve geliştiricileri için idealdir. Örneğin, [REST API](http://msdn.microsoft.com/library/azure/dn505719.aspx) ve [PowerShell Cmdlet'lerini](http://msdn.microsoft.com/library/mt740629.aspx) binlerce veritabanının yönetimsel işlemlerini otomatik hale getirmek ve yönetmek için kullanabilirsiniz. [Elastik havuzlar](sql-database-elastic-pool.md), uygulama katmanına odaklanmanıza ve çözümünüzü pazara daha hızlı bir şekilde sunmanıza olanak sağlar.

**Azure VM’lerde çalışan SQL Server**, var olan veya yeni uygulamalarınızın büyük veritabanları, birbiriyle ilişkili veritabanları veya SQL Server ya da Windows’un tüm özelliklerine erişim gerektirmesi durumunda idealdir. Bu aynı zamanda, var olan şirket içi uygulamaların ve veritabanlarının oldukları gibi Azure'a geçişini sağlamak istediğinizde iyi bir tercihtir. Sunumu, uygulamayı ve veri katmanlarını değiştirmeniz gerekmediği için, var olan çözümünüzü yeniden yapılandırma konusunda zamandan ve bütçeden tasarruf sağlarsınız. Bunun yerine, tüm çözümlerinizin Azure'a geçişini sağlamaya ve Azure platformu tarafından gerekli kılınabilen bazı performans iyileştirmelerini gerçekleştirmeye odaklanabilirsiniz. Daha fazla bilgi için bkz. [Azure Virtual Machines'de SQL Server için En İyi Performans Uygulamaları](../virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md).

## <a name="summary"></a>Özet
Bu makalede, SQL Database ve Azure Virtual Machines'de (VM'ler) SQL Server işlenmiş ve kararınızı etkileyebilecek genel iş teşvikleri ele alınmıştır. Aşağıda, dikkate almanız için önerilerin bir özeti sağlanmıştır:

**Azure SQL Database**'i aşağıdaki koşullar geçerli olduğunda tercih edin:

* Bulut hizmetlerinin sağladığı maliyet tasarruflarından ve performans iyileştirmesinden yararlanmak için yeni bulut tabanlı uygulamalar oluşturuyorsunuz. Bu yaklaşım, tam olarak yönetilen bir bulut hizmetinin avantajlarını sağlar, daha kısa bir ilk pazarlama süresi elde edilmesine yardımcı olur ve uzun vadeli maliyet iyileştirmesi sağlayabilir.
* Microsoft'un veritabanlarınız üzerinde genel yönetim işlemlerini gerçekleştirmesini ve veritabanları için daha güçlü kullanılabilirlik SLA'larını gerekli kılmasını istiyorsanız.

**Azure VM'lerinde SQL Server**'ı aşağıdaki koşullar geçerli olduğunda tercih edin:

* Buluta geçirmek veya genişletmek istediğiniz şirket içi uygulamalarınız var ya da 1 TB’den büyük kuruluş uygulamaları oluşturmak istiyorsunuz. Bu yaklaşım %100 SQL uyumluluğu, büyük veritabanı kapasitesi, SQL Server ve Windows üzerinde tam denetim ve şirket içi ile güvenli tünel avantajlarını sağlar. Bu yaklaşım var olan uygulamaların geliştirme ve değişiklik maliyetlerini azaltır.
* BT kaynaklarınız var ve sonuçta kendi düzeltme eki uygulama, yedekleme ve veritabanı yüksek kullanılabilirlik özelliklerinize sahip olabilirsiniz. Otomatik özelliklerin bazıları bu işlemleri önemli ölçüde basitleştirebilir. 

## <a name="next-steps"></a>Sonraki adımlar
* SQL Veritabanı’nı kullanmaya başlamak için bkz. [İlk Azure SQL Veritabanınız](sql-database-get-started.md).
* Bkz. [SQL Veritabanı Fiyatlandırması](https://azure.microsoft.com/pricing/details/sql-database/).
* Azure VM'lerinde SQL Server'ı kullanmaya başlamak için bkz. [Azure'da SQL Server sanal makinesi sağlama](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md).


