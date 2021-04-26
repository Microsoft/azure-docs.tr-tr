---
title: Etkin coğrafi çoğaltma
description: Aynı veya farklı veri merkezi bölgelerinde Azure SQL veritabanı 'nda ayrı ayrı veritabanlarının okunabilir ikincil veritabanlarını oluşturmak için etkin coğrafi çoğaltma ' yı kullanın.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 08/27/2020
ms.openlocfilehash: 3a678f6280b5f2d0fd372e75bfbeb6eb2e9b1577
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100634303"
---
# <a name="creating-and-using-active-geo-replication---azure-sql-database"></a>Etkin coğrafi çoğaltma oluşturma ve kullanma-Azure SQL veritabanı
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Etkin coğrafi çoğaltma, aynı veya farklı veri merkezi (bölge) içindeki bir sunucuda ayrı ayrı veritabanlarının okunabilir ikincil veritabanlarını oluşturmanıza olanak sağlayan bir Azure SQL veritabanı özelliğidir.

> [!NOTE]
> Etkin coğrafi çoğaltma, Azure SQL yönetilen örneği tarafından desteklenmiyor. SQL yönetilen örneği örneklerinin coğrafi yük devretmesi için [otomatik yük devretme grupları](auto-failover-group-overview.md)kullanın.

> [!NOTE]
> Etkin coğrafi çoğaltma kullanarak SQL veritabanlarını Azure Almanya 'dan geçirmek için bkz. [etkin coğrafi çoğaltma kullanarak SQL veritabanı geçirme](../../germany/germany-migration-databases.md#migrate-sql-database-using-active-geo-replication).

Etkin coğrafi çoğaltma, uygulamanın bölgesel bir olağanüstü durum veya büyük ölçekli bir kesinti olması durumunda bireysel veritabanlarının hızlı olağanüstü durum kurtarması gerçekleştirmesini sağlayan bir iş sürekliliği çözümü olarak tasarlanmıştır. Coğrafi çoğaltma etkinse, uygulama farklı bir Azure bölgesindeki ikincil bir veritabanına yük devretme başlatabilir. Aynı veya farklı bölgelerde en fazla dört ikincil desteklenir ve ikincil öğeler de salt okuma erişim sorguları için kullanılabilir. Yük devretme, uygulama veya Kullanıcı tarafından el ile başlatılmalıdır. Yük devretmeden sonra yeni birincil, farklı bir bağlantı uç noktasına sahiptir.

> [!NOTE]
> Etkin coğrafi çoğaltma, veritabanı işlem günlüğünü akışa alarak değişiklikleri çoğaltır. DML (INSERT, UPDATE, DELETE) komutları yürüterek değişiklikleri çoğaltan [işlem çoğaltmasıyla](/sql/relational-databases/replication/transactional/transactional-replication)ilgisi yoktur.

Aşağıdaki diyagramda, etkin coğrafi çoğaltma kullanılarak coğrafi olarak yedekli bir bulut uygulamasının tipik bir yapılandırması gösterilmektedir.

![etkin coğrafi çoğaltma](./media/active-geo-replication-overview/geo-replication.png )

> [!IMPORTANT]
> SQL veritabanı Ayrıca otomatik yük devretme gruplarını destekler. Daha fazla bilgi için bkz. [otomatik yük devretme grupları](auto-failover-group-overview.md)kullanma.

Birincil veritabanınız başarısız olursa veya yalnızca çevrimdışı duruma alınması gerekiyorsa, ikincil veritabanlarınızdan herhangi birine yük devretme işlemi başlatabilirsiniz. Yük devretme, ikincil veritabanlarından birine etkinleştirildiğinde, diğer tüm ikincil öğeler yeni birinciye otomatik olarak bağlanır.

Tek bir veritabanının veya bir sunucudaki veya bir veritabanı kümesinin çoğaltma ve yük devretmesini, etkin coğrafi çoğaltma kullanarak bir veya esnek havuzda yönetebilirsiniz. Bunu kullanarak şunları yapabilirsiniz:

- [Azure Portal](active-geo-replication-configure-portal.md)
- [PowerShell: tek veritabanı](scripts/setup-geodr-and-failover-database-powershell.md)
- [PowerShell: elastik havuz](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)
- [Transact-SQL: tek veritabanı veya elastik havuz](/sql/t-sql/statements/alter-database-azure-sql-database)
- [REST API: tek veritabanı](/rest/api/sql/replicationlinks)

Etkin coğrafi çoğaltma, veritabanı altyapısının [her zaman açık kullanılabilirlik grubu](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) teknolojisini kullanır. Bu işlem, birincil veritabanındaki kaydedilmiş işlemleri, anlık görüntü yalıtımı kullanılarak ikincil bir veritabanına zaman uyumsuz olarak çoğaltır. Otomatik yük devretme grupları, etkin coğrafi çoğaltmanın en üstünde grup semantiğini sağlar, ancak aynı zaman uyumsuz çoğaltma mekanizması kullanılır. Herhangi bir noktada, ikincil veritabanı birincil veritabanının biraz arkasında olabilir, ikincil verinin hiçbir zaman kısmi işlemlere sahip olmadığı garanti edilir. Bölgeler arası yedeklilik, uygulamaların, doğal felaketler, çok fazla insan hataları veya kötü amaçlı olarak davranmasından kaynaklanan bir veri merkezinin tamamen veya veri merkezinin bölümlerinin kalıcı bir kaybından hızlı bir şekilde kurtarılmasını sağlar. Belirli RPO verileri [Iş sürekliliği ' ne genel bakış](business-continuity-high-availability-disaster-recover-hadr-overview.md)konusunda bulunabilir.

> [!NOTE]
> İki bölge arasında bir ağ arızası varsa, bağlantıları yeniden kurmak için her 10 saniyede bir yeniden deneme yaptık.

> [!IMPORTANT]
> Birincil veritabanında kritik bir değişikliğin yük devretmeden önce ikincil olarak çoğaltılıp çoğaltılmadığını garantilemek için, kritik değişikliklerin çoğaltılmasını sağlamak üzere eşitlemeyi zorlayabilirsiniz (örneğin, parola güncelleştirmeleri). Zorlanan eşitleme, tüm kaydedilmiş işlemler çoğaltılana kadar çağıran iş parçacığını engellediği için performansı etkiler. Ayrıntılar için bkz. [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync). Birincil veritabanı ve coğrafi ikincil arasındaki çoğaltma gecikmesini izlemek için bkz. [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).

Aşağıdaki şekilde, Orta Kuzey ABD bölgesinde birincil ile yapılandırılmış etkin coğrafi çoğaltma örneği ve Orta Güney ABD bölgesinde ikincil değer gösterilmektedir.

![coğrafi çoğaltma ilişkisi](./media/active-geo-replication-overview/geo-replication-relationship.png)

İkincil veritabanları okunabilir olduğundan, raporlama işleri gibi salt okunur iş yüklerini boşaltmak için kullanılabilirler. Etkin coğrafi çoğaltma kullanıyorsanız, ikincil veritabanını birincil ile aynı bölgede oluşturmak mümkündür, ancak uygulamanın esnekliği önemli hatalara karşı bir şekilde artmaz. Otomatik yük devretme grupları kullanıyorsanız, ikincil veritabanınız her zaman farklı bir bölgede oluşturulur.

Olağanüstü durum kurtarma 'nın yanı sıra etkin coğrafi çoğaltma, aşağıdaki senaryolarda kullanılabilir:

- **Veritabanı geçişi**: en düşük kapalı kalma süresiyle bir veritabanını bir sunucudan başka bir çevrimiçine geçirmek için etkin Coğrafi çoğaltmayı kullanabilirsiniz.
- **Uygulama yükseltmeleri**: uygulama yükseltmeleri sırasında hata geri kopyası olarak ek bir ikincil oluşturabilirsiniz.

Gerçek iş sürekliliği sağlamak için, veri merkezleri arasında veritabanı yedekliliği eklemek çözümün yalnızca bir parçasıdır. Bir uygulamayı (hizmet) çok zararlı bir hatadan sonra kurtarmak, hizmeti ve bağımlı hizmetleri oluşturan tüm bileşenlerin kurtarılmasını gerektirir. Bu bileşenlere örnek olarak, istemci yazılımı (örneğin, özel JavaScript içeren bir tarayıcı), Web ön uçları, depolama alanı ve DNS sayılabilir. Tüm bileşenlerin aynı hatalara dayanıklı olması ve uygulamanızın kurtarma süresi hedefi (RTO) içinde kullanılabilir olması önemlidir. Bu nedenle, tüm bağımlı hizmetleri belirlemeniz ve sağladıkları garantileri ve özellikleri anlamanız gerekir. Daha sonra, hizmetin bağımlı olduğu hizmetlerin yük devretmesi sırasında işlevlerinizin çalıştığından emin olmak için yeterli adımları uygulamanız gerekir. Olağanüstü durum kurtarma çözümleri tasarlama hakkında daha fazla bilgi için bkz. [etkin coğrafi çoğaltma kullanarak olağanüstü durum kurtarma Için bulut çözümleri tasarlama](designing-cloud-solutions-for-disaster-recovery.md).

## <a name="active-geo-replication-terminology-and-capabilities"></a>Etkin coğrafi çoğaltma terimleri ve özellikleri

- **Otomatik zaman uyumsuz çoğaltma**

  Yalnızca var olan bir veritabanına ekleyerek ikincil bir veritabanı oluşturabilirsiniz. İkincil, herhangi bir sunucuda oluşturulabilir. Oluşturulduktan sonra ikincil veritabanı, birincil veritabanından kopyalanmış verilerle doldurulur. Bu işlem, dengeli dağıtım olarak bilinir. İkincil veritabanı oluşturulup oluşturulduktan sonra, birincil veritabanında yapılan güncelleştirmeler zaman uyumsuz olarak ikincil veritabanına çoğaltılır. Zaman uyumsuz çoğaltma, ikincil veritabanına çoğaltılmadan önce işlemlerin birincil veritabanında yürütüldüğü anlamına gelir.

- **Okunabilir ikincil veritabanları**

  Bir uygulama, birincil veritabanına erişmek için kullanılan aynı veya farklı güvenlik sorumlularını kullanarak salt okuma işlemleri için ikincil veritabanına erişebilir. İkincil veritabanları, birincil (günlüğü yeniden yürütme) güncelleştirmelerinin çoğaltılmasını sağlamak için anlık görüntü yalıtımı modunda çalışır.

> [!NOTE]
> Birincil üzerinde şema güncelleştirmeleri varsa, ikincil veritabanında günlük yeniden yürütme gecikiyor. İkinci, ikincil veritabanında bir şema kilidi gerektirir.

> [!IMPORTANT]
> Birincil ile aynı bölgede ikincil bir veritabanı oluşturmak için Coğrafi çoğaltmayı kullanabilirsiniz. Aynı bölgedeki bir salt okunurdur iş yüklerini yük dengelemek için bu ikincili kullanabilirsiniz. Ancak, aynı bölgedeki bir ikincil veritabanı ek hata esnekliği sağlamaz ve bu nedenle olağanüstü durum kurtarma için uygun bir yük devretme hedefi değildir. Ayrıca, kullanılabilirlik alanı yalıtımının garanti etmez. Kullanılabilirlik alanı yalıtımına ulaşmak için Iş açısından kritik veya Premium hizmet katmanını [bölge yedekli yapılandırma](high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) veya genel amaçlı hizmet katmanı [bölge yedekli yapılandırmasıyla](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) kullanın.
>

- **Planlı yük devretme**

  Planlı Yük devretme, tam eşitleme tamamlandıktan sonra birincil ve ikincil veritabanlarının rollerini değiştirir. Veri kaybına neden olmayan bir çevrimiçi işlemdir. İşlemin zamanı, eşitlenmesi gereken birincil üzerindeki işlem günlüğü boyutuna bağlıdır. Planlı Yük devretme, aşağıdaki senaryolar için tasarlanmıştır: (a) veri kaybı kabul edilebilir olmadığında üretimde DR tatbilar gerçekleştirmek için (b) veritabanının farklı bir bölgeye yeniden konumlandırılmanı sağlar; ve (c) kesinti azaltıldıktan sonra veritabanını birincil bölgeye döndürmek için (yeniden çalışma).

- **Planlanmamış yük devretme**

  Planlanmamış veya zorlamalı yük devretme, birincili eşitleme yapmadan ikincili doğrudan birincil role geçirir. Birincil öğesine kaydedilen ancak ikinciye çoğaltılmamış işlemler kaybedilir. Bu işlem, birincil erişim olmadığında, kesintiler sırasında kurtarma yöntemi olarak tasarlanmıştır, ancak veritabanı kullanılabilirliği hızlı bir şekilde geri yüklenmelidir. Özgün birincil yeniden çevrimiçi olduğunda otomatik olarak yeniden bağlanır ve yeni bir ikincil hale gelir. Yük devretmeden önce tüm eşitlenmemiş işlemler yedekleme dosyasında korunacak, ancak çakışmaları önlemek için yeni birinciyle eşitlenmeyecek. Bu işlemlerin, birincil veritabanının en son sürümüyle el ile birleştirilmesi gerekir.

- **Birden çok okunabilir ikinciller**

  Her birincil için 4 ' e kadar ikincil veritabanı oluşturulabilir. Yalnızca bir ikincil veritabanı varsa ve başarısız olursa, yeni bir ikincil veritabanı oluşturuluncaya kadar uygulama daha yüksek riske sunulur. Birden çok ikincil veritabanı varsa, ikincil veritabanlarından biri başarısız olsa bile uygulama korumalı olarak kalır. Ek ikincil öğeler, salt okuma iş yüklerini ölçeklendirmek için de kullanılabilir

  > [!NOTE]
  > Küresel olarak dağıtılan bir uygulama oluşturmak için etkin coğrafi çoğaltma kullanıyorsanız ve dörtten fazla bölgede bulunan verilere salt okuma erişimi sağlamak istiyorsanız, ikincil (zincirleme olarak bilinen bir işlem) ikincil kopyası oluşturabilirsiniz. Bu şekilde, veritabanı çoğaltmasının neredeyse sınırsız ölçeğini elde edebilirsiniz. Ayrıca, zincirleme, birincil veritabanından çoğaltma yükünü azaltır. Ön uç, en yüksek ikincil veritabanlarında bulunan çoğaltma gecikklarıdır.

- **Elastik havuzdaki veritabanlarının coğrafi çoğaltma**

  Her bir ikincil veritabanı, bir elastik havuzda ayrı olarak katılabilir veya hiç esnek havuzda olamaz. Her bir ikincil veritabanı için havuz seçimi ayrıdır ve diğer tüm ikincil veritabanlarının yapılandırmasına (birincil veya ikincil) göre bağlı değildir. Her elastik havuz tek bir bölgede bulunur, bu nedenle aynı topolojide birden fazla ikincil veritabanı hiçbir şekilde elastik havuzu paylaşamaz.

- **Kullanıcı denetimli yük devretme ve yeniden çalışma**

  İkincil bir veritabanı, uygulama veya Kullanıcı tarafından herhangi bir zamanda doğrudan birincil role değiştirilebilir. Gerçek bir kesinti sırasında "planlanmamış" seçeneği kullanılmalıdır ve bu, bir ikincil öğeyi hemen birincil olacak şekilde yükseltir. Başarısız birincil kurtarıldığında ve yeniden kullanılabilir olduğunda, sistem kurtarılan birincili ikincil olarak otomatik olarak işaretler ve yeni birincili güncel duruma getirir. Çoğaltmanın zaman uyumsuz doğası nedeniyle, birincil olan en son değişiklikleri çoğaltmadan önce başarısız olursa plansız yük devretme işlemleri sırasında az miktarda veri kaybolabilir. Birden çok ikincili sahip bir birincil yük devrediliyorsa, sistem çoğaltma ilişkilerini otomatik olarak yeniden yapılandırır ve kalan ikincil bağlantıları, herhangi bir kullanıcı müdahalesi gerektirmeden yeni yükseltilen birincil ile bağlantılandırır. Yük devretmeye neden olan kesinti azaltıldıktan sonra, uygulamayı birincil bölgeye döndürmek istenebilir. Bunu yapmak için, yük devretme komutu "planlanmış" seçeneği ile çağrılmalıdır.

## <a name="preparing-secondary-database-for-failover"></a>İkincil veritabanı yük devretme için hazırlanıyor

Uygulamanızın yük devretmeden sonra yeni birincil sunucuya hemen erişebildiğinden emin olmak için ikincil sunucunuzun ve veritabanınızın kimlik doğrulama gereksinimlerinin doğru yapılandırıldığından emin olun. Ayrıntılar için bkz. [olağanüstü durum kurtarma sonrasında SQL veritabanı güvenliği](active-geo-replication-security-configure.md). Yük devretmeden sonra uyumluluğu güvence altına almak için ikincil veritabanındaki yedekleme bekletme ilkesinin birincili ile eşleştiğinden emin olun. Bu ayarlar veritabanının bir parçası değildir ve çoğaltılmaz. Varsayılan olarak, ikincil değer yedi günlük bir varsayılan bir saklama süresi ile yapılandırılır. Ayrıntılar için bkz. [SQL veritabanı otomatik yedeklemeleri](automated-backups-overview.md).

> [!IMPORTANT]
> Veritabanınız bir yük devretme grubunun üyesiyse, coğrafi çoğaltma yük devretme komutunu kullanarak yük devretmesini başlatamazsınız. Grup için yük devretme komutunu kullanın. Tek bir veritabanının yük devretmesine ihtiyacınız varsa, önce onu yük devretme grubundan kaldırmanız gerekir. Ayrıntılar için bkz.  [Yük devretme grupları](auto-failover-group-overview.md) .

## <a name="configuring-secondary-database"></a>İkincil veritabanını yapılandırma

Birincil ve ikincil veritabanlarının aynı hizmet katmanına sahip olması gerekir. İkincil veritabanının birincil olarak aynı yedekleme depolama yedekliliği ve işlem boyutuyla (DTU 'Lar veya sanal çekirdekler) oluşturulması önemle tavsiye edilir. Birincil veritabanı ağır bir yazma iş yüküyle karşılaşıyorsa, daha düşük işlem boyutu olan bir ikincil değer buna devam edemeyebilir. Bu, ikincil üzerinde yineleme gecikmesi ve ikincil sunucunun kullanılamamasına neden olur. Bu riskleri azaltmak için, etkin coğrafi çoğaltma, ikincillerinin yakalanması için gerekliyse birincil işlem günlüğü oranını azaltacaktır.

İmledengelenmiş bir ikincil yapılandırmanın başka bir sonucu, yük devretmenin ardından, yeni birincili işlem kapasitesi yetersiz olduğundan uygulama performansının zarar verebilir. Bu durumda, veritabanı hizmeti hedefini, önemli zaman ve işlem kaynakları alabilir ve ölçek artırma işleminin sonunda [yüksek kullanılabilirliğe](high-availability-sla.md) sahip yük devretme gerektirecek şekilde, gerekli düzeye ölçeklendirmeniz gerekir.

İkincil işlem boyutuyla ikincil değer oluşturmaya karar verirseniz, Azure portal içindeki günlük GÇ yüzdesi grafiği, çoğaltma yükünü sürdürmek için gereken ikincil işlem boyutunu tahmin etmenin iyi bir yolunu sağlar. Örneğin, birincil veritabanınız P6 (1000 DTU) ve günlük yazma yüzdesi %50 ise, ikincinin en az P4 (500 DTU) olması gerekir. Geçmiş günlük GÇ verilerini almak için [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) görünümünü kullanın. Son günlük yazma verilerini, günlük hızında kısa süreli ani artışları daha iyi yansıtan daha yüksek ayrıntı düzeyi ile almak için [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) görünümünü kullanın.

İkincil işlem boyutu için birincil üzerinde işlem günlüğü hızı azaltma, [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) ve [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) veritabanı görünümlerinde görünür HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO bekleme türü kullanılarak raporlanır.

Varsayılan olarak, ikincil öğesinin yedek depolama yedekliği birincil veritabanıyla aynıdır. İkincili farklı bir yedek depolama yedekliliği ile yapılandırmayı seçebilirsiniz. Yedeklemeler her zaman birincil veritabanında alınır. İkincil, farklı bir yedek depolama yedekliliği ile yapılandırıldıysa, ikincil değer birincil olarak yükseltildiğinde yük devretmeden sonra yedeklemeler, yeni birincil (önceki ikincil) üzerinde seçilen depolama yedekliliğe göre faturalandırılır. 

> [!NOTE]
> Birincil üzerindeki işlem günlüğü hızı, ikincil üzerinde işlem boyutu alt sınır azalmasının ilgisiz olması nedeniyle kısıtlanmayabilir. İkincil öğe, birincili aynı veya daha yüksek bir işlem boyutuna sahip olsa bile, bu tür azaltma meydana gelebilir. Farklı türlerde günlük hızı azaltma için bekleme türleri dahil Ayrıntılar için bkz. [işlem günlüğü oranı İdaresi](resource-limits-logical-server.md#transaction-log-rate-governance).

> [!NOTE]
> Azure SQL veritabanı yapılandırılabilir yedekleme depolama yedekliği Şu anda Brezilya Güney ' de genel önizlemede mevcuttur ve genel olarak yalnızca Güneydoğu Asya Azure bölgesinde kullanılabilir. Kaynak veritabanı yerel olarak yedekli veya bölgesel olarak yedekli yedekleme depolama yedekliliği ile oluşturulduğunda, farklı bir Azure bölgesinde ikincil veritabanı oluşturmak desteklenmez. 

SQL veritabanı işlem boyutları hakkında daha fazla bilgi için bkz. [SQL veritabanı hizmet katmanları](purchasing-models.md)nelerdir.

## <a name="cross-subscription-geo-replication"></a>Çapraz abonelik coğrafi çoğaltma

Farklı aboneliklerde (aynı kiracı altında olsun) iki veritabanı arasında etkin Coğrafi çoğaltmayı ayarlamak için, bu bölümde açıklanan özel yordamı izlemeniz gerekir.  Yordam, SQL komutlarına dayalıdır ve şunları gerektirir:

- Her iki sunucuda da ayrıcalıklı bir oturum oluşturma
- IP adresini her iki sunucuda da değişikliği yapan istemcinin izin verilenler listesine ekleme (SQL Server Management Studio çalıştıran konağın IP adresi gibi).

Değişiklikleri gerçekleştiren istemcinin birincil sunucuya ağ erişimi olması gerekir. İstemcinin aynı IP adresi ikincil sunucuda izin verilenler listesine eklenmelidir, ancak ikincil sunucuya ağ bağlantısı kesinlikle gerekli değildir.

### <a name="on-the-master-of-the-primary-server"></a>Birincil sunucunun ana sayfasında

1. IP adresini, değişiklikleri gerçekleştiren istemcinin izin verilenler listesine ekleyin (daha fazla bilgi için bkz. [güvenlik duvarı yapılandırma](firewall-configure.md)).
1. Etkin coğrafi çoğaltma kurulumu için ayrılmış bir oturum açma oluşturun (ve kimlik bilgilerini gerektiği şekilde ayarlayın):

   ```sql
   create login geodrsetup with password = 'ComplexPassword01'
   ```

1. Karşılık gelen bir kullanıcı oluşturun ve bunu DBManager rolüne atayın:

   ```sql
   create user geodrsetup for login geodrsetup
   alter role dbmanager add member geodrsetup
   ```

1. Bu sorguyu kullanarak yeni oturum açma SID 'sine göz atın:

   ```sql
   select sid from sys.sql_logins where name = 'geodrsetup'
   ```

### <a name="on-the-source-database-on-the-primary-server"></a>Birincil sunucudaki kaynak veritabanında

1. Aynı oturum açma için bir kullanıcı oluşturun:

   ```sql
   create user geodrsetup for login geodrsetup
   ```

1. Kullanıcıyı db_owner rolüne ekleyin:

   ```sql
   alter role db_owner add member geodrsetup
   ```

### <a name="on-the-master-of-the-secondary-server"></a>İkincil sunucunun ana sayfasında

1. İstemci IP adresini, ikincil sunucu için güvenlik duvarı kuralları altında izin verilen listeye ekleyin. Birincil sunucuya eklenen aynı istemci IP adresinin ikinciye de eklenmiş olduğunu doğrulayın. Bu, coğrafi çoğaltma başlatmak için ALTER DATABASE IKINCIL komut Ekle komutunu çalıştırmadan önce yapılması gereken bir adımdır.

1. Aynı Kullanıcı adı parolasını ve SID 'yi kullanarak birincil sunucuda aynı oturum açma bilgilerini oluşturun:

   ```sql
   create login geodrsetup with password = 'ComplexPassword01', sid=0x010600000000006400000000000000001C98F52B95D9C84BBBA8578FACE37C3E
   ```

1. Karşılık gelen bir kullanıcı oluşturun ve bunu DBManager rolüne atayın:

   ```sql
   create user geodrsetup for login geodrsetup;
   alter role dbmanager add member geodrsetup
   ```

### <a name="on-the-master-of-the-primary-server"></a>Birincil sunucunun ana sayfasında

1. Yeni oturum açma bilgilerini kullanarak birincil sunucunun ana sunucusunda oturum açın.
1. İkincil sunucuda kaynak veritabanının ikincil bir çoğaltmasını oluşturun (veritabanı adını ve ServerName 'yi gerektiği şekilde ayarlayın):

   ```sql
   alter database dbrep add secondary on server <servername>
   ```

İlk kurulumdan sonra, oluşturulan kullanıcılar, oturumlar ve güvenlik duvarı kuralları kaldırılabilir.

## <a name="keeping-credentials-and-firewall-rules-in-sync"></a>Kimlik bilgilerini ve güvenlik duvarı kurallarını eşitlenmiş tutma

Coğrafi olarak çoğaltılan veritabanları için [veritabanı DÜZEYINDE IP güvenlik duvarı kuralları](firewall-configure.md) kullanmanızı öneririz. böylece, tüm ikincil veritabanlarının birincil Ile aynı IP güvenlik duvarı kurallarına sahip olduğundan emin olmak için bu kuralların veritabanıyla çoğaltılabilmesini sağlayabilirsiniz. Bu yaklaşım, müşterilerin hem birincil hem de ikincil veritabanlarını barındıran sunucularda güvenlik duvarı kurallarını el ile yapılandırma ve bakımını yapma gereksinimini ortadan kaldırır. Benzer şekilde, veri erişimi için [Kapsanan Veritabanı kullanıcılarının](logins-create-manage.md) kullanılması, hem birincil hem de ikincil veritabanlarının, yük devretme sırasında her zaman aynı kullanıcı kimlik bilgilerine sahip olmasını sağlar, oturum açma işlemleri ve parolalarla uyuşmazlıklar nedeniyle kesintiler olmaz. [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)eklenmesi, müşteriler hem birincil hem de ikincil veritabanlarına Kullanıcı erişimini yönetebilir ve veritabanlarında kimlik bilgilerini yönetme ihtiyacını tamamen ortadan kaldırır.

## <a name="upgrading-or-downgrading-primary-database"></a>Birincil veritabanını yükseltme veya eski sürüme indirme

İkincil veritabanlarının bağlantısını kesmeden, birincil veritabanını farklı bir işlem boyutuna yükseltebilir veya indirgeyebilmeniz gerekir (Genel Amaçlı ve İş Açısından Kritik arasında değil aynı hizmet katmanında). Yükseltme sırasında öncelikle ikincil veritabanını yükseltmenizi ve ardından birincili yükseltmeyi öneririz. Eski sürüme düşürürken bu sırayı tersine çevirin: İlk olarak birincili ve ardından ikincili eski sürüme düşürün. Veritabanını farklı bir hizmet katmanına yükseltirken veya eski sürüme düşürürken bu öneri zorunlu tutulur.

> [!NOTE]
> İkincil veritabanını yük devretme grup yapılandırmasının bir parçası olarak oluşturduysanız ikincil veritabanını eski sürüme düşürmeniz önerilmez. Bu, veri katmanınızın yük devretme etkinleştirildikten sonra düzenli iş yükünüzü işlemek için yeterli kapasiteye sahip olmasını sağlamaktır.

> [!IMPORTANT]
> İlk olarak ikincil veritabanı daha yüksek bir katmana ölçeklendirilmemişse yük devretme grubundaki birincil veritabanı da ölçeklendirilemez. İkinci veritabanının ölçeklendirilmesi için birincil veritabanını ölçeklendirmeye çalışırsanız, şu hatayı alabilirsiniz:
>
> `Error message: The source database 'Primaryserver.DBName' cannot have higher edition than the target database 'Secondaryserver.DBName'. Upgrade the edition on the target before upgrading the source.`
>

## <a name="preventing-the-loss-of-critical-data"></a>Kritik verilerin kaybını önlemek

Geniş alan ağlarının yüksek gecikmesi nedeniyle, sürekli kopyalama zaman uyumsuz bir çoğaltma mekanizması kullanır. Zaman uyumsuz çoğaltma, bir hata oluşursa, bazı veri kaybını korumasız hale getirir. Ancak, bazı uygulamalar veri kaybı gerektirmez. Bu kritik güncelleştirmeleri korumak için, bir uygulama geliştiricisi işlemi tamamladıktan hemen sonra [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) sistem yordamını çağırabilir. **Sp_wait_for_database_copy_sync** çağırmak, son kaydedilen işlem ikincil veritabanına iletilene kadar çağıran iş parçacığını engeller. Ancak, iletilen işlemlerin yeniden çalınmasını ve ikincil üzerinde gerçekleşmesini beklemez. **sp_wait_for_database_copy_sync** , belirli bir sürekli kopyalama bağlantısının kapsamına alınır. Birincil veritabanında bağlantı hakları olan herhangi bir Kullanıcı, bu yordamı çağırabilir.

> [!NOTE]
> **sp_wait_for_database_copy_sync** yük devretmeden sonra veri kaybını önler, ancak okuma erişimi için tam eşitlemeyi garanti etmez. **Sp_wait_for_database_copy_sync** yordam çağrısının neden olduğu gecikme önemli olabilir ve çağrı sırasında işlem günlüğünün boyutuna bağlıdır.

## <a name="monitoring-geo-replication-lag"></a>Coğrafi çoğaltma gecikmesini izleme

RPO 'ya göre gecikme süresini izlemek için, birincil veritabanında [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) *replication_lag_sec* sütununu kullanın. Birincil üzerinde yürütülen ve ikincil üzerinde kalıcı olan işlemler arasındaki gecikme süresini saniye cinsinden gösterir. Örneğin öteleme değeri 1 saniye ise, birincil, bu anda bir kesinti tarafından etkilenirse ve yük devretme başlatıldığında en son geçişlerin 1 ikinci geçiş kaydedilmez.

Birincil veritabanındaki değişikliklere göre gecikme süresini ölçmek için, ikincilden okunmak üzere, ikincil veritabanındaki *last_commit* süreyi birincil veritabanında aynı değere göre karşılaştırın.

> [!NOTE]
> Birincil veritabanındaki *replication_lag_sec* bazen null değere sahiptir, bu da birincil değerin ikincili 'nin ne kadar olduğunu bilmez.   Bu genellikle işlem yeniden başlatıldıktan sonra gerçekleşir ve geçici bir koşul olmalıdır. *Replication_lag_sec* uzun bir süre için null döndürürse uygulamayı uyarmayı düşünün. Kalıcı bir bağlantı arızası nedeniyle ikincil veritabanının birincil ile iletişim kuramadığını gösterir. Ayrıca, ikincil ve birincil veritabanındaki *last_commit* zaman arasındaki farkın büyük hale gelmesine neden olabilecek koşullar da vardır. Örneğin büyük bir süre sonra birincil üzerinde bir kayıt yapılırsa, fark 0 ' a hızla döndürülmeden önce büyük bir değere geçer. Bu iki değer arasındaki fark uzun bir süre kaldığında bunu bir hata koşuluna göz önünde bulundurun.

## <a name="programmatically-managing-active-geo-replication"></a>Etkin Coğrafi çoğaltmayı programlı bir şekilde yönetme

Daha önce anlatıldığı gibi, etkin coğrafi çoğaltma Azure PowerShell ve REST API kullanılarak programlı bir şekilde yönetilebilir. Aşağıdaki tablolarda kullanılabilen komut kümesi açıklanır. Etkin coğrafi çoğaltma, [Azure SQL veritabanı REST API](/rest/api/sql/) ve [Azure PowerShell cmdlet 'leri](/powershell/azure/)dahil olmak üzere yönetim için Azure Resource Manager API 'ler kümesi içerir. Bu API 'Ler, kaynak gruplarının kullanımını gerektirir ve Azure rol tabanlı erişim denetimi 'ni (Azure RBAC) destekler. Erişim rollerinin nasıl uygulanacağı hakkında daha fazla bilgi için bkz. [Azure rol tabanlı erişim denetimi (Azure RBAC)](../../role-based-access-control/overview.md).

### <a name="t-sql-manage-failover-of-single-and-pooled-databases"></a>T-SQL: tek ve havuza alınmış veritabanlarının yük devretmesini yönetme

> [!IMPORTANT]
> Bu Transact-SQL komutları yalnızca etkin coğrafi çoğaltma için geçerlidir ve yük devretme grupları için geçerli değildir. Bu nedenle, yalnızca yük devretme gruplarını desteklediklerinden, SQL yönetilen örnek örnekleri için de uygulanmazlar.

| Komut | Açıklama |
| --- | --- |
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-current) |Var olan bir veritabanı için ikincil bir veritabanı oluşturmak ve veri çoğaltmasını açmak için sunucu bağımsız değişkeni Ekle öğesini kullanın |
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-current) |Yük devretmeyi başlatmak için ikincil bir veritabanını birincil olacak şekilde değiştirmek için yük devretme veya FORCE_FAILOVER_ALLOW_DATA_LOSS kullanma |
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-current) |SQL veritabanı ve belirtilen ikincil veritabanı arasında bir veri çoğaltmasını sonlandırmak için sunucuda IKINCILI kaldır 'ı kullanın. |
| [sys.geo_replication_links](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |Bir sunucudaki her bir veritabanı için varolan tüm çoğaltma bağlantılarıyla ilgili bilgileri döndürür. |
| [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |Son çoğaltma zamanını, son çoğaltma gecikmesini ve belirli bir veritabanı için çoğaltma bağlantısıyla ilgili diğer bilgileri alır. |
| [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |Çoğaltma bağlantılarının durumu da dahil olmak üzere tüm veritabanı işlemlerinin durumunu gösterir. |
| [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |uygulamanın tüm kaydedilmiş işlemler etkin ikincil veritabanı tarafından çoğaltılıncaya ve onaylanana kadar bekleyip beklememesine neden olur. |
|  | |

### <a name="powershell-manage-failover-of-single-and-pooled-databases"></a>PowerShell: tek ve havuza alınmış veritabanlarının yük devretmesini yönetme

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager modülü Azure SQL veritabanı tarafından hala desteklenmektedir, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. Bu cmdlet 'ler için bkz. [Azurerd. SQL](/powershell/module/AzureRM.Sql/). Az Module ve Azurerd modüllerinde komutların bağımsız değişkenleri önemli ölçüde aynıdır.

| Cmdlet | Açıklama |
| --- | --- |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Bir veya daha fazla veritabanını alır. |
| [New-AzSqlDatabaseSecondary](/powershell/module/az.sql/new-azsqldatabasesecondary) |Mevcut bir veritabanı için ikincil bir veritabanı oluşturur ve veri çoğaltmaya başlar. |
| [Set-AzSqlDatabaseSecondary](/powershell/module/az.sql/set-azsqldatabasesecondary) |Yük devretmeyi başlatmak için ikincil bir veritabanını birincil olarak değiştirir. |
| [Remove-AzSqlDatabaseSecondary](/powershell/module/az.sql/remove-azsqldatabasesecondary) |Bir SQL Veritabanı ile belirtilen ikincil veritabanı arasında veri çoğaltmayı sonlandırır. |
| [Get-AzSqlDatabaseReplicationLink](/powershell/module/az.sql/get-azsqldatabasereplicationlink) |Bir Azure SQL veritabanı ile kaynak grubu veya mantıksal SQL Server arasındaki coğrafi çoğaltma bağlantılarını alır. |
|  | |

> [!IMPORTANT]
> Örnek betikler için bkz. [etkin coğrafi çoğaltma kullanarak tek bir veritabanını yapılandırma ve yük devretme](scripts/setup-geodr-and-failover-database-powershell.md) ve [etkin coğrafi çoğaltma kullanarak havuza alınmış bir veritabanını yapılandırma ve yük](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)devretme.

### <a name="rest-api-manage-failover-of-single-and-pooled-databases"></a>REST API: tek ve havuza alınmış veritabanlarının yük devretmesini yönetme

| API | Açıklama |
| --- | --- |
| [Veritabanı oluştur veya güncelleştir (createMode = restore)](/rest/api/sql/databases/createorupdate) |Birincil veya ikincil bir veritabanını oluşturur, güncelleştirir veya geri yükler. |
| [Veritabanı oluşturma veya güncelleştirme durumunu al](/rest/api/sql/databases/createorupdate) |Oluşturma işlemi sırasında durumu döndürür. |
| [Ikincil veritabanını birincil olarak ayarla (planlı yük devretme)](/rest/api/sql/replicationlinks/failover) |Geçerli birincil veritabanından yük devreterek hangi ikincil veritabanının birincil olduğunu ayarlar. **Bu seçenek SQL yönetilen örneği için desteklenmez.**|
| [Ikincil veritabanını birincil olarak ayarla (planlanmamış yük devretme)](/rest/api/sql/replicationlinks/failoverallowdataloss) |Geçerli birincil veritabanından yük devreterek hangi ikincil veritabanının birincil olduğunu ayarlar. Bu işlem, veri kaybına neden olabilir. **Bu seçenek SQL yönetilen örneği için desteklenmez.**|
| [Çoğaltma bağlantısını al](/rest/api/sql/replicationlinks/get) |Coğrafi çoğaltma ortaklığı içindeki belirli bir veritabanı için belirli bir çoğaltma bağlantısını alır. Sys.geo_replication_links katalog görünümünde görünen bilgileri alır. **Bu seçenek SQL yönetilen örneği için desteklenmez.**|
| [Çoğaltma bağlantıları-veritabanına göre liste](/rest/api/sql/replicationlinks/listbydatabase) | Coğrafi çoğaltma ortaklığında belirli bir veritabanı için tüm çoğaltma bağlantılarını alır. Sys.geo_replication_links katalog görünümünde görünen bilgileri alır. |
| [Çoğaltma bağlantısını sil](/rest/api/sql/replicationlinks/delete) | Bir veritabanı çoğaltma bağlantısını siler. Yük devretme sırasında gerçekleştirilemez. |
|  | |




## <a name="next-steps"></a>Sonraki adımlar

- Örnek betikler için bkz.:
  - [Etkin coğrafi çoğaltmayı kullanarak tek bir veritabanını yapılandırma ve tek bir veritabanının yükünü devretme](scripts/setup-geodr-and-failover-database-powershell.md)
  - [Etkin coğrafi çoğaltmayı kullanarak havuza alınan veritabanını yapılandırma ve havuza alınmış veritabanının yükünü devretme](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)
- SQL veritabanı Ayrıca otomatik yük devretme gruplarını destekler. Daha fazla bilgi için bkz. [otomatik yük devretme grupları](auto-failover-group-overview.md)kullanma.
- İş sürekliliği için genel bakış ve senaryolar için bkz. [iş sürekliliği genel bakış](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- Azure SQL veritabanı otomatik yedeklemeleri hakkında bilgi edinmek için bkz. [SQL veritabanı otomatik yedeklemeleri](automated-backups-overview.md).
- Kurtarmaya yönelik otomatik yedeklemeleri kullanma hakkında bilgi edinmek için bkz. [hizmet tarafından başlatılan yedeklemelerden bir veritabanını geri yükleme](recovery-using-backups.md).
- Yeni bir birincil sunucu ve veritabanına yönelik kimlik doğrulama gereksinimleri hakkında bilgi edinmek için, bkz. [olağanüstü durum kurtarma sonrasında SQL veritabanı güvenliği](active-geo-replication-security-configure.md).
