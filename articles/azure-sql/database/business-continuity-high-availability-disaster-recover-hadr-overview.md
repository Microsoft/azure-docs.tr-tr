---
title: Bulut iş sürekliliği-veritabanı kurtarma
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Azure SQL veritabanı ve SQL yönetilen örneği 'nin bulut iş sürekliliği ve veritabanı kurtarmayı nasıl destekledikleri ve görev açısından kritik bulut uygulamalarının çalışır durumda tutulmasına yardımcı olun.
keywords: iş sürekliliği, bulutta iş sürekliliği, veritabanı olağanüstü durum kurtarma, veritabanı kurtarma
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 06/25/2019
ms.openlocfilehash: 7bd991bd709bb4be69325afe967d7e5600a9e1a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98222573"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Azure SQL Veritabanı'nda iş sürekliliğine genel bakış
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL veritabanı ve SQL yönetilen örneğindeki **iş sürekliliği** , işletmenizin kesintiye uğramasıyla, özellikle de bilgi işlem altyapısından çalışmaya devam etmesini sağlayan mekanizmaların, ilkelerin ve yordamların anlamına gelir. Çoğu durumda, SQL veritabanı ve SQL yönetilen örneği, bulut ortamında oluşabilecek kesintiye uğratan olayları işleyecek ve uygulamalarınızı ve iş işlemlerinizi çalıştırmaya devam edecektir. Bununla birlikte, SQL veritabanı tarafından otomatik olarak işlenebilecek bazı kesintiye uğramayan olaylar vardır; örneğin:

- Kullanıcı yanlışlıkla tablodaki bir satırı sildi veya güncelleştirdik.
- Kötü amaçlı saldırgan, verileri silme veya bir veritabanını bırakma işlemi başarılı oldu.
- Deprem, güç kesintisi ve geçici olarak devre dışı bir veri merkezi.

Bu genel bakışta, SQL veritabanı ve SQL yönetilen örneğinin iş sürekliliği ve olağanüstü durum kurtarma için sağladığı yetenekler açıklanır. Veri kaybına neden olabilecek veya veritabanınızın ve uygulamanızın kullanılamaz hale gelmesine neden olabilecek kurtarmayan olaylardan kurtarmaya yönelik seçenekler, öneriler ve öğreticiler hakkında bilgi edinin. Bir kullanıcı veya uygulama hatası veri bütünlüğünü etkiliyorsa, bir Azure bölgesinin kesintiye neden olması veya uygulamanızın bakım gerektirmesi durumunda ne yapılacağını öğrenin.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>İş sürekliliği sağlamak için kullanabileceğiniz SQL Veritabanı özellikleri

Bir veritabanı perspektifinden, dört önemli olası kesinti senaryosu vardır:

- Bir disk sürücüsü hatası gibi veritabanı düğümünü etkileyen yerel donanım veya yazılım hataları.
- Genellikle bir uygulama hatası veya insan hatasından kaynaklanan veri bozulması veya silme. Bu tür arızalar uygulamaya özeldir ve genellikle veritabanı hizmeti tarafından algılanamaz.
- Büyük olasılıkla doğal bir olağanüstü durumdan kaynaklanan veri merkezi kesintisi. Bu senaryo, alternatif bir veri merkezine uygulama yük devretmesi için bazı coğrafi artıklık düzeyi gerektirir.
- Yükseltme veya bakım hataları, planlı altyapı bakımı veya yükseltmeleri sırasında oluşan beklenmeyen sorunlar, önceki bir veritabanı durumuna hızlı geri alma gerektirebilir.

Yerel donanım ve yazılım hatalarını hafifletmek için SQL veritabanı,% 99,995 kullanılabilirlik SLA 'Sı ile bu hatalardan otomatik kurtarmayı garanti eden [yüksek kullanılabilirliğe](high-availability-sla.md)sahip bir mimari içerir.  

İşinizi veri kaybına karşı korumak için, SQL veritabanı ve SQL yönetilen örneği otomatik olarak tam veritabanı yedeklemeleri, her 12 saatte bir değişiklik veritabanı yedeklemesi ve 5-10 dakikada bir işlem günlüğü yedeklemesi oluşturur. Yedeklemeler, tüm hizmet katmanları için en az 7 gün için RA-GRS depolama alanında depolanır. En fazla 35 güne kadar, noktadan noktaya geri yükleme için yapılandırılabilir yedekleme saklama süresi Temel destek hariç tüm hizmet katmanları.

SQL veritabanı ve SQL yönetilen örneği, çeşitli planlanmamış senaryoları azaltmak için kullanabileceğiniz çeşitli iş sürekliliği özellikleri de sağlar.

- [Zamana bağlı tablolar](../temporal-tables.md) satır sürümlerini herhangi bir noktadan geri yüklemenize olanak sağlar.
- [Yerleşik otomatik yedeklemeler](automated-backups-overview.md) ve [zaman içinde geri yükleme](recovery-using-backups.md#point-in-time-restore) , tüm veritabanını yapılandırılan saklama süresi içinde 35 güne kadar bir zaman noktasına geri yüklemenize olanak sağlar.
- Silinen bir veritabanını, **sunucu silinmediği** zaman silinmiş [bir noktaya geri yükleyebilirsiniz](recovery-using-backups.md#deleted-database-restore) .
- [Uzun süreli yedek saklama](long-term-retention-overview.md) , yedeklemeleri 10 yıla kadar tutmanıza olanak sağlar. Bu, SQL yönetilen örneği için sınırlı genel önizleme aşamasındadır
- [Etkin coğrafi çoğaltma](active-geo-replication-overview.md) , veri merkezi kesintisi veya uygulama yükseltmesi durumunda okunabilir çoğaltmalar oluşturmanıza ve herhangi bir çoğaltmaya el ile yük devretmenize olanak sağlar.
- [Otomatik yük devretme grubu](auto-failover-group-overview.md#terminology-and-capabilities) , bir veri merkezi kesintisi durumunda uygulamanın otomatik olarak kurtarılmasını sağlar.

## <a name="recover-a-database-within-the-same-azure-region"></a>Aynı Azure bölgesi içindeki bir veritabanını kurtarma

Bir veritabanını geçmişteki bir zaman noktasına geri yüklemek için otomatik veritabanı yedeklemeleri kullanabilirsiniz. Bu şekilde, insan hatalarının neden olduğu veri bozukluklarının kurtarılmasını sağlayabilirsiniz. Zaman içinde geri yükleme, aynı sunucuda, bozulmadan önce verilerin durumunu temsil eden yeni bir veritabanı oluşturmanıza olanak sağlar. Çoğu veritabanı için geri yükleme işlemleri 12 saatten daha az sürer. Çok büyük veya çok etkin bir veritabanını kurtarmak daha uzun sürebilir. Kurtarma zamanı hakkında daha fazla bilgi için bkz. [veritabanı kurtarma süresi](recovery-using-backups.md#recovery-time).

Zaman içinde nokta geri yükleme (ıNR) için desteklenen en fazla yedekleme saklama süresi, uygulamanız için yeterli değilse, veritabanları için uzun süreli bir saklama (LTR) ilkesi yapılandırarak bunu genişletebilirsiniz. Daha fazla bilgi için bkz. [uzun süreli yedek saklama](long-term-retention-overview.md).

## <a name="compare-geo-replication-with-failover-groups"></a>Yük devretme gruplarıyla Coğrafi çoğaltmayı karşılaştırın

[Otomatik yük devretme grupları](auto-failover-group-overview.md#terminology-and-capabilities) , [coğrafi çoğaltmanın](active-geo-replication-overview.md) dağıtımını ve kullanımını basitleştirir ve aşağıdaki tabloda açıklandığı gibi ek özellikleri ekler:

|                                              | Coğrafi çoğaltma | Yük devretme grupları  |
|:---------------------------------------------| :-------------- | :----------------|
| **Otomatik yük devretme**                          |     Hayır          |      Yes         |
| **Aynı anda birden çok veritabanının yükünü devretme**  |     Hayır          |      Yes         |
| **Kullanıcı, yük devretmeden sonra bağlantı dizesini güncelleştirmelidir**      |     Yes         |      Hayır          |
| **SQL Yönetilen Örneği desteği**                   |     Hayır          |      Yes         |
| **Birincil ile aynı bölgede olabilir**             |     Yes         |      Hayır          |
| **Birden çok çoğaltma**                            |     Yes         |      Hayır          |
| **Okuma ölçeğini destekler**                          |     Yes         |      Yes         |


## <a name="recover-a-database-to-the-existing-server"></a>Bir veritabanını mevcut sunucuya kurtar

Nadir olarak, bir Azure veri merkezinde kesinti olabilir. Kesinti yaşandığında yalnızca birkaç dakika sürebilecek veya saatler alacak bir hizmet kesintisi söz konusu olabilir.

- Tek bir seçenek, veri merkezi kesintisi olduğunda veritabanınızın çevrimiçi duruma gelmesini bekleyeyöneliktir. Bu, veritabanının çevrimdışı olmasının kabul edilebildiği uygulamalar için geçerlidir. Örnek olarak üzerinde sürekli çalışma yapmadığınız bir geliştirme projesi veya ücretsiz deneme sürümü verilebilir. Bir veri merkezinde kesinti olduğunda, kesintiden en son ne kadar süre sürdüğünü bilemezsiniz, bu nedenle bu seçenek yalnızca veritabanınızın bir süredir gerekli olmadığı durumlarda işe yarar.
- Diğer bir seçenek de, [coğrafi olarak yedekli veritabanı yedeklemeleri](recovery-using-backups.md#geo-restore) (coğrafi geri yükleme) kullanarak herhangi bir Azure bölgesindeki sunucuda bir veritabanını geri yüklemektir. Coğrafi geri yükleme, kaynağı olarak coğrafi olarak yedekli bir yedek kullanır ve veritabanı ya da veri merkezi bir kesinti nedeniyle erişilemez olsa bile bir veritabanını kurtarmak için kullanılabilir.
- Son olarak, [etkin coğrafi çoğaltma](active-geo-replication-overview.md) veya veritabanınız ya da veritabanlarınız için bir [otomatik yük devretme grubu](auto-failover-group-overview.md) kullanarak coğrafi ikincil yapılandırma yaptıysanız bir kesinti ile hızlı bir şekilde kurtarma yapabilirsiniz. Bu teknolojilerin seçiminize bağlı olarak, el ile veya otomatik yük devretmeyi kullanabilirsiniz. Yük devretme işlemi yalnızca birkaç saniye sürer, ancak hizmeti etkinleştirmek için en az 1 saat sürer. Bu, yük devretmenin kesinti ölçeğinde bir şekilde hizalı olduğundan emin olmak için gereklidir. Ayrıca, yük devretme, zaman uyumsuz çoğaltmanın doğası nedeniyle küçük veri kaybına neden olabilir.

İş sürekliliği planınızı geliştirirken, uygulamanın kesintiden sonra tamamen kurtarılmasına kadar kabul edilebilen maksimum süreyi anlamanız gerekir. Uygulamanın tam olarak kurtarılması için gereken süre, kurtarma süresi hedefi (RTO) olarak bilinir. Ayrıca, bir planlanmamış kesintiye uğramadan kurtarma sırasında uygulamanın zaman dilimini kabul edebildiği en son veri güncelleştirme süresini (zaman aralığı) anlamanız gerekir. Olası veri kaybı, kurtarma noktası hedefi (RPO) olarak bilinir.

Farklı kurtarma yöntemleri farklı RPO ve RTO düzeyleri sunar. Belirli bir kurtarma yöntemi seçebilir veya tam uygulama kurtarması elde etmek için yöntemlerin bir bileşimini kullanabilirsiniz. Aşağıdaki tabloda her kurtarma seçeneğinin RPO 'SU ve RTO karşılaştırılır. Otomatik yük devretme grupları, coğrafi çoğaltmanın dağıtımını ve kullanımını basitleştirir ve aşağıdaki tabloda açıklandığı gibi ek özellikleri ekler.

| Kurtarma yöntemi | RTO | RPO |
| --- | --- | --- |
| Coğrafi olarak çoğaltılan yedeklerden coğrafi geri yükleme | 12 h | 1 h |
| Otomatik yük devretme grupları | 1 h | 5 s |
| El ile veritabanı yük devretmesi | 30 s | 5 s |

> [!NOTE]
> *El ile veritabanı yük devretmesi* , [plansız mod](active-geo-replication-overview.md#active-geo-replication-terminology-and-capabilities)kullanılarak tek bir veritabanının yük devretmesini coğrafi olarak çoğaltılan ikincil öğesine başvurur.
Otomatik yük devretme RTO ve RPO ayrıntıları için bu makalenin önceki kısımlarında yer alacak tabloya bakın.

Uygulamanız şu ölçütlerden herhangi birini karşılıyorsa otomatik yük devretme gruplarını kullanın:

- Görev açısından kritikse.
- 12 saat veya daha fazla kapalı kalma süresine izin verilmeyen bir hizmet düzeyi sözleşmesine (SLA) sahiptir.
- Kapalı kalma süresi mali sorumluluktan kaynaklanabilir.
- Yüksek oranda veri değişikliğine sahiptir ve 1 saatlik veri kaybı kabul edilemez.
- Etkin coğrafi çoğaltma ek maliyeti, olası mali yükümlülükten veya ilgili iş kaybından daha düşükse.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

Uygulama gereksinimlerinize bağlı olarak, bir veritabanı yedeklemeleri ve etkin coğrafi çoğaltma birleşimini kullanmayı tercih edebilirsiniz. Tek başına veritabanlarına yönelik tasarım konuları ve bu iş sürekliliği özelliklerini kullanan elastik havuzlar hakkında bir tartışma için bkz. [bulut olağanüstü durum kurtarma](designing-cloud-solutions-for-disaster-recovery.md) ve [Esnek havuz olağanüstü durum kurtarma stratejileri](disaster-recovery-strategies-for-applications-with-elastic-pool.md)için uygulama tasarlama.

Aşağıdaki bölümlerde, veritabanı yedeklemeleri veya etkin coğrafi çoğaltma kullanarak kurtarmaya yönelik adımlara genel bir bakış sağlanmaktadır. Planlama gereksinimleri, kurtarma sonrası adımları ve olağanüstü durum kurtarma detayına yönelik bir kesinti benzetimi yapma hakkında ayrıntılı adımlar için bkz. [SQL veritabanı 'nda bir veritabanını bir kesinti Ile kurtarma](disaster-recovery-guidance.md).

### <a name="prepare-for-an-outage"></a>Kesinti için hazırlanma

Kullandığınız iş sürekliliği özelliğinden bağımsız olarak aşağıdaki adımları uygulamanız gerekir:

- Sunucu düzeyi IP güvenlik duvarı kuralları, oturum açmalar ve ana veritabanı düzeyi izinleri dahil olmak üzere hedef sunucuyu belirleyip hazırlayın.
- İstemcilerin ve istemci uygulamalarının yeni sunucuya nasıl yönlendirileceğini belirlemek
- Denetim ayarları ve uyarılar gibi diğer bağımlılık belgelerini oluşturmak

Doğru hazırlandıysanız, yük devretme veya veritabanı kurtarmasının ardından uygulamalarınızı çevrimiçi hale getirmek ek süre sürer ve büyük olasılıkla her zaman sorun giderme işlemi için hatalı bir bileşim gerektirir.

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>Coğrafi olarak çoğaltılan bir ikincil veritabanına yük devretme

Kurtarma mekanizmanız olarak etkin coğrafi çoğaltma veya otomatik yük devretme grupları kullanıyorsanız, bir otomatik yük devretme ilkesi yapılandırabilir veya [el ile planlanmamış yük devretmeyi](active-geo-replication-configure-portal.md#initiate-a-failover)kullanabilirsiniz. Yük devretme işlemi başlatıldıktan sonra, İkincilin yeni birincil haline gelmesine ve yeni işlemleri kaydetmeye ve sorgulara yanıt vermeye, ancak henüz çoğaltılmamış veriler için en az veri kaybıyla çalışmaya çalışmasına neden olur. Yük devretme işlemini tasarlama hakkında daha fazla bilgi için bkz. [bulut olağanüstü durum kurtarma için uygulama tasarlama](designing-cloud-solutions-for-disaster-recovery.md).

> [!NOTE]
> Veri merkezi yeniden çevrimiçi olduğunda, eski ana durumlar yeni birinciye otomatik olarak yeniden bağlanır ve ikincil veritabanları olur. Birincili özgün bölgeye geri yüklemeniz gerekiyorsa, planlı yük devretmeyi el ile başlatabilirsiniz (yeniden çalışma).

### <a name="perform-a-geo-restore"></a>Coğrafi geri yükleme gerçekleştirme

Otomatik yedeklemeleri coğrafi olarak yedekli depolama (varsayılan olarak etkindir) ile kullanıyorsanız, [coğrafi geri yükleme](disaster-recovery-guidance.md#recover-using-geo-restore)kullanarak veritabanını kurtarabilirsiniz. Kurtarma genellikle, son günlük yedeklemenin alındığı ve çoğaltılıp çoğaltılma göre belirlenen bir saate kadar bir saatlik veri kaybı ile 12 saat içinde gerçekleşir. Kurtarma işlemi tamamlanana kadar veritabanı işlem kaydedemez ve sorgulara yanıt veremez. Coğrafi geri yükleme, yalnızca veritabanını zaman içinde son kullanılabilir noktaya geri yükler.

> [!NOTE]
> Uygulamanızı kurtarılan veritabanına geçiş yapmadan önce, veri merkezi yeniden çevrimiçi duruma gelirse kurtarmayı iptal edebilirsiniz.

### <a name="perform-post-failover--recovery-tasks"></a>Yük devretme/kurtarma sonrası görevleri gerçekleştirme

Bu iki kurtarma sisteminden herhangi biriyle gerçekleştirilen kurtarma işleminden sonra kullanıcılarınızın ve uygulamalarınızın çalışmaya devam etmesi için aşağıdaki ek görevleri gerçekleştirmeniz gerekir:

- İstemcileri ve istemci uygulamalarını yeni sunucuya ve geri yüklenen veritabanına yeniden yönlendirin.
- Uygun kuralları etkinleştirmek üzere kullanıcıların bağlanabilmesi veya [veritabanı düzeyinde güvenlik duvarlarını](firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules) kullanabilmesi için uygun sunucu düzeyinde IP Güvenlik Duvarı kurallarının yapıldığından emin olun.
- Uygun oturum açma ve ana veritabanı düzeyi izinlerinin yerinde olduğundan emin olun (veya [Kapsanan kullanıcıları](/sql/relational-databases/security/contained-database-users-making-your-database-portable)kullanın).
- Uygun şekilde denetimi yapılandırın.
- Uyarıları uygun şekilde yapılandırın.

> [!NOTE]
> Bir yük devretme grubu kullanıyorsanız ve okuma-yazma dinleyicisi kullanarak veritabanlarına bağlanıyorsanız, yük devretmeden sonra yeniden yönlendirme otomatik olarak gerçekleşir ve uygulamaya saydam olarak uygulanır.

## <a name="upgrade-an-application-with-minimal-downtime"></a>Bir uygulamayı en az kesinti süresiyle yükseltme

Uygulama yükseltmesi gibi planlı bakım nedeniyle bazen bir uygulamanın çevrimdışı alınması gerekir. [Uygulama yükseltmelerini yönetme](manage-application-rolling-upgrade.md) , yükseltme sırasında kapalı kalma süresini en aza indirmek ve bir sorun varsa bir kurtarma yolu sağlamak üzere bulut uygulamanızın sıralı yükseltmelerini etkinleştirmek üzere etkin coğrafi çoğaltmanın nasıl kullanılacağını açıklar.

## <a name="next-steps"></a>Sonraki adımlar

Tek veritabanları ve elastik havuzlara yönelik uygulama tasarımı değerlendirmeleri hakkında bir tartışma için bkz. [bulut olağanüstü durum kurtarma](designing-cloud-solutions-for-disaster-recovery.md) ve [elastik havuz olağanüstü durum kurtarma stratejileri](disaster-recovery-strategies-for-applications-with-elastic-pool.md)için uygulama tasarlama.
