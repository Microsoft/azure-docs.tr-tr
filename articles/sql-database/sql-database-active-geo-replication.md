---
title: Etkin coğrafi çoğaltma
description: Aynı veya farklı veri merkezinde (bölge) tek tek veritabanlarının okunabilir ikincil veritabanlarını oluşturmak için etkin Coğrafi çoğaltmayı kullanın.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 07/09/2019
ms.openlocfilehash: 33697fd8d3b0c6faea423026e1462834c6b1ef4c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822651"
---
# <a name="creating-and-using-active-geo-replication"></a>Etkin coğrafi çoğaltma oluşturma ve kullanma

Etkin coğrafi çoğaltma, aynı veya farklı veri merkezinde (bölge) bir SQL veritabanı sunucusunda tek tek veritabanlarının okunabilir ikincil veritabanlarını oluşturmanıza olanak sağlayan Azure SQL veritabanı özelliğidir.

> [!NOTE]
> Etkin coğrafi çoğaltma, yönetilen örnek tarafından desteklenmiyor. Yönetilen örneklerin coğrafi yük devretmesi için [otomatik yük devretme grupları](sql-database-auto-failover-group.md)kullanın.

Etkin coğrafi çoğaltma, uygulamanın bölgesel bir olağanüstü durum veya büyük ölçekli bir kesinti olması durumunda bireysel veritabanlarının hızlı olağanüstü durum kurtarması gerçekleştirmesini sağlayan bir iş sürekliliği çözümü olarak tasarlanmıştır. Coğrafi çoğaltma etkinse, uygulama farklı bir Azure bölgesindeki ikincil bir veritabanına yük devretme başlatabilir. Aynı veya farklı bölgelerde en fazla dört ikincil desteklenir ve ikincil öğeler de salt okuma erişim sorguları için kullanılabilir. Yük devretme, uygulama veya Kullanıcı tarafından el ile başlatılmalıdır. Yük devretmeden sonra yeni birincil, farklı bir bağlantı uç noktasına sahiptir. Aşağıdaki diyagramda, etkin coğrafi çoğaltma kullanılarak coğrafi olarak yedekli bir bulut uygulamasının tipik bir yapılandırması gösterilmektedir.

![etkin coğrafi çoğaltma](./media/sql-database-active-geo-replication/geo-replication.png )

> [!IMPORTANT]
> SQL veritabanı Ayrıca otomatik yük devretme gruplarını destekler. Daha fazla bilgi için bkz. [otomatik yük devretme grupları](sql-database-auto-failover-group.md)kullanma. Ayrıca, etkin coğrafi çoğaltma, yönetilen bir örnek içinde oluşturulan veritabanları için desteklenmez. Yönetilen örneklerle [Yük devretme grupları](sql-database-auto-failover-group.md) kullanmayı göz önünde bulundurun.

Birincil veritabanınız başarısız olursa veya yalnızca çevrimdışı duruma alınması gerekiyorsa, ikincil veritabanlarınızdan herhangi birine yük devretme işlemi başlatabilirsiniz. Yük devretme, ikincil veritabanlarından birine etkinleştirildiğinde, diğer tüm ikincil öğeler yeni birinciye otomatik olarak bağlanır.

Tek bir veritabanının veya bir sunucudaki veya bir veritabanı kümesinin çoğaltma ve yük devretmesini, etkin coğrafi çoğaltma kullanarak bir veya esnek havuzda yönetebilirsiniz. Bunu kullanarak şunları yapabilirsiniz:

- [Azure portalı](sql-database-geo-replication-portal.md)
- [PowerShell: tek veritabanı](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [PowerShell: elastik havuz](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- [Transact-SQL: tek veritabanı veya elastik havuz](/sql/t-sql/statements/alter-database-azure-sql-database)
- [REST API: tek veritabanı](https://docs.microsoft.com/rest/api/sql/replicationlinks)


Etkin coğrafi çoğaltma, birincil veritabanındaki kaydedilmiş işlemleri anlık görüntü yalıtımı kullanarak ikincil bir veritabanına zaman uyumsuz olarak çoğaltmak için SQL Server [her zaman açık](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) teknolojisini kullanır. Otomatik yük devretme grupları, etkin coğrafi çoğaltmanın en üstünde grup semantiğini sağlar, ancak aynı zaman uyumsuz çoğaltma mekanizması kullanılır. Herhangi bir noktada, ikincil veritabanı birincil veritabanının biraz arkasında olabilir, ikincil verinin hiçbir zaman kısmi işlemlere sahip olmadığı garanti edilir. Bölgeler arası yedeklilik, uygulamaların, doğal felaketler, çok fazla insan hataları veya kötü amaçlı olarak davranmasından kaynaklanan bir veri merkezinin tamamen veya veri merkezinin bölümlerinin kalıcı bir kaybından hızlı bir şekilde kurtarılmasını sağlar. Belirli RPO verileri [Iş sürekliliği ' ne genel bakış](sql-database-business-continuity.md)konusunda bulunabilir.

> [!NOTE]
> İki bölge arasında bir ağ arızası varsa, bağlantıları yeniden kurmak için her 10 saniyede bir yeniden deneme yaptık.
> [!IMPORTANT]
> Birincil veritabanında kritik bir değişikliğin yük devretmeden önce ikincil olarak çoğaltılıp çoğaltılmadığını garantilemek için, kritik değişikliklerin çoğaltılmasını sağlamak üzere eşitlemeyi zorlayabilirsiniz (örneğin, parola güncelleştirmeleri). Zorlanan eşitleme, tüm kaydedilmiş işlemler çoğaltılana kadar çağıran iş parçacığını engellediği için performansı etkiler. Ayrıntılar için bkz. [sp_wait_for_database_copy_sync](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync). Birincil veritabanı ve coğrafi ikincil arasındaki çoğaltma gecikmesini izlemek için bkz. [sys. dm_geo_replication_link_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).

Aşağıdaki şekilde, Orta Kuzey ABD bölgesinde birincil ile yapılandırılmış etkin coğrafi çoğaltma örneği ve Orta Güney ABD bölgesinde ikincil değer gösterilmektedir.

![coğrafi çoğaltma ilişkisi](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

İkincil veritabanları okunabilir olduğundan, raporlama işleri gibi salt okunur iş yüklerini boşaltmak için kullanılabilirler. Etkin coğrafi çoğaltma kullanıyorsanız, ikincil veritabanını birincil ile aynı bölgede oluşturmak mümkündür, ancak uygulamanın esnekliği önemli hatalara karşı bir şekilde artmaz. Otomatik yük devretme grupları kullanıyorsanız, ikincil veritabanınız her zaman farklı bir bölgede oluşturulur.

Olağanüstü durum kurtarma 'nın yanı sıra etkin coğrafi çoğaltma, aşağıdaki senaryolarda kullanılabilir:

- **Veritabanı geçişi**: en düşük kapalı kalma süresiyle bir veritabanını bir sunucudan başka bir çevrimiçine geçirmek için etkin Coğrafi çoğaltmayı kullanabilirsiniz.
- **Uygulama yükseltmeleri**: uygulama yükseltmeleri sırasında hata geri kopyası olarak ek bir ikincil oluşturabilirsiniz.

Gerçek iş sürekliliği sağlamak için, veri merkezleri arasında veritabanı yedekliliği eklemek çözümün yalnızca bir parçasıdır. Bir uygulamayı (hizmet) çok zararlı bir hatadan sonra kurtarmak, hizmeti ve bağımlı hizmetleri oluşturan tüm bileşenlerin kurtarılmasını gerektirir. Bu bileşenlere örnek olarak, istemci yazılımı (örneğin, özel JavaScript içeren bir tarayıcı), Web ön uçları, depolama alanı ve DNS sayılabilir. Tüm bileşenlerin aynı hatalara dayanıklı olması ve uygulamanızın kurtarma süresi hedefi (RTO) içinde kullanılabilir olması önemlidir. Bu nedenle, tüm bağımlı hizmetleri belirlemeniz ve sağladıkları garantileri ve özellikleri anlamanız gerekir. Daha sonra, hizmetin bağımlı olduğu hizmetlerin yük devretmesi sırasında işlevlerinizin çalıştığından emin olmak için yeterli adımları uygulamanız gerekir. Olağanüstü durum kurtarma çözümleri tasarlama hakkında daha fazla bilgi için bkz. [etkin coğrafi çoğaltma kullanarak olağanüstü durum kurtarma Için bulut çözümleri tasarlama](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="active-geo-replication-terminology-and-capabilities"></a>Etkin coğrafi çoğaltma terimleri ve özellikleri

- **Otomatik zaman uyumsuz çoğaltma**

  Yalnızca var olan bir veritabanına ekleyerek ikincil bir veritabanı oluşturabilirsiniz. İkincil bu, herhangi bir Azure SQL veritabanı sunucusunda oluşturulabilir. Oluşturulduktan sonra ikincil veritabanı, birincil veritabanından kopyalanmış verilerle doldurulur. Bu işlem, dengeli dağıtım olarak bilinir. İkincil veritabanı oluşturulup oluşturulduktan sonra, birincil veritabanında yapılan güncelleştirmeler zaman uyumsuz olarak ikincil veritabanına çoğaltılır. Zaman uyumsuz çoğaltma, ikincil veritabanına çoğaltılmadan önce işlemlerin birincil veritabanında yürütüldüğü anlamına gelir.

- **Okunabilir ikincil veritabanları**

  Bir uygulama, birincil veritabanına erişmek için kullanılan aynı veya farklı güvenlik sorumlularını kullanarak salt okuma işlemleri için ikincil veritabanına erişebilir. İkincil veritabanları, birincil (günlüğü yeniden yürütme) güncelleştirmelerinin çoğaltılmasını sağlamak için anlık görüntü yalıtımı modunda çalışır.

> [!NOTE]
> Birincil üzerinde şema güncelleştirmeleri varsa, ikincil veritabanında günlük yeniden yürütme gecikiyor. İkinci, ikincil veritabanında bir şema kilidi gerektirir.
> [!IMPORTANT]
> Birincil ile aynı bölgede ikincil bir veritabanı oluşturmak için Coğrafi çoğaltmayı kullanabilirsiniz. Aynı bölgedeki bir salt okunurdur iş yüklerini yük dengelemek için bu ikincili kullanabilirsiniz. Ancak, aynı bölgedeki bir ikincil veritabanı ek hata esnekliği sağlamaz ve bu nedenle olağanüstü durum kurtarma için uygun bir yük devretme hedefi değildir. Ayrıca, kullanılabilirlik alanı yalıtımının garanti etmez. Kullanılabilirlik alanı yalıtımına ulaşmak için Iş açısından kritik veya Premium hizmet katmanını [bölge yedekli yapılandırmasıyla](sql-database-high-availability.md#zone-redundant-configuration) kullanın.   
>

- **Planlı Yük devretme**

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

Uygulamanızın yük devretmeden sonra yeni birincil sunucuya hemen erişebildiğinden emin olmak için ikincil sunucunuzun ve veritabanınızın kimlik doğrulama gereksinimlerinin doğru yapılandırıldığından emin olun. Ayrıntılar için bkz. [olağanüstü durum kurtarma sonrasında SQL veritabanı güvenliği](sql-database-geo-replication-security-config.md). Yük devretmeden sonra uyumluluğu güvence altına almak için ikincil veritabanındaki yedekleme bekletme ilkesinin birincili ile eşleştiğinden emin olun. Bu ayarlar veritabanının bir parçası değildir ve çoğaltılmaz. Varsayılan olarak, ikincil değer yedi günlük bir varsayılan bir saklama süresi ile yapılandırılır. Ayrıntılar için bkz. [SQL veritabanı otomatik yedeklemeleri](sql-database-automated-backups.md).

> [!IMPORTANT]
> Veritabanınız bir yük devretme grubunun üyesiyse, coğrafi çoğaltma faiover komutunu kullanarak yük devretmesini başlatamazsınız. Grup için yük devretme komutunu kullanmayı düşünün. Tek bir veritabanının yük devretmesine ihtiyacınız varsa, önce onu yük devretme grubundan kaldırmanız gerekir. Ayrıntılar için bkz. [Yük devretme grupları](sql-database-auto-failover-group.md) . 


## <a name="configuring-secondary-database"></a>İkincil veritabanını yapılandırma

Birincil ve ikincil veritabanlarının aynı hizmet katmanına sahip olması gerekir. İkincil veritabanının birincil olarak aynı işlem boyutuyla (DTU 'Lar veya sanal çekirdekler) oluşturulması kesinlikle önerilir. Birincil veritabanı ağır bir yazma iş yüküyle karşılaşıyorsa, daha düşük işlem boyutu olan bir ikincil değer buna devam edemeyebilir. İkincil ve olası kullanım dışı durumunda yineleme gecikmesi oluşmasına neden olur. İkincil veritabanının birincil veritabanının gerisinde kalması, zorlamalı yük devretme gerekmesi halinde büyük bir veri kaybı yaşanması riskini de taşır. Bu riskleri azaltmak için, etkin etkin coğrafi çoğaltma, İkincilin yakalamalı olması için birincil günlük hızını azaltacaktır. İmledengelenmiş bir ikincil yapılandırmanın diğer sonucu, yük devretme sonrasında uygulamanın performansının, yeni birincili işlem kapasitesi yetersiz olduğundan, bundan sonra zarar görür. Kesinti azaltılana kadar mümkün olmayacak şekilde, gereken düzeye daha yüksek bir işlem yükseltmesi yapması gerekecektir. 


> [!IMPORTANT]
> İkincil veritabanı, birincil ile aynı işlem boyutuyla yapılandırılmadığı sürece yayınlanan RPO = 5 sn garanti edilemez. 


İkincil işlem boyutuyla ikincil değer oluşturmaya karar verirseniz, Azure portal üzerindeki günlük GÇ yüzdesi grafiği, çoğaltma yükünü sürdürmek için gereken ikincil işlem boyutunu tahmin etmenin iyi bir yolunu sağlar. Örneğin, birincil veritabanınız P6 (1000 DTU) ve günlük GÇ yüzdesi %50 ise ikincinin en az P4 (500 DTU) olması gerekir. Ayrıca, [sys. resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) veya [sys. dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) VERITABANı görünümlerini kullanarak günlük GÇ verilerini de alabilirsiniz.  Daraltma, [sys. dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) ve [sys. dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) veritabanı görünümlerinde HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO bekleme durumu olarak bildirilir. 

SQL veritabanı işlem boyutları hakkında daha fazla bilgi için bkz. [SQL veritabanı hizmet katmanları](sql-database-purchase-models.md)nelerdir.

## <a name="keeping-credentials-and-firewall-rules-in-sync"></a>Kimlik bilgilerini ve güvenlik duvarı kurallarını eşitlenmiş tutma

Coğrafi olarak çoğaltılan veritabanları için [veritabanı DÜZEYINDE IP güvenlik duvarı kuralları](sql-database-firewall-configure.md) kullanmanızı öneririz. böylece, tüm ikincil veritabanlarının birincil Ile aynı IP güvenlik duvarı kurallarına sahip olduğundan emin olmak için bu kuralların veritabanıyla çoğaltılabilmesini sağlayabilirsiniz. Bu yaklaşım, müşterilerin hem birincil hem de ikincil veritabanlarını barındıran sunucularda güvenlik duvarı kurallarını el ile yapılandırma ve bakımını yapma gereksinimini ortadan kaldırır. Benzer şekilde, veri erişimi için [Kapsanan Veritabanı kullanıcılarının](sql-database-manage-logins.md) kullanılması, hem birincil hem de ikincil veritabanlarının, yük devretme sırasında her zaman aynı kullanıcı kimlik bilgilerine sahip olmasını sağlar, oturum açma işlemleri ve parolalarla uyuşmazlıklar nedeniyle kesintiler olmaz. [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)eklenmesi, müşteriler hem birincil hem de ikincil veritabanlarına Kullanıcı erişimini yönetebilir ve veritabanlarında kimlik bilgilerini yönetme ihtiyacını tamamen ortadan kaldırır.

## <a name="upgrading-or-downgrading-primary-database"></a>Birincil veritabanını yükseltme veya eski sürüme indirme

İkincil veritabanlarının bağlantısını kesmeden, birincil veritabanını farklı bir işlem boyutuna yükseltebilir veya indirgeyebilmeniz gerekir (Genel Amaçlı ve İş Açısından Kritik arasında değil aynı hizmet katmanında). Yükseltme sırasında öncelikle ikincil veritabanını yükseltmenizi ve ardından birincili yükseltmeyi öneririz. Eski sürüme düşürme sırasında sırayı tersine çevirin: önce birincili düşürme ve sonra ikincil sürümü düşürme. Veritabanını yükseltirken veya farklı bir hizmet katmanına indirgemeniz durumunda, bu öneri zorlanır.

> [!NOTE]
> İkincil veritabanını yük devretme grubu yapılandırmasının bir parçası olarak oluşturduysanız, ikincil veritabanının indirgenmesini öneririz. Bu, veri katmanınızın yük devretme etkinleştirildikten sonra düzenli iş yükünüzü işlemek için yeterli kapasiteye sahip olmasını sağlamaktır.

> [!IMPORTANT]
> İkincil veritabanı ilk olarak daha yüksek katmana ölçeklenmediği takdirde, bir yük devretme grubundaki birincil veritabanı daha yüksek bir katmana ölçeklendiremez. İkinci veritabanının ölçeklendirilmesi için birincil veritabanını ölçeklendirmeye çalışırsanız, şu hatayı alabilirsiniz:
>
> `Error message: The source database 'Primaryserver.DBName' cannot have higher edition than the target database 'Secondaryserver.DBName'. Upgrade the edition on the target before upgrading the source.`
>

## <a name="preventing-the-loss-of-critical-data"></a>Kritik verilerin kaybını önlemek

Geniş alan ağlarının yüksek gecikmesi nedeniyle, sürekli kopyalama zaman uyumsuz bir çoğaltma mekanizması kullanır. Zaman uyumsuz çoğaltma, bir hata oluşursa, bazı veri kaybını korumasız hale getirir. Ancak, bazı uygulamalar veri kaybı gerektirmez. Bu kritik güncelleştirmeleri korumak için, bir uygulama geliştiricisi işlemi tamamladıktan hemen sonra [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) sistem yordamını çağırabilir. **Sp_wait_for_database_copy_sync** çağırmak, son kaydedilen işlem ikincil veritabanına iletilene kadar çağıran iş parçacığını engeller. Ancak, iletilen işlemlerin yeniden çalınmasını ve ikincil üzerinde gerçekleşmesini beklemez. **sp_wait_for_database_copy_sync** , belirli bir sürekli kopyalama bağlantısının kapsamına alınır. Birincil veritabanında bağlantı hakları olan herhangi bir Kullanıcı, bu yordamı çağırabilir.

> [!NOTE]
> **sp_wait_for_database_copy_sync** yük devretmeden sonra veri kaybını önler, ancak okuma erişimi için tam eşitlemeyi garanti etmez. **Sp_wait_for_database_copy_sync** yordam çağrısının neden olduğu gecikme önemli olabilir ve çağrı sırasında işlem günlüğünün boyutuna bağlıdır.

## <a name="monitoring-geo-replication-lag"></a>Coğrafi çoğaltma gecikmesini izleme

RPO 'ya göre gecikme süresini izlemek için birincil veritabanında [sys. dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) *replication_lag_sec* sütununu kullanın. Birincil üzerinde yürütülen ve ikincil üzerinde kalıcı olan işlemler arasındaki gecikme süresini saniye cinsinden gösterir. Örneğin öteleme değeri 1 saniye ise, birincil, bu anda bir kesinti tarafından etkilenirse ve yük devretme başlatıldığında en son geçişlerin 1 ikinci geçiş kaydedilmez. 

Birincil veritabanındaki değişikliklere göre gecikme süresini ölçmek için, ikincilden okunmak üzere, ikincil veritabanındaki *last_commit* süreyi birincil veritabanında aynı değere göre karşılaştırın.

> [!NOTE]
> Birincil veritabanındaki *replication_lag_sec* bazen null değere sahiptir, bu da birincil değerin ikincili 'nin ne kadar olduğunu bilmez.   Bu genellikle işlem yeniden başlatıldıktan sonra gerçekleşir ve geçici bir koşul olmalıdır. *Replication_lag_sec* uzun bir süre için null döndürürse uygulamayı uyarmayı düşünün. Kalıcı bir bağlantı arızası nedeniyle ikincil veritabanının birincil ile iletişim kuramadığını gösterir. Ayrıca, ikincil ve birincil veritabanındaki *last_commit* zaman arasındaki farkın büyük hale gelmesine neden olabilecek koşullar da vardır. Örneğin büyük bir süre sonra birincil üzerinde bir kayıt yapılırsa, fark 0 ' a hızla döndürülmeden önce büyük bir değere geçer. Bu iki değer arasındaki fark uzun bir süre kaldığında bunu bir hata koşuluna göz önünde bulundurun.


## <a name="programmatically-managing-active-geo-replication"></a>Etkin Coğrafi çoğaltmayı programlı bir şekilde yönetme

Daha önce anlatıldığı gibi, etkin coğrafi çoğaltma Azure PowerShell ve REST API kullanılarak programlı bir şekilde yönetilebilir. Aşağıdaki tablolarda kullanılabilen komut kümesi açıklanır. Etkin coğrafi çoğaltma, [Azure SQL veritabanı REST API](https://docs.microsoft.com/rest/api/sql/) ve [Azure PowerShell cmdlet 'leri](https://docs.microsoft.com/powershell/azure/overview)dahil olmak üzere yönetim için Azure Resource Manager API 'ler kümesi içerir. Bu API 'Ler, kaynak gruplarının kullanımını gerektirir ve rol tabanlı güvenliği (RBAC) destekler. Erişim rollerinin nasıl uygulanacağı hakkında daha fazla bilgi için bkz. [Azure rol tabanlı Access Control](../role-based-access-control/overview.md).

### <a name="t-sql-manage-failover-of-single-and-pooled-databases"></a>T-SQL: tek ve havuza alınmış veritabanlarının yük devretmesini yönetme

> [!IMPORTANT]
> Bu Transact-SQL komutları yalnızca etkin coğrafi çoğaltma için geçerlidir ve yük devretme grupları için geçerli değildir. Bu nedenle, yalnızca yük devretme gruplarını destekledikleri için yönetilen örneklere de uygulanmaz.

| Komut | Açıklama |
| --- | --- |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Var olan bir veritabanı için ikincil bir veritabanı oluşturmak ve veri çoğaltmasını açmak için sunucu bağımsız değişkeni Ekle öğesini kullanın |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Yük devretmeyi başlatmak için ikincil bir veritabanını birincil olacak şekilde değiştirmek için yük devretme veya FORCE_FAILOVER_ALLOW_DATA_LOSS kullanma |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |SQL veritabanı ve belirtilen ikincil veritabanı arasında bir veri çoğaltmasını sonlandırmak için sunucuda IKINCILI kaldır 'ı kullanın. |
| [sys. geo_replication_links](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |Azure SQL veritabanı sunucusundaki her bir veritabanı için varolan tüm çoğaltma bağlantılarıyla ilgili bilgileri döndürür. |
| [sys. dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |Belirli bir SQL veritabanının çoğaltma bağlantısı ile ilgili son çoğaltma zamanını, son çoğaltma gecikmesini ve diğer bilgileri alır. |
| [sys. dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |Çoğaltma bağlantılarının durumu da dahil olmak üzere tüm veritabanı işlemlerinin durumunu gösterir. |
| [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |uygulamanın tüm kaydedilmiş işlemler etkin ikincil veritabanı tarafından çoğaltılıncaya ve onaylanana kadar bekleyip beklememesine neden olur. |
|  | |

### <a name="powershell-manage-failover-of-single-and-pooled-databases"></a>PowerShell: tek ve havuza alınmış veritabanlarının yük devretmesini yönetme

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager modülü Azure SQL veritabanı tarafından hala desteklenmektedir, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. Bu cmdlet 'ler için bkz. [Azurerd. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az Module ve Azurerd modüllerinde komutların bağımsız değişkenleri önemli ölçüde aynıdır.

| Cmdlet | Açıklama |
| --- | --- |
| [Get-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase) |Bir veya daha fazla veritabanını alır. |
| [New-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabasesecondary) |Mevcut bir veritabanı için ikincil bir veritabanı oluşturur ve veri çoğaltmaya başlar. |
| [Set-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesecondary) |Yük devretmeyi başlatmak için ikincil bir veritabanını birincil olarak değiştirir. |
| [Remove-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesecondary) |Bir SQL Veritabanı ile belirtilen ikincil veritabanı arasında veri çoğaltmayı sonlandırır. |
| [Get-AzSqlDatabaseReplicationLink](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasereplicationlink) |Bir Azure SQL Veritabanı ve bir kaynak grubu veya SQL Server arasındaki coğrafi çoğaltma bağlantılarını alır. |
|  | |

> [!IMPORTANT]
> Örnek betikler için bkz. [etkin coğrafi çoğaltma kullanarak tek bir veritabanını yapılandırma ve yük devretme](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) ve [etkin coğrafi çoğaltma kullanarak havuza alınmış bir veritabanını yapılandırma ve yük](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)devretme.

### <a name="rest-api-manage-failover-of-single-and-pooled-databases"></a>REST API: tek ve havuza alınmış veritabanlarının yük devretmesini yönetme

| API | Açıklama |
| --- | --- |
| [Veritabanı oluştur veya güncelleştir (createMode = restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |Birincil veya ikincil bir veritabanını oluşturur, güncelleştirir veya geri yükler. |
| [Veritabanı oluşturma veya güncelleştirme durumunu al](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |Oluşturma işlemi sırasında durumu döndürür. |
| [Ikincil veritabanını birincil olarak ayarla (planlı yük devretme)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failover) |Geçerli birincil veritabanından yük devreterek hangi ikincil veritabanının birincil olduğunu ayarlar. **Bu seçenek yönetilen örnek için desteklenmez.**|
| [Ikincil veritabanını birincil olarak ayarla (planlanmamış yük devretme)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failoverallowdataloss) |Geçerli birincil veritabanından yük devreterek hangi ikincil veritabanının birincil olduğunu ayarlar. Bu işlem, veri kaybına neden olabilir. **Bu seçenek yönetilen örnek için desteklenmez.**|
| [Çoğaltma bağlantısını al](https://docs.microsoft.com/rest/api/sql/replicationlinks/get) |Coğrafi çoğaltma ortaklığı içindeki belirli bir SQL veritabanı için belirli bir çoğaltma bağlantısını alır. Sys. geo_replication_links katalog görünümünde görünen bilgileri alır. **Bu seçenek yönetilen örnek için desteklenmez.**|
| [Çoğaltma bağlantıları-veritabanına göre liste](https://docs.microsoft.com/rest/api/sql/replicationlinks/listbydatabase) | Coğrafi çoğaltma ortaklığında belirli bir SQL veritabanı için tüm çoğaltma bağlantılarını alır. Sys. geo_replication_links katalog görünümünde görünen bilgileri alır. |
| [Çoğaltma bağlantısını sil](https://docs.microsoft.com/rest/api/sql/replicationlinks/delete) | Bir veritabanı çoğaltma bağlantısını siler. Yük devretme sırasında gerçekleştirilemez. |
|  | |

## <a name="next-steps"></a>Sonraki adımlar

- Örnek betikler için bkz.:
  - [Etkin coğrafi çoğaltmayı kullanarak tek bir veritabanını yapılandırma ve tek bir veritabanının yükünü devretme](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Etkin coğrafi çoğaltmayı kullanarak havuza alınan veritabanını yapılandırma ve havuza alınmış veritabanının yükünü devretme](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- SQL veritabanı Ayrıca otomatik yük devretme gruplarını destekler. Daha fazla bilgi için bkz. [otomatik yük devretme grupları](sql-database-auto-failover-group.md)kullanma.
- İş sürekliliği için genel bakış ve senaryolar için bkz. [iş sürekliliği genel bakış](sql-database-business-continuity.md)
- Azure SQL veritabanı otomatik yedeklemeleri hakkında bilgi edinmek için bkz. [SQL veritabanı otomatik yedeklemeleri](sql-database-automated-backups.md).
- Kurtarmaya yönelik otomatik yedeklemeleri kullanma hakkında bilgi edinmek için bkz. [hizmet tarafından başlatılan yedeklemelerden bir veritabanını geri yükleme](sql-database-recovery-using-backups.md).
- Yeni bir birincil sunucu ve veritabanına yönelik kimlik doğrulama gereksinimleri hakkında bilgi edinmek için, bkz. [olağanüstü durum kurtarma sonrasında SQL veritabanı güvenliği](sql-database-geo-replication-security-config.md).
