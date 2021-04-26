---
title: Olağanüstü durum kurtarma
description: Azure SQL veritabanı etkin coğrafi çoğaltma ve coğrafi geri yükleme yeteneklerini kullanarak bir veritabanını bölgesel veri merkezi kesintisinden veya hatadan nasıl kurtaracağınızı öğrenin.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 06/21/2019
ms.openlocfilehash: 11c83a6ec364865eb3478112c9f33add22a5c09d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105643272"
---
# <a name="restore-your-azure-sql-database-or-failover-to-a-secondary"></a>Azure SQL veritabanınızı veya yük devretmesini ikincil bir duruma geri yükleme
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL veritabanı, bir kesinti yaşanmadan kurtarmak için aşağıdaki özellikleri sunar:

- [Etkin coğrafi çoğaltma](active-geo-replication-overview.md)
- [Otomatik yük devretme grupları](auto-failover-group-overview.md)
- [Coğrafi geri yükleme](recovery-using-backups.md#point-in-time-restore)
- [Bölgesel olarak yedekli veritabanları](high-availability-sla.md)

İş sürekliliği senaryoları ve bu senaryoları destekleyen özellikler hakkında bilgi edinmek için bkz. [iş sürekliliği](business-continuity-high-availability-disaster-recover-hadr-overview.md).

> [!NOTE]
> Bölgesel olarak yedekli Premium veya İş Açısından Kritik veritabanları ya da havuzlar kullanıyorsanız, kurtarma işlemi otomatikleştirilir ve bu malzemenin geri kalanı uygulanmaz.
>
> Birincil ve ikincil veritabanlarının aynı hizmet katmanına sahip olması gerekir. İkincil veritabanının birincil olarak aynı işlem boyutuyla (DTU 'Lar veya sanal çekirdekler) oluşturulması kesinlikle önerilir. Daha fazla bilgi için bkz. [birincil veritabanı olarak yükseltme veya eski sürüme](active-geo-replication-overview.md#upgrading-or-downgrading-primary-database)düşürme.
>
> Birden çok veritabanının yük devretmesini yönetmek için bir veya birkaç yük devretme grubu kullanın.
> Yük devretme grubuna mevcut bir coğrafi çoğaltma ilişkisi eklerseniz, coğrafi ikincil öğenin aynı hizmet katmanıyla ve işlem boyutuyla birincil olarak yapılandırıldığından emin olun. Daha fazla bilgi için bkz. [otomatik yük devretme gruplarını kullanarak birden çok veritabanının saydam ve eşgüdümlü yük devretmesini etkinleştirme](auto-failover-group-overview.md).

## <a name="prepare-for-the-event-of-an-outage"></a>Kesinti durumunda hazırlanma

Yük devretme grupları veya coğrafi olarak yedekli yedeklemeler kullanarak başka bir veri bölgesine kurtarma ile başarılı olması için, başka bir veri merkezi kesintisinde bir sunucuyu yeni birincil sunucu olması gerekir ve sorunsuz bir kurtarma sağlamak için belgelenen ve test edilen adımlara sahip olmanız gerekir. Bu hazırlık adımları şunları içerir:

- Yeni birincil sunucu olmak üzere başka bir bölgedeki sunucuyu belirler. Coğrafi geri yükleme için, bu genellikle veritabanınızın bulunduğu bölgenin [eşleştirilmiş bölgesindeki](../../best-practices-availability-paired-regions.md) bir sunucusudur. Bu, coğrafi geri yükleme işlemleri sırasında ek trafik maliyetini ortadan kaldırır.
- Kullanıcıların yeni birincil veritabanına erişmesi için gereken sunucu düzeyi IP güvenlik duvarı kurallarını belirleyin ve isteğe bağlı olarak tanımlayın.
- Kullanıcıları, bağlantı dizelerini değiştirme veya DNS girdilerini değiştirme gibi yeni birincil sunucuya nasıl yönlendirebileceğinizi belirleme.
- Yeni birincil sunucuda ana veritabanında bulunması gereken oturum açmaları belirleyip, isteğe bağlı olarak oluşturun ve bu oturumların ana veritabanında uygun izinlere sahip olduğundan emin olun. Daha fazla bilgi için bkz. [olağanüstü durum kurtarma sonrasında SQL veritabanı güvenliği](active-geo-replication-security-configure.md)
- Yeni birincil veritabanıyla eşlenecek şekilde güncellenmesi gereken uyarı kurallarını belirler.
- Geçerli birincil veritabanında denetim yapılandırmasını belgeleyin
- [Olağanüstü durum kurtarma ayrıntısı](disaster-recovery-drills.md)gerçekleştirin. Coğrafi geri yükleme için bir kesinti benzetimi yapmak üzere, kaynak veritabanını silebilir veya yeniden adlandırabilir ve uygulama bağlantısı hatasına yol açabilir. Yük devretme gruplarını kullanarak bir kesinti benzetimi yapmak için, Web uygulamasını veya veritabanına bağlı sanal makineyi devre dışı bırakabilir ya da uygulama bağlantı hatalarının oluşmasına neden olacak şekilde veritabanını yük devretmeye bırakabilirsiniz.

## <a name="when-to-initiate-recovery"></a>Kurtarmanın başlatılacağı zaman

Kurtarma işlemi uygulamayı etkiler. DNS kullanılarak SQL bağlantı dizesinin veya yeniden yönlendirmenin değiştirilmesini gerektirir ve kalıcı veri kaybına neden olabilir. Bu nedenle, yalnızca kesinti büyük olasılıkla uygulamanızın kurtarma zamanı hedefinden daha uzun olduğunda yapılmalıdır. Uygulama üretime dağıtıldığında, uygulamanın sistem durumunu düzenli olarak izlemeyi ve kurtarmaya izin vermek için aşağıdaki veri noktalarını kullanmanız gerekir:

1. Uygulama katmanından veritabanına kalıcı bağlantı hatası.
2. Azure portal, bölgedeki bir olayla ilgili olarak geniş bir etkiye sahip bir uyarı gösterir.

> [!NOTE]
> Yük devretme grupları kullanıyorsanız ve otomatik yük devretmeyi seçerseniz, kurtarma işlemi otomatik hale getirilebilir ve uygulama için saydamdır.

Uygulama toleransına kapalı kalma süresi ve olası iş sorumluluğına bağlı olarak, aşağıdaki kurtarma seçeneklerini göz önünde bulundurmanız gerekir.

En son coğrafi çoğaltılan geri yükleme noktasını almak için [kurtarılabilir veritabanını al](/previous-versions/azure/reference/dn800985(v=azure.100)) (*lastavailablebackupdate*) kullanın.

## <a name="wait-for-service-recovery"></a>Hizmet kurtarmayı bekle

Azure ekipleri, hizmet kullanılabilirliğini mümkün olduğunca hızlı bir şekilde geri yüklemek için, ancak köke bağlı olarak, saat veya gün sürelerine bağlı olarak çalışır.  Uygulamanız önemli ölçüde kapalı kalma süresine yol açabilir, kurtarmanın tamamlanmasını beklemeniz yeterlidir. Bu durumda, sizin bölüminizdeki hiçbir işlem yapmanız gerekmez. Geçerli hizmet durumunu [Azure hizmet durumu panoımızda](https://azure.microsoft.com/status/)görebilirsiniz. Bölgenin kurtarmasından sonra uygulamanızın kullanılabilirliği geri yüklenir.

## <a name="fail-over-to-geo-replicated-secondary-server-in-the-failover-group"></a>Yük devretme grubundaki coğrafi çoğaltılan ikincil sunucu yükünü devreder

Uygulamanızın kapalı kalma süresi iş yükümlülük ile sonuçlanabileceğinden, yük devretme grupları kullanmanız gerekir. Uygulamanın kesinti durumunda farklı bir bölgedeki kullanılabilirliği hızlı bir şekilde geri yüklemesine olanak sağlar. Bir öğretici için bkz. [coğrafi olarak dağıtılmış bir veritabanı uygulama](geo-distributed-application-configure-tutorial.md).

Veritabanlarının kullanılabilirliğini geri yüklemek için, desteklenen yöntemlerden birini kullanarak ikincil sunucuya yük devretmeyi başlatmanız gerekir.

Coğrafi olarak çoğaltılan bir ikincil veritabanına yük devretmek için aşağıdaki kılavuzlardan birini kullanın:

- [Azure portal kullanarak coğrafi olarak çoğaltılan bir ikincil sunucuya Yük devretme](active-geo-replication-configure-portal.md)
- [PowerShell kullanarak ikincil sunucuya Yük devretme](scripts/setup-geodr-and-failover-database-powershell.md)
- [Transact-SQL (T-SQL) kullanarak ikincil sunucuya Yük devretme](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true#e-failover-to-a-geo-replication-secondary)

## <a name="recover-using-geo-restore"></a>Coğrafi geri yükleme kullanarak kurtarma

Uygulamanızın kapalı kalma süresi iş yükümlülük sonucu içermiyorsa, uygulama veritabanınızı kurtarmak için [coğrafi geri yüklemeyi](recovery-using-backups.md) Yöntem olarak kullanabilirsiniz. En son coğrafi olarak yedekli yedekten veritabanının bir kopyasını oluşturur.

## <a name="configure-your-database-after-recovery"></a>Kurtarma sonrasında veritabanınızı yapılandırma

Bir kesinti durumundan kurtulmak için coğrafi geri yükleme kullanıyorsanız, normal uygulama işlevinin devam edebilmesi için yeni veritabanlarıyla bağlantının doğru şekilde yapılandırıldığından emin olmanız gerekir. Bu, Kurtarılan veritabanı üretimini hazırlamak için bir görev denetim listesi sağlar.

### <a name="update-connection-strings"></a>Bağlantı dizelerini Güncelleştir

Kurtarılan veritabanınız farklı bir sunucuda bulunduğundan, bu sunucuyu işaret etmek için uygulamanızın bağlantı dizesini güncelleştirmeniz gerekir.

Bağlantı dizelerini değiştirme hakkında daha fazla bilgi için bkz. [bağlantı kitaplığınız](connect-query-content-reference-guide.md#libraries)için uygun geliştirme dili.

### <a name="configure-firewall-rules"></a>Güvenlik duvarı kurallarını yapılandırma

Sunucuda ve veritabanında yapılandırılan Güvenlik Duvarı kurallarının birincil sunucuda ve birincil veritabanında yapılandırılananlarla eşleştiğinden emin olmanız gerekir. Daha fazla bilgi için bkz. [nasıl yapılır: güvenlik duvarı ayarlarını yapılandırma (Azure SQL veritabanı)](firewall-configure.md).

### <a name="configure-logins-and-database-users"></a>Oturum açmaları ve veritabanı kullanıcılarını yapılandırma

Uygulamanız tarafından kullanılan tüm oturumların kurtarılan veritabanınızı barındıran sunucuda bulunduğundan emin olmanız gerekir. Daha fazla bilgi için bkz. [coğrafi çoğaltma Için güvenlik yapılandırması](active-geo-replication-security-configure.md).

> [!NOTE]
> Olağanüstü durum kurtarma detayında sunucu güvenlik duvarı kurallarınızı ve oturum açmaları (ve bunların izinlerini) yapılandırıp test etmelisiniz. Bu sunucu düzeyindeki nesneler ve bunların yapılandırması kesinti sırasında kullanılamayabilir.

### <a name="setup-telemetry-alerts"></a>Telemetri uyarılarını ayarlama

Mevcut uyarı kuralı ayarlarınızın Kurtarılan veritabanı ve farklı sunucu ile eşlenecek şekilde güncelleştirildiğinden emin olmanız gerekir.

Veritabanı uyarı kuralları hakkında daha fazla bilgi için bkz. [uyarı bildirimleri alma](../../azure-monitor/alerts/alerts-overview.md) ve [hizmet durumunu izleme](../../service-health/service-notifications.md).

### <a name="enable-auditing"></a>Denetimi etkinleştir

Veritabanınıza erişmek için denetim gerekiyorsa, veritabanı kurtarmasından sonra denetimi etkinleştirmeniz gerekir. Daha fazla bilgi için bkz. [veritabanı denetimi](../../azure-sql/database/auditing-overview.md).

## <a name="next-steps"></a>Sonraki adımlar

- Azure SQL veritabanı otomatik yedeklemeleri hakkında bilgi edinmek için bkz. [SQL veritabanı otomatik yedeklemeleri](automated-backups-overview.md)
- İş sürekliliği tasarım ve kurtarma senaryoları hakkında bilgi edinmek için bkz. [süreklilik senaryoları](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- Kurtarmaya yönelik otomatik yedeklemeleri kullanma hakkında bilgi edinmek için bkz [. hizmet tarafından başlatılan yedeklemelerden veritabanını geri yükleme](recovery-using-backups.md)